---
title: Konfigurowanie uwierzytelniania certyfikatów w ASP.NET Core
author: blowdart
description: Dowiedz się, jak skonfigurować uwierzytelnianie certyfikatów w ASP.NET Core dla usług IIS i HTTP.sys.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 07/16/2020
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
uid: security/authentication/certauth
ms.openlocfilehash: 71f05163c075a2ef88d5c606814925cdcef879d2
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253049"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a><span data-ttu-id="4f772-103">Konfigurowanie uwierzytelniania certyfikatów w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4f772-103">Configure certificate authentication in ASP.NET Core</span></span>

<span data-ttu-id="4f772-104">`Microsoft.AspNetCore.Authentication.Certificate` zawiera implementację podobną do [uwierzytelniania certyfikatu](https://tools.ietf.org/html/rfc5246#section-7.4.4) dla ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4f772-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="4f772-105">Uwierzytelnianie certyfikatu odbywa się na poziomie protokołu TLS, o ile nie zostanie kiedykolwiek przeASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4f772-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="4f772-106">Dokładniej, jest to procedura obsługi uwierzytelniania, która sprawdza poprawność certyfikatu, a następnie przekazuje zdarzenie, w którym można rozwiązać ten certyfikat do `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="4f772-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="4f772-107">[Skonfiguruj serwer](#configure-your-server-to-require-certificates) pod kątem uwierzytelniania przy użyciu certyfikatu, to usługi IIS, Kestrel, Azure Web Apps lub inne, z których korzystasz.</span><span class="sxs-lookup"><span data-stu-id="4f772-107">[Configure your server](#configure-your-server-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="4f772-108">Scenariusze dotyczące serwerów proxy i równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="4f772-108">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="4f772-109">Uwierzytelnianie certyfikatu jest scenariuszem stanowym głównie używanym w przypadku, gdy serwer proxy lub moduł równoważenia obciążenia nie obsługuje ruchu między klientami i serwerami.</span><span class="sxs-lookup"><span data-stu-id="4f772-109">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="4f772-110">Jeśli jest używany serwer proxy lub moduł równoważenia obciążenia, uwierzytelnianie certyfikatu działa tylko wtedy, gdy serwer proxy lub moduł równoważenia obciążenia:</span><span class="sxs-lookup"><span data-stu-id="4f772-110">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="4f772-111">Obsługuje uwierzytelnianie.</span><span class="sxs-lookup"><span data-stu-id="4f772-111">Handles the authentication.</span></span>
* <span data-ttu-id="4f772-112">Przekazuje informacje o uwierzytelnianiu użytkownika do aplikacji (na przykład w nagłówku żądania), która działa na informacje o uwierzytelnianiu.</span><span class="sxs-lookup"><span data-stu-id="4f772-112">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="4f772-113">Alternatywą dla uwierzytelniania certyfikatu w środowiskach, w których są używane serwery proxy i moduły równoważenia obciążenia, są Active Directory usług federacyjnych (AD FS) za pomocą OpenID Connect Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="4f772-113">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="4f772-114">Wprowadzenie</span><span class="sxs-lookup"><span data-stu-id="4f772-114">Get started</span></span>

<span data-ttu-id="4f772-115">Uzyskaj certyfikat HTTPS, zastosuj go i [Skonfiguruj serwer](#configure-your-server-to-require-certificates) tak, aby wymagał certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="4f772-115">Acquire an HTTPS certificate, apply it, and [configure your server](#configure-your-server-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="4f772-116">W aplikacji sieci Web Dodaj odwołanie do pakietu [Microsoft. AspNetCore. Authentication. Certificate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Certificate) .</span><span class="sxs-lookup"><span data-stu-id="4f772-116">In your web app, add a reference to the [Microsoft.AspNetCore.Authentication.Certificate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Certificate) package.</span></span> <span data-ttu-id="4f772-117">Następnie w `Startup.ConfigureServices` metodzie Zadzwoń `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` z własnymi opcjami, podając delegata w `OnCertificateValidated` celu wykonania dodatkowej weryfikacji dla certyfikatu klienta wysyłanego z żądaniami.</span><span class="sxs-lookup"><span data-stu-id="4f772-117">Then in the `Startup.ConfigureServices` method, call `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="4f772-118">Zmień te informacje na `ClaimsPrincipal` i ustaw dla `context.Principal` właściwości.</span><span class="sxs-lookup"><span data-stu-id="4f772-118">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="4f772-119">Jeśli uwierzytelnianie nie powiedzie się, ta procedura obsługi zwróci `403 (Forbidden)` odpowiedź zamiast elementu `401 (Unauthorized)` , zgodnie z oczekiwaniami.</span><span class="sxs-lookup"><span data-stu-id="4f772-119">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="4f772-120">Powodem jest to, że uwierzytelnianie powinno nastąpić podczas początkowego połączenia TLS.</span><span class="sxs-lookup"><span data-stu-id="4f772-120">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="4f772-121">Przez czas, gdy dociera do programu obsługi, jest zbyt opóźniony.</span><span class="sxs-lookup"><span data-stu-id="4f772-121">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="4f772-122">Nie ma możliwości uaktualnienia połączenia z anonimowego połączenia z certyfikatem.</span><span class="sxs-lookup"><span data-stu-id="4f772-122">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="4f772-123">Dodaj również `app.UseAuthentication();` `Startup.Configure` metodę.</span><span class="sxs-lookup"><span data-stu-id="4f772-123">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="4f772-124">W przeciwnym razie `HttpContext.User` nie zostanie ustawiona jako `ClaimsPrincipal` utworzona na podstawie certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="4f772-124">Otherwise, the `HttpContext.User` will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="4f772-125">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="4f772-125">For example:</span></span>

::: moniker range=">= aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate()
        // Adding an ICertificateValidationCache results in certificate auth caching the results.
        // The default implementation uses a memory cache.
        .AddCertificateCache();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

<span data-ttu-id="4f772-126">W powyższym przykładzie przedstawiono domyślny sposób dodawania uwierzytelniania przy użyciu certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="4f772-126">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="4f772-127">Program obsługi konstruuje jednostkę główną użytkownika przy użyciu wspólnych właściwości certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="4f772-127">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="4f772-128">Konfigurowanie weryfikacji certyfikatu</span><span class="sxs-lookup"><span data-stu-id="4f772-128">Configure certificate validation</span></span>

<span data-ttu-id="4f772-129">`CertificateAuthenticationOptions`Program obsługi ma pewne wbudowane walidacje, które są minimalnymi walidacjami, które należy wykonać na certyfikacie.</span><span class="sxs-lookup"><span data-stu-id="4f772-129">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="4f772-130">Każdy z tych ustawień jest domyślnie włączony.</span><span class="sxs-lookup"><span data-stu-id="4f772-130">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="4f772-131">AllowedCertificateTypes = łańcuchy, SelfSigned lub wszystkie (łańcuchowo | SelfSigned)</span><span class="sxs-lookup"><span data-stu-id="4f772-131">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="4f772-132">Wartość domyślna: `CertificateTypes.Chained`</span><span class="sxs-lookup"><span data-stu-id="4f772-132">Default value: `CertificateTypes.Chained`</span></span>

<span data-ttu-id="4f772-133">Ten test sprawdza, czy dozwolony jest tylko odpowiedni typ certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="4f772-133">This check validates that only the appropriate certificate type is allowed.</span></span> <span data-ttu-id="4f772-134">Jeśli aplikacja korzysta z certyfikatów z podpisem własnym, ta opcja musi być ustawiona na `CertificateTypes.All` lub `CertificateTypes.SelfSigned` .</span><span class="sxs-lookup"><span data-stu-id="4f772-134">If the app is using self-signed certificates, this option needs to be set to `CertificateTypes.All` or `CertificateTypes.SelfSigned`.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="4f772-135">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="4f772-135">ValidateCertificateUse</span></span>

<span data-ttu-id="4f772-136">Wartość domyślna: `true`</span><span class="sxs-lookup"><span data-stu-id="4f772-136">Default value: `true`</span></span>

<span data-ttu-id="4f772-137">Ten test sprawdza, czy certyfikat przedstawiony przez klienta ma rozszerzone użycie klucza uwierzytelniania klienta (EKU) lub nie rozszerzeń EKU w ogóle.</span><span class="sxs-lookup"><span data-stu-id="4f772-137">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="4f772-138">Zgodnie ze specyfikacją, jeśli nie określono rozszerzenia EKU, wszystkie rozszerzeń EKU są uznawane za prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="4f772-138">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="4f772-139">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="4f772-139">ValidateValidityPeriod</span></span>

<span data-ttu-id="4f772-140">Wartość domyślna: `true`</span><span class="sxs-lookup"><span data-stu-id="4f772-140">Default value: `true`</span></span>

<span data-ttu-id="4f772-141">Ten test sprawdza, czy certyfikat jest w jego okresie ważności.</span><span class="sxs-lookup"><span data-stu-id="4f772-141">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="4f772-142">W przypadku każdego żądania program obsługi zapewnia, że certyfikat, który był ważny, gdy był prezentowany, nie upłynął podczas bieżącej sesji.</span><span class="sxs-lookup"><span data-stu-id="4f772-142">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="4f772-143">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="4f772-143">RevocationFlag</span></span>

<span data-ttu-id="4f772-144">Wartość domyślna: `X509RevocationFlag.ExcludeRoot`</span><span class="sxs-lookup"><span data-stu-id="4f772-144">Default value: `X509RevocationFlag.ExcludeRoot`</span></span>

<span data-ttu-id="4f772-145">Flaga określająca, które certyfikaty w łańcuchu są sprawdzane pod kątem odwołania.</span><span class="sxs-lookup"><span data-stu-id="4f772-145">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="4f772-146">Sprawdzanie odwołań jest wykonywane tylko wtedy, gdy certyfikat jest powiązany z certyfikatem głównym.</span><span class="sxs-lookup"><span data-stu-id="4f772-146">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="4f772-147">Odwołaniemode</span><span class="sxs-lookup"><span data-stu-id="4f772-147">RevocationMode</span></span>

<span data-ttu-id="4f772-148">Wartość domyślna: `X509RevocationMode.Online`</span><span class="sxs-lookup"><span data-stu-id="4f772-148">Default value: `X509RevocationMode.Online`</span></span>

<span data-ttu-id="4f772-149">Flaga określająca sposób sprawdzania odwołania.</span><span class="sxs-lookup"><span data-stu-id="4f772-149">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="4f772-150">Określenie, że sprawdzanie online może skutkować długim opóźnieniem podczas kontaktowania się z urzędem certyfikacji.</span><span class="sxs-lookup"><span data-stu-id="4f772-150">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="4f772-151">Sprawdzanie odwołań jest wykonywane tylko wtedy, gdy certyfikat jest powiązany z certyfikatem głównym.</span><span class="sxs-lookup"><span data-stu-id="4f772-151">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="4f772-152">Czy mogę skonfigurować aplikację, aby wymagać certyfikatu tylko w określonych ścieżkach?</span><span class="sxs-lookup"><span data-stu-id="4f772-152">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="4f772-153">Nie jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="4f772-153">This isn't possible.</span></span> <span data-ttu-id="4f772-154">Należy pamiętać, że wymiana certyfikatów jest wykonywana na początku konwersacji HTTPS, przez serwer przed odebraniem pierwszego żądania w ramach tego połączenia, aby nie było możliwe Określanie zakresu na podstawie pól żądania.</span><span class="sxs-lookup"><span data-stu-id="4f772-154">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="4f772-155">Zdarzenia programu obsługi</span><span class="sxs-lookup"><span data-stu-id="4f772-155">Handler events</span></span>

<span data-ttu-id="4f772-156">Program obsługi ma dwa zdarzenia:</span><span class="sxs-lookup"><span data-stu-id="4f772-156">The handler has two events:</span></span>

* <span data-ttu-id="4f772-157">`OnAuthenticationFailed`: Wywołuje się, gdy wyjątek występuje podczas uwierzytelniania i pozwala na reagowanie.</span><span class="sxs-lookup"><span data-stu-id="4f772-157">`OnAuthenticationFailed`: Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="4f772-158">`OnCertificateValidated`: Wywołane po zweryfikowaniu certyfikatu, pomyślnie utworzono weryfikację i domyślny podmiot zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="4f772-158">`OnCertificateValidated`: Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="4f772-159">To zdarzenie umożliwia wykonywanie własnych weryfikacji i rozszerzanie lub zastępowanie podmiotu zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="4f772-159">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="4f772-160">Przykłady obejmują:</span><span class="sxs-lookup"><span data-stu-id="4f772-160">For examples include:</span></span>
  * <span data-ttu-id="4f772-161">Ustalanie, czy certyfikat jest znany dla usług.</span><span class="sxs-lookup"><span data-stu-id="4f772-161">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="4f772-162">Konstruowanie własnego podmiotu zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="4f772-162">Constructing your own principal.</span></span> <span data-ttu-id="4f772-163">Rozważmy następujący przykład w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4f772-163">Consider the following example in `Startup.ConfigureServices`:</span></span>

    ```csharp
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate(options =>
        {
            options.Events = new CertificateAuthenticationEvents
            {
                OnCertificateValidated = context =>
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject,
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(ClaimTypes.Name,
                            context.ClientCertificate.Subject,
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };
    
                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
    
                    return Task.CompletedTask;
                }
            };
        });
    ```

<span data-ttu-id="4f772-164">Jeśli okaże się, że certyfikat ruchu przychodzącego nie spełnia dodatkowej weryfikacji, wywołaj `context.Fail("failure reason")` z przyczynę niepowodzenia.</span><span class="sxs-lookup"><span data-stu-id="4f772-164">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="4f772-165">W przypadku rzeczywistej funkcjonalności prawdopodobnie chcesz wywołać usługę zarejestrowaną w iniekcji zależności, która łączy się z bazą danych lub innym typem magazynu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4f772-165">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="4f772-166">Uzyskaj dostęp do usługi przy użyciu kontekstu przesłanego do obiektu delegowanego.</span><span class="sxs-lookup"><span data-stu-id="4f772-166">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="4f772-167">Rozważmy następujący przykład w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4f772-167">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var validationService =
                    context.HttpContext.RequestServices
                        .GetRequiredService<ICertificateValidationService>();
                
                if (validationService.ValidateCertificate(
                    context.ClientCertificate))
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(
                            ClaimTypes.Name, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };

                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
                }                     

                return Task.CompletedTask;
            }
        };
    });
