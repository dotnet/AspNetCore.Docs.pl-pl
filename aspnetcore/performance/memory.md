---
title: Zarządzanie pamięcią i wzorce w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak pamięć jest zarządzana w ASP.NET Core oraz jak działa Moduł wyrzucania elementów bezużytecznych.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/memory
ms.openlocfilehash: d261a26de7b9ba77e5f9787ae2eb37293257a0fc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406397"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a><span data-ttu-id="807b4-103">Zarządzanie pamięcią i wyrzucanie elementów bezużytecznych (GC) w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="807b4-103">Memory management and garbage collection (GC) in ASP.NET Core</span></span>

<span data-ttu-id="807b4-104">Autorzy [Sébastien ros](https://github.com/sebastienros) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="807b4-104">By [Sébastien Ros](https://github.com/sebastienros) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="807b4-105">Zarządzanie pamięcią jest złożone, nawet w zarządzanym środowisku, takim jak .NET.</span><span class="sxs-lookup"><span data-stu-id="807b4-105">Memory management is complex, even in a managed framework like .NET.</span></span> <span data-ttu-id="807b4-106">Analizowanie i rozwiązywanie problemów z pamięcią może być trudne.</span><span class="sxs-lookup"><span data-stu-id="807b4-106">Analyzing and understanding memory issues can be challenging.</span></span> <span data-ttu-id="807b4-107">W tym artykule:</span><span class="sxs-lookup"><span data-stu-id="807b4-107">This article:</span></span>

* <span data-ttu-id="807b4-108">Został umotywowany przez wiele *przecieków pamięci* i *nie działa* problem z GC.</span><span class="sxs-lookup"><span data-stu-id="807b4-108">Was motivated by many *memory leak* and *GC not working* issues.</span></span> <span data-ttu-id="807b4-109">Większość z tych problemów została spowodowana przez niezrozumienie, jak zużycie pamięci działa w programie .NET Core, lub nie zrozumienie, jak to jest mierzone.</span><span class="sxs-lookup"><span data-stu-id="807b4-109">Most of these issues were caused by not understanding how memory consumption works in .NET Core, or not understanding how it's measured.</span></span>
* <span data-ttu-id="807b4-110">Pokazuje problematyczne użycie pamięci i sugeruje alternatywne podejścia.</span><span class="sxs-lookup"><span data-stu-id="807b4-110">Demonstrates problematic memory use, and suggests alternative approaches.</span></span>

## <a name="how-garbage-collection-gc-works-in-net-core"></a><span data-ttu-id="807b4-111">Jak działa wyrzucanie elementów bezużytecznych w programie .NET Core</span><span class="sxs-lookup"><span data-stu-id="807b4-111">How garbage collection (GC) works in .NET Core</span></span>

<span data-ttu-id="807b4-112">GC przypisuje segmenty sterty, w których każdy segment jest ciągłym zakresem pamięci.</span><span class="sxs-lookup"><span data-stu-id="807b4-112">The GC allocates heap segments where each segment is a contiguous range of memory.</span></span> <span data-ttu-id="807b4-113">Obiekty umieszczone w stercie są podzielone na jedną z trzech generacji: 0, 1 lub 2.</span><span class="sxs-lookup"><span data-stu-id="807b4-113">Objects placed in the heap are categorized into one of 3 generations: 0, 1, or 2.</span></span> <span data-ttu-id="807b4-114">Generacja określa częstotliwość, z jaką system GC próbuje zwolnić pamięć na zarządzanych obiektach, do których nie odwołuje się już aplikacja.</span><span class="sxs-lookup"><span data-stu-id="807b4-114">The generation determines the frequency the GC attempts to release memory on managed objects that are no longer referenced by the app.</span></span> <span data-ttu-id="807b4-115">Niższe numerowane generacji są częścią pamięci.</span><span class="sxs-lookup"><span data-stu-id="807b4-115">Lower numbered generations are GC'd more frequently.</span></span>

<span data-ttu-id="807b4-116">Obiekty są przenoszone z jednej generacji na inną w zależności od ich okresu istnienia.</span><span class="sxs-lookup"><span data-stu-id="807b4-116">Objects are moved from one generation to another based on their lifetime.</span></span> <span data-ttu-id="807b4-117">Gdy obiekty są już na żywo, są przenoszone do wyższego poziomu generacji.</span><span class="sxs-lookup"><span data-stu-id="807b4-117">As objects live longer, they are moved into a higher generation.</span></span> <span data-ttu-id="807b4-118">Jak wspomniano wcześniej, wyższe generacji są krótsze.</span><span class="sxs-lookup"><span data-stu-id="807b4-118">As mentioned previously, higher generations are GC'd less often.</span></span> <span data-ttu-id="807b4-119">Niekrótkoterminowe obiekty pozostają zawsze w generacji 0.</span><span class="sxs-lookup"><span data-stu-id="807b4-119">Short term lived objects always remain in generation 0.</span></span> <span data-ttu-id="807b4-120">Na przykład obiekty, do których istnieją odwołania w czasie trwania żądania sieci Web, są krótkotrwałe.</span><span class="sxs-lookup"><span data-stu-id="807b4-120">For example, objects that are referenced during the life of a web request are short lived.</span></span> <span data-ttu-id="807b4-121">[Pojedyncze](xref:fundamentals/dependency-injection#service-lifetimes) aplikacje są zwykle migrowane do generacji 2.</span><span class="sxs-lookup"><span data-stu-id="807b4-121">Application level [singletons](xref:fundamentals/dependency-injection#service-lifetimes) generally migrate to generation 2.</span></span>

<span data-ttu-id="807b4-122">Po uruchomieniu aplikacji ASP.NET Core, GC:</span><span class="sxs-lookup"><span data-stu-id="807b4-122">When an ASP.NET Core app starts, the GC:</span></span>

* <span data-ttu-id="807b4-123">Rezerwuje pewną ilość pamięci dla początkowych segmentów sterty.</span><span class="sxs-lookup"><span data-stu-id="807b4-123">Reserves some memory for the initial heap segments.</span></span>
* <span data-ttu-id="807b4-124">Zatwierdza małą część pamięci podczas ładowania środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="807b4-124">Commits a small portion of memory when the runtime is loaded.</span></span>

<span data-ttu-id="807b4-125">Powyższe alokacje pamięci są wykonywane ze względu na wydajność.</span><span class="sxs-lookup"><span data-stu-id="807b4-125">The preceding memory allocations are done for performance reasons.</span></span> <span data-ttu-id="807b4-126">Korzyść wydajności pochodzi z segmentów sterty w ciągłej pamięci.</span><span class="sxs-lookup"><span data-stu-id="807b4-126">The performance benefit comes from heap segments in contiguous memory.</span></span>

### <a name="call-gccollect"></a><span data-ttu-id="807b4-127">Wywołaj metodę GC. Kolekcjonowa</span><span class="sxs-lookup"><span data-stu-id="807b4-127">Call GC.Collect</span></span>

<span data-ttu-id="807b4-128">Wywoływanie [GC. Zbierz](xref:System.GC.Collect*) jawnie:</span><span class="sxs-lookup"><span data-stu-id="807b4-128">Calling [GC.Collect](xref:System.GC.Collect*) explicitly:</span></span>

* <span data-ttu-id="807b4-129">**Nie** należy wykonywać według ASP.NET Core produkcyjnych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="807b4-129">Should **not** be done by production ASP.NET Core apps.</span></span>
* <span data-ttu-id="807b4-130">Jest przydatne podczas badania przecieków pamięci.</span><span class="sxs-lookup"><span data-stu-id="807b4-130">Is useful when investigating memory leaks.</span></span>
* <span data-ttu-id="807b4-131">Podczas badania program sprawdza, czy moduł GC usunął wszystkie obiekty zawieszonego z pamięci, aby można było mierzyć pamięć.</span><span class="sxs-lookup"><span data-stu-id="807b4-131">When investigating, verifies the GC has removed all dangling objects from memory so memory can be measured.</span></span>

## <a name="analyzing-the-memory-usage-of-an-app"></a><span data-ttu-id="807b4-132">Analizowanie użycia pamięci przez aplikację</span><span class="sxs-lookup"><span data-stu-id="807b4-132">Analyzing the memory usage of an app</span></span>

<span data-ttu-id="807b4-133">Narzędzia dedykowane ułatwiają Analizowanie użycia pamięci:</span><span class="sxs-lookup"><span data-stu-id="807b4-133">Dedicated tools can help analyzing memory usage:</span></span>

- <span data-ttu-id="807b4-134">Liczenie odwołań do obiektów</span><span class="sxs-lookup"><span data-stu-id="807b4-134">Counting object references</span></span>
- <span data-ttu-id="807b4-135">Mierzenie, ile ma wpływ na wykorzystanie procesora CPU przez moduł GC</span><span class="sxs-lookup"><span data-stu-id="807b4-135">Measuring how much impact the GC has on CPU usage</span></span>
- <span data-ttu-id="807b4-136">Mierzenie przestrzeni pamięci używanej dla każdej generacji</span><span class="sxs-lookup"><span data-stu-id="807b4-136">Measuring memory space used for each generation</span></span>

<span data-ttu-id="807b4-137">Użyj następujących narzędzi do analizowania użycia pamięci:</span><span class="sxs-lookup"><span data-stu-id="807b4-137">Use the following tools to analyze memory usage:</span></span>

* <span data-ttu-id="807b4-138">[dotnet-Trace](/dotnet/core/diagnostics/dotnet-trace): może być używany na maszynach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="807b4-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Can be  used on production machines.</span></span>
* [<span data-ttu-id="807b4-139">Analizowanie użycia pamięci bez debugera programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="807b4-139">Analyze memory usage without the Visual Studio debugger</span></span>](/visualstudio/profiling/memory-usage-without-debugging2)
* [<span data-ttu-id="807b4-140">Użycie pamięci profilu w programie Visual Studio</span><span class="sxs-lookup"><span data-stu-id="807b4-140">Profile memory usage in Visual Studio</span></span>](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a><span data-ttu-id="807b4-141">Wykrywanie problemów z pamięcią</span><span class="sxs-lookup"><span data-stu-id="807b4-141">Detecting memory issues</span></span>

<span data-ttu-id="807b4-142">Za pomocą Menedżera zadań można uzyskać informacje o ilości pamięci używanej przez aplikację ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="807b4-142">Task Manager can be used to get an idea of how much memory an ASP.NET app is using.</span></span> <span data-ttu-id="807b4-143">Wartość pamięci Menedżera zadań:</span><span class="sxs-lookup"><span data-stu-id="807b4-143">The Task Manager memory value:</span></span>

* <span data-ttu-id="807b4-144">Przedstawia ilość pamięci używanej przez proces ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="807b4-144">Represents the amount of memory that is used by the ASP.NET process.</span></span>
* <span data-ttu-id="807b4-145">Obejmuje żywe obiekty i innych odbiorców pamięci, takich jak użycie pamięci natywnej.</span><span class="sxs-lookup"><span data-stu-id="807b4-145">Includes the app's living objects and other memory consumers such as native memory usage.</span></span>

<span data-ttu-id="807b4-146">Jeśli wartość pamięci Menedżera zadań zwiększy się w nieskończoność i nigdy nie zostanie spłaszczona, aplikacja ma przeciek pamięci.</span><span class="sxs-lookup"><span data-stu-id="807b4-146">If the Task Manager memory value increases indefinitely and never flattens out, the app has a memory leak.</span></span> <span data-ttu-id="807b4-147">W poniższych sekcjach przedstawiono i wyjaśniono kilka wzorców użycia pamięci.</span><span class="sxs-lookup"><span data-stu-id="807b4-147">The following sections demonstrate and explain several memory usage patterns.</span></span>

## <a name="sample-display-memory-usage-app"></a><span data-ttu-id="807b4-148">Przykładowa aplikacja do wyświetlania pamięci</span><span class="sxs-lookup"><span data-stu-id="807b4-148">Sample display memory usage app</span></span>

<span data-ttu-id="807b4-149">[Przykładowa aplikacja MemoryLeak](https://github.com/sebastienros/memoryleak) jest dostępna w witrynie GitHub.</span><span class="sxs-lookup"><span data-stu-id="807b4-149">The [MemoryLeak sample app](https://github.com/sebastienros/memoryleak) is available on GitHub.</span></span> <span data-ttu-id="807b4-150">Aplikacja MemoryLeak:</span><span class="sxs-lookup"><span data-stu-id="807b4-150">The MemoryLeak app:</span></span>

* <span data-ttu-id="807b4-151">Obejmuje kontroler diagnostyczny, który zbiera dane pamięci w czasie rzeczywistym i GC dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="807b4-151">Includes a diagnostic controller that gathers real-time memory and GC data for the app.</span></span>
* <span data-ttu-id="807b4-152">Zawiera stronę indeksu wyświetlającą pamięć i dane GC.</span><span class="sxs-lookup"><span data-stu-id="807b4-152">Has an Index page that displays the memory and GC data.</span></span> <span data-ttu-id="807b4-153">Strona indeks jest odświeżana co sekundę.</span><span class="sxs-lookup"><span data-stu-id="807b4-153">The Index page is refreshed every second.</span></span>
* <span data-ttu-id="807b4-154">Zawiera kontroler interfejsu API, który udostępnia różne wzorce obciążenia pamięci.</span><span class="sxs-lookup"><span data-stu-id="807b4-154">Contains an API controller that provides various memory load patterns.</span></span>
* <span data-ttu-id="807b4-155">Nie jest to obsługiwane narzędzie, ale może służyć do wyświetlania wzorców użycia pamięci ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="807b4-155">Is not a supported tool, however, it can be used to display memory usage patterns of ASP.NET Core apps.</span></span>

<span data-ttu-id="807b4-156">Uruchom MemoryLeak.</span><span class="sxs-lookup"><span data-stu-id="807b4-156">Run MemoryLeak.</span></span> <span data-ttu-id="807b4-157">Przydzieloną pamięć powoli wzrasta do momentu wystąpienia wykazu globalnego.</span><span class="sxs-lookup"><span data-stu-id="807b4-157">Allocated memory slowly increases until a GC occurs.</span></span> <span data-ttu-id="807b4-158">Pamięć rośnie, ponieważ narzędzie przydziela obiekt niestandardowy do przechwytywania danych.</span><span class="sxs-lookup"><span data-stu-id="807b4-158">Memory increases because the tool allocates custom object to capture data.</span></span> <span data-ttu-id="807b4-159">Na poniższej ilustracji przedstawiono stronę indeksu MemoryLeak w przypadku wystąpienia generacji 0 GC.</span><span class="sxs-lookup"><span data-stu-id="807b4-159">The following image shows the MemoryLeak Index page when a Gen 0 GC occurs.</span></span> <span data-ttu-id="807b4-160">Wykres pokazuje 0 RPS pliku (liczba żądań na sekundę), ponieważ nie wywołano punktów końcowych interfejsu API z kontrolera interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="807b4-160">The chart shows 0 RPS (Requests per second) because no API endpoints from the API controller have been called.</span></span>

![Poprzedni wykres](memory/_static/0RPS.png)

<span data-ttu-id="807b4-162">Na wykresie są wyświetlane dwie wartości użycia pamięci:</span><span class="sxs-lookup"><span data-stu-id="807b4-162">The chart displays two values for the memory usage:</span></span>

- <span data-ttu-id="807b4-163">Przydzielone: ilość pamięci zajętej przez zarządzane obiekty</span><span class="sxs-lookup"><span data-stu-id="807b4-163">Allocated: the amount of memory occupied by managed objects</span></span>
- <span data-ttu-id="807b4-164">[Zestaw roboczy](/windows/win32/memory/working-set): zestaw stron w wirtualnej przestrzeni adresowej procesu, który jest obecnie rezydentem pamięci fizycznej.</span><span class="sxs-lookup"><span data-stu-id="807b4-164">[Working set](/windows/win32/memory/working-set): The set of pages in the virtual address space of the process that are currently resident in physical memory.</span></span> <span data-ttu-id="807b4-165">Wyświetlany zestaw roboczy jest taka sama jak wartość w Menedżerze zadań.</span><span class="sxs-lookup"><span data-stu-id="807b4-165">The working set shown is the same value Task Manager displays.</span></span>

### <a name="transient-objects"></a><span data-ttu-id="807b4-166">Obiekty przejściowe</span><span class="sxs-lookup"><span data-stu-id="807b4-166">Transient objects</span></span>

<span data-ttu-id="807b4-167">Poniższy interfejs API tworzy wystąpienie ciągu 10 KB i zwraca go do klienta.</span><span class="sxs-lookup"><span data-stu-id="807b4-167">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="807b4-168">W przypadku każdego żądania nowy obiekt zostaje przydzielony w pamięci i zapisany w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="807b4-168">On each request, a new object is allocated in memory and written to the response.</span></span> <span data-ttu-id="807b4-169">Ciągi są przechowywane jako znaki UTF-16 w programie .NET, więc każdy znak pobiera 2 bajty w pamięci.</span><span class="sxs-lookup"><span data-stu-id="807b4-169">Strings are stored as UTF-16 characters in .NET so each character takes 2 bytes in memory.</span></span>

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

<span data-ttu-id="807b4-170">Poniższy wykres jest generowany z stosunkowo małym obciążeniem, aby pokazać, w jaki sposób przydziały pamięci mają wpływ system GC.</span><span class="sxs-lookup"><span data-stu-id="807b4-170">The following graph is generated with a relatively small load in to show how memory allocations are impacted by the GC.</span></span>

![Poprzedni wykres](memory/_static/bigstring.png)

<span data-ttu-id="807b4-172">Powyższy wykres przedstawia:</span><span class="sxs-lookup"><span data-stu-id="807b4-172">The preceding chart shows:</span></span>

* <span data-ttu-id="807b4-173">4K RPS pliku (żądania na sekundę).</span><span class="sxs-lookup"><span data-stu-id="807b4-173">4K RPS (Requests per second).</span></span>
* <span data-ttu-id="807b4-174">Kolekcje GC generacji 0 są wykonywane co dwa sekundy.</span><span class="sxs-lookup"><span data-stu-id="807b4-174">Generation 0 GC collections occur about every two seconds.</span></span>
* <span data-ttu-id="807b4-175">Zestaw roboczy jest stały o około 500 MB.</span><span class="sxs-lookup"><span data-stu-id="807b4-175">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="807b4-176">Procesor CPU wynosi 12%.</span><span class="sxs-lookup"><span data-stu-id="807b4-176">CPU is 12%.</span></span>
* <span data-ttu-id="807b4-177">Użycie pamięci i wydanie (za pomocą GC) jest stabilne.</span><span class="sxs-lookup"><span data-stu-id="807b4-177">The memory consumption and release (through GC) is stable.</span></span>

<span data-ttu-id="807b4-178">Na poniższym wykresie przedstawiono maksymalną przepływność, która może być obsługiwana przez maszynę.</span><span class="sxs-lookup"><span data-stu-id="807b4-178">The following chart is taken at the max throughput that can be handled by the machine.</span></span>

![Poprzedni wykres](memory/_static/bigstring2.png)

<span data-ttu-id="807b4-180">Powyższy wykres przedstawia:</span><span class="sxs-lookup"><span data-stu-id="807b4-180">The preceding chart shows:</span></span>

* <span data-ttu-id="807b4-181">22K RPS PLIKU</span><span class="sxs-lookup"><span data-stu-id="807b4-181">22K RPS</span></span>
* <span data-ttu-id="807b4-182">Kolekcje GC generacji 0 są wykonywane kilka razy na sekundę.</span><span class="sxs-lookup"><span data-stu-id="807b4-182">Generation 0 GC collections occur several times per second.</span></span>
* <span data-ttu-id="807b4-183">Kolekcje 1 generacji są wyzwalane, ponieważ aplikacja przydzieliła znacznie więcej pamięci na sekundę.</span><span class="sxs-lookup"><span data-stu-id="807b4-183">Generation 1 collections are triggered because the app allocated significantly more memory per second.</span></span>
* <span data-ttu-id="807b4-184">Zestaw roboczy jest stały o około 500 MB.</span><span class="sxs-lookup"><span data-stu-id="807b4-184">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="807b4-185">Procesor CPU wynosi 33%.</span><span class="sxs-lookup"><span data-stu-id="807b4-185">CPU is 33%.</span></span>
* <span data-ttu-id="807b4-186">Użycie pamięci i wydanie (za pomocą GC) jest stabilne.</span><span class="sxs-lookup"><span data-stu-id="807b4-186">The memory consumption and release (through GC) is stable.</span></span>
* <span data-ttu-id="807b4-187">Procesor CPU (33%) nie jest nadmiernie wykorzystane, dlatego wyrzucanie elementów bezużytecznych może obsłużyć dużą liczbę alokacji.</span><span class="sxs-lookup"><span data-stu-id="807b4-187">The CPU (33%) is not over-utilized, therefore the garbage collection can keep up with a high number of allocations.</span></span>

### <a name="workstation-gc-vs-server-gc"></a><span data-ttu-id="807b4-188">Stacja robocza GC a serwer GC</span><span class="sxs-lookup"><span data-stu-id="807b4-188">Workstation GC vs. Server GC</span></span>

<span data-ttu-id="807b4-189">Moduł wyrzucania elementów bezużytecznych platformy .NET ma dwa różne tryby:</span><span class="sxs-lookup"><span data-stu-id="807b4-189">The .NET Garbage Collector has two different modes:</span></span>

* <span data-ttu-id="807b4-190">**Stacja robocza GC**: zoptymalizowana dla pulpitu.</span><span class="sxs-lookup"><span data-stu-id="807b4-190">**Workstation GC**: Optimized for the desktop.</span></span>
* <span data-ttu-id="807b4-191">**Serwer GC**.</span><span class="sxs-lookup"><span data-stu-id="807b4-191">**Server GC**.</span></span> <span data-ttu-id="807b4-192">Domyślna wartość GC dla aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="807b4-192">The default GC for ASP.NET Core apps.</span></span> <span data-ttu-id="807b4-193">Zoptymalizowany pod kątem serwera.</span><span class="sxs-lookup"><span data-stu-id="807b4-193">Optimized for the server.</span></span>

<span data-ttu-id="807b4-194">Tryb GC można jawnie ustawić w pliku projektu lub w *runtimeconfig.jsna* pliku opublikowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="807b4-194">The GC mode can be set explicitly in the project file or in the *runtimeconfig.json* file of the published app.</span></span> <span data-ttu-id="807b4-195">Następujące znaczniki pokazują ustawienia `ServerGarbageCollection` w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="807b4-195">The following markup shows setting `ServerGarbageCollection` in the project file:</span></span>

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

<span data-ttu-id="807b4-196">Zmiana `ServerGarbageCollection` w pliku projektu wymaga odbudowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="807b4-196">Changing `ServerGarbageCollection` in the project file requires the app to be rebuilt.</span></span>

<span data-ttu-id="807b4-197">**Uwaga:** Zbieranie elementów bezużytecznych serwera **nie** jest dostępne na maszynach z jednym rdzeniem.</span><span class="sxs-lookup"><span data-stu-id="807b4-197">**Note:** Server garbage collection is **not** available on machines with a single core.</span></span> <span data-ttu-id="807b4-198">Aby uzyskać więcej informacji, zobacz <xref:System.Runtime.GCSettings.IsServerGC>.</span><span class="sxs-lookup"><span data-stu-id="807b4-198">For more information, see <xref:System.Runtime.GCSettings.IsServerGC>.</span></span>

<span data-ttu-id="807b4-199">Na poniższej ilustracji przedstawiono profil pamięci w ramach 5 K RPS pliku przy użyciu usługi Stacja robocza GC.</span><span class="sxs-lookup"><span data-stu-id="807b4-199">The following image shows the memory profile under a 5K RPS using the Workstation GC.</span></span>

![Poprzedni wykres](memory/_static/workstation.png)

<span data-ttu-id="807b4-201">Różnice między tym wykresem a wersją serwera są istotne:</span><span class="sxs-lookup"><span data-stu-id="807b4-201">The differences between this chart and the server version are significant:</span></span>

- <span data-ttu-id="807b4-202">Zestaw roboczy spadnie od 500 MB do 70 MB.</span><span class="sxs-lookup"><span data-stu-id="807b4-202">The working set drops from 500 MB to 70 MB.</span></span>
- <span data-ttu-id="807b4-203">GC wykonuje wiele kolekcji generacji 0 na sekundę, a nie co dwa sekundy.</span><span class="sxs-lookup"><span data-stu-id="807b4-203">The GC does generation 0 collections multiple times per second instead of every two seconds.</span></span>
- <span data-ttu-id="807b4-204">Wykaz globalny spadnie od 300 MB do 10 MB.</span><span class="sxs-lookup"><span data-stu-id="807b4-204">GC drops from 300 MB to 10 MB.</span></span>

<span data-ttu-id="807b4-205">W typowym środowisku serwera sieci Web użycie procesora CPU jest ważniejsze niż pamięć, dlatego serwer GC jest lepszy.</span><span class="sxs-lookup"><span data-stu-id="807b4-205">On a typical web server environment, CPU usage is more important than memory, therefore the Server GC is better.</span></span> <span data-ttu-id="807b4-206">Jeśli użycie pamięci jest wysokie i użycie procesora CPU jest stosunkowo niskie, stacja robocza GC może być bardziej wydajna.</span><span class="sxs-lookup"><span data-stu-id="807b4-206">If memory utilization is high and CPU usage is relatively low, the Workstation GC might be more performant.</span></span> <span data-ttu-id="807b4-207">Na przykład duża gęstość hostowanie kilku aplikacji sieci Web, w których ilość pamięci jest niestateczna.</span><span class="sxs-lookup"><span data-stu-id="807b4-207">For example, high density hosting several web apps where memory is scarce.</span></span>

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a><span data-ttu-id="807b4-208">GC przy użyciu platformy Docker i małych kontenerów</span><span class="sxs-lookup"><span data-stu-id="807b4-208">GC using Docker and small containers</span></span>

<span data-ttu-id="807b4-209">Gdy wiele aplikacji kontenerowych jest uruchomionych na jednym komputerze, stacja robocza GC może być bardziej niezależna od serwera GC.</span><span class="sxs-lookup"><span data-stu-id="807b4-209">When multiple containerized apps are running on one machine, Workstation GC might be more preformant than Server GC.</span></span> <span data-ttu-id="807b4-210">Aby uzyskać więcej informacji, zobacz [Uruchamianie z serwerem GC w niewielkim kontenerze](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) i [Uruchamianie z serwerem GC w niewielkim scenariuszu kontenera część 1 — stały limit dla sterty GC](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span><span class="sxs-lookup"><span data-stu-id="807b4-210">For more information, see [Running with Server GC in a Small Container](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) and [Running with Server GC in a Small Container Scenario Part 1 – Hard Limit for the GC Heap](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span></span>

### <a name="persistent-object-references"></a><span data-ttu-id="807b4-211">Trwałe odwołania do obiektów</span><span class="sxs-lookup"><span data-stu-id="807b4-211">Persistent object references</span></span>

<span data-ttu-id="807b4-212">GC nie może zwolnić obiektów, do których istnieją odwołania.</span><span class="sxs-lookup"><span data-stu-id="807b4-212">The GC cannot free objects that are referenced.</span></span> <span data-ttu-id="807b4-213">Obiekty, do których istnieją odwołania, ale nie są już potrzebne, powodują przeciek pamięci.</span><span class="sxs-lookup"><span data-stu-id="807b4-213">Objects that are referenced but no longer needed result in a memory leak.</span></span> <span data-ttu-id="807b4-214">Jeśli aplikacja często przydziela obiekty i nie może ich zwolnić, gdy nie są już potrzebne, użycie pamięci zwiększy się z upływem czasu.</span><span class="sxs-lookup"><span data-stu-id="807b4-214">If the app frequently allocates objects and fails to free them after they are no longer needed, memory usage will increase over time.</span></span>

<span data-ttu-id="807b4-215">Poniższy interfejs API tworzy wystąpienie ciągu 10 KB i zwraca go do klienta.</span><span class="sxs-lookup"><span data-stu-id="807b4-215">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="807b4-216">Różnica w poprzednim przykładzie polega na tym, że to wystąpienie jest przywoływane przez statyczną składową, co oznacza, że nigdy nie jest dostępna dla kolekcji.</span><span class="sxs-lookup"><span data-stu-id="807b4-216">The difference with the previous example is that this instance is referenced by a static member, which means it's never available for collection.</span></span>

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

<span data-ttu-id="807b4-217">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="807b4-217">The preceding code:</span></span>

* <span data-ttu-id="807b4-218">Jest przykładem typowego przecieku pamięci.</span><span class="sxs-lookup"><span data-stu-id="807b4-218">Is an example of a typical memory leak.</span></span>
* <span data-ttu-id="807b4-219">Częste wywołania powodują, że pamięć aplikacji wzrasta do momentu awarii procesu z `OutOfMemory` wyjątkiem.</span><span class="sxs-lookup"><span data-stu-id="807b4-219">With frequent calls, causes app memory to increases until the process crashes with an `OutOfMemory` exception.</span></span>

![Poprzedni wykres](memory/_static/eternal.png)

<span data-ttu-id="807b4-221">Na powyższym obrazie:</span><span class="sxs-lookup"><span data-stu-id="807b4-221">In the preceding image:</span></span>

* <span data-ttu-id="807b4-222">Testowanie obciążenia `/api/staticstring` punktu końcowego powoduje wzrost liniowy w pamięci.</span><span class="sxs-lookup"><span data-stu-id="807b4-222">Load testing the `/api/staticstring` endpoint causes a linear increase in memory.</span></span>
* <span data-ttu-id="807b4-223">Proces GC próbuje zwolnić pamięć w miarę wzrostu ilości pamięci, wywołując kolekcję generacji 2.</span><span class="sxs-lookup"><span data-stu-id="807b4-223">The GC tries to free memory as the memory pressure grows, by calling a generation 2 collection.</span></span>
* <span data-ttu-id="807b4-224">GC nie może zwolnić ilości pamięci.</span><span class="sxs-lookup"><span data-stu-id="807b4-224">The GC cannot free the leaked memory.</span></span> <span data-ttu-id="807b4-225">Alokacja i zestaw roboczy zwiększają się wraz z czasem.</span><span class="sxs-lookup"><span data-stu-id="807b4-225">Allocated and working set increase with time.</span></span>

<span data-ttu-id="807b4-226">Niektóre scenariusze, takie jak buforowanie, wymagają, aby odwołania do obiektów były przechowywane do momentu wymuszenia zwolnienia pamięci.</span><span class="sxs-lookup"><span data-stu-id="807b4-226">Some scenarios, such as caching, require object references to be held until memory pressure forces them to be released.</span></span> <span data-ttu-id="807b4-227"><xref:System.WeakReference>Klasa może być używana dla tego typu kodu buforowania.</span><span class="sxs-lookup"><span data-stu-id="807b4-227">The <xref:System.WeakReference> class can be used for this type of caching code.</span></span> <span data-ttu-id="807b4-228">`WeakReference`Obiekt jest zbierany pod ciśnieniem pamięci.</span><span class="sxs-lookup"><span data-stu-id="807b4-228">A `WeakReference` object is collected under memory pressures.</span></span> <span data-ttu-id="807b4-229">Domyślna implementacja programu <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> `WeakReference` .</span><span class="sxs-lookup"><span data-stu-id="807b4-229">The default implementation of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> uses `WeakReference`.</span></span>

### <a name="native-memory"></a><span data-ttu-id="807b4-230">Pamięć natywna</span><span class="sxs-lookup"><span data-stu-id="807b4-230">Native memory</span></span>

<span data-ttu-id="807b4-231">Niektóre obiekty .NET Core są zależne od pamięci natywnej.</span><span class="sxs-lookup"><span data-stu-id="807b4-231">Some .NET Core objects rely on native memory.</span></span> <span data-ttu-id="807b4-232">Pamięć natywna **nie** może być zbierana przez GC.</span><span class="sxs-lookup"><span data-stu-id="807b4-232">Native memory can **not** be collected by the GC.</span></span> <span data-ttu-id="807b4-233">Obiekt .NET używający pamięci natywnej musi być bezpłatny przy użyciu kodu natywnego.</span><span class="sxs-lookup"><span data-stu-id="807b4-233">The .NET object using native memory must free it using native code.</span></span>

<span data-ttu-id="807b4-234">Platforma .NET udostępnia <xref:System.IDisposable> interfejs, dzięki któremu deweloperzy mogą zwolnić pamięć natywną.</span><span class="sxs-lookup"><span data-stu-id="807b4-234">.NET provides the <xref:System.IDisposable> interface to let developers release native memory.</span></span> <span data-ttu-id="807b4-235">Nawet jeśli <xref:System.IDisposable.Dispose*> nie jest wywoływana, prawidłowo zaimplementowane klasy są wywoływane `Dispose` po uruchomieniu [finalizatora](/dotnet/csharp/programming-guide/classes-and-structs/destructors) .</span><span class="sxs-lookup"><span data-stu-id="807b4-235">Even if <xref:System.IDisposable.Dispose*> is not called, correctly implemented classes call `Dispose` when the [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) runs.</span></span>

<span data-ttu-id="807b4-236">Spójrzmy na poniższy kod:</span><span class="sxs-lookup"><span data-stu-id="807b4-236">Consider the following code:</span></span>

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

<span data-ttu-id="807b4-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) jest klasą zarządzaną, więc każde wystąpienie zostanie zebrane na końcu żądania.</span><span class="sxs-lookup"><span data-stu-id="807b4-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) is a managed class, so any instance will be collected at the end of the request.</span></span>

<span data-ttu-id="807b4-238">Na poniższej ilustracji przedstawiono profil pamięci podczas `fileprovider` ciągłego wywoływania interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="807b4-238">The following image shows the memory profile while invoking the `fileprovider` API continuously.</span></span>

![Poprzedni wykres](memory/_static/fileprovider.png)

<span data-ttu-id="807b4-240">Powyższy wykres przedstawia oczywisty problem z implementacją tej klasy, ponieważ zwiększa użycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="807b4-240">The preceding chart shows an obvious issue with the implementation of this class, as it keeps increasing memory usage.</span></span> <span data-ttu-id="807b4-241">Jest to znany problem, który jest śledzony w [tym problemie](https://github.com/dotnet/aspnetcore/issues/3110).</span><span class="sxs-lookup"><span data-stu-id="807b4-241">This is a known problem that is being tracked in [this issue](https://github.com/dotnet/aspnetcore/issues/3110).</span></span>

<span data-ttu-id="807b4-242">Ten sam wyciek może wystąpić w kodzie użytkownika, wykonując jedną z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="807b4-242">The same leak could be happen in user code, by one of the following:</span></span>

* <span data-ttu-id="807b4-243">Nie zwalniaj klasy prawidłowo.</span><span class="sxs-lookup"><span data-stu-id="807b4-243">Not releasing the class correctly.</span></span>
* <span data-ttu-id="807b4-244">Zapominanie o wywołaniu `Dispose` metody obiektów zależnych, które powinny zostać usunięte.</span><span class="sxs-lookup"><span data-stu-id="807b4-244">Forgetting to invoke the `Dispose`method of the dependent objects that should be disposed.</span></span>

### <a name="large-objects-heap"></a><span data-ttu-id="807b4-245">Sterta dużych obiektów</span><span class="sxs-lookup"><span data-stu-id="807b4-245">Large objects heap</span></span>

<span data-ttu-id="807b4-246">Częste alokacje pamięci/wolne cykle mogą fragmentacji pamięci, szczególnie podczas przydzielania dużych fragmentów pamięci.</span><span class="sxs-lookup"><span data-stu-id="807b4-246">Frequent memory allocation/free cycles can fragment memory, especially when allocating large chunks of memory.</span></span> <span data-ttu-id="807b4-247">Obiekty są przydzielono w ciągłych blokach pamięci.</span><span class="sxs-lookup"><span data-stu-id="807b4-247">Objects are allocated in contiguous blocks of memory.</span></span> <span data-ttu-id="807b4-248">W celu ograniczenia fragmentacji, gdy pamięć podwolna zostanie zwolniona, Trys ją do defragmentacji.</span><span class="sxs-lookup"><span data-stu-id="807b4-248">To mitigate fragmentation, when the GC frees memory, it trys to defragment it.</span></span> <span data-ttu-id="807b4-249">Ten proces jest nazywany **kompaktowania**.</span><span class="sxs-lookup"><span data-stu-id="807b4-249">This process is called **compaction**.</span></span> <span data-ttu-id="807b4-250">Kompaktowanie obejmuje przeniesienie obiektów.</span><span class="sxs-lookup"><span data-stu-id="807b4-250">Compaction involves moving objects.</span></span> <span data-ttu-id="807b4-251">Przeniesienie dużych obiektów nakłada spadek wydajności.</span><span class="sxs-lookup"><span data-stu-id="807b4-251">Moving large objects imposes a performance penalty.</span></span> <span data-ttu-id="807b4-252">Z tego powodu w wykazie globalnym tworzona jest specjalna strefa pamięci dla _dużych_ obiektów, nazywana [stertą dużego obiektu](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span><span class="sxs-lookup"><span data-stu-id="807b4-252">For this reason the GC creates a special memory zone for _large_ objects, called the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span></span> <span data-ttu-id="807b4-253">Obiekty, które są większe niż 85 000 bajtów (około 83 KB) są następujące:</span><span class="sxs-lookup"><span data-stu-id="807b4-253">Objects that are greater than 85,000 bytes (approximately 83 KB) are:</span></span>

* <span data-ttu-id="807b4-254">Umieszczone na LOH.</span><span class="sxs-lookup"><span data-stu-id="807b4-254">Placed on the LOH.</span></span>
* <span data-ttu-id="807b4-255">Nie kompaktuje.</span><span class="sxs-lookup"><span data-stu-id="807b4-255">Not compacted.</span></span>
* <span data-ttu-id="807b4-256">Zbierane podczas operacje odzyskiwania pamięci generacji 2.</span><span class="sxs-lookup"><span data-stu-id="807b4-256">Collected during generation 2 GCs.</span></span>

<span data-ttu-id="807b4-257">Gdy LOH jest zapełniony, GC wywoła kolekcję generacji 2.</span><span class="sxs-lookup"><span data-stu-id="807b4-257">When the LOH is full, the GC will trigger a generation 2 collection.</span></span> <span data-ttu-id="807b4-258">Kolekcje 2 generacji:</span><span class="sxs-lookup"><span data-stu-id="807b4-258">Generation 2 collections:</span></span>

* <span data-ttu-id="807b4-259">Są z założenia powolne.</span><span class="sxs-lookup"><span data-stu-id="807b4-259">Are inherently slow.</span></span>
* <span data-ttu-id="807b4-260">Dodatkowo Powiąż koszt wyzwolenia kolekcji na wszystkich innych generacjach.</span><span class="sxs-lookup"><span data-stu-id="807b4-260">Additionally incur the cost of triggering a collection on all other generations.</span></span>

<span data-ttu-id="807b4-261">Następujący kod kompaktuje LOH od razu:</span><span class="sxs-lookup"><span data-stu-id="807b4-261">The following code compacts the LOH immediately:</span></span>

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<span data-ttu-id="807b4-262">Zobacz <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> , aby uzyskać informacje na temat kompaktowania LOH.</span><span class="sxs-lookup"><span data-stu-id="807b4-262">See <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> for information on compacting the LOH.</span></span>

<span data-ttu-id="807b4-263">W kontenerach korzystających z platformy .NET Core 3,0 i nowszych LOH jest automatycznie kompaktowana.</span><span class="sxs-lookup"><span data-stu-id="807b4-263">In containers using .NET Core 3.0 and later, the LOH is automatically compacted.</span></span>

<span data-ttu-id="807b4-264">Poniższy interfejs API, który ilustruje to zachowanie:</span><span class="sxs-lookup"><span data-stu-id="807b4-264">The following API that illustrates this behavior:</span></span>

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

<span data-ttu-id="807b4-265">Na poniższym wykresie przedstawiono profil pamięci wywołania `/api/loh/84975` punktu końcowego w obszarze maksymalne obciążenie:</span><span class="sxs-lookup"><span data-stu-id="807b4-265">The following chart shows the memory profile of calling the `/api/loh/84975` endpoint, under maximum load:</span></span>

![Poprzedni wykres](memory/_static/loh1.png)

<span data-ttu-id="807b4-267">Na poniższym wykresie przedstawiono profil pamięci wywołania `/api/loh/84976` punktu końcowego, przydzielanie *tylko jednego bajtu*:</span><span class="sxs-lookup"><span data-stu-id="807b4-267">The following chart shows the memory profile of calling the `/api/loh/84976` endpoint, allocating *just one more byte*:</span></span>

![Poprzedni wykres](memory/_static/loh2.png)

<span data-ttu-id="807b4-269">Uwaga: `byte[]` Struktura zawiera bajty dodatkowe.</span><span class="sxs-lookup"><span data-stu-id="807b4-269">Note: The `byte[]` structure has overhead bytes.</span></span> <span data-ttu-id="807b4-270">Dlatego 84 976 bajtów wyzwala limit 85 000.</span><span class="sxs-lookup"><span data-stu-id="807b4-270">That's why 84,976 bytes triggers the 85,000 limit.</span></span>

<span data-ttu-id="807b4-271">Porównanie dwóch wcześniejszych wykresów:</span><span class="sxs-lookup"><span data-stu-id="807b4-271">Comparing the two preceding charts:</span></span>

* <span data-ttu-id="807b4-272">Zestaw roboczy jest podobny do obu scenariuszy, około 450 MB.</span><span class="sxs-lookup"><span data-stu-id="807b4-272">The working set is similar for both scenarios, about 450 MB.</span></span>
* <span data-ttu-id="807b4-273">W obszarze żądania LOH (84 975 bajtów) przedstawiono większość kolekcji generacji 0.</span><span class="sxs-lookup"><span data-stu-id="807b4-273">The under LOH requests (84,975 bytes) shows mostly generation 0 collections.</span></span>
* <span data-ttu-id="807b4-274">Żądania over LOH generują kolekcje stałej generacji 2.</span><span class="sxs-lookup"><span data-stu-id="807b4-274">The over LOH requests generate constant generation 2 collections.</span></span> <span data-ttu-id="807b4-275">Kolekcje generacji 2 są kosztowne.</span><span class="sxs-lookup"><span data-stu-id="807b4-275">Generation 2 collections are expensive.</span></span> <span data-ttu-id="807b4-276">Wymagany jest większy procesor CPU, a przepływność spadnie niemal 50% czasu.</span><span class="sxs-lookup"><span data-stu-id="807b4-276">More CPU is required and throughput drops almost 50%.</span></span>

<span data-ttu-id="807b4-277">Tymczasowe duże obiekty są szczególnie problematyczne, ponieważ powodują Gen2 operacje odzyskiwania pamięci.</span><span class="sxs-lookup"><span data-stu-id="807b4-277">Temporary large objects are particularly problematic because they cause gen2 GCs.</span></span>

<span data-ttu-id="807b4-278">W celu uzyskania maksymalnej wydajności należy zminimalizować użycie dużego obiektu.</span><span class="sxs-lookup"><span data-stu-id="807b4-278">For maximum performance, large object use should be minimized.</span></span> <span data-ttu-id="807b4-279">Jeśli to możliwe, rozdziel duże obiekty.</span><span class="sxs-lookup"><span data-stu-id="807b4-279">If possible, split up large objects.</span></span> <span data-ttu-id="807b4-280">Na przykład buforowanie z pamięci [podręcznej](xref:performance/caching/response) w ASP.NET Core podzielić wpisy pamięci podręcznej na bloki mniejsze niż 85 000 bajtów.</span><span class="sxs-lookup"><span data-stu-id="807b4-280">For example, [Response Caching](xref:performance/caching/response) middleware in ASP.NET Core split the cache entries into blocks less than 85,000 bytes.</span></span>

<span data-ttu-id="807b4-281">Następujące linki pokazują ASP.NET Core podejście do zachowywania obiektów w LOH limicie:</span><span class="sxs-lookup"><span data-stu-id="807b4-281">The following links show the ASP.NET Core approach to keeping objects under the LOH limit:</span></span>

* [<span data-ttu-id="807b4-282">ResponseCaching/strumienie/StreamUtilities. cs</span><span class="sxs-lookup"><span data-stu-id="807b4-282">ResponseCaching/Streams/StreamUtilities.cs</span></span>](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [<span data-ttu-id="807b4-283">ResponseCaching/MemoryResponseCache. cs</span><span class="sxs-lookup"><span data-stu-id="807b4-283">ResponseCaching/MemoryResponseCache.cs</span></span>](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

<span data-ttu-id="807b4-284">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="807b4-284">For more information, see:</span></span>

* [<span data-ttu-id="807b4-285">Niekryte sterta dużego obiektu</span><span class="sxs-lookup"><span data-stu-id="807b4-285">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="807b4-286">Sterta dużych obiektów</span><span class="sxs-lookup"><span data-stu-id="807b4-286">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a><span data-ttu-id="807b4-287">HttpClient</span><span class="sxs-lookup"><span data-stu-id="807b4-287">HttpClient</span></span>

<span data-ttu-id="807b4-288">Nieprawidłowe użycie <xref:System.Net.Http.HttpClient> może skutkować wyciekiem zasobów.</span><span class="sxs-lookup"><span data-stu-id="807b4-288">Incorrectly using <xref:System.Net.Http.HttpClient> can result in a resource leak.</span></span> <span data-ttu-id="807b4-289">Zasoby systemowe, takie jak połączenia z bazami danych, gniazda, uchwyty plików itp.:</span><span class="sxs-lookup"><span data-stu-id="807b4-289">System resources, such as database connections, sockets, file handles, etc.:</span></span>

* <span data-ttu-id="807b4-290">Jest bardziej nieograniczony niż pamięć.</span><span class="sxs-lookup"><span data-stu-id="807b4-290">Are more scarce than memory.</span></span>
* <span data-ttu-id="807b4-291">Są bardziej problematyczne w przypadku przecieków od pamięci.</span><span class="sxs-lookup"><span data-stu-id="807b4-291">Are more problematic when leaked than memory.</span></span>

<span data-ttu-id="807b4-292">Doświadczeni Deweloperzy platformy .NET wiedzą, jak odwoływać się do <xref:System.IDisposable.Dispose*> obiektów, które implementują <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="807b4-292">Experienced .NET developers know to call <xref:System.IDisposable.Dispose*> on objects that implement <xref:System.IDisposable>.</span></span> <span data-ttu-id="807b4-293">Nieusuwania obiektów, które implementują `IDisposable` zwykle, powoduje przeciek pamięci lub przeciek zasobów systemu.</span><span class="sxs-lookup"><span data-stu-id="807b4-293">Not disposing objects that implement `IDisposable` typically results in leaked memory or leaked system resources.</span></span>

<span data-ttu-id="807b4-294">`HttpClient`implementuje `IDisposable` , ale **nie** powinien być usuwany przy każdym wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="807b4-294">`HttpClient` implements `IDisposable`, but should **not** be disposed on every invocation.</span></span> <span data-ttu-id="807b4-295">Należy raczej `HttpClient` ponownie użyć.</span><span class="sxs-lookup"><span data-stu-id="807b4-295">Rather, `HttpClient` should be reused.</span></span>

<span data-ttu-id="807b4-296">Następujący punkt końcowy tworzy i usuwa nowe `HttpClient` wystąpienie dla każdego żądania:</span><span class="sxs-lookup"><span data-stu-id="807b4-296">The following endpoint creates and disposes a new  `HttpClient` instance on every request:</span></span>

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

<span data-ttu-id="807b4-297">W obszarze obciążenie rejestrowane są następujące komunikaty o błędach:</span><span class="sxs-lookup"><span data-stu-id="807b4-297">Under load, the following error messages are logged:</span></span>

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

<span data-ttu-id="807b4-298">Nawet jeśli `HttpClient` wystąpienia zostaną usunięte, rzeczywiste połączenie sieciowe zajmuje trochę czasu, aby zwolnić przez system operacyjny.</span><span class="sxs-lookup"><span data-stu-id="807b4-298">Even though the `HttpClient` instances are disposed, the actual network connection takes some time to be released by the operating system.</span></span> <span data-ttu-id="807b4-299">Nieustannie tworząc nowe połączenia, nastąpi _wyczerpanie portów_ .</span><span class="sxs-lookup"><span data-stu-id="807b4-299">By continuously creating new connections, _ports exhaustion_ occurs.</span></span> <span data-ttu-id="807b4-300">Każde połączenie klienta wymaga własnego portu klienta.</span><span class="sxs-lookup"><span data-stu-id="807b4-300">Each client connection requires its own client port.</span></span>

<span data-ttu-id="807b4-301">Jednym ze sposobów zapobiegania wyczerpaniu portów jest ponowne użycie tego samego `HttpClient` wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="807b4-301">One way to prevent port exhaustion is to reuse the same `HttpClient` instance:</span></span>

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

<span data-ttu-id="807b4-302">`HttpClient`Wystąpienie jest wydawany, gdy aplikacja zostanie zatrzymana.</span><span class="sxs-lookup"><span data-stu-id="807b4-302">The `HttpClient` instance is released when the app stops.</span></span> <span data-ttu-id="807b4-303">Ten przykład pokazuje, że nie każdy zasób jednorazowy powinien zostać usunięty po każdym użyciu.</span><span class="sxs-lookup"><span data-stu-id="807b4-303">This example shows that not every disposable resource should be disposed after each use.</span></span>

<span data-ttu-id="807b4-304">Aby lepiej obsługiwać okres istnienia wystąpienia, zobacz następujące tematy `HttpClient` :</span><span class="sxs-lookup"><span data-stu-id="807b4-304">See the following for a better way to handle the lifetime of an `HttpClient` instance:</span></span>

* [<span data-ttu-id="807b4-305">HttpClient i zarządzanie okresem istnienia</span><span class="sxs-lookup"><span data-stu-id="807b4-305">HttpClient and lifetime management</span></span>](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [<span data-ttu-id="807b4-306">Blog dotyczący fabryki HTTPClient</span><span class="sxs-lookup"><span data-stu-id="807b4-306">HTTPClient factory blog</span></span>](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a><span data-ttu-id="807b4-307">Buforowanie obiektów</span><span class="sxs-lookup"><span data-stu-id="807b4-307">Object pooling</span></span>

<span data-ttu-id="807b4-308">W poprzednim przykładzie pokazano, jak `HttpClient` wystąpienie może być statyczne i ponownie używane przez wszystkie żądania.</span><span class="sxs-lookup"><span data-stu-id="807b4-308">The previous example showed how the `HttpClient` instance can be made static and reused by all requests.</span></span> <span data-ttu-id="807b4-309">Ponowne użycie uniemożliwia uruchomienie zasobów.</span><span class="sxs-lookup"><span data-stu-id="807b4-309">Reuse prevents running out of resources.</span></span>

<span data-ttu-id="807b4-310">Buforowanie obiektów:</span><span class="sxs-lookup"><span data-stu-id="807b4-310">Object pooling:</span></span>

* <span data-ttu-id="807b4-311">Używa wzorca ponownego użycia.</span><span class="sxs-lookup"><span data-stu-id="807b4-311">Uses the reuse pattern.</span></span>
* <span data-ttu-id="807b4-312">Jest przeznaczony dla obiektów, które są kosztowne do utworzenia.</span><span class="sxs-lookup"><span data-stu-id="807b4-312">Is designed for objects that are expensive to create.</span></span>

<span data-ttu-id="807b4-313">Pula to kolekcja wstępnie zainicjowanych obiektów, które można zarezerwować i zwolnić między wątkami.</span><span class="sxs-lookup"><span data-stu-id="807b4-313">A pool is a collection of pre-initialized objects that can be reserved and released across threads.</span></span> <span data-ttu-id="807b4-314">Pule mogą definiować reguły alokacji, takie jak limity, wstępnie zdefiniowane rozmiary lub szybkość wzrostu.</span><span class="sxs-lookup"><span data-stu-id="807b4-314">Pools can define allocation rules such as limits, predefined sizes, or growth rate.</span></span>

<span data-ttu-id="807b4-315">Pakiet NuGet [Microsoft. Extensions. Objectpool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) zawiera klasy, które pomagają zarządzać takimi pulami.</span><span class="sxs-lookup"><span data-stu-id="807b4-315">The NuGet package [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contains classes that help to manage such pools.</span></span>

<span data-ttu-id="807b4-316">Następujący punkt końcowy interfejsu API tworzy wystąpienie `byte` buforu, który jest wypełniony liczbami losowymi dla każdego żądania:</span><span class="sxs-lookup"><span data-stu-id="807b4-316">The following API endpoint instantiates a `byte` buffer that is filled with random numbers on each request:</span></span>

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

<span data-ttu-id="807b4-317">Na poniższym wykresie przedstawiono wywoływanie poprzedzającego interfejsu API o umiarkowanym obciążeniu:</span><span class="sxs-lookup"><span data-stu-id="807b4-317">The following chart display calling the preceding API with moderate load:</span></span>

![Poprzedni wykres](memory/_static/array.png)

<span data-ttu-id="807b4-319">Na poprzednim wykresie kolekcje generacji 0 są wykonywane około raz na sekundę.</span><span class="sxs-lookup"><span data-stu-id="807b4-319">In the preceding chart, generation 0 collections happen approximately once per second.</span></span>

<span data-ttu-id="807b4-320">Poprzedni kod można zoptymalizować przez buforowanie `byte` buforu przy użyciu [ \<T> ArrayPool](xref:System.Buffers.ArrayPool`1).</span><span class="sxs-lookup"><span data-stu-id="807b4-320">The preceding code can be optimized by pooling the `byte` buffer by using [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1).</span></span> <span data-ttu-id="807b4-321">Wystąpienie statyczne jest ponownie używane między żądaniami.</span><span class="sxs-lookup"><span data-stu-id="807b4-321">A static instance is reused across requests.</span></span>

<span data-ttu-id="807b4-322">Różni się to od tego, czy obiekt w puli jest zwracany z interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="807b4-322">What's different with this approach is that a pooled object is returned from the API.</span></span> <span data-ttu-id="807b4-323">Oznacza to:</span><span class="sxs-lookup"><span data-stu-id="807b4-323">That means:</span></span>

* <span data-ttu-id="807b4-324">Obiekt jest poza kontrolką zaraz po powrocie z metody.</span><span class="sxs-lookup"><span data-stu-id="807b4-324">The object is out of your control as soon as you return from the method.</span></span>
* <span data-ttu-id="807b4-325">Nie można zwolnić obiektu.</span><span class="sxs-lookup"><span data-stu-id="807b4-325">You can't release the object.</span></span>

<span data-ttu-id="807b4-326">Aby skonfigurować usuwanie obiektu:</span><span class="sxs-lookup"><span data-stu-id="807b4-326">To set up disposal of the object:</span></span>

* <span data-ttu-id="807b4-327">Hermetyzuj tablicę w puli w obiekcie jednorazowym.</span><span class="sxs-lookup"><span data-stu-id="807b4-327">Encapsulate the pooled array in a disposable object.</span></span>
* <span data-ttu-id="807b4-328">Zarejestruj obiekt w puli za pomocą obiektu [HttpContext. Response. RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span><span class="sxs-lookup"><span data-stu-id="807b4-328">Register the pooled object with [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span></span>

<span data-ttu-id="807b4-329">`RegisterForDispose`zajmiemy się wywoływaniem `Dispose` obiektu docelowego, tak aby był on wydawany tylko po zakończeniu żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="807b4-329">`RegisterForDispose` will take care of calling `Dispose`on the target object so that it's only released when the HTTP request is complete.</span></span>

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

<span data-ttu-id="807b4-330">Zastosowanie tego samego obciążenia co wersja niebędąca w puli powoduje, że na poniższym wykresie:</span><span class="sxs-lookup"><span data-stu-id="807b4-330">Applying the same load as the non-pooled version results in the following chart:</span></span>

![Poprzedni wykres](memory/_static/pooledarray.png)

<span data-ttu-id="807b4-332">Główną różnicą jest przydzieloną liczbę bajtów, a jako wiele mniejszych kolekcji generacji 0.</span><span class="sxs-lookup"><span data-stu-id="807b4-332">The main difference is allocated bytes, and as a consequence much fewer generation 0 collections.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="807b4-333">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="807b4-333">Additional resources</span></span>

* [<span data-ttu-id="807b4-334">Odzyskiwanie pamięci</span><span class="sxs-lookup"><span data-stu-id="807b4-334">Garbage Collection</span></span>](/dotnet/standard/garbage-collection/)
* [<span data-ttu-id="807b4-335">Zrozumienie różnych trybów GC przy użyciu wizualizatora współbieżności</span><span class="sxs-lookup"><span data-stu-id="807b4-335">Understanding different GC modes with Concurrency Visualizer</span></span>](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [<span data-ttu-id="807b4-336">Niekryte sterta dużego obiektu</span><span class="sxs-lookup"><span data-stu-id="807b4-336">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="807b4-337">Sterta dużych obiektów</span><span class="sxs-lookup"><span data-stu-id="807b4-337">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)
