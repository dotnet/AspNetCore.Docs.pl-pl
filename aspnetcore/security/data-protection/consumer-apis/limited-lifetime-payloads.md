---
title: Ogranicz okres istnienia chronionych ładunków w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak ograniczyć okres istnienia chronionego ładunku przy użyciu interfejsów API ochrony danych ASP.NET Core.
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
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: c7bc86cd42a725f21cf66187c033376a8c5a9e65
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014831"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a><span data-ttu-id="ee110-103">Ogranicz okres istnienia chronionych ładunków w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ee110-103">Limit the lifetime of protected payloads in ASP.NET Core</span></span>

<span data-ttu-id="ee110-104">Istnieją scenariusze, w których Deweloper aplikacji chce utworzyć chroniony ładunek, który wygaśnie po upływie określonego czasu.</span><span class="sxs-lookup"><span data-stu-id="ee110-104">There are scenarios where the application developer wants to create a protected payload that expires after a set period of time.</span></span> <span data-ttu-id="ee110-105">Na przykład, chroniony ładunek może reprezentować token resetowania hasła, który powinien być prawidłowy tylko przez jedną godzinę.</span><span class="sxs-lookup"><span data-stu-id="ee110-105">For instance, the protected payload might represent a password reset token that should only be valid for one hour.</span></span> <span data-ttu-id="ee110-106">W przypadku deweloperów istnieje możliwość utworzenia własnego formatu ładunku zawierającego osadzoną datę wygaśnięcia, a zaawansowani deweloperzy mogą chcieć to zrobić mimo to, ale w przypadku większości deweloperów, którzy zarządzają tymi wygasami, mogą wzrosnąć żmudnym.</span><span class="sxs-lookup"><span data-stu-id="ee110-106">It's certainly possible for the developer to create their own payload format that contains an embedded expiration date, and advanced developers may wish to do this anyway, but for the majority of developers managing these expirations can grow tedious.</span></span>

