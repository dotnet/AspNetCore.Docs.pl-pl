---
title: Część 10, badanie szczegółów i metod usuwania aplikacji ASP.NET Core
author: rick-anderson
description: Część 10 serii samouczków na ASP.NET Core MVC.
ms.author: riande
ms.date: 12/13/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 49422cdfac73e586ce3a035ee79d464e9d6a6827
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405162"
---
# <a name="part-10-examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a><span data-ttu-id="76831-103">Część 10, badanie szczegółów i metod usuwania aplikacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="76831-103">Part 10, examine the Details and Delete methods of an ASP.NET Core app</span></span>

<span data-ttu-id="76831-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="76831-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="76831-105">Otwórz kontroler filmu i Przeanalizuj `Details` metodę:</span><span class="sxs-lookup"><span data-stu-id="76831-105">Open the Movie controller and examine the `Details` method:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

<span data-ttu-id="76831-106">Aparat tworzenia szkieletu MVC, który utworzył tę metodę akcji, dodaje komentarz zawierający żądanie HTTP, które wywołuje metodę.</span><span class="sxs-lookup"><span data-stu-id="76831-106">The MVC scaffolding engine that created this action method adds a comment showing an HTTP request that invokes the method.</span></span> <span data-ttu-id="76831-107">W tym przypadku jest to żądanie GET z trzema segmentami adresów URL, `Movies` kontrolerem, `Details` metodą i `id` wartością.</span><span class="sxs-lookup"><span data-stu-id="76831-107">In this case it's a GET request with three URL segments, the `Movies` controller, the `Details` method, and an `id` value.</span></span> <span data-ttu-id="76831-108">Wycofaj te segmenty są zdefiniowane w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="76831-108">Recall these segments are defined in *Startup.cs*.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie3/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="76831-109">EF ułatwia wyszukiwanie danych przy użyciu `FirstOrDefaultAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="76831-109">EF makes it easy to search for data using the `FirstOrDefaultAsync` method.</span></span> <span data-ttu-id="76831-110">Ważna funkcja zabezpieczeń wbudowana w metodę polega na tym, że kod sprawdza, czy metoda wyszukiwania znalazła film przed podjęciem próby wykonania jakichkolwiek czynności.</span><span class="sxs-lookup"><span data-stu-id="76831-110">An important security feature built into the method is that the code verifies that the search method has found a movie before it tries to do anything with it.</span></span> <span data-ttu-id="76831-111">Na przykład haker może wprowadzić błędy do witryny przez zmianę adresu URL utworzonego przez linki z `http://localhost:{PORT}/Movies/Details/1` do czegoś takiego jak `http://localhost:{PORT}/Movies/Details/12345` (lub innej wartości, która nie reprezentuje rzeczywistego filmu).</span><span class="sxs-lookup"><span data-stu-id="76831-111">For example, a hacker could introduce errors into the site by changing the URL created by the links from `http://localhost:{PORT}/Movies/Details/1` to something like  `http://localhost:{PORT}/Movies/Details/12345` (or some other value that doesn't represent an actual movie).</span></span> <span data-ttu-id="76831-112">Jeśli nie zaznaczono filmu o wartości null, aplikacja zgłosi wyjątek.</span><span class="sxs-lookup"><span data-stu-id="76831-112">If you didn't check for a null movie, the app would throw an exception.</span></span>

<span data-ttu-id="76831-113">Przejrzyj `Delete` metody i `DeleteConfirmed` .</span><span class="sxs-lookup"><span data-stu-id="76831-113">Examine the `Delete` and `DeleteConfirmed` methods.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

<span data-ttu-id="76831-114">Należy zauważyć, że `HTTP GET Delete` Metoda nie usuwa określonego filmu, zwraca widok filmu, w którym można przesłać (HTTPPOST) usunięcie.</span><span class="sxs-lookup"><span data-stu-id="76831-114">Note that the `HTTP GET Delete` method doesn't delete the specified movie, it returns a view of the movie where you can submit (HttpPost) the deletion.</span></span> <span data-ttu-id="76831-115">Wykonanie operacji usuwania w odpowiedzi na żądanie GET (lub w tym przypadku wykonanie operacji edycji, operacji tworzenia lub jakiejkolwiek innej operacji, która zmienia dane) powoduje otwarcie otworu zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="76831-115">Performing a delete operation in response to a GET request (or for that matter, performing an edit operation, create operation, or any other operation that changes data) opens up a security hole.</span></span>

<span data-ttu-id="76831-116">`[HttpPost]`Metoda, która usuwa dane, ma nazwę, `DeleteConfirmed` Aby nadać metodzie post protokołu HTTP unikatowy podpis lub nazwę.</span><span class="sxs-lookup"><span data-stu-id="76831-116">The `[HttpPost]` method that deletes the data is named `DeleteConfirmed` to give the HTTP POST method a unique signature or name.</span></span> <span data-ttu-id="76831-117">Poniżej przedstawiono dwie sygnatury metod:</span><span class="sxs-lookup"><span data-stu-id="76831-117">The two method signatures are shown below:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

