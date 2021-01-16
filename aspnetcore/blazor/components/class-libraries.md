---
title: RazorBiblioteki klas składników ASP.NET Core
author: guardrex
description: Odkryj, jak składniki mogą być dołączane do Blazor aplikacji z zewnętrznej biblioteki składników.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/12/2021
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
uid: blazor/components/class-libraries
ms.openlocfilehash: 14370f9bbf45079fd3654d3e55af4178691cf4f5
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252555"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a><span data-ttu-id="f28b3-103">RazorBiblioteki klas składników ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f28b3-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="f28b3-104">Autor [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="f28b3-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="f28b3-105">Składniki mogą być współużytkowane w [ Razor bibliotece klas (RCL)](xref:razor-pages/ui-class) w różnych projektach.</span><span class="sxs-lookup"><span data-stu-id="f28b3-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="f28b3-106">*Razor Biblioteka klas składników* może być dołączana z:</span><span class="sxs-lookup"><span data-stu-id="f28b3-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="f28b3-107">Inny projekt w rozwiązaniu.</span><span class="sxs-lookup"><span data-stu-id="f28b3-107">Another project in the solution.</span></span>
* <span data-ttu-id="f28b3-108">Pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="f28b3-108">A NuGet package.</span></span>
* <span data-ttu-id="f28b3-109">Przywoływana Biblioteka platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="f28b3-109">A referenced .NET library.</span></span>

<span data-ttu-id="f28b3-110">Podobnie jak składniki są zwykłymi typami .NET, składniki udostępniane przez RCL są normalnymi zestawami platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="f28b3-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="f28b3-111">Utwórz RCL</span><span class="sxs-lookup"><span data-stu-id="f28b3-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f28b3-112">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f28b3-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f28b3-113">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="f28b3-113">Create a new project.</span></span>
1. <span data-ttu-id="f28b3-114">Wybierz **Razor bibliotekę klas**.</span><span class="sxs-lookup"><span data-stu-id="f28b3-114">Select **Razor Class Library**.</span></span> <span data-ttu-id="f28b3-115">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="f28b3-115">Select **Next**.</span></span>
1. <span data-ttu-id="f28b3-116">W oknie dialogowym **Utwórz nową Razor bibliotekę klas** wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="f28b3-116">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="f28b3-117">Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj nazwę domyślną projektu.</span><span class="sxs-lookup"><span data-stu-id="f28b3-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="f28b3-118">W przykładach w tym temacie użyto nazwy projektu `ComponentLibrary` .</span><span class="sxs-lookup"><span data-stu-id="f28b3-118">The examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="f28b3-119">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="f28b3-119">Select **Create**.</span></span>
1. <span data-ttu-id="f28b3-120">Dodaj RCL do rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="f28b3-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="f28b3-121">Kliknij prawym przyciskiem myszy rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="f28b3-121">Right-click the solution.</span></span> <span data-ttu-id="f28b3-122">Wybierz pozycję **Dodaj**  >  **istniejący projekt**.</span><span class="sxs-lookup"><span data-stu-id="f28b3-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="f28b3-123">Przejdź do pliku projektu RCL.</span><span class="sxs-lookup"><span data-stu-id="f28b3-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="f28b3-124">Wybierz plik projektu RCL ( `.csproj` ).</span><span class="sxs-lookup"><span data-stu-id="f28b3-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="f28b3-125">Dodaj odwołanie do RCL z aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f28b3-125">Add a reference to the RCL from the app:</span></span>
   1. <span data-ttu-id="f28b3-126">Kliknij prawym przyciskiem myszy projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f28b3-126">Right-click the app project.</span></span> <span data-ttu-id="f28b3-127">Wybierz pozycję **Dodaj**  >  **odwołanie**.</span><span class="sxs-lookup"><span data-stu-id="f28b3-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="f28b3-128">Wybierz projekt RCL.</span><span class="sxs-lookup"><span data-stu-id="f28b3-128">Select the RCL project.</span></span> <span data-ttu-id="f28b3-129">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="f28b3-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="f28b3-130">Jeśli pole wyboru **strony i widoki pomocy technicznej** jest zaznaczone podczas generowania RCL z szablonu, Dodaj również `_Imports.razor` plik do katalogu głównego wygenerowanego projektu z następującą zawartością, aby włączyć Razor Tworzenie składników:</span><span class="sxs-lookup"><span data-stu-id="f28b3-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="f28b3-131">Ręcznie Dodaj plik do katalogu głównego wygenerowanego projektu.</span><span class="sxs-lookup"><span data-stu-id="f28b3-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f28b3-132">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="f28b3-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="f28b3-133">Użyj szablonu **Razor biblioteki klas** ( `razorclasslib` ) za pomocą [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenia w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="f28b3-133">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="f28b3-134">W poniższym przykładzie jest tworzony RCL o nazwie `ComponentLibrary` .</span><span class="sxs-lookup"><span data-stu-id="f28b3-134">In the following example, an RCL is created named `ComponentLibrary`.</span></span> <span data-ttu-id="f28b3-135">Folder, który `ComponentLibrary` ma zostać utworzony, jest tworzony automatycznie podczas wykonywania polecenia:</span><span class="sxs-lookup"><span data-stu-id="f28b3-135">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > <span data-ttu-id="f28b3-136">Jeśli `-s|--support-pages-and-views` przełącznik jest używany podczas generowania RCL z szablonu, Dodaj również `_Imports.razor` plik do katalogu głównego wygenerowanego projektu z następującą zawartością, aby włączyć Razor Tworzenie składników:</span><span class="sxs-lookup"><span data-stu-id="f28b3-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="f28b3-137">Ręcznie Dodaj plik do katalogu głównego wygenerowanego projektu.</span><span class="sxs-lookup"><span data-stu-id="f28b3-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="f28b3-138">Aby dodać bibliotekę do istniejącego projektu, użyj [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) polecenia w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="f28b3-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="f28b3-139">W poniższym przykładzie RCL jest dodawany do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f28b3-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="f28b3-140">Wykonaj następujące polecenie z folderu projektu aplikacji z ścieżką do biblioteki:</span><span class="sxs-lookup"><span data-stu-id="f28b3-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="f28b3-141">Korzystanie ze składnika biblioteki</span><span class="sxs-lookup"><span data-stu-id="f28b3-141">Consume a library component</span></span>

