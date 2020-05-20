---
<span data-ttu-id="180e6-101">title: "Skonfiguruj konsolidator dla ASP.NET Core Blazor " Author: Description: "Dowiedz się, jak kontrolować konsolidator języka pośredniego (IL) podczas kompilowania Blazor aplikacji".</span><span class="sxs-lookup"><span data-stu-id="180e6-101">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="180e6-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="180e6-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="180e6-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="180e6-103">'Blazor'</span></span>
- <span data-ttu-id="180e6-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="180e6-104">'Identity'</span></span>
- <span data-ttu-id="180e6-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="180e6-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="180e6-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="180e6-106">'Razor'</span></span>
- <span data-ttu-id="180e6-107">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="180e6-107">'SignalR' uid:</span></span> 

---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="180e6-108">Skonfiguruj konsolidator dla ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="180e6-108">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="180e6-109">Autor [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="180e6-109">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor<span data-ttu-id="180e6-110">Zestaw webassembly wykonuje konsolidację [języka pośredniego (IL)](/dotnet/standard/managed-code#intermediate-language--execution) podczas kompilacji, aby przyciąć niepotrzebny kod IL z zestawów wyjściowych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="180e6-110"> WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="180e6-111">Konsolidator jest wyłączony podczas kompilowania w konfiguracji debugowania.</span><span class="sxs-lookup"><span data-stu-id="180e6-111">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="180e6-112">Aplikacje muszą kompilować w konfiguracji wydania, aby umożliwić konsolidator.</span><span class="sxs-lookup"><span data-stu-id="180e6-112">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="180e6-113">Zalecamy Kompilowanie w wersji podczas wdrażania Blazor aplikacji webassembly.</span><span class="sxs-lookup"><span data-stu-id="180e6-113">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="180e6-114">Łączenie aplikacji jest zoptymalizowane pod kątem rozmiaru, ale mogą one mieć szkodliwe skutki.</span><span class="sxs-lookup"><span data-stu-id="180e6-114">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="180e6-115">Aplikacje korzystające z odbicia lub powiązane funkcje dynamiczne mogą być przerywane po przycięciu, ponieważ konsolidator nie wie o tym zachowaniu dynamicznym i nie może w ogóle określić, które typy są wymagane do odbicia w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="180e6-115">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="180e6-116">Aby przyciąć takie aplikacje, konsolidator musi być informowany o wszelkich typach wymaganych przez odbicie w kodzie oraz w pakietach lub strukturach, od których zależy aplikacja.</span><span class="sxs-lookup"><span data-stu-id="180e6-116">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="180e6-117">Aby upewnić się, że przycięta aplikacja działa prawidłowo po wdrożeniu, ważne jest, aby przetestować kompilacje wydań aplikacji często podczas opracowywania.</span><span class="sxs-lookup"><span data-stu-id="180e6-117">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="180e6-118">Łączenie dla Blazor aplikacji można skonfigurować za pomocą tych funkcji MSBuild:</span><span class="sxs-lookup"><span data-stu-id="180e6-118">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="180e6-119">Skonfiguruj konsolidację globalnie za pomocą [Właściwości programu MSBuild](#control-linking-with-an-msbuild-property).</span><span class="sxs-lookup"><span data-stu-id="180e6-119">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="180e6-120">Kontrolowanie łączenia poszczególnych zestawów z [plikiem konfiguracyjnym](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="180e6-120">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="180e6-121">Sterowanie łączeniem z właściwością programu MSBuild</span><span class="sxs-lookup"><span data-stu-id="180e6-121">Control linking with an MSBuild property</span></span>

<span data-ttu-id="180e6-122">Łączenie jest włączone, gdy aplikacja jest wbudowana w `Release` konfigurację.</span><span class="sxs-lookup"><span data-stu-id="180e6-122">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="180e6-123">Aby to zmienić, skonfiguruj `BlazorWebAssemblyEnableLinking` Właściwość programu MSBuild w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="180e6-123">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="180e6-124">Kontrola łączenia z plikiem konfiguracji</span><span class="sxs-lookup"><span data-stu-id="180e6-124">Control linking with a configuration file</span></span>

<span data-ttu-id="180e6-125">Kontroluj łączenie dla poszczególnych zestawów, dostarczając plik konfiguracyjny XML i określając plik jako element programu MSBuild w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="180e6-125">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="180e6-126">*LinkerConfig. XML*:</span><span class="sxs-lookup"><span data-stu-id="180e6-126">*LinkerConfig.xml*:</span></span>

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

<span data-ttu-id="180e6-127">Aby uzyskać więcej informacji i przykładów, zobacz [formaty danych (repozytorium w serwisie GitHub/konsolidator)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span><span class="sxs-lookup"><span data-stu-id="180e6-127">For more information and examples, see [Data Formats (mono/linker GitHub repository)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="180e6-128">Dodawanie pliku konfiguracji konsolidatora XML do biblioteki</span><span class="sxs-lookup"><span data-stu-id="180e6-128">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="180e6-129">Aby skonfigurować konsolidator dla określonej biblioteki, Dodaj plik konfiguracji konsolidatora XML do biblioteki jako zasób osadzony.</span><span class="sxs-lookup"><span data-stu-id="180e6-129">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="180e6-130">Osadzony zasób musi mieć taką samą nazwę jak zestaw.</span><span class="sxs-lookup"><span data-stu-id="180e6-130">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="180e6-131">W poniższym przykładzie plik *LinkerConfig. XML* jest określony jako zasób osadzony, który ma taką samą nazwę jak zestaw biblioteki:</span><span class="sxs-lookup"><span data-stu-id="180e6-131">In the following example, the *LinkerConfig.xml* file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="180e6-132">Konfigurowanie konsolidatora dla celów wielojęzycznych</span><span class="sxs-lookup"><span data-stu-id="180e6-132">Configure the linker for internationalization</span></span>

<span data-ttu-id="180e6-133">Domyślnie Blazor Konfiguracja konsolidatora dla aplikacji webassembly umożliwia rozłączenie Blazor informacji o danych wielojęzycznych z wyjątkiem lokalizacji lokalnych jawnie żądanych.</span><span class="sxs-lookup"><span data-stu-id="180e6-133">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="180e6-134">Usunięcie tych zestawów minimalizuje rozmiar aplikacji.</span><span class="sxs-lookup"><span data-stu-id="180e6-134">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="180e6-135">Aby kontrolować, które zestawy I18N są zachowywane, ustaw `<BlazorWebAssemblyI18NAssemblies>` Właściwość programu MSBuild w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="180e6-135">To control which I18N assemblies are retained, set the `<BlazorWebAssemblyI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="180e6-136">Wartość regionu</span><span class="sxs-lookup"><span data-stu-id="180e6-136">Region Value</span></span>     | <span data-ttu-id="180e6-137">Zestaw regionów mono</span><span class="sxs-lookup"><span data-stu-id="180e6-137">Mono region assembly</span></span>    |
| ---
<span data-ttu-id="180e6-138">title: "Skonfiguruj konsolidator dla ASP.NET Core Blazor " Author: Description: "Dowiedz się, jak kontrolować konsolidator języka pośredniego (IL) podczas kompilowania Blazor aplikacji".</span><span class="sxs-lookup"><span data-stu-id="180e6-138">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="180e6-139">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="180e6-139">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="180e6-140">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="180e6-140">'Blazor'</span></span>
- <span data-ttu-id="180e6-141">'Identity'</span><span class="sxs-lookup"><span data-stu-id="180e6-141">'Identity'</span></span>
- <span data-ttu-id="180e6-142">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="180e6-142">'Let's Encrypt'</span></span>
- <span data-ttu-id="180e6-143">'Razor'</span><span class="sxs-lookup"><span data-stu-id="180e6-143">'Razor'</span></span>
- <span data-ttu-id="180e6-144">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="180e6-144">'SignalR' uid:</span></span> 

-
<span data-ttu-id="180e6-145">title: "Skonfiguruj konsolidator dla ASP.NET Core Blazor " Author: Description: "Dowiedz się, jak kontrolować konsolidator języka pośredniego (IL) podczas kompilowania Blazor aplikacji".</span><span class="sxs-lookup"><span data-stu-id="180e6-145">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="180e6-146">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="180e6-146">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="180e6-147">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="180e6-147">'Blazor'</span></span>
- <span data-ttu-id="180e6-148">'Identity'</span><span class="sxs-lookup"><span data-stu-id="180e6-148">'Identity'</span></span>
- <span data-ttu-id="180e6-149">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="180e6-149">'Let's Encrypt'</span></span>
- <span data-ttu-id="180e6-150">'Razor'</span><span class="sxs-lookup"><span data-stu-id="180e6-150">'Razor'</span></span>
- <span data-ttu-id="180e6-151">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="180e6-151">'SignalR' uid:</span></span> 

-
<span data-ttu-id="180e6-152">title: "Skonfiguruj konsolidator dla ASP.NET Core Blazor " Author: Description: "Dowiedz się, jak kontrolować konsolidator języka pośredniego (IL) podczas kompilowania Blazor aplikacji".</span><span class="sxs-lookup"><span data-stu-id="180e6-152">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="180e6-153">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="180e6-153">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="180e6-154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="180e6-154">'Blazor'</span></span>
- <span data-ttu-id="180e6-155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="180e6-155">'Identity'</span></span>
- <span data-ttu-id="180e6-156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="180e6-156">'Let's Encrypt'</span></span>
- <span data-ttu-id="180e6-157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="180e6-157">'Razor'</span></span>
- <span data-ttu-id="180e6-158">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="180e6-158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="180e6-159">title: "Skonfiguruj konsolidator dla ASP.NET Core Blazor " Author: Description: "Dowiedz się, jak kontrolować konsolidator języka pośredniego (IL) podczas kompilowania Blazor aplikacji".</span><span class="sxs-lookup"><span data-stu-id="180e6-159">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="180e6-160">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="180e6-160">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="180e6-161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="180e6-161">'Blazor'</span></span>
- <span data-ttu-id="180e6-162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="180e6-162">'Identity'</span></span>
- <span data-ttu-id="180e6-163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="180e6-163">'Let's Encrypt'</span></span>
- <span data-ttu-id="180e6-164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="180e6-164">'Razor'</span></span>
- <span data-ttu-id="180e6-165">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="180e6-165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="180e6-166">title: "Skonfiguruj konsolidator dla ASP.NET Core Blazor " Author: Description: "Dowiedz się, jak kontrolować konsolidator języka pośredniego (IL) podczas kompilowania Blazor aplikacji".</span><span class="sxs-lookup"><span data-stu-id="180e6-166">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="180e6-167">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="180e6-167">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="180e6-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="180e6-168">'Blazor'</span></span>
- <span data-ttu-id="180e6-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="180e6-169">'Identity'</span></span>
- <span data-ttu-id="180e6-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="180e6-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="180e6-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="180e6-171">'Razor'</span></span>
- <span data-ttu-id="180e6-172">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="180e6-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="180e6-173">title: "Skonfiguruj konsolidator dla ASP.NET Core Blazor " Author: Description: "Dowiedz się, jak kontrolować konsolidator języka pośredniego (IL) podczas kompilowania Blazor aplikacji".</span><span class="sxs-lookup"><span data-stu-id="180e6-173">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="180e6-174">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="180e6-174">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="180e6-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="180e6-175">'Blazor'</span></span>
- <span data-ttu-id="180e6-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="180e6-176">'Identity'</span></span>
- <span data-ttu-id="180e6-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="180e6-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="180e6-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="180e6-178">'Razor'</span></span>
- <span data-ttu-id="180e6-179">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="180e6-179">'SignalR' uid:</span></span> 

<span data-ttu-id="180e6-180">-------- | ---Tytuł: "Skonfiguruj konsolidator dla ASP.NET Core Blazor " Author: Description: "Dowiedz się, jak kontrolować konsolidator języka pośredniego (IL) podczas kompilowania Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="180e6-180">-------- | --- title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="180e6-181">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="180e6-181">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="180e6-182">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="180e6-182">'Blazor'</span></span>
- <span data-ttu-id="180e6-183">'Identity'</span><span class="sxs-lookup"><span data-stu-id="180e6-183">'Identity'</span></span>
- <span data-ttu-id="180e6-184">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="180e6-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="180e6-185">'Razor'</span><span class="sxs-lookup"><span data-stu-id="180e6-185">'Razor'</span></span>
- <span data-ttu-id="180e6-186">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="180e6-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="180e6-187">title: "Skonfiguruj konsolidator dla ASP.NET Core Blazor " Author: Description: "Dowiedz się, jak kontrolować konsolidator języka pośredniego (IL) podczas kompilowania Blazor aplikacji".</span><span class="sxs-lookup"><span data-stu-id="180e6-187">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="180e6-188">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="180e6-188">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="180e6-189">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="180e6-189">'Blazor'</span></span>
- <span data-ttu-id="180e6-190">'Identity'</span><span class="sxs-lookup"><span data-stu-id="180e6-190">'Identity'</span></span>
- <span data-ttu-id="180e6-191">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="180e6-191">'Let's Encrypt'</span></span>
- <span data-ttu-id="180e6-192">'Razor'</span><span class="sxs-lookup"><span data-stu-id="180e6-192">'Razor'</span></span>
- <span data-ttu-id="180e6-193">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="180e6-193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="180e6-194">title: "Skonfiguruj konsolidator dla ASP.NET Core Blazor " Author: Description: "Dowiedz się, jak kontrolować konsolidator języka pośredniego (IL) podczas kompilowania Blazor aplikacji".</span><span class="sxs-lookup"><span data-stu-id="180e6-194">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="180e6-195">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="180e6-195">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="180e6-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="180e6-196">'Blazor'</span></span>
- <span data-ttu-id="180e6-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="180e6-197">'Identity'</span></span>
- <span data-ttu-id="180e6-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="180e6-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="180e6-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="180e6-199">'Razor'</span></span>
- <span data-ttu-id="180e6-200">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="180e6-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="180e6-201">title: "Skonfiguruj konsolidator dla ASP.NET Core Blazor " Author: Description: "Dowiedz się, jak kontrolować konsolidator języka pośredniego (IL) podczas kompilowania Blazor aplikacji".</span><span class="sxs-lookup"><span data-stu-id="180e6-201">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="180e6-202">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="180e6-202">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="180e6-203">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="180e6-203">'Blazor'</span></span>
- <span data-ttu-id="180e6-204">'Identity'</span><span class="sxs-lookup"><span data-stu-id="180e6-204">'Identity'</span></span>
- <span data-ttu-id="180e6-205">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="180e6-205">'Let's Encrypt'</span></span>
- <span data-ttu-id="180e6-206">'Razor'</span><span class="sxs-lookup"><span data-stu-id="180e6-206">'Razor'</span></span>
- <span data-ttu-id="180e6-207">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="180e6-207">'SignalR' uid:</span></span> 

-
<span data-ttu-id="180e6-208">title: "Skonfiguruj konsolidator dla ASP.NET Core Blazor " Author: Description: "Dowiedz się, jak kontrolować konsolidator języka pośredniego (IL) podczas kompilowania Blazor aplikacji".</span><span class="sxs-lookup"><span data-stu-id="180e6-208">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="180e6-209">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="180e6-209">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="180e6-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="180e6-210">'Blazor'</span></span>
- <span data-ttu-id="180e6-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="180e6-211">'Identity'</span></span>
- <span data-ttu-id="180e6-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="180e6-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="180e6-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="180e6-213">'Razor'</span></span>
- <span data-ttu-id="180e6-214">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="180e6-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="180e6-215">title: "Skonfiguruj konsolidator dla ASP.NET Core Blazor " Author: Description: "Dowiedz się, jak kontrolować konsolidator języka pośredniego (IL) podczas kompilowania Blazor aplikacji".</span><span class="sxs-lookup"><span data-stu-id="180e6-215">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="180e6-216">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="180e6-216">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="180e6-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="180e6-217">'Blazor'</span></span>
- <span data-ttu-id="180e6-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="180e6-218">'Identity'</span></span>
- <span data-ttu-id="180e6-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="180e6-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="180e6-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="180e6-220">'Razor'</span></span>
- <span data-ttu-id="180e6-221">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="180e6-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="180e6-222">title: "Skonfiguruj konsolidator dla ASP.NET Core Blazor " Author: Description: "Dowiedz się, jak kontrolować konsolidator języka pośredniego (IL) podczas kompilowania Blazor aplikacji".</span><span class="sxs-lookup"><span data-stu-id="180e6-222">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="180e6-223">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="180e6-223">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="180e6-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="180e6-224">'Blazor'</span></span>
- <span data-ttu-id="180e6-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="180e6-225">'Identity'</span></span>
- <span data-ttu-id="180e6-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="180e6-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="180e6-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="180e6-227">'Razor'</span></span>
- <span data-ttu-id="180e6-228">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="180e6-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="180e6-229">title: "Skonfiguruj konsolidator dla ASP.NET Core Blazor " Author: Description: "Dowiedz się, jak kontrolować konsolidator języka pośredniego (IL) podczas kompilowania Blazor aplikacji".</span><span class="sxs-lookup"><span data-stu-id="180e6-229">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="180e6-230">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="180e6-230">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="180e6-231">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="180e6-231">'Blazor'</span></span>
- <span data-ttu-id="180e6-232">'Identity'</span><span class="sxs-lookup"><span data-stu-id="180e6-232">'Identity'</span></span>
- <span data-ttu-id="180e6-233">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="180e6-233">'Let's Encrypt'</span></span>
- <span data-ttu-id="180e6-234">'Razor'</span><span class="sxs-lookup"><span data-stu-id="180e6-234">'Razor'</span></span>
- <span data-ttu-id="180e6-235">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="180e6-235">'SignalR' uid:</span></span> 

-
<span data-ttu-id="180e6-236">title: "Skonfiguruj konsolidator dla ASP.NET Core Blazor " Author: Description: "Dowiedz się, jak kontrolować konsolidator języka pośredniego (IL) podczas kompilowania Blazor aplikacji".</span><span class="sxs-lookup"><span data-stu-id="180e6-236">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="180e6-237">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="180e6-237">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="180e6-238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="180e6-238">'Blazor'</span></span>
- <span data-ttu-id="180e6-239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="180e6-239">'Identity'</span></span>
- <span data-ttu-id="180e6-240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="180e6-240">'Let's Encrypt'</span></span>
- <span data-ttu-id="180e6-241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="180e6-241">'Razor'</span></span>
- <span data-ttu-id="180e6-242">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="180e6-242">'SignalR' uid:</span></span> 

<span data-ttu-id="180e6-243">------------ | | `all`            | Wszystkie dołączone zestawy | | `cjk`             |  *I18n. CJK. dll* | | `mideast`         |  *I18n. Środkowy wschód. dll* | | `none`(ustawienie domyślne) | Brak | | `other`           |  *I18n. Inne. dll* | | `rare`            |  *I18n. Rzadki. dll* | | `west`            |  *I18n. Zachodni. dll*         |</span><span class="sxs-lookup"><span data-stu-id="180e6-243">------------ | | `all`            | All assemblies included | | `cjk`            | *I18N.CJK.dll*          | | `mideast`        | *I18N.MidEast.dll*      | | `none` (default) | None                    | | `other`          | *I18N.Other.dll*        | | `rare`           | *I18N.Rare.dll*         | | `west`           | *I18N.West.dll*         |</span></span>

<span data-ttu-id="180e6-244">Użyj przecinka, aby rozdzielić wiele wartości (na przykład `mideast,west` ).</span><span class="sxs-lookup"><span data-stu-id="180e6-244">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="180e6-245">Aby uzyskać więcej informacji, zobacz [i18n: Pnetlib — Biblioteka struktury wielojęzycznej (repozytorium usługi GitHub/mono)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="180e6-245">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="180e6-246">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="180e6-246">Additional resources</span></span>

* <xref:performance/blazor/webassembly-best-practices#intermediate-language-il-linking>
