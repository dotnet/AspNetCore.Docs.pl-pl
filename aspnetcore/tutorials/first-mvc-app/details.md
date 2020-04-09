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
# <a name="examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a><span data-ttu-id="da664-103">Sprawdź metody Szczegóły i Usuwanie aplikacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="da664-103">Examine the Details and Delete methods of an ASP.NET Core app</span></span>

<span data-ttu-id="da664-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="da664-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="da664-105">Otwórz kontroler movie i `Details` sprawdź metodę:</span><span class="sxs-lookup"><span data-stu-id="da664-105">Open the Movie controller and examine the `Details` method:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

<span data-ttu-id="da664-106">Aparat szkieletu MVC, który utworzył tę metodę akcji dodaje komentarz przedstawiający żądanie HTTP, który wywołuje metodę.</span><span class="sxs-lookup"><span data-stu-id="da664-106">The MVC scaffolding engine that created this action method adds a comment showing an HTTP request that invokes the method.</span></span> <span data-ttu-id="da664-107">W takim przypadku jest to żądanie GET z `Movies` trzema `Details` segmentami adresu `id` URL, kontrolerem, metodą i wartością.</span><span class="sxs-lookup"><span data-stu-id="da664-107">In this case it's a GET request with three URL segments, the `Movies` controller, the `Details` method, and an `id` value.</span></span> <span data-ttu-id="da664-108">Przypomnijmy, że te segmenty są zdefiniowane w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="da664-108">Recall these segments are defined in *Startup.cs*.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie3/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="da664-109">EF ułatwia wyszukiwanie danych przy `FirstOrDefaultAsync` użyciu metody.</span><span class="sxs-lookup"><span data-stu-id="da664-109">EF makes it easy to search for data using the `FirstOrDefaultAsync` method.</span></span> <span data-ttu-id="da664-110">Ważną funkcją zabezpieczeń wbudowaną w metodę jest to, że kod sprawdza, czy metoda wyszukiwania znalazła film, zanim spróbuje coś z nim zrobić.</span><span class="sxs-lookup"><span data-stu-id="da664-110">An important security feature built into the method is that the code verifies that the search method has found a movie before it tries to do anything with it.</span></span> <span data-ttu-id="da664-111">Na przykład haker może wprowadzić błędy w witrynie, zmieniając adres `http://localhost:{PORT}/Movies/Details/1` URL `http://localhost:{PORT}/Movies/Details/12345` utworzony przez linki na coś podobnego (lub inną wartość, która nie reprezentuje rzeczywistego filmu).</span><span class="sxs-lookup"><span data-stu-id="da664-111">For example, a hacker could introduce errors into the site by changing the URL created by the links from `http://localhost:{PORT}/Movies/Details/1` to something like  `http://localhost:{PORT}/Movies/Details/12345` (or some other value that doesn't represent an actual movie).</span></span> <span data-ttu-id="da664-112">Jeśli nie sprawdź filmu null, aplikacja zda wyjątek.</span><span class="sxs-lookup"><span data-stu-id="da664-112">If you didn't check for a null movie, the app would throw an exception.</span></span>

<span data-ttu-id="da664-113">Sprawdź `Delete` i `DeleteConfirmed` metody.</span><span class="sxs-lookup"><span data-stu-id="da664-113">Examine the `Delete` and `DeleteConfirmed` methods.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

<span data-ttu-id="da664-114">Należy zauważyć, że `HTTP GET Delete` metoda nie usuwa określonego filmu, zwraca widok filmu, w którym można przesłać (HttpPost) usunięcie.</span><span class="sxs-lookup"><span data-stu-id="da664-114">Note that the `HTTP GET Delete` method doesn't delete the specified movie, it returns a view of the movie where you can submit (HttpPost) the deletion.</span></span> <span data-ttu-id="da664-115">Wykonywanie operacji usuwania w odpowiedzi na żądanie GET (lub w tym celu, wykonywanie operacji edycji, tworzenie operacji lub innej operacji, która zmienia dane) otwiera dziurę w zabezpieczeniach.</span><span class="sxs-lookup"><span data-stu-id="da664-115">Performing a delete operation in response to a GET request (or for that matter, performing an edit operation, create operation, or any other operation that changes data) opens up a security hole.</span></span>

<span data-ttu-id="da664-116">Metoda, `[HttpPost]` która usuwa dane `DeleteConfirmed` ma na imię, aby nadać metodzie HTTP POST unikatowy podpis lub nazwę.</span><span class="sxs-lookup"><span data-stu-id="da664-116">The `[HttpPost]` method that deletes the data is named `DeleteConfirmed` to give the HTTP POST method a unique signature or name.</span></span> <span data-ttu-id="da664-117">Poniżej przedstawiono dwa podpisy metod:</span><span class="sxs-lookup"><span data-stu-id="da664-117">The two method signatures are shown below:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

