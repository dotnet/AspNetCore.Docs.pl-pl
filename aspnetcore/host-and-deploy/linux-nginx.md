---
title: Host ASP.NET Core na Linuksie z Nginx
author: rick-anderson
description: Dowiedz się, jak skonfigurować Nginx jako odwrotny serwer proxy na Ubuntu 16.04, aby przekazywać ruch HTTP do aplikacji sieci web ASP.NET Core działającej na Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/10/2020
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: ceb2ad857649dcfa8d04420dcc37792495edc3ff
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81224027"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a><span data-ttu-id="15cf7-103">Host ASP.NET Core na Linuksie z Nginx</span><span class="sxs-lookup"><span data-stu-id="15cf7-103">Host ASP.NET Core on Linux with Nginx</span></span>

<span data-ttu-id="15cf7-104">Przez [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="15cf7-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="15cf7-105">W tym przewodniku wyjaśniono konfigurowanie środowiska ASP.NET Core gotowych do produkcji na serwerze Ubuntu 16.04.</span><span class="sxs-lookup"><span data-stu-id="15cf7-105">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 server.</span></span> <span data-ttu-id="15cf7-106">Te instrukcje prawdopodobnie działają z nowszymi wersjami Ubuntu, ale instrukcje nie zostały przetestowane w nowszych wersjach.</span><span class="sxs-lookup"><span data-stu-id="15cf7-106">These instructions likely work with newer versions of Ubuntu, but the instructions haven't been tested with newer versions.</span></span>

<span data-ttu-id="15cf7-107">Aby uzyskać informacje na temat innych dystrybucji linuksa obsługiwanych przez ASP.NET Core, zobacz [Wymagania wstępne dla .NET Core w systemie Linux](/dotnet/core/linux-prerequisites).</span><span class="sxs-lookup"><span data-stu-id="15cf7-107">For information on other Linux distributions supported by ASP.NET Core, see [Prerequisites for .NET Core on Linux](/dotnet/core/linux-prerequisites).</span></span>

> [!NOTE]
> <span data-ttu-id="15cf7-108">Dla Ubuntu 14.04, *nadzorca* jest zalecane jako rozwiązanie do monitorowania procesu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="15cf7-108">For Ubuntu 14.04, *supervisord* is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="15cf7-109">*systemd* nie jest dostępny na Ubuntu 14.04.</span><span class="sxs-lookup"><span data-stu-id="15cf7-109">*systemd* isn't available on Ubuntu 14.04.</span></span> <span data-ttu-id="15cf7-110">Instrukcje Ubuntu 14.04 można znaleźć w [poprzedniej wersji tego tematu](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span><span class="sxs-lookup"><span data-stu-id="15cf7-110">For Ubuntu 14.04 instructions, see the [previous version of this topic](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span></span>

<span data-ttu-id="15cf7-111">Ten przewodnik:</span><span class="sxs-lookup"><span data-stu-id="15cf7-111">This guide:</span></span>

* <span data-ttu-id="15cf7-112">Umieszcza istniejącą aplikację ASP.NET Core za odwrotnym serwerem proxy.</span><span class="sxs-lookup"><span data-stu-id="15cf7-112">Places an existing ASP.NET Core app behind a reverse proxy server.</span></span>
* <span data-ttu-id="15cf7-113">Konfiguruje odwrotny serwer proxy do przesyłania dalej żądań do serwera sieci Web Kestrel.</span><span class="sxs-lookup"><span data-stu-id="15cf7-113">Sets up the reverse proxy server to forward requests to the Kestrel web server.</span></span>
* <span data-ttu-id="15cf7-114">Zapewnia, że aplikacja internetowa działa podczas uruchamiania jako demon.</span><span class="sxs-lookup"><span data-stu-id="15cf7-114">Ensures the web app runs on startup as a daemon.</span></span>
* <span data-ttu-id="15cf7-115">Konfiguruje narzędzie do zarządzania procesami, aby ułatwić ponowne uruchomienie aplikacji sieci web.</span><span class="sxs-lookup"><span data-stu-id="15cf7-115">Configures a process management tool to help restart the web app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="15cf7-116">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="15cf7-116">Prerequisites</span></span>

1. <span data-ttu-id="15cf7-117">Dostęp do serwera Ubuntu 16.04 ze standardowym kontem użytkownika z uprawnieniami sudo.</span><span class="sxs-lookup"><span data-stu-id="15cf7-117">Access to an Ubuntu 16.04 server with a standard user account with sudo privilege.</span></span>
1. <span data-ttu-id="15cf7-118">Zainstaluj środowisko uruchomieniowe .NET Core na serwerze.</span><span class="sxs-lookup"><span data-stu-id="15cf7-118">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="15cf7-119">Odwiedź [stronę Pobierz .NET Core](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="15cf7-119">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="15cf7-120">Wybierz najnowszą wersję .NET Core bez podglądu.</span><span class="sxs-lookup"><span data-stu-id="15cf7-120">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="15cf7-121">Pobierz najnowsze środowisko wykonawcze bez podglądu w tabeli w obszarze **Uruchamianie aplikacji - Środowisko wykonawcze**.</span><span class="sxs-lookup"><span data-stu-id="15cf7-121">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="15cf7-122">Wybierz link **do instrukcji menedżera pakietów** Linuksa i postępuj zgodnie z instrukcjami Ubuntu dla swojej wersji Ubuntu.</span><span class="sxs-lookup"><span data-stu-id="15cf7-122">Select the Linux **Package manager instructions** link and follow the Ubuntu instructions for your version of Ubuntu.</span></span>
1. <span data-ttu-id="15cf7-123">Istniejąca aplikacja ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="15cf7-123">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="15cf7-124">W dowolnym momencie w przyszłości po uaktualnieniu udostępnionej struktury uruchom ponownie aplikacje ASP.NET Core hostowane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="15cf7-124">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="15cf7-125">Publikowanie i kopiowanie za pomocą aplikacji</span><span class="sxs-lookup"><span data-stu-id="15cf7-125">Publish and copy over the app</span></span>

<span data-ttu-id="15cf7-126">Skonfiguruj aplikację dla [wdrożenia zależnego od struktury](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="15cf7-126">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="15cf7-127">Jeśli aplikacja jest uruchamiana lokalnie i nie jest skonfigurowana do nawiązywać bezpieczne połączenia (HTTPS), należy przyjąć jedną z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="15cf7-127">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="15cf7-128">Skonfiguruj aplikację do obsługi bezpiecznych połączeń lokalnych.</span><span class="sxs-lookup"><span data-stu-id="15cf7-128">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="15cf7-129">Aby uzyskać więcej informacji, zobacz sekcję [konfiguracji HTTPS.](#https-configuration)</span><span class="sxs-lookup"><span data-stu-id="15cf7-129">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="15cf7-130">Usuń `https://localhost:5001` (jeśli jest `applicationUrl` obecny) z właściwości w *pliku Properties/launchSettings.json.*</span><span class="sxs-lookup"><span data-stu-id="15cf7-130">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="15cf7-131">Uruchom [dotnet publikowania](/dotnet/core/tools/dotnet-publish) ze środowiska programistycznego, aby spakować aplikację do katalogu (na przykład *&lt;bin/Release/ target_framework_moniker&gt;/publish),* który można uruchomić na serwerze:</span><span class="sxs-lookup"><span data-stu-id="15cf7-131">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="15cf7-132">Aplikacja może być również opublikowana jako [samodzielne wdrożenie,](/dotnet/core/deploying/#self-contained-deployments-scd) jeśli wolisz nie utrzymywać środowiska uruchomieniowego .NET Core na serwerze.</span><span class="sxs-lookup"><span data-stu-id="15cf7-132">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="15cf7-133">Skopiuj aplikację ASP.NET Core na serwer przy użyciu narzędzia integrujące się z przepływem pracy organizacji (na przykład SCP, SFTP).</span><span class="sxs-lookup"><span data-stu-id="15cf7-133">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="15cf7-134">Często lokalizuje się aplikacje internetowe w katalogu *var* (na przykład *var/www/helloapp).*</span><span class="sxs-lookup"><span data-stu-id="15cf7-134">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="15cf7-135">W scenariuszu wdrażania produkcyjnego przepływ pracy ciągłej integracji wykonuje pracę publikowania aplikacji i kopiowania zasobów na serwerze.</span><span class="sxs-lookup"><span data-stu-id="15cf7-135">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

<span data-ttu-id="15cf7-136">Przetestuj aplikację:</span><span class="sxs-lookup"><span data-stu-id="15cf7-136">Test the app:</span></span>

1. <span data-ttu-id="15cf7-137">W wierszu polecenia uruchom `dotnet <app_assembly>.dll`aplikację: .</span><span class="sxs-lookup"><span data-stu-id="15cf7-137">From the command line, run the app: `dotnet <app_assembly>.dll`.</span></span>
1. <span data-ttu-id="15cf7-138">W przeglądarce przejdź `http://<serveraddress>:<port>` do, aby sprawdzić, czy aplikacja działa w systemie Linux lokalnie.</span><span class="sxs-lookup"><span data-stu-id="15cf7-138">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux locally.</span></span>

## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="15cf7-139">Konfigurowanie odwrotnego serwera proxy</span><span class="sxs-lookup"><span data-stu-id="15cf7-139">Configure a reverse proxy server</span></span>

<span data-ttu-id="15cf7-140">Odwrócony serwer proxy to powszechna konfiguracja do wyświetlania dynamicznych aplikacji internetowych.</span><span class="sxs-lookup"><span data-stu-id="15cf7-140">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="15cf7-141">Odwrócony serwer proxy kończy żądanie HTTP i przekazuje go do aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="15cf7-141">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core app.</span></span>

### <a name="use-a-reverse-proxy-server"></a><span data-ttu-id="15cf7-142">Używanie odwrotnego serwera proxy</span><span class="sxs-lookup"><span data-stu-id="15cf7-142">Use a reverse proxy server</span></span>

<span data-ttu-id="15cf7-143">Pustułka doskonale nadaje się do wyświetlania dynamicznych treści z ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="15cf7-143">Kestrel is great for serving dynamic content from ASP.NET Core.</span></span> <span data-ttu-id="15cf7-144">Jednak możliwości obsługi sieci Web nie są tak bogate w funkcje jak serwery takie jak IIS, Apache lub Nginx.</span><span class="sxs-lookup"><span data-stu-id="15cf7-144">However, the web serving capabilities aren't as feature rich as servers such as IIS, Apache, or Nginx.</span></span> <span data-ttu-id="15cf7-145">Serwer odwrotnego serwera proxy może odciążać pracę, taką jak obsługa zawartości statycznej, żądania buforowania, kompresowanie żądań i zakończenie protokołu HTTPS z serwera HTTPS.</span><span class="sxs-lookup"><span data-stu-id="15cf7-145">A reverse proxy server can offload work such as serving static content, caching requests, compressing requests, and HTTPS termination from the HTTP server.</span></span> <span data-ttu-id="15cf7-146">Serwer odwrotnego serwera proxy może znajdować się na dedykowanym komputerze lub może zostać wdrożony wraz z serwerem HTTP.</span><span class="sxs-lookup"><span data-stu-id="15cf7-146">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="15cf7-147">Na potrzeby tego przewodnika używane jest pojedyncze wystąpienie Nginx.</span><span class="sxs-lookup"><span data-stu-id="15cf7-147">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="15cf7-148">Działa na tym samym serwerze, obok serwera HTTP.</span><span class="sxs-lookup"><span data-stu-id="15cf7-148">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="15cf7-149">W zależności od wymagań można wybrać inną konfigurację.</span><span class="sxs-lookup"><span data-stu-id="15cf7-149">Based on requirements, a different setup may be chosen.</span></span>

<span data-ttu-id="15cf7-150">Ponieważ żądania są przekazywane za pośrednictwem odwrotnego serwera proxy, użyj [oprogramowania pośredniczącego nagłówków przesyłanych dalej](xref:host-and-deploy/proxy-load-balancer) z pakietu [Microsoft.AspNetCore.HttpOverrides.](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/)</span><span class="sxs-lookup"><span data-stu-id="15cf7-150">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="15cf7-151">Oprogramowanie pośredniczące `Request.Scheme`aktualizuje `X-Forwarded-Proto` , używając nagłówka, dzięki czemu przekierowanie identyfikatorów URI i innych zasad zabezpieczeń działa poprawnie.</span><span class="sxs-lookup"><span data-stu-id="15cf7-151">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="15cf7-152">Każdy składnik, który zależy od schematu, takich jak uwierzytelnianie, generowanie łączy, przekierowania i geolokalizacja, musi być umieszczony po wywołaniu oprogramowania pośredniczącego nagłówków przesyłanych dalej.</span><span class="sxs-lookup"><span data-stu-id="15cf7-152">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span> <span data-ttu-id="15cf7-153">Zgodnie z ogólną zasadą oprogramowanie pośredniczące nagłówków przesyłanych dalej powinno być uruchamiane przed innym oprogramowaniem pośredniczącym, z wyjątkiem diagnostyki i obsługi błędów oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="15cf7-153">As a general rule, Forwarded Headers Middleware should run before other middleware except diagnostics and error handling middleware.</span></span> <span data-ttu-id="15cf7-154">Ta kolejność gwarantuje, że oprogramowanie pośredniczące korzystające z informacji o nagłówkach przesyłanych dalej może zużywać wartości nagłówka do przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="15cf7-154">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span>

<span data-ttu-id="15cf7-155">Wywołać <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodę w górnej `Startup.Configure` części przed wywołaniem innych oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="15cf7-155">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="15cf7-156">Skonfiguruj oprogramowanie `X-Forwarded-For` pośredniczące do przekazania nagłówków i `X-Forwarded-Proto` nagłówków:</span><span class="sxs-lookup"><span data-stu-id="15cf7-156">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="15cf7-157">Jeśli <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> oprogramowanie pośredniczące nie jest określone, domyślnymi nagłówkami do przekazania są `None`.</span><span class="sxs-lookup"><span data-stu-id="15cf7-157">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="15cf7-158">Serwery proxy uruchomione na adresach sprzężenia zwrotnego (127.0.0.0/8, [::1]), w tym standardowy adres localhost (127.0.0.1), są domyślnie zaufane.</span><span class="sxs-lookup"><span data-stu-id="15cf7-158">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="15cf7-159">Jeśli inne zaufane serwery proxy lub sieci w organizacji obsługują żądania między Internetem <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>serwerem sieci web, dodaj je do listy lub z programem .</span><span class="sxs-lookup"><span data-stu-id="15cf7-159">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="15cf7-160">Poniższy przykład dodaje zaufany serwer proxy pod adresem IP 10.0.0.100 `KnownProxies` `Startup.ConfigureServices`do oprogramowania pośredniczącego Nagłówków przesyłanych dalej w:</span><span class="sxs-lookup"><span data-stu-id="15cf7-160">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="15cf7-161">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="15cf7-161">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-nginx"></a><span data-ttu-id="15cf7-162">Instalowanie Nginx</span><span class="sxs-lookup"><span data-stu-id="15cf7-162">Install Nginx</span></span>

<span data-ttu-id="15cf7-163">Służy `apt-get` do instalowania Nginx.</span><span class="sxs-lookup"><span data-stu-id="15cf7-163">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="15cf7-164">Instalator tworzy skrypt *init systemd,* który uruchamia Nginx jako demon podczas uruchamiania systemu.</span><span class="sxs-lookup"><span data-stu-id="15cf7-164">The installer creates a *systemd* init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="15cf7-165">Postępuj zgodnie z instrukcjami instalacji Ubuntu w [Nginx: Official Debian / Pakiety Ubuntu](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span><span class="sxs-lookup"><span data-stu-id="15cf7-165">Follow the installation instructions for Ubuntu at [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span></span>

> [!NOTE]
> <span data-ttu-id="15cf7-166">Jeśli wymagane są opcjonalne moduły Nginx, może być wymagane tworzenie Nginx ze źródła.</span><span class="sxs-lookup"><span data-stu-id="15cf7-166">If optional Nginx modules are required, building Nginx from source might be required.</span></span>

<span data-ttu-id="15cf7-167">Ponieważ Nginx został zainstalowany po raz pierwszy, jawnie uruchom go, uruchamiając:</span><span class="sxs-lookup"><span data-stu-id="15cf7-167">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="15cf7-168">Sprawdź, czy przeglądarka wyświetla domyślną stronę docelową Nginx.</span><span class="sxs-lookup"><span data-stu-id="15cf7-168">Verify a browser displays the default landing page for Nginx.</span></span> <span data-ttu-id="15cf7-169">Strona docelowa jest `http://<server_IP_address>/index.nginx-debian.html`osiągalna pod adresem .</span><span class="sxs-lookup"><span data-stu-id="15cf7-169">The landing page is reachable at `http://<server_IP_address>/index.nginx-debian.html`.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="15cf7-170">Konfigurowanie serwera Nginx</span><span class="sxs-lookup"><span data-stu-id="15cf7-170">Configure Nginx</span></span>

<span data-ttu-id="15cf7-171">Aby skonfigurować Nginx jako odwrotny serwer proxy do przesyłania dalej żądań do aplikacji Core ASP.NET, zmodyfikuj */etc/nginx/sites-available/default*.</span><span class="sxs-lookup"><span data-stu-id="15cf7-171">To configure Nginx as a reverse proxy to forward requests to your ASP.NET Core app, modify */etc/nginx/sites-available/default*.</span></span> <span data-ttu-id="15cf7-172">Otwórz go w edytorze tekstu i zastąp zawartość następującymi:</span><span class="sxs-lookup"><span data-stu-id="15cf7-172">Open it in a text editor, and replace the contents with the following:</span></span>

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

<span data-ttu-id="15cf7-173">Jeśli aplikacja jest aplikacją Blazor Server, która opiera się <xref:host-and-deploy/blazor/server#linux-with-nginx> na SignalR WebSockets, zobacz informacje na temat ustawiania nagłówka. `Connection`</span><span class="sxs-lookup"><span data-stu-id="15cf7-173">If the app is a Blazor Server app that relies on SignalR WebSockets, see <xref:host-and-deploy/blazor/server#linux-with-nginx> for information on how to set the `Connection` header.</span></span>

<span data-ttu-id="15cf7-174">Gdy `server_name` nie pasuje, Nginx używa serwera domyślnego.</span><span class="sxs-lookup"><span data-stu-id="15cf7-174">When no `server_name` matches, Nginx uses the default server.</span></span> <span data-ttu-id="15cf7-175">Jeśli nie zdefiniowano serwera domyślnego, pierwszym serwerem w pliku konfiguracyjnym jest serwer domyślny.</span><span class="sxs-lookup"><span data-stu-id="15cf7-175">If no default server is defined, the first server in the configuration file is the default server.</span></span> <span data-ttu-id="15cf7-176">Najlepszym rozwiązaniem jest dodanie określonego serwera domyślnego, który zwraca kod stanu 444 w pliku konfiguracyjnym.</span><span class="sxs-lookup"><span data-stu-id="15cf7-176">As a best practice, add a specific default server which returns a status code of 444 in your configuration file.</span></span> <span data-ttu-id="15cf7-177">Domyślny przykład konfiguracji serwera to:</span><span class="sxs-lookup"><span data-stu-id="15cf7-177">A default server configuration example is:</span></span>

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

<span data-ttu-id="15cf7-178">W poprzednim pliku konfiguracyjnym i serwerze domyślnym Nginx akceptuje `example.com` `*.example.com`ruch publiczny na porcie 80 z nagłówkiem hosta lub .</span><span class="sxs-lookup"><span data-stu-id="15cf7-178">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="15cf7-179">Żądania, które nie pasują do tych hostów, nie zostaną przekazane do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="15cf7-179">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="15cf7-180">Nginx przekazuje pasujące żądania do `http://localhost:5000`Kestrel w .</span><span class="sxs-lookup"><span data-stu-id="15cf7-180">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="15cf7-181">Zobacz [Jak nginx przetwarza żądanie, aby](https://nginx.org/docs/http/request_processing.html) uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="15cf7-181">See [How nginx processes a request](https://nginx.org/docs/http/request_processing.html) for more information.</span></span> <span data-ttu-id="15cf7-182">Aby zmienić adres IP/port Kestrel, zobacz [Kestrel: Konfiguracja punktu końcowego](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="15cf7-182">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="15cf7-183">Niepodkreślenie odpowiedniej [dyrektywy server_name](https://nginx.org/docs/http/server_names.html) naraża aplikację na luki w zabezpieczeniach.</span><span class="sxs-lookup"><span data-stu-id="15cf7-183">Failure to specify a proper [server_name directive](https://nginx.org/docs/http/server_names.html) exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="15cf7-184">Powiązanie symboli wieloznacznych `*.example.com`poddomeny (na przykład) nie stanowi tego zagrożenia bezpieczeństwa, jeśli kontrolujesz całą domenę nadrzędną (w przeciwieństwie do `*.com`, która jest narażona).</span><span class="sxs-lookup"><span data-stu-id="15cf7-184">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="15cf7-185">Zobacz [rfc7230 section-5.4,](https://tools.ietf.org/html/rfc7230#section-5.4) aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="15cf7-185">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="15cf7-186">Po ustanowieniu konfiguracji Nginx `sudo nginx -t` uruchom, aby sprawdzić składnię plików konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="15cf7-186">Once the Nginx configuration is established, run `sudo nginx -t` to verify the syntax of the configuration files.</span></span> <span data-ttu-id="15cf7-187">Jeśli test pliku konfiguracyjnego zakończy się pomyślnie, zmusić Nginx do pobrania zmian przez uruchomienie `sudo nginx -s reload`.</span><span class="sxs-lookup"><span data-stu-id="15cf7-187">If the configuration file test is successful, force Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

<span data-ttu-id="15cf7-188">Aby bezpośrednio uruchomić aplikację na serwerze:</span><span class="sxs-lookup"><span data-stu-id="15cf7-188">To directly run the app on the server:</span></span>

1. <span data-ttu-id="15cf7-189">Przejdź do katalogu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="15cf7-189">Navigate to the app's directory.</span></span>
1. <span data-ttu-id="15cf7-190">Uruchom aplikację: `dotnet <app_assembly.dll>`, `app_assembly.dll` gdzie jest nazwa pliku zestawu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="15cf7-190">Run the app: `dotnet <app_assembly.dll>`, where `app_assembly.dll` is the assembly file name of the app.</span></span>

<span data-ttu-id="15cf7-191">Jeśli aplikacja działa na serwerze, ale nie odpowiada przez Internet, sprawdź zaporę serwera i upewnij się, że port 80 jest otwarty.</span><span class="sxs-lookup"><span data-stu-id="15cf7-191">If the app runs on the server but fails to respond over the Internet, check the server's firewall and confirm that port 80 is open.</span></span> <span data-ttu-id="15cf7-192">Jeśli używasz maszyny Wirtualnej Ubuntu platformy Azure, dodaj regułę sieciowej grupy zabezpieczeń (NSG), która umożliwia ruch przychodzący port 80.</span><span class="sxs-lookup"><span data-stu-id="15cf7-192">If using an Azure Ubuntu VM, add a Network Security Group (NSG) rule that enables inbound port 80 traffic.</span></span> <span data-ttu-id="15cf7-193">Nie ma potrzeby włączania reguły portu wychodzącego 80, ponieważ ruch wychodzący jest automatycznie przyznawany, gdy reguła ruchu przychodzącego jest włączona.</span><span class="sxs-lookup"><span data-stu-id="15cf7-193">There's no need to enable an outbound port 80 rule, as the outbound traffic is automatically granted when the inbound rule is enabled.</span></span>

<span data-ttu-id="15cf7-194">Po zakończeniu testowania aplikacji zamknij `Ctrl+C` aplikację w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="15cf7-194">When done testing the app, shut the app down with `Ctrl+C` at the command prompt.</span></span>

## <a name="monitor-the-app"></a><span data-ttu-id="15cf7-195">Monitorowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="15cf7-195">Monitor the app</span></span>

<span data-ttu-id="15cf7-196">Serwer jest skonfigurowany do `http://<serveraddress>:80` przekazywania żądań kierowanych do aplikacji ASP.NET `http://127.0.0.1:5000`Core uruchomionej na Kestrel w punkcie .</span><span class="sxs-lookup"><span data-stu-id="15cf7-196">The server is setup to forward requests made to `http://<serveraddress>:80` on to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="15cf7-197">Jednak Nginx nie jest skonfigurowany do zarządzania procesem Kestrel.</span><span class="sxs-lookup"><span data-stu-id="15cf7-197">However, Nginx isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="15cf7-198">*systemd* może służyć do tworzenia pliku usługi, aby uruchomić i monitorować podstawową aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="15cf7-198">*systemd* can be used to create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="15cf7-199">*systemd* to system init, który zapewnia wiele zaawansowanych funkcji uruchamiania, zatrzymywania i zarządzania procesami.</span><span class="sxs-lookup"><span data-stu-id="15cf7-199">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="15cf7-200">Tworzenie pliku usługi</span><span class="sxs-lookup"><span data-stu-id="15cf7-200">Create the service file</span></span>

<span data-ttu-id="15cf7-201">Utwórz plik definicji usługi:</span><span class="sxs-lookup"><span data-stu-id="15cf7-201">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="15cf7-202">Poniżej przedstawiono przykładowy plik usługi dla aplikacji:</span><span class="sxs-lookup"><span data-stu-id="15cf7-202">The following is an example service file for the app:</span></span>

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="15cf7-203">W poprzednim przykładzie użytkownik, który zarządza usługą `User` jest określony przez opcję.</span><span class="sxs-lookup"><span data-stu-id="15cf7-203">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="15cf7-204">Użytkownik (`www-data`) musi istnieć i mieć odpowiednie prawo własności do plików aplikacji.</span><span class="sxs-lookup"><span data-stu-id="15cf7-204">The user (`www-data`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="15cf7-205">Służy `TimeoutStopSec` do konfigurowania czasu oczekiwania na zamknięcie aplikacji po odebraniu początkowego sygnału przerwania.</span><span class="sxs-lookup"><span data-stu-id="15cf7-205">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="15cf7-206">Jeśli aplikacja nie zostanie zamknięta w tym okresie, SIGKILL zostanie wystawiony w celu zakończenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="15cf7-206">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="15cf7-207">Podaj wartość jako sekundy `150`bez jednostek (na przykład), wartość przedziału czasu (na `2min 30s`przykład) lub `infinity` wyłącz limit czasu.</span><span class="sxs-lookup"><span data-stu-id="15cf7-207">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="15cf7-208">`TimeoutStopSec`domyślnie wartość w `DefaultTimeoutStopSec` pliku konfiguracyjnym menedżera (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span><span class="sxs-lookup"><span data-stu-id="15cf7-208">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="15cf7-209">Domyślny limit czasu dla większości dystrybucji wynosi 90 sekund.</span><span class="sxs-lookup"><span data-stu-id="15cf7-209">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="15cf7-210">Linux ma system plików z uwzględnieniem wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="15cf7-210">Linux has a case-sensitive file system.</span></span> <span data-ttu-id="15cf7-211">Ustawienie ASPNETCORE_ENVIRONMENT na "Produkcja" powoduje wyszukanie pliku konfiguracji *appsettings. Production.json*, nie *appsettings.production.json*.</span><span class="sxs-lookup"><span data-stu-id="15cf7-211">Setting ASPNETCORE_ENVIRONMENT to "Production" results in searching for the configuration file *appsettings.Production.json*, not *appsettings.production.json*.</span></span>

<span data-ttu-id="15cf7-212">Niektóre wartości (na przykład parametry połączenia SQL) muszą zostać zmienione dla dostawców konfiguracji, aby odczytać zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="15cf7-212">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="15cf7-213">Użyj następującego polecenia, aby wygenerować poprawnie odsunącą wartość do użycia w pliku konfiguracyjnym:</span><span class="sxs-lookup"><span data-stu-id="15cf7-213">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

<span data-ttu-id="15cf7-214">Separatory`:`dwukropek ( ) nie są obsługiwane w nazwach zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="15cf7-214">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="15cf7-215">Użyj podwójnego podkreślenia (`__`) zamiast jelita grubego.</span><span class="sxs-lookup"><span data-stu-id="15cf7-215">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="15cf7-216">[Dostawca konfiguracji zmiennych środowiskowych](xref:fundamentals/configuration/index#environment-variables-configuration-provider) konwertuje podwójne podkreślenia na dwukropki, gdy zmienne środowiskowe są odczytywane do konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="15cf7-216">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="15cf7-217">W poniższym przykładzie klucz `ConnectionStrings:DefaultConnection` parametry połączenia jest ustawiony `ConnectionStrings__DefaultConnection`w pliku definicji usługi jako:</span><span class="sxs-lookup"><span data-stu-id="15cf7-217">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="15cf7-218">Zapisz plik i włącz usługę.</span><span class="sxs-lookup"><span data-stu-id="15cf7-218">Save the file and enable the service.</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="15cf7-219">Uruchom usługę i sprawdź, czy jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="15cf7-219">Start the service and verify that it's running.</span></span>

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="15cf7-220">Dzięki skonfigurowanego odwrotnego serwera proxy i kestrelowi zarządzanej przez systemd, aplikacja internetowa jest w `http://localhost`pełni skonfigurowana i można uzyskać do niej dostęp z przeglądarki na komputerze lokalnym pod adresem .</span><span class="sxs-lookup"><span data-stu-id="15cf7-220">With the reverse proxy configured and Kestrel managed through systemd, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="15cf7-221">Jest również dostępny z komputera zdalnego, blokując każdą zaporę, która może blokować.</span><span class="sxs-lookup"><span data-stu-id="15cf7-221">It's also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="15cf7-222">Sprawdzając nagłówki odpowiedzi, `Server` nagłówek pokazuje ASP.NET Core aplikacji obsługiwanych przez Kestrel.</span><span class="sxs-lookup"><span data-stu-id="15cf7-222">Inspecting the response headers, the `Server` header shows the ASP.NET Core app being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="15cf7-223">Wyświetlanie dzienników</span><span class="sxs-lookup"><span data-stu-id="15cf7-223">View logs</span></span>

<span data-ttu-id="15cf7-224">Ponieważ aplikacja internetowa przy użyciu Kestrel jest zarządzana za pomocą `systemd`, wszystkie zdarzenia i procesy są rejestrowane w scentralizowanym dzienniku.</span><span class="sxs-lookup"><span data-stu-id="15cf7-224">Since the web app using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="15cf7-225">Jednak ten arkusz zawiera wszystkie wpisy dla `systemd`wszystkich usług i procesów zarządzanych przez program .</span><span class="sxs-lookup"><span data-stu-id="15cf7-225">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="15cf7-226">Aby wyświetlić elementy określone w `kestrel-helloapp.service`poszczególnych, użyj następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="15cf7-226">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="15cf7-227">W przypadku dalszego filtrowania `--since today`opcje `--until 1 hour ago` czasu, takie jak , lub ich kombinacja, mogą zmniejszyć liczbę zwróconych wpisów.</span><span class="sxs-lookup"><span data-stu-id="15cf7-227">For further filtering, time options such as `--since today`, `--until 1 hour ago` or a combination of these can reduce the amount of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="15cf7-228">Ochrona danych</span><span class="sxs-lookup"><span data-stu-id="15cf7-228">Data protection</span></span>

<span data-ttu-id="15cf7-229">[Stos ASP.NET Core Data Protection](xref:security/data-protection/introduction) jest używany przez kilka ASP.NET core middleware , w tym oprogramowanie [pośredniczące](xref:fundamentals/middleware/index)uwierzytelniania (na przykład oprogramowanie pośredniczące plików cookie) i zabezpieczenia fałszowania żądań między witrynami (CSRF).</span><span class="sxs-lookup"><span data-stu-id="15cf7-229">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="15cf7-230">Nawet jeśli interfejsy API ochrony danych nie są wywoływane przez kod użytkownika, ochrona danych powinna być skonfigurowana do tworzenia trwałego [magazynu kluczy](xref:security/data-protection/implementation/key-management)kryptograficznych .</span><span class="sxs-lookup"><span data-stu-id="15cf7-230">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="15cf7-231">Jeśli ochrona danych nie jest skonfigurowana, klucze są przechowywane w pamięci i odrzucane po ponownym uruchomieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="15cf7-231">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="15cf7-232">Jeśli pierścień klucza jest przechowywany w pamięci po ponownym uruchomieniu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="15cf7-232">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="15cf7-233">Wszystkie tokeny uwierzytelniania oparte na plikach cookie są unieważnione.</span><span class="sxs-lookup"><span data-stu-id="15cf7-233">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="15cf7-234">Użytkownicy muszą zalogować się ponownie przy następnym żądaniu.</span><span class="sxs-lookup"><span data-stu-id="15cf7-234">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="15cf7-235">Nie można już odszyfrować żadnych danych chronionych za pomocą pierścienia kluczy.</span><span class="sxs-lookup"><span data-stu-id="15cf7-235">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="15cf7-236">Może to obejmować [tokeny CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) i [ASP.NET podstawowe pliki cookie MVC TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="15cf7-236">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="15cf7-237">Aby skonfigurować ochronę danych do utrwalania i szyfrowania pierścienia kluczy, zobacz:</span><span class="sxs-lookup"><span data-stu-id="15cf7-237">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a><span data-ttu-id="15cf7-238">Pola nagłówka żądania długiego</span><span class="sxs-lookup"><span data-stu-id="15cf7-238">Long request header fields</span></span>

<span data-ttu-id="15cf7-239">Domyślne ustawienia serwera proxy zazwyczaj ograniczają pola nagłówka żądania do 4 K lub 8 K w zależności od platformy.</span><span class="sxs-lookup"><span data-stu-id="15cf7-239">Proxy server default settings typically limit request header fields to 4 K or 8 K depending on the platform.</span></span> <span data-ttu-id="15cf7-240">Aplikacja może wymagać pól dłuższych niż domyślne (na przykład aplikacji korzystających z [usługi Azure Active Directory).](https://azure.microsoft.com/services/active-directory/)</span><span class="sxs-lookup"><span data-stu-id="15cf7-240">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="15cf7-241">Jeśli wymagane są dłuższe pola, domyślne ustawienia serwera proxy wymagają dostosowania.</span><span class="sxs-lookup"><span data-stu-id="15cf7-241">If longer fields are required, the proxy server's default settings require adjustment.</span></span> <span data-ttu-id="15cf7-242">Wartości, które należy zastosować, zależą od scenariusza.</span><span class="sxs-lookup"><span data-stu-id="15cf7-242">The values to apply depend on the scenario.</span></span> <span data-ttu-id="15cf7-243">Aby uzyskać więcej informacji, zapoznaj się z dokumentacją serwera.</span><span class="sxs-lookup"><span data-stu-id="15cf7-243">For more information, see your server's documentation.</span></span>

* [<span data-ttu-id="15cf7-244">proxy_buffer_size</span><span class="sxs-lookup"><span data-stu-id="15cf7-244">proxy_buffer_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [<span data-ttu-id="15cf7-245">proxy_buffers</span><span class="sxs-lookup"><span data-stu-id="15cf7-245">proxy_buffers</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [<span data-ttu-id="15cf7-246">proxy_busy_buffers_size</span><span class="sxs-lookup"><span data-stu-id="15cf7-246">proxy_busy_buffers_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [<span data-ttu-id="15cf7-247">large_client_header_buffers</span><span class="sxs-lookup"><span data-stu-id="15cf7-247">large_client_header_buffers</span></span>](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> <span data-ttu-id="15cf7-248">Nie należy zwiększać wartości domyślnych buforów serwera proxy, chyba że jest to konieczne.</span><span class="sxs-lookup"><span data-stu-id="15cf7-248">Don't increase the default values of proxy buffers unless necessary.</span></span> <span data-ttu-id="15cf7-249">Zwiększenie tych wartości zwiększa ryzyko przepełnienia buforu (przepełnienie) i ataków typu "odmowa usługi" (DoS) przez złośliwych użytkowników.</span><span class="sxs-lookup"><span data-stu-id="15cf7-249">Increasing these values increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="secure-the-app"></a><span data-ttu-id="15cf7-250">Zabezpiecz aplikację</span><span class="sxs-lookup"><span data-stu-id="15cf7-250">Secure the app</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="15cf7-251">Włącz AppArmor</span><span class="sxs-lookup"><span data-stu-id="15cf7-251">Enable AppArmor</span></span>

<span data-ttu-id="15cf7-252">Linux Security Modules (LSM) to framework, który jest częścią jądra Linuksa od Linuksa 2.6.</span><span class="sxs-lookup"><span data-stu-id="15cf7-252">Linux Security Modules (LSM) is a framework that's part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="15cf7-253">LSM obsługuje różne implementacje modułów zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="15cf7-253">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="15cf7-254">[AppArmor](https://wiki.ubuntu.com/AppArmor) to LSM, który implementuje system obowiązkowej kontroli dostępu, który pozwala na ograniczenie programu do ograniczonego zestawu zasobów.</span><span class="sxs-lookup"><span data-stu-id="15cf7-254">[AppArmor](https://wiki.ubuntu.com/AppArmor) is a LSM that implements a Mandatory Access Control system which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="15cf7-255">Upewnij się, że AppArmor jest włączony i poprawnie skonfigurowany.</span><span class="sxs-lookup"><span data-stu-id="15cf7-255">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configure-the-firewall"></a><span data-ttu-id="15cf7-256">Konfigurowanie zapory</span><span class="sxs-lookup"><span data-stu-id="15cf7-256">Configure the firewall</span></span>

<span data-ttu-id="15cf7-257">Zamknij wszystkie porty zewnętrzne, które nie są używane.</span><span class="sxs-lookup"><span data-stu-id="15cf7-257">Close off all external ports that are not in use.</span></span> <span data-ttu-id="15cf7-258">Nieskomplikowana zapora (ufw) `iptables` zapewnia front-end, zapewniając interfejsu wiersza polecenia do konfigurowania zapory.</span><span class="sxs-lookup"><span data-stu-id="15cf7-258">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a CLI for configuring the firewall.</span></span>

> [!WARNING]
> <span data-ttu-id="15cf7-259">Zapora uniemożliwi dostęp do całego systemu, jeśli nie zostanie poprawnie skonfigurowany.</span><span class="sxs-lookup"><span data-stu-id="15cf7-259">A firewall will prevent access to the whole system if not configured correctly.</span></span> <span data-ttu-id="15cf7-260">Niepodjęcie prawidłowego portu SSH skutecznie zablokuje Cię z systemu, jeśli używasz SSH do łączenia się z nim.</span><span class="sxs-lookup"><span data-stu-id="15cf7-260">Failure to specify the correct SSH port will effectively lock you out of the system if you are using SSH to connect to it.</span></span> <span data-ttu-id="15cf7-261">Domyślny port to 22.</span><span class="sxs-lookup"><span data-stu-id="15cf7-261">The default port is 22.</span></span> <span data-ttu-id="15cf7-262">Aby uzyskać więcej informacji, zobacz [wprowadzenie do ufw](https://help.ubuntu.com/community/UFW) i [instrukcja](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span><span class="sxs-lookup"><span data-stu-id="15cf7-262">For more information, see the [introduction to ufw](https://help.ubuntu.com/community/UFW) and the [manual](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span></span>

<span data-ttu-id="15cf7-263">Zainstaluj `ufw` i skonfiguruj go tak, aby zezwalał na ruch na wszystkich potrzebnych portach.</span><span class="sxs-lookup"><span data-stu-id="15cf7-263">Install `ufw` and configure it to allow traffic on any ports needed.</span></span>

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a><span data-ttu-id="15cf7-264">Bezpieczny Nginx</span><span class="sxs-lookup"><span data-stu-id="15cf7-264">Secure Nginx</span></span>

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="15cf7-265">Zmienianie nazwy odpowiedzi Nginx</span><span class="sxs-lookup"><span data-stu-id="15cf7-265">Change the Nginx response name</span></span>

<span data-ttu-id="15cf7-266">Edycja *src/http/ngx_http_header_filter_module.c*:</span><span class="sxs-lookup"><span data-stu-id="15cf7-266">Edit *src/http/ngx_http_header_filter_module.c*:</span></span>

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a><span data-ttu-id="15cf7-267">Konfigurowanie opcji</span><span class="sxs-lookup"><span data-stu-id="15cf7-267">Configure options</span></span>

<span data-ttu-id="15cf7-268">Skonfiguruj serwer za pomocą dodatkowych wymaganych modułów.</span><span class="sxs-lookup"><span data-stu-id="15cf7-268">Configure the server with additional required modules.</span></span> <span data-ttu-id="15cf7-269">Należy rozważyć użycie zapory aplikacji sieci web, takich jak [ModSecurity](https://www.modsecurity.org/), aby wzmocnić aplikację.</span><span class="sxs-lookup"><span data-stu-id="15cf7-269">Consider using a web app firewall, such as [ModSecurity](https://www.modsecurity.org/), to harden the app.</span></span>

#### <a name="https-configuration"></a><span data-ttu-id="15cf7-270">Konfiguracja PROTOKOŁU HTTPS</span><span class="sxs-lookup"><span data-stu-id="15cf7-270">HTTPS configuration</span></span>

<span data-ttu-id="15cf7-271">**Konfigurowanie aplikacji do bezpiecznych połączeń lokalnych (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="15cf7-271">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="15cf7-272">Polecenie [dotnet run](/dotnet/core/tools/dotnet-run) używa pliku *Properties/launchSettings.json* aplikacji, który konfiguruje aplikację do nasłuchiwania na adresach URL dostarczonych przez `applicationUrl` właściwość (na przykład `https://localhost:5001;http://localhost:5000`).</span><span class="sxs-lookup"><span data-stu-id="15cf7-272">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="15cf7-273">Skonfiguruj aplikację do używania `dotnet run` certyfikatu w środowisku deweloperskim (F5 lub Ctrl+F5 w programie Visual Studio Code) przy użyciu jednego z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="15cf7-273">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="15cf7-274">[Zastąp domyślny certyfikat z konfiguracji](xref:fundamentals/servers/kestrel#configuration) (*Zalecane*)</span><span class="sxs-lookup"><span data-stu-id="15cf7-274">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="15cf7-275">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="15cf7-275">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="15cf7-276">**Konfigurowanie odwrotnego serwera proxy dla bezpiecznych połączeń klientów (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="15cf7-276">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

* <span data-ttu-id="15cf7-277">Skonfiguruj serwer do nasłuchiwania ruchu HTTPS na porcie, `443` określając prawidłowy certyfikat wystawiony przez zaufany urząd certyfikacji (CA).</span><span class="sxs-lookup"><span data-stu-id="15cf7-277">Configure the server to listen to HTTPS traffic on port `443` by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="15cf7-278">Wzmocnij bezpieczeństwo, stosując niektóre praktyki przedstawione w następującym pliku */etc/nginx/nginx.conf.*</span><span class="sxs-lookup"><span data-stu-id="15cf7-278">Harden the security by employing some of the practices depicted in the following */etc/nginx/nginx.conf* file.</span></span> <span data-ttu-id="15cf7-279">Przykłady obejmują wybranie silniejszego szyfru i przekierowanie całego ruchu za pośrednictwem protokołu HTTP do https.</span><span class="sxs-lookup"><span data-stu-id="15cf7-279">Examples include choosing a stronger cipher and redirecting all traffic over HTTP to HTTPS.</span></span>

* <span data-ttu-id="15cf7-280">Dodanie `HTTP Strict-Transport-Security` nagłówka (HSTS) gwarantuje, że wszystkie kolejne żądania wykonane przez klienta są za pośrednictwem protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="15cf7-280">Adding an `HTTP Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS.</span></span>

* <span data-ttu-id="15cf7-281">Nie dodawaj nagłówka HSTS ani `max-age` nie wybieraj odpowiedniego, jeśli protokół HTTPS zostanie wyłączony w przyszłości.</span><span class="sxs-lookup"><span data-stu-id="15cf7-281">Don't add the HSTS header or chose an appropriate `max-age` if HTTPS will be disabled in the future.</span></span>

<span data-ttu-id="15cf7-282">Dodaj plik konfiguracyjny */etc/nginx/proxy.conf:*</span><span class="sxs-lookup"><span data-stu-id="15cf7-282">Add the */etc/nginx/proxy.conf* configuration file:</span></span>

[!code-nginx[](linux-nginx/proxy.conf)]

<span data-ttu-id="15cf7-283">Edytuj plik konfiguracyjny */etc/nginx/nginx.conf.*</span><span class="sxs-lookup"><span data-stu-id="15cf7-283">Edit the */etc/nginx/nginx.conf* configuration file.</span></span> <span data-ttu-id="15cf7-284">Przykład zawiera `http` zarówno `server` i sekcje w jednym pliku konfiguracyjnym.</span><span class="sxs-lookup"><span data-stu-id="15cf7-284">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="15cf7-285">Zabezpiecz Nginx przed kliknięciem</span><span class="sxs-lookup"><span data-stu-id="15cf7-285">Secure Nginx from clickjacking</span></span>

<span data-ttu-id="15cf7-286">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), znany również jako *atak zadośćuczynienia interfejsu*użytkownika , to złośliwy atak, w którym użytkownik witryny zostaje oszukany, klikając link lub przycisk na innej stronie niż obecnie odwiedza.</span><span class="sxs-lookup"><span data-stu-id="15cf7-286">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="15cf7-287">Służy `X-FRAME-OPTIONS` do zabezpieczania witryny.</span><span class="sxs-lookup"><span data-stu-id="15cf7-287">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="15cf7-288">Aby ograniczyć ataki typu clickjacking:</span><span class="sxs-lookup"><span data-stu-id="15cf7-288">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="15cf7-289">Edytuj plik *nginx.conf:*</span><span class="sxs-lookup"><span data-stu-id="15cf7-289">Edit the *nginx.conf* file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="15cf7-290">Dodaj wiersz `add_header X-Frame-Options "SAMEORIGIN";`.</span><span class="sxs-lookup"><span data-stu-id="15cf7-290">Add the line `add_header X-Frame-Options "SAMEORIGIN";`.</span></span>
1. <span data-ttu-id="15cf7-291">Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="15cf7-291">Save the file.</span></span>
1. <span data-ttu-id="15cf7-292">Uruchom ponownie nginx.</span><span class="sxs-lookup"><span data-stu-id="15cf7-292">Restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="15cf7-293">Wąchanie typu MIME</span><span class="sxs-lookup"><span data-stu-id="15cf7-293">MIME-type sniffing</span></span>

<span data-ttu-id="15cf7-294">Ten nagłówek uniemożliwia większości przeglądarek z MIME wąchania odpowiedzi z dala od zadeklarowanego typu zawartości, jak nagłówek instruuje przeglądarkę, aby nie zastępować typu zawartości odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="15cf7-294">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="15cf7-295">Z `nosniff` opcją, jeśli serwer mówi, że zawartość jest "text/html", przeglądarka renderuje go jako "text/html".</span><span class="sxs-lookup"><span data-stu-id="15cf7-295">With the `nosniff` option, if the server says the content is "text/html", the browser renders it as "text/html".</span></span>

<span data-ttu-id="15cf7-296">Edytuj plik *nginx.conf:*</span><span class="sxs-lookup"><span data-stu-id="15cf7-296">Edit the *nginx.conf* file:</span></span>

```bash
sudo nano /etc/nginx/nginx.conf
```

<span data-ttu-id="15cf7-297">Dodaj wiersz `add_header X-Content-Type-Options "nosniff";` i zapisz plik, a następnie uruchom ponownie Nginx.</span><span class="sxs-lookup"><span data-stu-id="15cf7-297">Add the line `add_header X-Content-Type-Options "nosniff";` and save the file, then restart Nginx.</span></span>

## <a name="additional-nginx-suggestions"></a><span data-ttu-id="15cf7-298">Dodatkowe sugestie Nginx</span><span class="sxs-lookup"><span data-stu-id="15cf7-298">Additional Nginx suggestions</span></span>

<span data-ttu-id="15cf7-299">Po uaktualnieniu udostępnionej struktury na serwerze uruchom ponownie aplikacje ASP.NET Core hostowane przez serwer.</span><span class="sxs-lookup"><span data-stu-id="15cf7-299">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="15cf7-300">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="15cf7-300">Additional resources</span></span>

* [<span data-ttu-id="15cf7-301">Wymagania wstępne dla .NET Core w systemie Linux</span><span class="sxs-lookup"><span data-stu-id="15cf7-301">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* [<span data-ttu-id="15cf7-302">Nginx: Publikacje binarne: Oficjalne pakiety Debian/Ubuntu</span><span class="sxs-lookup"><span data-stu-id="15cf7-302">Nginx: Binary Releases: Official Debian/Ubuntu packages</span></span>](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="15cf7-303">NGINX: Korzystanie z nagłówka przesyłania dalej</span><span class="sxs-lookup"><span data-stu-id="15cf7-303">NGINX: Using the Forwarded header</span></span>](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