```

<span data-ttu-id="4f772-168">Koncepcyjnie sprawdzenie poprawności certyfikatu jest problemem z autoryzacją.</span><span class="sxs-lookup"><span data-stu-id="4f772-168">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="4f772-169">Dodanie kontroli, na przykład wystawcy lub odcisk palca w zasadach autoryzacji, a nie wewnątrz `OnCertificateValidated` , jest doskonale akceptowalne.</span><span class="sxs-lookup"><span data-stu-id="4f772-169">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-server-to-require-certificates"></a><span data-ttu-id="4f772-170">Skonfiguruj serwer tak, aby wymagał certyfikatów</span><span class="sxs-lookup"><span data-stu-id="4f772-170">Configure your server to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="4f772-171">Kestrel</span><span class="sxs-lookup"><span data-stu-id="4f772-171">Kestrel</span></span>

<span data-ttu-id="4f772-172">W *program.cs* Skonfiguruj Kestrel w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="4f772-172">In *Program.cs*, configure Kestrel as follows:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}

public static IHostBuilder CreateHostBuilder(string[] args)
{
    return Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(o =>
            {
                o.ConfigureHttpsDefaults(o => 
            o.ClientCertificateMode = 
                ClientCertificateMode.RequireCertificate);
            });
        });
}
```

