---
title: Opracowywanie aplikacji ASP.NET Core przy użyciu OpenAPI
author: ryanbrandenburg
description: Pokazuje, w jaki sposób używać narzędzia "Microsoft. dotnet-openapi" w celu dodawania odwołań do plików OpenAPI.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: eb8d6a1dc70b2aabf495bdb359e243c91e94289f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404798"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="fa182-103">Opracowywanie aplikacji ASP.NET Core przy użyciu narzędzi OpenAPI</span><span class="sxs-lookup"><span data-stu-id="fa182-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="fa182-104">Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="fa182-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="fa182-105">[Microsoft. dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) to [globalne narzędzie platformy .NET Core](/dotnet/core/tools/global-tools) służące do zarządzania odwołaniami [openapi](https://github.com/OAI/OpenAPI-Specification) w ramach projektu.</span><span class="sxs-lookup"><span data-stu-id="fa182-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="fa182-106">Instalacja</span><span class="sxs-lookup"><span data-stu-id="fa182-106">Installation</span></span>

<span data-ttu-id="fa182-107">Aby zainstalować `Microsoft.dotnet-openapi` program, uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="fa182-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="fa182-108">Dodaj</span><span class="sxs-lookup"><span data-stu-id="fa182-108">Add</span></span>

<span data-ttu-id="fa182-109">Dodanie odwołania OpenAPI przy użyciu dowolnego polecenia na tej stronie powoduje dodanie `<OpenApiReference />` elementu podobnego do poniższego do pliku *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="fa182-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="fa182-110">Poprzednie odwołanie jest wymagane, aby aplikacja mogła wywołać wygenerowany kod klienta.</span><span class="sxs-lookup"><span data-stu-id="fa182-110">The preceding reference is required for the app to call the generated client code.</span></span>

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

### <a name="add-file"></a><span data-ttu-id="fa182-111">Dodaj plik</span><span class="sxs-lookup"><span data-stu-id="fa182-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="fa182-112">Opcje</span><span class="sxs-lookup"><span data-stu-id="fa182-112">Options</span></span>

| <span data-ttu-id="fa182-113">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="fa182-113">Short option</span></span>| <span data-ttu-id="fa182-114">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="fa182-114">Long option</span></span>| <span data-ttu-id="fa182-115">Opis</span><span class="sxs-lookup"><span data-stu-id="fa182-115">Description</span></span> | <span data-ttu-id="fa182-116">Przykład</span><span class="sxs-lookup"><span data-stu-id="fa182-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="fa182-117">-p</span><span class="sxs-lookup"><span data-stu-id="fa182-117">-p</span></span>|<span data-ttu-id="fa182-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="fa182-118">--updateProject</span></span> | <span data-ttu-id="fa182-119">Projekt do działania.</span><span class="sxs-lookup"><span data-stu-id="fa182-119">The project to operate on.</span></span> |<span data-ttu-id="fa182-120">openapi dotnet Dodaj plik *--updateProject .\Ref.csproj* .\OpenAPI.jswłączone</span><span class="sxs-lookup"><span data-stu-id="fa182-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="fa182-121">-c</span><span class="sxs-lookup"><span data-stu-id="fa182-121">-c</span></span>|<span data-ttu-id="fa182-122">--Generator kodu</span><span class="sxs-lookup"><span data-stu-id="fa182-122">--code-generator</span></span>| <span data-ttu-id="fa182-123">Generator kodu, który ma zostać zastosowany do odwołania.</span><span class="sxs-lookup"><span data-stu-id="fa182-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="fa182-124">Dostępne opcje to `NSwagCSharp` i `NSwagTypeScript` .</span><span class="sxs-lookup"><span data-stu-id="fa182-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="fa182-125">Jeśli `--code-generator` nie określono ustawień domyślnych narzędzi `NSwagCSharp` .</span><span class="sxs-lookup"><span data-stu-id="fa182-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="fa182-126">openapia dotnet Dodaj plik .\OpenApi.jsw generatorze kodu</span><span class="sxs-lookup"><span data-stu-id="fa182-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="fa182-127">-h</span><span class="sxs-lookup"><span data-stu-id="fa182-127">-h</span></span>|<span data-ttu-id="fa182-128">--Pomoc</span><span class="sxs-lookup"><span data-stu-id="fa182-128">--help</span></span>|<span data-ttu-id="fa182-129">Pokaż informacje pomocy</span><span class="sxs-lookup"><span data-stu-id="fa182-129">Show help information</span></span>|<span data-ttu-id="fa182-130">openapi dotnet — Dodawanie pliku — pomoc</span><span class="sxs-lookup"><span data-stu-id="fa182-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="fa182-131">Argumenty</span><span class="sxs-lookup"><span data-stu-id="fa182-131">Arguments</span></span>

