# Net 6

## Windows  Command Prompt

- 基本指令

  - ```powershell
    移動至指定目錄 >> cd /d 路徑位置
    清除紀錄 >> cls
    列出檔案內容 >> type  檔案名稱
    找出檔案內指定文字>> Find "關鍵字" 檔案名稱
    ```

- 3



## CLI建置專案

### 新增套件

- 移動至`.CSPROJ`檔案並新增下列程式碼

  ```C#
  //新增套件Hangfire.SqlServer 版本1.7.25
  <PackageReference Include="Hangfire.SqlServer" Version="1.7.25" />
  ```

- 



## IIS 部署

1. 確認資料夾權限

2. 安裝環境

   1. dotnet-hosting https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/iis/?view=aspnetcore-3.1#install-the-net-core-hosting-bundle 

   [SDK、RunTime]: https://dotnet.microsoft.com/download



### IIS環境設定

1. ###### .NetCore 裡面可以依據IIS的環境設定，自動取得不同版本的環境設定檔 (appsettings.XXX.json )

2. ######  因此，再設定IIS時，需要先定義這次執行的環境值

   1. 關閉準備設定的IIS站台

   2. ######  開啟IIS站台並點選設定編輯器

      ![image-20210712141520635](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210712141520635.png)

   3. ###### 選擇“system.webServer/aspNetCore”

      ![image-20210712141732394](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210712141732394.png)

   4. ###### 再找到“environmentVariable”项，點擊編輯項目

      ![image-20210712141918893](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210712141918893.png)

   5. ###### 找到或新增 `ASPNETCORE_ENVIRONMENT` 環境變數，並給定變數值 (Production)。

      - ###### 這邊的變數值根據你想要讀取的 `appsettings.XXX.json ` 填入 XXX

      ![image-20210712142130492](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210712142130492.png)

   6. 完成後請點選套用並重啟IIS站台，確認該數值有正確存入

      ![image-20210712142408836](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210712142408836.png)



### PUT And Delete 權限

- **HTTP 错误 405.0 - Method Not Allowed** - 出現此錯誤時請做以下調整
- ![image-20210312153639993](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210312153639993.png)

### 確保排程持續啟動

- 由於IIS會自動回收期限內沒有使用的**application pool**。因此長時間的排程(Quatz)會被自動關閉。

  ![image-20200629162856867](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20200629162856867.png)

  

