---
title: Pomocnicy tagów w ASP.NET Core
author: rick-anderson
description: Dowiedz się, czym są pomocnicy tagów i jak ich używać w ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 03/18/2019
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
uid: mvc/views/tag-helpers/intro
ms.openlocfilehash: 067698687268ac072d8944fd5d9ae47c49fd4a24
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015014"
---
# <a name="tag-helpers-in-aspnet-core"></a><span data-ttu-id="ba92b-103">Pomocnicy tagów w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ba92b-103">Tag Helpers in ASP.NET Core</span></span>

<span data-ttu-id="ba92b-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ba92b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="what-are-tag-helpers"></a><span data-ttu-id="ba92b-105">Co to są pomocnicy tagów</span><span class="sxs-lookup"><span data-stu-id="ba92b-105">What are Tag Helpers</span></span>

<span data-ttu-id="ba92b-106">Pomocnicy tagów włączają kod po stronie serwera, aby uczestniczyć w tworzeniu i renderowaniu elementów HTML w Razor plikach.</span><span class="sxs-lookup"><span data-stu-id="ba92b-106">Tag Helpers enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="ba92b-107">Na przykład wbudowana `ImageTagHelper` można dołączyć numer wersji do nazwy obrazu.</span><span class="sxs-lookup"><span data-stu-id="ba92b-107">For example, the built-in `ImageTagHelper` can append a version number to the image name.</span></span> <span data-ttu-id="ba92b-108">Za każdym razem, gdy obraz ulegnie zmianie, serwer generuje nową unikatową wersję obrazu, dzięki czemu klienci mają zapewniony dostęp do bieżącego obrazu (zamiast nieodświeżonego obrazu w pamięci podręcznej).</span><span class="sxs-lookup"><span data-stu-id="ba92b-108">Whenever the image changes, the server generates a new unique version for the image, so clients are guaranteed to get the current image (instead of a stale cached image).</span></span> <span data-ttu-id="ba92b-109">Istnieje wiele wbudowanych pomocników tagów dla typowych zadań, takich jak tworzenie formularzy, linków, ładowanie zasobów i inne — a nawet więcej dostępnych w publicznych repozytoriach GitHub i jako pakiety NuGet.</span><span class="sxs-lookup"><span data-stu-id="ba92b-109">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="ba92b-110">Pomocnicy tagów są twórcy w języku C# i są elementami docelowymi elementów HTML w oparciu o nazwę elementu, nazwę atrybutu lub tag nadrzędny.</span><span class="sxs-lookup"><span data-stu-id="ba92b-110">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="ba92b-111">Na przykład, wbudowany `LabelTagHelper` może wskazywać element HTML, `<label>` gdy `LabelTagHelper` atrybuty są stosowane.</span><span class="sxs-lookup"><span data-stu-id="ba92b-111">For example, the built-in `LabelTagHelper` can target the HTML `<label>` element when the `LabelTagHelper` attributes are applied.</span></span> <span data-ttu-id="ba92b-112">Jeśli znasz [pomocników HTML](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), pomocników tagów zmniejszają jawne przejścia między językami HTML i C# w Razor widokach.</span><span class="sxs-lookup"><span data-stu-id="ba92b-112">If you're familiar with [HTML Helpers](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), Tag Helpers reduce the explicit transitions between HTML and C# in Razor views.</span></span> <span data-ttu-id="ba92b-113">W wielu przypadkach pomocników HTML udostępniają alternatywne podejście do osoby pomagającej z określonym znacznikiem, ale ważne jest, aby rozpoznać, że pomocnicy tagów nie zastępują pomocników HTML i nie ma pomocy dla tagów dla każdego pomocnika HTML.</span><span class="sxs-lookup"><span data-stu-id="ba92b-113">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="ba92b-114">[Pomocnicy tagów w porównaniu do pomocników HTML](#tag-helpers-compared-to-html-helpers) objaśniają różnice bardziej szczegółowo.</span><span class="sxs-lookup"><span data-stu-id="ba92b-114">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span>

## <a name="what-tag-helpers-provide"></a><span data-ttu-id="ba92b-115">Jakie pomocnicy tagów dostarczają</span><span class="sxs-lookup"><span data-stu-id="ba92b-115">What Tag Helpers provide</span></span>

<span data-ttu-id="ba92b-116">**Środowisko programistyczne przyjazne dla języka HTML** W większości, Razor znaczniki przy użyciu pomocników tagów wyglądają jak standardowa html.</span><span class="sxs-lookup"><span data-stu-id="ba92b-116">**An HTML-friendly development experience** For the most part, Razor markup using Tag Helpers looks like standard HTML.</span></span> <span data-ttu-id="ba92b-117">Projektanci frontonu z obsługą języka HTML/CSS/JavaScript można edytować Razor bez uczenia składni języka C# Razor .</span><span class="sxs-lookup"><span data-stu-id="ba92b-117">Front-end designers conversant with HTML/CSS/JavaScript can edit Razor without learning C# Razor syntax.</span></span>

