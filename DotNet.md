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

- ###### 信箱驗證功能

  - 實作發送信箱驗證及驗證流程









# 參考文獻

1. [ABP](https://aspnetboilerplate.com/Pages/Documents)