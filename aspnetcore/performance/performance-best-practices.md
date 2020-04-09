---
title: najważniejsze ASP.NET podstawowe rozwiązania dotyczące wydajności
author: mjrousos
description: Wskazówki dotyczące zwiększania wydajności w aplikacjach ASP.NET Core i unikania typowych problemów z wydajnością.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/06/2020
no-loc:
- SignalR
uid: performance/performance-best-practices
ms.openlocfilehash: 068a35fbe410dad24030fe68c0dfd062b402212c
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977187"
---
# <a name="aspnet-core-performance-best-practices"></a>najważniejsze ASP.NET podstawowe rozwiązania dotyczące wydajności

Przez [Mike Rousos](https://github.com/mjrousos)

Ten artykuł zawiera wskazówki dotyczące najlepszych rozwiązań dotyczących wydajności z ASP.NET Core.

## <a name="cache-aggressively"></a>Agresywna pamięć podręczna

Buforowanie jest omówione w kilku częściach tego dokumentu. Aby uzyskać więcej informacji, zobacz <xref:performance/caching/response>.

## <a name="understand-hot-code-paths"></a>Opis ścieżek gorącego kodu

W tym dokumencie *ścieżka kodu gorącego* jest zdefiniowana jako ścieżka kodu, która jest często wywoływana i gdzie występuje większość czasu wykonywania. Ścieżki kodu gorącego zazwyczaj ograniczają skalowanie aplikacji w poziomie i wydajność i są omówione w kilku częściach tego dokumentu.

## <a name="avoid-blocking-calls"></a>Unikaj blokowania połączeń

ASP.NET aplikacje Core powinny być przeznaczone do przetwarzania wielu żądań jednocześnie. Asynchroniczne interfejsy API umożliwiają małej puli wątków do obsługi tysięcy równoczesnych żądań, nie czekając na blokowanie wywołań. Zamiast czekać na długotrwałe zadanie synchroniczne do wykonania, wątek może pracować na innym żądaniu.

Typowy problem z wydajnością w aplikacjach ASP.NET Core blokuje wywołania, które mogą być asynchroniczne. Wiele wywołań blokowania synchroniczowego prowadzi do [głodu puli wątków](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) i czasy odpowiedzi zdegradowane.

**Nie**należy:

* Blokuj wykonanie asynchroniczne, wywołując [task.wait](/dotnet/api/system.threading.tasks.task.wait) lub [task.result](/dotnet/api/system.threading.tasks.task-1.result).
* Nabywanie blokad we wspólnych ścieżkach kodu. ASP.NET Aplikacje Core są najbardziej wydajne, gdy jest zaprojektowany do uruchamiania kodu równolegle.
* Wywołanie [Task.Run](/dotnet/api/system.threading.tasks.task.run) i natychmiast czekać na to. ASP.NET Core już uruchamia kod aplikacji w normalnych wątkach puli wątków, więc wywołanie Task.Run powoduje tylko dodatkowe niepotrzebne planowanie puli wątków. Nawet jeśli zaplanowany kod zablokuje wątek, Task.Run nie zapobiega.

**Czy**:

* Aby [ścieżki kodu gorącego](#understand-hot-code-paths) asynchroniczne.
* Wywołanie dostępu do danych, we/wy i długotrwałych operacji interfejsów API asynchronicznie, jeśli asynchroniczne interfejsu API jest dostępna. **Nie** należy używać [Task.Run,](/dotnet/api/system.threading.tasks.task.run) aby synchronus API asynchronous.
* Upewnij się, że akcje kontrolera/razor page'a są asynchroniczne. Cały stos wywołań jest asynchroniczne w celu skorzystania z [wzorców asynchron/await.](/dotnet/csharp/programming-guide/concepts/async/)

Profiler, takich jak [PerfView](https://github.com/Microsoft/perfview), może służyć do znajdowania wątków często dodawane do [puli wątków](/windows/desktop/procthread/thread-pools). Zdarzenie `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` wskazuje wątek dodany do puli wątków. <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="minimize-large-object-allocations"></a>Minimalizowanie alokacji dużych obiektów

Moduł [zbierający elementy bezużyteczne .NET Core](/dotnet/standard/garbage-collection/) automatycznie zarządza alokacji i zwalniania pamięci w ASP.NET aplikacji Core. Automatyczne wyrzucanie elementów bezużytecznych zazwyczaj oznacza, że deweloperzy nie muszą się martwić o jak i kiedy pamięć jest zwalniana. Jednak czyszczenie obiektów nieodwołanych wymaga czasu procesora CPU, więc deweloperzy powinni zminimalizować przydzielanie obiektów w [ścieżkach gorącego kodu.](#understand-hot-code-paths) Wyrzucanie elementów bezużytecznych jest szczególnie kosztowne w przypadku dużych obiektów (> 85 K bajtów). Duże obiekty są przechowywane na [stercie dużych obiektów](/dotnet/standard/garbage-collection/large-object-heap) i wymagają pełnego (generacji 2) wyrzucania elementów bezużytecznych do czyszczenia. W przeciwieństwie do kolekcji generacji 0 i generacji 1 kolekcja generacji 2 wymaga tymczasowego zawieszenia wykonywania aplikacji. Częste przydzielanie i delokacja dużych obiektów może powodować niespójną wydajność.

Zalecenia:

* **Należy** wziąć pod uwagę buforowanie dużych obiektów, które są często używane. Buforowanie dużych obiektów zapobiega kosztownym alokacjom.
* **Wykonaj** bufory puli przy użyciu [>\<T puli macierzy](/dotnet/api/system.buffers.arraypool-1) do przechowywania dużych tablic.
* **Nie** przydzielaj wielu, krótkotrwałych dużych obiektów na [ścieżkach gorącego kodu](#understand-hot-code-paths).

Problemy z pamięcią, takie jak poprzednie, można zdiagnozować, przeglądając statystyki wyrzucania elementów bezużytecznych (GC) w [PerfView](https://github.com/Microsoft/perfview) i badając:

* Czas wstrzymania wyrzucania elementów bezużytecznych.
* Jaki procent czasu procesora jest spędzany w wyrzucaniu elementów bezużytecznych.
* Ile wyrzucania elementów bezużytecznych są generacji 0, 1 i 2.

Aby uzyskać więcej informacji, zobacz [Wyrzucanie elementów bezużytecznych i wydajność](/dotnet/standard/garbage-collection/performance).

## <a name="optimize-data-access-and-io"></a>Optymalizacja dostępu do danych i we/wy

Interakcje z magazynem danych i innymi usługami zdalnymi są często najwolniejszymi częściami aplikacji ASP.NET Core. Wydajne odczytywanie i zapisywanie danych ma kluczowe znaczenie dla dobrej wydajności.

Zalecenia:

* **Wywołaj** asynchronicznie wszystkie interfejsy API dostępu do danych.
* **Nie** należy pobierać większej ilości danych niż jest to konieczne. Napisz zapytania, aby zwrócić tylko dane, które są niezbędne dla bieżącego żądania HTTP.
* **Należy** wziąć pod uwagę buforowanie często używanych danych pobranych z bazy danych lub usługi zdalnej, jeśli dane są nieco nieaktualne. W zależności od scenariusza należy użyć [memorycache](xref:performance/caching/memory) lub [DistributedCache](xref:performance/caching/distributed). Aby uzyskać więcej informacji, zobacz <xref:performance/caching/response>.
* **Czy** zminimalizować sieci rund. Celem jest pobranie wymaganych danych w jednym wywołaniu, a nie kilku wywołaniach.
* **Nie** należy używać [kwerend bez śledzenia](/ef/core/querying/tracking#no-tracking-queries) w entity framework core podczas uzyskiwania dostępu do danych do celów tylko do odczytu. EF Core może zwracać wyniki kwerend bez śledzenia bardziej efektywnie.
* **Należy** filtrować i agregować `.Where` `.Select`zapytania `.Sum` LINQ (na przykład z instrukcjami lub instrukcjami), tak aby filtrowanie było wykonywane przez bazę danych.
* **Należy** wziąć pod uwagę, że EF Core rozwiązuje niektóre operatory zapytań na kliencie, co może prowadzić do nieefektywnego wykonywania kwerendy. Aby uzyskać więcej informacji, zobacz [Problemy z wydajnością oceny klienta](/ef/core/querying/client-eval#client-evaluation-performance-issues).
* **Nie** należy używać kwerend rzutowania w kolekcjach, co może spowodować wykonanie kwerend SQL "N + 1". Aby uzyskać więcej informacji, zobacz [Optymalizacja skorelowanych podquerii](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).

Zobacz [EF High Performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) dla metod, które mogą poprawić wydajność w aplikacjach na dużą skalę:

* [Buforowanie DbContext](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [Jawnie skompilowane zapytania](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

Firma Microsoft zaleca pomiar wpływu poprzednich metod wysokiej wydajności przed zatwierdzeniem bazy kodu. Dodatkowa złożoność skompilowanych zapytań może nie uzasadniać poprawy wydajności.

Problemy z zapytaniami można wykryć, przeglądając czas spędzony na dostępie do danych za pomocą [usługi Application Insights](/azure/application-insights/app-insights-overview) lub narzędzi do profilowania. Większość baz danych udostępnia również statystyki dotyczące często wykonywanych zapytań.

## <a name="pool-http-connections-with-httpclientfactory"></a>Pula połączeń HTTP za pomocą protokołu HttpClientFactory

Chociaż [HttpClient](/dotnet/api/system.net.http.httpclient) implementuje `IDisposable` interfejs, jest przeznaczony do ponownego użycia. Zamknięte `HttpClient` wystąpienia pozostawiają gniazda `TIME_WAIT` otwarte w stanie przez krótki okres czasu. Jeśli ścieżka kodu, który tworzy `HttpClient` i usuwa obiektów jest często używany, aplikacja może wyczerpywać dostępne gniazda. [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) został wprowadzony w ASP.NET Core 2.1 jako rozwiązanie tego problemu. Obsługuje łączenie połączeń HTTP w celu optymalizacji wydajności i niezawodności.

Zalecenia:

* **Nie** należy tworzyć i `HttpClient` usuwać wystąpień bezpośrednio.
* **Należy** użyć [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) `HttpClient` do pobierania wystąpień. Aby uzyskać więcej informacji, zobacz [Używanie funkcji HttpClientFactory do implementowania odpornych żądań HTTP.](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)

## <a name="keep-common-code-paths-fast"></a>Utrzymuj szybkie ścieżki wspólnego kodu

Chcesz, aby cały kod był szybki. Często nazywane ścieżki kodu są najbardziej krytyczne do optymalizacji. Należą do nich:

* Składniki oprogramowania pośredniczącego w potoku przetwarzania żądań aplikacji, zwłaszcza oprogramowanie pośredniczące uruchamiane na wczesnym etapie potoku. Te składniki mają duży wpływ na wydajność.
* Kod, który jest wykonywany dla każdego żądania lub wiele razy na żądanie. Na przykład rejestrowanie niestandardowe, programy obsługi autoryzacji lub inicjowanie usług przejściowych.

Zalecenia:

* **Nie** należy używać niestandardowych składników oprogramowania pośredniczącego z długotrwałymi zadaniami.
* **Użyj** narzędzi do profilowania wydajności, takich jak [Narzędzia diagnostyczne programu Visual Studio](/visualstudio/profiling/profiling-feature-tour) lub [PerfView,](https://github.com/Microsoft/perfview)aby zidentyfikować [ścieżki gorącego kodu.](#understand-hot-code-paths)

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>Wykonywanie długotrwałych zadań poza żądaniami HTTP

Większość żądań do aplikacji core ASP.NET może być obsługiwana przez kontroler lub model strony, który wywołuje niezbędne usługi i zwraca odpowiedź HTTP. W przypadku niektórych żądań, które obejmują długotrwałe zadania, lepiej jest, aby cały proces żądania odpowiedzi asynchroniczne.

Zalecenia:

* **Nie** czekaj na długotrwałe zadania do wykonania w ramach zwykłego przetwarzania żądań HTTP.
* **Należy** rozważyć obsługę długotrwałych żądań z [usług w tle](xref:fundamentals/host/hosted-services) lub poza procesem za pomocą funkcji platformy [Azure.](/azure/azure-functions/) Ukończenie pracy poza procesem jest szczególnie korzystne dla zadań intensywnie korzystających z procesora CPU.
* **Użyj** opcji komunikacji w czasie [SignalR](xref:signalr/introduction)rzeczywistym, takich jak , aby komunikować się z klientami asynchronicznie.

## <a name="minify-client-assets"></a>Minify aktywów klienta

aplikacje ASP.NET Core ze złożonymi frontonami często obsługują wiele plików JavaScript, CSS lub obrazów. Wydajność żądań obciążenia początkowego można poprawić, aby:

* Łączenie, które łączy wiele plików w jeden.
* Minifying, co zmniejsza rozmiar plików, usuwając odstępy i komentarze.

Zalecenia:

* **Korzystaj** z [wbudowanej obsługi](xref:client-side/bundling-and-minification) ASP.NET Core do łączenia i wydobywania aktywów klientów.
* **Należy** wziąć pod uwagę inne narzędzia innych firm, takie jak [Webpack](https://webpack.js.org/), do kompleksowego zarządzania zasobami klienta.

## <a name="compress-responses"></a>Kompresowanie odpowiedzi

 Zmniejszenie rozmiaru odpowiedzi zwykle zwiększa czas reakcji aplikacji, często dramatycznie. Jednym ze sposobów zmniejszenia wielkości ładunku jest kompresja odpowiedzi aplikacji. Aby uzyskać więcej informacji, zobacz [Kompresja odpowiedzi](xref:performance/response-compression).

## <a name="use-the-latest-aspnet-core-release"></a>Korzystanie z najnowszej wersji ASP.NET Core

Każda nowa wersja ASP.NET Core zawiera ulepszenia wydajności. Optymalizacje w programach .NET Core i ASP.NET Core oznaczają, że nowsze wersje zazwyczaj przewyższają starsze wersje. Na przykład .NET Core 2.1 dodał obsługę skompilowanych wyrażeń regularnych i skorzystał z [Span\<T>](https://msdn.microsoft.com/magazine/mt814808.aspx). ASP.NET Core 2.2 dodał wsparcie dla HTTP/2. [ASP.NET Core 3.0 dodaje wiele ulepszeń,](xref:aspnetcore-3.0) które zmniejszają użycie pamięci i zwiększają przepustowość. Jeśli wydajność jest priorytetem, należy rozważyć uaktualnienie do bieżącej wersji ASP.NET Core.

## <a name="minimize-exceptions"></a>Minimalizowanie wyjątków

Wyjątki powinny być rzadkie. Zgłaszanie i przechwytywanie wyjątków jest powolny w stosunku do innych wzorców przepływu kodu. Z tego powodu wyjątki nie powinny być używane do kontrolowania normalnego przepływu programu.

Zalecenia:

* **Nie** używaj zgłaszania lub łapania wyjątków jako środka normalnego przepływu programu, szczególnie w [ścieżkach gorącego kodu.](#understand-hot-code-paths)
* **Należy** uwzględnić logikę w aplikacji do wykrywania i obsługi warunków, które mogłyby spowodować wyjątek.
* **Należy** zgłaszać lub przechwytyć wyjątki dla nietypowych lub nieoczekiwanych warunków.

Narzędzia diagnostyczne aplikacji, takie jak Usługa Application Insights, mogą pomóc w identyfikowaniu typowych wyjątków w aplikacji, które mogą mieć wpływ na wydajność.

## <a name="performance-and-reliability"></a>Wydajność i niezawodność

Poniższe sekcje zawierają wskazówki dotyczące wydajności oraz znane problemy z niezawodnością i rozwiązania.

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a>Unikaj synchronicznego odczytu lub zapisu na treści HttpRequest/HttpResponse

Wszystkie we/wy w ASP.NET Core jest asynchroniczne. Serwery `Stream` implementują interfejs, który ma zarówno synchroniczne i asynchroniczne przeciążenia. Te asynchroniczne powinny być preferowane, aby uniknąć blokowania wątków puli wątków. Blokowanie wątków może prowadzić do głodu puli wątków.

**Nie rób tego:** W poniższym przykładzie użyto <xref:System.IO.StreamReader.ReadToEnd*>pliku . Blokuje bieżący wątek czekać na wynik. Jest to przykład [synchronizacji z asynchronią](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
).

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

W poprzednim kodzie `Get` synchronicznie odczytuje całą treść żądania HTTP do pamięci. Jeśli klient jest powoli przekazywania, aplikacja robi synchronizacji za jąc asynchronicznie. Aplikacja synchronizuje się za pomocą asynchronicznych, ponieważ Kestrel **NIE** obsługuje odczytów synchronicznych.

**Zrób to:** W poniższym <xref:System.IO.StreamReader.ReadToEndAsync*> przykładzie używa i nie blokuje wątku podczas odczytu.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

Poprzedni kod asynchronicznie odczytuje całą treść żądania HTTP do pamięci.

> [!WARNING]
> Jeśli żądanie jest duże, odczyt całej treści żądania HTTP w pamięci może prowadzić do stanu braku pamięci (OOM). OOM może spowodować odmowę usługi.  Aby uzyskać więcej informacji, zobacz [Unikanie odczytywania dużych żądań organów lub jednostek odpowiedzi w pamięci](#arlb) w tym dokumencie.

**Zrób to:** Poniższy przykład jest w pełni asynchroniczne przy użyciu nie buforowane treści żądania:

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

Poprzedni kod asynchronicznie de-serializes treści żądania do obiektu C#.

## <a name="prefer-readformasync-over-requestform"></a>Preferuj ReadFormAsync nad Request.Form

Użyj `HttpContext.Request.ReadFormAsync` zamiast `HttpContext.Request.Form`.
`HttpContext.Request.Form`można bezpiecznie czytać tylko w następujących warunkach:

* Formularz został odczytany przez `ReadFormAsync`wezwanie do , i
* Wartość buforowanego formularza jest odczytywana przy użyciu`HttpContext.Request.Form`

**Nie rób tego:** W poniższym `HttpContext.Request.Form`przykładzie użyto pliku .  `HttpContext.Request.Form`używa [synchronizacji za pomocą asynchronii](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) i może prowadzić do głodu puli wątków.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

**Zrób to:** W poniższym `HttpContext.Request.ReadFormAsync` przykładzie użyto do odczytu treści formularza asynchronicznie.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a>Unikaj czytania dużych ciał żądań lub organów odpowiedzi w pamięci

W .NET każda alokacja obiektów większa niż 85 KB kończy się na stercie dużego obiektu ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)). Duże obiekty są drogie na dwa sposoby:

* Koszt alokacji jest wysoki, ponieważ pamięć dla nowo przydzielonego dużego obiektu musi zostać wyczyszczona. CLR gwarantuje, że pamięć dla wszystkich nowo przydzielonych obiektów jest wyczyszczone.
* LOH jest zbierany z resztą sterty. LOH wymaga pełnego [wyrzucania elementów bezużytecznych](/dotnet/standard/garbage-collection/fundamentals) lub [kolekcji Gen2.](/dotnet/standard/garbage-collection/fundamentals#generations)

Ten wpis w [blogu](https://adamsitnik.com/Array-Pool/#the-problem) opisuje problem zwięźle:

> Gdy duży obiekt jest przydzielany, jest oznaczony jako obiekt Gen 2. Nie Gen 0 jak w przypadku małych obiektów. Konsekwencje są takie, że jeśli zabraknie ci pamięci w LOH, GC czyści całą zarządzaną stertę, nie tylko LOH. Więc czyści Gen 0, Gen 1 i Gen 2 w tym LOH. Jest to nazywane pełne wyrzucanie elementów bezużytecznych i jest najbardziej czasochłonne wyrzucanie elementów bezużytecznych. W przypadku wielu aplikacji może to być dopuszczalne. Ale na pewno nie dla serwerów sieci web o wysokiej wydajności, gdzie kilka dużych buforów pamięci są potrzebne do obsługi przeciętnego żądania sieci web (odczyt z gniazda, dekompresyjne, dekodowanie JSON & więcej).

Naiwnie przechowując dużą treść żądania lub `byte[]` `string`odpowiedzi w jednym lub:

* Może spowodować szybkie wyczerpanie miejsca w LOH.
* Może powodować problemy z wydajnością aplikacji z powodu pełnej gc uruchomiony.

## <a name="working-with-a-synchronous-data-processing-api"></a>Praca z synchroniczowym interfejsem API przetwarzania danych

W przypadku używania serializatora/de-serializatora, który obsługuje tylko odczyty synchroniczne i zapisy (na przykład [JSON.NET):](https://www.newtonsoft.com/json/help/html/Introduction.htm)

* Bufor danych do pamięci asynchronicznie przed przekazaniem go do serializatora/de-serializatora.

> [!WARNING]
> Jeśli żądanie jest duże, może to prowadzić do warunku braku pamięci (OOM). OOM może spowodować odmowę usługi.  Aby uzyskać więcej informacji, zobacz [Unikanie odczytywania dużych żądań organów lub jednostek odpowiedzi w pamięci](#arlb) w tym dokumencie.

ASP.NET Core 3.0 używa <xref:System.Text.Json> domyślnie do serializacji JSON. <xref:System.Text.Json>:

* Odczytuje i zapisuje JSON asynchronicznie.
* Jest zoptymalizowany pod kątem tekstu UTF-8.
* Zazwyczaj wyższa wydajność `Newtonsoft.Json`niż .

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a>Nie przechowuj ihttpcontextaccessor.httpContext w polu

[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) zwraca `HttpContext` aktywne żądanie, gdy dostęp z wątku żądania. Nie `IHttpContextAccessor.HttpContext` **powinny** być przechowywane w polu lub zmiennej.

**Nie rób tego:** Poniższy przykład `HttpContext` przechowuje w polu, a następnie próbuje użyć go później.

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

Poprzedni kod często przechwytuje null lub `HttpContext` niepoprawne w konstruktorze.

**Zrób to:** Poniższy przykład:

* Przechowuje <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> w polu.
* Używa pola `HttpContext` w odpowiednim czasie i `null`sprawdza .

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a>Nie uzyskiwać dostępu do protokołu HttpContext z wielu wątków

`HttpContext`*NIE* jest bezpieczny dla nici. Uzyskiwanie `HttpContext` dostępu z wielu wątków równolegle może spowodować niezdefiniowane zachowanie, takie jak zawiesza się, awarie i uszkodzenie danych.

**Nie rób tego:** Poniższy przykład sprawia, że trzy równoległe żądania i rejestruje ścieżkę żądania przychodzącego przed i po wychodzącego żądania HTTP. Ścieżka żądania jest dostępny z wielu wątków, potencjalnie równolegle.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

**Zrób to:** Poniższy przykład kopiuje wszystkie dane z żądania przychodzącego przed dokonaniem trzech równoległych żądań.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a>Nie należy używać httpcontext po zakończeniu żądania

`HttpContext`jest prawidłowy tylko tak długo, jak istnieje aktywne żądanie HTTP w potoku ASP.NET Core. Cały ASP.NET core potoku jest asynchroniczne łańcucha delegatów, który wykonuje każde żądanie. Po `Task` zakończeniu zwrotu z tego `HttpContext` łańcucha, jest poddana recyklingowi.

**Nie rób tego:** W poniższym `async void` przykładzie użyto żądania HTTP, który jest kompletny po osiągnięciu pierwszego: `await`

* Co **zawsze** jest złą praktyką w aplikacjach ASP.NET Core.
* Uzyskuje dostęp `HttpResponse` do żądania HTTP po zakończeniu.
* Zawiesza proces.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

**Zrób to:** Poniższy przykład `Task` zwraca do struktury, więc żądanie HTTP nie kończy się, dopóki akcja zostanie zakończona.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a>Nie przechwytuj httpcontext w wątkach tła

**Nie rób tego:** Poniższy przykład pokazuje zamknięcia przechwytuje `HttpContext` z `Controller` właściwości. Jest to zła praktyka, ponieważ element pracy może:

* Uruchom poza zakresem żądania.
* Spróbuj odczytać niewłaściwy `HttpContext`.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

**Zrób to:** Poniższy przykład:

* Kopiuje dane wymagane w zadaniu w tle podczas żądania.
* Nie odwołuje się do niczego z kontrolera.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

Zadania w tle powinny być realizowane jako usługi hostowane. Aby uzyskać więcej informacji, zobacz [Zadania w tle z usługami hostowanymi](xref:fundamentals/host/hosted-services).

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a>Nie przechwytywać usług wstrzykniętych do kontrolerów na wątkach w tle

**Nie rób tego:** Poniższy przykład pokazuje zamknięcie przechwytuje `DbContext` z `Controller` parametru akcji. Jest to zła praktyka.  Element pracy może działać poza zakresem żądania. Jest `ContosoDbContext` objęty zakresem żądania, co `ObjectDisposedException`powoduje .

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

**Zrób to:** Poniższy przykład:

* Wstrzykuje <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> w celu utworzenia zakresu w elementów pracy w tle. `IServiceScopeFactory`jest singletonem.
* Tworzy nowy zakres iniekcji zależności w wątku w tle.
* Nie odwołuje się do niczego z kontrolera.
* Nie przechwytuje żądania `ContosoDbContext` przychodzącego.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

Poniższy wyróżniony kod:

* Tworzy zakres przez cały okres istnienia operacji w tle i rozpoznaje usługi z niego.
* Używa `ContosoDbContext` z właściwego zakresu.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a>Nie należy modyfikować kodu stanu ani nagłówków po uruchomieniu treści odpowiedzi

ASP.NET Core nie buforuje treści odpowiedzi HTTP. Po raz pierwszy odpowiedź jest napisana:

* Nagłówki są wysyłane wraz z tym fragmentem treści do klienta.
* Nie można już zmieniać nagłówków odpowiedzi.

**Nie rób tego:** Poniższy kod próbuje dodać nagłówki odpowiedzi po rozpoczęciu odpowiedzi:

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

W poprzednim kodzie `context.Response.Headers["test"] = "test value";` zda `next()` wyjątek, jeśli został napisany do odpowiedzi.

**Zrób to:** Poniższy przykład sprawdza, czy odpowiedź HTTP została uruchomiona przed zmodyfikowaniem nagłówków.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

**Zrób to:** Poniższy przykład `HttpResponse.OnStarting` używa, aby ustawić nagłówki przed nagłówki odpowiedzi są opróżniane do klienta.

Sprawdzanie, czy odpowiedź nie została uruchomiona, umożliwia rejestrowanie wywołania zwrotnego, które zostanie wywołane tuż przed zapisaniem nagłówków odpowiedzi. Sprawdzanie, czy odpowiedź nie została uruchomiona:

* Zapewnia możliwość dołączania lub zastępowania nagłówków w sam raz.
* Nie wymaga znajomości następnego oprogramowania pośredniczącego w potoku.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a>Nie należy dzwonić na następny(), jeśli rozpoczęto już pisanie do treści odpowiedzi

Składniki oczekują wywołania tylko wtedy, gdy jest możliwe dla nich do obsługi i manipulowania odpowiedzi.
