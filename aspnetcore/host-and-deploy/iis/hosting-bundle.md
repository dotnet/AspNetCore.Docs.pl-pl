---
title: Pakiet hostingu
author: rick-anderson
description: Dowiedz się, jak skonfigurować pakiet hostingu platformy .NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: ecf3dd45575390eee263a275e7f1fb9ec50011bb
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058444"
---
# <a name="the-net-core-hosting-bundle"></a>Pakiet hostingu platformy .NET Core

Pakiet hostingu platformy .NET Core jest instalatorem środowiska uruchomieniowego .NET Core i [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Pakiet umożliwia uruchamianie ASP.NET Core aplikacji z usługami IIS.

## <a name="install-the-net-core-hosting-bundle"></a>Zainstaluj pakiet hostingu platformy .NET Core

> [!IMPORTANT]
> Jeśli pakiet hostingu jest zainstalowany przed usługami IIS, należy naprawić pakiet instalacyjny. Uruchom ponownie Instalatora pakietu hostingu po zainstalowaniu usług IIS.
>
> Jeśli pakiet hostingu jest instalowany po zainstalowaniu 64-bitowej (x64) wersji programu .NET Core, prawdopodobnie brakuje zestawów SDK ([nie wykryto żadnych zestawów SDK dla platformy .NET Core](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Aby rozwiązać ten problem, zobacz <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> .

### <a name="direct-download-current-version"></a>Pobieranie bezpośrednie (aktualna wersja)

Pobierz instalatora przy użyciu następującego linku:

[Bieżący Instalator pakietu hostingu platformy .NET Core (Pobieranie bezpośrednie)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>Starsze wersje Instalatora

Aby uzyskać wcześniejszą wersję Instalatora:

1. Przejdź do strony [pobieranie platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .
1. Wybierz żądaną wersję programu .NET Core.
1. W kolumnie **Uruchom aplikacje — środowisko uruchomieniowe** Znajdź wiersz żądanej wersji środowiska uruchomieniowego platformy .NET Core.
1. Pobierz instalatora przy użyciu linku do **pakietu hostingu** .

> [!WARNING]
> Niektóre Instalatory zawierają wersje wydania, które osiągnęły koniec cyklu życia (EOL) i nie są już obsługiwane przez firmę Microsoft. Aby uzyskać więcej informacji, zobacz [zasady pomocy technicznej](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Zainstaluj pakiet hostingu

1. Uruchom Instalatora na serwerze. Następujące parametry są dostępne podczas uruchamiania Instalatora z powłoki poleceń administratora:

   * `OPT_NO_ANCM=1`: Pomiń Instalowanie modułu ASP.NET Core.
   * `OPT_NO_RUNTIME=1`: Pomiń Instalowanie środowiska uruchomieniowego platformy .NET Core. Używany, gdy serwer obsługuje tylko [wdrożenia samodzielne (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_SHAREDFX=1`: Pomiń instalację ASP.NET Shared Framework (ASP.NET Runtime). Używany, gdy serwer obsługuje tylko [wdrożenia samodzielne (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_X86=1`: Pomiń Instalowanie środowiska uruchomieniowego x86. Użyj tego parametru, Jeśli wiesz, że nie będziesz hostować aplikacji 32-bitowych. Jeśli w przyszłości będziesz hostować zarówno aplikacje 32-bitowe, jak i 64-bitowe, nie używaj tego parametru i zainstaluj oba środowiska uruchomieniowe.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`: Wyłącz sprawdzanie przy użyciu konfiguracji udostępnionej usług IIS, gdy konfiguracja udostępniona ( `applicationHost.config` ) znajduje się na tym samym komputerze, na którym zainstalowano program IIS. *Dostępne tylko w przypadku ASP.NET Core 2,2 lub nowszych instalatorów pakietu do obsługi.* Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Uruchom ponownie system lub wykonaj następujące polecenia w powłoce poleceń:

   ```console
   net stop was /y
   net start w3svc
   ```
   Ponowne uruchomienie usług IIS powoduje zmianę ścieżki systemowej, która jest zmienną środowiskową, wykonaną przez Instalatora.

ASP.NET Core nie przyjmuje zachowania z przekazaniem do przodu dla wersji poprawki współużytkowanych pakietów platformy. Po uaktualnieniu udostępnionej platformy, instalując nowy pakiet hostingu, ponownie uruchom system lub wykonaj następujące polecenia w powłoce poleceń:

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> Aby uzyskać informacje na temat konfiguracji udostępnionej usług IIS, zobacz [ASP.NET Core Module z udostępnioną konfiguracją usług IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Dzienniki instalacji pakietu i pakietów hostingu

Aby określić wersję zainstalowanego modułu ASP.NET Core:

1. W systemie hostingu przejdź do `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2` .
1. Znajdź `aspnetcorev2.dll` plik.
1. Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Właściwości** z menu kontekstowego.
1. Wybierz kartę **szczegóły** . **Wersja pliku** i **Wersja produktu** reprezentują zainstalowaną wersję modułu.

Dzienniki Instalatora pakietu hostingu dla modułu znajdują się pod adresem `C:\Users\%UserName%\AppData\Local\Temp` . Plik ma nazwę `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log` , gdzie symbol zastępczy `{TIMESTAMP}` to sygnatura czasowa pliku.
