---
title: Opracowywanie aplikacji ASP.NET Core przy użyciu OpenAPI
author: ryanbrandenburg
description: Pokazuje, w jaki sposób używać narzędzia "Microsoft. dotnet-openapi" w celu dodawania odwołań do plików OpenAPI.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 1924fb8ee5ac1ba8dc31d2175a336c8333c81fb2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775716"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="9c079-103">Opracowywanie aplikacji ASP.NET Core przy użyciu narzędzi OpenAPI</span><span class="sxs-lookup"><span data-stu-id="9c079-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="9c079-104">Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="9c079-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="9c079-105">[Microsoft. dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) to [globalne narzędzie platformy .NET Core](/dotnet/core/tools/global-tools) służące do zarządzania odwołaniami [openapi](https://github.com/OAI/OpenAPI-Specification) w ramach projektu.</span><span class="sxs-lookup"><span data-stu-id="9c079-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="9c079-106">Instalacja</span><span class="sxs-lookup"><span data-stu-id="9c079-106">Installation</span></span>

<span data-ttu-id="9c079-107">Aby zainstalować `Microsoft.dotnet-openapi`program, uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="9c079-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="9c079-108">Dodaj</span><span class="sxs-lookup"><span data-stu-id="9c079-108">Add</span></span>

<span data-ttu-id="9c079-109">Dodanie odwołania OpenAPI przy użyciu dowolnego polecenia na tej stronie powoduje dodanie `<OpenApiReference />` elementu podobnego do poniższego do pliku *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="9c079-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="9c079-110">Poprzednie odwołanie jest wymagane, aby aplikacja mogła wywołać wygenerowany kod klienta.</span><span class="sxs-lookup"><span data-stu-id="9c079-110">The preceding reference is required for the app to call the generated client code.</span></span>

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

### <a name="add-file"></a><span data-ttu-id="9c079-111">Dodaj plik</span><span class="sxs-lookup"><span data-stu-id="9c079-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="9c079-112">Opcje</span><span class="sxs-lookup"><span data-stu-id="9c079-112">Options</span></span>

| <span data-ttu-id="9c079-113">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="9c079-113">Short option</span></span>| <span data-ttu-id="9c079-114">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="9c079-114">Long option</span></span>| <span data-ttu-id="9c079-115">Opis</span><span class="sxs-lookup"><span data-stu-id="9c079-115">Description</span></span> | <span data-ttu-id="9c079-116">Przykład</span><span class="sxs-lookup"><span data-stu-id="9c079-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="9c079-117">-p</span><span class="sxs-lookup"><span data-stu-id="9c079-117">-p</span></span>|<span data-ttu-id="9c079-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="9c079-118">--updateProject</span></span> | <span data-ttu-id="9c079-119">Projekt do działania.</span><span class="sxs-lookup"><span data-stu-id="9c079-119">The project to operate on.</span></span> |<span data-ttu-id="9c079-120">openapi dotnet — Dodaj plik *--updateProject .\Ref.csproj* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="9c079-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="9c079-121">-c</span><span class="sxs-lookup"><span data-stu-id="9c079-121">-c</span></span>|<span data-ttu-id="9c079-122">--Generator kodu</span><span class="sxs-lookup"><span data-stu-id="9c079-122">--code-generator</span></span>| <span data-ttu-id="9c079-123">Generator kodu, który ma zostać zastosowany do odwołania.</span><span class="sxs-lookup"><span data-stu-id="9c079-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="9c079-124">Dostępne opcje `NSwagCSharp` to `NSwagTypeScript`i.</span><span class="sxs-lookup"><span data-stu-id="9c079-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="9c079-125">Jeśli `--code-generator` nie określono ustawień domyślnych narzędzi `NSwagCSharp`.</span><span class="sxs-lookup"><span data-stu-id="9c079-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="9c079-126">dotnet openapi Dodaj plik .\OpenApi.json--generator kodu</span><span class="sxs-lookup"><span data-stu-id="9c079-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="9c079-127">-h</span><span class="sxs-lookup"><span data-stu-id="9c079-127">-h</span></span>|<span data-ttu-id="9c079-128">--Pomoc</span><span class="sxs-lookup"><span data-stu-id="9c079-128">--help</span></span>|<span data-ttu-id="9c079-129">Pokaż informacje pomocy</span><span class="sxs-lookup"><span data-stu-id="9c079-129">Show help information</span></span>|<span data-ttu-id="9c079-130">openapi dotnet — Dodawanie pliku — pomoc</span><span class="sxs-lookup"><span data-stu-id="9c079-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="9c079-131">Argumenty</span><span class="sxs-lookup"><span data-stu-id="9c079-131">Arguments</span></span>

