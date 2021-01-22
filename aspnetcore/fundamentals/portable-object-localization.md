---
title: Konfigurowanie lokalizacji obiektu przenośnego w ASP.NET Core
author: sebastienros
description: W tym artykule wprowadzono przenośne pliki obiektów i instrukcje dotyczące korzystania z nich w aplikacji ASP.NET Coreej z podstawową platformą sadu.
ms.author: scaddie
ms.date: 09/26/2017
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
uid: fundamentals/portable-object-localization
ms.openlocfilehash: 175614c426c564ce91068e18035ce05311432698
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689243"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="18d3c-103">Konfigurowanie lokalizacji obiektu przenośnego w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18d3c-103">Configure portable object localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="18d3c-104">[Sébastien ros](https://github.com/sebastienros), [Scott Addie](https://twitter.com/Scott_Addie) i [Hisham bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="18d3c-104">By [Sébastien Ros](https://github.com/sebastienros), [Scott Addie](https://twitter.com/Scott_Addie) and [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="18d3c-105">W tym artykule omówiono procedurę używania plików przenośnych obiektów (PO) w aplikacji ASP.NET Core z podstawową platformą [sadu](https://github.com/OrchardCMS/OrchardCore) .</span><span class="sxs-lookup"><span data-stu-id="18d3c-105">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="18d3c-106">**Uwaga:** Rdzeń sadu nie jest produktem firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="18d3c-106">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="18d3c-107">W związku z tym firma Microsoft nie zapewnia wsparcia dla tej funkcji.</span><span class="sxs-lookup"><span data-stu-id="18d3c-107">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="18d3c-108">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/POLocalization) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="18d3c-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="18d3c-109">Co to jest plik ZZ?</span><span class="sxs-lookup"><span data-stu-id="18d3c-109">What is a PO file?</span></span>

<span data-ttu-id="18d3c-110">Pliki do zakupu są dystrybuowane jako pliki tekstowe zawierające przetłumaczone ciągi dla danego języka.</span><span class="sxs-lookup"><span data-stu-id="18d3c-110">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="18d3c-111">Niektóre zalety korzystania z plików ZZ zamiast plików *resx* obejmują:</span><span class="sxs-lookup"><span data-stu-id="18d3c-111">Some advantages of using PO files instead of *.resx* files include:</span></span>
- <span data-ttu-id="18d3c-112">Pliki PO serwisie obsługują pluralizacja; pliki *resx* nie obsługują pluralizacja.</span><span class="sxs-lookup"><span data-stu-id="18d3c-112">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="18d3c-113">Pliki ZZ nie są kompilowane jak pliki *resx* .</span><span class="sxs-lookup"><span data-stu-id="18d3c-113">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="18d3c-114">W związku z tym wyspecjalizowane narzędzia i kroki kompilacji nie są wymagane.</span><span class="sxs-lookup"><span data-stu-id="18d3c-114">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="18d3c-115">Pliki PO pracy działają dobrze z narzędziami do edycji w trybie online.</span><span class="sxs-lookup"><span data-stu-id="18d3c-115">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="18d3c-116">Przykład</span><span class="sxs-lookup"><span data-stu-id="18d3c-116">Example</span></span>

<span data-ttu-id="18d3c-117">Poniżej znajduje się przykładowy plik w formacie PO, zawierający tłumaczenie dla dwóch ciągów w języku francuskim, w tym jeden z jego postacią w liczbie mnogiej:</span><span class="sxs-lookup"><span data-stu-id="18d3c-117">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="18d3c-118">*fr. ZZ*</span><span class="sxs-lookup"><span data-stu-id="18d3c-118">*fr.po*</span></span>

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

<span data-ttu-id="18d3c-119">W tym przykładzie użyto następującej składni:</span><span class="sxs-lookup"><span data-stu-id="18d3c-119">This example uses the following syntax:</span></span>

- <span data-ttu-id="18d3c-120">`#:`: Komentarz wskazujący kontekst ciągu do tłumaczenia.</span><span class="sxs-lookup"><span data-stu-id="18d3c-120">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="18d3c-121">Ten sam ciąg może być przetłumaczony inaczej w zależności od tego, gdzie jest używany.</span><span class="sxs-lookup"><span data-stu-id="18d3c-121">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="18d3c-122">`msgid`: Nieprzetłumaczony ciąg.</span><span class="sxs-lookup"><span data-stu-id="18d3c-122">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="18d3c-123">`msgstr`: Przetłumaczony ciąg.</span><span class="sxs-lookup"><span data-stu-id="18d3c-123">`msgstr`: The translated string.</span></span>

<span data-ttu-id="18d3c-124">W przypadku pomocy technicznej pluralizacja można zdefiniować więcej wpisów.</span><span class="sxs-lookup"><span data-stu-id="18d3c-124">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="18d3c-125">`msgid_plural`: Nieprzetłumaczony ciąg w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="18d3c-125">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="18d3c-126">`msgstr[0]`: Przetłumaczony ciąg dla przypadku 0.</span><span class="sxs-lookup"><span data-stu-id="18d3c-126">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="18d3c-127">`msgstr[N]`: Przetłumaczony ciąg dla przypadku N.</span><span class="sxs-lookup"><span data-stu-id="18d3c-127">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="18d3c-128">Specyfikację pliku PO stronie można znaleźć [tutaj](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span><span class="sxs-lookup"><span data-stu-id="18d3c-128">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="18d3c-129">Konfigurowanie obsługi plików PO zalogowaniu w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18d3c-129">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="18d3c-130">Ten przykład jest oparty na aplikacji ASP.NET Core MVC wygenerowanej na podstawie szablonu projektu programu Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="18d3c-130">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="18d3c-131">Odwoływanie się do pakietu</span><span class="sxs-lookup"><span data-stu-id="18d3c-131">Referencing the package</span></span>

<span data-ttu-id="18d3c-132">Dodaj odwołanie do `OrchardCore.Localization.Core` pakietu NuGet.</span><span class="sxs-lookup"><span data-stu-id="18d3c-132">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span>

<span data-ttu-id="18d3c-133">Plik *. csproj* zawiera teraz wiersz podobny do następującego (numer wersji może się różnić):</span><span class="sxs-lookup"><span data-stu-id="18d3c-133">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/3.x/POLocalization/POLocalization.csproj?range=8)]