<span data-ttu-id="76831-118">Środowisko uruchomieniowe języka wspólnego (CLR) wymaga, aby przeciążone metody miały unikatowy podpis parametru (taka sama nazwa metody, ale inna lista parametrów).</span><span class="sxs-lookup"><span data-stu-id="76831-118">The common language runtime (CLR) requires overloaded methods to have a unique parameter signature (same method name but different list of parameters).</span></span> <span data-ttu-id="76831-119">Jednak w tym miejscu wymagane `Delete` są dwie metody — jeden dla elementu get i jeden dla elementu post--oba mają taki sam podpis parametru.</span><span class="sxs-lookup"><span data-stu-id="76831-119">However, here you need two `Delete` methods -- one for GET and one for POST -- that both have the same parameter signature.</span></span> <span data-ttu-id="76831-120">(Oba muszą akceptować jedną liczbę całkowitą jako parametr).</span><span class="sxs-lookup"><span data-stu-id="76831-120">(They both need to accept a single integer as a parameter.)</span></span>

<span data-ttu-id="76831-121">Istnieją dwa podejścia do tego problemu, jedną z nich jest nadanie metodom różnych nazw.</span><span class="sxs-lookup"><span data-stu-id="76831-121">There are two approaches to this problem, one is to give the methods different names.</span></span> <span data-ttu-id="76831-122">To właśnie mechanizm tworzenia szkieletu w poprzednim przykładzie.</span><span class="sxs-lookup"><span data-stu-id="76831-122">That's what the scaffolding mechanism did in the preceding example.</span></span> <span data-ttu-id="76831-123">Wprowadzamy jednak niewielki problem: ASP.NET mapuje segmenty adresu URL na metody akcji według nazwy, a jeśli zmienisz nazwę metody, routing zwykle nie będzie mógł znaleźć tej metody.</span><span class="sxs-lookup"><span data-stu-id="76831-123">However, this introduces a small problem: ASP.NET maps segments of a URL to action methods by name, and if you rename a method, routing normally wouldn't be able to find that method.</span></span> <span data-ttu-id="76831-124">To rozwiązanie jest widoczne w przykładzie, który polega na dodaniu `ActionName("Delete")` atrybutu do `DeleteConfirmed` metody.</span><span class="sxs-lookup"><span data-stu-id="76831-124">The solution is what you see in the example, which is to add the `ActionName("Delete")` attribute to the `DeleteConfirmed` method.</span></span> <span data-ttu-id="76831-125">Ten atrybut wykonuje mapowanie dla systemu routingu w taki sposób, aby adres URL, który zawiera/Delete/dla żądania POST, znajdował `DeleteConfirmed` metodę.</span><span class="sxs-lookup"><span data-stu-id="76831-125">That attribute performs mapping for the routing system so that a URL that includes /Delete/ for a POST request will find the `DeleteConfirmed` method.</span></span>

<span data-ttu-id="76831-126">Inna częsta obejście dla metod, które mają identyczne nazwy i podpisy, polega na sztucznej zmianie sygnatury metody POST w celu uwzględnienia dodatkowego parametru (nieużywane).</span><span class="sxs-lookup"><span data-stu-id="76831-126">Another common work around for methods that have identical names and signatures is to artificially change the signature of the POST method to include an extra (unused) parameter.</span></span> <span data-ttu-id="76831-127">To właśnie zrobiono w poprzednim wpisie po dodaniu `notUsed` parametru.</span><span class="sxs-lookup"><span data-stu-id="76831-127">That's what we did in a previous post when we added the `notUsed` parameter.</span></span> <span data-ttu-id="76831-128">Tę samą czynność można wykonać w tym miejscu dla `[HttpPost] Delete` metody:</span><span class="sxs-lookup"><span data-stu-id="76831-128">You could do the same thing here for the `[HttpPost] Delete` method:</span></span>

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a><span data-ttu-id="76831-129">Publikowanie na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="76831-129">Publish to Azure</span></span>

<span data-ttu-id="76831-130">Aby uzyskać informacje na temat wdrażania na platformie Azure, zobacz [Samouczek: Tworzenie aplikacji internetowej platformy .NET Core i SQL Database w Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span><span class="sxs-lookup"><span data-stu-id="76831-130">For information on deploying to Azure, see [Tutorial: Build a .NET Core and SQL Database web app in Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="76831-131">Poprzednie</span><span class="sxs-lookup"><span data-stu-id="76831-131">Previous</span></span>](validation.md)
