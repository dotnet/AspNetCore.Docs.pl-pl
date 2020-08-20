---
title: Ciągi przeznaczenie w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak ciągi przeznaczenia są używane w interfejsach API ochrony danych ASP.NET Core.
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
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 1119c45570338f629a3ab7adbd43361529aa23e7
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626925"
---
# <a name="purpose-strings-in-aspnet-core"></a><span data-ttu-id="c778d-103">Ciągi przeznaczenie w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c778d-103">Purpose strings in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-purposes"></a>

<span data-ttu-id="c778d-104">Składniki, które zużywają, `IDataProtectionProvider` muszą przekazać do metody unikatowy parametr *celów* `CreateProtector` .</span><span class="sxs-lookup"><span data-stu-id="c778d-104">Components which consume `IDataProtectionProvider` must pass a unique *purposes* parameter to the `CreateProtector` method.</span></span> <span data-ttu-id="c778d-105">*Parametr* cele jest związany z bezpieczeństwem systemu ochrony danych, ponieważ zapewnia on izolację między konsumentami kryptograficznymi, nawet jeśli główne klucze kryptograficzne są takie same.</span><span class="sxs-lookup"><span data-stu-id="c778d-105">The purposes *parameter* is inherent to the security of the data protection system, as it provides isolation between cryptographic consumers, even if the root cryptographic keys are the same.</span></span>

<span data-ttu-id="c778d-106">Gdy odbiorca określi przeznaczenie, ciąg celu jest używany razem z głównymi kluczami kryptograficznymi w celu uzyskania kluczy kryptograficznych, które są unikatowe dla tego klienta.</span><span class="sxs-lookup"><span data-stu-id="c778d-106">When a consumer specifies a purpose, the purpose string is used along with the root cryptographic keys to derive cryptographic subkeys unique to that consumer.</span></span> <span data-ttu-id="c778d-107">Spowoduje to oddzielenie konsumenta od wszystkich innych użytkowników kryptograficznych w aplikacji: żaden inny składnik nie może odczytywać swoich ładunków i nie może odczytać żadnych innych ładunków składnika.</span><span class="sxs-lookup"><span data-stu-id="c778d-107">This isolates the consumer from all other cryptographic consumers in the application: no other component can read its payloads, and it cannot read any other component's payloads.</span></span> <span data-ttu-id="c778d-108">Ta izolacja również renderuje w sposób niewykonalny cały poziom ataku na składnik.</span><span class="sxs-lookup"><span data-stu-id="c778d-108">This isolation also renders infeasible entire categories of attack against the component.</span></span>

![Przykład diagramu przeznaczenie](purpose-strings/_static/purposes.png)

<span data-ttu-id="c778d-110">Na powyższym diagramie `IDataProtector` wystąpienia a i B **nie mogą** odczytywać wszystkich ładunków, tylko ich własne.</span><span class="sxs-lookup"><span data-stu-id="c778d-110">In the diagram above, `IDataProtector` instances A and B **cannot** read each other's payloads, only their own.</span></span>

<span data-ttu-id="c778d-111">Ciąg celu nie musi być tajny.</span><span class="sxs-lookup"><span data-stu-id="c778d-111">The purpose string doesn't have to be secret.</span></span> <span data-ttu-id="c778d-112">Powinien być po prostu unikatowy w sensie, że żaden inny dobrze działający składnik nie będzie miał tego samego ciągu celu.</span><span class="sxs-lookup"><span data-stu-id="c778d-112">It should simply be unique in the sense that no other well-behaved component will ever provide the same purpose string.</span></span>

