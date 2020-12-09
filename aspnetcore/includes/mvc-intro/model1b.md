<span data-ttu-id="41cf9-101">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="41cf9-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="41cf9-102">`Movie`Klasa zawiera:</span><span class="sxs-lookup"><span data-stu-id="41cf9-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="41cf9-103">`Id`Pole, które jest wymagane przez bazę danych dla klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="41cf9-103">The `Id` field which is required by the database for the primary key.</span></span>
* <span data-ttu-id="41cf9-104">`[DataType(DataType.Date)]`: Atrybut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) określa typ danych ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="41cf9-104">`[DataType(DataType.Date)]`:  The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="41cf9-105">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="41cf9-105">With this attribute:</span></span>

  * <span data-ttu-id="41cf9-106">Użytkownik nie musi wprowadzać informacji o czasie w polu Data.</span><span class="sxs-lookup"><span data-stu-id="41cf9-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="41cf9-107">Tylko data jest wyświetlana, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="41cf9-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="41cf9-108">[Adnotacje DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) są omówione w kolejnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="41cf9-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>