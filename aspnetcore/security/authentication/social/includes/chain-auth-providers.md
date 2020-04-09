## <a name="multiple-authentication-providers"></a>Wielu dostawców uwierzytelniania

Gdy aplikacja wymaga wielu dostawców, łańcuch metod rozszerzenia dostawcy za [AddAuthentication:](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication)

```csharp
services.AddAuthentication()
    .AddMicrosoftAccount(microsoftOptions => { ... })
    .AddGoogle(googleOptions => { ... })
    .AddTwitter(twitterOptions => { ... })
    .AddFacebook(facebookOptions => { ... });
```
