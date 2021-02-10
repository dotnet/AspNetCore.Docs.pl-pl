---
title: ASP.NET Core Blazor pliki statyczne
author: guardrex
description: Dowiedz się, jak konfigurować i zarządzać plikami statycznymi dla Blazor aplikacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/27/2021
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
uid: blazor/fundamentals/static-files
ms.openlocfilehash: 709250251113014027fe86c6a373e58a9c2a5009
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100110034"
---
# <a name="aspnet-core-blazor-static-files"></a>ASP.NET Core Blazor pliki statyczne

*Ten artykuł ma zastosowanie do programu Blazor Server .*

Aby utworzyć dodatkowe mapowania plików z <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> lub skonfigurować inne <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> , użyj **jednej** z poniższych metod. W poniższych przykładach `{EXTENSION}` symbol zastępczy to rozszerzenie pliku, a `{CONTENT TYPE}` symbol zastępczy to typ zawartości.

* Skonfiguruj opcje za pomocą [iniekcji zależności (di)](xref:blazor/fundamentals/dependency-injection) w `Startup.ConfigureServices` ( `Startup.cs` ) przy użyciu <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> :

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  services.Configure<StaticFileOptions>(options =>
  {
      options.ContentTypeProvider = provider;
  });
  ```

  Ponieważ takie podejście konfiguruje tego samego dostawcę plików, który jest używany do obsługi programu `blazor.server.js` , należy się upewnić, że Konfiguracja niestandardowa nie zakłóca działania programu `blazor.server.js` . Na przykład nie należy usuwać mapowania dla plików JavaScript przez skonfigurowanie dostawcy za pomocą programu `provider.Mappings.Remove(".js")` .

* Użyj dwóch wywołań <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> w programie `Startup.Configure` ( `Startup.cs` ):
  * Skonfiguruj niestandardowego dostawcę plików przy pierwszym wywołaniu przy użyciu <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> .
  * Drugie oprogramowanie pośredniczące służy `blazor.server.js` do korzystania z domyślnej konfiguracji plików statycznych dostarczonych przez Blazor platformę.

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* Można uniknąć zakłócania działania programu przy `_framework/blazor.server.js` użyciu programu <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> do wykonywania niestandardowego oprogramowania pośredniczącego plików statycznych:

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```
