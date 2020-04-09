---
title: Wprowadzenie do ASP.NET Core i Entity Framework 6
author: rick-anderson
description: W tym artykule pokazano, jak używać entity framework 6 w aplikacji ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/entity-framework-6
ms.openlocfilehash: 85cf86dcb22ef94cfc87975abaab176e4f1227d3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656388"
---
# <a name="get-started-with-aspnet-core-and-entity-framework-6"></a><span data-ttu-id="3258d-103">Wprowadzenie do ASP.NET Core i Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="3258d-103">Get Started with ASP.NET Core and Entity Framework 6</span></span>

<span data-ttu-id="3258d-104">[Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex)i [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="3258d-104">By [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="3258d-105">W tym artykule pokazano, jak używać entity framework 6 w aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3258d-105">This article shows how to use Entity Framework 6 in an ASP.NET Core application.</span></span>

## <a name="overview"></a><span data-ttu-id="3258d-106">Omówienie</span><span class="sxs-lookup"><span data-stu-id="3258d-106">Overview</span></span>

<span data-ttu-id="3258d-107">Aby użyć entity framework 6, projekt musi skompilować względem .NET Framework, jako Entity Framework 6 nie obsługuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3258d-107">To use Entity Framework 6, your project has to compile against .NET Framework, as Entity Framework 6 doesn't support .NET Core.</span></span> <span data-ttu-id="3258d-108">Jeśli potrzebujesz funkcji międzyplatformowych, musisz uaktualnić do [entity framework core](/ef/).</span><span class="sxs-lookup"><span data-stu-id="3258d-108">If you need cross-platform features you will need to upgrade to [Entity Framework Core](/ef/).</span></span>

<span data-ttu-id="3258d-109">Zalecanym sposobem użycia entity framework 6 w aplikacji ASP.NET Core jest umieszczenie kontekstu EF6 i klas modelu w projekcie biblioteki klas, który jest przeznaczony dla platformy .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="3258d-109">The recommended way to use Entity Framework 6 in an ASP.NET Core application is to put the EF6 context and model classes in a class library project that targets .NET Framework.</span></span> <span data-ttu-id="3258d-110">Dodaj odwołanie do biblioteki klas z projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3258d-110">Add a reference to the class library from the ASP.NET Core project.</span></span> <span data-ttu-id="3258d-111">Zobacz przykładowe [rozwiązanie programu Visual Studio z ef6 i ASP.NET podstawowych projektów.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/)</span><span class="sxs-lookup"><span data-stu-id="3258d-111">See the sample [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span></span>

<span data-ttu-id="3258d-112">Nie można umieścić kontekstu EF6 w projekcie ASP.NET Core, ponieważ projekty .NET Core nie obsługują wszystkich funkcji wymaganych przez polecenia EF6, takie jak *Włącz migracje.*</span><span class="sxs-lookup"><span data-stu-id="3258d-112">You can't put an EF6 context in an ASP.NET Core project because .NET Core projects don't support all of the functionality that EF6 commands such as *Enable-Migrations* require.</span></span>

<span data-ttu-id="3258d-113">Niezależnie od typu projektu, w którym można zlokalizować kontekst EF6, tylko narzędzia wiersza polecenia EF6 pracy z kontekstu EF6.</span><span class="sxs-lookup"><span data-stu-id="3258d-113">Regardless of project type in which you locate your EF6 context, only EF6 command-line tools work with an EF6 context.</span></span> <span data-ttu-id="3258d-114">Na przykład `Scaffold-DbContext` jest dostępna tylko w entity framework core.</span><span class="sxs-lookup"><span data-stu-id="3258d-114">For example, `Scaffold-DbContext` is only available in Entity Framework Core.</span></span> <span data-ttu-id="3258d-115">Jeśli chcesz wykonać inżynierię odwrotną bazy danych w modelu EF6, zobacz [Code First to an Existing Database](https://msdn.microsoft.com/jj200620).</span><span class="sxs-lookup"><span data-stu-id="3258d-115">If you need to do reverse engineering of a database into an EF6 model, see [Code First to an Existing Database](https://msdn.microsoft.com/jj200620).</span></span>

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a><span data-ttu-id="3258d-116">Odniesienie do pełnych ram i EF6 w projekcie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3258d-116">Reference full framework and EF6 in the ASP.NET Core project</span></span>

<span data-ttu-id="3258d-117">Twój projekt ASP.NET Core musi być ukierunkowany na program .NET Framework i odwoływać się do systemu EF6.</span><span class="sxs-lookup"><span data-stu-id="3258d-117">Your ASP.NET Core project needs to target .NET Framework and reference EF6.</span></span> <span data-ttu-id="3258d-118">Na przykład plik *csproj* projektu ASP.NET Core będzie wyglądał podobnie do poniższego przykładu (wyświetlane są tylko odpowiednie części pliku).</span><span class="sxs-lookup"><span data-stu-id="3258d-118">For example, the *.csproj* file of your ASP.NET Core project will look similar to the following example (only relevant parts of the file are shown).</span></span>

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]

<span data-ttu-id="3258d-119">Podczas tworzenia nowego projektu należy użyć szablonu **ASP.NET Core Web Application (.NET Framework).**</span><span class="sxs-lookup"><span data-stu-id="3258d-119">When creating a new project, use the **ASP.NET Core Web Application (.NET Framework)** template.</span></span>

## <a name="handle-connection-strings"></a><span data-ttu-id="3258d-120">Obsługa ciągów połączeń</span><span class="sxs-lookup"><span data-stu-id="3258d-120">Handle connection strings</span></span>

<span data-ttu-id="3258d-121">Narzędzia wiersza polecenia EF6, które będą używane w projekcie biblioteki klas EF6 wymagają domyślnego konstruktora, aby mogły utworzyć wystąpienie kontekstu.</span><span class="sxs-lookup"><span data-stu-id="3258d-121">The EF6 command-line tools that you'll use in the EF6 class library project require a default constructor so they can instantiate the context.</span></span> <span data-ttu-id="3258d-122">Ale prawdopodobnie należy określić parametry połączenia do użycia w projekcie ASP.NET Core, w którym to przypadku konstruktor kontekstu musi mieć parametr, który umożliwia przekazywanie w ciągu połączenia.</span><span class="sxs-lookup"><span data-stu-id="3258d-122">But you'll probably want to specify the connection string to use in the ASP.NET Core project, in which case your context constructor must have a parameter that lets you pass in the connection string.</span></span> <span data-ttu-id="3258d-123">Oto przykład.</span><span class="sxs-lookup"><span data-stu-id="3258d-123">Here's an example.</span></span>

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]

<span data-ttu-id="3258d-124">Ponieważ kontekst EF6 nie ma konstruktora bez parametrów, projekt EF6 musi zapewnić implementację [IDbContextFactory](https://msdn.microsoft.com/library/hh506876).</span><span class="sxs-lookup"><span data-stu-id="3258d-124">Since your EF6 context doesn't have a parameterless constructor, your EF6 project has to provide an implementation of [IDbContextFactory](https://msdn.microsoft.com/library/hh506876).</span></span> <span data-ttu-id="3258d-125">Narzędzia wiersza polecenia EF6 znajdzie i użyje tej implementacji, dzięki czemu można utworzyć wystąpienia kontekstu.</span><span class="sxs-lookup"><span data-stu-id="3258d-125">The EF6 command-line tools will find and use that implementation so they can instantiate the context.</span></span> <span data-ttu-id="3258d-126">Oto przykład.</span><span class="sxs-lookup"><span data-stu-id="3258d-126">Here's an example.</span></span>

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]

<span data-ttu-id="3258d-127">W tym przykładowym `IDbContextFactory` kodzie implementacja przechodzi w ciąg połączenia zakodowane.</span><span class="sxs-lookup"><span data-stu-id="3258d-127">In this sample code, the `IDbContextFactory` implementation passes in a hard-coded connection string.</span></span> <span data-ttu-id="3258d-128">Jest to ciąg połączenia, który będą używane przez narzędzia wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="3258d-128">This is the connection string that the command-line tools will use.</span></span> <span data-ttu-id="3258d-129">Należy zaimplementować strategię, aby upewnić się, że biblioteka klas używa tego samego ciągu połączenia, który używa aplikacji wywołującej.</span><span class="sxs-lookup"><span data-stu-id="3258d-129">You'll want to implement a strategy to ensure that the class library uses the same connection string that the calling application uses.</span></span> <span data-ttu-id="3258d-130">Na przykład można uzyskać wartość ze zmiennej środowiskowej w obu projektach.</span><span class="sxs-lookup"><span data-stu-id="3258d-130">For example, you could get the value from an environment variable in both projects.</span></span>

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a><span data-ttu-id="3258d-131">Konfigurowanie iniekcji zależności w projekcie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3258d-131">Set up dependency injection in the ASP.NET Core project</span></span>

<span data-ttu-id="3258d-132">W pliku *Startup.cs* projektu Core skonfiguruj kontekst EF6 dla iniekcji zależności (DI) w programie `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3258d-132">In the Core project's *Startup.cs* file, set up the EF6 context for dependency injection (DI) in `ConfigureServices`.</span></span> <span data-ttu-id="3258d-133">Obiekty kontekstu EF powinny być objęte zakresem dla okresu istnienia żądania.</span><span class="sxs-lookup"><span data-stu-id="3258d-133">EF context objects should be scoped for a per-request lifetime.</span></span>

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

<span data-ttu-id="3258d-134">Następnie można uzyskać wystąpienie kontekstu w kontrolerach przy użyciu DI.</span><span class="sxs-lookup"><span data-stu-id="3258d-134">You can then get an instance of the context in your controllers by using DI.</span></span> <span data-ttu-id="3258d-135">Kod jest podobny do tego, co chcesz napisać w kontekście EF Core:</span><span class="sxs-lookup"><span data-stu-id="3258d-135">The code is similar to what you'd write for an EF Core context:</span></span>

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]

## <a name="sample-application"></a><span data-ttu-id="3258d-136">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="3258d-136">Sample application</span></span>

<span data-ttu-id="3258d-137">Dla pracy przykładowej aplikacji, zobacz [przykładowe rozwiązanie programu Visual Studio,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) który towarzyszy w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="3258d-137">For a working sample application, see the [sample Visual Studio solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) that accompanies this article.</span></span>

<span data-ttu-id="3258d-138">Ten przykład można utworzyć od podstaw przez następujące kroki w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="3258d-138">This sample can be created from scratch by the following steps in Visual Studio:</span></span>

* <span data-ttu-id="3258d-139">Tworzenie rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="3258d-139">Create a solution.</span></span>

* <span data-ttu-id="3258d-140">**Dodawanie** > **nowej aplikacji** > sieci**Web** > ASP.NET**Core projektu**</span><span class="sxs-lookup"><span data-stu-id="3258d-140">**Add** > **New Project** > **Web** > **ASP.NET Core Web Application**</span></span>
  * <span data-ttu-id="3258d-141">W oknie dialogowym wyboru szablonu projektu wybierz interfejs API i program .NET Framework w menu rozwijanym</span><span class="sxs-lookup"><span data-stu-id="3258d-141">In project template selection dialog, select API and .NET Framework in dropdown</span></span>

* <span data-ttu-id="3258d-142">**Dodawanie** > nowej biblioteki klas**pulpitu** > systemu Windows > **(.NET Framework)** **New Project**</span><span class="sxs-lookup"><span data-stu-id="3258d-142">**Add** > **New Project** > **Windows Desktop** > **Class Library (.NET Framework)**</span></span>

* <span data-ttu-id="3258d-143">W **konsoli Menedżera pakietów** (PMC) dla `Install-Package Entityframework`obu projektów uruchom polecenie .</span><span class="sxs-lookup"><span data-stu-id="3258d-143">In **Package Manager Console** (PMC) for both projects, run the command `Install-Package Entityframework`.</span></span>

* <span data-ttu-id="3258d-144">W projekcie biblioteki klas utwórz klasy modelu danych i `IDbContextFactory`klasę kontekstu oraz implementację programu .</span><span class="sxs-lookup"><span data-stu-id="3258d-144">In the class library project, create data model classes and a context class, and an implementation of `IDbContextFactory`.</span></span>

* <span data-ttu-id="3258d-145">W pmc dla projektu biblioteki klas, uruchom polecenia `Enable-Migrations` i `Add-Migration Initial`.</span><span class="sxs-lookup"><span data-stu-id="3258d-145">In PMC for the class library project, run the commands `Enable-Migrations` and `Add-Migration Initial`.</span></span> <span data-ttu-id="3258d-146">Jeśli projekt ASP.NET Core został ustawiony jako projekt `-StartupProjectName EF6` startowy, dodaj do tych poleceń.</span><span class="sxs-lookup"><span data-stu-id="3258d-146">If you have set the ASP.NET Core project as the startup project, add `-StartupProjectName EF6` to these commands.</span></span>

* <span data-ttu-id="3258d-147">W projekcie Core dodaj odwołanie do projektu biblioteki klas.</span><span class="sxs-lookup"><span data-stu-id="3258d-147">In the Core project, add a project reference to the class library project.</span></span>

* <span data-ttu-id="3258d-148">W projekcie Core, w *Startup.cs*, zarejestruj kontekst dla DI.</span><span class="sxs-lookup"><span data-stu-id="3258d-148">In the Core project, in *Startup.cs*, register the context for DI.</span></span>

* <span data-ttu-id="3258d-149">W projekcie Core w *pliku appsettings.json*dodaj parametry połączenia.</span><span class="sxs-lookup"><span data-stu-id="3258d-149">In the Core project, in *appsettings.json*, add the connection string.</span></span>

* <span data-ttu-id="3258d-150">W projekcie Core dodaj kontroler i widoky, aby sprawdzić, czy można odczytywać i zapisywać dane.</span><span class="sxs-lookup"><span data-stu-id="3258d-150">In the Core project, add a controller and view(s) to verify that you can read and write data.</span></span> <span data-ttu-id="3258d-151">(Należy zauważyć, że ASP.NET rdzenia MVC rusztowania nie będzie działać z kontekstu EF6 odwołuje się z biblioteki klas.)</span><span class="sxs-lookup"><span data-stu-id="3258d-151">(Note that ASP.NET Core MVC scaffolding won't work with the EF6 context referenced from the class library.)</span></span>

## <a name="summary"></a><span data-ttu-id="3258d-152">Podsumowanie</span><span class="sxs-lookup"><span data-stu-id="3258d-152">Summary</span></span>

<span data-ttu-id="3258d-153">W tym artykule przedstawiono podstawowe wskazówki dotyczące korzystania z entity framework 6 w aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3258d-153">This article has provided basic guidance for using Entity Framework 6 in an ASP.NET Core application.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3258d-154">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="3258d-154">Additional resources</span></span>

* [<span data-ttu-id="3258d-155">Struktura jednostek — konfiguracja oparta na kodzie</span><span class="sxs-lookup"><span data-stu-id="3258d-155">Entity Framework - Code-Based Configuration</span></span>](https://msdn.microsoft.com/data/jj680699.aspx)
