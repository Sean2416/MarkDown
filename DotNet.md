# Process 

- ###### 行程，亦可稱作處理程序、進程

- ###### Process 意旨已經執行並且 load 到記憶體中的 Program，行程中的每一行程式碼隨時都有可能被 CPU 執行。在實際生活中，點開應用程式就是將 Program 活化成 Process，我們在活動監控器 (mac) 或 jobs (linux) 中看到 PID，也就是執行中的 Process ID。Program 是工廠藍圖，Process 就是照著設計藍圖所完成的實體工廠。

- ##### 組成：

  - ###### 一個 Memory Space。相當於 Object 的 variable，不同 Process 的 Memory Space 也不同，彼此看不到對方的 Memory Space。

  - ###### 一個以上的 Thread。

- ###### 多工作業系統 (Multitasking Operating System) 可以同時執行數個 Process，然而一個 CPU 一次只能執行一個 Process (因此才有現在的多核處理器)，CPU 的總量又總是少於 Process 的運行總量，且 Process 會佔用記憶體。因此如何排程 (*Scheduling*)、如何有效管理記憶體 (*Memory Management*) 是作業系統 (OS) 所關注的事。

- #####  總結

  - ###### Process 是電腦中已執行 Program 的實體，每一個 Process 互相獨立。

  - ###### Process 需要一些資源才能完成工作，如 CPU、記憶體、檔案以及 I/O 裝置。

  - ###### Process 不是基本執行單位，而是 Thread (執行緒) 的容器。

  - ###### 每個 Process 由一個 Memory Space 和一個以上的 Thread 所組成。



# Thread

- ###### 同一個 Process 中會有很多個 Thread，每一個 Thread 負責某一項功能。以聊天室 Process 為例，可以同時接受對方傳來的訊息以及發送自己的訊息給對方，就是同個 Process 中不同 Thread 的功勞。Thread 就是實體工廠內的工人，確保工廠的每項功能，並且共享工廠內的每一項資源。

- ##### 組成：

  - ###### Stack：紀錄從某個起始點開始 (例如main)，到目前為止所有函數的呼叫路徑，以及在這些呼叫路徑上所用到的區域變數。

  - ###### 紀錄 CPU 內部的暫存器 (如 Program Counter, Stack Pointer, Program Status Word 等) 的狀態。

- ###### 多執行緒中 (Multithreading)，兩個執行緒若同時存取或改變全域變數 (Global Variable)，可能會發生同步 (*Synchronization*) 問題。若執行緒之間互搶資源，則可能產生死結 (*Deadlock*)，如何避免 (*Prevent*) 或預防 (*Avoid*) 上述兩種情況的發生，仍是作業系統 (OS) 所關注的。



# Dependency Injection

- ## 依賴反轉原則(Dependency-Inversion Principle)

  - ###### 高階模組不應該依賴於低階模組。

    - ###### 兩者都應該依賴抽象，不應該直接去依賴，而是必須藉由抽象來隔開。不應該直接去受到實作的影響，而是只要關注在所需要的功能。

    - ###### 如此一來，依賴就「反轉」了。原本是 `高階模組 → 低階模組` 的關係，變成了 `高階模組 → 介面 ← 低階模組`。並不是高階去依賴低階，而是低階去依賴高階要求的功能。

  - ###### 抽象不應該依賴細節；細節應該依賴抽象。

- ## 控制反轉 (Inversion of Control, IoC)

  - ###### 把實例的建立和實例的使用切分開來，不再是由高階模組去建立並控制低階模組，而是我們讓一個控制反轉中心去建立低階模組，然後高階模組要使用的時候再把這個低階模組交給高階模組使用。

  - ###### 如此一來，控制權也跟著反轉過來了，高階模組從主動建立低階模組，變成被動接收低階模組；也就是從原先的 `高階模組 —(建立)→ 低階模組`，變成了 `高階模組 ←(傳遞低階模組)— 控制反轉中心`。

  - ###### 高階模組再也不需要關心如何建立，該建立哪個實體，只專注於使用功能，真正達到介面的精神。低階模組也只需要等待控制反轉中心分發，到了崗位就把份內事做好，專心在自己的職責身上即可。如此一來就能解除兩者之間的耦合。

