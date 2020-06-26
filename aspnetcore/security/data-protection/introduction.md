---
title: Ochrona danych ASP.NET Core
author: rick-anderson
description: Poznaj koncepcję ochrony danych i zasady projektowania interfejsów API ochrony danych ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/introduction
ms.openlocfilehash: 60cf659c720012d05bb2a6f1433c18d347469462
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399533"
---
# <a name="aspnet-core-data-protection"></a>Ochrona danych ASP.NET Core

Aplikacje sieci Web często muszą przechowywać dane z uwzględnieniem zabezpieczeń. System Windows udostępnia aplikacje pulpitu DPAPI, ale jest to nieodpowiednie dla aplikacji sieci Web. Stos ochrony danych ASP.NET Core zapewnia prosty, łatwy w użyciu interfejs API kryptografii, który programista może używać do ochrony danych, w tym zarządzania kluczami i rotacji.

Stos ochrony danych ASP.NET Core został zaprojektowany tak, aby służyć jako długoterminowe zastąpienie &lt; &gt; elementu machineKey w ASP.NET 1. x-4. x. Została zaprojektowana tak, aby dotyczyła wielu wad starego stosu kryptograficznego, zapewniając gotowe rozwiązanie do większości przypadków użycia nowoczesnych aplikacji, które prawdopodobnie wystąpią.

## <a name="problem-statement"></a>Opis problemu

Ogólna Instrukcja problemu może być zwięzłie określona w jednym zdaniu: Chcę utrzymać zaufane informacje do późniejszego pobrania, ale nie mam zaufania mechanizmu trwałości. W przypadku terminów sieci Web może to być napisano jako "muszę przeprowadzić dwukierunkową relację zaufania za pośrednictwem niezaufanego klienta".

Przykładem kanonicznym jest plik cookie uwierzytelniania lub token okaziciela. Serwer generuje "jestem Groot i ma uprawnienia XYZ" i udostępnia go klientowi. W przyszłości klient będzie zaprezentować ten token z powrotem do serwera, ale serwer wymaga pewnego rodzaju pewności, że klient nie wykonał sfałszowanego tokenu. W rezultacie pierwsze wymaganie: autentyczność (vel integralność, nieautoryzowane sprawdzanie poprawności).

Ponieważ trwały stan jest traktowany jako zaufany przez serwer, przewidujemy, że ten stan może zawierać informacje specyficzne dla środowiska operacyjnego. Może to być w postaci ścieżki pliku, uprawnienia, uchwytu lub innego odwołania pośredniego lub innego elementu danych specyficznych dla serwera. Takie informacje nie powinny być zwykle ujawniane niezaufanym klientom. W tym przypadku drugie wymaganie: poufność.

Na koniec, ponieważ nowoczesne aplikacje są składnikiem, to to, co widzimy, jest to, że poszczególne składniki chcą korzystać z tego systemu bez względu na inne składniki systemu. Na przykład, jeśli składnik tokenu okaziciela używa tego stosu, powinien działać bez ingerencji z mechanizmu CSRF, który może również korzystać z tego samego stosu. W ten sposób końcowe wymaganie: izolacja.

Możemy zapewnić dalsze ograniczenia, aby zawęzić zakres naszych wymagań. Przyjęto założenie, że wszystkie usługi działające w ramach cryptosystem są równie zaufane i że dane nie muszą być generowane ani używane poza usługami w ramach naszej kontroli bezpośredniej. Ponadto wymagamy, aby operacje były tak szybko, jak to możliwe, ponieważ każde żądanie do usługi sieci Web może przekroczyć cryptosystem jeden lub więcej razy. Dzięki temu symetryczne Kryptografia jest idealnym rozwiązaniem w naszym scenariuszu, a firma Microsoft może rabatać asymetryczne kryptografie do momentu, gdy jest to konieczne.

## <a name="design-philosophy"></a>Zagadnienie projektowe

Rozpoczęto od zidentyfikowania problemów z istniejącym stosem. Po przeprowadzeniu tej czynności zbadamy poziom istniejących rozwiązań i zauważasz, że żadne dotychczasowe rozwiązania nie były już dostępne. Następnie tworzymy rozwiązanie na podstawie kilku zasad dotyczących identyfikatorów GUID.

* System powinien oferować prostotę konfiguracji. Idealnym rozwiązaniem może być konfiguracja zerowa i deweloperzy. W sytuacjach, gdy deweloperzy muszą skonfigurować określony aspekt (na przykład repozytorium kluczy), należy rozważyć, że te konkretne konfiguracje są proste.

* Oferuje prosty interfejs API dla klientów. Interfejsy API powinny być łatwe w użyciu i być trudne do poprawnego użycia.

* Deweloperzy nie powinni uczyć się zasad zarządzania kluczami. System powinien obsługiwać wybór algorytmu i okres istnienia klucza w imieniu dewelopera. W idealnym przypadku Deweloper nie powinien nawet mieć dostępu do surowca klucza.

