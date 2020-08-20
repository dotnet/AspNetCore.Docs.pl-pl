---
title: Co nowego w ASP.NET Core 2,1
author: isaac2004
description: Dowiedz się więcej o nowych funkcjach w ASP.NET Core 2,1.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
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
uid: aspnetcore-2.1
ms.openlocfilehash: d10fd89a3eac34e855ba92673033edc541aa6393
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632411"
---
# <a name="whats-new-in-aspnet-core-21"></a><span data-ttu-id="8913b-103">Co nowego w ASP.NET Core 2,1</span><span class="sxs-lookup"><span data-stu-id="8913b-103">What's new in ASP.NET Core 2.1</span></span>

<span data-ttu-id="8913b-104">W tym artykule przedstawiono najbardziej znaczące zmiany w ASP.NET Core 2,1 z linkami do odpowiedniej dokumentacji.</span><span class="sxs-lookup"><span data-stu-id="8913b-104">This article highlights the most significant changes in ASP.NET Core 2.1, with links to relevant documentation.</span></span>

## SignalR

<span data-ttu-id="8913b-105">SignalR został ponownie zapisany dla ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="8913b-105">SignalR has been rewritten for ASP.NET Core 2.1.</span></span> <span data-ttu-id="8913b-106">ASP.NET Core SignalR obejmuje kilka ulepszeń:</span><span class="sxs-lookup"><span data-stu-id="8913b-106">ASP.NET Core SignalR includes a number of improvements:</span></span>

* <span data-ttu-id="8913b-107">Uproszczony model skalowalny w poziomie.</span><span class="sxs-lookup"><span data-stu-id="8913b-107">A simplified scale-out model.</span></span>
* <span data-ttu-id="8913b-108">Nowy klient JavaScript bez zależności jQuery.</span><span class="sxs-lookup"><span data-stu-id="8913b-108">A new JavaScript client with no jQuery dependency.</span></span>
* <span data-ttu-id="8913b-109">Nowy, kompaktowy protokół binarny oparty na MessagePack.</span><span class="sxs-lookup"><span data-stu-id="8913b-109">A new compact binary protocol based on MessagePack.</span></span>
* <span data-ttu-id="8913b-110">Obsługa niestandardowych protokołów.</span><span class="sxs-lookup"><span data-stu-id="8913b-110">Support for custom protocols.</span></span>
* <span data-ttu-id="8913b-111">Nowy model odpowiedzi przesyłania strumieniowego.</span><span class="sxs-lookup"><span data-stu-id="8913b-111">A new streaming response model.</span></span>
* <span data-ttu-id="8913b-112">Obsługa klientów w oparciu o usługi WebSockets.</span><span class="sxs-lookup"><span data-stu-id="8913b-112">Support for clients based on bare WebSockets.</span></span>

