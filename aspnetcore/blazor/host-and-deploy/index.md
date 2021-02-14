---
title: Hostowanie i wdrażanie ASP.NET Core Blazor
author: guardrex
description: Dowiedz się, jak hostować i wdrażać Blazor aplikacje.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
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
uid: blazor/host-and-deploy/index
ms.openlocfilehash: e7bc44b396b46e2ac3e0279520c7cc8ea6679f5a
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279768"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="dc226-103">Hostowanie i wdrażanie ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="dc226-103">Host and deploy ASP.NET Core Blazor</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="dc226-104">Publikowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="dc226-104">Publish the app</span></span>

<span data-ttu-id="dc226-105">Aplikacje są publikowane do wdrożenia w konfiguracji wydania.</span><span class="sxs-lookup"><span data-stu-id="dc226-105">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dc226-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dc226-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="dc226-107">Wybierz pozycję **kompilacja**  >  **Opublikuj aplikację {aplikacja}** na pasku nawigacyjnym.</span><span class="sxs-lookup"><span data-stu-id="dc226-107">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="dc226-108">Wybierz *element docelowy publikowania*.</span><span class="sxs-lookup"><span data-stu-id="dc226-108">Select the *publish target*.</span></span> <span data-ttu-id="dc226-109">Aby opublikować lokalnie, wybierz pozycję **folder**.</span><span class="sxs-lookup"><span data-stu-id="dc226-109">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="dc226-110">Zaakceptuj lokalizację domyślną w polu **Wybierz folder** lub określ inną lokalizację.</span><span class="sxs-lookup"><span data-stu-id="dc226-110">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="dc226-111">Wybierz przycisk **`Publish`**.</span><span class="sxs-lookup"><span data-stu-id="dc226-111">Select the **`Publish`** button.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dc226-112">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="dc226-112">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="dc226-113">Wybierz pozycję **Kompiluj**  >  **Publikowanie do folderu**.</span><span class="sxs-lookup"><span data-stu-id="dc226-113">Select **Build** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="dc226-114">Potwierdź folder, w którym mają zostać odebrane opublikowane zasoby, a następnie wybierz opcję **`Publish`** .</span><span class="sxs-lookup"><span data-stu-id="dc226-114">Confirm the folder to receive the published assets and select **`Publish`**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="dc226-115">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="dc226-115">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="dc226-116">Użyj [`dotnet publish`](/dotnet/core/tools/dotnet-publish) polecenia, aby opublikować aplikację z konfiguracją wydania:</span><span class="sxs-lookup"><span data-stu-id="dc226-116">Use the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="dc226-117">Opublikowanie aplikacji wyzwala [przywracanie](/dotnet/core/tools/dotnet-restore) zależności projektu i [kompiluje](/dotnet/core/tools/dotnet-build) projekt przed utworzeniem zasobów do wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="dc226-117">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="dc226-118">W ramach procesu kompilacji nieużywane metody i zestawy są usuwane w celu zmniejszenia rozmiaru pobierania aplikacji i czasów ładowania.</span><span class="sxs-lookup"><span data-stu-id="dc226-118">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="dc226-119">Lokalizacje publikowania:</span><span class="sxs-lookup"><span data-stu-id="dc226-119">Publish locations:</span></span>

* Blazor WebAssembly
  * <span data-ttu-id="dc226-120">Autonomiczne: aplikacja jest publikowana w `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folderze.</span><span class="sxs-lookup"><span data-stu-id="dc226-120">Standalone: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span> <span data-ttu-id="dc226-121">Aby wdrożyć aplikację jako lokację statyczną, skopiuj zawartość `wwwroot` folderu do hosta lokacji statycznej.</span><span class="sxs-lookup"><span data-stu-id="dc226-121">To deploy the app as a static site, copy the contents of the `wwwroot` folder to the static site host.</span></span>
  * <span data-ttu-id="dc226-122">Hostowane: aplikacja kliencka Blazor WebAssembly jest publikowana w `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folderze aplikacji serwerowej wraz ze wszystkimi innymi statycznymi zasobami sieci Web aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="dc226-122">Hosted: The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="dc226-123">Wdróż zawartość `publish` folderu na hoście.</span><span class="sxs-lookup"><span data-stu-id="dc226-123">Deploy the contents of the `publish` folder to the host.</span></span>
* <span data-ttu-id="dc226-124">Blazor Server: Aplikacja jest publikowana w `/bin/Release/{TARGET FRAMEWORK}/publish` folderze.</span><span class="sxs-lookup"><span data-stu-id="dc226-124">Blazor Server: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="dc226-125">Wdróż zawartość `publish` folderu na hoście.</span><span class="sxs-lookup"><span data-stu-id="dc226-125">Deploy the contents of the `publish` folder to the host.</span></span>

