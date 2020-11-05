---
title: 'Samouczek: wprowadzenie do EF Core w aplikacji sieci Web ASP.NET MVC'
description: Ta strona jest pierwszą częścią serii samouczków, które wyjaśniają, jak skompilować przykładową aplikację Dr/MVC firmy Contoso.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
ms.topic: tutorial
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
uid: data/ef-mvc/intro
ms.openlocfilehash: 77cf1e9ad51b7044a35e1a9b2c125b0fdd91435e
ms.sourcegitcommit: 33f631a4427b9a422755601ac9119953db0b4a3e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365389"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a><span data-ttu-id="801fe-103">Samouczek: wprowadzenie do EF Core w aplikacji sieci Web ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="801fe-103">Tutorial: Get started with EF Core in an ASP.NET MVC web app</span></span>

<span data-ttu-id="801fe-104">Autorzy [Dykstra](https://github.com/tdykstra) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="801fe-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="801fe-105">Przykładowy punkt dostępu sieci Web firmy Contoso University demonstruje sposób tworzenia aplikacji sieci Web ASP.NET Core MVC przy użyciu narzędzia Entity Framework (EF) Core i programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="801fe-105">The Contoso University sample web ap demonstrates how to create an ASP.NET Core MVC web app using Entity Framework (EF) Core and Visual Studio.</span></span>

<span data-ttu-id="801fe-106">Przykładowa aplikacja jest witryną internetową fikcyjnej firmy Contoso University.</span><span class="sxs-lookup"><span data-stu-id="801fe-106">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="801fe-107">Obejmuje to funkcje, takie jak przyjmowanie studentów, tworzenie kursu i przydziały instruktora.</span><span class="sxs-lookup"><span data-stu-id="801fe-107">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="801fe-108">Jest to pierwsza z szeregu samouczków, które opisują sposób tworzenia przykładowej aplikacji firmy Contoso University.</span><span class="sxs-lookup"><span data-stu-id="801fe-108">This is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="801fe-109">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="801fe-109">Prerequisites</span></span>

* <span data-ttu-id="801fe-110">Jeśli dopiero zaczynasz korzystać z ASP.NET Core MVC, przejdź przez serię samouczka [wprowadzenie do ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc) przed rozpoczęciem tego.</span><span class="sxs-lookup"><span data-stu-id="801fe-110">If you're new to ASP.NET Core MVC, go through the [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc) tutorial series before starting this one.</span></span>

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

## <a name="database-engines"></a><span data-ttu-id="801fe-111">Aparaty bazy danych</span><span class="sxs-lookup"><span data-stu-id="801fe-111">Database engines</span></span>

<span data-ttu-id="801fe-112">Instrukcje programu Visual Studio używają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), wersji SQL Server Express, która działa tylko w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="801fe-112">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<!--
The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.

If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).
-->

## <a name="solve-problems-and-troubleshoot"></a><span data-ttu-id="801fe-113">Rozwiązywanie problemów i rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="801fe-113">Solve problems and troubleshoot</span></span>

