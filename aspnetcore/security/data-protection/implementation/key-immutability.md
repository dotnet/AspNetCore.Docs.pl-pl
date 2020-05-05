---
title: Key niezmienności i ustawienia klucza w ASP.NET Core
author: rick-anderson
description: Zapoznaj się ze szczegółami implementacji interfejsów API niezmienności w kluczach ochrony danych ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 5bbd1fb28fc0330ccfe4e829ab0b2f86226c7166
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776919"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a>Key niezmienności i ustawienia klucza w ASP.NET Core

Gdy obiekt zostanie utrwalony w magazynie zapasowym, jego reprezentacja jest trwała. Nowe dane można dodać do magazynu zapasowego, ale nie można zmodyfikować istniejących danych. Głównym celem tego zachowania jest uniknięcie uszkodzenia danych.

Jedną z konsekwencji tego zachowania jest to, że po zapisaniu klucza do magazynu zapasowego jest on niezmienny. Nie można zmienić daty jego utworzenia, aktywacji i wygaśnięcia, chociaż można ją odwołać przy użyciu `IKeyManager`. Ponadto zmienne podstawowe informacje o algorytmach, główny materiał do obsługi kluczy i szyfrowanie we właściwościach REST są również niemodyfikowalne.

Jeśli deweloper zmieni wszystkie ustawienia, które mają wpływ na trwałość klucza, te zmiany nie będą obowiązywać do momentu następnego wygenerowania klucza, za pomocą jawnego wywołania `IKeyManager.CreateNewKey` lub przez samodzielne [Automatyczne generowanie klucza](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) systemu ochrony danych. Ustawienia wpływające na trwałość klucza są następujące:

* [Domyślny okres istnienia klucza](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [Szyfrowanie klucza w mechanizmie REST](xref:security/data-protection/implementation/key-encryption-at-rest)

* [Informacje o algorytmach zawarte w kluczu](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

Jeśli te ustawienia są potrzebne, aby uruchomić je wcześniej niż w następnym automatycznym czasie wycofywania, rozważ jawne wywołanie `IKeyManager.CreateNewKey` , aby wymusić utworzenie nowego klucza. Pamiętaj, aby podać jawną datę aktywacji ({Now + 2 dni} to lepsza reguła przycisku przewijania, która pozwala na przekroczenie czasu zmiany) i datę wygaśnięcia w wywołaniu.

>[!TIP]
> Wszystkie aplikacje dotykające repozytorium powinny określać te same ustawienia przy użyciu `IDataProtectionBuilder` metod rozszerzających. W przeciwnym razie właściwości trwałego klucza będą zależne od konkretnej aplikacji, która wywołała procedury generowania kluczy.
