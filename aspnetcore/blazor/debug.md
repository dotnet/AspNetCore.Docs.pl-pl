---
title: Debugowanie ASP.NET Core Blazor WebAssembly
author: guardrex
description: Dowiedz się, Blazor jak debugować aplikacje.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 8b63444ba5c8cd45e64e722c8978ba4e6d90af36
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488751"
---
# <a name="debug-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="b5cd3-103">Debugowanie ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b5cd3-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="b5cd3-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="b5cd3-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="b5cd3-105">Aplikacje WebAssembly można debugować za pomocą narzędzi do tworzenia przeglądarki w przeglądarkach opartych na Chromium (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="b5cd3-105"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="b5cd3-106">Alternatywnie można debugować aplikację za pomocą programu Visual Studio lub Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-106">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="b5cd3-107">Dostępne scenariusze obejmują:</span><span class="sxs-lookup"><span data-stu-id="b5cd3-107">Available scenarios include:</span></span>

* <span data-ttu-id="b5cd3-108">Ustawianie i usuwanie punktów przerwania.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="b5cd3-109">Uruchom aplikację z obsługą debugowania w programie Visual Studio i visual studio code (obsługa<kbd>F5).</kbd></span><span class="sxs-lookup"><span data-stu-id="b5cd3-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="b5cd3-110">Jednoetapowy (<kbd>F10</kbd>) za pośrednictwem kodu.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="b5cd3-111">Wznów wykonanie kodu za pomocą <kbd>F8</kbd> w przeglądarce lub <kbd>F5</kbd> w programie Visual Studio lub visual studio code.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="b5cd3-112">Na wyświetlaczu *Locals* należy obserwować wartości zmiennych lokalnych.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="b5cd3-113">Zobacz stos wywołań, w tym łańcuchy wywołań, które przechodzą z języka JavaScript do platformy .NET i z platformy .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="b5cd3-114">Na razie *nie można:*</span><span class="sxs-lookup"><span data-stu-id="b5cd3-114">For now, you *can't*:</span></span>

* <span data-ttu-id="b5cd3-115">Sprawdź tablice.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-115">Inspect arrays.</span></span>
* <span data-ttu-id="b5cd3-116">Najedź kursorem, aby sprawdzić członków.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-116">Hover to inspect members.</span></span>
* <span data-ttu-id="b5cd3-117">Krok debugowania do lub z kodu zarządzanego.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-117">Step debug into or out of managed code.</span></span>
* <span data-ttu-id="b5cd3-118">Mieć pełną obsługę sprawdzania typów wartości.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-118">Have full support for inspecting value types.</span></span>
* <span data-ttu-id="b5cd3-119">Przerwij na nieobsługiwał wyjątki.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-119">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="b5cd3-120">Trafić punkty przerwania podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-120">Hit breakpoints during app startup.</span></span>
* <span data-ttu-id="b5cd3-121">Debugowanie aplikacji za pomocą procesu roboczego usługi.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-121">Debug an app with a service worker.</span></span>

<span data-ttu-id="b5cd3-122">Firma We will continue to improve the debugging experience in upcoming releases.We will continue to improve the debugging experience in upcoming releases.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b5cd3-123">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="b5cd3-123">Prerequisites</span></span>

<span data-ttu-id="b5cd3-124">Debugowanie wymaga jednej z następujących przeglądarek:</span><span class="sxs-lookup"><span data-stu-id="b5cd3-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="b5cd3-125">Microsoft Edge (wersja 80 lub nowsza)</span><span class="sxs-lookup"><span data-stu-id="b5cd3-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="b5cd3-126">Google Chrome (wersja 70 lub nowsza)</span><span class="sxs-lookup"><span data-stu-id="b5cd3-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="b5cd3-127">Włączanie debugowania dla programu Visual Studio i kodu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b5cd3-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="b5cd3-128">Debugowanie jest włączane automatycznie dla nowych projektów, które są tworzone przy użyciu ASP.NET Core Blazor 3.2 Preview 3 lub nowszym szablonie projektu WebAssembly[(bieżąca wersja to 3.2 Preview 4](xref:blazor/get-started)).</span><span class="sxs-lookup"><span data-stu-id="b5cd3-128">Debugging is enabled automatically for new projects that are created using the ASP.NET Core 3.2 Preview 3 or later Blazor WebAssembly project template ([current release is 3.2 Preview 4](xref:blazor/get-started)).</span></span>

<span data-ttu-id="b5cd3-129">Aby włączyć debugowanie Blazor dla istniejącej aplikacji WebAssembly, zaktualizuj plik *launchSettings.json* w projekcie startowym, aby uwzględnić następującą `inspectUri` właściwość w każdym profilu uruchamiania:</span><span class="sxs-lookup"><span data-stu-id="b5cd3-129">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="b5cd3-130">Po zaktualizowaniu plik *launchSettings.json* powinien wyglądać podobnie do następującego przykładu:</span><span class="sxs-lookup"><span data-stu-id="b5cd3-130">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="b5cd3-131">Obiekt: `inspectUri`</span><span class="sxs-lookup"><span data-stu-id="b5cd3-131">The `inspectUri` property:</span></span>

