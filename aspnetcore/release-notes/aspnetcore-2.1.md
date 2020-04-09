---
title: Co nowego w ASP.NET Core 2.1
author: isaac2004
description: Dowiedz się więcej o nowych funkcjach w ASP.NET Core 2.1.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- SignalR
uid: aspnetcore-2.1
ms.openlocfilehash: af5807b782d4acec8c7d40111dc508dfa6127057
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667546"
---
# <a name="whats-new-in-aspnet-core-21"></a><span data-ttu-id="565e7-103">Co nowego w ASP.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="565e7-103">What's new in ASP.NET Core 2.1</span></span>

<span data-ttu-id="565e7-104">W tym artykule przedstawiono najważniejsze zmiany w ASP.NET Core 2.1, wraz z łączami do odpowiedniej dokumentacji.</span><span class="sxs-lookup"><span data-stu-id="565e7-104">This article highlights the most significant changes in ASP.NET Core 2.1, with links to relevant documentation.</span></span>

## SignalR

SignalR<span data-ttu-id="565e7-105">został przepisany dla ASP.NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="565e7-105"> has been rewritten for ASP.NET Core 2.1.</span></span> <span data-ttu-id="565e7-106">ASP.NET Core SignalR zawiera szereg ulepszeń:</span><span class="sxs-lookup"><span data-stu-id="565e7-106">ASP.NET Core SignalR includes a number of improvements:</span></span>

* <span data-ttu-id="565e7-107">Uproszczony model skalowania w poziomie.</span><span class="sxs-lookup"><span data-stu-id="565e7-107">A simplified scale-out model.</span></span>
* <span data-ttu-id="565e7-108">Nowy klient JavaScript bez zależności jQuery.</span><span class="sxs-lookup"><span data-stu-id="565e7-108">A new JavaScript client with no jQuery dependency.</span></span>
* <span data-ttu-id="565e7-109">Nowy kompaktowy protokół binarny oparty na MessagePack.</span><span class="sxs-lookup"><span data-stu-id="565e7-109">A new compact binary protocol based on MessagePack.</span></span>
* <span data-ttu-id="565e7-110">Obsługa protokołów niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="565e7-110">Support for custom protocols.</span></span>
* <span data-ttu-id="565e7-111">Nowy model odpowiedzi przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="565e7-111">A new streaming response model.</span></span>
* <span data-ttu-id="565e7-112">Obsługa klientów na podstawie gołych WebSockets.</span><span class="sxs-lookup"><span data-stu-id="565e7-112">Support for clients based on bare WebSockets.</span></span>

