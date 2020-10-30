---
title: Hosting poza procesem przy użyciu usług IIS i ASP.NET Core
author: rick-anderson
description: Dowiedz się więcej na temat hostingu poza procesem przy użyciu usług IIS i modułu ASP.NET Core.
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
uid: host-and-deploy/iis/out-of-process-hosting
ms.openlocfilehash: 78ead27bd1373237d1c0a48655d73a41a0a72279
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060420"
---
# <a name="out-of-process-hosting-with-iis-and-aspnet-core"></a>Hosting poza procesem przy użyciu usług IIS i ASP.NET Core 

Ponieważ ASP.NET Core aplikacje działają w procesie innym niż proces roboczy usług IIS, moduł ASP.NET Core obsługuje zarządzanie procesami. Moduł uruchamia proces dla aplikacji ASP.NET Core, gdy pierwsze żądanie zostanie odebrane i ponownie uruchomiony, jeśli zostanie zamknięty lub ulegnie awarii. Jest to zasadniczo takie samo zachowanie jak w przypadku aplikacji uruchamianych w procesie, które są zarządzane przez [usługę aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Na poniższym diagramie przedstawiono relację między usługami IIS, modułem ASP.NET Core i hostowanym przez aplikację aplikacją:

![Moduł ASP.NET Core w scenariuszu hostingu poza procesem](index/_static/ancm-outofprocess.png)

1. Żądania docierają z sieci Web do sterownika HTTP.sys trybu jądra.
1. Sterownik kieruje żądania do usług IIS na skonfigurowanym porcie witryny sieci Web. Skonfigurowany port jest zwykle 80 (HTTP) lub 443 (HTTPS).
1. Moduł przekazuje żądania do Kestrel na losowo wybranym porcie dla aplikacji. Port losowy nie jest 80 lub 443.

<!-- make this a bullet list -->
Moduł ASP.NET Core określa port za pośrednictwem zmiennej środowiskowej podczas uruchamiania. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A>Rozszerzenie konfiguruje serwer do nasłuchiwania `http://localhost:{PORT}` . Dodatkowe sprawdzenia są wykonywane, a żądania, które nie pochodzą z modułu, są odrzucane. Moduł nie obsługuje przekazywania HTTPS. Żądania są przekazywane przez protokół HTTP nawet wtedy, gdy są odbierane przez usługi IIS przez protokół HTTPS.

Po poKestrel przez program żądania z modułu żądanie zostanie przekazane do ASP.NET Core potoku programu pośredniczącego. Potok oprogramowania pośredniczącego obsługuje żądanie i przekazuje go jako `HttpContext` wystąpienie do logiki aplikacji. Oprogramowanie pośredniczące dodane przez integrację usług IIS aktualizuje schemat, zdalny adres IP i pathbase, aby można było przesłać żądanie do Kestrel. Odpowiedź aplikacji jest przesyłana z powrotem do usług IIS, która przekazuje ją z powrotem do klienta HTTP, który zainicjował żądanie.

Aby uzyskać wskazówki dotyczące konfiguracji modułu ASP.NET Core, zobacz <xref:host-and-deploy/aspnet-core-module> .

Aby uzyskać więcej informacji na temat hostingu, zobacz [host in ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Konfiguracja aplikacji

### <a name="enable-the-iisintegration-components"></a>Włącz składniki IISIntegration

Podczas kompilowania hosta w `CreateHostBuilder` ( `Program.cs` ), wywołaj, <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> Aby włączyć integrację usług IIS:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

Aby uzyskać więcej informacji na temat `CreateDefaultBuilder` , zobacz <xref:fundamentals/host/generic-host#default-builder-settings> .


**Model hostingu poza procesem**

Aby skonfigurować opcje usług IIS, należy uwzględnić <xref:Microsoft.AspNetCore.Builder.IISOptions> w programie konfigurację usługi <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> . Poniższy przykład uniemożliwia wypełnianie aplikacji `HttpContext.Connection.ClientCertificate` :

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Opcja                         | Domyślne | Ustawienie |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Jeśli `true` [oprogramowanie pośredniczące integracji usług IIS](#enable-the-iisintegration-components) ustawi uwierzytelnienie `HttpContext.User` za pomocą [uwierzytelniania systemu Windows](xref:security/authentication/windowsauth). Jeśli `false` oprogramowanie pośredniczące zapewnia tylko tożsamość `HttpContext.User` i reaguje na wyzwania, gdy zostanie jawnie zażądana przez program `AuthenticationScheme` . Aby program mógł działać, należy włączyć uwierzytelnianie systemu Windows `AutomaticAuthentication` . Aby uzyskać więcej informacji, zobacz temat [uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth) . |
| `AuthenticationDisplayName`    | `null`  | Ustawia nazwę wyświetlaną pokazywaną użytkownikom na stronach logowania. |
| `ForwardClientCertificate`     | `true`  | Jeśli `true` `MS-ASPNETCORE-CLIENTCERT` jest obecny nagłówek żądania, `HttpContext.Connection.ClientCertificate` zostanie wypełniony. |


### <a name="proxy-server-and-load-balancer-scenarios"></a>Scenariusze serwera proxy i modułu równoważenia obciążenia

[Oprogramowanie pośredniczące integracji usług IIS](#enable-the-iisintegration-components) i moduł ASP.NET Core są skonfigurowane do przesyłania dalej:

* Schemat (HTTP/HTTPS).
* Zdalny adres IP, z którego pochodzi żądanie.

[Oprogramowanie pośredniczące integracji usług IIS](#enable-the-iisintegration-components) konfiguruje przekazane nagłówki pośredniczące.

W przypadku aplikacji hostowanych za dodatkowymi serwerami proxy i modułami równoważenia obciążenia może być wymagana dodatkowa konfiguracja. Aby uzyskać więcej informacji, zobacz [konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).


### <a name="out-of-process-hosting-model"></a>Model hostingu poza procesem

Aby skonfigurować aplikację do hostingu poza procesem, ustaw wartość `<AspNetCoreHostingModel>` właściwości na `OutOfProcess` w pliku projektu ( `.csproj` ):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

Hosting w procesie jest ustawiony przy użyciu `InProcess` wartości domyślnej.

Wartość `<AspNetCoreHostingModel>` jest uwzględniana wielkość liter, dlatego `inprocess` i `outofprocess` są prawidłowymi wartościami.

Serwer [Kestrel](xref:fundamentals/servers/kestrel) jest używany zamiast serwera http usług IIS ( `IISHttpServer` ).

W przypadku nieprzetwarzanych [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) wywołań <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> :

* Skonfiguruj port i ścieżkę bazową, na której serwer powinien nasłuchiwać przy uruchomionym za modułem ASP.NET Core.
* Skonfiguruj hosta do przechwytywania błędów uruchamiania.

### <a name="process-name"></a>Nazwa procesu

`Process.GetCurrentProcess().ProcessName`Raporty `w3wp` / `iisexpress` (w procesie) lub `dotnet` (pozaprocesowe).

Wiele modułów macierzystych, takich jak uwierzytelnianie systemu Windows, pozostaje aktywnych. Aby dowiedzieć się więcej na temat modułów usług IIS aktywnych przy użyciu modułu ASP.NET Core, zobacz <xref:host-and-deploy/iis/modules> .

Moduł ASP.NET Core może również:

* Ustaw zmienne środowiskowe dla procesu roboczego.
* Rejestruj dane wyjściowe stdout do magazynu plików w celu rozwiązywania problemów z uruchamianiem.
* Przekazuj tokeny uwierzytelniania systemu Windows.
