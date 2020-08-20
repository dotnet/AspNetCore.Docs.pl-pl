---
title: Zarządzanie pamięcią i wzorce w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak pamięć jest zarządzana w ASP.NET Core oraz jak działa Moduł wyrzucania elementów bezużytecznych.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
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
uid: performance/memory
ms.openlocfilehash: c409eaaf07109d363581ee7d61dc76521d6818d0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630669"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a>Zarządzanie pamięcią i wyrzucanie elementów bezużytecznych (GC) w ASP.NET Core

Autorzy [Sébastien ros](https://github.com/sebastienros) i [Rick Anderson](https://twitter.com/RickAndMSFT)

Zarządzanie pamięcią jest złożone, nawet w zarządzanym środowisku, takim jak .NET. Analizowanie i rozwiązywanie problemów z pamięcią może być trudne. W tym artykule:

* Został umotywowany przez wiele *przecieków pamięci* i *nie działa* problem z GC. Większość z tych problemów została spowodowana przez niezrozumienie, jak zużycie pamięci działa w programie .NET Core, lub nie zrozumienie, jak to jest mierzone.
* Pokazuje problematyczne użycie pamięci i sugeruje alternatywne podejścia.

## <a name="how-garbage-collection-gc-works-in-net-core"></a>Jak działa wyrzucanie elementów bezużytecznych w programie .NET Core

GC przypisuje segmenty sterty, w których każdy segment jest ciągłym zakresem pamięci. Obiekty umieszczone w stercie są podzielone na jedną z trzech generacji: 0, 1 lub 2. Generacja określa częstotliwość, z jaką system GC próbuje zwolnić pamięć na zarządzanych obiektach, do których nie odwołuje się już aplikacja. Niższe numerowane generacji są częścią pamięci.

Obiekty są przenoszone z jednej generacji na inną w zależności od ich okresu istnienia. Gdy obiekty są już na żywo, są przenoszone do wyższego poziomu generacji. Jak wspomniano wcześniej, wyższe generacji są krótsze. Niekrótkoterminowe obiekty pozostają zawsze w generacji 0. Na przykład obiekty, do których istnieją odwołania w czasie trwania żądania sieci Web, są krótkotrwałe. [Pojedyncze](xref:fundamentals/dependency-injection#service-lifetimes) aplikacje są zwykle migrowane do generacji 2.

Po uruchomieniu aplikacji ASP.NET Core, GC:

* Rezerwuje pewną ilość pamięci dla początkowych segmentów sterty.
* Zatwierdza małą część pamięci podczas ładowania środowiska uruchomieniowego.

Powyższe alokacje pamięci są wykonywane ze względu na wydajność. Korzyść wydajności pochodzi z segmentów sterty w ciągłej pamięci.

### <a name="call-gccollect"></a>Wywołaj metodę GC. Kolekcjonowa

Wywoływanie [GC. Zbierz](xref:System.GC.Collect*) jawnie:

* **Nie** należy wykonywać według ASP.NET Core produkcyjnych aplikacji.
* Jest przydatne podczas badania przecieków pamięci.
* Podczas badania program sprawdza, czy moduł GC usunął wszystkie obiekty zawieszonego z pamięci, aby można było mierzyć pamięć.

## <a name="analyzing-the-memory-usage-of-an-app"></a>Analizowanie użycia pamięci przez aplikację

Narzędzia dedykowane ułatwiają Analizowanie użycia pamięci:

- Liczenie odwołań do obiektów
- Mierzenie, ile ma wpływ na wykorzystanie procesora CPU przez moduł GC
- Mierzenie przestrzeni pamięci używanej dla każdej generacji

Użyj następujących narzędzi do analizowania użycia pamięci:

* [dotnet-Trace](/dotnet/core/diagnostics/dotnet-trace): może być używany na maszynach produkcyjnych.
* [Analizowanie użycia pamięci bez debugera programu Visual Studio](/visualstudio/profiling/memory-usage-without-debugging2)
* [Użycie pamięci profilu w programie Visual Studio](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a>Wykrywanie problemów z pamięcią

Za pomocą Menedżera zadań można uzyskać informacje o ilości pamięci używanej przez aplikację ASP.NET. Wartość pamięci Menedżera zadań:

* Przedstawia ilość pamięci używanej przez proces ASP.NET.
* Obejmuje żywe obiekty i innych odbiorców pamięci, takich jak użycie pamięci natywnej.

Jeśli wartość pamięci Menedżera zadań zwiększy się w nieskończoność i nigdy nie zostanie spłaszczona, aplikacja ma przeciek pamięci. W poniższych sekcjach przedstawiono i wyjaśniono kilka wzorców użycia pamięci.

## <a name="sample-display-memory-usage-app"></a>Przykładowa aplikacja do wyświetlania pamięci

[Przykładowa aplikacja MemoryLeak](https://github.com/sebastienros/memoryleak) jest dostępna w witrynie GitHub. Aplikacja MemoryLeak:

* Obejmuje kontroler diagnostyczny, który zbiera dane pamięci w czasie rzeczywistym i GC dla aplikacji.
* Zawiera stronę indeksu wyświetlającą pamięć i dane GC. Strona indeks jest odświeżana co sekundę.
* Zawiera kontroler interfejsu API, który udostępnia różne wzorce obciążenia pamięci.
* Nie jest to obsługiwane narzędzie, ale może służyć do wyświetlania wzorców użycia pamięci ASP.NET Core aplikacji.

Uruchom MemoryLeak. Przydzieloną pamięć powoli wzrasta do momentu wystąpienia wykazu globalnego. Pamięć rośnie, ponieważ narzędzie przydziela obiekt niestandardowy do przechwytywania danych. Na poniższej ilustracji przedstawiono stronę indeksu MemoryLeak w przypadku wystąpienia generacji 0 GC. Wykres pokazuje 0 RPS pliku (liczba żądań na sekundę), ponieważ nie wywołano punktów końcowych interfejsu API z kontrolera interfejsu API.

![Poprzedni wykres](memory/_static/0RPS.png)

Na wykresie są wyświetlane dwie wartości użycia pamięci:

- Przydzielone: ilość pamięci zajętej przez zarządzane obiekty
- [Zestaw roboczy](/windows/win32/memory/working-set): zestaw stron w wirtualnej przestrzeni adresowej procesu, który jest obecnie rezydentem pamięci fizycznej. Wyświetlany zestaw roboczy jest taka sama jak wartość w Menedżerze zadań.

### <a name="transient-objects"></a>Obiekty przejściowe

Poniższy interfejs API tworzy wystąpienie ciągu 10 KB i zwraca go do klienta. W przypadku każdego żądania nowy obiekt zostaje przydzielony w pamięci i zapisany w odpowiedzi. Ciągi są przechowywane jako znaki UTF-16 w programie .NET, więc każdy znak pobiera 2 bajty w pamięci.

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

Poniższy wykres jest generowany z stosunkowo małym obciążeniem, aby pokazać, w jaki sposób przydziały pamięci mają wpływ system GC.

![Poprzedni wykres](memory/_static/bigstring.png)

Powyższy wykres przedstawia:

* 4K RPS pliku (żądania na sekundę).
* Kolekcje GC generacji 0 są wykonywane co dwa sekundy.
* Zestaw roboczy jest stały o około 500 MB.
* Procesor CPU wynosi 12%.
* Użycie pamięci i wydanie (za pomocą GC) jest stabilne.

Na poniższym wykresie przedstawiono maksymalną przepływność, która może być obsługiwana przez maszynę.

![Poprzedni wykres](memory/_static/bigstring2.png)

Powyższy wykres przedstawia:

* 22K RPS PLIKU
* Kolekcje GC generacji 0 są wykonywane kilka razy na sekundę.
* Kolekcje 1 generacji są wyzwalane, ponieważ aplikacja przydzieliła znacznie więcej pamięci na sekundę.
* Zestaw roboczy jest stały o około 500 MB.
* Procesor CPU wynosi 33%.
* Użycie pamięci i wydanie (za pomocą GC) jest stabilne.
* Procesor CPU (33%) nie jest nadmiernie wykorzystane, dlatego wyrzucanie elementów bezużytecznych może obsłużyć dużą liczbę alokacji.

### <a name="workstation-gc-vs-server-gc"></a>Stacja robocza GC a serwer GC

Moduł wyrzucania elementów bezużytecznych platformy .NET ma dwa różne tryby:

* **Stacja robocza GC**: zoptymalizowana dla pulpitu.
* **Serwer GC**. Domyślna wartość GC dla aplikacji ASP.NET Core. Zoptymalizowany pod kątem serwera.

Tryb GC można jawnie ustawić w pliku projektu lub w *runtimeconfig.jsna* pliku opublikowanej aplikacji. Następujące znaczniki pokazują ustawienia `ServerGarbageCollection` w pliku projektu:

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

Zmiana `ServerGarbageCollection` w pliku projektu wymaga odbudowania aplikacji.

**Uwaga:** Zbieranie elementów bezużytecznych serwera **nie** jest dostępne na maszynach z jednym rdzeniem. Aby uzyskać więcej informacji, zobacz <xref:System.Runtime.GCSettings.IsServerGC>.

Na poniższej ilustracji przedstawiono profil pamięci w ramach 5 K RPS pliku przy użyciu usługi Stacja robocza GC.

![Poprzedni wykres](memory/_static/workstation.png)

Różnice między tym wykresem a wersją serwera są istotne:

- Zestaw roboczy spadnie od 500 MB do 70 MB.
- GC wykonuje wiele kolekcji generacji 0 na sekundę, a nie co dwa sekundy.
- Wykaz globalny spadnie od 300 MB do 10 MB.

W typowym środowisku serwera sieci Web użycie procesora CPU jest ważniejsze niż pamięć, dlatego serwer GC jest lepszy. Jeśli użycie pamięci jest wysokie i użycie procesora CPU jest stosunkowo niskie, stacja robocza GC może być bardziej wydajna. Na przykład duża gęstość hostowanie kilku aplikacji sieci Web, w których ilość pamięci jest niestateczna.

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a>GC przy użyciu platformy Docker i małych kontenerów

Gdy wiele aplikacji kontenerowych jest uruchomionych na jednym komputerze, stacja robocza GC może być bardziej niezależna od serwera GC. Aby uzyskać więcej informacji, zobacz [Uruchamianie z serwerem GC w niewielkim kontenerze](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) i [Uruchamianie z serwerem GC w niewielkim scenariuszu kontenera część 1 — stały limit dla sterty GC](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).

### <a name="persistent-object-references"></a>Trwałe odwołania do obiektów

GC nie może zwolnić obiektów, do których istnieją odwołania. Obiekty, do których istnieją odwołania, ale nie są już potrzebne, powodują przeciek pamięci. Jeśli aplikacja często przydziela obiekty i nie może ich zwolnić, gdy nie są już potrzebne, użycie pamięci zwiększy się z upływem czasu.

Poniższy interfejs API tworzy wystąpienie ciągu 10 KB i zwraca go do klienta. Różnica w poprzednim przykładzie polega na tym, że to wystąpienie jest przywoływane przez statyczną składową, co oznacza, że nigdy nie jest dostępna dla kolekcji.

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
* Częste wywołania powodują, że pamięć aplikacji wzrasta do momentu awarii procesu z `OutOfMemory` wyjątkiem.

![Poprzedni wykres](memory/_static/eternal.png)

Na powyższym obrazie:

* Testowanie obciążenia `/api/staticstring` punktu końcowego powoduje wzrost liniowy w pamięci.
* Proces GC próbuje zwolnić pamięć w miarę wzrostu ilości pamięci, wywołując kolekcję generacji 2.
* GC nie może zwolnić ilości pamięci. Alokacja i zestaw roboczy zwiększają się wraz z czasem.

Niektóre scenariusze, takie jak buforowanie, wymagają, aby odwołania do obiektów były przechowywane do momentu wymuszenia zwolnienia pamięci. <xref:System.WeakReference>Klasa może być używana dla tego typu kodu buforowania. `WeakReference`Obiekt jest zbierany pod ciśnieniem pamięci. Domyślna implementacja programu <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> `WeakReference` .

### <a name="native-memory"></a>Pamięć natywna

Niektóre obiekty .NET Core są zależne od pamięci natywnej. Pamięć natywna **nie** może być zbierana przez GC. Obiekt .NET używający pamięci natywnej musi być bezpłatny przy użyciu kodu natywnego.

Platforma .NET udostępnia <xref:System.IDisposable> interfejs, dzięki któremu deweloperzy mogą zwolnić pamięć natywną. Nawet jeśli <xref:System.IDisposable.Dispose*> nie jest wywoływana, prawidłowo zaimplementowane klasy są wywoływane `Dispose` po uruchomieniu [finalizatora](/dotnet/csharp/programming-guide/classes-and-structs/destructors) .

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

Na poniższej ilustracji przedstawiono profil pamięci podczas `fileprovider` ciągłego wywoływania interfejsu API.

![Poprzedni wykres](memory/_static/fileprovider.png)

Powyższy wykres przedstawia oczywisty problem z implementacją tej klasy, ponieważ zwiększa użycie pamięci. Jest to znany problem, który jest śledzony w [tym problemie](https://github.com/dotnet/aspnetcore/issues/3110).

Ten sam wyciek może wystąpić w kodzie użytkownika, wykonując jedną z następujących czynności:

* Nie zwalniaj klasy prawidłowo.
* Zapominanie o wywołaniu `Dispose` metody obiektów zależnych, które powinny zostać usunięte.

### <a name="large-objects-heap"></a>Sterta dużych obiektów

Częste alokacje pamięci/wolne cykle mogą fragmentacji pamięci, szczególnie podczas przydzielania dużych fragmentów pamięci. Obiekty są przydzielono w ciągłych blokach pamięci. W celu ograniczenia fragmentacji, gdy pamięć podwolna zostanie zwolniona, Trys ją do defragmentacji. Ten proces jest nazywany **kompaktowania**. Kompaktowanie obejmuje przeniesienie obiektów. Przeniesienie dużych obiektów nakłada spadek wydajności. Z tego powodu w wykazie globalnym tworzona jest specjalna strefa pamięci dla _dużych_ obiektów, nazywana [stertą dużego obiektu](/dotnet/standard/garbage-collection/large-object-heap) (LOH). Obiekty, które są większe niż 85 000 bajtów (około 83 KB) są następujące:

* Umieszczone na LOH.
* Nie kompaktuje.
* Zbierane podczas operacje odzyskiwania pamięci generacji 2.

Gdy LOH jest zapełniony, GC wywoła kolekcję generacji 2. Kolekcje 2 generacji:

* Są z założenia powolne.
* Dodatkowo Powiąż koszt wyzwolenia kolekcji na wszystkich innych generacjach.

Następujący kod kompaktuje LOH od razu:

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

Zobacz <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> , aby uzyskać informacje na temat kompaktowania LOH.

W kontenerach korzystających z platformy .NET Core 3,0 i nowszych LOH jest automatycznie kompaktowana.

Poniższy interfejs API, który ilustruje to zachowanie:

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

Na poniższym wykresie przedstawiono profil pamięci wywołania `/api/loh/84975` punktu końcowego w obszarze maksymalne obciążenie:

![Poprzedni wykres](memory/_static/loh1.png)

Na poniższym wykresie przedstawiono profil pamięci wywołania `/api/loh/84976` punktu końcowego, przydzielanie *tylko jednego bajtu*:

![Poprzedni wykres](memory/_static/loh2.png)

Uwaga: `byte[]` Struktura zawiera bajty dodatkowe. Dlatego 84 976 bajtów wyzwala limit 85 000.

Porównanie dwóch wcześniejszych wykresów:

* Zestaw roboczy jest podobny do obu scenariuszy, około 450 MB.
* W obszarze żądania LOH (84 975 bajtów) przedstawiono większość kolekcji generacji 0.
* Żądania over LOH generują kolekcje stałej generacji 2. Kolekcje generacji 2 są kosztowne. Wymagany jest większy procesor CPU, a przepływność spadnie niemal 50% czasu.

Tymczasowe duże obiekty są szczególnie problematyczne, ponieważ powodują Gen2 operacje odzyskiwania pamięci.

W celu uzyskania maksymalnej wydajności należy zminimalizować użycie dużego obiektu. Jeśli to możliwe, rozdziel duże obiekty. Na przykład buforowanie z pamięci [podręcznej](xref:performance/caching/response) w ASP.NET Core podzielić wpisy pamięci podręcznej na bloki mniejsze niż 85 000 bajtów.

Następujące linki pokazują ASP.NET Core podejście do zachowywania obiektów w LOH limicie:

* [ResponseCaching/strumienie/StreamUtilities. cs](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [ResponseCaching/MemoryResponseCache. cs](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

Aby uzyskać więcej informacji, zobacz:

* [Niekryte sterta dużego obiektu](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Sterta dużych obiektów](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a>HttpClient

Nieprawidłowe użycie <xref:System.Net.Http.HttpClient> może skutkować wyciekiem zasobów. Zasoby systemowe, takie jak połączenia z bazami danych, gniazda, uchwyty plików itp.:

* Jest bardziej nieograniczony niż pamięć.
* Są bardziej problematyczne w przypadku przecieków od pamięci.

Doświadczeni Deweloperzy platformy .NET wiedzą, jak odwoływać się do <xref:System.IDisposable.Dispose*> obiektów, które implementują <xref:System.IDisposable> . Nieusuwania obiektów, które implementują `IDisposable` zwykle, powoduje przeciek pamięci lub przeciek zasobów systemu.

`HttpClient` implementuje `IDisposable` , ale **nie** powinien być usuwany przy każdym wywołaniu. Należy raczej `HttpClient` ponownie użyć.

Następujący punkt końcowy tworzy i usuwa nowe  `HttpClient` wystąpienie dla każdego żądania:

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

W obszarze obciążenie rejestrowane są następujące komunikaty o błędach:

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

Nawet jeśli `HttpClient` wystąpienia zostaną usunięte, rzeczywiste połączenie sieciowe zajmuje trochę czasu, aby zwolnić przez system operacyjny. Nieustannie tworząc nowe połączenia, nastąpi _wyczerpanie portów_ . Każde połączenie klienta wymaga własnego portu klienta.

Jednym ze sposobów zapobiegania wyczerpaniu portów jest ponowne użycie tego samego `HttpClient` wystąpienia:

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

`HttpClient`Wystąpienie jest wydawany, gdy aplikacja zostanie zatrzymana. Ten przykład pokazuje, że nie każdy zasób jednorazowy powinien zostać usunięty po każdym użyciu.

Aby lepiej obsługiwać okres istnienia wystąpienia, zobacz następujące tematy `HttpClient` :

* [HttpClient i zarządzanie okresem istnienia](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [Blog dotyczący fabryki HTTPClient](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a>Buforowanie obiektów

W poprzednim przykładzie pokazano, jak `HttpClient` wystąpienie może być statyczne i ponownie używane przez wszystkie żądania. Ponowne użycie uniemożliwia uruchomienie zasobów.

Buforowanie obiektów:

* Używa wzorca ponownego użycia.
* Jest przeznaczony dla obiektów, które są kosztowne do utworzenia.

Pula to kolekcja wstępnie zainicjowanych obiektów, które można zarezerwować i zwolnić między wątkami. Pule mogą definiować reguły alokacji, takie jak limity, wstępnie zdefiniowane rozmiary lub szybkość wzrostu.

Pakiet NuGet [Microsoft. Extensions. Objectpool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) zawiera klasy, które pomagają zarządzać takimi pulami.

Następujący punkt końcowy interfejsu API tworzy wystąpienie `byte` buforu, który jest wypełniony liczbami losowymi dla każdego żądania:

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

Na poniższym wykresie przedstawiono wywoływanie poprzedzającego interfejsu API o umiarkowanym obciążeniu:

![Poprzedni wykres](memory/_static/array.png)

Na poprzednim wykresie kolekcje generacji 0 są wykonywane około raz na sekundę.

Poprzedni kod można zoptymalizować przez buforowanie `byte` buforu przy użyciu [ \<T> ArrayPool](xref:System.Buffers.ArrayPool`1). Wystąpienie statyczne jest ponownie używane między żądaniami.

Różni się to od tego, czy obiekt w puli jest zwracany z interfejsu API. Oznacza to:

* Obiekt jest poza kontrolką zaraz po powrocie z metody.
* Nie można zwolnić obiektu.

Aby skonfigurować usuwanie obiektu:

* Hermetyzuj tablicę w puli w obiekcie jednorazowym.
* Zarejestruj obiekt w puli za pomocą obiektu [HttpContext. Response. RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).

`RegisterForDispose` zajmiemy się wywoływaniem `Dispose` obiektu docelowego, tak aby był on wydawany tylko po zakończeniu żądania HTTP.

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

Zastosowanie tego samego obciążenia co wersja niebędąca w puli powoduje, że na poniższym wykresie:

![Poprzedni wykres](memory/_static/pooledarray.png)

Główną różnicą jest przydzieloną liczbę bajtów, a jako wiele mniejszych kolekcji generacji 0.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Odzyskiwanie pamięci](/dotnet/standard/garbage-collection/)
* [Zrozumienie różnych trybów GC przy użyciu wizualizatora współbieżności](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [Niekryte sterta dużego obiektu](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Sterta dużych obiektów](/dotnet/standard/garbage-collection/large-object-heap)