> [!NOTE]
> <span data-ttu-id="4f772-173">Punkty końcowe utworzone przez wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **przed** wywołaniem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nie mają zastosowania wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="4f772-173">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="iis"></a><span data-ttu-id="4f772-174">IIS</span><span class="sxs-lookup"><span data-stu-id="4f772-174">IIS</span></span>

<span data-ttu-id="4f772-175">Wykonaj następujące kroki w Menedżerze usług IIS:</span><span class="sxs-lookup"><span data-stu-id="4f772-175">Complete the following steps in IIS Manager:</span></span>

1. <span data-ttu-id="4f772-176">Wybierz swoją witrynę z karty **połączenia** .</span><span class="sxs-lookup"><span data-stu-id="4f772-176">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="4f772-177">Kliknij dwukrotnie opcję **Ustawienia protokołu SSL** w oknie **Widok funkcji** .</span><span class="sxs-lookup"><span data-stu-id="4f772-177">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="4f772-178">Zaznacz pole wyboru **Wymagaj protokołu SSL** , a następnie wybierz przycisk **Wymagaj** przycisku radiowego w sekcji **Certyfikaty klienta** .</span><span class="sxs-lookup"><span data-stu-id="4f772-178">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![Ustawienia certyfikatu klienta w usługach IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="4f772-180">Azure i niestandardowe serwery proxy sieci Web</span><span class="sxs-lookup"><span data-stu-id="4f772-180">Azure and custom web proxies</span></span>

<span data-ttu-id="4f772-181">Zapoznaj się z [dokumentacją hosta i Wdróż](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) , jak skonfigurować oprogramowanie pośredniczące przesyłania certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="4f772-181">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>

### <a name="use-certificate-authentication-in-azure-web-apps"></a><span data-ttu-id="4f772-182">Używanie uwierzytelniania certyfikatów na platformie Azure Web Apps</span><span class="sxs-lookup"><span data-stu-id="4f772-182">Use certificate authentication in Azure Web Apps</span></span>

<span data-ttu-id="4f772-183">Na platformie Azure nie jest wymagana żadna konfiguracja przekazywania.</span><span class="sxs-lookup"><span data-stu-id="4f772-183">No forwarding configuration is required for Azure.</span></span> <span data-ttu-id="4f772-184">Jest to już konfiguracja w oprogramowaniu pośredniczącym do przesyłania dalej certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="4f772-184">This is already setup in the certificate forwarding middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="4f772-185">Wymaga to obecności CertificateForwardingMiddleware.</span><span class="sxs-lookup"><span data-stu-id="4f772-185">This requires that the CertificateForwardingMiddleware is present.</span></span>

### <a name="use-certificate-authentication-in-custom-web-proxies"></a><span data-ttu-id="4f772-186">Używanie uwierzytelniania certyfikatów w niestandardowych serwerach proxy sieci Web</span><span class="sxs-lookup"><span data-stu-id="4f772-186">Use certificate authentication in custom web proxies</span></span>

<span data-ttu-id="4f772-187">`AddCertificateForwarding`Metoda jest używana do określenia:</span><span class="sxs-lookup"><span data-stu-id="4f772-187">The `AddCertificateForwarding` method is used to specify:</span></span>

* <span data-ttu-id="4f772-188">Nazwa nagłówka klienta.</span><span class="sxs-lookup"><span data-stu-id="4f772-188">The client header name.</span></span>
* <span data-ttu-id="4f772-189">Sposób ładowania certyfikatu (za pomocą `HeaderConverter` Właściwości).</span><span class="sxs-lookup"><span data-stu-id="4f772-189">How the certificate is to be loaded (using the `HeaderConverter` property).</span></span>

<span data-ttu-id="4f772-190">W niestandardowych proxy sieci Web certyfikat jest przekazywane jako niestandardowy nagłówek żądania, na przykład `X-SSL-CERT` .</span><span class="sxs-lookup"><span data-stu-id="4f772-190">In custom web proxies, the certificate is passed as a custom request header, for example `X-SSL-CERT`.</span></span> <span data-ttu-id="4f772-191">W tym celu należy skonfigurować przekazywanie certyfikatów w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4f772-191">To use it, configure certificate forwarding in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCertificateForwarding(options =>
    {
        options.CertificateHeader = "X-SSL-CERT";
        options.HeaderConverter = (headerValue) =>
        {
            X509Certificate2 clientCertificate = null;

            if(!string.IsNullOrWhiteSpace(headerValue))
            {
                byte[] bytes = StringToByteArray(headerValue);
                clientCertificate = new X509Certificate2(bytes);
            }

            return clientCertificate;
        };
    });
}

