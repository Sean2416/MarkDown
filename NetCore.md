

# Expression<TDelegate> 

- 將強類型 Lambda 運算式表示為運算式樹狀結構形式的資料結構。

- `public sealed class Expression<TDelegate> : System.Linq.Expressions.LambdaExpression`

  ```C#
  // Lambda expression as executable code.
  Func<int, bool> deleg = i => i < 5;
  // Invoke the delegate and display the output.
  Console.WriteLine("deleg(4) = {0}", deleg(4));
  
  // Lambda expression as data in the form of an expression tree.
  System.Linq.Expressions.Expression<Func<int, bool>> expr = i => i < 5;
  // Compile the expression tree into executable code.
  Func<int, bool> deleg2 = expr.Compile();
  // Invoke the method and print the output.
  Console.WriteLine("deleg2(4) = {0}", deleg2(4));
  
  /*  This code produces the following output:
  
      deleg(4) = True
      deleg2(4) = True
  */
  ```

  



# Dependency Injection

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

- ASP.NET Core 在 Middleware 的官方說明中，使用了 Pipeline 這個名詞，意旨 Middleware 像水管一樣可以串聯在一起，所有的 Request 及 Response 都會層層經過這些水管。

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





# Swagger

1. 安裝套件 `Swashbuckle.AspNetCore` 

   `dotnet add package Swashbuckle.AspNetCore`

2. 將 Swagger 產生器新增至 `Startup.ConfigureServices` 

3. 在 `Startup.Configure` 方法中，啟用中介軟體為產生的 JSON 文件和 Swagger UI 提供服務

   ```c#
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDbContext<TodoContext>(opt =>
           opt.UseInMemoryDatabase("TodoList"));
       services.AddControllers();
   
       // Register the Swagger generator, defining 1 or more Swagger documents
       services.AddSwaggerGen(c =>
       {
           c.SwaggerDoc("v1", new OpenApiInfo { Title = "My API", Version = "v1" });
       });
   }
   
   public void Configure(IApplicationBuilder app)
   {
       if (env.IsDevelopment())
       {
           app.UseDeveloperExceptionPage();
           app.UseSwagger();
           app.UseSwaggerUI(c =>
           {
             c.SwaggerEndpoint(
              url: "/swagger/v1/swagger.json",
              name: "RESTful API v1.0.0"
             );
            });
       }
   
       app.UseRouting();
       app.UseEndpoints(endpoints =>
       {
           endpoints.MapControllers();
       });
   }
   ```

   

4. 您可以在 `http://localhost:<port>/swagger` 找到 Swagger UI

![1583409797013](C:\Users\sean2\AppData\Roaming\Typora\typora-user-images\1583409797013.png)



## 自訂與擴充

### API 資訊與描述

- 傳遞至 `AddSwaggerGen` 方法的組態動作會新增作者、授權和描述等資訊

```C#
// Register the Swagger generator, defining 1 or more Swagger documents
services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo
    {
        Version = "v1",
        Title = "ToDo API",
        Description = "A simple example ASP.NET Core Web API",
        TermsOfService = new Uri("https://example.com/terms"),
        Contact = new OpenApiContact
        {
            Name = "Shayne Boyer",
            Email = string.Empty,
            Url = new Uri("https://twitter.com/spboyer"),
        },
        License = new OpenApiLicense
        {
            Name = "Use under LICX",
            Url = new Uri("https://example.com/license"),
        }
    });
});
```

![1583409902625](C:\Users\sean2\AppData\Roaming\Typora\typora-user-images\1583409902625.png)



## XML 註解

1. 打開 `*.csproj`，在 `<Project />` 區塊中插入以下程式碼：

   ```C#
   <PropertyGroup>
     <GenerateDocumentationFile>true</GenerateDocumentationFile>
     <NoWarn>$(NoWarn);1591</NoWarn>
   </PropertyGroup>
   ```

   

2. 調整 `startup.ConfigureServices`

   ```C#
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDbContext<TodoContext>(opt =>
           opt.UseInMemoryDatabase("TodoList"));
       services.AddControllers();
   
       // Register the Swagger generator, defining 1 or more Swagger documents
       services.AddSwaggerGen(c =>
       {
           c.SwaggerDoc("v1", new OpenApiInfo
           {
               Version = "v1",
               Title = "ToDo API",
               Description = "A simple example ASP.NET Core Web API",
               TermsOfService = new Uri("https://example.com/terms"),
               Contact = new OpenApiContact
               {
                   Name = "Shayne Boyer",
                   Email = string.Empty,
                   Url = new Uri("https://twitter.com/spboyer"),
               },
               License = new OpenApiLicense
               {
                   Name = "Use under LICX",
                   Url = new Uri("https://example.com/license"),
               }
           });
   
           // Set the comments path for the Swagger JSON and UI.
           var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
           var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
           c.IncludeXmlComments(xmlPath);
       });
   }
   ```

### 加入 `<summary>` 文件註解標籤

```C#
/// <summary>
/// Deletes a specific TodoItem.
/// </summary>
```

