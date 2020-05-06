---
title: Tworzenie pierwszej Blazor aplikacji
author: guardrex
description: Kompiluj Blazor aplikację krok po kroku.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/20/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-blazor-app
ms.openlocfilehash: 5a5a56ee646cba21a883df2cf686cb1ccb18d7f9
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776607"
---
# <a name="build-your-first-blazor-app"></a>Tworzenie pierwszej Blazor aplikacji

Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

W tym samouczku pokazano, jak utworzyć i zmodyfikować Blazor aplikację.

## <a name="build-components"></a>Składniki kompilacji

1. Postępuj zgodnie ze wskazówkami zawartymi <xref:blazor/get-started> w Blazor artykule, aby utworzyć projekt dla tego samouczka. Nazwij projekt *todolist*.

1. Przejdź do każdej z trzech stron aplikacji w folderze *Pages* : Home, Counter i Fetch Data. Te strony są implementowane przez Razor pliki składników *index. Razor*, *Counter. Razor*i *FetchData. Razor*.

1. Na stronie licznik wybierz przycisk **kliknij** , aby zwiększyć licznik bez odświeżania strony. Zwiększenie licznika na stronie sieci Web zwykle wymaga pisania kodu JavaScript. W Blazorprogramie można napisać w języku C#.

1. Sprawdzanie implementacji `Counter` składnika w pliku *Counter. Razor* .

   *Pages/Counter. Razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   Interfejs użytkownika `Counter` składnika jest definiowany przy użyciu języka HTML. Logika renderowania dynamicznego (na przykład pętle, warunkowe, wyrażenia) jest dodawana przy użyciu osadzonej [Razor](xref:mvc/views/razor)składni języka C# o nazwie. Logika renderowania w kodzie HTML i języka C# jest konwertowana na klasę składnika w czasie kompilacji. Nazwa wygenerowanej klasy .NET jest zgodna z nazwą pliku.

   Elementy członkowskie klasy składnika są zdefiniowane w `@code` bloku. W `@code` bloku, stan składnika (właściwości, pola) i metody są określone dla obsługi zdarzeń lub do definiowania innej logiki składnika. Te elementy członkowskie są następnie używane jako część logiki renderowania składnika i dla zdarzeń obsługi.

   Po wybraniu przycisku **kliknij do mnie** :

   * `Counter` Zarejestrowana `onclick` procedura obsługi jest wywoływana ( `IncrementCount` Metoda).
   * `Counter` Składnik ponownie generuje drzewo renderowania.
   * Nowe drzewo renderowania jest porównywane z poprzednią.
   * Stosowane są tylko modyfikacje Document Object Model (DOM). Wyświetlana liczba jest aktualizowana.

1. Zmodyfikuj logikę języka C# `Counter` składnika, aby zwiększyć liczbę o dwa zamiast jednego.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Skompiluj ponownie i uruchom aplikację, aby zobaczyć zmiany. Wybierz przycisk **kliknij mnie** . Licznik jest zwiększany o dwa.

## <a name="use-components"></a>Używanie składników

Uwzględnij składnik w innym składniku przy użyciu składni języka HTML.

1. `Counter` Dodaj składnik do `Index` składnika aplikacji, dodając `<Counter />` element do `Index` składnika (*index. Razor*).

   Blazor Jeśli używasz zestawu webassembly do obsługi tego środowiska, `SurveyPrompt` składnik jest używany przez `Index` komponent. Zastąp `<SurveyPrompt>` element `<Counter />` elementem. Jeśli używasz aplikacji Blazor serwera do tego środowiska, Dodaj `<Counter />` element do `Index` składnika:

   *Pages/index. Razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. Skompiluj ponownie i uruchom aplikację. `Index` Składnik ma swój własny licznik.

## <a name="component-parameters"></a>Parametry składnika

Składniki mogą także mieć parametry. Parametry składnika są definiowane przy użyciu właściwości publicznych w klasie składnika z `[Parameter]` atrybutem. Użyj atrybutów, aby określić argumenty dla składnika w znaczniku.

1. Zaktualizuj kod `@code` C# składnika w następujący sposób:

   * Dodaj publiczną `IncrementAmount` właściwość z `[Parameter]` atrybutem.
   * Zmień `IncrementCount` metodę, aby użyć `IncrementAmount` właściwości podczas zwiększania wartości. `currentCount`

   *Pages/Counter. Razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. Określ `IncrementAmount` parametr w `Index` `<Counter>` elemencie składnika przy użyciu atrybutu. Ustaw wartość, aby zwiększyć licznik o dziesięć.

   *Pages/index. Razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. Załaduj `Index` ponownie składnik. Licznik jest zwiększany o dziesięć za każdym razem, gdy jest zaznaczony przycisk **kliknij mnie** . Licznik w `Counter` składniku kontynuuje zwiększanie o jeden.

## <a name="route-to-components"></a>Kierowanie do składników

Dyrektywa w górnej części pliku *Counter. Razor* określa, że `Counter` składnik jest punktem końcowym routingu. `@page` `Counter` Składnik obsługuje żądania wysyłane do `/counter`. Bez `@page` dyrektywy składnik nie obsługuje rozesłanych żądań, ale składnik nadal może być używany przez inne składniki.

## <a name="dependency-injection"></a>Wstrzykiwanie zależności