|  <span data-ttu-id="9c079-132">Argument</span><span class="sxs-lookup"><span data-stu-id="9c079-132">Argument</span></span>  | <span data-ttu-id="9c079-133">Opis</span><span class="sxs-lookup"><span data-stu-id="9c079-133">Description</span></span> | <span data-ttu-id="9c079-134">Przykład</span><span class="sxs-lookup"><span data-stu-id="9c079-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="9c079-135">plik źródłowy</span><span class="sxs-lookup"><span data-stu-id="9c079-135">source-file</span></span> | <span data-ttu-id="9c079-136">Źródło, z którego ma zostać utworzone odwołanie.</span><span class="sxs-lookup"><span data-stu-id="9c079-136">The source to create a reference from.</span></span> <span data-ttu-id="9c079-137">Musi to być plik OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="9c079-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="9c079-138">openapi dotnet — Dodaj plik *.\OpenAPI.JSON*</span><span class="sxs-lookup"><span data-stu-id="9c079-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="9c079-139">Dodaj adres URL</span><span class="sxs-lookup"><span data-stu-id="9c079-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="9c079-140">Opcje</span><span class="sxs-lookup"><span data-stu-id="9c079-140">Options</span></span>

| <span data-ttu-id="9c079-141">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="9c079-141">Short option</span></span>| <span data-ttu-id="9c079-142">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="9c079-142">Long option</span></span>| <span data-ttu-id="9c079-143">Opis</span><span class="sxs-lookup"><span data-stu-id="9c079-143">Description</span></span> | <span data-ttu-id="9c079-144">Przykład</span><span class="sxs-lookup"><span data-stu-id="9c079-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="9c079-145">-p</span><span class="sxs-lookup"><span data-stu-id="9c079-145">-p</span></span>|<span data-ttu-id="9c079-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="9c079-146">--updateProject</span></span> | <span data-ttu-id="9c079-147">Projekt do działania.</span><span class="sxs-lookup"><span data-stu-id="9c079-147">The project to operate on.</span></span> |<span data-ttu-id="9c079-148">openapi dotnet — Dodaj adres URL *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="9c079-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="9c079-149">-o</span><span class="sxs-lookup"><span data-stu-id="9c079-149">-o</span></span>|<span data-ttu-id="9c079-150">--Output-File</span><span class="sxs-lookup"><span data-stu-id="9c079-150">--output-file</span></span> | <span data-ttu-id="9c079-151">Miejsce, w którym ma zostać umieszczona kopia lokalna pliku OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="9c079-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="9c079-152">openapi dotnet Dodaj adres `https://contoso.com/openapi.json` URL- *-output-plik WebClient. JSON*</span><span class="sxs-lookup"><span data-stu-id="9c079-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="9c079-153">-c</span><span class="sxs-lookup"><span data-stu-id="9c079-153">-c</span></span>|<span data-ttu-id="9c079-154">--Generator kodu</span><span class="sxs-lookup"><span data-stu-id="9c079-154">--code-generator</span></span>| <span data-ttu-id="9c079-155">Generator kodu, który ma zostać zastosowany do odwołania.</span><span class="sxs-lookup"><span data-stu-id="9c079-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="9c079-156">Dostępne opcje `NSwagCSharp` to `NSwagTypeScript`i.</span><span class="sxs-lookup"><span data-stu-id="9c079-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="9c079-157">dotnet openapi Dodaj plik .\OpenApi.json--generator kodu</span><span class="sxs-lookup"><span data-stu-id="9c079-157">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="9c079-158">-h</span><span class="sxs-lookup"><span data-stu-id="9c079-158">-h</span></span>|<span data-ttu-id="9c079-159">--Pomoc</span><span class="sxs-lookup"><span data-stu-id="9c079-159">--help</span></span>|<span data-ttu-id="9c079-160">Pokaż informacje pomocy</span><span class="sxs-lookup"><span data-stu-id="9c079-160">Show help information</span></span>|<span data-ttu-id="9c079-161">openapi dotnet — Dodaj adres URL — pomoc</span><span class="sxs-lookup"><span data-stu-id="9c079-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="9c079-162">Argumenty</span><span class="sxs-lookup"><span data-stu-id="9c079-162">Arguments</span></span>

