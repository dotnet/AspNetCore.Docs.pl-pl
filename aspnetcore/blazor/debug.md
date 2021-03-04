---
title: Debuguj ASP.NET Core Blazor WebAssembly
author: guardrex
description: Dowiedz się, jak debugować Blazor aplikacje.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
no-loc:
- appsettings.json
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
ms.openlocfilehash: 0421fd0509fbd89e4635dc7d80a584508627b52c
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109783"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="7b8ea-103">Debuguj ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7b8ea-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="7b8ea-104">Blazor WebAssembly aplikacje mogą być debugowane przy użyciu narzędzi deweloperskich przeglądarki w przeglądarkach opartych na chromie (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="7b8ea-104">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="7b8ea-105">Możesz również debugować aplikację przy użyciu następujących zintegrowanych środowisk projektowych (środowisk IDE):</span><span class="sxs-lookup"><span data-stu-id="7b8ea-105">You can also debug your app using the following integrated development environments (IDEs):</span></span>

* <span data-ttu-id="7b8ea-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7b8ea-106">Visual Studio</span></span>
* <span data-ttu-id="7b8ea-107">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="7b8ea-107">Visual Studio for Mac</span></span>
* <span data-ttu-id="7b8ea-108">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7b8ea-108">Visual Studio Code</span></span>

<span data-ttu-id="7b8ea-109">Dostępne scenariusze obejmują:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-109">Available scenarios include:</span></span>

* <span data-ttu-id="7b8ea-110">Ustawianie i usuwanie punktów przerwania.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-110">Set and remove breakpoints.</span></span>
* <span data-ttu-id="7b8ea-111">Uruchom aplikację z obsługą debugowania w środowisk IDE.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-111">Run the app with debugging support in IDEs.</span></span>
* <span data-ttu-id="7b8ea-112">Pojedynczy krok po kodzie.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-112">Single-step through the code.</span></span>
* <span data-ttu-id="7b8ea-113">Wznów wykonywanie kodu za pomocą skrótu klawiaturowego w środowisk IDE.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-113">Resume code execution with a keyboard shortcut in IDEs.</span></span>
* <span data-ttu-id="7b8ea-114">W oknie *lokalne* Sprawdź wartości zmiennych lokalnych.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-114">In the *Locals* window, observe the values of local variables.</span></span>
* <span data-ttu-id="7b8ea-115">Zobacz stos wywołań, w tym łańcuchy wywołań między językami JavaScript i .NET.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-115">See the call stack, including call chains between JavaScript and .NET.</span></span>

<span data-ttu-id="7b8ea-116">Obecnie *nie* można:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-116">For now, you *can't*:</span></span>