- 因此，必須調整IIS的設定

  1. ###### **Install the Application Initialization Module**

     ![Install IIS application initialization module](https://i0.wp.com/www.taithienbo.com/wp-content/uploads/screenshots/IIS_Application_Initialization/Screen-Shot-2018-10-06-at-3.07.30-PM-1.png?ssl=1)

     

  2. **設定應用程式集區**

     - Idle time-out(閒置愈時): 0代表永遠不會處於閒置狀態，因此不會被回收

     - start mode to “Always Running” tells IIS to start a worker process for your application right away, without waiting for the initial request.

       

     ![img](https://i0.wp.com/www.taithienbo.com/wp-content/uploads/2018/10/IIS_AppPool_Settings_AlwaysRunning.png?ssl=1)

     

  3. **設定IIS網站**

     ![Install IIS application initialization module](https://i2.wp.com/www.taithienbo.com/wp-content/uploads/screenshots/IIS_Application_Initialization/Screen-Shot-2018-10-06-at-4.22.39-PM-2.png?ssl=1)







# ABP



## Hangfire

1. CLI安裝

   - ```powershell
     Install-Package Abp.HangFire.AspNetCore -version 6.6.2
     Install-Package Hangfire.AspNetCore(單純使用原生的好像也沒問題?)
     Install-Package Hangfire.SqlServer -version 1.7.25
     ```
     
   - 第二個套件為指定hangfire紀錄存取位置(這邊指定SqlServer)

2. 在 startup 中註冊 handfire

   - 指定Hangfire連線資料庫,系統在執行時會建立相關資料表

     - ```C#
       services.AddHangfire(config =>
       {    		config.UseSqlServerStorage(_appConfiguration.GetConnectionString("Default"));
       }); 
       
       ```
     
   - ![img](https://raw.githubusercontent.com/Sean2416/Pic/master/img/202202121118598.png)
   
3.  在 configure 方法中加入

   - 將`UseHangfireServer` 提前至 ` app.UseAbp`前，就能在其他`Module`中註冊Hangfire排程。

   - ```C#
     public void Configure(IApplicationBuilder app, IWebHostEnvironment env, ILoggerFactory loggerFactory)
     {
       app.UseHangfireServer();
       app.UseHangfireDashboard();
     
       app.UseAbp(options => { options.UseAbpRequestLocalization = false; }); // Initializes ABP framework.
     }
     ```

     

   - 如果沒有在`UseAbp`前註冊的話，則會跳出下列錯誤

     - ![img](https://raw.githubusercontent.com/Sean2416/Pic/master/img/202202121306109.png)

4. 最後，將 abp 預設的 background job manager 替換成 hangfire，這邊就根據你是用什麼專案模板，我是用前後端分離的，所以要替換的檔案就在 [ProjectName].Web.Core 專案中的 [ProjectName]WebCoreModule.cs 這隻檔案中

### Storage

1. *永久儲存（Hangfire將後臺作業和其他與處理有關的資訊保留在永久性儲存器中，所以需要儲存庫來儲存如：MS SQL Server，Redis，MySQL，PostgreSql等）*

2. MySQL

   ```C#
   Install-Package Hangfire.MySqlStorage //下載MySql 套件
   ```

   ```C#
    public void ConfigureServices(IServiceCollection services)
    {    
       var connectionString = configuration["ConnectionStrings:Hangfire"];
       config.UseStorage(new MySqlStorage(connectionString, 
       new MySqlStorageOptions
       {
           QueuePollInterval = TimeSpan.FromSeconds(15), //- 作業佇列輪詢間隔。預設值為15秒。
           JobExpirationCheckInterval = TimeSpan.FromHours(1), //過期作業檢查間隔,預設一小時
           CountersAggregateInterval = TimeSpan.FromMinutes(5), //聚合計數器間隔,預設一小時
           PrepareSchemaIfNecessary = true, //是否自動建置Hangire預設資料表,若為False且資料庫內沒有資料表。系統跳出錯誤
           DashboardJobListLimit = 50000, //儀表板中作業限制,預設50000
           TransactionTimeout = TimeSpan.FromMinutes(1), //資料庫交易超時,預設一分鐘
           TablesPrefix = "Hangfire" //資料庫前墜字樣                       
       }));     
    }
   ```

3. SQL Server

   ```C#
   Install-Package Hangfire.SqlServer -version 1.7.25//下載MySql 套件
   ```

   ```C#
    public void ConfigureServices(IServiceCollection services)
    {
       var connectionString = configuration["ConnectionStrings:Hangfire"];
       config.UseSqlServerStorage(connectionString, new SqlServerStorageOptions
       {
           CommandBatchMaxTimeout = TimeSpan.FromMinutes(5),
           SlidingInvisibilityTimeout = TimeSpan.FromMinutes(5),
           QueuePollInterval = TimeSpan.Zero,
           UseRecommendedIsolationLevel = true,
           DisableGlobalLocks = true // Migration to Schema 7 is required
       });
    }
   ```

4. Redis : 待補充

   1. [Abp中使用Redis](https://blog.csdn.net/qq_39569480/article/details/107961516)


### Queue

- ###### Hangfire可以定義多個佇列，讓開發人員可以針對不同排程設定至不同柱列中

- 設定方式如下:

  1. 在Configure中預先定義佇列名稱，沒有定義的佇列將不會被執行

     ```C#
     public void Configure(IApplicationBuilder app, IWebHostEnvironment env, ILoggerFactory loggerFactory)
      {
          var jobServerOptions = new BackgroundJobServerOptions()
     	{
             Queues = new[] { "alpha", "beta", "default" }, //佇列名稱，可以多筆，預設 default
         };
     
     	app.UseHangfireServer(jobServerOptions);     
      }
     ```

  2. ###### 註冊排程時指定佇列名稱`queue參數`，可參考RecurringJobManager定義

     ```C#
     public static void AddOrUpdate<T>([NotNull] this IRecurringJobManager manager, [NotNull] string recurringJobId, [NotNull] Expression<Func<T, Task>> methodCall, [NotNull] string cronExpression, TimeZoneInfo timeZone = null, string queue = "default")
     ```

### 參數設定

- ###### BackgroundJobServerOptions: 配置 Hangfire 服務的設定

- 設定方式如下

  - ```C#
     public void Configure(IApplicationBuilder app, IWebHostEnvironment env, ILoggerFactory loggerFactory)
     {
         var jobServerOptions = new BackgroundJobServerOptions()
      	    {
                ServerName = "TestServer", //預設使用電腦名稱:ProcessID，例如：server1:9853，server1:4531，server2:6742
                WorkerCount = 20, //執行緒使用數量，預設 20 
                Queues = new[] { "alpha", "beta", "default" }, //佇列名稱，可以多筆，預設 default，如果你的任務想要用別的名稱，一開始就要宣告
                CancellationCheckInterval = TimeSpan.FromSeconds(5), //任務取消檢查週期，預設 00:00:05 (5秒)
                ServerTimeout = TimeSpan.FromMinutes(5),//服務逾時，預設 00:05:00 (5 分鐘)
                ServerCheckInterval = TimeSpan.FromMinutes(5), //服務檢查週期，預設 00:05:00 (5 分鐘)
                SchedulePollingInterval = TimeSpan.FromSeconds(15), //執行排程任務的輪詢週期，預設 00:00:15 (15秒)，每 15 秒執行一次任務
                ShutdownTimeout = TimeSpan.FromSeconds(15), //關閉逾時，預設 00:00:15 (15秒)
                StopTimeout = TimeSpan.FromSeconds(15), //停止逾時，預設 00:00:00
            };
      
      		app.UseHangfireServer(jobServerOptions);     
     }
    ```

### Cron Express

- ###### 字段含意

  |                      | 可用值                                                       |
  | -------------------- | ------------------------------------------------------------ |
  | 秒                   | **0****－59** ，’ **-** ’ , ’ * ’ , ’ **/** ’                |
  | 分                   | **0****－59** ，’ **-** ’ , ’ * ’ , ’ **/** ’                |
  | 時                   | **0 - 23**，’ **-** ’ , ’ * ’ , ’ **/** ’                    |
  | **Day-of-Month**(天) | **1 - 31** ，’ **-** ’ , ’ * ’ , ’ **?** ’ , ’ **/** ’ , ’ **L** ’ , ’ **W** ’ , ’ **C** ’ |
  | 月                   | 可以用 **0  - 11** ，或用字符串  “**JAN, FEB, MAR, APR, MAY, JUN, JUL, AUG, SEP,  OCT, NOV , DEC**” ，或 ’ **-** ’ , ’ * ’ ,  ’ **/** ’ |
  | **Day-of-Week**(周)  | **1 - 7** 表示（1 ＝ 星期日），或用字符口串“**SUN, MON, TUE, WED, THU, FRI , SAT**”，或 ’ **-** ’ , ’ * ’ , ’ **?** ’ , ’ **/** ’ , ’ **L** ’ , ’ **C** ’ , ’ **#** ’ |
  | 年                   | **1970 - 2099** ，’ **-** ’ , ’ * ’ , ’ **/** ’              |

- 字符含意

  | 符號   | 意思                                                         | 限制                              | 範例                                                         |
  | ------ | ------------------------------------------------------------ | --------------------------------- | ------------------------------------------------------------ |
  | *      | 表示所有值                                                   |                                   | 如在Minutes使用, 表示每分钟都会觸發事件                      |
  | ?      | 表示未說明的值，代表不須特別定義                             | 只能用在DayofMonth和DayofWeek域   | 例如想在每月的20日啟動排程，不管20日到底是星期幾，13 13 15 20 * ? |
  | **-**  | 表示一個指定範圍                                             |                                   | 如在Minutes使用5-20，表示从5分到20分每分鐘觸發一次           |
  | **，** | 表示列舉值                                                   |                                   | 在Minutes使用5,20，代表在5分和20分各觸發一次                 |
  | /      | 符號前表示開始時間 /  符號後表示週期                         |                                   | 如“0/15”表示每隔15分鐘執行一次,“0”表示從“0”分開始,  “3/20”表示表示每隔20分鐘執行一次，“3”表示從第3分鐘开始 |
  | **#**  | 表示這個月的第幾周的禮拜幾開始執行。  符號前表示禮拜幾,  符號後表示第幾周 | 只能用在day-of-week               | "6#3"指這個月第3三周的周五（6指周五，3指第3个）。如果指定的日期不存在則不會執行 |
  | L      | 代表最後                                                     | 只能出现在DayofWeek和DayofMonth域 | 用在day-of-month意思是 “這個月最後一天”；  用在 day-of-week, 意思是這周最後一天代表禮拜六。  如果在day-of-week字段和數字一起使用，代表這個月的最后一個星期幾” – 例如： “6L” 意味"本月的最後一个星期五" |
  | W      | 代表最接近指定日的工作日(周一到週五)，系統會在離指定日期最近的工作日啟動 | 只能出现在DayofMonth              | 在day-of-month字段用“15W”指“最接近這個月第15天的工作日”，  如果這個月第15天是周六，那么就會在這個月第14天即周五執行；如果月第15天是周日，那就會在這個月第 16天即周一触发；如果第15天是周二，那就在當天執行 |
  | LW     | 表示在某月最後一個工作日                                     |                                   |                                                              |

  

- 範例

  | */5 * * * * ?            | 每隔5秒執行一次                                              |
  | ------------------------ | ------------------------------------------------------------ |
  | 0 0 5-15 * * ?           | 每天5-15點整點執行                                           |
  | 0 0/5 14,18 * * ?        | 每天下午兩點開始每五分鐘執行一次到55分和下午六點開始每五分鐘執行一次到55分 |
  | 0 0 12 ? * WED           | 每個星期三12點執行                                           |
  | 0 15 10 ? * 6L 2002-2005 | 2002年至2005年的每月的最後一个星期五上午10:15觸發            |
  | 30 10 1 20 10 ? *        | 每年10月20號1點10分30秒                                      |
  | 0 15 10 ? * MON-FRI      | 星期一到星期五的10點15分                                     |
  | 0 15 10 LW * ?           | 每個月最後一個工作日10點15分觸發任务                         |

### 參考

- [.NET之Hangfire快速入門和使用](https://www.itread01.com/content/1569808862.html)
- [Abp 结成HangFire](https://www.cnblogs.com/tianxujun/p/15720646.html)
- [Hangfire Cron Expression](https://blog.csdn.net/long870294701/article/details/87983142)
- 





## 系統參數

### AppSetting

- Server端的系統參數設定，當整個Server端設定值固定時可透過`AppSetting`定義

- `AppSetting`可以跟據執行環境，定義不同的設定值對應內容 (ex,開發區/驗證區/正式環境資料庫連線資料)。

  - 新增環境設定檔僅需要建立新的`appsettings.XXX.json`文件

    - ENV = >Staging: `appsettings.Staging.json`

  - Docker可在`DockerFile`內指定發布Image時的環境設定

    - ```
      FROM mcr.microsoft.com/dotnet/aspnet:5.0-buster-slim AS base
      ENV ASPNETCORE_ENVIRONMENT Stage
      WORKDIR /app
      EXPOSE 80
      ....
      ```

  - IIS設定環境變數如下

    - 開啟IIS站台並點選設定編輯器

      ![image-20210712141520635](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210712141520635.png)

    - ###### 選擇“system.webServer/aspNetCore”

      ![image-20210712141732394](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210712141732394.png)

    - ###### 再找到“environmentVariable”项，點擊編輯項目

      ![image-20210712141918893](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210712141918893.png)

    - ###### 找到或新增 `ASPNETCORE_ENVIRONMENT` 環境變數，並給定變數值 (Production)。

      - ###### 這邊的變數值根據你想要讀取的 `appsettings.XXX.json ` 填入 XXX

      ![image-20210712142130492](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210712142130492.png)

    - 完成後請點選套用並重啟IIS站台，確認該數值有正確存入

      ![image-20210712142408836](https://raw.githubusercontent.com/Sean2416/Pic/master/img/image-20210712142408836.png)

- 取得AppSetting內的資料

  - 在建構子中載入 `HostingEnvironment`，並取得 `IConfigurationRoot`

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

  - 取得單一設定值

    ```C#
    public override void PreInitialize()
    {
       var setting = _appConfiguration["App:MultiTenancy"];
    }
    
    ```

  - 取得`Appsetting`中的對應物件並Bind至Config 物件

    ```C#
    public override void PreInitialize()
    {
         //透過DI註冊MailServerConfig物件
        IocManager.Register<MailServerConfig>();   
    }
    
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


- ###### Appsetting讀取步驟

  1. 讀取`appsettings.json`作為預設值
  2. 根據執行環境讀取`appsettings.XXX.json`檔案，碰到相同的設定參數則蓋掉預設值

- ###### 新增系統參數步驟如下:

  1. 至`appsettings.json`新增設定值

  2. 若該設定檔會根據執行環境不同而變更的話，至`appsettings.XXX.json`根據各別環境調整設定值

  3. 在需要使用的程式檔內，透過`IConfigurationRoot`取得設定值(可參考~\ABPFramework.Core\ABPFrameworkCoreModule.cs)

  4. 藉由`ConfigurationExtension`取得對應參數值,並將其轉換為對應型態

     ```C#
     Configuration.MultiTenancy.IsEnabled = AppConfiguration.GetConfigurationValue<bool>("App:MultiTenancy");
     ```

     

### Setting Management

- ###### 儲存在**AbpSettings**以Dictionary形式儲存的系統設定值

- ###### 與`appsetting`、`Consts`不同點在於，Setting可以針對 使用者/Tenant各別進行設定

- ###### SettingProvider

  - ###### 定義可用的系統設定資料

  - ###### 所有的系統設定值必須要先透過SettingProvider進行註冊才能在程式中使用,否則即使資料庫內有值也會出現錯誤

    - **Name** : 設定值名稱，必須為唯一直(代表`dictionary`內的key值)

    - **Default value**: 預設值，當系統在`abpSettings`資料庫內沒有找到相對應的資料時所賦予的初始值

    - **Scopes**: 代表該設定所對應的層級，系統在搜尋設定值時會根據 User > Tenant > Application依序往下尋找

    - ```C#
      public class MySettingProvider : SettingProvider
      {
          public override IEnumerable<SettingDefinition> GetSettingDefinitions(SettingDefinitionProviderContext context)
          {
              return new[]
                      {
                          new SettingDefinition(
                              "SmtpServerAddress", // Name 
                              "127.0.0.1" // Default value
                              ),
      
                          new SettingDefinition(
                              "PassiveUsersCanNotLogin",
                              "true",
                              scopes: SettingScopes.Application | SettingScopes.Tenant //Scopes
                              ),
      
                          new SettingDefinition(
                              "SiteColorPreference",
                              "red",
                              scopes: SettingScopes.User,
                              clientVisibilityProvider: new VisibleSettingClientVisibilityProvider()
                              )
      
                      };
          }
      }
      ```

  - ###### ABP預設系統參數

    - [LocalizationSettingProvider](https://github.com/aspnetboilerplate/aspnetboilerplate/blob/059db7626b3642114b7a2ba7d15b6a14304640dd/src/Abp/Localization/LocalizationSettingProvider.cs)
    - [TimingSettingProvider](https://github.com/aspnetboilerplate/aspnetboilerplate/blob/059db7626b3642114b7a2ba7d15b6a14304640dd/src/Abp/Timing/TimingSettingProvider.cs)
    - [EmailSettingProvider](https://github.com/aspnetboilerplate/aspnetboilerplate/blob/059db7626b3642114b7a2ba7d15b6a14304640dd/src/Abp/Net/Mail/EmailSettingProvider.cs)
    - [NotificationSettingProvider](https://github.com/aspnetboilerplate/aspnetboilerplate/blob/059db7626b3642114b7a2ba7d15b6a14304640dd/src/Abp/Notifications/NotificationSettingProvider.cs)
    - [AbpZeroSettingProvider](https://github.com/aspnetboilerplate/aspnetboilerplate/blob/059db7626b3642114b7a2ba7d15b6a14304640dd/src/Abp.Zero.Common/Zero/Configuration/AbpZeroSettingProvider.cs)

  - ###### 新增自定義系統參數

    - 在`module`的PreIntialize 進行註冊

    - ```C#
      Configuration.Settings.Providers.Add<MySettingProvider>();
      ```

- ISettingDefinitionManager

  - ###### 取得並管理所有繼承`SettingProvider`之系統定義參數內容。

  - ###### 簡言之，透過`ISettingDefinitionManager`來取得目前系統所有的參數設定

  - ###### [Source Code](https://github.com/aspnetboilerplate/aspnetboilerplate/blob/059db7626b3642114b7a2ba7d15b6a14304640dd/src/Abp/Configuration/SettingDefinitionManager.cs)

- ###### ISettingManager

  - ###### 管理系統設定存取的相關邏輯

  - ###### 系統要讀取設定值步驟

    1. 透過`ISettingDefinitionManager` 取得所有定義過的參數資料，並判斷要取得的設定名稱是否存在。(不存在則報錯)
    2. 根據取得的設定資料所定義的層級，依序從 User > Tenant > Application至資料庫取得對應值。(若有取得則回傳資料內容)
    3. 若在前一個步驟中都無法取得相關的設定內容，則回傳步驟一所取得的參數預設值。

  - [Source Code](https://github.com/aspnetboilerplate/aspnetboilerplate/blob/dev/src/Abp/Configuration/SettingManager.cs)

- ###### ISettingStore

  - 至`AbpSettings`存取設定值資料
  - [Source Code](https://github.com/aspnetboilerplate/aspnetboilerplate/blob/4bfc29aaaab3846962902e0aba86f57a475898f6/src/Abp.Zero.Common/Configuration/SettingStore.cs)

- ###### 新增系統參數步驟

  1. 至`~\ABPFramework.Core\Configuration\AppSettingNames.cs`定義參數名稱及對應代碼

  2. 至`~\ABPFramework.Core\Configuration\AppSettingProvider.cs`新增系統參數

  3. 根據設定值層級至`AbpSettings`建立對應資料(`AbpSettings.Name = 步驟1之代碼值`)

  4. 取得設定值內容

     - ```
       Configuration.Settings.Providers.Add<MySettingProvider>();
       ```





## Tenant Management

- ###### Tenant(租戶):  代表在單一系統中共用資料、權限的群體(ex,各別組織/公司)

- ###### **Multitenancy** 的設計架構，指在單一實例的服務上提供多租戶使用的軟體設計概念。每一個群體共享組織內的資料、權限、參數設定....等，但群體間分別獨立。

- ###### Multitenancy的架構包含多種設計方式,ABP提供單一實體-單一資料庫的設計架構。並透過`Configuration`的方式決定整個應用程式是否支援`multi-Tenant`

  - ```C#
    Configuration.MultiTenancy.IsEnabled = true; 
    ```

### Host vs Tenant

- ###### Tenant: 

  - ###### 代表每一群在使用服務的組織，每個組織擁有各自的使用者、腳色、權限...等。並且每一個群體的資料都是分別獨立於其他群體。

  - ###### Tenant admin: 代表群體的管理人員，擁有群體內的最高權限。可以管理各別群體的帳號、權限....等群組資料，但無法管理或查閱其他群體的資料內容。也無法新增新的群體。

- ###### Host: 

  - ###### 獨立於群體之外的腳色，可以編輯群體的資料。

  - ###### 相當於系統管理者。

- ###### 判別使用者身分

  - ###### ABP主要透過使用者登入後，Session內儲存的資料來判定使用者在服務中的身分

    -  UserId is not null /  TenantId is null => Host
    -  UserId is not null /  TenantId is not null => Tenant

### Data Filters

- ###### 在MultiTenancy裝況下，使用者登入時都會指定登入的TenantId。因此，在登入後透過`AbpSession.TenantId`可以取得使用者的TenantId

- ###### 當使用者在進行資料存取時，只要包含`TenantId`欄位的資料表都會自動依據`AbpSession.TenantId`進行過濾以取得所在群組的資料。(藉由TenantId的過濾將每個群體的資料獨立，以達到MultiTenancy的結果)

- 資料庫註記

  - ###### ABP提供衍生類別讓開發人員可以更快速的決定資料庫是否要包含Tenant的判斷

  - ###### IMustHaveTenant: 

    - 繼承此類別的Entity，會在資料表欄位中增加`TenantId`並且為**必填欄位**
    - 資料在新增時，系統會自動更具使用者Session內的`TenantId`欄位進行賦值。
    - 搜尋資料表時，系統會自動將TenantId納入查詢條件

  - ###### IMayHaveTenant: 

    - 繼承此類別的Entity，會在資料表欄位中增加`TenantId`為**非必填欄位**

    - ###### 使用時機會，該資料表同時會被Teanat User / Host User操作時使用

    - 如`AbpRoles`，Host User之間會有權限的區分而Tenant內部也會有權限的分別。因此，當`TenantId=null`時代表這是屬於Host的資料。

### Tenant API

- ###### ABP預設提供Teanat維護之API: ~\ABPFramework.Application\MultiTenancy\TenantAppService.cs

- ![img](https://raw.githubusercontent.com/Sean2416/Pic/master/img/202202061153735.png)

- ###### 預設權限擁有`Pages.Tenants`之**Host User**能進行維護

### AbpTenantManager

- [Source Code](https://github.com/aspnetboilerplate/aspnetboilerplate/blob/4bfc29aaaab3846962902e0aba86f57a475898f6/src/Abp.Zero.Common/MultiTenancy/AbpTenantManager.cs)

### 備註

- ###### ABP預設是啟用MultiTenancy,如要關閉請調整`Module`內的`Configuration.MultiTenancy.IsEnabled`

- ###### 若應用程式沒有啟用MultiTenancy，則再登入者的`AbpSession.TenantId`中會使用預設值1帶入。

  - 因此，在`Configuration.MultiTenancy.IsEnabled = false`的情況下所有的資料都只會搜尋`TenantId=1`的內容。新增的資料也會自動將1帶入`TenantId`中
  - 簡言之在`Configuration.MultiTenancy.IsEnabled = false`時，所有的User都是 `TenantId=1`的群體成員



## User Management

- ###### **AbpUsers** 

  - 代表應用程式中的使用者實體，繼承AbpUser原生屬性。

  - 路徑: ~\aspnet-core\src\ABPFramework.Core\Authorization\Users\User.cs

  - | AccessFailedCount      | 登入錯誤次數，預設超過五次後重新計算並更新鎖定時間           |
    | ---------------------- | ------------------------------------------------------------ |
    | AuthenticationSource   |                                                              |
    | ConcurrencyStamp       | 作為確認資料版本的隨機值。<br />資料更新前確認`ConcurrencyStamp`是否與現行資料庫版本一致，若不相同則代表資料已經被更新過無法直接更新。<br />資料更新後重新賦予新的Value，以確保不會同時間更新資料導致覆蓋情形<br /> |
    | EmailConfirmationCode  | 信箱驗證代碼，供使用者進行信箱驗證                           |
    | IsEmailConfirmed       | 使用者信箱是否進行過驗證                                     |
    | IsLockoutEnabled       | 是否啟用使用者鎖定功能，預設為true<br />搭配LockoutEndDateUtc一起使用 |
    | IsPhoneNumberConfirmed |                                                              |
    | IsTwoFactorEnabled     |                                                              |
    | LockoutEndDateUtc      | 使用者鎖定後解鎖時間,預設登入超過五次後會進行鎖定<br />只有在IsLockoutEnabled開啟時才會在登入時判斷此欄位 |
    | PasswordResetCode      |                                                              |
    | SecurityStamp          |                                                              |
    | TenantId               |                                                              |

- ###### Manager

  - [User Manager](https://github.com/aspnetboilerplate/aspnetboilerplate/blob/7647670db2a42b25be864094a42afe03ff118c2d/src/Abp.Zero/Authorization/Users/AbpUserManager.cs)
  - [Login Manager](https://github.com/aspnetboilerplate/aspnetboilerplate/blob/4bfc29aaaab3846962902e0aba86f57a475898f6/src/Abp.Zero/Authorization/AbpLoginManager.cs)

- ###### 信箱驗證

  - 當AbpSetting中`Abp.Zero.UserManagement.IsEmailConfirmationRequiredForLogin = true`時，系統會自動針對登入者的`IsEmailConfirmed`進行檢視。若不為`true`則會跳出信箱尚未驗證錯誤，使用者將無法登入。
  - 實作信箱驗證功能如下:
    - 2

- ###### 手機驗證

- ###### 登入次數相關

  - ###### 在使用者登入時，`LoginManager`會根據登入情形呼叫`UserManager`處理錯誤次數、鎖定時間等相關邏輯。

  - ###### 登入鎖定相關設定皆可透過`AbpSetting`進行變更

    - ```C#
       public static class UserLockOut
       {
           //
           // 摘要:
           //    是否啟用使用者鎖定.
           public const string IsEnabled = "Abp.Zero.UserManagement.UserLockOut.IsEnabled";
           //
           // 摘要:
           //   登入錯誤最大次數
           public const string MaxFailedAccessAttemptsBeforeLockout = "Abp.Zero.UserManagement.UserLockOut.MaxFailedAccessAttemptsBeforeLockout";
           //
           // 摘要:
           //     鎖定時間
           public const string DefaultAccountLockoutSeconds = "Abp.Zero.UserManagement.UserLockOut.DefaultAccountLockoutSeconds";
                  }
      ```

  - [修正ABP回傳登入錯誤次數、鎖定時間](https://www.cnblogs.com/6112562a/p/14042055.html)







## 原生資料表

| Table Name                     | 功能說明                                                     |
| ------------------------------ | :----------------------------------------------------------- |
| AbpAuditLogs                   | 紀錄API Request時所接受到的參數內容、Client Information以及最後執行結果 |
| AbpBackgroundJobs              |                                                              |
| AbpDynamicEntityProperties     |                                                              |
| AbpDynamicEntityPropertyValues |                                                              |
| AbpDynamicProperties           |                                                              |
| AbpDynamicPropertyValues       |                                                              |
| AbpEditions                    |                                                              |
| AbpEntityChanges               |                                                              |
| AbpEntityChangeSets            |                                                              |
| AbpEntityPropertyChanges       |                                                              |
| AbpFeatures                    |                                                              |
| AbpLanguages                   |                                                              |
| AbpLanguageTexts               |                                                              |
| AbpNotifications               |                                                              |
| AbpNotificationSubscriptions   |                                                              |
| AbpOrganizationUnitRoles       |                                                              |
| AbpOrganizationUnits           |                                                              |
| AbpPermissions                 |                                                              |
| AbpRoleClaims                  |                                                              |
| AbpRoles                       |                                                              |
| AbpSettings                    | 參數設定檔，請參考 [ABPSetting](#Setting Management)         |
| AbpTenantNotifications         |                                                              |
| AbpTenants                     | 使用MiltiTenancy時，定義Tenant基本資料 [ABPTenant](#Tenant Management) |
| AbpUserAccounts                |                                                              |
| AbpUserClaims                  |                                                              |
| AbpUserLoginAttempts           |                                                              |
| AbpUserLogins                  |                                                              |
| AbpUserNotifications           |                                                              |
| AbpUserOrganizationUnits       |                                                              |
| AbpUserRoles                   |                                                              |
| AbpUsers                       |                                                              |
| AbpUserTokens                  |                                                              |
| AbpWebhookEvents               |                                                              |
| AbpWebhookSendAttempts         |                                                              |
| AbpWebhookSubscriptions        |                                                              |





## Session





## Permission

- ###### MultiTenancySides: 當服務執行為多租戶環境時，Permission的設定可以根據使用者身分定義

  - MultiTenancySides.Host: 僅有Host User擁有此權限
  - MultiTenancySides.Tenant: 僅有Tenant User擁有此權限

- ###### PermissionChecker

  - [Source Code](https://github.com/aspnetboilerplate/aspnetboilerplate/blob/4bfc29aaaab3846962902e0aba86f57a475898f6/src/Abp.ZeroCore/Authorization/PermissionChecker.cs)





# 參考文獻

1. [ABP](https://aspnetboilerplate.com/Pages/Documents)