### <a name="registering-the-service"></a><span data-ttu-id="18d3c-134">Rejestrowanie usługi</span><span class="sxs-lookup"><span data-stu-id="18d3c-134">Registering the service</span></span>

<span data-ttu-id="18d3c-135">Dodaj wymagane usługi do `ConfigureServices` metody *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="18d3c-135">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="18d3c-136">Dodaj wymagane oprogramowanie pośredniczące do `Configure` metody *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="18d3c-136">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="18d3c-137">Dodaj następujący kod do Razor wybranego widoku.</span><span class="sxs-lookup"><span data-stu-id="18d3c-137">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="18d3c-138">W tym przykładzie użyto *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="18d3c-138">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/3.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="18d3c-139">`IViewLocalizer`Wystąpienie jest wstrzykiwane i używane do tłumaczenia tekstu "Hello World!".</span><span class="sxs-lookup"><span data-stu-id="18d3c-139">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="18d3c-140">Tworzenie pliku ZZ</span><span class="sxs-lookup"><span data-stu-id="18d3c-140">Creating a PO file</span></span>

<span data-ttu-id="18d3c-141">Utwórz plik o nazwie *\<culture code> . ZZ* w folderze głównym aplikacji.</span><span class="sxs-lookup"><span data-stu-id="18d3c-141">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="18d3c-142">W tym przykładzie nazwa pliku to *fr. po* , ponieważ używany jest język francuski:</span><span class="sxs-lookup"><span data-stu-id="18d3c-142">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

<span data-ttu-id="18d3c-143">Ten plik przechowuje zarówno ciąg do przetłumaczenia, jak i ciąg przetłumaczony w języku francuskim.</span><span class="sxs-lookup"><span data-stu-id="18d3c-143">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="18d3c-144">W razie potrzeby tłumaczenia są przywracane do ich kultury nadrzędnej.</span><span class="sxs-lookup"><span data-stu-id="18d3c-144">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="18d3c-145">W tym przykładzie plik *fr. ZZ* jest używany, jeśli żądana kultura jest `fr-FR` lub `fr-CA` .</span><span class="sxs-lookup"><span data-stu-id="18d3c-145">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="18d3c-146">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="18d3c-146">Testing the application</span></span>

<span data-ttu-id="18d3c-147">Uruchom aplikację i przejdź do adresu URL `/Home/About` .</span><span class="sxs-lookup"><span data-stu-id="18d3c-147">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="18d3c-148">Tekst **Witaj świecie!**</span><span class="sxs-lookup"><span data-stu-id="18d3c-148">The text **Hello world!**</span></span> <span data-ttu-id="18d3c-149">jest wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="18d3c-149">is displayed.</span></span>

<span data-ttu-id="18d3c-150">Przejdź do adresu URL `/Home/About?culture=fr-FR` .</span><span class="sxs-lookup"><span data-stu-id="18d3c-150">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="18d3c-151">Tekst **Bonjour Le Monde!**</span><span class="sxs-lookup"><span data-stu-id="18d3c-151">The text **Bonjour le monde!**</span></span> <span data-ttu-id="18d3c-152">jest wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="18d3c-152">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="18d3c-153">Pluralizacja</span><span class="sxs-lookup"><span data-stu-id="18d3c-153">Pluralization</span></span>

