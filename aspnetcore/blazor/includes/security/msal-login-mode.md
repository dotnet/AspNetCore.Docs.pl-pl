Domyślnie zostanie wyświetlona wartość domyślna tryb logowania w trybie przekierowania i powrót do trybu logowania przekierowywania, jeśli nie można otworzyć okna podręcznego. Skonfiguruj MSAL do używania trybu logowania przekierowania, ustawiając `LoginMode` Właściwość <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> na `redirect` :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

Ustawieniem domyślnym jest `popup` , a w wartości ciągu nie jest rozróżniana wielkość liter.