>[!TIP]
> <span data-ttu-id="c778d-113">Użycie przestrzeni nazw i nazwy typu składnika korzystającego z interfejsów API ochrony danych jest dobrą regułą kciuka, jak w przypadku te informacje nigdy nie będą powodować konfliktów.</span><span class="sxs-lookup"><span data-stu-id="c778d-113">Using the namespace and type name of the component consuming the data protection APIs is a good rule of thumb, as in practice this information will never conflict.</span></span>
>
><span data-ttu-id="c778d-114">Składnik firmy Contoso, który jest odpowiedzialny za tokeny okaziciela minting, może używać contoso. Security. BearerToken jako ciągu celu.</span><span class="sxs-lookup"><span data-stu-id="c778d-114">A Contoso-authored component which is responsible for minting bearer tokens might use Contoso.Security.BearerToken as its purpose string.</span></span> <span data-ttu-id="c778d-115">Lub — jeszcze lepsze — może używać contoso. Security. BearerToken. v1 jako ciągu celu.</span><span class="sxs-lookup"><span data-stu-id="c778d-115">Or - even better - it might use Contoso.Security.BearerToken.v1 as its purpose string.</span></span> <span data-ttu-id="c778d-116">Dołączenie numeru wersji umożliwia użycie w przyszłości firmy Contoso. Security. BearerToken. v2, a różne wersje są całkowicie odizolowane od siebie.</span><span class="sxs-lookup"><span data-stu-id="c778d-116">Appending the version number allows a future version to use Contoso.Security.BearerToken.v2 as its purpose, and the different versions would be completely isolated from one another as far as payloads go.</span></span>

<span data-ttu-id="c778d-117">Ponieważ parametr cele `CreateProtector` jest tablicą ciągów, zamiast tego można określić powyższe polecenie jako `[ "Contoso.Security.BearerToken", "v1" ]` .</span><span class="sxs-lookup"><span data-stu-id="c778d-117">Since the purposes parameter to `CreateProtector` is a string array, the above could've been instead specified as `[ "Contoso.Security.BearerToken", "v1" ]`.</span></span> <span data-ttu-id="c778d-118">Pozwala to na utworzenie hierarchii celów i otwarcie możliwości scenariuszy wielodostępnych z systemem ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="c778d-118">This allows establishing a hierarchy of purposes and opens up the possibility of multi-tenancy scenarios with the data protection system.</span></span>

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> <span data-ttu-id="c778d-119">Składniki nie powinny zezwalać na dostęp niezaufanych użytkowników jako jedyne źródło danych wejściowych dla łańcucha celów.</span><span class="sxs-lookup"><span data-stu-id="c778d-119">Components shouldn't allow untrusted user input to be the sole source of input for the purposes chain.</span></span>
>
><span data-ttu-id="c778d-120">Rozważmy na przykład składnik contoso. Messaging. SecureMessage, który jest odpowiedzialny za przechowywanie bezpiecznych komunikatów.</span><span class="sxs-lookup"><span data-stu-id="c778d-120">For example, consider a component Contoso.Messaging.SecureMessage which is responsible for storing secure messages.</span></span> <span data-ttu-id="c778d-121">Jeśli składnik zabezpieczonych komunikatów był wywoływany `CreateProtector([ username ])` , złośliwy użytkownik może utworzyć konto o nazwie "contoso. Security. BearerToken" w celu uzyskania składnika do wywołania `CreateProtector([ "Contoso.Security.BearerToken" ])` , a tym samym przypadkowo spowodować, że bezpieczny system obsługi komunikatów mennic ładunki, które mogą być postrzegane jako tokeny uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="c778d-121">If the secure messaging component were to call `CreateProtector([ username ])`, then a malicious user might create an account with username "Contoso.Security.BearerToken" in an attempt to get the component to call `CreateProtector([ "Contoso.Security.BearerToken" ])`, thus inadvertently causing the secure messaging system to mint payloads that could be perceived as authentication tokens.</span></span>
>
><span data-ttu-id="c778d-122">Lepszym łańcuchem zastosowań dla składnika obsługi komunikatów jest `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])` , który zapewnia właściwą izolację.</span><span class="sxs-lookup"><span data-stu-id="c778d-122">A better purposes chain for the messaging component would be `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`, which provides proper isolation.</span></span>

<span data-ttu-id="c778d-123">Izolacja zapewniana przez program oraz zachowania `IDataProtectionProvider` , `IDataProtector` i są następujące:</span><span class="sxs-lookup"><span data-stu-id="c778d-123">The isolation provided by and behaviors of `IDataProtectionProvider`, `IDataProtector`, and purposes are as follows:</span></span>