<span data-ttu-id="565e7-113">Aby uzyskać więcej informacji, zobacz [ASP.NET Core SignalR ](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="565e7-113">For more information, see [ASP.NET Core SignalR](xref:signalr/introduction).</span></span>

## <a name="razor-class-libraries"></a><span data-ttu-id="565e7-114">Biblioteki klas Razor</span><span class="sxs-lookup"><span data-stu-id="565e7-114">Razor class libraries</span></span>

<span data-ttu-id="565e7-115">ASP.NET Core 2.1 ułatwia tworzenie i dołączanie interfejsu użytkownika opartego na maszynie Razor w bibliotece i udostępnianie go w wielu projektach.</span><span class="sxs-lookup"><span data-stu-id="565e7-115">ASP.NET Core 2.1 makes it easier to build and include Razor-based UI in a library and share it across multiple projects.</span></span> <span data-ttu-id="565e7-116">Nowy zestawu Razor SDK umożliwia tworzenie plików Razor do projektu biblioteki klas, które mogą być pakowane w pakiecie NuGet.</span><span class="sxs-lookup"><span data-stu-id="565e7-116">The new Razor SDK enables building Razor files into a class library project that can be packaged into a NuGet package.</span></span> <span data-ttu-id="565e7-117">Widoki i strony w bibliotekach są automatycznie odnajdywane i mogą być zastępowane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="565e7-117">Views and pages in libraries are automatically discovered and can be overridden by the app.</span></span> <span data-ttu-id="565e7-118">Poprzez zintegrowanie kompilacji Razor do kompilacji:</span><span class="sxs-lookup"><span data-stu-id="565e7-118">By integrating Razor compilation into the build:</span></span>

* <span data-ttu-id="565e7-119">Czas uruchamiania aplikacji jest znacznie szybszy.</span><span class="sxs-lookup"><span data-stu-id="565e7-119">The app startup time is significantly faster.</span></span>
* <span data-ttu-id="565e7-120">Szybkie aktualizacje widoków razor i stron w czasie wykonywania są nadal dostępne w ramach iteracyjnego przepływu pracy rozwoju.</span><span class="sxs-lookup"><span data-stu-id="565e7-120">Fast updates to Razor views and pages at runtime are still available as part of an iterative development workflow.</span></span>

<span data-ttu-id="565e7-121">Aby uzyskać więcej informacji, zobacz [Tworzenie interfejsu użytkownika wielokrotnego użytku przy użyciu projektu Biblioteka klas Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="565e7-121">For more information, see [Create reusable UI using the Razor Class Library project](xref:razor-pages/ui-class).</span></span>

## <a name="identity-ui-library--scaffolding"></a><span data-ttu-id="565e7-122">Biblioteka interfejsu użytkownika tożsamości & rusztowania</span><span class="sxs-lookup"><span data-stu-id="565e7-122">Identity UI library & scaffolding</span></span>

<span data-ttu-id="565e7-123">ASP.NET Core 2.1 zapewnia [ASP.NET podstawową tożsamość](xref:security/authentication/identity) jako [bibliotekę klas Razor.](xref:razor-pages/ui-class)</span><span class="sxs-lookup"><span data-stu-id="565e7-123">ASP.NET Core 2.1 provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="565e7-124">Aplikacje, które zawierają tożsamość można zastosować nowy szkielet usztrzacz tożsamości selektywnie dodać kod źródłowy zawarty w bibliotece klas razor tożsamości (RCL).</span><span class="sxs-lookup"><span data-stu-id="565e7-124">Apps that include Identity can apply the new Identity scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="565e7-125">Można wygenerować kod źródłowy, dzięki czemu można zmodyfikować kod i zmienić zachowanie.</span><span class="sxs-lookup"><span data-stu-id="565e7-125">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="565e7-126">Na przykład można poinstruować szkieletu do generowania kodu używanego w rejestracji.</span><span class="sxs-lookup"><span data-stu-id="565e7-126">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="565e7-127">Wygenerowany kod ma pierwszeństwo przed tym samym kodem w podstawie listy RCL tożsamości.</span><span class="sxs-lookup"><span data-stu-id="565e7-127">Generated code takes precedence over the same code in the Identity RCL.</span></span>

<span data-ttu-id="565e7-128">Aplikacje, które **nie** zawierają uwierzytelniania można zastosować szkieletu tożsamości, aby dodać pakiet tożsamości RCL.</span><span class="sxs-lookup"><span data-stu-id="565e7-128">Apps that do **not** include authentication can apply the Identity scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="565e7-129">Masz możliwość wybrania kodu tożsamości, który ma zostać wygenerowany.</span><span class="sxs-lookup"><span data-stu-id="565e7-129">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="565e7-130">Aby uzyskać więcej informacji, zobacz [Tożsamość szkieletu w ASP.NET podstawowych projektach](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="565e7-130">For more information, see [Scaffold Identity in ASP.NET Core projects](xref:security/authentication/scaffold-identity).</span></span>

## <a name="https"></a><span data-ttu-id="565e7-131">HTTPS</span><span class="sxs-lookup"><span data-stu-id="565e7-131">HTTPS</span></span>

<span data-ttu-id="565e7-132">Przy zwiększonym skupieniu się na bezpieczeństwie i prywatności ważne jest włączenie protokołu HTTPS dla aplikacji internetowych.</span><span class="sxs-lookup"><span data-stu-id="565e7-132">With the increased focus on security and privacy, enabling HTTPS for web apps is important.</span></span> <span data-ttu-id="565e7-133">Egzekwowanie protokołu HTTPS staje się coraz bardziej rygorystyczne w internecie.</span><span class="sxs-lookup"><span data-stu-id="565e7-133">HTTPS enforcement is becoming increasingly strict on the web.</span></span> <span data-ttu-id="565e7-134">Witryny, które nie korzystają z protokołu HTTPS, są uważane za niezabezpieczone.</span><span class="sxs-lookup"><span data-stu-id="565e7-134">Sites that don't use HTTPS are considered insecure.</span></span> <span data-ttu-id="565e7-135">Przeglądarki (Chromium, Mozilla) zaczynają wymuszać, że funkcje internetowe muszą być używane z bezpiecznego kontekstu.</span><span class="sxs-lookup"><span data-stu-id="565e7-135">Browsers (Chromium, Mozilla) are starting to enforce that web features must be used from a secure context.</span></span> <span data-ttu-id="565e7-136">[RODO](xref:security/gdpr) wymaga użycia protokołu HTTPS w celu ochrony prywatności użytkowników.</span><span class="sxs-lookup"><span data-stu-id="565e7-136">[GDPR](xref:security/gdpr) requires the use of HTTPS to protect user privacy.</span></span> <span data-ttu-id="565e7-137">Podczas korzystania z protokołu HTTPS w środowisku produkcyjnym jest krytyczna, przy użyciu protokołu HTTPS w rozwoju może pomóc w zapobieganiu problemom we wdrożeniu (na przykład niezabezpieczone łącza).</span><span class="sxs-lookup"><span data-stu-id="565e7-137">While using HTTPS in production is critical, using HTTPS in development can help prevent issues in deployment (for example, insecure links).</span></span> <span data-ttu-id="565e7-138">ASP.NET Core 2.1 zawiera szereg ulepszeń, które ułatwiają korzystanie z protokołu HTTPS w programowaniu i konfigurowanie protokołu HTTPS w produkcji.</span><span class="sxs-lookup"><span data-stu-id="565e7-138">ASP.NET Core 2.1 includes a number of improvements that make it easier to use HTTPS in development and to configure HTTPS in production.</span></span> <span data-ttu-id="565e7-139">Aby uzyskać więcej informacji, zobacz [Wymuszanie protokołu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="565e7-139">For more information, see [Enforce HTTPS](xref:security/enforcing-ssl).</span></span>

### <a name="on-by-default"></a><span data-ttu-id="565e7-140">Domyślnie włączony</span><span class="sxs-lookup"><span data-stu-id="565e7-140">On by default</span></span>

<span data-ttu-id="565e7-141">Aby ułatwić bezpieczne tworzenie witryn sieci Web, protokół HTTPS jest teraz domyślnie włączony.</span><span class="sxs-lookup"><span data-stu-id="565e7-141">To facilitate secure website development, HTTPS  is now enabled by default.</span></span> <span data-ttu-id="565e7-142">Począwszy od 2.1, Kestrel nasłuchuje, `https://localhost:5001` kiedy certyfikat rozwoju lokalnego jest obecny.</span><span class="sxs-lookup"><span data-stu-id="565e7-142">Starting in 2.1, Kestrel listens on `https://localhost:5001` when a local development certificate is present.</span></span> <span data-ttu-id="565e7-143">Tworzony jest certyfikat dewelopera:</span><span class="sxs-lookup"><span data-stu-id="565e7-143">A development certificate is created:</span></span>

* <span data-ttu-id="565e7-144">W ramach pierwszego uruchomienia sdk .NET Core, gdy używasz SDK po raz pierwszy.</span><span class="sxs-lookup"><span data-stu-id="565e7-144">As part of the .NET Core SDK first-run experience, when you use the SDK for the first time.</span></span>
* <span data-ttu-id="565e7-145">Ręcznie za pomocą `dev-certs` nowego narzędzia.</span><span class="sxs-lookup"><span data-stu-id="565e7-145">Manually using the new `dev-certs` tool.</span></span>

<span data-ttu-id="565e7-146">Uruchom, `dotnet dev-certs https --trust` aby zaufać certyfikatowi.</span><span class="sxs-lookup"><span data-stu-id="565e7-146">Run `dotnet dev-certs https --trust` to trust the certificate.</span></span>

### <a name="https-redirection-and-enforcement"></a><span data-ttu-id="565e7-147">Przekierowanie i egzekwowanie protokołu HTTPS</span><span class="sxs-lookup"><span data-stu-id="565e7-147">HTTPS redirection and enforcement</span></span>

<span data-ttu-id="565e7-148">Aplikacje sieci Web zazwyczaj muszą słuchać zarówno na HTTP, jak i HTTPS, ale następnie przekierowywać cały ruch HTTP do protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="565e7-148">Web apps typically need to listen on both HTTP and HTTPS, but then redirect all HTTP traffic to HTTPS.</span></span> <span data-ttu-id="565e7-149">W wersji 2.1 wprowadzono specjalistyczne oprogramowanie pośredniczące przekierowania HTTPS, które inteligentnie przekierowuje na podstawie obecności konfiguracji lub powiązanych portów serwera.</span><span class="sxs-lookup"><span data-stu-id="565e7-149">In 2.1, specialized HTTPS redirection middleware that intelligently redirects based on the presence of configuration or bound server ports has been introduced.</span></span>

<span data-ttu-id="565e7-150">Korzystanie z protokołu HTTPS można dodatkowo wymusić za pomocą [protokołu HTTP Strict Transport Security Protocol (HSTS).](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)</span><span class="sxs-lookup"><span data-stu-id="565e7-150">Use of HTTPS can be further enforced using [HTTP Strict Transport Security Protocol (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="565e7-151">HSTS instruuje przeglądarki, aby zawsze uzyskiwały dostęp do witryny za pośrednictwem protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="565e7-151">HSTS instructs browsers to always access the site via HTTPS.</span></span> <span data-ttu-id="565e7-152">ASP.NET Core 2.1 dodaje oprogramowanie pośredniczące HSTS, które obsługuje opcje dla maksymalnego wieku, subdomen i listy preload HSTS.</span><span class="sxs-lookup"><span data-stu-id="565e7-152">ASP.NET Core 2.1 adds HSTS middleware that supports options for max age, subdomains, and the HSTS preload list.</span></span>

### <a name="configuration-for-production"></a><span data-ttu-id="565e7-153">Konfiguracja do produkcji</span><span class="sxs-lookup"><span data-stu-id="565e7-153">Configuration for production</span></span>

<span data-ttu-id="565e7-154">W produkcji protokół HTTPS musi być jawnie skonfigurowany.</span><span class="sxs-lookup"><span data-stu-id="565e7-154">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="565e7-155">W 2.1 dodano domyślny schemat konfiguracji konfiguracji protokołu HTTPS dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="565e7-155">In 2.1, default configuration schema for configuring HTTPS for Kestrel has been added.</span></span> <span data-ttu-id="565e7-156">Aplikacje można skonfigurować do używania:</span><span class="sxs-lookup"><span data-stu-id="565e7-156">Apps can be configured to use:</span></span>

* <span data-ttu-id="565e7-157">Wiele punktów końcowych, w tym adresy URL.</span><span class="sxs-lookup"><span data-stu-id="565e7-157">Multiple endpoints including the URLs.</span></span> <span data-ttu-id="565e7-158">Aby uzyskać więcej informacji, zobacz [Implementacja serwera sieci Web Kestrel: Konfiguracja punktu końcowego](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="565e7-158">For more information, see [Kestrel web server implementation: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>
* <span data-ttu-id="565e7-159">Certyfikat do użycia w pliku HTTPS z pliku na dysku lub z magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="565e7-159">The certificate to use for HTTPS either from a file on disk or from a certificate store.</span></span>

## <a name="gdpr"></a><span data-ttu-id="565e7-160">RODO</span><span class="sxs-lookup"><span data-stu-id="565e7-160">GDPR</span></span>

<span data-ttu-id="565e7-161">ASP.NET Core udostępnia interfejsy API i szablony, które pomagają spełnić niektóre wymogi ogólnego rozporządzenia o [ochronie danych (RODO) UE.](https://www.eugdpr.org/)</span><span class="sxs-lookup"><span data-stu-id="565e7-161">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://www.eugdpr.org/) requirements.</span></span> <span data-ttu-id="565e7-162">Aby uzyskać więcej informacji, zobacz [pomoc techniczna GDPR w ASP.NET Core](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="565e7-162">For more information, see [GDPR support in ASP.NET Core](xref:security/gdpr).</span></span> <span data-ttu-id="565e7-163">[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) pokazuje, jak używać i umożliwia testowanie większości punktów rozszerzenia RODO i interfejsów API dodanych do szablonów ASP.NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="565e7-163">A [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) shows how to use and lets you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span>

## <a name="integration-tests"></a><span data-ttu-id="565e7-164">Testy integracji</span><span class="sxs-lookup"><span data-stu-id="565e7-164">Integration tests</span></span>

<span data-ttu-id="565e7-165">Wprowadzono nowy pakiet, który usprawnia tworzenie i wykonywanie testów.</span><span class="sxs-lookup"><span data-stu-id="565e7-165">A new package is introduced that streamlines test creation and execution.</span></span> <span data-ttu-id="565e7-166">Pakiet [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) obsługuje następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="565e7-166">The [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) package handles the following tasks:</span></span>

* <span data-ttu-id="565e7-167">Kopiuje plik zależności (*\*deps*) z testowanej aplikacji do folderu *bin* projektu testowego.</span><span class="sxs-lookup"><span data-stu-id="565e7-167">Copies the dependency file (*\*.deps*) from the tested app into the test project's *bin* folder.</span></span>
* <span data-ttu-id="565e7-168">Ustawia katalog główny zawartości w katalogu głównym projektu testowanego aplikacji, tak aby pliki statyczne i strony/widoki były znajdowane podczas wykonywania testów.</span><span class="sxs-lookup"><span data-stu-id="565e7-168">Sets the content root to the tested app's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="565e7-169">Udostępnia [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) klasy usprawnić boottrapping testowane aplikacji z [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span><span class="sxs-lookup"><span data-stu-id="565e7-169">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the tested app with [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span>

<span data-ttu-id="565e7-170">Następujący test używa [xUnit,](https://xunit.github.io/) aby sprawdzić, czy strona Indeks jest wczytyty z kodem stanu sukcesu i z poprawnym nagłówkiem typu zawartości:</span><span class="sxs-lookup"><span data-stu-id="565e7-170">The following test uses [xUnit](https://xunit.github.io/) to check that the Index page loads with a success status code and with the correct Content-Type header:</span></span>

```csharp
public class BasicTests
    : IClassFixture<WebApplicationFactory<RazorPagesProject.Startup>>
{
    private readonly HttpClient _client;

    public BasicTests(WebApplicationFactory<RazorPagesProject.Startup> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetHomePage()
    {
        // Act
        var response = await _client.GetAsync("/");

        // Assert
        response.EnsureSuccessStatusCode(); // Status Code 200-299
        Assert.Equal("text/html; charset=utf-8",
            response.Content.Headers.ContentType.ToString());
    }
}
```

<span data-ttu-id="565e7-171">Aby uzyskać więcej informacji, zobacz temat [testy integracji.](xref:test/integration-tests)</span><span class="sxs-lookup"><span data-stu-id="565e7-171">For more information, see the [Integration tests](xref:test/integration-tests) topic.</span></span>

## <a name="apicontroller-actionresultt"></a><span data-ttu-id="565e7-172">[ApiController], ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="565e7-172">[ApiController], ActionResult\<T></span></span>

<span data-ttu-id="565e7-173">ASP.NET Core 2.1 dodaje nowe konwencje programowania, które ułatwiają tworzenie czystych i opisowych interfejsów API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="565e7-173">ASP.NET Core 2.1 adds new programming conventions that make it easier to build clean and descriptive web APIs.</span></span> <span data-ttu-id="565e7-174">`ActionResult<T>`jest nowy typ dodany, aby umożliwić aplikacji do zwrócenia typu odpowiedzi lub inny wynik akcji (podobne do IActionResult), przy jednoczesnym pokazaniu typu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="565e7-174">`ActionResult<T>` is a new type added to allow an app to return either a response type or any other action result (similar to IActionResult), while still indicating the response type.</span></span> <span data-ttu-id="565e7-175">Atrybut `[ApiController]` został również dodany jako sposób, aby zdecydować się na konwencje i zachowania specyficzne dla interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="565e7-175">The `[ApiController]` attribute has also been added as the way to opt in to Web API-specific conventions and behaviors.</span></span>

<span data-ttu-id="565e7-176">Aby uzyskać więcej informacji, zobacz [Tworzenie interfejsów API sieci Web z ASP.NET Core](xref:web-api/index).</span><span class="sxs-lookup"><span data-stu-id="565e7-176">For more information, see [Build Web APIs with ASP.NET Core](xref:web-api/index).</span></span>

## <a name="ihttpclientfactory"></a><span data-ttu-id="565e7-177">IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="565e7-177">IHttpClientFactory</span></span>

<span data-ttu-id="565e7-178">ASP.NET Core 2.1 zawiera nową `IHttpClientFactory` usługę, która ułatwia konfigurowanie i korzystanie `HttpClient` z wystąpień w aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="565e7-178">ASP.NET Core 2.1 includes a new `IHttpClientFactory` service that makes it easier to configure and consume instances of `HttpClient` in apps.</span></span> <span data-ttu-id="565e7-179">`HttpClient`już ma koncepcję delegowanie programów obsługi, które mogą być połączone ze sobą dla wychodzących żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="565e7-179">`HttpClient` already has the concept of delegating handlers that could be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="565e7-180">Fabryka:</span><span class="sxs-lookup"><span data-stu-id="565e7-180">The factory:</span></span>

* <span data-ttu-id="565e7-181">Sprawia, że rejestrowanie `HttpClient` wystąpień na nazwanego klienta jest bardziej intuicyjne.</span><span class="sxs-lookup"><span data-stu-id="565e7-181">Makes registering of instances of `HttpClient` per named client more intuitive.</span></span>
* <span data-ttu-id="565e7-182">Implementuje program obsługi Polly, który umożliwia zasady Polly, które mają być używane do ponowiania prób, CircuitBreakers, itp.</span><span class="sxs-lookup"><span data-stu-id="565e7-182">Implements a Polly handler that allows Polly policies to be used for Retry, CircuitBreakers, etc.</span></span>

<span data-ttu-id="565e7-183">Aby uzyskać więcej informacji, zobacz [Inicjowanie żądań HTTP](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="565e7-183">For more information, see [Initiate HTTP Requests](xref:fundamentals/http-requests).</span></span>

## <a name="kestrel-transport-configuration"></a><span data-ttu-id="565e7-184">Konfiguracja transportu pustułki</span><span class="sxs-lookup"><span data-stu-id="565e7-184">Kestrel transport configuration</span></span>

<span data-ttu-id="565e7-185">Wraz z wydaniem ASP.NET Core 2.1 domyślny transport Kestrel nie jest już oparty na Libuv, ale zamiast tego opiera się na zarządzanych gniazdach.</span><span class="sxs-lookup"><span data-stu-id="565e7-185">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="565e7-186">Aby uzyskać więcej informacji, zobacz [Kestrel web server implementation: Transport configuration](xref:fundamentals/servers/kestrel#transport-configuration).</span><span class="sxs-lookup"><span data-stu-id="565e7-186">For more information, see [Kestrel web server implementation: Transport configuration](xref:fundamentals/servers/kestrel#transport-configuration).</span></span>

## <a name="generic-host-builder"></a><span data-ttu-id="565e7-187">Ogólny konstruktor hostów</span><span class="sxs-lookup"><span data-stu-id="565e7-187">Generic host builder</span></span>

<span data-ttu-id="565e7-188">Wprowadzono generic host`HostBuilder`builder ( ).</span><span class="sxs-lookup"><span data-stu-id="565e7-188">The Generic Host Builder (`HostBuilder`) has been introduced.</span></span> <span data-ttu-id="565e7-189">Tego konstruktora można używać w przypadku aplikacji, które nie przetwarzają żądań HTTP (wiadomości, zadania w tle itp.).</span><span class="sxs-lookup"><span data-stu-id="565e7-189">This builder can be used for apps that don't process HTTP requests (Messaging, background tasks, etc.).</span></span>

<span data-ttu-id="565e7-190">Aby uzyskać więcej informacji, zobacz [Host ogólny platformy .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="565e7-190">For more information, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

## <a name="updated-spa-templates"></a><span data-ttu-id="565e7-191">Zaktualizowane szablony SPA</span><span class="sxs-lookup"><span data-stu-id="565e7-191">Updated SPA templates</span></span>

<span data-ttu-id="565e7-192">Szablony aplikacji jednostronicowych dla angular, React i React with Redux są aktualizowane w celu użycia standardowych struktur projektu i systemów kompilacji dla każdej struktury.</span><span class="sxs-lookup"><span data-stu-id="565e7-192">The Single Page Application templates for Angular, React, and React with Redux are updated to use the standard project structures and build systems for each framework.</span></span>

<span data-ttu-id="565e7-193">Szablon kątowy jest oparty na angular CLI, a szablony React są oparte na create-react-app.</span><span class="sxs-lookup"><span data-stu-id="565e7-193">The Angular template is based on the Angular CLI, and the React templates are based on create-react-app.</span></span>

<span data-ttu-id="565e7-194">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="565e7-194">For more information, see:</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="razor-pages-search-for-razor-assets"></a><span data-ttu-id="565e7-195">Strony razor wyszukiwania zasobów Razor</span><span class="sxs-lookup"><span data-stu-id="565e7-195">Razor Pages search for Razor assets</span></span>

<span data-ttu-id="565e7-196">W 2.1 Strony Razor wyszukują zasoby Razor (takie jak układy i części) w następujących katalogach w podanej kolejności:</span><span class="sxs-lookup"><span data-stu-id="565e7-196">In 2.1, Razor Pages search for Razor assets (such as layouts and partials) in the following directories in the listed order:</span></span>

1. <span data-ttu-id="565e7-197">Folder Bieżące strony.</span><span class="sxs-lookup"><span data-stu-id="565e7-197">Current Pages folder.</span></span>
1. <span data-ttu-id="565e7-198">*/Strony/Udostępnione/*</span><span class="sxs-lookup"><span data-stu-id="565e7-198">*/Pages/Shared/*</span></span>
1. <span data-ttu-id="565e7-199">*/Widoki/Udostępnione/*</span><span class="sxs-lookup"><span data-stu-id="565e7-199">*/Views/Shared/*</span></span>

## <a name="razor-pages-in-an-area"></a><span data-ttu-id="565e7-200">Strony brzytwy w obszarze</span><span class="sxs-lookup"><span data-stu-id="565e7-200">Razor Pages in an area</span></span>

<span data-ttu-id="565e7-201">Razor Pages obsługuje teraz [obszary](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="565e7-201">Razor Pages now support [areas](xref:mvc/controllers/areas).</span></span> <span data-ttu-id="565e7-202">Aby wyświetlić przykład obszarów, utwórz nową aplikację internetową Razor Pages z indywidualnymi kontami użytkowników.</span><span class="sxs-lookup"><span data-stu-id="565e7-202">To see an example of areas, create a new Razor Pages web app with individual user accounts.</span></span> <span data-ttu-id="565e7-203">Aplikacja internetowa Razor Pages z indywidualnymi kontami użytkowników zawiera */Obszary/Tożsamość/Strony*.</span><span class="sxs-lookup"><span data-stu-id="565e7-203">A Razor Pages web app with individual user accounts includes */Areas/Identity/Pages*.</span></span>

## <a name="mvc-compatibility-version"></a><span data-ttu-id="565e7-204">Wersja kompatybilności MVC</span><span class="sxs-lookup"><span data-stu-id="565e7-204">MVC compatibility version</span></span>

<span data-ttu-id="565e7-205">Metoda <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> umożliwia aplikacji do wyrażenia zgody lub rezygnacji z potencjalnie przełomowych zmian zachowania wprowadzonych w ASP.NET Core MVC 2.1 lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="565e7-205">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="565e7-206">Aby uzyskać więcej informacji, zobacz <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="565e7-206">For more information, see <xref:mvc/compatibility-version>.</span></span>

## <a name="migrate-from-20-to-21"></a><span data-ttu-id="565e7-207">Migracja z 2,0 do 2,1</span><span class="sxs-lookup"><span data-stu-id="565e7-207">Migrate from 2.0 to 2.1</span></span>

<span data-ttu-id="565e7-208">Zobacz [Migrowanie z ASP.NET Core 2.0 do 2.1](xref:migration/20_21).</span><span class="sxs-lookup"><span data-stu-id="565e7-208">See [Migrate from ASP.NET Core 2.0 to 2.1](xref:migration/20_21).</span></span>

## <a name="additional-information"></a><span data-ttu-id="565e7-209">Dodatkowe informacje</span><span class="sxs-lookup"><span data-stu-id="565e7-209">Additional information</span></span>

<span data-ttu-id="565e7-210">Aby uzyskać pełną listę zmian, zobacz [ASP.NET Informacje o wersji Core 2.1](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0).</span><span class="sxs-lookup"><span data-stu-id="565e7-210">For the complete list of changes, see the [ASP.NET Core 2.1 Release Notes](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0).</span></span>
