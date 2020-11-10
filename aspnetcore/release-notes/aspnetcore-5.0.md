---
title: Co nowego w ASP.NET Core 5,0
author: rick-anderson
description: Dowiedz się więcej o nowych funkcjach w ASP.NET Core 5,0.
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
- Kestrel
uid: aspnetcore-5.0
ms.openlocfilehash: 1f377f3be54ed8837d2857aed64c2d055ed9f582
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422590"
---
# <a name="whats-new-in-aspnet-core-50"></a>Co nowego w ASP.NET Core 5,0

W tym artykule przedstawiono najbardziej znaczące zmiany w ASP.NET Core 5,0 z linkami do odpowiedniej dokumentacji.

## <a name="aspnet-core-mvc-and-no-locrazor-improvements"></a>ASP.NET Core MVC i Razor ulepszenia

### <a name="model-binding-datetime-as-utc"></a>Element DateTime powiązania modelu jako czas UTC

Powiązanie modelu obsługuje teraz Powiązywanie ciągów czasu UTC z `DateTime` . Jeśli żądanie zawiera ciąg czasu UTC, powiązanie modelu wiąże go z czasem UTC `DateTime` . Na przykład następujący ciąg czasu jest związany z czasem UTC `DateTime` : `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`

### <a name="model-binding-and-validation-with-c-9-record-types"></a>Powiązanie modelu i walidacja przy użyciu typów rekordów języka C# 9

[Typy rekordów języka C# 9](/dotnet/csharp/whats-new/csharp-9#record-types) mogą być używane z powiązaniem modelu w kontrolerze MVC lub Razor stronie. Typy rekordów to dobry sposób na Modelowanie danych przesyłanych przez sieć.

Na przykład następujące polecenie `PersonController` używa `Person` typu rekordu z powiązaniem modelu i walidacją formularza:

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
          // ...
   }
}
```

`Person/Index.cshtml`Plik:

```cshtml
@model Person

Name: <input asp-for="Model.Name" />
<span asp-validation-for="Model.Name" />

Age: <input asp-for="Model.Age" />
<span asp-validation-for="Model.Age" />
```

### <a name="improvements-to-dynamicroutevaluetransformer"></a>Ulepszenia DynamicRouteValueTransformer

ASP.NET Core 3,1 wprowadzony <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> jako sposób używania niestandardowego punktu końcowego do dynamicznego wybierania akcji kontrolera MVC lub Razor strony. Aplikacje ASP.NET Core 5,0 umożliwiają przekazanie stanu do `DynamicRouteValueTransformer` i filtrowanie zestawu punktów końcowych.

### <a name="miscellaneous"></a>Różne

* Atrybut [[Compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute) można zastosować do właściwości w Razor modelu strony.
* Parametry i właściwości powiązane z treścią są uważane za wymagane domyślnie. <!-- Review: How is this different from 3.1
The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a [Required] attribute.
see https://docs.microsoft.com/aspnet/core/mvc/models/validation?view=aspnetcore-3.1   
-->

## <a name="web-api"></a>Interfejs API sieci Web

### <a name="openapi-specification-on-by-default"></a>Domyślnie Specyfikacja OpenAPI

[Specyfikacja openapi](http://spec.openapis.org/oas/v3.0.3) jest branżowym standardem do opisywania interfejsów API protokołu HTTP i integrowania ich z złożonymi procesami biznesowymi lub podmiotami trzecimi. OpenAPI jest szeroko obsługiwana przez wszystkich dostawców chmury i wiele rejestrów interfejsów API. Aplikacje, które emitują OpenAPI dokumenty z interfejsów API sieci Web, mają różne nowe możliwości, w których można używać tych interfejsów API. W obszarze partnerstwa z obsłudze projektu typu open source [Swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/)szablon interfejsu API ASP.NET Core zawiera zależność NuGet dla [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore). Swashbuckle to popularny pakiet NuGet typu "open source", który dynamicznie emituje dokumenty OpenAPI. Swashbuckle to przez introspecting kontrolerami API i Generowanie dokumentu OpenAPI w czasie wykonywania lub w czasie kompilacji przy użyciu interfejsu wiersza polecenia Swashbuckle.

W ASP.NET Core 5,0 szablony internetowego interfejsu API domyślnie włączają obsługę OpenAPI. Aby wyłączyć OpenAPI:

* Za pomocą wiersza polecenia:

    ```dotnetcli
    dotnet new webapi --no-openapi true
    ```
* W programie Visual Studio: Usuń zaznaczenie opcji **Włącz obsługę openapi**.

Wszystkie pliki *csproj* utworzone dla projektów internetowego interfejsu API zawierają odwołanie do pakietu NuGet [Swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) .

```xml
<ItemGroup>
    <PackageReference Include="Swashbuckle.AspNetCore" Version="5.5.1" />
