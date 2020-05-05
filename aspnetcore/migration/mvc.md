---
title: Migrowanie z ASP.NET MVC do ASP.NET Core MVC
author: ardalis
description: Dowiedz się, jak rozpocząć Migrowanie projektu ASP.NET MVC do ASP.NET Core MVC.
ms.author: riande
ms.date: 04/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/mvc
ms.openlocfilehash: 59a10c002958e5f719dbd59686f21df69da5f43e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777049"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="bf9a4-103">Migrowanie z ASP.NET MVC do ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="bf9a4-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

<span data-ttu-id="bf9a4-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Kowalski](https://ardalis.com/)i [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="bf9a4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="bf9a4-105">W tym artykule pokazano, jak rozpocząć Migrowanie projektu ASP.NET MVC do [ASP.NET Core MVC](../mvc/overview.md).</span><span class="sxs-lookup"><span data-stu-id="bf9a4-105">This article shows how to get started migrating an ASP.NET MVC project to [ASP.NET Core MVC](../mvc/overview.md).</span></span> <span data-ttu-id="bf9a4-106">W procesie wyróżniamy wiele elementów, które uległy zmianie z ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-106">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="bf9a4-107">Migrowanie z ASP.NET MVC jest procesem wielu kroków, a w tym artykule omówiono początkową konfigurację, podstawowe kontrolery i widoki, zawartość statyczną i zależności po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-107">Migrating from ASP.NET MVC is a multiple step process and this article covers the initial setup, basic controllers and views, static content, and client-side dependencies.</span></span> <span data-ttu-id="bf9a4-108">Dodatkowe artykuły obejmują Migrowanie konfiguracji i kodu tożsamości znalezionych w wielu projektach ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-108">Additional articles cover migrating configuration and identity code found in many ASP.NET MVC projects.</span></span>

> [!NOTE]
> <span data-ttu-id="bf9a4-109">Numery wersji w przykładach mogą być nieaktualne.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-109">The version numbers in the samples might not be current.</span></span> <span data-ttu-id="bf9a4-110">Może być konieczne odpowiednio zaktualizowanie projektów.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-110">You may need to update your projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="bf9a4-111">Tworzenie projektu Starter ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="bf9a4-111">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="bf9a4-112">Aby zademonstrować uaktualnienie, zaczniemy od utworzenia aplikacji ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-112">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="bf9a4-113">Utwórz go przy użyciu nazwy *WebApp1* , tak aby przestrzeń nazw była zgodna z ASP.NET Core projektem tworzonym w następnym kroku.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-113">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project we create in the next step.</span></span>

![Okno dialogowe nowego projektu programu Visual Studio](mvc/_static/new-project.png)