<span data-ttu-id="da664-118">Środowisko wykonawcze języka wspólnego (CLR) wymaga przeciążonych metod, aby mieć unikatowy podpis parametru (ta sama nazwa metody, ale inna lista parametrów).</span><span class="sxs-lookup"><span data-stu-id="da664-118">The common language runtime (CLR) requires overloaded methods to have a unique parameter signature (same method name but different list of parameters).</span></span> <span data-ttu-id="da664-119">Jednak w tym `Delete` miejscu potrzebne są dwie metody - jedna dla GET i jedna dla POST - które mają ten sam podpis parametru.</span><span class="sxs-lookup"><span data-stu-id="da664-119">However, here you need two `Delete` methods -- one for GET and one for POST -- that both have the same parameter signature.</span></span> <span data-ttu-id="da664-120">(Obaj muszą zaakceptować pojedynczą całkowitą ą jako parametr).</span><span class="sxs-lookup"><span data-stu-id="da664-120">(They both need to accept a single integer as a parameter.)</span></span>

<span data-ttu-id="da664-121">Istnieją dwa podejścia do tego problemu, jednym z nich jest nadadać metodom różne nazwy.</span><span class="sxs-lookup"><span data-stu-id="da664-121">There are two approaches to this problem, one is to give the methods different names.</span></span> <span data-ttu-id="da664-122">To, co mechanizm rusztowania zrobił w poprzednim przykładzie.</span><span class="sxs-lookup"><span data-stu-id="da664-122">That's what the scaffolding mechanism did in the preceding example.</span></span> <span data-ttu-id="da664-123">Jednak wprowadza to mały problem: ASP.NET segmentów adresu URL do metod działania według nazwy, a jeśli zmienisz nazwę metody, routing normalnie nie będzie w stanie znaleźć tej metody.</span><span class="sxs-lookup"><span data-stu-id="da664-123">However, this introduces a small problem: ASP.NET maps segments of a URL to action methods by name, and if you rename a method, routing normally wouldn't be able to find that method.</span></span> <span data-ttu-id="da664-124">Rozwiązanie jest to, co widzisz w przykładzie, który jest dodanie `ActionName("Delete")` atrybutu `DeleteConfirmed` do metody.</span><span class="sxs-lookup"><span data-stu-id="da664-124">The solution is what you see in the example, which is to add the `ActionName("Delete")` attribute to the `DeleteConfirmed` method.</span></span> <span data-ttu-id="da664-125">Ten atrybut wykonuje mapowanie dla systemu routingu, dzięki czemu adres URL zawierający /Delete/ dla żądania POST znajdzie `DeleteConfirmed` metodę.</span><span class="sxs-lookup"><span data-stu-id="da664-125">That attribute performs mapping for the routing system so that a URL that includes /Delete/ for a POST request will find the `DeleteConfirmed` method.</span></span>

<span data-ttu-id="da664-126">Innym typowym obejściem dla metod, które mają identyczne nazwy i podpisy jest sztucznie zmienić podpis METODY POST, aby uwzględnić dodatkowy (nieużywane) parametr.</span><span class="sxs-lookup"><span data-stu-id="da664-126">Another common work around for methods that have identical names and signatures is to artificially change the signature of the POST method to include an extra (unused) parameter.</span></span> <span data-ttu-id="da664-127">To, co zrobiliśmy w poprzednim poście, `notUsed` kiedy dodaliśmy parametr.</span><span class="sxs-lookup"><span data-stu-id="da664-127">That's what we did in a previous post when we added the `notUsed` parameter.</span></span> <span data-ttu-id="da664-128">Możesz zrobić to samo tutaj `[HttpPost] Delete` dla metody:</span><span class="sxs-lookup"><span data-stu-id="da664-128">You could do the same thing here for the `[HttpPost] Delete` method:</span></span>

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a><span data-ttu-id="da664-129">Publikowanie na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="da664-129">Publish to Azure</span></span>

<span data-ttu-id="da664-130">Aby uzyskać informacje na temat wdrażania na platformie Azure, zobacz [Samouczek: Tworzenie aplikacji sieci Web Core core i bazy danych SQL w usłudze Azure App Service.](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)</span><span class="sxs-lookup"><span data-stu-id="da664-130">For information on deploying to Azure, see [Tutorial: Build a .NET Core and SQL Database web app in Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="da664-131">Wstecz</span><span class="sxs-lookup"><span data-stu-id="da664-131">Previous</span></span>](validation.md)
