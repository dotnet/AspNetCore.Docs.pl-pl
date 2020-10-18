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
ms.openlocfilehash: ac84b9d2fac4fe3df48d356eea3ea48fd23bfda4
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113637"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="0afe1-103">ASP.NET Core Blazor Server z Entity Framework Core (EFCore)</span><span class="sxs-lookup"><span data-stu-id="0afe1-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="0afe1-104">Autor: [Jeremy Likness](https://github.com/JeremyLikness)</span><span class="sxs-lookup"><span data-stu-id="0afe1-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0afe1-105">Blazor Server jest platformą aplikacji stanowych.</span><span class="sxs-lookup"><span data-stu-id="0afe1-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="0afe1-106">Aplikacja utrzymuje ciągłe połączenie z serwerem, a stan użytkownika jest przechowywany w pamięci serwera w ramach *obwodu*.</span><span class="sxs-lookup"><span data-stu-id="0afe1-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="0afe1-107">Przykładem stanu użytkownika są dane przechowywane w wystąpieniach usługi [wtrysku zależności (di)](xref:fundamentals/dependency-injection) , które są objęte zakresem obwodu.</span><span class="sxs-lookup"><span data-stu-id="0afe1-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="0afe1-108">Unikatowy model aplikacji, który Blazor Server zapewnia, wymaga specjalnego podejścia do korzystania z Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="0afe1-108">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="0afe1-109">Ten artykuł zawiera adresy EF Core w Blazor Server aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="0afe1-109">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="0afe1-110">Blazor WebAssembly aplikacje działają w piaskownicy zestawu webassembly, które uniemożliwiają większość bezpośrednich połączeń z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="0afe1-110">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="0afe1-111">Uruchamianie EF Core w programie Blazor WebAssembly wykracza poza zakres tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="0afe1-111">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-5x"><span data-ttu-id="0afe1-112">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="0afe1-112">Sample app</span></span></h2>

<span data-ttu-id="0afe1-113">Przykładowa aplikacja została skompilowana jako odwołanie do Blazor Server aplikacji korzystających EF Core.</span><span class="sxs-lookup"><span data-stu-id="0afe1-113">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="0afe1-114">Przykładowa aplikacja zawiera siatkę z operacjami sortowania i filtrowania, usuwania, dodawania i aktualizowania.</span><span class="sxs-lookup"><span data-stu-id="0afe1-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="0afe1-115">Przykład ilustruje użycie EF Core do obsługi optymistycznej współbieżności.</span><span class="sxs-lookup"><span data-stu-id="0afe1-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="0afe1-116">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0afe1-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0afe1-117">Przykład korzysta z lokalnej bazy danych programu [SQLite](https://www.sqlite.org/index.html) , aby można go było używać na dowolnej platformie.</span><span class="sxs-lookup"><span data-stu-id="0afe1-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="0afe1-118">Przykład umożliwia również skonfigurowanie rejestrowania bazy danych w celu wyświetlenia wygenerowanych zapytań SQL.</span><span class="sxs-lookup"><span data-stu-id="0afe1-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="0afe1-119">Ta konfiguracja jest skonfigurowana w `appsettings.Development.json` :</span><span class="sxs-lookup"><span data-stu-id="0afe1-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="0afe1-120">Składniki Grid, Add i View używają wzorca "context-per-Operation", który tworzy kontekst dla każdej operacji.</span><span class="sxs-lookup"><span data-stu-id="0afe1-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="0afe1-121">Składnik edycyjny używa wzorca "kontekst-składnik", gdzie dla każdego składnika jest tworzony kontekst.</span><span class="sxs-lookup"><span data-stu-id="0afe1-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="0afe1-122">Niektóre przykłady kodu w tym temacie wymagają przestrzeni nazw i usług, które nie są wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="0afe1-122">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="0afe1-123">Aby sprawdzić w pełni działający kod, w tym wymagania [`@using`](xref:mvc/views/razor#using) i [`@inject`](xref:mvc/views/razor#inject) dyrektywy dotyczące Razor przykładów, zobacz [przykładową aplikację](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="0afe1-123">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-5x"><span data-ttu-id="0afe1-124">Dostęp do bazy danych</span><span class="sxs-lookup"><span data-stu-id="0afe1-124">Database access</span></span></h2>

<span data-ttu-id="0afe1-125">EF Core opiera się na <xref:Microsoft.EntityFrameworkCore.DbContext> metodzie [konfigurowania dostępu do bazy danych](/ef/core/miscellaneous/configuring-dbcontext) i działania jako [*jednostki pracy*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="0afe1-125">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="0afe1-126">EF Core zapewnia <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> rozszerzenie dla ASP.NET Core aplikacji, które domyślnie rejestruje kontekst jako usługę *objętą zakresem* .</span><span class="sxs-lookup"><span data-stu-id="0afe1-126">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="0afe1-127">W Blazor Server aplikacjach usługa rejestracji w zakresie może być problematyczna, ponieważ wystąpienie jest udostępniane między składnikami w ramach obwodu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0afe1-127">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="0afe1-128"><xref:Microsoft.EntityFrameworkCore.DbContext> nie jest bezpieczny dla wątków i nie jest przeznaczony do współbieżnego użycia.</span><span class="sxs-lookup"><span data-stu-id="0afe1-128"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="0afe1-129">Istniejące okresy istnienia są nieodpowiednie z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="0afe1-129">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="0afe1-130">**Pojedyncze** udziały są w stanie wszyscy użytkownicy aplikacji i potencjalni klienci mają nieodpowiednie współbieżne użycie.</span><span class="sxs-lookup"><span data-stu-id="0afe1-130">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="0afe1-131">**Zakres** (wartość domyślna) stanowi podobny problem między składnikami dla tego samego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0afe1-131">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="0afe1-132">**Przejściowe** wyniki w nowym wystąpieniu na żądanie; Jednak w przypadku, gdy składniki mogą być długotrwałe, wynik może być dłuższy niż w przypadku, gdy jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="0afe1-132">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="0afe1-133">Poniższe zalecenia zaprojektowano w celu zapewnienia spójnego podejścia do korzystania z EF Core w Blazor Server aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="0afe1-133">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="0afe1-134">Domyślnie należy rozważyć użycie jednego kontekstu dla operacji.</span><span class="sxs-lookup"><span data-stu-id="0afe1-134">By default, consider using one context per operation.</span></span> <span data-ttu-id="0afe1-135">Kontekst jest przeznaczony do szybkiego i niskiego tworzenia wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="0afe1-135">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="0afe1-136">Użyj flagi, aby zapobiec wielu współbieżnych operacjach:</span><span class="sxs-lookup"><span data-stu-id="0afe1-136">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="0afe1-137">Umieść operacje po `Loading = true;` wierszu w `try` bloku.</span><span class="sxs-lookup"><span data-stu-id="0afe1-137">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="0afe1-138">W przypadku długotrwałych operacji, które korzystają z [funkcji śledzenia zmian](/ef/core/querying/tracking) EF Core lub [kontroli współbieżności](/ef/core/saving/concurrency), należy [ograniczyć kontekst do okresu istnienia składnika](#scope-to-the-component-lifetime-5x).</span><span class="sxs-lookup"><span data-stu-id="0afe1-138">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-5x).</span></span>

<h3 id="new-dbcontext-instances-5x"><span data-ttu-id="0afe1-139">Nowe wystąpienia DbContext</span><span class="sxs-lookup"><span data-stu-id="0afe1-139">New DbContext instances</span></span></h3>

<span data-ttu-id="0afe1-140">Najszybszą metodą tworzenia nowego <xref:Microsoft.EntityFrameworkCore.DbContext> wystąpienia jest `new` utworzenie nowego wystąpienia przy użyciu programu.</span><span class="sxs-lookup"><span data-stu-id="0afe1-140">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="0afe1-141">Istnieje jednak kilka scenariuszy, które mogą wymagać rozwiązania dodatkowych zależności.</span><span class="sxs-lookup"><span data-stu-id="0afe1-141">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="0afe1-142">Na przykład możesz użyć, [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) Aby skonfigurować kontekst.</span><span class="sxs-lookup"><span data-stu-id="0afe1-142">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="0afe1-143">Zalecanym rozwiązaniem do utworzenia nowego <xref:Microsoft.EntityFrameworkCore.DbContext> z zależności jest użycie fabryki.</span><span class="sxs-lookup"><span data-stu-id="0afe1-143">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="0afe1-144">EF Core 5,0 lub nowszy zapewnia wbudowaną fabrykę do tworzenia nowych kontekstów.</span><span class="sxs-lookup"><span data-stu-id="0afe1-144">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="0afe1-145">Poniższy przykład umożliwia skonfigurowanie [oprogramowania SQLite](https://www.sqlite.org/index.html) i włączenie rejestrowania danych.</span><span class="sxs-lookup"><span data-stu-id="0afe1-145">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="0afe1-146">Kod używa [metody rozszerzającej ( `AddDbContextFactory` )](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) w celu skonfigurowania fabryki bazy danych dla opcji di i podaj opcje domyślne:</span><span class="sxs-lookup"><span data-stu-id="0afe1-146">The code uses an [extension method (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="0afe1-147">Fabryka jest wstrzykiwana do składników i używana do tworzenia nowych wystąpień.</span><span class="sxs-lookup"><span data-stu-id="0afe1-147">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="0afe1-148">Na przykład w `Pages/Index.razor` :</span><span class="sxs-lookup"><span data-stu-id="0afe1-148">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="0afe1-149">`Wrapper` jest [odwołaniem](xref:blazor/components/index#capture-references-to-components) do składnika `GridWrapper` .</span><span class="sxs-lookup"><span data-stu-id="0afe1-149">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="0afe1-150">Zobacz `Index` składnik ( `Pages/Index.razor` ) w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="0afe1-150">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="0afe1-151">Nowe <xref:Microsoft.EntityFrameworkCore.DbContext> wystąpienia można utworzyć przy użyciu fabryki, która umożliwia skonfigurowanie parametrów połączenia na, na przykład `DbContext` w przypadku używania modelu [ASP.NET Core Identity ]) (linki XREF: Security/Authentication/customize_identity_model):</span><span class="sxs-lookup"><span data-stu-id="0afe1-151">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's Identity model])(xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-5x"><span data-ttu-id="0afe1-152">Zakres czasu istnienia składnika</span><span class="sxs-lookup"><span data-stu-id="0afe1-152">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="0afe1-153">Można utworzyć element <xref:Microsoft.EntityFrameworkCore.DbContext> , który istnieje w okresie istnienia składnika.</span><span class="sxs-lookup"><span data-stu-id="0afe1-153">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="0afe1-154">Dzięki temu można używać go jako [jednostki pracy](https://martinfowler.com/eaaCatalog/unitOfWork.html) i korzystać z wbudowanych funkcji, takich jak śledzenie zmian i rozwiązywanie współbieżności.</span><span class="sxs-lookup"><span data-stu-id="0afe1-154">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="0afe1-155">Możesz użyć fabryki, aby utworzyć kontekst i śledzić go na okres istnienia składnika.</span><span class="sxs-lookup"><span data-stu-id="0afe1-155">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="0afe1-156">Najpierw Zaimplementuj <xref:System.IDisposable> i wsuń fabrykę, jak pokazano w `Pages/EditContact.razor` :</span><span class="sxs-lookup"><span data-stu-id="0afe1-156">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="0afe1-157">Aplikacja Przykładowa gwarantuje, że kontekst zostanie usunięty po usunięciu składnika:</span><span class="sxs-lookup"><span data-stu-id="0afe1-157">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="0afe1-158">Na koniec [`OnInitializedAsync`](xref:blazor/components/lifecycle) jest zastępowany w celu utworzenia nowego kontekstu.</span><span class="sxs-lookup"><span data-stu-id="0afe1-158">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="0afe1-159">W przykładowej aplikacji [`OnInitializedAsync`](xref:blazor/components/lifecycle) ładuje kontakt w tej samej metodzie:</span><span class="sxs-lookup"><span data-stu-id="0afe1-159">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="0afe1-160">Włącz rejestrowanie danych poufnych</span><span class="sxs-lookup"><span data-stu-id="0afe1-160">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="0afe1-161"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> zawiera dane aplikacji w komunikatach o wyjątkach i rejestrowaniu struktury.</span><span class="sxs-lookup"><span data-stu-id="0afe1-161"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="0afe1-162">Zarejestrowane dane mogą zawierać wartości przypisane do właściwości wystąpień jednostek i wartości parametrów dla poleceń wysyłanych do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="0afe1-162">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="0afe1-163">Rejestrowanie danych w programie <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> stanowi **zagrożenie bezpieczeństwa**, ponieważ może uwidaczniać hasła i inne dane osobowe, gdy rejestruje instrukcje SQL wykonywane względem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="0afe1-163">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk**, as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="0afe1-164">Zalecamy włączenie tylko <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> do celów deweloperskich i testowych:</span><span class="sxs-lookup"><span data-stu-id="0afe1-164">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

```csharp
#if DEBUG
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db")
        .EnableSensitiveDataLogging());
#else
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db"));
#endif
```

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="0afe1-165">Blazor Server jest platformą aplikacji stanowych.</span><span class="sxs-lookup"><span data-stu-id="0afe1-165">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="0afe1-166">Aplikacja utrzymuje ciągłe połączenie z serwerem, a stan użytkownika jest przechowywany w pamięci serwera w ramach *obwodu*.</span><span class="sxs-lookup"><span data-stu-id="0afe1-166">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="0afe1-167">Przykładem stanu użytkownika są dane przechowywane w wystąpieniach usługi [wtrysku zależności (di)](xref:fundamentals/dependency-injection) , które są objęte zakresem obwodu.</span><span class="sxs-lookup"><span data-stu-id="0afe1-167">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="0afe1-168">Unikatowy model aplikacji, który Blazor Server zapewnia, wymaga specjalnego podejścia do korzystania z Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="0afe1-168">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="0afe1-169">Ten artykuł zawiera adresy EF Core w Blazor Server aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="0afe1-169">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="0afe1-170">Blazor WebAssembly aplikacje działają w piaskownicy zestawu webassembly, które uniemożliwiają większość bezpośrednich połączeń z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="0afe1-170">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="0afe1-171">Uruchamianie EF Core w programie Blazor WebAssembly wykracza poza zakres tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="0afe1-171">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-3x"><span data-ttu-id="0afe1-172">Przykładowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="0afe1-172">Sample app</span></span></h2>

<span data-ttu-id="0afe1-173">Przykładowa aplikacja została skompilowana jako odwołanie do Blazor Server aplikacji korzystających EF Core.</span><span class="sxs-lookup"><span data-stu-id="0afe1-173">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="0afe1-174">Przykładowa aplikacja zawiera siatkę z operacjami sortowania i filtrowania, usuwania, dodawania i aktualizowania.</span><span class="sxs-lookup"><span data-stu-id="0afe1-174">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="0afe1-175">Przykład ilustruje użycie EF Core do obsługi optymistycznej współbieżności.</span><span class="sxs-lookup"><span data-stu-id="0afe1-175">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="0afe1-176">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0afe1-176">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0afe1-177">Przykład korzysta z lokalnej bazy danych programu [SQLite](https://www.sqlite.org/index.html) , aby można go było używać na dowolnej platformie.</span><span class="sxs-lookup"><span data-stu-id="0afe1-177">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="0afe1-178">Przykład umożliwia również skonfigurowanie rejestrowania bazy danych w celu wyświetlenia wygenerowanych zapytań SQL.</span><span class="sxs-lookup"><span data-stu-id="0afe1-178">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="0afe1-179">Ta konfiguracja jest skonfigurowana w `appsettings.Development.json` :</span><span class="sxs-lookup"><span data-stu-id="0afe1-179">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="0afe1-180">Składniki Grid, Add i View używają wzorca "context-per-Operation", który tworzy kontekst dla każdej operacji.</span><span class="sxs-lookup"><span data-stu-id="0afe1-180">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="0afe1-181">Składnik edycyjny używa wzorca "kontekst-składnik", gdzie dla każdego składnika jest tworzony kontekst.</span><span class="sxs-lookup"><span data-stu-id="0afe1-181">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="0afe1-182">Niektóre przykłady kodu w tym temacie wymagają przestrzeni nazw i usług, które nie są wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="0afe1-182">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="0afe1-183">Aby sprawdzić w pełni działający kod, w tym wymagania [`@using`](xref:mvc/views/razor#using) i [`@inject`](xref:mvc/views/razor#inject) dyrektywy dotyczące Razor przykładów, zobacz [przykładową aplikację](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="0afe1-183">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-3x"><span data-ttu-id="0afe1-184">Dostęp do bazy danych</span><span class="sxs-lookup"><span data-stu-id="0afe1-184">Database access</span></span></h2>

<span data-ttu-id="0afe1-185">EF Core opiera się na <xref:Microsoft.EntityFrameworkCore.DbContext> metodzie [konfigurowania dostępu do bazy danych](/ef/core/miscellaneous/configuring-dbcontext) i działania jako [*jednostki pracy*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="0afe1-185">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="0afe1-186">EF Core zapewnia <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> rozszerzenie dla ASP.NET Core aplikacji, które domyślnie rejestruje kontekst jako usługę *objętą zakresem* .</span><span class="sxs-lookup"><span data-stu-id="0afe1-186">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="0afe1-187">W Blazor Server aplikacjach może to być problematyczne, ponieważ wystąpienie jest udostępniane między składnikami w ramach obwodu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0afe1-187">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="0afe1-188"><xref:Microsoft.EntityFrameworkCore.DbContext> nie jest bezpieczny dla wątków i nie jest przeznaczony do współbieżnego użycia.</span><span class="sxs-lookup"><span data-stu-id="0afe1-188"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="0afe1-189">Istniejące okresy istnienia są nieodpowiednie z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="0afe1-189">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="0afe1-190">**Pojedyncze** udziały są w stanie wszyscy użytkownicy aplikacji i potencjalni klienci mają nieodpowiednie współbieżne użycie.</span><span class="sxs-lookup"><span data-stu-id="0afe1-190">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="0afe1-191">**Zakres** (wartość domyślna) stanowi podobny problem między składnikami dla tego samego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0afe1-191">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="0afe1-192">**Przejściowe** wyniki w nowym wystąpieniu na żądanie; Jednak w przypadku, gdy składniki mogą być długotrwałe, wynik może być dłuższy niż w przypadku, gdy jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="0afe1-192">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="0afe1-193">Poniższe zalecenia zaprojektowano w celu zapewnienia spójnego podejścia do korzystania z EF Core w Blazor Server aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="0afe1-193">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="0afe1-194">Domyślnie należy rozważyć użycie jednego kontekstu dla operacji.</span><span class="sxs-lookup"><span data-stu-id="0afe1-194">By default, consider using one context per operation.</span></span> <span data-ttu-id="0afe1-195">Kontekst jest przeznaczony do szybkiego i niskiego tworzenia wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="0afe1-195">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="0afe1-196">Użyj flagi, aby zapobiec wielu współbieżnych operacjach:</span><span class="sxs-lookup"><span data-stu-id="0afe1-196">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="0afe1-197">Umieść operacje po `Loading = true;` wierszu w `try` bloku.</span><span class="sxs-lookup"><span data-stu-id="0afe1-197">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="0afe1-198">W przypadku długotrwałych operacji, które korzystają z [funkcji śledzenia zmian](/ef/core/querying/tracking) EF Core lub [kontroli współbieżności](/ef/core/saving/concurrency), należy [ograniczyć kontekst do okresu istnienia składnika](#scope-to-the-component-lifetime-3x).</span><span class="sxs-lookup"><span data-stu-id="0afe1-198">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-3x).</span></span>

<h3 id="new-dbcontext-instances-3x"><span data-ttu-id="0afe1-199">Nowe wystąpienia DbContext</span><span class="sxs-lookup"><span data-stu-id="0afe1-199">New DbContext instances</span></span></h3>

<span data-ttu-id="0afe1-200">Najszybszą metodą tworzenia nowego <xref:Microsoft.EntityFrameworkCore.DbContext> wystąpienia jest `new` utworzenie nowego wystąpienia przy użyciu programu.</span><span class="sxs-lookup"><span data-stu-id="0afe1-200">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="0afe1-201">Istnieje jednak kilka scenariuszy, które mogą wymagać rozwiązania dodatkowych zależności.</span><span class="sxs-lookup"><span data-stu-id="0afe1-201">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="0afe1-202">Na przykład możesz użyć, [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) Aby skonfigurować kontekst.</span><span class="sxs-lookup"><span data-stu-id="0afe1-202">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="0afe1-203">Zalecanym rozwiązaniem do utworzenia nowego <xref:Microsoft.EntityFrameworkCore.DbContext> z zależności jest użycie fabryki.</span><span class="sxs-lookup"><span data-stu-id="0afe1-203">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="0afe1-204">Przykładowa aplikacja implementuje własną fabrykę w programie `Data/DbContextFactory.cs` .</span><span class="sxs-lookup"><span data-stu-id="0afe1-204">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="0afe1-205">W poprzedniej fabryce:</span><span class="sxs-lookup"><span data-stu-id="0afe1-205">In the preceding factory:</span></span>

* <span data-ttu-id="0afe1-206"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> spełnia wszelkie zależności za pośrednictwem dostawcy usług.</span><span class="sxs-lookup"><span data-stu-id="0afe1-206"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>
* <span data-ttu-id="0afe1-207">`IDbContextFactory` jest dostępny w EF Core ASP.NET Core 5,0 lub nowszej, więc interfejs jest [implementowany w przykładowej aplikacji dla ASP.NET Core 3. x](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).</span><span class="sxs-lookup"><span data-stu-id="0afe1-207">`IDbContextFactory` is available in EF Core ASP.NET Core 5.0 or later, so the interface is [implemented in the sample app for ASP.NET Core 3.x](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).</span></span>

<span data-ttu-id="0afe1-208">Poniższy przykład umożliwia skonfigurowanie [oprogramowania SQLite](https://www.sqlite.org/index.html) i włączenie rejestrowania danych.</span><span class="sxs-lookup"><span data-stu-id="0afe1-208">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="0afe1-209">Kod używa metody rozszerzającej, aby skonfigurować fabrykę bazy danych dla opcji DI i udostępnić opcje domyślne:</span><span class="sxs-lookup"><span data-stu-id="0afe1-209">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="0afe1-210">Fabryka jest wstrzykiwana do składników i używana do tworzenia nowych wystąpień.</span><span class="sxs-lookup"><span data-stu-id="0afe1-210">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="0afe1-211">Na przykład w `Pages/Index.razor` :</span><span class="sxs-lookup"><span data-stu-id="0afe1-211">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="0afe1-212">`Wrapper` jest [odwołaniem](xref:blazor/components/index#capture-references-to-components) do składnika `GridWrapper` .</span><span class="sxs-lookup"><span data-stu-id="0afe1-212">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="0afe1-213">Zobacz `Index` składnik ( `Pages/Index.razor` ) w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="0afe1-213">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="0afe1-214">Nowe <xref:Microsoft.EntityFrameworkCore.DbContext> wystąpienia można utworzyć przy użyciu fabryki, która umożliwia skonfigurowanie parametrów połączenia na, na przykład `DbContext` w przypadku używania modelu [ASP.NET Core Identity ]) (linki XREF: Security/Authentication/customize_identity_model):</span><span class="sxs-lookup"><span data-stu-id="0afe1-214">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's Identity model])(xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-3x"><span data-ttu-id="0afe1-215">Zakres czasu istnienia składnika</span><span class="sxs-lookup"><span data-stu-id="0afe1-215">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="0afe1-216">Można utworzyć element <xref:Microsoft.EntityFrameworkCore.DbContext> , który istnieje w okresie istnienia składnika.</span><span class="sxs-lookup"><span data-stu-id="0afe1-216">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="0afe1-217">Dzięki temu można używać go jako [jednostki pracy](https://martinfowler.com/eaaCatalog/unitOfWork.html) i korzystać z wbudowanych funkcji, takich jak śledzenie zmian i rozwiązywanie współbieżności.</span><span class="sxs-lookup"><span data-stu-id="0afe1-217">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="0afe1-218">Możesz użyć fabryki, aby utworzyć kontekst i śledzić go na okres istnienia składnika.</span><span class="sxs-lookup"><span data-stu-id="0afe1-218">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="0afe1-219">Najpierw Zaimplementuj <xref:System.IDisposable> i wsuń fabrykę, jak pokazano w `Pages/EditContact.razor` :</span><span class="sxs-lookup"><span data-stu-id="0afe1-219">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="0afe1-220">Aplikacja Przykładowa gwarantuje, że kontekst zostanie usunięty po usunięciu składnika:</span><span class="sxs-lookup"><span data-stu-id="0afe1-220">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="0afe1-221">Na koniec [`OnInitializedAsync`](xref:blazor/components/lifecycle) jest zastępowany w celu utworzenia nowego kontekstu.</span><span class="sxs-lookup"><span data-stu-id="0afe1-221">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="0afe1-222">W przykładowej aplikacji [`OnInitializedAsync`](xref:blazor/components/lifecycle) ładuje kontakt w tej samej metodzie:</span><span class="sxs-lookup"><span data-stu-id="0afe1-222">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="0afe1-223">W powyższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="0afe1-223">In the preceding example:</span></span>

* <span data-ttu-id="0afe1-224">Gdy `Busy` jest ustawiona na `true` , mogą zaczynać się operacje asynchroniczne.</span><span class="sxs-lookup"><span data-stu-id="0afe1-224">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="0afe1-225">Gdy `Busy` jest ustawiona z powrotem `false` , operacje asynchroniczne powinny zostać zakończone.</span><span class="sxs-lookup"><span data-stu-id="0afe1-225">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="0afe1-226">Umieść dodatkową logikę obsługi błędów w `catch` bloku.</span><span class="sxs-lookup"><span data-stu-id="0afe1-226">Place additional error handling logic in a `catch` block.</span></span>

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="0afe1-227">Włącz rejestrowanie danych poufnych</span><span class="sxs-lookup"><span data-stu-id="0afe1-227">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="0afe1-228"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> zawiera dane aplikacji w komunikatach o wyjątkach i rejestrowaniu struktury.</span><span class="sxs-lookup"><span data-stu-id="0afe1-228"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="0afe1-229">Zarejestrowane dane mogą zawierać wartości przypisane do właściwości wystąpień jednostek i wartości parametrów dla poleceń wysyłanych do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="0afe1-229">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="0afe1-230">Rejestrowanie danych w programie <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> stanowi **zagrożenie bezpieczeństwa**, ponieważ może uwidaczniać hasła i inne dane osobowe, gdy rejestruje instrukcje SQL wykonywane względem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="0afe1-230">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk**, as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="0afe1-231">Zalecamy włączenie tylko <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> do celów deweloperskich i testowych:</span><span class="sxs-lookup"><span data-stu-id="0afe1-231">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

```csharp
#if DEBUG
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db")
        .EnableSensitiveDataLogging());
#else
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db"));
#endif
```

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="0afe1-232">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="0afe1-232">Additional resources</span></span>

* [<span data-ttu-id="0afe1-233">Dokumentacja EF Core</span><span class="sxs-lookup"><span data-stu-id="0afe1-233">EF Core documentation</span></span>](/ef/)
