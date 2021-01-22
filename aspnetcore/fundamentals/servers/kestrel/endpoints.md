---
title: Skonfiguruj punkty końcowe dla ASP.NET Core serwera sieci Web Kestrel
author: rick-anderson
description: Dowiedz się więcej na temat konfigurowania punktów końcowych za pomocą Kestrel, międzyplatformowego serwera sieci Web dla ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/endpoints
ms.openlocfilehash: 5fec573013da5bcb5039b7a189fd84d964349b3a
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658745"
---
# <a name="configure-endpoints-for-the-aspnet-core-kestrel-web-server"></a>Skonfiguruj punkty końcowe dla ASP.NET Core serwera sieci Web Kestrel

Domyślnie ASP.NET Core wiąże się z:

* `http://localhost:5000`
* `https://localhost:5001` (gdy obecny jest lokalny certyfikat programistyczny)

Określ adresy URL przy użyciu:

* `ASPNETCORE_URLS` Zmienna środowiskowa.
* `--urls` argument wiersza polecenia.
* `urls` klucz konfiguracji hosta.
* `UseUrls` Metoda rozszerzenia.

Wartość podana przy użyciu tych metod może być jednym lub większą liczbą punktów końcowych HTTP i HTTPS (HTTPS, jeśli jest dostępny domyślny certyfikat). Skonfiguruj wartość jako listę rozdzieloną średnikami (na przykład `"Urls": "http://localhost:8000;http://localhost:8001"` ).

Aby uzyskać więcej informacji na temat tych metod, zobacz [adresy URL serwera](xref:fundamentals/host/web-host#server-urls) i [Zastąp konfigurację](xref:fundamentals/host/web-host#override-configuration).

Tworzony jest certyfikat programistyczny:

* Po zainstalowaniu [zestawu .NET SDK](/dotnet/core/sdk) .
* Do utworzenia certyfikatu służy [Narzędzie dev-certs](xref:aspnetcore-2.1#https) .

Niektóre przeglądarki wymagają przyznania jawnego uprawnienia do zaufania do lokalnego certyfikatu deweloperskiego.

Szablony projektu konfigurują aplikacje do uruchamiania domyślnie przy użyciu protokołu HTTPS i obejmują [przekierowania https i obsługę HSTS](xref:security/enforcing-ssl).

Wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> lub <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> metody w <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> celu skonfigurowania prefiksów i portów adresów URL dla Kestrel.

`UseUrls`, `--urls` argument wiersza polecenia, `urls` klucz konfiguracji hosta i `ASPNETCORE_URLS` zmienna środowiskowa również działają, ale mają ograniczenia wymienione w dalszej części tej sekcji (certyfikat domyślny musi być dostępny dla konfiguracji punktu końcowego HTTPS).

`KestrelServerOptions` skonfigurować

## <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults (Akcja \<ListenOptions> )

Określa konfigurację `Action` do uruchomienia dla każdego określonego punktu końcowego. Wywołanie `ConfigureEndpointDefaults` wielokrotne zastępuje przed `Action` ostatnimi `Action` parametrami.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> Punkty końcowe utworzone przez wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **przed** wywołaniem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults%2A> nie mają zastosowania wartości domyślnych.

## <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults (Akcja \<HttpsConnectionAdapterOptions> )

Określa konfigurację `Action` do uruchomienia dla każdego punktu końcowego HTTPS. Wywołanie `ConfigureHttpsDefaults` wielokrotne zastępuje przed `Action` ostatnimi `Action` parametrami.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> Punkty końcowe utworzone przez wywołanie <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **przed** wywołaniem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults%2A> nie mają zastosowania wartości domyślnych.

## <a name="configureiconfiguration"></a>Konfiguruj (IConfiguration)

Tworzy moduł ładujący konfigurację na potrzeby konfigurowania Kestrel, które pobiera <xref:Microsoft.Extensions.Configuration.IConfiguration> jako dane wejściowe. Konfiguracja musi być objęta zakresem sekcji konfiguracji dla Kestrel.

`CreateDefaultBuilder` wywołania `Configure(context.Configuration.GetSection("Kestrel"))` domyślnie do ładowania konfiguracji Kestrel.

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "Https": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    }
  }
}
```

## <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Skonfiguruj Kestrel do korzystania z protokołu HTTPS.

`ListenOptions.UseHttps` rozszerzenia

* `UseHttps`: Skonfiguruj Kestrel do używania protokołu HTTPS z domyślnym certyfikatem. Zgłasza wyjątek, jeśli nie został skonfigurowany żaden certyfikat domyślny.
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

`ListenOptions.UseHttps` wejściowe

* `filename` jest ścieżką i nazwą pliku certyfikatu względem katalogu zawierającego pliki zawartości aplikacji.
* `password` to hasło wymagane do uzyskania dostępu do danych certyfikatu X. 509.
* `configureOptions` jest `Action` do skonfigurowania `HttpsConnectionAdapterOptions` . Zwraca wartość `ListenOptions` .
* `storeName` to magazyn certyfikatów, z którego ma zostać załadowany certyfikat.
* `subject` to nazwa podmiotu certyfikatu.
* `allowInvalid` wskazuje, czy należy wziąć pod uwagę nieprawidłowe certyfikaty, takie jak certyfikaty z podpisem własnym.
* `location` jest lokalizacją magazynu, z której ma zostać załadowany certyfikat.
* `serverCertificate` jest certyfikatem X. 509.

W środowisku produkcyjnym należy jawnie skonfigurować protokół HTTPS. Należy podać co najmniej certyfikat domyślny.

Obsługiwane konfiguracje opisane dalej:

* Brak konfiguracji
* Zastąp domyślny certyfikat z konfiguracji
* Zmień wartości domyślne w kodzie

### <a name="no-configuration"></a>Brak konfiguracji

Kestrel nasłuchuje w systemie `http://localhost:5000` i `https://localhost:5001` (Jeśli domyślny certyfikat jest dostępny).

