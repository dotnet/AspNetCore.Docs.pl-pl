<span data-ttu-id="bb1eb-101">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="bb1eb-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="bb1eb-102">Klasa `Movie` zawiera:</span><span class="sxs-lookup"><span data-stu-id="bb1eb-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="bb1eb-103">Pole `Id` wymagane przez bazę danych dla klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="bb1eb-103">The `Id` field which is required by the database for the primary key.</span></span>
* <span data-ttu-id="bb1eb-104">`[DataType(DataType.Date)]`: Atrybut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) określa typ danych (`Date`).</span><span class="sxs-lookup"><span data-stu-id="bb1eb-104">`[DataType(DataType.Date)]`:  The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="bb1eb-105">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="bb1eb-105">With this attribute:</span></span>

  * <span data-ttu-id="bb1eb-106">Użytkownik nie musi wprowadzać informacji o godzinie w polu daty.</span><span class="sxs-lookup"><span data-stu-id="bb1eb-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="bb1eb-107">Wyświetlana jest tylko data, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="bb1eb-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="bb1eb-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) są omówione w późniejszym samouczku.</span><span class="sxs-lookup"><span data-stu-id="bb1eb-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>