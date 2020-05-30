---
<span data-ttu-id="5f045-101">title: "Debug ASP.NET Core Blazor webassembly" Author: guardrex Description: "informacje o debugowaniu Blazor aplikacji".</span><span class="sxs-lookup"><span data-stu-id="5f045-101">title: 'Debug ASP.NET Core Blazor WebAssembly' author: guardrex description: 'Learn how to debug Blazor apps.'</span></span>
<span data-ttu-id="5f045-102">monikerRange: ">= aspnetcore-3,1" MS. Author: Riande MS. Custom: MVC MS. Date: 05/29/2020 No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5f045-102">monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/29/2020 no-loc:</span></span>
- <span data-ttu-id="5f045-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5f045-103">'Blazor'</span></span>
- <span data-ttu-id="5f045-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5f045-104">'Identity'</span></span>
- <span data-ttu-id="5f045-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5f045-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="5f045-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5f045-106">'Razor'</span></span>
- <span data-ttu-id="5f045-107">" SignalR UID: blazor/Debug</span><span class="sxs-lookup"><span data-stu-id="5f045-107">'SignalR' uid: blazor/debug</span></span>

---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="5f045-108">Debuguj ASP.NET Core Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="5f045-108">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="5f045-109">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="5f045-109">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor<span data-ttu-id="5f045-110">Aplikacje webassembly można debugować przy użyciu narzędzi deweloperskich przeglądarki w przeglądarkach opartych na chromie (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="5f045-110"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="5f045-111">Alternatywnie możesz debugować aplikację przy użyciu programu Visual Studio lub Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5f045-111">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="5f045-112">Dostępne scenariusze obejmują:</span><span class="sxs-lookup"><span data-stu-id="5f045-112">Available scenarios include:</span></span>

* <span data-ttu-id="5f045-113">Ustawianie i usuwanie punktów przerwania.</span><span class="sxs-lookup"><span data-stu-id="5f045-113">Set and remove breakpoints.</span></span>
* <span data-ttu-id="5f045-114">Uruchom aplikację z obsługą debugowania w programie Visual Studio i Visual Studio Code (Obsługa<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="5f045-114">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="5f045-115">Jeden krok (<kbd>F10</kbd>) za pomocą kodu.</span><span class="sxs-lookup"><span data-stu-id="5f045-115">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="5f045-116">Wznów wykonywanie kodu za pomocą <kbd>klawisza F8</kbd> w przeglądarce lub <kbd>F5</kbd> w programie Visual Studio lub Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5f045-116">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="5f045-117">Na ekranie *Ustawienia lokalne* Obserwuj wartości zmiennych lokalnych.</span><span class="sxs-lookup"><span data-stu-id="5f045-117">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="5f045-118">Zobacz stos wywołań, w tym łańcuchy wywołań, które pochodzą z języka JavaScript do platformy .NET i z programu .NET do języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5f045-118">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="5f045-119">Obecnie *nie*można:</span><span class="sxs-lookup"><span data-stu-id="5f045-119">For now, you *can't*:</span></span>

* <span data-ttu-id="5f045-120">Przerwij w przypadku nieobsłużonych wyjątków.</span><span class="sxs-lookup"><span data-stu-id="5f045-120">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="5f045-121">Trafij punkty przerwania podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5f045-121">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="5f045-122">Będziemy nadal ulepszać środowisko debugowania w przyszłych wersjach.</span><span class="sxs-lookup"><span data-stu-id="5f045-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5f045-123">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="5f045-123">Prerequisites</span></span>

<span data-ttu-id="5f045-124">Debugowanie wymaga jednej z następujących przeglądarek:</span><span class="sxs-lookup"><span data-stu-id="5f045-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="5f045-125">Microsoft Edge (wersja 80 lub nowsza)</span><span class="sxs-lookup"><span data-stu-id="5f045-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="5f045-126">Google Chrome (wersja 70 lub nowsza)</span><span class="sxs-lookup"><span data-stu-id="5f045-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="5f045-127">Włącz debugowanie dla programu Visual Studio i Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5f045-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="5f045-128">Aby włączyć debugowanie istniejącej Blazor aplikacji webassembly, zaktualizuj plik *profilu launchsettings. JSON* w projekcie startowym w celu uwzględnienia następującej `inspectUri` właściwości w każdym profilu uruchamiania:</span><span class="sxs-lookup"><span data-stu-id="5f045-128">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="5f045-129">Po zaktualizowaniu plik *profilu launchsettings. JSON* powinien wyglądać podobnie do poniższego przykładu:</span><span class="sxs-lookup"><span data-stu-id="5f045-129">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="5f045-130">`inspectUri`Właściwość:</span><span class="sxs-lookup"><span data-stu-id="5f045-130">The `inspectUri` property:</span></span>

* <span data-ttu-id="5f045-131">Umożliwia środowisku IDE wykrywanie, czy aplikacja jest Blazor aplikacją webassembly.</span><span class="sxs-lookup"><span data-stu-id="5f045-131">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="5f045-132">Powoduje, że infrastruktura debugowania skryptów umożliwia łączenie się z przeglądarką przez Blazor serwer proxy debugowania.</span><span class="sxs-lookup"><span data-stu-id="5f045-132">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="5f045-133">Wartości zastępcze protokołu WebSockets Protocol ( `wsProtocol` ), hosta ( `url.hostname` ), portu ( `url.port` ) i kontrolera URI w uruchomionej przeglądarce ( `browserInspectUri` ) są udostępniane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="5f045-133">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="5f045-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f045-134">Visual Studio</span></span>

<span data-ttu-id="5f045-135">Aby debugować Blazor aplikację webassembly w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="5f045-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="5f045-136">Utwórz nową ASP.NET Core hostowanej Blazor aplikacji sieci webassembly.</span><span class="sxs-lookup"><span data-stu-id="5f045-136">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="5f045-137">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację w debugerze.</span><span class="sxs-lookup"><span data-stu-id="5f045-137">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="5f045-138">Ustaw punkt przerwania w elemencie *Counter. Razor* w `IncrementCount` metodzie.</span><span class="sxs-lookup"><span data-stu-id="5f045-138">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="5f045-139">Przejdź do karty **licznik** i wybierz przycisk, aby trafić w punkt przerwania:</span><span class="sxs-lookup"><span data-stu-id="5f045-139">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Licznik debugowania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="5f045-141">Sprawdź wartość `currentCount` pola w oknie zmiennych lokalnych:</span><span class="sxs-lookup"><span data-stu-id="5f045-141">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Wyświetl elementy lokalne](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="5f045-143">Naciśnij klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie.</span><span class="sxs-lookup"><span data-stu-id="5f045-143">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="5f045-144">Podczas debugowania Blazor aplikacji webassembly można także debugować kod serwera:</span><span class="sxs-lookup"><span data-stu-id="5f045-144">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="5f045-145">Ustaw punkt przerwania na stronie *FetchData. Razor* w temacie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="5f045-145">Set a breakpoint in the *FetchData.razor* page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="5f045-146">Ustaw punkt przerwania w `WeatherForecastController` `Get` metodzie Action.</span><span class="sxs-lookup"><span data-stu-id="5f045-146">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="5f045-147">Przejdź do karty **pobieranie danych** , aby trafić pierwszy punkt przerwania w `FetchData` składniku tuż przed wysłaniem żądania HTTP do serwera:</span><span class="sxs-lookup"><span data-stu-id="5f045-147">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Debuguj dane pobierania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="5f045-149">Naciśnij klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie, a następnie naciśnij punkt przerwania na serwerze w `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="5f045-149">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Serwer debugowania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="5f045-151">Naciśnij ponownie klawisz <kbd>F5</kbd> , aby umożliwić kontynuowanie wykonywania, a następnie zapoznaj się z renderowaną tabelą prognozy pogody.</span><span class="sxs-lookup"><span data-stu-id="5f045-151">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="5f045-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5f045-152">Visual Studio Code</span></span>

<span data-ttu-id="5f045-153">Aby debugować Blazor aplikację webassembly w Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="5f045-153">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="5f045-154">Zainstaluj [rozszerzenie C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) i rozszerzenie [JavaScript Debugger (nocne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) z `debug.javascript.usePreview` ustawionym na `true` .</span><span class="sxs-lookup"><span data-stu-id="5f045-154">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Rozszerzenia](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Debuger podglądu JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="5f045-157">Otwórz istniejącą Blazor aplikację webassembly z włączonym debugowaniem.</span><span class="sxs-lookup"><span data-stu-id="5f045-157">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="5f045-158">Jeśli otrzymujesz następujące powiadomienie, że do włączenia debugowania jest wymagana dodatkowa konfiguracja, upewnij się, że są zainstalowane odpowiednie rozszerzenia i włączono debugowanie JavaScript w wersji zapoznawczej, a następnie załaduj ponownie okno:</span><span class="sxs-lookup"><span data-stu-id="5f045-158">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Wymagana dodatkowa konfiguracja](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="5f045-160">Powiadomienia umożliwiają dodanie wymaganych zasobów do aplikacji na potrzeby kompilowania i debugowania.</span><span class="sxs-lookup"><span data-stu-id="5f045-160">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="5f045-161">Wybierz opcję **tak**:</span><span class="sxs-lookup"><span data-stu-id="5f045-161">Select **Yes**:</span></span>

     ![Dodaj wymagane zasoby](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="5f045-163">Uruchamianie aplikacji w debugerze jest procesem dwuetapowym:</span><span class="sxs-lookup"><span data-stu-id="5f045-163">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="5f045-164">1 \.</span><span class="sxs-lookup"><span data-stu-id="5f045-164">1\.</span></span> <span data-ttu-id="5f045-165">**Najpierw**Uruchom aplikację przy użyciu konfiguracji uruchamiania **uruchamiania programu .NET Core ( Blazor Standalone)** .</span><span class="sxs-lookup"><span data-stu-id="5f045-165">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="5f045-166">2 \.</span><span class="sxs-lookup"><span data-stu-id="5f045-166">2\.</span></span> <span data-ttu-id="5f045-167">**Po uruchomieniu aplikacji**Uruchom przeglądarkę przy użyciu **zestawu .NET Core Debug Blazor Web Assembly w** konfiguracji uruchamiania programu Chrome (wymaga programu Chrome).</span><span class="sxs-lookup"><span data-stu-id="5f045-167">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="5f045-168">Aby użyć krawędzi zamiast przeglądarki Chrome, Zmień `type` konfigurację uruchamiania w pliku *. programu vscode/Launch. JSON* z `pwa-chrome` na `pwa-msedge` .</span><span class="sxs-lookup"><span data-stu-id="5f045-168">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="5f045-169">Ustaw punkt przerwania w `IncrementCount` metodzie w `Counter` składniku, a następnie wybierz przycisk, aby trafić w punkt przerwania:</span><span class="sxs-lookup"><span data-stu-id="5f045-169">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Debuguj licznik w VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="5f045-171">Debuguj w przeglądarce</span><span class="sxs-lookup"><span data-stu-id="5f045-171">Debug in the browser</span></span>

1. <span data-ttu-id="5f045-172">Uruchom kompilację debugowania aplikacji w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="5f045-172">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="5f045-173">Naciśnij klawisze <kbd>SHIFT</kbd> + <kbd>Alt</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="5f045-173">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="5f045-174">Przeglądarka musi być uruchomiona z włączonym debugowaniem zdalnym.</span><span class="sxs-lookup"><span data-stu-id="5f045-174">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="5f045-175">Jeśli debugowanie zdalne jest wyłączone, **nie można odnaleźć strony błędu karty przeglądarki możliwością debugowania** .</span><span class="sxs-lookup"><span data-stu-id="5f045-175">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="5f045-176">Strona błędu zawiera instrukcje dotyczące uruchamiania przeglądarki z otwartym portem debugowania, dzięki czemu Blazor serwer proxy debugowania może połączyć się z aplikacją.</span><span class="sxs-lookup"><span data-stu-id="5f045-176">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="5f045-177">*Zamknij wszystkie wystąpienia przeglądarki* i uruchom ponownie przeglądarkę zgodnie z instrukcjami.</span><span class="sxs-lookup"><span data-stu-id="5f045-177">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="5f045-178">Po uruchomieniu przeglądarki z włączonym debugowaniem zdalnym skrót klawiaturowy debugowania otwiera nową kartę debugera. Po chwili na karcie **źródła** zostanie wyświetlona lista zestawów .NET w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5f045-178">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="5f045-179">Rozwiń każdy zestaw i Znajdź *.cs* / pliki źródłowe CS *. Razor* dostępne do debugowania.</span><span class="sxs-lookup"><span data-stu-id="5f045-179">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="5f045-180">Ustaw punkty przerwania, przełącz się z powrotem do karty aplikacji, a punkty przerwania są trafień, gdy kod jest wykonywany.</span><span class="sxs-lookup"><span data-stu-id="5f045-180">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="5f045-181">Po trafieniu punktu przerwania pojedynczy krok (<kbd>F10</kbd>) za pomocą kodu lub wznowienia kodu (<kbd>F8</kbd>) normalnie.</span><span class="sxs-lookup"><span data-stu-id="5f045-181">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="5f045-182">udostępnia serwer proxy debugowania, który implementuje [Protokół Chrome devtools](https://chromedevtools.github.io/devtools-protocol/) i rozszerza protokół z. Informacje specyficzne dla sieci.</span><span class="sxs-lookup"><span data-stu-id="5f045-182"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="5f045-183">Gdy skrót klawiaturowy debugowania zostanie nacionięty, Blazor wskazuje, że program Chrome devtools na serwerze proxy.</span><span class="sxs-lookup"><span data-stu-id="5f045-183">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="5f045-184">Serwer proxy nawiązuje połączenie z oknem przeglądarki, które próbujesz debugować (w związku z tym trzeba włączyć debugowanie zdalne).</span><span class="sxs-lookup"><span data-stu-id="5f045-184">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="5f045-185">Mapy źródeł przeglądarki</span><span class="sxs-lookup"><span data-stu-id="5f045-185">Browser source maps</span></span>

<span data-ttu-id="5f045-186">Mapy źródeł przeglądarki umożliwiają przeglądarce mapowanie skompilowanych plików z powrotem do ich oryginalnych plików źródłowych i są często używane do debugowania po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="5f045-186">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="5f045-187">Jednak Blazor obecnie nie mapuje języka C# bezpośrednio do języka JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="5f045-187">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="5f045-188">Zamiast tego program Blazor wykonuje interpretację Il w przeglądarce, dlatego mapy źródłowe nie są istotne.</span><span class="sxs-lookup"><span data-stu-id="5f045-188">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="5f045-189">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="5f045-189">Troubleshoot</span></span>

<span data-ttu-id="5f045-190">Jeśli występują błędy, następujące porady mogą pomóc:</span><span class="sxs-lookup"><span data-stu-id="5f045-190">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="5f045-191">Na karcie **debuger** Otwórz narzędzia deweloperskie w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="5f045-191">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="5f045-192">W konsoli programu wykonaj polecenie, `localStorage.clear()` Aby usunąć wszystkie punkty przerwania.</span><span class="sxs-lookup"><span data-stu-id="5f045-192">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="5f045-193">Upewnij się, że został zainstalowany i zaufany certyfikat programistyczny protokołu HTTPS ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5f045-193">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="5f045-194">Aby uzyskać więcej informacji, zobacz <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="5f045-194">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
