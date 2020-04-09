---
title: Host ASP.NET Core na Linuksie z Apache
author: rick-anderson
description: Dowiedz się, jak skonfigurować Apache jako odwrotny serwer proxy w CentOS, aby przekierować ruch HTTP do aplikacji sieci web ASP.NET Core działającej na Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 02/05/2020
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 3a3edd961b08c1952e6ded8038ed7ada381c54b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657900"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a><span data-ttu-id="80ccc-103">Host ASP.NET Core na Linuksie z Apache</span><span class="sxs-lookup"><span data-stu-id="80ccc-103">Host ASP.NET Core on Linux with Apache</span></span>

<span data-ttu-id="80ccc-104">Przez [Shayne Boyer](https://github.com/spboyer)</span><span class="sxs-lookup"><span data-stu-id="80ccc-104">By [Shayne Boyer](https://github.com/spboyer)</span></span>

<span data-ttu-id="80ccc-105">Korzystając z tego przewodnika, dowiedz się, jak skonfigurować [Apache](https://httpd.apache.org/) jako odwrotny serwer proxy w [systemie CentOS 7,](https://www.centos.org/) aby przekierować ruch HTTP do aplikacji sieci web ASP.NET Core działającej na serwerze [Kestrel.](xref:fundamentals/servers/kestrel)</span><span class="sxs-lookup"><span data-stu-id="80ccc-105">Using this guide, learn how to set up [Apache](https://httpd.apache.org/) as a reverse proxy server on [CentOS 7](https://www.centos.org/) to redirect HTTP traffic to an ASP.NET Core web app running on [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="80ccc-106">Rozszerzenie [mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) i powiązane moduły tworzą odwrotny serwer proxy serwera.</span><span class="sxs-lookup"><span data-stu-id="80ccc-106">The [mod_proxy extension](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) and related modules create the server's reverse proxy.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="80ccc-107">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="80ccc-107">Prerequisites</span></span>

* <span data-ttu-id="80ccc-108">Serwer z systemem CentOS 7 ze standardowym kontem użytkownika z uprawnieniami sudo.</span><span class="sxs-lookup"><span data-stu-id="80ccc-108">Server running CentOS 7 with a standard user account with sudo privilege.</span></span>
* <span data-ttu-id="80ccc-109">Zainstaluj środowisko uruchomieniowe .NET Core na serwerze.</span><span class="sxs-lookup"><span data-stu-id="80ccc-109">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="80ccc-110">Odwiedź [stronę Pobierz .NET Core](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="80ccc-110">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="80ccc-111">Wybierz najnowszą wersję .NET Core bez podglądu.</span><span class="sxs-lookup"><span data-stu-id="80ccc-111">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="80ccc-112">Pobierz najnowsze środowisko wykonawcze bez podglądu w tabeli w obszarze **Uruchamianie aplikacji - Środowisko wykonawcze**.</span><span class="sxs-lookup"><span data-stu-id="80ccc-112">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="80ccc-113">Wybierz **łącze z instrukcjami menedżera pakietów** systemu Linux i postępuj zgodnie z instrukcjami CentOS.</span><span class="sxs-lookup"><span data-stu-id="80ccc-113">Select the Linux **Package manager instructions** link and follow the CentOS instructions.</span></span>
* <span data-ttu-id="80ccc-114">Istniejąca aplikacja ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="80ccc-114">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="80ccc-115">W dowolnym momencie w przyszłości po uaktualnieniu udostępnionej struktury uruchom ponownie aplikacje ASP.NET Core hostowane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="80ccc-115">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="80ccc-116">Publikowanie i kopiowanie za pomocą aplikacji</span><span class="sxs-lookup"><span data-stu-id="80ccc-116">Publish and copy over the app</span></span>

<span data-ttu-id="80ccc-117">Skonfiguruj aplikację dla [wdrożenia zależnego od struktury](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="80ccc-117">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="80ccc-118">Jeśli aplikacja jest uruchamiana lokalnie i nie jest skonfigurowana do nawiązywać bezpieczne połączenia (HTTPS), należy przyjąć jedną z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="80ccc-118">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="80ccc-119">Skonfiguruj aplikację do obsługi bezpiecznych połączeń lokalnych.</span><span class="sxs-lookup"><span data-stu-id="80ccc-119">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="80ccc-120">Aby uzyskać więcej informacji, zobacz sekcję [konfiguracji HTTPS.](#https-configuration)</span><span class="sxs-lookup"><span data-stu-id="80ccc-120">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="80ccc-121">Usuń `https://localhost:5001` (jeśli jest `applicationUrl` obecny) z właściwości w *pliku Properties/launchSettings.json.*</span><span class="sxs-lookup"><span data-stu-id="80ccc-121">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="80ccc-122">Uruchom [dotnet publikowania](/dotnet/core/tools/dotnet-publish) ze środowiska programistycznego, aby spakować aplikację do katalogu (na przykład *&lt;bin/Release/ target_framework_moniker&gt;/publish),* który można uruchomić na serwerze:</span><span class="sxs-lookup"><span data-stu-id="80ccc-122">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="80ccc-123">Aplikacja może być również opublikowana jako [samodzielne wdrożenie,](/dotnet/core/deploying/#self-contained-deployments-scd) jeśli wolisz nie utrzymywać środowiska uruchomieniowego .NET Core na serwerze.</span><span class="sxs-lookup"><span data-stu-id="80ccc-123">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="80ccc-124">Skopiuj aplikację ASP.NET Core na serwer przy użyciu narzędzia integrujące się z przepływem pracy organizacji (na przykład SCP, SFTP).</span><span class="sxs-lookup"><span data-stu-id="80ccc-124">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="80ccc-125">Często lokalizuje się aplikacje internetowe w katalogu *var* (na przykład *var/www/helloapp).*</span><span class="sxs-lookup"><span data-stu-id="80ccc-125">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="80ccc-126">W scenariuszu wdrażania produkcyjnego przepływ pracy ciągłej integracji wykonuje pracę publikowania aplikacji i kopiowania zasobów na serwerze.</span><span class="sxs-lookup"><span data-stu-id="80ccc-126">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

## <a name="configure-a-proxy-server"></a><span data-ttu-id="80ccc-127">Konfigurowanie serwera proxy</span><span class="sxs-lookup"><span data-stu-id="80ccc-127">Configure a proxy server</span></span>

<span data-ttu-id="80ccc-128">Odwrócony serwer proxy to powszechna konfiguracja do wyświetlania dynamicznych aplikacji internetowych.</span><span class="sxs-lookup"><span data-stu-id="80ccc-128">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="80ccc-129">Odwrócony serwer proxy kończy żądanie HTTP i przekazuje go do aplikacji ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="80ccc-129">The reverse proxy terminates the HTTP request and forwards it to the ASP.NET app.</span></span>

<span data-ttu-id="80ccc-130">Serwer proxy to serwer, który przekazuje żądania klientów do innego serwera zamiast samych żądań.</span><span class="sxs-lookup"><span data-stu-id="80ccc-130">A proxy server is one which forwards client requests to another server instead of fulfilling requests itself.</span></span> <span data-ttu-id="80ccc-131">Odwrócony serwer proxy przekazuje do stałego miejsca docelowego, zazwyczaj w imieniu dowolnych klientów.</span><span class="sxs-lookup"><span data-stu-id="80ccc-131">A reverse proxy forwards to a fixed destination, typically on behalf of arbitrary clients.</span></span> <span data-ttu-id="80ccc-132">W tym przewodniku Apache jest skonfigurowany jako odwrotny serwer proxy uruchomiony na tym samym serwerze, który Kestrel obsługuje aplikację ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="80ccc-132">In this guide, Apache is configured as the reverse proxy running on the same server that Kestrel is serving the ASP.NET Core app.</span></span>

<span data-ttu-id="80ccc-133">Ponieważ żądania są przekazywane za pośrednictwem odwrotnego serwera proxy, użyj [oprogramowania pośredniczącego nagłówków przesyłanych dalej](xref:host-and-deploy/proxy-load-balancer) z pakietu [Microsoft.AspNetCore.HttpOverrides.](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/)</span><span class="sxs-lookup"><span data-stu-id="80ccc-133">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="80ccc-134">Oprogramowanie pośredniczące `Request.Scheme`aktualizuje `X-Forwarded-Proto` , używając nagłówka, dzięki czemu przekierowanie identyfikatorów URI i innych zasad zabezpieczeń działa poprawnie.</span><span class="sxs-lookup"><span data-stu-id="80ccc-134">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="80ccc-135">Każdy składnik, który zależy od schematu, takich jak uwierzytelnianie, generowanie łączy, przekierowania i geolokalizacja, musi być umieszczony po wywołaniu oprogramowania pośredniczącego nagłówków przesyłanych dalej.</span><span class="sxs-lookup"><span data-stu-id="80ccc-135">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span> <span data-ttu-id="80ccc-136">Zgodnie z ogólną zasadą oprogramowanie pośredniczące nagłówków przesyłanych dalej powinno być uruchamiane przed innym oprogramowaniem pośredniczącym, z wyjątkiem diagnostyki i obsługi błędów oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="80ccc-136">As a general rule, Forwarded Headers Middleware should run before other middleware except diagnostics and error handling middleware.</span></span> <span data-ttu-id="80ccc-137">Ta kolejność gwarantuje, że oprogramowanie pośredniczące korzystające z informacji o nagłówkach przesyłanych dalej może zużywać wartości nagłówka do przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="80ccc-137">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span>

<span data-ttu-id="80ccc-138">Wywołaj <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodę `Startup.Configure` przed <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> wywołaniem lub podobnym oprogramowaniem pośredniczącym schematu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="80ccc-138">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or similar authentication scheme middleware.</span></span> <span data-ttu-id="80ccc-139">Skonfiguruj oprogramowanie `X-Forwarded-For` pośredniczące do przekazania nagłówków i `X-Forwarded-Proto` nagłówków:</span><span class="sxs-lookup"><span data-stu-id="80ccc-139">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="80ccc-140">Jeśli <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> oprogramowanie pośredniczące nie jest określone, domyślnymi nagłówkami do przekazania są `None`.</span><span class="sxs-lookup"><span data-stu-id="80ccc-140">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="80ccc-141">Serwery proxy uruchomione na adresach sprzężenia zwrotnego (127.0.0.0/8, [::1]), w tym standardowy adres localhost (127.0.0.1), są domyślnie zaufane.</span><span class="sxs-lookup"><span data-stu-id="80ccc-141">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="80ccc-142">Jeśli inne zaufane serwery proxy lub sieci w organizacji obsługują żądania między Internetem <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>serwerem sieci web, dodaj je do listy lub z programem .</span><span class="sxs-lookup"><span data-stu-id="80ccc-142">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="80ccc-143">Poniższy przykład dodaje zaufany serwer proxy pod adresem IP 10.0.0.100 `KnownProxies` `Startup.ConfigureServices`do oprogramowania pośredniczącego Nagłówków przesyłanych dalej w:</span><span class="sxs-lookup"><span data-stu-id="80ccc-143">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="80ccc-144">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="80ccc-144">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-apache"></a><span data-ttu-id="80ccc-145">Instalowanie apache</span><span class="sxs-lookup"><span data-stu-id="80ccc-145">Install Apache</span></span>

<span data-ttu-id="80ccc-146">Aktualizuj pakiety CentOS do ich najnowszych stabilnych wersji:</span><span class="sxs-lookup"><span data-stu-id="80ccc-146">Update CentOS packages to their latest stable versions:</span></span>

```bash
sudo yum update -y
```

<span data-ttu-id="80ccc-147">Zainstaluj serwer www Apache na CentOS za pomocą jednego `yum` polecenia:</span><span class="sxs-lookup"><span data-stu-id="80ccc-147">Install the Apache web server on CentOS with a single `yum` command:</span></span>

```bash
sudo yum -y install httpd mod_ssl
```

<span data-ttu-id="80ccc-148">Przykładowe dane wyjściowe po uruchomieniu polecenia:</span><span class="sxs-lookup"><span data-stu-id="80ccc-148">Sample output after running the command:</span></span>

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> <span data-ttu-id="80ccc-149">W tym przykładzie dane wyjściowe odzwierciedla httpd.86_64 ponieważ wersja CentOS 7 jest 64 bit.</span><span class="sxs-lookup"><span data-stu-id="80ccc-149">In this example, the output reflects httpd.86_64 since the CentOS 7 version is 64 bit.</span></span> <span data-ttu-id="80ccc-150">Aby sprawdzić, gdzie jest zainstalowany `whereis httpd` apache, uruchom z wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="80ccc-150">To verify where Apache is installed, run `whereis httpd` from a command prompt.</span></span>

### <a name="configure-apache"></a><span data-ttu-id="80ccc-151">Konfigurowanie apache</span><span class="sxs-lookup"><span data-stu-id="80ccc-151">Configure Apache</span></span>

<span data-ttu-id="80ccc-152">Pliki konfiguracyjne apache `/etc/httpd/conf.d/` znajdują się w katalogu.</span><span class="sxs-lookup"><span data-stu-id="80ccc-152">Configuration files for Apache are located within the `/etc/httpd/conf.d/` directory.</span></span> <span data-ttu-id="80ccc-153">Każdy plik z rozszerzeniem *.conf* jest przetwarzany w kolejności alfabetycznej oprócz plików konfiguracyjnych modułu w `/etc/httpd/conf.modules.d/`, który zawiera wszelkie pliki konfiguracyjne niezbędne do załadowania modułów.</span><span class="sxs-lookup"><span data-stu-id="80ccc-153">Any file with the *.conf* extension is processed in alphabetical order in addition to the module configuration files in `/etc/httpd/conf.modules.d/`, which contains any configuration files necessary to load modules.</span></span>

<span data-ttu-id="80ccc-154">Utwórz plik konfiguracyjny o nazwie *helloapp.conf*dla aplikacji:</span><span class="sxs-lookup"><span data-stu-id="80ccc-154">Create a configuration file, named *helloapp.conf*, for the app:</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}helloapp-error.log
    CustomLog ${APACHE_LOG_DIR}helloapp-access.log common
</VirtualHost>
```

<span data-ttu-id="80ccc-155">Blok `VirtualHost` może pojawić się wiele razy, w jednym lub kilku plikach na serwerze.</span><span class="sxs-lookup"><span data-stu-id="80ccc-155">The `VirtualHost` block can appear multiple times, in one or more files on a server.</span></span> <span data-ttu-id="80ccc-156">W poprzednim pliku konfiguracyjnym Apache akceptuje ruch publiczny na porcie 80.</span><span class="sxs-lookup"><span data-stu-id="80ccc-156">In the preceding configuration file, Apache accepts public traffic on port 80.</span></span> <span data-ttu-id="80ccc-157">Domena `www.example.com` jest obsługiwana, `*.example.com` a alias jest rozpoznawany w tej samej witrynie sieci Web.</span><span class="sxs-lookup"><span data-stu-id="80ccc-157">The domain `www.example.com` is being served, and the `*.example.com` alias resolves to the same website.</span></span> <span data-ttu-id="80ccc-158">Aby uzyskać więcej informacji, zobacz [obsługa hosta wirtualnego opartego](https://httpd.apache.org/docs/current/vhosts/name-based.html) na nazwach.</span><span class="sxs-lookup"><span data-stu-id="80ccc-158">See [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html) for more information.</span></span> <span data-ttu-id="80ccc-159">Żądania są przenoszone przez katalog główny do portu 5000 serwera o godzinie 127.0.0.1.</span><span class="sxs-lookup"><span data-stu-id="80ccc-159">Requests are proxied at the root to port 5000 of the server at 127.0.0.1.</span></span> <span data-ttu-id="80ccc-160">Do komunikacji dwukierunkowej `ProxyPass` `ProxyPassReverse` i są wymagane.</span><span class="sxs-lookup"><span data-stu-id="80ccc-160">For bi-directional communication, `ProxyPass` and `ProxyPassReverse` are required.</span></span> <span data-ttu-id="80ccc-161">Aby zmienić adres IP/port Kestrel, zobacz [Kestrel: Konfiguracja punktu końcowego](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="80ccc-161">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="80ccc-162">Niepodjęcie odpowiedniej [dyrektywy ServerName](https://httpd.apache.org/docs/current/mod/core.html#servername) w bloku **VirtualHost** udostępnia aplikację na luki w zabezpieczeniach.</span><span class="sxs-lookup"><span data-stu-id="80ccc-162">Failure to specify a proper [ServerName directive](https://httpd.apache.org/docs/current/mod/core.html#servername) in the **VirtualHost** block exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="80ccc-163">Powiązanie symboli wieloznacznych `*.example.com`poddomeny (na przykład) nie stanowi tego zagrożenia bezpieczeństwa, jeśli kontrolujesz całą domenę nadrzędną (w przeciwieństwie do `*.com`, która jest narażona).</span><span class="sxs-lookup"><span data-stu-id="80ccc-163">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="80ccc-164">Zobacz [rfc7230 section-5.4,](https://tools.ietf.org/html/rfc7230#section-5.4) aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="80ccc-164">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="80ccc-165">Rejestrowanie można skonfigurować `VirtualHost` `ErrorLog` na `CustomLog` using i dyrektyw.</span><span class="sxs-lookup"><span data-stu-id="80ccc-165">Logging can be configured per `VirtualHost` using `ErrorLog` and `CustomLog` directives.</span></span> <span data-ttu-id="80ccc-166">`ErrorLog`to lokalizacja, w której serwer rejestruje `CustomLog` błędy i ustawia nazwę pliku i format pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="80ccc-166">`ErrorLog` is the location where the server logs errors, and `CustomLog` sets the filename and format of log file.</span></span> <span data-ttu-id="80ccc-167">W takim przypadku jest to miejsce, w którym rejestrowane są informacje o żądaniu.</span><span class="sxs-lookup"><span data-stu-id="80ccc-167">In this case, this is where request information is logged.</span></span> <span data-ttu-id="80ccc-168">Istnieje jeden wiersz dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="80ccc-168">There's one line for each request.</span></span>

<span data-ttu-id="80ccc-169">Zapisz plik i przetestuj konfigurację.</span><span class="sxs-lookup"><span data-stu-id="80ccc-169">Save the file and test the configuration.</span></span> <span data-ttu-id="80ccc-170">Jeśli wszystko przejdzie, odpowiedź `Syntax [OK]`powinna być .</span><span class="sxs-lookup"><span data-stu-id="80ccc-170">If everything passes, the response should be `Syntax [OK]`.</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="80ccc-171">Uruchom ponownie apache:</span><span class="sxs-lookup"><span data-stu-id="80ccc-171">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a><span data-ttu-id="80ccc-172">Monitorowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="80ccc-172">Monitor the app</span></span>

<span data-ttu-id="80ccc-173">Apache jest teraz skonfigurowany `http://localhost:80` do przekazywania żądań do aplikacji ASP.NET `http://127.0.0.1:5000`Core uruchomionej na Kestrel w .</span><span class="sxs-lookup"><span data-stu-id="80ccc-173">Apache is now setup to forward requests made to `http://localhost:80` to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="80ccc-174">Jednak Apache nie jest skonfigurowany do zarządzania procesem Kestrel.</span><span class="sxs-lookup"><span data-stu-id="80ccc-174">However, Apache isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="80ccc-175">Użyj *systemd* i utworzyć plik usługi, aby uruchomić i monitorować podstawowej aplikacji sieci web.</span><span class="sxs-lookup"><span data-stu-id="80ccc-175">Use *systemd* and create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="80ccc-176">*systemd* to system init, który zapewnia wiele zaawansowanych funkcji uruchamiania, zatrzymywania i zarządzania procesami.</span><span class="sxs-lookup"><span data-stu-id="80ccc-176">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span>

### <a name="create-the-service-file"></a><span data-ttu-id="80ccc-177">Tworzenie pliku usługi</span><span class="sxs-lookup"><span data-stu-id="80ccc-177">Create the service file</span></span>

<span data-ttu-id="80ccc-178">Utwórz plik definicji usługi:</span><span class="sxs-lookup"><span data-stu-id="80ccc-178">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="80ccc-179">Przykładowy plik usługi dla aplikacji:</span><span class="sxs-lookup"><span data-stu-id="80ccc-179">An example service file for the app:</span></span>

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="80ccc-180">W poprzednim przykładzie użytkownik, który zarządza usługą `User` jest określony przez opcję.</span><span class="sxs-lookup"><span data-stu-id="80ccc-180">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="80ccc-181">Użytkownik (`apache`) musi istnieć i mieć odpowiednie prawo własności do plików aplikacji.</span><span class="sxs-lookup"><span data-stu-id="80ccc-181">The user (`apache`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="80ccc-182">Służy `TimeoutStopSec` do konfigurowania czasu oczekiwania na zamknięcie aplikacji po odebraniu początkowego sygnału przerwania.</span><span class="sxs-lookup"><span data-stu-id="80ccc-182">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="80ccc-183">Jeśli aplikacja nie zostanie zamknięta w tym okresie, SIGKILL zostanie wystawiony w celu zakończenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="80ccc-183">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="80ccc-184">Podaj wartość jako sekundy `150`bez jednostek (na przykład), wartość przedziału czasu (na `2min 30s`przykład) lub `infinity` wyłącz limit czasu.</span><span class="sxs-lookup"><span data-stu-id="80ccc-184">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="80ccc-185">`TimeoutStopSec`domyślnie wartość w `DefaultTimeoutStopSec` pliku konfiguracyjnym menedżera (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span><span class="sxs-lookup"><span data-stu-id="80ccc-185">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="80ccc-186">Domyślny limit czasu dla większości dystrybucji wynosi 90 sekund.</span><span class="sxs-lookup"><span data-stu-id="80ccc-186">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="80ccc-187">Niektóre wartości (na przykład parametry połączenia SQL) muszą zostać zmienione dla dostawców konfiguracji, aby odczytać zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="80ccc-187">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="80ccc-188">Użyj następującego polecenia, aby wygenerować poprawnie odsunącą wartość do użycia w pliku konfiguracyjnym:</span><span class="sxs-lookup"><span data-stu-id="80ccc-188">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

<span data-ttu-id="80ccc-189">Separatory`:`dwukropek ( ) nie są obsługiwane w nazwach zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="80ccc-189">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="80ccc-190">Użyj podwójnego podkreślenia (`__`) zamiast jelita grubego.</span><span class="sxs-lookup"><span data-stu-id="80ccc-190">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="80ccc-191">[Dostawca konfiguracji zmiennych środowiskowych](xref:fundamentals/configuration/index#environment-variables-configuration-provider) konwertuje podwójne podkreślenia na dwukropki, gdy zmienne środowiskowe są odczytywane do konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="80ccc-191">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="80ccc-192">W poniższym przykładzie klucz `ConnectionStrings:DefaultConnection` parametry połączenia jest ustawiony `ConnectionStrings__DefaultConnection`w pliku definicji usługi jako:</span><span class="sxs-lookup"><span data-stu-id="80ccc-192">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="80ccc-193">Zapisz plik i włącz usługę:</span><span class="sxs-lookup"><span data-stu-id="80ccc-193">Save the file and enable the service:</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="80ccc-194">Uruchom usługę i sprawdź, czy jest uruchomiona:</span><span class="sxs-lookup"><span data-stu-id="80ccc-194">Start the service and verify that it's running:</span></span>

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="80ccc-195">Dzięki skonfigurowanego odwrotnego serwera proxy i Kestrel zarządzanej przez *systemd,* aplikacja internetowa jest w `http://localhost`pełni skonfigurowana i można uzyskać do niej dostęp z przeglądarki na komputerze lokalnym pod adresem .</span><span class="sxs-lookup"><span data-stu-id="80ccc-195">With the reverse proxy configured and Kestrel managed through *systemd*, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="80ccc-196">Sprawdzając nagłówki odpowiedzi, nagłówek **serwera** wskazuje, że aplikacja ASP.NET Core jest obsługiwana przez Kestrel:</span><span class="sxs-lookup"><span data-stu-id="80ccc-196">Inspecting the response headers, the **Server** header indicates that the ASP.NET Core app is served by Kestrel:</span></span>

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="80ccc-197">Wyświetlanie dzienników</span><span class="sxs-lookup"><span data-stu-id="80ccc-197">View logs</span></span>

<span data-ttu-id="80ccc-198">Ponieważ aplikacja internetowa przy użyciu Kestrel jest zarządzana za pomocą *systemd,* zdarzenia i procesy są rejestrowane w scentralizowanym dzienniku.</span><span class="sxs-lookup"><span data-stu-id="80ccc-198">Since the web app using Kestrel is managed using *systemd*, events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="80ccc-199">Jednak ten arkusz zawiera wpisy dla wszystkich usług i procesów zarządzanych przez *systemd*.</span><span class="sxs-lookup"><span data-stu-id="80ccc-199">However, this journal includes entries for all of the services and processes managed by *systemd*.</span></span> <span data-ttu-id="80ccc-200">Aby wyświetlić elementy określone w `kestrel-helloapp.service`poszczególnych, użyj następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="80ccc-200">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="80ccc-201">W przypadku filtrowania czasu należy określić opcje czasu za pomocą polecenia.</span><span class="sxs-lookup"><span data-stu-id="80ccc-201">For time filtering, specify time options with the command.</span></span> <span data-ttu-id="80ccc-202">Na przykład `--since today` służy do filtrowania `--until 1 hour ago` dla bieżącego dnia lub do wyświetlenia wpisów z poprzedniej godziny.</span><span class="sxs-lookup"><span data-stu-id="80ccc-202">For example, use `--since today` to filter for the current day or `--until 1 hour ago` to see the previous hour's entries.</span></span> <span data-ttu-id="80ccc-203">Aby uzyskać więcej informacji, zobacz [stronę man dla journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span><span class="sxs-lookup"><span data-stu-id="80ccc-203">For more information, see the [man page for journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="80ccc-204">Ochrona danych</span><span class="sxs-lookup"><span data-stu-id="80ccc-204">Data protection</span></span>

<span data-ttu-id="80ccc-205">[Stos ASP.NET Core Data Protection](xref:security/data-protection/introduction) jest używany przez kilka ASP.NET core middleware , w tym oprogramowanie [pośredniczące](xref:fundamentals/middleware/index)uwierzytelniania (na przykład oprogramowanie pośredniczące plików cookie) i zabezpieczenia fałszowania żądań między witrynami (CSRF).</span><span class="sxs-lookup"><span data-stu-id="80ccc-205">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="80ccc-206">Nawet jeśli interfejsy API ochrony danych nie są wywoływane przez kod użytkownika, ochrona danych powinna być skonfigurowana do tworzenia trwałego [magazynu kluczy](xref:security/data-protection/implementation/key-management)kryptograficznych .</span><span class="sxs-lookup"><span data-stu-id="80ccc-206">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="80ccc-207">Jeśli ochrona danych nie jest skonfigurowana, klucze są przechowywane w pamięci i odrzucane po ponownym uruchomieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="80ccc-207">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="80ccc-208">Jeśli pierścień klucza jest przechowywany w pamięci po ponownym uruchomieniu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="80ccc-208">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="80ccc-209">Wszystkie tokeny uwierzytelniania oparte na plikach cookie są unieważnione.</span><span class="sxs-lookup"><span data-stu-id="80ccc-209">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="80ccc-210">Użytkownicy muszą zalogować się ponownie przy następnym żądaniu.</span><span class="sxs-lookup"><span data-stu-id="80ccc-210">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="80ccc-211">Nie można już odszyfrować żadnych danych chronionych za pomocą pierścienia kluczy.</span><span class="sxs-lookup"><span data-stu-id="80ccc-211">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="80ccc-212">Może to obejmować [tokeny CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) i [ASP.NET podstawowe pliki cookie MVC TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="80ccc-212">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="80ccc-213">Aby skonfigurować ochronę danych do utrwalania i szyfrowania pierścienia kluczy, zobacz:</span><span class="sxs-lookup"><span data-stu-id="80ccc-213">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a><span data-ttu-id="80ccc-214">Zabezpiecz aplikację</span><span class="sxs-lookup"><span data-stu-id="80ccc-214">Secure the app</span></span>

### <a name="configure-firewall"></a><span data-ttu-id="80ccc-215">Konfigurowanie zapory</span><span class="sxs-lookup"><span data-stu-id="80ccc-215">Configure firewall</span></span>

<span data-ttu-id="80ccc-216">*Firewalld* to dynamiczny demon do zarządzania zaporą z obsługą stref sieciowych.</span><span class="sxs-lookup"><span data-stu-id="80ccc-216">*Firewalld* is a dynamic daemon to manage the firewall with support for network zones.</span></span> <span data-ttu-id="80ccc-217">Portami i filtrowania pakietów nadal mogą być zarządzane przez iptables.</span><span class="sxs-lookup"><span data-stu-id="80ccc-217">Ports and packet filtering can still be managed by iptables.</span></span> <span data-ttu-id="80ccc-218">*Zapórka* powinna być zainstalowana domyślnie.</span><span class="sxs-lookup"><span data-stu-id="80ccc-218">*Firewalld* should be installed by default.</span></span> <span data-ttu-id="80ccc-219">`yum`można użyć do zainstalowania pakietu lub sprawdzenia, czy jest zainstalowany.</span><span class="sxs-lookup"><span data-stu-id="80ccc-219">`yum` can be used to install the package or verify it's installed.</span></span>

```bash
sudo yum install firewalld -y
```

<span data-ttu-id="80ccc-220">Służy `firewalld` do otwierania tylko portów potrzebnych do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="80ccc-220">Use `firewalld` to open only the ports needed for the app.</span></span> <span data-ttu-id="80ccc-221">W takim przypadku używane są porty 80 i 443.</span><span class="sxs-lookup"><span data-stu-id="80ccc-221">In this case, port 80 and 443 are used.</span></span> <span data-ttu-id="80ccc-222">Następujące polecenia na stałe ustawiają porty 80 i 443, aby otworzyć:</span><span class="sxs-lookup"><span data-stu-id="80ccc-222">The following commands permanently set ports 80 and 443 to open:</span></span>

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

<span data-ttu-id="80ccc-223">Ponownie załaduj ustawienia zapory.</span><span class="sxs-lookup"><span data-stu-id="80ccc-223">Reload the firewall settings.</span></span> <span data-ttu-id="80ccc-224">Sprawdź dostępne usługi i porty w strefie domyślnej.</span><span class="sxs-lookup"><span data-stu-id="80ccc-224">Check the available services and ports in the default zone.</span></span> <span data-ttu-id="80ccc-225">Opcje są dostępne `firewall-cmd -h`po sprawdzeniu .</span><span class="sxs-lookup"><span data-stu-id="80ccc-225">Options are available by inspecting `firewall-cmd -h`.</span></span>

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="https-configuration"></a><span data-ttu-id="80ccc-226">Konfiguracja PROTOKOŁU HTTPS</span><span class="sxs-lookup"><span data-stu-id="80ccc-226">HTTPS configuration</span></span>

<span data-ttu-id="80ccc-227">**Konfigurowanie aplikacji do bezpiecznych połączeń lokalnych (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="80ccc-227">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="80ccc-228">Polecenie [dotnet run](/dotnet/core/tools/dotnet-run) używa pliku *Properties/launchSettings.json* aplikacji, który konfiguruje aplikację do nasłuchiwania na adresach URL dostarczonych przez `applicationUrl` właściwość (na przykład `https://localhost:5001;http://localhost:5000`).</span><span class="sxs-lookup"><span data-stu-id="80ccc-228">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="80ccc-229">Skonfiguruj aplikację do używania `dotnet run` certyfikatu w środowisku deweloperskim (F5 lub Ctrl+F5 w programie Visual Studio Code) przy użyciu jednego z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="80ccc-229">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="80ccc-230">[Zastąp domyślny certyfikat z konfiguracji](xref:fundamentals/servers/kestrel#configuration) (*Zalecane*)</span><span class="sxs-lookup"><span data-stu-id="80ccc-230">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="80ccc-231">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="80ccc-231">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="80ccc-232">**Konfigurowanie odwrotnego serwera proxy dla bezpiecznych połączeń klientów (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="80ccc-232">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

<span data-ttu-id="80ccc-233">Aby skonfigurować Apache dla protokołu HTTPS, używany jest moduł *mod_ssl.*</span><span class="sxs-lookup"><span data-stu-id="80ccc-233">To configure Apache for HTTPS, the *mod_ssl* module is used.</span></span> <span data-ttu-id="80ccc-234">Po zainstalowaniu modułu *httpd* zainstalowano również moduł *mod_ssl.*</span><span class="sxs-lookup"><span data-stu-id="80ccc-234">When the *httpd* module was installed, the *mod_ssl* module was also installed.</span></span> <span data-ttu-id="80ccc-235">Jeśli nie został zainstalowany, `yum` użyj, aby dodać go do konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="80ccc-235">If it wasn't installed, use `yum` to add it to the configuration.</span></span>

```bash
sudo yum install mod_ssl
```

<span data-ttu-id="80ccc-236">Aby wymusić https, należy zainstalować moduł, `mod_rewrite` aby włączyć przepisywanie adresów URL:</span><span class="sxs-lookup"><span data-stu-id="80ccc-236">To enforce HTTPS, install the `mod_rewrite` module to enable URL rewriting:</span></span>

```bash
sudo yum install mod_rewrite
```

<span data-ttu-id="80ccc-237">Zmodyfikuj plik *helloapp.conf,* aby włączyć przepisywanie adresów URL i bezpieczną komunikację na porcie 443:</span><span class="sxs-lookup"><span data-stu-id="80ccc-237">Modify the *helloapp.conf* file to enable URL rewriting and secure communication on port 443:</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> <span data-ttu-id="80ccc-238">W tym przykładzie jest przy użyciu certyfikatu generowane lokalnie.</span><span class="sxs-lookup"><span data-stu-id="80ccc-238">This example is using a locally-generated certificate.</span></span> <span data-ttu-id="80ccc-239">**SSLCertificateFile** powinien być podstawowym plikiem certyfikatu dla nazwy domeny.</span><span class="sxs-lookup"><span data-stu-id="80ccc-239">**SSLCertificateFile** should be the primary certificate file for the domain name.</span></span> <span data-ttu-id="80ccc-240">**SSLCertificateKeyFile** powinien być plikiem klucza generowanym podczas tworzenia csr.</span><span class="sxs-lookup"><span data-stu-id="80ccc-240">**SSLCertificateKeyFile** should be the key file generated when CSR is created.</span></span> <span data-ttu-id="80ccc-241">**SSLCertificateChainFile** powinien być pośrednim plikiem certyfikatu (jeśli istnieje), który został dostarczony przez urząd certyfikacji.</span><span class="sxs-lookup"><span data-stu-id="80ccc-241">**SSLCertificateChainFile** should be the intermediate certificate file (if any) that was supplied by the certificate authority.</span></span>

<span data-ttu-id="80ccc-242">Zapisz plik i przetestuj konfigurację:</span><span class="sxs-lookup"><span data-stu-id="80ccc-242">Save the file and test the configuration:</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="80ccc-243">Uruchom ponownie apache:</span><span class="sxs-lookup"><span data-stu-id="80ccc-243">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a><span data-ttu-id="80ccc-244">Dodatkowe sugestie Apache</span><span class="sxs-lookup"><span data-stu-id="80ccc-244">Additional Apache suggestions</span></span>

### <a name="restart-apps-with-shared-framework-updates"></a><span data-ttu-id="80ccc-245">Ponowne uruchamianie aplikacji z udostępnionymi aktualizacjami struktury</span><span class="sxs-lookup"><span data-stu-id="80ccc-245">Restart apps with shared framework updates</span></span>

<span data-ttu-id="80ccc-246">Po uaktualnieniu udostępnionej struktury na serwerze uruchom ponownie aplikacje ASP.NET Core hostowane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="80ccc-246">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

### <a name="additional-headers"></a><span data-ttu-id="80ccc-247">Dodatkowe nagłówki</span><span class="sxs-lookup"><span data-stu-id="80ccc-247">Additional headers</span></span>

<span data-ttu-id="80ccc-248">Aby zabezpieczyć się przed złośliwymi atakami, istnieje kilka nagłówków, które powinny zostać zmodyfikowane lub dodane.</span><span class="sxs-lookup"><span data-stu-id="80ccc-248">In order to secure against malicious attacks, there are a few headers that should either be modified or added.</span></span> <span data-ttu-id="80ccc-249">Upewnij się, że `mod_headers` moduł jest zainstalowany:</span><span class="sxs-lookup"><span data-stu-id="80ccc-249">Ensure that the `mod_headers` module is installed:</span></span>

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a><span data-ttu-id="80ccc-250">Bezpieczne Apache przed atakami typu clickjacking</span><span class="sxs-lookup"><span data-stu-id="80ccc-250">Secure Apache from clickjacking attacks</span></span>

<span data-ttu-id="80ccc-251">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), znany również jako *atak zadośćuczynienia interfejsu*użytkownika , to złośliwy atak, w którym użytkownik witryny zostaje oszukany, klikając link lub przycisk na innej stronie niż obecnie odwiedza.</span><span class="sxs-lookup"><span data-stu-id="80ccc-251">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="80ccc-252">Służy `X-FRAME-OPTIONS` do zabezpieczania witryny.</span><span class="sxs-lookup"><span data-stu-id="80ccc-252">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="80ccc-253">Aby ograniczyć ataki typu clickjacking:</span><span class="sxs-lookup"><span data-stu-id="80ccc-253">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="80ccc-254">Edytuj plik *httpd.conf:*</span><span class="sxs-lookup"><span data-stu-id="80ccc-254">Edit the *httpd.conf* file:</span></span>

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   <span data-ttu-id="80ccc-255">Dodaj wiersz `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span><span class="sxs-lookup"><span data-stu-id="80ccc-255">Add the line `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span></span>
1. <span data-ttu-id="80ccc-256">Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="80ccc-256">Save the file.</span></span>
1. <span data-ttu-id="80ccc-257">Uruchom ponownie apache.</span><span class="sxs-lookup"><span data-stu-id="80ccc-257">Restart Apache.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="80ccc-258">Wąchanie typu MIME</span><span class="sxs-lookup"><span data-stu-id="80ccc-258">MIME-type sniffing</span></span>

<span data-ttu-id="80ccc-259">Nagłówek `X-Content-Type-Options` zapobiega *wąchaniu programu* Internet Explorer do mime `Content-Type` (określanie pliku z zawartości pliku).</span><span class="sxs-lookup"><span data-stu-id="80ccc-259">The `X-Content-Type-Options` header prevents Internet Explorer from *MIME-sniffing* (determining a file's `Content-Type` from the file's content).</span></span> <span data-ttu-id="80ccc-260">Jeśli serwer ustawi `Content-Type` `text/html` nagłówek `nosniff` z zestawem opcji, program `text/html` Internet Explorer renderuje zawartość jako niezależnie od zawartości pliku.</span><span class="sxs-lookup"><span data-stu-id="80ccc-260">If the server sets the `Content-Type` header to `text/html` with the `nosniff` option set, Internet Explorer renders the content as `text/html` regardless of the file's content.</span></span>

<span data-ttu-id="80ccc-261">Edytuj plik *httpd.conf:*</span><span class="sxs-lookup"><span data-stu-id="80ccc-261">Edit the *httpd.conf* file:</span></span>

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="80ccc-262">Dodaj wiersz `Header set X-Content-Type-Options "nosniff"`.</span><span class="sxs-lookup"><span data-stu-id="80ccc-262">Add the line `Header set X-Content-Type-Options "nosniff"`.</span></span> <span data-ttu-id="80ccc-263">Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="80ccc-263">Save the file.</span></span> <span data-ttu-id="80ccc-264">Uruchom ponownie apache.</span><span class="sxs-lookup"><span data-stu-id="80ccc-264">Restart Apache.</span></span>

### <a name="load-balancing"></a><span data-ttu-id="80ccc-265">Równoważenie obciążenia</span><span class="sxs-lookup"><span data-stu-id="80ccc-265">Load Balancing</span></span>

<span data-ttu-id="80ccc-266">W tym przykładzie pokazano, jak skonfigurować apache na CentOS 7 i Kestrel na tym samym komputerze wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="80ccc-266">This example shows how to setup and configure Apache on CentOS 7 and Kestrel on the same instance machine.</span></span> <span data-ttu-id="80ccc-267">Aby nie mieć jednego punktu awarii; korzystanie *z mod_proxy_balancer* i modyfikowanie **VirtualHost** pozwoliłoby na zarządzanie wieloma wystąpieniami aplikacji internetowych za serwerem proxy Apache.</span><span class="sxs-lookup"><span data-stu-id="80ccc-267">In order to not have a single point of failure; using *mod_proxy_balancer* and modifying the **VirtualHost** would allow for managing multiple instances of the web apps behind the Apache proxy server.</span></span>

```bash
sudo yum install mod_proxy_balancer
```

<span data-ttu-id="80ccc-268">W pliku konfiguracyjnym pokazanym `helloapp` poniżej dodatkowe wystąpienie jest skonfigurowane do uruchamiania na porcie 5001.</span><span class="sxs-lookup"><span data-stu-id="80ccc-268">In the configuration file shown below, an additional instance of the `helloapp` is set up to run on port 5001.</span></span> <span data-ttu-id="80ccc-269">Sekcja *Proxy* jest ustawiona z konfiguracją balansu z dwoma członkami, aby równoważyć obciążenie *byrequests*.</span><span class="sxs-lookup"><span data-stu-id="80ccc-269">The *Proxy* section is set with a balancer configuration with two members to load balance *byrequests*.</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a><span data-ttu-id="80ccc-270">Limity stawek</span><span class="sxs-lookup"><span data-stu-id="80ccc-270">Rate Limits</span></span>

<span data-ttu-id="80ccc-271">Korzystając z *mod_ratelimit*, który jest zawarty w module *httpd,* przepustowość klientów może być ograniczona:</span><span class="sxs-lookup"><span data-stu-id="80ccc-271">Using *mod_ratelimit*, which is included in the *httpd* module, the bandwidth of clients can be limited:</span></span>

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

<span data-ttu-id="80ccc-272">Przykładowy plik ogranicza przepustowość jako 600 KB/s w lokalizacji głównej:</span><span class="sxs-lookup"><span data-stu-id="80ccc-272">The example file limits bandwidth as 600 KB/sec under the root location:</span></span>

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a><span data-ttu-id="80ccc-273">Pola nagłówka żądania długiego</span><span class="sxs-lookup"><span data-stu-id="80ccc-273">Long request header fields</span></span>

<span data-ttu-id="80ccc-274">Domyślne ustawienia serwera proxy zazwyczaj ograniczają pola nagłówka żądania do 8190 bajtów.</span><span class="sxs-lookup"><span data-stu-id="80ccc-274">Proxy server default settings typically limit request header fields to 8,190 bytes.</span></span> <span data-ttu-id="80ccc-275">Aplikacja może wymagać pól dłuższych niż domyślne (na przykład aplikacji korzystających z [usługi Azure Active Directory).](https://azure.microsoft.com/services/active-directory/)</span><span class="sxs-lookup"><span data-stu-id="80ccc-275">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="80ccc-276">Jeśli wymagane są dłuższe pola, dyrektywa [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) serwera proxy wymaga dostosowania.</span><span class="sxs-lookup"><span data-stu-id="80ccc-276">If longer fields are required, the proxy server's [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) directive requires adjustment.</span></span> <span data-ttu-id="80ccc-277">Wartość do zastosowania zależy od scenariusza.</span><span class="sxs-lookup"><span data-stu-id="80ccc-277">The value to apply depends on the scenario.</span></span> <span data-ttu-id="80ccc-278">Aby uzyskać więcej informacji, zapoznaj się z dokumentacją serwera.</span><span class="sxs-lookup"><span data-stu-id="80ccc-278">For more information, see your server's documentation.</span></span>

> [!WARNING]
> <span data-ttu-id="80ccc-279">Nie zwiększaj wartości domyślnej, `LimitRequestFieldSize` chyba że jest to konieczne.</span><span class="sxs-lookup"><span data-stu-id="80ccc-279">Don't increase the default value of `LimitRequestFieldSize` unless necessary.</span></span> <span data-ttu-id="80ccc-280">Zwiększenie wartości zwiększa ryzyko przepełnienia buforu (przepełnienie) i ataków typu "odmowa usługi" (DoS) przez złośliwych użytkowników.</span><span class="sxs-lookup"><span data-stu-id="80ccc-280">Increasing the value increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="80ccc-281">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="80ccc-281">Additional resources</span></span>

* [<span data-ttu-id="80ccc-282">Wymagania wstępne dla .NET Core w systemie Linux</span><span class="sxs-lookup"><span data-stu-id="80ccc-282">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
