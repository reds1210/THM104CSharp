# 依賴注入（Dependency Injection）

依賴注入（Dependency Injection）是一種設計模式，用於減少軟體元件之間的耦合度。  
在 DI 模式中，一個類別（消費者）不再直接創建它所需的依賴（其他類別或對象），而是從外部獲取（注入），通常是通過構造函數、屬性或方法來實現的。

## 優點

1. **減少耦合度**：類別之間不直接創建彼此的實例，減少了依賴關係。
2. **提高可測試性**：容易替換模擬對象進行單元測試。
3. **增強模組化和可維護性**：促進了清晰的代碼結構和可重用性。
4. **提高靈活性**：可以輕鬆更換依賴的具體實現，而不需要修改消費者類別。

## ASP.NET Core MVC (.NET 6) 中的 DI 實現

在 ASP.NET Core MVC 中，已經在框架內建DI 功能。

### 步驟 1：註冊服務

在 `Program.cs` 中註冊應用所需的服務。.NET 6以上的 ASP.NET Core 應用使用最小化的主機(minimal api)配置，因此所有配置都在 `Program.cs` 文件中完成。

```csharp
var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddControllersWithViews();

// 注冊自定義服務
builder.Services.AddScoped<IMyService, MyService>();

var app = builder.Build();
```

這裡 `AddScoped` 方法意味著每次 HTTP 請求時，都會創建 `MyService` 的新實例。

### 步驟 2：創建服務介面和實現

創建一個介面和實現該介面的類別。例如，您可能有一個 `IMyService` 介面和一個 `MyService` 類別。

```csharp
public interface IMyService
{
    void DoSomething();
}

public class MyService : IMyService
{
    public void DoSomething()
    {
        // 實現細節
    }
}
```

### 步驟 3：注入和使用依賴

在控制器中，您可以通過構造函數注入來使用這些服務，ASP.NET Core 會自動處理實際的注入過程。

```csharp
public class HomeController : Controller
{
    private readonly IMyService _myService;

    public HomeController(IMyService myService)
    {
        _myService = myService;
    }

    public IActionResult Index()
    {
        _myService.DoSomething();
        return View();
    }
}
```

當應用運行時，並且當您訪問涉及 `HomeController` 的路由時會將 `HomeController` 實例化，此時ASP.NET Core 會查找註冊的 DI 容器中的 `IMyService` 的實現，並將其提供給控制器的構造函數，`MyService` 的實例將被注入到 `HomeController` 中
。

## DI服務的生命週期

在依賴注入（DI）系統中，註冊服務時可以指定該服務的生命週期。  
這個生命週期決定了服務實例的創建和銷毀時間。

### 1. Transient（短暫的）

**特點**：

- 每次從服務容器請求時，都會創建一個新的實例。
- 適用於輕量級且無狀態的服務。

**範例**：

```csharp
public interface ITransientService { /* ... */ }
public class TransientService : ITransientService { /* ... */ }

services.AddTransient<ITransientService, TransientService>();
```

每次注入 `ITransientService` 或每次請求它時，都會創建 `TransientService` 的新實例。

### 2. Scoped（作用域）

**特點**：

- 每次 HTTP 請求時創建一個新的實例，對於該請求內的後續請求，將重用相同的實例。
- 適用於有狀態但僅在單個請求內有效的服務，例如，數據庫上下文。

**範例**：

```csharp
public interface IScopedService { /* ... */ }
public class ScopedService : IScopedService { /* ... */ }

services.AddScoped<IScopedService, ScopedService>();
```

在一次 HTTP 請求期間，對 `IScopedService` 的所有請求都將獲得相同的 `ScopedService` 實例。

### 3. Singleton（單例）

**特點**：

- 在應用程式的生命週期內只創建一個實例。
- 該實例在首次請求時創建，之後的每次請求都會重用這個實例。
- 適用於長期存在、共享狀態或無狀態的服務。

**範例**：

```csharp
public interface ISingletonService { /* ... */ }
public class SingletonService : ISingletonService { /* ... */ }

services.AddSingleton<ISingletonService, SingletonService>();
```

在整個應用程式生命週期中，`ISingletonService` 的所有請求都將獲得相同的 `SingletonService` 實例。

### 注意事項

- 在選擇生命週期時，需考慮服務的使用方式和需求。例如，使用 Singleton 生命週期的服務若非線程安全，可能會引起競爭條件。
- Scoped 服務在 Singleton 中使用時需特別小心，因為這可能導致在整個應用程序生命周期中使用相同的 Scoped 實例，這與 Scoped 生命週期的預期使用方式不符。

## DI的服務註冊方式

ASP.NET Core 中有多個方式來註冊服務和實現。

### 1. 通過介面和實作類別註冊

這是最常見的用法，將介面映射到其實現。

```csharp
services.AddScoped<IMyScopedService, MyScopedService>();
```

### 2. 使用具體類別註冊

如果不需要介面，可以直接註冊一個類的實例。

```csharp
services.AddScoped<MyScopedService>();
```

### 3. 使用工廠方法註冊

允許更靈活的註冊方式，例如在創建服務實例時需要額外邏輯或依賴其他服務。

```csharp
services.AddScoped<IMyScopedService>(provider =>
{
    var someDependency = provider.GetRequiredService<SomeOtherService>();
    return new MyScopedService(someDependency);
});
```

### 4. 使用實例註冊

直接使用現有的服務實例進行註冊。不過這種方式在 Scoped 服務中較少見，因為它會與 Scoped 的生命週期（每個請求一個實例）相衝突。

```csharp
var serviceInstance = new MyScopedService();
services.AddScoped<IMyScopedService>(provider => serviceInstance);
```
