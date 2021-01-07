---
title: Opracowywanie aplikacji ASP.NET Core przy użyciu OpenAPI
author: ryanbrandenburg
description: Pokazuje, w jaki sposób używać narzędzia "Microsoft. dotnet-openapi" w celu dodawania odwołań do plików OpenAPI.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
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
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 5d9f1684aa333c38c73673138a703b04d318c6df
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972031"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="8e364-103">Opracowywanie aplikacji ASP.NET Core przy użyciu narzędzi OpenAPI</span><span class="sxs-lookup"><span data-stu-id="8e364-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="8e364-104">Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="8e364-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="8e364-105">[Microsoft. dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) to [globalne narzędzie platformy .NET Core](/dotnet/core/tools/global-tools) służące do zarządzania odwołaniami [openapi](https://github.com/OAI/OpenAPI-Specification) w ramach projektu.</span><span class="sxs-lookup"><span data-stu-id="8e364-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="8e364-106">Instalacja</span><span class="sxs-lookup"><span data-stu-id="8e364-106">Installation</span></span>

<span data-ttu-id="8e364-107">Aby zainstalować `Microsoft.dotnet-openapi` program, uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="8e364-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="8e364-108">Dodaj</span><span class="sxs-lookup"><span data-stu-id="8e364-108">Add</span></span>

<span data-ttu-id="8e364-109">Dodanie odwołania OpenAPI przy użyciu dowolnego polecenia na tej stronie powoduje dodanie `<OpenApiReference />` elementu podobnego do poniższego do pliku *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="8e364-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="8e364-110">Poprzednie odwołanie jest wymagane, aby aplikacja mogła wywołać wygenerowany kod klienta.</span><span class="sxs-lookup"><span data-stu-id="8e364-110">The preceding reference is required for the app to call the generated client code.</span></span>

<!-- TODO: Restore after https://github.com/dotnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a><span data-ttu-id="8e364-111">Dodaj plik</span><span class="sxs-lookup"><span data-stu-id="8e364-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="8e364-112">Opcje</span><span class="sxs-lookup"><span data-stu-id="8e364-112">Options</span></span>

| <span data-ttu-id="8e364-113">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="8e364-113">Short option</span></span>| <span data-ttu-id="8e364-114">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="8e364-114">Long option</span></span>| <span data-ttu-id="8e364-115">Opis</span><span class="sxs-lookup"><span data-stu-id="8e364-115">Description</span></span> | <span data-ttu-id="8e364-116">Przykład</span><span class="sxs-lookup"><span data-stu-id="8e364-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="8e364-117">-p</span><span class="sxs-lookup"><span data-stu-id="8e364-117">-p</span></span>|<span data-ttu-id="8e364-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="8e364-118">--updateProject</span></span> | <span data-ttu-id="8e364-119">Projekt do działania.</span><span class="sxs-lookup"><span data-stu-id="8e364-119">The project to operate on.</span></span> |<span data-ttu-id="8e364-120">openapi dotnet Dodaj plik *--updateProject .\Ref.csproj* .\OpenAPI.jswłączone</span><span class="sxs-lookup"><span data-stu-id="8e364-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="8e364-121">-c</span><span class="sxs-lookup"><span data-stu-id="8e364-121">-c</span></span>|<span data-ttu-id="8e364-122">--Generator kodu</span><span class="sxs-lookup"><span data-stu-id="8e364-122">--code-generator</span></span>| <span data-ttu-id="8e364-123">Generator kodu, który ma zostać zastosowany do odwołania.</span><span class="sxs-lookup"><span data-stu-id="8e364-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="8e364-124">Dostępne opcje to `NSwagCSharp` i `NSwagTypeScript` .</span><span class="sxs-lookup"><span data-stu-id="8e364-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="8e364-125">Jeśli `--code-generator` nie określono ustawień domyślnych narzędzi `NSwagCSharp` .</span><span class="sxs-lookup"><span data-stu-id="8e364-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="8e364-126">openapia dotnet Dodaj plik .\OpenApi.jsw generatorze kodu</span><span class="sxs-lookup"><span data-stu-id="8e364-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="8e364-127">-h</span><span class="sxs-lookup"><span data-stu-id="8e364-127">-h</span></span>|<span data-ttu-id="8e364-128">--Pomoc</span><span class="sxs-lookup"><span data-stu-id="8e364-128">--help</span></span>|<span data-ttu-id="8e364-129">Pokaż informacje pomocy</span><span class="sxs-lookup"><span data-stu-id="8e364-129">Show help information</span></span>|<span data-ttu-id="8e364-130">openapi dotnet — Dodawanie pliku — pomoc</span><span class="sxs-lookup"><span data-stu-id="8e364-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="8e364-131">Argumenty</span><span class="sxs-lookup"><span data-stu-id="8e364-131">Arguments</span></span>

