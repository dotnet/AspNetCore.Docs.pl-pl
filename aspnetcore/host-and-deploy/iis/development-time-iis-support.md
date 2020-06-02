---
<span data-ttu-id="7d903-101">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7d903-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7d903-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7d903-102">'Blazor'</span></span>
- <span data-ttu-id="7d903-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7d903-103">'Identity'</span></span>
- <span data-ttu-id="7d903-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7d903-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="7d903-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7d903-105">'Razor'</span></span>
- <span data-ttu-id="7d903-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="7d903-106">'SignalR' uid:</span></span> 

---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="7d903-107">Obsługa usług IIS w czasie opracowywania w programie Visual Studio dla platformy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7d903-107">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="7d903-108">Autor [sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="7d903-108">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7d903-109">W tym artykule opisano obsługę [programu Visual Studio](https://visualstudio.microsoft.com) na potrzeby debugowania ASP.NET Core aplikacje działające z usługami IIS w systemie Windows Server.</span><span class="sxs-lookup"><span data-stu-id="7d903-109">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="7d903-110">Ten temat zawiera instrukcje dotyczące włączania tego scenariusza i konfigurowania projektu.</span><span class="sxs-lookup"><span data-stu-id="7d903-110">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7d903-111">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="7d903-111">Prerequisites</span></span>

* [<span data-ttu-id="7d903-112">Program Visual Studio dla systemu Windows</span><span class="sxs-lookup"><span data-stu-id="7d903-112">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="7d903-113">**ASP.NET i programowanie aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="7d903-113">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="7d903-114">**Tworzenie aplikacji dla wielu platform w środowisku .NET Core**</span><span class="sxs-lookup"><span data-stu-id="7d903-114">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="7d903-115">Certyfikat zabezpieczeń X. 509 (dla obsługi protokołu HTTPS)</span><span class="sxs-lookup"><span data-stu-id="7d903-115">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="7d903-116">Włącz usługi IIS</span><span class="sxs-lookup"><span data-stu-id="7d903-116">Enable IIS</span></span>

1. <span data-ttu-id="7d903-117">W systemie Windows przejdź do pozycji **Panel sterowania** > **programy** > **programy i funkcje** > **Włącz lub wyłącz funkcje systemu Windows** (po lewej stronie ekranu).</span><span class="sxs-lookup"><span data-stu-id="7d903-117">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="7d903-118">Zaznacz pole wyboru **Internet Information Services** .</span><span class="sxs-lookup"><span data-stu-id="7d903-118">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="7d903-119">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="7d903-119">Select **OK**.</span></span>

<span data-ttu-id="7d903-120">Instalacja usług IIS może wymagać ponownego uruchomienia systemu.</span><span class="sxs-lookup"><span data-stu-id="7d903-120">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="7d903-121">Konfigurowanie usług IIS</span><span class="sxs-lookup"><span data-stu-id="7d903-121">Configure IIS</span></span>

<span data-ttu-id="7d903-122">Usługi IIS muszą mieć skonfigurowaną witrynę sieci Web z następującymi:</span><span class="sxs-lookup"><span data-stu-id="7d903-122">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="7d903-123">**Nazwa hosta**: zazwyczaj **Domyślna witryna sieci Web** jest używana z **nazwą hosta** `localhost` .</span><span class="sxs-lookup"><span data-stu-id="7d903-123">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="7d903-124">Jednak każda prawidłowa witryna sieci Web usług IIS z unikatową nazwą hosta działa.</span><span class="sxs-lookup"><span data-stu-id="7d903-124">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="7d903-125">**Powiązanie witryny**</span><span class="sxs-lookup"><span data-stu-id="7d903-125">**Site Binding**</span></span>
  * <span data-ttu-id="7d903-126">W przypadku aplikacji wymagających protokołu HTTPS Utwórz powiązanie z portem 443 z certyfikatem.</span><span class="sxs-lookup"><span data-stu-id="7d903-126">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="7d903-127">Zwykle używany jest **certyfikat deweloperski IIS Express** , ale każdy prawidłowy certyfikat działa.</span><span class="sxs-lookup"><span data-stu-id="7d903-127">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="7d903-128">W przypadku aplikacji korzystających z protokołu HTTP upewnij się, że istnieje powiązanie do opublikowania 80 lub utwórz powiązanie z portem 80 dla nowej lokacji.</span><span class="sxs-lookup"><span data-stu-id="7d903-128">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="7d903-129">Użyj pojedynczego powiązania dla protokołu HTTP lub HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7d903-129">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="7d903-130">**Powiązanie jednocześnie z portami HTTP i HTTPS nie jest obsługiwane.**</span><span class="sxs-lookup"><span data-stu-id="7d903-130">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="7d903-131">Włączanie obsługi usług IIS w czasie opracowywania w programie Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d903-131">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="7d903-132">Uruchom Instalatora programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7d903-132">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="7d903-133">Wybierz pozycję **Modyfikuj** dla instalacji programu Visual Studio, która ma być używana na potrzeby obsługi czasu projektowania usług IIS.</span><span class="sxs-lookup"><span data-stu-id="7d903-133">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="7d903-134">W przypadku obciążeń **ASP.NET i sieci Web** Znajdź i Zainstaluj składnik **Obsługa usług IIS w czasie opracowywania** .</span><span class="sxs-lookup"><span data-stu-id="7d903-134">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="7d903-135">Składnik jest wymieniony w sekcji **opcjonalne** w obszarze **czas projektowania obsługa usług IIS** w panelu **szczegóły instalacji** po prawej stronie obciążeń.</span><span class="sxs-lookup"><span data-stu-id="7d903-135">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="7d903-136">Składnik instaluje [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module), który jest natywnym modułem usług IIS wymaganym do uruchamiania aplikacji ASP.NET Core za pomocą usług IIS.</span><span class="sxs-lookup"><span data-stu-id="7d903-136">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="7d903-137">Konfigurowanie projektu</span><span class="sxs-lookup"><span data-stu-id="7d903-137">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="7d903-138">Przekierowanie HTTPS</span><span class="sxs-lookup"><span data-stu-id="7d903-138">HTTPS redirection</span></span>

<span data-ttu-id="7d903-139">Dla nowego projektu, który wymaga protokołu HTTPS, zaznacz pole wyboru w celu **skonfigurowania protokołu HTTPS** w oknie **Tworzenie nowej ASP.NET Core aplikacji sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="7d903-139">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="7d903-140">Zaznaczenie tego pola wyboru powoduje dodanie [przekierowania https i HSTS oprogramowania](xref:security/enforcing-ssl) do aplikacji podczas jej tworzenia.</span><span class="sxs-lookup"><span data-stu-id="7d903-140">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="7d903-141">W przypadku istniejącego projektu wymagającego protokołu HTTPS Użyj przekierowania HTTPS i oprogramowania pośredniczącego HSTS w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="7d903-141">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="7d903-142">Aby uzyskać więcej informacji, zobacz <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="7d903-142">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="7d903-143">W przypadku projektu korzystającego z protokołu HTTP, [przekierowania https i oprogramowania pośredniczącego HSTS](xref:security/enforcing-ssl) nie są dodawane do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7d903-143">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="7d903-144">Nie jest wymagana żadna konfiguracja aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7d903-144">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="7d903-145">Profil uruchamiania usług IIS</span><span class="sxs-lookup"><span data-stu-id="7d903-145">IIS launch profile</span></span>

<span data-ttu-id="7d903-146">Utwórz nowy profil uruchamiania, aby dodać obsługę usług IIS w czasie projektowania:</span><span class="sxs-lookup"><span data-stu-id="7d903-146">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="7d903-147">Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań**.</span><span class="sxs-lookup"><span data-stu-id="7d903-147">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="7d903-148">Wybierz pozycję **Właściwości**.</span><span class="sxs-lookup"><span data-stu-id="7d903-148">Select **Properties**.</span></span> <span data-ttu-id="7d903-149">Otwórz kartę **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="7d903-149">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="7d903-150">W obszarze **profil**wybierz przycisk **Nowy** .</span><span class="sxs-lookup"><span data-stu-id="7d903-150">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="7d903-151">Nazwij profil "IIS" w oknie podręcznym.</span><span class="sxs-lookup"><span data-stu-id="7d903-151">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="7d903-152">Wybierz **przycisk OK** , aby utworzyć profil.</span><span class="sxs-lookup"><span data-stu-id="7d903-152">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="7d903-153">Dla ustawienia **uruchamiania** wybierz z listy pozycję **IIS** .</span><span class="sxs-lookup"><span data-stu-id="7d903-153">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="7d903-154">Zaznacz pole wyboru dla opcji **Uruchom przeglądarkę** i podaj adres URL punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="7d903-154">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="7d903-155">Gdy aplikacja wymaga protokołu HTTPS, użyj punktu końcowego HTTPS ( `https://` ).</span><span class="sxs-lookup"><span data-stu-id="7d903-155">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="7d903-156">W przypadku protokołu HTTP Użyj `http://` punktu końcowego http ().</span><span class="sxs-lookup"><span data-stu-id="7d903-156">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="7d903-157">Podaj tę samą nazwę hosta i port, co [Konfiguracja usług IIS określona wcześniej](#configure-iis), zazwyczaj `localhost` .</span><span class="sxs-lookup"><span data-stu-id="7d903-157">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="7d903-158">Podaj nazwę aplikacji na końcu adresu URL.</span><span class="sxs-lookup"><span data-stu-id="7d903-158">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="7d903-159">Na przykład `https://localhost/WebApplication1` (https) lub `http://localhost/WebApplication1` (http) są prawidłowymi adresami URL punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="7d903-159">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="7d903-160">W sekcji **zmienne środowiskowe** wybierz przycisk **Dodaj** .</span><span class="sxs-lookup"><span data-stu-id="7d903-160">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="7d903-161">Podaj zmienną środowiskową o **nazwie** `ASPNETCORE_ENVIRONMENT` i **wartości** `Development` .</span><span class="sxs-lookup"><span data-stu-id="7d903-161">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="7d903-162">W obszarze **Ustawienia serwera sieci Web** Ustaw **adres URL aplikacji** na wartość używaną dla adresu URL punktu końcowego **uruchamiania przeglądarki** .</span><span class="sxs-lookup"><span data-stu-id="7d903-162">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="7d903-163">W przypadku ustawienia **model hostingu** w programie Visual Studio 2019 lub nowszym wybierz pozycję **domyślne** , aby użyć modelu hostingu używanego przez projekt.</span><span class="sxs-lookup"><span data-stu-id="7d903-163">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="7d903-164">Jeśli projekt ustawia `<AspNetCoreHostingModel>` Właściwość w pliku projektu, używana jest wartość właściwości ( `InProcess` lub `OutOfProcess` ).</span><span class="sxs-lookup"><span data-stu-id="7d903-164">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="7d903-165">Jeśli właściwość nie jest obecna, używany jest domyślny model hostingu aplikacji, który jest w procesie.</span><span class="sxs-lookup"><span data-stu-id="7d903-165">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="7d903-166">Jeśli aplikacja wymaga jawnego ustawienia modelu hostingu innego niż normalny model hostingu aplikacji, należy ustawić **model hostingu** na albo `In Process` `Out Of Process` w razie potrzeby.</span><span class="sxs-lookup"><span data-stu-id="7d903-166">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="7d903-167">Zapisz profil.</span><span class="sxs-lookup"><span data-stu-id="7d903-167">Save the profile.</span></span>

<span data-ttu-id="7d903-168">Gdy nie korzystasz z programu Visual Studio, ręcznie Dodaj profil uruchamiania do pliku [profilu launchsettings. JSON](https://json.schemastore.org/launchsettings) w folderze *Properties* .</span><span class="sxs-lookup"><span data-stu-id="7d903-168">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="7d903-169">Poniższy przykład konfiguruje profil do korzystania z protokołu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="7d903-169">The following example configures the profile to use the HTTPS protocol:</span></span>

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

<span data-ttu-id="7d903-170">Upewnij się, `applicationUrl` że `launchUrl` punkty końcowe i używają tego samego protokołu, co Konfiguracja powiązania usług IIS, http lub https.</span><span class="sxs-lookup"><span data-stu-id="7d903-170">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="7d903-171">Uruchamianie projektu</span><span class="sxs-lookup"><span data-stu-id="7d903-171">Run the project</span></span>

<span data-ttu-id="7d903-172">Uruchom program Visual Studio jako administrator:</span><span class="sxs-lookup"><span data-stu-id="7d903-172">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="7d903-173">Upewnij się, że na liście rozwijanej konfiguracja kompilacji jest ustawiona wartość **Debuguj**.</span><span class="sxs-lookup"><span data-stu-id="7d903-173">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="7d903-174">Ustaw [przycisk Rozpocznij debugowanie](/visualstudio/debugger/debugger-feature-tour) na profil **usług IIS** i wybierz przycisk, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="7d903-174">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="7d903-175">Program Visual Studio może monitować o ponowne uruchomienie, jeśli nie jest uruchomiony jako administrator.</span><span class="sxs-lookup"><span data-stu-id="7d903-175">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="7d903-176">Jeśli zostanie wyświetlony monit, uruchom ponownie program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7d903-176">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="7d903-177">Jeśli jest używany niezaufany certyfikat programistyczny, w przeglądarce może być konieczne utworzenie wyjątku dla certyfikatu niezaufanego.</span><span class="sxs-lookup"><span data-stu-id="7d903-177">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="7d903-178">Debugowanie konfiguracji kompilacji wydania z optymalizacjami [tylko mój kod](/visualstudio/debugger/just-my-code) i kompilatora skutkuje obniżeniem wydajności.</span><span class="sxs-lookup"><span data-stu-id="7d903-178">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="7d903-179">Na przykład punkty przerwania nie trafią.</span><span class="sxs-lookup"><span data-stu-id="7d903-179">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7d903-180">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="7d903-180">Additional resources</span></span>

* [<span data-ttu-id="7d903-181">Wprowadzenie z menedżerem usług IIS w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="7d903-181">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7d903-182">W tym artykule opisano obsługę [programu Visual Studio](https://visualstudio.microsoft.com) na potrzeby debugowania ASP.NET Core aplikacje działające z usługami IIS w systemie Windows Server.</span><span class="sxs-lookup"><span data-stu-id="7d903-182">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="7d903-183">Ten temat zawiera instrukcje dotyczące włączania tego scenariusza i konfigurowania projektu.</span><span class="sxs-lookup"><span data-stu-id="7d903-183">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7d903-184">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="7d903-184">Prerequisites</span></span>

* [<span data-ttu-id="7d903-185">Program Visual Studio dla systemu Windows</span><span class="sxs-lookup"><span data-stu-id="7d903-185">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="7d903-186">**ASP.NET i programowanie aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="7d903-186">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="7d903-187">**Tworzenie aplikacji dla wielu platform w środowisku .NET Core**</span><span class="sxs-lookup"><span data-stu-id="7d903-187">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="7d903-188">Certyfikat zabezpieczeń X. 509 (dla obsługi protokołu HTTPS)</span><span class="sxs-lookup"><span data-stu-id="7d903-188">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="7d903-189">Włącz usługi IIS</span><span class="sxs-lookup"><span data-stu-id="7d903-189">Enable IIS</span></span>

1. <span data-ttu-id="7d903-190">W systemie Windows przejdź do pozycji **Panel sterowania** > **programy** > **programy i funkcje** > **Włącz lub wyłącz funkcje systemu Windows** (po lewej stronie ekranu).</span><span class="sxs-lookup"><span data-stu-id="7d903-190">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="7d903-191">Zaznacz pole wyboru **Internet Information Services** .</span><span class="sxs-lookup"><span data-stu-id="7d903-191">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="7d903-192">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="7d903-192">Select **OK**.</span></span>

<span data-ttu-id="7d903-193">Instalacja usług IIS może wymagać ponownego uruchomienia systemu.</span><span class="sxs-lookup"><span data-stu-id="7d903-193">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="7d903-194">Konfigurowanie usług IIS</span><span class="sxs-lookup"><span data-stu-id="7d903-194">Configure IIS</span></span>

<span data-ttu-id="7d903-195">Usługi IIS muszą mieć skonfigurowaną witrynę sieci Web z następującymi:</span><span class="sxs-lookup"><span data-stu-id="7d903-195">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="7d903-196">**Nazwa hosta**: zazwyczaj **Domyślna witryna sieci Web** jest używana z **nazwą hosta** `localhost` .</span><span class="sxs-lookup"><span data-stu-id="7d903-196">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="7d903-197">Jednak każda prawidłowa witryna sieci Web usług IIS z unikatową nazwą hosta działa.</span><span class="sxs-lookup"><span data-stu-id="7d903-197">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="7d903-198">**Powiązanie witryny**</span><span class="sxs-lookup"><span data-stu-id="7d903-198">**Site Binding**</span></span>
  * <span data-ttu-id="7d903-199">W przypadku aplikacji wymagających protokołu HTTPS Utwórz powiązanie z portem 443 z certyfikatem.</span><span class="sxs-lookup"><span data-stu-id="7d903-199">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="7d903-200">Zwykle używany jest **certyfikat deweloperski IIS Express** , ale każdy prawidłowy certyfikat działa.</span><span class="sxs-lookup"><span data-stu-id="7d903-200">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="7d903-201">W przypadku aplikacji korzystających z protokołu HTTP upewnij się, że istnieje powiązanie do opublikowania 80 lub utwórz powiązanie z portem 80 dla nowej lokacji.</span><span class="sxs-lookup"><span data-stu-id="7d903-201">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="7d903-202">Użyj pojedynczego powiązania dla protokołu HTTP lub HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7d903-202">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="7d903-203">**Powiązanie jednocześnie z portami HTTP i HTTPS nie jest obsługiwane.**</span><span class="sxs-lookup"><span data-stu-id="7d903-203">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="7d903-204">Włączanie obsługi usług IIS w czasie opracowywania w programie Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d903-204">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="7d903-205">Uruchom Instalatora programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7d903-205">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="7d903-206">Wybierz pozycję **Modyfikuj** dla instalacji programu Visual Studio, która ma być używana na potrzeby obsługi czasu projektowania usług IIS.</span><span class="sxs-lookup"><span data-stu-id="7d903-206">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="7d903-207">W przypadku obciążeń **ASP.NET i sieci Web** Znajdź i Zainstaluj składnik **Obsługa usług IIS w czasie opracowywania** .</span><span class="sxs-lookup"><span data-stu-id="7d903-207">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="7d903-208">Składnik jest wymieniony w sekcji **opcjonalne** w obszarze **czas projektowania obsługa usług IIS** w panelu **szczegóły instalacji** po prawej stronie obciążeń.</span><span class="sxs-lookup"><span data-stu-id="7d903-208">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="7d903-209">Składnik instaluje [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module), który jest natywnym modułem usług IIS wymaganym do uruchamiania aplikacji ASP.NET Core za pomocą usług IIS.</span><span class="sxs-lookup"><span data-stu-id="7d903-209">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="7d903-210">Konfigurowanie projektu</span><span class="sxs-lookup"><span data-stu-id="7d903-210">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="7d903-211">Przekierowanie HTTPS</span><span class="sxs-lookup"><span data-stu-id="7d903-211">HTTPS redirection</span></span>

<span data-ttu-id="7d903-212">Dla nowego projektu, który wymaga protokołu HTTPS, zaznacz pole wyboru w celu **skonfigurowania protokołu HTTPS** w oknie **Tworzenie nowej ASP.NET Core aplikacji sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="7d903-212">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="7d903-213">Zaznaczenie tego pola wyboru powoduje dodanie [przekierowania https i HSTS oprogramowania](xref:security/enforcing-ssl) do aplikacji podczas jej tworzenia.</span><span class="sxs-lookup"><span data-stu-id="7d903-213">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="7d903-214">W przypadku istniejącego projektu wymagającego protokołu HTTPS Użyj przekierowania HTTPS i oprogramowania pośredniczącego HSTS w `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="7d903-214">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="7d903-215">Aby uzyskać więcej informacji, zobacz <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="7d903-215">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="7d903-216">W przypadku projektu korzystającego z protokołu HTTP, [przekierowania https i oprogramowania pośredniczącego HSTS](xref:security/enforcing-ssl) nie są dodawane do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7d903-216">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="7d903-217">Nie jest wymagana żadna konfiguracja aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7d903-217">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="7d903-218">Profil uruchamiania usług IIS</span><span class="sxs-lookup"><span data-stu-id="7d903-218">IIS launch profile</span></span>

<span data-ttu-id="7d903-219">Utwórz nowy profil uruchamiania, aby dodać obsługę usług IIS w czasie projektowania:</span><span class="sxs-lookup"><span data-stu-id="7d903-219">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="7d903-220">Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań**.</span><span class="sxs-lookup"><span data-stu-id="7d903-220">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="7d903-221">Wybierz pozycję **Właściwości**.</span><span class="sxs-lookup"><span data-stu-id="7d903-221">Select **Properties**.</span></span> <span data-ttu-id="7d903-222">Otwórz kartę **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="7d903-222">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="7d903-223">W obszarze **profil**wybierz przycisk **Nowy** .</span><span class="sxs-lookup"><span data-stu-id="7d903-223">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="7d903-224">Nazwij profil "IIS" w oknie podręcznym.</span><span class="sxs-lookup"><span data-stu-id="7d903-224">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="7d903-225">Wybierz **przycisk OK** , aby utworzyć profil.</span><span class="sxs-lookup"><span data-stu-id="7d903-225">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="7d903-226">Dla ustawienia **uruchamiania** wybierz z listy pozycję **IIS** .</span><span class="sxs-lookup"><span data-stu-id="7d903-226">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="7d903-227">Zaznacz pole wyboru dla opcji **Uruchom przeglądarkę** i podaj adres URL punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="7d903-227">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="7d903-228">Gdy aplikacja wymaga protokołu HTTPS, użyj punktu końcowego HTTPS ( `https://` ).</span><span class="sxs-lookup"><span data-stu-id="7d903-228">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="7d903-229">W przypadku protokołu HTTP Użyj `http://` punktu końcowego http ().</span><span class="sxs-lookup"><span data-stu-id="7d903-229">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="7d903-230">Podaj tę samą nazwę hosta i port, co [Konfiguracja usług IIS określona wcześniej](#configure-iis), zazwyczaj `localhost` .</span><span class="sxs-lookup"><span data-stu-id="7d903-230">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="7d903-231">Podaj nazwę aplikacji na końcu adresu URL.</span><span class="sxs-lookup"><span data-stu-id="7d903-231">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="7d903-232">Na przykład `https://localhost/WebApplication1` (https) lub `http://localhost/WebApplication1` (http) są prawidłowymi adresami URL punktów końcowych.</span><span class="sxs-lookup"><span data-stu-id="7d903-232">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="7d903-233">W sekcji **zmienne środowiskowe** wybierz przycisk **Dodaj** .</span><span class="sxs-lookup"><span data-stu-id="7d903-233">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="7d903-234">Podaj zmienną środowiskową o **nazwie** `ASPNETCORE_ENVIRONMENT` i **wartości** `Development` .</span><span class="sxs-lookup"><span data-stu-id="7d903-234">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="7d903-235">W obszarze **Ustawienia serwera sieci Web** Ustaw **adres URL aplikacji** na wartość używaną dla adresu URL punktu końcowego **uruchamiania przeglądarki** .</span><span class="sxs-lookup"><span data-stu-id="7d903-235">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="7d903-236">W przypadku ustawienia **model hostingu** w programie Visual Studio 2019 lub nowszym wybierz pozycję **domyślne** , aby użyć modelu hostingu używanego przez projekt.</span><span class="sxs-lookup"><span data-stu-id="7d903-236">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="7d903-237">Jeśli projekt ustawia `<AspNetCoreHostingModel>` Właściwość w pliku projektu, używana jest wartość właściwości ( `InProcess` lub `OutOfProcess` ).</span><span class="sxs-lookup"><span data-stu-id="7d903-237">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="7d903-238">Jeśli właściwość nie jest obecna, używany jest domyślny model hostingu aplikacji, który jest poza procesem.</span><span class="sxs-lookup"><span data-stu-id="7d903-238">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="7d903-239">Jeśli aplikacja wymaga jawnego ustawienia modelu hostingu innego niż normalny model hostingu aplikacji, należy ustawić **model hostingu** na albo `In Process` `Out Of Process` w razie potrzeby.</span><span class="sxs-lookup"><span data-stu-id="7d903-239">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="7d903-240">Zapisz profil.</span><span class="sxs-lookup"><span data-stu-id="7d903-240">Save the profile.</span></span>

<span data-ttu-id="7d903-241">Gdy nie korzystasz z programu Visual Studio, ręcznie Dodaj profil uruchamiania do pliku [profilu launchsettings. JSON](https://json.schemastore.org/launchsettings) w folderze *Properties* .</span><span class="sxs-lookup"><span data-stu-id="7d903-241">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="7d903-242">Poniższy przykład konfiguruje profil do korzystania z protokołu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="7d903-242">The following example configures the profile to use the HTTPS protocol:</span></span>

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

<span data-ttu-id="7d903-243">Upewnij się, `applicationUrl` że `launchUrl` punkty końcowe i używają tego samego protokołu, co Konfiguracja powiązania usług IIS, http lub https.</span><span class="sxs-lookup"><span data-stu-id="7d903-243">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="7d903-244">Uruchamianie projektu</span><span class="sxs-lookup"><span data-stu-id="7d903-244">Run the project</span></span>

<span data-ttu-id="7d903-245">Uruchom program Visual Studio jako administrator:</span><span class="sxs-lookup"><span data-stu-id="7d903-245">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="7d903-246">Upewnij się, że na liście rozwijanej konfiguracja kompilacji jest ustawiona wartość **Debuguj**.</span><span class="sxs-lookup"><span data-stu-id="7d903-246">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="7d903-247">Ustaw [przycisk Rozpocznij debugowanie](/visualstudio/debugger/debugger-feature-tour) na profil **usług IIS** i wybierz przycisk, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="7d903-247">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="7d903-248">Program Visual Studio może monitować o ponowne uruchomienie, jeśli nie jest uruchomiony jako administrator.</span><span class="sxs-lookup"><span data-stu-id="7d903-248">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="7d903-249">Jeśli zostanie wyświetlony monit, uruchom ponownie program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7d903-249">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="7d903-250">Jeśli jest używany niezaufany certyfikat programistyczny, w przeglądarce może być konieczne utworzenie wyjątku dla certyfikatu niezaufanego.</span><span class="sxs-lookup"><span data-stu-id="7d903-250">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="7d903-251">Debugowanie konfiguracji kompilacji wydania z optymalizacjami [tylko mój kod](/visualstudio/debugger/just-my-code) i kompilatora skutkuje obniżeniem wydajności.</span><span class="sxs-lookup"><span data-stu-id="7d903-251">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="7d903-252">Na przykład punkty przerwania nie trafią.</span><span class="sxs-lookup"><span data-stu-id="7d903-252">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7d903-253">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="7d903-253">Additional resources</span></span>

* [<span data-ttu-id="7d903-254">Wprowadzenie z menedżerem usług IIS w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="7d903-254">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
