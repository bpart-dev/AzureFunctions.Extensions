# Aliencube.AzureFunctions.Extensions.OpenApi #

![Build and Test](https://github.com/aliencube/AzureFunctions.Extensions/workflows/Build%20and%20Test/badge.svg) [![](https://img.shields.io/nuget/dt/Aliencube.AzureFunctions.Extensions.OpenApi.svg)](https://www.nuget.org/packages/Aliencube.AzureFunctions.Extensions.OpenApi/) [![](https://img.shields.io/nuget/v/Aliencube.AzureFunctions.Extensions.OpenApi.svg)](https://www.nuget.org/packages/Aliencube.AzureFunctions.Extensions.OpenApi/)

This enables Azure Functions to render Open API document and Swagger UI. The more details around the Swagger UI on Azure Functions can be found on this [blog post](https://devkimchi.com/2019/02/02/introducing-swagger-ui-on-azure-functions/).

> **NOTE**: This extension supports both [Open API 2.0 (aka Swagger)](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md) and [Open API 3.0.1](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.1.md) spec.


## Acknowledgement ##

* In order to read JSON configuration on Azure Functions 1.x, these extensions have copied the source code of [Microsoft.Extensions.Configuration.Json](https://github.com/aspnet/Extensions/tree/master/src/Configuration/Config.Json) to make use of [Newtonsoft.Json 9.0.1](https://www.nuget.org/packages/Newtonsoft.Json/9.0.1) under the [MIT License](http://opensource.org/licenses/MIT).
* [Swagger UI](https://github.com/swagger-api/swagger-ui) version used for this library is [3.20.5](https://github.com/swagger-api/swagger-ui/releases/tag/v3.20.5) under the [Apache 2.0 license](https://opensource.org/licenses/Apache-2.0).


## Issues? ##

While using this library, if you find any issue, please raise a ticket on the [Issue](https://github.com/aliencube/AzureFunctions.Extensions/issues) page.


## Getting Started ##

### Install NuGet Package ###

In order for your Azure Functions app to enable Open API capability, download the following NuGet package into your Azure Functions project.

```bash
dotnet add <PROJECT> package Aliencube.AzureFunctions.Extensions.OpenApi
```


### Change Authorization Level ###

As a default, all endpoints to render Swagger UI and Open API documents have the authorisation level of `AuthorizationLevel.Function`, which requires the API key to render them.

```csharp
[FunctionName(nameof(OpenApiHttpTrigger.RenderSwaggerDocument))]
[OpenApiIgnore]
public static async Task<IActionResult> RenderSwaggerDocument(
    [HttpTrigger(AuthorizationLevel.Function, "GET", Route = "swagger.{extension}")] HttpRequest req,
    string extension,
    ILogger log)
{
    ...
}

[FunctionName(nameof(OpenApiHttpTrigger.RenderOpenApiDocument))]
[OpenApiIgnore]
public static async Task<IActionResult> RenderOpenApiDocument(
    [HttpTrigger(AuthorizationLevel.Function, "GET", Route = "openapi/{version}.{extension}")] HttpRequest req,
    string version,
    string extension,
    ILogger log)
{
    ...
}

[FunctionName(nameof(OpenApiHttpTrigger.RenderSwaggerUI))]
[OpenApiIgnore]
public static async Task<IActionResult> RenderSwaggerUI(
    [HttpTrigger(AuthorizationLevel.Function, "GET", Route = "swagger/ui")] HttpRequest req,
    ILogger log)
{
    ...
}
```

However, if you don't want to use the API key for them, change their authorisation level to `AuthorizationLevel.Anonymous`.

```csharp
[FunctionName(nameof(OpenApiHttpTrigger.RenderSwaggerDocument))]
[OpenApiIgnore]
public static async Task<IActionResult> RenderSwaggerDocument(
    [HttpTrigger(AuthorizationLevel.Anonymous, "GET", Route = "swagger.{extension}")] HttpRequest req,
    string extension,
    ILogger log)
{
    ...
}

[FunctionName(nameof(OpenApiHttpTrigger.RenderOpenApiDocument))]
[OpenApiIgnore]
public static async Task<IActionResult> RenderOpenApiDocument(
    [HttpTrigger(AuthorizationLevel.Anonymous, "GET", Route = "openapi/{version}.{extension}")] HttpRequest req,
    string version,
    string extension,
    ILogger log)
{
    ...
}

[FunctionName(nameof(OpenApiHttpTrigger.RenderSwaggerUI))]
[OpenApiIgnore]
public static async Task<IActionResult> RenderSwaggerUI(
    [HttpTrigger(AuthorizationLevel.Anonymous, "GET", Route = "swagger/ui")] HttpRequest req,
    ILogger log)
{
    ...
}
```


### Configure App Settings Key ###

This key is only required if:

* The Function app is deployed to Azure, and
* The Open API related endpoints has the `AuthorizationLevel` value other than `Anonymous`.

If the above conditions are met, add the following key to your `locall.settings.json` or App Settings blade on Azure.

* `OpenApi__ApiKey`: either the host key value or the master key value.

> **NOTE**: It is NOT required if your Open API related endpoints are set to the authorisation level of `Anonymous`.


## Open API Metadata Configuration ##

To generate an Open API document, [OpenApiInfo object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.1.md#infoObject) needs to be defined. This information can be declared in **ONE OF THREE** places &ndash; `host.json`, `openapisettings.json` or `local.settings.json`.

This library looks for the information in the following order:

1. `host.json`
2. `openapisettings.json`
3. `local.settings.json` or App Settings blade on Azure


### `host.json` ###

Although it has not been officially accepted to be a part of `host.json`, the Open API metadata still can be stored in it like:

```json
{
  ...
  "openApi": {
    "info": {
      "version": "1.0.0",
      "title": "Open API Sample on Azure Functions",
      "description": "A sample API that runs on Azure Functions 3.x using Open API specification - from **host. json**.",
      "termsOfService": "https://github.com/aliencube/AzureFunctions.Extensions",
      "contact": {
        "name": "Aliencube Community",
        "email": "no-reply@aliencube.org",
        "url": "https://github.com/aliencube/AzureFunctions.Extensions/issues"
      },
      "license": {
        "name": "MIT",
        "url": "http://opensource.org/licenses/MIT"
      }
    }
  }
  ...
}
```


### `openapisettings.json` ###

The Open API metadata can be defined in a separate file, `openapisettings.json` like:

```json
{
  "info": {
    "version": "1.0.0",
    "title": "Open API Sample on Azure Functions",
    "description": "A sample API that runs on Azure Functions 3.x using Open API specification - from  **openapisettings.json**.",
    "termsOfService": "https://github.com/aliencube/AzureFunctions.Extensions",
    "contact": {
      "name": "Aliencube Community",
      "email": "no-reply@aliencube.org",
      "url": "https://github.com/aliencube/AzureFunctions.Extensions/issues"
    },
    "license": {
      "name": "MIT",
      "url": "http://opensource.org/licenses/MIT"
    }
  }
}
```


### `local.settings.json` or App Settings Blade ###

On either your `local.settings.json` or App Settings on Azure Functions instance, those details can be set up like:

* `OpenApi__Info__Version`: **REQUIRED** Version of Open API document. This is not the version of Open API spec. eg. 1.0.0
* `OpenApi__Info__Title`: **REQUIRED** Title of Open API document. eg. Open API Sample on Azure Functions
* `OpenApi__Info__Description`: Description of Open API document. eg. A sample API that runs on Azure Functions either 1.x or 2.x using Open API specification.
* `OpenApi__Info__TermsOfService`: Terms of service URL. eg. https://github.com/aliencube/AzureFunctions.Extensions
* `OpenApi__Info__Contact__Name`: Name of contact. eg. Aliencube Community
* `OpenApi__Info__Contact__Email`: Email address for the contact. eg. no-reply@aliencube.org
* `OpenApi__Info__Contact__Url`: Contact URL. eg. https://github.com/aliencube/AzureFunctions.Extensions/issues
* `OpenApi__Info__License__Name`: **REQUIRED** License name. eg. MIT
* `OpenApi__Info__License__Url`: License URL. eg. http://opensource.org/licenses/MIT

> **NOTE**: In order to deploy Azure Functions v1 to Azure, the `AzureWebJobsScriptRoot` **MUST** be specified in the app settings section; otherwise it will throw an error that can't find `host.json`. Local debugging is fine, though. For more details, please visit [this page](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsscriptroot?WT.mc_id=azfuncextension-github-juyoo).
