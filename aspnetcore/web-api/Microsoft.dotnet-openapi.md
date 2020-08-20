---
title: Opracowywanie aplikacji ASP.NET Core przy użyciu OpenAPI
author: ryanbrandenburg
description: Pokazuje, w jaki sposób używać narzędzia "Microsoft. dotnet-openapi" w celu dodawania odwołań do plików OpenAPI.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
no-loc:
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
ms.openlocfilehash: 45921deb35452876b0a92a8731da68539a880c1d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626561"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="10c46-103">Opracowywanie aplikacji ASP.NET Core przy użyciu narzędzi OpenAPI</span><span class="sxs-lookup"><span data-stu-id="10c46-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="10c46-104">Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="10c46-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="10c46-105">[Microsoft. dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) to [globalne narzędzie platformy .NET Core](/dotnet/core/tools/global-tools) służące do zarządzania odwołaniami [openapi](https://github.com/OAI/OpenAPI-Specification) w ramach projektu.</span><span class="sxs-lookup"><span data-stu-id="10c46-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="10c46-106">Instalacja</span><span class="sxs-lookup"><span data-stu-id="10c46-106">Installation</span></span>

<span data-ttu-id="10c46-107">Aby zainstalować `Microsoft.dotnet-openapi` program, uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="10c46-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="10c46-108">Dodaj</span><span class="sxs-lookup"><span data-stu-id="10c46-108">Add</span></span>

<span data-ttu-id="10c46-109">Dodanie odwołania OpenAPI przy użyciu dowolnego polecenia na tej stronie powoduje dodanie `<OpenApiReference />` elementu podobnego do poniższego do pliku *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="10c46-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="10c46-110">Poprzednie odwołanie jest wymagane, aby aplikacja mogła wywołać wygenerowany kod klienta.</span><span class="sxs-lookup"><span data-stu-id="10c46-110">The preceding reference is required for the app to call the generated client code.</span></span>

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

### <a name="add-file"></a><span data-ttu-id="10c46-111">Dodaj plik</span><span class="sxs-lookup"><span data-stu-id="10c46-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="10c46-112">Opcje</span><span class="sxs-lookup"><span data-stu-id="10c46-112">Options</span></span>

| <span data-ttu-id="10c46-113">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="10c46-113">Short option</span></span>| <span data-ttu-id="10c46-114">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="10c46-114">Long option</span></span>| <span data-ttu-id="10c46-115">Opis</span><span class="sxs-lookup"><span data-stu-id="10c46-115">Description</span></span> | <span data-ttu-id="10c46-116">Przykład</span><span class="sxs-lookup"><span data-stu-id="10c46-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="10c46-117">-p</span><span class="sxs-lookup"><span data-stu-id="10c46-117">-p</span></span>|<span data-ttu-id="10c46-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="10c46-118">--updateProject</span></span> | <span data-ttu-id="10c46-119">Projekt do działania.</span><span class="sxs-lookup"><span data-stu-id="10c46-119">The project to operate on.</span></span> |<span data-ttu-id="10c46-120">openapi dotnet Dodaj plik *--updateProject .\Ref.csproj* .\OpenAPI.jswłączone</span><span class="sxs-lookup"><span data-stu-id="10c46-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="10c46-121">-c</span><span class="sxs-lookup"><span data-stu-id="10c46-121">-c</span></span>|<span data-ttu-id="10c46-122">--Generator kodu</span><span class="sxs-lookup"><span data-stu-id="10c46-122">--code-generator</span></span>| <span data-ttu-id="10c46-123">Generator kodu, który ma zostać zastosowany do odwołania.</span><span class="sxs-lookup"><span data-stu-id="10c46-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="10c46-124">Dostępne opcje to `NSwagCSharp` i `NSwagTypeScript` .</span><span class="sxs-lookup"><span data-stu-id="10c46-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="10c46-125">Jeśli `--code-generator` nie określono ustawień domyślnych narzędzi `NSwagCSharp` .</span><span class="sxs-lookup"><span data-stu-id="10c46-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="10c46-126">openapia dotnet Dodaj plik .\OpenApi.jsw generatorze kodu</span><span class="sxs-lookup"><span data-stu-id="10c46-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="10c46-127">-h</span><span class="sxs-lookup"><span data-stu-id="10c46-127">-h</span></span>|<span data-ttu-id="10c46-128">--Pomoc</span><span class="sxs-lookup"><span data-stu-id="10c46-128">--help</span></span>|<span data-ttu-id="10c46-129">Pokaż informacje pomocy</span><span class="sxs-lookup"><span data-stu-id="10c46-129">Show help information</span></span>|<span data-ttu-id="10c46-130">openapi dotnet — Dodawanie pliku — pomoc</span><span class="sxs-lookup"><span data-stu-id="10c46-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="10c46-131">Argumenty</span><span class="sxs-lookup"><span data-stu-id="10c46-131">Arguments</span></span>

