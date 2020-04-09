---
title: 'Samouczek: Wprowadzenie do EF Core w ASP.NET aplikacji sieci Web MVC'
description: Jest to pierwszy z serii samouczków, które wyjaśniają, jak utworzyć contoso University przykładowej aplikacji od podstaw.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 02/06/2019
ms.topic: tutorial
uid: data/ef-mvc/intro
ms.openlocfilehash: fca9fdc425506ec8b4eec5c609237208f4c0d7b5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511304"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a><span data-ttu-id="89efb-103">Samouczek: Wprowadzenie do EF Core w ASP.NET aplikacji sieci Web MVC</span><span class="sxs-lookup"><span data-stu-id="89efb-103">Tutorial: Get started with EF Core in an ASP.NET MVC web app</span></span>

<span data-ttu-id="89efb-104">Ten samouczek **nie** został zaktualizowany do ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="89efb-104">This tutorial has **not** been updated to ASP.NET Core 3.0.</span></span> <span data-ttu-id="89efb-105">[Zaktualizowano wersję Razor Pages.](xref:data/ef-rp/intro)</span><span class="sxs-lookup"><span data-stu-id="89efb-105">The [Razor Pages version](xref:data/ef-rp/intro) has been updated.</span></span> <span data-ttu-id="89efb-106">Większość zmian kodu dla ASP.NET Core 3.0 i nowszej wersji tego samouczka:</span><span class="sxs-lookup"><span data-stu-id="89efb-106">Most of the code changes for the ASP.NET Core 3.0 and later version of this tutorial:</span></span>

* <span data-ttu-id="89efb-107">Znajdują się w *plikach Startup.cs* i *Program.cs.*</span><span class="sxs-lookup"><span data-stu-id="89efb-107">Are in the *Startup.cs* and *Program.cs* files.</span></span>
* <span data-ttu-id="89efb-108">Można znaleźć w [wersji Razor Pages](xref:data/ef-rp/intro).</span><span class="sxs-lookup"><span data-stu-id="89efb-108">Can be found in the [Razor Pages version](xref:data/ef-rp/intro).</span></span> 

