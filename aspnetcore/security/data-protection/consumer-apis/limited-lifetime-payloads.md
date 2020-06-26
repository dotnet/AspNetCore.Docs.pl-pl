---
title: Ogranicz okres istnienia chronionych ładunków w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak ograniczyć okres istnienia chronionego ładunku przy użyciu interfejsów API ochrony danych ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: d8c83ca46b1993af1f5e7985571ff012d90b1e01
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408373"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a>Ogranicz okres istnienia chronionych ładunków w ASP.NET Core

Istnieją scenariusze, w których Deweloper aplikacji chce utworzyć chroniony ładunek, który wygaśnie po upływie określonego czasu. Na przykład, chroniony ładunek może reprezentować token resetowania hasła, który powinien być prawidłowy tylko przez jedną godzinę. W przypadku deweloperów istnieje możliwość utworzenia własnego formatu ładunku zawierającego osadzoną datę wygaśnięcia, a zaawansowani deweloperzy mogą chcieć to zrobić mimo to, ale w przypadku większości deweloperów, którzy zarządzają tymi wygasami, mogą wzrosnąć żmudnym.

Aby ułatwić naszym użytkownikom deweloperów, pakiet [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) zawiera interfejsy API narzędzi do tworzenia ładunków, które automatycznie wygasną po upływie określonego czasu. Te interfejsy API zawieszają się od `ITimeLimitedDataProtector` typu.

## <a name="api-usage"></a>Użycie interfejsu API

`ITimeLimitedDataProtector`Interfejs to podstawowy interfejs do ochrony i nieochrony ładunków z ograniczeniami czasowymi/z własnym wygaśnięciem. Aby utworzyć wystąpienie obiektu, należy `ITimeLimitedDataProtector` najpierw potrzebować wystąpienia regularnego [IDataProtector](xref:security/data-protection/consumer-apis/overview) skonstruowanego z określonym przeznaczeniem. Gdy `IDataProtector` wystąpienie jest dostępne, wywołaj `IDataProtector.ToTimeLimitedDataProtector` metodę rozszerzenia, aby odzyskać funkcję ochrony z wbudowanymi funkcjami wygaśnięcia.

`ITimeLimitedDataProtector`udostępnia następujące metody:

* Funkcja onprotecter (przeznaczenie ciągu): ITimeLimitedDataProtector — ten interfejs API jest podobny do istniejącego `IDataProtectionProvider.CreateProtector` w programie, który może służyć do tworzenia [łańcuchów celów](xref:security/data-protection/consumer-apis/purpose-strings) z poziomu głównej ochrony ograniczonej czasowo.

* Ochrona (Byte [] — zwykły tekst, wygaśnięcie DateTimeOffset): Byte []

* Ochrona (Byte [] — zwykły tekst, okres istnienia przedziału czasu): Byte []

* Chroń (Byte [] zwykły tekst): Byte []

* Ochrona (tekst tekstowy, wygaśnięcie DateTimeOffset): ciąg

* Ochrona (tekst tekstowy, okres istnienia przedziału): ciąg

* Ochrona (ciąg tekstowy): ciąg

Oprócz podstawowych `Protect` metod, które pobierają tylko zwykły tekst, istnieją nowe przeciążenia, które umożliwiają określenie daty wygaśnięcia ładunku. Datę wygaśnięcia można określić jako datę bezwzględną (przez `DateTimeOffset` ) lub jako czas względny (od bieżącego czasu systemowego za pośrednictwem programu `TimeSpan` ). Jeśli zostanie wywołane Przeciążenie, które nie przyjmuje czasu wygaśnięcia, ładunek jest założono, że nigdy nie wygaśnie.

* Unprotected (Byte [] protectedData, out — wygaśnięcie DateTimeOffset): Byte []

* Unprotected (Byte [] protectedData): Byte []

* Wyłącz ochronę (ciąg protectedData, out — wygaśnięcie DateTimeOffset): ciąg

* Unprotected (String protectedData): ciąg

`Unprotect`Metody zwracają oryginalne niechronione dane. Jeśli ładunek jeszcze nie wygasł, bezwzględne wygaśnięcie jest zwracane jako opcjonalny parametr out wraz z oryginalnymi danymi niechronionymi. Jeśli ładunek wygasł, wszystkie przeciążenia metody unprotected będą generować CryptographicException.

>[!WARNING]
> Nie zaleca się używania tych interfejsów API do ochrony ładunków, które wymagają długoterminowej lub nieograniczonej trwałości. "Czy mogę zapewnić trwałe nieodwracalne odzyskanie chronionych ładunków po miesiącu?" może działać jako dobra zasada dla kciuka; Jeśli odpowiedź nie jest, deweloperzy powinni rozważyć alternatywne interfejsy API.

W poniższym przykładzie są stosowane [ścieżki kodu inne niż di](xref:security/data-protection/configuration/non-di-scenarios) do tworzenia wystąpienia systemu ochrony danych. Aby uruchomić ten przykład, upewnij się, że najpierw Dodano odwołanie do pakietu Microsoft. AspNetCore. dataprotection. Extensions.

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
