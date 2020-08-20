---
title: Konfigurowanie lokalizacji obiektu przenośnego w ASP.NET Core
author: sebastienros
description: W tym artykule wprowadzono przenośne pliki obiektów i instrukcje dotyczące korzystania z nich w aplikacji ASP.NET Coreej z podstawową platformą sadu.
ms.author: scaddie
ms.date: 09/26/2017
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
uid: fundamentals/portable-object-localization
ms.openlocfilehash: 3919909067593a93384661b37d67bf2d90cd7df8
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634530"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="a20aa-103">Konfigurowanie lokalizacji obiektu przenośnego w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a20aa-103">Configure portable object localization in ASP.NET Core</span></span>

<span data-ttu-id="a20aa-104">Autorzy [Sébastien ros](https://github.com/sebastienros) i [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="a20aa-104">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="a20aa-105">W tym artykule omówiono procedurę używania plików przenośnych obiektów (PO) w aplikacji ASP.NET Core z podstawową platformą [sadu](https://github.com/OrchardCMS/OrchardCore) .</span><span class="sxs-lookup"><span data-stu-id="a20aa-105">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="a20aa-106">**Uwaga:** Rdzeń sadu nie jest produktem firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="a20aa-106">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="a20aa-107">W związku z tym firma Microsoft nie zapewnia wsparcia dla tej funkcji.</span><span class="sxs-lookup"><span data-stu-id="a20aa-107">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="a20aa-108">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a20aa-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="a20aa-109">Co to jest plik ZZ?</span><span class="sxs-lookup"><span data-stu-id="a20aa-109">What is a PO file?</span></span>

<span data-ttu-id="a20aa-110">Pliki do zakupu są dystrybuowane jako pliki tekstowe zawierające przetłumaczone ciągi dla danego języka.</span><span class="sxs-lookup"><span data-stu-id="a20aa-110">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="a20aa-111">Niektóre zalety korzystania z plików *. resx* są następujące:</span><span class="sxs-lookup"><span data-stu-id="a20aa-111">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="a20aa-112">Pliki PO serwisie obsługują pluralizacja; pliki *resx* nie obsługują pluralizacja.</span><span class="sxs-lookup"><span data-stu-id="a20aa-112">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="a20aa-113">Pliki ZZ nie są kompilowane jak pliki *resx* .</span><span class="sxs-lookup"><span data-stu-id="a20aa-113">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="a20aa-114">W związku z tym wyspecjalizowane narzędzia i kroki kompilacji nie są wymagane.</span><span class="sxs-lookup"><span data-stu-id="a20aa-114">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="a20aa-115">Pliki PO pracy działają dobrze z narzędziami do edycji w trybie online.</span><span class="sxs-lookup"><span data-stu-id="a20aa-115">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="a20aa-116">Przykład</span><span class="sxs-lookup"><span data-stu-id="a20aa-116">Example</span></span>

<span data-ttu-id="a20aa-117">Poniżej znajduje się przykładowy plik w formacie PO, zawierający tłumaczenie dla dwóch ciągów w języku francuskim, w tym jeden z jego postacią w liczbie mnogiej:</span><span class="sxs-lookup"><span data-stu-id="a20aa-117">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="a20aa-118">*fr. ZZ*</span><span class="sxs-lookup"><span data-stu-id="a20aa-118">*fr.po*</span></span>

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

<span data-ttu-id="a20aa-119">W tym przykładzie użyto następującej składni:</span><span class="sxs-lookup"><span data-stu-id="a20aa-119">This example uses the following syntax:</span></span>

- <span data-ttu-id="a20aa-120">`#:`: Komentarz wskazujący kontekst ciągu do tłumaczenia.</span><span class="sxs-lookup"><span data-stu-id="a20aa-120">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="a20aa-121">Ten sam ciąg może być przetłumaczony inaczej w zależności od tego, gdzie jest używany.</span><span class="sxs-lookup"><span data-stu-id="a20aa-121">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="a20aa-122">`msgid`: Nieprzetłumaczony ciąg.</span><span class="sxs-lookup"><span data-stu-id="a20aa-122">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="a20aa-123">`msgstr`: Przetłumaczony ciąg.</span><span class="sxs-lookup"><span data-stu-id="a20aa-123">`msgstr`: The translated string.</span></span>

<span data-ttu-id="a20aa-124">W przypadku pomocy technicznej pluralizacja można zdefiniować więcej wpisów.</span><span class="sxs-lookup"><span data-stu-id="a20aa-124">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="a20aa-125">`msgid_plural`: Nieprzetłumaczony ciąg w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="a20aa-125">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="a20aa-126">`msgstr[0]`: Przetłumaczony ciąg dla przypadku 0.</span><span class="sxs-lookup"><span data-stu-id="a20aa-126">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="a20aa-127">`msgstr[N]`: Przetłumaczony ciąg dla przypadku N.</span><span class="sxs-lookup"><span data-stu-id="a20aa-127">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="a20aa-128">Specyfikację pliku PO stronie można znaleźć [tutaj](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span><span class="sxs-lookup"><span data-stu-id="a20aa-128">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="a20aa-129">Konfigurowanie obsługi plików PO zalogowaniu w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a20aa-129">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="a20aa-130">Ten przykład jest oparty na aplikacji ASP.NET Core MVC wygenerowanej na podstawie szablonu projektu programu Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="a20aa-130">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="a20aa-131">Odwoływanie się do pakietu</span><span class="sxs-lookup"><span data-stu-id="a20aa-131">Referencing the package</span></span>

<span data-ttu-id="a20aa-132">Dodaj odwołanie do `OrchardCore.Localization.Core` pakietu NuGet.</span><span class="sxs-lookup"><span data-stu-id="a20aa-132">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span> <span data-ttu-id="a20aa-133">Jest on dostępny w witrynie [MyGet](https://www.myget.org/) pod następującym źródłem pakietu: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span><span class="sxs-lookup"><span data-stu-id="a20aa-133">It's available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span>

<span data-ttu-id="a20aa-134">Plik *. csproj* zawiera teraz wiersz podobny do następującego (numer wersji może się różnić):</span><span class="sxs-lookup"><span data-stu-id="a20aa-134">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="a20aa-135">Rejestrowanie usługi</span><span class="sxs-lookup"><span data-stu-id="a20aa-135">Registering the service</span></span>

<span data-ttu-id="a20aa-136">Dodaj wymagane usługi do `ConfigureServices` metody *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a20aa-136">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="a20aa-137">Dodaj wymagane oprogramowanie pośredniczące do `Configure` metody *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a20aa-137">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="a20aa-138">Dodaj następujący kod do Razor wybranego widoku.</span><span class="sxs-lookup"><span data-stu-id="a20aa-138">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="a20aa-139">W tym przykładzie użyto *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a20aa-139">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="a20aa-140">`IViewLocalizer`Wystąpienie jest wstrzykiwane i używane do tłumaczenia tekstu "Hello World!".</span><span class="sxs-lookup"><span data-stu-id="a20aa-140">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="a20aa-141">Tworzenie pliku ZZ</span><span class="sxs-lookup"><span data-stu-id="a20aa-141">Creating a PO file</span></span>

<span data-ttu-id="a20aa-142">Utwórz plik o nazwie \* \<culture code> . ZZ\* w folderze głównym aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a20aa-142">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="a20aa-143">W tym przykładzie nazwa pliku to *fr. po* , ponieważ używany jest język francuski:</span><span class="sxs-lookup"><span data-stu-id="a20aa-143">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

<span data-ttu-id="a20aa-144">Ten plik przechowuje zarówno ciąg do przetłumaczenia, jak i ciąg przetłumaczony w języku francuskim.</span><span class="sxs-lookup"><span data-stu-id="a20aa-144">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="a20aa-145">W razie potrzeby tłumaczenia są przywracane do ich kultury nadrzędnej.</span><span class="sxs-lookup"><span data-stu-id="a20aa-145">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="a20aa-146">W tym przykładzie plik *fr. ZZ* jest używany, jeśli żądana kultura jest `fr-FR` lub `fr-CA` .</span><span class="sxs-lookup"><span data-stu-id="a20aa-146">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="a20aa-147">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="a20aa-147">Testing the application</span></span>

<span data-ttu-id="a20aa-148">Uruchom aplikację i przejdź do adresu URL `/Home/About` .</span><span class="sxs-lookup"><span data-stu-id="a20aa-148">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="a20aa-149">Tekst **Witaj świecie!**</span><span class="sxs-lookup"><span data-stu-id="a20aa-149">The text **Hello world!**</span></span> <span data-ttu-id="a20aa-150">jest wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="a20aa-150">is displayed.</span></span>

<span data-ttu-id="a20aa-151">Przejdź do adresu URL `/Home/About?culture=fr-FR` .</span><span class="sxs-lookup"><span data-stu-id="a20aa-151">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="a20aa-152">Tekst **Bonjour Le Monde!**</span><span class="sxs-lookup"><span data-stu-id="a20aa-152">The text **Bonjour le monde!**</span></span> <span data-ttu-id="a20aa-153">jest wyświetlany.</span><span class="sxs-lookup"><span data-stu-id="a20aa-153">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="a20aa-154">Pluralizacja</span><span class="sxs-lookup"><span data-stu-id="a20aa-154">Pluralization</span></span>

<span data-ttu-id="a20aa-155">Pliki ZZ obsługują formularze pluralizacja, co jest przydatne, gdy ten sam ciąg musi być przetłumaczony inaczej w oparciu o Kardynalność.</span><span class="sxs-lookup"><span data-stu-id="a20aa-155">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="a20aa-156">To zadanie jest wykonywane w sposób skomplikowany przez fakt, że każdy język definiuje reguły niestandardowe, aby wybrać ciąg, który ma być używany na podstawie kardynalności.</span><span class="sxs-lookup"><span data-stu-id="a20aa-156">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="a20aa-157">Pakiet lokalizacji sadu udostępnia interfejs API umożliwiający automatyczne wywoływanie różnych formularzy w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="a20aa-157">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="a20aa-158">Tworzenie plików pluralizacja PO</span><span class="sxs-lookup"><span data-stu-id="a20aa-158">Creating pluralization PO files</span></span>

<span data-ttu-id="a20aa-159">Dodaj następującą zawartość do wspomnianego wcześniej pliku *fr. ZZ* :</span><span class="sxs-lookup"><span data-stu-id="a20aa-159">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="a20aa-160">Zobacz, [co to jest plik ZZ?](#what-is-a-po-file) , aby uzyskać wyjaśnienie, co reprezentuje każdy wpis w tym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="a20aa-160">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="a20aa-161">Dodawanie języka przy użyciu różnych formularzy pluralizacja</span><span class="sxs-lookup"><span data-stu-id="a20aa-161">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="a20aa-162">W poprzednim przykładzie użyto następujących ciągów w języku angielskim i francuskim.</span><span class="sxs-lookup"><span data-stu-id="a20aa-162">English and French strings were used in the previous example.</span></span> <span data-ttu-id="a20aa-163">W języku angielskim i francuskim istnieją tylko dwa formularze pluralizacja i współdzielą te same reguły formularza, co oznacza, że Kardynalność jednej z nich jest zamapowana na pierwszą formę w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="a20aa-163">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="a20aa-164">Każda inna Kardynalność jest mapowana na drugą formę w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="a20aa-164">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="a20aa-165">Nie wszystkie języki mają te same reguły.</span><span class="sxs-lookup"><span data-stu-id="a20aa-165">Not all languages share the same rules.</span></span> <span data-ttu-id="a20aa-166">Jest to zilustrowane w języku czeskim, który ma trzy formy w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="a20aa-166">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="a20aa-167">Utwórz `cs.po` plik w następujący sposób i zwróć uwagę na to, jak pluralizacja potrzebuje trzech różnych tłumaczeń:</span><span class="sxs-lookup"><span data-stu-id="a20aa-167">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

<span data-ttu-id="a20aa-168">Aby zaakceptować lokalizacje czeskie, należy dodać `"cs"` do listy obsługiwanych kultur w `ConfigureServices` metodzie:</span><span class="sxs-lookup"><span data-stu-id="a20aa-168">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

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

<span data-ttu-id="a20aa-169">Edytuj plik *views/Home/about. cshtml* , aby renderować zlokalizowane ciągi w liczbie mnogiej dla kilku kardynalnych:</span><span class="sxs-lookup"><span data-stu-id="a20aa-169">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="a20aa-170">**Uwaga:** W świecie rzeczywistym, zmienna byłaby używana do reprezentowania liczby.</span><span class="sxs-lookup"><span data-stu-id="a20aa-170">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="a20aa-171">W tym miejscu powtarzamy ten sam kod z trzema różnymi wartościami, aby uwidocznić konkretny przypadek.</span><span class="sxs-lookup"><span data-stu-id="a20aa-171">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="a20aa-172">Po przełączeniu kultur widoczne są następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="a20aa-172">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="a20aa-173">Instrukcję `/Home/About`:</span><span class="sxs-lookup"><span data-stu-id="a20aa-173">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="a20aa-174">Instrukcję `/Home/About?culture=fr`:</span><span class="sxs-lookup"><span data-stu-id="a20aa-174">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="a20aa-175">Instrukcję `/Home/About?culture=cs`:</span><span class="sxs-lookup"><span data-stu-id="a20aa-175">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="a20aa-176">Należy pamiętać, że w przypadku kultury Czeskiej trzy tłumaczenia są różne.</span><span class="sxs-lookup"><span data-stu-id="a20aa-176">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="a20aa-177">Kultury francuskie i angielskie korzystają z tej samej konstrukcji dla dwóch ostatnich przetłumaczonych ciągów.</span><span class="sxs-lookup"><span data-stu-id="a20aa-177">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="a20aa-178">Zaawansowane zadania</span><span class="sxs-lookup"><span data-stu-id="a20aa-178">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="a20aa-179">Contextualizing ciągi</span><span class="sxs-lookup"><span data-stu-id="a20aa-179">Contextualizing strings</span></span>

<span data-ttu-id="a20aa-180">Aplikacje często zawierają ciągi do tłumaczenia w kilku miejscach.</span><span class="sxs-lookup"><span data-stu-id="a20aa-180">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="a20aa-181">Ten sam ciąg może mieć inne tłumaczenie w określonych lokalizacjach w ramach aplikacji ( Razor widoków lub plików klas).</span><span class="sxs-lookup"><span data-stu-id="a20aa-181">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="a20aa-182">Plik ZZ obsługuje pojęcie kontekstu pliku, który może służyć do kategoryzowania reprezentowanego ciągu.</span><span class="sxs-lookup"><span data-stu-id="a20aa-182">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="a20aa-183">Przy użyciu kontekstu pliku ciąg może być przetłumaczony inaczej, w zależności od kontekstu pliku (lub braku kontekstu pliku).</span><span class="sxs-lookup"><span data-stu-id="a20aa-183">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="a20aa-184">Usługi lokalizowania PO stronie używają nazwy pełnej klasy lub widoku, który jest używany podczas tłumaczenia ciągu.</span><span class="sxs-lookup"><span data-stu-id="a20aa-184">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="a20aa-185">Jest to realizowane przez ustawienie wartości `msgctxt` wpisu.</span><span class="sxs-lookup"><span data-stu-id="a20aa-185">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="a20aa-186">Rozważmy drobne dodanie do poprzedniego przykładu *fr. po* .</span><span class="sxs-lookup"><span data-stu-id="a20aa-186">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="a20aa-187">RazorWidok znajdujący się w *widokach/Home/about. cshtml* można zdefiniować jako kontekst pliku przez ustawienie `msgctxt` wartości wpisu zastrzeżonego:</span><span class="sxs-lookup"><span data-stu-id="a20aa-187">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="a20aa-188">Z `msgctxt` ustawioną opcją, tłumaczenie tekstu odbywa się podczas nawigowania do `/Home/About?culture=fr-FR` .</span><span class="sxs-lookup"><span data-stu-id="a20aa-188">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="a20aa-189">Tłumaczenie nie będzie odbywać się podczas nawigowania do `/Home/Contact?culture=fr-FR` .</span><span class="sxs-lookup"><span data-stu-id="a20aa-189">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="a20aa-190">Gdy żaden konkretny wpis nie jest dopasowany do danego kontekstu pliku, mechanizm rezerwowy elementu sadu rdzeń szuka odpowiedniego pliku.</span><span class="sxs-lookup"><span data-stu-id="a20aa-190">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="a20aa-191">Przy założeniu, że nie określono określonego kontekstu pliku dla *widoków/Home/Contact. cshtml*, przechodzenie do `/Home/Contact?culture=fr-FR` ładowania pliku ZZ, takiego jak:</span><span class="sxs-lookup"><span data-stu-id="a20aa-191">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="a20aa-192">Zmiana lokalizacji plików ZZ</span><span class="sxs-lookup"><span data-stu-id="a20aa-192">Changing the location of PO files</span></span>

<span data-ttu-id="a20aa-193">Domyślną lokalizację plików PO stronie można zmienić w `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a20aa-193">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="a20aa-194">W tym przykładzie pliki PO załadowaniu są ładowane z folderu *lokalizacji* .</span><span class="sxs-lookup"><span data-stu-id="a20aa-194">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="a20aa-195">Implementowanie logiki niestandardowej do znajdowania plików lokalizacyjnych</span><span class="sxs-lookup"><span data-stu-id="a20aa-195">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="a20aa-196">Gdy do lokalizowania plików jest wymagana bardziej złożona logika, `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interfejs może zostać zaimplementowany i zarejestrowany jako usługa.</span><span class="sxs-lookup"><span data-stu-id="a20aa-196">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="a20aa-197">Jest to przydatne, gdy pliki do zakupu mogą być przechowywane w różnych lokalizacjach lub w przypadku, gdy pliki muszą znajdować się w hierarchii folderów.</span><span class="sxs-lookup"><span data-stu-id="a20aa-197">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="a20aa-198">Używanie innego domyślnego języka w liczbie mnogiej</span><span class="sxs-lookup"><span data-stu-id="a20aa-198">Using a different default pluralized language</span></span>

<span data-ttu-id="a20aa-199">Pakiet zawiera `Plural` metodę rozszerzenia, która jest specyficzna dla dwóch form w liczbie mnogiej.</span><span class="sxs-lookup"><span data-stu-id="a20aa-199">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="a20aa-200">W przypadku języków wymagających większej liczby formularzy w liczbie mnogiej Utwórz metodę rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="a20aa-200">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="a20aa-201">Przy użyciu metody rozszerzającej nie trzeba podawać pliku lokalizacji dla języka domyślnego &mdash; , oryginalne ciągi są już dostępne bezpośrednio w kodzie.</span><span class="sxs-lookup"><span data-stu-id="a20aa-201">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="a20aa-202">Można użyć bardziej ogólnego przeciążenia, `Plural(int count, string[] pluralForms, params object[] arguments)` które akceptuje tablicę ciągów tłumaczeń.</span><span class="sxs-lookup"><span data-stu-id="a20aa-202">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>
