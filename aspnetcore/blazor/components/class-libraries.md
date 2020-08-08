---
title: RazorBiblioteki klas składników ASP.NET Core
author: guardrex
description: Odkryj, jak składniki mogą być dołączane do Blazor aplikacji z zewnętrznej biblioteki składników.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
no-loc:
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
ms.openlocfilehash: 0bace66e0aab41cf31e18fe9f86dbf9bbcf59447
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014701"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a><span data-ttu-id="c8f72-103">RazorBiblioteki klas składników ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c8f72-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="c8f72-104">Autor [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="c8f72-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="c8f72-105">Składniki mogą być współużytkowane w [ Razor bibliotece klas (RCL)](xref:razor-pages/ui-class) w różnych projektach.</span><span class="sxs-lookup"><span data-stu-id="c8f72-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="c8f72-106">\* Razor Biblioteka klas składników\* może być dołączana z:</span><span class="sxs-lookup"><span data-stu-id="c8f72-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="c8f72-107">Inny projekt w rozwiązaniu.</span><span class="sxs-lookup"><span data-stu-id="c8f72-107">Another project in the solution.</span></span>
* <span data-ttu-id="c8f72-108">Pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="c8f72-108">A NuGet package.</span></span>
* <span data-ttu-id="c8f72-109">Przywoływana Biblioteka platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="c8f72-109">A referenced .NET library.</span></span>

<span data-ttu-id="c8f72-110">Podobnie jak składniki są zwykłymi typami .NET, składniki udostępniane przez RCL są normalnymi zestawami platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="c8f72-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="c8f72-111">Utwórz RCL</span><span class="sxs-lookup"><span data-stu-id="c8f72-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c8f72-112">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c8f72-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="c8f72-113">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="c8f72-113">Create a new project.</span></span>
1. <span data-ttu-id="c8f72-114">Wybierz \*\* Razor bibliotekę klas\*\*.</span><span class="sxs-lookup"><span data-stu-id="c8f72-114">Select **Razor Class Library**.</span></span> <span data-ttu-id="c8f72-115">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="c8f72-115">Select **Next**.</span></span>
1. <span data-ttu-id="c8f72-116">W oknie dialogowym **Utwórz nową Razor bibliotekę klas** wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="c8f72-116">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="c8f72-117">Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj nazwę domyślną projektu.</span><span class="sxs-lookup"><span data-stu-id="c8f72-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="c8f72-118">W przykładach w tym temacie użyto nazwy projektu `ComponentLibrary` .</span><span class="sxs-lookup"><span data-stu-id="c8f72-118">The examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="c8f72-119">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="c8f72-119">Select **Create**.</span></span>
1. <span data-ttu-id="c8f72-120">Dodaj RCL do rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="c8f72-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="c8f72-121">Kliknij prawym przyciskiem myszy rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="c8f72-121">Right-click the solution.</span></span> <span data-ttu-id="c8f72-122">Wybierz pozycję **Dodaj**  >  **istniejący projekt**.</span><span class="sxs-lookup"><span data-stu-id="c8f72-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="c8f72-123">Przejdź do pliku projektu RCL.</span><span class="sxs-lookup"><span data-stu-id="c8f72-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="c8f72-124">Wybierz plik projektu RCL ( `.csproj` ).</span><span class="sxs-lookup"><span data-stu-id="c8f72-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="c8f72-125">Dodaj odwołanie RCL z aplikacji:</span><span class="sxs-lookup"><span data-stu-id="c8f72-125">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="c8f72-126">Kliknij prawym przyciskiem myszy projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c8f72-126">Right-click the app project.</span></span> <span data-ttu-id="c8f72-127">Wybierz pozycję **Dodaj**  >  **odwołanie**.</span><span class="sxs-lookup"><span data-stu-id="c8f72-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="c8f72-128">Wybierz projekt RCL.</span><span class="sxs-lookup"><span data-stu-id="c8f72-128">Select the RCL project.</span></span> <span data-ttu-id="c8f72-129">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="c8f72-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="c8f72-130">Jeśli pole wyboru **strony i widoki pomocy technicznej** jest zaznaczone podczas generowania RCL z szablonu, Dodaj również `_Imports.razor` plik do katalogu głównego wygenerowanego projektu z następującą zawartością, aby włączyć Razor Tworzenie składników:</span><span class="sxs-lookup"><span data-stu-id="c8f72-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="c8f72-131">Ręcznie Dodaj plik do katalogu głównego wygenerowanego projektu.</span><span class="sxs-lookup"><span data-stu-id="c8f72-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c8f72-132">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="c8f72-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="c8f72-133">Użyj szablonu \*\* Razor biblioteki klas\*\* ( `razorclasslib` ) za pomocą [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenia w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="c8f72-133">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="c8f72-134">W poniższym przykładzie jest tworzony RCL o nazwie `ComponentLibrary` .</span><span class="sxs-lookup"><span data-stu-id="c8f72-134">In the following example, an RCL is created named `ComponentLibrary`.</span></span> <span data-ttu-id="c8f72-135">Folder, który `ComponentLibrary` ma zostać utworzony, jest tworzony automatycznie podczas wykonywania polecenia:</span><span class="sxs-lookup"><span data-stu-id="c8f72-135">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > <span data-ttu-id="c8f72-136">Jeśli `-s|--support-pages-and-views` przełącznik jest używany podczas generowania RCL z szablonu, Dodaj również `_Imports.razor` plik do katalogu głównego wygenerowanego projektu z następującą zawartością, aby włączyć Razor Tworzenie składników:</span><span class="sxs-lookup"><span data-stu-id="c8f72-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="c8f72-137">Ręcznie Dodaj plik do katalogu głównego wygenerowanego projektu.</span><span class="sxs-lookup"><span data-stu-id="c8f72-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="c8f72-138">Aby dodać bibliotekę do istniejącego projektu, użyj [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) polecenia w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="c8f72-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="c8f72-139">W poniższym przykładzie RCL jest dodawany do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c8f72-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="c8f72-140">Wykonaj następujące polecenie z folderu projektu aplikacji z ścieżką do biblioteki:</span><span class="sxs-lookup"><span data-stu-id="c8f72-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="c8f72-141">Korzystanie ze składnika biblioteki</span><span class="sxs-lookup"><span data-stu-id="c8f72-141">Consume a library component</span></span>