</ItemGroup>
```

Kod wygenerowany przez szablon zawiera kod `Startup.ConfigureServices` , który aktywuje Generowanie dokumentu openapi:

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet)]

`Startup.Configure`Metoda dodaje oprogramowanie pośredniczące Swashbuckle, które umożliwia:

* Proces generowania dokumentu.
* Strona interfejsu użytkownika struktury Swagger domyślnie w trybie projektowania.

Kod wygenerowany przez szablon nie spowoduje przypadkowego uwidocznienia opisu interfejsu API podczas publikowania w środowisku produkcyjnym.

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet2)]

#### <a name="azure-api-management-import"></a>Importowanie API Management platformy Azure

Gdy ASP.NET Core projekty interfejsu API włączają OpenAPI, publikowanie programu Visual Studio 2019 w wersji 16,8 i nowszych automatycznie oferuje dodatkowy krok w przepływie publikowania. Deweloperzy korzystający z [platformy azure API Management](xref:tutorials/publish-to-azure-api-management-using-vs) mogą automatycznie zaimportować interfejsy API do usługi Azure API Management podczas przepływu publikowania:

![Azure API Management Importowanie a publikowanie](~/release-notes/static/publish-to-apim.png)

### <a name="better-launch-experience-for-web-api-projects"></a>Lepsze środowisko uruchamiania dla projektów interfejsu API sieci Web

W przypadku OpenAPI domyślnie włączone środowisko uruchamiania aplikacji (F5) dla deweloperów interfejsu API sieci Web znacznie się zwiększa. W ASP.NET Core 5,0 szablon internetowego interfejsu API zawiera wstępnie skonfigurowane do załadowania strony interfejsu użytkownika struktury Swagger. Strona interfejsu użytkownika struktury Swagger zawiera dokumentację dodaną dla opublikowanego interfejsu API i umożliwia testowanie interfejsów API za pomocą jednego kliknięcia.

![Widok Swagger/index.html](~/release-notes/static/swagger-ui-page-rc1.png)

## Blazor

### <a name="performance-improvements"></a>Ulepszenia wydajności

W przypadku platformy .NET 5 Wprowadziliśmy znaczące ulepszenia Blazor WebAssembly wydajności środowiska uruchomieniowego z określonym fokusem na potrzeby skomplikowanych renderingów interfejsu użytkownika i serializacji JSON. W naszych testach wydajności Blazor WebAssembly w przypadku większości scenariuszy środowisko .NET 5 jest szybsze od dwóch do trzech razy. Aby uzyskać więcej informacji, zobacz [Blog ASP.NET: aktualizacje ASP.NET Core w programie .NET 5 Release Candidate 1](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements).

### <a name="css-isolation"></a>Izolacja CSS

Blazor Program obsługuje teraz Definiowanie stylów CSS, które są objęte zakresem danego składnika. Style CSS specyficzne dla składnika ułatwiają powodowanie stylów w aplikacji i unikanie niezamierzonych efektów ubocznych stylów globalnych. Aby uzyskać więcej informacji, zobacz <xref:blazor/components/css-isolation>.

### <a name="new-inputfile-component"></a>Nowy `InputFile` składnik

`InputFile`Składnik umożliwia odczytywanie co najmniej jednego pliku wybranego przez użytkownika do przekazania. Aby uzyskać więcej informacji, zobacz <xref:blazor/file-uploads>.

### <a name="new-inputradio-and-inputradiogroup-components"></a>Nowe `InputRadio` i `InputRadioGroup` składniki

Blazor Program zawiera wbudowane `InputRadio` i `InputRadioGroup` składniki upraszczające Powiązywanie danych z grupami przycisków radiowych ze zintegrowanym sprawdzaniem poprawności. Aby uzyskać więcej informacji, zobacz <xref:blazor/forms-validation>.

### <a name="component-virtualization"></a>Wirtualizacja składników

Popraw postrzeganą wydajność renderowania składników przy użyciu Blazor wbudowanej obsługi wirtualizacji platformy. Aby uzyskać więcej informacji, zobacz <xref:blazor/forms-validation#radio-buttons>.

### <a name="ontoggle-event-support"></a>`ontoggle` Obsługa zdarzeń

Blazor zdarzenia obsługują teraz `ontoggle` zdarzenie dom. Aby uzyskać więcej informacji, zobacz <xref:blazor/components/event-handling#event-argument-types>.

### <a name="set-ui-focus-in-no-locblazor-apps"></a>Ustawianie fokusu interfejsu użytkownika w Blazor aplikacjach

Użyj `FocusAsync` metody wygodnej dla odwołań do elementów, aby ustawić fokus interfejsu użytkownika dla tego elementu. Aby uzyskać więcej informacji, zobacz <xref:blazor/components/event-handling#focus-an-element>.

### <a name="custom-validation-class-attributes"></a>Niestandardowe atrybuty klasy walidacji

Niestandardowe nazwy klas walidacji są przydatne podczas integrowania z strukturami CSS, takimi jak Bootstrap. Aby uzyskać więcej informacji, zobacz <xref:blazor/forms-validation#custom-validation-class-attributes>.

### <a name="iasyncdisposable-support"></a>Obsługa IAsyncDisposable

Blazor składniki obsługują teraz <xref:System.IAsyncDisposable> interfejs do asynchronicznej wersji przyznanych zasobów.

### <a name="javascript-isolation-and-object-references"></a>Izolacja kodu JavaScript i odwołania do obiektów

Blazor Włącza izolację JavaScript w standardowych [modułach języka JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules). Aby uzyskać więcej informacji, zobacz <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.

### <a name="form-components-support-display-name"></a>Obsługa nazw wyświetlanych przez składniki formularza

Następujące wbudowane składniki obsługują nazwy wyświetlane z `DisplayName` parametrem:

* `InputDate`
* `InputNumber`
* `InputSelect`

Aby uzyskać więcej informacji, zobacz <xref:blazor/forms-validation#display-name-support>.

### <a name="catch-all-route-parameters"></a>Catch-wszystkie parametry tras

Wszystkie parametry trasy, które przechwytują ścieżki między wieloma granicami folderów, są obsługiwane w składnikach. Aby uzyskać więcej informacji, zobacz <xref:blazor/fundamentals/routing#catch-all-route-parameters>.

### <a name="debugging-improvements"></a>Ulepszenia debugowania

Blazor WebAssemblyUlepszono debugowanie aplikacji w ASP.NET Core 5,0. Ponadto debugowanie jest teraz obsługiwane w Visual Studio dla komputerów Mac. Aby uzyskać więcej informacji, zobacz <xref:blazor/debug>.

### <a name="microsoft-no-locidentity-v20-and-msal-v20"></a>Microsoft Identity v 2.0 i MSAL v 2.0

Blazor zabezpieczenia wykorzystują teraz oprogramowanie Microsoft Identity v 2.0 ( [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web) i [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) ) oraz MSAL v 2.0. Aby uzyskać więcej informacji, zobacz tematy w [ Blazor zabezpieczeniach i Identity węźle](xref:blazor/security/index).

### <a name="protected-browser-storage-for-no-locblazor-server"></a>Magazyn chronionej przeglądarki dla programu Blazor Server

Blazor Server aplikacje mogą teraz korzystać z wbudowanej obsługi przechowywania stanu aplikacji w przeglądarce chronionej przed naruszeniem przy użyciu ASP.NET Core ochrony danych. Dane mogą być przechowywane w lokalnym magazynie przeglądarki lub w magazynie sesji. Aby uzyskać więcej informacji, zobacz <xref:blazor/state-management>.

### <a name="no-locblazor-webassembly-prerendering"></a>Blazor WebAssembly Renderowanie prerenderingu

Ulepszono integrację składników między modelami hostingu, a Blazor WebAssembly aplikacje mogą teraz wygenerowania danych wyjściowych na serwerze. <!-- UNCOMMENT AFTER https://github.com/dotnet/AspNetCore.Docs/pull/19887 MERGES: For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps> and <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>. -->

### <a name="trimminglinking-improvements"></a>Ulepszenia przycinania/łączenia

Blazor WebAssembly wykonuje przycinanie/łączenie języka pośredniego (IL) podczas kompilacji, aby przyciąć niepotrzebny kod IL z zestawów wyjściowych aplikacji. W wersji ASP.NET Core 5,0 program Blazor WebAssembly wykonuje ulepszone przycinanie z użyciem dodatkowych opcji konfiguracji. Aby uzyskać więcej informacji, zobacz <xref:blazor/host-and-deploy/configure-trimmer> i [Opcje przycinania](/dotnet/core/deploying/trimming-options).

### <a name="browser-compatibility-analyzer"></a>Analizator zgodności przeglądarki

Blazor WebAssembly aplikacje są przeznaczone dla całego obszaru powierzchni interfejsu API platformy .NET, ale nie wszystkie interfejsy API platformy .NET są obsługiwane w zestawie webassembly z powodu ograniczeń piaskownicy przeglądarki. Nieobsługiwane interfejsy API są zgłaszane <xref:System.PlatformNotSupportedException> podczas uruchamiania w zestawie webassembly. Analizator zgodności platformy ostrzega dewelopera, gdy aplikacja korzysta z interfejsów API, które nie są obsługiwane przez Platformy docelowe aplikacji. Aby uzyskać więcej informacji, zobacz <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>.

### <a name="lazy-load-assemblies"></a>Zestawy ładowania z opóźnieniem

Blazor WebAssembly wydajność uruchamiania aplikacji można ulepszyć przez odroczenie ładowania niektórych zestawów aplikacji, dopóki nie są wymagane. Aby uzyskać więcej informacji, zobacz <xref:blazor/webassembly-lazy-load-assemblies>.

### <a name="updated-globalization-support"></a>Zaktualizowano obsługę globalizacji

Obsługa globalizacji jest dostępna w Blazor WebAssembly oparciu o składniki międzynarodowe dla standardu Unicode (ICU). Aby uzyskać więcej informacji, zobacz <xref:blazor/globalization-localization>.

## <a name="grpc"></a>gRPC

W programie [gRPC](https://grpc.io/)wprowadzono wiele ulepszeń preformowania. Aby uzyskać więcej informacji, zobacz [udoskonalenia wydajności gRPC w programie .NET 5](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/).

Więcej informacji gRPC można znaleźć w temacie <xref:grpc/index> .

## SignalR

### <a name="no-locsignalr-hub-filters"></a>SignalR Filtry centrum

SignalR Filtry centrów o nazwie potoki centrów w ASP.NET SignalR , to funkcja, która umożliwia uruchamianie kodu przed wywołaniem i po wywołaniu metod centrów. Uruchamianie kodu przed i po wywołaniu metody Hub jest podobne do sposobu, w jaki oprogramowanie pośredniczące ma możliwość uruchomienia kodu przed i po żądaniu HTTP. Typowe zastosowania obejmują rejestrowanie, obsługę błędów i walidację argumentów.

Aby uzyskać więcej informacji, zobacz [Używanie filtrów centrum w SignalR ASP.NET Core ](xref:signalr/hub-filters).

### <a name="no-locsignalr-parallel-hub-invocations"></a>SignalR równoległe wywołania koncentratora

ASP.NET Core SignalR może teraz obsługiwać równoległe wywołania koncentratora. Zachowanie domyślne można zmienić, aby umożliwić klientom wywoływanie więcej niż jednej metody centrum w danej chwili:

[!code-csharp[](~/release-notes/sample/StartupSignalRhubs.cs?name=snippet)]

### <a name="added-messagepack-support-in-no-locsignalr-java-client"></a>Dodano obsługę MessagePack w SignalR kliencie Java

Nowy pakiet, [com. Microsoft. signaler. MessagePack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack), dodaje obsługę MessagePack do SignalR klienta Java. Aby użyć protokołu MessagePack Hub, Dodaj `.withHubProtocol(new MessagePackHubProtocol())` do konstruktora połączeń:

```java
HubConnection hubConnection = HubConnectionBuilder.create(
                           "http://localhost:53353/MyHub")
               .withHubProtocol(new MessagePackHubProtocol())
               .build();
