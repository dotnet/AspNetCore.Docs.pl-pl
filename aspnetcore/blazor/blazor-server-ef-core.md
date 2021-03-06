---
title: ASP.NET Core Blazor Server z Entity Framework Core (EFCore)
author: JeremyLikness
description: Wskazówki dotyczące korzystania z EF Core w Blazor Server aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
no-loc:
- appsettings.json
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
ms.openlocfilehash: db70bafc85311f8ccbf87e123739ea99c8d8f3c5
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394580"
---
# <a name="aspnet-core-blazor-server-with-entity-framework-core-efcore"></a>ASP.NET Core Blazor Server z Entity Framework Core (EFCore)

:::moniker range=">= aspnetcore-5.0"

Blazor Server jest platformą aplikacji stanowych. Aplikacja utrzymuje ciągłe połączenie z serwerem, a stan użytkownika jest przechowywany w pamięci serwera w ramach *obwodu*. Przykładem stanu użytkownika są dane przechowywane w wystąpieniach usługi [wtrysku zależności (di)](xref:fundamentals/dependency-injection) , które są objęte zakresem obwodu. Unikatowy model aplikacji, który Blazor Server zapewnia, wymaga specjalnego podejścia do korzystania z Entity Framework Core.

> [!NOTE]
> Ten artykuł zawiera adresy EF Core w Blazor Server aplikacjach. Blazor WebAssembly aplikacje działają w piaskownicy zestawu webassembly, które uniemożliwiają większość bezpośrednich połączeń z bazą danych. Uruchamianie EF Core w programie Blazor WebAssembly wykracza poza zakres tego artykułu.

<h2 id="sample-app-5x">Przykładowa aplikacja</h2>

