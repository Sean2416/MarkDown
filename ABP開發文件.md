# ABP Framework

## Application Layer

### 	Application Service

- 命名方式: XXX***AppService***

- **Application Service** 主要是由展現層呼叫，並以 **DTO** 作為 `傳入/傳出` 的參數

- **Application Service** 可以包含一些簡單的商業邏輯，並可以直接操作 Entity 物件以達到 展現層與Domain完全切割

- 雖然 **Application Service** 可以直接操作 Entity實體。但為了提高每一個函式的再用性，在開發上還是希望將業務邏輯及資料處理轉至**Domain Layer**實作。

- #### ApplicationService Interface

  - 在ABP中，所有的**Application Service** 都必須繼承 **IApplicationService / ApplicationService** 。

  - `ApplicationService ` 裡面有提供基底的函示可供使用 ex. `Logger`、`Localized`...etc

  - `ApplicationService ` 內部也可以直接使用 `IRepository<T>` 來呼叫基底層的資料庫處理，但實務上還是希望將資料庫處理的相關函式放置在 `Domain Layer`中。

  - ```C#
    public class CustomerAppService : ApplicationService //也可以直接繼承 ~AppServiceBase
    {
        private readonly IRepository<Customer> _personRepository;
    
        public CustomerAppService(IRepository<Customer> personRepository)
        {
            _personRepository = personRepository;
        }
    
        public void CreatePerson(CreateCustomerInput input)
        {
            var customer = new Customer();
    
            ObjectMapper.Map(input, customer);
    
            _personRepository.Insert(customer);
        }
    }
    ```



### 	Data Transfer Objects

- 作為 `Application Layer` 跟 `Presentation Layer` 之間傳遞的參數

- DTO 的功能主要包含

  - 將傳遞參數物件化，讓每一個參數更具意義

  - 可以提高各個參數的重複性及可維護性。(例如，今天同時有多個API會回傳使用者的基本資料欄位時，使用DTO可以讓回傳內容一致且當需要替換回傳欄位時變得更加簡便。)

  - 避免曝光多餘或隱密性的資料給外部使用者 。(例如，今天要取得使用者資訊時，我們可以透過定義DTO來決定回傳的欄位。避免將隱密的資料透漏給外部使用者)

  - Object To Object Mapping

    - 提供 DTO 與 Entity之間的轉換

  - 針對傳入的參數可以做初步的欄位驗證 **(驗證的細節參考 Validating-Data-Transfer-Objects)**

    - ```C#
      [AutoMapTo(typeof(Customer))]
      public class CreateCustomerInput
      {
          /// <summary>
          ///  客戶名字
          /// </summary>
          [Required]
          [StringLength(10, MinimumLength = 5)]
          public string Name { get; set; }
      
          /// <summary>
          ///  客戶電話
          /// </summary>
          public string Phone { get; set; }
      
          /// <summary>
          ///  客戶Mail
          /// </summary>
          public string Email { get; set; }
      
          /// <summary>
          ///  客戶地址
          /// </summary>
          public string Address { get; set; }
      
      }
      ```

      

    - ![image-20201122141457279](C:\Users\sean2\AppData\Roaming\Typora\typora-user-images\image-20201122141457279.png)

  



------





# Appsetting

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

  

# 資料庫處理

1. ## 新增Entity

   1. 於 `~.Core` 專案內建立資料庫實體類別
   2. ABP 內的所有實體都需要繼承 `Entity` 類別 ( **細部內容可參考Domain Layer- Entity說明**)

2. ## 建立資料庫關聯

   1. 於`~.EntityFrameworkCore `得 `~DbContext`中建立資料庫關聯

      - 每一個`~DbContext` 對應到各自的DB連線資訊 ( **細部內容可參考Multi DataBase說明**)

      ![image-20201117104629942](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20201117104629942.png)

3. ## 資料庫還原

   1. 將 `~.EntityFrameworkCore` 指定為啟動專案

   2. 開啟套件管理器主控台輸入下列指令

      1. ```powershell
         add-migration create_user //建立資料庫錨點
         ```

      2. ```powershell
         update-database //更新資料庫
         ```

         ![image-20201117103903556](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20201117103903556.png)

   

