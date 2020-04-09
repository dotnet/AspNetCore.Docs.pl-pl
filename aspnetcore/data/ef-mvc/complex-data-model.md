---
title: 'Samouczek: Tworzenie złożonego modelu danych - ASP.NET MVC z EF Core'
description: W tym samouczku dodaj więcej jednostek i relacji i dostosuj model danych, określając reguły formatowania, sprawdzania poprawności i mapowania.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: 91fd09874ecab8bfdb6a38a404faba04aeb73edc
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657431"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a><span data-ttu-id="0c540-103">Samouczek: Tworzenie złożonego modelu danych - ASP.NET MVC z EF Core</span><span class="sxs-lookup"><span data-stu-id="0c540-103">Tutorial: Create a complex data model - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="0c540-104">W poprzednich samouczkach pracowałeś z prostym modelem danych, który składał się z trzech jednostek.</span><span class="sxs-lookup"><span data-stu-id="0c540-104">In the previous tutorials, you worked with a simple data model that was composed of three entities.</span></span> <span data-ttu-id="0c540-105">W tym samouczku dodasz więcej jednostek i relacji, a model danych zostanie dostosowany, określając reguły formatowania, sprawdzania poprawności i mapowania bazy danych.</span><span class="sxs-lookup"><span data-stu-id="0c540-105">In this tutorial, you'll add more entities and relationships and you'll customize the data model by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="0c540-106">Po zakończeniu klasy jednostek będą stanowić ukończony model danych, który jest wyświetlany na poniższej ilustracji:</span><span class="sxs-lookup"><span data-stu-id="0c540-106">When you're finished, the entity classes will make up the completed data model that's shown in the following illustration:</span></span>

![Diagram encji](complex-data-model/_static/diagram.png)

<span data-ttu-id="0c540-108">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="0c540-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0c540-109">Dostosowywanie modelu danych</span><span class="sxs-lookup"><span data-stu-id="0c540-109">Customize the Data model</span></span>
> * <span data-ttu-id="0c540-110">Wprowadzanie zmian w encji Student</span><span class="sxs-lookup"><span data-stu-id="0c540-110">Make changes to Student entity</span></span>
> * <span data-ttu-id="0c540-111">Tworzenie jednostki Instruktor</span><span class="sxs-lookup"><span data-stu-id="0c540-111">Create Instructor entity</span></span>
> * <span data-ttu-id="0c540-112">Tworzenie encji OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="0c540-112">Create OfficeAssignment entity</span></span>
> * <span data-ttu-id="0c540-113">Modify Course entity</span><span class="sxs-lookup"><span data-stu-id="0c540-113">Modify Course entity</span></span>
> * <span data-ttu-id="0c540-114">Tworzenie encji Dział</span><span class="sxs-lookup"><span data-stu-id="0c540-114">Create Department entity</span></span>
> * <span data-ttu-id="0c540-115">Modyfikowanie jednostki rejestracji</span><span class="sxs-lookup"><span data-stu-id="0c540-115">Modify Enrollment entity</span></span>
> * <span data-ttu-id="0c540-116">Aktualizowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="0c540-116">Update the database context</span></span>
> * <span data-ttu-id="0c540-117">Baza danych materiału siewnego z danymi testowymi</span><span class="sxs-lookup"><span data-stu-id="0c540-117">Seed database with test data</span></span>
> * <span data-ttu-id="0c540-118">Dodawanie migracji</span><span class="sxs-lookup"><span data-stu-id="0c540-118">Add a migration</span></span>
> * <span data-ttu-id="0c540-119">Zmienianie ciągu połączenia</span><span class="sxs-lookup"><span data-stu-id="0c540-119">Change the connection string</span></span>
> * <span data-ttu-id="0c540-120">Aktualizowanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="0c540-120">Update the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0c540-121">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="0c540-121">Prerequisites</span></span>

* [<span data-ttu-id="0c540-122">Korzystanie z migracji EF Core</span><span class="sxs-lookup"><span data-stu-id="0c540-122">Using EF Core migrations</span></span>](migrations.md)

## <a name="customize-the-data-model"></a><span data-ttu-id="0c540-123">Dostosowywanie modelu danych</span><span class="sxs-lookup"><span data-stu-id="0c540-123">Customize the Data model</span></span>

<span data-ttu-id="0c540-124">W tej sekcji zobaczysz, jak dostosować model danych przy użyciu atrybutów określających reguły formatowania, sprawdzania poprawności i mapowania bazy danych.</span><span class="sxs-lookup"><span data-stu-id="0c540-124">In this section you'll see how to customize the data model by using attributes that specify formatting, validation, and database mapping rules.</span></span> <span data-ttu-id="0c540-125">Następnie w kilku poniższych sekcjach utworzysz kompletny model danych szkoły, dodając atrybuty do klas, które zostały już utworzone i tworząc nowe klasy dla pozostałych typów jednostek w modelu.</span><span class="sxs-lookup"><span data-stu-id="0c540-125">Then in several of the following sections you'll create the complete School data model by adding attributes to the classes you already created and creating new classes for the remaining entity types in the model.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="0c540-126">Atrybut DataType</span><span class="sxs-lookup"><span data-stu-id="0c540-126">The DataType attribute</span></span>

<span data-ttu-id="0c540-127">W przypadku dat rejestracji uczniów na wszystkich stronach internetowych jest obecnie wyświetlana godzina wraz z datą, chociaż wszystko, na czym Ci zależy, to data.</span><span class="sxs-lookup"><span data-stu-id="0c540-127">For student enrollment dates, all of the web pages currently display the time along with the date, although all you care about for this field is the date.</span></span> <span data-ttu-id="0c540-128">Za pomocą atrybutów adnotacji danych, można wprowadzić jedną zmianę kodu, który naprawi format wyświetlania w każdym widoku, który pokazuje dane.</span><span class="sxs-lookup"><span data-stu-id="0c540-128">By using data annotation attributes, you can make one code change that will fix the display format in every view that shows the data.</span></span> <span data-ttu-id="0c540-129">Aby zobaczyć przykład, jak to zrobić, należy dodać atrybut `EnrollmentDate` do `Student` właściwości w klasie.</span><span class="sxs-lookup"><span data-stu-id="0c540-129">To see an example of how to do that, you'll add an attribute to the `EnrollmentDate` property in the `Student` class.</span></span>

