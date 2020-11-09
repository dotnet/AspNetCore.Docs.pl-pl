---
title: BlazorIzolacja ASP.NET Core CSS
author: daveabrock
description: Dowiedz się, jak izolacja CSS pozwala na określanie zakresu CSS do składników, co może uprościć arkusz CSS i uniknąć kolizji z innymi składnikami lub bibliotekami.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/components/css-isolation
ms.openlocfilehash: 628e7dc897912beaae0df792b82958517ac70ca4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056325"
---
# <a name="aspnet-core-no-locblazor-css-isolation"></a><span data-ttu-id="14a6b-103">BlazorIzolacja ASP.NET Core CSS</span><span class="sxs-lookup"><span data-stu-id="14a6b-103">ASP.NET Core Blazor CSS isolation</span></span>

<span data-ttu-id="14a6b-104">Autor [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="14a6b-104">By [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="14a6b-105">Izolacja CSS upraszcza rozmiary CSS aplikacji, uniemożliwiając zależności od stylów globalnych i pomaga uniknąć konfliktów stylów między składnikami i bibliotekami.</span><span class="sxs-lookup"><span data-stu-id="14a6b-105">CSS isolation simplifies an app's CSS footprint by preventing dependencies on global styles and helps to avoid styling conflicts among components and libraries.</span></span>

## <a name="enable-css-isolation"></a><span data-ttu-id="14a6b-106">Włącz izolację CSS</span><span class="sxs-lookup"><span data-stu-id="14a6b-106">Enable CSS isolation</span></span> 

<span data-ttu-id="14a6b-107">Aby zdefiniować Style specyficzne dla składnika, należy utworzyć `razor.css` plik pasujący do nazwy `.razor` pliku składnika.</span><span class="sxs-lookup"><span data-stu-id="14a6b-107">To define component-specific styles, create a `razor.css` file matching the name of the `.razor` file for the component.</span></span> <span data-ttu-id="14a6b-108">Ten `razor.css` plik jest *plikiem CSS z zakresem* .</span><span class="sxs-lookup"><span data-stu-id="14a6b-108">This `razor.css` file is a *scoped CSS file* .</span></span> 

<span data-ttu-id="14a6b-109">Dla `MyComponent` składnika, który ma `MyComponent.razor` plik, Utwórz plik obok składnika o nazwie `MyComponent.razor.css` .</span><span class="sxs-lookup"><span data-stu-id="14a6b-109">For a `MyComponent` component that has a `MyComponent.razor` file, create a file alongside the component called `MyComponent.razor.css`.</span></span> <span data-ttu-id="14a6b-110">`MyComponent`W `razor.css` nazwie pliku **nie** jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="14a6b-110">The `MyComponent` value in the `razor.css` filename is **not** case-sensitive.</span></span>

<span data-ttu-id="14a6b-111">Aby na przykład dodać izolację CSS do `Counter` składnika w domyślnym Blazor szablonie projektu, Dodaj nowy plik o nazwie `Counter.razor.css` obok `Counter.razor` pliku, a następnie Dodaj następujący kod CSS:</span><span class="sxs-lookup"><span data-stu-id="14a6b-111">For example to add CSS isolation to the `Counter` component in the default Blazor project template, add a new file named `Counter.razor.css` alongside the `Counter.razor` file, then add the following CSS:</span></span>

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

<span data-ttu-id="14a6b-112">Style zdefiniowane w programie `Counter.razor.css` są stosowane tylko do renderowanych danych wyjściowych `Counter` składnika.</span><span class="sxs-lookup"><span data-stu-id="14a6b-112">The styles defined in `Counter.razor.css` are only applied to the rendered output of the `Counter` component.</span></span> <span data-ttu-id="14a6b-113">Wszystkie `h1` deklaracje CSS zdefiniowane w innym miejscu w aplikacji nie powodują konfliktu ze `Counter` stylami.</span><span class="sxs-lookup"><span data-stu-id="14a6b-113">Any `h1` CSS declarations defined elsewhere in the app don't conflict with `Counter` styles.</span></span>

> [!NOTE]
> <span data-ttu-id="14a6b-114">W celu zagwarantowania izolacji stylu podczas tworzenia grupowania `@import` Razor bloki nie są obsługiwane w przypadku plików CSS z zakresem.</span><span class="sxs-lookup"><span data-stu-id="14a6b-114">In order to guarantee style isolation when bundling occurs, `@import` Razor blocks aren't supported with scoped CSS files.</span></span>

## <a name="css-isolation-bundling"></a><span data-ttu-id="14a6b-115">Tworzenie izolacji CSS</span><span class="sxs-lookup"><span data-stu-id="14a6b-115">CSS isolation bundling</span></span>

<span data-ttu-id="14a6b-116">Izolacja CSS występuje w czasie kompilacji.</span><span class="sxs-lookup"><span data-stu-id="14a6b-116">CSS isolation occurs at build time.</span></span> <span data-ttu-id="14a6b-117">W trakcie tego procesu program Blazor ponownie zapisuje selektory CSS, aby dopasować znaczniki renderowane przez składnik.</span><span class="sxs-lookup"><span data-stu-id="14a6b-117">During this process, Blazor rewrites CSS selectors to match markup rendered by the component.</span></span> <span data-ttu-id="14a6b-118">Te style CSS są powiązane i tworzone jako statyczny element zawartości w lokalizacji `{PROJECT NAME}.styles.css` , gdzie symbol zastępczy `{PROJECT NAME}` jest przywoływanym pakietem lub nazwą produktu.</span><span class="sxs-lookup"><span data-stu-id="14a6b-118">These rewritten CSS styles are bundled and produced as a static asset at `{PROJECT NAME}.styles.css`, where the placeholder `{PROJECT NAME}` is the referenced package or product name.</span></span>

<span data-ttu-id="14a6b-119">Te pliki statyczne są domyślnie przywoływane z ścieżki katalogu głównego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14a6b-119">These static files are referenced from the root path of the app by default.</span></span> <span data-ttu-id="14a6b-120">W aplikacji odwołuje się do powiązanego pliku, sprawdzając odwołanie wewnątrz `<head>` tagu wygenerowanego kodu HTML:</span><span class="sxs-lookup"><span data-stu-id="14a6b-120">In the app, reference the bundled file by inspecting the reference inside the `<head>` tag of the generated HTML:</span></span>

```html
<link href="MyProjectName.styles.css" rel="stylesheet">
```

<span data-ttu-id="14a6b-121">W ramach powiązanego pliku każdy składnik jest skojarzony z identyfikatorem zakresu.</span><span class="sxs-lookup"><span data-stu-id="14a6b-121">Within the bundled file, each component is associated with a scope identifier.</span></span> <span data-ttu-id="14a6b-122">Dla każdego składnika z stylem atrybut HTML jest dołączany w formacie `b-<10-character-string>` .</span><span class="sxs-lookup"><span data-stu-id="14a6b-122">For each styled component, an HTML attribute is appended with the format `b-<10-character-string>`.</span></span> <span data-ttu-id="14a6b-123">Identyfikator jest unikatowy i różny dla każdej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14a6b-123">The identifier is unique and different for each app.</span></span> <span data-ttu-id="14a6b-124">W wyrenderowanym `Counter` składniku Blazor dołącza identyfikator zakresu do `h1` elementu:</span><span class="sxs-lookup"><span data-stu-id="14a6b-124">In the rendered `Counter` component, Blazor appends a scope identifier to the `h1` element:</span></span>

```html
<h1 b-3xxtam6d07>
```

<span data-ttu-id="14a6b-125">`MyProjectName.styles.css`Plik używa identyfikatora zakresu do grupowania deklaracji stylu ze składnikiem.</span><span class="sxs-lookup"><span data-stu-id="14a6b-125">The `MyProjectName.styles.css` file uses the scope identifier to group a style declaration with its component.</span></span> <span data-ttu-id="14a6b-126">Poniższy przykład zawiera styl dla poprzedniego `<h1>` elementu:</span><span class="sxs-lookup"><span data-stu-id="14a6b-126">The following example provides the style for the preceding `<h1>` element:</span></span>

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

<span data-ttu-id="14a6b-127">W czasie kompilacji pakiet projektu jest tworzony przy użyciu konwencji `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css` , gdzie symbol zastępczy `{STATIC WEB ASSETS BASE PATH}` jest ścieżką bazową statycznych elementów zawartości sieci Web.</span><span class="sxs-lookup"><span data-stu-id="14a6b-127">At build time, a project bundle is created with the convention `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css`, where the placeholder `{STATIC WEB ASSETS BASE PATH}` is the static web assets base path.</span></span>

<span data-ttu-id="14a6b-128">Jeśli są wykorzystywane inne projekty, takie jak pakiety NuGet lub [ Razor biblioteki klas](xref:blazor/components/class-libraries), plik pakietu:</span><span class="sxs-lookup"><span data-stu-id="14a6b-128">If other projects are utilized, such as NuGet packages or [Razor class libraries](xref:blazor/components/class-libraries), the bundled file:</span></span>

* <span data-ttu-id="14a6b-129">Odwołuje się do stylów przy użyciu importu CSS.</span><span class="sxs-lookup"><span data-stu-id="14a6b-129">References the styles using CSS imports.</span></span>
* <span data-ttu-id="14a6b-130">Nie jest publikowany jako statyczny zasób sieci Web aplikacji, która używa stylów.</span><span class="sxs-lookup"><span data-stu-id="14a6b-130">Isn't published as a static web asset of the app that consumes the styles.</span></span>

## <a name="child-component-support"></a><span data-ttu-id="14a6b-131">Obsługa składników podrzędnych</span><span class="sxs-lookup"><span data-stu-id="14a6b-131">Child component support</span></span>

<span data-ttu-id="14a6b-132">Domyślnie izolacja CSS dotyczy tylko składnika, który został skojarzony z formatem `{COMPONENT NAME}.razor.css` , gdzie symbol zastępczy `{COMPONENT NAME}` jest zwykle nazwą składnika.</span><span class="sxs-lookup"><span data-stu-id="14a6b-132">By default, CSS isolation only applies to the component you associate with the format `{COMPONENT NAME}.razor.css`, where the placeholder `{COMPONENT NAME}` is usually the component name.</span></span> <span data-ttu-id="14a6b-133">Aby zastosować zmiany do składnika podrzędnego, użyj `::deep` Combinator do dowolnego elementu podrzędnego w pliku składnika nadrzędnego `razor.css` .</span><span class="sxs-lookup"><span data-stu-id="14a6b-133">To apply changes to a child component, use the `::deep` combinator to any descendant elements in the parent component's `razor.css` file.</span></span> <span data-ttu-id="14a6b-134">`::deep`Combinator wybiera elementy, które są *elementami podrzędnymi* wygenerowanego identyfikatora zakresu elementu.</span><span class="sxs-lookup"><span data-stu-id="14a6b-134">The `::deep` combinator selects elements that are *descendants* of an element's generated scope identifier.</span></span> 

<span data-ttu-id="14a6b-135">Poniższy przykład pokazuje składnik nadrzędny o nazwie `Parent` ze składnikiem podrzędnym o nazwie `Child` .</span><span class="sxs-lookup"><span data-stu-id="14a6b-135">The following example shows a parent component called `Parent` with a child component called `Child`.</span></span>

<span data-ttu-id="14a6b-136">`Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="14a6b-136">`Parent.razor`:</span></span>

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

<span data-ttu-id="14a6b-137">`Child.razor`:</span><span class="sxs-lookup"><span data-stu-id="14a6b-137">`Child.razor`:</span></span>

```razor
<h1>Child Component</h1>
```

<span data-ttu-id="14a6b-138">Zaktualizuj `h1` deklarację w `Parent.razor.css` Combinator, `::deep` Aby wyrównać, że `h1` Deklaracja stylu musi być stosowana do składnika nadrzędnego i jego elementów podrzędnych:</span><span class="sxs-lookup"><span data-stu-id="14a6b-138">Update the `h1` declaration in `Parent.razor.css` with the `::deep` combinator to signify the `h1` style declaration must apply to the parent component and its children:</span></span>

```css
::deep h1 { 
    color: red;
}
```

<span data-ttu-id="14a6b-139">`h1`Styl ma teraz zastosowanie do `Parent` składników i `Child` bez konieczności tworzenia ODDZIELNEgo pliku CSS z zakresem dla składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="14a6b-139">The `h1` style now applies to the `Parent` and `Child` components without the need to create a separate scoped CSS file for the child component.</span></span>

> [!NOTE]
> <span data-ttu-id="14a6b-140">`::deep`Combinator działa tylko z elementami podrzędnymi.</span><span class="sxs-lookup"><span data-stu-id="14a6b-140">The `::deep` combinator only works with descendant elements.</span></span> <span data-ttu-id="14a6b-141">Następująca struktura HTML stosuje `h1` Style do składników zgodnie z oczekiwaniami:</span><span class="sxs-lookup"><span data-stu-id="14a6b-141">The following HTML structure applies the `h1` styles to components as expected:</span></span>
> 
> ```razor
> <div>
>     <h1>Parent</h1>
>
>     <Child />
> </div>
> ```
>
> <span data-ttu-id="14a6b-142">W tym scenariuszu ASP.NET Core stosuje identyfikator zakresu składnika nadrzędnego do `div` elementu, dzięki czemu przeglądarka wie o dziedziczeniu stylów ze składnika nadrzędnego.</span><span class="sxs-lookup"><span data-stu-id="14a6b-142">In this scenario, ASP.NET Core applies the parent component's scope identifier to the `div` element, so the browser knows to inherit styles from the parent component.</span></span>
>
> <span data-ttu-id="14a6b-143">Jednak wykluczenie `div` elementu powoduje usunięcie relacji elementu podrzędnego, a styl **nie** zostanie zastosowany do składnika podrzędnego:</span><span class="sxs-lookup"><span data-stu-id="14a6b-143">However, excluding the `div` element removes the descendant relationship, and the style is **not** applied to the child component:</span></span>
>
> ```razor
> <h1>Parent</h1>
>
> <Child />
> ```

## <a name="css-preprocessor-support"></a><span data-ttu-id="14a6b-144">Obsługa preprocesora CSS</span><span class="sxs-lookup"><span data-stu-id="14a6b-144">CSS preprocessor support</span></span>

<span data-ttu-id="14a6b-145">Preprocesora CSS są przydatne do ulepszania tworzenia arkuszy CSS, wykorzystując funkcje takie jak zmienne, zagnieżdżanie, moduły, domieszki i dziedziczenie.</span><span class="sxs-lookup"><span data-stu-id="14a6b-145">CSS preprocessors are useful for improving CSS development by utilizing features such as variables, nesting, modules, mixins, and inheritance.</span></span> <span data-ttu-id="14a6b-146">Chociaż izolacja CSS nie obsługuje natywnych preprocesora CSS, takich jak Sass lub less, integrowanie preprocesora CSS jest bezproblemowe, o ile kompilacja preprocesora przejdzie przed Blazor odpisaniem selektorów CSS podczas procesu kompilacji.</span><span class="sxs-lookup"><span data-stu-id="14a6b-146">While CSS isolation doesn't natively support CSS preprocessors such as Sass or Less, integrating CSS preprocessors is seamless as long as preprocessor compilation occurs before Blazor rewrites the CSS selectors during the build process.</span></span> <span data-ttu-id="14a6b-147">Za pomocą programu Visual Studio można na przykład skonfigurować istniejącą kompilację preprocesora jako zadanie **kompilacji** w Eksploratorze modułu uruchamiającego zadania programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="14a6b-147">Using Visual Studio for example, configure existing preprocessor compilation as a **Before Build** task in the Visual Studio Task Runner Explorer.</span></span>

<span data-ttu-id="14a6b-148">Wiele pakietów NuGet innych firm, takich jak [Delegate. SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), może kompilować pliki Sass/SCSS na początku procesu kompilacji, zanim nastąpi izolacja CSS i dodatkowa dodatkowa konfiguracja nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="14a6b-148">Many third-party NuGet packages, such as [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), can compile SASS/SCSS files at the beginning of the build process before CSS isolation occurs, and no additional additional configuration is required.</span></span>

## <a name="css-isolation-configuration"></a><span data-ttu-id="14a6b-149">Konfiguracja izolacji CSS</span><span class="sxs-lookup"><span data-stu-id="14a6b-149">CSS isolation configuration</span></span>

<span data-ttu-id="14a6b-150">Izolacja CSS została zaprojektowana tak, aby działała na zewnątrz, ale zapewnia konfigurację dla niektórych zaawansowanych scenariuszy, takich jak sytuacje, w których istnieją zależności od istniejących narzędzi lub przepływów pracy.</span><span class="sxs-lookup"><span data-stu-id="14a6b-150">CSS isolation is designed to work out-of-the-box but provides configuration for some advanced scenarios, such as when there are dependencies on existing tools or workflows.</span></span>

### <a name="customize-scope-identifier-format"></a><span data-ttu-id="14a6b-151">Dostosuj format identyfikatora zakresu</span><span class="sxs-lookup"><span data-stu-id="14a6b-151">Customize scope identifier format</span></span>

<span data-ttu-id="14a6b-152">Domyślnie identyfikatory zakresów używają formatu `b-<10-character-string>` .</span><span class="sxs-lookup"><span data-stu-id="14a6b-152">By default, scope identifiers use the format `b-<10-character-string>`.</span></span> <span data-ttu-id="14a6b-153">Aby dostosować Format identyfikatora zakresu, zaktualizuj plik projektu do wybranego wzorca:</span><span class="sxs-lookup"><span data-stu-id="14a6b-153">To customize the scope identifier format, update the project file to a desired pattern:</span></span>

```xml
<ItemGroup>
    <None Update="MyComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="14a6b-154">W poprzednim przykładzie, CSS Wygenerowano dla `MyComponent.Razor.css` zmiany jego identyfikatora zakresu z `b-<10-character-string>` do `my-custom-scope-identifier` .</span><span class="sxs-lookup"><span data-stu-id="14a6b-154">In the preceding example, the CSS generated for `MyComponent.Razor.css` changes its scope identifier from `b-<10-character-string>` to `my-custom-scope-identifier`.</span></span>

### <a name="change-base-path-for-static-web-assets"></a><span data-ttu-id="14a6b-155">Zmień ścieżkę podstawową dla statycznych zasobów sieci Web</span><span class="sxs-lookup"><span data-stu-id="14a6b-155">Change base path for static web assets</span></span>

<span data-ttu-id="14a6b-156">`scoped.styles.css`Plik jest generowany w katalogu głównym aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14a6b-156">The `scoped.styles.css` file is generated at the root of the app.</span></span> <span data-ttu-id="14a6b-157">W pliku projektu Użyj `StaticWebAssetBasePath` właściwości, aby zmienić domyślną ścieżkę.</span><span class="sxs-lookup"><span data-stu-id="14a6b-157">In the project file, use the `StaticWebAssetBasePath` property to change the default path.</span></span> <span data-ttu-id="14a6b-158">Poniższy przykład umieszcza `scoped.styles.css` plik i resztę zasobów aplikacji w `_content` ścieżce:</span><span class="sxs-lookup"><span data-stu-id="14a6b-158">The following example places the `scoped.styles.css` file, and the rest of the app's assets, at the `_content` path:</span></span>

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a><span data-ttu-id="14a6b-159">Wyłącz automatyczne tworzenie</span><span class="sxs-lookup"><span data-stu-id="14a6b-159">Disable automatic bundling</span></span>

<span data-ttu-id="14a6b-160">Aby zrezygnować z Blazor publikowania i ładowania plików objętych zakresem w czasie wykonywania, użyj `DisableScopedCssBundling` właściwości.</span><span class="sxs-lookup"><span data-stu-id="14a6b-160">To opt out of how Blazor publishes and loads scoped files at runtime, use the `DisableScopedCssBundling` property.</span></span> <span data-ttu-id="14a6b-161">W przypadku korzystania z tej właściwości oznacza to, że inne narzędzia i procesy są odpowiedzialne za pobieranie odizolowanych plików CSS z `obj` katalogu i publikowanie ich w czasie wykonywania:</span><span class="sxs-lookup"><span data-stu-id="14a6b-161">When using this property, it means other tools or processes are responsible for taking the isolated CSS files from the `obj` directory and publishing and loading them at runtime:</span></span>

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```
