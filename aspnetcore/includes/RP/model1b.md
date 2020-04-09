<!-- THIS INCLUDE USED BY MVC AND RP -->
<span data-ttu-id="c9cd3-101">Dodaj następujące właściwości do `Movie` klasy:</span><span class="sxs-lookup"><span data-stu-id="c9cd3-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="c9cd3-102">Klasa `Movie` zawiera:</span><span class="sxs-lookup"><span data-stu-id="c9cd3-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="c9cd3-103">Pole `ID` jest wymagane przez bazę danych dla klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="c9cd3-103">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="c9cd3-104">`[DataType(DataType.Date)]`: Atrybut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) określa typ danych (Data).</span><span class="sxs-lookup"><span data-stu-id="c9cd3-104">`[DataType(DataType.Date)]`:  The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date).</span></span> <span data-ttu-id="c9cd3-105">Z tym atrybutem:</span><span class="sxs-lookup"><span data-stu-id="c9cd3-105">With this attribute:</span></span>

  * <span data-ttu-id="c9cd3-106">Użytkownik nie musi wprowadzać informacji o godzinie w polu daty.</span><span class="sxs-lookup"><span data-stu-id="c9cd3-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="c9cd3-107">Wyświetlana jest tylko data, a nie informacje o czasie.</span><span class="sxs-lookup"><span data-stu-id="c9cd3-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="c9cd3-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) są omówione w późniejszym samouczku.</span><span class="sxs-lookup"><span data-stu-id="c9cd3-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>