<span data-ttu-id="ba92b-118">**Rozbudowane środowisko IntelliSense do tworzenia kodu HTML i Razor adiustacji** jest to bardzo zróżnicowane dla pomocników HTML, poprzednie podejście do tworzenia znaczników w programie po stronie serwera w Razor widokach.</span><span class="sxs-lookup"><span data-stu-id="ba92b-118">**A rich IntelliSense environment for creating HTML and Razor markup** This is in sharp contrast to HTML Helpers, the previous approach to server-side creation of markup in Razor views.</span></span> <span data-ttu-id="ba92b-119">[Pomocnicy tagów w porównaniu do pomocników HTML](#tag-helpers-compared-to-html-helpers) objaśniają różnice bardziej szczegółowo.</span><span class="sxs-lookup"><span data-stu-id="ba92b-119">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span> <span data-ttu-id="ba92b-120">[Obsługa technologii IntelliSense dla pomocników tagów](#intellisense-support-for-tag-helpers) objaśnia środowisko IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="ba92b-120">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) explains the IntelliSense environment.</span></span> <span data-ttu-id="ba92b-121">Nawet deweloperzy z Razor składnią języka c# są wydajniejszi przy użyciu pomocników tagów niż pisanie znaczników języka c# Razor .</span><span class="sxs-lookup"><span data-stu-id="ba92b-121">Even developers experienced with Razor C# syntax are more productive using Tag Helpers than writing C# Razor markup.</span></span>

<span data-ttu-id="ba92b-122">**Sposób zwiększania produktywności i tworzenia bardziej niezawodnego, niezawodnego i możliwego do utrzymania kodu przy użyciu informacji dostępnych tylko na serwerze** Na przykład historycznie mantrą przy aktualizacji obrazów miało na celu zmianę nazwy obrazu po zmianie obrazu.</span><span class="sxs-lookup"><span data-stu-id="ba92b-122">**A way to make you more productive and able to produce more robust, reliable, and maintainable code using information only available on the server** For example, historically the mantra on updating images was to change the name of the image when you change the image.</span></span> <span data-ttu-id="ba92b-123">Obrazy powinny być agresywnie buforowane ze względu na wydajność, a jeśli nie zmienisz nazwy obrazu, jest to ryzykowne dla klientów, którzy będą otrzymywać stare kopie.</span><span class="sxs-lookup"><span data-stu-id="ba92b-123">Images should be aggressively cached for performance reasons, and unless you change the name of an image, you risk clients getting a stale copy.</span></span> <span data-ttu-id="ba92b-124">Historycznie, po edytowaniu obrazu należy zmienić nazwę i wszystkie odwołania do obrazu w aplikacji sieci Web wymagały zaktualizowania.</span><span class="sxs-lookup"><span data-stu-id="ba92b-124">Historically, after an image was edited, the name had to be changed and each reference to the image in the web app needed to be updated.</span></span> <span data-ttu-id="ba92b-125">Nie tylko jest to bardzo intensywna praca, ale jest również podatna na błędy (można pominąć odwołanie, przypadkowo wprowadzić niewłaściwy ciąg itd.) Wbudowana `ImageTagHelper` można to zrobić automatycznie.</span><span class="sxs-lookup"><span data-stu-id="ba92b-125">Not only is this very labor intensive, it's also error prone (you could miss a reference, accidentally enter the wrong string, etc.) The built-in `ImageTagHelper` can do this for you automatically.</span></span> <span data-ttu-id="ba92b-126">Do `ImageTagHelper` nazwy obrazu można dołączyć numer wersji, więc po każdym zmianie obrazu serwer automatycznie generuje nową unikatową wersję obrazu.</span><span class="sxs-lookup"><span data-stu-id="ba92b-126">The `ImageTagHelper` can append a version number to the image name, so whenever the image changes, the server automatically generates a new unique version for the image.</span></span> <span data-ttu-id="ba92b-127">Klienci mają gwarancję pobrania bieżącego obrazu.</span><span class="sxs-lookup"><span data-stu-id="ba92b-127">Clients are guaranteed to get the current image.</span></span> <span data-ttu-id="ba92b-128">Ta niezawodna oszczędność i robocizna jest ogólnie bezpłatna przy użyciu `ImageTagHelper` .</span><span class="sxs-lookup"><span data-stu-id="ba92b-128">This robustness and labor savings comes essentially free by using the `ImageTagHelper`.</span></span>

<span data-ttu-id="ba92b-129">Większość wbudowanych pomocników tagów docelowo standardowe elementy HTML i udostępniają atrybuty po stronie serwera dla elementu.</span><span class="sxs-lookup"><span data-stu-id="ba92b-129">Most built-in Tag Helpers target standard HTML elements and provide server-side attributes for the element.</span></span> <span data-ttu-id="ba92b-130">Na przykład `<input>` element użyty w wielu widokach w folderze *widoki/konto* zawiera `asp-for` atrybut.</span><span class="sxs-lookup"><span data-stu-id="ba92b-130">For example, the `<input>` element used in many views in the *Views/Account* folder contains the `asp-for` attribute.</span></span> <span data-ttu-id="ba92b-131">Ten atrybut wyodrębnia nazwę określonej właściwości modelu do renderowanego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="ba92b-131">This attribute extracts the name of the specified model property into the rendered HTML.</span></span> <span data-ttu-id="ba92b-132">Rozważ Razor Widok z następującym modelem:</span><span class="sxs-lookup"><span data-stu-id="ba92b-132">Consider a Razor view with the following model:</span></span>

```csharp
public class Movie
{
    public int ID { get; set; }
    public string Title { get; set; }
    public DateTime ReleaseDate { get; set; }
    public string Genre { get; set; }
    public decimal Price { get; set; }
}
```

<span data-ttu-id="ba92b-133">Następujące Razor znaczniki:</span><span class="sxs-lookup"><span data-stu-id="ba92b-133">The following Razor markup:</span></span>

```cshtml
<label asp-for="Movie.Title"></label>
```

<span data-ttu-id="ba92b-134">Generuje następujący kod HTML:</span><span class="sxs-lookup"><span data-stu-id="ba92b-134">Generates the following HTML:</span></span>

```html
<label for="Movie_Title">Title</label>
```

<span data-ttu-id="ba92b-135">`asp-for`Atrybut jest udostępniany przez `For` Właściwość w [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="ba92b-135">The `asp-for` attribute is made available by the `For` property in the [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0).</span></span> <span data-ttu-id="ba92b-136">Aby uzyskać więcej informacji, zobacz [pomocnicy tagów autora](xref:mvc/views/tag-helpers/authoring) .</span><span class="sxs-lookup"><span data-stu-id="ba92b-136">See [Author Tag Helpers](xref:mvc/views/tag-helpers/authoring) for more information.</span></span>

## <a name="managing-tag-helper-scope"></a><span data-ttu-id="ba92b-137">Zarządzanie zakresem pomocnika tagów</span><span class="sxs-lookup"><span data-stu-id="ba92b-137">Managing Tag Helper scope</span></span>

<span data-ttu-id="ba92b-138">Zakres pomocników tagów jest kontrolowany przez kombinację `@addTagHelper` `@removeTagHelper` znaku "!".</span><span class="sxs-lookup"><span data-stu-id="ba92b-138">Tag Helpers scope is controlled by a combination of `@addTagHelper`, `@removeTagHelper`, and the "!" opt-out character.</span></span>

<a name="add-helper-label"></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a><span data-ttu-id="ba92b-139">`@addTagHelper`udostępnia pomocników tagów</span><span class="sxs-lookup"><span data-stu-id="ba92b-139">`@addTagHelper` makes Tag Helpers available</span></span>

<span data-ttu-id="ba92b-140">Jeśli utworzysz nową aplikację sieci Web ASP.NET Core o nazwie *AuthoringTagHelpers*, do projektu zostanie dodany następujący plik *widoków/_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="ba92b-140">If you create a new ASP.NET Core web app named *AuthoringTagHelpers*, the following *Views/_ViewImports.cshtml* file will be added to your project:</span></span>

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

<span data-ttu-id="ba92b-141">`@addTagHelper`Dyrektywa powoduje, że pomocników tagów są dostępni do widoku.</span><span class="sxs-lookup"><span data-stu-id="ba92b-141">The `@addTagHelper` directive makes Tag Helpers available to the view.</span></span> <span data-ttu-id="ba92b-142">W takim przypadku plik widoku to *Pages/_ViewImports. cshtml*, które domyślnie są dziedziczone przez wszystkie pliki w folderze *stron* i podfolderach. Udostępnianie pomocników tagów.</span><span class="sxs-lookup"><span data-stu-id="ba92b-142">In this case, the view file is *Pages/_ViewImports.cshtml*, which by default is inherited by all files in the *Pages* folder and subfolders; making Tag Helpers available.</span></span> <span data-ttu-id="ba92b-143">W powyższym kodzie użyto składni wieloznacznej (" \* "), aby określić, że wszystkie pomocniki tagów w określonym zestawie (*Microsoft. AspNetCore. MVC. TagHelpers*) będą dostępne dla każdego pliku widoku w katalogu *widoków* lub podkatalogu.</span><span class="sxs-lookup"><span data-stu-id="ba92b-143">The code above uses the wildcard syntax ("\*") to specify that all Tag Helpers in the specified assembly (*Microsoft.AspNetCore.Mvc.TagHelpers*) will be available to every view file in the *Views* directory or subdirectory.</span></span> <span data-ttu-id="ba92b-144">Pierwszy parametr po `@addTagHelper` określeniu pomocników tagów do załadowania (używamy " \* " dla wszystkich pomocników tagów), a drugi parametr "Microsoft. AspNetCore. MVC. TagHelpers" określa zestaw zawierający pomocników tagów.</span><span class="sxs-lookup"><span data-stu-id="ba92b-144">The first parameter after `@addTagHelper` specifies the Tag Helpers to load (we are using "\*" for all Tag Helpers), and the second parameter "Microsoft.AspNetCore.Mvc.TagHelpers" specifies the assembly containing the Tag Helpers.</span></span> <span data-ttu-id="ba92b-145">*Microsoft. AspNetCore. MVC. TagHelpers* to zestaw wbudowanych pomocników tagów ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ba92b-145">*Microsoft.AspNetCore.Mvc.TagHelpers* is the assembly for the built-in ASP.NET Core Tag Helpers.</span></span>

<span data-ttu-id="ba92b-146">Aby uwidocznić wszystkich pomocników tagów w tym projekcie (tworząc zestaw o nazwie *AuthoringTagHelpers*), należy użyć następujących elementów:</span><span class="sxs-lookup"><span data-stu-id="ba92b-146">To expose all of the Tag Helpers in this project (which creates an assembly named *AuthoringTagHelpers*), you would use the following:</span></span>

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

<span data-ttu-id="ba92b-147">Jeśli projekt zawiera `EmailTagHelper` domyślną przestrzeń nazw ( `AuthoringTagHelpers.TagHelpers.EmailTagHelper` ), można podać w pełni kwalifikowaną nazwę (FQN) pomocnika tagów:</span><span class="sxs-lookup"><span data-stu-id="ba92b-147">If your project contains an `EmailTagHelper` with the default namespace (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), you can provide the fully qualified name (FQN) of the Tag Helper:</span></span>

```cshtml
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<span data-ttu-id="ba92b-148">Aby dodać pomocnika tagów do widoku przy użyciu FQN, należy najpierw dodać FQN ( `AuthoringTagHelpers.TagHelpers.EmailTagHelper` ), a następnie nazwę zestawu (*AuthoringTagHelpers*).</span><span class="sxs-lookup"><span data-stu-id="ba92b-148">To add a Tag Helper to a view using an FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the assembly name (*AuthoringTagHelpers*).</span></span> <span data-ttu-id="ba92b-149">Większość deweloperów preferuje użycie \* składni symbol wieloznaczny "".</span><span class="sxs-lookup"><span data-stu-id="ba92b-149">Most developers prefer to use the  "\*" wildcard syntax.</span></span> <span data-ttu-id="ba92b-150">Składnia symbol wieloznaczny umożliwia wstawianie symbolu wieloznacznego " \* " jako sufiksu w FQN.</span><span class="sxs-lookup"><span data-stu-id="ba92b-150">The wildcard syntax allows you to insert the wildcard character "\*" as the suffix in an FQN.</span></span> <span data-ttu-id="ba92b-151">Na przykład każda z poniższych dyrektyw przyniesie `EmailTagHelper` :</span><span class="sxs-lookup"><span data-stu-id="ba92b-151">For example, any of the following directives will bring in the `EmailTagHelper`:</span></span>

```cshtml
@addTagHelper AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers
```

<span data-ttu-id="ba92b-152">Jak wspomniano wcześniej, dodanie `@addTagHelper` dyrektywy do pliku *views/_ViewImports. cshtml* sprawia, że pomocnik tagów jest dostępny dla wszystkich plików widoku w katalogu *widoków* i podkatalogach.</span><span class="sxs-lookup"><span data-stu-id="ba92b-152">As mentioned previously, adding the `@addTagHelper` directive to the *Views/_ViewImports.cshtml* file makes the Tag Helper available to all view files in the *Views* directory and subdirectories.</span></span> <span data-ttu-id="ba92b-153">Możesz użyć `@addTagHelper` dyrektywy w określonych plikach widoku, jeśli chcesz, aby uwidocznić pomocnika tagów tylko w tych widokach.</span><span class="sxs-lookup"><span data-stu-id="ba92b-153">You can use the `@addTagHelper` directive in specific view files if you want to opt-in to exposing the Tag Helper to only those views.</span></span>

<a name="remove-razor-directives-label"></a>

### <a name="removetaghelper-removes-tag-helpers"></a><span data-ttu-id="ba92b-154">`@removeTagHelper`usuwa pomocników tagów</span><span class="sxs-lookup"><span data-stu-id="ba92b-154">`@removeTagHelper` removes Tag Helpers</span></span>

<span data-ttu-id="ba92b-155">`@removeTagHelper`Ma takie same dwa parametry jak `@addTagHelper` i usuwa pomocnika tagu, który został wcześniej dodany.</span><span class="sxs-lookup"><span data-stu-id="ba92b-155">The `@removeTagHelper` has the same two parameters as `@addTagHelper`, and it removes a Tag Helper that was previously added.</span></span> <span data-ttu-id="ba92b-156">Na przykład `@removeTagHelper` zastosowana do określonego widoku powoduje usunięcie określonego pomocnika tagów z widoku.</span><span class="sxs-lookup"><span data-stu-id="ba92b-156">For example, `@removeTagHelper` applied to a specific view removes the specified Tag Helper from the view.</span></span> <span data-ttu-id="ba92b-157">Użycie `@removeTagHelper` w pliku *viewss/Folder/_ViewImports. cshtml* usuwa określony pomocnik tagów ze wszystkich widoków w *folderze*.</span><span class="sxs-lookup"><span data-stu-id="ba92b-157">Using `@removeTagHelper` in a *Views/Folder/_ViewImports.cshtml* file removes the specified Tag Helper from all of the views in *Folder*.</span></span>

### <a name="controlling-tag-helper-scope-with-the-_viewimportscshtml-file"></a><span data-ttu-id="ba92b-158">Kontrolowanie zakresu pomocnika tagów przy użyciu pliku *_ViewImports. cshtml*</span><span class="sxs-lookup"><span data-stu-id="ba92b-158">Controlling Tag Helper scope with the *_ViewImports.cshtml* file</span></span>

<span data-ttu-id="ba92b-159">Można dodać plik *_ViewImports. cshtml* do dowolnego folderu widoku, a aparat widoku stosuje dyrektywy zarówno z tego pliku, jak i plików *views/_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="ba92b-159">You can add a *_ViewImports.cshtml* to any view folder, and the view engine applies the directives from both that file and the *Views/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="ba92b-160">Po dodaniu pustego pliku *views/Home/_ViewImports. cshtml* dla widoków *głównych* nie można zmienić, ponieważ plik *_ViewImports. cshtml* jest dodatkiem.</span><span class="sxs-lookup"><span data-stu-id="ba92b-160">If you added an empty *Views/Home/_ViewImports.cshtml* file for the *Home* views, there would be no change because the *_ViewImports.cshtml* file is additive.</span></span> <span data-ttu-id="ba92b-161">Wszystkie `@addTagHelper` dyrektywy dodawane do pliku *views/Home/_ViewImports. cshtml* (które nie znajdują się w pliku *widoków domyślnych/_ViewImports. cshtml* ) uwidaczniają te pomocników tagów tylko w folderze *głównym* .</span><span class="sxs-lookup"><span data-stu-id="ba92b-161">Any `@addTagHelper` directives you add to the *Views/Home/_ViewImports.cshtml* file (that are not in the default *Views/_ViewImports.cshtml* file) would expose those Tag Helpers to views only in the *Home* folder.</span></span>

<a name="opt-out"></a>

### <a name="opting-out-of-individual-elements"></a><span data-ttu-id="ba92b-162">Rezygnacja z poszczególnych elementów</span><span class="sxs-lookup"><span data-stu-id="ba92b-162">Opting out of individual elements</span></span>

<span data-ttu-id="ba92b-163">Pomocnika tagów można wyłączyć na poziomie elementu za pomocą znaku rezygnacji z pomocnika tagów ("!").</span><span class="sxs-lookup"><span data-stu-id="ba92b-163">You can disable a Tag Helper at the element level with the Tag Helper opt-out character ("!").</span></span> <span data-ttu-id="ba92b-164">Na przykład `Email` Walidacja jest wyłączona w elemencie `<span>` z znakiem rezygnacji pomocnika tagu:</span><span class="sxs-lookup"><span data-stu-id="ba92b-164">For example, `Email` validation is disabled in the `<span>` with the Tag Helper opt-out character:</span></span>

```cshtml
<!span asp-validation-for="Email" class="text-danger"></!span>
```

<span data-ttu-id="ba92b-165">Należy zastosować znak rezygnacji pomocnika tagów do otwierającego i zamykającego znacznika.</span><span class="sxs-lookup"><span data-stu-id="ba92b-165">You must apply the Tag Helper opt-out character to the opening and closing tag.</span></span> <span data-ttu-id="ba92b-166">(Edytor programu Visual Studio automatycznie dodaje znak rezygnacji do tagu zamykającego po dodaniu go do tagu otwierającego).</span><span class="sxs-lookup"><span data-stu-id="ba92b-166">(The Visual Studio editor automatically adds the opt-out character to the closing tag when you add one to the opening tag).</span></span> <span data-ttu-id="ba92b-167">Po dodaniu znaku rezygnacji atrybuty pomocnika elementu i tagu nie będą już wyświetlane w postaci charakterystycznej czcionki.</span><span class="sxs-lookup"><span data-stu-id="ba92b-167">After you add the opt-out character, the element and Tag Helper attributes are no longer displayed in a distinctive font.</span></span>

<a name="prefix-razor-directives-label"></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a><span data-ttu-id="ba92b-168">Używanie `@tagHelperPrefix` , aby utworzyć jawne użycie pomocnika tagów</span><span class="sxs-lookup"><span data-stu-id="ba92b-168">Using `@tagHelperPrefix` to make Tag Helper usage explicit</span></span>

<span data-ttu-id="ba92b-169">`@tagHelperPrefix`Dyrektywa pozwala określić ciąg prefiksu tagu w celu włączenia obsługi pomocnika tagów i zapewnienia jawnego użycia pomocnika tagów.</span><span class="sxs-lookup"><span data-stu-id="ba92b-169">The `@tagHelperPrefix` directive allows you to specify a tag prefix string to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> <span data-ttu-id="ba92b-170">Można na przykład dodać następujące znaczniki do pliku *views/_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="ba92b-170">For example, you could add the following markup to the *Views/_ViewImports.cshtml* file:</span></span>

```cshtml
@tagHelperPrefix th:
```

<span data-ttu-id="ba92b-171">W poniższym obrazie kodu prefiks pomocnika tagów jest ustawiony na `th:` , więc tylko te elementy, które używają `th:` pomocników tagów prefiksu (w przypadku elementów z obsługą pomocniczych tagów mają charakterystyczną czcionkę).</span><span class="sxs-lookup"><span data-stu-id="ba92b-171">In the code image below, the Tag Helper prefix is set to `th:`, so only those elements using the prefix `th:` support Tag Helpers (Tag Helper-enabled elements have a distinctive font).</span></span> <span data-ttu-id="ba92b-172">`<label>`Elementy i `<input>` mają prefiks pomocnika tagów i są obsługiwane przez pomocnika tagów, podczas gdy `<span>` element nie jest.</span><span class="sxs-lookup"><span data-stu-id="ba92b-172">The `<label>` and `<input>` elements have the Tag Helper prefix and are Tag Helper-enabled, while the `<span>` element doesn't.</span></span>

![image (obraz)](intro/_static/thp.png)

<span data-ttu-id="ba92b-174">Te same reguły hierarchii, które mają zastosowanie do programu, mają `@addTagHelper` również zastosowanie do `@tagHelperPrefix` .</span><span class="sxs-lookup"><span data-stu-id="ba92b-174">The same hierarchy rules that apply to `@addTagHelper` also apply to `@tagHelperPrefix`.</span></span>

## <a name="self-closing-tag-helpers"></a><span data-ttu-id="ba92b-175">Samozamykające pomocników tagów</span><span class="sxs-lookup"><span data-stu-id="ba92b-175">Self-closing Tag Helpers</span></span>

<span data-ttu-id="ba92b-176">Wielu pomocników tagów nie można używać jako tagów samozamykających.</span><span class="sxs-lookup"><span data-stu-id="ba92b-176">Many Tag Helpers can't be used as self-closing tags.</span></span> <span data-ttu-id="ba92b-177">Niektóre pomocnicy tagów zostały zaprojektowane jako Tagi samozamykające się.</span><span class="sxs-lookup"><span data-stu-id="ba92b-177">Some Tag Helpers are designed to be self-closing tags.</span></span> <span data-ttu-id="ba92b-178">Użycie pomocnika tagów, który nie został zaprojektowany do samodzielnego zamykania, pomija renderowane dane wyjściowe.</span><span class="sxs-lookup"><span data-stu-id="ba92b-178">Using a Tag Helper that was not designed to be self-closing suppresses the rendered output.</span></span> <span data-ttu-id="ba92b-179">Samodzielne zamknięcie pomocnika tagów skutkuje tagiem samozamykającym w renderowanych danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="ba92b-179">Self-closing a Tag Helper results in a self-closing tag in the rendered output.</span></span> <span data-ttu-id="ba92b-180">Aby uzyskać więcej informacji, zobacz [tę uwagę](xref:mvc/views/tag-helpers/authoring#self-closing) w [ułatwieniach tworzenia tagów](xref:mvc/views/tag-helpers/authoring).</span><span class="sxs-lookup"><span data-stu-id="ba92b-180">For more information, see [this note](xref:mvc/views/tag-helpers/authoring#self-closing) in [Authoring Tag Helpers](xref:mvc/views/tag-helpers/authoring).</span></span>

## <a name="c-in-tag-helpers-attributedeclaration"></a><span data-ttu-id="ba92b-181">Język C# w atrybutach/deklaracji pomocników tagów</span><span class="sxs-lookup"><span data-stu-id="ba92b-181">C# in Tag Helpers attribute/declaration</span></span> 

<span data-ttu-id="ba92b-182">Pomocnicy tagów nie zezwalają na C# w obszarze atrybutu lub deklaracji tagu elementu.</span><span class="sxs-lookup"><span data-stu-id="ba92b-182">Tag Helpers do not allow C# in the element's attribute or tag declaration area.</span></span> <span data-ttu-id="ba92b-183">Na przykład następujący kod jest nieprawidłowy:</span><span class="sxs-lookup"><span data-stu-id="ba92b-183">For example, the following code is not valid:</span></span>

```cshtml
<input asp-for="LastName"  
       @(Model?.LicenseId == null ? "disabled" : string.Empty) />
```

<span data-ttu-id="ba92b-184">Poprzedni kod można napisać jako:</span><span class="sxs-lookup"><span data-stu-id="ba92b-184">The preceding code can be written as:</span></span>

```cshtml
<input asp-for="LastName" 
       disabled="@(Model?.LicenseId == null)" />
```

## <a name="intellisense-support-for-tag-helpers"></a><span data-ttu-id="ba92b-185">Obsługa technologii IntelliSense dla pomocników tagów</span><span class="sxs-lookup"><span data-stu-id="ba92b-185">IntelliSense support for Tag Helpers</span></span>

<span data-ttu-id="ba92b-186">Podczas tworzenia nowej aplikacji sieci Web ASP.NET Core w programie Visual Studio zostanie dodany pakiet NuGet "Microsoft. AspNetCore. Razor .. Narzędzia ".</span><span class="sxs-lookup"><span data-stu-id="ba92b-186">When you create a new ASP.NET Core web app in Visual Studio, it adds the NuGet package "Microsoft.AspNetCore.Razor.Tools".</span></span> <span data-ttu-id="ba92b-187">Jest to pakiet, który dodaje narzędzia pomocnika tagów.</span><span class="sxs-lookup"><span data-stu-id="ba92b-187">This is the package that adds Tag Helper tooling.</span></span>

<span data-ttu-id="ba92b-188">Rozważ zapisanie `<label>` elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="ba92b-188">Consider writing an HTML `<label>` element.</span></span> <span data-ttu-id="ba92b-189">Gdy tylko wprowadzisz `<l` w edytorze programu Visual Studio, IntelliSense wyświetla pasujące elementy:</span><span class="sxs-lookup"><span data-stu-id="ba92b-189">As soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![image (obraz)](intro/_static/label.png)

<span data-ttu-id="ba92b-191">Możesz nie tylko uzyskać Pomoc HTML, ale ikonę (" @" symbol with "<>" w tym obszarze).</span><span class="sxs-lookup"><span data-stu-id="ba92b-191">Not only do you get HTML help, but the icon (the "@" symbol with "<>" under it).</span></span>

![image (obraz)](intro/_static/tagSym.png)

<span data-ttu-id="ba92b-193">identyfikuje element jako obiekt przeznaczony dla pomocników tagów.</span><span class="sxs-lookup"><span data-stu-id="ba92b-193">identifies the element as targeted by Tag Helpers.</span></span> <span data-ttu-id="ba92b-194">Czyste elementy HTML (takie jak `fieldset` ) wyświetlają ikonę "<>".</span><span class="sxs-lookup"><span data-stu-id="ba92b-194">Pure HTML elements (such as the `fieldset`) display the "<>" icon.</span></span>

<span data-ttu-id="ba92b-195">Czysty tag HTML `<label>` wyświetla tag HTML (z domyślnym motywem kolorów programu Visual Studio) w czcionkze brązowym, atrybuty w kolorze czerwonym i wartości atrybutów w kolorze niebieskim.</span><span class="sxs-lookup"><span data-stu-id="ba92b-195">A pure HTML `<label>` tag displays the HTML tag (with the default Visual Studio color theme) in a brown font, the attributes in red, and the attribute values in blue.</span></span>

![image (obraz)](intro/_static/LableHtmlTag.png)

<span data-ttu-id="ba92b-197">Po wprowadzeniu funkcja `<label` IntelliSense wyświetla dostępne atrybuty HTML/CSS i atrybuty dostosowane przez pomocnika tagów:</span><span class="sxs-lookup"><span data-stu-id="ba92b-197">After you enter `<label`, IntelliSense lists the available HTML/CSS attributes and the Tag Helper-targeted attributes:</span></span>

![image (obraz)](intro/_static/labelattr.png)

<span data-ttu-id="ba92b-199">Uzupełnianie instrukcji IntelliSense umożliwia wprowadzenie klawisza Tab w celu ukończenia instrukcji z wybraną wartością:</span><span class="sxs-lookup"><span data-stu-id="ba92b-199">IntelliSense statement completion allows you to enter the tab key to complete the statement with the selected value:</span></span>

![image (obraz)](intro/_static/stmtcomplete.png)

<span data-ttu-id="ba92b-201">Gdy tylko atrybut pomocnika tagów zostanie wprowadzony, czcionki tagów i atrybutów zmienią się.</span><span class="sxs-lookup"><span data-stu-id="ba92b-201">As soon as a Tag Helper attribute is entered, the tag and attribute fonts change.</span></span> <span data-ttu-id="ba92b-202">Przy użyciu domyślnego motywu kolorów "niebieska" lub "jasne" programu Visual Studio czcionka jest pogrubiona.</span><span class="sxs-lookup"><span data-stu-id="ba92b-202">Using the default Visual Studio "Blue" or "Light" color theme, the font is bold purple.</span></span> <span data-ttu-id="ba92b-203">Jeśli używasz motywu "ciemny", czcionka jest pogrubiona.</span><span class="sxs-lookup"><span data-stu-id="ba92b-203">If you're using the "Dark" theme the font is bold teal.</span></span> <span data-ttu-id="ba92b-204">Obrazy z tego dokumentu zostały wykonane przy użyciu motywu domyślnego.</span><span class="sxs-lookup"><span data-stu-id="ba92b-204">The images in this document were taken using the default theme.</span></span>

![image (obraz)](intro/_static/labelaspfor2.png)

<span data-ttu-id="ba92b-206">Możesz wprowadzić skrót do programu Visual Studio *CompleteWord* (kombinacja klawiszy CTRL + SPACJA jest [wartością domyślną](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) wewnątrz cudzysłowu podwójnego ("") i jesteś teraz w języku c#, podobnie jak w przypadku klasy języka c#.</span><span class="sxs-lookup"><span data-stu-id="ba92b-206">You can enter the Visual Studio *CompleteWord* shortcut (Ctrl +spacebar is the [default](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) inside the double quotes (""), and you are now in C#, just like you would be in a C# class.</span></span> <span data-ttu-id="ba92b-207">Technologia IntelliSense wyświetla wszystkie metody i właściwości w modelu strony.</span><span class="sxs-lookup"><span data-stu-id="ba92b-207">IntelliSense displays all the methods and properties on the page model.</span></span> <span data-ttu-id="ba92b-208">Metody i właściwości są dostępne, ponieważ typ właściwości to `ModelExpression` .</span><span class="sxs-lookup"><span data-stu-id="ba92b-208">The methods and properties are available because the property type is `ModelExpression`.</span></span> <span data-ttu-id="ba92b-209">Na poniższej ilustracji edytuję `Register` Widok, więc `RegisterViewModel` jest dostępny.</span><span class="sxs-lookup"><span data-stu-id="ba92b-209">In the image below, I'm editing the `Register` view, so the `RegisterViewModel` is available.</span></span>

![image (obraz)](intro/_static/intellemail.png)

<span data-ttu-id="ba92b-211">Technologia IntelliSense wyświetla listę właściwości i metod dostępnych dla modelu na stronie.</span><span class="sxs-lookup"><span data-stu-id="ba92b-211">IntelliSense lists the properties and methods available to the model on the page.</span></span> <span data-ttu-id="ba92b-212">Rozbudowane środowisko IntelliSense ułatwia wybranie klasy CSS:</span><span class="sxs-lookup"><span data-stu-id="ba92b-212">The rich IntelliSense environment helps you select the CSS class:</span></span>

![image (obraz)](intro/_static/iclass.png)

![image (obraz)](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a><span data-ttu-id="ba92b-215">Pomocnicy tagów w porównaniu do pomocników HTML</span><span class="sxs-lookup"><span data-stu-id="ba92b-215">Tag Helpers compared to HTML Helpers</span></span>

<span data-ttu-id="ba92b-216">Pomocnicy tagów dołącza do elementów HTML w Razor widokach, natomiast [pomocników HTML](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) są wywoływane jako metody odnoszące się do języka HTML w Razor widokach.</span><span class="sxs-lookup"><span data-stu-id="ba92b-216">Tag Helpers attach to HTML elements in Razor views, while [HTML Helpers](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) are invoked as methods interspersed with HTML in Razor views.</span></span> <span data-ttu-id="ba92b-217">Weź pod uwagę następujące Razor znaczniki, które tworzą etykietę HTML z klasą CSS "Caption":</span><span class="sxs-lookup"><span data-stu-id="ba92b-217">Consider the following Razor markup, which creates an HTML label with the CSS class "caption":</span></span>

```cshtml
@Html.Label("FirstName", "First Name:", new {@class="caption"})
```

<span data-ttu-id="ba92b-218">Symbol at ( `@` ) mówi Razor , że jest to początek kodu.</span><span class="sxs-lookup"><span data-stu-id="ba92b-218">The at (`@`) symbol tells Razor this is the start of code.</span></span> <span data-ttu-id="ba92b-219">Następne dwa parametry ("FirstName" i "First Name:") są ciągami, więc [technologia IntelliSense](/visualstudio/ide/using-intellisense) nie może pomóc.</span><span class="sxs-lookup"><span data-stu-id="ba92b-219">The next two parameters ("FirstName" and "First Name:") are strings, so [IntelliSense](/visualstudio/ide/using-intellisense) can't help.</span></span> <span data-ttu-id="ba92b-220">Ostatni argument:</span><span class="sxs-lookup"><span data-stu-id="ba92b-220">The last argument:</span></span>

```cshtml
new {@class="caption"}
```

<span data-ttu-id="ba92b-221">Jest obiektem anonimowym używanym do reprezentowania atrybutów.</span><span class="sxs-lookup"><span data-stu-id="ba92b-221">Is an anonymous object used to represent attributes.</span></span> <span data-ttu-id="ba92b-222">Ponieważ `class` jest zarezerwowanym słowem kluczowym w języku c#, należy użyć `@` symbolu, aby wymusić, że C# interpretuje `@class=` jako symbol (nazwa właściwości).</span><span class="sxs-lookup"><span data-stu-id="ba92b-222">Because `class` is a reserved keyword in C#, you use the `@` symbol to force C# to interpret `@class=` as a symbol (property name).</span></span> <span data-ttu-id="ba92b-223">Do projektanta frontonu (ktoś zaznajomiony z językiem HTML/CSS/JavaScript i innymi technologiami klienta, ale nie zna języka C# i Razor ), większość linii jest obca.</span><span class="sxs-lookup"><span data-stu-id="ba92b-223">To a front-end designer (someone familiar with HTML/CSS/JavaScript and other client technologies but not familiar with C# and Razor), most of the line is foreign.</span></span> <span data-ttu-id="ba92b-224">Cały wiersz musi być utworzony bez pomocy na platformie IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="ba92b-224">The entire line must be authored with no help from IntelliSense.</span></span>

<span data-ttu-id="ba92b-225">Przy użyciu `LabelTagHelper` , tę samą adiustację można napisać jako:</span><span class="sxs-lookup"><span data-stu-id="ba92b-225">Using the `LabelTagHelper`, the same markup can be written as:</span></span>

```cshtml
<label class="caption" asp-for="FirstName"></label>
```

<span data-ttu-id="ba92b-226">Za pomocą wersji pomocnika tagów, gdy tylko wprowadzisz `<l` w edytorze programu Visual Studio, IntelliSense wyświetla pasujące elementy:</span><span class="sxs-lookup"><span data-stu-id="ba92b-226">With the Tag Helper version, as soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![image (obraz)](intro/_static/label.png)

<span data-ttu-id="ba92b-228">Technologia IntelliSense pomaga napisać cały wiersz.</span><span class="sxs-lookup"><span data-stu-id="ba92b-228">IntelliSense helps you write the entire line.</span></span>

<span data-ttu-id="ba92b-229">Poniższy obraz kodu przedstawia część formularza widoku *widoki/Account/register. cshtml* Razor wygenerowanego na podstawie szablonu MVC ASP.NET 4.5. x dołączonego do programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ba92b-229">The following code image shows the Form portion of the *Views/Account/Register.cshtml* Razor view generated from the ASP.NET 4.5.x MVC template included with Visual Studio.</span></span>

![image (obraz)](intro/_static/regCS.png)

<span data-ttu-id="ba92b-231">Edytor programu Visual Studio Wyświetla kod C# z szarym tłem.</span><span class="sxs-lookup"><span data-stu-id="ba92b-231">The Visual Studio editor displays C# code with a grey background.</span></span> <span data-ttu-id="ba92b-232">Na przykład `AntiForgeryToken` pomocnik HTML:</span><span class="sxs-lookup"><span data-stu-id="ba92b-232">For example, the `AntiForgeryToken` HTML Helper:</span></span>

```cshtml
@Html.AntiForgeryToken()
```

<span data-ttu-id="ba92b-233">jest wyświetlany szare tło.</span><span class="sxs-lookup"><span data-stu-id="ba92b-233">is displayed with a grey background.</span></span> <span data-ttu-id="ba92b-234">Większość znaczników w widoku rejestru to C#.</span><span class="sxs-lookup"><span data-stu-id="ba92b-234">Most of the markup in the Register view is C#.</span></span> <span data-ttu-id="ba92b-235">Porównaj ją z równoważną metodą przy użyciu pomocników tagów:</span><span class="sxs-lookup"><span data-stu-id="ba92b-235">Compare that to the equivalent approach using Tag Helpers:</span></span>

![image (obraz)](intro/_static/regTH.png)

<span data-ttu-id="ba92b-237">Oznakowanie jest znacznie bardziej czytelne i łatwiejsze do odczytania, edycji i konserwacji niż podejście pomocników HTML.</span><span class="sxs-lookup"><span data-stu-id="ba92b-237">The markup is much cleaner and easier to read, edit, and maintain than the HTML Helpers approach.</span></span> <span data-ttu-id="ba92b-238">Kod w języku C# jest zredukowany do minimum, o którym serwer musi wiedzieć.</span><span class="sxs-lookup"><span data-stu-id="ba92b-238">The C# code is reduced to the minimum that the server needs to know about.</span></span> <span data-ttu-id="ba92b-239">Edytor programu Visual Studio Wyświetla znaczniki wskazywane przez pomocnika tagów w czcionce charakterystycznej.</span><span class="sxs-lookup"><span data-stu-id="ba92b-239">The Visual Studio editor displays markup targeted by a Tag Helper in a distinctive font.</span></span>

<span data-ttu-id="ba92b-240">Weź pod uwagę grupę *poczty e-mail* :</span><span class="sxs-lookup"><span data-stu-id="ba92b-240">Consider the *Email* group:</span></span>

[!code-cshtml[](intro/sample/Register.cshtml?range=12-18)]

<span data-ttu-id="ba92b-241">Każdy atrybut "ASP-" ma wartość "email", ale "Poczta E-mail" nie jest ciągiem.</span><span class="sxs-lookup"><span data-stu-id="ba92b-241">Each of the "asp-" attributes has a value of "Email", but "Email" isn't a string.</span></span> <span data-ttu-id="ba92b-242">W tym kontekście "Poczta E-mail" jest właściwością wyrażenia modelu C# dla `RegisterViewModel` .</span><span class="sxs-lookup"><span data-stu-id="ba92b-242">In this context, "Email" is the C# model expression property for the `RegisterViewModel`.</span></span>

<span data-ttu-id="ba92b-243">Edytor programu Visual Studio pomaga napisać **wszystkie** znaczniki w podejściu pomocnika tagów w formularzu rejestracji, natomiast program Visual Studio nie zapewnia pomocy w przypadku większości kodu w podejściu do pomocników html.</span><span class="sxs-lookup"><span data-stu-id="ba92b-243">The Visual Studio editor helps you write **all** of the markup in the Tag Helper approach of the register form, while Visual Studio provides no help for most of the code in the HTML Helpers approach.</span></span> <span data-ttu-id="ba92b-244">[Obsługa technologii IntelliSense dla pomocników tagów](#intellisense-support-for-tag-helpers) prowadzi do szczegółowych informacji na temat pracy z pomocnikami tagów w edytorze programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ba92b-244">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) goes into detail on working with Tag Helpers in the Visual Studio editor.</span></span>

## <a name="tag-helpers-compared-to-web-server-controls"></a><span data-ttu-id="ba92b-245">Pomocnicy tagów w porównaniu do kontrolek serwera sieci Web</span><span class="sxs-lookup"><span data-stu-id="ba92b-245">Tag Helpers compared to Web Server Controls</span></span>

* <span data-ttu-id="ba92b-246">Pomocnicy tagów nie są własnością elementu, z którym są skojarzone; po prostu biorą udział w renderowaniu elementu i zawartości.</span><span class="sxs-lookup"><span data-stu-id="ba92b-246">Tag Helpers don't own the element they're associated with; they simply participate in the rendering of the element and content.</span></span> <span data-ttu-id="ba92b-247">[Kontrolki serwera sieci Web](https://msdn.microsoft.com/library/7698y1f0.aspx) ASP.NET są deklarowane i wywoływane na stronie.</span><span class="sxs-lookup"><span data-stu-id="ba92b-247">ASP.NET [Web Server controls](https://msdn.microsoft.com/library/7698y1f0.aspx) are declared and invoked on a page.</span></span>

* <span data-ttu-id="ba92b-248">[Kontrolki serwera sieci Web](https://msdn.microsoft.com/library/zsyt68f1.aspx) mają nieuproszczony cykl życia, który może sprawiać, że programowanie i debugowanie jest trudne.</span><span class="sxs-lookup"><span data-stu-id="ba92b-248">[Web Server controls](https://msdn.microsoft.com/library/zsyt68f1.aspx) have a non-trivial lifecycle that can make developing and debugging difficult.</span></span>

* <span data-ttu-id="ba92b-249">Kontrolki serwera sieci Web umożliwiają dodawanie funkcji do elementów Document Object Model klienta (DOM) przy użyciu formantu klienta.</span><span class="sxs-lookup"><span data-stu-id="ba92b-249">Web Server controls allow you to add functionality to the client Document Object Model (DOM) elements by using a client control.</span></span> <span data-ttu-id="ba92b-250">Pomocnicy tagów nie mają modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="ba92b-250">Tag Helpers have no DOM.</span></span>

* <span data-ttu-id="ba92b-251">Formanty serwera sieci Web obejmują automatyczne wykrywanie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="ba92b-251">Web Server controls include automatic browser detection.</span></span> <span data-ttu-id="ba92b-252">Pomocnicy tagów nie znają przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="ba92b-252">Tag Helpers have no knowledge of the browser.</span></span>

* <span data-ttu-id="ba92b-253">Wiele pomocników tagów może działać na tym samym elemencie (zobacz [unikanie konfliktów pomocnika tagów](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts) ), podczas gdy zazwyczaj nie można tworzyć kontrolek serwera sieci Web.</span><span class="sxs-lookup"><span data-stu-id="ba92b-253">Multiple Tag Helpers can act on the same element (see [Avoiding Tag Helper conflicts](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts) ) while you typically can't compose Web Server controls.</span></span>

* <span data-ttu-id="ba92b-254">Pomocnicy tagów mogą modyfikować tag i zawartość elementów HTML, do których należą zakresy, ale nie mogą bezpośrednio modyfikować niczego na stronie.</span><span class="sxs-lookup"><span data-stu-id="ba92b-254">Tag Helpers can modify the tag and content of HTML elements that they're scoped to, but don't directly modify anything else on a page.</span></span> <span data-ttu-id="ba92b-255">Formanty serwera sieci Web mają mniej konkretny zakres i mogą wykonywać akcje, które wpływają na inne części strony. Włączanie niezamierzonych efektów ubocznych.</span><span class="sxs-lookup"><span data-stu-id="ba92b-255">Web Server controls have a less specific scope and can perform actions that affect other parts of your page; enabling unintended side effects.</span></span>

* <span data-ttu-id="ba92b-256">Formanty serwera sieci Web używają konwerterów typów do konwertowania ciągów na obiekty.</span><span class="sxs-lookup"><span data-stu-id="ba92b-256">Web Server controls use type converters to convert strings into objects.</span></span> <span data-ttu-id="ba92b-257">Korzystając z pomocników tagów, pracujesz natywnie w języku C#, więc nie musisz wykonywać konwersji typów.</span><span class="sxs-lookup"><span data-stu-id="ba92b-257">With Tag Helpers, you work natively in C#, so you don't need to do type conversion.</span></span>

* <span data-ttu-id="ba92b-258">Formanty serwera sieci Web używają elementu [System. ComponentModel](/dotnet/api/system.componentmodel) , aby zaimplementować zachowanie składników i formantów w czasie wykonywania oraz w czasie projektowania.</span><span class="sxs-lookup"><span data-stu-id="ba92b-258">Web Server controls use [System.ComponentModel](/dotnet/api/system.componentmodel) to implement the run-time and design-time behavior of components and controls.</span></span> <span data-ttu-id="ba92b-259">`System.ComponentModel`zawiera klasy bazowe i interfejsy służące do implementowania atrybutów i konwerterów typów, powiązania ze źródłami danych i składnikami licencjonowania.</span><span class="sxs-lookup"><span data-stu-id="ba92b-259">`System.ComponentModel` includes the base classes and interfaces for implementing attributes and type converters, binding to data sources, and licensing components.</span></span> <span data-ttu-id="ba92b-260">Kontrast służący do tagowania pomocników, które zazwyczaj pochodzą z `TagHelper` , i `TagHelper` klasy bazowej uwidacznia tylko dwie metody `Process` i `ProcessAsync` .</span><span class="sxs-lookup"><span data-stu-id="ba92b-260">Contrast that to Tag Helpers, which typically derive from `TagHelper`, and the `TagHelper` base class exposes only two methods, `Process` and `ProcessAsync`.</span></span>

## <a name="customizing-the-tag-helper-element-font"></a><span data-ttu-id="ba92b-261">Dostosowywanie czcionki elementu pomocnika tagów</span><span class="sxs-lookup"><span data-stu-id="ba92b-261">Customizing the Tag Helper element font</span></span>

<span data-ttu-id="ba92b-262">Możesz dostosować czcionkę i kolorowanie przy użyciu **Tools**  >  **opcji**narzędzia  >  **Environment**  >  **czcionki i kolory**środowiska:</span><span class="sxs-lookup"><span data-stu-id="ba92b-262">You can customize the font and colorization from **Tools** > **Options** > **Environment** > **Fonts and Colors**:</span></span>

![image (obraz)](intro/_static/fontoptions2.png)

[!INCLUDE[](~/includes/built-in-TH.md)]

## <a name="additional-resources"></a><span data-ttu-id="ba92b-264">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="ba92b-264">Additional resources</span></span>

* [<span data-ttu-id="ba92b-265">Autorzy tagów</span><span class="sxs-lookup"><span data-stu-id="ba92b-265">Author Tag Helpers</span></span>](xref:mvc/views/tag-helpers/authoring)
* [<span data-ttu-id="ba92b-266">Praca z formularzami</span><span class="sxs-lookup"><span data-stu-id="ba92b-266">Working with Forms</span></span>](xref:mvc/views/working-with-forms)
* <span data-ttu-id="ba92b-267">[TagHelperSamples w usłudze GitHub](https://github.com/dpaquette/TagHelperSamples) zawiera przykłady pomocnika tagów do pracy z [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="ba92b-267">[TagHelperSamples on GitHub](https://github.com/dpaquette/TagHelperSamples) contains Tag Helper samples for working with [Bootstrap](https://getbootstrap.com/).</span></span>
