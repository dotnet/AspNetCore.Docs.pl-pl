---
title: Debuguj ASP.NET Core Blazor WebAssembly
author: guardrex
description: Dowiedz się, jak debugować Blazor aplikacje.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 7b5dac5c634ae3eef180ef2c7c7287da94342169
ms.sourcegitcommit: 4df445e7d49a99f81625430f728c28e5d6bf2107
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2020
ms.locfileid: "88253580"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="db692-103">Debuguj ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="db692-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="db692-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="db692-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="db692-105">Blazor WebAssembly aplikacje mogą być debugowane przy użyciu narzędzi deweloperskich przeglądarki w przeglądarkach opartych na chromie (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="db692-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="db692-106">Alternatywnie możesz debugować aplikację przy użyciu programu Visual Studio lub Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="db692-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="db692-107">Dostępne scenariusze obejmują:</span><span class="sxs-lookup"><span data-stu-id="db692-107">Available scenarios include:</span></span>

* <span data-ttu-id="db692-108">Ustawianie i usuwanie punktów przerwania.</span><span class="sxs-lookup"><span data-stu-id="db692-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="db692-109">Uruchom aplikację z obsługą debugowania w programie Visual Studio i Visual Studio Code (Obsługa<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="db692-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="db692-110">Jeden krok (<kbd>F10</kbd>) za pomocą kodu.</span><span class="sxs-lookup"><span data-stu-id="db692-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="db692-111">Wznów wykonywanie kodu za pomocą <kbd>klawisza F8</kbd> w przeglądarce lub <kbd>F5</kbd> w programie Visual Studio lub Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="db692-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="db692-112">Na ekranie *Ustawienia lokalne* Obserwuj wartości zmiennych lokalnych.</span><span class="sxs-lookup"><span data-stu-id="db692-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="db692-113">Zobacz stos wywołań, w tym łańcuchy wywołań, które pochodzą z języka JavaScript do platformy .NET i z programu .NET do języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="db692-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="db692-114">Obecnie *nie*można:</span><span class="sxs-lookup"><span data-stu-id="db692-114">For now, you *can't*:</span></span>

* <span data-ttu-id="db692-115">Przerwij w przypadku nieobsłużonych wyjątków.</span><span class="sxs-lookup"><span data-stu-id="db692-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="db692-116">Trafij punkty przerwania podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="db692-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="db692-117">Będziemy nadal ulepszać środowisko debugowania w przyszłych wersjach.</span><span class="sxs-lookup"><span data-stu-id="db692-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="db692-118">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="db692-118">Prerequisites</span></span>

<span data-ttu-id="db692-119">Debugowanie wymaga jednej z następujących przeglądarek:</span><span class="sxs-lookup"><span data-stu-id="db692-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="db692-120">Google Chrome (wersja 70 lub nowsza) (domyślnie)</span><span class="sxs-lookup"><span data-stu-id="db692-120">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="db692-121">Microsoft Edge (wersja 80 lub nowsza)</span><span class="sxs-lookup"><span data-stu-id="db692-121">Microsoft Edge (version 80 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="db692-122">Włącz debugowanie dla programu Visual Studio i Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="db692-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="db692-123">Aby włączyć debugowanie dla istniejącej Blazor WebAssembly aplikacji, zaktualizuj `launchSettings.json` plik w projekcie startowym w celu uwzględnienia następującej `inspectUri` właściwości w każdym profilu uruchamiania:</span><span class="sxs-lookup"><span data-stu-id="db692-123">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="db692-124">Po zaktualizowaniu `launchSettings.json` plik powinien wyglądać podobnie do poniższego przykładu:</span><span class="sxs-lookup"><span data-stu-id="db692-124">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="db692-125">`inspectUri`Właściwość:</span><span class="sxs-lookup"><span data-stu-id="db692-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="db692-126">Umożliwia środowisku IDE wykrywanie, czy aplikacja jest Blazor WebAssembly aplikacją.</span><span class="sxs-lookup"><span data-stu-id="db692-126">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="db692-127">Powoduje, że infrastruktura debugowania skryptów umożliwia łączenie się z przeglądarką przez Blazor serwer proxy debugowania.</span><span class="sxs-lookup"><span data-stu-id="db692-127">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="db692-128">Wartości zastępcze protokołu WebSockets Protocol ( `wsProtocol` ), hosta ( `url.hostname` ), portu ( `url.port` ) i kontrolera URI w uruchomionej przeglądarce ( `browserInspectUri` ) są udostępniane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="db692-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="db692-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="db692-129">Visual Studio</span></span>

