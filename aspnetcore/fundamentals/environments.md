---
title: Używanie wielu środowisk na platformie ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak kontrolować zachowanie aplikacji w wielu środowiskach w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/1/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/environments
ms.openlocfilehash: 977d222ed61fa914bd4ffb70e192ef19d4da5c33
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2020
ms.locfileid: "87330663"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="09d82-103">Używanie wielu środowisk na platformie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="09d82-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="09d82-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="09d82-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="09d82-105">ASP.NET Core konfiguruje zachowanie aplikacji na podstawie środowiska uruchomieniowego przy użyciu zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="09d82-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="09d82-106">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="09d82-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="09d82-107">Środowiska</span><span class="sxs-lookup"><span data-stu-id="09d82-107">Environments</span></span>

<span data-ttu-id="09d82-108">Aby określić środowisko uruchomieniowe, ASP.NET Core odczytuje z następujących zmiennych środowiskowych:</span><span class="sxs-lookup"><span data-stu-id="09d82-108">To determine the runtime environment, ASP.NET Core reads from the following environment variables:</span></span>

1. [<span data-ttu-id="09d82-109">DOTNET_ENVIRONMENT</span><span class="sxs-lookup"><span data-stu-id="09d82-109">DOTNET_ENVIRONMENT</span></span>](xref:fundamentals/configuration/index#default-host-configuration)
1. <span data-ttu-id="09d82-110">`ASPNETCORE_ENVIRONMENT`gdy <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="09d82-110">`ASPNETCORE_ENVIRONMENT` when <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> is called.</span></span> <span data-ttu-id="09d82-111">Domyślne wywołania szablonów aplikacji sieci Web ASP.NET Core `ConfigureWebHostDefaults` .</span><span class="sxs-lookup"><span data-stu-id="09d82-111">The default ASP.NET Core web app templates call `ConfigureWebHostDefaults`.</span></span> <span data-ttu-id="09d82-112">`ASPNETCORE_ENVIRONMENT`Wartość jest zastąpień `DOTNET_ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="09d82-112">The `ASPNETCORE_ENVIRONMENT` value overrides `DOTNET_ENVIRONMENT`.</span></span>

<span data-ttu-id="09d82-113">`IHostEnvironment.EnvironmentName`można ustawić na dowolną wartość, ale następujące wartości są dostarczane przez platformę:</span><span class="sxs-lookup"><span data-stu-id="09d82-113">`IHostEnvironment.EnvironmentName` can be set to any value, but the following values are provided by the framework:</span></span>

* <span data-ttu-id="09d82-114"><xref:Microsoft.Extensions.Hosting.Environments.Development>: [launchSettings.jsw](#lsj) zestawach plików `ASPNETCORE_ENVIRONMENT` na `Development` komputerze lokalnym.</span><span class="sxs-lookup"><span data-stu-id="09d82-114"><xref:Microsoft.Extensions.Hosting.Environments.Development> : The [launchSettings.json](#lsj) file sets `ASPNETCORE_ENVIRONMENT` to `Development` on the local machine.</span></span>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="09d82-115"><xref:Microsoft.Extensions.Hosting.Environments.Production>: Wartość domyślna if `DOTNET_ENVIRONMENT` i `ASPNETCORE_ENVIRONMENT` nie została ustawiona.</span><span class="sxs-lookup"><span data-stu-id="09d82-115"><xref:Microsoft.Extensions.Hosting.Environments.Production> : The default if `DOTNET_ENVIRONMENT` and `ASPNETCORE_ENVIRONMENT` have not been set.</span></span>

<span data-ttu-id="09d82-116">Następujący kod:</span><span class="sxs-lookup"><span data-stu-id="09d82-116">The following code:</span></span>

* <span data-ttu-id="09d82-117">Wywołuje [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) `ASPNETCORE_ENVIRONMENT` , gdy jest ustawiony na `Development` .</span><span class="sxs-lookup"><span data-stu-id="09d82-117">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="09d82-118">Wywołuje [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) , gdy wartość `ASPNETCORE_ENVIRONMENT` jest ustawiona na `Staging` , `Production` lub `Staging_2` .</span><span class="sxs-lookup"><span data-stu-id="09d82-118">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set to `Staging`, `Production`, or  `Staging_2`.</span></span>
* <span data-ttu-id="09d82-119"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>Wprowadza do `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="09d82-119">Injects <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="09d82-120">Takie podejście jest przydatne, gdy aplikacja wymaga tylko dostosowania `Startup.Configure` w kilku środowiskach z minimalnymi różnicami kodu na środowisko.</span><span class="sxs-lookup"><span data-stu-id="09d82-120">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>
* <span data-ttu-id="09d82-121">Jest podobny do kodu wygenerowanego przez szablony ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="09d82-121">Is similar to the code generated by the ASP.NET Core templates.</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="09d82-122">[Pomocnik tagu środowiska](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) używa wartości [IHostEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) do dołączania lub wykluczania znaczników do elementu:</span><span class="sxs-lookup"><span data-stu-id="09d82-122">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="09d82-123">[Strona informacje](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) z [przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) zawiera poprzedni znacznik i wyświetla wartość `IWebHostEnvironment.EnvironmentName` .</span><span class="sxs-lookup"><span data-stu-id="09d82-123">The [About page](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) from the [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) includes the preceding markup and displays the value of `IWebHostEnvironment.EnvironmentName`.</span></span>

<span data-ttu-id="09d82-124">W systemach Windows i macOS, zmienne środowiskowe i wartości nie uwzględniają wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="09d82-124">On Windows and macOS, environment variables and values aren't case-sensitive.</span></span> <span data-ttu-id="09d82-125">Zmienne środowiskowe systemu Linux i wartości domyślnie **uwzględniają wielkość** liter.</span><span class="sxs-lookup"><span data-stu-id="09d82-125">Linux environment variables and values are **case-sensitive** by default.</span></span>

### <a name="create-environmentssample"></a><span data-ttu-id="09d82-126">Utwórz EnvironmentsSample</span><span class="sxs-lookup"><span data-stu-id="09d82-126">Create EnvironmentsSample</span></span>

<span data-ttu-id="09d82-127">[Przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) używany w tym dokumencie jest oparty na Razor projekcie stron o nazwie *EnvironmentsSample*.</span><span class="sxs-lookup"><span data-stu-id="09d82-127">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) used in this document is based on a Razor Pages project named *EnvironmentsSample*.</span></span>

<span data-ttu-id="09d82-128">Poniższy kod tworzy i uruchamia aplikację sieci Web o nazwie *EnvironmentsSample*:</span><span class="sxs-lookup"><span data-stu-id="09d82-128">The following code creates and runs a web app named *EnvironmentsSample*:</span></span>

```bash
dotnet new webapp -o EnvironmentsSample
cd EnvironmentsSample
dotnet run --verbosity normal
```

<span data-ttu-id="09d82-129">Po uruchomieniu aplikacji zostaną wyświetlone następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="09d82-129">When the app runs, it displays some of the following output:</span></span>

```bash
Using launch settings from c:\tmp\EnvironmentsSample\Properties\launchSettings.json
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: c:\tmp\EnvironmentsSample
```

<a name="lsj"></a>

### <a name="development-and-launchsettingsjson"></a><span data-ttu-id="09d82-130">Programowanie i launchSettings.jsna</span><span class="sxs-lookup"><span data-stu-id="09d82-130">Development and launchSettings.json</span></span>