private static byte[] StringToByteArray(string hex)
{
    int NumberChars = hex.Length;
    byte[] bytes = new byte[NumberChars / 2];

    for (int i = 0; i < NumberChars; i += 2)
    {
        bytes[i / 2] = Convert.ToByte(hex.Substring(i, 2), 16);
    }

    return bytes;
}
```

<span data-ttu-id="4f772-192">`Startup.Configure`Następnie Metoda dodaje oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="4f772-192">The `Startup.Configure` method then adds the middleware.</span></span> <span data-ttu-id="4f772-193">`UseCertificateForwarding` jest wywoływana przed wywołaniem do `UseAuthentication` i `UseAuthorization` :</span><span class="sxs-lookup"><span data-stu-id="4f772-193">`UseCertificateForwarding` is called before the calls to `UseAuthentication` and `UseAuthorization`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseRouting();

    app.UseCertificateForwarding();
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

<span data-ttu-id="4f772-194">Oddzielna Klasa może służyć do implementowania logiki walidacji.</span><span class="sxs-lookup"><span data-stu-id="4f772-194">A separate class can be used to implement validation logic.</span></span> <span data-ttu-id="4f772-195">Ponieważ ten sam certyfikat z podpisem własnym jest używany w tym przykładzie, należy się upewnić, że można używać tylko certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="4f772-195">Because the same self-signed certificate is used in this example, ensure that only your certificate can be used.</span></span> <span data-ttu-id="4f772-196">Sprawdź, czy odciski palca zarówno certyfikatu klienta, jak i certyfikatu serwera, w przeciwnym razie można użyć dowolnego certyfikatu i będzie on wystarczający do uwierzytelnienia.</span><span class="sxs-lookup"><span data-stu-id="4f772-196">Validate that the thumbprints of both the client certificate and the server certificate match, otherwise any certificate can be used and will be enough to authenticate.</span></span> <span data-ttu-id="4f772-197">Będzie on używany wewnątrz `AddCertificate` metody.</span><span class="sxs-lookup"><span data-stu-id="4f772-197">This would be used inside the `AddCertificate` method.</span></span> <span data-ttu-id="4f772-198">W przypadku korzystania z certyfikatów pośrednich lub podrzędnych można także zweryfikować temat lub wystawcę w tym miejscu.</span><span class="sxs-lookup"><span data-stu-id="4f772-198">You could also validate the subject or the issuer here if you're using intermediate or child certificates.</span></span>

```csharp
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            // Do not hardcode passwords in production code
            // Use thumbprint or key vault
            var cert = new X509Certificate2(
                Path.Combine("sts_dev_cert.pfx"), "1234");

            if (clientCertificate.Thumbprint == cert.Thumbprint)
            {
                return true;
            }

            return false;
        }
    }
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a><span data-ttu-id="4f772-199">Implementowanie HttpClient przy użyciu certyfikatu i HttpClientHandler</span><span class="sxs-lookup"><span data-stu-id="4f772-199">Implement an HttpClient using a certificate and the HttpClientHandler</span></span>

