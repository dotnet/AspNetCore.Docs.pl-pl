---
title: Zarządzanie kluczami w ASP.NET Core
author: rick-anderson
description: Poznaj szczegóły implementacji interfejsów API zarządzania kluczami ASP.NET Core ochrony danych.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/implementation/key-management
ms.openlocfilehash: f7e0fa5e80208469188a11239a9453ecafdcf5f3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060199"
---
# <a name="key-management-in-aspnet-core"></a>Zarządzanie kluczami w ASP.NET Core

<a name="data-protection-implementation-key-management"></a>

System ochrony danych automatycznie zarządza okresem istnienia kluczy głównych używanych do ochrony i nieochrony ładunków. Każdy klucz może istnieć w jednym z czterech etapów:

* Utworzono — klucz istnieje w pęku kluczy, ale nie został jeszcze aktywowany. Klucza nie należy używać w przypadku nowych operacji ochrony, dopóki nie upłynie wystarczająco dużo czasu, że klucz ma szansę na propagację na wszystkie maszyny, które zużywają ten pierścień kluczy.

* Aktywny — klucz istnieje w pierścieniu kluczy i powinien być używany dla wszystkich nowych operacji ochrony.

* Wygasł — klucz został uruchomiony jako jego naturalny okres istnienia i nie powinien już być używany dla nowych operacji ochrony.

* Odwołany — klucz został złamany i nie może być używany dla nowych operacji ochrony.

