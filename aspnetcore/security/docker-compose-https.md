---
title: Hostowanie ASP.NET Core obrazu w kontenerze za pomocą funkcji tworzenia aplikacji platformy Docker przy użyciu protokołu HTTPS
author: ravipal
description: Dowiedz się, jak hostować ASP.NET Core obrazy z Docker Compose za pośrednictwem protokołu HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
no-loc:
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
ms.openlocfilehash: c3b627cdc74f1b40611d84bc3419e678e2dfbba4
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022462"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a>Hostowanie ASP.NET Core obrazów z Docker Compose za pośrednictwem protokołu HTTPS


ASP.NET Core [domyślnie używa protokołu HTTPS](/aspnet/core/security/enforcing-ssl). [Protokół https](https://en.wikipedia.org/wiki/HTTPS) opiera się na [certyfikatach](https://en.wikipedia.org/wiki/Public_key_certificate) do zaufania, tożsamości i szyfrowania.

W tym dokumencie wyjaśniono, jak uruchamiać wstępnie skompilowane obrazy kontenerów przy użyciu protokołu HTTPS.

Zobacz [Tworzenie aplikacji ASP.NET Core przy użyciu platformy Docker za pośrednictwem protokołu HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) na potrzeby scenariuszy programistycznych.

Ten przykład wymaga [platformy docker 17,06](https://docs.docker.com/release-notes/docker-ce) lub nowszej wersji [klienta platformy Docker](https://www.docker.com/products/docker).

## <a name="prerequisites"></a>Wymagania wstępne

W przypadku niektórych instrukcji przedstawionych w tym dokumencie wymagany jest [zestaw .NET Core 2,2 SDK](https://dotnet.microsoft.com/download) lub nowszy.

## <a name="certificates"></a>Certyfikaty

Certyfikat z [urzędu certyfikacji](https://wikipedia.org/wiki/Certificate_authority) jest wymagany do hostingu w [środowisku produkcyjnym](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) dla domeny. [Let's Encrypt](https://letsencrypt.org/)to urząd certyfikacji, który oferuje bezpłatne certyfikaty.

W tym dokumencie są stosowane [Certyfikaty deweloperskie](https://wikipedia.org/wiki/Self-signed_certificate) z podpisem własnym do obsługi wstępnie utworzonych obrazów `localhost` . Instrukcje są podobne do korzystania z certyfikatów produkcyjnych.

Dla certyfikatów produkcyjnych:

* `dotnet dev-certs`Narzędzie nie jest wymagane.
* Certyfikaty nie muszą być przechowywane w lokalizacji używanej w instrukcjach. Przechowuj certyfikaty w dowolnej lokalizacji poza katalogiem lokacji.

Instrukcje zawarte w poniższej sekcji dotyczą instalowania certyfikatów do kontenerów przy użyciu `volumes` właściwości w *Docker-Compose. yml.* Można dodać certyfikaty do obrazów kontenerów za pomocą `COPY` polecenia w *pliku dockerfile*, ale nie jest to zalecane. Nie zaleca się kopiowania certyfikatów do obrazu z następujących powodów:

* Utrudnia to korzystanie z tego samego obrazu do testowania przy użyciu certyfikatów deweloperskich.
* Utrudnia korzystanie z tego samego obrazu na potrzeby hostowania z certyfikatami produkcyjnymi.
* Istnieje duże ryzyko ujawnienia certyfikatu.

## <a name="starting-a-container-with-https-support-using-docker-compose"></a>Uruchamianie kontenera z obsługą protokołu HTTPS przy użyciu funkcji tworzenia platformy Docker

Wykonaj następujące instrukcje dotyczące konfiguracji systemu operacyjnego.

### <a name="windows-using-linux-containers"></a>System Windows korzystający z kontenerów systemu Linux

Generuj certyfikat i skonfiguruj komputer lokalny:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

W poprzednich poleceniach Zastąp ciąg `{ password here }` hasłem.

Utwórz plik _Docker-Compose. Debug. yml_ o następującej zawartości:

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
Hasło określone w pliku redagowania platformy Docker musi być zgodne z hasłem użytym dla certyfikatu.

Uruchom kontener z ASP.NET Core skonfigurowanym dla protokołu HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a>macOS lub Linux

Generuj certyfikat i skonfiguruj komputer lokalny:

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

`dotnet dev-certs https --trust`jest obsługiwana tylko w systemach macOS i Windows. Należy zaufać certyfikatom w systemie Linux w sposób, który jest obsługiwany przez dystrybucję. Prawdopodobnie należy zaufać certyfikatowi w przeglądarce.

W poprzednich poleceniach Zastąp ciąg `{ password here }` hasłem.

Utwórz plik _Docker-Compose. Debug. yml_ o następującej zawartości:

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
Hasło określone w pliku redagowania platformy Docker musi być zgodne z hasłem użytym dla certyfikatu.

Uruchom kontener z ASP.NET Core skonfigurowanym dla protokołu HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a>Windows przy użyciu kontenerów systemu Windows

Generuj certyfikat i skonfiguruj komputer lokalny:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

W poprzednich poleceniach Zastąp ciąg `{ password here }` hasłem.

Utwórz plik _Docker-Compose. Debug. yml_ o następującej zawartości:

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
Hasło określone w pliku redagowania platformy Docker musi być zgodne z hasłem użytym dla certyfikatu.

Uruchom kontener z ASP.NET Core skonfigurowanym dla protokołu HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