<span data-ttu-id="4f772-200">Można `HttpClientHandler` dodać bezpośrednio w konstruktorze `HttpClient` klasy.</span><span class="sxs-lookup"><span data-stu-id="4f772-200">The `HttpClientHandler` could be added directly in the constructor of the `HttpClient` class.</span></span> <span data-ttu-id="4f772-201">Należy zachować ostrożność podczas tworzenia wystąpień `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="4f772-201">Care should be taken when creating instances of the `HttpClient`.</span></span> <span data-ttu-id="4f772-202">`HttpClient`Następnie wyśle certyfikat z każdym żądaniem.</span><span class="sxs-lookup"><span data-stu-id="4f772-202">The `HttpClient` will then send the certificate with each request.</span></span>

```csharp
private async Task<JsonDocument> GetApiDataUsingHttpClientHandler()
{
    var cert = new X509Certificate2(Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(cert);
    var client = new HttpClient(handler);
     
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a><span data-ttu-id="4f772-203">Implementowanie HttpClient przy użyciu certyfikatu i nazwanego HttpClient z usługi IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="4f772-203">Implement an HttpClient using a certificate and a named HttpClient from IHttpClientFactory</span></span> 

<span data-ttu-id="4f772-204">W poniższym przykładzie certyfikat klienta jest dodawany do `HttpClientHandler` właściwości przy użyciu programu `ClientCertificates` obsługi.</span><span class="sxs-lookup"><span data-stu-id="4f772-204">In the following example, a client certificate is added to a `HttpClientHandler` using the `ClientCertificates` property from the handler.</span></span> <span data-ttu-id="4f772-205">Tej procedury obsługi można następnie użyć w nazwanym wystąpieniu klasy `HttpClient` przy użyciu `ConfigurePrimaryHttpMessageHandler` metody.</span><span class="sxs-lookup"><span data-stu-id="4f772-205">This handler can then be used in a named instance of an `HttpClient` using the `ConfigurePrimaryHttpMessageHandler` method.</span></span> <span data-ttu-id="4f772-206">Jest to konfiguracja w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4f772-206">This is setup in `Startup.ConfigureServices`:</span></span>

```csharp
var clientCertificate = 
    new X509Certificate2(
      Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
 
var handler = new HttpClientHandler();
handler.ClientCertificates.Add(clientCertificate);
 
services.AddHttpClient("namedClient", c =>
{
}).ConfigurePrimaryHttpMessageHandler(() => handler);
```

<span data-ttu-id="4f772-207">`IHttpClientFactory`Można następnie użyć, aby uzyskać nazwane wystąpienie z programem obsługi i certyfikatem.</span><span class="sxs-lookup"><span data-stu-id="4f772-207">The `IHttpClientFactory` can then be used to get the named instance with the handler and the certificate.</span></span> <span data-ttu-id="4f772-208">`CreateClient`Metoda o nazwie klienta zdefiniowanej w `Startup` klasie jest używana do uzyskiwania wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="4f772-208">The `CreateClient` method with the name of the client defined in the `Startup` class is used to get the instance.</span></span> <span data-ttu-id="4f772-209">Żądanie HTTP można wysłać przy użyciu klienta zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="4f772-209">The HTTP request can be sent using the client as required.</span></span>

```csharp
private readonly IHttpClientFactory _clientFactory;
 
public ApiService(IHttpClientFactory clientFactory)
{
    _clientFactory = clientFactory;
}
 
private async Task<JsonDocument> GetApiDataWithNamedClient()
{
    var client = _clientFactory.CreateClient("namedClient");
 
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

<span data-ttu-id="4f772-210">Jeśli do serwera zostanie wysłany prawidłowy certyfikat, dane są zwracane.</span><span class="sxs-lookup"><span data-stu-id="4f772-210">If the correct certificate is sent to the server, the data is returned.</span></span> <span data-ttu-id="4f772-211">Jeśli certyfikat lub nieprawidłowy certyfikat nie zostanie wysłany, zwracany jest kod stanu HTTP 403.</span><span class="sxs-lookup"><span data-stu-id="4f772-211">If no certificate or the wrong certificate is sent, an HTTP 403 status code is returned.</span></span>

### <a name="create-certificates-in-powershell"></a><span data-ttu-id="4f772-212">Tworzenie certyfikatów w programie PowerShell</span><span class="sxs-lookup"><span data-stu-id="4f772-212">Create certificates in PowerShell</span></span>

<span data-ttu-id="4f772-213">Tworzenie certyfikatów jest najtrudniejszą częścią w konfigurowaniu tego przepływu.</span><span class="sxs-lookup"><span data-stu-id="4f772-213">Creating the certificates is the hardest part in setting up this flow.</span></span> <span data-ttu-id="4f772-214">Certyfikat główny można utworzyć przy użyciu `New-SelfSignedCertificate` polecenia cmdlet programu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="4f772-214">A root certificate can be created using the `New-SelfSignedCertificate` PowerShell cmdlet.</span></span> <span data-ttu-id="4f772-215">Podczas tworzenia certyfikatu należy użyć silnego hasła.</span><span class="sxs-lookup"><span data-stu-id="4f772-215">When creating the certificate, use a strong password.</span></span> <span data-ttu-id="4f772-216">Ważne jest, aby dodać `KeyUsageProperty` parametr i parametr, `KeyUsage` jak pokazano.</span><span class="sxs-lookup"><span data-stu-id="4f772-216">It's important to add the `KeyUsageProperty` parameter and the `KeyUsage` parameter as shown.</span></span>

#### <a name="create-root-ca"></a><span data-ttu-id="4f772-217">Utwórz główny urząd certyfikacji</span><span class="sxs-lookup"><span data-stu-id="4f772-217">Create root CA</span></span>

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> <span data-ttu-id="4f772-218">`-DnsName`Wartość parametru musi być zgodna z elementem docelowym wdrożenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4f772-218">The `-DnsName` parameter value must match the deployment target of the app.</span></span> <span data-ttu-id="4f772-219">Na przykład "localhost" do programowania.</span><span class="sxs-lookup"><span data-stu-id="4f772-219">For example, "localhost" for development.</span></span>

#### <a name="install-in-the-trusted-root"></a><span data-ttu-id="4f772-220">Zainstaluj w zaufanym katalogu głównym</span><span class="sxs-lookup"><span data-stu-id="4f772-220">Install in the trusted root</span></span>

<span data-ttu-id="4f772-221">Certyfikat główny musi być zaufany w systemie hosta.</span><span class="sxs-lookup"><span data-stu-id="4f772-221">The root certificate needs to be trusted on your host system.</span></span> <span data-ttu-id="4f772-222">Certyfikat główny, który nie został utworzony przez urząd certyfikacji, nie będzie domyślnie zaufany.</span><span class="sxs-lookup"><span data-stu-id="4f772-222">A root certificate which was not created by a certificate authority won't be trusted by default.</span></span> <span data-ttu-id="4f772-223">Poniższy link wyjaśnia, jak można to zrobić w systemie Windows:</span><span class="sxs-lookup"><span data-stu-id="4f772-223">The following link explains how this can be accomplished on Windows:</span></span>

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a><span data-ttu-id="4f772-224">Certyfikat pośredni</span><span class="sxs-lookup"><span data-stu-id="4f772-224">Intermediate certificate</span></span>

<span data-ttu-id="4f772-225">Certyfikat pośredni można teraz utworzyć na podstawie certyfikatu głównego.</span><span class="sxs-lookup"><span data-stu-id="4f772-225">An intermediate certificate can now be created from the root certificate.</span></span> <span data-ttu-id="4f772-226">Nie jest to wymagane w przypadku wszystkich przypadków użycia, ale może być konieczne utworzenie wielu certyfikatów lub konieczność aktywowania lub wyłączenia grup certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="4f772-226">This isn't required for all use cases, but you might need to create many certificates or need to activate or disable groups of certificates.</span></span> <span data-ttu-id="4f772-227">`TextExtension`Parametr jest wymagany, aby ustawić długość ścieżki w podstawowych ograniczeniach certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="4f772-227">The `TextExtension` parameter is required to set the path length in the basic constraints of the certificate.</span></span>

<span data-ttu-id="4f772-228">Certyfikat pośredni można następnie dodać do zaufanego certyfikatu pośredniego w systemie hosta systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="4f772-228">The intermediate certificate can then be added to the trusted intermediate certificate in the Windows host system.</span></span>

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a><span data-ttu-id="4f772-229">Utwórz certyfikat podrzędny z certyfikatu pośredniego</span><span class="sxs-lookup"><span data-stu-id="4f772-229">Create child certificate from intermediate certificate</span></span>

<span data-ttu-id="4f772-230">Certyfikat podrzędny można utworzyć na podstawie certyfikatu pośredniego.</span><span class="sxs-lookup"><span data-stu-id="4f772-230">A child certificate can be created from the intermediate certificate.</span></span> <span data-ttu-id="4f772-231">Jest to jednostka końcowa i nie trzeba tworzyć więcej certyfikatów podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="4f772-231">This is the end entity and doesn't need to create more child certificates.</span></span>

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a><span data-ttu-id="4f772-232">Utwórz certyfikat podrzędny z certyfikatu głównego</span><span class="sxs-lookup"><span data-stu-id="4f772-232">Create child certificate from root certificate</span></span>

<span data-ttu-id="4f772-233">Certyfikat podrzędny można także utworzyć bezpośrednio na podstawie certyfikatu głównego.</span><span class="sxs-lookup"><span data-stu-id="4f772-233">A child certificate can also be created from the root certificate directly.</span></span> 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a><span data-ttu-id="4f772-234">Przykład certyfikatu pośredniego głównego — certyfikat</span><span class="sxs-lookup"><span data-stu-id="4f772-234">Example root - intermediate certificate - certificate</span></span>

```powershell
$mypwdroot = ConvertTo-SecureString -String "1234" -Force -AsPlainText
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

Get-ChildItem -Path cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwdroot

Export-Certificate -Cert cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 -FilePath root_ca_dev_damienbod.crt

$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\0C89639E4E2998A93E423F919B36D4009A0F9991 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 -FilePath child_a_dev_damienbod.crt

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\BA9BF91ED35538A01375EFC212A2F46104B33A44 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_b_from_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_b_from_a_dev_damienbod.com" 

Get-ChildItem -Path cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_b_from_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A -FilePath child_b_from_a_dev_damienbod.crt
```

<span data-ttu-id="4f772-235">W przypadku korzystania z certyfikatów głównych, pośrednich lub podrzędnych certyfikaty można zweryfikować przy użyciu odcisku palca lub PublicKey zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="4f772-235">When using the root, intermediate, or child certificates, the certificates can be validated using the Thumbprint or PublicKey as required.</span></span>

```csharp
using System.Collections.Generic;
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService 
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            return CheckIfThumbprintIsValid(clientCertificate);
        }

        private bool CheckIfThumbprintIsValid(X509Certificate2 clientCertificate)
        {
            var listOfValidThumbprints = new List<string>
            {
                "141594A0AE38CBBECED7AF680F7945CD51D8F28A",
                "0C89639E4E2998A93E423F919B36D4009A0F9991",
                "BA9BF91ED35538A01375EFC212A2F46104B33A44"
            };

            if (listOfValidThumbprints.Contains(clientCertificate.Thumbprint))
            {
                return true;
            }

            return false;
        }
    }
}
```

<a name="occ"></a>

::: moniker range=">= aspnetcore-5.0"

## <a name="certificate-validation-caching"></a><span data-ttu-id="4f772-236">Buforowanie weryfikacji certyfikatu</span><span class="sxs-lookup"><span data-stu-id="4f772-236">Certificate validation caching</span></span>

<span data-ttu-id="4f772-237">ASP.NET Core 5,0 i nowsze wersje obsługują możliwość włączania buforowania wyników walidacji.</span><span class="sxs-lookup"><span data-stu-id="4f772-237">ASP.NET Core 5.0 and later versions support the ability to enable caching of validation results.</span></span> <span data-ttu-id="4f772-238">Buforowanie znacznie poprawia wydajność uwierzytelniania certyfikatów, ponieważ Walidacja jest kosztowną operacją.</span><span class="sxs-lookup"><span data-stu-id="4f772-238">The caching dramatically improves performance of certificate authentication, as validation is an expensive operation.</span></span>

<span data-ttu-id="4f772-239">Domyślnie uwierzytelnianie certyfikatu powoduje wyłączenie buforowania.</span><span class="sxs-lookup"><span data-stu-id="4f772-239">By default, certificate authentication disables caching.</span></span> <span data-ttu-id="4f772-240">Aby włączyć buforowanie, wywołaj `AddCertificateCache` w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4f772-240">To enable caching, call `AddCertificateCache` in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate()
            .AddCertificateCache(options =>
            {
                options.CacheSize = 1024;
                options.CacheEntryExpiration = TimeSpan.FromMinutes(2);
            });
}
```