<span data-ttu-id="0c540-130">W *models/student.cs*dodaj `using` instrukcję `System.ComponentModel.DataAnnotations` dla obszaru `DataType` `DisplayFormat` nazw i `EnrollmentDate` dodaj i przypisuje do właściwości, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="0c540-130">In *Models/Student.cs*, add a `using` statement for the `System.ComponentModel.DataAnnotations` namespace and add `DataType` and `DisplayFormat` attributes to the `EnrollmentDate` property, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="0c540-131">Atrybut `DataType` jest używany do określenia typu danych, który jest bardziej szczegółowy niż typ wewnętrznego bazy danych.</span><span class="sxs-lookup"><span data-stu-id="0c540-131">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="0c540-132">W takim przypadku chcemy tylko śledzić datę, a nie datę i godzinę.</span><span class="sxs-lookup"><span data-stu-id="0c540-132">In this case we only want to keep track of the date, not the date and time.</span></span> <span data-ttu-id="0c540-133">Wyliczenie `DataType` zawiera wiele typów danych, takich jak Data, Godzina, Numer telefonu, Waluta, EmailAddress i inne.</span><span class="sxs-lookup"><span data-stu-id="0c540-133">The  `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="0c540-134">Atrybut `DataType` może również włączyć aplikację, aby automatycznie udostępniać funkcje specyficzne dla typu.</span><span class="sxs-lookup"><span data-stu-id="0c540-134">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="0c540-135">Na przykład `mailto:` można utworzyć łącze dla `DataType.EmailAddress`, a selektor `DataType.Date` daty może być podany w przeglądarkach obsługujących HTML5.</span><span class="sxs-lookup"><span data-stu-id="0c540-135">For example, a `mailto:` link can be created for `DataType.EmailAddress`, and a date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="0c540-136">Atrybut `DataType` emituje atrybuty `data-` HTML 5 (wymawiane rozdzielenie danych), które przeglądarki HTML 5 mogą zrozumieć.</span><span class="sxs-lookup"><span data-stu-id="0c540-136">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers can understand.</span></span> <span data-ttu-id="0c540-137">Atrybuty `DataType` nie zapewniają sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="0c540-137">The `DataType` attributes don't provide any validation.</span></span>

<span data-ttu-id="0c540-138">`DataType.Date`nie określa formatu daty, która jest wyświetlana.</span><span class="sxs-lookup"><span data-stu-id="0c540-138">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="0c540-139">Domyślnie pole danych jest wyświetlane zgodnie z domyślnymi formatami opartymi na cultureinfo serwera.</span><span class="sxs-lookup"><span data-stu-id="0c540-139">By default, the data field is displayed according to the default formats based on the server's CultureInfo.</span></span>

<span data-ttu-id="0c540-140">Atrybut `DisplayFormat` jest używany do jawnego określenia formatu daty:</span><span class="sxs-lookup"><span data-stu-id="0c540-140">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="0c540-141">Ustawienie `ApplyFormatInEditMode` określa, że formatowanie powinno być również stosowane, gdy wartość jest wyświetlana w polu tekstowym do edycji.</span><span class="sxs-lookup"><span data-stu-id="0c540-141">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied when the value is displayed in a text box for editing.</span></span> <span data-ttu-id="0c540-142">(W przypadku niektórych pól może nie być odpowiedni symbol waluty do edycji).</span><span class="sxs-lookup"><span data-stu-id="0c540-142">(You might not want that for some fields -- for example, for currency values, you might not want the currency symbol in the text box for editing.)</span></span>

<span data-ttu-id="0c540-143">Możesz użyć `DisplayFormat` tego atrybutu samodzielnie, ale ogólnie warto użyć tego `DataType` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="0c540-143">You can use the `DisplayFormat` attribute by itself, but it's generally a good idea to use the `DataType` attribute also.</span></span> <span data-ttu-id="0c540-144">Atrybut `DataType` przekazuje semantykę danych, w przeciwieństwie do sposobu renderowania na ekranie i zapewnia następujące korzyści, `DisplayFormat`które nie są dostarczane z:</span><span class="sxs-lookup"><span data-stu-id="0c540-144">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with `DisplayFormat`:</span></span>

* <span data-ttu-id="0c540-145">Przeglądarka może włączyć funkcje HTML5 (na przykład, aby wyświetlić formant kalendarza, symbol waluty odpowiednie dla ustawień regionalnych, linki e-mail, niektóre sprawdzanie poprawności danych wejściowych po stronie klienta, itp.).</span><span class="sxs-lookup"><span data-stu-id="0c540-145">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, some client-side input validation, etc.).</span></span>

* <span data-ttu-id="0c540-146">Domyślnie przeglądarka będzie renderować dane przy użyciu odpowiedniego formatu na podstawie ustawień regionalnych.</span><span class="sxs-lookup"><span data-stu-id="0c540-146">By default, the browser will render data using the correct format based on your locale.</span></span>

<span data-ttu-id="0c540-147">Aby uzyskać więcej informacji, zobacz [ \<dokumentację pomocnika> tagów wprowadzania](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="0c540-147">For more information, see the [\<input> tag helper documentation](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span></span>

<span data-ttu-id="0c540-148">Uruchom aplikację, przejdź do strony Indeks uczniów i zwróć uwagę, że godziny nie są już wyświetlane dla dat rejestracji.</span><span class="sxs-lookup"><span data-stu-id="0c540-148">Run the app, go to the Students Index page and notice that times are no longer displayed for the enrollment dates.</span></span> <span data-ttu-id="0c540-149">To samo będzie dotyczyć każdego widoku, który używa modelu Student.</span><span class="sxs-lookup"><span data-stu-id="0c540-149">The same will be true for any view that uses the Student model.</span></span>

![Indeks uczniów przedstawiający daty bez godzin](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="0c540-151">Atrybut StringLength</span><span class="sxs-lookup"><span data-stu-id="0c540-151">The StringLength attribute</span></span>

<span data-ttu-id="0c540-152">Można również określić reguły sprawdzania poprawności danych i komunikaty o błędach sprawdzania poprawności przy użyciu atrybutów.</span><span class="sxs-lookup"><span data-stu-id="0c540-152">You can also specify data validation rules and validation error messages using attributes.</span></span> <span data-ttu-id="0c540-153">Atrybut `StringLength` ustawia maksymalną długość w bazie danych i zapewnia sprawdzanie poprawności po stronie klienta i po stronie serwera dla ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="0c540-153">The `StringLength` attribute sets the maximum length  in the database and provides client side and server side validation for ASP.NET Core MVC.</span></span> <span data-ttu-id="0c540-154">Można również określić minimalną długość ciągu w tym atrybucie, ale minimalna wartość nie ma wpływu na schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="0c540-154">You can also specify the minimum string length in this attribute, but the minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="0c540-155">Załóżmy, że chcesz mieć pewność, że użytkownicy nie wejdą więcej niż 50 znaków dla nazwy.</span><span class="sxs-lookup"><span data-stu-id="0c540-155">Suppose you want to ensure that users don't enter more than 50 characters for a name.</span></span> <span data-ttu-id="0c540-156">Aby dodać to `StringLength` ograniczenie, `LastName` dodaj `FirstMidName` atrybuty do właściwości i, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="0c540-156">To add this limitation, add `StringLength` attributes to the `LastName` and `FirstMidName` properties, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="0c540-157">Atrybut `StringLength` nie uniemożliwia użytkownikowi wprowadzania odstępu dla nazwy.</span><span class="sxs-lookup"><span data-stu-id="0c540-157">The `StringLength` attribute won't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="0c540-158">Za pomocą `RegularExpression` atrybutu można zastosować ograniczenia do danych wejściowych.</span><span class="sxs-lookup"><span data-stu-id="0c540-158">You can use the `RegularExpression` attribute to apply restrictions to the input.</span></span> <span data-ttu-id="0c540-159">Na przykład poniższy kod wymaga, aby pierwszy znak był wielką literą, a pozostałe znaki alfabetycznie:</span><span class="sxs-lookup"><span data-stu-id="0c540-159">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="0c540-160">Atrybut `MaxLength` zapewnia funkcje podobne `StringLength` do atrybutu, ale nie zapewnia sprawdzania poprawności po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="0c540-160">The `MaxLength` attribute provides functionality similar to the `StringLength` attribute but doesn't provide client side validation.</span></span>

<span data-ttu-id="0c540-161">Model bazy danych został zmieniony w sposób, który wymaga zmiany w schemacie bazy danych.</span><span class="sxs-lookup"><span data-stu-id="0c540-161">The database model has now changed in a way that requires a change in the database schema.</span></span> <span data-ttu-id="0c540-162">Migracji użyjesz do aktualizacji schematu bez utraty żadnych danych, które mogą być dodane do bazy danych przy użyciu interfejsu użytkownika aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0c540-162">You'll use migrations to update the schema without losing any data that you may have added to the database by using the application UI.</span></span>

<span data-ttu-id="0c540-163">Zapisz swoje zmiany i zbuduj projekt.</span><span class="sxs-lookup"><span data-stu-id="0c540-163">Save your changes and build the project.</span></span> <span data-ttu-id="0c540-164">Następnie otwórz okno polecenia w folderze projektu i wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="0c540-164">Then open the command window in the project folder and enter the following commands:</span></span>

```dotnetcli
dotnet ef migrations add MaxLengthOnNames
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="0c540-165">Polecenie `migrations add` ostrzega, że może wystąpić utrata danych, ponieważ zmiana sprawia, że maksymalna długość krótsza dla dwóch kolumn.</span><span class="sxs-lookup"><span data-stu-id="0c540-165">The `migrations add` command warns that data loss may occur, because the change makes the maximum length shorter for two columns.</span></span>  <span data-ttu-id="0c540-166">Migracje tworzy plik o nazwie \* \<timestamp>_MaxLengthOnNames.cs\*.</span><span class="sxs-lookup"><span data-stu-id="0c540-166">Migrations creates a file named *\<timeStamp>_MaxLengthOnNames.cs*.</span></span> <span data-ttu-id="0c540-167">Ten plik zawiera `Up` kod w metodzie, która zaktualizuje bazę danych, aby dopasować bieżący model danych.</span><span class="sxs-lookup"><span data-stu-id="0c540-167">This file contains code in the `Up` method that will update the database to match the current data model.</span></span> <span data-ttu-id="0c540-168">Polecenie `database update` uruchomiło ten kod.</span><span class="sxs-lookup"><span data-stu-id="0c540-168">The `database update` command ran that code.</span></span>

<span data-ttu-id="0c540-169">Sygnatura czasowa przedpremierowana do nazwy pliku migracji jest używana przez entity framework do zamawiania migracji.</span><span class="sxs-lookup"><span data-stu-id="0c540-169">The timestamp prefixed to the migrations file name is used by Entity Framework to order the migrations.</span></span> <span data-ttu-id="0c540-170">Przed uruchomieniem polecenia update-database można utworzyć wiele migracji, a następnie wszystkie migracje są stosowane w kolejności, w jakiej zostały utworzone.</span><span class="sxs-lookup"><span data-stu-id="0c540-170">You can create multiple migrations before running the update-database command, and then all of the migrations are applied in the order in which they were created.</span></span>

<span data-ttu-id="0c540-171">Uruchom aplikację, wybierz kartę **Uczniowie,** kliknij przycisk **Utwórz nowy**i spróbuj wprowadzić nazwę dłuższą niż 50 znaków.</span><span class="sxs-lookup"><span data-stu-id="0c540-171">Run the app, select the **Students** tab, click **Create New**, and try to enter either name longer than 50 characters.</span></span> <span data-ttu-id="0c540-172">Aplikacja powinna uniemożliwić wykonanie tego.</span><span class="sxs-lookup"><span data-stu-id="0c540-172">The application should prevent you from doing this.</span></span> 

### <a name="the-column-attribute"></a><span data-ttu-id="0c540-173">Atrybut Kolumna</span><span class="sxs-lookup"><span data-stu-id="0c540-173">The Column attribute</span></span>

