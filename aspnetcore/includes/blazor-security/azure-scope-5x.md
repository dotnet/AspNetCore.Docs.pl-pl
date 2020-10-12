Podczas pracy z interfejsem API serwera zarejestrowanym w usłudze AAD, a rejestracja w usłudze AAD aplikacji znajduje się w dzierżawie, która korzysta z [niezweryfikowanej domeny wydawcy](/azure/active-directory/develop/howto-configure-publisher-domain), identyfikator URI aplikacji interfejsu API serwera nie `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` jest, ale zamiast tego jest w formacie `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}` . W takim przypadku domyślny zakres tokenu dostępu w `Program.Main` ( `Program.cs` ) *`Client`* aplikacji wygląda podobnie do poniższego:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{DEFAULT SCOPE}");
```

Aby skonfigurować aplikację interfejsu API serwera dla pasujących odbiorców, należy ustawić `Audience` w *`Server`* pliku ustawień aplikacji interfejsu API ( `appsettings.json` ) dopasowanie do odbiorców aplikacji dostarczonych przez Azure Portal:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "ValidateAuthority": true,
    "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
  }
}
```

W poprzedniej konfiguracji koniec `Audience` wartości **nie** obejmuje zakresu domyślnego `/{DEFAULT SCOPE}` .

Przykład:

`Program.Main` ( `Program.cs` ) *`Client`* aplikacji:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```

Skonfiguruj *`Server`* plik ustawień aplikacji interfejsu API ( `appsettings.json` ) za pomocą pasujących odbiorców ( `Audience` ):

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true,
    "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

W poprzednim przykładzie koniec `Audience` wartości **nie** obejmuje zakresu domyślnego `/API.Access` .