|  <span data-ttu-id="9c079-163">Argument</span><span class="sxs-lookup"><span data-stu-id="9c079-163">Argument</span></span>  | <span data-ttu-id="9c079-164">Opis</span><span class="sxs-lookup"><span data-stu-id="9c079-164">Description</span></span> | <span data-ttu-id="9c079-165">Przykład</span><span class="sxs-lookup"><span data-stu-id="9c079-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="9c079-166">adres URL źródła</span><span class="sxs-lookup"><span data-stu-id="9c079-166">source-URL</span></span> | <span data-ttu-id="9c079-167">Źródło, z którego ma zostać utworzone odwołanie.</span><span class="sxs-lookup"><span data-stu-id="9c079-167">The source to create a reference from.</span></span> <span data-ttu-id="9c079-168">Musi być adresem URL.</span><span class="sxs-lookup"><span data-stu-id="9c079-168">Must be a URL.</span></span> |<span data-ttu-id="9c079-169">Dodawanie adresu URL openapi dotnet`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="9c079-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="9c079-170">Usuń</span><span class="sxs-lookup"><span data-stu-id="9c079-170">Remove</span></span>

<span data-ttu-id="9c079-171">Usuwa odwołanie OpenAPI pasujące do podanej nazwy pliku z pliku *csproj* .</span><span class="sxs-lookup"><span data-stu-id="9c079-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="9c079-172">Po usunięciu odwołania OpenAPI klienci nie będą wygenerował.</span><span class="sxs-lookup"><span data-stu-id="9c079-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="9c079-173">Pliki Local *. JSON* i *. YAML* są usuwane.</span><span class="sxs-lookup"><span data-stu-id="9c079-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="9c079-174">Opcje</span><span class="sxs-lookup"><span data-stu-id="9c079-174">Options</span></span>

| <span data-ttu-id="9c079-175">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="9c079-175">Short option</span></span>| <span data-ttu-id="9c079-176">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="9c079-176">Long option</span></span>| <span data-ttu-id="9c079-177">Opis</span><span class="sxs-lookup"><span data-stu-id="9c079-177">Description</span></span>| <span data-ttu-id="9c079-178">Przykład</span><span class="sxs-lookup"><span data-stu-id="9c079-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="9c079-179">-p</span><span class="sxs-lookup"><span data-stu-id="9c079-179">-p</span></span>|<span data-ttu-id="9c079-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="9c079-180">--updateProject</span></span> | <span data-ttu-id="9c079-181">Projekt do działania.</span><span class="sxs-lookup"><span data-stu-id="9c079-181">The project to operate on.</span></span> |<span data-ttu-id="9c079-182">openapi dotnet *--updateProject .\Ref.csproj* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="9c079-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="9c079-183">-h</span><span class="sxs-lookup"><span data-stu-id="9c079-183">-h</span></span>|<span data-ttu-id="9c079-184">--Pomoc</span><span class="sxs-lookup"><span data-stu-id="9c079-184">--help</span></span>|<span data-ttu-id="9c079-185">Pokaż informacje pomocy</span><span class="sxs-lookup"><span data-stu-id="9c079-185">Show help information</span></span>|<span data-ttu-id="9c079-186">openapi dotnet — pomoc</span><span class="sxs-lookup"><span data-stu-id="9c079-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="9c079-187">Argumenty</span><span class="sxs-lookup"><span data-stu-id="9c079-187">Arguments</span></span>

