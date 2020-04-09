---
title: Konfigurowanie lokalizacji obiektów przenośnych w ASP.NET Core
author: sebastienros
description: W tym artykule przedstawiono pliki obiektów przenośnych i opisano kroki dotyczące używania ich w aplikacji ASP.NET Core w ramach rdzenia Orchard.
ms.author: scaddie
ms.date: 09/26/2017
uid: fundamentals/portable-object-localization
ms.openlocfilehash: 08002564eb68bc04eebaeafed560202d0d69958a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656192"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="1fec3-103">Konfigurowanie lokalizacji obiektów przenośnych w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1fec3-103">Configure portable object localization in ASP.NET Core</span></span>

<span data-ttu-id="1fec3-104">Przez [Sébastien Ros](https://github.com/sebastienros) i [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="1fec3-104">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="1fec3-105">W tym artykule oprzeszą się od kroków dotyczących używania plików obiektów przenośnych (PO) w aplikacji ASP.NET Core z platformą [Orchard Core.](https://github.com/OrchardCMS/OrchardCore)</span><span class="sxs-lookup"><span data-stu-id="1fec3-105">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="1fec3-106">**Uwaga:** Orchard Core nie jest produktem firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="1fec3-106">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="1fec3-107">W związku z tym firma Microsoft nie zapewnia obsługi tej funkcji.</span><span class="sxs-lookup"><span data-stu-id="1fec3-107">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="1fec3-108">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1fec3-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="1fec3-109">Co to jest plik po zamówić?</span><span class="sxs-lookup"><span data-stu-id="1fec3-109">What is a PO file?</span></span>

<span data-ttu-id="1fec3-110">Pliki PO są dystrybuowane jako pliki tekstowe zawierające przetłumaczone ciągi dla danego języka.</span><span class="sxs-lookup"><span data-stu-id="1fec3-110">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="1fec3-111">Niektóre zalety korzystania z plików PO zamiast *.resx* plików obejmują:</span><span class="sxs-lookup"><span data-stu-id="1fec3-111">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="1fec3-112">Pliki PO obsługują pluralizm; *Pliki .resx* nie obsługują pluralizacji.</span><span class="sxs-lookup"><span data-stu-id="1fec3-112">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="1fec3-113">Pliki PO nie są kompilowane jak pliki *.resx.*</span><span class="sxs-lookup"><span data-stu-id="1fec3-113">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="1fec3-114">W związku z tym specjalistyczne kroki narzędzi i kompilacji nie są wymagane.</span><span class="sxs-lookup"><span data-stu-id="1fec3-114">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="1fec3-115">Pliki PO działają dobrze dzięki narzędziom do edycji online.</span><span class="sxs-lookup"><span data-stu-id="1fec3-115">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="1fec3-116">Przykład</span><span class="sxs-lookup"><span data-stu-id="1fec3-116">Example</span></span>

<span data-ttu-id="1fec3-117">Oto przykładowy plik PO zawierający tłumaczenie dla dwóch ciągów w języku francuskim, w tym jednego z jego postacią liczby mnogiej:</span><span class="sxs-lookup"><span data-stu-id="1fec3-117">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="1fec3-118">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="1fec3-118">*fr.po*</span></span>

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

<span data-ttu-id="1fec3-119">W tym przykładzie użyto następującej składni:</span><span class="sxs-lookup"><span data-stu-id="1fec3-119">This example uses the following syntax:</span></span>

- <span data-ttu-id="1fec3-120">`#:`: Komentarz wskazujący kontekst ciągu do przetłumaczenia.</span><span class="sxs-lookup"><span data-stu-id="1fec3-120">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="1fec3-121">Ten sam ciąg może być tłumaczony inaczej w zależności od tego, gdzie jest używany.</span><span class="sxs-lookup"><span data-stu-id="1fec3-121">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="1fec3-122">`msgid`: Nieprzetłumaczony ciąg.</span><span class="sxs-lookup"><span data-stu-id="1fec3-122">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="1fec3-123">`msgstr`: Przetłumaczony ciąg.</span><span class="sxs-lookup"><span data-stu-id="1fec3-123">`msgstr`: The translated string.</span></span>

<span data-ttu-id="1fec3-124">W przypadku obsługi pluralizacji można zdefiniować więcej wpisów.</span><span class="sxs-lookup"><span data-stu-id="1fec3-124">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="1fec3-125">`msgid_plural`: Nieprzetłumaczony ciąg mnogi.</span><span class="sxs-lookup"><span data-stu-id="1fec3-125">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="1fec3-126">`msgstr[0]`: Przetłumaczony ciąg dla sprawy 0.</span><span class="sxs-lookup"><span data-stu-id="1fec3-126">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="1fec3-127">`msgstr[N]`: Przetłumaczony ciąg dla sprawy N.</span><span class="sxs-lookup"><span data-stu-id="1fec3-127">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="1fec3-128">Specyfikację pliku PO można znaleźć [tutaj](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span><span class="sxs-lookup"><span data-stu-id="1fec3-128">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="1fec3-129">Konfigurowanie obsługi plików PO w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1fec3-129">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="1fec3-130">W tym przykładzie jest oparty na ASP.NET core aplikacji MVC generowane z szablonu projektu programu Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="1fec3-130">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="1fec3-131">Odwoływanie się do pakietu</span><span class="sxs-lookup"><span data-stu-id="1fec3-131">Referencing the package</span></span>

<span data-ttu-id="1fec3-132">Dodaj odwołanie do `OrchardCore.Localization.Core` pakietu NuGet.</span><span class="sxs-lookup"><span data-stu-id="1fec3-132">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span> <span data-ttu-id="1fec3-133">Jest on dostępny na [MyGet](https://www.myget.org/) w następującym źródle pakietu:https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span><span class="sxs-lookup"><span data-stu-id="1fec3-133">It's available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span>

<span data-ttu-id="1fec3-134">Plik *csproj* zawiera teraz wiersz podobny do następującego (numer wersji może się różnić):</span><span class="sxs-lookup"><span data-stu-id="1fec3-134">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="1fec3-135">Rejestracja usługi</span><span class="sxs-lookup"><span data-stu-id="1fec3-135">Registering the service</span></span>

<span data-ttu-id="1fec3-136">Dodaj wymagane usługi do `ConfigureServices` metody *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="1fec3-136">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="1fec3-137">Dodaj wymagane oprogramowanie pośredniczące `Configure` do metody *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="1fec3-137">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="1fec3-138">Dodaj następujący kod do wybranego widoku Razor.</span><span class="sxs-lookup"><span data-stu-id="1fec3-138">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="1fec3-139">*About.cshtml* jest używany w tym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="1fec3-139">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="1fec3-140">Instancja `IViewLocalizer` jest wstrzykiwana i używana do tłumaczenia tekstu "Hello world!".</span><span class="sxs-lookup"><span data-stu-id="1fec3-140">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="1fec3-141">Tworzenie pliku PO</span><span class="sxs-lookup"><span data-stu-id="1fec3-141">Creating a PO file</span></span>

<span data-ttu-id="1fec3-142">Utwórz plik o nazwie \* \<kod kultury>.po\* w folderze głównym aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1fec3-142">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="1fec3-143">W tym przykładzie nazwa pliku jest *fr.po,* ponieważ używany jest język francuski:</span><span class="sxs-lookup"><span data-stu-id="1fec3-143">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/POLocalization/fr.po)]

<span data-ttu-id="1fec3-144">Ten plik przechowuje zarówno ciąg do tłumaczenia, jak i ciąg przetłumaczony na język francuski.</span><span class="sxs-lookup"><span data-stu-id="1fec3-144">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="1fec3-145">Tłumaczenia powrócić do ich kultury nadrzędnej, jeśli to konieczne.</span><span class="sxs-lookup"><span data-stu-id="1fec3-145">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="1fec3-146">W tym przykładzie plik *fr.po* jest używany, `fr-FR` `fr-CA`jeśli żądana kultura jest lub .</span><span class="sxs-lookup"><span data-stu-id="1fec3-146">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="1fec3-147">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="1fec3-147">Testing the application</span></span>

<span data-ttu-id="1fec3-148">Uruchom aplikację i przejdź do `/Home/About`adresu URL .</span><span class="sxs-lookup"><span data-stu-id="1fec3-148">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="1fec3-149">Tekst **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="1fec3-149">The text **Hello world!**</span></span> <span data-ttu-id="1fec3-150">zostanie wyświetlony komunikat.</span><span class="sxs-lookup"><span data-stu-id="1fec3-150">is displayed.</span></span>

<span data-ttu-id="1fec3-151">Przejdź do `/Home/About?culture=fr-FR`adresu URL .</span><span class="sxs-lookup"><span data-stu-id="1fec3-151">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="1fec3-152">Tekst **Bonjour le monde!**</span><span class="sxs-lookup"><span data-stu-id="1fec3-152">The text **Bonjour le monde!**</span></span> <span data-ttu-id="1fec3-153">zostanie wyświetlony komunikat.</span><span class="sxs-lookup"><span data-stu-id="1fec3-153">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="1fec3-154">Pluralizm</span><span class="sxs-lookup"><span data-stu-id="1fec3-154">Pluralization</span></span>

<span data-ttu-id="1fec3-155">Pliki PO obsługują formularze pluralizacji, co jest przydatne, gdy ten sam ciąg musi być tłumaczony inaczej na podstawie kardynalności.</span><span class="sxs-lookup"><span data-stu-id="1fec3-155">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="1fec3-156">To zadanie jest skomplikowane przez fakt, że każdy język definiuje reguły niestandardowe, aby wybrać ciąg do użycia na podstawie kardynalności.</span><span class="sxs-lookup"><span data-stu-id="1fec3-156">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="1fec3-157">Pakiet Lokalizacji sadu udostępnia interfejs API do automatycznego wywoływania tych różnych form w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="1fec3-157">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="1fec3-158">Tworzenie plików pluralizacji po</span><span class="sxs-lookup"><span data-stu-id="1fec3-158">Creating pluralization PO files</span></span>

<span data-ttu-id="1fec3-159">Dodaj następującą zawartość do wcześniej wymienionego pliku *fr.po:*</span><span class="sxs-lookup"><span data-stu-id="1fec3-159">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="1fec3-160">Zobacz [Co to jest plik po zamówieniu?](#what-is-a-po-file)</span><span class="sxs-lookup"><span data-stu-id="1fec3-160">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="1fec3-161">Dodawanie języka przy użyciu różnych formularzy pluralizacji</span><span class="sxs-lookup"><span data-stu-id="1fec3-161">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="1fec3-162">W poprzednim przykładzie użyto ciągów angielskich i francuskich.</span><span class="sxs-lookup"><span data-stu-id="1fec3-162">English and French strings were used in the previous example.</span></span> <span data-ttu-id="1fec3-163">Angielski i francuski mają tylko dwie formy pluralizacji i mają te same reguły formularza, co oznacza, że kardynalność jednego jest mapowana na pierwszą formę mnogą.</span><span class="sxs-lookup"><span data-stu-id="1fec3-163">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="1fec3-164">Każda inna kardynalność jest mapowana na drugą formę mnogą.</span><span class="sxs-lookup"><span data-stu-id="1fec3-164">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="1fec3-165">Nie wszystkie języki mają te same reguły.</span><span class="sxs-lookup"><span data-stu-id="1fec3-165">Not all languages share the same rules.</span></span> <span data-ttu-id="1fec3-166">Jest to zilustrowane językiem czeskim, który ma trzy formy mnogie.</span><span class="sxs-lookup"><span data-stu-id="1fec3-166">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="1fec3-167">Utwórz `cs.po` plik w następujący sposób i zanotuj, jak pluralizacja wymaga trzech różnych tłumaczeń:</span><span class="sxs-lookup"><span data-stu-id="1fec3-167">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/POLocalization/cs.po)]

<span data-ttu-id="1fec3-168">Aby zaakceptować czeskie `"cs"` lokalizacji, dodaj do listy obsługiwanych kultur w metodzie: `ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="1fec3-168">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

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

<span data-ttu-id="1fec3-169">Edytuj plik *Views/Home/About.cshtml,* aby renderować zlokalizowane ciągi mnogie dla kilku kardynalizacji:</span><span class="sxs-lookup"><span data-stu-id="1fec3-169">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="1fec3-170">**Uwaga:** W scenariuszu rzeczywistych zmienna będzie używana do reprezentowania liczby.</span><span class="sxs-lookup"><span data-stu-id="1fec3-170">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="1fec3-171">W tym miejscu powtarzamy ten sam kod z trzema różnymi wartościami, aby udostępnić bardzo konkretny przypadek.</span><span class="sxs-lookup"><span data-stu-id="1fec3-171">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="1fec3-172">Po przełączeniu kultur zobaczysz następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="1fec3-172">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="1fec3-173">Instrukcję `/Home/About`:</span><span class="sxs-lookup"><span data-stu-id="1fec3-173">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="1fec3-174">Instrukcję `/Home/About?culture=fr`:</span><span class="sxs-lookup"><span data-stu-id="1fec3-174">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="1fec3-175">Instrukcję `/Home/About?culture=cs`:</span><span class="sxs-lookup"><span data-stu-id="1fec3-175">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="1fec3-176">Należy zauważyć, że dla kultury czeskiej, trzy tłumaczenia są różne.</span><span class="sxs-lookup"><span data-stu-id="1fec3-176">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="1fec3-177">Kultury francuskie i angielskie mają tę samą konstrukcję dla dwóch ostatnich przetłumaczonych strun.</span><span class="sxs-lookup"><span data-stu-id="1fec3-177">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="1fec3-178">Zaawansowane zadania</span><span class="sxs-lookup"><span data-stu-id="1fec3-178">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="1fec3-179">Ciągi kontekstowe</span><span class="sxs-lookup"><span data-stu-id="1fec3-179">Contextualizing strings</span></span>

<span data-ttu-id="1fec3-180">Aplikacje często zawierają ciągi do przetłumaczenia w kilku miejscach.</span><span class="sxs-lookup"><span data-stu-id="1fec3-180">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="1fec3-181">Ten sam ciąg może mieć inne tłumaczenie w niektórych lokalizacjach w aplikacji (widoki Razor lub pliki klas).</span><span class="sxs-lookup"><span data-stu-id="1fec3-181">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="1fec3-182">Plik po obsługuje pojęcie kontekstu pliku, który może służyć do kategoryzowanie reprezentowanego ciągu.</span><span class="sxs-lookup"><span data-stu-id="1fec3-182">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="1fec3-183">Za pomocą kontekstu pliku ciąg może być tłumaczony inaczej, w zależności od kontekstu pliku (lub braku kontekstu pliku).</span><span class="sxs-lookup"><span data-stu-id="1fec3-183">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="1fec3-184">Usługi lokalizacji po używają nazwy pełnej klasy lub widoku, który jest używany podczas tłumaczenia ciągu.</span><span class="sxs-lookup"><span data-stu-id="1fec3-184">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="1fec3-185">Jest to realizowane przez ustawienie `msgctxt` wartości we wpisie.</span><span class="sxs-lookup"><span data-stu-id="1fec3-185">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="1fec3-186">Należy wziąć pod uwagę niewielki dodatek do poprzedniego *przykładu fr.po.*</span><span class="sxs-lookup"><span data-stu-id="1fec3-186">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="1fec3-187">Widok Razor znajdujący się w *widoku Views/Home/About.cshtml* można `msgctxt` zdefiniować jako kontekst pliku, ustawiając wartość wpisu zastrzeżonego:</span><span class="sxs-lookup"><span data-stu-id="1fec3-187">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="1fec3-188">W `msgctxt` przypadku zestawu jako takiego tłumaczenie tekstu `/Home/About?culture=fr-FR`odbywa się podczas przechodzenia do pliku .</span><span class="sxs-lookup"><span data-stu-id="1fec3-188">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="1fec3-189">Tłumaczenie nie nastąpi podczas przechodzenia `/Home/Contact?culture=fr-FR`do .</span><span class="sxs-lookup"><span data-stu-id="1fec3-189">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="1fec3-190">Jeśli żaden określony wpis nie jest dopasowywał się do danego kontekstu pliku, mechanizm rezerwowy Orchard Core wyszukuje odpowiedni plik po bez kontekstu.</span><span class="sxs-lookup"><span data-stu-id="1fec3-190">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="1fec3-191">Zakładając, że dla *views/Home/Contact.cshtml*nie zdefiniowano określonego `/Home/Contact?culture=fr-FR` kontekstu pliku, nawigacja do ładowania pliku PO, takiego jak:</span><span class="sxs-lookup"><span data-stu-id="1fec3-191">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="1fec3-192">Zmiana lokalizacji plików po zamówienia</span><span class="sxs-lookup"><span data-stu-id="1fec3-192">Changing the location of PO files</span></span>

<span data-ttu-id="1fec3-193">Domyślną lokalizację plików PO można `ConfigureServices`zmienić w :</span><span class="sxs-lookup"><span data-stu-id="1fec3-193">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="1fec3-194">W tym przykładzie pliki po operacji roboczych są ładowane z folderu *Lokalizacja.*</span><span class="sxs-lookup"><span data-stu-id="1fec3-194">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="1fec3-195">Implementowanie niestandardowej logiki znajdowania plików lokalizacji</span><span class="sxs-lookup"><span data-stu-id="1fec3-195">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="1fec3-196">Gdy bardziej złożona logika jest `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` potrzebna do zlokalizowania plików PO, interfejs może być zaimplementowany i zarejestrowany jako usługa.</span><span class="sxs-lookup"><span data-stu-id="1fec3-196">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="1fec3-197">Jest to przydatne, gdy pliki po zamówieniu mogą być przechowywane w różnych lokalizacjach lub gdy pliki muszą znajdować się w hierarchii folderów.</span><span class="sxs-lookup"><span data-stu-id="1fec3-197">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="1fec3-198">Używanie innego domyślnego wielosuralizowanego języka</span><span class="sxs-lookup"><span data-stu-id="1fec3-198">Using a different default pluralized language</span></span>

<span data-ttu-id="1fec3-199">Pakiet zawiera `Plural` metodę rozszerzenia, która jest specyficzna dla dwóch form liczby mnogiej.</span><span class="sxs-lookup"><span data-stu-id="1fec3-199">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="1fec3-200">W przypadku języków wymagających większej liczby form mnogich należy utworzyć metodę rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="1fec3-200">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="1fec3-201">W przypadku metody rozszerzenia nie trzeba podawać żadnego pliku lokalizacji &mdash; dla domyślnego języka, w jakim oryginalne ciągi są już dostępne bezpośrednio w kodzie.</span><span class="sxs-lookup"><span data-stu-id="1fec3-201">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="1fec3-202">Można użyć bardziej `Plural(int count, string[] pluralForms, params object[] arguments)` ogólne przeciążenie, które akceptuje tablicy ciąg tłumaczeń.</span><span class="sxs-lookup"><span data-stu-id="1fec3-202">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>