<span data-ttu-id="09d82-131">Środowisko programistyczne może włączyć funkcje, które nie powinny być ujawnione w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="09d82-131">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="09d82-132">Na przykład szablony ASP.NET Core umożliwiają [stronie wyjątków dla deweloperów](xref:fundamentals/error-handling#developer-exception-page) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="09d82-132">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="09d82-133">Środowisko do tworzenia maszyn lokalnych można ustawić w *Properties\launchSettings.jsw* pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="09d82-133">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="09d82-134">Wartości środowiskowe ustawione w *launchSettings.jsna* wartościach zastąpienia ustawionych w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="09d82-134">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="09d82-135">*launchSettings.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="09d82-135">The *launchSettings.json* file:</span></span>

* <span data-ttu-id="09d82-136">Jest używana tylko na lokalnym komputerze deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="09d82-136">Is only used on the local development machine.</span></span>
* <span data-ttu-id="09d82-137">Nie jest wdrożony.</span><span class="sxs-lookup"><span data-stu-id="09d82-137">Is not deployed.</span></span>
* <span data-ttu-id="09d82-138">zawiera ustawienia profilu.</span><span class="sxs-lookup"><span data-stu-id="09d82-138">contains profile settings.</span></span>

<span data-ttu-id="09d82-139">Poniższy kod JSON przedstawia *launchSettings.jsw* pliku dla ASP.NET Core sieci Web o nazwie *EnvironmentsSample* utworzone w programie Visual Studio lub `dotnet new` :</span><span class="sxs-lookup"><span data-stu-id="09d82-139">The following JSON shows the *launchSettings.json* file for an ASP.NET Core web projected named *EnvironmentsSample* created with Visual Studio or `dotnet new`:</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettingsCopy.json)]

<span data-ttu-id="09d82-140">Poprzedzające znaczniki zawierają dwa profile:</span><span class="sxs-lookup"><span data-stu-id="09d82-140">The preceding markup contains two profiles:</span></span>

