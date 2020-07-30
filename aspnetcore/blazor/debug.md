---
title: Debuguj ASP.NET CoreBlazor WebAssembly
author: guardrex
description: Dowiedz się, jak debugować Blazor aplikacje.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: b4199c3a99af5875c5d9a87f29f7c7e2758ffd71
ms.sourcegitcommit: 5a36758cca2861aeb10840093e46d273a6e6e91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87303563"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="63cb8-103">Debuguj ASP.NET CoreBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="63cb8-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="63cb8-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="63cb8-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="63cb8-105">Blazor WebAssemblyaplikacje mogą być debugowane przy użyciu narzędzi deweloperskich przeglądarki w przeglądarkach opartych na chromie (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="63cb8-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="63cb8-106">Alternatywnie możesz debugować aplikację przy użyciu programu Visual Studio lub Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="63cb8-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="63cb8-107">Dostępne scenariusze obejmują:</span><span class="sxs-lookup"><span data-stu-id="63cb8-107">Available scenarios include:</span></span>

* <span data-ttu-id="63cb8-108">Ustawianie i usuwanie punktów przerwania.</span><span class="sxs-lookup"><span data-stu-id="63cb8-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="63cb8-109">Uruchom aplikację z obsługą debugowania w programie Visual Studio i Visual Studio Code (Obsługa<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="63cb8-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="63cb8-110">Jeden krok (<kbd>F10</kbd>) za pomocą kodu.</span><span class="sxs-lookup"><span data-stu-id="63cb8-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="63cb8-111">Wznów wykonywanie kodu za pomocą <kbd>klawisza F8</kbd> w przeglądarce lub <kbd>F5</kbd> w programie Visual Studio lub Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="63cb8-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="63cb8-112">Na ekranie *Ustawienia lokalne* Obserwuj wartości zmiennych lokalnych.</span><span class="sxs-lookup"><span data-stu-id="63cb8-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="63cb8-113">Zobacz stos wywołań, w tym łańcuchy wywołań, które pochodzą z języka JavaScript do platformy .NET i z programu .NET do języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="63cb8-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="63cb8-114">Obecnie *nie*można:</span><span class="sxs-lookup"><span data-stu-id="63cb8-114">For now, you *can't*:</span></span>

* <span data-ttu-id="63cb8-115">Przerwij w przypadku nieobsłużonych wyjątków.</span><span class="sxs-lookup"><span data-stu-id="63cb8-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="63cb8-116">Trafij punkty przerwania podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="63cb8-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="63cb8-117">Będziemy nadal ulepszać środowisko debugowania w przyszłych wersjach.</span><span class="sxs-lookup"><span data-stu-id="63cb8-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="63cb8-118">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="63cb8-118">Prerequisites</span></span>

<span data-ttu-id="63cb8-119">Debugowanie wymaga jednej z następujących przeglądarek:</span><span class="sxs-lookup"><span data-stu-id="63cb8-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="63cb8-120">Google Chrome (wersja 70 lub nowsza) (domyślnie)</span><span class="sxs-lookup"><span data-stu-id="63cb8-120">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="63cb8-121">Microsoft Edge (wersja 80 lub nowsza)</span><span class="sxs-lookup"><span data-stu-id="63cb8-121">Microsoft Edge (version 80 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="63cb8-122">Włącz debugowanie dla programu Visual Studio i Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="63cb8-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="63cb8-123">Aby włączyć debugowanie dla istniejącej Blazor WebAssembly aplikacji, zaktualizuj `launchSettings.json` plik w projekcie startowym w celu uwzględnienia następującej `inspectUri` właściwości w każdym profilu uruchamiania:</span><span class="sxs-lookup"><span data-stu-id="63cb8-123">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="63cb8-124">Po zaktualizowaniu `launchSettings.json` plik powinien wyglądać podobnie do poniższego przykładu:</span><span class="sxs-lookup"><span data-stu-id="63cb8-124">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="63cb8-125">`inspectUri`Właściwość:</span><span class="sxs-lookup"><span data-stu-id="63cb8-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="63cb8-126">Umożliwia środowisku IDE wykrywanie, czy aplikacja jest Blazor WebAssembly aplikacją.</span><span class="sxs-lookup"><span data-stu-id="63cb8-126">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="63cb8-127">Powoduje, że infrastruktura debugowania skryptów umożliwia łączenie się z przeglądarką przez Blazor serwer proxy debugowania.</span><span class="sxs-lookup"><span data-stu-id="63cb8-127">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="63cb8-128">Wartości zastępcze protokołu WebSockets Protocol ( `wsProtocol` ), hosta ( `url.hostname` ), portu ( `url.port` ) i kontrolera URI w uruchomionej przeglądarce ( `browserInspectUri` ) są udostępniane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="63cb8-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="63cb8-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="63cb8-129">Visual Studio</span></span>

<span data-ttu-id="63cb8-130">Aby debugować Blazor WebAssembly aplikację w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="63cb8-130">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="63cb8-131">Utwórz nową aplikację hostowaną ASP.NET Core Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="63cb8-131">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="63cb8-132">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację w debugerze.</span><span class="sxs-lookup"><span data-stu-id="63cb8-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="63cb8-133">Ustaw punkt przerwania w `Pages/Counter.razor` `IncrementCount` metodzie.</span><span class="sxs-lookup"><span data-stu-id="63cb8-133">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="63cb8-134">Przejdź do **`Counter`** karty i wybierz przycisk, aby trafić w punkt przerwania:</span><span class="sxs-lookup"><span data-stu-id="63cb8-134">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![Licznik debugowania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="63cb8-136">Sprawdź wartość `currentCount` pola w oknie zmiennych lokalnych:</span><span class="sxs-lookup"><span data-stu-id="63cb8-136">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Wyświetl elementy lokalne](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="63cb8-138">Naciśnij klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie.</span><span class="sxs-lookup"><span data-stu-id="63cb8-138">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="63cb8-139">Podczas debugowania Blazor WebAssembly aplikacji można także debugować kod serwera:</span><span class="sxs-lookup"><span data-stu-id="63cb8-139">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="63cb8-140">Ustaw punkt przerwania na `Pages/FetchData.razor` stronie w <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="63cb8-140">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="63cb8-141">Ustaw punkt przerwania w `WeatherForecastController` `Get` metodzie Action.</span><span class="sxs-lookup"><span data-stu-id="63cb8-141">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="63cb8-142">Przejdź do **`Fetch Data`** karty, aby trafić pierwszy punkt przerwania w `FetchData` składniku tuż przed wysłaniem żądania HTTP do serwera:</span><span class="sxs-lookup"><span data-stu-id="63cb8-142">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Debuguj dane pobierania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="63cb8-144">Naciśnij klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie, a następnie naciśnij punkt przerwania na serwerze w `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="63cb8-144">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Serwer debugowania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="63cb8-146">Naciśnij ponownie klawisz <kbd>F5</kbd> , aby umożliwić kontynuowanie wykonywania, a następnie zapoznaj się z renderowaną tabelą prognozy pogody.</span><span class="sxs-lookup"><span data-stu-id="63cb8-146">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="63cb8-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="63cb8-147">Visual Studio Code</span></span>

### <a name="debug-standalone-no-locblazor-webassembly"></a><span data-ttu-id="63cb8-148">Debuguj autonomiczneBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="63cb8-148">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="63cb8-149">Otwórz aplikację autonomiczną Blazor WebAssembly w vs Code.</span><span class="sxs-lookup"><span data-stu-id="63cb8-149">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="63cb8-150">Aby włączyć debugowanie, może zostać wyświetlone następujące powiadomienie:</span><span class="sxs-lookup"><span data-stu-id="63cb8-150">You may receive the following notification that additional setup is required to enable debugging:</span></span>
   
   ![Wymagana dodatkowa konfiguracja](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)
   
   <span data-ttu-id="63cb8-152">Jeśli otrzymasz powiadomienie:</span><span class="sxs-lookup"><span data-stu-id="63cb8-152">If you receive the notification:</span></span>

   * <span data-ttu-id="63cb8-153">Upewnij się, że zainstalowano najnowszą wersję [języka C# dla rozszerzenia Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) .</span><span class="sxs-lookup"><span data-stu-id="63cb8-153">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="63cb8-154">Aby sprawdzić zainstalowane rozszerzenia, Otwórz rozszerzenia **widoku**  >  **Extensions** na pasku menu lub wybierz ikonę **rozszerzenia** na pasku bocznym **działania** .</span><span class="sxs-lookup"><span data-stu-id="63cb8-154">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="63cb8-155">Upewnij się, że debugowanie JavaScript Preview jest włączone.</span><span class="sxs-lookup"><span data-stu-id="63cb8-155">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="63cb8-156">Otwórz ustawienia na pasku menu (**File**  >  **Preferences**  >  **Ustawienia**preferencji plików).</span><span class="sxs-lookup"><span data-stu-id="63cb8-156">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="63cb8-157">Wyszukaj przy użyciu słów kluczowych `debug preview` .</span><span class="sxs-lookup"><span data-stu-id="63cb8-157">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="63cb8-158">Upewnij się, że w wynikach wyszukiwania znajduje się pole wyboru **debuguj > JavaScript: Użyj opcji Podgląd** jest zaznaczone.</span><span class="sxs-lookup"><span data-stu-id="63cb8-158">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span>
   * <span data-ttu-id="63cb8-159">Załaduj ponownie okno.</span><span class="sxs-lookup"><span data-stu-id="63cb8-159">Reload the window.</span></span>

1. <span data-ttu-id="63cb8-160">Rozpocznij debugowanie przy użyciu skrótu klawiaturowego <kbd>F5</kbd> lub elementu menu.</span><span class="sxs-lookup"><span data-stu-id="63cb8-160">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

1. <span data-ttu-id="63cb8-161">Po wyświetleniu monitu wybierz opcję \*\* Blazor WebAssembly Debuguj\*\* , aby rozpocząć debugowanie.</span><span class="sxs-lookup"><span data-stu-id="63cb8-161">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Lista dostępnych opcji debugowania](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="63cb8-163">Uruchomiona jest aplikacja autonomiczna i zostanie otwarta przeglądarka debugowania.</span><span class="sxs-lookup"><span data-stu-id="63cb8-163">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="63cb8-164">Ustaw punkt przerwania w `IncrementCount` metodzie w `Counter` składniku, a następnie wybierz przycisk, aby trafić w punkt przerwania:</span><span class="sxs-lookup"><span data-stu-id="63cb8-164">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Debuguj licznik w VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="63cb8-166">Debuguj hostowaneBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="63cb8-166">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="63cb8-167">Otwórz Blazor WebAssembly folder rozwiązania aplikacji hostowanej w vs Code.</span><span class="sxs-lookup"><span data-stu-id="63cb8-167">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="63cb8-168">Jeśli nie ma ustawionej konfiguracji uruchamiania dla projektu, wyświetlane jest następujące powiadomienie.</span><span class="sxs-lookup"><span data-stu-id="63cb8-168">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="63cb8-169">Wybierz pozycję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="63cb8-169">Select **Yes**.</span></span>

   ![Dodaj wymagane zasoby](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="63cb8-171">W palecie poleceń w górnej części okna wybierz projekt *serwera* w ramach rozwiązania hostowanego.</span><span class="sxs-lookup"><span data-stu-id="63cb8-171">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="63cb8-172">`launch.json`Generowany jest plik z konfiguracją uruchamiania do uruchamiania debugera.</span><span class="sxs-lookup"><span data-stu-id="63cb8-172">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="63cb8-173">Dołącz do istniejącej sesji debugowania</span><span class="sxs-lookup"><span data-stu-id="63cb8-173">Attach to an existing debugging session</span></span>

<span data-ttu-id="63cb8-174">Aby dołączyć do uruchomionej Blazor aplikacji, Utwórz `launch.json` plik z następującą konfiguracją:</span><span class="sxs-lookup"><span data-stu-id="63cb8-174">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="63cb8-175">Dołączanie do sesji debugowania jest obsługiwane tylko w przypadku aplikacji autonomicznych.</span><span class="sxs-lookup"><span data-stu-id="63cb8-175">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="63cb8-176">Aby można było używać debugowania pełnego stosu, należy uruchomić aplikację z VS Code.</span><span class="sxs-lookup"><span data-stu-id="63cb8-176">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="63cb8-177">Opcje konfiguracji uruchamiania</span><span class="sxs-lookup"><span data-stu-id="63cb8-177">Launch configuration options</span></span>

<span data-ttu-id="63cb8-178">Następujące opcje konfiguracji uruchamiania są obsługiwane dla `blazorwasm` typu debugowania ( `.vscode/launch.json` ).</span><span class="sxs-lookup"><span data-stu-id="63cb8-178">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="63cb8-179">Opcja</span><span class="sxs-lookup"><span data-stu-id="63cb8-179">Option</span></span>    | <span data-ttu-id="63cb8-180">Opis</span><span class="sxs-lookup"><span data-stu-id="63cb8-180">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="63cb8-181">Służy `launch` do uruchamiania i dołączania sesji debugowania do Blazor WebAssembly aplikacji lub `attach` do dołączania sesji debugowania do już uruchomionej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="63cb8-181">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="63cb8-182">Adres URL, który ma zostać otwarty w przeglądarce podczas debugowania.</span><span class="sxs-lookup"><span data-stu-id="63cb8-182">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="63cb8-183">Wartość domyślna to `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="63cb8-183">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="63cb8-184">Przeglądarka do uruchomienia dla sesji debugowania.</span><span class="sxs-lookup"><span data-stu-id="63cb8-184">The browser to launch for the debugging session.</span></span> <span data-ttu-id="63cb8-185">Ustaw `edge` lub `chrome`.</span><span class="sxs-lookup"><span data-stu-id="63cb8-185">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="63cb8-186">Wartość domyślna to `chrome` .</span><span class="sxs-lookup"><span data-stu-id="63cb8-186">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="63cb8-187">Służy do generowania dzienników z debugera JS.</span><span class="sxs-lookup"><span data-stu-id="63cb8-187">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="63cb8-188">Ustaw, aby `true` generować dzienniki.</span><span class="sxs-lookup"><span data-stu-id="63cb8-188">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="63cb8-189">Musi mieć ustawioną wartość w `true` przypadku uruchamiania i debugowania hostowanej Blazor WebAssembly aplikacji.</span><span class="sxs-lookup"><span data-stu-id="63cb8-189">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="63cb8-190">Określa ścieżkę bezwzględną serwera sieci Web.</span><span class="sxs-lookup"><span data-stu-id="63cb8-190">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="63cb8-191">Należy ustawić, jeśli aplikacja jest obsługiwana z podmarszruty.</span><span class="sxs-lookup"><span data-stu-id="63cb8-191">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="63cb8-192">Liczba milisekund oczekiwania na dołączenie sesji debugowania.</span><span class="sxs-lookup"><span data-stu-id="63cb8-192">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="63cb8-193">Wartość domyślna to 30 000 milisekund (30 sekund).</span><span class="sxs-lookup"><span data-stu-id="63cb8-193">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="63cb8-194">Odwołanie do pliku wykonywalnego, aby uruchomić serwer hostowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="63cb8-194">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="63cb8-195">Musi być ustawiona, jeśli `hosted` ma wartość `true` .</span><span class="sxs-lookup"><span data-stu-id="63cb8-195">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="63cb8-196">Katalog roboczy, w którym jest uruchomiona aplikacja.</span><span class="sxs-lookup"><span data-stu-id="63cb8-196">The working directory to launch the app under.</span></span> <span data-ttu-id="63cb8-197">Musi być ustawiona, jeśli `hosted` ma wartość `true` .</span><span class="sxs-lookup"><span data-stu-id="63cb8-197">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="63cb8-198">Zmienne środowiskowe do zapewnienia uruchomionego procesu.</span><span class="sxs-lookup"><span data-stu-id="63cb8-198">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="63cb8-199">Ma zastosowanie tylko wtedy `hosted` , gdy jest ustawiona na `true` .</span><span class="sxs-lookup"><span data-stu-id="63cb8-199">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="63cb8-200">Przykładowe konfiguracje uruchamiania</span><span class="sxs-lookup"><span data-stu-id="63cb8-200">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="63cb8-201">Uruchamianie i debugowanie aplikacji autonomicznej Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="63cb8-201">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="63cb8-202">Dołącz do uruchomionej aplikacji pod określonym adresem URL</span><span class="sxs-lookup"><span data-stu-id="63cb8-202">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="63cb8-203">Uruchamianie i debugowanie hostowanej Blazor WebAssembly aplikacji przy użyciu przeglądarki Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="63cb8-203">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="63cb8-204">Domyślna konfiguracja przeglądarki w przeglądarce Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="63cb8-204">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="63cb8-205">W przypadku korzystania z przeglądarki Microsoft Edge na potrzeby debugowania ustaw wartość `browser` `edge` .</span><span class="sxs-lookup"><span data-stu-id="63cb8-205">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="63cb8-206">Aby użyć programu Google Chrome, nie ustawiaj `browser` opcji lub ustaw wartość opcji na `chrome` .</span><span class="sxs-lookup"><span data-stu-id="63cb8-206">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

<span data-ttu-id="63cb8-207">W poprzednim przykładzie `MyHostedApp.Server.dll` jest to zestaw aplikacji *serwera* .</span><span class="sxs-lookup"><span data-stu-id="63cb8-207">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="63cb8-208">Folder znajduje się `.vscode` w folderze rozwiązania obok `Client` `Server` folderów, i `Shared` .</span><span class="sxs-lookup"><span data-stu-id="63cb8-208">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

## <a name="debug-in-the-browser"></a><span data-ttu-id="63cb8-209">Debuguj w przeglądarce</span><span class="sxs-lookup"><span data-stu-id="63cb8-209">Debug in the browser</span></span>

1. <span data-ttu-id="63cb8-210">Uruchom kompilację debugowania aplikacji w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="63cb8-210">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="63cb8-211">Uruchom przeglądarkę i przejdź do adresu URL aplikacji (na przykład `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="63cb8-211">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="63cb8-212">W przeglądarce spróbuj rozpocząć debugowanie zdalne, naciskając klawisz <kbd>SHIFT</kbd> + <kbd>Alt</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="63cb8-212">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

   <span data-ttu-id="63cb8-213">Przeglądarka musi być uruchomiona z włączonym debugowaniem zdalnym, które nie jest ustawieniem domyślnym.</span><span class="sxs-lookup"><span data-stu-id="63cb8-213">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="63cb8-214">Jeśli debugowanie zdalne jest wyłączone, **nie można odnaleźć strony błędu karty przeglądarki możliwością debugowania** z instrukcjami dotyczącymi uruchamiania przeglądarki z otwartym portem debugowania.</span><span class="sxs-lookup"><span data-stu-id="63cb8-214">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="63cb8-215">Postępuj zgodnie z instrukcjami wyświetlanymi w przeglądarce, otwierając nowe okno przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="63cb8-215">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="63cb8-216">Zamknij poprzednie okno przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="63cb8-216">Close the previous browser window.</span></span>

1. <span data-ttu-id="63cb8-217">Po uruchomieniu przeglądarki z włączonym debugowaniem zdalnym skrót klawiaturowy debugowania (<kbd>SHIFT</kbd> + <kbd>Alt</kbd> + <kbd>D</kbd>) otwiera nową kartę debugera.</span><span class="sxs-lookup"><span data-stu-id="63cb8-217">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut (<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>) opens a new debugger tab.</span></span>

1. <span data-ttu-id="63cb8-218">Po chwili na karcie **źródła** zostanie wyświetlona lista zestawów .NET aplikacji znajdujących się w `file://` węźle.</span><span class="sxs-lookup"><span data-stu-id="63cb8-218">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="63cb8-219">W kodzie składnika ( `.razor` pliki) i pliki kodu C# ( `.cs` ) punkty przerwania, które można ustawić, są trafień, gdy zostanie wykonany kod.</span><span class="sxs-lookup"><span data-stu-id="63cb8-219">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="63cb8-220">Po trafieniu punktu przerwania pojedynczy krok (<kbd>F10</kbd>) za pomocą kodu lub wznowienia kodu (<kbd>F8</kbd>) normalnie.</span><span class="sxs-lookup"><span data-stu-id="63cb8-220">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="63cb8-221">Blazorudostępnia serwer proxy debugowania, który implementuje [Protokół Chrome devtools](https://chromedevtools.github.io/devtools-protocol/) i rozszerza protokół z. Informacje specyficzne dla sieci.</span><span class="sxs-lookup"><span data-stu-id="63cb8-221">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="63cb8-222">Gdy skrót klawiaturowy debugowania zostanie nacionięty, Blazor wskazuje, że program Chrome devtools na serwerze proxy.</span><span class="sxs-lookup"><span data-stu-id="63cb8-222">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="63cb8-223">Serwer proxy nawiązuje połączenie z oknem przeglądarki, które próbujesz debugować (w związku z tym trzeba włączyć debugowanie zdalne).</span><span class="sxs-lookup"><span data-stu-id="63cb8-223">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="63cb8-224">Mapy źródeł przeglądarki</span><span class="sxs-lookup"><span data-stu-id="63cb8-224">Browser source maps</span></span>

<span data-ttu-id="63cb8-225">Mapy źródeł przeglądarki umożliwiają przeglądarce mapowanie skompilowanych plików z powrotem do ich oryginalnych plików źródłowych i są często używane do debugowania po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="63cb8-225">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="63cb8-226">Jednak Blazor obecnie nie mapuje języka C# bezpośrednio do języka JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="63cb8-226">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="63cb8-227">Zamiast tego program Blazor wykonuje interpretację Il w przeglądarce, dlatego mapy źródłowe nie są istotne.</span><span class="sxs-lookup"><span data-stu-id="63cb8-227">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="63cb8-228">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="63cb8-228">Troubleshoot</span></span>

<span data-ttu-id="63cb8-229">Jeśli występują błędy, następujące porady mogą pomóc:</span><span class="sxs-lookup"><span data-stu-id="63cb8-229">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="63cb8-230">Na karcie **debuger** Otwórz narzędzia deweloperskie w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="63cb8-230">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="63cb8-231">W konsoli programu wykonaj polecenie, `localStorage.clear()` Aby usunąć wszystkie punkty przerwania.</span><span class="sxs-lookup"><span data-stu-id="63cb8-231">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="63cb8-232">Upewnij się, że został zainstalowany i zaufany certyfikat programistyczny protokołu HTTPS ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63cb8-232">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="63cb8-233">Aby uzyskać więcej informacji, zobacz <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="63cb8-233">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="63cb8-234">Program Visual Studio wymaga opcji **Enable JavaScript Debug for ASP.NET (Chrome, Edge i IE)** w **Tools**  >  **opcji**narzędzia  >  **debugowanie**  >  **Ogólne**.</span><span class="sxs-lookup"><span data-stu-id="63cb8-234">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="63cb8-235">Jest to ustawienie domyślne dla programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="63cb8-235">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="63cb8-236">Jeśli debugowanie nie działa, upewnij się, że opcja jest zaznaczona.</span><span class="sxs-lookup"><span data-stu-id="63cb8-236">If debugging isn't working, confirm that the option is selected.</span></span>