<span data-ttu-id="801fe-114">Jeśli wystąpi problem, którego nie można rozwiązać, można ogólnie znaleźć rozwiązanie, porównując kod z [ukończonym projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="801fe-114">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples).</span></span> <span data-ttu-id="801fe-115">Aby zapoznać się z listą typowych błędów i sposobu ich rozwiązywania, zobacz [sekcję Rozwiązywanie problemów w ostatnim samouczku w serii](advanced.md#common-errors).</span><span class="sxs-lookup"><span data-stu-id="801fe-115">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="801fe-116">Jeśli nie możesz znaleźć tego, czego potrzebujesz, możesz ogłosić pytanie do StackOverflow.com dla [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="801fe-116">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="801fe-117">Jest to seria 10 samouczków, z których każdy jest oparty na tym, co zostało zrobione we wcześniejszych samouczkach.</span><span class="sxs-lookup"><span data-stu-id="801fe-117">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="801fe-118">Rozważ zapisanie kopii projektu po każdym pomyślnym zakończeniu samouczka.</span><span class="sxs-lookup"><span data-stu-id="801fe-118">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="801fe-119">Jeśli wystąpią problemy, możesz zacząć od poprzedniego samouczka zamiast wrócić do początku całej serii.</span><span class="sxs-lookup"><span data-stu-id="801fe-119">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="801fe-120">Aplikacja sieci Web firmy Contoso University</span><span class="sxs-lookup"><span data-stu-id="801fe-120">Contoso University web app</span></span>

<span data-ttu-id="801fe-121">Aplikacja skompilowana w tych samouczkach jest podstawową szkołą w sieci Web.</span><span class="sxs-lookup"><span data-stu-id="801fe-121">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="801fe-122">Użytkownicy mogą wyświetlać i aktualizować informacje dotyczące uczniów, kursów i instruktorów.</span><span class="sxs-lookup"><span data-stu-id="801fe-122">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="801fe-123">Oto kilka ekranów w aplikacji:</span><span class="sxs-lookup"><span data-stu-id="801fe-123">Here are a few of the screens in the app:</span></span>

![Strona indeksu uczniów](intro/_static/students-index.png)

![Strona edycji uczniów](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="801fe-126">Tworzenie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="801fe-126">Create web app</span></span>

* <span data-ttu-id="801fe-127">Uruchom program Visual Studio i wybierz pozycję **ASP.NET Core aplikacji sieci Web** > **dalej**.</span><span class="sxs-lookup"><span data-stu-id="801fe-127">Start Visual Studio and select **ASP.NET Core Web Application** > **NEXT**.</span></span>
* <span data-ttu-id="801fe-128">Nadaj nazwę projektowi `ContosoUniversity` .</span><span class="sxs-lookup"><span data-stu-id="801fe-128">Name the project `ContosoUniversity`.</span></span> <span data-ttu-id="801fe-129">Ważne jest, aby użyć tej dokładnej nazwy, w tym wielką literą, więc przestrzenie nazw są zgodne, gdy kod jest kopiowany.</span><span class="sxs-lookup"><span data-stu-id="801fe-129">It's important to use this exact name including capitalization, so the namespaces match when code is copied.</span></span>
* <span data-ttu-id="801fe-130">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="801fe-130">Select **Create**.</span></span>
* <span data-ttu-id="801fe-131">Wybierz pozycję **.NET Core** i **ASP.NET Core 5,0** na liście rozwijanej, a następnie wybierz szablon **aplikacja sieci Web (model-kontroler-kontrolera)** .</span><span class="sxs-lookup"><span data-stu-id="801fe-131">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application (Model-View-Controller)** template.</span></span>
  <span data-ttu-id="801fe-132">![Nowe okno dialogowe projektu ASP.NET Core](intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="801fe-132">![New ASP.NET Core Project dialog](intro/_static/new-aspnet5.png)</span></span>

## <a name="set-up-the-site-style"></a><span data-ttu-id="801fe-133">Konfigurowanie stylu witryny</span><span class="sxs-lookup"><span data-stu-id="801fe-133">Set up the site style</span></span>

<span data-ttu-id="801fe-134">Kilka podstawowych zmian powoduje skonfigurowanie menu witryny, układu i strony głównej.</span><span class="sxs-lookup"><span data-stu-id="801fe-134">A few basic changes set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="801fe-135">Otwórz *Widok widoki/Shared/_Layout. cshtml* i wprowadź następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="801fe-135">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="801fe-136">Zmień każde wystąpienie elementu `ContosoUniversity` na `Contoso University` .</span><span class="sxs-lookup"><span data-stu-id="801fe-136">Change each occurrence of `ContosoUniversity` to `Contoso University`.</span></span> <span data-ttu-id="801fe-137">Istnieją trzy wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="801fe-137">There are three occurrences.</span></span>
* <span data-ttu-id="801fe-138">Dodaj pozycje menu dla **informacji o** programie, **studentów** , **kursy** , **Instruktorzy** i **działy** , a następnie usuń wpis menu **prywatność** .</span><span class="sxs-lookup"><span data-stu-id="801fe-138">Add menu entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="801fe-139">Powyższe zmiany są wyróżnione w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="801fe-139">The preceding changes are highlighted in the following code:</span></span>

[!code-cshtml[](intro/samples/5cu/Views/Shared/_Layout.cshtml?highlight=6,24-38,52)]

<span data-ttu-id="801fe-140">W obszarze *widoki/Home/index. cshtml* Zastąp zawartość pliku następującym znacznikiem:</span><span class="sxs-lookup"><span data-stu-id="801fe-140">In *Views/Home/Index.cshtml* , replace the contents of the file with the following markup:</span></span>

[!code-cshtml[](intro/samples/5cu/Views/Home/Index.cshtml)]

<span data-ttu-id="801fe-141">Naciśnij klawisze CTRL + F5, aby uruchomić projekt, lub wybierz polecenie **debuguj > Uruchom bez debugowania** z menu.</span><span class="sxs-lookup"><span data-stu-id="801fe-141">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="801fe-142">Strona główna jest wyświetlana z kartami dla stron utworzonych w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="801fe-142">The home page is displayed with tabs for the pages created in this tutorial.</span></span>

![Strona główna firmy Contoso University](intro/_static/home-page5.png)

## <a name="ef-core-nuget-packages"></a><span data-ttu-id="801fe-144">EF Core pakietów NuGet</span><span class="sxs-lookup"><span data-stu-id="801fe-144">EF Core NuGet packages</span></span>

<span data-ttu-id="801fe-145">Ten samouczek używa SQL Server, a pakiet dostawcy to [Microsoft. EntityFrameworkCore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="801fe-145">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

<span data-ttu-id="801fe-146">Pakiet EF SQL Server i jego zależności `Microsoft.EntityFrameworkCore` oraz `Microsoft.EntityFrameworkCore.Relational` zapewnia obsługę środowiska uruchomieniowego dla EF.</span><span class="sxs-lookup"><span data-stu-id="801fe-146">The EF SQL Server package and its dependencies, `Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`, provide runtime support for EF.</span></span>

<span data-ttu-id="801fe-147">Dodaj pakiet NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) oraz pakiet NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) .</span><span class="sxs-lookup"><span data-stu-id="801fe-147">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package and the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span> <span data-ttu-id="801fe-148">W konsoli Menedżera programów (PMC) wprowadź następujące polecenia, aby dodać pakiety NuGet:</span><span class="sxs-lookup"><span data-stu-id="801fe-148">In the Program Manager Console (PMC), enter the following commands to add the NuGet packages:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
Install-Package Microsoft.EntityFrameworkCore.SqlServer -Version 5.0.0-rc.2.20475.6
```

<span data-ttu-id="801fe-149">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore`Pakiet NuGet zawiera ASP.NET Core oprogramowanie pośredniczące dla EF Core stron błędów.</span><span class="sxs-lookup"><span data-stu-id="801fe-149">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for EF Core error pages.</span></span> <span data-ttu-id="801fe-150">To oprogramowanie pośredniczące pomaga wykrywać i diagnozować błędy za pomocą migracji EF Core.</span><span class="sxs-lookup"><span data-stu-id="801fe-150">This middleware helps to detect and diagnose errors with EF Core migrations.</span></span>

<span data-ttu-id="801fe-151">Aby uzyskać informacje o innych dostawcach baz danych, które są dostępne dla EF Core, zobacz [dostawcy bazy danych](/ef/core/providers/).</span><span class="sxs-lookup"><span data-stu-id="801fe-151">For information about other database providers that are available for EF Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="801fe-152">Tworzenie modelu danych</span><span class="sxs-lookup"><span data-stu-id="801fe-152">Create the data model</span></span>

<span data-ttu-id="801fe-153">Dla tej aplikacji są tworzone następujące klasy jednostek:</span><span class="sxs-lookup"><span data-stu-id="801fe-153">The following entity classes are created for this app:</span></span>

![Kurs — Diagram modelu danych ucznia](intro/_static/data-model-diagram.png)

<span data-ttu-id="801fe-155">Poprzednie jednostki mają następujące relacje:</span><span class="sxs-lookup"><span data-stu-id="801fe-155">The preceding entities have the following relationships:</span></span>

* <span data-ttu-id="801fe-156">Relacja jeden do wielu między elementami `Student` i `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="801fe-156">A one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="801fe-157">Student może być zarejestrowany w dowolnej liczbie kursów.</span><span class="sxs-lookup"><span data-stu-id="801fe-157">A student can be enrolled in any number of courses.</span></span>
* <span data-ttu-id="801fe-158">Relacja jeden do wielu między elementami `Course` i `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="801fe-158">A one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="801fe-159">Kurs może mieć dowolną liczbę uczniów zarejestrowanych w tym obszarze.</span><span class="sxs-lookup"><span data-stu-id="801fe-159">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="801fe-160">W poniższych sekcjach jest tworzona Klasa dla każdej z tych jednostek.</span><span class="sxs-lookup"><span data-stu-id="801fe-160">In the following sections, a class is created for each of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="801fe-161">Jednostka ucznia</span><span class="sxs-lookup"><span data-stu-id="801fe-161">The Student entity</span></span>

![Diagram jednostek uczniów](intro/_static/student-entity.png)

<span data-ttu-id="801fe-163">W folderze *modele* Utwórz `Student` klasę o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="801fe-163">In the *Models* folder, create the `Student` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="801fe-164">`ID`Właściwość jest kolumną klucza podstawowego ( **PK** ) tabeli bazy danych, która odnosi się do tej klasy.</span><span class="sxs-lookup"><span data-stu-id="801fe-164">The `ID` property is the primary key ( **PK** ) column of the database table that corresponds to this class.</span></span> <span data-ttu-id="801fe-165">Domyślnie EF interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="801fe-165">By default, EF interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="801fe-166">Na przykład klucz podstawowy może być nazwany, `StudentID` a nie `ID` .</span><span class="sxs-lookup"><span data-stu-id="801fe-166">For example, the PK could be named `StudentID` rather than `ID`.</span></span>

<span data-ttu-id="801fe-167">`Enrollments`Właściwość jest [właściwością nawigacji](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="801fe-167">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="801fe-168">Właściwości nawigacji zawierają inne jednostki, które są powiązane z tą jednostką.</span><span class="sxs-lookup"><span data-stu-id="801fe-168">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="801fe-169">`Enrollments`Właściwość `Student` jednostki:</span><span class="sxs-lookup"><span data-stu-id="801fe-169">The `Enrollments` property of a `Student` entity:</span></span>

* <span data-ttu-id="801fe-170">Zawiera wszystkie `Enrollment` jednostki, które są powiązane z tą `Student` jednostką.</span><span class="sxs-lookup"><span data-stu-id="801fe-170">Contains all of the `Enrollment` entities that are related to that `Student` entity.</span></span>
* <span data-ttu-id="801fe-171">Jeśli konkretny `Student` wiersz w bazie danych ma dwa powiązane `Enrollment` wiersze:</span><span class="sxs-lookup"><span data-stu-id="801fe-171">If a specific `Student` row in the database has two related `Enrollment` rows:</span></span>
  * <span data-ttu-id="801fe-172">`Student` `Enrollments` Właściwość nawigacji tej jednostki zawiera te dwie `Enrollment` jednostki.</span><span class="sxs-lookup"><span data-stu-id="801fe-172">That `Student` entity's `Enrollments` navigation property contains those two `Enrollment` entities.</span></span>
  
<span data-ttu-id="801fe-173">`Enrollment` wiersze zawierają wartość klucza podstawowego ucznia w `StudentID` kolumnie klucz obcy ( **FK** ).</span><span class="sxs-lookup"><span data-stu-id="801fe-173">`Enrollment` rows contain a student's PK value in the `StudentID` foreign key ( **FK** ) column.</span></span>

<span data-ttu-id="801fe-174">Jeśli właściwość nawigacji może zawierać wiele jednostek:</span><span class="sxs-lookup"><span data-stu-id="801fe-174">If a navigation property can hold multiple entities:</span></span>

 * <span data-ttu-id="801fe-175">Typ musi być listą, taką jak `ICollection<T>` , `List<T>` , lub `HashSet<T>` .</span><span class="sxs-lookup"><span data-stu-id="801fe-175">The type must be a list, such as `ICollection<T>`, `List<T>`, or `HashSet<T>`.</span></span>
 * <span data-ttu-id="801fe-176">Jednostki można dodawać, usuwać i aktualizować.</span><span class="sxs-lookup"><span data-stu-id="801fe-176">Entities can be added, deleted, and updated.</span></span>

<span data-ttu-id="801fe-177">Relacje "wiele do wielu" i "jeden do wielu" mogą zawierać wiele jednostek.</span><span class="sxs-lookup"><span data-stu-id="801fe-177">Many-to-many and one-to-many navigation relationships can contain multiple entities.</span></span> <span data-ttu-id="801fe-178">Gdy `ICollection<T>` jest używany, EF domyślnie tworzy `HashSet<T>` kolekcję.</span><span class="sxs-lookup"><span data-stu-id="801fe-178">When `ICollection<T>` is used, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="801fe-179">Jednostka rejestracji</span><span class="sxs-lookup"><span data-stu-id="801fe-179">The Enrollment entity</span></span>

![Diagram jednostek rejestracji](intro/_static/enrollment-entity.png)

<span data-ttu-id="801fe-181">W folderze *modele* Utwórz `Enrollment` klasę o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="801fe-181">In the *Models* folder, create the `Enrollment` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="801fe-182">`EnrollmentID`Właściwość jest klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="801fe-182">The `EnrollmentID` property is the PK.</span></span> <span data-ttu-id="801fe-183">Ta jednostka używa `classnameID` wzorca zamiast `ID` samego siebie.</span><span class="sxs-lookup"><span data-stu-id="801fe-183">This entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="801fe-184">`Student`Jednostka użyła `ID` wzorca.</span><span class="sxs-lookup"><span data-stu-id="801fe-184">The `Student` entity used the `ID` pattern.</span></span> <span data-ttu-id="801fe-185">Niektórzy deweloperzy wolą używać jednego wzorca w całym modelu danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-185">Some developers prefer to use one pattern throughout the data model.</span></span> <span data-ttu-id="801fe-186">W tym samouczku odmiana pokazuje, że można użyć wzorca.</span><span class="sxs-lookup"><span data-stu-id="801fe-186">In this tutorial, the variation illustrates that either pattern can be used.</span></span> <span data-ttu-id="801fe-187">W tym [samouczku](inheritance.md) pokazano, jak używać `ID` bez ClassName, ułatwia implementowanie dziedziczenia w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-187">A [later tutorial](inheritance.md) shows how using `ID` without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="801fe-188">`Grade`Właściwość jest `enum` .</span><span class="sxs-lookup"><span data-stu-id="801fe-188">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="801fe-189">`?`Po `Grade` deklaracji typu wskazuje, że `Grade` Właściwość [dopuszcza wartość null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types).</span><span class="sxs-lookup"><span data-stu-id="801fe-189">The `?` after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types).</span></span> <span data-ttu-id="801fe-190">Klasa, która `null` jest różna od zerowej klasy.</span><span class="sxs-lookup"><span data-stu-id="801fe-190">A grade that's `null` is different from a zero grade.</span></span> <span data-ttu-id="801fe-191">`null` oznacza, że Klasa nie jest znana lub nie została jeszcze przypisana.</span><span class="sxs-lookup"><span data-stu-id="801fe-191">`null` means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="801fe-192">`StudentID`Właściwość jest kluczem obcym (FK), a odpowiednia właściwość nawigacji to `Student` .</span><span class="sxs-lookup"><span data-stu-id="801fe-192">The `StudentID` property is a foreign key (FK), and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="801fe-193">`Enrollment`Jednostka jest skojarzona z jedną `Student` jednostką, więc właściwość może zawierać tylko jedną `Student` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="801fe-193">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity.</span></span> <span data-ttu-id="801fe-194">Różni się to od `Student.Enrollments` właściwości nawigacji, która może zawierać wiele `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="801fe-194">This differs from the `Student.Enrollments` navigation property, which can hold multiple `Enrollment` entities.</span></span>

<span data-ttu-id="801fe-195">`CourseID`Właściwość jest FK, a odpowiednia właściwość nawigacji to `Course` .</span><span class="sxs-lookup"><span data-stu-id="801fe-195">The `CourseID` property is a FK, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="801fe-196">`Enrollment`Jednostka jest skojarzona z jedną `Course` jednostką.</span><span class="sxs-lookup"><span data-stu-id="801fe-196">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="801fe-197">Entity Framework interpretuje właściwość jako właściwość klucza obcego, jeśli ma nazwę właściwości `<` nawigacyjnej nazwa `><` właściwości klucza podstawowego `>` .</span><span class="sxs-lookup"><span data-stu-id="801fe-197">Entity Framework interprets a property as a FK property if it's named `<`navigation property name`><`primary key property name`>`.</span></span> <span data-ttu-id="801fe-198">Na przykład `StudentID` dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` .</span><span class="sxs-lookup"><span data-stu-id="801fe-198">For example, `StudentID` for the `Student` navigation property since the `Student` entity's PK is `ID`.</span></span> <span data-ttu-id="801fe-199">Właściwości klucza obcego mogą również mieć  `<` nazwę właściwości klucz podstawowy `>` .</span><span class="sxs-lookup"><span data-stu-id="801fe-199">FK properties can also be named  `<`primary key property name`>`.</span></span> <span data-ttu-id="801fe-200">Na przykład, `CourseID` ponieważ `Course` klucz podstawowy jednostki to `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="801fe-200">For example, `CourseID` because the `Course` entity's PK is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="801fe-201">Jednostka kursu</span><span class="sxs-lookup"><span data-stu-id="801fe-201">The Course entity</span></span>

![Diagram jednostek kursu](intro/_static/course-entity.png)

<span data-ttu-id="801fe-203">W folderze *modele* Utwórz `Course` klasę o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="801fe-203">In the *Models* folder, create the `Course` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="801fe-204">`Enrollments`Właściwość jest właściwością nawigacji.</span><span class="sxs-lookup"><span data-stu-id="801fe-204">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="801fe-205">`Course`Jednostka może być powiązana z dowolną liczbą `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="801fe-205">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="801fe-206">Atrybut [DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) został wyjaśniony w [późniejszym samouczku](complex-data-model.md).</span><span class="sxs-lookup"><span data-stu-id="801fe-206">The [DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) attribute is explained in a [later tutorial](complex-data-model.md).</span></span> <span data-ttu-id="801fe-207">Ten atrybut umożliwia wprowadzenie klucza podstawowego dla kursu, a nie jego wygenerowanie.</span><span class="sxs-lookup"><span data-stu-id="801fe-207">This attribute allows entering the PK for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="801fe-208">Tworzenie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="801fe-208">Create the database context</span></span>

<span data-ttu-id="801fe-209">Klasa główna, która koordynuje funkcje EF dla danego modelu danych, jest <xref:Microsoft.EntityFrameworkCore.DbContext> klasą kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-209">The main class that coordinates EF functionality for a given data model is the <xref:Microsoft.EntityFrameworkCore.DbContext> database context class.</span></span> <span data-ttu-id="801fe-210">Ta klasa jest tworzona przez wyprowadzanie z `Microsoft.EntityFrameworkCore.DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="801fe-210">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="801fe-211">`DbContext`Klasa pochodna określa, które jednostki są uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-211">The `DbContext` derived class specifies which entities are included in the data model.</span></span> <span data-ttu-id="801fe-212">Niektóre zachowania EF można dostosować.</span><span class="sxs-lookup"><span data-stu-id="801fe-212">Some EF behaviors can be customized.</span></span> <span data-ttu-id="801fe-213">W tym projekcie Klasa ma nazwę `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="801fe-213">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="801fe-214">W folderze projektu Utwórz folder o nazwie `Data` .</span><span class="sxs-lookup"><span data-stu-id="801fe-214">In the project folder, create a folder named `Data`.</span></span>

<span data-ttu-id="801fe-215">W folderze *dane* Utwórz `SchoolContext` klasę o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="801fe-215">In the *Data* folder create a `SchoolContext` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="801fe-216">Poprzedni kod tworzy `DbSet` Właściwość dla każdego zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="801fe-216">The preceding code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="801fe-217">W terminologii EF:</span><span class="sxs-lookup"><span data-stu-id="801fe-217">In EF terminology:</span></span>

* <span data-ttu-id="801fe-218">Zestaw jednostek zwykle odpowiada tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-218">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="801fe-219">Jednostka odnosi się do wiersza w tabeli.</span><span class="sxs-lookup"><span data-stu-id="801fe-219">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="801fe-220">`DbSet<Enrollment>`Instrukcje i `DbSet<Course>` mogą być pominięte i mogą działać tak samo.</span><span class="sxs-lookup"><span data-stu-id="801fe-220">The `DbSet<Enrollment>` and `DbSet<Course>` statements could be omitted and it would work the same.</span></span> <span data-ttu-id="801fe-221">EF uwzględni je niejawnie, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="801fe-221">EF would include them implicitly because:</span></span>

* <span data-ttu-id="801fe-222">`Student`Jednostka odwołuje się do `Enrollment` jednostki.</span><span class="sxs-lookup"><span data-stu-id="801fe-222">The `Student` entity references the `Enrollment` entity.</span></span>
* <span data-ttu-id="801fe-223">`Enrollment`Jednostka odwołuje się do `Course` jednostki.</span><span class="sxs-lookup"><span data-stu-id="801fe-223">The `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="801fe-224">Po utworzeniu bazy danych EF tworzy tabele, które mają nazwy takie same jak `DbSet` nazwy właściwości.</span><span class="sxs-lookup"><span data-stu-id="801fe-224">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="801fe-225">Nazwy właściwości dla kolekcji są zwykle plural.</span><span class="sxs-lookup"><span data-stu-id="801fe-225">Property names for collections are typically plural.</span></span> <span data-ttu-id="801fe-226">Na przykład `Students` zamiast `Student` .</span><span class="sxs-lookup"><span data-stu-id="801fe-226">For example, `Students` rather than `Student`.</span></span> <span data-ttu-id="801fe-227">Deweloperzy nie zgadzają się, czy nazwy tabel powinny być w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="801fe-227">Developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="801fe-228">W przypadku tych samouczków domyślne zachowanie jest przesłonięte przez określenie pojedynczej nazwy tabeli w `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="801fe-228">For these tutorials, the default behavior is overridden by specifying singular table names in the `DbContext`.</span></span> <span data-ttu-id="801fe-229">Aby to zrobić, Dodaj następujący wyróżniony kod po ostatniej właściwości Nieogólnymi.</span><span class="sxs-lookup"><span data-stu-id="801fe-229">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a><span data-ttu-id="801fe-230">Zarejestruj `SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="801fe-230">Register the `SchoolContext`</span></span>

<span data-ttu-id="801fe-231">ASP.NET Core obejmuje [iniekcję zależności](../../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="801fe-231">ASP.NET Core includes [dependency injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="801fe-232">Usługi, takie jak kontekst bazy danych EF, są rejestrowane z iniekcją zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="801fe-232">Services, such as the EF database context, are registered with dependency injection during app startup.</span></span> <span data-ttu-id="801fe-233">Składniki, które wymagają tych usług, takich jak kontrolery MVC, są dostarczane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="801fe-233">Components that require these services, such as MVC controllers, are provided these services via constructor parameters.</span></span> <span data-ttu-id="801fe-234">Kod konstruktora kontrolera, który pobiera wystąpienie kontekstu, jest przedstawiony w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="801fe-234">The controller constructor code that gets a context instance is shown later in this tutorial.</span></span>

<span data-ttu-id="801fe-235">Aby zarejestrować się `SchoolContext` jako usługa, otwórz *Startup.cs* i Dodaj wyróżnione wiersze do `ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="801fe-235">To register `SchoolContext` as a service, open *Startup.cs* , and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/5cu-snap/Startup.cs?name=snippet&highlight=1-2,22-23)]

<span data-ttu-id="801fe-236">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w `DbContextOptionsBuilder` obiekcie.</span><span class="sxs-lookup"><span data-stu-id="801fe-236">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="801fe-237">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *:::no-loc(appsettings.json):::* pliku.</span><span class="sxs-lookup"><span data-stu-id="801fe-237">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

<span data-ttu-id="801fe-238">Otwórz *:::no-loc(appsettings.json):::* plik i Dodaj parametry połączenia, jak pokazano w następującej postaci:</span><span class="sxs-lookup"><span data-stu-id="801fe-238">Open the *:::no-loc(appsettings.json):::* file and add a connection string as shown in the following markup:</span></span>

[!code-json[](./intro/samples/5cu/appsettings1.json?highlight=2-4)]

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="801fe-239">Dodaj filtr wyjątku bazy danych</span><span class="sxs-lookup"><span data-stu-id="801fe-239">Add the database exception filter</span></span>

<span data-ttu-id="801fe-240">Dodaj <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> do `ConfigureServices` , jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="801fe-240">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

[!code-csharp[](intro/samples/5cu/Startup.cs?name=snippet&highlight=1=2,22-23)]

<span data-ttu-id="801fe-241">`AddDatabaseDeveloperPageExceptionFilter`Zapewnia przydatne informacje o błędzie w [środowisku programistycznym](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="801fe-241">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="801fe-242">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="801fe-242">SQL Server Express LocalDB</span></span>

<span data-ttu-id="801fe-243">Parametry połączenia określają [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="801fe-243">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="801fe-244">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użycia w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="801fe-244">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="801fe-245">LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie istnieje złożona konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="801fe-245">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="801fe-246">Domyślnie LocalDB tworzy pliki *. mdf* DB w `C:/Users/<user>` katalogu.</span><span class="sxs-lookup"><span data-stu-id="801fe-246">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="801fe-247">Zainicjuj bazę danych z danymi testowymi</span><span class="sxs-lookup"><span data-stu-id="801fe-247">Initialize DB with test data</span></span>

<span data-ttu-id="801fe-248">EF tworzy pustą bazę danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-248">EF creates an empty database.</span></span> <span data-ttu-id="801fe-249">W tej sekcji dodano metodę, która jest wywoływana po utworzeniu bazy danych w celu wypełnienia jej danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="801fe-249">In this section, a method is added that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="801fe-250">`EnsureCreated`Metoda jest używana do automatycznego tworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-250">The `EnsureCreated` method is used to automatically create the database.</span></span> <span data-ttu-id="801fe-251">W [późniejszym samouczku](migrations.md)zobaczysz, jak obsługiwać zmiany modelu przy użyciu migracje Code First, aby zmienić schemat bazy danych zamiast upuszczania i ponownego tworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-251">In a [later tutorial](migrations.md), you see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="801fe-252">W folderze *dane* Utwórz nową klasę o nazwie `DbInitializer` przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="801fe-252">In the *Data* folder, create a new class named `DbInitializer` with the following code:</span></span>

[!code-csharp[DbInitializer](intro/samples/5cu-snap/DbInitializer.cs)]

<span data-ttu-id="801fe-253">Poprzedni kod sprawdza, czy baza danych istnieje:</span><span class="sxs-lookup"><span data-stu-id="801fe-253">The preceding code checks if the database exists:</span></span>

* <span data-ttu-id="801fe-254">Jeśli baza danych nie zostanie znaleziona,</span><span class="sxs-lookup"><span data-stu-id="801fe-254">If the database is not found;</span></span>
  * <span data-ttu-id="801fe-255">Jest on tworzony i ładowany z danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="801fe-255">It is created and loaded with test data.</span></span> <span data-ttu-id="801fe-256">Ładuje dane testowe do tablic, a nie `List<T>` kolekcji w celu zoptymalizowania wydajności.</span><span class="sxs-lookup"><span data-stu-id="801fe-256">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>
* <span data-ttu-id="801fe-257">Jeśli baza danych zostanie znaleziona, nie podejmuje żadnych działań.</span><span class="sxs-lookup"><span data-stu-id="801fe-257">If the database if found, it takes no action.</span></span>

<span data-ttu-id="801fe-258">Zaktualizuj *program.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="801fe-258">Update *Program.cs* with the following code:</span></span>

[!code-csharp[Program file](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-37)]

<span data-ttu-id="801fe-259">*Program.cs* wykonuje następujące czynności podczas uruchamiania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="801fe-259">*Program.cs* does the following on app startup:</span></span>

* <span data-ttu-id="801fe-260">Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="801fe-260">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="801fe-261">Wywołaj `DbInitializer.Initialize` metodę.</span><span class="sxs-lookup"><span data-stu-id="801fe-261">Call the `DbInitializer.Initialize` method.</span></span>
* <span data-ttu-id="801fe-262">Usuń kontekst, gdy `Initialize` Metoda zostanie ukończona, jak pokazano w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="801fe-262">Dispose the context when the `Initialize` method completes as shown in the following code:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=5-18)]

<span data-ttu-id="801fe-263">Gdy aplikacja jest uruchamiana po raz pierwszy, baza danych zostanie utworzona i załadowana z danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="801fe-263">The first time the app is run, the database is created and loaded with test data.</span></span> <span data-ttu-id="801fe-264">Za każdym razem, gdy zmienia się model danych:</span><span class="sxs-lookup"><span data-stu-id="801fe-264">Whenever the data model changes:</span></span>

* <span data-ttu-id="801fe-265">Usuń bazę danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-265">Delete the database.</span></span>
* <span data-ttu-id="801fe-266">Zaktualizuj metodę inicjatora i zacznij od nowa bazę danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-266">Update the seed method, and start afresh with a new database.</span></span>

 <span data-ttu-id="801fe-267">W kolejnych samouczkach baza danych jest modyfikowana, gdy model danych ulegnie zmianie, bez usuwania i ponownego tworzenia.</span><span class="sxs-lookup"><span data-stu-id="801fe-267">In later tutorials, the database is modified when the data model changes, without deleting and re-creating it.</span></span> <span data-ttu-id="801fe-268">Po zmianie modelu danych nie są tracone żadne dane.</span><span class="sxs-lookup"><span data-stu-id="801fe-268">No data is lost when the data model changes.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="801fe-269">Tworzenie kontrolera i widoków</span><span class="sxs-lookup"><span data-stu-id="801fe-269">Create controller and views</span></span>

<span data-ttu-id="801fe-270">Użyj aparatu tworzenia szkieletów w programie Visual Studio, aby dodać kontroler MVC i widoki, które będą używać EF do wykonywania zapytań i zapisywania danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-270">Use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="801fe-271">Automatyczne tworzenie metod i widoków akcji [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) jest znane jako rusztowania.</span><span class="sxs-lookup"><span data-stu-id="801fe-271">The automatic creation of [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) action methods and views is known as scaffolding.</span></span>

* <span data-ttu-id="801fe-272">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `Controllers` folder, a następnie wybierz pozycję **Dodaj > nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="801fe-272">In **Solution Explorer** , right-click the `Controllers` folder  and select **Add > New Scaffolded Item**.</span></span>
* <span data-ttu-id="801fe-273">W oknie dialogowym **Dodawanie szkieletu** :</span><span class="sxs-lookup"><span data-stu-id="801fe-273">In the **Add Scaffold** dialog box:</span></span>
  * <span data-ttu-id="801fe-274">Wybierz **kontroler MVC z widokami, używając Entity Framework**.</span><span class="sxs-lookup"><span data-stu-id="801fe-274">Select **MVC controller with views, using Entity Framework**.</span></span>
  * <span data-ttu-id="801fe-275">Kliknij pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="801fe-275">Click **Add**.</span></span> <span data-ttu-id="801fe-276">Pojawia się okno dialogowe **Dodawanie kontrolera MVC z widokami z użyciem Entity Framework** : ![ studenta dla szkieletu](intro/_static/scaffold-student2.png)</span><span class="sxs-lookup"><span data-stu-id="801fe-276">The **Add MVC Controller with views, using Entity Framework** dialog box appears: ![Scaffold Student](intro/_static/scaffold-student2.png)</span></span>
  * <span data-ttu-id="801fe-277">W obszarze **Klasa modelu** wybierz pozycję **student**.</span><span class="sxs-lookup"><span data-stu-id="801fe-277">In **Model class** , select **Student**.</span></span>
  * <span data-ttu-id="801fe-278">W obszarze **Klasa kontekstu danych** wybierz pozycję **SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="801fe-278">In **Data context class** , select **SchoolContext**.</span></span>
  * <span data-ttu-id="801fe-279">Zaakceptuj domyślną **StudentsController** jako nazwę.</span><span class="sxs-lookup"><span data-stu-id="801fe-279">Accept the default **StudentsController** as the name.</span></span>
  * <span data-ttu-id="801fe-280">Kliknij pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="801fe-280">Click **Add**.</span></span>

<span data-ttu-id="801fe-281">Aparat szkieletu programu Visual Studio tworzy `StudentsController.cs` plik i zestaw widoków ( `*.cshtml` plików), które współpracują z kontrolerem.</span><span class="sxs-lookup"><span data-stu-id="801fe-281">The Visual Studio scaffolding engine creates a `StudentsController.cs` file and a set of views (`*.cshtml` files) that work with the controller.</span></span>

<span data-ttu-id="801fe-282">Zwróć uwagę, że kontroler przyjmuje `SchoolContext` jako parametr konstruktora.</span><span class="sxs-lookup"><span data-stu-id="801fe-282">Notice the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="801fe-283">ASP.NET Core iniekcja zależności ma zadbać o przekazanie wystąpienia `SchoolContext` do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="801fe-283">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="801fe-284">Skonfigurowano tę wartość w `Startup` klasie.</span><span class="sxs-lookup"><span data-stu-id="801fe-284">You configured that in the `Startup` class.</span></span>

<span data-ttu-id="801fe-285">Kontroler zawiera `Index` metodę akcji, która wyświetla wszystkich uczniów w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-285">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="801fe-286">Metoda pobiera listę studentów z zestawu jednostek studentów, odczytując `Students` Właściwość wystąpienia kontekstu bazy danych:</span><span class="sxs-lookup"><span data-stu-id="801fe-286">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="801fe-287">Asynchroniczne elementy programowania w tym kodzie są wyjaśnione w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="801fe-287">The asynchronous programming elements in this code are explained later in the tutorial.</span></span>

<span data-ttu-id="801fe-288">Widok *widoki/uczniowie/index. cshtml* wyświetla tę listę w tabeli:</span><span class="sxs-lookup"><span data-stu-id="801fe-288">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="801fe-289">Naciśnij klawisze CTRL + F5, aby uruchomić projekt, lub wybierz polecenie **debuguj > Uruchom bez debugowania** z menu.</span><span class="sxs-lookup"><span data-stu-id="801fe-289">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="801fe-290">Kliknij kartę studenci, aby zobaczyć dane testowe, które zostały `DbInitializer.Initialize` wstawione przez metodę.</span><span class="sxs-lookup"><span data-stu-id="801fe-290">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="801fe-291">W zależności od tego, jak Zawężasz okno przeglądarki, zobaczysz `Students` link do karty w górnej części strony lub kliknij ikonę nawigacji w prawym górnym rogu, aby zobaczyć link.</span><span class="sxs-lookup"><span data-stu-id="801fe-291">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Strona główna firmy Contoso University Narrow](intro/_static/home-page-narrow.png)

![Strona indeksu uczniów](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="801fe-294">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="801fe-294">View the database</span></span>

<span data-ttu-id="801fe-295">Gdy aplikacja zostanie uruchomiona, `DbInitializer.Initialize` Metoda wywołuje metodę `EnsureCreated` .</span><span class="sxs-lookup"><span data-stu-id="801fe-295">When the app is started, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="801fe-296">EF wykryto, że nie ma bazy danych:</span><span class="sxs-lookup"><span data-stu-id="801fe-296">EF saw that there was no database:</span></span>

* <span data-ttu-id="801fe-297">W związku z tym utworzono bazę danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-297">So it created a database.</span></span>
* <span data-ttu-id="801fe-298">`Initialize`Kod metody wypełniał bazę danych danymi.</span><span class="sxs-lookup"><span data-stu-id="801fe-298">The `Initialize` method code populated the database with data.</span></span>

<span data-ttu-id="801fe-299">Użyj **Eksplorator obiektów SQL Server** (SSOX), aby wyświetlić bazę danych w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="801fe-299">Use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio:</span></span>

* <span data-ttu-id="801fe-300">Wybierz **Eksplorator obiektów SQL Server** z menu **Widok** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="801fe-300">Select **SQL Server Object Explorer** from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="801fe-301">W SSOX wybierz pozycję **(LocalDB) \MSSQLLocalDB > bazy danych**.</span><span class="sxs-lookup"><span data-stu-id="801fe-301">In SSOX, select **(localdb)\MSSQLLocalDB > Databases**.</span></span>
* <span data-ttu-id="801fe-302">Wybierz pozycję `ContosoUniversity1` Nazwa bazy danych, która znajduje się w parametrach połączenia w *:::no-loc(appsettings.json):::* pliku.</span><span class="sxs-lookup"><span data-stu-id="801fe-302">Select `ContosoUniversity1`, the entry for the database name that's in the connection string in the *:::no-loc(appsettings.json):::* file.</span></span>
* <span data-ttu-id="801fe-303">Rozwiń węzeł **tabele** , aby wyświetlić tabele w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-303">Expand the **Tables** node to see the tables in the database.</span></span>

![Tabele w SSOX](intro/_static/ssox-tables.png)

<span data-ttu-id="801fe-305">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl dane** , aby wyświetlić dane w tabeli.</span><span class="sxs-lookup"><span data-stu-id="801fe-305">Right-click the **Student** table and click **View Data** to see the data in the table.</span></span>

![Tabela uczniów w SSOX](intro/_static/ssox-student-table.png)

<span data-ttu-id="801fe-307">`*.mdf` `*.ldf` Pliki bazy danych i znajdują się w folderze *C:\Users \\ \<username>* .</span><span class="sxs-lookup"><span data-stu-id="801fe-307">The `*.mdf` and `*.ldf` database files are in the *C:\Users\\\<username>* folder.</span></span>

<span data-ttu-id="801fe-308">Ponieważ `EnsureCreated` jest wywoływana w metodzie inicjatora, która jest uruchamiana podczas uruchamiania aplikacji, można:</span><span class="sxs-lookup"><span data-stu-id="801fe-308">Because `EnsureCreated` is called in the initializer method that runs on app start, you could:</span></span>

* <span data-ttu-id="801fe-309">Wprowadź zmianę w `Student` klasie.</span><span class="sxs-lookup"><span data-stu-id="801fe-309">Make a change to the `Student` class.</span></span>
* <span data-ttu-id="801fe-310">Usuń bazę danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-310">Delete the database.</span></span>
* <span data-ttu-id="801fe-311">Zatrzymaj, a następnie uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="801fe-311">Stop, then start the app.</span></span> <span data-ttu-id="801fe-312">Baza danych zostanie automatycznie utworzona w celu dopasowania do zmiany.</span><span class="sxs-lookup"><span data-stu-id="801fe-312">The database is automatically re-created to match the change.</span></span>

<span data-ttu-id="801fe-313">Na przykład, jeśli `EmailAddress` do klasy zostanie dodana Właściwość `Student` , Nowa `EmailAddress` kolumna w nowo utworzonej tabeli.</span><span class="sxs-lookup"><span data-stu-id="801fe-313">For example, if an `EmailAddress` property is added to the `Student` class, a new `EmailAddress` column in the re-created table.</span></span> <span data-ttu-id="801fe-314">Nie zostanie wyświetlona nowa właściwość widoku z klasą `EmailAddress` .</span><span class="sxs-lookup"><span data-stu-id="801fe-314">The view classed won't display the new `EmailAddress` property.</span></span>

## <a name="conventions"></a><span data-ttu-id="801fe-315">Konwencje</span><span class="sxs-lookup"><span data-stu-id="801fe-315">Conventions</span></span>

<span data-ttu-id="801fe-316">Ilość kodu zapisywana w celu utworzenia kompletnej bazy danych przez EF, jest minimalna ze względu na użycie konwencji EF korzysta z:</span><span class="sxs-lookup"><span data-stu-id="801fe-316">The amount of code written in order for the EF to to create a complete database is minimal because of the use of the conventions EF uses:</span></span>

* <span data-ttu-id="801fe-317">Nazwy `DbSet` właściwości są używane jako nazwy tabel.</span><span class="sxs-lookup"><span data-stu-id="801fe-317">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="801fe-318">W przypadku jednostek, do których nie odwołuje się `DbSet` Właściwość, nazwy klas jednostek są używane jako nazwy tabel.</span><span class="sxs-lookup"><span data-stu-id="801fe-318">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>
* <span data-ttu-id="801fe-319">Nazwy właściwości jednostki są używane w nazwach kolumn.</span><span class="sxs-lookup"><span data-stu-id="801fe-319">Entity property names are used for column names.</span></span>
* <span data-ttu-id="801fe-320">Właściwości jednostki o nazwach `ID` lub `classnameID` są rozpoznawane jako właściwości klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="801fe-320">Entity properties that are named `ID` or `classnameID` are recognized as PK properties.</span></span>
* <span data-ttu-id="801fe-321">Właściwość jest interpretowana jako właściwość klucza obcego, jeśli ma nazwę właściwość `<` nawigacji nazwa `><` klucza podstawowego `>` .</span><span class="sxs-lookup"><span data-stu-id="801fe-321">A property is interpreted as a FK property if it's named `<`navigation property name`><`PK property name`>`.</span></span> <span data-ttu-id="801fe-322">Na przykład `StudentID` dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` .</span><span class="sxs-lookup"><span data-stu-id="801fe-322">For example, `StudentID` for the `Student` navigation property since the `Student` entity's PK is `ID`.</span></span> <span data-ttu-id="801fe-323">Właściwości klucza obcego mogą również mieć `<` nazwę właściwości klucz podstawowy `>` .</span><span class="sxs-lookup"><span data-stu-id="801fe-323">FK properties can also be named `<`primary key property name`>`.</span></span> <span data-ttu-id="801fe-324">Na przykład, `EnrollmentID` ponieważ `Enrollment` klucz podstawowy jednostki to `EnrollmentID` .</span><span class="sxs-lookup"><span data-stu-id="801fe-324">For example, `EnrollmentID` since the `Enrollment` entity's PK is `EnrollmentID`.</span></span>

<span data-ttu-id="801fe-325">Zachowanie konwencjonalne można zastąpić.</span><span class="sxs-lookup"><span data-stu-id="801fe-325">Conventional behavior can be overridden.</span></span> <span data-ttu-id="801fe-326">Na przykład nazwy tabel można jawnie określić, jak pokazano wcześniej w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="801fe-326">For example, table names can be explicitly specified, as shown earlier in this tutorial.</span></span> <span data-ttu-id="801fe-327">Nazwy kolumn i dowolnej właściwości można ustawić jako PK lub FK.</span><span class="sxs-lookup"><span data-stu-id="801fe-327">Column names and any property can be set as a PK or FK.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="801fe-328">Kod asynchroniczny</span><span class="sxs-lookup"><span data-stu-id="801fe-328">Asynchronous code</span></span>

<span data-ttu-id="801fe-329">Programowanie asynchroniczne jest trybem domyślnym dla ASP.NET Core i EF Core.</span><span class="sxs-lookup"><span data-stu-id="801fe-329">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="801fe-330">Serwer sieci Web ma ograniczoną liczbę dostępnych wątków, a w przypadku dużego obciążenia mogą być używane wszystkie dostępne wątki.</span><span class="sxs-lookup"><span data-stu-id="801fe-330">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="801fe-331">W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione.</span><span class="sxs-lookup"><span data-stu-id="801fe-331">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="801fe-332">W przypadku kodu synchronicznego wiele wątków może zostać powiązanych, podczas gdy nie wykonuje żadnej pracy, ponieważ oczekuje na ukończenie operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="801fe-332">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="801fe-333">W przypadku kodu asynchronicznego, gdy proces oczekuje na ukończenie operacji we/wy, jego wątek zostanie zwolniony dla serwera do użycia na potrzeby przetwarzania innych żądań.</span><span class="sxs-lookup"><span data-stu-id="801fe-333">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="801fe-334">W efekcie kod asynchroniczny umożliwia efektywniejsze wykorzystanie zasobów serwera, a serwer jest włączony do obsługi większej ilości ruchu bez opóźnień.</span><span class="sxs-lookup"><span data-stu-id="801fe-334">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="801fe-335">Kod asynchroniczny wprowadza niewielką ilość narzutu w czasie wykonywania, ale w przypadku niskiego natężenia ruchu, gdy wydajność jest niewielka, w przypadku dużych sytuacji związanych z ruchem jest istotna poprawa wydajności.</span><span class="sxs-lookup"><span data-stu-id="801fe-335">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="801fe-336">W poniższym kodzie,, `async` , `Task<T>` `await` i sprawić, że `ToListAsync` kod jest wykonywany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="801fe-336">In the following code, `async`, `Task<T>`, `await`, and `ToListAsync` make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="801fe-337">`async`Słowo kluczowe instruuje kompilator, aby generował wywołania zwrotne dla części treści metody i automatycznie utworzyć `Task<IActionResult>` zwracany obiekt.</span><span class="sxs-lookup"><span data-stu-id="801fe-337">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>
* <span data-ttu-id="801fe-338">Typ zwracany `Task<IActionResult>` reprezentuje bieżącą współpracę z wynikiem typu `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="801fe-338">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>
* <span data-ttu-id="801fe-339">`await`Słowo kluczowe powoduje, że kompilator dzieli metodę na dwie części.</span><span class="sxs-lookup"><span data-stu-id="801fe-339">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="801fe-340">Pierwsza część jest zakończona operacją uruchomioną asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="801fe-340">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="801fe-341">Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="801fe-341">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="801fe-342">`ToListAsync` jest asynchroniczną wersją `ToList` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="801fe-342">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="801fe-343">Niektóre kwestie, o których należy pamiętać podczas pisania asynchronicznego kodu, który używa EF:</span><span class="sxs-lookup"><span data-stu-id="801fe-343">Some things to be aware of when  writing asynchronous code that uses EF:</span></span>

* <span data-ttu-id="801fe-344">Tylko instrukcje, które powodują, że zapytania lub polecenia wysyłane do bazy danych są wykonywane asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="801fe-344">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="801fe-345">Obejmuje to, na przykład, `ToListAsync` , `SingleOrDefaultAsync` i `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="801fe-345">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="801fe-346">Nie zawiera na przykład instrukcji, które po prostu zmieniają element `IQueryable` , taki jak `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="801fe-346">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="801fe-347">Kontekst EF nie jest bezpieczny wątkowo: nie próbuj wykonać równolegle wielu operacji.</span><span class="sxs-lookup"><span data-stu-id="801fe-347">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="801fe-348">Gdy wywoływana jest metoda async EF, zawsze używaj `await` słowa kluczowego.</span><span class="sxs-lookup"><span data-stu-id="801fe-348">When you call any async EF method, always use the `await` keyword.</span></span>
* <span data-ttu-id="801fe-349">Aby skorzystać z zalet wydajności w kodzie asynchronicznym, należy się upewnić, że wszystkie pakiety biblioteczne używają również metody asynchronicznej, jeśli wywołają one wszelkie metod EF, które powodują wysłanie zapytań do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-349">To take advantage of the performance benefits of async code, make sure that any library packages used also use async if they call any EF methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="801fe-350">Aby uzyskać więcej informacji na temat programowania asynchronicznego w programie .NET, zobacz [asynchroniczne Omówienie](/dotnet/articles/standard/async).</span><span class="sxs-lookup"><span data-stu-id="801fe-350">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="limit-entities-fetched"></a><span data-ttu-id="801fe-351">Pobrano limit liczby jednostek</span><span class="sxs-lookup"><span data-stu-id="801fe-351">Limit entities fetched</span></span>

<span data-ttu-id="801fe-352">Zobacz [zagadnienia dotyczące wydajności](xref:data/ef-rp/intro) , aby uzyskać informacje na temat ograniczania liczby lub jednostek zwróconych z zapytania.</span><span class="sxs-lookup"><span data-stu-id="801fe-352">See [Performance considerations](xref:data/ef-rp/intro) for information on limiting the number or entities returned from a query.</span></span>

<span data-ttu-id="801fe-353">Przejdź do następnego samouczka, aby dowiedzieć się, jak wykonywać podstawowe operacje CRUD (tworzenie, odczytywanie, aktualizowanie i usuwanie).</span><span class="sxs-lookup"><span data-stu-id="801fe-353">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="801fe-354">Zaimplementuj podstawowe funkcje CRUD</span><span class="sxs-lookup"><span data-stu-id="801fe-354">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="801fe-355">Przykładowa aplikacja internetowa Contoso University demonstruje sposób tworzenia aplikacji sieci Web ASP.NET Core 2,2 MVC przy użyciu Entity Framework (EF) Core 2,2 i Visual Studio 2017 lub 2019.</span><span class="sxs-lookup"><span data-stu-id="801fe-355">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.2 MVC web applications using Entity Framework (EF) Core 2.2 and Visual Studio 2017 or 2019.</span></span>

<span data-ttu-id="801fe-356">Przykładowa aplikacja jest witryną internetową fikcyjnej firmy Contoso University.</span><span class="sxs-lookup"><span data-stu-id="801fe-356">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="801fe-357">Obejmuje to funkcje, takie jak przyjmowanie studentów, tworzenie kursu i przydziały instruktora.</span><span class="sxs-lookup"><span data-stu-id="801fe-357">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="801fe-358">Jest to pierwsza z szeregu samouczków, które wyjaśniają, jak skompilować przykładową aplikację firmy Contoso University od podstaw.</span><span class="sxs-lookup"><span data-stu-id="801fe-358">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="801fe-359">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="801fe-359">Prerequisites</span></span>

* [<span data-ttu-id="801fe-360">Zestaw .NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="801fe-360">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download)
* <span data-ttu-id="801fe-361">[Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z następującymi obciążeniami:</span><span class="sxs-lookup"><span data-stu-id="801fe-361">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the following workloads:</span></span>
  * <span data-ttu-id="801fe-362">**ASP.NET i programowanie aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="801fe-362">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="801fe-363">**Tworzenie aplikacji dla wielu platform w środowisku .NET Core**</span><span class="sxs-lookup"><span data-stu-id="801fe-363">**.NET Core cross-platform development** workload</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="801fe-364">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="801fe-364">Troubleshooting</span></span>

<span data-ttu-id="801fe-365">Jeśli wystąpi problem, którego nie można rozwiązać, można ogólnie znaleźć rozwiązanie, porównując kod z [ukończonym projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="801fe-365">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples).</span></span> <span data-ttu-id="801fe-366">Aby zapoznać się z listą typowych błędów i sposobu ich rozwiązywania, zobacz [sekcję Rozwiązywanie problemów w ostatnim samouczku w serii](advanced.md#common-errors).</span><span class="sxs-lookup"><span data-stu-id="801fe-366">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="801fe-367">Jeśli nie możesz znaleźć tego, czego potrzebujesz, możesz ogłosić pytanie do StackOverflow.com dla [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) lub [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="801fe-367">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="801fe-368">Jest to seria 10 samouczków, z których każdy jest oparty na tym, co zostało zrobione we wcześniejszych samouczkach.</span><span class="sxs-lookup"><span data-stu-id="801fe-368">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="801fe-369">Rozważ zapisanie kopii projektu po każdym pomyślnym zakończeniu samouczka.</span><span class="sxs-lookup"><span data-stu-id="801fe-369">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="801fe-370">Jeśli wystąpią problemy, możesz zacząć od poprzedniego samouczka zamiast wrócić do początku całej serii.</span><span class="sxs-lookup"><span data-stu-id="801fe-370">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="801fe-371">Aplikacja sieci Web firmy Contoso University</span><span class="sxs-lookup"><span data-stu-id="801fe-371">Contoso University web app</span></span>

<span data-ttu-id="801fe-372">Aplikacja, którą tworzysz w tych samouczkach, jest prostą witryną sieci Web.</span><span class="sxs-lookup"><span data-stu-id="801fe-372">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="801fe-373">Użytkownicy mogą wyświetlać i aktualizować informacje dotyczące uczniów, kursów i instruktorów.</span><span class="sxs-lookup"><span data-stu-id="801fe-373">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="801fe-374">Poniżej przedstawiono kilka ekranów, które zostaną utworzone.</span><span class="sxs-lookup"><span data-stu-id="801fe-374">Here are a few of the screens you'll create.</span></span>

![Strona indeksu uczniów](intro/_static/students-index.png)

![Strona edycji uczniów](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="801fe-377">Tworzenie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="801fe-377">Create web app</span></span>

* <span data-ttu-id="801fe-378">Otwórz program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="801fe-378">Open Visual Studio.</span></span>

* <span data-ttu-id="801fe-379">Z menu **plik** wybierz pozycję **Nowy projekt >**.</span><span class="sxs-lookup"><span data-stu-id="801fe-379">From the **File** menu, select **New > Project**.</span></span>

* <span data-ttu-id="801fe-380">W lewym okienku wybierz pozycję **zainstalowane > Visual C# > sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="801fe-380">From the left pane, select **Installed > Visual C# > Web**.</span></span>

* <span data-ttu-id="801fe-381">Wybierz szablon projektu **aplikacji sieci Web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="801fe-381">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="801fe-382">Wprowadź **ContosoUniversity** jako nazwę, a następnie kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="801fe-382">Enter **ContosoUniversity** as the name and click **OK**.</span></span>

  ![Okno dialogowe Nowy projekt](intro/_static/new-project2.png)

* <span data-ttu-id="801fe-384">Zaczekaj, aż pojawi się okno dialogowe **Nowa aplikacja sieci Web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="801fe-384">Wait for the **New ASP.NET Core Web Application** dialog to appear.</span></span>

* <span data-ttu-id="801fe-385">Wybierz pozycję **.NET Core** , **ASP.NET Core 2,2** i szablon **aplikacji sieci Web (Model-View-Controller)** .</span><span class="sxs-lookup"><span data-stu-id="801fe-385">Select **.NET Core** , **ASP.NET Core 2.2** and the **Web Application (Model-View-Controller)** template.</span></span>

* <span data-ttu-id="801fe-386">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="801fe-386">Make sure **Authentication** is set to **No Authentication**.</span></span>

* <span data-ttu-id="801fe-387">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="801fe-387">Select **OK**</span></span>

  ![Nowe okno dialogowe projektu ASP.NET Core](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a><span data-ttu-id="801fe-389">Konfigurowanie stylu witryny</span><span class="sxs-lookup"><span data-stu-id="801fe-389">Set up the site style</span></span>

<span data-ttu-id="801fe-390">Kilka prostych zmian spowoduje skonfigurowanie menu witryny, układu i strony głównej.</span><span class="sxs-lookup"><span data-stu-id="801fe-390">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="801fe-391">Otwórz *Widok widoki/Shared/_Layout. cshtml* i wprowadź następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="801fe-391">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="801fe-392">Zmień każde wystąpienie "ContosoUniversity" na "Uniwersytet firmy Contoso".</span><span class="sxs-lookup"><span data-stu-id="801fe-392">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="801fe-393">Istnieją trzy wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="801fe-393">There are three occurrences.</span></span>

* <span data-ttu-id="801fe-394">Dodaj pozycje menu dla **informacji o** programie, **studentów** , **kursy** , **Instruktorzy** i **działy** , a następnie usuń wpis menu **prywatność** .</span><span class="sxs-lookup"><span data-stu-id="801fe-394">Add menu entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="801fe-395">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="801fe-395">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

<span data-ttu-id="801fe-396">W obszarze *widoki/Home/index. cshtml* Zastąp zawartość pliku następującym kodem, aby zamienić tekst na ASP.NET i MVC z tekstem dotyczącym tej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="801fe-396">In *Views/Home/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="801fe-397">Naciśnij klawisze CTRL + F5, aby uruchomić projekt, lub wybierz polecenie **debuguj > Uruchom bez debugowania** z menu.</span><span class="sxs-lookup"><span data-stu-id="801fe-397">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="801fe-398">Zostanie wyświetlona strona główna z kartami dla stron, które zostaną utworzone w tych samouczkach.</span><span class="sxs-lookup"><span data-stu-id="801fe-398">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Strona główna firmy Contoso University](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a><span data-ttu-id="801fe-400">Informacje o pakietach NuGet EF Core</span><span class="sxs-lookup"><span data-stu-id="801fe-400">About EF Core NuGet packages</span></span>

<span data-ttu-id="801fe-401">Aby dodać obsługę EF Core do projektu, zainstaluj dostawcę bazy danych, który ma być celem.</span><span class="sxs-lookup"><span data-stu-id="801fe-401">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="801fe-402">Ten samouczek używa SQL Server, a pakiet dostawcy to [Microsoft. EntityFrameworkCore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="801fe-402">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="801fe-403">Ten pakiet jest zawarty w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), dlatego nie musisz się odwoływać do pakietu.</span><span class="sxs-lookup"><span data-stu-id="801fe-403">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to reference the package.</span></span>

<span data-ttu-id="801fe-404">Pakiet EF SQL Server i jego zależności ( `Microsoft.EntityFrameworkCore` oraz `Microsoft.EntityFrameworkCore.Relational` ) zapewniają obsługę środowiska uruchomieniowego dla EF.</span><span class="sxs-lookup"><span data-stu-id="801fe-404">The EF SQL Server package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="801fe-405">Pakiet narzędzi zostanie dodany później, w samouczku [migracji](migrations.md) .</span><span class="sxs-lookup"><span data-stu-id="801fe-405">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="801fe-406">Aby uzyskać informacje o innych dostawcach baz danych, które są dostępne dla Entity Framework Core, zobacz [dostawcy bazy danych](/ef/core/providers/).</span><span class="sxs-lookup"><span data-stu-id="801fe-406">For information about other database providers that are available for Entity Framework Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="801fe-407">Tworzenie modelu danych</span><span class="sxs-lookup"><span data-stu-id="801fe-407">Create the data model</span></span>

<span data-ttu-id="801fe-408">Następnie utworzysz klasy jednostek dla aplikacji firmy Contoso University.</span><span class="sxs-lookup"><span data-stu-id="801fe-408">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="801fe-409">Zaczniesz od następujących trzech jednostek.</span><span class="sxs-lookup"><span data-stu-id="801fe-409">You'll start with the following three entities.</span></span>

![Kurs — Diagram modelu danych ucznia](intro/_static/data-model-diagram.png)

<span data-ttu-id="801fe-411">Istnieje relacja jeden do wielu między `Student` jednostkami i i `Enrollment` istnieje relacja jeden do wielu między elementami `Course` i `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="801fe-411">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="801fe-412">Innymi słowy, student może być zarejestrowany w dowolnej liczbie kursów, a kurs może mieć dowolną liczbę uczniów zarejestrowanych w nim.</span><span class="sxs-lookup"><span data-stu-id="801fe-412">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="801fe-413">W poniższych sekcjach utworzysz klasę dla każdej z tych jednostek.</span><span class="sxs-lookup"><span data-stu-id="801fe-413">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="801fe-414">Jednostka ucznia</span><span class="sxs-lookup"><span data-stu-id="801fe-414">The Student entity</span></span>

![Diagram jednostek uczniów](intro/_static/student-entity.png)

<span data-ttu-id="801fe-416">W folderze *modele* Utwórz plik klasy o nazwie *student.cs* i Zastąp kod szablonu poniższym kodem.</span><span class="sxs-lookup"><span data-stu-id="801fe-416">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="801fe-417">`ID`Właściwość stanie się kolumną klucza podstawowego tabeli bazy danych, która odnosi się do tej klasy.</span><span class="sxs-lookup"><span data-stu-id="801fe-417">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="801fe-418">Domyślnie platforma Entity Framework interpretuje właściwość o nazwie `ID` lub `classnameID` jako klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="801fe-418">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="801fe-419">`Enrollments`Właściwość jest [właściwością nawigacji](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="801fe-419">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="801fe-420">Właściwości nawigacji zawierają inne jednostki, które są powiązane z tą jednostką.</span><span class="sxs-lookup"><span data-stu-id="801fe-420">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="801fe-421">W tym przypadku `Enrollments` Właściwość a `Student entity` będzie zawierać wszystkie `Enrollment` jednostki, które są powiązane z tą `Student` jednostką.</span><span class="sxs-lookup"><span data-stu-id="801fe-421">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="801fe-422">Innymi słowy, jeśli `Student` wiersz w bazie danych ma dwa powiązane `Enrollment` wiersze (wiersze zawierające wartość klucza podstawowego tego ucznia w kolumnie klucza obcego StudentID), `Student` `Enrollments` Właściwość nawigacji tej jednostki będzie zawierać te dwie `Enrollment` jednostki.</span><span class="sxs-lookup"><span data-stu-id="801fe-422">In other words, if a `Student` row in the database has two related `Enrollment` rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="801fe-423">Jeśli właściwość nawigacji może zawierać wiele jednostek (tak jak w przypadku relacji "wiele do wielu" lub "jeden do wielu"), jej typem musi być lista, w której można dodawać, usuwać i aktualizować wpisy, na przykład `ICollection<T>` .</span><span class="sxs-lookup"><span data-stu-id="801fe-423">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="801fe-424">Możesz określić `ICollection<T>` lub typ, taki jak `List<T>` lub `HashSet<T>` .</span><span class="sxs-lookup"><span data-stu-id="801fe-424">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="801fe-425">Jeśli określisz `ICollection<T>` , EF domyślnie tworzy `HashSet<T>` kolekcję.</span><span class="sxs-lookup"><span data-stu-id="801fe-425">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="801fe-426">Jednostka rejestracji</span><span class="sxs-lookup"><span data-stu-id="801fe-426">The Enrollment entity</span></span>

![Diagram jednostek rejestracji](intro/_static/enrollment-entity.png)

<span data-ttu-id="801fe-428">W folderze *modele* Utwórz *Enrollment.cs* i Zastąp istniejący kod następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="801fe-428">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="801fe-429">`EnrollmentID`Właściwość będzie kluczem podstawowym; ta jednostka używa `classnameID` wzorca zamiast `ID` samego siebie, jak pokazano w `Student` jednostce.</span><span class="sxs-lookup"><span data-stu-id="801fe-429">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="801fe-430">Zwykle należy wybrać jeden wzorzec i używać go w całym modelu danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-430">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="801fe-431">Tutaj, odmiana ilustruje, że można użyć dowolnego wzorca.</span><span class="sxs-lookup"><span data-stu-id="801fe-431">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="801fe-432">W [późniejszym samouczku](inheritance.md)zobaczysz, jak używać identyfikatora bez ClassName, ułatwia implementowanie dziedziczenia w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-432">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="801fe-433">`Grade`Właściwość jest `enum` .</span><span class="sxs-lookup"><span data-stu-id="801fe-433">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="801fe-434">Znak zapytania po `Grade` deklaracji typu wskazuje, że `Grade` Właściwość dopuszcza wartość null.</span><span class="sxs-lookup"><span data-stu-id="801fe-434">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="801fe-435">Klasa o wartości null różni się od klasy zerowej — wartość null oznacza, że Klasa nie jest znana lub nie została jeszcze przypisana.</span><span class="sxs-lookup"><span data-stu-id="801fe-435">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="801fe-436">`StudentID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Student` .</span><span class="sxs-lookup"><span data-stu-id="801fe-436">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="801fe-437">`Enrollment`Jednostka jest skojarzona z jedną `Student` jednostką, więc właściwość może zawierać tylko jedną `Student` jednostkę (w przeciwieństwie do `Student.Enrollments` wytoczonej wcześniej właściwości nawigacji, która może zawierać wiele `Enrollment` jednostek).</span><span class="sxs-lookup"><span data-stu-id="801fe-437">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="801fe-438">`CourseID`Właściwość jest kluczem obcym, a odpowiednia właściwość nawigacji to `Course` .</span><span class="sxs-lookup"><span data-stu-id="801fe-438">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="801fe-439">`Enrollment`Jednostka jest skojarzona z jedną `Course` jednostką.</span><span class="sxs-lookup"><span data-stu-id="801fe-439">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="801fe-440">Entity Framework interpretuje właściwość jako właściwość klucza obcego, jeśli jest nazwana `<navigation property name><primary key property name>` (na przykład `StudentID` dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` ).</span><span class="sxs-lookup"><span data-stu-id="801fe-440">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="801fe-441">Właściwości klucza obcego mogą być również nazywane po prostu `<primary key property name>` (na przykład, `CourseID` ponieważ `Course` klucz podstawowy jednostki to `CourseID` ).</span><span class="sxs-lookup"><span data-stu-id="801fe-441">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="801fe-442">Jednostka kursu</span><span class="sxs-lookup"><span data-stu-id="801fe-442">The Course entity</span></span>

![Diagram jednostek kursu](intro/_static/course-entity.png)

<span data-ttu-id="801fe-444">W folderze *modele* Utwórz *Course.cs* i Zastąp istniejący kod następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="801fe-444">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="801fe-445">`Enrollments`Właściwość jest właściwością nawigacji.</span><span class="sxs-lookup"><span data-stu-id="801fe-445">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="801fe-446">`Course`Jednostka może być powiązana z dowolną liczbą `Enrollment` jednostek.</span><span class="sxs-lookup"><span data-stu-id="801fe-446">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="801fe-447">Dowiesz się więcej na temat `DatabaseGenerated` atrybutu w [kolejnym samouczku](complex-data-model.md) w tej serii.</span><span class="sxs-lookup"><span data-stu-id="801fe-447">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="801fe-448">Zasadniczo ten atrybut umożliwia wprowadzenie klucza podstawowego dla kursu, a nie jego wygenerowanie.</span><span class="sxs-lookup"><span data-stu-id="801fe-448">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="801fe-449">Tworzenie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="801fe-449">Create the database context</span></span>

<span data-ttu-id="801fe-450">Klasa główna, która koordynuje funkcje Entity Framework dla danego modelu danych, jest klasą kontekstu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-450">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="801fe-451">Tę klasę można utworzyć, wyprowadzając ją z `Microsoft.EntityFrameworkCore.DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="801fe-451">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="801fe-452">W kodzie możesz określić, które jednostki zostaną uwzględnione w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-452">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="801fe-453">Można również dostosować pewne zachowanie Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="801fe-453">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="801fe-454">W tym projekcie Klasa ma nazwę `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="801fe-454">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="801fe-455">W folderze projektu Utwórz folder o nazwie *dane*.</span><span class="sxs-lookup"><span data-stu-id="801fe-455">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="801fe-456">W folderze *dane* Utwórz nowy plik klasy o nazwie *SchoolContext.cs* i Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="801fe-456">In the *Data* folder create a new class file named *SchoolContext.cs* , and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="801fe-457">Ten kod tworzy `DbSet` Właściwość dla każdego zestawu jednostek.</span><span class="sxs-lookup"><span data-stu-id="801fe-457">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="801fe-458">W Entity Framework terminologii zestaw jednostek zwykle odpowiada tabeli bazy danych, a jednostka odpowiada wierszowi w tabeli.</span><span class="sxs-lookup"><span data-stu-id="801fe-458">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="801fe-459">Można pominąć `DbSet<Enrollment>` `DbSet<Course>` instrukcje i i będzie on działał tak samo.</span><span class="sxs-lookup"><span data-stu-id="801fe-459">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="801fe-460">Entity Framework będzie zawierać je niejawnie, ponieważ `Student` Jednostka odwołuje się do `Enrollment` jednostki, a `Enrollment` Jednostka odwołuje się do `Course` jednostki.</span><span class="sxs-lookup"><span data-stu-id="801fe-460">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="801fe-461">Po utworzeniu bazy danych EF tworzy tabele, które mają nazwy takie same jak `DbSet` nazwy właściwości.</span><span class="sxs-lookup"><span data-stu-id="801fe-461">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="801fe-462">Nazwy właściwości dla kolekcji są zwykle plural (studenci zamiast uczniów), ale deweloperzy zgadzają się na to, czy nazwy tabel powinny być wyrzucane.</span><span class="sxs-lookup"><span data-stu-id="801fe-462">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="801fe-463">Te samouczki zastąpią domyślne zachowanie, określając pojedyncze nazwy tabel w kontekście DbContext.</span><span class="sxs-lookup"><span data-stu-id="801fe-463">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="801fe-464">Aby to zrobić, Dodaj następujący wyróżniony kod po ostatniej właściwości Nieogólnymi.</span><span class="sxs-lookup"><span data-stu-id="801fe-464">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

<span data-ttu-id="801fe-465">Kompiluj projekt jako sprawdzenie błędów kompilatora.</span><span class="sxs-lookup"><span data-stu-id="801fe-465">Build the project as a check for compiler errors.</span></span>

## <a name="register-the-schoolcontext"></a><span data-ttu-id="801fe-466">Zarejestruj SchoolContext</span><span class="sxs-lookup"><span data-stu-id="801fe-466">Register the SchoolContext</span></span>

<span data-ttu-id="801fe-467">ASP.NET Core domyślnie implementuje [iniekcję zależności](../../fundamentals/dependency-injection.md) .</span><span class="sxs-lookup"><span data-stu-id="801fe-467">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="801fe-468">Usługi (takie jak kontekst bazy danych EF) są rejestrowane przy użyciu iniekcji zależności podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="801fe-468">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="801fe-469">Składniki, które wymagają tych usług (takich jak kontrolery MVC), są dostarczane przez parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="801fe-469">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="801fe-470">Zobaczysz kod konstruktora kontrolera, który pobiera wystąpienie kontekstu w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="801fe-470">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="801fe-471">Aby zarejestrować się `SchoolContext` jako usługa, otwórz *Startup.cs* i Dodaj wyróżnione wiersze do `ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="801fe-471">To register `SchoolContext` as a service, open *Startup.cs* , and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

<span data-ttu-id="801fe-472">Nazwa parametrów połączenia jest przenoszona do kontekstu przez wywołanie metody w `DbContextOptionsBuilder` obiekcie.</span><span class="sxs-lookup"><span data-stu-id="801fe-472">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="801fe-473">W przypadku lokalnego projektowania [system konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) odczytuje parametry połączenia z *:::no-loc(appsettings.json):::* pliku.</span><span class="sxs-lookup"><span data-stu-id="801fe-473">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

<span data-ttu-id="801fe-474">Dodaj `using` instrukcje dla `ContosoUniversity.Data` i `Microsoft.EntityFrameworkCore` przestrzeni nazw, a następnie Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="801fe-474">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="801fe-475">Otwórz *:::no-loc(appsettings.json):::* plik i Dodaj parametry połączenia, jak pokazano w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="801fe-475">Open the *:::no-loc(appsettings.json):::* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="801fe-476">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="801fe-476">SQL Server Express LocalDB</span></span>

<span data-ttu-id="801fe-477">Parametry połączenia określają SQL Server bazy danych LocalDB.</span><span class="sxs-lookup"><span data-stu-id="801fe-477">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="801fe-478">LocalDB to uproszczona wersja aparatu bazy danych SQL Server Express i jest przeznaczona do tworzenia aplikacji, a nie do użycia w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="801fe-478">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="801fe-479">LocalDB uruchamia się na żądanie i działa w trybie użytkownika, więc nie istnieje złożona konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="801fe-479">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="801fe-480">Domyślnie LocalDB tworzy pliki bazy danych *MDF* w `C:/Users/<user>` katalogu.</span><span class="sxs-lookup"><span data-stu-id="801fe-480">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="801fe-481">Zainicjuj bazę danych z danymi testowymi</span><span class="sxs-lookup"><span data-stu-id="801fe-481">Initialize DB with test data</span></span>

<span data-ttu-id="801fe-482">Entity Framework utworzy pustą bazę danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-482">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="801fe-483">W tej sekcji napiszesz metodę, która jest wywoływana po utworzeniu bazy danych w celu wypełnienia jej danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="801fe-483">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="801fe-484">W tym miejscu będziesz używać `EnsureCreated` metody do automatycznego tworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-484">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="801fe-485">W [późniejszym samouczku](migrations.md) zobaczysz, jak obsłużyć zmiany modelu przy użyciu migracje Code First, aby zmienić schemat bazy danych zamiast upuszczania i ponownego tworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-485">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="801fe-486">W folderze *dane* Utwórz nowy plik klasy o nazwie *DbInitializer.cs* i Zastąp kod szablonu następującym kodem, co spowoduje utworzenie bazy danych w razie potrzeby i załadowanie danych testowych do nowej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-486">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="801fe-487">Kod sprawdza, czy w bazie danych znajdują się uczniowie i czy nie, zakłada, że baza danych jest nowa i należy ją umieścić w danych testowych.</span><span class="sxs-lookup"><span data-stu-id="801fe-487">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="801fe-488">Ładuje dane testowe do tablic, a nie `List<T>` kolekcji w celu zoptymalizowania wydajności.</span><span class="sxs-lookup"><span data-stu-id="801fe-488">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="801fe-489">W *program.cs* Zmień metodę, `Main` Aby wykonać następujące czynności podczas uruchamiania aplikacji:</span><span class="sxs-lookup"><span data-stu-id="801fe-489">In *Program.cs* , modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="801fe-490">Pobierz wystąpienie kontekstu bazy danych z kontenera iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="801fe-490">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="801fe-491">Wywołaj metodę inicjatora, przekazując ją do kontekstu.</span><span class="sxs-lookup"><span data-stu-id="801fe-491">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="801fe-492">Usuń kontekst, gdy metoda inicjatora jest gotowa.</span><span class="sxs-lookup"><span data-stu-id="801fe-492">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="801fe-493">Przy pierwszym uruchomieniu aplikacji baza danych zostanie utworzona i roztworzona przy użyciu danych testowych.</span><span class="sxs-lookup"><span data-stu-id="801fe-493">The first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="801fe-494">Za każdym razem, gdy zmienisz model danych:</span><span class="sxs-lookup"><span data-stu-id="801fe-494">Whenever you change the data model:</span></span>

 * <span data-ttu-id="801fe-495">Usuń bazę danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-495">Delete the database.</span></span>
 * <span data-ttu-id="801fe-496">Zaktualizuj metodę inicjatora i zacznij od nowa bazę danych w ten sam sposób.</span><span class="sxs-lookup"><span data-stu-id="801fe-496">Update the seed method, and start afresh with a new database the same way.</span></span>
 
<span data-ttu-id="801fe-497">W kolejnych samouczkach zobaczysz, jak zmodyfikować bazę danych, gdy zmieni się model danych, bez usuwania i ponownego tworzenia.</span><span class="sxs-lookup"><span data-stu-id="801fe-497">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="801fe-498">Tworzenie kontrolera i widoków</span><span class="sxs-lookup"><span data-stu-id="801fe-498">Create controller and views</span></span>

<span data-ttu-id="801fe-499">W tej sekcji aparat tworzenia szkieletów w programie Visual Studio służy do dodawania kontrolera MVC i widoków, które będą używać EF do wykonywania zapytań i zapisywania danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-499">In this section, the scaffolding engine in Visual Studio is used to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="801fe-500">Automatyczne tworzenie metod i widoków akcji CRUD jest znane jako rusztowania.</span><span class="sxs-lookup"><span data-stu-id="801fe-500">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="801fe-501">Tworzenie szkieletu różni się od generowania kodu, ponieważ kod szkieletowy jest punktem wyjścia, który można zmodyfikować zgodnie z własnymi wymaganiami, ale zazwyczaj nie jest modyfikowany kod wygenerowany.</span><span class="sxs-lookup"><span data-stu-id="801fe-501">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="801fe-502">Jeśli musisz dostosować wygenerowany kod, użyj klas częściowych lub ponownie Wygeneruj kod, gdy zmienią się.</span><span class="sxs-lookup"><span data-stu-id="801fe-502">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="801fe-503">Kliknij prawym przyciskiem myszy folder **controllers** w **Eksplorator rozwiązań** a następnie wybierz pozycję **Dodaj > nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="801fe-503">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item**.</span></span>
* <span data-ttu-id="801fe-504">W oknie dialogowym **Dodawanie szkieletu** :</span><span class="sxs-lookup"><span data-stu-id="801fe-504">In the **Add Scaffold** dialog box:</span></span>
  * <span data-ttu-id="801fe-505">Wybierz **kontroler MVC z widokami, używając Entity Framework**.</span><span class="sxs-lookup"><span data-stu-id="801fe-505">Select **MVC controller with views, using Entity Framework**.</span></span>
  * <span data-ttu-id="801fe-506">Kliknij pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="801fe-506">Click **Add**.</span></span> <span data-ttu-id="801fe-507">Pojawia się okno dialogowe **Dodawanie kontrolera MVC z widokami z użyciem Entity Framework** : ![ studenta dla szkieletu](intro/_static/scaffold-student2.png)</span><span class="sxs-lookup"><span data-stu-id="801fe-507">The **Add MVC Controller with views, using Entity Framework** dialog box appears: ![Scaffold Student](intro/_static/scaffold-student2.png)</span></span>
  * <span data-ttu-id="801fe-508">W obszarze **Klasa modelu** wybierz **ucznia**.</span><span class="sxs-lookup"><span data-stu-id="801fe-508">In **Model class** select **Student**.</span></span>
  * <span data-ttu-id="801fe-509">W obszarze **Klasa kontekstu danych** wybierz pozycję **SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="801fe-509">In **Data context class** select **SchoolContext**.</span></span>
  * <span data-ttu-id="801fe-510">Zaakceptuj domyślną **StudentsController** jako nazwę.</span><span class="sxs-lookup"><span data-stu-id="801fe-510">Accept the default **StudentsController** as the name.</span></span>
  * <span data-ttu-id="801fe-511">Kliknij pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="801fe-511">Click **Add**.</span></span>

<span data-ttu-id="801fe-512">Aparat szkieletu programu Visual Studio tworzy plik *StudentsController.cs* i zestaw widoków (pliki *. cshtml* ), które współpracują z kontrolerem.</span><span class="sxs-lookup"><span data-stu-id="801fe-512">The Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views ( *.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="801fe-513">Zwróć uwagę, że kontroler przyjmuje `SchoolContext` jako parametr konstruktora.</span><span class="sxs-lookup"><span data-stu-id="801fe-513">Notice the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="801fe-514">ASP.NET Core iniekcja zależności ma zadbać o przekazanie wystąpienia `SchoolContext` do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="801fe-514">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="801fe-515">Który został skonfigurowany w pliku *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="801fe-515">That was configured  in the *Startup.cs* file.</span></span>

<span data-ttu-id="801fe-516">Kontroler zawiera `Index` metodę akcji, która wyświetla wszystkich uczniów w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-516">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="801fe-517">Metoda pobiera listę studentów z zestawu jednostek studentów, odczytując `Students` Właściwość wystąpienia kontekstu bazy danych:</span><span class="sxs-lookup"><span data-stu-id="801fe-517">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="801fe-518">Dowiesz się więcej na temat asynchronicznych elementów programowania w tym kodzie w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="801fe-518">You learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="801fe-519">Widok *widoki/uczniowie/index. cshtml* wyświetla tę listę w tabeli:</span><span class="sxs-lookup"><span data-stu-id="801fe-519">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="801fe-520">Naciśnij klawisze CTRL + F5, aby uruchomić projekt, lub wybierz polecenie **debuguj > Uruchom bez debugowania** z menu.</span><span class="sxs-lookup"><span data-stu-id="801fe-520">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="801fe-521">Kliknij kartę studenci, aby zobaczyć dane testowe, które zostały `DbInitializer.Initialize` wstawione przez metodę.</span><span class="sxs-lookup"><span data-stu-id="801fe-521">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="801fe-522">W zależności od tego, jak Zawężasz okno przeglądarki, zobaczysz `Students` link do karty w górnej części strony lub kliknij ikonę nawigacji w prawym górnym rogu, aby zobaczyć link.</span><span class="sxs-lookup"><span data-stu-id="801fe-522">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Strona główna firmy Contoso University Narrow](intro/_static/home-page-narrow.png)

![Strona indeksu uczniów](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="801fe-525">Wyświetlanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="801fe-525">View the database</span></span>

<span data-ttu-id="801fe-526">Po uruchomieniu aplikacji `DbInitializer.Initialize` Metoda wywołuje metodę `EnsureCreated` .</span><span class="sxs-lookup"><span data-stu-id="801fe-526">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="801fe-527">EF wykryto, że nie istniała baza danych i dlatego została utworzona, a następnie pozostała część `Initialize` kodu metody wypełnił bazę danych danymi.</span><span class="sxs-lookup"><span data-stu-id="801fe-527">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="801fe-528">Aby wyświetlić bazę danych w programie Visual Studio, można użyć **Eksplorator obiektów SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="801fe-528">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="801fe-529">Zamknij okno przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="801fe-529">Close the browser.</span></span>

<span data-ttu-id="801fe-530">Jeśli okno SSOX nie jest jeszcze otwarte, wybierz je z menu **Widok** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="801fe-530">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="801fe-531">W SSOX kliknij pozycję **(LocalDB) \MSSQLLocalDB > bazy danych** , a następnie kliknij pozycję Nazwa bazy danych, która znajduje się w parametrach połączenia w *:::no-loc(appsettings.json):::* pliku.</span><span class="sxs-lookup"><span data-stu-id="801fe-531">In SSOX, click **(localdb)\MSSQLLocalDB > Databases** , and then click the entry for the database name that's in the connection string in the *:::no-loc(appsettings.json):::* file.</span></span>

<span data-ttu-id="801fe-532">Rozwiń węzeł **tabele** , aby wyświetlić tabele w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-532">Expand the **Tables** node to see the tables in the database.</span></span>

![Tabele w SSOX](intro/_static/ssox-tables.png)

<span data-ttu-id="801fe-534">Kliknij prawym przyciskiem myszy tabelę **uczniów** i kliknij polecenie **Wyświetl dane** , aby wyświetlić utworzone kolumny i wiersze, które zostały wstawione do tabeli.</span><span class="sxs-lookup"><span data-stu-id="801fe-534">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![Tabela uczniów w SSOX](intro/_static/ssox-student-table.png)

<span data-ttu-id="801fe-536">Pliki *. mdf* i *. ldf* znajdują się w folderze *C:\Users \\ \<username>* .</span><span class="sxs-lookup"><span data-stu-id="801fe-536">The *.mdf* and *.ldf* database files are in the *C:\Users\\\<username>* folder.</span></span>

<span data-ttu-id="801fe-537">Ponieważ wywołujesz `EnsureCreated` metodę inicjatora, która jest uruchamiana podczas uruchamiania aplikacji, możesz teraz wprowadzić zmianę `Student` klasy, usunąć bazę danych, ponownie uruchomić aplikację, a baza danych zostanie automatycznie utworzona ponownie w celu dopasowania do zmiany.</span><span class="sxs-lookup"><span data-stu-id="801fe-537">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="801fe-538">Na przykład, jeśli dodasz `EmailAddress` Właściwość do `Student` klasy, zobaczysz nową `EmailAddress` kolumnę w nowo utworzonej tabeli.</span><span class="sxs-lookup"><span data-stu-id="801fe-538">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="801fe-539">Konwencje</span><span class="sxs-lookup"><span data-stu-id="801fe-539">Conventions</span></span>

<span data-ttu-id="801fe-540">Ilość kodu, który miał zostać zapisany w celu Entity Framework być w stanie utworzyć kompletną bazę danych, jest minimalny ze względu na stosowanie Konwencji lub zaEntity Framework łożeń.</span><span class="sxs-lookup"><span data-stu-id="801fe-540">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="801fe-541">Nazwy `DbSet` właściwości są używane jako nazwy tabel.</span><span class="sxs-lookup"><span data-stu-id="801fe-541">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="801fe-542">W przypadku jednostek, do których nie odwołuje się `DbSet` Właściwość, nazwy klas jednostek są używane jako nazwy tabel.</span><span class="sxs-lookup"><span data-stu-id="801fe-542">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>
* <span data-ttu-id="801fe-543">Nazwy właściwości jednostki są używane w nazwach kolumn.</span><span class="sxs-lookup"><span data-stu-id="801fe-543">Entity property names are used for column names.</span></span>
* <span data-ttu-id="801fe-544">Właściwości jednostki o nazwach ID lub classnameID są rozpoznawane jako właściwości klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="801fe-544">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>
* <span data-ttu-id="801fe-545">Właściwość jest interpretowana jako właściwość klucza obcego, jeśli jest nazwana *\<navigation property name>\<primary key property name>* (na przykład `StudentID` dla `Student` właściwości nawigacji, ponieważ `Student` klucz podstawowy jednostki to `ID` ).</span><span class="sxs-lookup"><span data-stu-id="801fe-545">A property is interpreted as a foreign key property if it's named *\<navigation property name>\<primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="801fe-546">Właściwości klucza obcego mogą być również nazywane po prostu *\<primary key property name>* (na przykład, `EnrollmentID` ponieważ `Enrollment` klucz podstawowy jednostki to `EnrollmentID` ).</span><span class="sxs-lookup"><span data-stu-id="801fe-546">Foreign key properties can also be named simply *\<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="801fe-547">Zachowanie konwencjonalne można zastąpić.</span><span class="sxs-lookup"><span data-stu-id="801fe-547">Conventional behavior can be overridden.</span></span> <span data-ttu-id="801fe-548">Na przykład można jawnie określić nazwy tabel, jak zostało to opisane wcześniej w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="801fe-548">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="801fe-549">I można ustawić nazwy kolumn i ustawić dowolną właściwość jako klucz podstawowy lub klucz obcy, jak widać w [późniejszym samouczku](complex-data-model.md) w tej serii.</span><span class="sxs-lookup"><span data-stu-id="801fe-549">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="801fe-550">Kod asynchroniczny</span><span class="sxs-lookup"><span data-stu-id="801fe-550">Asynchronous code</span></span>

<span data-ttu-id="801fe-551">Programowanie asynchroniczne jest trybem domyślnym dla ASP.NET Core i EF Core.</span><span class="sxs-lookup"><span data-stu-id="801fe-551">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="801fe-552">Serwer sieci Web ma ograniczoną liczbę dostępnych wątków, a w przypadku dużego obciążenia mogą być używane wszystkie dostępne wątki.</span><span class="sxs-lookup"><span data-stu-id="801fe-552">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="801fe-553">W takim przypadku serwer nie może przetwarzać nowych żądań, dopóki wątki nie zostaną zwolnione.</span><span class="sxs-lookup"><span data-stu-id="801fe-553">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="801fe-554">W przypadku kodu synchronicznego wiele wątków może zostać powiązanych, podczas gdy nie wykonuje żadnej pracy, ponieważ oczekuje na ukończenie operacji we/wy.</span><span class="sxs-lookup"><span data-stu-id="801fe-554">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="801fe-555">W przypadku kodu asynchronicznego, gdy proces oczekuje na ukończenie operacji we/wy, jego wątek zostanie zwolniony dla serwera do użycia na potrzeby przetwarzania innych żądań.</span><span class="sxs-lookup"><span data-stu-id="801fe-555">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="801fe-556">W efekcie kod asynchroniczny umożliwia efektywniejsze wykorzystanie zasobów serwera, a serwer jest włączony do obsługi większej ilości ruchu bez opóźnień.</span><span class="sxs-lookup"><span data-stu-id="801fe-556">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="801fe-557">Kod asynchroniczny wprowadza niewielką ilość narzutu w czasie wykonywania, ale w przypadku niskiego natężenia ruchu, gdy wydajność jest niewielka, w przypadku dużych sytuacji związanych z ruchem jest istotna poprawa wydajności.</span><span class="sxs-lookup"><span data-stu-id="801fe-557">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="801fe-558">W poniższym kodzie `async` słowo kluczowe, `Task<T>` wartość zwracana, `await` słowo kluczowe i `ToListAsync` Metoda sprawiają, że kod jest wykonywany asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="801fe-558">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="801fe-559">`async`Słowo kluczowe instruuje kompilator, aby generował wywołania zwrotne dla części treści metody i automatycznie utworzyć `Task<IActionResult>` zwracany obiekt.</span><span class="sxs-lookup"><span data-stu-id="801fe-559">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>
* <span data-ttu-id="801fe-560">Typ zwracany `Task<IActionResult>` reprezentuje bieżącą współpracę z wynikiem typu `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="801fe-560">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>
* <span data-ttu-id="801fe-561">`await`Słowo kluczowe powoduje, że kompilator dzieli metodę na dwie części.</span><span class="sxs-lookup"><span data-stu-id="801fe-561">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="801fe-562">Pierwsza część jest zakończona operacją uruchomioną asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="801fe-562">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="801fe-563">Druga część jest umieszczana w metodzie wywołania zwrotnego, która jest wywoływana po zakończeniu operacji.</span><span class="sxs-lookup"><span data-stu-id="801fe-563">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="801fe-564">`ToListAsync` jest asynchroniczną wersją `ToList` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="801fe-564">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="801fe-565">Niektóre kwestie, o których należy wiedzieć, gdy piszesz kod asynchroniczny, który używa Entity Framework:</span><span class="sxs-lookup"><span data-stu-id="801fe-565">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="801fe-566">Tylko instrukcje, które powodują, że zapytania lub polecenia wysyłane do bazy danych są wykonywane asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="801fe-566">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="801fe-567">Obejmuje to, na przykład, `ToListAsync` , `SingleOrDefaultAsync` i `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="801fe-567">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="801fe-568">Nie zawiera na przykład instrukcji, które po prostu zmieniają element `IQueryable` , taki jak `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="801fe-568">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="801fe-569">Kontekst EF nie jest bezpieczny wątkowo: nie próbuj wykonać równolegle wielu operacji.</span><span class="sxs-lookup"><span data-stu-id="801fe-569">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="801fe-570">Gdy wywoływana jest metoda async EF, zawsze używaj `await` słowa kluczowego.</span><span class="sxs-lookup"><span data-stu-id="801fe-570">When you call any async EF method, always use the `await` keyword.</span></span>
* <span data-ttu-id="801fe-571">Jeśli chcesz wykorzystać zalety wydajności w kodzie asynchronicznym, upewnij się, że wszystkie używane pakiety biblioteki (na przykład na potrzeby stronicowania) używają również metody asynchronicznej, jeśli wywołują wszelkie Entity Framework metod, które powodują wysłanie zapytań do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="801fe-571">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="801fe-572">Aby uzyskać więcej informacji na temat programowania asynchronicznego w programie .NET, zobacz [asynchroniczne Omówienie](/dotnet/articles/standard/async).</span><span class="sxs-lookup"><span data-stu-id="801fe-572">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="next-steps"></a><span data-ttu-id="801fe-573">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="801fe-573">Next steps</span></span>

<span data-ttu-id="801fe-574">Przejdź do następnego samouczka, aby dowiedzieć się, jak wykonywać podstawowe operacje CRUD (tworzenie, odczytywanie, aktualizowanie i usuwanie).</span><span class="sxs-lookup"><span data-stu-id="801fe-574">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="801fe-575">Zaimplementuj podstawowe funkcje CRUD</span><span class="sxs-lookup"><span data-stu-id="801fe-575">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end
