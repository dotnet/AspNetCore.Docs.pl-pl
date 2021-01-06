---
title: Hosting w procesie z usługami IIS i ASP.NET Core
author: rick-anderson
description: Dowiedz się więcej na temat hostingu w procesie przy użyciu usług IIS i modułu ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/in-process-hosting
ms.openlocfilehash: 47dc6f65f398ecce45c563c359dfde6e17d1dc1b
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058483"
---
# <a name="in-process-hosting-with-iis-and-aspnet-core"></a>Hosting w procesie z usługami IIS i ASP.NET Core 

Hosting w procesie uruchamia aplikację ASP.NET Core w tym samym procesie co proces roboczy usług IIS. Hosting w procesie zapewnia lepszą wydajność w porównaniu z obsługą hostingu, ponieważ żądania nie są kierowane do serwera proxy za pośrednictwem karty sprzężenia zwrotnego, czyli interfejsu sieciowego, który zwraca wychodzący ruch sieciowy z powrotem do tego samego komputera.

Na poniższym diagramie przedstawiono relację między usługami IIS, modułem ASP.NET Core i hostowaną w procesie aplikacją:

![Moduł ASP.NET Core w scenariuszu hostingu w procesie](index/_static/ancm-inprocess.png)

## <a name="enable-in-process-hosting"></a>Włącz hosting w procesie

Ponieważ ASP.NET Core 3,0, hosting w procesie został domyślnie włączony dla całej aplikacji wdrożonej w usługach IIS.

Aby jawnie skonfigurować aplikację do hostingu w procesie, ustaw wartość `<AspNetCoreHostingModel>` właściwości na `InProcess` w pliku projektu ( `.csproj` ):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

## <a name="general-architecture"></a>Architektura ogólna

Ogólny przepływ żądania jest następujący:

1. Żądanie dociera do sieci Web do sterownika HTTP.sys trybu jądra.
1. Sterownik kieruje natywne żądanie do usług IIS na skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS).
1. Moduł ASP.NET Core odbiera żądanie natywne i przekazuje go do serwera HTTP usług IIS ( `IISHttpServer` ). Serwer HTTP IIS jest implementacją serwera w procesie dla usług IIS, która konwertuje żądanie z natywnego na zarządzane.

Po przetworzeniu żądania przez serwer HTTP IIS:

1. Żądanie jest wysyłane do ASP.NET Core potoku oprogramowania pośredniczącego.
1. Potok oprogramowania pośredniczącego obsługuje żądanie i przekazuje go jako `HttpContext` wystąpienie do logiki aplikacji.
1. Odpowiedź aplikacji jest przesyłana z powrotem do usług IIS za pośrednictwem serwera HTTP IIS.
1. Program IIS wysyła odpowiedź do klienta, który zainicjował żądanie.

`CreateDefaultBuilder` dodaje <xref:Microsoft.AspNetCore.Hosting.Server.IServer> wystąpienie przez wywołanie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> metody w celu uruchomienia [CoreCLR](/dotnet/standard/glossary#coreclr) i hostowania aplikacji w procesie roboczym usług IIS ( `w3wp.exe` lub `iisexpress.exe` ). Testy wydajności wskazują, że hostowanie aplikacji platformy .NET Core w procesie zapewnia znacznie wyższą przepływność żądań w porównaniu z obsługą żądań proxy poza procesem i [Kestrel](xref:fundamentals/servers/kestrel).

Aplikacje publikowane jako pojedynczy plik wykonywalny nie mogą zostać załadowane przez model hostingu w procesie.

## <a name="application-configuration"></a>Konfiguracja aplikacji

Aby skonfigurować opcje usług IIS, należy uwzględnić <xref:Microsoft.AspNetCore.Builder.IISServerOptions> w programie konfigurację usługi <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> . Poniższy przykład wyłącza AutomaticAuthentication:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Opcja | Domyślne | Ustawienie |
| ------ | :-----: | ------- |
| `AutomaticAuthentication` | `true` | Jeśli `true` serwer usług IIS ustawi uwierzytelnienie `HttpContext.User` za pomocą [uwierzytelniania systemu Windows](xref:security/authentication/windowsauth). Jeśli `false` serwer zawiera tylko tożsamość dla `HttpContext.User` i reaguje na wyzwania, gdy zostanie jawnie zlecony przez `AuthenticationScheme` . Aby program mógł działać, należy włączyć uwierzytelnianie systemu Windows `AutomaticAuthentication` . Aby uzyskać więcej informacji, zobacz [uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName` | `null` | Ustawia nazwę wyświetlaną pokazywaną użytkownikom na stronach logowania. |
| `AllowSynchronousIO` | `false` | Czy synchroniczna operacja we/wy jest dozwolona dla `HttpContext.Request` i `HttpContext.Response` . |
| `MaxRequestBodySize` | `30000000` | Pobiera lub ustawia maksymalny rozmiar treści żądania dla `HttpRequest` . Należy zauważyć, że same usługi IIS mają limit, `maxAllowedContentLength` który zostanie przetworzony przed `MaxRequestBodySize` zestawem w `IISServerOptions` . Zmiana `MaxRequestBodySize` nie wpłynie na `maxAllowedContentLength` . Aby zwiększyć `maxAllowedContentLength` , Dodaj wpis w programie, `web.config` Aby ustawić `maxAllowedContentLength` wyższą wartość. Aby uzyskać więcej informacji, zobacz [Konfiguracja](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration). |

## <a name="differences-between-in-process-and-out-of-process-hosting"></a>Różnice między procesem hostingu w procesie a poza procesami

Następujące cechy są stosowane podczas hostingu w procesie:

* Serwer HTTP IIS ( `IISHttpServer` ) jest używany zamiast serwera [Kestrel](xref:fundamentals/servers/kestrel) . W przypadku wywołań w procesie [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> :

  * Zarejestruj `IISHttpServer` .
  * Skonfiguruj port i ścieżkę bazową, na której serwer powinien nasłuchiwać przy uruchomionym za modułem ASP.NET Core.
  * Skonfiguruj hosta do przechwytywania błędów uruchamiania.

* Ten [ `requestTimeout` atrybut](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) nie ma zastosowania do hostingu w procesie.

* Udostępnianie puli aplikacji między aplikacjami nie jest obsługiwane. Użyj jednej puli aplikacji na aplikację.

* Architektura (bitową) aplikacji i zainstalowane środowisko uruchomieniowe (x64 lub x86) musi być zgodna z architekturą puli aplikacji. Na przykład aplikacje opublikowane dla 32-bitowej (x86) muszą mieć włączone 32-bitowe dla ich pul aplikacji usług IIS. Aby uzyskać więcej informacji, zobacz sekcję [Tworzenie witryny usług IIS](xref:tutorials/publish-to-iis#create-the-iis-site) .

* Wykryto rozłączenia klientów. [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A)Token anulowania jest anulowany, gdy klient odłączy się.

* Podczas hostingu w procesie <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> nie jest wywoływana wewnętrznie w celu zainicjowania użytkownika. W związku z tym, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementacja użyta do przekształcenia oświadczeń po każdym uwierzytelnieniu nie jest domyślnie aktywowana. Podczas przekształcania oświadczeń z <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementacją, wywołaj polecenie <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> Dodaj usługi uwierzytelniania:

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
* [Wdrożenia pakietów sieci Web (jednego pliku)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) nie są obsługiwane.