![Okno dialogowe Nowa aplikacja sieci Web: szablon projektu MVC wybrany w panelu szablony ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="bf9a4-116">*Opcjonalne:* Zmień nazwę rozwiązania z *WebApp1* na *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-116">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="bf9a4-117">Program Visual Studio wyświetla nową nazwę rozwiązania (*Mvc5*), która ułatwia poinformowanie tego projektu z następnego projektu.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-117">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="bf9a4-118">Tworzenie projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf9a4-118">Create the ASP.NET Core project</span></span>

<span data-ttu-id="bf9a4-119">Utwórz nową *pustą* aplikację sieci Web ASP.NET Core o takiej samej nazwie jak w poprzednim projekcie (*WebApp1*), tak aby przestrzenie nazw w obu projektach były zgodne.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-119">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="bf9a4-120">Posiadanie tego samego obszaru nazw ułatwia kopiowanie kodu między tymi dwoma projektami.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-120">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="bf9a4-121">Musisz utworzyć ten projekt w innym katalogu niż poprzedni projekt, aby użyć tej samej nazwy.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-121">You'll have to create this project in a different directory than the previous project to use the same name.</span></span>

![Okno dialogowe Nowy projekt](mvc/_static/new_core.png)

![Nowe okno dialogowe aplikacji sieci Web ASP.NET: pusty szablon projektu wybrany w panelu szablony ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="bf9a4-124">*Opcjonalne:* Utwórz nową aplikację ASP.NET Core przy użyciu szablonu projektu *aplikacji sieci Web* .</span><span class="sxs-lookup"><span data-stu-id="bf9a4-124">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="bf9a4-125">Nazwij projekt *WebApp1*i wybierz opcję uwierzytelniania **poszczególnych kont użytkowników**.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-125">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="bf9a4-126">Zmień nazwę tej aplikacji na *FullAspNetCore*.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-126">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="bf9a4-127">Utworzenie tego projektu umożliwia zaoszczędzenie czasu w konwersji.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-127">Creating this project saves you time in the conversion.</span></span> <span data-ttu-id="bf9a4-128">Można przyjrzeć się kodowi generowanemu przez szablon, aby zobaczyć wynik końcowy lub skopiować kod do projektu konwersji.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-128">You can look at the template-generated code to see the end result or to copy code to the conversion project.</span></span> <span data-ttu-id="bf9a4-129">Jest on również przydatny w przypadku zablokowania kroku konwersji w celu porównania z projektem wygenerowanym przez szablon.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-129">It's also helpful when you get stuck on a conversion step to compare with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="bf9a4-130">Konfigurowanie lokacji do korzystania z MVC</span><span class="sxs-lookup"><span data-stu-id="bf9a4-130">Configure the site to use MVC</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="bf9a4-131">W przypadku określania platformy .NET Core jest przywoływany domyślny [pakiet Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="bf9a4-131">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="bf9a4-132">Ten pakiet zawiera pakiety powszechnie używane przez aplikacje MVC.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-132">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="bf9a4-133">W przypadku .NET Framework określania wartości docelowej odwołania do pakietów muszą być wyszczególnione pojedynczo w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-133">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="bf9a4-134">W przypadku określania platformy .NET Core do programu jest przywoływany [pakiet Microsoft. AspNetCore. All](xref:fundamentals/metapackage) .</span><span class="sxs-lookup"><span data-stu-id="bf9a4-134">When targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) is referenced by default.</span></span> <span data-ttu-id="bf9a4-135">Ten pakiet zawiera pakiety powszechnie używane przez aplikacje MVC.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-135">This package contains packages commonly used packages by MVC apps.</span></span> <span data-ttu-id="bf9a4-136">W przypadku .NET Framework określania wartości docelowej odwołania do pakietów muszą być wyszczególnione pojedynczo w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-136">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="bf9a4-137">W przypadku określania wartości docelowej .NET Core lub .NET Framework pakiety często używane przez aplikacje MVC są wymieniane pojedynczo w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-137">When targeting .NET Core or .NET Framework, packages commonly used packages by MVC apps are listed individually in the project file.</span></span>

::: moniker-end

<span data-ttu-id="bf9a4-138">`Microsoft.AspNetCore.Mvc`jest platformą ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-138">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="bf9a4-139">`Microsoft.AspNetCore.StaticFiles`jest programem obsługi plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-139">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="bf9a4-140">Środowisko uruchomieniowe ASP.NET Core jest modularne i należy jawnie zadecydować, aby obsługiwały pliki statyczne (zobacz [pliki statyczne](xref:fundamentals/static-files)).</span><span class="sxs-lookup"><span data-stu-id="bf9a4-140">The ASP.NET Core runtime is modular, and you must explicitly opt in to serve static files (see [Static files](xref:fundamentals/static-files)).</span></span>

* <span data-ttu-id="bf9a4-141">Otwórz plik *Startup.cs* i Zmień kod w taki sposób, aby był zgodny z następującymi:</span><span class="sxs-lookup"><span data-stu-id="bf9a4-141">Open the *Startup.cs* file and change the code to match the following:</span></span>

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

<span data-ttu-id="bf9a4-142">Metoda `UseStaticFiles` rozszerzenia dodaje procedurę obsługi pliku statycznego.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-142">The `UseStaticFiles` extension method adds the static file handler.</span></span> <span data-ttu-id="bf9a4-143">Jak wspomniano wcześniej, środowisko uruchomieniowe ASP.NET jest modularne, a użytkownik musi jawnie zadecydować, aby obsługiwał pliki statyczne.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-143">As mentioned previously, the ASP.NET runtime is modular, and you must explicitly opt in to serve static files.</span></span> <span data-ttu-id="bf9a4-144">Metoda `UseMvc` rozszerzenia dodaje Routing.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-144">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="bf9a4-145">Aby uzyskać więcej informacji, zobacz Uruchamianie i [Routing](xref:fundamentals/routing) [aplikacji](xref:fundamentals/startup) .</span><span class="sxs-lookup"><span data-stu-id="bf9a4-145">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="bf9a4-146">Dodawanie kontrolera i widoku</span><span class="sxs-lookup"><span data-stu-id="bf9a4-146">Add a controller and view</span></span>

<span data-ttu-id="bf9a4-147">W tej sekcji dodasz minimalny kontroler i widok służący jako symbole zastępcze dla kontrolera ASP.NET MVC i widoków, które zostaną zmigrowane w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-147">In this section, you'll add a minimal controller and view to serve as placeholders for the ASP.NET MVC controller and views you'll migrate in the next section.</span></span>

* <span data-ttu-id="bf9a4-148">Dodaj folder *controllers* .</span><span class="sxs-lookup"><span data-stu-id="bf9a4-148">Add a *Controllers* folder.</span></span>

* <span data-ttu-id="bf9a4-149">Dodaj **klasę kontrolera** o nazwie *HomeController.cs* do folderu *controllers* .</span><span class="sxs-lookup"><span data-stu-id="bf9a4-149">Add a **Controller Class** named *HomeController.cs* to the *Controllers* folder.</span></span>

![Okno dialogowe Dodawanie nowego elementu](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="bf9a4-151">Dodaj folder *widoki* .</span><span class="sxs-lookup"><span data-stu-id="bf9a4-151">Add a *Views* folder.</span></span>

* <span data-ttu-id="bf9a4-152">Dodaj *Widok/folder macierzysty* .</span><span class="sxs-lookup"><span data-stu-id="bf9a4-152">Add a *Views/Home* folder.</span></span>

* <span data-ttu-id="bf9a4-153">Dodaj \*\* Razor widok\*\* o nazwie *index. cshtml* do folderu *widoki/Home* .</span><span class="sxs-lookup"><span data-stu-id="bf9a4-153">Add a **Razor View** named *Index.cshtml* to the *Views/Home* folder.</span></span>

![Okno dialogowe Dodawanie nowego elementu](mvc/_static/view.png)

<span data-ttu-id="bf9a4-155">Poniżej przedstawiono strukturę projektu:</span><span class="sxs-lookup"><span data-stu-id="bf9a4-155">The project structure is shown below:</span></span>

![Eksplorator rozwiązań pokazywania plików i folderów WebApp1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="bf9a4-157">Zastąp zawartość pliku *viewss/Home/index. cshtml* następującym:</span><span class="sxs-lookup"><span data-stu-id="bf9a4-157">Replace the contents of the *Views/Home/Index.cshtml* file with the following:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="bf9a4-158">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-158">Run the app.</span></span>

![Aplikacja internetowa otwarta w przeglądarce Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="bf9a4-160">Aby uzyskać więcej informacji, zobacz [Kontrolery](xref:mvc/controllers/actions) i [widoki](xref:mvc/views/overview) .</span><span class="sxs-lookup"><span data-stu-id="bf9a4-160">See [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview) for more information.</span></span>

<span data-ttu-id="bf9a4-161">Teraz, gdy mamy minimalny działający projekt ASP.NET Core, możemy rozpocząć Migrowanie funkcji z projektu MVC ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-161">Now that we have a minimal working ASP.NET Core project, we can start migrating functionality from the ASP.NET MVC project.</span></span> <span data-ttu-id="bf9a4-162">Musimy przenieść następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="bf9a4-162">We need to move the following:</span></span>

* <span data-ttu-id="bf9a4-163">zawartość po stronie klienta (CSS, Fonts i scripts)</span><span class="sxs-lookup"><span data-stu-id="bf9a4-163">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="bf9a4-164">kontrolery</span><span class="sxs-lookup"><span data-stu-id="bf9a4-164">controllers</span></span>

* <span data-ttu-id="bf9a4-165">widoki</span><span class="sxs-lookup"><span data-stu-id="bf9a4-165">views</span></span>

* <span data-ttu-id="bf9a4-166">modele</span><span class="sxs-lookup"><span data-stu-id="bf9a4-166">models</span></span>

* <span data-ttu-id="bf9a4-167">tworzenia pakietów</span><span class="sxs-lookup"><span data-stu-id="bf9a4-167">bundling</span></span>

* <span data-ttu-id="bf9a4-168">filtry</span><span class="sxs-lookup"><span data-stu-id="bf9a4-168">filters</span></span>

* <span data-ttu-id="bf9a4-169">Logowanie/wylogowywanie, Identity (to jest wykonywane w następnym samouczku).</span><span class="sxs-lookup"><span data-stu-id="bf9a4-169">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="bf9a4-170">Kontrolery i widoki</span><span class="sxs-lookup"><span data-stu-id="bf9a4-170">Controllers and views</span></span>

* <span data-ttu-id="bf9a4-171">Skopiuj każdą z metod z ASP.NET MVC `HomeController` do nowej. `HomeController`</span><span class="sxs-lookup"><span data-stu-id="bf9a4-171">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="bf9a4-172">Należy pamiętać, że w ASP.NET MVC Metoda akcji kontrolera wbudowanego szablonu ma wartość [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); w ASP.NET Core MVC, zamiast tego zwracają `IActionResult` metody akcji.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-172">Note that in ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="bf9a4-173">`ActionResult`implementuje `IActionResult`, dlatego nie trzeba zmieniać zwracanego typu metod akcji.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-173">`ActionResult` implements `IActionResult`, so there's no need to change the return type of your action methods.</span></span>

* <span data-ttu-id="bf9a4-174">Skopiuj pliki *. cshtml*, *Contact. cshtml*i *index. cshtml* Razor z projektu ASP.NET MVC do projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-174">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

* <span data-ttu-id="bf9a4-175">Uruchom aplikację ASP.NET Core i przetestuj każdą metodę.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-175">Run the ASP.NET Core app and test each method.</span></span> <span data-ttu-id="bf9a4-176">Plik lub style układu nie został jeszcze zmigrowany, więc renderowane widoki zawierają tylko zawartość w plikach widoku.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-176">We haven't migrated the layout file or styles yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="bf9a4-177">Nie masz linków do pliku układu dla widoków `About` i `Contact` , więc musisz wywołać je z przeglądarki (Zastąp **4492** numerem portu używanym w projekcie).</span><span class="sxs-lookup"><span data-stu-id="bf9a4-177">You won't have the layout file generated links for the `About` and `Contact` views, so you'll have to invoke them from the browser (replace **4492** with the port number used in your project).</span></span>

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Strona kontaktu](mvc/_static/contact-page.png)

<span data-ttu-id="bf9a4-179">Zwróć uwagę na brak stylów i elementów menu.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-179">Note the lack of styling and menu items.</span></span> <span data-ttu-id="bf9a4-180">Naprawimy to w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-180">We'll fix that in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="bf9a4-181">Zawartość statyczna</span><span class="sxs-lookup"><span data-stu-id="bf9a4-181">Static content</span></span>

<span data-ttu-id="bf9a4-182">W poprzednich wersjach ASP.NET MVC zawartość statyczna była hostowana z poziomu korzenia projektu sieci Web i była mieszana z plikami po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-182">In previous versions of ASP.NET MVC, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="bf9a4-183">W ASP.NET Core zawartość statyczna jest hostowana w folderze *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="bf9a4-183">In ASP.NET Core, static content is hosted in the *wwwroot* folder.</span></span> <span data-ttu-id="bf9a4-184">Chcesz skopiować zawartość statyczną ze starej aplikacji ASP.NET MVC do folderu *wwwroot* w projekcie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-184">You'll want to copy the static content from your old ASP.NET MVC app to the *wwwroot* folder in your ASP.NET Core project.</span></span> <span data-ttu-id="bf9a4-185">W tej przykładowej konwersji:</span><span class="sxs-lookup"><span data-stu-id="bf9a4-185">In this sample conversion:</span></span>

* <span data-ttu-id="bf9a4-186">Skopiuj plik *favicon. ico* ze starego projektu MVC do folderu *wwwroot* w projekcie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-186">Copy the *favicon.ico* file from the old MVC project to the *wwwroot* folder in the ASP.NET Core project.</span></span>

<span data-ttu-id="bf9a4-187">Stary projekt ASP.NET MVC używa narzędzia [Bootstrap](https://getbootstrap.com/) do jego stylu i zapisuje pliki Bootstrap w folderach *zawartości* i *skryptów* .</span><span class="sxs-lookup"><span data-stu-id="bf9a4-187">The old ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* folders.</span></span> <span data-ttu-id="bf9a4-188">Szablon, który wygenerował stary projekt ASP.NET MVC, odwołuje się do Bootstrap w pliku układu (*views/Shared/_Layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="bf9a4-188">The template, which generated the old ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="bf9a4-189">Można skopiować pliki *Bootstrap. js* i *Bootstrap. css* z projektu ASP.NET MVC do folderu *wwwroot* w nowym projekcie.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-189">You could copy the *bootstrap.js* and *bootstrap.css* files from the ASP.NET MVC project to the *wwwroot* folder in the new project.</span></span> <span data-ttu-id="bf9a4-190">Zamiast tego dodamy obsługę ładowania początkowego (i innych bibliotek po stronie klienta) przy użyciu sieci CDN w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-190">Instead, we'll add support for Bootstrap (and other client-side libraries) using CDNs in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="bf9a4-191">Migrowanie pliku układu</span><span class="sxs-lookup"><span data-stu-id="bf9a4-191">Migrate the layout file</span></span>

* <span data-ttu-id="bf9a4-192">Skopiuj plik *_ViewStart. cshtml* ze starego folderu *widoków* projektu ASP.NET MVC do folderu *widoków* projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-192">Copy the *_ViewStart.cshtml* file from the old ASP.NET MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="bf9a4-193">Plik *_ViewStart. cshtml* nie został zmieniony w ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-193">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="bf9a4-194">Utwórz *Widok/folder udostępniony* .</span><span class="sxs-lookup"><span data-stu-id="bf9a4-194">Create a *Views/Shared* folder.</span></span>

* <span data-ttu-id="bf9a4-195">*Opcjonalne:* Skopiuj *_ViewImports. cshtml* z folderu *widoków* projektu *FullAspNetCore* MVC do folderu *widoków* projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-195">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="bf9a4-196">Usuń deklarację przestrzeni nazw w pliku *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="bf9a4-196">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="bf9a4-197">Plik *_ViewImports. cshtml* zawiera przestrzenie nazw dla wszystkich plików widoków i wywołuje [pomocników tagów](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="bf9a4-197">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="bf9a4-198">Pomocnicy tagów są używani w nowym pliku układu.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-198">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="bf9a4-199">Plik *_ViewImports. cshtml* jest nowy dla ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-199">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="bf9a4-200">Skopiuj plik *_Layout. cshtml* ze starego *widoku/folderu udostępnionego* projektu MVC ASP.NET do folderu *widoki/udostępniony* projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-200">Copy the *_Layout.cshtml* file from the old ASP.NET MVC project's *Views/Shared* folder into the ASP.NET Core project's *Views/Shared* folder.</span></span>

<span data-ttu-id="bf9a4-201">Otwórz plik *_Layout. cshtml* i wprowadź następujące zmiany (kod wykonany jest przedstawiony poniżej):</span><span class="sxs-lookup"><span data-stu-id="bf9a4-201">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="bf9a4-202">Zamień `@Styles.Render("~/Content/css")` na `<link>` element, który ma ładować *Bootstrap. css* (patrz poniżej).</span><span class="sxs-lookup"><span data-stu-id="bf9a4-202">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="bf9a4-203">Usuń `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-203">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="bf9a4-204">Dodaj komentarz do `@Html.Partial("_LoginPartial")` wiersza (Otocz linią `@*...*@`).</span><span class="sxs-lookup"><span data-stu-id="bf9a4-204">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="bf9a4-205">Aby uzyskać więcej informacji [, zobacz Migrowanie uwierzytelniania Identity i ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="bf9a4-205">For more information see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="bf9a4-206">Zamień `@Scripts.Render("~/bundles/jquery")` na `<script>` element (patrz poniżej).</span><span class="sxs-lookup"><span data-stu-id="bf9a4-206">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="bf9a4-207">Zamień `@Scripts.Render("~/bundles/bootstrap")` na `<script>` element (patrz poniżej).</span><span class="sxs-lookup"><span data-stu-id="bf9a4-207">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="bf9a4-208">Znacznik zastępczy dla dołączania do ładowania początkowego:</span><span class="sxs-lookup"><span data-stu-id="bf9a4-208">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="bf9a4-209">Znaczniki zamiany dla dołączania jQuery i ładowania początkowego JavaScript:</span><span class="sxs-lookup"><span data-stu-id="bf9a4-209">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="bf9a4-210">Zaktualizowany plik *_Layout. cshtml* jest przedstawiony poniżej:</span><span class="sxs-lookup"><span data-stu-id="bf9a4-210">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="bf9a4-211">Wyświetl witrynę w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-211">View the site in the browser.</span></span> <span data-ttu-id="bf9a4-212">Powinien być teraz poprawnie załadowany przy użyciu oczekiwanych stylów.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-212">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="bf9a4-213">*Opcjonalne:* Warto spróbować użyć nowego pliku układu.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-213">*Optional:* You might want to try using the new layout file.</span></span> <span data-ttu-id="bf9a4-214">Dla tego projektu można skopiować plik układu z projektu *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="bf9a4-214">For this project you can copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="bf9a4-215">Nowy plik układu używa [pomocników tagów](xref:mvc/views/tag-helpers/intro) i ma inne ulepszenia.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-215">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="bf9a4-216">Konfigurowanie grupowania i minifikacja</span><span class="sxs-lookup"><span data-stu-id="bf9a4-216">Configure bundling and minification</span></span>

<span data-ttu-id="bf9a4-217">Aby uzyskać informacje o sposobie konfigurowania grupowania i minifikacja, zobacz Tworzenie [i minifikacja](../client-side/bundling-and-minification.md).</span><span class="sxs-lookup"><span data-stu-id="bf9a4-217">For information about how to configure bundling and minification, see [Bundling and Minification](../client-side/bundling-and-minification.md).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="bf9a4-218">Rozwiązywanie błędów HTTP 500</span><span class="sxs-lookup"><span data-stu-id="bf9a4-218">Solve HTTP 500 errors</span></span>

<span data-ttu-id="bf9a4-219">Istnieje wiele problemów, które mogą spowodować wyświetlenie komunikatu o błędzie HTTP 500, który nie zawiera żadnych informacji na temat źródła problemu.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-219">There are many problems that can cause a HTTP 500 error message that contain no information on the source of the problem.</span></span> <span data-ttu-id="bf9a4-220">Na przykład, jeśli plik *views/_ViewImports. cshtml* zawiera przestrzeń nazw, która nie istnieje w projekcie, zostanie wyświetlony błąd HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-220">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in your project, you'll get a HTTP 500 error.</span></span> <span data-ttu-id="bf9a4-221">Domyślnie w aplikacjach ASP.NET Core `UseDeveloperExceptionPage` rozszerzenie jest dodawane do `IApplicationBuilder` i wykonywane podczas *opracowywania*konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-221">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="bf9a4-222">Jest to szczegółowo opisany w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="bf9a4-222">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

<span data-ttu-id="bf9a4-223">ASP.NET Core konwertuje Nieobsłużone wyjątki w aplikacji sieci Web do odpowiedzi na błędy HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-223">ASP.NET Core converts unhandled exceptions in a web app into HTTP 500 error responses.</span></span> <span data-ttu-id="bf9a4-224">Zwykle szczegóły błędu nie są uwzględniane w tych odpowiedziach, aby zapobiec ujawnieniu potencjalnie poufnych informacji o serwerze.</span><span class="sxs-lookup"><span data-stu-id="bf9a4-224">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="bf9a4-225">Aby uzyskać więcej informacji **, zobacz Używanie strony wyjątków dla deweloperów** w temacie [Obsługa błędów](../fundamentals/error-handling.md) .</span><span class="sxs-lookup"><span data-stu-id="bf9a4-225">See **Using the Developer Exception Page** in [Handle errors](../fundamentals/error-handling.md) for more information.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bf9a4-226">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="bf9a4-226">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
