---
title: Dowiedz się, jak przeprowadzić migrację z ASP.NET MVC do ASP.NET Core MVC
author: wadepickett
description: Dowiedz się, jak rozpocząć Migrowanie projektu ASP.NET MVC do ASP.NET Core MVC.
ms.author: wpickett
ms.date: 06/18/2020
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
uid: migration/mvc
ms.openlocfilehash: 51228e59284b5edf0554e9929b16deafe08ea31e
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326624"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="63cf5-103">Migrowanie z ASP.NET MVC do ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="63cf5-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="63cf5-104">W tym artykule pokazano, jak rozpocząć Migrowanie projektu ASP.NET MVC do [ASP.NET Core MVC](xref:mvc/overview).</span><span class="sxs-lookup"><span data-stu-id="63cf5-104">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview).</span></span> <span data-ttu-id="63cf5-105">W procesie są wyróżniane powiązane zmiany z ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="63cf5-105">In the process, it highlights related changes from ASP.NET MVC.</span></span>

<span data-ttu-id="63cf5-106">Migrowanie z ASP.NET MVC jest procesem wieloetapowym.</span><span class="sxs-lookup"><span data-stu-id="63cf5-106">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="63cf5-107">W tym artykule omówiono następujące zagadnienia:</span><span class="sxs-lookup"><span data-stu-id="63cf5-107">This article covers:</span></span>

* <span data-ttu-id="63cf5-108">Początkowa konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="63cf5-108">Initial setup.</span></span>
* <span data-ttu-id="63cf5-109">Podstawowe kontrolery i widoki.</span><span class="sxs-lookup"><span data-stu-id="63cf5-109">Basic controllers and views.</span></span>
* <span data-ttu-id="63cf5-110">Zawartość statyczna.</span><span class="sxs-lookup"><span data-stu-id="63cf5-110">Static content.</span></span>
* <span data-ttu-id="63cf5-111">Zależności po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="63cf5-111">Client-side dependencies.</span></span>