|  <span data-ttu-id="8e364-132">Argument</span><span class="sxs-lookup"><span data-stu-id="8e364-132">Argument</span></span>  | <span data-ttu-id="8e364-133">Opis</span><span class="sxs-lookup"><span data-stu-id="8e364-133">Description</span></span> | <span data-ttu-id="8e364-134">Przykład</span><span class="sxs-lookup"><span data-stu-id="8e364-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="8e364-135">plik źródłowy</span><span class="sxs-lookup"><span data-stu-id="8e364-135">source-file</span></span> | <span data-ttu-id="8e364-136">Źródło, z którego ma zostać utworzone odwołanie.</span><span class="sxs-lookup"><span data-stu-id="8e364-136">The source to create a reference from.</span></span> <span data-ttu-id="8e364-137">Musi to być plik OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="8e364-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="8e364-138">openapi dotnet Dodaj plik *.\OpenAPI.jsna*</span><span class="sxs-lookup"><span data-stu-id="8e364-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="8e364-139">Dodaj adres URL</span><span class="sxs-lookup"><span data-stu-id="8e364-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="8e364-140">Opcje</span><span class="sxs-lookup"><span data-stu-id="8e364-140">Options</span></span>

| <span data-ttu-id="8e364-141">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="8e364-141">Short option</span></span>| <span data-ttu-id="8e364-142">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="8e364-142">Long option</span></span>| <span data-ttu-id="8e364-143">Opis</span><span class="sxs-lookup"><span data-stu-id="8e364-143">Description</span></span> | <span data-ttu-id="8e364-144">Przykład</span><span class="sxs-lookup"><span data-stu-id="8e364-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="8e364-145">-p</span><span class="sxs-lookup"><span data-stu-id="8e364-145">-p</span></span>|<span data-ttu-id="8e364-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="8e364-146">--updateProject</span></span> | <span data-ttu-id="8e364-147">Projekt do działania.</span><span class="sxs-lookup"><span data-stu-id="8e364-147">The project to operate on.</span></span> |<span data-ttu-id="8e364-148">openapi dotnet — Dodaj adres URL *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="8e364-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="8e364-149">-o</span><span class="sxs-lookup"><span data-stu-id="8e364-149">-o</span></span>|<span data-ttu-id="8e364-150">--Output-File</span><span class="sxs-lookup"><span data-stu-id="8e364-150">--output-file</span></span> | <span data-ttu-id="8e364-151">Miejsce, w którym ma zostać umieszczona kopia lokalna pliku OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="8e364-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="8e364-152">openapi dotnet Dodaj adres URL- `https://contoso.com/openapi.json` *-Output-File myclient.json*</span><span class="sxs-lookup"><span data-stu-id="8e364-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="8e364-153">-c</span><span class="sxs-lookup"><span data-stu-id="8e364-153">-c</span></span>|<span data-ttu-id="8e364-154">--Generator kodu</span><span class="sxs-lookup"><span data-stu-id="8e364-154">--code-generator</span></span>| <span data-ttu-id="8e364-155">Generator kodu, który ma zostać zastosowany do odwołania.</span><span class="sxs-lookup"><span data-stu-id="8e364-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="8e364-156">Dostępne opcje to `NSwagCSharp` i `NSwagTypeScript` .</span><span class="sxs-lookup"><span data-stu-id="8e364-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="8e364-157">openapi dotnet — Dodaj adres URL — `https://contoso.com/openapi.json` generator kodu</span><span class="sxs-lookup"><span data-stu-id="8e364-157">dotnet openapi add url `https://contoso.com/openapi.json` --code-generator</span></span>
| <span data-ttu-id="8e364-158">-h</span><span class="sxs-lookup"><span data-stu-id="8e364-158">-h</span></span>|<span data-ttu-id="8e364-159">--Pomoc</span><span class="sxs-lookup"><span data-stu-id="8e364-159">--help</span></span>|<span data-ttu-id="8e364-160">Pokaż informacje pomocy</span><span class="sxs-lookup"><span data-stu-id="8e364-160">Show help information</span></span>|<span data-ttu-id="8e364-161">openapi dotnet — Dodaj adres URL — pomoc</span><span class="sxs-lookup"><span data-stu-id="8e364-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="8e364-162">Argumenty</span><span class="sxs-lookup"><span data-stu-id="8e364-162">Arguments</span></span>

