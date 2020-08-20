---
title: Część 10, badanie szczegółów i metod usuwania aplikacji ASP.NET Core
author: rick-anderson
description: Część 10 serii samouczków na ASP.NET Core MVC.
ms.author: riande
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: b25113f8e2132c06e06d53a25e71ed2a42deb00f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629707"
---
# <a name="part-10-examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a>Część 10, badanie szczegółów i metod usuwania aplikacji ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Otwórz kontroler filmu i Przeanalizuj `Details` metodę:

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

Aparat tworzenia szkieletu MVC, który utworzył tę metodę akcji, dodaje komentarz zawierający żądanie HTTP, które wywołuje metodę. W tym przypadku jest to żądanie GET z trzema segmentami adresów URL, `Movies` kontrolerem, `Details` metodą i `id` wartością. Wycofaj te segmenty są zdefiniowane w *Startup.cs*.

[!code-csharp[](start-mvc/sample/MvcMovie3/Startup.cs?highlight=5&name=snippet_1)]

EF ułatwia wyszukiwanie danych przy użyciu `FirstOrDefaultAsync` metody. Ważna funkcja zabezpieczeń wbudowana w metodę polega na tym, że kod sprawdza, czy metoda wyszukiwania znalazła film przed podjęciem próby wykonania jakichkolwiek czynności. Na przykład haker może wprowadzić błędy do witryny przez zmianę adresu URL utworzonego przez linki z `http://localhost:{PORT}/Movies/Details/1` do czegoś takiego jak  `http://localhost:{PORT}/Movies/Details/12345` (lub innej wartości, która nie reprezentuje rzeczywistego filmu). Jeśli nie zaznaczono filmu o wartości null, aplikacja zgłosi wyjątek.

Przejrzyj `Delete` metody i `DeleteConfirmed` .

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

Należy zauważyć, że `HTTP GET Delete` Metoda nie usuwa określonego filmu, zwraca widok filmu, w którym można przesłać (HTTPPOST) usunięcie. Wykonanie operacji usuwania w odpowiedzi na żądanie GET (lub w tym przypadku wykonanie operacji edycji, operacji tworzenia lub jakiejkolwiek innej operacji, która zmienia dane) powoduje otwarcie otworu zabezpieczeń.

`[HttpPost]`Metoda, która usuwa dane, ma nazwę, `DeleteConfirmed` Aby nadać metodzie post protokołu HTTP unikatowy podpis lub nazwę. Poniżej przedstawiono dwie sygnatury metod:

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

Środowisko uruchomieniowe języka wspólnego (CLR) wymaga, aby przeciążone metody miały unikatowy podpis parametru (taka sama nazwa metody, ale inna lista parametrów). Jednak w tym miejscu wymagane `Delete` są dwie metody — jeden dla elementu get i jeden dla elementu post--oba mają taki sam podpis parametru. (Oba muszą akceptować jedną liczbę całkowitą jako parametr).

Istnieją dwa podejścia do tego problemu, jedną z nich jest nadanie metodom różnych nazw. To właśnie mechanizm tworzenia szkieletu w poprzednim przykładzie. Wprowadzamy jednak niewielki problem: ASP.NET mapuje segmenty adresu URL na metody akcji według nazwy, a jeśli zmienisz nazwę metody, routing zwykle nie będzie mógł znaleźć tej metody. To rozwiązanie jest widoczne w przykładzie, który polega na dodaniu `ActionName("Delete")` atrybutu do `DeleteConfirmed` metody. Ten atrybut wykonuje mapowanie dla systemu routingu w taki sposób, aby adres URL, który zawiera/Delete/dla żądania POST, znajdował `DeleteConfirmed` metodę.

Inna częsta obejście dla metod, które mają identyczne nazwy i podpisy, polega na sztucznej zmianie sygnatury metody POST w celu uwzględnienia dodatkowego parametru (nieużywane). To właśnie zrobiono w poprzednim wpisie po dodaniu `notUsed` parametru. Tę samą czynność można wykonać w tym miejscu dla `[HttpPost] Delete` metody:

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Aby uzyskać informacje na temat wdrażania na platformie Azure, zobacz [Samouczek: Tworzenie aplikacji internetowej platformy .NET Core i SQL Database w Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).

> [!div class="step-by-step"]
> [Poprzednie](validation.md)
