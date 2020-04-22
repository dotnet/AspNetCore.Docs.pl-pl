---
title: Tworzenie i używanie ASP.NET podstawowych komponentów maszynki do golenia
author: guardrex
description: Dowiedz się, jak tworzyć składniki razor i używać ich, w tym jak wiązać się z danymi, obsługiwać zdarzenia i zarządzać cyklami życia komponentu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/21/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: 4434636992cb2506ef6525996690946f97c43764
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791487"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>Tworzenie i używanie ASP.NET podstawowych komponentów maszynki do golenia

[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)i [Tobias Bartsch](https://www.aveo-solutions.com/)

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

Blazoraplikacje są tworzone przy użyciu *składników*. Składnik jest samodzielnym fragmentem interfejsu użytkownika (UI), takim jak strona, okno dialogowe lub formularz. Składnik zawiera znaczników HTML i logiki przetwarzania wymagane do wstrzykiwania danych lub reagowania na zdarzenia interfejsu użytkownika. Komponenty są elastyczne i lekkie. Mogą być zagnieżdżone, ponownie i udostępniane między projektami.

## <a name="component-classes"></a>Klasy komponentów

Składniki są implementowane w plikach komponentów [Razor](xref:mvc/views/razor) (*.brzytwa*) przy użyciu kombinacji znaczników C# i HTML. Składnik w Blazor jest formalnie określany jako *składnik Razor*.

Nazwa komponentu musi zaczynać się od znaku wielkim. Na przykład *MyCoolComponent.brzytwa* jest prawidłowa, a *myCoolComponent.brzytwa* jest nieprawidłowa.

Interfejs użytkownika składnika jest zdefiniowany przy użyciu kodu HTML. Dynamiczna logika renderowania (na przykład pętle, warunki, wyrażenia) jest dodawana przy użyciu osadzonej składni języka C# o nazwie [Razor](xref:mvc/views/razor). Podczas kompilowania aplikacji, znaczników HTML i logiki renderowania Języka C# są konwertowane na klasę składnika. Nazwa wygenerowanej klasy jest zgodna z nazwą pliku.

Elementy członkowskie klasy komponentu `@code` są definiowane w bloku. W `@code` bloku stan składnika (właściwości, pola) jest określony za pomocą metod obsługi zdarzeń lub definiowania innej logiki składnika. Dopuszczalne `@code` jest więcej niż jeden blok.

Elementy członkowskie składnika mogą być używane jako część logiki renderowania `@`składnika przy użyciu wyrażeń Języka C#, które zaczynają się od . Na przykład pole C# jest renderowane `@` przez prefiks do nazwy pola. Poniższy przykład ocenia i renderuje:

* `_headingFontStyle`do wartości właściwości CSS dla `font-style`.
* `_headingText`do treści `<h1>` elementu.

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

Po składnik jest początkowo renderowane, składnik ponownie generuje jego drzewa renderowania w odpowiedzi na zdarzenia. Blazornastępnie porównuje nowe drzewo renderowania z poprzednim i stosuje wszelkie modyfikacje modelu obiektu dokumentu przeglądarki (DOM).

Składniki są zwykłe klasy C# i mogą być umieszczane w dowolnym miejscu w ramach projektu. Składniki, które tworzą strony sieci Web, zwykle znajdują się w folderze *Strony.* Składniki niebędące stronicowymi są często umieszczane w folderze *udostępnionym* lub folderze niestandardowym dodawanym do projektu.

Zazwyczaj obszar nazw składnika pochodzi z głównego obszaru nazw aplikacji i lokalizacji (folderu) składnika w aplikacji. Jeśli główny obszar nazw aplikacji `BlazorApp` jest `Counter` i składnik znajduje się w folderze *Strony:*

* Obszar `Counter` nazw składnika `BlazorApp.Pages`jest .
* W pełni kwalifikowana nazwa `BlazorApp.Pages.Counter`typu komponentu to .

Aby uzyskać więcej informacji, zobacz sekcję [Importowanie składników.](#import-components)

Aby użyć folderu niestandardowego, dodaj obszar nazw folderu niestandardowego do składnika nadrzędnego lub do pliku *_Imports.brzytwa* aplikacji. Na przykład następujący obszar nazw udostępnia składniki w folderze *Składniki,* gdy `BlazorApp`głównym obszarem nazw aplikacji jest:

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a>Aktywa statyczne

Blazornastępuje konwencja ASP.NET Aplikacje Core umieszczanie zasobów statycznych w [folderze głównego internetowego projektu (wwwroot).](xref:fundamentals/index#web-root)

Użyj ścieżki względnej`/`podstawowej ( ), aby odwołać się do katalogu głównego sieci Web dla zasobu statycznego. W poniższym przykładzie *logo.png* jest fizycznie zlokalizowane w folderze *{PROJECT ROOT}/wwwroot/images:*

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Komponenty brzytwy **nie** obsługują notacji tyldy ().`~/`

Aby uzyskać informacje na temat ustawiania <xref:host-and-deploy/blazor/index#app-base-path>ścieżki podstawowej aplikacji, zobacz .

## <a name="tag-helpers-arent-supported-in-components"></a>Pomocników tagów nie są obsługiwane w składnikach

[Pomocników tagów](xref:mvc/views/tag-helpers/intro) nie są obsługiwane w składnikach Razor (pliki *.brzytwa).* Aby zapewnić funkcję podobną do Blazorpomocnika znaczników w programie , utwórz składnik o takiej samej funkcjonalności jak Pomocnik znaczników i zamiast tego użyj składnika.

## <a name="use-components"></a>Użyj komponentów

Składniki mogą zawierać inne składniki, deklarując je przy użyciu składni elementu HTML. Znaczników przy użyciu składnika wygląda tag HTML, gdzie nazwa znacznika jest typem składnika.

Następujące znaczniki w *index.brzytwa* `HeadingComponent` renderuje wystąpienie:

```razor
<HeadingComponent />
```

*Składniki/HeadingComponent.brzytwa:*

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

Jeśli składnik zawiera element HTML z wielką literą pierwszej litery, która nie pasuje do nazwy składnika, jest emitowane ostrzeżenie wskazujące, że element ma nieoczekiwaną nazwę. Dodanie `@using` dyrektywy dla obszaru nazw składnika udostępnia składnik, który rozwiązuje ostrzeżenie.

## <a name="routing"></a>Routing

Routing Blazor w uzyskuje się przez zapewnienie szablonu trasy do każdego składnika dostępne w aplikacji.

Po skompilowaniu `@page` pliku Razor z dyrektywą, <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> wygenerowana klasa otrzymuje określający szablon trasy. W czasie wykonywania router wyszukuje `RouteAttribute` klasy składników z i renderuje, który składnik ma szablon trasy, który pasuje do żądanego adresu URL.

```razor
@page "/ParentComponent"

...
```

Aby uzyskać więcej informacji, zobacz <xref:blazor/routing>.

## <a name="parameters"></a>Parametry

### <a name="route-parameters"></a>Parametry trasy

Składniki mogą odbierać parametry trasy z `@page` szablonu trasy przedstawionego w dyrektywie. Router używa parametrów trasy do wypełniania odpowiednich parametrów komponentu.

*Strony/RouteParameter.brzytwa*:

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

Parametry opcjonalne nie są obsługiwane, więc dwie `@page` dyrektywy są stosowane w poprzednim przykładzie. Pierwszy umożliwia nawigację do składnika bez parametru. Druga `@page` dyrektywa odbiera `{text}` parametr trasy i przypisuje `Text` wartość do właściwości.

Składnia parametrów *Catch-all* (`*`/`**`), która przechwytuje ścieżkę przez wiele granic folderów, **nie** jest obsługiwana w składnikach Razor (*.brzytwa*).

### <a name="component-parameters"></a>Parametry komponentu

Komponenty mogą mieć *parametry komponentu,* które są definiowane `[Parameter]` przy użyciu właściwości publicznych w klasie składnika z atrybutem. Atrybuty służy do określania argumentów dla składnika w znacznikach.

*Składniki/ChildComponent.brzytwa*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

W poniższym przykładzie z `ParentComponent` przykładowej aplikacji `Title` ustawia wartość `ChildComponent`właściwości . .

*Strony/ParentComponent.brzytwa:*

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> Nie należy tworzyć komponentów, które zapisują do własnych *parametrów składnika,* zamiast tego należy użyć pola prywatnego. Aby uzyskać więcej informacji, zobacz [Nie należy tworzyć składników, które zapisują do własnych właściwości parametrów](#dont-create-components-that-write-to-their-own-parameter-properties) sekcji.

## <a name="child-content"></a>Zawartość podrzędna

Składniki można ustawić zawartość innego składnika. Składnik przypisujący udostępnia zawartość między znacznikami określającymi składnik odbiorczy.

W poniższym przykładzie `ChildComponent` `ChildContent` ma właściwość, która reprezentuje `RenderFragment`, który reprezentuje segment interfejsu użytkownika do renderowania. Wartość `ChildContent` jest umieszczana w znacznikach składnika, gdzie zawartość powinna być renderowana. Wartość `ChildContent` jest odbierana z komponentu nadrzędnego i renderowana `panel-body`wewnątrz panelu Bootstrap .

*Składniki/ChildComponent.brzytwa*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> Właściwość odbierająca `RenderFragment` zawartość musi `ChildContent` być nazwany przez konwencję.

W `ParentComponent` przykładowej aplikacji można zapewnić zawartość `ChildComponent` do renderowania `<ChildComponent>` przez umieszczenie zawartości wewnątrz tagów.

*Strony/ParentComponent.brzytwa:*

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Podział atrybutów i dowolne parametry

Składniki mogą przechwytywać i renderować dodatkowe atrybuty oprócz zadeklarowanych parametrów składnika. Dodatkowe atrybuty mogą być przechwytywane w słowniku, a następnie *splatted* na element, gdy składnik jest renderowany przy użyciu [`@attributes`](xref:mvc/views/razor#attributes) dyrektywy Razor. Ten scenariusz jest przydatny podczas definiowania składnika, który tworzy element znaczników, który obsługuje różne dostosowania. Na przykład może być żmudne definiowanie `<input>` atrybutów oddzielnie dla, który obsługuje wiele parametrów.

W poniższym przykładzie `<input>` pierwszy`id="useIndividualParams"`element ( ) używa poszczególnych parametrów komponentu, podczas gdy drugi `<input>` element (`id="useAttributesDict"`) używa splatting atrybutu:

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

Typ parametru musi `IEnumerable<KeyValuePair<string, object>>` zostać zaimplementowany za pomocą kluczy ciągów. Korzystanie `IReadOnlyDictionary<string, object>` jest również opcją w tym scenariuszu.

Renderowane `<input>` elementy przy użyciu obu podejść są identyczne:

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

Aby zaakceptować dowolne atrybuty, `[Parameter]` należy zdefiniować parametr `true`komponentu przy użyciu atrybutu z właściwością ustawioną na: `CaptureUnmatchedValues`

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

Właściwość `CaptureUnmatchedValues` `[Parameter]` na umożliwia parametr dopasować wszystkie atrybuty, które nie pasują do innego parametru. Komponent może definiować tylko `CaptureUnmatchedValues`jeden parametr za pomocą pliku . Typ właściwości używany `CaptureUnmatchedValues` z musi `Dictionary<string, object>` być przypisany z kluczami ciągu. `IEnumerable<KeyValuePair<string, object>>`lub `IReadOnlyDictionary<string, object>` są również opcje w tym scenariuszu.

Pozycja `@attributes` względem pozycji atrybutów elementu jest ważna. Gdy `@attributes` są splatted na element, atrybuty są przetwarzane od prawej do lewej (ostatnio do pierwszego). Rozważmy następujący przykład składnika, `Child` który zużywa składnik:

*ParentComponent.brzytwa*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent.brzytwa*:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Atrybut `Child` składnika `extra` jest ustawiony na prawo `@attributes`od . Renderowany `Parent` `<div>` składnik zawiera `extra="5"` po przejściu przez dodatkowy atrybut, ponieważ atrybuty są przetwarzane od prawej do lewej (od początku do pierwszego):

```html
<div extra="5" />
```

W poniższym przykładzie `extra` kolejność `@attributes` i jest `Child` odwrócona `<div>`w komponencie:

*ParentComponent.brzytwa*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent.brzytwa*:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Renderowane `<div>` w `Parent` składniku `extra="10"` zawiera, gdy przekazywane przez dodatkowy atrybut:

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>Przechwytywanie odwołań do komponentów

Odwołania do składników umożliwiają odwoływanie się do wystąpienia składnika, dzięki czemu `Show` `Reset`można wydawać polecenia do tego wystąpienia, takie jak lub . Aby przechwycić odwołanie do komponentu:

* Dodaj [`@ref`](xref:mvc/views/razor#ref) atrybut do składnika podrzędnego.
* Zdefiniuj pole o tym samym typie co komponent podrzędny.

```razor
<MyLoginDialog @ref="_loginDialog" ... />

@code {
    private MyLoginDialog _loginDialog;

    private void OnSomething()
    {
        _loginDialog.Show();
    }
}
```

Podczas renderowania komponentu `_loginDialog` pole jest wypełniane wystąpieniem komponentu podrzędnego. `MyLoginDialog` Następnie można wywołać metody .NET w wystąpieniu składnika.

> [!IMPORTANT]
> Zmienna `_loginDialog` jest wypełniana tylko po renderowaniu składnika, a jej dane wyjściowe zawierają `MyLoginDialog` element. Do tego momentu nie ma się czego odwołać. Aby manipulować odwołaniami do komponentów po zakończeniu renderowania przez komponent, należy użyć [metod OnAfterRenderAsync lub OnAfterRender](xref:blazor/lifecycle#after-component-render).

Aby odwoływać się do komponentów w pętli, zobacz [Przechwytywanie odwołań do wielu podobnych składników podrzędnych (#13358 dotnet/aspnetcore).](https://github.com/dotnet/aspnetcore/issues/13358)

Podczas przechwytywania odwołań do składników użyć podobnej składni do [przechwytywania odwołań do elementów,](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)nie jest to funkcja interop JavaScript. Odwołania do składników nie są przekazywane&mdash;do kodu JavaScript, który jest używany tylko w kodzie .NET.

> [!NOTE]
> **Nie** należy używać odwołań do komponentów do mutować stan komponentów podrzędnych. Zamiast tego należy użyć normalnych parametrów deklaratywnych do przekazywania danych do składników podrzędnych. Użycie normalnych parametrów deklaratywnych powoduje, że składniki podrzędne, które automatycznie rimanują w prawidłowych godzinach.

## <a name="invoke-component-methods-externally-to-update-state"></a>Wywoływanie metod składników zewnętrznie w celu aktualizacji stanu

Blazorużywa `SynchronizationContext` do wymuszania pojedynczego wątku logicznego wykonania. [Metody cyklu życia](xref:blazor/lifecycle) składnika i wszelkie wywołania zwrotne zdarzeń, które są wywoływane przez Blazor są wykonywane na ten `SynchronizationContext`temat . W przypadku, gdy składnik musi zostać zaktualizowany na podstawie zdarzenia zewnętrznego, takiego `InvokeAsync` jak czasomierz Blazorlub `SynchronizationContext`inne powiadomienia, użyj metody, która zostanie wysłana do 's .

Rozważmy na przykład *usługę powiadamiania,* która może powiadamiać dowolny składnik nasłuchiwania o zaktualizowanym stanie:

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

Zarejestruj `NotifierService` jako singletion:

* W Blazor WebAssembly zarejestruj usługę `Program.Main`w:

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* W Blazor systemie Server zarejestruj `Startup.ConfigureServices`usługę w:

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

Użyj, `NotifierService` aby zaktualizować składnik:

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @_lastNotification.key = @_lastNotification.value</p>

@code {
    private (string key, int value) _lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            _lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

W poprzednim przykładzie `NotifierService` wywołuje metodę składnika `OnNotify` poza Blazor's `SynchronizationContext`. `InvokeAsync`służy do przełączania się do prawidłowego kontekstu i kolejkowania renderowania.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Użyj \@klawisza do kontrolowania zachowania elementów i komponentów

Podczas renderowania listy elementów lub składników i elementy Blazorlub składniki następnie zmienić, 's diffing algorytm musi zdecydować, które z poprzednich elementów lub składników mogą być zachowywane i jak obiekty modelu powinny mapować do nich. Zwykle ten proces jest automatyczny i może być ignorowany, ale są przypadki, w których można kontrolować proces.

Rozważmy następujący przykład:

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

Zawartość `People` kolekcji może ulec zmianie za pomocą wpisów wstawionych, usuniętych lub ponownie uporządkowanych. Gdy składnik rerenders, `<DetailsEditor>` składnik może ulec `Details` zmianie, aby otrzymać różne wartości parametrów. Może to spowodować bardziej złożone rerendering niż oczekiwano. W niektórych przypadkach rerendering może prowadzić do widocznych różnic w zachowaniu, takich jak utrata fokusu elementu.

Proces mapowania można kontrolować [`@key`](xref:mvc/views/razor#key) za pomocą atrybutu dyrektywy. `@key`powoduje, że algorytm diffing gwarantuje zachowanie elementów lub komponentów w oparciu o wartość klucza:

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

Po `People` zmianie kolekcji algorytm diffing zachowuje `<DetailsEditor>` skojarzenie `person` między wystąpieniami i wystąpieniami:

* Jeśli `Person` a zostanie `People` usunięty z listy, tylko odpowiednie `<DetailsEditor>` wystąpienie jest usuwany z interfejsu użytkownika. Inne wystąpienia pozostają niezmienione.
* Jeśli `Person` a jest wstawiany w jakiejś pozycji na liście, jedno nowe `<DetailsEditor>` wystąpienie jest wstawiany w tym odpowiednim położeniu. Inne wystąpienia pozostają niezmienione.
* Jeśli `Person` wpisy są ponownie uporządkowane, odpowiednie `<DetailsEditor>` wystąpienia są zachowywane i ponownie uporządkowane w interfejsie użytkownika.

W niektórych scenariuszach `@key` użycie minimalizuje złożoność rerendering i pozwala uniknąć potencjalnych problemów ze stanowymi częściami dom zmiany, takich jak położenie fokusu.

> [!IMPORTANT]
> Klucze są lokalne dla każdego elementu kontenera lub składnika. Klucze nie są porównywane globalnie w całym dokumencie.

### <a name="when-to-use-key"></a>Kiedy używać \@klucza

Zazwyczaj warto używać za `@key` każdym razem, gdy lista jest renderowana `@foreach` (na przykład w bloku) `@key`i istnieje odpowiednia wartość do zdefiniowania programu .

Można również `@key` użyć, Blazor aby zapobiec zachowaniu elementu lub poddrzewa komponentu, gdy zmienia się obiekt:

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

Jeśli `@currentPerson` zmiany, `@key` dyrektywa atrybut Blazor wymusza `<div>` odrzucić cały i jego elementy podrzędne i odbudować poddrzewo w interfejsie użytkownika z nowych elementów i składników. Może to być przydatne, jeśli trzeba zagwarantować, że `@currentPerson` żaden stan interfejsu użytkownika jest zachowywany po zmianie.

### <a name="when-not-to-use-key"></a>Kiedy nie \@używać klucza

Jest koszt wydajności podczas diffing `@key`z . Koszt wydajności nie jest duży, `@key` ale należy określić tylko wtedy, gdy kontrolowanie zasad zachowania elementu lub składnika jest korzystne dla aplikacji.

Nawet `@key` jeśli nie jest Blazor używany, zachowuje element podrzędny i wystąpienia składników jak najwięcej. Jedyną zaletą `@key` przy użyciu jest kontrola nad *tym, jak* wystąpienia modelu są mapowane do zachowanych wystąpień składników, zamiast algorytmu diffing wybierając mapowanie.

### <a name="what-values-to-use-for-key"></a>Jakie wartości mają \@być używane dla klucza

Ogólnie rzecz biorąc, ma sens dostarczanie jednego z `@key`następujących rodzajów wartości dla:

* Wystąpienia obiektu modelu (na `Person` przykład wystąpienie, jak we wcześniejszym przykładzie). Zapewnia to zachowanie na podstawie równości odwołania do obiektu.
* Unikatowe identyfikatory (na przykład wartości `int` `string`klucza `Guid`podstawowego typu , lub ).

Upewnij się, `@key` że wartości używane dla nie kolidują ze sobą. Jeśli wartości kolizji zostaną wykryte w Blazor tym samym elemencie nadrzędnym, zgłasza wyjątek, ponieważ nie można deterministycznie mapować stare elementy lub składniki do nowych elementów lub składników. Używaj tylko różnych wartości, takich jak wystąpienia obiektów lub wartości klucza podstawowego.

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a>Nie należy tworzyć składników, które zapisują do własnych właściwości parametrów

Parametry są zastępowane pod następującymi warunkami:

* Zawartość składnika podrzędnego jest renderowana za pomocą pliku `RenderFragment`.
* <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>jest wywoływana w komponencie nadrzędnym.

Parametry są resetowane, ponieważ składnik <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> nadrzędny rerenders, gdy jest wywoływana i nowe wartości parametrów są dostarczane do składnika podrzędnego.

Należy wziąć `Expander` pod uwagę następujący składnik, który:

* Renderuje zawartość podrzędną.
* Przełączniki przedstawiające zawartość podrzędną z parametrem komponentu.

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @Expanded)

    @if (Expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

Komponent `Expander` jest dodawany do składnika `StateHasChanged`nadrzędnego, który może wywołać:

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

Początkowo `Expander` składniki zachowują się niezależnie, gdy ich `Expanded` właściwości są przełączane. Składniki podrzędne zachowują swoje stany zgodnie z oczekiwaniami. Po `StateHasChanged` wywołaniu w chyłce `Expanded` parametr pierwszego komponentu podrzędnego`true`jest resetowany z powrotem do jego wartości początkowej ( ). Wartość `Expander` drugiego składnika `Expanded` nie jest resetowana, ponieważ w drugim składniku nie jest renderowana żadna zawartość podrzędna.

Aby zachować stan w poprzednim scenariuszu, `Expander` należy użyć pola *prywatnego* w składniku, aby zachować jego stan przełączania.

Następujący `Expander` składnik:

* Akceptuje wartość `Expanded` parametru składnika z elementu nadrzędnego.
* Przypisuje wartość parametru komponentu do`_expanded`pola *prywatnego* ( ) w [zdarzeniu OnInitialized](xref:blazor/lifecycle#component-initialization-methods).
* Używa pola prywatnego do zachowania jego wewnętrznego stanu przełączania.

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @_expanded)

    @if (_expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool _expanded;

    protected override void OnInitialized()
    {
        _expanded = Expanded;
    }

    private void Toggle()
    {
        _expanded = !_expanded;
    }
}
```

## <a name="partial-class-support"></a>Obsługa częściowej klasy

Komponenty maszynki do golenia są generowane jako klasy częściowe. Komponenty maszynki do golenia są tworzyć przy użyciu jednego z następujących podejść:

* Kod C# jest [`@code`](xref:mvc/views/razor#code) zdefiniowany w bloku z znaczników HTML i kod Razor w jednym pliku. Blazorszablony definiują swoje składniki Razor przy użyciu tego podejścia.
* Kod C# jest umieszczany w pliku związanym z kodem zdefiniowanym jako klasa częściowa.

W poniższym przykładzie pokazano domyślny `Counter` składnik z blokiem `@code` w aplikacji wygenerowanej na podstawie szablonu. Blazor Znaczniki HTML, kod Razor i kod C# znajdują się w tym samym pliku:

*Counter.brzytwa*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int _currentCount = 0;

    void IncrementCount()
    {
        _currentCount++;
    }
}
```

Składnik `Counter` można również utworzyć przy użyciu pliku posuwu kodu z klasą częściową:

*Counter.brzytwa*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

*Counter.razor.cs:*

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int _currentCount = 0;

        void IncrementCount()
        {
            _currentCount++;
        }
    }
}
```

W razie potrzeby dodaj wszystkie wymagane obszary nazw do pliku klasy częściowej. Typowe przestrzenie nazw używane przez składniki Razor obejmują:

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a>Określanie klasy podstawowej

Dyrektywa [`@inherits`](xref:mvc/views/razor#inherits) może służyć do określania klasy podstawowej dla składnika. Poniższy przykład pokazuje, jak składnik może `BlazorRocksBase`dziedziczyć klasę podstawową, aby zapewnić właściwości i metody składnika. Klasa podstawowa powinna `ComponentBase`pochodzić od .

*Strony/BlazorRocks.brzytwa*:

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

*BlazorRocksBase.cs:*

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

## <a name="specify-an-attribute"></a>Określanie atrybutu

Atrybuty można określić w [`@attribute`](xref:mvc/views/razor#attribute) komponentach Razor z dyrektywą. Poniższy przykład stosuje `[Authorize]` atrybut do klasy składnika:

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a>Importowanie składników

Obszar nazw składnika chowanego za pomocą razora jest oparty na (w kolejności priorytetu):

* [`@namespace`](xref:mvc/views/razor#namespace)oznaczenie w pliku Razor (*.brzytwa*) znaczników (`@namespace BlazorSample.MyNamespace`).
* Projekt jest `RootNamespace` w pliku projektu`<RootNamespace>BlazorSample</RootNamespace>`( ).
* Nazwa projektu, zaczerpnięta z nazwy pliku projektu (*csproj*) i ścieżka z katalogu głównego projektu do składnika. Na przykład struktura rozwiązuje *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj* `BlazorSample.Pages`) do obszaru nazw . Składniki są zgodne z regułami wiązania nazw języka C#. Dla `Index` składnika w tym przykładzie składniki w zakresie są wszystkie składniki:
  * W tym samym folderze *strony*.
  * Składniki w katalogu głównym projektu, które jawnie nie określają innego obszaru nazw.

Składniki zdefiniowane w innej przestrzeni nazw są wprowadzane [`@using`](xref:mvc/views/razor#using) do zakresu przy użyciu dyrektywy Razor.

Jeśli inny `NavMenu.razor`składnik, istnieje w *folderze BlazorSample/Shared/,* składnik `Index.razor` może `@using` być użyty z następującą instrukcją:

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

Składniki można również odwoływać się przy użyciu ich w [`@using`](xref:mvc/views/razor#using) pełni kwalifikowanych nazw, które nie wymagają dyrektywy:

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> Kwalifikacje `global::` nie są obsługiwane.
>
> Importowanie składników z `using` aliasami instrukcji `@using Foo = Bar`(na przykład) nie jest obsługiwane.
>
> Częściowo kwalifikowane nazwy nie są obsługiwane. Na przykład `@using BlazorSample` dodawanie i `NavMenu.razor` `<Shared.NavMenu></Shared.NavMenu>` odwoływanie się do nie jest obsługiwane.

## <a name="conditional-html-element-attributes"></a>Warunkowe atrybuty elementów HTML

Atrybuty elementu HTML są warunkowo renderowane na podstawie wartości .NET. Jeśli wartość `false` jest `null`lub atrybut nie jest renderowany. Jeśli wartość `true`jest , atrybut jest renderowany zminimalizowane.

W poniższym `IsCompleted` przykładzie `checked` określa, czy jest renderowany w znacznikach elementu:

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

Jeśli `IsCompleted` `true`tak, to pole wyboru jest renderowane jako:

```html
<input type="checkbox" checked />
```

Jeśli `IsCompleted` `false`tak, to pole wyboru jest renderowane jako:

```html
<input type="checkbox" />
```

Aby uzyskać więcej informacji, zobacz <xref:mvc/views/razor>.

> [!WARNING]
> Niektóre atrybuty HTML, takie jak [wciśnięte aria,](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)nie działają `bool`poprawnie, gdy typem .NET jest . W takich przypadkach `string` należy użyć typu `bool`zamiast pliku .

## <a name="raw-html"></a>Surowy kod HTML

Ciągi są zwykle renderowane przy użyciu węzłów tekstu DOM, co oznacza, że wszelkie znaczniki, które mogą zawierać, są ignorowane i traktowane jako tekst dosłowny. Aby renderować nieprzetworzony `MarkupString` kod HTML, zawiń zawartość HTML w wartość. Wartość jest analizowana jako HTML lub SVG i wstawiana do modelu DOM.

> [!WARNING]
> Renderowanie surowego KODU HTML zbudowanego z dowolnego niezaufanego źródła stanowi **zagrożenie bezpieczeństwa** i należy ich unikać!

Poniższy przykład pokazuje, że przy użyciu `MarkupString` typu, aby dodać blok statycznej zawartości HTML do renderowanych danych wyjściowych składnika:

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a>Kaskadowe wartości i parametry

W niektórych scenariuszach jest niewygodne przepływ danych ze składnika nadrzędnego do składnika malejącego przy użyciu [parametrów komponentu](#component-parameters), zwłaszcza gdy istnieje kilka warstw składowych. Kaskadowe wartości i parametry rozwiązują ten problem, zapewniając wygodny sposób dla komponentu nadrzędnego, aby zapewnić wartość dla wszystkich jego składników potomnych. Kaskadowe wartości i parametry zapewniają również podejście do komponentów do koordynowania.

### <a name="theme-example"></a>Przykład motywu

W poniższym przykładzie z `ThemeInfo` przykładowej aplikacji klasa określa informacje o motywie, które mają przepływać w dół hierarchii składników, tak aby wszystkie przyciski w danej części aplikacji współużytkować ten sam styl.

*UIThemeClasses/ThemeInfo.cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Składnik nadrzędny może zapewnić wartość kaskadową przy użyciu składnika Wartość kaskadowa. Składnik `CascadingValue` zawija poddrzewo hierarchii komponentów i dostarcza pojedynczą wartość do wszystkich komponentów w tym poddrzewie.

Na przykład przykładowa aplikacja określa`ThemeInfo`informacje o motywie ( ) w jednym z układów aplikacji jako parametr `@Body` kaskadowy dla wszystkich składników, które tworzą treść układu właściwości. `ButtonClass`jest przypisana wartość `btn-success` w komponencie układu. Każdy składnik potomny może `ThemeInfo` korzystać z tej właściwości za pośrednictwem obiektu kaskadowego.

`CascadingValuesParametersLayout`Składnik:

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="_theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo _theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

Aby użyć wartości kaskadowych, składniki deklarują parametry `[CascadingParameter]` kaskadowe przy użyciu atrybutu. Wartości kaskadowe są powiązane z parametrami kaskadowymi według typu.

W przykładowej aplikacji `CascadingValuesParametersTheme` składnik wiąże `ThemeInfo` wartość kaskadową z parametrem kaskadowym. Parametr służy do ustawiania klasy CSS dla jednego z przycisków wyświetlanych przez składnik.

`CascadingValuesParametersTheme`Składnik:

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @_currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int _currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        _currentCount++;
    }
}
```

Aby kaskadowo przedstawić wiele wartości tego samego typu w `Name` obrębie `CascadingValue` tego samego `CascadingParameter`poddrzewa, podaj unikatowy ciąg do każdego komponentu i odpowiadającego mu pliku . W poniższym przykładzie dwa `CascadingValue` składniki kaskadowo różnych wystąpień `MyCascadingType` według nazwy:

```razor
<CascadingValue Value=@_parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType _parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

W komponencie podrzędnym parametry kaskadowe otrzymują swoje wartości z odpowiednich wartości kaskadowych w komponencie nadrzędnym według nazwy:

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>Przykład zestawu kart

Parametry kaskadowe umożliwiają również współpracę komponentów w hierarchii składników. Rozważmy na przykład poniższy *przykład Zestawu tabulatorów* w przykładowej aplikacji.

Przykładowa aplikacja `ITab` ma interfejs, który implementuje karty:

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

Komponent `CascadingValuesParametersTabSet` używa składnika, `TabSet` który `Tab` zawiera kilka składników:

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

Składniki `Tab` podrzędne nie są jawnie przekazywane `TabSet`jako parametry do . Zamiast tego składniki `Tab` podrzędne są częścią zawartości `TabSet`podrzędnej programu . Jednak `TabSet` nadal musi wiedzieć o `Tab` każdym składniku, tak aby można było renderować nagłówki i aktywną kartę. Aby włączyć tę koordynację bez `TabSet` konieczności dodatkowego kodu, składnik *może zapewnić się jako wartość kaskadowa,* która jest następnie pobierana przez składniki potomne. `Tab`

`TabSet`Składnik:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

Składniki `Tab` potomne przechwytują `TabSet` zawierające jako parametr kaskadowy, dzięki czemu `Tab` komponenty dodają `TabSet` się do i współrzędnych, na których karta jest aktywna.

`Tab`Składnik:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Szablony maszynki do golenia

Render fragmenty mogą być definiowane przy użyciu składni szablonu Razor. Szablony maszynki do golenia są sposobem definiowania fragmentu kodu interfejsu użytkownika i zakładania następującego formatu:

```razor
@<{HTML tag}>...</{HTML tag}>
```

Poniższy przykład ilustruje `RenderFragment` `RenderFragment<T>` sposób określania i wartości i renderowania szablonów bezpośrednio w składniku. Fragmenty renderowania mogą być również przekazywane jako argumenty do [składników szablonów](xref:blazor/templated-components).

```razor
@_timeTemplate

@_petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment _timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> _petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

Renderowane dane wyjściowe poprzedniego kodu:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a>Skalowalne obrazy grafiki wektorowej (SVG)

Od Blazor czasu renderowania html, obrazy obsługiwane przez przeglądarkę, `<img>` w tym scalable vector graphics (SVG) obrazy *(.svg),* są obsługiwane przez tag:

```html
<img alt="Example image" src="some-image.svg" />
```

Podobnie obrazy SVG są obsługiwane w regułach CSS pliku arkusza stylów (*.css*):

```css
.my-element {
    background-image: url("some-image.svg");
}
```

Jednak wbudowane znaczniki SVG nie są obsługiwane we wszystkich scenariuszach. Jeśli `<svg>` umieścisz znacznik bezpośrednio w pliku komponentu (*.brzytwa*), podstawowe renderowanie obrazu jest obsługiwane, ale wiele zaawansowanych scenariuszy nie jest jeszcze obsługiwanych. Na przykład `<use>` tagi nie są obecnie przestrzegane i `@bind` nie mogą być używane z niektórymi tagami SVG. Oczekujemy, że zajmiemy się tymi ograniczeniami w przyszłej wersji.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:security/blazor/server>&ndash; Zawiera wskazówki Blazor dotyczące tworzenia aplikacji serwera, które muszą zmagać się z wyczerpaniem zasobów.
