---
title: Wyłącz ochronę ładunków, których klucze zostały odwołane w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak wyłączyć ochronę danych, a następnie chronić klucze, które zostały odwołane w aplikacji ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 5f13b53182f720ac8b58d90701561d381981308a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051099"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a><span data-ttu-id="ce1a9-103">Wyłącz ochronę ładunków, których klucze zostały odwołane w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ce1a9-103">Unprotect payloads whose keys have been revoked in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

<span data-ttu-id="ce1a9-104">Interfejsy API ochrony danych ASP.NET Core nie są przede wszystkim przeznaczone do nieograniczonego trwałości poufnych ładunków.</span><span class="sxs-lookup"><span data-stu-id="ce1a9-104">The ASP.NET Core data protection APIs are not primarily intended for indefinite persistence of confidential payloads.</span></span> <span data-ttu-id="ce1a9-105">Inne technologie, takie jak [Windows CNG DPAPI](/windows/win32/seccng/cng-dpapi) i [Azure Rights Management](/rights-management/) , są bardziej odpowiednie dla scenariusza nieograniczonego magazynu i mają odpowiadające im silne możliwości zarządzania kluczami.</span><span class="sxs-lookup"><span data-stu-id="ce1a9-105">Other technologies like [Windows CNG DPAPI](/windows/win32/seccng/cng-dpapi) and [Azure Rights Management](/rights-management/) are more suited to the scenario of indefinite storage, and they have correspondingly strong key management capabilities.</span></span> <span data-ttu-id="ce1a9-106">Oznacza to, że nie ma żadnych zakazów używania ASP.NET Core interfejsów API ochrony danych do długoterminowej ochrony poufnych danych.</span><span class="sxs-lookup"><span data-stu-id="ce1a9-106">That said, there's nothing prohibiting a developer from using the ASP.NET Core data protection APIs for long-term protection of confidential data.</span></span> <span data-ttu-id="ce1a9-107">Klucze nigdy nie są usuwane z pierścienia kluczy, więc `IDataProtector.Unprotect` zawsze mogą odzyskiwać istniejące ładunki, o ile klucze są dostępne i prawidłowe.</span><span class="sxs-lookup"><span data-stu-id="ce1a9-107">Keys are never removed from the key ring, so `IDataProtector.Unprotect` can always recover existing payloads as long as the keys are available and valid.</span></span>

<span data-ttu-id="ce1a9-108">Występuje jednak problem polegający na tym, że deweloper próbuje wyłączyć ochronę danych chronionych za pomocą odwołanego klucza, co `IDataProtector.Unprotect` spowoduje zgłoszenie wyjątku w tym przypadku.</span><span class="sxs-lookup"><span data-stu-id="ce1a9-108">However, an issue arises when the developer tries to unprotect data that has been protected with a revoked key, as `IDataProtector.Unprotect` will throw an exception in this case.</span></span> <span data-ttu-id="ce1a9-109">Może to być korzystne w przypadku ładunków krótkoterminowych lub przejściowych (takich jak tokeny uwierzytelniania), ponieważ te rodzaje ładunków mogą być w łatwy sposób odtworzone przez system, a w najgorszym miejscu może być wymagane zalogowanie się do osoby odwiedzającej.</span><span class="sxs-lookup"><span data-stu-id="ce1a9-109">This might be fine for short-lived or transient payloads (like authentication tokens), as these kinds of payloads can easily be recreated by the system, and at worst the site visitor might be required to log in again.</span></span> <span data-ttu-id="ce1a9-110">Jednak w przypadku utrwalonych ładunków, których `Unprotect` Zgłoszenie może prowadzić do niedopuszczalnej utraty danych.</span><span class="sxs-lookup"><span data-stu-id="ce1a9-110">But for persisted payloads, having `Unprotect` throw could lead to unacceptable data loss.</span></span>

## <a name="ipersisteddataprotector"></a><span data-ttu-id="ce1a9-111">IPersistedDataProtector</span><span class="sxs-lookup"><span data-stu-id="ce1a9-111">IPersistedDataProtector</span></span>

<span data-ttu-id="ce1a9-112">Aby obsłużyć scenariusz zezwalania na nieochrony ładunków, nawet w przypadku wycofanych kluczy, system ochrony danych zawiera `IPersistedDataProtector` Typ.</span><span class="sxs-lookup"><span data-stu-id="ce1a9-112">To support the scenario of allowing payloads to be unprotected even in the face of revoked keys, the data protection system contains an `IPersistedDataProtector` type.</span></span> <span data-ttu-id="ce1a9-113">Aby uzyskać wystąpienie elementu `IPersistedDataProtector` , wystarczy uzyskać wystąpienie `IDataProtector` w normalny sposób i spróbować rzutować `IDataProtector` do `IPersistedDataProtector` .</span><span class="sxs-lookup"><span data-stu-id="ce1a9-113">To get an instance of `IPersistedDataProtector`, simply get an instance of `IDataProtector` in the normal fashion and try casting the `IDataProtector` to `IPersistedDataProtector`.</span></span>