Przykładowa aplikacja została skompilowana jako odwołanie do Blazor Server aplikacji korzystających EF Core. Przykładowa aplikacja zawiera siatkę z operacjami sortowania i filtrowania, usuwania, dodawania i aktualizowania. Przykład ilustruje użycie EF Core do obsługi optymistycznej współbieżności.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykład korzysta z lokalnej bazy danych programu [SQLite](https://www.sqlite.org/index.html) , aby można go było używać na dowolnej platformie. Przykład umożliwia również skonfigurowanie rejestrowania bazy danych w celu wyświetlenia wygenerowanych zapytań SQL. Ta konfiguracja jest skonfigurowana w `appsettings.Development.json` :

[!code-json[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

Składniki Grid, Add i View używają wzorca "context-per-Operation", który tworzy kontekst dla każdej operacji. Składnik edycyjny używa wzorca "kontekst-składnik", gdzie dla każdego składnika jest tworzony kontekst.

> [!NOTE]
> Niektóre przykłady kodu w tym temacie wymagają przestrzeni nazw i usług, które nie są wyświetlane. Aby sprawdzić w pełni działający kod, w tym wymagania [`@using`](xref:mvc/views/razor#using) i [`@inject`](xref:mvc/views/razor#inject) dyrektywy dotyczące Razor przykładów, zobacz [przykładową aplikację](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).

<h2 id="database-access-5x">Dostęp do bazy danych</h2>

EF Core opiera się na <xref:Microsoft.EntityFrameworkCore.DbContext> metodzie [konfigurowania dostępu do bazy danych](/ef/core/miscellaneous/configuring-dbcontext) i działania jako [*jednostki pracy*](https://martinfowler.com/eaaCatalog/unitOfWork.html). EF Core zapewnia <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> rozszerzenie dla ASP.NET Core aplikacji, które domyślnie rejestruje kontekst jako usługę *objętą zakresem* . W Blazor Server aplikacjach usługa rejestracji w zakresie może być problematyczna, ponieważ wystąpienie jest udostępniane między składnikami w ramach obwodu użytkownika. <xref:Microsoft.EntityFrameworkCore.DbContext> nie jest bezpieczny dla wątków i nie jest przeznaczony do współbieżnego użycia. Istniejące okresy istnienia są nieodpowiednie z następujących powodów:

* **Pojedyncze** udziały są w stanie wszyscy użytkownicy aplikacji i potencjalni klienci mają nieodpowiednie współbieżne użycie.
* **Zakres** (wartość domyślna) stanowi podobny problem między składnikami dla tego samego użytkownika.
* **Przejściowe** wyniki w nowym wystąpieniu na żądanie; Jednak w przypadku, gdy składniki mogą być długotrwałe, wynik może być dłuższy niż w przypadku, gdy jest to możliwe.

Poniższe zalecenia zaprojektowano w celu zapewnienia spójnego podejścia do korzystania z EF Core w Blazor Server aplikacjach.

* Domyślnie należy rozważyć użycie jednego kontekstu dla operacji. Kontekst jest przeznaczony do szybkiego i niskiego tworzenia wystąpienia:

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* Użyj flagi, aby zapobiec wielu współbieżnych operacjach:

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

  Umieść operacje po `Loading = true;` wierszu w `try` bloku.

* W przypadku długotrwałych operacji, które korzystają z [funkcji śledzenia zmian](/ef/core/querying/tracking) EF Core lub [kontroli współbieżności](/ef/core/saving/concurrency), należy [ograniczyć kontekst do okresu istnienia składnika](#scope-to-the-component-lifetime-5x).

<h3 id="new-dbcontext-instances-5x">Nowe wystąpienia DbContext</h3>

Najszybszą metodą tworzenia nowego <xref:Microsoft.EntityFrameworkCore.DbContext> wystąpienia jest `new` utworzenie nowego wystąpienia przy użyciu programu. Istnieje jednak kilka scenariuszy, które mogą wymagać rozwiązania dodatkowych zależności. Na przykład możesz użyć, [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) Aby skonfigurować kontekst.

Zalecanym rozwiązaniem do utworzenia nowego <xref:Microsoft.EntityFrameworkCore.DbContext> z zależności jest użycie fabryki. EF Core 5,0 lub nowszy zapewnia wbudowaną fabrykę do tworzenia nowych kontekstów.

Poniższy przykład umożliwia skonfigurowanie [oprogramowania SQLite](https://www.sqlite.org/index.html) i włączenie rejestrowania danych. Kod używa [metody rozszerzającej ( `AddDbContextFactory` )](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) w celu skonfigurowania fabryki bazy danych dla opcji di i podaj opcje domyślne:

[!code-csharp[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

Fabryka jest wstrzykiwana do składników i używana do tworzenia nowych wystąpień. Na przykład w `Pages/Index.razor` :

[!code-csharp[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> `Wrapper` jest [odwołaniem](xref:blazor/components/index#capture-references-to-components) do składnika `GridWrapper` . Zobacz `Index` składnik ( `Pages/Index.razor` ) w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).

Nowe <xref:Microsoft.EntityFrameworkCore.DbContext> wystąpienia można utworzyć przy użyciu fabryki, która umożliwia skonfigurowanie parametrów połączenia na, na przykład `DbContext` w przypadku używania [ Identity modelu ASP.NET Core](xref:security/authentication/customize_identity_model):

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-5x">Zakres czasu istnienia składnika</h3>

Można utworzyć element <xref:Microsoft.EntityFrameworkCore.DbContext> , który istnieje w okresie istnienia składnika. Dzięki temu można używać go jako [jednostki pracy](https://martinfowler.com/eaaCatalog/unitOfWork.html) i korzystać z wbudowanych funkcji, takich jak śledzenie zmian i rozwiązywanie współbieżności.
Możesz użyć fabryki, aby utworzyć kontekst i śledzić go na okres istnienia składnika. Najpierw Zaimplementuj <xref:System.IDisposable> i wsuń fabrykę, jak pokazano w `Pages/EditContact.razor` :

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

Aplikacja Przykładowa gwarantuje, że kontekst zostanie usunięty po usunięciu składnika:

[!code-csharp[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

Na koniec [`OnInitializedAsync`](xref:blazor/components/lifecycle) jest zastępowany w celu utworzenia nowego kontekstu. W przykładowej aplikacji [`OnInitializedAsync`](xref:blazor/components/lifecycle) ładuje kontakt w tej samej metodzie:

[!code-csharp[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<h3 id="enable-sensitive-data-logging">Włącz rejestrowanie danych poufnych</h3>

<xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> zawiera dane aplikacji w komunikatach o wyjątkach i rejestrowaniu struktury. Zarejestrowane dane mogą zawierać wartości przypisane do właściwości wystąpień jednostek i wartości parametrów dla poleceń wysyłanych do bazy danych. Rejestrowanie danych w programie <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> stanowi **zagrożenie bezpieczeństwa**, ponieważ może uwidaczniać hasła i inne dane osobowe, gdy rejestruje instrukcje SQL wykonywane względem bazy danych.

Zalecamy włączenie tylko <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> do celów deweloperskich i testowych:

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

Blazor Server jest platformą aplikacji stanowych. Aplikacja utrzymuje ciągłe połączenie z serwerem, a stan użytkownika jest przechowywany w pamięci serwera w ramach *obwodu*. Przykładem stanu użytkownika są dane przechowywane w wystąpieniach usługi [wtrysku zależności (di)](xref:fundamentals/dependency-injection) , które są objęte zakresem obwodu. Unikatowy model aplikacji, który Blazor Server zapewnia, wymaga specjalnego podejścia do korzystania z Entity Framework Core.

> [!NOTE]
> Ten artykuł zawiera adresy EF Core w Blazor Server aplikacjach. Blazor WebAssembly aplikacje działają w piaskownicy zestawu webassembly, które uniemożliwiają większość bezpośrednich połączeń z bazą danych. Uruchamianie EF Core w programie Blazor WebAssembly wykracza poza zakres tego artykułu.

<h2 id="sample-app-3x">Przykładowa aplikacja</h2>

Przykładowa aplikacja została skompilowana jako odwołanie do Blazor Server aplikacji korzystających EF Core. Przykładowa aplikacja zawiera siatkę z operacjami sortowania i filtrowania, usuwania, dodawania i aktualizowania. Przykład ilustruje użycie EF Core do obsługi optymistycznej współbieżności.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykład korzysta z lokalnej bazy danych programu [SQLite](https://www.sqlite.org/index.html) , aby można go było używać na dowolnej platformie. Przykład umożliwia również skonfigurowanie rejestrowania bazy danych w celu wyświetlenia wygenerowanych zapytań SQL. Ta konfiguracja jest skonfigurowana w `appsettings.Development.json` :

[!code-json[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

Składniki Grid, Add i View używają wzorca "context-per-Operation", który tworzy kontekst dla każdej operacji. Składnik edycyjny używa wzorca "kontekst-składnik", gdzie dla każdego składnika jest tworzony kontekst.

> [!NOTE]
> Niektóre przykłady kodu w tym temacie wymagają przestrzeni nazw i usług, które nie są wyświetlane. Aby sprawdzić w pełni działający kod, w tym wymagania [`@using`](xref:mvc/views/razor#using) i [`@inject`](xref:mvc/views/razor#inject) dyrektywy dotyczące Razor przykładów, zobacz [przykładową aplikację](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).

<h2 id="database-access-3x">Dostęp do bazy danych</h2>

EF Core opiera się na <xref:Microsoft.EntityFrameworkCore.DbContext> metodzie [konfigurowania dostępu do bazy danych](/ef/core/miscellaneous/configuring-dbcontext) i działania jako [*jednostki pracy*](https://martinfowler.com/eaaCatalog/unitOfWork.html). EF Core zapewnia <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> rozszerzenie dla ASP.NET Core aplikacji, które domyślnie rejestruje kontekst jako usługę *objętą zakresem* . W Blazor Server aplikacjach może to być problematyczne, ponieważ wystąpienie jest udostępniane między składnikami w ramach obwodu użytkownika. <xref:Microsoft.EntityFrameworkCore.DbContext> nie jest bezpieczny dla wątków i nie jest przeznaczony do współbieżnego użycia. Istniejące okresy istnienia są nieodpowiednie z następujących powodów:

* **Pojedyncze** udziały są w stanie wszyscy użytkownicy aplikacji i potencjalni klienci mają nieodpowiednie współbieżne użycie.
* **Zakres** (wartość domyślna) stanowi podobny problem między składnikami dla tego samego użytkownika.
* **Przejściowe** wyniki w nowym wystąpieniu na żądanie; Jednak w przypadku, gdy składniki mogą być długotrwałe, wynik może być dłuższy niż w przypadku, gdy jest to możliwe.

Poniższe zalecenia zaprojektowano w celu zapewnienia spójnego podejścia do korzystania z EF Core w Blazor Server aplikacjach.

* Domyślnie należy rozważyć użycie jednego kontekstu dla operacji. Kontekst jest przeznaczony do szybkiego i niskiego tworzenia wystąpienia:

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* Użyj flagi, aby zapobiec wielu współbieżnych operacjach:

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

  Umieść operacje po `Loading = true;` wierszu w `try` bloku.

* W przypadku długotrwałych operacji, które korzystają z [funkcji śledzenia zmian](/ef/core/querying/tracking) EF Core lub [kontroli współbieżności](/ef/core/saving/concurrency), należy [ograniczyć kontekst do okresu istnienia składnika](#scope-to-the-component-lifetime-3x).

<h3 id="new-dbcontext-instances-3x">Nowe wystąpienia DbContext</h3>

Najszybszą metodą tworzenia nowego <xref:Microsoft.EntityFrameworkCore.DbContext> wystąpienia jest `new` utworzenie nowego wystąpienia przy użyciu programu. Istnieje jednak kilka scenariuszy, które mogą wymagać rozwiązania dodatkowych zależności. Na przykład możesz użyć, [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) Aby skonfigurować kontekst.

Zalecanym rozwiązaniem do utworzenia nowego <xref:Microsoft.EntityFrameworkCore.DbContext> z zależności jest użycie fabryki. Przykładowa aplikacja implementuje własną fabrykę w programie `Data/DbContextFactory.cs` .

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

W poprzedniej fabryce:

* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> spełnia wszelkie zależności za pośrednictwem dostawcy usług.
* `IDbContextFactory` jest dostępny w EF Core ASP.NET Core 5,0 lub nowszej, więc interfejs jest [implementowany w przykładowej aplikacji dla ASP.NET Core 3. x](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).

Poniższy przykład umożliwia skonfigurowanie [oprogramowania SQLite](https://www.sqlite.org/index.html) i włączenie rejestrowania danych. Kod używa metody rozszerzającej, aby skonfigurować fabrykę bazy danych dla opcji DI i udostępnić opcje domyślne:

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

Fabryka jest wstrzykiwana do składników i używana do tworzenia nowych wystąpień. Na przykład w `Pages/Index.razor` :

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> `Wrapper` jest [odwołaniem](xref:blazor/components/index#capture-references-to-components) do składnika `GridWrapper` . Zobacz `Index` składnik ( `Pages/Index.razor` ) w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).

Nowe <xref:Microsoft.EntityFrameworkCore.DbContext> wystąpienia można utworzyć przy użyciu fabryki, która umożliwia skonfigurowanie parametrów połączenia na, na przykład `DbContext` w przypadku używania modelu [ASP.NET Core Identity ]) (linki XREF: Security/Authentication/customize_identity_model):

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-3x">Zakres czasu istnienia składnika</h3>

Można utworzyć element <xref:Microsoft.EntityFrameworkCore.DbContext> , który istnieje w okresie istnienia składnika. Dzięki temu można używać go jako [jednostki pracy](https://martinfowler.com/eaaCatalog/unitOfWork.html) i korzystać z wbudowanych funkcji, takich jak śledzenie zmian i rozwiązywanie współbieżności.
Możesz użyć fabryki, aby utworzyć kontekst i śledzić go na okres istnienia składnika. Najpierw Zaimplementuj <xref:System.IDisposable> i wsuń fabrykę, jak pokazano w `Pages/EditContact.razor` :

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

Aplikacja Przykładowa gwarantuje, że kontekst zostanie usunięty po usunięciu składnika:

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

Na koniec [`OnInitializedAsync`](xref:blazor/components/lifecycle) jest zastępowany w celu utworzenia nowego kontekstu. W przykładowej aplikacji [`OnInitializedAsync`](xref:blazor/components/lifecycle) ładuje kontakt w tej samej metodzie:

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

W powyższym przykładzie:

* Gdy `Busy` jest ustawiona na `true` , mogą zaczynać się operacje asynchroniczne. Gdy `Busy` jest ustawiona z powrotem `false` , operacje asynchroniczne powinny zostać zakończone.
* Umieść dodatkową logikę obsługi błędów w `catch` bloku.

<h3 id="enable-sensitive-data-logging">Włącz rejestrowanie danych poufnych</h3>

<xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> zawiera dane aplikacji w komunikatach o wyjątkach i rejestrowaniu struktury. Zarejestrowane dane mogą zawierać wartości przypisane do właściwości wystąpień jednostek i wartości parametrów dla poleceń wysyłanych do bazy danych. Rejestrowanie danych w programie <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> stanowi **zagrożenie bezpieczeństwa**, ponieważ może uwidaczniać hasła i inne dane osobowe, gdy rejestruje instrukcje SQL wykonywane względem bazy danych.

Zalecamy włączenie tylko <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> do celów deweloperskich i testowych:

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

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Dokumentacja EF Core](/ef/)
