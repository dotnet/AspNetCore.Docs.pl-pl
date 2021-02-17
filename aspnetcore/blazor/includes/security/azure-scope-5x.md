---
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
ms.openlocfilehash: 5964554c36e2242b70faee390374828acd2bd860
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552534"
---
<span data-ttu-id="ff48b-101">Podczas pracy z interfejsem API serwera zarejestrowanym w usłudze AAD, a rejestracja w usłudze AAD aplikacji znajduje się w dzierżawie, która korzysta z [niezweryfikowanej domeny wydawcy](/azure/active-directory/develop/howto-configure-publisher-domain), identyfikator URI aplikacji interfejsu API serwera nie `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` jest, ale zamiast tego jest w formacie `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}` .</span><span class="sxs-lookup"><span data-stu-id="ff48b-101">When working with a server API registered with AAD and the app's AAD registration is in an tenant that relies on an [unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain), the App ID URI of your server API app isn't `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` but instead is in the format `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}`.</span></span> <span data-ttu-id="ff48b-102">W takim przypadku domyślny zakres tokenu dostępu w `Program.Main` ( `Program.cs` ) *`Client`* aplikacji wygląda podobnie do poniższego:</span><span class="sxs-lookup"><span data-stu-id="ff48b-102">If that's the case, the default access token scope in `Program.Main` (`Program.cs`) of the *`Client`* app appears similar to the following:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{DEFAULT SCOPE}");
```

<span data-ttu-id="ff48b-103">Aby skonfigurować aplikację interfejsu API serwera dla pasujących odbiorców, należy ustawić `Audience` w *`Server`* pliku ustawień aplikacji interfejsu API ( `appsettings.json` ) dopasowanie do odbiorców aplikacji dostarczonych przez Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="ff48b-103">To configure the server API app for a matching audience, set the `Audience` in the *`Server`* API app settings file (`appsettings.json`) to match the app's audience provided by the Azure portal:</span></span>

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

<span data-ttu-id="ff48b-104">W poprzedniej konfiguracji koniec `Audience` wartości **nie** obejmuje zakresu domyślnego `/{DEFAULT SCOPE}` .</span><span class="sxs-lookup"><span data-stu-id="ff48b-104">In the preceding configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`.</span></span>

<span data-ttu-id="ff48b-105">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ff48b-105">Example:</span></span>

<span data-ttu-id="ff48b-106">`Program.Main` ( `Program.cs` ) *`Client`* aplikacji:</span><span class="sxs-lookup"><span data-stu-id="ff48b-106">`Program.Main` (`Program.cs`) of the *`Client`* app:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```

<span data-ttu-id="ff48b-107">Skonfiguruj *`Server`* plik ustawień aplikacji interfejsu API ( `appsettings.json` ) za pomocą pasujących odbiorców ( `Audience` ):</span><span class="sxs-lookup"><span data-stu-id="ff48b-107">Configure the *`Server`* API app settings file (`appsettings.json`) with a matching audience (`Audience`):</span></span>

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

<span data-ttu-id="ff48b-108">W poprzednim przykładzie koniec `Audience` wartości **nie** obejmuje zakresu domyślnego `/API.Access` .</span><span class="sxs-lookup"><span data-stu-id="ff48b-108">In the preceding example, the end of the `Audience` value does **not** include the default scope `/API.Access`.</span></span>