> [!NOTE]
> <span data-ttu-id="ce1a9-114">Nie wszystkie `IDataProtector` wystąpienia mogą być rzutowane na `IPersistedDataProtector` .</span><span class="sxs-lookup"><span data-stu-id="ce1a9-114">Not all `IDataProtector` instances can be cast to `IPersistedDataProtector`.</span></span> <span data-ttu-id="ce1a9-115">Deweloperzy powinni używać języka C# jako operatora lub podobnego, aby uniknąć wyjątków czasu wykonywania spowodowanych przez nieprawidłowe rzutowania i powinny być przygotowane w celu odpowiedniego obsłużenia przypadku awarii.</span><span class="sxs-lookup"><span data-stu-id="ce1a9-115">Developers should use the C# as operator or similar to avoid runtime exceptions caused by invalid casts, and they should be prepared to handle the failure case appropriately.</span></span>

<span data-ttu-id="ce1a9-116">`IPersistedDataProtector` uwidacznia następującą powierzchnię interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="ce1a9-116">`IPersistedDataProtector` exposes the following API surface:</span></span>

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

<span data-ttu-id="ce1a9-117">Ten interfejs API pobiera chroniony ładunek (jako tablicę bajtową) i zwraca ładunek niechroniony.</span><span class="sxs-lookup"><span data-stu-id="ce1a9-117">This API takes the protected payload (as a byte array) and returns the unprotected payload.</span></span> <span data-ttu-id="ce1a9-118">Brak przeciążenia opartego na ciągach.</span><span class="sxs-lookup"><span data-stu-id="ce1a9-118">There's no string-based overload.</span></span> <span data-ttu-id="ce1a9-119">Dwa parametry out są następujące.</span><span class="sxs-lookup"><span data-stu-id="ce1a9-119">The two out parameters are as follows.</span></span>

* <span data-ttu-id="ce1a9-120">`requiresMigration`: zostanie ustawiona na wartość true, jeśli klucz używany do ochrony tego ładunku nie jest już aktywnym kluczem domyślnym, np. klucz używany do ochrony tego ładunku jest stary i przeprowadzono kluczową operację wycofywania.</span><span class="sxs-lookup"><span data-stu-id="ce1a9-120">`requiresMigration`: will be set to true if the key used to protect this payload is no longer the active default key, e.g., the key used to protect this payload is old and a key rolling operation has since taken place.</span></span> <span data-ttu-id="ce1a9-121">Obiekt wywołujący może chcieć rozważyć ponowne włączenie ochrony ładunku w zależności od potrzeb firmy.</span><span class="sxs-lookup"><span data-stu-id="ce1a9-121">The caller may wish to consider reprotecting the payload depending on their business needs.</span></span>

* <span data-ttu-id="ce1a9-122">`wasRevoked`: zostanie ustawiona na wartość true, jeśli klucz używany do ochrony tego ładunku został odwołany.</span><span class="sxs-lookup"><span data-stu-id="ce1a9-122">`wasRevoked`: will be set to true if the key used to protect this payload was revoked.</span></span>

>[!WARNING]
> <span data-ttu-id="ce1a9-123">Podczas przekazywania do metody należy zachować szczególną ostrożność `ignoreRevocationErrors: true` `DangerousUnprotect` .</span><span class="sxs-lookup"><span data-stu-id="ce1a9-123">Exercise extreme caution when passing `ignoreRevocationErrors: true` to the `DangerousUnprotect` method.</span></span> <span data-ttu-id="ce1a9-124">Jeśli po wywołaniu tej metody `wasRevoked` wartość jest równa true, klucz używany do ochrony tego ładunku został odwołany, a autentyczność ładunku powinna być traktowana jako podejrzana.</span><span class="sxs-lookup"><span data-stu-id="ce1a9-124">If after calling this method the `wasRevoked` value is true, then the key used to protect this payload was revoked, and the payload's authenticity should be treated as suspect.</span></span> <span data-ttu-id="ce1a9-125">W takim przypadku Kontynuuj działanie w niechronionym ładunku, jeśli masz osobną gwarancję, że jest ona autentyczna, np. pochodzi z bezpiecznej bazy danych, a nie jest wysyłana przez niezaufanego klienta sieci Web.</span><span class="sxs-lookup"><span data-stu-id="ce1a9-125">In this case, only continue operating on the unprotected payload if you have some separate assurance that it's authentic, e.g. that it's coming from a secure database rather than being sent by an untrusted web client.</span></span>

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
