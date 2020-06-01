---
<span data-ttu-id="cdd24-101">title: "Debug ASP.NET Core Blazor webassembly" Author: guardrex Description: "informacje o debugowaniu Blazor aplikacji".</span><span class="sxs-lookup"><span data-stu-id="cdd24-101">title: 'Debug ASP.NET Core Blazor WebAssembly' author: guardrex description: 'Learn how to debug Blazor apps.'</span></span>
<span data-ttu-id="cdd24-102">monikerRange: ">= aspnetcore-3,1" MS. Author: Riande MS. Custom: MVC MS. Date: 05/31/2020 No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cdd24-102">monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/31/2020 no-loc:</span></span>
- <span data-ttu-id="cdd24-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdd24-103">'Blazor'</span></span>
- <span data-ttu-id="cdd24-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdd24-104">'Identity'</span></span>
- <span data-ttu-id="cdd24-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdd24-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdd24-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdd24-106">'Razor'</span></span>
- <span data-ttu-id="cdd24-107">" SignalR UID: blazor/Debug</span><span class="sxs-lookup"><span data-stu-id="cdd24-107">'SignalR' uid: blazor/debug</span></span>

---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="cdd24-108">Debuguj ASP.NET Core Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="cdd24-108">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="cdd24-109">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="cdd24-109">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor<span data-ttu-id="cdd24-110">Aplikacje webassembly można debugować przy użyciu narzędzi deweloperskich przeglądarki w przeglądarkach opartych na chromie (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="cdd24-110"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="cdd24-111">Alternatywnie możesz debugować aplikację przy użyciu programu Visual Studio lub Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="cdd24-111">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="cdd24-112">Dostępne scenariusze obejmują:</span><span class="sxs-lookup"><span data-stu-id="cdd24-112">Available scenarios include:</span></span>

* <span data-ttu-id="cdd24-113">Ustawianie i usuwanie punktów przerwania.</span><span class="sxs-lookup"><span data-stu-id="cdd24-113">Set and remove breakpoints.</span></span>
* <span data-ttu-id="cdd24-114">Uruchom aplikację z obsługą debugowania w programie Visual Studio i Visual Studio Code (Obsługa<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="cdd24-114">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="cdd24-115">Jeden krok (<kbd>F10</kbd>) za pomocą kodu.</span><span class="sxs-lookup"><span data-stu-id="cdd24-115">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="cdd24-116">Wznów wykonywanie kodu za pomocą <kbd>klawisza F8</kbd> w przeglądarce lub <kbd>F5</kbd> w programie Visual Studio lub Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="cdd24-116">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="cdd24-117">Na ekranie *Ustawienia lokalne* Obserwuj wartości zmiennych lokalnych.</span><span class="sxs-lookup"><span data-stu-id="cdd24-117">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="cdd24-118">Zobacz stos wywołań, w tym łańcuchy wywołań, które pochodzą z języka JavaScript do platformy .NET i z programu .NET do języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cdd24-118">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="cdd24-119">Obecnie *nie*można:</span><span class="sxs-lookup"><span data-stu-id="cdd24-119">For now, you *can't*:</span></span>

* <span data-ttu-id="cdd24-120">Przerwij w przypadku nieobsłużonych wyjątków.</span><span class="sxs-lookup"><span data-stu-id="cdd24-120">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="cdd24-121">Trafij punkty przerwania podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cdd24-121">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="cdd24-122">Będziemy nadal ulepszać środowisko debugowania w przyszłych wersjach.</span><span class="sxs-lookup"><span data-stu-id="cdd24-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cdd24-123">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="cdd24-123">Prerequisites</span></span>

<span data-ttu-id="cdd24-124">Debugowanie wymaga jednej z następujących przeglądarek:</span><span class="sxs-lookup"><span data-stu-id="cdd24-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="cdd24-125">Microsoft Edge (wersja 80 lub nowsza)</span><span class="sxs-lookup"><span data-stu-id="cdd24-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="cdd24-126">Google Chrome (wersja 70 lub nowsza)</span><span class="sxs-lookup"><span data-stu-id="cdd24-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="cdd24-127">Włącz debugowanie dla programu Visual Studio i Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cdd24-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="cdd24-128">Aby włączyć debugowanie istniejącej Blazor aplikacji webassembly, zaktualizuj plik *profilu launchsettings. JSON* w projekcie startowym w celu uwzględnienia następującej `inspectUri` właściwości w każdym profilu uruchamiania:</span><span class="sxs-lookup"><span data-stu-id="cdd24-128">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="cdd24-129">Po zaktualizowaniu plik *profilu launchsettings. JSON* powinien wyglądać podobnie do poniższego przykładu:</span><span class="sxs-lookup"><span data-stu-id="cdd24-129">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="cdd24-130">`inspectUri`Właściwość:</span><span class="sxs-lookup"><span data-stu-id="cdd24-130">The `inspectUri` property:</span></span>

* <span data-ttu-id="cdd24-131">Umożliwia środowisku IDE wykrywanie, czy aplikacja jest Blazor aplikacją webassembly.</span><span class="sxs-lookup"><span data-stu-id="cdd24-131">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="cdd24-132">Powoduje, że infrastruktura debugowania skryptów umożliwia łączenie się z przeglądarką przez Blazor serwer proxy debugowania.</span><span class="sxs-lookup"><span data-stu-id="cdd24-132">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="cdd24-133">Wartości zastępcze protokołu WebSockets Protocol ( `wsProtocol` ), hosta ( `url.hostname` ), portu ( `url.port` ) i kontrolera URI w uruchomionej przeglądarce ( `browserInspectUri` ) są udostępniane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="cdd24-133">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="cdd24-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cdd24-134">Visual Studio</span></span>

<span data-ttu-id="cdd24-135">Aby debugować Blazor aplikację webassembly w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="cdd24-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="cdd24-136">Utwórz nową ASP.NET Core hostowanej Blazor aplikacji sieci webassembly.</span><span class="sxs-lookup"><span data-stu-id="cdd24-136">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="cdd24-137">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację w debugerze.</span><span class="sxs-lookup"><span data-stu-id="cdd24-137">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="cdd24-138">Ustaw punkt przerwania w elemencie *Counter. Razor* w `IncrementCount` metodzie.</span><span class="sxs-lookup"><span data-stu-id="cdd24-138">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="cdd24-139">Przejdź do karty **licznik** i wybierz przycisk, aby trafić w punkt przerwania:</span><span class="sxs-lookup"><span data-stu-id="cdd24-139">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Licznik debugowania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="cdd24-141">Sprawdź wartość `currentCount` pola w oknie zmiennych lokalnych:</span><span class="sxs-lookup"><span data-stu-id="cdd24-141">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Wyświetl elementy lokalne](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="cdd24-143">Naciśnij klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie.</span><span class="sxs-lookup"><span data-stu-id="cdd24-143">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="cdd24-144">Podczas debugowania Blazor aplikacji webassembly można także debugować kod serwera:</span><span class="sxs-lookup"><span data-stu-id="cdd24-144">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="cdd24-145">Ustaw punkt przerwania na stronie *FetchData. Razor* w temacie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="cdd24-145">Set a breakpoint in the *FetchData.razor* page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="cdd24-146">Ustaw punkt przerwania w `WeatherForecastController` `Get` metodzie Action.</span><span class="sxs-lookup"><span data-stu-id="cdd24-146">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="cdd24-147">Przejdź do karty **pobieranie danych** , aby trafić pierwszy punkt przerwania w `FetchData` składniku tuż przed wysłaniem żądania HTTP do serwera:</span><span class="sxs-lookup"><span data-stu-id="cdd24-147">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Debuguj dane pobierania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="cdd24-149">Naciśnij klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie, a następnie naciśnij punkt przerwania na serwerze w `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="cdd24-149">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Serwer debugowania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="cdd24-151">Naciśnij ponownie klawisz <kbd>F5</kbd> , aby umożliwić kontynuowanie wykonywania, a następnie zapoznaj się z renderowaną tabelą prognozy pogody.</span><span class="sxs-lookup"><span data-stu-id="cdd24-151">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="cdd24-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cdd24-152">Visual Studio Code</span></span>

<span data-ttu-id="cdd24-153">Aby debugować Blazor aplikację webassembly w Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="cdd24-153">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
<span data-ttu-id="cdd24-154">Zainstaluj [rozszerzenie C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) i rozszerzenie [JavaScript Debugger (nocne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) z `debug.javascript.usePreview` ustawionym na `true` .</span><span class="sxs-lookup"><span data-stu-id="cdd24-154">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

![Rozszerzenia](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![Debuger podglądu JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a><span data-ttu-id="cdd24-157">Debuguj autonomiczny Blazor zestaw Webassembly</span><span class="sxs-lookup"><span data-stu-id="cdd24-157">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="cdd24-158">Otwórz autonomiczną Blazor aplikację webassembly w vs Code.</span><span class="sxs-lookup"><span data-stu-id="cdd24-158">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="cdd24-159">Jeśli zostanie wyświetlone następujące powiadomienie, że do włączenia debugowania jest wymagana dodatkowa konfiguracja:</span><span class="sxs-lookup"><span data-stu-id="cdd24-159">If you receive the following notification that additional setup is required to enable debugging:</span></span>
   
   * <span data-ttu-id="cdd24-160">Upewnij się, że zainstalowano poprawne rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="cdd24-160">Confirm that you have the correct extensions installed.</span></span>
   * <span data-ttu-id="cdd24-161">Upewnij się, że debugowanie JavaScript Preview jest włączone.</span><span class="sxs-lookup"><span data-stu-id="cdd24-161">Confirm that JavaScript preview debugging is enabled.</span></span>
   * <span data-ttu-id="cdd24-162">Załaduj ponownie okno.</span><span class="sxs-lookup"><span data-stu-id="cdd24-162">Reload the window.</span></span>

   ![Wymagana dodatkowa konfiguracja](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <span data-ttu-id="cdd24-164">Rozpocznij debugowanie przy użyciu skrótu klawiaturowego <kbd>F5</kbd> lub elementu menu.</span><span class="sxs-lookup"><span data-stu-id="cdd24-164">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

1. <span data-ttu-id="cdd24-165">Po wyświetleniu monitu wybierz opcję \*\* Blazor Debuguj zestaw webassembly\*\* , aby rozpocząć debugowanie.</span><span class="sxs-lookup"><span data-stu-id="cdd24-165">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Lista dostępnych opcji debugowania](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="cdd24-167">Uruchomiona jest aplikacja autonomiczna i zostanie otwarta przeglądarka debugowania.</span><span class="sxs-lookup"><span data-stu-id="cdd24-167">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="cdd24-168">Ustaw punkt przerwania w `IncrementCount` metodzie w `Counter` składniku, a następnie wybierz przycisk, aby trafić w punkt przerwania:</span><span class="sxs-lookup"><span data-stu-id="cdd24-168">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Debuguj licznik w VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="cdd24-170">Debuguj hostowany Blazor zestaw Webassembly</span><span class="sxs-lookup"><span data-stu-id="cdd24-170">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="cdd24-171">Otwórz hostowaną Blazor aplikację webassembly w vs Code.</span><span class="sxs-lookup"><span data-stu-id="cdd24-171">Open the hosted Blazor WebAssembly app in VS Code.</span></span>

1. <span data-ttu-id="cdd24-172">Jeśli nie ma ustawionej konfiguracji uruchamiania dla projektu, wyświetlane jest następujące powiadomienie.</span><span class="sxs-lookup"><span data-stu-id="cdd24-172">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="cdd24-173">Wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="cdd24-173">Select **Yes**.</span></span>

   ![Dodaj wymagane zasoby](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="cdd24-175">W oknie Wybór wybierz projekt *serwera* w ramach hostowanego rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="cdd24-175">In the selection window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="cdd24-176">Plik *Launch. JSON* jest generowany z konfiguracją uruchamiania do uruchamiania debugera.</span><span class="sxs-lookup"><span data-stu-id="cdd24-176">A *launch.json* file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="cdd24-177">Dołącz do istniejącej sesji debugowania</span><span class="sxs-lookup"><span data-stu-id="cdd24-177">Attach to an existing debugging session</span></span>

<span data-ttu-id="cdd24-178">Aby dołączyć do uruchomionej Blazor aplikacji, Utwórz plik *Launch. JSON* o następującej konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="cdd24-178">To attach to a running Blazor app, create a *launch.json* file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="cdd24-179">Dołączanie do sesji debugowania jest obsługiwane tylko w przypadku aplikacji autonomicznych.</span><span class="sxs-lookup"><span data-stu-id="cdd24-179">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="cdd24-180">Aby można było używać debugowania pełnego stosu, należy uruchomić aplikację z VS Code.</span><span class="sxs-lookup"><span data-stu-id="cdd24-180">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="cdd24-181">Opcje konfiguracji uruchamiania</span><span class="sxs-lookup"><span data-stu-id="cdd24-181">Launch configuration options</span></span>

<span data-ttu-id="cdd24-182">Dla typu debugowania są obsługiwane następujące opcje konfiguracji uruchamiania `blazorwasm` .</span><span class="sxs-lookup"><span data-stu-id="cdd24-182">The following launch configuration options are supported for the `blazorwasm` debug type.</span></span>

| <span data-ttu-id="cdd24-183">Opcja</span><span class="sxs-lookup"><span data-stu-id="cdd24-183">Option</span></span>    | <span data-ttu-id="cdd24-184">Opis</span><span class="sxs-lookup"><span data-stu-id="cdd24-184">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="cdd24-185">Służy `launch` do uruchamiania i dołączania sesji debugowania do Blazor aplikacji webassembly lub `attach` do dołączania sesji debugowania do już uruchomionej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cdd24-185">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="cdd24-186">Adres URL, który ma zostać otwarty w przeglądarce podczas debugowania.</span><span class="sxs-lookup"><span data-stu-id="cdd24-186">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="cdd24-187">Wartość domyślna to `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="cdd24-187">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="cdd24-188">Przeglądarka do uruchomienia dla sesji debugowania.</span><span class="sxs-lookup"><span data-stu-id="cdd24-188">The browser to launch for the debugging session.</span></span> <span data-ttu-id="cdd24-189">Ustaw `edge` lub `chrome`.</span><span class="sxs-lookup"><span data-stu-id="cdd24-189">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="cdd24-190">Wartość domyślna to `chrome` .</span><span class="sxs-lookup"><span data-stu-id="cdd24-190">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="cdd24-191">Służy do generowania dzienników z debugera JS.</span><span class="sxs-lookup"><span data-stu-id="cdd24-191">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="cdd24-192">Ustaw, aby `true` generować dzienniki.</span><span class="sxs-lookup"><span data-stu-id="cdd24-192">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="cdd24-193">Musi mieć ustawioną wartość w `true` przypadku uruchamiania i debugowania hostowanej Blazor aplikacji sieci webassembly.</span><span class="sxs-lookup"><span data-stu-id="cdd24-193">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="cdd24-194">Określa ścieżkę bezwzględną serwera sieci Web.</span><span class="sxs-lookup"><span data-stu-id="cdd24-194">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="cdd24-195">Należy ustawić, jeśli aplikacja jest obsługiwana z podmarszruty.</span><span class="sxs-lookup"><span data-stu-id="cdd24-195">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="cdd24-196">Liczba milisekund oczekiwania na dołączenie sesji debugowania.</span><span class="sxs-lookup"><span data-stu-id="cdd24-196">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="cdd24-197">Wartość domyślna to 30 000 milisekund (30 sekund).</span><span class="sxs-lookup"><span data-stu-id="cdd24-197">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="cdd24-198">Odwołanie do pliku wykonywalnego, aby uruchomić serwer hostowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cdd24-198">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="cdd24-199">Musi być ustawiona, jeśli `hosted` ma wartość `true` .</span><span class="sxs-lookup"><span data-stu-id="cdd24-199">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="cdd24-200">Katalog roboczy, w którym jest uruchomiona aplikacja.</span><span class="sxs-lookup"><span data-stu-id="cdd24-200">The working directory to launch the app under.</span></span> <span data-ttu-id="cdd24-201">Musi być ustawiona, jeśli `hosted` ma wartość `true` .</span><span class="sxs-lookup"><span data-stu-id="cdd24-201">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="cdd24-202">Zmienne środowiskowe do zapewnienia uruchomionego procesu.</span><span class="sxs-lookup"><span data-stu-id="cdd24-202">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="cdd24-203">Ma zastosowanie tylko wtedy `hosted` , gdy jest ustawiona na `true` .</span><span class="sxs-lookup"><span data-stu-id="cdd24-203">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="cdd24-204">Przykładowe konfiguracje uruchamiania</span><span class="sxs-lookup"><span data-stu-id="cdd24-204">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="cdd24-205">Uruchamianie i debugowanie autonomicznej Blazor aplikacji sieci Webassembly</span><span class="sxs-lookup"><span data-stu-id="cdd24-205">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="cdd24-206">Dołącz do uruchomionej aplikacji pod określonym adresem URL</span><span class="sxs-lookup"><span data-stu-id="cdd24-206">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="cdd24-207">Uruchamianie i debugowanie hostowanej Blazor aplikacji sieci Webassembly</span><span class="sxs-lookup"><span data-stu-id="cdd24-207">Launch and debug a hosted Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a><span data-ttu-id="cdd24-208">Debuguj w przeglądarce</span><span class="sxs-lookup"><span data-stu-id="cdd24-208">Debug in the browser</span></span>

1. <span data-ttu-id="cdd24-209">Uruchom kompilację debugowania aplikacji w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="cdd24-209">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="cdd24-210">Naciśnij klawisze <kbd>SHIFT</kbd> + <kbd>Alt</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="cdd24-210">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="cdd24-211">Przeglądarka musi być uruchomiona z włączonym debugowaniem zdalnym.</span><span class="sxs-lookup"><span data-stu-id="cdd24-211">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="cdd24-212">Jeśli debugowanie zdalne jest wyłączone, **nie można odnaleźć strony błędu karty przeglądarki możliwością debugowania** .</span><span class="sxs-lookup"><span data-stu-id="cdd24-212">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="cdd24-213">Strona błędu zawiera instrukcje dotyczące uruchamiania przeglądarki z otwartym portem debugowania, dzięki czemu Blazor serwer proxy debugowania może połączyć się z aplikacją.</span><span class="sxs-lookup"><span data-stu-id="cdd24-213">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="cdd24-214">*Zamknij wszystkie wystąpienia przeglądarki* i uruchom ponownie przeglądarkę zgodnie z instrukcjami.</span><span class="sxs-lookup"><span data-stu-id="cdd24-214">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="cdd24-215">Po uruchomieniu przeglądarki z włączonym debugowaniem zdalnym skrót klawiaturowy debugowania otwiera nową kartę debugera. Po chwili na karcie **źródła** zostanie wyświetlona lista zestawów .NET w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cdd24-215">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="cdd24-216">Rozwiń każdy zestaw i Znajdź *.cs* / pliki źródłowe CS *. Razor* dostępne do debugowania.</span><span class="sxs-lookup"><span data-stu-id="cdd24-216">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="cdd24-217">Ustaw punkty przerwania, przełącz się z powrotem do karty aplikacji, a punkty przerwania są trafień, gdy kod jest wykonywany.</span><span class="sxs-lookup"><span data-stu-id="cdd24-217">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="cdd24-218">Po trafieniu punktu przerwania pojedynczy krok (<kbd>F10</kbd>) za pomocą kodu lub wznowienia kodu (<kbd>F8</kbd>) normalnie.</span><span class="sxs-lookup"><span data-stu-id="cdd24-218">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="cdd24-219">udostępnia serwer proxy debugowania, który implementuje [Protokół Chrome devtools](https://chromedevtools.github.io/devtools-protocol/) i rozszerza protokół z. Informacje specyficzne dla sieci.</span><span class="sxs-lookup"><span data-stu-id="cdd24-219"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="cdd24-220">Gdy skrót klawiaturowy debugowania zostanie nacionięty, Blazor wskazuje, że program Chrome devtools na serwerze proxy.</span><span class="sxs-lookup"><span data-stu-id="cdd24-220">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="cdd24-221">Serwer proxy nawiązuje połączenie z oknem przeglądarki, które próbujesz debugować (w związku z tym trzeba włączyć debugowanie zdalne).</span><span class="sxs-lookup"><span data-stu-id="cdd24-221">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="cdd24-222">Mapy źródeł przeglądarki</span><span class="sxs-lookup"><span data-stu-id="cdd24-222">Browser source maps</span></span>

<span data-ttu-id="cdd24-223">Mapy źródeł przeglądarki umożliwiają przeglądarce mapowanie skompilowanych plików z powrotem do ich oryginalnych plików źródłowych i są często używane do debugowania po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="cdd24-223">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="cdd24-224">Jednak Blazor obecnie nie mapuje języka C# bezpośrednio do języka JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="cdd24-224">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="cdd24-225">Zamiast tego program Blazor wykonuje interpretację Il w przeglądarce, dlatego mapy źródłowe nie są istotne.</span><span class="sxs-lookup"><span data-stu-id="cdd24-225">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="cdd24-226">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="cdd24-226">Troubleshoot</span></span>

<span data-ttu-id="cdd24-227">Jeśli występują błędy, następujące porady mogą pomóc:</span><span class="sxs-lookup"><span data-stu-id="cdd24-227">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="cdd24-228">Na karcie **debuger** Otwórz narzędzia deweloperskie w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="cdd24-228">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="cdd24-229">W konsoli programu wykonaj polecenie, `localStorage.clear()` Aby usunąć wszystkie punkty przerwania.</span><span class="sxs-lookup"><span data-stu-id="cdd24-229">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="cdd24-230">Upewnij się, że został zainstalowany i zaufany certyfikat programistyczny protokołu HTTPS ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cdd24-230">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="cdd24-231">Aby uzyskać więcej informacji, zobacz <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="cdd24-231">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