|  <span data-ttu-id="fa182-132">Argument</span><span class="sxs-lookup"><span data-stu-id="fa182-132">Argument</span></span>  | <span data-ttu-id="fa182-133">Opis</span><span class="sxs-lookup"><span data-stu-id="fa182-133">Description</span></span> | <span data-ttu-id="fa182-134">Przykład</span><span class="sxs-lookup"><span data-stu-id="fa182-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="fa182-135">plik źródłowy</span><span class="sxs-lookup"><span data-stu-id="fa182-135">source-file</span></span> | <span data-ttu-id="fa182-136">Źródło, z którego ma zostać utworzone odwołanie.</span><span class="sxs-lookup"><span data-stu-id="fa182-136">The source to create a reference from.</span></span> <span data-ttu-id="fa182-137">Musi to być plik OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="fa182-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="fa182-138">openapi dotnet Dodaj plik *.\OpenAPI.jsna*</span><span class="sxs-lookup"><span data-stu-id="fa182-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="fa182-139">Dodaj adres URL</span><span class="sxs-lookup"><span data-stu-id="fa182-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="fa182-140">Opcje</span><span class="sxs-lookup"><span data-stu-id="fa182-140">Options</span></span>

| <span data-ttu-id="fa182-141">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="fa182-141">Short option</span></span>| <span data-ttu-id="fa182-142">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="fa182-142">Long option</span></span>| <span data-ttu-id="fa182-143">Opis</span><span class="sxs-lookup"><span data-stu-id="fa182-143">Description</span></span> | <span data-ttu-id="fa182-144">Przykład</span><span class="sxs-lookup"><span data-stu-id="fa182-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="fa182-145">-p</span><span class="sxs-lookup"><span data-stu-id="fa182-145">-p</span></span>|<span data-ttu-id="fa182-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="fa182-146">--updateProject</span></span> | <span data-ttu-id="fa182-147">Projekt do działania.</span><span class="sxs-lookup"><span data-stu-id="fa182-147">The project to operate on.</span></span> |<span data-ttu-id="fa182-148">openapi dotnet — Dodaj adres URL *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="fa182-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="fa182-149">-o</span><span class="sxs-lookup"><span data-stu-id="fa182-149">-o</span></span>|<span data-ttu-id="fa182-150">--Output-File</span><span class="sxs-lookup"><span data-stu-id="fa182-150">--output-file</span></span> | <span data-ttu-id="fa182-151">Miejsce, w którym ma zostać umieszczona kopia lokalna pliku OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="fa182-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="fa182-152">openapi dotnet Dodaj adres URL- `https://contoso.com/openapi.json` *-Output-File myclient.json*</span><span class="sxs-lookup"><span data-stu-id="fa182-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="fa182-153">-c</span><span class="sxs-lookup"><span data-stu-id="fa182-153">-c</span></span>|<span data-ttu-id="fa182-154">--Generator kodu</span><span class="sxs-lookup"><span data-stu-id="fa182-154">--code-generator</span></span>| <span data-ttu-id="fa182-155">Generator kodu, który ma zostać zastosowany do odwołania.</span><span class="sxs-lookup"><span data-stu-id="fa182-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="fa182-156">Dostępne opcje to `NSwagCSharp` i `NSwagTypeScript` .</span><span class="sxs-lookup"><span data-stu-id="fa182-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="fa182-157">openapia dotnet Dodaj plik .\OpenApi.jsw generatorze kodu</span><span class="sxs-lookup"><span data-stu-id="fa182-157">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="fa182-158">-h</span><span class="sxs-lookup"><span data-stu-id="fa182-158">-h</span></span>|<span data-ttu-id="fa182-159">--Pomoc</span><span class="sxs-lookup"><span data-stu-id="fa182-159">--help</span></span>|<span data-ttu-id="fa182-160">Pokaż informacje pomocy</span><span class="sxs-lookup"><span data-stu-id="fa182-160">Show help information</span></span>|<span data-ttu-id="fa182-161">openapi dotnet — Dodaj adres URL — pomoc</span><span class="sxs-lookup"><span data-stu-id="fa182-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="fa182-162">Argumenty</span><span class="sxs-lookup"><span data-stu-id="fa182-162">Arguments</span></span>

