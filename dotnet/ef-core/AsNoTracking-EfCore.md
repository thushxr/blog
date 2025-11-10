# EF Core Tip: Ever seen a simple GET query update a database row?
Last week, I ran into a behaviour in EF Core that surprised me — a record in PostgreSQL was getting updated even though I never called SaveChangesAsync().

Here’s the scenario:
- I was fetching a row from the database
- Modifying the in-memory object
- Publishing it to a queue
- Never explicitly calling SaveChangesAsync()

In MySQL, nothing happened, the database remained unchanged (as expected).

But in PostgreSQL, the row was updated automatically.
This made me pause… why is a GET + in-memory modification causing a DB update?

## What was actually happening?
EF Core uses Change Tracking by default.
When you fetch an entity without AsNoTracking(), EF Core begins tracking it.
If the entity is modified and SaveChanges is triggered anywhere in the scope, EF Core persists the change.
In my case, another part of the pipeline triggered SaveChangesAsync() — and because the entity was tracked, PostgreSQL ended up updating the row.

- MySQL provider didn’t persist the change because its provider behaves differently with tracked entities in this scenario.
- PostgreSQL provider persisted it because the tracked entity was marked as modified internally.

## The fix?
Always use AsNoTracking() when:
- You’re only reading data
- You don’t want EF Core to track the entity
- You want to avoid accidental writes
- You’re working in distributed/multi-service systems where SaveChanges might be called elsewhere

``` c#
var record = await _dbContext
    .Entities
    .AsNoTracking()
    .FirstOrDefaultAsync(x => x.Id == id);
```

## Key takeaway
Don’t rely on provider-specific behaviour.
Rely on EF Core behaviour you can control.

If your intention is read-only, always use AsNoTracking().
It’s safer, faster, and prevents surprising database updates, especially when working with multiple services or multiple database providers.
