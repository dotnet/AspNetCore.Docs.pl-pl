---
title: Tworzenie i używanie składników ASP.NET Core Razor
author: guardrex
description: Dowiedz się, jak tworzyć Razor i używać składników, w tym jak powiązać z danymi, obsługiwać zdarzenia i zarządzać cyklem życia składników.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/21/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components
ms.openlocfilehash: 9e36a3239e703e1279feafc65288a1f9ec82c277
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967184"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>Tworzenie i używanie składników Razor ASP.NET Core

[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)i [Tobias Bartsch](https://www.aveo-solutions.com/)

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

Blazoraplikacje są kompilowane przy użyciu *składników*programu. Składnik jest niezależnym fragmentem interfejsu użytkownika (UI), takim jak strona, okno dialogowe lub formularz. Składnik zawiera znaczniki HTML i logikę przetwarzania wymagane do iniekcji danych lub reagowania na zdarzenia interfejsu użytkownika. Składniki są elastyczne i lekkie. Mogą być zagnieżdżane, ponownie używane i udostępniane między projektami.

## <a name="component-classes"></a>Klasy składników

Składniki są zaimplementowane w [Razor](xref:mvc/views/razor) plikach składników (*. Razor*) przy użyciu kombinacji języka C# i znaczników HTML. Składnik w programie Blazor jest formalnie nazywany * Razor składnikiem*.

Nazwa składnika musi rozpoczynać się wielką literą. Na przykład *MyCoolComponent. Razor* jest prawidłowy, a *MyCoolComponent. Razor* jest nieprawidłowy.

Interfejs użytkownika dla składnika jest definiowany przy użyciu języka HTML. Logika renderowania dynamicznego (na przykład pętle, warunkowe, wyrażenia) jest dodawana przy użyciu osadzonej [Razor](xref:mvc/views/razor)składni języka C# o nazwie. Po skompilowaniu aplikacji logika kodu HTML i renderowania języka C# jest konwertowana na klasę składnika. Nazwa wygenerowanej klasy jest zgodna z nazwą pliku.

Elementy członkowskie klasy składnika są zdefiniowane w `@code` bloku. W `@code` bloku stan składnika (właściwości, pola) jest określany przy użyciu metod obsługi zdarzeń lub definiowania innej logiki składnika. Dozwolony jest więcej `@code` niż jeden blok.

Składowe składnika mogą być używane jako część logiki renderowania składnika przy użyciu wyrażeń języka C#, które zaczynają się od `@`. Na przykład pole C# jest renderowane przez utworzenie prefiksu `@` do nazwy pola. Poniższy przykład szacuje i renderuje:

* `headingFontStyle`na wartość właściwości CSS dla elementu `font-style`.
* `headingText`do zawartości `<h1>` elementu.

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

Po pierwszym wyrenderowaniu składnika składnik generuje jego drzewo renderowania w odpowiedzi na zdarzenia. BlazorNastępnie porównuje nowe drzewo renderowania z poprzednim i stosuje wszelkie modyfikacje Document Object Model przeglądarki (DOM).

Składniki to zwykłe klasy języka C# i można je umieścić w dowolnym miejscu w projekcie. Składniki, które generują strony sieci Web, zwykle znajdują się w folderze *strony* . Składniki niestronicowe są często umieszczane w folderze *udostępnionym* lub w folderze niestandardowym dodanym do projektu.

Zazwyczaj przestrzeń nazw składnika pochodzi od głównej przestrzeni nazw aplikacji i lokalizacji składnika (folderu) w aplikacji. Jeśli główna przestrzeń nazw aplikacji jest `BlazorApp` i `Counter` znajduje się w folderze *stron* :

* Przestrzeń `Counter` nazw składnika to `BlazorApp.Pages`.
* W pełni kwalifikowana nazwa typu składnika to `BlazorApp.Pages.Counter`.

Aby uzyskać więcej informacji, zobacz sekcję [Importowanie składników](#import-components) .

Aby użyć folderu niestandardowego, należy dodać przestrzeń nazw folderu niestandardowego do składnika nadrzędnego lub pliku *_Imports. Razor* aplikacji. Na przykład następująca przestrzeń nazw sprawia, że składniki w folderze *Components* są dostępne, gdy główna przestrzeń nazw `BlazorApp`aplikacji to:

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a>Statyczne zasoby

Blazorzgodnie z Konwencją ASP.NET Core aplikacje umieszczające statyczne zasoby w [folderze katalogu głównego sieci Web (wwwroot)](xref:fundamentals/index#web-root)projektu.

Użyj ścieżki względnej (`/`), aby odwołać się do katalogu głównego sieci Web dla statycznego elementu zawartości. W poniższym przykładzie *logo. png* znajduje się fizycznie w folderze *{Project root}/wwwroot/images* :

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razorskładniki nie **obsługują** notacji ukośnika odwrotnego`~/`().

Aby uzyskać informacje na temat ustawiania ścieżki podstawowej aplikacji, zobacz <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="tag-helpers-arent-supported-in-components"></a>Pomocnicy tagów nie są obsługiwani w składnikach

[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) nie są obsługiwani w Razor składnikach (pliki *. Razor* ). Aby zapewnić funkcję przypominającą pomocnik tagów w Blazorprogramie, należy utworzyć składnik o tej samej funkcji co pomocnik tagów i użyć składnika zamiast niego.

## <a name="use-components"></a>Używanie składników

Składniki mogą zawierać inne składniki, deklarując je za pomocą składni elementu HTML. Znaczniki użycia składnika wyglądają jak tag HTML, gdzie nazwa znacznika jest typem składnika.

Poniższy znacznik w *indeksie. Razor* renderuje `HeadingComponent` wystąpienie:

```razor
<HeadingComponent />
```

*Składniki/HeadingComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

Jeśli składnik zawiera element HTML z wielką literą, która nie jest zgodna z nazwą składnika, jest emitowane ostrzeżenie wskazujące, że element ma nieoczekiwaną nazwę. Dodanie `@using` dyrektywy dla przestrzeni nazw składnika sprawia, że składnik jest dostępny, co rozwiązuje ostrzeżenie.

## <a name="routing"></a>Routing

Routing w Blazor programie jest realizowany przez dostarczenie szablonu trasy do każdego dostępnego składnika w aplikacji.

Gdy Razor plik z `@page` dyrektywą jest kompilowany, wygenerowana Klasa ma określony <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> szablon trasy. W czasie wykonywania router szuka klas składników za pomocą `RouteAttribute` i renderuje, w zależności od tego, który składnik ma szablon trasy zgodny z ŻĄDANYM adresem URL.

```razor
@page "/ParentComponent"

...
```

Aby uzyskać więcej informacji, zobacz <xref:blazor/routing>.

## <a name="parameters"></a>Parametry

### <a name="route-parameters"></a>Parametry trasy

Składniki mogą odbierać parametry trasy z szablonu trasy dostarczonego w `@page` dyrektywie. Router używa parametrów trasy, aby wypełnić odpowiednie parametry składnika.

*Strony/RouteParameter. Razor*:

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

Parametry opcjonalne nie są obsługiwane, więc `@page` dwie dyrektywy są stosowane w powyższym przykładzie. Pierwszy zezwala na nawigowanie do składnika bez parametru. Druga `@page` dyrektywa odbiera parametr `{text}` Route i przypisuje wartość do `Text` właściwości.

*Catch-all* `*`/`**`parametrów (), która przechwytuje ścieżkę między wieloma granicami folderów, **nie** jest obsługiwana Razor w składnikach (*. Razor*).

### <a name="component-parameters"></a>Parametry składnika

Składniki mogą mieć *Parametry składnika*, które są zdefiniowane przy użyciu właściwości publicznych w klasie składnika z `[Parameter]` atrybutem. Użyj atrybutów, aby określić argumenty dla składnika w znaczniku.

*Składniki/ChildComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

W poniższym przykładzie z przykładowej aplikacji `ParentComponent` ustawia wartość `Title` właściwości. `ChildComponent`

*Strony/ParentComponent. Razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> Nie należy tworzyć składników, które zapisują do własnych *parametrów składników*, zamiast tego należy użyć pola private. Aby uzyskać więcej informacji, zobacz sekcję [nie twórz składników, które zapisują we własnych właściwościach parametrów](#dont-create-components-that-write-to-their-own-parameter-properties) .

## <a name="child-content"></a>Zawartość podrzędna

Składniki mogą ustawiać zawartość innego składnika. Składnik Assigner zawiera zawartość między tagami, które określają składnik do odbioru.

W poniższym przykładzie `ChildComponent` ma `ChildContent` właściwość, która reprezentuje element `RenderFragment`, który reprezentuje segment interfejsu użytkownika do renderowania. Wartość `ChildContent` jest umieszczana w znacznikach składnika, gdzie zawartość powinna być renderowana. Wartość `ChildContent` jest odbierana ze składnika nadrzędnego i renderowany w panelu uruchamiania `panel-body`.

*Składniki/ChildComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> Właściwość otrzymująca `RenderFragment` zawartość musi być nazywana `ChildContent` Konwencją.

`ParentComponent` W przykładowej aplikacji można dostarczyć zawartość do renderowania `ChildComponent` przez umieszczenie zawartości wewnątrz `<ChildComponent>` tagów.

*Strony/ParentComponent. Razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Korzystając atrybutów i dowolne parametry

Składniki mogą przechwytywać i renderować dodatkowe atrybuty oprócz zadeklarowanych parametrów składnika. Dodatkowe atrybuty mogą być przechwytywane w słowniku, a następnie *splatted* na element, gdy składnik jest renderowany przy [`@attributes`](xref:mvc/views/razor#attributes) Razor użyciu dyrektywy. Ten scenariusz jest przydatny podczas definiowania składnika, który generuje element znaczników, który obsługuje różne dostosowania. Na przykład może być żmudnym do definiowania atrybutów oddzielnie dla `<input>` , który obsługuje wiele parametrów.

W poniższym `<input>` przykładzie pierwszy element (`id="useIndividualParams"`) używa pojedynczych parametrów składnika, podczas gdy drugi `<input>` element (`id="useAttributesDict"`) używa atrybutu korzystając:

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

Typ parametru musi być zaimplementowany `IEnumerable<KeyValuePair<string, object>>` przy użyciu kluczy ciągu. Korzystanie `IReadOnlyDictionary<string, object>` z programu jest również opcją w tym scenariuszu.

Renderowane `<input>` elementy korzystające z obu metod są identyczne:

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

Aby zaakceptować dowolne atrybuty, zdefiniuj parametr składnika przy użyciu `[Parameter]` atrybutu z `CaptureUnmatchedValues` właściwością ustawioną na `true`:

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

`CaptureUnmatchedValues` Właściwość on `[Parameter]` umożliwia dopasowanie parametru do wszystkich atrybutów, które nie są zgodne z żadnym innym parametrem. Składnik może definiować tylko jeden parametr z `CaptureUnmatchedValues`. Typ właściwości używany z elementem `CaptureUnmatchedValues` musi być możliwy do przypisania `Dictionary<string, object>` z klucza ciągu. `IEnumerable<KeyValuePair<string, object>>`lub `IReadOnlyDictionary<string, object>` są również opcje w tym scenariuszu.

Pozycja `@attributes` względem pozycji atrybutów elementu jest ważna. Gdy `@attributes` są splatted w elemencie, atrybuty są przetwarzane od prawej do lewej (Ostatnia do). Rozważmy następujący przykład składnika, który zużywa `Child` składnik:

*ParentComponent. Razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent. Razor*:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Atrybut składnika jest ustawiony z prawej strony `@attributes` `Child` `extra` Renderowany `Parent` `<div>` składnik zawiera `extra="5"` gdy jest przenoszona przez dodatkowy atrybut, ponieważ atrybuty są przetwarzane od prawej do lewej (od ostatni do pierwszego):

```html
<div extra="5" />
```

W poniższym przykładzie `extra` porządek i `@attributes` jest odwrócony w `Child` składniku: `<div>`

*ParentComponent. Razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent. Razor*:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Renderowane `<div>` w `Parent` składniku zawiera `extra="10"` , gdy jest przenoszona przez dodatkowy atrybut:

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>Przechwyć odwołania do składników

Odwołania do składników zapewniają sposób odwoływania się do wystąpienia składnika, dzięki czemu można wydać polecenia do tego wystąpienia, takie `Show` jak `Reset`lub. Aby przechwycić odwołanie do składnika:

* Dodaj [`@ref`](xref:mvc/views/razor#ref) atrybut do składnika podrzędnego.
* Zdefiniuj pole z tym samym typem co składnik podrzędny.

```razor
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

Gdy składnik jest renderowany, `loginDialog` pole zostanie wypełnione wystąpieniem składnika `MyLoginDialog` podrzędnego. Następnie można wywołać metody .NET w wystąpieniu składnika.

> [!IMPORTANT]
> `loginDialog` Zmienna jest wypełniana tylko po wyrenderowaniu składnika, a jego wyjście zawiera `MyLoginDialog` element. Do tego momentu nie ma niczego do odwołania. Aby manipulować odwołaniami do składników po zakończeniu renderowania składnika, należy użyć [metody OnAfterRenderAsync lub OnAfterRender](xref:blazor/lifecycle#after-component-render).

Aby odwoływać się do składników w pętli, zobacz [przechwytywanie odwołań do wielu podobnych składników podrzędnych (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).

Podczas przechwytywania odwołań do składników użycie podobnej składni do [przechwytywania odwołań do elementów](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)nie jest funkcją międzyoperacyjności języka JavaScript. Odwołania do składników nie są przenoszone&mdash;do kodu JavaScript, są używane tylko w kodzie platformy .NET.

> [!NOTE]
> **Nie** należy używać odwołań do składników do mutacji stanu składników podrzędnych. Zamiast tego należy używać zwykłych parametrów deklaratywnych do przekazywania danych do składników podrzędnych. Użycie normalnych parametrów deklaratywnych powoduje, że składniki podrzędne, które automatycznie uruchamiają się w prawidłowym czasie.

## <a name="invoke-component-methods-externally-to-update-state"></a>Wywołaj metody składnika zewnętrznie, aby zaktualizować stan

Blazorużywa `SynchronizationContext` do wymuszania pojedynczego wątku logicznego wykonywania. [Metody cyklu życia](xref:blazor/lifecycle) składnika i wszelkie wywołania zwrotne zdarzeń, które są wywoływane Blazor przez są wykonywane na `SynchronizationContext`tym serwerze. W przypadku zdarzenia składnik należy zaktualizować w oparciu o zdarzenie zewnętrzne, takie jak czasomierz lub inne powiadomienia, przy użyciu `InvokeAsync` metody, która będzie wysyłana do Blazorużytkownika. `SynchronizationContext`

Rozważmy na przykład *usługę powiadamiania* , która może powiadomić dowolny składnik nasłuchujący zaktualizowanego stanu:

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

* W Blazor obszarze zestaw webassembly Zarejestruj usługę w `Program.Main`:

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* W Blazor obszarze serwer Zarejestruj usługę w `Startup.ConfigureServices`:

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

Aby zaktualizować `NotifierService` składnik, użyj programu:

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

`NotifierService` W poprzednim przykładzie `OnNotify` wywołuje metodę składnika poza. Blazor `SynchronizationContext` `InvokeAsync`służy do przełączania do poprawnego kontekstu i kolejki renderowania.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Użyj \@klawisza, aby kontrolować zachowywanie elementów i składników

Gdy renderuje listę elementów lub składników, a następnie zmienia się elementy lub składniki, Blazoralgorytm różnicowania musi zdecydować, które z poprzednich elementów lub składników mogą być zachowywane i jak obiekty modelu powinny być na nich mapowane. Zwykle ten proces jest automatyczny i można go zignorować, ale istnieją przypadki, w których może być konieczne sterowanie procesem.

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

Zawartość `People` kolekcji może ulec zmianie z wstawionymi, usuniętymi lub z ponownymi zamówieniami. Gdy składnik jest przerenderowany, `<DetailsEditor>` składnik może ulec zmianie, aby otrzymywać różne `Details` wartości parametrów. Może to spowodować bardziej złożone odwzorowanie niż oczekiwano. W niektórych przypadkach odzyskanie może prowadzić do zauważalnych różnic w zachowaniu, takich jak brak fokusu elementu.

Proces mapowania można kontrolować przy użyciu atrybutu [`@key`](xref:mvc/views/razor#key) dyrektywy. `@key`powoduje, że algorytm różnicowego gwarantuje zachowywanie elementów lub składników na podstawie wartości klucza:

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

Po zmianie `People` kolekcji algorytm różnicowy zachowuje skojarzenie między `<DetailsEditor>` wystąpieniami i `person` wystąpieniami:

* Jeśli element `Person` zostanie usunięty z `People` listy, tylko odpowiednie `<DetailsEditor>` wystąpienie zostanie usunięte z interfejsu użytkownika. Inne wystąpienia pozostaną bez zmian.
* Jeśli na `Person` liście zostanie wstawiony w pewnym miejscu, jedno nowe `<DetailsEditor>` wystąpienie zostanie wstawione w odpowiednim położeniu. Inne wystąpienia pozostaną bez zmian.
* W `Person` przypadku ponownego uporządkowania wpisów odpowiednie `<DetailsEditor>` wystąpienia są zachowywane i uporządkowane w interfejsie użytkownika.

W niektórych scenariuszach użycie programu `@key` minimalizuje złożoność operacji renderowania i pozwala uniknąć potencjalnych problemów związanych ze stanem częściowych elementów modelu dom, takich jak pozycja fokusu.

> [!IMPORTANT]
> Klucze są lokalne dla każdego elementu kontenera lub składnika. Klucze nie są porównywane globalnie w całym dokumencie.

### <a name="when-to-use-key"></a>Kiedy używać \@klucza

Zazwyczaj warto używać `@key` zawsze, gdy lista jest renderowana (na przykład w `@foreach` bloku) i odpowiednia wartość istnieje do zdefiniowania. `@key`

Można również użyć `@key` , aby uniemożliwić Blazor zachowanie poddrzewa elementu lub składnika po zmianie obiektu:

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

Jeśli `@currentPerson` zmiany, dyrektywa `@key` Attribute wymusza Blazor odrzucanie całości `<div>` i jego obiektów podrzędnych oraz odbudowa poddrzewa w interfejsie użytkownika o nowych elementach i składnikach. Może to być przydatne, jeśli zachodzi konieczność zagwarantowania, że stan `@currentPerson` interfejsu użytkownika nie jest zachowywany w przypadku zmiany.

### <a name="when-not-to-use-key"></a>Kiedy nie używać \@klucza

W przypadku różnicowania w programie `@key`występuje koszt wydajności. Koszt wydajności nie jest duży, ale określa `@key` tylko, czy kontrolowanie reguł utrwalania elementu lub składnika przynosi korzyści dla aplikacji.

Nawet jeśli `@key` nie jest używany Blazor , program zachowuje elementy podrzędne i wystąpienia składników tak jak to możliwe. Jedyną zaletą korzystania z `@key` programu jest kontrola nad sposobem, w *jaki* wystąpienia modelu są mapowane na zachowane wystąpienia składników, zamiast algorytmu różnicowego, wybierając mapowanie.

### <a name="what-values-to-use-for-key"></a>Wartości, które mają być \@używane dla klucza

Ogólnie rzecz biorąc, warto podać jeden z następujących rodzajów wartości dla `@key`:

* Wystąpienia obiektów modelu (na przykład `Person` wystąpienie takie jak w poprzednim przykładzie). Zapewnia to zachowywanie na podstawie równości odwołań do obiektów.
* Unikatowe identyfikatory (na przykład wartości klucza podstawowego typu `int`, `string`lub `Guid`).

Upewnij się, że wartości `@key` używane do nie kolidują. Jeśli w tym samym elemencie nadrzędnym zostaną wykryte wartości powodujące Blazor konflikt, program zgłosi wyjątek, ponieważ nie może on w sposób jednoznaczny zmapować starych elementów lub składników na nowe elementy lub składniki. Używaj tylko odrębnych wartości, takich jak wystąpienia obiektów lub wartości klucza podstawowego.

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a>Nie twórz składników, które zapisują do własnych właściwości parametrów

Parametry są zastępowane w następujących warunkach:

* Zawartość składnika podrzędnego jest renderowana przy użyciu `RenderFragment`.
* <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>jest wywoływana w składniku nadrzędnym.

Parametry są resetowane, ponieważ składnik nadrzędny jest ponownie renderowany <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> , gdy jest wywoływana, i nowe wartości parametrów są dostarczane do składnika podrzędnego.

Rozważmy następujący `Expander` składnik:

* Renderuje zawartość podrzędną.
* Włącza lub wyłącza wyświetlanie zawartości podrzędnej za pomocą parametru składnika.

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

`Expander` Składnik jest dodawany do składnika nadrzędnego, który może wywołać `StateHasChanged`:

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

Początkowo `Expander` składniki działają niezależnie, gdy ich `Expanded` właściwości są przełączane. Składniki podrzędne utrzymują swoje Stany zgodnie z oczekiwaniami. Gdy `StateHasChanged` jest wywoływana w obiekcie nadrzędnym, `Expanded` parametr pierwszego składnika podrzędnego jest resetowany z powrotem do jego wartości początkowej`true`(). `Expanded` Wartość drugiego `Expander` składnika nie zostanie zresetowana, ponieważ w drugim składniku nie jest renderowana żadna zawartość podrzędna.

Aby zachować stan w poprzednim scenariuszu, użyj *pola prywatnego* w `Expander` składniku, aby zachować stan przełączenia.

Następujący `Expander` składnik:

* Akceptuje wartość `Expanded` parametru składnika z elementu nadrzędnego.
* Przypisuje wartość parametru składnika do *pola prywatnego* (`expanded`) w [zdarzeniu OnInitialized](xref:blazor/lifecycle#component-initialization-methods).
* Używa prywatnego pola do utrzymania stanu wewnętrznego przełączania.

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @expanded)

    @if (expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool expanded;

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

## <a name="partial-class-support"></a>Obsługa klasy częściowej

Razorskładniki są generowane jako klasy częściowe. Razorskładniki są tworzone przy użyciu jednej z następujących metod:

* Kod C# jest zdefiniowany w [`@code`](xref:mvc/views/razor#code) bloku z oznaczeniem HTML i Razor kodem w pojedynczym pliku. BlazorSzablony definiują Razor ich składniki przy użyciu tego podejścia.
* Kod C# jest umieszczany w pliku związanym z kodem zdefiniowanym jako Klasa częściowa.

Poniższy przykład pokazuje składnik domyślny `Counter` z `@code` blokiem w aplikacji wygenerowanej na podstawie Blazor szablonu. Znaczniki HTML, Razor kod i kod C# są w tym samym pliku:

*Counter. Razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

`Counter` Składnik można również utworzyć przy użyciu pliku związanego z kodem z klasą częściową:

*Counter. Razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

*Counter.Razor.cs*:

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

W razie potrzeby dodaj wszystkie wymagane przestrzenie nazw do pliku klasy częściowej. Typowe przestrzenie nazw Razor używane przez składniki obejmują:

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a>Określ klasę bazową

[`@inherits`](xref:mvc/views/razor#inherits) Dyrektywa może służyć do określania klasy bazowej dla składnika. Poniższy przykład pokazuje, jak składnik może dziedziczyć klasę bazową, `BlazorRocksBase`, aby zapewnić właściwości i metody składnika. Klasa bazowa powinna pochodzić od `ComponentBase`.

*Strony/BlazorRocks. Razor*:

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

*BlazorRocksBase.cs*:

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

## <a name="specify-an-attribute"></a>Określ atrybut

Atrybuty można określić w Razor składnikach [`@attribute`](xref:mvc/views/razor#attribute) dyrektywy. Poniższy przykład stosuje `[Authorize]` atrybut do klasy składnika:

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a>Importuj składniki

Przestrzeń nazw składnika utworzona w programie Razor jest oparta na (w kolejności priorytetu):

* [`@namespace`](xref:mvc/views/razor#namespace)oznaczenie w Razor pliku (*. Razor*) Markup (`@namespace BlazorSample.MyNamespace`).
* Projekt znajduje `RootNamespace` się w pliku projektu (`<RootNamespace>BlazorSample</RootNamespace>`).
* Nazwa projektu, pobrana z nazwy pliku projektu (*. csproj*) i ścieżka z katalogu głównego projektu do składnika. Na przykład struktura rozpoznaje *{Project root}/Pages/index.Razor* (*BlazorSample. csproj*) do przestrzeni nazw `BlazorSample.Pages`. Składniki przestrzegają reguł powiązań nazw języka C#. W przypadku `Index` składnika w tym przykładzie składniki należące do zakresu są wszystkich składników:
  * W tym samym folderze *strony*.
  * Składniki w katalogu głównym projektu, które nie określają jawnie innej przestrzeni nazw.

Składniki zdefiniowane w innej przestrzeni nazw są wprowadzane do zakresu za Razorpomocą [`@using`](xref:mvc/views/razor#using) dyrektywy.

Jeśli inny składnik, `NavMenu.razor`,,, istnieje w *BlazorSample/Shared/* folder, można użyć w programie `Index.razor` z następującą `@using` instrukcją:

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

Do składników można także odwoływać się za pomocą ich w pełni kwalifikowanych nazw [`@using`](xref:mvc/views/razor#using) , które nie wymagają dyrektywy:

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> `global::` Kwalifikacja nie jest obsługiwana.
>
> Importowanie składników przy użyciu `using` instrukcji z aliasami (np `@using Foo = Bar`.) nie jest obsługiwane.
>
> Częściowo kwalifikowane nazwy nie są obsługiwane. Na przykład dodawanie `@using BlazorSample` i odwoływanie `NavMenu.razor` się `<Shared.NavMenu></Shared.NavMenu>` za pomocą nie jest obsługiwane.

## <a name="conditional-html-element-attributes"></a>Warunkowe atrybuty elementu HTML

Atrybuty elementu HTML są warunkowo renderowane na podstawie wartości .NET. Jeśli wartość jest `false` lub `null`, atrybut nie jest renderowany. Jeśli wartość to `true`, atrybut jest renderowany jako zminimalizowany.

W poniższym przykładzie, określa `IsCompleted` , czy `checked` jest renderowany w znacznikach elementu:

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

Jeśli `IsCompleted` jest `true`, pole wyboru jest renderowane jako:

```html
<input type="checkbox" checked />
```

Jeśli `IsCompleted` jest `false`, pole wyboru jest renderowane jako:

```html
<input type="checkbox" />
```

Aby uzyskać więcej informacji, zobacz <xref:mvc/views/razor>.

> [!WARNING]
> Niektóre atrybuty HTML, takie jak [Aria](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), nie działają prawidłowo, gdy typem .NET jest `bool`. W takich przypadkach należy użyć `string` typu zamiast. `bool`

## <a name="raw-html"></a>Nieprzetworzony kod HTML

Ciągi są zwykle renderowane przy użyciu węzłów tekstowych DOM, co oznacza, że wszystkie znaczniki, które mogą zawierać, są ignorowane i traktowane jako tekst literału. Aby renderować nieprzetworzony kod HTML, zawiń zawartość HTML w `MarkupString` wartości. Wartość jest analizowana jako plik HTML lub SVG i wstawiona do modelu DOM.

> [!WARNING]
> Renderowanie nieprzetworzonego kodu HTML zbudowanego z dowolnego niezaufanego źródła stanowi **zagrożenie bezpieczeństwa** i należy je unikać!

W poniższym przykładzie pokazano, `MarkupString` jak za pomocą typu dodać blok statycznej zawartości HTML do renderowanego danych wyjściowych składnika:

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a>Wartości kaskadowe i parametry

W niektórych scenariuszach nie można przepływać danych z składnika nadrzędnego do składnika potomnego przy użyciu [parametrów składnika](#component-parameters), zwłaszcza gdy istnieje kilka warstw składników. Wartości kaskadowe i parametry rozwiązują ten problem, zapewniając wygodną metodę dla składnika nadrzędnego, aby zapewnić wartość wszystkim jej składnikom potomnym. Kaskadowe wartości i parametry również zapewniają podejście do współrzędnych składników.

### <a name="theme-example"></a>Przykład motywu

W poniższym przykładzie z przykładowej aplikacji `ThemeInfo` Klasa określa informacje o motywie, aby przetworzyć hierarchię składników w taki sposób, aby wszystkie przyciski w danej części aplikacji miały ten sam styl.

*UIThemeClasses/themeinfo wskazuje. cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Składnik nadrzędny może zapewnić kaskadową wartość przy użyciu składnika wartości kaskadowych. `CascadingValue` Składnik otacza poddrzewo hierarchii składników i dostarcza jedną wartość do wszystkich składników w tym poddrzewie.

Przykładowo aplikacja Przykładowa określa informacje o motywie`ThemeInfo`() w jednej z układów aplikacji jako parametr kaskadowy dla wszystkich składników, które tworzą treść układu `@Body` właściwości. `ButtonClass`ma przypisaną wartość `btn-success` w składniku układu. Każdy składnik podrzędny może wykorzystać tę właściwość za pomocą `ThemeInfo` obiektu kaskadowego.

`CascadingValuesParametersLayout`składnika

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

Aby korzystać z wartości kaskadowych, składniki deklarują kaskadowe parametry przy użyciu `[CascadingParameter]` atrybutu. Wartości kaskadowe są powiązane z parametrami kaskadowymi według typu.

W przykładowej aplikacji `CascadingValuesParametersTheme` składnik wiąże wartość `ThemeInfo` kaskadową z parametrem kaskadowym. Parametr służy do ustawiania klasy CSS dla jednego z przycisków wyświetlanych przez składnik.

`CascadingValuesParametersTheme`składnika

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

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
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

Aby przetworzyć kaskadowo wiele wartości tego samego typu w ramach tego samego poddrzewa `Name` , podaj unikatowy `CascadingValue` ciąg dla każdego składnika `CascadingParameter`i odpowiadający mu. W poniższym przykładzie dwa `CascadingValue` składniki kaskaduje różne wystąpienia o `MyCascadingType` nazwie:

```razor
<CascadingValue Value=@parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

W składniku potomnym, kaskadowe parametry odbierają swoje wartości z odpowiednich kaskadowych wartości w składniku nadrzędnym według nazwy:

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>Przykład TabSet

Parametry kaskadowe umożliwiają również współdziałanie składników w hierarchii składników. Rozważmy na przykład następujący przykład *TabSet* w aplikacji przykładowej.

Przykładowa aplikacja ma `ITab` interfejs, który implementuje karty:

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

`CascadingValuesParametersTabSet` Składnik używa `TabSet` składnika, który zawiera kilka `Tab` składników:

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

Składniki podrzędne `Tab` nie są jawnie przenoszone jako parametry do `TabSet`. Zamiast tego składniki podrzędne `Tab` są częścią zawartości elementu podrzędnego `TabSet`. Jednak `TabSet` nadal musi wiedzieć o każdym `Tab` składniku, aby można było renderować nagłówki i aktywną kartę. Aby umożliwić tę koordynację bez konieczności stosowania dodatkowego kodu `TabSet` , składnik *może stanowić wartość kaskadową* , która następnie jest wybierana przez składniki potomne `Tab` .

`TabSet`składnika

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

Składniki potomne `Tab` przechwytują zawierający `TabSet` jako parametr kaskadowy, więc `Tab` składniki dodają się do `TabSet` i koordynują, na której karcie jest aktywna.

`Tab`składnika

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razorprzystawki

Fragmenty renderowania można definiować przy użyciu Razor składni szablonu. RazorSzablony są sposobem definiowania fragmentu interfejsu użytkownika i przyjmuje następujący format:

```razor
@<{HTML tag}>...</{HTML tag}>
```

Poniższy przykład ilustruje sposób określania `RenderFragment` i `RenderFragment<T>` wartości oraz renderowania szablonów bezpośrednio w składniku. Fragmenty renderowania mogą być również przekazane jako argumenty do [składników z szablonem](xref:blazor/templated-components).

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

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

## <a name="scalable-vector-graphics-svg-images"></a>Skalowalne obrazy wektorowe (SVG)

Ponieważ Blazor renderuje HTML, obrazy obsługiwane przez przeglądarkę, w tym obrazy*SVG (Scalable Vector Graphics),* są obsługiwane przez `<img>` Tag:

```html
<img alt="Example image" src="some-image.svg" />
```

Podobnie Obrazy SVG są obsługiwane w regułach CSS pliku arkusza stylów (*CSS*):

```css
.my-element {
    background-image: url("some-image.svg");
}
```

Jednak wbudowane znaczniki SVG nie są obsługiwane we wszystkich scenariuszach. Jeśli umieścisz `<svg>` tag bezpośrednio w pliku składnika (*. Razor*), podstawowe renderowanie obrazu jest obsługiwane, ale wiele scenariuszy zaawansowanych nie jest jeszcze obsługiwanych. Na przykład `<use>` Tagi nie są obecnie przestrzegane i `@bind` nie mogą być używane z niektórymi tagami SVG. Oczekujemy, że te ograniczenia są opisane w przyszłej wersji.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:security/blazor/server/threat-mitigation>&ndash; Zawiera wskazówki dotyczące tworzenia Blazor aplikacji serwera, które muszą będą konkurować o z wyczerpaniem zasobów.
