<!-- THIS INCLUDE USED BY MVC AND RP -->
Dodaj następujące właściwości do `Movie` klasy:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

Klasa `Movie` zawiera:

* Pole `ID` jest wymagane przez bazę danych dla klucza podstawowego.
* `[DataType(DataType.Date)]`: Atrybut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) określa typ danych (Data). Z tym atrybutem:

  * Użytkownik nie musi wprowadzać informacji o godzinie w polu daty.
  * Wyświetlana jest tylko data, a nie informacje o czasie.

[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) są omówione w późniejszym samouczku.
