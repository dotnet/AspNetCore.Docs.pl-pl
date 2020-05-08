## <a name="multiple-authentication-providers"></a>Wielu dostawców uwierzytelniania

Gdy aplikacja wymaga wielu dostawców, należy utworzyć łańcuch metod rozszerzenia dostawcy za pomocą [addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):

```csharp
services.AddAuthentication()
    .AddMicrosoftAccount(microsoftOptions => { ... })
    .AddGoogle(googleOptions => { ... })
    .AddTwitter(twitterOptions => { ... })
    .AddFacebook(facebookOptions => { ... });
```