<span data-ttu-id="8913b-113">Aby uzyskać więcej informacji, [zobacz SignalR ASP.NET Core ](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="8913b-113">For more information, see [ASP.NET Core SignalR](xref:signalr/introduction).</span></span>

## <a name="no-locrazor-class-libraries"></a><span data-ttu-id="8913b-114">Razor biblioteki klas</span><span class="sxs-lookup"><span data-stu-id="8913b-114">Razor class libraries</span></span>

<span data-ttu-id="8913b-115">ASP.NET Core 2,1 ułatwia tworzenie i dołączanie Razor interfejsu użytkownika opartego na interfejsie w bibliotece i udostępnianie go w wielu projektach.</span><span class="sxs-lookup"><span data-stu-id="8913b-115">ASP.NET Core 2.1 makes it easier to build and include Razor-based UI in a library and share it across multiple projects.</span></span> <span data-ttu-id="8913b-116">Nowy Razor zestaw SDK umożliwia tworzenie Razor plików w projekcie biblioteki klas, który można spakować do pakietu NuGet.</span><span class="sxs-lookup"><span data-stu-id="8913b-116">The new Razor SDK enables building Razor files into a class library project that can be packaged into a NuGet package.</span></span> <span data-ttu-id="8913b-117">Widoki i strony w bibliotekach są automatycznie odnajdywane i mogą zostać zastąpione przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="8913b-117">Views and pages in libraries are automatically discovered and can be overridden by the app.</span></span> <span data-ttu-id="8913b-118">Przez integrację kompilacji Razor z kompilacją:</span><span class="sxs-lookup"><span data-stu-id="8913b-118">By integrating Razor compilation into the build:</span></span>

* <span data-ttu-id="8913b-119">Czas uruchamiania aplikacji jest znacznie szybszy.</span><span class="sxs-lookup"><span data-stu-id="8913b-119">The app startup time is significantly faster.</span></span>
* <span data-ttu-id="8913b-120">Szybkie aktualizacje Razor widoków i stron w środowisku uruchomieniowym są nadal dostępne jako część iteracyjnego przepływu pracy programistycznej.</span><span class="sxs-lookup"><span data-stu-id="8913b-120">Fast updates to Razor views and pages at runtime are still available as part of an iterative development workflow.</span></span>

<span data-ttu-id="8913b-121">Aby uzyskać więcej informacji, zobacz [Tworzenie interfejsu użytkownika wielokrotnego użytku przy użyciu Razor biblioteki klas](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="8913b-121">For more information, see [Create reusable UI using the Razor Class Library project](xref:razor-pages/ui-class).</span></span>

## <a name="no-locidentity-ui-library--scaffolding"></a><span data-ttu-id="8913b-122">Identity Tworzenie szkieletu & biblioteki interfejsu użytkownika</span><span class="sxs-lookup"><span data-stu-id="8913b-122">Identity UI library & scaffolding</span></span>

<span data-ttu-id="8913b-123">ASP.NET Core 2,1 stanowi [ASP.NET Core Identity](xref:security/authentication/identity) [ Razor bibliotekę klas](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="8913b-123">ASP.NET Core 2.1 provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="8913b-124">Aplikacje, które obejmują, Identity mogą zastosować nowy Identity szkielet, aby selektywnie dodać kod źródłowy znajdujący się w Identity Razor bibliotece klas (RCL).</span><span class="sxs-lookup"><span data-stu-id="8913b-124">Apps that include Identity can apply the new Identity scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="8913b-125">Może być konieczne wygenerowanie kodu źródłowego, aby można było zmodyfikować kod i zmienić zachowanie.</span><span class="sxs-lookup"><span data-stu-id="8913b-125">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="8913b-126">Na przykład możesz poinstruować szkielet, aby wygenerował kod używany w rejestracji.</span><span class="sxs-lookup"><span data-stu-id="8913b-126">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="8913b-127">Wygenerowany kod ma pierwszeństwo przed tym samym kodem w Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="8913b-127">Generated code takes precedence over the same code in the Identity RCL.</span></span>

<span data-ttu-id="8913b-128">Aplikacje, które **nie** obejmują uwierzytelniania, mogą zastosować Identity szkieleter w celu dodania Identity pakietu RCL.</span><span class="sxs-lookup"><span data-stu-id="8913b-128">Apps that do **not** include authentication can apply the Identity scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="8913b-129">Dostępna jest opcja wybierania Identity kodu do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="8913b-129">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="8913b-130">Aby uzyskać więcej informacji, zobacz [szkielet Identity w ASP.NET Core projekty](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="8913b-130">For more information, see [Scaffold Identity in ASP.NET Core projects](xref:security/authentication/scaffold-identity).</span></span>

## <a name="https"></a><span data-ttu-id="8913b-131">HTTPS</span><span class="sxs-lookup"><span data-stu-id="8913b-131">HTTPS</span></span>

<span data-ttu-id="8913b-132">Dzięki zwiększeniu poziomu zabezpieczeń i ochrony prywatności włączenie protokołu HTTPS dla aplikacji sieci Web jest ważne.</span><span class="sxs-lookup"><span data-stu-id="8913b-132">With the increased focus on security and privacy, enabling HTTPS for web apps is important.</span></span> <span data-ttu-id="8913b-133">Wymuszanie protokołu HTTPS staje się coraz bardziej rygorystyczne w sieci Web.</span><span class="sxs-lookup"><span data-stu-id="8913b-133">HTTPS enforcement is becoming increasingly strict on the web.</span></span> <span data-ttu-id="8913b-134">Lokacje, które nie używają protokołu HTTPS, są uznawane za niezabezpieczone.</span><span class="sxs-lookup"><span data-stu-id="8913b-134">Sites that don't use HTTPS are considered insecure.</span></span> <span data-ttu-id="8913b-135">Przeglądarki (chrom, Mozilla) zaczynają wymuszać, że funkcje sieci Web muszą być używane z bezpiecznego kontekstu.</span><span class="sxs-lookup"><span data-stu-id="8913b-135">Browsers (Chromium, Mozilla) are starting to enforce that web features must be used from a secure context.</span></span> <span data-ttu-id="8913b-136">[Rodo](xref:security/gdpr) wymaga użycia protokołu HTTPS do ochrony prywatności użytkowników.</span><span class="sxs-lookup"><span data-stu-id="8913b-136">[GDPR](xref:security/gdpr) requires the use of HTTPS to protect user privacy.</span></span> <span data-ttu-id="8913b-137">Korzystanie z protokołu HTTPS w środowisku produkcyjnym ma krytyczne znaczenie, dzięki czemu korzystanie z protokołu HTTPS podczas programowania może pomóc w zapobieganiu problemom z wdrażaniem (na przykład niezabezpieczonych łączy).</span><span class="sxs-lookup"><span data-stu-id="8913b-137">While using HTTPS in production is critical, using HTTPS in development can help prevent issues in deployment (for example, insecure links).</span></span> <span data-ttu-id="8913b-138">ASP.NET Core 2,1 zawiera szereg ulepszeń, które ułatwiają korzystanie z protokołu HTTPS w programowaniu i Konfigurowanie protokołu HTTPS w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="8913b-138">ASP.NET Core 2.1 includes a number of improvements that make it easier to use HTTPS in development and to configure HTTPS in production.</span></span> <span data-ttu-id="8913b-139">Aby uzyskać więcej informacji, zobacz [Wymuszanie protokołu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="8913b-139">For more information, see [Enforce HTTPS](xref:security/enforcing-ssl).</span></span>

### <a name="on-by-default"></a><span data-ttu-id="8913b-140">Domyślnie włączone</span><span class="sxs-lookup"><span data-stu-id="8913b-140">On by default</span></span>

<span data-ttu-id="8913b-141">Aby ułatwić tworzenie bezpiecznych witryn internetowych, protokół HTTPS jest teraz domyślnie włączony.</span><span class="sxs-lookup"><span data-stu-id="8913b-141">To facilitate secure website development, HTTPS  is now enabled by default.</span></span> <span data-ttu-id="8913b-142">Począwszy od 2,1, Kestrel nasłuchuje, `https://localhost:5001` gdy obecny jest lokalny certyfikat programistyczny.</span><span class="sxs-lookup"><span data-stu-id="8913b-142">Starting in 2.1, Kestrel listens on `https://localhost:5001` when a local development certificate is present.</span></span> <span data-ttu-id="8913b-143">Tworzony jest certyfikat programistyczny:</span><span class="sxs-lookup"><span data-stu-id="8913b-143">A development certificate is created:</span></span>

* <span data-ttu-id="8913b-144">W ramach pierwszego uruchomienia zestaw .NET Core SDK, gdy zestaw SDK jest używany po raz pierwszy.</span><span class="sxs-lookup"><span data-stu-id="8913b-144">As part of the .NET Core SDK first-run experience, when you use the SDK for the first time.</span></span>
* <span data-ttu-id="8913b-145">Ręczne korzystanie z nowego `dev-certs` Narzędzia.</span><span class="sxs-lookup"><span data-stu-id="8913b-145">Manually using the new `dev-certs` tool.</span></span>

<span data-ttu-id="8913b-146">Uruchom, `dotnet dev-certs https --trust` Aby zaufać certyfikatowi.</span><span class="sxs-lookup"><span data-stu-id="8913b-146">Run `dotnet dev-certs https --trust` to trust the certificate.</span></span>

### <a name="https-redirection-and-enforcement"></a><span data-ttu-id="8913b-147">Przekierowywanie i wymuszanie protokołu HTTPS</span><span class="sxs-lookup"><span data-stu-id="8913b-147">HTTPS redirection and enforcement</span></span>

<span data-ttu-id="8913b-148">Aplikacje sieci Web zazwyczaj muszą nasłuchiwać zarówno protokół HTTP, jak i HTTPS, a następnie przekierować cały ruch HTTP do protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8913b-148">Web apps typically need to listen on both HTTP and HTTPS, but then redirect all HTTP traffic to HTTPS.</span></span> <span data-ttu-id="8913b-149">W 2,1, wyspecjalizowane oprogramowanie pośredniczące do przekierowywania HTTPS, które inteligentnie przekierowuje się w oparciu o obecność konfiguracji lub powiązanych portów serwera.</span><span class="sxs-lookup"><span data-stu-id="8913b-149">In 2.1, specialized HTTPS redirection middleware that intelligently redirects based on the presence of configuration or bound server ports has been introduced.</span></span>

<span data-ttu-id="8913b-150">Korzystanie z protokołu HTTPS może być realizowane przy użyciu [protokołu HTTP Strict Transport Security Protocol (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts).</span><span class="sxs-lookup"><span data-stu-id="8913b-150">Use of HTTPS can be further enforced using [HTTP Strict Transport Security Protocol (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="8913b-151">HSTS instruuje przeglądarki, aby zawsze mogli uzyskać dostęp do witryny za pośrednictwem protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8913b-151">HSTS instructs browsers to always access the site via HTTPS.</span></span> <span data-ttu-id="8913b-152">ASP.NET Core 2,1 dodaje oprogramowanie pośredniczące HSTS, które obsługuje opcje maksymalnego wieku, poddomen i listy wstępnego ładowania HSTS.</span><span class="sxs-lookup"><span data-stu-id="8913b-152">ASP.NET Core 2.1 adds HSTS middleware that supports options for max age, subdomains, and the HSTS preload list.</span></span>

### <a name="configuration-for-production"></a><span data-ttu-id="8913b-153">Konfiguracja dla środowiska produkcyjnego</span><span class="sxs-lookup"><span data-stu-id="8913b-153">Configuration for production</span></span>

<span data-ttu-id="8913b-154">W środowisku produkcyjnym należy jawnie skonfigurować protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8913b-154">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="8913b-155">W 2,1 został dodany domyślny schemat konfiguracji służący do konfigurowania protokołu HTTPS dla Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8913b-155">In 2.1, default configuration schema for configuring HTTPS for Kestrel has been added.</span></span> <span data-ttu-id="8913b-156">Aplikacje można skonfigurować do korzystania z:</span><span class="sxs-lookup"><span data-stu-id="8913b-156">Apps can be configured to use:</span></span>

* <span data-ttu-id="8913b-157">Wiele punktów końcowych, w tym adresów URL.</span><span class="sxs-lookup"><span data-stu-id="8913b-157">Multiple endpoints including the URLs.</span></span> <span data-ttu-id="8913b-158">Aby uzyskać więcej informacji, zobacz [Implementacja serwera sieci Web Kestrel: Konfiguracja punktu końcowego](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="8913b-158">For more information, see [Kestrel web server implementation: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>
* <span data-ttu-id="8913b-159">Certyfikat do użycia na potrzeby protokołu HTTPS z pliku znajdującego się na dysku lub z magazynu certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="8913b-159">The certificate to use for HTTPS either from a file on disk or from a certificate store.</span></span>

## <a name="gdpr"></a><span data-ttu-id="8913b-160">RODO</span><span class="sxs-lookup"><span data-stu-id="8913b-160">GDPR</span></span>

<span data-ttu-id="8913b-161">ASP.NET Core udostępnia interfejsy API i szablony, które pomagają spełnić niektóre wymagania dotyczące [ogólne rozporządzenie o ochronie danych UE (Rodo)](https://www.eugdpr.org/) .</span><span class="sxs-lookup"><span data-stu-id="8913b-161">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://www.eugdpr.org/) requirements.</span></span> <span data-ttu-id="8913b-162">Aby uzyskać więcej informacji, zobacz [Obsługa Rodo w ASP.NET Core](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="8913b-162">For more information, see [GDPR support in ASP.NET Core](xref:security/gdpr).</span></span> <span data-ttu-id="8913b-163">[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) pokazuje, jak używać programu i umożliwia testowanie większości punktów rozszerzenia Rodo i interfejsów API dodanych do szablonów ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="8913b-163">A [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) shows how to use and lets you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span>

## <a name="integration-tests"></a><span data-ttu-id="8913b-164">Testy integracji</span><span class="sxs-lookup"><span data-stu-id="8913b-164">Integration tests</span></span>

<span data-ttu-id="8913b-165">Wprowadzono nowy pakiet, który usprawnia tworzenie i wykonywanie testów.</span><span class="sxs-lookup"><span data-stu-id="8913b-165">A new package is introduced that streamlines test creation and execution.</span></span> <span data-ttu-id="8913b-166">Pakiet [Microsoft. AspNetCore. MVC. test](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) obsługuje następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="8913b-166">The [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) package handles the following tasks:</span></span>

* <span data-ttu-id="8913b-167">Kopiuje plik zależności (\* \* . deps\*) z testowanej aplikacji do folderu *bin* projektu testowego.</span><span class="sxs-lookup"><span data-stu-id="8913b-167">Copies the dependency file (*\*.deps*) from the tested app into the test project's *bin* folder.</span></span>
* <span data-ttu-id="8913b-168">Ustawia katalog główny zawartości dla elementu głównego projektu przetestowanej aplikacji, co umożliwia znalezienie plików statycznych i stron/widoków podczas wykonywania testów.</span><span class="sxs-lookup"><span data-stu-id="8913b-168">Sets the content root to the tested app's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="8913b-169">Udostępnia klasę [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) do usprawnienia uruchamiania przetestowanej aplikacji z [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span><span class="sxs-lookup"><span data-stu-id="8913b-169">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the tested app with [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span>

<span data-ttu-id="8913b-170">Poniższy test korzysta z [xUnit](https://xunit.github.io/) , aby sprawdzić, czy strona indeksu ładuje się z kodem stanu sukcesu i z prawidłowym nagłówkiem Content-Type:</span><span class="sxs-lookup"><span data-stu-id="8913b-170">The following test uses [xUnit](https://xunit.github.io/) to check that the Index page loads with a success status code and with the correct Content-Type header:</span></span>

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

<span data-ttu-id="8913b-171">Aby uzyskać więcej informacji, zobacz temat [testy integracji](xref:test/integration-tests) .</span><span class="sxs-lookup"><span data-stu-id="8913b-171">For more information, see the [Integration tests](xref:test/integration-tests) topic.</span></span>

## <a name="apicontroller-actionresultt"></a><span data-ttu-id="8913b-172">[ApiController], ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="8913b-172">[ApiController], ActionResult\<T></span></span>

<span data-ttu-id="8913b-173">ASP.NET Core 2,1 dodaje nowe konwencje programowania, które ułatwiają tworzenie czystych i opisowych interfejsów API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="8913b-173">ASP.NET Core 2.1 adds new programming conventions that make it easier to build clean and descriptive web APIs.</span></span> <span data-ttu-id="8913b-174">`ActionResult<T>` to nowy typ, który został dodany, aby umożliwić aplikacji zwracanie typu odpowiedzi lub dowolnego innego wyniku akcji (podobnego do IActionResult), przy czym wskazuje typ odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="8913b-174">`ActionResult<T>` is a new type added to allow an app to return either a response type or any other action result (similar to IActionResult), while still indicating the response type.</span></span> <span data-ttu-id="8913b-175">`[ApiController]`Atrybut został również dodany jako sposób, aby można było wyrazić zgodę na konwencje i zachowania specyficzne dla interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="8913b-175">The `[ApiController]` attribute has also been added as the way to opt in to Web API-specific conventions and behaviors.</span></span>

<span data-ttu-id="8913b-176">Aby uzyskać więcej informacji, zobacz [Tworzenie internetowych interfejsów API za pomocą ASP.NET Core](xref:web-api/index).</span><span class="sxs-lookup"><span data-stu-id="8913b-176">For more information, see [Build Web APIs with ASP.NET Core](xref:web-api/index).</span></span>

## <a name="ihttpclientfactory"></a><span data-ttu-id="8913b-177">IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="8913b-177">IHttpClientFactory</span></span>

<span data-ttu-id="8913b-178">ASP.NET Core 2,1 obejmuje nową `IHttpClientFactory` usługę, która ułatwia konfigurowanie i używanie wystąpień `HttpClient` w aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="8913b-178">ASP.NET Core 2.1 includes a new `IHttpClientFactory` service that makes it easier to configure and consume instances of `HttpClient` in apps.</span></span> <span data-ttu-id="8913b-179">`HttpClient` ma już koncepcję delegowania programów obsługi, które mogą być połączone ze sobą w przypadku wychodzących żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="8913b-179">`HttpClient` already has the concept of delegating handlers that could be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="8913b-180">Fabryka:</span><span class="sxs-lookup"><span data-stu-id="8913b-180">The factory:</span></span>

* <span data-ttu-id="8913b-181">Sprawia, że rejestrowanie wystąpień `HttpClient` na nazwę klienta jest bardziej intuicyjne.</span><span class="sxs-lookup"><span data-stu-id="8913b-181">Makes registering of instances of `HttpClient` per named client more intuitive.</span></span>
* <span data-ttu-id="8913b-182">Implementuje procedurę obsługi Polly, która umożliwia używanie zasad Polly do ponawiania, CircuitBreakers itd.</span><span class="sxs-lookup"><span data-stu-id="8913b-182">Implements a Polly handler that allows Polly policies to be used for Retry, CircuitBreakers, etc.</span></span>

<span data-ttu-id="8913b-183">Aby uzyskać więcej informacji, zobacz [Inicjowanie żądań HTTP](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="8913b-183">For more information, see [Initiate HTTP Requests](xref:fundamentals/http-requests).</span></span>

## <a name="kestrel-transport-configuration"></a><span data-ttu-id="8913b-184">Konfiguracja transportu Kestrel</span><span class="sxs-lookup"><span data-stu-id="8913b-184">Kestrel transport configuration</span></span>

<span data-ttu-id="8913b-185">W wersji ASP.NET Core 2,1 Kestrel domyślny transport nie jest już oparty na Libuv, ale zamiast w oparciu o zarządzane gniazda.</span><span class="sxs-lookup"><span data-stu-id="8913b-185">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="8913b-186">Aby uzyskać więcej informacji, zobacz [Kestrel Web Server implementation: transport Configuration](xref:fundamentals/servers/kestrel#transport-configuration).</span><span class="sxs-lookup"><span data-stu-id="8913b-186">For more information, see [Kestrel web server implementation: Transport configuration](xref:fundamentals/servers/kestrel#transport-configuration).</span></span>

## <a name="generic-host-builder"></a><span data-ttu-id="8913b-187">Ogólny Konstruktor hosta</span><span class="sxs-lookup"><span data-stu-id="8913b-187">Generic host builder</span></span>

<span data-ttu-id="8913b-188">Wprowadzono ogólny Konstruktor hosta ( `HostBuilder` ).</span><span class="sxs-lookup"><span data-stu-id="8913b-188">The Generic Host Builder (`HostBuilder`) has been introduced.</span></span> <span data-ttu-id="8913b-189">Tego konstruktora można używać w przypadku aplikacji, które nie przetwarzają żądań HTTP (wiadomości, zadań w tle itp.).</span><span class="sxs-lookup"><span data-stu-id="8913b-189">This builder can be used for apps that don't process HTTP requests (Messaging, background tasks, etc.).</span></span>

<span data-ttu-id="8913b-190">Aby uzyskać więcej informacji, zobacz [host ogólny programu .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="8913b-190">For more information, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

## <a name="updated-spa-templates"></a><span data-ttu-id="8913b-191">Zaktualizowane szablony SPA</span><span class="sxs-lookup"><span data-stu-id="8913b-191">Updated SPA templates</span></span>

<span data-ttu-id="8913b-192">Szablony aplikacji jednostronicowych umożliwiające przeznaczenie, reagowanie i reagowanie na Redux są aktualizowane w taki sposób, aby korzystały ze standardowych struktur projektu i systemów kompilacji dla każdej struktury.</span><span class="sxs-lookup"><span data-stu-id="8913b-192">The Single Page Application templates for Angular, React, and React with Redux are updated to use the standard project structures and build systems for each framework.</span></span>

<span data-ttu-id="8913b-193">Szablon kątowy jest oparty na kątowym interfejsie wiersza polecenia, a szablony reagowania opierają się na tworzeniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8913b-193">The Angular template is based on the Angular CLI, and the React templates are based on create-react-app.</span></span>

<span data-ttu-id="8913b-194">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="8913b-194">For more information, see:</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="no-locrazor-pages-search-for-no-locrazor-assets"></a><span data-ttu-id="8913b-195">Razor Strony szukają Razor elementów zawartości</span><span class="sxs-lookup"><span data-stu-id="8913b-195">Razor Pages search for Razor assets</span></span>

<span data-ttu-id="8913b-196">W 2,1 Razor strony szukają Razor zasobów (takich jak układy i części) w następujących katalogach w podanej kolejności:</span><span class="sxs-lookup"><span data-stu-id="8913b-196">In 2.1, Razor Pages search for Razor assets (such as layouts and partials) in the following directories in the listed order:</span></span>

1. <span data-ttu-id="8913b-197">Folder bieżące strony.</span><span class="sxs-lookup"><span data-stu-id="8913b-197">Current Pages folder.</span></span>
1. <span data-ttu-id="8913b-198">*/Pages/Shared/*</span><span class="sxs-lookup"><span data-stu-id="8913b-198">*/Pages/Shared/*</span></span>
1. <span data-ttu-id="8913b-199">*/Views/Shared/*</span><span class="sxs-lookup"><span data-stu-id="8913b-199">*/Views/Shared/*</span></span>

## <a name="no-locrazor-pages-in-an-area"></a><span data-ttu-id="8913b-200">Razor Strony w obszarze</span><span class="sxs-lookup"><span data-stu-id="8913b-200">Razor Pages in an area</span></span>

<span data-ttu-id="8913b-201">Razor Strony obsługują teraz [obszary](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="8913b-201">Razor Pages now support [areas](xref:mvc/controllers/areas).</span></span> <span data-ttu-id="8913b-202">Aby zobaczyć przykład obszarów, Utwórz nową Razor aplikację sieci Web dla poszczególnych kont użytkowników.</span><span class="sxs-lookup"><span data-stu-id="8913b-202">To see an example of areas, create a new Razor Pages web app with individual user accounts.</span></span> <span data-ttu-id="8913b-203">RazorAplikacja internetowa stron z pojedynczymi kontami użytkowników zawiera */Areas/ Identity /Pages*.</span><span class="sxs-lookup"><span data-stu-id="8913b-203">A Razor Pages web app with individual user accounts includes */Areas/Identity/Pages*.</span></span>

## <a name="mvc-compatibility-version"></a><span data-ttu-id="8913b-204">Wersja zgodności MVC</span><span class="sxs-lookup"><span data-stu-id="8913b-204">MVC compatibility version</span></span>

<span data-ttu-id="8913b-205"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>Metoda pozwala aplikacji na zgodę lub rezygnację z ewentualnych zmian w zachowaniu, wprowadzonych w ASP.NET Core MVC 2,1 lub nowszych.</span><span class="sxs-lookup"><span data-stu-id="8913b-205">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="8913b-206">Aby uzyskać więcej informacji, zobacz <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="8913b-206">For more information, see <xref:mvc/compatibility-version>.</span></span>

## <a name="migrate-from-20-to-21"></a><span data-ttu-id="8913b-207">Migrowanie z 2,0 do 2,1</span><span class="sxs-lookup"><span data-stu-id="8913b-207">Migrate from 2.0 to 2.1</span></span>

<span data-ttu-id="8913b-208">Zobacz [Migrowanie z ASP.NET Core 2,0 do 2,1](xref:migration/20_21).</span><span class="sxs-lookup"><span data-stu-id="8913b-208">See [Migrate from ASP.NET Core 2.0 to 2.1](xref:migration/20_21).</span></span>

## <a name="additional-information"></a><span data-ttu-id="8913b-209">Dodatkowe informacje</span><span class="sxs-lookup"><span data-stu-id="8913b-209">Additional information</span></span>

<span data-ttu-id="8913b-210">Aby uzyskać pełną listę zmian, zapoznaj się z [informacjami o wersji ASP.NET Core 2,1](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0).</span><span class="sxs-lookup"><span data-stu-id="8913b-210">For the complete list of changes, see the [ASP.NET Core 2.1 Release Notes](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0).</span></span>