* <span data-ttu-id="09d82-141">`IIS Express`: Profil domyślny używany podczas uruchamiania aplikacji z programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="09d82-141">`IIS Express`: The default profile used when launching the app from Visual Studio.</span></span> <span data-ttu-id="09d82-142">`"commandName"`Klucz ma wartość `"IISExpress"` , dlatego [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) jest serwerem sieci Web.</span><span class="sxs-lookup"><span data-stu-id="09d82-142">The `"commandName"` key has the value `"IISExpress"`, therefore, [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) is the web server.</span></span> <span data-ttu-id="09d82-143">Można ustawić profil uruchamiania dla projektu lub dowolnego innego profilu.</span><span class="sxs-lookup"><span data-stu-id="09d82-143">You can set the launch profile to the project or any other profile included.</span></span> <span data-ttu-id="09d82-144">Na przykład na poniższym obrazie wybranie nazwy projektu powoduje uruchomienie [serwera sieci Web Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="09d82-144">For example, in the image below, selecting the project name launches the [Kestrel web server](xref:fundamentals/servers/kestrel).</span></span>

  ![IIS Express uruchamiania w menu](environments/_static/iisx2.png)
* <span data-ttu-id="09d82-146">`EnvironmentsSample`: Nazwa profilu jest nazwą projektu.</span><span class="sxs-lookup"><span data-stu-id="09d82-146">`EnvironmentsSample`: The profile name is the project name.</span></span> <span data-ttu-id="09d82-147">Ten profil jest używany domyślnie podczas uruchamiania aplikacji za pomocą programu `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="09d82-147">This profile is used by default when launching the app with `dotnet run`.</span></span>  <span data-ttu-id="09d82-148">`"commandName"`Klucz ma wartość, w `"Project"` związku z czym jest uruchamiany [serwer sieci Web Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="09d82-148">The `"commandName"` key has the value `"Project"`, therefore, the [Kestrel web server](xref:fundamentals/servers/kestrel) is launched.</span></span>

<span data-ttu-id="09d82-149">Wartość `commandName` może określać serwer sieci Web do uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="09d82-149">The value of `commandName` can specify the web server to launch.</span></span> <span data-ttu-id="09d82-150">`commandName`może być jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="09d82-150">`commandName` can be any one of the following:</span></span>

* <span data-ttu-id="09d82-151">`IISExpress`: Uruchamia IIS Express.</span><span class="sxs-lookup"><span data-stu-id="09d82-151">`IISExpress` : Launches IIS Express.</span></span>
* <span data-ttu-id="09d82-152">`IIS`: Nie uruchomiono żadnego serwera sieci Web.</span><span class="sxs-lookup"><span data-stu-id="09d82-152">`IIS` : No web server launched.</span></span> <span data-ttu-id="09d82-153">Usługi IIS powinny być dostępne.</span><span class="sxs-lookup"><span data-stu-id="09d82-153">IIS is expected to be available.</span></span>
* <span data-ttu-id="09d82-154">`Project`: Uruchamia Kestrel.</span><span class="sxs-lookup"><span data-stu-id="09d82-154">`Project` : Launches Kestrel.</span></span>

<span data-ttu-id="09d82-155">Karta **debugowanie** właściwości projektu programu Visual Studio zawiera graficzny interfejs użytkownika umożliwiający edytowanie *launchSettings.js* pliku.</span><span class="sxs-lookup"><span data-stu-id="09d82-155">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file.</span></span> <span data-ttu-id="09d82-156">Zmiany wprowadzone w profilach projektu mogą zacząć obowiązywać dopiero po ponownym uruchomieniu serwera sieci Web.</span><span class="sxs-lookup"><span data-stu-id="09d82-156">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="09d82-157">Kestrel musi zostać uruchomiona ponownie, zanim będzie mógł wykryć zmiany wprowadzone w środowisku.</span><span class="sxs-lookup"><span data-stu-id="09d82-157">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

![Ustawienia właściwości projektu zmienne środowiskowe](environments/_static/project-properties-debug.png)

<span data-ttu-id="09d82-159">Następujący *launchSettings.js* pliku zawiera wiele profilów:</span><span class="sxs-lookup"><span data-stu-id="09d82-159">The following *launchSettings.json* file contains multiple profiles:</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettings.json)]

<span data-ttu-id="09d82-160">Można wybrać profile:</span><span class="sxs-lookup"><span data-stu-id="09d82-160">Profiles can be selected:</span></span>

* <span data-ttu-id="09d82-161">Z interfejsu użytkownika programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="09d82-161">From the Visual Studio UI.</span></span>
* <span data-ttu-id="09d82-162">Użycie [`dotnet run`](/dotnet/core/tools/dotnet-run) polecenia w powłoce poleceń z `--launch-profile` opcją ustawioną na nazwę profilu.</span><span class="sxs-lookup"><span data-stu-id="09d82-162">Using the [`dotnet run`](/dotnet/core/tools/dotnet-run) command in a command shell with the `--launch-profile` option set to the profile's name.</span></span> <span data-ttu-id="09d82-163">*To podejście obsługuje tylko profile Kestrel.*</span><span class="sxs-lookup"><span data-stu-id="09d82-163">*This approach only supports Kestrel profiles.*</span></span>

  ```dotnetcli
  dotnet run --launch-profile "SampleApp"
  ```

> [!WARNING]
> <span data-ttu-id="09d82-164">*launchSettings.js* nie należy przechowywać wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="09d82-164">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="09d82-165">[Narzędzia do zarządzania kluczami tajnymi](xref:security/app-secrets) można używać do przechowywania wpisów tajnych na potrzeby lokalnego tworzenia oprogramowania.</span><span class="sxs-lookup"><span data-stu-id="09d82-165">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="09d82-166">W przypadku korzystania z [Visual Studio Code](https://code.visualstudio.com/)zmienne środowiskowe można ustawić w pliku *. programu vscode/launch.js* .</span><span class="sxs-lookup"><span data-stu-id="09d82-166">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="09d82-167">W poniższym przykładzie są ustawiane [zmienne środowiskowe wartości konfiguracji hosta](xref:fundamentals/host/web-host#host-configuration-values):</span><span class="sxs-lookup"><span data-stu-id="09d82-167">The following example sets several [Host configuration values environment variables](xref:fundamentals/host/web-host#host-configuration-values):</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/.vscode/launch.json?range=4-10,32-38)]

<span data-ttu-id="09d82-168">Plik *. programu vscode/launch.jsw* pliku jest używany tylko przez Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="09d82-168">The *.vscode/launch.json* file is only used by Visual Studio Code.</span></span>

### <a name="production"></a><span data-ttu-id="09d82-169">Produkcja</span><span class="sxs-lookup"><span data-stu-id="09d82-169">Production</span></span>

<span data-ttu-id="09d82-170">Środowisko produkcyjne powinno być skonfigurowane w celu zmaksymalizowania zabezpieczeń, [wydajności](xref:performance/performance-best-practices)i niezawodności aplikacji.</span><span class="sxs-lookup"><span data-stu-id="09d82-170">The production environment should be configured to maximize security, [performance](xref:performance/performance-best-practices), and application robustness.</span></span> <span data-ttu-id="09d82-171">Niektóre typowe ustawienia, które różnią się od programowania, to m.in.:</span><span class="sxs-lookup"><span data-stu-id="09d82-171">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="09d82-172">[Buforowanie](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="09d82-172">[Caching](xref:performance/caching/memory).</span></span>
* <span data-ttu-id="09d82-173">Zasoby po stronie klienta są powiązane, zminimalizowanego i mogą być obsługiwane przez sieć CDN.</span><span class="sxs-lookup"><span data-stu-id="09d82-173">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="09d82-174">Wyłączono strony błędów diagnostyki.</span><span class="sxs-lookup"><span data-stu-id="09d82-174">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="09d82-175">Włączono przyjazne strony błędów.</span><span class="sxs-lookup"><span data-stu-id="09d82-175">Friendly error pages enabled.</span></span>
* <span data-ttu-id="09d82-176">Włączono [Rejestrowanie](xref:fundamentals/logging/index) i monitorowanie produkcji.</span><span class="sxs-lookup"><span data-stu-id="09d82-176">Production [logging](xref:fundamentals/logging/index) and monitoring enabled.</span></span> <span data-ttu-id="09d82-177">Na przykład przy użyciu [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="09d82-177">For example, using [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="09d82-178">Ustawianie środowiska</span><span class="sxs-lookup"><span data-stu-id="09d82-178">Set the environment</span></span>

<span data-ttu-id="09d82-179">Często warto ustawić określone środowisko do testowania przy użyciu zmiennej środowiskowej lub ustawienia platformy.</span><span class="sxs-lookup"><span data-stu-id="09d82-179">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="09d82-180">Jeśli środowisko nie jest ustawione, domyślnie jest to `Production` , co spowoduje wyłączenie większości funkcji debugowania.</span><span class="sxs-lookup"><span data-stu-id="09d82-180">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="09d82-181">Metoda ustawiania środowiska zależy od systemu operacyjnego.</span><span class="sxs-lookup"><span data-stu-id="09d82-181">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="09d82-182">Po skompilowaniu hosta ostatnie ustawienie środowiska odczytywane przez aplikację określa środowisko aplikacji.</span><span class="sxs-lookup"><span data-stu-id="09d82-182">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="09d82-183">Nie można zmienić środowiska aplikacji, gdy aplikacja jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="09d82-183">The app's environment can't be changed while the app is running.</span></span>

<span data-ttu-id="09d82-184">Na [stronie informacje](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) z [przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) jest wyświetlana wartość `IWebHostEnvironment.EnvironmentName` .</span><span class="sxs-lookup"><span data-stu-id="09d82-184">The [About page](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) from the [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) displays the value of `IWebHostEnvironment.EnvironmentName`.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="09d82-185">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="09d82-185">Azure App Service</span></span>

<span data-ttu-id="09d82-186">Aby ustawić środowisko w [Azure App Service](https://azure.microsoft.com/services/app-service/), wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="09d82-186">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="09d82-187">Wybierz aplikację z bloku **App Services** .</span><span class="sxs-lookup"><span data-stu-id="09d82-187">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="09d82-188">W grupie **Ustawienia** wybierz blok **Konfiguracja** .</span><span class="sxs-lookup"><span data-stu-id="09d82-188">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="09d82-189">Na karcie **Ustawienia aplikacji** wybierz pozycję **nowe ustawienie aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="09d82-189">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="09d82-190">W oknie **Dodawanie/Edytowanie ustawienia aplikacji** Podaj `ASPNETCORE_ENVIRONMENT` **nazwę**.</span><span class="sxs-lookup"><span data-stu-id="09d82-190">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="09d82-191">W polu **wartość**Podaj środowisko (na przykład `Staging` ).</span><span class="sxs-lookup"><span data-stu-id="09d82-191">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="09d82-192">Zaznacz pole wyboru **ustawienie miejsca wdrożenia** , jeśli chcesz, aby ustawienie środowiska pozostawało w bieżącym gnieździe w przypadku wymiany miejsc wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="09d82-192">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="09d82-193">Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowisk przejściowych w Azure App Service](/azure/app-service/web-sites-staged-publishing) w dokumentacji platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="09d82-193">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="09d82-194">Wybierz **przycisk OK** , aby zamknąć okno **Dodawanie/Edytowanie ustawienia aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="09d82-194">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="09d82-195">Wybierz pozycję **Zapisz** w górnej części bloku **Konfiguracja** .</span><span class="sxs-lookup"><span data-stu-id="09d82-195">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="09d82-196">Azure App Service automatycznie uruchamia ponownie aplikację po dodaniu, zmianie lub usunięciu ustawienia aplikacji w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="09d82-196">Azure App Service automatically restarts the app after an app setting is added, changed, or deleted in the Azure portal.</span></span>

### <a name="windows"></a><span data-ttu-id="09d82-197">Windows</span><span class="sxs-lookup"><span data-stu-id="09d82-197">Windows</span></span>

<span data-ttu-id="09d82-198">Wartości środowiskowe w *launchSettings.jsna* wartościach zastąpienia ustawionych w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="09d82-198">Environment values in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="09d82-199">Aby ustawić `ASPNETCORE_ENVIRONMENT` dla bieżącej sesji, gdy aplikacja zostanie uruchomiona przy użyciu [uruchomienia dotnet](/dotnet/core/tools/dotnet-run), są używane następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="09d82-199">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="09d82-200">**Wiersz polecenia**</span><span class="sxs-lookup"><span data-stu-id="09d82-200">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Staging
dotnet run --no-launch-profile
```

<span data-ttu-id="09d82-201">**Program PowerShell**</span><span class="sxs-lookup"><span data-stu-id="09d82-201">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Staging"
dotnet run --no-launch-profile
```

<span data-ttu-id="09d82-202">Poprzednie polecenie ustawia `ASPNETCORE_ENVIRONMENT` tylko dla procesów uruchomionych z tego okna poleceń.</span><span class="sxs-lookup"><span data-stu-id="09d82-202">The preceding command sets `ASPNETCORE_ENVIRONMENT` only for processes launched from that command window.</span></span>

<span data-ttu-id="09d82-203">Aby ustawić wartość globalnie w systemie Windows, użyj jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="09d82-203">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="09d82-204">Otwórz **Panel sterowania** > **system** > **Zaawansowane ustawienia systemowe** i Dodaj lub Edytuj `ASPNETCORE_ENVIRONMENT` wartość:</span><span class="sxs-lookup"><span data-stu-id="09d82-204">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Zaawansowane właściwości systemu](environments/_static/systemsetting_environment.png)

  ![Zmienna środowiskowa ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="09d82-207">Otwórz wiersz polecenia z uprawnieniami administracyjnymi i Użyj `setx` polecenia lub Otwórz wiersz polecenia administracyjnych programu PowerShell i Użyj `[Environment]::SetEnvironmentVariable` :</span><span class="sxs-lookup"><span data-stu-id="09d82-207">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="09d82-208">**Wiersz polecenia**</span><span class="sxs-lookup"><span data-stu-id="09d82-208">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Staging /M
  ```

  <span data-ttu-id="09d82-209">`/M`Przełącznik wskazuje, aby ustawić zmienną środowiskową na poziomie systemu.</span><span class="sxs-lookup"><span data-stu-id="09d82-209">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="09d82-210">Jeśli `/M` przełącznik nie jest używany, zmienna środowiskowa jest ustawiana dla konta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="09d82-210">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="09d82-211">**Program PowerShell**</span><span class="sxs-lookup"><span data-stu-id="09d82-211">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Staging", "Machine")
  ```

  <span data-ttu-id="09d82-212">`Machine`Wartość opcji wskazuje, aby ustawić zmienną środowiskową na poziomie systemu.</span><span class="sxs-lookup"><span data-stu-id="09d82-212">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="09d82-213">Jeśli wartość opcji jest zmieniana na `User` , zmienna środowiskowa jest ustawiana dla konta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="09d82-213">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="09d82-214">Gdy `ASPNETCORE_ENVIRONMENT` zmienna środowiskowa jest ustawiona globalnie, obowiązuje `dotnet run` w każdym oknie polecenia otwartym po ustawieniu wartości.</span><span class="sxs-lookup"><span data-stu-id="09d82-214">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span> <span data-ttu-id="09d82-215">Wartości środowiskowe w *launchSettings.jsna* wartościach zastąpienia ustawionych w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="09d82-215">Environment values in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="09d82-216">**web.config**</span><span class="sxs-lookup"><span data-stu-id="09d82-216">**web.config**</span></span>

<span data-ttu-id="09d82-217">Aby ustawić `ASPNETCORE_ENVIRONMENT` zmienną środowiskową o *web.config*, zobacz sekcję *Ustawianie zmiennych środowiskowych* w temacie <xref:host-and-deploy/aspnet-core-module#setting-environment-variables> .</span><span class="sxs-lookup"><span data-stu-id="09d82-217">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="09d82-218">**Plik projektu lub profil publikacji**</span><span class="sxs-lookup"><span data-stu-id="09d82-218">**Project file or publish profile**</span></span>

<span data-ttu-id="09d82-219">**W przypadku wdrożeń usług Windows IIS:** Uwzględnij `<EnvironmentName>` Właściwość w [profilu publikacji (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) lub w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="09d82-219">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="09d82-220">To podejście ustawia środowisko w *web.config* po opublikowaniu projektu:</span><span class="sxs-lookup"><span data-stu-id="09d82-220">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="09d82-221">**Na pulę aplikacji IIS**</span><span class="sxs-lookup"><span data-stu-id="09d82-221">**Per IIS Application Pool**</span></span>

<span data-ttu-id="09d82-222">Aby ustawić `ASPNETCORE_ENVIRONMENT` zmienną środowiskową dla aplikacji działającej w izolowanej puli aplikacji (obsługiwanej przez usługi IIS 10,0 lub nowszej), zobacz sekcję *polecenieAppCmd.exe* w temacie [zmienne środowiskowe &lt; &gt; environmentVariables](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) .</span><span class="sxs-lookup"><span data-stu-id="09d82-222">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="09d82-223">Gdy `ASPNETCORE_ENVIRONMENT` zmienna środowiskowa jest ustawiona dla puli aplikacji, jej wartość zastępuje ustawienie na poziomie systemu.</span><span class="sxs-lookup"><span data-stu-id="09d82-223">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

<span data-ttu-id="09d82-224">Podczas hostowania aplikacji w usługach IIS i dodawania lub zmieniania `ASPNETCORE_ENVIRONMENT` zmiennej środowiskowej Użyj jednego z poniższych metod, aby nowa wartość była pobierana przez aplikacje:</span><span class="sxs-lookup"><span data-stu-id="09d82-224">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>

* <span data-ttu-id="09d82-225">Wykonaj `net stop was /y` , a następnie `net start w3svc` z wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="09d82-225">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
* <span data-ttu-id="09d82-226">Uruchom ponownie serwer.</span><span class="sxs-lookup"><span data-stu-id="09d82-226">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="09d82-227">macOS</span><span class="sxs-lookup"><span data-stu-id="09d82-227">macOS</span></span>

<span data-ttu-id="09d82-228">Ustawienie bieżącego środowiska dla macOS można wykonać w trybie online podczas uruchamiania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="09d82-228">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Staging dotnet run
```

<span data-ttu-id="09d82-229">Alternatywnie można ustawić środowisko przy użyciu programu `export` przed uruchomieniem aplikacji:</span><span class="sxs-lookup"><span data-stu-id="09d82-229">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

<span data-ttu-id="09d82-230">Zmienne środowiskowe na poziomie komputera są ustawiane w pliku *. bashrc* lub *. bash_profile* .</span><span class="sxs-lookup"><span data-stu-id="09d82-230">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="09d82-231">Edytuj plik przy użyciu dowolnego edytora tekstu.</span><span class="sxs-lookup"><span data-stu-id="09d82-231">Edit the file using any text editor.</span></span> <span data-ttu-id="09d82-232">Dodaj następującą instrukcję:</span><span class="sxs-lookup"><span data-stu-id="09d82-232">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

#### <a name="linux"></a><span data-ttu-id="09d82-233">Linux</span><span class="sxs-lookup"><span data-stu-id="09d82-233">Linux</span></span>

<span data-ttu-id="09d82-234">W przypadku dystrybucji systemu Linux Użyj `export` polecenia z wiersza polecenia w przypadku ustawień zmiennych opartych na sesji i pliku *bash_profile* dla ustawień środowiska na poziomie komputera.</span><span class="sxs-lookup"><span data-stu-id="09d82-234">For Linux distributions, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="09d82-235">Ustawianie środowiska w kodzie</span><span class="sxs-lookup"><span data-stu-id="09d82-235">Set the environment in code</span></span>

<span data-ttu-id="09d82-236">Wywołanie <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> podczas kompilowania hosta.</span><span class="sxs-lookup"><span data-stu-id="09d82-236">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="09d82-237">Zobacz: <xref:fundamentals/host/generic-host#environmentname>.</span><span class="sxs-lookup"><span data-stu-id="09d82-237">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="09d82-238">Konfiguracja według środowiska</span><span class="sxs-lookup"><span data-stu-id="09d82-238">Configuration by environment</span></span>

<span data-ttu-id="09d82-239">Aby załadować konfigurację według środowiska, zobacz <xref:fundamentals/configuration/index#json-configuration-provider> .</span><span class="sxs-lookup"><span data-stu-id="09d82-239">To load configuration by environment, see <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="09d82-240">Klasy i metody uruchamiania oparte na środowisku</span><span class="sxs-lookup"><span data-stu-id="09d82-240">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="09d82-241">Wsuń IWebHostEnvironment do klasy startowej</span><span class="sxs-lookup"><span data-stu-id="09d82-241">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="09d82-242">Wsuń <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do `Startup` konstruktora.</span><span class="sxs-lookup"><span data-stu-id="09d82-242">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="09d82-243">Takie podejście jest przydatne, gdy aplikacja wymaga konfiguracji `Startup` tylko dla kilku środowisk z minimalnymi różnicami w kodzie dla danego środowiska.</span><span class="sxs-lookup"><span data-stu-id="09d82-243">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="09d82-244">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="09d82-244">In the following example:</span></span>

* <span data-ttu-id="09d82-245">Środowisko jest przechowywane w `_env` polu.</span><span class="sxs-lookup"><span data-stu-id="09d82-245">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="09d82-246">`_env`jest używany w systemie `ConfigureServices` i `Configure` do zastosowania konfiguracji uruchamiania na podstawie środowiska aplikacji.</span><span class="sxs-lookup"><span data-stu-id="09d82-246">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupInject.cs?name=snippet&highlight=3-11)]

