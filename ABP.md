

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



## Add Token

```
services.AddSwaggerGen(options =>
{
  ...
      // Define the BearerAuth scheme that's in use
      options.AddSecurityDefinition("bearerAuth", new OpenApiSecurityScheme()
      {
      Description = "JWT Authorization header using the Bearer scheme. 
      Example: \"Authorization: Bearer {token}\"",
      Name = "Authorization",
      In = ParameterLocation.Header,
      Type = SecuritySchemeType.ApiKey
      });
});
```



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



------



# Json Web Token

- JWT是一種『有限時間內可利用認證令牌要求對應的操作權限』的一種方法

- 使用者登入後產生Token，後續針對Request內夾帶的Token進行驗證。

  - 客戶端從伺服器端取得認證簽名後，伺服器端只對認證簽名進行驗證，從認證簽名來判斷是不是具備指定的操作資格．除此之外還會判斷令牌是否過期、是否是黑名單等狀況。

- 主要驗證內容為產生Token時的資訊內容。Ex.

  - 相同的 **Issuer **設定值
  - 相同的 **Audience **設定值
  - 驗證 **Token **有效期限
    - 符合對稱式加密的簽章
  - Token內可包含自定義資訊，但驗證時不會針對使用者進行驗證，因為驗證時不會再連接DB。
  - 因此，只要產生Token的設定一致，不同Service也可以透過相同Token驗證

- 包含了 **header**、**payload**和**signature** 三個部分

  - **Header** 

    - 包含了兩個主要資訊：使用的加密演算法和token的類型(基本上就是JWT)
    - 物件之後會以base64Url的方式轉換成字串

    ```C#
    // 使用HS256演算法來產生JWT token
    {
      "alg": "HS256",
      "typ": "JWT"
    }
    ```

  - **Payload**

    - 包含了聲明的資料，這裡可以存放一些基本的驗證資訊
    - 定義上有 3 種聲明 (Claims)
      - Reserved (註冊聲明)
      - Public (公開聲明)
      - Private (私有聲明)
    - Reserved (註冊聲明)
      - iss (Issuer) - JWT的發行人
      - sub (Subject) - JWT的主題（用戶）
      - aud（audience）：JWT的目標收件人
      - exp（expiration time）：JWT到期的時間
      - nbf（not before time）：不得接受JWT處理的時間
      - iat（issued at time）：發布JWT的時間; 可用於確定JWT的年齡
      - jti（JWT ID）：唯一標識符; 可用於防止JWT被重放（允許令牌僅使用一次）
    - base64 加密（該加密是可以對稱解密的)

    ```C#
    /*
        sub就是RFC7519中定義的基本資訊，
        age則是我們自己加上去的
    */
    
    {
      "sub": "wellwind",
      "age": 30
    }
    ```

  - **Signature**

    - 用來確保資料完整性的一個雜湊簽章

    - 可以選用任何雜湊演算法來進行處理

