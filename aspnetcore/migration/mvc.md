---
title: Dowiedz się, jak przeprowadzić migrację z ASP.NET MVC do ASP.NET Core MVC
author: wadepickett
description: Dowiedz się, jak rozpocząć Migrowanie projektu ASP.NET MVC do ASP.NET Core MVC.
ms.author: wpickett
ms.date: 06/18/2020
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
uid: migration/mvc
ms.openlocfilehash: 17f2a2532c58c3796835328260231d63f8fb2e40
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015052"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="4ae6d-103">Migrowanie z ASP.NET MVC do ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="4ae6d-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4ae6d-104">W tym artykule pokazano, jak rozpocząć Migrowanie projektu ASP.NET MVC do [ASP.NET Core MVC](xref:mvc/overview).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-104">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview).</span></span> <span data-ttu-id="4ae6d-105">W procesie są wyróżniane powiązane zmiany z ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-105">In the process, it highlights related changes from ASP.NET MVC.</span></span>

<span data-ttu-id="4ae6d-106">Migrowanie z ASP.NET MVC jest procesem wieloetapowym.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-106">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="4ae6d-107">W tym artykule omówiono następujące zagadnienia:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-107">This article covers:</span></span>

* <span data-ttu-id="4ae6d-108">Początkowa konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-108">Initial setup.</span></span>
* <span data-ttu-id="4ae6d-109">Podstawowe kontrolery i widoki.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-109">Basic controllers and views.</span></span>
* <span data-ttu-id="4ae6d-110">Zawartość statyczna.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-110">Static content.</span></span>
* <span data-ttu-id="4ae6d-111">Zależności po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-111">Client-side dependencies.</span></span>

