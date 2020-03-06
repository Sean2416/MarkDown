# IdentityServer4

## 執行流程

1.  前端透過 ClientID 及 Secret 向 AuthServer 取得 Scope
2.  前端拿著Token打向 API ，API 拿著TOKEN 判斷 是否符合SCOPE



## Quick Start

### 1. Setting up the ASP.NET Core application

- First create a directory for the application - then use our template to create an ASP.NET Core application that includes a basic IdentityServer setup

  ```powershell
  dotnet new -i IdentityServer4.Templates
  
  md quickstart
  cd quickstart
  
  md src
  cd src
  
  dotnet new is4empty -n IdentityServer
  ```

### 2.  Create .sln

```powershell
cd ..
//建立sln方案
dotnet new sln -n Quickstart

//將專案載入方案中
dotnet sln add .\src\IdentityServer\IdentityServer.csproj
```

```
Note

The protocol used in this Template is http and the port is set to 5000 when running on Kestrel or a random one on IISExpress. You can change that in the Properties\launchSettings.json file.
However, all of the quickstart instructions will assume you use the default port on Kestrel as well as the http protocol, which is sufficient for local development. You should not run identity server http in a production enviroment http should only be used for testing.
```

### 3. Defining an API Resource

- First create a directory for the application - then use our template to create an ASP.NET Core application that includes a basic IdentityServer setupI Resource

