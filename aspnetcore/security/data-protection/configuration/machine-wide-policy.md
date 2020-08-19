---
title: Obsługa zasad na całym komputerze ochrony danych w ASP.NET Core
author: rick-anderson
description: Dowiedz się więcej na temat obsługi ustawiania domyślnych zasad komputera dla wszystkich aplikacji, które używają ASP.NET Core ochrony danych.
ms.author: riande
ms.date: 10/14/2016
no-loc:
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
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: dea34a3c0fcee26bcf6bffb92f1b3fdcce0e9386
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632879"
---
# <a name="data-protection-machine-wide-policy-support-in-aspnet-core"></a>Obsługa zasad na całym komputerze ochrony danych w ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

W przypadku uruchamiania w systemie Windows system ochrony danych ma ograniczoną obsługę ustawiania domyślnych zasad komputera dla wszystkich aplikacji korzystających z ASP.NET Core ochrony danych. Ogólnym pomysłem jest to, że administrator może chcieć zmienić ustawienie domyślne, takie jak algorytmy używane lub kluczowy okres istnienia, bez konieczności ręcznego aktualizowania każdej aplikacji na komputerze.

> [!WARNING]
> Administrator systemu może ustawić zasady domyślne, ale nie może wymusić tego ustawienia. Deweloper aplikacji zawsze może przesłonić dowolną wartość przy użyciu jednego z nich. Zasady domyślne mają wpływ tylko na aplikacje, w których Deweloper nie określił jawnej wartości ustawienia.

## <a name="setting-default-policy"></a>Ustawianie zasad domyślnych

Aby ustawić zasady domyślne, administrator może ustawić znane wartości w rejestrze systemowym w następującym kluczu rejestru:

**HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection**

Jeśli korzystasz z 64-bitowego systemu operacyjnego i chcesz mieć wpływ na zachowanie aplikacji 32-bitowych, pamiętaj, aby skonfigurować odpowiednik Wow6432Node powyższego klucza.

Obsługiwane wartości są przedstawione poniżej.

| Wartość              | Typ   | Opis |
| ------------------ | :----: | ----------- |
| Szyfrowanietype     | ciąg | Określa, które algorytmy mają być używane do ochrony danych. Wartość musi być wartością CNG-CBC, CNG-GCM lub Managed i została opisana bardziej szczegółowo poniżej. |
| DefaultKeyLifetime | DWORD  | Określa okres istnienia nowo generowanych kluczy. Wartość jest określona w dniach i musi być >= 7. |
| KeyEscrowSinks     | ciąg | Określa typy, które są używane dla klucza Escrow. Wartość to rozdzielana średnikami lista kluczy ujścia usługi Escrow, gdzie każdy element na liście jest kwalifikowaną nazwą zestawu typu, który implementuje [IKeyEscrowSink](/dotnet/api/microsoft.aspnetcore.dataprotection.keymanagement.ikeyescrowsink). |

## <a name="encryption-types"></a>Typy szyfrowania

Jeśli EncryptionType ma wartość CNG-CBC, system jest skonfigurowany do używania szyfrowania bloku symetrycznego w trybie CBC w celu zapewnienia poufności i algorytmu HMAC w przypadku autentyczności z usługami udostępnianymi przez program Windows CNG (Aby uzyskać więcej informacji, zobacz [Określanie niestandardowych algorytmów CNG systemu Windows](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) ). Obsługiwane są następujące dodatkowe wartości, z których każdy odpowiada właściwości typu CngCbcAuthenticatedEncryptionSettings.

| Wartość                       | Typ   | Opis |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | ciąg | Nazwa algorytmu szyfrowania symetrycznego, zrozumiała dla CNG. Ten algorytm jest otwarty w trybie CBC. |
| EncryptionAlgorithmProvider | ciąg | Nazwa implementacji dostawcy CNG, która może generować algorytm EncryptionAlgorithm. |
| EncryptionAlgorithmKeySize  | DWORD  | Długość (w bitach) klucza, który ma być pochodny algorytmu szyfrowania symetrycznego. |
| Algorytm               | ciąg | Nazwa algorytmu wyznaczania wartości skrótu zrozumiała dla CNG. Ten algorytm jest otwierany w trybie HMAC. |
| HashAlgorithmProvider       | ciąg | Nazwa implementacji dostawcy CNG, która może generować algorytm algorytm. |

Jeśli EncryptionType ma wartość CNG-GCM, system jest skonfigurowany do używania szyfrowania bloku symetrycznego w trybie Galois/licznika do poufności i autentyczności przy użyciu usług zapewnianych przez system Windows CNG (zobacz [Określanie niestandardowych algorytmów CNG systemu Windows](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) , aby uzyskać więcej informacji). Obsługiwane są następujące dodatkowe wartości, z których każdy odpowiada właściwości typu CngGcmAuthenticatedEncryptionSettings.

| Wartość                       | Typ   | Opis |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | ciąg | Nazwa algorytmu szyfrowania symetrycznego, zrozumiała dla CNG. Ten algorytm jest otwarty w trybie Galois/counter. |
| EncryptionAlgorithmProvider | ciąg | Nazwa implementacji dostawcy CNG, która może generować algorytm EncryptionAlgorithm. |
| EncryptionAlgorithmKeySize  | DWORD  | Długość (w bitach) klucza, który ma być pochodny algorytmu szyfrowania symetrycznego. |

Jeśli EncryptionType jest zarządzany, system jest skonfigurowany do korzystania z zarządzanego SymmetricAlgorithm na potrzeby poufności i KeyedHashAlgorithm na potrzeby autentyczności (zobacz [Określanie niestandardowych algorytmów zarządzanych](xref:security/data-protection/configuration/overview#specifying-custom-managed-algorithms) , aby uzyskać więcej informacji). Obsługiwane są następujące dodatkowe wartości, z których każdy odpowiada właściwości typu ManagedAuthenticatedEncryptionSettings.

| Wartość                      | Typ   | Opis |
| -------------------------- | :----: | ----------- |
| EncryptionAlgorithmType    | ciąg | Kwalifikowana dla zestawu nazwa typu, który implementuje SymmetricAlgorithm. |
| EncryptionAlgorithmKeySize | DWORD  | Długość (w bitach) klucza do wygenerowania algorytmu szyfrowania symetrycznego. |
| ValidationAlgorithmType    | ciąg | Kwalifikowana dla zestawu nazwa typu, który implementuje KeyedHashAlgorithm. |

Jeśli EncryptionType ma inną wartość inną niż null lub jest pusty, system ochrony danych zgłasza wyjątek podczas uruchamiania.

> [!WARNING]
> Podczas konfigurowania domyślnego ustawienia zasad, które obejmuje nazwy typów (EncryptionAlgorithmType, ValidationAlgorithmType, KeyEscrowSinks), typy muszą być dostępne dla aplikacji. Oznacza to, że w przypadku aplikacji uruchamianych na pulpicie CLR zestawy, które zawierają te typy, powinny znajdować się w globalnej pamięci podręcznej zestawów (GAC). W przypadku aplikacji ASP.NET Core działających na platformie .NET Core należy zainstalować pakiety zawierające te typy.