### <a name="blazor-server-experience"></a>BlazorŚrodowisko serwera

W `Startup.ConfigureServices`przypadku pracy z Blazor aplikacją serwera `WeatherForecastService` usługa jest rejestrowana jako [Pojedyncza](xref:fundamentals/dependency-injection#service-lifetimes) . Wystąpienie usługi jest dostępne w całej aplikacji za pośrednictwem [iniekcji zależności (di)](xref:fundamentals/dependency-injection):

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

`@inject` Dyrektywa służy do wstrzykiwania wystąpienia `WeatherForecastService` usługi do `FetchData` składnika.

*Strony/FetchData. Razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

`FetchData` Składnik używa wstrzykniętej usługi jako `ForecastService`, aby pobrać tablicę `WeatherForecast` obiektów:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>BlazorŚrodowisko zestawu webassembly

W przypadku pracy z Blazor aplikacją webassembly `HttpClient` wprowadza się w celu uzyskania danych prognozy pogody z pliku *Pogoda. JSON* w folderze *wwwroot/Sample-Data* .

*Strony/FetchData. Razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

[`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) Pętla służy do renderowania każdego wystąpienia prognozy jako wiersza w tabeli danych o pogodzie:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a>Tworzenie listy zadań do wykonania

Dodaj do aplikacji nowy składnik implementujący prostą listę zadań do wykonania.

1. Dodaj nowy `Todo` Razor składnik do aplikacji w folderze *strony* . W programie Visual Studio kliknij prawym przyciskiem myszy folder **strony** i wybierz polecenie **Dodaj** > **nowy element**  >  ** Razor .** Nadaj nazwę plikowi do *zrobienia. Razor*. W innych środowiskach programistycznych Dodaj pusty plik do folderu **Pages** o nazwie do *zrobienia. Razor*.

1. Podaj początkowe znaczniki dla składnika:

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. Dodaj `Todo` składnik do paska nawigacyjnego.

   `NavMenu` Składnik (*Shared/NavMenu. Razor*) jest używany w układzie aplikacji. Układy są składnikami, które umożliwiają uniknięcie duplikowania zawartości w aplikacji.

   Dodaj `<NavLink>` element dla `Todo` składnika, dodając następujący znacznik elementu listy poniżej istniejących elementów listy w pliku *Shared/NavMenu. Razor* :

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Skompiluj ponownie i uruchom aplikację. Odwiedź stronę nowych zadań do wykonania, aby upewnić się, `Todo` że łącze do składnika działa.

1. Dodaj plik *TodoItem.cs* do katalogu głównego projektu, aby pomieścić klasę reprezentującą element do wykonania. Użyj następującego kodu w języku C# dla `TodoItem` klasy:

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Wróć do `Todo` składnika (*strony/do zrobienia. Razor*):

   * Dodaj pole do zadań do wykonania w `@code` bloku. `Todo` Składnik używa tego pola do obsługi stanu listy zadań do wykonania.
   * Dodaj znaczniki listy nieuporządkowane i `foreach` pętlę, aby renderować każdy element do wykonania jako element listy`<li>`().

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. Aplikacja wymaga elementów interfejsu użytkownika do dodawania do listy elementów do wykonania. Dodaj tekst wejściowy (`<input>`) i przycisk (`<button>`) poniżej listy nieuporządkowanej (`<ul>...</ul>`):

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Skompiluj ponownie i uruchom aplikację. Po wybraniu przycisku **Dodaj do zrobienia** nic się nie dzieje, ponieważ program obsługi zdarzeń nie jest w trybie przewodowym.

1. Dodaj `AddTodo` metodę do `Todo` składnika i zarejestruj ją w celu wybrania opcji przy użyciu `@onclick` atrybutu. Metoda `AddTodo` C# jest wywoływana, gdy przycisk jest zaznaczony:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Aby uzyskać tytuł nowego elementu do wykonania, `newTodo` należy dodać pole ciągu u góry `@code` bloku i powiązać je z wartością tekstu wejściowego przy użyciu `bind` atrybutu w `<input>` elemencie:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Zaktualizuj `AddTodo` metodę, aby dodać `TodoItem` z określonym tytułem do listy. Wyczyść wartość pola wprowadzanie tekstu przez ustawienie `newTodo` do pustego ciągu:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Skompiluj ponownie i uruchom aplikację. Dodaj do listy zadań do zrobienia elementy do wykonania, aby przetestować nowy kod.

1. Tekst tytułu dla każdego elementu do wykonania można edytować, a pole wyboru może pomóc użytkownikowi śledzić elementy ukończone. Dodaj pole wyboru dla każdego elementu do wykonania i powiąż jego wartość z `IsDone` właściwością. Zmień `@todo.Title` na `<input>` element powiązany z `@todo.Title`:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Aby sprawdzić, czy te wartości są powiązane, zaktualizuj `<h3>` nagłówek, aby pokazać liczbę elementów do wykonania, które nie zostały zakończone (`IsDone` is `false`).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Ukończony `Todo` składnik (*strony/do zrobienia. Razor*):

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Skompiluj ponownie i uruchom aplikację. Dodaj elementy do wykonania, aby przetestować nowy kod.

> [!div class="nextstepaction"]
> <xref:blazor/components>
