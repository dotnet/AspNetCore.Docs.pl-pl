---
title: zaawansowane scenariusze Blazor ASP.NET Core
author: guardrex
description: Dowiedz się więcej Blazoro zaawansowanych scenariuszach w , w tym jak włączyć ręczną logikę RenderTreeBuilder do aplikacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 5edbbe36e8389bac0335594b1e4331aee1c02867
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659454"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a>zaawansowane scenariusze ASP.NET Core Blazor

Autorstwa [Luke'a Lathama](https://github.com/guardrex) i [Daniela Rotha](https://github.com/danroth27)

## <a name="blazor-server-circuit-handler"></a>Obsługa obwodów serwera Blazor

Blazor Server pozwala na zdefiniowanie programu *obsługi obwodów,* który umożliwia uruchamianie kodu na zmiany stanu obwodu użytkownika. Program obsługi obwodu jest implementowany przez wyprowadzanie `CircuitHandler` i rejestrowanie klasy w kontenerze usługi aplikacji. Poniższy przykład obsługi obwodu śledzi otwarte połączenia SignalR:

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> _circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => _circuits.Count;
}
```

Programy obsługi obwodów są rejestrowane przy użyciu DI. Wystąpienia o określonym zakresie są tworzone na wystąpienie obwodu. `TrackingCircuitHandler` W poprzednim przykładzie tworzona jest usługa singleton, ponieważ stan wszystkich obwodów musi być śledzony:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

Jeśli metody niestandardowego programu obsługi obwodów zgłaszają nieobsługiowany wyjątek, wyjątek jest krytyczny dla obwodu serwera Blazor. Aby tolerować wyjątki w kodzie programu obsługi lub metody wywoływane, zawiń kod w co najmniej jednej [instrukcji try-catch](/dotnet/csharp/language-reference/keywords/try-catch) z obsługi błędów i rejestrowania.

Po zakończeniu obwodu, ponieważ użytkownik został odłączony, a struktura czyści stan obwodu, struktura usuwa zakres DI obwodu. Pozbywanie się zakresu usuwa wszystkie usługi DI <xref:System.IDisposable?displayProperty=fullName>o zakresie obwodu, które implementują . Jeśli dowolna usługa DI zgłasza nieobsługiowany wyjątek podczas likwidacji, struktura rejestruje wyjątek.

## <a name="manual-rendertreebuilder-logic"></a>Ręczna logika RenderTreeBuilder

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder`udostępnia metody manipulowania składników i elementów, w tym składników budynku ręcznie w kodzie C#.

> [!NOTE]
> Użycie `RenderTreeBuilder` do tworzenia składników jest scenariuszem zaawansowanym. Nieprawidłowo sformułowany składnik (na przykład niezamknięty znacznik znaczników) może spowodować niezdefiniowane zachowanie.

Należy wziąć `PetDetails` pod uwagę następujący składnik, który może być ręcznie wbudowany w inny składnik:

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

W poniższym przykładzie pętla w `CreateComponent` `PetDetails` metodzie generuje trzy składniki. Podczas `RenderTreeBuilder` wywoływania metod tworzenia`OpenComponent` `AddAttribute`składników ( i ), numery sekwencji są numery wierszy kodu źródłowego. Algorytm różnicy Blazor opiera się na numery sekwencji odpowiadające różnych wierszy kodu, a nie różne wywołania wywołania. Podczas tworzenia składnika `RenderTreeBuilder` z metodami, hardcode argumenty dla numerów sekwencji. **Przy użyciu obliczeń lub licznika do generowania numeru sekwencyjnyego może prowadzić do niskiej wydajności.** Aby uzyskać więcej informacji, zobacz [numery sekwencji odnoszą się do numerów wierszy kodu, a nie do](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) sekcji zlecenia wykonania.

`BuiltContent`Składnik:

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> Typy `Microsoft.AspNetCore.Components.RenderTree` w zezwalają na przetwarzanie *wyników* operacji renderowania. Są to wewnętrzne szczegóły implementacji ram Blazor. Te typy należy uznać za *niestabilne* i mogą ulec zmianie w przyszłych wersjach.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Numery sekwencji odnoszą się do numerów wierszy kodu, a nie do zlecenia wykonania

Pliki komponentów razor (*.brzytwa*) są zawsze kompilowane. Kompilacja jest potencjalną przewagą nad interpretacją kodu, ponieważ krok kompilacji może służyć do wstrzykiwania informacji, które poprawia wydajność aplikacji w czasie wykonywania.

