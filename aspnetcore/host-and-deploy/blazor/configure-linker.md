---
title: Konfigurowanie konsolidatora dla ASP.NET CoreBlazor
author: guardrex
description: Dowiedz się, jak sterować konsolidatorem języka Blazor pośredniego podczas tworzenia aplikacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: 109da5ef400c3b9d64ccf3ceb33a5387ea6b5618
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218664"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="ba4ac-103">Konfigurowanie konsolidatora dla ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="ba4ac-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="ba4ac-104">Przez [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ba4ac-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="ba4ac-105">Blazor WebAssembly wykonuje [intermediate language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) łączenie podczas kompilacji, aby przyciąć niepotrzebne IL z zestawów wyjściowych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ba4ac-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="ba4ac-106">Konsolidator jest wyłączony podczas tworzenia w konfiguracji debugowania.</span><span class="sxs-lookup"><span data-stu-id="ba4ac-106">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="ba4ac-107">Aplikacje muszą tworzyć w konfiguracji wersji, aby włączyć konsolidator.</span><span class="sxs-lookup"><span data-stu-id="ba4ac-107">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="ba4ac-108">Zalecamy tworzenie w wersji podczas wdrażania aplikacji Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="ba4ac-108">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="ba4ac-109">Łączenie aplikacji optymalizuje rozmiar, ale może mieć szkodliwe skutki.</span><span class="sxs-lookup"><span data-stu-id="ba4ac-109">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="ba4ac-110">Aplikacje, które używają odbicia lub powiązanych funkcji dynamicznych może pęknąć po przycięciu, ponieważ konsolidator nie wie o tym dynamicznym zachowaniu i nie można określić ogólnie, które typy są wymagane do odbicia w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="ba4ac-110">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="ba4ac-111">Aby przyciąć takie aplikacje, konsolidator musi być informowany o wszystkich typach wymaganych przez odbicie w kodzie i w pakietach lub strukturach, od których zależy aplikacja.</span><span class="sxs-lookup"><span data-stu-id="ba4ac-111">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="ba4ac-112">Aby upewnić się, że przycięta aplikacja działa poprawnie po wdrożeniu, ważne jest, aby testować kompilacje wydania aplikacji często podczas tworzenia.</span><span class="sxs-lookup"><span data-stu-id="ba4ac-112">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="ba4ac-113">Łączenie dla aplikacji Blazor można skonfigurować przy użyciu następujących funkcji MSBuild:</span><span class="sxs-lookup"><span data-stu-id="ba4ac-113">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="ba4ac-114">Konfigurowanie łączenia globalnie z [właściwością MSBuild](#control-linking-with-an-msbuild-property).</span><span class="sxs-lookup"><span data-stu-id="ba4ac-114">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="ba4ac-115">Sterowanie łączeniem na podstawie na zespół z [plikiem konfiguracyjnym](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="ba4ac-115">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="ba4ac-116">Łączenie formantów z właściwością MSBuild</span><span class="sxs-lookup"><span data-stu-id="ba4ac-116">Control linking with an MSBuild property</span></span>

<span data-ttu-id="ba4ac-117">Łączenie jest włączone, gdy aplikacja `Release` jest wbudowana w konfigurację.</span><span class="sxs-lookup"><span data-stu-id="ba4ac-117">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="ba4ac-118">Aby to zmienić, `BlazorWebAssemblyEnableLinking` skonfiguruj właściwość MSBuild w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="ba4ac-118">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="ba4ac-119">Sterowanie łączem z plikiem konfiguracyjnym</span><span class="sxs-lookup"><span data-stu-id="ba4ac-119">Control linking with a configuration file</span></span>

<span data-ttu-id="ba4ac-120">Kontroluj łączenie na podstawie naze zespołu, udostępniając plik konfiguracyjny XML i określając plik jako element MSBuild w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="ba4ac-120">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="ba4ac-121">*LinkerConfig.xml*:</span><span class="sxs-lookup"><span data-stu-id="ba4ac-121">*LinkerConfig.xml*:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

<span data-ttu-id="ba4ac-122">Aby uzyskać więcej informacji, zobacz [Łączenie przykładów plików xml (repozytorium GitHub mono/linker).](https://github.com/mono/linker#link-xml-file-examples)</span><span class="sxs-lookup"><span data-stu-id="ba4ac-122">For more information, see [Link xml file examples (mono/linker GitHub repository)](https://github.com/mono/linker#link-xml-file-examples).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="ba4ac-123">Dodawanie pliku konfiguracyjnego konsolidatora XML do biblioteki</span><span class="sxs-lookup"><span data-stu-id="ba4ac-123">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="ba4ac-124">Aby skonfigurować konsolidator dla określonej biblioteki, dodaj plik konfiguracyjny konsolidatora XML do biblioteki jako zasób osadzony.</span><span class="sxs-lookup"><span data-stu-id="ba4ac-124">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="ba4ac-125">Osadzony zasób musi mieć taką samą nazwę jak zestaw.</span><span class="sxs-lookup"><span data-stu-id="ba4ac-125">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="ba4ac-126">W poniższym przykładzie plik *LinkerConfig.xml* jest określony jako osadzony zasób, który ma taką samą nazwę jak zestaw biblioteki:</span><span class="sxs-lookup"><span data-stu-id="ba4ac-126">In the following example, the *LinkerConfig.xml* file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="ba4ac-127">Konfigurowanie konsolidatora do internacjonalizacji</span><span class="sxs-lookup"><span data-stu-id="ba4ac-127">Configure the linker for internationalization</span></span>

<span data-ttu-id="ba4ac-128">Domyślnie konfiguracja konsolidatora Blazora dla aplikacji Blazor WebAssembly usuwa informacje o internacjonalizacji, z wyjątkiem wyraźnie żądanych ustawień regionalnych.</span><span class="sxs-lookup"><span data-stu-id="ba4ac-128">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="ba4ac-129">Usunięcie tych zestawów minimalizuje rozmiar aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ba4ac-129">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="ba4ac-130">Aby kontrolować, które zestawy I18N są `<MonoLinkerI18NAssemblies>` zachowywane, ustaw właściwość MSBuild w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="ba4ac-130">To control which I18N assemblies are retained, set the `<MonoLinkerI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="ba4ac-131">Wartość regionu</span><span class="sxs-lookup"><span data-stu-id="ba4ac-131">Region Value</span></span>     | <span data-ttu-id="ba4ac-132">Montaż regionu mono</span><span class="sxs-lookup"><span data-stu-id="ba4ac-132">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="ba4ac-133">Wszystkie zestawy w zestawie</span><span class="sxs-lookup"><span data-stu-id="ba4ac-133">All assemblies included</span></span> |
| `cjk`            | <span data-ttu-id="ba4ac-134">*I18N.CJK.dll*</span><span class="sxs-lookup"><span data-stu-id="ba4ac-134">*I18N.CJK.dll*</span></span>          |
| `mideast`        | <span data-ttu-id="ba4ac-135">*I18N.MidEast.dll*</span><span class="sxs-lookup"><span data-stu-id="ba4ac-135">*I18N.MidEast.dll*</span></span>      |
| <span data-ttu-id="ba4ac-136">`none`(domyślnie)</span><span class="sxs-lookup"><span data-stu-id="ba4ac-136">`none` (default)</span></span> | <span data-ttu-id="ba4ac-137">Brak</span><span class="sxs-lookup"><span data-stu-id="ba4ac-137">None</span></span>                    |
| `other`          | <span data-ttu-id="ba4ac-138">*I18N.Other.dll*</span><span class="sxs-lookup"><span data-stu-id="ba4ac-138">*I18N.Other.dll*</span></span>        |
| `rare`           | <span data-ttu-id="ba4ac-139">*I18N.Rare.dll*</span><span class="sxs-lookup"><span data-stu-id="ba4ac-139">*I18N.Rare.dll*</span></span>         |
| `west`           | <span data-ttu-id="ba4ac-140">*I18N.West.dll*</span><span class="sxs-lookup"><span data-stu-id="ba4ac-140">*I18N.West.dll*</span></span>         |

<span data-ttu-id="ba4ac-141">Użyj przecinka, aby oddzielić `mideast,west`wiele wartości (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="ba4ac-141">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="ba4ac-142">Aby uzyskać więcej informacji, zobacz [I18N: Pnetlib Internationalization Framework Library (mon/mono Repozytorium GitHub).](https://github.com/mono/mono/tree/master/mcs/class/I18N)</span><span class="sxs-lookup"><span data-stu-id="ba4ac-142">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>