<span data-ttu-id="4ae6d-112">Aby przeprowadzić migrację konfiguracji i Identity kodu, zobacz [Migrowanie konfiguracji do ASP.NET Core](xref:migration/configuration) i [migrowanie uwierzytelniania i Identity ASP.NET Core](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-112">For migrating configuration and Identity code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4ae6d-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="4ae6d-113">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="4ae6d-114">Tworzenie projektu Starter ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="4ae6d-114">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="4ae6d-115">Utwórz przykładowy projekt ASP.NET MVC w programie Visual Studio do migracji:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-115">Create an example ASP.NET MVC project in Visual Studio to migrate:</span></span>

1. <span data-ttu-id="4ae6d-116">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-116">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="4ae6d-117">Wybierz pozycję **aplikacja sieci Web ASP.NET (.NET Framework)** , a następnie wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-117">Select **ASP.NET Web Application (.NET Framework)** and then select **Next**.</span></span>
1. <span data-ttu-id="4ae6d-118">Nazwij projekt *WebApp1* , aby przestrzeń nazw była zgodna z projektem ASP.NET Core utworzonym w następnym kroku.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-118">Name the project *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span> <span data-ttu-id="4ae6d-119">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-119">Select **Create**.</span></span>
1. <span data-ttu-id="4ae6d-120">Wybierz pozycję **MVC**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-120">Select **MVC**, and then select **Create**.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="4ae6d-121">Tworzenie projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4ae6d-121">Create the ASP.NET Core project</span></span>

<span data-ttu-id="4ae6d-122">Utwórz nowe rozwiązanie z nowym projektem ASP.NET Core, aby przeprowadzić migrację do:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-122">Create a new solution with a new ASP.NET Core project to migrate to:</span></span>

1. <span data-ttu-id="4ae6d-123">Uruchom drugie wystąpienie programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-123">Launch a second instance of Visual Studio.</span></span>
1. <span data-ttu-id="4ae6d-124">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-124">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="4ae6d-125">Wybierz pozycję **ASP.NET Web Core Web Application** , a następnie wybierz przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-125">Select **ASP.NET Web Core Web Application** and then select **Next**.</span></span>
1. <span data-ttu-id="4ae6d-126">W oknie dialogowym **Konfigurowanie nowego projektu** Nazwij projekt *WebApp1*.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-126">In the **Configure your new project** dialog, Name the project *WebApp1*.</span></span>
1. <span data-ttu-id="4ae6d-127">Ustaw lokalizację na inny katalog niż poprzedni projekt, aby użyć tej samej nazwy projektu.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-127">Set the location to a different directory than the previous project to use the same project name.</span></span> <span data-ttu-id="4ae6d-128">Korzystanie z tej samej przestrzeni nazw ułatwia kopiowanie kodu między dwoma projektami.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-128">Using the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="4ae6d-129">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-129">Select **Create**.</span></span>
1. <span data-ttu-id="4ae6d-130">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="4ae6d-131">Wybierz szablon projektu **aplikacja sieci Web (Model-View-Controller)** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-131">Select the **Web Application (Model-View-Controller)** project template, and select **Create**.</span></span>

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a><span data-ttu-id="4ae6d-132">Skonfiguruj witrynę ASP.NET Core, aby używać MVC</span><span class="sxs-lookup"><span data-stu-id="4ae6d-132">Configure the ASP.NET Core site to use MVC</span></span>

<span data-ttu-id="4ae6d-133">W ASP.NET Core 3,0 i nowszych projektach .NET Framework nie jest już obsługiwaną platformą docelową.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-133">In ASP.NET Core 3.0 and later projects, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="4ae6d-134">Projekt musi być przeznaczony dla platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-134">Your project must target .NET Core.</span></span> <span data-ttu-id="4ae6d-135">ASP.NET Core Shared Framework, która obejmuje MVC, jest częścią instalacji środowiska uruchomieniowego platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-135">The ASP.NET Core shared framework, which includes MVC, is part of the .NET Core runtime installation.</span></span> <span data-ttu-id="4ae6d-136">W przypadku korzystania z `Microsoft.NET.Sdk.Web` zestawu SDK w pliku projektu automatycznie występuje odwołanie do udostępnionej struktury:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-136">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="4ae6d-137">Aby uzyskać więcej informacji, zobacz temat [informacje dotyczące platformy](xref:migration/22-to-30#framework-reference).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-137">For more information, see [Framework reference](xref:migration/22-to-30#framework-reference).</span></span>

<span data-ttu-id="4ae6d-138">W ASP.NET Core `Startup` Klasa:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-138">In ASP.NET Core, the `Startup` class:</span></span>

* <span data-ttu-id="4ae6d-139">Zastępuje *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-139">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="4ae6d-140">Obsługuje wszystkie zadania uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="4ae6d-141">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-141">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="4ae6d-142">W projekcie ASP.NET Core Otwórz plik *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="4ae6d-142">In the ASP.NET Core project, open the *Startup.cs* file:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

<span data-ttu-id="4ae6d-143">Aplikacje ASP.NET Core muszą przystąpić do struktur funkcji za pomocą oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-143">ASP.NET Core apps must opt in to framework features with middleware.</span></span> <span data-ttu-id="4ae6d-144">Poprzedni kod wygenerowany przez szablon dodaje następujące usługi i oprogramowanie pośredniczące:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-144">The previous template-generated code adds the following services and middleware:</span></span>

* <span data-ttu-id="4ae6d-145"><xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A>Metoda rozszerzenia rejestruje obsługę usług MVC dla kontrolerów, funkcji związanych z interfejsem API i widoków.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-145">The <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> extension method registers MVC service support for controllers, API-related features, and views.</span></span> <span data-ttu-id="4ae6d-146">Aby uzyskać więcej informacji na temat opcji rejestracji usługi MVC, zobacz temat [Rejestrowanie usługi MVC](xref:migration/22-to-30#mvc-service-registration)</span><span class="sxs-lookup"><span data-stu-id="4ae6d-146">For more information on MVC service registration options, see [MVC service registration](xref:migration/22-to-30#mvc-service-registration)</span></span>
* <span data-ttu-id="4ae6d-147"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>Metoda rozszerzenia dodaje procedurę obsługi pliku statycznego `Microsoft.AspNetCore.StaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-147">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> extension method adds the static file handler `Microsoft.AspNetCore.StaticFiles`.</span></span> <span data-ttu-id="4ae6d-148">`UseStaticFiles`Metoda rozszerzenia musi być wywoływana przed `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-148">The `UseStaticFiles` extension method must be called before `UseRouting`.</span></span> <span data-ttu-id="4ae6d-149">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-149">For more information, see <xref:fundamentals/static-files>.</span></span>
* <span data-ttu-id="4ae6d-150"><xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>Metoda rozszerzenia dodaje Routing.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-150">The <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> extension method adds routing.</span></span> <span data-ttu-id="4ae6d-151">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-151">For more information, see <xref:fundamentals/routing>.</span></span>

<span data-ttu-id="4ae6d-152">Ta istniejąca konfiguracja zawiera informacje potrzebne do migracji przykładowego projektu MVC ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-152">This existing configuration includes what is needed to migrate the example ASP.NET MVC project.</span></span> <span data-ttu-id="4ae6d-153">Aby uzyskać więcej informacji o ASP.NET Core opcjach oprogramowania pośredniczącego, zobacz <xref:fundamentals/startup> .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-153">For more information on ASP.NET Core middleware options, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-controllers-and-views"></a><span data-ttu-id="4ae6d-154">Migrowanie kontrolerów i widoków</span><span class="sxs-lookup"><span data-stu-id="4ae6d-154">Migrate controllers and views</span></span>

<span data-ttu-id="4ae6d-155">W ASP.NET Core projekcie zostanie dodana nowa pusta Klasa kontrolera i Klasa widoku, które będą służyć jako symbole zastępcze przy użyciu takich samych nazw, jak kontroler i klasy widoków w dowolnym projekcie ASP.NET MVC do migracji.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-155">In the ASP.NET Core project, a new empty controller class and view class would be added to serve as placeholders using the same names as the controller and view classes in any ASP.NET MVC project to migrate from.</span></span>

<span data-ttu-id="4ae6d-156">Projekt ASP.NET Core *WebApp1* zawiera już minimalny przykładowy kontroler i widok o takiej samej nazwie jak projekt ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-156">The ASP.NET Core *WebApp1* project already includes a minimal example controller and view by the same name as the ASP.NET MVC project.</span></span> <span data-ttu-id="4ae6d-157">Dzięki temu będą one służyć jako symbole zastępcze dla kontrolera ASP.NET MVC i widoków do migracji z projektu ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-157">So those will serve as placeholders for the ASP.NET MVC controller and views to be migrated from the ASP.NET MVC *WebApp1* project.</span></span>

1. <span data-ttu-id="4ae6d-158">Skopiuj metody z ASP.NET MVC, `HomeController` Aby zastąpić nowe `HomeController` metody ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-158">Copy the methods from the ASP.NET MVC `HomeController` to replace the new ASP.NET Core `HomeController` methods.</span></span> <span data-ttu-id="4ae6d-159">Nie trzeba zmieniać zwracanego typu metod akcji.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-159">There's no need to change the return type of the action methods.</span></span> <span data-ttu-id="4ae6d-160">Zwracany typ metody akcji kontrolera wbudowanego szablonu MVC ASP.NET ma wartość [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); w ASP.NET Core MVC, zamiast tego zwracają metody akcji `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-160">The ASP.NET MVC built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="4ae6d-161">`ActionResult`implementuje `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-161">`ActionResult` implements `IActionResult`.</span></span>
1. <span data-ttu-id="4ae6d-162">W projekcie ASP.NET Core kliknij prawym przyciskiem myszy *widok/katalog macierzysty* , wybierz polecenie **Dodaj** > **istniejący element**.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-162">In the ASP.NET Core project, right-click the *Views/Home* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="4ae6d-163">W oknie dialogowym **Dodaj istniejący element** przejdź do *widoku/katalogu macierzystego* projektu ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-163">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Home* directory.</span></span>
1. <span data-ttu-id="4ae6d-164">Zaznacz pliki widoku *Informacje o. cshtml*, *Contact. cshtml*i *index. cshtml* Razor , a następnie wybierz pozycję **Dodaj**, zastępując istniejące pliki.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-164">Select the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files, then select **Add**, replacing the existing files.</span></span>

<span data-ttu-id="4ae6d-165">Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/actions> i <xref:mvc/views/overview>.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-165">For more information, see <xref:mvc/controllers/actions> and <xref:mvc/views/overview>.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="4ae6d-166">Przetestuj każdą metodę</span><span class="sxs-lookup"><span data-stu-id="4ae6d-166">Test each method</span></span>

<span data-ttu-id="4ae6d-167">Każdy punkt końcowy kontrolera może być testowany, ale układ i style są omówione w dalszej części dokumentu.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-167">Each controller endpoint can be tested, however, layout and styles are covered later in the document.</span></span>

1. <span data-ttu-id="4ae6d-168">Uruchom aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-168">Run the ASP.NET Core app.</span></span>
1. <span data-ttu-id="4ae6d-169">Wywołaj renderowane widoki z przeglądarki w działającej aplikacji ASP.NET Core, zastępując bieżący numer portu numerem portu używanym w ASP.NET Core projekcie.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-169">Invoke the rendered views from the browser on the running ASP.NET Core app by replacing the current port number with the port number used in the ASP.NET Core project.</span></span> <span data-ttu-id="4ae6d-170">Na przykład `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-170">For example, `https://localhost:44375/home/about`.</span></span>

## <a name="migrate-static-content"></a><span data-ttu-id="4ae6d-171">Migrowanie zawartości statycznej</span><span class="sxs-lookup"><span data-stu-id="4ae6d-171">Migrate static content</span></span>

<span data-ttu-id="4ae6d-172">W ASP.NET MVC 5 i starszych zawartość statyczna była hostowana z katalogu głównego projektu sieci Web i była mieszana przy użyciu plików po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-172">In ASP.NET MVC 5 and earlier, static content was hosted from the web project's root directory and was intermixed with server-side files.</span></span> <span data-ttu-id="4ae6d-173">W ASP.NET Core pliki statyczne są przechowywane w katalogu [głównym sieci Web](xref:fundamentals/index#web-root) projektu.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-173">In ASP.NET Core, static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="4ae6d-174">Domyślnym katalogiem jest *{Content root}/wwwroot*, ale można go zmienić.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-174">The default directory is *{content root}/wwwroot*, but it can be changed.</span></span> <span data-ttu-id="4ae6d-175">Aby uzyskać więcej informacji, zobacz [pliki statyczne w ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-175">For more information, see [Static files in ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span></span>

<span data-ttu-id="4ae6d-176">Skopiuj zawartość statyczną z projektu ASP.NET MVC *WebApp1* do katalogu *wwwroot* w projekcie ASP.NET Core *WebApp1* :</span><span class="sxs-lookup"><span data-stu-id="4ae6d-176">Copy the static content from the ASP.NET MVC *WebApp1* project to the *wwwroot* directory in the ASP.NET Core *WebApp1* project:</span></span>

1. <span data-ttu-id="4ae6d-177">W projekcie ASP.NET Core kliknij prawym przyciskiem myszy katalog *wwwroot* , wybierz pozycję **Dodaj** > **istniejący element**.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-177">In the ASP.NET Core project, right-click the *wwwroot* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="4ae6d-178">W oknie dialogowym **Dodaj istniejący element** przejdź do projektu ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-178">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project.</span></span>
1. <span data-ttu-id="4ae6d-179">Wybierz plik *favicon. ico* , a następnie wybierz pozycję **Dodaj**, zastępując istniejący plik.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-179">Select the *favicon.ico* file, then select **Add**, replacing the existing file.</span></span>

## <a name="migrate-the-layout-files"></a><span data-ttu-id="4ae6d-180">Migrowanie plików układu</span><span class="sxs-lookup"><span data-stu-id="4ae6d-180">Migrate the layout files</span></span>

<span data-ttu-id="4ae6d-181">Skopiuj pliki układu projektu MVC ASP.NET do projektu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-181">Copy the ASP.NET MVC project layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="4ae6d-182">W projekcie ASP.NET Core kliknij prawym przyciskiem myszy katalog *widoki* , a następnie wybierz pozycję **Dodaj** > **istniejący element**.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-182">In the ASP.NET Core project, right-click the *Views* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="4ae6d-183">W oknie dialogowym **Dodaj istniejący element** przejdź do katalogu *widoków* projektu ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-183">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views* directory.</span></span>
1. <span data-ttu-id="4ae6d-184">Wybierz plik *_ViewStart. cshtml* , a następnie wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-184">Select the *_ViewStart.cshtml* file then select **Add**.</span></span>

<span data-ttu-id="4ae6d-185">Skopiuj pliki wspólnych układów projektu ASP.NET MVC do projektu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-185">Copy the ASP.NET MVC project shared layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="4ae6d-186">W projekcie ASP.NET Core kliknij prawym przyciskiem myszy *Widok/udostępniony* katalog, wybierz polecenie **Dodaj** > **istniejący element**.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-186">In the ASP.NET Core project, right-click the *Views/Shared* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="4ae6d-187">W oknie dialogowym **Dodaj istniejący element** przejdź do *widoku/udostępnionego* katalogu projektu ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-187">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Shared* directory.</span></span>
1. <span data-ttu-id="4ae6d-188">Wybierz plik *_Layout. cshtml* , a następnie wybierz pozycję **Dodaj**, zastępując istniejący plik.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-188">Select the *_Layout.cshtml* file, then select **Add**, replacing the existing file.</span></span>

<span data-ttu-id="4ae6d-189">W projekcie ASP.NET Core Otwórz plik *_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-189">In the ASP.NET Core project, open the *_Layout.cshtml* file.</span></span> <span data-ttu-id="4ae6d-190">Wprowadź następujące zmiany, aby dopasować do pokazanego poniżej kodu:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-190">Make the following changes to match the completed code shown below:</span></span>

<span data-ttu-id="4ae6d-191">Zaktualizuj dołączenie do początkowego kodu CSS, aby pasowało do pożądanego poniższego szablonu:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-191">Update the Bootstrap CSS inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="4ae6d-192">Zamień na `@Styles.Render("~/Content/css")` `<link>` element, który ma ładować *Bootstrap. css* (patrz poniżej).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-192">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>
1. <span data-ttu-id="4ae6d-193">Usuń `@Scripts.Render("~/bundles/modernizr")` .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-193">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

<span data-ttu-id="4ae6d-194">Zakończony znacznik zastępczy dla dołączania do ładowania początkowego:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-194">The completed replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="4ae6d-195">Zaktualizuj dołączenie jQuery i Bootstrap w języku JavaScript, aby dopasować do poniższego kodu:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-195">Update the jQuery and Bootstrap JavaScript inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="4ae6d-196">Zamień `@Scripts.Render("~/bundles/jquery")` na `<script>` element (patrz poniżej).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-196">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>
1. <span data-ttu-id="4ae6d-197">Zamień `@Scripts.Render("~/bundles/bootstrap")` na `<script>` element (patrz poniżej).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-197">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="4ae6d-198">Zakończono dołączenie znacznika wymiany dla platformy jQuery i uruchomienia skryptu uruchomieniowego w języku JavaScript:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-198">The completed replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="4ae6d-199">Zaktualizowany plik *_Layout. cshtml* jest przedstawiony poniżej:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-199">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

<span data-ttu-id="4ae6d-200">Wyświetl witrynę w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-200">View the site in the browser.</span></span> <span data-ttu-id="4ae6d-201">Powinien on być renderowany przy użyciu oczekiwanych stylów.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-201">It should render with the expected styles in place.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="4ae6d-202">Konfigurowanie grupowania i minifikacja</span><span class="sxs-lookup"><span data-stu-id="4ae6d-202">Configure bundling and minification</span></span>

<span data-ttu-id="4ae6d-203">ASP.NET Core jest zgodny z kilkoma minifikacjaami i rozwiązaniami typu open source, takimi jak [Weboptimize](https://github.com/ligershark/WebOptimizer) i innymi podobnymi bibliotekami.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-203">ASP.NET Core is compatible with several open-source bundling and minification solutions such as [WebOptimizer](https://github.com/ligershark/WebOptimizer) and other similar libraries.</span></span> <span data-ttu-id="4ae6d-204">ASP.NET Core nie zapewnia natywnego tworzenia i minifikacja rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-204">ASP.NET Core doesn't provide a native bundling and minification solution.</span></span> <span data-ttu-id="4ae6d-205">Aby uzyskać informacje na temat konfigurowania grupowania i minifikacja, zobacz Tworzenie [i minifikacja](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-205">For information on configuring bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="4ae6d-206">Rozwiązywanie błędów HTTP 500</span><span class="sxs-lookup"><span data-stu-id="4ae6d-206">Solve HTTP 500 errors</span></span>

<span data-ttu-id="4ae6d-207">Istnieje wiele problemów, które mogą spowodować wyświetlenie komunikatu o błędzie HTTP 500, który nie zawiera żadnych informacji na temat źródła problemu.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-207">There are many problems that can cause an HTTP 500 error message that contains no information on the source of the problem.</span></span> <span data-ttu-id="4ae6d-208">Na przykład, jeśli plik *views/_ViewImports. cshtml* zawiera przestrzeń nazw, która nie istnieje w projekcie, generowany jest błąd HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-208">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, an HTTP 500 error is generated.</span></span> <span data-ttu-id="4ae6d-209">Domyślnie w aplikacjach ASP.NET Core `UseDeveloperExceptionPage` rozszerzenie jest dodawane do `IApplicationBuilder` i wykonywane, gdy środowisko jest *opracowywane*.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-209">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the environment is *Development*.</span></span> <span data-ttu-id="4ae6d-210">Jest to szczegółowo opisany w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-210">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

<span data-ttu-id="4ae6d-211">ASP.NET Core konwertuje Nieobsłużone wyjątki na odpowiedzi na błędy HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-211">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="4ae6d-212">Zwykle szczegóły błędu nie są uwzględniane w tych odpowiedziach, aby zapobiec ujawnieniu potencjalnie poufnych informacji o serwerze.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-212">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="4ae6d-213">Aby uzyskać więcej informacji, zobacz [stronę wyjątków dla deweloperów](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-213">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="next-steps"></a><span data-ttu-id="4ae6d-214">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="4ae6d-214">Next steps</span></span>

* <xref:migration/identity>

## <a name="additional-resources"></a><span data-ttu-id="4ae6d-215">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="4ae6d-215">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="4ae6d-216">W tym artykule pokazano, jak rozpocząć Migrowanie projektu ASP.NET MVC do [ASP.NET Core MVC](xref:mvc/overview) 2,2.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-216">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.2.</span></span> <span data-ttu-id="4ae6d-217">W procesie wyróżniamy wiele elementów, które uległy zmianie z ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-217">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="4ae6d-218">Migrowanie z ASP.NET MVC jest procesem wieloetapowym.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-218">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="4ae6d-219">W tym artykule omówiono następujące zagadnienia:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-219">This article covers:</span></span>

* <span data-ttu-id="4ae6d-220">Początkowa konfiguracja</span><span class="sxs-lookup"><span data-stu-id="4ae6d-220">Initial setup</span></span>
* <span data-ttu-id="4ae6d-221">Podstawowe kontrolery i widoki</span><span class="sxs-lookup"><span data-stu-id="4ae6d-221">Basic controllers and views</span></span>
* <span data-ttu-id="4ae6d-222">Zawartość statyczna</span><span class="sxs-lookup"><span data-stu-id="4ae6d-222">Static content</span></span>
* <span data-ttu-id="4ae6d-223">Zależności po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-223">Client-side dependencies.</span></span>

<span data-ttu-id="4ae6d-224">Aby migrować konfigurację i Identity kod, zobacz <xref:migration/configuration> i <xref:migration/identity> .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-224">For migrating configuration and Identity code, see <xref:migration/configuration> and <xref:migration/identity>.</span></span>

> [!NOTE]
> <span data-ttu-id="4ae6d-225">Numery wersji w przykładach mogą nie być aktualne, należy odpowiednio zaktualizować projekty.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-225">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="4ae6d-226">Tworzenie projektu Starter ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="4ae6d-226">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="4ae6d-227">Aby zademonstrować uaktualnienie, zaczniemy od utworzenia aplikacji ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-227">To demonstrate the upgrade, we'll start by creating an ASP.NET MVC app.</span></span> <span data-ttu-id="4ae6d-228">Utwórz go przy użyciu nazwy *WebApp1* , aby przestrzeń nazw była zgodna z projektem ASP.NET Core utworzonym w następnym kroku.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-228">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Okno dialogowe nowego projektu programu Visual Studio](mvc/_static/new-project.png)

![Okno dialogowe Nowa aplikacja sieci Web: szablon projektu MVC wybrany w panelu szablony ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="4ae6d-231">*Opcjonalne:* Zmień nazwę rozwiązania z *WebApp1* na *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-231">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="4ae6d-232">Program Visual Studio wyświetla nową nazwę rozwiązania (*Mvc5*), która ułatwia poinformowanie tego projektu z następnego projektu.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-232">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="4ae6d-233">Tworzenie projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4ae6d-233">Create the ASP.NET Core project</span></span>

<span data-ttu-id="4ae6d-234">Utwórz nową *pustą* aplikację sieci Web ASP.NET Core o takiej samej nazwie jak w poprzednim projekcie (*WebApp1*), tak aby przestrzenie nazw w obu projektach były zgodne.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-234">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="4ae6d-235">Posiadanie tego samego obszaru nazw ułatwia kopiowanie kodu między tymi dwoma projektami.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-235">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="4ae6d-236">Utwórz ten projekt w innym katalogu niż poprzedni projekt, aby użyć tej samej nazwy.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-236">Create this project in a different directory than the previous project to use the same name.</span></span>

![Okno dialogowe Nowy projekt](mvc/_static/new_core.png)

![Nowe okno dialogowe aplikacji sieci Web ASP.NET: pusty szablon projektu wybrany w panelu szablony ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="4ae6d-239">*Opcjonalne:* Utwórz nową aplikację ASP.NET Core przy użyciu szablonu projektu *aplikacji sieci Web* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-239">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="4ae6d-240">Nazwij projekt *WebApp1*i wybierz opcję uwierzytelniania **poszczególnych kont użytkowników**.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-240">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="4ae6d-241">Zmień nazwę tej aplikacji na *FullAspNetCore*.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-241">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="4ae6d-242">Utworzenie tego projektu powoduje zaoszczędzenie czasu w konwersji.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-242">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="4ae6d-243">Wynik końcowy może być wyświetlany w kodzie generowanym przez szablon, kod może być kopiowany do projektu konwersji lub porównywany z projektem generowanym przez szablon.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-243">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="4ae6d-244">Konfigurowanie lokacji do korzystania z MVC</span><span class="sxs-lookup"><span data-stu-id="4ae6d-244">Configure the site to use MVC</span></span>

* <span data-ttu-id="4ae6d-245">W przypadku określania platformy .NET Core jest przywoływany domyślny [pakiet Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-245">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="4ae6d-246">Ten pakiet zawiera pakiety powszechnie używane przez aplikacje MVC.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-246">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="4ae6d-247">W przypadku .NET Framework określania wartości docelowej odwołania do pakietów muszą być wyszczególnione pojedynczo w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-247">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="4ae6d-248">`Microsoft.AspNetCore.Mvc`jest platformą ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-248">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="4ae6d-249">`Microsoft.AspNetCore.StaticFiles`jest programem obsługi plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-249">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="4ae6d-250">Aplikacje ASP.NET Core jawnie wybierają oprogramowanie pośredniczące, na przykład w celu obsługi plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-250">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="4ae6d-251">Aby uzyskać więcej informacji, zobacz [pliki statyczne](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-251">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="4ae6d-252">Otwórz plik *Startup.cs* i Zmień kod w taki sposób, aby był zgodny z następującymi:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-252">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="4ae6d-253"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>Metoda rozszerzenia dodaje procedurę obsługi pliku statycznego.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-253">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="4ae6d-254">Aby uzyskać więcej informacji, zobacz Uruchamianie i [Routing](xref:fundamentals/routing) [aplikacji](xref:fundamentals/startup) .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-254">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="4ae6d-255">Dodawanie kontrolera i widoku</span><span class="sxs-lookup"><span data-stu-id="4ae6d-255">Add a controller and view</span></span>

<span data-ttu-id="4ae6d-256">W tej sekcji zostanie dodany minimalny kontroler i widok służący jako symbole zastępcze dla kontrolera ASP.NET MVC i widoków migrowanych w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-256">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="4ae6d-257">Dodaj katalog *kontrolerów* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-257">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="4ae6d-258">Dodaj **klasę kontrolera** o nazwie *HomeController.cs* do katalogu *controllers* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-258">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![Okno dialogowe Dodawanie nowego elementu](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="4ae6d-260">Dodaj katalog *widoków* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-260">Add a *Views* directory.</span></span>

* <span data-ttu-id="4ae6d-261">Dodaj *widok/katalog macierzysty* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-261">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="4ae6d-262">Dodaj \*\* Razor Widok\*\* o nazwie *index. cshtml* do *widoków/katalogu macierzystego* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-262">Add a **Razor View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![Okno dialogowe Dodawanie nowego elementu](mvc/_static/view.png)

<span data-ttu-id="4ae6d-264">Poniżej przedstawiono strukturę projektu:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-264">The project structure is shown below:</span></span>

![Eksplorator rozwiązań pokazywania plików i katalogów WebApp1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="4ae6d-266">Zastąp zawartość pliku *viewss/Home/index. cshtml* następującym znacznikiem:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-266">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="4ae6d-267">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-267">Run the app.</span></span>

![Aplikacja internetowa otwarta w przeglądarce Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="4ae6d-269">Aby uzyskać więcej informacji, zobacz [Kontrolery](xref:mvc/controllers/actions) i [widoki](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-269">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="4ae6d-270">Poniższe funkcje wymagają migracji z przykładowego projektu MVC ASP.NET do projektu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-270">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="4ae6d-271">zawartość po stronie klienta (CSS, Fonts i scripts)</span><span class="sxs-lookup"><span data-stu-id="4ae6d-271">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="4ae6d-272">kontrolery</span><span class="sxs-lookup"><span data-stu-id="4ae6d-272">controllers</span></span>

* <span data-ttu-id="4ae6d-273">widoki</span><span class="sxs-lookup"><span data-stu-id="4ae6d-273">views</span></span>

* <span data-ttu-id="4ae6d-274">modele</span><span class="sxs-lookup"><span data-stu-id="4ae6d-274">models</span></span>

* <span data-ttu-id="4ae6d-275">tworzenia pakietów</span><span class="sxs-lookup"><span data-stu-id="4ae6d-275">bundling</span></span>

* <span data-ttu-id="4ae6d-276">filtry</span><span class="sxs-lookup"><span data-stu-id="4ae6d-276">filters</span></span>

* <span data-ttu-id="4ae6d-277">Logowanie/wylogowywanie, Identity (to jest wykonywane w następnym samouczku).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-277">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="4ae6d-278">Kontrolery i widoki</span><span class="sxs-lookup"><span data-stu-id="4ae6d-278">Controllers and views</span></span>

* <span data-ttu-id="4ae6d-279">Skopiuj każdą z metod z ASP.NET MVC `HomeController` do nowej `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-279">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="4ae6d-280">W ASP.NET MVC Metoda akcji kontrolera wbudowanego szablonu ma wartość [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); w ASP.NET Core MVC, zamiast tego zwracają metody akcji `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-280">In ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="4ae6d-281">`ActionResult`implementuje `IActionResult` , dlatego nie trzeba zmieniać zwracanego typu metod akcji.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-281">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="4ae6d-282">Skopiuj pliki *. cshtml*, *Contact. cshtml*i *Index. cshtml* Razor z projektu ASP.NET MVC do projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-282">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="4ae6d-283">Przetestuj każdą metodę</span><span class="sxs-lookup"><span data-stu-id="4ae6d-283">Test each method</span></span>

<span data-ttu-id="4ae6d-284">Plik i style układu nie zostały jeszcze zmigrowane, więc renderowane widoki zawierają tylko zawartość w plikach widoku.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-284">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="4ae6d-285">Linki do pliku układu dla `About` `Contact` widoków i nie będą jeszcze dostępne.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-285">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

<span data-ttu-id="4ae6d-286">Wywołaj renderowane widoki z przeglądarki w działającej aplikacji ASP.NET Core, zastępując bieżący numer portu numerem portu używanym w projekcie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-286">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="4ae6d-287">Na przykład: `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-287">For example: `https://localhost:44375/home/about`.</span></span>

![Strona kontaktu](mvc/_static/contact-page.png)

<span data-ttu-id="4ae6d-289">Zwróć uwagę na brak stylów i elementów menu.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-289">Note the lack of styling and menu items.</span></span> <span data-ttu-id="4ae6d-290">Styl zostanie rozwiązany w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-290">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="4ae6d-291">Zawartość statyczna</span><span class="sxs-lookup"><span data-stu-id="4ae6d-291">Static content</span></span>

<span data-ttu-id="4ae6d-292">W ASP.NET MVC 5 i starszych zawartość statyczna była hostowana z poziomu korzenia projektu sieci Web i była mieszana przy użyciu plików po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-292">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="4ae6d-293">W ASP.NET Core zawartość statyczna jest hostowana w katalogu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-293">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="4ae6d-294">Skopiuj zawartość statyczną z aplikacji ASP.NET MVC do katalogu *wwwroot* w projekcie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-294">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="4ae6d-295">W tej przykładowej konwersji:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-295">In this sample conversion:</span></span>

* <span data-ttu-id="4ae6d-296">Skopiuj plik *favicon. ico* z projektu ASP.NET MVC do katalogu *wwwroot* w projekcie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-296">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="4ae6d-297">Projekt ASP.NET MVC używa narzędzia [Bootstrap](https://getbootstrap.com/) do jego stylu i przechowuje pliki Bootstrap w katalogach *zawartości* i *skryptów* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-297">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="4ae6d-298">Szablon, który wygenerował projekt ASP.NET MVC, odwołuje się do Bootstrap w pliku układu (*views/Shared/_Layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-298">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="4ae6d-299">Pliki *bootstrap.js* i *Bootstrap. css* można SKOPIOWAĆ z projektu ASP.NET MVC do katalogu *wwwroot* w nowym projekcie.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-299">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="4ae6d-300">Zamiast tego ten dokument dodaje obsługę ładowania początkowego (i innych bibliotek po stronie klienta) przy użyciu sieci CDN, w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-300">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="4ae6d-301">Migrowanie pliku układu</span><span class="sxs-lookup"><span data-stu-id="4ae6d-301">Migrate the layout file</span></span>

* <span data-ttu-id="4ae6d-302">Skopiuj plik *_ViewStart. cshtml* z katalogu *widoków* projektu ASP.NET MVC do katalogu *widoków* projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-302">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="4ae6d-303">Plik *_ViewStart. cshtml* nie został zmieniony w ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-303">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="4ae6d-304">Utwórz *widok/katalog udostępniony* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-304">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="4ae6d-305">*Opcjonalne:* Skopiuj *_ViewImports. cshtml* z katalogu *widoków* projektu *FullAspNetCore* MVC do katalogu *widoków* projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-305">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="4ae6d-306">Usuń deklarację przestrzeni nazw w pliku *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-306">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="4ae6d-307">Plik *_ViewImports. cshtml* zawiera przestrzenie nazw dla wszystkich plików widoków i wywołuje [pomocników tagów](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-307">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="4ae6d-308">Pomocnicy tagów są używani w nowym pliku układu.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-308">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="4ae6d-309">Plik *_ViewImports. cshtml* jest nowy dla ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-309">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="4ae6d-310">Skopiuj plik *_Layout. cshtml* z *widoku/udostępnionego* projektu ASP.NET MVC do widoku ASP.NET Core projektu */udostępnionego* katalogu.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-310">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="4ae6d-311">Otwórz plik *_Layout. cshtml* i wprowadź następujące zmiany (kod wykonany jest przedstawiony poniżej):</span><span class="sxs-lookup"><span data-stu-id="4ae6d-311">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="4ae6d-312">Zamień na `@Styles.Render("~/Content/css")` `<link>` element, który ma ładować *Bootstrap. css* (patrz poniżej).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-312">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="4ae6d-313">Usuń `@Scripts.Render("~/bundles/modernizr")` .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-313">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="4ae6d-314">Dodaj komentarz do `@Html.Partial("_LoginPartial")` wiersza (Otocz linią `@*...*@` ).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-314">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="4ae6d-315">Aby uzyskać więcej informacji, zobacz [Migrowanie uwierzytelniania i Identity ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="4ae6d-315">For more information, see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="4ae6d-316">Zamień `@Scripts.Render("~/bundles/jquery")` na `<script>` element (patrz poniżej).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-316">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="4ae6d-317">Zamień `@Scripts.Render("~/bundles/bootstrap")` na `<script>` element (patrz poniżej).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-317">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="4ae6d-318">Znacznik zastępczy dla dołączania do ładowania początkowego:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-318">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="4ae6d-319">Znaczniki zamiany dla dołączania jQuery i ładowania początkowego JavaScript:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-319">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="4ae6d-320">Zaktualizowany plik *_Layout. cshtml* jest przedstawiony poniżej:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-320">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="4ae6d-321">Wyświetl witrynę w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-321">View the site in the browser.</span></span> <span data-ttu-id="4ae6d-322">Powinien być teraz poprawnie załadowany przy użyciu oczekiwanych stylów.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-322">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="4ae6d-323">*Opcjonalne:* Spróbuj użyć nowego pliku układu.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-323">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="4ae6d-324">Skopiuj plik układu z projektu *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-324">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="4ae6d-325">Nowy plik układu używa [pomocników tagów](xref:mvc/views/tag-helpers/intro) i ma inne ulepszenia.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-325">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="4ae6d-326">Konfigurowanie grupowania i minifikacja</span><span class="sxs-lookup"><span data-stu-id="4ae6d-326">Configure bundling and minification</span></span>

<span data-ttu-id="4ae6d-327">Aby uzyskać informacje o sposobie konfigurowania grupowania i minifikacja, zobacz Tworzenie [i minifikacja](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-327">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="4ae6d-328">Rozwiązywanie błędów HTTP 500</span><span class="sxs-lookup"><span data-stu-id="4ae6d-328">Solve HTTP 500 errors</span></span>

<span data-ttu-id="4ae6d-329">Istnieje wiele problemów, które mogą spowodować, że komunikaty o błędach HTTP 500, które nie zawierają żadnych informacji na temat źródła problemu.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-329">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="4ae6d-330">Na przykład, jeśli plik *views/_ViewImports. cshtml* zawiera przestrzeń nazw, która nie istnieje w projekcie, generowany jest błąd HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-330">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="4ae6d-331">Domyślnie w aplikacjach ASP.NET Core `UseDeveloperExceptionPage` rozszerzenie jest dodawane do `IApplicationBuilder` i wykonywane podczas *opracowywania*konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-331">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="4ae6d-332">Zobacz przykład w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-332">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="4ae6d-333">ASP.NET Core konwertuje Nieobsłużone wyjątki na odpowiedzi na błędy HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-333">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="4ae6d-334">Zwykle szczegóły błędu nie są uwzględniane w tych odpowiedziach, aby zapobiec ujawnieniu potencjalnie poufnych informacji o serwerze.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-334">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="4ae6d-335">Aby uzyskać więcej informacji, zobacz [stronę wyjątków dla deweloperów](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-335">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4ae6d-336">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="4ae6d-336">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="4ae6d-337">W tym artykule pokazano, jak rozpocząć Migrowanie projektu ASP.NET MVC do [ASP.NET Core MVC](xref:mvc/overview) 2,1.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-337">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.1.</span></span> <span data-ttu-id="4ae6d-338">W procesie wyróżniamy wiele elementów, które uległy zmianie z ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-338">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="4ae6d-339">Migrowanie z ASP.NET MVC jest procesem wieloetapowym.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-339">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="4ae6d-340">W tym artykule omówiono następujące zagadnienia:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-340">This article covers:</span></span>

* <span data-ttu-id="4ae6d-341">Początkowa konfiguracja</span><span class="sxs-lookup"><span data-stu-id="4ae6d-341">Initial setup</span></span>
* <span data-ttu-id="4ae6d-342">Podstawowe kontrolery i widoki</span><span class="sxs-lookup"><span data-stu-id="4ae6d-342">Basic controllers and views</span></span>
* <span data-ttu-id="4ae6d-343">Zawartość statyczna</span><span class="sxs-lookup"><span data-stu-id="4ae6d-343">Static content</span></span>
* <span data-ttu-id="4ae6d-344">Zależności po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-344">Client-side dependencies.</span></span>

<span data-ttu-id="4ae6d-345">Aby przeprowadzić migrację konfiguracji i Identity kodu, zobacz [Migrowanie konfiguracji do ASP.NET Core](xref:migration/configuration) i [migrowanie uwierzytelniania i Identity ASP.NET Core](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-345">For migrating configuration and Identity code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity).</span></span>

> [!NOTE]
> <span data-ttu-id="4ae6d-346">Numery wersji w przykładach mogą nie być aktualne, należy odpowiednio zaktualizować projekty.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-346">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="4ae6d-347">Tworzenie projektu Starter ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="4ae6d-347">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="4ae6d-348">Aby zademonstrować uaktualnienie, zaczniemy od utworzenia aplikacji ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-348">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="4ae6d-349">Utwórz go przy użyciu nazwy *WebApp1* , aby przestrzeń nazw była zgodna z projektem ASP.NET Core utworzonym w następnym kroku.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-349">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Okno dialogowe nowego projektu programu Visual Studio](mvc/_static/new-project.png)

![Okno dialogowe Nowa aplikacja sieci Web: szablon projektu MVC wybrany w panelu szablony ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="4ae6d-352">*Opcjonalne:* Zmień nazwę rozwiązania z *WebApp1* na *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-352">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="4ae6d-353">Program Visual Studio wyświetla nową nazwę rozwiązania (*Mvc5*), która ułatwia poinformowanie tego projektu z następnego projektu.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-353">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="4ae6d-354">Tworzenie projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4ae6d-354">Create the ASP.NET Core project</span></span>

<span data-ttu-id="4ae6d-355">Utwórz nową *pustą* aplikację sieci Web ASP.NET Core o takiej samej nazwie jak w poprzednim projekcie (*WebApp1*), tak aby przestrzenie nazw w obu projektach były zgodne.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-355">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="4ae6d-356">Posiadanie tego samego obszaru nazw ułatwia kopiowanie kodu między tymi dwoma projektami.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-356">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="4ae6d-357">Utwórz ten projekt w innym katalogu niż poprzedni projekt, aby użyć tej samej nazwy.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-357">Create this project in a different directory than the previous project to use the same name.</span></span>

![Okno dialogowe Nowy projekt](mvc/_static/new_core.png)

![Nowe okno dialogowe aplikacji sieci Web ASP.NET: pusty szablon projektu wybrany w panelu szablony ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="4ae6d-360">*Opcjonalne:* Utwórz nową aplikację ASP.NET Core przy użyciu szablonu projektu *aplikacji sieci Web* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-360">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="4ae6d-361">Nazwij projekt *WebApp1*i wybierz opcję uwierzytelniania **poszczególnych kont użytkowników**.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-361">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="4ae6d-362">Zmień nazwę tej aplikacji na *FullAspNetCore*.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-362">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="4ae6d-363">Utworzenie tego projektu powoduje zaoszczędzenie czasu w konwersji.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-363">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="4ae6d-364">Wynik końcowy może być wyświetlany w kodzie generowanym przez szablon, kod może być kopiowany do projektu konwersji lub porównywany z projektem generowanym przez szablon.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-364">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="4ae6d-365">Konfigurowanie lokacji do korzystania z MVC</span><span class="sxs-lookup"><span data-stu-id="4ae6d-365">Configure the site to use MVC</span></span>

* <span data-ttu-id="4ae6d-366">W przypadku określania platformy .NET Core jest przywoływany domyślny [pakiet Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-366">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="4ae6d-367">Ten pakiet zawiera pakiety powszechnie używane przez aplikacje MVC.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-367">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="4ae6d-368">W przypadku .NET Framework określania wartości docelowej odwołania do pakietów muszą być wyszczególnione pojedynczo w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-368">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="4ae6d-369">`Microsoft.AspNetCore.Mvc`jest platformą ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-369">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="4ae6d-370">`Microsoft.AspNetCore.StaticFiles`jest programem obsługi plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-370">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="4ae6d-371">Aplikacje ASP.NET Core jawnie wybierają oprogramowanie pośredniczące, na przykład w celu obsługi plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-371">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="4ae6d-372">Aby uzyskać więcej informacji, zobacz [pliki statyczne](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-372">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="4ae6d-373">Otwórz plik *Startup.cs* i Zmień kod w taki sposób, aby był zgodny z następującymi:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-373">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="4ae6d-374"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>Metoda rozszerzenia dodaje procedurę obsługi pliku statycznego.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-374">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="4ae6d-375">`UseMvc`Metoda rozszerzenia dodaje Routing.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-375">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="4ae6d-376">Aby uzyskać więcej informacji, zobacz Uruchamianie i [Routing](xref:fundamentals/routing) [aplikacji](xref:fundamentals/startup) .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-376">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="4ae6d-377">Dodawanie kontrolera i widoku</span><span class="sxs-lookup"><span data-stu-id="4ae6d-377">Add a controller and view</span></span>

<span data-ttu-id="4ae6d-378">W tej sekcji zostanie dodany minimalny kontroler i widok służący jako symbole zastępcze dla kontrolera ASP.NET MVC i widoków migrowanych w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-378">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="4ae6d-379">Dodaj katalog *kontrolerów* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-379">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="4ae6d-380">Dodaj **klasę kontrolera** o nazwie *HomeController.cs* do katalogu *controllers* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-380">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![Okno dialogowe Dodawanie nowego elementu](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="4ae6d-382">Dodaj katalog *widoków* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-382">Add a *Views* directory.</span></span>

* <span data-ttu-id="4ae6d-383">Dodaj *widok/katalog macierzysty* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-383">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="4ae6d-384">Dodaj \*\* Razor Widok\*\* o nazwie *index. cshtml* do *widoków/katalogu macierzystego* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-384">Add a **Razor View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![Okno dialogowe Dodawanie nowego elementu](mvc/_static/view.png)

<span data-ttu-id="4ae6d-386">Poniżej przedstawiono strukturę projektu:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-386">The project structure is shown below:</span></span>

![Eksplorator rozwiązań pokazywania plików i katalogów WebApp1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="4ae6d-388">Zastąp zawartość pliku *viewss/Home/index. cshtml* następującym znacznikiem:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-388">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="4ae6d-389">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-389">Run the app.</span></span>

![Aplikacja internetowa otwarta w przeglądarce Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="4ae6d-391">Aby uzyskać więcej informacji, zobacz [Kontrolery](xref:mvc/controllers/actions) i [widoki](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-391">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="4ae6d-392">Poniższe funkcje wymagają migracji z przykładowego projektu MVC ASP.NET do projektu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-392">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="4ae6d-393">zawartość po stronie klienta (CSS, Fonts i scripts)</span><span class="sxs-lookup"><span data-stu-id="4ae6d-393">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="4ae6d-394">kontrolery</span><span class="sxs-lookup"><span data-stu-id="4ae6d-394">controllers</span></span>

* <span data-ttu-id="4ae6d-395">widoki</span><span class="sxs-lookup"><span data-stu-id="4ae6d-395">views</span></span>

* <span data-ttu-id="4ae6d-396">modele</span><span class="sxs-lookup"><span data-stu-id="4ae6d-396">models</span></span>

* <span data-ttu-id="4ae6d-397">tworzenia pakietów</span><span class="sxs-lookup"><span data-stu-id="4ae6d-397">bundling</span></span>

* <span data-ttu-id="4ae6d-398">filtry</span><span class="sxs-lookup"><span data-stu-id="4ae6d-398">filters</span></span>

* <span data-ttu-id="4ae6d-399">Logowanie/wylogowywanie, Identity (to jest wykonywane w następnym samouczku).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-399">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="4ae6d-400">Kontrolery i widoki</span><span class="sxs-lookup"><span data-stu-id="4ae6d-400">Controllers and views</span></span>

* <span data-ttu-id="4ae6d-401">Skopiuj każdą z metod z ASP.NET MVC `HomeController` do nowej `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-401">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="4ae6d-402">W ASP.NET MVC Metoda akcji kontrolera wbudowanego szablonu ma wartość [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); w ASP.NET Core MVC, zamiast tego zwracają metody akcji `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-402">In ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="4ae6d-403">`ActionResult`implementuje `IActionResult` , dlatego nie trzeba zmieniać zwracanego typu metod akcji.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-403">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="4ae6d-404">Skopiuj pliki *. cshtml*, *Contact. cshtml*i *Index. cshtml* Razor z projektu ASP.NET MVC do projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-404">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="4ae6d-405">Przetestuj każdą metodę</span><span class="sxs-lookup"><span data-stu-id="4ae6d-405">Test each method</span></span>

<span data-ttu-id="4ae6d-406">Plik i style układu nie zostały jeszcze zmigrowane, więc renderowane widoki zawierają tylko zawartość w plikach widoku.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-406">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="4ae6d-407">Linki do pliku układu dla `About` `Contact` widoków i nie będą jeszcze dostępne.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-407">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

* <span data-ttu-id="4ae6d-408">Wywołaj renderowane widoki z przeglądarki w działającej aplikacji ASP.NET Core, zastępując bieżący numer portu numerem portu używanym w projekcie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-408">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="4ae6d-409">Na przykład: `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-409">For example: `https://localhost:44375/home/about`.</span></span>

![Strona kontaktu](mvc/_static/contact-page.png)

<span data-ttu-id="4ae6d-411">Zwróć uwagę na brak stylów i elementów menu.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-411">Note the lack of styling and menu items.</span></span> <span data-ttu-id="4ae6d-412">Styl zostanie rozwiązany w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-412">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="4ae6d-413">Zawartość statyczna</span><span class="sxs-lookup"><span data-stu-id="4ae6d-413">Static content</span></span>

<span data-ttu-id="4ae6d-414">W ASP.NET MVC 5 i starszych zawartość statyczna była hostowana z poziomu korzenia projektu sieci Web i była mieszana przy użyciu plików po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-414">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="4ae6d-415">W ASP.NET Core zawartość statyczna jest hostowana w katalogu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-415">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="4ae6d-416">Skopiuj zawartość statyczną z aplikacji ASP.NET MVC do katalogu *wwwroot* w projekcie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-416">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="4ae6d-417">W tej przykładowej konwersji:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-417">In this sample conversion:</span></span>

* <span data-ttu-id="4ae6d-418">Skopiuj plik *favicon. ico* z projektu ASP.NET MVC do katalogu *wwwroot* w projekcie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-418">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="4ae6d-419">Projekt ASP.NET MVC używa narzędzia [Bootstrap](https://getbootstrap.com/) do jego stylu i przechowuje pliki Bootstrap w katalogach *zawartości* i *skryptów* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-419">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="4ae6d-420">Szablon, który wygenerował projekt ASP.NET MVC, odwołuje się do Bootstrap w pliku układu (*views/Shared/_Layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-420">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="4ae6d-421">Pliki *bootstrap.js* i *Bootstrap. css* można SKOPIOWAĆ z projektu ASP.NET MVC do katalogu *wwwroot* w nowym projekcie.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-421">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="4ae6d-422">Zamiast tego ten dokument dodaje obsługę ładowania początkowego (i innych bibliotek po stronie klienta) przy użyciu sieci CDN, w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-422">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="4ae6d-423">Migrowanie pliku układu</span><span class="sxs-lookup"><span data-stu-id="4ae6d-423">Migrate the layout file</span></span>

* <span data-ttu-id="4ae6d-424">Skopiuj plik *_ViewStart. cshtml* z katalogu *widoków* projektu ASP.NET MVC do katalogu *widoków* projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-424">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="4ae6d-425">Plik *_ViewStart. cshtml* nie został zmieniony w ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-425">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="4ae6d-426">Utwórz *widok/katalog udostępniony* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-426">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="4ae6d-427">*Opcjonalne:* Skopiuj *_ViewImports. cshtml* z katalogu *widoków* projektu *FullAspNetCore* MVC do katalogu *widoków* projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-427">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="4ae6d-428">Usuń deklarację przestrzeni nazw w pliku *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-428">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="4ae6d-429">Plik *_ViewImports. cshtml* zawiera przestrzenie nazw dla wszystkich plików widoków i wywołuje [pomocników tagów](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-429">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="4ae6d-430">Pomocnicy tagów są używani w nowym pliku układu.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-430">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="4ae6d-431">Plik *_ViewImports. cshtml* jest nowy dla ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-431">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="4ae6d-432">Skopiuj plik *_Layout. cshtml* z *widoku/udostępnionego* projektu ASP.NET MVC do widoku ASP.NET Core projektu */udostępnionego* katalogu.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-432">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="4ae6d-433">Otwórz plik *_Layout. cshtml* i wprowadź następujące zmiany (kod wykonany jest przedstawiony poniżej):</span><span class="sxs-lookup"><span data-stu-id="4ae6d-433">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="4ae6d-434">Zamień na `@Styles.Render("~/Content/css")` `<link>` element, który ma ładować *Bootstrap. css* (patrz poniżej).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-434">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="4ae6d-435">Usuń `@Scripts.Render("~/bundles/modernizr")` .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-435">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="4ae6d-436">Dodaj komentarz do `@Html.Partial("_LoginPartial")` wiersza (Otocz linią `@*...*@` ).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-436">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="4ae6d-437">Aby uzyskać więcej informacji, zobacz [Migrowanie uwierzytelniania i Identity ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="4ae6d-437">For more information, see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="4ae6d-438">Zamień `@Scripts.Render("~/bundles/jquery")` na `<script>` element (patrz poniżej).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-438">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="4ae6d-439">Zamień `@Scripts.Render("~/bundles/bootstrap")` na `<script>` element (patrz poniżej).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-439">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="4ae6d-440">Znacznik zastępczy dla dołączania do ładowania początkowego:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-440">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="4ae6d-441">Znaczniki zamiany dla dołączania jQuery i ładowania początkowego JavaScript:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-441">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="4ae6d-442">Zaktualizowany plik *_Layout. cshtml* jest przedstawiony poniżej:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-442">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="4ae6d-443">Wyświetl witrynę w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-443">View the site in the browser.</span></span> <span data-ttu-id="4ae6d-444">Powinien być teraz poprawnie załadowany przy użyciu oczekiwanych stylów.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-444">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="4ae6d-445">*Opcjonalne:* Spróbuj użyć nowego pliku układu.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-445">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="4ae6d-446">Skopiuj plik układu z projektu *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="4ae6d-446">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="4ae6d-447">Nowy plik układu używa [pomocników tagów](xref:mvc/views/tag-helpers/intro) i ma inne ulepszenia.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-447">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="4ae6d-448">Konfigurowanie grupowania i minifikacja</span><span class="sxs-lookup"><span data-stu-id="4ae6d-448">Configure bundling and minification</span></span>

<span data-ttu-id="4ae6d-449">Aby uzyskać informacje o sposobie konfigurowania grupowania i minifikacja, zobacz Tworzenie [i minifikacja](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-449">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="4ae6d-450">Rozwiązywanie błędów HTTP 500</span><span class="sxs-lookup"><span data-stu-id="4ae6d-450">Solve HTTP 500 errors</span></span>

<span data-ttu-id="4ae6d-451">Istnieje wiele problemów, które mogą spowodować, że komunikaty o błędach HTTP 500, które nie zawierają żadnych informacji na temat źródła problemu.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-451">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="4ae6d-452">Na przykład, jeśli plik *views/_ViewImports. cshtml* zawiera przestrzeń nazw, która nie istnieje w projekcie, generowany jest błąd HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-452">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="4ae6d-453">Domyślnie w aplikacjach ASP.NET Core `UseDeveloperExceptionPage` rozszerzenie jest dodawane do `IApplicationBuilder` i wykonywane podczas *opracowywania*konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-453">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="4ae6d-454">Zobacz przykład w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="4ae6d-454">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="4ae6d-455">ASP.NET Core konwertuje Nieobsłużone wyjątki na odpowiedzi na błędy HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-455">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="4ae6d-456">Zwykle szczegóły błędu nie są uwzględniane w tych odpowiedziach, aby zapobiec ujawnieniu potencjalnie poufnych informacji o serwerze.</span><span class="sxs-lookup"><span data-stu-id="4ae6d-456">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="4ae6d-457">Aby uzyskać więcej informacji, zobacz [stronę wyjątków dla deweloperów](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="4ae6d-457">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4ae6d-458">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="4ae6d-458">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
