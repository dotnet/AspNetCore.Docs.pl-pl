---
title: Zarządzanie pamięcią i wzorce w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak pamięć jest zarządzana w ASP.NET Core i jak działa moduł zbierający elementy bezużyteczne (GC).
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: performance/memory
ms.openlocfilehash: b2af9cb567cdb1d7b2d0942601fcc3ebd999a5d9
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440951"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a><span data-ttu-id="8db91-103">Zarządzanie pamięcią i wyrzucanie elementów bezużytecznych (GC) w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8db91-103">Memory management and garbage collection (GC) in ASP.NET Core</span></span>

<span data-ttu-id="8db91-104">Przez [Sébastien Ros](https://github.com/sebastienros) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8db91-104">By [Sébastien Ros](https://github.com/sebastienros) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8db91-105">Zarządzanie pamięcią jest złożone, nawet w zarządzanej ramach, takiej jak .NET.</span><span class="sxs-lookup"><span data-stu-id="8db91-105">Memory management is complex, even in a managed framework like .NET.</span></span> <span data-ttu-id="8db91-106">Analizowanie i rozumienie problemów z pamięcią może być wyzwaniem.</span><span class="sxs-lookup"><span data-stu-id="8db91-106">Analyzing and understanding memory issues can be challenging.</span></span> <span data-ttu-id="8db91-107">W tym artykule:</span><span class="sxs-lookup"><span data-stu-id="8db91-107">This article:</span></span>

* <span data-ttu-id="8db91-108">Był motywowany przez wiele *przeciek pamięci* i *GC nie działa* problemy.</span><span class="sxs-lookup"><span data-stu-id="8db91-108">Was motivated by many *memory leak* and *GC not working* issues.</span></span> <span data-ttu-id="8db91-109">Większość z tych problemów były spowodowane przez nie zrozumienie, jak działa zużycie pamięci w .NET Core, lub nie zrozumienie, jak jest mierzona.</span><span class="sxs-lookup"><span data-stu-id="8db91-109">Most of these issues were caused by not understanding how memory consumption works in .NET Core, or not understanding how it's measured.</span></span>
* <span data-ttu-id="8db91-110">Demonstruje problematyczne użycie pamięci i sugeruje alternatywne podejścia.</span><span class="sxs-lookup"><span data-stu-id="8db91-110">Demonstrates problematic memory use, and suggests alternative approaches.</span></span>

## <a name="how-garbage-collection-gc-works-in-net-core"></a><span data-ttu-id="8db91-111">Jak działa zbieranie elementów bezużytecznych (GC) w .NET Core</span><span class="sxs-lookup"><span data-stu-id="8db91-111">How garbage collection (GC) works in .NET Core</span></span>

<span data-ttu-id="8db91-112">GC przydziela segmenty sterty, gdzie każdy segment jest ciągły zakres pamięci.</span><span class="sxs-lookup"><span data-stu-id="8db91-112">The GC allocates heap segments where each segment is a contiguous range of memory.</span></span> <span data-ttu-id="8db91-113">Obiekty umieszczone w stercie są podzielone na jedną z 3 generacji: 0, 1 lub 2.</span><span class="sxs-lookup"><span data-stu-id="8db91-113">Objects placed in the heap are categorized into one of 3 generations: 0, 1, or 2.</span></span> <span data-ttu-id="8db91-114">Generowanie określa częstotliwość GC próbuje zwolnić pamięć na zarządzanych obiektów, które nie są już odwołuje się przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="8db91-114">The generation determines the frequency the GC attempts to release memory on managed objects that are no longer referenced by the app.</span></span> <span data-ttu-id="8db91-115">Niższe ponumerowane pokolenia są GC'd częściej.</span><span class="sxs-lookup"><span data-stu-id="8db91-115">Lower numbered generations are GC'd more frequently.</span></span>

<span data-ttu-id="8db91-116">Obiekty są przenoszone z pokolenia na pokolenie na podstawie ich okresu istnienia.</span><span class="sxs-lookup"><span data-stu-id="8db91-116">Objects are moved from one generation to another based on their lifetime.</span></span> <span data-ttu-id="8db91-117">Ponieważ obiekty żyją dłużej, są przenoszone do wyższej generacji.</span><span class="sxs-lookup"><span data-stu-id="8db91-117">As objects live longer, they are moved into a higher generation.</span></span> <span data-ttu-id="8db91-118">Jak wspomniano wcześniej, wyższe pokolenia są GC'd rzadziej.</span><span class="sxs-lookup"><span data-stu-id="8db91-118">As mentioned previously, higher generations are GC'd less often.</span></span> <span data-ttu-id="8db91-119">Krótkotrwałe obiekty żyjące zawsze pozostają w generacji 0.</span><span class="sxs-lookup"><span data-stu-id="8db91-119">Short term lived objects always remain in generation 0.</span></span> <span data-ttu-id="8db91-120">Na przykład obiekty, do których odwołuje się okres życia żądania sieci web są krótkotrwałe.</span><span class="sxs-lookup"><span data-stu-id="8db91-120">For example, objects that are referenced during the life of a web request are short lived.</span></span> <span data-ttu-id="8db91-121">[Singletony](xref:fundamentals/dependency-injection#service-lifetimes) na poziomie aplikacji zazwyczaj migrują do generacji 2.</span><span class="sxs-lookup"><span data-stu-id="8db91-121">Application level [singletons](xref:fundamentals/dependency-injection#service-lifetimes) generally migrate to generation 2.</span></span>

<span data-ttu-id="8db91-122">Po uruchomieniu aplikacji ASP.NET Core gc:</span><span class="sxs-lookup"><span data-stu-id="8db91-122">When an ASP.NET Core app starts, the GC:</span></span>

* <span data-ttu-id="8db91-123">Rezerwuje trochę pamięci dla początkowych segmentów sterty.</span><span class="sxs-lookup"><span data-stu-id="8db91-123">Reserves some memory for the initial heap segments.</span></span>
* <span data-ttu-id="8db91-124">Zatwierdza niewielką część pamięci po załadowaniu środowiska wykonawczego.</span><span class="sxs-lookup"><span data-stu-id="8db91-124">Commits a small portion of memory when the runtime is loaded.</span></span>

<span data-ttu-id="8db91-125">Poprzednie alokacje pamięci są wykonywane ze względu na wydajność.</span><span class="sxs-lookup"><span data-stu-id="8db91-125">The preceding memory allocations are done for performance reasons.</span></span> <span data-ttu-id="8db91-126">Korzyści wydajności pochodzi z segmentów sterty w pamięci ciągłej.</span><span class="sxs-lookup"><span data-stu-id="8db91-126">The performance benefit comes from heap segments in contiguous memory.</span></span>

### <a name="call-gccollect"></a><span data-ttu-id="8db91-127">Zadzwoń do GC. Zbierać</span><span class="sxs-lookup"><span data-stu-id="8db91-127">Call GC.Collect</span></span>

<span data-ttu-id="8db91-128">Wywołanie [GC. Zbieraj](xref:System.GC.Collect*) jawnie:</span><span class="sxs-lookup"><span data-stu-id="8db91-128">Calling [GC.Collect](xref:System.GC.Collect*) explicitly:</span></span>

* <span data-ttu-id="8db91-129">**Nie** powinny być wykonywane przez aplikacje ASP.NET core produkcji.</span><span class="sxs-lookup"><span data-stu-id="8db91-129">Should **not** be done by production ASP.NET Core apps.</span></span>
* <span data-ttu-id="8db91-130">Jest to przydatne podczas badania przecieków pamięci.</span><span class="sxs-lookup"><span data-stu-id="8db91-130">Is useful when investigating memory leaks.</span></span>
* <span data-ttu-id="8db91-131">Podczas badania, weryfikuje GC usunął wszystkie zwisające obiekty z pamięci, dzięki czemu można zmierzyć pamięć.</span><span class="sxs-lookup"><span data-stu-id="8db91-131">When investigating, verifies the GC has removed all dangling objects from memory so memory can be measured.</span></span>

## <a name="analyzing-the-memory-usage-of-an-app"></a><span data-ttu-id="8db91-132">Analizowanie użycia pamięci przez aplikację</span><span class="sxs-lookup"><span data-stu-id="8db91-132">Analyzing the memory usage of an app</span></span>

<span data-ttu-id="8db91-133">Dedykowane narzędzia mogą pomóc w analizie użycia pamięci:</span><span class="sxs-lookup"><span data-stu-id="8db91-133">Dedicated tools can help analyzing memory usage:</span></span>

- <span data-ttu-id="8db91-134">Zliczanie odwołań do obiektów</span><span class="sxs-lookup"><span data-stu-id="8db91-134">Counting object references</span></span>
- <span data-ttu-id="8db91-135">Pomiar wpływu GC na wykorzystanie procesora</span><span class="sxs-lookup"><span data-stu-id="8db91-135">Measuring how much impact the GC has on CPU usage</span></span>
- <span data-ttu-id="8db91-136">Pomiar przestrzeni pamięci używanej dla każdej generacji</span><span class="sxs-lookup"><span data-stu-id="8db91-136">Measuring memory space used for each generation</span></span>

<span data-ttu-id="8db91-137">Do analizowania użycia pamięci należy użyć następujących narzędzi:</span><span class="sxs-lookup"><span data-stu-id="8db91-137">Use the following tools to analyze memory usage:</span></span>

* <span data-ttu-id="8db91-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Może być stosowany na maszynach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="8db91-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Can be  used on production machines.</span></span>
* [<span data-ttu-id="8db91-139">Analizowanie użycia pamięci bez debugera programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8db91-139">Analyze memory usage without the Visual Studio debugger</span></span>](/visualstudio/profiling/memory-usage-without-debugging2)
* [<span data-ttu-id="8db91-140">Użycie pamięci profilu w programie Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8db91-140">Profile memory usage in Visual Studio</span></span>](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a><span data-ttu-id="8db91-141">Wykrywanie problemów z pamięcią</span><span class="sxs-lookup"><span data-stu-id="8db91-141">Detecting memory issues</span></span>

<span data-ttu-id="8db91-142">Menedżer zadań może służyć do zorientowania się, ile pamięci ASP.NET aplikacja używa.</span><span class="sxs-lookup"><span data-stu-id="8db91-142">Task Manager can be used to get an idea of how much memory an ASP.NET app is using.</span></span> <span data-ttu-id="8db91-143">Wartość pamięci Menedżera zadań:</span><span class="sxs-lookup"><span data-stu-id="8db91-143">The Task Manager memory value:</span></span>

* <span data-ttu-id="8db91-144">Reprezentuje ilość pamięci, która jest używana przez proces ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="8db91-144">Represents the amount of memory that is used by the ASP.NET process.</span></span>
* <span data-ttu-id="8db91-145">Zawiera żywe obiekty aplikacji i innych konsumentów pamięci, takich jak użycie pamięci natywnej.</span><span class="sxs-lookup"><span data-stu-id="8db91-145">Includes the app's living objects and other memory consumers such as native memory usage.</span></span>

<span data-ttu-id="8db91-146">Jeśli wartość pamięci Menedżera zadań zwiększa się w nieskończoność i nigdy nie spłaszcza się, aplikacja ma przeciek pamięci.</span><span class="sxs-lookup"><span data-stu-id="8db91-146">If the Task Manager memory value increases indefinitely and never flattens out, the app has a memory leak.</span></span> <span data-ttu-id="8db91-147">W poniższych sekcjach zademonstrować i wyjaśnić kilka wzorców użycia pamięci.</span><span class="sxs-lookup"><span data-stu-id="8db91-147">The following sections demonstrate and explain several memory usage patterns.</span></span>

## <a name="sample-display-memory-usage-app"></a><span data-ttu-id="8db91-148">Przykładowa aplikacja do korzystania z pamięci wyświetlacza</span><span class="sxs-lookup"><span data-stu-id="8db91-148">Sample display memory usage app</span></span>

<span data-ttu-id="8db91-149">Przykładowa [aplikacja MemoryLeak](https://github.com/sebastienros/memoryleak) jest dostępna w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="8db91-149">The [MemoryLeak sample app](https://github.com/sebastienros/memoryleak) is available on GitHub.</span></span> <span data-ttu-id="8db91-150">Aplikacja MemoryLeak:</span><span class="sxs-lookup"><span data-stu-id="8db91-150">The MemoryLeak app:</span></span>

* <span data-ttu-id="8db91-151">Zawiera kontroler diagnostyczny, który zbiera pamięć w czasie rzeczywistym i dane GC dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8db91-151">Includes a diagnostic controller that gathers real-time memory and GC data for the app.</span></span>
* <span data-ttu-id="8db91-152">Ma stronę indeksu, która wyświetla pamięć i dane GC.</span><span class="sxs-lookup"><span data-stu-id="8db91-152">Has an Index page that displays the memory and GC data.</span></span> <span data-ttu-id="8db91-153">Strona Indeks jest odświeżana co sekundę.</span><span class="sxs-lookup"><span data-stu-id="8db91-153">The Index page is refreshed every second.</span></span>
* <span data-ttu-id="8db91-154">Zawiera kontroler interfejsu API, który udostępnia różne wzorce obciążenia pamięci.</span><span class="sxs-lookup"><span data-stu-id="8db91-154">Contains an API controller that provides various memory load patterns.</span></span>
* <span data-ttu-id="8db91-155">Nie jest obsługiwanym narzędziem, jednak może służyć do wyświetlania wzorców użycia pamięci aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8db91-155">Is not a supported tool, however, it can be used to display memory usage patterns of ASP.NET Core apps.</span></span>

<span data-ttu-id="8db91-156">Uruchom MemoryLeak.</span><span class="sxs-lookup"><span data-stu-id="8db91-156">Run MemoryLeak.</span></span> <span data-ttu-id="8db91-157">Przydzielona pamięć powoli zwiększa się, aż wystąpi GC.</span><span class="sxs-lookup"><span data-stu-id="8db91-157">Allocated memory slowly increases until a GC occurs.</span></span> <span data-ttu-id="8db91-158">Pamięć zwiększa, ponieważ narzędzie przydziela obiekt niestandardowy do przechwytywania danych.</span><span class="sxs-lookup"><span data-stu-id="8db91-158">Memory increases because the tool allocates custom object to capture data.</span></span> <span data-ttu-id="8db91-159">Na poniższej ilustracji przedstawiono stronę Indeksu MemoryLeak, gdy występuje Gen 0 GC.</span><span class="sxs-lookup"><span data-stu-id="8db91-159">The following image shows the MemoryLeak Index page when a Gen 0 GC occurs.</span></span> <span data-ttu-id="8db91-160">Wykres pokazuje 0 RPS (Żądania na sekundę), ponieważ nie punkty końcowe interfejsu API z kontrolera interfejsu API zostały wywołane.</span><span class="sxs-lookup"><span data-stu-id="8db91-160">The chart shows 0 RPS (Requests per second) because no API endpoints from the API controller have been called.</span></span>

![poprzedni wykres](memory/_static/0RPS.png)

<span data-ttu-id="8db91-162">Na wykresie są wyświetlane dwie wartości użycia pamięci:</span><span class="sxs-lookup"><span data-stu-id="8db91-162">The chart displays two values for the memory usage:</span></span>

- <span data-ttu-id="8db91-163">Przydzielona: ilość pamięci zajmowanej przez obiekty zarządzane</span><span class="sxs-lookup"><span data-stu-id="8db91-163">Allocated: the amount of memory occupied by managed objects</span></span>
- <span data-ttu-id="8db91-164">[Zestaw roboczy:](/windows/win32/memory/working-set)Zestaw stron w wirtualnej przestrzeni adresowej procesu, które są obecnie rezydentne w pamięci fizycznej.</span><span class="sxs-lookup"><span data-stu-id="8db91-164">[Working set](/windows/win32/memory/working-set): The set of pages in the virtual address space of the process that are currently resident in physical memory.</span></span> <span data-ttu-id="8db91-165">Pokazany zestaw roboczy jest wyświetlaną tą samą wartością Menedżera zadań.</span><span class="sxs-lookup"><span data-stu-id="8db91-165">The working set shown is the same value Task Manager displays.</span></span>

### <a name="transient-objects"></a><span data-ttu-id="8db91-166">Obiekty przejściowe</span><span class="sxs-lookup"><span data-stu-id="8db91-166">Transient objects</span></span>

<span data-ttu-id="8db91-167">Następujący interfejs API tworzy wystąpienie ciągu 10 KB i zwraca je do klienta.</span><span class="sxs-lookup"><span data-stu-id="8db91-167">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="8db91-168">Na każde żądanie nowy obiekt jest przydzielany w pamięci i zapisywany do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="8db91-168">On each request, a new object is allocated in memory and written to the response.</span></span> <span data-ttu-id="8db91-169">Ciągi są przechowywane jako znaki UTF-16 w .NET, więc każdy znak ma 2 bajty w pamięci.</span><span class="sxs-lookup"><span data-stu-id="8db91-169">Strings are stored as UTF-16 characters in .NET so each character takes 2 bytes in memory.</span></span>

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

<span data-ttu-id="8db91-170">Poniższy wykres jest generowany przy stosunkowo małym obciążeniu, aby pokazać, jak alokacje pamięci mają wpływ na GC.</span><span class="sxs-lookup"><span data-stu-id="8db91-170">The following graph is generated with a relatively small load in to show how memory allocations are impacted by the GC.</span></span>

![poprzedni wykres](memory/_static/bigstring.png)

<span data-ttu-id="8db91-172">Poprzedni wykres przedstawia:</span><span class="sxs-lookup"><span data-stu-id="8db91-172">The preceding chart shows:</span></span>

* <span data-ttu-id="8db91-173">4K RPS (żądania na sekundę).</span><span class="sxs-lookup"><span data-stu-id="8db91-173">4K RPS (Requests per second).</span></span>
* <span data-ttu-id="8db91-174">Kolekcje GC generacji 0 występują co około dwie sekundy.</span><span class="sxs-lookup"><span data-stu-id="8db91-174">Generation 0 GC collections occur about every two seconds.</span></span>
* <span data-ttu-id="8db91-175">Zestaw roboczy jest stały na poziomie około 500 MB.</span><span class="sxs-lookup"><span data-stu-id="8db91-175">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="8db91-176">Procesor wynosi 12%.</span><span class="sxs-lookup"><span data-stu-id="8db91-176">CPU is 12%.</span></span>
* <span data-ttu-id="8db91-177">Zużycie i uwalnianie pamięci (przez GC) jest stabilne.</span><span class="sxs-lookup"><span data-stu-id="8db91-177">The memory consumption and release (through GC) is stable.</span></span>

<span data-ttu-id="8db91-178">Poniższy wykres jest przyjmowany przy maksymalnej przepływności, która może być obsługiwana przez maszynę.</span><span class="sxs-lookup"><span data-stu-id="8db91-178">The following chart is taken at the max throughput that can be handled by the machine.</span></span>

![poprzedni wykres](memory/_static/bigstring2.png)

<span data-ttu-id="8db91-180">Poprzedni wykres przedstawia:</span><span class="sxs-lookup"><span data-stu-id="8db91-180">The preceding chart shows:</span></span>

* <span data-ttu-id="8db91-181">22K RPS</span><span class="sxs-lookup"><span data-stu-id="8db91-181">22K RPS</span></span>
* <span data-ttu-id="8db91-182">Kolekcje GC generacji 0 występują kilka razy na sekundę.</span><span class="sxs-lookup"><span data-stu-id="8db91-182">Generation 0 GC collections occur several times per second.</span></span>
* <span data-ttu-id="8db91-183">Kolekcje generacji 1 są wyzwalane, ponieważ aplikacja przydzielona znacznie więcej pamięci na sekundę.</span><span class="sxs-lookup"><span data-stu-id="8db91-183">Generation 1 collections are triggered because the app allocated significantly more memory per second.</span></span>
* <span data-ttu-id="8db91-184">Zestaw roboczy jest stały na poziomie około 500 MB.</span><span class="sxs-lookup"><span data-stu-id="8db91-184">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="8db91-185">Procesor wynosi 33%.</span><span class="sxs-lookup"><span data-stu-id="8db91-185">CPU is 33%.</span></span>
* <span data-ttu-id="8db91-186">Zużycie i uwalnianie pamięci (przez GC) jest stabilne.</span><span class="sxs-lookup"><span data-stu-id="8db91-186">The memory consumption and release (through GC) is stable.</span></span>
* <span data-ttu-id="8db91-187">Procesor (33%) nie jest nadmiernie wykorzystywane, w związku z tym wyrzucania elementów bezużytecznych można nadążyć za dużą liczbę alokacji.</span><span class="sxs-lookup"><span data-stu-id="8db91-187">The CPU (33%) is not over-utilized, therefore the garbage collection can keep up with a high number of allocations.</span></span>

### <a name="workstation-gc-vs-server-gc"></a><span data-ttu-id="8db91-188">Gc stacji roboczej a gc serwera</span><span class="sxs-lookup"><span data-stu-id="8db91-188">Workstation GC vs. Server GC</span></span>

<span data-ttu-id="8db91-189">Moduł zbierający elementy bezużyteczne .NET ma dwa różne tryby:</span><span class="sxs-lookup"><span data-stu-id="8db91-189">The .NET Garbage Collector has two different modes:</span></span>

* <span data-ttu-id="8db91-190">**Stacja robocza GC**: Zoptymalizowana dla pulpitu.</span><span class="sxs-lookup"><span data-stu-id="8db91-190">**Workstation GC**: Optimized for the desktop.</span></span>
* <span data-ttu-id="8db91-191">**Serwer GC**.</span><span class="sxs-lookup"><span data-stu-id="8db91-191">**Server GC**.</span></span> <span data-ttu-id="8db91-192">Domyślny gc dla aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8db91-192">The default GC for ASP.NET Core apps.</span></span> <span data-ttu-id="8db91-193">Zoptymalizowany pod kątem serwera.</span><span class="sxs-lookup"><span data-stu-id="8db91-193">Optimized for the server.</span></span>

<span data-ttu-id="8db91-194">Tryb GC można ustawić jawnie w pliku projektu lub w pliku *runtimeconfig.json* opublikowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8db91-194">The GC mode can be set explicitly in the project file or in the *runtimeconfig.json* file of the published app.</span></span> <span data-ttu-id="8db91-195">Następujące znaczniki pokazuje `ServerGarbageCollection` ustawienie w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="8db91-195">The following markup shows setting `ServerGarbageCollection` in the project file:</span></span>

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

<span data-ttu-id="8db91-196">Zmiana `ServerGarbageCollection` w pliku projektu wymaga przebudowy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8db91-196">Changing `ServerGarbageCollection` in the project file requires the app to be rebuilt.</span></span>

<span data-ttu-id="8db91-197">**Uwaga:** Wyrzucanie elementów bezużytecznych serwera **nie** jest dostępne na komputerach z jednym rdzeniem.</span><span class="sxs-lookup"><span data-stu-id="8db91-197">**Note:** Server garbage collection is **not** available on machines with a single core.</span></span> <span data-ttu-id="8db91-198">Aby uzyskać więcej informacji, zobacz <xref:System.Runtime.GCSettings.IsServerGC>.</span><span class="sxs-lookup"><span data-stu-id="8db91-198">For more information, see <xref:System.Runtime.GCSettings.IsServerGC>.</span></span>

<span data-ttu-id="8db91-199">Na poniższej ilustracji przedstawiono profil pamięci w 5K RPS przy użyciu GC stacji roboczej.</span><span class="sxs-lookup"><span data-stu-id="8db91-199">The following image shows the memory profile under a 5K RPS using the Workstation GC.</span></span>

![poprzedni wykres](memory/_static/workstation.png)

<span data-ttu-id="8db91-201">Różnice między tym wykresem a wersją serwera są znaczące:</span><span class="sxs-lookup"><span data-stu-id="8db91-201">The differences between this chart and the server version are significant:</span></span>

- <span data-ttu-id="8db91-202">Zestaw roboczy spada z 500 MB do 70 MB.</span><span class="sxs-lookup"><span data-stu-id="8db91-202">The working set drops from 500 MB to 70 MB.</span></span>
- <span data-ttu-id="8db91-203">GC wykonuje kolekcje generacji 0 wiele razy na sekundę, a nie co dwie sekundy.</span><span class="sxs-lookup"><span data-stu-id="8db91-203">The GC does generation 0 collections multiple times per second instead of every two seconds.</span></span>
- <span data-ttu-id="8db91-204">GC spada z 300 MB do 10 MB.</span><span class="sxs-lookup"><span data-stu-id="8db91-204">GC drops from 300 MB to 10 MB.</span></span>

<span data-ttu-id="8db91-205">W typowym środowisku serwera sieci web użycie procesora CPU jest ważniejsze niż pamięć, dlatego gc serwera jest lepsze.</span><span class="sxs-lookup"><span data-stu-id="8db91-205">On a typical web server environment, CPU usage is more important than memory, therefore the Server GC is better.</span></span> <span data-ttu-id="8db91-206">Jeśli wykorzystanie pamięci jest wysokie, a użycie procesora CPU jest stosunkowo niskie, GC stacji roboczej może być bardziej wydajne.</span><span class="sxs-lookup"><span data-stu-id="8db91-206">If memory utilization is high and CPU usage is relatively low, the Workstation GC might be more performant.</span></span> <span data-ttu-id="8db91-207">Na przykład o wysokiej gęstości hosting kilka aplikacji sieci web, gdzie pamięć jest ograniczona.</span><span class="sxs-lookup"><span data-stu-id="8db91-207">For example, high density hosting several web apps where memory is scarce.</span></span>

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a><span data-ttu-id="8db91-208">GC przy użyciu platformy Docker i małych kontenerów</span><span class="sxs-lookup"><span data-stu-id="8db91-208">GC using Docker and small containers</span></span>

<span data-ttu-id="8db91-209">Gdy wiele konteneryzowanych aplikacji są uruchomione na jednym komputerze, GC stacji roboczej może być bardziej preformant niż GC serwera.</span><span class="sxs-lookup"><span data-stu-id="8db91-209">When multiple containerized apps are running on one machine, Workstation GC might be more preformant than Server GC.</span></span> <span data-ttu-id="8db91-210">Aby uzyskać więcej informacji, zobacz [Uruchamianie z gc serwera w małym kontenerze](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) i [uruchamianie z gc serwera w scenariuszu małego kontenera część 1 – twardy limit dla sterty GC](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span><span class="sxs-lookup"><span data-stu-id="8db91-210">For more information, see [Running with Server GC in a Small Container](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) and [Running with Server GC in a Small Container Scenario Part 1 – Hard Limit for the GC Heap](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span></span>

### <a name="persistent-object-references"></a><span data-ttu-id="8db91-211">Odwołania do obiektów trwałych</span><span class="sxs-lookup"><span data-stu-id="8db91-211">Persistent object references</span></span>

<span data-ttu-id="8db91-212">GC nie może zwolnić obiektów, do których istnieją odwołania.</span><span class="sxs-lookup"><span data-stu-id="8db91-212">The GC cannot free objects that are referenced.</span></span> <span data-ttu-id="8db91-213">Obiekty, do których istnieją odwołania, ale nie są już potrzebne, powodują przeciek pamięci.</span><span class="sxs-lookup"><span data-stu-id="8db91-213">Objects that are referenced but no longer needed result in a memory leak.</span></span> <span data-ttu-id="8db91-214">Jeśli aplikacja często przydziela obiekty i nie zwalnia ich po ich nie są już potrzebne, użycie pamięci wzrośnie wraz z upływem czasu.</span><span class="sxs-lookup"><span data-stu-id="8db91-214">If the app frequently allocates objects and fails to free them after they are no longer needed, memory usage will increase over time.</span></span>

<span data-ttu-id="8db91-215">Następujący interfejs API tworzy wystąpienie ciągu 10 KB i zwraca je do klienta.</span><span class="sxs-lookup"><span data-stu-id="8db91-215">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="8db91-216">Różnica w poprzednim przykładzie jest to, że to wystąpienie odwołuje się statyczny element członkowski, co oznacza, że nigdy nie jest dostępny dla kolekcji.</span><span class="sxs-lookup"><span data-stu-id="8db91-216">The difference with the previous example is that this instance is referenced by a static member, which means it's never available for collection.</span></span>

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

<span data-ttu-id="8db91-217">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="8db91-217">The preceding code:</span></span>

* <span data-ttu-id="8db91-218">Jest przykładem typowego przecieku pamięci.</span><span class="sxs-lookup"><span data-stu-id="8db91-218">Is an example of a typical memory leak.</span></span>
* <span data-ttu-id="8db91-219">W razie częstych połączeń powoduje zwiększenie pamięci `OutOfMemory` aplikacji, dopóki proces nie ulegnie awarii z wyjątkiem.</span><span class="sxs-lookup"><span data-stu-id="8db91-219">With frequent calls, causes app memory to increases until the process crashes with an `OutOfMemory` exception.</span></span>

![poprzedni wykres](memory/_static/eternal.png)

<span data-ttu-id="8db91-221">Na poprzednim obrazie:</span><span class="sxs-lookup"><span data-stu-id="8db91-221">In the preceding image:</span></span>

* <span data-ttu-id="8db91-222">Testowanie `/api/staticstring` obciążenia punktu końcowego powoduje liniowy wzrost pamięci.</span><span class="sxs-lookup"><span data-stu-id="8db91-222">Load testing the `/api/staticstring` endpoint causes a linear increase in memory.</span></span>
* <span data-ttu-id="8db91-223">GC próbuje zwolnić pamięć w miarę wzrostu ciśnienia pamięci, wywołując kolekcję generacji 2.</span><span class="sxs-lookup"><span data-stu-id="8db91-223">The GC tries to free memory as the memory pressure grows, by calling a generation 2 collection.</span></span>
* <span data-ttu-id="8db91-224">GC nie może zwolnić przeciekły pamięci.</span><span class="sxs-lookup"><span data-stu-id="8db91-224">The GC cannot free the leaked memory.</span></span> <span data-ttu-id="8db91-225">Przydzielony i zestaw roboczy wzrasta z czasem.</span><span class="sxs-lookup"><span data-stu-id="8db91-225">Allocated and working set increase with time.</span></span>

<span data-ttu-id="8db91-226">Niektóre scenariusze, takie jak buforowanie, wymagają, aby odwołania do obiektów były przechowywane, dopóki ciśnienie pamięci nie wymusza ich zwolnienia.</span><span class="sxs-lookup"><span data-stu-id="8db91-226">Some scenarios, such as caching, require object references to be held until memory pressure forces them to be released.</span></span> <span data-ttu-id="8db91-227">Klasa <xref:System.WeakReference> może służyć do tego typu kodu buforowania.</span><span class="sxs-lookup"><span data-stu-id="8db91-227">The <xref:System.WeakReference> class can be used for this type of caching code.</span></span> <span data-ttu-id="8db91-228">Obiekt `WeakReference` jest zbierany pod ciśnieniem pamięci.</span><span class="sxs-lookup"><span data-stu-id="8db91-228">A `WeakReference` object is collected under memory pressures.</span></span> <span data-ttu-id="8db91-229">Domyślna implementacja <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> `WeakReference`zastosowań .</span><span class="sxs-lookup"><span data-stu-id="8db91-229">The default implementation of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> uses `WeakReference`.</span></span>

### <a name="native-memory"></a><span data-ttu-id="8db91-230">Pamięć natywna</span><span class="sxs-lookup"><span data-stu-id="8db91-230">Native memory</span></span>

<span data-ttu-id="8db91-231">Niektóre obiekty .NET Core są zależne od pamięci natywnej.</span><span class="sxs-lookup"><span data-stu-id="8db91-231">Some .NET Core objects rely on native memory.</span></span> <span data-ttu-id="8db91-232">Pamięć natywna **nie** może być zbierana przez GC.</span><span class="sxs-lookup"><span data-stu-id="8db91-232">Native memory can **not** be collected by the GC.</span></span> <span data-ttu-id="8db91-233">Obiekt .NET przy użyciu pamięci natywnej musi zwolnić go przy użyciu kodu macierzystego.</span><span class="sxs-lookup"><span data-stu-id="8db91-233">The .NET object using native memory must free it using native code.</span></span>

<span data-ttu-id="8db91-234">.NET udostępnia <xref:System.IDisposable> interfejs, aby umożliwić deweloperom zwolnienie pamięci natywnej.</span><span class="sxs-lookup"><span data-stu-id="8db91-234">.NET provides the <xref:System.IDisposable> interface to let developers release native memory.</span></span> <span data-ttu-id="8db91-235">Nawet <xref:System.IDisposable.Dispose*> jeśli nie jest wywoływana, `Dispose` poprawnie zaimplementowane klasy wywołać po uruchomieniu [finalizatora.](/dotnet/csharp/programming-guide/classes-and-structs/destructors)</span><span class="sxs-lookup"><span data-stu-id="8db91-235">Even if <xref:System.IDisposable.Dispose*> is not called, correctly implemented classes call `Dispose` when the [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) runs.</span></span>

<span data-ttu-id="8db91-236">Spójrzmy na poniższy kod:</span><span class="sxs-lookup"><span data-stu-id="8db91-236">Consider the following code:</span></span>

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

<span data-ttu-id="8db91-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) jest klasą zarządzaną, więc każde wystąpienie zostanie zebrane na końcu żądania.</span><span class="sxs-lookup"><span data-stu-id="8db91-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) is a managed class, so any instance will be collected at the end of the request.</span></span>

<span data-ttu-id="8db91-238">Na poniższej ilustracji przedstawiono `fileprovider` profil pamięci podczas ciągłego wywoływania interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="8db91-238">The following image shows the memory profile while invoking the `fileprovider` API continuously.</span></span>

![poprzedni wykres](memory/_static/fileprovider.png)

<span data-ttu-id="8db91-240">Na poprzednim wykresie przedstawiono oczywisty problem z implementacją tej klasy, ponieważ stale zwiększa użycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="8db91-240">The preceding chart shows an obvious issue with the implementation of this class, as it keeps increasing memory usage.</span></span> <span data-ttu-id="8db91-241">Jest to znany problem, który jest śledzony w [tym problemie](https://github.com/dotnet/aspnetcore/issues/3110).</span><span class="sxs-lookup"><span data-stu-id="8db91-241">This is a known problem that is being tracked in [this issue](https://github.com/dotnet/aspnetcore/issues/3110).</span></span>

<span data-ttu-id="8db91-242">Ten sam wyciek może się zdarzyć w kodzie użytkownika, przez jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="8db91-242">The same leak could be happen in user code, by one of the following:</span></span>

* <span data-ttu-id="8db91-243">Nie zwalniając klasy poprawnie.</span><span class="sxs-lookup"><span data-stu-id="8db91-243">Not releasing the class correctly.</span></span>
* <span data-ttu-id="8db91-244">Zapominając wywołać `Dispose`metodę obiektów zależnych, które powinny być usuwane.</span><span class="sxs-lookup"><span data-stu-id="8db91-244">Forgetting to invoke the `Dispose`method of the dependent objects that should be disposed.</span></span>

### <a name="large-objects-heap"></a><span data-ttu-id="8db91-245">Sterta dużych obiektów</span><span class="sxs-lookup"><span data-stu-id="8db91-245">Large objects heap</span></span>

<span data-ttu-id="8db91-246">Częste alokacji pamięci/wolne cykle można fragment pamięci, zwłaszcza podczas przydzielania dużych fragmentów pamięci.</span><span class="sxs-lookup"><span data-stu-id="8db91-246">Frequent memory allocation/free cycles can fragment memory, especially when allocating large chunks of memory.</span></span> <span data-ttu-id="8db91-247">Obiekty są przydzielane w ciągłych blokach pamięci.</span><span class="sxs-lookup"><span data-stu-id="8db91-247">Objects are allocated in contiguous blocks of memory.</span></span> <span data-ttu-id="8db91-248">Aby zmniejszyć fragmentację, gdy GC zwalnia pamięć, próbuje ją zdefragmentować.</span><span class="sxs-lookup"><span data-stu-id="8db91-248">To mitigate fragmentation, when the GC frees memory, it trys to defragment it.</span></span> <span data-ttu-id="8db91-249">Proces ten nazywany jest **zagęszczaniem**.</span><span class="sxs-lookup"><span data-stu-id="8db91-249">This process is called **compaction**.</span></span> <span data-ttu-id="8db91-250">Zagęszczanie obejmuje poruszające się obiekty.</span><span class="sxs-lookup"><span data-stu-id="8db91-250">Compaction involves moving objects.</span></span> <span data-ttu-id="8db91-251">Przenoszenie dużych obiektów nakłada karę za wydajność.</span><span class="sxs-lookup"><span data-stu-id="8db91-251">Moving large objects imposes a performance penalty.</span></span> <span data-ttu-id="8db91-252">Z tego powodu GC tworzy specjalną strefę pamięci dla _dużych_ obiektów, o nazwie [sterty dużych obiektów](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span><span class="sxs-lookup"><span data-stu-id="8db91-252">For this reason the GC creates a special memory zone for _large_ objects, called the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span></span> <span data-ttu-id="8db91-253">Obiekty, które są większe niż 85 000 bajtów (około 83 KB) są:</span><span class="sxs-lookup"><span data-stu-id="8db91-253">Objects that are greater than 85,000 bytes (approximately 83 KB) are:</span></span>

* <span data-ttu-id="8db91-254">Umieszczone na LOH.</span><span class="sxs-lookup"><span data-stu-id="8db91-254">Placed on the LOH.</span></span>
* <span data-ttu-id="8db91-255">Nie zagęszczona.</span><span class="sxs-lookup"><span data-stu-id="8db91-255">Not compacted.</span></span>
* <span data-ttu-id="8db91-256">Zebrane podczas generacji 2 GCs.</span><span class="sxs-lookup"><span data-stu-id="8db91-256">Collected during generation 2 GCs.</span></span>

<span data-ttu-id="8db91-257">Gdy LOH jest pełna, GC uruchomi kolekcję generacji 2.</span><span class="sxs-lookup"><span data-stu-id="8db91-257">When the LOH is full, the GC will trigger a generation 2 collection.</span></span> <span data-ttu-id="8db91-258">Kolekcje generacji 2:</span><span class="sxs-lookup"><span data-stu-id="8db91-258">Generation 2 collections:</span></span>

* <span data-ttu-id="8db91-259">Są z natury powolne.</span><span class="sxs-lookup"><span data-stu-id="8db91-259">Are inherently slow.</span></span>
* <span data-ttu-id="8db91-260">Dodatkowo ponieść koszt wyzwalania kolekcji na wszystkich innych pokoleń.</span><span class="sxs-lookup"><span data-stu-id="8db91-260">Additionally incur the cost of triggering a collection on all other generations.</span></span>

<span data-ttu-id="8db91-261">Następujący kod kompaktywa LOH natychmiast:</span><span class="sxs-lookup"><span data-stu-id="8db91-261">The following code compacts the LOH immediately:</span></span>

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<span data-ttu-id="8db91-262">Informacje <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> na temat zagęszczania LOH można znaleźć w informacji na temat zagęszczania.</span><span class="sxs-lookup"><span data-stu-id="8db91-262">See <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> for information on compacting the LOH.</span></span>

<span data-ttu-id="8db91-263">W kontenerach korzystających z programu .NET Core 3.0 lub nowszych LOH jest automatycznie kompaktowany.</span><span class="sxs-lookup"><span data-stu-id="8db91-263">In containers using .NET Core 3.0 and later, the LOH is automatically compacted.</span></span>

<span data-ttu-id="8db91-264">Następujący interfejs API, który ilustruje to zachowanie:</span><span class="sxs-lookup"><span data-stu-id="8db91-264">The following API that illustrates this behavior:</span></span>

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

<span data-ttu-id="8db91-265">Na poniższym wykresie przedstawiono `/api/loh/84975` profil pamięci wywoływania punktu końcowego, przy maksymalnym obciążeniu:</span><span class="sxs-lookup"><span data-stu-id="8db91-265">The following chart shows the memory profile of calling the `/api/loh/84975` endpoint, under maximum load:</span></span>

![poprzedni wykres](memory/_static/loh1.png)

<span data-ttu-id="8db91-267">Na poniższym wykresie przedstawiono `/api/loh/84976` profil pamięci wywoływania punktu końcowego, przydzielając *jeszcze jeden bajt:*</span><span class="sxs-lookup"><span data-stu-id="8db91-267">The following chart shows the memory profile of calling the `/api/loh/84976` endpoint, allocating *just one more byte*:</span></span>

![poprzedni wykres](memory/_static/loh2.png)

<span data-ttu-id="8db91-269">Uwaga: `byte[]` Struktura ma bajty napowietrzne.</span><span class="sxs-lookup"><span data-stu-id="8db91-269">Note: The `byte[]` structure has overhead bytes.</span></span> <span data-ttu-id="8db91-270">Dlatego 84 976 bajtów wyzwala limit 85 000.</span><span class="sxs-lookup"><span data-stu-id="8db91-270">That's why 84,976 bytes triggers the 85,000 limit.</span></span>

<span data-ttu-id="8db91-271">Porównanie dwóch poprzednich wykresów:</span><span class="sxs-lookup"><span data-stu-id="8db91-271">Comparing the two preceding charts:</span></span>

* <span data-ttu-id="8db91-272">Zestaw roboczy jest podobny dla obu scenariuszy, około 450 MB.</span><span class="sxs-lookup"><span data-stu-id="8db91-272">The working set is similar for both scenarios, about 450 MB.</span></span>
* <span data-ttu-id="8db91-273">W obszarze LOH żądań (84,975 bajtów) pokazuje głównie generacji 0 kolekcji.</span><span class="sxs-lookup"><span data-stu-id="8db91-273">The under LOH requests (84,975 bytes) shows mostly generation 0 collections.</span></span>
* <span data-ttu-id="8db91-274">Ponad LOH żądań generowania stałych generacji 2 kolekcje.</span><span class="sxs-lookup"><span data-stu-id="8db91-274">The over LOH requests generate constant generation 2 collections.</span></span> <span data-ttu-id="8db91-275">Kolekcje generacji 2 są drogie.</span><span class="sxs-lookup"><span data-stu-id="8db91-275">Generation 2 collections are expensive.</span></span> <span data-ttu-id="8db91-276">Wymagany jest większy procesor, a przepustowość spada o prawie 50%.</span><span class="sxs-lookup"><span data-stu-id="8db91-276">More CPU is required and throughput drops almost 50%.</span></span>

<span data-ttu-id="8db91-277">Tymczasowe duże obiekty są szczególnie problematyczne, ponieważ powodują gen2 GCs.</span><span class="sxs-lookup"><span data-stu-id="8db91-277">Temporary large objects are particularly problematic because they cause gen2 GCs.</span></span>

<span data-ttu-id="8db91-278">Aby uzyskać maksymalną wydajność, należy zminimalizować użycie dużych obiektów.</span><span class="sxs-lookup"><span data-stu-id="8db91-278">For maximum performance, large object use should be minimized.</span></span> <span data-ttu-id="8db91-279">Jeśli to możliwe, podziel duże obiekty.</span><span class="sxs-lookup"><span data-stu-id="8db91-279">If possible, split up large objects.</span></span> <span data-ttu-id="8db91-280">Na przykład [oprogramowanie pośredniczące buforowania odpowiedzi](xref:performance/caching/response) w ASP.NET Core podzieliło wpisy pamięci podręcznej na bloki o masie mniejszej niż 85 000 bajtów.</span><span class="sxs-lookup"><span data-stu-id="8db91-280">For example, [Response Caching](xref:performance/caching/response) middleware in ASP.NET Core split the cache entries into blocks less than 85,000 bytes.</span></span>

<span data-ttu-id="8db91-281">Poniższe łącza pokazują ASP.NET podstawowe podejście do utrzymywania obiektów w granicach LOH:</span><span class="sxs-lookup"><span data-stu-id="8db91-281">The following links show the ASP.NET Core approach to keeping objects under the LOH limit:</span></span>

* [<span data-ttu-id="8db91-282">ResponseCaching/Streams/StreamUtilities.cs</span><span class="sxs-lookup"><span data-stu-id="8db91-282">ResponseCaching/Streams/StreamUtilities.cs</span></span>](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [<span data-ttu-id="8db91-283">ResponseCaching/MemoryResponseCache.cs</span><span class="sxs-lookup"><span data-stu-id="8db91-283">ResponseCaching/MemoryResponseCache.cs</span></span>](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

<span data-ttu-id="8db91-284">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="8db91-284">For more information, see:</span></span>

* [<span data-ttu-id="8db91-285">Odkryto stertę dużych obiektów</span><span class="sxs-lookup"><span data-stu-id="8db91-285">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="8db91-286">Sterta dużych obiektów</span><span class="sxs-lookup"><span data-stu-id="8db91-286">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a><span data-ttu-id="8db91-287">Funkcja HttpClient</span><span class="sxs-lookup"><span data-stu-id="8db91-287">HttpClient</span></span>

<span data-ttu-id="8db91-288">Niepoprawne <xref:System.Net.Http.HttpClient> użycie może spowodować wyciek zasobów.</span><span class="sxs-lookup"><span data-stu-id="8db91-288">Incorrectly using <xref:System.Net.Http.HttpClient> can result in a resource leak.</span></span> <span data-ttu-id="8db91-289">Zasoby systemowe, takie jak połączenia z bazą danych, gniazda, uchwyty plików itp.:</span><span class="sxs-lookup"><span data-stu-id="8db91-289">System resources, such as database connections, sockets, file handles, etc.:</span></span>

* <span data-ttu-id="8db91-290">Są bardziej rzadkie niż pamięć.</span><span class="sxs-lookup"><span data-stu-id="8db91-290">Are more scarce than memory.</span></span>
* <span data-ttu-id="8db91-291">Są bardziej problematyczne, gdy wycieka niż pamięć.</span><span class="sxs-lookup"><span data-stu-id="8db91-291">Are more problematic when leaked than memory.</span></span>

<span data-ttu-id="8db91-292">Doświadczeni deweloperzy platformy <xref:System.IDisposable.Dispose*> .NET wiedzą, aby wywołać obiekty, które implementują <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="8db91-292">Experienced .NET developers know to call <xref:System.IDisposable.Dispose*> on objects that implement <xref:System.IDisposable>.</span></span> <span data-ttu-id="8db91-293">Nie utylizacja `IDisposable` obiektów, które implementują zazwyczaj powoduje wyciek pamięci lub wyciekły zasoby systemowe.</span><span class="sxs-lookup"><span data-stu-id="8db91-293">Not disposing objects that implement `IDisposable` typically results in leaked memory or leaked system resources.</span></span>

<span data-ttu-id="8db91-294">`HttpClient``IDisposable`narzędzi, ale **nie** powinny być usuwane przy każdym wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="8db91-294">`HttpClient` implements `IDisposable`, but should **not** be disposed on every invocation.</span></span> <span data-ttu-id="8db91-295">Raczej `HttpClient` powinny być ponownie nadużywane.</span><span class="sxs-lookup"><span data-stu-id="8db91-295">Rather, `HttpClient` should be reused.</span></span>

<span data-ttu-id="8db91-296">Następujący punkt końcowy tworzy i `HttpClient` usuwa nowe wystąpienie na każde żądanie:</span><span class="sxs-lookup"><span data-stu-id="8db91-296">The following endpoint creates and disposes a new  `HttpClient` instance on every request:</span></span>

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

<span data-ttu-id="8db91-297">Pod obciążeniem rejestrowane są następujące komunikaty o błędach:</span><span class="sxs-lookup"><span data-stu-id="8db91-297">Under load, the following error messages are logged:</span></span>

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

<span data-ttu-id="8db91-298">Mimo że `HttpClient` wystąpienia są usuwane, rzeczywiste połączenie sieciowe zajmuje trochę czasu, aby zostać zwolnionym przez system operacyjny.</span><span class="sxs-lookup"><span data-stu-id="8db91-298">Even though the `HttpClient` instances are disposed, the actual network connection takes some time to be released by the operating system.</span></span> <span data-ttu-id="8db91-299">Poprzez ciągłe tworzenie nowych połączeń, _występuje wyczerpanie portów._</span><span class="sxs-lookup"><span data-stu-id="8db91-299">By continuously creating new connections, _ports exhaustion_ occurs.</span></span> <span data-ttu-id="8db91-300">Każde połączenie klienta wymaga własnego portu klienta.</span><span class="sxs-lookup"><span data-stu-id="8db91-300">Each client connection requires its own client port.</span></span>

<span data-ttu-id="8db91-301">Jednym ze sposobów zapobiegania wyczerpaniu portów `HttpClient` jest ponowne użycie tego samego wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="8db91-301">One way to prevent port exhaustion is to reuse the same `HttpClient` instance:</span></span>

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

<span data-ttu-id="8db91-302">Wystąpienie `HttpClient` jest zwalniane po zatrzymaniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8db91-302">The `HttpClient` instance is released when the app stops.</span></span> <span data-ttu-id="8db91-303">W tym przykładzie pokazano, że nie każdy jednorazowy zasób powinien być usuwany po każdym użyciu.</span><span class="sxs-lookup"><span data-stu-id="8db91-303">This example shows that not every disposable resource should be disposed after each use.</span></span>

<span data-ttu-id="8db91-304">Zobacz następujące dla lepszego sposobu obsługi `HttpClient` okresu istnienia wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="8db91-304">See the following for a better way to handle the lifetime of an `HttpClient` instance:</span></span>

* [<span data-ttu-id="8db91-305">Zarządzanie protokołem i okresem istnienia httpclient</span><span class="sxs-lookup"><span data-stu-id="8db91-305">HttpClient and lifetime management</span></span>](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [<span data-ttu-id="8db91-306">HttpClient dziennik fabryki</span><span class="sxs-lookup"><span data-stu-id="8db91-306">HTTPClient factory blog</span></span>](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a><span data-ttu-id="8db91-307">Buforowanie obiektów</span><span class="sxs-lookup"><span data-stu-id="8db91-307">Object pooling</span></span>

<span data-ttu-id="8db91-308">W poprzednim przykładzie `HttpClient` pokazano, jak wystąpienie może być statyczne i ponownie przez wszystkie żądania.</span><span class="sxs-lookup"><span data-stu-id="8db91-308">The previous example showed how the `HttpClient` instance can be made static and reused by all requests.</span></span> <span data-ttu-id="8db91-309">Ponowne użycie zapobiega wyczerpaniu zasobów.</span><span class="sxs-lookup"><span data-stu-id="8db91-309">Reuse prevents running out of resources.</span></span>

<span data-ttu-id="8db91-310">Buforowanie obiektów:</span><span class="sxs-lookup"><span data-stu-id="8db91-310">Object pooling:</span></span>

* <span data-ttu-id="8db91-311">Używa wzorca ponownego użycia.</span><span class="sxs-lookup"><span data-stu-id="8db91-311">Uses the reuse pattern.</span></span>
* <span data-ttu-id="8db91-312">Jest przeznaczony dla obiektów, które są kosztowne do utworzenia.</span><span class="sxs-lookup"><span data-stu-id="8db91-312">Is designed for objects that are expensive to create.</span></span>

<span data-ttu-id="8db91-313">Pula jest kolekcją wstępnie zainicjowanych obiektów, które mogą być zarezerwowane i zwalniane między wątkami.</span><span class="sxs-lookup"><span data-stu-id="8db91-313">A pool is a collection of pre-initialized objects that can be reserved and released across threads.</span></span> <span data-ttu-id="8db91-314">Pule mogą definiować reguły alokacji, takie jak limity, wstępnie zdefiniowane rozmiary lub tempo wzrostu.</span><span class="sxs-lookup"><span data-stu-id="8db91-314">Pools can define allocation rules such as limits, predefined sizes, or growth rate.</span></span>

<span data-ttu-id="8db91-315">Pakiet NuGet [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) zawiera klasy, które pomagają zarządzać takimi pulami.</span><span class="sxs-lookup"><span data-stu-id="8db91-315">The NuGet package [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contains classes that help to manage such pools.</span></span>

<span data-ttu-id="8db91-316">Następujący punkt końcowy interfejsu API `byte` wystąpienia buforu, który jest wypełniony liczb losowych na każde żądanie:</span><span class="sxs-lookup"><span data-stu-id="8db91-316">The following API endpoint instantiates a `byte` buffer that is filled with random numbers on each request:</span></span>

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

<span data-ttu-id="8db91-317">Na poniższym wykresie jest wywoływanie poprzedniego interfejsu API z umiarkowanym obciążeniem:</span><span class="sxs-lookup"><span data-stu-id="8db91-317">The following chart display calling the preceding API with moderate load:</span></span>

![poprzedni wykres](memory/_static/array.png)

<span data-ttu-id="8db91-319">Na poprzednim wykresie kolekcje generacji 0 zdarzają się mniej więcej raz na sekundę.</span><span class="sxs-lookup"><span data-stu-id="8db91-319">In the preceding chart, generation 0 collections happen approximately once per second.</span></span>

<span data-ttu-id="8db91-320">Poprzedni kod można zoptymalizować, łącząc `byte` bufor przy użyciu [\<>. ](xref:System.Buffers.ArrayPool`1)</span><span class="sxs-lookup"><span data-stu-id="8db91-320">The preceding code can be optimized by pooling the `byte` buffer by using [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1).</span></span> <span data-ttu-id="8db91-321">Wystąpienie statyczne jest ponownie zażywane w żądaniach.</span><span class="sxs-lookup"><span data-stu-id="8db91-321">A static instance is reused across requests.</span></span>

<span data-ttu-id="8db91-322">Co innego w tym podejściu jest to, że obiekt w puli jest zwracany z interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="8db91-322">What's different with this approach is that a pooled object is returned from the API.</span></span> <span data-ttu-id="8db91-323">Oznacza to:</span><span class="sxs-lookup"><span data-stu-id="8db91-323">That means:</span></span>

* <span data-ttu-id="8db91-324">Obiekt jest poza kontrolą, jak tylko powrócisz z metody.</span><span class="sxs-lookup"><span data-stu-id="8db91-324">The object is out of your control as soon as you return from the method.</span></span>
* <span data-ttu-id="8db91-325">Nie można zwolnić obiektu.</span><span class="sxs-lookup"><span data-stu-id="8db91-325">You can't release the object.</span></span>

<span data-ttu-id="8db91-326">Aby skonfigurować utylizację obiektu,</span><span class="sxs-lookup"><span data-stu-id="8db91-326">To set up disposal of the object:</span></span>

* <span data-ttu-id="8db91-327">Hermetyzuj tablicę zbiorczą w obiekcie jednorazowego użytku.</span><span class="sxs-lookup"><span data-stu-id="8db91-327">Encapsulate the pooled array in a disposable object.</span></span>
* <span data-ttu-id="8db91-328">Zarejestruj obiekt w puli za pomocą [httpcontext.response.registerfordispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span><span class="sxs-lookup"><span data-stu-id="8db91-328">Register the pooled object with [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span></span>

<span data-ttu-id="8db91-329">`RegisterForDispose`zajmie się wywoływaniem `Dispose`obiektu docelowego, tak aby został wydany tylko po zakończeniu żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="8db91-329">`RegisterForDispose` will take care of calling `Dispose`on the target object so that it's only released when the HTTP request is complete.</span></span>

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

<span data-ttu-id="8db91-330">Zastosowanie tego samego obciążenia co wersja nieskładka nieskładnia powoduje następujące wyniki na poniższym wykresie:</span><span class="sxs-lookup"><span data-stu-id="8db91-330">Applying the same load as the non-pooled version results in the following chart:</span></span>

![poprzedni wykres](memory/_static/pooledarray.png)

<span data-ttu-id="8db91-332">Główną różnicą są przydzielane bajty, a w konsekwencji znacznie mniej kolekcji generacji 0.</span><span class="sxs-lookup"><span data-stu-id="8db91-332">The main difference is allocated bytes, and as a consequence much fewer generation 0 collections.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8db91-333">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="8db91-333">Additional resources</span></span>

* [<span data-ttu-id="8db91-334">Wyrzucanie elementów bezużytecznych</span><span class="sxs-lookup"><span data-stu-id="8db91-334">Garbage Collection</span></span>](/dotnet/standard/garbage-collection/)
* [<span data-ttu-id="8db91-335">Opis różnych trybów GC za pomocą wizualizatora współbieżności</span><span class="sxs-lookup"><span data-stu-id="8db91-335">Understanding different GC modes with Concurrency Visualizer</span></span>](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [<span data-ttu-id="8db91-336">Odkryto stertę dużych obiektów</span><span class="sxs-lookup"><span data-stu-id="8db91-336">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="8db91-337">Sterta dużych obiektów</span><span class="sxs-lookup"><span data-stu-id="8db91-337">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)