|  <span data-ttu-id="fa182-163">Argument</span><span class="sxs-lookup"><span data-stu-id="fa182-163">Argument</span></span>  | <span data-ttu-id="fa182-164">Opis</span><span class="sxs-lookup"><span data-stu-id="fa182-164">Description</span></span> | <span data-ttu-id="fa182-165">Przykład</span><span class="sxs-lookup"><span data-stu-id="fa182-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="fa182-166">adres URL źródła</span><span class="sxs-lookup"><span data-stu-id="fa182-166">source-URL</span></span> | <span data-ttu-id="fa182-167">Źródło, z którego ma zostać utworzone odwołanie.</span><span class="sxs-lookup"><span data-stu-id="fa182-167">The source to create a reference from.</span></span> <span data-ttu-id="fa182-168">Musi być adresem URL.</span><span class="sxs-lookup"><span data-stu-id="fa182-168">Must be a URL.</span></span> |<span data-ttu-id="fa182-169">Dodawanie adresu URL openapi dotnet`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="fa182-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="fa182-170">Usuń</span><span class="sxs-lookup"><span data-stu-id="fa182-170">Remove</span></span>

<span data-ttu-id="fa182-171">Usuwa odwołanie OpenAPI pasujące do podanej nazwy pliku z pliku *csproj* .</span><span class="sxs-lookup"><span data-stu-id="fa182-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="fa182-172">Po usunięciu odwołania OpenAPI klienci nie będą wygenerował.</span><span class="sxs-lookup"><span data-stu-id="fa182-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="fa182-173">Pliki Local *. JSON* i *. YAML* są usuwane.</span><span class="sxs-lookup"><span data-stu-id="fa182-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="fa182-174">Opcje</span><span class="sxs-lookup"><span data-stu-id="fa182-174">Options</span></span>

| <span data-ttu-id="fa182-175">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="fa182-175">Short option</span></span>| <span data-ttu-id="fa182-176">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="fa182-176">Long option</span></span>| <span data-ttu-id="fa182-177">Opis</span><span class="sxs-lookup"><span data-stu-id="fa182-177">Description</span></span>| <span data-ttu-id="fa182-178">Przykład</span><span class="sxs-lookup"><span data-stu-id="fa182-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="fa182-179">-p</span><span class="sxs-lookup"><span data-stu-id="fa182-179">-p</span></span>|<span data-ttu-id="fa182-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="fa182-180">--updateProject</span></span> | <span data-ttu-id="fa182-181">Projekt do działania.</span><span class="sxs-lookup"><span data-stu-id="fa182-181">The project to operate on.</span></span> |<span data-ttu-id="fa182-182">openapi dotnet *updateProject .\OpenAPI.js.\Ref.csproj*</span><span class="sxs-lookup"><span data-stu-id="fa182-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="fa182-183">-h</span><span class="sxs-lookup"><span data-stu-id="fa182-183">-h</span></span>|<span data-ttu-id="fa182-184">--Pomoc</span><span class="sxs-lookup"><span data-stu-id="fa182-184">--help</span></span>|<span data-ttu-id="fa182-185">Pokaż informacje pomocy</span><span class="sxs-lookup"><span data-stu-id="fa182-185">Show help information</span></span>|<span data-ttu-id="fa182-186">openapi dotnet — pomoc</span><span class="sxs-lookup"><span data-stu-id="fa182-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="fa182-187">Argumenty</span><span class="sxs-lookup"><span data-stu-id="fa182-187">Arguments</span></span>