|  <span data-ttu-id="8e364-163">Argument</span><span class="sxs-lookup"><span data-stu-id="8e364-163">Argument</span></span>  | <span data-ttu-id="8e364-164">Opis</span><span class="sxs-lookup"><span data-stu-id="8e364-164">Description</span></span> | <span data-ttu-id="8e364-165">Przykład</span><span class="sxs-lookup"><span data-stu-id="8e364-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="8e364-166">adres URL źródła</span><span class="sxs-lookup"><span data-stu-id="8e364-166">source-URL</span></span> | <span data-ttu-id="8e364-167">Źródło, z którego ma zostać utworzone odwołanie.</span><span class="sxs-lookup"><span data-stu-id="8e364-167">The source to create a reference from.</span></span> <span data-ttu-id="8e364-168">Musi być adresem URL.</span><span class="sxs-lookup"><span data-stu-id="8e364-168">Must be a URL.</span></span> |<span data-ttu-id="8e364-169">Dodawanie adresu URL openapi dotnet `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="8e364-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="8e364-170">Usuń</span><span class="sxs-lookup"><span data-stu-id="8e364-170">Remove</span></span>

<span data-ttu-id="8e364-171">Usuwa odwołanie OpenAPI pasujące do podanej nazwy pliku z pliku *csproj* .</span><span class="sxs-lookup"><span data-stu-id="8e364-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="8e364-172">Po usunięciu odwołania OpenAPI klienci nie będą wygenerował.</span><span class="sxs-lookup"><span data-stu-id="8e364-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="8e364-173">Pliki Local *. JSON* i *. YAML* są usuwane.</span><span class="sxs-lookup"><span data-stu-id="8e364-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="8e364-174">Opcje</span><span class="sxs-lookup"><span data-stu-id="8e364-174">Options</span></span>

| <span data-ttu-id="8e364-175">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="8e364-175">Short option</span></span>| <span data-ttu-id="8e364-176">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="8e364-176">Long option</span></span>| <span data-ttu-id="8e364-177">Opis</span><span class="sxs-lookup"><span data-stu-id="8e364-177">Description</span></span>| <span data-ttu-id="8e364-178">Przykład</span><span class="sxs-lookup"><span data-stu-id="8e364-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="8e364-179">-p</span><span class="sxs-lookup"><span data-stu-id="8e364-179">-p</span></span>|<span data-ttu-id="8e364-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="8e364-180">--updateProject</span></span> | <span data-ttu-id="8e364-181">Projekt do działania.</span><span class="sxs-lookup"><span data-stu-id="8e364-181">The project to operate on.</span></span> |<span data-ttu-id="8e364-182">openapi dotnet *updateProject .\OpenAPI.js.\Ref.csproj*</span><span class="sxs-lookup"><span data-stu-id="8e364-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="8e364-183">-h</span><span class="sxs-lookup"><span data-stu-id="8e364-183">-h</span></span>|<span data-ttu-id="8e364-184">--Pomoc</span><span class="sxs-lookup"><span data-stu-id="8e364-184">--help</span></span>|<span data-ttu-id="8e364-185">Pokaż informacje pomocy</span><span class="sxs-lookup"><span data-stu-id="8e364-185">Show help information</span></span>|<span data-ttu-id="8e364-186">openapi dotnet — pomoc</span><span class="sxs-lookup"><span data-stu-id="8e364-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="8e364-187">Argumenty</span><span class="sxs-lookup"><span data-stu-id="8e364-187">Arguments</span></span>

