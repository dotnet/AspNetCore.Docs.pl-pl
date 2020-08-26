---
title: ASP.NET Core Blazor Server z Entity Framework Core (EFCore)
author: JeremyLikness
description: Wskazówki dotyczące korzystania z EF Core w Blazor Server aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
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
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: b71b742c8a60b4b563649baa181b8c332ff02501
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865194"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="5f809-103">ASP.NET Core Blazor Server z Entity Framework Core (EFCore)</span><span class="sxs-lookup"><span data-stu-id="5f809-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="5f809-104">Autor: [Jeremy Likness](https://github.com/JeremyLikness)</span><span class="sxs-lookup"><span data-stu-id="5f809-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="5f809-105">Blazor Server jest platformą aplikacji stanowych.</span><span class="sxs-lookup"><span data-stu-id="5f809-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="5f809-106">Aplikacja utrzymuje ciągłe połączenie z serwerem, a stan użytkownika jest przechowywany w pamięci serwera w ramach *obwodu*.</span><span class="sxs-lookup"><span data-stu-id="5f809-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="5f809-107">Przykładem stanu użytkownika są dane przechowywane w wystąpieniach usługi [wtrysku zależności (di)](xref:fundamentals/dependency-injection) , które są objęte zakresem obwodu.</span><span class="sxs-lookup"><span data-stu-id="5f809-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="5f809-108">Unikatowy model aplikacji, który Blazor Server zapewnia, wymaga specjalnego podejścia do korzystania z Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="5f809-108">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="5f809-109">Ten artykuł zawiera adresy EF Core w Blazor Server aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="5f809-109">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="5f809-110">Blazor WebAssembly aplikacje działają w piaskownicy zestawu webassembly, które uniemożliwiają większość bezpośrednich połączeń z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="5f809-110">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="5f809-111">Uruchamianie EF Core w programie Blazor WebAssembly wykracza poza zakres tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="5f809-111">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

## <a name="sample-app"></a><span data-ttu-id="5f809-112">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="5f809-112">Sample app</span></span>

<span data-ttu-id="5f809-113">Przykładowa aplikacja została skompilowana jako odwołanie do Blazor Server aplikacji korzystających EF Core.</span><span class="sxs-lookup"><span data-stu-id="5f809-113">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="5f809-114">Przykładowa aplikacja zawiera siatkę z operacjami sortowania i filtrowania, usuwania, dodawania i aktualizowania.</span><span class="sxs-lookup"><span data-stu-id="5f809-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="5f809-115">Przykład ilustruje użycie EF Core do obsługi optymistycznej współbieżności.</span><span class="sxs-lookup"><span data-stu-id="5f809-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="5f809-116">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5f809-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5f809-117">Przykład korzysta z lokalnej bazy danych programu [SQLite](https://www.sqlite.org/index.html) , aby można go było używać na dowolnej platformie.</span><span class="sxs-lookup"><span data-stu-id="5f809-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="5f809-118">Przykład umożliwia również skonfigurowanie rejestrowania bazy danych w celu wyświetlenia wygenerowanych zapytań SQL.</span><span class="sxs-lookup"><span data-stu-id="5f809-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="5f809-119">Ta konfiguracja jest skonfigurowana w `appsettings.Development.json` :</span><span class="sxs-lookup"><span data-stu-id="5f809-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="5f809-120">Składniki Grid, Add i View używają wzorca "context-per-Operation", który tworzy kontekst dla każdej operacji.</span><span class="sxs-lookup"><span data-stu-id="5f809-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="5f809-121">Składnik edycyjny używa wzorca "kontekst-składnik", gdzie dla każdego składnika jest tworzony kontekst.</span><span class="sxs-lookup"><span data-stu-id="5f809-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="5f809-122">Niektóre przykłady kodu w tym temacie wymagają przestrzeni nazw i usług, które nie są wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="5f809-122">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="5f809-123">Aby sprawdzić w pełni działający kod, w tym wymagania [`@using`](xref:mvc/views/razor#using) i [`@inject`](xref:mvc/views/razor#inject) dyrektywy dotyczące Razor przykładów, zobacz [przykładową aplikację](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="5f809-123">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

## <a name="database-access"></a><span data-ttu-id="5f809-124">Dostęp do bazy danych</span><span class="sxs-lookup"><span data-stu-id="5f809-124">Database access</span></span>

<span data-ttu-id="5f809-125">EF Core opiera się na <xref:Microsoft.EntityFrameworkCore.DbContext> metodzie [konfigurowania dostępu do bazy danych](/ef/core/miscellaneous/configuring-dbcontext) i działania jako [*jednostki pracy*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="5f809-125">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="5f809-126">EF Core zapewnia <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> rozszerzenie dla ASP.NET Core aplikacji, które domyślnie rejestruje kontekst jako usługę *objętą zakresem* .</span><span class="sxs-lookup"><span data-stu-id="5f809-126">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="5f809-127">W Blazor Server aplikacjach usługa rejestracji w zakresie może być problematyczna, ponieważ wystąpienie jest udostępniane między składnikami w ramach obwodu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5f809-127">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="5f809-128"><xref:Microsoft.EntityFrameworkCore.DbContext> nie jest bezpieczny dla wątków i nie jest przeznaczony do współbieżnego użycia.</span><span class="sxs-lookup"><span data-stu-id="5f809-128"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="5f809-129">Istniejące okresy istnienia są nieodpowiednie z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="5f809-129">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="5f809-130">**Pojedyncze** udziały są w stanie wszyscy użytkownicy aplikacji i potencjalni klienci mają nieodpowiednie współbieżne użycie.</span><span class="sxs-lookup"><span data-stu-id="5f809-130">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="5f809-131">**Zakres** (wartość domyślna) stanowi podobny problem między składnikami dla tego samego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5f809-131">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="5f809-132">**Przejściowe** wyniki w nowym wystąpieniu na żądanie; Jednak w przypadku, gdy składniki mogą być długotrwałe, wynik może być dłuższy niż w przypadku, gdy jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="5f809-132">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="5f809-133">Poniższe zalecenia zaprojektowano w celu zapewnienia spójnego podejścia do korzystania z EF Core w Blazor Server aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="5f809-133">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="5f809-134">Domyślnie należy rozważyć użycie jednego kontekstu dla operacji.</span><span class="sxs-lookup"><span data-stu-id="5f809-134">By default, consider using one context per operation.</span></span> <span data-ttu-id="5f809-135">Kontekst jest przeznaczony do szybkiego i niskiego tworzenia wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="5f809-135">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="5f809-136">Użyj flagi, aby zapobiec wielu współbieżnych operacjach:</span><span class="sxs-lookup"><span data-stu-id="5f809-136">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="5f809-137">Umieść operacje po `Loading = true;` wierszu w `try` bloku.</span><span class="sxs-lookup"><span data-stu-id="5f809-137">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="5f809-138">W przypadku długotrwałych operacji, które korzystają z [funkcji śledzenia zmian](/ef/core/querying/tracking) EF Core lub [kontroli współbieżności](/ef/core/saving/concurrency), należy [ograniczyć kontekst do okresu istnienia składnika](#scope-to-the-component-lifetime).</span><span class="sxs-lookup"><span data-stu-id="5f809-138">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime).</span></span>

### <a name="new-dbcontext-instances"></a><span data-ttu-id="5f809-139">Nowe wystąpienia DbContext</span><span class="sxs-lookup"><span data-stu-id="5f809-139">New DbContext instances</span></span>

<span data-ttu-id="5f809-140">Najszybszą metodą tworzenia nowego <xref:Microsoft.EntityFrameworkCore.DbContext> wystąpienia jest `new` utworzenie nowego wystąpienia przy użyciu programu.</span><span class="sxs-lookup"><span data-stu-id="5f809-140">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="5f809-141">Istnieje jednak kilka scenariuszy, które mogą wymagać rozwiązania dodatkowych zależności.</span><span class="sxs-lookup"><span data-stu-id="5f809-141">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="5f809-142">Na przykład możesz użyć, [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) Aby skonfigurować kontekst.</span><span class="sxs-lookup"><span data-stu-id="5f809-142">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="5f809-143">Zalecanym rozwiązaniem do utworzenia nowego <xref:Microsoft.EntityFrameworkCore.DbContext> z zależności jest użycie fabryki.</span><span class="sxs-lookup"><span data-stu-id="5f809-143">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="5f809-144">EF Core 5,0 lub nowszy zapewnia wbudowaną fabrykę do tworzenia nowych kontekstów.</span><span class="sxs-lookup"><span data-stu-id="5f809-144">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="5f809-145">Poniższy przykład umożliwia skonfigurowanie [oprogramowania SQLite](https://www.sqlite.org/index.html) i włączenie rejestrowania danych.</span><span class="sxs-lookup"><span data-stu-id="5f809-145">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="5f809-146">Kod używa metody rozszerzającej, aby skonfigurować fabrykę bazy danych dla opcji DI i udostępnić opcje domyślne:</span><span class="sxs-lookup"><span data-stu-id="5f809-146">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="5f809-147">Fabryka jest wstrzykiwana do składników i używana do tworzenia nowych wystąpień.</span><span class="sxs-lookup"><span data-stu-id="5f809-147">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="5f809-148">Na przykład w `Pages/Index.razor` :</span><span class="sxs-lookup"><span data-stu-id="5f809-148">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> <span data-ttu-id="5f809-149">! [Uwaga] `Wrapper` jest [odwołaniem](xref:blazor/components/index#capture-references-to-components) do składnika `GridWrapper` .</span><span class="sxs-lookup"><span data-stu-id="5f809-149">![NOTE] `Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="5f809-150">Zobacz `Index` składnik ( `Pages/Index.razor` ) w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="5f809-150">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

### <a name="scope-to-the-component-lifetime"></a><span data-ttu-id="5f809-151">Zakres czasu istnienia składnika</span><span class="sxs-lookup"><span data-stu-id="5f809-151">Scope to the component lifetime</span></span>

<span data-ttu-id="5f809-152">Można utworzyć element <xref:Microsoft.EntityFrameworkCore.DbContext> , który istnieje w okresie istnienia składnika.</span><span class="sxs-lookup"><span data-stu-id="5f809-152">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="5f809-153">Dzięki temu można używać go jako [jednostki pracy](https://martinfowler.com/eaaCatalog/unitOfWork.html) i korzystać z wbudowanych funkcji, takich jak śledzenie zmian i rozwiązywanie współbieżności.</span><span class="sxs-lookup"><span data-stu-id="5f809-153">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="5f809-154">Możesz użyć fabryki, aby utworzyć kontekst i śledzić go na okres istnienia składnika.</span><span class="sxs-lookup"><span data-stu-id="5f809-154">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="5f809-155">Najpierw Zaimplementuj <xref:System.IDisposable> i wsuń fabrykę, jak pokazano w `Pages/EditContact.razor` :</span><span class="sxs-lookup"><span data-stu-id="5f809-155">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="5f809-156">Aplikacja Przykładowa gwarantuje, że kontakt zostanie usunięty po usunięciu składnika:</span><span class="sxs-lookup"><span data-stu-id="5f809-156">The sample app ensures the contact is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="5f809-157">Na koniec [`OnInitializedAsync`](xref:blazor/components/lifecycle) jest zastępowany w celu utworzenia nowego kontekstu.</span><span class="sxs-lookup"><span data-stu-id="5f809-157">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="5f809-158">W przykładowej aplikacji [`OnInitializedAsync`](xref:blazor/components/lifecycle) ładuje kontakt w tej samej metodzie:</span><span class="sxs-lookup"><span data-stu-id="5f809-158">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="5f809-159">Blazor Server jest platformą aplikacji stanowych.</span><span class="sxs-lookup"><span data-stu-id="5f809-159">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="5f809-160">Aplikacja utrzymuje ciągłe połączenie z serwerem, a stan użytkownika jest przechowywany w pamięci serwera w ramach *obwodu*.</span><span class="sxs-lookup"><span data-stu-id="5f809-160">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="5f809-161">Przykładem stanu użytkownika są dane przechowywane w wystąpieniach usługi [wtrysku zależności (di)](xref:fundamentals/dependency-injection) , które są objęte zakresem obwodu.</span><span class="sxs-lookup"><span data-stu-id="5f809-161">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="5f809-162">Unikatowy model aplikacji, który Blazor Server zapewnia, wymaga specjalnego podejścia do korzystania z Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="5f809-162">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="5f809-163">Ten artykuł zawiera adresy EF Core w Blazor Server aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="5f809-163">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="5f809-164">Blazor WebAssembly aplikacje działają w piaskownicy zestawu webassembly, które uniemożliwiają większość bezpośrednich połączeń z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="5f809-164">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="5f809-165">Uruchamianie EF Core w programie Blazor WebAssembly wykracza poza zakres tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="5f809-165">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

## <a name="sample-app"></a><span data-ttu-id="5f809-166">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="5f809-166">Sample app</span></span>

<span data-ttu-id="5f809-167">Przykładowa aplikacja została skompilowana jako odwołanie do Blazor Server aplikacji korzystających EF Core.</span><span class="sxs-lookup"><span data-stu-id="5f809-167">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="5f809-168">Przykładowa aplikacja zawiera siatkę z operacjami sortowania i filtrowania, usuwania, dodawania i aktualizowania.</span><span class="sxs-lookup"><span data-stu-id="5f809-168">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="5f809-169">Przykład ilustruje użycie EF Core do obsługi optymistycznej współbieżności.</span><span class="sxs-lookup"><span data-stu-id="5f809-169">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="5f809-170">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5f809-170">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5f809-171">Przykład korzysta z lokalnej bazy danych programu [SQLite](https://www.sqlite.org/index.html) , aby można go było używać na dowolnej platformie.</span><span class="sxs-lookup"><span data-stu-id="5f809-171">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="5f809-172">Przykład umożliwia również skonfigurowanie rejestrowania bazy danych w celu wyświetlenia wygenerowanych zapytań SQL.</span><span class="sxs-lookup"><span data-stu-id="5f809-172">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="5f809-173">Ta konfiguracja jest skonfigurowana w `appsettings.Development.json` :</span><span class="sxs-lookup"><span data-stu-id="5f809-173">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="5f809-174">Składniki Grid, Add i View używają wzorca "context-per-Operation", który tworzy kontekst dla każdej operacji.</span><span class="sxs-lookup"><span data-stu-id="5f809-174">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="5f809-175">Składnik edycyjny używa wzorca "kontekst-składnik", gdzie dla każdego składnika jest tworzony kontekst.</span><span class="sxs-lookup"><span data-stu-id="5f809-175">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="5f809-176">Niektóre przykłady kodu w tym temacie wymagają przestrzeni nazw i usług, które nie są wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="5f809-176">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="5f809-177">Aby sprawdzić w pełni działający kod, w tym wymagania [`@using`](xref:mvc/views/razor#using) i [`@inject`](xref:mvc/views/razor#inject) dyrektywy dotyczące Razor przykładów, zobacz [przykładową aplikację](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="5f809-177">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

## <a name="database-access"></a><span data-ttu-id="5f809-178">Dostęp do bazy danych</span><span class="sxs-lookup"><span data-stu-id="5f809-178">Database access</span></span>

<span data-ttu-id="5f809-179">EF Core opiera się na <xref:Microsoft.EntityFrameworkCore.DbContext> metodzie [konfigurowania dostępu do bazy danych](/ef/core/miscellaneous/configuring-dbcontext) i działania jako [*jednostki pracy*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="5f809-179">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="5f809-180">EF Core zapewnia <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> rozszerzenie dla ASP.NET Core aplikacji, które domyślnie rejestruje kontekst jako usługę *objętą zakresem* .</span><span class="sxs-lookup"><span data-stu-id="5f809-180">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="5f809-181">W Blazor Server aplikacjach może to być problematyczne, ponieważ wystąpienie jest udostępniane między składnikami w ramach obwodu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5f809-181">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="5f809-182"><xref:Microsoft.EntityFrameworkCore.DbContext> nie jest bezpieczny dla wątków i nie jest przeznaczony do współbieżnego użycia.</span><span class="sxs-lookup"><span data-stu-id="5f809-182"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="5f809-183">Istniejące okresy istnienia są nieodpowiednie z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="5f809-183">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="5f809-184">**Pojedyncze** udziały są w stanie wszyscy użytkownicy aplikacji i potencjalni klienci mają nieodpowiednie współbieżne użycie.</span><span class="sxs-lookup"><span data-stu-id="5f809-184">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="5f809-185">**Zakres** (wartość domyślna) stanowi podobny problem między składnikami dla tego samego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5f809-185">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="5f809-186">**Przejściowe** wyniki w nowym wystąpieniu na żądanie; Jednak w przypadku, gdy składniki mogą być długotrwałe, wynik może być dłuższy niż w przypadku, gdy jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="5f809-186">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

## <a name="database-access"></a><span data-ttu-id="5f809-187">Dostęp do bazy danych</span><span class="sxs-lookup"><span data-stu-id="5f809-187">Database access</span></span>

<span data-ttu-id="5f809-188">Poniższe zalecenia zaprojektowano w celu zapewnienia spójnego podejścia do korzystania z EF Core w Blazor Server aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="5f809-188">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="5f809-189">Domyślnie należy rozważyć użycie jednego kontekstu dla operacji.</span><span class="sxs-lookup"><span data-stu-id="5f809-189">By default, consider using one context per operation.</span></span> <span data-ttu-id="5f809-190">Kontekst jest przeznaczony do szybkiego i niskiego tworzenia wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="5f809-190">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="5f809-191">Użyj flagi, aby zapobiec wielu współbieżnych operacjach:</span><span class="sxs-lookup"><span data-stu-id="5f809-191">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="5f809-192">Umieść operacje po `Loading = true;` wierszu w `try` bloku.</span><span class="sxs-lookup"><span data-stu-id="5f809-192">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="5f809-193">W przypadku długotrwałych operacji, które korzystają z [funkcji śledzenia zmian](/ef/core/querying/tracking) EF Core lub [kontroli współbieżności](/ef/core/saving/concurrency), należy [ograniczyć kontekst do okresu istnienia składnika](#scope-to-the-component-lifetime).</span><span class="sxs-lookup"><span data-stu-id="5f809-193">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime).</span></span>

### <a name="create-new-dbcontext-instances"></a><span data-ttu-id="5f809-194">Utwórz nowe wystąpienia DbContext</span><span class="sxs-lookup"><span data-stu-id="5f809-194">Create new DbContext instances</span></span>

<span data-ttu-id="5f809-195">Najszybszą metodą tworzenia nowego <xref:Microsoft.EntityFrameworkCore.DbContext> wystąpienia jest `new` utworzenie nowego wystąpienia przy użyciu programu.</span><span class="sxs-lookup"><span data-stu-id="5f809-195">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="5f809-196">Istnieje jednak kilka scenariuszy, które mogą wymagać rozwiązania dodatkowych zależności.</span><span class="sxs-lookup"><span data-stu-id="5f809-196">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="5f809-197">Na przykład możesz użyć, [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) Aby skonfigurować kontekst.</span><span class="sxs-lookup"><span data-stu-id="5f809-197">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="5f809-198">Zalecanym rozwiązaniem do utworzenia nowego <xref:Microsoft.EntityFrameworkCore.DbContext> z zależności jest użycie fabryki.</span><span class="sxs-lookup"><span data-stu-id="5f809-198">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="5f809-199">Przykładowa aplikacja implementuje własną fabrykę w programie `Data/DbContextFactory.cs` .</span><span class="sxs-lookup"><span data-stu-id="5f809-199">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="5f809-200">W poprzedniej fabryce program <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> spełnia wszelkie zależności za pośrednictwem dostawcy usług.</span><span class="sxs-lookup"><span data-stu-id="5f809-200">In the preceding factory, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>

<span data-ttu-id="5f809-201">Poniższy przykład umożliwia skonfigurowanie [oprogramowania SQLite](https://www.sqlite.org/index.html) i włączenie rejestrowania danych.</span><span class="sxs-lookup"><span data-stu-id="5f809-201">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="5f809-202">Kod używa metody rozszerzającej, aby skonfigurować fabrykę bazy danych dla opcji DI i udostępnić opcje domyślne:</span><span class="sxs-lookup"><span data-stu-id="5f809-202">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="5f809-203">Fabryka jest wstrzykiwana do składników i używana do tworzenia nowych wystąpień.</span><span class="sxs-lookup"><span data-stu-id="5f809-203">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="5f809-204">Na przykład w `Pages/Index.razor` :</span><span class="sxs-lookup"><span data-stu-id="5f809-204">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> <span data-ttu-id="5f809-205">! [Uwaga] `Wrapper` jest [odwołaniem](xref:blazor/components/index#capture-references-to-components) do składnika `GridWrapper` .</span><span class="sxs-lookup"><span data-stu-id="5f809-205">![NOTE] `Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="5f809-206">Zobacz `Index` składnik ( `Pages/Index.razor` ) w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="5f809-206">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

### <a name="scope-to-the-component-lifetime"></a><span data-ttu-id="5f809-207">Zakres czasu istnienia składnika</span><span class="sxs-lookup"><span data-stu-id="5f809-207">Scope to the component lifetime</span></span>

<span data-ttu-id="5f809-208">Można utworzyć element <xref:Microsoft.EntityFrameworkCore.DbContext> , który istnieje w okresie istnienia składnika.</span><span class="sxs-lookup"><span data-stu-id="5f809-208">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="5f809-209">Dzięki temu można używać go jako [jednostki pracy](https://martinfowler.com/eaaCatalog/unitOfWork.html) i korzystać z wbudowanych funkcji, takich jak śledzenie zmian i rozwiązywanie współbieżności.</span><span class="sxs-lookup"><span data-stu-id="5f809-209">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="5f809-210">Możesz użyć fabryki, aby utworzyć kontekst i śledzić go na okres istnienia składnika.</span><span class="sxs-lookup"><span data-stu-id="5f809-210">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="5f809-211">Najpierw Zaimplementuj <xref:System.IDisposable> i wsuń fabrykę, jak pokazano w `Pages/EditContact.razor` :</span><span class="sxs-lookup"><span data-stu-id="5f809-211">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="5f809-212">Aplikacja Przykładowa gwarantuje, że kontakt zostanie usunięty po usunięciu składnika:</span><span class="sxs-lookup"><span data-stu-id="5f809-212">The sample app ensures the contact is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="5f809-213">Na koniec [`OnInitializedAsync`](xref:blazor/components/lifecycle) jest zastępowany w celu utworzenia nowego kontekstu.</span><span class="sxs-lookup"><span data-stu-id="5f809-213">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="5f809-214">W przykładowej aplikacji [`OnInitializedAsync`](xref:blazor/components/lifecycle) ładuje kontakt w tej samej metodzie:</span><span class="sxs-lookup"><span data-stu-id="5f809-214">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="5f809-215">W powyższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="5f809-215">In the preceding example:</span></span>

* <span data-ttu-id="5f809-216">Gdy `Busy` jest ustawiona na `true` , mogą zaczynać się operacje asynchroniczne.</span><span class="sxs-lookup"><span data-stu-id="5f809-216">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="5f809-217">Gdy `Busy` jest ustawiona z powrotem `false` , operacje asynchroniczne powinny zostać zakończone.</span><span class="sxs-lookup"><span data-stu-id="5f809-217">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="5f809-218">Umieść dodatkową logikę obsługi błędów w `catch` bloku.</span><span class="sxs-lookup"><span data-stu-id="5f809-218">Place additional error handling logic in a `catch` block.</span></span>

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="5f809-219">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="5f809-219">Additional resources</span></span>

> [<span data-ttu-id="5f809-220">Dokumentacja EF Core</span><span class="sxs-lookup"><span data-stu-id="5f809-220">EF Core documentation</span></span>](/ef/)