### <a name="startup-class-conventions"></a><span data-ttu-id="09d82-247">Konwencje klasy uruchamiania</span><span class="sxs-lookup"><span data-stu-id="09d82-247">Startup class conventions</span></span>

<span data-ttu-id="09d82-248">Po uruchomieniu aplikacji ASP.NET Core, [Klasa startowa](xref:fundamentals/startup) uruchamia aplikację.</span><span class="sxs-lookup"><span data-stu-id="09d82-248">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="09d82-249">Aplikacja może definiować wiele `Startup` klas dla różnych środowisk.</span><span class="sxs-lookup"><span data-stu-id="09d82-249">The app can define multiple `Startup` classes for different environments.</span></span> <span data-ttu-id="09d82-250">Odpowiednia `Startup` Klasa jest wybierana w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="09d82-250">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="09d82-251">Kategoria, której sufiks nazwy jest zgodny z bieżącym środowiskiem, ma priorytet.</span><span class="sxs-lookup"><span data-stu-id="09d82-251">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="09d82-252">Jeśli `Startup{EnvironmentName}` nie odnaleziono zgodnej klasy, `Startup` Klasa jest używana.</span><span class="sxs-lookup"><span data-stu-id="09d82-252">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="09d82-253">Takie podejście jest przydatne, gdy aplikacja wymaga skonfigurowania uruchamiania dla kilku środowisk z wieloma różnicami kodu na środowisko.</span><span class="sxs-lookup"><span data-stu-id="09d82-253">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span> <span data-ttu-id="09d82-254">Typowe aplikacje nie będą potrzebować tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="09d82-254">Typical apps will not need this approach.</span></span>