<span data-ttu-id="4f772-241">Domyślna implementacja buforowania zapisuje wyniki w pamięci.</span><span class="sxs-lookup"><span data-stu-id="4f772-241">The default caching implementation stores results in memory.</span></span> <span data-ttu-id="4f772-242">Możesz zapewnić własną pamięć podręczną, implementując `ICertificateValidationCache` i rejestrując ją z iniekcją zależności.</span><span class="sxs-lookup"><span data-stu-id="4f772-242">You can provide your own cache by implementing `ICertificateValidationCache` and registering it with dependency injection.</span></span> <span data-ttu-id="4f772-243">Na przykład `services.AddSingleton<ICertificateValidationCache, YourCache>()`.</span><span class="sxs-lookup"><span data-stu-id="4f772-243">For example, `services.AddSingleton<ICertificateValidationCache, YourCache>()`.</span></span>

::: moniker-end

## <a name="optional-client-certificates"></a><span data-ttu-id="4f772-244">Opcjonalne certyfikaty klienta</span><span class="sxs-lookup"><span data-stu-id="4f772-244">Optional client certificates</span></span>

<span data-ttu-id="4f772-245">Ta sekcja zawiera informacje dotyczące aplikacji, które muszą chronić podzestaw aplikacji przy użyciu certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="4f772-245">This section provides information for apps that must protect a subset of the app with a certificate.</span></span> <span data-ttu-id="4f772-246">Na przykład Razor Strona lub kontroler w aplikacji może wymagać certyfikatów klienta.</span><span class="sxs-lookup"><span data-stu-id="4f772-246">For example, a Razor Page or controller in the app might require client certificates.</span></span> <span data-ttu-id="4f772-247">Przedstawia to wyzwania jako certyfikaty klienta:</span><span class="sxs-lookup"><span data-stu-id="4f772-247">This presents challenges as client certificates:</span></span>
  
