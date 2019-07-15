## <a name="multiple-authentication-providers"></a><span data-ttu-id="4c9ca-101">여러 인증 공급자</span><span class="sxs-lookup"><span data-stu-id="4c9ca-101">Multiple authentication providers</span></span>

<span data-ttu-id="4c9ca-102">앱에 여러 공급자가 필요한 경우 [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication) 뒤에 공급자 확장 메서드를 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="4c9ca-102">When the app requires multiple providers, chain the provider extension methods behind [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):</span></span>

```csharp
services.AddAuthentication()
    .AddMicrosoftAccount(microsoftOptions => { ... })
    .AddGoogle(googleOptions => { ... })
    .AddTwitter(twitterOptions => { ... })
    .AddFacebook(facebookOptions => { ... });
```