* <span data-ttu-id="c778d-124">Dla danego `IDataProtectionProvider` obiektu `CreateProtector` Metoda utworzy `IDataProtector` obiekt jednoznacznie powiązany zarówno z `IDataProtectionProvider` obiektem, który go utworzył, jak i parametrem cele, który został przekazany do metody.</span><span class="sxs-lookup"><span data-stu-id="c778d-124">For a given `IDataProtectionProvider` object, the `CreateProtector` method will create an `IDataProtector` object uniquely tied to both the `IDataProtectionProvider` object which created it and the purposes parameter which was passed into the method.</span></span>

* <span data-ttu-id="c778d-125">Parametr celu nie może mieć wartości null.</span><span class="sxs-lookup"><span data-stu-id="c778d-125">The purpose parameter must not be null.</span></span> <span data-ttu-id="c778d-126">(Jeśli cele są określone jako tablica, oznacza to, że tablica nie może mieć zerowej długości, a wszystkie elementy tablicy muszą być inne niż null.) Pusty cel ciągu jest technicznie dozwolony, ale nie jest odradzany.</span><span class="sxs-lookup"><span data-stu-id="c778d-126">(If purposes is specified as an array, this means that the array must not be of zero length and all elements of the array must be non-null.) An empty string purpose is technically allowed but is discouraged.</span></span>

* <span data-ttu-id="c778d-127">Dwa cele argumentów są równoważne, jeśli i tylko wtedy, gdy zawierają te same ciągi (przy użyciu porównującej porządkowej) w tej samej kolejności.</span><span class="sxs-lookup"><span data-stu-id="c778d-127">Two purposes arguments are equivalent if and only if they contain the same strings (using an ordinal comparer) in the same order.</span></span> <span data-ttu-id="c778d-128">Argument pojedynczego celu jest równoważny z odpowiadającą mu tablicą celów pojedynczego elementu.</span><span class="sxs-lookup"><span data-stu-id="c778d-128">A single purpose argument is equivalent to the corresponding single-element purposes array.</span></span>

* <span data-ttu-id="c778d-129">Dwa `IDataProtector` obiekty są równoważne w przypadku i tylko wtedy, gdy są tworzone z obiektów równoważnych `IDataProtectionProvider` z parametrami równoważnych celów.</span><span class="sxs-lookup"><span data-stu-id="c778d-129">Two `IDataProtector` objects are equivalent if and only if they're created from equivalent `IDataProtectionProvider` objects with equivalent purposes parameters.</span></span>

* <span data-ttu-id="c778d-130">Dla danego `IDataProtector` obiektu wywołanie zwróci `Unprotect(protectedData)` oryginalny element `unprotectedData` if i tylko wtedy, gdy `protectedData := Protect(unprotectedData)` dla obiektu równoważnego `IDataProtector` .</span><span class="sxs-lookup"><span data-stu-id="c778d-130">For a given `IDataProtector` object, a call to `Unprotect(protectedData)` will return the original `unprotectedData` if and only if `protectedData := Protect(unprotectedData)` for an equivalent `IDataProtector` object.</span></span>

> [!NOTE]
> <span data-ttu-id="c778d-131">Nie rozważamy przypadków, w których jakiś składnik celowo wybiera ciąg celu, który jest znany w konflikcie z innym składnikiem.</span><span class="sxs-lookup"><span data-stu-id="c778d-131">We're not considering the case where some component intentionally chooses a purpose string which is known to conflict with another component.</span></span> <span data-ttu-id="c778d-132">Taki składnik powinien być uważany za złośliwy i ten system nie jest przeznaczony do zapewnienia gwarancji bezpieczeństwa w przypadku, gdy złośliwy kod jest już uruchomiony w procesie roboczym.</span><span class="sxs-lookup"><span data-stu-id="c778d-132">Such a component would essentially be considered malicious, and this system isn't intended to provide security guarantees in the event that malicious code is already running inside of the worker process.</span></span>
