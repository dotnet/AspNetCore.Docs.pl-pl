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
ms.openlocfilehash: b54eb7142fc7e1665cc0aaaad068a67852ac2f74
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399078"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="aed69-103">RazorBiblioteki klas składników ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aed69-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="aed69-104">Autor [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="aed69-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="aed69-105">Składniki mogą być współużytkowane w [ Razor bibliotece klas (RCL)](xref:razor-pages/ui-class) w różnych projektach.</span><span class="sxs-lookup"><span data-stu-id="aed69-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="aed69-106">\* Razor Biblioteka klas składników\* może być dołączana z:</span><span class="sxs-lookup"><span data-stu-id="aed69-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="aed69-107">Inny projekt w rozwiązaniu.</span><span class="sxs-lookup"><span data-stu-id="aed69-107">Another project in the solution.</span></span>
* <span data-ttu-id="aed69-108">Pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="aed69-108">A NuGet package.</span></span>
* <span data-ttu-id="aed69-109">Przywoływana Biblioteka platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="aed69-109">A referenced .NET library.</span></span>

<span data-ttu-id="aed69-110">Podobnie jak składniki są zwykłymi typami .NET, składniki udostępniane przez RCL są normalnymi zestawami platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="aed69-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="aed69-111">Utwórz RCL</span><span class="sxs-lookup"><span data-stu-id="aed69-111">Create an RCL</span></span>

<span data-ttu-id="aed69-112">Postępuj zgodnie ze wskazówkami zawartymi w <xref:blazor/get-started> artykule, aby skonfigurować środowisko dla programu Blazor .</span><span class="sxs-lookup"><span data-stu-id="aed69-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aed69-113">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aed69-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="aed69-114">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="aed69-114">Create a new project.</span></span>
1. <span data-ttu-id="aed69-115">Wybierz \*\* Razor bibliotekę klas\*\*.</span><span class="sxs-lookup"><span data-stu-id="aed69-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="aed69-116">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="aed69-116">Select **Next**.</span></span>
1. <span data-ttu-id="aed69-117">W oknie dialogowym **Utwórz nową Razor bibliotekę klas** wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="aed69-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="aed69-118">Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj nazwę domyślną projektu.</span><span class="sxs-lookup"><span data-stu-id="aed69-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="aed69-119">W przykładach w tym temacie użyto nazwy projektu `MyComponentLib1` .</span><span class="sxs-lookup"><span data-stu-id="aed69-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="aed69-120">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="aed69-120">Select **Create**.</span></span>
1. <span data-ttu-id="aed69-121">Dodaj RCL do rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="aed69-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="aed69-122">Kliknij prawym przyciskiem myszy rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="aed69-122">Right-click the solution.</span></span> <span data-ttu-id="aed69-123">Wybierz pozycję **Dodaj**  >  **istniejący projekt**.</span><span class="sxs-lookup"><span data-stu-id="aed69-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="aed69-124">Przejdź do pliku projektu RCL.</span><span class="sxs-lookup"><span data-stu-id="aed69-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="aed69-125">Wybierz plik projektu RCL ( `.csproj` ).</span><span class="sxs-lookup"><span data-stu-id="aed69-125">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="aed69-126">Dodaj odwołanie RCL z aplikacji:</span><span class="sxs-lookup"><span data-stu-id="aed69-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="aed69-127">Kliknij prawym przyciskiem myszy projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="aed69-127">Right-click the app project.</span></span> <span data-ttu-id="aed69-128">Wybierz pozycję **Dodaj**  >  **odwołanie**.</span><span class="sxs-lookup"><span data-stu-id="aed69-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="aed69-129">Wybierz projekt RCL.</span><span class="sxs-lookup"><span data-stu-id="aed69-129">Select the RCL project.</span></span> <span data-ttu-id="aed69-130">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="aed69-130">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="aed69-131">Jeśli pole wyboru **strony i widoki pomocy technicznej** jest zaznaczone podczas generowania RCL z szablonu, Dodaj również `_Imports.razor` plik do katalogu głównego wygenerowanego projektu z następującą zawartością, aby włączyć Razor Tworzenie składników:</span><span class="sxs-lookup"><span data-stu-id="aed69-131">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="aed69-132">Ręcznie Dodaj plik do katalogu głównego wygenerowanego projektu.</span><span class="sxs-lookup"><span data-stu-id="aed69-132">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="aed69-133">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="aed69-133">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="aed69-134">Użyj szablonu \*\* Razor biblioteki klas\*\* ( `razorclasslib` ) za pomocą [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenia w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="aed69-134">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="aed69-135">W poniższym przykładzie jest tworzony RCL o nazwie `MyComponentLib1` .</span><span class="sxs-lookup"><span data-stu-id="aed69-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="aed69-136">Folder, który `MyComponentLib1` ma zostać utworzony, jest tworzony automatycznie podczas wykonywania polecenia:</span><span class="sxs-lookup"><span data-stu-id="aed69-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > <span data-ttu-id="aed69-137">Jeśli `-s|--support-pages-and-views` przełącznik jest używany podczas generowania RCL z szablonu, Dodaj również `_Imports.razor` plik do katalogu głównego wygenerowanego projektu z następującą zawartością, aby włączyć Razor Tworzenie składników:</span><span class="sxs-lookup"><span data-stu-id="aed69-137">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="aed69-138">Ręcznie Dodaj plik do katalogu głównego wygenerowanego projektu.</span><span class="sxs-lookup"><span data-stu-id="aed69-138">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="aed69-139">Aby dodać bibliotekę do istniejącego projektu, użyj [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) polecenia w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="aed69-139">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="aed69-140">W poniższym przykładzie RCL jest dodawany do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="aed69-140">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="aed69-141">Wykonaj następujące polecenie z folderu projektu aplikacji z ścieżką do biblioteki:</span><span class="sxs-lookup"><span data-stu-id="aed69-141">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="aed69-142">Korzystanie ze składnika biblioteki</span><span class="sxs-lookup"><span data-stu-id="aed69-142">Consume a library component</span></span>

