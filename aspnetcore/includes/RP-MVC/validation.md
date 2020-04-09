<!-- USED in RP and MVC tutorial -->

## <a name="add-validation-rules-to-the-movie-model"></a>Dodawanie reguł sprawdzania poprawności do modelu filmu

Obszar nazw DataAnnotations zawiera zestaw wbudowanych atrybutów sprawdzania poprawności, które są stosowane deklaratywnie do klasy lub właściwości. DataAnnotations zawiera również atrybuty formatowania, takie jak, `DataType` które pomagają w formatowaniu i nie zapewniają sprawdzania poprawności.

Zaktualizuj `Movie` klasę, aby `Required`skorzystać `StringLength` `RegularExpression`z `Range` wbudowanych atrybutów , , i sprawdzania poprawności.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

Atrybuty sprawdzania poprawności określają zachowanie, które chcesz wymusić we właściwościach modelu, do których są stosowane:

* `Required` Atrybuty `MinimumLength` i atrybuty wskazują, że właściwość musi mieć wartość; ale nic nie stoi na przeszkodzie, aby użytkownik wejdząc do białego miejsca, aby spełnić tę walidację.
* Atrybut `RegularExpression` jest używany do ograniczania, jakie znaki mogą być wprowadzane. W poprzednim kodzie "Genre":

  * Może używać tylko liter.
  * Pierwsza litera musi być wielką literą. Białe znaki, liczby i znaki specjalne nie są dozwolone.

* "Ocena": `RegularExpression`

  * Wymaga, aby pierwszy znak był wielką literą.
  * Umożliwia znaki specjalne i liczby w kolejnych spacjach. "PG-13" jest ważny dla oceny, ale nie dla "Gatunku".

* Atrybut `Range` ogranicza wartość do określonego zakresu.
* Atrybut `StringLength` umożliwia ustawienie maksymalnej długości właściwości ciągu i opcjonalnie jego minimalnej długości.
* Typy wartości `decimal`(takie `float` `DateTime`jak , `int`, , ) są `[Required]` z natury wymagane i nie potrzebują atrybutu.

Automatyczne wymuszanie reguł sprawdzania poprawności przez ASP.NET Core pomaga uczynić aplikację bardziej niezawodną. Zapewnia również, że nie można zapomnieć, aby sprawdzić poprawność czegoś i przypadkowo wpuścić złe dane do bazy danych.
