---
title: Debuguj ASP.NET Core Blazor WebAssembly
author: guardrex
description: Dowiedz się, jak debugować Blazor aplikacje.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
no-loc:
- ASP.NET Core Identity
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
ms.openlocfilehash: 7681deb70610a8fbc27ccda7317b73921646794a
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876779"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="0ded9-103">Debuguj ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="0ded9-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="0ded9-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="0ded9-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="0ded9-105">Blazor WebAssembly aplikacje mogą być debugowane przy użyciu narzędzi deweloperskich przeglądarki w przeglądarkach opartych na chromie (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="0ded9-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="0ded9-106">Możesz również debugować aplikację przy użyciu następujących zintegrowanych środowisk projektowych (środowisk IDE):</span><span class="sxs-lookup"><span data-stu-id="0ded9-106">You can also debug your app using the following integrated development environments (IDEs):</span></span>

* <span data-ttu-id="0ded9-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ded9-107">Visual Studio</span></span>
* <span data-ttu-id="0ded9-108">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="0ded9-108">Visual Studio for Mac</span></span>
* <span data-ttu-id="0ded9-109">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0ded9-109">Visual Studio Code</span></span>

<span data-ttu-id="0ded9-110">Dostępne scenariusze obejmują:</span><span class="sxs-lookup"><span data-stu-id="0ded9-110">Available scenarios include:</span></span>

* <span data-ttu-id="0ded9-111">Ustawianie i usuwanie punktów przerwania.</span><span class="sxs-lookup"><span data-stu-id="0ded9-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="0ded9-112">Uruchom aplikację z obsługą debugowania w środowisk IDE.</span><span class="sxs-lookup"><span data-stu-id="0ded9-112">Run the app with debugging support in IDEs.</span></span>
* <span data-ttu-id="0ded9-113">Pojedynczy krok po kodzie.</span><span class="sxs-lookup"><span data-stu-id="0ded9-113">Single-step through the code.</span></span>
* <span data-ttu-id="0ded9-114">Wznów wykonywanie kodu za pomocą skrótu klawiaturowego w środowisk IDE.</span><span class="sxs-lookup"><span data-stu-id="0ded9-114">Resume code execution with a keyboard shortcut in IDEs.</span></span>
* <span data-ttu-id="0ded9-115">W oknie *lokalne* Sprawdź wartości zmiennych lokalnych.</span><span class="sxs-lookup"><span data-stu-id="0ded9-115">In the *Locals* window, observe the values of local variables.</span></span>
* <span data-ttu-id="0ded9-116">Zobacz stos wywołań, w tym łańcuchy wywołań między językami JavaScript i .NET.</span><span class="sxs-lookup"><span data-stu-id="0ded9-116">See the call stack, including call chains between JavaScript and .NET.</span></span>

<span data-ttu-id="0ded9-117">Obecnie *nie*można:</span><span class="sxs-lookup"><span data-stu-id="0ded9-117">For now, you *can't*:</span></span>

* <span data-ttu-id="0ded9-118">Przerwij w przypadku nieobsłużonych wyjątków.</span><span class="sxs-lookup"><span data-stu-id="0ded9-118">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="0ded9-119">Punkty przerwania trafień podczas uruchamiania aplikacji przed uruchomieniem serwera proxy debugowania.</span><span class="sxs-lookup"><span data-stu-id="0ded9-119">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="0ded9-120">Obejmuje to punkty przerwania w `Program.Main` ( `Program.cs` ) i punkty przerwania w [ `OnInitialized{Async}` metodach](xref:blazor/components/lifecycle#component-initialization-methods) składników ładowanych przez pierwszą stronę żądaną przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="0ded9-120">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0ded9-121">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="0ded9-121">Prerequisites</span></span>

<span data-ttu-id="0ded9-122">Debugowanie wymaga jednej z następujących przeglądarek:</span><span class="sxs-lookup"><span data-stu-id="0ded9-122">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="0ded9-123">Google Chrome (wersja 70 lub nowsza) (domyślnie)</span><span class="sxs-lookup"><span data-stu-id="0ded9-123">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="0ded9-124">Microsoft Edge (wersja 80 lub nowsza)</span><span class="sxs-lookup"><span data-stu-id="0ded9-124">Microsoft Edge (version 80 or later)</span></span>

<span data-ttu-id="0ded9-125">Visual Studio dla komputerów Mac wymaga wersji 8,8 (kompilacja 1532) lub nowszej:</span><span class="sxs-lookup"><span data-stu-id="0ded9-125">Visual Studio for Mac requires version 8.8 (build 1532) or later:</span></span>

1. <span data-ttu-id="0ded9-126">Zainstaluj najnowszą wersję Visual Studio dla komputerów Mac, wybierając przycisk **pobierz Visual Studio dla komputerów Mac** w [firmie Microsoft: Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="0ded9-126">Install the latest release of Visual Studio for Mac by selecting the **Download Visual Studio for Mac** button at [Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>
1. <span data-ttu-id="0ded9-127">Wybierz kanał *podglądu* z programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0ded9-127">Select the *Preview* channel from within Visual Studio.</span></span> <span data-ttu-id="0ded9-128">Aby uzyskać więcej informacji, zobacz [Instalowanie wersji zapoznawczej Visual Studio dla komputerów Mac](/visualstudio/mac/install-preview).</span><span class="sxs-lookup"><span data-stu-id="0ded9-128">For more information, see [Install a preview version of Visual Studio for Mac](/visualstudio/mac/install-preview).</span></span>

> [!NOTE]
> <span data-ttu-id="0ded9-129">Przeglądarka Apple Safari w systemie macOS nie jest obecnie obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="0ded9-129">Apple Safari on macOS isn't currently supported.</span></span>

## <a name="enable-debugging"></a><span data-ttu-id="0ded9-130">Włączanie debugowania</span><span class="sxs-lookup"><span data-stu-id="0ded9-130">Enable debugging</span></span>

<span data-ttu-id="0ded9-131">Aby włączyć debugowanie dla istniejącej Blazor WebAssembly aplikacji, zaktualizuj `launchSettings.json` plik w projekcie startowym w celu uwzględnienia następującej `inspectUri` właściwości w każdym profilu uruchamiania:</span><span class="sxs-lookup"><span data-stu-id="0ded9-131">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="0ded9-132">Po zaktualizowaniu `launchSettings.json` plik powinien wyglądać podobnie do poniższego przykładu:</span><span class="sxs-lookup"><span data-stu-id="0ded9-132">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="0ded9-133">`inspectUri`Właściwość:</span><span class="sxs-lookup"><span data-stu-id="0ded9-133">The `inspectUri` property:</span></span>

* <span data-ttu-id="0ded9-134">Umożliwia środowisku IDE wykrywanie, czy aplikacja jest Blazor WebAssembly aplikacją.</span><span class="sxs-lookup"><span data-stu-id="0ded9-134">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="0ded9-135">Powoduje, że infrastruktura debugowania skryptów umożliwia łączenie się z przeglądarką przez Blazor serwer proxy debugowania.</span><span class="sxs-lookup"><span data-stu-id="0ded9-135">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="0ded9-136">Wartości zastępcze protokołu WebSockets Protocol ( `wsProtocol` ), hosta ( `url.hostname` ), portu ( `url.port` ) i kontrolera URI w uruchomionej przeglądarce ( `browserInspectUri` ) są udostępniane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="0ded9-136">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0ded9-137">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ded9-137">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0ded9-138">Aby debugować Blazor WebAssembly aplikację w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="0ded9-138">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="0ded9-139">Utwórz nową aplikację hostowaną ASP.NET Core Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="0ded9-139">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="0ded9-140">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację w debugerze.</span><span class="sxs-lookup"><span data-stu-id="0ded9-140">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="0ded9-141">**Uruchamianie bez debugowania** (<kbd>Ctrl</kbd> + <kbd>F5</kbd>) nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="0ded9-141">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="0ded9-142">Gdy aplikacja jest uruchamiana w konfiguracji debugowania, obciążenie związane z debugowaniem zawsze skutkuje niewielkim obniżeniem wydajności.</span><span class="sxs-lookup"><span data-stu-id="0ded9-142">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="0ded9-143">W aplikacji *klienckiej* Ustaw punkt przerwania w `currentCount++;` wierszu `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="0ded9-143">In the *Client* app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="0ded9-144">W przeglądarce przejdź do `Counter` strony i wybierz przycisk **kliknij** , aby trafić punkt przerwania.</span><span class="sxs-lookup"><span data-stu-id="0ded9-144">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>
1. <span data-ttu-id="0ded9-145">W programie Visual Studio Sprawdź wartość `currentCount` pola w oknie **zmiennych lokalnych** .</span><span class="sxs-lookup"><span data-stu-id="0ded9-145">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="0ded9-146">Naciśnij klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie.</span><span class="sxs-lookup"><span data-stu-id="0ded9-146">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="0ded9-147">Podczas debugowania Blazor WebAssembly aplikacji można także debugować kod serwera:</span><span class="sxs-lookup"><span data-stu-id="0ded9-147">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="0ded9-148">Ustaw punkt przerwania na `Pages/FetchData.razor` stronie w <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="0ded9-148">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="0ded9-149">Ustaw punkt przerwania w `WeatherForecastController` `Get` metodzie Action.</span><span class="sxs-lookup"><span data-stu-id="0ded9-149">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="0ded9-150">Przejdź do `Fetch Data` strony, aby trafić pierwszy punkt przerwania w `FetchData` składniku tuż przed wysłaniem żądania HTTP do serwera.</span><span class="sxs-lookup"><span data-stu-id="0ded9-150">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="0ded9-151">Naciśnij klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie, a następnie naciśnij punkt przerwania na serwerze w `WeatherForecastController` .</span><span class="sxs-lookup"><span data-stu-id="0ded9-151">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="0ded9-152">Naciśnij ponownie klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie, i zobacz tabelę prognoz pogody renderowanych w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="0ded9-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="0ded9-153">Punkty przerwania **nie** są trafiania podczas uruchamiania aplikacji przed uruchomieniem serwera proxy debugowania.</span><span class="sxs-lookup"><span data-stu-id="0ded9-153">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="0ded9-154">Obejmuje to punkty przerwania w `Program.Main` ( `Program.cs` ) i punkty przerwania w [ `OnInitialized{Async}` metodach](xref:blazor/components/lifecycle#component-initialization-methods) składników ładowanych przez pierwszą stronę żądaną przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="0ded9-154">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0ded9-155">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0ded9-155">Visual Studio Code</span></span>](#tab/visual-studio-code)

<a id="vscode"></a>

## <a name="debug-standalone-no-locblazor-webassembly"></a><span data-ttu-id="0ded9-156">Debuguj autonomiczne Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="0ded9-156">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="0ded9-157">Otwórz aplikację autonomiczną Blazor WebAssembly w vs Code.</span><span class="sxs-lookup"><span data-stu-id="0ded9-157">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="0ded9-158">Użytkownik może otrzymać powiadomienie, że do włączenia debugowania jest wymagana dodatkowa konfiguracja:</span><span class="sxs-lookup"><span data-stu-id="0ded9-158">You may receive a notification that additional setup is required to enable debugging:</span></span>

   > <span data-ttu-id="0ded9-159">Do debugowania aplikacji jest wymagana dodatkowa konfiguracja Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="0ded9-159">Additional setup is required to debug Blazor WebAssembly applications.</span></span>

   <span data-ttu-id="0ded9-160">Jeśli otrzymasz powiadomienie:</span><span class="sxs-lookup"><span data-stu-id="0ded9-160">If you receive the notification:</span></span>

   * <span data-ttu-id="0ded9-161">Upewnij się, że zainstalowano najnowszą wersję [języka C# dla rozszerzenia Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) .</span><span class="sxs-lookup"><span data-stu-id="0ded9-161">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="0ded9-162">Aby sprawdzić zainstalowane rozszerzenia, Otwórz rozszerzenia **widoku**  >  **Extensions** na pasku menu lub wybierz ikonę **rozszerzenia** na pasku bocznym **działania** .</span><span class="sxs-lookup"><span data-stu-id="0ded9-162">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="0ded9-163">Upewnij się, że debugowanie JavaScript Preview jest włączone.</span><span class="sxs-lookup"><span data-stu-id="0ded9-163">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="0ded9-164">Otwórz ustawienia na pasku menu (**File**  >  **Preferences**  >  **Ustawienia**preferencji plików).</span><span class="sxs-lookup"><span data-stu-id="0ded9-164">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="0ded9-165">Wyszukaj przy użyciu słów kluczowych `debug preview` .</span><span class="sxs-lookup"><span data-stu-id="0ded9-165">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="0ded9-166">Upewnij się, że w wynikach wyszukiwania znajduje się pole wyboru **debuguj > JavaScript: Użyj opcji Podgląd** jest zaznaczone.</span><span class="sxs-lookup"><span data-stu-id="0ded9-166">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="0ded9-167">Jeśli opcja Włącz debugowanie podglądu nie istnieje, Uaktualnij do najnowszej wersji VS Code lub zainstaluj [rozszerzenie debugera JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (vs Code w wersji 1,46 lub starszej).</span><span class="sxs-lookup"><span data-stu-id="0ded9-167">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="0ded9-168">Załaduj ponownie okno.</span><span class="sxs-lookup"><span data-stu-id="0ded9-168">Reload the window.</span></span>

1. <span data-ttu-id="0ded9-169">Rozpocznij debugowanie przy użyciu skrótu klawiaturowego <kbd>F5</kbd> lub elementu menu.</span><span class="sxs-lookup"><span data-stu-id="0ded9-169">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="0ded9-170">**Uruchamianie bez debugowania** (<kbd>Ctrl</kbd> + <kbd>F5</kbd>) nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="0ded9-170">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="0ded9-171">Gdy aplikacja jest uruchamiana w konfiguracji debugowania, obciążenie związane z debugowaniem zawsze skutkuje niewielkim obniżeniem wydajności.</span><span class="sxs-lookup"><span data-stu-id="0ded9-171">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="0ded9-172">Po wyświetleniu monitu wybierz opcję \*\* Blazor WebAssembly Debuguj\*\* , aby rozpocząć debugowanie.</span><span class="sxs-lookup"><span data-stu-id="0ded9-172">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

1. <span data-ttu-id="0ded9-173">Uruchomiona jest aplikacja autonomiczna i zostanie otwarta przeglądarka debugowania.</span><span class="sxs-lookup"><span data-stu-id="0ded9-173">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="0ded9-174">W aplikacji *klienckiej* Ustaw punkt przerwania w `currentCount++;` wierszu `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="0ded9-174">In the *Client* app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>

1. <span data-ttu-id="0ded9-175">W przeglądarce przejdź do `Counter` strony i wybierz przycisk **kliknij** , aby trafić punkt przerwania.</span><span class="sxs-lookup"><span data-stu-id="0ded9-175">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>

> [!NOTE]
> <span data-ttu-id="0ded9-176">Punkty przerwania **nie** są trafiania podczas uruchamiania aplikacji przed uruchomieniem serwera proxy debugowania.</span><span class="sxs-lookup"><span data-stu-id="0ded9-176">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="0ded9-177">Obejmuje to punkty przerwania w `Program.Main` ( `Program.cs` ) i punkty przerwania w [ `OnInitialized{Async}` metodach](xref:blazor/components/lifecycle#component-initialization-methods) składników ładowanych przez pierwszą stronę żądaną przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="0ded9-177">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="0ded9-178">Debuguj hostowane Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="0ded9-178">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="0ded9-179">Otwórz Blazor WebAssembly folder rozwiązania aplikacji hostowanej w vs Code.</span><span class="sxs-lookup"><span data-stu-id="0ded9-179">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="0ded9-180">Jeśli nie ma ustawionej konfiguracji uruchamiania dla projektu, wyświetlane jest następujące powiadomienie.</span><span class="sxs-lookup"><span data-stu-id="0ded9-180">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="0ded9-181">Wybierz pozycję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="0ded9-181">Select **Yes**.</span></span>

   > <span data-ttu-id="0ded9-182">Brak wymaganych zasobów do skompilowania i debugowania w "{APPLICATION NAME}".</span><span class="sxs-lookup"><span data-stu-id="0ded9-182">Required assets to build and debug are missing from '{APPLICATION NAME}'.</span></span> <span data-ttu-id="0ded9-183">Dodać je?</span><span class="sxs-lookup"><span data-stu-id="0ded9-183">Add them?</span></span>

1. <span data-ttu-id="0ded9-184">W palecie poleceń w górnej części okna wybierz projekt *serwera* w ramach rozwiązania hostowanego.</span><span class="sxs-lookup"><span data-stu-id="0ded9-184">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="0ded9-185">`launch.json`Generowany jest plik z konfiguracją uruchamiania do uruchamiania debugera.</span><span class="sxs-lookup"><span data-stu-id="0ded9-185">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

## <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="0ded9-186">Dołącz do istniejącej sesji debugowania</span><span class="sxs-lookup"><span data-stu-id="0ded9-186">Attach to an existing debugging session</span></span>

<span data-ttu-id="0ded9-187">Aby dołączyć do uruchomionej Blazor aplikacji, Utwórz `launch.json` plik z następującą konfiguracją:</span><span class="sxs-lookup"><span data-stu-id="0ded9-187">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="0ded9-188">Dołączanie do sesji debugowania jest obsługiwane tylko w przypadku aplikacji autonomicznych.</span><span class="sxs-lookup"><span data-stu-id="0ded9-188">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="0ded9-189">Aby można było używać debugowania pełnego stosu, należy uruchomić aplikację z VS Code.</span><span class="sxs-lookup"><span data-stu-id="0ded9-189">To use full-stack debugging, you must launch the app from VS Code.</span></span>

## <a name="launch-configuration-options"></a><span data-ttu-id="0ded9-190">Opcje konfiguracji uruchamiania</span><span class="sxs-lookup"><span data-stu-id="0ded9-190">Launch configuration options</span></span>

<span data-ttu-id="0ded9-191">Następujące opcje konfiguracji uruchamiania są obsługiwane dla `blazorwasm` typu debugowania ( `.vscode/launch.json` ).</span><span class="sxs-lookup"><span data-stu-id="0ded9-191">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="0ded9-192">Opcja</span><span class="sxs-lookup"><span data-stu-id="0ded9-192">Option</span></span>    | <span data-ttu-id="0ded9-193">Opis</span><span class="sxs-lookup"><span data-stu-id="0ded9-193">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="0ded9-194">Służy `launch` do uruchamiania i dołączania sesji debugowania do Blazor WebAssembly aplikacji lub `attach` do dołączania sesji debugowania do już uruchomionej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ded9-194">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="0ded9-195">Adres URL, który ma zostać otwarty w przeglądarce podczas debugowania.</span><span class="sxs-lookup"><span data-stu-id="0ded9-195">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="0ded9-196">Wartość domyślna to `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="0ded9-196">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="0ded9-197">Przeglądarka do uruchomienia dla sesji debugowania.</span><span class="sxs-lookup"><span data-stu-id="0ded9-197">The browser to launch for the debugging session.</span></span> <span data-ttu-id="0ded9-198">Ustaw `edge` lub `chrome`.</span><span class="sxs-lookup"><span data-stu-id="0ded9-198">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="0ded9-199">Wartość domyślna to `chrome` .</span><span class="sxs-lookup"><span data-stu-id="0ded9-199">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="0ded9-200">Służy do generowania dzienników z debugera JS.</span><span class="sxs-lookup"><span data-stu-id="0ded9-200">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="0ded9-201">Ustaw, aby `true` generować dzienniki.</span><span class="sxs-lookup"><span data-stu-id="0ded9-201">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="0ded9-202">Musi mieć ustawioną wartość w `true` przypadku uruchamiania i debugowania hostowanej Blazor WebAssembly aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ded9-202">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="0ded9-203">Określa ścieżkę bezwzględną serwera sieci Web.</span><span class="sxs-lookup"><span data-stu-id="0ded9-203">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="0ded9-204">Należy ustawić, jeśli aplikacja jest obsługiwana z podmarszruty.</span><span class="sxs-lookup"><span data-stu-id="0ded9-204">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="0ded9-205">Liczba milisekund oczekiwania na dołączenie sesji debugowania.</span><span class="sxs-lookup"><span data-stu-id="0ded9-205">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="0ded9-206">Wartość domyślna to 30 000 milisekund (30 sekund).</span><span class="sxs-lookup"><span data-stu-id="0ded9-206">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="0ded9-207">Odwołanie do pliku wykonywalnego, aby uruchomić serwer hostowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ded9-207">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="0ded9-208">Musi być ustawiona, jeśli `hosted` ma wartość `true` .</span><span class="sxs-lookup"><span data-stu-id="0ded9-208">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="0ded9-209">Katalog roboczy, w którym jest uruchomiona aplikacja.</span><span class="sxs-lookup"><span data-stu-id="0ded9-209">The working directory to launch the app under.</span></span> <span data-ttu-id="0ded9-210">Musi być ustawiona, jeśli `hosted` ma wartość `true` .</span><span class="sxs-lookup"><span data-stu-id="0ded9-210">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="0ded9-211">Zmienne środowiskowe do zapewnienia uruchomionego procesu.</span><span class="sxs-lookup"><span data-stu-id="0ded9-211">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="0ded9-212">Ma zastosowanie tylko wtedy `hosted` , gdy jest ustawiona na `true` .</span><span class="sxs-lookup"><span data-stu-id="0ded9-212">Only applicable if `hosted` is set to `true`.</span></span> |

## <a name="example-launch-configurations"></a><span data-ttu-id="0ded9-213">Przykładowe konfiguracje uruchamiania</span><span class="sxs-lookup"><span data-stu-id="0ded9-213">Example launch configurations</span></span>

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="0ded9-214">Uruchamianie i debugowanie aplikacji autonomicznej Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="0ded9-214">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="0ded9-215">Dołącz do uruchomionej aplikacji pod określonym adresem URL</span><span class="sxs-lookup"><span data-stu-id="0ded9-215">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="0ded9-216">Uruchamianie i debugowanie hostowanej Blazor WebAssembly aplikacji przy użyciu przeglądarki Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="0ded9-216">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="0ded9-217">Domyślna konfiguracja przeglądarki w przeglądarce Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="0ded9-217">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="0ded9-218">W przypadku korzystania z przeglądarki Microsoft Edge na potrzeby debugowania ustaw wartość `browser` `edge` .</span><span class="sxs-lookup"><span data-stu-id="0ded9-218">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="0ded9-219">Aby użyć programu Google Chrome, nie ustawiaj `browser` opcji lub ustaw wartość opcji na `chrome` .</span><span class="sxs-lookup"><span data-stu-id="0ded9-219">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

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

<span data-ttu-id="0ded9-220">W poprzednim przykładzie `MyHostedApp.Server.dll` jest to zestaw aplikacji *serwera* .</span><span class="sxs-lookup"><span data-stu-id="0ded9-220">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="0ded9-221">Folder znajduje się `.vscode` w folderze rozwiązania obok `Client` `Server` folderów, i `Shared` .</span><span class="sxs-lookup"><span data-stu-id="0ded9-221">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0ded9-222">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="0ded9-222">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0ded9-223">Aby debugować Blazor WebAssembly aplikację w Visual Studio dla komputerów Mac:</span><span class="sxs-lookup"><span data-stu-id="0ded9-223">To debug a Blazor WebAssembly app in Visual Studio for Mac:</span></span>

1. <span data-ttu-id="0ded9-224">Utwórz nową aplikację hostowaną ASP.NET Core Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="0ded9-224">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="0ded9-225">Naciśnij <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> , aby uruchomić aplikację w debugerze.</span><span class="sxs-lookup"><span data-stu-id="0ded9-225">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="0ded9-226">**Uruchamianie bez debugowania** (<kbd>&#8997;</kbd> + <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>) nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="0ded9-226">**Start Without Debugging** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) isn't supported.</span></span> <span data-ttu-id="0ded9-227">Gdy aplikacja jest uruchamiana w konfiguracji debugowania, obciążenie związane z debugowaniem zawsze skutkuje niewielkim obniżeniem wydajności.</span><span class="sxs-lookup"><span data-stu-id="0ded9-227">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="0ded9-228">Przeglądarka Google Chrome lub Microsoft Edge musi być wybraną przeglądarką dla sesji debugowania.</span><span class="sxs-lookup"><span data-stu-id="0ded9-228">Google Chrome or Microsoft Edge must be the selected browser for the debugging session.</span></span>

1. <span data-ttu-id="0ded9-229">W aplikacji *klienckiej* Ustaw punkt przerwania w `currentCount++;` wierszu `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="0ded9-229">In the *Client* app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="0ded9-230">W przeglądarce przejdź do `Counter` strony i wybierz przycisk **kliknij** , aby trafić w punkt przerwania:</span><span class="sxs-lookup"><span data-stu-id="0ded9-230">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint:</span></span>
1. <span data-ttu-id="0ded9-231">W programie Visual Studio Sprawdź wartość `currentCount` pola w oknie **zmiennych lokalnych** .</span><span class="sxs-lookup"><span data-stu-id="0ded9-231">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="0ded9-232">Naciśnij <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> , aby kontynuować wykonywanie.</span><span class="sxs-lookup"><span data-stu-id="0ded9-232">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution.</span></span>

<span data-ttu-id="0ded9-233">Podczas debugowania Blazor WebAssembly aplikacji można także debugować kod serwera:</span><span class="sxs-lookup"><span data-stu-id="0ded9-233">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="0ded9-234">Ustaw punkt przerwania na `Pages/FetchData.razor` stronie w <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="0ded9-234">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="0ded9-235">Ustaw punkt przerwania w `WeatherForecastController` `Get` metodzie Action.</span><span class="sxs-lookup"><span data-stu-id="0ded9-235">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="0ded9-236">Przejdź do `Fetch Data` strony, aby trafić pierwszy punkt przerwania w `FetchData` składniku tuż przed wysłaniem żądania HTTP do serwera.</span><span class="sxs-lookup"><span data-stu-id="0ded9-236">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="0ded9-237">Naciśnij <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> , aby kontynuować wykonywanie, a następnie naciśnij punkt przerwania na serwerze w `WeatherForecastController` .</span><span class="sxs-lookup"><span data-stu-id="0ded9-237">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="0ded9-238">Naciśnij <kbd>&#8984;</kbd> + ponownie przycisk <kbd>&#8617;</kbd>&#8984;, aby umożliwić kontynuowanie wykonywania, i zobacz tabelę prognoz pogody renderowanych w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="0ded9-238">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="0ded9-239">Punkty przerwania **nie** są trafiania podczas uruchamiania aplikacji przed uruchomieniem serwera proxy debugowania.</span><span class="sxs-lookup"><span data-stu-id="0ded9-239">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="0ded9-240">Obejmuje to punkty przerwania w `Program.Main` ( `Program.cs` ) i punkty przerwania w [ `OnInitialized{Async}` metodach](xref:blazor/components/lifecycle#component-initialization-methods) składników ładowanych przez pierwszą stronę żądaną przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="0ded9-240">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="0ded9-241">Aby uzyskać więcej informacji, zobacz [debugowanie za pomocą Visual Studio dla komputerów Mac](/visualstudio/mac/debugging?view=vsmac-2019).</span><span class="sxs-lookup"><span data-stu-id="0ded9-241">For more information, see [Debugging with Visual Studio for Mac](/visualstudio/mac/debugging?view=vsmac-2019).</span></span>

---

## <a name="debug-in-the-browser"></a><span data-ttu-id="0ded9-242">Debuguj w przeglądarce</span><span class="sxs-lookup"><span data-stu-id="0ded9-242">Debug in the browser</span></span>

<span data-ttu-id="0ded9-243">*Wskazówki zawarte w tej sekcji dotyczą przeglądarki Google Chrome i przeglądarki Microsoft Edge działającej w systemie Windows.*</span><span class="sxs-lookup"><span data-stu-id="0ded9-243">*The guidance in this section applies to Google Chrome and Microsoft Edge running on Windows.*</span></span>

1. <span data-ttu-id="0ded9-244">Uruchom kompilację debugowania aplikacji w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="0ded9-244">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="0ded9-245">Uruchom przeglądarkę i przejdź do adresu URL aplikacji (na przykład `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="0ded9-245">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="0ded9-246">W przeglądarce spróbuj rozpocząć debugowanie zdalne, naciskając klawisz <kbd>SHIFT</kbd> + <kbd>Alt</kbd> + <kbd>d</kbd>.</span><span class="sxs-lookup"><span data-stu-id="0ded9-246">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd>.</span></span>

   <span data-ttu-id="0ded9-247">Przeglądarka musi być uruchomiona z włączonym debugowaniem zdalnym, które nie jest ustawieniem domyślnym.</span><span class="sxs-lookup"><span data-stu-id="0ded9-247">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="0ded9-248">Jeśli debugowanie zdalne jest wyłączone, **nie można odnaleźć strony błędu karty przeglądarki możliwością debugowania** z instrukcjami dotyczącymi uruchamiania przeglądarki z otwartym portem debugowania.</span><span class="sxs-lookup"><span data-stu-id="0ded9-248">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="0ded9-249">Postępuj zgodnie z instrukcjami wyświetlanymi w przeglądarce, otwierając nowe okno przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="0ded9-249">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="0ded9-250">Zamknij poprzednie okno przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="0ded9-250">Close the previous browser window.</span></span>

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. <span data-ttu-id="0ded9-251">Po uruchomieniu przeglądarki z włączonym debugowaniem zdalnym skrót klawiaturowy debugowania w poprzednim kroku otwiera nową kartę debugera.</span><span class="sxs-lookup"><span data-stu-id="0ded9-251">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut in the previous step opens a new debugger tab.</span></span>

1. <span data-ttu-id="0ded9-252">Po chwili na karcie **źródła** zostanie wyświetlona lista zestawów .NET aplikacji znajdujących się w `file://` węźle.</span><span class="sxs-lookup"><span data-stu-id="0ded9-252">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="0ded9-253">W kodzie składnika ( `.razor` pliki) i pliki kodu C# ( `.cs` ) punkty przerwania, które można ustawić, są trafień, gdy zostanie wykonany kod.</span><span class="sxs-lookup"><span data-stu-id="0ded9-253">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="0ded9-254">Po trafieniu punktu przerwania pojedynczy krok (<kbd>F10</kbd>) za pomocą kodu lub wznowienia kodu (<kbd>F8</kbd>) normalnie.</span><span class="sxs-lookup"><span data-stu-id="0ded9-254">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="0ded9-255">Blazor udostępnia serwer proxy debugowania, który implementuje [Protokół Chrome devtools](https://chromedevtools.github.io/devtools-protocol/) i rozszerza protokół z. Informacje specyficzne dla sieci.</span><span class="sxs-lookup"><span data-stu-id="0ded9-255">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="0ded9-256">Gdy skrót klawiaturowy debugowania zostanie nacionięty, Blazor wskazuje, że program Chrome devtools na serwerze proxy.</span><span class="sxs-lookup"><span data-stu-id="0ded9-256">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="0ded9-257">Serwer proxy nawiązuje połączenie z oknem przeglądarki, które próbujesz debugować (w związku z tym trzeba włączyć debugowanie zdalne).</span><span class="sxs-lookup"><span data-stu-id="0ded9-257">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="0ded9-258">Mapy źródeł przeglądarki</span><span class="sxs-lookup"><span data-stu-id="0ded9-258">Browser source maps</span></span>

<span data-ttu-id="0ded9-259">Mapy źródeł przeglądarki umożliwiają przeglądarce mapowanie skompilowanych plików z powrotem do ich oryginalnych plików źródłowych i są często używane do debugowania po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="0ded9-259">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="0ded9-260">Jednak Blazor obecnie nie mapuje języka C# bezpośrednio do języka JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="0ded9-260">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="0ded9-261">Zamiast tego program Blazor wykonuje interpretację Il w przeglądarce, dlatego mapy źródłowe nie są istotne.</span><span class="sxs-lookup"><span data-stu-id="0ded9-261">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="0ded9-262">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="0ded9-262">Troubleshoot</span></span>

<span data-ttu-id="0ded9-263">Jeśli występują błędy, następujące porady mogą pomóc:</span><span class="sxs-lookup"><span data-stu-id="0ded9-263">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="0ded9-264">Na karcie **debuger** Otwórz narzędzia deweloperskie w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="0ded9-264">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="0ded9-265">W konsoli programu wykonaj polecenie, `localStorage.clear()` Aby usunąć wszystkie punkty przerwania.</span><span class="sxs-lookup"><span data-stu-id="0ded9-265">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="0ded9-266">Upewnij się, że został zainstalowany i zaufany certyfikat programistyczny protokołu HTTPS ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0ded9-266">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="0ded9-267">Aby uzyskać więcej informacji, zobacz <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="0ded9-267">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="0ded9-268">Program Visual Studio wymaga opcji **Enable JavaScript Debug for ASP.NET (Chrome, Edge i IE)** w **Tools**  >  **opcji**narzędzia  >  **debugowanie**  >  **Ogólne**.</span><span class="sxs-lookup"><span data-stu-id="0ded9-268">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="0ded9-269">Jest to ustawienie domyślne dla programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0ded9-269">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="0ded9-270">Jeśli debugowanie nie działa, upewnij się, że opcja jest zaznaczona.</span><span class="sxs-lookup"><span data-stu-id="0ded9-270">If debugging isn't working, confirm that the option is selected.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="0ded9-271">Punkty przerwania w elemencie `OnInitialized{Async}` nie trafią</span><span class="sxs-lookup"><span data-stu-id="0ded9-271">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="0ded9-272">BlazorSerwer proxy debugowania struktury zajmuje trochę czasu, dlatego punkty przerwania w [ `OnInitialized{Async}` metodzie cyklu życia](xref:blazor/components/lifecycle#component-initialization-methods) mogą nie zostać trafione.</span><span class="sxs-lookup"><span data-stu-id="0ded9-272">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="0ded9-273">Zalecamy dodanie opóźnienia na początku treści metody, aby dać serwerowi debugowania jakiś czas na uruchomienie przed trafieniem punktu przerwania.</span><span class="sxs-lookup"><span data-stu-id="0ded9-273">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="0ded9-274">Możesz uwzględnić opóźnienie na podstawie [ `if` dyrektywy kompilatora](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) , aby upewnić się, że opóźnienie nie jest obecne dla kompilacji wydania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ded9-274">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="0ded9-275"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="0ded9-275"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="0ded9-276"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="0ded9-276"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a><span data-ttu-id="0ded9-277">Limit czasu programu Visual Studio (Windows)</span><span class="sxs-lookup"><span data-stu-id="0ded9-277">Visual Studio (Windows) timeout</span></span>

<span data-ttu-id="0ded9-278">Jeśli program Visual Studio zgłasza wyjątek, że uruchomienie karty debugowania nie powiodło się z informacją o osiągnięciu limitu czasu, można dostosować limit czasu przy użyciu ustawienia rejestru:</span><span class="sxs-lookup"><span data-stu-id="0ded9-278">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="0ded9-279">`{TIMEOUT}`Symbol zastępczy w poprzednim poleceniu jest w milisekundach.</span><span class="sxs-lookup"><span data-stu-id="0ded9-279">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="0ded9-280">Na przykład jedna minuta jest przypisana jako `60000` .</span><span class="sxs-lookup"><span data-stu-id="0ded9-280">For example, one minute is assigned as `60000`.</span></span>
