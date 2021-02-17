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
ms.openlocfilehash: 4e7c0e9b0a164e0181af5d6baaedf0669c1c06aa
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552531"
---
## <a name="troubleshoot"></a><span data-ttu-id="bdc73-101">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="bdc73-101">Troubleshoot</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="common-errors"></a><span data-ttu-id="bdc73-102">Typowe błędy</span><span class="sxs-lookup"><span data-stu-id="bdc73-102">Common errors</span></span>

* <span data-ttu-id="bdc73-103">Nieautoryzowany klient dla usługi AAD</span><span class="sxs-lookup"><span data-stu-id="bdc73-103">Unauthorized client for AAD</span></span>

  > <span data-ttu-id="bdc73-104">Informacja: uwierzytelnianie Microsoft. AspNetCore. Authorization. DefaultAuthorizationService [2] nie powiodło się.</span><span class="sxs-lookup"><span data-stu-id="bdc73-104">info: Microsoft.AspNetCore.Authorization.DefaultAuthorizationService[2] Authorization failed.</span></span> <span data-ttu-id="bdc73-105">Te wymagania nie zostały spełnione: DenyAnonymousAuthorizationRequirement: wymaga uwierzytelnionego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bdc73-105">These requirements were not met: DenyAnonymousAuthorizationRequirement: Requires an authenticated user.</span></span>

  <span data-ttu-id="bdc73-106">Błąd wywołania zwrotnego logowania z usługi AAD:</span><span class="sxs-lookup"><span data-stu-id="bdc73-106">Login callback error from AAD:</span></span>

  * <span data-ttu-id="bdc73-107">Błąd: `unauthorized_client`</span><span class="sxs-lookup"><span data-stu-id="bdc73-107">Error: `unauthorized_client`</span></span>
  * <span data-ttu-id="bdc73-108">Zharmonizowan `AADB2C90058: The provided application is not configured to allow public clients.`</span><span class="sxs-lookup"><span data-stu-id="bdc73-108">Description: `AADB2C90058: The provided application is not configured to allow public clients.`</span></span>

  <span data-ttu-id="bdc73-109">Aby rozwiązać ten problem:</span><span class="sxs-lookup"><span data-stu-id="bdc73-109">To resolve the error:</span></span>

  1. <span data-ttu-id="bdc73-110">W Azure Portal uzyskaj dostęp do [manifestu aplikacji](/azure/active-directory/develop/reference-app-manifest).</span><span class="sxs-lookup"><span data-stu-id="bdc73-110">In the Azure portal, access the [app's manifest](/azure/active-directory/develop/reference-app-manifest).</span></span>
  1. <span data-ttu-id="bdc73-111">Ustaw [ `allowPublicClient` atrybut](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) na `null` lub `true` .</span><span class="sxs-lookup"><span data-stu-id="bdc73-111">Set the [`allowPublicClient` attribute](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) to `null` or `true`.</span></span>

::: moniker-end

### <a name="cookies-and-site-data"></a><span data-ttu-id="bdc73-112">Cookies i dane lokacji</span><span class="sxs-lookup"><span data-stu-id="bdc73-112">Cookies and site data</span></span>

<span data-ttu-id="bdc73-113">Cookiedane i lokacje mogą być utrwalane między aktualizacjami aplikacji i zakłócają testowanie i rozwiązywanie problemów.</span><span class="sxs-lookup"><span data-stu-id="bdc73-113">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="bdc73-114">W przypadku wprowadzania zmian w kodzie aplikacji należy wyczyścić następujące zmiany dotyczące konta użytkownika i konfiguracji aplikacji dostawcy:</span><span class="sxs-lookup"><span data-stu-id="bdc73-114">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="bdc73-115">Logowanie użytkownika cookie</span><span class="sxs-lookup"><span data-stu-id="bdc73-115">User sign-in cookies</span></span>
* <span data-ttu-id="bdc73-116">Aplikacje cookie s</span><span class="sxs-lookup"><span data-stu-id="bdc73-116">App cookies</span></span>
* <span data-ttu-id="bdc73-117">Buforowane i przechowywane dane lokacji</span><span class="sxs-lookup"><span data-stu-id="bdc73-117">Cached and stored site data</span></span>

<span data-ttu-id="bdc73-118">Jednym z metod zapobiegania powstawaniu cookie danych i wymawianiu przez proces testowania i rozwiązywania problemów jest:</span><span class="sxs-lookup"><span data-stu-id="bdc73-118">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="bdc73-119">Konfigurowanie przeglądarki</span><span class="sxs-lookup"><span data-stu-id="bdc73-119">Configure a browser</span></span>
  * <span data-ttu-id="bdc73-120">Użyj przeglądarki do testowania, który można skonfigurować, aby usunąć wszystkie cookie i dane witryny za każdym razem, gdy przeglądarka zostanie zamknięta.</span><span class="sxs-lookup"><span data-stu-id="bdc73-120">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
  * <span data-ttu-id="bdc73-121">Upewnij się, że przeglądarka została ZAMKNIĘTA ręcznie lub przez IDE w celu dowolnych zmian w konfiguracji aplikacji, użytkownika testowego lub dostawcy.</span><span class="sxs-lookup"><span data-stu-id="bdc73-121">Make sure that the browser is closed manually or by the IDE for any change to the app, test user, or provider configuration.</span></span>
* <span data-ttu-id="bdc73-122">Użyj polecenia niestandardowego, aby otworzyć przeglądarkę w trybie incognito lub prywatnym w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="bdc73-122">Use a custom command to open a browser in incognito or private mode in Visual Studio:</span></span>
  * <span data-ttu-id="bdc73-123">Otwórz okno dialogowe **Przeglądaj z programem** Visual Studio. </span><span class="sxs-lookup"><span data-stu-id="bdc73-123">Open **Browse With** dialog box from Visual Studio's **Run** button.</span></span>
  * <span data-ttu-id="bdc73-124">Wybierz przycisk **Add** (Dodaj).</span><span class="sxs-lookup"><span data-stu-id="bdc73-124">Select the **Add** button.</span></span>
  * <span data-ttu-id="bdc73-125">Podaj ścieżkę do przeglądarki w polu **program** .</span><span class="sxs-lookup"><span data-stu-id="bdc73-125">Provide the path to your browser in the **Program** field.</span></span> <span data-ttu-id="bdc73-126">Następujące ścieżki plików wykonywalnych są typowymi lokalizacjami instalacji dla systemu Windows 10.</span><span class="sxs-lookup"><span data-stu-id="bdc73-126">The following executable paths are typical installation locations for Windows 10.</span></span> <span data-ttu-id="bdc73-127">Jeśli przeglądarka jest zainstalowana w innej lokalizacji lub nie korzystasz z systemu Windows 10, podaj ścieżkę do pliku wykonywalnego przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="bdc73-127">If your browser is installed in a different location or you aren't using Windows 10, provide the path to the browser's executable.</span></span>
    * <span data-ttu-id="bdc73-128">Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span><span class="sxs-lookup"><span data-stu-id="bdc73-128">Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span></span>
    * <span data-ttu-id="bdc73-129">Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span><span class="sxs-lookup"><span data-stu-id="bdc73-129">Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span></span>
    * <span data-ttu-id="bdc73-130">Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`</span><span class="sxs-lookup"><span data-stu-id="bdc73-130">Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`</span></span>
  * <span data-ttu-id="bdc73-131">W polu **argumenty** Podaj opcję wiersza polecenia, która jest wykorzystywana przez przeglądarkę do otwierania w trybie incognito lub prywatnym.</span><span class="sxs-lookup"><span data-stu-id="bdc73-131">In the **Arguments** field, provide the command-line option that the browser uses to open in incognito or private mode.</span></span> <span data-ttu-id="bdc73-132">Niektóre przeglądarki wymagają adresu URL aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bdc73-132">Some browsers require the URL of the app.</span></span>
    * <span data-ttu-id="bdc73-133">Microsoft Edge: Użyj `-inprivate` .</span><span class="sxs-lookup"><span data-stu-id="bdc73-133">Microsoft Edge: Use `-inprivate`.</span></span>
    * <span data-ttu-id="bdc73-134">Google Chrome: Użyj `--incognito --new-window {URL}` , gdzie symbol zastępczy `{URL}` jest adresem URL do otwarcia (na przykład `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="bdc73-134">Google Chrome: Use `--incognito --new-window {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
    * <span data-ttu-id="bdc73-135">Mozilla Firefox: Użyj `-private -url {URL}` , gdzie symbol zastępczy `{URL}` jest adresem URL do otwarcia (na przykład `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="bdc73-135">Mozilla Firefox: Use `-private -url {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
  * <span data-ttu-id="bdc73-136">Podaj nazwę w polu **przyjazna nazwa** .</span><span class="sxs-lookup"><span data-stu-id="bdc73-136">Provide a name in the **Friendly name** field.</span></span> <span data-ttu-id="bdc73-137">Na przykład `Firefox Auth Testing`.</span><span class="sxs-lookup"><span data-stu-id="bdc73-137">For example, `Firefox Auth Testing`.</span></span>
  * <span data-ttu-id="bdc73-138">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="bdc73-138">Select the **OK** button.</span></span>
  * <span data-ttu-id="bdc73-139">Aby uniknąć konieczności wybierania profilu przeglądarki dla każdej iteracji testowania w aplikacji, Ustaw profil jako domyślny przy użyciu przycisku **Ustaw jako domyślny** .</span><span class="sxs-lookup"><span data-stu-id="bdc73-139">To avoid having to select the browser profile for each iteration of testing with an app, set the profile as the default with the **Set as Default** button.</span></span>
  * <span data-ttu-id="bdc73-140">Upewnij się, że w środowisku IDE jest ZAMKNIĘTA przeglądarka, aby wprowadzić zmiany w konfiguracji aplikacji, użytkownika testowego lub dostawcy.</span><span class="sxs-lookup"><span data-stu-id="bdc73-140">Make sure that the browser is closed by the IDE for any change to the app, test user, or provider configuration.</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="bdc73-141">Uruchom aplikację serwera</span><span class="sxs-lookup"><span data-stu-id="bdc73-141">Run the Server app</span></span>

<span data-ttu-id="bdc73-142">Podczas testowania i rozwiązywania problemów z hostowaną Blazor aplikacją upewnij się, że aplikacja jest uruchamiana z **`Server`** projektu.</span><span class="sxs-lookup"><span data-stu-id="bdc73-142">When testing and troubleshooting a hosted Blazor app, make sure that you're running the app from the **`Server`** project.</span></span> <span data-ttu-id="bdc73-143">Na przykład w programie Visual Studio upewnij się, że projekt serwera został wyróżniony w **Eksplorator rozwiązań** przed uruchomieniem aplikacji, korzystając z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="bdc73-143">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="bdc73-144">Wybierz przycisk **Run** (Uruchom).</span><span class="sxs-lookup"><span data-stu-id="bdc73-144">Select the **Run** button.</span></span>
* <span data-ttu-id="bdc73-145">Użyj **debugowania**  >  **Rozpocznij debugowanie** z menu.</span><span class="sxs-lookup"><span data-stu-id="bdc73-145">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="bdc73-146">Naciśnij klawisz <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="bdc73-146">Press <kbd>F5</kbd>.</span></span>

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a><span data-ttu-id="bdc73-147">Sprawdzanie zawartości tokenu sieci Web JSON (JWT)</span><span class="sxs-lookup"><span data-stu-id="bdc73-147">Inspect the content of a JSON Web Token (JWT)</span></span>

<span data-ttu-id="bdc73-148">Aby zdekodować token sieci Web JSON (JWT), użyj narzędzia [JWT.MS](https://jwt.ms/) firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="bdc73-148">To decode a JSON Web Token (JWT), use Microsoft's [jwt.ms](https://jwt.ms/) tool.</span></span> <span data-ttu-id="bdc73-149">Wartości w interfejsie użytkownika nigdy nie opuszczają przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="bdc73-149">Values in the UI never leave your browser.</span></span>
