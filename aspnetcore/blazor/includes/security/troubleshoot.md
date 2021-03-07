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
ms.openlocfilehash: f58da78475d65cbb70b0b177e1b0443535e97d55
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2021
ms.locfileid: "102402299"
---
## <a name="troubleshoot"></a><span data-ttu-id="a9692-101">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="a9692-101">Troubleshoot</span></span>

### <a name="common-errors"></a><span data-ttu-id="a9692-102">Typowe błędy</span><span class="sxs-lookup"><span data-stu-id="a9692-102">Common errors</span></span>

* <span data-ttu-id="a9692-103">Nieporozumiena konfiguracja aplikacji lub Identity dostawcy (IP)</span><span class="sxs-lookup"><span data-stu-id="a9692-103">Misconfiguration of the app or Identity Provider (IP)</span></span>

  <span data-ttu-id="a9692-104">Najczęstsze błędy są spowodowane przez niepoprawną konfigurację.</span><span class="sxs-lookup"><span data-stu-id="a9692-104">The most common errors are caused by incorrect configuration.</span></span> <span data-ttu-id="a9692-105">Poniżej przedstawiono kilka przykładów:</span><span class="sxs-lookup"><span data-stu-id="a9692-105">The following are a few examples:</span></span>
  
  * <span data-ttu-id="a9692-106">W zależności od wymagań scenariusza brakujące lub nieprawidłowe uprawnienia, wystąpienie, identyfikator dzierżawy, domena dzierżawy, identyfikator klienta lub identyfikator URI przekierowania uniemożliwiają aplikacji uwierzytelnianie klientów.</span><span class="sxs-lookup"><span data-stu-id="a9692-106">Depending on the requirements of the scenario, a missing or incorrect Authority, Instance, Tenant ID, Tenant domain, Client ID, or Redirect URI prevents an app from authenticating clients.</span></span>
  * <span data-ttu-id="a9692-107">Nieprawidłowy zakres tokenów dostępu uniemożliwia klientom dostęp do punktów końcowych interfejsu API sieci Web serwera.</span><span class="sxs-lookup"><span data-stu-id="a9692-107">An incorrect access token scope prevents clients from accessing server web API endpoints.</span></span>
  * <span data-ttu-id="a9692-108">Nieprawidłowe lub brakujące uprawnienia interfejsu API serwera uniemożliwiają klientom dostęp do punktów końcowych interfejsu API sieci Web serwera.</span><span class="sxs-lookup"><span data-stu-id="a9692-108">Incorrect or missing server API permissions prevent clients from accessing server web API endpoints.</span></span>
  * <span data-ttu-id="a9692-109">Uruchamianie aplikacji na innym porcie niż skonfigurowany w identyfikatorze URI przekierowania dla Identity rejestracji aplikacji dostawcy.</span><span class="sxs-lookup"><span data-stu-id="a9692-109">Running the app at a different port than is configured in the Redirect URI of the Identity Provider's app registration.</span></span>
  
  <span data-ttu-id="a9692-110">W sekcjach konfiguracji wskazówek tego artykułu przedstawiono przykłady poprawnej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="a9692-110">Configuration sections of this article's guidance show examples of the correct configuration.</span></span> <span data-ttu-id="a9692-111">Uważnie Sprawdź każdą sekcję artykułu w poszukiwaniu nieodpowiedniej konfiguracji aplikacji i adresu IP.</span><span class="sxs-lookup"><span data-stu-id="a9692-111">Carefully check each section of the article looking for app and IP misconfiguration.</span></span>
  
  <span data-ttu-id="a9692-112">Jeśli konfiguracja jest prawidłowa:</span><span class="sxs-lookup"><span data-stu-id="a9692-112">If the configuration appears correct:</span></span>
  
  * <span data-ttu-id="a9692-113">Analizuj Dzienniki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9692-113">Analyze application logs.</span></span>
  * <span data-ttu-id="a9692-114">Sprawdź ruch sieciowy między aplikacją kliencką a aplikacją IP lub serwerem za pomocą narzędzi deweloperskich w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="a9692-114">Examine the network traffic between the client app and the IP or server app with the browser's developer tools.</span></span> <span data-ttu-id="a9692-115">Często dokładny komunikat o błędzie lub komunikat informujący o tym, co jest przyczyną problemu, jest zwracany do klienta przez aplikację IP lub serwer po wykonaniu żądania.</span><span class="sxs-lookup"><span data-stu-id="a9692-115">Often, an exact error message or a message with a clue to what's causing the problem is returned to the client by the IP or server app after making a request.</span></span> <span data-ttu-id="a9692-116">Wskazówki dotyczące narzędzi deweloperskich znajdują się w następujących artykułach:</span><span class="sxs-lookup"><span data-stu-id="a9692-116">Developer tools guidance is found in the following articles:</span></span>

    * <span data-ttu-id="a9692-117">[Google Chrome](https://developers.google.com/web/tools/chrome-devtools/network) (dokumentacja firmy Google)</span><span class="sxs-lookup"><span data-stu-id="a9692-117">[Google Chrome](https://developers.google.com/web/tools/chrome-devtools/network) (Google documentation)</span></span>
    * [<span data-ttu-id="a9692-118">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="a9692-118">Microsoft Edge</span></span>](/microsoft-edge/devtools-guide-chromium/network/)
    * <span data-ttu-id="a9692-119">[Mozilla Firefox](https://developer.mozilla.org/docs/Tools/Network_Monitor) (dokumentacja firmy Mozilla)</span><span class="sxs-lookup"><span data-stu-id="a9692-119">[Mozilla Firefox](https://developer.mozilla.org/docs/Tools/Network_Monitor) (Mozilla documentation)</span></span>

  * <span data-ttu-id="a9692-120">Dekodowanie zawartości tokenu sieci Web JSON (JWT) używanego do uwierzytelniania klienta lub uzyskiwania dostępu do internetowego interfejsu API serwera w zależności od tego, gdzie występuje problem.</span><span class="sxs-lookup"><span data-stu-id="a9692-120">Decode the contents of a JSON Web Token (JWT) used for authenticating a client or accessing a server web API, depending on where the problem is occurring.</span></span> <span data-ttu-id="a9692-121">Aby uzyskać więcej informacji, zobacz [Sprawdzanie zawartości tokenu sieci Web JSON (JWT)](#inspect-the-content-of-a-json-web-token-jwt).</span><span class="sxs-lookup"><span data-stu-id="a9692-121">For more information, see [Inspect the content of a JSON Web Token (JWT)](#inspect-the-content-of-a-json-web-token-jwt).</span></span>
  
  <span data-ttu-id="a9692-122">Zespół zawiera reaguje na informacje zwrotne i usterki w artykułach (Otwórz problem z tej sekcji opinii o **stronie** ), ale nie jest w stanie zapewnić pomocy technicznej.</span><span class="sxs-lookup"><span data-stu-id="a9692-122">The documenation team responds to document feedback and bugs in articles (open an issue from the **This page** feedback section) but is unable to provide product support.</span></span> <span data-ttu-id="a9692-123">Dostępne są kilka publicznych forów pomocy technicznej, które ułatwiają rozwiązywanie problemów z aplikacją.</span><span class="sxs-lookup"><span data-stu-id="a9692-123">Several public support forums are available to assist with troubleshooting an app.</span></span> <span data-ttu-id="a9692-124">Zalecamy następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="a9692-124">We recommend the following:</span></span>
  
  * [<span data-ttu-id="a9692-125">Stack Overflow (tag: `blazor` )</span><span class="sxs-lookup"><span data-stu-id="a9692-125">Stack Overflow (tag: `blazor`)</span></span>](https://stackoverflow.com/questions/tagged/blazor)
  * [<span data-ttu-id="a9692-126">ASP.NET Core zespołu zapasowego</span><span class="sxs-lookup"><span data-stu-id="a9692-126">ASP.NET Core Slack Team</span></span>](http://tattoocoder.com/aspnet-slack-sign-up/)
  * <span data-ttu-id="a9692-127">[Blazor Warunkom](https://gitter.im/aspnet/Blazor)</span><span class="sxs-lookup"><span data-stu-id="a9692-127">[Blazor Gitter](https://gitter.im/aspnet/Blazor)</span></span>
  
  <span data-ttu-id="a9692-128">W przypadku niezwiązanych z zabezpieczeniami, niewrażliwych i niepoufnych raportów o błędach platformy, [Otwórz problem z jednostką produktu ASP.NET Core](https://github.com/dotnet/aspnetcore/issues).</span><span class="sxs-lookup"><span data-stu-id="a9692-128">For non-security, non-sensitive, and non-confidential reproducible framework bug reports, [open an issue with the ASP.NET Core product unit](https://github.com/dotnet/aspnetcore/issues).</span></span> <span data-ttu-id="a9692-129">Nie otwieraj problemu z jednostką produktu do momentu dokładnego zbadania przyczyny problemu i nie można jej rozwiązać samodzielnie i pomocy społeczności na forum pomocy technicznej publicznej.</span><span class="sxs-lookup"><span data-stu-id="a9692-129">Don't open an issue with the product unit until you've thoroughly investigated the cause of a problem and can't resolve it on your own and with the help of the community on a public support forum.</span></span> <span data-ttu-id="a9692-130">Jednostka produktu nie może rozwiązywać problemów z poszczególnymi aplikacjami, które są uszkodzone ze względu na prostą nieprawidłową konfigurację lub przypadki użycia obejmujące usługi innych firm.</span><span class="sxs-lookup"><span data-stu-id="a9692-130">The product unit isn't able to troubleshoot individual apps that are broken due to simple misconfiguration or use cases involving third-party services.</span></span> <span data-ttu-id="a9692-131">Jeśli raport jest poufny lub poufny lub zawiera opis potencjalnej luki w zabezpieczeniach produktu, którego mogą wykorzystać osoby atakujące, zobacz [Raportowanie problemów z zabezpieczeniami i błędów (repozytorium dotnet/Aspnetcore GitHub)](https://github.com/dotnet/aspnetcore/blob/main/CONTRIBUTING.md#reporting-security-issues-and-bugs).</span><span class="sxs-lookup"><span data-stu-id="a9692-131">If a report is sensitive or confidential in nature or describes a potential security flaw in the product that attackers may exploit, see [Reporting security issues and bugs (dotnet/aspnetcore GitHub repository)](https://github.com/dotnet/aspnetcore/blob/main/CONTRIBUTING.md#reporting-security-issues-and-bugs).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="a9692-132">Nieautoryzowany klient dla usługi AAD</span><span class="sxs-lookup"><span data-stu-id="a9692-132">Unauthorized client for AAD</span></span>

  > <span data-ttu-id="a9692-133">Informacja: uwierzytelnianie Microsoft. AspNetCore. Authorization. DefaultAuthorizationService [2] nie powiodło się.</span><span class="sxs-lookup"><span data-stu-id="a9692-133">info: Microsoft.AspNetCore.Authorization.DefaultAuthorizationService[2] Authorization failed.</span></span> <span data-ttu-id="a9692-134">Te wymagania nie zostały spełnione: DenyAnonymousAuthorizationRequirement: wymaga uwierzytelnionego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a9692-134">These requirements were not met: DenyAnonymousAuthorizationRequirement: Requires an authenticated user.</span></span>

  <span data-ttu-id="a9692-135">Błąd wywołania zwrotnego logowania z usługi AAD:</span><span class="sxs-lookup"><span data-stu-id="a9692-135">Login callback error from AAD:</span></span>

  * <span data-ttu-id="a9692-136">Błąd: `unauthorized_client`</span><span class="sxs-lookup"><span data-stu-id="a9692-136">Error: `unauthorized_client`</span></span>
  * <span data-ttu-id="a9692-137">Zharmonizowan `AADB2C90058: The provided application is not configured to allow public clients.`</span><span class="sxs-lookup"><span data-stu-id="a9692-137">Description: `AADB2C90058: The provided application is not configured to allow public clients.`</span></span>

  <span data-ttu-id="a9692-138">Aby rozwiązać ten problem:</span><span class="sxs-lookup"><span data-stu-id="a9692-138">To resolve the error:</span></span>

  1. <span data-ttu-id="a9692-139">W Azure Portal uzyskaj dostęp do [manifestu aplikacji](/azure/active-directory/develop/reference-app-manifest).</span><span class="sxs-lookup"><span data-stu-id="a9692-139">In the Azure portal, access the [app's manifest](/azure/active-directory/develop/reference-app-manifest).</span></span>
  1. <span data-ttu-id="a9692-140">Ustaw [ `allowPublicClient` atrybut](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) na `null` lub `true` .</span><span class="sxs-lookup"><span data-stu-id="a9692-140">Set the [`allowPublicClient` attribute](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) to `null` or `true`.</span></span>

::: moniker-end

### <a name="cookies-and-site-data"></a><span data-ttu-id="a9692-141">Cookies i dane lokacji</span><span class="sxs-lookup"><span data-stu-id="a9692-141">Cookies and site data</span></span>

<span data-ttu-id="a9692-142">Cookiedane i lokacje mogą być utrwalane między aktualizacjami aplikacji i zakłócają testowanie i rozwiązywanie problemów.</span><span class="sxs-lookup"><span data-stu-id="a9692-142">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="a9692-143">W przypadku wprowadzania zmian w kodzie aplikacji należy wyczyścić następujące zmiany dotyczące konta użytkownika i konfiguracji aplikacji dostawcy:</span><span class="sxs-lookup"><span data-stu-id="a9692-143">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="a9692-144">Logowanie użytkownika cookie</span><span class="sxs-lookup"><span data-stu-id="a9692-144">User sign-in cookies</span></span>
* <span data-ttu-id="a9692-145">Aplikacje cookie s</span><span class="sxs-lookup"><span data-stu-id="a9692-145">App cookies</span></span>
* <span data-ttu-id="a9692-146">Buforowane i przechowywane dane lokacji</span><span class="sxs-lookup"><span data-stu-id="a9692-146">Cached and stored site data</span></span>

<span data-ttu-id="a9692-147">Jednym z metod zapobiegania powstawaniu cookie danych i wymawianiu przez proces testowania i rozwiązywania problemów jest:</span><span class="sxs-lookup"><span data-stu-id="a9692-147">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="a9692-148">Konfigurowanie przeglądarki</span><span class="sxs-lookup"><span data-stu-id="a9692-148">Configure a browser</span></span>
  * <span data-ttu-id="a9692-149">Użyj przeglądarki do testowania, który można skonfigurować, aby usunąć wszystkie cookie i dane witryny za każdym razem, gdy przeglądarka zostanie zamknięta.</span><span class="sxs-lookup"><span data-stu-id="a9692-149">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
  * <span data-ttu-id="a9692-150">Upewnij się, że przeglądarka została ZAMKNIĘTA ręcznie lub przez IDE w celu dowolnych zmian w konfiguracji aplikacji, użytkownika testowego lub dostawcy.</span><span class="sxs-lookup"><span data-stu-id="a9692-150">Make sure that the browser is closed manually or by the IDE for any change to the app, test user, or provider configuration.</span></span>
* <span data-ttu-id="a9692-151">Użyj polecenia niestandardowego, aby otworzyć przeglądarkę w trybie incognito lub prywatnym w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a9692-151">Use a custom command to open a browser in incognito or private mode in Visual Studio:</span></span>
  * <span data-ttu-id="a9692-152">Otwórz okno dialogowe **Przeglądaj z programem** Visual Studio. </span><span class="sxs-lookup"><span data-stu-id="a9692-152">Open **Browse With** dialog box from Visual Studio's **Run** button.</span></span>
  * <span data-ttu-id="a9692-153">Wybierz przycisk **Add** (Dodaj).</span><span class="sxs-lookup"><span data-stu-id="a9692-153">Select the **Add** button.</span></span>
  * <span data-ttu-id="a9692-154">Podaj ścieżkę do przeglądarki w polu **program** .</span><span class="sxs-lookup"><span data-stu-id="a9692-154">Provide the path to your browser in the **Program** field.</span></span> <span data-ttu-id="a9692-155">Następujące ścieżki plików wykonywalnych są typowymi lokalizacjami instalacji dla systemu Windows 10.</span><span class="sxs-lookup"><span data-stu-id="a9692-155">The following executable paths are typical installation locations for Windows 10.</span></span> <span data-ttu-id="a9692-156">Jeśli przeglądarka jest zainstalowana w innej lokalizacji lub nie korzystasz z systemu Windows 10, podaj ścieżkę do pliku wykonywalnego przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="a9692-156">If your browser is installed in a different location or you aren't using Windows 10, provide the path to the browser's executable.</span></span>
    * <span data-ttu-id="a9692-157">Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span><span class="sxs-lookup"><span data-stu-id="a9692-157">Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span></span>
    * <span data-ttu-id="a9692-158">Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span><span class="sxs-lookup"><span data-stu-id="a9692-158">Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span></span>
    * <span data-ttu-id="a9692-159">Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`</span><span class="sxs-lookup"><span data-stu-id="a9692-159">Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`</span></span>
  * <span data-ttu-id="a9692-160">W polu **argumenty** Podaj opcję wiersza polecenia, która jest wykorzystywana przez przeglądarkę do otwierania w trybie incognito lub prywatnym.</span><span class="sxs-lookup"><span data-stu-id="a9692-160">In the **Arguments** field, provide the command-line option that the browser uses to open in incognito or private mode.</span></span> <span data-ttu-id="a9692-161">Niektóre przeglądarki wymagają adresu URL aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9692-161">Some browsers require the URL of the app.</span></span>
    * <span data-ttu-id="a9692-162">Microsoft Edge: Użyj `-inprivate` .</span><span class="sxs-lookup"><span data-stu-id="a9692-162">Microsoft Edge: Use `-inprivate`.</span></span>
    * <span data-ttu-id="a9692-163">Google Chrome: Użyj `--incognito --new-window {URL}` , gdzie symbol zastępczy `{URL}` jest adresem URL do otwarcia (na przykład `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="a9692-163">Google Chrome: Use `--incognito --new-window {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
    * <span data-ttu-id="a9692-164">Mozilla Firefox: Użyj `-private -url {URL}` , gdzie symbol zastępczy `{URL}` jest adresem URL do otwarcia (na przykład `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="a9692-164">Mozilla Firefox: Use `-private -url {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
  * <span data-ttu-id="a9692-165">Podaj nazwę w polu **przyjazna nazwa** .</span><span class="sxs-lookup"><span data-stu-id="a9692-165">Provide a name in the **Friendly name** field.</span></span> <span data-ttu-id="a9692-166">Na przykład `Firefox Auth Testing`.</span><span class="sxs-lookup"><span data-stu-id="a9692-166">For example, `Firefox Auth Testing`.</span></span>
  * <span data-ttu-id="a9692-167">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="a9692-167">Select the **OK** button.</span></span>
  * <span data-ttu-id="a9692-168">Aby uniknąć konieczności wybierania profilu przeglądarki dla każdej iteracji testowania w aplikacji, Ustaw profil jako domyślny przy użyciu przycisku **Ustaw jako domyślny** .</span><span class="sxs-lookup"><span data-stu-id="a9692-168">To avoid having to select the browser profile for each iteration of testing with an app, set the profile as the default with the **Set as Default** button.</span></span>
  * <span data-ttu-id="a9692-169">Upewnij się, że w środowisku IDE jest ZAMKNIĘTA przeglądarka, aby wprowadzić zmiany w konfiguracji aplikacji, użytkownika testowego lub dostawcy.</span><span class="sxs-lookup"><span data-stu-id="a9692-169">Make sure that the browser is closed by the IDE for any change to the app, test user, or provider configuration.</span></span>

### <a name="app-upgrades"></a><span data-ttu-id="a9692-170">Uaktualnienia aplikacji</span><span class="sxs-lookup"><span data-stu-id="a9692-170">App upgrades</span></span>

<span data-ttu-id="a9692-171">Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestaw .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w ramach aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9692-171">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="a9692-172">W niektórych przypadkach niespójne pakiety mogą przerwać aplikację podczas przeprowadzania uaktualnień głównych.</span><span class="sxs-lookup"><span data-stu-id="a9692-172">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="a9692-173">Większość tych problemów można naprawić, wykonując następujące instrukcje:</span><span class="sxs-lookup"><span data-stu-id="a9692-173">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="a9692-174">Wyczyść pamięć podręczną pakietów NuGet systemu lokalnego, wykonując [`dotnet nuget locals all --clear`](/dotnet/core/tools/dotnet-nuget-locals) ją z poziomu powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="a9692-174">Clear the local system's NuGet package caches by executing [`dotnet nuget locals all --clear`](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>
1. <span data-ttu-id="a9692-175">Usuń projekt `bin` i `obj` foldery.</span><span class="sxs-lookup"><span data-stu-id="a9692-175">Delete the project's `bin` and `obj` folders.</span></span>
1. <span data-ttu-id="a9692-176">Przywróć i skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="a9692-176">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="a9692-177">Usuń wszystkie pliki z folderu wdrożenia na serwerze przed ponownym wdrożeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a9692-177">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

> [!NOTE]
> <span data-ttu-id="a9692-178">Użycie wersji pakietu niezgodnej z platformą docelową aplikacji nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="a9692-178">Use of package versions incompatible with the app's target framework isn't supported.</span></span> <span data-ttu-id="a9692-179">Aby uzyskać informacje na temat pakietu, użyj [galerii NuGet](https://www.nuget.org) lub [Eksploratora pakietów FuGet](https://www.fuget.org).</span><span class="sxs-lookup"><span data-stu-id="a9692-179">For information on a package, use the [NuGet Gallery](https://www.nuget.org) or [FuGet Package Explorer](https://www.fuget.org).</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="a9692-180">Uruchom aplikację serwera</span><span class="sxs-lookup"><span data-stu-id="a9692-180">Run the Server app</span></span>

<span data-ttu-id="a9692-181">Podczas testowania i rozwiązywania problemów z hostowanym Blazor rozwiązaniem upewnij się, że aplikacja jest uruchamiana z **`Server`** projektu.</span><span class="sxs-lookup"><span data-stu-id="a9692-181">When testing and troubleshooting a hosted Blazor solution, make sure that you're running the app from the **`Server`** project.</span></span> <span data-ttu-id="a9692-182">Na przykład w programie Visual Studio upewnij się, że projekt serwera został wyróżniony w **Eksplorator rozwiązań** przed uruchomieniem aplikacji, korzystając z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="a9692-182">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="a9692-183">Wybierz przycisk **Run** (Uruchom).</span><span class="sxs-lookup"><span data-stu-id="a9692-183">Select the **Run** button.</span></span>
* <span data-ttu-id="a9692-184">Użyj **debugowania**  >  **Rozpocznij debugowanie** z menu.</span><span class="sxs-lookup"><span data-stu-id="a9692-184">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="a9692-185">Naciśnij klawisz <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="a9692-185">Press <kbd>F5</kbd>.</span></span>

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a><span data-ttu-id="a9692-186">Sprawdzanie zawartości tokenu sieci Web JSON (JWT)</span><span class="sxs-lookup"><span data-stu-id="a9692-186">Inspect the content of a JSON Web Token (JWT)</span></span>

<span data-ttu-id="a9692-187">Aby zdekodować token sieci Web JSON (JWT), użyj narzędzia [JWT.MS](https://jwt.ms/) firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="a9692-187">To decode a JSON Web Token (JWT), use Microsoft's [jwt.ms](https://jwt.ms/) tool.</span></span> <span data-ttu-id="a9692-188">Wartości w interfejsie użytkownika nigdy nie opuszczają przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="a9692-188">Values in the UI never leave your browser.</span></span>

<span data-ttu-id="a9692-189">Przykładowy zakodowany token JWT (skrócony na potrzeby wyświetlania):</span><span class="sxs-lookup"><span data-stu-id="a9692-189">Example encoded JWT (shortened for display):</span></span>

> <span data-ttu-id="a9692-190">eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1j ... bQdHBHGcQQRbW7Wmo6SWYG4V_bU55Ug_PW4pLPr20tTS8Ct7_uwy9DWrzCMzpD-EiwT5IjXwlGX3IXVjHIlX50IVIydBoPQtadvT7saKo1G5Jmutgq41o-dmz6-yBMKV2_nXA25Q</span><span class="sxs-lookup"><span data-stu-id="a9692-190">eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1j ... bQdHBHGcQQRbW7Wmo6SWYG4V_bU55Ug_PW4pLPr20tTS8Ct7_uwy9DWrzCMzpD-EiwT5IjXwlGX3IXVjHIlX50IVIydBoPQtadvT7saKo1G5Jmutgq41o-dmz6-yBMKV2_nXA25Q</span></span>

<span data-ttu-id="a9692-191">Przykładowa JWT zdekodowana przez narzędzie dla aplikacji, która uwierzytelnia się na platformie Azure AAD B2C:</span><span class="sxs-lookup"><span data-stu-id="a9692-191">Example JWT decoded by the tool for an app that authenticates against Azure AAD B2C:</span></span>

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1610059429,
  "nbf": 1610055829,
  "ver": "1.0",
  "iss": "https://mysiteb2c.b2clogin.com/5cc15ea8-a296-4aa3-97e4-226dcc9ad298/v2.0/",
  "sub": "5ee963fb-24d6-4d72-a1b6-889c6e2c7438",
  "aud": "70bde375-fce3-4b82-984a-b247d823a03f",
  "nonce": "b2641f54-8dc4-42ca-97ea-7f12ff4af871",
  "iat": 1610055829,
  "auth_time": 1610055822,
  "idp": "idp.com",
  "tfp": "B2C_1_signupsignin"
}.[Signature]
```
