---
title: Hostuj i wdrażaj ASP.NET CoreBlazor
author: guardrex
description: Dowiedz się, jak Blazor hostować i wdrażać aplikacje.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: ddf70da29a82d462422c1bdf74ff45b92bb10b56
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434268"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="7d0b9-103">Hostuj i wdrażaj ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="7d0b9-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="7d0b9-104">[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="7d0b9-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a><span data-ttu-id="7d0b9-105">Publikowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="7d0b9-105">Publish the app</span></span>

<span data-ttu-id="7d0b9-106">Aplikacje są publikowane do wdrożenia w konfiguracji wersji.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d0b9-107">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d0b9-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="7d0b9-108">Wybierz **polecenie Buduj** > **opublikuj {APLIKACJĘ}** na pasku nawigacyjnym.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="7d0b9-109">Wybierz *cel publikowania*.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-109">Select the *publish target*.</span></span> <span data-ttu-id="7d0b9-110">Aby publikować lokalnie, wybierz **folder**.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="7d0b9-111">Zaakceptuj lokalizację **domyślną** w polu Wybierz folder lub określ inną lokalizację.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="7d0b9-112">Wybierz przycisk **Publikuj**.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-112">Select the **Publish** button.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="7d0b9-113">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="7d0b9-113">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="7d0b9-114">Użyj polecenia [publikowania dotnet,](/dotnet/core/tools/dotnet-publish) aby opublikować aplikację z konfiguracją wersji:</span><span class="sxs-lookup"><span data-stu-id="7d0b9-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="7d0b9-115">Publikowanie aplikacji wyzwala [przywracanie](/dotnet/core/tools/dotnet-restore) zależności projektu i [tworzy](/dotnet/core/tools/dotnet-build) projekt przed utworzeniem zasobów do wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="7d0b9-116">W ramach procesu kompilacji nieużywane metody i zestawy są usuwane w celu zmniejszenia rozmiaru pobierania aplikacji i czasu ładowania.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="7d0b9-117">Publikuj lokalizacje:</span><span class="sxs-lookup"><span data-stu-id="7d0b9-117">Publish locations:</span></span>

* Blazor<span data-ttu-id="7d0b9-118">WebAssembly (WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="7d0b9-118"> WebAssembly</span></span>
  * <span data-ttu-id="7d0b9-119">&ndash; Samodzielny Aplikacja jest publikowana w folderze */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="7d0b9-119">Standalone &ndash; The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span> <span data-ttu-id="7d0b9-120">Aby wdrożyć aplikację jako witrynę statyczną, skopiuj zawartość folderu *wwwroot* do statycznego hosta witryny.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-120">To deploy the app as a static site, copy the contents of the *wwwroot* folder to the static site host.</span></span>
  * <span data-ttu-id="7d0b9-121">Hosted &ndash; Aplikacja Blazor WebAssembly klienta jest publikowana w folderze */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* aplikacji serwera wraz z innymi statycznymi zasobami sieci Web aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-121">Hosted &ndash; The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="7d0b9-122">Wdrażanie zawartości folderu *publikowania* na hoście.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-122">Deploy the contents of the *publish* folder to the host.</span></span>
* Blazor<span data-ttu-id="7d0b9-123">Serwer &ndash; Aplikacja jest publikowana w folderze */bin/Release/{TARGET FRAMEWORK}/publish.*</span><span class="sxs-lookup"><span data-stu-id="7d0b9-123"> Server &ndash; The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="7d0b9-124">Wdrażanie zawartości folderu *publikowania* na hoście.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-124">Deploy the contents of the *publish* folder to the host.</span></span>

<span data-ttu-id="7d0b9-125">Zasoby w folderze są wdrażane na serwerze sieci web.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-125">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="7d0b9-126">Wdrożenie może być procesem ręcznym lub zautomatyzowanym w zależności od używanych narzędzi programistycznych.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-126">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="7d0b9-127">Ścieżka podstawowa aplikacji</span><span class="sxs-lookup"><span data-stu-id="7d0b9-127">App base path</span></span>

<span data-ttu-id="7d0b9-128">*Ścieżka podstawowa aplikacji* to główna ścieżka adresu URL aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-128">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="7d0b9-129">Należy wziąć pod uwagę następujące Blazor ASP.NET aplikacji Core i podkoszuli:</span><span class="sxs-lookup"><span data-stu-id="7d0b9-129">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="7d0b9-130">Nazwa aplikacji ASP.NET `MyApp`Core:</span><span class="sxs-lookup"><span data-stu-id="7d0b9-130">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="7d0b9-131">Aplikacja fizycznie znajduje się w *d:/MyApp*.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-131">The app physically resides at *d:/MyApp*.</span></span>
  * <span data-ttu-id="7d0b9-132">Wnioski są `https://www.contoso.com/{MYAPP RESOURCE}`odbierane w punkcie .</span><span class="sxs-lookup"><span data-stu-id="7d0b9-132">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="7d0b9-133">Nazwa Blazor aplikacji `CoolApp` to podnauka: `MyApp`</span><span class="sxs-lookup"><span data-stu-id="7d0b9-133">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="7d0b9-134">Podaplika fizycznie znajduje się w *d:/MyApp/CoolApp*.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-134">The sub-app physically resides at *d:/MyApp/CoolApp*.</span></span>
  * <span data-ttu-id="7d0b9-135">Wnioski są `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`odbierane w punkcie .</span><span class="sxs-lookup"><span data-stu-id="7d0b9-135">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="7d0b9-136">Bez określania dodatkowej konfiguracji dla `CoolApp`aplikacji , podnauka w tym scenariuszu nie ma wiedzy o tym, gdzie znajduje się na serwerze.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-136">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="7d0b9-137">Na przykład aplikacja nie może tworzyć poprawnych względnych adresów URL do swoich zasobów, nie wiedząc, że znajduje się przy względnej ścieżce `/CoolApp/`adresu URL .</span><span class="sxs-lookup"><span data-stu-id="7d0b9-137">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="7d0b9-138">Aby Blazor zapewnić konfigurację ścieżki podstawowej `https://www.contoso.com/CoolApp/`aplikacji, atrybut `<base>` tagu `href` jest ustawiony na względną ścieżkę główną w plikuBlazor *Pages/_Host.cshtml* (Serwer) lub *wwwroot/index.html* pliku (WebAssembly):Blazor</span><span class="sxs-lookup"><span data-stu-id="7d0b9-138">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly):</span></span>

```html
<base href="/CoolApp/">
```

Blazor<span data-ttu-id="7d0b9-139">Aplikacje serwera dodatkowo ustawić ścieżkę bazową po stronie serwera, wywołując <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> w potoku żądania `Startup.Configure`aplikacji:</span><span class="sxs-lookup"><span data-stu-id="7d0b9-139"> Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="7d0b9-140">Udostępniając ścieżkę względnego adresu URL, składnik, który nie znajduje się w katalogu głównym, może tworzyć adresy URL względem ścieżki głównej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-140">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="7d0b9-141">Składniki na różnych poziomach struktury katalogów można tworzyć łącza do innych zasobów w lokalizacjach w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-141">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="7d0b9-142">Ścieżka podstawowa aplikacji służy również do przechwytywania `href` wybranych hiperłączy, gdzie miejsce docelowe łącza znajduje się w przestrzeni URI ścieżki podstawowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-142">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="7d0b9-143">Router Blazor obsługuje nawigację wewnętrzną.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-143">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="7d0b9-144">W wielu scenariuszach hostingu względna ścieżka adresu URL do aplikacji jest głównym źródłem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-144">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="7d0b9-145">W takich przypadkach względna ścieżka bazowa adresu URL`<base href="/" />`aplikacji jest ukośnikiem do przodu ( ), który jest domyślną konfiguracją Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-145">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="7d0b9-146">W innych scenariuszach hostingu, takich jak Strony GitHub i aplikacje podrzędne usług IIS, ścieżka podstawowa aplikacji musi być ustawiona na ścieżkę względnego adresu URL serwera aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-146">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="7d0b9-147">Aby ustawić ścieżkę podstawową aplikacji, `<base>` zaktualizuj znacznik w elementach `<head>` tagu pliku *Pages/_Host.cshtml* (Serwer)Blazor lub *wwwroot/index.html* (WebAssembly).Blazor</span><span class="sxs-lookup"><span data-stu-id="7d0b9-147">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly).</span></span> <span data-ttu-id="7d0b9-148">Ustaw `href` wartość atrybutu `/{RELATIVE URL PATH}/` (wymagany jest ukośnik `{RELATIVE URL PATH}` końcowy), gdzie jest pełna ścieżka względnego adresu URL aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-148">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="7d0b9-149">W Blazor przypadku aplikacji WebAssembly ze ścieżką względnego `<base href="/CoolApp/">`adresu URL niebędącego głównym (na przykład), aplikacja nie może znaleźć swoich zasobów *po uruchomieniu lokalnie*.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-149">For an Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="7d0b9-150">Aby rozwiązać ten problem podczas lokalnego rozwoju i testowania, `href` można `<base>` podać argument *podstawowy ścieżki,* który odpowiada wartości tagu w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-150">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="7d0b9-151">Nie dołączaj końcowego ukośnika.</span><span class="sxs-lookup"><span data-stu-id="7d0b9-151">Don't include a trailing slash.</span></span> <span data-ttu-id="7d0b9-152">Aby przekazać argument podstawowy ścieżki podczas lokalnego `dotnet run` uruchamiania aplikacji, wykonaj polecenie `--pathbase` z katalogu aplikacji z opcją:</span><span class="sxs-lookup"><span data-stu-id="7d0b9-152">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="7d0b9-153">W Blazor przypadku aplikacji WebAssembly ze `/CoolApp/` ścieżką względnego adresu URL (`<base href="/CoolApp/">`), polecenie brzmi:</span><span class="sxs-lookup"><span data-stu-id="7d0b9-153">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="7d0b9-154">Aplikacja Blazor WebAssembly odpowiada lokalnie `http://localhost:port/CoolApp`na stronie .</span><span class="sxs-lookup"><span data-stu-id="7d0b9-154">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="7d0b9-155">Wdrożenie</span><span class="sxs-lookup"><span data-stu-id="7d0b9-155">Deployment</span></span>

<span data-ttu-id="7d0b9-156">Aby uzyskać wskazówki dotyczące wdrażania, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="7d0b9-156">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
