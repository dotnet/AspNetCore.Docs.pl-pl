---
title: Scenariusze nieobsługujące ochrony danych w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak obsługiwać scenariusze ochrony danych, w których nie możesz lub nie chcesz używać usługi świadczonej przez iniekcję zależności.
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
uid: security/data-protection/configuration/non-di-scenarios
ms.openlocfilehash: 633548eabe38508ff0cc76ae880220e1dac9b414
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020122"
---
# <a name="non-di-aware-scenarios-for-data-protection-in-aspnet-core"></a>Scenariusze nieobsługujące ochrony danych w ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

System ochrony danych ASP.NET Core jest zwykle [dodawany do kontenera usługi](xref:security/data-protection/consumer-apis/overview) i zużywany przez składniki zależne za pośrednictwem iniekcji zależności (di). Istnieją jednak przypadki, w których nie jest to możliwe, zwłaszcza podczas importowania systemu do istniejącej aplikacji.

Aby można było obsługiwać te scenariusze, pakiet [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) oferuje konkretny typ [DataProtectionProvider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider), który oferuje prosty sposób używania ochrony danych bez polegania na di. `DataProtectionProvider`Typ implementuje [IDataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionprovider). Konstruowanie `DataProtectionProvider` wymaga wystąpienia [DirectoryInfo](/dotnet/api/system.io.directoryinfo) , aby wskazać, gdzie mają być przechowywane klucze kryptograficzne dostawcy, jak pokazano w następującym przykładzie kodu:

[!code-csharp[](non-di-scenarios/_static/nodisample1.cs)]

Domyślnie `DataProtectionProvider` konkretny typ nie szyfruje surowych surowców przed utrwaleniem go w systemie plików. Ma to na celu obsługę scenariuszy, w których deweloper wskazuje udział sieciowy, a system ochrony danych nie może automatycznie ustalić odpowiedniego mechanizmu szyfrowania klucza w czasie spoczynku.

Ponadto `DataProtectionProvider` konkretny typ nie domyślnie [izoluje aplikacje](xref:security/data-protection/configuration/overview#per-application-isolation) . Wszystkie aplikacje korzystające z tego samego katalogu kluczy mogą udostępniać ładunki, o ile ich [Parametry przeznaczenia](xref:security/data-protection/consumer-apis/purpose-strings) są zgodne.

Konstruktor [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) akceptuje opcjonalne wywołanie zwrotne konfiguracji, którego można użyć do dostosowania zachowań systemu. Poniższy przykład ilustruje przywracanie izolacji z jawnym wywołaniem metody [Setapplicationname](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname). Przykład ilustruje również Konfigurowanie systemu do automatycznego szyfrowania utrwalonych kluczy przy użyciu funkcji DPAPI systemu Windows. Jeśli katalog wskazuje udział UNC, możesz chcieć rozpowszechnić certyfikat współużytkowany na wszystkich odpowiednich maszynach i skonfigurować system do używania szyfrowania opartego na certyfikatach z wywołaniem do [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate).

[!code-csharp[](non-di-scenarios/_static/nodisample2.cs)]

> [!TIP]
> Wystąpienia `DataProtectionProvider` konkretnego typu są kosztowne do utworzenia. Jeśli aplikacja obsługuje wiele wystąpień tego typu i jeśli są one używane w tym samym katalogu magazynu kluczy, wydajność aplikacji może ulec obniżeniu. Jeśli używasz `DataProtectionProvider` typu, zalecamy utworzenie tego typu raz i wielokrotne użycie go. `DataProtectionProvider`Wszystkie wystąpienia [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) utworzone na podstawie tego typu są bezpieczne wątkowo dla wielu wywołań.
