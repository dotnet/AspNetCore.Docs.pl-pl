---
title: ASP.NET biblioteki klas składników Razor Core
author: guardrex
description: Dowiedz się, jak składniki Blazor mogą być uwzględniane w aplikacjach z zewnętrznej biblioteki składników.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: f2cc57638922bd1f6ab036adb2ed37209d14c5b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218769"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="b2e22-103">ASP.NET biblioteki klas składników Razor Core</span><span class="sxs-lookup"><span data-stu-id="b2e22-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="b2e22-104">Przez [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="b2e22-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="b2e22-105">Składniki mogą być współużytkowane w [bibliotece klas Razor (RCL)](xref:razor-pages/ui-class) między projektami.</span><span class="sxs-lookup"><span data-stu-id="b2e22-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="b2e22-106">*Biblioteka klas komponentów razor* może być dołączona z:</span><span class="sxs-lookup"><span data-stu-id="b2e22-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="b2e22-107">Kolejny projekt w rozwiązaniu.</span><span class="sxs-lookup"><span data-stu-id="b2e22-107">Another project in the solution.</span></span>
* <span data-ttu-id="b2e22-108">Pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="b2e22-108">A NuGet package.</span></span>
* <span data-ttu-id="b2e22-109">Dodowano do biblioteki .NET.</span><span class="sxs-lookup"><span data-stu-id="b2e22-109">A referenced .NET library.</span></span>

<span data-ttu-id="b2e22-110">Podobnie jak składniki są zwykłymi typami .NET, komponenty dostarczane przez rcl są normalnymi złożeniami .NET.</span><span class="sxs-lookup"><span data-stu-id="b2e22-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="b2e22-111">Tworzenie listy RCL</span><span class="sxs-lookup"><span data-stu-id="b2e22-111">Create an RCL</span></span>

<span data-ttu-id="b2e22-112">Postępuj zgodnie <xref:blazor/get-started> ze wskazówkami w artykule, aby skonfigurować środowisko dla Blazor.</span><span class="sxs-lookup"><span data-stu-id="b2e22-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2e22-113">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2e22-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b2e22-114">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="b2e22-114">Create a new project.</span></span>
1. <span data-ttu-id="b2e22-115">Wybierz **bibliotekę klas Razor**.</span><span class="sxs-lookup"><span data-stu-id="b2e22-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="b2e22-116">Wybierz **pozycję Dalej**.</span><span class="sxs-lookup"><span data-stu-id="b2e22-116">Select **Next**.</span></span>
1. <span data-ttu-id="b2e22-117">W oknie **dialogowym Tworzenie nowej biblioteki klas Razor** wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="b2e22-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="b2e22-118">Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj domyślną nazwę projektu.</span><span class="sxs-lookup"><span data-stu-id="b2e22-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="b2e22-119">W przykładach w tym temacie `MyComponentLib1`użyto nazwy projektu .</span><span class="sxs-lookup"><span data-stu-id="b2e22-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="b2e22-120">Wybierz **pozycję Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="b2e22-120">Select **Create**.</span></span>
1. <span data-ttu-id="b2e22-121">Dodaj RCL do rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="b2e22-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="b2e22-122">Kliknij prawym przyciskiem myszy rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="b2e22-122">Right-click the solution.</span></span> <span data-ttu-id="b2e22-123">Wybierz **pozycję Dodaj** > **istniejący projekt**.</span><span class="sxs-lookup"><span data-stu-id="b2e22-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="b2e22-124">Przejdź do pliku projektu RCL.</span><span class="sxs-lookup"><span data-stu-id="b2e22-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="b2e22-125">Wybierz plik projektu RCL (*.csproj*).</span><span class="sxs-lookup"><span data-stu-id="b2e22-125">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="b2e22-126">Dodaj odwołanie do listy RCL z aplikacji:</span><span class="sxs-lookup"><span data-stu-id="b2e22-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="b2e22-127">Kliknij prawym przyciskiem myszy projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b2e22-127">Right-click the app project.</span></span> <span data-ttu-id="b2e22-128">Wybierz **pozycję Dodaj** > **odniesienie**.</span><span class="sxs-lookup"><span data-stu-id="b2e22-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="b2e22-129">Wybierz projekt RCL.</span><span class="sxs-lookup"><span data-stu-id="b2e22-129">Select the RCL project.</span></span> <span data-ttu-id="b2e22-130">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="b2e22-130">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="b2e22-131">Jeśli podczas generowania listy RCL z szablonu jest zaznaczone pole wyboru **Strony pomocy technicznej i widoki,** dodaj również plik *_Imports.razor* do katalogu głównego wygenerowanego projektu z następującą zawartością, aby umożliwić tworzenie komponentów Razor:</span><span class="sxs-lookup"><span data-stu-id="b2e22-131">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="b2e22-132">Ręcznie dodaj plik główny wygenerowanego projektu.</span><span class="sxs-lookup"><span data-stu-id="b2e22-132">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b2e22-133">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="b2e22-133">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="b2e22-134">Użyj szablonu **Biblioteka klas Razor** (`razorclasslib`) z nowym poleceniem [dotnet](/dotnet/core/tools/dotnet-new) w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="b2e22-134">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="b2e22-135">W poniższym przykładzie tworzony jest `MyComponentLib1`rcl o nazwie .</span><span class="sxs-lookup"><span data-stu-id="b2e22-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="b2e22-136">Folder, który `MyComponentLib1` posiada jest tworzony automatycznie po wykonaniu polecenia:</span><span class="sxs-lookup"><span data-stu-id="b2e22-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > <span data-ttu-id="b2e22-137">Jeśli `-s|--support-pages-and-views` przełącznik jest używany podczas generowania RCL z szablonu, a następnie również dodać plik *_Imports.razor* do katalogu głównego wygenerowanego projektu z następującą zawartością, aby umożliwić tworzenie komponentów Razor:</span><span class="sxs-lookup"><span data-stu-id="b2e22-137">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="b2e22-138">Ręcznie dodaj plik główny wygenerowanego projektu.</span><span class="sxs-lookup"><span data-stu-id="b2e22-138">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="b2e22-139">Aby dodać bibliotekę do istniejącego projektu, użyj polecenia [dotnet dodaj odwołanie](/dotnet/core/tools/dotnet-add-reference) w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="b2e22-139">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="b2e22-140">W poniższym przykładzie RCL jest dodawany do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b2e22-140">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="b2e22-141">Wykonaj następujące polecenie z folderu projektu aplikacji ze ścieżką do biblioteki:</span><span class="sxs-lookup"><span data-stu-id="b2e22-141">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="b2e22-142">Korzystanie ze składnika biblioteki</span><span class="sxs-lookup"><span data-stu-id="b2e22-142">Consume a library component</span></span>

<span data-ttu-id="b2e22-143">Aby korzystać ze składników zdefiniowanych w bibliotece w innym projekcie, należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="b2e22-143">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="b2e22-144">Użyj pełnej nazwy typu z obszarem nazw.</span><span class="sxs-lookup"><span data-stu-id="b2e22-144">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="b2e22-145">Użyj dyrektywy Razor [ \@przy użyciu.](xref:mvc/views/razor#using)</span><span class="sxs-lookup"><span data-stu-id="b2e22-145">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="b2e22-146">Poszczególne komponenty można dodawać według nazwy.</span><span class="sxs-lookup"><span data-stu-id="b2e22-146">Individual components can be added by name.</span></span>

<span data-ttu-id="b2e22-147">W poniższych przykładach `MyComponentLib1` jest biblioteka `SalesReport` składników zawierająca składnik.</span><span class="sxs-lookup"><span data-stu-id="b2e22-147">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="b2e22-148">Do `SalesReport` składnika można odwoływać się przy użyciu jego pełnej nazwy typu z obszarem nazw:</span><span class="sxs-lookup"><span data-stu-id="b2e22-148">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="b2e22-149">Składnik można również odwoływać się, jeśli biblioteka `@using` jest wniesiona do zakresu z dyrektywą:</span><span class="sxs-lookup"><span data-stu-id="b2e22-149">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="b2e22-150">Dołącz `@using MyComponentLib1` dyrektywę do pliku *_Import.brzytwa* najwyższego poziomu, aby udostępnić składniki biblioteki całemu projektowi.</span><span class="sxs-lookup"><span data-stu-id="b2e22-150">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="b2e22-151">Dodaj dyrektywę do pliku *_Import.razor* na dowolnym poziomie, aby zastosować obszar nazw do pojedynczej strony lub zestawu stron w folderze.</span><span class="sxs-lookup"><span data-stu-id="b2e22-151">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="b2e22-152">Tworzenie biblioteki klas komponentów Razor z zasobami statycznymi</span><span class="sxs-lookup"><span data-stu-id="b2e22-152">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="b2e22-153">Lista RCL może zawierać zasoby statyczne.</span><span class="sxs-lookup"><span data-stu-id="b2e22-153">An RCL can include static assets.</span></span> <span data-ttu-id="b2e22-154">Zasoby statyczne są dostępne dla każdej aplikacji, która zużywa bibliotekę.</span><span class="sxs-lookup"><span data-stu-id="b2e22-154">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="b2e22-155">Aby uzyskać więcej informacji, zobacz <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="b2e22-155">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="b2e22-156">Buduj, pakuj i wysyłaj do NuGet</span><span class="sxs-lookup"><span data-stu-id="b2e22-156">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="b2e22-157">Ponieważ biblioteki składników są standardowe biblioteki .NET, pakowania i wysyłania ich do NuGet nie różni się od pakowania i wysyłki dowolnej biblioteki do NuGet.</span><span class="sxs-lookup"><span data-stu-id="b2e22-157">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="b2e22-158">Pakowanie odbywa się za pomocą polecenia [dotnet pack](/dotnet/core/tools/dotnet-pack) w powłoce polecenia:</span><span class="sxs-lookup"><span data-stu-id="b2e22-158">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="b2e22-159">Przekaż pakiet do NuGet przy użyciu polecenia [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="b2e22-159">Upload the package to NuGet using the [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b2e22-160">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="b2e22-160">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="b2e22-161">Dodawanie pliku konfiguracyjnego konsolidatora XML do biblioteki</span><span class="sxs-lookup"><span data-stu-id="b2e22-161">Add an XML linker configuration file to a library</span></span>](xref:host-and-deploy/blazor/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