<span data-ttu-id="dc226-126">Zasoby w folderze są wdrażane na serwerze sieci Web.</span><span class="sxs-lookup"><span data-stu-id="dc226-126">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="dc226-127">Wdrożenie może być procesem ręcznym lub zautomatyzowanym w zależności od używanych narzędzi programistycznych.</span><span class="sxs-lookup"><span data-stu-id="dc226-127">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="dc226-128">Ścieżka podstawowa aplikacji</span><span class="sxs-lookup"><span data-stu-id="dc226-128">App base path</span></span>

<span data-ttu-id="dc226-129">*Ścieżka podstawowa aplikacji* jest ścieżką głównego adresu URL aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dc226-129">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="dc226-130">Weź pod uwagę następujące ASP.NET Core aplikacji i aplikacji Blazor podrzędnej:</span><span class="sxs-lookup"><span data-stu-id="dc226-130">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="dc226-131">Aplikacja ASP.NET Core ma nazwę `MyApp` :</span><span class="sxs-lookup"><span data-stu-id="dc226-131">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="dc226-132">Aplikacja fizycznie znajduje się w lokalizacji `d:/MyApp` .</span><span class="sxs-lookup"><span data-stu-id="dc226-132">The app physically resides at `d:/MyApp`.</span></span>
  * <span data-ttu-id="dc226-133">Żądania są odbierane o `https://www.contoso.com/{MYAPP RESOURCE}` .</span><span class="sxs-lookup"><span data-stu-id="dc226-133">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="dc226-134">BlazorAplikacja o nazwie `CoolApp` jest aplikacją podrzędną `MyApp` :</span><span class="sxs-lookup"><span data-stu-id="dc226-134">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="dc226-135">Aplikacja podrzędna fizycznie znajduje się w `d:/MyApp/CoolApp` .</span><span class="sxs-lookup"><span data-stu-id="dc226-135">The sub-app physically resides at `d:/MyApp/CoolApp`.</span></span>
  * <span data-ttu-id="dc226-136">Żądania są odbierane o `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` .</span><span class="sxs-lookup"><span data-stu-id="dc226-136">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="dc226-137">Bez określania dodatkowej konfiguracji programu `CoolApp` aplikacja podrzędna w tym scenariuszu nie ma informacji o tym, gdzie znajduje się na serwerze.</span><span class="sxs-lookup"><span data-stu-id="dc226-137">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="dc226-138">Na przykład aplikacja nie może utworzyć prawidłowych względnych adresów URL do swoich zasobów, nie wiedząc, że znajduje się w względnej ścieżce adresu URL `/CoolApp/` .</span><span class="sxs-lookup"><span data-stu-id="dc226-138">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="dc226-139">Aby zapewnić konfigurację Blazor ścieżki podstawowej aplikacji `https://www.contoso.com/CoolApp/` , `<base>` `href` atrybut znacznika jest ustawiany na względną ścieżkę katalogu głównego w `wwwroot/index.html` pliku ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` pliku ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="dc226-139">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="dc226-140">Blazor WebAssembly (`wwwroot/index.html`):</span><span class="sxs-lookup"><span data-stu-id="dc226-140">Blazor WebAssembly (`wwwroot/index.html`):</span></span>

```html
<base href="/CoolApp/">
```

<span data-ttu-id="dc226-141">Blazor Server (`Pages/_Host.cshtml`):</span><span class="sxs-lookup"><span data-stu-id="dc226-141">Blazor Server (`Pages/_Host.cshtml`):</span></span>

```html
<base href="~/CoolApp/">
```

<span data-ttu-id="dc226-142">Blazor Server Ponadto aplikacje ustawiają ścieżkę bazową po stronie serwera, wywołując <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> w potoku żądania aplikacji `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="dc226-142">Blazor Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="dc226-143">Dostarczając względną ścieżkę adresu URL, składnik, który nie znajduje się w katalogu głównym, może konstruować adresy URL względem ścieżki katalogu głównego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dc226-143">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="dc226-144">Składniki na różnych poziomach struktury katalogów mogą tworzyć linki do innych zasobów w lokalizacji w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dc226-144">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="dc226-145">Ścieżka podstawowa aplikacji służy również do przechwytywania wybranych hiperłączy, w których miejsce `href` docelowe łącza znajduje się w obszarze URI ścieżki podstawowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dc226-145">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="dc226-146">BlazorRouter obsługuje nawigację wewnętrzną.</span><span class="sxs-lookup"><span data-stu-id="dc226-146">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="dc226-147">W wielu scenariuszach hostingu względna ścieżka URL do aplikacji jest katalogiem głównym aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dc226-147">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="dc226-148">W takich przypadkach Ścieżka bazowa względnego adresu URL aplikacji jest ukośnikiem ( `<base href="/" />` ), który jest domyślną konfiguracją dla Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dc226-148">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="dc226-149">W innych scenariuszach hostingu, takich jak strony GitHub i aplikacje podrzędne IIS, ścieżka podstawowa aplikacji musi być ustawiona na względną ścieżkę URL serwera aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dc226-149">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="dc226-150">Aby ustawić ścieżkę bazową aplikacji, zaktualizuj `<base>` tag wewnątrz `<head>` elementów tagów `Pages/_Host.cshtml` pliku ( Blazor Server ) lub `wwwroot/index.html` pliku ( Blazor WebAssembly ).</span><span class="sxs-lookup"><span data-stu-id="dc226-150">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the `Pages/_Host.cshtml` file (Blazor Server) or `wwwroot/index.html` file (Blazor WebAssembly).</span></span> <span data-ttu-id="dc226-151">Ustaw `href` wartość atrybutu na `/{RELATIVE URL PATH}/` ( Blazor WebAssembly ) lub `~/{RELATIVE URL PATH}/` ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="dc226-151">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (Blazor WebAssembly) or `~/{RELATIVE URL PATH}/` (Blazor Server).</span></span> <span data-ttu-id="dc226-152">**Końcowy ukośnik jest wymagany.**</span><span class="sxs-lookup"><span data-stu-id="dc226-152">**The trailing slash is required.**</span></span> <span data-ttu-id="dc226-153">Symbol zastępczy `{RELATIVE URL PATH}` jest pełną względną ścieżką URL aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dc226-153">The placeholder `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="dc226-154">W przypadku Blazor WebAssembly aplikacji z ścieżką względną adresu URL niegłówną (na przykład `<base href="/CoolApp/">` ) aplikacja nie będzie mogła znaleźć zasobów w *przypadku uruchamiania lokalnego*.</span><span class="sxs-lookup"><span data-stu-id="dc226-154">For a Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="dc226-155">Aby rozwiązać ten problem podczas lokalnego tworzenia i testowania, można podać podstawowy argument *ścieżki* , który jest zgodny z `href` wartością `<base>` tagu w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="dc226-155">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="dc226-156">**Nie dołączaj końcowego ukośnika.**</span><span class="sxs-lookup"><span data-stu-id="dc226-156">**Don't include a trailing slash.**</span></span> <span data-ttu-id="dc226-157">Aby przekazać argument podstawowy ścieżki podczas lokalnego uruchamiania aplikacji, należy wykonać `dotnet run` polecenie z katalogu aplikacji z `--pathbase` opcją:</span><span class="sxs-lookup"><span data-stu-id="dc226-157">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="dc226-158">W przypadku Blazor WebAssembly aplikacji ze względną ścieżką URL `/CoolApp/` ( `<base href="/CoolApp/">` ) polecenie to:</span><span class="sxs-lookup"><span data-stu-id="dc226-158">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="dc226-159">Blazor WebAssemblyAplikacja reaguje lokalnie o `http://localhost:port/CoolApp` .</span><span class="sxs-lookup"><span data-stu-id="dc226-159">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="dc226-160">**Blazor Server`MapFallbackToPage`Konfiguracja**</span><span class="sxs-lookup"><span data-stu-id="dc226-160">**Blazor Server `MapFallbackToPage` configuration**</span></span>