- 驗證流程

  ![img](https://dotblogsfile.blob.core.windows.net/user/wellwind/c93407af-3191-48e8-9029-2e02a7e03b1e/1479955047_94613.png)

- 流程

  1. 使用者登入進行身分驗證後產生Token

     ```C#
     [Route("api/[controller]")]
     [ApiController]
     public class AuthController : ControllerBase
     {
         private readonly IConfiguration _config;
     
         public AuthController(IConfiguration configuration)
         {
             _config = configuration;
         }
     
         // GET api/auth/login
         [HttpGet, Route("login")]
         public IActionResult Login(string name)
         {
             // STEP0: 在產生 JWT Token 之前，可以依需求做身分驗證
     
             // STEP1: 建立使用者的 Claims 聲明，這會是 JWT Payload 的一部分
             var userClaims = new ClaimsIdentity(new[] {
                 new Claim(JwtRegisteredClaimNames.NameId, name),
                 new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString()),
                 new Claim("CustomClaim", "Anything You Like")
             });
             // STEP2: 取得對稱式加密 JWT Signature 的金鑰
             // 這部分是選用，但此範例在 Startup.cs 中有設定 ValidateIssuerSigningKey = true 所以這裡必填
             var securityKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(_config["Jwt:Key"]));
             // STEP3: 建立 JWT TokenHandler 以及用於描述 JWT 的 TokenDescriptor
             var tokenHandler = new JwtSecurityTokenHandler();
             var tokenDescriptor = new SecurityTokenDescriptor
             {
                 Issuer = _config["Jwt:Issuer"],
                 Audience = _config["Jwt:Issuer"],
                 Subject = userClaims,
                 Expires = DateTime.Now.AddMinutes(30),
                 SigningCredentials = new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256)
             };
             // 產出所需要的 JWT Token 物件
             var securityToken = tokenHandler.CreateToken(tokenDescriptor);
             // 產出序列化的 JWT Token 字串
             var serializeToken = tokenHandler.WriteToken(securityToken);
     
             return new ContentResult() { Content = serializeToken };
         }
     }
     ```

  2. 專案內加入驗證

     ```C#
     public void ConfigureServices(IServiceCollection services)
     {
         services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
     
         // STEP1: 設定用哪種方式驗證 HTTP Request 是否合法
         services
         // 檢查 HTTP Header 的 Authorization 是否有 JWT Bearer Token
         .AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
         // 設定 JWT Bearer Token 的檢查選項
         .AddJwtBearer(options =>
          {
            options.TokenValidationParameters = new TokenValidationParameters
            {
                ValidateIssuer = true,
                ValidIssuer = Configuration["Jwt:Issuer"],
                ValidateAudience = true,
                ValidAudience = Configuration["Jwt:Issuer"],
                ValidateLifetime = true,
                ValidateIssuerSigningKey = true,
                IssuerSigningKey = new SymmetricSecurityKey
                    (Encoding.UTF8.GetBytes(Configuration["Jwt:Key"]))
            };
          });
     }
     ```

  3. 驗證API

     ```C#
     [Route("api/[controller]")]
     [ApiController]
     public class ValuesController : ControllerBase
     {
         // GET api/values/anonymous
         /// <summary>使用匿名登入，無視於身分驗證</summary>
         [AllowAnonymous]
         [HttpGet, Route("anonymous")]
         public IActionResult Anonymous()
         {
             return new ContentResult() { Content = $@"For all anonymous." };
         }
     
         // GET api/values/authorize
         /// <summary>使用身分驗證，HTTP 的 Authorization Header 必須設定合法的 JWT Bearer Token 才能使用</summary>
         [Authorize]
         [HttpGet, Route("authorize")]
         public IActionResult All()
         {
             return new ContentResult() { Content = $@"For all client who authorize." };
         }
     }
     ```

## JwtRegisteredClaimNames 屬性

- 在建立使用者的 Claims 聲明時，我們會用到很多 `JwtRegisteredClaimNames` 結構型別，來取得是先定義好的字串

- |   Claim    | 說明                                                 | 連結                                         |
  | :--------: | :--------------------------------------------------- | -------------------------------------------- |
  |    Jti     | 表示 JWT ID，Token 的唯一識別碼                      | http://tools.ietf.org/html/rfc7519#section-4 |
  |    Iss     | 表示 Issuer，發送 Token 的發行者                     | http://tools.ietf.org/html/rfc7519#section-4 |
  |    Iat     | 表示 Issued At，Token 的建立時間                     | http://tools.ietf.org/html/rfc7519#section-4 |
  |    Exp     | 表示 Expiration Time，Token 的逾期時間               | http://tools.ietf.org/html/rfc7519#section-4 |
  |    Sub     | 表示 Subject，Token 的主體內容                       | http://tools.ietf.org/html/rfc7519#section-4 |
  |    Aud     | 表示 Audience，接收 Token 的觀眾                     | http://tools.ietf.org/html/rfc7519#section-4 |
  |    Typ     | 表示 Token 的類型，例如 JWT 表示 JSON Web Token 類型 | http://tools.ietf.org/html/rfc7519#section-4 |
  |    Nbf     | 表示 Not Before，定義在什麼時間之前，不可用          | http://tools.ietf.org/html/rfc7519#section-4 |
  |   Actort   | 識別執行授權的代理是誰                               | http://tools.ietf.org/html/rfc7519#section-4 |
  |   NameId   | 使用者識別碼                                         | http://tools.ietf.org/html/rfc7519#section-4 |
  | FamilyName | 使用者姓氏                                           | http://tools.ietf.org/html/rfc7519#section-4 |
  | GivenName  | 使用者名字                                           | http://tools.ietf.org/html/rfc7519#section-4 |
  |   Gender   | 使用者性別                                           | http://tools.ietf.org/html/rfc7519#section-4 |
  |   Email    | 使用者的電子郵件                                     | http://tools.ietf.org/html/rfc7519#section-4 |
  | Birthdate  | 使用者生日                                           | http://tools.ietf.org/html/rfc7519#section-4 |
  |  Website   | 使用者的網站                                         | http://tools.ietf.org/html/rfc7519#section-4 |

  

## Bearer Token 

- HTTP 的認證「Authorization」方案有許多種格式，而 Bearer 就是其中一種且被定義在 Header 中的驗證方案，通常搭配於 JWT 上

- Resource server 是資源服務器，即後端存放用戶生成的 API Token 的服務器。 
- Authorization server 的意思是認證服務器，即後端專門用來處理認證的服務器
- 這些東西都是由 [OAuth 2.0](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html) 中所定義出來的，在定義中說明了 Client 如何取得 Access Token 的方法



------



# Microsoft.AspNetCore.Identity

![1589101554152](C:\Users\sean2\AppData\Roaming\Typora\typora-user-images\1589101554152.png)

## BasicUser<TUser> 

- 定義後續Identity所需的TUser的基底類別

- 可僅定義基本屬性，後續繼承的類別再增加即可

  ```C#
  public class BasicUser<TUser> : Entity<long>
  {
  
      /// <summary>
      /// 用户名
      /// </summary>
      public virtual string UserName { get; set; }
  
      /// <summary>
      /// 邮箱地址
      /// </summary>
      public virtual string EmailAddress { get; set; }
  
      /// <summary>
      /// 密码
      /// </summary>
      public virtual string Password { get; set; }
          
  }
  ```

- 後續再方法中可定義傳入的User必須為上述基底類別或其衍生類別

  ```C#
   public class BasicLogInManager<TUser> : IDomainService, ITransientDependency
          where TUser : BasicUser<TUser>
  ```

  

## UserManager<TUser>

- 包含針對使用者CRUD的相關處理函式，並可繼承修改
- 應只包含邏輯，而不直接進行資料庫的處理
- 關聯: IUserStore<TUser>



##  IUserStore<TUser>

- 針對使用者資料進行處理而不包含商業邏輯的介面，內部負責實作與資料庫的CRUD。

- 繼承後可實作針對資料處理的相關功能，沒有硬性規定資料處理的方式

  - 如，不一定需要IRepository、或甚至不一定要進入資料庫

- 繼承後實作針對User資料的處理功能

- **UserManager** 內部針對使用者的CRUD都會用到

- 有資料庫的UserStore

  ```C#
  public class BasicUserStore<TUser> :
      IUserStore<TUser>,
      IUserPasswordStore<TUser>,
      ITransientDependency
      where TUser : BasicUser<TUser>
  {
      public BasicUserStore(
          IUnitOfWorkManager unitOfWorkManager,
          IRepository<TUser, long> userRepository
          )
      {
          _unitOfWorkManager = unitOfWorkManager;
          UserRepository = userRepository;
      }
  }
  ```

  

- 沒有資料庫連結

  ```C#
  public class SOCUserStore :
      IUserStore<User>,
      ITransientDependency
  {
      public Task<User> FindByNameAsync(string normalizedUserName, CancellationToken cancellationToken = default)
      {
          //加入呼叫SOC dll Function邏輯
          if (1 == 1)
              return Task.FromResult(new User
              {
                  UserName = "SeanChu",
                  EmailAddress = "SeanChu@hotmail.com",
                  Password = "3345678",
                  Id = 1
              });
  
          throw new NotImplementedException();
      }
  }
  ```

  

## UserClaimsPrincipalFactory<TUser>

- 內涵基本的產生User Claims方法



## IdentityRegistrar

- 將Identity使用到的相關方法進行注入

  ```C#
  public static IdentityBuilder Register(IServiceCollection services)
  {
      services.AddLogging();
  
      return services.AddIdentityCore<User>()
          .AddUserManager<UserManager>()
          .AddUserStore<SOCUserStore>()
          .AddClaimsPrincipalFactory<ClaimsPrincipalFactory>()
          .AddDefaultTokenProviders();
  }
  ```



## Extract User Info From Token

1. 透過**HttpContext **取得 `ClaimsPrincipal`

   ```C#
   [AbpAuthorize]
   public async Task<UserDto> GetCurrentUserInfoAsync()
   {
       try
       {
           //在ABP的 Application Layer中需要痛過 IHttpContextAccessor取得
           //_httpContextAccessor.HttpContext.User
           var user = await _userManager.GetUserAsync(HttpContext.User);
   
           return _objectMapper.Map<UserDto>(user);
       }
       catch (Exception ex)
       {
           throw new Exception(ex.Message);
       }
   }
   ```

2. 透過UserManager 實作 Principal提取

   - Principal內部包含`Identity`、`Claims` 可取得資訊

     ```C#
     public override string GetUserName(ClaimsPrincipal principal)
     {
         IEnumerable<Claim> claim = principal.Claims;
     
         var usernameClaim = claim
             .Where(x => x.Type == ClaimTypes.Name)
             .FirstOrDefault();
     
         return usernameClaim.Value;
     }
     ```

     



# ASP.Net Boilerplate

![ABP_Structure.png](https://github.com/Sean2416/MarkDown/blob/master/pics/ABP_Structure.png?raw=true)

## Domain Layer

- `.Core`包括 **Domain Entity** 和 **Domain Logic**，這是應用程式的核心層。
- 內含***資料表實體(Entity)***、***倉儲介面(Repository)***、***領域事件(Event)***、**工作單元(Unit of Work) ** 與 ***商業邏輯(Domain Services)***

### Entity

-  對應資料表實體
-  ASP.NET Boilerplate內的所有實體都需要繼承 `Entity` 

#### ID

- 類別Entity繼承後會自動含有一個資料型態為int的ID屬性，該類別還有一個泛型版本Entity<T>可以繼承

#### Auditing

- ABP還提供了 **IHasCreationTime** 這個介面來統一該屬性**建立時間**名稱為 **CreationTime** 

  ```C#
  public interface IHasCreationTime
  {
      DateTime CreationTime { get; set; }
  }
  ```

- **ICreationAudited** 除了 `CreationTime` 以外，還包含了**CreatorUserId**屬性

  ```C#
  public interface ICreationAudited : IHasCreationTime
  {
      long? CreatorUserId { get; set; }
  }
  ```

- **Modifications**

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

- **IAudited**  包含了 `ICreationAudited` 、`IModificationAudited`

  ```C#
  public interface IAudited : ICreationAudited, IModificationAudited
  {
  }
  ```

- 可以直接繼承`AuditedEntity`包含了上述欄位內容

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

- 繼承 **ISoftDelete** 的 Entity，在執行刪除動作時不會真的刪除資料。而是透過標記 `IsDeleted = True`進行刪除

- 查詢資料時，ABP也會自動過濾掉 `IsDeleted = True`的資料

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

- **IFullAudited**  繼承上述所有的延伸介面

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

-  **IPassivable** 定義了 **IsActive** 屬性

#### IExtendableObject

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
- `Entity` 跟  `VALUE OBJECT` 差別在於 `Entity`代表了資料的實體(包含可唯一的識別碼)， `VALUE OBJECT` 只包含針對物件的描述性類別。
- In Domain Driven Design (DDD), the Value Object is another type of domain object which can include business logic and is an essential part of the domain.

#### Base Classes

- ABP has two base classes for value objects:  **ValueObject** and **ValueObject<T>** . Both classes override the equality operator (and other related operator and methods) to compare the two value objects and assume that they are identical if all the properties are the same.

- For example, all of these tests pass:

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

  - **Get** 可透過`Id`欄位取得資料，當沒有資料時會產生 `Exception`
  - **Single** 透過 `expression<TDelegate> `取得資料，當資料筆數不為一筆時產生 `Exception`
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

- **GetAllList** 可取得資料庫內的所有資料

- **GetAll** 也可以取得所有資料，差別在於 **GetAll**不會立即執行而是要等到 `.ToList()` 後才會真的連限至資料庫

- **GetAllIncluding** 透過 `expression<TDelegate> `取得符合條件的資料

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

  - `T Query<T>(Func<IQueryable<TEntity>, T> queryMethod);`
  - `Query`提供 **lambda** 來做查詢條件篩選

  ```C#
  var people = _personRepository.Query(q => q.Where(p => p.Name.Contains("H")).OrderBy(p => p.Name).ToList());
  ```

- #### Insert

  - **Insert** method simply inserts new a entity in to a database and returns the same inserted entity.
  - **InsertAndGetId** method returns the Id of a newly inserted entity.
  - **InsertOrUpdate** method inserts or updates a given entity by checking its Id's value
  - **InsertOrUpdateAndGetId** method returns the Id of the entity after inserting or updating it.

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

  - 資料庫連線及交易的開啟在 **repository **被使用時自動啟動
  - 當一個 Request 完成或一個 **UOW** 的方法結束後，連線會自動關閉 **transaction**也會自動提交
  - 當 Request 中間產生 `Exception` 時， **transaction**會自動  **rolled back**並關閉連接
  - 當有多個Repository出現時，會共用相同的連結及**transaction**。由第一個執行的Repo開啟

### Domain Services

- 用來進行商業邏輯的處理

- 僅能透過 **Application Services** 或 其他**Domain Services** 呼叫，不能提供外部接口

- 繼承**IDomainService interface** 後就能以 **DI**的方式自動進行注入，提供其他地方呼叫

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

- 可以視為一個資料庫的生命週期

- ABP 透過**UOW** 來管理資料庫的**連線** 及 **Transaction**

  - 進入UOW後開啟資料庫連線及Transaction
  - 離開UOW後關閉連線及完成Transaction
  - 中間若產生 **Exception**則關閉連線並 **rolled back**

- 當產生兩個以上的 UOW  重疊時，以第一個進入點作為**連線** 及 **交易** 的起始。

- Some methods are unit of work methods by default:

  - All [MVC](https://aspnetboilerplate.com/Pages/Documents/MVC-Controllers), [Web API](https://aspnetboilerplate.com/Pages/Documents/Web-API-Controllers) and [ASP.NET Core MVC](https://aspnetboilerplate.com/Pages/Documents/AspNet-Core) Controller actions.
  - All [Application Service](https://aspnetboilerplate.com/Pages/Documents/Application-Services) methods.
  - All [Repository](https://aspnetboilerplate.com/Pages/Documents/Repositories) methods.

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

- `[UnitOfWork]` 可定義下述方法為一個 **UOW**
- Both repositories use the same unit of work.
- `Application Service` 不需要特別標註，預設就為一個 **UOW**.

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

- 透過 `IUnitOfWorkManager` 可以自己定義開始及結束的範圍
- `.Begin()`開啟一個UOW，並透過 `.Complete()`結束
- 如果沒有執行`.Complete()`，在程式結束後所有交易會被 **rolled back**

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

- 在 UOW 方法內透過 **repository** 取得的實體，在 UOW 結束後都會自動更新資料庫內容。不需要額外透過`.Update()`

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

#### IRepository.GetAll()

- `IQueryable`使用 **延遲** 執行，`GetAll()`不會直接進行資料庫查詢直到使用 **ToList() **
- 資料庫也會在 `IQueryable.ToList()` 執行之後才進行連線動作

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

- **UOW** 有提供行為對應的方法 ( **Completed**, **Failed** and **Disposed**)，可以透過註冊這些方法來做後續操作

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

####  1. Injecting IEventBus

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

####  2. 定義新的事件介面

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

####  3. 事件觸發

- 執行 `EventBus.Trigger` 就可以觸發**TaskCompletedEventData**事件

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

####  4. 實作事件處理

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

#####   Handled Exceptions

- ASP.NET Boilerplate defines **AbpHandledExceptionData** and triggers this event when it automatically handles an exception. 
- This is especially useful if you want to get more information about exceptions. You can register to this event to be informed when an exception occurs.

#####   Entity Changes

- There are also generic event data classes for entity changes
- A change can be insert, update or delete.
  - **EntityCreatingEventData<TEntity>, EntityCreatedEventData<TEntity>**, **EntityUpdatingEventData<TEntity>, EntityUpdatedEventData<TEntity>, EntityDeletingEventData<TEntity>** and **EntityDeletedEventData<TEntity>**. Also, there are **EntityChangingEventData<TEntity>** and **EntityChangedEventData<TEntity>**. 

- 'ing' events (e.g. EntityUpdating) are triggered before committing a transaction. 
  - you can rollback the [unit of work](https://aspnetboilerplate.com/Pages/Documents/Unit-Of-Work) and prevent an operation by throwing an exception.
- 'ed' events (e.g. EntityUpdated) are triggered after committing a transaction
  -  you cannot rollback the unit of work.

- Entity change events are defined in the **Abp.Events.Bus.Entities** namespace and are **automatically triggered**  when an entity is inserted, updated or deleted.
- If you have a Person entity, you can register to EntityCreatedEventData<Person> to be informed when a new Person is created and inserted into the database.
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
  -  ` EventBus.Trigger(new TaskCompletedEventData { TaskId = 42 });`

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

####  Pre-Defined Filters

#####   ISoftDelete

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

- you can disable pre-defined filters globally. add this code to the PreInitialize method of your module

  `Configuration.UnitOfWork.OverrideFilter(AbpDataFilters.SoftDelete, false);`

#####  Setting Filter

- The `SetFilterParameter` method also returns an IDisposable. So, we can use it in a **using** statement to automatically **restore the old value** after the using statement.

  `CurrentUnitOfWork.SetFilterParameter("PersonFilter", "personId", 42);`

  

------

## Application Layer

- `.Application`應用服務主要是表現層與領域層中間溝通的橋樑，提供了服務給表現層調用，讓表現層與領域層的耦合降低
- 不包含業務邏輯。
- 這層也提供了資料傳輸物件(DTO)，來與表現層互相傳遞資料，主要是Input與Output部分

###  Application Service

-  An Application Service is called from the presentation layer using a DTO (Data Transfer Object) as a parameter.
-  It also uses domain objects to perform some specific business logic and returns a DTO back to the presentation layer. 

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
- The CrudAppService base class is **generic**, which gets the related **Entity** and **DTO** types as generic arguments.
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

  2. 將 Input 引用至 `AsyncCrudAppService` 中，並複寫方法

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

  - `User` 內部包含使用者密碼欄位，該欄位再查詢時不應該被回傳回前端。

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
    
    [AutoMapTo(new [] { typeof(User) , typeof(Role) })]
    public class CreateUserInput : UserDto
    {
        public string Password { get; set; }
    }
    ```

- `DataAnnotation`驗證資料欄位

  - ```C#
    /// <summary>
    ///  客戶新增Input
    /// </summary>
    [AutoMapTo(typeof(SeatOrder))]
    public class CreateOrderInput
    {        
    
        /// <summary>
        /// 訂票人姓名
        /// </summary>
        [StringLength(10, ErrorMessage = "訂票人姓名不得超過10個字")]
        public string CustomerName { get; set; }
    
        /// <summary>
        /// 訂票人電話
        /// </summary>
        [StringLength(10)]
        public string CustomerPhone { get; set; }
    
        /// <summary>
        /// 座位號碼
        /// </summary>
        [StringLength(5)]
        public string SeatId { get; set; }
    
        /// <summary>
        /// 票價
        /// </summary>
        public decimal Price { get; set; }
    
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


### Object To Object Mapping

- 用來將物件結合至另一個物件

- **IObjectMapper ** 提供`map` 將來源物件`input `轉換成目標物件`User `

  ```C#
  public class UserAppService : ApplicationService
  {
      private readonly IRepository<User> _userRepository;
      private readonly IObjectMapper _objectMapper;
  
      public UserAppService(IRepository<User> userRepository, IObjectMapper objectMapper)
      {
          _userRepository = userRepository;
          _objectMapper = objectMapper;
      }
  
      public void CreateUser(CreateUserInput input)
      {
          var user = _objectMapper.Map<User>(input);
          _userRepository.Insert(user);
      }
  }
  ```

- `map` 也可以將來源直接轉換成已存在的物件

  - 以下為例， input 會轉換成**user** 類別，並將 input中的欄位內容取代原本的欄位
  - 由於 **UIO** 的特性，User會在Request結束後自動更新資料庫。

  ```C#
  public void UpdateUser(UpdateUserInput input)
  {
      var user = _userRepository.Get(input.Id);
      _objectMapper.Map(input, user);
  }
  ```

#### AutoMapper Integration

- **Abp.AutoMapper** 套件實作 `IObjectMapper` 及附加其他功能

  1. `Install-Package Abp.AutoMapper`

  2. 在 `~Module` 加上dependency

     ```C#
     [DependsOn(typeof(AbpAutoMapperModule))]
     public class MyModule : AbpModule
     {
         ...
     }
     ```

- ##### Auto Mapping Attributes

  - Most of the time you may only want to directly (and conventionally) map classes. In this case, you can use the **AutoMap**, **AutoMapFrom** and **AutoMapTo** attributes. 

  - 要特別注意 **From **跟 **To** 問題

  - ##### Custom Mapping

    - 假設兩個類別有些欄位相同但名稱相異，或有些欄位需要隱藏時可使用 `Abp.AutoMapper` 提供客製化的mapping功能

      ```C#
      [DependsOn(typeof(AbpAutoMapperModule))]
      public class MyModule : AbpModule
      {
          public override void PreInitialize()
          {
              Configuration.Modules.AbpAutoMapper().Configurators.Add(config =>
              {
                  config.CreateMap<CreateUserInput, User>()
                        .ForMember(u => u.Password, options => options.Ignore())
                        .ForMember(u => u.Email, options => options.MapFrom(input => input.EmailAddress));
              });
          }
      }
      ```

    - Auto Mapper has many more options and abilities for object to object mapping. See it's [documentation](http://automapper.org/) for more info.

### Validating-Data-Transfer-Objects

#### Using data annotations

- ASP.NET Boilerplate 支援欄位驗證的屬性 `Required`...

- There are also many attributes (like `MaxLength`, `MinLength`, `RegularExpression`...) in the **System.ComponentModel.DataAnnotations** namespace. 

  ```C#
  public class CreateTaskInput
  {
      public int? AssignedPersonId { get; set; }
  
      [Required]
      public string Description { get; set; }
  }
  ```

#### Custom Validation

- DTO 繼承 `ICustomValidate ` 可透過實作 **AddValidationErrors** 來作客製化驗證

- In addition to ICustomValidate, ABP also supports **.NET's standard IValidatableObject interface**. You can also implement it to perform additional custom validations. If you implement both interfaces, both of them will be called.

  ```C#
     public class SearchUserInput : ICustomValidate
      {        
          public string TaskId { get; set; }
  
          public void AddValidationErrors(CustomValidationContext context)
          {
              if (string.IsNullOrEmpty(TaskId))
              {
                  context.Results.Add(
                  new ValidationResult("TaskId Should No Be Null!"));
              }
          }
      }
  ```

#### Normalization

- 可在 DTO 驗證過後進行後續動作

- 繼承 **IShouldNormalize** 並實作 `Normalize `

  ```C#
  public class GetTasksInput : IShouldNormalize
  {
      public string Sorting { get; set; }
  
      public void Normalize()
      {
          if (string.IsNullOrWhiteSpace(Sorting))
          {
              Sorting = "Name ASC";
          }
      }
  }
  ```

  

## Background  Service

### Background Jobs

- 一次性的背景服務
- 所有的J **jobs**都會排入佇列中依序執行
- You may need background jobs for several reasons.
  - 執行時間過長，使用者不需要即時的Response.
    - EX. 使用者透過API進行報表查詢及Mail發送
  - 需要重複嘗試直到任務成功 
    - For example, you can send emails in a background job to overcome **temporary failures** and **guarantee** that it eventually will be sent. That way users do not wait while sending emails.

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

- Service 透過 `IBackgroundJobManager`呼叫

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

- **BackgroundJobManager** 

  - 採用先進先出的方式執行，並使用單一執行序

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

#### Quatz (排程)

- 與 **worker**的差別在於worker第一次不會執行，要等到 `timer` 到了才會開啟

- 排程會在Service啟動時候執使行，並持續執行`Timer`

- 使用  [**Abp.Quartz**](https://www.nuget.org/packages/Abp.Quartz) 

  1. NuGet 安裝套件

  2. 在`~Module`上加入`typeof (AbpQuartzModule)`

     ```C#
     [DependsOn(typeof (AbpQuartzModule))]
     public class YourModule : AbpModule
     {
         //...
     }
     ```

  3. 新增排程任務

     -  implement Quartz's IJob interface, or derive from the JobBase class

     ```C#
     public class MonthlyReportSchedule : JobBase, ITransientDependency
     {
         [UnitOfWork]
         public override Task Execute(IJobExecutionContext context)
         {
             Console.WriteLine("Executed MyLogJob..!");
     
             return Task.CompletedTask;
         }
     }
     ```

   ```C#
  4. 在 `~Module` 或 其他接口 Inject **IQuartzScheduleJobManager** 註冊排程
      
     public override void PostInitialize()
     {
         var jobManager = IocManager.Resolve<IQuartzScheduleJobManager>();
     
         jobManager.ScheduleAsync<MonthlyReportSchedule>(
         job =>
         {
             job.WithIdentity("MonthlyReportSchedule", "MyGroup")
                 .WithDescription("A job to Create Monthly Report And Send Mail");
         },
         trigger =>
         {
             trigger
                // .StartAt(DateTime.Parse(DateTime.Now.ToString("yyyy/MM/dd 14:27:00"))) 決定啟動時機
                 .StartNow()
                 .WithSimpleSchedule(schedule =>
                 {
                     schedule.RepeatForever()
                         .WithIntervalInMinutes(10)
                         .Build();
                 });
         });
     }
   ```

- ### trigger 任務觸發器

  - 用於定義使用何種方式出發任務(job)，同一個job可以定義多個trigger ，多個trigger 各自獨立的執行排程，每個trigger 中必須且只能定義一種觸發器型別(calendar-interval、simple、cron)

  - **WithSimpleSchedule**

    - 簡易型的任務觸發器

    - start-time 任務開始執行時間

    - repeat-count 任務執行次數

    - repeat-interval 任務觸發間隔

    - ```C#
      trigger                 
      .StartAt(DateTime.Now)
      .WithSimpleSchedule(schedule =>
      {
          schedule.RepeatForever()
          .WithIntervalInHours(24)
          .Build();
      });
      ```

  - **WithCronSchedule**

    - 參考: https://www.itread01.com/content/1549747264.html

    - 定時任務 

    - 表示式按照順序分別為秒，分，時，日，月，周，年

    - 表示式 - 7段構成：秒 分 時 日 月 星期 年

      - "-" ：表示範圍 MON-WED表示星期一到星期三
      - "," ：表示列舉 MON,WEB表示星期一和星期三
      - "*" ：表是“每”，每月，每天，每週，每年等
      - "/" :表示增量：0/15（處於分鐘段裡面） 每15分鐘，在0分以後開始，3/20 每20分鐘，從3分鐘以後開始
      - "?" :只能出現在日，星期段裡面，表示不指定具體的值
      - "L" :只能出現在日，星期段裡面，是Last的縮寫，一個月的最後一天，一個星期的最後一天（星期六）
      - "W" :表示工作日，距離給定值最近的工作日
      - "#" :表示一個月的第幾個星期幾，例如："6#3"表示每個月的第三個星期五（1=SUN...6=FRI,7=SAT）

    - 範例

      - | **Expression**           | **Meaning**                                                  |
        | ------------------------ | ------------------------------------------------------------ |
        | 0 0 12 * * ?             | 每天中午12點觸發                                             |
        | 0 15 10 ? * *            | 每天上午10:15觸發                                            |
        | 0 15 10 * * ?            | 每天上午10:15觸發                                            |
        | 0 15 10 * * ? *          | 每天上午10:15觸發                                            |
        | 0 15 10 * * ? 2005       | 2005年的每天上午10:15觸發                                    |
        | 0 * 14 * * ?             | 在每天下午2點到下午2:59期間的每1分鐘觸發                     |
        | 0 0/5 14 * * ?           | 在每天下午2點到下午2:55期間的每5分鐘觸發                     |
        | 0 0/5 14,18 * * ?        | 在每天下午2點到2:55期間和下午6點到6:55期間的每5分鐘觸發      |
        | 0 0-5 14 * * ?           | 在每天下午2點到下午2:05期間的每1分鐘觸發                     |
        | 0 10,44 14 ? 3 WED       | 每年三月的星期三的下午2:10和2:44觸發                         |
        | 0 15 10 ? * MON-FRI      | 週一至週五的上午10:15觸發                                    |
        | 0 15 10 15 * ?           | 每月15日上午10:15觸發                                        |
        | 0 15 10 L * ?            | 每月最後一日的上午10:15觸發                                  |
        | 0 15 10 L-2 * ?          | Fire at 10:15am on the 2nd-to-last last day of every month   |
        | 0 15 10 ? * 6L           | 每月的最後一個星期五上午10:15觸發                            |
        | 0 15 10 ? * 6L           | Fire at 10:15am on the last Friday of every month            |
        | 0 15 10 ? * 6L 2002-2005 | 2002年至2005年的每月的最後一個星期五上午10:15觸發            |
        | 0 15 10 ? * 6#3          | 每月的第三個星期五上午10:15觸發                              |
        | 0 0 12 1/5 * ?           | Fire at 12pm (noon) every 5 days every month, starting on the first day of the month. |
        | 0 11 11 11 11 ?          | Fire every November 11th at 11:11am.                         |
        | 0/5 * * * * ?            | 每隔5秒執行                                                  |
        | 0 0/1 * * * ?            | 每隔一分鐘從0開始                                            |
        | 0 3/20 * * * ?           | 小時中從第3分鐘開始，每隔20分鐘執行                          |
        | 0 15 10 ? * MON-FRI      | 在星期一到星期五的每天上午10點15分執行                       |
        | 0 15 10 ? * 6L 2007-2010 | 在2007年到2010年的每個月的最後一個星期五上午10點15分執行     |

        

    - ```C#
      trigger =>
      {
          trigger
              .WithCronSchedule($"0 30 9,15 * * ?")
              .WithIdentity("MonthlyReportTrigger")
              .StartNow();
      });
      ```

      





## Web API

- ABP 預設將 **Public** 並繼承 **IApplicationService** 的Service視為WebApi

- 預設為**Post**方法，並透過**前贅字命名**的方式定Http 的方法

  - **Get**: Used if the method name starts with ***Get***.
  - **Put**: Used if the method name starts with ***Put*** or ***Update***.
  - **Delete**: Used if the method name starts with ***Delete*** or ***Remove***.
  - **Post**: Used if the method name starts with ***Post*** ,  ***Create***  or ***Insert***.
  - **Patch**: Used if the method name starts with ***Patch***.
  - Otherwise, **Post** is used **by default** as an HTTP verb.

- Service內的方法可透過 **RemoteService** 定義是否可由外部連接

  ```C#
  [RemoteService(false)]
  public ListResultDto<MonthlyReportOutPutDto> GetMonthlyReport(SearchCustomerReportDto input)
  {
      var result = _Repository.GetAll()
          .Where(col => col.CustomerName.Equals(input.CustomerName))
          .ToList();
  
      return new ListResultDto<MonthlyReportOutPutDto>(
          ObjectMapper.Map<List<MonthlyReportOutPutDto>>(result));
   }
  ```

### 錯誤處理

- **ABP** 自動將 Exception包入 **AbpExceptionFilter**，錯誤內容會以 **Json** 的形式回傳 
  - `AbpAuthorizationException` 回傳 **401 (unauthorized)**
  - `AbpValidationException` 回傳 **400 (bad request)**
  - `EntityNotFoundException` 回傳 **404 (not found)**
  - `Exception`、` `回傳 **500 (internal server error)**

###  開發順序

1. 在`.Core `新建資料實體，並繼承 ABP`Entity`

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

2. 在`.EntityFrameworkCore` 的 `~DbContext中`加入

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

5. 修正`.web` 專案 `startup.cs` 內　`ConfigureServices`

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

### Swagger Ui

1. Install the `Swashbuckle.AspNetCore` NuGet package to your **Web** project.

2. 在 `startup.cs` 新增相關Swagger設定，可參考上面Swagger章節

   1. Debug及Release都需要設定

3. 在Application中使用XML 

   1. 調整Application Layer屬性中的輸出

      ![img](https://img-blog.csdnimg.cn/20190802112350427.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dhcjNpc215bG92ZQ==,size_16,color_FFFFFF,t_70)

   2. StartUp變更

      ```C#
      services.AddSwaggerGen(c =>
      {
          c.SwaggerDoc("v1", new OpenApiInfo
          {
              Version = "v1",
              Title = "ToDo API",
              Description = "A simple example ASP.NET Core Web API"
          });
      
          c.DocInclusionPredicate((docName, description) => true);
      
          //將application 層的註解加入Swagger
          var baseDirectory = AppDomain.CurrentDomain.BaseDirectory;
      	
          //指向application的輸出文件名稱
          var CommentsFileName = @"SOC_APIService.Application.xml";
          var CommentsFile = Path.Combine(baseDirectory, CommentsFileName);
          //将注释的Xml文档添加到swaggerUi中
          c.IncludeXmlComments(CommentsFile);
      });
      ```

   3. 其餘同上

   4. 參考: https://blog.csdn.net/war3ismylove/article/details/98182680?utm_medium=distribute.pc_relevant.none-task-blog-baidujs-1



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
  
      public SimpleMailTaskCoreModule(IWebHostEnvironment env)
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

  

## Multiple Database

- 實作方法: 

  - 透過不同的DbContext決定每一個Entity連線的資料庫。
  - 亦即，同一個Entity無法再不同DbContext中

- 建置步驟:

  1. 新增資料庫連線字串 `appsettings.json`

     ```C#
     "ConnectionStrings": {
       "MsSql": "Server=localhost\\SQLExpress01; Database=MultipleDataBaseDb; Trusted_Connection=True;",
       "MySql": "Server=localhost;Database=SOC_DashBord;Uid=root;Pwd=root;"
     }
     ```

  2. 新增第二個字串名稱對應 `.core/ ~Consts`

     ```C#
     public class MultipleDataBaseConsts
     {
         public const string LocalizationSourceName = "MultipleDataBase";
     
         public const string ConnectionStringName = "MsSql";
     
         public const string MySqlConnectionStringName = "MySql";
     }
     ```

  3. 建立Entity

     ```C#
     [Table("Flow")]
     public class Flow : Entity, IHasCreationTime
     {
         public virtual string DevID { get; set; }
         //....
     }
     
     [Table("Flow")]
     public class FlowLog : Entity, IHasCreationTime
     {
         public virtual string DevID { get; set; }
         //....
     }
     ```

  4. 將第一個資料庫的Entity加入DbContext中

     ```C#
     public class MultipleDataBaseDbContext : AbpDbContext
     {
         public DbSet<Flow> Flows { get; set; }
     
         public MultipleDataBaseDbContext(DbContextOptions<MultipleDataBaseDbContext> options) 
             : base(options)
         {
     
         }
     }
     ```

  5. 在 `.EntityFrameworkCore`內建立第二個DbContext ，**SecondDbContext**

     ```C#
     public class SecondDbContext : AbpDbContext
     {
         public DbSet<FlowLog> FlowLogs { get; set; }
     
         public SecondDbContext(DbContextOptions<SecondDbContext> options) 
             : base(options)
         {
     
         }
     }
     ```

  6. **DbContextConfigurer** 調整 (如果有需要，ex.變更資料庫為MySql)

  7. 在 `.EntityFrameworkCore`內新增第二個DbContextConfigure，**SecondDbContextConfigurer**

     ```C#
     public static class SecondDbContextConfigurer
     {
         public static void Configure(
             DbContextOptionsBuilder<SecondDbContext> dbContextOptions, 
             string connectionString
             )
         {
             dbContextOptions.UseMySql(connectionString);
         }
         public static void Configure(DbContextOptionsBuilder<SecondDbContext> builder, DbConnection connection)
         {
             builder.UseMySql(connection);
         }
     }
     ```

  8. 在 `.EntityFrameworkCore`內新增第二個Factory，**SecondDbFactory**

     ```C#
     public class SecondDbFactory : IDesignTimeDbContextFactory<SecondDbContext>
     {
         public SecondDbContext CreateDbContext(string[] args)
         {
             var builder = new DbContextOptionsBuilder<SecondDbContext>();
     
             var configuration = AppConfigurations.Get(WebContentDirectoryFinder.CalculateContentRootFolder());
     
             SecondDbContextConfigurer.Configure(builder, configuration.GetConnectionString(MultipleDataBaseConsts.MySqlConnectionStringName));
     
             return new SecondDbContext(builder.Options);
         }
     }
     ```

  9. 在 `.EntityFrameworkCore`內新增 **MyConnectionStringResolver**

     ```C#
     public class MyConnectionStringResolver : DefaultConnectionStringResolver
     {
         private readonly IConfigurationRoot _appConfiguration;
     
         public MyConnectionStringResolver(IAbpStartupConfiguration configuration, IWebHostEnvironment hostingEnvironment)
             : base(configuration)
         {
             _appConfiguration =
                 AppConfigurations.Get(hostingEnvironment.ContentRootPath, hostingEnvironment.EnvironmentName);
         }
     
         public override string GetNameOrConnectionString(ConnectionStringResolveArgs args)
         {
             if (args["DbContextConcreteType"] as Type == typeof(EyeCloudDbContext))
             {
                 return _appConfiguration.GetConnectionString(SOC_ETLServiceConsts.EyeCloudConnectionStringName);
             }
     
             return base.GetNameOrConnectionString(args);
         }
      }
     ```

  10. 調整 `~EntityFrameworkCoreModule`，將 **MyConnectionStringResolver **取代 **IConnectionStringResolver**，並調整 `PreInitialize()`

      ```C#
      public override void PreInitialize()
      {
          Configuration.ReplaceService<IConnectionStringResolver, MyConnectionStringResolver>();
      
           // Configure first DbContext
          Configuration.Modules.AbpEfCore().AddDbContext<MultipleDataBaseDbContext>(options =>
          {
              if (options.ExistingConnection != null)
              {
                  DbContextOptionsConfigurer.Configure(options.DbContextOptions, options.ExistingConnection);
              }
              else
              {
                  DbContextOptionsConfigurer.Configure(options.DbContextOptions, options.ConnectionString);
              }
          });
          
      // Configure second DbContext
          Configuration.Modules.AbpEfCore().AddDbContext<SecondDbContext>(options =>
          {
              if (options.ExistingConnection != null)
              {
                  SecondDbContextConfigurer.Configure(options.DbContextOptions, options.ExistingConnection);
              }
              else
              {
                  SecondDbContextConfigurer.Configure(options.DbContextOptions, options.ConnectionString);
              }
          });
      }
      ```

  11. 測試

      ```C#
      public class DBCopySchedule : JobBase, ITransientDependency
      {
          private readonly IRepository<Flow> _ReportRepository;
          private readonly IRepository<FlowLog> _LogRepository;
      
          public DBCopySchedule(
              IRepository<Flow> ReportRepository,
              IRepository<FlowLog> logRepository
              )
          {
              _ReportRepository = ReportRepository;
              _LogRepository = logRepository;
          }
      
          [UnitOfWork]
          public override Task Execute(IJobExecutionContext context)
          {
              try
              {
                  Console.WriteLine("Executed MyLogJob..!");
                  CreateFlowLog(GetLogData());
                  return Task.CompletedTask;
              }
              catch (Exception ex)
              {
                  Console.WriteLine($"Error Comes From Mail Service : {ex.Message}");
                  throw new Exception("Something awful happened", ex, false);
              }
          }
      
          private List<FlowLog> GetLogData()
          {
              //Get Data From MySql
              var flowList = _LogRepository.GetAll().ToList();
              return flowList;
          }
      
          private void CreateFlowLog(List<FlowLog> flowList)
          {
              try
              {         
              	//Write Data In MSSqlDB     
                  foreach (var flow in flowList)
                      _ReportRepository.Insert(new Flow
                      {
                          Proto = 999,
                          DevID = flow.DevID,
                          Dhost = flow.Dhost,
                          Act = flow.Act,
                          Stime = flow.Stime,
                          Sip = flow.Sip,
                          Dip = flow.Dip,
                      });
      
                  flowList.First().Proto = 888;
              }
              catch (Exception ex)
              {
                  Console.WriteLine($"Error Comes From Mail Service : {ex.Message}");
              }
          }
      }
      ```



## User

- **AbpUser** 內包含了APB對於User的基本定義

  - **UserName**: Login name of the user. Should be **unique** for a [tenant](https://aspnetboilerplate.com/Pages/Documents/Zero/Tenant-Management).
  - **EmailAddress**: Email address of the user. Should be **unique** for a [tenant](https://aspnetboilerplate.com/Pages/Documents/Zero/Tenant-Management).
  - **Password**: Hashed password of the user.
  - **IsActive**: True, if this user can login to the application.
  - **Name** and **Surname** of the user.
  - **FullAuditedEntity**內定義的延伸欄位

- 可自行新增欄位，只要Entity修正後新增database migrations並更新資料庫即可

  ```C#
  [Table("User")]
  public class User : AbpUser<User>
  {
      public const string DefaultPassword = "123qwe";
  
      public virtual string CustomerColumn { get; set;}
   }
  ```

### User Manager

- **UserManager**是ABP用來處理User相關Domain Logic的Service

- 內部有提供一些方法可以執行，或可以透過 **override** 進行覆寫

- 在**multi-tenant**的狀態下，**UserManager**只會針對現在登入(session)的tenant進行處理

  - 針對tenant的處理範例

    ```C#
    public class MyTestAppService : ApplicationService
    {
        private readonly UserManager _userManager;
    
        public MyTestAppService(UserManager userManager)
        {
            _userManager = userManager;
        }
    
        public void TestMethod_1()
        {
            //Find a user by email for current tenant
            var user = _userManager.FindByEmail("sampleuser@aspnetboilerplate.com");
        }
    
        public void TestMethod_2()
        {
            //Switch to tenant 42
            CurrentUnitOfWork.SetFilterParameter(AbpDataFilters.MayHaveTenant, AbpDataFilters.Parameters.TenantId, 42);
    
            //Find a user by email for tenant 42
            var user = _userManager.FindByEmail("sampleuser@aspnetboilerplate.com");
        }
    
        public void TestMethod_3()
        {
            //Disabling MayHaveTenant filter, so we can reach all users
            using (CurrentUnitOfWork.DisableFilter(AbpDataFilters.MayHaveTenant))
            {
                //Now, we can search for a username in all tenants
                var users = _userManager.Users.Where(u => u.UserName == "sampleuser").ToList();
    
                //Or we can add TenantId filter if we want to search for a specific tenant
                var user = _userManager.Users.FirstOrDefault(u => u.TenantId == 42 && u.UserName == "sampleuser");
            }
        }
    }
    ```

### User Login

- ABP預先定義了 **LoginManager** 來處理登入的相關邏輯。
- **LoginAsync** 自動儲存所有的登入行為，可以透過 **UserLoginAttempt**  查詢

### Authentication

- ABP 預設使用 JWT Token進行驗證。

  1. 使用者透過帳號/密碼進行登入
  2. ABP驗證使用者登入資訊
  3. 驗證成功，產生Token
  4. 使用者在Token失效前皆可透過Token進行操作

- Token驗證資訊的設定放在 `appsettings.json`

- 如同一般的 JWT 驗證，只有驗證設定的內容一致即使是誇Service也可以使用同一個Token

  ```C#
  "Authentication": {
      "JwtBearer": {
        "IsEnabled": "true",
        "SecurityKey": "SOC_DashBord_C421AAEE0D114E9C",
        "Issuer": "SOC_DashBord",
        "Audience": "SOC_DashBord"
      }
    }
  ```

- ### External Authentication

  - **UserManager ** 提供覆寫驗證方式的功能。只需要繼承 **IExternalAuthenticationSource** 或 **DefaultExternalAuthenticationSource** 

  ```C#
  public class MyExternalAuthSource : DefaultExternalAuthenticationSource<Tenant, User>, ITransientDependency
  {
      public override string Name
      {
          get { return "MyCustomSource"; }
      }
  
      public override Task<bool> TryAuthenticateAsync(string userNameOrEmailAddress, string plainPassword, Tenant tenant)
      {
          //TODO: authenticate user and return true or false
          return Task.FromResult(true);
      }
  }
  ```

  - 在 `TryAuthenticateAsync` 方法裡面，可以直接實作自己的驗證方式並回傳是否驗證成功

  - 另外，也可以覆寫`CreateUser ` 及 `UpdateUser`變更使用者的新增及更新

  - 當使用外部驗證時，ABP會自動確認資料庫內是否有包含這個User。

    - 沒有，則新增User資料
    - 有，則更新User

  - 新增完驗證Service後，再透過DI的方式加入至`~module` 中

    ```C#
    Configuration.Modules.Zero().UserManagement.ExternalAuthenticationSources.Add<MyExternalAuthSource>();
    ```

    

### Multi-Tenancy

- ABP 內建提供多組織架構，資料表內 加入`IMayHaveTenant` 、`IMustHaveTenant` 就能在查找資料時自動過濾組織資料。

  - ###### 在 `IAbpSession`中有一個 `TenantId`的值，預設取得Claims中的`AbpClaimTypes.TenantId`值。

  - ###### 如果開啟多組織架構時資料表會自動根據TenantId去做資料篩選。(例如，登入者Session內TenantId取得為`組織編號3`，則有使用多組織註記的資料表會自動篩選編號為3的內容出來)

  - ###### 多組織標記

    - ######  IMustHaveTenant: 加入此註記的資料表會自動產生`TenantId`欄位，且該欄位為必填。並且在新增資料時會自動根據登入者的TenantId塞入欄位資料。
  
    - ###### IMayHaveTenant: 加入此註記的資料表會自動產生`TenantId`欄位，該欄位為非必填。在進行資料新增時，必須主動給予TenantId欄位數值。
  
    - ###### 兩則標記共通處在於查詢資料時，只要有開啟多組織標記則資料表會自動根據登入者TenantId篩選資料
  
    - ```C#
      Configuration.MultiTenancy.IsEnabled = true; //在Module內加入開啟多組織架構
      ```
  
    - ###### 沒有使用多組織標記的資料表不在此限中
  
- ###### 由於目前專案屬性，因此框架預設不啟用多組織架構。下面簡要說明加入多組織架構的流程

  1. ######  在 `~CoreModule` > `PreInitialize`內加入

     ```C#
     onfiguration.MultiTenancy.IsEnabled = true; //啟動多組織架構
     ```

  2. ######  實作組織架構的相關資料表、函式及使用者關聯。並在登入時根據登入者身分加入組織編號資料進入Claims中

     1. 登入時取得登入者組織編號

     2. 於 `Authorization` > `ClaimsPrincipalFactory` 內加入`tenantId`

        ```C#
        principal.Identities.First().AddClaim(new Claim(AbpClaimTypes.TenantId, user?.TenantId.ToString()));
        ```

  3. ######  `Authorization`  >  `SessionManager` 預設取得 Claims內 `AbpClaimTypes.TenantId`資料作為登入者的 TenantId，若有需要變更請自行改寫

  4. ###### 資料表內 加入`IMayHaveTenant` 、`IMustHaveTenant` ，系統即會自動根據TenantId欄位進行資料過濾

- ## Notice

  - ###### ABP預設有PowerUser的權限，當Session回傳的`TenantId`為NULL時，該登入者即視為最高權限使用者。該使用者可以查看所有的資料表，而不會根據TenantId篩選。

- Tenancy類似組織的概念，ABP有支援在一個服務中包含多組織。

  - ##### Single Deployment - Multiple Database

    - ABP可以透過 **tenant**  資料表內定義的 `ConnectionString` 取得各組織對應的資料庫

  - ##### Single Deployment - Single Database

    - 在各資料表內透過**TenantId** 過濾資料

#### Tenant Entity

- 在**AbpTenant** 有定義一些基本的屬性

  - **TenancyName** : 類似組織代碼，必須是唯一值。且通常是不可變更
  - **Name**: 組織名稱
  - **IsActive**: 啟用狀態

- 其他自訂欄位可透過繼承**AbpTenant** 來新增

  ```C#
  [Table("Tenant")]
  public class Tenant : AbpTenant<User>
  {
      public virtual string CustomerColumn { get; set;}
      
      public Tenant()
      {            
      }
  
      public Tenant(string tenancyName, string name)
          : base(tenancyName, name)
      {
      }
  }
  ```

#### Tenant Manager

- ABP預設**AbpTenantManager** 來管理Tenant

- 開放繼承來修改裡面的Funtion

  ```C#
  public class TenantManager : AbpTenantManager<Tenant, Role, User>
  {
      public TenantManager(IRepository<Tenant> tenantRepository)
          : base(tenantRepository)
      {
  
      }
  }
  ```

####   IMustHaveTenant Interface

- 在實體內加入 `IMustHaveTenant`，ABP就會自動將這張資料表視為需要切分組織的資料表

```C#
public class Product : Entity, IMustHaveTenant
{
    public int TenantId { get; set; }

    public string Name { get; set; }

    //...other properties
}
```

####   IMayHaveTenant interface

- A **null** value means this is a **host** entity, a **non-null** value means this entity is owned by a **tenant** where the Id is the **TenantId**.

```C#
public class Role : Entity, IMayHaveTenant
{
    public int? TenantId { get; set; }

    public string RoleName { get; set; }

    //...other properties
}
```

####   Enabling Multi-Tenancy

- `Configuration.MultiTenancy.IsEnabled = true; `

####   Ignore Feature Check For Host Users

- `Configuration.MultiTenancy.IgnoreFeatureCheckForHostUsers = true; `

### Session

- ABP提供 **IAbpSession**  來取得目前登入者的資訊

- Tenant會在使用者登入的時候，使用者需決定這次的Session對應的tenant

- Application Layer已經將**AbpSession** 進行DI，因此可直接使用

  ```C#
  public class MyClass : ITransientDependency
  {
      public IAbpSession AbpSession { get; set; }
  
      public MyClass()
      {
          AbpSession = NullAbpSession.Instance;
      }
  
      public void MyMethod()
      {
          var currentUserId = AbpSession.UserId;
          //...
      }
  }
  ```

  

### Permissions

- 針對角色設定的權限，只要屬於此角色的使用者都會繼承該權限

- ABP針對權限的設定可分為兩種

  - 針對整個Service
  - 針對CRUD各別行為

- 權限管理流程

  1. 建立權限對應列表，避免使用*Magic String*

     ```C#
     public static class PermissionNames
     {
         public const string Pages_Tenants = "Pages.Tenants";
     
         public const string Pages_Users = "Pages.Users";
     
         public const string Pages_Roles = "Pages.Roles";
     
         public const string MyTaskCreationPermission = "MyTaskCreationPermission";
     }
     ```

  2. 建立繼承**AuthorizationProvider**  的類別來定義權限內容

     ```C#
     public class ABP_SidePorjectAuthorizationProvider : AuthorizationProvider
     {
         public override void SetPermissions(IPermissionDefinitionContext context)
         {
             context.CreatePermission(PermissionNames.MyTaskCreationPermission, L("Create TTTTT"));
             context.CreatePermission(PermissionNames.Pages_Users, L("Users"));
             context.CreatePermission(PermissionNames.Pages_Roles, L("Roles"));
             context.CreatePermission(PermissionNames.Pages_Tenants, L("Tenants"), multiTenancySides: MultiTenancySides.Host);
         }
     
         private static ILocalizableString L(string name)
         {
             return new LocalizableString(name, ABP_SidePorjectConsts.LocalizationSourceName);
         }
     }
     ```

     - **IPermissionDefinitionContext**  has methods to get and create permissions
       - **Name**: 第一個參數，權限代碼不可重複。
       - **Display name**: 會影響到之後前端顯示的名稱
       - **Description**: 影響後續前端顯示對該權限的定義
       - **MultiTenancySides**: 定義該權限只有 **Host** 或是 tenant使用

  3. 權限是針對 **Module**定義的，因此要在 `PreInitialize` 注入

     ```C#
     // ~.Application/PreInitialize()
     
     Configuration.Authorization.Providers.Add<ABP_SidePorjectAuthorizationProvider>();
     ```

  4. 在 **AppService** 使用

     ```C#
     //定義進入這個Service至少需要.Pages_Roles權限
     [AbpAuthorize(PermissionNames.Pages_Roles)] 
         public class RoleAppService : AsyncCrudAppService<Role, RoleDto, int, PagedRoleResultRequestDto, CreateRoleDto, RoleDto>, IRoleAppService
     {
     	public RoleAppService()
             : base(repository)
         {
             //定義要使用Create的方法必須要有.MyTaskCreationPermission才行
             CreatePermissionName = PermissionNames.MyTaskCreationPermission;
         }
             
         //沒有.MyTaskCreationPermission的權限會在這邊被擋掉
         [AbpAuthorize(PermissionNames.MyTaskCreationPermission)]
     	public override async Task<RoleDto> CreateAsync(CreateRoleDto input)
         {
             try
             {
                 
                 //....
             }
             catch (Exception ex) {
                 throw ex;
             }
         }
     
     }
     ```

  5. 如果只加入 **AbpAuthorize **，則只會確認使用者有沒有登入而不會管各別權限

     ```C#
     //AbpAllowAnonymous 將不進行驗證
     [AbpAuthorize]
     public void SomeMethod(SomeMethodInput input)
     {
         //A user can not execute this method if he did not login.
     }
     ```

  6. 更細部的function權限可使用**IPermissionChecker** 管理

     ```C#
     public void CreateUser(CreateOrUpdateUserInput input)
     {
         if (!PermissionChecker.IsGranted("Administration.UserManagement.CreateUser"))
         {
             throw new AbpAuthorizationException("You are not authorized to create user!");
         }
     
         //A user can not reach this point if he is not granted for "Administration.UserManagement.CreateUser" permission.
     }
     ```

  7. 或直接使用.**Authorize** 

     ```C#
     public void CreateUser(CreateOrUpdateUserInput input)
     {
         PermissionChecker.Authorize("Administration.UserManagement.CreateUser");
     
         //A user can not reach this point if he is not granted for "Administration.UserManagement.CreateUser" permission.
     }
     ```

### ABP預設多組織流程

1. 第一次啟動Service會預設建置**admin** 帳號做為 **Host**，及建立Default tenant。
   - 若為單一組織服務，則可將人加入default的tenant即可
2. admin使用Host權限登入(不輸入tenant資料)，新增組織(tenant)。
   - 新增成功後，ABP會自動在AbpUsers內新增user及tenant的對應表
   - 新增成功後，ABP會自動在role內產生該tenant的admin腳色
3. 建立組織使用者，並給予admin最高權限腳色
   - 使用者可以包含多個腳色，並取得所有繼承腳色的集合權限
4. 登出使用者，並切換帳號後確認該腳色權限
5. 進行後續組織維護

![Untitled Diagram](C:\Users\6591\Downloads\Untitled Diagram.jpg)



## Entity Framework Core

- 隱藏Entity欄位，不建立在資料庫中

  ```C#
   modelBuilder.Entity<Cusotmer>().HasIndex(r=>r.Name).HasName("Index_Name").IsUnique();
   modelBuilder.Entity<RolePermission>().HasIndex(r => new { r.TenantId, r.RoleCode }).HasName("Index_Code").IsUnique();
  ```

- 建立資料庫Key、索引

  - 在**OnModelCreating**地方加入

  ```C#
   modelBuilder.Entity<User>().Ignore(a => a.Name);
   modelBuilder.Entity<User>().Ignore(a => a.Surname);
  ```

  - 此外，如果是繼承來的欄位要隱藏時，如果原先的欄位為必填則需將之改為 **private**處理。

  ```C#
  [Table("User")]
  public class User : AbpUser<User>
  {
      private new string Name { get; set; }
  
      private new string Surname { get; set; }
  
  }
  
  /*
   這兩個欄位在AbpUser中都是必填欄位，改成private後就不會再對它們進行驗證
    //
          // 摘要:
          //     Surname of the user.
          [Required]
          [StringLength(64)]
          public virtual string Surname { get; set; }
          //
          // 摘要:
          //     Name of the user.
          [Required]
          [StringLength(64)]
          public virtual string Name { get; set; }
  */
  ```


- 加入Schema 

  - 在 **~DbContext** 加入

  ```C#
  protected override void OnModelCreating(ModelBuilder modelBuilder)
  {
  	//搜尋的結果會變為 Select * FROM sche_bpms.XX
      modelBuilder.HasDefaultSchema("sche_bpms");
      base.OnModelCreating(modelBuilder);
  }
  ```

- SQL View查詢

  1. 建立 View **Class**

     ```C#
     [Table("vw_UserRole")]
     public class VUserRole : Entity
     {
         [Column("User_SN")]
         public override int Id { get; set; }
     
         [Column("User_ID")]
         public string UserName { get; set; }
     
         [Column("User_Name")]
         public string Name { get; set; }
     }
     ```

  2. 建立 **Mapping**

     ```C#
     public class VUserRoleMap : IEntityTypeConfiguration<VUserRole>
     {
         public void Configure(EntityTypeBuilder<VUserRole> builder)
         {
             builder.ToTable("vw_UserRole");
        }
     }
     ```

  3. ~DBContext處理

     ```C#
     protected override void OnModelCreating(ModelBuilder modelBuilder)
     {
         modelBuilder.Ignore<VUserRole>();
         modelBuilder.ApplyConfiguration(new VUserRoleMap());
         base.OnModelCreating(modelBuilder);
     }
     ```

  4. 若View沒有 **primary key**

     1. 隱藏 ID 欄位

        ```C#
        [NotMapped]
        public override int Id
        {
            get { return 0; }
            set { /* nothing */ }
        }
        ```

     2. 建立其他鍵值

        ```C#
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Ignore<VUserRole>();
            modelBuilder.ApplyConfiguration(new VUserRoleMap());
            
            modelBuilder.Entity<VUserRole>().HasKey(r=>new { r.RoleSN , r.UserSN});
            base.OnModelCreating(modelBuilder);
        }
        ```

        

## 環境設定

- 使用 `appsettings.json`控管環境變數

- 建置環境設定檔

  - 建置順序=> 先取 `appsettings` > 再取 `appsettings.XX.json`，有重複的設定則蓋過去
  - ![image-20201202155434886](C:\Users\6591\AppData\Roaming\Typora\typora-user-images\image-20201202155434886.png) 
  - ![image-20201202154611237](C:\Users\6591\AppData\Roaming\Typora\typora-user-images\image-20201202154611237.png)

- 加入引用設定黨

  - ABP預設路徑為 `.\src\XXX.Core\Configuration`

  - ```C#
    private static IConfigurationRoot BuildConfiguration(string path, string environmentName = null)
    {
        var builder = new ConfigurationBuilder()
            .SetBasePath(path)
            .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true);
    
        return builder.Build();
    }
    ```

    



## IIS 部署

1. 確認資料夾權限

2. 1. [dotnet-hosting]: https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/iis/?view=aspnetcore-3.1#install-the-net-core-hosting-bundle

   2. [SDK、RunTime]: https://dotnet.microsoft.com/download

### PUT And Delete 權限

- **HTTP 错误 405.0 - Method Not Allowed** - 出現此錯誤時請做以下調整
- ![image-20210312153639993](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210312153639993.png)

### Keeping the app to run continuously on IIS

- 由於IIS會自動回收期限內沒有使用的**application pool**。因此長時間的排程(Quatz)會被自動關閉。

  ![image-20200629162856867](C:\Users\6591\AppData\Roaming\Typora\typora-user-images\image-20200629162856867.png)

  

- 因此，必須調整IIS的設定

  1. ###### **Install the Application Initialization Module**

     ![Install IIS application initialization module](https://i0.wp.com/www.taithienbo.com/wp-content/uploads/screenshots/IIS_Application_Initialization/Screen-Shot-2018-10-06-at-3.07.30-PM-1.png?ssl=1)

     

  2. **設定應用程式集區**

     - Idle time-out(閒置愈時): 0代表永遠不會處於閒置狀態，因此不會被回收

     - start mode to “Always Running” tells IIS to start a worker process for your application right away, without waiting for the initial request.

       

     ![img](https://i0.wp.com/www.taithienbo.com/wp-content/uploads/2018/10/IIS_AppPool_Settings_AlwaysRunning.png?ssl=1)

     

  3. **設定IIS網站**

     ![Install IIS application initialization module](https://i2.wp.com/www.taithienbo.com/wp-content/uploads/screenshots/IIS_Application_Initialization/Screen-Shot-2018-10-06-at-4.22.39-PM-2.png?ssl=1)



# 參考資料

1. [Swashbuckle.AspNetCore.Filters](https://github.com/mattfrear/Swashbuckle.AspNetCore.Filters)

2. [ Swagger 編寫文件的技巧和 Client Code Gen](https://dotblogs.com.tw/yc421206/2019/01/23/tips_write_write_web_api_document_via_swagger)

3. [ASP.Net Boilerplate](https://aspnetboilerplate.com/)

4. [ABP (ASP.NET Boilerplate) 應用程式開發框架新手教學](https://dotblogs.com.tw/jakeuj/2016/07/27/abp3)

5. [Volo.ABP](https://docs.abp.io/en/abp/latest/Getting-Started-AspNetCore-MVC-Template)

6. [Multiple DB Setting](https://stackoverflow.com/questions/49243891/how-to-use-multiples-databases-in-abp-core-zero)

7. [在 ASP.NET Core WebAPI 中使用 JWT 驗證](https://poychang.github.io/authenticating-jwt-tokens-in-asp-net-core-webapi/)

8. [JWT JSON Web Token 使用 ASP.NET Core 2.0 Web API 的逐步練習教學與各種情境測試](https://csharpkh.blogspot.com/2018/04/jwt-json-web-token-aspnet-core.html)

9. [如何在 ASP.NET Core 3 使用 Token-based 身分驗證與授權 (JWT)](https://blog.miniasp.com/post/2019/12/16/How-to-use-JWT-token-based-auth-in-aspnet-core-31)

10. [Abp User Entity Design](https://github.com/aspnetboilerplate/aspnetboilerplate/issues/3033 )

11. [C#使用Quartz.NET中Cron表示式](https://www.itread01.com/content/1549747264.html)

12. [How to auto start and keep an ASP.NET core web application running on IIS](https://www.taithienbo.com/how-to-auto-start-and-keep-an-asp-net-core-web-application-and-keep-it-running-on-iis/)

    