|  <span data-ttu-id="10c46-132">Argument</span><span class="sxs-lookup"><span data-stu-id="10c46-132">Argument</span></span>  | <span data-ttu-id="10c46-133">Opis</span><span class="sxs-lookup"><span data-stu-id="10c46-133">Description</span></span> | <span data-ttu-id="10c46-134">Przykład</span><span class="sxs-lookup"><span data-stu-id="10c46-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="10c46-135">plik źródłowy</span><span class="sxs-lookup"><span data-stu-id="10c46-135">source-file</span></span> | <span data-ttu-id="10c46-136">Źródło, z którego ma zostać utworzone odwołanie.</span><span class="sxs-lookup"><span data-stu-id="10c46-136">The source to create a reference from.</span></span> <span data-ttu-id="10c46-137">Musi to być plik OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="10c46-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="10c46-138">openapi dotnet Dodaj plik *.\OpenAPI.jsna*</span><span class="sxs-lookup"><span data-stu-id="10c46-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="10c46-139">Dodaj adres URL</span><span class="sxs-lookup"><span data-stu-id="10c46-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="10c46-140">Opcje</span><span class="sxs-lookup"><span data-stu-id="10c46-140">Options</span></span>

| <span data-ttu-id="10c46-141">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="10c46-141">Short option</span></span>| <span data-ttu-id="10c46-142">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="10c46-142">Long option</span></span>| <span data-ttu-id="10c46-143">Opis</span><span class="sxs-lookup"><span data-stu-id="10c46-143">Description</span></span> | <span data-ttu-id="10c46-144">Przykład</span><span class="sxs-lookup"><span data-stu-id="10c46-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="10c46-145">-p</span><span class="sxs-lookup"><span data-stu-id="10c46-145">-p</span></span>|<span data-ttu-id="10c46-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="10c46-146">--updateProject</span></span> | <span data-ttu-id="10c46-147">Projekt do działania.</span><span class="sxs-lookup"><span data-stu-id="10c46-147">The project to operate on.</span></span> |<span data-ttu-id="10c46-148">openapi dotnet — Dodaj adres URL *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="10c46-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="10c46-149">-o</span><span class="sxs-lookup"><span data-stu-id="10c46-149">-o</span></span>|<span data-ttu-id="10c46-150">--Output-File</span><span class="sxs-lookup"><span data-stu-id="10c46-150">--output-file</span></span> | <span data-ttu-id="10c46-151">Miejsce, w którym ma zostać umieszczona kopia lokalna pliku OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="10c46-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="10c46-152">openapi dotnet Dodaj adres URL- `https://contoso.com/openapi.json` *-Output-File myclient.json*</span><span class="sxs-lookup"><span data-stu-id="10c46-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="10c46-153">-c</span><span class="sxs-lookup"><span data-stu-id="10c46-153">-c</span></span>|<span data-ttu-id="10c46-154">--Generator kodu</span><span class="sxs-lookup"><span data-stu-id="10c46-154">--code-generator</span></span>| <span data-ttu-id="10c46-155">Generator kodu, który ma zostać zastosowany do odwołania.</span><span class="sxs-lookup"><span data-stu-id="10c46-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="10c46-156">Dostępne opcje to `NSwagCSharp` i `NSwagTypeScript` .</span><span class="sxs-lookup"><span data-stu-id="10c46-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="10c46-157">openapia dotnet Dodaj plik .\OpenApi.jsw generatorze kodu</span><span class="sxs-lookup"><span data-stu-id="10c46-157">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="10c46-158">-h</span><span class="sxs-lookup"><span data-stu-id="10c46-158">-h</span></span>|<span data-ttu-id="10c46-159">--Pomoc</span><span class="sxs-lookup"><span data-stu-id="10c46-159">--help</span></span>|<span data-ttu-id="10c46-160">Pokaż informacje pomocy</span><span class="sxs-lookup"><span data-stu-id="10c46-160">Show help information</span></span>|<span data-ttu-id="10c46-161">openapi dotnet — Dodaj adres URL — pomoc</span><span class="sxs-lookup"><span data-stu-id="10c46-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="10c46-162">Argumenty</span><span class="sxs-lookup"><span data-stu-id="10c46-162">Arguments</span></span>

