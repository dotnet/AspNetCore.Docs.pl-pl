---
title: Wprowadzenie do ASP.NET Core i Entity Framework 6
author: rick-anderson
description: W tym artykule pokazano, jak używać Entity Framework 6 w aplikacji ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/entity-framework-6
ms.openlocfilehash: 7c77ecb663bebcaf0c6239f07ac15dda23f17b9c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775482"
---
# <a name="get-started-with-aspnet-core-and-entity-framework-6"></a><span data-ttu-id="dae32-103">Wprowadzenie do ASP.NET Core i Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="dae32-103">Get Started with ASP.NET Core and Entity Framework 6</span></span>

<span data-ttu-id="dae32-104">[Paweł grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex)i [Tomasz Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="dae32-104">By [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="dae32-105">W tym artykule pokazano, jak używać Entity Framework 6 w aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dae32-105">This article shows how to use Entity Framework 6 in an ASP.NET Core application.</span></span>

## <a name="overview"></a><span data-ttu-id="dae32-106">Omówienie</span><span class="sxs-lookup"><span data-stu-id="dae32-106">Overview</span></span>

<span data-ttu-id="dae32-107">Aby użyć Entity Framework 6, projekt musi zostać skompilowany w oparciu o .NET Framework, ponieważ Entity Framework 6 nie obsługuje programu .NET Core.</span><span class="sxs-lookup"><span data-stu-id="dae32-107">To use Entity Framework 6, your project has to compile against .NET Framework, as Entity Framework 6 doesn't support .NET Core.</span></span> <span data-ttu-id="dae32-108">Jeśli potrzebujesz funkcji międzyplatformowych, musisz przeprowadzić uaktualnienie do [Entity Framework Core](/ef/).</span><span class="sxs-lookup"><span data-stu-id="dae32-108">If you need cross-platform features you will need to upgrade to [Entity Framework Core](/ef/).</span></span>

<span data-ttu-id="dae32-109">Zalecanym sposobem użycia Entity Framework 6 w aplikacji ASP.NET Core jest umieszczenie EF6 kontekstu i klas modelu w projekcie biblioteki klas, który jest przeznaczony dla .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="dae32-109">The recommended way to use Entity Framework 6 in an ASP.NET Core application is to put the EF6 context and model classes in a class library project that targets .NET Framework.</span></span> <span data-ttu-id="dae32-110">Dodaj odwołanie do biblioteki klas z projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dae32-110">Add a reference to the class library from the ASP.NET Core project.</span></span> <span data-ttu-id="dae32-111">Zobacz przykładowe [rozwiązanie programu Visual Studio z Ef6 i projektami ASP.NET Core](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span><span class="sxs-lookup"><span data-stu-id="dae32-111">See the sample [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span></span>

<span data-ttu-id="dae32-112">Nie można umieścić kontekstu EF6 w projekcie ASP.NET Core, ponieważ projekty .NET Core nie obsługują wszystkich funkcji, które EF6 polecenia, takie jak *enable-migrations* .</span><span class="sxs-lookup"><span data-stu-id="dae32-112">You can't put an EF6 context in an ASP.NET Core project because .NET Core projects don't support all of the functionality that EF6 commands such as *Enable-Migrations* require.</span></span>

<span data-ttu-id="dae32-113">Niezależnie od typu projektu, w którym znajduje się kontekst EF6, tylko narzędzia wiersza polecenia EF6 współpracują z kontekstem EF6.</span><span class="sxs-lookup"><span data-stu-id="dae32-113">Regardless of project type in which you locate your EF6 context, only EF6 command-line tools work with an EF6 context.</span></span> <span data-ttu-id="dae32-114">Na przykład, `Scaffold-DbContext` jest dostępna tylko w Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="dae32-114">For example, `Scaffold-DbContext` is only available in Entity Framework Core.</span></span> <span data-ttu-id="dae32-115">Jeśli musisz przeprowadzić odtwarzanie bazy danych do modelu EF6, zobacz [Code First do istniejącej bazy danych](https://msdn.microsoft.com/jj200620).</span><span class="sxs-lookup"><span data-stu-id="dae32-115">If you need to do reverse engineering of a database into an EF6 model, see [Code First to an Existing Database](https://msdn.microsoft.com/jj200620).</span></span>

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a><span data-ttu-id="dae32-116">Odwołuje się do pełnej struktury i EF6 w projekcie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dae32-116">Reference full framework and EF6 in the ASP.NET Core project</span></span>

<span data-ttu-id="dae32-117">Projekt ASP.NET Core musi kierować do .NET Framework i odwołania EF6.</span><span class="sxs-lookup"><span data-stu-id="dae32-117">Your ASP.NET Core project needs to target .NET Framework and reference EF6.</span></span> <span data-ttu-id="dae32-118">Na przykład plik *. csproj* projektu ASP.NET Core będzie wyglądać podobnie do poniższego przykładu (wyświetlane są tylko odpowiednie części pliku).</span><span class="sxs-lookup"><span data-stu-id="dae32-118">For example, the *.csproj* file of your ASP.NET Core project will look similar to the following example (only relevant parts of the file are shown).</span></span>

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]

<span data-ttu-id="dae32-119">Podczas tworzenia nowego projektu Użyj szablonu **aplikacji sieci Web ASP.NET Core (.NET Framework)** .</span><span class="sxs-lookup"><span data-stu-id="dae32-119">When creating a new project, use the **ASP.NET Core Web Application (.NET Framework)** template.</span></span>

## <a name="handle-connection-strings"></a><span data-ttu-id="dae32-120">Obsługa parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="dae32-120">Handle connection strings</span></span>

<span data-ttu-id="dae32-121">Narzędzia wiersza polecenia EF6, które będą używane w projekcie biblioteki klas EF6, wymagają konstruktora domyślnego, aby mogły utworzyć wystąpienie kontekstu.</span><span class="sxs-lookup"><span data-stu-id="dae32-121">The EF6 command-line tools that you'll use in the EF6 class library project require a default constructor so they can instantiate the context.</span></span> <span data-ttu-id="dae32-122">Jednak prawdopodobnie chcesz określić parametry połączenia do użycia w projekcie ASP.NET Core, w takim przypadku Konstruktor kontekstu musi mieć parametr, który umożliwia przekazywanie parametrów połączenia.</span><span class="sxs-lookup"><span data-stu-id="dae32-122">But you'll probably want to specify the connection string to use in the ASP.NET Core project, in which case your context constructor must have a parameter that lets you pass in the connection string.</span></span> <span data-ttu-id="dae32-123">Oto przykład.</span><span class="sxs-lookup"><span data-stu-id="dae32-123">Here's an example.</span></span>

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]

<span data-ttu-id="dae32-124">Ponieważ kontekst EF6 nie ma konstruktora bez parametrów, projekt EF6 musi dostarczyć implementację [IDbContextFactory](https://msdn.microsoft.com/library/hh506876).</span><span class="sxs-lookup"><span data-stu-id="dae32-124">Since your EF6 context doesn't have a parameterless constructor, your EF6 project has to provide an implementation of [IDbContextFactory](https://msdn.microsoft.com/library/hh506876).</span></span> <span data-ttu-id="dae32-125">Narzędzia wiersza polecenia EF6 znajdą i używają tej implementacji, aby mogli utworzyć wystąpienie kontekstu.</span><span class="sxs-lookup"><span data-stu-id="dae32-125">The EF6 command-line tools will find and use that implementation so they can instantiate the context.</span></span> <span data-ttu-id="dae32-126">Oto przykład.</span><span class="sxs-lookup"><span data-stu-id="dae32-126">Here's an example.</span></span>

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]

<span data-ttu-id="dae32-127">W tym przykładowym kodzie `IDbContextFactory` implementacja przebiega w postaci zakodowanych parametrów połączenia.</span><span class="sxs-lookup"><span data-stu-id="dae32-127">In this sample code, the `IDbContextFactory` implementation passes in a hard-coded connection string.</span></span> <span data-ttu-id="dae32-128">To są parametry połączenia, które będą używane przez narzędzia wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="dae32-128">This is the connection string that the command-line tools will use.</span></span> <span data-ttu-id="dae32-129">Należy wdrożyć strategię, aby upewnić się, że Biblioteka klas używa tych samych parametrów połączenia, które są używane przez aplikację wywołującą.</span><span class="sxs-lookup"><span data-stu-id="dae32-129">You'll want to implement a strategy to ensure that the class library uses the same connection string that the calling application uses.</span></span> <span data-ttu-id="dae32-130">Można na przykład uzyskać wartość ze zmiennej środowiskowej w obu projektach.</span><span class="sxs-lookup"><span data-stu-id="dae32-130">For example, you could get the value from an environment variable in both projects.</span></span>

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a><span data-ttu-id="dae32-131">Konfigurowanie iniekcji zależności w projekcie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dae32-131">Set up dependency injection in the ASP.NET Core project</span></span>

<span data-ttu-id="dae32-132">W pliku *Startup.cs* projektu podstawowego Skonfiguruj kontekst Ef6 dla iniekcji zależności (di) w `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="dae32-132">In the Core project's *Startup.cs* file, set up the EF6 context for dependency injection (DI) in `ConfigureServices`.</span></span> <span data-ttu-id="dae32-133">Obiekty kontekstu EF powinny być ograniczone do zakresu okresu istnienia żądania.</span><span class="sxs-lookup"><span data-stu-id="dae32-133">EF context objects should be scoped for a per-request lifetime.</span></span>

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

<span data-ttu-id="dae32-134">Następnie można uzyskać wystąpienie kontekstu na kontrolerach za pomocą DI.</span><span class="sxs-lookup"><span data-stu-id="dae32-134">You can then get an instance of the context in your controllers by using DI.</span></span> <span data-ttu-id="dae32-135">Kod jest podobny do tego, co zapisujesz w kontekście EF Core:</span><span class="sxs-lookup"><span data-stu-id="dae32-135">The code is similar to what you'd write for an EF Core context:</span></span>

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]

## <a name="sample-application"></a><span data-ttu-id="dae32-136">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="dae32-136">Sample application</span></span>

<span data-ttu-id="dae32-137">Aby uzyskać działającą przykładową aplikację, zobacz [przykładowe rozwiązanie Visual Studio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) , które jest dołączone do tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="dae32-137">For a working sample application, see the [sample Visual Studio solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) that accompanies this article.</span></span>

<span data-ttu-id="dae32-138">Ten przykład można utworzyć od podstaw, wykonując następujące kroki w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="dae32-138">This sample can be created from scratch by the following steps in Visual Studio:</span></span>

* <span data-ttu-id="dae32-139">Utwórz rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="dae32-139">Create a solution.</span></span>

* <span data-ttu-id="dae32-140">**Dodaj**  >  **Nowy projekt**  >  **Sieć Web**  >  **ASP.NET Core aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="dae32-140">**Add** > **New Project** > **Web** > **ASP.NET Core Web Application**</span></span>
  * <span data-ttu-id="dae32-141">W oknie dialogowym Wybieranie szablonu projektu wybierz pozycję Interfejs API i .NET Framework na liście rozwijanej</span><span class="sxs-lookup"><span data-stu-id="dae32-141">In project template selection dialog, select API and .NET Framework in dropdown</span></span>

* <span data-ttu-id="dae32-142">**Dodaj**  >  **Nowy projekt**  >  **Pulpit**  >  systemu Windows **Biblioteka klas (.NET Framework)**</span><span class="sxs-lookup"><span data-stu-id="dae32-142">**Add** > **New Project** > **Windows Desktop** > **Class Library (.NET Framework)**</span></span>

* <span data-ttu-id="dae32-143">W **konsoli Menedżera pakietów** (PMC) dla obu projektów Uruchom polecenie `Install-Package Entityframework` .</span><span class="sxs-lookup"><span data-stu-id="dae32-143">In **Package Manager Console** (PMC) for both projects, run the command `Install-Package Entityframework`.</span></span>

* <span data-ttu-id="dae32-144">W projekcie Biblioteka klas Utwórz klasy modelu danych i klasę kontekstu oraz implementację programu `IDbContextFactory` .</span><span class="sxs-lookup"><span data-stu-id="dae32-144">In the class library project, create data model classes and a context class, and an implementation of `IDbContextFactory`.</span></span>

* <span data-ttu-id="dae32-145">W polu PMC dla projektu biblioteki klas Uruchom polecenia `Enable-Migrations` i `Add-Migration Initial` .</span><span class="sxs-lookup"><span data-stu-id="dae32-145">In PMC for the class library project, run the commands `Enable-Migrations` and `Add-Migration Initial`.</span></span> <span data-ttu-id="dae32-146">Jeśli ustawisz projekt ASP.NET Core jako projekt startowy, Dodaj `-StartupProjectName EF6` do tych poleceń.</span><span class="sxs-lookup"><span data-stu-id="dae32-146">If you have set the ASP.NET Core project as the startup project, add `-StartupProjectName EF6` to these commands.</span></span>

* <span data-ttu-id="dae32-147">W projekcie podstawowym Dodaj odwołanie projektu do projektu biblioteki klas.</span><span class="sxs-lookup"><span data-stu-id="dae32-147">In the Core project, add a project reference to the class library project.</span></span>

* <span data-ttu-id="dae32-148">W projekcie podstawowym w *Startup.cs*Zarejestruj kontekst dla di.</span><span class="sxs-lookup"><span data-stu-id="dae32-148">In the Core project, in *Startup.cs*, register the context for DI.</span></span>

* <span data-ttu-id="dae32-149">W projekcie podstawowym w pliku *appSettings. JSON*Dodaj parametry połączenia.</span><span class="sxs-lookup"><span data-stu-id="dae32-149">In the Core project, in *appsettings.json*, add the connection string.</span></span>

* <span data-ttu-id="dae32-150">W projekcie podstawowym Dodaj kontroler i widok, aby sprawdzić, czy można odczytywać i zapisywać dane.</span><span class="sxs-lookup"><span data-stu-id="dae32-150">In the Core project, add a controller and view(s) to verify that you can read and write data.</span></span> <span data-ttu-id="dae32-151">(Należy zauważyć, że ASP.NET Core tworzenie szkieletu MVC nie będzie współpracowało z kontekstem EF6, do którego odwołuje się Biblioteka klas).</span><span class="sxs-lookup"><span data-stu-id="dae32-151">(Note that ASP.NET Core MVC scaffolding won't work with the EF6 context referenced from the class library.)</span></span>

## <a name="summary"></a><span data-ttu-id="dae32-152">Podsumowanie</span><span class="sxs-lookup"><span data-stu-id="dae32-152">Summary</span></span>

<span data-ttu-id="dae32-153">W tym artykule przedstawiono podstawowe wskazówki dotyczące używania Entity Framework 6 w aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dae32-153">This article has provided basic guidance for using Entity Framework 6 in an ASP.NET Core application.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dae32-154">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="dae32-154">Additional resources</span></span>

* [<span data-ttu-id="dae32-155">Konfiguracja oparta na kodzie Entity Framework</span><span class="sxs-lookup"><span data-stu-id="dae32-155">Entity Framework - Code-Based Configuration</span></span>](https://msdn.microsoft.com/data/jj680699.aspx)
