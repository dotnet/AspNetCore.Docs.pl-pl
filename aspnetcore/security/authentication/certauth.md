---
title: Konfigurowanie uwierzytelniania certyfikatów w ASP.NET Core
author: blowdart
description: Dowiedz się, jak skonfigurować uwierzytelnianie certyfikatów w ASP.NET Core dla usług IIS i HTTP.sys.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 07/16/2020
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
uid: security/authentication/certauth
ms.openlocfilehash: 57d46e34993148943b1e9680a372405be9c80605
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424207"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a>Konfigurowanie uwierzytelniania certyfikatów w ASP.NET Core

`Microsoft.AspNetCore.Authentication.Certificate` zawiera implementację podobną do [uwierzytelniania certyfikatu](https://tools.ietf.org/html/rfc5246#section-7.4.4) dla ASP.NET Core. Uwierzytelnianie certyfikatu odbywa się na poziomie protokołu TLS, o ile nie zostanie kiedykolwiek przeASP.NET Core. Dokładniej, jest to procedura obsługi uwierzytelniania, która sprawdza poprawność certyfikatu, a następnie przekazuje zdarzenie, w którym można rozwiązać ten certyfikat do `ClaimsPrincipal` . 

[Skonfiguruj serwer](#configure-your-server-to-require-certificates) pod kątem uwierzytelniania przy użyciu certyfikatu, to usługi IIS, Kestrel, Azure Web Apps lub inne, z których korzystasz.

## <a name="proxy-and-load-balancer-scenarios"></a>Scenariusze dotyczące serwerów proxy i równoważenia obciążenia

Uwierzytelnianie certyfikatu jest scenariuszem stanowym głównie używanym w przypadku, gdy serwer proxy lub moduł równoważenia obciążenia nie obsługuje ruchu między klientami i serwerami. Jeśli jest używany serwer proxy lub moduł równoważenia obciążenia, uwierzytelnianie certyfikatu działa tylko wtedy, gdy serwer proxy lub moduł równoważenia obciążenia:

* Obsługuje uwierzytelnianie.
* Przekazuje informacje o uwierzytelnianiu użytkownika do aplikacji (na przykład w nagłówku żądania), która działa na informacje o uwierzytelnianiu.

Alternatywą dla uwierzytelniania certyfikatu w środowiskach, w których są używane serwery proxy i moduły równoważenia obciążenia, są Active Directory usług federacyjnych (AD FS) za pomocą OpenID Connect Connect (OIDC).

## <a name="get-started"></a>Rozpoczęcie pracy

Uzyskaj certyfikat HTTPS, zastosuj go i [Skonfiguruj serwer](#configure-your-server-to-require-certificates) tak, aby wymagał certyfikatów.

W aplikacji sieci Web Dodaj odwołanie do pakietu [Microsoft. AspNetCore. Authentication. Certificate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Certificate) . Następnie w `Startup.ConfigureServices` metodzie Zadzwoń `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` z własnymi opcjami, podając delegata w `OnCertificateValidated` celu wykonania dodatkowej weryfikacji dla certyfikatu klienta wysyłanego z żądaniami. Zmień te informacje na `ClaimsPrincipal` i ustaw dla `context.Principal` właściwości.

Jeśli uwierzytelnianie nie powiedzie się, ta procedura obsługi zwróci `403 (Forbidden)` odpowiedź zamiast elementu `401 (Unauthorized)` , zgodnie z oczekiwaniami. Powodem jest to, że uwierzytelnianie powinno nastąpić podczas początkowego połączenia TLS. Przez czas, gdy dociera do programu obsługi, jest zbyt opóźniony. Nie ma możliwości uaktualnienia połączenia z anonimowego połączenia z certyfikatem.

Dodaj również `app.UseAuthentication();` `Startup.Configure` metodę. W przeciwnym razie `HttpContext.User` nie zostanie ustawiona jako `ClaimsPrincipal` utworzona na podstawie certyfikatu. Na przykład:

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

W powyższym przykładzie przedstawiono domyślny sposób dodawania uwierzytelniania przy użyciu certyfikatu. Program obsługi konstruuje jednostkę główną użytkownika przy użyciu wspólnych właściwości certyfikatu.

## <a name="configure-certificate-validation"></a>Konfigurowanie weryfikacji certyfikatu

`CertificateAuthenticationOptions`Program obsługi ma pewne wbudowane walidacje, które są minimalnymi walidacjami, które należy wykonać na certyfikacie. Każdy z tych ustawień jest domyślnie włączony.

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a>AllowedCertificateTypes = łańcuchy, SelfSigned lub wszystkie (łańcuchowo | SelfSigned)

Wartość domyślna: `CertificateTypes.Chained`

Ten test sprawdza, czy dozwolony jest tylko odpowiedni typ certyfikatu. Jeśli aplikacja korzysta z certyfikatów z podpisem własnym, ta opcja musi być ustawiona na `CertificateTypes.All` lub `CertificateTypes.SelfSigned` .

### <a name="validatecertificateuse"></a>ValidateCertificateUse

Wartość domyślna: `true`

Ten test sprawdza, czy certyfikat przedstawiony przez klienta ma rozszerzone użycie klucza uwierzytelniania klienta (EKU) lub nie rozszerzeń EKU w ogóle. Zgodnie ze specyfikacją, jeśli nie określono rozszerzenia EKU, wszystkie rozszerzeń EKU są uznawane za prawidłowe.

### <a name="validatevalidityperiod"></a>ValidateValidityPeriod

Wartość domyślna: `true`

Ten test sprawdza, czy certyfikat jest w jego okresie ważności. W przypadku każdego żądania program obsługi zapewnia, że certyfikat, który był ważny, gdy był prezentowany, nie upłynął podczas bieżącej sesji.

### <a name="revocationflag"></a>RevocationFlag

Wartość domyślna: `X509RevocationFlag.ExcludeRoot`

Flaga określająca, które certyfikaty w łańcuchu są sprawdzane pod kątem odwołania.

Sprawdzanie odwołań jest wykonywane tylko wtedy, gdy certyfikat jest powiązany z certyfikatem głównym.

### <a name="revocationmode"></a>Odwołaniemode

Wartość domyślna: `X509RevocationMode.Online`

Flaga określająca sposób sprawdzania odwołania.

Określenie, że sprawdzanie online może skutkować długim opóźnieniem podczas kontaktowania się z urzędem certyfikacji.

Sprawdzanie odwołań jest wykonywane tylko wtedy, gdy certyfikat jest powiązany z certyfikatem głównym.

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a>Czy mogę skonfigurować aplikację, aby wymagać certyfikatu tylko w określonych ścieżkach?

Nie jest to możliwe. Należy pamiętać, że wymiana certyfikatów jest wykonywana na początku konwersacji HTTPS, przez serwer przed odebraniem pierwszego żądania w ramach tego połączenia, aby nie było możliwe Określanie zakresu na podstawie pól żądania.

## <a name="handler-events"></a>Zdarzenia programu obsługi

Program obsługi ma dwa zdarzenia:

* `OnAuthenticationFailed`: Wywołuje się, gdy wyjątek występuje podczas uwierzytelniania i pozwala na reagowanie.
* `OnCertificateValidated`: Wywołane po zweryfikowaniu certyfikatu, pomyślnie utworzono weryfikację i domyślny podmiot zabezpieczeń. To zdarzenie umożliwia wykonywanie własnych weryfikacji i rozszerzanie lub zastępowanie podmiotu zabezpieczeń. Przykłady obejmują:
  * Ustalanie, czy certyfikat jest znany dla usług.
  * Konstruowanie własnego podmiotu zabezpieczeń. Rozważmy następujący przykład w `Startup.ConfigureServices` :

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

Jeśli okaże się, że certyfikat ruchu przychodzącego nie spełnia dodatkowej weryfikacji, wywołaj `context.Fail("failure reason")` z przyczynę niepowodzenia.

W przypadku rzeczywistej funkcjonalności prawdopodobnie chcesz wywołać usługę zarejestrowaną w iniekcji zależności, która łączy się z bazą danych lub innym typem magazynu użytkownika. Uzyskaj dostęp do usługi przy użyciu kontekstu przesłanego do obiektu delegowanego. Rozważmy następujący przykład w `Startup.ConfigureServices` :

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

Koncepcyjnie sprawdzenie poprawności certyfikatu jest problemem z autoryzacją. Dodanie kontroli, na przykład wystawcy lub odcisk palca w zasadach autoryzacji, a nie wewnątrz `OnCertificateValidated` , jest doskonale akceptowalne.

## <a name="configure-your-server-to-require-certificates"></a>Skonfiguruj serwer tak, aby wymagał certyfikatów

### <a name="kestrel"></a>Kestrel

W *program.cs*Skonfiguruj Kestrel w następujący sposób:

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
> Punkty końcowe utworzone przez wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **przed** wywołaniem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nie mają zastosowania wartości domyślnych.

### <a name="iis"></a>IIS

Wykonaj następujące kroki w Menedżerze usług IIS:

1. Wybierz swoją witrynę z karty **połączenia** .
1. Kliknij dwukrotnie opcję **Ustawienia protokołu SSL** w oknie **Widok funkcji** .
1. Zaznacz pole wyboru **Wymagaj protokołu SSL** , a następnie wybierz przycisk **Wymagaj** przycisku radiowego w sekcji **Certyfikaty klienta** .

![Ustawienia certyfikatu klienta w usługach IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a>Azure i niestandardowe serwery proxy sieci Web

Zapoznaj się z [dokumentacją hosta i Wdróż](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) , jak skonfigurować oprogramowanie pośredniczące przesyłania certyfikatów.

### <a name="use-certificate-authentication-in-azure-web-apps"></a>Używanie uwierzytelniania certyfikatów na platformie Azure Web Apps

Na platformie Azure nie jest wymagana żadna konfiguracja przekazywania. Jest to już konfiguracja w oprogramowaniu pośredniczącym do przesyłania dalej certyfikatu.

> [!NOTE]
> Wymaga to obecności CertificateForwardingMiddleware.

### <a name="use-certificate-authentication-in-custom-web-proxies"></a>Używanie uwierzytelniania certyfikatów w niestandardowych serwerach proxy sieci Web

`AddCertificateForwarding`Metoda jest używana do określenia:

* Nazwa nagłówka klienta.
* Sposób ładowania certyfikatu (za pomocą `HeaderConverter` Właściwości).

W niestandardowych proxy sieci Web certyfikat jest przekazywane jako niestandardowy nagłówek żądania, na przykład `X-SSL-CERT` . W tym celu należy skonfigurować przekazywanie certyfikatów w programie `Startup.ConfigureServices` :

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

`Startup.Configure`Następnie Metoda dodaje oprogramowanie pośredniczące. `UseCertificateForwarding` jest wywoływana przed wywołaniem do `UseAuthentication` i `UseAuthorization` :

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

Oddzielna Klasa może służyć do implementowania logiki walidacji. Ponieważ ten sam certyfikat z podpisem własnym jest używany w tym przykładzie, należy się upewnić, że można używać tylko certyfikatu. Sprawdź, czy odciski palca zarówno certyfikatu klienta, jak i certyfikatu serwera, w przeciwnym razie można użyć dowolnego certyfikatu i będzie on wystarczający do uwierzytelnienia. Będzie on używany wewnątrz `AddCertificate` metody. W przypadku korzystania z certyfikatów pośrednich lub podrzędnych można także zweryfikować temat lub wystawcę w tym miejscu.

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

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a>Implementowanie HttpClient przy użyciu certyfikatu i HttpClientHandler

Można `HttpClientHandler` dodać bezpośrednio w konstruktorze `HttpClient` klasy. Należy zachować ostrożność podczas tworzenia wystąpień `HttpClient` . `HttpClient`Następnie wyśle certyfikat z każdym żądaniem.

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

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a>Implementowanie HttpClient przy użyciu certyfikatu i nazwanego HttpClient z usługi IHttpClientFactory 

W poniższym przykładzie certyfikat klienta jest dodawany do `HttpClientHandler` właściwości przy użyciu programu `ClientCertificates` obsługi. Tej procedury obsługi można następnie użyć w nazwanym wystąpieniu klasy `HttpClient` przy użyciu `ConfigurePrimaryHttpMessageHandler` metody. Jest to konfiguracja w programie `Startup.ConfigureServices` :

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

`IHttpClientFactory`Można następnie użyć, aby uzyskać nazwane wystąpienie z programem obsługi i certyfikatem. `CreateClient`Metoda o nazwie klienta zdefiniowanej w `Startup` klasie jest używana do uzyskiwania wystąpienia. Żądanie HTTP można wysłać przy użyciu klienta zgodnie z wymaganiami.

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

Jeśli do serwera zostanie wysłany prawidłowy certyfikat, dane są zwracane. Jeśli certyfikat lub nieprawidłowy certyfikat nie zostanie wysłany, zwracany jest kod stanu HTTP 403.

### <a name="create-certificates-in-powershell"></a>Tworzenie certyfikatów w programie PowerShell

Tworzenie certyfikatów jest najtrudniejszą częścią w konfigurowaniu tego przepływu. Certyfikat główny można utworzyć przy użyciu `New-SelfSignedCertificate` polecenia cmdlet programu PowerShell. Podczas tworzenia certyfikatu należy użyć silnego hasła. Ważne jest, aby dodać `KeyUsageProperty` parametr i parametr, `KeyUsage` jak pokazano.

#### <a name="create-root-ca"></a>Utwórz główny urząd certyfikacji

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> `-DnsName`Wartość parametru musi być zgodna z elementem docelowym wdrożenia aplikacji. Na przykład "localhost" do programowania.

#### <a name="install-in-the-trusted-root"></a>Zainstaluj w zaufanym katalogu głównym

Certyfikat główny musi być zaufany w systemie hosta. Certyfikat główny, który nie został utworzony przez urząd certyfikacji, nie będzie domyślnie zaufany. Poniższy link wyjaśnia, jak można to zrobić w systemie Windows:

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a>Certyfikat pośredni

Certyfikat pośredni można teraz utworzyć na podstawie certyfikatu głównego. Nie jest to wymagane w przypadku wszystkich przypadków użycia, ale może być konieczne utworzenie wielu certyfikatów lub konieczność aktywowania lub wyłączenia grup certyfikatów. `TextExtension`Parametr jest wymagany, aby ustawić długość ścieżki w podstawowych ograniczeniach certyfikatu.

Certyfikat pośredni można następnie dodać do zaufanego certyfikatu pośredniego w systemie hosta systemu Windows.

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a>Utwórz certyfikat podrzędny z certyfikatu pośredniego

Certyfikat podrzędny można utworzyć na podstawie certyfikatu pośredniego. Jest to jednostka końcowa i nie trzeba tworzyć więcej certyfikatów podrzędnych.

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a>Utwórz certyfikat podrzędny z certyfikatu głównego

Certyfikat podrzędny można także utworzyć bezpośrednio na podstawie certyfikatu głównego. 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a>Przykład certyfikatu pośredniego głównego — certyfikat

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

W przypadku korzystania z certyfikatów głównych, pośrednich lub podrzędnych certyfikaty można zweryfikować przy użyciu odcisku palca lub PublicKey zgodnie z wymaganiami.

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

## <a name="certificate-validation-caching"></a>Buforowanie weryfikacji certyfikatu

ASP.NET Core 5,0 i nowsze wersje obsługują możliwość włączania buforowania wyników walidacji. Buforowanie znacznie poprawia wydajność uwierzytelniania certyfikatów, ponieważ Walidacja jest kosztowną operacją.

Domyślnie uwierzytelnianie certyfikatu powoduje wyłączenie buforowania. Aby włączyć buforowanie, wywołaj `AddCertificateCache` w `Startup.ConfigureServices` :

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

Domyślna implementacja buforowania zapisuje wyniki w pamięci. Możesz zapewnić własną pamięć podręczną, implementując `ICertificateValidationCache` i rejestrując ją z iniekcją zależności. Na przykład `services.AddSingleton<ICertificateValidationCache, YourCache>()`.

::: moniker-end

## <a name="optional-client-certificates"></a>Opcjonalne certyfikaty klienta

Ta sekcja zawiera informacje dotyczące aplikacji, które muszą chronić podzestaw aplikacji przy użyciu certyfikatu. Na przykład Razor Strona lub kontroler w aplikacji może wymagać certyfikatów klienta. Przedstawia to wyzwania jako certyfikaty klienta:
  
* Są funkcją TLS, a nie funkcją protokołu HTTP.
* Są negocjowane dla każdego połączenia i muszą być negocjowane na początku połączenia przed udostępnieniem jakichkolwiek danych HTTP. Na początku połączenia jest znany tylko Oznaczanie nazwy serwera (SNI) &dagger; . Certyfikaty klienta i serwera są negocjowane przed pierwszym żądaniem połączenia, a żądania zwykle nie mogą być ponownie negocjowane.

Renegocjowanie protokołu TLS było starym sposobem implementacji opcjonalnych certyfikatów klienta. Nie jest to już zalecane, ponieważ:
- W przypadku protokołu HTTP/1.1 ponowne negocjowanie podczas żądania POST może spowodować zakleszczenie, w którym treść żądania została wypełniona w oknie protokołu TCP i nie można odebrać pakietów renegocjacji.
- Protokół HTTP/2 [jawnie zabrania](https://tools.ietf.org/html/rfc7540#section-9.2.1) ponownej negocjacji.
- Protokół TLS 1,3 został [usunięty](https://tools.ietf.org/html/rfc8740#section-1) z obsługi ponownej negocjacji.

ASP.NET Core 5 w wersji zapoznawczej 7 i nowszych dodaje bardziej wygodną obsługę opcjonalnych certyfikatów klienta. Aby uzyskać więcej informacji, zobacz [przykład opcjonalnych certyfikatów](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample).

Następujące podejście obsługuje opcjonalne certyfikaty klienta:

* Skonfiguruj powiązanie dla domeny i poddomeny:
  * Na przykład Skonfiguruj powiązania w systemach `contoso.com` i `myClient.contoso.com` . `contoso.com`Host nie wymaga certyfikatu klienta, ale `myClient.contoso.com` robi ten.
  * Aby uzyskać więcej informacji, zobacz:
    * [Kestrel](/fundamentals/servers/kestrel):
      * [ListenOptions.UseHttps](xref:fundamentals/servers/kestrel#listenoptionsusehttps)
      * <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ClientCertificateMode>
      * Uwaga Kestrel obecnie nie obsługuje wielu konfiguracji protokołu TLS w jednym powiązaniu, potrzebne są dwa powiązania z unikatowymi adresami IP lub portami. Zobacz https://github.com/dotnet/runtime/issues/31097
    * IIS
      * [Hostowanie usług IIS](xref:host-and-deploy/iis/index#create-the-iis-site)
      * [Konfigurowanie zabezpieczeń w usługach IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#configure-ssl-settings-2)
    * Http.Sys: [Konfigurowanie systemu Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)
* W przypadku żądań do aplikacji sieci Web, które wymagają certyfikatu klienta i nie ma go:
  * Przekieruj na tę samą stronę przy użyciu poddomeny chronionej certyfikat klienta.
  * Na przykład Przekieruj do `myClient.contoso.com/requestedPage` . Ze względu na `myClient.contoso.com/requestedPage` to, że żądanie jest inną nazwą hosta `contoso.com/requestedPage` , a klient ustanowi inne połączenie i podano certyfikat klienta.
  * Aby uzyskać więcej informacji, zobacz <xref:security/authorization/introduction>.

Wystaw pytania, komentarze i inne opinie dotyczące opcjonalnych certyfikatów klienta w [tym](https://github.com/dotnet/AspNetCore.Docs/issues/18720) wydaniu dyskusji w witrynie GitHub.

&dagger; Oznaczanie nazwy serwera (SNI) to rozszerzenie TLS służące do dołączania domeny wirtualnej w ramach negocjacji protokołu SSL. W praktyce oznacza to, że nazwa domeny wirtualnej lub nazwy hosta mogą służyć do identyfikowania punktu końcowego sieci.