* Klucze powinny być chronione w stanie spoczynku, gdy jest to możliwe. System powinien ustalić odpowiedni domyślny mechanizm ochrony i zastosować go automatycznie.

Korzystając z tych zasad, opracowano prosty, [łatwy w](xref:security/data-protection/using-data-protection) obsłudze stos ochrony danych.

Interfejsy API ochrony danych ASP.NET Core nie są przede wszystkim przeznaczone do nieograniczonego trwałości poufnych ładunków. Inne technologie, takie jak [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) i [Azure Rights Management](/rights-management/) , są bardziej odpowiednie dla scenariusza nieograniczonego magazynu i mają odpowiadające im silne możliwości zarządzania kluczami. Oznacza to, że nie ma żadnych zakazów używania ASP.NET Core interfejsów API ochrony danych do długoterminowej ochrony poufnych danych.

## <a name="audience"></a>Grupy odbiorców

System ochrony danych jest podzielony na pięć głównych pakietów. Różne aspekty tych interfejsów API są przeznaczone dla trzech głównych odbiorców;

1. [Interfejsy API odbiorców — Omówienie](xref:security/data-protection/consumer-apis/overview) aplikacji docelowych i deweloperów platformy.

   "Nie chcę dowiedzieć się, jak działa stos lub jak jest on skonfigurowany. Po prostu chcę wykonać pewne operacje w sposób możliwie prosty z dużym prawdopodobieństwem, aby pomyślnie korzystać z interfejsów API ".

2. [Interfejsy API konfiguracji](xref:security/data-protection/configuration/overview) są przeznaczone dla deweloperów aplikacji i administratorów systemu.

   "Muszę poinformować system ochrony danych, że moje środowisko wymaga niedomyślnych ścieżek lub ustawień".

3. Interfejsy API rozszerzalności są przeznaczone dla deweloperów odpowiedzialnych za implementowanie zasad niestandardowych. Użycie tych interfejsów API byłoby ograniczone do rzadkich sytuacji i doświadczonych deweloperów, których dotyczą zabezpieczenia.

   "Muszę zastąpić cały składnik w systemie, ponieważ mam prawdziwie wyjątkowe wymagania dotyczące zachowania. Chcę poznać nietypowe części powierzchni interfejsu API, aby utworzyć wtyczkę, która spełnia wymagania ".

## <a name="package-layout"></a>Układ pakietu

Stos ochrony danych składa się z pięciu pakietów.

* [Microsoft. AspNetCore. dataprotection. abstrakcje](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) zawierają <xref:Microsoft.AspNetCore.DataProtection.IDataProtectionProvider> interfejsy i <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> umożliwiają tworzenie usług ochrony danych. Zawiera także przydatne metody rozszerzające do pracy z tymi typami (na przykład [IDataProtector. Protect](xref:Microsoft.AspNetCore.DataProtection.DataProtectionCommonExtensions.Protect*)). Jeśli system ochrony danych jest inicjowany w innym miejscu i korzystasz z interfejsu API, należy odwołać się do niego `Microsoft.AspNetCore.DataProtection.Abstractions` .

* [Microsoft. AspNetCore. dataprotection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) zawiera podstawową implementację systemu ochrony danych, w tym podstawowe operacje kryptograficzne, zarządzanie kluczami, konfigurację i rozszerzalność. Aby utworzyć wystąpienie systemu ochrony danych (na przykład dodając go do <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> ) lub modyfikując lub rozszerzając jego zachowanie, należy odwołać się do `Microsoft.AspNetCore.DataProtection` .

* [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) zawiera dodatkowe interfejsy API, które deweloperzy mogą znaleźć użyteczne, ale które nie należą do pakietu podstawowego. Na przykład ten pakiet zawiera metody fabryki umożliwiające utworzenie wystąpienia systemu ochrony danych w celu przechowywania kluczy w lokalizacji w systemie plików bez wstrzykiwania zależności (zobacz <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> ). Zawiera również metody rozszerzające, które ograniczają okres istnienia chronionych ładunków (zobacz <xref:Microsoft.AspNetCore.DataProtection.ITimeLimitedDataProtector> ).

* [Microsoft.AspNetCore.DataProtection.SystemWeb](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.SystemWeb/) można zainstalować w istniejącej aplikacji ASP.NET 4. x, aby przekierować jej `<machineKey>` operacje do korzystania z nowego stosu ochrony danych ASP.NET Core. Aby uzyskać więcej informacji, zobacz <xref:security/data-protection/compatibility/replacing-machinekey>.

* [Microsoft. AspNetCore. Cryptography. Datapochodny](https://www.nuget.org/packages/Microsoft.AspNetCore.Cryptography.KeyDerivation/) dostarcza implementację procedury skrótu hasła PBKDF2 i może być używana przez systemy, które muszą bezpiecznie obsługiwać hasła użytkowników. Aby uzyskać więcej informacji, zobacz <xref:security/data-protection/consumer-apis/password-hashing>.

## <a name="additional-resources"></a>Zasoby dodatkowe

<xref:host-and-deploy/web-farm>
