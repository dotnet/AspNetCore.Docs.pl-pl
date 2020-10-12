<span data-ttu-id="af3b7-101">Azure Portal zawiera jeden z następujących formatów identyfikatora URI aplikacji na podstawie tego, czy dzierżawa usługi AAD ma [zweryfikowaną lub niezweryfikowaną domenę wydawcy](/azure/active-directory/develop/howto-configure-publisher-domain):</span><span class="sxs-lookup"><span data-stu-id="af3b7-101">The Azure portal provides one of the following App ID URI formats based on whether or not the AAD tenant has a [verified or unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain):</span></span>

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

<span data-ttu-id="af3b7-102">Gdy w aplikacji klienckiej jest używany drugi identyfikator URI identyfikatora aplikacji do tworzenia identyfikatora URI zakresu, a autoryzacja nie powiedzie się, spróbuj podać identyfikator URI zakresu bez schematu i hosta:</span><span class="sxs-lookup"><span data-stu-id="af3b7-102">When the latter of the two preceding App ID URIs is used in the client app to form the scope URI and authorization isn't successful, try supplying the scope URI without the scheme and host:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

<span data-ttu-id="af3b7-103">Przykład:</span><span class="sxs-lookup"><span data-stu-id="af3b7-103">Example:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
