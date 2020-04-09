## <a name="multiple-authentication-providers"></a><span data-ttu-id="310f2-101">Wielu dostawców uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="310f2-101">Multiple authentication providers</span></span>

<span data-ttu-id="310f2-102">Gdy aplikacja wymaga wielu dostawców, łańcuch metod rozszerzenia dostawcy za [AddAuthentication:](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication)</span><span class="sxs-lookup"><span data-stu-id="310f2-102">When the app requires multiple providers, chain the provider extension methods behind [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):</span></span>

```csharp
services.AddAuthentication()
    .AddMicrosoftAccount(microsoftOptions => { ... })
    .AddGoogle(googleOptions => { ... })
    .AddTwitter(twitterOptions => { ... })
    .AddFacebook(facebookOptions => { ... });
```