* <span data-ttu-id="b5cd3-132">Umożliwia IDE wykryć, że Blazor aplikacja jest webassembly aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-132">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="b5cd3-133">Nakazuje infrastruktury debugowania skryptu, aby Blazorpołączyć się z przeglądarką za pośrednictwem debugowania serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-133">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="b5cd3-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b5cd3-134">Visual Studio</span></span>

<span data-ttu-id="b5cd3-135">Aby debugować aplikację Blazor WebAssembly w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="b5cd3-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="b5cd3-136">Upewnij się, że [zainstalowano najnowszą wersję zapoznawczą programu Visual Studio 2019 16.6](https://visualstudio.com/preview) (wersja zapoznawcza 2 lub nowsza).</span><span class="sxs-lookup"><span data-stu-id="b5cd3-136">Ensure you have [installed the latest preview release of Visual Studio 2019 16.6](https://visualstudio.com/preview) (Preview 2 or later).</span></span>
1. <span data-ttu-id="b5cd3-137">Utwórz nową ASP.NET Core hostowane Blazor WebAssembly aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-137">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="b5cd3-138">Naciśnij <kbd>klawisz F5,</kbd> aby uruchomić aplikację w debugerze.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-138">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="b5cd3-139">Ustaw punkt przerwania w *Counter.brzytwa* w metodzie. `IncrementCount`</span><span class="sxs-lookup"><span data-stu-id="b5cd3-139">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="b5cd3-140">Przejdź do karty **Licznik** i wybierz przycisk, aby trafić w punkt przerwania:</span><span class="sxs-lookup"><span data-stu-id="b5cd3-140">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Licznik debugowania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="b5cd3-142">Sprawdź wartość pola `currentCount` w oknie dla mieszkańców:</span><span class="sxs-lookup"><span data-stu-id="b5cd3-142">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Zobacz lokalnych mieszkańców](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="b5cd3-144">Naciśnij <kbd>klawisz F5,</kbd> aby kontynuować wykonywanie.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-144">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="b5cd3-145">Podczas debugowania Blazor aplikacji WebAssembly można również debugować kod serwera:</span><span class="sxs-lookup"><span data-stu-id="b5cd3-145">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="b5cd3-146">Ustaw punkt przerwania na stronie *FetchData.brzytwa* w `OnInitializedAsync`programie .</span><span class="sxs-lookup"><span data-stu-id="b5cd3-146">Set a breakpoint in the *FetchData.razor* page in `OnInitializedAsync`.</span></span>
1. <span data-ttu-id="b5cd3-147">Ustaw punkt przerwania `WeatherForecastController` w `Get` metodzie akcji.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-147">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="b5cd3-148">Przejdź do karty **Pobierz dane,** aby trafić `FetchData` pierwszy punkt przerwania w składniku tuż przed wysuniem żądania HTTP do serwera:</span><span class="sxs-lookup"><span data-stu-id="b5cd3-148">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Debugowanie danych pobierania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="b5cd3-150">Naciśnij <kbd>klawisz F5,</kbd> aby kontynuować wykonywanie, a `WeatherForecastController`następnie naciśnij punkt przerwania na serwerze w :</span><span class="sxs-lookup"><span data-stu-id="b5cd3-150">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Serwer debugowania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="b5cd3-152">Naciśnij klawisz <kbd>F5</kbd> ponownie, aby umożliwić kontynuowanie wykonywania i wyświetlić tabelę prognozy pogody renderowane.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

## <a name="visual-studio-code"></a><span data-ttu-id="b5cd3-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b5cd3-153">Visual Studio Code</span></span>

<span data-ttu-id="b5cd3-154">Aby debugować aplikację Blazor WebAssembly w programie Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="b5cd3-154">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="b5cd3-155">Zainstaluj [rozszerzenie C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) i rozszerzenie [debugera JavaScript (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) z `debug.javascript.usePreview` ustawionym na `true`.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-155">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Rozszerzenia](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Debuger podglądu JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="b5cd3-158">Otwórz istniejącą Blazor aplikację WebAssembly z włączonym debugowaniem.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-158">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="b5cd3-159">Jeśli zostanie wyświetlone następujące powiadomienie, że do włączenia debugowania jest wymagana dodatkowa konfiguracja, upewnij się, że masz zainstalowane poprawne rozszerzenia i włączone debugowanie w wersji zapoznawczej JavaScript, a następnie ponownie załaduj okno:</span><span class="sxs-lookup"><span data-stu-id="b5cd3-159">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Dodatkowa konfiguracja requried](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="b5cd3-161">Powiadomienie oferuje dodać wymagane zasoby do aplikacji do tworzenia i debugowania.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-161">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="b5cd3-162">Wybierz **tak**:</span><span class="sxs-lookup"><span data-stu-id="b5cd3-162">Select **Yes**:</span></span>

     ![Dodawanie wymaganych zasobów](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="b5cd3-164">Uruchamianie aplikacji w debugerze jest procesem dwuetapowym:</span><span class="sxs-lookup"><span data-stu-id="b5cd3-164">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="b5cd3-165">1\.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-165">1\.</span></span> <span data-ttu-id="b5cd3-166">**Najpierw**uruchom aplikację przy użyciu konfiguracji \*\*uruchamiania .NET Core Launch (Standalone).Blazor \*\*</span><span class="sxs-lookup"><span data-stu-id="b5cd3-166">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="b5cd3-167">2\.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-167">2\.</span></span> <span data-ttu-id="b5cd3-168">**Po uruchomieniu aplikacji**uruchom przeglądarkę za pomocą **zestawu Blazor .NET Core Debug Web Assembly w** konfiguracji uruchamiania Chrome (wymaga Chrome).</span><span class="sxs-lookup"><span data-stu-id="b5cd3-168">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="b5cd3-169">Aby użyć edge'a zamiast `type` Chrome, zmień konfigurację uruchamiania w pliku `pwa-chrome` `pwa-msedge` *.vscode/launch.json* z na .</span><span class="sxs-lookup"><span data-stu-id="b5cd3-169">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="b5cd3-170">Ustaw punkt przerwania `IncrementCount` w `Counter` metodzie w komponencie, a następnie wybierz przycisk, aby trafić punkt przerwania:</span><span class="sxs-lookup"><span data-stu-id="b5cd3-170">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Licznik debugowania w kodzie VS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="b5cd3-172">Debugowanie w przeglądarce</span><span class="sxs-lookup"><span data-stu-id="b5cd3-172">Debug in the browser</span></span>

1. <span data-ttu-id="b5cd3-173">Uruchom kompilację debugowania aplikacji w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-173">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="b5cd3-174">Naciśnij <kbd>klawisz Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-174">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="b5cd3-175">Przeglądarka musi być uruchamiana z włączonym debugowaniem zdalnym.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-175">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="b5cd3-176">Jeśli debugowanie zdalne jest wyłączone, zostanie wygenerowana strona błędu **karty przeglądarki nie można znaleźć debugowania.**</span><span class="sxs-lookup"><span data-stu-id="b5cd3-176">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="b5cd3-177">Strona błędu zawiera instrukcje dotyczące uruchamiania przeglądarki z otwartym Blazor portem debugowania, aby serwer proxy debugowania mógł połączyć się z aplikacją.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-177">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="b5cd3-178">*Zamknij wszystkie wystąpienia przeglądarki* i uruchom ponownie przeglądarkę zgodnie z instrukcjami.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-178">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="b5cd3-179">Gdy przeglądarka jest uruchomiona z włączonym debugowaniem zdalnym, skrót klawiaturowy debugowania otwiera nową kartę debugera. Po chwili na karcie **Źródła** jest wyświetlana lista zestawów .NET w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-179">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="b5cd3-180">Rozwiń każdy zestaw i znajdź pliki źródłowe *.cs*/*.razor* dostępne do debugowania.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-180">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="b5cd3-181">Ustaw punkty przerwania, przełącz się z powrotem do karty aplikacji, a punkty przerwania zostaną trafione podczas wykonywania kodu.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-181">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="b5cd3-182">Po trafieniu punktu przerwania, jednoetapowe<kbd>(F10)</kbd>za pośrednictwem kodu lub wznowić<kbd>(F8</kbd>) wykonanie kodu normalnie.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-182">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="b5cd3-183">zapewnia debugowanie serwera proxy, który implementuje [protokół Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) i rozszerza protokół o . informacje specyficzne dla sieci.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-183"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="b5cd3-184">Po naciśnięciu skrótu klawiaturowego debugowania Blazor wskazuje mapę Chrome DevTools na serwerze proxy.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-184">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="b5cd3-185">Serwer proxy łączy się z oknem przeglądarki, które chcesz debugować (stąd konieczność włączenia zdalnego debugowania).</span><span class="sxs-lookup"><span data-stu-id="b5cd3-185">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="b5cd3-186">Mapy źródeł przeglądarki</span><span class="sxs-lookup"><span data-stu-id="b5cd3-186">Browser source maps</span></span>

<span data-ttu-id="b5cd3-187">Mapy źródłowe przeglądarki umożliwiają przeglądarce mapowanie skompilowanych plików z powrotem do ich oryginalnych plików źródłowych i są powszechnie używane do debugowania po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-187">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="b5cd3-188">Jednak Blazor obecnie nie mapuje języka C# bezpośrednio do języka JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-188">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="b5cd3-189">Zamiast Blazor tego, czy interpretacja IL w przeglądarce, więc mapy źródłowe nie są istotne.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-189">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="b5cd3-190">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="b5cd3-190">Troubleshoot</span></span>

<span data-ttu-id="b5cd3-191">Jeśli nadajesz się na błędy, może pomóc następująca wskazówka:</span><span class="sxs-lookup"><span data-stu-id="b5cd3-191">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="b5cd3-192">Na karcie **Debuger** otwórz narzędzia programistyczne w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-192">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="b5cd3-193">W konsoli wykonaj, `localStorage.clear()` aby usunąć wszystkie punkty przerwania.</span><span class="sxs-lookup"><span data-stu-id="b5cd3-193">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