<a name="configuration"></a>

### <a name="replace-the-default-certificate-from-configuration"></a>Zastąp domyślny certyfikat z konfiguracji

Domyślny schemat konfiguracji ustawień aplikacji HTTPS jest dostępny dla Kestrel. Konfigurowanie wielu punktów końcowych, w tym adresów URL i certyfikatów do użycia, z pliku znajdującego się na dysku lub z magazynu certyfikatów.

W poniższym *appsettings.json* przykładzie:

* Ustaw `AllowInvalid` na, aby `true` zezwolić na użycie nieprawidłowych certyfikatów (na przykład certyfikatów z podpisem własnym).
* Wszystkie punkty końcowe HTTPS, które nie określają certyfikatu (w poniższym przykładzie) powracają `HttpsDefaultCert` do certyfikatu zdefiniowanego w ramach `Certificates:Default` lub w certyfikacie deweloperskim.

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertAndKeyFile": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Path": "<path to .pem/.crt file>",
          "KeyPath": "<path to .key file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5003",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5004"
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

Uwagi dotyczące schematu:

* W nazwach punktów końcowych nie jest [rozróżniana wielkość liter](xref:fundamentals/configuration/index#configuration-keys-and-values). Na przykład elementy `HTTPS` i `Https` są równoważne.
* `Url`Parametr jest wymagany dla każdego punktu końcowego. Format tego parametru jest taki sam jak parametr konfiguracji najwyższego poziomu, `Urls` z tą różnicą, że jest ograniczony do pojedynczej wartości.
* Te punkty końcowe zastępują te zdefiniowane w konfiguracji najwyższego poziomu `Urls` zamiast dodawać je do nich. Punkty końcowe zdefiniowane w kodzie za pośrednictwem `Listen` łączą się z punktami końcowymi zdefiniowanymi w sekcji konfiguracji.
* `Certificate`Sekcja jest opcjonalna. Jeśli `Certificate` sekcja nie jest określona, używane są wartości domyślne zdefiniowane w programie `Certificates:Default` . Jeśli wartości domyślne nie są dostępne, używany jest certyfikat deweloperski. Jeśli nie ma żadnych ustawień domyślnych i certyfikat programistyczny nie istnieje, serwer zgłasza wyjątek i nie może się uruchomić.
* `Certificate`Sekcja obsługuje wiele [źródeł certyfikatów](#certificate-sources).
* W [konfiguracji](xref:fundamentals/configuration/index) można zdefiniować dowolną liczbę punktów końcowych, o ile nie powodują one konfliktów portów.

#### <a name="certificate-sources"></a>Źródła certyfikatów

Węzły certyfikatów można skonfigurować do ładowania certyfikatów z różnych źródeł:

* `Path` i `Password` ładować pliki *. pfx* .
* `Path``KeyPath`oraz `Password` do ładowania plików *PEM* / *. CRT* i *. Key* .
* `Subject` i `Store` ładowanie z magazynu certyfikatów.

Certyfikat można na przykład `Certificates:Default` określić jako:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

#### <a name="configurationloader"></a>ConfigurationLoader

`options.Configure(context.Configuration.GetSection("{SECTION}"))` zwraca <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelConfigurationLoader> `.Endpoint(string name, listenOptions => { })` metodę, która może służyć do uzupełniania skonfigurowanych ustawień punktu końcowego:

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

`KestrelServerOptions.ConfigurationLoader` można uzyskać bezpośredni dostęp do kontynuowania iteracji w istniejącym module ładującym, takim jak dostarczony przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> .

* Sekcja konfiguracji dla każdego punktu końcowego jest dostępna w opcjach w `Endpoint` metodzie, aby można było odczytać ustawienia niestandardowe.
* Można załadować wiele konfiguracji, wywołując `options.Configure(context.Configuration.GetSection("{SECTION}"))` ponownie z inną sekcją. Używana jest tylko Ostatnia konfiguracja, chyba że `Load` jest jawnie wywoływana w poprzednich wystąpieniach. Pakiet nie wywołuje metody, `Load` Aby można było zastąpić sekcję konfiguracji domyślnej.
* `KestrelConfigurationLoader` odzwierciedla `Listen` rodzinę interfejsów API z programu `KestrelServerOptions` jako `Endpoint` przeciążenia, dlatego punkty końcowe kodu i konfiguracji można skonfigurować w tym samym miejscu. Te przeciążenia nie używają nazw i używają tylko ustawień domyślnych z konfiguracji.

### <a name="change-the-defaults-in-code"></a>Zmień wartości domyślne w kodzie

`ConfigureEndpointDefaults` i `ConfigureHttpsDefaults` może służyć do zmiany ustawień domyślnych dla `ListenOptions` i `HttpsConnectionAdapterOptions` , w tym przesłanianie domyślnego certyfikatu określonego w poprzednim scenariuszu. `ConfigureEndpointDefaults` i `ConfigureHttpsDefaults` powinny być wywoływane przed skonfigurowaniem punktów końcowych.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

## <a name="configure-endpoints-using-server-name-indication"></a>Konfigurowanie punktów końcowych przy użyciu Oznaczanie nazwy serwera

[Oznaczanie nazwy serwera (SNI)](https://tools.ietf.org/html/rfc6066#section-3) może służyć do hostowania wielu domen na tym samym adresie IP i porcie. Aby SNI działał, klient wysyła nazwę hosta dla bezpiecznej sesji do serwera podczas uzgadniania TLS, aby serwer mógł zapewnić prawidłowy certyfikat. Klient korzysta z dostarczonego certyfikatu w celu zaszyfrowania komunikacji z serwerem podczas bezpiecznej sesji, która następuje po uzgadnianiu protokołu TLS.

Kestrel obsługuje SNI za pośrednictwem `ServerCertificateSelector` wywołania zwrotnego. Wywołanie zwrotne jest wywoływane jednokrotnie dla każdego połączenia, aby umożliwić aplikacji inspekcję nazwy hosta i wybieranie odpowiedniego certyfikatu. Następujący kod wywołania zwrotnego może być używany w `ConfigureWebHostDefaults` wywołaniu metody pliku *program.cs* projektu:

```csharp
//using System.Security.Cryptography.X509Certificates;
//using Microsoft.AspNetCore.Server.Kestrel.Https;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(StringComparer.OrdinalIgnoreCase)
            {
                { "localhost", localhostCert },
                { "example.com", exampleCert },
                { "sub.example.com", subExampleCert },
            };            

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

Obsługa SNI wymaga:

* Uruchomiona w środowisku docelowym `netcoreapp2.1` lub nowszym. W dniu `net461` lub później wywołanie zwrotne jest wywoływane, ale `name` jest zawsze `null` . `name`Jest również, `null` Jeśli klient nie poda parametru nazwy hosta w UZGADNIANIU protokołu TLS.
* Wszystkie witryny sieci Web działają na tym samym wystąpieniu Kestrel. Kestrel nie obsługuje udostępniania adresu IP i portu w wielu wystąpieniach bez zwrotnego serwera proxy.

## <a name="connection-logging"></a>Rejestrowanie połączeń

Wywołanie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging%2A> do emisji dzienników na poziomie debugowania dla komunikacji na poziomie bajtów w ramach połączenia. Rejestrowanie połączeń ułatwia rozwiązywanie problemów z komunikacją na niskim poziomie, na przykład podczas szyfrowania TLS i za serwerami proxy. Jeśli `UseConnectionLogging` jest umieszczona przed `UseHttps` , szyfrowany ruch jest rejestrowany. Jeśli `UseConnectionLogging` jest umieszczony po `UseHttps` , odszyfrowany ruch jest rejestrowany. Jest to wbudowane [oprogramowanie pośredniczące](#connection-middleware).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

## <a name="bind-to-a-tcp-socket"></a>Powiąż z gniazdem TCP

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A>Metoda wiąże się z gniazdem TCP, a opcja lambda zezwala na konfigurację certyfikatu X. 509:

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

Przykład konfiguruje HTTPS dla punktu końcowego za pomocą <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> . Użyj tego samego interfejsu API, aby skonfigurować inne ustawienia Kestrel dla określonych punktów końcowych.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

## <a name="bind-to-a-unix-socket"></a>Powiąż z gniazdem systemu UNIX

Nasłuchiwanie w gnieździe systemu UNIX za pomocą <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> programu w celu zwiększenia wydajności za pomocą usługi Nginx, jak pokazano w tym przykładzie:

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* W pliku konfiguracji Nginx Ustaw `server`  >  `location`  >  `proxy_pass` pozycję na `http://unix:/tmp/{KESTREL SOCKET}:/;` . `{KESTREL SOCKET}` jest nazwą gniazda, na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> przykład `kestrel-test.sock` w powyższym przykładzie).
* Upewnij się, że gniazdo jest zapisywalne przez Nginx (na przykład `chmod go+w /tmp/kestrel-test.sock` ).

## <a name="port-0"></a>Port 0

Gdy numer portu `0` jest określony, Kestrel dynamicznie wiąże się z dostępnym portem. Poniższy przykład pokazuje, jak określić, który port Kestrel jest powiązany w czasie wykonywania:

[!code-csharp[](samples/5.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Po uruchomieniu aplikacji dane wyjściowe okna konsoli wskazują port dynamiczny, w którym można uzyskać dostęp do aplikacji:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

## <a name="limitations"></a>Ograniczenia

Skonfiguruj punkty końcowe przy użyciu następujących metod:

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls%2A>
* `--urls` argument wiersza polecenia
* `urls` klucz konfiguracji hosta
* `ASPNETCORE_URLS` Zmienna środowiskowa

Te metody są przydatne do tworzenia kodu w pracy z serwerami innymi niż Kestrel. Należy jednak pamiętać o następujących ograniczeniach:

* Nie można użyć protokołu HTTPS z tymi metodami, chyba że w konfiguracji punktu końcowego HTTPS jest podany certyfikat domyślny (na przykład za pomocą `KestrelServerOptions` konfiguracji lub pliku konfiguracji, jak pokazano wcześniej w tym artykule).
* Gdy oba `Listen` podejścia i `UseUrls` są używane jednocześnie, `Listen` punkty końcowe zastępują `UseUrls` punkty końcowe.

## <a name="iis-endpoint-configuration"></a>Konfiguracja punktu końcowego usług IIS

W przypadku korzystania z usług IIS powiązania URL dla powiązań przesłonięć usług IIS są ustawiane przez `Listen` lub `UseUrls` . Aby uzyskać więcej informacji, zobacz [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).

## <a name="listenoptionsprotocols"></a>ListenOptions. Protocols

`Protocols`Właściwość ustanawia protokoły HTTP ( `HttpProtocols` ) włączone w punkcie końcowym połączenia lub dla serwera. Przypisz wartość do `Protocols` właściwości z `HttpProtocols` wyliczenia.

| `HttpProtocols` wartość wyliczenia | Dozwolony protokół połączenia |
| -------------------------- | ----------------------------- |
| `Http1`                    | Tylko HTTP/1.1. Może być używany z protokołem TLS lub bez niego. |
| `Http2`                    | Tylko HTTP/2. Mogą być używane bez protokołu TLS tylko wtedy, gdy klient obsługuje [poprzedni tryb wiedzy](https://tools.ietf.org/html/rfc7540#section-3.4). |
| `Http1AndHttp2`            | HTTP/1.1 i HTTP/2. Protokół HTTP/2 wymaga od klienta wyboru protokołu HTTP/2 w uzgadnianiu [negocjowania protokołu TLS aplikacji (ClientHello alpn)](https://tools.ietf.org/html/rfc7301#section-3) . w przeciwnym razie wartość domyślna połączenia to HTTP/1.1. |

Wartość domyślna `ListenOptions.Protocols` dla każdego punktu końcowego to `HttpProtocols.Http1AndHttp2` .

Ograniczenia protokołu TLS dla protokołu HTTP/2:

* TLS w wersji 1,2 lub nowszej
* Ponowne negocjowanie wyłączone
* Kompresja wyłączona
* Minimalne rozmiary tymczasowych kluczy wymiany:
  * Krzywa eliptyczna Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : minimum 224 bitów
  * Ograniczone pole Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; : minimum 2048 bitów
* Mechanizm szyfrowania jest niedozwolony. 

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; z krzywą eliptyczna P-256 &lbrack; `FIPS186` &rbrack; jest domyślnie obsługiwana.

Poniższy przykład umożliwia nawiązywanie połączeń HTTP/1.1 i HTTP/2 na porcie 8000. Połączenia są zabezpieczone przez protokół TLS przy użyciu podanego certyfikatu:

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

W systemie Linux <xref:System.Net.Security.CipherSuitesPolicy> można używać do filtrowania uzgadniania protokołu TLS dla poszczególnych połączeń:

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

## <a name="connection-middleware"></a>Oprogramowanie pośredniczące połączenia

Niestandardowe oprogramowanie pośredniczące umożliwia filtrowanie uzgadniania protokołu TLS dla poszczególnych połączeń dla określonych szyfrów w razie potrzeby.

Poniższy przykład zgłasza <xref:System.NotSupportedException> dowolny algorytm szyfrowania, który nie jest obsługiwany przez aplikację. Alternatywnie można zdefiniować i porównać [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) z listą akceptowalnych mechanizmów szyfrowania.

Nie jest używane szyfrowanie z algorytmem szyfrowania [CipherAlgorithmType. null](xref:System.Security.Authentication.CipherAlgorithmType) .

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

Filtrowanie połączeń można również skonfigurować za pomocą wyrażenia <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

## <a name="set-the-http-protocol-from-configuration"></a>Ustawianie protokołu HTTP z konfiguracji

`CreateDefaultBuilder` wywołania `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` domyślnie do ładowania konfiguracji Kestrel.

W poniższym *appsettings.json* przykładzie jako domyślny protokół połączenia dla wszystkich punktów końcowych jest przychodzący ruch HTTP/1.1:

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

Poniższy *appsettings.json* przykład ustanawia protokół połączeń HTTP/1.1 dla określonego punktu końcowego:

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

Protokoły określone w wartościach zastąpienia kodu ustawione przez konfigurację.

## <a name="url-prefixes"></a>Prefiksy adresów URL

W przypadku użycia `UseUrls` , `--urls` argumentu wiersza polecenia, `urls` klucza konfiguracji hosta lub `ASPNETCORE_URLS` zmiennej środowiskowej prefiksy adresów URL mogą znajdować się w jednym z następujących formatów.

Tylko prefiksy adresów URL HTTP są prawidłowe. Kestrel nie obsługuje protokołu HTTPS podczas konfigurowania powiązań adresów URL przy użyciu programu `UseUrls` .

* Adres IPv4 z numerem portu

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0` jest szczególnym przypadkiem, który wiąże się ze wszystkimi adresami IPv4.

* Adres IPv6 z numerem portu

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]` jest odpowiednikiem IPv6 dla protokołu IPv4 `0.0.0.0` .

* Nazwa hosta z numerem portu

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Nazwy hostów, `*` i `+` , nie są specjalne. Wszystkie nie są rozpoznawane jako prawidłowy adres IP lub są `localhost` powiązane ze wszystkimi IP IPv4 i IPv6. Aby powiązać różne nazwy hostów z różnymi ASP.NET Core aplikacjami na tym samym porcie, użyj [HTTP.sys](xref:fundamentals/servers/httpsys) lub zwrotnego serwera proxy. Przykładami serwera zwrotnego proxy są usługi IIS, Nginx lub Apache.

  > [!WARNING]
  > Hostowanie w konfiguracji zwrotnego serwera proxy wymaga [filtrowania hosta](xref:fundamentals/servers/kestrel/host-filtering).

* `localhost`Nazwa hosta z numerem portu lub adresem IP sprzężenia zwrotnego z numerem portu

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Gdy `localhost` jest określony, Kestrel próbuje powiązać z interfejsami sprzężenia zwrotnego IPv4 i IPv6. Jeśli żądany port jest używany przez inną usługę w dowolnym interfejsie sprzężenia zwrotnego, uruchomienie Kestrel nie powiedzie się. Jeśli którykolwiek z innych przyczyn interfejsu sprzężenia zwrotnego jest niedostępny (najczęściej jest to spowodowane tym, że protokół IPv6 nie jest obsługiwany), Kestrel rejestruje ostrzeżenie.
