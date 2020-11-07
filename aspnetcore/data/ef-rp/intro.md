---
title: ':::no-loc(Razor)::: Strony z Entity Framework Core w ASP.NET Core — samouczek 1 z 8'
author: rick-anderson
description: 'Pokazuje, jak utworzyć :::no-loc(Razor)::: aplikację stron przy użyciu Entity Framework Core'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: data/ef-rp/intro
ms.openlocfilehash: 5849f4bfb9d0355177ceb5c2f4236c6d39dd9e92
ms.sourcegitcommit: bb475e69cb647f22cf6d2c6f93d0836c160080d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "94340039"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="28cd8-103">:::no-loc(Razor)::: Strony z Entity Framework Core w ASP.NET Core — samouczek 1 z 8</span><span class="sxs-lookup"><span data-stu-id="28cd8-103">:::no-loc(Razor)::: Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="28cd8-104">Autorzy [Dykstra](https://github.com/tdykstra) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="28cd8-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="28cd8-105">Jest to pierwsza z serii samouczków, które pokazują, jak używać rdzenia Entity Framework (EF) w aplikacji [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="28cd8-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="28cd8-106">Samouczki budują witrynę sieci Web dla fikcyjnej uczelni firmy Contoso.</span><span class="sxs-lookup"><span data-stu-id="28cd8-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="28cd8-107">Lokacja obejmuje funkcje, takie jak przyjmowanie uczniów, tworzenie kursu i przydziały instruktora.</span><span class="sxs-lookup"><span data-stu-id="28cd8-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="28cd8-108">Samouczek używa metody Code First.</span><span class="sxs-lookup"><span data-stu-id="28cd8-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="28cd8-109">Aby uzyskać informacje na temat korzystania z pierwszego podejścia do bazy danych, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/16897)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="28cd8-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="28cd8-110">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="28cd8-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="28cd8-111">[Instrukcje pobierania](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="28cd8-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="28cd8-112">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="28cd8-112">Prerequisites</span></span>

* <span data-ttu-id="28cd8-113">Jeśli :::no-loc(Razor)::: dopiero zaczynasz korzystać z stron, przejdź do serii samouczek wprowadzenie [do :::no-loc(Razor)::: stron](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="28cd8-113">If you're new to :::no-loc(Razor)::: Pages, go through the [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="28cd8-114">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28cd8-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="28cd8-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28cd8-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="28cd8-116">Aparaty bazy danych</span><span class="sxs-lookup"><span data-stu-id="28cd8-116">Database engines</span></span>

<span data-ttu-id="28cd8-117">Instrukcje programu Visual Studio używają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), wersji SQL Server Express, która działa tylko w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="28cd8-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="28cd8-118">Instrukcje Visual Studio Code korzystają z [oprogramowania SQLite](https://www.sqlite.org/), wieloplatformowego aparatu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="28cd8-119">Jeśli zdecydujesz się na korzystanie z oprogramowania SQLite, Pobierz i zainstaluj narzędzie innej firmy służące do zarządzania bazą danych programu SQLite i wyświetlania jej, na przykład w [przeglądarce DB dla oprogramowania SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="28cd8-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="28cd8-120">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="28cd8-120">Troubleshooting</span></span>

<span data-ttu-id="28cd8-121">Jeśli wystąpi problem, którego nie można rozwiązać, porównaj swój kod z [zakończonym projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="28cd8-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="28cd8-122">Dobrym sposobem uzyskania pomocy jest opublikowanie pytania do StackOverflow.com przy użyciu [tagu ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [tagu EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="28cd8-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="28cd8-123">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="28cd8-123">The sample app</span></span>

<span data-ttu-id="28cd8-124">Aplikacja skompilowana w tych samouczkach jest podstawową szkołą w sieci Web.</span><span class="sxs-lookup"><span data-stu-id="28cd8-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="28cd8-125">Użytkownicy mogą wyświetlać i aktualizować informacje dotyczące uczniów, kursów i instruktorów.</span><span class="sxs-lookup"><span data-stu-id="28cd8-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="28cd8-126">Poniżej przedstawiono kilka ekranów utworzonych w samouczku.</span><span class="sxs-lookup"><span data-stu-id="28cd8-126">Here are a few of the screens created in the tutorial.</span></span>

![Strona indeksu uczniów](intro/_static/students-index30.png)

![Strona edycji uczniów](intro/_static/student-edit30.png)

<span data-ttu-id="28cd8-129">Styl interfejsu użytkownika tej witryny jest oparty na wbudowanych szablonach projektów.</span><span class="sxs-lookup"><span data-stu-id="28cd8-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="28cd8-130">Fokus samouczka polega na tym, jak używać EF Core z ASP.NET Core, a nie sposobu dostosowywania interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="28cd8-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

<!-- 
Follow the link at the top of the page to get the source code for the completed project. The *cu50* folder has the code for the ASP.NET Core 5.0 version of the tutorial. Files that reflect the state of the code for tutorials 1-7 can be found in the *cu50snapshots* folder.

# [Visual Studio](#tab/visual-studio)

To run the app after downloading the completed project:

* Build the project.
* In Package Manager Console (PMC) run the following command:

  ```powershell
  Update-Database
  ```

* Run the project to seed the database.

# [Visual Studio Code](#tab/visual-studio-code)

To run the app after downloading the completed project:

* In *Program.cs*, remove the comments from `// webBuilder.UseStartup<StartupSQLite>();`  so `StartupSQLite` is used.
* Copy the contents of *appSettingsSQLite.json* into *appSettings.json*.
* Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.
* Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.
* Build the project.
* At a command prompt in the project folder, run the following commands:

  ```dotnetcli
  dotnet tool install --global dotnet-ef -v 5.0.0-*
  dotnet ef database update
  ```

* In your SQLite tool, run the following SQL statement:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Run the project to seed the database.

---

-->

## <a name="create-the-web-app-project"></a><span data-ttu-id="28cd8-131">Tworzenie projektu aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="28cd8-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="28cd8-132">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28cd8-132">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="28cd8-133">Uruchom program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-133">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="28cd8-134">W oknie dialogowym **Tworzenie nowego projektu** wybierz pozycję **ASP.NET Core aplikacja sieci Web** > **dalej**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-134">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="28cd8-135">W oknie dialogowym **Konfigurowanie nowego projektu** wprowadź wartość `ContosoUniversity` w polu **Nazwa projektu**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-135">In the **Configure your new project** dialog, enter `ContosoUniversity` for **Project name**.</span></span> <span data-ttu-id="28cd8-136">Ważne jest, aby użyć tej dokładnej nazwy, z uwzględnieniem wielkich liter, więc każdy pasuje do tego, `namespace` kiedy kod jest kopiowany.</span><span class="sxs-lookup"><span data-stu-id="28cd8-136">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="28cd8-137">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-137">Select **Create**.</span></span>
1. <span data-ttu-id="28cd8-138">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz:</span><span class="sxs-lookup"><span data-stu-id="28cd8-138">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="28cd8-139">**Platformy .NET Core** i **ASP.NET Core 5,0** na liście rozwijanej.</span><span class="sxs-lookup"><span data-stu-id="28cd8-139">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="28cd8-140">**ASP.NET Core aplikacji sieci Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-140">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="28cd8-141">**Create** 
       Utwórz ![ Nowe okno dialogowe projektu ASP.NET Core](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="28cd8-141">**Create**
![New ASP.NET Core Project dialog](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span></span>
    
# <a name="visual-studio-code"></a>[<span data-ttu-id="28cd8-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28cd8-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="28cd8-143">W terminalu przejdź do folderu, w którym ma zostać utworzony folder projektu.</span><span class="sxs-lookup"><span data-stu-id="28cd8-143">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="28cd8-144">Uruchom następujące polecenia, aby utworzyć :::no-loc(Razor)::: Projekt strony i `cd` do nowego folderu projektu:</span><span class="sxs-lookup"><span data-stu-id="28cd8-144">Run the following commands to create a :::no-loc(Razor)::: Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="28cd8-145">Konfigurowanie stylu witryny</span><span class="sxs-lookup"><span data-stu-id="28cd8-145">Set up the site style</span></span>

<span data-ttu-id="28cd8-146">Skopiuj i wklej następujący kod do pliku *Pages/Shared/_Layout. cshtml* : [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span><span class="sxs-lookup"><span data-stu-id="28cd8-146">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span></span>

<span data-ttu-id="28cd8-147">Plik układu ustawia nagłówek, stopkę i menu witryny.</span><span class="sxs-lookup"><span data-stu-id="28cd8-147">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="28cd8-148">Poprzedni kod wprowadza następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="28cd8-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="28cd8-149">Każde wystąpienie "ContosoUniversity" na "University-Uniwersytet".</span><span class="sxs-lookup"><span data-stu-id="28cd8-149">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="28cd8-150">Istnieją trzy wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="28cd8-150">There are three occurrences.</span></span>
* <span data-ttu-id="28cd8-151">Wpisy menu **Home** i **privacy** są usuwane.</span><span class="sxs-lookup"><span data-stu-id="28cd8-151">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="28cd8-152">Wpisy są **dodawane do programu** , **studentów** , **kursów** , **instruktorów** i **działów**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-152">Entries are added for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="28cd8-153">W obszarze *Pages/index. cshtml* Zastąp zawartość pliku następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="28cd8-153">In *Pages/Index.cshtml* , replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="28cd8-154">Poprzedni kod zastępuje tekst dotyczący ASP.NET Core tekstem dla tej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-154">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="28cd8-155">Uruchom aplikację, aby sprawdzić, czy zostanie wyświetlona strona główna.</span><span class="sxs-lookup"><span data-stu-id="28cd8-155">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="28cd8-156">Model danych</span><span class="sxs-lookup"><span data-stu-id="28cd8-156">The data model</span></span>

<span data-ttu-id="28cd8-157">W poniższych sekcjach opisano tworzenie modelu danych:</span><span class="sxs-lookup"><span data-stu-id="28cd8-157">The following sections create a data model:</span></span>

![Kurs — Diagram modelu danych ucznia](intro/_static/data-model-diagram.png)

<span data-ttu-id="28cd8-159">Student może zarejestrować się w dowolnej liczbie kursów, a kurs może mieć dowolną liczbę studentów, którzy zarejestrowali się w nim.</span><span class="sxs-lookup"><span data-stu-id="28cd8-159">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="28cd8-160">Jednostka ucznia</span><span class="sxs-lookup"><span data-stu-id="28cd8-160">The Student entity</span></span>

![Diagram jednostek uczniów](intro/_static/student-entity.png)

* <span data-ttu-id="28cd8-162">Utwórz folder *models* w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="28cd8-162">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="28cd8-163">Utwórz *modele/uczniów. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="28cd8-163">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="28cd8-164">`ID`Właściwość zostanie przestała kolumną klucza podstawowego tabeli bazy danych, która odnosi się do tej klasy.</span><span class="sxs-lookup"><span data-stu-id="28cd8-164">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="28cd8-165">Domyślnie EF Core interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="28cd8-165">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="28cd8-166">W związku z tym alternatywną automatycznie rozpoznawaną nazwą `Student` klucza podstawowego klasy jest `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-166">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="28cd8-167">Aby uzyskać więcej informacji, zobacz [EF Core-Keys](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="28cd8-167">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="28cd8-168">`Enrollments`Właściwość jest [właściwością nawigacji](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="28cd8-168">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="28cd8-169">Właściwości nawigacji zawierają inne jednostki, które są powiązane z tą jednostką.</span><span class="sxs-lookup"><span data-stu-id="28cd8-169">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="28cd8-170">W takim przypadku `Enrollments` Właściwość `Student` jednostki zawiera wszystkie `Enrollment` jednostki, które są powiązane z tym uczniem.</span><span class="sxs-lookup"><span data-stu-id="28cd8-170">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="28cd8-171">Na przykład jeśli wiersz student w bazie danych ma dwa powiązane wiersze rejestracji, `Enrollments` Właściwość nawigacji zawiera te dwie jednostki rejestracji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-171">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="28cd8-172">W bazie danych wiersz rejestracji jest powiązany z wierszem studenta, jeśli jego kolumna StudentID zawiera wartość identyfikatora studenta.</span><span class="sxs-lookup"><span data-stu-id="28cd8-172">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="28cd8-173">Załóżmy na przykład, że wiersz student ma identyfikator = 1.</span><span class="sxs-lookup"><span data-stu-id="28cd8-173">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="28cd8-174">Powiązane wiersze rejestracji będą mieć StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="28cd8-174">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="28cd8-175">StudentID jest *kluczem obcym* w tabeli rejestracji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-175">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="28cd8-176">`Enrollments`Właściwość jest zdefiniowana tak, `ICollection<Enrollment>` ponieważ może istnieć wiele powiązanych jednostek rejestracji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-176">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="28cd8-177">Można użyć innych typów kolekcji, takich jak `List<Enrollment>` lub `HashSet<Enrollment>` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-177">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="28cd8-178">Gdy `ICollection<Enrollment>` jest używany, EF Core domyślnie tworzy `HashSet<Enrollment>` kolekcję.</span><span class="sxs-lookup"><span data-stu-id="28cd8-178">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="28cd8-179">Jednostka rejestracji</span><span class="sxs-lookup"><span data-stu-id="28cd8-179">The Enrollment entity</span></span>

![Diagram jednostek rejestracji](intro/_static/enrollment-entity.png)

<span data-ttu-id="28cd8-181">Utwórz *modele/rejestrację. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="28cd8-181">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="28cd8-182">`EnrollmentID`Właściwość jest kluczem podstawowym; ta jednostka używa `classnameID` wzorca zamiast `ID` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="28cd8-182">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="28cd8-183">Dla modelu danych produkcyjnych wybierz jeden wzorzec i użyj go spójnie.</span><span class="sxs-lookup"><span data-stu-id="28cd8-183">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="28cd8-184">Ten samouczek używa obu tych metod, aby zilustrować, że obie działają.</span><span class="sxs-lookup"><span data-stu-id="28cd8-184">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="28cd8-185">Używanie `ID` bez `classname` ułatwi implementowanie niektórych rodzajów zmian modelu danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-185">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="28cd8-186">`Grade`Właściwość jest `enum` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-186">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="28cd8-187">Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` Właściwość [dopuszcza wartość null](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="28cd8-187">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="28cd8-188">Klasa, która ma wartość null, różni się od zerowej klasy zerowej &mdash; oznacza, że Klasa nie jest znana lub nie została jeszcze przypisana.</span><span class="sxs-lookup"><span data-stu-id="28cd8-188">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="28cd8-189">`StudentID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Student` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-189">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="28cd8-190">`Enrollment`Jednostka jest skojarzona z jedną `Student` jednostką, więc Właściwość zawiera jedną `Student` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="28cd8-190">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="28cd8-191">`CourseID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Course` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-191">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="28cd8-192">`Enrollment`Jednostka jest skojarzona z jedną `Course` jednostką.</span><span class="sxs-lookup"><span data-stu-id="28cd8-192">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="28cd8-193">EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwę `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-193">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="28cd8-194">Na przykład `StudentID` jest kluczem obcym dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-194">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="28cd8-195">Właściwości klucza obcego mogą być również nazwane `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-195">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="28cd8-196">Na przykład, `CourseID` ponieważ `Course` klucz podstawowy jednostki to `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-196">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="28cd8-197">Jednostka kursu</span><span class="sxs-lookup"><span data-stu-id="28cd8-197">The Course entity</span></span>

![Diagram jednostek kursu](intro/_static/course-entity.png)

<span data-ttu-id="28cd8-199">Utwórz *modele/kurs. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="28cd8-199">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="28cd8-200">`Enrollments`Właściwość jest właściwością nawigacji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-200">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="28cd8-201">`Course`Jednostka może być powiązana z dowolną liczbą `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="28cd8-201">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="28cd8-202">Ten `DatabaseGenerated` atrybut umożliwia aplikacji określenie klucza podstawowego zamiast wygenerowania go przez bazę danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-202">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="28cd8-203">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="28cd8-203">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="28cd8-204">Strony uczniów tworzenia szkieletu</span><span class="sxs-lookup"><span data-stu-id="28cd8-204">Scaffold Student pages</span></span>

<span data-ttu-id="28cd8-205">W tej sekcji użyjesz narzędzia do tworzenia szkieletów ASP.NET Core do wygenerowania:</span><span class="sxs-lookup"><span data-stu-id="28cd8-205">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="28cd8-206">Klasa EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-206">An EF Core `DbContext` class.</span></span> <span data-ttu-id="28cd8-207">Kontekst jest klasą główną, która koordynuje Entity Framework funkcji dla danego modelu danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-207">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="28cd8-208">Pochodzi od <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> klasy.</span><span class="sxs-lookup"><span data-stu-id="28cd8-208">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="28cd8-209">:::no-loc(Razor)::: strony obsługujące operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) dla `Student` jednostki.</span><span class="sxs-lookup"><span data-stu-id="28cd8-209">:::no-loc(Razor)::: pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="28cd8-210">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28cd8-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="28cd8-211">Utwórz folder *stron/uczniów* .</span><span class="sxs-lookup"><span data-stu-id="28cd8-211">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="28cd8-212">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy folder *strony/uczniowie* i wybierz polecenie **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-212">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="28cd8-213">W oknie dialogowym **Dodaj nowy element szkieletowy** :</span><span class="sxs-lookup"><span data-stu-id="28cd8-213">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="28cd8-214">Na lewej karcie Wybierz pozycję **zainstalowane > typowe :::no-loc(Razor)::: strony >**</span><span class="sxs-lookup"><span data-stu-id="28cd8-214">In the left tab, select **Installed > Common > :::no-loc(Razor)::: Pages**</span></span>
  * <span data-ttu-id="28cd8-215">Wybierz **:::no-loc(Razor)::: strony przy użyciu Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-215">Select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="28cd8-216">Na **stronie Dodawanie :::no-loc(Razor)::: stron przy użyciu Entity Framework (CRUD)** okna dialogowego:</span><span class="sxs-lookup"><span data-stu-id="28cd8-216">In the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="28cd8-217">Z listy rozwijanej **Klasa modelu** wybierz pozycję **student (ContosoUniversity. models)**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-217">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="28cd8-218">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus).</span><span class="sxs-lookup"><span data-stu-id="28cd8-218">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="28cd8-219">Zmień nazwę kontekstu danych na End `SchoolContext` zamiast `ContosoUniversityContext` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-219">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="28cd8-220">Nazwa zaktualizowanego kontekstu: `ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="28cd8-220">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="28cd8-221">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-221">Select **Add**.</span></span>

<span data-ttu-id="28cd8-222">Następujące pakiety są instalowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="28cd8-222">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="28cd8-223">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28cd8-223">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="28cd8-224">Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core, aby zainstalować wymagane pakiety NuGet:</span><span class="sxs-lookup"><span data-stu-id="28cd8-224">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="28cd8-225">Pakiet Microsoft. VisualStudio. Web. CodeGeneration. Design jest wymagany do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="28cd8-225">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="28cd8-226">Mimo że aplikacja nie będzie używać SQL Server, narzędzie do tworzenia szkieletów musi być pakietem SQL Server.</span><span class="sxs-lookup"><span data-stu-id="28cd8-226">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="28cd8-227">Utwórz folder *stron/uczniów* .</span><span class="sxs-lookup"><span data-stu-id="28cd8-227">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="28cd8-228">Uruchom następujące polecenie, aby zainstalować narzędzie do tworzenia [szkieletu ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="28cd8-228">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="28cd8-229">Uruchom następujące polecenie, aby uzyskać możliwość tworzenia szkieletu stron z uczniami.</span><span class="sxs-lookup"><span data-stu-id="28cd8-229">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="28cd8-230">**W systemie Windows**</span><span class="sxs-lookup"><span data-stu-id="28cd8-230">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="28cd8-231">**W systemie macOS lub Linux**</span><span class="sxs-lookup"><span data-stu-id="28cd8-231">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="28cd8-232">Jeśli poprzedni krok zakończy się niepowodzeniem, Skompiluj projekt i ponów próbę wykonania szkieletu.</span><span class="sxs-lookup"><span data-stu-id="28cd8-232">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="28cd8-233">Proces tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="28cd8-233">The scaffolding process:</span></span>

* <span data-ttu-id="28cd8-234">Tworzy :::no-loc(Razor)::: strony w folderze *stron/uczniów* :</span><span class="sxs-lookup"><span data-stu-id="28cd8-234">Creates :::no-loc(Razor)::: pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="28cd8-235">*Create. cshtml* i *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="28cd8-235">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="28cd8-236">*DELETE. cshtml* i *DELETE.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="28cd8-236">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="28cd8-237">*Details. cshtml* i *details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="28cd8-237">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="28cd8-238">*Edit. cshtml* i *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="28cd8-238">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="28cd8-239">*Index. cshtml* i *index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="28cd8-239">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="28cd8-240">Tworzy *dane/SchoolContext. cs*.</span><span class="sxs-lookup"><span data-stu-id="28cd8-240">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="28cd8-241">Dodaje kontekst do iniekcji zależności w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="28cd8-241">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="28cd8-242">Dodaje parametry połączenia z bazą danych do programu *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="28cd8-242">Adds a database connection string to *:::no-loc(appsettings.json):::*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="28cd8-243">Parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="28cd8-243">Database connection string</span></span>

<span data-ttu-id="28cd8-244">Narzędzie do tworzenia szkieletu generuje parametry połączenia w *:::no-loc(appsettings.json):::* pliku.</span><span class="sxs-lookup"><span data-stu-id="28cd8-244">The scaffolding tool generates a connection string in the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="28cd8-245">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28cd8-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="28cd8-246">Parametry połączenia określają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span><span class="sxs-lookup"><span data-stu-id="28cd8-246">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/:::no-loc(appsettings.json):::?highlight=11)]

<span data-ttu-id="28cd8-247">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użycia w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="28cd8-247">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="28cd8-248">Domyślnie LocalDB tworzy pliki *MDF* w `C:/Users/<user>` katalogu.</span><span class="sxs-lookup"><span data-stu-id="28cd8-248">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="28cd8-249">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28cd8-249">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="28cd8-250">Skróć parametry połączenia oprogramowania SQLite do *bazy danych cu. DB* :</span><span class="sxs-lookup"><span data-stu-id="28cd8-250">Shorten the SQLite connection string to *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="28cd8-251">Aktualizowanie klasy kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="28cd8-251">Update the database context class</span></span>

<span data-ttu-id="28cd8-252">Klasa główna, która koordynuje funkcje EF Core dla danego modelu danych, jest klasą kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-252">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="28cd8-253">Kontekst pochodzi od [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="28cd8-253">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="28cd8-254">Kontekst określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-254">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="28cd8-255">W tym projekcie Klasa ma nazwę `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-255">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="28cd8-256">Zaktualizuj *dane/SchoolContext. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="28cd8-256">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="28cd8-257">Poprzedni kod zmienia się z pojedynczej `DbSet<Student> Student` na plural `DbSet<Student> Students` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-257">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="28cd8-258">Aby :::no-loc(Razor)::: kod stron był zgodny z nową `DBSet` nazwą, wprowadź globalną zmianę z: `_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="28cd8-258">To make the :::no-loc(Razor)::: Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="28cd8-259">do `_context.Students.`</span><span class="sxs-lookup"><span data-stu-id="28cd8-259">to: `_context.Students.`</span></span>

<span data-ttu-id="28cd8-260">Istnieją 8 wystąpień.</span><span class="sxs-lookup"><span data-stu-id="28cd8-260">There are 8 occurrences.</span></span>

<span data-ttu-id="28cd8-261">Ponieważ zestaw jednostek zawiera wiele jednostek, wielu deweloperów preferuje `DBSet` nazwy właściwości.</span><span class="sxs-lookup"><span data-stu-id="28cd8-261">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="28cd8-262">Wyróżniony kod:</span><span class="sxs-lookup"><span data-stu-id="28cd8-262">The highlighted code:</span></span>

* <span data-ttu-id="28cd8-263">Tworzy właściwość [nieogólnymi \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla każdego zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="28cd8-263">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="28cd8-264">W EF Core terminologii:</span><span class="sxs-lookup"><span data-stu-id="28cd8-264">In EF Core terminology:</span></span>
  * <span data-ttu-id="28cd8-265">Zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-265">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="28cd8-266">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="28cd8-266">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="28cd8-267">Wywołania <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .</span><span class="sxs-lookup"><span data-stu-id="28cd8-267">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="28cd8-268">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="28cd8-268">`OnModelCreating`:</span></span>
  * <span data-ttu-id="28cd8-269">Jest wywoływana, gdy została `SchoolContext` zainicjowana, ale zanim model zostanie zablokowany i użyty do zainicjowania kontekstu.</span><span class="sxs-lookup"><span data-stu-id="28cd8-269">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="28cd8-270">Jest wymagany, ponieważ w dalszej części samouczka `Student` jednostka będzie miała odwołania do innych jednostek.</span><span class="sxs-lookup"><span data-stu-id="28cd8-270">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="28cd8-271">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="28cd8-271">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="28cd8-272">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="28cd8-272">Startup.cs</span></span>

<span data-ttu-id="28cd8-273">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="28cd8-273">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="28cd8-274">Usługi takie jak `SchoolContext` są rejestrowane przy użyciu iniekcji zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-274">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="28cd8-275">Składniki, które wymagają tych usług, takie jak :::no-loc(Razor)::: strony, są dostarczane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="28cd8-275">Components that require these services, such as :::no-loc(Razor)::: Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="28cd8-276">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="28cd8-276">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="28cd8-277">Narzędzie do tworzenia szkieletów automatycznie zarejestrowało klasę kontekstu z kontenerem iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="28cd8-277">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="28cd8-278">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28cd8-278">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="28cd8-279">Program szkieleter dodał następujące wyróżnione wiersze:</span><span class="sxs-lookup"><span data-stu-id="28cd8-279">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="28cd8-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28cd8-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="28cd8-281">Sprawdź kod dodany przez wywołania szkieletu `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-281">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="28cd8-282">Aby uzyskać informacje na temat korzystania z produkcyjnej bazy danych, zobacz temat [Korzystanie z oprogramowania SQLite na potrzeby programowania. SQL Server do produkcji](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) .</span><span class="sxs-lookup"><span data-stu-id="28cd8-282">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="28cd8-283">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="28cd8-283">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="28cd8-284">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *:::no-loc(appsettings.json):::* pliku.</span><span class="sxs-lookup"><span data-stu-id="28cd8-284">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="28cd8-285">Dodaj filtr wyjątku bazy danych</span><span class="sxs-lookup"><span data-stu-id="28cd8-285">Add the database exception filter</span></span>

<span data-ttu-id="28cd8-286">Dodaj <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> do `ConfigureServices` , jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="28cd8-286">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="28cd8-287">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28cd8-287">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="28cd8-288">Dodaj pakiet NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) .</span><span class="sxs-lookup"><span data-stu-id="28cd8-288">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="28cd8-289">W obszarze PMC wprowadź następujące polecenie, aby dodać pakiet NuGet:</span><span class="sxs-lookup"><span data-stu-id="28cd8-289">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="28cd8-290">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28cd8-290">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="28cd8-291">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore`Pakiet NuGet zawiera ASP.NET Core oprogramowanie pośredniczące dla Entity Framework Core stron błędów.</span><span class="sxs-lookup"><span data-stu-id="28cd8-291">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="28cd8-292">To oprogramowanie pośredniczące pomaga wykrywać i diagnozować błędy za pomocą migracji Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="28cd8-292">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="28cd8-293">`AddDatabaseDeveloperPageExceptionFilter`Zapewnia przydatne informacje o błędzie w [środowisku programistycznym](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="28cd8-293">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="28cd8-294">Tworzenie bazy danych</span><span class="sxs-lookup"><span data-stu-id="28cd8-294">Create the database</span></span>

<span data-ttu-id="28cd8-295">Zaktualizuj *program.cs* , aby utworzyć bazę danych, jeśli nie istnieje:</span><span class="sxs-lookup"><span data-stu-id="28cd8-295">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="28cd8-296">Jeśli istnieje baza danych dla kontekstu, Metoda [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) nie przyjmuje żadnej akcji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-296">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="28cd8-297">Jeśli baza danych nie istnieje, tworzy bazę danych i schemat.</span><span class="sxs-lookup"><span data-stu-id="28cd8-297">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="28cd8-298">`EnsureCreated` umożliwia korzystanie z następującego przepływu pracy w celu obsługi zmian modelu danych:</span><span class="sxs-lookup"><span data-stu-id="28cd8-298">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="28cd8-299">Usuń bazę danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-299">Delete the database.</span></span> <span data-ttu-id="28cd8-300">Wszystkie istniejące dane zostaną utracone.</span><span class="sxs-lookup"><span data-stu-id="28cd8-300">Any existing data is lost.</span></span>
* <span data-ttu-id="28cd8-301">Zmień model danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-301">Change the data model.</span></span> <span data-ttu-id="28cd8-302">Na przykład Dodaj `EmailAddress` pole.</span><span class="sxs-lookup"><span data-stu-id="28cd8-302">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="28cd8-303">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="28cd8-303">Run the app.</span></span>
* <span data-ttu-id="28cd8-304">`EnsureCreated` tworzy bazę danych z nowym schematem.</span><span class="sxs-lookup"><span data-stu-id="28cd8-304">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="28cd8-305">Ten przepływ pracy działa dobrze na etapie opracowywania, gdy schemat jest gwałtownie zmieniany, o ile nie trzeba zachować danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-305">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="28cd8-306">Sytuacja jest inna, gdy dane wprowadzone do bazy danych muszą zostać zachowane.</span><span class="sxs-lookup"><span data-stu-id="28cd8-306">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="28cd8-307">W takim przypadku należy użyć migracji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-307">When that is the case, use migrations.</span></span>

<span data-ttu-id="28cd8-308">W dalszej części tego samouczka zostanie usunięta baza danych, która została utworzona przez program, `EnsureCreated` a zamiast tego zostanie użyta migracja.</span><span class="sxs-lookup"><span data-stu-id="28cd8-308">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="28cd8-309">Baza danych utworzona przez `EnsureCreated` program nie może zostać zaktualizowana przy użyciu migracji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-309">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="28cd8-310">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="28cd8-310">Test the app</span></span>

* <span data-ttu-id="28cd8-311">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="28cd8-311">Run the app.</span></span>
* <span data-ttu-id="28cd8-312">Wybierz link **Students (studenci** ), a następnie **Utwórz nowy**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-312">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="28cd8-313">Przetestuj linki Edytuj, Szczegóły i Usuń.</span><span class="sxs-lookup"><span data-stu-id="28cd8-313">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="28cd8-314">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="28cd8-314">Seed the database</span></span>

<span data-ttu-id="28cd8-315">`EnsureCreated`Metoda tworzy pustą bazę danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-315">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="28cd8-316">Ta sekcja dodaje kod wypełniający bazę danych danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="28cd8-316">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="28cd8-317">Utwórz *dane/Dbinitializeer. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="28cd8-317">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="28cd8-318">Kod sprawdza, czy w bazie danych istnieją uczniowie.</span><span class="sxs-lookup"><span data-stu-id="28cd8-318">The code checks if there are any students in the database.</span></span> <span data-ttu-id="28cd8-319">Jeśli nie ma uczniów, dodaje dane testowe do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-319">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="28cd8-320">Tworzy dane testowe w tablicach, a nie w `List<T>` celu zoptymalizowania wydajności.</span><span class="sxs-lookup"><span data-stu-id="28cd8-320">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="28cd8-321">W *program.cs* Zastąp wywołanie wywołaniem `EnsureCreated` `DbInitializer.Initialize` :</span><span class="sxs-lookup"><span data-stu-id="28cd8-321">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="28cd8-322">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28cd8-322">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="28cd8-323">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):</span><span class="sxs-lookup"><span data-stu-id="28cd8-323">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="28cd8-324">Odpowiedz przy użyciu `Y` , aby usunąć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-324">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="28cd8-325">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28cd8-325">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="28cd8-326">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie usuń plik *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="28cd8-326">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="28cd8-327">Uruchom ponownie aplikację.</span><span class="sxs-lookup"><span data-stu-id="28cd8-327">Restart the app.</span></span>
* <span data-ttu-id="28cd8-328">Wybierz stronę uczniów, aby zobaczyć dane z rozrzutu.</span><span class="sxs-lookup"><span data-stu-id="28cd8-328">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="28cd8-329">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="28cd8-329">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="28cd8-330">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28cd8-330">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="28cd8-331">Otwórz **Eksplorator obiektów SQL Server** (SSOX) z menu **Widok** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="28cd8-331">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="28cd8-332">W SSOX wybierz pozycję **(LocalDB) \MSSQLLocalDB > bazy danych > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-332">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="28cd8-333">Nazwa bazy danych jest generowana na podstawie podanej wcześniej nazwy kontekstu oraz łącznika i identyfikatora GUID.</span><span class="sxs-lookup"><span data-stu-id="28cd8-333">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="28cd8-334">Rozwiń węzeł **tabele** .</span><span class="sxs-lookup"><span data-stu-id="28cd8-334">Expand the **Tables** node.</span></span>
* <span data-ttu-id="28cd8-335">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl dane** , aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.</span><span class="sxs-lookup"><span data-stu-id="28cd8-335">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="28cd8-336">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl kod** , aby zobaczyć, jak `Student` model mapuje się do `Student` schematu tabeli.</span><span class="sxs-lookup"><span data-stu-id="28cd8-336">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="28cd8-337">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28cd8-337">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="28cd8-338">Użyj narzędzia SQLite, aby wyświetlić schemat bazy danych i dane referencyjne.</span><span class="sxs-lookup"><span data-stu-id="28cd8-338">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="28cd8-339">Plik bazy danych ma nazwę *cu. DB* i znajduje się w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="28cd8-339">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="28cd8-340">Kod asynchroniczny</span><span class="sxs-lookup"><span data-stu-id="28cd8-340">Asynchronous code</span></span>

<span data-ttu-id="28cd8-341">Programowanie asynchroniczne jest trybem domyślnym dla ASP.NET Core i EF Core.</span><span class="sxs-lookup"><span data-stu-id="28cd8-341">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="28cd8-342">Serwer sieci Web ma ograniczoną liczbę dostępnych wątków, a w przypadku dużego obciążenia mogą być używane wszystkie dostępne wątki.</span><span class="sxs-lookup"><span data-stu-id="28cd8-342">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="28cd8-343">W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione.</span><span class="sxs-lookup"><span data-stu-id="28cd8-343">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="28cd8-344">W przypadku kodu synchronicznego wiele wątków może zostać rozwiązanych, chociaż nie działają, ponieważ oczekują na ukończenie operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="28cd8-344">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="28cd8-345">W przypadku kodu asynchronicznego, gdy proces oczekuje na ukończenie operacji we/wy, jego wątek zostanie zwolniony dla serwera do użycia na potrzeby przetwarzania innych żądań.</span><span class="sxs-lookup"><span data-stu-id="28cd8-345">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="28cd8-346">W efekcie kod asynchroniczny umożliwia efektywniejsze wykorzystanie zasobów serwera, a serwer może obsłużyć więcej ruchu bez opóźnień.</span><span class="sxs-lookup"><span data-stu-id="28cd8-346">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="28cd8-347">Kod asynchroniczny wprowadza niewielką ilość zapasową w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="28cd8-347">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="28cd8-348">W przypadku niskiego ruchu zwiększenie wydajności jest nieznaczne, a jednocześnie w przypadku dużego ruchu, potencjalne udoskonalenia wydajności są istotne.</span><span class="sxs-lookup"><span data-stu-id="28cd8-348">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="28cd8-349">W poniższym kodzie słowo kluczowe [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` wartość zwracana, `await` słowo kluczowe i `ToListAsync` Metoda sprawiają, że kod jest wykonywany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="28cd8-349">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="28cd8-350">`async`Słowo kluczowe instruuje kompilator, aby:</span><span class="sxs-lookup"><span data-stu-id="28cd8-350">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="28cd8-351">Generuj wywołania zwrotne dla części treści metody.</span><span class="sxs-lookup"><span data-stu-id="28cd8-351">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="28cd8-352">Utwórz obiekt [zadania](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) , który jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="28cd8-352">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="28cd8-353">`Task<T>`Typ zwracany reprezentuje bieżącą liczbę zadań.</span><span class="sxs-lookup"><span data-stu-id="28cd8-353">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="28cd8-354">`await`Słowo kluczowe powoduje, że kompilator dzieli metodę na dwie części.</span><span class="sxs-lookup"><span data-stu-id="28cd8-354">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="28cd8-355">Pierwsza część jest zakończona operacją uruchomioną asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="28cd8-355">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="28cd8-356">Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-356">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="28cd8-357">`ToListAsync` jest asynchroniczną wersją `ToList` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="28cd8-357">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="28cd8-358">Niektóre kwestie, o których należy pamiętać podczas pisania asynchronicznego kodu, który używa EF Core:</span><span class="sxs-lookup"><span data-stu-id="28cd8-358">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="28cd8-359">Tylko instrukcje, które powodują, że zapytania lub polecenia wysyłane do bazy danych są wykonywane asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="28cd8-359">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="28cd8-360">Obejmuje `ToListAsync` , `SingleOrDefaultAsync` , `FirstOrDefaultAsync` i `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-360">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="28cd8-361">Nie zawiera instrukcji, które po prostu zmieniają element `IQueryable` , taki jak `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-361">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="28cd8-362">Kontekst EF Core nie jest bezpieczny wątkowo: nie próbuj wykonać równolegle wielu operacji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-362">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="28cd8-363">Aby skorzystać z zalet wydajności w kodzie asynchronicznym, należy sprawdzić, czy pakiety biblioteki (na przykład stronicowania) używają wartości asynchronicznej, jeśli są wywoływane EF Core metod wysyłających zapytania do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-363">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="28cd8-364">Aby uzyskać więcej informacji na temat programowania asynchronicznego w programie .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne z Async i await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="28cd8-364">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="28cd8-365">Zagadnienia dotyczące wydajności</span><span class="sxs-lookup"><span data-stu-id="28cd8-365">Performance considerations</span></span>

<span data-ttu-id="28cd8-366">Ogólnie rzecz biorąc, Strona sieci Web nie powinna ładować dowolnej liczby wierszy.</span><span class="sxs-lookup"><span data-stu-id="28cd8-366">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="28cd8-367">Zapytanie powinno używać podejścia do ograniczania.</span><span class="sxs-lookup"><span data-stu-id="28cd8-367">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="28cd8-368">Na przykład poprzednie zapytanie może użyć, `Take` Aby ograniczyć liczbę zwracanych wierszy:</span><span class="sxs-lookup"><span data-stu-id="28cd8-368">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="28cd8-369">Wyliczenie dużej tabeli w widoku może zwrócić częściowo skonstruowaną odpowiedź HTTP 200, jeśli wyjątek bazy danych wystąpił częściowo przez wyliczenie.</span><span class="sxs-lookup"><span data-stu-id="28cd8-369">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="28cd8-370"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> wartość domyślna to 1024.</span><span class="sxs-lookup"><span data-stu-id="28cd8-370"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="28cd8-371">Następujące zestawy kodu `MaxModelBindingCollectionSize` :</span><span class="sxs-lookup"><span data-stu-id="28cd8-371">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="28cd8-372">Stronicowanie jest omówione w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="28cd8-372">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="28cd8-373">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="28cd8-373">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="28cd8-374">Następny samouczek</span><span class="sxs-lookup"><span data-stu-id="28cd8-374">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="28cd8-375">Jest to pierwsza z serii samouczków, które pokazują, jak używać rdzenia Entity Framework (EF) w aplikacji [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="28cd8-375">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="28cd8-376">Samouczki budują witrynę sieci Web dla fikcyjnej uczelni firmy Contoso.</span><span class="sxs-lookup"><span data-stu-id="28cd8-376">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="28cd8-377">Lokacja obejmuje funkcje, takie jak przyjmowanie uczniów, tworzenie kursu i przydziały instruktora.</span><span class="sxs-lookup"><span data-stu-id="28cd8-377">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="28cd8-378">Samouczek używa metody Code First.</span><span class="sxs-lookup"><span data-stu-id="28cd8-378">The tutorial uses the code first approach.</span></span> <span data-ttu-id="28cd8-379">Aby uzyskać informacje na temat korzystania z pierwszego podejścia do bazy danych, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/16897)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="28cd8-379">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="28cd8-380">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="28cd8-380">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="28cd8-381">[Instrukcje pobierania](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="28cd8-381">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="28cd8-382">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="28cd8-382">Prerequisites</span></span>

* <span data-ttu-id="28cd8-383">Jeśli :::no-loc(Razor)::: dopiero zaczynasz korzystać z stron, przejdź do serii samouczek wprowadzenie [do :::no-loc(Razor)::: stron](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="28cd8-383">If you're new to :::no-loc(Razor)::: Pages, go through the [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="28cd8-384">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28cd8-384">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="28cd8-385">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28cd8-385">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="28cd8-386">Aparaty bazy danych</span><span class="sxs-lookup"><span data-stu-id="28cd8-386">Database engines</span></span>

<span data-ttu-id="28cd8-387">Instrukcje programu Visual Studio używają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), wersji SQL Server Express, która działa tylko w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="28cd8-387">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="28cd8-388">Instrukcje Visual Studio Code korzystają z [oprogramowania SQLite](https://www.sqlite.org/), wieloplatformowego aparatu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-388">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="28cd8-389">Jeśli zdecydujesz się na korzystanie z oprogramowania SQLite, Pobierz i zainstaluj narzędzie innej firmy służące do zarządzania bazą danych programu SQLite i wyświetlania jej, na przykład w [przeglądarce DB dla oprogramowania SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="28cd8-389">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="28cd8-390">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="28cd8-390">Troubleshooting</span></span>

<span data-ttu-id="28cd8-391">Jeśli wystąpi problem, którego nie można rozwiązać, porównaj swój kod z [zakończonym projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="28cd8-391">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="28cd8-392">Dobrym sposobem uzyskania pomocy jest opublikowanie pytania do StackOverflow.com przy użyciu [tagu ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [tagu EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="28cd8-392">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="28cd8-393">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="28cd8-393">The sample app</span></span>

<span data-ttu-id="28cd8-394">Aplikacja skompilowana w tych samouczkach jest podstawową szkołą w sieci Web.</span><span class="sxs-lookup"><span data-stu-id="28cd8-394">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="28cd8-395">Użytkownicy mogą wyświetlać i aktualizować informacje dotyczące uczniów, kursów i instruktorów.</span><span class="sxs-lookup"><span data-stu-id="28cd8-395">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="28cd8-396">Poniżej przedstawiono kilka ekranów utworzonych w samouczku.</span><span class="sxs-lookup"><span data-stu-id="28cd8-396">Here are a few of the screens created in the tutorial.</span></span>

![Strona indeksu uczniów](intro/_static/students-index30.png)

![Strona edycji uczniów](intro/_static/student-edit30.png)

<span data-ttu-id="28cd8-399">Styl interfejsu użytkownika tej witryny jest oparty na wbudowanych szablonach projektów.</span><span class="sxs-lookup"><span data-stu-id="28cd8-399">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="28cd8-400">Fokus samouczka dotyczy korzystania z EF Core, a nie dostosowywania interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="28cd8-400">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="28cd8-401">Skorzystaj z linku w górnej części strony, aby uzyskać kod źródłowy dla ukończonego projektu.</span><span class="sxs-lookup"><span data-stu-id="28cd8-401">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="28cd8-402">Folder *cu30* ma kod dla ASP.NET Core wersji 3,0 samouczka.</span><span class="sxs-lookup"><span data-stu-id="28cd8-402">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="28cd8-403">Pliki odzwierciedlające stan kodu dla samouczków 1-7 można znaleźć w folderze *cu30snapshots* .</span><span class="sxs-lookup"><span data-stu-id="28cd8-403">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="28cd8-404">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28cd8-404">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="28cd8-405">Aby uruchomić aplikację po pobraniu ukończonego projektu:</span><span class="sxs-lookup"><span data-stu-id="28cd8-405">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="28cd8-406">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="28cd8-406">Build the project.</span></span>
* <span data-ttu-id="28cd8-407">W konsoli Menedżera pakietów (PMC) Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="28cd8-407">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="28cd8-408">Uruchom projekt, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-408">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="28cd8-409">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28cd8-409">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="28cd8-410">Aby uruchomić aplikację po pobraniu ukończonego projektu:</span><span class="sxs-lookup"><span data-stu-id="28cd8-410">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="28cd8-411">Usuń *ContosoUniversity. csproj* i Zmień nazwę *ContosoUniversitySQLite. csproj* na *ContosoUniversity. csproj*.</span><span class="sxs-lookup"><span data-stu-id="28cd8-411">Delete *ContosoUniversity.csproj* , and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="28cd8-412">W *program.cs* , komentarz `#define Startup` `StartupSQLite` jest używany.</span><span class="sxs-lookup"><span data-stu-id="28cd8-412">In *Program.cs* , comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="28cd8-413">Usuń *appSettings.js* i zmień nazwę *appSettingsSQLite.jsna* na *appSettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="28cd8-413">Delete *appSettings.json* , and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="28cd8-414">Usuń folder *migracji* , a następnie zmień nazwę *MigrationsSQL* na *migracji*.</span><span class="sxs-lookup"><span data-stu-id="28cd8-414">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="28cd8-415">Wykonaj wyszukiwanie globalne dla `#if SQLiteVersion` i Usuń `#if SQLiteVersion` oraz skojarzoną `#endif` instrukcję.</span><span class="sxs-lookup"><span data-stu-id="28cd8-415">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="28cd8-416">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="28cd8-416">Build the project.</span></span>
* <span data-ttu-id="28cd8-417">W wierszu polecenia w folderze projektu uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="28cd8-417">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="28cd8-418">W narzędziu SQLite uruchom następującą instrukcję SQL:</span><span class="sxs-lookup"><span data-stu-id="28cd8-418">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="28cd8-419">Uruchom projekt, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-419">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="28cd8-420">Tworzenie projektu aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="28cd8-420">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="28cd8-421">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28cd8-421">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="28cd8-422">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-422">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="28cd8-423">Wybierz **ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-423">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="28cd8-424">Nazwij projekt *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="28cd8-424">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="28cd8-425">Ważne jest, aby użyć tej dokładnej nazwy, łącznie z wielką literą, więc przestrzenie nazw są zgodne, gdy kod jest kopiowany i wklejany.</span><span class="sxs-lookup"><span data-stu-id="28cd8-425">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="28cd8-426">Na liście rozwijanej wybierz pozycję **.NET Core** i **ASP.NET Core 3,0** , a następnie wybierz pozycję **aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-426">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="28cd8-427">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28cd8-427">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="28cd8-428">W terminalu przejdź do folderu, w którym ma zostać utworzony folder projektu.</span><span class="sxs-lookup"><span data-stu-id="28cd8-428">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="28cd8-429">Uruchom następujące polecenia, aby utworzyć :::no-loc(Razor)::: Projekt strony i `cd` do nowego folderu projektu:</span><span class="sxs-lookup"><span data-stu-id="28cd8-429">Run the following commands to create a :::no-loc(Razor)::: Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="28cd8-430">Konfigurowanie stylu witryny</span><span class="sxs-lookup"><span data-stu-id="28cd8-430">Set up the site style</span></span>

<span data-ttu-id="28cd8-431">Skonfiguruj nagłówek, stopkę i menu witryny przez aktualizację *stron/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="28cd8-431">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml* :</span></span>

* <span data-ttu-id="28cd8-432">Zmień każde wystąpienie "ContosoUniversity" na "Uniwersytet firmy Contoso".</span><span class="sxs-lookup"><span data-stu-id="28cd8-432">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="28cd8-433">Istnieją trzy wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="28cd8-433">There are three occurrences.</span></span>

* <span data-ttu-id="28cd8-434">Usuń wpisy menu **Home** i **privacy** oraz Dodaj wpisy dotyczące **osób,** **studentów** , **kursów** , **instruktorów** i **działów**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-434">Delete the **Home** and **Privacy** menu entries, and add entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="28cd8-435">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="28cd8-435">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="28cd8-436">W obszarze *Pages/index. cshtml* Zastąp zawartość pliku następującym kodem, aby zamienić tekst na ASP.NET Core z tekstem dotyczącym tej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="28cd8-436">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="28cd8-437">Uruchom aplikację, aby sprawdzić, czy zostanie wyświetlona strona główna.</span><span class="sxs-lookup"><span data-stu-id="28cd8-437">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="28cd8-438">Model danych</span><span class="sxs-lookup"><span data-stu-id="28cd8-438">The data model</span></span>

<span data-ttu-id="28cd8-439">W poniższych sekcjach opisano tworzenie modelu danych:</span><span class="sxs-lookup"><span data-stu-id="28cd8-439">The following sections create a data model:</span></span>

![Kurs — Diagram modelu danych ucznia](intro/_static/data-model-diagram.png)

<span data-ttu-id="28cd8-441">Student może zarejestrować się w dowolnej liczbie kursów, a kurs może mieć dowolną liczbę studentów, którzy zarejestrowali się w nim.</span><span class="sxs-lookup"><span data-stu-id="28cd8-441">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="28cd8-442">Jednostka ucznia</span><span class="sxs-lookup"><span data-stu-id="28cd8-442">The Student entity</span></span>

![Diagram jednostek uczniów](intro/_static/student-entity.png)

* <span data-ttu-id="28cd8-444">Utwórz folder *models* w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="28cd8-444">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="28cd8-445">Utwórz *modele/uczniów. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="28cd8-445">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="28cd8-446">`ID`Właściwość zostanie przestała kolumną klucza podstawowego tabeli bazy danych, która odnosi się do tej klasy.</span><span class="sxs-lookup"><span data-stu-id="28cd8-446">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="28cd8-447">Domyślnie EF Core interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="28cd8-447">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="28cd8-448">W związku z tym alternatywną automatycznie rozpoznawaną nazwą `Student` klucza podstawowego klasy jest `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-448">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="28cd8-449">Aby uzyskać więcej informacji, zobacz [EF Core-Keys](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="28cd8-449">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="28cd8-450">`Enrollments`Właściwość jest [właściwością nawigacji](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="28cd8-450">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="28cd8-451">Właściwości nawigacji zawierają inne jednostki, które są powiązane z tą jednostką.</span><span class="sxs-lookup"><span data-stu-id="28cd8-451">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="28cd8-452">W takim przypadku `Enrollments` Właściwość `Student` jednostki zawiera wszystkie `Enrollment` jednostki, które są powiązane z tym uczniem.</span><span class="sxs-lookup"><span data-stu-id="28cd8-452">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="28cd8-453">Na przykład jeśli wiersz student w bazie danych ma dwa powiązane wiersze rejestracji, `Enrollments` Właściwość nawigacji zawiera te dwie jednostki rejestracji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-453">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="28cd8-454">W bazie danych wiersz rejestracji jest powiązany z wierszem studenta, jeśli jego kolumna StudentID zawiera wartość identyfikatora studenta.</span><span class="sxs-lookup"><span data-stu-id="28cd8-454">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="28cd8-455">Załóżmy na przykład, że wiersz student ma identyfikator = 1.</span><span class="sxs-lookup"><span data-stu-id="28cd8-455">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="28cd8-456">Powiązane wiersze rejestracji będą mieć StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="28cd8-456">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="28cd8-457">StudentID jest *kluczem obcym* w tabeli rejestracji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-457">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="28cd8-458">`Enrollments`Właściwość jest zdefiniowana tak, `ICollection<Enrollment>` ponieważ może istnieć wiele powiązanych jednostek rejestracji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-458">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="28cd8-459">Można użyć innych typów kolekcji, takich jak `List<Enrollment>` lub `HashSet<Enrollment>` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-459">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="28cd8-460">Gdy `ICollection<Enrollment>` jest używany, EF Core domyślnie tworzy `HashSet<Enrollment>` kolekcję.</span><span class="sxs-lookup"><span data-stu-id="28cd8-460">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="28cd8-461">Jednostka rejestracji</span><span class="sxs-lookup"><span data-stu-id="28cd8-461">The Enrollment entity</span></span>

![Diagram jednostek rejestracji](intro/_static/enrollment-entity.png)

<span data-ttu-id="28cd8-463">Utwórz *modele/rejestrację. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="28cd8-463">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="28cd8-464">`EnrollmentID`Właściwość jest kluczem podstawowym; ta jednostka używa `classnameID` wzorca zamiast `ID` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="28cd8-464">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="28cd8-465">Dla modelu danych produkcyjnych wybierz jeden wzorzec i użyj go spójnie.</span><span class="sxs-lookup"><span data-stu-id="28cd8-465">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="28cd8-466">Ten samouczek używa obu tych metod, aby zilustrować, że obie działają.</span><span class="sxs-lookup"><span data-stu-id="28cd8-466">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="28cd8-467">Używanie `ID` bez `classname` ułatwi implementowanie niektórych rodzajów zmian modelu danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-467">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="28cd8-468">`Grade`Właściwość jest `enum` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-468">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="28cd8-469">Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` Właściwość [dopuszcza wartość null](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="28cd8-469">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="28cd8-470">Klasa, która ma wartość null, różni się od zerowej klasy zerowej &mdash; oznacza, że Klasa nie jest znana lub nie została jeszcze przypisana.</span><span class="sxs-lookup"><span data-stu-id="28cd8-470">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="28cd8-471">`StudentID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Student` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-471">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="28cd8-472">`Enrollment`Jednostka jest skojarzona z jedną `Student` jednostką, więc Właściwość zawiera jedną `Student` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="28cd8-472">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="28cd8-473">`CourseID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Course` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-473">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="28cd8-474">`Enrollment`Jednostka jest skojarzona z jedną `Course` jednostką.</span><span class="sxs-lookup"><span data-stu-id="28cd8-474">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="28cd8-475">EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwę `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-475">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="28cd8-476">Na przykład `StudentID` jest kluczem obcym dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-476">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="28cd8-477">Właściwości klucza obcego mogą być również nazwane `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-477">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="28cd8-478">Na przykład, `CourseID` ponieważ `Course` klucz podstawowy jednostki to `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-478">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="28cd8-479">Jednostka kursu</span><span class="sxs-lookup"><span data-stu-id="28cd8-479">The Course entity</span></span>

![Diagram jednostek kursu](intro/_static/course-entity.png)

<span data-ttu-id="28cd8-481">Utwórz *modele/kurs. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="28cd8-481">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="28cd8-482">`Enrollments`Właściwość jest właściwością nawigacji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-482">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="28cd8-483">`Course`Jednostka może być powiązana z dowolną liczbą `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="28cd8-483">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="28cd8-484">Ten `DatabaseGenerated` atrybut umożliwia aplikacji określenie klucza podstawowego zamiast wygenerowania go przez bazę danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-484">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="28cd8-485">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="28cd8-485">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="28cd8-486">Strony uczniów tworzenia szkieletu</span><span class="sxs-lookup"><span data-stu-id="28cd8-486">Scaffold Student pages</span></span>

<span data-ttu-id="28cd8-487">W tej sekcji użyjesz narzędzia do tworzenia szkieletów ASP.NET Core do wygenerowania:</span><span class="sxs-lookup"><span data-stu-id="28cd8-487">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="28cd8-488">Klasa *kontekstu* EF Core.</span><span class="sxs-lookup"><span data-stu-id="28cd8-488">An EF Core *context* class.</span></span> <span data-ttu-id="28cd8-489">Kontekst jest klasą główną, która koordynuje Entity Framework funkcji dla danego modelu danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-489">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="28cd8-490">Pochodzi od `Microsoft.EntityFrameworkCore.DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="28cd8-490">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="28cd8-491">:::no-loc(Razor)::: strony obsługujące operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) dla `Student` jednostki.</span><span class="sxs-lookup"><span data-stu-id="28cd8-491">:::no-loc(Razor)::: pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="28cd8-492">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28cd8-492">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="28cd8-493">Utwórz folder *uczniów* w folderze *strony* .</span><span class="sxs-lookup"><span data-stu-id="28cd8-493">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="28cd8-494">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy folder *strony/uczniowie* i wybierz polecenie **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-494">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="28cd8-495">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **:::no-loc(Razor)::: strony z użyciem Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-495">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="28cd8-496">Na **stronie Dodawanie :::no-loc(Razor)::: stron przy użyciu Entity Framework (CRUD)** okna dialogowego:</span><span class="sxs-lookup"><span data-stu-id="28cd8-496">In the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="28cd8-497">Z listy rozwijanej **Klasa modelu** wybierz pozycję **student (ContosoUniversity. models)**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-497">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="28cd8-498">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus).</span><span class="sxs-lookup"><span data-stu-id="28cd8-498">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="28cd8-499">Zmień nazwę kontekstu danych z *ContosoUniversity. models. ContosoUniversityContext* na *ContosoUniversity. Data. SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="28cd8-499">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="28cd8-500">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-500">Select **Add**.</span></span>

<span data-ttu-id="28cd8-501">Następujące pakiety są instalowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="28cd8-501">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="28cd8-502">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28cd8-502">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="28cd8-503">Uruchom następujące polecenia interfejs wiersza polecenia platformy .NET Core, aby zainstalować wymagane pakiety NuGet:</span><span class="sxs-lookup"><span data-stu-id="28cd8-503">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  <span data-ttu-id="28cd8-504">Pakiet Microsoft. VisualStudio. Web. CodeGeneration. Design jest wymagany do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="28cd8-504">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="28cd8-505">Mimo że aplikacja nie będzie używać SQL Server, narzędzie do tworzenia szkieletów musi być pakietem SQL Server.</span><span class="sxs-lookup"><span data-stu-id="28cd8-505">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="28cd8-506">Utwórz folder *stron/uczniów* .</span><span class="sxs-lookup"><span data-stu-id="28cd8-506">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="28cd8-507">Uruchom następujące polecenie, aby zainstalować narzędzie do tworzenia [szkieletu ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="28cd8-507">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="28cd8-508">Uruchom następujące polecenie, aby uzyskać możliwość tworzenia szkieletu stron z uczniami.</span><span class="sxs-lookup"><span data-stu-id="28cd8-508">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="28cd8-509">**W systemie Windows**</span><span class="sxs-lookup"><span data-stu-id="28cd8-509">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="28cd8-510">**W systemie macOS lub Linux**</span><span class="sxs-lookup"><span data-stu-id="28cd8-510">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="28cd8-511">Jeśli wystąpił problem z poprzednim krokiem, Skompiluj projekt i ponów próbę wykonania szkieletu.</span><span class="sxs-lookup"><span data-stu-id="28cd8-511">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="28cd8-512">Proces tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="28cd8-512">The scaffolding process:</span></span>

* <span data-ttu-id="28cd8-513">Tworzy :::no-loc(Razor)::: strony w folderze *stron/uczniów* :</span><span class="sxs-lookup"><span data-stu-id="28cd8-513">Creates :::no-loc(Razor)::: pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="28cd8-514">*Create. cshtml* i *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="28cd8-514">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="28cd8-515">*DELETE. cshtml* i *DELETE.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="28cd8-515">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="28cd8-516">*Details. cshtml* i *details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="28cd8-516">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="28cd8-517">*Edit. cshtml* i *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="28cd8-517">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="28cd8-518">*Index. cshtml* i *index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="28cd8-518">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="28cd8-519">Tworzy *dane/SchoolContext. cs*.</span><span class="sxs-lookup"><span data-stu-id="28cd8-519">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="28cd8-520">Dodaje kontekst do iniekcji zależności w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="28cd8-520">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="28cd8-521">Dodaje parametry połączenia z bazą danych do programu *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="28cd8-521">Adds a database connection string to *:::no-loc(appsettings.json):::*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="28cd8-522">Parametry połączenia z bazą danych</span><span class="sxs-lookup"><span data-stu-id="28cd8-522">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="28cd8-523">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28cd8-523">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="28cd8-524">*:::no-loc(appsettings.json):::* Plik określa parametry połączenia [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="28cd8-524">The *:::no-loc(appsettings.json):::* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/:::no-loc(appsettings.json):::?highlight=11)]

<span data-ttu-id="28cd8-525">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użycia w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="28cd8-525">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="28cd8-526">Domyślnie LocalDB tworzy pliki *MDF* w `C:/Users/<user>` katalogu.</span><span class="sxs-lookup"><span data-stu-id="28cd8-526">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="28cd8-527">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28cd8-527">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="28cd8-528">Zmień parametry połączenia w taki sposób, aby wskazywały plik bazy danych programu SQLite o nazwie *cu. DB* :</span><span class="sxs-lookup"><span data-stu-id="28cd8-528">Change the connection string to point to a SQLite database file named *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="28cd8-529">Aktualizowanie klasy kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="28cd8-529">Update the database context class</span></span>

<span data-ttu-id="28cd8-530">Klasa główna, która koordynuje funkcje EF Core dla danego modelu danych, jest klasą kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-530">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="28cd8-531">Kontekst pochodzi od [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="28cd8-531">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="28cd8-532">Kontekst określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-532">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="28cd8-533">W tym projekcie Klasa ma nazwę `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-533">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="28cd8-534">Zaktualizuj *dane/SchoolContext. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="28cd8-534">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="28cd8-535">Wyróżniony kod tworzy właściwość [nieogólnymi \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla każdego zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="28cd8-535">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="28cd8-536">W EF Core terminologii:</span><span class="sxs-lookup"><span data-stu-id="28cd8-536">In EF Core terminology:</span></span>

* <span data-ttu-id="28cd8-537">Zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-537">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="28cd8-538">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="28cd8-538">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="28cd8-539">Ponieważ zestaw jednostek zawiera wiele jednostek, właściwości Nieogólnymi powinny mieć nazwy w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="28cd8-539">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="28cd8-540">Ponieważ narzędzie tworzenia szkieletów utworzyło `Student` nieogólnymi, ten krok zmienia go na plural `Students` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-540">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="28cd8-541">Aby :::no-loc(Razor)::: kod stron był zgodny z nową nazwą nieogólnymi, wprowadź globalną zmianę w całym projekcie `_context.Student` do `_context.Students` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-541">To make the :::no-loc(Razor)::: Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="28cd8-542">Istnieją 8 wystąpień.</span><span class="sxs-lookup"><span data-stu-id="28cd8-542">There are 8 occurrences.</span></span>

<span data-ttu-id="28cd8-543">Skompiluj projekt, aby sprawdzić, czy nie występują błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="28cd8-543">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="28cd8-544">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="28cd8-544">Startup.cs</span></span>

<span data-ttu-id="28cd8-545">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="28cd8-545">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="28cd8-546">Usługi (takie jak kontekst bazy danych EF Core) są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-546">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="28cd8-547">Składniki wymagające tych usług (takie jak :::no-loc(Razor)::: strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="28cd8-547">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="28cd8-548">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="28cd8-548">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="28cd8-549">Narzędzie do tworzenia szkieletów automatycznie zarejestrowało klasę kontekstu z kontenerem iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="28cd8-549">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="28cd8-550">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28cd8-550">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="28cd8-551">W programie `ConfigureServices` wyróżnione wiersze zostały dodane przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="28cd8-551">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="28cd8-552">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28cd8-552">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="28cd8-553">W programie upewnij `ConfigureServices` się, że kod dodany przez program tworzący szkielet jest wywoływany `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-553">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="28cd8-554">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="28cd8-554">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="28cd8-555">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *:::no-loc(appsettings.json):::* pliku.</span><span class="sxs-lookup"><span data-stu-id="28cd8-555">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="28cd8-556">Tworzenie bazy danych</span><span class="sxs-lookup"><span data-stu-id="28cd8-556">Create the database</span></span>

<span data-ttu-id="28cd8-557">Zaktualizuj *program.cs* , aby utworzyć bazę danych, jeśli nie istnieje:</span><span class="sxs-lookup"><span data-stu-id="28cd8-557">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="28cd8-558">Jeśli istnieje baza danych dla kontekstu, Metoda [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) nie przyjmuje żadnej akcji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-558">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="28cd8-559">Jeśli baza danych nie istnieje, tworzy bazę danych i schemat.</span><span class="sxs-lookup"><span data-stu-id="28cd8-559">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="28cd8-560">`EnsureCreated` umożliwia korzystanie z następującego przepływu pracy w celu obsługi zmian modelu danych:</span><span class="sxs-lookup"><span data-stu-id="28cd8-560">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="28cd8-561">Usuń bazę danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-561">Delete the database.</span></span> <span data-ttu-id="28cd8-562">Wszystkie istniejące dane zostaną utracone.</span><span class="sxs-lookup"><span data-stu-id="28cd8-562">Any existing data is lost.</span></span>
* <span data-ttu-id="28cd8-563">Zmień model danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-563">Change the data model.</span></span> <span data-ttu-id="28cd8-564">Na przykład Dodaj `EmailAddress` pole.</span><span class="sxs-lookup"><span data-stu-id="28cd8-564">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="28cd8-565">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="28cd8-565">Run the app.</span></span>
* <span data-ttu-id="28cd8-566">`EnsureCreated` tworzy bazę danych z nowym schematem.</span><span class="sxs-lookup"><span data-stu-id="28cd8-566">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="28cd8-567">Ten przepływ pracy działa dobrze na etapie opracowywania, gdy schemat jest gwałtownie zmieniany, o ile nie trzeba zachować danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-567">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="28cd8-568">Sytuacja jest inna, gdy dane wprowadzone do bazy danych muszą zostać zachowane.</span><span class="sxs-lookup"><span data-stu-id="28cd8-568">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="28cd8-569">W takim przypadku należy użyć migracji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-569">When that is the case, use migrations.</span></span>

<span data-ttu-id="28cd8-570">W dalszej części tego samouczka zostanie usunięta baza danych, która została utworzona przez program, `EnsureCreated` a zamiast tego zostanie użyta migracja.</span><span class="sxs-lookup"><span data-stu-id="28cd8-570">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="28cd8-571">Baza danych utworzona przez `EnsureCreated` program nie może zostać zaktualizowana przy użyciu migracji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-571">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="28cd8-572">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="28cd8-572">Test the app</span></span>

* <span data-ttu-id="28cd8-573">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="28cd8-573">Run the app.</span></span>
* <span data-ttu-id="28cd8-574">Wybierz link **Students (studenci** ), a następnie **Utwórz nowy**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-574">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="28cd8-575">Przetestuj linki Edytuj, Szczegóły i Usuń.</span><span class="sxs-lookup"><span data-stu-id="28cd8-575">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="28cd8-576">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="28cd8-576">Seed the database</span></span>

<span data-ttu-id="28cd8-577">`EnsureCreated`Metoda tworzy pustą bazę danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-577">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="28cd8-578">Ta sekcja dodaje kod wypełniający bazę danych danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="28cd8-578">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="28cd8-579">Utwórz *dane/Dbinitializeer. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="28cd8-579">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="28cd8-580">Kod sprawdza, czy w bazie danych istnieją uczniowie.</span><span class="sxs-lookup"><span data-stu-id="28cd8-580">The code checks if there are any students in the database.</span></span> <span data-ttu-id="28cd8-581">Jeśli nie ma uczniów, dodaje dane testowe do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-581">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="28cd8-582">Tworzy dane testowe w tablicach, a nie w `List<T>` celu zoptymalizowania wydajności.</span><span class="sxs-lookup"><span data-stu-id="28cd8-582">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="28cd8-583">W *program.cs* Zastąp wywołanie wywołaniem `EnsureCreated` `DbInitializer.Initialize` :</span><span class="sxs-lookup"><span data-stu-id="28cd8-583">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="28cd8-584">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28cd8-584">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="28cd8-585">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):</span><span class="sxs-lookup"><span data-stu-id="28cd8-585">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="28cd8-586">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28cd8-586">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="28cd8-587">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie usuń plik *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="28cd8-587">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="28cd8-588">Uruchom ponownie aplikację.</span><span class="sxs-lookup"><span data-stu-id="28cd8-588">Restart the app.</span></span>

* <span data-ttu-id="28cd8-589">Wybierz stronę uczniów, aby zobaczyć dane z rozrzutu.</span><span class="sxs-lookup"><span data-stu-id="28cd8-589">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="28cd8-590">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="28cd8-590">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="28cd8-591">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28cd8-591">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="28cd8-592">Otwórz **Eksplorator obiektów SQL Server** (SSOX) z menu **Widok** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="28cd8-592">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="28cd8-593">W SSOX wybierz pozycję **(LocalDB) \MSSQLLocalDB > bazy danych > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-593">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="28cd8-594">Nazwa bazy danych jest generowana na podstawie podanej wcześniej nazwy kontekstu oraz łącznika i identyfikatora GUID.</span><span class="sxs-lookup"><span data-stu-id="28cd8-594">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="28cd8-595">Rozwiń węzeł **tabele** .</span><span class="sxs-lookup"><span data-stu-id="28cd8-595">Expand the **Tables** node.</span></span>
* <span data-ttu-id="28cd8-596">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl dane** , aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.</span><span class="sxs-lookup"><span data-stu-id="28cd8-596">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="28cd8-597">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl kod** , aby zobaczyć, jak `Student` model mapuje się do `Student` schematu tabeli.</span><span class="sxs-lookup"><span data-stu-id="28cd8-597">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="28cd8-598">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28cd8-598">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="28cd8-599">Użyj narzędzia SQLite, aby wyświetlić schemat bazy danych i dane referencyjne.</span><span class="sxs-lookup"><span data-stu-id="28cd8-599">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="28cd8-600">Plik bazy danych ma nazwę *cu. DB* i znajduje się w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="28cd8-600">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="28cd8-601">Kod asynchroniczny</span><span class="sxs-lookup"><span data-stu-id="28cd8-601">Asynchronous code</span></span>

<span data-ttu-id="28cd8-602">Programowanie asynchroniczne jest trybem domyślnym dla ASP.NET Core i EF Core.</span><span class="sxs-lookup"><span data-stu-id="28cd8-602">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="28cd8-603">Serwer sieci Web ma ograniczoną liczbę dostępnych wątków, a w przypadku dużego obciążenia mogą być używane wszystkie dostępne wątki.</span><span class="sxs-lookup"><span data-stu-id="28cd8-603">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="28cd8-604">W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione.</span><span class="sxs-lookup"><span data-stu-id="28cd8-604">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="28cd8-605">W przypadku kodu synchronicznego wiele wątków może zostać powiązanych, podczas gdy nie wykonuje żadnej pracy, ponieważ oczekuje na ukończenie operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="28cd8-605">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="28cd8-606">W przypadku kodu asynchronicznego, gdy proces oczekuje na ukończenie operacji we/wy, jego wątek zostanie zwolniony dla serwera do użycia na potrzeby przetwarzania innych żądań.</span><span class="sxs-lookup"><span data-stu-id="28cd8-606">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="28cd8-607">W efekcie kod asynchroniczny umożliwia efektywniejsze wykorzystanie zasobów serwera, a serwer może obsłużyć więcej ruchu bez opóźnień.</span><span class="sxs-lookup"><span data-stu-id="28cd8-607">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="28cd8-608">Kod asynchroniczny wprowadza niewielką ilość zapasową w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="28cd8-608">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="28cd8-609">W przypadku niskiego ruchu zwiększenie wydajności jest nieznaczne, a jednocześnie w przypadku dużego ruchu, potencjalne udoskonalenia wydajności są istotne.</span><span class="sxs-lookup"><span data-stu-id="28cd8-609">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="28cd8-610">W poniższym kodzie słowo kluczowe [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` wartość zwracana, `await` słowo kluczowe i `ToListAsync` Metoda sprawiają, że kod jest wykonywany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="28cd8-610">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="28cd8-611">`async`Słowo kluczowe instruuje kompilator, aby:</span><span class="sxs-lookup"><span data-stu-id="28cd8-611">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="28cd8-612">Generuj wywołania zwrotne dla części treści metody.</span><span class="sxs-lookup"><span data-stu-id="28cd8-612">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="28cd8-613">Utwórz obiekt [zadania](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) , który jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="28cd8-613">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="28cd8-614">`Task<T>`Typ zwracany reprezentuje bieżącą liczbę zadań.</span><span class="sxs-lookup"><span data-stu-id="28cd8-614">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="28cd8-615">`await`Słowo kluczowe powoduje, że kompilator dzieli metodę na dwie części.</span><span class="sxs-lookup"><span data-stu-id="28cd8-615">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="28cd8-616">Pierwsza część jest zakończona operacją uruchomioną asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="28cd8-616">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="28cd8-617">Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-617">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="28cd8-618">`ToListAsync` jest asynchroniczną wersją `ToList` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="28cd8-618">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="28cd8-619">Niektóre kwestie, o których należy pamiętać podczas pisania asynchronicznego kodu, który używa EF Core:</span><span class="sxs-lookup"><span data-stu-id="28cd8-619">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="28cd8-620">Tylko instrukcje, które powodują, że zapytania lub polecenia wysyłane do bazy danych są wykonywane asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="28cd8-620">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="28cd8-621">Obejmuje `ToListAsync` , `SingleOrDefaultAsync` , `FirstOrDefaultAsync` i `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-621">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="28cd8-622">Nie zawiera instrukcji, które po prostu zmieniają element `IQueryable` , taki jak `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-622">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="28cd8-623">Kontekst EF Core nie jest bezpieczny wątkowo: nie próbuj wykonać równolegle wielu operacji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-623">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="28cd8-624">Aby skorzystać z zalet wydajności w kodzie asynchronicznym, należy sprawdzić, czy pakiety biblioteki (na przykład stronicowania) używają wartości asynchronicznej, jeśli są wywoływane EF Core metod wysyłających zapytania do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-624">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="28cd8-625">Aby uzyskać więcej informacji na temat programowania asynchronicznego w programie .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne z Async i await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="28cd8-625">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="28cd8-626">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="28cd8-626">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="28cd8-627">Następny samouczek</span><span class="sxs-lookup"><span data-stu-id="28cd8-627">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="28cd8-628">Przykładowa aplikacja internetowa Contoso University demonstruje sposób tworzenia aplikacji ASP.NET Core :::no-loc(Razor)::: stronach przy użyciu programu Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="28cd8-628">The Contoso University sample web app demonstrates how to create an ASP.NET Core :::no-loc(Razor)::: Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="28cd8-629">Przykładowa aplikacja jest witryną internetową fikcyjnej firmy Contoso University.</span><span class="sxs-lookup"><span data-stu-id="28cd8-629">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="28cd8-630">Obejmuje to funkcje, takie jak przyjmowanie studentów, tworzenie kursu i przydziały instruktora.</span><span class="sxs-lookup"><span data-stu-id="28cd8-630">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="28cd8-631">Ta strona jest pierwszą częścią serii samouczków, które wyjaśniają sposób tworzenia przykładowej aplikacji firmy Contoso University.</span><span class="sxs-lookup"><span data-stu-id="28cd8-631">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="28cd8-632">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="28cd8-632">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="28cd8-633">[Instrukcje pobierania](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="28cd8-633">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="28cd8-634">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="28cd8-634">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="28cd8-635">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28cd8-635">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="28cd8-636">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28cd8-636">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="28cd8-637">Znajomość [ :::no-loc(Razor)::: stron](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="28cd8-637">Familiarity with [:::no-loc(Razor)::: Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="28cd8-638">Nowi programiści powinni zakończyć pracę [z :::no-loc(Razor)::: stronami](xref:tutorials/razor-pages/razor-pages-start) przed rozpoczęciem tej serii.</span><span class="sxs-lookup"><span data-stu-id="28cd8-638">New programmers should complete [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="28cd8-639">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="28cd8-639">Troubleshooting</span></span>

<span data-ttu-id="28cd8-640">Jeśli wystąpi problem, którego nie można rozwiązać, można ogólnie znaleźć rozwiązanie, porównując kod z [ukończonym projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="28cd8-640">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="28cd8-641">Dobrym sposobem uzyskania pomocy jest zaksięgowanie pytania [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) na [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="28cd8-641">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="28cd8-642">Aplikacja sieci Web firmy Contoso University</span><span class="sxs-lookup"><span data-stu-id="28cd8-642">The Contoso University web app</span></span>

<span data-ttu-id="28cd8-643">Aplikacja skompilowana w tych samouczkach jest podstawową szkołą w sieci Web.</span><span class="sxs-lookup"><span data-stu-id="28cd8-643">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="28cd8-644">Użytkownicy mogą wyświetlać i aktualizować informacje dotyczące uczniów, kursów i instruktorów.</span><span class="sxs-lookup"><span data-stu-id="28cd8-644">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="28cd8-645">Poniżej przedstawiono kilka ekranów utworzonych w samouczku.</span><span class="sxs-lookup"><span data-stu-id="28cd8-645">Here are a few of the screens created in the tutorial.</span></span>

![Strona indeksu uczniów](intro/_static/students-index.png)

![Strona edycji uczniów](intro/_static/student-edit.png)

<span data-ttu-id="28cd8-648">Styl interfejsu użytkownika tej witryny jest zbliżony do zawartości wygenerowanej przez wbudowane szablony.</span><span class="sxs-lookup"><span data-stu-id="28cd8-648">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="28cd8-649">Fokus samouczka znajduje się na EF Core ze :::no-loc(Razor)::: stronami, a nie interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="28cd8-649">The tutorial focus is on EF Core with :::no-loc(Razor)::: Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="28cd8-650">Tworzenie :::no-loc(Razor)::: aplikacji sieci Web ContosoUniversity Pages</span><span class="sxs-lookup"><span data-stu-id="28cd8-650">Create the ContosoUniversity :::no-loc(Razor)::: Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="28cd8-651">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28cd8-651">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="28cd8-652">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-652">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="28cd8-653">Utwórz nową aplikację sieci Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="28cd8-653">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="28cd8-654">Nazwij projekt **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-654">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="28cd8-655">Ważne jest, aby nazwa projektu *ContosoUniversity* , tak aby przestrzenie nazw były zgodne, gdy kod jest kopiowany/wklejany.</span><span class="sxs-lookup"><span data-stu-id="28cd8-655">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="28cd8-656">Wybierz pozycję **ASP.NET Core 2,1** na liście rozwijanej, a następnie wybierz pozycję **aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-656">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="28cd8-657">Aby poznać obrazy powyższych kroków, zobacz [Tworzenie :::no-loc(Razor)::: aplikacji sieci Web](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="28cd8-657">For images of the preceding steps, see [Create a :::no-loc(Razor)::: web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="28cd8-658">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="28cd8-658">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="28cd8-659">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28cd8-659">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="28cd8-660">Konfigurowanie stylu witryny</span><span class="sxs-lookup"><span data-stu-id="28cd8-660">Set up the site style</span></span>

<span data-ttu-id="28cd8-661">Kilka zmian powoduje skonfigurowanie menu witryny, układu i strony głównej.</span><span class="sxs-lookup"><span data-stu-id="28cd8-661">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="28cd8-662">Aktualizowanie *stron/Shared/_Layout. cshtml* z następującymi zmianami:</span><span class="sxs-lookup"><span data-stu-id="28cd8-662">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="28cd8-663">Zmień każde wystąpienie "ContosoUniversity" na "Uniwersytet firmy Contoso".</span><span class="sxs-lookup"><span data-stu-id="28cd8-663">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="28cd8-664">Istnieją trzy wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="28cd8-664">There are three occurrences.</span></span>

* <span data-ttu-id="28cd8-665">Dodaj pozycje menu dla **studentów** , **kursów** , **instruktorów** i **działów** , a następnie usuń wpis menu **kontakt** .</span><span class="sxs-lookup"><span data-stu-id="28cd8-665">Add menu entries for **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="28cd8-666">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="28cd8-666">The changes are highlighted.</span></span> <span data-ttu-id="28cd8-667">(Wszystkie znaczniki *nie* są wyświetlane).</span><span class="sxs-lookup"><span data-stu-id="28cd8-667">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="28cd8-668">W obszarze *Pages/index. cshtml* Zastąp zawartość pliku następującym kodem, aby zamienić tekst na ASP.NET i MVC z tekstem dotyczącym tej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="28cd8-668">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="28cd8-669">Tworzenie modelu danych</span><span class="sxs-lookup"><span data-stu-id="28cd8-669">Create the data model</span></span>

<span data-ttu-id="28cd8-670">Utwórz klasy jednostek dla aplikacji firmy Contoso University.</span><span class="sxs-lookup"><span data-stu-id="28cd8-670">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="28cd8-671">Zacznij od następujących trzech jednostek:</span><span class="sxs-lookup"><span data-stu-id="28cd8-671">Start with the following three entities:</span></span>

![Kurs — Diagram modelu danych ucznia](intro/_static/data-model-diagram.png)

<span data-ttu-id="28cd8-673">Istnieje relacja jeden do wielu między elementami `Student` i `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-673">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="28cd8-674">Istnieje relacja jeden do wielu między elementami `Course` i `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-674">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="28cd8-675">Student może zarejestrować się w dowolnej liczbie kursów.</span><span class="sxs-lookup"><span data-stu-id="28cd8-675">A student can enroll in any number of courses.</span></span> <span data-ttu-id="28cd8-676">Kurs może mieć dowolną liczbę uczniów zarejestrowanych w tym obszarze.</span><span class="sxs-lookup"><span data-stu-id="28cd8-676">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="28cd8-677">W poniższych sekcjach zostanie utworzona Klasa dla każdej z tych jednostek.</span><span class="sxs-lookup"><span data-stu-id="28cd8-677">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="28cd8-678">Jednostka ucznia</span><span class="sxs-lookup"><span data-stu-id="28cd8-678">The Student entity</span></span>

![Diagram jednostek uczniów](intro/_static/student-entity.png)

<span data-ttu-id="28cd8-680">Utwórz folder *models* .</span><span class="sxs-lookup"><span data-stu-id="28cd8-680">Create a *Models* folder.</span></span> <span data-ttu-id="28cd8-681">W folderze *modele* Utwórz plik klasy o nazwie *student.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="28cd8-681">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="28cd8-682">`ID`Właściwość zostanie przestała kolumną klucza podstawowego tabeli bazy danych (DB), która odnosi się do tej klasy.</span><span class="sxs-lookup"><span data-stu-id="28cd8-682">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="28cd8-683">Domyślnie EF Core interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="28cd8-683">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="28cd8-684">W `classnameID` , `classname` jest nazwą klasy.</span><span class="sxs-lookup"><span data-stu-id="28cd8-684">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="28cd8-685">Alternatywny automatycznie rozpoznany klucz podstawowy znajduje się `StudentID` w poprzednim przykładzie.</span><span class="sxs-lookup"><span data-stu-id="28cd8-685">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="28cd8-686">`Enrollments`Właściwość jest [właściwością nawigacji](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="28cd8-686">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="28cd8-687">Właściwości nawigacji łączą się z innymi jednostkami, które są powiązane z tą jednostką.</span><span class="sxs-lookup"><span data-stu-id="28cd8-687">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="28cd8-688">W tym przypadku `Enrollments` Właściwość a `Student entity` zawiera wszystkie `Enrollment` jednostki, które są powiązane z tym `Student` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-688">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="28cd8-689">Na przykład jeśli wiersz student w bazie danych ma dwa powiązane wiersze rejestracji, `Enrollments` Właściwość nawigacji zawiera te dwie `Enrollment` jednostki.</span><span class="sxs-lookup"><span data-stu-id="28cd8-689">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="28cd8-690">Powiązany `Enrollment` wiersz jest wierszem zawierającym wartość klucza podstawowego studenta w `StudentID` kolumnie.</span><span class="sxs-lookup"><span data-stu-id="28cd8-690">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="28cd8-691">Załóżmy na przykład, że student o IDENTYFIKATORze 1 ma dwa wiersze w `Enrollment` tabeli.</span><span class="sxs-lookup"><span data-stu-id="28cd8-691">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="28cd8-692">`Enrollment`Tabela ma dwa wiersze z `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="28cd8-692">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="28cd8-693">`StudentID` jest kluczem obcym w `Enrollment` tabeli, który określa student w `Student` tabeli.</span><span class="sxs-lookup"><span data-stu-id="28cd8-693">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="28cd8-694">Jeśli właściwość nawigacji może zawierać wiele jednostek, właściwość nawigacji musi być typem listy, na przykład `ICollection<T>` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-694">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="28cd8-695">`ICollection<T>` można określić typ, taki jak `List<T>` lub `HashSet<T>` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-695">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="28cd8-696">Gdy `ICollection<T>` jest używany, EF Core domyślnie tworzy `HashSet<T>` kolekcję.</span><span class="sxs-lookup"><span data-stu-id="28cd8-696">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="28cd8-697">Właściwości nawigacji, które przechowują wiele jednostek, pochodzą z relacji "wiele do wielu" i "jeden do wielu".</span><span class="sxs-lookup"><span data-stu-id="28cd8-697">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="28cd8-698">Jednostka rejestracji</span><span class="sxs-lookup"><span data-stu-id="28cd8-698">The Enrollment entity</span></span>

![Diagram jednostek rejestracji](intro/_static/enrollment-entity.png)

<span data-ttu-id="28cd8-700">W folderze *modele* Utwórz *Enrollment.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="28cd8-700">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="28cd8-701">`EnrollmentID`Właściwość jest kluczem podstawowym.</span><span class="sxs-lookup"><span data-stu-id="28cd8-701">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="28cd8-702">Ta jednostka używa `classnameID` wzorca zamiast `ID` `Student` obiektu.</span><span class="sxs-lookup"><span data-stu-id="28cd8-702">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="28cd8-703">Zazwyczaj deweloperzy wybierają jeden wzorzec i używają go w całym modelu danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-703">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="28cd8-704">W późniejszym samouczku, użycie identyfikatora bez ClassName jest pokazane, aby ułatwić implementację dziedziczenia w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-704">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="28cd8-705">`Grade`Właściwość jest `enum` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-705">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="28cd8-706">Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` Właściwość dopuszcza wartość null.</span><span class="sxs-lookup"><span data-stu-id="28cd8-706">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="28cd8-707">Klasa o wartości null różni się od klasy zerowej — wartość null oznacza, że Klasa nie jest znana lub nie została jeszcze przypisana.</span><span class="sxs-lookup"><span data-stu-id="28cd8-707">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="28cd8-708">`StudentID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Student` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-708">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="28cd8-709">`Enrollment`Jednostka jest skojarzona z jedną `Student` jednostką, więc Właściwość zawiera jedną `Student` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="28cd8-709">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="28cd8-710">`Student`Jednostka różni się od `Student.Enrollments` właściwości nawigacji, która zawiera wiele `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="28cd8-710">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="28cd8-711">`CourseID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Course` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-711">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="28cd8-712">`Enrollment`Jednostka jest skojarzona z jedną `Course` jednostką.</span><span class="sxs-lookup"><span data-stu-id="28cd8-712">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="28cd8-713">EF Core interpretuje właściwość jako klucz obcy, jeśli ma nazwę `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-713">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="28cd8-714">Na przykład `StudentID` dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-714">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="28cd8-715">Właściwości klucza obcego mogą być również nazwane `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-715">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="28cd8-716">Na przykład, `CourseID` ponieważ `Course` klucz podstawowy jednostki to `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-716">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="28cd8-717">Jednostka kursu</span><span class="sxs-lookup"><span data-stu-id="28cd8-717">The Course entity</span></span>

![Diagram jednostek kursu](intro/_static/course-entity.png)

<span data-ttu-id="28cd8-719">W folderze *modele* Utwórz *Course.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="28cd8-719">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="28cd8-720">`Enrollments`Właściwość jest właściwością nawigacji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-720">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="28cd8-721">`Course`Jednostka może być powiązana z dowolną liczbą `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="28cd8-721">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="28cd8-722">Ten `DatabaseGenerated` atrybut umożliwia aplikacji określenie klucza podstawowego zamiast generowania bazy danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-722">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="28cd8-723">Tworzenie szkieletu modelu ucznia</span><span class="sxs-lookup"><span data-stu-id="28cd8-723">Scaffold the student model</span></span>

<span data-ttu-id="28cd8-724">W tej sekcji model ucznia jest szkieletem.</span><span class="sxs-lookup"><span data-stu-id="28cd8-724">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="28cd8-725">Oznacza to, że narzędzie tworzenia szkieletów tworzy strony dla operacji Create, Read, Update i Delete (CRUD) dla modelu ucznia.</span><span class="sxs-lookup"><span data-stu-id="28cd8-725">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="28cd8-726">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="28cd8-726">Build the project.</span></span>
* <span data-ttu-id="28cd8-727">Utwórz folder *strony/uczniowie* .</span><span class="sxs-lookup"><span data-stu-id="28cd8-727">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="28cd8-728">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28cd8-728">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="28cd8-729">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy folder *strony/uczniowie* > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-729">In **Solution Explorer** , right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="28cd8-730">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **:::no-loc(Razor)::: strony z użyciem Entity Framework (CRUD)** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-730">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="28cd8-731">Wypełnij okno dialogowe **Dodawanie :::no-loc(Razor)::: stron przy użyciu Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="28cd8-731">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="28cd8-732">Z listy rozwijanej **Klasa modelu** wybierz pozycję **student (ContosoUniversity. models)**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-732">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="28cd8-733">W wierszu **klasy kontekstu danych** wybierz **+** znak (plus), a następnie zmień wygenerowaną nazwę na **ContosoUniversity. models. SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-733">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="28cd8-734">Z listy rozwijanej **Klasa kontekstu danych** wybierz pozycję **ContosoUniversity. models. SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="28cd8-734">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="28cd8-735">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-735">Select **Add**.</span></span>

![Okno dialogowe CRUD](intro/_static/s1.png)

<span data-ttu-id="28cd8-737">Zobacz Tworzenie [szkieletu modelu filmu w](xref:tutorials/razor-pages/model#scaffold-the-movie-model) przypadku problemu z poprzednim krokiem.</span><span class="sxs-lookup"><span data-stu-id="28cd8-737">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="28cd8-738">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28cd8-738">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="28cd8-739">Uruchom następujące polecenia, aby uzyskać szkielet modelu ucznia.</span><span class="sxs-lookup"><span data-stu-id="28cd8-739">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="28cd8-740">Proces szkieletu utworzył i zmienił następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="28cd8-740">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="28cd8-741">Utworzone pliki</span><span class="sxs-lookup"><span data-stu-id="28cd8-741">Files created</span></span>

* <span data-ttu-id="28cd8-742">*Strony/uczniowie* Tworzenie, usuwanie, szczegóły, edytowanie, indeksowanie.</span><span class="sxs-lookup"><span data-stu-id="28cd8-742">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="28cd8-743">*Data/SchoolContext. cs*</span><span class="sxs-lookup"><span data-stu-id="28cd8-743">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="28cd8-744">Aktualizacje plików</span><span class="sxs-lookup"><span data-stu-id="28cd8-744">File updates</span></span>

* <span data-ttu-id="28cd8-745">*Startup.cs* : szczegółowe zmiany w tym pliku opisano w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-745">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="28cd8-746">*:::no-loc(appsettings.json):::* : Dodano parametry połączenia używane do nawiązania połączenia z lokalną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-746">*:::no-loc(appsettings.json):::* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="28cd8-747">Sprawdzanie kontekstu zarejestrowanego przy iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="28cd8-747">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="28cd8-748">ASP.NET Core jest skompilowana z [iniekcją zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="28cd8-748">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="28cd8-749">Usługi (takie jak kontekst EF Core DB) są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-749">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="28cd8-750">Składniki wymagające tych usług (takie jak :::no-loc(Razor)::: strony) są udostępniane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="28cd8-750">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="28cd8-751">Kod konstruktora, który pobiera wystąpienie kontekstu bazy danych, jest wyświetlany w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="28cd8-751">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="28cd8-752">Narzędzie do tworzenia szkieletów automatycznie utworzyło kontekst bazy danych i zarejestrował go przy użyciu kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="28cd8-752">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="28cd8-753">Zapoznaj się z `ConfigureServices` metodą w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="28cd8-753">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="28cd8-754">Podświetlony wiersz został dodany przez program do tworzenia szkieletu:</span><span class="sxs-lookup"><span data-stu-id="28cd8-754">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="28cd8-755">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w obiekcie [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="28cd8-755">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="28cd8-756">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *:::no-loc(appsettings.json):::* pliku.</span><span class="sxs-lookup"><span data-stu-id="28cd8-756">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="28cd8-757">Aktualizuj główne</span><span class="sxs-lookup"><span data-stu-id="28cd8-757">Update main</span></span>

<span data-ttu-id="28cd8-758">W *program.cs* Zmień metodę, `Main` Aby wykonać następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="28cd8-758">In *Program.cs* , modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="28cd8-759">Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="28cd8-759">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="28cd8-760">Wywołaj  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="28cd8-760">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="28cd8-761">Usuń kontekst, gdy `EnsureCreated` Metoda zostanie zakończona.</span><span class="sxs-lookup"><span data-stu-id="28cd8-761">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="28cd8-762">Poniższy kod przedstawia zaktualizowany plik *program.cs* .</span><span class="sxs-lookup"><span data-stu-id="28cd8-762">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="28cd8-763">`EnsureCreated` zapewnia, że baza danych dla kontekstu istnieje.</span><span class="sxs-lookup"><span data-stu-id="28cd8-763">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="28cd8-764">Jeśli istnieje, nie zostanie podjęta żadna akcja.</span><span class="sxs-lookup"><span data-stu-id="28cd8-764">If it exists, no action is taken.</span></span> <span data-ttu-id="28cd8-765">Jeśli nie istnieje, zostanie utworzona baza danych i jej wszystkie schematy.</span><span class="sxs-lookup"><span data-stu-id="28cd8-765">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="28cd8-766">`EnsureCreated` Program nie korzysta z migracji w celu utworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-766">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="28cd8-767">Baza danych utworzona za pomocą programu `EnsureCreated` nie może zostać zaktualizowana później przy użyciu migracji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-767">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="28cd8-768">`EnsureCreated` jest wywoływana podczas uruchamiania aplikacji, co pozwala na następujący przepływ pracy:</span><span class="sxs-lookup"><span data-stu-id="28cd8-768">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="28cd8-769">Usuń bazę danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-769">Delete the DB.</span></span>
* <span data-ttu-id="28cd8-770">Zmień schemat bazy danych (na przykład Dodaj `EmailAddress` pole).</span><span class="sxs-lookup"><span data-stu-id="28cd8-770">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="28cd8-771">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="28cd8-771">Run the app.</span></span>
* <span data-ttu-id="28cd8-772">`EnsureCreated` tworzy bazę danych z `EmailAddress` kolumną.</span><span class="sxs-lookup"><span data-stu-id="28cd8-772">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="28cd8-773">`EnsureCreated` jest wygodnie wczesny podczas opracowywania, gdy schemat jest szybko zmieniany.</span><span class="sxs-lookup"><span data-stu-id="28cd8-773">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="28cd8-774">W dalszej części tego samouczka baza danych została usunięta, a migracja jest używana.</span><span class="sxs-lookup"><span data-stu-id="28cd8-774">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="28cd8-775">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="28cd8-775">Test the app</span></span>

<span data-ttu-id="28cd8-776">Uruchom aplikację i zaakceptuj :::no-loc(cookie)::: zasady.</span><span class="sxs-lookup"><span data-stu-id="28cd8-776">Run the app and accept the :::no-loc(cookie)::: policy.</span></span> <span data-ttu-id="28cd8-777">Ta aplikacja nie przechowuje informacji osobistych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-777">This app doesn't keep personal information.</span></span> <span data-ttu-id="28cd8-778">Informacje o zasadach można znaleźć :::no-loc(cookie)::: w witrynie [pomocy technicznej ogólne rozporządzenie O ochronie danych (Rodo)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="28cd8-778">You can read about the :::no-loc(cookie)::: policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="28cd8-779">Wybierz link **Students (studenci** ), a następnie **Utwórz nowy**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-779">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="28cd8-780">Przetestuj linki Edytuj, Szczegóły i Usuń.</span><span class="sxs-lookup"><span data-stu-id="28cd8-780">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="28cd8-781">Sprawdzanie kontekstu bazy danych SchoolContext DB</span><span class="sxs-lookup"><span data-stu-id="28cd8-781">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="28cd8-782">Klasa główna, która koordynuje funkcje EF Core dla danego modelu danych, jest klasą kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-782">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="28cd8-783">Kontekst danych pochodzi od elementu [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="28cd8-783">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="28cd8-784">Kontekst danych określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-784">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="28cd8-785">W tym projekcie Klasa ma nazwę `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-785">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="28cd8-786">Zaktualizuj *SchoolContext.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="28cd8-786">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="28cd8-787">Wyróżniony kod tworzy właściwość [nieogólnymi \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) dla każdego zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="28cd8-787">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="28cd8-788">W EF Core terminologii:</span><span class="sxs-lookup"><span data-stu-id="28cd8-788">In EF Core terminology:</span></span>

* <span data-ttu-id="28cd8-789">Zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-789">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="28cd8-790">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="28cd8-790">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="28cd8-791">`DbSet<Enrollment>` i można `DbSet<Course>` je pominąć.</span><span class="sxs-lookup"><span data-stu-id="28cd8-791">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="28cd8-792">EF Core obejmuje te niejawnie `Student` , ponieważ jednostka odwołuje się do `Enrollment` jednostki, a `Enrollment` Jednostka odwołuje się do `Course` jednostki.</span><span class="sxs-lookup"><span data-stu-id="28cd8-792">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="28cd8-793">W tym samouczku należy zachować `DbSet<Enrollment>` i `DbSet<Course>` w `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-793">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="28cd8-794">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="28cd8-794">SQL Server Express LocalDB</span></span>

<span data-ttu-id="28cd8-795">Parametry połączenia określają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="28cd8-795">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="28cd8-796">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użycia w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="28cd8-796">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="28cd8-797">LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie istnieje złożona konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="28cd8-797">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="28cd8-798">Domyślnie LocalDB tworzy pliki *. mdf* DB w `C:/Users/<user>` katalogu.</span><span class="sxs-lookup"><span data-stu-id="28cd8-798">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="28cd8-799">Dodawanie kodu w celu zainicjowania bazy danych z danymi testowymi</span><span class="sxs-lookup"><span data-stu-id="28cd8-799">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="28cd8-800">EF Core tworzy pustą bazę danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-800">EF Core creates an empty DB.</span></span> <span data-ttu-id="28cd8-801">W tej sekcji `Initialize` Metoda jest zapisywana w celu wypełnienia jej danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="28cd8-801">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="28cd8-802">W folderze *dane* Utwórz nowy plik klasy o nazwie *DbInitializer.cs* i Dodaj następujący kod:</span><span class="sxs-lookup"><span data-stu-id="28cd8-802">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="28cd8-803">Uwaga: Poprzedni kod używa `Models` dla przestrzeni nazw ( `namespace ContosoUniversity.Models` ), a nie `Data` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-803">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="28cd8-804">`Models` jest spójny z kodem generowanym przez program rusztowaer.</span><span class="sxs-lookup"><span data-stu-id="28cd8-804">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="28cd8-805">Aby uzyskać więcej informacji, zobacz [ten problem z szkieletem usługi GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="28cd8-805">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="28cd8-806">Kod sprawdza, czy w bazie danych istnieją uczniowie.</span><span class="sxs-lookup"><span data-stu-id="28cd8-806">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="28cd8-807">Jeśli w bazie danych nie ma studentów, baza danych zostanie zainicjowana z danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="28cd8-807">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="28cd8-808">Ładuje dane testowe do tablic, a nie `List<T>` kolekcji w celu zoptymalizowania wydajności.</span><span class="sxs-lookup"><span data-stu-id="28cd8-808">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="28cd8-809">`EnsureCreated`Metoda automatycznie tworzy bazę danych dla kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-809">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="28cd8-810">Jeśli baza danych istnieje, `EnsureCreated` zwraca bez modyfikacji bazy danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-810">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="28cd8-811">W *program.cs* Zmień metodę, `Main` Aby wywołać `Initialize` :</span><span class="sxs-lookup"><span data-stu-id="28cd8-811">In *Program.cs* , modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="28cd8-812">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28cd8-812">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="28cd8-813">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie uruchom następujące polecenie w **konsoli Menedżera pakietów** (PMC):</span><span class="sxs-lookup"><span data-stu-id="28cd8-813">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="28cd8-814">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28cd8-814">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="28cd8-815">Zatrzymaj aplikację, jeśli jest uruchomiona, a następnie usuń plik *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="28cd8-815">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="28cd8-816">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="28cd8-816">View the DB</span></span>

<span data-ttu-id="28cd8-817">Nazwa bazy danych jest generowana na podstawie podanej wcześniej nazwy kontekstu oraz łącznika i identyfikatora GUID.</span><span class="sxs-lookup"><span data-stu-id="28cd8-817">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="28cd8-818">W rezultacie nazwa bazy danych będzie "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="28cd8-818">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="28cd8-819">Identyfikator GUID będzie różny dla każdego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="28cd8-819">The GUID will be different for each user.</span></span>
<span data-ttu-id="28cd8-820">Otwórz **Eksplorator obiektów SQL Server** (SSOX) z menu **Widok** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="28cd8-820">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="28cd8-821">W SSOX kliknij pozycję **(LocalDB) \MSSQLLocalDB > bazy danych > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="28cd8-821">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="28cd8-822">Rozwiń węzeł **tabele** .</span><span class="sxs-lookup"><span data-stu-id="28cd8-822">Expand the **Tables** node.</span></span>

<span data-ttu-id="28cd8-823">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl dane** , aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.</span><span class="sxs-lookup"><span data-stu-id="28cd8-823">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="28cd8-824">Kod asynchroniczny</span><span class="sxs-lookup"><span data-stu-id="28cd8-824">Asynchronous code</span></span>

<span data-ttu-id="28cd8-825">Programowanie asynchroniczne jest trybem domyślnym dla ASP.NET Core i EF Core.</span><span class="sxs-lookup"><span data-stu-id="28cd8-825">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="28cd8-826">Serwer sieci Web ma ograniczoną liczbę dostępnych wątków, a w przypadku dużego obciążenia mogą być używane wszystkie dostępne wątki.</span><span class="sxs-lookup"><span data-stu-id="28cd8-826">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="28cd8-827">W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione.</span><span class="sxs-lookup"><span data-stu-id="28cd8-827">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="28cd8-828">W przypadku kodu synchronicznego wiele wątków może zostać powiązanych, podczas gdy nie wykonuje żadnej pracy, ponieważ oczekuje na ukończenie operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="28cd8-828">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="28cd8-829">W przypadku kodu asynchronicznego, gdy proces oczekuje na ukończenie operacji we/wy, jego wątek zostanie zwolniony dla serwera do użycia na potrzeby przetwarzania innych żądań.</span><span class="sxs-lookup"><span data-stu-id="28cd8-829">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="28cd8-830">W efekcie kod asynchroniczny umożliwia efektywniejsze wykorzystanie zasobów serwera, a serwer jest włączony do obsługi większej ilości ruchu bez opóźnień.</span><span class="sxs-lookup"><span data-stu-id="28cd8-830">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="28cd8-831">Kod asynchroniczny wprowadza niewielką ilość zapasową w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="28cd8-831">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="28cd8-832">W przypadku niskiego ruchu zwiększenie wydajności jest nieznaczne, a jednocześnie w przypadku dużego ruchu, potencjalne udoskonalenia wydajności są istotne.</span><span class="sxs-lookup"><span data-stu-id="28cd8-832">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="28cd8-833">W poniższym kodzie słowo kluczowe [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` wartość zwracana, `await` słowo kluczowe i `ToListAsync` Metoda sprawiają, że kod jest wykonywany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="28cd8-833">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="28cd8-834">`async`Słowo kluczowe instruuje kompilator, aby:</span><span class="sxs-lookup"><span data-stu-id="28cd8-834">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="28cd8-835">Generuj wywołania zwrotne dla części treści metody.</span><span class="sxs-lookup"><span data-stu-id="28cd8-835">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="28cd8-836">Automatycznie Utwórz obiekt [zadania](/dotnet/api/system.threading.tasks.task) , który jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="28cd8-836">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="28cd8-837">Aby uzyskać więcej informacji, zobacz [Typ zwracany zadania](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="28cd8-837">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="28cd8-838">Niejawny typ zwracany `Task` reprezentuje bieżącą liczbę zadań.</span><span class="sxs-lookup"><span data-stu-id="28cd8-838">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="28cd8-839">`await`Słowo kluczowe powoduje, że kompilator dzieli metodę na dwie części.</span><span class="sxs-lookup"><span data-stu-id="28cd8-839">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="28cd8-840">Pierwsza część jest zakończona operacją uruchomioną asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="28cd8-840">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="28cd8-841">Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-841">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="28cd8-842">`ToListAsync` jest asynchroniczną wersją `ToList` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="28cd8-842">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="28cd8-843">Niektóre kwestie, o których należy pamiętać podczas pisania asynchronicznego kodu, który używa EF Core:</span><span class="sxs-lookup"><span data-stu-id="28cd8-843">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="28cd8-844">Tylko instrukcje, które powodują, że zapytania lub polecenia wysyłane do bazy danych są wykonywane asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="28cd8-844">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="28cd8-845">Obejmuje, `ToListAsync` ,, `SingleOrDefaultAsync` `FirstOrDefaultAsync` i `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-845">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="28cd8-846">Nie zawiera instrukcji, które po prostu zmieniają element `IQueryable` , taki jak `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="28cd8-846">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="28cd8-847">Kontekst EF Core nie jest bezpieczny wątkowo: nie próbuj wykonać równolegle wielu operacji.</span><span class="sxs-lookup"><span data-stu-id="28cd8-847">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="28cd8-848">Aby skorzystać z zalet wydajności w kodzie asynchronicznym, należy sprawdzić, czy pakiety biblioteki (na przykład stronicowania) używają wartości asynchronicznej, jeśli są wywoływane EF Core metod wysyłających zapytania do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="28cd8-848">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="28cd8-849">Aby uzyskać więcej informacji na temat programowania asynchronicznego w programie .NET, zobacz [Omówienie asynchroniczne](/dotnet/standard/async) i [programowanie asynchroniczne z Async i await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="28cd8-849">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="28cd8-850">W następnym samouczku są badane podstawowe operacje CRUD (tworzenie, odczytywanie, aktualizowanie, usuwanie).</span><span class="sxs-lookup"><span data-stu-id="28cd8-850">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="28cd8-851">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="28cd8-851">Additional resources</span></span>

* [<span data-ttu-id="28cd8-852">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="28cd8-852">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="28cd8-853">Dalej</span><span class="sxs-lookup"><span data-stu-id="28cd8-853">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