![顯示 XML 註解 'Deletes a specific TodoItem.' 的 Swagger UI](https://docs.microsoft.com/zh-tw/aspnet/core/tutorials/web-api-help-pages-using-swagger/_static/triple-slash-comments.png?view=aspnetcore-3.1)

### 加入 `<remarks>`

```C#
/// <remarks>
/// Sample request:
///
///     POST /Todo
///     {
///        "id": 1,
///        "name": "Item1",
///        "isComplete": true
///     }
///
/// </remarks>
```

![顯示額外註解的 Swagger UI](https://docs.microsoft.com/zh-tw/aspnet/core/tutorials/web-api-help-pages-using-swagger/_static/xml-comments-extended.png?view=aspnetcore-3.1)

### 定義回傳格式為 `application/json`

- 將 `[Produces("application/json")]` 屬性新增至 API Controller

```C#
[Produces("application/json")]
[Route("api/[controller]")]
[ApiController]
public class ControlTypeController : Controller
{
}
```

![含有預設回應內容類型的 Swagger UI](https://docs.microsoft.com/zh-tw/aspnet/core/tutorials/web-api-help-pages-using-swagger/_static/json-response-content-type.png?view=aspnetcore-3.1)

### 描述回傳型別

- 描述回傳狀態碼對應內容

```C#
/// <summary>
/// Creates a TodoItem.
/// </summary>
/// <param name="item"></param>
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(typeof(IEnumerable<ControlType>), 200)]
[ProducesResponseType(typeof(string), 400)]
public ActionResult<TodoItem> Create(TodoItem item)
{
    _context.TodoItems.Add(item);
    _context.SaveChanges();

    return CreatedAtRoute("GetTodo", new { id = item.Id }, item);
}
```

![1583411488210](C:\Users\sean2\AppData\Roaming\Typora\typora-user-images\1583411488210.png)

### Model定義

- 支援 [Required]、[StringLength]、[Range(1, 100)]
- 某些 Attribute 需要透過滑鼠事件才會呈現，例如：[StringLength]

```C#
/// <summary>
///     會員
/// </summary>
public class Member 
{
     /// <summary>
        ///     編碼
        /// </summary>
        [Column("id")]
        public Guid Id { get; set; }

        /// <summary>
        ///  ControlTypeCode
        /// </summary>
        [Column("control_type_code")]
        [Required]
        public string ControlTypeCode { get; set; }

        /// <summary>
        ///     姓名
        /// </summary>
        [StringLength(100)]
        [Column("name")]
        public string Name { get; set; }
 
}
```

![1583484527638](C:\Users\sean2\AppData\Roaming\Typora\typora-user-images\1583484527638.png)



### Swashbuckle.AspNetCore.Filters

- #### 建立Request 及 Response Example

1. Install the [NuGet package](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Filters/)

2. In the *ConfigureServices* method of *Startup.cs*, inside your `AddSwaggerGen` call, enable whichever filters you need

   ```C#
   public void ConfigureServices(IServiceCollection services)
   {
    services.AddSwaggerGen(c =>
    {
      //[SwaggerRequestExample] & [SwaggerResponseExample]
      // version < 3.0 like this: c.OperationFilter<ExamplesOperationFilter>(); 
      // version 3.0 like this: c.AddSwaggerExamples(services.BuildServiceProvider());
      // version > 4.0 like this:
      c.ExampleFilters();
        
   	//.......
    });
    //將參考範例加入介面呈現內容中
    services.AddSwaggerExamplesFromAssemblyOf<Startup>();
   }
   ```



#### Request examples

1. 實作 `IExamplesProvider<T>`

   ```C#
   // SwagExample/ControlTypeItemExample
   
   public class ControlTypeItemExample : IExamplesProvider<ControlTypeItem>
       {
           public ControlTypeItem GetExamples()
           {
               return new ControlTypeItem
               {
                   ControlTypeCode = "Test1",
                   Name = "測試明細123",
                   Status = 1,
                   SiteCode = "CW01"
               };
           }
       }
   ```

   

2. use the `SwaggerRequestExample` attribute

   ```C#
   [SwaggerRequestExample(typeof(ControlTypeItem), typeof(ControlTypeItemExample))]
   public async Task<IActionResult> Post(ControlTypeItem entity)
   {
       try
       {
           entity.Id = Guid.NewGuid();
   
           var affected = await _db.Query
           (
               ueryHelper.GetTableName<ControlTypeItem>())
               .InsertAsync(QueryHelper.Map(entity)
            );
   
           return Ok(entity);
       }
       catch (Exception e)
       {
           return BadRequest(e.Message);
       }
    }
   ```

   

3. #### List Request examples

   ```C#
    public class ControlTypeExample : IExamplesProvider<List<ControlType>>
       {
           public List<ControlType> GetExamples()
           {
               return new List<ControlType>
               {
                   new ControlType { Code = "AA", Name = "Test Country" },
                   new ControlType { Code = "BB", Name = "And another" }
               };
           }
       }
   ```

   ```C#
   [SwaggerRequestExample(typeof(ControlType), typeof(ControlTypeExample), jsonConverter: typeof(StringEnumConverter))]
   public async Task<IActionResult> Post(List<ControlType> entityList)
   {
       try
       {
           foreach (var entity in entityList)
           {
               entity.Id = Guid.NewGuid();
   
               await _db.Query(
                   QueryHelper.GetTableName<ControlType>()).
                   InsertAsync(QueryHelper.Map(entity));
           }
   
           return Ok(entityList);
       }
       catch (Exception e)
       {
           return BadRequest(e.Message);
       }
   }
   ```

   ![1583499292771](C:\Users\sean2\AppData\Roaming\Typora\typora-user-images\1583499292771.png)

#### Response examples

```C#
 [SwaggerResponse(200, "The list of ControlType", typeof(IEnumerable<ControlType>))]
 [SwaggerResponseExample(200, typeof(ControlTypeExample))]
 [SwaggerResponse(400, type: typeof(string))]
 public async Task<IActionResult> Get()
 {
     try
     {
         var result = await _db.FromQuery(
             new Query(QueryHelper.GetTableName<ControlType>())
         ).GetAsync<ControlType>();

         return Ok(result.ToList());
     }
     catch (Exception e)
     {
         return BadRequest(e.Message);
     }
 }
```

![1583499476251](C:\Users\sean2\AppData\Roaming\Typora\typora-user-images\1583499476251.png)





# ASP.Net Boilerplate

ABP遵循DDD（領域驅動設計）的原則，將工程分為四個層：

- **展現層（****Presentation**）：提供一個使用者介面，實現使用者交交互操作。

- **基礎設施層（****Infrastructure**）：提供通用技術來支援更高的層。例如基礎設施層的倉儲(Repository)可通過ORM來實現資料庫交互。

- **根據實際需要，可能會有額外添加的層。例如：**分散式服務層（**Distributed Service**）：用於公開應用程式接口供遠端用戶端調用。比如通過ASP.NET Web API或WCF來實現。這些都是常見的以領域為中心的分層體系結構。不同的項目在實現上可能會有細微的差別。

  ![img](file:///C:/Users/6591/AppData/Local/Temp/msohtmlclip1/01/clip_image002.gif)

  ![Startup template projects](https://raw.githubusercontent.com/aspnetboilerplate/aspnetboilerplate/master/doc/WebSite/Articles/Introduction-With-AspNet-Core-And-Entity-Framework-Core-Part-1/Template-Projects.png)

- **.EntityFramework**
  基礎設施層：EF框架、DbContext、實作倉儲介面、Migration資料庫遷移內含Seed預設資料列產生作業

- **.Web** project is for ASP.NET MVC layer.

- **.Tests** project is for unit and integration tests (up to application layer, excluding web layer)

- **.Web.Tests** project is for ASP.NET Core integrated tests (complete integration test including the web layer).

  

## Domain Layer

- `.Core`包括業務物件和業務規則，這是應用程式的核心層。
- 領域驅動設計(DDD)核心，內含***實體(Entity)***、***倉儲介面(Repository)***、***領域事件(Event)***、**工作單元(Unit of Work) ** 與 ***領域服務(Domain Services)***

### Entity

-  ASP.NET Boilerplate內的所有實體都需要繼承 `Entity` 

#### ID

- 類別Entity繼承後會自動含有一個資料型態為int的ID屬性，該類別還有一個泛型版本Entity<T>可以繼承

#### Auditing

- ABP還提供了IHasCreationTime這個介面來讓我們統一所有會使用到建立時間這個屬性的實體，藉此統一該屬性名稱為CreationTime 

  ```C#
  public interface IHasCreationTime
  {
      DateTime CreationTime { get; set; }
  }
  ```

- **ICreationAudited** extends IHasCreationTime by adding **CreatorUserId**

  ```C#
  public interface ICreationAudited : IHasCreationTime
  {
      long? CreatorUserId { get; set; }
  }
  ```

- modifications

  - ASP.NET Boilerplate automatically sets these properties when updating an entity. You just have to define them for your entity.

  ```C#
  public interface IHasModificationTime
  {
      DateTime? LastModificationTime { get; set; }
  }
  
  public interface IModificationAudited : IHasModificationTime
  {
      long? LastModifierUserId { get; set; }
  }
  ```

- If you want to implement all of the audit properties, you can directly implement the **IAudited** interface:

  ```C#
  public interface IAudited : ICreationAudited, IModificationAudited
  {
  
  }
  ```

- 可以直接繼承`**AuditedEntity**`包含了上述欄位內容

  ```C#
  using Abp.Domain.Entities;
  using Abp.Domain.Entities.Auditing;
  using System;
  using System.ComponentModel.DataAnnotations.Schema;
  
  namespace ABPMysql.Entities
  {
      [Table("AppBooks")]
      public class Book : AuditedEntity<Guid>, ISoftDelete
      {
          public string Name { get; set; }
  
          public BookType Type { get; set; }
  
          public DateTime PublishDate { get; set; }
  
          public float Price { get; set; }
  
          public virtual bool IsDeleted { get; set; }
  
          protected Book()
          {
  
          }
  
          public Book(string name, BookType type, DateTime publishDate, float price)
          {
              Name = name;
              Type = type;
              PublishDate = publishDate;
              Price = price;
              CreationTime = DateTime.Now;
              IsDeleted = false;
          }
      }
     
  }
  
  ```

#### Soft Delete

- 透過 `IsDeleted` 欄位標註資料型態而非真的刪除

- Soft delete is a commonly used pattern to mark an Entity as deleted instead of actually deleting it from database. For instance, you may not want to hard delete a User from the database since it has many relations to other tables

- ASP.NET Boilerplate implements the soft delete pattern out-of-the-box. When a soft-delete entity is being deleted, ASP.NET Boilerplate detects this, prevents deleting, sets IsDeleted as true, and then updates the entity in the database. It also does not retrieve (select) soft deleted entities from the database by automatically filtering them.

  ```C#
  public interface ISoftDelete
  {
      bool IsDeleted { get; set; }
  }
  ```

- **IDeletionAudited**

  ```C#
  public interface IDeletionAudited : ISoftDelete
  {
      long? DeleterUserId { get; set; }
  
      DateTime? DeletionTime { get; set; }
  }
  ```

- f you want to implement all the audit interfaces (creation, modification and deletion) for an entity, you can directly implement **IFullAudited** since it inherits from the others:

  ```C#
  public interface IFullAudited : IAudited, IDeletionAudited
  {
  
  }
  ```

- 可以繼承`FullAuditedEntity `包含所有 `Auditing`內容

  ```C#
  using Abp.Domain.Entities;
  using Abp.Domain.Entities.Auditing;
  using System;
  using System.ComponentModel.DataAnnotations.Schema;
  
  namespace ABPMysql.Entities
  {
      [Table("AppBooks")]
      public class Book : FullAuditedEntity<Guid>, ISoftDelete
      {
          public string Name { get; set; }
  
          public BookType Type { get; set; }
  
          public DateTime PublishDate { get; set; }
  
          public float Price { get; set; }
  
          protected Book()
          {
  
          }
  
          public Book(string name, BookType type, DateTime publishDate, float price)
          {
              Name = name;
              Type = type;
              PublishDate = publishDate;
              Price = price;
              CreationTime = DateTime.Now;
              IsDeleted = false;
          }
          public enum BookType
          {
              Undefined,
              Adventure,
              Biography,
              Dystopia,
              Fantastic,
              Horror,
              Science,
              ScienceFiction,
              Poetry
          }
      }   
  }
  ```

#### Active/Passive Entities

- Implement the **IPassivable** interface that has been created for this reason. It defines the **IsActive** property.

#### IExtendableObject Interface

- 用來儲存 **JSON formatted** 資料內容 (類似 `dictionary`)

  ```C#
  public class Person : Entity, IExtendableObject
  {
      public string Name { get; set; }
  
      public string ExtensionData { get; set; }
  
      public Person(string name)
      {
          Name = name;
      }
  }
  //------------------------------------------------------------------------------------
  
  var person = new Person("John");
  
  person.SetData("RandomValue", RandomHelper.GetRandom(1, 1000));
  person.SetData("CustomData", new MyCustomObject { Value1 = 42, Value2 = "forty-two" });
  
  /* person.ExtensionData
  	{"CustomData":{"Value1":42,"Value2":"forty-two"},"RandomValue":178}
  	var randomValue = person.GetData<int>("RandomValue");
  	var customData = person.GetData<MyCustomObject>("CustomData");
  	person.RemoveData("RandomValue");
  */
  ```

  



#### Create Entity And Context

1. 在`.Core`層建立實體

2. 在`.EntityFrameworkCore ` 新增 `DbSet`

   ```C#
   public class SimpleTaskAppDbContext : AbpDbContext
   {
       public DbSet<Task> Tasks { get; set; }
   
       public SimpleTaskAppDbContext(DbContextOptions<SimpleTaskAppDbContext> options) 
           : base(options)
       {
   
       }
   }
   ```

   

3. **Add-Migration**

4. **Update the Database**

#### Value Object

- 具有描述性類別而沒有 *identity* 概念的物件`VALUE OBJECT`
- `Entity` 跟  `VALUE OBJECT` 差別在於 `Entity`代表了資料的實體(包含可唯一的識別碼)， `VALUE OBJECT` 只包含針對物件的描述性類別。ex. Imagine two different people that have the same Name, Surname and Age but are different people (their identity numbers are different). For an Address class (which is a classic Value Object), if the two addresses have the same Country, City, and Street number, etc, they are considered to be the same address.
- In Domain Driven Design (DDD), the Value Object is another type of domain object which can include business logic and is an essential part of the domain.

#### Base Classes

- ABP has two base classes for value objects:  **ValueObject** and **ValueObject<T>** . Both classes override the equality operator (and other related operator and methods) to compare the two value objects and assume that they are identical if all the properties are the same.

-  For example, all of these tests pass:

  ```C#
  var address1 = new Address(new Guid("21C67A65-ED5A-4512-AA29-66308FAAB5AF"), "Baris Manco Street", 42);
  var address2 = new Address(new Guid("21C67A65-ED5A-4512-AA29-66308FAAB5AF"), "Baris Manco Street", 42);
  
  Assert.Equal(address1, address2);
  Assert.Equal(address1.GetHashCode(), address2.GetHashCode());
  Assert.True(address1 == address2);
  Assert.False(address1 != address2);
  
  // Even if they are different objects in memory, they are identical for our domain.
  ```



### Repositories

- 針對各個 `Entity` 建立資料庫操作的物件

- 每個 `Entity` 再進行CRUD時，可繼承 **IRepository<TEntity>  OR  IRepository<TEntity, TPrimaryKey>**。兩者差別僅在於當初宣告的`Entity.Id`型別。

  ```C#
  // Person
  public class Person : AuditedEntity<Guid>
   {
   	public virtual string Name { get; set; }
      public virtual string EmailAddress { get; set; }
  
  	public Person()
      {
      	CreationTime = DateTime.Now;
  
  		Id = Guid.NewGuid();
       }
  }
  
  // PersonAppService
  public class PersonAppService : SimpleTaskAppAppServiceBase, IPersonAppService
  {
  	private readonly IRepository<Person,Guid> _personRepository;
  	
      public PersonAppService(IRepository<Person, Guid> personRepository)
      {
      	_personRepository = personRepository;
  	}
  }
  ```

- Repository提供一些通用的功能:

  - **Get** is used to get an Entity with a given primary key (Id). It throws an exception if there is no entity in the database with the given Id
  - **Single** method is similar to Get but takes an expression rather than an Id. This way, you can write a lambda expression to get an Entity。It throws an exception if there is no entity with the given conditions or where there is more than one entity.
  - **Load** does not retrieve the entity from the database but creates a proxy object for lazy-loading.

  ```C#
  TEntity Get(TPrimaryKey id);
  Task<TEntity> GetAsync(TPrimaryKey id);
  TEntity Single(Expression<Func<TEntity, bool>> predicate);
  Task<TEntity> SingleAsync(Expression<Func<TEntity, bool>> predicate);
  TEntity FirstOrDefault(TPrimaryKey id);
  Task<TEntity> FirstOrDefaultAsync(TPrimaryKey id);
  TEntity FirstOrDefault(Expression<Func<TEntity, bool>> predicate);
  Task<TEntity> FirstOrDefaultAsync(Expression<Func<TEntity, bool>> predicate);
  TEntity Load(TPrimaryKey id);
  
  /*
      var person = _personRepository.Get(42);
      var person = _personRepository.Single(p => p.Name == "John");
  */
  ```

- **GetAllList** method is used to retrieve all entities from the database. An overload of it can be used to filter entities.

- **GetAllIncluding** allows to specify related data to be included in query results. In the following example, people will be retrieved with their Addresses property populated.

  ```C#
  List<TEntity> GetAllList();
  Task<List<TEntity>> GetAllListAsync();
  List<TEntity> GetAllList(Expression<Func<TEntity, bool>> predicate);
  Task<List<TEntity>> GetAllListAsync(Expression<Func<TEntity, bool>> predicate);
  IQueryable<TEntity> GetAll();
  IQueryable<TEntity> GetAllIncluding(params Expression<Func<TEntity, object>>[] propertySelectors)
      
  /*
      var allPeople = _personRepository.GetAllList();
      var somePeople = _personRepository.GetAllList(person => person.IsActive);
      var allPeople = await _personRepository.GetAllIncluding(
      p => p.Addresses).ToListAsync();
  */
  ```

- ### Custom return value

- There is also an additional method to provide the power of the IQueryable that can be usable out of a unit of work.

  `T Query<T>(Func<IQueryable<TEntity>, T> queryMethod);`

  ```C#
  var people = _personRepository.Query(q => q.Where(p => p.Name.Contains("H")).OrderBy(p => p.Name).ToList());
  ```

- #### Insert

  - **Insert** method simply inserts new a entity in to a database and returns the same inserted entity.
  - **InsertAndGetId** method returns the Id of a newly inserted entity.
  - **InsertOrUpdate** method inserts or updates a given entity by checking its Id's value
  -  **InsertOrUpdateAndGetId** method returns the Id of the entity after inserting or updating it.

  ```C#
  TEntity Insert(TEntity entity);
  Task<TEntity> InsertAsync(TEntity entity);
  TPrimaryKey InsertAndGetId(TEntity entity);
  Task<TPrimaryKey> InsertAndGetIdAsync(TEntity entity);
  TEntity InsertOrUpdate(TEntity entity);
  Task<TEntity> InsertOrUpdateAsync(TEntity entity);
  TPrimaryKey InsertOrUpdateAndGetId(TEntity entity);
  Task<TPrimaryKey> InsertOrUpdateAndGetIdAsync(TEntity entity);
  ```

- #### Update

  ```C#
  TEntity Update(TEntity entity);
  Task<TEntity> UpdateAsync(TEntity entity);
  ```

- #### Delete

  ```C#
  void Delete(TEntity entity);
  Task DeleteAsync(TEntity entity);
  void Delete(TPrimaryKey id);
  Task DeleteAsync(TPrimaryKey id);
  void Delete(Expression<Func<TEntity, bool>> predicate);
  Task DeleteAsync(Expression<Func<TEntity, bool>> predicate);
  ```

- ### Database Connections

  - A database connection is **opened** and a **transaction** automatically begins while entering a repository method
  - When the method ends and returns, all changes are **saved**, the transaction is **committed** and the database connection is **closed** by ASP.NET Boilerplate. 
  - If your repository method throws any type of Exception, the transaction is automatically **rolled back** and the database connection is closed.
  - If a repository method calls another repository method (even a method of a different repository) they share the **same connection and transaction.**
  -  The connection is managed (opened/closed) by the first method that enters a repository.

### Domain Services

- Domain Services (or just Services in DDD) is used to ***perform domain operations and business rules.***

- Unlike [Application Services](https://aspnetboilerplate.com/Pages/Documents/Application-Services) which get/return [Data Transfer Objects](https://aspnetboilerplate.com/Pages/Documents/Data-Transfer-Objects), a Domain Service gets/returns **domain objects** (like [entities](https://aspnetboilerplate.com/Pages/Documents/Entities) or value types).

- A Domain Service can be used by Application Services and other Domain Services, but not directly by the presentation layer (application services are for that).

-  **IDomainService interface** that is implemented by all domain services conventionally. When it's implemented, the domain service is **automatically registered** to the [Dependency Injection](https://aspnetboilerplate.com/Pages/Documents/Dependency-Injection) system as **transient**.

- ### Example

  1. ####  Creating an Interface

     ```C#
      public interface IPersonManager : IDomainService
      {
      	void CheckPeronEmail(string mailAddress);
      }
     ```

  2. ####  Service Implementation

     ```C#
     public class PersonManager : IPersonManager
     {
     	private readonly IRepository<Person, Guid> _personRepository;
     
     	public PersonManager(IRepository<Person, Guid> personRepository)
         {
         	_personRepository = personRepository;
         }
     
     	public void CheckPeronEmail(string mailAddress)
         {
         	var person = _personRepository.FirstOrDefault(
         	r => r.EmailAddress.Equals(mailAddress));
     
         	if (person != null)
     		{
             throw new UserFriendlyException("There is already a person with given email address");
             }
         }
     
     }
     ```

  3. ####  Application Service 加入 Manager驗證

     ```C#
     public class PersonAppService : SimpleTaskAppAppServiceBase, IPersonAppService
     {
     	private readonly IRepository<Person,Guid> _personRepository;
         private readonly IPersonManager _manager;
     
     	public PersonAppService(IRepository<Person, Guid> personRepository, IPersonManager manager)
         {
         	_personRepository = personRepository;
             _manager = manager;
         }
     
     	public void CreatePerson(CreatePersonInput input)
         {
         	var person = new Person {
             	Name = input.Name, EmailAddress = input.EmailAddress 
             };
     
     		_manager.CheckPeronEmail(input.EmailAddress);
     
     		_personRepository.Insert(person);
         }
     }
     ```

     

### Unit Of Work

- ASP.NET Boilerplate **opens** a database connection and begins a **transaction** when **entering** a **unit of work method**.  At the end of the method, the transaction is **committed** and the connection is **disposed**. If the method throws an **exception**, the transaction is **rolled back**, and the connection is disposed.

- If a unit of work method calls another unit of work method, both use the same connection & transaction. The first entered method manages the connection & transaction and then the others reuse it.

- Some methods are unit of work methods by default:

  - All [MVC](https://aspnetboilerplate.com/Pages/Documents/MVC-Controllers), [Web API](https://aspnetboilerplate.com/Pages/Documents/Web-API-Controllers) and [ASP.NET Core MVC](https://aspnetboilerplate.com/Pages/Documents/AspNet-Core) Controller actions.
  - All [Application Service](https://aspnetboilerplate.com/Pages/Documents/Application-Services) methods.
  - All [Repository](https://aspnetboilerplate.com/Pages/Documents/Repositories) methods.

- The unit of work is ambient. If a unit of work method calls another unit of work method, they share the same connection and transaction. **The first method** manages the connection and then the other methods reuse it.

- Example:

  - In the `CreatePerson` method, we're inserting a person using the **person repository** and incrementing the total people count using the  **statistics repository**. 
  - Both of these repositories **share** the same connection and transaction, since the application service method is a unit of work by default.
  - Boilerplate opens a database connection and starts a transaction when entering the CreatePerson method, and if no exception is thrown, it commits the transaction at the end of it.

  ```C#
  public class PersonAppService : IPersonAppService
  {
      private readonly IPersonRepository _personRepository;
      private readonly IStatisticsRepository _statisticsRepository;
  
      public PersonAppService(IPersonRepository personRepository, IStatisticsRepository statisticsRepository)
      {
          _personRepository = personRepository;
          _statisticsRepository = statisticsRepository;
      }
  
      public void CreatePerson(CreatePersonInput input)
      {
          var person = new Person { Name = input.Name, EmailAddress = input.EmailAddress };
          _personRepository.Insert(person);
          _statisticsRepository.IncrementPeopleCount();
      }
  }
  ```

#### Controlling the Unit Of Work

- You can **explicitly** use it if you want to control the unit of work somewhere else. There are two approaches for it. 

##### UnitOfWork Attribute

- `CreatePerson` method becomes a unit of work and manages the database connection and transaction.
- Both repositories use the same unit of work.
- Note that you do not need the UnitOfWork attribute if this is an application service method.

```C#
[UnitOfWork]
public void CreatePerson(CreatePersonInput input)
{
    var person = new Person { Name = input.Name, EmailAddress = input.EmailAddress };
    _personRepository.Insert(person);
    _statisticsRepository.IncrementPeopleCount();
}
```

##### IUnitOfWorkManager

- You can inject and use `IUnitOfWorkManager` as shown here (Some base classes already have **UnitOfWorkManager** injected by default: MVC Controllers, [application services](https://aspnetboilerplate.com/Pages/Documents/Application-Services), [domain services](https://aspnetboilerplate.com/Pages/Documents/Domain-Services)...). 
- You can create a **limited scope** unit of work. Using this approach, you must call the **Complete** method manually.
- If you don't call it, the transaction is **rolled back** and the changes are not saved.

```C#
public class MyService
{
    private readonly IUnitOfWorkManager _unitOfWorkManager;
    private readonly IPersonRepository _personRepository;
    private readonly IStatisticsRepository _statisticsRepository;

    public MyService(IUnitOfWorkManager unitOfWorkManager, IPersonRepository personRepository, IStatisticsRepository statisticsRepository)
    {
        _unitOfWorkManager = unitOfWorkManager;
        _personRepository = personRepository;
        _statisticsRepository = statisticsRepository;
    }

    public void CreatePerson(CreatePersonInput input)
    {
        var person = new Person { Name = input.Name, EmailAddress = input.EmailAddress };

        using (var unitOfWork = _unitOfWorkManager.Begin())
        {
            _personRepository.Insert(person);
            _statisticsRepository.IncrementPeopleCount();

            unitOfWork.Complete();
        }
    }
}
```



#### Unit Of Work in Detail

##### Disabling Unit Of Work

- You may want to disable the unit of work **for the conventional unit of work methods** . To do that, use the `UnitOfWorkAttribute's` **IsDisabled** property

```C#
[UnitOfWork(IsDisabled = true)]
public virtual void RemoveFriendship(RemoveFriendshipInput input)
{
    _friendshipRepository.Delete(input.Id);
}
```

##### Non-Transactional Unit Of Work

- Assume that you updated a few Entities in a non-transactional UOW. Even in this situation all the updates are performed at end of the unit of work with a single database command.
- If you execute an SQL query directly, it's performed immediately and not rolled back if your UOW is not transactional.
- If you're already in a transactional unit of work scope, setting `isTransactional` to false is ignored

```C#
[UnitOfWork(isTransactional: false)]
public GetTasksOutput GetTasks(GetTasksInput input)
{
    var tasks = _taskRepository.GetAllWithPeople(input.AssignedPersonId, input.State);
    return new GetTasksOutput
            {
                Tasks = Mapper.Map<List<TaskDto>>(tasks)
            };
}
```

##### Automatically Saving Changes

- If a method is a unit of work, ASP.NET Boilerplate automatically saves all the changes at the end of the method.
- `Repository` 取得的資料內容會與資料庫Binding。變更時，會自動更新資料庫內容 不需要使用 `_personRepository.Update`

```C#
[UnitOfWork]
 public async Task<ListResultDto<PersonDto>> GetAll()
 {
     var tasks = await _personRepository
         .GetAll()
         .ToListAsync();

     tasks.First().Name = "朱書陳1234";

     return new ListResultDto<PersonDto>(
         ObjectMapper.Map<List<PersonDto>>(tasks)
     );
 }
```

![1584160575565](C:\Users\sean2\AppData\Roaming\Typora\typora-user-images\1584160575565.png)



#### IRepository.GetAll()

- `IQueryable`使用 **延遲** 執行，`GetAll()`不會直接進行資料庫查詢直到使用 **ToList() **
- The database connection must also be open when `IQueryable.ToList()` is executed.

```C#
public SearchPeopleOutput SearchPeople(SearchPeopleInput input)
{
    // get IQueryable<Person>
    var query = _personRepository.GetAll();

    // add some filters if selected
    if (!string.IsNullOrEmpty(input.SearchedName))
    {
        query = query.Where(person => person.Name.StartsWith(input.SearchedName));
    }

    if (input.IsActive.HasValue)
    {
        query = query.Where(person => person.IsActive == input.IsActive.Value);
    }

    // get paged result list
    var people = query.Skip(input.SkipCount).Take(input.MaxResultCount).ToList();

    return new SearchPeopleOutput { People = Mapper.Map<List<PersonDto>>(people) };
}
```

#### UnitOfWork Attribute Restrictions

- You can use the UnitOfWork attribute for:
  - All **public** or **public virtual** methods for classes that are used over an interface (Like an application service used over a service interface).
  - All **public virtual** methods for self-injected classes (Like **MVC Controllers** and **Web API Controllers**).
  - All **protected virtual** methods.
- You can **not use the attribute for private methods** because ASP.NET Boilerplate uses dynamic proxying for that, and because private methods can not be seen from derived classes
- The UnitOfWork attribute (and any proxying) does not work if you don't use [dependency injection](https://aspnetboilerplate.com/Pages/Documents/Dependency-Injection) and instantiate the class yourself.

#### Options

-  we can change the default values of all the unit of works in the [startup configuration](https://aspnetboilerplate.com/Pages/Documents/Startup-Configuration).

```C#
public class SimpleTaskSystemCoreModule : AbpModule
{
    public override void PreInitialize()
    {
        Configuration.UnitOfWork.IsolationLevel = IsolationLevel.ReadCommitted;
        Configuration.UnitOfWork.Timeout = TimeSpan.FromMinutes(30);
    }

    //...other module methods
}
```

#### Events

- A unit of work has the **Completed**, **Failed** and **Disposed** events. You can register to these events and perform any operations you need.

-  For example, you may want to run some code when the current unit of work successfully completes. Example:

  ```C#
  public void CreateTask(CreateTaskInput input)
  {
      var task = new Task { Description = input.Description };
  
      if (input.AssignedPersonId.HasValue)
      {
          task.AssignedPersonId = input.AssignedPersonId.Value;
          _unitOfWorkManager.Current.Completed += (sender, args) => { /* TODO: Send email to assigned person */ };
      }
  
      _taskRepository.Insert(task);
  }
  ```

  

### EventBus

- The EventBus is a **singleton** object that is shared by other classes to trigger and handle events.

#### 1. Injecting IEventBus

1. use **DI** to get a reference to the **IEventBus**

   ```C#
   public class TaskAppService : ApplicationService
   {
       public IEventBus EventBus { get; set; }
   
       public TaskAppService()
       {
           EventBus = NullEventBus.Instance;
       }
   }
   ```

   

2. Getting The **Default Instance**

   - If you can not inject it, you can directly use **EventBus.Default**. 
   - We **do not recommend** that you directly use EventBus.Default, since it makes unit testing harder.

   ```C#
   EventBus.Default.Trigger(...); //trigger an event
   ```

#### 2. Defining Events

- Event 需要繼承 `EventData`

- `EventData` 內包含 兩個屬性

  - **EventSource** (the object that triggered the event)
  - **EventTime** (when it's triggered)

  ```C#
  public class TaskCompletedEventData : EventData
  {
      public int TaskId { get; set; }
  }
  ```

#### 3. Triggering Events

- 執行 `EventBus.Trigger` 就可以觸發TaskCompletedEventData事件

```C#
public class TaskAppService : ApplicationService
{
    public IEventBus EventBus { get; set; }

    public TaskAppService()
    {
        EventBus = NullEventBus.Instance;
    }

    public void CompleteTask(CompleteTaskInput input)
    {
        //TODO: complete the task in the database...
        EventBus.Trigger(new TaskCompletedEventData {TaskId = 42});
    }
}
```

#### 4. Handling Events

- To handle an event, you should implement the **IEventHandler<T>** interface
- 所有有繼承 `IEventHandler<TaskCompletedEventData>` 都會在 `EventBus.Trigger(new TaskCompletedEventData {TaskId = 42});` 觸發時執行。

```C#
public class Act1 : IEventHandler<TaskCompletedEventData>, ITransientDependency
{
    public void HandleEvent(TaskCompletedEventData eventData)
    {
        WriteActivity("A task is completed by id = " + eventData.TaskId);
    }
}

public class Act2 : IEventHandler<TaskCompletedEventData>, ITransientDependency
{
    public void HandleEvent(TaskCompletedEventData eventData)
    {
  	  Console.WriteLine("This is the Second Event");
    }
}
```

#### Predefined Events

#####  Handled Exceptions

- ASP.NET Boilerplate defines **AbpHandledExceptionData** and triggers this event when it automatically handles an exception. 
- This is especially useful if you want to get more information about exceptions. You can register to this event to be informed when an exception occurs.

#####  Entity Changes

- There are also generic event data classes for entity changes
- A change can be insert, update or delete.
  - **EntityCreatingEventData<TEntity>, EntityCreatedEventData<TEntity>**, **EntityUpdatingEventData<TEntity>, EntityUpdatedEventData<TEntity>, EntityDeletingEventData<TEntity>** and **EntityDeletedEventData<TEntity>**. Also, there are **EntityChangingEventData<TEntity>** and **EntityChangedEventData<TEntity>**. 

- 'ing' events (e.g. EntityUpdating) are triggered before committing a transaction. 
  - you can rollback the [unit of work](https://aspnetboilerplate.com/Pages/Documents/Unit-Of-Work) and prevent an operation by throwing an exception.
- 'ed' events (e.g. EntityUpdated) are triggered after committing a transaction
  -  you cannot rollback the unit of work.

- Entity change events are defined in the **Abp.Events.Bus.Entities** namespace and are **automatically triggered**  when an entity is inserted, updated or deleted.
-  If you have a Person entity, you can register to EntityCreatedEventData<Person> to be informed when a new Person is created and inserted into the database.
- These events also support inheritance. If the Student class is derived from the Person class and you registered to EntityCreatedEventData<Person>, you will be informed when a Person **or** Student is inserted.

#### Handling Base Event

- Eventbus 提供 Event的繼承 . 如下，**TaskEventData** 為父類別並包含兩個子類別**TaskCompletedEventData** and **TaskCreatedEventData**:

  ```C#
  public class TaskEventData : EventData
  {
      public Task Task { get; set; }
  }
  
  public class TaskCreatedEventData : TaskEventData
  {
      public User CreatorUser { get; set; }
  }
  
  public class TaskCompletedEventData : TaskEventData
  {
      public User CompletorUser { get; set; }
  }
  ```

- 因此當觸發的事件有繼承**TaskEventData **時，都會觸發下列事件 ex.

  -  ` EventBus.Trigger(new TaskCreatedEventData { S = "Create" });`
  - ` EventBus.Trigger(new TaskCompletedEventData { TaskId = 42 });`

  ```C#
  public class ActivityWriter : IEventHandler<TaskEventData>, ITransientDependency
  {
      public void HandleEvent(TaskEventData eventData)
      {
          if (eventData is TaskCreatedEventData)
          {
              //...
          }
          {
              //...
          }
      }
  }
  ```

  ![1584203073281](C:\Users\sean2\AppData\Roaming\Typora\typora-user-images\1584203073281.png)



#### Handling Multiple Events

- You can handle **multiple events** in a single handler.

```C#
public class ActivityWriter :
    IEventHandler<TaskCompletedEventData>,
    IEventHandler<TaskCreatedEventData>,
    ITransientDependency
{
    public void HandleEvent(TaskCompletedEventData eventData)
    {
        //TODO: handle the event...
    }

    public void HandleEvent(TaskCreatedEventData eventData)
    {
        //TODO: handle the event...
    }
}
```



## Data Filters

-  Boilerplate provides **data filters** that can be used to automatically filter queries based on some rules.

#### Pre-Defined Filters

#####  ISoftDelete

- 當`Entity `繼承了 `ISoftDelete` 後，資料就不會進行實際的刪除動作。而是在欄位 `IsDeleted` 標註為刪除

  ```C#
   public class Person : FullAuditedEntity<Guid>
   {
       public virtual string Name { get; set; }
       public virtual string EmailAddress { get; set; }
  
      public Person()
      {
    	  CreationTime = DateTime.Now;
  
     	  Id = Guid.NewGuid();
      }
  }
  
  /*
     migrationBuilder.CreateTable(
                  name: "persons",
                  columns: table => new
                  {
                      Id = table.Column<Guid>(nullable: false),
                      CreationTime = table.Column<DateTime>(nullable: false),
                      CreatorUserId = table.Column<long>(nullable: true),
                      LastModificationTime = table.Column<DateTime>(nullable: true),
                      LastModifierUserId = table.Column<long>(nullable: true),
                     
                      IsDeleted = table.Column<bool>(nullable: false),
                      DeleterUserId = table.Column<long>(nullable: true),
                      DeletionTime = table.Column<DateTime>(nullable: true),
                      
                      Name = table.Column<string>(nullable: true),
                      EmailAddress = table.Column<string>(nullable: true)
                  },
  */
  ```

- 此時在正常情況下查詢該資料表時 ABP會自動將 `ISoftDelete = True `  的資料過濾掉

  ```C#
  public async Task<ListResultDto<PersonDto>> GetAll()
  {
      var tasks = await _personRepository
          .GetAll()
          .ToListAsync();
  
      return new ListResultDto<PersonDto>(
          ObjectMapper.Map<List<PersonDto>>(tasks)
      );
  }
  
  // Only Get ISoftDelete = False Data
  ```

- 如果要真的刪除該筆資料，可使用 **IRepository.HardDelete**

- 如果想停止  `IsDeleted` 資料過濾，可使用

   ` using (_unitOfWorkManager.Current.DisableFilter(AbpDataFilters.SoftDelete))`

  ```C#
   public async Task<ListResultDto<PersonDto>> GetAll()
   {
       using (_unitOfWorkManager.Current.DisableFilter(AbpDataFilters.SoftDelete))
       {
           var people2 = _personRepository.GetAllList();
           
           return new ListResultDto<PersonDto>(
           ObjectMapper.Map<List<PersonDto>>(people2)
           );
       }
  
  }
  ```

#####  IMustHaveTenant

- If you are building **multi-tenant**(用戶) applications and store all tenant data in **single database**, you definitely do not want a tenant accidentally seeing other tenants' data.

  - You can implement **IMustHaveTenant** in that case. **IMustHaveTenant** defines the **TenantId** property to distinguish between different tenant entities.

  ```C#
  public class Product : Entity, IMustHaveTenant
  {
  	public int TenantId { get; set; }
  
  	public string Name { get; set; }
  }
  ```

- If an entity class is shared by tenants **and** the host, you can use the IMayHaveTenant filter. 

  - The **IMayHaveTenant** interface defines **TenantId** but it's **nullable**.
  - A **null** value means this is a **host** entity, a **non-null** value means this entity is owned by a **tenant** in which the Id is the TenantId. 

  ```c#
  public class Role : Entity, IMayHaveTenant
  {
      public int? TenantId { get; set; }
  
      public string RoleName { get; set; }
  }
  ```

#####  Disable Filters Globally

-  you can disable pre-defined filters globally. add this code to the PreInitialize method of your module

  `Configuration.UnitOfWork.OverrideFilter(AbpDataFilters.SoftDelete, false);`

#####  Setting Filter

- The `SetFilterParameter` method also returns an IDisposable. So, we can use it in a **using** statement to automatically **restore the old value** after the using statement.

  `CurrentUnitOfWork.SetFilterParameter("PersonFilter", "personId", 42);`



## Application Layer

- `.Application`應用服務主要是表現層與領域層中間溝通的橋樑，提供了服務給表現層調用，讓表現層與領域層的耦合降低
- 不包含業務邏輯。
- 這層也提供了資料傳輸物件(DTO)，來與表現層互相傳遞資料，主要是Input與Output部分

###  Application Service

-  An Application Service is called from the presentation layer using a DTO (Data Transfer Object) as a parameter.
- It also uses domain objects to perform some specific business logic and returns a DTO back to the presentation layer. 

####  IApplicationService Interface

- Application Sevice 必須實作 **IApplicationService** 

- `CreatePersonInput` 是定義好的DTO (Data Transfer Object)，用來規範方法傳入的參數內容

  ```C#
  public interface IPersonAppService : IApplicationService
  {
      void CreatePerson(CreatePersonInput input);
  }
  
  public class CreatePersonInput
  {
      [Required]
      public string Name { get; set; }
  
      public string EmailAddress { get; set; }
  }
  ```

- 實作 `IPersonAppService`

#### ApplicationService Class

- **Optionally**, it can be derived from the **ApplicationService** base class. Thus, IApplicationService is inherently implemented.

- `ApplicationService` class has some basic functionality that makes it easy to do **logging,** **localization** and so on... 

- You can have a base class which defines **LocalizationSourceName** in it's constructor. This way you do not repeat it for all service classes.

  ```C#
  public class TaskAppService : ApplicationService, ITaskAppService
  {
      public TaskAppService()
      {
          LocalizationSourceName = "SimpleTaskSystem";
      }
  
      public void CreateTask(CreateTaskInput input)
      {
          //Write some logs (Logger is defined in ApplicationService class)
          Logger.Info("Creating a new task with description: " + input.Description);
  
          //Get a localized text (L is a shortcut for LocalizationHelper.GetString(...), defined in ApplicationService class)
          var text = L("SampleLocalizableTextKey");
  
          //TODO: Add new task to database...
      }
  }
  ```

  

#### CrudAppService and AsyncCrudAppService Classes

- `AsyncCrudAppService ` 及 `CrudAppService` 是 ABP 提供的基本 Service。
- 裡面已經包含了 **CRUD**的基本功能
-  The CrudAppService base class is **generic**, which gets the related **Entity** and **DTO** types as generic arguments.
- This is also **extensible**, allowing you to override functionality when you need to customize it.

#####   Simple CRUD Application Service Example

1. Create DTO Object

   - The `AutoMap` attribute creates a mapping configuration between the entity and dto.

   ```C#
   public class Company : Entity<Guid>, ISoftDelete
   {
       public virtual string Name { get; set; }
       public virtual string Address { get; set; }
       public virtual string Phone { get; set; }
   
       public virtual bool IsDeleted { get; set; }
   
       public DateTime CreationTime { get; set; }
   
       public Company()
       {
           CreationTime = DateTime.Now;
           IsDeleted = false;
       }
   }
   
   //DTO
   [AutoMap(typeof(Company))]
   public class CompanyDto : EntityDto<Guid>, IHasCreationTime, ISoftDelete
   {
       public string Name { get; set; }
       public string Address { get; set; }
   
       public string Phone { get; set; }
   
       public DateTime CreationTime { get; set; }
   
       public  bool IsDeleted { get; set; }
   }
   ```

2. Create Service

   ```C#
   //這樣 CompanyAppService 就已經包含了CRUD的API功能
   public class CompanyAppService :  AsyncCrudAppService<Company, CompanyDto, Guid>
   {
   	public ICompanyAppService(IRepository<Company, Guid> repository)
               : base(repository)
       {
   
       }
   }
   ```

3. 或是先建立`Interface` 再實作

   ```C#
   public interface ITaskAppService : IAsyncCrudAppService<TaskDto>
   {
           
   }
   
   public class TaskAppService : AsyncCrudAppService<Task, TaskDto>, ITaskAppService
   {
       public TaskAppService(IRepository<Task> repository) 
           : base(repository)
       {
   
       }
   }
   ```

#####   Customize CRUD Application Services

- `AsyncCrudAppService` 的方法可以透過複寫的方式來進行調整

  1. 新增一個查詢的 Input Object，並繼承基底`PagedAndSortedResultRequestDto`

     - A Crud application service gets **PagedAndSortedResultRequestDto** as an argument for the **GetAll** method as default, which provides optional sorting and paging parameters.
     - Here we inherit from **PagedAndSortedResultRequestInput**. This is **not required**, but if you want, you can use the paging & sorting parameters from the base class. 

     ```C#
     public class GetAllCompanysInput : PagedAndSortedResultRequestDto
     {
         public bool? IsDeleted { get; set; }
     }
     ```

  2.  將 Input 引用至 `AsyncCrudAppService` 中，並複寫方法

     1. We added **GetAllTasksInput** as a 4th generic parameter to the AsyncCrudAppService class (3rd one is PK type of the entity). 
     2. Override the **CreateFilteredQuery** method to apply custom filters. This method is an extension point for the AsyncCrudAppService class. 
     3. WhereIf is an extension method of ABP to simplify conditional filtering. What we're doing here is simply filtering an IQueryable.

     ```C#
     public class ICompanyAppService : AsyncCrudAppService<Company, CompanyDto, Guid , GetAllCompanysInput>
     {
         public ICompanyAppService(IRepository<Company, Guid> repository)
             : base(repository)
         {
     
         }
         protected override IQueryable<Company> CreateFilteredQuery(GetAllCompanysInput input)
         {
             return base.CreateFilteredQuery(input)
                 .WhereIf(input.IsDeleted.HasValue, t => t.IsDeleted == input.IsDeleted.Value);
         }
     }
     ```

- ##### Create and Update  ***測試失敗*** 可能是 Id 型態

  - Notice that we are using same DTO (TaskDto) for getting, **creating** and **updating** tasks which may not be good for real life applications
  - **customize the create and update DTOs**.
    - 將 Input 引用至 `AsyncCrudAppService` 中

### **Data Transfer Objects**

- Transfer data between the **Application Layer** and the **Presentation Layer**.

- 可以根據不同的需求定義不同的傳輸物件，以達到資料保密的目的

- Ex.

  ```C#
  public class User : Entity
  {
      public virtual string Name { get; set; }
      public virtual string EmailAddress { get; set; }
      public virtual string Phone { get; set; }
      public virtual string Password { get; set; }
  }
  ```

  -  `User` 內部包含使用者密碼欄位，該欄位再查詢時不應該被回傳回前端。

  - 因此，我們可根據不同的狀況回傳不同內容

    ```C#
    [AutoMap(typeof(User))]
    public class UserDto : EntityDto
    {
        public string Name { get; set; }
        public string EmailAddress { get; set; }
    }
    
    public class SearchUserInput
    {
        [StringLength(40, MinimumLength = 1)]
        public string SearchedName { get; set; }
    }
    
    public class SearchUserOutput
    {
        public List<UserDto> People { get; set; }
    }
    
    public class CreateUserInput : UserDto
    {
        public string Password { get; set; }
    }
    ```

  - `IObjectMapper` 可協助我們將　`List<User>` 轉換成`List<UserDto>`

  ```C#
  public class UserAppService :  IUserAppService
  {
      private readonly IRepository<User> _Repository;
  
      private readonly IObjectMapper _objectMapper;
  
      public UserAppService(IRepository<User> repository, IObjectMapper objectMapper)
      {
          _Repository = repository;
          _objectMapper = objectMapper;
      }
  
      public ListResultDto<UserDto> GetAll()
      {
          //Get entities
          var peopleEntityList = _Repository.GetAllList();
              
          return new ListResultDto<UserDto>(
              _objectMapper.Map<List<UserDto>>(peopleEntityList)
          );
  
      }
  
      public SearchUserOutput GetUser(SearchUserInput input)
      {
          //Get entities
          var peopleEntityList = _Repository.GetAllList(r => r.Name.Equals(input.SearchedName));
  
          //Convert to DTOs
          var peopleDtoList = peopleEntityList
              .Select(person => new UserDto
              {
                  Id = person.Id,
                  Name = person.Name,
                  EmailAddress = person.EmailAddress
              }).ToList();
  
          return new SearchUserOutput { People = _objectMapper.Map<List<UserDto>>(peopleEntityList) };
      }
  
      public void CreateUser(CreateUserInput input)
      {
          var user = new User { Name = input.Name, EmailAddress = input.EmailAddress , Password = input.Password};
  
          _Repository.Insert(user);
      }
  }
  ```

#### Helper interfaces and classes

- ASP.NET Boilerplate 提供一些 DTO 的通用屬性供使用

- **IPagedResultRequest** extends **ILimitedResultRequest** by adding **SkipCount**. Let's implement this interface in SearchPeopleInput for paging

  ```C#
   public class SearchUserInput : IPagedResultRequest
      {
          [StringLength(40, MinimumLength = 1)]
          public string SearchedName { get; set; }
          public int MaxResultCount { get; set; }
          public int SkipCount { get; set; }
      }
  ```

  

## Background  Service

### Background Jobs

- 一次性的背景服務
- Background jobs are used to queue some tasks to be executed in the background
- You may need background jobs for several reasons.
  - To perform **long-running tasks** without having the users wait.
    - EX. 使用者透過API進行報表查詢及Mail發送
  - To create **re-trying** and **persistent tasks** to **guarantee** code will be **successfully executed**. 
    - .For example, you can send emails in a background job to overcome **temporary failures** and **guarantee** that it eventually will be sent. That way users do not wait while sending emails.

####  Create a Background Job

- 透過繼承**BackgroundJob<Targs>** 或 **IBackgroundJob<Targs>**，建立背景工作

  - 複寫`Execute` 方法進行背景工作執行

  - `Targs` 代表傳入工作的參數

  - 繼承**ITransientDependency**  來完成DI註冊

    ```C#
    public class TestJob : BackgroundJob<User>, ITransientDependency
    {
        public override void Execute(User user)
        {
            Thread.Sleep(10000);
    
            Console.WriteLine($"Create User Name IS {user.Name}");
    
            Logger.Debug(user.Name);
        }
    }
    ```

- Service 透過 `IBackgroundJobManager`執行

  ```C#
  public class UserAppService : SimpleMailTaskAppServiceBase, IUserAppService
  {
      private readonly IRepository<User, Guid> _Repository;
      private readonly IObjectMapper _objectMapper;
      private readonly IBackgroundJobManager _backgroundJobManager;
  
      public UserAppService(IRepository<User, Guid> repository, IUserManager manager, IBackgroundJobManager backgroundJobManager)
      {
          _Repository = repository;
          _objectMapper = objectMapper;
          _backgroundJobManager = backgroundJobManager;
      }
      public void CreateUser(CreateUserInput input)
      {
          var user = new User { Name = input.Name, EmailAddress = input.EmailAddress, Phone = input.Phone };
  
          _backgroundJobManager.Enqueue<TestJob, User>(user);
  
          _manager.CheckCreateValue(user);
  
          _Repository.Insert(user);
      }   
  }
  ```

- 使用者的Request完成後會直接回傳結果，而背景服務在停留10秒後繼續執行不會影響到使用者

### Background Workers

- **可重複執行**
- They are simple **independent threads** in the application running in the background

#### Create a Background Worker

- 實作 **IBackgroundWorker** 介面 或 繼承 **BackgroundWorkerBase** or **PeriodicBackgroundWorkerBase**
- If you derive from **PeriodicBackgroundWorkerBase**, you should implement the **DoWork**.
- If you derive from the **BackgroundWorkerBase** or directly implement **IBackgroundWorker**, you will override/implement the **Start**, **Stop** and **WaitToStop**
  - Start and Stop methods should be **non-blocking**, the WaitToStop method should **wait** for the worker to finish its current critical job.

```C#
public class TestJob : PeriodicBackgroundWorkerBase, ISingletonDependency
{
    private int _Count = 0;
    public TestJob(AbpTimer timer)
    : base(timer)
    {
        Timer.Period = 5000; //5 seconds (good for tests, but normally will be more)
    }

    [UnitOfWork]
    protected override void DoWork()
    {
        Console.WriteLine($"BackGround Service Running For: {++_Count} Times");

    }
}
```

- 註冊背景服務

```C#
[DependsOn(typeof(AbpMailKitModule))]
public class SimpleMailTaskCoreModule : AbpModule
{
    public override void PreInitialize()
    {
        Configuration.Auditing.IsEnabledForAnonymousUsers = true;
            
        Configuration.ReplaceService<ISmtpEmailSenderConfiguration, MailConfiguration>();

        SimpleMailTaskLocalizationConfigurer.Configure(Configuration.Localization);
    }

    public override void PostInitialize()
    {
        var workManager = IocManager.Resolve<IBackgroundWorkerManager>();
        workManager.Add(IocManager.Resolve<TestJob>());
    }
}
```



## API 開發順序

1.  在`.Core `新建資料實體，並繼承 ABP`Entity`

   ```C#
   public class Company : FullAuditedEntity<Guid>
   {
   	public virtual string Name { get; set; }
       public virtual string Address { get; set; }
   
   	public Company()
       {
       	CreationTime = DateTime.Now;
   
   		Id = Guid.NewGuid();
       }
   }
   ```

2.  在`.EntityFrameworkCore` 的 `~DbContext中`加入

   ```C#
   public class SimpleTaskAppDbContext : AbpDbContext
   {
       public DbSet<Company> Companys { get; set; }
   
       public SimpleTaskAppDbContext(DbContextOptions<SimpleTaskAppDbContext> options) 
           : base(options)
       {
   
       }
   }
   ```

3. 建立 `Migiration`

   - 將 設為起始專案
   - 在套件管理器主控台執行 `Add-Migration Create_Company`
   - 在套件管理器主控台執行 `update-database`

4. 新增 `ApplicationService`

   1. 建立繼承 **IApplicationService** 的介面
   2. 建立 DTO Class
   3. 實作介面方法 

5. 　修正`.web` 專案 `startup.cs` 內　`ConfigureServices`

```C#
/*
services.AddControllersWithViews(options =>
{
  options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute());

}).AddNewtonsoftJson();
*/
// TO

services.AddMvc();
```



## Swagger Ui

1. Install the `Swashbuckle.AspNetCore` NuGet package to your **Web** project.
2. 在 `startup.cs` 新增相關Swagger設定，可參考上面Swagger章節



## MySQL DB

1. 修正 `.web`專案 `appsettings`內`ConnectionStrings` 

   ` "Default": "Server=localhost;Database=ABPMysqlDb;Uid=root;Pwd=root;"`

2. 調整`.EntityFrameworkCore` Package

   - 移除`.Server`相關套件

   - 安裝 `Pomelo.EntityFrameworkCore.MySql`

   - 調整` ~DbContextConfigurer.cs`

     ```C#
     // dbContextOptions.UseSqlServer(connectionString);
     //  TO
     dbContextOptions.UseMySql(connectionString);
     ```

3. 移除`.EntityFrameworkCore`舊有Migrations

4. Open **Package Manager Console** and select the ***.EntityFrameworkCore** project.

5. Run the `add-migration Initial_Migration` command

6. Run the `update-database` command



## Appsetting

- 安裝`Microsoft.AspNetCore`

- 在**~CoreModule** 的建構子中載入 `HostingEnvironment`，並取得 `IConfigurationRoot`

  ```C#
  [DependsOn(typeof(AbpMailKitModule))]
  public class SimpleMailTaskCoreModule : AbpModule
  {
      private readonly IConfigurationRoot _appConfiguration;
  
      public SimpleMailTaskCoreModule(IHostingEnvironment env)
      {
          _appConfiguration = AppConfigurations
              .Get(env.ContentRootPath, env.EnvironmentName);
      }
  }
  ```

- 在`PreInitialize()`中透過DI註冊Config物件

  ```C#
  public override void PreInitialize()
  {
      IocManager.Register<MailServerConfig>();
      //....
  }
  
  ```

- 在 `Initialize()` 透過 `Configuration.GetSection('Config')` 取得`Appsetting`中的對應物件並Bind至Config 物件

  ```C#
  public override void Initialize()
  {    _appConfiguration.GetSection("MailServer")
      .Bind(Configuration.Get<MailServerConfig>());          
              
  }
  ```

- Service中即可透過 **Inject** 取得

  ```C#
  public class MailConfiguration : ISmtpEmailSenderConfiguration
  {     
      public MailConfiguration(IAbpStartupConfiguration config)
      {
          var mailConfig = config.Get<MailServerConfig>();
  
          Host = mailConfig.Host;
          Port = int.Parse(mailConfig.Port);
          UserName = mailConfig.UserName;
          Password = mailConfig.Password;
          Domain = mailConfig.Domain;
          DefaultFromAddress = mailConfig.DefaultFromAddress;
          DefaultFromDisplayName = mailConfig.DefaultFromDisplayName;
          EnableSsl = false;
          UseDefaultCredentials = false;
  
      }
  }
  ```

  





# 參考資料

1. [Swashbuckle.AspNetCore.Filters](https://github.com/mattfrear/Swashbuckle.AspNetCore.Filters)
2. [ Swagger 編寫文件的技巧和 Client Code Gen](https://dotblogs.com.tw/yc421206/2019/01/23/tips_write_write_web_api_document_via_swagger)
3. [ASP.Net Boilerplate](https://aspnetboilerplate.com/)
4. [ABP (ASP.NET Boilerplate) 應用程式開發框架新手教學](https://dotblogs.com.tw/jakeuj/2016/07/27/abp3)
5. [Volo.ABP](https://docs.abp.io/en/abp/latest/Getting-Started-AspNetCore-MVC-Template)













































































































































