---
title: Globalizacja i lokalizacja w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak ASP.NET Core zapewnia usługi i oprogramowanie pośredniczące do lokalizowania zawartości w różnych językach i kulturach.
ms.author: riande
ms.date: 11/30/2019
uid: fundamentals/localization
ms.openlocfilehash: b175354220a8a71c029e005f27443d5a72749a11
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662121"
---
# <a name="globalization-and-localization-in-aspnet-core"></a>Globalizacja i lokalizacja w ASP.NET Core

Rick [Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), Bart [Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/)i [Hisham Bin Ateya](https://twitter.com/hishambinateya)

Utworzenie wielojęzycznej witryny z ASP.NET Core pozwoli Twojej witrynie dotrzeć do szerszego grona odbiorców. ASP.NET Core zapewnia usługi i oprogramowanie pośredniczące do lokalizacji w różnych językach i kulturach.

Internacjonalizacja obejmuje [globalizację](/dotnet/api/system.globalization) i [lokalizację.](/dotnet/standard/globalization-localization/localization) Globalizacja to proces projektowania aplikacji, które obsługują różne kultury. Globalizacja dodaje obsługę danych wejściowych, wyświetlanych i wyjściowych zdefiniowanego zestawu skryptów językowych, które odnoszą się do określonych obszarów geograficznych.

Lokalizacja to proces dostosowywania zglobalizowanej aplikacji, która została już przetworzona pod kątem lokalizacji, do określonej kultury/ustawień regionalnych. Aby uzyskać więcej informacji, zobacz **terminy globalizacji i lokalizacji** na końcu tego dokumentu.

Lokalizacja aplikacji obejmuje następujące czynności:

1. Umiejscowienie zawartości aplikacji

2. Zapewnianie zlokalizowanych zasobów dla obsługiwanych języków i kultur

3. Implementowanie strategii wyboru języka/kultury dla każdego żądania

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="make-the-apps-content-localizable"></a>Umiejscowienie zawartości aplikacji

Wprowadzony w ASP.NET Core `IStringLocalizer` i `IStringLocalizer<T>` zostały zaprojektowany w celu zwiększenia wydajności podczas tworzenia zlokalizowanych aplikacji. `IStringLocalizer`używa [ResourceManager](/dotnet/api/system.resources.resourcemanager) i [ResourceReader](/dotnet/api/system.resources.resourcereader) do zapewnienia zasobów specyficznych dla kultury w czasie wykonywania. Prosty interfejs ma indeksatora `IEnumerable` i do zwracania zlokalizowanych ciągów. `IStringLocalizer`nie wymaga przechowywania domyślnych ciągów języka w pliku zasobów. Można opracować aplikację przeznaczoną do lokalizacji i nie trzeba tworzyć plików zasobów na wczesnym etapie rozwoju. Poniższy kod pokazuje, jak zawinąć ciąg "O tytule" dla lokalizacji.

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

W powyższym kodzie `IStringLocalizer<T>` implementacja pochodzi z [iniekcji zależności](dependency-injection.md). Jeśli zlokalizowana wartość "O tytule" nie zostanie znaleziona, zwracany jest klucz indeksatora, czyli ciąg "O tytule". Można pozostawić domyślne ciągi literał języka w aplikacji i zawinąć je w lokalizatorze, dzięki czemu można skupić się na tworzeniu aplikacji. Programujesz aplikację przy pracy w domyślnym języku i przygotowujesz ją do kroku lokalizacji bez uprzedniego utworzenia domyślnego pliku zasobów. Alternatywnie można użyć tradycyjnego podejścia i podać klucz do pobrania domyślnego ciągu języka. Dla wielu deweloperów nowy przepływ pracy nie ma domyślnego pliku *.resx* języka i po prostu zawijania literałów ciąg może zmniejszyć obciążenie związane z lokalizowaniem aplikacji. Inni deweloperzy wolą tradycyjny przepływ pracy, ponieważ może to ułatwić pracę z dłuższymi literałami ciągów i ułatwić aktualizowanie zlokalizowanych ciągów.

Użyj `IHtmlLocalizer<T>` implementacji dla zasobów, które zawierają html. `IHtmlLocalizer`HTML koduje argumenty, które są sformatowane w ciągu zasobu, ale nie koduje html samego ciągu zasobu. W przykładzie wyróżnionym poniżej `name` tylko wartość parametru jest zakodowana w formacie HTML.

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

**Uwaga:** Zazwyczaj chcesz zlokalizować tylko tekst, a nie html.

Na najniższym poziomie, `IStringLocalizerFactory` można wyjść z [iniekcji zależności:](dependency-injection.md)

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

Powyższy kod pokazuje każdą z dwóch metod tworzenia fabrycznego.

Można podzielić zlokalizowane ciągi według kontrolera, obszaru lub mieć tylko jeden kontener. W przykładowej aplikacji klasy fikcyjnej o nazwie `SharedResource` jest używany dla zasobów udostępnionych.

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

Niektórzy deweloperzy `Startup` używają tej klasy do zawierania ciągów globalnych lub udostępnionych. W poniższym przykładzie stosuje się `InfoController` lokalizatory: `SharedResource`

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>Zobacz lokalizację

Usługa `IViewLocalizer` udostępnia zlokalizowane ciągi dla [widoku](xref:mvc/views/overview). Klasa `ViewLocalizer` implementuje ten interfejs i znajduje lokalizację zasobu ze ścieżki pliku widoku. Poniższy kod pokazuje, jak używać `IViewLocalizer`domyślnej implementacji:

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

Domyślna implementacja `IViewLocalizer` znajduje plik zasobu na podstawie nazwy pliku widoku. Nie ma opcji użycia globalnego udostępnionego pliku zasobów. `ViewLocalizer`implementuje localizer `IHtmlLocalizer`przy użyciu , więc Razor nie koduje HTML zlokalizowane ciąg. Można sparametryzować ciągi zasobów i `IViewLocalizer` kodować parametry HTML, ale nie ciąg zasobu. Należy wziąć pod uwagę następujące znaczniki Razor:

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Francuski plik zasobów może zawierać następujące elementy:

| Klucz | Wartość |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

Renderowany widok będzie zawierał znaczniki HTML z pliku zasobu.

**Uwaga:** Zazwyczaj chcesz zlokalizować tylko tekst, a nie html.

Aby użyć udostępnionego pliku zasobu `IHtmlLocalizer<T>`w widoku, wstrzyknąć:

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Lokalizacjaanotacji danych

Komunikaty o błędach DataAnnotations są zlokalizowane za pomocą `IStringLocalizer<T>`pliku . Korzystając z `ResourcesPath = "Resources"`tej opcji, `RegisterViewModel` komunikaty o błędach mogą być przechowywane w jednej z następujących ścieżek:

* *Zasoby/ViewModels.Account.RegisterViewModel.fr.resx*
* *Zasoby/ViewModels/Account/RegisterViewModel.fr.resx*

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

W ASP.NET Core MVC 1.1.0 i nowsze, atrybuty nie sprawdzania poprawności są zlokalizowane. ASP.NET Core MVC 1.0 **nie** wyszukuje zlokalizowanych ciągów atrybutów nieuwzględniania.

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a>Używanie jednego ciągu zasobu dla wielu klas

Poniższy kod pokazuje, jak używać jednego ciągu zasobu dla atrybutów sprawdzania poprawności z wieloma klasami:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

W poprzednim kodzie `SharedResource` jest klasa odpowiadająca resx, gdzie są przechowywane komunikaty sprawdzania poprawności. Dzięki takiemu podejściu dataannotations będzie używać `SharedResource`tylko , a nie zasobu dla każdej klasy.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>Zapewnianie zlokalizowanych zasobów dla obsługiwanych języków i kultur

### <a name="supportedcultures-and-supporteduicultures"></a>Wspieranekultury i wspieranekultury

ASP.NET Core umożliwia określenie dwóch wartości `SupportedCultures` kultury `SupportedUICultures`i . [CultureInfo](/dotnet/api/system.globalization.cultureinfo) Obiekt `SupportedCultures` dla określa wyniki funkcji zależnych od kultury, takich jak data, godzina, liczba i formatowanie waluty. `SupportedCultures`określa również kolejność sortowania tekstu, konwencje wielkości liter i porównania ciągów. Zobacz [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) aby uzyskać więcej informacji na temat sposobu serwera pobiera kultury. Określa, `SupportedUICultures` które tłumaczy ciągi (z plików *resx)* są wyszukane przez [ResourceManager](/dotnet/api/system.resources.resourcemanager). Po `ResourceManager` prostu wyszukuje ciągi specyficzne `CurrentUICulture`dla kultury, które są określane przez . Każdy wątek `CurrentCulture` w `CurrentUICulture` .NET ma i obiektów. ASP.NET Core sprawdza te wartości podczas renderowania funkcji zależnych od kultury. Na przykład jeśli kultura bieżącego wątku jest ustawiona na "en-US" (angielski, Stany Zjednoczone), `DateTime.Now.ToLongDateString()` wyświetla "Czwartek, 18 lutego 2016", ale jeśli `CurrentCulture` jest ustawiona na "es-ES" (hiszpański, Hiszpania), wyjście będzie "jueves, 18 de febrero de 2016".

## <a name="resource-files"></a>Pliki zasobów

Plik zasobów jest przydatnym mechanizmem oddzielania ciągów zlokalizowanych od kodu. Przetłumaczone ciągi dla języka nie domyślnego są izolowane pliki zasobów *.resx.* Na przykład można utworzyć hiszpański plik zasobów o nazwie *Welcome.es.resx* zawierający przetłumaczone ciągi. "es" to kod języka hiszpańskiego. Aby utworzyć ten plik zasobu w programie Visual Studio:

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy folder, który będzie zawierał plik zasobu > **Dodaj** > **nowy element**.

    ![Zagnieżdżone menu kontekstowe: W Eksploratorze rozwiązań menu kontekstowe jest otwarte dla zasobów. Drugie menu kontekstowe jest otwarte dla polecenia Dodaj z wyróżnionym poleceniem Nowy element.](localization/_static/newi.png)

2. W polu **Wyszukaj zainstalowane szablony** wprowadź "zasób" i nazwij plik.

    ![Okno dialogowe Dodawanie nowego elementu](localization/_static/res.png)

3. Wprowadź wartość klucza (ciąg macierzysty) w kolumnie **Nazwa** i przetłumaczonym ciągu w kolumnie **Wartość.**

    ![Plik Welcome.es.resx (plik zasobów powitalnych dla języka hiszpańskiego) ze słowem Hello w kolumnie Nazwa i słowem Hola (Hello in Spanish) w kolumnie Wartość](localization/_static/hola.png)

    Program Visual Studio pokazuje plik *Welcome.es.resx.*

    ![Eksplorator rozwiązań z plikiem zasobów Języka Hiszpańskiego powitanego (es)](localization/_static/se.png)

## <a name="resource-file-naming"></a>Nazewnictwo plików zasobów

Zasoby są nazwane dla pełnej nazwy typu ich klasy minus nazwa zestawu. Na przykład francuski zasób w projekcie, którego głównym zestawem jest `LocalizationWebsite.Web.dll` dla klasy, `LocalizationWebsite.Web.Startup` będzie miał nazwę *Startup.fr.resx*. Zasób dla `LocalizationWebsite.Web.Controllers.HomeController` klasy będzie nazwany *Controllers.HomeController.fr.resx*. Jeśli obszar nazw klasy docelowej nie jest taki sam jak nazwa zestawu, będziesz potrzebować pełnej nazwy typu. Na przykład w przykładowym projekcie zasób dla tego typu `ExtraNamespace.Tools` będzie miał nazwę *ExtraNamespace.Tools.fr.resx*.

W przykładowym projekcie `ConfigureServices` metoda `ResourcesPath` ustawia na "Zasoby", więc ścieżka względna projektu dla francuskiego pliku zasobów kontrolera macierzystego to *Resources/Controllers.HomeController.fr.resx*. Alternatywnie można użyć folderów do organizowania plików zasobów. Dla kontrolera domowego ścieżką będą *zasoby/kontrolery/strona GłównaController.fr.resx*. Jeśli nie użyjesz `ResourcesPath` tej opcji, plik *.resx* przejdzie do katalogu podstawowego projektu. Plik zasobu `HomeController` będzie miał nazwę *Controllers.HomeController.fr.resx*. Wybór konwencji nazewnictwa kropki lub ścieżki zależy od sposobu organizowania plików zasobów.

| Nazwa zasobu | Nazewnictwo kropki lub ścieżki |
| ------------   | ------------- |
| Zasoby/Controllers.HomeController.fr.resx | Dot  |
| Zasoby/Kontrolery/HomeController.fr.resx  | Ścieżka |
|    |     |

Pliki zasobów `@inject IViewLocalizer` używające w widokach Razor mają podobny wzorzec. Plik zasobów dla widoku można nazwać przy użyciu nazewnictwa kropki lub nazewnictwa ścieżek. Razor zobacz pliki zasobów naśladować ścieżkę ich skojarzonego pliku widoku. Zakładając, że `ResourcesPath` ustawimy na "Zasoby", francuski plik zasobów skojarzony z widokiem *Views/Home/About.cshtml* może być jednym z następujących:

* Zasoby/Widoki/Strona główna/About.fr.resx

* Zasoby/Views.Home.About.fr.resx

Jeśli nie użyjesz `ResourcesPath` tej opcji, plik *.resx* dla widoku będzie znajdować się w tym samym folderze co widok.

### <a name="rootnamespaceattribute"></a>Atrybut RootNamespaceAttribute 

Atrybut [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) udostępnia główny obszar nazw zestawu, gdy główna przestrzeń nazw zestawu jest inna niż nazwa zestawu. 

> [!WARNING]
> Może to nastąpić, gdy nazwa projektu nie jest prawidłowym identyfikatorem platformy .NET. Na `my-project-name.csproj` przykład użyje głównego `my_project_name` obszaru nazw `my-project-name` i nazwy zestawu prowadzącego do tego błędu. 

Jeśli główna przestrzeń nazw zestawu jest inna niż nazwa zestawu:

* Lokalizacja nie działa domyślnie.
* Lokalizacja kończy się niepowodzeniem ze względu na sposób, w jaki zasoby są przeszukiwane w ramach zestawu. `RootNamespace`jest wartością czasu kompilacji, która nie jest dostępna dla procesu wykonywania. 

Jeśli `RootNamespace` jest inny `AssemblyName`niż , należy uwzględnić następujące w *AssemblyInfo.cs* (z wartościami parametrów zastąpionymi wartościami rzeczywistymi):

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

Poprzedni kod umożliwia pomyślne rozpoznawanie plików resx.

## <a name="culture-fallback-behavior"></a>Zachowanie rezerwowe kultury

Podczas wyszukiwania zasobu, lokalizacja angażuje się w "kultury rezerwy". Począwszy od żądanej kultury, jeśli nie znaleziono, powraca do kultury nadrzędnej tej kultury. Na marginesie [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) właściwość reprezentuje kultury nadrzędnej. Zwykle (ale nie zawsze) oznacza usunięcie krajowego oznaczenia z ISO. Na przykład dialekt języka hiszpańskiego używany w Meksyku to "es-MX". Ma rodzica "es"&mdash;hiszpański niespecyfyjniej dla każdego kraju.

Wyobraź sobie, że Twoja witryna otrzymuje żądanie zasobu "Powitalnego" przy użyciu kultury "fr-CA". System lokalizacji wyszukuje następujące zasoby w kolejności i wybiera pierwsze dopasowanie:

* *Welcome.fr-CA.resx*
* *Witamy.fr.resx*
* *Welcome.resx* (jeśli `NeutralResourcesLanguage` jest "fr-CA")

Na przykład po usunięciu ".fr" kultury projektowania i masz kultury ustawionej na francuski, domyślny plik zasobów jest odczytywany i ciągi są zlokalizowane. Menedżer zasobów wyznacza domyślny lub rezerwowy zasób, gdy nic nie spełnia żądanej kultury. Jeśli chcesz po prostu zwrócić klucz, gdy brakuje zasobu dla żądanej kultury nie może mieć domyślny plik zasobu.

### <a name="generate-resource-files-with-visual-studio"></a>Generowanie plików zasobów za pomocą programu Visual Studio

Jeśli utworzysz plik zasobów w programie Visual Studio bez kultury w nazwie pliku (na przykład *Welcome.resx),* Visual Studio utworzy klasę C# z właściwością dla każdego ciągu. To zwykle nie jest to, co chcesz z ASP.NET Core. Zazwyczaj nie masz domyślnego pliku zasobów *resx* (pliku *resx* bez nazwy kultury). Zalecamy utworzenie pliku *.resx* o nazwie kultury (na przykład *Welcome.fr.resx).* Podczas tworzenia pliku *.resx* o nazwie kultury program Visual Studio nie wygeneruje pliku klasy.

### <a name="add-other-cultures"></a>Dodawanie innych kultur

Każda kombinacja języka i kultury (inna niż domyślny język) wymaga unikatowego pliku zasobów. Pliki zasobów dla różnych kultur i ustawień regionalnych można tworzyć, tworząc nowe pliki zasobów, w których kody języków ISO są częścią nazwy pliku (na przykład **en-us**, **fr-ca**i **en-gb**). Te kody ISO są umieszczane między nazwą pliku a rozszerzeniem pliku *.resx,* jak w *Welcome.es-MX.resx* (hiszpański/Meksyk).

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>Implementowanie strategii wyboru języka/kultury dla każdego żądania

### <a name="configure-localization"></a>Konfigurowanie lokalizacji

Lokalizacja jest konfigurowana `Startup.ConfigureServices` w metodzie:

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* `AddLocalization`Dodaje usługi lokalizacji do kontenera usług. Powyższy kod ustawia również ścieżkę zasobów na "Zasoby".

* `AddViewLocalization`Dodaje obsługę zlokalizowanych plików widoku. W tym przykładowym widoku lokalizacja jest oparta na sufiksie pliku widoku. Na przykład "fr" w pliku *Index.fr.cshtml.*

* `AddDataAnnotationsLocalization`Dodaje obsługę zlokalizowanych komunikatów `DataAnnotations` sprawdzania poprawności za pośrednictwem `IStringLocalizer` abstrakcji.

### <a name="localization-middleware"></a>Oprogramowanie pośredniczące lokalizacji

Bieżąca kultura na żądanie jest ustawiona w oprogramowaniu [pośredniczącym](xref:fundamentals/middleware/index)lokalizacji . Oprogramowanie pośredniczące lokalizacji jest `Startup.Configure` włączone w metodzie. Oprogramowanie pośredniczące lokalizacji musi być skonfigurowane przed oprogramowaniem pośredniczącym, `app.UseMvcWithDefaultRoute()`które może sprawdzić kulturę żądania (na przykład ).

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

`UseRequestLocalization`inicjuje `RequestLocalizationOptions` obiekt. Na każde żądanie `RequestCultureProvider` lista `RequestLocalizationOptions` w jest wyliczona i pierwszy dostawca, który może pomyślnie określić kultury żądania jest używany. Dostawcy domyślni pochodzą `RequestLocalizationOptions` z klasy:

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

Lista domyślna przechodzi od najbardziej specyficznego do najmniej specyficznego. W dalszej części artykułu zobaczymy, jak można zmienić kolejność, a nawet dodać niestandardowego dostawcy kultury. Jeśli żaden z dostawców można określić `DefaultRequestCulture` kultury żądania, jest używany.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

Niektóre aplikacje będą używać ciągu zapytania, aby ustawić [kulturę i kulturę interfejsu użytkownika](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx). W przypadku aplikacji, które używają podejścia nagłówka cookie lub Accept-Language dodawanie ciągu zapytania do adresu URL jest przydatne do debugowania i testowania kodu. Domyślnie `QueryStringRequestCultureProvider` jest zarejestrowany jako pierwszy dostawca lokalizacji `RequestCultureProvider` na liście. Należy przekazać parametry `culture` ciągu `ui-culture`zapytania i . Poniższy przykład ustawia określonej kultury (język i region) do hiszpański/Meksyk:

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

Jeśli przekażesz tylko w`culture` jednym `ui-culture`z dwóch ( lub ), dostawca ciągu zapytania ustawi obie wartości przy użyciu tej, która została przekazana. Na przykład ustawienie tylko kultury ustawi `Culture` `UICulture`zarówno : i :

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a>CookieRequestKulturaProvider

Aplikacje produkcyjne często zapewniają mechanizm, aby ustawić kulturę z ASP.NET core kultury cookie. Użyj `MakeCookieValue` metody, aby utworzyć plik cookie.

Zwraca `CookieRequestCultureProvider` `DefaultCookieName` domyślną nazwę pliku cookie używaną do śledzenia preferowanych informacji o kulturze użytkownika. Domyślna nazwa `.AspNetCore.Culture`pliku cookie to .

Format pliku `c=%LANGCODE%|uic=%LANGCODE%`cookie `c` to `Culture` `uic` , `UICulture`gdzie jest i jest, na przykład:

    c=en-UK|uic=en-US

Jeśli określisz tylko jedną z informacji o kulturze i kultury interfejsu użytkownika, określona kultura będzie używana zarówno dla informacji o kulturze, jak i kultury interfejsu użytkownika.

### <a name="the-accept-language-http-header"></a>Nagłówek HTTP w języku akceptującym

[Nagłówek Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) jest ustawiany w większości przeglądarek i pierwotnie miał na celu określenie języka użytkownika. To ustawienie wskazuje, co przeglądarka została ustawiona do wysyłania lub została odziedziczona z podstawowego systemu operacyjnego. Nagłówek HTTP w języku akceptującym z żądania przeglądarki nie jest nieomylnym sposobem wykrywania preferowanego języka użytkownika (zobacz [Ustawianie preferencji językowych w przeglądarce).](https://www.w3.org/International/questions/qa-lang-priorities.en.php) Aplikacja produkcyjna powinna zawierać sposób dla użytkownika, aby dostosować ich wybór kultury.

### <a name="set-the-accept-language-http-header-in-ie"></a>Ustawianie nagłówka HTTP w języku akceptującym w języku IE

1. W ikonie koła zębatego naciśnij pozycję **Opcje internetowe**.

2. Naciśnij pozycję **Języki**.

    ![Opcje internetowe](localization/_static/lang.png)

3. Naciśnij pozycję **Ustaw preferencje językowe**.

4. Naciśnij pozycję **Dodaj język**.

5. Dodaj język.

6. Naciśnij język, a następnie naciśnij pozycję **Przenieś w górę**.

::: moniker range="> aspnetcore-3.1"
### <a name="the-content-language-http-header"></a>Nagłówek HTTP w języku zawartości

Nagłówek encji [Język zawartości:](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language)

 - Służy do opisywania języka(-ów) przeznaczonego dla odbiorców.
 - Umożliwia użytkownikowi rozróżnianie według preferowanego języka użytkownika.

Nagłówki encji są używane zarówno w żądaniach HTTP, jak i w odpowiedziach.

Nagłówek `Content-Language` można dodać, ustawiając `ApplyCurrentCultureToResponseHeaders`właściwość .

Dodawanie `Content-Language` nagłówka:

 - Umożliwia requestlocalizationMiddleware ustawić `Content-Language` nagłówek z `CurrentUICulture`.
 - Eliminuje konieczność jawnego ustawiania `Content-Language` nagłówka odpowiedzi.

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```
::: moniker-end

### <a name="use-a-custom-provider"></a>Korzystanie z niestandardowego dostawcy

Załóżmy, że chcesz umożliwić klientom przechowywanie ich języka i kultury w bazach danych. Można napisać dostawcę, aby wyszukać te wartości dla użytkownika. Poniższy kod pokazuje, jak dodać niestandardowego dostawcy:

::: moniker range="< aspnetcore-3.0"
```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```
::: moniker-end

Służy `RequestLocalizationOptions` do dodawania lub usuwania dostawców lokalizacji.

### <a name="set-the-culture-programmatically"></a>Ustaw kulturę programowo

Ten przykładowy projekt **Localization.StarterWeb** w [usłudze GitHub](https://github.com/aspnet/entropy) zawiera interfejs użytkownika, aby ustawić `Culture`plik . *Plik Views/Shared/_SelectLanguagePartial.cshtml* umożliwia wybranie kultury z listy obsługiwanych kultur:

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

Plik *Views/Shared/_SelectLanguagePartial.cshtml* jest dodawany do `footer` sekcji pliku układu, dzięki czemu będzie dostępny dla wszystkich widoków:

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

Metoda `SetLanguage` ustawia plik cookie kultury.

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

Nie można podłączyć *_SelectLanguagePartial.cshtml* do przykładowego kodu dla tego projektu. **Localization.StarterWeb** projektu w [usłudze GitHub](https://github.com/aspnet/entropy) ma kod do przepływu `RequestLocalizationOptions` do Razor częściowe za pośrednictwem [kontenera iniekcji zależności.](dependency-injection.md)

## <a name="model-binding-route-data-and-query-strings"></a>Dane marszruty wiązania modelu i ciągi zapytań

Zobacz [Zachowanie globalizacji danych marszruty wiązania modelu i ciągów zapytań](xref:mvc/models/model-binding#glob).

## <a name="globalization-and-localization-terms"></a>Terminy globalizacji i lokalizacji

Proces lokalizowania aplikacji wymaga również podstawowej wiedzy na temat odpowiednich zestawów znaków powszechnie używanych w nowoczesnym tworzeniu oprogramowania i zrozumienia problemów z nimi związanych. Chociaż wszystkie komputery przechowują tekst jako liczby (kody), różne systemy przechowują ten sam tekst przy użyciu różnych liczb. Proces lokalizacji odnosi się do tłumaczenia interfejsu użytkownika aplikacji (UI) dla określonej kultury/ustawień regionalnych.

[Lokalizacja](/dotnet/standard/globalization-localization/localizability-review) jest procesem pośrednim do sprawdzania, czy zglobalizowana aplikacja jest gotowa do lokalizacji.

Format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) dla nazwy `<languagecode2>-<country/regioncode2>`kultury `<languagecode2>` jest , `<country/regioncode2>` gdzie jest kod języka i jest kodem subkultury. Na przykład `es-CL` dla języka `en-US` hiszpańskiego (Chile), `en-AU` angielskiego (Stany Zjednoczone) i angielskiego (Australia). [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) to połączenie dwuliterowego kodu kultury małych liter ISO 639 skojarzonego z językiem oraz dwuliterowego kodu subkultury ISO 3166 skojarzonego z krajem lub regionem. Zobacz [Nazwa kultury językowej](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).

Internacjonalizacja jest często skracana do "I18N". Skrót przyjmuje pierwszą i ostatnią literę i liczbę liter między nimi, więc 18 oznacza liczbę liter między pierwszym "I" a ostatnim "N". To samo dotyczy globalizacji (G11N) i lokalizacji (L10N).

Warunki:

* Globalizacja (G11N): proces tworzenia aplikacji obsługuje różne języki i regiony.
* Lokalizacja (L10N): proces dostosowywania aplikacji dla danego języka i regionu.
* Internacjonalizacja (I18N): Opisuje zarówno globalizację, jak i lokalizację.
* Kultura: To język i, opcjonalnie, region.
* Kultura neutralna: kultura, która ma określony język, ale nie region. (na przykład "en", "es")
* Określona kultura: Kultura, która ma określony język i region. (na przykład "en-US", "en-GB", "es-CL")
* Kultura nadrzędna: kultura neutralna, która zawiera określoną kulturę. (na przykład "en" jest kulturą nadrzędną "en-US" i "en-GB")
* Ustawienia regionalne: Ustawienia regionalne są takie same jak kultury.

[!INCLUDE[](~/includes/localization/currency.md)]

::: moniker range=">= aspnetcore-3.0"
[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]
::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* [Localization.StarterWeb projekt](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) używany w artykule.
* [Globalizacja i lokalizowanie aplikacji .NET](/dotnet/standard/globalization-localization/index)
* [Zasoby w plikach resx](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Zestaw narzędzi wielojęzycznych aplikacji firmy Microsoft](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [Lokalizacja & generycznych](http://hishambinateya.com/localization-and-generics)