```

<!--
See [Update SignalR code](xref:migration/31-to-50#signalr) for migration instructions.
-->

## Kestrel

* Punkty końcowe do ponownego ładowania za pośrednictwem konfiguracji: Kestrel mogą wykryć zmiany w konfiguracji przekazane do [ KestrelServerOptions.Configuruj](xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Configure%2A) i usunąć powiązania z istniejących punktów końcowych i powiązać je z nowymi punktami końcowymi bez konieczności ponownego uruchamiania aplikacji, gdy `reloadOnChange` parametr ma wartość `true` . Domyślnie w przypadku korzystania z programu <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> lub <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> program Kestrel tworzy powiązanie z podsekcją konfiguracji [" Kestrel "](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) z `reloadOnChange` włączonym. Aplikacje muszą `reloadOnChange: true` być przekazywane podczas `KestrelServerOptions.Configure` ręcznego wywoływania, aby uzyskać punkty końcowe do ponownego ładowania.
* Ulepszenia nagłówków odpowiedzi HTTP/2. Aby uzyskać więcej informacji, zobacz [ulepszenia wydajności](#performance-improvements) w następnej sekcji.
* Obsługa dodatkowych typów punktów końcowych w usłudze Sockets transport: Dodawanie do nowego interfejsu API wprowadzonego w systemie, domyślnie przydziały usługi <xref:System.Net.Sockets?displayProperty=nameWithType> Sockets w [Kestrel](xref:fundamentals/servers/kestrel) systemie umożliwiają powiązanie zarówno z istniejącymi dojściami plików, jak i gniazdami domen systemu UNIX Obsługa tworzenia powiązań z istniejącymi dojściami plików umożliwia korzystanie z istniejącej `Systemd` integracji bez konieczności `libuv` transportu.
* Dekodowanie nagłówków niestandardowych w programie Kestrel : aplikacje mogą określić, które elementy <xref:System.Text.Encoding> mają być używane do interpretacji nagłówków przychodzących na podstawie nazwy nagłówka zamiast domyślnego kodowania UTF-8. Ustaw <!--<xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector> --> `Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector` właściwość określająca kodowanie, które ma być używane:
 
  ```csharp
  public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.RequestHeaderEncodingSelector = encoding =>
                {
                      return encoding switch
                      {
                          "Host" => System.Text.Encoding.Latin1,
                          _ => System.Text.Encoding.UTF8,
                      };
                };
            });
            webBuilder.UseStartup<Startup>();
        });
  ```

### <a name="no-lockestrel-endpoint-specific-options-via-configuration"></a>Kestrel Opcje specyficzne dla punktu końcowego za pośrednictwem konfiguracji

Dodano pomoc techniczną do konfigurowania Kestrel opcji specyficznych dla punktu końcowego za pośrednictwem [konfiguracji](xref:fundamentals/configuration/index). Konfiguracje specyficzne dla punktu końcowego obejmują:

* Używane protokoły HTTP
* Używane protokoły TLS
* Wybrano certyfikat
* Tryb certyfikatu cient

Konfiguracja pozwala określić, który certyfikat jest wybierany w oparciu o określoną nazwę serwera. Nazwa serwera jest częścią rozszerzenia Oznaczanie nazwy serwera (SNI) do protokołu TLS wskazanego przez klienta. KestrelKonfiguracja programu obsługuje również prefiks wieloznaczny w nazwie hosta.

Poniższy przykład pokazuje, jak określić konkretny punkt końcowy przy użyciu pliku konfiguracji:

```json
{
  "Kestrel": {
    "Endpoints": {
      "EndpointName": {
        "Url": "https://*",
        "Sni": {
          "a.example.org": {
            "Protocols": "Http1AndHttp2",
            "SslProtocols": [ "Tls11", "Tls12"],
            "Certificate": {
              "Path": "testCert.pfx",
              "Password": "testPassword"
            },
            "ClientCertificateMode" : "NoCertificate"
          },
          "*.example.org": {
            "Certificate": {
              "Path": "testCert2.pfx",
              "Password": "testPassword"
            }
          },
          "*": {
            // At least one sub-property needs to exist per
            // SNI section or it cannot be discovered via
            // IConfiguration
            "Protocols": "Http1",
          }
        }
      }
    }
  }
}
```

Oznaczanie nazwy serwera (SNI) to rozszerzenie TLS służące do dołączania domeny wirtualnej w ramach negocjacji protokołu SSL. W praktyce oznacza to, że nazwa domeny wirtualnej lub nazwa hosta może służyć do identyfikowania punktu końcowego sieci.

## <a name="performance-improvements"></a>Ulepszenia wydajności

### <a name="http2"></a>HTTP/2

* Znaczące redukcje alokacji w ścieżce kodu HTTP/2.
* Obsługa [HPack dynamicznej kompresji](https://tools.ietf.org/html/rfc7541) nagłówków odpowiedzi HTTP/2 w programie [Kestrel](xref:fundamentals/servers/kestrel) . Aby uzyskać więcej informacji, zobacz [rozmiar tabeli nagłówka](xref:fundamentals/servers/kestrel#header-table-size) i [HPACK: dyskretne Killer (funkcja) protokołu HTTP/2](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/).
* Wysyłanie ramek PING protokołu HTTP/2: protokół HTTP/2 ma mechanizm wysyłania ramek PING, aby upewnić się, że połączenie bezczynne jest nadal funkcjonalne. Zapewnienie działającego połączenia jest szczególnie przydatne podczas pracy z długimi strumieniami, które często są bezczynne, ale tylko sporadycznie widzą aktywność, na przykład gRPC strumienie. Aplikacje mogą wysyłać okresowe ramki PING w programie [Kestrel](xref:fundamentals/servers/kestrel) , ustawiając limity dla <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions> :

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.Limits.Http2.KeepAlivePingInterval =
                                               TimeSpan.FromSeconds(10);
                options.Limits.Http2.KeepAlivePingTimeout =
                                               TimeSpan.FromSeconds(1);
            });
            webBuilder.UseStartup<Startup>();
        });
   ```
   <!-- review: KeepAlivePingInterval not found in RC1. Try testing with RC1. See https://github.com/dotnet/aspnetcore/pull/22565/files see C:/Users/riande/source/repos/WebApplication128/WebApplication128 -->