<span data-ttu-id="0c540-174">Można również użyć atrybutów, aby kontrolować, jak klasy i właściwości są mapowane do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="0c540-174">You can also use attributes to control how your classes and properties are mapped to the database.</span></span> <span data-ttu-id="0c540-175">Załóżmy, że `FirstMidName` użyto nazwy pola imienia, ponieważ pole może również zawierać drugie imię.</span><span class="sxs-lookup"><span data-stu-id="0c540-175">Suppose you had used the name `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span> <span data-ttu-id="0c540-176">Ale chcesz, aby kolumna `FirstName`bazy danych ma być nazwany , ponieważ użytkownicy, którzy będą pisać zapytania ad hoc względem bazy danych są przyzwyczajeni do tej nazwy.</span><span class="sxs-lookup"><span data-stu-id="0c540-176">But you want the database column to be named `FirstName`, because users who will be writing ad-hoc queries against the database are accustomed to that name.</span></span> <span data-ttu-id="0c540-177">Aby wykonać to mapowanie, `Column` można użyć atrybutu.</span><span class="sxs-lookup"><span data-stu-id="0c540-177">To make this mapping, you can use the `Column` attribute.</span></span>

<span data-ttu-id="0c540-178">Atrybut `Column` określa, że podczas tworzenia bazy danych kolumna `Student` tabeli, `FirstMidName` która mapuje właściwość, zostanie nazwana `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="0c540-178">The `Column` attribute specifies that when the database is created, the column of the `Student` table that maps to the `FirstMidName` property will be named `FirstName`.</span></span> <span data-ttu-id="0c540-179">Innymi słowy, gdy kod odnosi `Student.FirstMidName`się do , dane będą `FirstName` pochodzić `Student` z lub być aktualizowane w kolumnie tabeli.</span><span class="sxs-lookup"><span data-stu-id="0c540-179">In other words, when your code refers to `Student.FirstMidName`, the data will come from or be updated in the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="0c540-180">Jeśli nie określisz nazw kolumn, otrzymasz taką samą nazwę jak nazwa właściwości.</span><span class="sxs-lookup"><span data-stu-id="0c540-180">If you don't specify column names, they're given the same name as the property name.</span></span>

<span data-ttu-id="0c540-181">W pliku *Student.cs* dodaj instrukcję `using` i `System.ComponentModel.DataAnnotations.Schema` dodaj atrybut nazwy kolumny `FirstMidName` do właściwości, jak pokazano w poniższym wyróżnionym kodzie:</span><span class="sxs-lookup"><span data-stu-id="0c540-181">In the *Student.cs* file, add a `using` statement for `System.ComponentModel.DataAnnotations.Schema` and add the column name attribute to the `FirstMidName` property, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="0c540-182">Dodanie atrybutu `Column` zmienia model kopii `SchoolContext`zapasowej , więc nie będzie zgodny z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="0c540-182">The addition of the `Column` attribute changes the model backing the `SchoolContext`, so it won't match the database.</span></span>

<span data-ttu-id="0c540-183">Zapisz swoje zmiany i zbuduj projekt.</span><span class="sxs-lookup"><span data-stu-id="0c540-183">Save your changes and build the project.</span></span> <span data-ttu-id="0c540-184">Następnie otwórz okno polecenia w folderze projektu i wprowadź następujące polecenia, aby utworzyć kolejną migrację:</span><span class="sxs-lookup"><span data-stu-id="0c540-184">Then open the command window in the project folder and enter the following commands to create another migration:</span></span>

```dotnetcli
dotnet ef migrations add ColumnFirstName
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="0c540-185">W **Eksploratorze obiektów programu SQL Server**otwórz projektanta tabeli ucznia, klikając dwukrotnie tabelę **Student.**</span><span class="sxs-lookup"><span data-stu-id="0c540-185">In **SQL Server Object Explorer**, open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabela studentów w SSOX po migracji](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="0c540-187">Przed zastosowaniem pierwszych dwóch migracji kolumny nazw były typu nvarchar(MAX).</span><span class="sxs-lookup"><span data-stu-id="0c540-187">Before you applied the first two migrations, the name columns were of type nvarchar(MAX).</span></span> <span data-ttu-id="0c540-188">Są one teraz nvarchar(50) i nazwa kolumny została zmieniona z FirstMidName na FirstName.</span><span class="sxs-lookup"><span data-stu-id="0c540-188">They're now nvarchar(50) and the column name has changed from FirstMidName to FirstName.</span></span>

> [!Note]
> <span data-ttu-id="0c540-189">Jeśli spróbujesz skompilować przed zakończeniem tworzenia wszystkich klas jednostek w poniższych sekcjach, mogą pojawić się błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="0c540-189">If you try to compile before you finish creating all of the entity classes in the following sections, you might get compiler errors.</span></span>

## <a name="changes-to-student-entity"></a><span data-ttu-id="0c540-190">Zmiany w jednostce Student</span><span class="sxs-lookup"><span data-stu-id="0c540-190">Changes to Student entity</span></span>

![Jednostka studencka](complex-data-model/_static/student-entity.png)

<span data-ttu-id="0c540-192">W *Models/Student.cs*zastąp kod dodany wcześniej następującym kodem.</span><span class="sxs-lookup"><span data-stu-id="0c540-192">In *Models/Student.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="0c540-193">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="0c540-193">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="0c540-194">Wymagany atrybut</span><span class="sxs-lookup"><span data-stu-id="0c540-194">The Required attribute</span></span>

<span data-ttu-id="0c540-195">Atrybut `Required` sprawia, że właściwości nazwy wymagane pola.</span><span class="sxs-lookup"><span data-stu-id="0c540-195">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="0c540-196">Atrybut `Required` nie jest potrzebny dla typów niedopuszczania do wartości, takich jak typy wartości (DateTime, int, double, float itp.).</span><span class="sxs-lookup"><span data-stu-id="0c540-196">The `Required` attribute isn't needed for non-nullable types such as value types (DateTime, int, double, float, etc.).</span></span> <span data-ttu-id="0c540-197">Typy, których nie można mieć wartości null, są automatycznie traktowane jako pola wymagane.</span><span class="sxs-lookup"><span data-stu-id="0c540-197">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="0c540-198">Atrybut `Required` musi być używany `MinimumLength` z `MinimumLength` do wymuszania.</span><span class="sxs-lookup"><span data-stu-id="0c540-198">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="0c540-199">Atrybut Display</span><span class="sxs-lookup"><span data-stu-id="0c540-199">The Display attribute</span></span>

<span data-ttu-id="0c540-200">Atrybut `Display` określa, że podpis dla pól tekstowych powinien być "Imię", "Nazwisko", "Imię" i "Data rejestracji" zamiast nazwy właściwości w każdym wystąpieniu (który nie ma miejsca dzielącego wyrazy).</span><span class="sxs-lookup"><span data-stu-id="0c540-200">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date" instead of the property name in each instance (which has no space dividing the words).</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="0c540-201">Właściwość obliczana fullname</span><span class="sxs-lookup"><span data-stu-id="0c540-201">The FullName calculated property</span></span>

<span data-ttu-id="0c540-202">`FullName`jest obliczoną właściwością, która zwraca wartość utworzoną przez łączenie dwóch innych właściwości.</span><span class="sxs-lookup"><span data-stu-id="0c540-202">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="0c540-203">W związku z tym ma tylko `FullName` get akcesor i żadna kolumna nie zostaną wygenerowane w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="0c540-203">Therefore it has only a get accessor, and no `FullName` column will be generated in the database.</span></span>

## <a name="create-instructor-entity"></a><span data-ttu-id="0c540-204">Tworzenie jednostki Instruktor</span><span class="sxs-lookup"><span data-stu-id="0c540-204">Create Instructor entity</span></span>

![Jednostka instruktora](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="0c540-206">Utwórz *Models/Instructor.cs*, zastępując kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="0c540-206">Create *Models/Instructor.cs*, replacing the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

<span data-ttu-id="0c540-207">Należy zauważyć, że kilka właściwości są takie same w student i instruktora jednostek.</span><span class="sxs-lookup"><span data-stu-id="0c540-207">Notice that several properties are the same in the Student and Instructor entities.</span></span> <span data-ttu-id="0c540-208">W samouczku [implementacji dziedziczenia](inheritance.md) w dalszej części tej serii, będziesz refaktoryzuje ten kod, aby wyeliminować nadmiarowości.</span><span class="sxs-lookup"><span data-stu-id="0c540-208">In the [Implementing Inheritance](inheritance.md) tutorial later in this series, you'll refactor this code to eliminate the redundancy.</span></span>

<span data-ttu-id="0c540-209">Można umieścić wiele atrybutów w jednym wierszu, dzięki czemu można również napisać atrybuty w `HireDate` następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="0c540-209">You can put multiple attributes on one line, so you could also write the `HireDate` attributes as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="0c540-210">Właściwości nawigacji w ramach kursuAssignments i OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="0c540-210">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="0c540-211">Właściwości `CourseAssignments` `OfficeAssignment` i są właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="0c540-211">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="0c540-212">Instruktor może uczyć dowolną liczbę kursów, więc `CourseAssignments` jest zdefiniowany jako kolekcja.</span><span class="sxs-lookup"><span data-stu-id="0c540-212">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="0c540-213">Jeśli właściwość nawigacji może pomieścić wiele jednostek, jej typ musi być listą, na której można dodawać, usuwać i aktualizować wpisy.</span><span class="sxs-lookup"><span data-stu-id="0c540-213">If a navigation property can hold multiple entities, its type must be a list in which entries can be added, deleted, and updated.</span></span>  <span data-ttu-id="0c540-214">Można określić `ICollection<T>` lub typ, taki jak `List<T>` lub `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="0c540-214">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="0c540-215">Jeśli określisz `ICollection<T>`, `HashSet<T>` EF tworzy kolekcję domyślnie.</span><span class="sxs-lookup"><span data-stu-id="0c540-215">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="0c540-216">Powód, dla `CourseAssignment` którego są to jednostki jest wyjaśnione poniżej w sekcji o relacji wiele do wielu.</span><span class="sxs-lookup"><span data-stu-id="0c540-216">The reason why these are `CourseAssignment` entities is explained below in the section about many-to-many relationships.</span></span>

