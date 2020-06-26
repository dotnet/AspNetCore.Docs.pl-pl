---
title: Obsługa usług IIS w czasie opracowywania w programie Visual Studio dla platformy ASP.NET Core
author: rick-anderson
description: Odkryj obsługę debugowania aplikacji ASP.NET Core podczas uruchamiania programu z usługami IIS w systemie Windows Server.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: d922b2eb4d00a252b3e7da2f00de8b4571359b20
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406436"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="9ebc6-103">Obsługa usług IIS w czasie opracowywania w programie Visual Studio dla platformy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9ebc6-103">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="9ebc6-104">Autor [sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="9ebc6-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9ebc6-105">W tym artykule opisano obsługę [programu Visual Studio](https://visualstudio.microsoft.com) na potrzeby debugowania ASP.NET Core aplikacje działające z usługami IIS w systemie Windows Server.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-105">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="9ebc6-106">Ten temat zawiera instrukcje dotyczące włączania tego scenariusza i konfigurowania projektu.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-106">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9ebc6-107">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="9ebc6-107">Prerequisites</span></span>

* [<span data-ttu-id="9ebc6-108">Program Visual Studio dla systemu Windows</span><span class="sxs-lookup"><span data-stu-id="9ebc6-108">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="9ebc6-109">**ASP.NET i programowanie aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="9ebc6-109">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="9ebc6-110">**Tworzenie aplikacji dla wielu platform w środowisku .NET Core**</span><span class="sxs-lookup"><span data-stu-id="9ebc6-110">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="9ebc6-111">Certyfikat zabezpieczeń X. 509 (dla obsługi protokołu HTTPS)</span><span class="sxs-lookup"><span data-stu-id="9ebc6-111">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="9ebc6-112">Włącz usługi IIS</span><span class="sxs-lookup"><span data-stu-id="9ebc6-112">Enable IIS</span></span>

1. <span data-ttu-id="9ebc6-113">W systemie Windows przejdź do pozycji **Panel sterowania** > **programy** > **programy i funkcje** > **Włącz lub wyłącz funkcje systemu Windows** (po lewej stronie ekranu).</span><span class="sxs-lookup"><span data-stu-id="9ebc6-113">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="9ebc6-114">Zaznacz pole wyboru **Internet Information Services** .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-114">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="9ebc6-115">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-115">Select **OK**.</span></span>

<span data-ttu-id="9ebc6-116">Instalacja usług IIS może wymagać ponownego uruchomienia systemu.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-116">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="9ebc6-117">Konfigurowanie usług IIS</span><span class="sxs-lookup"><span data-stu-id="9ebc6-117">Configure IIS</span></span>

<span data-ttu-id="9ebc6-118">Usługi IIS muszą mieć skonfigurowaną witrynę sieci Web z następującymi:</span><span class="sxs-lookup"><span data-stu-id="9ebc6-118">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="9ebc6-119">**Nazwa hosta**: zazwyczaj **Domyślna witryna sieci Web** jest używana z **nazwą hosta** `localhost` .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-119">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="9ebc6-120">Jednak każda prawidłowa witryna sieci Web usług IIS z unikatową nazwą hosta działa.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-120">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="9ebc6-121">**Powiązanie witryny**</span><span class="sxs-lookup"><span data-stu-id="9ebc6-121">**Site Binding**</span></span>
  * <span data-ttu-id="9ebc6-122">W przypadku aplikacji wymagających protokołu HTTPS Utwórz powiązanie z portem 443 z certyfikatem.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-122">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="9ebc6-123">Zwykle używany jest **certyfikat deweloperski IIS Express** , ale każdy prawidłowy certyfikat działa.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-123">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="9ebc6-124">W przypadku aplikacji korzystających z protokołu HTTP upewnij się, że istnieje powiązanie do opublikowania 80 lub utwórz powiązanie z portem 80 dla nowej lokacji.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-124">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="9ebc6-125">Użyj pojedynczego powiązania dla protokołu HTTP lub HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-125">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="9ebc6-126">**Powiązanie jednocześnie z portami HTTP i HTTPS nie jest obsługiwane.**</span><span class="sxs-lookup"><span data-stu-id="9ebc6-126">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="9ebc6-127">Włączanie obsługi usług IIS w czasie opracowywania w programie Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9ebc6-127">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="9ebc6-128">Uruchom Instalatora programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-128">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="9ebc6-129">Wybierz pozycję **Modyfikuj** dla instalacji programu Visual Studio, która ma być używana na potrzeby obsługi czasu projektowania usług IIS.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-129">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="9ebc6-130">W przypadku obciążeń **ASP.NET i sieci Web** Znajdź i Zainstaluj składnik **Obsługa usług IIS w czasie opracowywania** .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-130">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="9ebc6-131">Składnik jest wymieniony w sekcji **opcjonalne** w obszarze **czas projektowania obsługa usług IIS** w panelu **szczegóły instalacji** po prawej stronie obciążeń.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-131">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="9ebc6-132">Składnik instaluje [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module), który jest natywnym modułem usług IIS wymaganym do uruchamiania aplikacji ASP.NET Core za pomocą usług IIS.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-132">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="9ebc6-133">Konfigurowanie projektu</span><span class="sxs-lookup"><span data-stu-id="9ebc6-133">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="9ebc6-134">Przekierowanie HTTPS</span><span class="sxs-lookup"><span data-stu-id="9ebc6-134">HTTPS redirection</span></span>

<span data-ttu-id="9ebc6-135">Dla nowego projektu, który wymaga protokołu HTTPS, zaznacz pole wyboru w celu **skonfigurowania protokołu HTTPS** w oknie **Tworzenie nowej ASP.NET Core aplikacji sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-135">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="9ebc6-136">Zaznaczenie tego pola wyboru powoduje dodanie [przekierowania https i HSTS oprogramowania](xref:security/enforcing-ssl) do aplikacji podczas jej tworzenia.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-136">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="9ebc6-137">W przypadku istniejącego projektu wymagającego protokołu HTTPS Użyj przekierowania HTTPS i oprogramowania pośredniczącego HSTS w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-137">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="9ebc6-138">Aby uzyskać więcej informacji, zobacz <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-138">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="9ebc6-139">W przypadku projektu korzystającego z protokołu HTTP, [przekierowania https i oprogramowania pośredniczącego HSTS](xref:security/enforcing-ssl) nie są dodawane do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-139">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="9ebc6-140">Nie jest wymagana żadna konfiguracja aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-140">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="9ebc6-141">Profil uruchamiania usług IIS</span><span class="sxs-lookup"><span data-stu-id="9ebc6-141">IIS launch profile</span></span>

<span data-ttu-id="9ebc6-142">Utwórz nowy profil uruchamiania, aby dodać obsługę usług IIS w czasie projektowania:</span><span class="sxs-lookup"><span data-stu-id="9ebc6-142">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="9ebc6-143">Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań**.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-143">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="9ebc6-144">Wybierz pozycję **Właściwości**.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-144">Select **Properties**.</span></span> <span data-ttu-id="9ebc6-145">Otwórz kartę **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-145">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="9ebc6-146">W obszarze **profil**wybierz przycisk **Nowy** .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-146">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="9ebc6-147">Nazwij profil "IIS" w oknie podręcznym.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-147">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="9ebc6-148">Wybierz **przycisk OK** , aby utworzyć profil.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-148">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="9ebc6-149">Dla ustawienia **uruchamiania** wybierz z listy pozycję **IIS** .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-149">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="9ebc6-150">Zaznacz pole wyboru dla opcji **Uruchom przeglądarkę** i podaj adres URL punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-150">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="9ebc6-151">Gdy aplikacja wymaga protokołu HTTPS, użyj punktu końcowego HTTPS ( `https://` ).</span><span class="sxs-lookup"><span data-stu-id="9ebc6-151">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="9ebc6-152">W przypadku protokołu HTTP Użyj `http://` punktu końcowego http ().</span><span class="sxs-lookup"><span data-stu-id="9ebc6-152">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="9ebc6-153">Podaj tę samą nazwę hosta i port, co [Konfiguracja usług IIS określona wcześniej](#configure-iis), zazwyczaj `localhost` .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-153">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="9ebc6-154">Podaj nazwę aplikacji na końcu adresu URL.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-154">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="9ebc6-155">Na przykład `https://localhost/WebApplication1` (https) lub `http://localhost/WebApplication1` (http) są prawidłowymi adresami URL punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-155">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="9ebc6-156">W sekcji **zmienne środowiskowe** wybierz przycisk **Dodaj** .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-156">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="9ebc6-157">Podaj zmienną środowiskową o **nazwie** `ASPNETCORE_ENVIRONMENT` i **wartości** `Development` .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-157">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="9ebc6-158">W obszarze **Ustawienia serwera sieci Web** Ustaw **adres URL aplikacji** na wartość używaną dla adresu URL punktu końcowego **uruchamiania przeglądarki** .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-158">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="9ebc6-159">W przypadku ustawienia **model hostingu** w programie Visual Studio 2019 lub nowszym wybierz pozycję **domyślne** , aby użyć modelu hostingu używanego przez projekt.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-159">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="9ebc6-160">Jeśli projekt ustawia `<AspNetCoreHostingModel>` Właściwość w pliku projektu, używana jest wartość właściwości ( `InProcess` lub `OutOfProcess` ).</span><span class="sxs-lookup"><span data-stu-id="9ebc6-160">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="9ebc6-161">Jeśli właściwość nie jest obecna, używany jest domyślny model hostingu aplikacji, który jest w procesie.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-161">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="9ebc6-162">Jeśli aplikacja wymaga jawnego ustawienia modelu hostingu innego niż normalny model hostingu aplikacji, należy ustawić **model hostingu** na albo `In Process` `Out Of Process` w razie potrzeby.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-162">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="9ebc6-163">Zapisz profil.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-163">Save the profile.</span></span>

<span data-ttu-id="9ebc6-164">Gdy nie korzystasz z programu Visual Studio, ręcznie Dodaj profil uruchamiania do [launchSettings.js](https://json.schemastore.org/launchsettings) w pliku w folderze *Właściwości* .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-164">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="9ebc6-165">Poniższy przykład konfiguruje profil do korzystania z protokołu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="9ebc6-165">The following example configures the profile to use the HTTPS protocol:</span></span>

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

<span data-ttu-id="9ebc6-166">Upewnij się, `applicationUrl` że `launchUrl` punkty końcowe i używają tego samego protokołu, co Konfiguracja powiązania usług IIS, http lub https.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-166">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="9ebc6-167">Uruchamianie projektu</span><span class="sxs-lookup"><span data-stu-id="9ebc6-167">Run the project</span></span>

<span data-ttu-id="9ebc6-168">Uruchom program Visual Studio jako administrator:</span><span class="sxs-lookup"><span data-stu-id="9ebc6-168">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="9ebc6-169">Upewnij się, że na liście rozwijanej konfiguracja kompilacji jest ustawiona wartość **Debuguj**.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-169">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="9ebc6-170">Ustaw [przycisk Rozpocznij debugowanie](/visualstudio/debugger/debugger-feature-tour) na profil **usług IIS** i wybierz przycisk, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-170">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="9ebc6-171">Program Visual Studio może monitować o ponowne uruchomienie, jeśli nie jest uruchomiony jako administrator.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-171">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="9ebc6-172">Jeśli zostanie wyświetlony monit, uruchom ponownie program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-172">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="9ebc6-173">Jeśli jest używany niezaufany certyfikat programistyczny, w przeglądarce może być konieczne utworzenie wyjątku dla certyfikatu niezaufanego.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-173">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="9ebc6-174">Debugowanie konfiguracji kompilacji wydania z optymalizacjami [tylko mój kod](/visualstudio/debugger/just-my-code) i kompilatora skutkuje obniżeniem wydajności.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-174">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="9ebc6-175">Na przykład punkty przerwania nie trafią.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-175">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9ebc6-176">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="9ebc6-176">Additional resources</span></span>

* [<span data-ttu-id="9ebc6-177">Wprowadzenie z menedżerem usług IIS w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="9ebc6-177">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9ebc6-178">W tym artykule opisano obsługę [programu Visual Studio](https://visualstudio.microsoft.com) na potrzeby debugowania ASP.NET Core aplikacje działające z usługami IIS w systemie Windows Server.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-178">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="9ebc6-179">Ten temat zawiera instrukcje dotyczące włączania tego scenariusza i konfigurowania projektu.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-179">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9ebc6-180">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="9ebc6-180">Prerequisites</span></span>

* [<span data-ttu-id="9ebc6-181">Program Visual Studio dla systemu Windows</span><span class="sxs-lookup"><span data-stu-id="9ebc6-181">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="9ebc6-182">**ASP.NET i programowanie aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="9ebc6-182">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="9ebc6-183">**Tworzenie aplikacji dla wielu platform w środowisku .NET Core**</span><span class="sxs-lookup"><span data-stu-id="9ebc6-183">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="9ebc6-184">Certyfikat zabezpieczeń X. 509 (dla obsługi protokołu HTTPS)</span><span class="sxs-lookup"><span data-stu-id="9ebc6-184">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="9ebc6-185">Włącz usługi IIS</span><span class="sxs-lookup"><span data-stu-id="9ebc6-185">Enable IIS</span></span>

1. <span data-ttu-id="9ebc6-186">W systemie Windows przejdź do pozycji **Panel sterowania** > **programy** > **programy i funkcje** > **Włącz lub wyłącz funkcje systemu Windows** (po lewej stronie ekranu).</span><span class="sxs-lookup"><span data-stu-id="9ebc6-186">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="9ebc6-187">Zaznacz pole wyboru **Internet Information Services** .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-187">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="9ebc6-188">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-188">Select **OK**.</span></span>

<span data-ttu-id="9ebc6-189">Instalacja usług IIS może wymagać ponownego uruchomienia systemu.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-189">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="9ebc6-190">Konfigurowanie usług IIS</span><span class="sxs-lookup"><span data-stu-id="9ebc6-190">Configure IIS</span></span>

<span data-ttu-id="9ebc6-191">Usługi IIS muszą mieć skonfigurowaną witrynę sieci Web z następującymi:</span><span class="sxs-lookup"><span data-stu-id="9ebc6-191">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="9ebc6-192">**Nazwa hosta**: zazwyczaj **Domyślna witryna sieci Web** jest używana z **nazwą hosta** `localhost` .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-192">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="9ebc6-193">Jednak każda prawidłowa witryna sieci Web usług IIS z unikatową nazwą hosta działa.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-193">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="9ebc6-194">**Powiązanie witryny**</span><span class="sxs-lookup"><span data-stu-id="9ebc6-194">**Site Binding**</span></span>
  * <span data-ttu-id="9ebc6-195">W przypadku aplikacji wymagających protokołu HTTPS Utwórz powiązanie z portem 443 z certyfikatem.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-195">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="9ebc6-196">Zwykle używany jest **certyfikat deweloperski IIS Express** , ale każdy prawidłowy certyfikat działa.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-196">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="9ebc6-197">W przypadku aplikacji korzystających z protokołu HTTP upewnij się, że istnieje powiązanie do opublikowania 80 lub utwórz powiązanie z portem 80 dla nowej lokacji.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-197">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="9ebc6-198">Użyj pojedynczego powiązania dla protokołu HTTP lub HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-198">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="9ebc6-199">**Powiązanie jednocześnie z portami HTTP i HTTPS nie jest obsługiwane.**</span><span class="sxs-lookup"><span data-stu-id="9ebc6-199">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="9ebc6-200">Włączanie obsługi usług IIS w czasie opracowywania w programie Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9ebc6-200">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="9ebc6-201">Uruchom Instalatora programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-201">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="9ebc6-202">Wybierz pozycję **Modyfikuj** dla instalacji programu Visual Studio, która ma być używana na potrzeby obsługi czasu projektowania usług IIS.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-202">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="9ebc6-203">W przypadku obciążeń **ASP.NET i sieci Web** Znajdź i Zainstaluj składnik **Obsługa usług IIS w czasie opracowywania** .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-203">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="9ebc6-204">Składnik jest wymieniony w sekcji **opcjonalne** w obszarze **czas projektowania obsługa usług IIS** w panelu **szczegóły instalacji** po prawej stronie obciążeń.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-204">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="9ebc6-205">Składnik instaluje [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module), który jest natywnym modułem usług IIS wymaganym do uruchamiania aplikacji ASP.NET Core za pomocą usług IIS.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-205">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="9ebc6-206">Konfigurowanie projektu</span><span class="sxs-lookup"><span data-stu-id="9ebc6-206">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="9ebc6-207">Przekierowanie HTTPS</span><span class="sxs-lookup"><span data-stu-id="9ebc6-207">HTTPS redirection</span></span>

<span data-ttu-id="9ebc6-208">Dla nowego projektu, który wymaga protokołu HTTPS, zaznacz pole wyboru w celu **skonfigurowania protokołu HTTPS** w oknie **Tworzenie nowej ASP.NET Core aplikacji sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-208">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="9ebc6-209">Zaznaczenie tego pola wyboru powoduje dodanie [przekierowania https i HSTS oprogramowania](xref:security/enforcing-ssl) do aplikacji podczas jej tworzenia.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-209">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="9ebc6-210">W przypadku istniejącego projektu wymagającego protokołu HTTPS Użyj przekierowania HTTPS i oprogramowania pośredniczącego HSTS w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-210">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="9ebc6-211">Aby uzyskać więcej informacji, zobacz <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-211">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="9ebc6-212">W przypadku projektu korzystającego z protokołu HTTP, [przekierowania https i oprogramowania pośredniczącego HSTS](xref:security/enforcing-ssl) nie są dodawane do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-212">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="9ebc6-213">Nie jest wymagana żadna konfiguracja aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-213">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="9ebc6-214">Profil uruchamiania usług IIS</span><span class="sxs-lookup"><span data-stu-id="9ebc6-214">IIS launch profile</span></span>

<span data-ttu-id="9ebc6-215">Utwórz nowy profil uruchamiania, aby dodać obsługę usług IIS w czasie projektowania:</span><span class="sxs-lookup"><span data-stu-id="9ebc6-215">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="9ebc6-216">Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań**.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-216">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="9ebc6-217">Wybierz pozycję **Właściwości**.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-217">Select **Properties**.</span></span> <span data-ttu-id="9ebc6-218">Otwórz kartę **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-218">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="9ebc6-219">W obszarze **profil**wybierz przycisk **Nowy** .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-219">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="9ebc6-220">Nazwij profil "IIS" w oknie podręcznym.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-220">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="9ebc6-221">Wybierz **przycisk OK** , aby utworzyć profil.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-221">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="9ebc6-222">Dla ustawienia **uruchamiania** wybierz z listy pozycję **IIS** .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-222">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="9ebc6-223">Zaznacz pole wyboru dla opcji **Uruchom przeglądarkę** i podaj adres URL punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-223">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="9ebc6-224">Gdy aplikacja wymaga protokołu HTTPS, użyj punktu końcowego HTTPS ( `https://` ).</span><span class="sxs-lookup"><span data-stu-id="9ebc6-224">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="9ebc6-225">W przypadku protokołu HTTP Użyj `http://` punktu końcowego http ().</span><span class="sxs-lookup"><span data-stu-id="9ebc6-225">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="9ebc6-226">Podaj tę samą nazwę hosta i port, co [Konfiguracja usług IIS określona wcześniej](#configure-iis), zazwyczaj `localhost` .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-226">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="9ebc6-227">Podaj nazwę aplikacji na końcu adresu URL.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-227">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="9ebc6-228">Na przykład `https://localhost/WebApplication1` (https) lub `http://localhost/WebApplication1` (http) są prawidłowymi adresami URL punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-228">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="9ebc6-229">W sekcji **zmienne środowiskowe** wybierz przycisk **Dodaj** .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-229">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="9ebc6-230">Podaj zmienną środowiskową o **nazwie** `ASPNETCORE_ENVIRONMENT` i **wartości** `Development` .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-230">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="9ebc6-231">W obszarze **Ustawienia serwera sieci Web** Ustaw **adres URL aplikacji** na wartość używaną dla adresu URL punktu końcowego **uruchamiania przeglądarki** .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-231">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="9ebc6-232">W przypadku ustawienia **model hostingu** w programie Visual Studio 2019 lub nowszym wybierz pozycję **domyślne** , aby użyć modelu hostingu używanego przez projekt.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-232">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="9ebc6-233">Jeśli projekt ustawia `<AspNetCoreHostingModel>` Właściwość w pliku projektu, używana jest wartość właściwości ( `InProcess` lub `OutOfProcess` ).</span><span class="sxs-lookup"><span data-stu-id="9ebc6-233">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="9ebc6-234">Jeśli właściwość nie jest obecna, używany jest domyślny model hostingu aplikacji, który jest poza procesem.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-234">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="9ebc6-235">Jeśli aplikacja wymaga jawnego ustawienia modelu hostingu innego niż normalny model hostingu aplikacji, należy ustawić **model hostingu** na albo `In Process` `Out Of Process` w razie potrzeby.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-235">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="9ebc6-236">Zapisz profil.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-236">Save the profile.</span></span>

<span data-ttu-id="9ebc6-237">Gdy nie korzystasz z programu Visual Studio, ręcznie Dodaj profil uruchamiania do [launchSettings.js](https://json.schemastore.org/launchsettings) w pliku w folderze *Właściwości* .</span><span class="sxs-lookup"><span data-stu-id="9ebc6-237">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="9ebc6-238">Poniższy przykład konfiguruje profil do korzystania z protokołu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="9ebc6-238">The following example configures the profile to use the HTTPS protocol:</span></span>

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

<span data-ttu-id="9ebc6-239">Upewnij się, `applicationUrl` że `launchUrl` punkty końcowe i używają tego samego protokołu, co Konfiguracja powiązania usług IIS, http lub https.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-239">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="9ebc6-240">Uruchamianie projektu</span><span class="sxs-lookup"><span data-stu-id="9ebc6-240">Run the project</span></span>

<span data-ttu-id="9ebc6-241">Uruchom program Visual Studio jako administrator:</span><span class="sxs-lookup"><span data-stu-id="9ebc6-241">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="9ebc6-242">Upewnij się, że na liście rozwijanej konfiguracja kompilacji jest ustawiona wartość **Debuguj**.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-242">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="9ebc6-243">Ustaw [przycisk Rozpocznij debugowanie](/visualstudio/debugger/debugger-feature-tour) na profil **usług IIS** i wybierz przycisk, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-243">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="9ebc6-244">Program Visual Studio może monitować o ponowne uruchomienie, jeśli nie jest uruchomiony jako administrator.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-244">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="9ebc6-245">Jeśli zostanie wyświetlony monit, uruchom ponownie program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-245">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="9ebc6-246">Jeśli jest używany niezaufany certyfikat programistyczny, w przeglądarce może być konieczne utworzenie wyjątku dla certyfikatu niezaufanego.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-246">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="9ebc6-247">Debugowanie konfiguracji kompilacji wydania z optymalizacjami [tylko mój kod](/visualstudio/debugger/just-my-code) i kompilatora skutkuje obniżeniem wydajności.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-247">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="9ebc6-248">Na przykład punkty przerwania nie trafią.</span><span class="sxs-lookup"><span data-stu-id="9ebc6-248">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9ebc6-249">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="9ebc6-249">Additional resources</span></span>

* [<span data-ttu-id="9ebc6-250">Wprowadzenie z menedżerem usług IIS w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="9ebc6-250">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