* <span data-ttu-id="4f772-248">Są funkcją TLS, a nie funkcją protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="4f772-248">Are a TLS feature, not an HTTP feature.</span></span>
* <span data-ttu-id="4f772-249">Są negocjowane dla każdego połączenia i muszą być negocjowane na początku połączenia przed udostępnieniem jakichkolwiek danych HTTP.</span><span class="sxs-lookup"><span data-stu-id="4f772-249">Are negotiated per-connection and must be be negotiated at the start of the connection before any HTTP data is available.</span></span> <span data-ttu-id="4f772-250">Na początku połączenia jest znany tylko Oznaczanie nazwy serwera (SNI) &dagger; .</span><span class="sxs-lookup"><span data-stu-id="4f772-250">At the start of the connection, only the Server Name Indication (SNI)&dagger; is known.</span></span> <span data-ttu-id="4f772-251">Certyfikaty klienta i serwera są negocjowane przed pierwszym żądaniem połączenia, a żądania zwykle nie mogą być ponownie negocjowane.</span><span class="sxs-lookup"><span data-stu-id="4f772-251">The client and server certificates are negotiated prior to the first request on a connection and requests generally aren't able to renegotiate.</span></span>

<span data-ttu-id="4f772-252">Renegocjowanie protokołu TLS było starym sposobem implementacji opcjonalnych certyfikatów klienta.</span><span class="sxs-lookup"><span data-stu-id="4f772-252">TLS renegotiation was an old way to implement optional client certificates.</span></span> <span data-ttu-id="4f772-253">Nie jest to już zalecane, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="4f772-253">This is no longer recommended because:</span></span>
- <span data-ttu-id="4f772-254">W przypadku protokołu HTTP/1.1 ponowne negocjowanie podczas żądania POST może spowodować zakleszczenie, w którym treść żądania została wypełniona w oknie protokołu TCP i nie można odebrać pakietów renegocjacji.</span><span class="sxs-lookup"><span data-stu-id="4f772-254">In HTTP/1.1, renegotiating during a POST request could cause a deadlock where the request body filled up the TCP window and the renegotiation packets can't be received.</span></span>
- <span data-ttu-id="4f772-255">Protokół HTTP/2 [jawnie zabrania](https://tools.ietf.org/html/rfc7540#section-9.2.1) ponownej negocjacji.</span><span class="sxs-lookup"><span data-stu-id="4f772-255">HTTP/2 [explicitly prohibits](https://tools.ietf.org/html/rfc7540#section-9.2.1) renegotiation.</span></span>
- <span data-ttu-id="4f772-256">Protokół TLS 1,3 został [usunięty](https://tools.ietf.org/html/rfc8740#section-1) z obsługi ponownej negocjacji.</span><span class="sxs-lookup"><span data-stu-id="4f772-256">TLS 1.3 has [removed](https://tools.ietf.org/html/rfc8740#section-1) support for renegotiation.</span></span>

<span data-ttu-id="4f772-257">ASP.NET Core 5 w wersji zapoznawczej 7 i nowszych dodaje bardziej wygodną obsługę opcjonalnych certyfikatów klienta.</span><span class="sxs-lookup"><span data-stu-id="4f772-257">ASP.NET Core 5 preview 7 and later adds more convenient support for optional client certificates.</span></span> <span data-ttu-id="4f772-258">Aby uzyskać więcej informacji, zobacz [przykład opcjonalnych certyfikatów](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample).</span><span class="sxs-lookup"><span data-stu-id="4f772-258">For more information, see the [Optional certificates sample](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample).</span></span>

<span data-ttu-id="4f772-259">Następujące podejście obsługuje opcjonalne certyfikaty klienta:</span><span class="sxs-lookup"><span data-stu-id="4f772-259">The following approach supports optional client certificates:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="4f772-260">Skonfiguruj powiązanie dla domeny i poddomeny:</span><span class="sxs-lookup"><span data-stu-id="4f772-260">Set up binding for the domain and subdomain:</span></span>
  * <span data-ttu-id="4f772-261">Na przykład Skonfiguruj powiązania w systemach `contoso.com` i `myClient.contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="4f772-261">For example, set up bindings on `contoso.com` and `myClient.contoso.com`.</span></span> <span data-ttu-id="4f772-262">`contoso.com`Host nie wymaga certyfikatu klienta, ale `myClient.contoso.com` robi ten.</span><span class="sxs-lookup"><span data-stu-id="4f772-262">The `contoso.com` host doesn't require a client certificate but `myClient.contoso.com` does.</span></span>
  * <span data-ttu-id="4f772-263">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="4f772-263">For more information, see:</span></span>
    * <span data-ttu-id="4f772-264">[Kestrel](/fundamentals/servers/kestrel):</span><span class="sxs-lookup"><span data-stu-id="4f772-264">[Kestrel](/fundamentals/servers/kestrel):</span></span>
      * [<span data-ttu-id="4f772-265">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="4f772-265">ListenOptions.UseHttps</span></span>](xref:fundamentals/servers/kestrel/endpoints#listenoptionsusehttps)
      * <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ClientCertificateMode>
      * <span data-ttu-id="4f772-266">Uwaga Kestrel obecnie nie obsługuje wielu konfiguracji protokołu TLS w jednym powiązaniu, potrzebne są dwa powiązania z unikatowymi adresami IP lub portami.</span><span class="sxs-lookup"><span data-stu-id="4f772-266">Note Kestrel does not currently support multiple TLS configurations on one binding, you'll need two bindings with unique IPs or ports.</span></span> <span data-ttu-id="4f772-267">Zobacz https://github.com/dotnet/runtime/issues/31097</span><span class="sxs-lookup"><span data-stu-id="4f772-267">See https://github.com/dotnet/runtime/issues/31097</span></span>
    * <span data-ttu-id="4f772-268">IIS</span><span class="sxs-lookup"><span data-stu-id="4f772-268">IIS</span></span>
      * [<span data-ttu-id="4f772-269">Hostowanie usług IIS</span><span class="sxs-lookup"><span data-stu-id="4f772-269">Hosting IIS</span></span>](xref:host-and-deploy/iis/index#create-the-iis-site)
      * [<span data-ttu-id="4f772-270">Konfigurowanie zabezpieczeń w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="4f772-270">Configure security on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#configure-ssl-settings-2)
    * <span data-ttu-id="4f772-271">Http.Sys: [Konfigurowanie systemu Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)</span><span class="sxs-lookup"><span data-stu-id="4f772-271">Http.Sys: [Configure Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="4f772-272">Skonfiguruj powiązanie dla domeny i poddomeny:</span><span class="sxs-lookup"><span data-stu-id="4f772-272">Set up binding for the domain and subdomain:</span></span>
  * <span data-ttu-id="4f772-273">Na przykład Skonfiguruj powiązania w systemach `contoso.com` i `myClient.contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="4f772-273">For example, set up bindings on `contoso.com` and `myClient.contoso.com`.</span></span> <span data-ttu-id="4f772-274">`contoso.com`Host nie wymaga certyfikatu klienta, ale `myClient.contoso.com` robi ten.</span><span class="sxs-lookup"><span data-stu-id="4f772-274">The `contoso.com` host doesn't require a client certificate but `myClient.contoso.com` does.</span></span>
  * <span data-ttu-id="4f772-275">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="4f772-275">For more information, see:</span></span>
    * <span data-ttu-id="4f772-276">[Kestrel](/fundamentals/servers/kestrel):</span><span class="sxs-lookup"><span data-stu-id="4f772-276">[Kestrel](/fundamentals/servers/kestrel):</span></span>
      * [<span data-ttu-id="4f772-277">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="4f772-277">ListenOptions.UseHttps</span></span>](xref:fundamentals/servers/kestrel#listenoptionsusehttps)
      * <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ClientCertificateMode>
      * <span data-ttu-id="4f772-278">Uwaga Kestrel obecnie nie obsługuje wielu konfiguracji protokołu TLS w jednym powiązaniu, potrzebne są dwa powiązania z unikatowymi adresami IP lub portami.</span><span class="sxs-lookup"><span data-stu-id="4f772-278">Note Kestrel does not currently support multiple TLS configurations on one binding, you'll need two bindings with unique IPs or ports.</span></span> <span data-ttu-id="4f772-279">Zobacz https://github.com/dotnet/runtime/issues/31097</span><span class="sxs-lookup"><span data-stu-id="4f772-279">See https://github.com/dotnet/runtime/issues/31097</span></span>
    * <span data-ttu-id="4f772-280">IIS</span><span class="sxs-lookup"><span data-stu-id="4f772-280">IIS</span></span>
      * [<span data-ttu-id="4f772-281">Hostowanie usług IIS</span><span class="sxs-lookup"><span data-stu-id="4f772-281">Hosting IIS</span></span>](xref:host-and-deploy/iis/index#create-the-iis-site)
      * [<span data-ttu-id="4f772-282">Konfigurowanie zabezpieczeń w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="4f772-282">Configure security on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#configure-ssl-settings-2)
    * <span data-ttu-id="4f772-283">Http.Sys: [Konfigurowanie systemu Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)</span><span class="sxs-lookup"><span data-stu-id="4f772-283">Http.Sys: [Configure Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)</span></span>

::: moniker-end

* <span data-ttu-id="4f772-284">W przypadku żądań do aplikacji sieci Web, które wymagają certyfikatu klienta i nie ma go:</span><span class="sxs-lookup"><span data-stu-id="4f772-284">For requests to the web app that require a client certificate and don't have one:</span></span>
  * <span data-ttu-id="4f772-285">Przekieruj na tę samą stronę przy użyciu poddomeny chronionej certyfikat klienta.</span><span class="sxs-lookup"><span data-stu-id="4f772-285">Redirect to the same page using the client certificate protected subdomain.</span></span>
  * <span data-ttu-id="4f772-286">Na przykład Przekieruj do `myClient.contoso.com/requestedPage` .</span><span class="sxs-lookup"><span data-stu-id="4f772-286">For example, redirect to `myClient.contoso.com/requestedPage`.</span></span> <span data-ttu-id="4f772-287">Ze względu na `myClient.contoso.com/requestedPage` to, że żądanie jest inną nazwą hosta `contoso.com/requestedPage` , a klient ustanowi inne połączenie i podano certyfikat klienta.</span><span class="sxs-lookup"><span data-stu-id="4f772-287">Because the request to `myClient.contoso.com/requestedPage` is a different hostname than `contoso.com/requestedPage`, the client establishes a different connection and the client certificate is provided.</span></span>
  * <span data-ttu-id="4f772-288">Aby uzyskać więcej informacji, zobacz <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="4f772-288">For more information, see <xref:security/authorization/introduction>.</span></span>

<span data-ttu-id="4f772-289">Wystaw pytania, komentarze i inne opinie dotyczące opcjonalnych certyfikatów klienta w [tym](https://github.com/dotnet/AspNetCore.Docs/issues/18720) wydaniu dyskusji w witrynie GitHub.</span><span class="sxs-lookup"><span data-stu-id="4f772-289">Leave questions, comments, and other feedback on optional client certificates in [this GitHub discussion](https://github.com/dotnet/AspNetCore.Docs/issues/18720) issue.</span></span>

<span data-ttu-id="4f772-290">&dagger; Oznaczanie nazwy serwera (SNI) to rozszerzenie TLS służące do dołączania domeny wirtualnej w ramach negocjacji protokołu SSL.</span><span class="sxs-lookup"><span data-stu-id="4f772-290">&dagger; Server Name Indication (SNI) is a TLS extension to include a virtual domain as a part of SSL negotiation.</span></span> <span data-ttu-id="4f772-291">W praktyce oznacza to, że nazwa domeny wirtualnej lub nazwy hosta mogą służyć do identyfikowania punktu końcowego sieci.</span><span class="sxs-lookup"><span data-stu-id="4f772-291">This effectively means the virtual domain name, or a hostname, can be used to identify the network end point.</span></span>
