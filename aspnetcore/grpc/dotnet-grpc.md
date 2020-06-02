---
title: Zarządzanie odwołaniami narzędzia Protobuf za pomocą narzędzia dotnet-GRPC
author: juntaoluo
description: Dowiedz się więcej na temat dodawania, aktualizowania, usuwania i wyświetlania protobuf odwołań za pomocą narzędzia dotnet-GRPC Global.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/dotnet-grpc
ms.openlocfilehash: 0990013947be2cee5045deac92efc3c6bcf12e03
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768838"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="16fa0-103">Zarządzanie odwołaniami narzędzia Protobuf za pomocą narzędzia dotnet-GRPC</span><span class="sxs-lookup"><span data-stu-id="16fa0-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="16fa0-104">Przez [Jan Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="16fa0-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="16fa0-105">`dotnet-grpc`jest globalnym narzędziem platformy .NET Core do zarządzania odwołaniami [protobuf (*. proto*)](xref:grpc/basics#proto-file) w ramach projektu .NET gRPC.</span><span class="sxs-lookup"><span data-stu-id="16fa0-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf (*.proto*)](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="16fa0-106">Narzędzie może służyć do dodawania, odświeżania, usuwania i wyświetlania listy odwołań protobuf.</span><span class="sxs-lookup"><span data-stu-id="16fa0-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="16fa0-107">Instalacja</span><span class="sxs-lookup"><span data-stu-id="16fa0-107">Installation</span></span>

<span data-ttu-id="16fa0-108">Aby zainstalować `dotnet-grpc` [Narzędzie globalne platformy .NET Core](/dotnet/core/tools/global-tools), uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="16fa0-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="16fa0-109">Dodaj odwołania</span><span class="sxs-lookup"><span data-stu-id="16fa0-109">Add references</span></span>

<span data-ttu-id="16fa0-110">`dotnet-grpc`może służyć do dodawania odwołań protobuf jako `<Protobuf />` elementów do pliku *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="16fa0-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="16fa0-111">Odwołania do protobuf są używane do generowania zasobów klienta i/lub serwera w języku C#.</span><span class="sxs-lookup"><span data-stu-id="16fa0-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="16fa0-112">To `dotnet-grpc` Narzędzie może:</span><span class="sxs-lookup"><span data-stu-id="16fa0-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="16fa0-113">Utwórz odwołanie protobuf z plików lokalnych na dysku.</span><span class="sxs-lookup"><span data-stu-id="16fa0-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="16fa0-114">Utwórz odwołanie protobuf z pliku zdalnego określonego przez adres URL.</span><span class="sxs-lookup"><span data-stu-id="16fa0-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="16fa0-115">Upewnij się, że odpowiednie zależności pakietu gRPC są dodawane do projektu.</span><span class="sxs-lookup"><span data-stu-id="16fa0-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="16fa0-116">Na przykład `Grpc.AspNetCore` pakiet zostanie dodany do aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="16fa0-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="16fa0-117">`Grpc.AspNetCore`zawiera biblioteki serwera gRPC i klienta oraz obsługę narzędzi.</span><span class="sxs-lookup"><span data-stu-id="16fa0-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="16fa0-118">Alternatywnie, `Grpc.Net.Client` `Grpc.Tools` pakiety i, `Google.Protobuf` które zawierają tylko biblioteki i narzędzia klienckie gRPC, są dodawane do aplikacji konsoli.</span><span class="sxs-lookup"><span data-stu-id="16fa0-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="16fa0-119">Dodaj plik</span><span class="sxs-lookup"><span data-stu-id="16fa0-119">Add file</span></span>

<span data-ttu-id="16fa0-120">`add-file`Polecenie służy do dodawania plików lokalnych na dysku jako odwołań protobuf.</span><span class="sxs-lookup"><span data-stu-id="16fa0-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="16fa0-121">Podane ścieżki plików:</span><span class="sxs-lookup"><span data-stu-id="16fa0-121">The file paths provided:</span></span>

* <span data-ttu-id="16fa0-122">Może być względna względem bieżącego katalogu lub ścieżek bezwzględnych.</span><span class="sxs-lookup"><span data-stu-id="16fa0-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="16fa0-123">Mogą zawierać symbole wieloznaczne dla [obsługi symboli wieloznacznych](https://wikipedia.org/wiki/Glob_(programming))plików opartych na wzorcu.</span><span class="sxs-lookup"><span data-stu-id="16fa0-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="16fa0-124">Jeśli jakiekolwiek pliki znajdują się poza katalogiem projektu, `Link` element zostanie dodany w celu wyświetlenia pliku w folderze `Protos` w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="16fa0-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="16fa0-125">Użycie</span><span class="sxs-lookup"><span data-stu-id="16fa0-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="16fa0-126">Argumenty</span><span class="sxs-lookup"><span data-stu-id="16fa0-126">Arguments</span></span>

| <span data-ttu-id="16fa0-127">Argument</span><span class="sxs-lookup"><span data-stu-id="16fa0-127">Argument</span></span> | <span data-ttu-id="16fa0-128">Opis</span><span class="sxs-lookup"><span data-stu-id="16fa0-128">Description</span></span> |
|-|-|
| <span data-ttu-id="16fa0-129">files</span><span class="sxs-lookup"><span data-stu-id="16fa0-129">files</span></span> | <span data-ttu-id="16fa0-130">Odwołuje się do pliku protobuf.</span><span class="sxs-lookup"><span data-stu-id="16fa0-130">The protobuf file references.</span></span> <span data-ttu-id="16fa0-131">Mogą to być ścieżki do globalizowania dla lokalnych plików protobuf.</span><span class="sxs-lookup"><span data-stu-id="16fa0-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="16fa0-132">Opcje</span><span class="sxs-lookup"><span data-stu-id="16fa0-132">Options</span></span>

| <span data-ttu-id="16fa0-133">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="16fa0-133">Short option</span></span> | <span data-ttu-id="16fa0-134">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="16fa0-134">Long option</span></span> | <span data-ttu-id="16fa0-135">Opis</span><span class="sxs-lookup"><span data-stu-id="16fa0-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="16fa0-136">-p</span><span class="sxs-lookup"><span data-stu-id="16fa0-136">-p</span></span> | <span data-ttu-id="16fa0-137">--projekt</span><span class="sxs-lookup"><span data-stu-id="16fa0-137">--project</span></span> | <span data-ttu-id="16fa0-138">Ścieżka do pliku projektu, na którym mają być wykonywane działania.</span><span class="sxs-lookup"><span data-stu-id="16fa0-138">The path to the project file to operate on.</span></span> <span data-ttu-id="16fa0-139">Jeśli plik nie zostanie określony, polecenie przeszukuje bieżący katalog.</span><span class="sxs-lookup"><span data-stu-id="16fa0-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="16fa0-140">-s</span><span class="sxs-lookup"><span data-stu-id="16fa0-140">-s</span></span> | <span data-ttu-id="16fa0-141">--usługi</span><span class="sxs-lookup"><span data-stu-id="16fa0-141">--services</span></span> | <span data-ttu-id="16fa0-142">Typ usług gRPC, które mają zostać wygenerowane.</span><span class="sxs-lookup"><span data-stu-id="16fa0-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="16fa0-143">Jeśli `Default` jest określony, `Both` jest używany dla projektów sieci Web i `Client` jest używany dla projektów nieopartych na sieci Web.</span><span class="sxs-lookup"><span data-stu-id="16fa0-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="16fa0-144">Akceptowane wartości to,,,, `Both` `Client` `Default` `None` `Server` .</span><span class="sxs-lookup"><span data-stu-id="16fa0-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="16fa0-145">-i</span><span class="sxs-lookup"><span data-stu-id="16fa0-145">-i</span></span> | <span data-ttu-id="16fa0-146">--dodatkowe-import-katalogów</span><span class="sxs-lookup"><span data-stu-id="16fa0-146">--additional-import-dirs</span></span> | <span data-ttu-id="16fa0-147">Dodatkowe katalogi, które mają być używane podczas rozpoznawania importu dla plików protobuf.</span><span class="sxs-lookup"><span data-stu-id="16fa0-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="16fa0-148">Jest to rozdzielana średnikami lista ścieżek.</span><span class="sxs-lookup"><span data-stu-id="16fa0-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="16fa0-149">--dostęp</span><span class="sxs-lookup"><span data-stu-id="16fa0-149">--access</span></span> | <span data-ttu-id="16fa0-150">Modyfikator dostępu, który ma być używany dla wygenerowanych klas języka C#.</span><span class="sxs-lookup"><span data-stu-id="16fa0-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="16fa0-151">Wartość domyślna to `Public`.</span><span class="sxs-lookup"><span data-stu-id="16fa0-151">The default value is `Public`.</span></span> <span data-ttu-id="16fa0-152">Akceptowane wartości to `Internal` i `Public` .</span><span class="sxs-lookup"><span data-stu-id="16fa0-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="16fa0-153">Dodaj adres URL</span><span class="sxs-lookup"><span data-stu-id="16fa0-153">Add URL</span></span>

<span data-ttu-id="16fa0-154">`add-url`Polecenie służy do dodawania pliku zdalnego określonego przez źródłowy adres URL jako odwołanie protobuf.</span><span class="sxs-lookup"><span data-stu-id="16fa0-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="16fa0-155">Należy podać ścieżkę pliku, aby określić, gdzie pobrać plik zdalny.</span><span class="sxs-lookup"><span data-stu-id="16fa0-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="16fa0-156">Ścieżka pliku może być względna względem bieżącego katalogu lub ścieżki bezwzględnej.</span><span class="sxs-lookup"><span data-stu-id="16fa0-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="16fa0-157">Jeśli ścieżka pliku znajduje się poza katalogiem projektu, `Link` element zostanie dodany w celu wyświetlenia pliku w folderze wirtualnym `Protos` w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="16fa0-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="16fa0-158">Użycie</span><span class="sxs-lookup"><span data-stu-id="16fa0-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="16fa0-159">Argumenty</span><span class="sxs-lookup"><span data-stu-id="16fa0-159">Arguments</span></span>

| <span data-ttu-id="16fa0-160">Argument</span><span class="sxs-lookup"><span data-stu-id="16fa0-160">Argument</span></span> | <span data-ttu-id="16fa0-161">Opis</span><span class="sxs-lookup"><span data-stu-id="16fa0-161">Description</span></span> |
|-|-|
| <span data-ttu-id="16fa0-162">url</span><span class="sxs-lookup"><span data-stu-id="16fa0-162">url</span></span> | <span data-ttu-id="16fa0-163">Adres URL pliku protobuf zdalnego.</span><span class="sxs-lookup"><span data-stu-id="16fa0-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="16fa0-164">Opcje</span><span class="sxs-lookup"><span data-stu-id="16fa0-164">Options</span></span>

| <span data-ttu-id="16fa0-165">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="16fa0-165">Short option</span></span> | <span data-ttu-id="16fa0-166">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="16fa0-166">Long option</span></span> | <span data-ttu-id="16fa0-167">Opis</span><span class="sxs-lookup"><span data-stu-id="16fa0-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="16fa0-168">-o</span><span class="sxs-lookup"><span data-stu-id="16fa0-168">-o</span></span> | <span data-ttu-id="16fa0-169">--output</span><span class="sxs-lookup"><span data-stu-id="16fa0-169">--output</span></span> | <span data-ttu-id="16fa0-170">Określa ścieżkę pobierania pliku protobuf zdalnego.</span><span class="sxs-lookup"><span data-stu-id="16fa0-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="16fa0-171">Ta opcja jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="16fa0-171">This is a required option.</span></span>
| <span data-ttu-id="16fa0-172">-p</span><span class="sxs-lookup"><span data-stu-id="16fa0-172">-p</span></span> | <span data-ttu-id="16fa0-173">--projekt</span><span class="sxs-lookup"><span data-stu-id="16fa0-173">--project</span></span> | <span data-ttu-id="16fa0-174">Ścieżka do pliku projektu, na którym mają być wykonywane działania.</span><span class="sxs-lookup"><span data-stu-id="16fa0-174">The path to the project file to operate on.</span></span> <span data-ttu-id="16fa0-175">Jeśli plik nie zostanie określony, polecenie przeszukuje bieżący katalog.</span><span class="sxs-lookup"><span data-stu-id="16fa0-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="16fa0-176">-s</span><span class="sxs-lookup"><span data-stu-id="16fa0-176">-s</span></span> | <span data-ttu-id="16fa0-177">--usługi</span><span class="sxs-lookup"><span data-stu-id="16fa0-177">--services</span></span> | <span data-ttu-id="16fa0-178">Typ usług gRPC, które mają zostać wygenerowane.</span><span class="sxs-lookup"><span data-stu-id="16fa0-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="16fa0-179">Jeśli `Default` jest określony, `Both` jest używany dla projektów sieci Web i `Client` jest używany dla projektów nieopartych na sieci Web.</span><span class="sxs-lookup"><span data-stu-id="16fa0-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="16fa0-180">Akceptowane wartości to,,,, `Both` `Client` `Default` `None` `Server` .</span><span class="sxs-lookup"><span data-stu-id="16fa0-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="16fa0-181">-i</span><span class="sxs-lookup"><span data-stu-id="16fa0-181">-i</span></span> | <span data-ttu-id="16fa0-182">--dodatkowe-import-katalogów</span><span class="sxs-lookup"><span data-stu-id="16fa0-182">--additional-import-dirs</span></span> | <span data-ttu-id="16fa0-183">Dodatkowe katalogi, które mają być używane podczas rozpoznawania importu dla plików protobuf.</span><span class="sxs-lookup"><span data-stu-id="16fa0-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="16fa0-184">Jest to rozdzielana średnikami lista ścieżek.</span><span class="sxs-lookup"><span data-stu-id="16fa0-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="16fa0-185">--dostęp</span><span class="sxs-lookup"><span data-stu-id="16fa0-185">--access</span></span> | <span data-ttu-id="16fa0-186">Modyfikator dostępu, który ma być używany dla wygenerowanych klas języka C#.</span><span class="sxs-lookup"><span data-stu-id="16fa0-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="16fa0-187">Wartość domyślna to `Public` .</span><span class="sxs-lookup"><span data-stu-id="16fa0-187">Default value is `Public`.</span></span> <span data-ttu-id="16fa0-188">Akceptowane wartości to `Internal` i `Public` .</span><span class="sxs-lookup"><span data-stu-id="16fa0-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="16fa0-189">Usuń</span><span class="sxs-lookup"><span data-stu-id="16fa0-189">Remove</span></span>

<span data-ttu-id="16fa0-190">`remove`Polecenie służy do usuwania odwołań protobuf z pliku *csproj* .</span><span class="sxs-lookup"><span data-stu-id="16fa0-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="16fa0-191">Polecenie akceptuje argumenty ścieżki i źródłowe adresy URL jako argumenty.</span><span class="sxs-lookup"><span data-stu-id="16fa0-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="16fa0-192">Narzędzie:</span><span class="sxs-lookup"><span data-stu-id="16fa0-192">The tool:</span></span>

* <span data-ttu-id="16fa0-193">Usuwa odwołanie protobuf.</span><span class="sxs-lookup"><span data-stu-id="16fa0-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="16fa0-194">Nie usuwa pliku *. proto* , nawet jeśli został pierwotnie pobrany ze zdalnego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="16fa0-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="16fa0-195">Użycie</span><span class="sxs-lookup"><span data-stu-id="16fa0-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="16fa0-196">Argumenty</span><span class="sxs-lookup"><span data-stu-id="16fa0-196">Arguments</span></span>

| <span data-ttu-id="16fa0-197">Argument</span><span class="sxs-lookup"><span data-stu-id="16fa0-197">Argument</span></span> | <span data-ttu-id="16fa0-198">Opis</span><span class="sxs-lookup"><span data-stu-id="16fa0-198">Description</span></span> |
|-|-|
| <span data-ttu-id="16fa0-199">odwołania</span><span class="sxs-lookup"><span data-stu-id="16fa0-199">references</span></span> | <span data-ttu-id="16fa0-200">Adresy URL lub ścieżki plików odwołań protobuf do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="16fa0-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="16fa0-201">Opcje</span><span class="sxs-lookup"><span data-stu-id="16fa0-201">Options</span></span>

| <span data-ttu-id="16fa0-202">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="16fa0-202">Short option</span></span> | <span data-ttu-id="16fa0-203">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="16fa0-203">Long option</span></span> | <span data-ttu-id="16fa0-204">Opis</span><span class="sxs-lookup"><span data-stu-id="16fa0-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="16fa0-205">-p</span><span class="sxs-lookup"><span data-stu-id="16fa0-205">-p</span></span> | <span data-ttu-id="16fa0-206">--projekt</span><span class="sxs-lookup"><span data-stu-id="16fa0-206">--project</span></span> | <span data-ttu-id="16fa0-207">Ścieżka do pliku projektu, na którym mają być wykonywane działania.</span><span class="sxs-lookup"><span data-stu-id="16fa0-207">The path to the project file to operate on.</span></span> <span data-ttu-id="16fa0-208">Jeśli plik nie zostanie określony, polecenie przeszukuje bieżący katalog.</span><span class="sxs-lookup"><span data-stu-id="16fa0-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="16fa0-209">Odśwież</span><span class="sxs-lookup"><span data-stu-id="16fa0-209">Refresh</span></span>

<span data-ttu-id="16fa0-210">`refresh`Polecenie służy do aktualizowania odwołania zdalnego z najnowszą zawartością ze źródłowego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="16fa0-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="16fa0-211">Aby określić odwołanie, które ma zostać zaktualizowane, można użyć zarówno ścieżki pliku pobierania, jak i źródłowego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="16fa0-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="16fa0-212">Uwaga:</span><span class="sxs-lookup"><span data-stu-id="16fa0-212">Note:</span></span>

* <span data-ttu-id="16fa0-213">Skróty zawartości plików są porównywane, aby określić, czy plik lokalny powinien zostać zaktualizowany.</span><span class="sxs-lookup"><span data-stu-id="16fa0-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="16fa0-214">Nie są porównywane żadne informacje o znaczniku czasu.</span><span class="sxs-lookup"><span data-stu-id="16fa0-214">No timestamp information is compared.</span></span>

<span data-ttu-id="16fa0-215">Narzędzie zawsze zastępuje plik lokalny plikiem zdalnym, jeśli jest wymagana aktualizacja.</span><span class="sxs-lookup"><span data-stu-id="16fa0-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="16fa0-216">Użycie</span><span class="sxs-lookup"><span data-stu-id="16fa0-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="16fa0-217">Argumenty</span><span class="sxs-lookup"><span data-stu-id="16fa0-217">Arguments</span></span>

| <span data-ttu-id="16fa0-218">Argument</span><span class="sxs-lookup"><span data-stu-id="16fa0-218">Argument</span></span> | <span data-ttu-id="16fa0-219">Opis</span><span class="sxs-lookup"><span data-stu-id="16fa0-219">Description</span></span> |
|-|-|
| <span data-ttu-id="16fa0-220">odwołania</span><span class="sxs-lookup"><span data-stu-id="16fa0-220">references</span></span> | <span data-ttu-id="16fa0-221">Adresy URL lub ścieżki plików do zdalnych odwołań protobuf, które powinny zostać zaktualizowane.</span><span class="sxs-lookup"><span data-stu-id="16fa0-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="16fa0-222">Pozostaw ten argument pusty, aby odświeżyć wszystkie odwołania zdalne.</span><span class="sxs-lookup"><span data-stu-id="16fa0-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="16fa0-223">Opcje</span><span class="sxs-lookup"><span data-stu-id="16fa0-223">Options</span></span>

| <span data-ttu-id="16fa0-224">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="16fa0-224">Short option</span></span> | <span data-ttu-id="16fa0-225">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="16fa0-225">Long option</span></span> | <span data-ttu-id="16fa0-226">Opis</span><span class="sxs-lookup"><span data-stu-id="16fa0-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="16fa0-227">-p</span><span class="sxs-lookup"><span data-stu-id="16fa0-227">-p</span></span> | <span data-ttu-id="16fa0-228">--projekt</span><span class="sxs-lookup"><span data-stu-id="16fa0-228">--project</span></span> | <span data-ttu-id="16fa0-229">Ścieżka do pliku projektu, na którym mają być wykonywane działania.</span><span class="sxs-lookup"><span data-stu-id="16fa0-229">The path to the project file to operate on.</span></span> <span data-ttu-id="16fa0-230">Jeśli plik nie zostanie określony, polecenie przeszukuje bieżący katalog.</span><span class="sxs-lookup"><span data-stu-id="16fa0-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="16fa0-231">--osuszyć-Run</span><span class="sxs-lookup"><span data-stu-id="16fa0-231">--dry-run</span></span> | <span data-ttu-id="16fa0-232">Wyświetla listę plików, które zostaną zaktualizowane bez pobierania nowej zawartości.</span><span class="sxs-lookup"><span data-stu-id="16fa0-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="16fa0-233">Lista</span><span class="sxs-lookup"><span data-stu-id="16fa0-233">List</span></span>

<span data-ttu-id="16fa0-234">`list`Polecenie jest używane do wyświetlania wszystkich odwołań protobuf w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="16fa0-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="16fa0-235">Jeśli wszystkie wartości w kolumnie są wartościami domyślnymi, kolumna może zostać pominięta.</span><span class="sxs-lookup"><span data-stu-id="16fa0-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="16fa0-236">Użycie</span><span class="sxs-lookup"><span data-stu-id="16fa0-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="16fa0-237">Opcje</span><span class="sxs-lookup"><span data-stu-id="16fa0-237">Options</span></span>

| <span data-ttu-id="16fa0-238">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="16fa0-238">Short option</span></span> | <span data-ttu-id="16fa0-239">Opcja Long</span><span class="sxs-lookup"><span data-stu-id="16fa0-239">Long option</span></span> | <span data-ttu-id="16fa0-240">Opis</span><span class="sxs-lookup"><span data-stu-id="16fa0-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="16fa0-241">-p</span><span class="sxs-lookup"><span data-stu-id="16fa0-241">-p</span></span> | <span data-ttu-id="16fa0-242">--projekt</span><span class="sxs-lookup"><span data-stu-id="16fa0-242">--project</span></span> | <span data-ttu-id="16fa0-243">Ścieżka do pliku projektu, na którym mają być wykonywane działania.</span><span class="sxs-lookup"><span data-stu-id="16fa0-243">The path to the project file to operate on.</span></span> <span data-ttu-id="16fa0-244">Jeśli plik nie zostanie określony, polecenie przeszukuje bieżący katalog.</span><span class="sxs-lookup"><span data-stu-id="16fa0-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="16fa0-245">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="16fa0-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
