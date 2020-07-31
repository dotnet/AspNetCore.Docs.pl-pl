---
title: Konfigurowanie uwierzytelniania systemu Windows w ASP.NET Core
author: scottaddie
description: Dowiedz się, jak skonfigurować uwierzytelnianie systemu Windows w ASP.NET Core dla usług IIS i HTTP.sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/windowsauth
ms.openlocfilehash: 8f6dc8620df04bcebe996119869ca2e498cffccc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "87330690"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="7a9b3-103">Konfigurowanie uwierzytelniania systemu Windows w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7a9b3-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="7a9b3-104">Przez [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="7a9b3-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a9b3-105">Uwierzytelnianie systemu Windows (znane także jako uwierzytelnianie negocjowane, Kerberos lub NTLM) można skonfigurować dla ASP.NET Core aplikacji hostowanych za pomocą [usług IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel)lub [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="7a9b3-105">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel), or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7a9b3-106">Uwierzytelnianie systemu Windows (znane także jako uwierzytelnianie Negocjuj, Kerberos lub NTLM) można skonfigurować dla ASP.NET Core aplikacji hostowanych za pomocą [usług IIS](xref:host-and-deploy/iis/index) lub [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="7a9b3-106">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

<span data-ttu-id="7a9b3-107">Uwierzytelnianie systemu Windows polega na użyciu systemu operacyjnego w celu uwierzytelniania użytkowników ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-107">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="7a9b3-108">Możesz użyć uwierzytelniania systemu Windows, gdy serwer działa w sieci firmowej przy użyciu tożsamości domeny Active Directory lub kont systemu Windows w celu identyfikowania użytkowników.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-108">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="7a9b3-109">Uwierzytelnianie systemu Windows najlepiej nadaje się w środowiskach intranetowych, w których użytkownicy, aplikacje klienckie i serwery sieci Web należą do tej samej domeny systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-109">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

> [!NOTE]
> <span data-ttu-id="7a9b3-110">Uwierzytelnianie systemu Windows nie jest obsługiwane w przypadku protokołu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-110">Windows Authentication isn't supported with HTTP/2.</span></span> <span data-ttu-id="7a9b3-111">Problemy z uwierzytelnianiem można wysyłać w odpowiedzi HTTP/2, ale klient programu musi obniżyć poziom protokołu HTTP/1.1 Przed uwierzytelnieniem.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-111">Authentication challenges can be sent on HTTP/2 responses, but the client must downgrade to HTTP/1.1 before authenticating.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="7a9b3-112">Scenariusze dotyczące serwerów proxy i równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="7a9b3-112">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="7a9b3-113">Uwierzytelnianie systemu Windows jest scenariuszem stanowym głównie używanym w intranecie, gdzie serwer proxy lub moduł równoważenia obciążenia zazwyczaj nie obsługuje ruchu między klientami i serwerami.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-113">Windows Authentication is a stateful scenario primarily used in an intranet, where a proxy or load balancer doesn't usually handle traffic between clients and servers.</span></span> <span data-ttu-id="7a9b3-114">Jeśli używany jest serwer proxy lub moduł równoważenia obciążenia, uwierzytelnianie systemu Windows działa tylko wtedy, gdy serwer proxy lub moduł równoważenia obciążenia:</span><span class="sxs-lookup"><span data-stu-id="7a9b3-114">If a proxy or load balancer is used, Windows Authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="7a9b3-115">Obsługuje uwierzytelnianie.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-115">Handles the authentication.</span></span>
* <span data-ttu-id="7a9b3-116">Przekazuje informacje o uwierzytelnianiu użytkownika do aplikacji (na przykład w nagłówku żądania), która działa na informacje o uwierzytelnianiu.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-116">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="7a9b3-117">Alternatywą dla uwierzytelniania systemu Windows w środowiskach, w których są używane serwery proxy i moduły równoważenia obciążenia, Active Directory usług federacyjnych (AD FS) za pomocą OpenID Connect Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="7a9b3-117">An alternative to Windows Authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="iisiis-express"></a><span data-ttu-id="7a9b3-118">Usługi IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="7a9b3-118">IIS/IIS Express</span></span>

<span data-ttu-id="7a9b3-119">Dodaj usługi uwierzytelniania, wywołując <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ( <xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> przestrzeń nazw) w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7a9b3-119">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a><span data-ttu-id="7a9b3-120">Ustawienia uruchamiania (debuger)</span><span class="sxs-lookup"><span data-stu-id="7a9b3-120">Launch settings (debugger)</span></span>

<span data-ttu-id="7a9b3-121">Konfiguracja ustawień uruchamiania ma wpływ tylko na *Właściwości/launchSettings.jsw* pliku dla IIS Express i nie KONFIGURUJE usług IIS do uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-121">Configuration for launch settings only affects the *Properties/launchSettings.json* file for IIS Express and doesn't configure IIS for Windows Authentication.</span></span> <span data-ttu-id="7a9b3-122">Konfiguracja serwera została omówiona w sekcji [usług IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="7a9b3-122">Server configuration is explained in the [IIS](#iis) section.</span></span>

<span data-ttu-id="7a9b3-123">Szablon **aplikacji sieci Web** dostępny za pośrednictwem programu Visual Studio lub interfejs wiersza polecenia platformy .NET Core można skonfigurować do obsługi uwierzytelniania systemu Windows, który automatycznie aktualizuje *Właściwości/launchSettings.jsna* pliku.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-123">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication, which updates the *Properties/launchSettings.json* file automatically.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7a9b3-124">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7a9b3-124">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7a9b3-125">**Nowy projekt**</span><span class="sxs-lookup"><span data-stu-id="7a9b3-125">**New project**</span></span>

1. <span data-ttu-id="7a9b3-126">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-126">Create a new project.</span></span>
1. <span data-ttu-id="7a9b3-127">Wybierz **ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-127">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="7a9b3-128">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-128">Select **Next**.</span></span>
1. <span data-ttu-id="7a9b3-129">Podaj nazwę w polu **Nazwa projektu** .</span><span class="sxs-lookup"><span data-stu-id="7a9b3-129">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="7a9b3-130">Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-130">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="7a9b3-131">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-131">Select **Create**.</span></span>
1. <span data-ttu-id="7a9b3-132">Wybierz pozycję **Zmień** w obszarze **uwierzytelnianie**.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-132">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="7a9b3-133">W oknie **Zmienianie uwierzytelniania** wybierz pozycję **uwierzytelnianie systemu Windows**.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-133">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="7a9b3-134">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-134">Select **OK**.</span></span>
1. <span data-ttu-id="7a9b3-135">Wybierz pozycję **aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-135">Select **Web Application**.</span></span>
1. <span data-ttu-id="7a9b3-136">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-136">Select **Create**.</span></span>

<span data-ttu-id="7a9b3-137">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-137">Run the app.</span></span> <span data-ttu-id="7a9b3-138">Nazwa użytkownika jest wyświetlana w interfejsie użytkownika renderowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-138">The username appears in the rendered app's user interface.</span></span>

<span data-ttu-id="7a9b3-139">**Istniejący projekt**</span><span class="sxs-lookup"><span data-stu-id="7a9b3-139">**Existing project**</span></span>

<span data-ttu-id="7a9b3-140">Właściwości projektu umożliwiają uwierzytelnianie systemu Windows i Wyłącz uwierzytelnianie anonimowe:</span><span class="sxs-lookup"><span data-stu-id="7a9b3-140">The project's properties enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="7a9b3-141">Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **Właściwości**.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-141">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="7a9b3-142">Wybierz kartę **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="7a9b3-142">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="7a9b3-143">Usuń zaznaczenie pola wyboru **Włącz uwierzytelnianie anonimowe**.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-143">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="7a9b3-144">Zaznacz pole wyboru **Włącz uwierzytelnianie systemu Windows**.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-144">Select the check box for **Enable Windows Authentication**.</span></span>
1. <span data-ttu-id="7a9b3-145">Zapisz i zamknij stronę właściwości.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-145">Save and close the property page.</span></span>

<span data-ttu-id="7a9b3-146">Alternatywnie można skonfigurować właściwości w `iisSettings` węźle *launchSettings.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="7a9b3-146">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-cli"></a>[<span data-ttu-id="7a9b3-147">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="7a9b3-147">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="7a9b3-148">**Nowy projekt**</span><span class="sxs-lookup"><span data-stu-id="7a9b3-148">**New project**</span></span>

<span data-ttu-id="7a9b3-149">Wykonaj polecenie [dotnet New](/dotnet/core/tools/dotnet-new) przy użyciu `webapp` argumentu (ASP.NET Core aplikacji sieci Web) i `--auth Windows` Przełącz:</span><span class="sxs-lookup"><span data-stu-id="7a9b3-149">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```dotnetcli
dotnet new webapp --auth Windows
```

<span data-ttu-id="7a9b3-150">**Istniejący projekt**</span><span class="sxs-lookup"><span data-stu-id="7a9b3-150">**Existing project**</span></span>

<span data-ttu-id="7a9b3-151">Aktualizuj `iisSettings` węzeł *launchSettings.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="7a9b3-151">Update the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

<span data-ttu-id="7a9b3-152">Podczas modyfikowania istniejącego projektu upewnij się, że plik projektu zawiera odwołanie do pakietu dla pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) , **lub** pakiet NuGet [Microsoft. AspNetCore. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="7a9b3-152">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

### <a name="iis"></a><span data-ttu-id="7a9b3-153">IIS</span><span class="sxs-lookup"><span data-stu-id="7a9b3-153">IIS</span></span>

<span data-ttu-id="7a9b3-154">Usługi IIS używają [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) do hostowania aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-154">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="7a9b3-155">Uwierzytelnianie systemu Windows jest konfigurowane dla usług IIS za pośrednictwem pliku *web.config* .</span><span class="sxs-lookup"><span data-stu-id="7a9b3-155">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="7a9b3-156">W poniższych sekcjach pokazano, jak:</span><span class="sxs-lookup"><span data-stu-id="7a9b3-156">The following sections show how to:</span></span>

* <span data-ttu-id="7a9b3-157">Podaj plik *web.config* lokalnego, który aktywuje uwierzytelnianie systemu Windows na serwerze podczas wdrażania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-157">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="7a9b3-158">Za pomocą Menedżera usług IIS Skonfiguruj plik *web.config* aplikacji ASP.NET Core, która została już wdrożona na serwerze.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-158">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

<span data-ttu-id="7a9b3-159">Jeśli jeszcze tego nie zrobiono, Włącz usługi IIS do hostowania aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-159">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="7a9b3-160">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-160">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="7a9b3-161">Włącz usługę roli IIS na potrzeby uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-161">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="7a9b3-162">Aby uzyskać więcej informacji, zobacz [Włączanie uwierzytelniania systemu Windows w usługach ról usług IIS (zobacz krok 2)](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="7a9b3-162">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="7a9b3-163">[Oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) jest skonfigurowane do automatycznego uwierzytelniania żądań domyślnie.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-163">[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="7a9b3-164">Aby uzyskać więcej informacji, zobacz [hostowanie ASP.NET Core w systemie Windows za pomocą usług IIS: opcje usług IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span><span class="sxs-lookup"><span data-stu-id="7a9b3-164">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="7a9b3-165">Moduł ASP.NET Core jest skonfigurowany do domyślnego przesyłania dalej tokenu uwierzytelniania systemu Windows do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-165">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="7a9b3-166">Aby uzyskać więcej informacji, zobacz [ASP.NET Core informacje o konfiguracji modułu: atrybuty elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="7a9b3-166">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

<span data-ttu-id="7a9b3-167">Użyj **jednej** z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="7a9b3-167">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="7a9b3-168">**Przed opublikowaniem i wdrożeniem projektu** Dodaj następujący plik *web.config* do katalogu głównego projektu:</span><span class="sxs-lookup"><span data-stu-id="7a9b3-168">**Before publishing and deploying the project,** add the following *web.config* file to the project root:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  <span data-ttu-id="7a9b3-169">Gdy projekt jest publikowany przez zestaw .NET Core SDK (bez `<IsTransformWebConfigDisabled>` Właściwości ustawionej `true` w pliku projektu), publikowany plik *web.config* zawiera `<location><system.webServer><security><authentication>` sekcję.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-169">When the project is published by the .NET Core SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="7a9b3-170">Aby uzyskać więcej informacji na temat `<IsTransformWebConfigDisabled>` właściwości, zobacz <xref:host-and-deploy/iis/index#webconfig-file> .</span><span class="sxs-lookup"><span data-stu-id="7a9b3-170">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

* <span data-ttu-id="7a9b3-171">**Po opublikowaniu i wdrożeniu projektu** wykonaj konfigurację po stronie serwera za pomocą Menedżera usług IIS:</span><span class="sxs-lookup"><span data-stu-id="7a9b3-171">**After publishing and deploying the project,** perform server-side configuration with the IIS Manager:</span></span>

  1. <span data-ttu-id="7a9b3-172">W Menedżerze usług IIS wybierz lokację usług IIS w węźle **Lokacje** na pasku bocznym **połączenia** .</span><span class="sxs-lookup"><span data-stu-id="7a9b3-172">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
  1. <span data-ttu-id="7a9b3-173">Kliknij dwukrotnie pozycję **uwierzytelnianie** w obszarze **usługi IIS** .</span><span class="sxs-lookup"><span data-stu-id="7a9b3-173">Double-click **Authentication** in the **IIS** area.</span></span>
  1. <span data-ttu-id="7a9b3-174">Wybierz opcję **uwierzytelnianie anonimowe**.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-174">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="7a9b3-175">Na pasku bocznym **Akcje** wybierz pozycję **Wyłącz** .</span><span class="sxs-lookup"><span data-stu-id="7a9b3-175">Select **Disable** in the **Actions** sidebar.</span></span>
  1. <span data-ttu-id="7a9b3-176">Wybierz pozycję **uwierzytelnianie systemu Windows**.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-176">Select **Windows Authentication**.</span></span> <span data-ttu-id="7a9b3-177">Na pasku bocznym **Akcje** wybierz pozycję **Włącz** .</span><span class="sxs-lookup"><span data-stu-id="7a9b3-177">Select **Enable** in the **Actions** sidebar.</span></span>

  <span data-ttu-id="7a9b3-178">Po wykonaniu tych działań Menedżer usług IIS modyfikuje plik *web.config* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-178">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="7a9b3-179">`<system.webServer><security><authentication>`Dodano węzeł z zaktualizowanymi ustawieniami dla `anonymousAuthentication` i `windowsAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="7a9b3-179">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  <span data-ttu-id="7a9b3-180">`<system.webServer>`Sekcja dodana do pliku *web.config* przez Menedżera usług IIS znajduje się poza `<location>` sekcją aplikacji dodanej przez zestaw .NET Core SDK po opublikowaniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-180">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="7a9b3-181">Ponieważ sekcja została dodana poza `<location>` węzłem, ustawienia są dziedziczone przez wszystkie [aplikacje podrzędne](xref:host-and-deploy/iis/index#sub-applications) do bieżącej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-181">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="7a9b3-182">Aby zapobiec dziedziczeniu, Przenieś dodaną `<security>` sekcję wewnątrz `<location><system.webServer>` sekcji dostarczonej przez zestaw .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-182">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the .NET Core SDK provided.</span></span>

  <span data-ttu-id="7a9b3-183">Gdy Menedżer usług IIS jest używany do dodawania konfiguracji usług IIS, ma wpływ tylko na plik *web.config* aplikacji na serwerze.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-183">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="7a9b3-184">Kolejne wdrożenie aplikacji może spowodować zastąpienie ustawień na serwerze, jeśli kopia *web.config* serwera jest zastępowana przez plik *web.config* projektu.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-184">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="7a9b3-185">Użyj **jednego** z poniższych metod, aby zarządzać ustawieniami:</span><span class="sxs-lookup"><span data-stu-id="7a9b3-185">Use **either** of the following approaches to manage the settings:</span></span>

  * <span data-ttu-id="7a9b3-186">Za pomocą Menedżera usług IIS Zresetuj ustawienia w pliku *web.config* po zastępowaniu pliku we wdrożeniu.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-186">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
  * <span data-ttu-id="7a9b3-187">Dodaj *plikweb.config* do aplikacji lokalnie przy użyciu ustawień.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-187">Add a *web.config file* to the app locally with the settings.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a><span data-ttu-id="7a9b3-188">Kestrel</span><span class="sxs-lookup"><span data-stu-id="7a9b3-188">Kestrel</span></span>

<span data-ttu-id="7a9b3-189">Pakiet NuGet [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) może być używany z [Kestrel](xref:fundamentals/servers/kestrel) do obsługi uwierzytelniania systemu Windows przy użyciu protokołu Negotiate i Kerberos w systemach Windows, Linux i macOS.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-189">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet package can be used with [Kestrel](xref:fundamentals/servers/kestrel) to support Windows Authentication using Negotiate and Kerberos on Windows, Linux, and macOS.</span></span>

> [!WARNING]
> <span data-ttu-id="7a9b3-190">Poświadczenia mogą być utrwalane między żądaniami w ramach połączenia.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-190">Credentials can be persisted across requests on a connection.</span></span> <span data-ttu-id="7a9b3-191">*Uwierzytelnianie negocjowane nie może być używane z serwerami proxy, chyba że serwer proxy utrzymuje koligację połączenia 1:1 (połączenie trwałe) z Kestrel.*</span><span class="sxs-lookup"><span data-stu-id="7a9b3-191">*Negotiate authentication must not be used with proxies unless the proxy maintains a 1:1 connection affinity (a persistent connection) with Kestrel.*</span></span>

> [!NOTE]
> <span data-ttu-id="7a9b3-192">Procedura obsługi negocjuje wykrywa, czy podstawowy serwer obsługuje uwierzytelnianie systemu Windows w sposób natywny, a jeśli jest włączony.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-192">The Negotiate handler detects if the underlying server supports Windows Authentication natively and if it's enabled.</span></span> <span data-ttu-id="7a9b3-193">Jeśli serwer obsługuje uwierzytelnianie systemu Windows, ale jest wyłączony, zostanie zgłoszony błąd z pytaniem, czy należy włączyć implementację serwera.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-193">If the server supports Windows Authentication but it's disabled, an error is thrown asking you to enable the server implementation.</span></span> <span data-ttu-id="7a9b3-194">Gdy na serwerze jest włączone uwierzytelnianie systemu Windows, program obsługi negocjuje w sposób niewidoczny do przodu.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-194">When Windows Authentication is enabled in the server, the Negotiate handler transparently forwards to it.</span></span>

<span data-ttu-id="7a9b3-195">Dodawanie usług uwierzytelniania przez wywoływanie <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> i <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7a9b3-195">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> in `Startup.ConfigureServices`:</span></span>

 ```csharp
// using Microsoft.AspNetCore.Authentication.Negotiate;
// using Microsoft.Extensions.DependencyInjection;

services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

<span data-ttu-id="7a9b3-196">Dodaj oprogramowanie pośredniczące uwierzytelniania przez wywołanie <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> w `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="7a9b3-196">Add Authentication Middleware by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> in `Startup.Configure`:</span></span>

 ```csharp
app.UseAuthentication();
```

<span data-ttu-id="7a9b3-197">Aby uzyskać więcej informacji na temat oprogramowania pośredniczącego, zobacz <xref:fundamentals/middleware/index> .</span><span class="sxs-lookup"><span data-stu-id="7a9b3-197">For more information on middleware, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="7a9b3-198">Dozwolone są żądania anonimowe.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-198">Anonymous requests are allowed.</span></span> <span data-ttu-id="7a9b3-199">Użyj [autoryzacji ASP.NET Core](xref:security/authorization/introduction) , aby zażądać anonimowych żądań uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-199">Use [ASP.NET Core Authorization](xref:security/authorization/introduction) to challenge anonymous requests for authentication.</span></span>

### <a name="windows-environment-configuration"></a><span data-ttu-id="7a9b3-200">Konfiguracja środowiska systemu Windows</span><span class="sxs-lookup"><span data-stu-id="7a9b3-200">Windows environment configuration</span></span>

<span data-ttu-id="7a9b3-201">Składnik [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) wykonuje uwierzytelnianie w trybie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-201">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) component performs User Mode authentication.</span></span> <span data-ttu-id="7a9b3-202">Nazwy główne usług (SPN) należy dodać do konta użytkownika, na którym działa usługa, a nie konta komputera.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-202">Service Principal Names (SPNs) must be added to the user account running the service, not the machine account.</span></span> <span data-ttu-id="7a9b3-203">Wykonaj `setspn -S HTTP/myservername.mydomain.com myuser` w powłoce poleceń administracyjnych.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-203">Execute `setspn -S HTTP/myservername.mydomain.com myuser` in an administrative command shell.</span></span>

### <a name="linux-and-macos-environment-configuration"></a><span data-ttu-id="7a9b3-204">Konfiguracja środowiska Linux i macOS</span><span class="sxs-lookup"><span data-stu-id="7a9b3-204">Linux and macOS environment configuration</span></span>

<span data-ttu-id="7a9b3-205">Instrukcje dotyczące przyłączania maszyny z systemem Linux lub macOS do domeny systemu Windows są dostępne w [Azure Data Studio połączenia SQL Server przy użyciu uwierzytelniania systemu Windows — artykuł Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) .</span><span class="sxs-lookup"><span data-stu-id="7a9b3-205">Instructions for joining a Linux or macOS machine to a Windows domain are available in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article.</span></span> <span data-ttu-id="7a9b3-206">Instrukcje tworzą konto komputera dla komputera z systemem Linux w domenie.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-206">The instructions create a machine account for the Linux machine on the domain.</span></span> <span data-ttu-id="7a9b3-207">Nazwy SPN należy dodać do tego konta komputera.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-207">SPNs must be added to that machine account.</span></span>

> [!NOTE]
> <span data-ttu-id="7a9b3-208">Po [zastosowaniu wskazówek w Azure Data Studio połącz SQL Server przy użyciu uwierzytelniania systemu Windows — artykuł Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) , Zastąp `python-software-properties` w `python3-software-properties` razie potrzeby.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-208">When following the guidance in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article, replace `python-software-properties` with `python3-software-properties` if needed.</span></span>

<span data-ttu-id="7a9b3-209">Po przyłączeniu maszyny z systemem Linux lub macOS do domeny należy wykonać dodatkowe czynności, aby dostarczyć [plik plik KEYTAB](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) z nazwami SPN:</span><span class="sxs-lookup"><span data-stu-id="7a9b3-209">Once the Linux or macOS machine is joined to the domain, additional steps are required to provide a [keytab file](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) with the SPNs:</span></span>

* <span data-ttu-id="7a9b3-210">Na kontrolerze domeny Dodaj nowe nazwy SPN usługi sieci Web do konta komputera:</span><span class="sxs-lookup"><span data-stu-id="7a9b3-210">On the domain controller, add new web service SPNs to the machine account:</span></span>
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* <span data-ttu-id="7a9b3-211">Użyj [ktpass](/windows-server/administration/windows-commands/ktpass) , aby wygenerować plik plik KEYTAB:</span><span class="sxs-lookup"><span data-stu-id="7a9b3-211">Use [ktpass](/windows-server/administration/windows-commands/ktpass) to generate a keytab file:</span></span>
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * <span data-ttu-id="7a9b3-212">Niektóre pola muszą być określone wielkimi literami, jak wskazano.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-212">Some fields must be specified in uppercase as indicated.</span></span>
* <span data-ttu-id="7a9b3-213">Skopiuj plik plik KEYTAB na maszynę z systemem Linux lub macOS.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-213">Copy the keytab file to the Linux or macOS machine.</span></span>
* <span data-ttu-id="7a9b3-214">Wybierz plik plik KEYTAB za pomocą zmiennej środowiskowej:`export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span><span class="sxs-lookup"><span data-stu-id="7a9b3-214">Select the keytab file via an environment variable: `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span></span>
* <span data-ttu-id="7a9b3-215">Wywołaj `klist` , aby wyświetlić nazwy SPN obecnie dostępne do użycia.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-215">Invoke `klist` to show the SPNs currently available for use.</span></span>

> [!NOTE]
> <span data-ttu-id="7a9b3-216">Plik plik KEYTAB zawiera poświadczenia dostępu do domeny i musi być odpowiednio chroniony.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-216">A keytab file contains domain access credentials and must be protected accordingly.</span></span>

::: moniker-end

## <a name="httpsys"></a><span data-ttu-id="7a9b3-217">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="7a9b3-217">HTTP.sys</span></span>

<span data-ttu-id="7a9b3-218">[HTTP.sys](xref:fundamentals/servers/httpsys) obsługuje uwierzytelnianie systemu Windows trybu jądra przy użyciu protokołu negocjowania, NTLM lub uwierzytelniania podstawowego.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-218">[HTTP.sys](xref:fundamentals/servers/httpsys) supports Kernel Mode Windows Authentication using Negotiate, NTLM, or Basic authentication.</span></span>

<span data-ttu-id="7a9b3-219">Dodaj usługi uwierzytelniania, wywołując <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ( <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> przestrzeń nazw) w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7a9b3-219">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

<span data-ttu-id="7a9b3-220">Skonfiguruj hosta sieci Web aplikacji, aby używał HTTP.sys z uwierzytelnianiem systemu Windows (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="7a9b3-220">Configure the app's web host to use HTTP.sys with Windows Authentication (*Program.cs*).</span></span> <span data-ttu-id="7a9b3-221"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>znajduje się w <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-221"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> is in the <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="7a9b3-222">HTTP.sys delegatów do uwierzytelniania w trybie jądra przy użyciu protokołu uwierzytelniania Kerberos.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-222">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="7a9b3-223">Uwierzytelnianie w trybie użytkownika nie jest obsługiwane w przypadku używania protokołów Kerberos i HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-223">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="7a9b3-224">Konto komputera musi służyć do odszyfrowywania tokenu lub biletu Kerberos uzyskanych z Active Directory i przesłanych przez klienta na serwer w celu uwierzytelnienia użytkownika.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-224">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="7a9b3-225">Zarejestruj główną nazwę usługi (SPN) dla hosta, a nie użytkownika aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-225">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="7a9b3-226">HTTP.sys nie jest obsługiwana w systemie nano Server w wersji 1709 lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-226">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="7a9b3-227">Aby użyć uwierzytelniania systemu Windows i HTTP.sys z serwerem nano Server, użyj [kontenera Server Core (Microsoft/windowsservercore)](https://hub.docker.com/r/microsoft/windowsservercore/).</span><span class="sxs-lookup"><span data-stu-id="7a9b3-227">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="7a9b3-228">Aby uzyskać więcej informacji na temat serwera Server Core, zobacz [co to jest opcja instalacji Server Core w systemie Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span><span class="sxs-lookup"><span data-stu-id="7a9b3-228">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="authorize-users"></a><span data-ttu-id="7a9b3-229">Autoryzuj użytkowników</span><span class="sxs-lookup"><span data-stu-id="7a9b3-229">Authorize users</span></span>

<span data-ttu-id="7a9b3-230">Stan konfiguracji dostępu anonimowego określa sposób, w jaki `[Authorize]` `[AllowAnonymous]` atrybuty i są używane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-230">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="7a9b3-231">W poniższych dwóch sekcjach opisano sposób obsługi niedozwolonych i dozwolonych Stanów konfiguracji dostępu anonimowego.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-231">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="7a9b3-232">Nie Zezwalaj na dostęp anonimowy</span><span class="sxs-lookup"><span data-stu-id="7a9b3-232">Disallow anonymous access</span></span>

<span data-ttu-id="7a9b3-233">Gdy uwierzytelnianie systemu Windows jest włączone i dostęp anonimowy jest wyłączony, `[Authorize]` `[AllowAnonymous]` atrybuty i nie mają żadnego wpływu.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-233">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="7a9b3-234">Jeśli witryna IIS jest skonfigurowana tak, aby nie zezwalać na dostęp anonimowy, żądanie nigdy nie dociera do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-234">If an IIS site is configured to disallow anonymous access, the request never reaches the app.</span></span> <span data-ttu-id="7a9b3-235">Z tego powodu `[AllowAnonymous]` atrybut nie ma zastosowania.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-235">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="7a9b3-236">Zezwalaj na dostęp anonimowy</span><span class="sxs-lookup"><span data-stu-id="7a9b3-236">Allow anonymous access</span></span>

<span data-ttu-id="7a9b3-237">W przypadku włączenia uwierzytelniania systemu Windows i dostępu anonimowego należy użyć `[Authorize]` `[AllowAnonymous]` atrybutów i.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-237">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="7a9b3-238">Ten `[Authorize]` atrybut umożliwia zabezpieczenie punktów końcowych aplikacji, które wymagają uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-238">The `[Authorize]` attribute allows you to secure endpoints of the app which require authentication.</span></span> <span data-ttu-id="7a9b3-239">Ten `[AllowAnonymous]` atrybut zastępuje `[Authorize]` atrybut w aplikacjach, które zezwalają na dostęp anonimowy.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-239">The `[AllowAnonymous]` attribute overrides the `[Authorize]` attribute in apps that allow anonymous access.</span></span> <span data-ttu-id="7a9b3-240">Aby uzyskać szczegółowe informacje dotyczące użycia atrybutów, zobacz <xref:security/authorization/simple> .</span><span class="sxs-lookup"><span data-stu-id="7a9b3-240">For attribute usage details, see <xref:security/authorization/simple>.</span></span>

> [!NOTE]
> <span data-ttu-id="7a9b3-241">Domyślnie użytkownicy, którzy nie posiadają autoryzacji dostępu do strony, są wyświetlani z pustą odpowiedzią HTTP 403.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-241">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="7a9b3-242">[Oprogramowanie pośredniczące StatusCodePages](xref:fundamentals/error-handling#usestatuscodepages) można skonfigurować w celu zapewnienia użytkownikom lepszego środowiska "odmowa dostępu".</span><span class="sxs-lookup"><span data-stu-id="7a9b3-242">The [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

## <a name="impersonation"></a><span data-ttu-id="7a9b3-243">Personifikacja</span><span class="sxs-lookup"><span data-stu-id="7a9b3-243">Impersonation</span></span>

<span data-ttu-id="7a9b3-244">ASP.NET Core nie implementuje personifikacji.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-244">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="7a9b3-245">Aplikacje są uruchamiane z tożsamością aplikacji dla wszystkich żądań, przy użyciu puli aplikacji lub tożsamości procesu.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-245">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="7a9b3-246">Jeśli aplikacja powinna wykonać akcję w imieniu użytkownika, należy użyć [WindowsIdentity. RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) w ramach [wbudowanego oprogramowania terminala](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) w programie `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="7a9b3-246">If the app should perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="7a9b3-247">Uruchom pojedynczą akcję w tym kontekście, a następnie zamknij kontekst.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-247">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="7a9b3-248">`RunImpersonated`nie obsługuje operacji asynchronicznych i nie powinna być używana w scenariuszach złożonych.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-248">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="7a9b3-249">Na przykład Zawijanie całych żądań lub łańcuchów oprogramowania pośredniczącego nie jest obsługiwane ani zalecane.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-249">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a9b3-250">Podczas gdy pakiet [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) umożliwia uwierzytelnianie w systemach Windows, Linux i macOS, personifikacja jest obsługiwana tylko w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-250">While the [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package enables authentication on Windows, Linux, and macOS, impersonation is only supported on Windows.</span></span>

::: moniker-end

## <a name="claims-transformations"></a><span data-ttu-id="7a9b3-251">Przekształcenia oświadczeń</span><span class="sxs-lookup"><span data-stu-id="7a9b3-251">Claims transformations</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a9b3-252">W przypadku hostowania za pomocą usług IIS <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> nie jest wywoływana wewnętrznie w celu zainicjowania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-252">When hosting with IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="7a9b3-253">W związku z tym, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementacja użyta do przekształcenia oświadczeń po każdym uwierzytelnieniu nie jest domyślnie aktywowana.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-253">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="7a9b3-254">Aby uzyskać więcej informacji i przykład kodu, który aktywuje przekształcenia oświadczeń, zobacz <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .</span><span class="sxs-lookup"><span data-stu-id="7a9b3-254">For more information and a code example that activates claims transformations, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7a9b3-255">W przypadku hostowania z programem IIS w trybie przetwarzania <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> nie jest wywoływana wewnętrznie w celu zainicjowania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-255">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="7a9b3-256">W związku z tym, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementacja użyta do przekształcenia oświadczeń po każdym uwierzytelnieniu nie jest domyślnie aktywowana.</span><span class="sxs-lookup"><span data-stu-id="7a9b3-256">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="7a9b3-257">Aby uzyskać więcej informacji i przykład kodu, który aktywuje przekształcenia oświadczeń podczas hostingu w procesie, zobacz <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .</span><span class="sxs-lookup"><span data-stu-id="7a9b3-257">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="7a9b3-258">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="7a9b3-258">Additional resources</span></span>

* [<span data-ttu-id="7a9b3-259">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="7a9b3-259">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
