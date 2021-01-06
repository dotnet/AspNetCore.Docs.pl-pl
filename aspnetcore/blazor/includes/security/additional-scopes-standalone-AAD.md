Dodaj element <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> dla `User.Read` uprawnienia z <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes> :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes
        .Add("https://graph.microsoft.com/User.Read");
});
```
