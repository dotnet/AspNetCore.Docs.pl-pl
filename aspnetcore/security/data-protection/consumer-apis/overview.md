---
title: Omówienie interfejsów API odbiorców dla ASP.NET Core
author: rick-anderson
description: Otrzymuj krótkie omówienie różnych interfejsów API odbiorców dostępnych w ramach biblioteki ochrony danych ASP.NET Core.
ms.author: riande
ms.date: 06/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: e840111d702882a45e59cf89d679b87fa537d833
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767860"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a>Omówienie interfejsów API odbiorców dla ASP.NET Core

Interfejsy `IDataProtectionProvider` i `IDataProtector` to podstawowe interfejsy, za pomocą których konsumenci używają systemu ochrony danych. Znajdują się one w pakiecie [Microsoft. AspNetCore. dataprotection. Abstracts](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) .

## <a name="idataprotectionprovider"></a>IDataProtectionProvider

Interfejs dostawcy reprezentuje główny system ochrony danych. Nie może być on bezpośrednio używany do ochrony lub wyochronowania danych. Zamiast tego konsument musi uzyskać odwołanie do obiektu `IDataProtector` przez wywołanie `IDataProtectionProvider.CreateProtector(purpose)`, gdzie cel jest ciągiem opisującym zamierzony przypadek użycia konsumenta. Zobacz [ciągi przeznaczenia](xref:security/data-protection/consumer-apis/purpose-strings) , aby uzyskać więcej informacji na temat zamiaru tego parametru i sposobu wybierania odpowiedniej wartości.

## <a name="idataprotector"></a>IDataProtector

Interfejs funkcji ochrony jest zwracany przez wywołanie do `CreateProtector`i jest to interfejs, którego klienci mogą używać do wykonywania operacji ochrony i wycofania ochrony.

Aby chronić dane, Przekaż dane do `Protect` metody. Basic Interface definiuje metodę, która konwertuje bajt []-> Byte [], ale istnieje również Przeciążenie (dostarczone jako Metoda rozszerzenia), które konwertuje ciąg > ciągu. Zabezpieczenia oferowane przez dwie metody są identyczne. Deweloper powinien wybrać dowolne Przeciążenie, które jest najwygodniejsze dla przypadków użycia. Niezależnie od wybranego przeciążenia, wartość zwrócona przez metodę ochrony jest teraz chroniona (ENCIPHERED i nieautoryzowane), a aplikacja może wysłać ją do niezaufanego klienta.

Aby wyłączyć ochronę wcześniej chronionego fragmentu danych, Przekaż chronione dane do `Unprotect` metody. (Istnieją oparte na bajtach [] i przeciążenia oparte na ciągach dla wygody dla deweloperów). Jeśli chroniony ładunek został wygenerowany przez wcześniejsze wywołanie do `Protect` tego samego `IDataProtector`, `Unprotect` Metoda zwróci oryginalny niechroniony ładunek. Jeśli chroniony ładunek został naruszony lub został wyprodukowany przez inny `IDataProtector`, `Unprotect` Metoda zwróci CryptographicException.

Koncepcja tego samego programu a różne `IDataProtector` powiązania z powrotem do koncepcji celu. Jeśli dwa `IDataProtector` wystąpienia zostały wygenerowane z tego samego `IDataProtectionProvider` katalogu głównego, ale za pośrednictwem różnych ciągów `IDataProtectionProvider.CreateProtector`przeznaczenie w wywołaniu, są one uznawane za [różne funkcje ochrony](xref:security/data-protection/consumer-apis/purpose-strings), a nikt nie będzie w stanie chronić ładunków wygenerowanych przez inne.

## <a name="consuming-these-interfaces"></a>Korzystanie z tych interfejsów

W przypadku składnika o podwójnej próbie zamierzone użycie polega na tym, że `IDataProtectionProvider` składnik przyjmuje parametr w konstruktorze, a system di automatycznie udostępnia tę usługę podczas tworzenia wystąpienia składnika.

> [!NOTE]
> Niektóre aplikacje (takie jak aplikacje konsolowe lub aplikacje ASP.NET 4. x) mogą nie być rozróżniane, więc nie można użyć mechanizmu opisanego w tym miejscu. W tych scenariuszach zapoznaj się z dokumentem [scenariusze bez nieznanej wiedzy](xref:security/data-protection/configuration/non-di-scenarios) , aby uzyskać więcej `IDataProtection` informacji na temat uzyskiwania wystąpienia dostawcy bez przechodzenia przez di.

Poniższy przykład ilustruje trzy koncepcje:

1. [Dodaj system ochrony danych](xref:security/data-protection/configuration/overview) do kontenera usługi,

2. Przy użyciu DI `IDataProtectionProvider`, aby otrzymać wystąpienie elementu, i

3. Tworzenie `IDataProtector` z poziomu `IDataProtectionProvider` i używanie go do ochrony i nieochrony danych.

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Pakiet Microsoft. AspNetCore. dataprotection. Abstracts zawiera metodę `IServiceProvider.GetDataProtector` rozszerzenia jako wygodę dla deweloperów. Jest on hermetyzowany jako pojedyncza operacja zarówno pobierającą `IDataProtectionProvider` od dostawcy usługi, jak i `IDataProtectionProvider.CreateProtector`wywołującym. Poniższy przykład demonstruje użycie.

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> Wystąpienia `IDataProtectionProvider` i `IDataProtector` są bezpieczne dla wątków dla wielu wywołań. Jest to zamierzone, gdy składnik pobiera odwołanie do `IDataProtector` elementu za pośrednictwem wywołania do `CreateProtector`, będzie używać tego odwołania dla wielu wywołań do `Protect` i. `Unprotect` Wywołanie `Unprotect` będzie zgłaszać CryptographicException, jeśli nie można zweryfikować ani deszyfrowanie chronionego ładunku. Niektóre składniki mogą chcieć zignorować błędy podczas operacji usunięcia ochrony; składnik, który odczytuje pliki cookie uwierzytelniania, może obsłużyć ten błąd i traktować żądanie tak, jakby nie zawierało w ogóle pliku cookie, a nie niepowodzeniem. Składniki, które chcą tego zachowania, powinny zwrócić uwagę na CryptographicException zamiast połknięcia wszystkich wyjątków.