<span data-ttu-id="aed69-143">Aby można było korzystać ze składników zdefiniowanych w bibliotece w innym projekcie, należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="aed69-143">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="aed69-144">Użyj pełnej nazwy typu z przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="aed69-144">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="aed69-145">Zastosuj Razor [`@using`](xref:mvc/views/razor#using) dyrektywę.</span><span class="sxs-lookup"><span data-stu-id="aed69-145">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="aed69-146">Poszczególne składniki można dodawać według nazwy.</span><span class="sxs-lookup"><span data-stu-id="aed69-146">Individual components can be added by name.</span></span>

<span data-ttu-id="aed69-147">W poniższych przykładach `MyComponentLib1` jest biblioteka składników zawierająca `SalesReport` składnik.</span><span class="sxs-lookup"><span data-stu-id="aed69-147">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="aed69-148">Do `SalesReport` składnika można odwoływać się za pomocą jego pełnej nazwy typu z przestrzenią nazw:</span><span class="sxs-lookup"><span data-stu-id="aed69-148">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="aed69-149">Składnik może być również przywoływany, jeśli biblioteka została wprowadzona do zakresu przy użyciu `@using` dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="aed69-149">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="aed69-150">Uwzględnij `@using MyComponentLib1` dyrektywę w pliku najwyższego poziomu, `_Import.razor` Aby udostępnić składniki biblioteki dla całego projektu.</span><span class="sxs-lookup"><span data-stu-id="aed69-150">Include the `@using MyComponentLib1` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="aed69-151">Dodaj dyrektywę do `_Import.razor` pliku na dowolnym poziomie, aby zastosować przestrzeń nazw do pojedynczej strony lub zestawu stron w ramach folderu.</span><span class="sxs-lookup"><span data-stu-id="aed69-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="aed69-152">Tworzenie Razor biblioteki klas składników ze statycznymi zasobami</span><span class="sxs-lookup"><span data-stu-id="aed69-152">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="aed69-153">RCL może zawierać statyczne zasoby.</span><span class="sxs-lookup"><span data-stu-id="aed69-153">An RCL can include static assets.</span></span> <span data-ttu-id="aed69-154">Zasoby statyczne są dostępne dla każdej aplikacji, która korzysta z biblioteki.</span><span class="sxs-lookup"><span data-stu-id="aed69-154">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="aed69-155">Aby uzyskać więcej informacji, zobacz <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="aed69-155">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="aed69-156">Kompilowanie, pakowanie i dostarczanie do narzędzia NuGet</span><span class="sxs-lookup"><span data-stu-id="aed69-156">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="aed69-157">Ponieważ biblioteki składników są standardowymi bibliotekami .NET, pakowanie i dostarczanie ich do narzędzia NuGet nie różni się od pakowania i wysyłania żadnej biblioteki do narzędzia NuGet.</span><span class="sxs-lookup"><span data-stu-id="aed69-157">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="aed69-158">Pakowanie jest wykonywane przy użyciu [`dotnet pack`](/dotnet/core/tools/dotnet-pack) polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="aed69-158">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="aed69-159">Przekaż pakiet do narzędzia NuGet przy użyciu [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) polecenia w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="aed69-159">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aed69-160">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="aed69-160">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="aed69-161">Dodawanie pliku konfiguracji konsolidatora XML do biblioteki</span><span class="sxs-lookup"><span data-stu-id="aed69-161">Add an XML linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