<span data-ttu-id="db692-130">Aby debugować Blazor WebAssembly aplikację w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="db692-130">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="db692-131">Utwórz nową aplikację hostowaną ASP.NET Core Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="db692-131">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="db692-132">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację w debugerze.</span><span class="sxs-lookup"><span data-stu-id="db692-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="db692-133">**Uruchamianie bez debugowania** (<kbd>Ctrl</kbd> + <kbd>F5</kbd>) nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="db692-133">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="db692-134">Gdy aplikacja jest uruchamiana w konfiguracji debugowania, obciążenie związane z debugowaniem zawsze skutkuje niewielkim obniżeniem wydajności.</span><span class="sxs-lookup"><span data-stu-id="db692-134">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="db692-135">Ustaw punkt przerwania w `Pages/Counter.razor` `IncrementCount` metodzie.</span><span class="sxs-lookup"><span data-stu-id="db692-135">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="db692-136">Przejdź do **`Counter`** karty i wybierz przycisk, aby trafić w punkt przerwania:</span><span class="sxs-lookup"><span data-stu-id="db692-136">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![Licznik debugowania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="db692-138">Sprawdź wartość `currentCount` pola w oknie zmiennych lokalnych:</span><span class="sxs-lookup"><span data-stu-id="db692-138">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Wyświetl elementy lokalne](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="db692-140">Naciśnij klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie.</span><span class="sxs-lookup"><span data-stu-id="db692-140">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="db692-141">Podczas debugowania Blazor WebAssembly aplikacji można także debugować kod serwera:</span><span class="sxs-lookup"><span data-stu-id="db692-141">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="db692-142">Ustaw punkt przerwania na `Pages/FetchData.razor` stronie w <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="db692-142">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="db692-143">Ustaw punkt przerwania w `WeatherForecastController` `Get` metodzie Action.</span><span class="sxs-lookup"><span data-stu-id="db692-143">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="db692-144">Przejdź do **`Fetch Data`** karty, aby trafić pierwszy punkt przerwania w `FetchData` składniku tuż przed wysłaniem żądania HTTP do serwera:</span><span class="sxs-lookup"><span data-stu-id="db692-144">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Debuguj dane pobierania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="db692-146">Naciśnij klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie, a następnie naciśnij punkt przerwania na serwerze w `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="db692-146">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Serwer debugowania](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="db692-148">Naciśnij ponownie klawisz <kbd>F5</kbd> , aby umożliwić kontynuowanie wykonywania, a następnie zapoznaj się z renderowaną tabelą prognozy pogody.</span><span class="sxs-lookup"><span data-stu-id="db692-148">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="db692-149">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="db692-149">Visual Studio Code</span></span>

