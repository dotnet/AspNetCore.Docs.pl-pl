---
title: Zarządzanie kluczami i okres istnienia ochrony danych w ASP.NET Core
author: rick-anderson
description: Informacje na temat zarządzania kluczami i okresu istnienia ochrony danych w programie ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: b39187d93247dc83c34bbbe6ec6accfd77108794
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021383"
---
# <a name="data-protection-key-management-and-lifetime-in-aspnet-core"></a>Zarządzanie kluczami i okres istnienia ochrony danych w ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="key-management"></a>Zarządzanie kluczami

Aplikacja próbuje wykryć swoje środowisko operacyjne i samodzielnie obsługiwać konfigurację klucza.

1. Jeśli aplikacja jest hostowana w [usłudze Azure Apps](https://azure.microsoft.com/services/app-service/), klucze są utrwalane w folderze *%Home%\ASP.NET\DataProtection-Keys* . Ten folder jest obsługiwany przez magazyn sieciowy i synchronizowany na wszystkich komputerach obsługujących aplikację.
   * Klucze nie są chronione w stanie spoczynku.
   * Folder *dataprotection-Keys* dostarcza kluczowy pierścień do wszystkich wystąpień aplikacji w jednym miejscu wdrożenia.
   * Oddzielne miejsca wdrożenia, takie jak przygotowanie i środowisko produkcyjne, nie dzielą się z kluczem. W przypadku wymiany między miejscami wdrożenia, na przykład zamiany przejściowej na produkcję lub użycie testowania/B, dowolna aplikacja korzystająca z ochrony danych nie będzie w stanie odszyfrować przechowywanych danych przy użyciu dzwonka klucza w poprzednim gnieździe. Prowadzi to do użytkowników logujących się z aplikacji korzystającej ze standardowego uwierzytelniania ASP.NET Core cookie , ponieważ używa ochrony danych do ochrony swoich elementów cookie . Jeśli potrzebujesz pierścieni z kluczami niezależnymi od gniazda, Użyj zewnętrznego dostawcy usługi Key dystynktywnego, takiego jak Azure Blob Storage, Azure Key Vault, magazynu SQL lub pamięci podręcznej Redis.

1. Jeśli profil użytkownika jest dostępny, klucze są utrwalane w folderze *%LocalAppData%\ASP.NET\DataProtection-Keys* . Jeśli system operacyjny jest Windows, klucze są szyfrowane przy użyciu funkcji DPAPI.

   [Atrybut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) puli aplikacji również musi być włączony. Wartość domyślna `setProfileEnvironment` to `true` . W niektórych scenariuszach (na przykład system operacyjny Windows) `setProfileEnvironment` jest ustawiony na `false` . Jeśli klucze nie są przechowywane w katalogu profilu użytkownika zgodnie z oczekiwaniami:

   1. Przejdź do folderu *% windir%/system32/inetsrv/config*
   1. Otwórz plik *applicationHost.config* .
   1. Znajdź `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.
   1. Upewnij się, że `setProfileEnvironment` atrybut nie istnieje, który jest wartością domyślną `true` , lub jawnie ustaw wartość atrybutu na `true` .

1. Jeśli aplikacja jest hostowana w usługach IIS, klucze są utrwalane w rejestrze HKLM w specjalnym kluczu rejestru, który jest ACLed tylko dla konta procesu roboczego. Klucze są szyfrowane przy użyciu funkcji DPAPI.

1. Jeśli żaden z tych warunków nie jest zgodny, klucze nie są utrwalane poza bieżącym procesem. Gdy proces zostanie zamknięty, wszystkie wygenerowane klucze zostaną utracone.

Deweloper ma zawsze pełną kontrolę i może przesłonić, w jaki sposób i gdzie są przechowywane klucze. Pierwsze trzy opcje powinny zapewnić dobre wartości domyślne dla większości aplikacji, podobnie jak **\<machineKey>** procedury autogeneracji ASP.NET działały w przeszłości. Końcowa opcja powrotu jest jedynym scenariuszem, który wymaga, aby programista określił [konfigurację](xref:security/data-protection/configuration/overview) z góry, jeśli chcą, aby klucze były trwałe, ale ta wartość rezerwowa występuje tylko w rzadkich sytuacjach.

W przypadku hostowania w kontenerze platformy Docker klucze powinny być utrwalane w folderze, który jest woluminem platformy Docker (udostępnionym woluminem lub woluminem zainstalowanym przez hosta, który utrzymuje się poza okresem istnienia kontenera) lub dostawcą zewnętrznym, takim jak [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) lub [Redis](https://redis.io/). Dostawca zewnętrzny jest również przydatny w scenariuszach farmy sieci Web, jeśli aplikacje nie mogą uzyskać dostępu do udostępnionego woluminu sieciowego (zobacz [PersistKeysToFileSystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem) , aby uzyskać więcej informacji).

> [!WARNING]
> Jeśli deweloper zastępuje reguły opisane powyżej i wskazuje system ochrony danych w określonym repozytorium kluczy, automatyczne szyfrowanie kluczy w spoczynku jest wyłączone. Ochronę w czasie spoczynku można włączyć za pomocą [konfiguracji](xref:security/data-protection/configuration/overview).

## <a name="key-lifetime"></a>Okres istnienia klucza

Klucze mają domyślnie 90-dniowy okres istnienia. Po wygaśnięciu klucza aplikacja automatycznie generuje nowy klucz i ustawia nowy klucz jako klucz aktywny. Tak długo, jak wycofane klucze pozostają w systemie, aplikacja może odszyfrować wszystkie dane, z którymi są chronione. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling) .

## <a name="default-algorithms"></a>Domyślne algorytmy

Domyślnym algorytmem ochrony ładunku jest algorytm AES-256-CBC w celu poufności i HMACSHA256 na potrzeby autentyczności. 512-bitowy klucz główny, zmieniony co 90 dni, jest używany do wygenerowania dwóch podkluczy używanych dla tych algorytmów dla poszczególnych ładunków. Aby uzyskać więcej informacji, zobacz [wyprowadzanie podklucza](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation) .

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:security/data-protection/extensibility/key-management>
* <xref:host-and-deploy/web-farm>
