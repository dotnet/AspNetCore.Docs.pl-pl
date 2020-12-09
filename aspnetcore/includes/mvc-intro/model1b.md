Dodaj następujące właściwości do `Movie` klasy:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

`Movie`Klasa zawiera:

* `Id`Pole, które jest wymagane przez bazę danych dla klucza podstawowego.
* `[DataType(DataType.Date)]`: Atrybut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) określa typ danych ( `Date` ). Z tym atrybutem:

  * Użytkownik nie musi wprowadzać informacji o czasie w polu Data.
  * Tylko data jest wyświetlana, a nie informacje o czasie.

[Adnotacje DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) są omówione w kolejnym samouczku.