4. ## 資料庫CRUD處理

   1.  依據ABP 及 共用開發原則，所有的Entity在進行實際資料庫的處理皆因放置在 `~Store`類別中

   2. ABP中可使用  **IRepository<TEntity>  OR  IRepository<TEntity, TPrimaryKey>** 進行資料庫操作行為 ( **細部內容可參考Domain Layer- Repositories 說明**)

      ```C#
      public class UserStore : ITransientDependency
      {
          public IRepository<User> _Repo { get; }
          
          public UserStore(
              IRepository<User> repo)
          {
              _Repo = repo;
          }
      
          public async Task<List<User>> GetUser()
          {
              return await _Repo.GetAllListAsync();
          }
      }
      ```

   3. `~Store`類別中因只包含單純的資料庫操作，而不包含商業邏輯處理。相關的Domain Logic應該放置在`~Manager`類別中

------



# 建立API

1. 新增 `ApplicationService`

   1. 架構: 依據各API建立資料結構內部需包含

      1. DTO  ( **細部內容可參考 Application Layer- Data Transfer Objects說明**)

      2. Application Service ` ~AppService`   ( **細部內容可參考 Application Layer- Application Service**)

         ![image-20201117114830464](C:\Users\6591\AppData\Roaming\Typora\typora-user-images\image-20201117114830464.png)

2. Application Service內應不包含

   1. Domain Logic

   2. 資料庫實體Entity，需以DTO作為 *request*、 *response* 傳遞類別。並以Object Mapping與Domain Layer進行溝通 ( **細部內容可參考 Application Layer- Object To Object Mapping**)

       

------

# Microsoft.AspNetCore.Identity

## User

1. 定義Identity所需的TUser的基底類別

2. 定義驗證使用者資訊所需之基本屬性，後續透過擴充方式進行調整

   ```C#
   public class BasicUser<TUser> : Entity<long>
   {
       public virtual string Account { get; set; }
       public virtual string Password { get; set; }
           
   }
   ```

3. 後續Identity內有使用User的類別皆需繼承至 `TUser`

   ```C#
    public class BasicLogInManager<TUser> : IDomainService, ITransientDependency
           where TUser : BasicUser<TUser>
   ```



##  UserStore

- 負責實作與資料庫的CRUD。

- 需繼承.Net Identity<TUser>

- 繼承後可實作針對資料處理的相關功能，沒有硬性規定資料處理的方式

  - 如，不一定需要IRepository、或甚至不一定要進入資料庫

- 有資料庫的UserStore

  ```C#
  public class BasicUserStore<TUser> :
      IUserStore<TUser>,
      IUserPasswordStore<TUser>,
      ITransientDependency
      where TUser : BasicUser<TUser> //定義User的類別都必須繼承BasicUser<TUser>
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

  

## UserManager

- 繼承 **Identity** 的 UserManager<TUser>
- 包含使用者驗證、或資料處理的相關方法類別
- 不直接操作資料庫
- 關聯類別: IUserStore<TUser>



## UserClaimsPrincipalFactory

- 繼承UserClaimsPrincipalFactory<TUser>
- 將使用者資訊封裝至Claim中



# IdentityRegistrar

- 將Identity相關的類別注入

- 位置: `~.Web` StartUp中

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





------

# Swagger Ui

## Basic

1. Install the `Swashbuckle.AspNetCore` NuGet package to your **Web** project.

2. 在 `startup.cs` 新增相關Swagger設定，可參考上面Swagger章節

   1. Debug及Release都需要設定

3. 在Application中使用XML 

   1. 調整Application Layer屬性中的輸出

      ![image-20201117105558634](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20201117105558634.png)

4. 將 Swagger 產生器新增至 `Startup.ConfigureServices` 

5. 在 `Startup.Configure` 方法中，啟用中介軟體為產生的 JSON 文件和 Swagger UI 提供服務

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



### Add Token

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



### XML 註解

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



## 加入Auth

### 新增前端登入畫面

1. 引用前端登入畫面及JS登入功能
2. 調整 Configure 插入登入元件

![image-20201117110304028](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20201117110304028.png)