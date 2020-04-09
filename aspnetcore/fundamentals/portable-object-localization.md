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
# <a name="configure-portable-object-localization-in-aspnet-core"></a>Konfigurowanie lokalizacji obiektów przenośnych w ASP.NET Core

Przez [Sébastien Ros](https://github.com/sebastienros) i [Scott Addie](https://twitter.com/Scott_Addie)

W tym artykule oprzeszą się od kroków dotyczących używania plików obiektów przenośnych (PO) w aplikacji ASP.NET Core z platformą [Orchard Core.](https://github.com/OrchardCMS/OrchardCore)

**Uwaga:** Orchard Core nie jest produktem firmy Microsoft. W związku z tym firma Microsoft nie zapewnia obsługi tej funkcji.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="what-is-a-po-file"></a>Co to jest plik po zamówić?

Pliki PO są dystrybuowane jako pliki tekstowe zawierające przetłumaczone ciągi dla danego języka. Niektóre zalety korzystania z plików PO zamiast *.resx* plików obejmują:
- Pliki PO obsługują pluralizm; *Pliki .resx* nie obsługują pluralizacji.
- Pliki PO nie są kompilowane jak pliki *.resx.* W związku z tym specjalistyczne kroki narzędzi i kompilacji nie są wymagane.
- Pliki PO działają dobrze dzięki narzędziom do edycji online.

### <a name="example"></a>Przykład

Oto przykładowy plik PO zawierający tłumaczenie dla dwóch ciągów w języku francuskim, w tym jednego z jego postacią liczby mnogiej:

*fr.po*

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

W tym przykładzie użyto następującej składni:

- `#:`: Komentarz wskazujący kontekst ciągu do przetłumaczenia. Ten sam ciąg może być tłumaczony inaczej w zależności od tego, gdzie jest używany.
- `msgid`: Nieprzetłumaczony ciąg.
- `msgstr`: Przetłumaczony ciąg.

W przypadku obsługi pluralizacji można zdefiniować więcej wpisów.

- `msgid_plural`: Nieprzetłumaczony ciąg mnogi.
- `msgstr[0]`: Przetłumaczony ciąg dla sprawy 0.
- `msgstr[N]`: Przetłumaczony ciąg dla sprawy N.

Specyfikację pliku PO można znaleźć [tutaj](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).

## <a name="configuring-po-file-support-in-aspnet-core"></a>Konfigurowanie obsługi plików PO w ASP.NET Core

W tym przykładzie jest oparty na ASP.NET core aplikacji MVC generowane z szablonu projektu programu Visual Studio 2017.

### <a name="referencing-the-package"></a>Odwoływanie się do pakietu

Dodaj odwołanie do `OrchardCore.Localization.Core` pakietu NuGet. Jest on dostępny na [MyGet](https://www.myget.org/) w następującym źródle pakietu:https://www.myget.org/F/orchardcore-preview/api/v3/index.json

Plik *csproj* zawiera teraz wiersz podobny do następującego (numer wersji może się różnić):

[!code-xml[](localization/sample/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a>Rejestracja usługi

Dodaj wymagane usługi do `ConfigureServices` metody *Startup.cs:*

[!code-csharp[](localization/sample/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

Dodaj wymagane oprogramowanie pośredniczące `Configure` do metody *Startup.cs:*

[!code-csharp[](localization/sample/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

Dodaj następujący kod do wybranego widoku Razor. *About.cshtml* jest używany w tym przykładzie.

[!code-cshtml[](localization/sample/POLocalization/Views/Home/About.cshtml)]

Instancja `IViewLocalizer` jest wstrzykiwana i używana do tłumaczenia tekstu "Hello world!".

### <a name="creating-a-po-file"></a>Tworzenie pliku PO

Utwórz plik o nazwie * \<kod kultury>.po* w folderze głównym aplikacji. W tym przykładzie nazwa pliku jest *fr.po,* ponieważ używany jest język francuski:

[!code-text[](localization/sample/POLocalization/fr.po)]

Ten plik przechowuje zarówno ciąg do tłumaczenia, jak i ciąg przetłumaczony na język francuski. Tłumaczenia powrócić do ich kultury nadrzędnej, jeśli to konieczne. W tym przykładzie plik *fr.po* jest używany, `fr-FR` `fr-CA`jeśli żądana kultura jest lub .

### <a name="testing-the-application"></a>Testowanie aplikacji

Uruchom aplikację i przejdź do `/Home/About`adresu URL . Tekst **Hello world!** zostanie wyświetlony komunikat.

Przejdź do `/Home/About?culture=fr-FR`adresu URL . Tekst **Bonjour le monde!** zostanie wyświetlony komunikat.

## <a name="pluralization"></a>Pluralizm

Pliki PO obsługują formularze pluralizacji, co jest przydatne, gdy ten sam ciąg musi być tłumaczony inaczej na podstawie kardynalności. To zadanie jest skomplikowane przez fakt, że każdy język definiuje reguły niestandardowe, aby wybrać ciąg do użycia na podstawie kardynalności.

Pakiet Lokalizacji sadu udostępnia interfejs API do automatycznego wywoływania tych różnych form w liczbie mnogiej.

### <a name="creating-pluralization-po-files"></a>Tworzenie plików pluralizacji po

Dodaj następującą zawartość do wcześniej wymienionego pliku *fr.po:*

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

Zobacz [Co to jest plik po zamówieniu?](#what-is-a-po-file)

### <a name="adding-a-language-using-different-pluralization-forms"></a>Dodawanie języka przy użyciu różnych formularzy pluralizacji

W poprzednim przykładzie użyto ciągów angielskich i francuskich. Angielski i francuski mają tylko dwie formy pluralizacji i mają te same reguły formularza, co oznacza, że kardynalność jednego jest mapowana na pierwszą formę mnogą. Każda inna kardynalność jest mapowana na drugą formę mnogą.

Nie wszystkie języki mają te same reguły. Jest to zilustrowane językiem czeskim, który ma trzy formy mnogie.

Utwórz `cs.po` plik w następujący sposób i zanotuj, jak pluralizacja wymaga trzech różnych tłumaczeń:

[!code-text[](localization/sample/POLocalization/cs.po)]

Aby zaakceptować czeskie `"cs"` lokalizacji, dodaj do listy obsługiwanych kultur w metodzie: `ConfigureServices`

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

Edytuj plik *Views/Home/About.cshtml,* aby renderować zlokalizowane ciągi mnogie dla kilku kardynalizacji:

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

**Uwaga:** W scenariuszu rzeczywistych zmienna będzie używana do reprezentowania liczby. W tym miejscu powtarzamy ten sam kod z trzema różnymi wartościami, aby udostępnić bardzo konkretny przypadek.

Po przełączeniu kultur zobaczysz następujące elementy:

Instrukcję `/Home/About`:

```html
There is one item.
There are 2 items.
There are 5 items.
```

Instrukcję `/Home/About?culture=fr`:

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

Instrukcję `/Home/About?culture=cs`:

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

Należy zauważyć, że dla kultury czeskiej, trzy tłumaczenia są różne. Kultury francuskie i angielskie mają tę samą konstrukcję dla dwóch ostatnich przetłumaczonych strun.

## <a name="advanced-tasks"></a>Zaawansowane zadania

### <a name="contextualizing-strings"></a>Ciągi kontekstowe

Aplikacje często zawierają ciągi do przetłumaczenia w kilku miejscach. Ten sam ciąg może mieć inne tłumaczenie w niektórych lokalizacjach w aplikacji (widoki Razor lub pliki klas). Plik po obsługuje pojęcie kontekstu pliku, który może służyć do kategoryzowanie reprezentowanego ciągu. Za pomocą kontekstu pliku ciąg może być tłumaczony inaczej, w zależności od kontekstu pliku (lub braku kontekstu pliku).

Usługi lokalizacji po używają nazwy pełnej klasy lub widoku, który jest używany podczas tłumaczenia ciągu. Jest to realizowane przez ustawienie `msgctxt` wartości we wpisie.

Należy wziąć pod uwagę niewielki dodatek do poprzedniego *przykładu fr.po.* Widok Razor znajdujący się w *widoku Views/Home/About.cshtml* można `msgctxt` zdefiniować jako kontekst pliku, ustawiając wartość wpisu zastrzeżonego:

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

W `msgctxt` przypadku zestawu jako takiego tłumaczenie tekstu `/Home/About?culture=fr-FR`odbywa się podczas przechodzenia do pliku . Tłumaczenie nie nastąpi podczas przechodzenia `/Home/Contact?culture=fr-FR`do .

Jeśli żaden określony wpis nie jest dopasowywał się do danego kontekstu pliku, mechanizm rezerwowy Orchard Core wyszukuje odpowiedni plik po bez kontekstu. Zakładając, że dla *views/Home/Contact.cshtml*nie zdefiniowano określonego `/Home/Contact?culture=fr-FR` kontekstu pliku, nawigacja do ładowania pliku PO, takiego jak:

[!code-text[](localization/sample/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a>Zmiana lokalizacji plików po zamówienia

Domyślną lokalizację plików PO można `ConfigureServices`zmienić w :

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

W tym przykładzie pliki po operacji roboczych są ładowane z folderu *Lokalizacja.*

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a>Implementowanie niestandardowej logiki znajdowania plików lokalizacji

Gdy bardziej złożona logika jest `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` potrzebna do zlokalizowania plików PO, interfejs może być zaimplementowany i zarejestrowany jako usługa. Jest to przydatne, gdy pliki po zamówieniu mogą być przechowywane w różnych lokalizacjach lub gdy pliki muszą znajdować się w hierarchii folderów.

### <a name="using-a-different-default-pluralized-language"></a>Używanie innego domyślnego wielosuralizowanego języka

Pakiet zawiera `Plural` metodę rozszerzenia, która jest specyficzna dla dwóch form liczby mnogiej. W przypadku języków wymagających większej liczby form mnogich należy utworzyć metodę rozszerzenia. W przypadku metody rozszerzenia nie trzeba podawać żadnego pliku lokalizacji &mdash; dla domyślnego języka, w jakim oryginalne ciągi są już dostępne bezpośrednio w kodzie.

Można użyć bardziej `Plural(int count, string[] pluralForms, params object[] arguments)` ogólne przeciążenie, które akceptuje tablicy ciąg tłumaczeń.
