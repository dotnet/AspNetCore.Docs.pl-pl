---
title: Strony brzytwy z EF Core w rdzeniu ASP.NET - Model danych - 5 z 8
author: rick-anderson
description: W tym samouczku dodaj więcej jednostek i relacji i dostosuj model danych, określając reguły formatowania, sprawdzania poprawności i mapowania.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 1d81a0444487c6396bb32381ed2cb26d44312c3a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665719"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a><span data-ttu-id="346e2-103">Strony brzytwy z EF Core w rdzeniu ASP.NET - Model danych - 5 z 8</span><span class="sxs-lookup"><span data-stu-id="346e2-103">Razor Pages with EF Core in ASP.NET Core - Data Model - 5 of 8</span></span>

<span data-ttu-id="346e2-104">Przez [Tom Dykstra](https://github.com/tdykstra) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="346e2-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="346e2-105">Poprzednie samouczki pracował z podstawowym modelem danych, który składał się z trzech jednostek.</span><span class="sxs-lookup"><span data-stu-id="346e2-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="346e2-106">W tym samouczku:</span><span class="sxs-lookup"><span data-stu-id="346e2-106">In this tutorial:</span></span>

* <span data-ttu-id="346e2-107">Dodano więcej jednostek i relacji.</span><span class="sxs-lookup"><span data-stu-id="346e2-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="346e2-108">Model danych jest dostosowywany przez określenie reguł formatowania, sprawdzania poprawności i mapowania bazy danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="346e2-109">Ukończony model danych jest pokazany na poniższej ilustracji:</span><span class="sxs-lookup"><span data-stu-id="346e2-109">The completed data model is shown in the following illustration:</span></span>

![Diagram encji](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a><span data-ttu-id="346e2-111">Jednostka Student</span><span class="sxs-lookup"><span data-stu-id="346e2-111">The Student entity</span></span>

![Jednostka studencka](complex-data-model/_static/student-entity.png)

<span data-ttu-id="346e2-113">Zastąp kod w *Models/Student.cs* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="346e2-113">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="346e2-114">Powyższy kod dodaje `FullName` właściwość i dodaje następujące atrybuty do istniejących właściwości:</span><span class="sxs-lookup"><span data-stu-id="346e2-114">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="346e2-115">Właściwość obliczana fullname</span><span class="sxs-lookup"><span data-stu-id="346e2-115">The FullName calculated property</span></span>

<span data-ttu-id="346e2-116">`FullName`jest obliczoną właściwością, która zwraca wartość utworzoną przez łączenie dwóch innych właściwości.</span><span class="sxs-lookup"><span data-stu-id="346e2-116">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="346e2-117">`FullName`nie można ustawić, więc ma tylko akcesor get.</span><span class="sxs-lookup"><span data-stu-id="346e2-117">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="346e2-118">Żadna kolumna nie `FullName` jest tworzona w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-118">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="346e2-119">Atrybut DataType</span><span class="sxs-lookup"><span data-stu-id="346e2-119">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="346e2-120">W przypadku dat rejestracji uczniów wszystkie strony są obecnie wyświetlane o godzinie dnia wraz z datą, chociaż tylko data jest odpowiednia.</span><span class="sxs-lookup"><span data-stu-id="346e2-120">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="346e2-121">Za pomocą atrybutów adnotacji danych, można wprowadzić jedną zmianę kodu, który naprawi format wyświetlania na każdej stronie, która pokazuje dane.</span><span class="sxs-lookup"><span data-stu-id="346e2-121">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="346e2-122">[Atrybut DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) określa typ danych, który jest bardziej szczegółowy niż typ wewnętrznego bazy danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-122">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="346e2-123">W takim przypadku powinna być wyświetlana tylko data, a nie data i godzina.</span><span class="sxs-lookup"><span data-stu-id="346e2-123">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="346e2-124">[Wyliczenie datatype](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) zawiera wiele typów danych, takich jak Data, Godzina, Numer telefonu, Waluta, EmailAddress, itp. Atrybut `DataType` może również włączyć aplikację, aby automatycznie udostępniać funkcje specyficzne dla typu.</span><span class="sxs-lookup"><span data-stu-id="346e2-124">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="346e2-125">Przykład:</span><span class="sxs-lookup"><span data-stu-id="346e2-125">For example:</span></span>

* <span data-ttu-id="346e2-126">Łącze `mailto:` jest tworzone `DataType.EmailAddress`automatycznie dla pliku .</span><span class="sxs-lookup"><span data-stu-id="346e2-126">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="346e2-127">Selektor daty jest `DataType.Date` dostępny w większości przeglądarek.</span><span class="sxs-lookup"><span data-stu-id="346e2-127">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="346e2-128">Atrybut `DataType` emituje atrybuty `data-` HTML 5 (wymawiane rozdzielenie danych).</span><span class="sxs-lookup"><span data-stu-id="346e2-128">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="346e2-129">Atrybuty `DataType` nie zapewniają sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="346e2-129">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="346e2-130">Atrybut DisplayFormat</span><span class="sxs-lookup"><span data-stu-id="346e2-130">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="346e2-131">`DataType.Date`nie określa formatu daty, która jest wyświetlana.</span><span class="sxs-lookup"><span data-stu-id="346e2-131">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="346e2-132">Domyślnie pole daty jest wyświetlane zgodnie z domyślnymi formatami opartymi na [programie CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)serwera .</span><span class="sxs-lookup"><span data-stu-id="346e2-132">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="346e2-133">Atrybut `DisplayFormat` jest używany do jawnego określenia formatu daty.</span><span class="sxs-lookup"><span data-stu-id="346e2-133">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="346e2-134">Ustawienie `ApplyFormatInEditMode` określa, że formatowanie powinno być również stosowane do interfejsu użytkownika edycji.</span><span class="sxs-lookup"><span data-stu-id="346e2-134">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="346e2-135">Niektóre pola nie `ApplyFormatInEditMode`powinny używać .</span><span class="sxs-lookup"><span data-stu-id="346e2-135">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="346e2-136">Na przykład symbol waluty zazwyczaj nie powinien być wyświetlany w polu tekstowym edycji.</span><span class="sxs-lookup"><span data-stu-id="346e2-136">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="346e2-137">Atrybut `DisplayFormat` może być używany przez siebie.</span><span class="sxs-lookup"><span data-stu-id="346e2-137">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="346e2-138">Ogólnie warto użyć atrybutu `DataType` z atrybutem. `DisplayFormat`</span><span class="sxs-lookup"><span data-stu-id="346e2-138">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="346e2-139">Atrybut `DataType` przekazuje semantyki danych, w przeciwieństwie do sposobu renderowania go na ekranie.</span><span class="sxs-lookup"><span data-stu-id="346e2-139">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="346e2-140">Atrybut `DataType` zapewnia następujące korzyści, które nie `DisplayFormat`są dostępne w:</span><span class="sxs-lookup"><span data-stu-id="346e2-140">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="346e2-141">Przeglądarka może włączyć funkcje HTML5.</span><span class="sxs-lookup"><span data-stu-id="346e2-141">The browser can enable HTML5 features.</span></span> <span data-ttu-id="346e2-142">Na przykład pokaż formant kalendarza, symbol waluty odpowiednie dla ustawień regionalnych, łącza e-mail i sprawdzanie poprawności danych wejściowych po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="346e2-142">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="346e2-143">Domyślnie przeglądarka renderuje dane przy użyciu poprawnego formatu opartego na ustawieniach regionalnych.</span><span class="sxs-lookup"><span data-stu-id="346e2-143">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="346e2-144">Aby uzyskać więcej informacji, zobacz [ \<dokumentację> Pomocnika tagów](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="346e2-144">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="346e2-145">Atrybut StringLength</span><span class="sxs-lookup"><span data-stu-id="346e2-145">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="346e2-146">Reguły sprawdzania poprawności danych i komunikaty o błędach sprawdzania poprawności można określić za pomocą atrybutów.</span><span class="sxs-lookup"><span data-stu-id="346e2-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="346e2-147">[Atrybut StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) określa minimalną i maksymalną długość znaków dozwolonych w polu danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="346e2-148">Pokazany kod ogranicza nazwy do nie więcej niż 50 znaków.</span><span class="sxs-lookup"><span data-stu-id="346e2-148">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="346e2-149">Przykład, który ustawia minimalną długość ciągu, jest wyświetlany [później](#the-required-attribute).</span><span class="sxs-lookup"><span data-stu-id="346e2-149">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="346e2-150">Atrybut `StringLength` zapewnia również weryfikacji po stronie klienta i po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="346e2-150">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="346e2-151">Minimalna wartość nie ma wpływu na schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-151">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="346e2-152">Atrybut `StringLength` nie uniemożliwia użytkownikowi wprowadzania odstępu dla nazwy.</span><span class="sxs-lookup"><span data-stu-id="346e2-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="346e2-153">[Atrybut RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) może służyć do stosowania ograniczeń danych wejściowych.</span><span class="sxs-lookup"><span data-stu-id="346e2-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="346e2-154">Na przykład poniższy kod wymaga, aby pierwszy znak był wielką literą, a pozostałe znaki alfabetycznie:</span><span class="sxs-lookup"><span data-stu-id="346e2-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

# <a name="visual-studio"></a>[<span data-ttu-id="346e2-155">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="346e2-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="346e2-156">W **programie SQL Server Object Explorer** (SSOX) otwórz projektanta tabel uczniów, klikając dwukrotnie tabelę **Student.**</span><span class="sxs-lookup"><span data-stu-id="346e2-156">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabela studentów w SSOX przed migracją](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="346e2-158">Poprzedni obraz przedstawia schemat tabeli. `Student`</span><span class="sxs-lookup"><span data-stu-id="346e2-158">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="346e2-159">Pola nazwy mają `nvarchar(MAX)`typ .</span><span class="sxs-lookup"><span data-stu-id="346e2-159">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="346e2-160">Po utworzeniu i zastosowaniu migracji w dalszej `nvarchar(50)` części tego samouczka pola nazw stają się wynikiem atrybutów długości ciągu.</span><span class="sxs-lookup"><span data-stu-id="346e2-160">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="346e2-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="346e2-161">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="346e2-162">W narzędziu SQLite sprawdź definicje `Student` kolumn dla tabeli.</span><span class="sxs-lookup"><span data-stu-id="346e2-162">In your SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="346e2-163">Pola nazwy mają `Text`typ .</span><span class="sxs-lookup"><span data-stu-id="346e2-163">The name fields have type `Text`.</span></span> <span data-ttu-id="346e2-164">Należy zauważyć, że pole `FirstMidName`imienia jest wywoływane .</span><span class="sxs-lookup"><span data-stu-id="346e2-164">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="346e2-165">W następnej sekcji nazwę tej kolumny `FirstName`można zmienić na .</span><span class="sxs-lookup"><span data-stu-id="346e2-165">In the next section, you change the name of that column to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="346e2-166">Atrybut Kolumna</span><span class="sxs-lookup"><span data-stu-id="346e2-166">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="346e2-167">Atrybuty mogą kontrolować sposób mapowania klas i właściwości do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-167">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="346e2-168">W `Student` modelu `Column` atrybut jest używany do mapowania `FirstMidName` nazwy właściwości do "FirstName" w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-168">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="346e2-169">Podczas tworzenia bazy danych nazwy właściwości w modelu są używane `Column` dla nazw kolumn (z wyjątkiem sytuacji, gdy używany jest atrybut).</span><span class="sxs-lookup"><span data-stu-id="346e2-169">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="346e2-170">Model `Student` używa `FirstMidName` dla pola imienia, ponieważ pole może również zawierać drugie imię.</span><span class="sxs-lookup"><span data-stu-id="346e2-170">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="346e2-171">Za `[Column]` pomocą atrybutu `Student.FirstMidName` w modelu danych `FirstName` jest `Student` mapowana do kolumny tabeli.</span><span class="sxs-lookup"><span data-stu-id="346e2-171">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="346e2-172">Dodanie atrybutu `Column` zmienia model, który `SchoolContext`wspiera program .</span><span class="sxs-lookup"><span data-stu-id="346e2-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="346e2-173">Model kopii `SchoolContext` zapasowej nie jest już zgodny z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="346e2-174">Ta rozbieżność zostanie rozwiązana przez dodanie migracji w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="346e2-174">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="346e2-175">Wymagany atrybut</span><span class="sxs-lookup"><span data-stu-id="346e2-175">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="346e2-176">Atrybut `Required` sprawia, że właściwości nazwy wymagane pola.</span><span class="sxs-lookup"><span data-stu-id="346e2-176">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="346e2-177">Atrybut `Required` nie jest potrzebny dla typów nienastępalnych do wartości `DateTime`null, takich jak typy wartości (na przykład , `int`, i `double`).</span><span class="sxs-lookup"><span data-stu-id="346e2-177">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="346e2-178">Typy, których nie można mieć wartości null, są automatycznie traktowane jako pola wymagane.</span><span class="sxs-lookup"><span data-stu-id="346e2-178">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="346e2-179">Atrybut `Required` musi być używany `MinimumLength` z `MinimumLength` do wymuszania.</span><span class="sxs-lookup"><span data-stu-id="346e2-179">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

<span data-ttu-id="346e2-180">`MinimumLength`i `Required` zezwolić na zaspokojenie sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="346e2-180">`MinimumLength` and `Required` allow whitespace to satisfy the validation.</span></span> <span data-ttu-id="346e2-181">Użyj `RegularExpression` atrybutu dla pełnej kontroli nad ciągiem.</span><span class="sxs-lookup"><span data-stu-id="346e2-181">Use the `RegularExpression` attribute for full control over the string.</span></span>

### <a name="the-display-attribute"></a><span data-ttu-id="346e2-182">Atrybut Display</span><span class="sxs-lookup"><span data-stu-id="346e2-182">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="346e2-183">Atrybut `Display` określa, że podpis dla pól tekstowych powinien być "Imię", "Nazwisko", "Imię" i "Data rejestracji".</span><span class="sxs-lookup"><span data-stu-id="346e2-183">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="346e2-184">Domyślne podpisy nie miały miejsca dzielącego wyrazy, na przykład "Nazwisko".</span><span class="sxs-lookup"><span data-stu-id="346e2-184">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="346e2-185">Tworzenie migracji</span><span class="sxs-lookup"><span data-stu-id="346e2-185">Create a migration</span></span>

<span data-ttu-id="346e2-186">Uruchom aplikację i przejdź do strony Uczniowie.</span><span class="sxs-lookup"><span data-stu-id="346e2-186">Run the app and go to the Students page.</span></span> <span data-ttu-id="346e2-187">Wyjątek zostanie zgłoszony.</span><span class="sxs-lookup"><span data-stu-id="346e2-187">An exception is thrown.</span></span> <span data-ttu-id="346e2-188">Atrybut `[Column]` powoduje, że EF oczekiwać, `FirstName`aby znaleźć kolumnę o `FirstMidName`nazwie , ale nazwa kolumny w bazie danych jest nadal .</span><span class="sxs-lookup"><span data-stu-id="346e2-188">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="346e2-189">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="346e2-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="346e2-190">Komunikat o błędzie jest podobny do następującego przykładu:</span><span class="sxs-lookup"><span data-stu-id="346e2-190">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

* <span data-ttu-id="346e2-191">W pmc wprowadź następujące polecenia, aby utworzyć nową migrację i zaktualizować bazę danych:</span><span class="sxs-lookup"><span data-stu-id="346e2-191">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  <span data-ttu-id="346e2-192">Pierwsze z tych poleceń generuje następujący komunikat ostrzegawczy:</span><span class="sxs-lookup"><span data-stu-id="346e2-192">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="346e2-193">Ostrzeżenie jest generowane, ponieważ pola nazw są teraz ograniczone do 50 znaków.</span><span class="sxs-lookup"><span data-stu-id="346e2-193">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="346e2-194">Jeśli nazwa w bazie danych ma więcej niż 50 znaków, 51 do ostatniego znaku zostaną utracone.</span><span class="sxs-lookup"><span data-stu-id="346e2-194">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="346e2-195">Otwórz tabelę uczniów w SSOX:</span><span class="sxs-lookup"><span data-stu-id="346e2-195">Open the Student table in SSOX:</span></span>

  ![Tabela studentów w SSOX po migracji](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="346e2-197">Przed zastosowaniem migracji kolumny nazw były typu [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="346e2-197">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="346e2-198">Kolumny nazw są `nvarchar(50)`teraz .</span><span class="sxs-lookup"><span data-stu-id="346e2-198">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="346e2-199">Nazwa kolumny została `FirstMidName` zmieniona z na `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="346e2-199">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="346e2-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="346e2-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="346e2-201">Komunikat o błędzie jest podobny do następującego przykładu:</span><span class="sxs-lookup"><span data-stu-id="346e2-201">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="346e2-202">Otwórz okno polecenia w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="346e2-202">Open a command window in the project folder.</span></span> <span data-ttu-id="346e2-203">Wprowadź następujące polecenia, aby utworzyć nową migrację i zaktualizować bazę danych:</span><span class="sxs-lookup"><span data-stu-id="346e2-203">Enter the following commands to create a new migration and update the database:</span></span>

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="346e2-204">Polecenie aktualizacji bazy danych wyświetla błąd, taki jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="346e2-204">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="346e2-205">W tym samouczku sposobem na ominięcia tego błędu jest usunięcie i ponowne utworzenie migracji początkowej.</span><span class="sxs-lookup"><span data-stu-id="346e2-205">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="346e2-206">Aby uzyskać więcej informacji, zobacz notatkę o ostrzeżeniu SQLite w górnej części [samouczka migracji](xref:data/ef-rp/migrations).</span><span class="sxs-lookup"><span data-stu-id="346e2-206">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="346e2-207">Usuń folder *Migracje.*</span><span class="sxs-lookup"><span data-stu-id="346e2-207">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="346e2-208">Uruchom następujące polecenia, aby usunąć bazę danych, utworzyć nową migrację początkową i zastosować migrację:</span><span class="sxs-lookup"><span data-stu-id="346e2-208">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* <span data-ttu-id="346e2-209">Sprawdź tabelę Student w narzędziu SQLite.</span><span class="sxs-lookup"><span data-stu-id="346e2-209">Examine the Student table in your SQLite tool.</span></span> <span data-ttu-id="346e2-210">Kolumna, która była FirstMidName jest teraz FirstName.</span><span class="sxs-lookup"><span data-stu-id="346e2-210">The column that was FirstMidName is now FirstName.</span></span>

---

* <span data-ttu-id="346e2-211">Uruchom aplikację i przejdź do strony Uczniowie.</span><span class="sxs-lookup"><span data-stu-id="346e2-211">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="346e2-212">Należy zauważyć, że godziny nie są wprowadzane ani wyświetlane wraz z datami.</span><span class="sxs-lookup"><span data-stu-id="346e2-212">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="346e2-213">Wybierz **pozycję Utwórz nowy**i spróbuj wprowadzić nazwę dłuższą niż 50 znaków.</span><span class="sxs-lookup"><span data-stu-id="346e2-213">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="346e2-214">W poniższych sekcjach tworzenie aplikacji na niektórych etapach generuje błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="346e2-214">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="346e2-215">Instrukcje określają, kiedy utworzyć aplikację.</span><span class="sxs-lookup"><span data-stu-id="346e2-215">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="346e2-216">Jednostka instruktora</span><span class="sxs-lookup"><span data-stu-id="346e2-216">The Instructor Entity</span></span>

![Jednostka instruktora](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="346e2-218">Utwórz *modele/instructor.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="346e2-218">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

<span data-ttu-id="346e2-219">Wiele atrybutów może znajdować się w jednym wierszu.</span><span class="sxs-lookup"><span data-stu-id="346e2-219">Multiple attributes can be on one line.</span></span> <span data-ttu-id="346e2-220">Atrybuty `HireDate` mogą być zapisywane w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="346e2-220">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="346e2-221">Właściwości nawigacji</span><span class="sxs-lookup"><span data-stu-id="346e2-221">Navigation properties</span></span>

<span data-ttu-id="346e2-222">Właściwości `CourseAssignments` `OfficeAssignment` i są właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="346e2-222">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="346e2-223">Instruktor może uczyć dowolną liczbę kursów, więc `CourseAssignments` jest zdefiniowany jako kolekcja.</span><span class="sxs-lookup"><span data-stu-id="346e2-223">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="346e2-224">Instruktor może mieć co najwyżej jedno `OfficeAssignment` biuro, `OfficeAssignment` więc właściwość posiada jedną jednostkę.</span><span class="sxs-lookup"><span data-stu-id="346e2-224">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="346e2-225">`OfficeAssignment`jest null, jeśli nie jest przypisany żaden urząd.</span><span class="sxs-lookup"><span data-stu-id="346e2-225">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="346e2-226">Jednostka OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="346e2-226">The OfficeAssignment entity</span></span>

![Encja officeassignment](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="346e2-228">Utwórz *modele/officeassignment.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="346e2-228">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="346e2-229">Atrybut Klucz</span><span class="sxs-lookup"><span data-stu-id="346e2-229">The Key attribute</span></span>

<span data-ttu-id="346e2-230">Atrybut `[Key]` jest używany do identyfikowania właściwości jako klucz podstawowy (PK), gdy nazwa właściwości jest czymś innym niż classnameID lub ID.</span><span class="sxs-lookup"><span data-stu-id="346e2-230">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="346e2-231">Istnieje relacja jeden do zera lub jeden między `Instructor` jednostkami. `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="346e2-231">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="346e2-232">Przypisanie biura istnieje tylko w odniesieniu do instruktora, do który jest przypisany.</span><span class="sxs-lookup"><span data-stu-id="346e2-232">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="346e2-233">PK `OfficeAssignment` jest również jego klucz obcy (FK) do jednostki. `Instructor`</span><span class="sxs-lookup"><span data-stu-id="346e2-233">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span>

<span data-ttu-id="346e2-234">EF Core nie można `InstructorID` automatycznie rozpoznać `OfficeAssignment` jako `InstructorID` PK, ponieważ nie jest zgodna z konwencją nazewnictwa identyfikatora lub classnameID.</span><span class="sxs-lookup"><span data-stu-id="346e2-234">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="346e2-235">W związku `Key` z tym atrybut `InstructorID` jest używany do identyfikowania jako PK:</span><span class="sxs-lookup"><span data-stu-id="346e2-235">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="346e2-236">Domyślnie EF Core traktuje klucz jako nieogenerowana przez bazę danych, ponieważ kolumna jest dla identyfikującej relacji.</span><span class="sxs-lookup"><span data-stu-id="346e2-236">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="346e2-237">Właściwość nawigacji instruktora</span><span class="sxs-lookup"><span data-stu-id="346e2-237">The Instructor navigation property</span></span>

<span data-ttu-id="346e2-238">Właściwość `Instructor.OfficeAssignment` nawigacji może mieć wartość null, ponieważ może nie być wiersz `OfficeAssignment` dla danego instruktora.</span><span class="sxs-lookup"><span data-stu-id="346e2-238">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="346e2-239">Instruktor może nie mieć przypisania biura.</span><span class="sxs-lookup"><span data-stu-id="346e2-239">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="346e2-240">Właściwość `OfficeAssignment.Instructor` nawigacji zawsze będzie mieć jednostkę `InstructorID` instruktora, ponieważ typ klucza obcego jest `int`, typ wartości nienastępującej wartości.</span><span class="sxs-lookup"><span data-stu-id="346e2-240">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="346e2-241">Przypisanie biura nie może istnieć bez instruktora.</span><span class="sxs-lookup"><span data-stu-id="346e2-241">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="346e2-242">Gdy `Instructor` jednostka ma `OfficeAssignment` jednostkę pokrewną, każda jednostka ma odwołanie do drugiej w swojej właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="346e2-242">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="346e2-243">Jednostka kursu</span><span class="sxs-lookup"><span data-stu-id="346e2-243">The Course Entity</span></span>

![Jednostka kursu](complex-data-model/_static/course-entity.png)

<span data-ttu-id="346e2-245">Zaktualizuj *modele/Course.cs* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="346e2-245">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="346e2-246">Jednostka `Course` ma właściwość `DepartmentID`klucza obcego (FK).</span><span class="sxs-lookup"><span data-stu-id="346e2-246">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="346e2-247">`DepartmentID`wskazuje na `Department` podmiot powiązany.</span><span class="sxs-lookup"><span data-stu-id="346e2-247">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="346e2-248">Jednostka `Course` ma `Department` właściwość nawigacji.</span><span class="sxs-lookup"><span data-stu-id="346e2-248">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="346e2-249">EF Core nie wymaga właściwości klucza obcego dla modelu danych, gdy model ma właściwość nawigacji dla jednostki pokrewne.</span><span class="sxs-lookup"><span data-stu-id="346e2-249">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="346e2-250">EF Core automatycznie tworzy pliki FK w bazie danych, gdziekolwiek są potrzebne.</span><span class="sxs-lookup"><span data-stu-id="346e2-250">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="346e2-251">EF Core tworzy [właściwości cienia](/ef/core/modeling/shadow-properties) dla automatycznie tworzonych FKs.</span><span class="sxs-lookup"><span data-stu-id="346e2-251">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="346e2-252">Jednak jawnie włączenie FK w modelu danych można ułatwić aktualizacje i bardziej wydajne.</span><span class="sxs-lookup"><span data-stu-id="346e2-252">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="346e2-253">Rozważmy na przykład model, w `DepartmentID` którym właściwość FK *nie* jest uwzględniona.</span><span class="sxs-lookup"><span data-stu-id="346e2-253">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="346e2-254">Po pobraniu jednostki kursu do edycji:</span><span class="sxs-lookup"><span data-stu-id="346e2-254">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="346e2-255">Właściwość `Department` jest null, jeśli nie jest jawnie załadowany.</span><span class="sxs-lookup"><span data-stu-id="346e2-255">The `Department` property is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="346e2-256">Aby zaktualizować jednostkę `Department` kursu, jednostka musi najpierw zostać pobrana.</span><span class="sxs-lookup"><span data-stu-id="346e2-256">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="346e2-257">Gdy FK `DepartmentID` właściwość znajduje się w modelu danych, nie ma `Department` potrzeby pobierania jednostki przed aktualizacją.</span><span class="sxs-lookup"><span data-stu-id="346e2-257">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="346e2-258">Atrybut DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="346e2-258">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="346e2-259">Atrybut `[DatabaseGenerated(DatabaseGeneratedOption.None)]` określa, że PK jest dostarczany przez aplikację, a nie generowane przez bazę danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-259">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="346e2-260">Domyślnie EF Core zakłada, że wartości PK są generowane przez bazę danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-260">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="346e2-261">Generowane przez bazę danych jest zazwyczaj najlepszym rozwiązaniem.</span><span class="sxs-lookup"><span data-stu-id="346e2-261">Database-generated is generally the best approach.</span></span> <span data-ttu-id="346e2-262">Dla `Course` jednostek użytkownik określa PK.</span><span class="sxs-lookup"><span data-stu-id="346e2-262">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="346e2-263">Na przykład numer kursu, taki jak seria 1000 dla działu matematyki, seria 2000 dla działu angielskiego.</span><span class="sxs-lookup"><span data-stu-id="346e2-263">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="346e2-264">Atrybut `DatabaseGenerated` może również służyć do generowania wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="346e2-264">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="346e2-265">Na przykład baza danych może automatycznie wygenerować pole daty, aby zarejestrować datę utworzenia lub zaktualizowania wiersza.</span><span class="sxs-lookup"><span data-stu-id="346e2-265">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="346e2-266">Aby uzyskać więcej informacji, zobacz [Wygenerowane właściwości](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="346e2-266">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="346e2-267">Właściwości klucza obcego i nawigacji</span><span class="sxs-lookup"><span data-stu-id="346e2-267">Foreign key and navigation properties</span></span>

<span data-ttu-id="346e2-268">Właściwości klucza obcego (FK) i `Course` właściwości nawigacji w jednostce odzwierciedlają następujące relacje:</span><span class="sxs-lookup"><span data-stu-id="346e2-268">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="346e2-269">Kurs jest przypisany do jednego działu, `DepartmentID` więc istnieje `Department` FK i właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="346e2-269">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="346e2-270">Kurs może mieć dowolną liczbę studentów zapisanych `Enrollments` w nim, więc właściwość nawigacji jest kolekcja:</span><span class="sxs-lookup"><span data-stu-id="346e2-270">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="346e2-271">Kurs może być prowadzony przez wielu instruktorów, więc właściwość `CourseAssignments` nawigacji jest kolekcją:</span><span class="sxs-lookup"><span data-stu-id="346e2-271">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="346e2-272">`CourseAssignment`zostanie wyjaśnione [później](#many-to-many-relationships).</span><span class="sxs-lookup"><span data-stu-id="346e2-272">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="the-department-entity"></a><span data-ttu-id="346e2-273">Jednostka departamentu</span><span class="sxs-lookup"><span data-stu-id="346e2-273">The Department entity</span></span>

![Jednostka działu](complex-data-model/_static/department-entity.png)

<span data-ttu-id="346e2-275">Utwórz *modele/department.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="346e2-275">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a><span data-ttu-id="346e2-276">Atrybut Kolumna</span><span class="sxs-lookup"><span data-stu-id="346e2-276">The Column attribute</span></span>

<span data-ttu-id="346e2-277">Wcześniej `Column` atrybut był używany do zmiany mapowania nazw kolumn.</span><span class="sxs-lookup"><span data-stu-id="346e2-277">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="346e2-278">W kodzie `Department` jednostki `Column` atrybut jest używany do zmiany mapowania typów danych SQL.</span><span class="sxs-lookup"><span data-stu-id="346e2-278">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="346e2-279">Kolumna `Budget` jest definiowana przy użyciu typu pieniądza programu SQL Server w bazie danych:</span><span class="sxs-lookup"><span data-stu-id="346e2-279">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="346e2-280">Mapowanie kolumn zazwyczaj nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="346e2-280">Column mapping is generally not required.</span></span> <span data-ttu-id="346e2-281">EF Core wybiera odpowiedni typ danych programu SQL Server na podstawie typu CLR dla właściwości.</span><span class="sxs-lookup"><span data-stu-id="346e2-281">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="346e2-282">Typ CLR `decimal` jest mapowany `decimal` na typ programu SQL Server.</span><span class="sxs-lookup"><span data-stu-id="346e2-282">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="346e2-283">`Budget`jest dla waluty, a typ danych pieniądza jest bardziej odpowiedni dla waluty.</span><span class="sxs-lookup"><span data-stu-id="346e2-283">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="346e2-284">Właściwości klucza obcego i nawigacji</span><span class="sxs-lookup"><span data-stu-id="346e2-284">Foreign key and navigation properties</span></span>

<span data-ttu-id="346e2-285">FK i właściwości nawigacji odzwierciedlają następujące relacje:</span><span class="sxs-lookup"><span data-stu-id="346e2-285">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="346e2-286">Dział może, ale nie musi mieć administratora.</span><span class="sxs-lookup"><span data-stu-id="346e2-286">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="346e2-287">Administrator jest zawsze instruktorem.</span><span class="sxs-lookup"><span data-stu-id="346e2-287">An administrator is always an instructor.</span></span> <span data-ttu-id="346e2-288">W związku `InstructorID` z tym właściwość jest `Instructor` uwzględniona jako FK do jednostki.</span><span class="sxs-lookup"><span data-stu-id="346e2-288">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="346e2-289">Właściwość nawigacji `Administrator` jest nazwany, ale posiada jednostki: `Instructor`</span><span class="sxs-lookup"><span data-stu-id="346e2-289">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="346e2-290">Znak zapytania (?) w poprzednim kodzie określa, że właściwość jest nullable.</span><span class="sxs-lookup"><span data-stu-id="346e2-290">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="346e2-291">Dział może mieć wiele kursów, więc istnieje właściwość nawigacji Kursy:</span><span class="sxs-lookup"><span data-stu-id="346e2-291">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="346e2-292">Zgodnie z konwencją EF Core umożliwia kaskadowe usuwanie dla nienastępalnych do null FK i dla relacji wiele do wielu.</span><span class="sxs-lookup"><span data-stu-id="346e2-292">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="346e2-293">To domyślne zachowanie może spowodować reguły usuwania kaskady kołowych.</span><span class="sxs-lookup"><span data-stu-id="346e2-293">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="346e2-294">Reguły usuwania kaskadowego cyklicznego powodują wyjątek po dodaniu migracji.</span><span class="sxs-lookup"><span data-stu-id="346e2-294">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="346e2-295">Na przykład jeśli `Department.InstructorID` właściwość została zdefiniowana jako nie można nullable, EF Core skonfiguruje regułę usuwania kaskadowego.</span><span class="sxs-lookup"><span data-stu-id="346e2-295">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="346e2-296">W takim przypadku dział zostanie usunięty, gdy instruktor przypisany jako administrator zostanie usunięty.</span><span class="sxs-lookup"><span data-stu-id="346e2-296">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="346e2-297">W tym scenariuszu reguła ograniczenia miałaby większy sens.</span><span class="sxs-lookup"><span data-stu-id="346e2-297">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="346e2-298">Poniższy [płynny interfejs API](#fluent-api-alternative-to-attributes) ustawi regułę ograniczania i wyłączy usuwanie kaskadowe.</span><span class="sxs-lookup"><span data-stu-id="346e2-298">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a><span data-ttu-id="346e2-299">Jednostka Rejestracji</span><span class="sxs-lookup"><span data-stu-id="346e2-299">The Enrollment entity</span></span>

<span data-ttu-id="346e2-300">Rekord rejestracji jest dla jednego kursu podjętego przez jednego ucznia.</span><span class="sxs-lookup"><span data-stu-id="346e2-300">An enrollment record is for one course taken by one student.</span></span>

![Jednostka rejestracji](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="346e2-302">Zaktualizuj *modele/enrollment.cs* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="346e2-302">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="346e2-303">Właściwości klucza obcego i nawigacji</span><span class="sxs-lookup"><span data-stu-id="346e2-303">Foreign key and navigation properties</span></span>

<span data-ttu-id="346e2-304">Właściwości FK i właściwości nawigacji odzwierciedlają następujące relacje:</span><span class="sxs-lookup"><span data-stu-id="346e2-304">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="346e2-305">Rekord rejestracji jest dla jednego kursu, `CourseID` więc istnieje właściwość FK i właściwość `Course` nawigacji:</span><span class="sxs-lookup"><span data-stu-id="346e2-305">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="346e2-306">Rekord rejestracji jest dla jednego ucznia, `StudentID` więc istnieje `Student` właściwość FK i właściwość nawigacji:</span><span class="sxs-lookup"><span data-stu-id="346e2-306">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="346e2-307">Relacje wiele do wielu</span><span class="sxs-lookup"><span data-stu-id="346e2-307">Many-to-Many Relationships</span></span>

<span data-ttu-id="346e2-308">Istnieje wiele do wielu relacji między `Student` i `Course` jednostek.</span><span class="sxs-lookup"><span data-stu-id="346e2-308">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="346e2-309">Jednostka `Enrollment` działa jako tabela sprzężenia wiele do wielu *z ładunkiem* w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-309">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="346e2-310">"Z ładunkiem" oznacza, że `Enrollment` tabela zawiera dodatkowe dane oprócz FK dla tabel `Grade`połączonych (w tym przypadku PK i ).</span><span class="sxs-lookup"><span data-stu-id="346e2-310">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="346e2-311">Na poniższej ilustracji przedstawiono, jak wyglądają te relacje na diagramie jednostek.</span><span class="sxs-lookup"><span data-stu-id="346e2-311">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="346e2-312">(Ten diagram został wygenerowany przy użyciu [ef power tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) dla EF 6.x.</span><span class="sxs-lookup"><span data-stu-id="346e2-312">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="346e2-313">Tworzenie diagramu nie jest częścią samouczka.)</span><span class="sxs-lookup"><span data-stu-id="346e2-313">Creating the diagram isn't part of the tutorial.)</span></span>

![Student-Kurs wielu do wielu relacji](complex-data-model/_static/student-course.png)

<span data-ttu-id="346e2-315">Każda linia relacji ma 1 na jednym końcu i gwiazdkę (\*) na drugim, wskazując relację jeden do wielu.</span><span class="sxs-lookup"><span data-stu-id="346e2-315">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="346e2-316">Jeśli `Enrollment` tabela nie zawiera informacji o gatunku, musiałaby zawierać`CourseID` tylko `StudentID`dwa FK (i ).</span><span class="sxs-lookup"><span data-stu-id="346e2-316">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="346e2-317">Tabela sprzężeń wiele do wielu bez ładunku jest czasami nazywana tabelą czystego sprzężenia (PJT).</span><span class="sxs-lookup"><span data-stu-id="346e2-317">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="346e2-318">I `Instructor` `Course` jednostek mają relację wiele do wielu przy użyciu tabeli sprzężenia czystego.</span><span class="sxs-lookup"><span data-stu-id="346e2-318">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="346e2-319">Uwaga: EF 6.x obsługuje tabele sprzężenia niejawnego dla relacji wiele-do-wielu, ale EF Core nie.</span><span class="sxs-lookup"><span data-stu-id="346e2-319">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="346e2-320">Aby uzyskać więcej informacji, zobacz [Relacje wiele do wielu w EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="346e2-320">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="346e2-321">Jednostka CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="346e2-321">The CourseAssignment entity</span></span>

![Jednostka courseAssignment](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="346e2-323">Utwórz *modele/courseAssignment.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="346e2-323">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

<span data-ttu-id="346e2-324">Relacja Instructor-to-Courses wiele-do-wielu wymaga tabeli sprzężenia, a encja dla tej tabeli sprzężenia jest CourseAssignment.</span><span class="sxs-lookup"><span data-stu-id="346e2-324">The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.</span></span>

![Instruktor-kursy m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="346e2-326">Często nazywa się encję `EntityName1EntityName2`sprzężenia .</span><span class="sxs-lookup"><span data-stu-id="346e2-326">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="346e2-327">Na przykład instructor-to-kursy dołączyć tabeli przy `CourseInstructor`użyciu tego wzorca będzie .</span><span class="sxs-lookup"><span data-stu-id="346e2-327">For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`.</span></span> <span data-ttu-id="346e2-328">Jednak zaleca się użycie nazwy opisującej relację.</span><span class="sxs-lookup"><span data-stu-id="346e2-328">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="346e2-329">Modele danych zaczynają się proste i rosną.</span><span class="sxs-lookup"><span data-stu-id="346e2-329">Data models start out simple and grow.</span></span> <span data-ttu-id="346e2-330">Dołączanie do tabel bez ładunku (PJTs) często ewoluuje w celu uwzględnienia ładunku.</span><span class="sxs-lookup"><span data-stu-id="346e2-330">Join tables without payload (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="346e2-331">Zaczynając od opisowej nazwy encji, nazwa nie musi się zmieniać po zmianie tabeli sprzężenia.</span><span class="sxs-lookup"><span data-stu-id="346e2-331">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="346e2-332">W idealnym przypadku jednostka sprzężenia miałaby własną naturalną (ewentualnie pojedynczą nazwę) w domenie biznesowej.</span><span class="sxs-lookup"><span data-stu-id="346e2-332">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="346e2-333">Na przykład książki i klienci mogą być połączone z jednostką sprzężenia o nazwie Oceny.</span><span class="sxs-lookup"><span data-stu-id="346e2-333">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="346e2-334">Dla instruktora do kursów wiele do wielu `CourseAssignment` relacji, `CourseInstructor`jest preferowany nad .</span><span class="sxs-lookup"><span data-stu-id="346e2-334">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="346e2-335">Klucz złożony</span><span class="sxs-lookup"><span data-stu-id="346e2-335">Composite key</span></span>

<span data-ttu-id="346e2-336">Dwa FKs `CourseAssignment` w`InstructorID` `CourseID`( i ) razem jednoznacznie zidentyfikować każdy wiersz `CourseAssignment` tabeli.</span><span class="sxs-lookup"><span data-stu-id="346e2-336">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="346e2-337">`CourseAssignment`nie wymaga dedykowanego PK.</span><span class="sxs-lookup"><span data-stu-id="346e2-337">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="346e2-338">I `InstructorID` `CourseID` właściwości działają jako złożona PK.</span><span class="sxs-lookup"><span data-stu-id="346e2-338">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="346e2-339">Jedynym sposobem określenia złożonych PKs do EF Core jest *płynny interfejs API*.</span><span class="sxs-lookup"><span data-stu-id="346e2-339">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="346e2-340">W następnej sekcji pokazano, jak skonfigurować złożoną PK.</span><span class="sxs-lookup"><span data-stu-id="346e2-340">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="346e2-341">Klucz złożony zapewnia, że:</span><span class="sxs-lookup"><span data-stu-id="346e2-341">The composite key ensures that:</span></span>

* <span data-ttu-id="346e2-342">Wiele wierszy są dozwolone dla jednego kursu.</span><span class="sxs-lookup"><span data-stu-id="346e2-342">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="346e2-343">Wiele wierszy są dozwolone dla jednego instruktora.</span><span class="sxs-lookup"><span data-stu-id="346e2-343">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="346e2-344">Wiele wierszy nie są dozwolone dla tego samego instruktora i kursu.</span><span class="sxs-lookup"><span data-stu-id="346e2-344">Multiple rows aren't allowed for the same instructor and course.</span></span>

<span data-ttu-id="346e2-345">Jednostka `Enrollment` sprzężenia definiuje własną PK, więc duplikaty tego rodzaju są możliwe.</span><span class="sxs-lookup"><span data-stu-id="346e2-345">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="346e2-346">Aby zapobiec takim duplikatom:</span><span class="sxs-lookup"><span data-stu-id="346e2-346">To prevent such duplicates:</span></span>

* <span data-ttu-id="346e2-347">Dodaj unikatowy indeks w polach FK lub</span><span class="sxs-lookup"><span data-stu-id="346e2-347">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="346e2-348">Skonfiguruj `Enrollment` za pomocą `CourseAssignment`podstawowego klucza kompozytowego podobnego do .</span><span class="sxs-lookup"><span data-stu-id="346e2-348">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="346e2-349">Aby uzyskać więcej informacji, zobacz [Indeksy](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="346e2-349">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="346e2-350">Aktualizowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="346e2-350">Update the database context</span></span>

<span data-ttu-id="346e2-351">Zaktualizuj *plik Data/SchoolContext.cs* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="346e2-351">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

<span data-ttu-id="346e2-352">Powyższy kod dodaje nowe jednostki i `CourseAssignment` konfiguruje złożoną PK jednostki.</span><span class="sxs-lookup"><span data-stu-id="346e2-352">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="346e2-353">Płynna alternatywa dla API dla atrybutów</span><span class="sxs-lookup"><span data-stu-id="346e2-353">Fluent API alternative to attributes</span></span>

<span data-ttu-id="346e2-354">Metoda `OnModelCreating` w poprzednim kodzie używa *płynnego interfejsu API* do konfigurowania zachowania EF Core.</span><span class="sxs-lookup"><span data-stu-id="346e2-354">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="346e2-355">Interfejs API jest nazywany "fluent", ponieważ jest często używany przez ciągnie serię wywołań metody razem w jednej instrukcji.</span><span class="sxs-lookup"><span data-stu-id="346e2-355">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="346e2-356">[Poniższy kod](/ef/core/modeling/#use-fluent-api-to-configure-a-model) jest przykładem płynnego interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="346e2-356">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="346e2-357">W tym samouczku płynny interfejs API jest używany tylko do mapowania bazy danych, którego nie można wykonać za pomocą atrybutów.</span><span class="sxs-lookup"><span data-stu-id="346e2-357">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="346e2-358">Jednak płynny interfejs API można określić większość reguł formatowania, sprawdzania poprawności i mapowania, które można wykonać za pomocą atrybutów.</span><span class="sxs-lookup"><span data-stu-id="346e2-358">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="346e2-359">Niektóre atrybuty, takie jak `MinimumLength` nie można zastosować za pomocą interfejsu API fluent.</span><span class="sxs-lookup"><span data-stu-id="346e2-359">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="346e2-360">`MinimumLength`nie zmienia schematu, stosuje tylko regułę sprawdzania poprawności minimalnej długości.</span><span class="sxs-lookup"><span data-stu-id="346e2-360">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="346e2-361">Niektórzy deweloperzy wolą używać płynnego interfejsu API wyłącznie, aby mogli zachować swoje klasy jednostek "czyste".</span><span class="sxs-lookup"><span data-stu-id="346e2-361">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="346e2-362">Atrybuty i płynny interfejs API można mieszać.</span><span class="sxs-lookup"><span data-stu-id="346e2-362">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="346e2-363">Istnieją pewne konfiguracje, które można wykonać tylko za pomocą płynnego interfejsu API (określając kompozytowe PK).</span><span class="sxs-lookup"><span data-stu-id="346e2-363">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="346e2-364">Istnieją pewne konfiguracje, które można wykonać`MinimumLength`tylko za pomocą atrybutów ( ).</span><span class="sxs-lookup"><span data-stu-id="346e2-364">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="346e2-365">Zalecana praktyka używania płynnego interfejsu API lub atrybutów:</span><span class="sxs-lookup"><span data-stu-id="346e2-365">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="346e2-366">Wybierz jedno z tych dwóch podejść.</span><span class="sxs-lookup"><span data-stu-id="346e2-366">Choose one of these two approaches.</span></span>
* <span data-ttu-id="346e2-367">W miarę możliwości należy konsekwentnie używać wybranego podejścia.</span><span class="sxs-lookup"><span data-stu-id="346e2-367">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="346e2-368">Niektóre atrybuty używane w tym samouczku są używane do:</span><span class="sxs-lookup"><span data-stu-id="346e2-368">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="346e2-369">Tylko sprawdzanie poprawności `MinimumLength`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="346e2-369">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="346e2-370">Tylko konfiguracja EF Core `HasKey`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="346e2-370">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="346e2-371">Sprawdzanie poprawności i konfiguracja `[StringLength(50)]`EF Core (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="346e2-371">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="346e2-372">Aby uzyskać więcej informacji na temat atrybutów a płynnego interfejsu API, zobacz [Metody konfiguracji](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="346e2-372">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram"></a><span data-ttu-id="346e2-373">Diagram encji</span><span class="sxs-lookup"><span data-stu-id="346e2-373">Entity diagram</span></span>

<span data-ttu-id="346e2-374">Na poniższej ilustracji przedstawiono diagram, który ef power tools utworzyć dla ukończonego modelu szkoły.</span><span class="sxs-lookup"><span data-stu-id="346e2-374">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Diagram encji](complex-data-model/_static/diagram.png)

<span data-ttu-id="346e2-376">Na powyższym diagramie przedstawiono:</span><span class="sxs-lookup"><span data-stu-id="346e2-376">The preceding diagram shows:</span></span>

* <span data-ttu-id="346e2-377">Kilka linii relacji jeden do wielu \*(od 1 do ).</span><span class="sxs-lookup"><span data-stu-id="346e2-377">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="346e2-378">Wiersz relacji jeden do zera lub jeden (od 1 do 0..1) między `Instructor` jednostkami a `OfficeAssignment` jednostkami.</span><span class="sxs-lookup"><span data-stu-id="346e2-378">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="346e2-379">Linia relacji zero-lub-jeden-do-wielu (od 0..1 `Instructor` do `Department` \*) między elementami i jednostkami.</span><span class="sxs-lookup"><span data-stu-id="346e2-379">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="346e2-380">Seed bazy danych</span><span class="sxs-lookup"><span data-stu-id="346e2-380">Seed the database</span></span>

<span data-ttu-id="346e2-381">Zaktualizuj kod w *pliku Data/DbInitializer.cs*:</span><span class="sxs-lookup"><span data-stu-id="346e2-381">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

<span data-ttu-id="346e2-382">Powyższy kod zawiera dane źródłowe dla nowych jednostek.</span><span class="sxs-lookup"><span data-stu-id="346e2-382">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="346e2-383">Większość tego kodu tworzy nowe obiekty jednostki i ładuje przykładowe dane.</span><span class="sxs-lookup"><span data-stu-id="346e2-383">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="346e2-384">Przykładowe dane są używane do testowania.</span><span class="sxs-lookup"><span data-stu-id="346e2-384">The sample data is used for testing.</span></span> <span data-ttu-id="346e2-385">Zobacz `Enrollments` `CourseAssignments` i przykłady, ile do wielu tabel sprzężenia mogą być rozstawione.</span><span class="sxs-lookup"><span data-stu-id="346e2-385">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="346e2-386">Dodawanie migracji</span><span class="sxs-lookup"><span data-stu-id="346e2-386">Add a migration</span></span>

<span data-ttu-id="346e2-387">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="346e2-387">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="346e2-388">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="346e2-388">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="346e2-389">W pmc uruchom następujące polecenie.</span><span class="sxs-lookup"><span data-stu-id="346e2-389">In PMC, run the following command.</span></span>

```powershell
Add-Migration ComplexDataModel
```

<span data-ttu-id="346e2-390">Poprzednie polecenie wyświetla ostrzeżenie o możliwej utracie danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-390">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="346e2-391">Jeśli `database update` polecenie jest uruchamiane, jest wywoływany następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="346e2-391">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

<span data-ttu-id="346e2-392">W następnej sekcji zobaczysz, co zrobić z tym błędem.</span><span class="sxs-lookup"><span data-stu-id="346e2-392">In the next section, you see what to do about this error.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="346e2-393">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="346e2-393">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="346e2-394">Jeśli dodasz migrację `database update` i uruchomisz polecenie, zostanie uruchomiony następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="346e2-394">If you add a migration and run the `database update` command, the following error would be produced:</span></span>

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

<span data-ttu-id="346e2-395">W następnej sekcji zobaczysz, jak uniknąć tego błędu.</span><span class="sxs-lookup"><span data-stu-id="346e2-395">In the next section, you see how to avoid this error.</span></span>

---

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="346e2-396">Stosowanie migracji lub upuszczania i ponownego tworzenia</span><span class="sxs-lookup"><span data-stu-id="346e2-396">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="346e2-397">Teraz, gdy masz istniejącą bazę danych, musisz zastanowić się, jak zastosować do niej zmiany.</span><span class="sxs-lookup"><span data-stu-id="346e2-397">Now that you have an existing database, you need to think about how to apply changes to it.</span></span> <span data-ttu-id="346e2-398">W tym samouczku przedstawiono dwie alternatywy:</span><span class="sxs-lookup"><span data-stu-id="346e2-398">This tutorial shows two alternatives:</span></span>

* <span data-ttu-id="346e2-399">[Upuść i ponownie utwórz bazę danych](#drop).</span><span class="sxs-lookup"><span data-stu-id="346e2-399">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="346e2-400">Wybierz tę sekcję, jeśli używasz SQLite.</span><span class="sxs-lookup"><span data-stu-id="346e2-400">Choose this section if you're using SQLite.</span></span>
* <span data-ttu-id="346e2-401">[Zastosuj migrację do istniejącej bazy danych](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="346e2-401">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="346e2-402">Instrukcje w tej sekcji działają tylko dla programu SQL Server, **a nie dla SQLite**.</span><span class="sxs-lookup"><span data-stu-id="346e2-402">The instructions in this section work for SQL Server only, **not for SQLite**.</span></span> 

<span data-ttu-id="346e2-403">Każdy wybór działa dla programu SQL Server.</span><span class="sxs-lookup"><span data-stu-id="346e2-403">Either choice works for SQL Server.</span></span> <span data-ttu-id="346e2-404">Chociaż metoda stosowania i migracji jest bardziej złożona i czasochłonna, jest to preferowane podejście do rzeczywistych środowisk produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="346e2-404">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="346e2-405">Upuść i ponownie utwórz bazę danych</span><span class="sxs-lookup"><span data-stu-id="346e2-405">Drop and re-create the database</span></span>

<span data-ttu-id="346e2-406">[Pomiń tę sekcję,](#apply-the-migration) jeśli używasz programu SQL Server i chcesz wykonać podejście zastosuj-migracja w poniższej sekcji.</span><span class="sxs-lookup"><span data-stu-id="346e2-406">[Skip this section](#apply-the-migration) if you're using SQL Server and want to do the apply-migration approach in the following section.</span></span>

<span data-ttu-id="346e2-407">Aby wymusić ef core do utworzenia nowej bazy danych, upuść i zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="346e2-407">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="346e2-408">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="346e2-408">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="346e2-409">W **konsoli Menedżera pakietów** (PMC) uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="346e2-409">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Drop-Database
  ```

* <span data-ttu-id="346e2-410">Usuń folder *Migracje,* a następnie uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="346e2-410">Delete the *Migrations* folder, then run the following command:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="346e2-411">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="346e2-411">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="346e2-412">Otwórz okno polecenia i przejdź do folderu projektu.</span><span class="sxs-lookup"><span data-stu-id="346e2-412">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="346e2-413">Folder projektu zawiera plik *ContosoUniversity.csproj.*</span><span class="sxs-lookup"><span data-stu-id="346e2-413">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="346e2-414">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="346e2-414">Run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

* <span data-ttu-id="346e2-415">Usuń folder *Migracje,* a następnie uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="346e2-415">Delete the *Migrations* folder, then run the following command:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="346e2-416">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="346e2-416">Run the app.</span></span> <span data-ttu-id="346e2-417">Uruchomienie aplikacji uruchamia `DbInitializer.Initialize` metodę.</span><span class="sxs-lookup"><span data-stu-id="346e2-417">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="346e2-418">Wypełnia `DbInitializer.Initialize` nową bazę danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-418">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="346e2-419">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="346e2-419">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="346e2-420">Otwórz bazę danych w SSOX:</span><span class="sxs-lookup"><span data-stu-id="346e2-420">Open the database in SSOX:</span></span>

* <span data-ttu-id="346e2-421">Jeśli SSOX został otwarty wcześniej, kliknij przycisk **Odśwież.**</span><span class="sxs-lookup"><span data-stu-id="346e2-421">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="346e2-422">Rozwiń węzeł **Tabele.**</span><span class="sxs-lookup"><span data-stu-id="346e2-422">Expand the **Tables** node.</span></span> <span data-ttu-id="346e2-423">Zostaną wyświetlone utworzone tabele.</span><span class="sxs-lookup"><span data-stu-id="346e2-423">The created tables are displayed.</span></span>

  ![Tabele w SSOX](complex-data-model/_static/ssox-tables.png)

* <span data-ttu-id="346e2-425">Sprawdź tabelę **CourseAssignment:**</span><span class="sxs-lookup"><span data-stu-id="346e2-425">Examine the **CourseAssignment** table:</span></span>

  * <span data-ttu-id="346e2-426">Kliknij prawym przyciskiem myszy **tabelę CourseAssignment** i wybierz polecenie **Wyświetl dane**.</span><span class="sxs-lookup"><span data-stu-id="346e2-426">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
  * <span data-ttu-id="346e2-427">Sprawdź **CourseAssignment tabela** zawiera dane.</span><span class="sxs-lookup"><span data-stu-id="346e2-427">Verify the **CourseAssignment** table contains data.</span></span>

  ![Dane dotyczące kursusysignment w SSOX](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="346e2-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="346e2-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="346e2-430">Użyj narzędzia SQLite, aby sprawdzić bazę danych:</span><span class="sxs-lookup"><span data-stu-id="346e2-430">Use your SQLite tool to examine the database:</span></span>

* <span data-ttu-id="346e2-431">Nowe tabele i kolumny.</span><span class="sxs-lookup"><span data-stu-id="346e2-431">New tables and columns.</span></span>
* <span data-ttu-id="346e2-432">Rozstawione dane w tabelach, na przykład **CourseAssignment** tabeli.</span><span class="sxs-lookup"><span data-stu-id="346e2-432">Seeded data in tables, for example the **CourseAssignment** table.</span></span>

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a><span data-ttu-id="346e2-433">Stosowanie migracji</span><span class="sxs-lookup"><span data-stu-id="346e2-433">Apply the migration</span></span>

<span data-ttu-id="346e2-434">Ta sekcja jest opcjonalna.</span><span class="sxs-lookup"><span data-stu-id="346e2-434">This section is optional.</span></span> <span data-ttu-id="346e2-435">Te kroki działają tylko dla sql server localdb i tylko wtedy, gdy pominięto poprzedni [drop i ponownie utworzyć sekcję bazy danych.](#drop)</span><span class="sxs-lookup"><span data-stu-id="346e2-435">These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="346e2-436">Gdy migracje są uruchamiane z istniejącymi danymi, mogą istnieć ograniczenia FK, które nie są spełnione z istniejącymi danymi.</span><span class="sxs-lookup"><span data-stu-id="346e2-436">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="346e2-437">W przypadku danych produkcyjnych należy podjąć kroki migracji istniejących danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-437">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="346e2-438">W tej sekcji przedstawiono przykład ustalania naruszeń ograniczeń FK.</span><span class="sxs-lookup"><span data-stu-id="346e2-438">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="346e2-439">Nie należy wprowadzać tych zmian kodu bez kopii zapasowej.</span><span class="sxs-lookup"><span data-stu-id="346e2-439">Don't make these code changes without a backup.</span></span> <span data-ttu-id="346e2-440">Nie należy wprowadzać tych zmian kodu, jeśli zakończono poprzedni [drop i ponownie utworzyć sekcję bazy danych.](#drop)</span><span class="sxs-lookup"><span data-stu-id="346e2-440">Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="346e2-441">Plik *{timestamp}_ComplexDataModel.cs* zawiera następujący kod:</span><span class="sxs-lookup"><span data-stu-id="346e2-441">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="346e2-442">Poprzedni kod dodaje niedopuszczalny `DepartmentID` do null `Course` FK do tabeli.</span><span class="sxs-lookup"><span data-stu-id="346e2-442">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="346e2-443">Baza danych z poprzedniego samouczka `Course`zawiera wiersze w , dzięki czemu tabela nie może być aktualizowana przez migracje.</span><span class="sxs-lookup"><span data-stu-id="346e2-443">The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="346e2-444">Aby migracja działała `ComplexDataModel` z istniejącymi danymi:</span><span class="sxs-lookup"><span data-stu-id="346e2-444">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="346e2-445">Zmień kod, aby nadać`DepartmentID`nowej kolumnie ( ) wartość domyślną.</span><span class="sxs-lookup"><span data-stu-id="346e2-445">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="346e2-446">Utwórz fałszywy dział o nazwie "Temp", aby działać jako domyślny dział.</span><span class="sxs-lookup"><span data-stu-id="346e2-446">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="346e2-447">Naprawianie ograniczeń klucza obcego</span><span class="sxs-lookup"><span data-stu-id="346e2-447">Fix the foreign key constraints</span></span>

<span data-ttu-id="346e2-448">W `ComplexDataModel` klasie migracji zaktualizuj `Up` metodę:</span><span class="sxs-lookup"><span data-stu-id="346e2-448">In the `ComplexDataModel` migration class, update the `Up` method:</span></span>

* <span data-ttu-id="346e2-449">Otwórz plik *{timestamp}_ComplexDataModel.cs.*</span><span class="sxs-lookup"><span data-stu-id="346e2-449">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="346e2-450">Skomentuj wiersz kodu, który `Course` dodaje kolumnę `DepartmentID` do tabeli.</span><span class="sxs-lookup"><span data-stu-id="346e2-450">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="346e2-451">Dodaj poniższy wyróżniony kod.</span><span class="sxs-lookup"><span data-stu-id="346e2-451">Add the following highlighted code.</span></span> <span data-ttu-id="346e2-452">Nowy kod idzie `.CreateTable( name: "Department"` po bloku:</span><span class="sxs-lookup"><span data-stu-id="346e2-452">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

<span data-ttu-id="346e2-453">Z poprzednimi zmianami `Course` istniejące wiersze będą powiązane z działem "Temp" po uruchomieniu `ComplexDataModel.Up` metody.</span><span class="sxs-lookup"><span data-stu-id="346e2-453">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.</span></span>

<span data-ttu-id="346e2-454">Sposób obsługi sytuacji pokazano tutaj jest uproszczony dla tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="346e2-454">The way of handling the situation shown here is simplified for this tutorial.</span></span> <span data-ttu-id="346e2-455">Aplikacja produkcyjna:</span><span class="sxs-lookup"><span data-stu-id="346e2-455">A production app would:</span></span>

* <span data-ttu-id="346e2-456">Dołącz kod lub skrypty, aby dodać `Department` wiersze i powiązane `Course` wiersze do nowych `Department` wierszy.</span><span class="sxs-lookup"><span data-stu-id="346e2-456">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="346e2-457">Nie używaj działu "Temp" ani `Course.DepartmentID`wartości domyślnej dla programu .</span><span class="sxs-lookup"><span data-stu-id="346e2-457">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="346e2-458">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="346e2-458">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="346e2-459">W **konsoli Menedżera pakietów** (PMC) uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="346e2-459">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Update-Database
  ```

<span data-ttu-id="346e2-460">Ponieważ `DbInitializer.Initialize` metoda jest przeznaczona do pracy tylko z pustą bazą danych, użyj SSOX, aby usunąć wszystkie wiersze w tabelach Uczeń i kurs.</span><span class="sxs-lookup"><span data-stu-id="346e2-460">Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables.</span></span> <span data-ttu-id="346e2-461">(Kaskadowe usuwanie zajmie się tabelą Rejestracja).</span><span class="sxs-lookup"><span data-stu-id="346e2-461">(Cascade delete will take care of the Enrollment table.)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="346e2-462">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="346e2-462">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="346e2-463">Jeśli używasz usługi SQL Server LocalDB z programem Visual Studio Code, uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="346e2-463">If you're using SQL Server LocalDB with Visual Studio Code, run the following command:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<span data-ttu-id="346e2-464">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="346e2-464">Run the app.</span></span> <span data-ttu-id="346e2-465">Uruchomienie aplikacji uruchamia `DbInitializer.Initialize` metodę.</span><span class="sxs-lookup"><span data-stu-id="346e2-465">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="346e2-466">Wypełnia `DbInitializer.Initialize` nową bazę danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-466">The `DbInitializer.Initialize` populates the new database.</span></span>

## <a name="next-steps"></a><span data-ttu-id="346e2-467">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="346e2-467">Next steps</span></span>

<span data-ttu-id="346e2-468">Następne dwa samouczki pokazują, jak czytać i aktualizować powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="346e2-468">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="346e2-469">[Poprzedni samouczek](xref:data/ef-rp/migrations)
> [Następny samouczek](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="346e2-469">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="346e2-470">Poprzednie samouczki pracował z podstawowym modelem danych, który składał się z trzech jednostek.</span><span class="sxs-lookup"><span data-stu-id="346e2-470">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="346e2-471">W tym samouczku:</span><span class="sxs-lookup"><span data-stu-id="346e2-471">In this tutorial:</span></span>

* <span data-ttu-id="346e2-472">Dodano więcej jednostek i relacji.</span><span class="sxs-lookup"><span data-stu-id="346e2-472">More entities and relationships are added.</span></span>
* <span data-ttu-id="346e2-473">Model danych jest dostosowywany przez określenie reguł formatowania, sprawdzania poprawności i mapowania bazy danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-473">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="346e2-474">Klasy jednostek dla ukończonego modelu danych są wyświetlane na poniższej ilustracji:</span><span class="sxs-lookup"><span data-stu-id="346e2-474">The entity classes for the completed data model are shown in the following illustration:</span></span>

![Diagram encji](complex-data-model/_static/diagram.png)

<span data-ttu-id="346e2-476">Jeśli napotkasz problemy, których nie możesz rozwiązać, pobierz [ukończoną aplikację](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="346e2-476">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="346e2-477">Dostosowywanie modelu danych za pomocą atrybutów</span><span class="sxs-lookup"><span data-stu-id="346e2-477">Customize the data model with attributes</span></span>

<span data-ttu-id="346e2-478">W tej sekcji model danych jest dostosowywany przy użyciu atrybutów.</span><span class="sxs-lookup"><span data-stu-id="346e2-478">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="346e2-479">Atrybut DataType</span><span class="sxs-lookup"><span data-stu-id="346e2-479">The DataType attribute</span></span>

<span data-ttu-id="346e2-480">Na stronach uczniów jest aktualnie wyświetlana godzina rejestracji.</span><span class="sxs-lookup"><span data-stu-id="346e2-480">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="346e2-481">Zazwyczaj pola daty pokazują tylko datę, a nie godzinę.</span><span class="sxs-lookup"><span data-stu-id="346e2-481">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="346e2-482">Aktualizuj *modele/student.cs* za pomocą następującego podświetlanym kodem:</span><span class="sxs-lookup"><span data-stu-id="346e2-482">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="346e2-483">[Atrybut DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) określa typ danych, który jest bardziej szczegółowy niż typ wewnętrznego bazy danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-483">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="346e2-484">W takim przypadku powinna być wyświetlana tylko data, a nie data i godzina.</span><span class="sxs-lookup"><span data-stu-id="346e2-484">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="346e2-485">[Wyliczenie datatype](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) zawiera wiele typów danych, takich jak Data, Godzina, Numer telefonu, Waluta, EmailAddress, itp. Atrybut `DataType` może również włączyć aplikację, aby automatycznie udostępniać funkcje specyficzne dla typu.</span><span class="sxs-lookup"><span data-stu-id="346e2-485">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="346e2-486">Przykład:</span><span class="sxs-lookup"><span data-stu-id="346e2-486">For example:</span></span>

* <span data-ttu-id="346e2-487">Łącze `mailto:` jest tworzone `DataType.EmailAddress`automatycznie dla pliku .</span><span class="sxs-lookup"><span data-stu-id="346e2-487">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="346e2-488">Selektor daty jest `DataType.Date` dostępny w większości przeglądarek.</span><span class="sxs-lookup"><span data-stu-id="346e2-488">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="346e2-489">Atrybut `DataType` emituje atrybuty `data-` HTML 5 (wymawiane rozdzielenie danych), które zużywają przeglądarki HTML 5.</span><span class="sxs-lookup"><span data-stu-id="346e2-489">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="346e2-490">Atrybuty `DataType` nie zapewniają sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="346e2-490">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="346e2-491">`DataType.Date`nie określa formatu daty, która jest wyświetlana.</span><span class="sxs-lookup"><span data-stu-id="346e2-491">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="346e2-492">Domyślnie pole daty jest wyświetlane zgodnie z domyślnymi formatami opartymi na [programie CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)serwera .</span><span class="sxs-lookup"><span data-stu-id="346e2-492">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="346e2-493">Atrybut `DisplayFormat` jest używany do jawnego określenia formatu daty:</span><span class="sxs-lookup"><span data-stu-id="346e2-493">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="346e2-494">Ustawienie `ApplyFormatInEditMode` określa, że formatowanie powinno być również stosowane do interfejsu użytkownika edycji.</span><span class="sxs-lookup"><span data-stu-id="346e2-494">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="346e2-495">Niektóre pola nie `ApplyFormatInEditMode`powinny używać .</span><span class="sxs-lookup"><span data-stu-id="346e2-495">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="346e2-496">Na przykład symbol waluty zazwyczaj nie powinien być wyświetlany w polu tekstowym edycji.</span><span class="sxs-lookup"><span data-stu-id="346e2-496">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="346e2-497">Atrybut `DisplayFormat` może być używany przez siebie.</span><span class="sxs-lookup"><span data-stu-id="346e2-497">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="346e2-498">Ogólnie warto użyć atrybutu `DataType` z atrybutem. `DisplayFormat`</span><span class="sxs-lookup"><span data-stu-id="346e2-498">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="346e2-499">Atrybut `DataType` przekazuje semantyki danych, w przeciwieństwie do sposobu renderowania go na ekranie.</span><span class="sxs-lookup"><span data-stu-id="346e2-499">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="346e2-500">Atrybut `DataType` zapewnia następujące korzyści, które nie `DisplayFormat`są dostępne w:</span><span class="sxs-lookup"><span data-stu-id="346e2-500">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="346e2-501">Przeglądarka może włączyć funkcje HTML5.</span><span class="sxs-lookup"><span data-stu-id="346e2-501">The browser can enable HTML5 features.</span></span> <span data-ttu-id="346e2-502">Na przykład pokaż formant kalendarza, odpowiedni dla ustawień regionalnych symbol waluty, łącza e-mail, sprawdzanie poprawności danych wejściowych po stronie klienta itp.</span><span class="sxs-lookup"><span data-stu-id="346e2-502">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="346e2-503">Domyślnie przeglądarka renderuje dane przy użyciu poprawnego formatu opartego na ustawieniach regionalnych.</span><span class="sxs-lookup"><span data-stu-id="346e2-503">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="346e2-504">Aby uzyskać więcej informacji, zobacz [ \<dokumentację> Pomocnika tagów](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="346e2-504">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="346e2-505">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="346e2-505">Run the app.</span></span> <span data-ttu-id="346e2-506">Przejdź do strony Indeks uczniów.</span><span class="sxs-lookup"><span data-stu-id="346e2-506">Navigate to the Students Index page.</span></span> <span data-ttu-id="346e2-507">Czasy nie są już wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="346e2-507">Times are no longer displayed.</span></span> <span data-ttu-id="346e2-508">Każdy widok, który `Student` używa modelu wyświetla datę bez godziny.</span><span class="sxs-lookup"><span data-stu-id="346e2-508">Every view that uses the `Student` model displays the date without time.</span></span>

![Indeks uczniów przedstawiający daty bez godzin](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="346e2-510">Atrybut StringLength</span><span class="sxs-lookup"><span data-stu-id="346e2-510">The StringLength attribute</span></span>

<span data-ttu-id="346e2-511">Reguły sprawdzania poprawności danych i komunikaty o błędach sprawdzania poprawności można określić za pomocą atrybutów.</span><span class="sxs-lookup"><span data-stu-id="346e2-511">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="346e2-512">[Atrybut StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) określa minimalną i maksymalną długość znaków dozwolonych w polu danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-512">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="346e2-513">Atrybut `StringLength` zapewnia również weryfikacji po stronie klienta i po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="346e2-513">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="346e2-514">Minimalna wartość nie ma wpływu na schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-514">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="346e2-515">Zaktualizuj `Student` model za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="346e2-515">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="346e2-516">Poprzedni kod ogranicza nazwy do nie więcej niż 50 znaków.</span><span class="sxs-lookup"><span data-stu-id="346e2-516">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="346e2-517">Atrybut `StringLength` nie uniemożliwia użytkownikowi wprowadzania odstępu dla nazwy.</span><span class="sxs-lookup"><span data-stu-id="346e2-517">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="346e2-518">[Atrybut RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) służy do stosowania ograniczeń danych wejściowych.</span><span class="sxs-lookup"><span data-stu-id="346e2-518">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="346e2-519">Na przykład poniższy kod wymaga, aby pierwszy znak był wielką literą, a pozostałe znaki alfabetycznie:</span><span class="sxs-lookup"><span data-stu-id="346e2-519">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="346e2-520">Uruchom aplikację:</span><span class="sxs-lookup"><span data-stu-id="346e2-520">Run the app:</span></span>

* <span data-ttu-id="346e2-521">Przejdź do strony Uczniowie.</span><span class="sxs-lookup"><span data-stu-id="346e2-521">Navigate to the Students page.</span></span>
* <span data-ttu-id="346e2-522">Wybierz **pozycję Utwórz nowy**i wprowadź nazwę dłuższą niż 50 znaków.</span><span class="sxs-lookup"><span data-stu-id="346e2-522">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="346e2-523">Wybierz **pozycję Utwórz**, sprawdzanie poprawności po stronie klienta wyświetla komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="346e2-523">Select **Create**, client-side validation shows an error message.</span></span>

![Strona indeksu uczniów z błędami długości ciągu](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="346e2-525">W **programie SQL Server Object Explorer** (SSOX) otwórz projektanta tabel uczniów, klikając dwukrotnie tabelę **Student.**</span><span class="sxs-lookup"><span data-stu-id="346e2-525">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabela studentów w SSOX przed migracją](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="346e2-527">Poprzedni obraz przedstawia schemat tabeli. `Student`</span><span class="sxs-lookup"><span data-stu-id="346e2-527">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="346e2-528">Pola nazw mają `nvarchar(MAX)` typ, ponieważ migracje nie zostały uruchomione w bazy danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-528">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="346e2-529">Gdy migracje są uruchamiane w dalszej `nvarchar(50)`części tego samouczka, pola nazw stają się .</span><span class="sxs-lookup"><span data-stu-id="346e2-529">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="346e2-530">Atrybut Kolumna</span><span class="sxs-lookup"><span data-stu-id="346e2-530">The Column attribute</span></span>

<span data-ttu-id="346e2-531">Atrybuty mogą kontrolować sposób mapowania klas i właściwości do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-531">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="346e2-532">W tej sekcji `Column` atrybut jest używany do mapowania nazwy `FirstMidName` właściwości do "FirstName" w db.</span><span class="sxs-lookup"><span data-stu-id="346e2-532">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="346e2-533">Podczas tworzenia bazy danych nazwy właściwości w modelu są używane dla `Column` nazw kolumn (z wyjątkiem sytuacji, gdy używany jest atrybut).</span><span class="sxs-lookup"><span data-stu-id="346e2-533">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="346e2-534">Model `Student` używa `FirstMidName` dla pola imienia, ponieważ pole może również zawierać drugie imię.</span><span class="sxs-lookup"><span data-stu-id="346e2-534">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="346e2-535">Zaktualizuj plik *Student.cs* za pomocą następującego podświetlanym kodem:</span><span class="sxs-lookup"><span data-stu-id="346e2-535">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="346e2-536">Wraz z poprzednią `Student.FirstMidName` zmianą w `FirstName` aplikacji jest `Student` mapowana do kolumny tabeli.</span><span class="sxs-lookup"><span data-stu-id="346e2-536">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="346e2-537">Dodanie atrybutu `Column` zmienia model, który `SchoolContext`wspiera program .</span><span class="sxs-lookup"><span data-stu-id="346e2-537">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="346e2-538">Model kopii `SchoolContext` zapasowej nie jest już zgodny z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-538">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="346e2-539">Jeśli aplikacja jest uruchamiana przed zastosowaniem migracji, generowany jest następujący wyjątek:</span><span class="sxs-lookup"><span data-stu-id="346e2-539">If the app is run before applying migrations, the following exception is generated:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="346e2-540">Aby zaktualizować bazę danych:</span><span class="sxs-lookup"><span data-stu-id="346e2-540">To update the DB:</span></span>

* <span data-ttu-id="346e2-541">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="346e2-541">Build the project.</span></span>
* <span data-ttu-id="346e2-542">Otwórz okno polecenia w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="346e2-542">Open a command window in the project folder.</span></span> <span data-ttu-id="346e2-543">Wprowadź następujące polecenia, aby utworzyć nową migrację i zaktualizować bazę danych:</span><span class="sxs-lookup"><span data-stu-id="346e2-543">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="346e2-544">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="346e2-544">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="346e2-545">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="346e2-545">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="346e2-546">Polecenie `migrations add ColumnFirstName` generuje następujący komunikat ostrzegawczy:</span><span class="sxs-lookup"><span data-stu-id="346e2-546">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="346e2-547">Ostrzeżenie jest generowane, ponieważ pola nazw są teraz ograniczone do 50 znaków.</span><span class="sxs-lookup"><span data-stu-id="346e2-547">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="346e2-548">Jeśli nazwa wdb miał więcej niż 50 znaków, 51 do ostatniego znaku zostaną utracone.</span><span class="sxs-lookup"><span data-stu-id="346e2-548">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="346e2-549">Testowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="346e2-549">Test the app.</span></span>

<span data-ttu-id="346e2-550">Otwórz tabelę uczniów w SSOX:</span><span class="sxs-lookup"><span data-stu-id="346e2-550">Open the Student table in SSOX:</span></span>

![Tabela studentów w SSOX po migracji](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="346e2-552">Przed zastosowaniem migracji kolumny nazw były typu [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="346e2-552">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="346e2-553">Kolumny nazw są `nvarchar(50)`teraz .</span><span class="sxs-lookup"><span data-stu-id="346e2-553">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="346e2-554">Nazwa kolumny została `FirstMidName` zmieniona z na `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="346e2-554">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="346e2-555">W poniższej sekcji tworzenie aplikacji na niektórych etapach generuje błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="346e2-555">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="346e2-556">Instrukcje określają, kiedy utworzyć aplikację.</span><span class="sxs-lookup"><span data-stu-id="346e2-556">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="346e2-557">Aktualizacja encji ucznia</span><span class="sxs-lookup"><span data-stu-id="346e2-557">Student entity update</span></span>

![Jednostka studencka](complex-data-model/_static/student-entity.png)

<span data-ttu-id="346e2-559">Zaktualizuj *modele/Student.cs* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="346e2-559">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="346e2-560">Wymagany atrybut</span><span class="sxs-lookup"><span data-stu-id="346e2-560">The Required attribute</span></span>

<span data-ttu-id="346e2-561">Atrybut `Required` sprawia, że właściwości nazwy wymagane pola.</span><span class="sxs-lookup"><span data-stu-id="346e2-561">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="346e2-562">Atrybut `Required` nie jest potrzebny dla typów nienastępalnych do`DateTime` `int`wartości, takich jak typy wartości ( , , `double`itp.).</span><span class="sxs-lookup"><span data-stu-id="346e2-562">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="346e2-563">Typy, których nie można mieć wartości null, są automatycznie traktowane jako pola wymagane.</span><span class="sxs-lookup"><span data-stu-id="346e2-563">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="346e2-564">Atrybut `Required` można zastąpić parametrem minimalnej długości `StringLength` w atrybucie:</span><span class="sxs-lookup"><span data-stu-id="346e2-564">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="346e2-565">Atrybut Display</span><span class="sxs-lookup"><span data-stu-id="346e2-565">The Display attribute</span></span>

<span data-ttu-id="346e2-566">Atrybut `Display` określa, że podpis dla pól tekstowych powinien być "Imię", "Nazwisko", "Imię" i "Data rejestracji".</span><span class="sxs-lookup"><span data-stu-id="346e2-566">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="346e2-567">Domyślne podpisy nie miały miejsca dzielącego wyrazy, na przykład "Nazwisko".</span><span class="sxs-lookup"><span data-stu-id="346e2-567">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="346e2-568">Właściwość obliczana fullname</span><span class="sxs-lookup"><span data-stu-id="346e2-568">The FullName calculated property</span></span>

<span data-ttu-id="346e2-569">`FullName`jest obliczoną właściwością, która zwraca wartość utworzoną przez łączenie dwóch innych właściwości.</span><span class="sxs-lookup"><span data-stu-id="346e2-569">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="346e2-570">`FullName`nie można ustawić, ma tylko akcesor get.</span><span class="sxs-lookup"><span data-stu-id="346e2-570">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="346e2-571">Żadna kolumna nie `FullName` jest tworzona w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-571">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="346e2-572">Tworzenie jednostki instruktora</span><span class="sxs-lookup"><span data-stu-id="346e2-572">Create the Instructor Entity</span></span>

![Jednostka instruktora](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="346e2-574">Utwórz *modele/instructor.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="346e2-574">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="346e2-575">Wiele atrybutów może znajdować się w jednym wierszu.</span><span class="sxs-lookup"><span data-stu-id="346e2-575">Multiple attributes can be on one line.</span></span> <span data-ttu-id="346e2-576">Atrybuty `HireDate` mogą być zapisywane w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="346e2-576">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="346e2-577">Właściwości nawigacji w ramach kursuAssignments i OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="346e2-577">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="346e2-578">Właściwości `CourseAssignments` `OfficeAssignment` i są właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="346e2-578">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="346e2-579">Instruktor może uczyć dowolną liczbę kursów, więc `CourseAssignments` jest zdefiniowany jako kolekcja.</span><span class="sxs-lookup"><span data-stu-id="346e2-579">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="346e2-580">Jeśli właściwość nawigacji zawiera wiele jednostek:</span><span class="sxs-lookup"><span data-stu-id="346e2-580">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="346e2-581">Musi to być typ listy, na którym można dodawać, usuwać i aktualizować wpisy.</span><span class="sxs-lookup"><span data-stu-id="346e2-581">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="346e2-582">Typy właściwości nawigacji obejmują:</span><span class="sxs-lookup"><span data-stu-id="346e2-582">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="346e2-583">Jeśli `ICollection<T>` jest określony, EF `HashSet<T>` Core tworzy kolekcję domyślnie.</span><span class="sxs-lookup"><span data-stu-id="346e2-583">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="346e2-584">Jednostka `CourseAssignment` jest wyjaśniona w sekcji dotyczącej relacji wiele do wielu.</span><span class="sxs-lookup"><span data-stu-id="346e2-584">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="346e2-585">Reguły biznesowe Contoso University stwierdzają, że instruktor może mieć co najwyżej jedno biuro.</span><span class="sxs-lookup"><span data-stu-id="346e2-585">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="346e2-586">Właściwość `OfficeAssignment` posiada `OfficeAssignment` jedną jednostkę.</span><span class="sxs-lookup"><span data-stu-id="346e2-586">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="346e2-587">`OfficeAssignment`jest null, jeśli nie jest przypisany żaden urząd.</span><span class="sxs-lookup"><span data-stu-id="346e2-587">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="346e2-588">Tworzenie encji OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="346e2-588">Create the OfficeAssignment entity</span></span>

![Encja officeassignment](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="346e2-590">Utwórz *modele/officeassignment.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="346e2-590">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="346e2-591">Atrybut Klucz</span><span class="sxs-lookup"><span data-stu-id="346e2-591">The Key attribute</span></span>

<span data-ttu-id="346e2-592">Atrybut `[Key]` jest używany do identyfikowania właściwości jako klucz podstawowy (PK), gdy nazwa właściwości jest czymś innym niż classnameID lub ID.</span><span class="sxs-lookup"><span data-stu-id="346e2-592">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="346e2-593">Istnieje relacja jeden do zera lub jeden między `Instructor` jednostkami. `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="346e2-593">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="346e2-594">Przypisanie biura istnieje tylko w odniesieniu do instruktora, do który jest przypisany.</span><span class="sxs-lookup"><span data-stu-id="346e2-594">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="346e2-595">PK `OfficeAssignment` jest również jego klucz obcy (FK) do jednostki. `Instructor`</span><span class="sxs-lookup"><span data-stu-id="346e2-595">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="346e2-596">EF Core nie może `InstructorID` automatycznie rozpoznać `OfficeAssignment` jako PK, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="346e2-596">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="346e2-597">`InstructorID`nie jest zgodna z konwencją nazewnictwa identyfikatorów ani classnameID.</span><span class="sxs-lookup"><span data-stu-id="346e2-597">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="346e2-598">W związku `Key` z tym atrybut `InstructorID` jest używany do identyfikowania jako PK:</span><span class="sxs-lookup"><span data-stu-id="346e2-598">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="346e2-599">Domyślnie EF Core traktuje klucz jako nieogenerowana przez bazę danych, ponieważ kolumna jest dla identyfikującej relacji.</span><span class="sxs-lookup"><span data-stu-id="346e2-599">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="346e2-600">Właściwość nawigacji instruktora</span><span class="sxs-lookup"><span data-stu-id="346e2-600">The Instructor navigation property</span></span>

<span data-ttu-id="346e2-601">Właściwość `OfficeAssignment` nawigacji `Instructor` dla jednostki jest nullable, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="346e2-601">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="346e2-602">Typy odwołań (takie jak klasy są nullable).</span><span class="sxs-lookup"><span data-stu-id="346e2-602">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="346e2-603">Instruktor może nie mieć przypisania biura.</span><span class="sxs-lookup"><span data-stu-id="346e2-603">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="346e2-604">Jednostka `OfficeAssignment` ma właściwość nawigacji `Instructor` nienastępnącą do wartości null, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="346e2-604">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="346e2-605">`InstructorID`nie można anulować.</span><span class="sxs-lookup"><span data-stu-id="346e2-605">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="346e2-606">Przypisanie biura nie może istnieć bez instruktora.</span><span class="sxs-lookup"><span data-stu-id="346e2-606">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="346e2-607">Gdy `Instructor` jednostka ma `OfficeAssignment` jednostkę pokrewną, każda jednostka ma odwołanie do drugiej w swojej właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="346e2-607">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="346e2-608">Atrybut `[Required]` może być stosowany do `Instructor` właściwości nawigacji:</span><span class="sxs-lookup"><span data-stu-id="346e2-608">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="346e2-609">Poprzedni kod określa, że musi istnieć powiązany instruktor.</span><span class="sxs-lookup"><span data-stu-id="346e2-609">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="346e2-610">Poprzedni kod jest zbędne, `InstructorID` ponieważ klucz obcy (który jest również PK) jest nie można nullable.</span><span class="sxs-lookup"><span data-stu-id="346e2-610">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="346e2-611">Modyfikowanie jednostki kursu</span><span class="sxs-lookup"><span data-stu-id="346e2-611">Modify the Course Entity</span></span>

![Jednostka kursu](complex-data-model/_static/course-entity.png)

<span data-ttu-id="346e2-613">Zaktualizuj *modele/Course.cs* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="346e2-613">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="346e2-614">Jednostka `Course` ma właściwość `DepartmentID`klucza obcego (FK).</span><span class="sxs-lookup"><span data-stu-id="346e2-614">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="346e2-615">`DepartmentID`wskazuje na `Department` podmiot powiązany.</span><span class="sxs-lookup"><span data-stu-id="346e2-615">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="346e2-616">Jednostka `Course` ma `Department` właściwość nawigacji.</span><span class="sxs-lookup"><span data-stu-id="346e2-616">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="346e2-617">EF Core nie wymaga właściwości FK dla modelu danych, gdy model ma właściwość nawigacji dla jednostki pokrewne.</span><span class="sxs-lookup"><span data-stu-id="346e2-617">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="346e2-618">EF Core automatycznie tworzy pliki FK w bazie danych, gdziekolwiek są potrzebne.</span><span class="sxs-lookup"><span data-stu-id="346e2-618">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="346e2-619">EF Core tworzy [właściwości cienia](/ef/core/modeling/shadow-properties) dla automatycznie tworzonych FKs.</span><span class="sxs-lookup"><span data-stu-id="346e2-619">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="346e2-620">Posiadanie FK w modelu danych może sprawić, że aktualizacje będą prostsze i bardziej wydajne.</span><span class="sxs-lookup"><span data-stu-id="346e2-620">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="346e2-621">Rozważmy na przykład model, w `DepartmentID` którym właściwość FK *nie* jest uwzględniona.</span><span class="sxs-lookup"><span data-stu-id="346e2-621">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="346e2-622">Po pobraniu jednostki kursu do edycji:</span><span class="sxs-lookup"><span data-stu-id="346e2-622">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="346e2-623">Jednostka `Department` ma wartość null, jeśli nie jest jawnie załadowany.</span><span class="sxs-lookup"><span data-stu-id="346e2-623">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="346e2-624">Aby zaktualizować jednostkę `Department` kursu, jednostka musi najpierw zostać pobrana.</span><span class="sxs-lookup"><span data-stu-id="346e2-624">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="346e2-625">Gdy FK `DepartmentID` właściwość znajduje się w modelu danych, nie ma `Department` potrzeby pobierania jednostki przed aktualizacją.</span><span class="sxs-lookup"><span data-stu-id="346e2-625">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="346e2-626">Atrybut DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="346e2-626">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="346e2-627">Atrybut `[DatabaseGenerated(DatabaseGeneratedOption.None)]` określa, że PK jest dostarczany przez aplikację, a nie generowane przez bazę danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-627">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="346e2-628">Domyślnie EF Core zakłada, że wartości PK są generowane przez bazę danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-628">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="346e2-629">Wartości PK generowane przez bazę danych jest na ogół najlepszym rozwiązaniem.</span><span class="sxs-lookup"><span data-stu-id="346e2-629">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="346e2-630">Dla `Course` jednostek użytkownik określa PK.</span><span class="sxs-lookup"><span data-stu-id="346e2-630">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="346e2-631">Na przykład numer kursu, taki jak seria 1000 dla działu matematyki, seria 2000 dla działu angielskiego.</span><span class="sxs-lookup"><span data-stu-id="346e2-631">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="346e2-632">Atrybut `DatabaseGenerated` może również służyć do generowania wartości domyślnych.</span><span class="sxs-lookup"><span data-stu-id="346e2-632">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="346e2-633">Na przykład baza danych może automatycznie wygenerować pole daty, aby zarejestrować datę utworzenia lub zaktualizowania wiersza.</span><span class="sxs-lookup"><span data-stu-id="346e2-633">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="346e2-634">Aby uzyskać więcej informacji, zobacz [Wygenerowane właściwości](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="346e2-634">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="346e2-635">Właściwości klucza obcego i nawigacji</span><span class="sxs-lookup"><span data-stu-id="346e2-635">Foreign key and navigation properties</span></span>

<span data-ttu-id="346e2-636">Właściwości klucza obcego (FK) i `Course` właściwości nawigacji w jednostce odzwierciedlają następujące relacje:</span><span class="sxs-lookup"><span data-stu-id="346e2-636">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="346e2-637">Kurs jest przypisany do jednego działu, `DepartmentID` więc istnieje `Department` FK i właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="346e2-637">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="346e2-638">Kurs może mieć dowolną liczbę studentów zapisanych `Enrollments` w nim, więc właściwość nawigacji jest kolekcja:</span><span class="sxs-lookup"><span data-stu-id="346e2-638">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="346e2-639">Kurs może być prowadzony przez wielu instruktorów, więc właściwość `CourseAssignments` nawigacji jest kolekcją:</span><span class="sxs-lookup"><span data-stu-id="346e2-639">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="346e2-640">`CourseAssignment`zostanie wyjaśnione [później](#many-to-many-relationships).</span><span class="sxs-lookup"><span data-stu-id="346e2-640">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="346e2-641">Tworzenie encji Dział</span><span class="sxs-lookup"><span data-stu-id="346e2-641">Create the Department entity</span></span>

![Jednostka działu](complex-data-model/_static/department-entity.png)

<span data-ttu-id="346e2-643">Utwórz *modele/department.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="346e2-643">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="346e2-644">Atrybut Kolumna</span><span class="sxs-lookup"><span data-stu-id="346e2-644">The Column attribute</span></span>

<span data-ttu-id="346e2-645">Wcześniej `Column` atrybut był używany do zmiany mapowania nazw kolumn.</span><span class="sxs-lookup"><span data-stu-id="346e2-645">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="346e2-646">W kodzie `Department` jednostki `Column` atrybut jest używany do zmiany mapowania typów danych SQL.</span><span class="sxs-lookup"><span data-stu-id="346e2-646">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="346e2-647">Kolumna `Budget` jest definiowana przy użyciu typu pieniądza programu SQL Server w db:</span><span class="sxs-lookup"><span data-stu-id="346e2-647">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="346e2-648">Mapowanie kolumn zazwyczaj nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="346e2-648">Column mapping is generally not required.</span></span> <span data-ttu-id="346e2-649">EF Core zazwyczaj wybiera odpowiedni typ danych programu SQL Server na podstawie typu CLR dla właściwości.</span><span class="sxs-lookup"><span data-stu-id="346e2-649">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="346e2-650">Typ CLR `decimal` jest mapowany `decimal` na typ programu SQL Server.</span><span class="sxs-lookup"><span data-stu-id="346e2-650">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="346e2-651">`Budget`jest dla waluty, a typ danych pieniądza jest bardziej odpowiedni dla waluty.</span><span class="sxs-lookup"><span data-stu-id="346e2-651">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="346e2-652">Właściwości klucza obcego i nawigacji</span><span class="sxs-lookup"><span data-stu-id="346e2-652">Foreign key and navigation properties</span></span>

<span data-ttu-id="346e2-653">FK i właściwości nawigacji odzwierciedlają następujące relacje:</span><span class="sxs-lookup"><span data-stu-id="346e2-653">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="346e2-654">Dział może, ale nie musi mieć administratora.</span><span class="sxs-lookup"><span data-stu-id="346e2-654">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="346e2-655">Administrator jest zawsze instruktorem.</span><span class="sxs-lookup"><span data-stu-id="346e2-655">An administrator is always an instructor.</span></span> <span data-ttu-id="346e2-656">W związku `InstructorID` z tym właściwość jest `Instructor` uwzględniona jako FK do jednostki.</span><span class="sxs-lookup"><span data-stu-id="346e2-656">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="346e2-657">Właściwość nawigacji `Administrator` jest nazwany, ale posiada jednostki: `Instructor`</span><span class="sxs-lookup"><span data-stu-id="346e2-657">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="346e2-658">Znak zapytania (?) w poprzednim kodzie określa, że właściwość jest nullable.</span><span class="sxs-lookup"><span data-stu-id="346e2-658">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="346e2-659">Dział może mieć wiele kursów, więc istnieje właściwość nawigacji Kursy:</span><span class="sxs-lookup"><span data-stu-id="346e2-659">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="346e2-660">Uwaga: Zgodnie z konwencją EF Core umożliwia usuwanie kaskadowe dla nienastępalnych do null FK i dla relacji wiele do wielu.</span><span class="sxs-lookup"><span data-stu-id="346e2-660">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="346e2-661">Usuwanie kaskadowe może spowodować reguły usuwania kaskady kołowych.</span><span class="sxs-lookup"><span data-stu-id="346e2-661">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="346e2-662">Reguły usuwania kaskadowego cyklicznego powoduje wyjątek po dodaniu migracji.</span><span class="sxs-lookup"><span data-stu-id="346e2-662">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="346e2-663">Na przykład jeśli `Department.InstructorID` właściwość została zdefiniowana jako nie można nullable:</span><span class="sxs-lookup"><span data-stu-id="346e2-663">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="346e2-664">EF Core konfiguruje regułę usuwania kaskadowego, aby usunąć dział po usunięciu instruktora.</span><span class="sxs-lookup"><span data-stu-id="346e2-664">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="346e2-665">Usuwanie działu po usunięciu instruktora nie jest zamierzonym zachowaniem.</span><span class="sxs-lookup"><span data-stu-id="346e2-665">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="346e2-666">Następujący [płynny interfejs API](#fluent-api-alternative-to-attributes) ustawi regułę ograniczania zamiast kaskady.</span><span class="sxs-lookup"><span data-stu-id="346e2-666">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="346e2-667">Poprzedni kod wyłącza kaskadowe usuwanie w relacji department-instructor.</span><span class="sxs-lookup"><span data-stu-id="346e2-667">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="346e2-668">Aktualizowanie encji Rejestracja</span><span class="sxs-lookup"><span data-stu-id="346e2-668">Update the Enrollment entity</span></span>

<span data-ttu-id="346e2-669">Rekord rejestracji jest dla jednego kursu podjętego przez jednego ucznia.</span><span class="sxs-lookup"><span data-stu-id="346e2-669">An enrollment record is for one course taken by one student.</span></span>

![Jednostka rejestracji](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="346e2-671">Zaktualizuj *modele/enrollment.cs* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="346e2-671">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="346e2-672">Właściwości klucza obcego i nawigacji</span><span class="sxs-lookup"><span data-stu-id="346e2-672">Foreign key and navigation properties</span></span>

<span data-ttu-id="346e2-673">Właściwości FK i właściwości nawigacji odzwierciedlają następujące relacje:</span><span class="sxs-lookup"><span data-stu-id="346e2-673">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="346e2-674">Rekord rejestracji jest dla jednego kursu, `CourseID` więc istnieje właściwość FK i właściwość `Course` nawigacji:</span><span class="sxs-lookup"><span data-stu-id="346e2-674">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="346e2-675">Rekord rejestracji jest dla jednego ucznia, `StudentID` więc istnieje `Student` właściwość FK i właściwość nawigacji:</span><span class="sxs-lookup"><span data-stu-id="346e2-675">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="346e2-676">Relacje wiele do wielu</span><span class="sxs-lookup"><span data-stu-id="346e2-676">Many-to-Many Relationships</span></span>

<span data-ttu-id="346e2-677">Istnieje wiele do wielu relacji między `Student` i `Course` jednostek.</span><span class="sxs-lookup"><span data-stu-id="346e2-677">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="346e2-678">Jednostka `Enrollment` działa jako tabela sprzężenia wiele do wielu *z ładunkiem* w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-678">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="346e2-679">"Z ładunkiem" oznacza, że `Enrollment` tabela zawiera dodatkowe dane oprócz FK dla tabel `Grade`połączonych (w tym przypadku PK i ).</span><span class="sxs-lookup"><span data-stu-id="346e2-679">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="346e2-680">Na poniższej ilustracji przedstawiono, jak wyglądają te relacje na diagramie jednostek.</span><span class="sxs-lookup"><span data-stu-id="346e2-680">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="346e2-681">(Ten diagram został wygenerowany przy użyciu [ef power tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) dla EF 6.x.</span><span class="sxs-lookup"><span data-stu-id="346e2-681">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="346e2-682">Tworzenie diagramu nie jest częścią samouczka.)</span><span class="sxs-lookup"><span data-stu-id="346e2-682">Creating the diagram isn't part of the tutorial.)</span></span>

![Student-Kurs wielu do wielu relacji](complex-data-model/_static/student-course.png)

<span data-ttu-id="346e2-684">Każda linia relacji ma 1 na jednym końcu i gwiazdkę (\*) na drugim, wskazując relację jeden do wielu.</span><span class="sxs-lookup"><span data-stu-id="346e2-684">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="346e2-685">Jeśli `Enrollment` tabela nie zawiera informacji o gatunku, musiałaby zawierać`CourseID` tylko `StudentID`dwa FK (i ).</span><span class="sxs-lookup"><span data-stu-id="346e2-685">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="346e2-686">Tabela sprzężeń wiele do wielu bez ładunku jest czasami nazywana tabelą czystego sprzężenia (PJT).</span><span class="sxs-lookup"><span data-stu-id="346e2-686">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="346e2-687">I `Instructor` `Course` jednostek mają relację wiele do wielu przy użyciu tabeli sprzężenia czystego.</span><span class="sxs-lookup"><span data-stu-id="346e2-687">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="346e2-688">Uwaga: EF 6.x obsługuje tabele sprzężenia niejawnego dla relacji wiele-do-wielu, ale EF Core nie.</span><span class="sxs-lookup"><span data-stu-id="346e2-688">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="346e2-689">Aby uzyskać więcej informacji, zobacz [Relacje wiele do wielu w EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="346e2-689">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="346e2-690">Jednostka CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="346e2-690">The CourseAssignment entity</span></span>

![Jednostka courseAssignment](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="346e2-692">Utwórz *modele/courseAssignment.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="346e2-692">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="346e2-693">Instruktor-kursy</span><span class="sxs-lookup"><span data-stu-id="346e2-693">Instructor-to-Courses</span></span>

![Instruktor-kursy m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="346e2-695">The Instructor-to-Kursy wiele do wielu relacji:</span><span class="sxs-lookup"><span data-stu-id="346e2-695">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="346e2-696">Wymaga tabeli sprzężenia, która musi być reprezentowana przez zestaw jednostek.</span><span class="sxs-lookup"><span data-stu-id="346e2-696">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="346e2-697">Jest czystą tabelą sprzężenia (tabela bez ładunku).</span><span class="sxs-lookup"><span data-stu-id="346e2-697">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="346e2-698">Często nazywa się encję `EntityName1EntityName2`sprzężenia .</span><span class="sxs-lookup"><span data-stu-id="346e2-698">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="346e2-699">Na przykład tabela sprzężenia Instructor-to-Courses przy użyciu tego wzorca jest `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="346e2-699">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="346e2-700">Jednak zaleca się użycie nazwy opisującej relację.</span><span class="sxs-lookup"><span data-stu-id="346e2-700">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="346e2-701">Modele danych zaczynają się proste i rosną.</span><span class="sxs-lookup"><span data-stu-id="346e2-701">Data models start out simple and grow.</span></span> <span data-ttu-id="346e2-702">Sprzężenia bez ładowności (PJTs) często ewoluują w celu uwzględnienia ładunku.</span><span class="sxs-lookup"><span data-stu-id="346e2-702">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="346e2-703">Zaczynając od opisowej nazwy encji, nazwa nie musi się zmieniać po zmianie tabeli sprzężenia.</span><span class="sxs-lookup"><span data-stu-id="346e2-703">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="346e2-704">W idealnym przypadku jednostka sprzężenia miałaby własną naturalną (ewentualnie pojedynczą nazwę) w domenie biznesowej.</span><span class="sxs-lookup"><span data-stu-id="346e2-704">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="346e2-705">Na przykład książki i klienci mogą być połączone z jednostką sprzężenia o nazwie Oceny.</span><span class="sxs-lookup"><span data-stu-id="346e2-705">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="346e2-706">Dla instruktora do kursów wiele do wielu `CourseAssignment` relacji, `CourseInstructor`jest preferowany nad .</span><span class="sxs-lookup"><span data-stu-id="346e2-706">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="346e2-707">Klucz złożony</span><span class="sxs-lookup"><span data-stu-id="346e2-707">Composite key</span></span>

<span data-ttu-id="346e2-708">FKs nie są nullable.</span><span class="sxs-lookup"><span data-stu-id="346e2-708">FKs are not nullable.</span></span> <span data-ttu-id="346e2-709">Dwa FKs `CourseAssignment` w`InstructorID` `CourseID`( i ) razem jednoznacznie zidentyfikować każdy wiersz `CourseAssignment` tabeli.</span><span class="sxs-lookup"><span data-stu-id="346e2-709">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="346e2-710">`CourseAssignment`nie wymaga dedykowanego PK.</span><span class="sxs-lookup"><span data-stu-id="346e2-710">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="346e2-711">I `InstructorID` `CourseID` właściwości działają jako złożona PK.</span><span class="sxs-lookup"><span data-stu-id="346e2-711">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="346e2-712">Jedynym sposobem określenia złożonych PKs do EF Core jest *płynny interfejs API*.</span><span class="sxs-lookup"><span data-stu-id="346e2-712">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="346e2-713">W następnej sekcji pokazano, jak skonfigurować złożoną PK.</span><span class="sxs-lookup"><span data-stu-id="346e2-713">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="346e2-714">Klucz kompozytowy zapewnia:</span><span class="sxs-lookup"><span data-stu-id="346e2-714">The composite key ensures:</span></span>

* <span data-ttu-id="346e2-715">Wiele wierszy są dozwolone dla jednego kursu.</span><span class="sxs-lookup"><span data-stu-id="346e2-715">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="346e2-716">Wiele wierszy są dozwolone dla jednego instruktora.</span><span class="sxs-lookup"><span data-stu-id="346e2-716">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="346e2-717">Wiele wierszy dla tego samego instruktora i kursu nie jest dozwolone.</span><span class="sxs-lookup"><span data-stu-id="346e2-717">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="346e2-718">Jednostka `Enrollment` sprzężenia definiuje własną PK, więc duplikaty tego rodzaju są możliwe.</span><span class="sxs-lookup"><span data-stu-id="346e2-718">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="346e2-719">Aby zapobiec takim duplikatom:</span><span class="sxs-lookup"><span data-stu-id="346e2-719">To prevent such duplicates:</span></span>

* <span data-ttu-id="346e2-720">Dodaj unikatowy indeks w polach FK lub</span><span class="sxs-lookup"><span data-stu-id="346e2-720">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="346e2-721">Skonfiguruj `Enrollment` za pomocą `CourseAssignment`podstawowego klucza kompozytowego podobnego do .</span><span class="sxs-lookup"><span data-stu-id="346e2-721">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="346e2-722">Aby uzyskać więcej informacji, zobacz [Indeksy](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="346e2-722">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="346e2-723">Aktualizowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="346e2-723">Update the DB context</span></span>

<span data-ttu-id="346e2-724">Dodaj następujący wyróżniony kod do *pliku Data/SchoolContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="346e2-724">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="346e2-725">Powyższy kod dodaje nowe jednostki i `CourseAssignment` konfiguruje złożoną PK jednostki.</span><span class="sxs-lookup"><span data-stu-id="346e2-725">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="346e2-726">Płynna alternatywa dla API dla atrybutów</span><span class="sxs-lookup"><span data-stu-id="346e2-726">Fluent API alternative to attributes</span></span>

<span data-ttu-id="346e2-727">Metoda `OnModelCreating` w poprzednim kodzie używa *płynnego interfejsu API* do konfigurowania zachowania EF Core.</span><span class="sxs-lookup"><span data-stu-id="346e2-727">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="346e2-728">Interfejs API jest nazywany "fluent", ponieważ jest często używany przez ciągnie serię wywołań metody razem w jednej instrukcji.</span><span class="sxs-lookup"><span data-stu-id="346e2-728">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="346e2-729">[Poniższy kod](/ef/core/modeling/#use-fluent-api-to-configure-a-model) jest przykładem płynnego interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="346e2-729">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="346e2-730">W tym samouczku płynny interfejs API jest używany tylko dla mapowania bazy danych, których nie można wykonać za pomocą atrybutów.</span><span class="sxs-lookup"><span data-stu-id="346e2-730">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="346e2-731">Jednak płynny interfejs API można określić większość reguł formatowania, sprawdzania poprawności i mapowania, które można wykonać za pomocą atrybutów.</span><span class="sxs-lookup"><span data-stu-id="346e2-731">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="346e2-732">Niektóre atrybuty, takie jak `MinimumLength` nie można zastosować za pomocą interfejsu API fluent.</span><span class="sxs-lookup"><span data-stu-id="346e2-732">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="346e2-733">`MinimumLength`nie zmienia schematu, stosuje tylko regułę sprawdzania poprawności minimalnej długości.</span><span class="sxs-lookup"><span data-stu-id="346e2-733">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="346e2-734">Niektórzy deweloperzy wolą używać płynnego interfejsu API wyłącznie, aby mogli zachować swoje klasy jednostek "czyste".</span><span class="sxs-lookup"><span data-stu-id="346e2-734">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="346e2-735">Atrybuty i płynny interfejs API można mieszać.</span><span class="sxs-lookup"><span data-stu-id="346e2-735">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="346e2-736">Istnieją pewne konfiguracje, które można wykonać tylko za pomocą płynnego interfejsu API (określając kompozytowe PK).</span><span class="sxs-lookup"><span data-stu-id="346e2-736">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="346e2-737">Istnieją pewne konfiguracje, które można wykonać`MinimumLength`tylko za pomocą atrybutów ( ).</span><span class="sxs-lookup"><span data-stu-id="346e2-737">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="346e2-738">Zalecana praktyka używania płynnego interfejsu API lub atrybutów:</span><span class="sxs-lookup"><span data-stu-id="346e2-738">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="346e2-739">Wybierz jedno z tych dwóch podejść.</span><span class="sxs-lookup"><span data-stu-id="346e2-739">Choose one of these two approaches.</span></span>
* <span data-ttu-id="346e2-740">W miarę możliwości należy konsekwentnie używać wybranego podejścia.</span><span class="sxs-lookup"><span data-stu-id="346e2-740">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="346e2-741">Niektóre atrybuty używane w tym samouczku są używane do:</span><span class="sxs-lookup"><span data-stu-id="346e2-741">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="346e2-742">Tylko sprawdzanie poprawności `MinimumLength`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="346e2-742">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="346e2-743">Tylko konfiguracja EF Core `HasKey`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="346e2-743">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="346e2-744">Sprawdzanie poprawności i konfiguracja `[StringLength(50)]`EF Core (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="346e2-744">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="346e2-745">Aby uzyskać więcej informacji na temat atrybutów a płynnego interfejsu API, zobacz [Metody konfiguracji](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="346e2-745">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="346e2-746">Diagram encji przedstawiający relacje</span><span class="sxs-lookup"><span data-stu-id="346e2-746">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="346e2-747">Na poniższej ilustracji przedstawiono diagram, który ef power tools utworzyć dla ukończonego modelu szkoły.</span><span class="sxs-lookup"><span data-stu-id="346e2-747">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Diagram encji](complex-data-model/_static/diagram.png)

<span data-ttu-id="346e2-749">Na powyższym diagramie przedstawiono:</span><span class="sxs-lookup"><span data-stu-id="346e2-749">The preceding diagram shows:</span></span>

* <span data-ttu-id="346e2-750">Kilka linii relacji jeden do wielu \*(od 1 do ).</span><span class="sxs-lookup"><span data-stu-id="346e2-750">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="346e2-751">Wiersz relacji jeden do zera lub jeden (od 1 do 0..1) między `Instructor` jednostkami a `OfficeAssignment` jednostkami.</span><span class="sxs-lookup"><span data-stu-id="346e2-751">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="346e2-752">Linia relacji zero-lub-jeden-do-wielu (od 0..1 `Instructor` do `Department` \*) między elementami i jednostkami.</span><span class="sxs-lookup"><span data-stu-id="346e2-752">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="346e2-753">Seed db z danymi testowymi</span><span class="sxs-lookup"><span data-stu-id="346e2-753">Seed the DB with Test Data</span></span>

<span data-ttu-id="346e2-754">Zaktualizuj kod w *pliku Data/DbInitializer.cs*:</span><span class="sxs-lookup"><span data-stu-id="346e2-754">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="346e2-755">Powyższy kod zawiera dane źródłowe dla nowych jednostek.</span><span class="sxs-lookup"><span data-stu-id="346e2-755">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="346e2-756">Większość tego kodu tworzy nowe obiekty jednostki i ładuje przykładowe dane.</span><span class="sxs-lookup"><span data-stu-id="346e2-756">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="346e2-757">Przykładowe dane są używane do testowania.</span><span class="sxs-lookup"><span data-stu-id="346e2-757">The sample data is used for testing.</span></span> <span data-ttu-id="346e2-758">Zobacz `Enrollments` `CourseAssignments` i przykłady, ile do wielu tabel sprzężenia mogą być rozstawione.</span><span class="sxs-lookup"><span data-stu-id="346e2-758">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="346e2-759">Dodawanie migracji</span><span class="sxs-lookup"><span data-stu-id="346e2-759">Add a migration</span></span>

<span data-ttu-id="346e2-760">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="346e2-760">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="346e2-761">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="346e2-761">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="346e2-762">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="346e2-762">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="346e2-763">Poprzednie polecenie wyświetla ostrzeżenie o możliwej utracie danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-763">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="346e2-764">Jeśli `database update` polecenie jest uruchamiane, jest wywoływany następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="346e2-764">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="346e2-765">Stosowanie migracji</span><span class="sxs-lookup"><span data-stu-id="346e2-765">Apply the migration</span></span>

<span data-ttu-id="346e2-766">Teraz, gdy masz istniejącą bazę danych, musisz zastanowić się, jak zastosować do niej przyszłe zmiany.</span><span class="sxs-lookup"><span data-stu-id="346e2-766">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="346e2-767">W tym samouczku przedstawiono dwa podejścia:</span><span class="sxs-lookup"><span data-stu-id="346e2-767">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="346e2-768">Upuść i ponownie utwórz bazę danych</span><span class="sxs-lookup"><span data-stu-id="346e2-768">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="346e2-769">[Zastosuj migrację do istniejącej bazy danych](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="346e2-769">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="346e2-770">Chociaż ta metoda jest bardziej złożona i czasochłonna, jest to preferowane podejście do rzeczywistych środowisk produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="346e2-770">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="346e2-771">**Uwaga:** Jest to opcjonalna sekcja samouczka.</span><span class="sxs-lookup"><span data-stu-id="346e2-771">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="346e2-772">Możesz wykonać kroki upuszczania i ponownego tworzenia i pominąć tę sekcję.</span><span class="sxs-lookup"><span data-stu-id="346e2-772">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="346e2-773">Jeśli chcesz wykonać kroki opisane w tej sekcji, nie rób kroków upuszczania i ponownego tworzenia.</span><span class="sxs-lookup"><span data-stu-id="346e2-773">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="346e2-774">Upuść i ponownie utwórz bazę danych</span><span class="sxs-lookup"><span data-stu-id="346e2-774">Drop and re-create the database</span></span>

<span data-ttu-id="346e2-775">Kod w updated `DbInitializer` dodaje dane źródłowe dla nowych jednostek.</span><span class="sxs-lookup"><span data-stu-id="346e2-775">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="346e2-776">Aby wymusić na EF Core utworzenie nowej bazy danych, upuść i zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="346e2-776">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="346e2-777">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="346e2-777">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="346e2-778">W **konsoli Menedżera pakietów** (PMC) uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="346e2-778">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
Update-Database
```

<span data-ttu-id="346e2-779">Uruchom `Get-Help about_EntityFrameworkCore` z PMC, aby uzyskać informacje o pomocy.</span><span class="sxs-lookup"><span data-stu-id="346e2-779">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="346e2-780">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="346e2-780">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="346e2-781">Otwórz okno polecenia i przejdź do folderu projektu.</span><span class="sxs-lookup"><span data-stu-id="346e2-781">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="346e2-782">Folder projektu zawiera plik *Startup.cs.*</span><span class="sxs-lookup"><span data-stu-id="346e2-782">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="346e2-783">W oknie polecenia wprowadź następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="346e2-783">Enter the following in the command window:</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

<span data-ttu-id="346e2-784">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="346e2-784">Run the app.</span></span> <span data-ttu-id="346e2-785">Uruchomienie aplikacji uruchamia `DbInitializer.Initialize` metodę.</span><span class="sxs-lookup"><span data-stu-id="346e2-785">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="346e2-786">Wypełnia `DbInitializer.Initialize` nowy DB.</span><span class="sxs-lookup"><span data-stu-id="346e2-786">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="346e2-787">Otwórz bazę danych w SSOX:</span><span class="sxs-lookup"><span data-stu-id="346e2-787">Open the DB in SSOX:</span></span>

* <span data-ttu-id="346e2-788">Jeśli SSOX został otwarty wcześniej, kliknij przycisk **Odśwież.**</span><span class="sxs-lookup"><span data-stu-id="346e2-788">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="346e2-789">Rozwiń węzeł **Tabele.**</span><span class="sxs-lookup"><span data-stu-id="346e2-789">Expand the **Tables** node.</span></span> <span data-ttu-id="346e2-790">Zostaną wyświetlone utworzone tabele.</span><span class="sxs-lookup"><span data-stu-id="346e2-790">The created tables are displayed.</span></span>

![Tabele w SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="346e2-792">Sprawdź tabelę **CourseAssignment:**</span><span class="sxs-lookup"><span data-stu-id="346e2-792">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="346e2-793">Kliknij prawym przyciskiem myszy **tabelę CourseAssignment** i wybierz polecenie **Wyświetl dane**.</span><span class="sxs-lookup"><span data-stu-id="346e2-793">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="346e2-794">Sprawdź **CourseAssignment tabela** zawiera dane.</span><span class="sxs-lookup"><span data-stu-id="346e2-794">Verify the **CourseAssignment** table contains data.</span></span>

![Dane dotyczące kursusysignment w SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="346e2-796">Stosowanie migracji do istniejącej bazy danych</span><span class="sxs-lookup"><span data-stu-id="346e2-796">Apply the migration to the existing database</span></span>

<span data-ttu-id="346e2-797">Ta sekcja jest opcjonalna.</span><span class="sxs-lookup"><span data-stu-id="346e2-797">This section is optional.</span></span> <span data-ttu-id="346e2-798">Te kroki działają tylko wtedy, gdy pominięto poprzedni [drop i ponownie utworzyć sekcję bazy danych.](#drop)</span><span class="sxs-lookup"><span data-stu-id="346e2-798">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="346e2-799">Gdy migracje są uruchamiane z istniejącymi danymi, mogą istnieć ograniczenia FK, które nie są spełnione z istniejącymi danymi.</span><span class="sxs-lookup"><span data-stu-id="346e2-799">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="346e2-800">W przypadku danych produkcyjnych należy podjąć kroki migracji istniejących danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-800">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="346e2-801">W tej sekcji przedstawiono przykład ustalania naruszeń ograniczeń FK.</span><span class="sxs-lookup"><span data-stu-id="346e2-801">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="346e2-802">Nie należy wprowadzać tych zmian kodu bez kopii zapasowej.</span><span class="sxs-lookup"><span data-stu-id="346e2-802">Don't make these code changes without a backup.</span></span> <span data-ttu-id="346e2-803">Nie należy wprowadzać tych zmian kodu, jeśli ukończono poprzednią sekcję i zaktualizowano bazę danych.</span><span class="sxs-lookup"><span data-stu-id="346e2-803">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="346e2-804">Plik *{timestamp}_ComplexDataModel.cs* zawiera następujący kod:</span><span class="sxs-lookup"><span data-stu-id="346e2-804">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="346e2-805">Poprzedni kod dodaje niedopuszczalny `DepartmentID` do null `Course` FK do tabeli.</span><span class="sxs-lookup"><span data-stu-id="346e2-805">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="346e2-806">Baza danych z poprzedniego samouczka `Course`zawiera wiersze w , dzięki czemu tabela nie może być aktualizowana przez migracje.</span><span class="sxs-lookup"><span data-stu-id="346e2-806">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="346e2-807">Aby migracja działała `ComplexDataModel` z istniejącymi danymi:</span><span class="sxs-lookup"><span data-stu-id="346e2-807">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="346e2-808">Zmień kod, aby nadać`DepartmentID`nowej kolumnie ( ) wartość domyślną.</span><span class="sxs-lookup"><span data-stu-id="346e2-808">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="346e2-809">Utwórz fałszywy dział o nazwie "Temp", aby działać jako domyślny dział.</span><span class="sxs-lookup"><span data-stu-id="346e2-809">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="346e2-810">Naprawianie ograniczeń klucza obcego</span><span class="sxs-lookup"><span data-stu-id="346e2-810">Fix the foreign key constraints</span></span>

<span data-ttu-id="346e2-811">Zaktualizuj `ComplexDataModel` metodę klas: `Up`</span><span class="sxs-lookup"><span data-stu-id="346e2-811">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="346e2-812">Otwórz plik *{timestamp}_ComplexDataModel.cs.*</span><span class="sxs-lookup"><span data-stu-id="346e2-812">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="346e2-813">Skomentuj wiersz kodu, który `Course` dodaje kolumnę `DepartmentID` do tabeli.</span><span class="sxs-lookup"><span data-stu-id="346e2-813">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="346e2-814">Dodaj poniższy wyróżniony kod.</span><span class="sxs-lookup"><span data-stu-id="346e2-814">Add the following highlighted code.</span></span> <span data-ttu-id="346e2-815">Nowy kod idzie `.CreateTable( name: "Department"` po bloku:</span><span class="sxs-lookup"><span data-stu-id="346e2-815">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="346e2-816">Z poprzednimi zmianami `Course` istniejące wiersze będą powiązane z działem "Temp" po `ComplexDataModel` `Up` uruchomieniu metody.</span><span class="sxs-lookup"><span data-stu-id="346e2-816">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="346e2-817">Aplikacja produkcyjna:</span><span class="sxs-lookup"><span data-stu-id="346e2-817">A production app would:</span></span>

* <span data-ttu-id="346e2-818">Dołącz kod lub skrypty, aby dodać `Department` wiersze i powiązane `Course` wiersze do nowych `Department` wierszy.</span><span class="sxs-lookup"><span data-stu-id="346e2-818">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="346e2-819">Nie używaj działu "Temp" ani `Course.DepartmentID`wartości domyślnej dla programu .</span><span class="sxs-lookup"><span data-stu-id="346e2-819">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="346e2-820">Następny samouczek obejmuje powiązane dane.</span><span class="sxs-lookup"><span data-stu-id="346e2-820">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="346e2-821">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="346e2-821">Additional resources</span></span>

* [<span data-ttu-id="346e2-822">Wersja YouTube tego samouczka (część 1)</span><span class="sxs-lookup"><span data-stu-id="346e2-822">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="346e2-823">Wersja YouTube tego samouczka (część 2)</span><span class="sxs-lookup"><span data-stu-id="346e2-823">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> <span data-ttu-id="346e2-824">[Poprzedni](xref:data/ef-rp/migrations)
> [następny](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="346e2-824">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end