<span data-ttu-id="c8f72-142">Aby można było korzystać ze składników zdefiniowanych w bibliotece w innym projekcie, należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="c8f72-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="c8f72-143">Użyj pełnej nazwy typu z przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="c8f72-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="c8f72-144">Zastosuj Razor [`@using`](xref:mvc/views/razor#using) dyrektywę.</span><span class="sxs-lookup"><span data-stu-id="c8f72-144">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="c8f72-145">Poszczególne składniki można dodawać według nazwy.</span><span class="sxs-lookup"><span data-stu-id="c8f72-145">Individual components can be added by name.</span></span>

<span data-ttu-id="c8f72-146">W poniższych przykładach `ComponentLibrary` jest biblioteka składników zawierająca `Component1` składnik ( `Component1.razor` ).</span><span class="sxs-lookup"><span data-stu-id="c8f72-146">In the following examples, `ComponentLibrary` is a component library containing the `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="c8f72-147">`Component1`Składnik jest przykładowym składnikiem automatycznie dodawanym przez szablon projektu RCL podczas tworzenia biblioteki.</span><span class="sxs-lookup"><span data-stu-id="c8f72-147">The `Component1` component is an example component automatically added by the RCL project template when the library is created.</span></span>

<span data-ttu-id="c8f72-148">Odwołuje się do `Component1` składnika przy użyciu jego przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="c8f72-148">Reference the `Component1` component using its namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

<span data-ttu-id="c8f72-149">Alternatywnie można przenieść bibliotekę do zakresu za pomocą [`@using`](xref:mvc/views/razor#using) dyrektywy i użyć składnika bez jego przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="c8f72-149">Alternatively, bring the library into scope with an [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace:</span></span>

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="c8f72-150">Opcjonalnie należy uwzględnić `@using ComponentLibrary` dyrektywę w pliku najwyższego poziomu, `_Import.razor` Aby udostępnić składniki biblioteki dla całego projektu.</span><span class="sxs-lookup"><span data-stu-id="c8f72-150">Optionally, include the `@using ComponentLibrary` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="c8f72-151">Dodaj dyrektywę do pliku na `_Import.razor` dowolnym poziomie, aby zastosować przestrzeń nazw do pojedynczego składnika lub zestawu składników w folderze.</span><span class="sxs-lookup"><span data-stu-id="c8f72-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c8f72-152">Aby dostarczyć `Component1` `my-component` klasy CSS do składnika, Połącz z arkuszem stylów biblioteki przy użyciu [ `Link` składnika](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) struktury w `Component1.razor` :</span><span class="sxs-lookup"><span data-stu-id="c8f72-152">To provide `Component1`'s `my-component` CSS class to the component, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:</span></span>

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

<span data-ttu-id="c8f72-153">Aby udostępnić arkusz stylów w aplikacji, możesz połączyć się z arkuszem stylów biblioteki w `wwwroot/index.html` pliku ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` pliku () aplikacji ( Blazor Server ):</span><span class="sxs-lookup"><span data-stu-id="c8f72-153">To provide the stylesheet across the app, you can alternatively link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

<span data-ttu-id="c8f72-154">Gdy `Link` składnik jest używany w składniku podrzędnym, połączony element zawartości jest również dostępny dla każdego innego składnika podrzędnego składnika nadrzędnego, o ile jest renderowany element podrzędny ze `Link` składnikiem.</span><span class="sxs-lookup"><span data-stu-id="c8f72-154">When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered.</span></span> <span data-ttu-id="c8f72-155">Rozróżnienie między użyciem `Link` składnika w składniku podrzędnym i umieszczenie `<link>` znacznika HTML w `wwwroot/index.html` lub `Pages/_Host.cshtml` polega na tym, że renderowany tag HTML składnika Framework:</span><span class="sxs-lookup"><span data-stu-id="c8f72-155">The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="c8f72-156">Mogą być modyfikowane przez stan aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c8f72-156">Can be modified by application state.</span></span> <span data-ttu-id="c8f72-157">`<link>`Nie można zmodyfikować oznakowanego języka HTML w stanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c8f72-157">A hard-coded `<link>` HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="c8f72-158">Jest usuwany z kodu HTML, `<head>` gdy składnik nadrzędny nie jest już renderowany.</span><span class="sxs-lookup"><span data-stu-id="c8f72-158">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="c8f72-159">Aby podać `Component1` `my-component` klasę CSS, Połącz się z arkuszem stylów biblioteki w `wwwroot/index.html` pliku ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` pliku () aplikacji ( Blazor Server ):</span><span class="sxs-lookup"><span data-stu-id="c8f72-159">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a><span data-ttu-id="c8f72-160">Tworzenie Razor biblioteki klas składników ze statycznymi zasobami</span><span class="sxs-lookup"><span data-stu-id="c8f72-160">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="c8f72-161">RCL może zawierać statyczne zasoby.</span><span class="sxs-lookup"><span data-stu-id="c8f72-161">An RCL can include static assets.</span></span> <span data-ttu-id="c8f72-162">Zasoby statyczne są dostępne dla każdej aplikacji, która korzysta z biblioteki.</span><span class="sxs-lookup"><span data-stu-id="c8f72-162">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="c8f72-163">Aby uzyskać więcej informacji, zobacz <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="c8f72-163">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a><span data-ttu-id="c8f72-164">Dostarczaj składniki i zasoby statyczne do wielu Blazor aplikacji hostowanych</span><span class="sxs-lookup"><span data-stu-id="c8f72-164">Supply components and static assets to multiple hosted Blazor apps</span></span>

<span data-ttu-id="c8f72-165">Aby uzyskać więcej informacji, zobacz <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="c8f72-165">For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="c8f72-166">Kompilowanie, pakowanie i dostarczanie do narzędzia NuGet</span><span class="sxs-lookup"><span data-stu-id="c8f72-166">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="c8f72-167">Ponieważ biblioteki składników są standardowymi bibliotekami .NET, pakowanie i dostarczanie ich do narzędzia NuGet nie różni się od pakowania i wysyłania żadnej biblioteki do narzędzia NuGet.</span><span class="sxs-lookup"><span data-stu-id="c8f72-167">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="c8f72-168">Pakowanie jest wykonywane przy użyciu [`dotnet pack`](/dotnet/core/tools/dotnet-pack) polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="c8f72-168">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="c8f72-169">Przekaż pakiet do narzędzia NuGet przy użyciu [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) polecenia w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="c8f72-169">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c8f72-170">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="c8f72-170">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="c8f72-171">Dodawanie pliku konfiguracji konsolidatora XML do biblioteki</span><span class="sxs-lookup"><span data-stu-id="c8f72-171">Add an XML linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