|  <span data-ttu-id="10c46-163">Argument</span><span class="sxs-lookup"><span data-stu-id="10c46-163">Argument</span></span>  | <span data-ttu-id="10c46-164">Opis</span><span class="sxs-lookup"><span data-stu-id="10c46-164">Description</span></span> | <span data-ttu-id="10c46-165">Przykład</span><span class="sxs-lookup"><span data-stu-id="10c46-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="10c46-166">adres URL źródła</span><span class="sxs-lookup"><span data-stu-id="10c46-166">source-URL</span></span> | <span data-ttu-id="10c46-167">Źródło, z którego ma zostać utworzone odwołanie.</span><span class="sxs-lookup"><span data-stu-id="10c46-167">The source to create a reference from.</span></span> <span data-ttu-id="10c46-168">Musi być adresem URL.</span><span class="sxs-lookup"><span data-stu-id="10c46-168">Must be a URL.</span></span> |<span data-ttu-id="10c46-169">Dodawanie adresu URL openapi dotnet `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="10c46-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="10c46-170">Usuń</span><span class="sxs-lookup"><span data-stu-id="10c46-170">Remove</span></span>

<span data-ttu-id="10c46-171">Usuwa odwołanie OpenAPI pasujące do podanej nazwy pliku z pliku *csproj* .</span><span class="sxs-lookup"><span data-stu-id="10c46-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="10c46-172">Po usunięciu odwołania OpenAPI klienci nie będą wygenerował.</span><span class="sxs-lookup"><span data-stu-id="10c46-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="10c46-173">Pliki Local *. JSON* i *. YAML* są usuwane.</span><span class="sxs-lookup"><span data-stu-id="10c46-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="10c46-174">Opcje</span><span class="sxs-lookup"><span data-stu-id="10c46-174">Options</span></span>

| <span data-ttu-id="10c46-175">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="10c46-175">Short option</span></span>| <span data-ttu-id="10c46-176">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="10c46-176">Long option</span></span>| <span data-ttu-id="10c46-177">Opis</span><span class="sxs-lookup"><span data-stu-id="10c46-177">Description</span></span>| <span data-ttu-id="10c46-178">Przykład</span><span class="sxs-lookup"><span data-stu-id="10c46-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="10c46-179">-p</span><span class="sxs-lookup"><span data-stu-id="10c46-179">-p</span></span>|<span data-ttu-id="10c46-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="10c46-180">--updateProject</span></span> | <span data-ttu-id="10c46-181">Projekt do działania.</span><span class="sxs-lookup"><span data-stu-id="10c46-181">The project to operate on.</span></span> |<span data-ttu-id="10c46-182">openapi dotnet *updateProject .\OpenAPI.js.\Ref.csproj*</span><span class="sxs-lookup"><span data-stu-id="10c46-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="10c46-183">-h</span><span class="sxs-lookup"><span data-stu-id="10c46-183">-h</span></span>|<span data-ttu-id="10c46-184">--Pomoc</span><span class="sxs-lookup"><span data-stu-id="10c46-184">--help</span></span>|<span data-ttu-id="10c46-185">Pokaż informacje pomocy</span><span class="sxs-lookup"><span data-stu-id="10c46-185">Show help information</span></span>|<span data-ttu-id="10c46-186">openapi dotnet — pomoc</span><span class="sxs-lookup"><span data-stu-id="10c46-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="10c46-187">Argumenty</span><span class="sxs-lookup"><span data-stu-id="10c46-187">Arguments</span></span>

