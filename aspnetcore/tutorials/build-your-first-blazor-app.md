---
title: Tworzenie pierwszej Blazor aplikacji
author: guardrex
description: Tworzenie Blazor aplikacji krok po kroku.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/20/2020
no-loc:
- Blazor
uid: tutorials/first-blazor-app
ms.openlocfilehash: 138057c2ceb9ed01bdf958c01f5cf2275387df23
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79989431"
---
# <a name="build-your-first-opno-locblazor-app"></a>Tworzenie pierwszej Blazor aplikacji

Autorstwa [Daniela Rotha](https://github.com/danroth27) i [Luke'a Lathama](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

W tym samouczku pokazano, Blazor jak skompilować i zmodyfikować aplikację.

## <a name="build-components"></a>Buduj komponenty

1. Postępuj zgodnie <xref:blazor/get-started> ze wskazówkami Blazor zawartymi w artykule, aby utworzyć projekt dla tego samouczka. Nazwij projekt *ToDoList*.

1. Przejdź do każdej z trzech stron aplikacji w folderze *Strony:* Dane główne, Licznik i Pobieranie. Strony te są realizowane przez pliki składowe Razor *Index.brzytwa*, *Counter.brzytwa*i *FetchData.brzytwa.*

1. Na stronie Licznik wybierz przycisk **Kliknij mnie,** aby zwiększać licznik bez odświeżania strony. Zwiększanie licznika na stronie sieci Web zwykle wymaga pisania javascript. Za Blazorpomocą , można napisać C# zamiast.

1. Sprawdź implementację `Counter` składnika w pliku *Counter.brzytwa.*

   *Strony/Counter.brzytwa*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   Interfejs użytkownika składnika `Counter` jest zdefiniowany przy użyciu kodu HTML. Dynamiczna logika renderowania (na przykład pętle, warunki, wyrażenia) jest dodawana przy użyciu osadzonej składni języka C# o nazwie [Razor](xref:mvc/views/razor). Logika renderowania znaczników HTML i C# są konwertowane na klasę składników w czasie kompilacji. Nazwa wygenerowanej klasy .NET jest zgodna z nazwą pliku.

   Elementy członkowskie klasy komponentu `@code` są definiowane w bloku. W `@code` bloku stan składnika (właściwości, pola) i metody są określone dla obsługi zdarzeń lub do definiowania innych logiki składnika. Te elementy członkowskie są następnie używane jako część logiki renderowania składnika i do obsługi zdarzeń.

   Po wybraniu przycisku **Kliknij mnie:**

   * Wywoływany `Counter` jest `onclick` program obsługi zarejestrowany `IncrementCount` przez składnik (metoda).
   * Składnik `Counter` regeneruje jego drzewa renderowania.
   * Nowe drzewo renderowania jest porównywane z poprzednim.
   * Stosowane są tylko modyfikacje modelu obiektowego dokumentu (DOM). Wyświetlana liczba zostanie zaktualizowana.

1. Zmodyfikuj `Counter` logikę C# składnika, aby liczba zwiększała się o dwa zamiast o jeden.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Odbuduj i uruchom aplikację, aby zobaczyć zmiany. Wybierz przycisk **Kliknij mnie.** Licznik zwiększa się o dwa.

## <a name="use-components"></a>Użyj komponentów

Dołącz składnik do innego składnika przy użyciu składni HTML.

1. Dodaj `Counter` składnik do składnika `Index` aplikacji, `<Counter />` dodając element `Index` do składnika *(Index.brzytwa*).

   Jeśli używasz Blazor WebAssembly dla tego `SurveyPrompt` środowiska, składnik `Index` jest używany przez składnik. Zamień `<SurveyPrompt>` element `<Counter />` na element. Jeśli używasz aplikacji Blazor serwera dla tego środowiska, `<Counter />` dodaj `Index` element do składnika:

   *Strony/Index.brzytwa*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. Odbuduj i uruchom aplikację. Składnik `Index` ma swój własny licznik.

## <a name="component-parameters"></a>Parametry komponentu

Komponenty mogą również mieć parametry. Parametry komponentu są definiowane przy użyciu `[Parameter]` właściwości publicznych w klasie składnika z atrybutem. Atrybuty służy do określania argumentów dla składnika w znacznikach.

1. Zaktualizuj `@code` kod C# składnika w następujący sposób:

   * Dodaj właściwość publiczną `IncrementAmount` z atrybutem. `[Parameter]`
   * Zmień `IncrementCount` metodę, aby `IncrementAmount` użyć właściwości podczas `currentCount`zwiększania wartości .

   *Strony/Counter.brzytwa*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. Określ `IncrementAmount` parametr `Index` w elemencie składnika `<Counter>` przy użyciu atrybutu. Ustaw wartość, aby zwiększać licznik o dziesięć.

   *Strony/Index.brzytwa*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. Przeładuj `Index` ponownie komponent. Licznik zwiększa się o dziesięć za każdym razem, gdy zaznaczony jest przycisk **Kliknij mnie.** Licznik w `Counter` komponencie nadal zwiększa się o jeden.

## <a name="route-to-components"></a>Droga do komponentów

Dyrektywa `@page` w górnej części pliku *Counter.brzytwa* określa, że `Counter` składnik jest punktem końcowym routingu. Składnik `Counter` obsługuje żądania wysyłane `/counter`do . Bez `@page` dyrektywy składnik nie obsługuje kierowanych żądań, ale składnik może nadal być używany przez inne składniki.

## <a name="dependency-injection"></a>Wstrzykiwanie zależności

### <a name="opno-locblazor-server-experience"></a>BlazorDoświadczenie serwera

W przypadku Blazor pracy z `WeatherForecastService` aplikacją Server usługa jest `Startup.ConfigureServices`rejestrowana jako [singleton](xref:fundamentals/dependency-injection#service-lifetimes) w pliku . Wystąpienie usługi jest dostępne w całej aplikacji za pośrednictwem [iniekcji zależności (DI)](xref:fundamentals/dependency-injection):

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

Dyrektywa `@inject` jest używana do wstrzykiwania `WeatherForecastService` wystąpienia `FetchData` usługi do składnika.

*Strony/FetchData.brzytwa:*

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

Składnik `FetchData` używa wtryskiwacza `ForecastService`usługi, jak , `WeatherForecast` aby pobrać tablicę obiektów:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="opno-locblazor-webassembly-experience"></a>BlazorWebAssembly doświadczenie

Podczas pracy Blazor z aplikacją WebAssembly jest wstrzykiwany w `HttpClient` celu uzyskania danych prognozy pogody z pliku *weather.json* w folderze *wwwroot/sample-data.*

*Strony/FetchData.brzytwa:*

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

Pętla służy do renderowania [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) każdego wystąpienia prognozy jako wiersza w tabeli danych pogodowych:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a>Tworzenie listy zadań do wykonania

Dodaj nowy składnik do aplikacji, który implementuje prostą listę zadań do wykonania.

1. Dodaj nowy `Todo` składnik Razor do aplikacji w folderze *Strony.* W programie Visual Studio kliknij prawym przyciskiem myszy folder **Strony** i wybierz polecenie **Dodaj** > **nowy składnik razor****elementu** > . Nazwij plik składnika *Todo.brzytwa*. W innych środowiskach programistyzuj pusty plik do folderu **Strony** o nazwie *Todo.brzytwa*.

1. Podaj początkowe znaczniki dla składnika:

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. Dodaj `Todo` składnik do paska nawigacyjnego.

   Składnik `NavMenu` *(Shared/NavMenu.brzytwa)* jest używany w układzie aplikacji. Układy są składnikami, które pozwalają uniknąć powielania zawartości w aplikacji.

   Dodaj `<NavLink>` element dla `Todo` składnika, dodając następujące znaczniki elementu listy poniżej istniejących elementów listy w pliku *Shared/NavMenu.razor:*

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Odbuduj i uruchom aplikację. Odwiedź nową stronę Todo, aby potwierdzić, że link do komponentu `Todo` działa.

1. Dodaj plik *TodoItem.cs* do katalogu głównego projektu, aby pomieścić klasę reprezentującą element do wykonania. Użyj następującego kodu C# dla `TodoItem` klasy:

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Powrót do `Todo` komponentu (*Pages/Todo.brzytwa):*

   * Dodaj pole dla elementów do `@code` wykonania w bloku. Składnik `Todo` używa tego pola do zachowania stanu listy zadań do wykonania.
   * Dodaj nieuiszczone znaczniki `foreach` listy i pętlę, aby uczynić każdy element todo jako element listy (`<li>`).

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. Aplikacja wymaga elementów interfejsu użytkownika do dodawania elementów todo do listy. Dodaj wprowadzanie`<input>`tekstu ( )`<button>`i przycisk ( )`<ul>...</ul>`poniżej listy nieuporządkowanych ( ):

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Odbuduj i uruchom aplikację. Po wybraniu przycisku **Dodaj todo** nic się nie dzieje, ponieważ program obsługi zdarzeń nie jest podłączony do przycisku.

1. Dodaj `AddTodo` metodę do `Todo` składnika i zarejestruj ją `@onclick` dla wyborów przycisków przy użyciu atrybutu. Metoda `AddTodo` C# jest wywoływana, gdy przycisk jest zaznaczony:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Aby uzyskać tytuł nowego elementu todo, `newTodo` dodaj pole ciągu `@code` w górnej części bloku i powiąż je z wartością wprowadzania tekstu przy użyciu atrybutu `bind` w elemencie: `<input>`

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Zaktualizuj `AddTodo` `TodoItem` metodę, aby dodać z określonym tytułem do listy. Wyczyść wartość wprowadzania `newTodo` tekstu, ustawiając pusty ciąg:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Odbuduj i uruchom aplikację. Dodaj kilka elementów todo do listy zadań do wykonania, aby przetestować nowy kod.

1. Tekst tytułu dla każdego elementu todo można edytować, a pole wyboru może pomóc użytkownikowi śledzić ukończone elementy. Dodaj dane wejściowe pola wyboru dla każdego elementu `IsDone` todo i powiąż jego wartość z właściwością. Zmiana `@todo.Title` elementu `<input>` powiązanego `@todo.Title`z:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Aby sprawdzić, czy te wartości `<h3>` są powiązane, zaktualizuj nagłówek, aby wyświetlić liczbę elementów do wykonania, które nie zostały ukończone (`IsDone` jest `false`).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Wypełniony `Todo` komponent (*Pages/Todo.brzytwa):*

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Odbuduj i uruchom aplikację. Dodaj elementy todo, aby przetestować nowy kod.

> [!div class="nextstepaction"]
> <xref:blazor/components>