Kluczowym przykładem tych ulepszeń są *numery sekwencji*. Numery sekwencji wskazują do środowiska wykonawczego, które dane wyjściowe pochodzą z których różne i uporządkowane wiersze kodu. Środowisko wykonawcze używa tych informacji do generowania efektywnych różnic kursowych drzewa w czasie liniowym, co jest znacznie szybsze niż zwykle jest to możliwe dla ogólnego algorytmu różnicowego drzewa.

Rozważmy następujący plik komponentu Razor (*.brzytwa):*

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

Poprzedni kod kompiluje się do czegoś następującego:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Gdy kod jest wykonywany po raz `someFlag` `true`pierwszy, jeśli jest , konstruktor odbiera:

| Sequence | Typ      | Dane   |
| :------: | --------- | :----: |
| 0        | Węzeł tekstowy | First  |
| 1        | Węzeł tekstowy | Sekunda |

Wyobraź `someFlag` sobie, że staje się `false`, a znaczniki są renderowane ponownie. Tym razem budowniczy otrzymuje:

| Sequence | Typ       | Dane   |
| :------: | ---------- | :----: |
| 1        | Węzeł tekstowy  | Sekunda |

Gdy środowisko wykonawcze wykonuje diff, widzi, że `0` element w sekwencji został usunięty, więc generuje następujący skrypt *edycji*trywialne:

* Usuń pierwszy węzeł tekstowy.

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>Problem z programowym generowaniem numerów sekwencyjnych

Wyobraź sobie, że zamiast tego napisałeś następującą logikę konstruktora drzew renderowania:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

Teraz pierwsze wyjście to:

| Sequence | Typ      | Dane   |
| :------: | --------- | :----: |
| 0        | Węzeł tekstowy | First  |
| 1        | Węzeł tekstowy | Sekunda |

Wynik ten jest identyczny z poprzednim przypadkiem, więc nie istnieją żadne negatywne problemy. `someFlag`znajduje `false` się na drugim renderowaniu, a dane wyjściowe są następujące:

| Sequence | Typ      | Dane   |
| :------: | --------- | ------ |
| 0        | Węzeł tekstowy | Sekunda |

Tym razem algorytm różnicowy widzi, że wystąpiły *dwie* zmiany, a algorytm generuje następujący skrypt edycji:

* Zmień wartość pierwszego węzła `Second`tekstowego na .
* Usuń drugi węzeł tekstowy.

Generowanie numerów sekwencji utracił wszystkie przydatne `if/else` informacje o tym, gdzie gałęzie i pętle były obecne w oryginalnym kodzie. Powoduje to dyferwę **dwa razy dłużej** niż wcześniej.

To trywialny przykład. W bardziej realistycznych przypadkach ze złożonymi i głęboko zagnieżdżonymi strukturami, a zwłaszcza w przypadku pętli, koszt wydajności jest zwykle wyższy. Zamiast natychmiast zidentyfikować, które bloki pętli lub gałęzie zostały wstawione lub usunięte, algorytm różnicujący musi ponownie wciągać się głęboko w drzewa renderowania. Zwykle powoduje to konieczność tworzenia dłuższych skryptów edycji, ponieważ algorytm różnicowy jest błędnie poinformowany o tym, jak stare i nowe struktury odnoszą się do siebie nawzajem.

### <a name="guidance-and-conclusions"></a>Wytyczne i wnioski

* Wydajność aplikacji cierpi, jeśli numery sekwencji są generowane dynamicznie.
* Struktura nie można utworzyć własne numery sekwencji automatycznie w czasie wykonywania, ponieważ niezbędne informacje nie istnieje, chyba że jest przechwycony w czasie kompilacji.
* Nie zapisuj długich bloków logiki implementowanych `RenderTreeBuilder` ręcznie. Preferuj pliki *.brzytwa* i pozwól kompilatorowi radzić sobie z numerami sekwencji. Jeśli nie możesz uniknąć `RenderTreeBuilder` logiki ręcznej, podziel długie bloki `OpenRegion` / `CloseRegion` kodu na mniejsze kawałki zawinięte w wywołania. Każdy region ma własną oddzielną przestrzeń numerów sekwencyjnych, dzięki czemu można ponownie uruchomić od zera (lub dowolną inną dowolną liczbę) wewnątrz każdego regionu.
* Jeśli numery sekwencji są zakodowane na stałe, algorytm różnicowy wymaga tylko, aby numery sekwencji zwiększyć wartość. Wartość początkowa i luki są nieistotne. Jedną z legalnych opcji jest użycie numeru wiersza kodu jako numeru sekwencyjnyego lub rozpoczęcia od zera i zwiększenia o jeden lub setki (lub dowolnego preferowanego interwału). 
* Blazorużywa numerów sekwencji, podczas gdy inne struktury interfejsu użytkownika różnicujące drzewa nie używają ich. Diffing jest znacznie szybsze, gdy Blazor numery sekwencji są używane i ma tę zaletę, krok kompilacji, który zajmuje się numery sekwencji automatycznie dla programistów tworzenia plików *.brzytwa.*