* <span data-ttu-id="7b8ea-117">Przerwij w przypadku nieobsłużonych wyjątków.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-117">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="7b8ea-118">Punkty przerwania trafień podczas uruchamiania aplikacji przed uruchomieniem serwera proxy debugowania.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-118">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="7b8ea-119">Obejmuje to punkty przerwania w `Program.Main` ( `Program.cs` ) i punkty przerwania w [ `OnInitialized{Async}` metodach](xref:blazor/components/lifecycle#component-initialization-methods) składników ładowanych przez pierwszą stronę żądaną przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-119">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>
* <span data-ttu-id="7b8ea-120">Debuguj w scenariuszach nielokalnych (na przykład [podsystem Windows dla systemu Linux (WSL)](/windows/wsl/) lub [Visual Studio Codespaces](/visualstudio/codespaces/overview/what-is-vsonline)).</span><span class="sxs-lookup"><span data-stu-id="7b8ea-120">Debug in non-local scenarios (for example, [Windows Subsystem for Linux (WSL)](/windows/wsl/) or [Visual Studio Codespaces](/visualstudio/codespaces/overview/what-is-vsonline)).</span></span>
* <span data-ttu-id="7b8ea-121">Automatycznie Kompiluj ponownie aplikację zaplecza `*Server*` hostowanego Blazor rozwiązania podczas debugowania, na przykład uruchamiając aplikację w programie [`dotnet watch run`](xref:tutorials/dotnet-watch) .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-121">Automatically rebuild the backend `*Server*` app of a hosted Blazor solution during debugging, for example by running the app with [`dotnet watch run`](xref:tutorials/dotnet-watch).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7b8ea-122">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="7b8ea-122">Prerequisites</span></span>

<span data-ttu-id="7b8ea-123">Debugowanie wymaga jednej z następujących przeglądarek:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-123">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="7b8ea-124">Google Chrome (wersja 70 lub nowsza) (domyślnie)</span><span class="sxs-lookup"><span data-stu-id="7b8ea-124">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="7b8ea-125">Microsoft Edge (wersja 80 lub nowsza)</span><span class="sxs-lookup"><span data-stu-id="7b8ea-125">Microsoft Edge (version 80 or later)</span></span>

<span data-ttu-id="7b8ea-126">Upewnij się, że zapory lub serwery proxy nie blokują komunikacji z serwerem proxy debugowania ( `NodeJS` proces).</span><span class="sxs-lookup"><span data-stu-id="7b8ea-126">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="7b8ea-127">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja zapory](#firewall-configuration) .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-127">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

<span data-ttu-id="7b8ea-128">Użytkownicy Visual Studio Code muszą mieć następujące rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-128">Visual Studio Code users require the following extensions:</span></span>

* [<span data-ttu-id="7b8ea-129">C# dla rozszerzenia Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7b8ea-129">C# for Visual Studio Code Extension</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="7b8ea-130">[ Blazor Rozszerzenie debugowania WASM](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.blazorwasm-companion) (przy użyciu języka C# dla rozszerzenia Visual Studio Code wersja 1.23.9 lub nowsza)</span><span class="sxs-lookup"><span data-stu-id="7b8ea-130">[Blazor WASM Debugging Extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.blazorwasm-companion) (when using the C# for Visual Studio Code Extension version 1.23.9 or later)</span></span>

<span data-ttu-id="7b8ea-131">Po otwarciu projektu w VS Code może zostać wyświetlone powiadomienie, że do włączenia debugowania jest wymagana dodatkowa konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-131">After opening a project in VS Code, you may receive a notification that additional setup is required to enable debugging.</span></span> <span data-ttu-id="7b8ea-132">Jeśli jest to wymagane, Zainstaluj wymagane rozszerzenia z Visual Studio Marketplace.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-132">If requested, install the required extensions from the Visual Studio Marketplace.</span></span> <span data-ttu-id="7b8ea-133">Aby sprawdzić zainstalowane rozszerzenia, Otwórz rozszerzenia **widoku**  >   na pasku menu lub wybierz ikonę **rozszerzenia** na pasku bocznym **działania** .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-133">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>

<span data-ttu-id="7b8ea-134">Visual Studio dla komputerów Mac wymaga wersji 8,8 (kompilacja 1532) lub nowszej:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-134">Visual Studio for Mac requires version 8.8 (build 1532) or later:</span></span>

1. <span data-ttu-id="7b8ea-135">Zainstaluj najnowszą wersję Visual Studio dla komputerów Mac, wybierając przycisk **pobierz Visual Studio dla komputerów Mac** w [firmie Microsoft: Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="7b8ea-135">Install the latest release of Visual Studio for Mac by selecting the **Download Visual Studio for Mac** button at [Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>
1. <span data-ttu-id="7b8ea-136">Wybierz kanał *podglądu* z programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-136">Select the *Preview* channel from within Visual Studio.</span></span> <span data-ttu-id="7b8ea-137">Aby uzyskać więcej informacji, zobacz [Instalowanie wersji zapoznawczej Visual Studio dla komputerów Mac](/visualstudio/mac/install-preview).</span><span class="sxs-lookup"><span data-stu-id="7b8ea-137">For more information, see [Install a preview version of Visual Studio for Mac](/visualstudio/mac/install-preview).</span></span>

> [!NOTE]
> <span data-ttu-id="7b8ea-138">Przeglądarka Apple Safari w systemie macOS nie jest obecnie obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-138">Apple Safari on macOS isn't currently supported.</span></span>

## <a name="enable-debugging"></a><span data-ttu-id="7b8ea-139">Włączanie debugowania</span><span class="sxs-lookup"><span data-stu-id="7b8ea-139">Enable debugging</span></span>

<span data-ttu-id="7b8ea-140">Aby włączyć debugowanie dla istniejącej Blazor WebAssembly aplikacji, zaktualizuj `launchSettings.json` plik w projekcie startowym w celu uwzględnienia następującej `inspectUri` właściwości w każdym profilu uruchamiania:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-140">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="7b8ea-141">Po zaktualizowaniu `launchSettings.json` plik powinien wyglądać podobnie do poniższego przykładu:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-141">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="7b8ea-142">`inspectUri`Właściwość:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-142">The `inspectUri` property:</span></span>

* <span data-ttu-id="7b8ea-143">Umożliwia środowisku IDE wykrywanie, czy aplikacja jest Blazor WebAssembly aplikacją.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-143">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="7b8ea-144">Powoduje, że infrastruktura debugowania skryptów umożliwia łączenie się z przeglądarką przez Blazor serwer proxy debugowania.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-144">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="7b8ea-145">Wartości zastępcze protokołu WebSockets Protocol ( `wsProtocol` ), hosta ( `url.hostname` ), portu ( `url.port` ) i kontrolera URI w uruchomionej przeglądarce ( `browserInspectUri` ) są udostępniane przez platformę.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-145">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7b8ea-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7b8ea-146">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7b8ea-147">Aby debugować Blazor WebAssembly aplikację w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-147">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="7b8ea-148">Utwórz nową aplikację hostowaną ASP.NET Core Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-148">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="7b8ea-149">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację w debugerze.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-149">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="7b8ea-150">**Uruchamianie bez debugowania** (<kbd>Ctrl</kbd> + <kbd>F5</kbd>) nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-150">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="7b8ea-151">Gdy aplikacja jest uruchamiana w konfiguracji debugowania, obciążenie związane z debugowaniem zawsze skutkuje niewielkim obniżeniem wydajności.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-151">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="7b8ea-152">W `*Client*` aplikacji ustaw punkt przerwania w `currentCount++;` wierszu `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-152">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="7b8ea-153">W przeglądarce przejdź do `Counter` strony i wybierz przycisk **kliknij** , aby trafić punkt przerwania.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-153">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>
1. <span data-ttu-id="7b8ea-154">W programie Visual Studio Sprawdź wartość `currentCount` pola w oknie **zmiennych lokalnych** .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-154">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="7b8ea-155">Naciśnij klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-155">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="7b8ea-156">Podczas debugowania Blazor WebAssembly aplikacji można także debugować kod serwera:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-156">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="7b8ea-157">Ustaw punkt przerwania na `Pages/FetchData.razor` stronie w <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-157">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="7b8ea-158">Ustaw punkt przerwania w `WeatherForecastController` `Get` metodzie Action.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-158">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="7b8ea-159">Przejdź do `Fetch Data` strony, aby trafić pierwszy punkt przerwania w `FetchData` składniku tuż przed wysłaniem żądania HTTP do serwera.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-159">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="7b8ea-160">Naciśnij klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie, a następnie naciśnij punkt przerwania na serwerze w `WeatherForecastController` .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-160">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="7b8ea-161">Naciśnij ponownie klawisz <kbd>F5</kbd> , aby kontynuować wykonywanie, i zobacz tabelę prognoz pogody renderowanych w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-161">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="7b8ea-162">Punkty przerwania **nie** są trafiania podczas uruchamiania aplikacji przed uruchomieniem serwera proxy debugowania.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-162">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="7b8ea-163">Obejmuje to punkty przerwania w `Program.Main` ( `Program.cs` ) i punkty przerwania w [ `OnInitialized{Async}` metodach](xref:blazor/components/lifecycle#component-initialization-methods) składników ładowanych przez pierwszą stronę żądaną przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-163">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="7b8ea-164">Jeśli aplikacja jest hostowana pod inną [ścieżką bazową aplikacji](xref:blazor/host-and-deploy/index#app-base-path) niż `/` , zaktualizuj następujące właściwości w programie w `Properties/launchSettings.json` celu odzwierciedlenia podstawowej ścieżki aplikacji:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-164">If the app is hosted at a different [app base path](xref:blazor/host-and-deploy/index#app-base-path) than `/`, update the following properties in `Properties/launchSettings.json` to reflect the app's base path:</span></span>

* <span data-ttu-id="7b8ea-165">`applicationUrl`:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-165">`applicationUrl`:</span></span>

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* <span data-ttu-id="7b8ea-166">`inspectUri` dla każdego profilu:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-166">`inspectUri` of each profile:</span></span>

  ```json
  "profiles": {
    ...
    "{PROFILE 1, 2, ... N}": {
      ...
      "inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/{APP BASE PATH}/_framework/debug/ws-proxy?browser={browserInspectUri}",
      ...
    }
  }
  ```

<span data-ttu-id="7b8ea-167">Symbole zastępcze w poprzednich ustawieniach:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-167">The placeholders in the preceding settings:</span></span>

* <span data-ttu-id="7b8ea-168">`{INSECURE PORT}`: Niezabezpieczony port.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-168">`{INSECURE PORT}`: The insecure port.</span></span> <span data-ttu-id="7b8ea-169">Wartość losowa jest domyślnie określona, ale jest dozwolony port niestandardowy.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-169">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="7b8ea-170">`{APP BASE PATH}`: Ścieżka podstawowa aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-170">`{APP BASE PATH}`: The app's base path.</span></span>
* <span data-ttu-id="7b8ea-171">`{SECURE PORT}`: Bezpieczny port.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-171">`{SECURE PORT}`: The secure port.</span></span> <span data-ttu-id="7b8ea-172">Wartość losowa jest domyślnie określona, ale jest dozwolony port niestandardowy.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-172">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="7b8ea-173">`{PROFILE 1, 2, ... N}`: Uruchamianie profilów ustawień.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-173">`{PROFILE 1, 2, ... N}`: Launch settings profiles.</span></span> <span data-ttu-id="7b8ea-174">Zazwyczaj aplikacja określa domyślnie więcej niż jeden profil (na przykład profil IIS Express i profil projektu, który jest używany przez serwer Kestrel).</span><span class="sxs-lookup"><span data-stu-id="7b8ea-174">Usually, an app specifies more than one profile by default (for example, a profile for IIS Express and a project profile, which is used by Kestrel server).</span></span>

<span data-ttu-id="7b8ea-175">W poniższych przykładach aplikacja jest hostowana `/OAT` z ścieżką bazową aplikacji skonfigurowaną w programie `wwwroot/index.html` jako `<base href="/OAT/">` :</span><span class="sxs-lookup"><span data-stu-id="7b8ea-175">In the following examples, the app is hosted at `/OAT` with an app base path configured in `wwwroot/index.html` as `<base href="/OAT/">`:</span></span>

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

<span data-ttu-id="7b8ea-176">Aby uzyskać informacje na temat używania niestandardowej ścieżki bazy aplikacji dla Blazor WebAssembly aplikacji, zobacz <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-176">For information on using a custom app base path for Blazor WebAssembly apps, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7b8ea-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7b8ea-177">Visual Studio Code</span></span>](#tab/visual-studio-code)

<h2 id="vscode"><span data-ttu-id="7b8ea-178">Debuguj autonomiczne Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7b8ea-178">Debug standalone Blazor WebAssembly</span></span></h2>

<span data-ttu-id="7b8ea-179">Aby uzyskać informacje dotyczące konfigurowania VS Code zasobów w `.vscode` folderze, zobacz Wskazówki dotyczące systemu operacyjnego **Linux** w temacie <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-179">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="7b8ea-180">Otwórz aplikację autonomiczną Blazor WebAssembly w vs Code.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-180">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="7b8ea-181">Użytkownik może otrzymać powiadomienie, że do włączenia debugowania jest wymagana dodatkowa konfiguracja:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-181">You may receive a notification that additional setup is required to enable debugging:</span></span>

   > <span data-ttu-id="7b8ea-182">Do debugowania aplikacji jest wymagana dodatkowa konfiguracja Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-182">Additional setup is required to debug Blazor WebAssembly applications.</span></span>

   <span data-ttu-id="7b8ea-183">Jeśli otrzymasz powiadomienie:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-183">If you receive the notification:</span></span>

   * <span data-ttu-id="7b8ea-184">Upewnij się, że zainstalowano najnowszą wersję [języka C# dla rozszerzenia Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-184">Confirm that the latest [C# for Visual Studio Code Extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="7b8ea-185">Aby sprawdzić zainstalowane rozszerzenia, Otwórz rozszerzenia **widoku**  >   na pasku menu lub wybierz ikonę **rozszerzenia** na pasku bocznym **działania** .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-185">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="7b8ea-186">Podczas używania [języka C# dla rozszerzenia Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) w **wersji 1.23.9 lub nowszej** upewnij się, że zainstalowano najnowsze [ Blazor rozszerzenie debugowania WASM](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.blazorwasm-companion) .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-186">When using the [C# for Visual Studio Code Extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) **version 1.23.9 or later**, confirm that the latest [Blazor WASM Debugging Extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.blazorwasm-companion) is installed.</span></span> <span data-ttu-id="7b8ea-187">Aby sprawdzić zainstalowane rozszerzenia, Otwórz rozszerzenia **widoku**  >   na pasku menu lub wybierz ikonę **rozszerzenia** na pasku bocznym **działania** .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-187">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="7b8ea-188">Upewnij się, że debugowanie JavaScript Preview jest włączone.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-188">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="7b8ea-189">Otwórz ustawienia na pasku menu (  >    >  **Ustawienia** preferencji plików).</span><span class="sxs-lookup"><span data-stu-id="7b8ea-189">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="7b8ea-190">Wyszukaj przy użyciu słów kluczowych `debug preview` .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-190">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="7b8ea-191">W wynikach wyszukiwania Ustaw lub upewnij się, że pole wyboru **debuguj > JavaScript: Użyj podglądu** jest zaznaczone.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-191">In the search results, set or confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="7b8ea-192">Jeśli opcja Włącz debugowanie podglądu nie istnieje, Uaktualnij do najnowszej wersji VS Code lub zainstaluj [rozszerzenie debugera JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (vs Code w wersji 1,46 lub starszej).</span><span class="sxs-lookup"><span data-stu-id="7b8ea-192">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="7b8ea-193">Załaduj ponownie okno.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-193">Reload the window.</span></span>

1. <span data-ttu-id="7b8ea-194">Rozpocznij debugowanie przy użyciu skrótu klawiaturowego <kbd>F5</kbd> lub elementu menu.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-194">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="7b8ea-195">**Uruchamianie bez debugowania** (<kbd>Ctrl</kbd> + <kbd>F5</kbd>) nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-195">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="7b8ea-196">Gdy aplikacja jest uruchamiana w konfiguracji debugowania, obciążenie związane z debugowaniem zawsze skutkuje niewielkim obniżeniem wydajności.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-196">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="7b8ea-197">Po wyświetleniu monitu wybierz opcję **Blazor WebAssembly Debuguj** , aby rozpocząć debugowanie.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-197">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

1. <span data-ttu-id="7b8ea-198">Uruchomiona jest aplikacja autonomiczna i zostanie otwarta przeglądarka debugowania.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-198">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="7b8ea-199">W `*Client*` aplikacji ustaw punkt przerwania w `currentCount++;` wierszu `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-199">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>

1. <span data-ttu-id="7b8ea-200">W przeglądarce przejdź do `Counter` strony i wybierz przycisk **kliknij** , aby trafić punkt przerwania.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-200">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>

> [!NOTE]
> <span data-ttu-id="7b8ea-201">Punkty przerwania **nie** są trafiania podczas uruchamiania aplikacji przed uruchomieniem serwera proxy debugowania.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-201">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="7b8ea-202">Obejmuje to punkty przerwania w `Program.Main` ( `Program.cs` ) i punkty przerwania w [ `OnInitialized{Async}` metodach](xref:blazor/components/lifecycle#component-initialization-methods) składników ładowanych przez pierwszą stronę żądaną przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-202">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="7b8ea-203">Debuguj hostowane Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7b8ea-203">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="7b8ea-204">Otwórz Blazor WebAssembly folder rozwiązania aplikacji hostowanej w vs Code.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-204">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="7b8ea-205">Jeśli nie ma ustawionej konfiguracji uruchamiania dla projektu, wyświetlane jest następujące powiadomienie.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-205">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="7b8ea-206">Wybierz pozycję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-206">Select **Yes**.</span></span>

   > <span data-ttu-id="7b8ea-207">Brak wymaganych zasobów do skompilowania i debugowania w "{APPLICATION NAME}".</span><span class="sxs-lookup"><span data-stu-id="7b8ea-207">Required assets to build and debug are missing from '{APPLICATION NAME}'.</span></span> <span data-ttu-id="7b8ea-208">Dodać je?</span><span class="sxs-lookup"><span data-stu-id="7b8ea-208">Add them?</span></span>

   <span data-ttu-id="7b8ea-209">Aby uzyskać informacje dotyczące konfigurowania VS Code zasobów w `.vscode` folderze, zobacz Wskazówki dotyczące systemu operacyjnego **Linux** w temacie <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-209">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="7b8ea-210">W palecie poleceń w górnej części okna wybierz projekt *serwera* w ramach rozwiązania hostowanego.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-210">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="7b8ea-211">`launch.json`Generowany jest plik z konfiguracją uruchamiania do uruchamiania debugera.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-211">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

## <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="7b8ea-212">Dołącz do istniejącej sesji debugowania</span><span class="sxs-lookup"><span data-stu-id="7b8ea-212">Attach to an existing debugging session</span></span>

<span data-ttu-id="7b8ea-213">Aby dołączyć do uruchomionej Blazor aplikacji, Utwórz `launch.json` plik z następującą konfiguracją:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-213">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="7b8ea-214">Dołączanie do sesji debugowania jest obsługiwane tylko w przypadku aplikacji autonomicznych.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-214">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="7b8ea-215">Aby można było używać debugowania pełnego stosu, należy uruchomić aplikację z VS Code.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-215">To use full-stack debugging, you must launch the app from VS Code.</span></span>

## <a name="launch-configuration-options"></a><span data-ttu-id="7b8ea-216">Opcje konfiguracji uruchamiania</span><span class="sxs-lookup"><span data-stu-id="7b8ea-216">Launch configuration options</span></span>

<span data-ttu-id="7b8ea-217">Następujące opcje konfiguracji uruchamiania są obsługiwane dla `blazorwasm` typu debugowania ( `.vscode/launch.json` ).</span><span class="sxs-lookup"><span data-stu-id="7b8ea-217">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="7b8ea-218">Opcja</span><span class="sxs-lookup"><span data-stu-id="7b8ea-218">Option</span></span>    | <span data-ttu-id="7b8ea-219">Opis</span><span class="sxs-lookup"><span data-stu-id="7b8ea-219">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="7b8ea-220">Służy `launch` do uruchamiania i dołączania sesji debugowania do Blazor WebAssembly aplikacji lub `attach` do dołączania sesji debugowania do już uruchomionej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-220">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="7b8ea-221">Adres URL, który ma zostać otwarty w przeglądarce podczas debugowania.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-221">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="7b8ea-222">Wartość domyślna to `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-222">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="7b8ea-223">Przeglądarka do uruchomienia dla sesji debugowania.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-223">The browser to launch for the debugging session.</span></span> <span data-ttu-id="7b8ea-224">Ustaw `edge` lub `chrome`.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-224">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="7b8ea-225">Wartość domyślna to `chrome` .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-225">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="7b8ea-226">Służy do generowania dzienników z debugera JS.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-226">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="7b8ea-227">Ustaw, aby `true` generować dzienniki.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-227">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="7b8ea-228">Musi mieć ustawioną wartość w `true` przypadku uruchamiania i debugowania hostowanej Blazor WebAssembly aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-228">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="7b8ea-229">Określa ścieżkę bezwzględną serwera sieci Web.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-229">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="7b8ea-230">Należy ustawić, jeśli aplikacja jest obsługiwana z podmarszruty.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-230">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="7b8ea-231">Liczba milisekund oczekiwania na dołączenie sesji debugowania.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-231">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="7b8ea-232">Wartość domyślna to 30 000 milisekund (30 sekund).</span><span class="sxs-lookup"><span data-stu-id="7b8ea-232">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="7b8ea-233">Odwołanie do pliku wykonywalnego, aby uruchomić serwer hostowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-233">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="7b8ea-234">Musi być ustawiona, jeśli `hosted` ma wartość `true` .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-234">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="7b8ea-235">Katalog roboczy, w którym jest uruchomiona aplikacja.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-235">The working directory to launch the app under.</span></span> <span data-ttu-id="7b8ea-236">Musi być ustawiona, jeśli `hosted` ma wartość `true` .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-236">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="7b8ea-237">Zmienne środowiskowe do zapewnienia uruchomionego procesu.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-237">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="7b8ea-238">Ma zastosowanie tylko wtedy `hosted` , gdy jest ustawiona na `true` .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-238">Only applicable if `hosted` is set to `true`.</span></span> |

## <a name="example-launch-configurations"></a><span data-ttu-id="7b8ea-239">Przykładowe konfiguracje uruchamiania</span><span class="sxs-lookup"><span data-stu-id="7b8ea-239">Example launch configurations</span></span>

### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="7b8ea-240">Uruchamianie i debugowanie aplikacji autonomicznej Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7b8ea-240">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="7b8ea-241">Dołącz do uruchomionej aplikacji pod określonym adresem URL</span><span class="sxs-lookup"><span data-stu-id="7b8ea-241">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-blazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="7b8ea-242">Uruchamianie i debugowanie hostowanej Blazor WebAssembly aplikacji przy użyciu przeglądarki Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="7b8ea-242">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="7b8ea-243">Domyślna konfiguracja przeglądarki w przeglądarce Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-243">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="7b8ea-244">W przypadku korzystania z przeglądarki Microsoft Edge na potrzeby debugowania ustaw wartość `browser` `edge` .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-244">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="7b8ea-245">Aby użyć programu Google Chrome, nie ustawiaj `browser` opcji lub ustaw wartość opcji na `chrome` .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-245">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

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

<span data-ttu-id="7b8ea-246">W poprzednim przykładzie `MyHostedApp.Server.dll` jest to zestaw aplikacji *serwera* .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-246">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="7b8ea-247">Folder znajduje się `.vscode` w folderze rozwiązania obok `Client` `Server` folderów, i `Shared` .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-247">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7b8ea-248">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="7b8ea-248">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7b8ea-249">Aby debugować Blazor WebAssembly aplikację w Visual Studio dla komputerów Mac:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-249">To debug a Blazor WebAssembly app in Visual Studio for Mac:</span></span>

1. <span data-ttu-id="7b8ea-250">Utwórz nową aplikację hostowaną ASP.NET Core Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-250">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="7b8ea-251">Naciśnij <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> , aby uruchomić aplikację w debugerze.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-251">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="7b8ea-252">**Uruchamianie bez debugowania** (<kbd>&#8997;</kbd> + <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>) nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-252">**Start Without Debugging** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) isn't supported.</span></span> <span data-ttu-id="7b8ea-253">Gdy aplikacja jest uruchamiana w konfiguracji debugowania, obciążenie związane z debugowaniem zawsze skutkuje niewielkim obniżeniem wydajności.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-253">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="7b8ea-254">Przeglądarka Google Chrome lub Microsoft Edge musi być wybraną przeglądarką dla sesji debugowania.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-254">Google Chrome or Microsoft Edge must be the selected browser for the debugging session.</span></span>

1. <span data-ttu-id="7b8ea-255">W `*Client*` aplikacji ustaw punkt przerwania w `currentCount++;` wierszu `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-255">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="7b8ea-256">W przeglądarce przejdź do `Counter` strony i wybierz przycisk **kliknij** , aby trafić w punkt przerwania:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-256">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint:</span></span>
1. <span data-ttu-id="7b8ea-257">W programie Visual Studio Sprawdź wartość `currentCount` pola w oknie **zmiennych lokalnych** .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-257">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="7b8ea-258">Naciśnij <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> , aby kontynuować wykonywanie.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-258">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution.</span></span>

<span data-ttu-id="7b8ea-259">Podczas debugowania Blazor WebAssembly aplikacji można także debugować kod serwera:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-259">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="7b8ea-260">Ustaw punkt przerwania na `Pages/FetchData.razor` stronie w <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-260">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="7b8ea-261">Ustaw punkt przerwania w `WeatherForecastController` `Get` metodzie Action.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-261">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="7b8ea-262">Przejdź do `Fetch Data` strony, aby trafić pierwszy punkt przerwania w `FetchData` składniku tuż przed wysłaniem żądania HTTP do serwera.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-262">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="7b8ea-263">Naciśnij <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd> , aby kontynuować wykonywanie, a następnie naciśnij punkt przerwania na serwerze w `WeatherForecastController` .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-263">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="7b8ea-264">Naciśnij <kbd></kbd> + ponownie przycisk <kbd>&#8617;</kbd>&#8984;, aby umożliwić kontynuowanie wykonywania, i zobacz tabelę prognoz pogody renderowanych w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-264">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="7b8ea-265">Punkty przerwania **nie** są trafiania podczas uruchamiania aplikacji przed uruchomieniem serwera proxy debugowania.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-265">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="7b8ea-266">Obejmuje to punkty przerwania w `Program.Main` ( `Program.cs` ) i punkty przerwania w [ `OnInitialized{Async}` metodach](xref:blazor/components/lifecycle#component-initialization-methods) składników ładowanych przez pierwszą stronę żądaną przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-266">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="7b8ea-267">Aby uzyskać więcej informacji, zobacz [debugowanie za pomocą Visual Studio dla komputerów Mac](/visualstudio/mac/debugging).</span><span class="sxs-lookup"><span data-stu-id="7b8ea-267">For more information, see [Debugging with Visual Studio for Mac](/visualstudio/mac/debugging).</span></span>

---

## <a name="debug-in-the-browser"></a><span data-ttu-id="7b8ea-268">Debuguj w przeglądarce</span><span class="sxs-lookup"><span data-stu-id="7b8ea-268">Debug in the browser</span></span>

<span data-ttu-id="7b8ea-269">*Wskazówki zawarte w tej sekcji dotyczą przeglądarki Google Chrome i przeglądarki Microsoft Edge działającej w systemie Windows.*</span><span class="sxs-lookup"><span data-stu-id="7b8ea-269">*The guidance in this section applies to Google Chrome and Microsoft Edge running on Windows.*</span></span>

1. <span data-ttu-id="7b8ea-270">Uruchom kompilację debugowania aplikacji w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-270">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="7b8ea-271">Uruchom przeglądarkę i przejdź do adresu URL aplikacji (na przykład `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="7b8ea-271">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="7b8ea-272">W przeglądarce spróbuj rozpocząć debugowanie zdalne, naciskając klawisz <kbd>SHIFT</kbd> + <kbd>Alt</kbd> + <kbd>d</kbd>.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-272">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd>.</span></span>

   <span data-ttu-id="7b8ea-273">Przeglądarka musi być uruchomiona z włączonym debugowaniem zdalnym, które nie jest ustawieniem domyślnym.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-273">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="7b8ea-274">Jeśli debugowanie zdalne jest wyłączone, **nie można odnaleźć strony błędu karty przeglądarki możliwością debugowania** z instrukcjami dotyczącymi uruchamiania przeglądarki z otwartym portem debugowania.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-274">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="7b8ea-275">Postępuj zgodnie z instrukcjami wyświetlanymi w przeglądarce, otwierając nowe okno przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-275">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="7b8ea-276">Zamknij poprzednie okno przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-276">Close the previous browser window.</span></span>

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. <span data-ttu-id="7b8ea-277">Po uruchomieniu przeglądarki z włączonym debugowaniem zdalnym skrót klawiaturowy debugowania w poprzednim kroku otwiera nową kartę debugera.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-277">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut in the previous step opens a new debugger tab.</span></span>

1. <span data-ttu-id="7b8ea-278">Po chwili na karcie **źródła** zostanie wyświetlona lista zestawów .NET aplikacji znajdujących się w `file://` węźle.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-278">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="7b8ea-279">W kodzie składnika ( `.razor` pliki) i pliki kodu C# ( `.cs` ) punkty przerwania, które można ustawić, są trafień, gdy zostanie wykonany kod.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-279">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="7b8ea-280">Po trafieniu punktu przerwania pojedynczy krok (<kbd>F10</kbd>) za pomocą kodu lub wznowienia kodu (<kbd>F8</kbd>) normalnie.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-280">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="7b8ea-281">Blazor udostępnia serwer proxy debugowania, który implementuje [Protokół Chrome devtools](https://chromedevtools.github.io/devtools-protocol/) i rozszerza protokół z. Informacje specyficzne dla sieci.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-281">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="7b8ea-282">Gdy skrót klawiaturowy debugowania zostanie nacionięty, Blazor wskazuje, że program Chrome devtools na serwerze proxy.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-282">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="7b8ea-283">Serwer proxy nawiązuje połączenie z oknem przeglądarki, które próbujesz debugować (w związku z tym trzeba włączyć debugowanie zdalne).</span><span class="sxs-lookup"><span data-stu-id="7b8ea-283">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="7b8ea-284">Mapy źródeł przeglądarki</span><span class="sxs-lookup"><span data-stu-id="7b8ea-284">Browser source maps</span></span>

<span data-ttu-id="7b8ea-285">Mapy źródeł przeglądarki umożliwiają przeglądarce mapowanie skompilowanych plików z powrotem do ich oryginalnych plików źródłowych i są często używane do debugowania po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-285">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="7b8ea-286">Jednak Blazor obecnie nie mapuje języka C# bezpośrednio do języka JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-286">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="7b8ea-287">Zamiast tego program Blazor wykonuje interpretację Il w przeglądarce, dlatego mapy źródłowe nie są istotne.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-287">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="firewall-configuration"></a><span data-ttu-id="7b8ea-288">Konfiguracja zapory</span><span class="sxs-lookup"><span data-stu-id="7b8ea-288">Firewall configuration</span></span>

<span data-ttu-id="7b8ea-289">Jeśli zapora blokuje komunikację z serwerem proxy debugowania, należy utworzyć regułę wyjątku zapory, która zezwala na komunikację między przeglądarką a `NodeJS` procesem.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-289">If a firewall blocks communication with the debug proxy, create a firewall exception rule that permits communication between the browser and the `NodeJS` process.</span></span>

> [!WARNING]
> <span data-ttu-id="7b8ea-290">Aby uniknąć tworzenia vulnerablities zabezpieczeń, należy wprowadzić modyfikację konfiguracji zapory.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-290">Modification of a firewall configuration must be made with care to avoid creating security vulnerablities.</span></span> <span data-ttu-id="7b8ea-291">Starannie stosuj wskazówki dotyczące zabezpieczeń, stosuj najlepsze rozwiązania w zakresie zabezpieczeń oraz ostrzeżenia wydawane przez producenta zapory.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-291">Carefully apply security guidance, follow best security practices, and respect warnings issued by the firewall's manufacturer.</span></span>
>
> <span data-ttu-id="7b8ea-292">Zezwalanie na otwieranie komunikacji z `NodeJS` procesem:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-292">Permitting open communication with the `NodeJS` process:</span></span>
>
> * <span data-ttu-id="7b8ea-293">Otwiera serwer węzła do dowolnego połączenia, w zależności od możliwości i konfiguracji zapory.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-293">Opens up the Node server to any connection, depending on the firewall's capabilities and configuration.</span></span>
> * <span data-ttu-id="7b8ea-294">Może być ryzykowne w zależności od sieci.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-294">Might be risky depending on your network.</span></span>
> * <span data-ttu-id="7b8ea-295">**Jest zalecane tylko na maszynach deweloperskich.**</span><span class="sxs-lookup"><span data-stu-id="7b8ea-295">**Is only recommended on developer machines.**</span></span>
>
> <span data-ttu-id="7b8ea-296">Jeśli to możliwe, zezwalanie na tylko otwartej komunikacji z `NodeJS` procesem **w sieciach zaufanych lub prywatnych**.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-296">If possible, only allow open communication with the `NodeJS` process **on trusted or private networks**.</span></span>

<span data-ttu-id="7b8ea-297">Aby uzyskać wskazówki dotyczące konfiguracji [zapory systemu Windows](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) , zobacz [Tworzenie reguły programu w programie lub usłudze przychodzącej](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule).</span><span class="sxs-lookup"><span data-stu-id="7b8ea-297">For [Windows Firewall](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) configuration guidance, see [Create an Inbound Program or Service Rule](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule).</span></span> <span data-ttu-id="7b8ea-298">Aby uzyskać więcej informacji, zobacz [Zapora Windows Defender z zabezpieczeniami zaawansowanymi](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) i pokrewne artykuły w zestawie dokumentacji zapory systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-298">For more information, see [Windows Defender Firewall with Advanced Security](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) and related articles in the Windows Firewall documentation set.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="7b8ea-299">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="7b8ea-299">Troubleshoot</span></span>

<span data-ttu-id="7b8ea-300">Jeśli występują błędy, następujące porady mogą pomóc:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-300">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="7b8ea-301">Na karcie **debuger** Otwórz narzędzia deweloperskie w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-301">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="7b8ea-302">W konsoli programu wykonaj polecenie, `localStorage.clear()` Aby usunąć wszystkie punkty przerwania.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-302">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="7b8ea-303">Upewnij się, że został zainstalowany i zaufany certyfikat programistyczny protokołu HTTPS ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-303">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="7b8ea-304">Aby uzyskać więcej informacji, zobacz <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-304">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="7b8ea-305">Program Visual Studio wymaga opcji **Enable JavaScript Debug for ASP.NET (Chrome, Edge i IE)** w   >  **opcji** narzędzia  >  **debugowanie**  >  **Ogólne**.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-305">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="7b8ea-306">Jest to ustawienie domyślne dla programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-306">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="7b8ea-307">Jeśli debugowanie nie działa, upewnij się, że opcja jest zaznaczona.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-307">If debugging isn't working, confirm that the option is selected.</span></span>
* <span data-ttu-id="7b8ea-308">Jeśli w środowisku używany jest serwer proxy HTTP, upewnij się, że `localhost` jest on uwzględniony w ustawieniach obejścia serwera proxy.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-308">If your environment uses an HTTP proxy, make sure that `localhost` is included in the proxy bypass settings.</span></span> <span data-ttu-id="7b8ea-309">Można to zrobić, ustawiając `NO_PROXY` zmienną środowiskową w jednej z:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-309">This can be done by setting the `NO_PROXY` environment variable in either:</span></span>
  * <span data-ttu-id="7b8ea-310">`launchSettings.json`Plik dla projektu.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-310">The `launchSettings.json` file for the project.</span></span>
  * <span data-ttu-id="7b8ea-311">Na poziomie zmiennych środowiskowych użytkownika lub systemu na potrzeby zastosowania do wszystkich aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-311">At the user or system environment variables level for it to apply to all apps.</span></span> <span data-ttu-id="7b8ea-312">W przypadku korzystania ze zmiennej środowiskowej Uruchom ponownie program Visual Studio, aby zmiany zaczęły obowiązywać.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-312">When using an environment variable, restart Visual Studio for the change to take effect.</span></span>
* <span data-ttu-id="7b8ea-313">Upewnij się, że zapory lub serwery proxy nie blokują komunikacji z serwerem proxy debugowania ( `NodeJS` proces).</span><span class="sxs-lookup"><span data-stu-id="7b8ea-313">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="7b8ea-314">Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja zapory](#firewall-configuration) .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-314">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="7b8ea-315">Punkty przerwania w elemencie `OnInitialized{Async}` nie trafią</span><span class="sxs-lookup"><span data-stu-id="7b8ea-315">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="7b8ea-316">BlazorSerwer proxy debugowania struktury zajmuje trochę czasu, dlatego punkty przerwania w [ `OnInitialized{Async}` metodzie cyklu życia](xref:blazor/components/lifecycle#component-initialization-methods) mogą nie zostać trafione.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-316">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="7b8ea-317">Zalecamy dodanie opóźnienia na początku treści metody, aby dać serwerowi debugowania jakiś czas na uruchomienie przed trafieniem punktu przerwania.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-317">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="7b8ea-318">Możesz uwzględnić opóźnienie na podstawie [ `if` dyrektywy kompilatora](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) , aby upewnić się, że opóźnienie nie jest obecne dla kompilacji wydania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-318">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="7b8ea-319"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-319"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="7b8ea-320"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-320"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a><span data-ttu-id="7b8ea-321">Limit czasu programu Visual Studio (Windows)</span><span class="sxs-lookup"><span data-stu-id="7b8ea-321">Visual Studio (Windows) timeout</span></span>

<span data-ttu-id="7b8ea-322">Jeśli program Visual Studio zgłasza wyjątek, że uruchomienie karty debugowania nie powiodło się z informacją o osiągnięciu limitu czasu, można dostosować limit czasu przy użyciu ustawienia rejestru:</span><span class="sxs-lookup"><span data-stu-id="7b8ea-322">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="7b8ea-323">`{TIMEOUT}`Symbol zastępczy w poprzednim poleceniu jest w milisekundach.</span><span class="sxs-lookup"><span data-stu-id="7b8ea-323">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="7b8ea-324">Na przykład jedna minuta jest przypisana jako `60000` .</span><span class="sxs-lookup"><span data-stu-id="7b8ea-324">For example, one minute is assigned as `60000`.</span></span>
