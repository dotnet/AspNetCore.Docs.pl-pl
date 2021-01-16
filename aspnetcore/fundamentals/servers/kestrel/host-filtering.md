---
title: Filtrowanie hostów przy użyciu serwera ASP.NET Core Kestrel sieci Web
author: rick-anderson
description: Dowiedz się więcej na temat używania filtrowania hosta z Kestrel, wieloplatformowego serwera sieci Web dla ASP.NET Core.
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
uid: fundamentals/servers/kestrel/host-filtering
ms.openlocfilehash: d55c211f05a77f6acabedef2ff62a621d9a1844e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253994"
---
# <a name="host-filtering-with-aspnet-core-kestrel-web-server"></a>Filtrowanie hostów przy użyciu serwera ASP.NET Core Kestrel sieci Web

Program Kestrel obsługuje konfigurację na podstawie prefiksów, takich jak `http://example.com:5000` , Kestrel w znacznym stopniu ignoruje nazwę hosta. Host `localhost` jest specjalnym przypadkiem używanym do tworzenia powiązań z adresami sprzężenia zwrotnego. Każdy host poza jawnym adresem IP tworzy powiązanie ze wszystkimi publicznymi adresami IP. `Host` nagłówki nie są sprawdzane.

Aby obejść ten sposób, użyj oprogramowania pośredniczącego filtrowania hosta. Oprogramowanie pośredniczące do filtrowania hosta jest dostarczane przez pakiet [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , który jest niejawnie dostarczany dla ASP.NET Core aplikacji. Oprogramowanie pośredniczące jest dodawane przez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> program, który wywołuje <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering%2A> następujące wywołania:

[!code-csharp[](samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Programowe filtrowanie hosta jest domyślnie wyłączone. Aby włączyć oprogramowanie pośredniczące, zdefiniuj `AllowedHosts` klucz w *appsettings.json* / *appSettings. \<EnvironmentName> kod JSON*. Wartość to rozdzielana średnikami lista nazw hostów bez numerów portów:

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [Przekierowane nagłówki oprogramowania](xref:host-and-deploy/proxy-load-balancer) również mają <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> opcję. Przekazane nagłówki — oprogramowanie pośredniczące i filtrowanie hostów oprogramowanie pośredniczące ma podobną funkcjonalność dla różnych scenariuszy. Ustawienie `AllowedHosts` z przekierowanymi nagłówkami — oprogramowanie pośredniczące jest odpowiednie, gdy `Host` nagłówek nie jest zachowywany podczas przekazywania żądań z odwrotnym serwerem proxy lub modułem równoważenia obciążenia. Ustawienie `AllowedHosts` przy użyciu oprogramowania pośredniczącego filtrowania hosta jest odpowiednie, gdy Kestrel jest używany jako publiczny serwer graniczny lub gdy `Host` nagłówek jest bezpośrednio przekazywany.
>
> Aby uzyskać więcej informacji na temat przekierowanych nagłówków, należy zapoznać się z tematem <xref:host-and-deploy/proxy-load-balancer> .
