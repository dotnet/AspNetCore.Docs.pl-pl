---
title: ASP.NET Core i Entity Framework 6
author: rick-anderson
description: Entity Framework 6,3 i nowsze współdziałają z ASP.NET Core 3,1 i nowszymi.
ms.author: riande
ms.custom: mvc
ms.date: 7/14/2020
no-loc:
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/entity-framework-6
ms.openlocfilehash: 7e7a07a080d2feefb994396c689279545ce5432f
ms.sourcegitcommit: dfea24471f4f3d7904faa92fe60c000853bddc3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88504700"
---
# <a name="aspnet-core-and-entity-framework-6"></a>ASP.NET Core i Entity Framework 6
::: moniker range=">= aspnetcore-3.0"

Zgodnie z [dobrymi](https://github.com/attrib75)

## <a name="using-entity-framework-6-with-aspnet-core"></a>Używanie Entity Framework 6 z ASP.NET Core

[Entity Framework Core](/ef/) należy używać w przypadku nowych rozwiązań programistycznych. [Przykład pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/3.xsample) używa [Entity Framework 6 (Ef6)](/ef/ef6), który może służyć do migrowania zamykania aplikacji do ASP.NET Core.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Konfiguracja oparta na kodzie Entity Framework](/ef/ef6/fundamentals/configuring/code-based)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Paweł grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex)i [Tomasz Dykstra](https://github.com/tdykstra)

W tym artykule pokazano, jak używać Entity Framework 6 w aplikacji ASP.NET Core.    

## <a name="overview"></a>Omówienie 

Aby użyć Entity Framework 6, projekt musi zostać skompilowany w oparciu o .NET Framework, ponieważ Entity Framework 6 nie obsługuje programu .NET Core. Jeśli potrzebujesz funkcji międzyplatformowych, musisz przeprowadzić uaktualnienie do [Entity Framework Core](/ef/).  

Zalecanym sposobem użycia Entity Framework 6 w aplikacji ASP.NET Core jest umieszczenie EF6 kontekstu i klas modelu w projekcie biblioteki klas, który jest przeznaczony dla .NET Framework. Dodaj odwołanie do biblioteki klas z projektu ASP.NET Core. Zobacz przykładowe [rozwiązanie programu Visual Studio z Ef6 i projektami ASP.NET Core](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).  

Nie można umieścić kontekstu EF6 w projekcie ASP.NET Core, ponieważ projekty .NET Core nie obsługują wszystkich funkcji, które EF6 polecenia, takie jak *enable-migrations* .    

Niezależnie od typu projektu, w którym znajduje się kontekst EF6, tylko narzędzia wiersza polecenia EF6 współpracują z kontekstem EF6. Na przykład, `Scaffold-DbContext` jest dostępna tylko w Entity Framework Core. Jeśli musisz przeprowadzić odtwarzanie bazy danych do modelu EF6, zobacz [Code First do istniejącej bazy danych](https://msdn.microsoft.com/jj200620).  

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a>Odwołuje się do pełnej struktury i EF6 w projekcie ASP.NET Core 

Projekt ASP.NET Core musi kierować do .NET Framework i odwołania EF6. Na przykład plik *. csproj* projektu ASP.NET Core będzie wyglądać podobnie do poniższego przykładu (wyświetlane są tylko odpowiednie części pliku).    

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]   

Podczas tworzenia nowego projektu Użyj szablonu **aplikacji sieci Web ASP.NET Core (.NET Framework)** .    

## <a name="handle-connection-strings"></a>Obsługa parametrów połączenia    

Narzędzia wiersza polecenia EF6, które będą używane w projekcie biblioteki klas EF6, wymagają konstruktora domyślnego, aby mogły utworzyć wystąpienie kontekstu. Jednak prawdopodobnie chcesz określić parametry połączenia do użycia w projekcie ASP.NET Core, w takim przypadku Konstruktor kontekstu musi mieć parametr, który umożliwia przekazywanie parametrów połączenia. Oto przykład.   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]   

Ponieważ kontekst EF6 nie ma konstruktora bez parametrów, projekt EF6 musi dostarczyć implementację [IDbContextFactory](https://msdn.microsoft.com/library/hh506876). Narzędzia wiersza polecenia EF6 znajdą i używają tej implementacji, aby mogli utworzyć wystąpienie kontekstu. Oto przykład.    

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]  

W tym przykładowym kodzie `IDbContextFactory` implementacja przebiega w postaci zakodowanych parametrów połączenia. To są parametry połączenia, które będą używane przez narzędzia wiersza polecenia. Należy wdrożyć strategię, aby upewnić się, że Biblioteka klas używa tych samych parametrów połączenia, które są używane przez aplikację wywołującą. Można na przykład uzyskać wartość ze zmiennej środowiskowej w obu projektach.   

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a>Konfigurowanie iniekcji zależności w projekcie ASP.NET Core  

W pliku *Startup.cs* projektu podstawowego Skonfiguruj kontekst Ef6 dla iniekcji zależności (di) w `ConfigureServices` . Obiekty kontekstu EF powinny być ograniczone do zakresu okresu istnienia żądania.   

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]   

Następnie można uzyskać wystąpienie kontekstu na kontrolerach za pomocą DI. Kod jest podobny do tego, co zapisujesz w kontekście EF Core:    

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]  

## <a name="sample-application"></a>Przykładowa aplikacja   

Aby uzyskać działającą przykładową aplikację, zobacz [przykładowe rozwiązanie Visual Studio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) , które jest dołączone do tego artykułu.  

Ten przykład można utworzyć od podstaw, wykonując następujące kroki w programie Visual Studio:    

* Utwórz rozwiązanie.    

* **Dodaj** > **Nowy projekt** > **Sieć Web** > **ASP.NET Core aplikacji sieci Web**    
  * W oknie dialogowym Wybieranie szablonu projektu wybierz pozycję Interfejs API i .NET Framework na liście rozwijanej 

* **Dodaj** > **Nowy projekt** > **Pulpit** > systemu Windows **Biblioteka klas (.NET Framework)**  

* W **konsoli Menedżera pakietów** (PMC) dla obu projektów Uruchom polecenie `Install-Package Entityframework` .    

* W projekcie Biblioteka klas Utwórz klasy modelu danych i klasę kontekstu oraz implementację programu `IDbContextFactory` .    

* W polu PMC dla projektu biblioteki klas Uruchom polecenia `Enable-Migrations` i `Add-Migration Initial` . Jeśli ustawisz projekt ASP.NET Core jako projekt startowy, Dodaj `-StartupProjectName EF6` do tych poleceń. 

* W projekcie podstawowym Dodaj odwołanie projektu do projektu biblioteki klas.    

* W projekcie podstawowym w *Startup.cs*Zarejestruj kontekst dla di.    

* W projekcie podstawowym w *appsettings.jsna*, Dodaj parametry połączenia.    

* W projekcie podstawowym Dodaj kontroler i widok, aby sprawdzić, czy można odczytywać i zapisywać dane. (Należy zauważyć, że ASP.NET Core tworzenie szkieletu MVC nie będzie współpracowało z kontekstem EF6, do którego odwołuje się Biblioteka klas).

::: moniker-end
