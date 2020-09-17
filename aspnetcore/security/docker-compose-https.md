---
title: Hostowanie ASP.NET Core obrazu w kontenerze za pomocą funkcji tworzenia aplikacji platformy Docker przy użyciu protokołu HTTPS
author: ravipal
description: Dowiedz się, jak hostować ASP.NET Core obrazy z Docker Compose za pośrednictwem protokołu HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
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
uid: security/docker-compose-https
ms.openlocfilehash: 71ead7dcce32dab090a9b0b3573aa855d00fa7f1
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722764"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a><span data-ttu-id="367c7-103">Hostowanie ASP.NET Core obrazów z Docker Compose za pośrednictwem protokołu HTTPS</span><span class="sxs-lookup"><span data-stu-id="367c7-103">Hosting ASP.NET Core images with Docker Compose over HTTPS</span></span>


<span data-ttu-id="367c7-104">ASP.NET Core [domyślnie używa protokołu HTTPS](./enforcing-ssl.md).</span><span class="sxs-lookup"><span data-stu-id="367c7-104">ASP.NET Core uses [HTTPS by default](./enforcing-ssl.md).</span></span> <span data-ttu-id="367c7-105">[Protokół https](https://en.wikipedia.org/wiki/HTTPS) opiera się na [certyfikatach](https://en.wikipedia.org/wiki/Public_key_certificate) do zaufania, tożsamości i szyfrowania.</span><span class="sxs-lookup"><span data-stu-id="367c7-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="367c7-106">W tym dokumencie wyjaśniono, jak uruchamiać wstępnie skompilowane obrazy kontenerów przy użyciu protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="367c7-106">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="367c7-107">Zobacz [Tworzenie aplikacji ASP.NET Core przy użyciu platformy Docker za pośrednictwem protokołu HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) na potrzeby scenariuszy programistycznych.</span><span class="sxs-lookup"><span data-stu-id="367c7-107">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="367c7-108">Ten przykład wymaga [platformy docker 17,06](https://docs.docker.com/release-notes/docker-ce) lub nowszej wersji [klienta platformy Docker](https://www.docker.com/products/docker).</span><span class="sxs-lookup"><span data-stu-id="367c7-108">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="367c7-109">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="367c7-109">Prerequisites</span></span>

<span data-ttu-id="367c7-110">W przypadku niektórych instrukcji przedstawionych w tym dokumencie wymagany jest [zestaw .NET Core 2,2 SDK](https://dotnet.microsoft.com/download) lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="367c7-110">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="367c7-111">Certyfikaty</span><span class="sxs-lookup"><span data-stu-id="367c7-111">Certificates</span></span>

<span data-ttu-id="367c7-112">Certyfikat z [urzędu certyfikacji](https://wikipedia.org/wiki/Certificate_authority) jest wymagany do hostingu w [środowisku produkcyjnym](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) dla domeny.</span><span class="sxs-lookup"><span data-stu-id="367c7-112">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="367c7-113">[Let's Encrypt](https://letsencrypt.org/) to urząd certyfikacji, który oferuje bezpłatne certyfikaty.</span><span class="sxs-lookup"><span data-stu-id="367c7-113">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="367c7-114">W tym dokumencie są stosowane [Certyfikaty deweloperskie](https://wikipedia.org/wiki/Self-signed_certificate) z podpisem własnym do obsługi wstępnie utworzonych obrazów `localhost` .</span><span class="sxs-lookup"><span data-stu-id="367c7-114">This document uses [self-signed development certificates](https://wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="367c7-115">Instrukcje są podobne do korzystania z certyfikatów produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="367c7-115">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="367c7-116">Dla certyfikatów produkcyjnych:</span><span class="sxs-lookup"><span data-stu-id="367c7-116">For production certificates:</span></span>

* <span data-ttu-id="367c7-117">`dotnet dev-certs`Narzędzie nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="367c7-117">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="367c7-118">Certyfikaty nie muszą być przechowywane w lokalizacji używanej w instrukcjach.</span><span class="sxs-lookup"><span data-stu-id="367c7-118">Certificates don't need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="367c7-119">Przechowuj certyfikaty w dowolnej lokalizacji poza katalogiem lokacji.</span><span class="sxs-lookup"><span data-stu-id="367c7-119">Store the certificates in any location outside the site directory.</span></span>

<span data-ttu-id="367c7-120">Instrukcje zawarte w poniższej sekcji dotyczą instalowania certyfikatów do kontenerów przy użyciu `volumes` właściwości w *Docker-Compose. yml.*</span><span class="sxs-lookup"><span data-stu-id="367c7-120">The instructions contained in the following section volume mount certificates into containers using the `volumes` property in *docker-compose.yml.*</span></span> <span data-ttu-id="367c7-121">Można dodać certyfikaty do obrazów kontenerów za pomocą `COPY` polecenia w *pliku dockerfile*, ale nie jest to zalecane.</span><span class="sxs-lookup"><span data-stu-id="367c7-121">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="367c7-122">Nie zaleca się kopiowania certyfikatów do obrazu z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="367c7-122">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="367c7-123">Utrudnia to korzystanie z tego samego obrazu do testowania przy użyciu certyfikatów deweloperskich.</span><span class="sxs-lookup"><span data-stu-id="367c7-123">It makes it difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="367c7-124">Utrudnia korzystanie z tego samego obrazu na potrzeby hostowania z certyfikatami produkcyjnymi.</span><span class="sxs-lookup"><span data-stu-id="367c7-124">It makes it difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="367c7-125">Istnieje duże ryzyko ujawnienia certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="367c7-125">There is significant risk of certificate disclosure.</span></span>

## <a name="starting-a-container-with-https-support-using-docker-compose"></a><span data-ttu-id="367c7-126">Uruchamianie kontenera z obsługą protokołu HTTPS przy użyciu funkcji tworzenia platformy Docker</span><span class="sxs-lookup"><span data-stu-id="367c7-126">Starting a container with https support using docker compose</span></span>

<span data-ttu-id="367c7-127">Wykonaj następujące instrukcje dotyczące konfiguracji systemu operacyjnego.</span><span class="sxs-lookup"><span data-stu-id="367c7-127">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="367c7-128">System Windows korzystający z kontenerów systemu Linux</span><span class="sxs-lookup"><span data-stu-id="367c7-128">Windows using Linux containers</span></span>

<span data-ttu-id="367c7-129">Generuj certyfikat i skonfiguruj komputer lokalny:</span><span class="sxs-lookup"><span data-stu-id="367c7-129">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="367c7-130">W poprzednich poleceniach Zastąp ciąg `{ password here }` hasłem.</span><span class="sxs-lookup"><span data-stu-id="367c7-130">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="367c7-131">Utwórz plik _Docker-Compose. Debug. yml_ o następującej zawartości:</span><span class="sxs-lookup"><span data-stu-id="367c7-131">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="367c7-132">Hasło określone w pliku redagowania platformy Docker musi być zgodne z hasłem użytym dla certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="367c7-132">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="367c7-133">Uruchom kontener z ASP.NET Core skonfigurowanym dla protokołu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="367c7-133">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a><span data-ttu-id="367c7-134">macOS lub Linux</span><span class="sxs-lookup"><span data-stu-id="367c7-134">macOS or Linux</span></span>

<span data-ttu-id="367c7-135">Generuj certyfikat i skonfiguruj komputer lokalny:</span><span class="sxs-lookup"><span data-stu-id="367c7-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="367c7-136">`dotnet dev-certs https --trust` jest obsługiwana tylko w systemach macOS i Windows.</span><span class="sxs-lookup"><span data-stu-id="367c7-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="367c7-137">Należy zaufać certyfikatom w systemie Linux w sposób, który jest obsługiwany przez dystrybucję.</span><span class="sxs-lookup"><span data-stu-id="367c7-137">You need to trust certificates on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="367c7-138">Prawdopodobnie należy zaufać certyfikatowi w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="367c7-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="367c7-139">W poprzednich poleceniach Zastąp ciąg `{ password here }` hasłem.</span><span class="sxs-lookup"><span data-stu-id="367c7-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="367c7-140">Utwórz plik _Docker-Compose. Debug. yml_ o następującej zawartości:</span><span class="sxs-lookup"><span data-stu-id="367c7-140">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="367c7-141">Hasło określone w pliku redagowania platformy Docker musi być zgodne z hasłem użytym dla certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="367c7-141">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="367c7-142">Uruchom kontener z ASP.NET Core skonfigurowanym dla protokołu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="367c7-142">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a><span data-ttu-id="367c7-143">Windows przy użyciu kontenerów systemu Windows</span><span class="sxs-lookup"><span data-stu-id="367c7-143">Windows using Windows containers</span></span>

<span data-ttu-id="367c7-144">Generuj certyfikat i skonfiguruj komputer lokalny:</span><span class="sxs-lookup"><span data-stu-id="367c7-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="367c7-145">W poprzednich poleceniach Zastąp ciąg `{ password here }` hasłem.</span><span class="sxs-lookup"><span data-stu-id="367c7-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="367c7-146">Utwórz plik _Docker-Compose. Debug. yml_ o następującej zawartości:</span><span class="sxs-lookup"><span data-stu-id="367c7-146">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=C:\https\aspnetapp.pfx
    volumes:
      - ${USERPROFILE}\.aspnet\https:C:\https:ro
```
<span data-ttu-id="367c7-147">Hasło określone w pliku redagowania platformy Docker musi być zgodne z hasłem użytym dla certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="367c7-147">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="367c7-148">Uruchom kontener z ASP.NET Core skonfigurowanym dla protokołu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="367c7-148">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```