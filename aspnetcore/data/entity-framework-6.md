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
# <a name="get-started-with-aspnet-core-and-entity-framework-6"></a>Wprowadzenie do ASP.NET Core i Entity Framework 6

[Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex)i [Tom Dykstra](https://github.com/tdykstra)

W tym artykule pokazano, jak używać entity framework 6 w aplikacji ASP.NET Core.

## <a name="overview"></a>Omówienie

Aby użyć entity framework 6, projekt musi skompilować względem .NET Framework, jako Entity Framework 6 nie obsługuje .NET Core. Jeśli potrzebujesz funkcji międzyplatformowych, musisz uaktualnić do [entity framework core](/ef/).

Zalecanym sposobem użycia entity framework 6 w aplikacji ASP.NET Core jest umieszczenie kontekstu EF6 i klas modelu w projekcie biblioteki klas, który jest przeznaczony dla platformy .NET Framework. Dodaj odwołanie do biblioteki klas z projektu ASP.NET Core. Zobacz przykładowe [rozwiązanie programu Visual Studio z ef6 i ASP.NET podstawowych projektów.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/)

Nie można umieścić kontekstu EF6 w projekcie ASP.NET Core, ponieważ projekty .NET Core nie obsługują wszystkich funkcji wymaganych przez polecenia EF6, takie jak *Włącz migracje.*

Niezależnie od typu projektu, w którym można zlokalizować kontekst EF6, tylko narzędzia wiersza polecenia EF6 pracy z kontekstu EF6. Na przykład `Scaffold-DbContext` jest dostępna tylko w entity framework core. Jeśli chcesz wykonać inżynierię odwrotną bazy danych w modelu EF6, zobacz [Code First to an Existing Database](https://msdn.microsoft.com/jj200620).

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a>Odniesienie do pełnych ram i EF6 w projekcie ASP.NET Core

Twój projekt ASP.NET Core musi być ukierunkowany na program .NET Framework i odwoływać się do systemu EF6. Na przykład plik *csproj* projektu ASP.NET Core będzie wyglądał podobnie do poniższego przykładu (wyświetlane są tylko odpowiednie części pliku).

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]

Podczas tworzenia nowego projektu należy użyć szablonu **ASP.NET Core Web Application (.NET Framework).**

## <a name="handle-connection-strings"></a>Obsługa ciągów połączeń

Narzędzia wiersza polecenia EF6, które będą używane w projekcie biblioteki klas EF6 wymagają domyślnego konstruktora, aby mogły utworzyć wystąpienie kontekstu. Ale prawdopodobnie należy określić parametry połączenia do użycia w projekcie ASP.NET Core, w którym to przypadku konstruktor kontekstu musi mieć parametr, który umożliwia przekazywanie w ciągu połączenia. Oto przykład.

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]

Ponieważ kontekst EF6 nie ma konstruktora bez parametrów, projekt EF6 musi zapewnić implementację [IDbContextFactory](https://msdn.microsoft.com/library/hh506876). Narzędzia wiersza polecenia EF6 znajdzie i użyje tej implementacji, dzięki czemu można utworzyć wystąpienia kontekstu. Oto przykład.

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]

W tym przykładowym `IDbContextFactory` kodzie implementacja przechodzi w ciąg połączenia zakodowane. Jest to ciąg połączenia, który będą używane przez narzędzia wiersza polecenia. Należy zaimplementować strategię, aby upewnić się, że biblioteka klas używa tego samego ciągu połączenia, który używa aplikacji wywołującej. Na przykład można uzyskać wartość ze zmiennej środowiskowej w obu projektach.

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a>Konfigurowanie iniekcji zależności w projekcie ASP.NET Core

W pliku *Startup.cs* projektu Core skonfiguruj kontekst EF6 dla iniekcji zależności (DI) w programie `ConfigureServices`. Obiekty kontekstu EF powinny być objęte zakresem dla okresu istnienia żądania.

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

Następnie można uzyskać wystąpienie kontekstu w kontrolerach przy użyciu DI. Kod jest podobny do tego, co chcesz napisać w kontekście EF Core:

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]

## <a name="sample-application"></a>Przykładowa aplikacja

Dla pracy przykładowej aplikacji, zobacz [przykładowe rozwiązanie programu Visual Studio,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) który towarzyszy w tym artykule.

Ten przykład można utworzyć od podstaw przez następujące kroki w programie Visual Studio:

* Tworzenie rozwiązania.

* **Dodawanie** > **nowej aplikacji** > sieci**Web** > ASP.NET**Core projektu**
  * W oknie dialogowym wyboru szablonu projektu wybierz interfejs API i program .NET Framework w menu rozwijanym

* **Dodawanie** > nowej biblioteki klas**pulpitu** > systemu Windows > **(.NET Framework)** **New Project**

* W **konsoli Menedżera pakietów** (PMC) dla `Install-Package Entityframework`obu projektów uruchom polecenie .

* W projekcie biblioteki klas utwórz klasy modelu danych i `IDbContextFactory`klasę kontekstu oraz implementację programu .

* W pmc dla projektu biblioteki klas, uruchom polecenia `Enable-Migrations` i `Add-Migration Initial`. Jeśli projekt ASP.NET Core został ustawiony jako projekt `-StartupProjectName EF6` startowy, dodaj do tych poleceń.

* W projekcie Core dodaj odwołanie do projektu biblioteki klas.

* W projekcie Core, w *Startup.cs*, zarejestruj kontekst dla DI.

* W projekcie Core w *pliku appsettings.json*dodaj parametry połączenia.

* W projekcie Core dodaj kontroler i widoky, aby sprawdzić, czy można odczytywać i zapisywać dane. (Należy zauważyć, że ASP.NET rdzenia MVC rusztowania nie będzie działać z kontekstu EF6 odwołuje się z biblioteki klas.)

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono podstawowe wskazówki dotyczące korzystania z entity framework 6 w aplikacji ASP.NET Core.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Struktura jednostek — konfiguracja oparta na kodzie](https://msdn.microsoft.com/data/jj680699.aspx)