<span data-ttu-id="ee110-107">Aby ułatwić naszym użytkownikom deweloperów, pakiet [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) zawiera interfejsy API narzędzi do tworzenia ładunków, które automatycznie wygasną po upływie określonego czasu.</span><span class="sxs-lookup"><span data-stu-id="ee110-107">To make this easier for our developer audience, the package [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contains utility APIs for creating payloads that automatically expire after a set period of time.</span></span> <span data-ttu-id="ee110-108">Te interfejsy API zawieszają się od `ITimeLimitedDataProtector` typu.</span><span class="sxs-lookup"><span data-stu-id="ee110-108">These APIs hang off of the `ITimeLimitedDataProtector` type.</span></span>

## <a name="api-usage"></a><span data-ttu-id="ee110-109">Użycie interfejsu API</span><span class="sxs-lookup"><span data-stu-id="ee110-109">API usage</span></span>

<span data-ttu-id="ee110-110">`ITimeLimitedDataProtector`Interfejs to podstawowy interfejs do ochrony i nieochrony ładunków z ograniczeniami czasowymi/z własnym wygaśnięciem.</span><span class="sxs-lookup"><span data-stu-id="ee110-110">The `ITimeLimitedDataProtector` interface is the core interface for protecting and unprotecting time-limited / self-expiring payloads.</span></span> <span data-ttu-id="ee110-111">Aby utworzyć wystąpienie obiektu, należy `ITimeLimitedDataProtector` najpierw potrzebować wystąpienia regularnego [IDataProtector](xref:security/data-protection/consumer-apis/overview) skonstruowanego z określonym przeznaczeniem.</span><span class="sxs-lookup"><span data-stu-id="ee110-111">To create an instance of an `ITimeLimitedDataProtector`, you'll first need an instance of a regular [IDataProtector](xref:security/data-protection/consumer-apis/overview) constructed with a specific purpose.</span></span> <span data-ttu-id="ee110-112">Gdy `IDataProtector` wystąpienie jest dostępne, wywołaj `IDataProtector.ToTimeLimitedDataProtector` metodę rozszerzenia, aby odzyskać funkcję ochrony z wbudowanymi funkcjami wygaśnięcia.</span><span class="sxs-lookup"><span data-stu-id="ee110-112">Once the `IDataProtector` instance is available, call the `IDataProtector.ToTimeLimitedDataProtector` extension method to get back a protector with built-in expiration capabilities.</span></span>

<span data-ttu-id="ee110-113">`ITimeLimitedDataProtector`udostępnia następujące metody:</span><span class="sxs-lookup"><span data-stu-id="ee110-113">`ITimeLimitedDataProtector` exposes the following API surface and extension methods:</span></span>

* <span data-ttu-id="ee110-114">Funkcja onprotecter (przeznaczenie ciągu): ITimeLimitedDataProtector — ten interfejs API jest podobny do istniejącego `IDataProtectionProvider.CreateProtector` w programie, który może służyć do tworzenia [łańcuchów celów](xref:security/data-protection/consumer-apis/purpose-strings) z poziomu głównej ochrony ograniczonej czasowo.</span><span class="sxs-lookup"><span data-stu-id="ee110-114">CreateProtector(string purpose) : ITimeLimitedDataProtector - This API is similar to the existing `IDataProtectionProvider.CreateProtector` in that it can be used to create [purpose chains](xref:security/data-protection/consumer-apis/purpose-strings) from a root time-limited protector.</span></span>

* <span data-ttu-id="ee110-115">Ochrona (Byte [] — zwykły tekst, wygaśnięcie DateTimeOffset): Byte []</span><span class="sxs-lookup"><span data-stu-id="ee110-115">Protect(byte[] plaintext, DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="ee110-116">Ochrona (Byte [] — zwykły tekst, okres istnienia przedziału czasu): Byte []</span><span class="sxs-lookup"><span data-stu-id="ee110-116">Protect(byte[] plaintext, TimeSpan lifetime) : byte[]</span></span>

* <span data-ttu-id="ee110-117">Chroń (Byte [] zwykły tekst): Byte []</span><span class="sxs-lookup"><span data-stu-id="ee110-117">Protect(byte[] plaintext) : byte[]</span></span>

* <span data-ttu-id="ee110-118">Ochrona (tekst tekstowy, wygaśnięcie DateTimeOffset): ciąg</span><span class="sxs-lookup"><span data-stu-id="ee110-118">Protect(string plaintext, DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="ee110-119">Ochrona (tekst tekstowy, okres istnienia przedziału): ciąg</span><span class="sxs-lookup"><span data-stu-id="ee110-119">Protect(string plaintext, TimeSpan lifetime) : string</span></span>

* <span data-ttu-id="ee110-120">Ochrona (ciąg tekstowy): ciąg</span><span class="sxs-lookup"><span data-stu-id="ee110-120">Protect(string plaintext) : string</span></span>

<span data-ttu-id="ee110-121">Oprócz podstawowych `Protect` metod, które pobierają tylko zwykły tekst, istnieją nowe przeciążenia, które umożliwiają określenie daty wygaśnięcia ładunku.</span><span class="sxs-lookup"><span data-stu-id="ee110-121">In addition to the core `Protect` methods which take only the plaintext, there are new overloads which allow specifying the payload's expiration date.</span></span> <span data-ttu-id="ee110-122">Datę wygaśnięcia można określić jako datę bezwzględną (przez `DateTimeOffset` ) lub jako czas względny (od bieżącego czasu systemowego za pośrednictwem programu `TimeSpan` ).</span><span class="sxs-lookup"><span data-stu-id="ee110-122">The expiration date can be specified as an absolute date (via a `DateTimeOffset`) or as a relative time (from the current system time, via a `TimeSpan`).</span></span> <span data-ttu-id="ee110-123">Jeśli zostanie wywołane Przeciążenie, które nie przyjmuje czasu wygaśnięcia, ładunek jest założono, że nigdy nie wygaśnie.</span><span class="sxs-lookup"><span data-stu-id="ee110-123">If an overload which doesn't take an expiration is called, the payload is assumed never to expire.</span></span>

* <span data-ttu-id="ee110-124">Unprotected (Byte [] protectedData, out — wygaśnięcie DateTimeOffset): Byte []</span><span class="sxs-lookup"><span data-stu-id="ee110-124">Unprotect(byte[] protectedData, out DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="ee110-125">Unprotected (Byte [] protectedData): Byte []</span><span class="sxs-lookup"><span data-stu-id="ee110-125">Unprotect(byte[] protectedData) : byte[]</span></span>

* <span data-ttu-id="ee110-126">Wyłącz ochronę (ciąg protectedData, out — wygaśnięcie DateTimeOffset): ciąg</span><span class="sxs-lookup"><span data-stu-id="ee110-126">Unprotect(string protectedData, out DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="ee110-127">Unprotected (String protectedData): ciąg</span><span class="sxs-lookup"><span data-stu-id="ee110-127">Unprotect(string protectedData) : string</span></span>

<span data-ttu-id="ee110-128">`Unprotect`Metody zwracają oryginalne niechronione dane.</span><span class="sxs-lookup"><span data-stu-id="ee110-128">The `Unprotect` methods return the original unprotected data.</span></span> <span data-ttu-id="ee110-129">Jeśli ładunek jeszcze nie wygasł, bezwzględne wygaśnięcie jest zwracane jako opcjonalny parametr out wraz z oryginalnymi danymi niechronionymi.</span><span class="sxs-lookup"><span data-stu-id="ee110-129">If the payload hasn't yet expired, the absolute expiration is returned as an optional out parameter along with the original unprotected data.</span></span> <span data-ttu-id="ee110-130">Jeśli ładunek wygasł, wszystkie przeciążenia metody unprotected będą generować CryptographicException.</span><span class="sxs-lookup"><span data-stu-id="ee110-130">If the payload is expired, all overloads of the Unprotect method will throw CryptographicException.</span></span>

>[!WARNING]
> <span data-ttu-id="ee110-131">Nie zaleca się używania tych interfejsów API do ochrony ładunków, które wymagają długoterminowej lub nieograniczonej trwałości.</span><span class="sxs-lookup"><span data-stu-id="ee110-131">It's not advised to use these APIs to protect payloads which require long-term or indefinite persistence.</span></span> <span data-ttu-id="ee110-132">"Czy mogę zapewnić trwałe nieodwracalne odzyskanie chronionych ładunków po miesiącu?"</span><span class="sxs-lookup"><span data-stu-id="ee110-132">"Can I afford for the protected payloads to be permanently unrecoverable after a month?"</span></span> <span data-ttu-id="ee110-133">może działać jako dobra zasada dla kciuka; Jeśli odpowiedź nie jest, deweloperzy powinni rozważyć alternatywne interfejsy API.</span><span class="sxs-lookup"><span data-stu-id="ee110-133">can serve as a good rule of thumb; if the answer is no then developers should consider alternative APIs.</span></span>

<span data-ttu-id="ee110-134">W poniższym przykładzie są stosowane [ścieżki kodu inne niż di](xref:security/data-protection/configuration/non-di-scenarios) do tworzenia wystąpienia systemu ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="ee110-134">The sample below uses the [non-DI code paths](xref:security/data-protection/configuration/non-di-scenarios) for instantiating the data protection system.</span></span> <span data-ttu-id="ee110-135">Aby uruchomić ten przykład, upewnij się, że najpierw Dodano odwołanie do pakietu Microsoft. AspNetCore. dataprotection. Extensions.</span><span class="sxs-lookup"><span data-stu-id="ee110-135">To run this sample, ensure that you have first added a reference to the Microsoft.AspNetCore.DataProtection.Extensions package.</span></span>

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
