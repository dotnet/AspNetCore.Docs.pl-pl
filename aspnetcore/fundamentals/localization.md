---
title: Globalizacja i lokalizacja w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak ASP.NET Core udostępnia usługi i oprogramowanie pośredniczące do lokalizowania zawartości w różnych językach i kulturach.
ms.author: riande
ms.date: 11/30/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/localization
ms.openlocfilehash: 1111c0c57baf5bfd52b26b553179338100b9ba79
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776568"
---
# <a name="globalization-and-localization-in-aspnet-core"></a>Globalizacja i lokalizacja w ASP.NET Core

[Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/)i [Hisham bin Ateya](https://twitter.com/hishambinateya)

Utworzenie wielojęzycznej witryny sieci Web z ASP.NET Core umożliwi witrynie dostęp do szerszego grona użytkowników. ASP.NET Core udostępnia usługi i oprogramowanie pośredniczące do lokalizowania w różnych językach i kulturach.

Między innymi są używane [globalizacji](/dotnet/api/system.globalization) i [lokalizacje](/dotnet/standard/globalization-localization/localization). Globalizacja to proces projektowania aplikacji, które obsługują różne kultury. Globalizacja dodaje obsługę danych wejściowych, wyświetlanych i wyjściowych zdefiniowanego zestawu skryptów języka, które odnoszą się do określonych obszarów geograficznych.

Lokalizacja to proces adaptacji aplikacji, która została już przetworzona w celu zlokalizowania, do określonej kultury lub ustawień regionalnych. Aby uzyskać więcej informacji **, zobacz sekcję globalizacja i warunki lokalizacji** na końcu tego dokumentu.

Lokalizacja aplikacji obejmuje następujące elementy:

1. Ustaw lokalizowalność zawartości aplikacji

2. Zapewnianie zlokalizowanych zasobów dla obsługiwanych języków i kultur

3. Zaimplementuj strategię, aby wybrać język/kulturę dla każdego żądania

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="make-the-apps-content-localizable"></a>Ustaw lokalizowalność zawartości aplikacji

Wprowadzono w ASP.NET Core `IStringLocalizer` i `IStringLocalizer<T>` zostały zaprojektowane w celu zwiększenia produktywności podczas tworzenia zlokalizowanych aplikacji. `IStringLocalizer`używa programu [ResourceManager](/dotnet/api/system.resources.resourcemanager) i [ResourceReader](/dotnet/api/system.resources.resourcereader) w celu zapewnienia zasobów specyficznych dla kultury w czasie wykonywania. Prosty interfejs ma indeksator i `IEnumerable` zwraca zlokalizowane ciągi. `IStringLocalizer`nie wymaga przechowywania w pliku zasobów domyślnych ciągów języka. Możesz tworzyć aplikacje przeznaczone do lokalizacji i nie musisz już tworzyć plików zasobów w fazie opracowywania. Poniższy kod przedstawia sposób zawijania ciągu "informacje o tytule" dla lokalizacji.

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

W powyższym kodzie `IStringLocalizer<T>` implementacja pochodzi z [iniekcji zależności](dependency-injection.md). Jeśli zlokalizowana wartość "informacje o tytule" nie zostanie znaleziona, zostanie zwrócony klucz indeksatora, czyli ciąg "informacje o tytule". Możesz pozostawić domyślne ciągi literałów języka w aplikacji i otoczyć je w lokalizatorze, aby można było skupić się na tworzeniu aplikacji. Tworzysz aplikację przy użyciu języka domyślnego i przygotujesz ją do kroku lokalizacji bez wcześniejszego tworzenia domyślnego pliku zasobów. Alternatywnie można użyć tradycyjnego podejścia i podać klucz do pobrania domyślnego ciągu języka. Dla wielu deweloperów nowy przepływ pracy nie ma domyślnego pliku języka *. resx* i po prostu zawijający literały ciągu może zmniejszyć obciążenie lokalizowania aplikacji. Inni deweloperzy będą wolą tradycyjne przepływy pracy, ponieważ ułatwiają one pracę z dłuższymi literałami ciągów i ułatwiają aktualizowanie zlokalizowanych ciągów.

Użyj `IHtmlLocalizer<T>` implementacji dla zasobów, które zawierają kod HTML. `IHtmlLocalizer`KOD HTML koduje argumenty, które są sformatowane w ciągu zasobu, ale nie kodu HTML samego samego ciągu zasobu. W przykładzie wyróżnionym poniżej tylko wartość `name` parametru jest zakodowana w formacie HTML.

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

**Uwaga:** Zazwyczaj chcesz zlokalizować tylko tekst, a nie HTML.

Na najniższym poziomie można uzyskać `IStringLocalizerFactory` możliwość [iniekcji zależności](dependency-injection.md):

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

Powyższy kod demonstruje każdą z dwóch metod tworzenia fabryk.

Zlokalizowane ciągi można podzielić według kontrolera, obszaru lub tylko jednego kontenera. W aplikacji przykładowej Klasa fikcyjna o nazwie `SharedResource` jest używana do współużytkowanych zasobów.

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

Niektórzy Deweloperzy używają klasy `Startup` , aby zawierały ciągi globalne lub udostępnione. W poniższym przykładzie są używane `InfoController` i `SharedResource` lokalizatory:

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>Wyświetl lokalizację

`IViewLocalizer` Usługa udostępnia zlokalizowane ciągi dla [widoku](xref:mvc/views/overview). `ViewLocalizer` Klasa implementuje ten interfejs i odnajduje lokalizację zasobu ze ścieżki pliku widoku. Poniższy kod pokazuje, jak używać domyślnej implementacji `IViewLocalizer`:

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

Domyślna implementacja programu `IViewLocalizer` znajduje plik zasobów na podstawie nazwy pliku widoku. Nie ma możliwości użycia globalnego pliku zasobów udostępnionych. `ViewLocalizer`implementuje lokalizatora przy użyciu `IHtmlLocalizer`, dlatego nie można KODOWAĆ w formacie HTML zlokalizowanego ciągu. Można Sparametryzuj ciągi zasobów i `IViewLocalizer` zakodować w kodzie HTML parametry, ale nie ciąg zasobu. Weź pod uwagę następujące znaczniki Razor:

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Plik zasobów francuski może zawierać następujące elementy:

| Klucz | Wartość |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

Renderowany widok będzie zawierać znacznik HTML z pliku zasobu.

**Uwaga:** Zazwyczaj chcesz zlokalizować tylko tekst, a nie HTML.

Aby użyć udostępnionego pliku zasobu w widoku, wstrzyknąć `IHtmlLocalizer<T>`:

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Lokalizacja adnotacji

Komunikaty o błędach DataAnnotations `IStringLocalizer<T>`są zlokalizowane przy użyciu. Korzystając z opcji `ResourcesPath = "Resources"`, komunikaty o błędach `RegisterViewModel` w programie mogą być przechowywane w jednej z następujących ścieżek:

* *Zasoby/modele widoków. Account. RegisterViewModel. fr. resx*
* *Zasoby/modele widoków/Account/RegisterViewModel. fr. resx*

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

W ASP.NET Core MVC 1.1.0 i wyższych atrybuty inne niż Walidacja są zlokalizowane. ASP.NET Core MVC 1,0 **nie** wyszukuje zlokalizowanych ciągów dla atrybutów niezwiązanych z walidacją.

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a>Używanie jednego ciągu zasobu dla wielu klas

Poniższy kod przedstawia sposób użycia jednego ciągu zasobu do atrybutów walidacji z wieloma klasami:

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

W poprzednim kodzie, `SharedResource` jest klasą odpowiadającą resx, gdzie są przechowywane Twoje wiadomości weryfikacyjne. W tym podejściu DataAnnotations będą używać `SharedResource`tylko, a nie zasobów dla każdej klasy.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>Zapewnianie zlokalizowanych zasobów dla obsługiwanych języków i kultur

### <a name="supportedcultures-and-supporteduicultures"></a>SupportedCultures i SupportedUICultures

ASP.NET Core pozwala określić dwie wartości kulturowe `SupportedCultures` i. `SupportedUICultures` Obiekt [CultureInfo](/dotnet/api/system.globalization.cultureinfo) dla `SupportedCultures` określa wyniki funkcji zależnych od kultury, takich jak data, godzina, liczba i formatowanie waluty. `SupportedCultures`określa również kolejność sortowania tekstu, Konwencji wielkości liter i porównań ciągów. Zobacz [CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) , aby uzyskać więcej informacji na temat sposobu, w jaki serwer pobiera kulturę. Określa, które przetłumaczone ciągi (z plików *resx* ) są wyszukiwane przez program [ResourceManager.](/dotnet/api/system.resources.resourcemanager) `SupportedUICultures` `ResourceManager` Po prostu wyszukuje ciągi specyficzne dla kultury, które są określane `CurrentUICulture`przez. Każdy wątek w programie .NET `CurrentCulture` ma `CurrentUICulture` obiekty i. ASP.NET Core sprawdza te wartości podczas renderowania funkcji zależnych od kultury. Na przykład, jeśli kultura bieżącego wątku jest ustawiona na wartość "en-US" (angielski, Stany Zjednoczone), `DateTime.Now.ToLongDateString()` zostanie wyświetlona wartość "czwartek, 18 lutego, 2016" `CurrentCulture` , ale jeśli jest ustawiona na "es-ES" (hiszpański, Hiszpania) dane wyjściowe będą "jueves, 18 de febrero de 2016".

## <a name="resource-files"></a>Pliki zasobów

Plik zasobów jest użytecznym mechanizmem oddzielania lokalizowalnych ciągów od kodu. Przetłumaczone ciągi dla języka innego niż domyślny są izolowane w plikach zasobów *resx* . Na przykład możesz chcieć utworzyć plik zasobów hiszpański o nazwie *Welcome. es. resx* zawierający przetłumaczone ciągi. "es" to kod języka w języku hiszpańskim. Aby utworzyć ten plik zasobów w programie Visual Studio:

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder, który będzie zawierać plik zasobów, > **Dodaj** > **nowy element**.

    ![Zagnieżdżone menu kontekstowe: w Eksplorator rozwiązań menu kontekstowe jest otwarte dla zasobów. Drugie menu kontekstowe jest otwarte dla dodawania pokazującego wyróżnione polecenie Nowy element.](localization/_static/newi.png)

2. W polu **wyszukiwania zainstalowanych szablonów** wprowadź "Resource" i Nazwij plik.

    ![Okno dialogowe Dodawanie nowego elementu](localization/_static/res.png)

3. Wprowadź wartość klucza (ciąg macierzysty) w kolumnie **Nazwa** i przetłumaczony ciąg w kolumnie **wartość** .

    ![Welcome. es. resx plik (plik zasobów powitalnych dla języka hiszpańskiego) z słowem Hello w kolumnie Nazwa i słowem Hola (Hello w języku hiszpańskim) w kolumnie wartość](localization/_static/hola.png)

    Program Visual Studio wyświetla plik *Welcome. es. resx* .

    ![Eksplorator rozwiązań przedstawiający plik zasobów programu Welcome hiszpański (ES)](localization/_static/se.png)

## <a name="resource-file-naming"></a>Nazewnictwo plików zasobów

Zasoby są nazwane dla pełnej nazwy typu swojej klasy pomniejszonej o nazwę zestawu. Na przykład zasób francuski w projekcie, którego głównym zestawem jest `LocalizationWebsite.Web.dll` dla klasy `LocalizationWebsite.Web.Startup` , będzie nazwany *Startup. fr. resx*. Zasób klasy `LocalizationWebsite.Web.Controllers.HomeController` nosi nazwę *controllers. HomeController. fr. resx*. Jeśli przestrzeń nazw klasy Target nie jest taka sama jak nazwa zestawu, będzie potrzebna pełna nazwa typu. Przykładowo w przykładowym projekcie zasób dla typu `ExtraNamespace.Tools` powinien mieć nazwę *ExtraNamespace. Tools. fr. resx*.

W przykładowym projekcie `ConfigureServices` Metoda ustawia `ResourcesPath` do "zasobów", więc ścieżka względna projektu dla francuskiego pliku zasobów kontrolera głównego to *zasoby/kontrolery. HomeController. fr. resx*. Alternatywnie można użyć folderów do organizowania plików zasobów. W przypadku kontrolera macierzystego ścieżka będzie zawierać *zasoby/kontrolery/HomeController. fr. resx*. Jeśli ta `ResourcesPath` opcja nie zostanie użyta, plik *. resx* zostanie użyty w katalogu bazowym projektu. Plik zasobu dla `HomeController` nosi nazwę *controllers. HomeController. fr. resx*. Wybór przy użyciu konwencji nazewnictwa kropka lub ścieżki zależy od tego, w jaki sposób chcesz zorganizować pliki zasobów.

| Nazwa zasobu | Nazwa kropka lub ścieżki |
| ------------   | ------------- |
| Zasoby/kontrolery. HomeController. fr. resx | Kropka  |
| Zasoby/kontrolery/HomeController. fr. resx  | Ścieżka |
|    |     |

Pliki zasobów `@inject IViewLocalizer` używające Razor w widokach są zgodne z podobnym wzorcem. Plik zasobów dla widoku może być nazwany przy użyciu nazw kropek lub nazw ścieżek. RazorWyświetl pliki zasobów, aby naśladować ścieżkę skojarzonego pliku widoku. Przy założeniu, `ResourcesPath` że ustawimy na "zasoby", plik zasobów francuski skojarzony z widokiem */Home/about. cshtml* może mieć jedną z następujących wartości:

* Zasoby/widoki/Strona główna/informacje. fr. resx

* Zasoby/widoki. Strona główna. informacje. fr. resx

Jeśli `ResourcesPath` opcja nie zostanie użyta, plik *resx* dla widoku powinien znajdować się w tym samym folderze co widok.

### <a name="rootnamespaceattribute"></a>RootNamespaceAttribute 

Atrybut [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) udostępnia główną przestrzeń nazw zestawu, gdy główna przestrzeń nazw zestawu jest inna niż nazwa zestawu. 

> [!WARNING]
> Taka sytuacja może wystąpić, gdy nazwa projektu nie jest prawidłowym identyfikatorem platformy .NET. Na przykład `my-project-name.csproj` użyje głównej przestrzeni nazw `my_project_name` i nazwy `my-project-name` zestawu prowadzącej do tego błędu. 

Jeśli główna przestrzeń nazw zestawu jest inna niż nazwa zestawu:

* Lokalizacja nie działa domyślnie.
* Lokalizowanie nie powiedzie się z powodu sposobu wyszukiwania zasobów w zestawie. `RootNamespace`jest wartością czasu kompilacji, która nie jest dostępna dla wykonywanego procesu. 

Jeśli różni `RootNamespace` się od `AssemblyName`, należy uwzględnić następujące w *AssemblyInfo.cs* (z wartościami parametrów zamienionymi na wartości rzeczywiste):

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

Poprzedni kod umożliwia pomyślne rozpoznanie plików resx.

## <a name="culture-fallback-behavior"></a>Zachowanie rezerwowe kultury

Podczas wyszukiwania zasobu lokalizacja prowadzi do "rezerwy kulturowej". Jeśli nie zostanie znaleziona, rozpoczyna się od żądanej kultury, przywraca kulturę nadrzędną tej kultury. Poza tym Właściwość [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) reprezentuje kulturę nadrzędną. Zwykle (ale nie zawsze) oznacza usunięcie z ISO. Na przykład dialekt języka hiszpańskiego wymawianego w Meksyku to "es-MX". Jest to wersja Hiszpańska "es"&mdash;, która nie jest specyficzna dla żadnego z krajów.

Wyobraź sobie, że witryna otrzymuje żądanie dotyczące zasobu "Welcome" przy użyciu kultury "fr-CA". System lokalizacji wyszukuje następujące zasoby w kolejności i wybiera pierwsze dopasowanie:

* *Welcome.fr — CA. resx*
* *Witamy. fr. resx*
* *Welcome. resx* (Jeśli `NeutralResourcesLanguage` jest "fr-CA")

Na przykład, jeśli usuniesz oznaczenie kultury ". fr" i masz kulturę ustawioną na francuski, domyślny plik zasobów jest odczytywany, a ciągi są zlokalizowane. Menedżer zasobów określa domyślny lub rezerwowy zasób, gdy nic nie spełnia wymaganej kultury. Jeśli chcesz po prostu zwrócić klucz, gdy brakuje zasobu dla wymaganej kultury, nie musisz mieć domyślnego pliku zasobów.

### <a name="generate-resource-files-with-visual-studio"></a>Generowanie plików zasobów przy użyciu programu Visual Studio

Jeśli utworzysz plik zasobów w programie Visual Studio bez kultury w nazwie pliku (na przykład *Welcome. resx*), program Visual Studio utworzy klasę języka C# z właściwością dla każdego ciągu. Zwykle nie jest to możliwe dzięki ASP.NET Core. Zazwyczaj nie istnieje domyślny plik zasobów *resx* (plik *. resx* bez nazwy kultury). Zalecamy utworzenie pliku *resx* z nazwą kultury (na przykład *Welcome. fr. resx*). Podczas tworzenia pliku *resx* przy użyciu nazwy kultury program Visual Studio nie generuje pliku klasy.

### <a name="add-other-cultures"></a>Dodaj inne kultury

Każda kombinacja języka i kultury (oprócz języka domyślnego) wymaga unikatowego pliku zasobów. Tworzysz pliki zasobów dla różnych kultur i ustawień regionalnych, tworząc nowe pliki zasobów, w których kody języka ISO są częścią nazwy pliku (na przykład **en-us**, **fr-CA**i **pl-GB**). Te kody ISO są umieszczane między nazwami plików i rozszerzeniem *resx* , jak w *Welcome.es-MX. resx* (hiszpański/Meksyk).

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>Zaimplementuj strategię, aby wybrać język/kulturę dla każdego żądania

### <a name="configure-localization"></a>Konfigurowanie lokalizacji

Lokalizacja jest skonfigurowana w ramach `Startup.ConfigureServices` metody:

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* `AddLocalization`Dodaje usługi lokalizacyjne do kontenera usług. Powyższy kod również ustawia ścieżkę zasobów na "zasoby".

* `AddViewLocalization`Dodaje obsługę zlokalizowanych plików widoku. Ta lokalizacja widoku przykładowego jest oparta na sufiksie pliku widoku. Na przykład "fr" w pliku *index. fr. cshtml* .

* `AddDataAnnotationsLocalization`Dodaje obsługę zlokalizowanych `DataAnnotations` komunikatów weryfikacyjnych `IStringLocalizer` za pomocą abstrakcji.

### <a name="localization-middleware"></a>Oprogramowanie pośredniczące lokalizacji

Bieżąca kultura w żądaniu jest ustawiana w oprogramowaniu [pośredniczącym](xref:fundamentals/middleware/index)lokalizacji. Oprogramowanie pośredniczące lokalizacji jest włączone w tej `Startup.Configure` metodzie. Oprogramowanie pośredniczące lokalizacyjne musi być skonfigurowane przed jakimkolwiek oprogramowanie pośredniczące, które może sprawdzić kulturę żądania ( `app.UseMvcWithDefaultRoute()`na przykład).

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

`UseRequestLocalization`Inicjuje `RequestLocalizationOptions` obiekt. Na każdym zażądaniu listy `RequestCultureProvider` w programie `RequestLocalizationOptions` jest wyliczany, a pierwszy dostawca, który może pomyślnie ustalić kulturę żądań, jest używany. Dostawcy domyślnie pochodzą z `RequestLocalizationOptions` klasy:

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

Lista domyślna przechodzi od najbardziej konkretnych do najmniej określonych. W dalszej części artykułu zobaczymy, jak można zmienić kolejność, a nawet dodać niestandardowego dostawcę kultury. Jeśli żaden z dostawców nie może określić kultury żądania, `DefaultRequestCulture` jest używana.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

Niektóre aplikacje będą używać ciągu zapytania w celu ustawienia kultur [i kultury interfejsu użytkownika](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx). W przypadku aplikacji korzystających z metody pliku cookie lub z nagłówka Accept-Language Dodawanie ciągu zapytania do adresu URL jest przydatne w przypadku debugowania i testowania kodu. Domyślnie program `QueryStringRequestCultureProvider` jest rejestrowany jako pierwszy dostawca lokalizacji na `RequestCultureProvider` liście. Parametry `culture` ciągu zapytania są przekazywane i `ui-culture`. Poniższy przykład ustawia określoną kulturę (język i region) na hiszpański/Meksyk:

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

Jeśli przekazujesz tylko jeden z dwóch (`culture` lub `ui-culture`), dostawca ciągu zapytania ustawi obie wartości przy użyciu przekazanego elementu. Na przykład ustawienie tylko kulturowe ustawi zarówno, `Culture` jak i: `UICulture`

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a>CookieRequestCultureProvider

Aplikacje produkcyjne często udostępniają mechanizm ustawiania kultury przy użyciu pliku cookie ASP.NET Core kultury. Użyj metody `MakeCookieValue` , aby utworzyć plik cookie.

`CookieRequestCultureProvider` Zwraca domyślną nazwę pliku cookie używaną do śledzenia preferowanych informacji o kulturze `DefaultCookieName` użytkownika. Domyślna nazwa pliku cookie to `.AspNetCore.Culture`.

Format pliku cookie to `c=%LANGCODE%|uic=%LANGCODE%`, gdzie `c` is `Culture` i `uic` is `UICulture`, na przykład:

    c=en-UK|uic=en-US

Jeśli określisz tylko jedną z informacji o kulturze i kulturze interfejsu użytkownika, określona kultura zostanie użyta dla informacji kultury i kultury interfejsu użytkownika.

### <a name="the-accept-language-http-header"></a>Nagłówek Accept-Language HTTP

[Nagłówek Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) jest settable w większości przeglądarek i był pierwotnie przeznaczony do określenia języka użytkownika. To ustawienie wskazuje, co przeglądarka została ustawiona do wysłania lub która dziedziczy z bazowego systemu operacyjnego. Nagłówek Accept-Language HTTP z żądania przeglądarki nie jest infallibleym sposobem wykrywania preferowanego języka użytkownika (zobacz [Ustawianie preferencji językowych w przeglądarce](https://www.w3.org/International/questions/qa-lang-priorities.en.php)). Aplikacja produkcyjna powinna uwzględniać sposób, w jaki użytkownik może dostosować wybór kultury.

### <a name="set-the-accept-language-http-header-in-ie"></a>Ustawianie nagłówka HTTP Accept-Language w programie IE

1. Na ikonie koła zębatego naciśnij pozycję **Opcje internetowe**.

2. Naciśnij pozycję **Języki**.

    ![Opcje internetowe](localization/_static/lang.png)

3. Naciśnij pozycję **Ustaw preferencje językowe**.

4. Naciśnij pozycję **Dodaj język**.

5. Dodaj język.

6. Naciśnij pozycję język, a następnie naciśnij pozycję **Przenieś w górę**.

::: moniker range="> aspnetcore-3.1"
### <a name="the-content-language-http-header"></a>Nagłówek HTTP w języku zawartości

Nagłówek jednostki [zawartości w języku](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) :

 - Służy do opisywania języków przeznaczonych dla odbiorców.
 - Umożliwia użytkownikowi odróżnienie zgodnie z preferowanym językiem użytkownika.

Nagłówki jednostek są używane w żądaniach i odpowiedziach HTTP.

`Content-Language` Nagłówek można dodać przez ustawienie właściwości `ApplyCurrentCultureToResponseHeaders`.

Dodawanie `Content-Language` nagłówka:

 - Zezwala RequestLocalizationMiddleware na ustawienie `Content-Language` nagłówka przy użyciu. `CurrentUICulture`
 - Eliminuje konieczność jawnego ustawiania nagłówka `Content-Language` odpowiedzi.

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```
::: moniker-end

### <a name="use-a-custom-provider"></a>Używanie dostawcy niestandardowego

Załóżmy, że chcesz zezwolić klientom na przechowywanie w swoich bazach danych językowych i kulturowych. Można napisać dostawcę, aby wyszukać te wartości dla użytkownika. Poniższy kod pokazuje, jak dodać niestandardowego dostawcę:

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

Ten przykład **lokalizacji. StarterWeb** projekt w witrynie [GitHub](https://github.com/aspnet/entropy) zawiera interfejs użytkownika służący do ustawiania `Culture`. Plik *views/Shared/_SelectLanguagePartial. cshtml* umożliwia wybranie kultury z listy obsługiwanych kultur:

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

Plik *views/Shared/_SelectLanguagePartial. cshtml* zostanie dodany do `footer` sekcji pliku układu, więc będzie dostępny dla wszystkich widoków:

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

`SetLanguage` Metoda ustawia plik cookie kultury.

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

Nie można podłączyć *_SelectLanguagePartial. cshtml* do przykładowego kodu dla tego projektu. Projekt **Lokalizacja. StarterWeb** w usłudze [GitHub](https://github.com/aspnet/entropy) ma kod, `RequestLocalizationOptions` który umożliwia przechodzenie Razor do częściowej przez kontener [iniekcji zależności](dependency-injection.md) .

## <a name="model-binding-route-data-and-query-strings"></a>Dane tras powiązań modelu i ciągi zapytania

Zobacz [zachowanie globalizacji powiązań modelu powiązanie danych i ciągi zapytań](xref:mvc/models/model-binding#glob).

## <a name="globalization-and-localization-terms"></a>Warunki globalizacji i lokalizacja

Proces lokalizowania aplikacji wymaga również podstawowej znajomości odpowiednich zestawów znaków, które są często używane w nowoczesnych opracowywaniu oprogramowania i zrozumieniu skojarzonych z nimi problemów. Mimo że wszystkie komputery przechowują tekst jako cyfry (kody), różne systemy przechowują ten sam tekst przy użyciu różnych liczb. Proces lokalizowania dotyczy tłumaczenia interfejsu użytkownika aplikacji (UI) dla określonych kultur/ustawień regionalnych.

Możliwość [lokalizowania](/dotnet/standard/globalization-localization/localizability-review) to proces pośredni służący do sprawdzania, czy aplikacja globalna jest gotowa do lokalizacji.

Format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) dla nazwy kultury to `<languagecode2>-<country/regioncode2>`, gdzie `<languagecode2>` jest kodem języka i `<country/regioncode2>` jest kodem podkultury. Na przykład `es-CL` w przypadku języka hiszpańskiego (Chile `en-US` ) dla języka angielskiego (Stany Zjednoczone) `en-AU` i dla języka angielskiego (Australia). [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) jest kombinacją kodu ISO 639 2 litery małymi literami związanymi z językiem i ISO 3166 2 literą w postaci wielkiej litery, skojarzonej z krajem lub regionem. Zobacz [nazwa kultury języka](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).

Międzynarodowe jest często skracane do "I18N". Skrót przyjmuje pierwszą i ostatnią literę oraz liczbę liter między nimi, więc 18 oznacza liczbę liter między pierwszym "I" i ostatnim "N". Dotyczy to zarówno globalizacji (G11N), jak i lokalizacji (L10N).

Odsetk

* Globalizacja (G11N): proces tworzenia aplikacji w różnych językach i regionach.
* Lokalizacja (L10N): proces dostosowywania aplikacji dla danego języka i regionu.
* Międzynarodowe (I18N): opisuje zarówno globalizację, jak i lokalizację.
* Kultura: jest to język i, opcjonalnie, region.
* Kultura neutralna: kultura, która ma określony język, ale nie region. (na przykład "en", "es")
* Określona kultura: kultura, która ma określony język i region. (na przykład "en-US", "pl-GB", "es-CL")
* Kultura nadrzędna: neutralna kultura, która zawiera określoną kulturę. (na przykład "en" jest kulturą nadrzędną wartości "pl-US" i "pl-GB")
* Ustawienia regionalne: ustawienie regionalne jest takie samo jak kultura.

[!INCLUDE[](~/includes/localization/currency.md)]

::: moniker range=">= aspnetcore-3.0"
[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]
::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* [Projekt lokalizacji. StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) używany w artykule.
* [Globalizacja i lokalizowanie aplikacji platformy .NET](/dotnet/standard/globalization-localization/index)
* [Zasoby w plikach resx](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Zestaw Microsoft Multilingual App Toolkit](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [Lokalizacje & typy ogólne](http://hishambinateya.com/localization-and-generics)
