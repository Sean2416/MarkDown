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



# Multi-Tenancy

- ###### ABP 內建提供多組織架構，資料表內 加入`IMayHaveTenant` 、`IMustHaveTenant` 就能在查找資料時自動過濾組織資料。

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



# Dockerize

1. Create DockerFile

   1. 在`sln` 層新增 **Docker File** 及  **.dockerignore**

      ```powershell
      #See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.
      
      FROM mcr.microsoft.com/dotnet/core/aspnet:3.1-buster-slim AS base
      ENV ASPNETCORE_ENVIRONMENT Stage
      WORKDIR /app
      EXPOSE 80
      
      FROM mcr.microsoft.com/dotnet/core/sdk:3.1-buster AS build
      WORKDIR /
      COPY ["src/ABPFramework.Web/ABPFramework.Web.csproj", "src/ABPFramework.Web/"]
      COPY ["src/ABPFramework.EntityFrameworkCore/ABPFramework.EntityFrameworkCore.csproj", "src/ABPFramework.EntityFrameworkCore/"]
      COPY ["src/ABPFramework.Core/ABPFramework.Core.csproj", "src/ABPFramework.Core/"]
      COPY ["src/ABPFramework.Application/ABPFramework.Application.csproj", "src/ABPFramework.Application/"]
      RUN dotnet restore "src/ABPFramework.Web/ABPFramework.Web.csproj"
      COPY . .
      WORKDIR "/src/ABPFramework.Web"
      RUN dotnet build "ABPFramework.Web.csproj" -c Release -o /app/build
      
      FROM build AS publish
      RUN dotnet publish "ABPFramework.Web.csproj" -c Release -o /app/publish
      
      FROM base AS final
      WORKDIR /app
      COPY --from=publish /app/publish .
      ENTRYPOINT ["dotnet", "ABPFramework.Web.dll"]
      ```

2. 建置Docker Image

3. Run Container

   ```powershell
   cd D:\github\ABP_Practice
   docker build -t myproject_image .
   docker run -d -p 8000:80 --name myproject myproject_image
   ```



- 如果Swagger沒有出來，但API可以使用。則，確認 `Application.xml`是否有產出
  - 可以透過發佈專案確認資料夾內是否有檔案
  - ![image-20201220114137012](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20201220114137012.png)
  - ![image-20201220114015693](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20201220114015693.png)



# Jenkins CI/CD

