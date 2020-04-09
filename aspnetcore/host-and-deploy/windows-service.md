---
title: Host ASP.NET Core w usłudze systemu Windows
author: rick-anderson
description: Dowiedz się, jak hostować aplikację ASP.NET Core w usłudze systemu Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/windows-service
ms.openlocfilehash: 5cb61d330df7e15fbd54396207792596ae018fd3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417579"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="7f4a4-103">Host ASP.NET Core w usłudze systemu Windows</span><span class="sxs-lookup"><span data-stu-id="7f4a4-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7f4a4-104">Aplikacja ASP.NET Core może być hostowana w systemie Windows jako [usługa systemu Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez korzystania z usług IIS.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="7f4a4-105">Po uruchomieniu jako usługa systemu Windows aplikacja jest uruchamiana automatycznie po ponownym uruchomieniu serwera.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="7f4a4-106">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7f4a4-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7f4a4-107">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="7f4a4-107">Prerequisites</span></span>

* [<span data-ttu-id="7f4a4-108">ASP.NET Core SDK 2.1 lub nowszym</span><span class="sxs-lookup"><span data-stu-id="7f4a4-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="7f4a4-109">Program PowerShell 6.2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="7f4a4-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="7f4a4-110">Szablon usługi pracownika</span><span class="sxs-lookup"><span data-stu-id="7f4a4-110">Worker Service template</span></span>

<span data-ttu-id="7f4a4-111">Szablon ASP.NET Core Worker Service stanowi punkt wyjścia do pisania długo działających aplikacji usługi.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="7f4a4-112">Aby użyć szablonu jako podstawy aplikacji usługi windows:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="7f4a4-113">Utwórz aplikację usługi procesu roboczego na podstawie szablonu .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="7f4a4-114">Postępuj zgodnie ze wskazówkami w sekcji [Konfiguracja aplikacji,](#app-configuration) aby zaktualizować aplikację usługi procesu roboczego, aby mogła działać jako usługa systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="7f4a4-115">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="7f4a4-115">App configuration</span></span>

<span data-ttu-id="7f4a4-116">Aplikacja wymaga odwołania do pakietu [dla microsoft.extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="7f4a4-117">`IHostBuilder.UseWindowsService`podczas budowania hosta.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="7f4a4-118">Jeśli aplikacja jest uruchomiona jako usługa systemu Windows, metoda:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="7f4a4-119">Ustawia okres istnienia `WindowsServiceLifetime`hosta na .</span><span class="sxs-lookup"><span data-stu-id="7f4a4-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="7f4a4-120">Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) na [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="7f4a4-121">Aby uzyskać więcej informacji, zobacz [sekcję Bieżący katalog i katalog zawartości.](#current-directory-and-content-root)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="7f4a4-122">Umożliwia rejestrowanie w dzienniku zdarzeń:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="7f4a4-123">Nazwa aplikacji jest używana jako domyślna nazwa źródła.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="7f4a4-124">Domyślny poziom dziennika to *Ostrzeżenie* lub wyższy dla aplikacji opartej `CreateDefaultBuilder` na szablonie ASP.NET Core, który wywołuje tworzenie hosta.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="7f4a4-125">Zastąp domyślny poziom `Logging:EventLog:LogLevel:Default` dziennika za pomocą klucza w *appsettings.json*/*appsettings.{ Środowisko}.json* lub inny dostawca konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="7f4a4-126">Tylko administratorzy mogą tworzyć nowe źródła zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="7f4a4-127">Jeśli nie można utworzyć źródła zdarzeń przy użyciu nazwy aplikacji, ostrzeżenie jest rejestrowane w źródle *aplikacji,* a dzienniki zdarzeń są wyłączone.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="7f4a4-128">W `CreateHostBuilder` *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="7f4a4-128">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="7f4a4-129">W tym temacie dołączone są następujące przykładowe aplikacje:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="7f4a4-130">Przykład &ndash; usługi procesu roboczego w tle Przykładowa aplikacja niesienia sieci web oparta na [szablonie Usługi procesu roboczego,](#worker-service-template) który używa [hostowanych usług](xref:fundamentals/host/hosted-services) do wykonywania zadań w tle.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-130">Background Worker Service Sample &ndash; A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="7f4a4-131">Przykładowa próbka &ndash; aplikacji sieciOwy Razor Pages, która działa jako usługa systemu Windows z [usługami hostowanymi](xref:fundamentals/host/hosted-services) dla zadań w tle.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-131">Web App Service Sample &ndash; A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="7f4a4-132">Wskazówki MVC można znaleźć <xref:mvc/overview> <xref:migration/22-to-30>w artykułach w obszarze i .</span><span class="sxs-lookup"><span data-stu-id="7f4a4-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="7f4a4-133">Typ wdrożenia</span><span class="sxs-lookup"><span data-stu-id="7f4a4-133">Deployment type</span></span>

<span data-ttu-id="7f4a4-134">Aby uzyskać informacje i porady dotyczące scenariuszy wdrażania, zobacz [wdrożenie aplikacji .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="7f4a4-135">SDK</span><span class="sxs-lookup"><span data-stu-id="7f4a4-135">SDK</span></span>

<span data-ttu-id="7f4a4-136">W przypadku usługi opartej na aplikacjach sieci web, która używa stron Razor Pages lub struktur MVC, określ składnik Web SDK w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-136">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="7f4a4-137">Jeśli usługa wykonuje tylko zadania w tle (na przykład [usługi hostowane),](xref:fundamentals/host/hosted-services)określ sdk roboczy w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="7f4a4-138">Wdrożenie zależne od struktury (FDD)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="7f4a4-139">Wdrożenie zależne od struktury (FDD) opiera się na obecności udostępnionej wersji systemu .NET Core w systemie docelowym.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="7f4a4-140">Gdy scenariusz FDD zostanie przyjęty zgodnie ze wskazówkami zawartymi w tym artykule, SDK tworzy plik wykonywalny (*.exe*), nazywany *wykonywalnym zależnym od struktury*.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="7f4a4-141">W przypadku korzystania z [pakietu Web SDK](#sdk)plik *web.config,* który jest zwykle produkowany podczas publikowania aplikacji ASP.NET Core, nie jest potrzebny dla aplikacji Usług systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="7f4a4-142">Aby wyłączyć tworzenie pliku *web.config,* `<IsTransformWebConfigDisabled>` dodaj właściwość ustawioną na `true`.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="7f4a4-143">Samodzielne wdrażanie (SCD)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="7f4a4-144">Samodzielne wdrożenie (SCD) nie opiera się na obecności udostępnionej struktury w systemie hosta.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="7f4a4-145">Środowisko wykonawcze i zależności aplikacji są wdrażane za pomocą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="7f4a4-146">Identyfikator środowiska wykonawczego systemu Windows [(RID)](/dotnet/core/rid-catalog) `<PropertyGroup>` znajduje się w platformie zawierającej platformę docelową:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="7f4a4-147">Aby opublikować wiele identyfikatorów RID:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="7f4a4-148">Podaj identyfikatory RID na liście rozdzielanych średnikami.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="7f4a4-149">Nazwa właściwości [ \<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (liczba mnoga).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="7f4a4-150">Aby uzyskać więcej informacji, zobacz [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="7f4a4-151">Konto użytkownika usługi</span><span class="sxs-lookup"><span data-stu-id="7f4a4-151">Service user account</span></span>

<span data-ttu-id="7f4a4-152">Aby utworzyć konto użytkownika dla usługi, użyj polecenia cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z administracyjnej powłoki poleceń programu PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="7f4a4-153">W systemie Windows 10 October 2018 Update (wersja 1809/kompilacja 10.0.17763) lub nowszej:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="7f4a4-154">W systemie operacyjnym Windows wcześniej niż Windows 10 October 2018 Update (wersja 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="7f4a4-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="7f4a4-155">Po wyświetleniu monitu podaj [silne hasło.](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="7f4a4-156">O `-AccountExpires` ile parametr nie zostanie dostarczony do polecenia cmdlet <xref:System.DateTime> [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z wygaśnięciem, konto nie wygaśnie.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="7f4a4-157">Aby uzyskać więcej informacji, zobacz [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) i [Konta użytkowników usług](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="7f4a4-158">Alternatywnym podejściem do zarządzania użytkownikami podczas korzystania z usługi Active Directory jest użycie kont usług zarządzanych.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="7f4a4-159">Aby uzyskać więcej informacji, zobacz [Omówienie grupowych kont usług zarządzanych](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="7f4a4-160">Zaloguj się jako prawa do usługi</span><span class="sxs-lookup"><span data-stu-id="7f4a4-160">Log on as a service rights</span></span>

<span data-ttu-id="7f4a4-161">Aby *ustanowić logowanie jako* prawa do usługi dla konta użytkownika usługi:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="7f4a4-162">Otwórz edytor lokalnych zasad zabezpieczeń, uruchamiając *plik secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="7f4a4-163">Rozwiń węzeł **Zasady lokalne** i wybierz pozycję **Przypisanie praw użytkownika**.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="7f4a4-164">Otwórz **zasadę Logowanie jako usługi.**</span><span class="sxs-lookup"><span data-stu-id="7f4a4-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="7f4a4-165">Wybierz **pozycję Dodaj użytkownika lub grupę**.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="7f4a4-166">Podaj nazwę obiektu (konto użytkownika) przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="7f4a4-167">Wpisz konto użytkownika`{DOMAIN OR COMPUTER NAME\USER}`( ) w polu nazwa obiektu i wybierz **przycisk OK,** aby dodać użytkownika do zasad.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="7f4a4-168">Wybierz **pozycję Zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-168">Select **Advanced**.</span></span> <span data-ttu-id="7f4a4-169">Wybierz **pozycję Znajdź teraz**.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-169">Select **Find Now**.</span></span> <span data-ttu-id="7f4a4-170">Wybierz konto użytkownika z listy.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-170">Select the user account from the list.</span></span> <span data-ttu-id="7f4a4-171">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-171">Select **OK**.</span></span> <span data-ttu-id="7f4a4-172">Wybierz **przycisk OK** ponownie, aby dodać użytkownika do zasad.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="7f4a4-173">Wybierz **przycisk OK** lub **Zastosuj,** aby zaakceptować zmiany.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="7f4a4-174">Tworzenie usługi systemu Windows i zarządzanie nią</span><span class="sxs-lookup"><span data-stu-id="7f4a4-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="7f4a4-175">Tworzenie usługi</span><span class="sxs-lookup"><span data-stu-id="7f4a4-175">Create a service</span></span>

<span data-ttu-id="7f4a4-176">Użyj poleceń programu PowerShell, aby zarejestrować usługę.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="7f4a4-177">Z administracyjnej powłoki poleceń programu PowerShell 6 wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="7f4a4-178">`{EXE PATH}`&ndash; Ścieżka do folderu aplikacji na hoście `d:\myservice`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-178">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="7f4a4-179">Nie dołączaj pliku wykonywalnego aplikacji do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="7f4a4-180">Ukośnik na końcu nie jest wymagany.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="7f4a4-181">`{DOMAIN OR COMPUTER NAME\USER}`&ndash; Konto użytkownika usługi (na przykład `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-181">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="7f4a4-182">`{SERVICE NAME}`&ndash; Nazwa usługi (na `MyService`przykład ).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-182">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="7f4a4-183">`{EXE FILE PATH}`&ndash; Ścieżka wykonywalna aplikacji `d:\myservice\myservice.exe`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-183">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="7f4a4-184">Dołącz nazwę pliku wykonywalnego z rozszerzeniem.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="7f4a4-185">`{DESCRIPTION}`&ndash; Opis usługi (na `My sample service`przykład ).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-185">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="7f4a4-186">`{DISPLAY NAME}`&ndash; Nazwa wyświetlana usługi `My Service`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-186">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="7f4a4-187">Uruchamianie usługi</span><span class="sxs-lookup"><span data-stu-id="7f4a4-187">Start a service</span></span>

<span data-ttu-id="7f4a4-188">Uruchom usługę za pomocą następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="7f4a4-189">Polecenie trwa kilka sekund, aby uruchomić usługę.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="7f4a4-190">Określanie stanu usługi</span><span class="sxs-lookup"><span data-stu-id="7f4a4-190">Determine a service's status</span></span>

<span data-ttu-id="7f4a4-191">Aby sprawdzić stan usługi, użyj następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="7f4a4-192">Stan jest zgłaszany jako jedna z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="7f4a4-193">Zatrzymywanie usługi</span><span class="sxs-lookup"><span data-stu-id="7f4a4-193">Stop a service</span></span>

<span data-ttu-id="7f4a4-194">Zatrzymaj usługę za pomocą następującego polecenia programu Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="7f4a4-195">Usuwanie usługi</span><span class="sxs-lookup"><span data-stu-id="7f4a4-195">Remove a service</span></span>

<span data-ttu-id="7f4a4-196">Po krótkim opóźnieniu zatrzymania usługi usuń usługę za pomocą następującego polecenia Programu Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="7f4a4-197">Scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="7f4a4-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="7f4a4-198">Usługi, które wchodzą w interakcję z żądaniami z Internetu lub sieci firmowej i znajdują się za serwerem proxy lub modułem równoważenia obciążenia, mogą wymagać dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="7f4a4-199">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="7f4a4-200">Konfigurowanie punktów końcowych</span><span class="sxs-lookup"><span data-stu-id="7f4a4-200">Configure endpoints</span></span>

<span data-ttu-id="7f4a4-201">Domyślnie ASP.NET Core wiąże się `http://localhost:5000`z programem .</span><span class="sxs-lookup"><span data-stu-id="7f4a4-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="7f4a4-202">Skonfiguruj adres URL `ASPNETCORE_URLS` i port, ustawiając zmienną środowiskową.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="7f4a4-203">Aby uzyskać dodatkowe podejścia do konfiguracji adresów URL i portów, zobacz odpowiedni artykuł na temat serwera:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="7f4a4-204">Powyższe wskazówki obejmują obsługę punktów końcowych HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="7f4a4-205">Na przykład skonfiguruj aplikację dla protokołu HTTPS, gdy uwierzytelnianie jest używane z usługą systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="7f4a4-206">Użycie ASP.NET certyfikatu dewelopera HTTPS w celu zabezpieczenia punktu końcowego usługi nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="7f4a4-207">Bieżący katalog i katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="7f4a4-207">Current directory and content root</span></span>

<span data-ttu-id="7f4a4-208">Bieżący katalog roboczy <xref:System.IO.Directory.GetCurrentDirectory*> zwracany przez wywołanie usługi systemu Windows to folder *C:\\WINDOWS\\system32.*</span><span class="sxs-lookup"><span data-stu-id="7f4a4-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="7f4a4-209">Folder *system32* nie jest odpowiednią lokalizacją do przechowywania plików usługi (na przykład plików ustawień).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="7f4a4-210">Użyj jednego z następujących podejść do obsługi i uzyskiwania dostępu do zasobów i plików ustawień usługi.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="7f4a4-211">Korzystanie z programu ContentRootPath lub ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="7f4a4-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="7f4a4-212">Użyj [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) lub <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> zlokalizować zasoby aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="7f4a4-213">Gdy aplikacja działa jako <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> usługa, <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> ustawia na [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="7f4a4-214">Domyślne pliki ustawień aplikacji, *appsettings.json* i *appsettings.{ Środowisko}.json*, są ładowane z katalogu głównego zawartości aplikacji, wywołując [CreateDefaultBuilder podczas budowy hosta](xref:fundamentals/host/generic-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-214">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="7f4a4-215">W przypadku innych plików ustawień <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>załadowanych przez kod <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>programisty w , nie ma potrzeby, aby zadzwonić .</span><span class="sxs-lookup"><span data-stu-id="7f4a4-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="7f4a4-216">W poniższym przykładzie plik *custom_settings.json* istnieje w katalogu głównym zawartości aplikacji i jest ładowany bez jawnego ustawiania ścieżki podstawowej:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="7f4a4-217">Nie próbuj używać <xref:System.IO.Directory.GetCurrentDirectory*> do uzyskania ścieżki zasobów, ponieważ aplikacja usługi systemu Windows zwraca folder *C:\\WINDOWS\\system32* jako bieżący katalog.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="7f4a4-218">Przechowywanie plików usługi w odpowiednim miejscu na dysku</span><span class="sxs-lookup"><span data-stu-id="7f4a4-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="7f4a4-219">Określ ścieżkę <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> bezwzględną <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> podczas korzystania z folderu zawierającego pliki.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="7f4a4-220">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="7f4a4-220">Troubleshoot</span></span>

<span data-ttu-id="7f4a4-221">Aby rozwiązać problem z <xref:test/troubleshoot>aplikacją Usługi systemu Windows, zobacz .</span><span class="sxs-lookup"><span data-stu-id="7f4a4-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="7f4a4-222">Typowe błędy</span><span class="sxs-lookup"><span data-stu-id="7f4a4-222">Common errors</span></span>

* <span data-ttu-id="7f4a4-223">Używana jest stara lub wersja wstępna programu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="7f4a4-224">Zarejestrowana usługa nie używa **opublikowanych** danych wyjściowych aplikacji z polecenia [publikowania dotnet.](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="7f4a4-225">Dane wyjściowe polecenia [kompilacji dotnet](/dotnet/core/tools/dotnet-build) nie jest obsługiwany dla wdrażania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="7f4a4-226">Opublikowane zasoby znajdują się w jednym z następujących folderów w zależności od typu wdrożenia:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="7f4a4-227">*bin/release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="7f4a4-228">*bin/release/{TARGET FRAMEWORK}/{IDENTYFIKATOR ŚRODOWISKA URUCHOMIENIOWEGO}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="7f4a4-229">Usługa nie jest w stanie RUNNING.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="7f4a4-230">Ścieżki do zasobów używanych przez aplikację (na przykład certyfikaty) są niepoprawne.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="7f4a4-231">Podstawową ścieżką usługi systemu Windows jest *c:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="7f4a4-232">Użytkownik nie ma *logowania jako* praw do usługi.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="7f4a4-233">Hasło użytkownika wygasło lub zostało niepoprawnie przekazane `New-Service` podczas wykonywania polecenia programu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="7f4a4-234">Aplikacja wymaga ASP.NET uwierzytelniania core, ale nie jest skonfigurowana do bezpiecznych połączeń (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="7f4a4-235">Port adresu URL żądania jest niepoprawny lub niepoprawnie skonfigurowany w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="7f4a4-236">Dzienniki zdarzeń systemu i aplikacji</span><span class="sxs-lookup"><span data-stu-id="7f4a4-236">System and Application Event Logs</span></span>

<span data-ttu-id="7f4a4-237">Dostęp do dzienników zdarzeń systemu i aplikacji:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="7f4a4-238">Otwórz menu Start, wyszukaj *podgląd zdarzeń*i wybierz aplikację **Podgląd zdarzeń.**</span><span class="sxs-lookup"><span data-stu-id="7f4a4-238">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="7f4a4-239">W **Podglądzie zdarzeń**otwórz węzeł **Dzienniki systemu Windows.**</span><span class="sxs-lookup"><span data-stu-id="7f4a4-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="7f4a4-240">Wybierz **system,** aby otworzyć dziennik zdarzeń systemu.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="7f4a4-241">Wybierz **opcję Aplikacja,** aby otworzyć dziennik zdarzeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="7f4a4-242">Wyszukaj błędy skojarzone z nieudaną aplikacją.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="7f4a4-243">Uruchamianie aplikacji w wierszu polecenia</span><span class="sxs-lookup"><span data-stu-id="7f4a4-243">Run the app at a command prompt</span></span>

<span data-ttu-id="7f4a4-244">Wiele błędów uruchamiania nie generuje przydatnych informacji w dziennikach zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="7f4a4-245">Przyczynę niektórych błędów można znaleźć, uruchamiając aplikację w wierszu polecenia w systemie hostingowym.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="7f4a4-246">Aby zarejestrować dodatkowe szczegóły z aplikacji, obniżyć [poziom dziennika](xref:fundamentals/logging/index#log-level) lub uruchomić aplikację w [środowisku deweloperskim](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="7f4a4-247">Wyczyść pamięć podręczną pakietów</span><span class="sxs-lookup"><span data-stu-id="7f4a4-247">Clear package caches</span></span>

<span data-ttu-id="7f4a4-248">Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestawu .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="7f4a4-249">W niektórych przypadkach niespójne pakiety mogą spowodować przerwanie aplikacji podczas wykonywania głównych uaktualnień.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="7f4a4-250">Większość z tych problemów można rozwiązać, postępując zgodnie z następującymi instrukcjami:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="7f4a4-251">Usuń *foldery bin* i *obj.*</span><span class="sxs-lookup"><span data-stu-id="7f4a4-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="7f4a4-252">Wyczyść bufory pakietu, wykonując [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) z powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="7f4a4-253">Czyszczenie pamięci podręcznych pakietów można również wykonać za pomocą narzędzia [nuget.exe](https://www.nuget.org/downloads) i wykonać polecenie `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="7f4a4-254">*nuget.exe* nie jest w pakiecie zainstalować z systemem operacyjnym Windows dla komputerów stacjonarnych i muszą być uzyskane oddzielnie od [strony internetowej NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="7f4a4-255">Przywracanie i odbudowywać projekt.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="7f4a4-256">Usuń wszystkie pliki w folderze wdrażania na serwerze przed ponowne wdrożeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="7f4a4-257">Powolna lub wisząca aplikacja</span><span class="sxs-lookup"><span data-stu-id="7f4a4-257">Slow or hanging app</span></span>

<span data-ttu-id="7f4a4-258">*Zrzut awaryjny* jest migawką pamięci systemu i może pomóc w określeniu przyczyny awarii aplikacji, awarii uruchamiania lub powolnej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="7f4a4-259">Aplikacja ulega awarii lub napotyka wyjątek</span><span class="sxs-lookup"><span data-stu-id="7f4a4-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="7f4a4-260">Uzyskiwanie i analizowanie zrzutu z [raportowania błędów systemu Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="7f4a4-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="7f4a4-261">Utwórz folder do przechowywania `c:\dumps`plików zrzutu awaryjnego w pliku .</span><span class="sxs-lookup"><span data-stu-id="7f4a4-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="7f4a4-262">Uruchom [skrypt EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) o nazwie wykonywalnej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="7f4a4-263">Uruchom aplikację w warunkach, które powodują awarię.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="7f4a4-264">Po wystąpieniu awarii uruchom [skrypt DisableDumps PowerShell:](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="7f4a4-265">Po awarii aplikacji i kolekcji zrzutu jest zakończona, aplikacja może zakończyć się normalnie.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="7f4a4-266">Skrypt programu PowerShell konfiguruje program WER do zbierania maksymalnie pięciu zrzutów na aplikację.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="7f4a4-267">Zrzuty awaryjne mogą zająć dużą ilość miejsca na dysku (do kilku gigabajtów).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="7f4a4-268">Aplikacja zawiesza się, kończy się niepowodzeniem podczas uruchamiania lub działa normalnie</span><span class="sxs-lookup"><span data-stu-id="7f4a4-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="7f4a4-269">Gdy aplikacja *zawiesza* się (przestaje odpowiadać, ale nie ulega awarii), kończy się niepowodzeniem podczas uruchamiania lub działa normalnie, zobacz [Pliki zrzutu w trybie użytkownika: Wybieranie najlepszego narzędzia,](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) aby wybrać odpowiednie narzędzie do produkcji zrzutu.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="7f4a4-270">Analizowanie zrzutu</span><span class="sxs-lookup"><span data-stu-id="7f4a4-270">Analyze the dump</span></span>

<span data-ttu-id="7f4a4-271">Zrzut można analizować przy użyciu kilku metod.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="7f4a4-272">Aby uzyskać więcej informacji, zobacz [Analizowanie pliku zrzutu w trybie użytkownika](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7f4a4-273">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="7f4a4-273">Additional resources</span></span>

* <span data-ttu-id="7f4a4-274">[Konfiguracja punktu końcowego pustułka](xref:fundamentals/servers/kestrel#endpoint-configuration) (obejmuje konfigurację HTTPS i obsługę SNI)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="7f4a4-275">Aplikacja ASP.NET Core może być hostowana w systemie Windows jako [usługa systemu Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez korzystania z usług IIS.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-275">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="7f4a4-276">Po uruchomieniu jako usługa systemu Windows aplikacja jest uruchamiana automatycznie po ponownym uruchomieniu serwera.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-276">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="7f4a4-277">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7f4a4-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7f4a4-278">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="7f4a4-278">Prerequisites</span></span>

* [<span data-ttu-id="7f4a4-279">ASP.NET Core SDK 2.1 lub nowszym</span><span class="sxs-lookup"><span data-stu-id="7f4a4-279">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="7f4a4-280">Program PowerShell 6.2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="7f4a4-280">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="7f4a4-281">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="7f4a4-281">App configuration</span></span>

<span data-ttu-id="7f4a4-282">Aplikacja wymaga odwołań do pakietów dla [witryny Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) i [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-282">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="7f4a4-283">Aby przetestować i debugować podczas uruchamiania poza usługą, dodaj kod, aby ustalić, czy aplikacja jest uruchomiona jako usługa lub aplikacja konsoli.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-283">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="7f4a4-284">Sprawdź, czy debuger jest `--console` dołączony lub przełącznik jest obecny.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-284">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="7f4a4-285">Jeśli którykolwiek z tych warunków jest spełniony (aplikacja <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>nie jest uruchamiana jako usługa), zadzwoń .</span><span class="sxs-lookup"><span data-stu-id="7f4a4-285">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="7f4a4-286">Jeśli warunki są fałszywe (aplikacja jest uruchamiana jako usługa):</span><span class="sxs-lookup"><span data-stu-id="7f4a4-286">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="7f4a4-287">Zadzwoń <xref:System.IO.Directory.SetCurrentDirectory*> i użyj ścieżki do opublikowanej lokalizacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-287">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="7f4a4-288">Nie wywołaj, <xref:System.IO.Directory.GetCurrentDirectory*> aby uzyskać ścieżkę, ponieważ aplikacja usługi systemu Windows <xref:System.IO.Directory.GetCurrentDirectory*> zwraca folder *C:\\WINDOWS\\system32,* gdy jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-288">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="7f4a4-289">Aby uzyskać więcej informacji, zobacz [sekcję Bieżący katalog i katalog zawartości.](#current-directory-and-content-root)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-289">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="7f4a4-290">Ten krok jest wykonywany przed skonfigurowaniem aplikacji w pliku `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-290">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="7f4a4-291">Wywołanie, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> aby uruchomić aplikację jako usługę.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-291">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="7f4a4-292">Ponieważ [dostawca konfiguracji wiersza polecenia](xref:fundamentals/configuration/index#command-line-configuration-provider) wymaga par nazwa-wartość dla `--console` argumentów wiersza <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> polecenia, przełącznik jest usuwany z argumentów przed odebraniem argumentów.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-292">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="7f4a4-293">Aby zapisać w dzienniku zdarzeń systemu Windows, dodaj dostawcę EventLog do <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-293">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="7f4a4-294">Ustaw poziom rejestrowania `Logging:LogLevel:Default` za pomocą klucza w *appsettings. Plik Production.json.*</span><span class="sxs-lookup"><span data-stu-id="7f4a4-294">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="7f4a4-295">W poniższym przykładzie z `RunAsCustomService` przykładowej aplikacji, jest wywoływana zamiast <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> w celu obsługi zdarzeń lifetime w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-295">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="7f4a4-296">Aby uzyskać więcej informacji, zobacz [Handle uruchamiania i zatrzymywania zdarzeń](#handle-starting-and-stopping-events) sekcji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-296">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="7f4a4-297">Typ wdrożenia</span><span class="sxs-lookup"><span data-stu-id="7f4a4-297">Deployment type</span></span>

<span data-ttu-id="7f4a4-298">Aby uzyskać informacje i porady dotyczące scenariuszy wdrażania, zobacz [wdrożenie aplikacji .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-298">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="7f4a4-299">SDK</span><span class="sxs-lookup"><span data-stu-id="7f4a4-299">SDK</span></span>

<span data-ttu-id="7f4a4-300">W przypadku usługi opartej na aplikacjach sieci web, która używa stron Razor Pages lub struktur MVC, określ składnik Web SDK w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-300">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="7f4a4-301">Jeśli usługa wykonuje tylko zadania w tle (na przykład [usługi hostowane),](xref:fundamentals/host/hosted-services)określ sdk roboczy w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-301">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="7f4a4-302">Wdrożenie zależne od struktury (FDD)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-302">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="7f4a4-303">Wdrożenie zależne od struktury (FDD) opiera się na obecności udostępnionej wersji systemu .NET Core w systemie docelowym.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-303">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="7f4a4-304">Gdy scenariusz FDD zostanie przyjęty zgodnie ze wskazówkami zawartymi w tym artykule, SDK tworzy plik wykonywalny (*.exe*), nazywany *wykonywalnym zależnym od struktury*.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-304">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="7f4a4-305">Identyfikator środowiska wykonawczego systemu Windows [(RID)](/dotnet/core/rid-catalog) [\<(RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) zawiera platformę docelową.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-305">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="7f4a4-306">W poniższym przykładzie identyfikator `win7-x64`RID jest ustawiony na .</span><span class="sxs-lookup"><span data-stu-id="7f4a4-306">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="7f4a4-307">Właściwość `<SelfContained>` jest `false`ustawiona na .</span><span class="sxs-lookup"><span data-stu-id="7f4a4-307">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="7f4a4-308">Te właściwości instruują SDK do generowania pliku wykonywalnego *(exe)* dla systemu Windows i aplikacji, która zależy od udostępnionej struktury .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-308">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="7f4a4-309">Plik *web.config,* który jest zwykle produkowany podczas publikowania aplikacji ASP.NET Core, nie jest potrzebny dla aplikacji Usług systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-309">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="7f4a4-310">Aby wyłączyć tworzenie pliku *web.config,* `<IsTransformWebConfigDisabled>` dodaj właściwość ustawioną na `true`.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-310">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="7f4a4-311">Samodzielne wdrażanie (SCD)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-311">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="7f4a4-312">Samodzielne wdrożenie (SCD) nie opiera się na obecności udostępnionej struktury w systemie hosta.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-312">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="7f4a4-313">Środowisko wykonawcze i zależności aplikacji są wdrażane za pomocą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-313">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="7f4a4-314">Identyfikator środowiska wykonawczego systemu Windows [(RID)](/dotnet/core/rid-catalog) `<PropertyGroup>` znajduje się w platformie zawierającej platformę docelową:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-314">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="7f4a4-315">Aby opublikować wiele identyfikatorów RID:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-315">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="7f4a4-316">Podaj identyfikatory RID na liście rozdzielanych średnikami.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-316">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="7f4a4-317">Nazwa właściwości [ \<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (liczba mnoga).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-317">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="7f4a4-318">Aby uzyskać więcej informacji, zobacz [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-318">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="7f4a4-319">Właściwość `<SelfContained>` jest `true`ustawiona na:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-319">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="7f4a4-320">Konto użytkownika usługi</span><span class="sxs-lookup"><span data-stu-id="7f4a4-320">Service user account</span></span>

<span data-ttu-id="7f4a4-321">Aby utworzyć konto użytkownika dla usługi, użyj polecenia cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z administracyjnej powłoki poleceń programu PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-321">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="7f4a4-322">W systemie Windows 10 October 2018 Update (wersja 1809/kompilacja 10.0.17763) lub nowszej:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-322">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="7f4a4-323">W systemie operacyjnym Windows wcześniej niż Windows 10 October 2018 Update (wersja 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="7f4a4-323">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="7f4a4-324">Po wyświetleniu monitu podaj [silne hasło.](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-324">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="7f4a4-325">O `-AccountExpires` ile parametr nie zostanie dostarczony do polecenia cmdlet <xref:System.DateTime> [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z wygaśnięciem, konto nie wygaśnie.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-325">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="7f4a4-326">Aby uzyskać więcej informacji, zobacz [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) i [Konta użytkowników usług](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-326">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="7f4a4-327">Alternatywnym podejściem do zarządzania użytkownikami podczas korzystania z usługi Active Directory jest użycie kont usług zarządzanych.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-327">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="7f4a4-328">Aby uzyskać więcej informacji, zobacz [Omówienie grupowych kont usług zarządzanych](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-328">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="7f4a4-329">Zaloguj się jako prawa do usługi</span><span class="sxs-lookup"><span data-stu-id="7f4a4-329">Log on as a service rights</span></span>

<span data-ttu-id="7f4a4-330">Aby *ustanowić logowanie jako* prawa do usługi dla konta użytkownika usługi:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-330">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="7f4a4-331">Otwórz edytor lokalnych zasad zabezpieczeń, uruchamiając *plik secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-331">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="7f4a4-332">Rozwiń węzeł **Zasady lokalne** i wybierz pozycję **Przypisanie praw użytkownika**.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-332">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="7f4a4-333">Otwórz **zasadę Logowanie jako usługi.**</span><span class="sxs-lookup"><span data-stu-id="7f4a4-333">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="7f4a4-334">Wybierz **pozycję Dodaj użytkownika lub grupę**.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-334">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="7f4a4-335">Podaj nazwę obiektu (konto użytkownika) przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-335">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="7f4a4-336">Wpisz konto użytkownika`{DOMAIN OR COMPUTER NAME\USER}`( ) w polu nazwa obiektu i wybierz **przycisk OK,** aby dodać użytkownika do zasad.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-336">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="7f4a4-337">Wybierz **pozycję Zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-337">Select **Advanced**.</span></span> <span data-ttu-id="7f4a4-338">Wybierz **pozycję Znajdź teraz**.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-338">Select **Find Now**.</span></span> <span data-ttu-id="7f4a4-339">Wybierz konto użytkownika z listy.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-339">Select the user account from the list.</span></span> <span data-ttu-id="7f4a4-340">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-340">Select **OK**.</span></span> <span data-ttu-id="7f4a4-341">Wybierz **przycisk OK** ponownie, aby dodać użytkownika do zasad.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-341">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="7f4a4-342">Wybierz **przycisk OK** lub **Zastosuj,** aby zaakceptować zmiany.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-342">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="7f4a4-343">Tworzenie usługi systemu Windows i zarządzanie nią</span><span class="sxs-lookup"><span data-stu-id="7f4a4-343">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="7f4a4-344">Tworzenie usługi</span><span class="sxs-lookup"><span data-stu-id="7f4a4-344">Create a service</span></span>

<span data-ttu-id="7f4a4-345">Użyj poleceń programu PowerShell, aby zarejestrować usługę.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-345">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="7f4a4-346">Z administracyjnej powłoki poleceń programu PowerShell 6 wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-346">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="7f4a4-347">`{EXE PATH}`&ndash; Ścieżka do folderu aplikacji na hoście `d:\myservice`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-347">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="7f4a4-348">Nie dołączaj pliku wykonywalnego aplikacji do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-348">Don't include the app's executable in the path.</span></span> <span data-ttu-id="7f4a4-349">Ukośnik na końcu nie jest wymagany.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-349">A trailing slash isn't required.</span></span>
* <span data-ttu-id="7f4a4-350">`{DOMAIN OR COMPUTER NAME\USER}`&ndash; Konto użytkownika usługi (na przykład `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-350">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="7f4a4-351">`{SERVICE NAME}`&ndash; Nazwa usługi (na `MyService`przykład ).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-351">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="7f4a4-352">`{EXE FILE PATH}`&ndash; Ścieżka wykonywalna aplikacji `d:\myservice\myservice.exe`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-352">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="7f4a4-353">Dołącz nazwę pliku wykonywalnego z rozszerzeniem.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-353">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="7f4a4-354">`{DESCRIPTION}`&ndash; Opis usługi (na `My sample service`przykład ).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-354">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="7f4a4-355">`{DISPLAY NAME}`&ndash; Nazwa wyświetlana usługi `My Service`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-355">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="7f4a4-356">Uruchamianie usługi</span><span class="sxs-lookup"><span data-stu-id="7f4a4-356">Start a service</span></span>

<span data-ttu-id="7f4a4-357">Uruchom usługę za pomocą następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-357">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="7f4a4-358">Polecenie trwa kilka sekund, aby uruchomić usługę.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-358">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="7f4a4-359">Określanie stanu usługi</span><span class="sxs-lookup"><span data-stu-id="7f4a4-359">Determine a service's status</span></span>

<span data-ttu-id="7f4a4-360">Aby sprawdzić stan usługi, użyj następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-360">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="7f4a4-361">Stan jest zgłaszany jako jedna z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-361">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="7f4a4-362">Zatrzymywanie usługi</span><span class="sxs-lookup"><span data-stu-id="7f4a4-362">Stop a service</span></span>

<span data-ttu-id="7f4a4-363">Zatrzymaj usługę za pomocą następującego polecenia programu Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-363">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="7f4a4-364">Usuwanie usługi</span><span class="sxs-lookup"><span data-stu-id="7f4a4-364">Remove a service</span></span>

<span data-ttu-id="7f4a4-365">Po krótkim opóźnieniu zatrzymania usługi usuń usługę za pomocą następującego polecenia Programu Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-365">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="7f4a4-366">Obsługa zdarzeń uruchamiania i zatrzymywania</span><span class="sxs-lookup"><span data-stu-id="7f4a4-366">Handle starting and stopping events</span></span>

<span data-ttu-id="7f4a4-367">Aby <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>obsłużyć , <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>i <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> zdarzenia:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-367">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="7f4a4-368">Utwórz klasę, która <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> wywodzi się z `OnStarting`, `OnStarted`i `OnStopping` metody:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-368">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="7f4a4-369">Utwórz metodę <xref:Microsoft.AspNetCore.Hosting.IWebHost> rozszerzenia, `CustomWebHostService` która <xref:System.ServiceProcess.ServiceBase.Run*>przekazuje do :</span><span class="sxs-lookup"><span data-stu-id="7f4a4-369">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="7f4a4-370">W `Program.Main`, `RunAsCustomService` wywołać metodę <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>rozszerzenia zamiast:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-370">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="7f4a4-371">Aby wyświetlić <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> lokalizację `Program.Main`w , zapoznaj się z przykładowym kodem pokazanym w sekcji [Typ wdrożenia.](#deployment-type)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-371">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="7f4a4-372">Scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="7f4a4-372">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="7f4a4-373">Usługi, które wchodzą w interakcję z żądaniami z Internetu lub sieci firmowej i znajdują się za serwerem proxy lub modułem równoważenia obciążenia, mogą wymagać dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-373">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="7f4a4-374">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-374">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="7f4a4-375">Konfigurowanie punktów końcowych</span><span class="sxs-lookup"><span data-stu-id="7f4a4-375">Configure endpoints</span></span>

<span data-ttu-id="7f4a4-376">Domyślnie ASP.NET Core wiąże się `http://localhost:5000`z programem .</span><span class="sxs-lookup"><span data-stu-id="7f4a4-376">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="7f4a4-377">Skonfiguruj adres URL `ASPNETCORE_URLS` i port, ustawiając zmienną środowiskową.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-377">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="7f4a4-378">Aby uzyskać dodatkowe podejścia do konfiguracji adresów URL i portów, zobacz odpowiedni artykuł na temat serwera:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-378">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="7f4a4-379">Powyższe wskazówki obejmują obsługę punktów końcowych HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-379">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="7f4a4-380">Na przykład skonfiguruj aplikację dla protokołu HTTPS, gdy uwierzytelnianie jest używane z usługą systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-380">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="7f4a4-381">Użycie ASP.NET certyfikatu dewelopera HTTPS w celu zabezpieczenia punktu końcowego usługi nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-381">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="7f4a4-382">Bieżący katalog i katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="7f4a4-382">Current directory and content root</span></span>

<span data-ttu-id="7f4a4-383">Bieżący katalog roboczy <xref:System.IO.Directory.GetCurrentDirectory*> zwracany przez wywołanie usługi systemu Windows to folder *C:\\WINDOWS\\system32.*</span><span class="sxs-lookup"><span data-stu-id="7f4a4-383">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="7f4a4-384">Folder *system32* nie jest odpowiednią lokalizacją do przechowywania plików usługi (na przykład plików ustawień).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-384">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="7f4a4-385">Użyj jednego z następujących podejść do obsługi i uzyskiwania dostępu do zasobów i plików ustawień usługi.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-385">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="7f4a4-386">Ustawianie ścieżki głównej zawartości do folderu aplikacji</span><span class="sxs-lookup"><span data-stu-id="7f4a4-386">Set the content root path to the app's folder</span></span>

<span data-ttu-id="7f4a4-387">Jest <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> to ta sama `binPath` ścieżka podana do argumentu podczas tworzenia usługi.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-387">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="7f4a4-388">Zamiast dzwonić, `GetCurrentDirectory` aby utworzyć ścieżki do <xref:System.IO.Directory.SetCurrentDirectory*> plików ustawień, zadzwoń ze ścieżką do [katalogu głównego zawartości](xref:fundamentals/index#content-root)aplikacji .</span><span class="sxs-lookup"><span data-stu-id="7f4a4-388">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="7f4a4-389">W `Program.Main`obszarze , określ ścieżkę do folderu pliku wykonywalnego usługi i użyj ścieżki do ustanowienia katalogu głównego zawartości aplikacji:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-389">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="7f4a4-390">Przechowywanie plików usługi w odpowiednim miejscu na dysku</span><span class="sxs-lookup"><span data-stu-id="7f4a4-390">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="7f4a4-391">Określ ścieżkę <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> bezwzględną <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> podczas korzystania z folderu zawierającego pliki.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-391">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="7f4a4-392">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="7f4a4-392">Troubleshoot</span></span>

<span data-ttu-id="7f4a4-393">Aby rozwiązać problem z <xref:test/troubleshoot>aplikacją Usługi systemu Windows, zobacz .</span><span class="sxs-lookup"><span data-stu-id="7f4a4-393">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="7f4a4-394">Typowe błędy</span><span class="sxs-lookup"><span data-stu-id="7f4a4-394">Common errors</span></span>

* <span data-ttu-id="7f4a4-395">Używana jest stara lub wersja wstępna programu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-395">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="7f4a4-396">Zarejestrowana usługa nie używa **opublikowanych** danych wyjściowych aplikacji z polecenia [publikowania dotnet.](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-396">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="7f4a4-397">Dane wyjściowe polecenia [kompilacji dotnet](/dotnet/core/tools/dotnet-build) nie jest obsługiwany dla wdrażania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-397">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="7f4a4-398">Opublikowane zasoby znajdują się w jednym z następujących folderów w zależności od typu wdrożenia:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-398">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="7f4a4-399">*bin/release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-399">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="7f4a4-400">*bin/release/{TARGET FRAMEWORK}/{IDENTYFIKATOR ŚRODOWISKA URUCHOMIENIOWEGO}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="7f4a4-401">Usługa nie jest w stanie RUNNING.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-401">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="7f4a4-402">Ścieżki do zasobów używanych przez aplikację (na przykład certyfikaty) są niepoprawne.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-402">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="7f4a4-403">Podstawową ścieżką usługi systemu Windows jest *c:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-403">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="7f4a4-404">Użytkownik nie ma *logowania jako* praw do usługi.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-404">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="7f4a4-405">Hasło użytkownika wygasło lub zostało niepoprawnie przekazane `New-Service` podczas wykonywania polecenia programu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-405">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="7f4a4-406">Aplikacja wymaga ASP.NET uwierzytelniania core, ale nie jest skonfigurowana do bezpiecznych połączeń (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-406">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="7f4a4-407">Port adresu URL żądania jest niepoprawny lub niepoprawnie skonfigurowany w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-407">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="7f4a4-408">Dzienniki zdarzeń systemu i aplikacji</span><span class="sxs-lookup"><span data-stu-id="7f4a4-408">System and Application Event Logs</span></span>

<span data-ttu-id="7f4a4-409">Dostęp do dzienników zdarzeń systemu i aplikacji:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-409">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="7f4a4-410">Otwórz menu Start, wyszukaj *podgląd zdarzeń*i wybierz aplikację **Podgląd zdarzeń.**</span><span class="sxs-lookup"><span data-stu-id="7f4a4-410">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="7f4a4-411">W **Podglądzie zdarzeń**otwórz węzeł **Dzienniki systemu Windows.**</span><span class="sxs-lookup"><span data-stu-id="7f4a4-411">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="7f4a4-412">Wybierz **system,** aby otworzyć dziennik zdarzeń systemu.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-412">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="7f4a4-413">Wybierz **opcję Aplikacja,** aby otworzyć dziennik zdarzeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-413">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="7f4a4-414">Wyszukaj błędy skojarzone z nieudaną aplikacją.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-414">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="7f4a4-415">Uruchamianie aplikacji w wierszu polecenia</span><span class="sxs-lookup"><span data-stu-id="7f4a4-415">Run the app at a command prompt</span></span>

<span data-ttu-id="7f4a4-416">Wiele błędów uruchamiania nie generuje przydatnych informacji w dziennikach zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-416">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="7f4a4-417">Przyczynę niektórych błędów można znaleźć, uruchamiając aplikację w wierszu polecenia w systemie hostingowym.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-417">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="7f4a4-418">Aby zarejestrować dodatkowe szczegóły z aplikacji, obniżyć [poziom dziennika](xref:fundamentals/logging/index#log-level) lub uruchomić aplikację w [środowisku deweloperskim](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-418">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="7f4a4-419">Wyczyść pamięć podręczną pakietów</span><span class="sxs-lookup"><span data-stu-id="7f4a4-419">Clear package caches</span></span>

<span data-ttu-id="7f4a4-420">Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestawu .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-420">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="7f4a4-421">W niektórych przypadkach niespójne pakiety mogą spowodować przerwanie aplikacji podczas wykonywania głównych uaktualnień.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-421">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="7f4a4-422">Większość z tych problemów można rozwiązać, postępując zgodnie z następującymi instrukcjami:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-422">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="7f4a4-423">Usuń *foldery bin* i *obj.*</span><span class="sxs-lookup"><span data-stu-id="7f4a4-423">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="7f4a4-424">Wyczyść bufory pakietu, wykonując [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) z powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-424">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="7f4a4-425">Czyszczenie pamięci podręcznych pakietów można również wykonać za pomocą narzędzia [nuget.exe](https://www.nuget.org/downloads) i wykonać polecenie `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-425">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="7f4a4-426">*nuget.exe* nie jest w pakiecie zainstalować z systemem operacyjnym Windows dla komputerów stacjonarnych i muszą być uzyskane oddzielnie od [strony internetowej NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-426">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="7f4a4-427">Przywracanie i odbudowywać projekt.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-427">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="7f4a4-428">Usuń wszystkie pliki w folderze wdrażania na serwerze przed ponowne wdrożeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-428">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="7f4a4-429">Powolna lub wisząca aplikacja</span><span class="sxs-lookup"><span data-stu-id="7f4a4-429">Slow or hanging app</span></span>

<span data-ttu-id="7f4a4-430">*Zrzut awaryjny* jest migawką pamięci systemu i może pomóc w określeniu przyczyny awarii aplikacji, awarii uruchamiania lub powolnej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-430">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="7f4a4-431">Aplikacja ulega awarii lub napotyka wyjątek</span><span class="sxs-lookup"><span data-stu-id="7f4a4-431">App crashes or encounters an exception</span></span>

<span data-ttu-id="7f4a4-432">Uzyskiwanie i analizowanie zrzutu z [raportowania błędów systemu Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="7f4a4-432">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="7f4a4-433">Utwórz folder do przechowywania `c:\dumps`plików zrzutu awaryjnego w pliku .</span><span class="sxs-lookup"><span data-stu-id="7f4a4-433">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="7f4a4-434">Uruchom [skrypt EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) o nazwie wykonywalnej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-434">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="7f4a4-435">Uruchom aplikację w warunkach, które powodują awarię.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-435">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="7f4a4-436">Po wystąpieniu awarii uruchom [skrypt DisableDumps PowerShell:](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-436">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="7f4a4-437">Po awarii aplikacji i kolekcji zrzutu jest zakończona, aplikacja może zakończyć się normalnie.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-437">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="7f4a4-438">Skrypt programu PowerShell konfiguruje program WER do zbierania maksymalnie pięciu zrzutów na aplikację.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-438">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="7f4a4-439">Zrzuty awaryjne mogą zająć dużą ilość miejsca na dysku (do kilku gigabajtów).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-439">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="7f4a4-440">Aplikacja zawiesza się, kończy się niepowodzeniem podczas uruchamiania lub działa normalnie</span><span class="sxs-lookup"><span data-stu-id="7f4a4-440">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="7f4a4-441">Gdy aplikacja *zawiesza* się (przestaje odpowiadać, ale nie ulega awarii), kończy się niepowodzeniem podczas uruchamiania lub działa normalnie, zobacz [Pliki zrzutu w trybie użytkownika: Wybieranie najlepszego narzędzia,](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) aby wybrać odpowiednie narzędzie do produkcji zrzutu.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-441">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="7f4a4-442">Analizowanie zrzutu</span><span class="sxs-lookup"><span data-stu-id="7f4a4-442">Analyze the dump</span></span>

<span data-ttu-id="7f4a4-443">Zrzut można analizować przy użyciu kilku metod.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-443">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="7f4a4-444">Aby uzyskać więcej informacji, zobacz [Analizowanie pliku zrzutu w trybie użytkownika](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-444">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7f4a4-445">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="7f4a4-445">Additional resources</span></span>

* <span data-ttu-id="7f4a4-446">[Konfiguracja punktu końcowego pustułka](xref:fundamentals/servers/kestrel#endpoint-configuration) (obejmuje konfigurację HTTPS i obsługę SNI)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-446">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="7f4a4-447">Aplikacja ASP.NET Core może być hostowana w systemie Windows jako [usługa systemu Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez korzystania z usług IIS.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-447">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="7f4a4-448">Po uruchomieniu jako usługa systemu Windows aplikacja jest uruchamiana automatycznie po ponownym uruchomieniu serwera.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-448">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="7f4a4-449">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7f4a4-449">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7f4a4-450">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="7f4a4-450">Prerequisites</span></span>

* [<span data-ttu-id="7f4a4-451">ASP.NET Core SDK 2.1 lub nowszym</span><span class="sxs-lookup"><span data-stu-id="7f4a4-451">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="7f4a4-452">Program PowerShell 6.2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="7f4a4-452">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="7f4a4-453">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="7f4a4-453">App configuration</span></span>

<span data-ttu-id="7f4a4-454">Aplikacja wymaga odwołań do pakietów dla [witryny Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) i [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-454">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="7f4a4-455">Aby przetestować i debugować podczas uruchamiania poza usługą, dodaj kod, aby ustalić, czy aplikacja jest uruchomiona jako usługa lub aplikacja konsoli.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-455">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="7f4a4-456">Sprawdź, czy debuger jest `--console` dołączony lub przełącznik jest obecny.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-456">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="7f4a4-457">Jeśli którykolwiek z tych warunków jest spełniony (aplikacja <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>nie jest uruchamiana jako usługa), zadzwoń .</span><span class="sxs-lookup"><span data-stu-id="7f4a4-457">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="7f4a4-458">Jeśli warunki są fałszywe (aplikacja jest uruchamiana jako usługa):</span><span class="sxs-lookup"><span data-stu-id="7f4a4-458">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="7f4a4-459">Zadzwoń <xref:System.IO.Directory.SetCurrentDirectory*> i użyj ścieżki do opublikowanej lokalizacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-459">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="7f4a4-460">Nie wywołaj, <xref:System.IO.Directory.GetCurrentDirectory*> aby uzyskać ścieżkę, ponieważ aplikacja usługi systemu Windows <xref:System.IO.Directory.GetCurrentDirectory*> zwraca folder *C:\\WINDOWS\\system32,* gdy jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-460">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="7f4a4-461">Aby uzyskać więcej informacji, zobacz [sekcję Bieżący katalog i katalog zawartości.](#current-directory-and-content-root)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-461">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="7f4a4-462">Ten krok jest wykonywany przed skonfigurowaniem aplikacji w pliku `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-462">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="7f4a4-463">Wywołanie, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> aby uruchomić aplikację jako usługę.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-463">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="7f4a4-464">Ponieważ [dostawca konfiguracji wiersza polecenia](xref:fundamentals/configuration/index#command-line-configuration-provider) wymaga par nazwa-wartość dla `--console` argumentów wiersza <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> polecenia, przełącznik jest usuwany z argumentów przed odebraniem argumentów.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-464">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="7f4a4-465">Aby zapisać w dzienniku zdarzeń systemu Windows, dodaj dostawcę EventLog do <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-465">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="7f4a4-466">Ustaw poziom rejestrowania `Logging:LogLevel:Default` za pomocą klucza w *appsettings. Plik Production.json.*</span><span class="sxs-lookup"><span data-stu-id="7f4a4-466">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="7f4a4-467">W poniższym przykładzie z `RunAsCustomService` przykładowej aplikacji, jest wywoływana zamiast <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> w celu obsługi zdarzeń lifetime w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-467">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="7f4a4-468">Aby uzyskać więcej informacji, zobacz [Handle uruchamiania i zatrzymywania zdarzeń](#handle-starting-and-stopping-events) sekcji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-468">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="7f4a4-469">Typ wdrożenia</span><span class="sxs-lookup"><span data-stu-id="7f4a4-469">Deployment type</span></span>

<span data-ttu-id="7f4a4-470">Aby uzyskać informacje i porady dotyczące scenariuszy wdrażania, zobacz [wdrożenie aplikacji .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-470">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="7f4a4-471">SDK</span><span class="sxs-lookup"><span data-stu-id="7f4a4-471">SDK</span></span>

<span data-ttu-id="7f4a4-472">W przypadku usługi opartej na aplikacjach sieci web, która używa stron Razor Pages lub struktur MVC, określ składnik Web SDK w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-472">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="7f4a4-473">Jeśli usługa wykonuje tylko zadania w tle (na przykład [usługi hostowane),](xref:fundamentals/host/hosted-services)określ sdk roboczy w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-473">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="7f4a4-474">Wdrożenie zależne od struktury (FDD)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-474">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="7f4a4-475">Wdrożenie zależne od struktury (FDD) opiera się na obecności udostępnionej wersji systemu .NET Core w systemie docelowym.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-475">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="7f4a4-476">Gdy scenariusz FDD zostanie przyjęty zgodnie ze wskazówkami zawartymi w tym artykule, SDK tworzy plik wykonywalny (*.exe*), nazywany *wykonywalnym zależnym od struktury*.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-476">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="7f4a4-477">Identyfikator środowiska wykonawczego systemu Windows [(RID)](/dotnet/core/rid-catalog) [\<(RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) zawiera platformę docelową.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-477">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="7f4a4-478">W poniższym przykładzie identyfikator `win7-x64`RID jest ustawiony na .</span><span class="sxs-lookup"><span data-stu-id="7f4a4-478">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="7f4a4-479">Właściwość `<SelfContained>` jest `false`ustawiona na .</span><span class="sxs-lookup"><span data-stu-id="7f4a4-479">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="7f4a4-480">Te właściwości instruują SDK do generowania pliku wykonywalnego *(exe)* dla systemu Windows i aplikacji, która zależy od udostępnionej struktury .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-480">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="7f4a4-481">Właściwość `<UseAppHost>` jest `true`ustawiona na .</span><span class="sxs-lookup"><span data-stu-id="7f4a4-481">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="7f4a4-482">Ta właściwość zapewnia usługi ze ścieżką aktywacji (wykonywalny, *.exe)* dla FDD.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-482">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="7f4a4-483">Plik *web.config,* który jest zwykle produkowany podczas publikowania aplikacji ASP.NET Core, nie jest potrzebny dla aplikacji Usług systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-483">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="7f4a4-484">Aby wyłączyć tworzenie pliku *web.config,* `<IsTransformWebConfigDisabled>` dodaj właściwość ustawioną na `true`.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-484">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="7f4a4-485">Samodzielne wdrażanie (SCD)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-485">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="7f4a4-486">Samodzielne wdrożenie (SCD) nie opiera się na obecności udostępnionej struktury w systemie hosta.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-486">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="7f4a4-487">Środowisko wykonawcze i zależności aplikacji są wdrażane za pomocą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-487">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="7f4a4-488">Identyfikator środowiska wykonawczego systemu Windows [(RID)](/dotnet/core/rid-catalog) `<PropertyGroup>` znajduje się w platformie zawierającej platformę docelową:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-488">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="7f4a4-489">Aby opublikować wiele identyfikatorów RID:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-489">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="7f4a4-490">Podaj identyfikatory RID na liście rozdzielanych średnikami.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-490">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="7f4a4-491">Nazwa właściwości [ \<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (liczba mnoga).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-491">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="7f4a4-492">Aby uzyskać więcej informacji, zobacz [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-492">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="7f4a4-493">Właściwość `<SelfContained>` jest `true`ustawiona na:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-493">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="7f4a4-494">Konto użytkownika usługi</span><span class="sxs-lookup"><span data-stu-id="7f4a4-494">Service user account</span></span>

<span data-ttu-id="7f4a4-495">Aby utworzyć konto użytkownika dla usługi, użyj polecenia cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z administracyjnej powłoki poleceń programu PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-495">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="7f4a4-496">W systemie Windows 10 October 2018 Update (wersja 1809/kompilacja 10.0.17763) lub nowszej:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-496">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="7f4a4-497">W systemie operacyjnym Windows wcześniej niż Windows 10 October 2018 Update (wersja 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="7f4a4-497">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="7f4a4-498">Po wyświetleniu monitu podaj [silne hasło.](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-498">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="7f4a4-499">O `-AccountExpires` ile parametr nie zostanie dostarczony do polecenia cmdlet <xref:System.DateTime> [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z wygaśnięciem, konto nie wygaśnie.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-499">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="7f4a4-500">Aby uzyskać więcej informacji, zobacz [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) i [Konta użytkowników usług](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-500">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="7f4a4-501">Alternatywnym podejściem do zarządzania użytkownikami podczas korzystania z usługi Active Directory jest użycie kont usług zarządzanych.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-501">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="7f4a4-502">Aby uzyskać więcej informacji, zobacz [Omówienie grupowych kont usług zarządzanych](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-502">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="7f4a4-503">Zaloguj się jako prawa do usługi</span><span class="sxs-lookup"><span data-stu-id="7f4a4-503">Log on as a service rights</span></span>

<span data-ttu-id="7f4a4-504">Aby *ustanowić logowanie jako* prawa do usługi dla konta użytkownika usługi:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-504">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="7f4a4-505">Otwórz edytor lokalnych zasad zabezpieczeń, uruchamiając *plik secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-505">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="7f4a4-506">Rozwiń węzeł **Zasady lokalne** i wybierz pozycję **Przypisanie praw użytkownika**.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-506">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="7f4a4-507">Otwórz **zasadę Logowanie jako usługi.**</span><span class="sxs-lookup"><span data-stu-id="7f4a4-507">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="7f4a4-508">Wybierz **pozycję Dodaj użytkownika lub grupę**.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-508">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="7f4a4-509">Podaj nazwę obiektu (konto użytkownika) przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-509">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="7f4a4-510">Wpisz konto użytkownika`{DOMAIN OR COMPUTER NAME\USER}`( ) w polu nazwa obiektu i wybierz **przycisk OK,** aby dodać użytkownika do zasad.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-510">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="7f4a4-511">Wybierz **pozycję Zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-511">Select **Advanced**.</span></span> <span data-ttu-id="7f4a4-512">Wybierz **pozycję Znajdź teraz**.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-512">Select **Find Now**.</span></span> <span data-ttu-id="7f4a4-513">Wybierz konto użytkownika z listy.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-513">Select the user account from the list.</span></span> <span data-ttu-id="7f4a4-514">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-514">Select **OK**.</span></span> <span data-ttu-id="7f4a4-515">Wybierz **przycisk OK** ponownie, aby dodać użytkownika do zasad.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-515">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="7f4a4-516">Wybierz **przycisk OK** lub **Zastosuj,** aby zaakceptować zmiany.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-516">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="7f4a4-517">Tworzenie usługi systemu Windows i zarządzanie nią</span><span class="sxs-lookup"><span data-stu-id="7f4a4-517">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="7f4a4-518">Tworzenie usługi</span><span class="sxs-lookup"><span data-stu-id="7f4a4-518">Create a service</span></span>

<span data-ttu-id="7f4a4-519">Użyj poleceń programu PowerShell, aby zarejestrować usługę.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-519">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="7f4a4-520">Z administracyjnej powłoki poleceń programu PowerShell 6 wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-520">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="7f4a4-521">`{EXE PATH}`&ndash; Ścieżka do folderu aplikacji na hoście `d:\myservice`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-521">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="7f4a4-522">Nie dołączaj pliku wykonywalnego aplikacji do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-522">Don't include the app's executable in the path.</span></span> <span data-ttu-id="7f4a4-523">Ukośnik na końcu nie jest wymagany.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-523">A trailing slash isn't required.</span></span>
* <span data-ttu-id="7f4a4-524">`{DOMAIN OR COMPUTER NAME\USER}`&ndash; Konto użytkownika usługi (na przykład `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-524">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="7f4a4-525">`{SERVICE NAME}`&ndash; Nazwa usługi (na `MyService`przykład ).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-525">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="7f4a4-526">`{EXE FILE PATH}`&ndash; Ścieżka wykonywalna aplikacji `d:\myservice\myservice.exe`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-526">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="7f4a4-527">Dołącz nazwę pliku wykonywalnego z rozszerzeniem.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-527">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="7f4a4-528">`{DESCRIPTION}`&ndash; Opis usługi (na `My sample service`przykład ).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-528">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="7f4a4-529">`{DISPLAY NAME}`&ndash; Nazwa wyświetlana usługi `My Service`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-529">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="7f4a4-530">Uruchamianie usługi</span><span class="sxs-lookup"><span data-stu-id="7f4a4-530">Start a service</span></span>

<span data-ttu-id="7f4a4-531">Uruchom usługę za pomocą następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-531">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="7f4a4-532">Polecenie trwa kilka sekund, aby uruchomić usługę.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-532">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="7f4a4-533">Określanie stanu usługi</span><span class="sxs-lookup"><span data-stu-id="7f4a4-533">Determine a service's status</span></span>

<span data-ttu-id="7f4a4-534">Aby sprawdzić stan usługi, użyj następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-534">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="7f4a4-535">Stan jest zgłaszany jako jedna z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-535">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="7f4a4-536">Zatrzymywanie usługi</span><span class="sxs-lookup"><span data-stu-id="7f4a4-536">Stop a service</span></span>

<span data-ttu-id="7f4a4-537">Zatrzymaj usługę za pomocą następującego polecenia programu Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-537">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="7f4a4-538">Usuwanie usługi</span><span class="sxs-lookup"><span data-stu-id="7f4a4-538">Remove a service</span></span>

<span data-ttu-id="7f4a4-539">Po krótkim opóźnieniu zatrzymania usługi usuń usługę za pomocą następującego polecenia Programu Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-539">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="7f4a4-540">Obsługa zdarzeń uruchamiania i zatrzymywania</span><span class="sxs-lookup"><span data-stu-id="7f4a4-540">Handle starting and stopping events</span></span>

<span data-ttu-id="7f4a4-541">Aby <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>obsłużyć , <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>i <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> zdarzenia:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-541">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="7f4a4-542">Utwórz klasę, która <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> wywodzi się z `OnStarting`, `OnStarted`i `OnStopping` metody:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-542">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="7f4a4-543">Utwórz metodę <xref:Microsoft.AspNetCore.Hosting.IWebHost> rozszerzenia, `CustomWebHostService` która <xref:System.ServiceProcess.ServiceBase.Run*>przekazuje do :</span><span class="sxs-lookup"><span data-stu-id="7f4a4-543">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="7f4a4-544">W `Program.Main`, `RunAsCustomService` wywołać metodę <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>rozszerzenia zamiast:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-544">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="7f4a4-545">Aby wyświetlić <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> lokalizację `Program.Main`w , zapoznaj się z przykładowym kodem pokazanym w sekcji [Typ wdrożenia.](#deployment-type)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-545">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="7f4a4-546">Scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="7f4a4-546">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="7f4a4-547">Usługi, które wchodzą w interakcję z żądaniami z Internetu lub sieci firmowej i znajdują się za serwerem proxy lub modułem równoważenia obciążenia, mogą wymagać dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-547">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="7f4a4-548">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-548">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="7f4a4-549">Konfigurowanie punktów końcowych</span><span class="sxs-lookup"><span data-stu-id="7f4a4-549">Configure endpoints</span></span>

<span data-ttu-id="7f4a4-550">Domyślnie ASP.NET Core wiąże się `http://localhost:5000`z programem .</span><span class="sxs-lookup"><span data-stu-id="7f4a4-550">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="7f4a4-551">Skonfiguruj adres URL `ASPNETCORE_URLS` i port, ustawiając zmienną środowiskową.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-551">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="7f4a4-552">Aby uzyskać dodatkowe podejścia do konfiguracji adresów URL i portów, zobacz odpowiedni artykuł na temat serwera:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-552">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="7f4a4-553">Powyższe wskazówki obejmują obsługę punktów końcowych HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-553">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="7f4a4-554">Na przykład skonfiguruj aplikację dla protokołu HTTPS, gdy uwierzytelnianie jest używane z usługą systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-554">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="7f4a4-555">Użycie ASP.NET certyfikatu dewelopera HTTPS w celu zabezpieczenia punktu końcowego usługi nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-555">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="7f4a4-556">Bieżący katalog i katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="7f4a4-556">Current directory and content root</span></span>

<span data-ttu-id="7f4a4-557">Bieżący katalog roboczy <xref:System.IO.Directory.GetCurrentDirectory*> zwracany przez wywołanie usługi systemu Windows to folder *C:\\WINDOWS\\system32.*</span><span class="sxs-lookup"><span data-stu-id="7f4a4-557">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="7f4a4-558">Folder *system32* nie jest odpowiednią lokalizacją do przechowywania plików usługi (na przykład plików ustawień).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-558">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="7f4a4-559">Użyj jednego z następujących podejść do obsługi i uzyskiwania dostępu do zasobów i plików ustawień usługi.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-559">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="7f4a4-560">Ustawianie ścieżki głównej zawartości do folderu aplikacji</span><span class="sxs-lookup"><span data-stu-id="7f4a4-560">Set the content root path to the app's folder</span></span>

<span data-ttu-id="7f4a4-561">Jest <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> to ta sama `binPath` ścieżka podana do argumentu podczas tworzenia usługi.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-561">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="7f4a4-562">Zamiast dzwonić, `GetCurrentDirectory` aby utworzyć ścieżki do <xref:System.IO.Directory.SetCurrentDirectory*> plików ustawień, zadzwoń ze ścieżką do [katalogu głównego zawartości](xref:fundamentals/index#content-root)aplikacji .</span><span class="sxs-lookup"><span data-stu-id="7f4a4-562">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="7f4a4-563">W `Program.Main`obszarze , określ ścieżkę do folderu pliku wykonywalnego usługi i użyj ścieżki do ustanowienia katalogu głównego zawartości aplikacji:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-563">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="7f4a4-564">Przechowywanie plików usługi w odpowiednim miejscu na dysku</span><span class="sxs-lookup"><span data-stu-id="7f4a4-564">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="7f4a4-565">Określ ścieżkę <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> bezwzględną <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> podczas korzystania z folderu zawierającego pliki.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-565">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="7f4a4-566">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="7f4a4-566">Troubleshoot</span></span>

<span data-ttu-id="7f4a4-567">Aby rozwiązać problem z <xref:test/troubleshoot>aplikacją Usługi systemu Windows, zobacz .</span><span class="sxs-lookup"><span data-stu-id="7f4a4-567">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="7f4a4-568">Typowe błędy</span><span class="sxs-lookup"><span data-stu-id="7f4a4-568">Common errors</span></span>

* <span data-ttu-id="7f4a4-569">Używana jest stara lub wersja wstępna programu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-569">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="7f4a4-570">Zarejestrowana usługa nie używa **opublikowanych** danych wyjściowych aplikacji z polecenia [publikowania dotnet.](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-570">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="7f4a4-571">Dane wyjściowe polecenia [kompilacji dotnet](/dotnet/core/tools/dotnet-build) nie jest obsługiwany dla wdrażania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-571">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="7f4a4-572">Opublikowane zasoby znajdują się w jednym z następujących folderów w zależności od typu wdrożenia:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-572">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="7f4a4-573">*bin/release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-573">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="7f4a4-574">*bin/release/{TARGET FRAMEWORK}/{IDENTYFIKATOR ŚRODOWISKA URUCHOMIENIOWEGO}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="7f4a4-575">Usługa nie jest w stanie RUNNING.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-575">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="7f4a4-576">Ścieżki do zasobów używanych przez aplikację (na przykład certyfikaty) są niepoprawne.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-576">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="7f4a4-577">Podstawową ścieżką usługi systemu Windows jest *c:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-577">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="7f4a4-578">Użytkownik nie ma *logowania jako* praw do usługi.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-578">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="7f4a4-579">Hasło użytkownika wygasło lub zostało niepoprawnie przekazane `New-Service` podczas wykonywania polecenia programu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-579">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="7f4a4-580">Aplikacja wymaga ASP.NET uwierzytelniania core, ale nie jest skonfigurowana do bezpiecznych połączeń (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-580">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="7f4a4-581">Port adresu URL żądania jest niepoprawny lub niepoprawnie skonfigurowany w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-581">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="7f4a4-582">Dzienniki zdarzeń systemu i aplikacji</span><span class="sxs-lookup"><span data-stu-id="7f4a4-582">System and Application Event Logs</span></span>

<span data-ttu-id="7f4a4-583">Dostęp do dzienników zdarzeń systemu i aplikacji:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-583">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="7f4a4-584">Otwórz menu Start, wyszukaj *podgląd zdarzeń*i wybierz aplikację **Podgląd zdarzeń.**</span><span class="sxs-lookup"><span data-stu-id="7f4a4-584">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="7f4a4-585">W **Podglądzie zdarzeń**otwórz węzeł **Dzienniki systemu Windows.**</span><span class="sxs-lookup"><span data-stu-id="7f4a4-585">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="7f4a4-586">Wybierz **system,** aby otworzyć dziennik zdarzeń systemu.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-586">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="7f4a4-587">Wybierz **opcję Aplikacja,** aby otworzyć dziennik zdarzeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-587">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="7f4a4-588">Wyszukaj błędy skojarzone z nieudaną aplikacją.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-588">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="7f4a4-589">Uruchamianie aplikacji w wierszu polecenia</span><span class="sxs-lookup"><span data-stu-id="7f4a4-589">Run the app at a command prompt</span></span>

<span data-ttu-id="7f4a4-590">Wiele błędów uruchamiania nie generuje przydatnych informacji w dziennikach zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-590">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="7f4a4-591">Przyczynę niektórych błędów można znaleźć, uruchamiając aplikację w wierszu polecenia w systemie hostingowym.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-591">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="7f4a4-592">Aby zarejestrować dodatkowe szczegóły z aplikacji, obniżyć [poziom dziennika](xref:fundamentals/logging/index#log-level) lub uruchomić aplikację w [środowisku deweloperskim](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-592">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="7f4a4-593">Wyczyść pamięć podręczną pakietów</span><span class="sxs-lookup"><span data-stu-id="7f4a4-593">Clear package caches</span></span>

<span data-ttu-id="7f4a4-594">Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestawu .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-594">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="7f4a4-595">W niektórych przypadkach niespójne pakiety mogą spowodować przerwanie aplikacji podczas wykonywania głównych uaktualnień.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-595">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="7f4a4-596">Większość z tych problemów można rozwiązać, postępując zgodnie z następującymi instrukcjami:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-596">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="7f4a4-597">Usuń *foldery bin* i *obj.*</span><span class="sxs-lookup"><span data-stu-id="7f4a4-597">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="7f4a4-598">Wyczyść bufory pakietu, wykonując [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) z powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-598">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="7f4a4-599">Czyszczenie pamięci podręcznych pakietów można również wykonać za pomocą narzędzia [nuget.exe](https://www.nuget.org/downloads) i wykonać polecenie `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-599">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="7f4a4-600">*nuget.exe* nie jest w pakiecie zainstalować z systemem operacyjnym Windows dla komputerów stacjonarnych i muszą być uzyskane oddzielnie od [strony internetowej NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-600">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="7f4a4-601">Przywracanie i odbudowywać projekt.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-601">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="7f4a4-602">Usuń wszystkie pliki w folderze wdrażania na serwerze przed ponowne wdrożeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-602">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="7f4a4-603">Powolna lub wisząca aplikacja</span><span class="sxs-lookup"><span data-stu-id="7f4a4-603">Slow or hanging app</span></span>

<span data-ttu-id="7f4a4-604">*Zrzut awaryjny* jest migawką pamięci systemu i może pomóc w określeniu przyczyny awarii aplikacji, awarii uruchamiania lub powolnej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-604">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="7f4a4-605">Aplikacja ulega awarii lub napotyka wyjątek</span><span class="sxs-lookup"><span data-stu-id="7f4a4-605">App crashes or encounters an exception</span></span>

<span data-ttu-id="7f4a4-606">Uzyskiwanie i analizowanie zrzutu z [raportowania błędów systemu Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="7f4a4-606">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="7f4a4-607">Utwórz folder do przechowywania `c:\dumps`plików zrzutu awaryjnego w pliku .</span><span class="sxs-lookup"><span data-stu-id="7f4a4-607">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="7f4a4-608">Uruchom [skrypt EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) o nazwie wykonywalnej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="7f4a4-608">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="7f4a4-609">Uruchom aplikację w warunkach, które powodują awarię.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-609">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="7f4a4-610">Po wystąpieniu awarii uruchom [skrypt DisableDumps PowerShell:](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-610">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="7f4a4-611">Po awarii aplikacji i kolekcji zrzutu jest zakończona, aplikacja może zakończyć się normalnie.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-611">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="7f4a4-612">Skrypt programu PowerShell konfiguruje program WER do zbierania maksymalnie pięciu zrzutów na aplikację.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-612">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="7f4a4-613">Zrzuty awaryjne mogą zająć dużą ilość miejsca na dysku (do kilku gigabajtów).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-613">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="7f4a4-614">Aplikacja zawiesza się, kończy się niepowodzeniem podczas uruchamiania lub działa normalnie</span><span class="sxs-lookup"><span data-stu-id="7f4a4-614">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="7f4a4-615">Gdy aplikacja *zawiesza* się (przestaje odpowiadać, ale nie ulega awarii), kończy się niepowodzeniem podczas uruchamiania lub działa normalnie, zobacz [Pliki zrzutu w trybie użytkownika: Wybieranie najlepszego narzędzia,](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) aby wybrać odpowiednie narzędzie do produkcji zrzutu.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-615">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="7f4a4-616">Analizowanie zrzutu</span><span class="sxs-lookup"><span data-stu-id="7f4a4-616">Analyze the dump</span></span>

<span data-ttu-id="7f4a4-617">Zrzut można analizować przy użyciu kilku metod.</span><span class="sxs-lookup"><span data-stu-id="7f4a4-617">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="7f4a4-618">Aby uzyskać więcej informacji, zobacz [Analizowanie pliku zrzutu w trybie użytkownika](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="7f4a4-618">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7f4a4-619">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="7f4a4-619">Additional resources</span></span>

* <span data-ttu-id="7f4a4-620">[Konfiguracja punktu końcowego pustułka](xref:fundamentals/servers/kestrel#endpoint-configuration) (obejmuje konfigurację HTTPS i obsługę SNI)</span><span class="sxs-lookup"><span data-stu-id="7f4a4-620">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