### <a name="containers"></a>Kontenery

Przed rozpoczęciem programu .NET 5,0, kompilowanie i publikowanie *pliku dockerfile* dla aplikacji ASP.NET Core wymaga pobrania całego zestaw .NET Core SDK i ASP.NET Core obrazu. W tej wersji jest zmniejszana liczba bajtów obrazów zestawu SDK, a bajty pobierane dla ASP.NET Core obrazu są w znacznym stopniu eliminowane. Aby uzyskać więcej informacji, zobacz [ten komentarz dotyczący problemu](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750)w serwisie GitHub.

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja

### <a name="azure-active-directory-authentication-with-microsoftno-locidentityweb"></a>Azure Active Directory uwierzytelnianie z firmą Microsoft. Identity . Witrynę

Szablony projektów ASP.NET Core teraz integrują się z usługą <xref:Microsoft.Identity.Web?displayProperty=fullName> w celu obsługi uwierzytelniania przy użyciu [katalogu aktywności platformy Azure](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD). [Microsoft. Identity .. Pakiet internetowy](https://www.nuget.org/packages/Microsoft.Identity.Web/) oferuje następujące informacje:

* Lepsze środowisko uwierzytelniania za pomocą usługi Azure AD.
* Łatwiejszy sposób uzyskiwania dostępu do zasobów platformy Azure w imieniu użytkowników, w tym [Microsoft Graph](/graph/overview). Zobacz [Microsoft. Identity .. Przykład sieci Web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2), który rozpoczyna się od podstawowej nazwy logowania i przechodzi przez wiele dzierżawców przy użyciu interfejsów API platformy Azure, przy użyciu Microsoft Graph i ochrony własnych interfejsów API. `Microsoft.Identity.Web` jest dostępny razem z platformą .NET 5.

### <a name="allow-anonymous-access-to-an-endpoint"></a>Zezwalanie na dostęp anonimowy do punktu końcowego

`AllowAnonymous`Metoda rozszerzenia umożliwia anonimowy dostęp do punktu końcowego:

[!code-csharp[](~/release-notes/sample/StartupAnonEndpoint.cs?name=snippet)]

### <a name="custom-handling-of-authorization-failures"></a>Niestandardowa obsługa niepowodzeń autoryzacji

Niestandardowa obsługa niepowodzeń autoryzacji jest teraz łatwiejsza przy użyciu nowego interfejsu [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs) , który jest wywoływany przez [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) [autoryzacji](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) . Domyślna implementacja pozostaje taka sama, ale obsługa niestandardowa może być zarejestrowana w [iniekcja zależności, która umożliwia niestandardowe odpowiedzi HTTP na podstawie przyczyny niepowodzenia autoryzacji. Zapoznaj się z [tym przykładem](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs) , który pokazuje użycie `IAuthorizationMiddlewareResultHandler` .

### <a name="authorization-when-using-endpoint-routing"></a>Autoryzacja w przypadku korzystania z routingu punktu końcowego

Autoryzacja w przypadku korzystania z usługi Routing punktów końcowych otrzymuje teraz `HttpContext` zamiast wystąpienia punktu końcowego. Dzięki temu oprogramowanie pośredniczące autoryzacji uzyskuje dostęp do `RouteData` i innych właściwości `HttpContext` , które były niedostępne, chociaż Klasa jest niedostępna <xref:Microsoft.AspNetCore.Http.Endpoint> . Punkt końcowy można pobrać z kontekstu przy użyciu [kontekstu. GetEndPoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A).

### <a name="role-based-access-control-with-kerberos-authentication-and-ldap-on-linux"></a>Kontrola dostępu oparta na rolach z uwierzytelnianiem Kerberos i LDAP w systemie Linux

Zobacz [uwierzytelnianie Kerberos i kontrola dostępu oparta na rolach (RBAC)](xref:security/authentication/windowsauth#rbac)

## <a name="api-improvements"></a>Udoskonalenia interfejsu API

### <a name="json-extension-methods-for-httprequest-and-httpresponse"></a>Metody rozszerzenia JSON dla elementu HttpRequest i HttpResponse

Dane JSON mogą być odczytywane i zapisywane przy `HttpRequest` `HttpResponse` użyciu <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> metod New i `WriteAsJsonAsync` Extension. Te metody rozszerzające używają [System.Text.Jsna](xref:System.Text.Json) serializatorze do obsługi danych JSON. Nowa `HasJsonContentType` Metoda rozszerzenia może również sprawdzić, czy żądanie ma typ zawartości JSON.

Metody rozszerzenia JSON można łączyć z [routingiem punktów końcowych](xref:fundamentals/routing) w celu utworzenia interfejsów API JSON w stylu programowania wywoływanych * **Route do Code** _. Jest to nowa opcja dla deweloperów, którzy chcą w prosty sposób tworzyć podstawowe interfejsy API JSON. Na przykład aplikacja sieci Web, która ma tylko kilku punktów końcowych, może wybrać użycie trasy do kodu, a nie pełnej funkcjonalności ASP.NET Core MVC:

```csharp
endpoints.MapGet("/weather/{city:alpha}", async context =>
{
    var city = (string)context.Request.RouteValues["city"];
    var weather = GetFromDatabase(city);

    await context.Response.WriteAsJsonAsync(weather);
});
```

Aby uzyskać więcej informacji na temat nowych metod rozszerzenia JSON i trasy do kodu, zobacz [ten program .NET show](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code).

### <a name="systemdiagnosticsactivity"></a>System. Diagnostics. Activity

Domyślny format dla <xref:System.Diagnostics.Activity?displayProperty=fullName> teraz domyślnie jest FORMATEM W3C. Zapewnia to obsługę śledzenia rozproszonego w ASP.NET Core współdziałanie z większą liczbą platform.

### <a name="frombodyattribute"></a>FromBodyAttribute

<xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> Co więcej obsługuje Konfigurowanie opcji, która umożliwia określenie parametrów lub właściwości jako opcjonalnych:

```csharp
public IActionResult Post([FromBody(EmptyBodyBehavior = EmptyBodyBehavior.Allow)]
                           MyModel model) {
     ...
     }
```

## <a name="miscellaneous-improvements"></a>Różne ulepszenia

Rozpoczęto stosowanie [adnotacji dopuszczającej wartości null](/dotnet/csharp/nullable-references#attributes-describe-apis) do zestawów ASP.NET Core. Firma Microsoft planuje Dodawanie adnotacji do najbardziej typowej publicznej powierzchni interfejsu API platformy .NET 5. <!-- Review: what's the impact of this? How does it work? Need more info.  Check the link I added -->

### <a name="control-startup-class-activation"></a>Kontrola aktywacji klasy uruchamiania

Dodano dodatkowe <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A> Przeciążenie, które umożliwia aplikacji dostarczenie metody fabryki do kontrolowania `Startup` aktywacji klasy. Kontrolowanie `Startup` aktywacji klasy jest przydatne do przekazywania dodatkowych parametrów do `Startup` , które są inicjowane wraz z hostem:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var logger = CreateLogger();
        var host = Host.CreateDefaultBuilder()
            .ConfigureWebHost(builder =>
            {
                builder.UseStartup(context => new Startup(logger));
            })
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="auto-refresh-with-dotnet-watch"></a>Autoodświeżanie za pomocą czujki dotnet

W programie .NET 5 uruchomienie [czujka dotnet](xref:tutorials/dotnet-watch) w projekcie ASP.NET Core powoduje uruchomienie domyślnej przeglądarki i autoodowieżanie przeglądarki w miarę wprowadzania zmian w kodzie. Oznacza to, że możesz:

_ Otwórz projekt ASP.NET Core w edytorze tekstu.
* Uruchom polecenie `dotnet watch`.
* Należy skoncentrować się na zmianach kodu, gdy narzędzia obsługują ponowne kompilowanie, ponowny Start i ładowanie aplikacji.

Mamy nadzieję, że w przyszłości przeniesiesz funkcję autoodświeżania do programu Visual Studio.

### <a name="console-logger-formatter"></a>Program formatujący rejestratora konsoli

Wprowadzono ulepszenia dostawcy dziennika konsoli w `Microsoft.Extensions.Logging` bibliotece programu. Deweloperzy mogą teraz zaimplementować niestandardowe, `ConsoleFormatter` Aby uzyskać pełną kontrolę nad formatowaniem i kolorem danych wyjściowych konsoli. Interfejsy API programu formatującego umożliwiają Zaawansowane formatowanie przez implementację podzestawu sekwencji unikowych VT-100. Emulator VT-100 jest obsługiwany przez większość nowoczesnych terminali. Rejestrator konsoli może analizować sekwencje ucieczki dla nieobsługiwanych terminali, umożliwiając deweloperom tworzenie pojedynczego programu formatującego dla wszystkich terminali.

### <a name="json-console-logger"></a>Rejestrator konsoli JSON

Oprócz obsługi niestandardowych elementów formatujących dodano również wbudowany program formatujący JSON, który emituje uporządkowane dzienniki JSON do konsoli programu. Poniższy kod przedstawia sposób przełączania z domyślnego rejestratora do formatu JSON:

[!code-csharp[](~/release-notes/sample/ProgramJsonLog.cs?name=snippet)]

Komunikaty dziennika emitowane do konsoli są sformatowane w formacie JSON:

```json
{
  "EventId": 0,
  "LogLevel": "Information",
  "Category": "Microsoft.Hosting.Lifetime",
  "Message": "Now listening on: https://localhost:5001",
  "State": {
    "Message": "Now listening on: https://localhost:5001",
    "address": "https://localhost:5001",
    "{OriginalFormat}": "Now listening on: {address}"
  }
}
```
