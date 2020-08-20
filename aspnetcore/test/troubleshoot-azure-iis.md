---
title: Rozwiązywanie problemów ASP.NET Core na Azure App Service i usługach IIS
author: rick-anderson
description: Dowiedz się, jak zdiagnozować problemy z wdrożeniami Azure App Service i Internet Information Services (IIS) ASP.NET Core aplikacji.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
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
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: 117c777dc9ae1b8c6448f097132454b714a1b5dc
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632164"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="b1d7d-103">Rozwiązywanie problemów ASP.NET Core na Azure App Service i usługach IIS</span><span class="sxs-lookup"><span data-stu-id="b1d7d-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="b1d7d-104">Autor [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b1d7d-105">Ten artykuł zawiera informacje dotyczące typowych błędów uruchamiania aplikacji oraz instrukcje dotyczące sposobu diagnozowania błędów podczas wdrażania aplikacji w usłudze Azure App Service lub IIS:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="b1d7d-106">Błędy uruchamiania aplikacji</span><span class="sxs-lookup"><span data-stu-id="b1d7d-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="b1d7d-107">Objaśnia typowe scenariusze uruchamiania kodu stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="b1d7d-108">Rozwiązywanie problemów dotyczących Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b1d7d-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="b1d7d-109">Zawiera porady dotyczące rozwiązywania problemów z aplikacjami wdrożonymi w celu Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="b1d7d-110">Rozwiązywanie problemów w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="b1d7d-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="b1d7d-111">Zawiera porady dotyczące rozwiązywania problemów z aplikacjami wdrożonymi w usługach IIS lub lokalnie uruchomionymi IIS Express.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="b1d7d-112">Wskazówki dotyczą zarówno wdrożeń systemu Windows Server, jak i pulpitu systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="b1d7d-113">Wyczyść pamięć podręczną pakietów</span><span class="sxs-lookup"><span data-stu-id="b1d7d-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="b1d7d-114">Wyjaśnia, co należy zrobić, gdy niespójne pakiety przerywają działanie aplikacji podczas przeprowadzania uaktualnień głównych lub zmiany wersji pakietu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="b1d7d-115">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="b1d7d-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="b1d7d-116">Wyświetla listę dodatkowych tematów dotyczących rozwiązywania problemów.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="b1d7d-117">Błędy uruchamiania aplikacji</span><span class="sxs-lookup"><span data-stu-id="b1d7d-117">App startup errors</span></span>

<span data-ttu-id="b1d7d-118">W programie Visual Studio ASP.NET Core Project domyślnie [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting podczas debugowania.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="b1d7d-119">*502,5 — błąd procesu* lub *błąd uruchomienia 500,30* , który występuje, gdy debugowanie lokalne można zdiagnozować przy użyciu porady w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="b1d7d-120">403,14 zabronione</span><span class="sxs-lookup"><span data-stu-id="b1d7d-120">403.14 Forbidden</span></span>

<span data-ttu-id="b1d7d-121">Nie można uruchomić aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-121">The app fails to start.</span></span> <span data-ttu-id="b1d7d-122">Rejestrowany jest następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-122">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="b1d7d-123">Ten błąd jest zwykle spowodowany przez uszkodzone wdrożenie w systemie hostingu, który obejmuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-123">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="b1d7d-124">Aplikacja jest wdrażana w niewłaściwym folderze w systemie hostingu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-124">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="b1d7d-125">W procesie wdrażania nie powiodło się przeniesienie wszystkich plików i folderów aplikacji do folderu wdrożenia w systemie hostingu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-125">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="b1d7d-126">W wdrożeniu brakuje pliku *web.config* lub zawartość pliku *web.config* jest źle sformułowana.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-126">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="b1d7d-127">Wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-127">Perform the following steps:</span></span>

1. <span data-ttu-id="b1d7d-128">Usuń wszystkie pliki i foldery z folderu wdrożenia w systemie hostingu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-128">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="b1d7d-129">Wdróż ponownie zawartość folderu *publikowania* aplikacji w systemie hostingu przy użyciu zwykłej metody wdrażania, takiej jak Visual Studio, PowerShell lub wdrażanie ręczne:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-129">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="b1d7d-130">Upewnij się, że plik *web.config* jest obecny we wdrożeniu i że jego zawartość jest poprawna.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-130">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="b1d7d-131">Podczas hostowania w Azure App Service upewnij się, że aplikacja została wdrożona w `D:\home\site\wwwroot` folderze.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-131">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="b1d7d-132">Jeśli aplikacja jest hostowana przez usługi IIS, upewnij się, że aplikacja jest wdrożona w **ścieżce fizycznej** usług IIS pokazanej w **ustawieniach podstawowych**w **Menedżerze usług IIS**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-132">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="b1d7d-133">Upewnij się, że wszystkie pliki i foldery aplikacji zostały wdrożone, porównując wdrożenie w systemie hostingu z zawartością folderu *publikowania* projektu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-133">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="b1d7d-134">Aby uzyskać więcej informacji na temat układu opublikowanej aplikacji ASP.NET Core, zobacz <xref:host-and-deploy/directory-structure> .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-134">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="b1d7d-135">Aby uzyskać więcej informacji na temat pliku *web.config* , zobacz <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-135">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="b1d7d-136">500 Wewnętrzny błąd serwera</span><span class="sxs-lookup"><span data-stu-id="b1d7d-136">500 Internal Server Error</span></span>

<span data-ttu-id="b1d7d-137">Aplikacja zostanie uruchomiona, ale błąd uniemożliwia serwerowi spełnienie żądania.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-137">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="b1d7d-138">Ten błąd występuje w kodzie aplikacji podczas uruchamiania lub podczas tworzenia odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-138">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="b1d7d-139">Odpowiedź może nie zawierać żadnej zawartości lub odpowiedź może pojawić się w przeglądarce jako *błąd wewnętrzny serwera 500* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-139">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="b1d7d-140">Dziennik zdarzeń aplikacji zwykle stwierdza, że aplikacja została uruchomiona normalnie.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-140">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="b1d7d-141">Z perspektywy serwera jest to poprawne.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-141">From the server's perspective, that's correct.</span></span> <span data-ttu-id="b1d7d-142">Aplikacja została uruchomiona, ale nie może wygenerować prawidłowej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-142">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="b1d7d-143">Uruchom aplikację w wierszu polecenia na serwerze lub Włącz dziennik stdout modułu ASP.NET Core, aby rozwiązać problem.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-143">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="b1d7d-144">500,0 błąd ładowania procedury obsługi w procesie</span><span class="sxs-lookup"><span data-stu-id="b1d7d-144">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="b1d7d-145">Proces roboczy kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-145">The worker process fails.</span></span> <span data-ttu-id="b1d7d-146">Aplikacja nie zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-146">The app doesn't start.</span></span>

<span data-ttu-id="b1d7d-147">Wystąpił nieznany błąd podczas ładowania składników [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-147">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="b1d7d-148">Wykonaj jedno z następujących działań:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-148">Take one of the following actions:</span></span>

* <span data-ttu-id="b1d7d-149">[Pomoc techniczna firmy Microsoft](https://support.microsoft.com/oas/default.aspx?prid=15832) kontaktu (wybierz **Narzędzia deweloperskie** następnie **ASP.NET Core**).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-149">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="b1d7d-150">Zadawaj pytanie na Stack Overflow.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-150">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="b1d7d-151">Zajrzyj do problemu w naszym [repozytorium GitHub](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-151">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="b1d7d-152">Niepowodzenie uruchamiania w procesie 500,30</span><span class="sxs-lookup"><span data-stu-id="b1d7d-152">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="b1d7d-153">Proces roboczy kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-153">The worker process fails.</span></span> <span data-ttu-id="b1d7d-154">Aplikacja nie zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-154">The app doesn't start.</span></span>

<span data-ttu-id="b1d7d-155">[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) próbuje uruchomić program .NET Core CLR w procesie, ale nie można go uruchomić.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-155">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="b1d7d-156">Przyczyna niepowodzenia uruchomienia procesu zwykle można ustalić na podstawie wpisów w dzienniku zdarzeń aplikacji i dzienniku modułu ASP.NET Core stdout.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-156">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="b1d7d-157">Typowe warunki awarii:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-157">Common failure conditions:</span></span>

* <span data-ttu-id="b1d7d-158">Aplikacja jest nieprawidłowo skonfigurowana z powodu docelowej wersji ASP.NET Core udostępnionej platformy, która nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-158">The app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="b1d7d-159">Sprawdź, które wersje ASP.NET Core udostępnionej platformy są zainstalowane na maszynie docelowej.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-159">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>
* <span data-ttu-id="b1d7d-160">Za pomocą Azure Key Vault, brak uprawnień do Key Vault.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-160">Using Azure Key Vault, lack of permissions to the Key Vault.</span></span> <span data-ttu-id="b1d7d-161">Sprawdź zasady dostępu w Key Vault celem, aby upewnić się, że udzielono odpowiednich uprawnień.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-161">Check the access policies in the targeted Key Vault to ensure that the correct permissions are granted.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="b1d7d-162">500,31 ANCM nie może odnaleźć natywnych zależności</span><span class="sxs-lookup"><span data-stu-id="b1d7d-162">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="b1d7d-163">Proces roboczy kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-163">The worker process fails.</span></span> <span data-ttu-id="b1d7d-164">Aplikacja nie zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-164">The app doesn't start.</span></span>

<span data-ttu-id="b1d7d-165">[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) próbuje uruchomić środowisko uruchomieniowe programu .NET Core w procesie, ale nie może go uruchomić.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-165">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="b1d7d-166">Najczęstszym powodem tego błędu uruchomienia jest to, że `Microsoft.NETCore.App` `Microsoft.AspNetCore.App` środowisko uruchomieniowe lub nie jest zainstalowane.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-166">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="b1d7d-167">Jeśli aplikacja jest wdrożona w programie docelowym ASP.NET Core 3,0 i ta wersja nie istnieje na maszynie, wystąpi błąd.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-167">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="b1d7d-168">Poniżej znajduje się przykładowy komunikat o błędzie:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-168">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="b1d7d-169">W komunikacie o błędzie są wyświetlane wszystkie zainstalowane wersje programu .NET Core i wersja żądana przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-169">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="b1d7d-170">Aby naprawić ten błąd, należy:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-170">To fix this error, either:</span></span>

* <span data-ttu-id="b1d7d-171">Zainstaluj na komputerze odpowiednią wersję programu .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-171">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="b1d7d-172">Zmień aplikację na wersję docelową platformy .NET Core, która jest obecna na maszynie.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-172">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="b1d7d-173">Publikowanie aplikacji jako [samodzielnego wdrożenia](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-173">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="b1d7d-174">Podczas pracy w środowisku deweloperskim ( `ASPNETCORE_ENVIRONMENT` zmienna środowiskowa jest ustawiona na `Development` ) określony błąd jest zapisywana w odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-174">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="b1d7d-175">Przyczyna niepowodzenia uruchomienia procesu znajduje się również w dzienniku zdarzeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-175">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="b1d7d-176">500,32 ANCM nie może załadować biblioteki DLL</span><span class="sxs-lookup"><span data-stu-id="b1d7d-176">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="b1d7d-177">Proces roboczy kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-177">The worker process fails.</span></span> <span data-ttu-id="b1d7d-178">Aplikacja nie zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-178">The app doesn't start.</span></span>

<span data-ttu-id="b1d7d-179">Najbardziej typową przyczyną tego błędu jest to, że aplikacja jest publikowana dla niezgodnej architektury procesora.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-179">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="b1d7d-180">Jeśli proces roboczy jest uruchomiony jako aplikacja 32-bitowa, a aplikacja została opublikowana w docelowym 64-bitowym, ten błąd wystąpi.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-180">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="b1d7d-181">Aby naprawić ten błąd, należy:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-181">To fix this error, either:</span></span>

* <span data-ttu-id="b1d7d-182">Opublikuj ponownie aplikację dla tej samej architektury procesora co proces roboczy.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-182">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="b1d7d-183">Opublikuj aplikację jako [wdrożenie zależne od platformy](/dotnet/core/deploying/#framework-dependent-executables-fde).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-183">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="b1d7d-184">500,33 błąd ładowania obsługi żądania ANCM</span><span class="sxs-lookup"><span data-stu-id="b1d7d-184">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="b1d7d-185">Proces roboczy kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-185">The worker process fails.</span></span> <span data-ttu-id="b1d7d-186">Aplikacja nie zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-186">The app doesn't start.</span></span>

<span data-ttu-id="b1d7d-187">Aplikacja nie odwołuje się do `Microsoft.AspNetCore.App` struktury.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-187">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="b1d7d-188">`Microsoft.AspNetCore.App` [Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module)może obsługiwać tylko aplikacje ukierunkowane na platformę.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-188">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="b1d7d-189">Aby naprawić ten błąd, upewnij się, że aplikacja jest ukierunkowana na `Microsoft.AspNetCore.App` platformę.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-189">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="b1d7d-190">Sprawdź, `.runtimeconfig.json` czy w celu zweryfikowania struktury wskazywanej przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-190">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="b1d7d-191">500,34 ANCM mieszane modele hostingu nie są obsługiwane</span><span class="sxs-lookup"><span data-stu-id="b1d7d-191">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="b1d7d-192">Proces roboczy nie może uruchomić zarówno aplikacji w procesie, jak i aplikacji pozaprocesowej w tym samym procesie.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-192">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="b1d7d-193">Aby naprawić ten błąd, uruchom aplikacje w osobnych pulach aplikacji usług IIS.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-193">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="b1d7d-194">500,35 ANCM wiele aplikacji w procesie w tym samym procesie</span><span class="sxs-lookup"><span data-stu-id="b1d7d-194">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="b1d7d-195">Proces roboczy nie może uruchamiać wielu aplikacji w procesie w tym samym procesie.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-195">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="b1d7d-196">Aby naprawić ten błąd, uruchom aplikacje w osobnych pulach aplikacji usług IIS.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-196">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="b1d7d-197">500,36 ANCM błąd ładowania procedury obsługi poza procesem</span><span class="sxs-lookup"><span data-stu-id="b1d7d-197">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="b1d7d-198">Procedura obsługi żądań poza procesem, *aspnetcorev2_outofprocess.dll*, nie jest obok pliku *aspnetcorev2.dll* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-198">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="b1d7d-199">Oznacza to uszkodzenie instalacji [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-199">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="b1d7d-200">Aby naprawić ten błąd, napraw instalację [pakietu hostingu platformy .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (dla usług IIS) lub programu Visual Studio (w przypadku IIS Express).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-200">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="b1d7d-201">Nie można uruchomić 500,37 ANCM w ramach limitu czasu uruchamiania</span><span class="sxs-lookup"><span data-stu-id="b1d7d-201">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="b1d7d-202">Nie można uruchomić ANCM w limicie czasu uruchamiania dostarczają.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-202">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="b1d7d-203">Domyślnie limit czasu wynosi 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-203">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="b1d7d-204">Ten błąd może wystąpić podczas uruchamiania dużej liczby aplikacji na tym samym komputerze.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-204">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="b1d7d-205">Podczas uruchamiania Sprawdź, czy na serwerze są naskoki użycie procesora CPU/pamięci.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-205">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="b1d7d-206">Może być konieczne rozłożenie procesu uruchamiania wielu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-206">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="50038-ancm-application-dll-not-found"></a><span data-ttu-id="b1d7d-207">500,38 nie znaleziono biblioteki DLL aplikacji ANCM</span><span class="sxs-lookup"><span data-stu-id="b1d7d-207">500.38 ANCM Application DLL Not Found</span></span>

<span data-ttu-id="b1d7d-208">ANCM nie może odnaleźć biblioteki DLL aplikacji, która powinna znajdować się obok pliku wykonywalnego.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-208">ANCM failed to locate the application DLL, which should be next to the executable.</span></span>

<span data-ttu-id="b1d7d-209">Ten błąd występuje podczas hostowania aplikacji jako [pliku wykonywalnego jednoplikowego](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) przy użyciu modelu hostingu w procesie.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-209">This error occurs when hosting an app packaged as a [single-file executable](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) using the in-process hosting model.</span></span> <span data-ttu-id="b1d7d-210">Model w procesie wymaga, aby ANCM ładował aplikację .NET Core do istniejącego procesu usług IIS.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-210">The in-process model requires that the ANCM load the .NET Core app into the existing IIS process.</span></span> <span data-ttu-id="b1d7d-211">Ten scenariusz nie jest obsługiwany przez model wdrażania pojedynczego pliku.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-211">This scenario isn't supported by the single-file deployment model.</span></span> <span data-ttu-id="b1d7d-212">Aby naprawić ten błąd, użyj **jednego** z następujących metod w pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-212">Use **one** of the following approaches in the app's project file to fix this error:</span></span>

1. <span data-ttu-id="b1d7d-213">Wyłącz publikowanie pojedynczych plików przez ustawienie `PublishSingleFile` właściwości MSBuild na `false` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-213">Disable single-file publishing by setting the `PublishSingleFile` MSBuild property to `false`.</span></span>
1. <span data-ttu-id="b1d7d-214">Przejdź do modelu hostingu poza procesem, ustawiając `AspNetCoreHostingModel` Właściwość programu MSBuild na wartość `OutOfProcess` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-214">Switch to the out-of-process hosting model by setting the `AspNetCoreHostingModel` MSBuild property to `OutOfProcess`.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="b1d7d-215">502.5 Błąd procesu</span><span class="sxs-lookup"><span data-stu-id="b1d7d-215">502.5 Process Failure</span></span>

<span data-ttu-id="b1d7d-216">Proces roboczy kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-216">The worker process fails.</span></span> <span data-ttu-id="b1d7d-217">Aplikacja nie zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-217">The app doesn't start.</span></span>

<span data-ttu-id="b1d7d-218">[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) próbuje uruchomić proces roboczy, ale jego uruchomienie nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-218">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="b1d7d-219">Przyczyna niepowodzenia uruchomienia procesu zwykle można ustalić na podstawie wpisów w dzienniku zdarzeń aplikacji i dzienniku modułu ASP.NET Core stdout.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-219">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="b1d7d-220">Typowym warunkiem niepowodzenia jest to, że aplikacja jest nieprawidłowo skonfigurowana z powodu docelowej wersji ASP.NET Core udostępnionej platformy, która nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-220">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="b1d7d-221">Sprawdź, które wersje ASP.NET Core udostępnionej platformy są zainstalowane na maszynie docelowej.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-221">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="b1d7d-222">*Platforma udostępniona* jest zestawem zestawów (plików*dll* ), które są zainstalowane na maszynie i do których odwołuje się pakiet `Microsoft.AspNetCore.App` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-222">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="b1d7d-223">Odwołanie do pakietu nie może określać minimalnej wymaganej wersji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-223">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="b1d7d-224">Aby uzyskać więcej informacji, zobacz [udostępnioną strukturę](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-224">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="b1d7d-225">Strona błędu *niepowodzenia procesu 502,5* jest zwracana w przypadku niepowodzenia konfiguracji hostingu lub aplikacji. powoduje to niepowodzenie procesu roboczego:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-225">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="b1d7d-226">Nie można uruchomić aplikacji (ErrorCode "0x800700C1")</span><span class="sxs-lookup"><span data-stu-id="b1d7d-226">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="b1d7d-227">Nie można uruchomić aplikacji, ponieważ nie można załadować zestawu aplikacji (*dll*).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-227">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="b1d7d-228">Ten błąd występuje, gdy wystąpiła niezgodność między opublikowaną aplikacją a procesem w3wp/iisexpress.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-228">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="b1d7d-229">Upewnij się, że ustawienie 32-bitowe puli aplikacji jest poprawne:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-229">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="b1d7d-230">Wybierz pulę aplikacji w **pulach aplikacji**Menedżera usług IIS.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-230">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="b1d7d-231">Wybierz pozycję **Ustawienia zaawansowane** w obszarze **Edytuj pulę aplikacji** w panelu **Akcje** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-231">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="b1d7d-232">Ustaw **opcję Włącz aplikacje 32-bitowe**:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-232">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="b1d7d-233">W przypadku wdrażania aplikacji 32-bitowej (x86) ustaw wartość na `True` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-233">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="b1d7d-234">W przypadku wdrażania aplikacji 64-bitowej (x64) ustaw wartość na `False` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-234">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="b1d7d-235">Upewnij się, że nie występuje konflikt między `<Platform>` właściwością programu MSBuild w pliku projektu a opublikowaną bitową w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-235">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="b1d7d-236">Resetowanie połączenia</span><span class="sxs-lookup"><span data-stu-id="b1d7d-236">Connection reset</span></span>

<span data-ttu-id="b1d7d-237">Jeśli wystąpi błąd po wysłaniu nagłówków, jest zbyt późno, aby serwer mógł wysłać **błąd wewnętrzny serwera 500** w przypadku wystąpienia błędu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-237">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="b1d7d-238">Zdarza się to często w przypadku wystąpienia błędu podczas serializacji obiektów złożonych dla odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-238">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="b1d7d-239">Ten typ błędu pojawia się jako błąd *resetowania połączenia* na kliencie.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-239">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="b1d7d-240">[Rejestrowanie aplikacji](xref:fundamentals/logging/index) może pomóc w rozwiązywaniu tego typu błędów.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-240">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="b1d7d-241">Domyślne limity uruchamiania</span><span class="sxs-lookup"><span data-stu-id="b1d7d-241">Default startup limits</span></span>

<span data-ttu-id="b1d7d-242">[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) jest skonfigurowany z domyślną *startupTimeLimitą* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-242">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="b1d7d-243">Po lewej stronie wartości domyślnej, uruchomienie aplikacji może trwać do dwóch minut przed zarejestrowaniem awarii procesu przez moduł.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-243">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="b1d7d-244">Aby uzyskać informacje na temat konfigurowania modułu, zobacz [atrybuty elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-244">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="b1d7d-245">Rozwiązywanie problemów dotyczących Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b1d7d-245">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="b1d7d-246">Dziennik zdarzeń aplikacji (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-246">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="b1d7d-247">Aby uzyskać dostęp do dziennika zdarzeń aplikacji, użyj bloku **diagnozowanie i rozwiązywanie problemów** w Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-247">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="b1d7d-248">W Azure Portal Otwórz aplikację w **App Services**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-248">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="b1d7d-249">Kliknij pozycję **Diagnozowanie i rozwiązywanie problemów**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-249">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="b1d7d-250">Wybierz nagłówek **Narzędzia diagnostyczne** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-250">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="b1d7d-251">W obszarze **Narzędzia obsługi**wybierz przycisk **zdarzenia aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-251">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="b1d7d-252">Zapoznaj się z najnowszym błędem podanym w pozycji *AspNetCoreModule IIS* lub *IIS AspNetCoreModule v2* w kolumnie **Źródło** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-252">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="b1d7d-253">Alternatywą dla korzystania z bloku **diagnozowanie i rozwiązywanie problemów** jest przetestowanie pliku dziennika zdarzeń aplikacji bezpośrednio przy użyciu [kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="b1d7d-253">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="b1d7d-254">Otwórz **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-254">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="b1d7d-255">Wybierz przycisk \*\*Przejdź &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-255">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="b1d7d-256">Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-256">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="b1d7d-257">Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-257">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="b1d7d-258">Otwórz folder **LogFiles** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-258">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="b1d7d-259">Wybierz ikonę ołówka obok pliku *eventlog.xml* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-259">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="b1d7d-260">Przejrzyj dziennik.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-260">Examine the log.</span></span> <span data-ttu-id="b1d7d-261">Przewiń w dół dziennika, aby zobaczyć najnowsze zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-261">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="b1d7d-262">Uruchamianie aplikacji w konsoli kudu</span><span class="sxs-lookup"><span data-stu-id="b1d7d-262">Run the app in the Kudu console</span></span>

<span data-ttu-id="b1d7d-263">Wiele błędów uruchamiania nie tworzy użytecznych informacji w dzienniku zdarzeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-263">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="b1d7d-264">Możesz uruchomić aplikację w konsoli zdalnego wykonywania [kudu](https://github.com/projectkudu/kudu/wiki) , aby wykryć błąd:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-264">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="b1d7d-265">Otwórz **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-265">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="b1d7d-266">Wybierz przycisk \*\*Przejdź &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-266">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="b1d7d-267">Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-267">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="b1d7d-268">Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-268">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="b1d7d-269">Testowanie aplikacji 32-bitowej (x86)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-269">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="b1d7d-270">**Bieżąca wersja**</span><span class="sxs-lookup"><span data-stu-id="b1d7d-270">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="b1d7d-271">Uruchom aplikację: </span><span class="sxs-lookup"><span data-stu-id="b1d7d-271">Run the app:</span></span>
   * <span data-ttu-id="b1d7d-272">Jeśli aplikacja jest [wdrożeniem zależnym od platformy](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="b1d7d-272">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="b1d7d-273">Jeśli aplikacja jest [wdrożeniem](/dotnet/core/deploying/#self-contained-deployments-scd)niezależnym:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-273">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="b1d7d-274">Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-274">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="b1d7d-275">**Wdrożenie zależne od platformy uruchomione w wersji zapoznawczej**</span><span class="sxs-lookup"><span data-stu-id="b1d7d-275">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="b1d7d-276">*Wymaga zainstalowania rozszerzenia witryny środowiska uruchomieniowego ASP.NET Core {VERSION} (x86).*</span><span class="sxs-lookup"><span data-stu-id="b1d7d-276">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="b1d7d-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` ( `{X.Y}` to wersja środowiska uruchomieniowego)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="b1d7d-278">Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="b1d7d-278">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="b1d7d-279">Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-279">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="b1d7d-280">Testowanie aplikacji 64-bitowej (x64)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-280">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="b1d7d-281">**Bieżąca wersja**</span><span class="sxs-lookup"><span data-stu-id="b1d7d-281">**Current release**</span></span>

* <span data-ttu-id="b1d7d-282">Jeśli aplikacja jest wdrożeniem 64-bitowym (x64), [zależnym od platformy](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="b1d7d-282">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="b1d7d-283">Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="b1d7d-283">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="b1d7d-284">Jeśli aplikacja jest [wdrożeniem](/dotnet/core/deploying/#self-contained-deployments-scd)niezależnym:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-284">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="b1d7d-285">Uruchom aplikację: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="b1d7d-285">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="b1d7d-286">Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-286">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="b1d7d-287">**Wdrożenie zależne od platformy uruchomione w wersji zapoznawczej**</span><span class="sxs-lookup"><span data-stu-id="b1d7d-287">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="b1d7d-288">*Wymaga zainstalowania rozszerzenia witryny środowiska uruchomieniowego ASP.NET Core {VERSION} (x64).*</span><span class="sxs-lookup"><span data-stu-id="b1d7d-288">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="b1d7d-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` ( `{X.Y}` to wersja środowiska uruchomieniowego)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="b1d7d-290">Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="b1d7d-290">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="b1d7d-291">Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-291">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="b1d7d-292">Dziennik stdout modułu ASP.NET Core (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-292">ASP.NET Core Module stdout log (Azure App Service)</span></span>

> [!WARNING]
> <span data-ttu-id="b1d7d-293">Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-293">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="b1d7d-294">Nie ma żadnego limitu rozmiaru pliku dziennika lub liczby utworzonych plików dziennika.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-294">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="b1d7d-295">Rejestrowania stdout można używać tylko w celu rozwiązywania problemów z uruchamianiem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-295">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="b1d7d-296">Aby uzyskać ogólne rejestrowanie w aplikacji ASP.NET Core po uruchomieniu, należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-296">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="b1d7d-297">Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-297">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="b1d7d-298">Dziennik modułu ASP.NET Core stdout często rejestruje przydatne komunikaty o błędach, które nie są dostępne w dzienniku zdarzeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-298">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="b1d7d-299">Aby włączyć i wyświetlić dzienniki stdout:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-299">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="b1d7d-300">W witrynie Azure Portal przejdź do aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-300">In the Azure Portal, navigate to the web app.</span></span>
1. <span data-ttu-id="b1d7d-301">W bloku **App Service** wprowadź **kudu** w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-301">In the **App Service** blade, enter **kudu** in the search box.</span></span>
1. <span data-ttu-id="b1d7d-302">Wybierz pozycję **Narzędzia zaawansowane** > **Przejdź**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-302">Select **Advanced Tools** > **Go**.</span></span>
1. <span data-ttu-id="b1d7d-303">Wybierz  **konsolę debugowania > cmd**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-303">Select  **Debug console > CMD**.</span></span>
1. <span data-ttu-id="b1d7d-304">Przejdź do *witryny/wwwroot*</span><span class="sxs-lookup"><span data-stu-id="b1d7d-304">Navigate to *site/wwwroot*</span></span>
1. <span data-ttu-id="b1d7d-305">Wybierz ikonę ołówka, aby edytować plik *web.config* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-305">Select the pencil icon to edit the *web.config* file.</span></span>
1. <span data-ttu-id="b1d7d-306">W `<aspNetCore />` elemencie Ustaw `stdoutLogEnabled="true"` i wybierz pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-306">In the `<aspNetCore />` element, set `stdoutLogEnabled="true"` and select **Save**.</span></span>

<span data-ttu-id="b1d7d-307">Wyłącz rejestrowanie stdout, gdy Rozwiązywanie problemów zostanie zakończone przez ustawienie `stdoutLogEnabled="false"` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-307">Disable stdout logging when troubleshooting is complete by setting `stdoutLogEnabled="false"`.</span></span>

<span data-ttu-id="b1d7d-308">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-308">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="b1d7d-309">Dziennik debugowania modułu ASP.NET Core (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-309">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="b1d7d-310">Dziennik debugowania modułu ASP.NET Core zapewnia dodatkowe, dokładniejsze rejestrowanie z modułu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-310">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="b1d7d-311">Aby włączyć i wyświetlić dzienniki stdout:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-311">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="b1d7d-312">Aby włączyć Rozszerzony Dziennik diagnostyczny, wykonaj jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-312">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="b1d7d-313">Postępuj zgodnie z instrukcjami w temacie [udoskonalone dzienniki diagnostyczne](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) , aby skonfigurować aplikację do rozszerzonego rejestrowania diagnostycznego.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-313">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="b1d7d-314">Wdróż ponownie aplikację.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-314">Redeploy the app.</span></span>
   * <span data-ttu-id="b1d7d-315">Dodaj `<handlerSettings>` pokazany w [ulepszonych dziennikach diagnostycznych](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do pliku *web.config* aplikacji na żywo za pomocą konsoli kudu:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-315">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="b1d7d-316">Otwórz **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-316">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="b1d7d-317">Wybierz przycisk \*\*Przejdź &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-317">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="b1d7d-318">Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-318">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="b1d7d-319">Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-319">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="b1d7d-320">Otwórz foldery w **witrynie**Path  >  **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-320">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="b1d7d-321">Edytuj plik *web.config* , wybierając przycisk ołówka.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-321">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="b1d7d-322">Dodaj `<handlerSettings>` sekcję, jak pokazano w [udoskonalonych dziennikach diagnostycznych](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-322">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="b1d7d-323">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-323">Select the **Save** button.</span></span>
1. <span data-ttu-id="b1d7d-324">Otwórz **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-324">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="b1d7d-325">Wybierz przycisk \*\*Przejdź &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-325">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="b1d7d-326">Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-326">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="b1d7d-327">Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-327">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="b1d7d-328">Otwórz foldery w **witrynie**Path  >  **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-328">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="b1d7d-329">Jeśli nie podano ścieżki do pliku *aspnetcore-Debug. log* , plik zostanie wyświetlony na liście.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-329">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="b1d7d-330">Jeśli podano ścieżkę, przejdź do lokalizacji pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-330">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="b1d7d-331">Otwórz plik dziennika z przyciskiem ołówek obok nazwy pliku.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-331">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="b1d7d-332">Wyłącz rejestrowanie debugowania po zakończeniu rozwiązywania problemów:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-332">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="b1d7d-333">Aby wyłączyć rozszerzony Dziennik debugowania, wykonaj jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-333">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="b1d7d-334">Usuń `<handlerSettings>` plik z pliku *web.config* lokalnie i Wdróż ponownie aplikację.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-334">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="b1d7d-335">Za pomocą konsoli kudu Edytuj plik *web.config* i Usuń `<handlerSettings>` sekcję.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-335">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="b1d7d-336">Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-336">Save the file.</span></span>

<span data-ttu-id="b1d7d-337">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-337">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="b1d7d-338">Niepowodzenie wyłączenia dziennika debugowania może prowadzić do awarii aplikacji lub serwera.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-338">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="b1d7d-339">Nie ma limitu rozmiaru pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-339">There's no limit on log file size.</span></span> <span data-ttu-id="b1d7d-340">Funkcja rejestrowania debugowania służy tylko do rozwiązywania problemów z uruchamianiem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-340">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="b1d7d-341">Aby uzyskać ogólne rejestrowanie w aplikacji ASP.NET Core po uruchomieniu, należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-341">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="b1d7d-342">Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-342">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="b1d7d-343">Aplikacja wolna lub wysunięta (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-343">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="b1d7d-344">Gdy aplikacja reaguje powoli lub zawiesza się na żądanie, zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-344">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="b1d7d-345">Rozwiązywanie problemów związanych z niską wydajnością aplikacji internetowych w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b1d7d-345">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="b1d7d-346">Użyj rozszerzenia witryny diagnostyki awarii, aby przechwycić zrzut dla sporadycznych problemów z wyjątkami lub problemów z wydajnością w usłudze Azure Web App</span><span class="sxs-lookup"><span data-stu-id="b1d7d-346">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="b1d7d-347">Bloki monitorowania</span><span class="sxs-lookup"><span data-stu-id="b1d7d-347">Monitoring blades</span></span>

<span data-ttu-id="b1d7d-348">Bloki monitorowania zapewniają alternatywne środowisko rozwiązywania problemów z metodami opisanymi wcześniej w temacie.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-348">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="b1d7d-349">Te bloki mogą służyć do diagnozowania błędów serii 500.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-349">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="b1d7d-350">Upewnij się, że rozszerzenia ASP.NET Core są zainstalowane.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-350">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="b1d7d-351">Jeśli rozszerzenia nie są zainstalowane, zainstaluj je ręcznie:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-351">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="b1d7d-352">W sekcji blok **narzędzi programistycznych** wybierz blok **rozszerzenia** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-352">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="b1d7d-353">**Rozszerzenia ASP.NET Core** powinny znajdować się na liście.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-353">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="b1d7d-354">Jeśli rozszerzenia nie są zainstalowane, wybierz przycisk **Dodaj** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-354">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="b1d7d-355">Wybierz z listy **rozszerzenia ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-355">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="b1d7d-356">Wybierz **przycisk OK** , aby zaakceptować postanowienia prawne.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-356">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="b1d7d-357">W bloku **Dodaj rozszerzenie** wybierz pozycję **OK** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-357">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="b1d7d-358">Komunikat podręczny informujący o pomyślnym zainstalowaniu rozszerzeń.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-358">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="b1d7d-359">Jeśli rejestrowanie stdout nie jest włączone, wykonaj następujące kroki:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-359">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="b1d7d-360">W Azure Portal wybierz blok **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-360">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="b1d7d-361">Wybierz przycisk \*\*Przejdź &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-361">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="b1d7d-362">Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-362">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="b1d7d-363">Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-363">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="b1d7d-364">Otwórz foldery w **witrynie** Path > **wwwroot** i przewiń w dół, aby odsłonić *web.config* plik w dolnej części listy.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-364">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="b1d7d-365">Kliknij ikonę ołówka obok pliku *web.config* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-365">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="b1d7d-366">Ustaw wartość **stdoutLogEnabled** na `true` i zmień ścieżkę **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-366">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="b1d7d-367">Wybierz pozycję **Zapisz** , aby zapisać zaktualizowany plik *web.config* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-367">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="b1d7d-368">Wykonaj aktywację rejestrowania diagnostycznego:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-368">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="b1d7d-369">W Azure Portal wybierz blok **dzienników diagnostycznych** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-369">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="b1d7d-370">Wybierz pozycję **Włącz** , aby włączyć **Rejestrowanie aplikacji (system plików)** i **szczegółowe komunikaty o błędach**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-370">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="b1d7d-371">Wybierz przycisk **Zapisz** znajdujący się u góry bloku.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-371">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="b1d7d-372">Aby uwzględnić śledzenie nieudanych żądań, znane także jako rejestrowanie nieudanych żądań buforowania zdarzeń (FREB), wybierz **przełącznik dla** **śledzenia nieudanych żądań**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-372">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="b1d7d-373">Wybierz blok **strumień dziennika** , który jest wyświetlany bezpośrednio w bloku **dzienników diagnostycznych** w portalu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-373">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="b1d7d-374">Utwórz żądanie do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-374">Make a request to the app.</span></span>
1. <span data-ttu-id="b1d7d-375">W danych strumienia dziennika jest wskazywana Przyczyna błędu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-375">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="b1d7d-376">Należy pamiętać o wyłączeniu rejestrowania stdout po zakończeniu rozwiązywania problemów.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-376">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="b1d7d-377">Aby wyświetlić dzienniki śledzenia niepomyślnych żądań (dzienniki FREB):</span><span class="sxs-lookup"><span data-stu-id="b1d7d-377">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="b1d7d-378">Przejdź do bloku **diagnozowanie i rozwiązywanie problemów** w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-378">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="b1d7d-379">Wybierz pozycję **dzienniki śledzenia niepomyślnych żądań** z obszaru **Narzędzia obsługi** na pasku bocznym.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-379">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="b1d7d-380">Zobacz [sekcję śledzenie niepomyślnych żądań w temacie Włączanie rejestrowania diagnostyki dla aplikacji sieci Web w programie Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) i informacje [o wydajności aplikacji dotyczące Web Apps na platformie Azure: Jak mogę włączyć śledzenie nieudanych żądań?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) Aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-380">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="b1d7d-381">Aby uzyskać więcej informacji, zobacz [Włączanie rejestrowania diagnostycznego dla aplikacji sieci Web w Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-381">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="b1d7d-382">Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-382">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="b1d7d-383">Nie ma żadnego limitu rozmiaru pliku dziennika lub liczby utworzonych plików dziennika.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-383">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="b1d7d-384">Aby rejestrować procedury w aplikacji ASP.NET Core, użyj biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-384">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="b1d7d-385">Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-385">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="b1d7d-386">Rozwiązywanie problemów w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="b1d7d-386">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="b1d7d-387">Dziennik zdarzeń aplikacji (IIS)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-387">Application Event Log (IIS)</span></span>

<span data-ttu-id="b1d7d-388">Dostęp do dziennika zdarzeń aplikacji:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-388">Access the Application Event Log:</span></span>

1. <span data-ttu-id="b1d7d-389">Otwórz menu Start, wyszukaj ciąg *Podgląd zdarzeń*i wybierz aplikację **Podgląd zdarzeń** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-389">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="b1d7d-390">W **Podgląd zdarzeń**Otwórz węzeł **Dzienniki systemu Windows** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-390">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="b1d7d-391">Wybierz pozycję **aplikacja** , aby otworzyć dziennik zdarzeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-391">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="b1d7d-392">Wyszukaj błędy związane z niepowodzeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-392">Search for errors associated with the failing app.</span></span> <span data-ttu-id="b1d7d-393">Błędy mają wartość *modułu AspNetCore IIS* lub *moduł IIS Express AspNetCore* w kolumnie *Źródło* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-393">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="b1d7d-394">Uruchamianie aplikacji w wierszu polecenia</span><span class="sxs-lookup"><span data-stu-id="b1d7d-394">Run the app at a command prompt</span></span>

<span data-ttu-id="b1d7d-395">Wiele błędów uruchamiania nie tworzy użytecznych informacji w dzienniku zdarzeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-395">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="b1d7d-396">Przyczynę niektórych błędów można znaleźć, uruchamiając aplikację w wierszu polecenia w systemie hostingu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-396">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="b1d7d-397">Wdrożenie zależne od platformy</span><span class="sxs-lookup"><span data-stu-id="b1d7d-397">Framework-dependent deployment</span></span>

<span data-ttu-id="b1d7d-398">Jeśli aplikacja jest [wdrożeniem zależnym od platformy](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="b1d7d-398">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="b1d7d-399">W wierszu polecenia przejdź do folderu wdrożenia i uruchom aplikację, wykonując zestaw aplikacji za pomocą *dotnet.exe*.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-399">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="b1d7d-400">W poniższym poleceniu Zastąp nazwę zestawu aplikacji dla \<assembly_name> : `dotnet .\<assembly_name>.dll` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-400">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="b1d7d-401">Dane wyjściowe konsoli z aplikacji, pokazujące błędy, są zapisywane w oknie konsoli.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-401">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="b1d7d-402">Jeśli wystąpią błędy podczas wysyłania żądania do aplikacji, należy wysłać żądanie do hosta i portu, gdzie Kestrel nasłuchuje.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-402">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="b1d7d-403">Przy użyciu domyślnego hosta i wpisu Wyślij żądanie do `http://localhost:5000/` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-403">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="b1d7d-404">Jeśli aplikacja będzie odpowiadać zwykle na adres punktu końcowego Kestrel, problem jest bardziej prawdopodobnie związany z konfiguracją hostingu i mniej korzystnie w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-404">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="b1d7d-405">Wdrażanie samodzielne</span><span class="sxs-lookup"><span data-stu-id="b1d7d-405">Self-contained deployment</span></span>

<span data-ttu-id="b1d7d-406">Jeśli aplikacja jest [wdrożeniem](/dotnet/core/deploying/#self-contained-deployments-scd)niezależnym:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-406">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="b1d7d-407">W wierszu polecenia przejdź do folderu wdrożenia i uruchom plik wykonywalny aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-407">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="b1d7d-408">W poniższym poleceniu Zastąp nazwę zestawu aplikacji dla \<assembly_name> : `<assembly_name>.exe` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-408">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="b1d7d-409">Dane wyjściowe konsoli z aplikacji, pokazujące błędy, są zapisywane w oknie konsoli.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-409">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="b1d7d-410">Jeśli wystąpią błędy podczas wysyłania żądania do aplikacji, należy wysłać żądanie do hosta i portu, gdzie Kestrel nasłuchuje.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-410">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="b1d7d-411">Przy użyciu domyślnego hosta i wpisu Wyślij żądanie do `http://localhost:5000/` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-411">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="b1d7d-412">Jeśli aplikacja będzie odpowiadać zwykle na adres punktu końcowego Kestrel, problem jest bardziej prawdopodobnie związany z konfiguracją hostingu i mniej korzystnie w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-412">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="b1d7d-413">Dziennik stdout modułu ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-413">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="b1d7d-414">Aby włączyć i wyświetlić dzienniki stdout:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-414">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="b1d7d-415">Przejdź do folderu wdrożenia lokacji w systemie hostingu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-415">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="b1d7d-416">Jeśli folder *Logs* nie istnieje, utwórz go.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-416">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="b1d7d-417">Aby uzyskać instrukcje dotyczące włączania programu MSBuild do automatycznego tworzenia folderu *Logs* w ramach wdrożenia, zobacz temat [Struktura katalogów](xref:host-and-deploy/directory-structure) .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-417">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="b1d7d-418">Edytuj plik *web.config* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-418">Edit the *web.config* file.</span></span> <span data-ttu-id="b1d7d-419">Ustaw wartość **stdoutLogEnabled** na `true` i zmień ścieżkę **stdoutLogFile** , tak aby wskazywała folder *Logs* (na przykład `.\logs\stdout` ).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-419">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="b1d7d-420">`stdout` ścieżka jest prefiksem nazwy pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-420">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="b1d7d-421">Sygnatura czasowa, identyfikator procesu i rozszerzenie pliku są dodawane automatycznie podczas tworzenia dziennika.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-421">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="b1d7d-422">Używając `stdout` jako prefiksu nazwy pliku, typowy plik dziennika ma nazwę *stdout_20180205184032_5412. log*.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-422">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="b1d7d-423">Upewnij się, że tożsamość puli aplikacji ma uprawnienia do zapisu w folderze *Logs* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-423">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="b1d7d-424">Zapisz zaktualizowany plik *web.config* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-424">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="b1d7d-425">Utwórz żądanie do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-425">Make a request to the app.</span></span>
1. <span data-ttu-id="b1d7d-426">Przejdź do folderu *Logs* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-426">Navigate to the *logs* folder.</span></span> <span data-ttu-id="b1d7d-427">Znajdź i Otwórz najnowszy dziennik stdout.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-427">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="b1d7d-428">Zbadaj dziennik pod kątem błędów.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-428">Study the log for errors.</span></span>

<span data-ttu-id="b1d7d-429">Wyłącz rejestrowanie stdout po zakończeniu rozwiązywania problemów:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-429">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="b1d7d-430">Edytuj plik *web.config* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-430">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="b1d7d-431">Ustaw wartość **stdoutLogEnabled** na `false` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-431">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="b1d7d-432">Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-432">Save the file.</span></span>

<span data-ttu-id="b1d7d-433">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-433">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="b1d7d-434">Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-434">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="b1d7d-435">Nie ma żadnego limitu rozmiaru pliku dziennika lub liczby utworzonych plików dziennika.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-435">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="b1d7d-436">Aby rejestrować procedury w aplikacji ASP.NET Core, użyj biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-436">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="b1d7d-437">Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-437">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="b1d7d-438">Dziennik debugowania modułu ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-438">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="b1d7d-439">Dodaj następujące ustawienia programu obsługi do pliku *web.config* aplikacji, aby włączyć Dziennik debugowania modułu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-439">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="b1d7d-440">Upewnij się, że ścieżka określona dla dziennika istnieje i że tożsamość puli aplikacji ma uprawnienia do zapisu w tej lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-440">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="b1d7d-441">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-441">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="b1d7d-442">Włącz stronę wyjątków dla deweloperów</span><span class="sxs-lookup"><span data-stu-id="b1d7d-442">Enable the Developer Exception Page</span></span>

<span data-ttu-id="b1d7d-443">`ASPNETCORE_ENVIRONMENT` [Zmienną środowiskową można dodać do web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) , aby uruchomić aplikację w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-443">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="b1d7d-444">O ile środowisko nie zostanie zastąpione podczas uruchamiania aplikacji przez `UseEnvironment` konstruktora hosta, ustawienie zmiennej środowiskowej umożliwia wyświetlenie [strony wyjątku dewelopera](xref:fundamentals/error-handling) po uruchomieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-444">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

<span data-ttu-id="b1d7d-445">Ustawienie zmiennej środowiskowej dla programu `ASPNETCORE_ENVIRONMENT` jest zalecane wyłącznie do użytku na przejściowych i testowych serwerach, które nie są dostępne w Internecie.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-445">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="b1d7d-446">Usuń zmienną środowiskową z pliku *web.config* po rozwiązaniu problemu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-446">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="b1d7d-447">Aby uzyskać informacje na temat ustawiania zmiennych środowiskowych w *web.config*, zobacz [EnvironmentVariables podrzędny element aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-447">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="b1d7d-448">Uzyskiwanie danych z aplikacji</span><span class="sxs-lookup"><span data-stu-id="b1d7d-448">Obtain data from an app</span></span>

<span data-ttu-id="b1d7d-449">Jeśli aplikacja może odpowiadać na żądania, uzyskiwać żądania, połączenia i dodatkowe dane z aplikacji przy użyciu wbudowanego oprogramowania terminala.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-449">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="b1d7d-450">Aby uzyskać więcej informacji i przykładowy kod, zobacz <xref:test/troubleshoot#obtain-data-from-an-app> .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-450">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="b1d7d-451">Aplikacja wolna lub wysunięta (IIS)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-451">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="b1d7d-452">*Zrzut awaryjny* to migawka pamięci systemu, która może pomóc w ustaleniu przyczyny awarii aplikacji, awarii uruchamiania lub powolnej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-452">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="b1d7d-453">Awaria aplikacji lub napotka wyjątek</span><span class="sxs-lookup"><span data-stu-id="b1d7d-453">App crashes or encounters an exception</span></span>

<span data-ttu-id="b1d7d-454">Uzyskaj i Analizuj Zrzut z [raportowanie błędów systemu Windows (raportowanie błędów systemu Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="b1d7d-454">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="b1d7d-455">Utwórz folder do przechowywania plików zrzutu awaryjnego w `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-455">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="b1d7d-456">Pula aplikacji musi mieć dostęp do zapisu w folderze.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-456">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="b1d7d-457">Uruchom [skrypt programu PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)w programie EnableDumps:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-457">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="b1d7d-458">Jeśli aplikacja korzysta z [modelu hostingu w procesie](xref:host-and-deploy/iis/index#in-process-hosting-model), uruchom skrypt dla *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-458">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="b1d7d-459">Jeśli aplikacja korzysta z [modelu hostingu poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), uruchom skrypt dla *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-459">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="b1d7d-460">Uruchom aplikację w warunkach, które powodują awarię.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-460">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="b1d7d-461">Po wystąpieniu awarii Uruchom [skrypt programu DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="b1d7d-461">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="b1d7d-462">Jeśli aplikacja korzysta z [modelu hostingu w procesie](xref:host-and-deploy/iis/index#in-process-hosting-model), uruchom skrypt dla *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-462">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="b1d7d-463">Jeśli aplikacja korzysta z [modelu hostingu poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), uruchom skrypt dla *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-463">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="b1d7d-464">Po awarii aplikacji i zakończeniu zbierania zrzutów aplikacja może zakończyć normalne działanie.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-464">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="b1d7d-465">Skrypt programu PowerShell konfiguruje raportowanie błędów systemu Windows w celu zebrania do pięciu zrzutów na aplikację.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-465">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="b1d7d-466">Zrzuty awaryjne mogą wymagać dużej ilości miejsca na dysku (do kilku gigabajtów).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-466">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="b1d7d-467">Aplikacja zawiesza się, kończy się niepowodzeniem podczas uruchamiania lub działa normalnie</span><span class="sxs-lookup"><span data-stu-id="b1d7d-467">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="b1d7d-468">Gdy aplikacja *zawiesza* się (bez awarii), kończy się niepowodzeniem podczas uruchamiania lub działa normalnie, zobacz [pliki zrzutu w trybie użytkownika: wybór najlepszego narzędzia](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) do wygenerowania zrzutu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-468">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="b1d7d-469">Analizowanie zrzutu</span><span class="sxs-lookup"><span data-stu-id="b1d7d-469">Analyze the dump</span></span>

<span data-ttu-id="b1d7d-470">Zrzut można analizować przy użyciu kilku metod.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-470">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="b1d7d-471">Aby uzyskać więcej informacji, zobacz [Analizowanie pliku zrzutu w trybie użytkownika](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-471">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="b1d7d-472">Wyczyść pamięć podręczną pakietów</span><span class="sxs-lookup"><span data-stu-id="b1d7d-472">Clear package caches</span></span>

<span data-ttu-id="b1d7d-473">Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestaw .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w ramach aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-473">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="b1d7d-474">W niektórych przypadkach niespójne pakiety mogą przerwać aplikację podczas przeprowadzania uaktualnień głównych.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-474">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="b1d7d-475">Większość tych problemów można naprawić, wykonując następujące instrukcje:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-475">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="b1d7d-476">Usuń foldery *bin* i *obj* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-476">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="b1d7d-477">Wyczyść pamięć podręczną pakietów, wykonując [wszystkie elementy lokalne usługi NuGet programu dotnet--Wyczyść](/dotnet/core/tools/dotnet-nuget-locals) z poziomu powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-477">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="b1d7d-478">Czyszczenie pamięci podręcznych pakietów można także wykonać przy użyciu narzędzia [nuget.exe](https://www.nuget.org/downloads) i wykonując polecenie `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-478">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="b1d7d-479">*nuget.exe* nie jest instalacją w pakiecie z systemem operacyjnym Windows dla komputerów stacjonarnych i należy ją pobrać niezależnie od [witryny sieci Web programu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-479">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="b1d7d-480">Przywróć i skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-480">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="b1d7d-481">Usuń wszystkie pliki z folderu wdrożenia na serwerze przed ponownym wdrożeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-481">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b1d7d-482">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="b1d7d-482">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="b1d7d-483">Dokumentacja platformy Azure</span><span class="sxs-lookup"><span data-stu-id="b1d7d-483">Azure documentation</span></span>

* [<span data-ttu-id="b1d7d-484">Usługa Application Insights dla platformy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b1d7d-484">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="b1d7d-485">Sekcja zdalne debugowanie aplikacji sieci Web Rozwiązywanie problemów z aplikacją sieci Web w Azure App Service przy użyciu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1d7d-485">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="b1d7d-486">Omówienie diagnostyki Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b1d7d-486">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="b1d7d-487">Jak monitorować aplikacje w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b1d7d-487">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="b1d7d-488">Rozwiązywanie problemów z aplikacją sieci Web w Azure App Service przy użyciu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1d7d-488">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="b1d7d-489">Rozwiązywanie problemów z błędami HTTP "502 złej Gateway" i "503 Usługa niedostępna" w usłudze Azure Web Apps</span><span class="sxs-lookup"><span data-stu-id="b1d7d-489">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="b1d7d-490">Rozwiązywanie problemów związanych z niską wydajnością aplikacji internetowych w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b1d7d-490">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="b1d7d-491">Często zadawane pytania dotyczące wydajności aplikacji dla Web Apps na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="b1d7d-491">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="b1d7d-492">Piaskownica usługi Azure Web App (ograniczenia wykonywania App Service Runtime)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-492">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="b1d7d-493">Piątek Azure: Azure App Service środowisko diagnostyczne i rozwiązywania problemów (wideo 12-minutowy)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-493">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="b1d7d-494">Dokumentacja programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1d7d-494">Visual Studio documentation</span></span>

* [<span data-ttu-id="b1d7d-495">ASP.NET Core debugowania zdalnego na serwerze IIS na platformie Azure w programie Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="b1d7d-495">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="b1d7d-496">Zdalne debugowanie ASP.NET Core na zdalnym komputerze IIS w programie Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="b1d7d-496">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="b1d7d-497">Nauka debugowania przy użyciu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1d7d-497">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="b1d7d-498">Dokumentacja Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b1d7d-498">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="b1d7d-499">Debugowanie za pomocą Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b1d7d-499">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="b1d7d-500">Ten artykuł zawiera informacje dotyczące typowych błędów uruchamiania aplikacji oraz instrukcje dotyczące sposobu diagnozowania błędów podczas wdrażania aplikacji w usłudze Azure App Service lub IIS:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-500">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="b1d7d-501">Błędy uruchamiania aplikacji</span><span class="sxs-lookup"><span data-stu-id="b1d7d-501">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="b1d7d-502">Objaśnia typowe scenariusze uruchamiania kodu stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-502">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="b1d7d-503">Rozwiązywanie problemów dotyczących Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b1d7d-503">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="b1d7d-504">Zawiera porady dotyczące rozwiązywania problemów z aplikacjami wdrożonymi w celu Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-504">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="b1d7d-505">Rozwiązywanie problemów w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="b1d7d-505">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="b1d7d-506">Zawiera porady dotyczące rozwiązywania problemów z aplikacjami wdrożonymi w usługach IIS lub lokalnie uruchomionymi IIS Express.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-506">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="b1d7d-507">Wskazówki dotyczą zarówno wdrożeń systemu Windows Server, jak i pulpitu systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-507">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="b1d7d-508">Wyczyść pamięć podręczną pakietów</span><span class="sxs-lookup"><span data-stu-id="b1d7d-508">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="b1d7d-509">Wyjaśnia, co należy zrobić, gdy niespójne pakiety przerywają działanie aplikacji podczas przeprowadzania uaktualnień głównych lub zmiany wersji pakietu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-509">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="b1d7d-510">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="b1d7d-510">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="b1d7d-511">Wyświetla listę dodatkowych tematów dotyczących rozwiązywania problemów.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-511">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="b1d7d-512">Błędy uruchamiania aplikacji</span><span class="sxs-lookup"><span data-stu-id="b1d7d-512">App startup errors</span></span>

<span data-ttu-id="b1d7d-513">W programie Visual Studio ASP.NET Core Project domyślnie [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting podczas debugowania.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-513">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="b1d7d-514">*502,5 — błąd procesu* lub *błąd uruchomienia 500,30* , który występuje, gdy debugowanie lokalne można zdiagnozować przy użyciu porady w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-514">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="b1d7d-515">403,14 zabronione</span><span class="sxs-lookup"><span data-stu-id="b1d7d-515">403.14 Forbidden</span></span>

<span data-ttu-id="b1d7d-516">Nie można uruchomić aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-516">The app fails to start.</span></span> <span data-ttu-id="b1d7d-517">Rejestrowany jest następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-517">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="b1d7d-518">Ten błąd jest zwykle spowodowany przez uszkodzone wdrożenie w systemie hostingu, który obejmuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-518">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="b1d7d-519">Aplikacja jest wdrażana w niewłaściwym folderze w systemie hostingu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-519">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="b1d7d-520">W procesie wdrażania nie powiodło się przeniesienie wszystkich plików i folderów aplikacji do folderu wdrożenia w systemie hostingu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-520">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="b1d7d-521">W wdrożeniu brakuje pliku *web.config* lub zawartość pliku *web.config* jest źle sformułowana.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-521">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="b1d7d-522">Wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-522">Perform the following steps:</span></span>

1. <span data-ttu-id="b1d7d-523">Usuń wszystkie pliki i foldery z folderu wdrożenia w systemie hostingu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-523">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="b1d7d-524">Wdróż ponownie zawartość folderu *publikowania* aplikacji w systemie hostingu przy użyciu zwykłej metody wdrażania, takiej jak Visual Studio, PowerShell lub wdrażanie ręczne:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-524">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="b1d7d-525">Upewnij się, że plik *web.config* jest obecny we wdrożeniu i że jego zawartość jest poprawna.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-525">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="b1d7d-526">Podczas hostowania w Azure App Service upewnij się, że aplikacja została wdrożona w `D:\home\site\wwwroot` folderze.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-526">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="b1d7d-527">Jeśli aplikacja jest hostowana przez usługi IIS, upewnij się, że aplikacja jest wdrożona w **ścieżce fizycznej** usług IIS pokazanej w **ustawieniach podstawowych**w **Menedżerze usług IIS**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-527">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="b1d7d-528">Upewnij się, że wszystkie pliki i foldery aplikacji zostały wdrożone, porównując wdrożenie w systemie hostingu z zawartością folderu *publikowania* projektu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-528">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="b1d7d-529">Aby uzyskać więcej informacji na temat układu opublikowanej aplikacji ASP.NET Core, zobacz <xref:host-and-deploy/directory-structure> .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-529">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="b1d7d-530">Aby uzyskać więcej informacji na temat pliku *web.config* , zobacz <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-530">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="b1d7d-531">500 Wewnętrzny błąd serwera</span><span class="sxs-lookup"><span data-stu-id="b1d7d-531">500 Internal Server Error</span></span>

<span data-ttu-id="b1d7d-532">Aplikacja zostanie uruchomiona, ale błąd uniemożliwia serwerowi spełnienie żądania.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-532">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="b1d7d-533">Ten błąd występuje w kodzie aplikacji podczas uruchamiania lub podczas tworzenia odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-533">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="b1d7d-534">Odpowiedź może nie zawierać żadnej zawartości lub odpowiedź może pojawić się w przeglądarce jako *błąd wewnętrzny serwera 500* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-534">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="b1d7d-535">Dziennik zdarzeń aplikacji zwykle stwierdza, że aplikacja została uruchomiona normalnie.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-535">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="b1d7d-536">Z perspektywy serwera jest to poprawne.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-536">From the server's perspective, that's correct.</span></span> <span data-ttu-id="b1d7d-537">Aplikacja została uruchomiona, ale nie może wygenerować prawidłowej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-537">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="b1d7d-538">Uruchom aplikację w wierszu polecenia na serwerze lub Włącz dziennik stdout modułu ASP.NET Core, aby rozwiązać problem.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-538">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="b1d7d-539">500,0 błąd ładowania procedury obsługi w procesie</span><span class="sxs-lookup"><span data-stu-id="b1d7d-539">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="b1d7d-540">Proces roboczy kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-540">The worker process fails.</span></span> <span data-ttu-id="b1d7d-541">Aplikacja nie zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-541">The app doesn't start.</span></span>

<span data-ttu-id="b1d7d-542">[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) nie może znaleźć platformy .NET Core CLR i znaleźć procedury obsługi żądań w procesie (*aspnetcorev2_inprocess.dll*).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-542">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="b1d7d-543">Sprawdź, czy:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-543">Check that:</span></span>

* <span data-ttu-id="b1d7d-544">Aplikacja jest przeznaczona dla pakietu NuGet [Microsoft. AspNetCore. Server. IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) lub [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-544">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="b1d7d-545">Wersja ASP.NET Core udostępnionej platformy, do której aplikacja jest zainstalowana na maszynie docelowej.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-545">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="b1d7d-546">500,0 błąd ładowania procedury obsługi poza procesem</span><span class="sxs-lookup"><span data-stu-id="b1d7d-546">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="b1d7d-547">Proces roboczy kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-547">The worker process fails.</span></span> <span data-ttu-id="b1d7d-548">Aplikacja nie zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-548">The app doesn't start.</span></span>

<span data-ttu-id="b1d7d-549">[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) nie może odnaleźć procedury obsługi żądania hostingu poza procesem.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-549">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="b1d7d-550">Upewnij się, że *aspnetcorev2_outofprocess.dll* znajduje się w podfolderze obok *aspnetcorev2.dll*.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-550">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="b1d7d-551">502.5 Błąd procesu</span><span class="sxs-lookup"><span data-stu-id="b1d7d-551">502.5 Process Failure</span></span>

<span data-ttu-id="b1d7d-552">Proces roboczy kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-552">The worker process fails.</span></span> <span data-ttu-id="b1d7d-553">Aplikacja nie zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-553">The app doesn't start.</span></span>

<span data-ttu-id="b1d7d-554">[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) próbuje uruchomić proces roboczy, ale jego uruchomienie nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-554">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="b1d7d-555">Przyczyna niepowodzenia uruchomienia procesu zwykle można ustalić na podstawie wpisów w dzienniku zdarzeń aplikacji i dzienniku modułu ASP.NET Core stdout.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-555">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="b1d7d-556">Typowym warunkiem niepowodzenia jest to, że aplikacja jest nieprawidłowo skonfigurowana z powodu docelowej wersji ASP.NET Core udostępnionej platformy, która nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-556">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="b1d7d-557">Sprawdź, które wersje ASP.NET Core udostępnionej platformy są zainstalowane na maszynie docelowej.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-557">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="b1d7d-558">*Platforma udostępniona* jest zestawem zestawów (plików*dll* ), które są zainstalowane na maszynie i do których odwołuje się pakiet `Microsoft.AspNetCore.App` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-558">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="b1d7d-559">Odwołanie do pakietu nie może określać minimalnej wymaganej wersji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-559">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="b1d7d-560">Aby uzyskać więcej informacji, zobacz [udostępnioną strukturę](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-560">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="b1d7d-561">Strona błędu *niepowodzenia procesu 502,5* jest zwracana w przypadku niepowodzenia konfiguracji hostingu lub aplikacji. powoduje to niepowodzenie procesu roboczego:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-561">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="b1d7d-562">Nie można uruchomić aplikacji (ErrorCode "0x800700C1")</span><span class="sxs-lookup"><span data-stu-id="b1d7d-562">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="b1d7d-563">Nie można uruchomić aplikacji, ponieważ nie można załadować zestawu aplikacji (*dll*).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-563">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="b1d7d-564">Ten błąd występuje, gdy wystąpiła niezgodność między opublikowaną aplikacją a procesem w3wp/iisexpress.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-564">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="b1d7d-565">Upewnij się, że ustawienie 32-bitowe puli aplikacji jest poprawne:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-565">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="b1d7d-566">Wybierz pulę aplikacji w **pulach aplikacji**Menedżera usług IIS.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-566">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="b1d7d-567">Wybierz pozycję **Ustawienia zaawansowane** w obszarze **Edytuj pulę aplikacji** w panelu **Akcje** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-567">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="b1d7d-568">Ustaw **opcję Włącz aplikacje 32-bitowe**:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-568">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="b1d7d-569">W przypadku wdrażania aplikacji 32-bitowej (x86) ustaw wartość na `True` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-569">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="b1d7d-570">W przypadku wdrażania aplikacji 64-bitowej (x64) ustaw wartość na `False` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-570">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="b1d7d-571">Upewnij się, że nie występuje konflikt między `<Platform>` właściwością programu MSBuild w pliku projektu a opublikowaną bitową w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-571">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="b1d7d-572">Resetowanie połączenia</span><span class="sxs-lookup"><span data-stu-id="b1d7d-572">Connection reset</span></span>

<span data-ttu-id="b1d7d-573">Jeśli wystąpi błąd po wysłaniu nagłówków, jest zbyt późno, aby serwer mógł wysłać **błąd wewnętrzny serwera 500** w przypadku wystąpienia błędu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-573">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="b1d7d-574">Zdarza się to często w przypadku wystąpienia błędu podczas serializacji obiektów złożonych dla odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-574">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="b1d7d-575">Ten typ błędu pojawia się jako błąd *resetowania połączenia* na kliencie.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-575">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="b1d7d-576">[Rejestrowanie aplikacji](xref:fundamentals/logging/index) może pomóc w rozwiązywaniu tego typu błędów.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-576">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="b1d7d-577">Domyślne limity uruchamiania</span><span class="sxs-lookup"><span data-stu-id="b1d7d-577">Default startup limits</span></span>

<span data-ttu-id="b1d7d-578">[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) jest skonfigurowany z domyślną *startupTimeLimitą* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-578">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="b1d7d-579">Po lewej stronie wartości domyślnej, uruchomienie aplikacji może trwać do dwóch minut przed zarejestrowaniem awarii procesu przez moduł.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-579">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="b1d7d-580">Aby uzyskać informacje na temat konfigurowania modułu, zobacz [atrybuty elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-580">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="b1d7d-581">Rozwiązywanie problemów dotyczących Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b1d7d-581">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="b1d7d-582">Dziennik zdarzeń aplikacji (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-582">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="b1d7d-583">Aby uzyskać dostęp do dziennika zdarzeń aplikacji, użyj bloku **diagnozowanie i rozwiązywanie problemów** w Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-583">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="b1d7d-584">W Azure Portal Otwórz aplikację w **App Services**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-584">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="b1d7d-585">Kliknij pozycję **Diagnozowanie i rozwiązywanie problemów**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-585">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="b1d7d-586">Wybierz nagłówek **Narzędzia diagnostyczne** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-586">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="b1d7d-587">W obszarze **Narzędzia obsługi**wybierz przycisk **zdarzenia aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-587">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="b1d7d-588">Zapoznaj się z najnowszym błędem podanym w pozycji *AspNetCoreModule IIS* lub *IIS AspNetCoreModule v2* w kolumnie **Źródło** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-588">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="b1d7d-589">Alternatywą dla korzystania z bloku **diagnozowanie i rozwiązywanie problemów** jest przetestowanie pliku dziennika zdarzeń aplikacji bezpośrednio przy użyciu [kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="b1d7d-589">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="b1d7d-590">Otwórz **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-590">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="b1d7d-591">Wybierz przycisk \*\*Przejdź &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-591">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="b1d7d-592">Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-592">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="b1d7d-593">Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-593">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="b1d7d-594">Otwórz folder **LogFiles** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-594">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="b1d7d-595">Wybierz ikonę ołówka obok pliku *eventlog.xml* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-595">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="b1d7d-596">Przejrzyj dziennik.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-596">Examine the log.</span></span> <span data-ttu-id="b1d7d-597">Przewiń w dół dziennika, aby zobaczyć najnowsze zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-597">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="b1d7d-598">Uruchamianie aplikacji w konsoli kudu</span><span class="sxs-lookup"><span data-stu-id="b1d7d-598">Run the app in the Kudu console</span></span>

<span data-ttu-id="b1d7d-599">Wiele błędów uruchamiania nie tworzy użytecznych informacji w dzienniku zdarzeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-599">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="b1d7d-600">Możesz uruchomić aplikację w konsoli zdalnego wykonywania [kudu](https://github.com/projectkudu/kudu/wiki) , aby wykryć błąd:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-600">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="b1d7d-601">Otwórz **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-601">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="b1d7d-602">Wybierz przycisk \*\*Przejdź &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-602">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="b1d7d-603">Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-603">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="b1d7d-604">Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-604">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="b1d7d-605">Testowanie aplikacji 32-bitowej (x86)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-605">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="b1d7d-606">**Bieżąca wersja**</span><span class="sxs-lookup"><span data-stu-id="b1d7d-606">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="b1d7d-607">Uruchom aplikację: </span><span class="sxs-lookup"><span data-stu-id="b1d7d-607">Run the app:</span></span>
   * <span data-ttu-id="b1d7d-608">Jeśli aplikacja jest [wdrożeniem zależnym od platformy](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="b1d7d-608">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="b1d7d-609">Jeśli aplikacja jest [wdrożeniem](/dotnet/core/deploying/#self-contained-deployments-scd)niezależnym:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-609">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="b1d7d-610">Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-610">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="b1d7d-611">**Wdrożenie zależne od platformy uruchomione w wersji zapoznawczej**</span><span class="sxs-lookup"><span data-stu-id="b1d7d-611">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="b1d7d-612">*Wymaga zainstalowania rozszerzenia witryny środowiska uruchomieniowego ASP.NET Core {VERSION} (x86).*</span><span class="sxs-lookup"><span data-stu-id="b1d7d-612">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="b1d7d-613">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` ( `{X.Y}` to wersja środowiska uruchomieniowego)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-613">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="b1d7d-614">Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="b1d7d-614">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="b1d7d-615">Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-615">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="b1d7d-616">Testowanie aplikacji 64-bitowej (x64)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-616">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="b1d7d-617">**Bieżąca wersja**</span><span class="sxs-lookup"><span data-stu-id="b1d7d-617">**Current release**</span></span>

* <span data-ttu-id="b1d7d-618">Jeśli aplikacja jest wdrożeniem 64-bitowym (x64), [zależnym od platformy](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="b1d7d-618">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="b1d7d-619">Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="b1d7d-619">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="b1d7d-620">Jeśli aplikacja jest [wdrożeniem](/dotnet/core/deploying/#self-contained-deployments-scd)niezależnym:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-620">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="b1d7d-621">Uruchom aplikację: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="b1d7d-621">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="b1d7d-622">Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-622">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="b1d7d-623">**Wdrożenie zależne od platformy uruchomione w wersji zapoznawczej**</span><span class="sxs-lookup"><span data-stu-id="b1d7d-623">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="b1d7d-624">*Wymaga zainstalowania rozszerzenia witryny środowiska uruchomieniowego ASP.NET Core {VERSION} (x64).*</span><span class="sxs-lookup"><span data-stu-id="b1d7d-624">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="b1d7d-625">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` ( `{X.Y}` to wersja środowiska uruchomieniowego)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-625">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="b1d7d-626">Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="b1d7d-626">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="b1d7d-627">Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-627">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="b1d7d-628">Dziennik stdout modułu ASP.NET Core (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-628">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="b1d7d-629">Dziennik modułu ASP.NET Core stdout często rejestruje przydatne komunikaty o błędach, które nie są dostępne w dzienniku zdarzeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-629">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="b1d7d-630">Aby włączyć i wyświetlić dzienniki stdout:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-630">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="b1d7d-631">Przejdź do bloku **diagnozowanie i rozwiązywanie problemów** w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-631">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="b1d7d-632">W obszarze **Wybierz kategorię problemu**wybierz przycisk **aplikacji sieci Web w dół** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-632">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="b1d7d-633">W obszarze **sugerowane rozwiązania** > **Włącz przekierowywanie dziennika stdout**, wybierz przycisk, aby **otworzyć konsolę kudu, aby edytować Web.Config**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-633">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="b1d7d-634">W **konsoli diagnostyki**kudu Otwórz foldery w **witrynie**Path  >  **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-634">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="b1d7d-635">Przewiń w dół, aby odsłonić *web.config* plik w dolnej części listy.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-635">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="b1d7d-636">Kliknij ikonę ołówka obok pliku *web.config* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-636">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="b1d7d-637">Ustaw wartość **stdoutLogEnabled** na `true` i zmień ścieżkę **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-637">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="b1d7d-638">Wybierz pozycję **Zapisz** , aby zapisać zaktualizowany plik *web.config* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-638">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="b1d7d-639">Utwórz żądanie do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-639">Make a request to the app.</span></span>
1. <span data-ttu-id="b1d7d-640">Wróć do witryny Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-640">Return to the Azure portal.</span></span> <span data-ttu-id="b1d7d-641">Wybierz blok **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-641">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="b1d7d-642">Wybierz przycisk \*\*Przejdź &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-642">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="b1d7d-643">Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-643">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="b1d7d-644">Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-644">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="b1d7d-645">Wybierz folder **LogFiles** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-645">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="b1d7d-646">Sprawdź **zmodyfikowaną** kolumnę i wybierz ikonę ołówka, aby edytować dziennik stdout z datą ostatniej modyfikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-646">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="b1d7d-647">Po otwarciu pliku dziennika zostanie wyświetlony komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-647">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="b1d7d-648">Wyłącz rejestrowanie stdout po zakończeniu rozwiązywania problemów:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-648">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="b1d7d-649">W **konsoli diagnostyki**kudu Wróć do **witryny**ścieżki  >  **wwwroot** , aby odsłonić plik *web.config* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-649">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="b1d7d-650">Otwórz plik **web.config** ponownie, wybierając ikonę ołówka.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-650">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="b1d7d-651">Ustaw wartość **stdoutLogEnabled** na `false` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-651">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="b1d7d-652">Wybierz pozycję **Zapisz** , aby zapisać plik.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-652">Select **Save** to save the file.</span></span>

<span data-ttu-id="b1d7d-653">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-653">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="b1d7d-654">Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-654">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="b1d7d-655">Nie ma żadnego limitu rozmiaru pliku dziennika lub liczby utworzonych plików dziennika.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-655">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="b1d7d-656">Rejestrowania stdout można używać tylko w celu rozwiązywania problemów z uruchamianiem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-656">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="b1d7d-657">Aby uzyskać ogólne rejestrowanie w aplikacji ASP.NET Core po uruchomieniu, należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-657">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="b1d7d-658">Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-658">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="b1d7d-659">Dziennik debugowania modułu ASP.NET Core (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-659">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="b1d7d-660">Dziennik debugowania modułu ASP.NET Core zapewnia dodatkowe, dokładniejsze rejestrowanie z modułu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-660">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="b1d7d-661">Aby włączyć i wyświetlić dzienniki stdout:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-661">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="b1d7d-662">Aby włączyć Rozszerzony Dziennik diagnostyczny, wykonaj jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-662">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="b1d7d-663">Postępuj zgodnie z instrukcjami w temacie [udoskonalone dzienniki diagnostyczne](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) , aby skonfigurować aplikację do rozszerzonego rejestrowania diagnostycznego.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-663">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="b1d7d-664">Wdróż ponownie aplikację.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-664">Redeploy the app.</span></span>
   * <span data-ttu-id="b1d7d-665">Dodaj `<handlerSettings>` pokazany w [ulepszonych dziennikach diagnostycznych](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do pliku *web.config* aplikacji na żywo za pomocą konsoli kudu:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-665">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="b1d7d-666">Otwórz **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-666">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="b1d7d-667">Wybierz przycisk \*\*Przejdź &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-667">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="b1d7d-668">Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-668">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="b1d7d-669">Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-669">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="b1d7d-670">Otwórz foldery w **witrynie**Path  >  **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-670">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="b1d7d-671">Edytuj plik *web.config* , wybierając przycisk ołówka.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-671">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="b1d7d-672">Dodaj `<handlerSettings>` sekcję, jak pokazano w [udoskonalonych dziennikach diagnostycznych](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-672">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="b1d7d-673">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-673">Select the **Save** button.</span></span>
1. <span data-ttu-id="b1d7d-674">Otwórz **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-674">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="b1d7d-675">Wybierz przycisk \*\*Przejdź &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-675">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="b1d7d-676">Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-676">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="b1d7d-677">Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-677">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="b1d7d-678">Otwórz foldery w **witrynie**Path  >  **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-678">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="b1d7d-679">Jeśli nie podano ścieżki do pliku *aspnetcore-Debug. log* , plik zostanie wyświetlony na liście.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-679">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="b1d7d-680">Jeśli podano ścieżkę, przejdź do lokalizacji pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-680">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="b1d7d-681">Otwórz plik dziennika z przyciskiem ołówek obok nazwy pliku.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-681">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="b1d7d-682">Wyłącz rejestrowanie debugowania po zakończeniu rozwiązywania problemów:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-682">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="b1d7d-683">Aby wyłączyć rozszerzony Dziennik debugowania, wykonaj jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-683">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="b1d7d-684">Usuń `<handlerSettings>` plik z pliku *web.config* lokalnie i Wdróż ponownie aplikację.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-684">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="b1d7d-685">Za pomocą konsoli kudu Edytuj plik *web.config* i Usuń `<handlerSettings>` sekcję.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-685">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="b1d7d-686">Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-686">Save the file.</span></span>

<span data-ttu-id="b1d7d-687">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-687">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="b1d7d-688">Niepowodzenie wyłączenia dziennika debugowania może prowadzić do awarii aplikacji lub serwera.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-688">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="b1d7d-689">Nie ma limitu rozmiaru pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-689">There's no limit on log file size.</span></span> <span data-ttu-id="b1d7d-690">Funkcja rejestrowania debugowania służy tylko do rozwiązywania problemów z uruchamianiem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-690">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="b1d7d-691">Aby uzyskać ogólne rejestrowanie w aplikacji ASP.NET Core po uruchomieniu, należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-691">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="b1d7d-692">Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-692">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="b1d7d-693">Aplikacja wolna lub wysunięta (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-693">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="b1d7d-694">Gdy aplikacja reaguje powoli lub zawiesza się na żądanie, zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-694">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="b1d7d-695">Rozwiązywanie problemów związanych z niską wydajnością aplikacji internetowych w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b1d7d-695">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="b1d7d-696">Użyj rozszerzenia witryny diagnostyki awarii, aby przechwycić zrzut dla sporadycznych problemów z wyjątkami lub problemów z wydajnością w usłudze Azure Web App</span><span class="sxs-lookup"><span data-stu-id="b1d7d-696">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="b1d7d-697">Bloki monitorowania</span><span class="sxs-lookup"><span data-stu-id="b1d7d-697">Monitoring blades</span></span>

<span data-ttu-id="b1d7d-698">Bloki monitorowania zapewniają alternatywne środowisko rozwiązywania problemów z metodami opisanymi wcześniej w temacie.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-698">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="b1d7d-699">Te bloki mogą służyć do diagnozowania błędów serii 500.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-699">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="b1d7d-700">Upewnij się, że rozszerzenia ASP.NET Core są zainstalowane.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-700">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="b1d7d-701">Jeśli rozszerzenia nie są zainstalowane, zainstaluj je ręcznie:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-701">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="b1d7d-702">W sekcji blok **narzędzi programistycznych** wybierz blok **rozszerzenia** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-702">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="b1d7d-703">**Rozszerzenia ASP.NET Core** powinny znajdować się na liście.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-703">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="b1d7d-704">Jeśli rozszerzenia nie są zainstalowane, wybierz przycisk **Dodaj** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-704">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="b1d7d-705">Wybierz z listy **rozszerzenia ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-705">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="b1d7d-706">Wybierz **przycisk OK** , aby zaakceptować postanowienia prawne.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-706">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="b1d7d-707">W bloku **Dodaj rozszerzenie** wybierz pozycję **OK** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-707">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="b1d7d-708">Komunikat podręczny informujący o pomyślnym zainstalowaniu rozszerzeń.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-708">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="b1d7d-709">Jeśli rejestrowanie stdout nie jest włączone, wykonaj następujące kroki:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-709">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="b1d7d-710">W Azure Portal wybierz blok **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-710">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="b1d7d-711">Wybierz przycisk \*\*Przejdź &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-711">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="b1d7d-712">Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-712">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="b1d7d-713">Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-713">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="b1d7d-714">Otwórz foldery w **witrynie** Path > **wwwroot** i przewiń w dół, aby odsłonić *web.config* plik w dolnej części listy.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-714">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="b1d7d-715">Kliknij ikonę ołówka obok pliku *web.config* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-715">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="b1d7d-716">Ustaw wartość **stdoutLogEnabled** na `true` i zmień ścieżkę **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-716">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="b1d7d-717">Wybierz pozycję **Zapisz** , aby zapisać zaktualizowany plik *web.config* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-717">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="b1d7d-718">Wykonaj aktywację rejestrowania diagnostycznego:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-718">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="b1d7d-719">W Azure Portal wybierz blok **dzienników diagnostycznych** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-719">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="b1d7d-720">Wybierz pozycję **Włącz** , aby włączyć **Rejestrowanie aplikacji (system plików)** i **szczegółowe komunikaty o błędach**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-720">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="b1d7d-721">Wybierz przycisk **Zapisz** znajdujący się u góry bloku.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-721">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="b1d7d-722">Aby uwzględnić śledzenie nieudanych żądań, znane także jako rejestrowanie nieudanych żądań buforowania zdarzeń (FREB), wybierz **przełącznik dla** **śledzenia nieudanych żądań**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-722">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="b1d7d-723">Wybierz blok **strumień dziennika** , który jest wyświetlany bezpośrednio w bloku **dzienników diagnostycznych** w portalu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-723">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="b1d7d-724">Utwórz żądanie do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-724">Make a request to the app.</span></span>
1. <span data-ttu-id="b1d7d-725">W danych strumienia dziennika jest wskazywana Przyczyna błędu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-725">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="b1d7d-726">Należy pamiętać o wyłączeniu rejestrowania stdout po zakończeniu rozwiązywania problemów.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-726">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="b1d7d-727">Aby wyświetlić dzienniki śledzenia niepomyślnych żądań (dzienniki FREB):</span><span class="sxs-lookup"><span data-stu-id="b1d7d-727">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="b1d7d-728">Przejdź do bloku **diagnozowanie i rozwiązywanie problemów** w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-728">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="b1d7d-729">Wybierz pozycję **dzienniki śledzenia niepomyślnych żądań** z obszaru **Narzędzia obsługi** na pasku bocznym.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-729">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="b1d7d-730">Zobacz [sekcję śledzenie niepomyślnych żądań w temacie Włączanie rejestrowania diagnostyki dla aplikacji sieci Web w programie Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) i informacje [o wydajności aplikacji dotyczące Web Apps na platformie Azure: Jak mogę włączyć śledzenie nieudanych żądań?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) Aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-730">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="b1d7d-731">Aby uzyskać więcej informacji, zobacz [Włączanie rejestrowania diagnostycznego dla aplikacji sieci Web w Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-731">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="b1d7d-732">Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-732">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="b1d7d-733">Nie ma żadnego limitu rozmiaru pliku dziennika lub liczby utworzonych plików dziennika.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-733">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="b1d7d-734">Aby rejestrować procedury w aplikacji ASP.NET Core, użyj biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-734">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="b1d7d-735">Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-735">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="b1d7d-736">Rozwiązywanie problemów w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="b1d7d-736">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="b1d7d-737">Dziennik zdarzeń aplikacji (IIS)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-737">Application Event Log (IIS)</span></span>

<span data-ttu-id="b1d7d-738">Dostęp do dziennika zdarzeń aplikacji:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-738">Access the Application Event Log:</span></span>

1. <span data-ttu-id="b1d7d-739">Otwórz menu Start, wyszukaj ciąg *Podgląd zdarzeń*i wybierz aplikację **Podgląd zdarzeń** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-739">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="b1d7d-740">W **Podgląd zdarzeń**Otwórz węzeł **Dzienniki systemu Windows** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-740">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="b1d7d-741">Wybierz pozycję **aplikacja** , aby otworzyć dziennik zdarzeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-741">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="b1d7d-742">Wyszukaj błędy związane z niepowodzeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-742">Search for errors associated with the failing app.</span></span> <span data-ttu-id="b1d7d-743">Błędy mają wartość *modułu AspNetCore IIS* lub *moduł IIS Express AspNetCore* w kolumnie *Źródło* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-743">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="b1d7d-744">Uruchamianie aplikacji w wierszu polecenia</span><span class="sxs-lookup"><span data-stu-id="b1d7d-744">Run the app at a command prompt</span></span>

<span data-ttu-id="b1d7d-745">Wiele błędów uruchamiania nie tworzy użytecznych informacji w dzienniku zdarzeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-745">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="b1d7d-746">Przyczynę niektórych błędów można znaleźć, uruchamiając aplikację w wierszu polecenia w systemie hostingu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-746">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="b1d7d-747">Wdrożenie zależne od platformy</span><span class="sxs-lookup"><span data-stu-id="b1d7d-747">Framework-dependent deployment</span></span>

<span data-ttu-id="b1d7d-748">Jeśli aplikacja jest [wdrożeniem zależnym od platformy](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="b1d7d-748">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="b1d7d-749">W wierszu polecenia przejdź do folderu wdrożenia i uruchom aplikację, wykonując zestaw aplikacji za pomocą *dotnet.exe*.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-749">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="b1d7d-750">W poniższym poleceniu Zastąp nazwę zestawu aplikacji dla \<assembly_name> : `dotnet .\<assembly_name>.dll` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-750">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="b1d7d-751">Dane wyjściowe konsoli z aplikacji, pokazujące błędy, są zapisywane w oknie konsoli.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-751">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="b1d7d-752">Jeśli wystąpią błędy podczas wysyłania żądania do aplikacji, należy wysłać żądanie do hosta i portu, gdzie Kestrel nasłuchuje.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-752">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="b1d7d-753">Przy użyciu domyślnego hosta i wpisu Wyślij żądanie do `http://localhost:5000/` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-753">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="b1d7d-754">Jeśli aplikacja będzie odpowiadać zwykle na adres punktu końcowego Kestrel, problem jest bardziej prawdopodobnie związany z konfiguracją hostingu i mniej korzystnie w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-754">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="b1d7d-755">Wdrażanie samodzielne</span><span class="sxs-lookup"><span data-stu-id="b1d7d-755">Self-contained deployment</span></span>

<span data-ttu-id="b1d7d-756">Jeśli aplikacja jest [wdrożeniem](/dotnet/core/deploying/#self-contained-deployments-scd)niezależnym:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-756">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="b1d7d-757">W wierszu polecenia przejdź do folderu wdrożenia i uruchom plik wykonywalny aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-757">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="b1d7d-758">W poniższym poleceniu Zastąp nazwę zestawu aplikacji dla \<assembly_name> : `<assembly_name>.exe` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-758">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="b1d7d-759">Dane wyjściowe konsoli z aplikacji, pokazujące błędy, są zapisywane w oknie konsoli.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-759">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="b1d7d-760">Jeśli wystąpią błędy podczas wysyłania żądania do aplikacji, należy wysłać żądanie do hosta i portu, gdzie Kestrel nasłuchuje.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-760">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="b1d7d-761">Przy użyciu domyślnego hosta i wpisu Wyślij żądanie do `http://localhost:5000/` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-761">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="b1d7d-762">Jeśli aplikacja będzie odpowiadać zwykle na adres punktu końcowego Kestrel, problem jest bardziej prawdopodobnie związany z konfiguracją hostingu i mniej korzystnie w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-762">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="b1d7d-763">Dziennik stdout modułu ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-763">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="b1d7d-764">Aby włączyć i wyświetlić dzienniki stdout:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-764">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="b1d7d-765">Przejdź do folderu wdrożenia lokacji w systemie hostingu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-765">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="b1d7d-766">Jeśli folder *Logs* nie istnieje, utwórz go.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-766">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="b1d7d-767">Aby uzyskać instrukcje dotyczące włączania programu MSBuild do automatycznego tworzenia folderu *Logs* w ramach wdrożenia, zobacz temat [Struktura katalogów](xref:host-and-deploy/directory-structure) .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-767">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="b1d7d-768">Edytuj plik *web.config* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-768">Edit the *web.config* file.</span></span> <span data-ttu-id="b1d7d-769">Ustaw wartość **stdoutLogEnabled** na `true` i zmień ścieżkę **stdoutLogFile** , tak aby wskazywała folder *Logs* (na przykład `.\logs\stdout` ).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-769">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="b1d7d-770">`stdout` ścieżka jest prefiksem nazwy pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-770">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="b1d7d-771">Sygnatura czasowa, identyfikator procesu i rozszerzenie pliku są dodawane automatycznie podczas tworzenia dziennika.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-771">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="b1d7d-772">Używając `stdout` jako prefiksu nazwy pliku, typowy plik dziennika ma nazwę *stdout_20180205184032_5412. log*.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-772">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="b1d7d-773">Upewnij się, że tożsamość puli aplikacji ma uprawnienia do zapisu w folderze *Logs* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-773">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="b1d7d-774">Zapisz zaktualizowany plik *web.config* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-774">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="b1d7d-775">Utwórz żądanie do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-775">Make a request to the app.</span></span>
1. <span data-ttu-id="b1d7d-776">Przejdź do folderu *Logs* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-776">Navigate to the *logs* folder.</span></span> <span data-ttu-id="b1d7d-777">Znajdź i Otwórz najnowszy dziennik stdout.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-777">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="b1d7d-778">Zbadaj dziennik pod kątem błędów.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-778">Study the log for errors.</span></span>

<span data-ttu-id="b1d7d-779">Wyłącz rejestrowanie stdout po zakończeniu rozwiązywania problemów:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-779">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="b1d7d-780">Edytuj plik *web.config* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-780">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="b1d7d-781">Ustaw wartość **stdoutLogEnabled** na `false` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-781">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="b1d7d-782">Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-782">Save the file.</span></span>

<span data-ttu-id="b1d7d-783">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-783">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="b1d7d-784">Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-784">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="b1d7d-785">Nie ma żadnego limitu rozmiaru pliku dziennika lub liczby utworzonych plików dziennika.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-785">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="b1d7d-786">Aby rejestrować procedury w aplikacji ASP.NET Core, użyj biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-786">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="b1d7d-787">Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-787">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="b1d7d-788">Dziennik debugowania modułu ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-788">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="b1d7d-789">Dodaj następujące ustawienia programu obsługi do pliku *web.config* aplikacji, aby włączyć Dziennik debugowania modułu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-789">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="b1d7d-790">Upewnij się, że ścieżka określona dla dziennika istnieje i że tożsamość puli aplikacji ma uprawnienia do zapisu w tej lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-790">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="b1d7d-791">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-791">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="b1d7d-792">Włącz stronę wyjątków dla deweloperów</span><span class="sxs-lookup"><span data-stu-id="b1d7d-792">Enable the Developer Exception Page</span></span>

<span data-ttu-id="b1d7d-793">`ASPNETCORE_ENVIRONMENT` [Zmienną środowiskową można dodać do web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) , aby uruchomić aplikację w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-793">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="b1d7d-794">O ile środowisko nie zostanie zastąpione podczas uruchamiania aplikacji przez `UseEnvironment` konstruktora hosta, ustawienie zmiennej środowiskowej umożliwia wyświetlenie [strony wyjątku dewelopera](xref:fundamentals/error-handling) po uruchomieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-794">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

<span data-ttu-id="b1d7d-795">Ustawienie zmiennej środowiskowej dla programu `ASPNETCORE_ENVIRONMENT` jest zalecane wyłącznie do użytku na przejściowych i testowych serwerach, które nie są dostępne w Internecie.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-795">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="b1d7d-796">Usuń zmienną środowiskową z pliku *web.config* po rozwiązaniu problemu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-796">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="b1d7d-797">Aby uzyskać informacje na temat ustawiania zmiennych środowiskowych w *web.config*, zobacz [EnvironmentVariables podrzędny element aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-797">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="b1d7d-798">Uzyskiwanie danych z aplikacji</span><span class="sxs-lookup"><span data-stu-id="b1d7d-798">Obtain data from an app</span></span>

<span data-ttu-id="b1d7d-799">Jeśli aplikacja może odpowiadać na żądania, uzyskiwać żądania, połączenia i dodatkowe dane z aplikacji przy użyciu wbudowanego oprogramowania terminala.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-799">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="b1d7d-800">Aby uzyskać więcej informacji i przykładowy kod, zobacz <xref:test/troubleshoot#obtain-data-from-an-app> .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-800">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="b1d7d-801">Aplikacja wolna lub wysunięta (IIS)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-801">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="b1d7d-802">*Zrzut awaryjny* to migawka pamięci systemu, która może pomóc w ustaleniu przyczyny awarii aplikacji, awarii uruchamiania lub powolnej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-802">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="b1d7d-803">Awaria aplikacji lub napotka wyjątek</span><span class="sxs-lookup"><span data-stu-id="b1d7d-803">App crashes or encounters an exception</span></span>

<span data-ttu-id="b1d7d-804">Uzyskaj i Analizuj Zrzut z [raportowanie błędów systemu Windows (raportowanie błędów systemu Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="b1d7d-804">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="b1d7d-805">Utwórz folder do przechowywania plików zrzutu awaryjnego w `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-805">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="b1d7d-806">Pula aplikacji musi mieć dostęp do zapisu w folderze.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-806">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="b1d7d-807">Uruchom [skrypt programu PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)w programie EnableDumps:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-807">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="b1d7d-808">Jeśli aplikacja korzysta z [modelu hostingu w procesie](xref:host-and-deploy/iis/index#in-process-hosting-model), uruchom skrypt dla *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-808">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="b1d7d-809">Jeśli aplikacja korzysta z [modelu hostingu poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), uruchom skrypt dla *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-809">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="b1d7d-810">Uruchom aplikację w warunkach, które powodują awarię.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-810">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="b1d7d-811">Po wystąpieniu awarii Uruchom [skrypt programu DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="b1d7d-811">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="b1d7d-812">Jeśli aplikacja korzysta z [modelu hostingu w procesie](xref:host-and-deploy/iis/index#in-process-hosting-model), uruchom skrypt dla *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-812">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="b1d7d-813">Jeśli aplikacja korzysta z [modelu hostingu poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), uruchom skrypt dla *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-813">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="b1d7d-814">Po awarii aplikacji i zakończeniu zbierania zrzutów aplikacja może zakończyć normalne działanie.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-814">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="b1d7d-815">Skrypt programu PowerShell konfiguruje raportowanie błędów systemu Windows w celu zebrania do pięciu zrzutów na aplikację.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-815">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="b1d7d-816">Zrzuty awaryjne mogą wymagać dużej ilości miejsca na dysku (do kilku gigabajtów).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-816">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="b1d7d-817">Aplikacja zawiesza się, kończy się niepowodzeniem podczas uruchamiania lub działa normalnie</span><span class="sxs-lookup"><span data-stu-id="b1d7d-817">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="b1d7d-818">Gdy aplikacja *zawiesza* się (bez awarii), kończy się niepowodzeniem podczas uruchamiania lub działa normalnie, zobacz [pliki zrzutu w trybie użytkownika: wybór najlepszego narzędzia](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) do wygenerowania zrzutu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-818">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="b1d7d-819">Analizowanie zrzutu</span><span class="sxs-lookup"><span data-stu-id="b1d7d-819">Analyze the dump</span></span>

<span data-ttu-id="b1d7d-820">Zrzut można analizować przy użyciu kilku metod.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-820">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="b1d7d-821">Aby uzyskać więcej informacji, zobacz [Analizowanie pliku zrzutu w trybie użytkownika](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-821">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="b1d7d-822">Wyczyść pamięć podręczną pakietów</span><span class="sxs-lookup"><span data-stu-id="b1d7d-822">Clear package caches</span></span>

<span data-ttu-id="b1d7d-823">Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestaw .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w ramach aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-823">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="b1d7d-824">W niektórych przypadkach niespójne pakiety mogą przerwać aplikację podczas przeprowadzania uaktualnień głównych.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-824">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="b1d7d-825">Większość tych problemów można naprawić, wykonując następujące instrukcje:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-825">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="b1d7d-826">Usuń foldery *bin* i *obj* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-826">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="b1d7d-827">Wyczyść pamięć podręczną pakietów, wykonując [wszystkie elementy lokalne usługi NuGet programu dotnet--Wyczyść](/dotnet/core/tools/dotnet-nuget-locals) z poziomu powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-827">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="b1d7d-828">Czyszczenie pamięci podręcznych pakietów można także wykonać przy użyciu narzędzia [nuget.exe](https://www.nuget.org/downloads) i wykonując polecenie `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-828">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="b1d7d-829">*nuget.exe* nie jest instalacją w pakiecie z systemem operacyjnym Windows dla komputerów stacjonarnych i należy ją pobrać niezależnie od [witryny sieci Web programu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-829">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="b1d7d-830">Przywróć i skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-830">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="b1d7d-831">Usuń wszystkie pliki z folderu wdrożenia na serwerze przed ponownym wdrożeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-831">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b1d7d-832">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="b1d7d-832">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="b1d7d-833">Dokumentacja platformy Azure</span><span class="sxs-lookup"><span data-stu-id="b1d7d-833">Azure documentation</span></span>

* [<span data-ttu-id="b1d7d-834">Usługa Application Insights dla platformy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b1d7d-834">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="b1d7d-835">Sekcja zdalne debugowanie aplikacji sieci Web Rozwiązywanie problemów z aplikacją sieci Web w Azure App Service przy użyciu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1d7d-835">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="b1d7d-836">Omówienie diagnostyki Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b1d7d-836">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="b1d7d-837">Jak monitorować aplikacje w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b1d7d-837">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="b1d7d-838">Rozwiązywanie problemów z aplikacją sieci Web w Azure App Service przy użyciu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1d7d-838">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="b1d7d-839">Rozwiązywanie problemów z błędami HTTP "502 złej Gateway" i "503 Usługa niedostępna" w usłudze Azure Web Apps</span><span class="sxs-lookup"><span data-stu-id="b1d7d-839">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="b1d7d-840">Rozwiązywanie problemów związanych z niską wydajnością aplikacji internetowych w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b1d7d-840">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="b1d7d-841">Często zadawane pytania dotyczące wydajności aplikacji dla Web Apps na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="b1d7d-841">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="b1d7d-842">Piaskownica usługi Azure Web App (ograniczenia wykonywania App Service Runtime)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-842">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="b1d7d-843">Piątek Azure: Azure App Service środowisko diagnostyczne i rozwiązywania problemów (wideo 12-minutowy)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-843">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="b1d7d-844">Dokumentacja programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1d7d-844">Visual Studio documentation</span></span>

* [<span data-ttu-id="b1d7d-845">ASP.NET Core debugowania zdalnego na serwerze IIS na platformie Azure w programie Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="b1d7d-845">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="b1d7d-846">Zdalne debugowanie ASP.NET Core na zdalnym komputerze IIS w programie Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="b1d7d-846">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="b1d7d-847">Nauka debugowania przy użyciu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1d7d-847">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="b1d7d-848">Dokumentacja Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b1d7d-848">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="b1d7d-849">Debugowanie za pomocą Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b1d7d-849">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b1d7d-850">Ten artykuł zawiera informacje dotyczące typowych błędów uruchamiania aplikacji oraz instrukcje dotyczące sposobu diagnozowania błędów podczas wdrażania aplikacji w usłudze Azure App Service lub IIS:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-850">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="b1d7d-851">Błędy uruchamiania aplikacji</span><span class="sxs-lookup"><span data-stu-id="b1d7d-851">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="b1d7d-852">Objaśnia typowe scenariusze uruchamiania kodu stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-852">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="b1d7d-853">Rozwiązywanie problemów dotyczących Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b1d7d-853">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="b1d7d-854">Zawiera porady dotyczące rozwiązywania problemów z aplikacjami wdrożonymi w celu Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-854">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="b1d7d-855">Rozwiązywanie problemów w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="b1d7d-855">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="b1d7d-856">Zawiera porady dotyczące rozwiązywania problemów z aplikacjami wdrożonymi w usługach IIS lub lokalnie uruchomionymi IIS Express.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-856">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="b1d7d-857">Wskazówki dotyczą zarówno wdrożeń systemu Windows Server, jak i pulpitu systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-857">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="b1d7d-858">Wyczyść pamięć podręczną pakietów</span><span class="sxs-lookup"><span data-stu-id="b1d7d-858">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="b1d7d-859">Wyjaśnia, co należy zrobić, gdy niespójne pakiety przerywają działanie aplikacji podczas przeprowadzania uaktualnień głównych lub zmiany wersji pakietu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-859">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="b1d7d-860">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="b1d7d-860">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="b1d7d-861">Wyświetla listę dodatkowych tematów dotyczących rozwiązywania problemów.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-861">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="b1d7d-862">Błędy uruchamiania aplikacji</span><span class="sxs-lookup"><span data-stu-id="b1d7d-862">App startup errors</span></span>

<span data-ttu-id="b1d7d-863">W programie Visual Studio ASP.NET Core Project domyślnie [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting podczas debugowania.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-863">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="b1d7d-864">*Błąd procesu 502,5* , który występuje, gdy debugowanie lokalne można zdiagnozować przy użyciu porady w tym temacie.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-864">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="b1d7d-865">403,14 zabronione</span><span class="sxs-lookup"><span data-stu-id="b1d7d-865">403.14 Forbidden</span></span>

<span data-ttu-id="b1d7d-866">Nie można uruchomić aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-866">The app fails to start.</span></span> <span data-ttu-id="b1d7d-867">Rejestrowany jest następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-867">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="b1d7d-868">Ten błąd jest zwykle spowodowany przez uszkodzone wdrożenie w systemie hostingu, który obejmuje następujące scenariusze:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-868">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="b1d7d-869">Aplikacja jest wdrażana w niewłaściwym folderze w systemie hostingu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-869">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="b1d7d-870">W procesie wdrażania nie powiodło się przeniesienie wszystkich plików i folderów aplikacji do folderu wdrożenia w systemie hostingu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-870">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="b1d7d-871">W wdrożeniu brakuje pliku *web.config* lub zawartość pliku *web.config* jest źle sformułowana.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-871">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="b1d7d-872">Wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-872">Perform the following steps:</span></span>

1. <span data-ttu-id="b1d7d-873">Usuń wszystkie pliki i foldery z folderu wdrożenia w systemie hostingu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-873">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="b1d7d-874">Wdróż ponownie zawartość folderu *publikowania* aplikacji w systemie hostingu przy użyciu zwykłej metody wdrażania, takiej jak Visual Studio, PowerShell lub wdrażanie ręczne:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-874">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="b1d7d-875">Upewnij się, że plik *web.config* jest obecny we wdrożeniu i że jego zawartość jest poprawna.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-875">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="b1d7d-876">Podczas hostowania w Azure App Service upewnij się, że aplikacja została wdrożona w `D:\home\site\wwwroot` folderze.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-876">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="b1d7d-877">Jeśli aplikacja jest hostowana przez usługi IIS, upewnij się, że aplikacja jest wdrożona w **ścieżce fizycznej** usług IIS pokazanej w **ustawieniach podstawowych**w **Menedżerze usług IIS**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-877">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="b1d7d-878">Upewnij się, że wszystkie pliki i foldery aplikacji zostały wdrożone, porównując wdrożenie w systemie hostingu z zawartością folderu *publikowania* projektu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-878">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="b1d7d-879">Aby uzyskać więcej informacji na temat układu opublikowanej aplikacji ASP.NET Core, zobacz <xref:host-and-deploy/directory-structure> .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-879">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="b1d7d-880">Aby uzyskać więcej informacji na temat pliku *web.config* , zobacz <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-880">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="b1d7d-881">500 Wewnętrzny błąd serwera</span><span class="sxs-lookup"><span data-stu-id="b1d7d-881">500 Internal Server Error</span></span>

<span data-ttu-id="b1d7d-882">Aplikacja zostanie uruchomiona, ale błąd uniemożliwia serwerowi spełnienie żądania.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-882">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="b1d7d-883">Ten błąd występuje w kodzie aplikacji podczas uruchamiania lub podczas tworzenia odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-883">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="b1d7d-884">Odpowiedź może nie zawierać żadnej zawartości lub odpowiedź może pojawić się w przeglądarce jako *błąd wewnętrzny serwera 500* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-884">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="b1d7d-885">Dziennik zdarzeń aplikacji zwykle stwierdza, że aplikacja została uruchomiona normalnie.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-885">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="b1d7d-886">Z perspektywy serwera jest to poprawne.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-886">From the server's perspective, that's correct.</span></span> <span data-ttu-id="b1d7d-887">Aplikacja została uruchomiona, ale nie może wygenerować prawidłowej odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-887">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="b1d7d-888">Uruchom aplikację w wierszu polecenia na serwerze lub Włącz dziennik stdout modułu ASP.NET Core, aby rozwiązać problem.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-888">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="b1d7d-889">502.5 Błąd procesu</span><span class="sxs-lookup"><span data-stu-id="b1d7d-889">502.5 Process Failure</span></span>

<span data-ttu-id="b1d7d-890">Proces roboczy kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-890">The worker process fails.</span></span> <span data-ttu-id="b1d7d-891">Aplikacja nie zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-891">The app doesn't start.</span></span>

<span data-ttu-id="b1d7d-892">[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) próbuje uruchomić proces roboczy, ale jego uruchomienie nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-892">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="b1d7d-893">Przyczyna niepowodzenia uruchomienia procesu zwykle można ustalić na podstawie wpisów w dzienniku zdarzeń aplikacji i dzienniku modułu ASP.NET Core stdout.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-893">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="b1d7d-894">Typowym warunkiem niepowodzenia jest to, że aplikacja jest nieprawidłowo skonfigurowana z powodu docelowej wersji ASP.NET Core udostępnionej platformy, która nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-894">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="b1d7d-895">Sprawdź, które wersje ASP.NET Core udostępnionej platformy są zainstalowane na maszynie docelowej.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-895">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="b1d7d-896">*Platforma udostępniona* jest zestawem zestawów (plików*dll* ), które są zainstalowane na maszynie i do których odwołuje się pakiet `Microsoft.AspNetCore.App` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-896">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="b1d7d-897">Odwołanie do pakietu nie może określać minimalnej wymaganej wersji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-897">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="b1d7d-898">Aby uzyskać więcej informacji, zobacz [udostępnioną strukturę](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-898">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="b1d7d-899">Strona błędu *niepowodzenia procesu 502,5* jest zwracana w przypadku niepowodzenia konfiguracji hostingu lub aplikacji. powoduje to niepowodzenie procesu roboczego:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-899">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="b1d7d-900">Nie można uruchomić aplikacji (ErrorCode "0x800700C1")</span><span class="sxs-lookup"><span data-stu-id="b1d7d-900">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="b1d7d-901">Nie można uruchomić aplikacji, ponieważ nie można załadować zestawu aplikacji (*dll*).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-901">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="b1d7d-902">Ten błąd występuje, gdy wystąpiła niezgodność między opublikowaną aplikacją a procesem w3wp/iisexpress.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-902">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="b1d7d-903">Upewnij się, że ustawienie 32-bitowe puli aplikacji jest poprawne:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-903">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="b1d7d-904">Wybierz pulę aplikacji w **pulach aplikacji**Menedżera usług IIS.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-904">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="b1d7d-905">Wybierz pozycję **Ustawienia zaawansowane** w obszarze **Edytuj pulę aplikacji** w panelu **Akcje** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-905">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="b1d7d-906">Ustaw **opcję Włącz aplikacje 32-bitowe**:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-906">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="b1d7d-907">W przypadku wdrażania aplikacji 32-bitowej (x86) ustaw wartość na `True` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-907">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="b1d7d-908">W przypadku wdrażania aplikacji 64-bitowej (x64) ustaw wartość na `False` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-908">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="b1d7d-909">Upewnij się, że nie występuje konflikt między `<Platform>` właściwością programu MSBuild w pliku projektu a opublikowaną bitową w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-909">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="b1d7d-910">Resetowanie połączenia</span><span class="sxs-lookup"><span data-stu-id="b1d7d-910">Connection reset</span></span>

<span data-ttu-id="b1d7d-911">Jeśli wystąpi błąd po wysłaniu nagłówków, jest zbyt późno, aby serwer mógł wysłać **błąd wewnętrzny serwera 500** w przypadku wystąpienia błędu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-911">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="b1d7d-912">Zdarza się to często w przypadku wystąpienia błędu podczas serializacji obiektów złożonych dla odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-912">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="b1d7d-913">Ten typ błędu pojawia się jako błąd *resetowania połączenia* na kliencie.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-913">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="b1d7d-914">[Rejestrowanie aplikacji](xref:fundamentals/logging/index) może pomóc w rozwiązywaniu tego typu błędów.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-914">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="b1d7d-915">Domyślne limity uruchamiania</span><span class="sxs-lookup"><span data-stu-id="b1d7d-915">Default startup limits</span></span>

<span data-ttu-id="b1d7d-916">[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) jest skonfigurowany z domyślną *startupTimeLimitą* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-916">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="b1d7d-917">Po lewej stronie wartości domyślnej, uruchomienie aplikacji może trwać do dwóch minut przed zarejestrowaniem awarii procesu przez moduł.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-917">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="b1d7d-918">Aby uzyskać informacje na temat konfigurowania modułu, zobacz [atrybuty elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-918">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="b1d7d-919">Rozwiązywanie problemów dotyczących Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b1d7d-919">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="b1d7d-920">Dziennik zdarzeń aplikacji (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-920">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="b1d7d-921">Aby uzyskać dostęp do dziennika zdarzeń aplikacji, użyj bloku **diagnozowanie i rozwiązywanie problemów** w Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-921">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="b1d7d-922">W Azure Portal Otwórz aplikację w **App Services**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-922">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="b1d7d-923">Kliknij pozycję **Diagnozowanie i rozwiązywanie problemów**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-923">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="b1d7d-924">Wybierz nagłówek **Narzędzia diagnostyczne** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-924">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="b1d7d-925">W obszarze **Narzędzia obsługi**wybierz przycisk **zdarzenia aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-925">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="b1d7d-926">Zapoznaj się z najnowszym błędem podanym w pozycji *AspNetCoreModule IIS* lub *IIS AspNetCoreModule v2* w kolumnie **Źródło** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-926">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="b1d7d-927">Alternatywą dla korzystania z bloku **diagnozowanie i rozwiązywanie problemów** jest przetestowanie pliku dziennika zdarzeń aplikacji bezpośrednio przy użyciu [kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="b1d7d-927">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="b1d7d-928">Otwórz **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-928">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="b1d7d-929">Wybierz przycisk \*\*Przejdź &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-929">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="b1d7d-930">Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-930">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="b1d7d-931">Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-931">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="b1d7d-932">Otwórz folder **LogFiles** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-932">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="b1d7d-933">Wybierz ikonę ołówka obok pliku *eventlog.xml* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-933">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="b1d7d-934">Przejrzyj dziennik.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-934">Examine the log.</span></span> <span data-ttu-id="b1d7d-935">Przewiń w dół dziennika, aby zobaczyć najnowsze zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-935">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="b1d7d-936">Uruchamianie aplikacji w konsoli kudu</span><span class="sxs-lookup"><span data-stu-id="b1d7d-936">Run the app in the Kudu console</span></span>

<span data-ttu-id="b1d7d-937">Wiele błędów uruchamiania nie tworzy użytecznych informacji w dzienniku zdarzeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-937">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="b1d7d-938">Możesz uruchomić aplikację w konsoli zdalnego wykonywania [kudu](https://github.com/projectkudu/kudu/wiki) , aby wykryć błąd:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-938">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="b1d7d-939">Otwórz **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-939">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="b1d7d-940">Wybierz przycisk \*\*Przejdź &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-940">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="b1d7d-941">Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-941">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="b1d7d-942">Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-942">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="b1d7d-943">Testowanie aplikacji 32-bitowej (x86)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-943">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="b1d7d-944">**Bieżąca wersja**</span><span class="sxs-lookup"><span data-stu-id="b1d7d-944">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="b1d7d-945">Uruchom aplikację: </span><span class="sxs-lookup"><span data-stu-id="b1d7d-945">Run the app:</span></span>
   * <span data-ttu-id="b1d7d-946">Jeśli aplikacja jest [wdrożeniem zależnym od platformy](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="b1d7d-946">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="b1d7d-947">Jeśli aplikacja jest [wdrożeniem](/dotnet/core/deploying/#self-contained-deployments-scd)niezależnym:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-947">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="b1d7d-948">Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-948">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="b1d7d-949">**Wdrożenie zależne od platformy uruchomione w wersji zapoznawczej**</span><span class="sxs-lookup"><span data-stu-id="b1d7d-949">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="b1d7d-950">*Wymaga zainstalowania rozszerzenia witryny środowiska uruchomieniowego ASP.NET Core {VERSION} (x86).*</span><span class="sxs-lookup"><span data-stu-id="b1d7d-950">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="b1d7d-951">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` ( `{X.Y}` to wersja środowiska uruchomieniowego)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-951">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="b1d7d-952">Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="b1d7d-952">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="b1d7d-953">Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-953">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="b1d7d-954">Testowanie aplikacji 64-bitowej (x64)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-954">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="b1d7d-955">**Bieżąca wersja**</span><span class="sxs-lookup"><span data-stu-id="b1d7d-955">**Current release**</span></span>

* <span data-ttu-id="b1d7d-956">Jeśli aplikacja jest wdrożeniem 64-bitowym (x64), [zależnym od platformy](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="b1d7d-956">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="b1d7d-957">Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="b1d7d-957">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="b1d7d-958">Jeśli aplikacja jest [wdrożeniem](/dotnet/core/deploying/#self-contained-deployments-scd)niezależnym:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-958">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="b1d7d-959">Uruchom aplikację: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="b1d7d-959">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="b1d7d-960">Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-960">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="b1d7d-961">**Wdrożenie zależne od platformy uruchomione w wersji zapoznawczej**</span><span class="sxs-lookup"><span data-stu-id="b1d7d-961">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="b1d7d-962">*Wymaga zainstalowania rozszerzenia witryny środowiska uruchomieniowego ASP.NET Core {VERSION} (x64).*</span><span class="sxs-lookup"><span data-stu-id="b1d7d-962">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="b1d7d-963">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` ( `{X.Y}` to wersja środowiska uruchomieniowego)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-963">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="b1d7d-964">Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="b1d7d-964">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="b1d7d-965">Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-965">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="b1d7d-966">Dziennik stdout modułu ASP.NET Core (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-966">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="b1d7d-967">Dziennik modułu ASP.NET Core stdout często rejestruje przydatne komunikaty o błędach, które nie są dostępne w dzienniku zdarzeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-967">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="b1d7d-968">Aby włączyć i wyświetlić dzienniki stdout:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-968">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="b1d7d-969">Przejdź do bloku **diagnozowanie i rozwiązywanie problemów** w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-969">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="b1d7d-970">W obszarze **Wybierz kategorię problemu**wybierz przycisk **aplikacji sieci Web w dół** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-970">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="b1d7d-971">W obszarze **sugerowane rozwiązania** > **Włącz przekierowywanie dziennika stdout**, wybierz przycisk, aby **otworzyć konsolę kudu, aby edytować Web.Config**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-971">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="b1d7d-972">W **konsoli diagnostyki**kudu Otwórz foldery w **witrynie**Path  >  **wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-972">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="b1d7d-973">Przewiń w dół, aby odsłonić *web.config* plik w dolnej części listy.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-973">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="b1d7d-974">Kliknij ikonę ołówka obok pliku *web.config* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-974">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="b1d7d-975">Ustaw wartość **stdoutLogEnabled** na `true` i zmień ścieżkę **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-975">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="b1d7d-976">Wybierz pozycję **Zapisz** , aby zapisać zaktualizowany plik *web.config* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-976">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="b1d7d-977">Utwórz żądanie do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-977">Make a request to the app.</span></span>
1. <span data-ttu-id="b1d7d-978">Wróć do witryny Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-978">Return to the Azure portal.</span></span> <span data-ttu-id="b1d7d-979">Wybierz blok **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-979">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="b1d7d-980">Wybierz przycisk \*\*Przejdź &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-980">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="b1d7d-981">Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-981">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="b1d7d-982">Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-982">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="b1d7d-983">Wybierz folder **LogFiles** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-983">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="b1d7d-984">Sprawdź **zmodyfikowaną** kolumnę i wybierz ikonę ołówka, aby edytować dziennik stdout z datą ostatniej modyfikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-984">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="b1d7d-985">Po otwarciu pliku dziennika zostanie wyświetlony komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-985">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="b1d7d-986">Wyłącz rejestrowanie stdout po zakończeniu rozwiązywania problemów:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-986">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="b1d7d-987">W **konsoli diagnostyki**kudu Wróć do **witryny**ścieżki  >  **wwwroot** , aby odsłonić plik *web.config* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-987">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="b1d7d-988">Otwórz plik **web.config** ponownie, wybierając ikonę ołówka.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-988">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="b1d7d-989">Ustaw wartość **stdoutLogEnabled** na `false` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-989">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="b1d7d-990">Wybierz pozycję **Zapisz** , aby zapisać plik.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-990">Select **Save** to save the file.</span></span>

<span data-ttu-id="b1d7d-991">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-991">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="b1d7d-992">Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-992">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="b1d7d-993">Nie ma żadnego limitu rozmiaru pliku dziennika lub liczby utworzonych plików dziennika.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-993">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="b1d7d-994">Rejestrowania stdout można używać tylko w celu rozwiązywania problemów z uruchamianiem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-994">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="b1d7d-995">Aby uzyskać ogólne rejestrowanie w aplikacji ASP.NET Core po uruchomieniu, należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-995">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="b1d7d-996">Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-996">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="b1d7d-997">Aplikacja wolna lub wysunięta (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-997">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="b1d7d-998">Gdy aplikacja reaguje powoli lub zawiesza się na żądanie, zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-998">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="b1d7d-999">Rozwiązywanie problemów związanych z niską wydajnością aplikacji internetowych w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b1d7d-999">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="b1d7d-1000">Użyj rozszerzenia witryny diagnostyki awarii, aby przechwycić zrzut dla sporadycznych problemów z wyjątkami lub problemów z wydajnością w usłudze Azure Web App</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1000">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="b1d7d-1001">Bloki monitorowania</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1001">Monitoring blades</span></span>

<span data-ttu-id="b1d7d-1002">Bloki monitorowania zapewniają alternatywne środowisko rozwiązywania problemów z metodami opisanymi wcześniej w temacie.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1002">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="b1d7d-1003">Te bloki mogą służyć do diagnozowania błędów serii 500.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1003">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="b1d7d-1004">Upewnij się, że rozszerzenia ASP.NET Core są zainstalowane.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1004">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="b1d7d-1005">Jeśli rozszerzenia nie są zainstalowane, zainstaluj je ręcznie:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1005">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="b1d7d-1006">W sekcji blok **narzędzi programistycznych** wybierz blok **rozszerzenia** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1006">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="b1d7d-1007">**Rozszerzenia ASP.NET Core** powinny znajdować się na liście.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1007">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="b1d7d-1008">Jeśli rozszerzenia nie są zainstalowane, wybierz przycisk **Dodaj** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1008">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="b1d7d-1009">Wybierz z listy **rozszerzenia ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1009">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="b1d7d-1010">Wybierz **przycisk OK** , aby zaakceptować postanowienia prawne.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1010">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="b1d7d-1011">W bloku **Dodaj rozszerzenie** wybierz pozycję **OK** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1011">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="b1d7d-1012">Komunikat podręczny informujący o pomyślnym zainstalowaniu rozszerzeń.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1012">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="b1d7d-1013">Jeśli rejestrowanie stdout nie jest włączone, wykonaj następujące kroki:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1013">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="b1d7d-1014">W Azure Portal wybierz blok **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1014">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="b1d7d-1015">Wybierz przycisk \*\*Przejdź &rarr; \*\* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1015">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="b1d7d-1016">Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1016">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="b1d7d-1017">Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1017">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="b1d7d-1018">Otwórz foldery w **witrynie** Path > **wwwroot** i przewiń w dół, aby odsłonić *web.config* plik w dolnej części listy.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1018">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="b1d7d-1019">Kliknij ikonę ołówka obok pliku *web.config* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1019">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="b1d7d-1020">Ustaw wartość **stdoutLogEnabled** na `true` i zmień ścieżkę **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1020">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="b1d7d-1021">Wybierz pozycję **Zapisz** , aby zapisać zaktualizowany plik *web.config* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1021">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="b1d7d-1022">Wykonaj aktywację rejestrowania diagnostycznego:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1022">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="b1d7d-1023">W Azure Portal wybierz blok **dzienników diagnostycznych** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1023">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="b1d7d-1024">Wybierz pozycję **Włącz** , aby włączyć **Rejestrowanie aplikacji (system plików)** i **szczegółowe komunikaty o błędach**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1024">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="b1d7d-1025">Wybierz przycisk **Zapisz** znajdujący się u góry bloku.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1025">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="b1d7d-1026">Aby uwzględnić śledzenie nieudanych żądań, znane także jako rejestrowanie nieudanych żądań buforowania zdarzeń (FREB), wybierz **przełącznik dla** **śledzenia nieudanych żądań**.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1026">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="b1d7d-1027">Wybierz blok **strumień dziennika** , który jest wyświetlany bezpośrednio w bloku **dzienników diagnostycznych** w portalu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1027">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="b1d7d-1028">Utwórz żądanie do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1028">Make a request to the app.</span></span>
1. <span data-ttu-id="b1d7d-1029">W danych strumienia dziennika jest wskazywana Przyczyna błędu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1029">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="b1d7d-1030">Należy pamiętać o wyłączeniu rejestrowania stdout po zakończeniu rozwiązywania problemów.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1030">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="b1d7d-1031">Aby wyświetlić dzienniki śledzenia niepomyślnych żądań (dzienniki FREB):</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1031">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="b1d7d-1032">Przejdź do bloku **diagnozowanie i rozwiązywanie problemów** w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1032">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="b1d7d-1033">Wybierz pozycję **dzienniki śledzenia niepomyślnych żądań** z obszaru **Narzędzia obsługi** na pasku bocznym.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1033">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="b1d7d-1034">Zobacz [sekcję śledzenie niepomyślnych żądań w temacie Włączanie rejestrowania diagnostyki dla aplikacji sieci Web w programie Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) i informacje [o wydajności aplikacji dotyczące Web Apps na platformie Azure: Jak mogę włączyć śledzenie nieudanych żądań?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) Aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1034">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="b1d7d-1035">Aby uzyskać więcej informacji, zobacz [Włączanie rejestrowania diagnostycznego dla aplikacji sieci Web w Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1035">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="b1d7d-1036">Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1036">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="b1d7d-1037">Nie ma żadnego limitu rozmiaru pliku dziennika lub liczby utworzonych plików dziennika.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1037">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="b1d7d-1038">Aby rejestrować procedury w aplikacji ASP.NET Core, użyj biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1038">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="b1d7d-1039">Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1039">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="b1d7d-1040">Rozwiązywanie problemów w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1040">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="b1d7d-1041">Dziennik zdarzeń aplikacji (IIS)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1041">Application Event Log (IIS)</span></span>

<span data-ttu-id="b1d7d-1042">Dostęp do dziennika zdarzeń aplikacji:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1042">Access the Application Event Log:</span></span>

1. <span data-ttu-id="b1d7d-1043">Otwórz menu Start, wyszukaj ciąg *Podgląd zdarzeń*i wybierz aplikację **Podgląd zdarzeń** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1043">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="b1d7d-1044">W **Podgląd zdarzeń**Otwórz węzeł **Dzienniki systemu Windows** .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1044">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="b1d7d-1045">Wybierz pozycję **aplikacja** , aby otworzyć dziennik zdarzeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1045">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="b1d7d-1046">Wyszukaj błędy związane z niepowodzeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1046">Search for errors associated with the failing app.</span></span> <span data-ttu-id="b1d7d-1047">Błędy mają wartość *modułu AspNetCore IIS* lub *moduł IIS Express AspNetCore* w kolumnie *Źródło* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1047">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="b1d7d-1048">Uruchamianie aplikacji w wierszu polecenia</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1048">Run the app at a command prompt</span></span>

<span data-ttu-id="b1d7d-1049">Wiele błędów uruchamiania nie tworzy użytecznych informacji w dzienniku zdarzeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1049">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="b1d7d-1050">Przyczynę niektórych błędów można znaleźć, uruchamiając aplikację w wierszu polecenia w systemie hostingu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1050">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="b1d7d-1051">Wdrożenie zależne od platformy</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1051">Framework-dependent deployment</span></span>

<span data-ttu-id="b1d7d-1052">Jeśli aplikacja jest [wdrożeniem zależnym od platformy](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1052">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="b1d7d-1053">W wierszu polecenia przejdź do folderu wdrożenia i uruchom aplikację, wykonując zestaw aplikacji za pomocą *dotnet.exe*.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1053">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="b1d7d-1054">W poniższym poleceniu Zastąp nazwę zestawu aplikacji dla \<assembly_name> : `dotnet .\<assembly_name>.dll` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1054">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="b1d7d-1055">Dane wyjściowe konsoli z aplikacji, pokazujące błędy, są zapisywane w oknie konsoli.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1055">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="b1d7d-1056">Jeśli wystąpią błędy podczas wysyłania żądania do aplikacji, należy wysłać żądanie do hosta i portu, gdzie Kestrel nasłuchuje.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1056">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="b1d7d-1057">Przy użyciu domyślnego hosta i wpisu Wyślij żądanie do `http://localhost:5000/` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1057">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="b1d7d-1058">Jeśli aplikacja będzie odpowiadać zwykle na adres punktu końcowego Kestrel, problem jest bardziej prawdopodobnie związany z konfiguracją hostingu i mniej korzystnie w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1058">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="b1d7d-1059">Wdrażanie samodzielne</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1059">Self-contained deployment</span></span>

<span data-ttu-id="b1d7d-1060">Jeśli aplikacja jest [wdrożeniem](/dotnet/core/deploying/#self-contained-deployments-scd)niezależnym:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1060">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="b1d7d-1061">W wierszu polecenia przejdź do folderu wdrożenia i uruchom plik wykonywalny aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1061">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="b1d7d-1062">W poniższym poleceniu Zastąp nazwę zestawu aplikacji dla \<assembly_name> : `<assembly_name>.exe` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1062">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="b1d7d-1063">Dane wyjściowe konsoli z aplikacji, pokazujące błędy, są zapisywane w oknie konsoli.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1063">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="b1d7d-1064">Jeśli wystąpią błędy podczas wysyłania żądania do aplikacji, należy wysłać żądanie do hosta i portu, gdzie Kestrel nasłuchuje.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1064">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="b1d7d-1065">Przy użyciu domyślnego hosta i wpisu Wyślij żądanie do `http://localhost:5000/` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1065">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="b1d7d-1066">Jeśli aplikacja będzie odpowiadać zwykle na adres punktu końcowego Kestrel, problem jest bardziej prawdopodobnie związany z konfiguracją hostingu i mniej korzystnie w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1066">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="b1d7d-1067">Dziennik stdout modułu ASP.NET Core (IIS)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1067">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="b1d7d-1068">Aby włączyć i wyświetlić dzienniki stdout:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1068">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="b1d7d-1069">Przejdź do folderu wdrożenia lokacji w systemie hostingu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1069">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="b1d7d-1070">Jeśli folder *Logs* nie istnieje, utwórz go.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1070">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="b1d7d-1071">Aby uzyskać instrukcje dotyczące włączania programu MSBuild do automatycznego tworzenia folderu *Logs* w ramach wdrożenia, zobacz temat [Struktura katalogów](xref:host-and-deploy/directory-structure) .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1071">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="b1d7d-1072">Edytuj plik *web.config* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1072">Edit the *web.config* file.</span></span> <span data-ttu-id="b1d7d-1073">Ustaw wartość **stdoutLogEnabled** na `true` i zmień ścieżkę **stdoutLogFile** , tak aby wskazywała folder *Logs* (na przykład `.\logs\stdout` ).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1073">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="b1d7d-1074">`stdout` ścieżka jest prefiksem nazwy pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1074">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="b1d7d-1075">Sygnatura czasowa, identyfikator procesu i rozszerzenie pliku są dodawane automatycznie podczas tworzenia dziennika.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1075">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="b1d7d-1076">Używając `stdout` jako prefiksu nazwy pliku, typowy plik dziennika ma nazwę *stdout_20180205184032_5412. log*.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1076">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="b1d7d-1077">Upewnij się, że tożsamość puli aplikacji ma uprawnienia do zapisu w folderze *Logs* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1077">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="b1d7d-1078">Zapisz zaktualizowany plik *web.config* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1078">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="b1d7d-1079">Utwórz żądanie do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1079">Make a request to the app.</span></span>
1. <span data-ttu-id="b1d7d-1080">Przejdź do folderu *Logs* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1080">Navigate to the *logs* folder.</span></span> <span data-ttu-id="b1d7d-1081">Znajdź i Otwórz najnowszy dziennik stdout.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1081">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="b1d7d-1082">Zbadaj dziennik pod kątem błędów.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1082">Study the log for errors.</span></span>

<span data-ttu-id="b1d7d-1083">Wyłącz rejestrowanie stdout po zakończeniu rozwiązywania problemów:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1083">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="b1d7d-1084">Edytuj plik *web.config* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1084">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="b1d7d-1085">Ustaw wartość **stdoutLogEnabled** na `false` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1085">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="b1d7d-1086">Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1086">Save the file.</span></span>

<span data-ttu-id="b1d7d-1087">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1087">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="b1d7d-1088">Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1088">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="b1d7d-1089">Nie ma żadnego limitu rozmiaru pliku dziennika lub liczby utworzonych plików dziennika.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1089">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="b1d7d-1090">Aby rejestrować procedury w aplikacji ASP.NET Core, użyj biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1090">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="b1d7d-1091">Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1091">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="b1d7d-1092">Włącz stronę wyjątków dla deweloperów</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1092">Enable the Developer Exception Page</span></span>

<span data-ttu-id="b1d7d-1093">`ASPNETCORE_ENVIRONMENT` [Zmienną środowiskową można dodać do web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) , aby uruchomić aplikację w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1093">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="b1d7d-1094">O ile środowisko nie zostanie zastąpione podczas uruchamiania aplikacji przez `UseEnvironment` konstruktora hosta, ustawienie zmiennej środowiskowej umożliwia wyświetlenie [strony wyjątku dewelopera](xref:fundamentals/error-handling) po uruchomieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1094">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

<span data-ttu-id="b1d7d-1095">Ustawienie zmiennej środowiskowej dla programu `ASPNETCORE_ENVIRONMENT` jest zalecane wyłącznie do użytku na przejściowych i testowych serwerach, które nie są dostępne w Internecie.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1095">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="b1d7d-1096">Usuń zmienną środowiskową z pliku *web.config* po rozwiązaniu problemu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1096">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="b1d7d-1097">Aby uzyskać informacje na temat ustawiania zmiennych środowiskowych w *web.config*, zobacz [EnvironmentVariables podrzędny element aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1097">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="b1d7d-1098">Uzyskiwanie danych z aplikacji</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1098">Obtain data from an app</span></span>

<span data-ttu-id="b1d7d-1099">Jeśli aplikacja może odpowiadać na żądania, uzyskiwać żądania, połączenia i dodatkowe dane z aplikacji przy użyciu wbudowanego oprogramowania terminala.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1099">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="b1d7d-1100">Aby uzyskać więcej informacji i przykładowy kod, zobacz <xref:test/troubleshoot#obtain-data-from-an-app> .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1100">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="b1d7d-1101">Aplikacja wolna lub wysunięta (IIS)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1101">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="b1d7d-1102">*Zrzut awaryjny* to migawka pamięci systemu, która może pomóc w ustaleniu przyczyny awarii aplikacji, awarii uruchamiania lub powolnej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1102">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="b1d7d-1103">Awaria aplikacji lub napotka wyjątek</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1103">App crashes or encounters an exception</span></span>

<span data-ttu-id="b1d7d-1104">Uzyskaj i Analizuj Zrzut z [raportowanie błędów systemu Windows (raportowanie błędów systemu Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1104">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="b1d7d-1105">Utwórz folder do przechowywania plików zrzutu awaryjnego w `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1105">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="b1d7d-1106">Pula aplikacji musi mieć dostęp do zapisu w folderze.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1106">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="b1d7d-1107">Uruchom [skrypt programu PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)w programie EnableDumps:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1107">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="b1d7d-1108">Jeśli aplikacja korzysta z [modelu hostingu w procesie](xref:host-and-deploy/iis/index#in-process-hosting-model), uruchom skrypt dla *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1108">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="b1d7d-1109">Jeśli aplikacja korzysta z [modelu hostingu poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), uruchom skrypt dla *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1109">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="b1d7d-1110">Uruchom aplikację w warunkach, które powodują awarię.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1110">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="b1d7d-1111">Po wystąpieniu awarii Uruchom [skrypt programu DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1111">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="b1d7d-1112">Jeśli aplikacja korzysta z [modelu hostingu w procesie](xref:host-and-deploy/iis/index#in-process-hosting-model), uruchom skrypt dla *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1112">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="b1d7d-1113">Jeśli aplikacja korzysta z [modelu hostingu poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), uruchom skrypt dla *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1113">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="b1d7d-1114">Po awarii aplikacji i zakończeniu zbierania zrzutów aplikacja może zakończyć normalne działanie.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1114">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="b1d7d-1115">Skrypt programu PowerShell konfiguruje raportowanie błędów systemu Windows w celu zebrania do pięciu zrzutów na aplikację.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1115">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="b1d7d-1116">Zrzuty awaryjne mogą wymagać dużej ilości miejsca na dysku (do kilku gigabajtów).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1116">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="b1d7d-1117">Aplikacja zawiesza się, kończy się niepowodzeniem podczas uruchamiania lub działa normalnie</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1117">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="b1d7d-1118">Gdy aplikacja *zawiesza* się (bez awarii), kończy się niepowodzeniem podczas uruchamiania lub działa normalnie, zobacz [pliki zrzutu w trybie użytkownika: wybór najlepszego narzędzia](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) do wygenerowania zrzutu.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1118">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="b1d7d-1119">Analizowanie zrzutu</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1119">Analyze the dump</span></span>

<span data-ttu-id="b1d7d-1120">Zrzut można analizować przy użyciu kilku metod.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1120">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="b1d7d-1121">Aby uzyskać więcej informacji, zobacz [Analizowanie pliku zrzutu w trybie użytkownika](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1121">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="b1d7d-1122">Wyczyść pamięć podręczną pakietów</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1122">Clear package caches</span></span>

<span data-ttu-id="b1d7d-1123">Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestaw .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w ramach aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1123">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="b1d7d-1124">W niektórych przypadkach niespójne pakiety mogą przerwać aplikację podczas przeprowadzania uaktualnień głównych.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1124">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="b1d7d-1125">Większość tych problemów można naprawić, wykonując następujące instrukcje:</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1125">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="b1d7d-1126">Usuń foldery *bin* i *obj* .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1126">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="b1d7d-1127">Wyczyść pamięć podręczną pakietów, wykonując [wszystkie elementy lokalne usługi NuGet programu dotnet--Wyczyść](/dotnet/core/tools/dotnet-nuget-locals) z poziomu powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1127">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="b1d7d-1128">Czyszczenie pamięci podręcznych pakietów można także wykonać przy użyciu narzędzia [nuget.exe](https://www.nuget.org/downloads) i wykonując polecenie `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1128">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="b1d7d-1129">*nuget.exe* nie jest instalacją w pakiecie z systemem operacyjnym Windows dla komputerów stacjonarnych i należy ją pobrać niezależnie od [witryny sieci Web programu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1129">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="b1d7d-1130">Przywróć i skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1130">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="b1d7d-1131">Usuń wszystkie pliki z folderu wdrożenia na serwerze przed ponownym wdrożeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1131">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b1d7d-1132">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1132">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="b1d7d-1133">Dokumentacja platformy Azure</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1133">Azure documentation</span></span>

* [<span data-ttu-id="b1d7d-1134">Usługa Application Insights dla platformy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1134">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="b1d7d-1135">Sekcja zdalne debugowanie aplikacji sieci Web Rozwiązywanie problemów z aplikacją sieci Web w Azure App Service przy użyciu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1135">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="b1d7d-1136">Omówienie diagnostyki Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1136">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="b1d7d-1137">Jak monitorować aplikacje w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1137">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="b1d7d-1138">Rozwiązywanie problemów z aplikacją sieci Web w Azure App Service przy użyciu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1138">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="b1d7d-1139">Rozwiązywanie problemów z błędami HTTP "502 złej Gateway" i "503 Usługa niedostępna" w usłudze Azure Web Apps</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1139">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="b1d7d-1140">Rozwiązywanie problemów związanych z niską wydajnością aplikacji internetowych w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1140">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="b1d7d-1141">Często zadawane pytania dotyczące wydajności aplikacji dla Web Apps na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1141">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="b1d7d-1142">Piaskownica usługi Azure Web App (ograniczenia wykonywania App Service Runtime)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1142">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="b1d7d-1143">Piątek Azure: Azure App Service środowisko diagnostyczne i rozwiązywania problemów (wideo 12-minutowy)</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1143">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="b1d7d-1144">Dokumentacja programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1144">Visual Studio documentation</span></span>

* [<span data-ttu-id="b1d7d-1145">ASP.NET Core debugowania zdalnego na serwerze IIS na platformie Azure w programie Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1145">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="b1d7d-1146">Zdalne debugowanie ASP.NET Core na zdalnym komputerze IIS w programie Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1146">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="b1d7d-1147">Nauka debugowania przy użyciu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1147">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="b1d7d-1148">Dokumentacja Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1148">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="b1d7d-1149">Debugowanie za pomocą Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b1d7d-1149">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