<span data-ttu-id="09d82-255">Aby zaimplementować klasy oparte na środowisku `Startup` , należy utworzyć `Startup{EnvironmentName}` klasy i klasę Fallback `Startup` :</span><span class="sxs-lookup"><span data-stu-id="09d82-255">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` classes and a fallback `Startup` class:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupClassConventions.cs?name=snippet)]

<span data-ttu-id="09d82-256">Użyj przeciążenia [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) , które akceptuje nazwę zestawu:</span><span class="sxs-lookup"><span data-stu-id="09d82-256">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Program.cs?name=snippet)]

### <a name="startup-method-conventions"></a><span data-ttu-id="09d82-257">Konwencje metody uruchamiania</span><span class="sxs-lookup"><span data-stu-id="09d82-257">Startup method conventions</span></span>

<span data-ttu-id="09d82-258">[Skonfiguruj](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) i [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) , aby obsługiwały wersje formularzy `Configure<EnvironmentName>` i `Configure<EnvironmentName>Services` .</span><span class="sxs-lookup"><span data-stu-id="09d82-258">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="09d82-259">Takie podejście jest przydatne, gdy aplikacja wymaga skonfigurowania uruchamiania dla kilku środowisk z wieloma różnicami kodu na środowisko:</span><span class="sxs-lookup"><span data-stu-id="09d82-259">This approach is useful when the app requires configuring startup for several environments with many code differences per environment:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupMethodConventions.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="09d82-260">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="09d82-260">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* <xref:blazor/fundamentals/environments>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="09d82-261">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="09d82-261">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="09d82-262">ASP.NET Core konfiguruje zachowanie aplikacji na podstawie środowiska uruchomieniowego przy użyciu zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="09d82-262">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="09d82-263">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="09d82-263">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="09d82-264">Środowiska</span><span class="sxs-lookup"><span data-stu-id="09d82-264">Environments</span></span>

<span data-ttu-id="09d82-265">ASP.NET Core odczytuje zmienną środowiskową `ASPNETCORE_ENVIRONMENT` przy uruchamianiu aplikacji i zapisuje wartość w [IHostingEnvironment. EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="09d82-265">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="09d82-266">`ASPNETCORE_ENVIRONMENT`można ustawić na dowolną wartość, ale trzy wartości są dostarczane przez strukturę:</span><span class="sxs-lookup"><span data-stu-id="09d82-266">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="09d82-267"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production>wartooć</span><span class="sxs-lookup"><span data-stu-id="09d82-267"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="09d82-268">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="09d82-268">The preceding code:</span></span>

* <span data-ttu-id="09d82-269">Wywołuje [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) `ASPNETCORE_ENVIRONMENT` , gdy jest ustawiony na `Development` .</span><span class="sxs-lookup"><span data-stu-id="09d82-269">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="09d82-270">Wywołuje [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) , gdy wartość `ASPNETCORE_ENVIRONMENT` jest ustawiona jedną z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="09d82-270">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="09d82-271">[Pomocnik tagu środowiska](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) używa wartości `IHostingEnvironment.EnvironmentName` do dołączania lub wykluczania znaczników w elemencie:</span><span class="sxs-lookup"><span data-stu-id="09d82-271">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="09d82-272">W systemach Windows i macOS, zmienne środowiskowe i wartości nie uwzględniają wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="09d82-272">On Windows and macOS, environment variables and values aren't case-sensitive.</span></span> <span data-ttu-id="09d82-273">Zmienne środowiskowe systemu Linux i wartości domyślnie uwzględniają wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="09d82-273">Linux environment variables and values are case-sensitive by default.</span></span>

### <a name="development"></a><span data-ttu-id="09d82-274">Opracowywanie zawartości</span><span class="sxs-lookup"><span data-stu-id="09d82-274">Development</span></span>

<span data-ttu-id="09d82-275">Środowisko programistyczne może włączyć funkcje, które nie powinny być ujawnione w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="09d82-275">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="09d82-276">Na przykład szablony ASP.NET Core umożliwiają [stronie wyjątków dla deweloperów](xref:fundamentals/error-handling#developer-exception-page) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="09d82-276">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="09d82-277">Środowisko do tworzenia maszyn lokalnych można ustawić w *Properties\launchSettings.jsw* pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="09d82-277">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="09d82-278">Wartości środowiskowe ustawione w *launchSettings.jsna* wartościach zastąpienia ustawionych w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="09d82-278">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="09d82-279">Poniższy kod JSON przedstawia trzy profile z *launchSettings.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="09d82-279">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> <span data-ttu-id="09d82-280">`applicationUrl`Właściwość w *launchSettings.jsna* można określić listę adresów URL serwera.</span><span class="sxs-lookup"><span data-stu-id="09d82-280">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="09d82-281">Użyj średnika między adresami URL na liście:</span><span class="sxs-lookup"><span data-stu-id="09d82-281">Use a semicolon between the URLs in the list:</span></span>
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

<span data-ttu-id="09d82-282">Gdy aplikacja jest uruchamiana z [uruchomieniem dotnet](/dotnet/core/tools/dotnet-run), pierwszy profil z `"commandName": "Project"` jest używany.</span><span class="sxs-lookup"><span data-stu-id="09d82-282">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="09d82-283">Wartość `commandName` Określa serwer sieci Web do uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="09d82-283">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="09d82-284">`commandName`może być jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="09d82-284">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="09d82-285">`Project`(co spowoduje uruchomienie Kestrel)</span><span class="sxs-lookup"><span data-stu-id="09d82-285">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="09d82-286">Gdy aplikacja jest uruchamiana z [uruchomieniem dotnet](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="09d82-286">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="09d82-287">*launchSettings.json* jest odczytywany, jeśli jest dostępny.</span><span class="sxs-lookup"><span data-stu-id="09d82-287">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="09d82-288">`environmentVariables`Ustawienia w *launchSettings.jsprzy* zastępowaniu zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="09d82-288">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="09d82-289">Zostanie wyświetlone środowisko hostingu.</span><span class="sxs-lookup"><span data-stu-id="09d82-289">The hosting environment is displayed.</span></span>

<span data-ttu-id="09d82-290">Następujące dane wyjściowe pokazują aplikację uruchomioną z [uruchomieniem dotnet](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="09d82-290">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="09d82-291">Karta **debugowanie** właściwości projektu programu Visual Studio udostępnia graficzny interfejs użytkownika umożliwiający edytowanie *launchSettings.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="09d82-291">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Ustawienia właściwości projektu zmienne środowiskowe](environments/_static/project-properties-debug.png)

<span data-ttu-id="09d82-293">Zmiany wprowadzone w profilach projektu mogą zacząć obowiązywać dopiero po ponownym uruchomieniu serwera sieci Web.</span><span class="sxs-lookup"><span data-stu-id="09d82-293">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="09d82-294">Kestrel musi zostać uruchomiona ponownie, zanim będzie mógł wykryć zmiany wprowadzone w środowisku.</span><span class="sxs-lookup"><span data-stu-id="09d82-294">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="09d82-295">*launchSettings.js* nie należy przechowywać wpisów tajnych.</span><span class="sxs-lookup"><span data-stu-id="09d82-295">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="09d82-296">[Narzędzia do zarządzania kluczami tajnymi](xref:security/app-secrets) można używać do przechowywania wpisów tajnych na potrzeby lokalnego tworzenia oprogramowania.</span><span class="sxs-lookup"><span data-stu-id="09d82-296">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="09d82-297">W przypadku korzystania z [Visual Studio Code](https://code.visualstudio.com/)zmienne środowiskowe można ustawić w pliku *. programu vscode/launch.js* .</span><span class="sxs-lookup"><span data-stu-id="09d82-297">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="09d82-298">Poniższy przykład ustawia środowisko na `Development` :</span><span class="sxs-lookup"><span data-stu-id="09d82-298">The following example sets the environment to `Development`:</span></span>

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

<span data-ttu-id="09d82-299">Element *. programu vscode/launch.jsw* pliku w projekcie nie jest odczytywany podczas uruchamiania aplikacji `dotnet run` w taki sam sposób, jak *Właściwości/launchSettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="09d82-299">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="09d82-300">Podczas uruchamiania aplikacji w środowisku deweloperskim, która nie ma *launchSettings.jsw* pliku, należy ustawić środowisko przy użyciu zmiennej środowiskowej lub argumentu wiersza polecenia do `dotnet run` polecenia.</span><span class="sxs-lookup"><span data-stu-id="09d82-300">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="09d82-301">Produkcja</span><span class="sxs-lookup"><span data-stu-id="09d82-301">Production</span></span>

<span data-ttu-id="09d82-302">Środowisko produkcyjne powinno być skonfigurowane w celu zmaksymalizowania zabezpieczeń, wydajności i niezawodności aplikacji.</span><span class="sxs-lookup"><span data-stu-id="09d82-302">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="09d82-303">Niektóre typowe ustawienia, które różnią się od programowania, to m.in.:</span><span class="sxs-lookup"><span data-stu-id="09d82-303">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="09d82-304">Pamięć.</span><span class="sxs-lookup"><span data-stu-id="09d82-304">Caching.</span></span>
* <span data-ttu-id="09d82-305">Zasoby po stronie klienta są powiązane, zminimalizowanego i mogą być obsługiwane przez sieć CDN.</span><span class="sxs-lookup"><span data-stu-id="09d82-305">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="09d82-306">Wyłączono strony błędów diagnostyki.</span><span class="sxs-lookup"><span data-stu-id="09d82-306">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="09d82-307">Włączono przyjazne strony błędów.</span><span class="sxs-lookup"><span data-stu-id="09d82-307">Friendly error pages enabled.</span></span>
* <span data-ttu-id="09d82-308">Włączono rejestrowanie i monitorowanie produkcji.</span><span class="sxs-lookup"><span data-stu-id="09d82-308">Production logging and monitoring enabled.</span></span> <span data-ttu-id="09d82-309">Na przykład [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="09d82-309">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="09d82-310">Ustawianie środowiska</span><span class="sxs-lookup"><span data-stu-id="09d82-310">Set the environment</span></span>

<span data-ttu-id="09d82-311">Często warto ustawić określone środowisko do testowania przy użyciu zmiennej środowiskowej lub ustawienia platformy.</span><span class="sxs-lookup"><span data-stu-id="09d82-311">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="09d82-312">Jeśli środowisko nie jest ustawione, domyślnie jest to `Production` , co spowoduje wyłączenie większości funkcji debugowania.</span><span class="sxs-lookup"><span data-stu-id="09d82-312">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="09d82-313">Metoda ustawiania środowiska zależy od systemu operacyjnego.</span><span class="sxs-lookup"><span data-stu-id="09d82-313">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="09d82-314">Po skompilowaniu hosta ostatnie ustawienie środowiska odczytywane przez aplikację określa środowisko aplikacji.</span><span class="sxs-lookup"><span data-stu-id="09d82-314">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="09d82-315">Nie można zmienić środowiska aplikacji, gdy aplikacja jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="09d82-315">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="09d82-316">Zmienna środowiskowa lub ustawienie platformy</span><span class="sxs-lookup"><span data-stu-id="09d82-316">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="09d82-317">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="09d82-317">Azure App Service</span></span>

<span data-ttu-id="09d82-318">Aby ustawić środowisko w [Azure App Service](https://azure.microsoft.com/services/app-service/), wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="09d82-318">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="09d82-319">Wybierz aplikację z bloku **App Services** .</span><span class="sxs-lookup"><span data-stu-id="09d82-319">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="09d82-320">W grupie **Ustawienia** wybierz blok **Konfiguracja** .</span><span class="sxs-lookup"><span data-stu-id="09d82-320">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="09d82-321">Na karcie **Ustawienia aplikacji** wybierz pozycję **nowe ustawienie aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="09d82-321">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="09d82-322">W oknie **Dodawanie/Edytowanie ustawienia aplikacji** Podaj `ASPNETCORE_ENVIRONMENT` **nazwę**.</span><span class="sxs-lookup"><span data-stu-id="09d82-322">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="09d82-323">W polu **wartość**Podaj środowisko (na przykład `Staging` ).</span><span class="sxs-lookup"><span data-stu-id="09d82-323">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="09d82-324">Zaznacz pole wyboru **ustawienie miejsca wdrożenia** , jeśli chcesz, aby ustawienie środowiska pozostawało w bieżącym gnieździe w przypadku wymiany miejsc wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="09d82-324">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="09d82-325">Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowisk przejściowych w Azure App Service](/azure/app-service/web-sites-staged-publishing) w dokumentacji platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="09d82-325">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="09d82-326">Wybierz **przycisk OK** , aby zamknąć okno **Dodawanie/Edytowanie ustawienia aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="09d82-326">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="09d82-327">Wybierz pozycję **Zapisz** w górnej części bloku **Konfiguracja** .</span><span class="sxs-lookup"><span data-stu-id="09d82-327">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="09d82-328">Azure App Service automatycznie uruchamia ponownie aplikację po dodaniu, zmianie lub usunięciu ustawienia aplikacji (zmienna środowiskowa) w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="09d82-328">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="09d82-329">Windows</span><span class="sxs-lookup"><span data-stu-id="09d82-329">Windows</span></span>

<span data-ttu-id="09d82-330">Aby ustawić `ASPNETCORE_ENVIRONMENT` dla bieżącej sesji, gdy aplikacja zostanie uruchomiona przy użyciu [uruchomienia dotnet](/dotnet/core/tools/dotnet-run), są używane następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="09d82-330">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="09d82-331">**Wiersz polecenia**</span><span class="sxs-lookup"><span data-stu-id="09d82-331">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="09d82-332">**Program PowerShell**</span><span class="sxs-lookup"><span data-stu-id="09d82-332">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="09d82-333">Te polecenia zaczną obowiązywać tylko dla bieżącego okna.</span><span class="sxs-lookup"><span data-stu-id="09d82-333">These commands only take effect for the current window.</span></span> <span data-ttu-id="09d82-334">Po zamknięciu okna `ASPNETCORE_ENVIRONMENT` ustawienie przywraca ustawienie domyślne lub wartość maszyny.</span><span class="sxs-lookup"><span data-stu-id="09d82-334">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="09d82-335">Aby ustawić wartość globalnie w systemie Windows, użyj jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="09d82-335">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="09d82-336">Otwórz **Panel sterowania** > **system** > **Zaawansowane ustawienia systemowe** i Dodaj lub Edytuj `ASPNETCORE_ENVIRONMENT` wartość:</span><span class="sxs-lookup"><span data-stu-id="09d82-336">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Zaawansowane właściwości systemu](environments/_static/systemsetting_environment.png)

  ![Zmienna środowiskowa ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="09d82-339">Otwórz wiersz polecenia z uprawnieniami administracyjnymi i Użyj `setx` polecenia lub Otwórz wiersz polecenia administracyjnych programu PowerShell i Użyj `[Environment]::SetEnvironmentVariable` :</span><span class="sxs-lookup"><span data-stu-id="09d82-339">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="09d82-340">**Wiersz polecenia**</span><span class="sxs-lookup"><span data-stu-id="09d82-340">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="09d82-341">`/M`Przełącznik wskazuje, aby ustawić zmienną środowiskową na poziomie systemu.</span><span class="sxs-lookup"><span data-stu-id="09d82-341">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="09d82-342">Jeśli `/M` przełącznik nie jest używany, zmienna środowiskowa jest ustawiana dla konta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="09d82-342">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="09d82-343">**Program PowerShell**</span><span class="sxs-lookup"><span data-stu-id="09d82-343">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="09d82-344">`Machine`Wartość opcji wskazuje, aby ustawić zmienną środowiskową na poziomie systemu.</span><span class="sxs-lookup"><span data-stu-id="09d82-344">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="09d82-345">Jeśli wartość opcji jest zmieniana na `User` , zmienna środowiskowa jest ustawiana dla konta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="09d82-345">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="09d82-346">Gdy `ASPNETCORE_ENVIRONMENT` zmienna środowiskowa jest ustawiona globalnie, obowiązuje `dotnet run` w każdym oknie polecenia otwartym po ustawieniu wartości.</span><span class="sxs-lookup"><span data-stu-id="09d82-346">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="09d82-347">**web.config**</span><span class="sxs-lookup"><span data-stu-id="09d82-347">**web.config**</span></span>

<span data-ttu-id="09d82-348">Aby ustawić `ASPNETCORE_ENVIRONMENT` zmienną środowiskową o *web.config*, zobacz sekcję *Ustawianie zmiennych środowiskowych* w temacie <xref:host-and-deploy/aspnet-core-module#setting-environment-variables> .</span><span class="sxs-lookup"><span data-stu-id="09d82-348">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="09d82-349">**Plik projektu lub profil publikacji**</span><span class="sxs-lookup"><span data-stu-id="09d82-349">**Project file or publish profile**</span></span>

<span data-ttu-id="09d82-350">**W przypadku wdrożeń usług Windows IIS:** Uwzględnij `<EnvironmentName>` Właściwość w [profilu publikacji (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) lub w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="09d82-350">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="09d82-351">To podejście ustawia środowisko w *web.config* po opublikowaniu projektu:</span><span class="sxs-lookup"><span data-stu-id="09d82-351">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="09d82-352">**Na pulę aplikacji IIS**</span><span class="sxs-lookup"><span data-stu-id="09d82-352">**Per IIS Application Pool**</span></span>

<span data-ttu-id="09d82-353">Aby ustawić `ASPNETCORE_ENVIRONMENT` zmienną środowiskową dla aplikacji działającej w izolowanej puli aplikacji (obsługiwanej przez usługi IIS 10,0 lub nowszej), zobacz sekcję *polecenieAppCmd.exe* w temacie [zmienne środowiskowe &lt; &gt; environmentVariables](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) .</span><span class="sxs-lookup"><span data-stu-id="09d82-353">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="09d82-354">Gdy `ASPNETCORE_ENVIRONMENT` zmienna środowiskowa jest ustawiona dla puli aplikacji, jej wartość zastępuje ustawienie na poziomie systemu.</span><span class="sxs-lookup"><span data-stu-id="09d82-354">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="09d82-355">Podczas hostowania aplikacji w usługach IIS i dodawania lub zmieniania `ASPNETCORE_ENVIRONMENT` zmiennej środowiskowej Użyj jednego z poniższych metod, aby nowa wartość była pobierana przez aplikacje:</span><span class="sxs-lookup"><span data-stu-id="09d82-355">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="09d82-356">Wykonaj `net stop was /y` , a następnie `net start w3svc` z wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="09d82-356">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="09d82-357">Uruchom ponownie serwer.</span><span class="sxs-lookup"><span data-stu-id="09d82-357">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="09d82-358">macOS</span><span class="sxs-lookup"><span data-stu-id="09d82-358">macOS</span></span>

<span data-ttu-id="09d82-359">Ustawienie bieżącego środowiska dla macOS można wykonać w trybie online podczas uruchamiania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="09d82-359">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="09d82-360">Alternatywnie można ustawić środowisko przy użyciu programu `export` przed uruchomieniem aplikacji:</span><span class="sxs-lookup"><span data-stu-id="09d82-360">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="09d82-361">Zmienne środowiskowe na poziomie komputera są ustawiane w pliku *. bashrc* lub *. bash_profile* .</span><span class="sxs-lookup"><span data-stu-id="09d82-361">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="09d82-362">Edytuj plik przy użyciu dowolnego edytora tekstu.</span><span class="sxs-lookup"><span data-stu-id="09d82-362">Edit the file using any text editor.</span></span> <span data-ttu-id="09d82-363">Dodaj następującą instrukcję:</span><span class="sxs-lookup"><span data-stu-id="09d82-363">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="09d82-364">Linux</span><span class="sxs-lookup"><span data-stu-id="09d82-364">Linux</span></span>

<span data-ttu-id="09d82-365">W przypadku dystrybucji systemu Linux Użyj `export` polecenia z wiersza polecenia w przypadku ustawień zmiennych opartych na sesji i pliku *bash_profile* dla ustawień środowiska na poziomie komputera.</span><span class="sxs-lookup"><span data-stu-id="09d82-365">For Linux distributions, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="09d82-366">Ustawianie środowiska w kodzie</span><span class="sxs-lookup"><span data-stu-id="09d82-366">Set the environment in code</span></span>

<span data-ttu-id="09d82-367">Wywołanie <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> podczas kompilowania hosta.</span><span class="sxs-lookup"><span data-stu-id="09d82-367">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="09d82-368">Zobacz: <xref:fundamentals/host/web-host#environment>.</span><span class="sxs-lookup"><span data-stu-id="09d82-368">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="09d82-369">Konfiguracja według środowiska</span><span class="sxs-lookup"><span data-stu-id="09d82-369">Configuration by environment</span></span>

<span data-ttu-id="09d82-370">Aby załadować konfigurację według środowiska, zalecamy:</span><span class="sxs-lookup"><span data-stu-id="09d82-370">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="09d82-371">pliki *AppSettings* (*appSettings. { Environment}. JSON*).</span><span class="sxs-lookup"><span data-stu-id="09d82-371">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="09d82-372">Zobacz: <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="09d82-372">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="09d82-373">Zmienne środowiskowe (ustawiane dla każdego systemu, w którym jest hostowana aplikacja).</span><span class="sxs-lookup"><span data-stu-id="09d82-373">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="09d82-374">Zobacz <xref:fundamentals/host/web-host#environment> i <xref:security/app-secrets#environment-variables> .</span><span class="sxs-lookup"><span data-stu-id="09d82-374">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="09d82-375">Secret Manager (tylko w środowisku programistycznym).</span><span class="sxs-lookup"><span data-stu-id="09d82-375">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="09d82-376">Zobacz: <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="09d82-376">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="09d82-377">Klasy i metody uruchamiania oparte na środowisku</span><span class="sxs-lookup"><span data-stu-id="09d82-377">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="09d82-378">Wsuń IHostingEnvironment do Startup.Configuruj</span><span class="sxs-lookup"><span data-stu-id="09d82-378">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="09d82-379">Wsuń <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> do `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="09d82-379">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="09d82-380">Takie podejście jest przydatne, gdy aplikacja wymaga konfiguracji tylko `Startup.Configure` dla kilku środowisk z minimalnymi różnicami w kodzie dla danego środowiska.</span><span class="sxs-lookup"><span data-stu-id="09d82-380">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="09d82-381">Wsuń IHostingEnvironment do klasy startowej</span><span class="sxs-lookup"><span data-stu-id="09d82-381">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="09d82-382">Wsuń <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> do `Startup` konstruktora i przypisz usługę do pola do użycia w całej `Startup` klasie.</span><span class="sxs-lookup"><span data-stu-id="09d82-382">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="09d82-383">Takie podejście jest przydatne, gdy aplikacja wymaga konfiguracji uruchamiania tylko dla kilku środowisk z minimalnymi różnicami w kodzie dla danego środowiska.</span><span class="sxs-lookup"><span data-stu-id="09d82-383">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="09d82-384">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="09d82-384">In the following example:</span></span>