Klucze utworzone, aktywne i wygasłe mogą służyć do wykluczania ochrony przychodzących ładunków. Klucze odwołane domyślnie nie mogą być używane do ochrony ładunków, ale Deweloper aplikacji może [przesłonić to zachowanie](xref:security/data-protection/consumer-apis/dangerous-unprotect#data-protection-consumer-apis-dangerous-unprotect) w razie potrzeby.

>[!WARNING]
> Deweloper może być skłonny do usunięcia klucza z pierścienia kluczy (np. przez usunięcie odpowiedniego pliku z systemu plików). W tym momencie wszystkie dane chronione przez klucz są trwale nierozpoznawalne i nie ma żadnych zastąpień awaryjnych, takich jak istnieje odwołanie do unieważnionych kluczy. Usunięcie klucza jest naprawdę zachowaniem destrukcyjnym, a w związku z tym system ochrony danych nie ujawnia interfejsu API pierwszej klasy do wykonania tej operacji.

## <a name="default-key-selection"></a>Wybór klucza domyślnego

Gdy system ochrony danych odczytuje pierścień kluczy z repozytorium zapasowego, podejmie próbę odnalezienia domyślnego klucza z pierścienia kluczy. Domyślny klucz jest używany dla nowych operacji ochrony.

Ogólna heurystyka polega na tym, że system ochrony danych wybiera klucz z najnowszą datą aktywacji jako klucz domyślny. (Istnieje mały fudgey czynnik, który umożliwia przechylenie między serwerami a serwerem). Jeśli klucz wygasł lub został odwołany, a jeśli aplikacja nie wyłączyła automatycznej generacji kluczy, zostanie wygenerowany nowy klucz z natychmiastową aktywacją zgodnie z poniższą opcją [wygaśnięcia klucza i zasadami wycofywania](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration) .

Od momentu, gdy system ochrony danych generuje nowy klucz bezpośrednio, zamiast powracać do innego klucza, to nowa generacja kluczy powinna być traktowana jako niejawne wygaśnięcie wszystkich kluczy, które zostały aktywowane przed nowym kluczem. Dobrym pomysłem jest to, że nowe klucze mogą zostać skonfigurowane przy użyciu różnych algorytmów lub mechanizmów szyfrowania w trybie spoczynku niż stare klucze, a system powinien preferować bieżącą konfigurację w porównaniu z powrotem.

Wystąpił wyjątek. Jeśli deweloper aplikacji [wyłączył automatyczne generowanie kluczy](xref:security/data-protection/configuration/overview#disableautomatickeygeneration), system ochrony danych musi wybrać coś jako klucz domyślny. W tym scenariuszu rezerwowym system wybierze klucz nieodwołany z datą ostatniej aktywacji z preferencjami dotyczącymi kluczy, które miały czas na propagację na inne maszyny w klastrze. System rezerwowy może zakończyć Wybieranie wygasłego klucza domyślnego w wyniku. System rezerwowy nigdy nie wybiera odwołanego klucza jako klucza domyślnego, a jeśli pierścień kluczy jest pusty lub każdy klucz został odwołany, system wygeneruje błąd po zainicjowaniu.

<a name="data-protection-implementation-key-management-expiration"></a>

## <a name="key-expiration-and-rolling"></a>Wygaśnięcie i wycofywanie klucza

Po utworzeniu klucza jest on automatycznie miał datę aktywacji {Now + 2 dni} i datę wygaśnięcia {Now + 90 dni}. Opóźnienie 2-dniowe przed aktywacją umożliwia przekazanie klucza przez system. Oznacza to, że umożliwia innym aplikacjom wskazanie magazynu zapasowego, aby obserwować klucz w kolejnym okresie autoodświeżania, w ten sposób maksymalizując prawdopodobieństwo, że gdy pierścień klucza stanie się aktywny, został rozpropagowany do wszystkich aplikacji, które mogą wymagać użycia.

Jeśli klucz domyślny wygaśnie w ciągu 2 dni i jeśli pierścień kluczy nie ma jeszcze klucza, który będzie aktywny po wygaśnięciu klucza domyślnego, system ochrony danych automatycznie powróci nowy klucz do dzwonka klucza. Ten nowy klucz ma datę aktywacji {Data wygaśnięcia klucza domyślnego} i datę wygaśnięcia {Now + 90 dni}. Pozwala to systemowi na regularne automatyczne rzutowanie kluczy bez przerwy w działaniu usługi.

Mogą wystąpić sytuacje, w których klucz zostanie utworzony z natychmiastową aktywacją. Przykładem może być, gdy aplikacja nie zostanie uruchomiona przez czas, a wszystkie klucze w pęku kluczy wygasły. W takim przypadku klucz otrzymuje datę aktywacji {Now} bez normalnego 2-dniowego opóźnienia aktywacji.

Domyślny okres istnienia klucza to 90 dni, ale można go skonfigurować tak, jak w poniższym przykładzie.

```csharp
services.AddDataProtection()
       // use 14-day lifetime instead of 90-day lifetime
       .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
```

Administrator może również zmienić domyślne ustawienie całego systemu, chociaż jawne wywołanie `SetDefaultKeyLifetime` spowoduje zastąpienie wszystkich zasad dotyczących całego systemu. Domyślny okres istnienia klucza nie może być krótszy niż 7 dni.

## <a name="automatic-key-ring-refresh"></a>Automatyczne odświeżanie dzwonka klucza

Po zainicjowaniu system ochrony danych odczytuje pierścień klucza z bazowego repozytorium i buforuje go w pamięci. Ta pamięć podręczna umożliwia ochronę i wyłączanie ochrony, aby można było wykonać operację bez nachodzenia do magazynu zapasowego. System automatycznie sprawdzi magazyn zapasowy pod kątem zmian co około 24 godziny lub gdy bieżący klucz domyślny wygaśnie, w zależności od tego, co nastąpi wcześniej.

>[!WARNING]
> Deweloperzy muszą bardzo rzadko korzystać z interfejsów API zarządzania kluczami. System ochrony danych wykona automatyczne zarządzanie kluczami, zgodnie z powyższym opisem.

System ochrony danych udostępnia interfejs `IKeyManager` , który może służyć do sprawdzania i wprowadzania zmian w pierścieniu kluczy. System DI, który dostarczył wystąpienia, `IDataProtectionProvider` może również dostarczyć wystąpienie `IKeyManager` do użycia. Alternatywnie można ściągnąć `IKeyManager` bezpośrednio z metody `IServiceProvider` AS w poniższym przykładzie.

Każda operacja, która modyfikuje pierścień kluczy (utworzenie nowego klucza jawnie lub wykonanie odwołania), spowoduje unieważnienie pamięci podręcznej w pamięci. Następne wywołanie `Protect` lub `Unprotect` spowoduje, że system ochrony danych odczyta dzwonek klucza i ponownie utworzy pamięć podręczną.

Poniższy przykład demonstruje użycie `IKeyManager` interfejsu do sprawdzenia i manipulowania pierścieniem kluczy, w tym odwoływanie istniejących kluczy i ręczne generowanie nowego klucza.

[!code-csharp[](key-management/samples/key-management.cs)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="key-storage"></a>Magazyn kluczy

System ochrony danych ma algorytm heurystyczny, przy użyciu którego próbuje automatycznie ustalić odpowiednią lokalizację magazynu kluczy i mechanizm szyfrowania w systemie spoczynku. Mechanizm trwałości klucza jest również konfigurowalny przez dewelopera aplikacji. W poniższych dokumentach omówiono implementacje w usłudze Box następujących mechanizmów:

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>
