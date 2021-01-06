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
ms.openlocfilehash: a580c70d3141177be2508a0513f612eee56dbbf9
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93343640"
---
# <a name="the-net-core-hosting-bundle"></a><span data-ttu-id="9a042-103">Pakiet hostingu platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="9a042-103">The .NET Core Hosting Bundle</span></span>

<span data-ttu-id="9a042-104">Pakiet hostingu platformy .NET Core jest instalatorem środowiska uruchomieniowego .NET Core i [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="9a042-104">The .NET Core Hosting bundle is an installer for the .NET Core Runtime and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="9a042-105">Pakiet umożliwia uruchamianie ASP.NET Core aplikacji z usługami IIS.</span><span class="sxs-lookup"><span data-stu-id="9a042-105">The bundle allows ASP.NET Core apps to run with IIS.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="9a042-106">Zainstaluj pakiet hostingu platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="9a042-106">Install the .NET Core Hosting Bundle</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9a042-107">Jeśli pakiet hostingu jest zainstalowany przed usługami IIS, należy naprawić pakiet instalacyjny.</span><span class="sxs-lookup"><span data-stu-id="9a042-107">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="9a042-108">Uruchom ponownie Instalatora pakietu hostingu po zainstalowaniu usług IIS.</span><span class="sxs-lookup"><span data-stu-id="9a042-108">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="9a042-109">Jeśli pakiet hostingu jest instalowany po zainstalowaniu 64-bitowej (x64) wersji programu .NET Core, prawdopodobnie brakuje zestawów SDK ([nie wykryto żadnych zestawów SDK dla platformy .NET Core](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="9a042-109">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="9a042-110">Aby rozwiązać ten problem, zobacz <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> .</span><span class="sxs-lookup"><span data-stu-id="9a042-110">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

## <a name="direct-download-current-version"></a><span data-ttu-id="9a042-111">Pobieranie bezpośrednie (aktualna wersja)</span><span class="sxs-lookup"><span data-stu-id="9a042-111">Direct download (current version)</span></span>

<span data-ttu-id="9a042-112">Pobierz instalatora przy użyciu następującego linku:</span><span class="sxs-lookup"><span data-stu-id="9a042-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="9a042-113">Bieżący Instalator pakietu hostingu platformy .NET Core (Pobieranie bezpośrednie)</span><span class="sxs-lookup"><span data-stu-id="9a042-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

## <a name="visual-c-redistributable-requirement"></a><span data-ttu-id="9a042-114">Visual C++ wymaganie redystrybucyjne</span><span class="sxs-lookup"><span data-stu-id="9a042-114">Visual C++ Redistributable Requirement</span></span>

<span data-ttu-id="9a042-115">W starszych wersjach systemu Windows, na przykład Windows Server 2012 R2, zainstaluj pakiet redystrybucyjny programu Visual Studio C++ 2015, 2017, 2019.</span><span class="sxs-lookup"><span data-stu-id="9a042-115">On older versions of Windows, for example Windows Server 2012 R2, install the Visual Studio C++ 2015, 2017, 2019 Redistributable.</span></span> <span data-ttu-id="9a042-116">W przeciwnym razie w raportach dzienników zdarzeń systemu Windows jest mylący komunikat o błędzie, który `The data is the error.`</span><span class="sxs-lookup"><span data-stu-id="9a042-116">Otherwise, a confusing error message in the Windows Event Log reports that `The data is the error.`</span></span>

<span data-ttu-id="9a042-117">[Bieżący pakiet redystrybucyjny](https://aka.ms/vs/16/release/vc_redist.x64.exe) 
 x64 vs C++ [Bieżący pakiet redystrybucyjny x86 vs C++](https://aka.ms/vs/16/release/vc_redist.x86.exe)</span><span class="sxs-lookup"><span data-stu-id="9a042-117">[Current x64 VS C++ redistributable](https://aka.ms/vs/16/release/vc_redist.x64.exe)
[Current x86 VS C++ redistributable](https://aka.ms/vs/16/release/vc_redist.x86.exe)</span></span>

## <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="9a042-118">Starsze wersje Instalatora</span><span class="sxs-lookup"><span data-stu-id="9a042-118">Earlier versions of the installer</span></span>

<span data-ttu-id="9a042-119">Aby uzyskać wcześniejszą wersję Instalatora:</span><span class="sxs-lookup"><span data-stu-id="9a042-119">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="9a042-120">Przejdź do strony [pobieranie platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .</span><span class="sxs-lookup"><span data-stu-id="9a042-120">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="9a042-121">Wybierz żądaną wersję programu .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9a042-121">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="9a042-122">W kolumnie **Uruchom aplikacje — środowisko uruchomieniowe** Znajdź wiersz żądanej wersji środowiska uruchomieniowego platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9a042-122">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="9a042-123">Pobierz instalatora przy użyciu linku do **pakietu hostingu** .</span><span class="sxs-lookup"><span data-stu-id="9a042-123">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="9a042-124">Niektóre Instalatory zawierają wersje wydania, które osiągnęły koniec cyklu życia (EOL) i nie są już obsługiwane przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="9a042-124">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="9a042-125">Aby uzyskać więcej informacji, zobacz [zasady pomocy technicznej](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="9a042-125">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

## <a name="options"></a><span data-ttu-id="9a042-126">Opcje</span><span class="sxs-lookup"><span data-stu-id="9a042-126">Options</span></span>

1. <span data-ttu-id="9a042-127">Następujące parametry są dostępne podczas uruchamiania Instalatora z powłoki poleceń administratora:</span><span class="sxs-lookup"><span data-stu-id="9a042-127">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="9a042-128">`OPT_NO_ANCM=1`: Pomiń Instalowanie modułu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9a042-128">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="9a042-129">`OPT_NO_RUNTIME=1`: Pomiń Instalowanie środowiska uruchomieniowego platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9a042-129">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="9a042-130">Używany, gdy serwer obsługuje tylko [wdrożenia samodzielne (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="9a042-130">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="9a042-131">`OPT_NO_SHAREDFX=1`: Pomiń instalację ASP.NET Shared Framework (ASP.NET Runtime).</span><span class="sxs-lookup"><span data-stu-id="9a042-131">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="9a042-132">Używany, gdy serwer obsługuje tylko [wdrożenia samodzielne (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="9a042-132">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="9a042-133">`OPT_NO_X86=1`: Pomiń Instalowanie środowiska uruchomieniowego x86.</span><span class="sxs-lookup"><span data-stu-id="9a042-133">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="9a042-134">Użyj tego parametru, Jeśli wiesz, że nie będziesz hostować aplikacji 32-bitowych.</span><span class="sxs-lookup"><span data-stu-id="9a042-134">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="9a042-135">Jeśli w przyszłości będziesz hostować zarówno aplikacje 32-bitowe, jak i 64-bitowe, nie używaj tego parametru i zainstaluj oba środowiska uruchomieniowe.</span><span class="sxs-lookup"><span data-stu-id="9a042-135">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="9a042-136">`OPT_NO_SHARED_CONFIG_CHECK=1`: Wyłącz sprawdzanie przy użyciu konfiguracji udostępnionej usług IIS, gdy konfiguracja udostępniona ( `applicationHost.config` ) znajduje się na tym samym komputerze, na którym zainstalowano program IIS.</span><span class="sxs-lookup"><span data-stu-id="9a042-136">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="9a042-137">*Dostępne tylko w przypadku ASP.NET Core 2,2 lub nowszych instalatorów pakietu do obsługi.*</span><span class="sxs-lookup"><span data-stu-id="9a042-137">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="9a042-138">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="9a042-138">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>

> [!NOTE]
> <span data-ttu-id="9a042-139">Aby uzyskać informacje na temat konfiguracji udostępnionej usług IIS, zobacz [ASP.NET Core Module z udostępnioną konfiguracją usług IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="9a042-139">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="restart-iis"></a><span data-ttu-id="9a042-140">Uruchom ponownie usługi IIS</span><span class="sxs-lookup"><span data-stu-id="9a042-140">Restart IIS</span></span>

<span data-ttu-id="9a042-141">Po zainstalowaniu pakietu hostingu może być wymagane ręczne ponowne uruchomienie usług IIS.</span><span class="sxs-lookup"><span data-stu-id="9a042-141">After the Hosting Bundle is installed, a manual IIS restart may be required.</span></span> <span data-ttu-id="9a042-142">Na przykład `dotnet` Narzędzia interfejsu wiersza polecenia (polecenie) mogą nie istnieć w ścieżce do uruchamiania procesów roboczych usług IIS.</span><span class="sxs-lookup"><span data-stu-id="9a042-142">For example, the `dotnet` CLI tooling (command) might not exist on the PATH for running IIS worker processes.</span></span>

<span data-ttu-id="9a042-143">Aby ręcznie zatrzymać i uruchomić usługi IIS, wykonaj następujące polecenia w powłoce poleceń z podwyższonym poziomem uprawnień:</span><span class="sxs-lookup"><span data-stu-id="9a042-143">To manually stop and start IIS, execute the following commands in an elevated command shell:</span></span>

```console
net stop was /y
net start w3svc
```

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="9a042-144">Dzienniki instalacji pakietu i pakietów hostingu</span><span class="sxs-lookup"><span data-stu-id="9a042-144">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="9a042-145">Aby określić wersję zainstalowanego modułu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="9a042-145">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="9a042-146">W systemie hostingu przejdź do `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2` .</span><span class="sxs-lookup"><span data-stu-id="9a042-146">On the hosting system, navigate to `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span></span>
1. <span data-ttu-id="9a042-147">Znajdź `aspnetcorev2.dll` plik.</span><span class="sxs-lookup"><span data-stu-id="9a042-147">Locate the `aspnetcorev2.dll` file.</span></span>
1. <span data-ttu-id="9a042-148">Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Właściwości** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="9a042-148">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="9a042-149">Wybierz kartę **szczegóły** . **Wersja pliku** i **Wersja produktu** reprezentują zainstalowaną wersję modułu.</span><span class="sxs-lookup"><span data-stu-id="9a042-149">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="9a042-150">Dzienniki Instalatora pakietu hostingu dla modułu znajdują się pod adresem `C:\Users\%UserName%\AppData\Local\Temp` .</span><span class="sxs-lookup"><span data-stu-id="9a042-150">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="9a042-151">Plik ma nazwę `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log` , gdzie symbol zastępczy `{TIMESTAMP}` to sygnatura czasowa pliku.</span><span class="sxs-lookup"><span data-stu-id="9a042-151">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp of the file.</span></span>
