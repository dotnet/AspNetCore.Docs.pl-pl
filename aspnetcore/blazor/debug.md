---
<span data-ttu-id="cc9d6-101">title: "Debug ASP.NET Core Blazor webassembly" Author: Description: "informacje o sposobie debugowania Blazor aplikacji".</span><span class="sxs-lookup"><span data-stu-id="cc9d6-101">title: 'Debug ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to debug Blazor apps.'</span></span>
<span data-ttu-id="cc9d6-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="cc9d6-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cc9d6-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cc9d6-103">'Blazor'</span></span>
- <span data-ttu-id="cc9d6-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cc9d6-104">'Identity'</span></span>
- <span data-ttu-id="cc9d6-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cc9d6-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="cc9d6-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cc9d6-106">'Razor'</span></span>
- <span data-ttu-id="cc9d6-107">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="cc9d6-107">'SignalR' uid:</span></span> 

---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="cc9d6-108">Debuguj ASP.NET Core Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="cc9d6-108">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="cc9d6-109">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="cc9d6-109">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor<span data-ttu-id="cc9d6-110">Aplikacje webassembly można debugować przy użyciu narzędzi deweloperskich przeglądarki w przeglądarkach opartych na chromie (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="cc9d6-110"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="cc9d6-111">Alternatywnie możesz debugować aplikację przy użyciu programu Visual Studio lub Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-111">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="cc9d6-112">Dostępne scenariusze obejmują:</span><span class="sxs-lookup"><span data-stu-id="cc9d6-112">Available scenarios include:</span></span>

* <span data-ttu-id="cc9d6-113">Ustawianie i usuwanie punktów przerwania.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-113">Set and remove breakpoints.</span></span>
* <span data-ttu-id="cc9d6-114">Uruchom aplikację z obsługą debugowania w programie Visual Studio i Visual Studio Code (Obsługa<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="cc9d6-114">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="cc9d6-115">Jeden krok (<kbd>F10</kbd>) za pomocą kodu.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-115">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="cc9d6-116">Wznów wykonywanie kodu za pomocą <kbd>klawisza F8</kbd> w przeglądarce lub <kbd>F5</kbd> w programie Visual Studio lub Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-116">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="cc9d6-117">Na ekranie *Ustawienia lokalne* Obserwuj wartości zmiennych lokalnych.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-117">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="cc9d6-118">Zobacz stos wywołań, w tym łańcuchy wywołań, które pochodzą z języka JavaScript do platformy .NET i z programu .NET do języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-118">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="cc9d6-119">Obecnie *nie*można:</span><span class="sxs-lookup"><span data-stu-id="cc9d6-119">For now, you *can't*:</span></span>

* <span data-ttu-id="cc9d6-120">Przerwij w przypadku nieobsłużonych wyjątków.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-120">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="cc9d6-121">Trafij punkty przerwania podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-121">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="cc9d6-122">Będziemy nadal ulepszać środowisko debugowania w przyszłych wersjach.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cc9d6-123">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="cc9d6-123">Prerequisites</span></span>

<span data-ttu-id="cc9d6-124">Debugowanie wymaga jednej z następujących przeglądarek:</span><span class="sxs-lookup"><span data-stu-id="cc9d6-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="cc9d6-125">Microsoft Edge (wersja 80 lub nowsza)</span><span class="sxs-lookup"><span data-stu-id="cc9d6-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="cc9d6-126">Google Chrome (wersja 70 lub nowsza)</span><span class="sxs-lookup"><span data-stu-id="cc9d6-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="cc9d6-127">Włącz debugowanie dla programu Visual Studio i Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cc9d6-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="cc9d6-128">Aby włączyć debugowanie istniejącej Blazor aplikacji webassembly, zaktualizuj plik *profilu launchsettings. JSON* w projekcie startowym w celu uwzględnienia następującej `inspectUri` właściwości w każdym profilu uruchamiania:</span><span class="sxs-lookup"><span data-stu-id="cc9d6-128">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="cc9d6-129">Po zaktualizowaniu plik *profilu launchsettings. JSON* powinien wyglądać podobnie do poniższego przykładu:</span><span class="sxs-lookup"><span data-stu-id="cc9d6-129">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="cc9d6-130">`inspectUri`Właściwość:</span><span class="sxs-lookup"><span data-stu-id="cc9d6-130">The `inspectUri` property:</span></span>

* <span data-ttu-id="cc9d6-131">Umożliwia środowisku IDE wykrywanie, czy aplikacja jest Blazor aplikacją webassembly.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-131">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="cc9d6-132">Powoduje, że infrastruktura debugowania skryptów umożliwia łączenie się z przeglądarką przez Blazor serwer proxy debugowania.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-132">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="cc9d6-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc9d6-133">Visual Studio</span></span>

<span data-ttu-id="cc9d6-134">Aby debugować Blazor aplikację webassembly w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="cc9d6-134">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="cc9d6-135">Utwórz nową ASP.NET Core hostowanej Blazor aplikacji sieci webassembly.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-135">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="cc9d6-136">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację w debugerze.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-136">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="cc9d6-137">Ustaw punkt przerwania w elemencie *Counter. Razor* w `IncrementCount` metodzie.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-137">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="cc9d6-138">Przejdź do karty **licznik** i wybierz przycisk, aby trafić w punkt przerwania:</span><span class="sxs-lookup"><span data-stu-id="cc9d6-138">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Licznik debugowania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="cc9d6-140">Sprawdź wartość `currentCount` pola w oknie zmiennych lokalnych:</span><span class="sxs-lookup"><span data-stu-id="cc9d6-140">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Wyświetl elementy lokalne](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="cc9d6-142">Naciśnij klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-142">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="cc9d6-143">Podczas debugowania Blazor aplikacji webassembly można także debugować kod serwera:</span><span class="sxs-lookup"><span data-stu-id="cc9d6-143">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="cc9d6-144">Ustaw punkt przerwania na stronie *FetchData. Razor* w temacie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="cc9d6-144">Set a breakpoint in the *FetchData.razor* page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="cc9d6-145">Ustaw punkt przerwania w `WeatherForecastController` `Get` metodzie Action.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-145">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="cc9d6-146">Przejdź do karty **pobieranie danych** , aby trafić pierwszy punkt przerwania w `FetchData` składniku tuż przed wysłaniem żądania HTTP do serwera:</span><span class="sxs-lookup"><span data-stu-id="cc9d6-146">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Debuguj dane pobierania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="cc9d6-148">Naciśnij klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie, a następnie naciśnij punkt przerwania na serwerze w `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="cc9d6-148">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Serwer debugowania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="cc9d6-150">Naciśnij ponownie klawisz <kbd>F5</kbd> , aby umożliwić kontynuowanie wykonywania, a następnie zapoznaj się z renderowaną tabelą prognozy pogody.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-150">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="cc9d6-151">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cc9d6-151">Visual Studio Code</span></span>

<span data-ttu-id="cc9d6-152">Aby debugować Blazor aplikację webassembly w Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="cc9d6-152">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="cc9d6-153">Zainstaluj [rozszerzenie C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) i rozszerzenie [JavaScript Debugger (nocne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) z `debug.javascript.usePreview` ustawionym na `true` .</span><span class="sxs-lookup"><span data-stu-id="cc9d6-153">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Rozszerzenia](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Debuger podglądu JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="cc9d6-156">Otwórz istniejącą Blazor aplikację webassembly z włączonym debugowaniem.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-156">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="cc9d6-157">Jeśli otrzymujesz następujące powiadomienie, że do włączenia debugowania jest wymagana dodatkowa konfiguracja, upewnij się, że są zainstalowane odpowiednie rozszerzenia i włączono debugowanie JavaScript w wersji zapoznawczej, a następnie załaduj ponownie okno:</span><span class="sxs-lookup"><span data-stu-id="cc9d6-157">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Wymagana dodatkowa konfiguracja](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="cc9d6-159">Powiadomienia umożliwiają dodanie wymaganych zasobów do aplikacji na potrzeby kompilowania i debugowania.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-159">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="cc9d6-160">Wybierz opcję **tak**:</span><span class="sxs-lookup"><span data-stu-id="cc9d6-160">Select **Yes**:</span></span>

     ![Dodaj wymagane zasoby](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="cc9d6-162">Uruchamianie aplikacji w debugerze jest procesem dwuetapowym:</span><span class="sxs-lookup"><span data-stu-id="cc9d6-162">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="cc9d6-163">1 \.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-163">1\.</span></span> <span data-ttu-id="cc9d6-164">**Najpierw**Uruchom aplikację przy użyciu konfiguracji uruchamiania **uruchamiania programu .NET Core ( Blazor Standalone)** .</span><span class="sxs-lookup"><span data-stu-id="cc9d6-164">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="cc9d6-165">2 \.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-165">2\.</span></span> <span data-ttu-id="cc9d6-166">**Po uruchomieniu aplikacji**Uruchom przeglądarkę przy użyciu **zestawu .NET Core Debug Blazor Web Assembly w** konfiguracji uruchamiania programu Chrome (wymaga programu Chrome).</span><span class="sxs-lookup"><span data-stu-id="cc9d6-166">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="cc9d6-167">Aby użyć krawędzi zamiast przeglądarki Chrome, Zmień `type` konfigurację uruchamiania w pliku *. programu vscode/Launch. JSON* z `pwa-chrome` na `pwa-msedge` .</span><span class="sxs-lookup"><span data-stu-id="cc9d6-167">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="cc9d6-168">Ustaw punkt przerwania w `IncrementCount` metodzie w `Counter` składniku, a następnie wybierz przycisk, aby trafić w punkt przerwania:</span><span class="sxs-lookup"><span data-stu-id="cc9d6-168">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Debuguj licznik w VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="cc9d6-170">Debuguj w przeglądarce</span><span class="sxs-lookup"><span data-stu-id="cc9d6-170">Debug in the browser</span></span>

1. <span data-ttu-id="cc9d6-171">Uruchom kompilację debugowania aplikacji w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-171">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="cc9d6-172">Naciśnij klawisze <kbd>SHIFT</kbd> + <kbd>Alt</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-172">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="cc9d6-173">Przeglądarka musi być uruchomiona z włączonym debugowaniem zdalnym.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-173">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="cc9d6-174">Jeśli debugowanie zdalne jest wyłączone, **nie można odnaleźć strony błędu karty przeglądarki możliwością debugowania** .</span><span class="sxs-lookup"><span data-stu-id="cc9d6-174">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="cc9d6-175">Strona błędu zawiera instrukcje dotyczące uruchamiania przeglądarki z otwartym portem debugowania, dzięki czemu Blazor serwer proxy debugowania może połączyć się z aplikacją.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-175">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="cc9d6-176">*Zamknij wszystkie wystąpienia przeglądarki* i uruchom ponownie przeglądarkę zgodnie z instrukcjami.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-176">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="cc9d6-177">Po uruchomieniu przeglądarki z włączonym debugowaniem zdalnym skrót klawiaturowy debugowania otwiera nową kartę debugera. Po chwili na karcie **źródła** zostanie wyświetlona lista zestawów .NET w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-177">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="cc9d6-178">Rozwiń każdy zestaw i Znajdź *.cs* / pliki źródłowe CS *. Razor* dostępne do debugowania.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-178">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="cc9d6-179">Ustaw punkty przerwania, przełącz się z powrotem do karty aplikacji, a punkty przerwania są trafień, gdy kod jest wykonywany.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-179">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="cc9d6-180">Po trafieniu punktu przerwania pojedynczy krok (<kbd>F10</kbd>) za pomocą kodu lub wznowienia kodu (<kbd>F8</kbd>) normalnie.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-180">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="cc9d6-181">udostępnia serwer proxy debugowania, który implementuje [Protokół Chrome devtools](https://chromedevtools.github.io/devtools-protocol/) i rozszerza protokół z. Informacje specyficzne dla sieci.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-181"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="cc9d6-182">Gdy skrót klawiaturowy debugowania zostanie nacionięty, Blazor wskazuje, że program Chrome devtools na serwerze proxy.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-182">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="cc9d6-183">Serwer proxy nawiązuje połączenie z oknem przeglądarki, które próbujesz debugować (w związku z tym trzeba włączyć debugowanie zdalne).</span><span class="sxs-lookup"><span data-stu-id="cc9d6-183">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="cc9d6-184">Mapy źródeł przeglądarki</span><span class="sxs-lookup"><span data-stu-id="cc9d6-184">Browser source maps</span></span>

<span data-ttu-id="cc9d6-185">Mapy źródeł przeglądarki umożliwiają przeglądarce mapowanie skompilowanych plików z powrotem do ich oryginalnych plików źródłowych i są często używane do debugowania po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-185">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="cc9d6-186">Jednak Blazor obecnie nie mapuje języka C# bezpośrednio do języka JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-186">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="cc9d6-187">Zamiast tego program Blazor wykonuje interpretację Il w przeglądarce, dlatego mapy źródłowe nie są istotne.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-187">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="cc9d6-188">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="cc9d6-188">Troubleshoot</span></span>

<span data-ttu-id="cc9d6-189">Jeśli występują błędy, Poniższa Wskazówka może pomóc:</span><span class="sxs-lookup"><span data-stu-id="cc9d6-189">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="cc9d6-190">Na karcie **debuger** Otwórz narzędzia deweloperskie w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-190">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="cc9d6-191">W konsoli programu wykonaj polecenie, `localStorage.clear()` Aby usunąć wszystkie punkty przerwania.</span><span class="sxs-lookup"><span data-stu-id="cc9d6-191">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
