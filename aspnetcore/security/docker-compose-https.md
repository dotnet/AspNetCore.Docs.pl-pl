---
title: Hostowanie obrazu ASP.NET Core w kontenerze przy użyciu dokowanekomoncja z HTTPS
author: ravipal
description: Dowiedz się, jak hostować ASP.NET podstawowych obrazów za pomocą dokowanekomonkusowania za pośrednictwem protokołu HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
no-loc:
- Let's Encrypt
uid: security/docker-compose-https
ms.openlocfilehash: 616ccf906e98534ffda08c0c2b6d0a171f063cc1
ms.sourcegitcommit: d03905aadf5ceac39fff17706481af7f6c130411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80381825"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a><span data-ttu-id="3fd6a-103">Hostowanie obrazów ASP.NET podstawowych za pomocą dokowanewyskusić za pośrednictwem protokołu HTTPS</span><span class="sxs-lookup"><span data-stu-id="3fd6a-103">Hosting ASP.NET Core images with Docker Compose over HTTPS</span></span>


<span data-ttu-id="3fd6a-104">ASP.NET Core domyślnie używa [protokołu HTTPS](/aspnet/core/security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="3fd6a-104">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="3fd6a-105">[Protokół HTTPS](https://en.wikipedia.org/wiki/HTTPS) opiera się na [certyfikatach](https://en.wikipedia.org/wiki/Public_key_certificate) zaufania, tożsamości i szyfrowania.</span><span class="sxs-lookup"><span data-stu-id="3fd6a-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="3fd6a-106">W tym dokumencie wyjaśniono, jak uruchomić wstępnie utworzone obrazy kontenerów za pomocą protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3fd6a-106">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="3fd6a-107">Zobacz Tworzenie ASP.NET podstawowych aplikacji za pomocą platformy [Docker za pośrednictwem protokołu HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) dla scenariuszy rozwoju.</span><span class="sxs-lookup"><span data-stu-id="3fd6a-107">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="3fd6a-108">Ten przykład wymaga [platformy Docker 17.06](https://docs.docker.com/release-notes/docker-ce) lub nowszej [klienta platformy Docker.](https://www.docker.com/products/docker)</span><span class="sxs-lookup"><span data-stu-id="3fd6a-108">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3fd6a-109">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="3fd6a-109">Prerequisites</span></span>

<span data-ttu-id="3fd6a-110">Zestaw [SDK .NET Core 2.2](https://dotnet.microsoft.com/download) lub nowszy jest wymagany dla niektórych instrukcji zawartych w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="3fd6a-110">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="3fd6a-111">Certyfikaty</span><span class="sxs-lookup"><span data-stu-id="3fd6a-111">Certificates</span></span>

<span data-ttu-id="3fd6a-112">Certyfikat z [urzędu certyfikacji](https://wikipedia.org/wiki/Certificate_authority) jest wymagany do [hostingu produkcyjnego](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) dla domeny.</span><span class="sxs-lookup"><span data-stu-id="3fd6a-112">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="3fd6a-113">[Let's Encrypt](https://letsencrypt.org/)to urząd certyfikacji, który oferuje bezpłatne certyfikaty.</span><span class="sxs-lookup"><span data-stu-id="3fd6a-113">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="3fd6a-114">Ten dokument używa [samodzielnie podpisanych certyfikatów deweloperskich](https://wikipedia.org/wiki/Self-signed_certificate) do hostowania wstępnie utworzonych obrazów. `localhost`</span><span class="sxs-lookup"><span data-stu-id="3fd6a-114">This document uses [self-signed development certificates](https://wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="3fd6a-115">Instrukcje są podobne do używania certyfikatów produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="3fd6a-115">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="3fd6a-116">Dla świadectw produkcji:</span><span class="sxs-lookup"><span data-stu-id="3fd6a-116">For production certificates:</span></span>

* <span data-ttu-id="3fd6a-117">Narzędzie `dotnet dev-certs` nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="3fd6a-117">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="3fd6a-118">Certyfikaty nie muszą być przechowywane w lokalizacji użytej w instrukcjach.</span><span class="sxs-lookup"><span data-stu-id="3fd6a-118">Certificates don't need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="3fd6a-119">Przechowuj certyfikaty w dowolnym miejscu poza katalogiem lokacji.</span><span class="sxs-lookup"><span data-stu-id="3fd6a-119">Store the certificates in any location outside the site directory.</span></span>

<span data-ttu-id="3fd6a-120">Instrukcje zawarte w poniższej sekcji zainstalować certyfikaty `volumes` woluminu do kontenerów przy użyciu właściwości w *docker-compose.yml.*</span><span class="sxs-lookup"><span data-stu-id="3fd6a-120">The instructions contained in the following section volume mount certificates into containers using the `volumes` property in *docker-compose.yml.*</span></span> <span data-ttu-id="3fd6a-121">Można dodać certyfikaty do `COPY` obrazów kontenerów za pomocą polecenia w *pliku Dockerfile*, ale nie jest to zalecane.</span><span class="sxs-lookup"><span data-stu-id="3fd6a-121">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="3fd6a-122">Kopiowanie certyfikatów do obrazu nie jest zalecane z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="3fd6a-122">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="3fd6a-123">Utrudnia użycie tego samego obrazu do testowania z certyfikatami dewelopera.</span><span class="sxs-lookup"><span data-stu-id="3fd6a-123">It makes it difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="3fd6a-124">Utrudnia to użycie tego samego obrazu dla hostingu z certyfikatami produkcyjnymi.</span><span class="sxs-lookup"><span data-stu-id="3fd6a-124">It makes it difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="3fd6a-125">Istnieje znaczne ryzyko ujawnienia certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="3fd6a-125">There is significant risk of certificate disclosure.</span></span>

## <a name="starting-a-container-with-https-support-using-docker-compose"></a><span data-ttu-id="3fd6a-126">Uruchamianie kontenera z obsługą https przy użyciu kompozycji docker</span><span class="sxs-lookup"><span data-stu-id="3fd6a-126">Starting a container with https support using docker compose</span></span>

<span data-ttu-id="3fd6a-127">Użyj poniższych instrukcji dotyczących konfiguracji systemu operacyjnego.</span><span class="sxs-lookup"><span data-stu-id="3fd6a-127">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="3fd6a-128">System Windows korzystający z kontenerów systemu Linux</span><span class="sxs-lookup"><span data-stu-id="3fd6a-128">Windows using Linux containers</span></span>

<span data-ttu-id="3fd6a-129">Generowanie certyfikatu i konfigurowanie komputera lokalnego:</span><span class="sxs-lookup"><span data-stu-id="3fd6a-129">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="3fd6a-130">W poprzednich poleceniach zastąp `{ password here }` hasłem.</span><span class="sxs-lookup"><span data-stu-id="3fd6a-130">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="3fd6a-131">Utwórz plik _docker-compose.debug.yml_ z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="3fd6a-131">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

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
<span data-ttu-id="3fd6a-132">Hasło określone w pliku redagowania docker musi być zgodne z hasłem używanym dla certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="3fd6a-132">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="3fd6a-133">Uruchom kontener z ASP.NET Core skonfigurowanym dla protokołu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="3fd6a-133">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a><span data-ttu-id="3fd6a-134">macOS lub Linux</span><span class="sxs-lookup"><span data-stu-id="3fd6a-134">macOS or Linux</span></span>

<span data-ttu-id="3fd6a-135">Generowanie certyfikatu i konfigurowanie komputera lokalnego:</span><span class="sxs-lookup"><span data-stu-id="3fd6a-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="3fd6a-136">`dotnet dev-certs https --trust`jest obsługiwana tylko w systemach macOS i Windows.</span><span class="sxs-lookup"><span data-stu-id="3fd6a-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="3fd6a-137">Musisz ufać certyfikatom w systemie Linux w sposób obsługiwany przez twoją dystrybucję.</span><span class="sxs-lookup"><span data-stu-id="3fd6a-137">You need to trust certificates on Linux in the way that is supported by your distro.</span></span> <span data-ttu-id="3fd6a-138">Jest prawdopodobne, że musisz zaufać certyfikatowi w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="3fd6a-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="3fd6a-139">W poprzednich poleceniach zastąp `{ password here }` hasłem.</span><span class="sxs-lookup"><span data-stu-id="3fd6a-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="3fd6a-140">Utwórz plik _docker-compose.debug.yml_ z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="3fd6a-140">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

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
<span data-ttu-id="3fd6a-141">Hasło określone w pliku redagowania docker musi być zgodne z hasłem używanym dla certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="3fd6a-141">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="3fd6a-142">Uruchom kontener z ASP.NET Core skonfigurowanym dla protokołu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="3fd6a-142">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a><span data-ttu-id="3fd6a-143">System Windows przy użyciu kontenerów systemu Windows</span><span class="sxs-lookup"><span data-stu-id="3fd6a-143">Windows using Windows containers</span></span>

<span data-ttu-id="3fd6a-144">Generowanie certyfikatu i konfigurowanie komputera lokalnego:</span><span class="sxs-lookup"><span data-stu-id="3fd6a-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="3fd6a-145">W poprzednich poleceniach zastąp `{ password here }` hasłem.</span><span class="sxs-lookup"><span data-stu-id="3fd6a-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="3fd6a-146">Utwórz plik _docker-compose.debug.yml_ z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="3fd6a-146">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

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
<span data-ttu-id="3fd6a-147">Hasło określone w pliku redagowania docker musi być zgodne z hasłem używanym dla certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="3fd6a-147">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="3fd6a-148">Uruchom kontener z ASP.NET Core skonfigurowanym dla protokołu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="3fd6a-148">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
