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
ms.openlocfilehash: 5e29882414d49c0971bc11c688067878714d3b2d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634192"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="93efd-103">Hostowanie ASP.NET Core obrazów przy użyciu platformy Docker za pośrednictwem protokołu HTTPS</span><span class="sxs-lookup"><span data-stu-id="93efd-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="93efd-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="93efd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="93efd-105">ASP.NET Core [domyślnie używa protokołu HTTPS](/aspnet/core/security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="93efd-105">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="93efd-106">[Protokół https](https://en.wikipedia.org/wiki/HTTPS) opiera się na [certyfikatach](https://en.wikipedia.org/wiki/Public_key_certificate) do zaufania, tożsamości i szyfrowania.</span><span class="sxs-lookup"><span data-stu-id="93efd-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="93efd-107">W tym dokumencie wyjaśniono, jak uruchamiać wstępnie skompilowane obrazy kontenerów przy użyciu protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="93efd-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="93efd-108">Zobacz [Tworzenie aplikacji ASP.NET Core przy użyciu platformy Docker za pośrednictwem protokołu HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) na potrzeby scenariuszy programistycznych.</span><span class="sxs-lookup"><span data-stu-id="93efd-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="93efd-109">Ten przykład wymaga [platformy docker 17,06](https://docs.docker.com/release-notes/docker-ce) lub nowszej wersji [klienta platformy Docker](https://www.docker.com/products/docker).</span><span class="sxs-lookup"><span data-stu-id="93efd-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="93efd-110">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="93efd-110">Prerequisites</span></span>

<span data-ttu-id="93efd-111">W przypadku niektórych instrukcji przedstawionych w tym dokumencie wymagany jest [zestaw .NET Core 2,2 SDK](https://dotnet.microsoft.com/download) lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="93efd-111">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="93efd-112">Certyfikaty</span><span class="sxs-lookup"><span data-stu-id="93efd-112">Certificates</span></span>

<span data-ttu-id="93efd-113">Certyfikat z [urzędu certyfikacji](https://wikipedia.org/wiki/Certificate_authority) jest wymagany do hostingu w [środowisku produkcyjnym](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) dla domeny.</span><span class="sxs-lookup"><span data-stu-id="93efd-113">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="93efd-114">[Let's Encrypt](https://letsencrypt.org/) to urząd certyfikacji, który oferuje bezpłatne certyfikaty.</span><span class="sxs-lookup"><span data-stu-id="93efd-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="93efd-115">W tym dokumencie są stosowane [Certyfikaty deweloperskie](https://en.wikipedia.org/wiki/Self-signed_certificate) z podpisem własnym do obsługi wstępnie utworzonych obrazów `localhost` .</span><span class="sxs-lookup"><span data-stu-id="93efd-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="93efd-116">Instrukcje są podobne do korzystania z certyfikatów produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="93efd-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="93efd-117">Dla certyfikatów produkcyjnych:</span><span class="sxs-lookup"><span data-stu-id="93efd-117">For production certs:</span></span>

* <span data-ttu-id="93efd-118">`dotnet dev-certs`Narzędzie nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="93efd-118">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="93efd-119">Certyfikaty nie muszą być przechowywane w lokalizacji używanej w instrukcjach.</span><span class="sxs-lookup"><span data-stu-id="93efd-119">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="93efd-120">Każda lokalizacja powinna funkcjonować, chociaż przechowywanie certyfikatów w katalogu witryn nie jest zalecane.</span><span class="sxs-lookup"><span data-stu-id="93efd-120">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="93efd-121">Instrukcje zawarte w poniższej sekcji dotyczą instalowania certyfikatów do kontenerów przy użyciu `-v` opcji wiersza polecenia platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="93efd-121">The instructions contained in the following section volume mount certificates into containers using Docker's `-v` command-line option.</span></span> <span data-ttu-id="93efd-122">Można dodać certyfikaty do obrazów kontenerów za pomocą `COPY` polecenia w *pliku dockerfile*, ale nie jest to zalecane.</span><span class="sxs-lookup"><span data-stu-id="93efd-122">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="93efd-123">Nie zaleca się kopiowania certyfikatów do obrazu z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="93efd-123">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="93efd-124">Trudno jest użyć tego samego obrazu do testowania przy użyciu certyfikatów deweloperskich.</span><span class="sxs-lookup"><span data-stu-id="93efd-124">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="93efd-125">Trudno jest użyć tego samego obrazu do hostowania z certyfikatami produkcyjnymi.</span><span class="sxs-lookup"><span data-stu-id="93efd-125">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="93efd-126">Istnieje duże ryzyko ujawnienia certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="93efd-126">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="93efd-127">Uruchamianie wstępnie skompilowanych obrazów kontenerów za pomocą protokołu HTTPS</span><span class="sxs-lookup"><span data-stu-id="93efd-127">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="93efd-128">Wykonaj następujące instrukcje dotyczące konfiguracji systemu operacyjnego.</span><span class="sxs-lookup"><span data-stu-id="93efd-128">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="93efd-129">System Windows korzystający z kontenerów systemu Linux</span><span class="sxs-lookup"><span data-stu-id="93efd-129">Windows using Linux containers</span></span>

<span data-ttu-id="93efd-130">Generuj certyfikat i skonfiguruj komputer lokalny:</span><span class="sxs-lookup"><span data-stu-id="93efd-130">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="93efd-131">W poprzednich poleceniach Zastąp ciąg `{ password here }` hasłem.</span><span class="sxs-lookup"><span data-stu-id="93efd-131">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="93efd-132">Uruchom obraz kontenera z ASP.NET Core skonfigurowanym dla protokołu HTTPS w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="93efd-132">Run the container image with ASP.NET Core configured for HTTPS in a command shell:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="93efd-133">W przypadku korzystania z [programu PowerShell](/powershell/scripting/overview)Zamień `%USERPROFILE%` na `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="93efd-133">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="93efd-134">Hasło musi być zgodne z hasłem użytym dla certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="93efd-134">The password must match the password used for the certificate.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="93efd-135">macOS lub Linux</span><span class="sxs-lookup"><span data-stu-id="93efd-135">macOS or Linux</span></span>

<span data-ttu-id="93efd-136">Generuj certyfikat i skonfiguruj komputer lokalny:</span><span class="sxs-lookup"><span data-stu-id="93efd-136">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="93efd-137">`dotnet dev-certs https --trust` jest obsługiwana tylko w systemach macOS i Windows.</span><span class="sxs-lookup"><span data-stu-id="93efd-137">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="93efd-138">Certyfikaty w systemie Linux muszą być zaufane w sposób, który jest obsługiwany przez dystrybucję.</span><span class="sxs-lookup"><span data-stu-id="93efd-138">You need to trust certs on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="93efd-139">Prawdopodobnie należy zaufać certyfikatowi w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="93efd-139">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="93efd-140">W poprzednich poleceniach Zastąp ciąg `{ password here }` hasłem.</span><span class="sxs-lookup"><span data-stu-id="93efd-140">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="93efd-141">Uruchom obraz kontenera z ASP.NET Core skonfigurowanym dla protokołu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="93efd-141">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="93efd-142">Hasło musi być zgodne z hasłem użytym dla certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="93efd-142">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="93efd-143">Windows przy użyciu kontenerów systemu Windows</span><span class="sxs-lookup"><span data-stu-id="93efd-143">Windows using Windows containers</span></span>

<span data-ttu-id="93efd-144">Generuj certyfikat i skonfiguruj komputer lokalny:</span><span class="sxs-lookup"><span data-stu-id="93efd-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="93efd-145">W poprzednich poleceniach Zastąp ciąg `{ password here }` hasłem.</span><span class="sxs-lookup"><span data-stu-id="93efd-145">In the preceding commands, replace `{ password here }` with a password.</span></span> <span data-ttu-id="93efd-146">W przypadku korzystania z [programu PowerShell](/powershell/scripting/overview)Zamień `%USERPROFILE%` na `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="93efd-146">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="93efd-147">Uruchom obraz kontenera z ASP.NET Core skonfigurowanym dla protokołu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="93efd-147">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="93efd-148">Hasło musi być zgodne z hasłem użytym dla certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="93efd-148">The password must match the password used for the certificate.</span></span> <span data-ttu-id="93efd-149">W przypadku korzystania z [programu PowerShell](/powershell/scripting/overview)Zamień `%USERPROFILE%` na `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="93efd-149">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>
