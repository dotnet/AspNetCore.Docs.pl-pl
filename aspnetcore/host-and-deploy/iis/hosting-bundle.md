---
title: Pakiet hostingu
author: rick-anderson
description: Dowiedz się, jak skonfigurować pakiet hostingu platformy .NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: ecf3dd45575390eee263a275e7f1fb9ec50011bb
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058444"
---
# <a name="the-net-core-hosting-bundle"></a><span data-ttu-id="63275-103">Pakiet hostingu platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="63275-103">The .NET Core Hosting Bundle</span></span>

<span data-ttu-id="63275-104">Pakiet hostingu platformy .NET Core jest instalatorem środowiska uruchomieniowego .NET Core i [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="63275-104">The .NET Core Hosting bundle is an installer for the .NET Core Runtime and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="63275-105">Pakiet umożliwia uruchamianie ASP.NET Core aplikacji z usługami IIS.</span><span class="sxs-lookup"><span data-stu-id="63275-105">The bundle allows ASP.NET Core apps to run with IIS.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="63275-106">Zainstaluj pakiet hostingu platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="63275-106">Install the .NET Core Hosting Bundle</span></span>

> [!IMPORTANT]
> <span data-ttu-id="63275-107">Jeśli pakiet hostingu jest zainstalowany przed usługami IIS, należy naprawić pakiet instalacyjny.</span><span class="sxs-lookup"><span data-stu-id="63275-107">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="63275-108">Uruchom ponownie Instalatora pakietu hostingu po zainstalowaniu usług IIS.</span><span class="sxs-lookup"><span data-stu-id="63275-108">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="63275-109">Jeśli pakiet hostingu jest instalowany po zainstalowaniu 64-bitowej (x64) wersji programu .NET Core, prawdopodobnie brakuje zestawów SDK ([nie wykryto żadnych zestawów SDK dla platformy .NET Core](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="63275-109">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="63275-110">Aby rozwiązać ten problem, zobacz <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> .</span><span class="sxs-lookup"><span data-stu-id="63275-110">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="63275-111">Pobieranie bezpośrednie (aktualna wersja)</span><span class="sxs-lookup"><span data-stu-id="63275-111">Direct download (current version)</span></span>

<span data-ttu-id="63275-112">Pobierz instalatora przy użyciu następującego linku:</span><span class="sxs-lookup"><span data-stu-id="63275-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="63275-113">Bieżący Instalator pakietu hostingu platformy .NET Core (Pobieranie bezpośrednie)</span><span class="sxs-lookup"><span data-stu-id="63275-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="63275-114">Starsze wersje Instalatora</span><span class="sxs-lookup"><span data-stu-id="63275-114">Earlier versions of the installer</span></span>

<span data-ttu-id="63275-115">Aby uzyskać wcześniejszą wersję Instalatora:</span><span class="sxs-lookup"><span data-stu-id="63275-115">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="63275-116">Przejdź do strony [pobieranie platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .</span><span class="sxs-lookup"><span data-stu-id="63275-116">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="63275-117">Wybierz żądaną wersję programu .NET Core.</span><span class="sxs-lookup"><span data-stu-id="63275-117">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="63275-118">W kolumnie **Uruchom aplikacje — środowisko uruchomieniowe** Znajdź wiersz żądanej wersji środowiska uruchomieniowego platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="63275-118">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="63275-119">Pobierz instalatora przy użyciu linku do **pakietu hostingu** .</span><span class="sxs-lookup"><span data-stu-id="63275-119">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="63275-120">Niektóre Instalatory zawierają wersje wydania, które osiągnęły koniec cyklu życia (EOL) i nie są już obsługiwane przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="63275-120">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="63275-121">Aby uzyskać więcej informacji, zobacz [zasady pomocy technicznej](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="63275-121">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="63275-122">Zainstaluj pakiet hostingu</span><span class="sxs-lookup"><span data-stu-id="63275-122">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="63275-123">Uruchom Instalatora na serwerze.</span><span class="sxs-lookup"><span data-stu-id="63275-123">Run the installer on the server.</span></span> <span data-ttu-id="63275-124">Następujące parametry są dostępne podczas uruchamiania Instalatora z powłoki poleceń administratora:</span><span class="sxs-lookup"><span data-stu-id="63275-124">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="63275-125">`OPT_NO_ANCM=1`: Pomiń Instalowanie modułu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63275-125">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="63275-126">`OPT_NO_RUNTIME=1`: Pomiń Instalowanie środowiska uruchomieniowego platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="63275-126">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="63275-127">Używany, gdy serwer obsługuje tylko [wdrożenia samodzielne (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="63275-127">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="63275-128">`OPT_NO_SHAREDFX=1`: Pomiń instalację ASP.NET Shared Framework (ASP.NET Runtime).</span><span class="sxs-lookup"><span data-stu-id="63275-128">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="63275-129">Używany, gdy serwer obsługuje tylko [wdrożenia samodzielne (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="63275-129">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="63275-130">`OPT_NO_X86=1`: Pomiń Instalowanie środowiska uruchomieniowego x86.</span><span class="sxs-lookup"><span data-stu-id="63275-130">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="63275-131">Użyj tego parametru, Jeśli wiesz, że nie będziesz hostować aplikacji 32-bitowych.</span><span class="sxs-lookup"><span data-stu-id="63275-131">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="63275-132">Jeśli w przyszłości będziesz hostować zarówno aplikacje 32-bitowe, jak i 64-bitowe, nie używaj tego parametru i zainstaluj oba środowiska uruchomieniowe.</span><span class="sxs-lookup"><span data-stu-id="63275-132">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="63275-133">`OPT_NO_SHARED_CONFIG_CHECK=1`: Wyłącz sprawdzanie przy użyciu konfiguracji udostępnionej usług IIS, gdy konfiguracja udostępniona ( `applicationHost.config` ) znajduje się na tym samym komputerze, na którym zainstalowano program IIS.</span><span class="sxs-lookup"><span data-stu-id="63275-133">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="63275-134">*Dostępne tylko w przypadku ASP.NET Core 2,2 lub nowszych instalatorów pakietu do obsługi.*</span><span class="sxs-lookup"><span data-stu-id="63275-134">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="63275-135">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="63275-135">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="63275-136">Uruchom ponownie system lub wykonaj następujące polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="63275-136">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="63275-137">Ponowne uruchomienie usług IIS powoduje zmianę ścieżki systemowej, która jest zmienną środowiskową, wykonaną przez Instalatora.</span><span class="sxs-lookup"><span data-stu-id="63275-137">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="63275-138">ASP.NET Core nie przyjmuje zachowania z przekazaniem do przodu dla wersji poprawki współużytkowanych pakietów platformy.</span><span class="sxs-lookup"><span data-stu-id="63275-138">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="63275-139">Po uaktualnieniu udostępnionej platformy, instalując nowy pakiet hostingu, ponownie uruchom system lub wykonaj następujące polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="63275-139">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="63275-140">Aby uzyskać informacje na temat konfiguracji udostępnionej usług IIS, zobacz [ASP.NET Core Module z udostępnioną konfiguracją usług IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="63275-140">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="63275-141">Dzienniki instalacji pakietu i pakietów hostingu</span><span class="sxs-lookup"><span data-stu-id="63275-141">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="63275-142">Aby określić wersję zainstalowanego modułu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="63275-142">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="63275-143">W systemie hostingu przejdź do `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2` .</span><span class="sxs-lookup"><span data-stu-id="63275-143">On the hosting system, navigate to `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span></span>
1. <span data-ttu-id="63275-144">Znajdź `aspnetcorev2.dll` plik.</span><span class="sxs-lookup"><span data-stu-id="63275-144">Locate the `aspnetcorev2.dll` file.</span></span>
1. <span data-ttu-id="63275-145">Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Właściwości** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="63275-145">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="63275-146">Wybierz kartę **szczegóły** . **Wersja pliku** i **Wersja produktu** reprezentują zainstalowaną wersję modułu.</span><span class="sxs-lookup"><span data-stu-id="63275-146">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="63275-147">Dzienniki Instalatora pakietu hostingu dla modułu znajdują się pod adresem `C:\Users\%UserName%\AppData\Local\Temp` .</span><span class="sxs-lookup"><span data-stu-id="63275-147">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="63275-148">Plik ma nazwę `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log` , gdzie symbol zastępczy `{TIMESTAMP}` to sygnatura czasowa pliku.</span><span class="sxs-lookup"><span data-stu-id="63275-148">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp of the file.</span></span>
