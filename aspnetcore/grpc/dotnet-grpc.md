---
title: Zarządzanie odwołaniami narzędzia Protobuf za pomocą narzędzia dotnet-GRPC
author: juntaoluo
description: Dowiedz się więcej o dodawaniu, aktualizowaniu, usuwaniu i wystawianiu odwołań protobuf za pomocą globalnego narzędzia dotnet-grpc.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: 994597c854a95bb33de1686ab025cb3744cf6845
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667336"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="9e0d9-103">Zarządzanie odwołaniami narzędzia Protobuf za pomocą narzędzia dotnet-GRPC</span><span class="sxs-lookup"><span data-stu-id="9e0d9-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="9e0d9-104">Przez [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="9e0d9-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="9e0d9-105">`dotnet-grpc`jest globalnym narzędziem .NET Core do zarządzania odwołaniami [protobuf (*.proto*)](xref:grpc/basics#proto-file) w ramach projektu gRPC .NET.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf (*.proto*)](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="9e0d9-106">Narzędzie może służyć do dodawania, odświeżania, usuwania i listy odniesień protobuf.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="9e0d9-107">Instalacja</span><span class="sxs-lookup"><span data-stu-id="9e0d9-107">Installation</span></span>

<span data-ttu-id="9e0d9-108">Aby zainstalować `dotnet-grpc` narzędzie [.NET Core Global Tool](/dotnet/core/tools/global-tools), uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="9e0d9-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="9e0d9-109">Dodawanie odwołań</span><span class="sxs-lookup"><span data-stu-id="9e0d9-109">Add references</span></span>

<span data-ttu-id="9e0d9-110">`dotnet-grpc`może służyć do dodawania odwołań `<Protobuf />` protobuf jako elementów do pliku *csproj:*</span><span class="sxs-lookup"><span data-stu-id="9e0d9-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="9e0d9-111">Odwołania Protobuf są używane do generowania zasobów klienta i/lub serwera c#.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="9e0d9-112">Narzędzie `dotnet-grpc` może:</span><span class="sxs-lookup"><span data-stu-id="9e0d9-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="9e0d9-113">Utwórz odwołanie Protobuf z plików lokalnych na dysku.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="9e0d9-114">Utwórz odwołanie Protobuf ze zdalnego pliku określonego przez adres URL.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="9e0d9-115">Upewnij się, że do projektu są dodawane poprawne zależności pakietu gRPC.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="9e0d9-116">Na przykład `Grpc.AspNetCore` pakiet jest dodawany do aplikacji sieci web.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="9e0d9-117">`Grpc.AspNetCore`zawiera biblioteki serwerów i klientów gRPC oraz obsługę narzędzi.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="9e0d9-118">Alternatywnie `Grpc.Net.Client`, `Grpc.Tools` i `Google.Protobuf` pakiety, które zawierają tylko biblioteki klienta gRPC i obsługę narzędzi, są dodawane do aplikacji konsoli.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="9e0d9-119">Dodaj plik</span><span class="sxs-lookup"><span data-stu-id="9e0d9-119">Add file</span></span>

<span data-ttu-id="9e0d9-120">Polecenie `add-file` służy do dodawania plików lokalnych na dysku jako odwołań Protobuf.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="9e0d9-121">Ścieżki plików pod warunkiem:</span><span class="sxs-lookup"><span data-stu-id="9e0d9-121">The file paths provided:</span></span>

* <span data-ttu-id="9e0d9-122">Może być względem bieżącego katalogu lub ścieżek bezwzględnych.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="9e0d9-123">Może zawierać dzikie karty do [globbingu](https://wikipedia.org/wiki/Glob_(programming))plików opartego na wzorcach .</span><span class="sxs-lookup"><span data-stu-id="9e0d9-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="9e0d9-124">Jeśli wszystkie pliki znajdują się poza `Link` katalogiem projektu, element jest `Protos` dodawany do wyświetlania pliku w folderze w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="9e0d9-125">Sposób użycia</span><span class="sxs-lookup"><span data-stu-id="9e0d9-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="9e0d9-126">Argumenty</span><span class="sxs-lookup"><span data-stu-id="9e0d9-126">Arguments</span></span>

| <span data-ttu-id="9e0d9-127">Argument</span><span class="sxs-lookup"><span data-stu-id="9e0d9-127">Argument</span></span> | <span data-ttu-id="9e0d9-128">Opis</span><span class="sxs-lookup"><span data-stu-id="9e0d9-128">Description</span></span> |
|-|-|
| <span data-ttu-id="9e0d9-129">files</span><span class="sxs-lookup"><span data-stu-id="9e0d9-129">files</span></span> | <span data-ttu-id="9e0d9-130">Odwołania do pliku protobuf.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-130">The protobuf file references.</span></span> <span data-ttu-id="9e0d9-131">Mogą to być ścieżki do glob dla lokalnych plików protobuf.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="9e0d9-132">Opcje</span><span class="sxs-lookup"><span data-stu-id="9e0d9-132">Options</span></span>

| <span data-ttu-id="9e0d9-133">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="9e0d9-133">Short option</span></span> | <span data-ttu-id="9e0d9-134">Opcja długa</span><span class="sxs-lookup"><span data-stu-id="9e0d9-134">Long option</span></span> | <span data-ttu-id="9e0d9-135">Opis</span><span class="sxs-lookup"><span data-stu-id="9e0d9-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="9e0d9-136">-p</span><span class="sxs-lookup"><span data-stu-id="9e0d9-136">-p</span></span> | <span data-ttu-id="9e0d9-137">--projekt</span><span class="sxs-lookup"><span data-stu-id="9e0d9-137">--project</span></span> | <span data-ttu-id="9e0d9-138">Ścieżka do pliku projektu do działania.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-138">The path to the project file to operate on.</span></span> <span data-ttu-id="9e0d9-139">Jeśli plik nie jest określony, polecenie przeszukuje bieżący katalog dla jednego.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="9e0d9-140">-s</span><span class="sxs-lookup"><span data-stu-id="9e0d9-140">-s</span></span> | <span data-ttu-id="9e0d9-141">--usługi</span><span class="sxs-lookup"><span data-stu-id="9e0d9-141">--services</span></span> | <span data-ttu-id="9e0d9-142">Typ usług gRPC, które powinny być generowane.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="9e0d9-143">Jeśli `Default` jest `Both` określony, jest używany `Client` dla projektów sieci Web i jest używany dla projektów innych niż Web.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="9e0d9-144">Akceptowane wartości `Both`to `Client` `Default`, `None` `Server`, , , .</span><span class="sxs-lookup"><span data-stu-id="9e0d9-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="9e0d9-145">-i</span><span class="sxs-lookup"><span data-stu-id="9e0d9-145">-i</span></span> | <span data-ttu-id="9e0d9-146">--additional-import-dirs</span><span class="sxs-lookup"><span data-stu-id="9e0d9-146">--additional-import-dirs</span></span> | <span data-ttu-id="9e0d9-147">Dodatkowe katalogi, które mają być używane podczas rozwiązywania importu plików protobuf.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="9e0d9-148">Jest to oddzielona od średnikowa lista ścieżek.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="9e0d9-149">--dostęp</span><span class="sxs-lookup"><span data-stu-id="9e0d9-149">--access</span></span> | <span data-ttu-id="9e0d9-150">Modyfikator dostępu do użycia dla wygenerowanych klas Języka C#.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="9e0d9-151">Wartością domyślną jest `Public`.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-151">The default value is `Public`.</span></span> <span data-ttu-id="9e0d9-152">Akceptowane wartości `Internal` są `Public`i .</span><span class="sxs-lookup"><span data-stu-id="9e0d9-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="9e0d9-153">Dodaj adres URL</span><span class="sxs-lookup"><span data-stu-id="9e0d9-153">Add URL</span></span>

<span data-ttu-id="9e0d9-154">Polecenie `add-url` służy do dodawania pliku zdalnego określonego przez źródłowy adres URL jako odwołanie protobuf.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="9e0d9-155">Aby określić, gdzie pobrać plik zdalny, należy podać ścieżkę pliku.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="9e0d9-156">Ścieżka pliku może być względem bieżącego katalogu lub ścieżki bezwzględnej.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="9e0d9-157">Jeśli ścieżka pliku znajduje się poza `Link` katalogiem projektu, element jest dodawany do wyświetlania pliku w folderze `Protos` wirtualnym w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="9e0d9-158">Sposób użycia</span><span class="sxs-lookup"><span data-stu-id="9e0d9-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="9e0d9-159">Argumenty</span><span class="sxs-lookup"><span data-stu-id="9e0d9-159">Arguments</span></span>

| <span data-ttu-id="9e0d9-160">Argument</span><span class="sxs-lookup"><span data-stu-id="9e0d9-160">Argument</span></span> | <span data-ttu-id="9e0d9-161">Opis</span><span class="sxs-lookup"><span data-stu-id="9e0d9-161">Description</span></span> |
|-|-|
| <span data-ttu-id="9e0d9-162">url</span><span class="sxs-lookup"><span data-stu-id="9e0d9-162">url</span></span> | <span data-ttu-id="9e0d9-163">Adres URL do zdalnego pliku protobuf.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="9e0d9-164">Opcje</span><span class="sxs-lookup"><span data-stu-id="9e0d9-164">Options</span></span>

| <span data-ttu-id="9e0d9-165">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="9e0d9-165">Short option</span></span> | <span data-ttu-id="9e0d9-166">Opcja długa</span><span class="sxs-lookup"><span data-stu-id="9e0d9-166">Long option</span></span> | <span data-ttu-id="9e0d9-167">Opis</span><span class="sxs-lookup"><span data-stu-id="9e0d9-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="9e0d9-168">-o</span><span class="sxs-lookup"><span data-stu-id="9e0d9-168">-o</span></span> | <span data-ttu-id="9e0d9-169">--output</span><span class="sxs-lookup"><span data-stu-id="9e0d9-169">--output</span></span> | <span data-ttu-id="9e0d9-170">Określa ścieżkę pobierania zdalnego pliku protobuf.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="9e0d9-171">Ta opcja jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-171">This is a required option.</span></span>
| <span data-ttu-id="9e0d9-172">-p</span><span class="sxs-lookup"><span data-stu-id="9e0d9-172">-p</span></span> | <span data-ttu-id="9e0d9-173">--projekt</span><span class="sxs-lookup"><span data-stu-id="9e0d9-173">--project</span></span> | <span data-ttu-id="9e0d9-174">Ścieżka do pliku projektu do działania.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-174">The path to the project file to operate on.</span></span> <span data-ttu-id="9e0d9-175">Jeśli plik nie jest określony, polecenie przeszukuje bieżący katalog dla jednego.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="9e0d9-176">-s</span><span class="sxs-lookup"><span data-stu-id="9e0d9-176">-s</span></span> | <span data-ttu-id="9e0d9-177">--usługi</span><span class="sxs-lookup"><span data-stu-id="9e0d9-177">--services</span></span> | <span data-ttu-id="9e0d9-178">Typ usług gRPC, które powinny być generowane.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="9e0d9-179">Jeśli `Default` jest `Both` określony, jest używany `Client` dla projektów sieci Web i jest używany dla projektów innych niż Web.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="9e0d9-180">Akceptowane wartości `Both`to `Client` `Default`, `None` `Server`, , , .</span><span class="sxs-lookup"><span data-stu-id="9e0d9-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="9e0d9-181">-i</span><span class="sxs-lookup"><span data-stu-id="9e0d9-181">-i</span></span> | <span data-ttu-id="9e0d9-182">--additional-import-dirs</span><span class="sxs-lookup"><span data-stu-id="9e0d9-182">--additional-import-dirs</span></span> | <span data-ttu-id="9e0d9-183">Dodatkowe katalogi, które mają być używane podczas rozwiązywania importu plików protobuf.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="9e0d9-184">Jest to oddzielona od średnikowa lista ścieżek.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="9e0d9-185">--dostęp</span><span class="sxs-lookup"><span data-stu-id="9e0d9-185">--access</span></span> | <span data-ttu-id="9e0d9-186">Modyfikator dostępu do użycia dla wygenerowanych klas Języka C#.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="9e0d9-187">Wartością `Public`domyślną jest .</span><span class="sxs-lookup"><span data-stu-id="9e0d9-187">Default value is `Public`.</span></span> <span data-ttu-id="9e0d9-188">Akceptowane wartości `Internal` są `Public`i .</span><span class="sxs-lookup"><span data-stu-id="9e0d9-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="9e0d9-189">Remove</span><span class="sxs-lookup"><span data-stu-id="9e0d9-189">Remove</span></span>

<span data-ttu-id="9e0d9-190">Polecenie `remove` służy do usuwania odwołań Protobuf z pliku *csproj.*</span><span class="sxs-lookup"><span data-stu-id="9e0d9-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="9e0d9-191">Polecenie akceptuje argumenty ścieżki i źródłowe adresy URL jako argumenty.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="9e0d9-192">Narzędzie:</span><span class="sxs-lookup"><span data-stu-id="9e0d9-192">The tool:</span></span>

* <span data-ttu-id="9e0d9-193">Usuwa tylko odwołanie Protobuf.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="9e0d9-194">Nie usuwa pliku *.proto,* nawet jeśli został pierwotnie pobrany ze zdalnego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="9e0d9-195">Sposób użycia</span><span class="sxs-lookup"><span data-stu-id="9e0d9-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="9e0d9-196">Argumenty</span><span class="sxs-lookup"><span data-stu-id="9e0d9-196">Arguments</span></span>

| <span data-ttu-id="9e0d9-197">Argument</span><span class="sxs-lookup"><span data-stu-id="9e0d9-197">Argument</span></span> | <span data-ttu-id="9e0d9-198">Opis</span><span class="sxs-lookup"><span data-stu-id="9e0d9-198">Description</span></span> |
|-|-|
| <span data-ttu-id="9e0d9-199">odwołania</span><span class="sxs-lookup"><span data-stu-id="9e0d9-199">references</span></span> | <span data-ttu-id="9e0d9-200">Adresy URL lub ścieżki plików odwołań protobuf do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="9e0d9-201">Opcje</span><span class="sxs-lookup"><span data-stu-id="9e0d9-201">Options</span></span>

| <span data-ttu-id="9e0d9-202">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="9e0d9-202">Short option</span></span> | <span data-ttu-id="9e0d9-203">Opcja długa</span><span class="sxs-lookup"><span data-stu-id="9e0d9-203">Long option</span></span> | <span data-ttu-id="9e0d9-204">Opis</span><span class="sxs-lookup"><span data-stu-id="9e0d9-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="9e0d9-205">-p</span><span class="sxs-lookup"><span data-stu-id="9e0d9-205">-p</span></span> | <span data-ttu-id="9e0d9-206">--projekt</span><span class="sxs-lookup"><span data-stu-id="9e0d9-206">--project</span></span> | <span data-ttu-id="9e0d9-207">Ścieżka do pliku projektu do działania.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-207">The path to the project file to operate on.</span></span> <span data-ttu-id="9e0d9-208">Jeśli plik nie jest określony, polecenie przeszukuje bieżący katalog dla jednego.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="9e0d9-209">Odświeżanie</span><span class="sxs-lookup"><span data-stu-id="9e0d9-209">Refresh</span></span>

<span data-ttu-id="9e0d9-210">Polecenie `refresh` służy do aktualizowania odwołania zdalnego z najnowszą zawartością ze źródłowego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="9e0d9-211">Zarówno ścieżka pliku pobierania, jak i źródłowy adres URL mogą służyć do określania odwołania, które ma zostać zaktualizowane.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="9e0d9-212">Uwaga:</span><span class="sxs-lookup"><span data-stu-id="9e0d9-212">Note:</span></span>

* <span data-ttu-id="9e0d9-213">Skróty zawartości pliku są porównywane w celu ustalenia, czy plik lokalny powinien zostać zaktualizowany.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="9e0d9-214">Nie są porównywane żadne informacje o sygnaturach czasowych.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-214">No timestamp information is compared.</span></span>

<span data-ttu-id="9e0d9-215">Narzędzie zawsze zastępuje plik lokalny plikiem zdalnym, jeśli potrzebna jest aktualizacja.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="9e0d9-216">Sposób użycia</span><span class="sxs-lookup"><span data-stu-id="9e0d9-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="9e0d9-217">Argumenty</span><span class="sxs-lookup"><span data-stu-id="9e0d9-217">Arguments</span></span>

| <span data-ttu-id="9e0d9-218">Argument</span><span class="sxs-lookup"><span data-stu-id="9e0d9-218">Argument</span></span> | <span data-ttu-id="9e0d9-219">Opis</span><span class="sxs-lookup"><span data-stu-id="9e0d9-219">Description</span></span> |
|-|-|
| <span data-ttu-id="9e0d9-220">odwołania</span><span class="sxs-lookup"><span data-stu-id="9e0d9-220">references</span></span> | <span data-ttu-id="9e0d9-221">Adresy URL lub ścieżki plików do zdalnych odwołań protobuf, które powinny zostać zaktualizowane.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="9e0d9-222">Pozostaw ten argument pusty, aby odświeżyć wszystkie odwołania zdalne.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="9e0d9-223">Opcje</span><span class="sxs-lookup"><span data-stu-id="9e0d9-223">Options</span></span>

| <span data-ttu-id="9e0d9-224">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="9e0d9-224">Short option</span></span> | <span data-ttu-id="9e0d9-225">Opcja długa</span><span class="sxs-lookup"><span data-stu-id="9e0d9-225">Long option</span></span> | <span data-ttu-id="9e0d9-226">Opis</span><span class="sxs-lookup"><span data-stu-id="9e0d9-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="9e0d9-227">-p</span><span class="sxs-lookup"><span data-stu-id="9e0d9-227">-p</span></span> | <span data-ttu-id="9e0d9-228">--projekt</span><span class="sxs-lookup"><span data-stu-id="9e0d9-228">--project</span></span> | <span data-ttu-id="9e0d9-229">Ścieżka do pliku projektu do działania.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-229">The path to the project file to operate on.</span></span> <span data-ttu-id="9e0d9-230">Jeśli plik nie jest określony, polecenie przeszukuje bieżący katalog dla jednego.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="9e0d9-231">--dry-run</span><span class="sxs-lookup"><span data-stu-id="9e0d9-231">--dry-run</span></span> | <span data-ttu-id="9e0d9-232">Wyprowadza listę plików, które zostaną zaktualizowane bez pobierania nowej zawartości.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="9e0d9-233">List</span><span class="sxs-lookup"><span data-stu-id="9e0d9-233">List</span></span>

<span data-ttu-id="9e0d9-234">Polecenie `list` służy do wyświetlania wszystkich odwołań Protobuf w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="9e0d9-235">Jeśli wszystkie wartości kolumny są wartościami domyślnymi, kolumna może zostać pominięta.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="9e0d9-236">Sposób użycia</span><span class="sxs-lookup"><span data-stu-id="9e0d9-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="9e0d9-237">Opcje</span><span class="sxs-lookup"><span data-stu-id="9e0d9-237">Options</span></span>

| <span data-ttu-id="9e0d9-238">Opcja krótka</span><span class="sxs-lookup"><span data-stu-id="9e0d9-238">Short option</span></span> | <span data-ttu-id="9e0d9-239">Opcja długa</span><span class="sxs-lookup"><span data-stu-id="9e0d9-239">Long option</span></span> | <span data-ttu-id="9e0d9-240">Opis</span><span class="sxs-lookup"><span data-stu-id="9e0d9-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="9e0d9-241">-p</span><span class="sxs-lookup"><span data-stu-id="9e0d9-241">-p</span></span> | <span data-ttu-id="9e0d9-242">--projekt</span><span class="sxs-lookup"><span data-stu-id="9e0d9-242">--project</span></span> | <span data-ttu-id="9e0d9-243">Ścieżka do pliku projektu do działania.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-243">The path to the project file to operate on.</span></span> <span data-ttu-id="9e0d9-244">Jeśli plik nie jest określony, polecenie przeszukuje bieżący katalog dla jednego.</span><span class="sxs-lookup"><span data-stu-id="9e0d9-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9e0d9-245">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="9e0d9-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
