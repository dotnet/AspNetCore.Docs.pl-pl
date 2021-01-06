Azure Portal zawiera jeden z następujących formatów identyfikatora URI aplikacji na podstawie tego, czy dzierżawa usługi AAD ma [zweryfikowaną lub niezweryfikowaną domenę wydawcy](/azure/active-directory/develop/howto-configure-publisher-domain):

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

Gdy w aplikacji klienckiej jest używany drugi identyfikator URI identyfikatora aplikacji do tworzenia identyfikatora URI zakresu, a autoryzacja nie powiedzie się, spróbuj podać identyfikator URI zakresu bez schematu i hosta:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

Przykład:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
