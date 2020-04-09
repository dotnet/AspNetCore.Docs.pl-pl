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
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a>Hostowanie obrazów ASP.NET podstawowych za pomocą dokowanewyskusić za pośrednictwem protokołu HTTPS


ASP.NET Core domyślnie używa [protokołu HTTPS](/aspnet/core/security/enforcing-ssl). [Protokół HTTPS](https://en.wikipedia.org/wiki/HTTPS) opiera się na [certyfikatach](https://en.wikipedia.org/wiki/Public_key_certificate) zaufania, tożsamości i szyfrowania.

W tym dokumencie wyjaśniono, jak uruchomić wstępnie utworzone obrazy kontenerów za pomocą protokołu HTTPS.

Zobacz Tworzenie ASP.NET podstawowych aplikacji za pomocą platformy [Docker za pośrednictwem protokołu HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) dla scenariuszy rozwoju.

Ten przykład wymaga [platformy Docker 17.06](https://docs.docker.com/release-notes/docker-ce) lub nowszej [klienta platformy Docker.](https://www.docker.com/products/docker)

## <a name="prerequisites"></a>Wymagania wstępne

Zestaw [SDK .NET Core 2.2](https://dotnet.microsoft.com/download) lub nowszy jest wymagany dla niektórych instrukcji zawartych w tym dokumencie.

## <a name="certificates"></a>Certyfikaty

Certyfikat z [urzędu certyfikacji](https://wikipedia.org/wiki/Certificate_authority) jest wymagany do [hostingu produkcyjnego](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) dla domeny. [Let's Encrypt](https://letsencrypt.org/)to urząd certyfikacji, który oferuje bezpłatne certyfikaty.

Ten dokument używa [samodzielnie podpisanych certyfikatów deweloperskich](https://wikipedia.org/wiki/Self-signed_certificate) do hostowania wstępnie utworzonych obrazów. `localhost` Instrukcje są podobne do używania certyfikatów produkcyjnych.

Dla świadectw produkcji:

* Narzędzie `dotnet dev-certs` nie jest wymagane.
* Certyfikaty nie muszą być przechowywane w lokalizacji użytej w instrukcjach. Przechowuj certyfikaty w dowolnym miejscu poza katalogiem lokacji.

Instrukcje zawarte w poniższej sekcji zainstalować certyfikaty `volumes` woluminu do kontenerów przy użyciu właściwości w *docker-compose.yml.* Można dodać certyfikaty do `COPY` obrazów kontenerów za pomocą polecenia w *pliku Dockerfile*, ale nie jest to zalecane. Kopiowanie certyfikatów do obrazu nie jest zalecane z następujących powodów:

* Utrudnia użycie tego samego obrazu do testowania z certyfikatami dewelopera.
* Utrudnia to użycie tego samego obrazu dla hostingu z certyfikatami produkcyjnymi.
* Istnieje znaczne ryzyko ujawnienia certyfikatu.

## <a name="starting-a-container-with-https-support-using-docker-compose"></a>Uruchamianie kontenera z obsługą https przy użyciu kompozycji docker

Użyj poniższych instrukcji dotyczących konfiguracji systemu operacyjnego.

### <a name="windows-using-linux-containers"></a>System Windows korzystający z kontenerów systemu Linux

Generowanie certyfikatu i konfigurowanie komputera lokalnego:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

W poprzednich poleceniach zastąp `{ password here }` hasłem.

Utwórz plik _docker-compose.debug.yml_ z następującą zawartością:

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
Hasło określone w pliku redagowania docker musi być zgodne z hasłem używanym dla certyfikatu.

Uruchom kontener z ASP.NET Core skonfigurowanym dla protokołu HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a>macOS lub Linux

Generowanie certyfikatu i konfigurowanie komputera lokalnego:

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

`dotnet dev-certs https --trust`jest obsługiwana tylko w systemach macOS i Windows. Musisz ufać certyfikatom w systemie Linux w sposób obsługiwany przez twoją dystrybucję. Jest prawdopodobne, że musisz zaufać certyfikatowi w przeglądarce.

W poprzednich poleceniach zastąp `{ password here }` hasłem.

Utwórz plik _docker-compose.debug.yml_ z następującą zawartością:

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
Hasło określone w pliku redagowania docker musi być zgodne z hasłem używanym dla certyfikatu.

Uruchom kontener z ASP.NET Core skonfigurowanym dla protokołu HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a>System Windows przy użyciu kontenerów systemu Windows

Generowanie certyfikatu i konfigurowanie komputera lokalnego:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

W poprzednich poleceniach zastąp `{ password here }` hasłem.

Utwórz plik _docker-compose.debug.yml_ z następującą zawartością:

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
Hasło określone w pliku redagowania docker musi być zgodne z hasłem używanym dla certyfikatu.

Uruchom kontener z ASP.NET Core skonfigurowanym dla protokołu HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