<span data-ttu-id="dc226-161">Przekaż następującą ścieżkę do <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> programu w programie `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="dc226-161">Pass the following path to <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> in `Startup.Configure`:</span></span>

```csharp
endpoints.MapFallbackToPage("/{RELATIVE PATH}/{**path:nonfile}");
```

<span data-ttu-id="dc226-162">Symbol zastępczy `{RELATIVE PATH}` jest ścieżką spoza katalogu głównego na serwerze.</span><span class="sxs-lookup"><span data-stu-id="dc226-162">The placeholder `{RELATIVE PATH}` is the non-root path on the server.</span></span> <span data-ttu-id="dc226-163">Na przykład, `CoolApp` jest segmentem symbolu zastępczego, jeśli nie jest to adres URL dla aplikacji `https://{HOST}:{PORT}/CoolApp/` :</span><span class="sxs-lookup"><span data-stu-id="dc226-163">For example, `CoolApp` is the placeholder segment if the non-root URL to the app is `https://{HOST}:{PORT}/CoolApp/`):</span></span>

```csharp
endpoints.MapFallbackToPage("/CoolApp/{**path:nonfile}");
```

<span data-ttu-id="dc226-164">**Hostowanie wielu Blazor WebAssembly aplikacji**</span><span class="sxs-lookup"><span data-stu-id="dc226-164">**Host multiple Blazor WebAssembly apps**</span></span>

<span data-ttu-id="dc226-165">Aby uzyskać więcej informacji na temat hostowania wielu Blazor WebAssembly aplikacji w hostowanym Blazor rozwiązaniu, zobacz <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps> .</span><span class="sxs-lookup"><span data-stu-id="dc226-165">For more information on hosting multiple Blazor WebAssembly apps in a hosted Blazor solution, see <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps>.</span></span>

## <a name="deployment"></a><span data-ttu-id="dc226-166">Wdrożenie</span><span class="sxs-lookup"><span data-stu-id="dc226-166">Deployment</span></span>

<span data-ttu-id="dc226-167">Aby uzyskać wskazówki dotyczące wdrażania, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="dc226-167">For deployment guidance, see the following topics:</span></span>

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>