<span data-ttu-id="18d3c-154">Pliki ZZ obsługują formularze pluralizacja, co jest przydatne, gdy ten sam ciąg musi być przetłumaczony inaczej w oparciu o Kardynalność.</span><span class="sxs-lookup"><span data-stu-id="18d3c-154">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="18d3c-155">To zadanie jest wykonywane w sposób skomplikowany przez fakt, że każdy język definiuje reguły niestandardowe, aby wybrać ciąg, który ma być używany na podstawie kardynalności.</span><span class="sxs-lookup"><span data-stu-id="18d3c-155">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="18d3c-156">Pakiet lokalizacji sadu udostępnia interfejs API umożliwiający automatyczne wywoływanie różnych formularzy w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="18d3c-156">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="18d3c-157">Tworzenie plików pluralizacja PO</span><span class="sxs-lookup"><span data-stu-id="18d3c-157">Creating pluralization PO files</span></span>

<span data-ttu-id="18d3c-158">Dodaj następującą zawartość do wspomnianego wcześniej pliku *fr. ZZ* :</span><span class="sxs-lookup"><span data-stu-id="18d3c-158">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="18d3c-159">Zobacz, [co to jest plik ZZ?](#what-is-a-po-file) , aby uzyskać wyjaśnienie, co reprezentuje każdy wpis w tym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="18d3c-159">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="18d3c-160">Dodawanie języka przy użyciu różnych formularzy pluralizacja</span><span class="sxs-lookup"><span data-stu-id="18d3c-160">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="18d3c-161">W poprzednim przykładzie użyto następujących ciągów w języku angielskim i francuskim.</span><span class="sxs-lookup"><span data-stu-id="18d3c-161">English and French strings were used in the previous example.</span></span> <span data-ttu-id="18d3c-162">W języku angielskim i francuskim istnieją tylko dwa formularze pluralizacja i współdzielą te same reguły formularza, co oznacza, że Kardynalność jednej z nich jest zamapowana na pierwszą formę w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="18d3c-162">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="18d3c-163">Każda inna Kardynalność jest mapowana na drugą formę w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="18d3c-163">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="18d3c-164">Nie wszystkie języki mają te same reguły.</span><span class="sxs-lookup"><span data-stu-id="18d3c-164">Not all languages share the same rules.</span></span> <span data-ttu-id="18d3c-165">Jest to zilustrowane w języku czeskim, który ma trzy formy w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="18d3c-165">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="18d3c-166">Utwórz `cs.po` plik w następujący sposób i zwróć uwagę na to, jak pluralizacja potrzebuje trzech różnych tłumaczeń:</span><span class="sxs-lookup"><span data-stu-id="18d3c-166">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/cs.po)]

<span data-ttu-id="18d3c-167">Aby zaakceptować lokalizacje czeskie, należy dodać `"cs"` do listy obsługiwanych kultur w `ConfigureServices` metodzie:</span><span class="sxs-lookup"><span data-stu-id="18d3c-167">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

<span data-ttu-id="18d3c-168">Edytuj plik *views/Home/about. cshtml* , aby renderować zlokalizowane ciągi w liczbie mnogiej dla kilku kardynalnych:</span><span class="sxs-lookup"><span data-stu-id="18d3c-168">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="18d3c-169">**Uwaga:** W świecie rzeczywistym, zmienna byłaby używana do reprezentowania liczby.</span><span class="sxs-lookup"><span data-stu-id="18d3c-169">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="18d3c-170">W tym miejscu powtarzamy ten sam kod z trzema różnymi wartościami, aby uwidocznić konkretny przypadek.</span><span class="sxs-lookup"><span data-stu-id="18d3c-170">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="18d3c-171">Po przełączeniu kultur widoczne są następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="18d3c-171">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="18d3c-172">Dla `/Home/About`:</span><span class="sxs-lookup"><span data-stu-id="18d3c-172">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="18d3c-173">Dla `/Home/About?culture=fr`:</span><span class="sxs-lookup"><span data-stu-id="18d3c-173">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="18d3c-174">Dla `/Home/About?culture=cs`:</span><span class="sxs-lookup"><span data-stu-id="18d3c-174">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="18d3c-175">Należy pamiętać, że w przypadku kultury Czeskiej trzy tłumaczenia są różne.</span><span class="sxs-lookup"><span data-stu-id="18d3c-175">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="18d3c-176">Kultury francuskie i angielskie korzystają z tej samej konstrukcji dla dwóch ostatnich przetłumaczonych ciągów.</span><span class="sxs-lookup"><span data-stu-id="18d3c-176">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="18d3c-177">Zaawansowane zadania</span><span class="sxs-lookup"><span data-stu-id="18d3c-177">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="18d3c-178">Contextualizing ciągi</span><span class="sxs-lookup"><span data-stu-id="18d3c-178">Contextualizing strings</span></span>

<span data-ttu-id="18d3c-179">Aplikacje często zawierają ciągi do tłumaczenia w kilku miejscach.</span><span class="sxs-lookup"><span data-stu-id="18d3c-179">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="18d3c-180">Ten sam ciąg może mieć inne tłumaczenie w określonych lokalizacjach w ramach aplikacji ( Razor widoków lub plików klas).</span><span class="sxs-lookup"><span data-stu-id="18d3c-180">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="18d3c-181">Plik ZZ obsługuje pojęcie kontekstu pliku, który może służyć do kategoryzowania reprezentowanego ciągu.</span><span class="sxs-lookup"><span data-stu-id="18d3c-181">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="18d3c-182">Przy użyciu kontekstu pliku ciąg może być przetłumaczony inaczej, w zależności od kontekstu pliku (lub braku kontekstu pliku).</span><span class="sxs-lookup"><span data-stu-id="18d3c-182">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="18d3c-183">Usługi lokalizowania PO stronie używają nazwy pełnej klasy lub widoku, który jest używany podczas tłumaczenia ciągu.</span><span class="sxs-lookup"><span data-stu-id="18d3c-183">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="18d3c-184">Jest to realizowane przez ustawienie wartości `msgctxt` wpisu.</span><span class="sxs-lookup"><span data-stu-id="18d3c-184">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="18d3c-185">Rozważmy drobne dodanie do poprzedniego przykładu *fr. po* .</span><span class="sxs-lookup"><span data-stu-id="18d3c-185">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="18d3c-186">RazorWidok znajdujący się w *widokach/Home/about. cshtml* można zdefiniować jako kontekst pliku przez ustawienie `msgctxt` wartości wpisu zastrzeżonego:</span><span class="sxs-lookup"><span data-stu-id="18d3c-186">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="18d3c-187">Z `msgctxt` ustawioną opcją, tłumaczenie tekstu odbywa się podczas nawigowania do `/Home/About?culture=fr-FR` .</span><span class="sxs-lookup"><span data-stu-id="18d3c-187">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="18d3c-188">Tłumaczenie nie będzie odbywać się podczas nawigowania do `/Home/Contact?culture=fr-FR` .</span><span class="sxs-lookup"><span data-stu-id="18d3c-188">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="18d3c-189">Gdy żaden konkretny wpis nie jest dopasowany do danego kontekstu pliku, mechanizm rezerwowy elementu sadu rdzeń szuka odpowiedniego pliku.</span><span class="sxs-lookup"><span data-stu-id="18d3c-189">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="18d3c-190">Przy założeniu, że nie określono określonego kontekstu pliku dla *widoków/Home/Contact. cshtml*, przechodzenie do `/Home/Contact?culture=fr-FR` ładowania pliku ZZ, takiego jak:</span><span class="sxs-lookup"><span data-stu-id="18d3c-190">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="18d3c-191">Zmiana lokalizacji plików ZZ</span><span class="sxs-lookup"><span data-stu-id="18d3c-191">Changing the location of PO files</span></span>

<span data-ttu-id="18d3c-192">Domyślną lokalizację plików PO stronie można zmienić w `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="18d3c-192">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="18d3c-193">W tym przykładzie pliki PO załadowaniu są ładowane z folderu *lokalizacji* .</span><span class="sxs-lookup"><span data-stu-id="18d3c-193">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="18d3c-194">Implementowanie logiki niestandardowej do znajdowania plików lokalizacyjnych</span><span class="sxs-lookup"><span data-stu-id="18d3c-194">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="18d3c-195">Gdy do lokalizowania plików jest wymagana bardziej złożona logika, `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interfejs może zostać zaimplementowany i zarejestrowany jako usługa.</span><span class="sxs-lookup"><span data-stu-id="18d3c-195">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="18d3c-196">Jest to przydatne, gdy pliki do zakupu mogą być przechowywane w różnych lokalizacjach lub w przypadku, gdy pliki muszą znajdować się w hierarchii folderów.</span><span class="sxs-lookup"><span data-stu-id="18d3c-196">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="18d3c-197">Używanie innego domyślnego języka w liczbie mnogiej</span><span class="sxs-lookup"><span data-stu-id="18d3c-197">Using a different default pluralized language</span></span>

<span data-ttu-id="18d3c-198">Pakiet zawiera `Plural` metodę rozszerzenia, która jest specyficzna dla dwóch form w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="18d3c-198">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="18d3c-199">W przypadku języków wymagających większej liczby formularzy w liczbie mnogiej Utwórz metodę rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="18d3c-199">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="18d3c-200">Przy użyciu metody rozszerzającej nie trzeba podawać pliku lokalizacji dla języka domyślnego &mdash; , oryginalne ciągi są już dostępne bezpośrednio w kodzie.</span><span class="sxs-lookup"><span data-stu-id="18d3c-200">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="18d3c-201">Można użyć bardziej ogólnego przeciążenia, `Plural(int count, string[] pluralForms, params object[] arguments)` które akceptuje tablicę ciągów tłumaczeń.</span><span class="sxs-lookup"><span data-stu-id="18d3c-201">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="18d3c-202">Autorzy [Sébastien ros](https://github.com/sebastienros) i [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="18d3c-202">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="18d3c-203">W tym artykule omówiono procedurę używania plików przenośnych obiektów (PO) w aplikacji ASP.NET Core z podstawową platformą [sadu](https://github.com/OrchardCMS/OrchardCore) .</span><span class="sxs-lookup"><span data-stu-id="18d3c-203">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="18d3c-204">**Uwaga:** Rdzeń sadu nie jest produktem firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="18d3c-204">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="18d3c-205">W związku z tym firma Microsoft nie zapewnia wsparcia dla tej funkcji.</span><span class="sxs-lookup"><span data-stu-id="18d3c-205">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="18d3c-206">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="18d3c-206">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="18d3c-207">Co to jest plik ZZ?</span><span class="sxs-lookup"><span data-stu-id="18d3c-207">What is a PO file?</span></span>

<span data-ttu-id="18d3c-208">Pliki do zakupu są dystrybuowane jako pliki tekstowe zawierające przetłumaczone ciągi dla danego języka.</span><span class="sxs-lookup"><span data-stu-id="18d3c-208">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="18d3c-209">Niektóre zalety korzystania z plików *. resx* są następujące:</span><span class="sxs-lookup"><span data-stu-id="18d3c-209">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="18d3c-210">Pliki PO serwisie obsługują pluralizacja; pliki *resx* nie obsługują pluralizacja.</span><span class="sxs-lookup"><span data-stu-id="18d3c-210">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="18d3c-211">Pliki ZZ nie są kompilowane jak pliki *resx* .</span><span class="sxs-lookup"><span data-stu-id="18d3c-211">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="18d3c-212">W związku z tym wyspecjalizowane narzędzia i kroki kompilacji nie są wymagane.</span><span class="sxs-lookup"><span data-stu-id="18d3c-212">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="18d3c-213">Pliki PO pracy działają dobrze z narzędziami do edycji w trybie online.</span><span class="sxs-lookup"><span data-stu-id="18d3c-213">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="18d3c-214">Przykład</span><span class="sxs-lookup"><span data-stu-id="18d3c-214">Example</span></span>

<span data-ttu-id="18d3c-215">Poniżej znajduje się przykładowy plik w formacie PO, zawierający tłumaczenie dla dwóch ciągów w języku francuskim, w tym jeden z jego postacią w liczbie mnogiej:</span><span class="sxs-lookup"><span data-stu-id="18d3c-215">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="18d3c-216">*fr. ZZ*</span><span class="sxs-lookup"><span data-stu-id="18d3c-216">*fr.po*</span></span>

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

<span data-ttu-id="18d3c-217">W tym przykładzie użyto następującej składni:</span><span class="sxs-lookup"><span data-stu-id="18d3c-217">This example uses the following syntax:</span></span>

- <span data-ttu-id="18d3c-218">`#:`: Komentarz wskazujący kontekst ciągu do tłumaczenia.</span><span class="sxs-lookup"><span data-stu-id="18d3c-218">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="18d3c-219">Ten sam ciąg może być przetłumaczony inaczej w zależności od tego, gdzie jest używany.</span><span class="sxs-lookup"><span data-stu-id="18d3c-219">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="18d3c-220">`msgid`: Nieprzetłumaczony ciąg.</span><span class="sxs-lookup"><span data-stu-id="18d3c-220">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="18d3c-221">`msgstr`: Przetłumaczony ciąg.</span><span class="sxs-lookup"><span data-stu-id="18d3c-221">`msgstr`: The translated string.</span></span>

<span data-ttu-id="18d3c-222">W przypadku pomocy technicznej pluralizacja można zdefiniować więcej wpisów.</span><span class="sxs-lookup"><span data-stu-id="18d3c-222">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="18d3c-223">`msgid_plural`: Nieprzetłumaczony ciąg w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="18d3c-223">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="18d3c-224">`msgstr[0]`: Przetłumaczony ciąg dla przypadku 0.</span><span class="sxs-lookup"><span data-stu-id="18d3c-224">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="18d3c-225">`msgstr[N]`: Przetłumaczony ciąg dla przypadku N.</span><span class="sxs-lookup"><span data-stu-id="18d3c-225">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="18d3c-226">Specyfikację pliku PO stronie można znaleźć [tutaj](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span><span class="sxs-lookup"><span data-stu-id="18d3c-226">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="18d3c-227">Konfigurowanie obsługi plików PO zalogowaniu w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18d3c-227">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="18d3c-228">Ten przykład jest oparty na aplikacji ASP.NET Core MVC wygenerowanej na podstawie szablonu projektu programu Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="18d3c-228">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="18d3c-229">Odwoływanie się do pakietu</span><span class="sxs-lookup"><span data-stu-id="18d3c-229">Referencing the package</span></span>

<span data-ttu-id="18d3c-230">Dodaj odwołanie do `OrchardCore.Localization.Core` pakietu NuGet.</span><span class="sxs-lookup"><span data-stu-id="18d3c-230">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span>

<span data-ttu-id="18d3c-231">Plik *. csproj* zawiera teraz wiersz podobny do następującego (numer wersji może się różnić):</span><span class="sxs-lookup"><span data-stu-id="18d3c-231">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="18d3c-232">Rejestrowanie usługi</span><span class="sxs-lookup"><span data-stu-id="18d3c-232">Registering the service</span></span>

<span data-ttu-id="18d3c-233">Dodaj wymagane usługi do `ConfigureServices` metody *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="18d3c-233">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="18d3c-234">Dodaj wymagane oprogramowanie pośredniczące do `Configure` metody *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="18d3c-234">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="18d3c-235">Dodaj następujący kod do Razor wybranego widoku.</span><span class="sxs-lookup"><span data-stu-id="18d3c-235">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="18d3c-236">W tym przykładzie użyto *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="18d3c-236">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="18d3c-237">`IViewLocalizer`Wystąpienie jest wstrzykiwane i używane do tłumaczenia tekstu "Hello World!".</span><span class="sxs-lookup"><span data-stu-id="18d3c-237">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="18d3c-238">Tworzenie pliku ZZ</span><span class="sxs-lookup"><span data-stu-id="18d3c-238">Creating a PO file</span></span>

<span data-ttu-id="18d3c-239">Utwórz plik o nazwie *\<culture code> . ZZ* w folderze głównym aplikacji.</span><span class="sxs-lookup"><span data-stu-id="18d3c-239">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="18d3c-240">W tym przykładzie nazwa pliku to *fr. po* , ponieważ używany jest język francuski:</span><span class="sxs-lookup"><span data-stu-id="18d3c-240">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

<span data-ttu-id="18d3c-241">Ten plik przechowuje zarówno ciąg do przetłumaczenia, jak i ciąg przetłumaczony w języku francuskim.</span><span class="sxs-lookup"><span data-stu-id="18d3c-241">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="18d3c-242">W razie potrzeby tłumaczenia są przywracane do ich kultury nadrzędnej.</span><span class="sxs-lookup"><span data-stu-id="18d3c-242">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="18d3c-243">W tym przykładzie plik *fr. ZZ* jest używany, jeśli żądana kultura jest `fr-FR` lub `fr-CA` .</span><span class="sxs-lookup"><span data-stu-id="18d3c-243">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="18d3c-244">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="18d3c-244">Testing the application</span></span>

<span data-ttu-id="18d3c-245">Uruchom aplikację i przejdź do adresu URL `/Home/About` .</span><span class="sxs-lookup"><span data-stu-id="18d3c-245">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="18d3c-246">Tekst **Witaj świecie!**</span><span class="sxs-lookup"><span data-stu-id="18d3c-246">The text **Hello world!**</span></span> <span data-ttu-id="18d3c-247">jest wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="18d3c-247">is displayed.</span></span>

<span data-ttu-id="18d3c-248">Przejdź do adresu URL `/Home/About?culture=fr-FR` .</span><span class="sxs-lookup"><span data-stu-id="18d3c-248">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="18d3c-249">Tekst **Bonjour Le Monde!**</span><span class="sxs-lookup"><span data-stu-id="18d3c-249">The text **Bonjour le monde!**</span></span> <span data-ttu-id="18d3c-250">jest wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="18d3c-250">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="18d3c-251">Pluralizacja</span><span class="sxs-lookup"><span data-stu-id="18d3c-251">Pluralization</span></span>

<span data-ttu-id="18d3c-252">Pliki ZZ obsługują formularze pluralizacja, co jest przydatne, gdy ten sam ciąg musi być przetłumaczony inaczej w oparciu o Kardynalność.</span><span class="sxs-lookup"><span data-stu-id="18d3c-252">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="18d3c-253">To zadanie jest wykonywane w sposób skomplikowany przez fakt, że każdy język definiuje reguły niestandardowe, aby wybrać ciąg, który ma być używany na podstawie kardynalności.</span><span class="sxs-lookup"><span data-stu-id="18d3c-253">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="18d3c-254">Pakiet lokalizacji sadu udostępnia interfejs API umożliwiający automatyczne wywoływanie różnych formularzy w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="18d3c-254">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="18d3c-255">Tworzenie plików pluralizacja PO</span><span class="sxs-lookup"><span data-stu-id="18d3c-255">Creating pluralization PO files</span></span>

<span data-ttu-id="18d3c-256">Dodaj następującą zawartość do wspomnianego wcześniej pliku *fr. ZZ* :</span><span class="sxs-lookup"><span data-stu-id="18d3c-256">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="18d3c-257">Zobacz, [co to jest plik ZZ?](#what-is-a-po-file) , aby uzyskać wyjaśnienie, co reprezentuje każdy wpis w tym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="18d3c-257">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="18d3c-258">Dodawanie języka przy użyciu różnych formularzy pluralizacja</span><span class="sxs-lookup"><span data-stu-id="18d3c-258">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="18d3c-259">W poprzednim przykładzie użyto następujących ciągów w języku angielskim i francuskim.</span><span class="sxs-lookup"><span data-stu-id="18d3c-259">English and French strings were used in the previous example.</span></span> <span data-ttu-id="18d3c-260">W języku angielskim i francuskim istnieją tylko dwa formularze pluralizacja i współdzielą te same reguły formularza, co oznacza, że Kardynalność jednej z nich jest zamapowana na pierwszą formę w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="18d3c-260">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="18d3c-261">Każda inna Kardynalność jest mapowana na drugą formę w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="18d3c-261">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="18d3c-262">Nie wszystkie języki mają te same reguły.</span><span class="sxs-lookup"><span data-stu-id="18d3c-262">Not all languages share the same rules.</span></span> <span data-ttu-id="18d3c-263">Jest to zilustrowane w języku czeskim, który ma trzy formy w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="18d3c-263">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="18d3c-264">Utwórz `cs.po` plik w następujący sposób i zwróć uwagę na to, jak pluralizacja potrzebuje trzech różnych tłumaczeń:</span><span class="sxs-lookup"><span data-stu-id="18d3c-264">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

<span data-ttu-id="18d3c-265">Aby zaakceptować lokalizacje czeskie, należy dodać `"cs"` do listy obsługiwanych kultur w `ConfigureServices` metodzie:</span><span class="sxs-lookup"><span data-stu-id="18d3c-265">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

<span data-ttu-id="18d3c-266">Edytuj plik *views/Home/about. cshtml* , aby renderować zlokalizowane ciągi w liczbie mnogiej dla kilku kardynalnych:</span><span class="sxs-lookup"><span data-stu-id="18d3c-266">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="18d3c-267">**Uwaga:** W świecie rzeczywistym, zmienna byłaby używana do reprezentowania liczby.</span><span class="sxs-lookup"><span data-stu-id="18d3c-267">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="18d3c-268">W tym miejscu powtarzamy ten sam kod z trzema różnymi wartościami, aby uwidocznić konkretny przypadek.</span><span class="sxs-lookup"><span data-stu-id="18d3c-268">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="18d3c-269">Po przełączeniu kultur widoczne są następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="18d3c-269">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="18d3c-270">Dla `/Home/About`:</span><span class="sxs-lookup"><span data-stu-id="18d3c-270">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="18d3c-271">Dla `/Home/About?culture=fr`:</span><span class="sxs-lookup"><span data-stu-id="18d3c-271">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="18d3c-272">Dla `/Home/About?culture=cs`:</span><span class="sxs-lookup"><span data-stu-id="18d3c-272">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="18d3c-273">Należy pamiętać, że w przypadku kultury Czeskiej trzy tłumaczenia są różne.</span><span class="sxs-lookup"><span data-stu-id="18d3c-273">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="18d3c-274">Kultury francuskie i angielskie korzystają z tej samej konstrukcji dla dwóch ostatnich przetłumaczonych ciągów.</span><span class="sxs-lookup"><span data-stu-id="18d3c-274">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="18d3c-275">Zaawansowane zadania</span><span class="sxs-lookup"><span data-stu-id="18d3c-275">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="18d3c-276">Contextualizing ciągi</span><span class="sxs-lookup"><span data-stu-id="18d3c-276">Contextualizing strings</span></span>

<span data-ttu-id="18d3c-277">Aplikacje często zawierają ciągi do tłumaczenia w kilku miejscach.</span><span class="sxs-lookup"><span data-stu-id="18d3c-277">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="18d3c-278">Ten sam ciąg może mieć inne tłumaczenie w określonych lokalizacjach w ramach aplikacji ( Razor widoków lub plików klas).</span><span class="sxs-lookup"><span data-stu-id="18d3c-278">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="18d3c-279">Plik ZZ obsługuje pojęcie kontekstu pliku, który może służyć do kategoryzowania reprezentowanego ciągu.</span><span class="sxs-lookup"><span data-stu-id="18d3c-279">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="18d3c-280">Przy użyciu kontekstu pliku ciąg może być przetłumaczony inaczej, w zależności od kontekstu pliku (lub braku kontekstu pliku).</span><span class="sxs-lookup"><span data-stu-id="18d3c-280">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="18d3c-281">Usługi lokalizowania PO stronie używają nazwy pełnej klasy lub widoku, który jest używany podczas tłumaczenia ciągu.</span><span class="sxs-lookup"><span data-stu-id="18d3c-281">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="18d3c-282">Jest to realizowane przez ustawienie wartości `msgctxt` wpisu.</span><span class="sxs-lookup"><span data-stu-id="18d3c-282">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="18d3c-283">Rozważmy drobne dodanie do poprzedniego przykładu *fr. po* .</span><span class="sxs-lookup"><span data-stu-id="18d3c-283">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="18d3c-284">RazorWidok znajdujący się w *widokach/Home/about. cshtml* można zdefiniować jako kontekst pliku przez ustawienie `msgctxt` wartości wpisu zastrzeżonego:</span><span class="sxs-lookup"><span data-stu-id="18d3c-284">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="18d3c-285">Z `msgctxt` ustawioną opcją, tłumaczenie tekstu odbywa się podczas nawigowania do `/Home/About?culture=fr-FR` .</span><span class="sxs-lookup"><span data-stu-id="18d3c-285">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="18d3c-286">Tłumaczenie nie będzie odbywać się podczas nawigowania do `/Home/Contact?culture=fr-FR` .</span><span class="sxs-lookup"><span data-stu-id="18d3c-286">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="18d3c-287">Gdy żaden konkretny wpis nie jest dopasowany do danego kontekstu pliku, mechanizm rezerwowy elementu sadu rdzeń szuka odpowiedniego pliku.</span><span class="sxs-lookup"><span data-stu-id="18d3c-287">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="18d3c-288">Przy założeniu, że nie określono określonego kontekstu pliku dla *widoków/Home/Contact. cshtml*, przechodzenie do `/Home/Contact?culture=fr-FR` ładowania pliku ZZ, takiego jak:</span><span class="sxs-lookup"><span data-stu-id="18d3c-288">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="18d3c-289">Zmiana lokalizacji plików ZZ</span><span class="sxs-lookup"><span data-stu-id="18d3c-289">Changing the location of PO files</span></span>

<span data-ttu-id="18d3c-290">Domyślną lokalizację plików PO stronie można zmienić w `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="18d3c-290">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="18d3c-291">W tym przykładzie pliki PO załadowaniu są ładowane z folderu *lokalizacji* .</span><span class="sxs-lookup"><span data-stu-id="18d3c-291">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="18d3c-292">Implementowanie logiki niestandardowej do znajdowania plików lokalizacyjnych</span><span class="sxs-lookup"><span data-stu-id="18d3c-292">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="18d3c-293">Gdy do lokalizowania plików jest wymagana bardziej złożona logika, `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interfejs może zostać zaimplementowany i zarejestrowany jako usługa.</span><span class="sxs-lookup"><span data-stu-id="18d3c-293">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="18d3c-294">Jest to przydatne, gdy pliki do zakupu mogą być przechowywane w różnych lokalizacjach lub w przypadku, gdy pliki muszą znajdować się w hierarchii folderów.</span><span class="sxs-lookup"><span data-stu-id="18d3c-294">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="18d3c-295">Używanie innego domyślnego języka w liczbie mnogiej</span><span class="sxs-lookup"><span data-stu-id="18d3c-295">Using a different default pluralized language</span></span>

<span data-ttu-id="18d3c-296">Pakiet zawiera `Plural` metodę rozszerzenia, która jest specyficzna dla dwóch form w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="18d3c-296">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="18d3c-297">W przypadku języków wymagających większej liczby formularzy w liczbie mnogiej Utwórz metodę rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="18d3c-297">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="18d3c-298">Przy użyciu metody rozszerzającej nie trzeba podawać pliku lokalizacji dla języka domyślnego &mdash; , oryginalne ciągi są już dostępne bezpośrednio w kodzie.</span><span class="sxs-lookup"><span data-stu-id="18d3c-298">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="18d3c-299">Można użyć bardziej ogólnego przeciążenia, `Plural(int count, string[] pluralForms, params object[] arguments)` które akceptuje tablicę ciągów tłumaczeń.</span><span class="sxs-lookup"><span data-stu-id="18d3c-299">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

::: moniker-end
