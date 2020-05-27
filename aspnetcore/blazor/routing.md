---
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---
# <a name="aspnet-core-blazor-routing"></a>BlazorRouting ASP.NET Core

Autor [Luke Latham](https://github.com/guardrex)

Dowiedz się, jak kierować żądania i jak używać <xref:Microsoft.AspNetCore.Components.Routing.NavLink> składnika do tworzenia linków nawigacji w Blazor aplikacjach.

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET Core integracja z routingiem punktu końcowego

BlazorSerwer jest zintegrowany z [ASP.NET Core routingiem punktów końcowych](xref:fundamentals/routing). Aplikacja ASP.NET Core jest skonfigurowana do akceptowania połączeń przychodzących dla składników interaktywnych za pomocą <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> programu w programie `Startup.Configure` :

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

Najbardziej typową konfiguracją jest kierowanie wszystkich żądań do Razor strony, które pełnią rolę hosta dla części aplikacji serwerowej po stronie serwera Blazor . Zgodnie z Konwencją strona *hosta* ma zwykle nazwę *_Host. cshtml*. Trasa określona w pliku hosta jest nazywana *trasą rezerwową* , ponieważ działa z niskim priorytetem w dopasowaniu tras. Trasa rezerwowa jest brana pod uwagę, gdy inne trasy nie są zgodne. Dzięki temu aplikacja może korzystać z innych kontrolerów i stron bez zakłócania działania Blazor aplikacji serwera.

## <a name="route-templates"></a>Szablony tras

<xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik umożliwia kierowanie do każdego składnika z określoną trasą. <xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik pojawi się w pliku *App. Razor* :

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

Gdy plik *Razor* z `@page` dyrektywą jest kompilowany, wygenerowana Klasa jest dostarczana z <xref:Microsoft.AspNetCore.Components.RouteAttribute> określeniem szablonu trasy.

W środowisku uruchomieniowym <xref:Microsoft.AspNetCore.Components.RouteView> składnik:

* Odbiera <xref:Microsoft.AspNetCore.Components.RouteData> od siebie <xref:Microsoft.AspNetCore.Components.Routing.Router> wraz z dowolnymi żądanymi parametrami.
* Renderuje określony składnik za pomocą układu (lub opcjonalnego układu domyślnego) przy użyciu określonych parametrów.

Opcjonalnie można określić <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parametr z klasą układu, która ma być używana dla składników, które nie określają układu. Domyślne Blazor Szablony określają `MainLayout` składnik. *MainLayout. Razor* znajduje się w folderze *udostępnionym* projektu szablonu. Aby uzyskać więcej informacji na temat układów, zobacz <xref:blazor/layouts> .

Do składnika można zastosować wiele szablonów tras. Poniższy składnik odpowiada na żądania dla `/BlazorRoute` i `/DifferentBlazorRoute` :

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> Aby można było poprawnie rozwiązać adresy URL, aplikacja musi zawierać `<base>` tag w pliku *wwwroot/index.html* ( Blazor Webassembly) lub *Pages/_Host. cshtml* ( Blazor serwer) z ścieżką bazową aplikacji określoną w `href` atrybucie ( `<base href="/">` ). Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Podaj zawartość niestandardową, jeśli nie można odnaleźć zawartości

<xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik umożliwia aplikacji określenie zawartości niestandardowej, jeśli nie można odnaleźć zawartości dla żądanej trasy.

W pliku *App. Razor* Ustaw zawartość niestandardową w <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> parametrze szablonu <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika:

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

Zawartość `<NotFound>` tagów może zawierać dowolne elementy, takie jak inne składniki interaktywne. Aby zastosować domyślny układ do <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> zawartości, zobacz <xref:blazor/layouts> .

## <a name="route-to-components-from-multiple-assemblies"></a>Kierowanie do składników z wielu zestawów

Użyj <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parametru, aby określić dodatkowe zestawy dla <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika do uwzględnienia podczas wyszukiwania składników routingu. Określone zestawy są traktowane jako uzupełnienie `AppAssembly` określonego zestawu. W poniższym przykładzie `Component1` jest składnikiem rutowanym zdefiniowanym w bibliotece klas, do której się odwołuje. W poniższym <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> przykładzie przedstawiono obsługę routingu dla `Component1` :

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>Parametry trasy

Router używa parametrów trasy do wypełniania odpowiednich parametrów składnika o tej samej nazwie (bez uwzględniania wielkości liter):

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

Parametry opcjonalne nie są obsługiwane. `@page`W poprzednim przykładzie są stosowane dwie dyrektywy. Pierwszy zezwala na nawigowanie do składnika bez parametru. Druga `@page` dyrektywa przyjmuje `{text}` parametr Route i przypisuje wartość do `Text` właściwości.

## <a name="route-constraints"></a>Ograniczenia trasy

Ograniczenie trasy wymusza dopasowanie typu w segmencie trasy do składnika.

W poniższym przykładzie trasa do `Users` składnika dopasowuje się tylko wtedy, gdy:

* `Id`Segment trasy jest obecny w adresie URL żądania.
* `Id`Segment jest liczbą całkowitą ( `int` ).

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

Dostępne są ograniczenia trasy podane w poniższej tabeli. W przypadku ograniczeń trasy, które pasują do niezmiennej kultury, zobacz ostrzeżenie poniżej tabeli, aby uzyskać więcej informacji.

| Typu | Przykład           | Przykładowe dopasowania                                                                  | Niezmiennej<br>kultura<br>parowanie |
| ---
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

----- | ---title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

--------- | ---title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---------------------------------------- | :---title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Nie | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Tak | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Tak | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Tak | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Tak | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Nie | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Tak | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Tak |

> [!WARNING]
> Ograniczenia trasy, które weryfikują adres URL i są konwertowane na typ CLR (takie jak `int` lub <xref:System.DateTime> ), zawsze używają niezmiennej kultury. W tych ograniczeniach przyjęto założenie, że adres URL nie jest Lokalizowalny.

### <a name="routing-with-urls-that-contain-dots"></a>Routing z adresami URL zawierającymi kropki

W obszarze Blazor aplikacje serwera domyślna trasa w *_Host. cshtml* to `/` ( `@page "/"` ). Adres URL żądania, który zawiera kropkę ( `.` ) nie pasuje do trasy domyślnej, ponieważ adres URL wygląda na żądanie pliku. BlazorAplikacja zwraca *404 — nie odnaleziono* odpowiedzi dla pliku statycznego, który nie istnieje. Aby użyć tras zawierających kropkę, skonfiguruj *_Host. cshtml* przy użyciu następującego szablonu trasy:

```cshtml
@page "/{**path}"
```

`"/{**path}"`Szablon zawiera:

* Podwójna gwiazdka *catch-all* ( `**` ) do przechwytywania ścieżki między wieloma granicami folderów bez kodowania ukośników ( `/` ).
* `path`Nazwa parametru trasy.

> [!NOTE]
> Składnia *catch-all* parametru ( `*` / `**` ) **nie** jest obsługiwana w Razor składnikach (*. Razor*).

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing>.

## <a name="navlink-component"></a>Składnik NavLink

<xref:Microsoft.AspNetCore.Components.Routing.NavLink>Podczas tworzenia linków nawigacji Użyj składnika zamiast elementów hiperlinków HTML ( `<a>` ). <xref:Microsoft.AspNetCore.Components.Routing.NavLink>Składnik zachowuje się jak `<a>` element, z wyjątkiem przełączenia `active` klasy CSS w zależności od tego, czy jest on `href` zgodny z bieżącym adresem URL. `active`Klasa pomaga użytkownikowi zrozumieć, która strona jest aktywną stroną między wyświetlanymi łączami nawigacji.

Poniższy `NavMenu` składnik tworzy pasek nawigacyjny [Bootstrap](https://getbootstrap.com/docs/) , który pokazuje, jak używać <xref:Microsoft.AspNetCore.Components.Routing.NavLink> składników:

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Dostępne są dwie <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> Opcje, które można przypisać do `Match` atrybutu `<NavLink>` elementu:

* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>&ndash; <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Jest aktywny, gdy jest zgodny z całym bieżącym adresem URL.
* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>(*ustawienie domyślne*) &ndash; <xref:Microsoft.AspNetCore.Components.Routing.NavLink>Jest aktywny, gdy pasuje do dowolnego prefiksu bieżącego adresu URL.

W poprzednim przykładzie Strona główna <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` odpowiada GŁÓWNEmu adresowi URL i odbiera tylko `active` klasy CSS przy domyślnym adresie URL ścieżki podstawowej aplikacji (na przykład `https://localhost:5001/` ). Druga <xref:Microsoft.AspNetCore.Components.Routing.NavLink> otrzymuje klasę, `active` gdy użytkownik odwiedzi dowolny adres URL z `MyComponent` prefiksem (na przykład `https://localhost:5001/MyComponent` i `https://localhost:5001/MyComponent/AnotherSegment` ).

Dodatkowe <xref:Microsoft.AspNetCore.Components.Routing.NavLink> atrybuty składników są przenoszone do renderowanego tagu zakotwiczenia. W poniższym przykładzie <xref:Microsoft.AspNetCore.Components.Routing.NavLink> składnik zawiera `target` atrybut:

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

Renderuje następujący znacznik HTML:

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>Pomoc dotycząca stanu identyfikatora URI i nawigacji

Służy <xref:Microsoft.AspNetCore.Components.NavigationManager> do pracy z identyfikatorami URI i nawigacją w kodzie C#. <xref:Microsoft.AspNetCore.Components.NavigationManager>zawiera zdarzenie i metody przedstawione w poniższej tabeli.

| Członek | Opis |
| ---
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

--- | ---title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor routing" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

------ | | <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Pobiera bieżący bezwzględny identyfikator URI. | | <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Pobiera podstawowy identyfikator URI (z końcowym ukośnikiem), który można dołączać do względnych ścieżek URI w celu utworzenia bezwzględnego identyfikatora URI. Zazwyczaj <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> odpowiada `href` atrybutowi `<base>` elementu dokumentu w *wwwroot/index.html* ( Blazor webassembly) lub *Pages/_Host. cshtml* ( Blazor serwer). | | <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Przechodzi do określonego identyfikatora URI. Jeśli `forceLoad` jest `true` :<ul><li>Routing po stronie klienta jest pomijany.</li><li>W przeglądarce wymuszone jest załadowanie nowej strony z serwera, niezależnie od tego, czy identyfikator URI jest zwykle obsługiwany przez router po stronie klienta.</li></ul> | | <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Zdarzenie, które jest wyzwalane po zmianie lokalizacji nawigacji. | | <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Konwertuje względny identyfikator URI na bezwzględny identyfikator URI. | | <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Przy użyciu podstawowego identyfikatora URI (na przykład identyfikatora URI zwróconego wcześniej przez <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), program konwertuje bezwzględny identyfikator URI na identyfikator URI względem podstawowego prefiksu URI. |

Poniższy składnik przechodzi do `Counter` składnika aplikacji po wybraniu przycisku:

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

Poniższy składnik obsługuje zdarzenie zmiany lokalizacji przez ustawienie <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> . Metoda jest odłączana, `HandleLocationChanged` gdy `Dispose` jest wywoływana przez platformę. Odłączanie metody zezwala na wyrzucanie elementów bezużytecznych składnika.

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>zawiera następujące informacje o zdarzeniu:

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash;Adres URL nowej lokalizacji.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash;Jeśli `true` Blazor przechwycono nawigację z przeglądarki. Jeśli `false` <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> wystąpiła nawigacja.

Aby uzyskać więcej informacji na temat usuwania składników, zobacz <xref:blazor/lifecycle#component-disposal-with-idisposable> .