<span data-ttu-id="0c540-217">Reguły biznesowe contoso University twierdzą, że instruktor może `OfficeAssignment` mieć tylko w większości jednego biura, więc właściwość posiada jedną jednostkę OfficeAssignment (która może mieć wartość null, jeśli nie jest przypisane żadne biuro).</span><span class="sxs-lookup"><span data-stu-id="0c540-217">Contoso University business rules state that an instructor can only have at most one office, so the `OfficeAssignment` property holds a single OfficeAssignment entity (which may be null if no office is assigned).</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a><span data-ttu-id="0c540-218">Tworzenie encji OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="0c540-218">Create OfficeAssignment entity</span></span>

![Encja officeassignment](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="0c540-220">Utwórz *modele/officeassignment.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="0c540-220">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="0c540-221">Atrybut Klucz</span><span class="sxs-lookup"><span data-stu-id="0c540-221">The Key attribute</span></span>

<span data-ttu-id="0c540-222">Istnieje relacja jeden do zera lub jeden między instructor i OfficeAssignment jednostek.</span><span class="sxs-lookup"><span data-stu-id="0c540-222">There's a one-to-zero-or-one relationship  between the Instructor and the OfficeAssignment entities.</span></span> <span data-ttu-id="0c540-223">Przypisanie biura istnieje tylko w odniesieniu do instruktora, który jest przypisany do, a zatem jego klucz podstawowy jest również jego klucz obcy do instructor jednostki.</span><span class="sxs-lookup"><span data-stu-id="0c540-223">An office assignment only exists in relation to the instructor it's assigned to, and therefore its primary key is also its foreign key to the Instructor entity.</span></span> <span data-ttu-id="0c540-224">Ale Entity Framework nie może automatycznie rozpoznać InstructorID jako klucz podstawowy tej jednostki, ponieważ jego nazwa nie jest zgodna z konwencją nazewnictwa identyfikatora lub classnameID.</span><span class="sxs-lookup"><span data-stu-id="0c540-224">But the Entity Framework can't automatically recognize InstructorID as the primary key of this entity because its name doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="0c540-225">W związku `Key` z tym atrybut jest używany do identyfikowania go jako klucz:</span><span class="sxs-lookup"><span data-stu-id="0c540-225">Therefore, the `Key` attribute is used to identify it as the key:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="0c540-226">Można również użyć `Key` atrybutu, jeśli jednostka ma swój własny klucz podstawowy, ale chcesz nazwać właściwość czymś innym niż classnameID lub ID.</span><span class="sxs-lookup"><span data-stu-id="0c540-226">You can also use the `Key` attribute if the entity does have its own primary key but you want to name the property something other than classnameID or ID.</span></span>

<span data-ttu-id="0c540-227">Domyślnie EF traktuje klucz jako nie-bazy danych generowane, ponieważ kolumna jest dla relacji identyfikującej.</span><span class="sxs-lookup"><span data-stu-id="0c540-227">By default, EF treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="0c540-228">Właściwość nawigacji instruktora</span><span class="sxs-lookup"><span data-stu-id="0c540-228">The Instructor navigation property</span></span>

<span data-ttu-id="0c540-229">Instructor Jednostka ma nullable `OfficeAssignment` właściwości nawigacji (ponieważ instruktor może nie mieć przypisania biura), a OfficeAssignment jednostki ma nienastępne `Instructor` null `InstructorID` właściwości nawigacji (ponieważ przypisanie biura nie może istnieć bez instruktora -- jest nie można anulować).</span><span class="sxs-lookup"><span data-stu-id="0c540-229">The Instructor entity has a nullable `OfficeAssignment` navigation property (because an instructor might not have an office assignment), and the OfficeAssignment entity has a non-nullable `Instructor` navigation property (because an office assignment can't exist without an instructor -- `InstructorID` is non-nullable).</span></span> <span data-ttu-id="0c540-230">Gdy Instructor jednostki ma powiązaną jednostkę OfficeAssignment, każda jednostka będzie miała odwołanie do drugiej w jego właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="0c540-230">When an Instructor entity has a related OfficeAssignment entity, each entity will have a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="0c540-231">Można umieścić `[Required]` atrybut na Instructor navigation właściwości, aby określić, że musi istnieć powiązany instruktor, ale nie trzeba tego robić, ponieważ klucz `InstructorID` obcy (który jest również klucz do tej tabeli) jest nie można anulować.</span><span class="sxs-lookup"><span data-stu-id="0c540-231">You could put a `[Required]` attribute on the Instructor navigation property to specify that there must be a related instructor, but you don't have to do that because the `InstructorID` foreign key (which is also the key to this table) is non-nullable.</span></span>

## <a name="modify-course-entity"></a><span data-ttu-id="0c540-232">Modify Course entity</span><span class="sxs-lookup"><span data-stu-id="0c540-232">Modify Course entity</span></span>

![Jednostka kursu](complex-data-model/_static/course-entity.png)

<span data-ttu-id="0c540-234">W *Models/Course.cs*zastąp kod dodany wcześniej następującym kodem.</span><span class="sxs-lookup"><span data-stu-id="0c540-234">In *Models/Course.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="0c540-235">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="0c540-235">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="0c540-236">Jednostka kursu ma właściwość `DepartmentID` klucza obcego, która wskazuje `Department` na powiązaną jednostkę Działu i ma właściwość nawigacji.</span><span class="sxs-lookup"><span data-stu-id="0c540-236">The course entity has a foreign key property `DepartmentID` which points to the related Department entity and it has a `Department` navigation property.</span></span>

<span data-ttu-id="0c540-237">Entity Framework nie wymaga, aby dodać właściwość klucza obcego do modelu danych, gdy masz właściwość nawigacji dla jednostki pokrewne.</span><span class="sxs-lookup"><span data-stu-id="0c540-237">The Entity Framework doesn't require you to add a foreign key property to your data model when you have a navigation property for a related entity.</span></span>  <span data-ttu-id="0c540-238">EF automatycznie tworzy klucze obce w bazie danych, gdziekolwiek są one potrzebne i tworzy [właściwości cienia](/ef/core/modeling/shadow-properties) dla nich.</span><span class="sxs-lookup"><span data-stu-id="0c540-238">EF automatically creates foreign keys in the database wherever they're needed and creates [shadow properties](/ef/core/modeling/shadow-properties) for them.</span></span> <span data-ttu-id="0c540-239">Ale posiadanie klucza obcego w modelu danych może ułatwić i bardziej efektywne aktualizacje.</span><span class="sxs-lookup"><span data-stu-id="0c540-239">But having the foreign key in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="0c540-240">Na przykład podczas pobierania jednostki kursu do edycji, Dział jednostki jest null, jeśli nie załadować, więc podczas aktualizowania jednostki kursu, trzeba najpierw pobrać jednostki Dział.</span><span class="sxs-lookup"><span data-stu-id="0c540-240">For example, when you fetch a course entity to edit, the  Department entity is null if you don't load it, so when you update the course entity, you would have to first fetch the Department entity.</span></span> <span data-ttu-id="0c540-241">Gdy właściwość `DepartmentID` klucza obcego jest uwzględniona w modelu danych, nie trzeba pobierać jednostki Dział przed aktualizacją.</span><span class="sxs-lookup"><span data-stu-id="0c540-241">When the foreign key property `DepartmentID` is included in the data model, you don't need to fetch the Department entity before you update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="0c540-242">Atrybut DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="0c540-242">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="0c540-243">Atrybut `DatabaseGenerated` z parametrem `None` we `CourseID` właściwości określa, że wartości klucza podstawowego są dostarczane przez użytkownika, a nie generowane przez bazę danych.</span><span class="sxs-lookup"><span data-stu-id="0c540-243">The `DatabaseGenerated` attribute with the `None` parameter on the `CourseID` property specifies that primary key values are provided by the user rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="0c540-244">Domyślnie entity framework zakłada, że wartości klucza podstawowego są generowane przez bazę danych.</span><span class="sxs-lookup"><span data-stu-id="0c540-244">By default, Entity Framework assumes that primary key values are generated by the database.</span></span> <span data-ttu-id="0c540-245">To jest to, co chcesz w większości scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="0c540-245">That's what you want in most scenarios.</span></span> <span data-ttu-id="0c540-246">Jednak dla jednostek Course użyjesz numeru kursu określonego przez użytkownika, takiego jak seria 1000 dla jednego działu, seria 2000 dla innego działu i tak dalej.</span><span class="sxs-lookup"><span data-stu-id="0c540-246">However, for Course entities, you'll use a user-specified course number such as a 1000 series for one department, a 2000 series for another department, and so on.</span></span>

<span data-ttu-id="0c540-247">Atrybut `DatabaseGenerated` może również służyć do generowania wartości domyślnych, jak w przypadku kolumn bazy danych używanych do rejestrowania daty wiersz został utworzony lub zaktualizowany.</span><span class="sxs-lookup"><span data-stu-id="0c540-247">The `DatabaseGenerated` attribute can also be used to generate default values, as in the case of database columns used to record the date a row was created or updated.</span></span>  <span data-ttu-id="0c540-248">Aby uzyskać więcej informacji, zobacz [Wygenerowane właściwości](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="0c540-248">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="0c540-249">Właściwości klucza obcego i nawigacji</span><span class="sxs-lookup"><span data-stu-id="0c540-249">Foreign key and navigation properties</span></span>

<span data-ttu-id="0c540-250">Właściwości klucza obcego i właściwości nawigacji w Course jednostki odzwierciedlają następujące relacje:</span><span class="sxs-lookup"><span data-stu-id="0c540-250">The foreign key properties and navigation properties in the Course entity reflect the following relationships:</span></span>

<span data-ttu-id="0c540-251">Kurs jest przypisany do jednego działu, `DepartmentID` więc istnieje `Department` klucz obcy i właściwość nawigacji z powodów wymienionych powyżej.</span><span class="sxs-lookup"><span data-stu-id="0c540-251">A course is assigned to one department, so there's a `DepartmentID` foreign key and a `Department` navigation property for the reasons mentioned above.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="0c540-252">Kurs może mieć dowolną liczbę studentów zapisanych `Enrollments` w nim, więc właściwość nawigacji jest kolekcja:</span><span class="sxs-lookup"><span data-stu-id="0c540-252">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="0c540-253">Kurs może być prowadzony przez wielu instruktorów, więc właściwość `CourseAssignments` `CourseAssignment` nawigacji jest kolekcją (typ jest wyjaśniony [później):](#many-to-many-relationships)</span><span class="sxs-lookup"><span data-stu-id="0c540-253">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection (the type `CourseAssignment` is explained [later](#many-to-many-relationships)):</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a><span data-ttu-id="0c540-254">Tworzenie encji Dział</span><span class="sxs-lookup"><span data-stu-id="0c540-254">Create Department entity</span></span>

![Jednostka działu](complex-data-model/_static/department-entity.png)

<span data-ttu-id="0c540-256">Utwórz *modele/department.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="0c540-256">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="0c540-257">Atrybut Kolumna</span><span class="sxs-lookup"><span data-stu-id="0c540-257">The Column attribute</span></span>

<span data-ttu-id="0c540-258">Wcześniej użyto `Column` atrybutu do zmiany mapowania nazw kolumn.</span><span class="sxs-lookup"><span data-stu-id="0c540-258">Earlier you used the `Column` attribute to change column name mapping.</span></span> <span data-ttu-id="0c540-259">W kodzie jednostki Dział `Column` atrybut jest używany do zmiany mapowania typów danych SQL, tak aby kolumna była definiowana przy użyciu typu pieniądza programu SQL Server w bazie danych:</span><span class="sxs-lookup"><span data-stu-id="0c540-259">In the code for the Department entity, the `Column` attribute is being used to change SQL data type mapping so that the column will be defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="0c540-260">Mapowanie kolumn zazwyczaj nie jest wymagane, ponieważ Entity Framework wybiera odpowiedni typ danych programu SQL Server na podstawie typu CLR zdefiniowanego dla właściwości.</span><span class="sxs-lookup"><span data-stu-id="0c540-260">Column mapping is generally not required, because the Entity Framework chooses the appropriate SQL Server data type based on the CLR type that you define for the property.</span></span> <span data-ttu-id="0c540-261">Typ CLR `decimal` jest mapowany `decimal` na typ programu SQL Server.</span><span class="sxs-lookup"><span data-stu-id="0c540-261">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="0c540-262">Ale w tym przypadku wiesz, że kolumna będzie trzymania kwot walutowych, a typ danych pieniądza jest bardziej odpowiedni do tego.</span><span class="sxs-lookup"><span data-stu-id="0c540-262">But in this case you know that the column will be holding currency amounts, and the money data type is more appropriate for that.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="0c540-263">Właściwości klucza obcego i nawigacji</span><span class="sxs-lookup"><span data-stu-id="0c540-263">Foreign key and navigation properties</span></span>

<span data-ttu-id="0c540-264">Klucz obcy i właściwości nawigacji odzwierciedlają następujące relacje:</span><span class="sxs-lookup"><span data-stu-id="0c540-264">The foreign key and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="0c540-265">Dział może lub nie może mieć administratora, a administrator jest zawsze instruktorem.</span><span class="sxs-lookup"><span data-stu-id="0c540-265">A department may or may not have an administrator, and an administrator is always an instructor.</span></span> <span data-ttu-id="0c540-266">W związku `InstructorID` z tym właściwość jest uwzględniona jako klucz obcy do `int` Instructor jednostki i znak zapytania jest dodawany po oznaczeniu typu, aby oznaczyć właściwość jako nullable.</span><span class="sxs-lookup"><span data-stu-id="0c540-266">Therefore the `InstructorID` property is included as the foreign key to the Instructor entity, and a question mark is added after the `int` type designation to mark the property as nullable.</span></span> <span data-ttu-id="0c540-267">Właściwość nawigacji `Administrator` jest nazwany, ale posiada Instructor jednostki:</span><span class="sxs-lookup"><span data-stu-id="0c540-267">The navigation property is named `Administrator` but holds an Instructor entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="0c540-268">Dział może mieć wiele kursów, więc istnieje właściwość nawigacji Kursy:</span><span class="sxs-lookup"><span data-stu-id="0c540-268">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> <span data-ttu-id="0c540-269">Zgodnie z konwencją Entity Framework umożliwia kaskadowe usuwanie dla kluczy obcych nienastępnych do wartości null i dla relacji wiele-do-wielu.</span><span class="sxs-lookup"><span data-stu-id="0c540-269">By convention, the Entity Framework enables cascade delete for non-nullable foreign keys and for many-to-many relationships.</span></span> <span data-ttu-id="0c540-270">Może to spowodować reguły usuwania kaskadowego cyklicznego, co spowoduje wyjątek podczas próby dodania migracji.</span><span class="sxs-lookup"><span data-stu-id="0c540-270">This can result in circular cascade delete rules, which will cause an exception when you try to add a migration.</span></span> <span data-ttu-id="0c540-271">Na przykład jeśli nie zdefiniowano Department.InstructorID właściwość jako nullable, EF skonfiguruje regułę usuwania kaskadowego, aby usunąć dział po usunięciu instruktora, który nie jest to, co chcesz mieć miejsce.</span><span class="sxs-lookup"><span data-stu-id="0c540-271">For example, if you didn't define the Department.InstructorID property as nullable, EF would configure a cascade delete rule to delete the department when you delete the instructor, which isn't what you want to have happen.</span></span> <span data-ttu-id="0c540-272">Jeśli reguły biznesowe `InstructorID` wymagały, aby właściwość była nienależna do wartości null, należy użyć następującej instrukcji interfejsu API fluent, aby wyłączyć usuwanie kaskadowe w relacji:</span><span class="sxs-lookup"><span data-stu-id="0c540-272">If your business rules required the `InstructorID` property to be non-nullable, you would have to use the following fluent API statement to disable cascade delete on the relationship:</span></span>
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a><span data-ttu-id="0c540-273">Modyfikowanie jednostki rejestracji</span><span class="sxs-lookup"><span data-stu-id="0c540-273">Modify Enrollment entity</span></span>

![Jednostka rejestracji](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="0c540-275">W *Models/Enrollment.cs*zastąp kod dodany wcześniej następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="0c540-275">In *Models/Enrollment.cs*, replace the code you added earlier with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="0c540-276">Właściwości klucza obcego i nawigacji</span><span class="sxs-lookup"><span data-stu-id="0c540-276">Foreign key and navigation properties</span></span>

<span data-ttu-id="0c540-277">Właściwości klucza obcego i właściwości nawigacji odzwierciedlają następujące relacje:</span><span class="sxs-lookup"><span data-stu-id="0c540-277">The foreign key properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="0c540-278">Rekord rejestracji jest dla jednego kursu, więc `CourseID` istnieje właściwość `Course` klucza obcego i właściwości nawigacji:</span><span class="sxs-lookup"><span data-stu-id="0c540-278">An enrollment record is for a single course, so there's a `CourseID` foreign key property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="0c540-279">Rekord rejestracji jest dla jednego ucznia, `StudentID` więc istnieje właściwość klucza obcego `Student` i właściwość nawigacji:</span><span class="sxs-lookup"><span data-stu-id="0c540-279">An enrollment record is for a single student, so there's a `StudentID` foreign key property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="0c540-280">Relacje wiele do wielu</span><span class="sxs-lookup"><span data-stu-id="0c540-280">Many-to-Many relationships</span></span>

<span data-ttu-id="0c540-281">Istnieje wiele do wielu relacji między jednostkami Student i Course, a jednostka Enrollment działa jako tabela sprzężenia wiele do wielu *z ładunkiem* w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="0c540-281">There's a many-to-many relationship between the Student and Course entities, and the Enrollment entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="0c540-282">"Z ładunkiem" oznacza, że enrollment tabela zawiera dodatkowe dane oprócz kluczy obcych dla tabel połączonych (w tym przypadku klucz podstawowy i Grade właściwości).</span><span class="sxs-lookup"><span data-stu-id="0c540-282">"With payload" means that the Enrollment table contains additional data besides foreign keys for the joined tables (in this case, a primary key and a Grade property).</span></span>

<span data-ttu-id="0c540-283">Na poniższej ilustracji przedstawiono, jak wyglądają te relacje na diagramie jednostek.</span><span class="sxs-lookup"><span data-stu-id="0c540-283">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="0c540-284">(Ten diagram został wygenerowany przy użyciu entity framework power tools dla EF 6.x; tworzenie diagramu nie jest częścią samouczka, jest po prostu używany w tym miejscu jako ilustracja.)</span><span class="sxs-lookup"><span data-stu-id="0c540-284">(This diagram was generated using the Entity Framework Power Tools for EF 6.x; creating the diagram isn't part of the tutorial, it's just being used here as an illustration.)</span></span>

![Student-Kurs wielu do wielu relacji](complex-data-model/_static/student-course.png)

<span data-ttu-id="0c540-286">Każda linia relacji ma 1 na jednym końcu i gwiazdkę (\*) na drugim, wskazując relację jeden do wielu.</span><span class="sxs-lookup"><span data-stu-id="0c540-286">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="0c540-287">Jeśli enrollment tabeli nie zawiera informacji o klasie, to tylko musi zawierać dwa klucze obce CourseID i StudentID.</span><span class="sxs-lookup"><span data-stu-id="0c540-287">If the Enrollment table didn't include grade information, it would only need to contain the two foreign keys CourseID and StudentID.</span></span> <span data-ttu-id="0c540-288">W takim przypadku będzie to tabela sprzężenia wiele do wielu bez ładunku (lub tabela czystego sprzężenia) w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="0c540-288">In that case, it would be a many-to-many join table without payload (or a pure join table) in the database.</span></span> <span data-ttu-id="0c540-289">Instruktor i kurs jednostek mają tego rodzaju relacji wiele do wielu, a następnym krokiem jest utworzenie klasy jednostki do działania jako tabela sprzężenia bez ładunku.</span><span class="sxs-lookup"><span data-stu-id="0c540-289">The Instructor and Course entities have that kind of many-to-many relationship, and your next step is to create an entity class to function as a join table without payload.</span></span>

<span data-ttu-id="0c540-290">(EF 6.x obsługuje niejawne tabele sprzężenia dla relacji wiele do wielu, ale EF Core nie.</span><span class="sxs-lookup"><span data-stu-id="0c540-290">(EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="0c540-291">Aby uzyskać więcej informacji, zobacz [dyskusję w repozytorium EF Core GitHub](https://github.com/aspnet/EntityFramework/issues/1368).)</span><span class="sxs-lookup"><span data-stu-id="0c540-291">For more information, see the [discussion in the EF Core GitHub repository](https://github.com/aspnet/EntityFramework/issues/1368).)</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="0c540-292">Jednostka CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="0c540-292">The CourseAssignment entity</span></span>

![Jednostka courseAssignment](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="0c540-294">Utwórz *modele/courseAssignment.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="0c540-294">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a><span data-ttu-id="0c540-295">Dołączanie nazw encji</span><span class="sxs-lookup"><span data-stu-id="0c540-295">Join entity names</span></span>

<span data-ttu-id="0c540-296">Tabela sprzężenia jest wymagana w bazie danych dla relacji Instructor-to-Courses wiele-do-wielu i musi być reprezentowana przez zestaw jednostek.</span><span class="sxs-lookup"><span data-stu-id="0c540-296">A join table is required in the database for the Instructor-to-Courses many-to-many relationship, and it has to be represented by an entity set.</span></span> <span data-ttu-id="0c540-297">Często nazywa się jednostkę `EntityName1EntityName2`sprzężenia, która `CourseInstructor`w tym przypadku byłaby .</span><span class="sxs-lookup"><span data-stu-id="0c540-297">It's common to name a join entity `EntityName1EntityName2`, which in this case would be `CourseInstructor`.</span></span> <span data-ttu-id="0c540-298">Zaleca się jednak wybranie nazwy opisującej relację.</span><span class="sxs-lookup"><span data-stu-id="0c540-298">However, we recommend that you choose a name that describes the relationship.</span></span> <span data-ttu-id="0c540-299">Modele danych zaczynają się proste i rosną, a bez ładowności łączy często coraz ładunków później.</span><span class="sxs-lookup"><span data-stu-id="0c540-299">Data models start out simple and grow, with no-payload joins frequently getting payloads later.</span></span> <span data-ttu-id="0c540-300">Jeśli zaczniesz od opisowej nazwy encji, nie trzeba będzie później zmieniać nazwy.</span><span class="sxs-lookup"><span data-stu-id="0c540-300">If you start with a descriptive entity name, you won't have to change the name later.</span></span> <span data-ttu-id="0c540-301">W idealnym przypadku jednostka sprzężenia miałaby własną naturalną (ewentualnie pojedynczą nazwę) w domenie biznesowej.</span><span class="sxs-lookup"><span data-stu-id="0c540-301">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="0c540-302">Na przykład książki i klienci mogą być połączone za pośrednictwem ocen.</span><span class="sxs-lookup"><span data-stu-id="0c540-302">For example, Books and Customers could be linked through Ratings.</span></span> <span data-ttu-id="0c540-303">Dla tej `CourseAssignment` relacji, jest `CourseInstructor`lepszym wyborem niż .</span><span class="sxs-lookup"><span data-stu-id="0c540-303">For this relationship, `CourseAssignment` is a better choice than `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="0c540-304">Klucz złożony</span><span class="sxs-lookup"><span data-stu-id="0c540-304">Composite key</span></span>

<span data-ttu-id="0c540-305">Ponieważ klucze obce nie są możliwe do anulowania i razem jednoznacznie identyfikują każdy wiersz tabeli, nie ma potrzeby oddzielnego klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="0c540-305">Since the foreign keys are not nullable and together uniquely identify each row of the table, there's no need for a separate primary key.</span></span> <span data-ttu-id="0c540-306">*InstructorID* i *CourseID* właściwości powinny działać jako złożony klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="0c540-306">The *InstructorID* and *CourseID* properties should function as a composite primary key.</span></span> <span data-ttu-id="0c540-307">Jedynym sposobem identyfikowania złożonych kluczy podstawowych do EF jest przy użyciu *płynnego interfejsu API* (nie można tego zrobić przy użyciu atrybutów).</span><span class="sxs-lookup"><span data-stu-id="0c540-307">The only way to identify composite primary keys to EF is by using the *fluent API* (it can't be done by using attributes).</span></span> <span data-ttu-id="0c540-308">Zobaczysz, jak skonfigurować złożony klucz podstawowy w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="0c540-308">You'll see how to configure the composite primary key in the next section.</span></span>

<span data-ttu-id="0c540-309">Klucz złożony zapewnia, że chociaż można mieć wiele wierszy dla jednego kursu i wiele wierszy dla jednego instruktora, nie można mieć wiele wierszy dla tego samego instruktora i kursu.</span><span class="sxs-lookup"><span data-stu-id="0c540-309">The composite key ensures that while you can have multiple rows for one course, and multiple rows for one instructor, you can't have multiple rows for the same instructor and course.</span></span> <span data-ttu-id="0c540-310">Jednostka `Enrollment` sprzężenia definiuje własny klucz podstawowy, więc duplikaty tego rodzaju są możliwe.</span><span class="sxs-lookup"><span data-stu-id="0c540-310">The `Enrollment` join entity defines its own primary key, so duplicates of this sort are possible.</span></span> <span data-ttu-id="0c540-311">Aby zapobiec takim duplikatom, można dodać unikatowy indeks `Enrollment` w polach klucza `CourseAssignment`obcego lub skonfigurować przy pomocy podstawowego klucza złożonego podobnego do .</span><span class="sxs-lookup"><span data-stu-id="0c540-311">To prevent such duplicates, you could add a unique index on the foreign key fields, or configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="0c540-312">Aby uzyskać więcej informacji, zobacz [Indeksy](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="0c540-312">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="0c540-313">Aktualizowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="0c540-313">Update the database context</span></span>

<span data-ttu-id="0c540-314">Dodaj następujący wyróżniony kod do pliku *Data/SchoolContext.cs:*</span><span class="sxs-lookup"><span data-stu-id="0c540-314">Add the following highlighted code to the *Data/SchoolContext.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="0c540-315">Ten kod dodaje nowe jednostki i konfiguruje złożony klucz podstawowy jednostki CourseAssignment.</span><span class="sxs-lookup"><span data-stu-id="0c540-315">This code adds the new entities and configures the CourseAssignment entity's composite primary key.</span></span>

## <a name="about-a-fluent-api-alternative"></a><span data-ttu-id="0c540-316">O płynnej alternatywie API</span><span class="sxs-lookup"><span data-stu-id="0c540-316">About a fluent API alternative</span></span>

<span data-ttu-id="0c540-317">Kod w `OnModelCreating` metodzie `DbContext` klasy używa *płynnego interfejsu API* do konfigurowania zachowania EF.</span><span class="sxs-lookup"><span data-stu-id="0c540-317">The code in the `OnModelCreating` method of the `DbContext` class uses the *fluent API* to configure EF behavior.</span></span> <span data-ttu-id="0c540-318">Interfejs API jest nazywany "fluent", ponieważ jest często używany przez ciągowanie serii wywołań metody razem w jednej instrukcji, jak w tym przykładzie z [dokumentacji EF Core:](/ef/core/modeling/#use-fluent-api-to-configure-a-model)</span><span class="sxs-lookup"><span data-stu-id="0c540-318">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement, as in this example from the [EF Core documentation](/ef/core/modeling/#use-fluent-api-to-configure-a-model):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="0c540-319">W tym samouczku używasz płynnego interfejsu API tylko do mapowania bazy danych, którego nie można zrobić z atrybutami.</span><span class="sxs-lookup"><span data-stu-id="0c540-319">In this tutorial, you're using the fluent API only for database mapping that you can't do with attributes.</span></span> <span data-ttu-id="0c540-320">Jednak można również użyć płynnego interfejsu API, aby określić większość reguł formatowania, sprawdzania poprawności i mapowania, które można wykonać przy użyciu atrybutów.</span><span class="sxs-lookup"><span data-stu-id="0c540-320">However, you can also use the fluent API to specify most of the formatting, validation, and mapping rules that you can do by using attributes.</span></span> <span data-ttu-id="0c540-321">Niektóre atrybuty, takie jak `MinimumLength` nie można zastosować za pomocą interfejsu API fluent.</span><span class="sxs-lookup"><span data-stu-id="0c540-321">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="0c540-322">Jak wspomniano wcześniej, `MinimumLength` nie zmienia schematu, stosuje tylko regułę sprawdzania poprawności po stronie klienta i serwera.</span><span class="sxs-lookup"><span data-stu-id="0c540-322">As mentioned previously, `MinimumLength` doesn't change the schema, it only applies a client and server side validation rule.</span></span>

<span data-ttu-id="0c540-323">Niektórzy deweloperzy wolą używać płynnego interfejsu API wyłącznie, aby mogli zachować swoje klasy jednostek "czyste".</span><span class="sxs-lookup"><span data-stu-id="0c540-323">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="0c540-324">Można mieszać atrybuty i płynne interfejsu API, jeśli chcesz, a istnieje kilka dostosowań, które można wykonać tylko przy użyciu płynnego interfejsu API, ale ogólnie zalecana praktyka jest wybrać jedną z tych dwóch metod i używać, że konsekwentnie jak najwięcej.</span><span class="sxs-lookup"><span data-stu-id="0c540-324">You can mix attributes and fluent API if you want, and there are a few customizations that can only be done by using fluent API, but in general the recommended practice is to choose one of these two approaches and use that consistently as much as possible.</span></span> <span data-ttu-id="0c540-325">Jeśli używasz obu, należy pamiętać, że wszędzie tam, gdzie występuje konflikt, Fluent INTERFEJSU API zastępuje atrybuty.</span><span class="sxs-lookup"><span data-stu-id="0c540-325">If you do use both, note that wherever there's a conflict, Fluent API overrides attributes.</span></span>

<span data-ttu-id="0c540-326">Aby uzyskać więcej informacji na temat atrybutów a płynnego interfejsu API, zobacz [Metody konfiguracji](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="0c540-326">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="0c540-327">Diagram encji przedstawiający relacje</span><span class="sxs-lookup"><span data-stu-id="0c540-327">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="0c540-328">Na poniższej ilustracji przedstawiono diagram, który power tools entity framework utworzyć dla ukończonego modelu szkoły.</span><span class="sxs-lookup"><span data-stu-id="0c540-328">The following illustration shows the diagram that the Entity Framework Power Tools create for the completed School model.</span></span>

![Diagram encji](complex-data-model/_static/diagram.png)

<span data-ttu-id="0c540-330">Oprócz wierszy relacji jeden do wielu \*(od 1 do ), można zobaczyć w tym miejscu wiersz relacji jeden do zera lub jeden (od 1 do 0..1) między jednostkami Instructor i OfficeAssignment i wiersz relacji zero-lub-jeden-do-wielu (od 0..1 do \*) między jednostkami Instruktor i Dział.</span><span class="sxs-lookup"><span data-stu-id="0c540-330">Besides the one-to-many relationship lines (1 to \*), you can see here the one-to-zero-or-one relationship line (1 to 0..1) between the Instructor and OfficeAssignment entities and the zero-or-one-to-many relationship line (0..1 to \*) between the Instructor and Department entities.</span></span>

## <a name="seed-database-with-test-data"></a><span data-ttu-id="0c540-331">Baza danych materiału siewnego z danymi testowymi</span><span class="sxs-lookup"><span data-stu-id="0c540-331">Seed database with test data</span></span>

<span data-ttu-id="0c540-332">Zastąp kod w pliku *Data/DbInitializer.cs* następującym kodem, aby zapewnić dane źródłowe dla nowo utworzonych jednostek.</span><span class="sxs-lookup"><span data-stu-id="0c540-332">Replace the code in the *Data/DbInitializer.cs* file with the following code in order to provide seed data for the new entities you've created.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="0c540-333">Jak widać w pierwszym samouczku, większość tego kodu po prostu tworzy nowe obiekty jednostki i ładuje przykładowe dane do właściwości wymaganych do testowania.</span><span class="sxs-lookup"><span data-stu-id="0c540-333">As you saw in the first tutorial, most of this code simply creates new entity objects and loads sample data into properties as required for testing.</span></span> <span data-ttu-id="0c540-334">Zwróć uwagę, jak relacje wiele do wielu są obsługiwane: kod tworzy relacje `Enrollments` przez `CourseAssignment` tworzenie jednostek w zestawach jednostek i sprzężenia.</span><span class="sxs-lookup"><span data-stu-id="0c540-334">Notice how the many-to-many relationships are handled: the code creates relationships by creating entities in the `Enrollments` and `CourseAssignment` join entity sets.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="0c540-335">Dodawanie migracji</span><span class="sxs-lookup"><span data-stu-id="0c540-335">Add a migration</span></span>

<span data-ttu-id="0c540-336">Zapisz swoje zmiany i zbuduj projekt.</span><span class="sxs-lookup"><span data-stu-id="0c540-336">Save your changes and build the project.</span></span> <span data-ttu-id="0c540-337">Następnie otwórz okno polecenia w folderze `migrations add` projektu i wprowadź polecenie (nie rób jeszcze polecenia update-database):</span><span class="sxs-lookup"><span data-stu-id="0c540-337">Then open the command window in the project folder and enter the `migrations add` command (don't do the update-database command yet):</span></span>

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

<span data-ttu-id="0c540-338">Otrzymasz ostrzeżenie o możliwej utracie danych.</span><span class="sxs-lookup"><span data-stu-id="0c540-338">You get a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="0c540-339">Jeśli w tym `database update` momencie próbowano uruchomić polecenie (nie rób tego jeszcze), zostanie wyświetlony następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="0c540-339">If you tried to run the `database update` command at this point (don't do it yet), you would get the following error:</span></span>

> <span data-ttu-id="0c540-340">Instrukcja ALTER TABLE była sprzeczna z ograniczeniem FOREIGN KEY "FK_dbo. Course_dbo. Department_DepartmentID".</span><span class="sxs-lookup"><span data-stu-id="0c540-340">The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID".</span></span> <span data-ttu-id="0c540-341">Konflikt wystąpił w bazie danych "ContosoUniversity", tabeli "dbo. departamentu", kolumna "DepartmentID".</span><span class="sxs-lookup"><span data-stu-id="0c540-341">The conflict occurred in database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.</span></span>

<span data-ttu-id="0c540-342">Czasami podczas wykonywania migracji z istniejącymi danymi, należy wstawić dane skrótowe do bazy danych, aby spełnić ograniczenia klucza obcego.</span><span class="sxs-lookup"><span data-stu-id="0c540-342">Sometimes when you execute migrations with existing data, you need to insert stub data into the database to satisfy foreign key constraints.</span></span> <span data-ttu-id="0c540-343">Wygenerowany kod `Up` w metodzie dodaje klucz obcy Departamentu nienależy do wartości null do tabeli Course.</span><span class="sxs-lookup"><span data-stu-id="0c540-343">The generated code in the `Up` method adds a non-nullable DepartmentID foreign key to the Course table.</span></span> <span data-ttu-id="0c540-344">Jeśli istnieją już wiersze w tabeli Course `AddColumn` po uruchomieniu kodu, operacja kończy się niepowodzeniem, ponieważ SQL Server nie wie, jaką wartość umieścić w kolumnie, która nie może mieć wartości null.</span><span class="sxs-lookup"><span data-stu-id="0c540-344">If there are already rows in the Course table when the code runs, the `AddColumn` operation fails because SQL Server doesn't know what value to put in the column that can't be null.</span></span> <span data-ttu-id="0c540-345">W tym samouczku uruchomisz migrację w nowej bazie danych, ale w aplikacji produkcyjnej musisz wykonać migrację do obsługi istniejących danych, więc poniższe wskazówki pokazują przykład, jak to zrobić.</span><span class="sxs-lookup"><span data-stu-id="0c540-345">For this tutorial you'll run the migration on a new database, but in a production application you'd have to make the migration handle existing data, so the following directions show an example of how to do that.</span></span>

<span data-ttu-id="0c540-346">Aby ta migracja działała z istniejącymi danymi, należy zmienić kod, aby nadać nowej kolumnie wartość domyślną i utworzyć dział skrótowy o nazwie "Temp", aby działał jako dział domyślny.</span><span class="sxs-lookup"><span data-stu-id="0c540-346">To make this migration work with existing data you have to change the code to give the new column a default value, and create a stub department named "Temp" to act as the default department.</span></span> <span data-ttu-id="0c540-347">W rezultacie istniejące wiersze kursu będą powiązane z działem `Up` "Temp" po uruchomieniu metody.</span><span class="sxs-lookup"><span data-stu-id="0c540-347">As a result, existing Course rows will all be related to the "Temp" department after the `Up` method runs.</span></span>

* <span data-ttu-id="0c540-348">Otwórz plik *{timestamp}_ComplexDataModel.cs.*</span><span class="sxs-lookup"><span data-stu-id="0c540-348">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>

* <span data-ttu-id="0c540-349">Skomentuj wiersz kodu, który dodaje kolumnę DepartmentID do tabeli Course.</span><span class="sxs-lookup"><span data-stu-id="0c540-349">Comment out the line of code that adds the DepartmentID column to the Course table.</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* <span data-ttu-id="0c540-350">Dodaj następujący wyróżniony kod po kodzie, który tworzy tabelę Dział:</span><span class="sxs-lookup"><span data-stu-id="0c540-350">Add the following highlighted code after the code that creates the Department table:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="0c540-351">W aplikacji produkcyjnej należy napisać kod lub skrypty, aby dodać wiersze działu i powiązać wiersze kursu do nowych wierszy działu.</span><span class="sxs-lookup"><span data-stu-id="0c540-351">In a production application, you would write code or scripts to add Department rows and relate Course rows to the new Department rows.</span></span> <span data-ttu-id="0c540-352">Wtedy nie będzie już potrzebny dział "Temp" ani wartość domyślna w kolumnie Course.DepartmentID.</span><span class="sxs-lookup"><span data-stu-id="0c540-352">You would then no longer need the "Temp" department or the default value on the Course.DepartmentID column.</span></span>

<span data-ttu-id="0c540-353">Zapisz swoje zmiany i zbuduj projekt.</span><span class="sxs-lookup"><span data-stu-id="0c540-353">Save your changes and build the project.</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="0c540-354">Zmienianie ciągu połączenia</span><span class="sxs-lookup"><span data-stu-id="0c540-354">Change the connection string</span></span>

<span data-ttu-id="0c540-355">Masz teraz nowy kod `DbInitializer` w klasie, który dodaje dane źródłowe dla nowych jednostek do pustej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="0c540-355">You now have new code in the `DbInitializer` class that adds seed data for the new entities to an empty database.</span></span> <span data-ttu-id="0c540-356">Aby ef utworzyć nową pustą bazę danych, należy zmienić nazwę bazy danych w ciągu połączenia w *appsettings.json* contosoUniversity3 lub innej nazwy, która nie została użyta na komputerze, którego używasz.</span><span class="sxs-lookup"><span data-stu-id="0c540-356">To make EF create a new empty database, change the name of the database in the connection string in *appsettings.json* to ContosoUniversity3 or some other name that you haven't used on the computer you're using.</span></span>

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

<span data-ttu-id="0c540-357">Zapisz zmiany do *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="0c540-357">Save your change to *appsettings.json*.</span></span>

> [!NOTE]
> <span data-ttu-id="0c540-358">Jako alternatywę dla zmiany nazwy bazy danych można usunąć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="0c540-358">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="0c540-359">Użyj **programu SQL Server Object Explorer** `database drop` (SSOX) lub polecenia CLI:</span><span class="sxs-lookup"><span data-stu-id="0c540-359">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```dotnetcli
> dotnet ef database drop
> ```

## <a name="update-the-database"></a><span data-ttu-id="0c540-360">Aktualizowanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="0c540-360">Update the database</span></span>

<span data-ttu-id="0c540-361">Po zmianie nazwy bazy danych lub usunięciu `database update` bazy danych uruchom polecenie w oknie polecenia, aby wykonać migracje.</span><span class="sxs-lookup"><span data-stu-id="0c540-361">After you have changed the database name or deleted the database, run the `database update` command in the command window to execute the migrations.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="0c540-362">Uruchom aplikację, aby `DbInitializer.Initialize` spowodować, że metoda do uruchomienia i wypełnić nową bazę danych.</span><span class="sxs-lookup"><span data-stu-id="0c540-362">Run the app to cause the `DbInitializer.Initialize` method to run and populate the new database.</span></span>

<span data-ttu-id="0c540-363">Otwórz bazę danych w SSOX, tak jak wcześniej, i rozwiń węzeł **Tabele,** aby zobaczyć, że wszystkie tabele zostały utworzone.</span><span class="sxs-lookup"><span data-stu-id="0c540-363">Open the database in SSOX as you did earlier, and expand the **Tables** node to see that all of the tables have been created.</span></span> <span data-ttu-id="0c540-364">(Jeśli nadal masz otwarty SSOX od wcześniejszego czasu, kliknij przycisk **Odśwież).**</span><span class="sxs-lookup"><span data-stu-id="0c540-364">(If you still have SSOX open from the earlier time, click the **Refresh** button.)</span></span>

![Tabele w SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="0c540-366">Uruchom aplikację, aby wyzwolić kod inicjatora, który nasiona bazy danych.</span><span class="sxs-lookup"><span data-stu-id="0c540-366">Run the app to trigger the initializer code that seeds the database.</span></span>

<span data-ttu-id="0c540-367">Kliknij prawym przyciskiem myszy **tabelę CourseAssignment** i wybierz polecenie **Wyświetl dane,** aby sprawdzić, czy zawiera ona dane.</span><span class="sxs-lookup"><span data-stu-id="0c540-367">Right-click the **CourseAssignment** table and select **View Data** to verify that it has data in it.</span></span>

![Dane dotyczące kursusysignment w SSOX](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a><span data-ttu-id="0c540-369">Uzyskiwanie kodu</span><span class="sxs-lookup"><span data-stu-id="0c540-369">Get the code</span></span>

[<span data-ttu-id="0c540-370">Pobierz lub wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="0c540-370">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="0c540-371">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="0c540-371">Next steps</span></span>

<span data-ttu-id="0c540-372">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="0c540-372">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0c540-373">Dostosowano model danych</span><span class="sxs-lookup"><span data-stu-id="0c540-373">Customized the Data model</span></span>
> * <span data-ttu-id="0c540-374">Wprowadzone zmiany w encji Student</span><span class="sxs-lookup"><span data-stu-id="0c540-374">Made changes to Student entity</span></span>
> * <span data-ttu-id="0c540-375">Utworzono jednostkę Instructor</span><span class="sxs-lookup"><span data-stu-id="0c540-375">Created Instructor entity</span></span>
> * <span data-ttu-id="0c540-376">Utworzono encję OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="0c540-376">Created OfficeAssignment entity</span></span>
> * <span data-ttu-id="0c540-377">Zmodyfikowana jednostka kursu</span><span class="sxs-lookup"><span data-stu-id="0c540-377">Modified Course entity</span></span>
> * <span data-ttu-id="0c540-378">Utworzono encję Dział</span><span class="sxs-lookup"><span data-stu-id="0c540-378">Created Department entity</span></span>
> * <span data-ttu-id="0c540-379">Zmodyfikowana jednostka rejestracji</span><span class="sxs-lookup"><span data-stu-id="0c540-379">Modified Enrollment entity</span></span>
> * <span data-ttu-id="0c540-380">Zaktualizowano kontekst bazy danych</span><span class="sxs-lookup"><span data-stu-id="0c540-380">Updated the database context</span></span>
> * <span data-ttu-id="0c540-381">Rozstawiona baza danych z danymi testowymi</span><span class="sxs-lookup"><span data-stu-id="0c540-381">Seeded database with test data</span></span>
> * <span data-ttu-id="0c540-382">Dodano migrację</span><span class="sxs-lookup"><span data-stu-id="0c540-382">Added a migration</span></span>
> * <span data-ttu-id="0c540-383">Zmieniono parametry połączenia</span><span class="sxs-lookup"><span data-stu-id="0c540-383">Changed the connection string</span></span>
> * <span data-ttu-id="0c540-384">Zaktualizowano bazę danych</span><span class="sxs-lookup"><span data-stu-id="0c540-384">Updated the database</span></span>

<span data-ttu-id="0c540-385">Przejdź do następnego samouczka, aby dowiedzieć się więcej o tym, jak uzyskać dostęp do powiązanych danych.</span><span class="sxs-lookup"><span data-stu-id="0c540-385">Advance to the next tutorial to learn more about how to access related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="0c540-386">Dalej: Dostęp do powiązanych danych</span><span class="sxs-lookup"><span data-stu-id="0c540-386">Next: Access related data</span></span>](read-related-data.md)