<span data-ttu-id="f28b3-142">Aby można było korzystać ze składników zdefiniowanych w bibliotece w innym projekcie, należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="f28b3-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="f28b3-143">Użyj pełnej nazwy typu z przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="f28b3-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="f28b3-144">Zastosuj Razor [`@using`](xref:mvc/views/razor#using) dyrektywę.</span><span class="sxs-lookup"><span data-stu-id="f28b3-144">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="f28b3-145">Poszczególne składniki można dodawać według nazwy.</span><span class="sxs-lookup"><span data-stu-id="f28b3-145">Individual components can be added by name.</span></span>

<span data-ttu-id="f28b3-146">W poniższych przykładach `ComponentLibrary` jest biblioteka składników zawierająca `Component1` składnik ( `Component1.razor` ).</span><span class="sxs-lookup"><span data-stu-id="f28b3-146">In the following examples, `ComponentLibrary` is a component library containing the `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="f28b3-147">`Component1`Składnik jest przykładowym składnikiem automatycznie dodawanym przez szablon projektu RCL podczas tworzenia biblioteki.</span><span class="sxs-lookup"><span data-stu-id="f28b3-147">The `Component1` component is an example component automatically added by the RCL project template when the library is created.</span></span>

<span data-ttu-id="f28b3-148">Odwołuje się do `Component1` składnika przy użyciu jego przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="f28b3-148">Reference the `Component1` component using its namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

<span data-ttu-id="f28b3-149">Alternatywnie można przenieść bibliotekę do zakresu za pomocą [`@using`](xref:mvc/views/razor#using) dyrektywy i użyć składnika bez jego przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="f28b3-149">Alternatively, bring the library into scope with an [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace:</span></span>

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="f28b3-150">Opcjonalnie należy uwzględnić `@using ComponentLibrary` dyrektywę w pliku najwyższego poziomu, `_Import.razor` Aby udostępnić składniki biblioteki dla całego projektu.</span><span class="sxs-lookup"><span data-stu-id="f28b3-150">Optionally, include the `@using ComponentLibrary` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="f28b3-151">Dodaj dyrektywę do pliku na `_Import.razor` dowolnym poziomie, aby zastosować przestrzeń nazw do pojedynczego składnika lub zestawu składników w folderze.</span><span class="sxs-lookup"><span data-stu-id="f28b3-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f28b3-152">W przypadku składników biblioteki, które korzystają z [izolacji CSS](xref:blazor/components/css-isolation), nie istnieje potrzeba jawnego łączenia stylów poszczególnych składników biblioteki w aplikacji, która korzysta z biblioteki.</span><span class="sxs-lookup"><span data-stu-id="f28b3-152">For library components that use [CSS isolation](xref:blazor/components/css-isolation), there's no need to explicitly link the library's individual component stylesheets in the app that consumes the library.</span></span> <span data-ttu-id="f28b3-153">Style składników są automatycznie udostępniane aplikacji zużywanej.</span><span class="sxs-lookup"><span data-stu-id="f28b3-153">The component styles are automatically made available to the consuming app.</span></span>

<!-- REACTIVATE WHEN HEAD COMPONENTS COME BACK AT 6.0

To provide additional library component styles from stylesheets in the library's `wwwroot` folder, link the stylesheets using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

NEXT PARAGRAPH: RECAST TO 'CAN ALSO ADOPT ...'

-->

<span data-ttu-id="f28b3-154">Aby zapewnić dodatkowe style składników biblioteki z arkuszy stylów w `wwwroot` folderze biblioteki, Połącz arkusze stylów w `wwwroot/index.html` pliku aplikacji ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` pliku ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="f28b3-154">To provide additional library component styles from stylesheets in the library's `wwwroot` folder, link the stylesheets in the consuming app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/additionalStyles.css" rel="stylesheet" />
</head>
```

<!-- REACTIVATE WHEN HEAD COMPONENTS COME BACK AT 6.0

When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered. The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded `<link>` HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="f28b3-155">Aby podać `Component1` `my-component` klasę CSS, Połącz się z arkuszem stylów biblioteki w `wwwroot/index.html` pliku ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` pliku () aplikacji ( Blazor Server ):</span><span class="sxs-lookup"><span data-stu-id="f28b3-155">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a><span data-ttu-id="f28b3-156">Tworzenie Razor biblioteki klas składników ze statycznymi zasobami</span><span class="sxs-lookup"><span data-stu-id="f28b3-156">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="f28b3-157">RCL może zawierać statyczne zasoby.</span><span class="sxs-lookup"><span data-stu-id="f28b3-157">An RCL can include static assets.</span></span> <span data-ttu-id="f28b3-158">Zasoby statyczne są dostępne dla każdej aplikacji, która korzysta z biblioteki.</span><span class="sxs-lookup"><span data-stu-id="f28b3-158">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="f28b3-159">Aby uzyskać więcej informacji, zobacz <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="f28b3-159">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a><span data-ttu-id="f28b3-160">Dostarczaj składniki i zasoby statyczne do wielu Blazor aplikacji hostowanych</span><span class="sxs-lookup"><span data-stu-id="f28b3-160">Supply components and static assets to multiple hosted Blazor apps</span></span>

<span data-ttu-id="f28b3-161">Aby uzyskać więcej informacji, zobacz <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="f28b3-161">For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-no-locblazor-webassembly"></a><span data-ttu-id="f28b3-162">Analizator zgodności przeglądarki dla programu Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="f28b3-162">Browser compatibility analyzer for Blazor WebAssembly</span></span>

<span data-ttu-id="f28b3-163">Blazor WebAssembly aplikacje są przeznaczone dla całego obszaru powierzchni interfejsu API platformy .NET, ale nie wszystkie interfejsy API platformy .NET są obsługiwane w zestawie webassembly z powodu ograniczeń piaskownicy przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="f28b3-163">Blazor WebAssembly apps target the full .NET API surface area, but not all .NET APIs are supported on WebAssembly due to browser sandbox constraints.</span></span> <span data-ttu-id="f28b3-164">Nieobsługiwane interfejsy API są zgłaszane <xref:System.PlatformNotSupportedException> podczas uruchamiania w zestawie webassembly.</span><span class="sxs-lookup"><span data-stu-id="f28b3-164">Unsupported APIs throw <xref:System.PlatformNotSupportedException> when running on WebAssembly.</span></span> <span data-ttu-id="f28b3-165">Analizator zgodności platformy ostrzega dewelopera, gdy aplikacja korzysta z interfejsów API, które nie są obsługiwane przez Platformy docelowe aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f28b3-165">A platform compatibility analyzer warns the developer when the app uses APIs that aren't supported by the app's target platforms.</span></span> <span data-ttu-id="f28b3-166">W przypadku Blazor WebAssembly aplikacji oznacza to sprawdzenie, czy interfejsy API są obsługiwane w przeglądarkach.</span><span class="sxs-lookup"><span data-stu-id="f28b3-166">For Blazor WebAssembly apps, this means checking that APIs are supported in browsers.</span></span> <span data-ttu-id="f28b3-167">Dodawanie adnotacji do interfejsów API programu .NET Framework dla analizatora zgodności jest procesem trwającym, więc nie wszystkie interfejsy API programu .NET Framework są obecnie opatrzone adnotacją.</span><span class="sxs-lookup"><span data-stu-id="f28b3-167">Annotating .NET framework APIs for the compatibility analyzer is an on-going process, so not all .NET framework API is currently annotated.</span></span>

<span data-ttu-id="f28b3-168">Blazor WebAssembly i Razor projekty biblioteki klas *automatycznie* włączają testy compatibilty przeglądarki przez dodanie `browser` jako obsługiwanej platformy z `SupportedPlatform` elementem MSBuild.</span><span class="sxs-lookup"><span data-stu-id="f28b3-168">Blazor WebAssembly and Razor class library projects *automatically* enable browser compatibilty checks by adding `browser` as a supported platform with the `SupportedPlatform` MSBuild item.</span></span> <span data-ttu-id="f28b3-169">Deweloperzy biblioteki mogą ręcznie dodać `SupportedPlatform` element do pliku projektu biblioteki, aby włączyć tę funkcję:</span><span class="sxs-lookup"><span data-stu-id="f28b3-169">Library developers can manually add the `SupportedPlatform` item to a library's project file to enable the feature:</span></span>

```xml
<ItemGroup>
  <SupportedPlatform Include="browser" />
</ItemGroup>
```

<span data-ttu-id="f28b3-170">Podczas tworzenia biblioteki należy wskazać, że konkretny interfejs API nie jest obsługiwany w przeglądarkach, określając `browser` <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute> :</span><span class="sxs-lookup"><span data-stu-id="f28b3-170">When authoring a library, indicate that a particular API isn't supported in browsers by specifying `browser` to <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute>:</span></span>

```csharp
[UnsupportedOSPlatform("browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

<span data-ttu-id="f28b3-171">Aby uzyskać więcej informacji, zobacz [Dodawanie adnotacji do interfejsów API jako nieobsługiwanych na określonych platformach (repozytorium dotnet/Designing w witrynie GitHub](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).</span><span class="sxs-lookup"><span data-stu-id="f28b3-171">For more information, see [Annotating APIs as unsupported on specific platforms (dotnet/designs GitHub repository](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).</span></span>

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="f28b3-172">Blazor Izolacja kodu JavaScript i odwołania do obiektów</span><span class="sxs-lookup"><span data-stu-id="f28b3-172">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="f28b3-173">Blazor Włącza izolację JavaScript w standardowych [modułach języka JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span><span class="sxs-lookup"><span data-stu-id="f28b3-173">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="f28b3-174">Izolacja JavaScript zapewnia następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="f28b3-174">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="f28b3-175">Zaimportowana wartość JavaScript nie jest już zanieczyszczana globalną przestrzenią nazw.</span><span class="sxs-lookup"><span data-stu-id="f28b3-175">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="f28b3-176">Odbiorcy biblioteki i składników nie są zobowiązani do ręcznego zaimportowania powiązanego języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f28b3-176">Consumers of the library and components aren't required to manually import the related JavaScript.</span></span>

<span data-ttu-id="f28b3-177">Aby uzyskać więcej informacji, zobacz <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span><span class="sxs-lookup"><span data-stu-id="f28b3-177">For more information, see <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span></span>

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="f28b3-178">Kompilowanie, pakowanie i dostarczanie do narzędzia NuGet</span><span class="sxs-lookup"><span data-stu-id="f28b3-178">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="f28b3-179">Ponieważ biblioteki składników są standardowymi bibliotekami .NET, pakowanie i dostarczanie ich do narzędzia NuGet nie różni się od pakowania i wysyłania żadnej biblioteki do narzędzia NuGet.</span><span class="sxs-lookup"><span data-stu-id="f28b3-179">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="f28b3-180">Pakowanie jest wykonywane przy użyciu [`dotnet pack`](/dotnet/core/tools/dotnet-pack) polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="f28b3-180">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="f28b3-181">Przekaż pakiet do narzędzia NuGet przy użyciu [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) polecenia w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="f28b3-181">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f28b3-182">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="f28b3-182">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="f28b3-183">Dodawanie pliku konfiguracji programu do biblioteki języka pośredniego (IL) w formacie XML</span><span class="sxs-lookup"><span data-stu-id="f28b3-183">Add an XML Intermediate Language (IL) Trimmer configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-trimmer)
* [<span data-ttu-id="f28b3-184">Obsługa izolacji CSS z Razor bibliotekami klas</span><span class="sxs-lookup"><span data-stu-id="f28b3-184">CSS isolation support with Razor class libraries</span></span>](xref:blazor/components/css-isolation#razor-class-library-rcl-support)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="f28b3-185">Dodawanie pliku konfiguracji konsolidatora języka pośredniego (IL) XML do biblioteki</span><span class="sxs-lookup"><span data-stu-id="f28b3-185">Add an XML Intermediate Language (IL) Linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
