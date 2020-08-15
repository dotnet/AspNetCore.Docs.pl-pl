---
title: ASP.NET Core Blazor Server z Entity Framework Core (EFCore)
author: JeremyLikness
description: Wskazówki dotyczące korzystania z EF Core w Blazor Server aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
no-loc:
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
ms.openlocfilehash: 2ea702aa73a2981afc223e5c1700d6ec2dc62df4
ms.sourcegitcommit: 503b348e9046fcd969de85898394a1ea8274ec38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227797"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a>ASP.NET Core Blazor Server z Entity Framework Core (EFCore)

Autor: [Jeremy Likness](https://github.com/JeremyLikness)

:::moniker range=">= aspnetcore-5.0"

Blazor Server jest platformą aplikacji stanowych. Aplikacja utrzymuje ciągłe połączenie z serwerem, a stan użytkownika jest przechowywany w pamięci serwera w ramach *obwodu*. Przykładem stanu użytkownika są dane przechowywane w wystąpieniach usługi [wtrysku zależności (di)](xref:fundamentals/dependency-injection) , które są objęte zakresem obwodu. Unikatowy model aplikacji, który Blazor Server zapewnia, wymaga specjalnego podejścia do korzystania z Entity Framework Core. 

> [!NOTE]
> Ten artykuł zawiera adresy EF Core w Blazor Server aplikacjach. Blazor WebAssembly aplikacje działają w piaskownicy zestawu webassembly, które uniemożliwiają większość bezpośrednich połączeń z bazą danych. Uruchamianie EF Core w programie Blazor WebAssembly wykracza poza zakres tego artykułu.

## <a name="sample-app"></a>Przykładowa aplikacja

Przykładowa aplikacja została skompilowana jako odwołanie do Blazor Server aplikacji korzystających EF Core. Przykładowa aplikacja zawiera siatkę z operacjami sortowania i filtrowania, usuwania, dodawania i aktualizowania. Przykład ilustruje użycie EF Core do obsługi optymistycznej współbieżności.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykład korzysta z lokalnej bazy danych programu [SQLite](https://www.sqlite.org/index.html) , aby można go było używać na dowolnej platformie. Przykład umożliwia również skonfigurowanie rejestrowania bazy danych w celu wyświetlenia wygenerowanych zapytań SQL. Ta konfiguracja jest skonfigurowana w `appsettings.Development.json` :

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)] 

Składniki Grid, Add i View używają wzorca "context-per-Operation", który tworzy kontekst dla każdej operacji. Składnik edycyjny używa wzorca "kontekst-składnik", gdzie dla każdego składnika jest tworzony kontekst.

## <a name="database-access"></a>Dostęp do bazy danych

EF Core opiera się na <xref:Microsoft.EntityFrameworkCore.DbContext> metodzie [konfigurowania dostępu do bazy danych](/ef/core/miscellaneous/configuring-dbcontext) i działania jako [_jednostki pracy_](https://martinfowler.com/eaaCatalog/unitOfWork.html). EF Core zapewnia <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> rozszerzenie dla ASP.NET Core aplikacji, które domyślnie rejestruje kontekst jako usługę _objętą zakresem_ . W Blazor Server aplikacjach usługa rejestracji w zakresie może być problematyczna, ponieważ wystąpienie jest udostępniane między składnikami w ramach obwodu użytkownika. <xref:Microsoft.EntityFrameworkCore.DbContext> nie jest bezpieczny dla wątków i nie jest przeznaczony do współbieżnego użycia. Istniejące okresy istnienia są nieodpowiednie z następujących powodów:

* **Pojedyncze** udziały są w stanie wszyscy użytkownicy aplikacji i potencjalni klienci mają nieodpowiednie współbieżne użycie.
* **Zakres** (wartość domyślna) stanowi podobny problem między składnikami dla tego samego użytkownika.
* **Przejściowe** wyniki w nowym wystąpieniu na żądanie; Jednak w przypadku, gdy składniki mogą być długotrwałe, wynik może być dłuższy niż w przypadku, gdy jest to możliwe.

Poniższe zalecenia zaprojektowano w celu zapewnienia spójnego podejścia do korzystania z EF Core w Blazor Server aplikacjach. 

* Domyślnie należy rozważyć użycie jednego kontekstu dla operacji. Kontekst jest przeznaczony do szybkiego i niskiego tworzenia wystąpienia:

  ```csharp
  var using context = new MyContext();

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
      // operations go here
  }
  finally 
  {
      Loading = false;
  }
  ```

* W przypadku długotrwałych operacji, które korzystają z [funkcji śledzenia zmian](/ef/core/querying/tracking) EF Core lub [kontroli współbieżności](/ef/core/saving/concurrency), należy [ograniczyć kontekst do okresu istnienia składnika](#scope-to-the-component-lifetime).

### <a name="new-dbcontext-instances"></a>Nowe wystąpienia DbContext

Najszybszą metodą tworzenia nowego <xref:Microsoft.EntityFrameworkCore.DbContext> wystąpienia jest `new` utworzenie nowego wystąpienia przy użyciu programu. Istnieje jednak kilka scenariuszy, które mogą wymagać rozwiązania dodatkowych zależności. Na przykład możesz użyć, [`DbContextOptions`](https://docs.microsoft.com/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) Aby skonfigurować kontekst. 

Zalecanym rozwiązaniem do utworzenia nowego <xref:Microsoft.EntityFrameworkCore.DbContext> z zależności jest użycie fabryki. EF Core 5,0 lub nowszy zapewnia wbudowaną fabrykę do tworzenia nowych kontekstów.

Poniższy przykład umożliwia skonfigurowanie [oprogramowania SQLite](https://www.sqlite.org/index.html) i włączenie rejestrowania danych. Kod używa metody rozszerzającej, aby skonfigurować fabrykę bazy danych dla opcji DI i udostępnić opcje domyślne:

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?range=29-31)]

Fabryka jest wstrzykiwana do składników i używana do tworzenia nowych wystąpień. Na przykład w `Pages/Index.razor` :

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?range=199-212)]