- 在沒有使用 DI Framework 的情況下，假設在 UserController 要呼叫 UserLogic，會直接在 UserController 實例化 UserLogic，如下：

  ```C#
  public class MyDependency
  {
      public MyDependency()
      {
      }
  
      public Task WriteMessage(string message)
      {
          Console.WriteLine(
              $"MyDependency.WriteMessage called. Message: {message}");
  
          return Task.FromResult(0);
      }
  }
  
  public class IndexModel : PageModel
  {
      MyDependency _dependency = new MyDependency();
  
      public async Task OnGetAsync()
      {
          await _dependency.WriteMessage(
              "IndexModel.OnGetAsync created this message.");
      }
  }
  ```

- 上述程式碼可正常運作，但會出現下列問題

  - 若要將 `MyDependency` 取代為不同的實作，必須修改該類別。
  - 實作`MyDependency` 的地方越多，未來在置換或維護上越不便。
  - 實作難以進行單元測試。 應用程式應該使用模擬 (Mock) 或虛設常式 (Stub) `MyDependency` 類別，這在使用此方法時無法使用。

- 相依性插入可透過下列方式解決這些問題：

  - 使用介面或基底類別來將相依性資訊抽象化
  - 在服務容器中註冊相依性。 ASP.NET Core 提供內建服務容器 [IServiceProvider](https://docs.microsoft.com/dotnet/api/system.iserviceprovider)。 服務會在應用程式的 `Startup.ConfigureServices` 方法中註冊
  - 將服務「插入」到服務使用位置之類別的建構函式。 架構會負責建立相依性的執行個體，並在不再需要時將它捨棄。

- 建構抽象類別

  ```C#
  public interface IMyDependency
  {
      Task WriteMessage(string message);
  }
  ```

- 實作類別

  ```C#
  public class MyDependency : IMyDependency
  {
      private readonly ILogger<MyDependency> _logger;
  
      public MyDependency(ILogger<MyDependency> logger)
      {
          _logger = logger;
      }
  
      public Task WriteMessage(string message)
      {
          _logger.LogInformation(
              "MyDependency.WriteMessage called. Message: {MESSAGE}", 
              message);
  
          return Task.FromResult(0);
      }
  }
  ```

- 注入

  ```C#
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddRazorPages();
  
      services.AddScoped<IMyDependency, MyDependency>();
      services.AddTransient<IOperationTransient, Operation>();
      services.AddScoped<IOperationScoped, Operation>();
      services.AddSingleton<IOperationSingleton, Operation>();
      services.AddSingleton<IOperationSingletonInstance>(new Operation(Guid.Empty));
  
      // OperationService depends on each of the other Operation types.
      services.AddTransient<OperationService, OperationService>();
      
      //沒有使用介面宣告時
      services.AddScoped<UserManager>();
      
      //宣告實體 帶入參數
       services.AddScoped(provider => new UserManager(configManager.IdSvrConfig));
  }
  ```

## lifetimes

- **Transient**: 每次注入時，都重新 `new` 一個新的實例。
- **Scoped**: 每個 **Request** 都重新 `new` 一個新的實例，同一個 **Request** 不管經過多少個 Pipeline 都是用同一個實例。上例所使用的就是 **Scoped**。
- **Singleton**: 被實例化後就不會消失，程式運行期間只會有一個實例。

![[鐵人賽 Day04] ASP.NET Core 2 系列 - 依賴注入(Dependency Injection) - 實例產生動畫](https://blog.johnwu.cc/images/pasted-209.gif)

## View

- View 注入 Service 的方式，直接在 `*.cshtml` 使用 `@inject`：

  ```C#
  @using MyWebsite
  
  @inject ISampleTransient transient
  @inject ISampleScoped scoped
  @inject ISampleSingleton singleton
  
  <table border="1">
      <tr><td colspan="3">Cotroller</td></tr>
      <tr><td>Lifetimes</td><td>Id</td><td>Hash Code</td></tr>
      <tr><td>Transient</td><td>@ViewBag.TransientId</td><td>@ViewBag.TransientHashCode</td></tr>
      <tr><td>Scoped</td><td>@ViewBag.ScopedId</td><td>@ViewBag.ScopedHashCode</td></tr>
      <tr><td>Singleton</td><td>@ViewBag.SingletonId</td><td>@ViewBag.SingletonHashCode</td></tr>
  </table>
  <hr />
  <table border="1">
      <tr><td colspan="3">View</td></tr>
      <tr><td>Lifetimes</td><td>Id</td><td>Hash Code</td></tr>
      <tr><td>Transient</td><td>@transient.Id</td><td>@transient.GetHashCode()</td></tr>
      <tr><td>Scoped</td><td>@scoped.Id</td><td>@scoped.GetHashCode()</td></tr>
      <tr><td>Singleton</td><td>@singleton.Id</td><td>@singleton.GetHashCode()</td></tr>
  </table>
  ```

## Service

```C#
public class CustomService
{
    public ISample Transient { get; private set; }
    public ISample Scoped { get; private set; }
    public ISample Singleton { get; private set; }

    public CustomService(ISampleTransient transient,
        ISampleScoped scoped,
        ISampleSingleton singleton)
    {
        Transient = transient;
        Scoped = scoped;
        Singleton = singleton;
    }
}
```



# Middleware

- SP.NET Core 在 Middleware 的官方說明中，使用了 Pipeline 這個名詞，意旨 Middleware 像水管一樣可以串聯在一起，所有的 Request 及 Response 都會層層經過這些水管。

  ![[鐵人賽 Day03] ASP.NET Core 2 系列 - Middleware - 概念](https://blog.johnwu.cc/images/i03-1.png)

  ![[鐵人賽 Day03] ASP.NET Core 2 系列 - Middleware](https://blog.johnwu.cc/images/pasted-114.gif)

## App.Use

- Middleware 的註冊方式是在 *Startup.cs* 的 `Configure` 對 `IApplicationBuilder` 使用 `Use` 方法註冊。
  大部分擴充的 Middleware 也都是以 **Use** 開頭的方法註冊，例如：
  - **UseMvc()** ：MVC 的 Middleware
  - **UseRewriter()** ：URL rewriting 的 Middleware

```C#
public class Startup
{
    // ...
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) => 
        {
            await context.Response.WriteAsync("First Middleware in. \r\n");
            await next.Invoke();
            await context.Response.WriteAsync("First Middleware out. \r\n");
        });

        app.Use(async (context, next) => 
        {
            await context.Response.WriteAsync("Second Middleware in. \r\n");
            await next.Invoke();
            await context.Response.WriteAsync("Second Middleware out. \r\n");
        });

        app.Use(async (context, next) => 
        {
            await context.Response.WriteAsync("Third Middleware in. \r\n");
            // 水管阻塞，封包不往後送
            //await next.Invoke();
            await context.Response.WriteAsync("Third Middleware out. \r\n");
        });

        app.Run(async (context) =>
        {
            await context.Response.WriteAsync("Hello World! \r\n");
        });
    }
}

/*
First Middleware in. 
Second Middleware in. 
Second Middleware out. 
First Middleware out. 
*/
```

## App.Run

- `Run` 是 Middleware 的最後一個行為，以上面圖例來說，就是最末端的 Action。它不像 `Use` 能串聯其他 Middleware，但 `Run` 還是能完整的使用 Request 及 Response。

## App.Map

- `Map` 是能用來處理一些簡單路由的 Middleware，可依照不同的 URL 指向不同的 `Run` 及註冊不同的 `Use`。

```C#
public class Startup
{
    // ...
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) => 
        {
            await context.Response.WriteAsync("First Middleware in. \r\n");
            await next.Invoke();
            await context.Response.WriteAsync("First Middleware out. \r\n");
        });

        //進到 http://localhost:5000/second 才會執行
        app.Map("/second", mapApp =>
        {
            mapApp.Use(async (context, next) => 
            {
                await context.Response.WriteAsync("Second Middleware in. \r\n");
                await next.Invoke();
                await context.Response.WriteAsync("Second Middleware out. \r\n");
            });
            mapApp.Run(async context =>
            {
                await context.Response.WriteAsync("Second. \r\n");
            });
        });

        app.Run(async context =>
        {
            await context.Response.WriteAsync("Hello World! \r\n");
        });
    }
}

/*
https://localhost:5001/
    First Middleware in. 
    Hello World! 
    First Middleware out. 

https://localhost:5001/second
    First Middleware in. 
    Second Middleware in. 
    Second. 
    Second Middleware out. 
    First Middleware out. 
*/
```



## Extract Middleware

- 新增類別檔，將各Middleware獨立出來

```C#
//FirstMiddleware.cs
public class FirstMiddleware
{
    private readonly RequestDelegate _next;

    public FirstMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task Invoke(HttpContext context)
    {
        await context.Response.WriteAsync($"{nameof(FirstMiddleware)} in. \r\n");

        await _next(context);

        await context.Response.WriteAsync($"{nameof(FirstMiddleware)} out. \r\n");
    }
}
```

### 全域註冊

- 在 `Startup.Configure` 註冊 Middleware 就可以套用到所有的 Request。如下：

```C#
public class Startup
{
    // ...
    public void Configure(IApplicationBuilder app)
    {
        app.UseMiddleware<FirstMiddleware>();
        // ...
    }
}
```

### 區域註冊

- Middleware 也可以只套用在特定的 Controller 或 Action。註冊方式如下：

```C#
[MiddlewareFilter(typeof(FirstMiddleware))]
public class HomeController : Controller
{
    // ...

    [MiddlewareFilter(typeof(SecondMiddleware))]
    public IActionResult Index()
    {
        // ...
    }
}
```

### Extensions

- 建立IApplicationBuilder Extension Method

  ```C#
  // Extensions\CustomMiddlewareExtensions.cs
  public static class CustomMiddlewareExtensions
  {
      public static IApplicationBuilder UseFirstMiddleware(this IApplicationBuilder builder)
      {
          return builder.UseMiddleware<FirstMiddleware>();
      }
  }
  ```

  ```C#
  public class Startup
  {
      // ...
      public void Configure(IApplicationBuilder app)
      {
          app.UseFirstMiddleware();
          // ...
      }
  }
  ```

  



# appsettings.json

1. 若執行環境為 *Production* ，只讀 *appsettings.json* 。

2. 若執行環境為 *Development* ，先讀取 *appsettings.json* 再讀取 *appsettings.Development.json* 。並合併兩檔的組態。若組態項目重複，則以後來者覆蓋先前設定，亦即以 *appsettings.Development.json* 為準。

3. #### 如何決定執行環境

   - *launchSettings.json* 內 **ASPNETCORE_ENVIRONMENT**值
   - 如果沒有指定 ASPNETCORE_ENVIRONMENT 環境變數，則預設的執行環境是 *Production* 



# Attribute

- ###### Attribute 提供一個有用的方法，使中繼資料 (或宣告式資訊) 與程式碼 (組件、型別、方法和屬性 (Property) 等) 產生關聯。 當屬性 (Attribute) 與程式實體 (Entity) 產生關聯之後，即可在執行階段使用稱為「反映」(Reflection) 的技術來加以查詢。

- ###### 透過Attribute定義Enum或類別描述，可透過Reflection取得

  - ###### 定義一個客製化Attribute，並設定`Description`屬性

    - ```C#
      public class ReportDescriptionAttribute : Attribute
      {
          public string Description { get; set; }
      
          public ReportDescriptionAttribute(string Description)
          {
              this.Description = Description;
          }
      
          public override string ToString()
          {
              return this.Description.ToString();
          }
      }
      ```

  - ###### 針對類別內的各屬性加入`Attribute`描述

    - ```C#
      public class SNCode
      {
          /// <summary>
          /// 里程碑主檔
          /// </summary>
          [ReportDescription("里程碑主檔")]
          public const string Milestone = "Milestone_SN";
      
          /// <summary>
          /// 里程碑產出檔案
          /// </summary>
          [ReportDescription("里程碑產出檔案")]
          public const string MilestoneProd = "Milestone_Prod_SN";
      
          /// <summary>
          /// 專案風險檔
          /// </summary>
          [ReportDescription("專案風險檔")]
          public const string ProjectRisk = "Project_Risk_SN";
      
          /// <summary>
          /// 專案風險檔
          /// </summary>
          [ReportDescription("專案風險檔")]
          public const string ProjectMeasure = "Project_Measure_SN";
      }
      ```

  - ###### 透過Reflection取得內容

    - ```c#
      var members = typeof(SNCode).GetMember("MilestoneProd");
      var attributes = members[0].GetCustomAttributes(typeof(ReportDescriptionAttribute), false);
      var description = ((ReportDescriptionAttribute)attributes[0]).Description;
      ```

      
