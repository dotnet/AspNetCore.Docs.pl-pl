---
title: BlazorIzolacja ASP.NET Core CSS
author: daveabrock
description: Dowiedz się, jak izolacja CSS pozwala na określanie zakresu CSS do składników, co może uprościć arkusz CSS i uniknąć kolizji z innymi składnikami lub bibliotekami.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/css-isolation
ms.openlocfilehash: 0748f606314963788e6733ca2ae2ca2123d839b3
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99529985"
---
# <a name="aspnet-core-blazor-css-isolation"></a>BlazorIzolacja ASP.NET Core CSS

Autor [Dave Brock](https://twitter.com/daveabrock)

Izolacja CSS upraszcza rozmiary CSS aplikacji, uniemożliwiając zależności od stylów globalnych i pomaga uniknąć konfliktów stylów między składnikami i bibliotekami.

## <a name="enable-css-isolation"></a>Włącz izolację CSS 

Aby zdefiniować Style specyficzne dla składnika, Utwórz `.razor.css` plik pasujący do nazwy `.razor` pliku dla składnika w tym samym folderze. `.razor.css`Plik to *plik CSS z zakresem*. 

W przypadku `Example` składnika w `Example.razor` pliku Utwórz plik obok składnika o nazwie `Example.razor.css` . `Example.razor.css`Plik musi znajdować się w tym samym folderze co `Example` składnik ( `Example.razor` ). W `Example` nazwie podstawowej pliku **nie** jest rozróżniana wielkość liter.

`Pages/Example.razor`:

```razor
@page "/example"

<h1>Scoped CSS Example</h1>
```

`Pages/Example.razor.css`:

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

**Style zdefiniowane w programie `Example.razor.css` są stosowane tylko do renderowanych danych wyjściowych `Example` składnika.** Izolacja CSS jest stosowana do elementów HTML w pasującym Razor pliku. Wszystkie `h1` deklaracje CSS zdefiniowane w innym miejscu w aplikacji nie powodują konfliktu ze `Example` stylami składnika.

> [!NOTE]
> W celu zagwarantowania izolacji stylu podczas tworzenia grupowania nie jest obsługiwane Importowanie stylów CSS w Razor blokach kodu.

## <a name="css-isolation-bundling"></a>Tworzenie izolacji CSS

Izolacja CSS występuje w czasie kompilacji. W trakcie tego procesu program Blazor ponownie zapisuje selektory CSS, aby dopasować znaczniki renderowane przez składnik. Te style CSS są powiązane i tworzone jako statyczny element zawartości w lokalizacji `{PROJECT NAME}.styles.css` , gdzie symbol zastępczy `{PROJECT NAME}` jest przywoływanym pakietem lub nazwą produktu.

Te pliki statyczne są domyślnie przywoływane z ścieżki katalogu głównego aplikacji. W aplikacji odwołuje się do powiązanego pliku, sprawdzając odwołanie wewnątrz `<head>` tagu wygenerowanego kodu HTML:

```html
<link href="ProjectName.styles.css" rel="stylesheet">
```

W ramach powiązanego pliku każdy składnik jest skojarzony z identyfikatorem zakresu. Dla każdego składnika z stylem atrybut HTML jest dołączany w formacie `b-<10-character-string>` . Identyfikator jest unikatowy i różny dla każdej aplikacji. W wyrenderowanym `Counter` składniku Blazor dołącza identyfikator zakresu do `h1` elementu:

```html
<h1 b-3xxtam6d07>
```

`ProjectName.styles.css`Plik używa identyfikatora zakresu do grupowania deklaracji stylu ze składnikiem. Poniższy przykład zawiera styl dla poprzedniego `<h1>` elementu:

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

W czasie kompilacji pakiet projektu jest tworzony przy użyciu konwencji `{STATIC WEB ASSETS BASE PATH}/Project.lib.scp.css` , gdzie symbol zastępczy `{STATIC WEB ASSETS BASE PATH}` jest ścieżką bazową statycznych elementów zawartości sieci Web.

Jeśli są wykorzystywane inne projekty, takie jak pakiety NuGet lub [ Razor biblioteki klas](xref:blazor/components/class-libraries), plik pakietu:

* Odwołuje się do stylów przy użyciu importu CSS.
* Nie jest publikowany jako statyczny zasób sieci Web aplikacji, która używa stylów.

## <a name="child-component-support"></a>Obsługa składników podrzędnych

Domyślnie izolacja CSS dotyczy tylko składnika, który został skojarzony z formatem `{COMPONENT NAME}.razor.css` , gdzie symbol zastępczy `{COMPONENT NAME}` jest zwykle nazwą składnika. Aby zastosować zmiany do składnika podrzędnego, użyj `::deep` Combinator do dowolnego elementu podrzędnego w pliku składnika nadrzędnego `.razor.css` . `::deep`Combinator wybiera elementy, które są *elementami podrzędnymi* wygenerowanego identyfikatora zakresu elementu. 

Poniższy przykład pokazuje składnik nadrzędny o nazwie `Parent` ze składnikiem podrzędnym o nazwie `Child` .

`Pages/Parent.razor`:

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

`Shared/Child.razor`:

```razor
<h1>Child Component</h1>
```

Zaktualizuj `h1` deklarację w `Parent.razor.css` Combinator, `::deep` Aby wyrównać, że `h1` Deklaracja stylu musi być stosowana do składnika nadrzędnego i jego elementów podrzędnych.

`Pages/Parent.razor.css`:

```css
::deep h1 { 
    color: red;
}
```

`h1`Styl ma teraz zastosowanie do `Parent` składników i `Child` bez konieczności tworzenia ODDZIELNEgo pliku CSS z zakresem dla składnika podrzędnego.

`::deep`Combinator działa tylko z elementami podrzędnymi. Poniższe znaczniki stosują `h1` Style do składników zgodnie z oczekiwaniami. Identyfikator zakresu składnika nadrzędnego jest stosowany do `div` elementu, dzięki czemu przeglądarka wie o dziedziczeniu stylów ze składnika nadrzędnego.

`Pages/Parent.razor`:

```razor
<div>
    <h1>Parent</h1>

    <Child />
</div>
```

Jednak wykluczenie `div` elementu powoduje usunięcie relacji elementu podrzędnego. W poniższym przykładzie styl **nie** jest stosowany do składnika podrzędnego.

`Pages/Parent.razor`:

```razor
<h1>Parent</h1>

<Child />
```

## <a name="css-preprocessor-support"></a>Obsługa preprocesora CSS

Preprocesora CSS są przydatne do ulepszania tworzenia arkuszy CSS, wykorzystując funkcje takie jak zmienne, zagnieżdżanie, moduły, domieszki i dziedziczenie. Chociaż izolacja CSS nie obsługuje natywnych preprocesora CSS, takich jak Sass lub less, integrowanie preprocesora CSS jest bezproblemowe, o ile kompilacja preprocesora przejdzie przed Blazor odpisaniem selektorów CSS podczas procesu kompilacji. Za pomocą programu Visual Studio można na przykład skonfigurować istniejącą kompilację preprocesora jako zadanie **kompilacji** w Eksploratorze modułu uruchamiającego zadania programu Visual Studio.

Wiele pakietów NuGet innych firm, takich jak [Delegate. SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), może kompilować pliki Sass/SCSS na początku procesu kompilacji, zanim nastąpi izolacja CSS i żadna dodatkowa konfiguracja nie jest wymagana.

## <a name="css-isolation-configuration"></a>Konfiguracja izolacji CSS

Izolacja CSS została zaprojektowana tak, aby działała na zewnątrz, ale zapewnia konfigurację dla niektórych zaawansowanych scenariuszy, takich jak sytuacje, w których istnieją zależności od istniejących narzędzi lub przepływów pracy.

### <a name="customize-scope-identifier-format"></a>Dostosuj format identyfikatora zakresu

Domyślnie identyfikatory zakresów używają formatu `b-<10-character-string>` . Aby dostosować Format identyfikatora zakresu, zaktualizuj plik projektu do wybranego wzorca:

```xml
<ItemGroup>
  <None Update="Pages/Example.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

W poprzednim przykładzie, CSS Wygenerowano dla `Example.Razor.css` zmiany jego identyfikatora zakresu z `b-<10-character-string>` do `my-custom-scope-identifier` .

Identyfikatory zakresów umożliwiają uzyskanie dziedziczenia z plikami CSS z zakresem. W poniższym przykładzie pliku projektu `BaseComponent.razor.css` plik zawiera typowe style między składnikami. `DerivedComponent.razor.css`Plik dziedziczy te style.

```xml
<ItemGroup>
  <None Update="Pages/BaseComponent.razor.css" CssScope="my-custom-scope-identifier" />
  <None Update="Pages/DerivedComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

Użyj operatora wieloznacznego ( `*` ), aby udostępnić identyfikatory zakresów dla wielu plików:

```xml
<ItemGroup>
  <None Update="Pages/*.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

### <a name="change-base-path-for-static-web-assets"></a>Zmień ścieżkę podstawową dla statycznych zasobów sieci Web

`scoped.styles.css`Plik jest generowany w katalogu głównym aplikacji. W pliku projektu Użyj `StaticWebAssetBasePath` właściwości, aby zmienić domyślną ścieżkę. Poniższy przykład umieszcza `scoped.styles.css` plik i resztę zasobów aplikacji w `_content` ścieżce:

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a>Wyłącz automatyczne tworzenie

Aby zrezygnować z Blazor publikowania i ładowania plików objętych zakresem w czasie wykonywania, użyj `DisableScopedCssBundling` właściwości. W przypadku korzystania z tej właściwości oznacza to, że inne narzędzia i procesy są odpowiedzialne za pobieranie odizolowanych plików CSS z `obj` katalogu i publikowanie ich w czasie wykonywania:

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```

## <a name="razor-class-library-rcl-support"></a>Razor Obsługa biblioteki klas (RCL)

Gdy [ Razor Biblioteka klas (RCL)](xref:razor-pages/ui-class) zapewnia odizolowane style, `<link>` atrybut znacznika `href` wskazuje `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css` , gdzie symbole zastępcze są:

* `{STATIC WEB ASSET BASE PATH}`: Ścieżka podstawowa statycznego elementu zawartości sieci Web.
* `{ASSEMBLY NAME}`: Nazwa zestawu biblioteki klas.

W poniższym przykładzie:

* Ścieżka podstawowa statycznego elementu zawartości sieci Web to `_content/ClassLib` .
* Nazwa zestawu biblioteki klas to `ClassLib` .

`wwwroot/index.html` ( Blazor WebAssembly ) lub `Pages_Host.cshtml` (Blazor Server):

```html
<link href="_content/ClassLib/ClassLib.bundle.scp.css" rel="stylesheet">
```

Aby uzyskać więcej informacji na temat RCLs i bibliotek składników, zobacz:

* <xref:razor-pages/ui-class>
* <xref:blazor/components/class-libraries>.
