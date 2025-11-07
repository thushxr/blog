# Service to Service Invocation
- DAPR provides a feature to invoke a service from another service using the sidecar runtime. This help the microservice architecture to communicate between two service.
- But still we do have a downside that if one service is not up and running then the other service which is waiting for the response will also fail here.
- We use this feature when both the services are up and running, so that it can communicate each other.
- Service to service invocation is tightly coupled between two services.

### **Why DAPR Here?**
- Normally we call one service to another service using the domain URL.
	- Ex: If transaction service is hosted in transaction domain then we call it like `https://transaction-domain.com/api/transaction`
- We can ignore the domain name here, so that if the domain is changed in future it doesn't effect the other service, because the API calls between the service is done through the sidecar runtime.

### **How DAPR Communities With Service**
- Lets assume we have two service as **OrderService** and **TransactionService** which are hosted independently.
- When these two service are running it also runs it corresponding sidecar runtime along side.
- So if **OrderService** is calling `/transaction` API in **TransactionService**, the API call will go like:
	- **OrderService** -> **OrderService Sidecar** -> **TransactionService Sidecar** -> **TransactionService**

### **Coding Examples in .NET**
Create two simple Web API projects, I'll name them as **OrderService** and **TransactionService**

**OrderService**
Step 1: Configure in the `program.cs`
```c#
public class Program
{
    public static void Main(string[] args)
    {
        var builder = WebApplication.CreateBuilder(args);

        builder.Services.AddDaprClient();
        builder.Services.AddControllers();
        builder.Services.AddEndpointsApiExplorer();
        builder.Services.AddSwaggerGen();

        var app = builder.Build();

        if (app.Environment.IsDevelopment())
        {
            app.UseSwagger();
            app.UseSwaggerUI();
        }

        app.UseAuthorization();
        app.MapControllers();
        app.Run();
    }
}
```

Step 2: Create `OrderController`, configure the DAPR and build an API
```c#
[ApiController]
[Route("order")]
public class OrderController : ControllerBase
{
    DaprClient client { get; set; }
    
    public OrderController(DaprClient daprClient) 
    {
        client = daprClient;
    }

    [HttpGet]
    public async Task<IActionResult> GetTransactionList(DaprClient client)
    {
        var response = await client.CreateInvokableHttpClient(appId: "transaction-service").GetFromJsonAsync<List<string>>("transaction");
        return Ok(response);
    }

    [HttpPost]
    public async Task<IActionResult> PostTransaction([FromBody] TransactionCode transactionCode)
    {
        var response = await client.CreateInvokableHttpClient(appId: "transaction-service").PostAsJsonAsync("transaction", transactionCode);
        return Ok(response);
    }
}

 public class TransactionCode
 {
     public string Code { get; set; }
 }
```

TransactionService
Step 1: Configure in program.cs
```c#
public class Program
{
    public static void Main(string[] args)
    {
        var builder = WebApplication.CreateBuilder(args);

        builder.Services.AddControllers();
        builder.Services.AddEndpointsApiExplorer();
        builder.Services.AddSwaggerGen();

        var app = builder.Build();

        if (app.Environment.IsDevelopment())
        {
            app.UseSwagger();
            app.UseSwaggerUI();
        }

        app.UseAuthorization();
        app.MapControllers();
        app.Run();
    }
}
```

Step 2: `TransactionController`
```c#
[ApiController]
[Route("transaction")]
public class TransactionController : ControllerBase
{
    private readonly ILogger<TransactionController> _logger;

    private static List<string> TransactionCode = new List<string>
    {
        "T001", "T002"
    };

    public TransactionController(ILogger<TransactionController> logger)
    {
        _logger = logger;
    }

    [HttpGet]
    public IActionResult Get()
    {
        Console.WriteLine("Get Transaction List");
        return Ok(TransactionCode);
    }

    [HttpPost]
    public IActionResult Post([FromBody] TransactionCode transactionCode)
    {
        Console.WriteLine("Post Transaction");
        TransactionCode.Add(transactionCode.Code);
        return Ok(transactionCode);
    }
}

 public class TransactionCode
 {
     public string Code { get; set; }
 }
```

### **Run Dotnet Application along with the DAPR sidecar**
Running `OrderService`
- Go to the path of `program.cs` file and open terminal
- Execute the below command:
```bash
dapr run --app-port 7000 --app-id order-service --app-protocol http --dapr-http-port 7001 -- dotnet run
```
Running `TransactionService`
- Go to the path of `program.cs` file and open terminal
- Execute the below command:
```bash
dapr run --app-port 8080 --app-id transaction-service --app-protocol http --dapr-http-port 8081 -- dotnet run
```
##### **DAPR Commands**
- `dapr run`: runs the dapr sidecar
- `--app-port`: dotnet application port which we mention in launchsettings.json
- `--app-id`: a unique id for the service which will be used to invoke the api in that service
- `--app-protocol`: protocal through which the service communicates
- `--dapr-http-port`: port number for the dapr sidecar

### **Benefits of Using Dapr for Service Invocation**
- Authentication supported.
- Built in retries.
- No need for manual service discovery.

### **Drawbacks of Service Invocation**
- Consumes extra space for the sidecar runtime.
- Debugging can be hard if sidecar is misconfigured.
- May feel over engineered for small application.

### **Best Practices**
- Use when
	- You have multiple microservices are needing the consistent communication.
- Avoid when:
	- You're building small application.

In the next post we'll explore how dapr simplifies event driven communication using the pub/sub.