|  <span data-ttu-id="fa182-188">Argument</span><span class="sxs-lookup"><span data-stu-id="fa182-188">Argument</span></span>  | <span data-ttu-id="fa182-189">Opis</span><span class="sxs-lookup"><span data-stu-id="fa182-189">Description</span></span>| <span data-ttu-id="fa182-190">Przykład</span><span class="sxs-lookup"><span data-stu-id="fa182-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="fa182-191">plik źródłowy</span><span class="sxs-lookup"><span data-stu-id="fa182-191">source-file</span></span> | <span data-ttu-id="fa182-192">Źródło, do którego ma zostać usunięte odwołanie.</span><span class="sxs-lookup"><span data-stu-id="fa182-192">The source to remove the reference to.</span></span> |<span data-ttu-id="fa182-193">openapi z systemem dotnet — Usuń *.\OpenAPI.js*</span><span class="sxs-lookup"><span data-stu-id="fa182-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="fa182-194">Odśwież</span><span class="sxs-lookup"><span data-stu-id="fa182-194">Refresh</span></span>

<span data-ttu-id="fa182-195">Odświeża lokalną wersję pliku, który został pobrany przy użyciu najnowszej zawartości z adresu URL pobierania.</span><span class="sxs-lookup"><span data-stu-id="fa182-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="fa182-196">Opcje</span><span class="sxs-lookup"><span data-stu-id="fa182-196">Options</span></span>

| <span data-ttu-id="fa182-197">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="fa182-197">Short option</span></span>| <span data-ttu-id="fa182-198">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="fa182-198">Long option</span></span>| <span data-ttu-id="fa182-199">Opis</span><span class="sxs-lookup"><span data-stu-id="fa182-199">Description</span></span> | <span data-ttu-id="fa182-200">Przykład</span><span class="sxs-lookup"><span data-stu-id="fa182-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="fa182-201">-p</span><span class="sxs-lookup"><span data-stu-id="fa182-201">-p</span></span>|<span data-ttu-id="fa182-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="fa182-202">--updateProject</span></span> | <span data-ttu-id="fa182-203">Projekt do działania.</span><span class="sxs-lookup"><span data-stu-id="fa182-203">The project to operate on.</span></span> | <span data-ttu-id="fa182-204">openapi dotnet *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="fa182-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="fa182-205">-h</span><span class="sxs-lookup"><span data-stu-id="fa182-205">-h</span></span>|<span data-ttu-id="fa182-206">--Pomoc</span><span class="sxs-lookup"><span data-stu-id="fa182-206">--help</span></span>|<span data-ttu-id="fa182-207">Pokaż informacje pomocy</span><span class="sxs-lookup"><span data-stu-id="fa182-207">Show help information</span></span>|<span data-ttu-id="fa182-208">openapi dotnet — pomoc</span><span class="sxs-lookup"><span data-stu-id="fa182-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="fa182-209">Argumenty</span><span class="sxs-lookup"><span data-stu-id="fa182-209">Arguments</span></span>

|  <span data-ttu-id="fa182-210">Argument</span><span class="sxs-lookup"><span data-stu-id="fa182-210">Argument</span></span>  | <span data-ttu-id="fa182-211">Opis</span><span class="sxs-lookup"><span data-stu-id="fa182-211">Description</span></span> | <span data-ttu-id="fa182-212">Przykład</span><span class="sxs-lookup"><span data-stu-id="fa182-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="fa182-213">adres URL źródła</span><span class="sxs-lookup"><span data-stu-id="fa182-213">source-URL</span></span> | <span data-ttu-id="fa182-214">Adres URL, z którego ma zostać odświeżone odwołanie.</span><span class="sxs-lookup"><span data-stu-id="fa182-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="fa182-215">Odświeżanie openapi dotnet`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="fa182-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
