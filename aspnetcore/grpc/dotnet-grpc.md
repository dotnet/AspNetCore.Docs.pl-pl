---
title: Zarządzanie odwołaniami narzędzia Protobuf za pomocą narzędzia dotnet-GRPC
author: juntaoluo
description: Dowiedz się więcej na temat dodawania, aktualizowania, usuwania i wyświetlania protobuf odwołań za pomocą narzędzia dotnet-GRPC Global.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
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
uid: grpc/dotnet-grpc
ms.openlocfilehash: f34e1543d9695e138a85db3b79e013cf5fb6d138
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059913"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="78044-103">Zarządzanie odwołaniami narzędzia Protobuf za pomocą narzędzia dotnet-GRPC</span><span class="sxs-lookup"><span data-stu-id="78044-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="78044-104">Przez [Jan Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="78044-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="78044-105">`dotnet-grpc` jest globalnym narzędziem platformy .NET Core do zarządzania odwołaniami [protobuf (*. proto*)](xref:grpc/basics#proto-file) w ramach projektu .NET gRPC.</span><span class="sxs-lookup"><span data-stu-id="78044-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf (*.proto*)](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="78044-106">Narzędzie może służyć do dodawania, odświeżania, usuwania i wyświetlania listy odwołań protobuf.</span><span class="sxs-lookup"><span data-stu-id="78044-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="78044-107">Instalacja</span><span class="sxs-lookup"><span data-stu-id="78044-107">Installation</span></span>

<span data-ttu-id="78044-108">Aby zainstalować `dotnet-grpc` [Narzędzie globalne platformy .NET Core](/dotnet/core/tools/global-tools), uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="78044-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="78044-109">Dodaj odwołania</span><span class="sxs-lookup"><span data-stu-id="78044-109">Add references</span></span>

<span data-ttu-id="78044-110">`dotnet-grpc` może służyć do dodawania odwołań protobuf jako `<Protobuf />` elementów do pliku *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="78044-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="78044-111">Odwołania do protobuf są używane do generowania zasobów klienta i/lub serwera w języku C#.</span><span class="sxs-lookup"><span data-stu-id="78044-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="78044-112">To `dotnet-grpc` Narzędzie może:</span><span class="sxs-lookup"><span data-stu-id="78044-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="78044-113">Utwórz odwołanie protobuf z plików lokalnych na dysku.</span><span class="sxs-lookup"><span data-stu-id="78044-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="78044-114">Utwórz odwołanie protobuf z pliku zdalnego określonego przez adres URL.</span><span class="sxs-lookup"><span data-stu-id="78044-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="78044-115">Upewnij się, że odpowiednie zależności pakietu gRPC są dodawane do projektu.</span><span class="sxs-lookup"><span data-stu-id="78044-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="78044-116">Na przykład `Grpc.AspNetCore` pakiet zostanie dodany do aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="78044-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="78044-117">`Grpc.AspNetCore` zawiera biblioteki serwera gRPC i klienta oraz obsługę narzędzi.</span><span class="sxs-lookup"><span data-stu-id="78044-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="78044-118">Alternatywnie, `Grpc.Net.Client` `Grpc.Tools` pakiety i, `Google.Protobuf` które zawierają tylko biblioteki i narzędzia klienckie gRPC, są dodawane do aplikacji konsoli.</span><span class="sxs-lookup"><span data-stu-id="78044-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="78044-119">Dodaj plik</span><span class="sxs-lookup"><span data-stu-id="78044-119">Add file</span></span>

<span data-ttu-id="78044-120">`add-file`Polecenie służy do dodawania plików lokalnych na dysku jako odwołań protobuf.</span><span class="sxs-lookup"><span data-stu-id="78044-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="78044-121">Podane ścieżki plików:</span><span class="sxs-lookup"><span data-stu-id="78044-121">The file paths provided:</span></span>

* <span data-ttu-id="78044-122">Może być względna względem bieżącego katalogu lub ścieżek bezwzględnych.</span><span class="sxs-lookup"><span data-stu-id="78044-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="78044-123">Mogą zawierać symbole wieloznaczne dla [obsługi symboli wieloznacznych](https://wikipedia.org/wiki/Glob_(programming))plików opartych na wzorcu.</span><span class="sxs-lookup"><span data-stu-id="78044-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="78044-124">Jeśli jakiekolwiek pliki znajdują się poza katalogiem projektu, `Link` element zostanie dodany w celu wyświetlenia pliku w folderze `Protos` w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="78044-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="78044-125">Użycie</span><span class="sxs-lookup"><span data-stu-id="78044-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="78044-126">Argumenty</span><span class="sxs-lookup"><span data-stu-id="78044-126">Arguments</span></span>

| <span data-ttu-id="78044-127">Argument</span><span class="sxs-lookup"><span data-stu-id="78044-127">Argument</span></span> | <span data-ttu-id="78044-128">Opis</span><span class="sxs-lookup"><span data-stu-id="78044-128">Description</span></span> |
|-|-|
| <span data-ttu-id="78044-129">files</span><span class="sxs-lookup"><span data-stu-id="78044-129">files</span></span> | <span data-ttu-id="78044-130">Odwołuje się do pliku protobuf.</span><span class="sxs-lookup"><span data-stu-id="78044-130">The protobuf file references.</span></span> <span data-ttu-id="78044-131">Mogą to być ścieżki do globalizowania dla lokalnych plików protobuf.</span><span class="sxs-lookup"><span data-stu-id="78044-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="78044-132">Opcje</span><span class="sxs-lookup"><span data-stu-id="78044-132">Options</span></span>

| <span data-ttu-id="78044-133">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="78044-133">Short option</span></span> | <span data-ttu-id="78044-134">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="78044-134">Long option</span></span> | <span data-ttu-id="78044-135">Opis</span><span class="sxs-lookup"><span data-stu-id="78044-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="78044-136">-p</span><span class="sxs-lookup"><span data-stu-id="78044-136">-p</span></span> | <span data-ttu-id="78044-137">--projekt</span><span class="sxs-lookup"><span data-stu-id="78044-137">--project</span></span> | <span data-ttu-id="78044-138">Ścieżka do pliku projektu, na którym mają być wykonywane działania.</span><span class="sxs-lookup"><span data-stu-id="78044-138">The path to the project file to operate on.</span></span> <span data-ttu-id="78044-139">Jeśli plik nie zostanie określony, polecenie przeszukuje bieżący katalog.</span><span class="sxs-lookup"><span data-stu-id="78044-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="78044-140">-s</span><span class="sxs-lookup"><span data-stu-id="78044-140">-s</span></span> | <span data-ttu-id="78044-141">--usługi</span><span class="sxs-lookup"><span data-stu-id="78044-141">--services</span></span> | <span data-ttu-id="78044-142">Typ usług gRPC, które mają zostać wygenerowane.</span><span class="sxs-lookup"><span data-stu-id="78044-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="78044-143">Jeśli `Default` jest określony, `Both` jest używany dla projektów sieci Web i `Client` jest używany dla projektów nieopartych na sieci Web.</span><span class="sxs-lookup"><span data-stu-id="78044-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="78044-144">Akceptowane wartości to,,,, `Both` `Client` `Default` `None` `Server` .</span><span class="sxs-lookup"><span data-stu-id="78044-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="78044-145">-i</span><span class="sxs-lookup"><span data-stu-id="78044-145">-i</span></span> | <span data-ttu-id="78044-146">--dodatkowe-import-katalogów</span><span class="sxs-lookup"><span data-stu-id="78044-146">--additional-import-dirs</span></span> | <span data-ttu-id="78044-147">Dodatkowe katalogi, które mają być używane podczas rozpoznawania importu dla plików protobuf.</span><span class="sxs-lookup"><span data-stu-id="78044-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="78044-148">Jest to rozdzielana średnikami lista ścieżek.</span><span class="sxs-lookup"><span data-stu-id="78044-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="78044-149">--dostęp</span><span class="sxs-lookup"><span data-stu-id="78044-149">--access</span></span> | <span data-ttu-id="78044-150">Modyfikator dostępu, który ma być używany dla wygenerowanych klas języka C#.</span><span class="sxs-lookup"><span data-stu-id="78044-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="78044-151">Wartość domyślna to `Public`.</span><span class="sxs-lookup"><span data-stu-id="78044-151">The default value is `Public`.</span></span> <span data-ttu-id="78044-152">Akceptowane wartości to `Internal` i `Public` .</span><span class="sxs-lookup"><span data-stu-id="78044-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="78044-153">Dodaj adres URL</span><span class="sxs-lookup"><span data-stu-id="78044-153">Add URL</span></span>

<span data-ttu-id="78044-154">`add-url`Polecenie służy do dodawania pliku zdalnego określonego przez źródłowy adres URL jako odwołanie protobuf.</span><span class="sxs-lookup"><span data-stu-id="78044-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="78044-155">Należy podać ścieżkę pliku, aby określić, gdzie pobrać plik zdalny.</span><span class="sxs-lookup"><span data-stu-id="78044-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="78044-156">Ścieżka pliku może być względna względem bieżącego katalogu lub ścieżki bezwzględnej.</span><span class="sxs-lookup"><span data-stu-id="78044-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="78044-157">Jeśli ścieżka pliku znajduje się poza katalogiem projektu, `Link` element zostanie dodany w celu wyświetlenia pliku w folderze wirtualnym `Protos` w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="78044-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="78044-158">Użycie</span><span class="sxs-lookup"><span data-stu-id="78044-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="78044-159">Argumenty</span><span class="sxs-lookup"><span data-stu-id="78044-159">Arguments</span></span>

| <span data-ttu-id="78044-160">Argument</span><span class="sxs-lookup"><span data-stu-id="78044-160">Argument</span></span> | <span data-ttu-id="78044-161">Opis</span><span class="sxs-lookup"><span data-stu-id="78044-161">Description</span></span> |
|-|-|
| <span data-ttu-id="78044-162">url</span><span class="sxs-lookup"><span data-stu-id="78044-162">url</span></span> | <span data-ttu-id="78044-163">Adres URL pliku protobuf zdalnego.</span><span class="sxs-lookup"><span data-stu-id="78044-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="78044-164">Opcje</span><span class="sxs-lookup"><span data-stu-id="78044-164">Options</span></span>

| <span data-ttu-id="78044-165">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="78044-165">Short option</span></span> | <span data-ttu-id="78044-166">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="78044-166">Long option</span></span> | <span data-ttu-id="78044-167">Opis</span><span class="sxs-lookup"><span data-stu-id="78044-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="78044-168">-o</span><span class="sxs-lookup"><span data-stu-id="78044-168">-o</span></span> | <span data-ttu-id="78044-169">--output</span><span class="sxs-lookup"><span data-stu-id="78044-169">--output</span></span> | <span data-ttu-id="78044-170">Określa ścieżkę pobierania pliku protobuf zdalnego.</span><span class="sxs-lookup"><span data-stu-id="78044-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="78044-171">Ta opcja jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="78044-171">This is a required option.</span></span>
| <span data-ttu-id="78044-172">-p</span><span class="sxs-lookup"><span data-stu-id="78044-172">-p</span></span> | <span data-ttu-id="78044-173">--projekt</span><span class="sxs-lookup"><span data-stu-id="78044-173">--project</span></span> | <span data-ttu-id="78044-174">Ścieżka do pliku projektu, na którym mają być wykonywane działania.</span><span class="sxs-lookup"><span data-stu-id="78044-174">The path to the project file to operate on.</span></span> <span data-ttu-id="78044-175">Jeśli plik nie zostanie określony, polecenie przeszukuje bieżący katalog.</span><span class="sxs-lookup"><span data-stu-id="78044-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="78044-176">-s</span><span class="sxs-lookup"><span data-stu-id="78044-176">-s</span></span> | <span data-ttu-id="78044-177">--usługi</span><span class="sxs-lookup"><span data-stu-id="78044-177">--services</span></span> | <span data-ttu-id="78044-178">Typ usług gRPC, które mają zostać wygenerowane.</span><span class="sxs-lookup"><span data-stu-id="78044-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="78044-179">Jeśli `Default` jest określony, `Both` jest używany dla projektów sieci Web i `Client` jest używany dla projektów nieopartych na sieci Web.</span><span class="sxs-lookup"><span data-stu-id="78044-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="78044-180">Akceptowane wartości to,,,, `Both` `Client` `Default` `None` `Server` .</span><span class="sxs-lookup"><span data-stu-id="78044-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="78044-181">-i</span><span class="sxs-lookup"><span data-stu-id="78044-181">-i</span></span> | <span data-ttu-id="78044-182">--dodatkowe-import-katalogów</span><span class="sxs-lookup"><span data-stu-id="78044-182">--additional-import-dirs</span></span> | <span data-ttu-id="78044-183">Dodatkowe katalogi, które mają być używane podczas rozpoznawania importu dla plików protobuf.</span><span class="sxs-lookup"><span data-stu-id="78044-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="78044-184">Jest to rozdzielana średnikami lista ścieżek.</span><span class="sxs-lookup"><span data-stu-id="78044-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="78044-185">--dostęp</span><span class="sxs-lookup"><span data-stu-id="78044-185">--access</span></span> | <span data-ttu-id="78044-186">Modyfikator dostępu, który ma być używany dla wygenerowanych klas języka C#.</span><span class="sxs-lookup"><span data-stu-id="78044-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="78044-187">Wartość domyślna to `Public`.</span><span class="sxs-lookup"><span data-stu-id="78044-187">Default value is `Public`.</span></span> <span data-ttu-id="78044-188">Akceptowane wartości to `Internal` i `Public` .</span><span class="sxs-lookup"><span data-stu-id="78044-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="78044-189">Usuń</span><span class="sxs-lookup"><span data-stu-id="78044-189">Remove</span></span>

<span data-ttu-id="78044-190">`remove`Polecenie służy do usuwania odwołań protobuf z pliku *csproj* .</span><span class="sxs-lookup"><span data-stu-id="78044-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="78044-191">Polecenie akceptuje argumenty ścieżki i źródłowe adresy URL jako argumenty.</span><span class="sxs-lookup"><span data-stu-id="78044-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="78044-192">Narzędzie:</span><span class="sxs-lookup"><span data-stu-id="78044-192">The tool:</span></span>

* <span data-ttu-id="78044-193">Usuwa odwołanie protobuf.</span><span class="sxs-lookup"><span data-stu-id="78044-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="78044-194">Nie usuwa pliku *. proto* , nawet jeśli został pierwotnie pobrany ze zdalnego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="78044-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="78044-195">Użycie</span><span class="sxs-lookup"><span data-stu-id="78044-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="78044-196">Argumenty</span><span class="sxs-lookup"><span data-stu-id="78044-196">Arguments</span></span>

| <span data-ttu-id="78044-197">Argument</span><span class="sxs-lookup"><span data-stu-id="78044-197">Argument</span></span> | <span data-ttu-id="78044-198">Opis</span><span class="sxs-lookup"><span data-stu-id="78044-198">Description</span></span> |
|-|-|
| <span data-ttu-id="78044-199">odwołania</span><span class="sxs-lookup"><span data-stu-id="78044-199">references</span></span> | <span data-ttu-id="78044-200">Adresy URL lub ścieżki plików odwołań protobuf do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="78044-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="78044-201">Opcje</span><span class="sxs-lookup"><span data-stu-id="78044-201">Options</span></span>

| <span data-ttu-id="78044-202">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="78044-202">Short option</span></span> | <span data-ttu-id="78044-203">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="78044-203">Long option</span></span> | <span data-ttu-id="78044-204">Opis</span><span class="sxs-lookup"><span data-stu-id="78044-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="78044-205">-p</span><span class="sxs-lookup"><span data-stu-id="78044-205">-p</span></span> | <span data-ttu-id="78044-206">--projekt</span><span class="sxs-lookup"><span data-stu-id="78044-206">--project</span></span> | <span data-ttu-id="78044-207">Ścieżka do pliku projektu, na którym mają być wykonywane działania.</span><span class="sxs-lookup"><span data-stu-id="78044-207">The path to the project file to operate on.</span></span> <span data-ttu-id="78044-208">Jeśli plik nie zostanie określony, polecenie przeszukuje bieżący katalog.</span><span class="sxs-lookup"><span data-stu-id="78044-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="78044-209">Odśwież</span><span class="sxs-lookup"><span data-stu-id="78044-209">Refresh</span></span>

<span data-ttu-id="78044-210">`refresh`Polecenie służy do aktualizowania odwołania zdalnego z najnowszą zawartością ze źródłowego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="78044-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="78044-211">Aby określić odwołanie, które ma zostać zaktualizowane, można użyć zarówno ścieżki pliku pobierania, jak i źródłowego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="78044-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="78044-212">Uwaga:</span><span class="sxs-lookup"><span data-stu-id="78044-212">Note:</span></span>

* <span data-ttu-id="78044-213">Skróty zawartości plików są porównywane, aby określić, czy plik lokalny powinien zostać zaktualizowany.</span><span class="sxs-lookup"><span data-stu-id="78044-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="78044-214">Nie są porównywane żadne informacje o znaczniku czasu.</span><span class="sxs-lookup"><span data-stu-id="78044-214">No timestamp information is compared.</span></span>

<span data-ttu-id="78044-215">Narzędzie zawsze zastępuje plik lokalny plikiem zdalnym, jeśli jest wymagana aktualizacja.</span><span class="sxs-lookup"><span data-stu-id="78044-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="78044-216">Użycie</span><span class="sxs-lookup"><span data-stu-id="78044-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="78044-217">Argumenty</span><span class="sxs-lookup"><span data-stu-id="78044-217">Arguments</span></span>

| <span data-ttu-id="78044-218">Argument</span><span class="sxs-lookup"><span data-stu-id="78044-218">Argument</span></span> | <span data-ttu-id="78044-219">Opis</span><span class="sxs-lookup"><span data-stu-id="78044-219">Description</span></span> |
|-|-|
| <span data-ttu-id="78044-220">odwołania</span><span class="sxs-lookup"><span data-stu-id="78044-220">references</span></span> | <span data-ttu-id="78044-221">Adresy URL lub ścieżki plików do zdalnych odwołań protobuf, które powinny zostać zaktualizowane.</span><span class="sxs-lookup"><span data-stu-id="78044-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="78044-222">Pozostaw ten argument pusty, aby odświeżyć wszystkie odwołania zdalne.</span><span class="sxs-lookup"><span data-stu-id="78044-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="78044-223">Opcje</span><span class="sxs-lookup"><span data-stu-id="78044-223">Options</span></span>

| <span data-ttu-id="78044-224">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="78044-224">Short option</span></span> | <span data-ttu-id="78044-225">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="78044-225">Long option</span></span> | <span data-ttu-id="78044-226">Opis</span><span class="sxs-lookup"><span data-stu-id="78044-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="78044-227">-p</span><span class="sxs-lookup"><span data-stu-id="78044-227">-p</span></span> | <span data-ttu-id="78044-228">--projekt</span><span class="sxs-lookup"><span data-stu-id="78044-228">--project</span></span> | <span data-ttu-id="78044-229">Ścieżka do pliku projektu, na którym mają być wykonywane działania.</span><span class="sxs-lookup"><span data-stu-id="78044-229">The path to the project file to operate on.</span></span> <span data-ttu-id="78044-230">Jeśli plik nie zostanie określony, polecenie przeszukuje bieżący katalog.</span><span class="sxs-lookup"><span data-stu-id="78044-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="78044-231">--osuszyć-Run</span><span class="sxs-lookup"><span data-stu-id="78044-231">--dry-run</span></span> | <span data-ttu-id="78044-232">Wyświetla listę plików, które zostaną zaktualizowane bez pobierania nowej zawartości.</span><span class="sxs-lookup"><span data-stu-id="78044-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="78044-233">Lista</span><span class="sxs-lookup"><span data-stu-id="78044-233">List</span></span>

<span data-ttu-id="78044-234">`list`Polecenie jest używane do wyświetlania wszystkich odwołań protobuf w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="78044-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="78044-235">Jeśli wszystkie wartości w kolumnie są wartościami domyślnymi, kolumna może zostać pominięta.</span><span class="sxs-lookup"><span data-stu-id="78044-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="78044-236">Użycie</span><span class="sxs-lookup"><span data-stu-id="78044-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="78044-237">Opcje</span><span class="sxs-lookup"><span data-stu-id="78044-237">Options</span></span>

| <span data-ttu-id="78044-238">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="78044-238">Short option</span></span> | <span data-ttu-id="78044-239">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="78044-239">Long option</span></span> | <span data-ttu-id="78044-240">Opis</span><span class="sxs-lookup"><span data-stu-id="78044-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="78044-241">-p</span><span class="sxs-lookup"><span data-stu-id="78044-241">-p</span></span> | <span data-ttu-id="78044-242">--projekt</span><span class="sxs-lookup"><span data-stu-id="78044-242">--project</span></span> | <span data-ttu-id="78044-243">Ścieżka do pliku projektu, na którym mają być wykonywane działania.</span><span class="sxs-lookup"><span data-stu-id="78044-243">The path to the project file to operate on.</span></span> <span data-ttu-id="78044-244">Jeśli plik nie zostanie określony, polecenie przeszukuje bieżący katalog.</span><span class="sxs-lookup"><span data-stu-id="78044-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78044-245">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="78044-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
