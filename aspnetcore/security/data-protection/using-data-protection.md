---
title: Wprowadzenie do interfejsów API ochrony danych w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać interfejsów API ochrony danych ASP.NET Core do ochrony i nieochrony danych w aplikacji.
ms.author: riande
ms.date: 11/12/2019
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
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 1f0d42a7b12edb870481024372d75cdc9e57be21
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051658"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a><span data-ttu-id="b6a71-103">Wprowadzenie do interfejsów API ochrony danych w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b6a71-103">Get started with the Data Protection APIs in ASP.NET Core</span></span>

<a name="security-data-protection-getting-started"></a>

<span data-ttu-id="b6a71-104">W najprostszym zakresie Ochrona danych obejmuje następujące kroki:</span><span class="sxs-lookup"><span data-stu-id="b6a71-104">At its simplest, protecting data consists of the following steps:</span></span>

1. <span data-ttu-id="b6a71-105">Utwórz funkcję ochrony danych z poziomu dostawcy ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="b6a71-105">Create a data protector from a data protection provider.</span></span>

2. <span data-ttu-id="b6a71-106">Wywołaj `Protect` metodę z danymi, które chcesz chronić.</span><span class="sxs-lookup"><span data-stu-id="b6a71-106">Call the `Protect` method with the data you want to protect.</span></span>

3. <span data-ttu-id="b6a71-107">Wywołaj `Unprotect` metodę z danymi, które chcesz wrócić do zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="b6a71-107">Call the `Unprotect` method with the data you want to turn back into plain text.</span></span>

<span data-ttu-id="b6a71-108">Większość platform i modeli aplikacji, takich jak ASP.NET Core lub SignalR , już skonfigurowano system ochrony danych i dodaje go do kontenera usługi, do którego uzyskuje dostęp za pośrednictwem iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="b6a71-108">Most frameworks and app models, such as ASP.NET Core or SignalR, already configure the data protection system and add it to a service container you access via dependency injection.</span></span> <span data-ttu-id="b6a71-109">Poniższy przykład demonstruje skonfigurowanie kontenera usługi pod kątem iniekcji zależności i rejestrowanie stosu ochrony danych, otrzymywanie dostawcy ochrony danych za pośrednictwem programu DI, utworzenie funkcji ochrony i ochronę danych.</span><span class="sxs-lookup"><span data-stu-id="b6a71-109">The following sample demonstrates configuring a service container for dependency injection and registering the data protection stack, receiving the data protection provider via DI, creating a protector and protecting then unprotecting data.</span></span>

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

<span data-ttu-id="b6a71-110">Podczas tworzenia funkcji ochrony należy podać co najmniej jeden [ciąg przeznaczenia](xref:security/data-protection/consumer-apis/purpose-strings).</span><span class="sxs-lookup"><span data-stu-id="b6a71-110">When you create a protector you must provide one or more [Purpose Strings](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="b6a71-111">Ciąg celu zapewnia izolację między użytkownikami.</span><span class="sxs-lookup"><span data-stu-id="b6a71-111">A purpose string provides isolation between consumers.</span></span> <span data-ttu-id="b6a71-112">Na przykład funkcja ochrony utworzona z przeznaczeniem ciągu "Green" nie może wyłączyć ochrony danych dostarczonych przez funkcję ochrony w celu "purpurowego".</span><span class="sxs-lookup"><span data-stu-id="b6a71-112">For example, a protector created with a purpose string of "green" wouldn't be able to unprotect data provided by a protector with a purpose of "purple".</span></span>

>[!TIP]
> <span data-ttu-id="b6a71-113">Wystąpienia `IDataProtectionProvider` i `IDataProtector` są bezpieczne dla wątków dla wielu wywołań.</span><span class="sxs-lookup"><span data-stu-id="b6a71-113">Instances of `IDataProtectionProvider` and `IDataProtector` are thread-safe for multiple callers.</span></span> <span data-ttu-id="b6a71-114">Jest to zamierzone, gdy składnik pobiera odwołanie do elementu `IDataProtector` za pośrednictwem wywołania do `CreateProtector` , będzie używać tego odwołania dla wielu wywołań do `Protect` i `Unprotect` .</span><span class="sxs-lookup"><span data-stu-id="b6a71-114">It's intended that once a component gets a reference to an `IDataProtector` via a call to `CreateProtector`, it will use that reference for multiple calls to `Protect` and `Unprotect`.</span></span>
>
><span data-ttu-id="b6a71-115">Wywołanie będzie zgłaszać `Unprotect` CryptographicException, jeśli nie można zweryfikować ani deszyfrowanie chronionego ładunku.</span><span class="sxs-lookup"><span data-stu-id="b6a71-115">A call to `Unprotect` will throw CryptographicException if the protected payload cannot be verified or deciphered.</span></span> <span data-ttu-id="b6a71-116">Niektóre składniki mogą chcieć zignorować błędy podczas operacji usunięcia ochrony; składnik, który odczytuje uwierzytelnianie cookie s, może obsłużyć ten błąd i traktować żądanie tak, jakby nie było cookie w ogóle kończyć się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="b6a71-116">Some components may wish to ignore errors during unprotect operations; a component which reads authentication cookies might handle this error and treat the request as if it had no cookie at all rather than fail the request outright.</span></span> <span data-ttu-id="b6a71-117">Składniki, które chcą tego zachowania, powinny zwrócić uwagę na CryptographicException zamiast połknięcia wszystkich wyjątków.</span><span class="sxs-lookup"><span data-stu-id="b6a71-117">Components which want this behavior should specifically catch CryptographicException instead of swallowing all exceptions.</span></span>