### <a name="scope-to-the-component-lifetime"></a>Zakres czasu istnienia składnika

Można utworzyć element <xref:Microsoft.EntityFrameworkCore.DbContext> , który istnieje w okresie istnienia składnika. Dzięki temu można używać go jako [jednostki pracy](https://martinfowler.com/eaaCatalog/unitOfWork.html) i korzystać z wbudowanych funkcji, takich jak śledzenie zmian i rozwiązywanie współbieżności.
Możesz użyć fabryki, aby utworzyć kontekst i śledzić go na okres istnienia składnika. Najpierw Zaimplementuj <xref:System.IDisposable> i wsuń fabrykę, jak pokazano w `Pages/EditContact.razor` :

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=5-7)]

Aplikacja Przykładowa gwarantuje, że kontakt zostanie usunięty po usunięciu składnika:

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=181-184)]

Na koniec `OnInitializedAsync` jest zastępowany w celu utworzenia nowego kontekstu. W przykładowej aplikacji `OnInitializedAsync` ładuje kontakt w tej samej metodzie:

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=89-104)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

Blazor Server jest platformą aplikacji stanowych. Aplikacja utrzymuje ciągłe połączenie z serwerem, a stan użytkownika jest przechowywany w pamięci serwera w ramach *obwodu*. Przykładem stanu użytkownika są dane przechowywane w wystąpieniach usługi [wtrysku zależności (di)](xref:fundamentals/dependency-injection) , które są objęte zakresem obwodu. Unikatowy model aplikacji, który Blazor Server zapewnia, wymaga specjalnego podejścia do korzystania z Entity Framework Core. 

> [!NOTE]
> Ten artykuł zawiera adresy EF Core w Blazor Server aplikacjach. Blazor WebAssembly aplikacje działają w piaskownicy zestawu webassembly, które uniemożliwiają większość bezpośrednich połączeń z bazą danych. Uruchamianie EF Core w programie Blazor WebAssembly wykracza poza zakres tego artykułu.

## <a name="sample-app"></a>Przykładowa aplikacja

Przykładowa aplikacja została skompilowana jako odwołanie do Blazor Server aplikacji korzystających EF Core. Przykładowa aplikacja zawiera siatkę z operacjami sortowania i filtrowania, usuwania, dodawania i aktualizowania. Przykład ilustruje użycie EF Core do obsługi optymistycznej współbieżności.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykład korzysta z lokalnej bazy danych programu [SQLite](https://www.sqlite.org/index.html) , aby można go było używać na dowolnej platformie. Przykład umożliwia również skonfigurowanie rejestrowania bazy danych w celu wyświetlenia wygenerowanych zapytań SQL. Ta konfiguracja jest skonfigurowana w `appsettings.Development.json` :

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)] 

Składniki Grid, Add i View używają wzorca "context-per-Operation", który tworzy kontekst dla każdej operacji. Składnik edycyjny używa wzorca "kontekst-składnik", gdzie dla każdego składnika jest tworzony kontekst.

## <a name="database-access"></a>Dostęp do bazy danych

