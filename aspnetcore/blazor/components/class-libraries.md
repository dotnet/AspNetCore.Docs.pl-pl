---
title: RazorBiblioteki klas składników ASP.NET Core
author: guardrex
description: Odkryj, jak składniki mogą być dołączane do Blazor aplikacji z zewnętrznej biblioteki składników.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/class-libraries
ms.openlocfilehash: b172059407f9a08dacc0fadd804864c7aee7fb90
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944503"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="79cfc-103">RazorBiblioteki klas składników ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="79cfc-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="79cfc-104">Autor [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="79cfc-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="79cfc-105">Składniki mogą być współużytkowane w [ Razor bibliotece klas (RCL)](xref:razor-pages/ui-class) w różnych projektach.</span><span class="sxs-lookup"><span data-stu-id="79cfc-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="79cfc-106">\* Razor Biblioteka klas składników\* może być dołączana z:</span><span class="sxs-lookup"><span data-stu-id="79cfc-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="79cfc-107">Inny projekt w rozwiązaniu.</span><span class="sxs-lookup"><span data-stu-id="79cfc-107">Another project in the solution.</span></span>
* <span data-ttu-id="79cfc-108">Pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="79cfc-108">A NuGet package.</span></span>
* <span data-ttu-id="79cfc-109">Przywoływana Biblioteka platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="79cfc-109">A referenced .NET library.</span></span>

<span data-ttu-id="79cfc-110">Podobnie jak składniki są zwykłymi typami .NET, składniki udostępniane przez RCL są normalnymi zestawami platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="79cfc-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="79cfc-111">Utwórz RCL</span><span class="sxs-lookup"><span data-stu-id="79cfc-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="79cfc-112">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="79cfc-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="79cfc-113">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="79cfc-113">Create a new project.</span></span>
1. <span data-ttu-id="79cfc-114">Wybierz \*\* Razor bibliotekę klas\*\*.</span><span class="sxs-lookup"><span data-stu-id="79cfc-114">Select **Razor Class Library**.</span></span> <span data-ttu-id="79cfc-115">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="79cfc-115">Select **Next**.</span></span>
1. <span data-ttu-id="79cfc-116">W oknie dialogowym **Utwórz nową Razor bibliotekę klas** wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="79cfc-116">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="79cfc-117">Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj nazwę domyślną projektu.</span><span class="sxs-lookup"><span data-stu-id="79cfc-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="79cfc-118">W przykładach w tym temacie użyto nazwy projektu `MyComponentLib1` .</span><span class="sxs-lookup"><span data-stu-id="79cfc-118">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="79cfc-119">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="79cfc-119">Select **Create**.</span></span>
1. <span data-ttu-id="79cfc-120">Dodaj RCL do rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="79cfc-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="79cfc-121">Kliknij prawym przyciskiem myszy rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="79cfc-121">Right-click the solution.</span></span> <span data-ttu-id="79cfc-122">Wybierz pozycję **Dodaj**  >  **istniejący projekt**.</span><span class="sxs-lookup"><span data-stu-id="79cfc-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="79cfc-123">Przejdź do pliku projektu RCL.</span><span class="sxs-lookup"><span data-stu-id="79cfc-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="79cfc-124">Wybierz plik projektu RCL ( `.csproj` ).</span><span class="sxs-lookup"><span data-stu-id="79cfc-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="79cfc-125">Dodaj odwołanie RCL z aplikacji:</span><span class="sxs-lookup"><span data-stu-id="79cfc-125">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="79cfc-126">Kliknij prawym przyciskiem myszy projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="79cfc-126">Right-click the app project.</span></span> <span data-ttu-id="79cfc-127">Wybierz pozycję **Dodaj**  >  **odwołanie**.</span><span class="sxs-lookup"><span data-stu-id="79cfc-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="79cfc-128">Wybierz projekt RCL.</span><span class="sxs-lookup"><span data-stu-id="79cfc-128">Select the RCL project.</span></span> <span data-ttu-id="79cfc-129">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="79cfc-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="79cfc-130">Jeśli pole wyboru **strony i widoki pomocy technicznej** jest zaznaczone podczas generowania RCL z szablonu, Dodaj również `_Imports.razor` plik do katalogu głównego wygenerowanego projektu z następującą zawartością, aby włączyć Razor Tworzenie składników:</span><span class="sxs-lookup"><span data-stu-id="79cfc-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="79cfc-131">Ręcznie Dodaj plik do katalogu głównego wygenerowanego projektu.</span><span class="sxs-lookup"><span data-stu-id="79cfc-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="79cfc-132">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="79cfc-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="79cfc-133">Użyj szablonu \*\* Razor biblioteki klas\*\* ( `razorclasslib` ) za pomocą [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenia w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="79cfc-133">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="79cfc-134">W poniższym przykładzie jest tworzony RCL o nazwie `MyComponentLib1` .</span><span class="sxs-lookup"><span data-stu-id="79cfc-134">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="79cfc-135">Folder, który `MyComponentLib1` ma zostać utworzony, jest tworzony automatycznie podczas wykonywania polecenia:</span><span class="sxs-lookup"><span data-stu-id="79cfc-135">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > <span data-ttu-id="79cfc-136">Jeśli `-s|--support-pages-and-views` przełącznik jest używany podczas generowania RCL z szablonu, Dodaj również `_Imports.razor` plik do katalogu głównego wygenerowanego projektu z następującą zawartością, aby włączyć Razor Tworzenie składników:</span><span class="sxs-lookup"><span data-stu-id="79cfc-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="79cfc-137">Ręcznie Dodaj plik do katalogu głównego wygenerowanego projektu.</span><span class="sxs-lookup"><span data-stu-id="79cfc-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="79cfc-138">Aby dodać bibliotekę do istniejącego projektu, użyj [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) polecenia w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="79cfc-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="79cfc-139">W poniższym przykładzie RCL jest dodawany do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="79cfc-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="79cfc-140">Wykonaj następujące polecenie z folderu projektu aplikacji z ścieżką do biblioteki:</span><span class="sxs-lookup"><span data-stu-id="79cfc-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="79cfc-141">Korzystanie ze składnika biblioteki</span><span class="sxs-lookup"><span data-stu-id="79cfc-141">Consume a library component</span></span>

<span data-ttu-id="79cfc-142">Aby można było korzystać ze składników zdefiniowanych w bibliotece w innym projekcie, należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="79cfc-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="79cfc-143">Użyj pełnej nazwy typu z przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="79cfc-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="79cfc-144">Zastosuj Razor [`@using`](xref:mvc/views/razor#using) dyrektywę.</span><span class="sxs-lookup"><span data-stu-id="79cfc-144">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="79cfc-145">Poszczególne składniki można dodawać według nazwy.</span><span class="sxs-lookup"><span data-stu-id="79cfc-145">Individual components can be added by name.</span></span>

<span data-ttu-id="79cfc-146">W poniższych przykładach `MyComponentLib1` jest biblioteka składników zawierająca `SalesReport` składnik.</span><span class="sxs-lookup"><span data-stu-id="79cfc-146">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="79cfc-147">Do `SalesReport` składnika można odwoływać się za pomocą jego pełnej nazwy typu z przestrzenią nazw:</span><span class="sxs-lookup"><span data-stu-id="79cfc-147">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="79cfc-148">Składnik może być również przywoływany, jeśli biblioteka została wprowadzona do zakresu przy użyciu `@using` dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="79cfc-148">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="79cfc-149">Uwzględnij `@using MyComponentLib1` dyrektywę w pliku najwyższego poziomu, `_Import.razor` Aby udostępnić składniki biblioteki dla całego projektu.</span><span class="sxs-lookup"><span data-stu-id="79cfc-149">Include the `@using MyComponentLib1` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="79cfc-150">Dodaj dyrektywę do `_Import.razor` pliku na dowolnym poziomie, aby zastosować przestrzeń nazw do pojedynczej strony lub zestawu stron w ramach folderu.</span><span class="sxs-lookup"><span data-stu-id="79cfc-150">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="79cfc-151">Tworzenie Razor biblioteki klas składników ze statycznymi zasobami</span><span class="sxs-lookup"><span data-stu-id="79cfc-151">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="79cfc-152">RCL może zawierać statyczne zasoby.</span><span class="sxs-lookup"><span data-stu-id="79cfc-152">An RCL can include static assets.</span></span> <span data-ttu-id="79cfc-153">Zasoby statyczne są dostępne dla każdej aplikacji, która korzysta z biblioteki.</span><span class="sxs-lookup"><span data-stu-id="79cfc-153">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="79cfc-154">Aby uzyskać więcej informacji, zobacz <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="79cfc-154">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="79cfc-155">Kompilowanie, pakowanie i dostarczanie do narzędzia NuGet</span><span class="sxs-lookup"><span data-stu-id="79cfc-155">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="79cfc-156">Ponieważ biblioteki składników są standardowymi bibliotekami .NET, pakowanie i dostarczanie ich do narzędzia NuGet nie różni się od pakowania i wysyłania żadnej biblioteki do narzędzia NuGet.</span><span class="sxs-lookup"><span data-stu-id="79cfc-156">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="79cfc-157">Pakowanie jest wykonywane przy użyciu [`dotnet pack`](/dotnet/core/tools/dotnet-pack) polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="79cfc-157">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="79cfc-158">Przekaż pakiet do narzędzia NuGet przy użyciu [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) polecenia w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="79cfc-158">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="79cfc-159">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="79cfc-159">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="79cfc-160">Dodawanie pliku konfiguracji konsolidatora XML do biblioteki</span><span class="sxs-lookup"><span data-stu-id="79cfc-160">Add an XML linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