|  <span data-ttu-id="9c079-188">Argument</span><span class="sxs-lookup"><span data-stu-id="9c079-188">Argument</span></span>  | <span data-ttu-id="9c079-189">Opis</span><span class="sxs-lookup"><span data-stu-id="9c079-189">Description</span></span>| <span data-ttu-id="9c079-190">Przykład</span><span class="sxs-lookup"><span data-stu-id="9c079-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="9c079-191">plik źródłowy</span><span class="sxs-lookup"><span data-stu-id="9c079-191">source-file</span></span> | <span data-ttu-id="9c079-192">Źródło, do którego ma zostać usunięte odwołanie.</span><span class="sxs-lookup"><span data-stu-id="9c079-192">The source to remove the reference to.</span></span> |<span data-ttu-id="9c079-193">openapi/Usuń *.\OpenAPI.JSON* dotnet</span><span class="sxs-lookup"><span data-stu-id="9c079-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="9c079-194">Odświeżanie</span><span class="sxs-lookup"><span data-stu-id="9c079-194">Refresh</span></span>

<span data-ttu-id="9c079-195">Odświeża lokalną wersję pliku, który został pobrany przy użyciu najnowszej zawartości z adresu URL pobierania.</span><span class="sxs-lookup"><span data-stu-id="9c079-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="9c079-196">Opcje</span><span class="sxs-lookup"><span data-stu-id="9c079-196">Options</span></span>

| <span data-ttu-id="9c079-197">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="9c079-197">Short option</span></span>| <span data-ttu-id="9c079-198">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="9c079-198">Long option</span></span>| <span data-ttu-id="9c079-199">Opis</span><span class="sxs-lookup"><span data-stu-id="9c079-199">Description</span></span> | <span data-ttu-id="9c079-200">Przykład</span><span class="sxs-lookup"><span data-stu-id="9c079-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="9c079-201">-p</span><span class="sxs-lookup"><span data-stu-id="9c079-201">-p</span></span>|<span data-ttu-id="9c079-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="9c079-202">--updateProject</span></span> | <span data-ttu-id="9c079-203">Projekt do działania.</span><span class="sxs-lookup"><span data-stu-id="9c079-203">The project to operate on.</span></span> | <span data-ttu-id="9c079-204">openapi dotnet *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="9c079-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="9c079-205">-h</span><span class="sxs-lookup"><span data-stu-id="9c079-205">-h</span></span>|<span data-ttu-id="9c079-206">--Pomoc</span><span class="sxs-lookup"><span data-stu-id="9c079-206">--help</span></span>|<span data-ttu-id="9c079-207">Pokaż informacje pomocy</span><span class="sxs-lookup"><span data-stu-id="9c079-207">Show help information</span></span>|<span data-ttu-id="9c079-208">openapi dotnet — pomoc</span><span class="sxs-lookup"><span data-stu-id="9c079-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="9c079-209">Argumenty</span><span class="sxs-lookup"><span data-stu-id="9c079-209">Arguments</span></span>

|  <span data-ttu-id="9c079-210">Argument</span><span class="sxs-lookup"><span data-stu-id="9c079-210">Argument</span></span>  | <span data-ttu-id="9c079-211">Opis</span><span class="sxs-lookup"><span data-stu-id="9c079-211">Description</span></span> | <span data-ttu-id="9c079-212">Przykład</span><span class="sxs-lookup"><span data-stu-id="9c079-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="9c079-213">adres URL źródła</span><span class="sxs-lookup"><span data-stu-id="9c079-213">source-URL</span></span> | <span data-ttu-id="9c079-214">Adres URL, z którego ma zostać odświeżone odwołanie.</span><span class="sxs-lookup"><span data-stu-id="9c079-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="9c079-215">Odświeżanie openapi dotnet`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="9c079-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
