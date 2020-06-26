---
title: Debuguj ASP.NET CoreBlazor WebAssembly
author: guardrex
description: Dowiedz się, jak debugować Blazor aplikacje.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/25/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 9fe51b8c7eafdd62cc6fc1a820135d9ee5ff010e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401015"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="7d28f-103">Debuguj ASP.NET CoreBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7d28f-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="7d28f-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="7d28f-104">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor WebAssembly<span data-ttu-id="7d28f-105">aplikacje mogą być debugowane przy użyciu narzędzi deweloperskich przeglądarki w przeglądarkach opartych na chromie (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="7d28f-105"> apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="7d28f-106">Alternatywnie możesz debugować aplikację przy użyciu programu Visual Studio lub Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="7d28f-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="7d28f-107">Dostępne scenariusze obejmują:</span><span class="sxs-lookup"><span data-stu-id="7d28f-107">Available scenarios include:</span></span>

* <span data-ttu-id="7d28f-108">Ustawianie i usuwanie punktów przerwania.</span><span class="sxs-lookup"><span data-stu-id="7d28f-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="7d28f-109">Uruchom aplikację z obsługą debugowania w programie Visual Studio i Visual Studio Code (Obsługa<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="7d28f-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="7d28f-110">Jeden krok (<kbd>F10</kbd>) za pomocą kodu.</span><span class="sxs-lookup"><span data-stu-id="7d28f-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="7d28f-111">Wznów wykonywanie kodu za pomocą <kbd>klawisza F8</kbd> w przeglądarce lub <kbd>F5</kbd> w programie Visual Studio lub Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="7d28f-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="7d28f-112">Na ekranie *Ustawienia lokalne* Obserwuj wartości zmiennych lokalnych.</span><span class="sxs-lookup"><span data-stu-id="7d28f-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="7d28f-113">Zobacz stos wywołań, w tym łańcuchy wywołań, które pochodzą z języka JavaScript do platformy .NET i z programu .NET do języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7d28f-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="7d28f-114">Obecnie *nie*można:</span><span class="sxs-lookup"><span data-stu-id="7d28f-114">For now, you *can't*:</span></span>

* <span data-ttu-id="7d28f-115">Przerwij w przypadku nieobsłużonych wyjątków.</span><span class="sxs-lookup"><span data-stu-id="7d28f-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="7d28f-116">Trafij punkty przerwania podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7d28f-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="7d28f-117">Będziemy nadal ulepszać środowisko debugowania w przyszłych wersjach.</span><span class="sxs-lookup"><span data-stu-id="7d28f-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7d28f-118">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="7d28f-118">Prerequisites</span></span>

<span data-ttu-id="7d28f-119">Debugowanie wymaga jednej z następujących przeglądarek:</span><span class="sxs-lookup"><span data-stu-id="7d28f-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="7d28f-120">Microsoft Edge (wersja 80 lub nowsza)</span><span class="sxs-lookup"><span data-stu-id="7d28f-120">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="7d28f-121">Google Chrome (wersja 70 lub nowsza)</span><span class="sxs-lookup"><span data-stu-id="7d28f-121">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="7d28f-122">Włącz debugowanie dla programu Visual Studio i Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7d28f-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="7d28f-123">Aby włączyć debugowanie dla istniejącej Blazor WebAssembly aplikacji, zaktualizuj `launchSettings.json` plik w projekcie startowym w celu uwzględnienia następującej `inspectUri` właściwości w każdym profilu uruchamiania:</span><span class="sxs-lookup"><span data-stu-id="7d28f-123">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="7d28f-124">Po zaktualizowaniu `launchSettings.json` plik powinien wyglądać podobnie do poniższego przykładu:</span><span class="sxs-lookup"><span data-stu-id="7d28f-124">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="7d28f-125">`inspectUri`Właściwość:</span><span class="sxs-lookup"><span data-stu-id="7d28f-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="7d28f-126">Umożliwia środowisku IDE wykrywanie, czy aplikacja jest Blazor WebAssembly aplikacją.</span><span class="sxs-lookup"><span data-stu-id="7d28f-126">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="7d28f-127">Powoduje, że infrastruktura debugowania skryptów umożliwia łączenie się z przeglądarką przez Blazor serwer proxy debugowania.</span><span class="sxs-lookup"><span data-stu-id="7d28f-127">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="7d28f-128">Wartości zastępcze protokołu WebSockets Protocol ( `wsProtocol` ), hosta ( `url.hostname` ), portu ( `url.port` ) i kontrolera URI w uruchomionej przeglądarce ( `browserInspectUri` ) są udostępniane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="7d28f-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="7d28f-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d28f-129">Visual Studio</span></span>

<span data-ttu-id="7d28f-130">Aby debugować Blazor WebAssembly aplikację w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="7d28f-130">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="7d28f-131">Utwórz nową aplikację hostowaną ASP.NET Core Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="7d28f-131">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="7d28f-132">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację w debugerze.</span><span class="sxs-lookup"><span data-stu-id="7d28f-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="7d28f-133">Ustaw punkt przerwania w `Pages/Counter.razor` `IncrementCount` metodzie.</span><span class="sxs-lookup"><span data-stu-id="7d28f-133">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="7d28f-134">Przejdź do **`Counter`** karty i wybierz przycisk, aby trafić w punkt przerwania:</span><span class="sxs-lookup"><span data-stu-id="7d28f-134">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![Licznik debugowania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="7d28f-136">Sprawdź wartość `currentCount` pola w oknie zmiennych lokalnych:</span><span class="sxs-lookup"><span data-stu-id="7d28f-136">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Wyświetl elementy lokalne](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="7d28f-138">Naciśnij klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie.</span><span class="sxs-lookup"><span data-stu-id="7d28f-138">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="7d28f-139">Podczas debugowania Blazor WebAssembly aplikacji można także debugować kod serwera:</span><span class="sxs-lookup"><span data-stu-id="7d28f-139">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="7d28f-140">Ustaw punkt przerwania na `Pages/FetchData.razor` stronie w <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="7d28f-140">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="7d28f-141">Ustaw punkt przerwania w `WeatherForecastController` `Get` metodzie Action.</span><span class="sxs-lookup"><span data-stu-id="7d28f-141">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="7d28f-142">Przejdź do **`Fetch Data`** karty, aby trafić pierwszy punkt przerwania w `FetchData` składniku tuż przed wysłaniem żądania HTTP do serwera:</span><span class="sxs-lookup"><span data-stu-id="7d28f-142">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Debuguj dane pobierania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="7d28f-144">Naciśnij klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie, a następnie naciśnij punkt przerwania na serwerze w `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="7d28f-144">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Serwer debugowania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="7d28f-146">Naciśnij ponownie klawisz <kbd>F5</kbd> , aby umożliwić kontynuowanie wykonywania, a następnie zapoznaj się z renderowaną tabelą prognozy pogody.</span><span class="sxs-lookup"><span data-stu-id="7d28f-146">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="7d28f-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7d28f-147">Visual Studio Code</span></span>

<span data-ttu-id="7d28f-148">Aby debugować Blazor WebAssembly aplikację w Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="7d28f-148">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
<span data-ttu-id="7d28f-149">Zainstaluj [rozszerzenie C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) i rozszerzenie [JavaScript Debugger (nocne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) z `debug.javascript.usePreview` ustawionym na `true` .</span><span class="sxs-lookup"><span data-stu-id="7d28f-149">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

![Rozszerzenia](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![Debuger podglądu JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a><span data-ttu-id="7d28f-152">Debuguj autonomiczneBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7d28f-152">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="7d28f-153">Otwórz aplikację autonomiczną Blazor WebAssembly w vs Code.</span><span class="sxs-lookup"><span data-stu-id="7d28f-153">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="7d28f-154">Jeśli zostanie wyświetlone następujące powiadomienie, że do włączenia debugowania jest wymagana dodatkowa konfiguracja:</span><span class="sxs-lookup"><span data-stu-id="7d28f-154">If you receive the following notification that additional setup is required to enable debugging:</span></span>
   
   * <span data-ttu-id="7d28f-155">Upewnij się, że zainstalowano poprawne rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="7d28f-155">Confirm that you have the correct extensions installed.</span></span>
   * <span data-ttu-id="7d28f-156">Upewnij się, że debugowanie JavaScript Preview jest włączone.</span><span class="sxs-lookup"><span data-stu-id="7d28f-156">Confirm that JavaScript preview debugging is enabled.</span></span>
   * <span data-ttu-id="7d28f-157">Załaduj ponownie okno.</span><span class="sxs-lookup"><span data-stu-id="7d28f-157">Reload the window.</span></span>

   ![Wymagana dodatkowa konfiguracja](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <span data-ttu-id="7d28f-159">Rozpocznij debugowanie przy użyciu skrótu klawiaturowego <kbd>F5</kbd> lub elementu menu.</span><span class="sxs-lookup"><span data-stu-id="7d28f-159">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

1. <span data-ttu-id="7d28f-160">Po wyświetleniu monitu wybierz opcję \*\* Blazor WebAssembly Debuguj\*\* , aby rozpocząć debugowanie.</span><span class="sxs-lookup"><span data-stu-id="7d28f-160">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Lista dostępnych opcji debugowania](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="7d28f-162">Uruchomiona jest aplikacja autonomiczna i zostanie otwarta przeglądarka debugowania.</span><span class="sxs-lookup"><span data-stu-id="7d28f-162">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="7d28f-163">Ustaw punkt przerwania w `IncrementCount` metodzie w `Counter` składniku, a następnie wybierz przycisk, aby trafić w punkt przerwania:</span><span class="sxs-lookup"><span data-stu-id="7d28f-163">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Debuguj licznik w VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="7d28f-165">Debuguj hostowaneBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7d28f-165">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="7d28f-166">Otwórz hostowaną Blazor WebAssembly aplikację w vs Code.</span><span class="sxs-lookup"><span data-stu-id="7d28f-166">Open the hosted Blazor WebAssembly app in VS Code.</span></span>

1. <span data-ttu-id="7d28f-167">Jeśli nie ma ustawionej konfiguracji uruchamiania dla projektu, wyświetlane jest następujące powiadomienie.</span><span class="sxs-lookup"><span data-stu-id="7d28f-167">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="7d28f-168">Wybierz pozycję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="7d28f-168">Select **Yes**.</span></span>

   ![Dodaj wymagane zasoby](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="7d28f-170">W oknie Wybór wybierz projekt *serwera* w ramach hostowanego rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="7d28f-170">In the selection window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="7d28f-171">`launch.json`Generowany jest plik z konfiguracją uruchamiania do uruchamiania debugera.</span><span class="sxs-lookup"><span data-stu-id="7d28f-171">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="7d28f-172">Dołącz do istniejącej sesji debugowania</span><span class="sxs-lookup"><span data-stu-id="7d28f-172">Attach to an existing debugging session</span></span>

<span data-ttu-id="7d28f-173">Aby dołączyć do uruchomionej Blazor aplikacji, Utwórz `launch.json` plik z następującą konfiguracją:</span><span class="sxs-lookup"><span data-stu-id="7d28f-173">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="7d28f-174">Dołączanie do sesji debugowania jest obsługiwane tylko w przypadku aplikacji autonomicznych.</span><span class="sxs-lookup"><span data-stu-id="7d28f-174">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="7d28f-175">Aby można było używać debugowania pełnego stosu, należy uruchomić aplikację z VS Code.</span><span class="sxs-lookup"><span data-stu-id="7d28f-175">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="7d28f-176">Opcje konfiguracji uruchamiania</span><span class="sxs-lookup"><span data-stu-id="7d28f-176">Launch configuration options</span></span>

<span data-ttu-id="7d28f-177">Dla typu debugowania są obsługiwane następujące opcje konfiguracji uruchamiania `blazorwasm` .</span><span class="sxs-lookup"><span data-stu-id="7d28f-177">The following launch configuration options are supported for the `blazorwasm` debug type.</span></span>

| <span data-ttu-id="7d28f-178">Opcja</span><span class="sxs-lookup"><span data-stu-id="7d28f-178">Option</span></span>    | <span data-ttu-id="7d28f-179">Opis</span><span class="sxs-lookup"><span data-stu-id="7d28f-179">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="7d28f-180">Służy `launch` do uruchamiania i dołączania sesji debugowania do Blazor WebAssembly aplikacji lub `attach` do dołączania sesji debugowania do już uruchomionej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7d28f-180">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="7d28f-181">Adres URL, który ma zostać otwarty w przeglądarce podczas debugowania.</span><span class="sxs-lookup"><span data-stu-id="7d28f-181">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="7d28f-182">Wartość domyślna to `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="7d28f-182">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="7d28f-183">Przeglądarka do uruchomienia dla sesji debugowania.</span><span class="sxs-lookup"><span data-stu-id="7d28f-183">The browser to launch for the debugging session.</span></span> <span data-ttu-id="7d28f-184">Ustaw `edge` lub `chrome`.</span><span class="sxs-lookup"><span data-stu-id="7d28f-184">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="7d28f-185">Wartość domyślna to `chrome` .</span><span class="sxs-lookup"><span data-stu-id="7d28f-185">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="7d28f-186">Służy do generowania dzienników z debugera JS.</span><span class="sxs-lookup"><span data-stu-id="7d28f-186">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="7d28f-187">Ustaw, aby `true` generować dzienniki.</span><span class="sxs-lookup"><span data-stu-id="7d28f-187">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="7d28f-188">Musi mieć ustawioną wartość w `true` przypadku uruchamiania i debugowania hostowanej Blazor WebAssembly aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7d28f-188">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="7d28f-189">Określa ścieżkę bezwzględną serwera sieci Web.</span><span class="sxs-lookup"><span data-stu-id="7d28f-189">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="7d28f-190">Należy ustawić, jeśli aplikacja jest obsługiwana z podmarszruty.</span><span class="sxs-lookup"><span data-stu-id="7d28f-190">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="7d28f-191">Liczba milisekund oczekiwania na dołączenie sesji debugowania.</span><span class="sxs-lookup"><span data-stu-id="7d28f-191">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="7d28f-192">Wartość domyślna to 30 000 milisekund (30 sekund).</span><span class="sxs-lookup"><span data-stu-id="7d28f-192">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="7d28f-193">Odwołanie do pliku wykonywalnego, aby uruchomić serwer hostowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7d28f-193">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="7d28f-194">Musi być ustawiona, jeśli `hosted` ma wartość `true` .</span><span class="sxs-lookup"><span data-stu-id="7d28f-194">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="7d28f-195">Katalog roboczy, w którym jest uruchomiona aplikacja.</span><span class="sxs-lookup"><span data-stu-id="7d28f-195">The working directory to launch the app under.</span></span> <span data-ttu-id="7d28f-196">Musi być ustawiona, jeśli `hosted` ma wartość `true` .</span><span class="sxs-lookup"><span data-stu-id="7d28f-196">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="7d28f-197">Zmienne środowiskowe do zapewnienia uruchomionego procesu.</span><span class="sxs-lookup"><span data-stu-id="7d28f-197">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="7d28f-198">Ma zastosowanie tylko wtedy `hosted` , gdy jest ustawiona na `true` .</span><span class="sxs-lookup"><span data-stu-id="7d28f-198">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="7d28f-199">Przykładowe konfiguracje uruchamiania</span><span class="sxs-lookup"><span data-stu-id="7d28f-199">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="7d28f-200">Uruchamianie i debugowanie aplikacji autonomicznej Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7d28f-200">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="7d28f-201">Dołącz do uruchomionej aplikacji pod określonym adresem URL</span><span class="sxs-lookup"><span data-stu-id="7d28f-201">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="7d28f-202">Uruchamianie i debugowanie hostowanej Blazor WebAssembly aplikacji</span><span class="sxs-lookup"><span data-stu-id="7d28f-202">Launch and debug a hosted Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a><span data-ttu-id="7d28f-203">Debuguj w przeglądarce</span><span class="sxs-lookup"><span data-stu-id="7d28f-203">Debug in the browser</span></span>

1. <span data-ttu-id="7d28f-204">Uruchom kompilację debugowania aplikacji w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="7d28f-204">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="7d28f-205">Naciśnij klawisze <kbd>SHIFT</kbd> + <kbd>Alt</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="7d28f-205">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="7d28f-206">Przeglądarka musi być uruchomiona z włączonym debugowaniem zdalnym.</span><span class="sxs-lookup"><span data-stu-id="7d28f-206">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="7d28f-207">Jeśli debugowanie zdalne jest wyłączone, **nie można odnaleźć strony błędu karty przeglądarki możliwością debugowania** .</span><span class="sxs-lookup"><span data-stu-id="7d28f-207">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="7d28f-208">Strona błędu zawiera instrukcje dotyczące uruchamiania przeglądarki z otwartym portem debugowania, dzięki czemu Blazor serwer proxy debugowania może połączyć się z aplikacją.</span><span class="sxs-lookup"><span data-stu-id="7d28f-208">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="7d28f-209">*Zamknij wszystkie wystąpienia przeglądarki* i uruchom ponownie przeglądarkę zgodnie z instrukcjami.</span><span class="sxs-lookup"><span data-stu-id="7d28f-209">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="7d28f-210">Po uruchomieniu przeglądarki z włączonym debugowaniem zdalnym skrót klawiaturowy debugowania otwiera nową kartę debugera. Po chwili na karcie **źródła** zostanie wyświetlona lista zestawów .NET w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7d28f-210">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="7d28f-211">Rozwiń każdy zestaw i Znajdź `.cs` / `.razor` pliki źródłowe dostępne do debugowania.</span><span class="sxs-lookup"><span data-stu-id="7d28f-211">Expand each assembly and find the `.cs`/`.razor` source files available for debugging.</span></span> <span data-ttu-id="7d28f-212">Ustaw punkty przerwania, przełącz się z powrotem do karty aplikacji, a punkty przerwania są trafień, gdy kod jest wykonywany.</span><span class="sxs-lookup"><span data-stu-id="7d28f-212">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="7d28f-213">Po trafieniu punktu przerwania pojedynczy krok (<kbd>F10</kbd>) za pomocą kodu lub wznowienia kodu (<kbd>F8</kbd>) normalnie.</span><span class="sxs-lookup"><span data-stu-id="7d28f-213">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="7d28f-214">udostępnia serwer proxy debugowania, który implementuje [Protokół Chrome devtools](https://chromedevtools.github.io/devtools-protocol/) i rozszerza protokół z. Informacje specyficzne dla sieci.</span><span class="sxs-lookup"><span data-stu-id="7d28f-214"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="7d28f-215">Gdy skrót klawiaturowy debugowania zostanie nacionięty, Blazor wskazuje, że program Chrome devtools na serwerze proxy.</span><span class="sxs-lookup"><span data-stu-id="7d28f-215">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="7d28f-216">Serwer proxy nawiązuje połączenie z oknem przeglądarki, które próbujesz debugować (w związku z tym trzeba włączyć debugowanie zdalne).</span><span class="sxs-lookup"><span data-stu-id="7d28f-216">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="7d28f-217">Mapy źródeł przeglądarki</span><span class="sxs-lookup"><span data-stu-id="7d28f-217">Browser source maps</span></span>

<span data-ttu-id="7d28f-218">Mapy źródeł przeglądarki umożliwiają przeglądarce mapowanie skompilowanych plików z powrotem do ich oryginalnych plików źródłowych i są często używane do debugowania po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="7d28f-218">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="7d28f-219">Jednak Blazor obecnie nie mapuje języka C# bezpośrednio do języka JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="7d28f-219">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="7d28f-220">Zamiast tego program Blazor wykonuje interpretację Il w przeglądarce, dlatego mapy źródłowe nie są istotne.</span><span class="sxs-lookup"><span data-stu-id="7d28f-220">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="7d28f-221">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="7d28f-221">Troubleshoot</span></span>

<span data-ttu-id="7d28f-222">Jeśli występują błędy, następujące porady mogą pomóc:</span><span class="sxs-lookup"><span data-stu-id="7d28f-222">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="7d28f-223">Na karcie **debuger** Otwórz narzędzia deweloperskie w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="7d28f-223">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="7d28f-224">W konsoli programu wykonaj polecenie, `localStorage.clear()` Aby usunąć wszystkie punkty przerwania.</span><span class="sxs-lookup"><span data-stu-id="7d28f-224">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="7d28f-225">Upewnij się, że został zainstalowany i zaufany certyfikat programistyczny protokołu HTTPS ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7d28f-225">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="7d28f-226">Aby uzyskać więcej informacji, zobacz <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="7d28f-226">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="7d28f-227">Program Visual Studio wymaga opcji **Enable JavaScript Debug for ASP.NET (Chrome, Edge i IE)** w **Tools**  >  **opcji**narzędzia  >  **debugowanie**  >  **Ogólne**.</span><span class="sxs-lookup"><span data-stu-id="7d28f-227">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="7d28f-228">Jest to ustawienie domyślne dla programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7d28f-228">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="7d28f-229">Jeśli debugowanie nie działa, upewnij się, że opcja jest zaznaczona.</span><span class="sxs-lookup"><span data-stu-id="7d28f-229">If debugging isn't working, confirm that the option is selected.</span></span>
