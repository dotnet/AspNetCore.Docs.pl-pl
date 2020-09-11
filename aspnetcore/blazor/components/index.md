---
title: Tworzenie i używanie Razor składników ASP.NET Core
author: guardrex
description: Dowiedz się, jak tworzyć i używać Razor składników, w tym jak powiązać z danymi, obsługiwać zdarzenia i zarządzać cyklem życia składników.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/19/2020
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
uid: blazor/components/index
ms.openlocfilehash: be1584e72fc1504ac9f8ca10a6b084c95a579b5b
ms.sourcegitcommit: 8fcb08312a59c37e3542e7a67dad25faf5bb8e76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2020
ms.locfileid: "90009625"
---
# <a name="create-and-use-aspnet-core-no-locrazor-components"></a>Tworzenie i używanie Razor składników ASP.NET Core

[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)i [Tobias Bartsch](https://www.aveo-solutions.com/)

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

Blazor aplikacje są kompilowane przy użyciu *składników*programu. Składnik jest niezależnym fragmentem interfejsu użytkownika (UI), takim jak strona, okno dialogowe lub formularz. Składnik zawiera znaczniki HTML i logikę przetwarzania wymagane do iniekcji danych lub reagowania na zdarzenia interfejsu użytkownika. Składniki są elastyczne i lekkie. Mogą być zagnieżdżane, ponownie używane i udostępniane między projektami.

## <a name="component-classes"></a>Klasy składników

Składniki są implementowane w [Razor](xref:mvc/views/razor) plikach składników ( `.razor` ) przy użyciu kombinacji języka C# i znaczników HTML. Składnik w programie Blazor jest formalnie nazywany * Razor składnikiem*.

### <a name="no-locrazor-syntax"></a>Razor obowiązuje

Razor składniki w Blazor aplikacjach szeroko wykorzystują Razor składnię. Jeśli nie znasz Razor języka znaczników, zalecamy przeczytanie <xref:mvc/views/razor> przed kontynuowaniem.

Podczas uzyskiwania dostępu do zawartości w Razor składni należy zwrócić szczególną uwagę na następujące sekcje:

* [Dyrektywy](xref:mvc/views/razor#directives): `@` — wstępnie ustalone zastrzeżone słowa kluczowe, które zwykle zmieniają sposób, w jaki znaczniki składnika są analizowane lub działają.
* [Atrybuty dyrektywy](xref:mvc/views/razor#directive-attributes): `@` — wstępnie ustalone zastrzeżone słowa kluczowe, które zwykle zmieniają sposób analizowania lub działania elementów składnika.

### <a name="names"></a>Nazwy

Nazwa składnika musi rozpoczynać się wielką literą. Na przykład `MyCoolComponent.razor` prawidłowe i `myCoolComponent.razor` jest nieprawidłowe.

### <a name="routing"></a>Routing

Routing w programie jest realizowany Blazor przez dostarczenie szablonu trasy do każdego dostępnego składnika w aplikacji. Gdy Razor plik z [`@page`][9] dyrektywą jest kompilowany, wygenerowana Klasa ma określony <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> szablon trasy. W czasie wykonywania router szuka klas składników za pomocą <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> i renderuje, w zależności od tego, który składnik ma szablon trasy zgodny z żądanym adresem URL. Aby uzyskać więcej informacji, zobacz <xref:blazor/fundamentals/routing>.

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a>Znaczniki

Interfejs użytkownika dla składnika jest definiowany przy użyciu języka HTML. Logika renderowania dynamicznego (na przykład pętle, warunkowe, wyrażenia) jest dodawana przy użyciu osadzonej składni języka C# o nazwie *Razor* . Po skompilowaniu aplikacji logika kodu HTML i renderowania języka C# jest konwertowana na klasę składnika. Nazwa wygenerowanej klasy jest zgodna z nazwą pliku.

Elementy członkowskie klasy składnika są zdefiniowane w [`@code`][1] bloku. W [`@code`][1] bloku stan składnika (właściwości, pola) jest określany przy użyciu metod obsługi zdarzeń lub definiowania innej logiki składnika. Dozwolony jest więcej niż jeden [`@code`][1] blok.

Składowe składnika mogą być używane jako część logiki renderowania składnika przy użyciu wyrażeń języka C#, które zaczynają się od `@` . Na przykład pole C# jest renderowane przez utworzenie prefiksu `@` do nazwy pola. Poniższy przykład szacuje i renderuje:

* `headingFontStyle` na wartość właściwości CSS dla elementu `font-style` .
* `headingText` do zawartości `<h1>` elementu.

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

Po pierwszym wyrenderowaniu składnika składnik generuje jego drzewo renderowania w odpowiedzi na zdarzenia. Blazor Następnie porównuje nowe drzewo renderowania z poprzednim i stosuje wszelkie modyfikacje Document Object Model przeglądarki (DOM).

Składniki to zwykłe klasy języka C# i można je umieścić w dowolnym miejscu w projekcie. Składniki, które generują strony sieci Web, zwykle znajdują się w `Pages` folderze. Składniki niestronicowe są często umieszczane w `Shared` folderze lub do folderu niestandardowego dodanego do projektu.

### <a name="namespaces"></a>Przestrzenie nazw

Zazwyczaj przestrzeń nazw składnika pochodzi od głównej przestrzeni nazw aplikacji i lokalizacji składnika (folderu) w aplikacji. Jeśli główna przestrzeń nazw aplikacji jest `BlazorSample` i znajduje się `Counter` w `Pages` folderze:

* `Counter`Przestrzeń nazw składnika to `BlazorSample.Pages` .
* W pełni kwalifikowana nazwa typu składnika to `BlazorSample.Pages.Counter` .

W przypadku folderów niestandardowych, które zawierają składniki, Dodaj [`@using`][2] dyrektywę do składnika nadrzędnego lub do `_Imports.razor` pliku aplikacji. W poniższym przykładzie są dostępne składniki w `Components` folderze:

```razor
@using BlazorSample.Components
```

Do składników można także odwoływać się za pomocą ich w pełni kwalifikowanych nazw, które nie wymagają [`@using`][2] dyrektywy:

```razor
<BlazorSample.Components.MyComponent />
```

Przestrzeń nazw składnika utworzona w programie Razor jest oparta na (w kolejności priorytetu):

* [`@namespace`][8] oznaczenie w Razor pliku ( `.razor` ) Markup ( `@namespace BlazorSample.MyNamespace` ).
* Projekt znajduje się `RootNamespace` w pliku projektu ( `<RootNamespace>BlazorSample</RootNamespace>` ).
* Nazwa projektu, pobrana z pliku projektu nazwa pliku ( `.csproj` ) i ścieżka z katalogu głównego projektu do składnika. Na przykład struktura rozpoznaje `{PROJECT ROOT}/Pages/Index.razor` ( `BlazorSample.csproj` ) do przestrzeni nazw `BlazorSample.Pages` . Składniki przestrzegają reguł powiązań nazw języka C#. W przypadku `Index` składnika w tym przykładzie składniki należące do zakresu są wszystkich składników:
  * W tym samym folderze, `Pages` .
  * Składniki w katalogu głównym projektu, które nie określają jawnie innej przestrzeni nazw.

> [!NOTE]
> `global::`Kwalifikacja nie jest obsługiwana.
>
> Importowanie składników przy użyciu instrukcji z aliasami [`using`](/dotnet/csharp/language-reference/keywords/using-statement) (np `@using Foo = Bar` .) nie jest obsługiwane.
>
> Częściowo kwalifikowane nazwy nie są obsługiwane. Na przykład dodawanie `@using BlazorSample` i odwoływanie się do `NavMenu` składnika ( `NavMenu.razor` ) za pomocą `<Shared.NavMenu></Shared.NavMenu>` nie jest obsługiwane.

### <a name="partial-class-support"></a>Obsługa klasy częściowej

Razor składniki są generowane jako klasy częściowe. Razor składniki są tworzone przy użyciu jednej z następujących metod:

* Kod C# jest zdefiniowany w [`@code`][1] bloku z oznaczeniem HTML i Razor kodem w pojedynczym pliku. Blazor Szablony definiują ich Razor składniki przy użyciu tego podejścia.
* Kod C# jest umieszczany w pliku związanym z kodem zdefiniowanym jako Klasa częściowa.

Poniższy przykład pokazuje `Counter` składnik domyślny z [`@code`][1] blokiem w aplikacji wygenerowanej na podstawie Blazor szablonu. Znaczniki HTML, Razor kod i kod C# są w tym samym pliku:

`Pages/Counter.razor`:

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

`Counter`Składnik można również utworzyć przy użyciu pliku związanego z kodem z klasą częściową:

`Pages/Counter.razor`:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

`Counter.razor.cs`:

```csharp
namespace BlazorSample.Pages
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

W razie potrzeby dodaj wszystkie wymagane przestrzenie nazw do pliku klasy częściowej. Typowe przestrzenie nazw używane przez Razor składniki obejmują:

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> [`@using`][2] dyrektywy w `_Imports.razor` pliku są stosowane tylko do Razor plików ( `.razor` ), nie plików C# ( `.cs` ).

### <a name="specify-a-base-class"></a>Określ klasę bazową

[`@inherits`][6]Dyrektywa może służyć do określania klasy bazowej dla składnika. Poniższy przykład pokazuje, jak składnik może dziedziczyć klasę bazową, `BlazorRocksBase` , aby zapewnić właściwości i metody składnika. Klasa bazowa powinna pochodzić od <xref:Microsoft.AspNetCore.Components.ComponentBase> .

`Pages/BlazorRocks.razor`:

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

`BlazorRocksBase.cs`:

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

### <a name="use-components"></a>Używanie składników

Składniki mogą zawierać inne składniki, deklarując je za pomocą składni elementu HTML. Znaczniki użycia składnika wyglądają jak tag HTML, gdzie nazwa znacznika jest typem składnika.

Następujące znaczniki w programie `Pages/Index.razor` renderuje `HeadingComponent` wystąpienie:

```razor
<HeadingComponent />
```

`Components/HeadingComponent.razor`:

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

Jeśli składnik zawiera element HTML z wielką literą, która nie jest zgodna z nazwą składnika, jest emitowane ostrzeżenie wskazujące, że element ma nieoczekiwaną nazwę. Dodanie [`@using`][2] dyrektywy dla przestrzeni nazw składnika sprawia, że składnik jest dostępny, co rozwiązuje ostrzeżenie.

## <a name="parameters"></a>Parametry

### <a name="route-parameters"></a>Parametry trasy

Składniki mogą odbierać parametry trasy z szablonu trasy dostarczonego w [`@page`][9] dyrektywie. Router używa parametrów trasy, aby wypełnić odpowiednie parametry składnika.

`Pages/RouteParameter.razor`:

[!code-razor[](index/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

Parametry opcjonalne nie są obsługiwane, więc dwie [`@page`][9] dyrektywy są stosowane w powyższym przykładzie. Pierwszy zezwala na nawigowanie do składnika bez parametru. Druga [`@page`][9] dyrektywa odbiera `{text}` parametr Route i przypisuje wartość do `Text` właściwości.

Aby uzyskać informacje dotyczące przechwytywania wszystkich parametrów trasy ( `{*pageRoute}` ), które przechwytują ścieżki między wieloma granicami folderów, zobacz <xref:blazor/fundamentals/routing#catch-all-route-parameters> .

### <a name="component-parameters"></a>Parametry składnika

Składniki mogą mieć *Parametry składnika*, które są zdefiniowane przy użyciu właściwości publicznych w klasie składnika z [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atrybutem. Użyj atrybutów, aby określić argumenty dla składnika w znaczniku.

`Components/ChildComponent.razor`:

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

W poniższym przykładzie z przykładowej aplikacji `ParentComponent` ustawia wartość `Title` właściwości `ChildComponent` .

`Pages/ParentComponent.razor`:

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> Nie należy tworzyć składników, które zapisują do własnych *parametrów składników*, zamiast tego należy użyć pola private. Aby uzyskać więcej informacji, zobacz sekcję [zastępowanie parametrów](#overwritten-parameters) .

## <a name="child-content"></a>Zawartość podrzędna

Składniki mogą ustawiać zawartość innego składnika. Składnik Assigner zawiera zawartość między tagami, które określają składnik do odbioru.

W poniższym przykładzie `ChildComponent` ma `ChildContent` Właściwość, która reprezentuje element <xref:Microsoft.AspNetCore.Components.RenderFragment> , który reprezentuje segment interfejsu użytkownika do renderowania. Wartość `ChildContent` jest umieszczana w znacznikach składnika, gdzie zawartość powinna być renderowana. Wartość `ChildContent` jest odbierana ze składnika nadrzędnego i renderowany w panelu uruchamiania `panel-body` .

`Components/ChildComponent.razor`:

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> Właściwość otrzymująca <xref:Microsoft.AspNetCore.Components.RenderFragment> zawartość musi być nazywana `ChildContent` Konwencją.

`ParentComponent`W przykładowej aplikacji można dostarczyć zawartość do renderowania `ChildComponent` przez umieszczenie zawartości wewnątrz `<ChildComponent>` tagów.

`Pages/ParentComponent.razor`:

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

Ze względu na sposób, w jaki Blazor renderuje zawartość podrzędną, składniki renderowania wewnątrz `for` pętli wymagają zmiennej lokalnego indeksu, jeśli zmienna pętli zwiększania jest używana w zawartości składnika podrzędnego:
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <ChildComponent Param1="@c">
>         Child Content: Count: @current
>     </ChildComponent>
> }
> ```
>
> Alternatywnie możesz użyć `foreach` pętli z <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Param1="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Korzystając atrybutów i dowolne parametry

Składniki mogą przechwytywać i renderować dodatkowe atrybuty oprócz zadeklarowanych parametrów składnika. Dodatkowe atrybuty mogą być przechwytywane w słowniku, a następnie *splatted* na element, gdy składnik jest renderowany przy użyciu [`@attributes`][3] Razor dyrektywy. Ten scenariusz jest przydatny podczas definiowania składnika, który generuje element znaczników, który obsługuje różne dostosowania. Na przykład może być żmudnym do definiowania atrybutów oddzielnie dla `<input>` , który obsługuje wiele parametrów.

W poniższym przykładzie pierwszy `<input>` element ( `id="useIndividualParams"` ) używa pojedynczych parametrów składnika, podczas gdy drugi `<input>` element ( `id="useAttributesDict"` ) używa atrybutu korzystając:

```razor
<input id="useIndividualParams"
       maxlength="@maxlength"
       placeholder="@placeholder"
       required="@required"
       size="@size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    public string maxlength = "10";
    public string placeholder = "Input placeholder text";
    public string required = "required";
    public string size = "50";

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

Typ parametru musi być zaimplementowany `IEnumerable<KeyValuePair<string, object>>` lub `IReadOnlyDictionary<string, object>` z kluczami ciągu.

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

Aby zaakceptować dowolne atrybuty, zdefiniuj parametr składnika przy użyciu [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atrybutu z <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> właściwością ustawioną na `true` :

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>Właściwość on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) umożliwia dopasowanie parametru do wszystkich atrybutów, które nie są zgodne z żadnym innym parametrem. Składnik może definiować tylko jeden parametr z <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> . Typ właściwości używany z elementem <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> musi być możliwy do przypisania z `Dictionary<string, object>` klucza ciągu. `IEnumerable<KeyValuePair<string, object>>` lub `IReadOnlyDictionary<string, object>` są również opcje w tym scenariuszu.

Pozycja [`@attributes`][3] względem pozycji atrybutów elementu jest ważna. Gdy [`@attributes`][3] są splatted w elemencie, atrybuty są przetwarzane od prawej do lewej (Ostatnia do). Rozważmy następujący przykład składnika, który zużywa `Child` składnik:

`ParentComponent.razor`:

```razor
<ChildComponent extra="10" />
```

`ChildComponent.razor`:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

`Child` `extra` Atrybut składnika jest ustawiony z prawej strony [`@attributes`][3] . `Parent`Renderowany składnik `<div>` zawiera gdy jest `extra="5"` przenoszona przez dodatkowy atrybut, ponieważ atrybuty są przetwarzane od prawej do lewej (od ostatni do pierwszego):

```html
<div extra="5" />
```

W poniższym przykładzie porządek `extra` i [`@attributes`][3] jest odwrócony w `Child` składniku `<div>` :

`ParentComponent.razor`:

```razor
<ChildComponent extra="10" />
```

`ChildComponent.razor`:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Renderowane `<div>` w `Parent` składniku zawiera, `extra="10"` gdy jest przenoszona przez dodatkowy atrybut:

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>Przechwyć odwołania do składników

Odwołania do składników zapewniają sposób odwoływania się do wystąpienia składnika, dzięki czemu można wydać polecenia do tego wystąpienia, takie jak `Show` lub `Reset` . Aby przechwycić odwołanie do składnika:

* Dodaj [`@ref`][4] atrybut do składnika podrzędnego.
* Zdefiniuj pole z tym samym typem co składnik podrzędny.

```razor
<CustomLoginDialog @ref="loginDialog" ... />

@code {
    private CustomLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

Gdy składnik jest renderowany, `loginDialog` pole zostanie wypełnione `MyLoginDialog` wystąpieniem składnika podrzędnego. Następnie można wywołać metody .NET w wystąpieniu składnika.

> [!IMPORTANT]
> `loginDialog`Zmienna jest wypełniana tylko po wyrenderowaniu składnika, a jego wyjście zawiera `MyLoginDialog` element. Dopóki składnik nie zostanie renderowany, nie ma niczego do odwołania.
>
> Aby manipulować odwołaniami do składników po zakończeniu renderowania składnika, użyj [ `OnAfterRenderAsync` `OnAfterRender` metod lub](xref:blazor/components/lifecycle#after-component-render).
>
> Aby użyć zmiennej odniesienia z obsługą zdarzeń, należy użyć wyrażenia lambda lub przypisać delegata obsługi zdarzeń w [ `OnAfterRenderAsync` `OnAfterRender` metodach lub](xref:blazor/components/lifecycle#after-component-render). Daje to pewność, że zmienna odwołania zostanie przypisana przed przypisaniem programu obsługi zdarzeń.
>
> ```razor
> <button type="button" 
>     @onclick="@(() => loginDialog.DoSomething())">Do Something</button>
>
> <MyLoginDialog @ref="loginDialog" ... />
>
> @code {
>     private MyLoginDialog loginDialog;
> }
> ```

Aby odwoływać się do składników w pętli, zobacz [przechwytywanie odwołań do wielu podobnych składników podrzędnych (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).

Podczas przechwytywania odwołań do składników użycie podobnej składni do [przechwytywania odwołań do elementów](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)nie jest funkcją międzyoperacyjności języka JavaScript. Odwołania do składników nie są przesyłane do kodu JavaScript. Odwołania do składników są używane tylko w kodzie platformy .NET.

> [!NOTE]
> **Nie** należy używać odwołań do składników do mutacji stanu składników podrzędnych. Zamiast tego należy używać zwykłych parametrów deklaratywnych do przekazywania danych do składników podrzędnych. Użycie normalnych parametrów deklaratywnych powoduje, że składniki podrzędne, które automatycznie uruchamiają się w prawidłowym czasie.

## <a name="synchronization-context"></a>Kontekst synchronizacji

Blazor używa kontekstu synchronizacji ( <xref:System.Threading.SynchronizationContext> ) w celu wymuszenia pojedynczego wątku logicznego wykonywania. [Metody cyklu życia](xref:blazor/components/lifecycle) składnika i wszelkie wywołania zwrotne zdarzeń, które są wywoływane przez Blazor są wykonywane w kontekście synchronizacji.

Blazor Serverkontekst synchronizacji programu próbuje emulować środowisko jednowątkowe, aby dokładnie pasowało do modelu webassembly w przeglądarce, który jest pojedynczym wątkiem. W dowolnym momencie prace są wykonywane na dokładnie jednym wątku, co daje wrażenie pojedynczego wątku logicznego. Nie wykonano jednocześnie dwóch operacji.

### <a name="avoid-thread-blocking-calls"></a>Unikaj wywołań blokowania wątków

Ogólnie rzecz biorąc nie należy wywoływać następujących metod. Poniższe metody blokują wątek i w ten sposób blokują pracę aplikacji przed jej <xref:System.Threading.Tasks.Task> ukończeniem:

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a>Wywołaj metody składnika zewnętrznie, aby zaktualizować stan

W przypadku zdarzenia składnika należy zaktualizować na podstawie zdarzenia zewnętrznego, takiego jak czasomierz lub inne powiadomienia, użyj `InvokeAsync` metody, która jest wysyłana do Blazor kontekstu synchronizacji. Rozważmy na przykład *usługę powiadamiania* , która może powiadomić dowolny składnik nasłuchujący zaktualizowanego stanu:

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

Zarejestruj `NotifierService` :

* W programie Blazor WebAssembly Zarejestruj usługę jako pojedynczą w `Program.Main` :

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* W programie Blazor Server Zarejestruj usługę jako objętą zakresem `Startup.ConfigureServices` :

  ```csharp
  services.AddScoped<NotifierService>();
  ```

`NotifierService`Aby zaktualizować składnik, użyj programu:

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

W poprzednim przykładzie `NotifierService` wywołuje `OnNotify` metodę składnika poza Blazor kontekstem synchronizacji. `InvokeAsync` służy do przełączania do poprawnego kontekstu i kolejki renderowania.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Użyj \@ klawisza, aby kontrolować zachowywanie elementów i składników

Gdy renderuje listę elementów lub składników, a następnie zmienia się elementy lub składniki, Blazor algorytm różnicowania musi zdecydować, które z poprzednich elementów lub składników mogą być zachowywane i jak obiekty modelu powinny być na nich mapowane. Zwykle ten proces jest automatyczny i można go zignorować, ale istnieją przypadki, w których może być konieczne sterowanie procesem.

Rozpatrzmy następujący przykład:

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

Proces mapowania można kontrolować przy użyciu [`@key`][5] atrybutu dyrektywy. [`@key`][5] powoduje, że algorytm różnicowego gwarantuje zachowywanie elementów lub składników na podstawie wartości klucza:

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

Po `People` zmianie kolekcji algorytm różnicowy zachowuje skojarzenie między `<DetailsEditor>` wystąpieniami i `person` wystąpieniami:

* Jeśli element `Person` zostanie usunięty z `People` listy, tylko odpowiednie `<DetailsEditor>` wystąpienie zostanie usunięte z interfejsu użytkownika. Inne wystąpienia pozostaną bez zmian.
* Jeśli na `Person` liście zostanie wstawiony w pewnym miejscu, jedno nowe `<DetailsEditor>` wystąpienie zostanie wstawione w odpowiednim położeniu. Inne wystąpienia pozostaną bez zmian.
* `Person`W przypadku ponownego uporządkowania wpisów odpowiednie `<DetailsEditor>` wystąpienia są zachowywane i uporządkowane w interfejsie użytkownika.

W niektórych scenariuszach użycie programu [`@key`][5] minimalizuje złożoność operacji renderowania i pozwala uniknąć potencjalnych problemów związanych ze stanem częściowych elementów modelu dom, takich jak pozycja fokusu.

> [!IMPORTANT]
> Klucze są lokalne dla każdego elementu kontenera lub składnika. Klucze nie są porównywane globalnie w całym dokumencie.

### <a name="when-to-use-key"></a>Kiedy używać \@ klucza

Zazwyczaj warto używać [`@key`][5] zawsze, gdy lista jest renderowana (na przykład w bloku [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) ), a odpowiednia wartość istnieje do zdefiniowania [`@key`][5] .

Można również użyć, [`@key`][5] Aby uniemożliwić Blazor zachowanie poddrzewa elementu lub składnika po zmianie obiektu:

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

Jeśli `@currentPerson` zmiany, [`@key`][5] dyrektywa Attribute wymusza Blazor odrzucanie całości `<div>` i jego obiektów podrzędnych oraz odbudowa poddrzewa w interfejsie użytkownika o nowych elementach i składnikach. Może to być przydatne, jeśli zachodzi konieczność zagwarantowania, że stan interfejsu użytkownika nie jest zachowywany w przypadku `@currentPerson` zmiany.

### <a name="when-not-to-use-key"></a>Kiedy nie używać \@ klucza

W przypadku różnicowania w programie występuje koszt wydajności [`@key`][5] . Koszt wydajności nie jest duży, ale określa tylko, [`@key`][5] czy kontrolowanie reguł utrwalania elementu lub składnika przynosi korzyści dla aplikacji.

Nawet jeśli [`@key`][5] nie jest używany, program Blazor zachowuje elementy podrzędne i wystąpienia składników tak jak to możliwe. Jedyną zaletą korzystania z programu [`@key`][5] jest kontrola nad *sposobem* , w jaki wystąpienia modelu są mapowane na zachowane wystąpienia składników, zamiast algorytmu różnicowego, wybierając mapowanie.

### <a name="what-values-to-use-for-key"></a>Wartości, które mają być używane dla \@ klucza

Ogólnie rzecz biorąc, warto podać jeden z następujących rodzajów wartości dla [`@key`][5] :

* Wystąpienia obiektów modelu (na przykład `Person` wystąpienie takie jak w poprzednim przykładzie). Zapewnia to zachowywanie na podstawie równości odwołań do obiektów.
* Unikatowe identyfikatory (na przykład wartości klucza podstawowego typu `int` , `string` lub `Guid` ).

Upewnij się, że wartości używane do [`@key`][5] nie kolidują. Jeśli w tym samym elemencie nadrzędnym zostaną wykryte wartości powodujące konflikt, program Blazor zgłosi wyjątek, ponieważ nie może on w sposób jednoznaczny zmapować starych elementów lub składników na nowe elementy lub składniki. Używaj tylko odrębnych wartości, takich jak wystąpienia obiektów lub wartości klucza podstawowego.

## <a name="overwritten-parameters"></a>Zastępowanie parametrów

Podawane są nowe wartości parametrów, zwykle zastępując istniejące, gdy składnik nadrzędny zostanie przerenderowany.

Rozważmy następujący `Expander` składnik:

* Renderuje zawartość podrzędną.
* Włącza lub wyłącza wyświetlanie zawartości podrzędnej za pomocą parametru składnika.

```razor
<div @onclick="@Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>Expanded</code> = @Expanded)</h2>

        @if (Expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
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

`Expander`Składnik jest dodawany do składnika nadrzędnego, który może wywołać <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> :

```razor
@page "/expander"

<Expander Expanded="true">
    Expander 1 content
</Expander>

<Expander Expanded="true" />

<button @onclick="StateHasChanged">
    Call StateHasChanged
</button>
```

Początkowo `Expander` składniki działają niezależnie, gdy ich `Expanded` właściwości są przełączane. Składniki podrzędne utrzymują swoje Stany zgodnie z oczekiwaniami. Gdy <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> jest wywoływana w obiekcie nadrzędnym, `Expanded` parametr pierwszego składnika podrzędnego jest resetowany z powrotem do jego wartości początkowej ( `true` ). Wartość drugiego `Expander` składnika `Expanded` nie zostanie zresetowana, ponieważ w drugim składniku nie jest renderowana żadna zawartość podrzędna.

Aby zachować stan w poprzednim scenariuszu, użyj *pola prywatnego* w `Expander` składniku, aby zachować stan przełączenia.

Następujący zmieniony `Expander` składnik:

* Akceptuje `Expanded` wartość parametru składnika z elementu nadrzędnego.
* Przypisuje wartość parametru składnika do *pola prywatnego* ( `expanded` ) w [zdarzeniu OnInitialized](xref:blazor/components/lifecycle#component-initialization-methods).
* Używa prywatnego pola do utrzymania stanu wewnętrznego przełączania.

```razor
<div @onclick="@Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>expanded</code> = @expanded)</h2>

        @if (expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    private bool expanded;

    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

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

## <a name="apply-an-attribute"></a>Zastosuj atrybut

Atrybuty mogą być stosowane do Razor składników z [`@attribute`][7] dyrektywą. Poniższy przykład stosuje [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybut do klasy składnika:

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a>Warunkowe atrybuty elementu HTML

Atrybuty elementu HTML są warunkowo renderowane na podstawie wartości .NET. Jeśli wartość jest `false` lub `null` , atrybut nie jest renderowany. Jeśli wartość to `true` , atrybut jest renderowany jako zminimalizowany.

W poniższym przykładzie, `IsCompleted` określa, czy `checked` jest renderowany w znacznikach elementu:

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

Jeśli `IsCompleted` jest `true` , pole wyboru jest renderowane jako:

```html
<input type="checkbox" checked />
```

Jeśli `IsCompleted` jest `false` , pole wyboru jest renderowane jako:

```html
<input type="checkbox" />
```

Aby uzyskać więcej informacji, zobacz <xref:mvc/views/razor>.

> [!WARNING]
> Niektóre atrybuty HTML, takie jak [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons) , nie działają prawidłowo, gdy typem .NET jest `bool` . W takich przypadkach należy użyć `string` typu zamiast `bool` .

## <a name="raw-html"></a>Nieprzetworzony kod HTML

Ciągi są zwykle renderowane przy użyciu węzłów tekstowych DOM, co oznacza, że wszystkie znaczniki, które mogą zawierać, są ignorowane i traktowane jako tekst literału. Aby renderować nieprzetworzony kod HTML, zawiń zawartość HTML w `MarkupString` wartości. Wartość jest analizowana jako plik HTML lub SVG i wstawiona do modelu DOM.

> [!WARNING]
> Renderowanie nieprzetworzonego kodu HTML zbudowanego z dowolnego niezaufanego źródła stanowi **zagrożenie bezpieczeństwa** i należy je unikać!

W poniższym przykładzie pokazano, jak za pomocą `MarkupString` typu dodać blok statycznej zawartości HTML do renderowanego danych wyjściowych składnika:

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="no-locrazor-templates"></a>Razor przystawki

Fragmenty renderowania można definiować przy użyciu Razor składni szablonu. Razor Szablony są sposobem definiowania fragmentu interfejsu użytkownika i przyjmuje następujący format:

```razor
@<{HTML tag}>...</{HTML tag}>
```

Poniższy przykład ilustruje sposób określania <xref:Microsoft.AspNetCore.Components.RenderFragment> i <xref:Microsoft.AspNetCore.Components.RenderFragment%601> wartości oraz renderowania szablonów bezpośrednio w składniku. Fragmenty renderowania mogą być również przekazane jako argumenty do [składników z szablonem](xref:blazor/components/templated-components).

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

## <a name="static-assets"></a>Statyczne zasoby

Blazorzgodnie z Konwencją ASP.NET Core aplikacje umieszczające statyczne zasoby w [ `web root (wwwroot)` folderze](xref:fundamentals/index#web-root)projektu.

Użyj ścieżki względnej (), `/` Aby odwołać się do katalogu głównego sieci Web dla statycznego elementu zawartości. W poniższym przykładzie znajduje się `logo.png` fizycznie w `{PROJECT ROOT}/wwwroot/images` folderze:

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor składniki nie **obsługują** notacji ukośnika odwrotnego ( `~/` ).

Aby uzyskać informacje na temat ustawiania ścieżki podstawowej aplikacji, zobacz <xref:blazor/host-and-deploy/index#app-base-path> .

## <a name="tag-helpers-arent-supported-in-components"></a>Pomocnicy tagów nie są obsługiwani w składnikach

[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) nie są obsługiwane w Razor składnikach ( `.razor` pliki). Aby zapewnić funkcję przypominającą pomocnik tagów w programie Blazor , należy utworzyć składnik o tej samej funkcji co pomocnik tagów i użyć składnika zamiast niego.

## <a name="scalable-vector-graphics-svg-images"></a>Skalowalne obrazy wektorowe (SVG)

Ponieważ Blazor renderuje HTML, obrazy obsługiwane przez przeglądarkę, w tym obrazy Scalable Vector Graphics (SVG) `.svg` , są obsługiwane przez `<img>` tag:

```html
<img alt="Example image" src="some-image.svg" />
```

Podobnie Obrazy SVG są obsługiwane w regułach CSS pliku arkusza stylów ( `.css` ):

```css
.my-element {
    background-image: url("some-image.svg");
}
```

Jednak wbudowane znaczniki SVG nie są obsługiwane we wszystkich scenariuszach. Jeśli umieścisz `<svg>` tag bezpośrednio w pliku składnika ( `.razor` ), podstawowe renderowanie obrazu jest obsługiwane, ale wiele scenariuszy zaawansowanych nie jest jeszcze obsługiwanych. Na przykład `<use>` tagi nie są obecnie przestrzegane i [`@bind`][10] nie mogą być używane z niektórymi tagami SVG. Aby uzyskać więcej informacji, zobacz [Obsługa SVG w Blazor (#18271 dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore/issues/18271).

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:blazor/security/server/threat-mitigation>: Zawiera wskazówki dotyczące tworzenia Blazor Server aplikacji, które muszą będą konkurować o z wyczerpaniem zasobów.

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
