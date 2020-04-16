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
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a>Zarządzanie pamięcią i wyrzucanie elementów bezużytecznych (GC) w ASP.NET Core

Przez [Sébastien Ros](https://github.com/sebastienros) i [Rick Anderson](https://twitter.com/RickAndMSFT)

Zarządzanie pamięcią jest złożone, nawet w zarządzanej ramach, takiej jak .NET. Analizowanie i rozumienie problemów z pamięcią może być wyzwaniem. W tym artykule:

* Był motywowany przez wiele *przeciek pamięci* i *GC nie działa* problemy. Większość z tych problemów były spowodowane przez nie zrozumienie, jak działa zużycie pamięci w .NET Core, lub nie zrozumienie, jak jest mierzona.
* Demonstruje problematyczne użycie pamięci i sugeruje alternatywne podejścia.

## <a name="how-garbage-collection-gc-works-in-net-core"></a>Jak działa zbieranie elementów bezużytecznych (GC) w .NET Core

GC przydziela segmenty sterty, gdzie każdy segment jest ciągły zakres pamięci. Obiekty umieszczone w stercie są podzielone na jedną z 3 generacji: 0, 1 lub 2. Generowanie określa częstotliwość GC próbuje zwolnić pamięć na zarządzanych obiektów, które nie są już odwołuje się przez aplikację. Niższe ponumerowane pokolenia są GC'd częściej.

Obiekty są przenoszone z pokolenia na pokolenie na podstawie ich okresu istnienia. Ponieważ obiekty żyją dłużej, są przenoszone do wyższej generacji. Jak wspomniano wcześniej, wyższe pokolenia są GC'd rzadziej. Krótkotrwałe obiekty żyjące zawsze pozostają w generacji 0. Na przykład obiekty, do których odwołuje się okres życia żądania sieci web są krótkotrwałe. [Singletony](xref:fundamentals/dependency-injection#service-lifetimes) na poziomie aplikacji zazwyczaj migrują do generacji 2.

Po uruchomieniu aplikacji ASP.NET Core gc:

* Rezerwuje trochę pamięci dla początkowych segmentów sterty.
* Zatwierdza niewielką część pamięci po załadowaniu środowiska wykonawczego.

Poprzednie alokacje pamięci są wykonywane ze względu na wydajność. Korzyści wydajności pochodzi z segmentów sterty w pamięci ciągłej.

### <a name="call-gccollect"></a>Zadzwoń do GC. Zbierać

Wywołanie [GC. Zbieraj](xref:System.GC.Collect*) jawnie:

* **Nie** powinny być wykonywane przez aplikacje ASP.NET core produkcji.
* Jest to przydatne podczas badania przecieków pamięci.
* Podczas badania, weryfikuje GC usunął wszystkie zwisające obiekty z pamięci, dzięki czemu można zmierzyć pamięć.

## <a name="analyzing-the-memory-usage-of-an-app"></a>Analizowanie użycia pamięci przez aplikację

Dedykowane narzędzia mogą pomóc w analizie użycia pamięci:

- Zliczanie odwołań do obiektów
- Pomiar wpływu GC na wykorzystanie procesora
- Pomiar przestrzeni pamięci używanej dla każdej generacji

Do analizowania użycia pamięci należy użyć następujących narzędzi:

* [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Może być stosowany na maszynach produkcyjnych.
* [Analizowanie użycia pamięci bez debugera programu Visual Studio](/visualstudio/profiling/memory-usage-without-debugging2)
* [Użycie pamięci profilu w programie Visual Studio](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a>Wykrywanie problemów z pamięcią

Menedżer zadań może służyć do zorientowania się, ile pamięci ASP.NET aplikacja używa. Wartość pamięci Menedżera zadań:

* Reprezentuje ilość pamięci, która jest używana przez proces ASP.NET.
* Zawiera żywe obiekty aplikacji i innych konsumentów pamięci, takich jak użycie pamięci natywnej.

Jeśli wartość pamięci Menedżera zadań zwiększa się w nieskończoność i nigdy nie spłaszcza się, aplikacja ma przeciek pamięci. W poniższych sekcjach zademonstrować i wyjaśnić kilka wzorców użycia pamięci.

## <a name="sample-display-memory-usage-app"></a>Przykładowa aplikacja do korzystania z pamięci wyświetlacza

Przykładowa [aplikacja MemoryLeak](https://github.com/sebastienros/memoryleak) jest dostępna w usłudze GitHub. Aplikacja MemoryLeak:

* Zawiera kontroler diagnostyczny, który zbiera pamięć w czasie rzeczywistym i dane GC dla aplikacji.
* Ma stronę indeksu, która wyświetla pamięć i dane GC. Strona Indeks jest odświeżana co sekundę.
* Zawiera kontroler interfejsu API, który udostępnia różne wzorce obciążenia pamięci.
* Nie jest obsługiwanym narzędziem, jednak może służyć do wyświetlania wzorców użycia pamięci aplikacji ASP.NET Core.

Uruchom MemoryLeak. Przydzielona pamięć powoli zwiększa się, aż wystąpi GC. Pamięć zwiększa, ponieważ narzędzie przydziela obiekt niestandardowy do przechwytywania danych. Na poniższej ilustracji przedstawiono stronę Indeksu MemoryLeak, gdy występuje Gen 0 GC. Wykres pokazuje 0 RPS (Żądania na sekundę), ponieważ nie punkty końcowe interfejsu API z kontrolera interfejsu API zostały wywołane.

![poprzedni wykres](memory/_static/0RPS.png)

Na wykresie są wyświetlane dwie wartości użycia pamięci:

- Przydzielona: ilość pamięci zajmowanej przez obiekty zarządzane
- [Zestaw roboczy:](/windows/win32/memory/working-set)Zestaw stron w wirtualnej przestrzeni adresowej procesu, które są obecnie rezydentne w pamięci fizycznej. Pokazany zestaw roboczy jest wyświetlaną tą samą wartością Menedżera zadań.

### <a name="transient-objects"></a>Obiekty przejściowe

Następujący interfejs API tworzy wystąpienie ciągu 10 KB i zwraca je do klienta. Na każde żądanie nowy obiekt jest przydzielany w pamięci i zapisywany do odpowiedzi. Ciągi są przechowywane jako znaki UTF-16 w .NET, więc każdy znak ma 2 bajty w pamięci.

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

Poniższy wykres jest generowany przy stosunkowo małym obciążeniu, aby pokazać, jak alokacje pamięci mają wpływ na GC.

![poprzedni wykres](memory/_static/bigstring.png)

Poprzedni wykres przedstawia:

* 4K RPS (żądania na sekundę).
* Kolekcje GC generacji 0 występują co około dwie sekundy.
* Zestaw roboczy jest stały na poziomie około 500 MB.
* Procesor wynosi 12%.
* Zużycie i uwalnianie pamięci (przez GC) jest stabilne.

Poniższy wykres jest przyjmowany przy maksymalnej przepływności, która może być obsługiwana przez maszynę.

![poprzedni wykres](memory/_static/bigstring2.png)

Poprzedni wykres przedstawia:

* 22K RPS
* Kolekcje GC generacji 0 występują kilka razy na sekundę.
* Kolekcje generacji 1 są wyzwalane, ponieważ aplikacja przydzielona znacznie więcej pamięci na sekundę.
* Zestaw roboczy jest stały na poziomie około 500 MB.
* Procesor wynosi 33%.
* Zużycie i uwalnianie pamięci (przez GC) jest stabilne.
* Procesor (33%) nie jest nadmiernie wykorzystywane, w związku z tym wyrzucania elementów bezużytecznych można nadążyć za dużą liczbę alokacji.

### <a name="workstation-gc-vs-server-gc"></a>Gc stacji roboczej a gc serwera

Moduł zbierający elementy bezużyteczne .NET ma dwa różne tryby:

* **Stacja robocza GC**: Zoptymalizowana dla pulpitu.
* **Serwer GC**. Domyślny gc dla aplikacji ASP.NET Core. Zoptymalizowany pod kątem serwera.

Tryb GC można ustawić jawnie w pliku projektu lub w pliku *runtimeconfig.json* opublikowanej aplikacji. Następujące znaczniki pokazuje `ServerGarbageCollection` ustawienie w pliku projektu:

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

Zmiana `ServerGarbageCollection` w pliku projektu wymaga przebudowy aplikacji.

**Uwaga:** Wyrzucanie elementów bezużytecznych serwera **nie** jest dostępne na komputerach z jednym rdzeniem. Aby uzyskać więcej informacji, zobacz <xref:System.Runtime.GCSettings.IsServerGC>.

Na poniższej ilustracji przedstawiono profil pamięci w 5K RPS przy użyciu GC stacji roboczej.

![poprzedni wykres](memory/_static/workstation.png)

Różnice między tym wykresem a wersją serwera są znaczące:

- Zestaw roboczy spada z 500 MB do 70 MB.
- GC wykonuje kolekcje generacji 0 wiele razy na sekundę, a nie co dwie sekundy.
- GC spada z 300 MB do 10 MB.

W typowym środowisku serwera sieci web użycie procesora CPU jest ważniejsze niż pamięć, dlatego gc serwera jest lepsze. Jeśli wykorzystanie pamięci jest wysokie, a użycie procesora CPU jest stosunkowo niskie, GC stacji roboczej może być bardziej wydajne. Na przykład o wysokiej gęstości hosting kilka aplikacji sieci web, gdzie pamięć jest ograniczona.

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a>GC przy użyciu platformy Docker i małych kontenerów

Gdy wiele konteneryzowanych aplikacji są uruchomione na jednym komputerze, GC stacji roboczej może być bardziej preformant niż GC serwera. Aby uzyskać więcej informacji, zobacz [Uruchamianie z gc serwera w małym kontenerze](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) i [uruchamianie z gc serwera w scenariuszu małego kontenera część 1 – twardy limit dla sterty GC](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).

### <a name="persistent-object-references"></a>Odwołania do obiektów trwałych

GC nie może zwolnić obiektów, do których istnieją odwołania. Obiekty, do których istnieją odwołania, ale nie są już potrzebne, powodują przeciek pamięci. Jeśli aplikacja często przydziela obiekty i nie zwalnia ich po ich nie są już potrzebne, użycie pamięci wzrośnie wraz z upływem czasu.

Następujący interfejs API tworzy wystąpienie ciągu 10 KB i zwraca je do klienta. Różnica w poprzednim przykładzie jest to, że to wystąpienie odwołuje się statyczny element członkowski, co oznacza, że nigdy nie jest dostępny dla kolekcji.

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

Powyższy kod ma następujące działanie:

* Jest przykładem typowego przecieku pamięci.
* W razie częstych połączeń powoduje zwiększenie pamięci `OutOfMemory` aplikacji, dopóki proces nie ulegnie awarii z wyjątkiem.

![poprzedni wykres](memory/_static/eternal.png)

Na poprzednim obrazie:

* Testowanie `/api/staticstring` obciążenia punktu końcowego powoduje liniowy wzrost pamięci.
* GC próbuje zwolnić pamięć w miarę wzrostu ciśnienia pamięci, wywołując kolekcję generacji 2.
* GC nie może zwolnić przeciekły pamięci. Przydzielony i zestaw roboczy wzrasta z czasem.

Niektóre scenariusze, takie jak buforowanie, wymagają, aby odwołania do obiektów były przechowywane, dopóki ciśnienie pamięci nie wymusza ich zwolnienia. Klasa <xref:System.WeakReference> może służyć do tego typu kodu buforowania. Obiekt `WeakReference` jest zbierany pod ciśnieniem pamięci. Domyślna implementacja <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> `WeakReference`zastosowań .

### <a name="native-memory"></a>Pamięć natywna

Niektóre obiekty .NET Core są zależne od pamięci natywnej. Pamięć natywna **nie** może być zbierana przez GC. Obiekt .NET przy użyciu pamięci natywnej musi zwolnić go przy użyciu kodu macierzystego.

.NET udostępnia <xref:System.IDisposable> interfejs, aby umożliwić deweloperom zwolnienie pamięci natywnej. Nawet <xref:System.IDisposable.Dispose*> jeśli nie jest wywoływana, `Dispose` poprawnie zaimplementowane klasy wywołać po uruchomieniu [finalizatora.](/dotnet/csharp/programming-guide/classes-and-structs/destructors)

Spójrzmy na poniższy kod:

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) jest klasą zarządzaną, więc każde wystąpienie zostanie zebrane na końcu żądania.

Na poniższej ilustracji przedstawiono `fileprovider` profil pamięci podczas ciągłego wywoływania interfejsu API.

![poprzedni wykres](memory/_static/fileprovider.png)

Na poprzednim wykresie przedstawiono oczywisty problem z implementacją tej klasy, ponieważ stale zwiększa użycie pamięci. Jest to znany problem, który jest śledzony w [tym problemie](https://github.com/dotnet/aspnetcore/issues/3110).

Ten sam wyciek może się zdarzyć w kodzie użytkownika, przez jedną z następujących czynności:

* Nie zwalniając klasy poprawnie.
* Zapominając wywołać `Dispose`metodę obiektów zależnych, które powinny być usuwane.

### <a name="large-objects-heap"></a>Sterta dużych obiektów

Częste alokacji pamięci/wolne cykle można fragment pamięci, zwłaszcza podczas przydzielania dużych fragmentów pamięci. Obiekty są przydzielane w ciągłych blokach pamięci. Aby zmniejszyć fragmentację, gdy GC zwalnia pamięć, próbuje ją zdefragmentować. Proces ten nazywany jest **zagęszczaniem**. Zagęszczanie obejmuje poruszające się obiekty. Przenoszenie dużych obiektów nakłada karę za wydajność. Z tego powodu GC tworzy specjalną strefę pamięci dla _dużych_ obiektów, o nazwie [sterty dużych obiektów](/dotnet/standard/garbage-collection/large-object-heap) (LOH). Obiekty, które są większe niż 85 000 bajtów (około 83 KB) są:

* Umieszczone na LOH.
* Nie zagęszczona.
* Zebrane podczas generacji 2 GCs.

Gdy LOH jest pełna, GC uruchomi kolekcję generacji 2. Kolekcje generacji 2:

* Są z natury powolne.
* Dodatkowo ponieść koszt wyzwalania kolekcji na wszystkich innych pokoleń.

Następujący kod kompaktywa LOH natychmiast:

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

Informacje <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> na temat zagęszczania LOH można znaleźć w informacji na temat zagęszczania.

W kontenerach korzystających z programu .NET Core 3.0 lub nowszych LOH jest automatycznie kompaktowany.

Następujący interfejs API, który ilustruje to zachowanie:

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

Na poniższym wykresie przedstawiono `/api/loh/84975` profil pamięci wywoływania punktu końcowego, przy maksymalnym obciążeniu:

![poprzedni wykres](memory/_static/loh1.png)

Na poniższym wykresie przedstawiono `/api/loh/84976` profil pamięci wywoływania punktu końcowego, przydzielając *jeszcze jeden bajt:*

![poprzedni wykres](memory/_static/loh2.png)

Uwaga: `byte[]` Struktura ma bajty napowietrzne. Dlatego 84 976 bajtów wyzwala limit 85 000.

Porównanie dwóch poprzednich wykresów:

* Zestaw roboczy jest podobny dla obu scenariuszy, około 450 MB.
* W obszarze LOH żądań (84,975 bajtów) pokazuje głównie generacji 0 kolekcji.
* Ponad LOH żądań generowania stałych generacji 2 kolekcje. Kolekcje generacji 2 są drogie. Wymagany jest większy procesor, a przepustowość spada o prawie 50%.

Tymczasowe duże obiekty są szczególnie problematyczne, ponieważ powodują gen2 GCs.

Aby uzyskać maksymalną wydajność, należy zminimalizować użycie dużych obiektów. Jeśli to możliwe, podziel duże obiekty. Na przykład [oprogramowanie pośredniczące buforowania odpowiedzi](xref:performance/caching/response) w ASP.NET Core podzieliło wpisy pamięci podręcznej na bloki o masie mniejszej niż 85 000 bajtów.

Poniższe łącza pokazują ASP.NET podstawowe podejście do utrzymywania obiektów w granicach LOH:

* [ResponseCaching/Streams/StreamUtilities.cs](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [ResponseCaching/MemoryResponseCache.cs](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

Aby uzyskać więcej informacji, zobacz:

* [Odkryto stertę dużych obiektów](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Sterta dużych obiektów](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a>Funkcja HttpClient

Niepoprawne <xref:System.Net.Http.HttpClient> użycie może spowodować wyciek zasobów. Zasoby systemowe, takie jak połączenia z bazą danych, gniazda, uchwyty plików itp.:

* Są bardziej rzadkie niż pamięć.
* Są bardziej problematyczne, gdy wycieka niż pamięć.

Doświadczeni deweloperzy platformy <xref:System.IDisposable.Dispose*> .NET wiedzą, aby wywołać obiekty, które implementują <xref:System.IDisposable>. Nie utylizacja `IDisposable` obiektów, które implementują zazwyczaj powoduje wyciek pamięci lub wyciekły zasoby systemowe.

`HttpClient``IDisposable`narzędzi, ale **nie** powinny być usuwane przy każdym wywołaniu. Raczej `HttpClient` powinny być ponownie nadużywane.

Następujący punkt końcowy tworzy i `HttpClient` usuwa nowe wystąpienie na każde żądanie:

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

Pod obciążeniem rejestrowane są następujące komunikaty o błędach:

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

Mimo że `HttpClient` wystąpienia są usuwane, rzeczywiste połączenie sieciowe zajmuje trochę czasu, aby zostać zwolnionym przez system operacyjny. Poprzez ciągłe tworzenie nowych połączeń, _występuje wyczerpanie portów._ Każde połączenie klienta wymaga własnego portu klienta.

Jednym ze sposobów zapobiegania wyczerpaniu portów `HttpClient` jest ponowne użycie tego samego wystąpienia:

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

Wystąpienie `HttpClient` jest zwalniane po zatrzymaniu aplikacji. W tym przykładzie pokazano, że nie każdy jednorazowy zasób powinien być usuwany po każdym użyciu.

Zobacz następujące dla lepszego sposobu obsługi `HttpClient` okresu istnienia wystąpienia:

* [Zarządzanie protokołem i okresem istnienia httpclient](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [HttpClient dziennik fabryki](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a>Buforowanie obiektów

W poprzednim przykładzie `HttpClient` pokazano, jak wystąpienie może być statyczne i ponownie przez wszystkie żądania. Ponowne użycie zapobiega wyczerpaniu zasobów.

Buforowanie obiektów:

* Używa wzorca ponownego użycia.
* Jest przeznaczony dla obiektów, które są kosztowne do utworzenia.

Pula jest kolekcją wstępnie zainicjowanych obiektów, które mogą być zarezerwowane i zwalniane między wątkami. Pule mogą definiować reguły alokacji, takie jak limity, wstępnie zdefiniowane rozmiary lub tempo wzrostu.

Pakiet NuGet [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) zawiera klasy, które pomagają zarządzać takimi pulami.

Następujący punkt końcowy interfejsu API `byte` wystąpienia buforu, który jest wypełniony liczb losowych na każde żądanie:

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

Na poniższym wykresie jest wywoływanie poprzedniego interfejsu API z umiarkowanym obciążeniem:

![poprzedni wykres](memory/_static/array.png)

Na poprzednim wykresie kolekcje generacji 0 zdarzają się mniej więcej raz na sekundę.

Poprzedni kod można zoptymalizować, łącząc `byte` bufor przy użyciu [\<>. ](xref:System.Buffers.ArrayPool`1) Wystąpienie statyczne jest ponownie zażywane w żądaniach.

Co innego w tym podejściu jest to, że obiekt w puli jest zwracany z interfejsu API. Oznacza to:

* Obiekt jest poza kontrolą, jak tylko powrócisz z metody.
* Nie można zwolnić obiektu.

Aby skonfigurować utylizację obiektu,

* Hermetyzuj tablicę zbiorczą w obiekcie jednorazowego użytku.
* Zarejestruj obiekt w puli za pomocą [httpcontext.response.registerfordispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).

`RegisterForDispose`zajmie się wywoływaniem `Dispose`obiektu docelowego, tak aby został wydany tylko po zakończeniu żądania HTTP.

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

Zastosowanie tego samego obciążenia co wersja nieskładka nieskładnia powoduje następujące wyniki na poniższym wykresie:

![poprzedni wykres](memory/_static/pooledarray.png)

Główną różnicą są przydzielane bajty, a w konsekwencji znacznie mniej kolekcji generacji 0.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wyrzucanie elementów bezużytecznych](/dotnet/standard/garbage-collection/)
* [Opis różnych trybów GC za pomocą wizualizatora współbieżności](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [Odkryto stertę dużych obiektów](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Sterta dużych obiektów](/dotnet/standard/garbage-collection/large-object-heap)