- An API is a resource in your system that you want to protect
-  Resource definitions can be loaded in many ways, the template you used to create the project above shows how to use a “code as configuration” approach
- Find the [Config.cs](https://github.com/IdentityServer/IdentityServer4/blob/master/samples/Quickstarts/1_ClientCredentials/src/IdentityServer/Config.cs) file you can find a method called `GetApis`, define the API as follows:

```c#
public static IEnumerable<ApiResource> GetApis()
{
    return new List<ApiResource>
    {
        new ApiResource("api1", "My API")
    };
}
```

### 4. Defining the client

-  Define a client application that we will use to access our new API.
- For this scenario, the client will not have an interactive user, and will authenticate using the so called client secret with IdentityServer.
- You can think of the ClientId and the ClientSecret as the login and password for your application itself. It identifies your application to the identity server so that it knows which application is trying to connect to it.

```C#
public static IEnumerable<Client> GetClients()
{
    return new List<Client>
    {
        new Client
        {
            ClientId = "client",

            // no interactive user, use the clientid/secret for authentication
            AllowedGrantTypes = GrantTypes.ClientCredentials,

            // secret for authentication
            ClientSecrets =
            {
                new Secret("secret".Sha256())
            },

            // scopes that client has access to
            AllowedScopes = { "api1" }
        }
    };
}
```

### 5. Configuring IdentityServer

- Loading the resource and client definitions happens in Startup.cs
- At first startup, IdentityServer will create a developer signing key for you, it’s a file called `tempkey.rsa`. You don’t have to check that file into your source control, it will be re-created if it is not present.

```C#
public void ConfigureServices(IServiceCollection services)
{
    var builder = services.AddIdentityServer()
        .AddInMemoryIdentityResources(Config.GetIdentityResources())
        .AddInMemoryApiResources(Config.GetApis())
        .AddInMemoryClients(Config.GetClients());

    // omitted for brvity
}
```

### 6. 新增API 並加入認證流程

```C#
 	[Route("api/[controller]")]
    [ApiController]
    [Authorize]
    public class ValuesController : ControllerBase
    {
        // GET api/values
        [HttpGet]
        public ActionResult<IEnumerable<string>> Get()
        {
            return new string[] { "value1", "value2" };
        }
	}
```

- Configure the API application to run on `http://localhost:5001`only
-  You can do this by editing the [launchSettings.json](https://github.com/IdentityServer/IdentityServer4/blob/master/samples/Quickstarts/1_ClientCredentials/src/IdentityServer/Properties/launchSettings.json) file inside the Properties folder

### 7. Configuration

- The last step is to add the authentication services to DI (dependency injection) and the authentication middleware to the pipeline.
  - validate the incoming token to make sure it is coming from a trusted issuer
  - validate that the token is valid to be used with this api (aka audience)
- `AddAuthentication` adds the authentication services to DI and configures `"Bearer"` as the default scheme. 
- `UseAuthentication` adds the authentication middleware to the pipeline so authentication will be performed automatically on every call into the host.

```C#
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvcCore()
            .AddAuthorization()
            .AddJsonFormatters();

        services.AddAuthentication("Bearer")
            .AddJwtBearer("Bearer", options =>
            {
                options.Authority = "http://localhost:5000";
                options.RequireHttpsMetadata = false;

                options.Audience = "api1";
            });
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseAuthentication();

        app.UseMvc();
    }
}
```

### 8. 測試

- 建立Console Application
- 取得Token並打向 API

```C#
using IdentityModel.Client;
using Newtonsoft.Json.Linq;
using System;
using System.Net.Http;
using System.Threading.Tasks;

namespace Client
{
    public class Program
    {
        private static async Task Main()
        {
            // discover endpoints from metadata
            var client = new HttpClient();

            var disco = await client.GetDiscoveryDocumentAsync("http://localhost:5000");
            if (disco.IsError)
            {
                Console.WriteLine(disco.Error);
                return;
            }

            // request token
            var tokenResponse = await client.RequestClientCredentialsTokenAsync(new ClientCredentialsTokenRequest
            {
                Address = disco.TokenEndpoint,
                ClientId = "client",
                ClientSecret = "secret",

                Scope = "api1"
            });

            if (tokenResponse.IsError)
            {
                Console.WriteLine(tokenResponse.Error);
                return;
            }

            Console.WriteLine(tokenResponse.Json);
            Console.WriteLine("\n\n");

            // call api
            var apiClient = new HttpClient();
            apiClient.SetBearerToken(tokenResponse.AccessToken);

            var response = await apiClient.GetAsync("https://localhost:5566/api/values");
            if (!response.IsSuccessStatusCode)
            {
                Console.WriteLine(response.StatusCode);
            }
            else
            {
                var content = await response.Content.ReadAsStringAsync();
                Console.WriteLine(JArray.Parse(content));
            }
        }
    }
}
```



## Protecting an API using Passwords

- The OAuth 2.0 resource owner password grant allows a client to send username and password to the token service and get an access token back that represents that user.


### 1. Adding users

- First add the following using statement to the `Config.cs` file:


```C#
using IdentityServer4.Test;

public static List<TestUser> GetUsers()
{
    return new List<TestUser>
    {
        new TestUser
        {
            SubjectId = "1",
            Username = "Sean",
            Password = "1234"
        },
        new TestUser
        {
            SubjectId = "2",
            Username = "bob",
            Password = "password"
        }
    };
}
```

- Then register the test users with IdentityServer:


```C#
public void ConfigureServices(IServiceCollection services)
{
    // configure identity server with in-memory stores, keys, clients and scopes
    services.AddIdentityServer()
        .AddInMemoryApiResources(Config.GetApiResources())
        .AddInMemoryClients(Config.GetClients())
        .AddTestUsers(Config.GetUsers());
}
```

- The `AddTestUsers` extension method does a couple of things under the hood
  - adds support for the resource owner password grant
  - adds support to user related services typically used by a login UI (we’ll use that in the next quickstart)
  - adds support for a profile service based on the test users (you’ll learn more about that in the next quickstart)

### 2. Adding a client for the resource owner password grant

- You could simply add support for the grant type to our existing client by changing the `AllowedGrantTypes` property. 
- If you need your client to be able to use both grant types that is absolutely supported.
- We are creating a separate client for the resource owner use case, add the following to your clients configuration:

```C#
public static IEnumerable<Client> GetClients()
{
    return new List<Client>
    {
        // other clients omitted...

        // resource owner password grant client
        new Client
        {
            ClientId = "ro.client",
            AllowedGrantTypes = GrantTypes.ResourceOwnerPassword,

            ClientSecrets =
            {
                new Secret("secret".Sha256())
            },
            AllowedScopes = { "api1" }
        }
    };
}
```

### 3. Requesting a token using the password grant

```C#
 // request token
 var tokenResponse = await client.RequestPasswordTokenAsync(new PasswordTokenRequest
            {
                Address = disco.TokenEndpoint,
                ClientId = "ro.client",
                ClientSecret = "secret",

                UserName = "Sean",
                Password = "1234"
            });

    if (tokenResponse.IsError)
    {
        Console.WriteLine(tokenResponse.Error);
        return;
    }

    Console.WriteLine(tokenResponse.Json);
	Console.WriteLine("\n\n");
```



## Adding User Authentication with OpenID Connect(oidc)

- OIDC(OpenID Connect) 是基於OAuth 2.0 的一種身份認證協議，基於JSON 格式，良好的兼容REST。
- 允許使用者端通過Authorization Server 驗證使用者身份，並以標準的ID_TOKEN 獲取使用者的基本資訊。
- 讓使用者能夠登錄到企業內的身分辨識，使用者成功辨識身分之後，再自動轉到相對應的應用程式，完成登入作業。讓使用者在不同的應用程式中遊走，卻不用到處註冊留下密碼。
- OpenID Connect = 身份驗證+ OAuth 2.0

### 1. Adding the UI

- 在原先的idsvr4專案下建立MVC架構，讓使用者可以登入/登出
- `src/IdentityServer`

```powershell
dotnet new is4ui
```

### 2. Creating an MVC client

- 建立一個新的Client並加入驗證
- Use the ASP.NET Core “Web Application” (i.e. MVC) template for that. Don’t configure the “Authentication” settings in the wizard – you will do this manually in this quickstart. Once you’ve created the project, configure the application to run on port 5002.
- To add support for OpenID Connect authentication to the MVC application, add the following to `ConfigureServices` in `Startup`:

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Clear();

    services.AddAuthentication(options =>
        {
            options.DefaultScheme = "Cookies";
            options.DefaultChallengeScheme = "oidc";
        })
        .AddCookie("Cookies")
        .AddOpenIdConnect("oidc", options =>
        {
            options.Authority = "http://localhost:5000";
            options.RequireHttpsMetadata = false;

            options.ClientId = "mvc";
            options.SaveTokens = true;
        });
}
```



## Switching to Hybrid Flow and adding API Access back

### 1. Modifying the idsvr

-  First we want to allow the client to use the hybrid flow, in addition we also want the client to allow doing server to server API calls which are not in the context of a user . This is expressed using the `AllowedGrantTypes` property.
- Next we need to add a client secret. This will be used to retrieve the access token on the back channel.
- We also give the client access to the `offline_access` scope - this allows requesting refresh tokens for long lived API access:

```C#
public static IEnumerable<Client> GetClients()
{
    return new List<Client>
    {
        new Client
        {
            ClientId = "mvc",
            ClientName = "MVC Client",
            AllowedGrantTypes = GrantTypes.Hybrid,

            ClientSecrets =
            {
                new Secret("secret".Sha256())
            },

            RedirectUris = new List<string> {"https://localhost:5002/signin-oidc"},
            PostLogoutRedirectUris = new List<string> {"https://localhost:5002/signout-callback-oidc" },


            AllowedScopes =
            {
                IdentityServerConstants.StandardScopes.OpenId,
                IdentityServerConstants.StandardScopes.Profile,
                "bio"
            },
            AllowOfflineAccess = true
        }
    };
}
```



### 2. Modifying the MVC client

- We configure the `ClientSecret` to match the secret at IdentityServer. 
-  Add the `offline_access` and `api1` scopes
- And set the `ResponseType` to `code id_token` (which basically means “use hybrid flow”).
- To keep the `website` claim in our mvc client identity we need to explicitly map the claim using ClaimActions.

```C#
.AddOpenIdConnect("oidc", options =>
{
    options.SignInScheme = "Cookies";

    options.Authority = "http://localhost:5000";
    options.RequireHttpsMetadata = false;

    options.ClientId = "mvc";
    options.ClientSecret = "secret";
    options.ResponseType = "code id_token";

    options.SaveTokens = true;
    options.GetClaimsFromUserInfoEndpoint = true;

    options.Scope.Add("api1");
    options.Scope.Add("offline_access");
    options.ClaimActions.MapJsonKey("website", "website");
});
```

### 3. Using the access token

- Technically the tokens are stored inside the properties section of the cookie. 
- The easiest way to access them is by using extension methods from the `Microsoft.AspNetCore.Authentication` namespace.

For example:

```C#
var accessToken = await HttpContext.GetTokenAsync("access_token")
var refreshToken = await HttpContext.GetTokenAsync("refresh_token");
```

- 取得AccessToken，並將之傳送給API做認證

```C#
public async Task<IActionResult> CallApi()
{
    var accessToken = await HttpContext.GetTokenAsync("access_token");

    var client = new HttpClient();
    
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
    
    var content = await client.GetStringAsync("http://localhost:5566/api/Pathogen/CW01");

    ViewBag.Json = JArray.Parse(content).ToString();
    
    return View("json");
}
```



# BioSecurity 加入 IdSvr4流程

## 1. API

### 	IdSvr4 :

- Config內新增API對應

```C#
 public IEnumerable<ApiResource> GetApiResources()
 {
	 return new List<ApiResource>
 	{
    	 new ApiResource( "icw.biosecurity.api", "ichainwin co. biosecurity api"),
     };
 }
```

### 	New API:

- 調整 Start up - ConfigureServices

```C#
public void ConfigureServices(IServiceCollection services)
{
    #region identity server

    services.AddAuthentication("Bearer")
    .AddJwtBearer("Bearer", options =>
    {
        options.Authority =  "http://192.168.1.102:13000";
        options.RequireHttpsMetadata = false;
        options.Audience = "icw.biosecurity.api";
    });

    #endregion
}
```

- 調整 Start up - Configure


```C#
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
        app.UseDeveloperExceptionPage();
    else
        app.UseHsts();

    app.UseAuthentication();
    app.UseCors();
    app.UseMvc();
}
```

- Controller 加入 `[Authorize]`

## 2. WebClient

### 	Portal:

- `yarn add oidc-client`
- 建立傳送至 `IdSvr4` 資料

```javascript
auth: 
{
    authority: "http://192.168.1.102:13000",
    client_id: "icw_biosecurity",
    redirect_uri: "/Callback",
    response_type: "id_token token",
    scope: "openid profile icw.biosecurity.api",
    silent_redirect_uri: "/SilentRenew",
    automaticSilentRenew: true
}
```

- 建立authService 產生`UserManager`設定
- 建立導頁(login、callback、signOut)
- HomePage透過 `getUser()` 判斷是否有Token
  - 有 => 導致首頁
  - 沒有 => 導致登入頁面

```javascript
async mounted() {
    let vi = this;
    userManager.getUser().then(function(user) {
        if (!user) {
       	 	vi.$router.push("login");
        }
        else
       		 store.commit('setUserProfile', user);     
    });
}
```

- loginPage 處理導向 IdSvr4

```javascript
methods: {
		login() {
			userManager.signinRedirect();
		}
	}
```

- 登入完成後，透過Callback判斷導向

```javascript
 mounted() {
    let vi = this;    
    userManager.signinRedirectCallback()
      .then(function() {
        vi.$router.push("/");
      })
      .catch(function(e) {        
        vi.$router.push("login");
      });
  }
```

- `signoutRedirect()` 可登出

### IdSvr4:

- 新增Client設定

```C#
new Client
{
	ClientId = "icw_biosecurity",
	ClientName =  "生物安全管理平台",

	AllowedGrantTypes = GrantTypes.Implicit,
	AllowAccessTokensViaBrowser = true,

	AccessTokenLifetime = 3600,
	RequirePkce = true,
	RequireClientSecret = false,
	RedirectUris =  
	new List<string>		{"http://localhost:8080/Callback", "http://localhost:8080/SilentRenew"},
	
PostLogoutRedirectUris = new List<string> {"http://localhost:8080"},

AllowedCorsOrigins = new List<string> {"http://localhost"},

AllowedScopes =
{
	IdentityServerConstants.StandardScopes.OpenId,
	IdentityServerConstants.StandardScopes.Profile,
	IdentityServerConstants.LocalApi.ScopeName,
	"icw.biosecurity.api"
},
	RequireConsent = false
}
```







