|  <span data-ttu-id="10c46-188">Argument</span><span class="sxs-lookup"><span data-stu-id="10c46-188">Argument</span></span>  | <span data-ttu-id="10c46-189">Opis</span><span class="sxs-lookup"><span data-stu-id="10c46-189">Description</span></span>| <span data-ttu-id="10c46-190">Przykład</span><span class="sxs-lookup"><span data-stu-id="10c46-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="10c46-191">plik źródłowy</span><span class="sxs-lookup"><span data-stu-id="10c46-191">source-file</span></span> | <span data-ttu-id="10c46-192">Źródło, do którego ma zostać usunięte odwołanie.</span><span class="sxs-lookup"><span data-stu-id="10c46-192">The source to remove the reference to.</span></span> |<span data-ttu-id="10c46-193">openapi z systemem dotnet — Usuń *.\OpenAPI.js*</span><span class="sxs-lookup"><span data-stu-id="10c46-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="10c46-194">Odśwież</span><span class="sxs-lookup"><span data-stu-id="10c46-194">Refresh</span></span>

<span data-ttu-id="10c46-195">Odświeża lokalną wersję pliku, który został pobrany przy użyciu najnowszej zawartości z adresu URL pobierania.</span><span class="sxs-lookup"><span data-stu-id="10c46-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="10c46-196">Opcje</span><span class="sxs-lookup"><span data-stu-id="10c46-196">Options</span></span>

| <span data-ttu-id="10c46-197">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="10c46-197">Short option</span></span>| <span data-ttu-id="10c46-198">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="10c46-198">Long option</span></span>| <span data-ttu-id="10c46-199">Opis</span><span class="sxs-lookup"><span data-stu-id="10c46-199">Description</span></span> | <span data-ttu-id="10c46-200">Przykład</span><span class="sxs-lookup"><span data-stu-id="10c46-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="10c46-201">-p</span><span class="sxs-lookup"><span data-stu-id="10c46-201">-p</span></span>|<span data-ttu-id="10c46-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="10c46-202">--updateProject</span></span> | <span data-ttu-id="10c46-203">Projekt do działania.</span><span class="sxs-lookup"><span data-stu-id="10c46-203">The project to operate on.</span></span> | <span data-ttu-id="10c46-204">openapi dotnet *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="10c46-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="10c46-205">-h</span><span class="sxs-lookup"><span data-stu-id="10c46-205">-h</span></span>|<span data-ttu-id="10c46-206">--Pomoc</span><span class="sxs-lookup"><span data-stu-id="10c46-206">--help</span></span>|<span data-ttu-id="10c46-207">Pokaż informacje pomocy</span><span class="sxs-lookup"><span data-stu-id="10c46-207">Show help information</span></span>|<span data-ttu-id="10c46-208">openapi dotnet — pomoc</span><span class="sxs-lookup"><span data-stu-id="10c46-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="10c46-209">Argumenty</span><span class="sxs-lookup"><span data-stu-id="10c46-209">Arguments</span></span>

|  <span data-ttu-id="10c46-210">Argument</span><span class="sxs-lookup"><span data-stu-id="10c46-210">Argument</span></span>  | <span data-ttu-id="10c46-211">Opis</span><span class="sxs-lookup"><span data-stu-id="10c46-211">Description</span></span> | <span data-ttu-id="10c46-212">Przykład</span><span class="sxs-lookup"><span data-stu-id="10c46-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="10c46-213">adres URL źródła</span><span class="sxs-lookup"><span data-stu-id="10c46-213">source-URL</span></span> | <span data-ttu-id="10c46-214">Adres URL, z którego ma zostać odświeżone odwołanie.</span><span class="sxs-lookup"><span data-stu-id="10c46-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="10c46-215">Odświeżanie openapi dotnet `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="10c46-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