|  <span data-ttu-id="8e364-188">Argument</span><span class="sxs-lookup"><span data-stu-id="8e364-188">Argument</span></span>  | <span data-ttu-id="8e364-189">Opis</span><span class="sxs-lookup"><span data-stu-id="8e364-189">Description</span></span>| <span data-ttu-id="8e364-190">Przykład</span><span class="sxs-lookup"><span data-stu-id="8e364-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="8e364-191">plik źródłowy</span><span class="sxs-lookup"><span data-stu-id="8e364-191">source-file</span></span> | <span data-ttu-id="8e364-192">Źródło, do którego ma zostać usunięte odwołanie.</span><span class="sxs-lookup"><span data-stu-id="8e364-192">The source to remove the reference to.</span></span> |<span data-ttu-id="8e364-193">openapi z systemem dotnet — Usuń *.\OpenAPI.js*</span><span class="sxs-lookup"><span data-stu-id="8e364-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="8e364-194">Odśwież</span><span class="sxs-lookup"><span data-stu-id="8e364-194">Refresh</span></span>

<span data-ttu-id="8e364-195">Odświeża lokalną wersję pliku, który został pobrany przy użyciu najnowszej zawartości z adresu URL pobierania.</span><span class="sxs-lookup"><span data-stu-id="8e364-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="8e364-196">Opcje</span><span class="sxs-lookup"><span data-stu-id="8e364-196">Options</span></span>

| <span data-ttu-id="8e364-197">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="8e364-197">Short option</span></span>| <span data-ttu-id="8e364-198">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="8e364-198">Long option</span></span>| <span data-ttu-id="8e364-199">Opis</span><span class="sxs-lookup"><span data-stu-id="8e364-199">Description</span></span> | <span data-ttu-id="8e364-200">Przykład</span><span class="sxs-lookup"><span data-stu-id="8e364-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="8e364-201">-p</span><span class="sxs-lookup"><span data-stu-id="8e364-201">-p</span></span>|<span data-ttu-id="8e364-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="8e364-202">--updateProject</span></span> | <span data-ttu-id="8e364-203">Projekt do działania.</span><span class="sxs-lookup"><span data-stu-id="8e364-203">The project to operate on.</span></span> | <span data-ttu-id="8e364-204">openapi dotnet *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="8e364-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="8e364-205">-h</span><span class="sxs-lookup"><span data-stu-id="8e364-205">-h</span></span>|<span data-ttu-id="8e364-206">--Pomoc</span><span class="sxs-lookup"><span data-stu-id="8e364-206">--help</span></span>|<span data-ttu-id="8e364-207">Pokaż informacje pomocy</span><span class="sxs-lookup"><span data-stu-id="8e364-207">Show help information</span></span>|<span data-ttu-id="8e364-208">openapi dotnet — pomoc</span><span class="sxs-lookup"><span data-stu-id="8e364-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="8e364-209">Argumenty</span><span class="sxs-lookup"><span data-stu-id="8e364-209">Arguments</span></span>

|  <span data-ttu-id="8e364-210">Argument</span><span class="sxs-lookup"><span data-stu-id="8e364-210">Argument</span></span>  | <span data-ttu-id="8e364-211">Opis</span><span class="sxs-lookup"><span data-stu-id="8e364-211">Description</span></span> | <span data-ttu-id="8e364-212">Przykład</span><span class="sxs-lookup"><span data-stu-id="8e364-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="8e364-213">adres URL źródła</span><span class="sxs-lookup"><span data-stu-id="8e364-213">source-URL</span></span> | <span data-ttu-id="8e364-214">Adres URL, z którego ma zostać odświeżone odwołanie.</span><span class="sxs-lookup"><span data-stu-id="8e364-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="8e364-215">Odświeżanie openapi dotnet `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="8e364-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
