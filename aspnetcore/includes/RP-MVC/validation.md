---
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
ms.openlocfilehash: c96c5e66d2e15db03c321d239a64b98119a7543a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552329"
---
<!-- USED in RP and MVC tutorial -->

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="c0437-101">Dodawanie reguł walidacji do modelu filmu</span><span class="sxs-lookup"><span data-stu-id="c0437-101">Add validation rules to the movie model</span></span>

<span data-ttu-id="c0437-102">Przestrzeń nazw DataAnnotations zawiera zestaw wbudowanych atrybutów walidacji, które są stosowane deklaratywnie do klasy lub właściwości.</span><span class="sxs-lookup"><span data-stu-id="c0437-102">The DataAnnotations namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="c0437-103">Adnotacje DataAnnotation zawierają również atrybuty formatowania, takie jak `DataType` Pomoc dotycząca formatowania i nie zapewniają weryfikacji.</span><span class="sxs-lookup"><span data-stu-id="c0437-103">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="c0437-104">Zaktualizuj `Movie` klasę, aby skorzystać z wbudowanych `Required` `StringLength` atrybutów,, `RegularExpression` i `Range` walidacji.</span><span class="sxs-lookup"><span data-stu-id="c0437-104">Update the `Movie` class to take advantage of the built-in `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="c0437-105">Atrybuty walidacji określają zachowanie, które chcesz wymusić na właściwościach modelu, do których są stosowane:</span><span class="sxs-lookup"><span data-stu-id="c0437-105">The validation attributes specify behavior that you want to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="c0437-106">`Required`Atrybuty i `MinimumLength` wskazują, że właściwość musi mieć wartość, ale nic nie zapobiega wprowadzaniu przez użytkownika odstępu w celu zaspokojenia tej walidacji.</span><span class="sxs-lookup"><span data-stu-id="c0437-106">The `Required` and `MinimumLength` attributes indicate that a property must have a value; but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="c0437-107">Ten `RegularExpression` atrybut służy do ograniczania, jakie znaki mogą być wprowadzane.</span><span class="sxs-lookup"><span data-stu-id="c0437-107">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="c0437-108">W poprzednim kodzie "gatunek":</span><span class="sxs-lookup"><span data-stu-id="c0437-108">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="c0437-109">Należy używać tylko liter.</span><span class="sxs-lookup"><span data-stu-id="c0437-109">Must only use letters.</span></span>
  * <span data-ttu-id="c0437-110">Pierwsza litera musi być wielką literą.</span><span class="sxs-lookup"><span data-stu-id="c0437-110">The first letter is required to be uppercase.</span></span> <span data-ttu-id="c0437-111">Odstępy, cyfry i znaki specjalne są niedozwolone.</span><span class="sxs-lookup"><span data-stu-id="c0437-111">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="c0437-112">`RegularExpression`"Ocena":</span><span class="sxs-lookup"><span data-stu-id="c0437-112">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="c0437-113">Wymaga, aby pierwszy znak był wielką literą.</span><span class="sxs-lookup"><span data-stu-id="c0437-113">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="c0437-114">Zezwala na znaki specjalne i cyfry w kolejnych odstępach.</span><span class="sxs-lookup"><span data-stu-id="c0437-114">Allows special characters and numbers in  subsequent spaces.</span></span> <span data-ttu-id="c0437-115">"PG-13" jest prawidłowy dla oceny, ale kończy się niepowodzeniem dla "gatunku".</span><span class="sxs-lookup"><span data-stu-id="c0437-115">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="c0437-116">Atrybut `Range` ogranicza wartość do określonego zakresu.</span><span class="sxs-lookup"><span data-stu-id="c0437-116">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="c0437-117">Ten `StringLength` atrybut pozwala ustawić maksymalną długość właściwości ciągu i opcjonalnie jej długość minimalną.</span><span class="sxs-lookup"><span data-stu-id="c0437-117">The `StringLength` attribute lets you set the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="c0437-118">Typy wartości (takie jak `decimal` ,,, `int` `float` `DateTime` ) są z założenia wymagane i nie wymagają `[Required]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="c0437-118">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="c0437-119">Automatyczne Wymuszanie reguł sprawdzania poprawności przez ASP.NET Core pomaga zwiększyć niezawodność aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c0437-119">Having validation rules automatically enforced by ASP.NET Core helps make your app more robust.</span></span> <span data-ttu-id="c0437-120">Gwarantuje to również, że nie można zapomnieć, aby zweryfikować coś i przypadkowo umożliwić niewłaściwe dane w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="c0437-120">It also ensures that you can't forget to validate something and inadvertently let bad data into the database.</span></span>