<span data-ttu-id="63cf5-112">Aby przeprowadzić migrację konfiguracji i Identity kodu, zobacz [Migrowanie konfiguracji do ASP.NET Core](xref:migration/configuration) i [migrowanie uwierzytelniania i Identity ASP.NET Core](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="63cf5-112">For migrating configuration and Identity code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="63cf5-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="63cf5-113">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="63cf5-114">Tworzenie projektu Starter ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="63cf5-114">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="63cf5-115">Utwórz przykładowy projekt ASP.NET MVC w programie Visual Studio do migracji:</span><span class="sxs-lookup"><span data-stu-id="63cf5-115">Create an example ASP.NET MVC project in Visual Studio to migrate:</span></span>

1. <span data-ttu-id="63cf5-116">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="63cf5-116">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="63cf5-117">Wybierz pozycję **aplikacja sieci Web ASP.NET (.NET Framework)** , a następnie wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="63cf5-117">Select **ASP.NET Web Application (.NET Framework)** and then select **Next**.</span></span>
1. <span data-ttu-id="63cf5-118">Nazwij projekt *WebApp1* , aby przestrzeń nazw była zgodna z projektem ASP.NET Core utworzonym w następnym kroku.</span><span class="sxs-lookup"><span data-stu-id="63cf5-118">Name the project *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span> <span data-ttu-id="63cf5-119">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="63cf5-119">Select **Create**.</span></span>
1. <span data-ttu-id="63cf5-120">Wybierz pozycję **MVC**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="63cf5-120">Select **MVC**, and then select **Create**.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="63cf5-121">Tworzenie projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="63cf5-121">Create the ASP.NET Core project</span></span>

<span data-ttu-id="63cf5-122">Utwórz nowe rozwiązanie z nowym projektem ASP.NET Core, aby przeprowadzić migrację do:</span><span class="sxs-lookup"><span data-stu-id="63cf5-122">Create a new solution with a new ASP.NET Core project to migrate to:</span></span>

1. <span data-ttu-id="63cf5-123">Uruchom drugie wystąpienie programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="63cf5-123">Launch a second instance of Visual Studio.</span></span>
1. <span data-ttu-id="63cf5-124">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="63cf5-124">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="63cf5-125">Wybierz **ASP.NET Core aplikacji sieci Web** , a następnie wybierz przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="63cf5-125">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>
1. <span data-ttu-id="63cf5-126">W oknie dialogowym **Konfigurowanie nowego projektu** Nazwij projekt *WebApp1*.</span><span class="sxs-lookup"><span data-stu-id="63cf5-126">In the **Configure your new project** dialog, Name the project *WebApp1*.</span></span>
1. <span data-ttu-id="63cf5-127">Ustaw lokalizację na inny katalog niż poprzedni projekt, aby użyć tej samej nazwy projektu.</span><span class="sxs-lookup"><span data-stu-id="63cf5-127">Set the location to a different directory than the previous project to use the same project name.</span></span> <span data-ttu-id="63cf5-128">Korzystanie z tej samej przestrzeni nazw ułatwia kopiowanie kodu między dwoma projektami.</span><span class="sxs-lookup"><span data-stu-id="63cf5-128">Using the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="63cf5-129">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="63cf5-129">Select **Create**.</span></span>
1. <span data-ttu-id="63cf5-130">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="63cf5-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="63cf5-131">Wybierz szablon projektu **aplikacja sieci Web (Model-View-Controller)** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="63cf5-131">Select the **Web Application (Model-View-Controller)** project template, and select **Create**.</span></span>

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a><span data-ttu-id="63cf5-132">Skonfiguruj witrynę ASP.NET Core, aby używać MVC</span><span class="sxs-lookup"><span data-stu-id="63cf5-132">Configure the ASP.NET Core site to use MVC</span></span>

<span data-ttu-id="63cf5-133">W ASP.NET Core 3,0 i nowszych projektach .NET Framework nie jest już obsługiwaną platformą docelową.</span><span class="sxs-lookup"><span data-stu-id="63cf5-133">In ASP.NET Core 3.0 and later projects, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="63cf5-134">Projekt musi być przeznaczony dla platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="63cf5-134">Your project must target .NET Core.</span></span> <span data-ttu-id="63cf5-135">ASP.NET Core Shared Framework, która obejmuje MVC, jest częścią instalacji środowiska uruchomieniowego platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="63cf5-135">The ASP.NET Core shared framework, which includes MVC, is part of the .NET Core runtime installation.</span></span> <span data-ttu-id="63cf5-136">W przypadku korzystania z `Microsoft.NET.Sdk.Web` zestawu SDK w pliku projektu automatycznie występuje odwołanie do udostępnionej struktury:</span><span class="sxs-lookup"><span data-stu-id="63cf5-136">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="63cf5-137">Aby uzyskać więcej informacji, zobacz temat [informacje dotyczące platformy](xref:migration/22-to-30#framework-reference).</span><span class="sxs-lookup"><span data-stu-id="63cf5-137">For more information, see [Framework reference](xref:migration/22-to-30#framework-reference).</span></span>

<span data-ttu-id="63cf5-138">W ASP.NET Core `Startup` Klasa:</span><span class="sxs-lookup"><span data-stu-id="63cf5-138">In ASP.NET Core, the `Startup` class:</span></span>

* <span data-ttu-id="63cf5-139">Zastępuje *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="63cf5-139">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="63cf5-140">Obsługuje wszystkie zadania uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="63cf5-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="63cf5-141">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="63cf5-141">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="63cf5-142">W projekcie ASP.NET Core Otwórz plik *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="63cf5-142">In the ASP.NET Core project, open the *Startup.cs* file:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

<span data-ttu-id="63cf5-143">Aplikacje ASP.NET Core muszą przystąpić do struktur funkcji za pomocą oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="63cf5-143">ASP.NET Core apps must opt in to framework features with middleware.</span></span> <span data-ttu-id="63cf5-144">Poprzedni kod wygenerowany przez szablon dodaje następujące usługi i oprogramowanie pośredniczące:</span><span class="sxs-lookup"><span data-stu-id="63cf5-144">The previous template-generated code adds the following services and middleware:</span></span>

* <span data-ttu-id="63cf5-145"><xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A>Metoda rozszerzenia rejestruje obsługę usług MVC dla kontrolerów, funkcji związanych z interfejsem API i widoków.</span><span class="sxs-lookup"><span data-stu-id="63cf5-145">The <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> extension method registers MVC service support for controllers, API-related features, and views.</span></span> <span data-ttu-id="63cf5-146">Aby uzyskać więcej informacji na temat opcji rejestracji usługi MVC, zobacz temat [Rejestrowanie usługi MVC](xref:migration/22-to-30#mvc-service-registration)</span><span class="sxs-lookup"><span data-stu-id="63cf5-146">For more information on MVC service registration options, see [MVC service registration](xref:migration/22-to-30#mvc-service-registration)</span></span>
* <span data-ttu-id="63cf5-147"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>Metoda rozszerzenia dodaje procedurę obsługi pliku statycznego `Microsoft.AspNetCore.StaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="63cf5-147">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> extension method adds the static file handler `Microsoft.AspNetCore.StaticFiles`.</span></span> <span data-ttu-id="63cf5-148">`UseStaticFiles`Metoda rozszerzenia musi być wywoływana przed `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="63cf5-148">The `UseStaticFiles` extension method must be called before `UseRouting`.</span></span> <span data-ttu-id="63cf5-149">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="63cf5-149">For more information, see <xref:fundamentals/static-files>.</span></span>
* <span data-ttu-id="63cf5-150"><xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>Metoda rozszerzenia dodaje Routing.</span><span class="sxs-lookup"><span data-stu-id="63cf5-150">The <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> extension method adds routing.</span></span> <span data-ttu-id="63cf5-151">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="63cf5-151">For more information, see <xref:fundamentals/routing>.</span></span>

<span data-ttu-id="63cf5-152">Ta istniejąca konfiguracja zawiera informacje potrzebne do migracji przykładowego projektu MVC ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="63cf5-152">This existing configuration includes what is needed to migrate the example ASP.NET MVC project.</span></span> <span data-ttu-id="63cf5-153">Aby uzyskać więcej informacji o ASP.NET Core opcjach oprogramowania pośredniczącego, zobacz <xref:fundamentals/startup> .</span><span class="sxs-lookup"><span data-stu-id="63cf5-153">For more information on ASP.NET Core middleware options, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-controllers-and-views"></a><span data-ttu-id="63cf5-154">Migrowanie kontrolerów i widoków</span><span class="sxs-lookup"><span data-stu-id="63cf5-154">Migrate controllers and views</span></span>

<span data-ttu-id="63cf5-155">W ASP.NET Core projekcie zostanie dodana nowa pusta Klasa kontrolera i Klasa widoku, które będą służyć jako symbole zastępcze przy użyciu takich samych nazw, jak kontroler i klasy widoków w dowolnym projekcie ASP.NET MVC do migracji.</span><span class="sxs-lookup"><span data-stu-id="63cf5-155">In the ASP.NET Core project, a new empty controller class and view class would be added to serve as placeholders using the same names as the controller and view classes in any ASP.NET MVC project to migrate from.</span></span>

<span data-ttu-id="63cf5-156">Projekt ASP.NET Core *WebApp1* zawiera już minimalny przykładowy kontroler i widok o takiej samej nazwie jak projekt ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="63cf5-156">The ASP.NET Core *WebApp1* project already includes a minimal example controller and view by the same name as the ASP.NET MVC project.</span></span> <span data-ttu-id="63cf5-157">Dzięki temu będą one służyć jako symbole zastępcze dla kontrolera ASP.NET MVC i widoków do migracji z projektu ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-157">So those will serve as placeholders for the ASP.NET MVC controller and views to be migrated from the ASP.NET MVC *WebApp1* project.</span></span>

1. <span data-ttu-id="63cf5-158">Skopiuj metody z ASP.NET MVC, `HomeController` Aby zastąpić nowe `HomeController` metody ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63cf5-158">Copy the methods from the ASP.NET MVC `HomeController` to replace the new ASP.NET Core `HomeController` methods.</span></span> <span data-ttu-id="63cf5-159">Nie trzeba zmieniać zwracanego typu metod akcji.</span><span class="sxs-lookup"><span data-stu-id="63cf5-159">There's no need to change the return type of the action methods.</span></span> <span data-ttu-id="63cf5-160">Zwracany typ metody akcji kontrolera wbudowanego szablonu MVC ASP.NET to <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; w ASP.NET Core MVC, zamiast tego zwracają metody akcji `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="63cf5-160">The ASP.NET MVC built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="63cf5-161">`ActionResult` implementuje `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="63cf5-161">`ActionResult` implements `IActionResult`.</span></span>
1. <span data-ttu-id="63cf5-162">W projekcie ASP.NET Core kliknij prawym przyciskiem myszy *widok/katalog macierzysty* , wybierz polecenie **Dodaj** > **istniejący element**.</span><span class="sxs-lookup"><span data-stu-id="63cf5-162">In the ASP.NET Core project, right-click the *Views/Home* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="63cf5-163">W oknie dialogowym **Dodaj istniejący element** przejdź do *widoku/katalogu macierzystego* projektu ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-163">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Home* directory.</span></span>
1. <span data-ttu-id="63cf5-164">Zaznacz pliki widoku *Informacje o. cshtml*, *Contact. cshtml*i *index. cshtml* Razor , a następnie wybierz pozycję **Dodaj**, zastępując istniejące pliki.</span><span class="sxs-lookup"><span data-stu-id="63cf5-164">Select the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files, then select **Add**, replacing the existing files.</span></span>

<span data-ttu-id="63cf5-165">Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/actions> i <xref:mvc/views/overview>.</span><span class="sxs-lookup"><span data-stu-id="63cf5-165">For more information, see <xref:mvc/controllers/actions> and <xref:mvc/views/overview>.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="63cf5-166">Przetestuj każdą metodę</span><span class="sxs-lookup"><span data-stu-id="63cf5-166">Test each method</span></span>

<span data-ttu-id="63cf5-167">Każdy punkt końcowy kontrolera może być testowany, ale układ i style są omówione w dalszej części dokumentu.</span><span class="sxs-lookup"><span data-stu-id="63cf5-167">Each controller endpoint can be tested, however, layout and styles are covered later in the document.</span></span>

1. <span data-ttu-id="63cf5-168">Uruchom aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63cf5-168">Run the ASP.NET Core app.</span></span>
1. <span data-ttu-id="63cf5-169">Wywołaj renderowane widoki z przeglądarki w działającej aplikacji ASP.NET Core, zastępując bieżący numer portu numerem portu używanym w ASP.NET Core projekcie.</span><span class="sxs-lookup"><span data-stu-id="63cf5-169">Invoke the rendered views from the browser on the running ASP.NET Core app by replacing the current port number with the port number used in the ASP.NET Core project.</span></span> <span data-ttu-id="63cf5-170">Na przykład `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="63cf5-170">For example, `https://localhost:44375/home/about`.</span></span>

## <a name="migrate-static-content"></a><span data-ttu-id="63cf5-171">Migrowanie zawartości statycznej</span><span class="sxs-lookup"><span data-stu-id="63cf5-171">Migrate static content</span></span>

<span data-ttu-id="63cf5-172">W ASP.NET MVC 5 i starszych zawartość statyczna była hostowana z katalogu głównego projektu sieci Web i była mieszana przy użyciu plików po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="63cf5-172">In ASP.NET MVC 5 and earlier, static content was hosted from the web project's root directory and was intermixed with server-side files.</span></span> <span data-ttu-id="63cf5-173">W ASP.NET Core pliki statyczne są przechowywane w katalogu [głównym sieci Web](xref:fundamentals/index#web-root) projektu.</span><span class="sxs-lookup"><span data-stu-id="63cf5-173">In ASP.NET Core, static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="63cf5-174">Domyślnym katalogiem jest *{Content root}/wwwroot*, ale można go zmienić.</span><span class="sxs-lookup"><span data-stu-id="63cf5-174">The default directory is *{content root}/wwwroot*, but it can be changed.</span></span> <span data-ttu-id="63cf5-175">Aby uzyskać więcej informacji, zobacz [pliki statyczne w ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span><span class="sxs-lookup"><span data-stu-id="63cf5-175">For more information, see [Static files in ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span></span>

<span data-ttu-id="63cf5-176">Skopiuj zawartość statyczną z projektu ASP.NET MVC *WebApp1* do katalogu *wwwroot* w projekcie ASP.NET Core *WebApp1* :</span><span class="sxs-lookup"><span data-stu-id="63cf5-176">Copy the static content from the ASP.NET MVC *WebApp1* project to the *wwwroot* directory in the ASP.NET Core *WebApp1* project:</span></span>

1. <span data-ttu-id="63cf5-177">W projekcie ASP.NET Core kliknij prawym przyciskiem myszy katalog *wwwroot* , wybierz pozycję **Dodaj** > **istniejący element**.</span><span class="sxs-lookup"><span data-stu-id="63cf5-177">In the ASP.NET Core project, right-click the *wwwroot* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="63cf5-178">W oknie dialogowym **Dodaj istniejący element** przejdź do projektu ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-178">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project.</span></span>
1. <span data-ttu-id="63cf5-179">Wybierz plik *favicon. ico* , a następnie wybierz pozycję **Dodaj**, zastępując istniejący plik.</span><span class="sxs-lookup"><span data-stu-id="63cf5-179">Select the *favicon.ico* file, then select **Add**, replacing the existing file.</span></span>

## <a name="migrate-the-layout-files"></a><span data-ttu-id="63cf5-180">Migrowanie plików układu</span><span class="sxs-lookup"><span data-stu-id="63cf5-180">Migrate the layout files</span></span>

<span data-ttu-id="63cf5-181">Skopiuj pliki układu projektu MVC ASP.NET do projektu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="63cf5-181">Copy the ASP.NET MVC project layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="63cf5-182">W projekcie ASP.NET Core kliknij prawym przyciskiem myszy katalog *widoki* , a następnie wybierz pozycję **Dodaj** > **istniejący element**.</span><span class="sxs-lookup"><span data-stu-id="63cf5-182">In the ASP.NET Core project, right-click the *Views* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="63cf5-183">W oknie dialogowym **Dodaj istniejący element** przejdź do katalogu *widoków* projektu ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-183">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views* directory.</span></span>
1. <span data-ttu-id="63cf5-184">Wybierz plik *_ViewStart. cshtml* , a następnie wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="63cf5-184">Select the *_ViewStart.cshtml* file then select **Add**.</span></span>

<span data-ttu-id="63cf5-185">Skopiuj pliki wspólnych układów projektu ASP.NET MVC do projektu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="63cf5-185">Copy the ASP.NET MVC project shared layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="63cf5-186">W projekcie ASP.NET Core kliknij prawym przyciskiem myszy *Widok/udostępniony* katalog, wybierz polecenie **Dodaj** > **istniejący element**.</span><span class="sxs-lookup"><span data-stu-id="63cf5-186">In the ASP.NET Core project, right-click the *Views/Shared* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="63cf5-187">W oknie dialogowym **Dodaj istniejący element** przejdź do *widoku/udostępnionego* katalogu projektu ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-187">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Shared* directory.</span></span>
1. <span data-ttu-id="63cf5-188">Wybierz plik *_Layout. cshtml* , a następnie wybierz pozycję **Dodaj**, zastępując istniejący plik.</span><span class="sxs-lookup"><span data-stu-id="63cf5-188">Select the *_Layout.cshtml* file, then select **Add**, replacing the existing file.</span></span>

<span data-ttu-id="63cf5-189">W projekcie ASP.NET Core Otwórz plik *_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-189">In the ASP.NET Core project, open the *_Layout.cshtml* file.</span></span> <span data-ttu-id="63cf5-190">Wprowadź następujące zmiany, aby dopasować do pokazanego poniżej kodu:</span><span class="sxs-lookup"><span data-stu-id="63cf5-190">Make the following changes to match the completed code shown below:</span></span>

<span data-ttu-id="63cf5-191">Zaktualizuj dołączenie do początkowego kodu CSS, aby pasowało do pożądanego poniższego szablonu:</span><span class="sxs-lookup"><span data-stu-id="63cf5-191">Update the Bootstrap CSS inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="63cf5-192">Zamień na `@Styles.Render("~/Content/css")` `<link>` element, który ma ładować *Bootstrap. css* (patrz poniżej).</span><span class="sxs-lookup"><span data-stu-id="63cf5-192">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>
1. <span data-ttu-id="63cf5-193">Usuń `@Scripts.Render("~/bundles/modernizr")` .</span><span class="sxs-lookup"><span data-stu-id="63cf5-193">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

<span data-ttu-id="63cf5-194">Zakończony znacznik zastępczy dla dołączania do ładowania początkowego:</span><span class="sxs-lookup"><span data-stu-id="63cf5-194">The completed replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="63cf5-195">Zaktualizuj dołączenie jQuery i Bootstrap w języku JavaScript, aby dopasować do poniższego kodu:</span><span class="sxs-lookup"><span data-stu-id="63cf5-195">Update the jQuery and Bootstrap JavaScript inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="63cf5-196">Zamień `@Scripts.Render("~/bundles/jquery")` na `<script>` element (patrz poniżej).</span><span class="sxs-lookup"><span data-stu-id="63cf5-196">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>
1. <span data-ttu-id="63cf5-197">Zamień `@Scripts.Render("~/bundles/bootstrap")` na `<script>` element (patrz poniżej).</span><span class="sxs-lookup"><span data-stu-id="63cf5-197">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="63cf5-198">Zakończono dołączenie znacznika wymiany dla platformy jQuery i uruchomienia skryptu uruchomieniowego w języku JavaScript:</span><span class="sxs-lookup"><span data-stu-id="63cf5-198">The completed replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="63cf5-199">Zaktualizowany plik *_Layout. cshtml* jest przedstawiony poniżej:</span><span class="sxs-lookup"><span data-stu-id="63cf5-199">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

<span data-ttu-id="63cf5-200">Wyświetl witrynę w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="63cf5-200">View the site in the browser.</span></span> <span data-ttu-id="63cf5-201">Powinien on być renderowany przy użyciu oczekiwanych stylów.</span><span class="sxs-lookup"><span data-stu-id="63cf5-201">It should render with the expected styles in place.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="63cf5-202">Konfigurowanie grupowania i minifikacja</span><span class="sxs-lookup"><span data-stu-id="63cf5-202">Configure bundling and minification</span></span>

<span data-ttu-id="63cf5-203">ASP.NET Core jest zgodny z kilkoma minifikacjaami i rozwiązaniami typu open source, takimi jak [Weboptimize](https://github.com/ligershark/WebOptimizer) i innymi podobnymi bibliotekami.</span><span class="sxs-lookup"><span data-stu-id="63cf5-203">ASP.NET Core is compatible with several open-source bundling and minification solutions such as [WebOptimizer](https://github.com/ligershark/WebOptimizer) and other similar libraries.</span></span> <span data-ttu-id="63cf5-204">ASP.NET Core nie zapewnia natywnego tworzenia i minifikacja rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="63cf5-204">ASP.NET Core doesn't provide a native bundling and minification solution.</span></span> <span data-ttu-id="63cf5-205">Aby uzyskać informacje na temat konfigurowania grupowania i minifikacja, zobacz Tworzenie [i minifikacja](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="63cf5-205">For information on configuring bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="63cf5-206">Rozwiązywanie błędów HTTP 500</span><span class="sxs-lookup"><span data-stu-id="63cf5-206">Solve HTTP 500 errors</span></span>

<span data-ttu-id="63cf5-207">Istnieje wiele problemów, które mogą spowodować wyświetlenie komunikatu o błędzie HTTP 500, który nie zawiera żadnych informacji na temat źródła problemu.</span><span class="sxs-lookup"><span data-stu-id="63cf5-207">There are many problems that can cause an HTTP 500 error message that contains no information on the source of the problem.</span></span> <span data-ttu-id="63cf5-208">Na przykład, jeśli plik *views/_ViewImports. cshtml* zawiera przestrzeń nazw, która nie istnieje w projekcie, generowany jest błąd HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="63cf5-208">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, an HTTP 500 error is generated.</span></span> <span data-ttu-id="63cf5-209">Domyślnie w aplikacjach ASP.NET Core `UseDeveloperExceptionPage` rozszerzenie jest dodawane do `IApplicationBuilder` i wykonywane, gdy środowisko jest *opracowywane*.</span><span class="sxs-lookup"><span data-stu-id="63cf5-209">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the environment is *Development*.</span></span> <span data-ttu-id="63cf5-210">Jest to szczegółowo opisany w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="63cf5-210">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

<span data-ttu-id="63cf5-211">ASP.NET Core konwertuje Nieobsłużone wyjątki na odpowiedzi na błędy HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="63cf5-211">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="63cf5-212">Zwykle szczegóły błędu nie są uwzględniane w tych odpowiedziach, aby zapobiec ujawnieniu potencjalnie poufnych informacji o serwerze.</span><span class="sxs-lookup"><span data-stu-id="63cf5-212">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="63cf5-213">Aby uzyskać więcej informacji, zobacz [stronę wyjątków dla deweloperów](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="63cf5-213">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="next-steps"></a><span data-ttu-id="63cf5-214">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="63cf5-214">Next steps</span></span>

* <xref:migration/identity>

## <a name="additional-resources"></a><span data-ttu-id="63cf5-215">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="63cf5-215">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="63cf5-216">W tym artykule pokazano, jak rozpocząć Migrowanie projektu ASP.NET MVC do [ASP.NET Core MVC](xref:mvc/overview) 2,2.</span><span class="sxs-lookup"><span data-stu-id="63cf5-216">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.2.</span></span> <span data-ttu-id="63cf5-217">W procesie wyróżniamy wiele elementów, które uległy zmianie z ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="63cf5-217">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="63cf5-218">Migrowanie z ASP.NET MVC jest procesem wieloetapowym.</span><span class="sxs-lookup"><span data-stu-id="63cf5-218">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="63cf5-219">W tym artykule omówiono następujące zagadnienia:</span><span class="sxs-lookup"><span data-stu-id="63cf5-219">This article covers:</span></span>

* <span data-ttu-id="63cf5-220">Początkowa konfiguracja</span><span class="sxs-lookup"><span data-stu-id="63cf5-220">Initial setup</span></span>
* <span data-ttu-id="63cf5-221">Podstawowe kontrolery i widoki</span><span class="sxs-lookup"><span data-stu-id="63cf5-221">Basic controllers and views</span></span>
* <span data-ttu-id="63cf5-222">Zawartość statyczna</span><span class="sxs-lookup"><span data-stu-id="63cf5-222">Static content</span></span>
* <span data-ttu-id="63cf5-223">Zależności po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="63cf5-223">Client-side dependencies.</span></span>

<span data-ttu-id="63cf5-224">Aby migrować konfigurację i Identity kod, zobacz <xref:migration/configuration> i <xref:migration/identity> .</span><span class="sxs-lookup"><span data-stu-id="63cf5-224">For migrating configuration and Identity code, see <xref:migration/configuration> and <xref:migration/identity>.</span></span>

> [!NOTE]
> <span data-ttu-id="63cf5-225">Numery wersji w przykładach mogą nie być aktualne, należy odpowiednio zaktualizować projekty.</span><span class="sxs-lookup"><span data-stu-id="63cf5-225">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="63cf5-226">Tworzenie projektu Starter ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="63cf5-226">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="63cf5-227">Aby zademonstrować uaktualnienie, zaczniemy od utworzenia aplikacji ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="63cf5-227">To demonstrate the upgrade, we'll start by creating an ASP.NET MVC app.</span></span> <span data-ttu-id="63cf5-228">Utwórz go przy użyciu nazwy *WebApp1* , aby przestrzeń nazw była zgodna z projektem ASP.NET Core utworzonym w następnym kroku.</span><span class="sxs-lookup"><span data-stu-id="63cf5-228">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Okno dialogowe nowego projektu programu Visual Studio](mvc/_static/new-project.png)

![Okno dialogowe Nowa aplikacja sieci Web: szablon projektu MVC wybrany w panelu szablony ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="63cf5-231">*Opcjonalne:* Zmień nazwę rozwiązania z *WebApp1* na *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="63cf5-231">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="63cf5-232">Program Visual Studio wyświetla nową nazwę rozwiązania (*Mvc5*), która ułatwia poinformowanie tego projektu z następnego projektu.</span><span class="sxs-lookup"><span data-stu-id="63cf5-232">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="63cf5-233">Tworzenie projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="63cf5-233">Create the ASP.NET Core project</span></span>

<span data-ttu-id="63cf5-234">Utwórz nową *pustą* aplikację sieci Web ASP.NET Core o takiej samej nazwie jak w poprzednim projekcie (*WebApp1*), tak aby przestrzenie nazw w obu projektach były zgodne.</span><span class="sxs-lookup"><span data-stu-id="63cf5-234">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="63cf5-235">Posiadanie tego samego obszaru nazw ułatwia kopiowanie kodu między tymi dwoma projektami.</span><span class="sxs-lookup"><span data-stu-id="63cf5-235">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="63cf5-236">Utwórz ten projekt w innym katalogu niż poprzedni projekt, aby użyć tej samej nazwy.</span><span class="sxs-lookup"><span data-stu-id="63cf5-236">Create this project in a different directory than the previous project to use the same name.</span></span>

![Okno dialogowe Nowy projekt](mvc/_static/new_core.png)

![Nowe okno dialogowe aplikacji sieci Web ASP.NET: pusty szablon projektu wybrany w panelu szablony ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="63cf5-239">*Opcjonalne:* Utwórz nową aplikację ASP.NET Core przy użyciu szablonu projektu *aplikacji sieci Web* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-239">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="63cf5-240">Nazwij projekt *WebApp1*i wybierz opcję uwierzytelniania **poszczególnych kont użytkowników**.</span><span class="sxs-lookup"><span data-stu-id="63cf5-240">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="63cf5-241">Zmień nazwę tej aplikacji na *FullAspNetCore*.</span><span class="sxs-lookup"><span data-stu-id="63cf5-241">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="63cf5-242">Utworzenie tego projektu powoduje zaoszczędzenie czasu w konwersji.</span><span class="sxs-lookup"><span data-stu-id="63cf5-242">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="63cf5-243">Wynik końcowy może być wyświetlany w kodzie generowanym przez szablon, kod może być kopiowany do projektu konwersji lub porównywany z projektem generowanym przez szablon.</span><span class="sxs-lookup"><span data-stu-id="63cf5-243">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="63cf5-244">Konfigurowanie lokacji do korzystania z MVC</span><span class="sxs-lookup"><span data-stu-id="63cf5-244">Configure the site to use MVC</span></span>

* <span data-ttu-id="63cf5-245">W przypadku określania platformy .NET Core jest przywoływany domyślny [pakiet Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="63cf5-245">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="63cf5-246">Ten pakiet zawiera pakiety powszechnie używane przez aplikacje MVC.</span><span class="sxs-lookup"><span data-stu-id="63cf5-246">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="63cf5-247">W przypadku .NET Framework określania wartości docelowej odwołania do pakietów muszą być wyszczególnione pojedynczo w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="63cf5-247">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="63cf5-248">`Microsoft.AspNetCore.Mvc` jest platformą ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="63cf5-248">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="63cf5-249">`Microsoft.AspNetCore.StaticFiles` jest programem obsługi plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="63cf5-249">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="63cf5-250">Aplikacje ASP.NET Core jawnie wybierają oprogramowanie pośredniczące, na przykład w celu obsługi plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="63cf5-250">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="63cf5-251">Aby uzyskać więcej informacji, zobacz [pliki statyczne](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="63cf5-251">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="63cf5-252">Otwórz plik *Startup.cs* i Zmień kod w taki sposób, aby był zgodny z następującymi:</span><span class="sxs-lookup"><span data-stu-id="63cf5-252">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="63cf5-253"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>Metoda rozszerzenia dodaje procedurę obsługi pliku statycznego.</span><span class="sxs-lookup"><span data-stu-id="63cf5-253">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="63cf5-254">Aby uzyskać więcej informacji, zobacz Uruchamianie i [Routing](xref:fundamentals/routing) [aplikacji](xref:fundamentals/startup) .</span><span class="sxs-lookup"><span data-stu-id="63cf5-254">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="63cf5-255">Dodawanie kontrolera i widoku</span><span class="sxs-lookup"><span data-stu-id="63cf5-255">Add a controller and view</span></span>

<span data-ttu-id="63cf5-256">W tej sekcji zostanie dodany minimalny kontroler i widok służący jako symbole zastępcze dla kontrolera ASP.NET MVC i widoków migrowanych w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="63cf5-256">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="63cf5-257">Dodaj katalog *kontrolerów* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-257">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="63cf5-258">Dodaj **klasę kontrolera** o nazwie *HomeController.cs* do katalogu *controllers* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-258">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![Okno dialogowe Dodawanie nowego elementu](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="63cf5-260">Dodaj katalog *widoków* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-260">Add a *Views* directory.</span></span>

* <span data-ttu-id="63cf5-261">Dodaj *widok/katalog macierzysty* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-261">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="63cf5-262">Dodaj \*\* Razor Widok\*\* o nazwie *index. cshtml* do *widoków/katalogu macierzystego* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-262">Add a **Razor View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![Okno dialogowe Dodawanie nowego elementu](mvc/_static/view.png)

<span data-ttu-id="63cf5-264">Poniżej przedstawiono strukturę projektu:</span><span class="sxs-lookup"><span data-stu-id="63cf5-264">The project structure is shown below:</span></span>

![Eksplorator rozwiązań pokazywania plików i katalogów WebApp1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="63cf5-266">Zastąp zawartość pliku *viewss/Home/index. cshtml* następującym znacznikiem:</span><span class="sxs-lookup"><span data-stu-id="63cf5-266">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="63cf5-267">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="63cf5-267">Run the app.</span></span>

![Aplikacja internetowa otwarta w przeglądarce Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="63cf5-269">Aby uzyskać więcej informacji, zobacz [Kontrolery](xref:mvc/controllers/actions) i [widoki](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="63cf5-269">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="63cf5-270">Poniższe funkcje wymagają migracji z przykładowego projektu MVC ASP.NET do projektu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="63cf5-270">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="63cf5-271">zawartość po stronie klienta (CSS, Fonts i scripts)</span><span class="sxs-lookup"><span data-stu-id="63cf5-271">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="63cf5-272">kontrolery</span><span class="sxs-lookup"><span data-stu-id="63cf5-272">controllers</span></span>

* <span data-ttu-id="63cf5-273">widoki</span><span class="sxs-lookup"><span data-stu-id="63cf5-273">views</span></span>

* <span data-ttu-id="63cf5-274">modele</span><span class="sxs-lookup"><span data-stu-id="63cf5-274">models</span></span>

* <span data-ttu-id="63cf5-275">tworzenia pakietów</span><span class="sxs-lookup"><span data-stu-id="63cf5-275">bundling</span></span>

* <span data-ttu-id="63cf5-276">filtry</span><span class="sxs-lookup"><span data-stu-id="63cf5-276">filters</span></span>

* <span data-ttu-id="63cf5-277">Logowanie/wylogowywanie, Identity (to jest wykonywane w następnym samouczku).</span><span class="sxs-lookup"><span data-stu-id="63cf5-277">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="63cf5-278">Kontrolery i widoki</span><span class="sxs-lookup"><span data-stu-id="63cf5-278">Controllers and views</span></span>

* <span data-ttu-id="63cf5-279">Skopiuj każdą z metod z ASP.NET MVC `HomeController` do nowej `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="63cf5-279">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="63cf5-280">W ASP.NET MVC typ zwracany metody akcji kontrolera wbudowanego szablonu to <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; w ASP.NET Core MVC, zamiast tego zwracają metody akcji `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="63cf5-280">In ASP.NET MVC, the built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="63cf5-281">`ActionResult` implementuje `IActionResult` , dlatego nie trzeba zmieniać zwracanego typu metod akcji.</span><span class="sxs-lookup"><span data-stu-id="63cf5-281">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="63cf5-282">Skopiuj pliki *. cshtml*, *Contact. cshtml*i *Index. cshtml* Razor z projektu ASP.NET MVC do projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63cf5-282">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="63cf5-283">Przetestuj każdą metodę</span><span class="sxs-lookup"><span data-stu-id="63cf5-283">Test each method</span></span>

<span data-ttu-id="63cf5-284">Plik i style układu nie zostały jeszcze zmigrowane, więc renderowane widoki zawierają tylko zawartość w plikach widoku.</span><span class="sxs-lookup"><span data-stu-id="63cf5-284">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="63cf5-285">Linki do pliku układu dla `About` `Contact` widoków i nie będą jeszcze dostępne.</span><span class="sxs-lookup"><span data-stu-id="63cf5-285">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

<span data-ttu-id="63cf5-286">Wywołaj renderowane widoki z przeglądarki w działającej aplikacji ASP.NET Core, zastępując bieżący numer portu numerem portu używanym w projekcie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63cf5-286">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="63cf5-287">Na przykład: `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="63cf5-287">For example: `https://localhost:44375/home/about`.</span></span>

![Strona kontaktu](mvc/_static/contact-page.png)

<span data-ttu-id="63cf5-289">Zwróć uwagę na brak stylów i elementów menu.</span><span class="sxs-lookup"><span data-stu-id="63cf5-289">Note the lack of styling and menu items.</span></span> <span data-ttu-id="63cf5-290">Styl zostanie rozwiązany w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="63cf5-290">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="63cf5-291">Zawartość statyczna</span><span class="sxs-lookup"><span data-stu-id="63cf5-291">Static content</span></span>

<span data-ttu-id="63cf5-292">W ASP.NET MVC 5 i starszych zawartość statyczna była hostowana z poziomu korzenia projektu sieci Web i była mieszana przy użyciu plików po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="63cf5-292">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="63cf5-293">W ASP.NET Core zawartość statyczna jest hostowana w katalogu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-293">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="63cf5-294">Skopiuj zawartość statyczną z aplikacji ASP.NET MVC do katalogu *wwwroot* w projekcie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63cf5-294">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="63cf5-295">W tej przykładowej konwersji:</span><span class="sxs-lookup"><span data-stu-id="63cf5-295">In this sample conversion:</span></span>

* <span data-ttu-id="63cf5-296">Skopiuj plik *favicon. ico* z projektu ASP.NET MVC do katalogu *wwwroot* w projekcie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63cf5-296">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="63cf5-297">Projekt ASP.NET MVC używa narzędzia [Bootstrap](https://getbootstrap.com/) do jego stylu i przechowuje pliki Bootstrap w katalogach *zawartości* i *skryptów* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-297">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="63cf5-298">Szablon, który wygenerował projekt ASP.NET MVC, odwołuje się do Bootstrap w pliku układu (*views/Shared/_Layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="63cf5-298">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="63cf5-299">Pliki *bootstrap.js* i *Bootstrap. css* można SKOPIOWAĆ z projektu ASP.NET MVC do katalogu *wwwroot* w nowym projekcie.</span><span class="sxs-lookup"><span data-stu-id="63cf5-299">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="63cf5-300">Zamiast tego ten dokument dodaje obsługę ładowania początkowego (i innych bibliotek po stronie klienta) przy użyciu sieci CDN, w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="63cf5-300">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="63cf5-301">Migrowanie pliku układu</span><span class="sxs-lookup"><span data-stu-id="63cf5-301">Migrate the layout file</span></span>

* <span data-ttu-id="63cf5-302">Skopiuj plik *_ViewStart. cshtml* z katalogu *widoków* projektu ASP.NET MVC do katalogu *widoków* projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63cf5-302">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="63cf5-303">Plik *_ViewStart. cshtml* nie został zmieniony w ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="63cf5-303">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="63cf5-304">Utwórz *widok/katalog udostępniony* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-304">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="63cf5-305">*Opcjonalne:* Skopiuj *_ViewImports. cshtml* z katalogu *widoków* projektu *FullAspNetCore* MVC do katalogu *widoków* projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63cf5-305">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="63cf5-306">Usuń deklarację przestrzeni nazw w pliku *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-306">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="63cf5-307">Plik *_ViewImports. cshtml* zawiera przestrzenie nazw dla wszystkich plików widoków i wywołuje [pomocników tagów](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="63cf5-307">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="63cf5-308">Pomocnicy tagów są używani w nowym pliku układu.</span><span class="sxs-lookup"><span data-stu-id="63cf5-308">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="63cf5-309">Plik *_ViewImports. cshtml* jest nowy dla ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63cf5-309">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="63cf5-310">Skopiuj plik *_Layout. cshtml* z *widoku/udostępnionego* projektu ASP.NET MVC do widoku ASP.NET Core projektu */udostępnionego* katalogu.</span><span class="sxs-lookup"><span data-stu-id="63cf5-310">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="63cf5-311">Otwórz plik *_Layout. cshtml* i wprowadź następujące zmiany (kod wykonany jest przedstawiony poniżej):</span><span class="sxs-lookup"><span data-stu-id="63cf5-311">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="63cf5-312">Zamień na `@Styles.Render("~/Content/css")` `<link>` element, który ma ładować *Bootstrap. css* (patrz poniżej).</span><span class="sxs-lookup"><span data-stu-id="63cf5-312">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="63cf5-313">Usuń `@Scripts.Render("~/bundles/modernizr")` .</span><span class="sxs-lookup"><span data-stu-id="63cf5-313">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="63cf5-314">Dodaj komentarz do `@Html.Partial("_LoginPartial")` wiersza (Otocz linią `@*...*@` ).</span><span class="sxs-lookup"><span data-stu-id="63cf5-314">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="63cf5-315">Aby uzyskać więcej informacji, zobacz [Migrowanie uwierzytelniania i Identity ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="63cf5-315">For more information, see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="63cf5-316">Zamień `@Scripts.Render("~/bundles/jquery")` na `<script>` element (patrz poniżej).</span><span class="sxs-lookup"><span data-stu-id="63cf5-316">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="63cf5-317">Zamień `@Scripts.Render("~/bundles/bootstrap")` na `<script>` element (patrz poniżej).</span><span class="sxs-lookup"><span data-stu-id="63cf5-317">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="63cf5-318">Znacznik zastępczy dla dołączania do ładowania początkowego:</span><span class="sxs-lookup"><span data-stu-id="63cf5-318">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="63cf5-319">Znaczniki zamiany dla dołączania jQuery i ładowania początkowego JavaScript:</span><span class="sxs-lookup"><span data-stu-id="63cf5-319">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="63cf5-320">Zaktualizowany plik *_Layout. cshtml* jest przedstawiony poniżej:</span><span class="sxs-lookup"><span data-stu-id="63cf5-320">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="63cf5-321">Wyświetl witrynę w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="63cf5-321">View the site in the browser.</span></span> <span data-ttu-id="63cf5-322">Powinien być teraz poprawnie załadowany przy użyciu oczekiwanych stylów.</span><span class="sxs-lookup"><span data-stu-id="63cf5-322">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="63cf5-323">*Opcjonalne:* Spróbuj użyć nowego pliku układu.</span><span class="sxs-lookup"><span data-stu-id="63cf5-323">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="63cf5-324">Skopiuj plik układu z projektu *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-324">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="63cf5-325">Nowy plik układu używa [pomocników tagów](xref:mvc/views/tag-helpers/intro) i ma inne ulepszenia.</span><span class="sxs-lookup"><span data-stu-id="63cf5-325">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="63cf5-326">Konfigurowanie grupowania i minifikacja</span><span class="sxs-lookup"><span data-stu-id="63cf5-326">Configure bundling and minification</span></span>

<span data-ttu-id="63cf5-327">Aby uzyskać informacje o sposobie konfigurowania grupowania i minifikacja, zobacz Tworzenie [i minifikacja](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="63cf5-327">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="63cf5-328">Rozwiązywanie błędów HTTP 500</span><span class="sxs-lookup"><span data-stu-id="63cf5-328">Solve HTTP 500 errors</span></span>

<span data-ttu-id="63cf5-329">Istnieje wiele problemów, które mogą spowodować, że komunikaty o błędach HTTP 500, które nie zawierają żadnych informacji na temat źródła problemu.</span><span class="sxs-lookup"><span data-stu-id="63cf5-329">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="63cf5-330">Na przykład, jeśli plik *views/_ViewImports. cshtml* zawiera przestrzeń nazw, która nie istnieje w projekcie, generowany jest błąd HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="63cf5-330">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="63cf5-331">Domyślnie w aplikacjach ASP.NET Core `UseDeveloperExceptionPage` rozszerzenie jest dodawane do `IApplicationBuilder` i wykonywane podczas *opracowywania*konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="63cf5-331">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="63cf5-332">Zobacz przykład w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="63cf5-332">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="63cf5-333">ASP.NET Core konwertuje Nieobsłużone wyjątki na odpowiedzi na błędy HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="63cf5-333">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="63cf5-334">Zwykle szczegóły błędu nie są uwzględniane w tych odpowiedziach, aby zapobiec ujawnieniu potencjalnie poufnych informacji o serwerze.</span><span class="sxs-lookup"><span data-stu-id="63cf5-334">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="63cf5-335">Aby uzyskać więcej informacji, zobacz [stronę wyjątków dla deweloperów](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="63cf5-335">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="63cf5-336">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="63cf5-336">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="63cf5-337">W tym artykule pokazano, jak rozpocząć Migrowanie projektu ASP.NET MVC do [ASP.NET Core MVC](xref:mvc/overview) 2,1.</span><span class="sxs-lookup"><span data-stu-id="63cf5-337">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.1.</span></span> <span data-ttu-id="63cf5-338">W procesie wyróżniamy wiele elementów, które uległy zmianie z ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="63cf5-338">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="63cf5-339">Migrowanie z ASP.NET MVC jest procesem wieloetapowym.</span><span class="sxs-lookup"><span data-stu-id="63cf5-339">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="63cf5-340">W tym artykule omówiono następujące zagadnienia:</span><span class="sxs-lookup"><span data-stu-id="63cf5-340">This article covers:</span></span>

* <span data-ttu-id="63cf5-341">Początkowa konfiguracja</span><span class="sxs-lookup"><span data-stu-id="63cf5-341">Initial setup</span></span>
* <span data-ttu-id="63cf5-342">Podstawowe kontrolery i widoki</span><span class="sxs-lookup"><span data-stu-id="63cf5-342">Basic controllers and views</span></span>
* <span data-ttu-id="63cf5-343">Zawartość statyczna</span><span class="sxs-lookup"><span data-stu-id="63cf5-343">Static content</span></span>
* <span data-ttu-id="63cf5-344">Zależności po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="63cf5-344">Client-side dependencies.</span></span>

<span data-ttu-id="63cf5-345">Aby przeprowadzić migrację konfiguracji i Identity kodu, zobacz [Migrowanie konfiguracji do ASP.NET Core](xref:migration/configuration) i [migrowanie uwierzytelniania i Identity ASP.NET Core](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="63cf5-345">For migrating configuration and Identity code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity).</span></span>

> [!NOTE]
> <span data-ttu-id="63cf5-346">Numery wersji w przykładach mogą nie być aktualne, należy odpowiednio zaktualizować projekty.</span><span class="sxs-lookup"><span data-stu-id="63cf5-346">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="63cf5-347">Tworzenie projektu Starter ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="63cf5-347">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="63cf5-348">Aby zademonstrować uaktualnienie, zaczniemy od utworzenia aplikacji ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="63cf5-348">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="63cf5-349">Utwórz go przy użyciu nazwy *WebApp1* , aby przestrzeń nazw była zgodna z projektem ASP.NET Core utworzonym w następnym kroku.</span><span class="sxs-lookup"><span data-stu-id="63cf5-349">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Okno dialogowe nowego projektu programu Visual Studio](mvc/_static/new-project.png)

![Okno dialogowe Nowa aplikacja sieci Web: szablon projektu MVC wybrany w panelu szablony ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="63cf5-352">*Opcjonalne:* Zmień nazwę rozwiązania z *WebApp1* na *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="63cf5-352">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="63cf5-353">Program Visual Studio wyświetla nową nazwę rozwiązania (*Mvc5*), która ułatwia poinformowanie tego projektu z następnego projektu.</span><span class="sxs-lookup"><span data-stu-id="63cf5-353">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="63cf5-354">Tworzenie projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="63cf5-354">Create the ASP.NET Core project</span></span>

<span data-ttu-id="63cf5-355">Utwórz nową *pustą* aplikację sieci Web ASP.NET Core o takiej samej nazwie jak w poprzednim projekcie (*WebApp1*), tak aby przestrzenie nazw w obu projektach były zgodne.</span><span class="sxs-lookup"><span data-stu-id="63cf5-355">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="63cf5-356">Posiadanie tego samego obszaru nazw ułatwia kopiowanie kodu między tymi dwoma projektami.</span><span class="sxs-lookup"><span data-stu-id="63cf5-356">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="63cf5-357">Utwórz ten projekt w innym katalogu niż poprzedni projekt, aby użyć tej samej nazwy.</span><span class="sxs-lookup"><span data-stu-id="63cf5-357">Create this project in a different directory than the previous project to use the same name.</span></span>

![Okno dialogowe Nowy projekt](mvc/_static/new_core.png)

![Nowe okno dialogowe aplikacji sieci Web ASP.NET: pusty szablon projektu wybrany w panelu szablony ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="63cf5-360">*Opcjonalne:* Utwórz nową aplikację ASP.NET Core przy użyciu szablonu projektu *aplikacji sieci Web* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-360">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="63cf5-361">Nazwij projekt *WebApp1*i wybierz opcję uwierzytelniania **poszczególnych kont użytkowników**.</span><span class="sxs-lookup"><span data-stu-id="63cf5-361">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="63cf5-362">Zmień nazwę tej aplikacji na *FullAspNetCore*.</span><span class="sxs-lookup"><span data-stu-id="63cf5-362">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="63cf5-363">Utworzenie tego projektu powoduje zaoszczędzenie czasu w konwersji.</span><span class="sxs-lookup"><span data-stu-id="63cf5-363">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="63cf5-364">Wynik końcowy może być wyświetlany w kodzie generowanym przez szablon, kod może być kopiowany do projektu konwersji lub porównywany z projektem generowanym przez szablon.</span><span class="sxs-lookup"><span data-stu-id="63cf5-364">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="63cf5-365">Konfigurowanie lokacji do korzystania z MVC</span><span class="sxs-lookup"><span data-stu-id="63cf5-365">Configure the site to use MVC</span></span>

* <span data-ttu-id="63cf5-366">W przypadku określania platformy .NET Core jest przywoływany domyślny [pakiet Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="63cf5-366">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="63cf5-367">Ten pakiet zawiera pakiety powszechnie używane przez aplikacje MVC.</span><span class="sxs-lookup"><span data-stu-id="63cf5-367">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="63cf5-368">W przypadku .NET Framework określania wartości docelowej odwołania do pakietów muszą być wyszczególnione pojedynczo w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="63cf5-368">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="63cf5-369">`Microsoft.AspNetCore.Mvc` jest platformą ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="63cf5-369">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="63cf5-370">`Microsoft.AspNetCore.StaticFiles` jest programem obsługi plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="63cf5-370">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="63cf5-371">Aplikacje ASP.NET Core jawnie wybierają oprogramowanie pośredniczące, na przykład w celu obsługi plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="63cf5-371">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="63cf5-372">Aby uzyskać więcej informacji, zobacz [pliki statyczne](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="63cf5-372">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="63cf5-373">Otwórz plik *Startup.cs* i Zmień kod w taki sposób, aby był zgodny z następującymi:</span><span class="sxs-lookup"><span data-stu-id="63cf5-373">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="63cf5-374"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>Metoda rozszerzenia dodaje procedurę obsługi pliku statycznego.</span><span class="sxs-lookup"><span data-stu-id="63cf5-374">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="63cf5-375">`UseMvc`Metoda rozszerzenia dodaje Routing.</span><span class="sxs-lookup"><span data-stu-id="63cf5-375">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="63cf5-376">Aby uzyskać więcej informacji, zobacz Uruchamianie i [Routing](xref:fundamentals/routing) [aplikacji](xref:fundamentals/startup) .</span><span class="sxs-lookup"><span data-stu-id="63cf5-376">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="63cf5-377">Dodawanie kontrolera i widoku</span><span class="sxs-lookup"><span data-stu-id="63cf5-377">Add a controller and view</span></span>

<span data-ttu-id="63cf5-378">W tej sekcji zostanie dodany minimalny kontroler i widok służący jako symbole zastępcze dla kontrolera ASP.NET MVC i widoków migrowanych w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="63cf5-378">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="63cf5-379">Dodaj katalog *kontrolerów* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-379">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="63cf5-380">Dodaj **klasę kontrolera** o nazwie *HomeController.cs* do katalogu *controllers* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-380">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![Okno dialogowe Dodawanie nowego elementu](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="63cf5-382">Dodaj katalog *widoków* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-382">Add a *Views* directory.</span></span>

* <span data-ttu-id="63cf5-383">Dodaj *widok/katalog macierzysty* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-383">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="63cf5-384">Dodaj \*\* Razor Widok\*\* o nazwie *index. cshtml* do *widoków/katalogu macierzystego* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-384">Add a **Razor View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![Okno dialogowe Dodawanie nowego elementu](mvc/_static/view.png)

<span data-ttu-id="63cf5-386">Poniżej przedstawiono strukturę projektu:</span><span class="sxs-lookup"><span data-stu-id="63cf5-386">The project structure is shown below:</span></span>

![Eksplorator rozwiązań pokazywania plików i katalogów WebApp1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="63cf5-388">Zastąp zawartość pliku *viewss/Home/index. cshtml* następującym znacznikiem:</span><span class="sxs-lookup"><span data-stu-id="63cf5-388">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="63cf5-389">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="63cf5-389">Run the app.</span></span>

![Aplikacja internetowa otwarta w przeglądarce Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="63cf5-391">Aby uzyskać więcej informacji, zobacz [Kontrolery](xref:mvc/controllers/actions) i [widoki](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="63cf5-391">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="63cf5-392">Poniższe funkcje wymagają migracji z przykładowego projektu MVC ASP.NET do projektu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="63cf5-392">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="63cf5-393">zawartość po stronie klienta (CSS, Fonts i scripts)</span><span class="sxs-lookup"><span data-stu-id="63cf5-393">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="63cf5-394">kontrolery</span><span class="sxs-lookup"><span data-stu-id="63cf5-394">controllers</span></span>

* <span data-ttu-id="63cf5-395">widoki</span><span class="sxs-lookup"><span data-stu-id="63cf5-395">views</span></span>

* <span data-ttu-id="63cf5-396">modele</span><span class="sxs-lookup"><span data-stu-id="63cf5-396">models</span></span>

* <span data-ttu-id="63cf5-397">tworzenia pakietów</span><span class="sxs-lookup"><span data-stu-id="63cf5-397">bundling</span></span>

* <span data-ttu-id="63cf5-398">filtry</span><span class="sxs-lookup"><span data-stu-id="63cf5-398">filters</span></span>

* <span data-ttu-id="63cf5-399">Logowanie/wylogowywanie, Identity (to jest wykonywane w następnym samouczku).</span><span class="sxs-lookup"><span data-stu-id="63cf5-399">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="63cf5-400">Kontrolery i widoki</span><span class="sxs-lookup"><span data-stu-id="63cf5-400">Controllers and views</span></span>

* <span data-ttu-id="63cf5-401">Skopiuj każdą z metod z ASP.NET MVC `HomeController` do nowej `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="63cf5-401">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="63cf5-402">W ASP.NET MVC typ zwracany metody akcji kontrolera wbudowanego szablonu to <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; w ASP.NET Core MVC, zamiast tego zwracają metody akcji `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="63cf5-402">In ASP.NET MVC, the built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="63cf5-403">`ActionResult` implementuje `IActionResult` , dlatego nie trzeba zmieniać zwracanego typu metod akcji.</span><span class="sxs-lookup"><span data-stu-id="63cf5-403">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="63cf5-404">Skopiuj pliki *. cshtml*, *Contact. cshtml*i *Index. cshtml* Razor z projektu ASP.NET MVC do projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63cf5-404">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="63cf5-405">Przetestuj każdą metodę</span><span class="sxs-lookup"><span data-stu-id="63cf5-405">Test each method</span></span>

<span data-ttu-id="63cf5-406">Plik i style układu nie zostały jeszcze zmigrowane, więc renderowane widoki zawierają tylko zawartość w plikach widoku.</span><span class="sxs-lookup"><span data-stu-id="63cf5-406">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="63cf5-407">Linki do pliku układu dla `About` `Contact` widoków i nie będą jeszcze dostępne.</span><span class="sxs-lookup"><span data-stu-id="63cf5-407">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

* <span data-ttu-id="63cf5-408">Wywołaj renderowane widoki z przeglądarki w działającej aplikacji ASP.NET Core, zastępując bieżący numer portu numerem portu używanym w projekcie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63cf5-408">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="63cf5-409">Na przykład: `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="63cf5-409">For example: `https://localhost:44375/home/about`.</span></span>

![Strona kontaktu](mvc/_static/contact-page.png)

<span data-ttu-id="63cf5-411">Zwróć uwagę na brak stylów i elementów menu.</span><span class="sxs-lookup"><span data-stu-id="63cf5-411">Note the lack of styling and menu items.</span></span> <span data-ttu-id="63cf5-412">Styl zostanie rozwiązany w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="63cf5-412">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="63cf5-413">Zawartość statyczna</span><span class="sxs-lookup"><span data-stu-id="63cf5-413">Static content</span></span>

<span data-ttu-id="63cf5-414">W ASP.NET MVC 5 i starszych zawartość statyczna była hostowana z poziomu korzenia projektu sieci Web i była mieszana przy użyciu plików po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="63cf5-414">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="63cf5-415">W ASP.NET Core zawartość statyczna jest hostowana w katalogu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-415">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="63cf5-416">Skopiuj zawartość statyczną z aplikacji ASP.NET MVC do katalogu *wwwroot* w projekcie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63cf5-416">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="63cf5-417">W tej przykładowej konwersji:</span><span class="sxs-lookup"><span data-stu-id="63cf5-417">In this sample conversion:</span></span>

* <span data-ttu-id="63cf5-418">Skopiuj plik *favicon. ico* z projektu ASP.NET MVC do katalogu *wwwroot* w projekcie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63cf5-418">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="63cf5-419">Projekt ASP.NET MVC używa narzędzia [Bootstrap](https://getbootstrap.com/) do jego stylu i przechowuje pliki Bootstrap w katalogach *zawartości* i *skryptów* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-419">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="63cf5-420">Szablon, który wygenerował projekt ASP.NET MVC, odwołuje się do Bootstrap w pliku układu (*views/Shared/_Layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="63cf5-420">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="63cf5-421">Pliki *bootstrap.js* i *Bootstrap. css* można SKOPIOWAĆ z projektu ASP.NET MVC do katalogu *wwwroot* w nowym projekcie.</span><span class="sxs-lookup"><span data-stu-id="63cf5-421">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="63cf5-422">Zamiast tego ten dokument dodaje obsługę ładowania początkowego (i innych bibliotek po stronie klienta) przy użyciu sieci CDN, w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="63cf5-422">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="63cf5-423">Migrowanie pliku układu</span><span class="sxs-lookup"><span data-stu-id="63cf5-423">Migrate the layout file</span></span>

* <span data-ttu-id="63cf5-424">Skopiuj plik *_ViewStart. cshtml* z katalogu *widoków* projektu ASP.NET MVC do katalogu *widoków* projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63cf5-424">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="63cf5-425">Plik *_ViewStart. cshtml* nie został zmieniony w ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="63cf5-425">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="63cf5-426">Utwórz *widok/katalog udostępniony* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-426">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="63cf5-427">*Opcjonalne:* Skopiuj *_ViewImports. cshtml* z katalogu *widoków* projektu *FullAspNetCore* MVC do katalogu *widoków* projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63cf5-427">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="63cf5-428">Usuń deklarację przestrzeni nazw w pliku *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-428">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="63cf5-429">Plik *_ViewImports. cshtml* zawiera przestrzenie nazw dla wszystkich plików widoków i wywołuje [pomocników tagów](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="63cf5-429">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="63cf5-430">Pomocnicy tagów są używani w nowym pliku układu.</span><span class="sxs-lookup"><span data-stu-id="63cf5-430">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="63cf5-431">Plik *_ViewImports. cshtml* jest nowy dla ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63cf5-431">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="63cf5-432">Skopiuj plik *_Layout. cshtml* z *widoku/udostępnionego* projektu ASP.NET MVC do widoku ASP.NET Core projektu */udostępnionego* katalogu.</span><span class="sxs-lookup"><span data-stu-id="63cf5-432">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="63cf5-433">Otwórz plik *_Layout. cshtml* i wprowadź następujące zmiany (kod wykonany jest przedstawiony poniżej):</span><span class="sxs-lookup"><span data-stu-id="63cf5-433">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="63cf5-434">Zamień na `@Styles.Render("~/Content/css")` `<link>` element, który ma ładować *Bootstrap. css* (patrz poniżej).</span><span class="sxs-lookup"><span data-stu-id="63cf5-434">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="63cf5-435">Usuń `@Scripts.Render("~/bundles/modernizr")` .</span><span class="sxs-lookup"><span data-stu-id="63cf5-435">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="63cf5-436">Dodaj komentarz do `@Html.Partial("_LoginPartial")` wiersza (Otocz linią `@*...*@` ).</span><span class="sxs-lookup"><span data-stu-id="63cf5-436">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="63cf5-437">Aby uzyskać więcej informacji, zobacz [Migrowanie uwierzytelniania i Identity ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="63cf5-437">For more information, see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="63cf5-438">Zamień `@Scripts.Render("~/bundles/jquery")` na `<script>` element (patrz poniżej).</span><span class="sxs-lookup"><span data-stu-id="63cf5-438">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="63cf5-439">Zamień `@Scripts.Render("~/bundles/bootstrap")` na `<script>` element (patrz poniżej).</span><span class="sxs-lookup"><span data-stu-id="63cf5-439">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="63cf5-440">Znacznik zastępczy dla dołączania do ładowania początkowego:</span><span class="sxs-lookup"><span data-stu-id="63cf5-440">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="63cf5-441">Znaczniki zamiany dla dołączania jQuery i ładowania początkowego JavaScript:</span><span class="sxs-lookup"><span data-stu-id="63cf5-441">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="63cf5-442">Zaktualizowany plik *_Layout. cshtml* jest przedstawiony poniżej:</span><span class="sxs-lookup"><span data-stu-id="63cf5-442">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="63cf5-443">Wyświetl witrynę w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="63cf5-443">View the site in the browser.</span></span> <span data-ttu-id="63cf5-444">Powinien być teraz poprawnie załadowany przy użyciu oczekiwanych stylów.</span><span class="sxs-lookup"><span data-stu-id="63cf5-444">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="63cf5-445">*Opcjonalne:* Spróbuj użyć nowego pliku układu.</span><span class="sxs-lookup"><span data-stu-id="63cf5-445">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="63cf5-446">Skopiuj plik układu z projektu *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="63cf5-446">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="63cf5-447">Nowy plik układu używa [pomocników tagów](xref:mvc/views/tag-helpers/intro) i ma inne ulepszenia.</span><span class="sxs-lookup"><span data-stu-id="63cf5-447">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="63cf5-448">Konfigurowanie grupowania i minifikacja</span><span class="sxs-lookup"><span data-stu-id="63cf5-448">Configure bundling and minification</span></span>

<span data-ttu-id="63cf5-449">Aby uzyskać informacje o sposobie konfigurowania grupowania i minifikacja, zobacz Tworzenie [i minifikacja](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="63cf5-449">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="63cf5-450">Rozwiązywanie błędów HTTP 500</span><span class="sxs-lookup"><span data-stu-id="63cf5-450">Solve HTTP 500 errors</span></span>

<span data-ttu-id="63cf5-451">Istnieje wiele problemów, które mogą spowodować, że komunikaty o błędach HTTP 500, które nie zawierają żadnych informacji na temat źródła problemu.</span><span class="sxs-lookup"><span data-stu-id="63cf5-451">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="63cf5-452">Na przykład, jeśli plik *views/_ViewImports. cshtml* zawiera przestrzeń nazw, która nie istnieje w projekcie, generowany jest błąd HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="63cf5-452">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="63cf5-453">Domyślnie w aplikacjach ASP.NET Core `UseDeveloperExceptionPage` rozszerzenie jest dodawane do `IApplicationBuilder` i wykonywane podczas *opracowywania*konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="63cf5-453">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="63cf5-454">Zobacz przykład w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="63cf5-454">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="63cf5-455">ASP.NET Core konwertuje Nieobsłużone wyjątki na odpowiedzi na błędy HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="63cf5-455">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="63cf5-456">Zwykle szczegóły błędu nie są uwzględniane w tych odpowiedziach, aby zapobiec ujawnieniu potencjalnie poufnych informacji o serwerze.</span><span class="sxs-lookup"><span data-stu-id="63cf5-456">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="63cf5-457">Aby uzyskać więcej informacji, zobacz [stronę wyjątków dla deweloperów](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="63cf5-457">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="63cf5-458">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="63cf5-458">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