* <span data-ttu-id="09d82-385">Środowisko jest przechowywane w `_env` polu.</span><span class="sxs-lookup"><span data-stu-id="09d82-385">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="09d82-386">`_env`jest używany w systemie `ConfigureServices` i `Configure` do zastosowania konfiguracji uruchamiania na podstawie środowiska aplikacji.</span><span class="sxs-lookup"><span data-stu-id="09d82-386">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IHostingEnvironment _env;

    public Startup(IHostingEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```

### <a name="startup-class-conventions"></a><span data-ttu-id="09d82-387">Konwencje klasy uruchamiania</span><span class="sxs-lookup"><span data-stu-id="09d82-387">Startup class conventions</span></span>

<span data-ttu-id="09d82-388">Po uruchomieniu aplikacji ASP.NET Core, [Klasa startowa](xref:fundamentals/startup) uruchamia aplikację.</span><span class="sxs-lookup"><span data-stu-id="09d82-388">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="09d82-389">Aplikacja może definiować oddzielne `Startup` klasy dla różnych środowisk (na przykład `StartupDevelopment` ).</span><span class="sxs-lookup"><span data-stu-id="09d82-389">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="09d82-390">Odpowiednia `Startup` Klasa jest wybierana w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="09d82-390">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="09d82-391">Kategoria, której sufiks nazwy jest zgodny z bieżącym środowiskiem, ma priorytet.</span><span class="sxs-lookup"><span data-stu-id="09d82-391">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="09d82-392">Jeśli `Startup{EnvironmentName}` nie odnaleziono zgodnej klasy, `Startup` Klasa jest używana.</span><span class="sxs-lookup"><span data-stu-id="09d82-392">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="09d82-393">Takie podejście jest przydatne, gdy aplikacja wymaga skonfigurowania uruchamiania dla kilku środowisk z wieloma różnicami kodu na środowisko.</span><span class="sxs-lookup"><span data-stu-id="09d82-393">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="09d82-394">Aby zaimplementować klasy oparte na środowisku `Startup` , należy utworzyć `Startup{EnvironmentName}` klasę dla każdego używanego środowiska i klasy rezerwowej `Startup` :</span><span class="sxs-lookup"><span data-stu-id="09d82-394">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

<span data-ttu-id="09d82-395">Użyj przeciążenia [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) , które akceptuje nazwę zestawu:</span><span class="sxs-lookup"><span data-stu-id="09d82-395">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a><span data-ttu-id="09d82-396">Konwencje metody uruchamiania</span><span class="sxs-lookup"><span data-stu-id="09d82-396">Startup method conventions</span></span>

<span data-ttu-id="09d82-397">[Skonfiguruj](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) i [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) , aby obsługiwały wersje formularzy `Configure<EnvironmentName>` i `Configure<EnvironmentName>Services` .</span><span class="sxs-lookup"><span data-stu-id="09d82-397">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="09d82-398">Takie podejście jest przydatne, gdy aplikacja wymaga skonfigurowania uruchamiania dla kilku środowisk z wieloma różnicami kodu na środowisko.</span><span class="sxs-lookup"><span data-stu-id="09d82-398">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="09d82-399">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="09d82-399">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
