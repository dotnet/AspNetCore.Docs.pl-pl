---
title: Używanie wielu środowisk na platformie ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak kontrolować zachowanie aplikacji w wielu środowiskach w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/17/2019
uid: fundamentals/environments
ms.openlocfilehash: b0218b2c77c283c0849dca9491046534b88c5a77
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656220"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="71854-103">Używanie wielu środowisk na platformie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71854-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71854-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="71854-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="71854-105">ASP.NET Core konfiguruje zachowanie aplikacji na podstawie środowiska wykonawczego przy użyciu zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="71854-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="71854-106">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="71854-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="71854-107">Środowiska</span><span class="sxs-lookup"><span data-stu-id="71854-107">Environments</span></span>

<span data-ttu-id="71854-108">ASP.NET Core odczytuje zmienną `ASPNETCORE_ENVIRONMENT` środowiskową podczas uruchamiania aplikacji i przechowuje wartość w [pliku IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="71854-108">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span></span> <span data-ttu-id="71854-109">`ASPNETCORE_ENVIRONMENT`można ustawić na dowolną wartość, ale trzy wartości są dostarczane przez framework:</span><span class="sxs-lookup"><span data-stu-id="71854-109">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.Extensions.Hosting.Environments.Development>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="71854-110"><xref:Microsoft.Extensions.Hosting.Environments.Production>(domyślnie)</span><span class="sxs-lookup"><span data-stu-id="71854-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="71854-111">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="71854-111">The preceding code:</span></span>

* <span data-ttu-id="71854-112">Wywołania [UseDeveloperExceptionPage,](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) `ASPNETCORE_ENVIRONMENT` `Development`gdy jest ustawiona na .</span><span class="sxs-lookup"><span data-stu-id="71854-112">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="71854-113">Wywołuje [UseExceptionHandler,](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) gdy `ASPNETCORE_ENVIRONMENT` wartość jest ustawiona jeden z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="71854-113">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="71854-114">[Pomocnik znaczników środowiska](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) używa `IHostingEnvironment.EnvironmentName` wartości, aby uwzględnić lub wykluczyć znaczniki w elemencie:</span><span class="sxs-lookup"><span data-stu-id="71854-114">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="71854-115">W systemach Windows i macOS zmienne i wartości środowiskowe nie są rozróżniane.</span><span class="sxs-lookup"><span data-stu-id="71854-115">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="71854-116">Zmienne i wartości środowiskowe systemu Linux są domyślnie **rozróżniane wielkość liter.**</span><span class="sxs-lookup"><span data-stu-id="71854-116">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="71854-117">Opracowywanie zawartości</span><span class="sxs-lookup"><span data-stu-id="71854-117">Development</span></span>

<span data-ttu-id="71854-118">Środowisko programistyczne można włączyć funkcje, które nie powinny być udostępniane w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="71854-118">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="71854-119">Na przykład szablony ASP.NET Core włączyć [stronę wyjątków dewelopera](xref:fundamentals/error-handling#developer-exception-page) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="71854-119">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="71854-120">Środowisko lokalnego rozwoju maszyny można ustawić w pliku *Properties\launchSettings.json* projektu.</span><span class="sxs-lookup"><span data-stu-id="71854-120">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="71854-121">Wartości środowiska ustawione w *launchSettings.json* zastępują wartości ustawione w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="71854-121">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="71854-122">Następujący JSON pokazuje trzy profile z pliku *launchSettings.json:*</span><span class="sxs-lookup"><span data-stu-id="71854-122">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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
> <span data-ttu-id="71854-123">Właściwość `applicationUrl` w *launchSettings.json* może określić listę adresów URL serwera.</span><span class="sxs-lookup"><span data-stu-id="71854-123">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="71854-124">Użyj średnika między adresami URL na liście:</span><span class="sxs-lookup"><span data-stu-id="71854-124">Use a semicolon between the URLs in the list:</span></span>
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

<span data-ttu-id="71854-125">Po uruchomieniu aplikacji z [dotnet run,](/dotnet/core/tools/dotnet-run) `"commandName": "Project"` pierwszy profil z jest używany.</span><span class="sxs-lookup"><span data-stu-id="71854-125">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="71854-126">Wartość `commandName` określa serwer www do uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="71854-126">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="71854-127">`commandName`może być jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="71854-127">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="71854-128">`Project`(który uruchamia Kestrel)</span><span class="sxs-lookup"><span data-stu-id="71854-128">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="71854-129">Po uruchomieniu aplikacji z [dotnet run:](/dotnet/core/tools/dotnet-run)</span><span class="sxs-lookup"><span data-stu-id="71854-129">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="71854-130">*launchSettings.json* jest odczytywany, jeśli jest dostępny.</span><span class="sxs-lookup"><span data-stu-id="71854-130">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="71854-131">`environmentVariables`w *launchSettings.json* zastępuj zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="71854-131">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="71854-132">Zostanie wyświetlone środowisko hostingu.</span><span class="sxs-lookup"><span data-stu-id="71854-132">The hosting environment is displayed.</span></span>

<span data-ttu-id="71854-133">Poniższe dane wyjściowe pokazują aplikację uruchomiono z [dotnet run:](/dotnet/core/tools/dotnet-run)</span><span class="sxs-lookup"><span data-stu-id="71854-133">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="71854-134">Karta **Debugowanie** właściwości projektu programu Visual Studio zawiera interfejs graficzny do edytowania pliku *launchSettings.json:*</span><span class="sxs-lookup"><span data-stu-id="71854-134">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Ustawienie właściwości projektu Zmienne środowiskowe](environments/_static/project-properties-debug.png)

<span data-ttu-id="71854-136">Zmiany wprowadzone w profilach projektów mogą zostać wprowadzone dopiero po ponownym uruchomieniu serwera sieci web.</span><span class="sxs-lookup"><span data-stu-id="71854-136">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="71854-137">Pustułka musi zostać ponownie uruchomiona, zanim będzie mogła wykryć zmiany wprowadzone w jego środowisku.</span><span class="sxs-lookup"><span data-stu-id="71854-137">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="71854-138">*launchSettings.json* nie powinien przechowywać tajemnice.</span><span class="sxs-lookup"><span data-stu-id="71854-138">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="71854-139">[Narzędzie Secret Manager](xref:security/app-secrets) może służyć do przechowywania wpisów tajnych dla rozwoju lokalnego.</span><span class="sxs-lookup"><span data-stu-id="71854-139">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="71854-140">Podczas korzystania z [programu Visual Studio Code,](https://code.visualstudio.com/)zmienne środowiskowe można ustawić w pliku *vscode/launch.json.*</span><span class="sxs-lookup"><span data-stu-id="71854-140">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="71854-141">Poniższy przykład ustawia `Development`środowisko na:</span><span class="sxs-lookup"><span data-stu-id="71854-141">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="71854-142">Plik *.vscode/launch.json* w projekcie nie jest odczytywany `dotnet run` podczas uruchamiania aplikacji w taki sam sposób, jak *właściwości/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="71854-142">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="71854-143">Podczas uruchamiania aplikacji w rozwoju, który nie ma pliku *launchSettings.json,* albo ustawić środowisko ze zmienną środowiskową lub argument wiersza polecenia do `dotnet run` polecenia.</span><span class="sxs-lookup"><span data-stu-id="71854-143">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="71854-144">Produkcja</span><span class="sxs-lookup"><span data-stu-id="71854-144">Production</span></span>

<span data-ttu-id="71854-145">Środowisko produkcyjne należy skonfigurować tak, aby zmaksymalizować bezpieczeństwo, wydajność i niezawodność aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71854-145">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="71854-146">Niektóre typowe ustawienia, które różnią się od rozwoju obejmują:</span><span class="sxs-lookup"><span data-stu-id="71854-146">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="71854-147">Buforowanie.</span><span class="sxs-lookup"><span data-stu-id="71854-147">Caching.</span></span>
* <span data-ttu-id="71854-148">Zasoby po stronie klienta są powiązane, zminifikowane i potencjalnie obsługiwane z sieci CDN.</span><span class="sxs-lookup"><span data-stu-id="71854-148">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="71854-149">Strony błędów diagnostycznych wyłączone.</span><span class="sxs-lookup"><span data-stu-id="71854-149">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="71854-150">Przyjazne strony błędów włączone.</span><span class="sxs-lookup"><span data-stu-id="71854-150">Friendly error pages enabled.</span></span>
* <span data-ttu-id="71854-151">Rejestrowanie produkcji i monitorowanie włączone.</span><span class="sxs-lookup"><span data-stu-id="71854-151">Production logging and monitoring enabled.</span></span> <span data-ttu-id="71854-152">Na przykład [usługa Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="71854-152">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="71854-153">Ustawianie środowiska</span><span class="sxs-lookup"><span data-stu-id="71854-153">Set the environment</span></span>

<span data-ttu-id="71854-154">Często jest przydatne do ustawienia określonego środowiska do testowania przy obliczu zmiennej środowiskowej lub ustawienia platformy.</span><span class="sxs-lookup"><span data-stu-id="71854-154">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="71854-155">Jeśli środowisko nie jest ustawione, domyślnie jest to `Production`, co wyłącza większość funkcji debugowania.</span><span class="sxs-lookup"><span data-stu-id="71854-155">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="71854-156">Metoda ustawiania środowiska zależy od systemu operacyjnego.</span><span class="sxs-lookup"><span data-stu-id="71854-156">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="71854-157">Po utworzeniu hosta ostatnie ustawienie środowiska odczytane przez aplikację określa środowisko aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71854-157">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="71854-158">Nie można zmienić środowiska aplikacji, gdy aplikacja jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="71854-158">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="71854-159">Ustawienie zmiennej środowiskowej lub platformy</span><span class="sxs-lookup"><span data-stu-id="71854-159">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="71854-160">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="71854-160">Azure App Service</span></span>

<span data-ttu-id="71854-161">Aby ustawić środowisko w [usłudze Azure App Service,](https://azure.microsoft.com/services/app-service/)wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="71854-161">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="71854-162">Wybierz aplikację z **bloku Usługi aplikacji.**</span><span class="sxs-lookup"><span data-stu-id="71854-162">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="71854-163">W grupie **Ustawienia** wybierz blok **Konfiguracja.**</span><span class="sxs-lookup"><span data-stu-id="71854-163">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="71854-164">Na karcie **Ustawienia aplikacji** wybierz pozycję Nowe **ustawienie aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="71854-164">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="71854-165">W oknie **ustawień Dodaj/Edytuj** `ASPNETCORE_ENVIRONMENT` aplikację podaj **nazwę**.</span><span class="sxs-lookup"><span data-stu-id="71854-165">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="71854-166">Dla **wartości**, zapewnić środowisko `Staging`(na przykład).</span><span class="sxs-lookup"><span data-stu-id="71854-166">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="71854-167">Zaznacz pole wyboru **Ustawienie gniazda wdrożenia,** jeśli ustawienie środowiska ma pozostać z bieżącym miejscem po zamianie gniazd wdrażania.</span><span class="sxs-lookup"><span data-stu-id="71854-167">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="71854-168">Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowisk przejściowych w usłudze Azure App Service](/azure/app-service/web-sites-staged-publishing) w dokumentacji platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="71854-168">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="71854-169">Wybierz **przycisk OK,** aby zamknąć okno **ustawienia Dodaj/Edytuj aplikację.**</span><span class="sxs-lookup"><span data-stu-id="71854-169">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="71854-170">Wybierz **pozycję Zapisz** u góry bloku **Konfiguracja.**</span><span class="sxs-lookup"><span data-stu-id="71854-170">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="71854-171">Usługa Azure App Service automatycznie uruchamia ponownie aplikację po dodaniu, zmianie lub usunięciu ustawienia aplikacji (zmiennej środowiskowej) w witrynie Azure portal.</span><span class="sxs-lookup"><span data-stu-id="71854-171">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="71854-172">Windows</span><span class="sxs-lookup"><span data-stu-id="71854-172">Windows</span></span>

<span data-ttu-id="71854-173">Aby ustawić `ASPNETCORE_ENVIRONMENT` dla bieżącej sesji, gdy aplikacja jest uruchomiona przy użyciu [dotnet run](/dotnet/core/tools/dotnet-run), używane są następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="71854-173">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="71854-174">**Wiersz polecenia**</span><span class="sxs-lookup"><span data-stu-id="71854-174">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="71854-175">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="71854-175">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="71854-176">Te polecenia są obowiązywać tylko dla bieżącego okna.</span><span class="sxs-lookup"><span data-stu-id="71854-176">These commands only take effect for the current window.</span></span> <span data-ttu-id="71854-177">Gdy okno jest zamknięte, `ASPNETCORE_ENVIRONMENT` ustawienie zostanie przywrócone do domyślnego ustawienia lub wartości maszyny.</span><span class="sxs-lookup"><span data-stu-id="71854-177">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="71854-178">Aby ustawić wartość globalnie w systemie Windows, użyj jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="71854-178">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="71854-179">Otwórz zaawansowane **System** > **ustawienia systemowe** systemu Panelu `ASPNETCORE_ENVIRONMENT` **sterowania** > i dodaj lub edytuj wartość:</span><span class="sxs-lookup"><span data-stu-id="71854-179">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Zaawansowane właściwości systemu](environments/_static/systemsetting_environment.png)

  ![Zmienna środowiska podstawowego ASPNET](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="71854-182">Otwórz wiersz polecenia administracyjnego `setx` i użyj polecenia lub otwórz administracyjny wiersz polecenia programu PowerShell i użyj: `[Environment]::SetEnvironmentVariable`</span><span class="sxs-lookup"><span data-stu-id="71854-182">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="71854-183">**Wiersz polecenia**</span><span class="sxs-lookup"><span data-stu-id="71854-183">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="71854-184">Przełącznik `/M` wskazuje, aby ustawić zmienną środowiskową na poziomie systemu.</span><span class="sxs-lookup"><span data-stu-id="71854-184">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="71854-185">Jeśli `/M` przełącznik nie jest używany, zmienna środowiskowa jest ustawiona dla konta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="71854-185">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="71854-186">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="71854-186">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="71854-187">Wartość `Machine` opcji wskazuje, aby ustawić zmienną środowiskową na poziomie systemu.</span><span class="sxs-lookup"><span data-stu-id="71854-187">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="71854-188">Jeśli wartość opcji zostanie `User`zmieniona na , zmienna środowiskowa jest ustawiana dla konta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="71854-188">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="71854-189">Gdy `ASPNETCORE_ENVIRONMENT` zmienna środowiskowa jest ustawiona `dotnet run` globalnie, staje się skuteczna w każdym oknie polecenia otwartym po ustawieniu wartości.</span><span class="sxs-lookup"><span data-stu-id="71854-189">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="71854-190">**Web.config**</span><span class="sxs-lookup"><span data-stu-id="71854-190">**web.config**</span></span>

<span data-ttu-id="71854-191">Aby ustawić `ASPNETCORE_ENVIRONMENT` zmienną środowiskową za pomocą *pliku web.config,* zobacz sekcję *Ustawianie zmiennych środowiskowych* w pliku <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="71854-191">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="71854-192">**Plik projektu lub profil publikowania**</span><span class="sxs-lookup"><span data-stu-id="71854-192">**Project file or publish profile**</span></span>

<span data-ttu-id="71854-193">**W przypadku wdrożeń usług Windows IIS:** Dołącz `<EnvironmentName>` właściwość do [profilu publikowania (pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) lub pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="71854-193">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="71854-194">Takie podejście ustawia środowisko w *web.config* po opublikowaniu projektu:</span><span class="sxs-lookup"><span data-stu-id="71854-194">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="71854-195">**Pula aplikacji dla usług IIS**</span><span class="sxs-lookup"><span data-stu-id="71854-195">**Per IIS Application Pool**</span></span>

<span data-ttu-id="71854-196">Aby ustawić `ASPNETCORE_ENVIRONMENT` zmienną środowiskową dla aplikacji działającej w izolowanej puli aplikacji (obsługiwanej w serwisach IIS 10.0 lub nowszych), [ &lt;&gt; ](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) zobacz *sekcję polecenia AppCmd.exe* w temacie Środowisko zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="71854-196">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="71854-197">Gdy `ASPNETCORE_ENVIRONMENT` zmienna środowiskowa jest ustawiona dla puli aplikacji, jej wartość zastępuje ustawienie na poziomie systemu.</span><span class="sxs-lookup"><span data-stu-id="71854-197">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="71854-198">Podczas hostowania aplikacji w użyczanych `ASPNETCORE_ENVIRONMENT` usług IIS i dodawania lub zmieniania zmiennej środowiskowej użyj jednego z następujących podejść, aby nowa wartość była pobierana przez aplikacje:</span><span class="sxs-lookup"><span data-stu-id="71854-198">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="71854-199">Wykonaj, `net stop was /y` `net start w3svc` po którym następuje wiersz polecenia.</span><span class="sxs-lookup"><span data-stu-id="71854-199">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="71854-200">Uruchom ponownie serwer.</span><span class="sxs-lookup"><span data-stu-id="71854-200">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="71854-201">macOS</span><span class="sxs-lookup"><span data-stu-id="71854-201">macOS</span></span>

<span data-ttu-id="71854-202">Ustawienie bieżącego środowiska dla systemu macOS można wykonać w wierszu podczas uruchamiania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="71854-202">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="71854-203">Alternatywnie, ustaw środowisko `export` przed uruchomieniem aplikacji:</span><span class="sxs-lookup"><span data-stu-id="71854-203">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="71854-204">Zmienne środowiskowe na poziomie komputera są ustawiane w pliku *bashrc* lub *.bash_profile.*</span><span class="sxs-lookup"><span data-stu-id="71854-204">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="71854-205">Edytuj plik za pomocą dowolnego edytora tekstu.</span><span class="sxs-lookup"><span data-stu-id="71854-205">Edit the file using any text editor.</span></span> <span data-ttu-id="71854-206">Dodaj następującą instrukcję:</span><span class="sxs-lookup"><span data-stu-id="71854-206">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="71854-207">Linux</span><span class="sxs-lookup"><span data-stu-id="71854-207">Linux</span></span>

<span data-ttu-id="71854-208">W przypadku dystrybucji systemu `export` Linux użyj polecenia w wierszu polecenia dla ustawień zmiennych opartych na sesji i *bash_profile* pliku dla ustawień środowiska na poziomie komputera.</span><span class="sxs-lookup"><span data-stu-id="71854-208">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="71854-209">Ustawianie środowiska w kodzie</span><span class="sxs-lookup"><span data-stu-id="71854-209">Set the environment in code</span></span>

<span data-ttu-id="71854-210">Zadzwoń <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> podczas budowania hosta.</span><span class="sxs-lookup"><span data-stu-id="71854-210">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="71854-211">Zobacz: <xref:fundamentals/host/generic-host#environmentname>.</span><span class="sxs-lookup"><span data-stu-id="71854-211">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>


### <a name="configuration-by-environment"></a><span data-ttu-id="71854-212">Konfiguracja według środowiska</span><span class="sxs-lookup"><span data-stu-id="71854-212">Configuration by environment</span></span>

<span data-ttu-id="71854-213">Aby załadować konfigurację według środowiska, zalecamy:</span><span class="sxs-lookup"><span data-stu-id="71854-213">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="71854-214">*appsettings* plików (*appsettings.{ środowiska}.json*).</span><span class="sxs-lookup"><span data-stu-id="71854-214">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="71854-215">Zobacz: <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="71854-215">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="71854-216">Zmienne środowiskowe (ustawione w każdym systemie, w którym aplikacja jest hostowana).</span><span class="sxs-lookup"><span data-stu-id="71854-216">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="71854-217">Zobacz <xref:fundamentals/host/generic-host#environmentname> <xref:security/app-secrets#environment-variables>i .</span><span class="sxs-lookup"><span data-stu-id="71854-217">See <xref:fundamentals/host/generic-host#environmentname> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="71854-218">Secret Manager (tylko w środowisku programistycznym).</span><span class="sxs-lookup"><span data-stu-id="71854-218">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="71854-219">Zobacz: <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="71854-219">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="71854-220">Klasa i metody uruchamiania oparte na środowisku</span><span class="sxs-lookup"><span data-stu-id="71854-220">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-startupconfigure"></a><span data-ttu-id="71854-221">Wstrzyknąć IWebHostEnvironment do Startup.Configure</span><span class="sxs-lookup"><span data-stu-id="71854-221">Inject IWebHostEnvironment into Startup.Configure</span></span>

<span data-ttu-id="71854-222">Wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="71854-222">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="71854-223">Takie podejście jest przydatne, gdy `Startup.Configure` aplikacja wymaga tylko dostosowania do kilku środowisk z minimalnymi różnicami kodu na środowisko.</span><span class="sxs-lookup"><span data-stu-id="71854-223">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
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

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="71854-224">Wstrzyknąć IWebHostEnvironment do startup klasy</span><span class="sxs-lookup"><span data-stu-id="71854-224">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="71854-225">Wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do `Startup` konstruktora.</span><span class="sxs-lookup"><span data-stu-id="71854-225">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="71854-226">Takie podejście jest przydatne, gdy `Startup` aplikacja wymaga skonfigurowania tylko dla kilku środowisk z minimalnymi różnicami kodu na środowisko.</span><span class="sxs-lookup"><span data-stu-id="71854-226">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="71854-227">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="71854-227">In the following example:</span></span>

* <span data-ttu-id="71854-228">Środowisko odbywa się `_env` w terenie.</span><span class="sxs-lookup"><span data-stu-id="71854-228">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="71854-229">`_env`jest używany `ConfigureServices` `Configure` w i zastosować konfigurację uruchamiania na podstawie środowiska aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71854-229">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IWebHostEnvironment _env;

    public Startup(IWebHostEnvironment env)
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
### <a name="startup-class-conventions"></a><span data-ttu-id="71854-230">Konwencje klasy uruchamiania</span><span class="sxs-lookup"><span data-stu-id="71854-230">Startup class conventions</span></span>

<span data-ttu-id="71854-231">Po uruchomieniu aplikacji ASP.NET Core, [startup klasy](xref:fundamentals/startup) bootstraps aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71854-231">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="71854-232">Aplikacja może definiować oddzielne `Startup` klasy dla `StartupDevelopment`różnych środowisk (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="71854-232">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="71854-233">Odpowiednia `Startup` klasa jest wybierana w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="71854-233">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="71854-234">Klasa, której sufiks nazwy pasuje do bieżącego środowiska, jest traktowana priorytetowo.</span><span class="sxs-lookup"><span data-stu-id="71854-234">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="71854-235">Jeśli pasująca `Startup{EnvironmentName}` klasa nie zostanie `Startup` znaleziona, klasa jest używana.</span><span class="sxs-lookup"><span data-stu-id="71854-235">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="71854-236">Takie podejście jest przydatne, gdy aplikacja wymaga skonfigurowania uruchamiania dla kilku środowisk z wieloma różnicami kodu na środowisko.</span><span class="sxs-lookup"><span data-stu-id="71854-236">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="71854-237">Aby zaimplementować klasy oparte na `Startup` środowisku, należy utworzyć `Startup{EnvironmentName}` klasę dla każdego używanego środowiska i klasę rezerwową: `Startup`</span><span class="sxs-lookup"><span data-stu-id="71854-237">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

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

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="71854-238">Użyj [przeciążenia UseStartup(IWebHostBuilder, String),](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) który akceptuje nazwę zestawu:</span><span class="sxs-lookup"><span data-stu-id="71854-238">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

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

### <a name="startup-method-conventions"></a><span data-ttu-id="71854-239">Konwencje metody uruchamiania</span><span class="sxs-lookup"><span data-stu-id="71854-239">Startup method conventions</span></span>

<span data-ttu-id="71854-240">[Konfigurowanie](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) i [konfigurowanie Usługi](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) obsługują wersje formularza `Configure<EnvironmentName>` `Configure<EnvironmentName>Services`specyficzne dla środowiska i .</span><span class="sxs-lookup"><span data-stu-id="71854-240">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="71854-241">Takie podejście jest przydatne, gdy aplikacja wymaga skonfigurowania uruchamiania dla kilku środowisk z wieloma różnicami kodu na środowisko.</span><span class="sxs-lookup"><span data-stu-id="71854-241">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="71854-242">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="71854-242">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="71854-243">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="71854-243">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="71854-244">ASP.NET Core konfiguruje zachowanie aplikacji na podstawie środowiska wykonawczego przy użyciu zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="71854-244">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="71854-245">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="71854-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="71854-246">Środowiska</span><span class="sxs-lookup"><span data-stu-id="71854-246">Environments</span></span>

<span data-ttu-id="71854-247">ASP.NET Core odczytuje zmienną `ASPNETCORE_ENVIRONMENT` środowiskową podczas uruchamiania aplikacji i przechowuje wartość w [pliku IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="71854-247">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="71854-248">`ASPNETCORE_ENVIRONMENT`można ustawić na dowolną wartość, ale trzy wartości są dostarczane przez framework:</span><span class="sxs-lookup"><span data-stu-id="71854-248">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="71854-249"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production>(domyślnie)</span><span class="sxs-lookup"><span data-stu-id="71854-249"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="71854-250">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="71854-250">The preceding code:</span></span>

* <span data-ttu-id="71854-251">Wywołania [UseDeveloperExceptionPage,](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) `ASPNETCORE_ENVIRONMENT` `Development`gdy jest ustawiona na .</span><span class="sxs-lookup"><span data-stu-id="71854-251">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="71854-252">Wywołuje [UseExceptionHandler,](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) gdy `ASPNETCORE_ENVIRONMENT` wartość jest ustawiona jeden z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="71854-252">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="71854-253">[Pomocnik znaczników środowiska](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) używa `IHostingEnvironment.EnvironmentName` wartości, aby uwzględnić lub wykluczyć znaczniki w elemencie:</span><span class="sxs-lookup"><span data-stu-id="71854-253">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="71854-254">W systemach Windows i macOS zmienne i wartości środowiskowe nie są rozróżniane.</span><span class="sxs-lookup"><span data-stu-id="71854-254">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="71854-255">Zmienne i wartości środowiskowe systemu Linux są domyślnie **rozróżniane wielkość liter.**</span><span class="sxs-lookup"><span data-stu-id="71854-255">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="71854-256">Opracowywanie zawartości</span><span class="sxs-lookup"><span data-stu-id="71854-256">Development</span></span>

<span data-ttu-id="71854-257">Środowisko programistyczne można włączyć funkcje, które nie powinny być udostępniane w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="71854-257">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="71854-258">Na przykład szablony ASP.NET Core włączyć [stronę wyjątków dewelopera](xref:fundamentals/error-handling#developer-exception-page) w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="71854-258">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="71854-259">Środowisko lokalnego rozwoju maszyny można ustawić w pliku *Properties\launchSettings.json* projektu.</span><span class="sxs-lookup"><span data-stu-id="71854-259">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="71854-260">Wartości środowiska ustawione w *launchSettings.json* zastępują wartości ustawione w środowisku systemowym.</span><span class="sxs-lookup"><span data-stu-id="71854-260">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="71854-261">Następujący JSON pokazuje trzy profile z pliku *launchSettings.json:*</span><span class="sxs-lookup"><span data-stu-id="71854-261">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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
> <span data-ttu-id="71854-262">Właściwość `applicationUrl` w *launchSettings.json* może określić listę adresów URL serwera.</span><span class="sxs-lookup"><span data-stu-id="71854-262">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="71854-263">Użyj średnika między adresami URL na liście:</span><span class="sxs-lookup"><span data-stu-id="71854-263">Use a semicolon between the URLs in the list:</span></span>
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

<span data-ttu-id="71854-264">Po uruchomieniu aplikacji z [dotnet run,](/dotnet/core/tools/dotnet-run) `"commandName": "Project"` pierwszy profil z jest używany.</span><span class="sxs-lookup"><span data-stu-id="71854-264">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="71854-265">Wartość `commandName` określa serwer www do uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="71854-265">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="71854-266">`commandName`może być jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="71854-266">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="71854-267">`Project`(który uruchamia Kestrel)</span><span class="sxs-lookup"><span data-stu-id="71854-267">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="71854-268">Po uruchomieniu aplikacji z [dotnet run:](/dotnet/core/tools/dotnet-run)</span><span class="sxs-lookup"><span data-stu-id="71854-268">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="71854-269">*launchSettings.json* jest odczytywany, jeśli jest dostępny.</span><span class="sxs-lookup"><span data-stu-id="71854-269">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="71854-270">`environmentVariables`w *launchSettings.json* zastępuj zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="71854-270">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="71854-271">Zostanie wyświetlone środowisko hostingu.</span><span class="sxs-lookup"><span data-stu-id="71854-271">The hosting environment is displayed.</span></span>

<span data-ttu-id="71854-272">Poniższe dane wyjściowe pokazują aplikację uruchomiono z [dotnet run:](/dotnet/core/tools/dotnet-run)</span><span class="sxs-lookup"><span data-stu-id="71854-272">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="71854-273">Karta **Debugowanie** właściwości projektu programu Visual Studio zawiera interfejs graficzny do edytowania pliku *launchSettings.json:*</span><span class="sxs-lookup"><span data-stu-id="71854-273">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Ustawienie właściwości projektu Zmienne środowiskowe](environments/_static/project-properties-debug.png)

<span data-ttu-id="71854-275">Zmiany wprowadzone w profilach projektów mogą zostać wprowadzone dopiero po ponownym uruchomieniu serwera sieci web.</span><span class="sxs-lookup"><span data-stu-id="71854-275">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="71854-276">Pustułka musi zostać ponownie uruchomiona, zanim będzie mogła wykryć zmiany wprowadzone w jego środowisku.</span><span class="sxs-lookup"><span data-stu-id="71854-276">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="71854-277">*launchSettings.json* nie powinien przechowywać tajemnice.</span><span class="sxs-lookup"><span data-stu-id="71854-277">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="71854-278">[Narzędzie Secret Manager](xref:security/app-secrets) może służyć do przechowywania wpisów tajnych dla rozwoju lokalnego.</span><span class="sxs-lookup"><span data-stu-id="71854-278">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="71854-279">Podczas korzystania z [programu Visual Studio Code,](https://code.visualstudio.com/)zmienne środowiskowe można ustawić w pliku *vscode/launch.json.*</span><span class="sxs-lookup"><span data-stu-id="71854-279">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="71854-280">Poniższy przykład ustawia `Development`środowisko na:</span><span class="sxs-lookup"><span data-stu-id="71854-280">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="71854-281">Plik *.vscode/launch.json* w projekcie nie jest odczytywany `dotnet run` podczas uruchamiania aplikacji w taki sam sposób, jak *właściwości/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="71854-281">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="71854-282">Podczas uruchamiania aplikacji w rozwoju, który nie ma pliku *launchSettings.json,* albo ustawić środowisko ze zmienną środowiskową lub argument wiersza polecenia do `dotnet run` polecenia.</span><span class="sxs-lookup"><span data-stu-id="71854-282">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="71854-283">Produkcja</span><span class="sxs-lookup"><span data-stu-id="71854-283">Production</span></span>

<span data-ttu-id="71854-284">Środowisko produkcyjne należy skonfigurować tak, aby zmaksymalizować bezpieczeństwo, wydajność i niezawodność aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71854-284">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="71854-285">Niektóre typowe ustawienia, które różnią się od rozwoju obejmują:</span><span class="sxs-lookup"><span data-stu-id="71854-285">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="71854-286">Buforowanie.</span><span class="sxs-lookup"><span data-stu-id="71854-286">Caching.</span></span>
* <span data-ttu-id="71854-287">Zasoby po stronie klienta są powiązane, zminifikowane i potencjalnie obsługiwane z sieci CDN.</span><span class="sxs-lookup"><span data-stu-id="71854-287">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="71854-288">Strony błędów diagnostycznych wyłączone.</span><span class="sxs-lookup"><span data-stu-id="71854-288">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="71854-289">Przyjazne strony błędów włączone.</span><span class="sxs-lookup"><span data-stu-id="71854-289">Friendly error pages enabled.</span></span>
* <span data-ttu-id="71854-290">Rejestrowanie produkcji i monitorowanie włączone.</span><span class="sxs-lookup"><span data-stu-id="71854-290">Production logging and monitoring enabled.</span></span> <span data-ttu-id="71854-291">Na przykład [usługa Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="71854-291">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="71854-292">Ustawianie środowiska</span><span class="sxs-lookup"><span data-stu-id="71854-292">Set the environment</span></span>

<span data-ttu-id="71854-293">Często jest przydatne do ustawienia określonego środowiska do testowania przy obliczu zmiennej środowiskowej lub ustawienia platformy.</span><span class="sxs-lookup"><span data-stu-id="71854-293">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="71854-294">Jeśli środowisko nie jest ustawione, domyślnie jest to `Production`, co wyłącza większość funkcji debugowania.</span><span class="sxs-lookup"><span data-stu-id="71854-294">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="71854-295">Metoda ustawiania środowiska zależy od systemu operacyjnego.</span><span class="sxs-lookup"><span data-stu-id="71854-295">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="71854-296">Po utworzeniu hosta ostatnie ustawienie środowiska odczytane przez aplikację określa środowisko aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71854-296">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="71854-297">Nie można zmienić środowiska aplikacji, gdy aplikacja jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="71854-297">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="71854-298">Ustawienie zmiennej środowiskowej lub platformy</span><span class="sxs-lookup"><span data-stu-id="71854-298">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="71854-299">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="71854-299">Azure App Service</span></span>

<span data-ttu-id="71854-300">Aby ustawić środowisko w [usłudze Azure App Service,](https://azure.microsoft.com/services/app-service/)wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="71854-300">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="71854-301">Wybierz aplikację z **bloku Usługi aplikacji.**</span><span class="sxs-lookup"><span data-stu-id="71854-301">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="71854-302">W grupie **Ustawienia** wybierz blok **Konfiguracja.**</span><span class="sxs-lookup"><span data-stu-id="71854-302">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="71854-303">Na karcie **Ustawienia aplikacji** wybierz pozycję Nowe **ustawienie aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="71854-303">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="71854-304">W oknie **ustawień Dodaj/Edytuj** `ASPNETCORE_ENVIRONMENT` aplikację podaj **nazwę**.</span><span class="sxs-lookup"><span data-stu-id="71854-304">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="71854-305">Dla **wartości**, zapewnić środowisko `Staging`(na przykład).</span><span class="sxs-lookup"><span data-stu-id="71854-305">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="71854-306">Zaznacz pole wyboru **Ustawienie gniazda wdrożenia,** jeśli ustawienie środowiska ma pozostać z bieżącym miejscem po zamianie gniazd wdrażania.</span><span class="sxs-lookup"><span data-stu-id="71854-306">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="71854-307">Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowisk przejściowych w usłudze Azure App Service](/azure/app-service/web-sites-staged-publishing) w dokumentacji platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="71854-307">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="71854-308">Wybierz **przycisk OK,** aby zamknąć okno **ustawienia Dodaj/Edytuj aplikację.**</span><span class="sxs-lookup"><span data-stu-id="71854-308">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="71854-309">Wybierz **pozycję Zapisz** u góry bloku **Konfiguracja.**</span><span class="sxs-lookup"><span data-stu-id="71854-309">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="71854-310">Usługa Azure App Service automatycznie uruchamia ponownie aplikację po dodaniu, zmianie lub usunięciu ustawienia aplikacji (zmiennej środowiskowej) w witrynie Azure portal.</span><span class="sxs-lookup"><span data-stu-id="71854-310">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="71854-311">Windows</span><span class="sxs-lookup"><span data-stu-id="71854-311">Windows</span></span>

<span data-ttu-id="71854-312">Aby ustawić `ASPNETCORE_ENVIRONMENT` dla bieżącej sesji, gdy aplikacja jest uruchomiona przy użyciu [dotnet run](/dotnet/core/tools/dotnet-run), używane są następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="71854-312">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="71854-313">**Wiersz polecenia**</span><span class="sxs-lookup"><span data-stu-id="71854-313">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="71854-314">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="71854-314">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="71854-315">Te polecenia są obowiązywać tylko dla bieżącego okna.</span><span class="sxs-lookup"><span data-stu-id="71854-315">These commands only take effect for the current window.</span></span> <span data-ttu-id="71854-316">Gdy okno jest zamknięte, `ASPNETCORE_ENVIRONMENT` ustawienie zostanie przywrócone do domyślnego ustawienia lub wartości maszyny.</span><span class="sxs-lookup"><span data-stu-id="71854-316">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="71854-317">Aby ustawić wartość globalnie w systemie Windows, użyj jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="71854-317">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="71854-318">Otwórz zaawansowane **System** > **ustawienia systemowe** systemu Panelu `ASPNETCORE_ENVIRONMENT` **sterowania** > i dodaj lub edytuj wartość:</span><span class="sxs-lookup"><span data-stu-id="71854-318">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Zaawansowane właściwości systemu](environments/_static/systemsetting_environment.png)

  ![Zmienna środowiska podstawowego ASPNET](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="71854-321">Otwórz wiersz polecenia administracyjnego `setx` i użyj polecenia lub otwórz administracyjny wiersz polecenia programu PowerShell i użyj: `[Environment]::SetEnvironmentVariable`</span><span class="sxs-lookup"><span data-stu-id="71854-321">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="71854-322">**Wiersz polecenia**</span><span class="sxs-lookup"><span data-stu-id="71854-322">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="71854-323">Przełącznik `/M` wskazuje, aby ustawić zmienną środowiskową na poziomie systemu.</span><span class="sxs-lookup"><span data-stu-id="71854-323">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="71854-324">Jeśli `/M` przełącznik nie jest używany, zmienna środowiskowa jest ustawiona dla konta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="71854-324">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="71854-325">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="71854-325">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="71854-326">Wartość `Machine` opcji wskazuje, aby ustawić zmienną środowiskową na poziomie systemu.</span><span class="sxs-lookup"><span data-stu-id="71854-326">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="71854-327">Jeśli wartość opcji zostanie `User`zmieniona na , zmienna środowiskowa jest ustawiana dla konta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="71854-327">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="71854-328">Gdy `ASPNETCORE_ENVIRONMENT` zmienna środowiskowa jest ustawiona `dotnet run` globalnie, staje się skuteczna w każdym oknie polecenia otwartym po ustawieniu wartości.</span><span class="sxs-lookup"><span data-stu-id="71854-328">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="71854-329">**Web.config**</span><span class="sxs-lookup"><span data-stu-id="71854-329">**web.config**</span></span>

<span data-ttu-id="71854-330">Aby ustawić `ASPNETCORE_ENVIRONMENT` zmienną środowiskową za pomocą *pliku web.config,* zobacz sekcję *Ustawianie zmiennych środowiskowych* w pliku <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="71854-330">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="71854-331">**Plik projektu lub profil publikowania**</span><span class="sxs-lookup"><span data-stu-id="71854-331">**Project file or publish profile**</span></span>

<span data-ttu-id="71854-332">**W przypadku wdrożeń usług Windows IIS:** Dołącz `<EnvironmentName>` właściwość do [profilu publikowania (pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) lub pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="71854-332">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="71854-333">Takie podejście ustawia środowisko w *web.config* po opublikowaniu projektu:</span><span class="sxs-lookup"><span data-stu-id="71854-333">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="71854-334">**Pula aplikacji dla usług IIS**</span><span class="sxs-lookup"><span data-stu-id="71854-334">**Per IIS Application Pool**</span></span>

<span data-ttu-id="71854-335">Aby ustawić `ASPNETCORE_ENVIRONMENT` zmienną środowiskową dla aplikacji działającej w izolowanej puli aplikacji (obsługiwanej w serwisach IIS 10.0 lub nowszych), [ &lt;&gt; ](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) zobacz *sekcję polecenia AppCmd.exe* w temacie Środowisko zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="71854-335">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="71854-336">Gdy `ASPNETCORE_ENVIRONMENT` zmienna środowiskowa jest ustawiona dla puli aplikacji, jej wartość zastępuje ustawienie na poziomie systemu.</span><span class="sxs-lookup"><span data-stu-id="71854-336">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="71854-337">Podczas hostowania aplikacji w użyczanych `ASPNETCORE_ENVIRONMENT` usług IIS i dodawania lub zmieniania zmiennej środowiskowej użyj jednego z następujących podejść, aby nowa wartość była pobierana przez aplikacje:</span><span class="sxs-lookup"><span data-stu-id="71854-337">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="71854-338">Wykonaj, `net stop was /y` `net start w3svc` po którym następuje wiersz polecenia.</span><span class="sxs-lookup"><span data-stu-id="71854-338">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="71854-339">Uruchom ponownie serwer.</span><span class="sxs-lookup"><span data-stu-id="71854-339">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="71854-340">macOS</span><span class="sxs-lookup"><span data-stu-id="71854-340">macOS</span></span>

<span data-ttu-id="71854-341">Ustawienie bieżącego środowiska dla systemu macOS można wykonać w wierszu podczas uruchamiania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="71854-341">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="71854-342">Alternatywnie, ustaw środowisko `export` przed uruchomieniem aplikacji:</span><span class="sxs-lookup"><span data-stu-id="71854-342">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="71854-343">Zmienne środowiskowe na poziomie komputera są ustawiane w pliku *bashrc* lub *.bash_profile.*</span><span class="sxs-lookup"><span data-stu-id="71854-343">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="71854-344">Edytuj plik za pomocą dowolnego edytora tekstu.</span><span class="sxs-lookup"><span data-stu-id="71854-344">Edit the file using any text editor.</span></span> <span data-ttu-id="71854-345">Dodaj następującą instrukcję:</span><span class="sxs-lookup"><span data-stu-id="71854-345">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="71854-346">Linux</span><span class="sxs-lookup"><span data-stu-id="71854-346">Linux</span></span>

<span data-ttu-id="71854-347">W przypadku dystrybucji systemu `export` Linux użyj polecenia w wierszu polecenia dla ustawień zmiennych opartych na sesji i *bash_profile* pliku dla ustawień środowiska na poziomie komputera.</span><span class="sxs-lookup"><span data-stu-id="71854-347">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="71854-348">Ustawianie środowiska w kodzie</span><span class="sxs-lookup"><span data-stu-id="71854-348">Set the environment in code</span></span>

<span data-ttu-id="71854-349">Zadzwoń <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> podczas budowania hosta.</span><span class="sxs-lookup"><span data-stu-id="71854-349">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="71854-350">Zobacz: <xref:fundamentals/host/web-host#environment>.</span><span class="sxs-lookup"><span data-stu-id="71854-350">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="71854-351">Konfiguracja według środowiska</span><span class="sxs-lookup"><span data-stu-id="71854-351">Configuration by environment</span></span>

<span data-ttu-id="71854-352">Aby załadować konfigurację według środowiska, zalecamy:</span><span class="sxs-lookup"><span data-stu-id="71854-352">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="71854-353">*appsettings* plików (*appsettings.{ środowiska}.json*).</span><span class="sxs-lookup"><span data-stu-id="71854-353">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="71854-354">Zobacz: <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="71854-354">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="71854-355">Zmienne środowiskowe (ustawione w każdym systemie, w którym aplikacja jest hostowana).</span><span class="sxs-lookup"><span data-stu-id="71854-355">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="71854-356">Zobacz <xref:fundamentals/host/web-host#environment> <xref:security/app-secrets#environment-variables>i .</span><span class="sxs-lookup"><span data-stu-id="71854-356">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="71854-357">Secret Manager (tylko w środowisku programistycznym).</span><span class="sxs-lookup"><span data-stu-id="71854-357">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="71854-358">Zobacz: <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="71854-358">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="71854-359">Klasa i metody uruchamiania oparte na środowisku</span><span class="sxs-lookup"><span data-stu-id="71854-359">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="71854-360">Wstrzyknąć IHostingEnvironment do Startup.Configure</span><span class="sxs-lookup"><span data-stu-id="71854-360">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="71854-361">Wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> do `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="71854-361">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="71854-362">Takie podejście jest przydatne, gdy `Startup.Configure` aplikacja wymaga tylko konfigurowania tylko dla kilku środowisk z minimalnymi różnicami kodu na środowisko.</span><span class="sxs-lookup"><span data-stu-id="71854-362">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

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

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="71854-363">Wstrzyknąć IHostingEnvironment do klasy Uruchamiani</span><span class="sxs-lookup"><span data-stu-id="71854-363">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="71854-364">Wstrzyknąć <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> do `Startup` konstruktora i przypisać `Startup` usługę do pola do użytku w całej klasie.</span><span class="sxs-lookup"><span data-stu-id="71854-364">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="71854-365">Takie podejście jest przydatne, gdy aplikacja wymaga skonfigurowania uruchamiania tylko dla kilku środowisk z minimalnymi różnicami kodu na środowisko.</span><span class="sxs-lookup"><span data-stu-id="71854-365">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="71854-366">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="71854-366">In the following example:</span></span>

* <span data-ttu-id="71854-367">Środowisko odbywa się `_env` w terenie.</span><span class="sxs-lookup"><span data-stu-id="71854-367">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="71854-368">`_env`jest używany `ConfigureServices` `Configure` w i zastosować konfigurację uruchamiania na podstawie środowiska aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71854-368">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

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

### <a name="startup-class-conventions"></a><span data-ttu-id="71854-369">Konwencje klasy uruchamiania</span><span class="sxs-lookup"><span data-stu-id="71854-369">Startup class conventions</span></span>

<span data-ttu-id="71854-370">Po uruchomieniu aplikacji ASP.NET Core, [startup klasy](xref:fundamentals/startup) bootstraps aplikacji.</span><span class="sxs-lookup"><span data-stu-id="71854-370">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="71854-371">Aplikacja może definiować oddzielne `Startup` klasy dla `StartupDevelopment`różnych środowisk (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="71854-371">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="71854-372">Odpowiednia `Startup` klasa jest wybierana w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="71854-372">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="71854-373">Klasa, której sufiks nazwy pasuje do bieżącego środowiska, jest traktowana priorytetowo.</span><span class="sxs-lookup"><span data-stu-id="71854-373">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="71854-374">Jeśli pasująca `Startup{EnvironmentName}` klasa nie zostanie `Startup` znaleziona, klasa jest używana.</span><span class="sxs-lookup"><span data-stu-id="71854-374">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="71854-375">Takie podejście jest przydatne, gdy aplikacja wymaga skonfigurowania uruchamiania dla kilku środowisk z wieloma różnicami kodu na środowisko.</span><span class="sxs-lookup"><span data-stu-id="71854-375">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="71854-376">Aby zaimplementować klasy oparte na `Startup` środowisku, należy utworzyć `Startup{EnvironmentName}` klasę dla każdego używanego środowiska i klasę rezerwową: `Startup`</span><span class="sxs-lookup"><span data-stu-id="71854-376">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

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

<span data-ttu-id="71854-377">Użyj [przeciążenia UseStartup(IWebHostBuilder, String),](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) który akceptuje nazwę zestawu:</span><span class="sxs-lookup"><span data-stu-id="71854-377">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

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

### <a name="startup-method-conventions"></a><span data-ttu-id="71854-378">Konwencje metody uruchamiania</span><span class="sxs-lookup"><span data-stu-id="71854-378">Startup method conventions</span></span>

<span data-ttu-id="71854-379">[Konfigurowanie](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) i [konfigurowanie Usługi](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) obsługują wersje formularza `Configure<EnvironmentName>` `Configure<EnvironmentName>Services`specyficzne dla środowiska i .</span><span class="sxs-lookup"><span data-stu-id="71854-379">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="71854-380">Takie podejście jest przydatne, gdy aplikacja wymaga skonfigurowania uruchamiania dla kilku środowisk z wieloma różnicami kodu na środowisko.</span><span class="sxs-lookup"><span data-stu-id="71854-380">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="71854-381">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="71854-381">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
