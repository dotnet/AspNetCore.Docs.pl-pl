---
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
ms.openlocfilehash: ca743cdb39423d833902c330f6f0682b600c9833
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552613"
---
::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

> [!WARNING]
> Parametr **"catch-all"** może być zgodny z niepoprawnymi trasami z powodu [błędu](https://github.com/dotnet/aspnetcore/issues/18677) w routingu. Aplikacje, na które ma wpływ ta usterka, mają następującą charakterystykę:
>
> * Trasa przechwycenia, na przykład `{**slug}"`
> * Trasa catch-all nie będzie zgodna z żądaniami, które powinny być zgodne.
> * Usunięcie innych tras spowoduje rozpoczęcie pracy z całą trasą.
>
> Zobacz błędy usługi GitHub [18677](https://github.com/dotnet/aspnetcore/issues/18677) i [16579](https://github.com/dotnet/aspnetcore/issues/16579) , aby zobaczyć przykładowe przypadki, w których trafili ten błąd.
>
> Poprawka dotycząca tej usterki jest zawarta w programie [.NET Core 3.1.301 SDK i nowszych](https://dotnet.microsoft.com/download/dotnet-core/3.1). Poniższy kod ustawia przełącznik wewnętrzny, który naprawia tę usterkę:
>
>```csharp
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```

::: moniker-end