EF Core opiera się na <xref:Microsoft.EntityFrameworkCore.DbContext> metodzie [konfigurowania dostępu do bazy danych](/ef/core/miscellaneous/configuring-dbcontext) i działania jako [_jednostki pracy_](https://martinfowler.com/eaaCatalog/unitOfWork.html). EF Core zapewnia <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> rozszerzenie dla ASP.NET Core aplikacji, które domyślnie rejestruje kontekst jako usługę _objętą zakresem_ . W Blazor Server aplikacjach może to być problematyczne, ponieważ wystąpienie jest udostępniane między składnikami w ramach obwodu użytkownika. <xref:Microsoft.EntityFrameworkCore.DbContext> nie jest bezpieczny dla wątków i nie jest przeznaczony do współbieżnego użycia. Istniejące okresy istnienia są nieodpowiednie z następujących powodów:

* **Pojedyncze** udziały są w stanie wszyscy użytkownicy aplikacji i potencjalni klienci mają nieodpowiednie współbieżne użycie.
* **Zakres** (wartość domyślna) stanowi podobny problem między składnikami dla tego samego użytkownika.
* **Przejściowe** wyniki w nowym wystąpieniu na żądanie; Jednak w przypadku, gdy składniki mogą być długotrwałe, wynik może być dłuższy niż w przypadku, gdy jest to możliwe.

## <a name="database-access"></a>Dostęp do bazy danych

Poniższe zalecenia zaprojektowano w celu zapewnienia spójnego podejścia do korzystania z EF Core w Blazor Server aplikacjach. 

* Domyślnie należy rozważyć użycie jednego kontekstu dla operacji. Kontekst jest przeznaczony do szybkiego i niskiego tworzenia wystąpienia:

  ```csharp
  var using context = new MyContext();

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
      // operations go here
  }
  finally 
  {
      Loading = false;
  }
  ```

* W przypadku długotrwałych operacji, które korzystają z [funkcji śledzenia zmian](/ef/core/querying/tracking) EF Core lub [kontroli współbieżności](/ef/core/saving/concurrency), należy [ograniczyć kontekst do okresu istnienia składnika](#scope-to-the-component-lifetime).

### <a name="create-new-dbcontext-instances"></a>Utwórz nowe wystąpienia DbContext

Najszybszą metodą tworzenia nowego <xref:Microsoft.EntityFrameworkCore.DbContext> wystąpienia jest `new` utworzenie nowego wystąpienia przy użyciu programu. Istnieje jednak kilka scenariuszy, które mogą wymagać rozwiązania dodatkowych zależności. Na przykład możesz użyć, [`DbContextOptions`](https://docs.microsoft.com/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) Aby skonfigurować kontekst. 

Zalecanym rozwiązaniem do utworzenia nowego <xref:Microsoft.EntityFrameworkCore.DbContext> z zależności jest użycie fabryki. Przykładowa aplikacja implementuje własną fabrykę w programie `Data/DbContextFactory.cs` . 

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

Poniższy przykład umożliwia skonfigurowanie [oprogramowania SQLite](https://www.sqlite.org/index.html) i włączenie rejestrowania danych. Kod używa metody rozszerzającej, aby skonfigurować fabrykę bazy danych dla opcji DI i udostępnić opcje domyślne:

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?range=29-31)]

Fabryka jest wstrzykiwana do składników i używana do tworzenia nowych wystąpień. Na przykład w `Pages/Index.razor` :

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?range=199-212)]

### <a name="scope-to-the-component-lifetime"></a>Zakres czasu istnienia składnika

Można utworzyć element <xref:Microsoft.EntityFrameworkCore.DbContext> , który istnieje w okresie istnienia składnika. Dzięki temu można używać go jako [jednostki pracy](https://martinfowler.com/eaaCatalog/unitOfWork.html) i korzystać z wbudowanych funkcji, takich jak śledzenie zmian i rozwiązywanie współbieżności.
Możesz użyć fabryki, aby utworzyć kontekst i śledzić go na okres istnienia składnika. Najpierw Zaimplementuj <xref:System.IDisposable> i wsuń fabrykę, jak pokazano w `Pages/EditContact.razor` :

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=5-7)]

Aplikacja Przykładowa gwarantuje, że kontakt zostanie usunięty po usunięciu składnika:

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=181-184)]

Na koniec `OnInitializedAsync` jest zastępowany w celu utworzenia nowego kontekstu. W przykładowej aplikacji `OnInitializedAsync` ładuje kontakt w tej samej metodzie:

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=89-104)]

:::moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

> [Dokumentacja EF Core](/ef/)
