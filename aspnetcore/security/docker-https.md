---
title: Hostowanie ASP.NET Core obrazów przy użyciu platformy Docker za pośrednictwem protokołu HTTPS
author: rick-anderson
description: Dowiedz się, jak hostować ASP.NET Core obrazy za pomocą platformy Docker za pośrednictwem protokołu HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
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
uid: security/docker-https
ms.openlocfilehash: 3af2aff477604eb19ac211753f848d08d0c67c72
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588643"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="3fe1c-103">Hostowanie ASP.NET Core obrazów przy użyciu platformy Docker za pośrednictwem protokołu HTTPS</span><span class="sxs-lookup"><span data-stu-id="3fe1c-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="3fe1c-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3fe1c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3fe1c-105">ASP.NET Core [domyślnie używa protokołu HTTPS](./enforcing-ssl.md).</span><span class="sxs-lookup"><span data-stu-id="3fe1c-105">ASP.NET Core uses [HTTPS by default](./enforcing-ssl.md).</span></span> <span data-ttu-id="3fe1c-106">[Protokół https](https://en.wikipedia.org/wiki/HTTPS) opiera się na [certyfikatach](https://en.wikipedia.org/wiki/Public_key_certificate) do zaufania, tożsamości i szyfrowania.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="3fe1c-107">W tym dokumencie wyjaśniono, jak uruchamiać wstępnie skompilowane obrazy kontenerów przy użyciu protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="3fe1c-108">Zobacz [Tworzenie aplikacji ASP.NET Core przy użyciu platformy Docker za pośrednictwem protokołu HTTPS](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md) na potrzeby scenariuszy programistycznych.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="3fe1c-109">Ten przykład wymaga [platformy docker 17,06](https://docs.docker.com/release-notes/docker-ce) lub nowszej wersji [klienta platformy Docker](https://www.docker.com/products/docker).</span><span class="sxs-lookup"><span data-stu-id="3fe1c-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3fe1c-110">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="3fe1c-110">Prerequisites</span></span>

<span data-ttu-id="3fe1c-111">W przypadku niektórych instrukcji przedstawionych w tym dokumencie wymagany jest [zestaw .NET Core 2,2 SDK](https://dotnet.microsoft.com/download) lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-111">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="3fe1c-112">Certyfikaty</span><span class="sxs-lookup"><span data-stu-id="3fe1c-112">Certificates</span></span>

<span data-ttu-id="3fe1c-113">Certyfikat z [urzędu certyfikacji](https://wikipedia.org/wiki/Certificate_authority) jest wymagany do hostingu w [środowisku produkcyjnym](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) dla domeny.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-113">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="3fe1c-114">[Let's Encrypt](https://letsencrypt.org/) to urząd certyfikacji, który oferuje bezpłatne certyfikaty.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="3fe1c-115">W tym dokumencie są stosowane [Certyfikaty deweloperskie](https://en.wikipedia.org/wiki/Self-signed_certificate) z podpisem własnym do obsługi wstępnie utworzonych obrazów `localhost` .</span><span class="sxs-lookup"><span data-stu-id="3fe1c-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="3fe1c-116">Instrukcje są podobne do korzystania z certyfikatów produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="3fe1c-117">Aby tworzyć certyfikaty z podpisem własnym na potrzeby programowania i testowania, Użyj usługi [dotnet dev-certs](/dotnet/core/additional-tools/self-signed-certificates-guide) .</span><span class="sxs-lookup"><span data-stu-id="3fe1c-117">Use [dotnet dev-certs](/dotnet/core/additional-tools/self-signed-certificates-guide) to create self-signed certificates for development and testing.</span></span>

<span data-ttu-id="3fe1c-118">Dla certyfikatów produkcyjnych:</span><span class="sxs-lookup"><span data-stu-id="3fe1c-118">For production certs:</span></span>

* <span data-ttu-id="3fe1c-119">`dotnet dev-certs`Narzędzie nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-119">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="3fe1c-120">Certyfikaty nie muszą być przechowywane w lokalizacji używanej w instrukcjach.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-120">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="3fe1c-121">Każda lokalizacja powinna funkcjonować, chociaż przechowywanie certyfikatów w katalogu witryn nie jest zalecane.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-121">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="3fe1c-122">Instrukcje zawarte w poniższej sekcji dotyczą instalowania certyfikatów do kontenerów przy użyciu `-v` opcji wiersza polecenia platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-122">The instructions contained in the following section volume mount certificates into containers using Docker's `-v` command-line option.</span></span> <span data-ttu-id="3fe1c-123">Można dodać certyfikaty do obrazów kontenerów za pomocą `COPY` polecenia w *pliku dockerfile*, ale nie jest to zalecane.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-123">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="3fe1c-124">Nie zaleca się kopiowania certyfikatów do obrazu z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="3fe1c-124">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="3fe1c-125">Trudno jest użyć tego samego obrazu do testowania przy użyciu certyfikatów deweloperskich.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-125">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="3fe1c-126">Trudno jest użyć tego samego obrazu do hostowania z certyfikatami produkcyjnymi.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-126">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="3fe1c-127">Istnieje duże ryzyko ujawnienia certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-127">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="3fe1c-128">Uruchamianie wstępnie skompilowanych obrazów kontenerów za pomocą protokołu HTTPS</span><span class="sxs-lookup"><span data-stu-id="3fe1c-128">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="3fe1c-129">Wykonaj następujące instrukcje dotyczące konfiguracji systemu operacyjnego.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-129">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="3fe1c-130">System Windows korzystający z kontenerów systemu Linux</span><span class="sxs-lookup"><span data-stu-id="3fe1c-130">Windows using Linux containers</span></span>

<span data-ttu-id="3fe1c-131">Generuj certyfikat i skonfiguruj komputer lokalny:</span><span class="sxs-lookup"><span data-stu-id="3fe1c-131">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="3fe1c-132">W poprzednich poleceniach Zastąp ciąg `{ password here }` hasłem.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-132">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="3fe1c-133">Uruchom obraz kontenera z ASP.NET Core skonfigurowanym dla protokołu HTTPS w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="3fe1c-133">Run the container image with ASP.NET Core configured for HTTPS in a command shell:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="3fe1c-134">W przypadku korzystania z [programu PowerShell](/powershell/scripting/overview)Zamień `%USERPROFILE%` na `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="3fe1c-134">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="3fe1c-135">Hasło musi być zgodne z hasłem użytym dla certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-135">The password must match the password used for the certificate.</span></span>


<span data-ttu-id="3fe1c-136">Uwaga: certyfikat w tym przypadku musi być `.pfx` plikiem.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-136">Note: The certificate in this case must be a `.pfx` file.</span></span>  <span data-ttu-id="3fe1c-137">Używanie `.crt` `.key` pliku lub z hasłem lub bez niego jest nieobsługiwane w przypadku kontenera przykładowego.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-137">Utilizing a `.crt` or `.key` file with or without the password isn't supported with the sample container.</span></span>  <span data-ttu-id="3fe1c-138">Na przykład podczas określania `.crt` pliku kontener może zwracać komunikaty o błędach, takie jak "protokół SSL w trybie serwera musi używać certyfikatu ze skojarzonym kluczem prywatnym".</span><span class="sxs-lookup"><span data-stu-id="3fe1c-138">For example, when specifying a `.crt` file, the container may return error messages such as 'The server mode SSL must use a certificate with the associated private key.'.</span></span> <span data-ttu-id="3fe1c-139">W przypadku korzystania z [WSL](/windows/wsl/about)Sprawdź poprawność ścieżki instalacji, aby upewnić się, że certyfikat został poprawnie załadowany.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-139">When using [WSL](/windows/wsl/about), validate the mount path to ensure that the certificate loads correctly.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="3fe1c-140">macOS lub Linux</span><span class="sxs-lookup"><span data-stu-id="3fe1c-140">macOS or Linux</span></span>

<span data-ttu-id="3fe1c-141">Generuj certyfikat i skonfiguruj komputer lokalny:</span><span class="sxs-lookup"><span data-stu-id="3fe1c-141">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="3fe1c-142">`dotnet dev-certs https --trust` jest obsługiwana tylko w systemach macOS i Windows.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-142">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="3fe1c-143">Certyfikaty w systemie Linux muszą być zaufane w sposób, który jest obsługiwany przez dystrybucję.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-143">You need to trust certs on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="3fe1c-144">Prawdopodobnie należy zaufać certyfikatowi w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-144">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="3fe1c-145">W poprzednich poleceniach Zastąp ciąg `{ password here }` hasłem.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="3fe1c-146">Uruchom obraz kontenera z ASP.NET Core skonfigurowanym dla protokołu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="3fe1c-146">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="3fe1c-147">Hasło musi być zgodne z hasłem użytym dla certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-147">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="3fe1c-148">Windows przy użyciu kontenerów systemu Windows</span><span class="sxs-lookup"><span data-stu-id="3fe1c-148">Windows using Windows containers</span></span>

<span data-ttu-id="3fe1c-149">Generuj certyfikat i skonfiguruj komputer lokalny:</span><span class="sxs-lookup"><span data-stu-id="3fe1c-149">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="3fe1c-150">W poprzednich poleceniach Zastąp ciąg `{ password here }` hasłem.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-150">In the preceding commands, replace `{ password here }` with a password.</span></span> <span data-ttu-id="3fe1c-151">W przypadku korzystania z [programu PowerShell](/powershell/scripting/overview)Zamień `%USERPROFILE%` na `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="3fe1c-151">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="3fe1c-152">Uruchom obraz kontenera z ASP.NET Core skonfigurowanym dla protokołu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="3fe1c-152">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="3fe1c-153">Hasło musi być zgodne z hasłem użytym dla certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="3fe1c-153">The password must match the password used for the certificate.</span></span> <span data-ttu-id="3fe1c-154">W przypadku korzystania z [programu PowerShell](/powershell/scripting/overview)Zamień `%USERPROFILE%` na `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="3fe1c-154">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>
