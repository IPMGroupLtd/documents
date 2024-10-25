# Identity Server 4 Integration

**\*\*IMPORTANT NOTICE\*\***
Identity Server 4 maintainance stopped on November 2022, see [official announcement](https://identityserver4.readthedocs.io/en/latest/). Because of that, it is removed from ASP.NET Zero. We suggest migrating to OpenIddict. Check out ASP.NET Zero's [OpenIddict integration document](Infrastructure-Core-Angular-OpenIddict-Integration.md).

[IdentityServer4](http://identityserver.io/) is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core. ASP.NET Zero is integrated to IdentityServer4. It's **disabled by default**. Its located in `*.Web.Host` project.

## Configuration

You can enable/disable or configure it from **appsettings.json** file

```json
"IdentityServer": {
  "IsEnabled": "false",
  "Clients": [
    {
      "ClientId": "client",
      "AllowedGrantTypes": [ "password" ],
      "ClientSecrets": [
        {
          "Value": "def2edf7-5d42-4edc-a84a-30136c340e13"
        }
      ],
      "AllowedScopes": [ "default-api" ]
    },
    {
      "ClientId": "demo",
      "ClientName": "MVC Client Demo",
      "AllowedGrantTypes": [ "hybrid", "client_credentials" ],
      "RequireConsent": "true",
      "ClientSecrets": [
        {
          "Value": "def2edf7-5d42-4edc-a84a-30136c340e13"
        }
      ],
      "RedirectUris": [ "http://openidclientdemo.com:8001/signin-oidc" ],
      "PostLogoutRedirectUris": [ "http://openidclientdemo.com:8001/signout-callback-oidc" ],
      "AllowedScopes": [ "openid", "profile", "email", "phone", "default-api" ],
      "AllowOfflineAccess": "true"
    }
  ]
}
```

## Testing with Client

ASP.NET Zero solution has a sample console application (ConsoleApiClient) that can connects to the application, authenticates through IdentityServer4 and calls an API.



## Testing with MVC Client

You can use [aspnet-zero-samples](https://github.com/aspnetzero/aspnet-zero-samples)  -> `IdentityServerClient` project to test identity server with MVC client. 

Add a new client to `*.Web.Host` appsettings.json

```json
...
    {
      "ClientId": "mvcdemo",
      "ClientName": "MVC Client Demo 2",
      "AllowedGrantTypes": [ "implicit", "client_credentials" ],
      "RequireConsent": "true",
      "ClientSecrets": [
        {
          "Value": "mysecret"
        }
      ],
      "RedirectUris": [ "http://localhost:62964/signin-oidc" ],
      "PostLogoutRedirectUris": [ "http://localhost:62964/signout-callback-oidc" ],
      "AllowedScopes": [ "openid", "profile", "email", "phone", "default-api" ],
      "AllowOfflineAccess": "true"
    }
...
```

Download the `IdentityServerClient` project and open it's `Startup.cs` and modify `AddOpenIdConnect` area as seen below

```csharp
...
.AddOpenIdConnect("oidc", options =>
{
    options.SignInScheme = "Cookies";

    options.Authority = "https://localhost:44301";//change with your project url
    options.RequireHttpsMetadata = false;

    options.ClientId = "mvcdemo";
    options.ClientSecret = "mysecret";

    options.SaveTokens = true;
});
...
```



That is all. Now you can test it. 

Run both projects. Go to `IdentityServerClient` project's secure. <img src="images/identity-server-4-test-mvc-secure.png">

It will redirect you to the login page.

<img src="images/identity-server-4-test-host-login.png">

After you successfully login, you will see the consent page. <img src="images/identity-server-4-test-host-consent.png">

After you allow consents, you will redirect to the secure page and get user claims. <img src="images/identity-server-4-test-mvc-secure-after-login.png">

## OpenId Connect Integration

Once IdentityServer4 integration is enabled Web.Mvc application becomes an OpenId Connect server. That means another web application can use standard OpenId Connect protocol to authenticate users with your
application and get permission to share their information (a.k.a. consent screen).

## More

See [IdentityServer4's own documentation](https://identityserver4.readthedocs.io/en/latest/) to understand and configure IdentityServer4.
