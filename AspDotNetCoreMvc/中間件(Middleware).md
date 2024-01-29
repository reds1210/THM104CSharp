# 中間件(Middleware)

中間件（Middleware）在ASP.NET Core中是處理HTTP請求和響應的關鍵組件。它們按順序組成請求處理管道，每個中間件可以選擇處理並結束請求或將其傳遞給管道中的下一個中間件。

## 中間件配置方法

根據ASP.NET Core的版本，中間件的配置方式有所不同：

1. **.NET Core 3.1 和 .NET 5**：在`Startup.cs`的`Configure`方法中配置。
2. **.NET 6 及更高版本**：在`Program.cs`中直接配置，符合最新的最小主機模型。

## 配置示例

### .NET Core 3.1 和 .NET 5

在`Startup.cs`：

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        // 配置中間件
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseRouting();

        app.UseAuthentication();
        app.UseAuthorization();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
    }
}
```

### .NET 6 及更高版本

在`Program.cs`：

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

if (app.Environment.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
}

app.UseRouting();

app.UseAuthentication();
app.UseAuthorization();

app.MapControllers();

app.Run();
```

## 使用匿名函數中間件

.NET 6及更高版本中，您還可以使用匿名函數直接在`Program.cs`中添加自定義中間件：

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.Use(async (context, next) =>
{
    // 處理HTTP請求
    await next(); // 呼叫管道中的下一個中間件
    // 處理HTTP響應
});

app.Run();
```

## 自定義中間件

無論使用哪個版本，您都可以創建自定義中間件類。以下是自定義中間件的基本結構：

```csharp
public class MyCustomMiddleware
{
    private readonly RequestDelegate _next;

    public MyCustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        // 自定義邏輯
        // 處理HTTP請求
        await _next(context);// 呼叫管道中的下一個中間件
        // 處理HTTP響應
    }
}

public static class MyCustomMiddlewareExtensions
{
    public static IApplicationBuilder UseMyCustomMiddleware(this IApplicationBuilder builder)
    {
        return builder.UseMiddleware<MyCustomMiddleware>();
    }
}
```

在`Startup.cs`（對於.NET Core 3.1 和 .NET 5）或`Program.cs`（對於.NET 6及更高版本）中註冊自定義中間件：

```csharp
app.UseMyCustomMiddleware();
```

## 範例

製作一個Middleware用於河蟹部分文字，會將請求丟進來的文字進行替換，並且在action處理後，再進行檢查替換。

### 製作Middleware

讀取和修改Request Body：由於Request Body是一個Stream ，Stream它只能被讀取一次。
所以需要將Stream內容讀入一個可重新讀取的流。  
而修改Response Body也是類似的方法，需要捕獲Response Body以便修改它。

```csharp
public class ContentModificationMiddleware
{
    private readonly RequestDelegate _next;

    public ContentModificationMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        context.Request.EnableBuffering();
        var originalRequestBody = await new StreamReader(context.Request.Body).ReadToEndAsync();
        context.Request.Body.Position = 0;

        var modifiedRequestBody = ModifyContent(originalRequestBody);
        var requestStream = new MemoryStream(Encoding.UTF8.GetBytes(modifiedRequestBody));
        context.Request.Body = requestStream;

        // get originalBodyStream
        var originalBodyStream = context.Response.Body;
        using var newBodyStream = new MemoryStream();
        context.Response.Body = newBodyStream;

        // 調用下一個中間件（包括Controller Action）
        await _next(context);

        // 修改響應體
        newBodyStream.Position = 0; // 重置新響應流的位置
        var responseBody = await new StreamReader(newBodyStream).ReadToEndAsync();
        var modifiedResponseBody = ModifyContent(responseBody);

        // 將修改後的響應寫回originalResponse
        var responseBytes = Encoding.UTF8.GetBytes(modifiedResponseBody);
        await originalBodyStream.WriteAsync(responseBytes, 0, responseBytes.Length);

        // 確保將原始ResponseStream放回HttpContext
        context.Response.Body = originalBodyStream;
    }

    private string ModifyContent(string content)
    {
        // 替換內容的邏輯
        return content.Replace("oldText", "newText");
    }
}

public static class ContentModificationMiddlewareExtensions
{
    public static IApplicationBuilder UseContentModification(this IApplicationBuilder builder)
    {
        return builder.UseMiddleware<ContentModificationMiddleware>();
    }
}
```

### 使用中間件

然後在您的Startup.cs或Program.cs中使用這個中間件：

```csharp
app.UseContentModification();
```

### 建立Controller 跟 Action

為了完整地展示流程，會建立Controller 與 Action，以及如何使用JavaScript的`fetch` 從前端發送請求到這個Action。

```csharp
using Microsoft.AspNetCore.Mvc;

[Route("api/[controller]")]
[ApiController]
public class SampleController : ControllerBase
{
    [HttpPost("process")]
    public IActionResult ProcessData([FromBody] DataModel data)
    {
        data.Data+="oldText";
        var response = new { Message = "數據已處理", ProcessedData = data };
        return Ok(response);
    }
}

public class DataModel
{
    public string Data { get; set; }
}
```

在這個控制器中，我們有一個`ProcessData`的Action，它接受一個`DataModel`型別的物件，進行處理，並返回一個包含處理結果的響應。

### JavaScript Fetch 請求

然後，您可以使用JavaScript的`fetch` API從客戶端發送請求到這個API。

```javascript
async function sendDataToServer(data) {
    const response = await fetch('http://localhost:5000/api/sample/process', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
        },
        body: JSON.stringify(data)
    });

    if (!response.ok) {
        throw new Error('Network response was not ok.');
    }

    const responseData = await response.json();
    console.log(responseData);
}

sendDataToServer({ Data: "oldText" });
```