## <a name="perform-large-data-transfers-in-opno-locblazor-server-apps"></a>Wykonywanie dużych transferów danych w Blazor aplikacjach serwera

W niektórych scenariuszach duże ilości danych muszą być Blazorprzesyłane między javascript i . Zazwyczaj duże transfery danych występują, gdy:

* Interfejsy API systemu plików przeglądarki służą do przekazywania lub pobierania pliku.
* Wymagany jest interop z biblioteką innej firmy.

W Blazor serwerze ograniczenie jest w miejscu, aby zapobiec przekazywaniu pojedynczych dużych wiadomości, które mogą powodować problemy z wydajnością.

Podczas opracowywania kodu, który przesyła dane Blazormiędzy javascript i:

* Podziel dane na mniejsze kawałki i wysyłaj segmenty danych sekwencyjnie, aż wszystkie dane nie będą odbierane przez serwer.
* Nie przydzielaj dużych obiektów w kodzie JavaScript i C#.
* Nie blokuj głównego wątku interfejsu użytkownika przez długi czas podczas wysyłania lub odbierania danych.
* Zwolnić pamięć zużytą po zakończeniu lub anulowaniu procesu.
* Wymuszaj następujące dodatkowe wymagania ze względów bezpieczeństwa:
  * Zadeklaruj maksymalny rozmiar pliku lub danych, który może zostać przekazany.
  * Zadeklarować minimalną szybkość przekazywania z klienta do serwera.
* Po odebraniu danych przez serwer dane mogą być:
  * Tymczasowo przechowywane w buforze pamięci, dopóki nie zostaną zebrane wszystkie segmenty.
  * Zużyte natychmiast. Na przykład dane mogą być natychmiast przechowywane w bazie danych lub zapisywane na dysku podczas odnoszenia każdego segmentu.

Następująca klasa przekazywania plików obsługuje JS interop z klientem. Klasa przesyłająca używa JS interop do:

* Sonduj klienta, aby wysłać segment danych.
* Przerwij transakcję, jeśli limit czasu sondowania.

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private readonly int _segmentSize = 6144;
    private readonly int _maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> _thisReference;
    private List<IMemoryOwner<byte>> _uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await _jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)_segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await _jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < _maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(_segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, _segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          _uploadedSegments.Add(current);
        }

        var segments = _uploadedSegments;
        _uploadedSegments = null;

        return new SegmentedStream(segments, _segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (_uploadedSegments != null)
        {
            foreach (var segment in _uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

W poprzednim przykładzie:

* Jest `_maxBase64SegmentSize` ustawiona `8192`na , `_maxBase64SegmentSize = _segmentSize * 4 / 3`który jest obliczany od .
* Interfejsy API zarządzania pamięcią .NET Core niskiego poziomu służą `_uploadedSegments`do przechowywania segmentów pamięci na serwerze w programie .
* Metoda `ReceiveFile` jest używana do obsługi przekazywania za pośrednictwem js interop:
  * Rozmiar pliku jest określany w bajtach `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`za pośrednictwem JS interop z .
  * Liczba segmentów do odebrania jest `numberOfSegments`obliczana i przechowywana w pliku .
  * Segmenty są wymagane w `for` pętli przez JS `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`interop z . Wszystkie segmenty, ale ostatni musi być 8,192 bajtów przed dekodowaniem. Klient jest zmuszony do wysyłania danych w skuteczny sposób.
  * Dla każdego odebranego segmentu kontrole <xref:System.Convert.TryFromBase64String*>są wykonywane przed dekodowaniem za pomocą pliku .
  * Strumień z danymi jest zwracany <xref:System.IO.Stream> `SegmentedStream`jako nowy ( ) po zakończeniu przekazywania.

Klasa strumienia segmentowego udostępnia listę segmentów jako nieszukalną <xref:System.IO.Stream>tylko w przypadku odczytu:

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> _sequence;
    private long _currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            _sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        _sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(_currentPosition + count < _sequence.Length ? 
            count : _sequence.Length - _currentPosition);
        var data = _sequence.Slice(_currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        _currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

Następujący kod implementuje funkcje JavaScript do odbierania danych:

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```
