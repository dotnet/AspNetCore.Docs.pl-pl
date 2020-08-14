<span data-ttu-id="bae3f-101">Domyślnie zostanie wyświetlona wartość domyślna tryb logowania w trybie przekierowania i powrót do trybu logowania przekierowywania, jeśli nie można otworzyć okna podręcznego.</span><span class="sxs-lookup"><span data-stu-id="bae3f-101">The framework defaults to pop-up login mode and falls back to redirect login mode if a pop-up can't be opened.</span></span> <span data-ttu-id="bae3f-102">Skonfiguruj MSAL do używania trybu logowania przekierowania, ustawiając `LoginMode` Właściwość <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> na `redirect` :</span><span class="sxs-lookup"><span data-stu-id="bae3f-102">Configure MSAL to use redirect login mode by setting the `LoginMode` property of <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> to `redirect`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

<span data-ttu-id="bae3f-103">Ustawieniem domyślnym jest `popup` , a w wartości ciągu nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="bae3f-103">The default setting is `popup`, and the string value isn't case sensitive.</span></span>
