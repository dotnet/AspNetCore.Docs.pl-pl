## <a name="multiple-authentication-providers"></a><span data-ttu-id="77d50-101">Wielu dostawców uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="77d50-101">Multiple authentication providers</span></span>

<span data-ttu-id="77d50-102">Gdy aplikacja wymaga wielu dostawców, należy utworzyć łańcuch metod rozszerzenia dostawcy za pomocą [addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):</span><span class="sxs-lookup"><span data-stu-id="77d50-102">When the app requires multiple providers, chain the provider extension methods behind [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):</span></span>

```csharp
services.AddAuthentication()
    .AddMicrosoftAccount(microsoftOptions => { ... })
    .AddGoogle(googleOptions => { ... })
    .AddTwitter(twitterOptions => { ... })
    .AddFacebook(facebookOptions => { ... });
```
