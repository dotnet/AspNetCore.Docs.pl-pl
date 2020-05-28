---
<span data-ttu-id="5424a-101">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5424a-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5424a-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5424a-102">'Blazor'</span></span>
- <span data-ttu-id="5424a-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5424a-103">'Identity'</span></span>
- <span data-ttu-id="5424a-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5424a-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="5424a-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5424a-105">'Razor'</span></span>
- <span data-ttu-id="5424a-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="5424a-106">'SignalR' uid:</span></span> 

---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="5424a-107">ASP.NET Core hosta w usłudze systemu Windows</span><span class="sxs-lookup"><span data-stu-id="5424a-107">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5424a-108">Aplikacja ASP.NET Core może być hostowana w systemie Windows jako [Usługa systemu Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez korzystania z usług IIS.</span><span class="sxs-lookup"><span data-stu-id="5424a-108">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="5424a-109">Gdy usługa jest hostowana w systemie Windows, aplikacja jest uruchamiana automatycznie po ponownym uruchomieniu serwera.</span><span class="sxs-lookup"><span data-stu-id="5424a-109">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="5424a-110">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5424a-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5424a-111">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="5424a-111">Prerequisites</span></span>

* [<span data-ttu-id="5424a-112">ASP.NET Core zestaw SDK 2,1 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="5424a-112">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="5424a-113">Program PowerShell 6,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="5424a-113">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="5424a-114">Szablon usługi procesu roboczego</span><span class="sxs-lookup"><span data-stu-id="5424a-114">Worker Service template</span></span>

<span data-ttu-id="5424a-115">Szablon usługi ASP.NET Core Worker zapewnia punkt początkowy do pisania długotrwałych aplikacji usługi.</span><span class="sxs-lookup"><span data-stu-id="5424a-115">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="5424a-116">Aby użyć szablonu jako podstawy dla aplikacji usługi systemu Windows:</span><span class="sxs-lookup"><span data-stu-id="5424a-116">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="5424a-117">Utwórz aplikację usługi procesu roboczego na podstawie szablonu .NET Core.</span><span class="sxs-lookup"><span data-stu-id="5424a-117">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="5424a-118">Postępuj zgodnie ze wskazówkami w sekcji [Konfiguracja aplikacji](#app-configuration) , aby zaktualizować aplikację usługi procesu roboczego tak, aby mogła ona działać jako usługa systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="5424a-118">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="5424a-119">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="5424a-119">App configuration</span></span>

<span data-ttu-id="5424a-120">Aplikacja wymaga odwołania do pakietu dla elementu [Microsoft. Extensions. hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="5424a-120">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="5424a-121">`IHostBuilder.UseWindowsService`jest wywoływana podczas kompilowania hosta.</span><span class="sxs-lookup"><span data-stu-id="5424a-121">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="5424a-122">Jeśli aplikacja działa jako usługa systemu Windows, Metoda:</span><span class="sxs-lookup"><span data-stu-id="5424a-122">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="5424a-123">Ustawia okres istnienia hosta na `WindowsServiceLifetime` .</span><span class="sxs-lookup"><span data-stu-id="5424a-123">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="5424a-124">Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) na [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="5424a-124">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="5424a-125">Aby uzyskać więcej informacji, zapoznaj się z sekcją [Current Directory i root Content](#current-directory-and-content-root) .</span><span class="sxs-lookup"><span data-stu-id="5424a-125">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="5424a-126">Włącza rejestrowanie w dzienniku zdarzeń:</span><span class="sxs-lookup"><span data-stu-id="5424a-126">Enables logging to the event log:</span></span>
  * <span data-ttu-id="5424a-127">Nazwa aplikacji jest używana jako domyślna nazwa źródła.</span><span class="sxs-lookup"><span data-stu-id="5424a-127">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="5424a-128">Domyślny poziom rejestrowania jest *ostrzegawczy* lub wyższy dla aplikacji opartej na szablonie ASP.NET Core, który wywołuje, `CreateDefaultBuilder` Aby skompilować hosta.</span><span class="sxs-lookup"><span data-stu-id="5424a-128">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="5424a-129">Zastąp domyślny poziom dziennika `Logging:EventLog:LogLevel:Default` kluczem w pliku *appSettings. JSON* / *. { Environment}. JSON* lub inny dostawca konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="5424a-129">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="5424a-130">Tylko Administratorzy mogą tworzyć nowe źródła zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="5424a-130">Only administrators can create new event sources.</span></span> <span data-ttu-id="5424a-131">Gdy nie można utworzyć źródła zdarzeń przy użyciu nazwy aplikacji, w źródle *aplikacji* jest rejestrowane ostrzeżenie, a dzienniki zdarzeń są wyłączone.</span><span class="sxs-lookup"><span data-stu-id="5424a-131">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="5424a-132">W `CreateHostBuilder` programie *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5424a-132">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="5424a-133">Następujące przykładowe aplikacje zostały dołączone do tego tematu:</span><span class="sxs-lookup"><span data-stu-id="5424a-133">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="5424a-134">Przykład usługi procesu roboczego w tle: przykład aplikacji innej niż Internet oparta na [szablonie usługi procesu roboczego](#worker-service-template) , który korzysta z [usług hostowanych](xref:fundamentals/host/hosted-services) na potrzeby zadań w tle.</span><span class="sxs-lookup"><span data-stu-id="5424a-134">Background Worker Service Sample: A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="5424a-135">Przykład App Service sieci Web: Razor Przykładowa aplikacja sieci Web, która działa jako usługa systemu Windows z [usługami hostowanymi](xref:fundamentals/host/hosted-services) dla zadań w tle.</span><span class="sxs-lookup"><span data-stu-id="5424a-135">Web App Service Sample: A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="5424a-136">Wskazówki dotyczące MVC znajdują się w artykułach <xref:mvc/overview> i <xref:migration/22-to-30> .</span><span class="sxs-lookup"><span data-stu-id="5424a-136">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="5424a-137">Typ wdrożenia</span><span class="sxs-lookup"><span data-stu-id="5424a-137">Deployment type</span></span>

<span data-ttu-id="5424a-138">Aby uzyskać informacje i porady dotyczące scenariuszy wdrażania, zobacz [wdrażanie aplikacji .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="5424a-138">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="5424a-139">SDK</span><span class="sxs-lookup"><span data-stu-id="5424a-139">SDK</span></span>

<span data-ttu-id="5424a-140">W przypadku usługi opartej na aplikacji sieci Web korzystającej ze Razor stron lub platform MVC należy określić zestaw SDK sieci Web w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="5424a-140">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="5424a-141">Jeśli usługa wykonuje tylko zadania w tle (na przykład [usługi hostowane](xref:fundamentals/host/hosted-services)), określ zestaw SDK procesu roboczego w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="5424a-141">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="5424a-142">Wdrożenie zależne od platformy (FDD)</span><span class="sxs-lookup"><span data-stu-id="5424a-142">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="5424a-143">Wdrożenie zależne od platformy (FDD) zależy od obecności udostępnionej systemowej wersji platformy .NET Core w systemie docelowym.</span><span class="sxs-lookup"><span data-stu-id="5424a-143">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="5424a-144">Po przyjęciu scenariusza FDD zgodnie ze wskazówkami zawartymi w tym artykule zestaw SDK tworzy plik wykonywalny (*. exe*), nazywany *plik wykonywalny zależny od platformy*.</span><span class="sxs-lookup"><span data-stu-id="5424a-144">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="5424a-145">Jeśli używasz [zestawu SDK sieci Web](#sdk), plik *Web. config* , który jest zwykle tworzony podczas publikowania aplikacji ASP.NET Core, jest zbędny dla aplikacji usług systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="5424a-145">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="5424a-146">Aby wyłączyć tworzenie pliku *Web. config* , należy dodać `<IsTransformWebConfigDisabled>` Właściwość ustawioną na `true` .</span><span class="sxs-lookup"><span data-stu-id="5424a-146">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="5424a-147">Wdrażanie samodzielne (SCD)</span><span class="sxs-lookup"><span data-stu-id="5424a-147">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="5424a-148">Wdrożenie samodzielne (SCD) nie polega na obecności struktury udostępnionej w systemie hosta.</span><span class="sxs-lookup"><span data-stu-id="5424a-148">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="5424a-149">Środowisko uruchomieniowe i zależności aplikacji są wdrażane przy użyciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-149">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="5424a-150">[Identyfikator środowiska uruchomieniowego systemu Windows (RID)](/dotnet/core/rid-catalog) znajduje się w, `<PropertyGroup>` który zawiera platformę docelową:</span><span class="sxs-lookup"><span data-stu-id="5424a-150">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="5424a-151">Aby opublikować dla wielu identyfikatorów RID:</span><span class="sxs-lookup"><span data-stu-id="5424a-151">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="5424a-152">Podaj identyfikatory RID na liście rozdzielanej średnikami.</span><span class="sxs-lookup"><span data-stu-id="5424a-152">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="5424a-153">Użyj nazwy właściwości [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="5424a-153">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="5424a-154">Aby uzyskać więcej informacji, zobacz [katalog .NET Core RID Catalog](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="5424a-154">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="5424a-155">Konto użytkownika usługi</span><span class="sxs-lookup"><span data-stu-id="5424a-155">Service user account</span></span>

<span data-ttu-id="5424a-156">Aby utworzyć konto użytkownika dla usługi, należy użyć polecenia cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) w powłoce poleceń administracyjnych programu PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="5424a-156">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="5424a-157">W systemie Windows 10 października 2018 Update (wersja 1809/Kompilacja 10.0.17763) lub nowsza:</span><span class="sxs-lookup"><span data-stu-id="5424a-157">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="5424a-158">W systemie operacyjnym Windows w wersji starszej niż aktualizacja 2018 systemu Windows 10 października (wersja 1809/Kompilacja 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="5424a-158">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="5424a-159">Podaj [silne hasło](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) po wyświetleniu monitu.</span><span class="sxs-lookup"><span data-stu-id="5424a-159">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="5424a-160">Jeśli `-AccountExpires` parametr nie zostanie dostarczony do polecenia cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z wygaśnięciem <xref:System.DateTime> , konto nie wygaśnie.</span><span class="sxs-lookup"><span data-stu-id="5424a-160">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="5424a-161">Aby uzyskać więcej informacji, zobacz [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) i [konta użytkowników usług](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="5424a-161">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="5424a-162">Alternatywna metoda zarządzania użytkownikami podczas korzystania z Active Directory polega na użyciu zarządzanych kont usług.</span><span class="sxs-lookup"><span data-stu-id="5424a-162">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="5424a-163">Aby uzyskać więcej informacji, zobacz [Omówienie kont usług zarządzanych przez grupę](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="5424a-163">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="5424a-164">Logowanie jako prawa usługi</span><span class="sxs-lookup"><span data-stu-id="5424a-164">Log on as a service rights</span></span>

<span data-ttu-id="5424a-165">Aby nawiązać *Logowanie jako prawa usługi* dla konta użytkownika usługi:</span><span class="sxs-lookup"><span data-stu-id="5424a-165">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="5424a-166">Otwórz Edytor lokalnych zasad zabezpieczeń, uruchamiając program *secpol. msc*.</span><span class="sxs-lookup"><span data-stu-id="5424a-166">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="5424a-167">Rozwiń węzeł **Zasady lokalne** , a następnie wybierz pozycję **Przypisywanie praw użytkownika**.</span><span class="sxs-lookup"><span data-stu-id="5424a-167">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="5424a-168">Otwórz okno **Logowanie jako usługa** .</span><span class="sxs-lookup"><span data-stu-id="5424a-168">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="5424a-169">Wybierz pozycję **Dodaj użytkownika lub grupę**.</span><span class="sxs-lookup"><span data-stu-id="5424a-169">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="5424a-170">Podaj nazwę obiektu (konto użytkownika) przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="5424a-170">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="5424a-171">Wpisz konto użytkownika ( `{DOMAIN OR COMPUTER NAME\USER}` ) w polu Nazwa obiektu, a następnie wybierz **przycisk OK** , aby dodać użytkownika do zasad.</span><span class="sxs-lookup"><span data-stu-id="5424a-171">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="5424a-172">Wybierz pozycję **Zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="5424a-172">Select **Advanced**.</span></span> <span data-ttu-id="5424a-173">Wybierz pozycję **Znajdź teraz**.</span><span class="sxs-lookup"><span data-stu-id="5424a-173">Select **Find Now**.</span></span> <span data-ttu-id="5424a-174">Wybierz z listy konto użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5424a-174">Select the user account from the list.</span></span> <span data-ttu-id="5424a-175">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="5424a-175">Select **OK**.</span></span> <span data-ttu-id="5424a-176">Ponownie wybierz **przycisk OK** , aby dodać użytkownika do zasad.</span><span class="sxs-lookup"><span data-stu-id="5424a-176">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="5424a-177">Wybierz **przycisk OK** lub **Zastosuj** , aby zaakceptować zmiany.</span><span class="sxs-lookup"><span data-stu-id="5424a-177">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="5424a-178">Tworzenie usługi systemu Windows i zarządzanie nią</span><span class="sxs-lookup"><span data-stu-id="5424a-178">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="5424a-179">Tworzenie usługi</span><span class="sxs-lookup"><span data-stu-id="5424a-179">Create a service</span></span>

<span data-ttu-id="5424a-180">Zarejestrowanie usługi za pomocą poleceń programu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="5424a-180">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="5424a-181">Z poziomu powłoki poleceń administracyjnych programu PowerShell 6 wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="5424a-181">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="5424a-182">`{EXE PATH}`: Ścieżka do folderu aplikacji na hoście (na przykład `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="5424a-182">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="5424a-183">Nie dołączaj pliku wykonywalnego aplikacji do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="5424a-183">Don't include the app's executable in the path.</span></span> <span data-ttu-id="5424a-184">Końcowy ukośnik nie jest wymagany.</span><span class="sxs-lookup"><span data-stu-id="5424a-184">A trailing slash isn't required.</span></span>
* <span data-ttu-id="5424a-185">`{DOMAIN OR COMPUTER NAME\USER}`: Konto użytkownika usługi (na przykład `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="5424a-185">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="5424a-186">`{SERVICE NAME}`: Nazwa usługi (na przykład `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="5424a-186">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="5424a-187">`{EXE FILE PATH}`: Ścieżka pliku wykonywalnego aplikacji (na przykład `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="5424a-187">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="5424a-188">Uwzględnij nazwę pliku wykonywalnego z rozszerzeniem.</span><span class="sxs-lookup"><span data-stu-id="5424a-188">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="5424a-189">`{DESCRIPTION}`: Opis usługi (na przykład `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="5424a-189">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="5424a-190">`{DISPLAY NAME}`: Nazwa wyświetlana usługi (na przykład `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="5424a-190">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="5424a-191">Uruchamianie usługi</span><span class="sxs-lookup"><span data-stu-id="5424a-191">Start a service</span></span>

<span data-ttu-id="5424a-192">Uruchom usługę za pomocą następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="5424a-192">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="5424a-193">Uruchomienie usługi może potrwać kilka sekund.</span><span class="sxs-lookup"><span data-stu-id="5424a-193">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="5424a-194">Określanie stanu usługi</span><span class="sxs-lookup"><span data-stu-id="5424a-194">Determine a service's status</span></span>

<span data-ttu-id="5424a-195">Aby sprawdzić stan usługi, użyj następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="5424a-195">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="5424a-196">Stan jest raportowany jako jedna z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="5424a-196">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="5424a-197">Zatrzymywanie usługi</span><span class="sxs-lookup"><span data-stu-id="5424a-197">Stop a service</span></span>

<span data-ttu-id="5424a-198">Zatrzymaj usługę za pomocą następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="5424a-198">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="5424a-199">Usuwanie usługi</span><span class="sxs-lookup"><span data-stu-id="5424a-199">Remove a service</span></span>

<span data-ttu-id="5424a-200">Po krótkim opóźnieniu na zatrzymanie usługi Usuń usługę za pomocą następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="5424a-200">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="5424a-201">Scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="5424a-201">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="5424a-202">Usługi, które współdziałają z żądaniami z Internetu lub sieci firmowej i znajdują się za serwerem proxy lub modułem równoważenia obciążenia, mogą wymagać dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="5424a-202">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="5424a-203">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="5424a-203">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="5424a-204">Konfigurowanie punktów końcowych</span><span class="sxs-lookup"><span data-stu-id="5424a-204">Configure endpoints</span></span>

<span data-ttu-id="5424a-205">Domyślnie ASP.NET Core wiąże się z `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="5424a-205">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="5424a-206">Skonfiguruj adres URL i port przez ustawienie `ASPNETCORE_URLS` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="5424a-206">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="5424a-207">Aby uzyskać dodatkowe podejścia do konfiguracji adresów URL i portów, zobacz artykuł dotyczący odpowiedniego serwera:</span><span class="sxs-lookup"><span data-stu-id="5424a-207">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="5424a-208">Powyższe wskazówki obejmują obsługę punktów końcowych HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5424a-208">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="5424a-209">Na przykład skonfiguruj aplikację do obsługi protokołu HTTPS, gdy uwierzytelnianie jest używane z usługą systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="5424a-209">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="5424a-210">Korzystanie z ASP.NET Core certyfikatu deweloperskiego HTTPS w celu zabezpieczenia punktu końcowego usługi nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="5424a-210">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="5424a-211">Bieżący katalog i główna Zawartość</span><span class="sxs-lookup"><span data-stu-id="5424a-211">Current directory and content root</span></span>

<span data-ttu-id="5424a-212">Bieżącym katalogiem roboczym zwróconym przez wywołanie <xref:System.IO.Directory.GetCurrentDirectory*> dla usługi systemu Windows jest folder *C: \\ Windows \\ system32* .</span><span class="sxs-lookup"><span data-stu-id="5424a-212">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="5424a-213">Folder *system32* nie jest odpowiednią lokalizacją do przechowywania plików usługi (na przykład plików ustawień).</span><span class="sxs-lookup"><span data-stu-id="5424a-213">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="5424a-214">Użyj jednego z poniższych metod, aby zachować i uzyskać dostęp do plików ustawień i zasobów usługi.</span><span class="sxs-lookup"><span data-stu-id="5424a-214">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="5424a-215">Użyj ContentRootPath lub ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="5424a-215">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="5424a-216">Użyj [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) lub <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> do lokalizowania zasobów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-216">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="5424a-217">Gdy aplikacja działa jako usługa, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> ustawia wartość <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> na [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="5424a-217">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="5424a-218">Domyślne pliki ustawień aplikacji, *appSettings. JSON* i *appSettings. { Environment}. JSON*jest ładowany z katalogu głównego zawartości aplikacji przez wywołanie [CreateDefaultBuilder podczas konstruowania hosta](xref:fundamentals/host/generic-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="5424a-218">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="5424a-219">W przypadku innych plików ustawień ładowanych przez kod dewelopera w <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> , nie ma potrzeby wywoływania <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> .</span><span class="sxs-lookup"><span data-stu-id="5424a-219">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="5424a-220">W poniższym przykładzie plik *custom_settings. JSON* znajduje się w katalogu głównym zawartości aplikacji i jest ładowany bez jawnego ustawiania ścieżki podstawowej:</span><span class="sxs-lookup"><span data-stu-id="5424a-220">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="5424a-221">Nie należy próbować użyć <xref:System.IO.Directory.GetCurrentDirectory*> programu w celu uzyskania ścieżki zasobu, ponieważ aplikacja usługi systemu Windows zwraca folder *C: \\ Windows \\ system32* jako bieżący katalog.</span><span class="sxs-lookup"><span data-stu-id="5424a-221">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="5424a-222">Przechowywanie plików usługi w odpowiedniej lokalizacji na dysku</span><span class="sxs-lookup"><span data-stu-id="5424a-222">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="5424a-223">Określ ścieżkę bezwzględną przy <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> użyciu <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do folderu zawierającego pliki.</span><span class="sxs-lookup"><span data-stu-id="5424a-223">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="5424a-224">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="5424a-224">Troubleshoot</span></span>

<span data-ttu-id="5424a-225">Aby rozwiązać problem z aplikacją usługi systemu Windows, zobacz <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="5424a-225">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="5424a-226">Typowe błędy</span><span class="sxs-lookup"><span data-stu-id="5424a-226">Common errors</span></span>

* <span data-ttu-id="5424a-227">Starsza lub wstępnie wydana wersja programu PowerShell jest używana.</span><span class="sxs-lookup"><span data-stu-id="5424a-227">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="5424a-228">Zarejestrowana usługa nie używa **opublikowanych** danych wyjściowych aplikacji z [dotnet Publish](/dotnet/core/tools/dotnet-publish) polecenia.</span><span class="sxs-lookup"><span data-stu-id="5424a-228">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="5424a-229">Dane wyjściowe polecenia [kompilacji dotnet](/dotnet/core/tools/dotnet-build) nie są obsługiwane w przypadku wdrażania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-229">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="5424a-230">Opublikowane zasoby znajdują się w dowolnym z następujących folderów w zależności od typu wdrożenia:</span><span class="sxs-lookup"><span data-stu-id="5424a-230">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="5424a-231">*bin/Release/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="5424a-231">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="5424a-232">*bin/Release/{Target Framework}/{Runtime identyfikator}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="5424a-232">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="5424a-233">Usługa nie jest w stanie uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="5424a-233">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="5424a-234">Ścieżki do zasobów używanych przez aplikację (na przykład certyfikaty) są nieprawidłowe.</span><span class="sxs-lookup"><span data-stu-id="5424a-234">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="5424a-235">Ścieżką podstawową usługi systemu Windows jest *c: \\ Windows \\ system32*.</span><span class="sxs-lookup"><span data-stu-id="5424a-235">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="5424a-236">Użytkownik nie ma uprawnień do *logowania się jako usługa* .</span><span class="sxs-lookup"><span data-stu-id="5424a-236">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="5424a-237">Hasło użytkownika wygasło lub zostało nieprawidłowo przesłane podczas wykonywania `New-Service` polecenia programu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="5424a-237">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="5424a-238">Aplikacja wymaga uwierzytelniania ASP.NET Core, ale nie jest skonfigurowana dla połączeń Secure (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="5424a-238">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="5424a-239">Port adresu URL żądania jest niepoprawny lub niepoprawnie skonfigurowany w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-239">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="5424a-240">Dzienniki zdarzeń systemu i aplikacji</span><span class="sxs-lookup"><span data-stu-id="5424a-240">System and Application Event Logs</span></span>

<span data-ttu-id="5424a-241">Uzyskaj dostęp do dzienników zdarzeń systemu i aplikacji:</span><span class="sxs-lookup"><span data-stu-id="5424a-241">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="5424a-242">Otwórz menu Start, wyszukaj ciąg *Podgląd zdarzeń*i wybierz aplikację **Podgląd zdarzeń** .</span><span class="sxs-lookup"><span data-stu-id="5424a-242">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="5424a-243">W **Podgląd zdarzeń**Otwórz węzeł **Dzienniki systemu Windows** .</span><span class="sxs-lookup"><span data-stu-id="5424a-243">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="5424a-244">Wybierz pozycję **system** , aby otworzyć dziennik zdarzeń systemu.</span><span class="sxs-lookup"><span data-stu-id="5424a-244">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="5424a-245">Wybierz pozycję **aplikacja** , aby otworzyć dziennik zdarzeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-245">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="5424a-246">Wyszukaj błędy związane z niepowodzeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-246">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="5424a-247">Uruchamianie aplikacji w wierszu polecenia</span><span class="sxs-lookup"><span data-stu-id="5424a-247">Run the app at a command prompt</span></span>

<span data-ttu-id="5424a-248">Wiele błędów uruchamiania nie produkuje użytecznych informacji w dziennikach zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="5424a-248">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="5424a-249">Przyczynę niektórych błędów można znaleźć, uruchamiając aplikację w wierszu polecenia w systemie hostingu.</span><span class="sxs-lookup"><span data-stu-id="5424a-249">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="5424a-250">Aby rejestrować dodatkowe szczegóły aplikacji, Obniż [poziom rejestrowania](xref:fundamentals/logging/index#log-level) lub Uruchom aplikację w [środowisku deweloperskim](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="5424a-250">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="5424a-251">Wyczyść pamięć podręczną pakietów</span><span class="sxs-lookup"><span data-stu-id="5424a-251">Clear package caches</span></span>

<span data-ttu-id="5424a-252">Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestaw .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w ramach aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-252">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="5424a-253">W niektórych przypadkach niespójne pakiety mogą przerwać aplikację podczas przeprowadzania uaktualnień głównych.</span><span class="sxs-lookup"><span data-stu-id="5424a-253">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="5424a-254">Większość tych problemów można naprawić, wykonując następujące instrukcje:</span><span class="sxs-lookup"><span data-stu-id="5424a-254">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="5424a-255">Usuń foldery *bin* i *obj* .</span><span class="sxs-lookup"><span data-stu-id="5424a-255">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="5424a-256">Wyczyść pamięć podręczną pakietów, wykonując [wszystkie elementy lokalne usługi NuGet programu dotnet--Wyczyść](/dotnet/core/tools/dotnet-nuget-locals) z poziomu powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="5424a-256">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="5424a-257">Czyszczenie pamięci podręcznych pakietów można także wykonać przy użyciu narzędzia [NuGet. exe](https://www.nuget.org/downloads) i wykonując polecenie `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="5424a-257">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="5424a-258">*NuGet. exe* nie jest pakietem instalowanym z systemem operacyjnym Windows dla komputerów stacjonarnych i musi być uzyskany niezależnie od [witryny sieci Web programu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="5424a-258">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="5424a-259">Przywróć i skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="5424a-259">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="5424a-260">Usuń wszystkie pliki z folderu wdrożenia na serwerze przed ponownym wdrożeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-260">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="5424a-261">Aplikacja wolna lub wysunięta</span><span class="sxs-lookup"><span data-stu-id="5424a-261">Slow or hanging app</span></span>

<span data-ttu-id="5424a-262">*Zrzut awaryjny* to migawka pamięci systemu, która może pomóc w ustaleniu przyczyny awarii aplikacji, awarii uruchamiania lub powolnej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-262">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="5424a-263">Awaria aplikacji lub napotka wyjątek</span><span class="sxs-lookup"><span data-stu-id="5424a-263">App crashes or encounters an exception</span></span>

<span data-ttu-id="5424a-264">Uzyskaj i Analizuj Zrzut z [raportowanie błędów systemu Windows (raportowanie błędów systemu Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="5424a-264">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="5424a-265">Utwórz folder do przechowywania plików zrzutu awaryjnego w `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="5424a-265">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="5424a-266">Uruchom [skrypt programu PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) w programie EnableDumps z nazwą pliku wykonywalnego aplikacji:</span><span class="sxs-lookup"><span data-stu-id="5424a-266">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="5424a-267">Uruchom aplikację w warunkach, które powodują awarię.</span><span class="sxs-lookup"><span data-stu-id="5424a-267">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="5424a-268">Po wystąpieniu awarii Uruchom [skrypt programu DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="5424a-268">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="5424a-269">Po awarii aplikacji i zakończeniu zbierania zrzutów aplikacja może zakończyć normalne działanie.</span><span class="sxs-lookup"><span data-stu-id="5424a-269">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="5424a-270">Skrypt programu PowerShell konfiguruje raportowanie błędów systemu Windows w celu zebrania do pięciu zrzutów na aplikację.</span><span class="sxs-lookup"><span data-stu-id="5424a-270">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="5424a-271">Zrzuty awaryjne mogą wymagać dużej ilości miejsca na dysku (do kilku gigabajtów).</span><span class="sxs-lookup"><span data-stu-id="5424a-271">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="5424a-272">Aplikacja zawiesza się, kończy się niepowodzeniem podczas uruchamiania lub działa normalnie</span><span class="sxs-lookup"><span data-stu-id="5424a-272">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="5424a-273">Gdy aplikacja *zawiesza* się (bez awarii), kończy się niepowodzeniem podczas uruchamiania lub działa normalnie, zobacz [pliki zrzutu w trybie użytkownika: wybór najlepszego narzędzia](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) do wygenerowania zrzutu.</span><span class="sxs-lookup"><span data-stu-id="5424a-273">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="5424a-274">Analizowanie zrzutu</span><span class="sxs-lookup"><span data-stu-id="5424a-274">Analyze the dump</span></span>

<span data-ttu-id="5424a-275">Zrzut można analizować przy użyciu kilku metod.</span><span class="sxs-lookup"><span data-stu-id="5424a-275">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="5424a-276">Aby uzyskać więcej informacji, zobacz [Analizowanie pliku zrzutu w trybie użytkownika](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="5424a-276">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5424a-277">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="5424a-277">Additional resources</span></span>

* <span data-ttu-id="5424a-278">[Konfiguracja punktu końcowego Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (w tym Konfiguracja protokołu HTTPS i obsługa SNI)</span><span class="sxs-lookup"><span data-stu-id="5424a-278">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="5424a-279">Aplikacja ASP.NET Core może być hostowana w systemie Windows jako [Usługa systemu Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez korzystania z usług IIS.</span><span class="sxs-lookup"><span data-stu-id="5424a-279">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="5424a-280">Gdy usługa jest hostowana w systemie Windows, aplikacja jest uruchamiana automatycznie po ponownym uruchomieniu serwera.</span><span class="sxs-lookup"><span data-stu-id="5424a-280">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="5424a-281">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5424a-281">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5424a-282">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="5424a-282">Prerequisites</span></span>

* [<span data-ttu-id="5424a-283">ASP.NET Core zestaw SDK 2,1 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="5424a-283">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="5424a-284">Program PowerShell 6,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="5424a-284">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="5424a-285">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="5424a-285">App configuration</span></span>

<span data-ttu-id="5424a-286">Aplikacja wymaga odwołania do pakietów dla elementu [Microsoft. AspNetCore. hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) i [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="5424a-286">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="5424a-287">Aby przetestować i debugować w przypadku uruchamiania poza usługą, należy dodać kod, aby określić, czy aplikacja działa jako usługa, czy Aplikacja konsolowa.</span><span class="sxs-lookup"><span data-stu-id="5424a-287">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="5424a-288">Sprawdź, czy debuger jest dołączony lub czy `--console` jest obecny przełącznik.</span><span class="sxs-lookup"><span data-stu-id="5424a-288">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="5424a-289">Jeśli którykolwiek z warunków jest spełniony (aplikacja nie jest uruchomiona jako usługa), wywołaj metodę <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> .</span><span class="sxs-lookup"><span data-stu-id="5424a-289">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="5424a-290">Jeśli warunki są fałszywe (aplikacja jest uruchamiana jako usługa):</span><span class="sxs-lookup"><span data-stu-id="5424a-290">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="5424a-291">Wywołaj <xref:System.IO.Directory.SetCurrentDirectory*> i użyj ścieżki do opublikowanej lokalizacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-291">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="5424a-292">Nie wywołuj <xref:System.IO.Directory.GetCurrentDirectory*> , aby uzyskać ścieżkę, ponieważ aplikacja usługi systemu Windows zwraca folder *C: \\ Windows \\ system32* , gdy <xref:System.IO.Directory.GetCurrentDirectory*> jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="5424a-292">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="5424a-293">Aby uzyskać więcej informacji, zapoznaj się z sekcją [Current Directory i root Content](#current-directory-and-content-root) .</span><span class="sxs-lookup"><span data-stu-id="5424a-293">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="5424a-294">Ten krok jest wykonywany przed skonfigurowaniem aplikacji w programie `CreateWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="5424a-294">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="5424a-295">Wywołaj <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> , aby uruchomić aplikację jako usługę.</span><span class="sxs-lookup"><span data-stu-id="5424a-295">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="5424a-296">Ponieważ [dostawca konfiguracji wiersza polecenia](xref:fundamentals/configuration/index#command-line-configuration-provider) wymaga par nazwa-wartość dla argumentów wiersza polecenia, `--console` przełącznik jest usuwany z argumentów przed <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> odebraniem argumentów.</span><span class="sxs-lookup"><span data-stu-id="5424a-296">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="5424a-297">Aby zapisać w dzienniku zdarzeń systemu Windows, Dodaj dostawcę EventLog do programu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> .</span><span class="sxs-lookup"><span data-stu-id="5424a-297">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="5424a-298">Ustaw poziom rejestrowania przy użyciu `Logging:LogLevel:Default` klucza w pliku *appSettings. Plik Product. JSON* .</span><span class="sxs-lookup"><span data-stu-id="5424a-298">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="5424a-299">W poniższym przykładzie z przykładowej aplikacji `RunAsCustomService` jest wywoływana zamiast <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> w celu obsługi zdarzeń okresu istnienia w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-299">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="5424a-300">Aby uzyskać więcej informacji, zobacz sekcję [Obsługa zdarzeń dotyczących uruchamiania i zatrzymywania](#handle-starting-and-stopping-events) .</span><span class="sxs-lookup"><span data-stu-id="5424a-300">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="5424a-301">Typ wdrożenia</span><span class="sxs-lookup"><span data-stu-id="5424a-301">Deployment type</span></span>

<span data-ttu-id="5424a-302">Aby uzyskać informacje i porady dotyczące scenariuszy wdrażania, zobacz [wdrażanie aplikacji .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="5424a-302">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="5424a-303">SDK</span><span class="sxs-lookup"><span data-stu-id="5424a-303">SDK</span></span>

<span data-ttu-id="5424a-304">W przypadku usługi opartej na aplikacji sieci Web korzystającej ze Razor stron lub platform MVC należy określić zestaw SDK sieci Web w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="5424a-304">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="5424a-305">Jeśli usługa wykonuje tylko zadania w tle (na przykład [usługi hostowane](xref:fundamentals/host/hosted-services)), określ zestaw SDK procesu roboczego w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="5424a-305">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="5424a-306">Wdrożenie zależne od platformy (FDD)</span><span class="sxs-lookup"><span data-stu-id="5424a-306">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="5424a-307">Wdrożenie zależne od platformy (FDD) zależy od obecności udostępnionej systemowej wersji platformy .NET Core w systemie docelowym.</span><span class="sxs-lookup"><span data-stu-id="5424a-307">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="5424a-308">Po przyjęciu scenariusza FDD zgodnie ze wskazówkami zawartymi w tym artykule zestaw SDK tworzy plik wykonywalny (*. exe*), nazywany *plik wykonywalny zależny od platformy*.</span><span class="sxs-lookup"><span data-stu-id="5424a-308">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="5424a-309">[Identyfikator środowiska uruchomieniowego systemu Windows (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) zawiera platformę docelową.</span><span class="sxs-lookup"><span data-stu-id="5424a-309">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="5424a-310">W poniższym przykładzie identyfikator RID jest ustawiony na `win7-x64` .</span><span class="sxs-lookup"><span data-stu-id="5424a-310">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="5424a-311">`<SelfContained>`Właściwość jest ustawiona na `false` .</span><span class="sxs-lookup"><span data-stu-id="5424a-311">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="5424a-312">Te właściwości instruują zestaw SDK, aby wygenerował plik wykonywalny (*exe*) dla systemu Windows i aplikację, która zależy od współużytkowanej platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="5424a-312">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="5424a-313">Plik *Web. config* , który jest zwykle tworzony podczas publikowania aplikacji ASP.NET Core, nie jest konieczny dla aplikacji usług systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="5424a-313">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="5424a-314">Aby wyłączyć tworzenie pliku *Web. config* , należy dodać `<IsTransformWebConfigDisabled>` Właściwość ustawioną na `true` .</span><span class="sxs-lookup"><span data-stu-id="5424a-314">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="5424a-315">Wdrażanie samodzielne (SCD)</span><span class="sxs-lookup"><span data-stu-id="5424a-315">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="5424a-316">Wdrożenie samodzielne (SCD) nie polega na obecności struktury udostępnionej w systemie hosta.</span><span class="sxs-lookup"><span data-stu-id="5424a-316">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="5424a-317">Środowisko uruchomieniowe i zależności aplikacji są wdrażane przy użyciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-317">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="5424a-318">[Identyfikator środowiska uruchomieniowego systemu Windows (RID)](/dotnet/core/rid-catalog) znajduje się w, `<PropertyGroup>` który zawiera platformę docelową:</span><span class="sxs-lookup"><span data-stu-id="5424a-318">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="5424a-319">Aby opublikować dla wielu identyfikatorów RID:</span><span class="sxs-lookup"><span data-stu-id="5424a-319">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="5424a-320">Podaj identyfikatory RID na liście rozdzielanej średnikami.</span><span class="sxs-lookup"><span data-stu-id="5424a-320">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="5424a-321">Użyj nazwy właściwości [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="5424a-321">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="5424a-322">Aby uzyskać więcej informacji, zobacz [katalog .NET Core RID Catalog](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="5424a-322">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="5424a-323">`<SelfContained>`Właściwość jest ustawiona na `true` :</span><span class="sxs-lookup"><span data-stu-id="5424a-323">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="5424a-324">Konto użytkownika usługi</span><span class="sxs-lookup"><span data-stu-id="5424a-324">Service user account</span></span>

<span data-ttu-id="5424a-325">Aby utworzyć konto użytkownika dla usługi, należy użyć polecenia cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) w powłoce poleceń administracyjnych programu PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="5424a-325">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="5424a-326">W systemie Windows 10 października 2018 Update (wersja 1809/Kompilacja 10.0.17763) lub nowsza:</span><span class="sxs-lookup"><span data-stu-id="5424a-326">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="5424a-327">W systemie operacyjnym Windows w wersji starszej niż aktualizacja 2018 systemu Windows 10 października (wersja 1809/Kompilacja 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="5424a-327">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="5424a-328">Podaj [silne hasło](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) po wyświetleniu monitu.</span><span class="sxs-lookup"><span data-stu-id="5424a-328">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="5424a-329">Jeśli `-AccountExpires` parametr nie zostanie dostarczony do polecenia cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z wygaśnięciem <xref:System.DateTime> , konto nie wygaśnie.</span><span class="sxs-lookup"><span data-stu-id="5424a-329">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="5424a-330">Aby uzyskać więcej informacji, zobacz [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) i [konta użytkowników usług](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="5424a-330">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="5424a-331">Alternatywna metoda zarządzania użytkownikami podczas korzystania z Active Directory polega na użyciu zarządzanych kont usług.</span><span class="sxs-lookup"><span data-stu-id="5424a-331">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="5424a-332">Aby uzyskać więcej informacji, zobacz [Omówienie kont usług zarządzanych przez grupę](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="5424a-332">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="5424a-333">Logowanie jako prawa usługi</span><span class="sxs-lookup"><span data-stu-id="5424a-333">Log on as a service rights</span></span>

<span data-ttu-id="5424a-334">Aby nawiązać *Logowanie jako prawa usługi* dla konta użytkownika usługi:</span><span class="sxs-lookup"><span data-stu-id="5424a-334">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="5424a-335">Otwórz Edytor lokalnych zasad zabezpieczeń, uruchamiając program *secpol. msc*.</span><span class="sxs-lookup"><span data-stu-id="5424a-335">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="5424a-336">Rozwiń węzeł **Zasady lokalne** , a następnie wybierz pozycję **Przypisywanie praw użytkownika**.</span><span class="sxs-lookup"><span data-stu-id="5424a-336">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="5424a-337">Otwórz okno **Logowanie jako usługa** .</span><span class="sxs-lookup"><span data-stu-id="5424a-337">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="5424a-338">Wybierz pozycję **Dodaj użytkownika lub grupę**.</span><span class="sxs-lookup"><span data-stu-id="5424a-338">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="5424a-339">Podaj nazwę obiektu (konto użytkownika) przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="5424a-339">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="5424a-340">Wpisz konto użytkownika ( `{DOMAIN OR COMPUTER NAME\USER}` ) w polu Nazwa obiektu, a następnie wybierz **przycisk OK** , aby dodać użytkownika do zasad.</span><span class="sxs-lookup"><span data-stu-id="5424a-340">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="5424a-341">Wybierz pozycję **Zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="5424a-341">Select **Advanced**.</span></span> <span data-ttu-id="5424a-342">Wybierz pozycję **Znajdź teraz**.</span><span class="sxs-lookup"><span data-stu-id="5424a-342">Select **Find Now**.</span></span> <span data-ttu-id="5424a-343">Wybierz z listy konto użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5424a-343">Select the user account from the list.</span></span> <span data-ttu-id="5424a-344">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="5424a-344">Select **OK**.</span></span> <span data-ttu-id="5424a-345">Ponownie wybierz **przycisk OK** , aby dodać użytkownika do zasad.</span><span class="sxs-lookup"><span data-stu-id="5424a-345">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="5424a-346">Wybierz **przycisk OK** lub **Zastosuj** , aby zaakceptować zmiany.</span><span class="sxs-lookup"><span data-stu-id="5424a-346">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="5424a-347">Tworzenie usługi systemu Windows i zarządzanie nią</span><span class="sxs-lookup"><span data-stu-id="5424a-347">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="5424a-348">Tworzenie usługi</span><span class="sxs-lookup"><span data-stu-id="5424a-348">Create a service</span></span>

<span data-ttu-id="5424a-349">Zarejestrowanie usługi za pomocą poleceń programu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="5424a-349">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="5424a-350">Z poziomu powłoki poleceń administracyjnych programu PowerShell 6 wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="5424a-350">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="5424a-351">`{EXE PATH}`: Ścieżka do folderu aplikacji na hoście (na przykład `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="5424a-351">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="5424a-352">Nie dołączaj pliku wykonywalnego aplikacji do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="5424a-352">Don't include the app's executable in the path.</span></span> <span data-ttu-id="5424a-353">Końcowy ukośnik nie jest wymagany.</span><span class="sxs-lookup"><span data-stu-id="5424a-353">A trailing slash isn't required.</span></span>
* <span data-ttu-id="5424a-354">`{DOMAIN OR COMPUTER NAME\USER}`: Konto użytkownika usługi (na przykład `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="5424a-354">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="5424a-355">`{SERVICE NAME}`: Nazwa usługi (na przykład `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="5424a-355">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="5424a-356">`{EXE FILE PATH}`: Ścieżka pliku wykonywalnego aplikacji (na przykład `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="5424a-356">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="5424a-357">Uwzględnij nazwę pliku wykonywalnego z rozszerzeniem.</span><span class="sxs-lookup"><span data-stu-id="5424a-357">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="5424a-358">`{DESCRIPTION}`: Opis usługi (na przykład `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="5424a-358">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="5424a-359">`{DISPLAY NAME}`: Nazwa wyświetlana usługi (na przykład `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="5424a-359">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="5424a-360">Uruchamianie usługi</span><span class="sxs-lookup"><span data-stu-id="5424a-360">Start a service</span></span>

<span data-ttu-id="5424a-361">Uruchom usługę za pomocą następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="5424a-361">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="5424a-362">Uruchomienie usługi może potrwać kilka sekund.</span><span class="sxs-lookup"><span data-stu-id="5424a-362">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="5424a-363">Określanie stanu usługi</span><span class="sxs-lookup"><span data-stu-id="5424a-363">Determine a service's status</span></span>

<span data-ttu-id="5424a-364">Aby sprawdzić stan usługi, użyj następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="5424a-364">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="5424a-365">Stan jest raportowany jako jedna z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="5424a-365">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="5424a-366">Zatrzymywanie usługi</span><span class="sxs-lookup"><span data-stu-id="5424a-366">Stop a service</span></span>

<span data-ttu-id="5424a-367">Zatrzymaj usługę za pomocą następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="5424a-367">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="5424a-368">Usuwanie usługi</span><span class="sxs-lookup"><span data-stu-id="5424a-368">Remove a service</span></span>

<span data-ttu-id="5424a-369">Po krótkim opóźnieniu na zatrzymanie usługi Usuń usługę za pomocą następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="5424a-369">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="5424a-370">Obsługa zdarzeń uruchamiania i zatrzymywania</span><span class="sxs-lookup"><span data-stu-id="5424a-370">Handle starting and stopping events</span></span>

<span data-ttu-id="5424a-371">Aby obsłużyć <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*> <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> zdarzenia, i <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> :</span><span class="sxs-lookup"><span data-stu-id="5424a-371">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="5424a-372">Utwórz klasę, która dziedziczy z <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> `OnStarting` `OnStarted` metody,, i `OnStopping` :</span><span class="sxs-lookup"><span data-stu-id="5424a-372">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="5424a-373">Utwórz metodę rozszerzenia dla <xref:Microsoft.AspNetCore.Hosting.IWebHost> , która przekazuje `CustomWebHostService` do <xref:System.ServiceProcess.ServiceBase.Run*> :</span><span class="sxs-lookup"><span data-stu-id="5424a-373">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="5424a-374">W `Program.Main` , wywołaj `RunAsCustomService` metodę rozszerzenia zamiast <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :</span><span class="sxs-lookup"><span data-stu-id="5424a-374">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="5424a-375">Aby zobaczyć lokalizację programu <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> w programie `Program.Main` , zapoznaj się z przykładem kodu pokazanym w sekcji [typ wdrożenia](#deployment-type) .</span><span class="sxs-lookup"><span data-stu-id="5424a-375">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="5424a-376">Scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="5424a-376">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="5424a-377">Usługi, które współdziałają z żądaniami z Internetu lub sieci firmowej i znajdują się za serwerem proxy lub modułem równoważenia obciążenia, mogą wymagać dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="5424a-377">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="5424a-378">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="5424a-378">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="5424a-379">Konfigurowanie punktów końcowych</span><span class="sxs-lookup"><span data-stu-id="5424a-379">Configure endpoints</span></span>

<span data-ttu-id="5424a-380">Domyślnie ASP.NET Core wiąże się z `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="5424a-380">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="5424a-381">Skonfiguruj adres URL i port przez ustawienie `ASPNETCORE_URLS` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="5424a-381">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="5424a-382">Aby uzyskać dodatkowe podejścia do konfiguracji adresów URL i portów, zobacz artykuł dotyczący odpowiedniego serwera:</span><span class="sxs-lookup"><span data-stu-id="5424a-382">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="5424a-383">Powyższe wskazówki obejmują obsługę punktów końcowych HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5424a-383">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="5424a-384">Na przykład skonfiguruj aplikację do obsługi protokołu HTTPS, gdy uwierzytelnianie jest używane z usługą systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="5424a-384">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="5424a-385">Korzystanie z ASP.NET Core certyfikatu deweloperskiego HTTPS w celu zabezpieczenia punktu końcowego usługi nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="5424a-385">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="5424a-386">Bieżący katalog i główna Zawartość</span><span class="sxs-lookup"><span data-stu-id="5424a-386">Current directory and content root</span></span>

<span data-ttu-id="5424a-387">Bieżącym katalogiem roboczym zwróconym przez wywołanie <xref:System.IO.Directory.GetCurrentDirectory*> dla usługi systemu Windows jest folder *C: \\ Windows \\ system32* .</span><span class="sxs-lookup"><span data-stu-id="5424a-387">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="5424a-388">Folder *system32* nie jest odpowiednią lokalizacją do przechowywania plików usługi (na przykład plików ustawień).</span><span class="sxs-lookup"><span data-stu-id="5424a-388">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="5424a-389">Użyj jednego z poniższych metod, aby zachować i uzyskać dostęp do plików ustawień i zasobów usługi.</span><span class="sxs-lookup"><span data-stu-id="5424a-389">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="5424a-390">Ustawianie ścieżki katalogu głównego zawartości do folderu aplikacji</span><span class="sxs-lookup"><span data-stu-id="5424a-390">Set the content root path to the app's folder</span></span>

<span data-ttu-id="5424a-391">Jest to ta <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> sama ścieżka do `binPath` argumentu podczas tworzenia usługi.</span><span class="sxs-lookup"><span data-stu-id="5424a-391">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="5424a-392">Zamiast wywoływania funkcji `GetCurrentDirectory` tworzenia ścieżek do plików ustawień, należy wywołać <xref:System.IO.Directory.SetCurrentDirectory*> ścieżkę do [katalogu głównego zawartości](xref:fundamentals/index#content-root)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-392">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="5424a-393">W programie `Program.Main` Określ ścieżkę do folderu wykonywalnego usługi i użyj ścieżki, aby określić katalog główny zawartości aplikacji:</span><span class="sxs-lookup"><span data-stu-id="5424a-393">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="5424a-394">Przechowywanie plików usługi w odpowiedniej lokalizacji na dysku</span><span class="sxs-lookup"><span data-stu-id="5424a-394">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="5424a-395">Określ ścieżkę bezwzględną przy <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> użyciu <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do folderu zawierającego pliki.</span><span class="sxs-lookup"><span data-stu-id="5424a-395">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="5424a-396">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="5424a-396">Troubleshoot</span></span>

<span data-ttu-id="5424a-397">Aby rozwiązać problem z aplikacją usługi systemu Windows, zobacz <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="5424a-397">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="5424a-398">Typowe błędy</span><span class="sxs-lookup"><span data-stu-id="5424a-398">Common errors</span></span>

* <span data-ttu-id="5424a-399">Starsza lub wstępnie wydana wersja programu PowerShell jest używana.</span><span class="sxs-lookup"><span data-stu-id="5424a-399">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="5424a-400">Zarejestrowana usługa nie używa **opublikowanych** danych wyjściowych aplikacji z [dotnet Publish](/dotnet/core/tools/dotnet-publish) polecenia.</span><span class="sxs-lookup"><span data-stu-id="5424a-400">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="5424a-401">Dane wyjściowe polecenia [kompilacji dotnet](/dotnet/core/tools/dotnet-build) nie są obsługiwane w przypadku wdrażania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-401">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="5424a-402">Opublikowane zasoby znajdują się w dowolnym z następujących folderów w zależności od typu wdrożenia:</span><span class="sxs-lookup"><span data-stu-id="5424a-402">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="5424a-403">*bin/Release/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="5424a-403">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="5424a-404">*bin/Release/{Target Framework}/{Runtime identyfikator}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="5424a-404">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="5424a-405">Usługa nie jest w stanie uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="5424a-405">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="5424a-406">Ścieżki do zasobów używanych przez aplikację (na przykład certyfikaty) są nieprawidłowe.</span><span class="sxs-lookup"><span data-stu-id="5424a-406">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="5424a-407">Ścieżką podstawową usługi systemu Windows jest *c: \\ Windows \\ system32*.</span><span class="sxs-lookup"><span data-stu-id="5424a-407">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="5424a-408">Użytkownik nie ma uprawnień do *logowania się jako usługa* .</span><span class="sxs-lookup"><span data-stu-id="5424a-408">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="5424a-409">Hasło użytkownika wygasło lub zostało nieprawidłowo przesłane podczas wykonywania `New-Service` polecenia programu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="5424a-409">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="5424a-410">Aplikacja wymaga uwierzytelniania ASP.NET Core, ale nie jest skonfigurowana dla połączeń Secure (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="5424a-410">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="5424a-411">Port adresu URL żądania jest niepoprawny lub niepoprawnie skonfigurowany w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-411">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="5424a-412">Dzienniki zdarzeń systemu i aplikacji</span><span class="sxs-lookup"><span data-stu-id="5424a-412">System and Application Event Logs</span></span>

<span data-ttu-id="5424a-413">Uzyskaj dostęp do dzienników zdarzeń systemu i aplikacji:</span><span class="sxs-lookup"><span data-stu-id="5424a-413">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="5424a-414">Otwórz menu Start, wyszukaj ciąg *Podgląd zdarzeń*i wybierz aplikację **Podgląd zdarzeń** .</span><span class="sxs-lookup"><span data-stu-id="5424a-414">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="5424a-415">W **Podgląd zdarzeń**Otwórz węzeł **Dzienniki systemu Windows** .</span><span class="sxs-lookup"><span data-stu-id="5424a-415">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="5424a-416">Wybierz pozycję **system** , aby otworzyć dziennik zdarzeń systemu.</span><span class="sxs-lookup"><span data-stu-id="5424a-416">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="5424a-417">Wybierz pozycję **aplikacja** , aby otworzyć dziennik zdarzeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-417">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="5424a-418">Wyszukaj błędy związane z niepowodzeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-418">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="5424a-419">Uruchamianie aplikacji w wierszu polecenia</span><span class="sxs-lookup"><span data-stu-id="5424a-419">Run the app at a command prompt</span></span>

<span data-ttu-id="5424a-420">Wiele błędów uruchamiania nie produkuje użytecznych informacji w dziennikach zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="5424a-420">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="5424a-421">Przyczynę niektórych błędów można znaleźć, uruchamiając aplikację w wierszu polecenia w systemie hostingu.</span><span class="sxs-lookup"><span data-stu-id="5424a-421">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="5424a-422">Aby rejestrować dodatkowe szczegóły aplikacji, Obniż [poziom rejestrowania](xref:fundamentals/logging/index#log-level) lub Uruchom aplikację w [środowisku deweloperskim](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="5424a-422">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="5424a-423">Wyczyść pamięć podręczną pakietów</span><span class="sxs-lookup"><span data-stu-id="5424a-423">Clear package caches</span></span>

<span data-ttu-id="5424a-424">Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestaw .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w ramach aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-424">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="5424a-425">W niektórych przypadkach niespójne pakiety mogą przerwać aplikację podczas przeprowadzania uaktualnień głównych.</span><span class="sxs-lookup"><span data-stu-id="5424a-425">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="5424a-426">Większość tych problemów można naprawić, wykonując następujące instrukcje:</span><span class="sxs-lookup"><span data-stu-id="5424a-426">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="5424a-427">Usuń foldery *bin* i *obj* .</span><span class="sxs-lookup"><span data-stu-id="5424a-427">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="5424a-428">Wyczyść pamięć podręczną pakietów, wykonując [wszystkie elementy lokalne usługi NuGet programu dotnet--Wyczyść](/dotnet/core/tools/dotnet-nuget-locals) z poziomu powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="5424a-428">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="5424a-429">Czyszczenie pamięci podręcznych pakietów można także wykonać przy użyciu narzędzia [NuGet. exe](https://www.nuget.org/downloads) i wykonując polecenie `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="5424a-429">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="5424a-430">*NuGet. exe* nie jest pakietem instalowanym z systemem operacyjnym Windows dla komputerów stacjonarnych i musi być uzyskany niezależnie od [witryny sieci Web programu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="5424a-430">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="5424a-431">Przywróć i skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="5424a-431">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="5424a-432">Usuń wszystkie pliki z folderu wdrożenia na serwerze przed ponownym wdrożeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-432">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="5424a-433">Aplikacja wolna lub wysunięta</span><span class="sxs-lookup"><span data-stu-id="5424a-433">Slow or hanging app</span></span>

<span data-ttu-id="5424a-434">*Zrzut awaryjny* to migawka pamięci systemu, która może pomóc w ustaleniu przyczyny awarii aplikacji, awarii uruchamiania lub powolnej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-434">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="5424a-435">Awaria aplikacji lub napotka wyjątek</span><span class="sxs-lookup"><span data-stu-id="5424a-435">App crashes or encounters an exception</span></span>

<span data-ttu-id="5424a-436">Uzyskaj i Analizuj Zrzut z [raportowanie błędów systemu Windows (raportowanie błędów systemu Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="5424a-436">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="5424a-437">Utwórz folder do przechowywania plików zrzutu awaryjnego w `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="5424a-437">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="5424a-438">Uruchom [skrypt programu PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) w programie EnableDumps z nazwą pliku wykonywalnego aplikacji:</span><span class="sxs-lookup"><span data-stu-id="5424a-438">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="5424a-439">Uruchom aplikację w warunkach, które powodują awarię.</span><span class="sxs-lookup"><span data-stu-id="5424a-439">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="5424a-440">Po wystąpieniu awarii Uruchom [skrypt programu DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="5424a-440">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="5424a-441">Po awarii aplikacji i zakończeniu zbierania zrzutów aplikacja może zakończyć normalne działanie.</span><span class="sxs-lookup"><span data-stu-id="5424a-441">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="5424a-442">Skrypt programu PowerShell konfiguruje raportowanie błędów systemu Windows w celu zebrania do pięciu zrzutów na aplikację.</span><span class="sxs-lookup"><span data-stu-id="5424a-442">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="5424a-443">Zrzuty awaryjne mogą wymagać dużej ilości miejsca na dysku (do kilku gigabajtów).</span><span class="sxs-lookup"><span data-stu-id="5424a-443">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="5424a-444">Aplikacja zawiesza się, kończy się niepowodzeniem podczas uruchamiania lub działa normalnie</span><span class="sxs-lookup"><span data-stu-id="5424a-444">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="5424a-445">Gdy aplikacja *zawiesza* się (bez awarii), kończy się niepowodzeniem podczas uruchamiania lub działa normalnie, zobacz [pliki zrzutu w trybie użytkownika: wybór najlepszego narzędzia](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) do wygenerowania zrzutu.</span><span class="sxs-lookup"><span data-stu-id="5424a-445">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="5424a-446">Analizowanie zrzutu</span><span class="sxs-lookup"><span data-stu-id="5424a-446">Analyze the dump</span></span>

<span data-ttu-id="5424a-447">Zrzut można analizować przy użyciu kilku metod.</span><span class="sxs-lookup"><span data-stu-id="5424a-447">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="5424a-448">Aby uzyskać więcej informacji, zobacz [Analizowanie pliku zrzutu w trybie użytkownika](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="5424a-448">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5424a-449">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="5424a-449">Additional resources</span></span>

* <span data-ttu-id="5424a-450">[Konfiguracja punktu końcowego Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (w tym Konfiguracja protokołu HTTPS i obsługa SNI)</span><span class="sxs-lookup"><span data-stu-id="5424a-450">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="5424a-451">Aplikacja ASP.NET Core może być hostowana w systemie Windows jako [Usługa systemu Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez korzystania z usług IIS.</span><span class="sxs-lookup"><span data-stu-id="5424a-451">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="5424a-452">Gdy usługa jest hostowana w systemie Windows, aplikacja jest uruchamiana automatycznie po ponownym uruchomieniu serwera.</span><span class="sxs-lookup"><span data-stu-id="5424a-452">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="5424a-453">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5424a-453">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5424a-454">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="5424a-454">Prerequisites</span></span>

* [<span data-ttu-id="5424a-455">ASP.NET Core zestaw SDK 2,1 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="5424a-455">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="5424a-456">Program PowerShell 6,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="5424a-456">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="5424a-457">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="5424a-457">App configuration</span></span>

<span data-ttu-id="5424a-458">Aplikacja wymaga odwołania do pakietów dla elementu [Microsoft. AspNetCore. hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) i [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="5424a-458">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="5424a-459">Aby przetestować i debugować w przypadku uruchamiania poza usługą, należy dodać kod, aby określić, czy aplikacja działa jako usługa, czy Aplikacja konsolowa.</span><span class="sxs-lookup"><span data-stu-id="5424a-459">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="5424a-460">Sprawdź, czy debuger jest dołączony lub czy `--console` jest obecny przełącznik.</span><span class="sxs-lookup"><span data-stu-id="5424a-460">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="5424a-461">Jeśli którykolwiek z warunków jest spełniony (aplikacja nie jest uruchomiona jako usługa), wywołaj metodę <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> .</span><span class="sxs-lookup"><span data-stu-id="5424a-461">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="5424a-462">Jeśli warunki są fałszywe (aplikacja jest uruchamiana jako usługa):</span><span class="sxs-lookup"><span data-stu-id="5424a-462">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="5424a-463">Wywołaj <xref:System.IO.Directory.SetCurrentDirectory*> i użyj ścieżki do opublikowanej lokalizacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-463">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="5424a-464">Nie wywołuj <xref:System.IO.Directory.GetCurrentDirectory*> , aby uzyskać ścieżkę, ponieważ aplikacja usługi systemu Windows zwraca folder *C: \\ Windows \\ system32* , gdy <xref:System.IO.Directory.GetCurrentDirectory*> jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="5424a-464">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="5424a-465">Aby uzyskać więcej informacji, zapoznaj się z sekcją [Current Directory i root Content](#current-directory-and-content-root) .</span><span class="sxs-lookup"><span data-stu-id="5424a-465">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="5424a-466">Ten krok jest wykonywany przed skonfigurowaniem aplikacji w programie `CreateWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="5424a-466">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="5424a-467">Wywołaj <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> , aby uruchomić aplikację jako usługę.</span><span class="sxs-lookup"><span data-stu-id="5424a-467">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="5424a-468">Ponieważ [dostawca konfiguracji wiersza polecenia](xref:fundamentals/configuration/index#command-line-configuration-provider) wymaga par nazwa-wartość dla argumentów wiersza polecenia, `--console` przełącznik jest usuwany z argumentów przed <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> odebraniem argumentów.</span><span class="sxs-lookup"><span data-stu-id="5424a-468">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="5424a-469">Aby zapisać w dzienniku zdarzeń systemu Windows, Dodaj dostawcę EventLog do programu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> .</span><span class="sxs-lookup"><span data-stu-id="5424a-469">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="5424a-470">Ustaw poziom rejestrowania przy użyciu `Logging:LogLevel:Default` klucza w pliku *appSettings. Plik Product. JSON* .</span><span class="sxs-lookup"><span data-stu-id="5424a-470">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="5424a-471">W poniższym przykładzie z przykładowej aplikacji `RunAsCustomService` jest wywoływana zamiast <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> w celu obsługi zdarzeń okresu istnienia w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-471">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="5424a-472">Aby uzyskać więcej informacji, zobacz sekcję [Obsługa zdarzeń dotyczących uruchamiania i zatrzymywania](#handle-starting-and-stopping-events) .</span><span class="sxs-lookup"><span data-stu-id="5424a-472">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="5424a-473">Typ wdrożenia</span><span class="sxs-lookup"><span data-stu-id="5424a-473">Deployment type</span></span>

<span data-ttu-id="5424a-474">Aby uzyskać informacje i porady dotyczące scenariuszy wdrażania, zobacz [wdrażanie aplikacji .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="5424a-474">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="5424a-475">SDK</span><span class="sxs-lookup"><span data-stu-id="5424a-475">SDK</span></span>

<span data-ttu-id="5424a-476">W przypadku usługi opartej na aplikacji sieci Web korzystającej ze Razor stron lub platform MVC należy określić zestaw SDK sieci Web w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="5424a-476">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="5424a-477">Jeśli usługa wykonuje tylko zadania w tle (na przykład [usługi hostowane](xref:fundamentals/host/hosted-services)), określ zestaw SDK procesu roboczego w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="5424a-477">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="5424a-478">Wdrożenie zależne od platformy (FDD)</span><span class="sxs-lookup"><span data-stu-id="5424a-478">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="5424a-479">Wdrożenie zależne od platformy (FDD) zależy od obecności udostępnionej systemowej wersji platformy .NET Core w systemie docelowym.</span><span class="sxs-lookup"><span data-stu-id="5424a-479">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="5424a-480">Po przyjęciu scenariusza FDD zgodnie ze wskazówkami zawartymi w tym artykule zestaw SDK tworzy plik wykonywalny (*. exe*), nazywany *plik wykonywalny zależny od platformy*.</span><span class="sxs-lookup"><span data-stu-id="5424a-480">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="5424a-481">[Identyfikator środowiska uruchomieniowego systemu Windows (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) zawiera platformę docelową.</span><span class="sxs-lookup"><span data-stu-id="5424a-481">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="5424a-482">W poniższym przykładzie identyfikator RID jest ustawiony na `win7-x64` .</span><span class="sxs-lookup"><span data-stu-id="5424a-482">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="5424a-483">`<SelfContained>`Właściwość jest ustawiona na `false` .</span><span class="sxs-lookup"><span data-stu-id="5424a-483">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="5424a-484">Te właściwości instruują zestaw SDK, aby wygenerował plik wykonywalny (*exe*) dla systemu Windows i aplikację, która zależy od współużytkowanej platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="5424a-484">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="5424a-485">`<UseAppHost>`Właściwość jest ustawiona na `true` .</span><span class="sxs-lookup"><span data-stu-id="5424a-485">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="5424a-486">Ta właściwość zapewnia usługę ze ścieżką aktywacji (plik wykonywalny, *exe*) dla FDD.</span><span class="sxs-lookup"><span data-stu-id="5424a-486">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="5424a-487">Plik *Web. config* , który jest zwykle tworzony podczas publikowania aplikacji ASP.NET Core, nie jest konieczny dla aplikacji usług systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="5424a-487">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="5424a-488">Aby wyłączyć tworzenie pliku *Web. config* , należy dodać `<IsTransformWebConfigDisabled>` Właściwość ustawioną na `true` .</span><span class="sxs-lookup"><span data-stu-id="5424a-488">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="5424a-489">Wdrażanie samodzielne (SCD)</span><span class="sxs-lookup"><span data-stu-id="5424a-489">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="5424a-490">Wdrożenie samodzielne (SCD) nie polega na obecności struktury udostępnionej w systemie hosta.</span><span class="sxs-lookup"><span data-stu-id="5424a-490">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="5424a-491">Środowisko uruchomieniowe i zależności aplikacji są wdrażane przy użyciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-491">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="5424a-492">[Identyfikator środowiska uruchomieniowego systemu Windows (RID)](/dotnet/core/rid-catalog) znajduje się w, `<PropertyGroup>` który zawiera platformę docelową:</span><span class="sxs-lookup"><span data-stu-id="5424a-492">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="5424a-493">Aby opublikować dla wielu identyfikatorów RID:</span><span class="sxs-lookup"><span data-stu-id="5424a-493">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="5424a-494">Podaj identyfikatory RID na liście rozdzielanej średnikami.</span><span class="sxs-lookup"><span data-stu-id="5424a-494">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="5424a-495">Użyj nazwy właściwości [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="5424a-495">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="5424a-496">Aby uzyskać więcej informacji, zobacz [katalog .NET Core RID Catalog](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="5424a-496">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="5424a-497">`<SelfContained>`Właściwość jest ustawiona na `true` :</span><span class="sxs-lookup"><span data-stu-id="5424a-497">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="5424a-498">Konto użytkownika usługi</span><span class="sxs-lookup"><span data-stu-id="5424a-498">Service user account</span></span>

<span data-ttu-id="5424a-499">Aby utworzyć konto użytkownika dla usługi, należy użyć polecenia cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) w powłoce poleceń administracyjnych programu PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="5424a-499">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="5424a-500">W systemie Windows 10 października 2018 Update (wersja 1809/Kompilacja 10.0.17763) lub nowsza:</span><span class="sxs-lookup"><span data-stu-id="5424a-500">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="5424a-501">W systemie operacyjnym Windows w wersji starszej niż aktualizacja 2018 systemu Windows 10 października (wersja 1809/Kompilacja 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="5424a-501">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="5424a-502">Podaj [silne hasło](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) po wyświetleniu monitu.</span><span class="sxs-lookup"><span data-stu-id="5424a-502">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="5424a-503">Jeśli `-AccountExpires` parametr nie zostanie dostarczony do polecenia cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z wygaśnięciem <xref:System.DateTime> , konto nie wygaśnie.</span><span class="sxs-lookup"><span data-stu-id="5424a-503">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="5424a-504">Aby uzyskać więcej informacji, zobacz [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) i [konta użytkowników usług](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="5424a-504">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="5424a-505">Alternatywna metoda zarządzania użytkownikami podczas korzystania z Active Directory polega na użyciu zarządzanych kont usług.</span><span class="sxs-lookup"><span data-stu-id="5424a-505">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="5424a-506">Aby uzyskać więcej informacji, zobacz [Omówienie kont usług zarządzanych przez grupę](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="5424a-506">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="5424a-507">Logowanie jako prawa usługi</span><span class="sxs-lookup"><span data-stu-id="5424a-507">Log on as a service rights</span></span>

<span data-ttu-id="5424a-508">Aby nawiązać *Logowanie jako prawa usługi* dla konta użytkownika usługi:</span><span class="sxs-lookup"><span data-stu-id="5424a-508">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="5424a-509">Otwórz Edytor lokalnych zasad zabezpieczeń, uruchamiając program *secpol. msc*.</span><span class="sxs-lookup"><span data-stu-id="5424a-509">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="5424a-510">Rozwiń węzeł **Zasady lokalne** , a następnie wybierz pozycję **Przypisywanie praw użytkownika**.</span><span class="sxs-lookup"><span data-stu-id="5424a-510">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="5424a-511">Otwórz okno **Logowanie jako usługa** .</span><span class="sxs-lookup"><span data-stu-id="5424a-511">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="5424a-512">Wybierz pozycję **Dodaj użytkownika lub grupę**.</span><span class="sxs-lookup"><span data-stu-id="5424a-512">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="5424a-513">Podaj nazwę obiektu (konto użytkownika) przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="5424a-513">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="5424a-514">Wpisz konto użytkownika ( `{DOMAIN OR COMPUTER NAME\USER}` ) w polu Nazwa obiektu, a następnie wybierz **przycisk OK** , aby dodać użytkownika do zasad.</span><span class="sxs-lookup"><span data-stu-id="5424a-514">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="5424a-515">Wybierz pozycję **Zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="5424a-515">Select **Advanced**.</span></span> <span data-ttu-id="5424a-516">Wybierz pozycję **Znajdź teraz**.</span><span class="sxs-lookup"><span data-stu-id="5424a-516">Select **Find Now**.</span></span> <span data-ttu-id="5424a-517">Wybierz z listy konto użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5424a-517">Select the user account from the list.</span></span> <span data-ttu-id="5424a-518">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="5424a-518">Select **OK**.</span></span> <span data-ttu-id="5424a-519">Ponownie wybierz **przycisk OK** , aby dodać użytkownika do zasad.</span><span class="sxs-lookup"><span data-stu-id="5424a-519">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="5424a-520">Wybierz **przycisk OK** lub **Zastosuj** , aby zaakceptować zmiany.</span><span class="sxs-lookup"><span data-stu-id="5424a-520">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="5424a-521">Tworzenie usługi systemu Windows i zarządzanie nią</span><span class="sxs-lookup"><span data-stu-id="5424a-521">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="5424a-522">Tworzenie usługi</span><span class="sxs-lookup"><span data-stu-id="5424a-522">Create a service</span></span>

<span data-ttu-id="5424a-523">Zarejestrowanie usługi za pomocą poleceń programu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="5424a-523">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="5424a-524">Z poziomu powłoki poleceń administracyjnych programu PowerShell 6 wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="5424a-524">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="5424a-525">`{EXE PATH}`: Ścieżka do folderu aplikacji na hoście (na przykład `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="5424a-525">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="5424a-526">Nie dołączaj pliku wykonywalnego aplikacji do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="5424a-526">Don't include the app's executable in the path.</span></span> <span data-ttu-id="5424a-527">Końcowy ukośnik nie jest wymagany.</span><span class="sxs-lookup"><span data-stu-id="5424a-527">A trailing slash isn't required.</span></span>
* <span data-ttu-id="5424a-528">`{DOMAIN OR COMPUTER NAME\USER}`: Konto użytkownika usługi (na przykład `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="5424a-528">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="5424a-529">`{SERVICE NAME}`: Nazwa usługi (na przykład `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="5424a-529">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="5424a-530">`{EXE FILE PATH}`: Ścieżka pliku wykonywalnego aplikacji (na przykład `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="5424a-530">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="5424a-531">Uwzględnij nazwę pliku wykonywalnego z rozszerzeniem.</span><span class="sxs-lookup"><span data-stu-id="5424a-531">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="5424a-532">`{DESCRIPTION}`: Opis usługi (na przykład `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="5424a-532">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="5424a-533">`{DISPLAY NAME}`: Nazwa wyświetlana usługi (na przykład `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="5424a-533">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="5424a-534">Uruchamianie usługi</span><span class="sxs-lookup"><span data-stu-id="5424a-534">Start a service</span></span>

<span data-ttu-id="5424a-535">Uruchom usługę za pomocą następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="5424a-535">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="5424a-536">Uruchomienie usługi może potrwać kilka sekund.</span><span class="sxs-lookup"><span data-stu-id="5424a-536">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="5424a-537">Określanie stanu usługi</span><span class="sxs-lookup"><span data-stu-id="5424a-537">Determine a service's status</span></span>

<span data-ttu-id="5424a-538">Aby sprawdzić stan usługi, użyj następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="5424a-538">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="5424a-539">Stan jest raportowany jako jedna z następujących wartości:</span><span class="sxs-lookup"><span data-stu-id="5424a-539">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="5424a-540">Zatrzymywanie usługi</span><span class="sxs-lookup"><span data-stu-id="5424a-540">Stop a service</span></span>

<span data-ttu-id="5424a-541">Zatrzymaj usługę za pomocą następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="5424a-541">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="5424a-542">Usuwanie usługi</span><span class="sxs-lookup"><span data-stu-id="5424a-542">Remove a service</span></span>

<span data-ttu-id="5424a-543">Po krótkim opóźnieniu na zatrzymanie usługi Usuń usługę za pomocą następującego polecenia programu PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="5424a-543">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="5424a-544">Obsługa zdarzeń uruchamiania i zatrzymywania</span><span class="sxs-lookup"><span data-stu-id="5424a-544">Handle starting and stopping events</span></span>

<span data-ttu-id="5424a-545">Aby obsłużyć <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*> <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> zdarzenia, i <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> :</span><span class="sxs-lookup"><span data-stu-id="5424a-545">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="5424a-546">Utwórz klasę, która dziedziczy z <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> `OnStarting` `OnStarted` metody,, i `OnStopping` :</span><span class="sxs-lookup"><span data-stu-id="5424a-546">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="5424a-547">Utwórz metodę rozszerzenia dla <xref:Microsoft.AspNetCore.Hosting.IWebHost> , która przekazuje `CustomWebHostService` do <xref:System.ServiceProcess.ServiceBase.Run*> :</span><span class="sxs-lookup"><span data-stu-id="5424a-547">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="5424a-548">W `Program.Main` , wywołaj `RunAsCustomService` metodę rozszerzenia zamiast <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :</span><span class="sxs-lookup"><span data-stu-id="5424a-548">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="5424a-549">Aby zobaczyć lokalizację programu <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> w programie `Program.Main` , zapoznaj się z przykładem kodu pokazanym w sekcji [typ wdrożenia](#deployment-type) .</span><span class="sxs-lookup"><span data-stu-id="5424a-549">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="5424a-550">Scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="5424a-550">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="5424a-551">Usługi, które współdziałają z żądaniami z Internetu lub sieci firmowej i znajdują się za serwerem proxy lub modułem równoważenia obciążenia, mogą wymagać dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="5424a-551">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="5424a-552">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="5424a-552">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="5424a-553">Konfigurowanie punktów końcowych</span><span class="sxs-lookup"><span data-stu-id="5424a-553">Configure endpoints</span></span>

<span data-ttu-id="5424a-554">Domyślnie ASP.NET Core wiąże się z `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="5424a-554">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="5424a-555">Skonfiguruj adres URL i port przez ustawienie `ASPNETCORE_URLS` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="5424a-555">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="5424a-556">Aby uzyskać dodatkowe podejścia do konfiguracji adresów URL i portów, zobacz artykuł dotyczący odpowiedniego serwera:</span><span class="sxs-lookup"><span data-stu-id="5424a-556">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="5424a-557">Powyższe wskazówki obejmują obsługę punktów końcowych HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5424a-557">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="5424a-558">Na przykład skonfiguruj aplikację do obsługi protokołu HTTPS, gdy uwierzytelnianie jest używane z usługą systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="5424a-558">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="5424a-559">Korzystanie z ASP.NET Core certyfikatu deweloperskiego HTTPS w celu zabezpieczenia punktu końcowego usługi nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="5424a-559">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="5424a-560">Bieżący katalog i główna Zawartość</span><span class="sxs-lookup"><span data-stu-id="5424a-560">Current directory and content root</span></span>

<span data-ttu-id="5424a-561">Bieżącym katalogiem roboczym zwróconym przez wywołanie <xref:System.IO.Directory.GetCurrentDirectory*> dla usługi systemu Windows jest folder *C: \\ Windows \\ system32* .</span><span class="sxs-lookup"><span data-stu-id="5424a-561">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="5424a-562">Folder *system32* nie jest odpowiednią lokalizacją do przechowywania plików usługi (na przykład plików ustawień).</span><span class="sxs-lookup"><span data-stu-id="5424a-562">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="5424a-563">Użyj jednego z poniższych metod, aby zachować i uzyskać dostęp do plików ustawień i zasobów usługi.</span><span class="sxs-lookup"><span data-stu-id="5424a-563">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="5424a-564">Ustawianie ścieżki katalogu głównego zawartości do folderu aplikacji</span><span class="sxs-lookup"><span data-stu-id="5424a-564">Set the content root path to the app's folder</span></span>

<span data-ttu-id="5424a-565">Jest to ta <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> sama ścieżka do `binPath` argumentu podczas tworzenia usługi.</span><span class="sxs-lookup"><span data-stu-id="5424a-565">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="5424a-566">Zamiast wywoływania funkcji `GetCurrentDirectory` tworzenia ścieżek do plików ustawień, należy wywołać <xref:System.IO.Directory.SetCurrentDirectory*> ścieżkę do [katalogu głównego zawartości](xref:fundamentals/index#content-root)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-566">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="5424a-567">W programie `Program.Main` Określ ścieżkę do folderu wykonywalnego usługi i użyj ścieżki, aby określić katalog główny zawartości aplikacji:</span><span class="sxs-lookup"><span data-stu-id="5424a-567">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="5424a-568">Przechowywanie plików usługi w odpowiedniej lokalizacji na dysku</span><span class="sxs-lookup"><span data-stu-id="5424a-568">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="5424a-569">Określ ścieżkę bezwzględną przy <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> użyciu <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do folderu zawierającego pliki.</span><span class="sxs-lookup"><span data-stu-id="5424a-569">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="5424a-570">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="5424a-570">Troubleshoot</span></span>

<span data-ttu-id="5424a-571">Aby rozwiązać problem z aplikacją usługi systemu Windows, zobacz <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="5424a-571">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="5424a-572">Typowe błędy</span><span class="sxs-lookup"><span data-stu-id="5424a-572">Common errors</span></span>

* <span data-ttu-id="5424a-573">Starsza lub wstępnie wydana wersja programu PowerShell jest używana.</span><span class="sxs-lookup"><span data-stu-id="5424a-573">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="5424a-574">Zarejestrowana usługa nie używa **opublikowanych** danych wyjściowych aplikacji z [dotnet Publish](/dotnet/core/tools/dotnet-publish) polecenia.</span><span class="sxs-lookup"><span data-stu-id="5424a-574">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="5424a-575">Dane wyjściowe polecenia [kompilacji dotnet](/dotnet/core/tools/dotnet-build) nie są obsługiwane w przypadku wdrażania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-575">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="5424a-576">Opublikowane zasoby znajdują się w dowolnym z następujących folderów w zależności od typu wdrożenia:</span><span class="sxs-lookup"><span data-stu-id="5424a-576">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="5424a-577">*bin/Release/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="5424a-577">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="5424a-578">*bin/Release/{Target Framework}/{Runtime identyfikator}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="5424a-578">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="5424a-579">Usługa nie jest w stanie uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="5424a-579">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="5424a-580">Ścieżki do zasobów używanych przez aplikację (na przykład certyfikaty) są nieprawidłowe.</span><span class="sxs-lookup"><span data-stu-id="5424a-580">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="5424a-581">Ścieżką podstawową usługi systemu Windows jest *c: \\ Windows \\ system32*.</span><span class="sxs-lookup"><span data-stu-id="5424a-581">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="5424a-582">Użytkownik nie ma uprawnień do *logowania się jako usługa* .</span><span class="sxs-lookup"><span data-stu-id="5424a-582">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="5424a-583">Hasło użytkownika wygasło lub zostało nieprawidłowo przesłane podczas wykonywania `New-Service` polecenia programu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="5424a-583">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="5424a-584">Aplikacja wymaga uwierzytelniania ASP.NET Core, ale nie jest skonfigurowana dla połączeń Secure (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="5424a-584">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="5424a-585">Port adresu URL żądania jest niepoprawny lub niepoprawnie skonfigurowany w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-585">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="5424a-586">Dzienniki zdarzeń systemu i aplikacji</span><span class="sxs-lookup"><span data-stu-id="5424a-586">System and Application Event Logs</span></span>

<span data-ttu-id="5424a-587">Uzyskaj dostęp do dzienników zdarzeń systemu i aplikacji:</span><span class="sxs-lookup"><span data-stu-id="5424a-587">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="5424a-588">Otwórz menu Start, wyszukaj ciąg *Podgląd zdarzeń*i wybierz aplikację **Podgląd zdarzeń** .</span><span class="sxs-lookup"><span data-stu-id="5424a-588">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="5424a-589">W **Podgląd zdarzeń**Otwórz węzeł **Dzienniki systemu Windows** .</span><span class="sxs-lookup"><span data-stu-id="5424a-589">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="5424a-590">Wybierz pozycję **system** , aby otworzyć dziennik zdarzeń systemu.</span><span class="sxs-lookup"><span data-stu-id="5424a-590">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="5424a-591">Wybierz pozycję **aplikacja** , aby otworzyć dziennik zdarzeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-591">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="5424a-592">Wyszukaj błędy związane z niepowodzeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-592">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="5424a-593">Uruchamianie aplikacji w wierszu polecenia</span><span class="sxs-lookup"><span data-stu-id="5424a-593">Run the app at a command prompt</span></span>

<span data-ttu-id="5424a-594">Wiele błędów uruchamiania nie produkuje użytecznych informacji w dziennikach zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="5424a-594">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="5424a-595">Przyczynę niektórych błędów można znaleźć, uruchamiając aplikację w wierszu polecenia w systemie hostingu.</span><span class="sxs-lookup"><span data-stu-id="5424a-595">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="5424a-596">Aby rejestrować dodatkowe szczegóły aplikacji, Obniż [poziom rejestrowania](xref:fundamentals/logging/index#log-level) lub Uruchom aplikację w [środowisku deweloperskim](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="5424a-596">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="5424a-597">Wyczyść pamięć podręczną pakietów</span><span class="sxs-lookup"><span data-stu-id="5424a-597">Clear package caches</span></span>

<span data-ttu-id="5424a-598">Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestaw .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w ramach aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-598">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="5424a-599">W niektórych przypadkach niespójne pakiety mogą przerwać aplikację podczas przeprowadzania uaktualnień głównych.</span><span class="sxs-lookup"><span data-stu-id="5424a-599">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="5424a-600">Większość tych problemów można naprawić, wykonując następujące instrukcje:</span><span class="sxs-lookup"><span data-stu-id="5424a-600">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="5424a-601">Usuń foldery *bin* i *obj* .</span><span class="sxs-lookup"><span data-stu-id="5424a-601">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="5424a-602">Wyczyść pamięć podręczną pakietów, wykonując [wszystkie elementy lokalne usługi NuGet programu dotnet--Wyczyść](/dotnet/core/tools/dotnet-nuget-locals) z poziomu powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="5424a-602">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="5424a-603">Czyszczenie pamięci podręcznych pakietów można także wykonać przy użyciu narzędzia [NuGet. exe](https://www.nuget.org/downloads) i wykonując polecenie `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="5424a-603">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="5424a-604">*NuGet. exe* nie jest pakietem instalowanym z systemem operacyjnym Windows dla komputerów stacjonarnych i musi być uzyskany niezależnie od [witryny sieci Web programu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="5424a-604">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="5424a-605">Przywróć i skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="5424a-605">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="5424a-606">Usuń wszystkie pliki z folderu wdrożenia na serwerze przed ponownym wdrożeniem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-606">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="5424a-607">Aplikacja wolna lub wysunięta</span><span class="sxs-lookup"><span data-stu-id="5424a-607">Slow or hanging app</span></span>

<span data-ttu-id="5424a-608">*Zrzut awaryjny* to migawka pamięci systemu, która może pomóc w ustaleniu przyczyny awarii aplikacji, awarii uruchamiania lub powolnej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5424a-608">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="5424a-609">Awaria aplikacji lub napotka wyjątek</span><span class="sxs-lookup"><span data-stu-id="5424a-609">App crashes or encounters an exception</span></span>

<span data-ttu-id="5424a-610">Uzyskaj i Analizuj Zrzut z [raportowanie błędów systemu Windows (raportowanie błędów systemu Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="5424a-610">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="5424a-611">Utwórz folder do przechowywania plików zrzutu awaryjnego w `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="5424a-611">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="5424a-612">Uruchom [skrypt programu PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) w programie EnableDumps z nazwą pliku wykonywalnego aplikacji:</span><span class="sxs-lookup"><span data-stu-id="5424a-612">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="5424a-613">Uruchom aplikację w warunkach, które powodują awarię.</span><span class="sxs-lookup"><span data-stu-id="5424a-613">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="5424a-614">Po wystąpieniu awarii Uruchom [skrypt programu DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="5424a-614">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="5424a-615">Po awarii aplikacji i zakończeniu zbierania zrzutów aplikacja może zakończyć normalne działanie.</span><span class="sxs-lookup"><span data-stu-id="5424a-615">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="5424a-616">Skrypt programu PowerShell konfiguruje raportowanie błędów systemu Windows w celu zebrania do pięciu zrzutów na aplikację.</span><span class="sxs-lookup"><span data-stu-id="5424a-616">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="5424a-617">Zrzuty awaryjne mogą wymagać dużej ilości miejsca na dysku (do kilku gigabajtów).</span><span class="sxs-lookup"><span data-stu-id="5424a-617">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="5424a-618">Aplikacja zawiesza się, kończy się niepowodzeniem podczas uruchamiania lub działa normalnie</span><span class="sxs-lookup"><span data-stu-id="5424a-618">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="5424a-619">Gdy aplikacja *zawiesza* się (bez awarii), kończy się niepowodzeniem podczas uruchamiania lub działa normalnie, zobacz [pliki zrzutu w trybie użytkownika: wybór najlepszego narzędzia](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) do wygenerowania zrzutu.</span><span class="sxs-lookup"><span data-stu-id="5424a-619">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="5424a-620">Analizowanie zrzutu</span><span class="sxs-lookup"><span data-stu-id="5424a-620">Analyze the dump</span></span>

<span data-ttu-id="5424a-621">Zrzut można analizować przy użyciu kilku metod.</span><span class="sxs-lookup"><span data-stu-id="5424a-621">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="5424a-622">Aby uzyskać więcej informacji, zobacz [Analizowanie pliku zrzutu w trybie użytkownika](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="5424a-622">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5424a-623">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="5424a-623">Additional resources</span></span>

* <span data-ttu-id="5424a-624">[Konfiguracja punktu końcowego Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (w tym Konfiguracja protokołu HTTPS i obsługa SNI)</span><span class="sxs-lookup"><span data-stu-id="5424a-624">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