1. 安裝Jenkins (https://jenkins.io/download/)

2. 變更admin預設密碼

   1. 點選使用者, 選擇admin, 點選左側的設定.
   2. 裡面即有密碼欄位供變更.
   3. 完成後儲存即可變更admin密碼.

3. 設定Jenkins URL - 管理Jenkins > 設定系統

   - 調整URL設定

     ![image-20201220102855513](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20201220102855513.png)

4. 安裝套件 - 管理Jenkins > 管理外掛程式

   1. MSBuild

      - 如果需要使用Jenkins建置.Net專案，需要設定MsBuild

      - 安裝完成後至 (管理Jenkins >Global Tool Configuration) 調整設定

   2. Nuget

      - Nuget為.Net常用的套件管理工具, 要在Jenkins使用同樣先需進行環境設定.

      - 下載Nuget.exe (https://www.nuget.org/downloads)

      - 設定環境變數的Path以使用Nuget command

        - 控制台 > 系統 > 進階系統設定 > 環境變數
     - 編輯使用者變數與系統變數, 在Path中加入C:\Nuget\
   
      - 於Jenkins設定Nuget

        - 管理Jenkins > 設定系統
     - 於Nuget區塊設定NuGet command line executable. 例如: C:\Nuget\nuget.exe
        - 儲存
   
   3. PowerShell

   4. Docker

      - 設定Docker.exe 執行位置
- ![image-20201220114238475](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20201220114238475.png)
      
5. GitHub
   
5. 建立新工作

   1. 設定 `Git源碼管理`

      ![image-20201220114404042](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20201220114404042.png)

   2. 設定 `建置觸發程序`

      1.  Jekins 設定 hook URL for Github

         - 此URL 不能使用Localhost，需使用外部可連接的URL。否則無法連接

         ![image-20201220115512996](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20201220115512996.png)

      2. 至Github > Repository > 設定 > Webhooks

         - 設定 **2-1** `Payload URL` 建置新的Weebhook

           ![image-20201220115554172](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20201220115554172.png)

      3. 勾選 `GitHub hook trigger for GITScm polling`

        ![image-20201220115433644](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20201220115433644.png)

        

   3. 設定建置程序
   
      1. 新增 `建置步驟 > PowerShell`
   
      2. 撰寫建置程序
   
         ```powershell
         Write-Host  "1. Start Building For ABP On Docker"
         
         Write-Host  "2. Stop Container And Remove exists Container/Image"
         docker stop myproject
         docker rm myproject
         docker rmi myproject_image
         
         ```
      
      Write-Host  "3. Build Image"
         docker build -t myproject_image .
           
         Write-Host  "4. Start Container"
      docker run -d -p 8000:80 --name myproject myproject_image
         ```
      
         
         ```



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



## Microsoft.AspNetCore.Identity

### TUser

1. 定義使用者資訊所需之基本屬性，後續透過擴充方式進行調整

   ```C#
   public class BasicUser<TUser> : Entity<long>
   {
       public virtual string Account { get; set; }
       public virtual string Password { get; set; }
           
   }
   ```

2. 後續Identity內有使用User的類別皆需繼承至 `TUser`

   ```C#
    public class BasicLogInManager<TUser> : IDomainService, ITransientDependency
           where TUser : BasicUser<TUser>
   ```

###  IUserStore<TUser>

- Identity 所定義的基底類別，主要負責 `User`的CRUD處理

- `UserManager<TUser>` 引用。因此，若需要使用.Net Identity中的使用者管理類別則需建立繼承至IUserStore<TUser>的類別

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


### UserManager<TUser>

- .Net Identity所定義針對使用者管理相關邏輯處理的基底類別

- 包含使用者驗證、或資料處理的相關方法。但，不直接操作資料庫

- 引用類別: IUserStore<TUser>

  ![image-20201221100757090](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20201221100757090.png)

### UserClaimsPrincipalFactory<TUser>

- .Net Identity所定義，針對使用者Claims進行處理的基底類別
- 引用類別: `UserManager<TUser> `

![image-20201221100741165](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20201221100741165.png)

### IdentityRegistrar

- 將Identity相關的類別注入專案中

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

## Login Manager

### Login

- 定義整理驗證流程:從身分驗證一直到回傳Token

- ```C#
  public virtual async Task<AuthResult> Login(TLoginModel loginModel)
  {
      var user = await UserAuth(loginModel);
  
      var tenancyID = await AuthTenant(user.Id, loginModel.TenancyID);          
  
      var loginResult = await GetLoginResultAsync(user);
  
      loginResult.Identity.AddClaim(new Claim(CustomClaimType.TenancyID, tenancyID.ToString()));
  
      var accessToken = CreateAccessToken(CreateJwtClaims(loginResult.Identity));
  
      return new AuthResult
      {
          AccessToken = accessToken,
          ExpireInSeconds = (int)Config.JWTConfig.Expiration.TotalSeconds,
          UserID = loginResult.User.Id
      };
  }
  ```

### UserAuth

- 驗證使用者基本資料。如，帳號、密碼.....

```C#
public virtual async Task<TUser> UserAuth(TLoginModel loginModel)
{

    if (loginModel.Account.IsNullOrEmpty() || loginModel.Password.IsNullOrEmpty())
        throw new UserFriendlyException("Invalid account or password");

    var user = await UserManager.UserAuthenticate(loginModel.Account, loginModel.Password);

    if (user == null)
        throw new UserFriendlyException("Invalid account or password");

    return user;
}
```

### LoginResult

- 針對登入者身分產生回傳內容

  - 使用者

  - 使用者相關資訊 (Claims)

  - ```C#
    public virtual async Task<LoginResult<TUser>> GetLoginResultAsync(TUser user)
    {
        try
        {
            using (var uow = UnitOfWorkManager.Begin())
            {
                await UserManager.InitializeOptionsAsync();
    
                var result = await CreateLoginResultAsync(user);
    
                await uow.CompleteAsync();
    
                return result;
            }
        }
        catch (Exception ex)
        {
            throw new UserFriendlyException(ex.Message);
        }
    }
    
    public virtual async Task<LoginResult<TUser>> CreateLoginResultAsync(TUser user)
    {
        var principal = await _claimsPrincipalFactory.CreateAsync(user);
    
        return new LoginResult<TUser> { 
            User = user,
            Identity = principal.Identity as ClaimsIdentity
        };
    }
    ```

### AccessToken

- ![image-20201221102319737](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20201221102319737.png)



## AuthorizationHelper

- ABP用來進行使用者Token驗證的基底類別

- 會在 **Application Service**的 `AbpAuthorize` 時呼叫驗證

- 內部包含Token的驗證及Permission的驗證

  ![image-20201221142614202](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20201221142614202.png)

- `UserAuthorizationHelper` 衍生至AuthorizationHelper，自行開發改寫內部權限驗證功能

- `IEnumerable<IAbpAuthorizeAttribute>` 會根據 **Application Service** 中 `AbpAuthorize` 所傳入的參數內容進行權限驗證

- ![image-20201221143112437](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20201221143112437.png)

- ![image-20201221142731451](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20201221142731451.png)



## PermissionChecker

- `自行開發`，用來確認使用者是否有所需權限的類別

- 透過驗證使用者 `Token`內所夾帶的角色清單是否包含在權限設定做盼斷

  ![image-20201221143355333](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20201221143355333.png)



------

# Swagger Ui

## Basic

1. Install the `Swashbuckle.AspNetCore` NuGet package to your **Web** project.

2. 在 `startup.cs` 新增相關Swagger設定，可參考上面Swagger章節

   1. Debug及Release都需要設定

3. 在Application中使用XML 

   1. 調整Application Layer屬性中的輸出

   2. 如果再IIS或docker中佈署時，API/首頁可以測通唯獨Swagger無法顯示，則請確認`Release`時有沒有產生XML檔案

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





------

# 參考

## Docker

1. [DockerFile](https://www.jinnsblog.com/2018/12/docker-dockerfile-guide.html)

## CI/CD

1. [Jenkins 新手教學](https://dotblogs.com.tw/seven/2018/10/19/125229)
2. [Deploy. Net core to docker in Jenkins](https://developpaper.com/deploy-net-core-to-docker-in-jenkins/)
3. [Adding a GitHub Webhook in Your Jenkins Pipeline](https://dzone.com/articles/adding-a-github-webhook-in-your-jenkins-pipeline)