### <a name="debug-standalone-no-locblazor-webassembly"></a><span data-ttu-id="db692-150">Debuguj autonomiczne Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="db692-150">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="db692-151">Otwórz aplikację autonomiczną Blazor WebAssembly w vs Code.</span><span class="sxs-lookup"><span data-stu-id="db692-151">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="db692-152">Aby włączyć debugowanie, może zostać wyświetlone następujące powiadomienie:</span><span class="sxs-lookup"><span data-stu-id="db692-152">You may receive the following notification that additional setup is required to enable debugging:</span></span>
   
   ![Wymagana dodatkowa konfiguracja](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)
   
   <span data-ttu-id="db692-154">Jeśli otrzymasz powiadomienie:</span><span class="sxs-lookup"><span data-stu-id="db692-154">If you receive the notification:</span></span>

   * <span data-ttu-id="db692-155">Upewnij się, że zainstalowano najnowszą wersję [języka C# dla rozszerzenia Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) .</span><span class="sxs-lookup"><span data-stu-id="db692-155">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="db692-156">Aby sprawdzić zainstalowane rozszerzenia, Otwórz rozszerzenia **widoku**  >  **Extensions** na pasku menu lub wybierz ikonę **rozszerzenia** na pasku bocznym **działania** .</span><span class="sxs-lookup"><span data-stu-id="db692-156">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="db692-157">Upewnij się, że debugowanie JavaScript Preview jest włączone.</span><span class="sxs-lookup"><span data-stu-id="db692-157">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="db692-158">Otwórz ustawienia na pasku menu (**File**  >  **Preferences**  >  **Ustawienia**preferencji plików).</span><span class="sxs-lookup"><span data-stu-id="db692-158">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="db692-159">Wyszukaj przy użyciu słów kluczowych `debug preview` .</span><span class="sxs-lookup"><span data-stu-id="db692-159">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="db692-160">Upewnij się, że w wynikach wyszukiwania znajduje się pole wyboru **debuguj > JavaScript: Użyj opcji Podgląd** jest zaznaczone.</span><span class="sxs-lookup"><span data-stu-id="db692-160">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="db692-161">Jeśli opcja Włącz debugowanie podglądu nie istnieje, Uaktualnij do najnowszej wersji VS Code lub zainstaluj [rozszerzenie debugera JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (vs Code w wersji 1,46 lub starszej).</span><span class="sxs-lookup"><span data-stu-id="db692-161">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="db692-162">Załaduj ponownie okno.</span><span class="sxs-lookup"><span data-stu-id="db692-162">Reload the window.</span></span>

1. <span data-ttu-id="db692-163">Rozpocznij debugowanie przy użyciu skrótu klawiaturowego <kbd>F5</kbd> lub elementu menu.</span><span class="sxs-lookup"><span data-stu-id="db692-163">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="db692-164">**Uruchamianie bez debugowania** (<kbd>Ctrl</kbd> + <kbd>F5</kbd>) nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="db692-164">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="db692-165">Gdy aplikacja jest uruchamiana w konfiguracji debugowania, obciążenie związane z debugowaniem zawsze skutkuje niewielkim obniżeniem wydajności.</span><span class="sxs-lookup"><span data-stu-id="db692-165">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="db692-166">Po wyświetleniu monitu wybierz opcję \*\* Blazor WebAssembly Debuguj\*\* , aby rozpocząć debugowanie.</span><span class="sxs-lookup"><span data-stu-id="db692-166">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Lista dostępnych opcji debugowania](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="db692-168">Uruchomiona jest aplikacja autonomiczna i zostanie otwarta przeglądarka debugowania.</span><span class="sxs-lookup"><span data-stu-id="db692-168">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="db692-169">Ustaw punkt przerwania w `IncrementCount` metodzie w `Counter` składniku, a następnie wybierz przycisk, aby trafić w punkt przerwania:</span><span class="sxs-lookup"><span data-stu-id="db692-169">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Debuguj licznik w VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="db692-171">Debuguj hostowane Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="db692-171">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="db692-172">Otwórz Blazor WebAssembly folder rozwiązania aplikacji hostowanej w vs Code.</span><span class="sxs-lookup"><span data-stu-id="db692-172">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="db692-173">Jeśli nie ma ustawionej konfiguracji uruchamiania dla projektu, wyświetlane jest następujące powiadomienie.</span><span class="sxs-lookup"><span data-stu-id="db692-173">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="db692-174">Wybierz pozycję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="db692-174">Select **Yes**.</span></span>

   ![Dodaj wymagane zasoby](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="db692-176">W palecie poleceń w górnej części okna wybierz projekt *serwera* w ramach rozwiązania hostowanego.</span><span class="sxs-lookup"><span data-stu-id="db692-176">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="db692-177">`launch.json`Generowany jest plik z konfiguracją uruchamiania do uruchamiania debugera.</span><span class="sxs-lookup"><span data-stu-id="db692-177">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="db692-178">Dołącz do istniejącej sesji debugowania</span><span class="sxs-lookup"><span data-stu-id="db692-178">Attach to an existing debugging session</span></span>

<span data-ttu-id="db692-179">Aby dołączyć do uruchomionej Blazor aplikacji, Utwórz `launch.json` plik z następującą konfiguracją:</span><span class="sxs-lookup"><span data-stu-id="db692-179">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="db692-180">Dołączanie do sesji debugowania jest obsługiwane tylko w przypadku aplikacji autonomicznych.</span><span class="sxs-lookup"><span data-stu-id="db692-180">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="db692-181">Aby można było używać debugowania pełnego stosu, należy uruchomić aplikację z VS Code.</span><span class="sxs-lookup"><span data-stu-id="db692-181">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="db692-182">Opcje konfiguracji uruchamiania</span><span class="sxs-lookup"><span data-stu-id="db692-182">Launch configuration options</span></span>

<span data-ttu-id="db692-183">Następujące opcje konfiguracji uruchamiania są obsługiwane dla `blazorwasm` typu debugowania ( `.vscode/launch.json` ).</span><span class="sxs-lookup"><span data-stu-id="db692-183">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="db692-184">Opcja</span><span class="sxs-lookup"><span data-stu-id="db692-184">Option</span></span>    | <span data-ttu-id="db692-185">Opis</span><span class="sxs-lookup"><span data-stu-id="db692-185">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="db692-186">Służy `launch` do uruchamiania i dołączania sesji debugowania do Blazor WebAssembly aplikacji lub `attach` do dołączania sesji debugowania do już uruchomionej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="db692-186">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="db692-187">Adres URL, który ma zostać otwarty w przeglądarce podczas debugowania.</span><span class="sxs-lookup"><span data-stu-id="db692-187">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="db692-188">Wartość domyślna to `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="db692-188">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="db692-189">Przeglądarka do uruchomienia dla sesji debugowania.</span><span class="sxs-lookup"><span data-stu-id="db692-189">The browser to launch for the debugging session.</span></span> <span data-ttu-id="db692-190">Ustaw `edge` lub `chrome`.</span><span class="sxs-lookup"><span data-stu-id="db692-190">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="db692-191">Wartość domyślna to `chrome` .</span><span class="sxs-lookup"><span data-stu-id="db692-191">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="db692-192">Służy do generowania dzienników z debugera JS.</span><span class="sxs-lookup"><span data-stu-id="db692-192">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="db692-193">Ustaw, aby `true` generować dzienniki.</span><span class="sxs-lookup"><span data-stu-id="db692-193">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="db692-194">Musi mieć ustawioną wartość w `true` przypadku uruchamiania i debugowania hostowanej Blazor WebAssembly aplikacji.</span><span class="sxs-lookup"><span data-stu-id="db692-194">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="db692-195">Określa ścieżkę bezwzględną serwera sieci Web.</span><span class="sxs-lookup"><span data-stu-id="db692-195">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="db692-196">Należy ustawić, jeśli aplikacja jest obsługiwana z podmarszruty.</span><span class="sxs-lookup"><span data-stu-id="db692-196">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="db692-197">Liczba milisekund oczekiwania na dołączenie sesji debugowania.</span><span class="sxs-lookup"><span data-stu-id="db692-197">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="db692-198">Wartość domyślna to 30 000 milisekund (30 sekund).</span><span class="sxs-lookup"><span data-stu-id="db692-198">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="db692-199">Odwołanie do pliku wykonywalnego, aby uruchomić serwer hostowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="db692-199">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="db692-200">Musi być ustawiona, jeśli `hosted` ma wartość `true` .</span><span class="sxs-lookup"><span data-stu-id="db692-200">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="db692-201">Katalog roboczy, w którym jest uruchomiona aplikacja.</span><span class="sxs-lookup"><span data-stu-id="db692-201">The working directory to launch the app under.</span></span> <span data-ttu-id="db692-202">Musi być ustawiona, jeśli `hosted` ma wartość `true` .</span><span class="sxs-lookup"><span data-stu-id="db692-202">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="db692-203">Zmienne środowiskowe do zapewnienia uruchomionego procesu.</span><span class="sxs-lookup"><span data-stu-id="db692-203">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="db692-204">Ma zastosowanie tylko wtedy `hosted` , gdy jest ustawiona na `true` .</span><span class="sxs-lookup"><span data-stu-id="db692-204">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="db692-205">Przykładowe konfiguracje uruchamiania</span><span class="sxs-lookup"><span data-stu-id="db692-205">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="db692-206">Uruchamianie i debugowanie aplikacji autonomicznej Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="db692-206">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="db692-207">Dołącz do uruchomionej aplikacji pod określonym adresem URL</span><span class="sxs-lookup"><span data-stu-id="db692-207">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="db692-208">Uruchamianie i debugowanie hostowanej Blazor WebAssembly aplikacji przy użyciu przeglądarki Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="db692-208">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="db692-209">Domyślna konfiguracja przeglądarki w przeglądarce Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="db692-209">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="db692-210">W przypadku korzystania z przeglądarki Microsoft Edge na potrzeby debugowania ustaw wartość `browser` `edge` .</span><span class="sxs-lookup"><span data-stu-id="db692-210">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="db692-211">Aby użyć programu Google Chrome, nie ustawiaj `browser` opcji lub ustaw wartość opcji na `chrome` .</span><span class="sxs-lookup"><span data-stu-id="db692-211">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

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

<span data-ttu-id="db692-212">W poprzednim przykładzie `MyHostedApp.Server.dll` jest to zestaw aplikacji *serwera* .</span><span class="sxs-lookup"><span data-stu-id="db692-212">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="db692-213">Folder znajduje się `.vscode` w folderze rozwiązania obok `Client` `Server` folderów, i `Shared` .</span><span class="sxs-lookup"><span data-stu-id="db692-213">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

## <a name="debug-in-the-browser"></a><span data-ttu-id="db692-214">Debuguj w przeglądarce</span><span class="sxs-lookup"><span data-stu-id="db692-214">Debug in the browser</span></span>

1. <span data-ttu-id="db692-215">Uruchom kompilację debugowania aplikacji w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="db692-215">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="db692-216">Uruchom przeglądarkę i przejdź do adresu URL aplikacji (na przykład `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="db692-216">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="db692-217">W przeglądarce spróbuj rozpocząć debugowanie zdalne, naciskając klawisz <kbd>SHIFT</kbd> + <kbd>Alt</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="db692-217">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

   <span data-ttu-id="db692-218">Przeglądarka musi być uruchomiona z włączonym debugowaniem zdalnym, które nie jest ustawieniem domyślnym.</span><span class="sxs-lookup"><span data-stu-id="db692-218">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="db692-219">Jeśli debugowanie zdalne jest wyłączone, **nie można odnaleźć strony błędu karty przeglądarki możliwością debugowania** z instrukcjami dotyczącymi uruchamiania przeglądarki z otwartym portem debugowania.</span><span class="sxs-lookup"><span data-stu-id="db692-219">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="db692-220">Postępuj zgodnie z instrukcjami wyświetlanymi w przeglądarce, otwierając nowe okno przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="db692-220">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="db692-221">Zamknij poprzednie okno przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="db692-221">Close the previous browser window.</span></span>

1. <span data-ttu-id="db692-222">Po uruchomieniu przeglądarki z włączonym debugowaniem zdalnym skrót klawiaturowy debugowania (<kbd>SHIFT</kbd> + <kbd>Alt</kbd> + <kbd>D</kbd>) otwiera nową kartę debugera.</span><span class="sxs-lookup"><span data-stu-id="db692-222">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut (<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>) opens a new debugger tab.</span></span>

1. <span data-ttu-id="db692-223">Po chwili na karcie **źródła** zostanie wyświetlona lista zestawów .NET aplikacji znajdujących się w `file://` węźle.</span><span class="sxs-lookup"><span data-stu-id="db692-223">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="db692-224">W kodzie składnika ( `.razor` pliki) i pliki kodu C# ( `.cs` ) punkty przerwania, które można ustawić, są trafień, gdy zostanie wykonany kod.</span><span class="sxs-lookup"><span data-stu-id="db692-224">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="db692-225">Po trafieniu punktu przerwania pojedynczy krok (<kbd>F10</kbd>) za pomocą kodu lub wznowienia kodu (<kbd>F8</kbd>) normalnie.</span><span class="sxs-lookup"><span data-stu-id="db692-225">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="db692-226">Blazor udostępnia serwer proxy debugowania, który implementuje [Protokół Chrome devtools](https://chromedevtools.github.io/devtools-protocol/) i rozszerza protokół z. Informacje specyficzne dla sieci.</span><span class="sxs-lookup"><span data-stu-id="db692-226">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="db692-227">Gdy skrót klawiaturowy debugowania zostanie nacionięty, Blazor wskazuje, że program Chrome devtools na serwerze proxy.</span><span class="sxs-lookup"><span data-stu-id="db692-227">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="db692-228">Serwer proxy nawiązuje połączenie z oknem przeglądarki, które próbujesz debugować (w związku z tym trzeba włączyć debugowanie zdalne).</span><span class="sxs-lookup"><span data-stu-id="db692-228">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="db692-229">Mapy źródeł przeglądarki</span><span class="sxs-lookup"><span data-stu-id="db692-229">Browser source maps</span></span>

<span data-ttu-id="db692-230">Mapy źródeł przeglądarki umożliwiają przeglądarce mapowanie skompilowanych plików z powrotem do ich oryginalnych plików źródłowych i są często używane do debugowania po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="db692-230">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="db692-231">Jednak Blazor obecnie nie mapuje języka C# bezpośrednio do języka JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="db692-231">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="db692-232">Zamiast tego program Blazor wykonuje interpretację Il w przeglądarce, dlatego mapy źródłowe nie są istotne.</span><span class="sxs-lookup"><span data-stu-id="db692-232">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="db692-233">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="db692-233">Troubleshoot</span></span>

<span data-ttu-id="db692-234">Jeśli występują błędy, następujące porady mogą pomóc:</span><span class="sxs-lookup"><span data-stu-id="db692-234">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="db692-235">Na karcie **debuger** Otwórz narzędzia deweloperskie w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="db692-235">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="db692-236">W konsoli programu wykonaj polecenie, `localStorage.clear()` Aby usunąć wszystkie punkty przerwania.</span><span class="sxs-lookup"><span data-stu-id="db692-236">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="db692-237">Upewnij się, że został zainstalowany i zaufany certyfikat programistyczny protokołu HTTPS ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="db692-237">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="db692-238">Aby uzyskać więcej informacji, zobacz <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="db692-238">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="db692-239">Program Visual Studio wymaga opcji **Enable JavaScript Debug for ASP.NET (Chrome, Edge i IE)** w **Tools**  >  **opcji**narzędzia  >  **debugowanie**  >  **Ogólne**.</span><span class="sxs-lookup"><span data-stu-id="db692-239">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="db692-240">Jest to ustawienie domyślne dla programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="db692-240">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="db692-241">Jeśli debugowanie nie działa, upewnij się, że opcja jest zaznaczona.</span><span class="sxs-lookup"><span data-stu-id="db692-241">If debugging isn't working, confirm that the option is selected.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="db692-242">Punkty przerwania w elemencie `OnInitialized{Async}` nie trafią</span><span class="sxs-lookup"><span data-stu-id="db692-242">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="db692-243">BlazorSerwer proxy debugowania struktury zajmuje trochę czasu, dlatego punkty przerwania w [ `OnInitialized{Async}` metodzie cyklu życia](xref:blazor/components/lifecycle#component-initialization-methods) mogą nie zostać trafione.</span><span class="sxs-lookup"><span data-stu-id="db692-243">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="db692-244">Zalecamy dodanie opóźnienia na początku treści metody, aby dać serwerowi debugowania jakiś czas na uruchomienie przed trafieniem punktu przerwania.</span><span class="sxs-lookup"><span data-stu-id="db692-244">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="db692-245">Możesz uwzględnić opóźnienie na podstawie [ `if` dyrektywy kompilatora](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) , aby upewnić się, że opóźnienie nie jest obecne dla kompilacji wydania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="db692-245">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="db692-246"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="db692-246"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="db692-247"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="db692-247"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-timeout"></a><span data-ttu-id="db692-248">Limit czasu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="db692-248">Visual Studio timeout</span></span>

<span data-ttu-id="db692-249">Jeśli program Visual Studio zgłasza wyjątek, że uruchomienie karty debugowania nie powiodło się z informacją o osiągnięciu limitu czasu, można dostosować limit czasu przy użyciu ustawienia rejestru:</span><span class="sxs-lookup"><span data-stu-id="db692-249">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="db692-250">`{TIMEOUT}`Symbol zastępczy w poprzednim poleceniu jest w milisekundach.</span><span class="sxs-lookup"><span data-stu-id="db692-250">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="db692-251">Na przykład jedna minuta jest przypisana jako `60000` .</span><span class="sxs-lookup"><span data-stu-id="db692-251">For example, one minute is assigned as `60000`.</span></span>
