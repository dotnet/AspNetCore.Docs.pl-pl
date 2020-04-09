---
title: Obsługa usług IIS w czasie opracowywania w programie Visual Studio dla platformy ASP.NET Core
author: rick-anderson
description: Odnajdowanie obsługi debugowania aplikacji ASP.NET Core podczas pracy z usługami IIS w systemie Windows Server.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: f87a1d8cf41248f14932908c0633f98a7198853f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664046"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="8c896-103">Obsługa usług IIS w czasie opracowywania w programie Visual Studio dla platformy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8c896-103">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="8c896-104">Przez [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="8c896-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8c896-105">W tym artykule opisano obsługę [programu Visual Studio](https://visualstudio.microsoft.com) do debugowania aplikacji ASP.NET Core uruchomionych z usługami IIS w systemie Windows Server.</span><span class="sxs-lookup"><span data-stu-id="8c896-105">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="8c896-106">W tym temacie ominia się włączanie tego scenariusza i konfigurowanie projektu.</span><span class="sxs-lookup"><span data-stu-id="8c896-106">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8c896-107">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="8c896-107">Prerequisites</span></span>

* [<span data-ttu-id="8c896-108">Program Visual Studio dla systemu Windows</span><span class="sxs-lookup"><span data-stu-id="8c896-108">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="8c896-109">**obciążenie ASP.NET i tworzeniem stron internetowych**</span><span class="sxs-lookup"><span data-stu-id="8c896-109">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="8c896-110">**Wieloplatformowe obciążenie programistyczne .NET Core**</span><span class="sxs-lookup"><span data-stu-id="8c896-110">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="8c896-111">Certyfikat zabezpieczeń X.509 (dla obsługi protokołu HTTPS)</span><span class="sxs-lookup"><span data-stu-id="8c896-111">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="8c896-112">Włączanie iis</span><span class="sxs-lookup"><span data-stu-id="8c896-112">Enable IIS</span></span>

1. <span data-ttu-id="8c896-113">W systemie Windows przejdź do pozycji **Programy i funkcje** > **Panelu** > **sterowania** > **Włączaj lub wyłączaj funkcje systemu Windows** (po lewej stronie ekranu).</span><span class="sxs-lookup"><span data-stu-id="8c896-113">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="8c896-114">Zaznacz pole wyboru **Internetowe Usługi informacyjne.**</span><span class="sxs-lookup"><span data-stu-id="8c896-114">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="8c896-115">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="8c896-115">Select **OK**.</span></span>

<span data-ttu-id="8c896-116">Instalacja usług IIS może wymagać ponownego uruchomienia systemu.</span><span class="sxs-lookup"><span data-stu-id="8c896-116">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="8c896-117">Konfigurowanie usług IIS</span><span class="sxs-lookup"><span data-stu-id="8c896-117">Configure IIS</span></span>

<span data-ttu-id="8c896-118">Serwis IIS musi mieć skonfigurowaną witrynę sieci Web z następującymi potrzebami:</span><span class="sxs-lookup"><span data-stu-id="8c896-118">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="8c896-119">**Nazwa** &ndash; hosta Zazwyczaj **domyślna witryna sieci Web** jest używana z nazwą **hosta** `localhost`.</span><span class="sxs-lookup"><span data-stu-id="8c896-119">**Host name** &ndash; Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="8c896-120">Jednak każda prawidłowa witryna sieci Web iis z unikatową nazwą hosta działa.</span><span class="sxs-lookup"><span data-stu-id="8c896-120">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="8c896-121">**Powiązanie witryny**</span><span class="sxs-lookup"><span data-stu-id="8c896-121">**Site Binding**</span></span>
  * <span data-ttu-id="8c896-122">W przypadku aplikacji, które wymagają protokołu HTTPS, utwórz powiązanie z portem 443 z certyfikatem.</span><span class="sxs-lookup"><span data-stu-id="8c896-122">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="8c896-123">Zazwyczaj używany jest **certyfikat dewelopera IIS Express,** ale każdy prawidłowy certyfikat działa.</span><span class="sxs-lookup"><span data-stu-id="8c896-123">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="8c896-124">W przypadku aplikacji korzystających z protokołu HTTP upewnij się, że istnieje powiązanie, aby opublikować 80 lub utworzyć powiązanie z portem 80 dla nowej witryny.</span><span class="sxs-lookup"><span data-stu-id="8c896-124">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="8c896-125">Użyj jednego powiązania dla http lub https.</span><span class="sxs-lookup"><span data-stu-id="8c896-125">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="8c896-126">**Powiązanie z portami HTTP i HTTPS jednocześnie nie jest obsługiwane.**</span><span class="sxs-lookup"><span data-stu-id="8c896-126">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="8c896-127">Włączanie obsługi iis w czasie programowania w programie Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c896-127">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="8c896-128">Uruchom instalator programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8c896-128">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="8c896-129">Wybierz **pozycję Modyfikuj** dla instalacji programu Visual Studio, która ma być używana dla obsługi czasu programowania usług IIS.</span><span class="sxs-lookup"><span data-stu-id="8c896-129">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="8c896-130">W przypadku obciążenia **ASP.NET i tworzenia sieci Web** zlokalizuj i zainstaluj składnik obsługi usług **IIS czasu programowania.**</span><span class="sxs-lookup"><span data-stu-id="8c896-130">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="8c896-131">Składnik jest wymieniony w sekcji **Opcjonalne** w obszarze **Obsługa usług IIS czasu osiągnięć** w panelu **Szczegóły instalacji** po prawej stronie obciążeń.</span><span class="sxs-lookup"><span data-stu-id="8c896-131">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="8c896-132">Składnik instaluje [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), który jest natywnym modułem usług IIS wymaganym do uruchamiania aplikacji ASP.NET Core z usługami IIS.</span><span class="sxs-lookup"><span data-stu-id="8c896-132">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="8c896-133">Konfigurowanie projektu</span><span class="sxs-lookup"><span data-stu-id="8c896-133">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="8c896-134">Przekierowanie HTTPS</span><span class="sxs-lookup"><span data-stu-id="8c896-134">HTTPS redirection</span></span>

<span data-ttu-id="8c896-135">W przypadku nowego projektu wymagającego protokołu HTTPS zaznacz pole wyboru **Konfigurowanie protokołu HTTPS** w oknie **Utwórz nową ASP.NET podstawową aplikację sieci Web.**</span><span class="sxs-lookup"><span data-stu-id="8c896-135">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="8c896-136">Zaznaczenie tego pola wyboru powoduje dodanie [przekierowania HTTPS i oprogramowania pośredniczącego HSTS](xref:security/enforcing-ssl) do aplikacji podczas jej tworzenia.</span><span class="sxs-lookup"><span data-stu-id="8c896-136">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="8c896-137">W przypadku istniejącego projektu wymagającego protokołu HTTPS należy użyć `Startup.Configure`programu HTTPS Redirection i oprogramowania pośredniczącego HSTS w programie .</span><span class="sxs-lookup"><span data-stu-id="8c896-137">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="8c896-138">Aby uzyskać więcej informacji, zobacz <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="8c896-138">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="8c896-139">W przypadku projektu korzystającego z protokołu HTTP [przekierowanie HTTPS i oprogramowanie pośredniczące HSTS](xref:security/enforcing-ssl) nie są dodawane do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c896-139">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="8c896-140">Nie jest wymagana żadna konfiguracja aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c896-140">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="8c896-141">Profil uruchamiania usługi IIS</span><span class="sxs-lookup"><span data-stu-id="8c896-141">IIS launch profile</span></span>

<span data-ttu-id="8c896-142">Utwórz nowy profil uruchamiania, aby dodać obsługę usługi IIS w czasie rozwoju:</span><span class="sxs-lookup"><span data-stu-id="8c896-142">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="8c896-143">Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań**.</span><span class="sxs-lookup"><span data-stu-id="8c896-143">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="8c896-144">Wybierz **pozycję Właściwości**.</span><span class="sxs-lookup"><span data-stu-id="8c896-144">Select **Properties**.</span></span> <span data-ttu-id="8c896-145">Otwórz kartę **Debugowanie.**</span><span class="sxs-lookup"><span data-stu-id="8c896-145">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="8c896-146">W obszarze **Profil**wybierz przycisk **Nowy.**</span><span class="sxs-lookup"><span data-stu-id="8c896-146">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="8c896-147">Nazwij profil "IIS" w oknie podręcznym.</span><span class="sxs-lookup"><span data-stu-id="8c896-147">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="8c896-148">Wybierz **przycisk OK,** aby utworzyć profil.</span><span class="sxs-lookup"><span data-stu-id="8c896-148">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="8c896-149">W przypadku ustawienia **Uruchom** wybierz pozycję **IIS** z listy.</span><span class="sxs-lookup"><span data-stu-id="8c896-149">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="8c896-150">Zaznacz pole wyboru **Uruchom przeglądarkę** i podaj adres URL punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="8c896-150">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="8c896-151">Gdy aplikacja wymaga protokołu HTTPS, użyj`https://`punktu końcowego HTTPS ( ).</span><span class="sxs-lookup"><span data-stu-id="8c896-151">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="8c896-152">W przypadku protokołu HTTP`http://`należy użyć punktu końcowego HTTP ( ).</span><span class="sxs-lookup"><span data-stu-id="8c896-152">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="8c896-153">Podaj tę samą nazwę hosta i port, co `localhost` [konfiguracja usług IIS określona wcześniej używa](#configure-iis), zazwyczaj .</span><span class="sxs-lookup"><span data-stu-id="8c896-153">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="8c896-154">Podaj nazwę aplikacji na końcu adresu URL.</span><span class="sxs-lookup"><span data-stu-id="8c896-154">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="8c896-155">Na przykład `https://localhost/WebApplication1` (HTTPS) `http://localhost/WebApplication1` lub (HTTP) są prawidłowymi adresami URL punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="8c896-155">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="8c896-156">W sekcji **Zmienne środowiska** wybierz przycisk **Dodaj.**</span><span class="sxs-lookup"><span data-stu-id="8c896-156">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="8c896-157">Podaj zmienną środowiskową **Value** o `Development` **nazwie** `ASPNETCORE_ENVIRONMENT` i wartości .</span><span class="sxs-lookup"><span data-stu-id="8c896-157">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="8c896-158">W obszarze **Ustawienia serwera sieci Web** ustaw adres URL **aplikacji** na tę samą wartość, która jest używana dla adresu URL punktu końcowego **przeglądarki Uruchamianie.**</span><span class="sxs-lookup"><span data-stu-id="8c896-158">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="8c896-159">Dla ustawienia **modelu hostingu** w programie Visual Studio 2019 lub nowszym wybierz pozycję **Domyślny,** aby użyć modelu hostingu używanego przez projekt.</span><span class="sxs-lookup"><span data-stu-id="8c896-159">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="8c896-160">Jeśli projekt ustawia `<AspNetCoreHostingModel>` właściwość w pliku projektu, używana`InProcess` `OutOfProcess`jest wartość właściwości ( lub ).</span><span class="sxs-lookup"><span data-stu-id="8c896-160">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="8c896-161">Jeśli właściwość nie jest obecny, domyślny model hostingu aplikacji jest używany, który jest w toku.</span><span class="sxs-lookup"><span data-stu-id="8c896-161">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="8c896-162">Jeśli aplikacja wymaga jawnego ustawienia modelu hostingu innego niż normalny model hostingu `In Process` `Out Of Process` aplikacji, ustaw **model hostingu** na jeden lub w razie potrzeby.</span><span class="sxs-lookup"><span data-stu-id="8c896-162">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="8c896-163">Zapisz profil.</span><span class="sxs-lookup"><span data-stu-id="8c896-163">Save the profile.</span></span>

<span data-ttu-id="8c896-164">Jeśli nie jest używany program Visual Studio, ręcznie dodać profil uruchamiania do pliku [launchSettings.json](https://json.schemastore.org/launchsettings) w folderze *Właściwości.*</span><span class="sxs-lookup"><span data-stu-id="8c896-164">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="8c896-165">Poniższy przykład konfiguruje profil do używania protokołu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="8c896-165">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="8c896-166">Upewnij się, że `applicationUrl` i `launchUrl` punktów końcowych zgodne i używać tego samego protokołu jako konfiguracji powiązania usług IIS, HTTP lub HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8c896-166">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="8c896-167">Uruchamianie projektu</span><span class="sxs-lookup"><span data-stu-id="8c896-167">Run the project</span></span>

<span data-ttu-id="8c896-168">Uruchom program Visual Studio jako administrator:</span><span class="sxs-lookup"><span data-stu-id="8c896-168">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="8c896-169">Upewnij się, że lista rozwijana konfiguracji kompilacji jest ustawiona na **Debugowanie**.</span><span class="sxs-lookup"><span data-stu-id="8c896-169">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="8c896-170">Ustaw [przycisk Rozpocznij debugowanie](/visualstudio/debugger/debugger-feature-tour) na profilu **IIS** i wybierz przycisk, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="8c896-170">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="8c896-171">Visual Studio może monitować o ponowne uruchomienie, jeśli nie jest uruchomiony jako administrator.</span><span class="sxs-lookup"><span data-stu-id="8c896-171">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="8c896-172">Jeśli zostanie wyświetlony monit, uruchom ponownie program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8c896-172">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="8c896-173">Jeśli używany jest niezaufany certyfikat dewelopera, przeglądarka może wymagać utworzenia wyjątku dla niezaufanego certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="8c896-173">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="8c896-174">Debugowanie konfiguracji kompilacji wydania za pomocą [tylko mój kod](/visualstudio/debugger/just-my-code) i optymalizacje kompilatora powoduje pogorszenie środowiska.</span><span class="sxs-lookup"><span data-stu-id="8c896-174">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="8c896-175">Na przykład break pointy nie są trafione.</span><span class="sxs-lookup"><span data-stu-id="8c896-175">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8c896-176">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="8c896-176">Additional resources</span></span>

* [<span data-ttu-id="8c896-177">Wprowadzenie do menedżera usług IIS w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="8c896-177">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8c896-178">W tym artykule opisano obsługę [programu Visual Studio](https://visualstudio.microsoft.com) do debugowania aplikacji ASP.NET Core uruchomionych z usługami IIS w systemie Windows Server.</span><span class="sxs-lookup"><span data-stu-id="8c896-178">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="8c896-179">W tym temacie ominia się włączanie tego scenariusza i konfigurowanie projektu.</span><span class="sxs-lookup"><span data-stu-id="8c896-179">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8c896-180">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="8c896-180">Prerequisites</span></span>

* [<span data-ttu-id="8c896-181">Program Visual Studio dla systemu Windows</span><span class="sxs-lookup"><span data-stu-id="8c896-181">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="8c896-182">**obciążenie ASP.NET i tworzeniem stron internetowych**</span><span class="sxs-lookup"><span data-stu-id="8c896-182">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="8c896-183">**Wieloplatformowe obciążenie programistyczne .NET Core**</span><span class="sxs-lookup"><span data-stu-id="8c896-183">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="8c896-184">Certyfikat zabezpieczeń X.509 (dla obsługi protokołu HTTPS)</span><span class="sxs-lookup"><span data-stu-id="8c896-184">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="8c896-185">Włączanie iis</span><span class="sxs-lookup"><span data-stu-id="8c896-185">Enable IIS</span></span>

1. <span data-ttu-id="8c896-186">W systemie Windows przejdź do pozycji **Programy i funkcje** > **Panelu** > **sterowania** > **Włączaj lub wyłączaj funkcje systemu Windows** (po lewej stronie ekranu).</span><span class="sxs-lookup"><span data-stu-id="8c896-186">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="8c896-187">Zaznacz pole wyboru **Internetowe Usługi informacyjne.**</span><span class="sxs-lookup"><span data-stu-id="8c896-187">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="8c896-188">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="8c896-188">Select **OK**.</span></span>

<span data-ttu-id="8c896-189">Instalacja usług IIS może wymagać ponownego uruchomienia systemu.</span><span class="sxs-lookup"><span data-stu-id="8c896-189">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="8c896-190">Konfigurowanie usług IIS</span><span class="sxs-lookup"><span data-stu-id="8c896-190">Configure IIS</span></span>

<span data-ttu-id="8c896-191">Serwis IIS musi mieć skonfigurowaną witrynę sieci Web z następującymi potrzebami:</span><span class="sxs-lookup"><span data-stu-id="8c896-191">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="8c896-192">**Nazwa** &ndash; hosta Zazwyczaj **domyślna witryna sieci Web** jest używana z nazwą **hosta** `localhost`.</span><span class="sxs-lookup"><span data-stu-id="8c896-192">**Host name** &ndash; Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="8c896-193">Jednak każda prawidłowa witryna sieci Web iis z unikatową nazwą hosta działa.</span><span class="sxs-lookup"><span data-stu-id="8c896-193">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="8c896-194">**Powiązanie witryny**</span><span class="sxs-lookup"><span data-stu-id="8c896-194">**Site Binding**</span></span>
  * <span data-ttu-id="8c896-195">W przypadku aplikacji, które wymagają protokołu HTTPS, utwórz powiązanie z portem 443 z certyfikatem.</span><span class="sxs-lookup"><span data-stu-id="8c896-195">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="8c896-196">Zazwyczaj używany jest **certyfikat dewelopera IIS Express,** ale każdy prawidłowy certyfikat działa.</span><span class="sxs-lookup"><span data-stu-id="8c896-196">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="8c896-197">W przypadku aplikacji korzystających z protokołu HTTP upewnij się, że istnieje powiązanie, aby opublikować 80 lub utworzyć powiązanie z portem 80 dla nowej witryny.</span><span class="sxs-lookup"><span data-stu-id="8c896-197">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="8c896-198">Użyj jednego powiązania dla http lub https.</span><span class="sxs-lookup"><span data-stu-id="8c896-198">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="8c896-199">**Powiązanie z portami HTTP i HTTPS jednocześnie nie jest obsługiwane.**</span><span class="sxs-lookup"><span data-stu-id="8c896-199">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="8c896-200">Włączanie obsługi iis w czasie programowania w programie Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c896-200">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="8c896-201">Uruchom instalator programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8c896-201">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="8c896-202">Wybierz **pozycję Modyfikuj** dla instalacji programu Visual Studio, która ma być używana dla obsługi czasu programowania usług IIS.</span><span class="sxs-lookup"><span data-stu-id="8c896-202">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="8c896-203">W przypadku obciążenia **ASP.NET i tworzenia sieci Web** zlokalizuj i zainstaluj składnik obsługi usług **IIS czasu programowania.**</span><span class="sxs-lookup"><span data-stu-id="8c896-203">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="8c896-204">Składnik jest wymieniony w sekcji **Opcjonalne** w obszarze **Obsługa usług IIS czasu osiągnięć** w panelu **Szczegóły instalacji** po prawej stronie obciążeń.</span><span class="sxs-lookup"><span data-stu-id="8c896-204">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="8c896-205">Składnik instaluje [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), który jest natywnym modułem usług IIS wymaganym do uruchamiania aplikacji ASP.NET Core z usługami IIS.</span><span class="sxs-lookup"><span data-stu-id="8c896-205">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="8c896-206">Konfigurowanie projektu</span><span class="sxs-lookup"><span data-stu-id="8c896-206">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="8c896-207">Przekierowanie HTTPS</span><span class="sxs-lookup"><span data-stu-id="8c896-207">HTTPS redirection</span></span>

<span data-ttu-id="8c896-208">W przypadku nowego projektu wymagającego protokołu HTTPS zaznacz pole wyboru **Konfigurowanie protokołu HTTPS** w oknie **Utwórz nową ASP.NET podstawową aplikację sieci Web.**</span><span class="sxs-lookup"><span data-stu-id="8c896-208">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="8c896-209">Zaznaczenie tego pola wyboru powoduje dodanie [przekierowania HTTPS i oprogramowania pośredniczącego HSTS](xref:security/enforcing-ssl) do aplikacji podczas jej tworzenia.</span><span class="sxs-lookup"><span data-stu-id="8c896-209">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="8c896-210">W przypadku istniejącego projektu wymagającego protokołu HTTPS należy użyć `Startup.Configure`programu HTTPS Redirection i oprogramowania pośredniczącego HSTS w programie .</span><span class="sxs-lookup"><span data-stu-id="8c896-210">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="8c896-211">Aby uzyskać więcej informacji, zobacz <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="8c896-211">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="8c896-212">W przypadku projektu korzystającego z protokołu HTTP [przekierowanie HTTPS i oprogramowanie pośredniczące HSTS](xref:security/enforcing-ssl) nie są dodawane do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c896-212">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="8c896-213">Nie jest wymagana żadna konfiguracja aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c896-213">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="8c896-214">Profil uruchamiania usługi IIS</span><span class="sxs-lookup"><span data-stu-id="8c896-214">IIS launch profile</span></span>

<span data-ttu-id="8c896-215">Utwórz nowy profil uruchamiania, aby dodać obsługę usługi IIS w czasie rozwoju:</span><span class="sxs-lookup"><span data-stu-id="8c896-215">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="8c896-216">Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań**.</span><span class="sxs-lookup"><span data-stu-id="8c896-216">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="8c896-217">Wybierz **pozycję Właściwości**.</span><span class="sxs-lookup"><span data-stu-id="8c896-217">Select **Properties**.</span></span> <span data-ttu-id="8c896-218">Otwórz kartę **Debugowanie.**</span><span class="sxs-lookup"><span data-stu-id="8c896-218">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="8c896-219">W obszarze **Profil**wybierz przycisk **Nowy.**</span><span class="sxs-lookup"><span data-stu-id="8c896-219">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="8c896-220">Nazwij profil "IIS" w oknie podręcznym.</span><span class="sxs-lookup"><span data-stu-id="8c896-220">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="8c896-221">Wybierz **przycisk OK,** aby utworzyć profil.</span><span class="sxs-lookup"><span data-stu-id="8c896-221">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="8c896-222">W przypadku ustawienia **Uruchom** wybierz pozycję **IIS** z listy.</span><span class="sxs-lookup"><span data-stu-id="8c896-222">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="8c896-223">Zaznacz pole wyboru **Uruchom przeglądarkę** i podaj adres URL punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="8c896-223">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="8c896-224">Gdy aplikacja wymaga protokołu HTTPS, użyj`https://`punktu końcowego HTTPS ( ).</span><span class="sxs-lookup"><span data-stu-id="8c896-224">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="8c896-225">W przypadku protokołu HTTP`http://`należy użyć punktu końcowego HTTP ( ).</span><span class="sxs-lookup"><span data-stu-id="8c896-225">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="8c896-226">Podaj tę samą nazwę hosta i port, co `localhost` [konfiguracja usług IIS określona wcześniej używa](#configure-iis), zazwyczaj .</span><span class="sxs-lookup"><span data-stu-id="8c896-226">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="8c896-227">Podaj nazwę aplikacji na końcu adresu URL.</span><span class="sxs-lookup"><span data-stu-id="8c896-227">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="8c896-228">Na przykład `https://localhost/WebApplication1` (HTTPS) `http://localhost/WebApplication1` lub (HTTP) są prawidłowymi adresami URL punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="8c896-228">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="8c896-229">W sekcji **Zmienne środowiska** wybierz przycisk **Dodaj.**</span><span class="sxs-lookup"><span data-stu-id="8c896-229">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="8c896-230">Podaj zmienną środowiskową **Value** o `Development` **nazwie** `ASPNETCORE_ENVIRONMENT` i wartości .</span><span class="sxs-lookup"><span data-stu-id="8c896-230">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="8c896-231">W obszarze **Ustawienia serwera sieci Web** ustaw adres URL **aplikacji** na tę samą wartość, która jest używana dla adresu URL punktu końcowego **przeglądarki Uruchamianie.**</span><span class="sxs-lookup"><span data-stu-id="8c896-231">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="8c896-232">Dla ustawienia **modelu hostingu** w programie Visual Studio 2019 lub nowszym wybierz pozycję **Domyślny,** aby użyć modelu hostingu używanego przez projekt.</span><span class="sxs-lookup"><span data-stu-id="8c896-232">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="8c896-233">Jeśli projekt ustawia `<AspNetCoreHostingModel>` właściwość w pliku projektu, używana`InProcess` `OutOfProcess`jest wartość właściwości ( lub ).</span><span class="sxs-lookup"><span data-stu-id="8c896-233">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="8c896-234">Jeśli właściwość nie jest obecny, domyślny model hostingu aplikacji jest używany, który jest poza procesem.</span><span class="sxs-lookup"><span data-stu-id="8c896-234">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="8c896-235">Jeśli aplikacja wymaga jawnego ustawienia modelu hostingu innego niż normalny model hostingu `In Process` `Out Of Process` aplikacji, ustaw **model hostingu** na jeden lub w razie potrzeby.</span><span class="sxs-lookup"><span data-stu-id="8c896-235">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="8c896-236">Zapisz profil.</span><span class="sxs-lookup"><span data-stu-id="8c896-236">Save the profile.</span></span>

<span data-ttu-id="8c896-237">Jeśli nie jest używany program Visual Studio, ręcznie dodać profil uruchamiania do pliku [launchSettings.json](https://json.schemastore.org/launchsettings) w folderze *Właściwości.*</span><span class="sxs-lookup"><span data-stu-id="8c896-237">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="8c896-238">Poniższy przykład konfiguruje profil do używania protokołu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="8c896-238">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="8c896-239">Upewnij się, że `applicationUrl` i `launchUrl` punktów końcowych zgodne i używać tego samego protokołu jako konfiguracji powiązania usług IIS, HTTP lub HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8c896-239">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="8c896-240">Uruchamianie projektu</span><span class="sxs-lookup"><span data-stu-id="8c896-240">Run the project</span></span>

<span data-ttu-id="8c896-241">Uruchom program Visual Studio jako administrator:</span><span class="sxs-lookup"><span data-stu-id="8c896-241">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="8c896-242">Upewnij się, że lista rozwijana konfiguracji kompilacji jest ustawiona na **Debugowanie**.</span><span class="sxs-lookup"><span data-stu-id="8c896-242">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="8c896-243">Ustaw [przycisk Rozpocznij debugowanie](/visualstudio/debugger/debugger-feature-tour) na profilu **IIS** i wybierz przycisk, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="8c896-243">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="8c896-244">Visual Studio może monitować o ponowne uruchomienie, jeśli nie jest uruchomiony jako administrator.</span><span class="sxs-lookup"><span data-stu-id="8c896-244">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="8c896-245">Jeśli zostanie wyświetlony monit, uruchom ponownie program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8c896-245">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="8c896-246">Jeśli używany jest niezaufany certyfikat dewelopera, przeglądarka może wymagać utworzenia wyjątku dla niezaufanego certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="8c896-246">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="8c896-247">Debugowanie konfiguracji kompilacji wydania za pomocą [tylko mój kod](/visualstudio/debugger/just-my-code) i optymalizacje kompilatora powoduje pogorszenie środowiska.</span><span class="sxs-lookup"><span data-stu-id="8c896-247">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="8c896-248">Na przykład break pointy nie są trafione.</span><span class="sxs-lookup"><span data-stu-id="8c896-248">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8c896-249">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="8c896-249">Additional resources</span></span>

* [<span data-ttu-id="8c896-250">Wprowadzenie do menedżera usług IIS w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="8c896-250">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
