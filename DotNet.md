# Net 6













# ABP

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
| AbpTenants                     |                                                              |
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





- 





## User Manager

- ###### Entity - User

  - 代表應用程式中的使用者實體，繼承AbpUser原生屬性。

  - 對應資料表**AbpUsers**  

  - 路徑: ~\aspnet-core\src\ABPFramework.Core\Authorization\Users\User.cs

  - | AccessFailedCount      |                                                              |
    | ---------------------- | ------------------------------------------------------------ |
    | AuthenticationSource   |                                                              |
    | ConcurrencyStamp       | 作為確認資料版本的隨機值。<br />資料更新前確認`ConcurrencyStamp`是否與現行資料庫版本一致，若不相同則代表資料已經被更新過無法直接更新。<br />資料更新後重新賦予新的Value，以確保不會同時間更新資料導致覆蓋情形<br /> |
    | EmailConfirmationCode  | 使用者信箱驗證代碼                                           |
    | IsEmailConfirmed       | Email信箱是否驗證                                            |
    | IsLockoutEnabled       |                                                              |
    | IsPhoneNumberConfirmed |                                                              |
    | IsTwoFactorEnabled     |                                                              |
    | LockoutEndDateUtc      |                                                              |
    | PasswordResetCode      |                                                              |
    | SecurityStamp          |                                                              |
    | TenantId               |                                                              |

    

- ###### Manager

  - [Source Code](https://github.com/aspnetboilerplate/aspnetboilerplate/blob/7647670db2a42b25be864094a42afe03ff118c2d/src/Abp.Zero/Authorization/Users/AbpUserManager.cs)

- ###### 信箱驗證功能

  - 實作發送信箱驗證及驗證流程



## Permission

- ###### MultiTenancySides: 當服務執行為多租戶環境時，Permission的設定可以根據使用者身分定義

  - MultiTenancySides.Host: 僅有Host User擁有此權限
  - MultiTenancySides.Tenant: 僅有Tenant User擁有此權限

- ###### PermissionChecker

  - [Source Code](https://github.com/aspnetboilerplate/aspnetboilerplate/blob/4bfc29aaaab3846962902e0aba86f57a475898f6/src/Abp.ZeroCore/Authorization/PermissionChecker.cs)





# 參考文獻

1. [ABP](https://aspnetboilerplate.com/Pages/Documents)