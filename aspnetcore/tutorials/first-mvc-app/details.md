---
title: Sprawdź metody Szczegóły i Usuwanie aplikacji ASP.NET Core
author: rick-anderson
description: Dowiedz się więcej o metodzie kontrolera szczegółów i widoku w podstawowej ASP.NET aplikacji Core MVC.
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 04eb2efa4e67d84e575580a6248d0b5b567064af
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662912"
---
# <a name="examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a>Sprawdź metody Szczegóły i Usuwanie aplikacji ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Otwórz kontroler movie i `Details` sprawdź metodę:

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

Aparat szkieletu MVC, który utworzył tę metodę akcji dodaje komentarz przedstawiający żądanie HTTP, który wywołuje metodę. W takim przypadku jest to żądanie GET z `Movies` trzema `Details` segmentami adresu `id` URL, kontrolerem, metodą i wartością. Przypomnijmy, że te segmenty są zdefiniowane w *Startup.cs*.

[!code-csharp[](start-mvc/sample/MvcMovie3/Startup.cs?highlight=5&name=snippet_1)]

EF ułatwia wyszukiwanie danych przy `FirstOrDefaultAsync` użyciu metody. Ważną funkcją zabezpieczeń wbudowaną w metodę jest to, że kod sprawdza, czy metoda wyszukiwania znalazła film, zanim spróbuje coś z nim zrobić. Na przykład haker może wprowadzić błędy w witrynie, zmieniając adres `http://localhost:{PORT}/Movies/Details/1` URL `http://localhost:{PORT}/Movies/Details/12345` utworzony przez linki na coś podobnego (lub inną wartość, która nie reprezentuje rzeczywistego filmu). Jeśli nie sprawdź filmu null, aplikacja zda wyjątek.

Sprawdź `Delete` i `DeleteConfirmed` metody.

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

Należy zauważyć, że `HTTP GET Delete` metoda nie usuwa określonego filmu, zwraca widok filmu, w którym można przesłać (HttpPost) usunięcie. Wykonywanie operacji usuwania w odpowiedzi na żądanie GET (lub w tym celu, wykonywanie operacji edycji, tworzenie operacji lub innej operacji, która zmienia dane) otwiera dziurę w zabezpieczeniach.

Metoda, `[HttpPost]` która usuwa dane `DeleteConfirmed` ma na imię, aby nadać metodzie HTTP POST unikatowy podpis lub nazwę. Poniżej przedstawiono dwa podpisy metod:

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

Środowisko wykonawcze języka wspólnego (CLR) wymaga przeciążonych metod, aby mieć unikatowy podpis parametru (ta sama nazwa metody, ale inna lista parametrów). Jednak w tym `Delete` miejscu potrzebne są dwie metody - jedna dla GET i jedna dla POST - które mają ten sam podpis parametru. (Obaj muszą zaakceptować pojedynczą całkowitą ą jako parametr).

Istnieją dwa podejścia do tego problemu, jednym z nich jest nadadać metodom różne nazwy. To, co mechanizm rusztowania zrobił w poprzednim przykładzie. Jednak wprowadza to mały problem: ASP.NET segmentów adresu URL do metod działania według nazwy, a jeśli zmienisz nazwę metody, routing normalnie nie będzie w stanie znaleźć tej metody. Rozwiązanie jest to, co widzisz w przykładzie, który jest dodanie `ActionName("Delete")` atrybutu `DeleteConfirmed` do metody. Ten atrybut wykonuje mapowanie dla systemu routingu, dzięki czemu adres URL zawierający /Delete/ dla żądania POST znajdzie `DeleteConfirmed` metodę.

Innym typowym obejściem dla metod, które mają identyczne nazwy i podpisy jest sztucznie zmienić podpis METODY POST, aby uwzględnić dodatkowy (nieużywane) parametr. To, co zrobiliśmy w poprzednim poście, `notUsed` kiedy dodaliśmy parametr. Możesz zrobić to samo tutaj `[HttpPost] Delete` dla metody:

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Aby uzyskać informacje na temat wdrażania na platformie Azure, zobacz [Samouczek: Tworzenie aplikacji sieci Web Core core i bazy danych SQL w usłudze Azure App Service.](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

> [!div class="step-by-step"]
> [Wstecz](validation.md)
