---
title: Telemetrię HttpRepl
author: scottaddie
description: Dowiedz się więcej o telemetrii zebranej przez HttpRepl.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/11/2020
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
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 5ff22753f566c494e51dae67c8c4f6371211be78
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550611"
---
# <a name="httprepl-telemetry"></a>Telemetrię HttpRepl

[HttpRepl](xref:web-api/http-repl) zawiera funkcję telemetrii, która zbiera dane dotyczące użycia. Należy pamiętać, że zespół HttpRepl rozumie, w jaki sposób narzędzie jest używane, aby można je było ulepszyć.

## <a name="how-to-opt-out"></a>Jak zrezygnować

Funkcja telemetrii HttpRepl jest domyślnie włączona. Aby zrezygnować z funkcji telemetrii, należy ustawić `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` zmienną środowiskową na `1` lub `true` .

## <a name="disclosure"></a>Mogąc

Podczas pierwszego uruchomienia narzędzia HttpRepl wyświetla tekst podobny do poniższego. Tekst może się nieco różnić w zależności od wersji uruchomionego narzędzia. To "pierwsze uruchomienie" polega na tym, jak firma Microsoft powiadamia użytkownika o zbieraniu danych.

```console
Telemetry
---------
The .NET tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

Aby pominąć tekst środowiska "pierwsze uruchomienie", należy ustawić `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` zmienną środowiskową na `1` lub `true` .

## <a name="data-points"></a>Punkty danych

Funkcja telemetrii nie:

* Zbieraj dane osobowe, takie jak nazwy użytkowników, adresy e-mail lub adresy URL.
* Skanuj żądania HTTP lub odpowiedzi.

Dane są bezpiecznie przesyłane do serwerów firmy Microsoft i przechowywane w ograniczonym dostępie.

Ochrona prywatności jest dla nas ważna. Jeśli podejrzewasz, że funkcja telemetrii zbiera poufne dane lub dane są w sposób niezabezpieczony lub niewłaściwie obsłużone, wykonaj jedną z następujących czynności:

* Zaplikowanie problemu w repozytorium [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) .
* Wyślij wiadomość e-mail na adres [dotnet@microsoft.com](mailto:dotnet@microsoft.com) w celu zbadania problemu.

Funkcja telemetrii zbiera następujące dane.

| Wersje zestawu SDK platformy .NET | Dane |
|--------------|------|
| >= 5,0        | Sygnatura czasowa wywołania. |
| >= 5,0        | Trzy-oktetowe adresy IP używane do określenia lokalizacji geograficznej. |
| >= 5,0        | System operacyjny i wersja. |
| >= 5,0        | Identyfikator środowiska uruchomieniowego (RID), na którym jest uruchomione narzędzie. |
| >= 5,0        | Czy narzędzie jest uruchomione w kontenerze. |
| >= 5,0        | Adres MAC w postaci skrótu Access Control: kryptograficzny (SHA256) i unikatowy identyfikator dla maszyny. |
| >= 5,0        | Wersja jądra. |
| >= 5,0        | Wersja HttpRepl. |
| >= 5,0        | Czy narzędzie zostało uruchomione z `help` `run` argumentami,, czy `connect` . Rzeczywiste wartości argumentów nie są zbierane. |
| >= 5,0        | Wywołano polecenie (na przykład `get` ) i czy zakończyło się ono pomyślnie. |
| >= 5,0        | Dla `connect` polecenia, czy `root` `base` argumenty, lub `openapi` zostały dostarczone. Rzeczywiste wartości argumentów nie są zbierane. |
| >= 5,0        | Dla `pref` polecenia — czy `get` `set` został wystawiony, a także do którego dostępu uzyskano dostęp. Jeśli nie jest to dobrze znane preferencja, nazwa jest skrótem. Wartość nie jest zbierana. |
| >= 5,0        | Dla `set header` polecenia zostanie ustawiona nazwa nagłówka. Jeśli nie jest dobrze znanym nagłówkiem, nazwa jest skrótem. Wartość nie jest zbierana. |
| >= 5,0        | Dla polecenia, niezależnie od tego, czy `connect` użyto specjalnego przypadku `dotnet new webapi` , i czy został pominięty przez preferencję. |
| >= 5,0        | Dla wszystkich poleceń HTTP (na przykład GET, POST, PUT), niezależnie od tego, czy każda z opcji została określona. Wartości opcji nie są zbierane. |

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Dane telemetryczne zestaw .NET Core SDK](/dotnet/core/tools/telemetry)
* [Interfejs wiersza polecenia platformy .NET Core dane telemetryczne](https://dotnet.microsoft.com/platform/telemetry)
