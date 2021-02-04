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
# <a name="aspnet-core-blazor-css-isolation"></a><span data-ttu-id="adbd1-103">BlazorIzolacja ASP.NET Core CSS</span><span class="sxs-lookup"><span data-stu-id="adbd1-103">ASP.NET Core Blazor CSS isolation</span></span>

<span data-ttu-id="adbd1-104">Autor [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="adbd1-104">By [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="adbd1-105">Izolacja CSS upraszcza rozmiary CSS aplikacji, uniemożliwiając zależności od stylów globalnych i pomaga uniknąć konfliktów stylów między składnikami i bibliotekami.</span><span class="sxs-lookup"><span data-stu-id="adbd1-105">CSS isolation simplifies an app's CSS footprint by preventing dependencies on global styles and helps to avoid styling conflicts among components and libraries.</span></span>

## <a name="enable-css-isolation"></a><span data-ttu-id="adbd1-106">Włącz izolację CSS</span><span class="sxs-lookup"><span data-stu-id="adbd1-106">Enable CSS isolation</span></span> 

<span data-ttu-id="adbd1-107">Aby zdefiniować Style specyficzne dla składnika, Utwórz `.razor.css` plik pasujący do nazwy `.razor` pliku dla składnika w tym samym folderze.</span><span class="sxs-lookup"><span data-stu-id="adbd1-107">To define component-specific styles, create a `.razor.css` file matching the name of the `.razor` file for the component in the same folder.</span></span> <span data-ttu-id="adbd1-108">`.razor.css`Plik to *plik CSS z zakresem*.</span><span class="sxs-lookup"><span data-stu-id="adbd1-108">The `.razor.css` file is a *scoped CSS file*.</span></span> 

<span data-ttu-id="adbd1-109">W przypadku `Example` składnika w `Example.razor` pliku Utwórz plik obok składnika o nazwie `Example.razor.css` .</span><span class="sxs-lookup"><span data-stu-id="adbd1-109">For an `Example` component in an `Example.razor` file, create a file alongside the component named `Example.razor.css`.</span></span> <span data-ttu-id="adbd1-110">`Example.razor.css`Plik musi znajdować się w tym samym folderze co `Example` składnik ( `Example.razor` ).</span><span class="sxs-lookup"><span data-stu-id="adbd1-110">The `Example.razor.css` file must reside in the same folder as the `Example` component (`Example.razor`).</span></span> <span data-ttu-id="adbd1-111">W `Example` nazwie podstawowej pliku **nie** jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="adbd1-111">The "`Example`" base name of the file is **not** case-sensitive.</span></span>

<span data-ttu-id="adbd1-112">`Pages/Example.razor`:</span><span class="sxs-lookup"><span data-stu-id="adbd1-112">`Pages/Example.razor`:</span></span>

```razor
@page "/example"

<h1>Scoped CSS Example</h1>
```

<span data-ttu-id="adbd1-113">`Pages/Example.razor.css`:</span><span class="sxs-lookup"><span data-stu-id="adbd1-113">`Pages/Example.razor.css`:</span></span>

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

<span data-ttu-id="adbd1-114">**Style zdefiniowane w programie `Example.razor.css` są stosowane tylko do renderowanych danych wyjściowych `Example` składnika.**</span><span class="sxs-lookup"><span data-stu-id="adbd1-114">**The styles defined in `Example.razor.css` are only applied to the rendered output of the `Example` component.**</span></span> <span data-ttu-id="adbd1-115">Izolacja CSS jest stosowana do elementów HTML w pasującym Razor pliku.</span><span class="sxs-lookup"><span data-stu-id="adbd1-115">CSS isolation is applied to HTML elements in the matching Razor file.</span></span> <span data-ttu-id="adbd1-116">Wszystkie `h1` deklaracje CSS zdefiniowane w innym miejscu w aplikacji nie powodują konfliktu ze `Example` stylami składnika.</span><span class="sxs-lookup"><span data-stu-id="adbd1-116">Any `h1` CSS declarations defined elsewhere in the app don't conflict with the `Example` component's styles.</span></span>

> [!NOTE]
> <span data-ttu-id="adbd1-117">W celu zagwarantowania izolacji stylu podczas tworzenia grupowania nie jest obsługiwane Importowanie stylów CSS w Razor blokach kodu.</span><span class="sxs-lookup"><span data-stu-id="adbd1-117">In order to guarantee style isolation when bundling occurs, importing CSS in Razor code blocks isn't supported.</span></span>

## <a name="css-isolation-bundling"></a><span data-ttu-id="adbd1-118">Tworzenie izolacji CSS</span><span class="sxs-lookup"><span data-stu-id="adbd1-118">CSS isolation bundling</span></span>

<span data-ttu-id="adbd1-119">Izolacja CSS występuje w czasie kompilacji.</span><span class="sxs-lookup"><span data-stu-id="adbd1-119">CSS isolation occurs at build time.</span></span> <span data-ttu-id="adbd1-120">W trakcie tego procesu program Blazor ponownie zapisuje selektory CSS, aby dopasować znaczniki renderowane przez składnik.</span><span class="sxs-lookup"><span data-stu-id="adbd1-120">During this process, Blazor rewrites CSS selectors to match markup rendered by the component.</span></span> <span data-ttu-id="adbd1-121">Te style CSS są powiązane i tworzone jako statyczny element zawartości w lokalizacji `{PROJECT NAME}.styles.css` , gdzie symbol zastępczy `{PROJECT NAME}` jest przywoływanym pakietem lub nazwą produktu.</span><span class="sxs-lookup"><span data-stu-id="adbd1-121">These rewritten CSS styles are bundled and produced as a static asset at `{PROJECT NAME}.styles.css`, where the placeholder `{PROJECT NAME}` is the referenced package or product name.</span></span>

<span data-ttu-id="adbd1-122">Te pliki statyczne są domyślnie przywoływane z ścieżki katalogu głównego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="adbd1-122">These static files are referenced from the root path of the app by default.</span></span> <span data-ttu-id="adbd1-123">W aplikacji odwołuje się do powiązanego pliku, sprawdzając odwołanie wewnątrz `<head>` tagu wygenerowanego kodu HTML:</span><span class="sxs-lookup"><span data-stu-id="adbd1-123">In the app, reference the bundled file by inspecting the reference inside the `<head>` tag of the generated HTML:</span></span>

```html
<link href="ProjectName.styles.css" rel="stylesheet">
```

<span data-ttu-id="adbd1-124">W ramach powiązanego pliku każdy składnik jest skojarzony z identyfikatorem zakresu.</span><span class="sxs-lookup"><span data-stu-id="adbd1-124">Within the bundled file, each component is associated with a scope identifier.</span></span> <span data-ttu-id="adbd1-125">Dla każdego składnika z stylem atrybut HTML jest dołączany w formacie `b-<10-character-string>` .</span><span class="sxs-lookup"><span data-stu-id="adbd1-125">For each styled component, an HTML attribute is appended with the format `b-<10-character-string>`.</span></span> <span data-ttu-id="adbd1-126">Identyfikator jest unikatowy i różny dla każdej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="adbd1-126">The identifier is unique and different for each app.</span></span> <span data-ttu-id="adbd1-127">W wyrenderowanym `Counter` składniku Blazor dołącza identyfikator zakresu do `h1` elementu:</span><span class="sxs-lookup"><span data-stu-id="adbd1-127">In the rendered `Counter` component, Blazor appends a scope identifier to the `h1` element:</span></span>

```html
<h1 b-3xxtam6d07>
```

<span data-ttu-id="adbd1-128">`ProjectName.styles.css`Plik używa identyfikatora zakresu do grupowania deklaracji stylu ze składnikiem.</span><span class="sxs-lookup"><span data-stu-id="adbd1-128">The `ProjectName.styles.css` file uses the scope identifier to group a style declaration with its component.</span></span> <span data-ttu-id="adbd1-129">Poniższy przykład zawiera styl dla poprzedniego `<h1>` elementu:</span><span class="sxs-lookup"><span data-stu-id="adbd1-129">The following example provides the style for the preceding `<h1>` element:</span></span>

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

<span data-ttu-id="adbd1-130">W czasie kompilacji pakiet projektu jest tworzony przy użyciu konwencji `{STATIC WEB ASSETS BASE PATH}/Project.lib.scp.css` , gdzie symbol zastępczy `{STATIC WEB ASSETS BASE PATH}` jest ścieżką bazową statycznych elementów zawartości sieci Web.</span><span class="sxs-lookup"><span data-stu-id="adbd1-130">At build time, a project bundle is created with the convention `{STATIC WEB ASSETS BASE PATH}/Project.lib.scp.css`, where the placeholder `{STATIC WEB ASSETS BASE PATH}` is the static web assets base path.</span></span>

<span data-ttu-id="adbd1-131">Jeśli są wykorzystywane inne projekty, takie jak pakiety NuGet lub [ Razor biblioteki klas](xref:blazor/components/class-libraries), plik pakietu:</span><span class="sxs-lookup"><span data-stu-id="adbd1-131">If other projects are utilized, such as NuGet packages or [Razor class libraries](xref:blazor/components/class-libraries), the bundled file:</span></span>

* <span data-ttu-id="adbd1-132">Odwołuje się do stylów przy użyciu importu CSS.</span><span class="sxs-lookup"><span data-stu-id="adbd1-132">References the styles using CSS imports.</span></span>
* <span data-ttu-id="adbd1-133">Nie jest publikowany jako statyczny zasób sieci Web aplikacji, która używa stylów.</span><span class="sxs-lookup"><span data-stu-id="adbd1-133">Isn't published as a static web asset of the app that consumes the styles.</span></span>

## <a name="child-component-support"></a><span data-ttu-id="adbd1-134">Obsługa składników podrzędnych</span><span class="sxs-lookup"><span data-stu-id="adbd1-134">Child component support</span></span>

<span data-ttu-id="adbd1-135">Domyślnie izolacja CSS dotyczy tylko składnika, który został skojarzony z formatem `{COMPONENT NAME}.razor.css` , gdzie symbol zastępczy `{COMPONENT NAME}` jest zwykle nazwą składnika.</span><span class="sxs-lookup"><span data-stu-id="adbd1-135">By default, CSS isolation only applies to the component you associate with the format `{COMPONENT NAME}.razor.css`, where the placeholder `{COMPONENT NAME}` is usually the component name.</span></span> <span data-ttu-id="adbd1-136">Aby zastosować zmiany do składnika podrzędnego, użyj `::deep` Combinator do dowolnego elementu podrzędnego w pliku składnika nadrzędnego `.razor.css` .</span><span class="sxs-lookup"><span data-stu-id="adbd1-136">To apply changes to a child component, use the `::deep` combinator to any descendant elements in the parent component's `.razor.css` file.</span></span> <span data-ttu-id="adbd1-137">`::deep`Combinator wybiera elementy, które są *elementami podrzędnymi* wygenerowanego identyfikatora zakresu elementu.</span><span class="sxs-lookup"><span data-stu-id="adbd1-137">The `::deep` combinator selects elements that are *descendants* of an element's generated scope identifier.</span></span> 

<span data-ttu-id="adbd1-138">Poniższy przykład pokazuje składnik nadrzędny o nazwie `Parent` ze składnikiem podrzędnym o nazwie `Child` .</span><span class="sxs-lookup"><span data-stu-id="adbd1-138">The following example shows a parent component called `Parent` with a child component called `Child`.</span></span>

<span data-ttu-id="adbd1-139">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="adbd1-139">`Pages/Parent.razor`:</span></span>

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

<span data-ttu-id="adbd1-140">`Shared/Child.razor`:</span><span class="sxs-lookup"><span data-stu-id="adbd1-140">`Shared/Child.razor`:</span></span>

```razor
<h1>Child Component</h1>
```

<span data-ttu-id="adbd1-141">Zaktualizuj `h1` deklarację w `Parent.razor.css` Combinator, `::deep` Aby wyrównać, że `h1` Deklaracja stylu musi być stosowana do składnika nadrzędnego i jego elementów podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="adbd1-141">Update the `h1` declaration in `Parent.razor.css` with the `::deep` combinator to signify the `h1` style declaration must apply to the parent component and its children.</span></span>

<span data-ttu-id="adbd1-142">`Pages/Parent.razor.css`:</span><span class="sxs-lookup"><span data-stu-id="adbd1-142">`Pages/Parent.razor.css`:</span></span>

```css
::deep h1 { 
    color: red;
}
```

<span data-ttu-id="adbd1-143">`h1`Styl ma teraz zastosowanie do `Parent` składników i `Child` bez konieczności tworzenia ODDZIELNEgo pliku CSS z zakresem dla składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="adbd1-143">The `h1` style now applies to the `Parent` and `Child` components without the need to create a separate scoped CSS file for the child component.</span></span>

<span data-ttu-id="adbd1-144">`::deep`Combinator działa tylko z elementami podrzędnymi.</span><span class="sxs-lookup"><span data-stu-id="adbd1-144">The `::deep` combinator only works with descendant elements.</span></span> <span data-ttu-id="adbd1-145">Poniższe znaczniki stosują `h1` Style do składników zgodnie z oczekiwaniami.</span><span class="sxs-lookup"><span data-stu-id="adbd1-145">The following markup applies the `h1` styles to components as expected.</span></span> <span data-ttu-id="adbd1-146">Identyfikator zakresu składnika nadrzędnego jest stosowany do `div` elementu, dzięki czemu przeglądarka wie o dziedziczeniu stylów ze składnika nadrzędnego.</span><span class="sxs-lookup"><span data-stu-id="adbd1-146">The parent component's scope identifier is applied to the `div` element, so the browser knows to inherit styles from the parent component.</span></span>

<span data-ttu-id="adbd1-147">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="adbd1-147">`Pages/Parent.razor`:</span></span>

```razor
<div>
    <h1>Parent</h1>

    <Child />
</div>
```

<span data-ttu-id="adbd1-148">Jednak wykluczenie `div` elementu powoduje usunięcie relacji elementu podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="adbd1-148">However, excluding the `div` element removes the descendant relationship.</span></span> <span data-ttu-id="adbd1-149">W poniższym przykładzie styl **nie** jest stosowany do składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="adbd1-149">In the following example, the style is **not** applied to the child component.</span></span>

<span data-ttu-id="adbd1-150">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="adbd1-150">`Pages/Parent.razor`:</span></span>

```razor
<h1>Parent</h1>

<Child />
```

## <a name="css-preprocessor-support"></a><span data-ttu-id="adbd1-151">Obsługa preprocesora CSS</span><span class="sxs-lookup"><span data-stu-id="adbd1-151">CSS preprocessor support</span></span>

<span data-ttu-id="adbd1-152">Preprocesora CSS są przydatne do ulepszania tworzenia arkuszy CSS, wykorzystując funkcje takie jak zmienne, zagnieżdżanie, moduły, domieszki i dziedziczenie.</span><span class="sxs-lookup"><span data-stu-id="adbd1-152">CSS preprocessors are useful for improving CSS development by utilizing features such as variables, nesting, modules, mixins, and inheritance.</span></span> <span data-ttu-id="adbd1-153">Chociaż izolacja CSS nie obsługuje natywnych preprocesora CSS, takich jak Sass lub less, integrowanie preprocesora CSS jest bezproblemowe, o ile kompilacja preprocesora przejdzie przed Blazor odpisaniem selektorów CSS podczas procesu kompilacji.</span><span class="sxs-lookup"><span data-stu-id="adbd1-153">While CSS isolation doesn't natively support CSS preprocessors such as Sass or Less, integrating CSS preprocessors is seamless as long as preprocessor compilation occurs before Blazor rewrites the CSS selectors during the build process.</span></span> <span data-ttu-id="adbd1-154">Za pomocą programu Visual Studio można na przykład skonfigurować istniejącą kompilację preprocesora jako zadanie **kompilacji** w Eksploratorze modułu uruchamiającego zadania programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="adbd1-154">Using Visual Studio for example, configure existing preprocessor compilation as a **Before Build** task in the Visual Studio Task Runner Explorer.</span></span>

<span data-ttu-id="adbd1-155">Wiele pakietów NuGet innych firm, takich jak [Delegate. SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), może kompilować pliki Sass/SCSS na początku procesu kompilacji, zanim nastąpi izolacja CSS i żadna dodatkowa konfiguracja nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="adbd1-155">Many third-party NuGet packages, such as [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), can compile SASS/SCSS files at the beginning of the build process before CSS isolation occurs, and no additional configuration is required.</span></span>

## <a name="css-isolation-configuration"></a><span data-ttu-id="adbd1-156">Konfiguracja izolacji CSS</span><span class="sxs-lookup"><span data-stu-id="adbd1-156">CSS isolation configuration</span></span>

<span data-ttu-id="adbd1-157">Izolacja CSS została zaprojektowana tak, aby działała na zewnątrz, ale zapewnia konfigurację dla niektórych zaawansowanych scenariuszy, takich jak sytuacje, w których istnieją zależności od istniejących narzędzi lub przepływów pracy.</span><span class="sxs-lookup"><span data-stu-id="adbd1-157">CSS isolation is designed to work out-of-the-box but provides configuration for some advanced scenarios, such as when there are dependencies on existing tools or workflows.</span></span>

### <a name="customize-scope-identifier-format"></a><span data-ttu-id="adbd1-158">Dostosuj format identyfikatora zakresu</span><span class="sxs-lookup"><span data-stu-id="adbd1-158">Customize scope identifier format</span></span>

<span data-ttu-id="adbd1-159">Domyślnie identyfikatory zakresów używają formatu `b-<10-character-string>` .</span><span class="sxs-lookup"><span data-stu-id="adbd1-159">By default, scope identifiers use the format `b-<10-character-string>`.</span></span> <span data-ttu-id="adbd1-160">Aby dostosować Format identyfikatora zakresu, zaktualizuj plik projektu do wybranego wzorca:</span><span class="sxs-lookup"><span data-stu-id="adbd1-160">To customize the scope identifier format, update the project file to a desired pattern:</span></span>

```xml
<ItemGroup>
  <None Update="Pages/Example.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="adbd1-161">W poprzednim przykładzie, CSS Wygenerowano dla `Example.Razor.css` zmiany jego identyfikatora zakresu z `b-<10-character-string>` do `my-custom-scope-identifier` .</span><span class="sxs-lookup"><span data-stu-id="adbd1-161">In the preceding example, the CSS generated for `Example.Razor.css` changes its scope identifier from `b-<10-character-string>` to `my-custom-scope-identifier`.</span></span>

<span data-ttu-id="adbd1-162">Identyfikatory zakresów umożliwiają uzyskanie dziedziczenia z plikami CSS z zakresem.</span><span class="sxs-lookup"><span data-stu-id="adbd1-162">Use scope identifiers to achieve inheritance with scoped CSS files.</span></span> <span data-ttu-id="adbd1-163">W poniższym przykładzie pliku projektu `BaseComponent.razor.css` plik zawiera typowe style między składnikami.</span><span class="sxs-lookup"><span data-stu-id="adbd1-163">In the following project file example, a `BaseComponent.razor.css` file contains common styles across components.</span></span> <span data-ttu-id="adbd1-164">`DerivedComponent.razor.css`Plik dziedziczy te style.</span><span class="sxs-lookup"><span data-stu-id="adbd1-164">A `DerivedComponent.razor.css` file inherits these styles.</span></span>

```xml
<ItemGroup>
  <None Update="Pages/BaseComponent.razor.css" CssScope="my-custom-scope-identifier" />
  <None Update="Pages/DerivedComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="adbd1-165">Użyj operatora wieloznacznego ( `*` ), aby udostępnić identyfikatory zakresów dla wielu plików:</span><span class="sxs-lookup"><span data-stu-id="adbd1-165">Use the wildcard (`*`) operator to share scope identifiers across multiple files:</span></span>

```xml
<ItemGroup>
  <None Update="Pages/*.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

### <a name="change-base-path-for-static-web-assets"></a><span data-ttu-id="adbd1-166">Zmień ścieżkę podstawową dla statycznych zasobów sieci Web</span><span class="sxs-lookup"><span data-stu-id="adbd1-166">Change base path for static web assets</span></span>

<span data-ttu-id="adbd1-167">`scoped.styles.css`Plik jest generowany w katalogu głównym aplikacji.</span><span class="sxs-lookup"><span data-stu-id="adbd1-167">The `scoped.styles.css` file is generated at the root of the app.</span></span> <span data-ttu-id="adbd1-168">W pliku projektu Użyj `StaticWebAssetBasePath` właściwości, aby zmienić domyślną ścieżkę.</span><span class="sxs-lookup"><span data-stu-id="adbd1-168">In the project file, use the `StaticWebAssetBasePath` property to change the default path.</span></span> <span data-ttu-id="adbd1-169">Poniższy przykład umieszcza `scoped.styles.css` plik i resztę zasobów aplikacji w `_content` ścieżce:</span><span class="sxs-lookup"><span data-stu-id="adbd1-169">The following example places the `scoped.styles.css` file, and the rest of the app's assets, at the `_content` path:</span></span>

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a><span data-ttu-id="adbd1-170">Wyłącz automatyczne tworzenie</span><span class="sxs-lookup"><span data-stu-id="adbd1-170">Disable automatic bundling</span></span>

<span data-ttu-id="adbd1-171">Aby zrezygnować z Blazor publikowania i ładowania plików objętych zakresem w czasie wykonywania, użyj `DisableScopedCssBundling` właściwości.</span><span class="sxs-lookup"><span data-stu-id="adbd1-171">To opt out of how Blazor publishes and loads scoped files at runtime, use the `DisableScopedCssBundling` property.</span></span> <span data-ttu-id="adbd1-172">W przypadku korzystania z tej właściwości oznacza to, że inne narzędzia i procesy są odpowiedzialne za pobieranie odizolowanych plików CSS z `obj` katalogu i publikowanie ich w czasie wykonywania:</span><span class="sxs-lookup"><span data-stu-id="adbd1-172">When using this property, it means other tools or processes are responsible for taking the isolated CSS files from the `obj` directory and publishing and loading them at runtime:</span></span>

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```

## <a name="razor-class-library-rcl-support"></a><span data-ttu-id="adbd1-173">Razor Obsługa biblioteki klas (RCL)</span><span class="sxs-lookup"><span data-stu-id="adbd1-173">Razor class library (RCL) support</span></span>

<span data-ttu-id="adbd1-174">Gdy [ Razor Biblioteka klas (RCL)](xref:razor-pages/ui-class) zapewnia odizolowane style, `<link>` atrybut znacznika `href` wskazuje `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css` , gdzie symbole zastępcze są:</span><span class="sxs-lookup"><span data-stu-id="adbd1-174">When a [Razor class library (RCL)](xref:razor-pages/ui-class) provides isolated styles, the `<link>` tag's `href` attribute points to `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`, where the placeholders are:</span></span>

* <span data-ttu-id="adbd1-175">`{STATIC WEB ASSET BASE PATH}`: Ścieżka podstawowa statycznego elementu zawartości sieci Web.</span><span class="sxs-lookup"><span data-stu-id="adbd1-175">`{STATIC WEB ASSET BASE PATH}`: The static web asset base path.</span></span>
* <span data-ttu-id="adbd1-176">`{ASSEMBLY NAME}`: Nazwa zestawu biblioteki klas.</span><span class="sxs-lookup"><span data-stu-id="adbd1-176">`{ASSEMBLY NAME}`: The class library's assembly name.</span></span>

<span data-ttu-id="adbd1-177">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="adbd1-177">In the following example:</span></span>

* <span data-ttu-id="adbd1-178">Ścieżka podstawowa statycznego elementu zawartości sieci Web to `_content/ClassLib` .</span><span class="sxs-lookup"><span data-stu-id="adbd1-178">The static web asset base path is `_content/ClassLib`.</span></span>
* <span data-ttu-id="adbd1-179">Nazwa zestawu biblioteki klas to `ClassLib` .</span><span class="sxs-lookup"><span data-stu-id="adbd1-179">The class library's assembly name is `ClassLib`.</span></span>

<span data-ttu-id="adbd1-180">`wwwroot/index.html` ( Blazor WebAssembly ) lub `Pages_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="adbd1-180">`wwwroot/index.html` (Blazor WebAssembly) or `Pages_Host.cshtml` (Blazor Server):</span></span>

```html
<link href="_content/ClassLib/ClassLib.bundle.scp.css" rel="stylesheet">
```

<span data-ttu-id="adbd1-181">Aby uzyskać więcej informacji na temat RCLs i bibliotek składników, zobacz:</span><span class="sxs-lookup"><span data-stu-id="adbd1-181">For more information on RCLs and component libraries, see:</span></span>

* <xref:razor-pages/ui-class>
* <span data-ttu-id="adbd1-182"><xref:blazor/components/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="adbd1-182"><xref:blazor/components/class-libraries>.</span></span>
