---
title: Łącze przeglądarki w ASP.NET Core
author: ncarandini
description: W tym artykule wyjaśniono, jak łącze przeglądarki jest funkcją programu Visual Studio, która łączy środowisko programistyczne z co najmniej jedną przeglądarką sieci Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: 19cc3c2ed91bd9e05df3c036123c78ecbf81fcc0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658852"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="e2245-103">Łącze przeglądarki w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e2245-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="e2245-104">Przez [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson)i [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="e2245-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="e2245-105">Łącze przeglądarki jest funkcją programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e2245-105">Browser Link is a Visual Studio feature.</span></span> <span data-ttu-id="e2245-106">Tworzy kanał komunikacji między środowiskiem programistycznym a jedną lub kilkoma przeglądarkami internetowymi.</span><span class="sxs-lookup"><span data-stu-id="e2245-106">It creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="e2245-107">Za pomocą łącza przeglądarki można odświeżyć aplikację internetową w kilku przeglądarkach jednocześnie, co jest przydatne do testowania między przeglądarkami.</span><span class="sxs-lookup"><span data-stu-id="e2245-107">You can use Browser Link to refresh your web app in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="e2245-108">Konfiguracja łącza przeglądarki</span><span class="sxs-lookup"><span data-stu-id="e2245-108">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e2245-109">Dodaj pakiet [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) do projektu.</span><span class="sxs-lookup"><span data-stu-id="e2245-109">Add the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package to your project.</span></span> <span data-ttu-id="e2245-110">W przypadku ASP.NET podstawowych stron razor lub projektów MVC, należy również włączyć kompilację środowiska <xref:mvc/views/view-compilation>uruchomieniowego plików Razor (*.cshtml*), jak opisano w .</span><span class="sxs-lookup"><span data-stu-id="e2245-110">For ASP.NET Core Razor Pages or MVC projects, also enable runtime compilation of Razor (*.cshtml*) files as described in <xref:mvc/views/view-compilation>.</span></span> <span data-ttu-id="e2245-111">Zmiany składni maszynki do golenia są stosowane tylko wtedy, gdy kompilacja środowiska uruchomieniowego została włączona.</span><span class="sxs-lookup"><span data-stu-id="e2245-111">Razor syntax changes are applied only when runtime compilation has been enabled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="e2245-112">Podczas konwertowania projektu ASP.NET Core 2.0 na ASP.NET Core 2.1 i przejścia do [metapakietu Microsoft.AspNetCore.App,](xref:fundamentals/metapackage-app)zainstaluj pakiet [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) dla funkcji łącza przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="e2245-112">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for Browser Link functionality.</span></span> <span data-ttu-id="e2245-113">Szablony projektów ASP.NET Core 2.1 domyślnie używają `Microsoft.AspNetCore.App` metapakiety.</span><span class="sxs-lookup"><span data-stu-id="e2245-113">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="e2245-114">Szablony projektów ASP.NET Core 2.0 **Web Application**, **Empty**i **Web API** używają [metapakiety Microsoft.AspNetCore.All,](xref:fundamentals/metapackage)która zawiera odwołanie do pakietu dla [witryny Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span><span class="sxs-lookup"><span data-stu-id="e2245-114">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="e2245-115">W związku z `Microsoft.AspNetCore.All` tym za pomocą metapakiety nie wymaga dalszych działań, aby udostępnić łącze przeglądarki do użycia.</span><span class="sxs-lookup"><span data-stu-id="e2245-115">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="e2245-116">Szablon projektu **aplikacji sieci Web** ASP.NET Core 1.x zawiera odwołanie do pakietu [Microsoft.VisualStudio.Web.BrowserLink.](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)</span><span class="sxs-lookup"><span data-stu-id="e2245-116">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="e2245-117">Inne typy projektów wymagają dodania `Microsoft.VisualStudio.Web.BrowserLink`odwołania do pakietu .</span><span class="sxs-lookup"><span data-stu-id="e2245-117">Other project types require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="e2245-118">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="e2245-118">Configuration</span></span>

<span data-ttu-id="e2245-119">Zadzwoń `UseBrowserLink` w `Startup.Configure` metodzie:</span><span class="sxs-lookup"><span data-stu-id="e2245-119">Call `UseBrowserLink` in the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="e2245-120">Wywołanie `UseBrowserLink` jest zazwyczaj umieszczane wewnątrz `if` bloku, który włącza tylko łącze przeglądarki w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="e2245-120">The `UseBrowserLink` call is typically placed inside an `if` block that only enables Browser Link in the Development environment.</span></span> <span data-ttu-id="e2245-121">Przykład:</span><span class="sxs-lookup"><span data-stu-id="e2245-121">For example:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="e2245-122">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="e2245-122">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="e2245-123">Jak korzystać z łącza przeglądarki</span><span class="sxs-lookup"><span data-stu-id="e2245-123">How to use Browser Link</span></span>

<span data-ttu-id="e2245-124">Po otwarciu projektu ASP.NET Core program Visual Studio pokazuje kontrolka paska narzędzi Łącze przeglądarki obok formantu paska narzędzi **Debug target:**</span><span class="sxs-lookup"><span data-stu-id="e2245-124">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Menu rozwijane Link przeglądarki](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="e2245-126">Za pomocą paska narzędzi Łącze przeglądarki można:</span><span class="sxs-lookup"><span data-stu-id="e2245-126">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="e2245-127">Odśwież aplikację internetową w kilku przeglądarkach jednocześnie.</span><span class="sxs-lookup"><span data-stu-id="e2245-127">Refresh the web app in several browsers at once.</span></span>
* <span data-ttu-id="e2245-128">Otwórz **pulpit nawigacyjny łącza przeglądarki**.</span><span class="sxs-lookup"><span data-stu-id="e2245-128">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="e2245-129">Włącz lub wyłącz **łącze przeglądarki**.</span><span class="sxs-lookup"><span data-stu-id="e2245-129">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="e2245-130">Uwaga: Łącze przeglądarki jest domyślnie wyłączone w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e2245-130">Note: Browser Link is disabled by default in Visual Studio.</span></span>
* <span data-ttu-id="e2245-131">Włącz lub wyłącz [automatyczną synchronizację CSS](#enable-or-disable-css-auto-sync).</span><span class="sxs-lookup"><span data-stu-id="e2245-131">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="e2245-132">Odświeżanie aplikacji internetowej w kilku przeglądarkach jednocześnie</span><span class="sxs-lookup"><span data-stu-id="e2245-132">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="e2245-133">Aby wybrać pojedynczą przeglądarkę internetową do uruchomienia podczas uruchamiania projektu, użyj menu rozwijanego w formancie paska narzędzi **Debug target:**</span><span class="sxs-lookup"><span data-stu-id="e2245-133">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![Menu rozwijane F5](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="e2245-135">Aby otworzyć wiele przeglądarek jednocześnie, wybierz **pozycję Przeglądaj z...** z tego samego rozwijania.</span><span class="sxs-lookup"><span data-stu-id="e2245-135">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="e2245-136">Przytrzymaj naciśnięty klawisz <kbd>Ctrl,</kbd> aby zaznaczyć żądane przeglądarki, a następnie kliknij przycisk **Przeglądaj:**</span><span class="sxs-lookup"><span data-stu-id="e2245-136">Hold down the <kbd>Ctrl</kbd> key to select the browsers you want, and then click **Browse**:</span></span>

![Otwieranie wielu przeglądarek jednocześnie](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="e2245-138">Poniższy zrzut ekranu przedstawia program Visual Studio z otwartym widokiem indeksu i dwiema otwartymi przeglądarkami:</span><span class="sxs-lookup"><span data-stu-id="e2245-138">The following screenshot shows Visual Studio with the Index view open and two open browsers:</span></span>

![Przykład synchronizacji z dwiema przeglądarkami](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="e2245-140">Umieść wskaźnik myszy na kontrolce paska narzędzi Łącze przeglądarki, aby wyświetlić przeglądarki połączone z projektem:</span><span class="sxs-lookup"><span data-stu-id="e2245-140">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Najedź na końcówkę](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="e2245-142">Zmień widok indeksu, a wszystkie połączone przeglądarki są aktualizowane po kliknięciu przycisku Odświeżanie łącza przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="e2245-142">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![przeglądarki-sync-to-changes](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="e2245-144">Łącze przeglądarki działa również z przeglądarkami uruchamiane z zewnątrz programu Visual Studio i przejdź do adresu URL aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e2245-144">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the app URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="e2245-145">Pulpit nawigacyjny łącza przeglądarki</span><span class="sxs-lookup"><span data-stu-id="e2245-145">The Browser Link Dashboard</span></span>

<span data-ttu-id="e2245-146">Otwórz okno **Pulpit nawigacyjny łącza przeglądarki** z menu rozwijanego Łącze przeglądarki, aby zarządzać połączeniem z otwartymi przeglądarkami:</span><span class="sxs-lookup"><span data-stu-id="e2245-146">Open the **Browser Link Dashboard** window from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="e2245-148">Jeśli nie jest połączona żadna przeglądarka, można rozpocząć sesję bez debugowania, wybierając łącze **Wyświetl w przeglądarce:**</span><span class="sxs-lookup"><span data-stu-id="e2245-148">If no browser is connected, you can start a non-debugging session by selecting the **View in Browser** link:</span></span>

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="e2245-150">W przeciwnym razie połączone przeglądarki są wyświetlane ze ścieżką do strony, na którą wyświetla każda przeglądarka:</span><span class="sxs-lookup"><span data-stu-id="e2245-150">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-dashboard-dwa połączenia](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="e2245-152">Możesz również kliknąć indywidualną nazwę przeglądarki, aby odświeżyć tylko tę przeglądarkę.</span><span class="sxs-lookup"><span data-stu-id="e2245-152">You can also click on an individual browser name to refresh only that browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="e2245-153">Włączanie lub wyłączanie łącza przeglądarki</span><span class="sxs-lookup"><span data-stu-id="e2245-153">Enable or disable Browser Link</span></span>

<span data-ttu-id="e2245-154">Po ponownym włączeniu łącza przeglądarki po jego wyłączeniu należy odświeżyć przeglądarki, aby ponownie je połączyć.</span><span class="sxs-lookup"><span data-stu-id="e2245-154">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="e2245-155">Włączanie lub wyłączanie automatycznej synchronizacji CSS</span><span class="sxs-lookup"><span data-stu-id="e2245-155">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="e2245-156">Gdy css auto-sync jest włączony, połączone przeglądarki są automatycznie odświeżane po wprowadzeniu jakichkolwiek zmian w plikach CSS.</span><span class="sxs-lookup"><span data-stu-id="e2245-156">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="e2245-157">Jak to działa</span><span class="sxs-lookup"><span data-stu-id="e2245-157">How it works</span></span>

<span data-ttu-id="e2245-158">Łącze [SignalR](xref:signalr/introduction) przeglądarki służy do tworzenia kanału komunikacji między programem Visual Studio a przeglądarką.</span><span class="sxs-lookup"><span data-stu-id="e2245-158">Browser Link uses [SignalR](xref:signalr/introduction) to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="e2245-159">Gdy łącze przeglądarki jest włączone, SignalR program Visual Studio działa jako serwer, z którym może się połączyć wielu klientów (przeglądarki).</span><span class="sxs-lookup"><span data-stu-id="e2245-159">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="e2245-160">Łącze przeglądarki rejestruje również składnik oprogramowania pośredniczącego w potoku żądań ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e2245-160">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="e2245-161">Ten składnik wstrzykuje odwołania specjalne `<script>` do każdego żądania strony z serwera.</span><span class="sxs-lookup"><span data-stu-id="e2245-161">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="e2245-162">Odwołania do skryptów można wyświetlić, wybierając **pozycję Wyświetl źródło** w `<body>` przeglądarce i przewijając do końca zawartości znacznika:</span><span class="sxs-lookup"><span data-stu-id="e2245-162">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="e2245-163">Pliki źródłowe nie są modyfikowane.</span><span class="sxs-lookup"><span data-stu-id="e2245-163">Your source files aren't modified.</span></span> <span data-ttu-id="e2245-164">Składnik oprogramowania pośredniczącego dynamicznie wstrzykuje odwołania do skryptu.</span><span class="sxs-lookup"><span data-stu-id="e2245-164">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="e2245-165">Ponieważ kod po stronie przeglądarki jest cały JavaScript, SignalR działa na wszystkich przeglądarkach, które obsługuje bez konieczności wtyczki przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="e2245-165">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
