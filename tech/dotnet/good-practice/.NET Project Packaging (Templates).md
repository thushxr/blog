# .NET Project Packaging (Templates)

.NET project templates allow you to package and share a predefined project structure so it can be reused across teams or projects. This is especially useful when you want to standardize architecture (for example, Clean Architecture) and avoid repetitive setup work.

By using the .NET templating and packaging feature, developers can quickly scaffold new projects with a consistent structure, configuration, and best practices.

## When to Use .NET Templates

- Share a standardized project structure with other developers or teams  
- Reuse the same folder and solution structure across multiple projects  
- Reduce repetitive and redundant setup work  
- Enforce architectural patterns such as Clean Architecture  


## Step 1: Create Template Configuration

At the **root of the project**, create a folder named:

```
.template.config
```

Inside this folder, create a file named:

```
template.json
```

### template.json

```json
{
  "$schema": "http://json.schemastore.org/template",
  "author": "thushxr",
  "classifications": [
    "Web API",
    ".NET",
    "C#",
    "Clean Architecture"
  ],
  "identity": "Thushxr.SimpleWebApi.CleanArchitecture",
  "name": "Web API - Clean Architecture",
  "description": "A template for Web API projects following Clean Architecture",
  "shortName": "clean-simple-webapi",
  "groupIdentity": "Thushxr.WebApi",
  "sourceName": "Company.Domain.Project",
  "tags": {
    "language": "C#",
    "type": "project"
  }
}
```

## Step 2: Create the Template Project File

In the root folder, create a `.csproj` file (for example, `ProjectTemplate.csproj`) that defines how the template is packaged.

### ProjectTemplate.csproj

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <PackageType>Template</PackageType>
    <TargetFramework>net8.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>

    <PackageId>Thushxr.SimpleWebApi.CleanArchitecture</PackageId>
    <Title>Web API with Clean Architecture</Title>
    <Version>1.0.0</Version>
    <Authors>thushxr</Authors>
    <Company>thushxr</Company>
    <Description>
      A production-ready Web API template based on Clean Architecture
    </Description>

    <PackageReadmeFile>README.md</PackageReadmeFile>
    <RepositoryUrl>https://github.com/thushxr/web-api-clean-architecture-template</RepositoryUrl>
    <FileVersion>1.0.1</FileVersion>
    <PackageLicenseExpression>MIT</PackageLicenseExpression>
    <PackageTags>webapi;dotnet;cleanarchitecture</PackageTags>

    <IsPackable>true</IsPackable>
  </PropertyGroup>

  <ItemGroup>
    <Compile Remove="**\*" />

    <Content Include=".template.config\**\*"
             Pack="true"
             PackagePath="content\Company.Domain.Project\.template.config\" />

    <Content Include="services\project\host\**\*"
             Exclude="services\project\host\**\bin\**;
                      services\project\host\**\obj\**;
                      services\project\host\**\*.csproj.user"
             Pack="true"
             PackagePath="content\Company.Domain.Project\services\project\host\" />

    <Content Include="services\project\src\**\*"
             Exclude="services\project\src\**\bin\**;
                      services\project\src\**\obj\**"
             Pack="true"
             PackagePath="content\Company.Domain.Project\services\project\src\" />

    <Content Include="Company.Domain.Project.slnx"
             Pack="true"
             PackagePath="content\Company.Domain.Project\" />

    <Content Include="README.md"
             Pack="true"
             PackagePath="content\Company.Domain.Project\" />
  </ItemGroup>

  <ItemGroup>
    <None Update="README.md">
      <Pack>true</Pack>
      <PackagePath>\</PackagePath>
    </None>
  </ItemGroup>
</Project>
```


## Step 3: Pack the Template

```bash
dotnet pack ProjectTemplate.csproj -c Release
```

The generated `.nupkg` file will be available in:

```
bin/Release
```


## Step 4: Install the Template Locally

```bash
dotnet new install <path-to-nupkg>
```

Example:

```bash
dotnet new install bin/Release/Thushxr.SimpleWebApi.CleanArchitecture.1.0.0.nupkg
```


## Step 5: Verify the Template

```bash
dotnet new clean-simple-webapi -n MyNewWebApi
```

This will create a new project using the packaged template.


## Publishing to NuGet

Once validated, upload the `.nupkg` file to **NuGet.org** to make the template publicly available.

Developers can then install it using:

```bash
dotnet new install Thushxr.SimpleWebApi.CleanArchitecture
```


## Summary

- Use `.template.config/template.json` to define template metadata  
- Use a dedicated `.csproj` to control packaging  
- Package using `dotnet pack`  
- Install locally using `dotnet new install`  
- Publish to NuGet for community usage  