<span data-ttu-id="89efb-109">Aby uzyskać informacje o tym, kiedy może to być zaktualizowane, zobacz [ten problem z githubem](https://github.com/dotnet/AspNetCore.Docs/issues/13920).</span><span class="sxs-lookup"><span data-stu-id="89efb-109">For information on when this might be updated, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/13920).</span></span>

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="89efb-110">Przykładowa aplikacja sieci web contoso University pokazuje, jak tworzyć ASP.NET core 2.2 MVC aplikacji sieci web przy użyciu entity framework (EF) Core 2.2 i Visual Studio 2017 lub 2019.</span><span class="sxs-lookup"><span data-stu-id="89efb-110">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.2 MVC web applications using Entity Framework (EF) Core 2.2 and Visual Studio 2017 or 2019.</span></span>

<span data-ttu-id="89efb-111">Przykładowa aplikacja jest witryną sieci web fikcyjnego Uniwersytetu Contoso.</span><span class="sxs-lookup"><span data-stu-id="89efb-111">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="89efb-112">Obejmuje funkcje, takie jak wstęp dla studentów, tworzenie kursów i zadania instruktora.</span><span class="sxs-lookup"><span data-stu-id="89efb-112">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="89efb-113">Jest to pierwszy z serii samouczków, które wyjaśniają, jak utworzyć contoso University przykładowej aplikacji od podstaw.</span><span class="sxs-lookup"><span data-stu-id="89efb-113">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

<span data-ttu-id="89efb-114">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="89efb-114">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="89efb-115">Tworzenie aplikacji sieci Web ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="89efb-115">Create an ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="89efb-116">Konfigurowanie stylu witryny</span><span class="sxs-lookup"><span data-stu-id="89efb-116">Set up the site style</span></span>
> * <span data-ttu-id="89efb-117">Dowiedz się więcej o pakietach EF Core NuGet</span><span class="sxs-lookup"><span data-stu-id="89efb-117">Learn about EF Core NuGet packages</span></span>
> * <span data-ttu-id="89efb-118">Tworzenie modelu danych</span><span class="sxs-lookup"><span data-stu-id="89efb-118">Create the data model</span></span>
> * <span data-ttu-id="89efb-119">Tworzenie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="89efb-119">Create the database context</span></span>
> * <span data-ttu-id="89efb-120">Rejestrowanie kontekstu iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="89efb-120">Register the context for dependency injection</span></span>
> * <span data-ttu-id="89efb-121">Inicjowanie bazy danych za pomocą danych testowych</span><span class="sxs-lookup"><span data-stu-id="89efb-121">Initialize the database with test data</span></span>
> * <span data-ttu-id="89efb-122">Tworzenie kontrolera i widoków</span><span class="sxs-lookup"><span data-stu-id="89efb-122">Create a controller and views</span></span>
> * <span data-ttu-id="89efb-123">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="89efb-123">View the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="89efb-124">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="89efb-124">Prerequisites</span></span>

* [<span data-ttu-id="89efb-125">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="89efb-125">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download)
* <span data-ttu-id="89efb-126">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z następującymi obciążeniami:</span><span class="sxs-lookup"><span data-stu-id="89efb-126">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the following workloads:</span></span>
  * <span data-ttu-id="89efb-127">**obciążenie ASP.NET i tworzeniem stron internetowych**</span><span class="sxs-lookup"><span data-stu-id="89efb-127">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="89efb-128">**Wieloplatformowe obciążenie programistyczne .NET Core**</span><span class="sxs-lookup"><span data-stu-id="89efb-128">**.NET Core cross-platform development** workload</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="89efb-129">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="89efb-129">Troubleshooting</span></span>

<span data-ttu-id="89efb-130">Jeśli napotkasz problem, którego nie możesz rozwiązać, zazwyczaj możesz znaleźć rozwiązanie, porównując kod z [ukończonym projektem.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)</span><span class="sxs-lookup"><span data-stu-id="89efb-130">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final).</span></span> <span data-ttu-id="89efb-131">Aby uzyskać listę typowych błędów i jak je rozwiązać, zobacz [sekcję Rozwiązywanie problemów z ostatnim samouczkiem z serii](advanced.md#common-errors).</span><span class="sxs-lookup"><span data-stu-id="89efb-131">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="89efb-132">Jeśli nie znajdziesz tam tego, czego potrzebujesz, możesz zadać pytanie, aby StackOverflow.com dla [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="89efb-132">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="89efb-133">Jest to seria 10 samouczków, z których każdy opiera się na tym, co zostało zrobione we wcześniejszych samouczkach.</span><span class="sxs-lookup"><span data-stu-id="89efb-133">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="89efb-134">Należy rozważyć zapisanie kopii projektu po każdym pomyślnym zakończeniu samouczka.</span><span class="sxs-lookup"><span data-stu-id="89efb-134">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="89efb-135">Następnie, jeśli napotkasz problemy, możesz zacząć od początku poprzedniego samouczka zamiast wracać do początku całej serii.</span><span class="sxs-lookup"><span data-stu-id="89efb-135">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="89efb-136">Aplikacja internetowa Uniwersytetu Contoso</span><span class="sxs-lookup"><span data-stu-id="89efb-136">Contoso University web app</span></span>

<span data-ttu-id="89efb-137">Aplikacja, którą będziesz budować w tych samouczkach jest prosta strona internetowa uczelni.</span><span class="sxs-lookup"><span data-stu-id="89efb-137">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="89efb-138">Użytkownicy mogą wyświetlać i aktualizować informacje o uczniach, kursach i instruktorach.</span><span class="sxs-lookup"><span data-stu-id="89efb-138">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="89efb-139">Oto kilka ekranów, które utworzysz.</span><span class="sxs-lookup"><span data-stu-id="89efb-139">Here are a few of the screens you'll create.</span></span>

![Strona Indeks uczniów](intro/_static/students-index.png)

![Uczniowie Edytuj stronę](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="89efb-142">Tworzenie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="89efb-142">Create web app</span></span>

* <span data-ttu-id="89efb-143">Otwórz program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="89efb-143">Open Visual Studio.</span></span>

* <span data-ttu-id="89efb-144">Z menu **Plik** wybierz polecenie **Nowy > project**.</span><span class="sxs-lookup"><span data-stu-id="89efb-144">From the **File** menu, select **New > Project**.</span></span>

* <span data-ttu-id="89efb-145">W lewym okienku wybierz pozycję **Zainstalowana > visual C# > Web**.</span><span class="sxs-lookup"><span data-stu-id="89efb-145">From the left pane, select **Installed > Visual C# > Web**.</span></span>

* <span data-ttu-id="89efb-146">Wybierz szablon projektu **ASP.NET Core Web Application.**</span><span class="sxs-lookup"><span data-stu-id="89efb-146">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="89efb-147">Wprowadź **ContosoUniversity** jako nazwę i kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="89efb-147">Enter **ContosoUniversity** as the name and click **OK**.</span></span>

  ![Okno dialogowe Nowy projekt](intro/_static/new-project2.png)

* <span data-ttu-id="89efb-149">Poczekaj na wyświetlenie okna dialogowego **Nowa ASP.NET Core Aplikacji Sieci Web.**</span><span class="sxs-lookup"><span data-stu-id="89efb-149">Wait for the **New ASP.NET Core Web Application** dialog to appear.</span></span>

* <span data-ttu-id="89efb-150">Wybierz **opcję .NET Core**, ASP.NET Core **2.2** i szablon aplikacji sieci Web **(Model-View-Controller).**</span><span class="sxs-lookup"><span data-stu-id="89efb-150">Select **.NET Core**, **ASP.NET Core 2.2** and the **Web Application (Model-View-Controller)** template.</span></span>

* <span data-ttu-id="89efb-151">Upewnij **się,** że uwierzytelnianie jest ustawione na **Brak uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="89efb-151">Make sure **Authentication** is set to **No Authentication**.</span></span>

* <span data-ttu-id="89efb-152">Wybierz **przycisk OK**</span><span class="sxs-lookup"><span data-stu-id="89efb-152">Select **OK**</span></span>

  ![Nowe okno dialogowe projektu ASP.NET Core](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a><span data-ttu-id="89efb-154">Konfigurowanie stylu witryny</span><span class="sxs-lookup"><span data-stu-id="89efb-154">Set up the site style</span></span>

<span data-ttu-id="89efb-155">Kilka prostych zmian skonfiguruje menu witryny, układ i stronę główną.</span><span class="sxs-lookup"><span data-stu-id="89efb-155">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="89efb-156">*Otwórz widoki/udostępnione/_Layout.cshtml* i wprowadzać następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="89efb-156">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="89efb-157">Zmień każde wystąpienie "ContosoUniversity" na "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="89efb-157">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="89efb-158">Istnieją trzy zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="89efb-158">There are three occurrences.</span></span>

* <span data-ttu-id="89efb-159">Dodaj pozycje menu **Informacje**, **Studenci**, **Kursy,** **Instruktorzy**i **Działy**i usuń wpis menu **Prywatność.**</span><span class="sxs-lookup"><span data-stu-id="89efb-159">Add menu entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="89efb-160">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="89efb-160">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

<span data-ttu-id="89efb-161">W *Views/Home/Index.cshtml*zastąp zawartość pliku następującym kodem, aby zastąpić tekst o ASP.NET i MVC tekstem o tej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="89efb-161">In *Views/Home/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="89efb-162">Naciśnij klawisze CTRL+F5, aby uruchomić projekt, lub wybierz polecenie **Debugowanie > rozpocznij bez debugowania** z menu.</span><span class="sxs-lookup"><span data-stu-id="89efb-162">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="89efb-163">Zobaczysz stronę główną z kartami dla stron, które utworzysz w tych samouczkach.</span><span class="sxs-lookup"><span data-stu-id="89efb-163">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Strona główna Uniwersytetu Contoso](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a><span data-ttu-id="89efb-165">Informacje o pakietach EF Core NuGet</span><span class="sxs-lookup"><span data-stu-id="89efb-165">About EF Core NuGet packages</span></span>

<span data-ttu-id="89efb-166">Aby dodać obsługę EF Core do projektu, zainstaluj dostawcę bazy danych, który chcesz kierować reklamy.</span><span class="sxs-lookup"><span data-stu-id="89efb-166">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="89efb-167">Ten samouczek używa programu SQL Server, a pakietem dostawcy jest [Microsoft.EntityFrameCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="89efb-167">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="89efb-168">Ten pakiet znajduje się w [metapakietie Microsoft.AspNetCore.App,](xref:fundamentals/metapackage-app)więc nie trzeba odwoływać się do pakietu.</span><span class="sxs-lookup"><span data-stu-id="89efb-168">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to reference the package.</span></span>

<span data-ttu-id="89efb-169">Pakiet EF SQL Server i`Microsoft.EntityFrameworkCore` jego `Microsoft.EntityFrameworkCore.Relational`zależności ( i ) zapewniają obsługę środowiska uruchomieniowego dla EF.</span><span class="sxs-lookup"><span data-stu-id="89efb-169">The EF SQL Server package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="89efb-170">Pakiet narzędzi zostanie dodany później w samouczku [Migracje.](migrations.md)</span><span class="sxs-lookup"><span data-stu-id="89efb-170">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="89efb-171">Aby uzyskać informacje o innych dostawcach baz danych dostępnych dla entity framework core, zobacz [Dostawców bazy danych](/ef/core/providers/).</span><span class="sxs-lookup"><span data-stu-id="89efb-171">For information about other database providers that are available for Entity Framework Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="89efb-172">Tworzenie modelu danych</span><span class="sxs-lookup"><span data-stu-id="89efb-172">Create the data model</span></span>

<span data-ttu-id="89efb-173">Następnie utworzysz klasy jednostek dla aplikacji Contoso University.</span><span class="sxs-lookup"><span data-stu-id="89efb-173">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="89efb-174">Zaczniesz od następujących trzech jednostek.</span><span class="sxs-lookup"><span data-stu-id="89efb-174">You'll start with the following three entities.</span></span>

![Diagram modelu danych dla uczniów course-enrollment-student](intro/_static/data-model-diagram.png)

<span data-ttu-id="89efb-176">Istnieje relacja jeden do wielu między `Student` `Enrollment` i jednostek i istnieje relacja jeden do `Course` wielu `Enrollment` między i jednostek.</span><span class="sxs-lookup"><span data-stu-id="89efb-176">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="89efb-177">Innymi słowy, student może być zapisany na dowolną liczbę kursów, a kurs może mieć dowolną liczbę studentów zapisanych w nim.</span><span class="sxs-lookup"><span data-stu-id="89efb-177">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="89efb-178">W poniższych sekcjach utworzysz klasę dla każdej z tych jednostek.</span><span class="sxs-lookup"><span data-stu-id="89efb-178">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="89efb-179">Jednostka Student</span><span class="sxs-lookup"><span data-stu-id="89efb-179">The Student entity</span></span>

![Diagram encji ucznia](intro/_static/student-entity.png)

<span data-ttu-id="89efb-181">W folderze *Modele* utwórz plik klasy o nazwie *Student.cs* i zastąp kod szablonu następującym kodem.</span><span class="sxs-lookup"><span data-stu-id="89efb-181">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="89efb-182">Właściwość `ID` stanie się kolumną klucza podstawowego tabeli bazy danych, która odpowiada tej klasie.</span><span class="sxs-lookup"><span data-stu-id="89efb-182">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="89efb-183">Domyślnie platforma Entity Framework interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="89efb-183">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="89efb-184">Właściwość `Enrollments` jest [właściwością nawigacji](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="89efb-184">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="89efb-185">Właściwości nawigacji przechowują inne jednostki, które są powiązane z tą encją.</span><span class="sxs-lookup"><span data-stu-id="89efb-185">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="89efb-186">W takim przypadku `Enrollments` właściwość `Student entity` a będzie `Enrollment` zawierać wszystkie jednostki, `Student` które są związane z tej jednostki.</span><span class="sxs-lookup"><span data-stu-id="89efb-186">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="89efb-187">Innymi słowy, jeśli dany wiersz studenta w bazie danych ma dwa powiązane wiersze rejestracji (wiersze, które `Student` zawierają wartość `Enrollments` klucza podstawowego `Enrollment` tego ucznia w kolumnie klucza obcego StudentID), właściwość nawigacji tej jednostki będzie zawierać te dwie jednostki.</span><span class="sxs-lookup"><span data-stu-id="89efb-187">In other words, if a given Student row in the database has two related Enrollment rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="89efb-188">Jeśli właściwość nawigacji może zawierać wiele jednostek (jak w relacjach wiele do wielu lub jeden do wielu), jej typem musi być `ICollection<T>`lista, na której można dodawać, usuwać i aktualizować wpisy, takie jak .</span><span class="sxs-lookup"><span data-stu-id="89efb-188">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="89efb-189">Można określić `ICollection<T>` lub typ, taki jak `List<T>` lub `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="89efb-189">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="89efb-190">Jeśli określisz `ICollection<T>`, `HashSet<T>` EF tworzy kolekcję domyślnie.</span><span class="sxs-lookup"><span data-stu-id="89efb-190">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="89efb-191">Jednostka Rejestracji</span><span class="sxs-lookup"><span data-stu-id="89efb-191">The Enrollment entity</span></span>

![Diagram jednostki rejestracji](intro/_static/enrollment-entity.png)

<span data-ttu-id="89efb-193">W folderze *Modele* utwórz *Enrollment.cs* i zastąp istniejący kod następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="89efb-193">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="89efb-194">Właściwość `EnrollmentID` będzie kluczem podstawowym; ta jednostka używa `classnameID` wzorca `ID` zamiast sam, jak `Student` widać w jednostce.</span><span class="sxs-lookup"><span data-stu-id="89efb-194">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="89efb-195">Zwykle można wybrać jeden wzorzec i używać go w całym modelu danych.</span><span class="sxs-lookup"><span data-stu-id="89efb-195">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="89efb-196">W tym miejscu odmiana ilustruje, że można użyć albo wzorzec.</span><span class="sxs-lookup"><span data-stu-id="89efb-196">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="89efb-197">W [późniejszym samouczku](inheritance.md)zobaczysz, jak przy użyciu identyfikatora bez nazwy klasy ułatwia implementowanie dziedziczenia w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="89efb-197">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="89efb-198">Obiekt `Grade` jest `enum`własnością .</span><span class="sxs-lookup"><span data-stu-id="89efb-198">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="89efb-199">Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` właściwość jest nullable.</span><span class="sxs-lookup"><span data-stu-id="89efb-199">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="89efb-200">Stopień, który ma wartość null, różni się od klasy zerowej — wartość null oznacza, że ocena nie jest znana lub nie została jeszcze przypisana.</span><span class="sxs-lookup"><span data-stu-id="89efb-200">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="89efb-201">Właściwość `StudentID` jest kluczem obcym, a `Student`odpowiednią właściwością nawigacji jest .</span><span class="sxs-lookup"><span data-stu-id="89efb-201">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="89efb-202">Jednostka `Enrollment` jest skojarzona `Student` z jedną jednostką, więc `Student` właściwość może `Student.Enrollments` zawierać tylko jedną jednostkę `Enrollment` (w przeciwieństwie do właściwości nawigacji, która została wcześniej osobowa, która może zawierać wiele jednostek).</span><span class="sxs-lookup"><span data-stu-id="89efb-202">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="89efb-203">Właściwość `CourseID` jest kluczem obcym, a `Course`odpowiednią właściwością nawigacji jest .</span><span class="sxs-lookup"><span data-stu-id="89efb-203">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="89efb-204">Jednostka `Enrollment` jest skojarzona `Course` z jedną encją.</span><span class="sxs-lookup"><span data-stu-id="89efb-204">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="89efb-205">Entity Framework interpretuje właściwość jako właściwość klucza obcego, `<navigation property name><primary key property name>` jeśli jest nazwana (na przykład dla właściwości `StudentID` `Student` nawigacji, ponieważ klucz podstawowy `Student` jednostki jest `ID`).</span><span class="sxs-lookup"><span data-stu-id="89efb-205">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="89efb-206">Właściwości klucza obcego można `<primary key property name>` również nazwać po prostu (na przykład, `CourseID` ponieważ klucz podstawowy `Course` jednostki jest `CourseID`).</span><span class="sxs-lookup"><span data-stu-id="89efb-206">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="89efb-207">Jednostka Kurs</span><span class="sxs-lookup"><span data-stu-id="89efb-207">The Course entity</span></span>

![Diagram encji kursu](intro/_static/course-entity.png)

<span data-ttu-id="89efb-209">W folderze *Modele* utwórz *Course.cs* i zastąp istniejący kod następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="89efb-209">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="89efb-210">Właściwość `Enrollments` jest właściwością nawigacji.</span><span class="sxs-lookup"><span data-stu-id="89efb-210">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="89efb-211">Jednostka `Course` może być powiązana `Enrollment` z dowolną liczbą jednostek.</span><span class="sxs-lookup"><span data-stu-id="89efb-211">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="89efb-212">Więcej informacji na temat `DatabaseGenerated` atrybutu powiemy w [późniejszym samouczku](complex-data-model.md) z tej serii.</span><span class="sxs-lookup"><span data-stu-id="89efb-212">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="89efb-213">Zasadniczo ten atrybut umożliwia wprowadzenie klucza podstawowego kursu, a nie generowanie go przez bazę danych.</span><span class="sxs-lookup"><span data-stu-id="89efb-213">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="89efb-214">Tworzenie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="89efb-214">Create the database context</span></span>

<span data-ttu-id="89efb-215">Klasą główną, która koordynuje funkcjonalność entity framework dla danego modelu danych jest klasą kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="89efb-215">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="89efb-216">Tę klasę można utworzyć, `Microsoft.EntityFrameworkCore.DbContext` wywodząc się z klasy.</span><span class="sxs-lookup"><span data-stu-id="89efb-216">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="89efb-217">W kodzie można określić, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="89efb-217">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="89efb-218">Można również dostosować pewne zachowanie struktury encji.</span><span class="sxs-lookup"><span data-stu-id="89efb-218">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="89efb-219">W tym projekcie klasa `SchoolContext`nosi nazwę .</span><span class="sxs-lookup"><span data-stu-id="89efb-219">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="89efb-220">W folderze projektu utwórz folder o nazwie *Dane*.</span><span class="sxs-lookup"><span data-stu-id="89efb-220">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="89efb-221">W folderze *Dane* utwórz nowy plik klasy o nazwie *SchoolContext.cs*i zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="89efb-221">In the *Data* folder create a new class file named *SchoolContext.cs*, and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="89efb-222">Ten kod `DbSet` tworzy właściwość dla każdego zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="89efb-222">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="89efb-223">W terminologii entity framework zestaw jednostek zazwyczaj odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="89efb-223">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="89efb-224">Można było pominąć `DbSet<Enrollment>` i `DbSet<Course>` oświadczenia i będzie działać tak samo.</span><span class="sxs-lookup"><span data-stu-id="89efb-224">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="89efb-225">Struktura jednostek obejmowałaby je `Student` w sposób `Enrollment` dorozumiany, ponieważ jednostka odwołuje się do jednostki, a `Enrollment` jednostka odwołuje się do jednostki. `Course`</span><span class="sxs-lookup"><span data-stu-id="89efb-225">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="89efb-226">Po utworzeniu bazy danych EF tworzy tabele, `DbSet` które mają nazwy takie same jak nazwy właściwości.</span><span class="sxs-lookup"><span data-stu-id="89efb-226">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="89efb-227">Nazwy właściwości dla kolekcji są zazwyczaj w liczbie mnogiej (Studenci, a nie Student), ale deweloperzy nie zgadzają się co do tego, czy nazwy tabel powinny być pluralizowane, czy nie.</span><span class="sxs-lookup"><span data-stu-id="89efb-227">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="89efb-228">W przypadku tych samouczków należy zastąpić domyślne zachowanie, określając nazwy poszczególnych tabel w DbContext.</span><span class="sxs-lookup"><span data-stu-id="89efb-228">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="89efb-229">Aby to zrobić, należy dodać następujący wyróżniony kod po ostatniej właściwości DbSet.</span><span class="sxs-lookup"><span data-stu-id="89efb-229">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a><span data-ttu-id="89efb-230">Zarejestrujcie schoolcontext</span><span class="sxs-lookup"><span data-stu-id="89efb-230">Register the SchoolContext</span></span>

<span data-ttu-id="89efb-231">ASP.NET Core domyślnie implementuje [iniekcję zależności.](../../fundamentals/dependency-injection.md)</span><span class="sxs-lookup"><span data-stu-id="89efb-231">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="89efb-232">Usługi (takie jak kontekst bazy danych EF) są rejestrowane z iniekcji zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="89efb-232">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="89efb-233">Składniki, które wymagają tych usług (takich jak kontrolery MVC) są dostarczane te usługi za pośrednictwem parametrów konstruktora.</span><span class="sxs-lookup"><span data-stu-id="89efb-233">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="89efb-234">Zobaczysz kod konstruktora kontrolera, który pobiera wystąpienie kontekstu w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="89efb-234">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="89efb-235">Aby `SchoolContext` zarejestrować się jako usługa, otwórz *Startup.cs*i dodaj `ConfigureServices` wyróżnione wiersze do metody.</span><span class="sxs-lookup"><span data-stu-id="89efb-235">To register `SchoolContext` as a service, open *Startup.cs*, and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

<span data-ttu-id="89efb-236">Nazwa ciągu połączenia jest przekazywana do kontekstu przez `DbContextOptionsBuilder` wywołanie metody na obiekcie.</span><span class="sxs-lookup"><span data-stu-id="89efb-236">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="89efb-237">W przypadku rozwoju lokalnego [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje ciąg połączenia z pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="89efb-237">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="89efb-238">Dodaj `using` instrukcje dla `ContosoUniversity.Data` i `Microsoft.EntityFrameworkCore` przestrzeni nazw, a następnie skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="89efb-238">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="89efb-239">Otwórz plik *appsettings.json* i dodaj parametry połączenia, jak pokazano w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="89efb-239">Open the *appsettings.json* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="89efb-240">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="89efb-240">SQL Server Express LocalDB</span></span>

<span data-ttu-id="89efb-241">Parametry połączenia określają bazę danych usługi SQL Server LocalDB.</span><span class="sxs-lookup"><span data-stu-id="89efb-241">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="89efb-242">LocalDB jest lekką wersją aparatu baz danych programu SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użytku produkcyjnego.</span><span class="sxs-lookup"><span data-stu-id="89efb-242">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="89efb-243">LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie ma złożonej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="89efb-243">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="89efb-244">Domyślnie LocalDB tworzy pliki bazy danych `C:/Users/<user>` *.mdf* w katalogu.</span><span class="sxs-lookup"><span data-stu-id="89efb-244">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="89efb-245">Inicjowanie bazy danych za pomocą danych testowych</span><span class="sxs-lookup"><span data-stu-id="89efb-245">Initialize DB with test data</span></span>

<span data-ttu-id="89efb-246">Entity Framework utworzy pustą bazę danych dla Ciebie.</span><span class="sxs-lookup"><span data-stu-id="89efb-246">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="89efb-247">W tej sekcji napisz metodę, która jest wywoływana po utworzeniu bazy danych w celu wypełnić go danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="89efb-247">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="89efb-248">W tym miejscu `EnsureCreated` użyjesz tej metody do automatycznego utworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="89efb-248">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="89efb-249">W [późniejszym samouczku](migrations.md) zobaczysz, jak obsługiwać zmiany modelu przy użyciu code first migracji, aby zmienić schemat bazy danych zamiast upuszczania i ponownego tworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="89efb-249">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="89efb-250">W folderze *Dane* utwórz nowy plik klasy o nazwie *DbInitializer.cs* i zastąp kod szablonu następującym kodem, co powoduje utworzenie bazy danych w razie potrzeby i wczytywanie danych testowych do nowej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="89efb-250">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="89efb-251">Kod sprawdza, czy istnieją studenci w bazie danych, a jeśli nie, zakłada, że baza danych jest nowa i musi być rozstawiony z danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="89efb-251">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="89efb-252">Ładuje dane testowe do `List<T>` tablic, a nie kolekcji w celu optymalizacji wydajności.</span><span class="sxs-lookup"><span data-stu-id="89efb-252">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="89efb-253">W *Program.cs*, zmodyfikuj `Main` metodę, aby wykonać następujące czynności podczas uruchamiania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="89efb-253">In *Program.cs*, modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="89efb-254">Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="89efb-254">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="89efb-255">Wywołaj metodę seed, przekazując do niej kontekst.</span><span class="sxs-lookup"><span data-stu-id="89efb-255">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="89efb-256">Zutylizuj kontekst, gdy metoda inicjatora jest wykonywana.</span><span class="sxs-lookup"><span data-stu-id="89efb-256">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

<span data-ttu-id="89efb-257">Dodaj `using` instrukcje:</span><span class="sxs-lookup"><span data-stu-id="89efb-257">Add `using` statements:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

<span data-ttu-id="89efb-258">W starszych samouczkach podobny kod `Configure` można zobaczyć w metodzie *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="89efb-258">In older tutorials, you may see similar code in the `Configure` method in *Startup.cs*.</span></span> <span data-ttu-id="89efb-259">Zaleca się, aby `Configure` użyć tej metody tylko do skonfigurowania potoku żądania.</span><span class="sxs-lookup"><span data-stu-id="89efb-259">We recommend that you use the `Configure` method only to set up the request pipeline.</span></span> <span data-ttu-id="89efb-260">Kod startowy aplikacji `Main` należy do metody.</span><span class="sxs-lookup"><span data-stu-id="89efb-260">Application startup code belongs in the `Main` method.</span></span>

<span data-ttu-id="89efb-261">Teraz przy pierwszym uruchomieniu aplikacji baza danych zostanie utworzona i rozstawiona z danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="89efb-261">Now the first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="89efb-262">Za każdym razem, gdy zmieniasz model danych, możesz usunąć bazę danych, zaktualizować metodę inicjatora i zacząć od nowa z nową bazą danych w ten sam sposób.</span><span class="sxs-lookup"><span data-stu-id="89efb-262">Whenever you change your data model, you can delete the database, update your seed method, and start afresh with a new database the same way.</span></span> <span data-ttu-id="89efb-263">W późniejszych samouczkach zobaczysz, jak zmodyfikować bazę danych po zmianie modelu danych, bez usuwania i ponownego tworzenia.</span><span class="sxs-lookup"><span data-stu-id="89efb-263">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="89efb-264">Tworzenie kontrolera i widoków</span><span class="sxs-lookup"><span data-stu-id="89efb-264">Create controller and views</span></span>

<span data-ttu-id="89efb-265">Następnie użyjesz aparatu szkieletu w programie Visual Studio, aby dodać kontroler MVC i widoki, które będą używać EF do wykonywania zapytań i zapisywania danych.</span><span class="sxs-lookup"><span data-stu-id="89efb-265">Next, you'll use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="89efb-266">Automatyczne tworzenie metod akcji CRUD i widoków jest znany jako rusztowania.</span><span class="sxs-lookup"><span data-stu-id="89efb-266">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="89efb-267">Szkielety różni się od generowania kodu, ponieważ szkieletowy kod jest punktem wyjścia, który można zmodyfikować zgodnie z własnymi wymaganiami, podczas gdy zazwyczaj nie modyfikuje wygenerowany kod.</span><span class="sxs-lookup"><span data-stu-id="89efb-267">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="89efb-268">Gdy trzeba dostosować wygenerowany kod, należy użyć klas częściowych lub ponownie wygenerować kod, gdy coś się zmieni.</span><span class="sxs-lookup"><span data-stu-id="89efb-268">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="89efb-269">Kliknij prawym przyciskiem myszy folder **Kontrolery** w **Eksploratorze rozwiązań** i wybierz polecenie **Dodaj > nowy element szkieletu**.</span><span class="sxs-lookup"><span data-stu-id="89efb-269">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item**.</span></span>

* <span data-ttu-id="89efb-270">W oknie dialogowym **Dodawanie rusztowania:**</span><span class="sxs-lookup"><span data-stu-id="89efb-270">In the **Add Scaffold** dialog box:</span></span>

  * <span data-ttu-id="89efb-271">Wybierz **kontroler MVC z widokami, używając entity framework**.</span><span class="sxs-lookup"><span data-stu-id="89efb-271">Select **MVC controller with views, using Entity Framework**.</span></span>

  * <span data-ttu-id="89efb-272">Kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="89efb-272">Click **Add**.</span></span> <span data-ttu-id="89efb-273">Zostanie wyświetlone okno dialogowe **Dodawanie kontrolera MVC z widokami przy użyciu programu Entity Framework.**</span><span class="sxs-lookup"><span data-stu-id="89efb-273">The **Add MVC Controller with views, using Entity Framework** dialog box appears.</span></span>

    ![Uczeń rusztowania](intro/_static/scaffold-student2.png)

  * <span data-ttu-id="89efb-275">W **klasie Model** wybierz **Uczeń**.</span><span class="sxs-lookup"><span data-stu-id="89efb-275">In **Model class** select **Student**.</span></span>

  * <span data-ttu-id="89efb-276">W **klasie kontekstu danych** wybierz **SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="89efb-276">In **Data context class** select **SchoolContext**.</span></span>

  * <span data-ttu-id="89efb-277">Zaakceptuj domyślny **StudentsController** jako nazwę.</span><span class="sxs-lookup"><span data-stu-id="89efb-277">Accept the default **StudentsController** as the name.</span></span>

  * <span data-ttu-id="89efb-278">Kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="89efb-278">Click **Add**.</span></span>

  <span data-ttu-id="89efb-279">Po kliknięciu **przycisku Dodaj**aparat rusztowań programu Visual Studio tworzy plik *StudentsController.cs* i zestaw widoków (*.cshtml* plików), które współpracują z kontrolerem.</span><span class="sxs-lookup"><span data-stu-id="89efb-279">When you click **Add**, the Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views (*.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="89efb-280">(Aparat szkieletu można również utworzyć kontekst bazy danych dla Ciebie, jeśli nie utworzyć go ręcznie najpierw, jak to miało miejsce wcześniej w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="89efb-280">(The scaffolding engine can also create the database context for you if you don't create it manually first as you did earlier for this tutorial.</span></span> <span data-ttu-id="89efb-281">Można określić nową klasę kontekstu w polu **Dodaj kontroler,** klikając znak plus po prawej stronie **klasy kontekstu dane**.</span><span class="sxs-lookup"><span data-stu-id="89efb-281">You can specify a new context class in the **Add Controller** box by clicking the plus sign to the right of **Data context class**.</span></span>  <span data-ttu-id="89efb-282">Visual Studio utworzy `DbContext` klasę, a także kontroler i widoki.)</span><span class="sxs-lookup"><span data-stu-id="89efb-282">Visual Studio will then create your `DbContext` class as well as the controller and views.)</span></span>

<span data-ttu-id="89efb-283">Można zauważyć, że kontroler `SchoolContext` przyjmuje jako parametr konstruktora.</span><span class="sxs-lookup"><span data-stu-id="89efb-283">You'll notice that the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="89efb-284">ASP.NET Iniekcja zależności core zajmuje się `SchoolContext` przekazywanie wystąpienia do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="89efb-284">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="89efb-285">Skonfigurowano to wcześniej w pliku *Startup.cs.*</span><span class="sxs-lookup"><span data-stu-id="89efb-285">You configured that in the *Startup.cs* file earlier.</span></span>

<span data-ttu-id="89efb-286">Kontroler zawiera `Index` metodę akcji, która wyświetla wszystkich uczniów w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="89efb-286">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="89efb-287">Metoda pobiera listę studentów z Student jednostki `Students` ustawionej przez odczytywanie właściwości wystąpienia kontekstu bazy danych:</span><span class="sxs-lookup"><span data-stu-id="89efb-287">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="89efb-288">Dowiesz się o asynchronicznych elementów programowania w tym kodzie w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="89efb-288">You'll learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="89efb-289">*Widok/Studenci/Index.cshtml* wyświetla tę listę w tabeli:</span><span class="sxs-lookup"><span data-stu-id="89efb-289">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="89efb-290">Naciśnij klawisze CTRL+F5, aby uruchomić projekt, lub wybierz polecenie **Debugowanie > rozpocznij bez debugowania** z menu.</span><span class="sxs-lookup"><span data-stu-id="89efb-290">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="89efb-291">Kliknij kartę Uczniowie, aby wyświetlić dane testowe wstawione przez `DbInitializer.Initialize` metodę.</span><span class="sxs-lookup"><span data-stu-id="89efb-291">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="89efb-292">W zależności od tego, jak wąskie `Students` jest okno przeglądarki, zobaczysz łącze karty u góry strony lub musisz kliknąć ikonę nawigacji w prawym górnym rogu, aby zobaczyć link.</span><span class="sxs-lookup"><span data-stu-id="89efb-292">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Contoso University strona główna wąska](intro/_static/home-page-narrow.png)

![Strona Indeks uczniów](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="89efb-295">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="89efb-295">View the database</span></span>

<span data-ttu-id="89efb-296">Po uruchomieniu aplikacji `DbInitializer.Initialize` metoda wywołuje `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="89efb-296">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="89efb-297">EF zobaczył, że nie ma bazy danych i tak `Initialize` utworzono jedną, a następnie pozostałą część kodu metody zapełnił bazę danych danymi.</span><span class="sxs-lookup"><span data-stu-id="89efb-297">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="89efb-298">Za pomocą **programu SQL Server Object Explorer** (SSOX) można wyświetlić bazę danych w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="89efb-298">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="89efb-299">Zamknij okno przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="89efb-299">Close the browser.</span></span>

<span data-ttu-id="89efb-300">Jeśli okno SSOX nie jest jeszcze otwarte, wybierz je z menu **Widok** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="89efb-300">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="89efb-301">W polu SSOX kliknij **pozycję (localdb)\MSSQLLocalDB > Databases**, a następnie kliknij wpis nazwy bazy danych znajdującej się w ciągu połączenia w pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="89efb-301">In SSOX, click **(localdb)\MSSQLLocalDB > Databases**, and then click the entry for the database name that's in the connection string in your *appsettings.json* file.</span></span>

<span data-ttu-id="89efb-302">Rozwiń węzeł **Tabele,** aby wyświetlić tabele w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="89efb-302">Expand the **Tables** node to see the tables in your database.</span></span>

![Tabele w SSOX](intro/_static/ssox-tables.png)

<span data-ttu-id="89efb-304">Kliknij prawym przyciskiem myszy tabelę **Student** i kliknij polecenie **Wyświetl dane,** aby wyświetlić utworzone kolumny i wiersze wstawione do tabeli.</span><span class="sxs-lookup"><span data-stu-id="89efb-304">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![Stół studencki w SSOX](intro/_static/ssox-student-table.png)

<span data-ttu-id="89efb-306">Pliki bazy danych *.mdf* i *.ldf* znajdują się w folderze *C:\Users\\\<yourusername>.*</span><span class="sxs-lookup"><span data-stu-id="89efb-306">The *.mdf* and *.ldf* database files are in the *C:\Users\\\<yourusername>* folder.</span></span>

<span data-ttu-id="89efb-307">Ponieważ wywołujesz `EnsureCreated` w metodzie inicjatora, która działa w uruchomieniu aplikacji, `Student` można teraz wprowadzić zmiany do klasy, usunąć bazę danych, uruchomić aplikację ponownie, a baza danych zostanie automatycznie utworzona ponownie, aby dopasować zmiany.</span><span class="sxs-lookup"><span data-stu-id="89efb-307">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="89efb-308">Na przykład jeśli dodasz `EmailAddress` właściwość do `Student` klasy, zobaczysz `EmailAddress` nową kolumnę w tabeli ponownie utworzone.</span><span class="sxs-lookup"><span data-stu-id="89efb-308">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="89efb-309">Konwencja</span><span class="sxs-lookup"><span data-stu-id="89efb-309">Conventions</span></span>

<span data-ttu-id="89efb-310">Ilość kodu trzeba było napisać, aby entity framework, aby móc utworzyć pełną bazę danych dla Ciebie jest minimalna ze względu na użycie konwencji lub założeń, które framework jednostek sprawia, że.</span><span class="sxs-lookup"><span data-stu-id="89efb-310">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="89efb-311">Nazwy `DbSet` właściwości są używane jako nazwy tabel.</span><span class="sxs-lookup"><span data-stu-id="89efb-311">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="89efb-312">W przypadku jednostek, `DbSet` do których nie odwołuje się właściwość, nazwy klas jednostek są używane jako nazwy tabel.</span><span class="sxs-lookup"><span data-stu-id="89efb-312">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>

* <span data-ttu-id="89efb-313">Nazwy właściwości encji są używane dla nazw kolumn.</span><span class="sxs-lookup"><span data-stu-id="89efb-313">Entity property names are used for column names.</span></span>

* <span data-ttu-id="89efb-314">Właściwości encji o nazwie ID lub ClassnameID są rozpoznawane jako właściwości klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="89efb-314">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>

* <span data-ttu-id="89efb-315">Właściwość jest interpretowana jako właściwość klucza obcego, jeśli nosi \* \<nazwę właściwości nawigacji>\<nazwa właściwości klucza podstawowego>\* (na przykład dla właściwości `StudentID` `Student` nawigacji, ponieważ klucz podstawowy `Student` jednostki jest `ID`).</span><span class="sxs-lookup"><span data-stu-id="89efb-315">A property is interpreted as a foreign key property if it's named *\<navigation property name>\<primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="89efb-316">Właściwości klucza obcego można również nazwać po prostu `EnrollmentID` `Enrollment` `EnrollmentID` \* \<nazwą właściwości klucza podstawowego>\* (na przykład, ponieważ klucz podstawowy jednostki jest ).</span><span class="sxs-lookup"><span data-stu-id="89efb-316">Foreign key properties can also be named simply *\<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="89efb-317">Konwencjonalne zachowanie może zostać zastąpione.</span><span class="sxs-lookup"><span data-stu-id="89efb-317">Conventional behavior can be overridden.</span></span> <span data-ttu-id="89efb-318">Na przykład można jawnie określić nazwy tabel, jak widzieliśmy wcześniej w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="89efb-318">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="89efb-319">Możesz też ustawić nazwy kolumn i ustawić dowolną właściwość jako klucz podstawowy lub klucz obcy, co zostanie wyświetlene w [późniejszym samouczku](complex-data-model.md) z tej serii.</span><span class="sxs-lookup"><span data-stu-id="89efb-319">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="89efb-320">Kod asynchroniczne</span><span class="sxs-lookup"><span data-stu-id="89efb-320">Asynchronous code</span></span>

<span data-ttu-id="89efb-321">Programowanie asynchroniczne jest domyślnym trybem dla ASP.NET Core i EF Core.</span><span class="sxs-lookup"><span data-stu-id="89efb-321">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="89efb-322">Serwer sieci web ma ograniczoną liczbę dostępnych wątków, a w sytuacjach wysokiego obciążenia wszystkie dostępne wątki mogą być używane.</span><span class="sxs-lookup"><span data-stu-id="89efb-322">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="89efb-323">W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione.</span><span class="sxs-lookup"><span data-stu-id="89efb-323">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="89efb-324">Z kodu synchroniczowego, wiele wątków może być związany, podczas gdy w rzeczywistości nie wykonuje żadnej pracy, ponieważ oczekują na we/wy, aby zakończyć.</span><span class="sxs-lookup"><span data-stu-id="89efb-324">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="89efb-325">Z kodem asynchronizacyjnym, gdy proces oczekuje na zakończenie we/wy, jego wątek jest zwalniany dla serwera do użycia do przetwarzania innych żądań.</span><span class="sxs-lookup"><span data-stu-id="89efb-325">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="89efb-326">W rezultacie kod asynchroniczne umożliwia bardziej efektywne korzystanie z zasobów serwera, a serwer jest włączony do obsługi większej liczby ruchu bez opóźnień.</span><span class="sxs-lookup"><span data-stu-id="89efb-326">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="89efb-327">Kod asynchroniczne wprowadza niewielką ilość narzutów w czasie wykonywania, ale w sytuacjach o małym natężeniu ruchu trafienie wydajności jest znikome, podczas gdy w sytuacjach o dużym natężeniu ruchu potencjalna poprawa wydajności jest znaczna.</span><span class="sxs-lookup"><span data-stu-id="89efb-327">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="89efb-328">W poniższym kodzie `async` `Task<T>` słowo kluczowe, zwraca wartość, `await` słowo kluczowe i `ToListAsync` metoda sprawiają, że kod jest wykonywany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="89efb-328">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="89efb-329">Słowo `async` kluczowe mówi kompilatorowi do generowania wywołań zwrotnych dla `Task<IActionResult>` części treści metody i automatycznie utworzyć obiekt, który jest zwracany.</span><span class="sxs-lookup"><span data-stu-id="89efb-329">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>

* <span data-ttu-id="89efb-330">Typ `Task<IActionResult>` zwracania reprezentuje bieżącą pracę `IActionResult`z wynikiem typu .</span><span class="sxs-lookup"><span data-stu-id="89efb-330">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>

* <span data-ttu-id="89efb-331">Słowo `await` kluczowe powoduje, że kompilator podzielić metodę na dwie części.</span><span class="sxs-lookup"><span data-stu-id="89efb-331">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="89efb-332">Pierwsza część kończy się operacją, która jest uruchamiana asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="89efb-332">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="89efb-333">Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="89efb-333">The second part is put into a callback method that's called when the operation completes.</span></span>

* <span data-ttu-id="89efb-334">`ToListAsync`jest asynchroniczne wersji `ToList` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="89efb-334">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="89efb-335">Niektóre rzeczy, o których należy pamiętać podczas pisania kodu asynchroniowego, który używa entity framework:</span><span class="sxs-lookup"><span data-stu-id="89efb-335">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="89efb-336">Tylko instrukcje, które powodują kwerendy lub polecenia, które mają być wysyłane do bazy danych są wykonywane asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="89efb-336">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="89efb-337">Obejmuje to na `ToListAsync`przykład `SingleOrDefaultAsync`, `SaveChangesAsync`i .</span><span class="sxs-lookup"><span data-stu-id="89efb-337">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="89efb-338">Nie zawiera, na przykład, stwierdzeń, `IQueryable`które `var students = context.Students.Where(s => s.LastName == "Davolio")`po prostu zmienić , takich jak .</span><span class="sxs-lookup"><span data-stu-id="89efb-338">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>

* <span data-ttu-id="89efb-339">Kontekst EF nie jest bezpieczny dla wątków: nie próbuj wykonywać wielu operacji równolegle.</span><span class="sxs-lookup"><span data-stu-id="89efb-339">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="89efb-340">Po wywołaniu dowolnej metody asynchronii EF, zawsze używaj słowa kluczowego. `await`</span><span class="sxs-lookup"><span data-stu-id="89efb-340">When you call any async EF method, always use the `await` keyword.</span></span>

* <span data-ttu-id="89efb-341">Jeśli chcesz skorzystać z zalet wydajności kodu asynchronii, upewnij się, że wszystkie pakiety biblioteki, których używasz (na przykład do stronicowania), należy również użyć asynchronii, jeśli wywołają one wszelkie metody entity framework, które powodują kwerendy mają być wysyłane do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="89efb-341">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="89efb-342">Aby uzyskać więcej informacji na temat programowania asynchroniowego w .NET, zobacz [Omówienie asynchroniczne](/dotnet/articles/standard/async).</span><span class="sxs-lookup"><span data-stu-id="89efb-342">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="89efb-343">Uzyskiwanie kodu</span><span class="sxs-lookup"><span data-stu-id="89efb-343">Get the code</span></span>

[<span data-ttu-id="89efb-344">Pobierz lub wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="89efb-344">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="89efb-345">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="89efb-345">Next steps</span></span>

<span data-ttu-id="89efb-346">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="89efb-346">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="89efb-347">Utworzono ASP.NET core aplikacji internetowej MVC</span><span class="sxs-lookup"><span data-stu-id="89efb-347">Created ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="89efb-348">Konfigurowanie stylu witryny</span><span class="sxs-lookup"><span data-stu-id="89efb-348">Set up the site style</span></span>
> * <span data-ttu-id="89efb-349">Dowiedz się więcej o pakietach EF Core NuGet</span><span class="sxs-lookup"><span data-stu-id="89efb-349">Learned about EF Core NuGet packages</span></span>
> * <span data-ttu-id="89efb-350">Utworzono model danych</span><span class="sxs-lookup"><span data-stu-id="89efb-350">Created the data model</span></span>
> * <span data-ttu-id="89efb-351">Utworzono kontekst bazy danych</span><span class="sxs-lookup"><span data-stu-id="89efb-351">Created the database context</span></span>
> * <span data-ttu-id="89efb-352">Zarejestrowany SchoolContext</span><span class="sxs-lookup"><span data-stu-id="89efb-352">Registered the SchoolContext</span></span>
> * <span data-ttu-id="89efb-353">Zainicjowana baza danych z danymi testowymi</span><span class="sxs-lookup"><span data-stu-id="89efb-353">Initialized DB with test data</span></span>
> * <span data-ttu-id="89efb-354">Utworzony kontroler i widoki</span><span class="sxs-lookup"><span data-stu-id="89efb-354">Created controller and views</span></span>
> * <span data-ttu-id="89efb-355">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="89efb-355">Viewed the database</span></span>

<span data-ttu-id="89efb-356">W poniższym samouczku dowiesz się, jak wykonać podstawowe operacje CRUD (tworzenie, czytanie, aktualizowanie, usuwanie).</span><span class="sxs-lookup"><span data-stu-id="89efb-356">In the following tutorial, you'll learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

<span data-ttu-id="89efb-357">Przejdź do następnego samouczka, aby dowiedzieć się, jak wykonać podstawowe operacje CRUD (tworzenie, czytanie, aktualizowanie, usuwanie).</span><span class="sxs-lookup"><span data-stu-id="89efb-357">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="89efb-358">Wdrażanie podstawowych funkcji CRUD</span><span class="sxs-lookup"><span data-stu-id="89efb-358">Implement basic CRUD functionality</span></span>](crud.md)

