---
title: ASP.NET Core hosta w usłudze systemu Windows
author: rick-anderson
description: Dowiedz się, jak hostować aplikację ASP.NET Core w usłudze systemu Windows.
monikerRange: '>= aspnetcore-2.1'
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
uid: host-and-deploy/windows-service
ms.openlocfilehash: e5c7dd0e52f1246d3ac6ad9622573db4c276654b
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588818"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="df645-103">ASP.NET Core hosta w usłudze systemu Windows</span><span class="sxs-lookup"><span data-stu-id="df645-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="df645-104">Aplikacja ASP.NET Core może być hostowana w systemie Windows jako [Usługa systemu Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez korzystania z usług IIS.</span><span class="sxs-lookup"><span data-stu-id="df645-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="df645-105">Gdy usługa jest hostowana w systemie Windows, aplikacja jest uruchamiana automatycznie po ponownym uruchomieniu serwera.</span><span class="sxs-lookup"><span data-stu-id="df645-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="df645-106">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="df645-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="df645-107">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="df645-107">Prerequisites</span></span>

* [<span data-ttu-id="df645-108">ASP.NET Core zestaw SDK 2,1 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="df645-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="df645-109">Program PowerShell 6,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="df645-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="df645-110">Szablon usługi procesu roboczego</span><span class="sxs-lookup"><span data-stu-id="df645-110">Worker Service template</span></span>

<span data-ttu-id="df645-111">Szablon usługi ASP.NET Core Worker zapewnia punkt początkowy do pisania długotrwałych aplikacji usługi.</span><span class="sxs-lookup"><span data-stu-id="df645-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="df645-112">Aby użyć szablonu jako podstawy dla aplikacji usługi systemu Windows:</span><span class="sxs-lookup"><span data-stu-id="df645-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="df645-113">Utwórz aplikację usługi procesu roboczego na podstawie szablonu .NET Core.</span><span class="sxs-lookup"><span data-stu-id="df645-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="df645-114">Postępuj zgodnie ze wskazówkami w sekcji [Konfiguracja aplikacji](#app-configuration) , aby zaktualizować aplikację usługi procesu roboczego tak, aby mogła ona działać jako usługa systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="df645-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="df645-115">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="df645-115">App configuration</span></span>

<span data-ttu-id="df645-116">Aplikacja wymaga odwołania do pakietu dla elementu [Microsoft. Extensions. hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="df645-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="df645-117">`IHostBuilder.UseWindowsService` jest wywoływana podczas kompilowania hosta.</span><span class="sxs-lookup"><span data-stu-id="df645-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="df645-118">Jeśli aplikacja działa jako usługa systemu Windows, Metoda:</span><span class="sxs-lookup"><span data-stu-id="df645-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="df645-119">Ustawia okres istnienia hosta na `WindowsServiceLifetime` .</span><span class="sxs-lookup"><span data-stu-id="df645-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="df645-120">Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) na [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="df645-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="df645-121">Aby uzyskać więcej informacji, zapoznaj się z sekcją [Current Directory i root Content](#current-directory-and-content-root) .</span><span class="sxs-lookup"><span data-stu-id="df645-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="df645-122">Włącza rejestrowanie w dzienniku zdarzeń:</span><span class="sxs-lookup"><span data-stu-id="df645-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="df645-123">Nazwa aplikacji jest używana jako domyślna nazwa źródła.</span><span class="sxs-lookup"><span data-stu-id="df645-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="df645-124">Domyślny poziom rejestrowania jest *ostrzegawczy* lub wyższy dla aplikacji opartej na szablonie ASP.NET Core, który wywołuje, `CreateDefaultBuilder` Aby skompilować hosta.</span><span class="sxs-lookup"><span data-stu-id="df645-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="df645-125">Zastąp domyślny poziom dziennika `Logging:EventLog:LogLevel:Default` kluczem w *appsettings.json* / *appSettings. { Environment}. JSON* lub inny dostawca konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="df645-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="df645-126">Tylko Administratorzy mogą tworzyć nowe źródła zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="df645-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="df645-127">Gdy nie można utworzyć źródła zdarzeń przy użyciu nazwy aplikacji, w źródle *aplikacji* jest rejestrowane ostrzeżenie, a dzienniki zdarzeń są wyłączone.</span><span class="sxs-lookup"><span data-stu-id="df645-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="df645-128">W `CreateHostBuilder` programie *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="df645-128">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="df645-129">Następujące przykładowe aplikacje zostały dołączone do tego tematu:</span><span class="sxs-lookup"><span data-stu-id="df645-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="df645-130">Przykład usługi procesu roboczego w tle: przykład aplikacji innej niż Internet oparta na [szablonie usługi procesu roboczego](#worker-service-template) , który korzysta z [usług hostowanych](xref:fundamentals/host/hosted-services) na potrzeby zadań w tle.</span><span class="sxs-lookup"><span data-stu-id="df645-130">Background Worker Service Sample: A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="df645-131">Przykład App Service sieci Web: Razor Przykładowa aplikacja sieci Web, która działa jako usługa systemu Windows z [usługami hostowanymi](xref:fundamentals/host/hosted-services) dla zadań w tle.</span><span class="sxs-lookup"><span data-stu-id="df645-131">Web App Service Sample: A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="df645-132">Wskazówki dotyczące MVC znajdują się w artykułach <xref:mvc/overview> i <xref:migration/22-to-30> .</span><span class="sxs-lookup"><span data-stu-id="df645-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="df645-133">Typ wdrożenia</span><span class="sxs-lookup"><span data-stu-id="df645-133">Deployment type</span></span>

<span data-ttu-id="df645-134">Aby uzyskać informacje i porady dotyczące scenariuszy wdrażania, zobacz [wdrażanie aplikacji .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="df645-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="df645-135">SDK</span><span class="sxs-lookup"><span data-stu-id="df645-135">SDK</span></span>

<span data-ttu-id="df645-136">W przypadku usługi opartej na aplikacji sieci Web korzystającej ze Razor stron lub platform MVC należy określić zestaw SDK sieci Web w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="df645-136">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="df645-137">Jeśli usługa wykonuje tylko zadania w tle (na przykład [usługi hostowane](xref:fundamentals/host/hosted-services)), określ zestaw SDK procesu roboczego w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="df645-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="df645-138">Wdrożenie zależne od platformy (FDD)</span><span class="sxs-lookup"><span data-stu-id="df645-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="df645-139">Wdrożenie zależne od platformy (FDD) zależy od obecności udostępnionej systemowej wersji platformy .NET Core w systemie docelowym.</span><span class="sxs-lookup"><span data-stu-id="df645-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="df645-140">Po przyjęciu scenariusza FDD zgodnie ze wskazówkami zawartymi w tym artykule zestaw SDK tworzy plik wykonywalny (*. exe*), nazywany *plik wykonywalny zależny od platformy*.</span><span class="sxs-lookup"><span data-stu-id="df645-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="df645-141">Jeśli używasz [zestawu Web SDK](#sdk), plik *web.config* , który jest zwykle tworzony podczas publikowania aplikacji ASP.NET Core, nie jest konieczny dla aplikacji usług systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="df645-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="df645-142">Aby wyłączyć tworzenie pliku *web.config* , należy dodać `<IsTransformWebConfigDisabled>` Właściwość ustawioną na `true` .</span><span class="sxs-lookup"><span data-stu-id="df645-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="df645-143">Wdrażanie samodzielne (SCD)</span><span class="sxs-lookup"><span data-stu-id="df645-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="df645-144">Wdrożenie samodzielne (SCD) nie polega na obecności struktury udostępnionej w systemie hosta.</span><span class="sxs-lookup"><span data-stu-id="df645-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="df645-145">Środowisko uruchomieniowe i zależności aplikacji są wdrażane przy użyciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="df645-146">[Identyfikator środowiska uruchomieniowego systemu Windows (RID)](/dotnet/core/rid-catalog) znajduje się w, `<PropertyGroup>` który zawiera platformę docelową:</span><span class="sxs-lookup"><span data-stu-id="df645-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="df645-147">Aby opublikować dla wielu identyfikatorów RID:</span><span class="sxs-lookup"><span data-stu-id="df645-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="df645-148">Podaj identyfikatory RID na liście rozdzielanej średnikami.</span><span class="sxs-lookup"><span data-stu-id="df645-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="df645-149">Użyj nazwy właściwości [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="df645-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="df645-150">Aby uzyskać więcej informacji, zobacz [katalog .NET Core RID Catalog](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="df645-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="df645-151">Konto użytkownika usługi</span><span class="sxs-lookup"><span data-stu-id="df645-151">Service user account</span></span>

<span data-ttu-id="df645-152">Aby utworzyć konto użytkownika dla usługi, należy użyć polecenia cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) w powłoce poleceń administracyjnych programu PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="df645-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="df645-153">W systemie Windows 10 października 2018 Update (wersja 1809/Kompilacja 10.0.17763) lub nowsza:</span><span class="sxs-lookup"><span data-stu-id="df645-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="df645-154">W systemie operacyjnym Windows w wersji starszej niż aktualizacja 2018 systemu Windows 10 października (wersja 1809/Kompilacja 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="df645-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="df645-155">Podaj [silne hasło](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) po wyświetleniu monitu.</span><span class="sxs-lookup"><span data-stu-id="df645-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="df645-156">Jeśli `-AccountExpires` parametr nie zostanie dostarczony do polecenia cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z wygaśnięciem <xref:System.DateTime> , konto nie wygaśnie.</span><span class="sxs-lookup"><span data-stu-id="df645-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="df645-157">Aby uzyskać więcej informacji, zobacz [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) i [konta użytkowników usług](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="df645-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="df645-158">Alternatywna metoda zarządzania użytkownikami podczas korzystania z Active Directory polega na użyciu zarządzanych kont usług.</span><span class="sxs-lookup"><span data-stu-id="df645-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="df645-159">Aby uzyskać więcej informacji, zobacz [Omówienie kont usług zarządzanych przez grupę](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="df645-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="df645-160">Logowanie jako prawa usługi</span><span class="sxs-lookup"><span data-stu-id="df645-160">Log on as a service rights</span></span>

<span data-ttu-id="df645-161">Aby nawiązać *Logowanie jako prawa usługi* dla konta użytkownika usługi:</span><span class="sxs-lookup"><span data-stu-id="df645-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="df645-162">Otwórz Edytor lokalnych zasad zabezpieczeń, uruchamiając program *secpol. msc*.</span><span class="sxs-lookup"><span data-stu-id="df645-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="df645-163">Rozwiń węzeł **Zasady lokalne** , a następnie wybierz pozycję **Przypisywanie praw użytkownika**.</span><span class="sxs-lookup"><span data-stu-id="df645-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="df645-164">Otwórz okno **Logowanie jako usługa** .</span><span class="sxs-lookup"><span data-stu-id="df645-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="df645-165">Wybierz pozycję **Dodaj użytkownika lub grupę**.</span><span class="sxs-lookup"><span data-stu-id="df645-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="df645-166">Podaj nazwę obiektu (konto użytkownika) przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="df645-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="df645-167">Wpisz konto użytkownika ( `{DOMAIN OR COMPUTER NAME\USER}` ) w polu Nazwa obiektu, a następnie wybierz **przycisk OK** , aby dodać użytkownika do zasad.</span><span class="sxs-lookup"><span data-stu-id="df645-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="df645-168">Wybierz pozycję **Zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="df645-168">Select **Advanced**.</span></span> <span data-ttu-id="df645-169">Wybierz pozycję **Znajdź teraz**.</span><span class="sxs-lookup"><span data-stu-id="df645-169">Select **Find Now**.</span></span> <span data-ttu-id="df645-170">Wybierz z listy konto użytkownika.</span><span class="sxs-lookup"><span data-stu-id="df645-170">Select the user account from the list.</span></span> <span data-ttu-id="df645-171">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="df645-171">Select **OK**.</span></span> <span data-ttu-id="df645-172">Ponownie wybierz **przycisk OK** , aby dodać użytkownika do zasad.</span><span class="sxs-lookup"><span data-stu-id="df645-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="df645-173">Wybierz **przycisk OK** lub **Zastosuj** , aby zaakceptować zmiany.</span><span class="sxs-lookup"><span data-stu-id="df645-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="df645-174">Tworzenie usługi systemu Windows i zarządzanie nią</span><span class="sxs-lookup"><span data-stu-id="df645-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="df645-175">Tworzenie usługi</span><span class="sxs-lookup"><span data-stu-id="df645-175">Create a service</span></span>

<span data-ttu-id="df645-176">Zarejestrowanie usługi za pomocą poleceń programu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="df645-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="df645-177">Z poziomu powłoki poleceń administracyjnych programu PowerShell 6 wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="df645-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = "{DOMAIN OR COMPUTER NAME\USER}", "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName "{EXE FILE PATH}" -Credential "{DOMAIN OR COMPUTER NAME\USER}" -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="df645-178">`{EXE PATH}`: Ścieżka do folderu aplikacji na hoście (na przykład `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="df645-178">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="df645-179">Nie dołączaj pliku wykonywalnego aplikacji do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="df645-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="df645-180">Końcowy ukośnik nie jest wymagany.</span><span class="sxs-lookup"><span data-stu-id="df645-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="df645-181">`{DOMAIN OR COMPUTER NAME\USER}`: Konto użytkownika usługi (na przykład `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="df645-181">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="df645-182">`{SERVICE NAME}`: Nazwa usługi (na przykład `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="df645-182">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="df645-183">`{EXE FILE PATH}`: Ścieżka pliku wykonywalnego aplikacji (na przykład `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="df645-183">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="df645-184">Uwzględnij nazwę pliku wykonywalnego z rozszerzeniem.</span><span class="sxs-lookup"><span data-stu-id="df645-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="df645-185">`{DESCRIPTION}`: Opis usługi (na przykład `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="df645-185">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="df645-186">`{DISPLAY NAME}`: Nazwa wyświetlana usługi (na przykład `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="df645-186">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="df645-187">Uruchamianie usługi</span><span class="sxs-lookup"><span data-stu-id="df645-187">Start a service</span></span>

<span data-ttu-id="df645-188">Uruchom usługę za pomocą następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="df645-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="df645-189">Uruchomienie usługi może potrwać kilka sekund.</span><span class="sxs-lookup"><span data-stu-id="df645-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="df645-190">Określanie stanu usługi</span><span class="sxs-lookup"><span data-stu-id="df645-190">Determine a service's status</span></span>

<span data-ttu-id="df645-191">Aby sprawdzić stan usługi, użyj następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="df645-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="df645-192">Stan jest raportowany jako jedna z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="df645-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="df645-193">Zatrzymywanie usługi</span><span class="sxs-lookup"><span data-stu-id="df645-193">Stop a service</span></span>

<span data-ttu-id="df645-194">Zatrzymaj usługę za pomocą następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="df645-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="df645-195">Usuwanie usługi</span><span class="sxs-lookup"><span data-stu-id="df645-195">Remove a service</span></span>

<span data-ttu-id="df645-196">Po krótkim opóźnieniu na zatrzymanie usługi Usuń usługę za pomocą następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="df645-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="df645-197">Scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="df645-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="df645-198">Usługi, które współdziałają z żądaniami z Internetu lub sieci firmowej i znajdują się za serwerem proxy lub modułem równoważenia obciążenia, mogą wymagać dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="df645-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="df645-199">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="df645-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="df645-200">Konfigurowanie punktów końcowych</span><span class="sxs-lookup"><span data-stu-id="df645-200">Configure endpoints</span></span>

<span data-ttu-id="df645-201">Domyślnie ASP.NET Core wiąże się z `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="df645-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="df645-202">Skonfiguruj adres URL i port przez ustawienie `ASPNETCORE_URLS` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="df645-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="df645-203">Aby uzyskać dodatkowe podejścia do konfiguracji adresów URL i portów, zobacz artykuł dotyczący odpowiedniego serwera:</span><span class="sxs-lookup"><span data-stu-id="df645-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* <xref:fundamentals/servers/kestrel/endpoints>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

* <xref:fundamentals/servers/kestrel#endpoint-configuration>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="df645-204">Powyższe wskazówki obejmują obsługę punktów końcowych HTTPS.</span><span class="sxs-lookup"><span data-stu-id="df645-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="df645-205">Na przykład skonfiguruj aplikację do obsługi protokołu HTTPS, gdy uwierzytelnianie jest używane z usługą systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="df645-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="df645-206">Korzystanie z ASP.NET Core certyfikatu deweloperskiego HTTPS w celu zabezpieczenia punktu końcowego usługi nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="df645-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="df645-207">Bieżący katalog i główna Zawartość</span><span class="sxs-lookup"><span data-stu-id="df645-207">Current directory and content root</span></span>

<span data-ttu-id="df645-208">Bieżącym katalogiem roboczym zwróconym przez wywołanie <xref:System.IO.Directory.GetCurrentDirectory%2A> dla usługi systemu Windows jest folder *C: \\ Windows \\ system32* .</span><span class="sxs-lookup"><span data-stu-id="df645-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory%2A> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="df645-209">Folder *system32* nie jest odpowiednią lokalizacją do przechowywania plików usługi (na przykład plików ustawień).</span><span class="sxs-lookup"><span data-stu-id="df645-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="df645-210">Użyj jednego z poniższych metod, aby zachować i uzyskać dostęp do plików ustawień i zasobów usługi.</span><span class="sxs-lookup"><span data-stu-id="df645-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="df645-211">Użyj ContentRootPath lub ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="df645-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="df645-212">Użyj [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) lub <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> do lokalizowania zasobów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="df645-213">Gdy aplikacja działa jako usługa, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService%2A> ustawia wartość <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> na [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="df645-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService%2A> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="df645-214">Domyślne pliki ustawień aplikacji *appsettings.json* i *appSettings. { Environment}. JSON* jest ładowany z katalogu głównego zawartości aplikacji przez wywołanie [CreateDefaultBuilder podczas konstruowania hosta](xref:fundamentals/host/generic-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="df645-214">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="df645-215">W przypadku innych plików ustawień ładowanych przez kod dewelopera w <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> , nie ma potrzeby wywoływania <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> .</span><span class="sxs-lookup"><span data-stu-id="df645-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A>.</span></span> <span data-ttu-id="df645-216">W poniższym przykładzie *custom_settings.jsw* pliku istnieje w katalogu głównym zawartości aplikacji i jest ładowany bez jawnego ustawiania ścieżki podstawowej:</span><span class="sxs-lookup"><span data-stu-id="df645-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="df645-217">Nie należy próbować użyć <xref:System.IO.Directory.GetCurrentDirectory%2A> programu w celu uzyskania ścieżki zasobu, ponieważ aplikacja usługi systemu Windows zwraca folder *C: \\ Windows \\ system32* jako bieżący katalog.</span><span class="sxs-lookup"><span data-stu-id="df645-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory%2A> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="df645-218">Przechowywanie plików usługi w odpowiedniej lokalizacji na dysku</span><span class="sxs-lookup"><span data-stu-id="df645-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="df645-219">Określ ścieżkę bezwzględną przy <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> użyciu <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do folderu zawierającego pliki.</span><span class="sxs-lookup"><span data-stu-id="df645-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="df645-220">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="df645-220">Troubleshoot</span></span>

<span data-ttu-id="df645-221">Aby rozwiązać problem z aplikacją usługi systemu Windows, zobacz <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="df645-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="df645-222">Typowe błędy</span><span class="sxs-lookup"><span data-stu-id="df645-222">Common errors</span></span>

* <span data-ttu-id="df645-223">Starsza lub wstępnie wydana wersja programu PowerShell jest używana.</span><span class="sxs-lookup"><span data-stu-id="df645-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="df645-224">Zarejestrowana usługa nie używa **opublikowanych** danych wyjściowych aplikacji z [dotnet Publish](/dotnet/core/tools/dotnet-publish) polecenia.</span><span class="sxs-lookup"><span data-stu-id="df645-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="df645-225">Dane wyjściowe polecenia [kompilacji dotnet](/dotnet/core/tools/dotnet-build) nie są obsługiwane w przypadku wdrażania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="df645-226">Opublikowane zasoby znajdują się w dowolnym z następujących folderów w zależności od typu wdrożenia:</span><span class="sxs-lookup"><span data-stu-id="df645-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="df645-227">*bin/Release/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="df645-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="df645-228">*bin/Release/{Target Framework}/{Runtime identyfikator}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="df645-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="df645-229">Usługa nie jest w stanie uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="df645-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="df645-230">Ścieżki do zasobów używanych przez aplikację (na przykład certyfikaty) są nieprawidłowe.</span><span class="sxs-lookup"><span data-stu-id="df645-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="df645-231">Ścieżką podstawową usługi systemu Windows jest *c: \\ Windows \\ system32*.</span><span class="sxs-lookup"><span data-stu-id="df645-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="df645-232">Użytkownik nie ma uprawnień do *logowania się jako usługa* .</span><span class="sxs-lookup"><span data-stu-id="df645-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="df645-233">Hasło użytkownika wygasło lub zostało nieprawidłowo przesłane podczas wykonywania `New-Service` polecenia programu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="df645-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="df645-234">Aplikacja wymaga uwierzytelniania ASP.NET Core, ale nie jest skonfigurowana dla połączeń Secure (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="df645-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="df645-235">Port adresu URL żądania jest niepoprawny lub niepoprawnie skonfigurowany w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="df645-236">Dzienniki zdarzeń systemu i aplikacji</span><span class="sxs-lookup"><span data-stu-id="df645-236">System and Application Event Logs</span></span>

<span data-ttu-id="df645-237">Uzyskaj dostęp do dzienników zdarzeń systemu i aplikacji:</span><span class="sxs-lookup"><span data-stu-id="df645-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="df645-238">Otwórz menu Start, wyszukaj ciąg *Podgląd zdarzeń* i wybierz aplikację **Podgląd zdarzeń** .</span><span class="sxs-lookup"><span data-stu-id="df645-238">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="df645-239">W **Podgląd zdarzeń** Otwórz węzeł **Dzienniki systemu Windows** .</span><span class="sxs-lookup"><span data-stu-id="df645-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="df645-240">Wybierz pozycję **system** , aby otworzyć dziennik zdarzeń systemu.</span><span class="sxs-lookup"><span data-stu-id="df645-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="df645-241">Wybierz pozycję **aplikacja** , aby otworzyć dziennik zdarzeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="df645-242">Wyszukaj błędy związane z niepowodzeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="df645-243">Uruchamianie aplikacji w wierszu polecenia</span><span class="sxs-lookup"><span data-stu-id="df645-243">Run the app at a command prompt</span></span>

<span data-ttu-id="df645-244">Wiele błędów uruchamiania nie produkuje użytecznych informacji w dziennikach zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="df645-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="df645-245">Przyczynę niektórych błędów można znaleźć, uruchamiając aplikację w wierszu polecenia w systemie hostingu.</span><span class="sxs-lookup"><span data-stu-id="df645-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="df645-246">Aby rejestrować dodatkowe szczegóły aplikacji, Obniż [poziom rejestrowania](xref:fundamentals/logging/index#log-level) lub Uruchom aplikację w [środowisku deweloperskim](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="df645-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="df645-247">Wyczyść pamięć podręczną pakietów</span><span class="sxs-lookup"><span data-stu-id="df645-247">Clear package caches</span></span>

<span data-ttu-id="df645-248">Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestaw .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w ramach aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="df645-249">W niektórych przypadkach niespójne pakiety mogą przerwać aplikację podczas przeprowadzania uaktualnień głównych.</span><span class="sxs-lookup"><span data-stu-id="df645-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="df645-250">Większość tych problemów można naprawić, wykonując następujące instrukcje:</span><span class="sxs-lookup"><span data-stu-id="df645-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="df645-251">Usuń foldery *bin* i *obj* .</span><span class="sxs-lookup"><span data-stu-id="df645-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="df645-252">Wyczyść pamięć podręczną pakietów, wykonując [wszystkie elementy lokalne usługi NuGet programu dotnet--Wyczyść](/dotnet/core/tools/dotnet-nuget-locals) z poziomu powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="df645-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="df645-253">Czyszczenie pamięci podręcznych pakietów można także wykonać przy użyciu narzędzia [nuget.exe](https://www.nuget.org/downloads) i wykonując polecenie `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="df645-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="df645-254">*nuget.exe* nie jest instalacją w pakiecie z systemem operacyjnym Windows dla komputerów stacjonarnych i należy ją pobrać niezależnie od [witryny sieci Web programu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="df645-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="df645-255">Przywróć i skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="df645-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="df645-256">Usuń wszystkie pliki z folderu wdrożenia na serwerze przed ponownym wdrożeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="df645-257">Aplikacja wolna lub wysunięta</span><span class="sxs-lookup"><span data-stu-id="df645-257">Slow or hanging app</span></span>

<span data-ttu-id="df645-258">*Zrzut awaryjny* to migawka pamięci systemu, która może pomóc w ustaleniu przyczyny awarii aplikacji, awarii uruchamiania lub powolnej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="df645-259">Awaria aplikacji lub napotka wyjątek</span><span class="sxs-lookup"><span data-stu-id="df645-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="df645-260">Uzyskaj i Analizuj Zrzut z [raportowanie błędów systemu Windows (raportowanie błędów systemu Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="df645-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="df645-261">Utwórz folder do przechowywania plików zrzutu awaryjnego w `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="df645-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="df645-262">Uruchom [skrypt programu PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) w programie EnableDumps z nazwą pliku wykonywalnego aplikacji:</span><span class="sxs-lookup"><span data-stu-id="df645-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="df645-263">Uruchom aplikację w warunkach, które powodują awarię.</span><span class="sxs-lookup"><span data-stu-id="df645-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="df645-264">Po wystąpieniu awarii Uruchom [skrypt programu DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="df645-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="df645-265">Po awarii aplikacji i zakończeniu zbierania zrzutów aplikacja może zakończyć normalne działanie.</span><span class="sxs-lookup"><span data-stu-id="df645-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="df645-266">Skrypt programu PowerShell konfiguruje raportowanie błędów systemu Windows w celu zebrania do pięciu zrzutów na aplikację.</span><span class="sxs-lookup"><span data-stu-id="df645-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="df645-267">Zrzuty awaryjne mogą wymagać dużej ilości miejsca na dysku (do kilku gigabajtów).</span><span class="sxs-lookup"><span data-stu-id="df645-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="df645-268">Aplikacja zawiesza się, kończy się niepowodzeniem podczas uruchamiania lub działa normalnie</span><span class="sxs-lookup"><span data-stu-id="df645-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="df645-269">Gdy aplikacja *zawiesza* się (bez awarii), kończy się niepowodzeniem podczas uruchamiania lub działa normalnie, zobacz [pliki zrzutu w trybie użytkownika: wybór najlepszego narzędzia](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) do wygenerowania zrzutu.</span><span class="sxs-lookup"><span data-stu-id="df645-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="df645-270">Analizowanie zrzutu</span><span class="sxs-lookup"><span data-stu-id="df645-270">Analyze the dump</span></span>

<span data-ttu-id="df645-271">Zrzut można analizować przy użyciu kilku metod.</span><span class="sxs-lookup"><span data-stu-id="df645-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="df645-272">Aby uzyskać więcej informacji, zobacz [Analizowanie pliku zrzutu User-Mode](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="df645-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="df645-273">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="df645-273">Additional resources</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"
* <span data-ttu-id="df645-274">[Konfiguracja punktu końcowego Kestrel](xref:fundamentals/servers/kestrel/endpoints) (w tym Konfiguracja protokołu HTTPS i obsługa SNI)</span><span class="sxs-lookup"><span data-stu-id="df645-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints) (includes HTTPS configuration and SNI support)</span></span>
::: moniker-end
::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"
* <span data-ttu-id="df645-275">[Konfiguracja punktu końcowego Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (w tym Konfiguracja protokołu HTTPS i obsługa SNI)</span><span class="sxs-lookup"><span data-stu-id="df645-275">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="df645-276">Aplikacja ASP.NET Core może być hostowana w systemie Windows jako [Usługa systemu Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez korzystania z usług IIS.</span><span class="sxs-lookup"><span data-stu-id="df645-276">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="df645-277">Gdy usługa jest hostowana w systemie Windows, aplikacja jest uruchamiana automatycznie po ponownym uruchomieniu serwera.</span><span class="sxs-lookup"><span data-stu-id="df645-277">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="df645-278">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="df645-278">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="df645-279">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="df645-279">Prerequisites</span></span>

* [<span data-ttu-id="df645-280">ASP.NET Core zestaw SDK 2,1 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="df645-280">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="df645-281">Program PowerShell 6,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="df645-281">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="df645-282">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="df645-282">App configuration</span></span>

<span data-ttu-id="df645-283">Aplikacja wymaga odwołania do pakietów dla elementu [Microsoft. AspNetCore. hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) i [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="df645-283">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="df645-284">Aby przetestować i debugować w przypadku uruchamiania poza usługą, należy dodać kod, aby określić, czy aplikacja działa jako usługa, czy Aplikacja konsolowa.</span><span class="sxs-lookup"><span data-stu-id="df645-284">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="df645-285">Sprawdź, czy debuger jest dołączony lub czy `--console` jest obecny przełącznik.</span><span class="sxs-lookup"><span data-stu-id="df645-285">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="df645-286">Jeśli którykolwiek z warunków jest spełniony (aplikacja nie jest uruchomiona jako usługa), wywołaj metodę <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> .</span><span class="sxs-lookup"><span data-stu-id="df645-286">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="df645-287">Jeśli warunki są fałszywe (aplikacja jest uruchamiana jako usługa):</span><span class="sxs-lookup"><span data-stu-id="df645-287">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="df645-288">Wywołaj <xref:System.IO.Directory.SetCurrentDirectory*> i użyj ścieżki do opublikowanej lokalizacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-288">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="df645-289">Nie wywołuj <xref:System.IO.Directory.GetCurrentDirectory*> , aby uzyskać ścieżkę, ponieważ aplikacja usługi systemu Windows zwraca folder *C: \\ Windows \\ system32* , gdy <xref:System.IO.Directory.GetCurrentDirectory*> jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="df645-289">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="df645-290">Aby uzyskać więcej informacji, zapoznaj się z sekcją [Current Directory i root Content](#current-directory-and-content-root) .</span><span class="sxs-lookup"><span data-stu-id="df645-290">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="df645-291">Ten krok jest wykonywany przed skonfigurowaniem aplikacji w programie `CreateWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="df645-291">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="df645-292">Wywołaj <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> , aby uruchomić aplikację jako usługę.</span><span class="sxs-lookup"><span data-stu-id="df645-292">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="df645-293">Ponieważ [dostawca konfiguracji wiersza polecenia](xref:fundamentals/configuration/index#command-line-configuration-provider) wymaga par nazwa-wartość dla argumentów wiersza polecenia, `--console` przełącznik jest usuwany z argumentów przed <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> odebraniem argumentów.</span><span class="sxs-lookup"><span data-stu-id="df645-293">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="df645-294">Aby zapisać w dzienniku zdarzeń systemu Windows, Dodaj dostawcę EventLog do programu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> .</span><span class="sxs-lookup"><span data-stu-id="df645-294">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="df645-295">Ustaw poziom rejestrowania z `Logging:LogLevel:Default` kluczem w *appsettings.Production.js* pliku.</span><span class="sxs-lookup"><span data-stu-id="df645-295">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="df645-296">W poniższym przykładzie z przykładowej aplikacji `RunAsCustomService` jest wywoływana zamiast <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> w celu obsługi zdarzeń okresu istnienia w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-296">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="df645-297">Aby uzyskać więcej informacji, zobacz sekcję [Obsługa zdarzeń dotyczących uruchamiania i zatrzymywania](#handle-starting-and-stopping-events) .</span><span class="sxs-lookup"><span data-stu-id="df645-297">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="df645-298">Typ wdrożenia</span><span class="sxs-lookup"><span data-stu-id="df645-298">Deployment type</span></span>

<span data-ttu-id="df645-299">Aby uzyskać informacje i porady dotyczące scenariuszy wdrażania, zobacz [wdrażanie aplikacji .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="df645-299">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="df645-300">SDK</span><span class="sxs-lookup"><span data-stu-id="df645-300">SDK</span></span>

<span data-ttu-id="df645-301">W przypadku usługi opartej na aplikacji sieci Web korzystającej ze Razor stron lub platform MVC należy określić zestaw SDK sieci Web w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="df645-301">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="df645-302">Jeśli usługa wykonuje tylko zadania w tle (na przykład [usługi hostowane](xref:fundamentals/host/hosted-services)), określ zestaw SDK procesu roboczego w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="df645-302">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="df645-303">Wdrożenie zależne od platformy (FDD)</span><span class="sxs-lookup"><span data-stu-id="df645-303">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="df645-304">Wdrożenie zależne od platformy (FDD) zależy od obecności udostępnionej systemowej wersji platformy .NET Core w systemie docelowym.</span><span class="sxs-lookup"><span data-stu-id="df645-304">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="df645-305">Po przyjęciu scenariusza FDD zgodnie ze wskazówkami zawartymi w tym artykule zestaw SDK tworzy plik wykonywalny (*. exe*), nazywany *plik wykonywalny zależny od platformy*.</span><span class="sxs-lookup"><span data-stu-id="df645-305">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="df645-306">[Identyfikator środowiska uruchomieniowego systemu Windows (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) zawiera platformę docelową.</span><span class="sxs-lookup"><span data-stu-id="df645-306">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="df645-307">W poniższym przykładzie identyfikator RID jest ustawiony na `win7-x64` .</span><span class="sxs-lookup"><span data-stu-id="df645-307">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="df645-308">Właściwość `<SelfContained>` ma ustawioną wartość `false`.</span><span class="sxs-lookup"><span data-stu-id="df645-308">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="df645-309">Te właściwości instruują zestaw SDK, aby wygenerował plik wykonywalny (*exe*) dla systemu Windows i aplikację, która zależy od współużytkowanej platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="df645-309">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="df645-310">Plik *web.config* , który jest zwykle tworzony podczas publikowania aplikacji ASP.NET Core, nie jest konieczny dla aplikacji usług systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="df645-310">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="df645-311">Aby wyłączyć tworzenie pliku *web.config* , należy dodać `<IsTransformWebConfigDisabled>` Właściwość ustawioną na `true` .</span><span class="sxs-lookup"><span data-stu-id="df645-311">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="df645-312">Wdrażanie samodzielne (SCD)</span><span class="sxs-lookup"><span data-stu-id="df645-312">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="df645-313">Wdrożenie samodzielne (SCD) nie polega na obecności struktury udostępnionej w systemie hosta.</span><span class="sxs-lookup"><span data-stu-id="df645-313">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="df645-314">Środowisko uruchomieniowe i zależności aplikacji są wdrażane przy użyciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-314">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="df645-315">[Identyfikator środowiska uruchomieniowego systemu Windows (RID)](/dotnet/core/rid-catalog) znajduje się w, `<PropertyGroup>` który zawiera platformę docelową:</span><span class="sxs-lookup"><span data-stu-id="df645-315">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="df645-316">Aby opublikować dla wielu identyfikatorów RID:</span><span class="sxs-lookup"><span data-stu-id="df645-316">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="df645-317">Podaj identyfikatory RID na liście rozdzielanej średnikami.</span><span class="sxs-lookup"><span data-stu-id="df645-317">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="df645-318">Użyj nazwy właściwości [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="df645-318">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="df645-319">Aby uzyskać więcej informacji, zobacz [katalog .NET Core RID Catalog](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="df645-319">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="df645-320">`<SelfContained>`Właściwość jest ustawiona na `true` :</span><span class="sxs-lookup"><span data-stu-id="df645-320">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="df645-321">Konto użytkownika usługi</span><span class="sxs-lookup"><span data-stu-id="df645-321">Service user account</span></span>

<span data-ttu-id="df645-322">Aby utworzyć konto użytkownika dla usługi, należy użyć polecenia cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) w powłoce poleceń administracyjnych programu PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="df645-322">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="df645-323">W systemie Windows 10 października 2018 Update (wersja 1809/Kompilacja 10.0.17763) lub nowsza:</span><span class="sxs-lookup"><span data-stu-id="df645-323">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="df645-324">W systemie operacyjnym Windows w wersji starszej niż aktualizacja 2018 systemu Windows 10 października (wersja 1809/Kompilacja 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="df645-324">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="df645-325">Podaj [silne hasło](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) po wyświetleniu monitu.</span><span class="sxs-lookup"><span data-stu-id="df645-325">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="df645-326">Jeśli `-AccountExpires` parametr nie zostanie dostarczony do polecenia cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z wygaśnięciem <xref:System.DateTime> , konto nie wygaśnie.</span><span class="sxs-lookup"><span data-stu-id="df645-326">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="df645-327">Aby uzyskać więcej informacji, zobacz [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) i [konta użytkowników usług](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="df645-327">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="df645-328">Alternatywna metoda zarządzania użytkownikami podczas korzystania z Active Directory polega na użyciu zarządzanych kont usług.</span><span class="sxs-lookup"><span data-stu-id="df645-328">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="df645-329">Aby uzyskać więcej informacji, zobacz [Omówienie kont usług zarządzanych przez grupę](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="df645-329">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="df645-330">Logowanie jako prawa usługi</span><span class="sxs-lookup"><span data-stu-id="df645-330">Log on as a service rights</span></span>

<span data-ttu-id="df645-331">Aby nawiązać *Logowanie jako prawa usługi* dla konta użytkownika usługi:</span><span class="sxs-lookup"><span data-stu-id="df645-331">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="df645-332">Otwórz Edytor lokalnych zasad zabezpieczeń, uruchamiając program *secpol. msc*.</span><span class="sxs-lookup"><span data-stu-id="df645-332">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="df645-333">Rozwiń węzeł **Zasady lokalne** , a następnie wybierz pozycję **Przypisywanie praw użytkownika**.</span><span class="sxs-lookup"><span data-stu-id="df645-333">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="df645-334">Otwórz okno **Logowanie jako usługa** .</span><span class="sxs-lookup"><span data-stu-id="df645-334">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="df645-335">Wybierz pozycję **Dodaj użytkownika lub grupę**.</span><span class="sxs-lookup"><span data-stu-id="df645-335">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="df645-336">Podaj nazwę obiektu (konto użytkownika) przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="df645-336">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="df645-337">Wpisz konto użytkownika ( `{DOMAIN OR COMPUTER NAME\USER}` ) w polu Nazwa obiektu, a następnie wybierz **przycisk OK** , aby dodać użytkownika do zasad.</span><span class="sxs-lookup"><span data-stu-id="df645-337">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="df645-338">Wybierz pozycję **Zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="df645-338">Select **Advanced**.</span></span> <span data-ttu-id="df645-339">Wybierz pozycję **Znajdź teraz**.</span><span class="sxs-lookup"><span data-stu-id="df645-339">Select **Find Now**.</span></span> <span data-ttu-id="df645-340">Wybierz z listy konto użytkownika.</span><span class="sxs-lookup"><span data-stu-id="df645-340">Select the user account from the list.</span></span> <span data-ttu-id="df645-341">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="df645-341">Select **OK**.</span></span> <span data-ttu-id="df645-342">Ponownie wybierz **przycisk OK** , aby dodać użytkownika do zasad.</span><span class="sxs-lookup"><span data-stu-id="df645-342">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="df645-343">Wybierz **przycisk OK** lub **Zastosuj** , aby zaakceptować zmiany.</span><span class="sxs-lookup"><span data-stu-id="df645-343">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="df645-344">Tworzenie usługi systemu Windows i zarządzanie nią</span><span class="sxs-lookup"><span data-stu-id="df645-344">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="df645-345">Tworzenie usługi</span><span class="sxs-lookup"><span data-stu-id="df645-345">Create a service</span></span>

<span data-ttu-id="df645-346">Zarejestrowanie usługi za pomocą poleceń programu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="df645-346">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="df645-347">Z poziomu powłoki poleceń administracyjnych programu PowerShell 6 wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="df645-347">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="df645-348">`{EXE PATH}`: Ścieżka do folderu aplikacji na hoście (na przykład `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="df645-348">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="df645-349">Nie dołączaj pliku wykonywalnego aplikacji do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="df645-349">Don't include the app's executable in the path.</span></span> <span data-ttu-id="df645-350">Końcowy ukośnik nie jest wymagany.</span><span class="sxs-lookup"><span data-stu-id="df645-350">A trailing slash isn't required.</span></span>
* <span data-ttu-id="df645-351">`{DOMAIN OR COMPUTER NAME\USER}`: Konto użytkownika usługi (na przykład `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="df645-351">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="df645-352">`{SERVICE NAME}`: Nazwa usługi (na przykład `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="df645-352">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="df645-353">`{EXE FILE PATH}`: Ścieżka pliku wykonywalnego aplikacji (na przykład `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="df645-353">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="df645-354">Uwzględnij nazwę pliku wykonywalnego z rozszerzeniem.</span><span class="sxs-lookup"><span data-stu-id="df645-354">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="df645-355">`{DESCRIPTION}`: Opis usługi (na przykład `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="df645-355">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="df645-356">`{DISPLAY NAME}`: Nazwa wyświetlana usługi (na przykład `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="df645-356">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="df645-357">Uruchamianie usługi</span><span class="sxs-lookup"><span data-stu-id="df645-357">Start a service</span></span>

<span data-ttu-id="df645-358">Uruchom usługę za pomocą następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="df645-358">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="df645-359">Uruchomienie usługi może potrwać kilka sekund.</span><span class="sxs-lookup"><span data-stu-id="df645-359">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="df645-360">Określanie stanu usługi</span><span class="sxs-lookup"><span data-stu-id="df645-360">Determine a service's status</span></span>

<span data-ttu-id="df645-361">Aby sprawdzić stan usługi, użyj następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="df645-361">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="df645-362">Stan jest raportowany jako jedna z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="df645-362">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="df645-363">Zatrzymywanie usługi</span><span class="sxs-lookup"><span data-stu-id="df645-363">Stop a service</span></span>

<span data-ttu-id="df645-364">Zatrzymaj usługę za pomocą następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="df645-364">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="df645-365">Usuwanie usługi</span><span class="sxs-lookup"><span data-stu-id="df645-365">Remove a service</span></span>

<span data-ttu-id="df645-366">Po krótkim opóźnieniu na zatrzymanie usługi Usuń usługę za pomocą następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="df645-366">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="df645-367">Obsługa zdarzeń uruchamiania i zatrzymywania</span><span class="sxs-lookup"><span data-stu-id="df645-367">Handle starting and stopping events</span></span>

<span data-ttu-id="df645-368">Aby obsłużyć <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*> <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> zdarzenia, i <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> :</span><span class="sxs-lookup"><span data-stu-id="df645-368">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="df645-369">Utwórz klasę, która dziedziczy z <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> `OnStarting` `OnStarted` metody,, i `OnStopping` :</span><span class="sxs-lookup"><span data-stu-id="df645-369">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="df645-370">Utwórz metodę rozszerzenia dla <xref:Microsoft.AspNetCore.Hosting.IWebHost> , która przekazuje `CustomWebHostService` do <xref:System.ServiceProcess.ServiceBase.Run*> :</span><span class="sxs-lookup"><span data-stu-id="df645-370">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="df645-371">W `Program.Main` , wywołaj `RunAsCustomService` metodę rozszerzenia zamiast <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :</span><span class="sxs-lookup"><span data-stu-id="df645-371">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="df645-372">Aby zobaczyć lokalizację programu <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> w programie `Program.Main` , zapoznaj się z przykładem kodu pokazanym w sekcji [typ wdrożenia](#deployment-type) .</span><span class="sxs-lookup"><span data-stu-id="df645-372">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="df645-373">Scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="df645-373">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="df645-374">Usługi, które współdziałają z żądaniami z Internetu lub sieci firmowej i znajdują się za serwerem proxy lub modułem równoważenia obciążenia, mogą wymagać dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="df645-374">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="df645-375">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="df645-375">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="df645-376">Konfigurowanie punktów końcowych</span><span class="sxs-lookup"><span data-stu-id="df645-376">Configure endpoints</span></span>

<span data-ttu-id="df645-377">Domyślnie ASP.NET Core wiąże się z `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="df645-377">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="df645-378">Skonfiguruj adres URL i port przez ustawienie `ASPNETCORE_URLS` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="df645-378">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="df645-379">Aby uzyskać dodatkowe podejścia do konfiguracji adresów URL i portów, zobacz artykuł dotyczący odpowiedniego serwera:</span><span class="sxs-lookup"><span data-stu-id="df645-379">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="df645-380">Powyższe wskazówki obejmują obsługę punktów końcowych HTTPS.</span><span class="sxs-lookup"><span data-stu-id="df645-380">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="df645-381">Na przykład skonfiguruj aplikację do obsługi protokołu HTTPS, gdy uwierzytelnianie jest używane z usługą systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="df645-381">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="df645-382">Korzystanie z ASP.NET Core certyfikatu deweloperskiego HTTPS w celu zabezpieczenia punktu końcowego usługi nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="df645-382">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="df645-383">Bieżący katalog i główna Zawartość</span><span class="sxs-lookup"><span data-stu-id="df645-383">Current directory and content root</span></span>

<span data-ttu-id="df645-384">Bieżącym katalogiem roboczym zwróconym przez wywołanie <xref:System.IO.Directory.GetCurrentDirectory*> dla usługi systemu Windows jest folder *C: \\ Windows \\ system32* .</span><span class="sxs-lookup"><span data-stu-id="df645-384">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="df645-385">Folder *system32* nie jest odpowiednią lokalizacją do przechowywania plików usługi (na przykład plików ustawień).</span><span class="sxs-lookup"><span data-stu-id="df645-385">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="df645-386">Użyj jednego z poniższych metod, aby zachować i uzyskać dostęp do plików ustawień i zasobów usługi.</span><span class="sxs-lookup"><span data-stu-id="df645-386">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="df645-387">Ustawianie ścieżki katalogu głównego zawartości do folderu aplikacji</span><span class="sxs-lookup"><span data-stu-id="df645-387">Set the content root path to the app's folder</span></span>

<span data-ttu-id="df645-388">Jest to ta <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> sama ścieżka do `binPath` argumentu podczas tworzenia usługi.</span><span class="sxs-lookup"><span data-stu-id="df645-388">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="df645-389">Zamiast wywoływania funkcji `GetCurrentDirectory` tworzenia ścieżek do plików ustawień, należy wywołać <xref:System.IO.Directory.SetCurrentDirectory*> ścieżkę do [katalogu głównego zawartości](xref:fundamentals/index#content-root)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-389">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="df645-390">W programie `Program.Main` Określ ścieżkę do folderu wykonywalnego usługi i użyj ścieżki, aby określić katalog główny zawartości aplikacji:</span><span class="sxs-lookup"><span data-stu-id="df645-390">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="df645-391">Przechowywanie plików usługi w odpowiedniej lokalizacji na dysku</span><span class="sxs-lookup"><span data-stu-id="df645-391">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="df645-392">Określ ścieżkę bezwzględną przy <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> użyciu <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do folderu zawierającego pliki.</span><span class="sxs-lookup"><span data-stu-id="df645-392">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="df645-393">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="df645-393">Troubleshoot</span></span>

<span data-ttu-id="df645-394">Aby rozwiązać problem z aplikacją usługi systemu Windows, zobacz <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="df645-394">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="df645-395">Typowe błędy</span><span class="sxs-lookup"><span data-stu-id="df645-395">Common errors</span></span>

* <span data-ttu-id="df645-396">Starsza lub wstępnie wydana wersja programu PowerShell jest używana.</span><span class="sxs-lookup"><span data-stu-id="df645-396">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="df645-397">Zarejestrowana usługa nie używa **opublikowanych** danych wyjściowych aplikacji z [dotnet Publish](/dotnet/core/tools/dotnet-publish) polecenia.</span><span class="sxs-lookup"><span data-stu-id="df645-397">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="df645-398">Dane wyjściowe polecenia [kompilacji dotnet](/dotnet/core/tools/dotnet-build) nie są obsługiwane w przypadku wdrażania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-398">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="df645-399">Opublikowane zasoby znajdują się w dowolnym z następujących folderów w zależności od typu wdrożenia:</span><span class="sxs-lookup"><span data-stu-id="df645-399">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="df645-400">*bin/Release/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="df645-400">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="df645-401">*bin/Release/{Target Framework}/{Runtime identyfikator}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="df645-401">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="df645-402">Usługa nie jest w stanie uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="df645-402">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="df645-403">Ścieżki do zasobów używanych przez aplikację (na przykład certyfikaty) są nieprawidłowe.</span><span class="sxs-lookup"><span data-stu-id="df645-403">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="df645-404">Ścieżką podstawową usługi systemu Windows jest *c: \\ Windows \\ system32*.</span><span class="sxs-lookup"><span data-stu-id="df645-404">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="df645-405">Użytkownik nie ma uprawnień do *logowania się jako usługa* .</span><span class="sxs-lookup"><span data-stu-id="df645-405">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="df645-406">Hasło użytkownika wygasło lub zostało nieprawidłowo przesłane podczas wykonywania `New-Service` polecenia programu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="df645-406">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="df645-407">Aplikacja wymaga uwierzytelniania ASP.NET Core, ale nie jest skonfigurowana dla połączeń Secure (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="df645-407">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="df645-408">Port adresu URL żądania jest niepoprawny lub niepoprawnie skonfigurowany w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-408">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="df645-409">Dzienniki zdarzeń systemu i aplikacji</span><span class="sxs-lookup"><span data-stu-id="df645-409">System and Application Event Logs</span></span>

<span data-ttu-id="df645-410">Uzyskaj dostęp do dzienników zdarzeń systemu i aplikacji:</span><span class="sxs-lookup"><span data-stu-id="df645-410">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="df645-411">Otwórz menu Start, wyszukaj ciąg *Podgląd zdarzeń* i wybierz aplikację **Podgląd zdarzeń** .</span><span class="sxs-lookup"><span data-stu-id="df645-411">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="df645-412">W **Podgląd zdarzeń** Otwórz węzeł **Dzienniki systemu Windows** .</span><span class="sxs-lookup"><span data-stu-id="df645-412">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="df645-413">Wybierz pozycję **system** , aby otworzyć dziennik zdarzeń systemu.</span><span class="sxs-lookup"><span data-stu-id="df645-413">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="df645-414">Wybierz pozycję **aplikacja** , aby otworzyć dziennik zdarzeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-414">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="df645-415">Wyszukaj błędy związane z niepowodzeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-415">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="df645-416">Uruchamianie aplikacji w wierszu polecenia</span><span class="sxs-lookup"><span data-stu-id="df645-416">Run the app at a command prompt</span></span>

<span data-ttu-id="df645-417">Wiele błędów uruchamiania nie produkuje użytecznych informacji w dziennikach zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="df645-417">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="df645-418">Przyczynę niektórych błędów można znaleźć, uruchamiając aplikację w wierszu polecenia w systemie hostingu.</span><span class="sxs-lookup"><span data-stu-id="df645-418">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="df645-419">Aby rejestrować dodatkowe szczegóły aplikacji, Obniż [poziom rejestrowania](xref:fundamentals/logging/index#log-level) lub Uruchom aplikację w [środowisku deweloperskim](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="df645-419">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="df645-420">Wyczyść pamięć podręczną pakietów</span><span class="sxs-lookup"><span data-stu-id="df645-420">Clear package caches</span></span>

<span data-ttu-id="df645-421">Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestaw .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w ramach aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-421">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="df645-422">W niektórych przypadkach niespójne pakiety mogą przerwać aplikację podczas przeprowadzania uaktualnień głównych.</span><span class="sxs-lookup"><span data-stu-id="df645-422">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="df645-423">Większość tych problemów można naprawić, wykonując następujące instrukcje:</span><span class="sxs-lookup"><span data-stu-id="df645-423">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="df645-424">Usuń foldery *bin* i *obj* .</span><span class="sxs-lookup"><span data-stu-id="df645-424">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="df645-425">Wyczyść pamięć podręczną pakietów, wykonując [wszystkie elementy lokalne usługi NuGet programu dotnet--Wyczyść](/dotnet/core/tools/dotnet-nuget-locals) z poziomu powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="df645-425">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="df645-426">Czyszczenie pamięci podręcznych pakietów można także wykonać przy użyciu narzędzia [nuget.exe](https://www.nuget.org/downloads) i wykonując polecenie `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="df645-426">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="df645-427">*nuget.exe* nie jest instalacją w pakiecie z systemem operacyjnym Windows dla komputerów stacjonarnych i należy ją pobrać niezależnie od [witryny sieci Web programu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="df645-427">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="df645-428">Przywróć i skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="df645-428">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="df645-429">Usuń wszystkie pliki z folderu wdrożenia na serwerze przed ponownym wdrożeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-429">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="df645-430">Aplikacja wolna lub wysunięta</span><span class="sxs-lookup"><span data-stu-id="df645-430">Slow or hanging app</span></span>

<span data-ttu-id="df645-431">*Zrzut awaryjny* to migawka pamięci systemu, która może pomóc w ustaleniu przyczyny awarii aplikacji, awarii uruchamiania lub powolnej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-431">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="df645-432">Awaria aplikacji lub napotka wyjątek</span><span class="sxs-lookup"><span data-stu-id="df645-432">App crashes or encounters an exception</span></span>

<span data-ttu-id="df645-433">Uzyskaj i Analizuj Zrzut z [raportowanie błędów systemu Windows (raportowanie błędów systemu Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="df645-433">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="df645-434">Utwórz folder do przechowywania plików zrzutu awaryjnego w `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="df645-434">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="df645-435">Uruchom [skrypt programu PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) w programie EnableDumps z nazwą pliku wykonywalnego aplikacji:</span><span class="sxs-lookup"><span data-stu-id="df645-435">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="df645-436">Uruchom aplikację w warunkach, które powodują awarię.</span><span class="sxs-lookup"><span data-stu-id="df645-436">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="df645-437">Po wystąpieniu awarii Uruchom [skrypt programu DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="df645-437">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="df645-438">Po awarii aplikacji i zakończeniu zbierania zrzutów aplikacja może zakończyć normalne działanie.</span><span class="sxs-lookup"><span data-stu-id="df645-438">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="df645-439">Skrypt programu PowerShell konfiguruje raportowanie błędów systemu Windows w celu zebrania do pięciu zrzutów na aplikację.</span><span class="sxs-lookup"><span data-stu-id="df645-439">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="df645-440">Zrzuty awaryjne mogą wymagać dużej ilości miejsca na dysku (do kilku gigabajtów).</span><span class="sxs-lookup"><span data-stu-id="df645-440">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="df645-441">Aplikacja zawiesza się, kończy się niepowodzeniem podczas uruchamiania lub działa normalnie</span><span class="sxs-lookup"><span data-stu-id="df645-441">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="df645-442">Gdy aplikacja *zawiesza* się (bez awarii), kończy się niepowodzeniem podczas uruchamiania lub działa normalnie, zobacz [pliki zrzutu w trybie użytkownika: wybór najlepszego narzędzia](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) do wygenerowania zrzutu.</span><span class="sxs-lookup"><span data-stu-id="df645-442">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="df645-443">Analizowanie zrzutu</span><span class="sxs-lookup"><span data-stu-id="df645-443">Analyze the dump</span></span>

<span data-ttu-id="df645-444">Zrzut można analizować przy użyciu kilku metod.</span><span class="sxs-lookup"><span data-stu-id="df645-444">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="df645-445">Aby uzyskać więcej informacji, zobacz [Analizowanie pliku zrzutu User-Mode](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="df645-445">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="df645-446">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="df645-446">Additional resources</span></span>

* <span data-ttu-id="df645-447">[Konfiguracja punktu końcowego Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (w tym Konfiguracja protokołu HTTPS i obsługa SNI)</span><span class="sxs-lookup"><span data-stu-id="df645-447">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="df645-448">Aplikacja ASP.NET Core może być hostowana w systemie Windows jako [Usługa systemu Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez korzystania z usług IIS.</span><span class="sxs-lookup"><span data-stu-id="df645-448">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="df645-449">Gdy usługa jest hostowana w systemie Windows, aplikacja jest uruchamiana automatycznie po ponownym uruchomieniu serwera.</span><span class="sxs-lookup"><span data-stu-id="df645-449">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="df645-450">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="df645-450">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="df645-451">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="df645-451">Prerequisites</span></span>

* [<span data-ttu-id="df645-452">ASP.NET Core zestaw SDK 2,1 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="df645-452">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="df645-453">Program PowerShell 6,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="df645-453">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="df645-454">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="df645-454">App configuration</span></span>

<span data-ttu-id="df645-455">Aplikacja wymaga odwołania do pakietów dla elementu [Microsoft. AspNetCore. hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) i [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="df645-455">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="df645-456">Aby przetestować i debugować w przypadku uruchamiania poza usługą, należy dodać kod, aby określić, czy aplikacja działa jako usługa, czy Aplikacja konsolowa.</span><span class="sxs-lookup"><span data-stu-id="df645-456">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="df645-457">Sprawdź, czy debuger jest dołączony lub czy `--console` jest obecny przełącznik.</span><span class="sxs-lookup"><span data-stu-id="df645-457">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="df645-458">Jeśli którykolwiek z warunków jest spełniony (aplikacja nie jest uruchomiona jako usługa), wywołaj metodę <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> .</span><span class="sxs-lookup"><span data-stu-id="df645-458">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="df645-459">Jeśli warunki są fałszywe (aplikacja jest uruchamiana jako usługa):</span><span class="sxs-lookup"><span data-stu-id="df645-459">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="df645-460">Wywołaj <xref:System.IO.Directory.SetCurrentDirectory*> i użyj ścieżki do opublikowanej lokalizacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-460">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="df645-461">Nie wywołuj <xref:System.IO.Directory.GetCurrentDirectory*> , aby uzyskać ścieżkę, ponieważ aplikacja usługi systemu Windows zwraca folder *C: \\ Windows \\ system32* , gdy <xref:System.IO.Directory.GetCurrentDirectory*> jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="df645-461">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="df645-462">Aby uzyskać więcej informacji, zapoznaj się z sekcją [Current Directory i root Content](#current-directory-and-content-root) .</span><span class="sxs-lookup"><span data-stu-id="df645-462">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="df645-463">Ten krok jest wykonywany przed skonfigurowaniem aplikacji w programie `CreateWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="df645-463">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="df645-464">Wywołaj <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> , aby uruchomić aplikację jako usługę.</span><span class="sxs-lookup"><span data-stu-id="df645-464">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="df645-465">Ponieważ [dostawca konfiguracji wiersza polecenia](xref:fundamentals/configuration/index#command-line-configuration-provider) wymaga par nazwa-wartość dla argumentów wiersza polecenia, `--console` przełącznik jest usuwany z argumentów przed <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> odebraniem argumentów.</span><span class="sxs-lookup"><span data-stu-id="df645-465">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="df645-466">Aby zapisać w dzienniku zdarzeń systemu Windows, Dodaj dostawcę EventLog do programu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> .</span><span class="sxs-lookup"><span data-stu-id="df645-466">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="df645-467">Ustaw poziom rejestrowania z `Logging:LogLevel:Default` kluczem w *appsettings.Production.js* pliku.</span><span class="sxs-lookup"><span data-stu-id="df645-467">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="df645-468">W poniższym przykładzie z przykładowej aplikacji `RunAsCustomService` jest wywoływana zamiast <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> w celu obsługi zdarzeń okresu istnienia w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-468">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="df645-469">Aby uzyskać więcej informacji, zobacz sekcję [Obsługa zdarzeń dotyczących uruchamiania i zatrzymywania](#handle-starting-and-stopping-events) .</span><span class="sxs-lookup"><span data-stu-id="df645-469">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="df645-470">Typ wdrożenia</span><span class="sxs-lookup"><span data-stu-id="df645-470">Deployment type</span></span>

<span data-ttu-id="df645-471">Aby uzyskać informacje i porady dotyczące scenariuszy wdrażania, zobacz [wdrażanie aplikacji .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="df645-471">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="df645-472">SDK</span><span class="sxs-lookup"><span data-stu-id="df645-472">SDK</span></span>

<span data-ttu-id="df645-473">W przypadku usługi opartej na aplikacji sieci Web korzystającej ze Razor stron lub platform MVC należy określić zestaw SDK sieci Web w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="df645-473">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="df645-474">Jeśli usługa wykonuje tylko zadania w tle (na przykład [usługi hostowane](xref:fundamentals/host/hosted-services)), określ zestaw SDK procesu roboczego w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="df645-474">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="df645-475">Wdrożenie zależne od platformy (FDD)</span><span class="sxs-lookup"><span data-stu-id="df645-475">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="df645-476">Wdrożenie zależne od platformy (FDD) zależy od obecności udostępnionej systemowej wersji platformy .NET Core w systemie docelowym.</span><span class="sxs-lookup"><span data-stu-id="df645-476">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="df645-477">Po przyjęciu scenariusza FDD zgodnie ze wskazówkami zawartymi w tym artykule zestaw SDK tworzy plik wykonywalny (*. exe*), nazywany *plik wykonywalny zależny od platformy*.</span><span class="sxs-lookup"><span data-stu-id="df645-477">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="df645-478">[Identyfikator środowiska uruchomieniowego systemu Windows (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) zawiera platformę docelową.</span><span class="sxs-lookup"><span data-stu-id="df645-478">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="df645-479">W poniższym przykładzie identyfikator RID jest ustawiony na `win7-x64` .</span><span class="sxs-lookup"><span data-stu-id="df645-479">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="df645-480">Właściwość `<SelfContained>` ma ustawioną wartość `false`.</span><span class="sxs-lookup"><span data-stu-id="df645-480">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="df645-481">Te właściwości instruują zestaw SDK, aby wygenerował plik wykonywalny (*exe*) dla systemu Windows i aplikację, która zależy od współużytkowanej platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="df645-481">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="df645-482">Właściwość `<UseAppHost>` ma ustawioną wartość `true`.</span><span class="sxs-lookup"><span data-stu-id="df645-482">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="df645-483">Ta właściwość zapewnia usługę ze ścieżką aktywacji (plik wykonywalny, *exe*) dla FDD.</span><span class="sxs-lookup"><span data-stu-id="df645-483">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="df645-484">Plik *web.config* , który jest zwykle tworzony podczas publikowania aplikacji ASP.NET Core, nie jest konieczny dla aplikacji usług systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="df645-484">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="df645-485">Aby wyłączyć tworzenie pliku *web.config* , należy dodać `<IsTransformWebConfigDisabled>` Właściwość ustawioną na `true` .</span><span class="sxs-lookup"><span data-stu-id="df645-485">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="df645-486">Wdrażanie samodzielne (SCD)</span><span class="sxs-lookup"><span data-stu-id="df645-486">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="df645-487">Wdrożenie samodzielne (SCD) nie polega na obecności struktury udostępnionej w systemie hosta.</span><span class="sxs-lookup"><span data-stu-id="df645-487">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="df645-488">Środowisko uruchomieniowe i zależności aplikacji są wdrażane przy użyciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-488">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="df645-489">[Identyfikator środowiska uruchomieniowego systemu Windows (RID)](/dotnet/core/rid-catalog) znajduje się w, `<PropertyGroup>` który zawiera platformę docelową:</span><span class="sxs-lookup"><span data-stu-id="df645-489">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="df645-490">Aby opublikować dla wielu identyfikatorów RID:</span><span class="sxs-lookup"><span data-stu-id="df645-490">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="df645-491">Podaj identyfikatory RID na liście rozdzielanej średnikami.</span><span class="sxs-lookup"><span data-stu-id="df645-491">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="df645-492">Użyj nazwy właściwości [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="df645-492">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="df645-493">Aby uzyskać więcej informacji, zobacz [katalog .NET Core RID Catalog](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="df645-493">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="df645-494">`<SelfContained>`Właściwość jest ustawiona na `true` :</span><span class="sxs-lookup"><span data-stu-id="df645-494">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="df645-495">Konto użytkownika usługi</span><span class="sxs-lookup"><span data-stu-id="df645-495">Service user account</span></span>

<span data-ttu-id="df645-496">Aby utworzyć konto użytkownika dla usługi, należy użyć polecenia cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) w powłoce poleceń administracyjnych programu PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="df645-496">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="df645-497">W systemie Windows 10 października 2018 Update (wersja 1809/Kompilacja 10.0.17763) lub nowsza:</span><span class="sxs-lookup"><span data-stu-id="df645-497">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="df645-498">W systemie operacyjnym Windows w wersji starszej niż aktualizacja 2018 systemu Windows 10 października (wersja 1809/Kompilacja 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="df645-498">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="df645-499">Podaj [silne hasło](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) po wyświetleniu monitu.</span><span class="sxs-lookup"><span data-stu-id="df645-499">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="df645-500">Jeśli `-AccountExpires` parametr nie zostanie dostarczony do polecenia cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z wygaśnięciem <xref:System.DateTime> , konto nie wygaśnie.</span><span class="sxs-lookup"><span data-stu-id="df645-500">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="df645-501">Aby uzyskać więcej informacji, zobacz [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) i [konta użytkowników usług](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="df645-501">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="df645-502">Alternatywna metoda zarządzania użytkownikami podczas korzystania z Active Directory polega na użyciu zarządzanych kont usług.</span><span class="sxs-lookup"><span data-stu-id="df645-502">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="df645-503">Aby uzyskać więcej informacji, zobacz [Omówienie kont usług zarządzanych przez grupę](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="df645-503">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="df645-504">Logowanie jako prawa usługi</span><span class="sxs-lookup"><span data-stu-id="df645-504">Log on as a service rights</span></span>

<span data-ttu-id="df645-505">Aby nawiązać *Logowanie jako prawa usługi* dla konta użytkownika usługi:</span><span class="sxs-lookup"><span data-stu-id="df645-505">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="df645-506">Otwórz Edytor lokalnych zasad zabezpieczeń, uruchamiając program *secpol. msc*.</span><span class="sxs-lookup"><span data-stu-id="df645-506">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="df645-507">Rozwiń węzeł **Zasady lokalne** , a następnie wybierz pozycję **Przypisywanie praw użytkownika**.</span><span class="sxs-lookup"><span data-stu-id="df645-507">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="df645-508">Otwórz okno **Logowanie jako usługa** .</span><span class="sxs-lookup"><span data-stu-id="df645-508">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="df645-509">Wybierz pozycję **Dodaj użytkownika lub grupę**.</span><span class="sxs-lookup"><span data-stu-id="df645-509">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="df645-510">Podaj nazwę obiektu (konto użytkownika) przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="df645-510">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="df645-511">Wpisz konto użytkownika ( `{DOMAIN OR COMPUTER NAME\USER}` ) w polu Nazwa obiektu, a następnie wybierz **przycisk OK** , aby dodać użytkownika do zasad.</span><span class="sxs-lookup"><span data-stu-id="df645-511">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="df645-512">Wybierz pozycję **Zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="df645-512">Select **Advanced**.</span></span> <span data-ttu-id="df645-513">Wybierz pozycję **Znajdź teraz**.</span><span class="sxs-lookup"><span data-stu-id="df645-513">Select **Find Now**.</span></span> <span data-ttu-id="df645-514">Wybierz z listy konto użytkownika.</span><span class="sxs-lookup"><span data-stu-id="df645-514">Select the user account from the list.</span></span> <span data-ttu-id="df645-515">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="df645-515">Select **OK**.</span></span> <span data-ttu-id="df645-516">Ponownie wybierz **przycisk OK** , aby dodać użytkownika do zasad.</span><span class="sxs-lookup"><span data-stu-id="df645-516">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="df645-517">Wybierz **przycisk OK** lub **Zastosuj** , aby zaakceptować zmiany.</span><span class="sxs-lookup"><span data-stu-id="df645-517">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="df645-518">Tworzenie usługi systemu Windows i zarządzanie nią</span><span class="sxs-lookup"><span data-stu-id="df645-518">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="df645-519">Tworzenie usługi</span><span class="sxs-lookup"><span data-stu-id="df645-519">Create a service</span></span>

<span data-ttu-id="df645-520">Zarejestrowanie usługi za pomocą poleceń programu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="df645-520">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="df645-521">Z poziomu powłoki poleceń administracyjnych programu PowerShell 6 wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="df645-521">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="df645-522">`{EXE PATH}`: Ścieżka do folderu aplikacji na hoście (na przykład `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="df645-522">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="df645-523">Nie dołączaj pliku wykonywalnego aplikacji do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="df645-523">Don't include the app's executable in the path.</span></span> <span data-ttu-id="df645-524">Końcowy ukośnik nie jest wymagany.</span><span class="sxs-lookup"><span data-stu-id="df645-524">A trailing slash isn't required.</span></span>
* <span data-ttu-id="df645-525">`{DOMAIN OR COMPUTER NAME\USER}`: Konto użytkownika usługi (na przykład `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="df645-525">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="df645-526">`{SERVICE NAME}`: Nazwa usługi (na przykład `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="df645-526">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="df645-527">`{EXE FILE PATH}`: Ścieżka pliku wykonywalnego aplikacji (na przykład `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="df645-527">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="df645-528">Uwzględnij nazwę pliku wykonywalnego z rozszerzeniem.</span><span class="sxs-lookup"><span data-stu-id="df645-528">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="df645-529">`{DESCRIPTION}`: Opis usługi (na przykład `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="df645-529">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="df645-530">`{DISPLAY NAME}`: Nazwa wyświetlana usługi (na przykład `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="df645-530">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="df645-531">Uruchamianie usługi</span><span class="sxs-lookup"><span data-stu-id="df645-531">Start a service</span></span>

<span data-ttu-id="df645-532">Uruchom usługę za pomocą następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="df645-532">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="df645-533">Uruchomienie usługi może potrwać kilka sekund.</span><span class="sxs-lookup"><span data-stu-id="df645-533">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="df645-534">Określanie stanu usługi</span><span class="sxs-lookup"><span data-stu-id="df645-534">Determine a service's status</span></span>

<span data-ttu-id="df645-535">Aby sprawdzić stan usługi, użyj następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="df645-535">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="df645-536">Stan jest raportowany jako jedna z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="df645-536">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="df645-537">Zatrzymywanie usługi</span><span class="sxs-lookup"><span data-stu-id="df645-537">Stop a service</span></span>

<span data-ttu-id="df645-538">Zatrzymaj usługę za pomocą następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="df645-538">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="df645-539">Usuwanie usługi</span><span class="sxs-lookup"><span data-stu-id="df645-539">Remove a service</span></span>

<span data-ttu-id="df645-540">Po krótkim opóźnieniu na zatrzymanie usługi Usuń usługę za pomocą następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="df645-540">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="df645-541">Obsługa zdarzeń uruchamiania i zatrzymywania</span><span class="sxs-lookup"><span data-stu-id="df645-541">Handle starting and stopping events</span></span>

<span data-ttu-id="df645-542">Aby obsłużyć <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*> <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> zdarzenia, i <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> :</span><span class="sxs-lookup"><span data-stu-id="df645-542">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="df645-543">Utwórz klasę, która dziedziczy z <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> `OnStarting` `OnStarted` metody,, i `OnStopping` :</span><span class="sxs-lookup"><span data-stu-id="df645-543">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="df645-544">Utwórz metodę rozszerzenia dla <xref:Microsoft.AspNetCore.Hosting.IWebHost> , która przekazuje `CustomWebHostService` do <xref:System.ServiceProcess.ServiceBase.Run*> :</span><span class="sxs-lookup"><span data-stu-id="df645-544">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="df645-545">W `Program.Main` , wywołaj `RunAsCustomService` metodę rozszerzenia zamiast <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :</span><span class="sxs-lookup"><span data-stu-id="df645-545">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="df645-546">Aby zobaczyć lokalizację programu <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> w programie `Program.Main` , zapoznaj się z przykładem kodu pokazanym w sekcji [typ wdrożenia](#deployment-type) .</span><span class="sxs-lookup"><span data-stu-id="df645-546">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="df645-547">Scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="df645-547">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="df645-548">Usługi, które współdziałają z żądaniami z Internetu lub sieci firmowej i znajdują się za serwerem proxy lub modułem równoważenia obciążenia, mogą wymagać dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="df645-548">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="df645-549">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="df645-549">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="df645-550">Konfigurowanie punktów końcowych</span><span class="sxs-lookup"><span data-stu-id="df645-550">Configure endpoints</span></span>

<span data-ttu-id="df645-551">Domyślnie ASP.NET Core wiąże się z `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="df645-551">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="df645-552">Skonfiguruj adres URL i port przez ustawienie `ASPNETCORE_URLS` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="df645-552">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="df645-553">Aby uzyskać dodatkowe podejścia do konfiguracji adresów URL i portów, zobacz artykuł dotyczący odpowiedniego serwera:</span><span class="sxs-lookup"><span data-stu-id="df645-553">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="df645-554">Powyższe wskazówki obejmują obsługę punktów końcowych HTTPS.</span><span class="sxs-lookup"><span data-stu-id="df645-554">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="df645-555">Na przykład skonfiguruj aplikację do obsługi protokołu HTTPS, gdy uwierzytelnianie jest używane z usługą systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="df645-555">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="df645-556">Korzystanie z ASP.NET Core certyfikatu deweloperskiego HTTPS w celu zabezpieczenia punktu końcowego usługi nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="df645-556">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="df645-557">Bieżący katalog i główna Zawartość</span><span class="sxs-lookup"><span data-stu-id="df645-557">Current directory and content root</span></span>

<span data-ttu-id="df645-558">Bieżącym katalogiem roboczym zwróconym przez wywołanie <xref:System.IO.Directory.GetCurrentDirectory*> dla usługi systemu Windows jest folder *C: \\ Windows \\ system32* .</span><span class="sxs-lookup"><span data-stu-id="df645-558">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="df645-559">Folder *system32* nie jest odpowiednią lokalizacją do przechowywania plików usługi (na przykład plików ustawień).</span><span class="sxs-lookup"><span data-stu-id="df645-559">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="df645-560">Użyj jednego z poniższych metod, aby zachować i uzyskać dostęp do plików ustawień i zasobów usługi.</span><span class="sxs-lookup"><span data-stu-id="df645-560">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="df645-561">Ustawianie ścieżki katalogu głównego zawartości do folderu aplikacji</span><span class="sxs-lookup"><span data-stu-id="df645-561">Set the content root path to the app's folder</span></span>

<span data-ttu-id="df645-562">Jest to ta <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> sama ścieżka do `binPath` argumentu podczas tworzenia usługi.</span><span class="sxs-lookup"><span data-stu-id="df645-562">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="df645-563">Zamiast wywoływania funkcji `GetCurrentDirectory` tworzenia ścieżek do plików ustawień, należy wywołać <xref:System.IO.Directory.SetCurrentDirectory*> ścieżkę do [katalogu głównego zawartości](xref:fundamentals/index#content-root)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-563">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="df645-564">W programie `Program.Main` Określ ścieżkę do folderu wykonywalnego usługi i użyj ścieżki, aby określić katalog główny zawartości aplikacji:</span><span class="sxs-lookup"><span data-stu-id="df645-564">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="df645-565">Przechowywanie plików usługi w odpowiedniej lokalizacji na dysku</span><span class="sxs-lookup"><span data-stu-id="df645-565">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="df645-566">Określ ścieżkę bezwzględną przy <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> użyciu <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do folderu zawierającego pliki.</span><span class="sxs-lookup"><span data-stu-id="df645-566">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="df645-567">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="df645-567">Troubleshoot</span></span>

<span data-ttu-id="df645-568">Aby rozwiązać problem z aplikacją usługi systemu Windows, zobacz <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="df645-568">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="df645-569">Typowe błędy</span><span class="sxs-lookup"><span data-stu-id="df645-569">Common errors</span></span>

* <span data-ttu-id="df645-570">Starsza lub wstępnie wydana wersja programu PowerShell jest używana.</span><span class="sxs-lookup"><span data-stu-id="df645-570">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="df645-571">Zarejestrowana usługa nie używa **opublikowanych** danych wyjściowych aplikacji z [dotnet Publish](/dotnet/core/tools/dotnet-publish) polecenia.</span><span class="sxs-lookup"><span data-stu-id="df645-571">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="df645-572">Dane wyjściowe polecenia [kompilacji dotnet](/dotnet/core/tools/dotnet-build) nie są obsługiwane w przypadku wdrażania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-572">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="df645-573">Opublikowane zasoby znajdują się w dowolnym z następujących folderów w zależności od typu wdrożenia:</span><span class="sxs-lookup"><span data-stu-id="df645-573">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="df645-574">*bin/Release/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="df645-574">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="df645-575">*bin/Release/{Target Framework}/{Runtime identyfikator}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="df645-575">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="df645-576">Usługa nie jest w stanie uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="df645-576">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="df645-577">Ścieżki do zasobów używanych przez aplikację (na przykład certyfikaty) są nieprawidłowe.</span><span class="sxs-lookup"><span data-stu-id="df645-577">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="df645-578">Ścieżką podstawową usługi systemu Windows jest *c: \\ Windows \\ system32*.</span><span class="sxs-lookup"><span data-stu-id="df645-578">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="df645-579">Użytkownik nie ma uprawnień do *logowania się jako usługa* .</span><span class="sxs-lookup"><span data-stu-id="df645-579">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="df645-580">Hasło użytkownika wygasło lub zostało nieprawidłowo przesłane podczas wykonywania `New-Service` polecenia programu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="df645-580">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="df645-581">Aplikacja wymaga uwierzytelniania ASP.NET Core, ale nie jest skonfigurowana dla połączeń Secure (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="df645-581">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="df645-582">Port adresu URL żądania jest niepoprawny lub niepoprawnie skonfigurowany w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-582">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="df645-583">Dzienniki zdarzeń systemu i aplikacji</span><span class="sxs-lookup"><span data-stu-id="df645-583">System and Application Event Logs</span></span>

<span data-ttu-id="df645-584">Uzyskaj dostęp do dzienników zdarzeń systemu i aplikacji:</span><span class="sxs-lookup"><span data-stu-id="df645-584">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="df645-585">Otwórz menu Start, wyszukaj ciąg *Podgląd zdarzeń* i wybierz aplikację **Podgląd zdarzeń** .</span><span class="sxs-lookup"><span data-stu-id="df645-585">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="df645-586">W **Podgląd zdarzeń** Otwórz węzeł **Dzienniki systemu Windows** .</span><span class="sxs-lookup"><span data-stu-id="df645-586">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="df645-587">Wybierz pozycję **system** , aby otworzyć dziennik zdarzeń systemu.</span><span class="sxs-lookup"><span data-stu-id="df645-587">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="df645-588">Wybierz pozycję **aplikacja** , aby otworzyć dziennik zdarzeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-588">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="df645-589">Wyszukaj błędy związane z niepowodzeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-589">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="df645-590">Uruchamianie aplikacji w wierszu polecenia</span><span class="sxs-lookup"><span data-stu-id="df645-590">Run the app at a command prompt</span></span>

<span data-ttu-id="df645-591">Wiele błędów uruchamiania nie produkuje użytecznych informacji w dziennikach zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="df645-591">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="df645-592">Przyczynę niektórych błędów można znaleźć, uruchamiając aplikację w wierszu polecenia w systemie hostingu.</span><span class="sxs-lookup"><span data-stu-id="df645-592">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="df645-593">Aby rejestrować dodatkowe szczegóły aplikacji, Obniż [poziom rejestrowania](xref:fundamentals/logging/index#log-level) lub Uruchom aplikację w [środowisku deweloperskim](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="df645-593">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="df645-594">Wyczyść pamięć podręczną pakietów</span><span class="sxs-lookup"><span data-stu-id="df645-594">Clear package caches</span></span>

<span data-ttu-id="df645-595">Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestaw .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w ramach aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-595">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="df645-596">W niektórych przypadkach niespójne pakiety mogą przerwać aplikację podczas przeprowadzania uaktualnień głównych.</span><span class="sxs-lookup"><span data-stu-id="df645-596">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="df645-597">Większość tych problemów można naprawić, wykonując następujące instrukcje:</span><span class="sxs-lookup"><span data-stu-id="df645-597">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="df645-598">Usuń foldery *bin* i *obj* .</span><span class="sxs-lookup"><span data-stu-id="df645-598">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="df645-599">Wyczyść pamięć podręczną pakietów, wykonując [wszystkie elementy lokalne usługi NuGet programu dotnet--Wyczyść](/dotnet/core/tools/dotnet-nuget-locals) z poziomu powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="df645-599">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="df645-600">Czyszczenie pamięci podręcznych pakietów można także wykonać przy użyciu narzędzia [nuget.exe](https://www.nuget.org/downloads) i wykonując polecenie `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="df645-600">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="df645-601">*nuget.exe* nie jest instalacją w pakiecie z systemem operacyjnym Windows dla komputerów stacjonarnych i należy ją pobrać niezależnie od [witryny sieci Web programu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="df645-601">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="df645-602">Przywróć i skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="df645-602">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="df645-603">Usuń wszystkie pliki z folderu wdrożenia na serwerze przed ponownym wdrożeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-603">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="df645-604">Aplikacja wolna lub wysunięta</span><span class="sxs-lookup"><span data-stu-id="df645-604">Slow or hanging app</span></span>

<span data-ttu-id="df645-605">*Zrzut awaryjny* to migawka pamięci systemu, która może pomóc w ustaleniu przyczyny awarii aplikacji, awarii uruchamiania lub powolnej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df645-605">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="df645-606">Awaria aplikacji lub napotka wyjątek</span><span class="sxs-lookup"><span data-stu-id="df645-606">App crashes or encounters an exception</span></span>

<span data-ttu-id="df645-607">Uzyskaj i Analizuj Zrzut z [raportowanie błędów systemu Windows (raportowanie błędów systemu Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="df645-607">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="df645-608">Utwórz folder do przechowywania plików zrzutu awaryjnego w `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="df645-608">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="df645-609">Uruchom [skrypt programu PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) w programie EnableDumps z nazwą pliku wykonywalnego aplikacji:</span><span class="sxs-lookup"><span data-stu-id="df645-609">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="df645-610">Uruchom aplikację w warunkach, które powodują awarię.</span><span class="sxs-lookup"><span data-stu-id="df645-610">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="df645-611">Po wystąpieniu awarii Uruchom [skrypt programu DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="df645-611">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="df645-612">Po awarii aplikacji i zakończeniu zbierania zrzutów aplikacja może zakończyć normalne działanie.</span><span class="sxs-lookup"><span data-stu-id="df645-612">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="df645-613">Skrypt programu PowerShell konfiguruje raportowanie błędów systemu Windows w celu zebrania do pięciu zrzutów na aplikację.</span><span class="sxs-lookup"><span data-stu-id="df645-613">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="df645-614">Zrzuty awaryjne mogą wymagać dużej ilości miejsca na dysku (do kilku gigabajtów).</span><span class="sxs-lookup"><span data-stu-id="df645-614">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="df645-615">Aplikacja zawiesza się, kończy się niepowodzeniem podczas uruchamiania lub działa normalnie</span><span class="sxs-lookup"><span data-stu-id="df645-615">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="df645-616">Gdy aplikacja *zawiesza* się (bez awarii), kończy się niepowodzeniem podczas uruchamiania lub działa normalnie, zobacz [pliki zrzutu w trybie użytkownika: wybór najlepszego narzędzia](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) do wygenerowania zrzutu.</span><span class="sxs-lookup"><span data-stu-id="df645-616">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="df645-617">Analizowanie zrzutu</span><span class="sxs-lookup"><span data-stu-id="df645-617">Analyze the dump</span></span>

<span data-ttu-id="df645-618">Zrzut można analizować przy użyciu kilku metod.</span><span class="sxs-lookup"><span data-stu-id="df645-618">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="df645-619">Aby uzyskać więcej informacji, zobacz [Analizowanie pliku zrzutu User-Mode](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="df645-619">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="df645-620">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="df645-620">Additional resources</span></span>

* <span data-ttu-id="df645-621">[Konfiguracja punktu końcowego Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (w tym Konfiguracja protokołu HTTPS i obsługa SNI)</span><span class="sxs-lookup"><span data-stu-id="df645-621">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
