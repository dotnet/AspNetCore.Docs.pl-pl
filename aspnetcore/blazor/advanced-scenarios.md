---
title: "ASP.NET Core Blazor scenariuszy zaawansowanych" autor: Opis: "informacje o zaawansowanych scenariuszach w programie Blazor , w tym o sposobie włączania ręcznej logiki RenderTreeBuilder do aplikacji".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---
# <a name="aspnet-core-blazor-advanced-scenarios"></a>BlazorZaawansowane scenariusze ASP.NET Core

Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)

## <a name="blazor-server-circuit-handler"></a>BlazorProcedura obsługi obwodu serwera

BlazorSerwer umożliwia kodowi Definiowanie *procedury obsługi obwodu*, która umożliwia uruchamianie kodu na zmiany stanu obwodu użytkownika. Procedura obsługi obwodu jest implementowana przez wyprowadzanie z `CircuitHandler` i rejestrowanie klasy w kontenerze usługi aplikacji. Poniższy przykład obsługi obwodu śledzi otwarte SignalR połączenia:

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => circuits.Count;
}
```

Procedury obsługi obwodu są rejestrowane przy użyciu funkcji DI. Wystąpienia w zakresie są tworzone na wystąpienie obwodu. Korzystając z `TrackingCircuitHandler` powyższego przykładu, tworzona jest usługa singleton, ponieważ stan wszystkich obwodów musi być śledzony:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

Jeśli metody obsługi niestandardowego obwodu zgłaszają nieobsługiwany wyjątek, wyjątek jest krytyczny dla Blazor obwodu serwera. Aby tolerować wyjątki w kodzie programu obsługi lub metodach wywoływanych, zawiń kod w co najmniej jednej instrukcji [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) z obsługą błędów i rejestrowaniem.

Gdy obwód kończy się, ponieważ użytkownik odłączył się i struktura czyści stan obwodu, struktura usuwa zakres DI obwodu. Oddysponowanie zakresu polega na usunięciu wszelkich usług DI-Scope w zakresie, które implementują <xref:System.IDisposable?displayProperty=fullName> . Jeśli jakakolwiek usługa nie zgłasza nieobsłużonego wyjątku podczas usuwania, struktura rejestruje wyjątek.

## <a name="manual-rendertreebuilder-logic"></a>Ręczna logika RenderTreeBuilder

<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>zapewnia metody manipulowania składnikami i elementami, w tym ręczne Kompilowanie składników w kodzie C#.

> [!NOTE]
> Korzystanie z programu <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> do tworzenia składników jest zaawansowanym scenariuszem. Nieprawidłowo sformułowany składnik (na przykład niezamknięty tag znacznika) może spowodować niezdefiniowane zachowanie.

Rozważmy następujący `PetDetails` składnik, który można ręcznie utworzyć w innym składniku:

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

W poniższym przykładzie pętla w `CreateComponent` metodzie generuje trzy `PetDetails` składniki. Podczas wywoływania <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> metod tworzenia składników ( `OpenComponent` i `AddAttribute` ) numery sekwencji są numerami wierszy kodu źródłowego. BlazorAlgorytm różnic polega na numerach sekwencji odpowiadających odrębnym wierszom kodu, a nie odrębnym wywoływaniu wywołań. Podczas tworzenia składnika przy użyciu <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> metod umieszczaj argumenty dla numerów sekwencji. **Użycie obliczenia lub licznika do wygenerowania numeru sekwencji może prowadzić do niskiej wydajności.** Aby uzyskać więcej informacji, zobacz sekcję [numery sekwencji powiązane z numerami wierszy kodu i kolejnością niewykonania](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .

`BuiltContent`składnika

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
> Typy w programie <xref:Microsoft.AspNetCore.Components.RenderTree> umożliwiają przetwarzanie *wyników* operacji renderowania. Są to wewnętrzne szczegóły Blazor implementacji platformy. Te typy powinny być uznawane za *niestabilne* i mogą ulec zmianie w przyszłych wersjach.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Numery sekwencji odnoszą się do numerów wierszy kodu, a nie kolejności wykonywania

RazorPliki składników (*. Razor*) są zawsze kompilowane. Kompilacja jest potencjalną możliwością przekroczenia interpretacji kodu, ponieważ krok kompilacji może służyć do iniekcji informacji, które zwiększają wydajność aplikacji w czasie wykonywania.

Najważniejszym przykładem tych ulepszeń są *numery sekwencji*. Numery sekwencji wskazują na środowisko uruchomieniowe, które pochodzą z różnych i uporządkowanych wierszy kodu. Środowisko uruchomieniowe używa tych informacji do generowania wydajnych różnic drzewa w czasie liniowym, które są znacznie szybsze niż zwykle jest to możliwe dla algorytmu różnicowego drzewa ogólnego.

Weź pod uwagę następujący Razor plik składnika (*Razor*):

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

Poprzedni kod kompiluje się w taki sposób, aby wyglądał następująco:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Gdy kod jest wykonywany po raz pierwszy, jeśli `someFlag` jest `true` , Konstruktor odbiera:

| Sequence | Typ      | Dane   |
| :---
title: "ASP.NET Core Blazor scenariuszy zaawansowanych" autor: Opis: "informacje o zaawansowanych scenariuszach w programie Blazor , w tym o sposobie włączania ręcznej logiki RenderTreeBuilder do aplikacji".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---: | ---title: "ASP.NET Core Blazor zaawansowane scenariusze" Author: Description: "Poznaj zaawansowane scenariusze w programie Blazor , w tym sposób włączania ręcznej logiki RenderTreeBuilder do aplikacji".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor scenariuszy zaawansowanych" autor: Opis: "informacje o zaawansowanych scenariuszach w programie Blazor , w tym o sposobie włączania ręcznej logiki RenderTreeBuilder do aplikacji".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

----- | :----: | | 0 | Węzeł tekstu | Pierwszy | | 1 | Węzeł tekstu | Sekundę |

Wyobraź sobie `someFlag` , że zostanie ona `false` przerenderowana, a znaczniki są renderowane ponownie. Tym razem Konstruktor odbiera:

| Sequence | Typ       | Dane   |
| :---
title: "ASP.NET Core Blazor scenariuszy zaawansowanych" autor: Opis: "informacje o zaawansowanych scenariuszach w programie Blazor , w tym o sposobie włączania ręcznej logiki RenderTreeBuilder do aplikacji".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---: | ---title: "ASP.NET Core Blazor zaawansowane scenariusze" Author: Description: "Poznaj zaawansowane scenariusze w programie Blazor , w tym sposób włączania ręcznej logiki RenderTreeBuilder do aplikacji".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor scenariuszy zaawansowanych" autor: Opis: "informacje o zaawansowanych scenariuszach w programie Blazor , w tym o sposobie włączania ręcznej logiki RenderTreeBuilder do aplikacji".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor scenariuszy zaawansowanych" autor: Opis: "informacje o zaawansowanych scenariuszach w programie Blazor , w tym o sposobie włączania ręcznej logiki RenderTreeBuilder do aplikacji".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

----- | :----: | | 1 | Węzeł tekstu | Sekundę |

Gdy środowisko uruchomieniowe wykonuje porównanie, zobaczy, że element w sekwencji `0` został usunięty, więc generuje następujący *skrypt*uproszczonej edycji:

* Usuń pierwszy węzeł tekstu.

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>Problem z programowo generowanymi numerami sekwencji

Wyobraź sobie, że została zapisana następująca logika konstruktora drzewa renderowania:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

Teraz pierwsze dane wyjściowe to:

| Sequence | Typ      | Dane   |
| :---
title: "ASP.NET Core Blazor scenariuszy zaawansowanych" autor: Opis: "informacje o zaawansowanych scenariuszach w programie Blazor , w tym o sposobie włączania ręcznej logiki RenderTreeBuilder do aplikacji".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---: | ---title: "ASP.NET Core Blazor zaawansowane scenariusze" Author: Description: "Poznaj zaawansowane scenariusze w programie Blazor , w tym sposób włączania ręcznej logiki RenderTreeBuilder do aplikacji".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor scenariuszy zaawansowanych" autor: Opis: "informacje o zaawansowanych scenariuszach w programie Blazor , w tym o sposobie włączania ręcznej logiki RenderTreeBuilder do aplikacji".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

----- | :----: | | 0 | Węzeł tekstu | Pierwszy | | 1 | Węzeł tekstu | Sekundę |

Ten wynik jest identyczny z poprzednim przypadkiem, dlatego nie istnieją żadne negatywne problemy. `someFlag`znajduje się `false` na drugim renderingu, a dane wyjściowe:

| Sequence | Typ      | Dane   |
| :---
title: "ASP.NET Core Blazor scenariuszy zaawansowanych" autor: Opis: "informacje o zaawansowanych scenariuszach w programie Blazor , w tym o sposobie włączania ręcznej logiki RenderTreeBuilder do aplikacji".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---: | ---title: "ASP.NET Core Blazor zaawansowane scenariusze" Author: Description: "Poznaj zaawansowane scenariusze w programie Blazor , w tym sposób włączania ręcznej logiki RenderTreeBuilder do aplikacji".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

-
title: "ASP.NET Core Blazor scenariuszy zaawansowanych" autor: Opis: "informacje o zaawansowanych scenariuszach w programie Blazor , w tym o sposobie włączania ręcznej logiki RenderTreeBuilder do aplikacji".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

----- | ---title: "ASP.NET Core Blazor zaawansowane scenariusze" Author: Description: "Poznaj zaawansowane scenariusze w programie Blazor , w tym sposób włączania ręcznej logiki RenderTreeBuilder do aplikacji".
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

--- | | 0 | Węzeł tekstu | Sekundę |

Tym razem algorytm diff widzi, że pojawiły się *dwie* zmiany, a algorytm generuje następujący skrypt edycji:

* Zmień wartość pierwszego węzła tekstowego na `Second` .
* Usuń drugi węzeł tekstu.

Generowanie numerów sekwencji utraciło wszystkie przydatne informacje o tym, gdzie `if/else` znajdują się gałęzie i pętle w oryginalnym kodzie. Wynikiem tego jest różnica **dwa razy** , tak długo, jak wcześniej.

Jest to prosty przykład. W bardziej realistycznych przypadkach ze złożonymi i głęboko zagnieżdżonymi strukturami, w szczególności z pętlami, koszt wydajności jest zwykle wyższy. Zamiast natychmiastowego identyfikowania, które bloki lub gałęzie pętli zostały wstawione lub usunięte, algorytm diff musi odnosił się do drzewa renderowania. Zwykle polega to na konieczności kompilowania dłużej edytowanych skryptów, ponieważ algorytm różnicowy jest niewiadome o tym, jak stare i nowe struktury odnoszą się do siebie.

### <a name="guidance-and-conclusions"></a>Wskazówki i wnioski

* Wydajność aplikacji ma wpływ na to, że numery sekwencji są generowane dynamicznie.
* Struktura nie może automatycznie tworzyć własnych numerów sekwencji w czasie wykonywania, ponieważ niezbędne informacje nie istnieją, chyba że są przechwytywane w czasie kompilacji.
* Nie zapisuj długich bloków logiki wykonywanej ręcznie <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> . Preferuj pliki *Razor* i Zezwalaj kompilatorowi na rozpatrywanie numerów sekwencyjnych. Jeśli nie możesz uniknąć ręcznej <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logiki, Podziel długie bloki kodu na mniejsze fragmenty opakowane <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A> / <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> . Każdy region ma własne oddzielne miejsce numerów sekwencyjnych, więc można uruchomić ponownie od zera (lub dowolnego innego numeru) w każdym regionie.
* Jeśli numery sekwencji są stałee, algorytm diff wymaga tylko zwiększenia wartości sekwencji. Początkowa wartość i przerwy są nieistotne. Jedną z wiarygodnych opcji jest użycie numeru wiersza kodu jako numeru sekwencyjnego lub rozpoczęcie od zera i zwiększenie według wartości lub setek (lub dowolnego preferowanego interwału). 
* Blazorużywa numerów sekwencji, podczas gdy inne struktury interfejsu użytkownika rozróżniania drzewa nie są używane. Różnica jest znacznie szybsza, gdy są używane numery sekwencyjne i Blazor ma zalety kroku kompilacji, który zajmuje się automatycznie numerami sekwencyjnymi dla deweloperów tworzących pliki *. Razor* .

## <a name="perform-large-data-transfers-in-blazor-server-apps"></a>Wykonywanie dużych transferów danych w Blazor aplikacjach serwera

W niektórych scenariuszach należy przenieść duże ilości danych między językami JavaScript i Blazor . Zwykle duże transfery danych odbywają się w przypadku:

* Interfejsy API systemu plików przeglądarki służą do przekazywania lub pobierania pliku.
* Wymagana jest współdziałanie z biblioteką innej firmy.

Na Blazor serwerze jest stosowane ograniczenie uniemożliwiające przekazywanie pojedynczych dużych komunikatów, które mogą powodować problemy z wydajnością.

Podczas tworzenia kodu, który przesyła dane między językami JavaScript, należy wziąć pod uwagę następujące wskazówki Blazor :

* Wydziel dane na mniejsze fragmenty i Wyślij segmenty danych sekwencyjnie, dopóki wszystkie dane nie zostaną odebrane przez serwer.
* Nie przydzielaj dużych obiektów w kodzie JavaScript i C#.
* Nie blokuj głównego wątku interfejsu użytkownika przez długie okresy podczas wysyłania lub otrzymywania danych.
* Zwolnij wszystkie używane pamięci, gdy proces zostanie ukończony lub anulowany.
* Wymuś następujące dodatkowe wymagania dotyczące zabezpieczeń:
  * Zadeklaruj maksymalny rozmiar pliku lub danych, który może zostać przesłany.
  * Zadeklaruj minimalną szybkość przekazywania z klienta na serwerze.
* Po odebraniu danych przez serwer dane mogą być następujące:
  * Tymczasowo przechowywane w buforze pamięci do momentu zebrania wszystkich segmentów.
  * Wykorzystano natychmiast. Na przykład dane mogą być przechowywane bezpośrednio w bazie danych lub zapisywane na dysku w miarę odbierania poszczególnych segmentów.

Następująca Klasa obiektu przekazującego plików obsługuje program JS Interop z klientem programu. Klasa obiektu przekazującego używa programu JS Interop do:

* Przesondowaj klienta, aby wysłał segment danych.
* Przerywaj transakcję, jeśli trwa sondowanie.

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime jsRuntime;
    private readonly int segmentSize = 6144;
    private readonly int maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> thisReference;
    private List<IMemoryOwner<byte>> uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        this.jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          uploadedSegments.Add(current);
        }

        var segments = uploadedSegments;
        uploadedSegments = null;

        return new SegmentedStream(segments, segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (uploadedSegments != null)
        {
            foreach (var segment in uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

W poprzednim przykładzie:

* `maxBase64SegmentSize`Jest ustawiona na `8192` , który jest obliczany z `maxBase64SegmentSize = segmentSize * 4 / 3` .
* Interfejsy API zarządzania pamięcią programu .NET Core na niskim poziomie są używane do przechowywania segmentów pamięci na serwerze w systemie `uploadedSegments` .
* `ReceiveFile`Metoda jest używana do obsługi przekazywania za pomocą narzędzia js Interop:
  * Rozmiar pliku jest określany w bajtach za pomocą narzędzia JS Interop z `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)` .
  * Liczba segmentów do odebrania jest obliczana i przechowywana w `numberOfSegments` .
  * Segmenty są żądane w `for` pętli za pomocą narzędzia js Interop z `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)` . Wszystkie segmenty, ale ostatnie muszą mieć 8 192 bajtów przed dekodowaniem. Klient jest zmuszony do wydajnego wysyłania danych.
  * W przypadku każdego odebranego segmentu sprawdzenia są wykonywane przed dekodowaniem w <xref:System.Convert.TryFromBase64String%2A> .
  * Strumień z danymi jest zwracany jako nowy <xref:System.IO.Stream> ( `SegmentedStream` ) po zakończeniu przekazywania.

Klasa łańcucha segmentów uwidacznia listę segmentów jako niemożliwy do przeszukiwania <xref:System.IO.Stream> :

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> sequence;
    private long currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            sequence = new ReadOnlySequence<byte>(
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

        sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(currentPosition + count < sequence.Length ? 
            count : sequence.Length - currentPosition);
        var data = sequence.Slice(currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        currentPosition += bytesToWrite;

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

Poniższy kod implementuje funkcje języka JavaScript do odbierania danych:

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
