---
title: 'Samouczek: Tworzenie złożonego modelu danych — ASP.NET MVC z EF Core'
description: W tym samouczku Dodaj więcej jednostek i relacje i Dostosuj model danych, określając formatowanie, walidację i reguły mapowania.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: cee9e9eb4c5435f3f63f7d1d04f131d88effe9f6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054479"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a><span data-ttu-id="96785-103">Samouczek: Tworzenie złożonego modelu danych — ASP.NET MVC z EF Core</span><span class="sxs-lookup"><span data-stu-id="96785-103">Tutorial: Create a complex data model - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="96785-104">W poprzednich samouczkach pracujesz z prostym modelem danych, który składa się z trzech jednostek.</span><span class="sxs-lookup"><span data-stu-id="96785-104">In the previous tutorials, you worked with a simple data model that was composed of three entities.</span></span> <span data-ttu-id="96785-105">W tym samouczku dodasz więcej jednostek i relacji, a następnie utworzysz model danych, określając formatowanie, walidację i reguły mapowania bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96785-105">In this tutorial, you'll add more entities and relationships and you'll customize the data model by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="96785-106">Po zakończeniu klasy jednostek składają się na ukończony model danych przedstawiony na poniższej ilustracji:</span><span class="sxs-lookup"><span data-stu-id="96785-106">When you're finished, the entity classes will make up the completed data model that's shown in the following illustration:</span></span>

![Diagram jednostek](complex-data-model/_static/diagram.png)

<span data-ttu-id="96785-108">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="96785-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="96785-109">Dostosowywanie modelu danych</span><span class="sxs-lookup"><span data-stu-id="96785-109">Customize the Data model</span></span>
> * <span data-ttu-id="96785-110">Wprowadzanie zmian w jednostce ucznia</span><span class="sxs-lookup"><span data-stu-id="96785-110">Make changes to Student entity</span></span>
> * <span data-ttu-id="96785-111">Utwórz jednostkę instruktora</span><span class="sxs-lookup"><span data-stu-id="96785-111">Create Instructor entity</span></span>
> * <span data-ttu-id="96785-112">Tworzenie jednostki OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="96785-112">Create OfficeAssignment entity</span></span>
> * <span data-ttu-id="96785-113">Modyfikuj jednostkę kursu</span><span class="sxs-lookup"><span data-stu-id="96785-113">Modify Course entity</span></span>
> * <span data-ttu-id="96785-114">Utwórz jednostkę działu</span><span class="sxs-lookup"><span data-stu-id="96785-114">Create Department entity</span></span>
> * <span data-ttu-id="96785-115">Modyfikuj jednostkę rejestracji</span><span class="sxs-lookup"><span data-stu-id="96785-115">Modify Enrollment entity</span></span>
> * <span data-ttu-id="96785-116">Aktualizowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="96785-116">Update the database context</span></span>
> * <span data-ttu-id="96785-117">Wypełnianie bazy danych z danymi testowymi</span><span class="sxs-lookup"><span data-stu-id="96785-117">Seed database with test data</span></span>
> * <span data-ttu-id="96785-118">Dodawanie migracji</span><span class="sxs-lookup"><span data-stu-id="96785-118">Add a migration</span></span>
> * <span data-ttu-id="96785-119">Zmień parametry połączenia</span><span class="sxs-lookup"><span data-stu-id="96785-119">Change the connection string</span></span>
> * <span data-ttu-id="96785-120">Aktualizowanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="96785-120">Update the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="96785-121">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="96785-121">Prerequisites</span></span>

* [<span data-ttu-id="96785-122">Korzystanie z EF Core migracji</span><span class="sxs-lookup"><span data-stu-id="96785-122">Using EF Core migrations</span></span>](migrations.md)

## <a name="customize-the-data-model"></a><span data-ttu-id="96785-123">Dostosowywanie modelu danych</span><span class="sxs-lookup"><span data-stu-id="96785-123">Customize the Data model</span></span>

<span data-ttu-id="96785-124">W tej sekcji dowiesz się, jak dostosować model danych przy użyciu atrybutów, które określają formatowanie, walidację i reguły mapowania bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96785-124">In this section you'll see how to customize the data model by using attributes that specify formatting, validation, and database mapping rules.</span></span> <span data-ttu-id="96785-125">Następnie w kilku poniższych sekcjach utworzysz kompletny model danych szkolnych przez dodanie atrybutów do klas, które zostały już utworzone, i tworzenie nowych klas dla pozostałych typów jednostek w modelu.</span><span class="sxs-lookup"><span data-stu-id="96785-125">Then in several of the following sections you'll create the complete School data model by adding attributes to the classes you already created and creating new classes for the remaining entity types in the model.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="96785-126">Atrybut DataType</span><span class="sxs-lookup"><span data-stu-id="96785-126">The DataType attribute</span></span>

<span data-ttu-id="96785-127">W przypadku dat rejestracji uczniów na wszystkich stronach sieci Web jest obecnie wyświetlany czas wraz z datą, chociaż wszystko, co jest ważne dla tego pola, to Data.</span><span class="sxs-lookup"><span data-stu-id="96785-127">For student enrollment dates, all of the web pages currently display the time along with the date, although all you care about for this field is the date.</span></span> <span data-ttu-id="96785-128">Używając atrybutów adnotacji danych, można wprowadzić jedną zmianę kodu, która naprawi format wyświetlania w każdym widoku, który wyświetla dane.</span><span class="sxs-lookup"><span data-stu-id="96785-128">By using data annotation attributes, you can make one code change that will fix the display format in every view that shows the data.</span></span> <span data-ttu-id="96785-129">Aby zapoznać się z przykładem, jak to zrobić, Dodaj atrybut do `EnrollmentDate` właściwości w `Student` klasie.</span><span class="sxs-lookup"><span data-stu-id="96785-129">To see an example of how to do that, you'll add an attribute to the `EnrollmentDate` property in the `Student` class.</span></span>

<span data-ttu-id="96785-130">W *modelach/student. cs* Dodaj `using` instrukcję do `System.ComponentModel.DataAnnotations` przestrzeni nazw i Dodaj `DataType` `DisplayFormat` atrybuty i do `EnrollmentDate` właściwości, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="96785-130">In *Models/Student.cs* , add a `using` statement for the `System.ComponentModel.DataAnnotations` namespace and add `DataType` and `DisplayFormat` attributes to the `EnrollmentDate` property, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="96785-131">Ten `DataType` atrybut służy do określania typu danych, który jest bardziej szczegółowy niż typ wewnętrzny bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96785-131">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="96785-132">W tym przypadku chcemy tylko śledzić datę, a nie datę i godzinę.</span><span class="sxs-lookup"><span data-stu-id="96785-132">In this case we only want to keep track of the date, not the date and time.</span></span> <span data-ttu-id="96785-133">`DataType`Wyliczenie zawiera wiele typów danych, takich jak data, godzina, numer telefonu, waluta, EmailAddress i inne.</span><span class="sxs-lookup"><span data-stu-id="96785-133">The  `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="96785-134">Ten `DataType` atrybut może również umożliwić aplikacji automatyczne udostępnianie funkcji specyficznych dla typu.</span><span class="sxs-lookup"><span data-stu-id="96785-134">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="96785-135">Na przykład `mailto:` można utworzyć łącze dla i dla programu `DataType.EmailAddress` `DataType.Date` w przeglądarkach, które obsługują HTML5, można podać selektor daty.</span><span class="sxs-lookup"><span data-stu-id="96785-135">For example, a `mailto:` link can be created for `DataType.EmailAddress`, and a date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="96785-136">Ten `DataType` atrybut emituje kod HTML 5 `data-` (wymawiane kreski danych), które przeglądarki HTML 5 mogą zrozumieć.</span><span class="sxs-lookup"><span data-stu-id="96785-136">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers can understand.</span></span> <span data-ttu-id="96785-137">`DataType`Atrybuty nie zapewniają żadnych weryfikacji.</span><span class="sxs-lookup"><span data-stu-id="96785-137">The `DataType` attributes don't provide any validation.</span></span>

<span data-ttu-id="96785-138">`DataType.Date` nie określa formatu wyświetlanej daty.</span><span class="sxs-lookup"><span data-stu-id="96785-138">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="96785-139">Domyślnie pole dane jest wyświetlane zgodnie z domyślnymi formatami opartymi na CultureInfo serwera.</span><span class="sxs-lookup"><span data-stu-id="96785-139">By default, the data field is displayed according to the default formats based on the server's CultureInfo.</span></span>

<span data-ttu-id="96785-140">Ten `DisplayFormat` atrybut służy do jawnego określenia formatu daty:</span><span class="sxs-lookup"><span data-stu-id="96785-140">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="96785-141">`ApplyFormatInEditMode`Ustawienie określa, że formatowanie powinno być również stosowane, gdy wartość jest wyświetlana w polu tekstowym do edycji.</span><span class="sxs-lookup"><span data-stu-id="96785-141">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied when the value is displayed in a text box for editing.</span></span> <span data-ttu-id="96785-142">(Możesz nie chcieć, aby dla niektórych pól — na przykład w przypadku wartości walutowych, można nie chcieć, aby symbol waluty w polu tekstowym do edycji.)</span><span class="sxs-lookup"><span data-stu-id="96785-142">(You might not want that for some fields -- for example, for currency values, you might not want the currency symbol in the text box for editing.)</span></span>

<span data-ttu-id="96785-143">Możesz użyć `DisplayFormat` atrybutu przez siebie, ale zazwyczaj dobrym pomysłem jest użycie `DataType` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="96785-143">You can use the `DisplayFormat` attribute by itself, but it's generally a good idea to use the `DataType` attribute also.</span></span> <span data-ttu-id="96785-144">Ten `DataType` atrybut przekazuje semantykę danych w przeciwieństwie do sposobu renderowania na ekranie i zapewnia następujące korzyści, których nie można uzyskać `DisplayFormat` :</span><span class="sxs-lookup"><span data-stu-id="96785-144">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with `DisplayFormat`:</span></span>

* <span data-ttu-id="96785-145">Przeglądarka może włączać funkcje HTML5 (na przykład w celu wyświetlenia kontrolki Calendar, symbolu waluty właściwej dla ustawień regionalnych, linków poczty e-mail, niektórych weryfikacji danych wejściowych po stronie klienta itp.).</span><span class="sxs-lookup"><span data-stu-id="96785-145">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, some client-side input validation, etc.).</span></span>

* <span data-ttu-id="96785-146">Domyślnie przeglądarka będzie renderować dane przy użyciu poprawnego formatu na podstawie ustawień regionalnych.</span><span class="sxs-lookup"><span data-stu-id="96785-146">By default, the browser will render data using the correct format based on your locale.</span></span>

<span data-ttu-id="96785-147">Aby uzyskać więcej informacji, zobacz [ \<input> dokumentację pomocnika tagów](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="96785-147">For more information, see the [\<input> tag helper documentation](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span></span>

<span data-ttu-id="96785-148">Uruchom aplikację, przejdź do strony indeks uczniów i zwróć uwagę na to, że czasy nie są już wyświetlane dla dat rejestracji.</span><span class="sxs-lookup"><span data-stu-id="96785-148">Run the app, go to the Students Index page and notice that times are no longer displayed for the enrollment dates.</span></span> <span data-ttu-id="96785-149">Ta sama wartość będzie dotyczyć dowolnego widoku, który korzysta z modelu ucznia.</span><span class="sxs-lookup"><span data-stu-id="96785-149">The same will be true for any view that uses the Student model.</span></span>

![Strona indeksu uczniów pokazująca daty bez czasu](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="96785-151">Atrybut StringLength</span><span class="sxs-lookup"><span data-stu-id="96785-151">The StringLength attribute</span></span>

<span data-ttu-id="96785-152">Można również określić reguły walidacji danych i komunikaty o błędach walidacji przy użyciu atrybutów.</span><span class="sxs-lookup"><span data-stu-id="96785-152">You can also specify data validation rules and validation error messages using attributes.</span></span> <span data-ttu-id="96785-153">`StringLength`Atrybut ustawia maksymalną długość w bazie danych i zapewnia walidację po stronie klienta i serwera dla ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="96785-153">The `StringLength` attribute sets the maximum length  in the database and provides client side and server side validation for ASP.NET Core MVC.</span></span> <span data-ttu-id="96785-154">Możesz również określić minimalną długość ciągu w tym atrybucie, ale wartość minimalna nie ma wpływu na schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96785-154">You can also specify the minimum string length in this attribute, but the minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="96785-155">Załóżmy, że chcesz upewnić się, że użytkownicy nie wprowadzają więcej niż 50 znaków.</span><span class="sxs-lookup"><span data-stu-id="96785-155">Suppose you want to ensure that users don't enter more than 50 characters for a name.</span></span> <span data-ttu-id="96785-156">Aby dodać to ograniczenie, należy dodać `StringLength` atrybuty do `LastName` `FirstMidName` właściwości i, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="96785-156">To add this limitation, add `StringLength` attributes to the `LastName` and `FirstMidName` properties, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="96785-157">Ten `StringLength` atrybut nie uniemożliwi użytkownikowi wprowadzania białych znaków w nazwie.</span><span class="sxs-lookup"><span data-stu-id="96785-157">The `StringLength` attribute won't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="96785-158">Możesz użyć atrybutu, `RegularExpression` Aby zastosować ograniczenia do danych wejściowych.</span><span class="sxs-lookup"><span data-stu-id="96785-158">You can use the `RegularExpression` attribute to apply restrictions to the input.</span></span> <span data-ttu-id="96785-159">Na przykład poniższy kod wymaga, aby pierwszy znak był wielką literą, a pozostałe znaki są alfabetyczne:</span><span class="sxs-lookup"><span data-stu-id="96785-159">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

<span data-ttu-id="96785-160">Ten `MaxLength` atrybut zawiera funkcje podobne do `StringLength` atrybutu, ale nie zapewnia weryfikacji po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="96785-160">The `MaxLength` attribute provides functionality similar to the `StringLength` attribute but doesn't provide client side validation.</span></span>

<span data-ttu-id="96785-161">Model bazy danych został zmieniony w sposób, który wymaga zmiany w schemacie bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96785-161">The database model has now changed in a way that requires a change in the database schema.</span></span> <span data-ttu-id="96785-162">Migracje zostaną użyte do zaktualizowania schematu bez utraty danych, które mogły zostać dodane do bazy danych za pomocą interfejsu użytkownika aplikacji.</span><span class="sxs-lookup"><span data-stu-id="96785-162">You'll use migrations to update the schema without losing any data that you may have added to the database by using the application UI.</span></span>

<span data-ttu-id="96785-163">Zapisz zmiany i skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="96785-163">Save your changes and build the project.</span></span> <span data-ttu-id="96785-164">Następnie otwórz okno polecenia w folderze projektu i wprowadź następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="96785-164">Then open the command window in the project folder and enter the following commands:</span></span>

```dotnetcli
dotnet ef migrations add MaxLengthOnNames
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="96785-165">`migrations add`Polecenie ostrzega o utracie danych, ponieważ zmiana powoduje krótszą długość dla dwóch kolumn.</span><span class="sxs-lookup"><span data-stu-id="96785-165">The `migrations add` command warns that data loss may occur, because the change makes the maximum length shorter for two columns.</span></span>  <span data-ttu-id="96785-166">Migracja tworzy plik o nazwie *\<timeStamp> _MaxLengthOnNames. cs* .</span><span class="sxs-lookup"><span data-stu-id="96785-166">Migrations creates a file named *\<timeStamp>_MaxLengthOnNames.cs* .</span></span> <span data-ttu-id="96785-167">Ten plik zawiera kod w `Up` metodzie, która zaktualizuje bazę danych w taki sposób, aby była zgodna z bieżącym modelem danych.</span><span class="sxs-lookup"><span data-stu-id="96785-167">This file contains code in the `Up` method that will update the database to match the current data model.</span></span> <span data-ttu-id="96785-168">`database update`Polecenie uruchomiło ten kod.</span><span class="sxs-lookup"><span data-stu-id="96785-168">The `database update` command ran that code.</span></span>

<span data-ttu-id="96785-169">Sygnatura czasowa poprzedzona nazwą pliku migracji jest używana przez Entity Framework do porządkowania migracji.</span><span class="sxs-lookup"><span data-stu-id="96785-169">The timestamp prefixed to the migrations file name is used by Entity Framework to order the migrations.</span></span> <span data-ttu-id="96785-170">Można utworzyć wiele migracji przed uruchomieniem polecenia Update-Database, a następnie wszystkie migracje zostaną zastosowane w kolejności, w której zostały utworzone.</span><span class="sxs-lookup"><span data-stu-id="96785-170">You can create multiple migrations before running the update-database command, and then all of the migrations are applied in the order in which they were created.</span></span>

<span data-ttu-id="96785-171">Uruchom aplikację, wybierz kartę **uczniowie** , kliknij pozycję **Utwórz nową** , a następnie spróbuj wprowadzić nazwy dłuższe niż 50 znaków.</span><span class="sxs-lookup"><span data-stu-id="96785-171">Run the app, select the **Students** tab, click **Create New** , and try to enter either name longer than 50 characters.</span></span> <span data-ttu-id="96785-172">Aplikacja powinna uniemożliwiać wykonanie tej czynności.</span><span class="sxs-lookup"><span data-stu-id="96785-172">The application should prevent you from doing this.</span></span> 

### <a name="the-column-attribute"></a><span data-ttu-id="96785-173">Atrybut Column</span><span class="sxs-lookup"><span data-stu-id="96785-173">The Column attribute</span></span>

<span data-ttu-id="96785-174">Można również użyć atrybutów do kontrolowania sposobu, w jaki klasy i właściwości są mapowane do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96785-174">You can also use attributes to control how your classes and properties are mapped to the database.</span></span> <span data-ttu-id="96785-175">Załóżmy, że użyto nazwy `FirstMidName` dla pola pierwszej nazwy, ponieważ pole może zawierać również nazwę środkową.</span><span class="sxs-lookup"><span data-stu-id="96785-175">Suppose you had used the name `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span> <span data-ttu-id="96785-176">Ale chcesz, aby kolumna bazy danych była nazywana `FirstName` , ponieważ użytkownicy, którzy będą zapisywać zapytania ad hoc względem bazy danych, są przyzwyczajoni do tej nazwy.</span><span class="sxs-lookup"><span data-stu-id="96785-176">But you want the database column to be named `FirstName`, because users who will be writing ad-hoc queries against the database are accustomed to that name.</span></span> <span data-ttu-id="96785-177">Aby to mapowanie było możliwe, można użyć `Column` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="96785-177">To make this mapping, you can use the `Column` attribute.</span></span>

<span data-ttu-id="96785-178">Ten `Column` atrybut określa, że podczas tworzenia bazy danych kolumna tabeli, która jest `Student` mapowana na `FirstMidName` Właściwość, będzie nosiła nazwę `FirstName` .</span><span class="sxs-lookup"><span data-stu-id="96785-178">The `Column` attribute specifies that when the database is created, the column of the `Student` table that maps to the `FirstMidName` property will be named `FirstName`.</span></span> <span data-ttu-id="96785-179">Innymi słowy, gdy Twój kod odwołuje się do `Student.FirstMidName` , dane będą pochodzić z lub zostaną zaktualizowane w `FirstName` kolumnie `Student` tabeli.</span><span class="sxs-lookup"><span data-stu-id="96785-179">In other words, when your code refers to `Student.FirstMidName`, the data will come from or be updated in the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="96785-180">Jeśli nie określisz nazw kolumn, otrzymają one taką samą nazwę jak nazwa właściwości.</span><span class="sxs-lookup"><span data-stu-id="96785-180">If you don't specify column names, they're given the same name as the property name.</span></span>

<span data-ttu-id="96785-181">W pliku *student.cs* Dodaj `using` instrukcję do `System.ComponentModel.DataAnnotations.Schema` i Dodaj atrybut nazwy kolumny do `FirstMidName` właściwości, jak pokazano w następującym wyróżnionym kodzie:</span><span class="sxs-lookup"><span data-stu-id="96785-181">In the *Student.cs* file, add a `using` statement for `System.ComponentModel.DataAnnotations.Schema` and add the column name attribute to the `FirstMidName` property, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="96785-182">Dodanie `Column` atrybutu zmienia model przy użyciu kopii zapasowej `SchoolContext` , dlatego nie jest on zgodny z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="96785-182">The addition of the `Column` attribute changes the model backing the `SchoolContext`, so it won't match the database.</span></span>

<span data-ttu-id="96785-183">Zapisz zmiany i skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="96785-183">Save your changes and build the project.</span></span> <span data-ttu-id="96785-184">Następnie otwórz okno polecenia w folderze projektu i wprowadź następujące polecenia, aby utworzyć kolejną migrację:</span><span class="sxs-lookup"><span data-stu-id="96785-184">Then open the command window in the project folder and enter the following commands to create another migration:</span></span>

```dotnetcli
dotnet ef migrations add ColumnFirstName
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="96785-185">W **Eksplorator obiektów SQL Server** Otwórz projektanta tabeli uczniów, klikając dwukrotnie tabelę **uczniów** .</span><span class="sxs-lookup"><span data-stu-id="96785-185">In **SQL Server Object Explorer** , open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabela studentów w SSOX po migracji](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="96785-187">Przed zastosowaniem pierwszych dwóch migracji, kolumny nazw były typu nvarchar (MAX).</span><span class="sxs-lookup"><span data-stu-id="96785-187">Before you applied the first two migrations, the name columns were of type nvarchar(MAX).</span></span> <span data-ttu-id="96785-188">Są one teraz nvarchar (50), a nazwa kolumny została zmieniona z FirstMidName na FirstName.</span><span class="sxs-lookup"><span data-stu-id="96785-188">They're now nvarchar(50) and the column name has changed from FirstMidName to FirstName.</span></span>

> [!Note]
> <span data-ttu-id="96785-189">Jeśli spróbujesz skompilować przed ukończeniem tworzenia wszystkich klas jednostek w poniższych sekcjach, mogą wystąpić błędy kompilatora.</span><span class="sxs-lookup"><span data-stu-id="96785-189">If you try to compile before you finish creating all of the entity classes in the following sections, you might get compiler errors.</span></span>

## <a name="changes-to-student-entity"></a><span data-ttu-id="96785-190">Zmiany w jednostce ucznia</span><span class="sxs-lookup"><span data-stu-id="96785-190">Changes to Student entity</span></span>

![Jednostka ucznia](complex-data-model/_static/student-entity.png)

<span data-ttu-id="96785-192">W *modelach/student. cs* Zastąp kod, który został dodany wcześniej do poniższego kodu.</span><span class="sxs-lookup"><span data-stu-id="96785-192">In *Models/Student.cs* , replace the code you added earlier with the following code.</span></span> <span data-ttu-id="96785-193">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="96785-193">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="96785-194">Wymagany atrybut</span><span class="sxs-lookup"><span data-stu-id="96785-194">The Required attribute</span></span>

<span data-ttu-id="96785-195">`Required`Atrybut powoduje, że właściwości nazwy są wymagane.</span><span class="sxs-lookup"><span data-stu-id="96785-195">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="96785-196">`Required`Atrybut nie jest wymagany dla typów niedopuszczających wartości null, takich jak typy wartości (DateTime, int, Double, float itp.).</span><span class="sxs-lookup"><span data-stu-id="96785-196">The `Required` attribute isn't needed for non-nullable types such as value types (DateTime, int, double, float, etc.).</span></span> <span data-ttu-id="96785-197">Typy, które nie mogą mieć wartości null, są automatycznie traktowane jako pola wymagane.</span><span class="sxs-lookup"><span data-stu-id="96785-197">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="96785-198">`Required`Atrybut musi być używany z `MinimumLength` , `MinimumLength` Aby można było wymusić.</span><span class="sxs-lookup"><span data-stu-id="96785-198">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="96785-199">Atrybut wyświetlania</span><span class="sxs-lookup"><span data-stu-id="96785-199">The Display attribute</span></span>

<span data-ttu-id="96785-200">Ten `Display` atrybut określa, że podpis pól tekstowych powinien mieć wartość "First Name", "nazwisko", "Full Name" i "Data rejestracji" zamiast nazwy właściwości w każdym wystąpieniu (które nie zawiera spacji dzielących wyrazy).</span><span class="sxs-lookup"><span data-stu-id="96785-200">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date" instead of the property name in each instance (which has no space dividing the words).</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="96785-201">Właściwość obliczeniowa FullName</span><span class="sxs-lookup"><span data-stu-id="96785-201">The FullName calculated property</span></span>

<span data-ttu-id="96785-202">`FullName` jest właściwością obliczaną, która zwraca wartość utworzoną przez połączenie dwóch innych właściwości.</span><span class="sxs-lookup"><span data-stu-id="96785-202">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="96785-203">W związku z tym ma tylko metodę dostępu get i żadna `FullName` kolumna nie zostanie wygenerowana w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="96785-203">Therefore it has only a get accessor, and no `FullName` column will be generated in the database.</span></span>

## <a name="create-instructor-entity"></a><span data-ttu-id="96785-204">Utwórz jednostkę instruktora</span><span class="sxs-lookup"><span data-stu-id="96785-204">Create Instructor entity</span></span>

![Jednostka instruktora](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="96785-206">Utwórz *modele/instruktor. cs* , zastępując kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="96785-206">Create *Models/Instructor.cs* , replacing the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

<span data-ttu-id="96785-207">Zwróć uwagę, że kilka właściwości jest taka sama w jednostkach studenta i instruktora.</span><span class="sxs-lookup"><span data-stu-id="96785-207">Notice that several properties are the same in the Student and Instructor entities.</span></span> <span data-ttu-id="96785-208">W samouczku [implementującym dziedziczenie](inheritance.md) w dalszej części tej serii powrócisz ten kod, aby wyeliminować nadmiarowość.</span><span class="sxs-lookup"><span data-stu-id="96785-208">In the [Implementing Inheritance](inheritance.md) tutorial later in this series, you'll refactor this code to eliminate the redundancy.</span></span>

<span data-ttu-id="96785-209">Można umieścić wiele atrybutów w jednym wierszu, aby można było również napisać atrybuty w `HireDate` następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="96785-209">You can put multiple attributes on one line, so you could also write the `HireDate` attributes as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="96785-210">Właściwości nawigacji CourseAssignments i OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="96785-210">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="96785-211">`CourseAssignments`Właściwości i `OfficeAssignment` są właściwościami nawigacji.</span><span class="sxs-lookup"><span data-stu-id="96785-211">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="96785-212">Instruktor może nauczyć dowolną liczbę kursów, więc `CourseAssignments` jest zdefiniowany jako kolekcja.</span><span class="sxs-lookup"><span data-stu-id="96785-212">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="96785-213">Jeśli właściwość nawigacji może zawierać wiele jednostek, jej typem musi być lista, w której można dodawać, usuwać i aktualizować wpisy.</span><span class="sxs-lookup"><span data-stu-id="96785-213">If a navigation property can hold multiple entities, its type must be a list in which entries can be added, deleted, and updated.</span></span>  <span data-ttu-id="96785-214">Możesz określić `ICollection<T>` lub typ, taki jak `List<T>` lub `HashSet<T>` .</span><span class="sxs-lookup"><span data-stu-id="96785-214">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="96785-215">Jeśli określisz `ICollection<T>` , EF domyślnie tworzy `HashSet<T>` kolekcję.</span><span class="sxs-lookup"><span data-stu-id="96785-215">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="96785-216">Przyczyny tego problemu są `CourseAssignment` opisane poniżej w sekcji dotyczącej relacji wiele-do-wielu.</span><span class="sxs-lookup"><span data-stu-id="96785-216">The reason why these are `CourseAssignment` entities is explained below in the section about many-to-many relationships.</span></span>

<span data-ttu-id="96785-217">Firma Contoso University State rules, że instruktor może mieć tylko co najwyżej jeden urząd, więc `OfficeAssignment` Właściwość zawiera jedną jednostkę OfficeAssignment (która może mieć wartość null, jeśli nie przypisano pakietu Office).</span><span class="sxs-lookup"><span data-stu-id="96785-217">Contoso University business rules state that an instructor can only have at most one office, so the `OfficeAssignment` property holds a single OfficeAssignment entity (which may be null if no office is assigned).</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a><span data-ttu-id="96785-218">Tworzenie jednostki OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="96785-218">Create OfficeAssignment entity</span></span>

![Jednostka OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="96785-220">Utwórz *modele/OfficeAssignment. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="96785-220">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="96785-221">Atrybut klucza</span><span class="sxs-lookup"><span data-stu-id="96785-221">The Key attribute</span></span>

<span data-ttu-id="96785-222">Istnieje relacja jeden do zera między instruktorem a jednostkami OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="96785-222">There's a one-to-zero-or-one relationship  between the Instructor and the OfficeAssignment entities.</span></span> <span data-ttu-id="96785-223">Przypisanie pakietu Office istnieje tylko w odniesieniu do instruktora, do którego jest przypisany, i w związku z tym jego klucz podstawowy jest również jego kluczem obcym dla jednostki instruktora.</span><span class="sxs-lookup"><span data-stu-id="96785-223">An office assignment only exists in relation to the instructor it's assigned to, and therefore its primary key is also its foreign key to the Instructor entity.</span></span> <span data-ttu-id="96785-224">Jednak Entity Framework nie może automatycznie rozpoznać InstructorID jako klucza podstawowego tej jednostki, ponieważ jego nazwa nie jest zgodna z konwencją nazewnictwa ID lub classnameID.</span><span class="sxs-lookup"><span data-stu-id="96785-224">But the Entity Framework can't automatically recognize InstructorID as the primary key of this entity because its name doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="96785-225">W związku z tym `Key` atrybut jest używany do identyfikowania go jako klucz:</span><span class="sxs-lookup"><span data-stu-id="96785-225">Therefore, the `Key` attribute is used to identify it as the key:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="96785-226">Można również użyć atrybutu, `Key` Jeśli jednostka ma własny klucz podstawowy, ale chcesz nazwać Właściwość inną niż classnameID lub ID.</span><span class="sxs-lookup"><span data-stu-id="96785-226">You can also use the `Key` attribute if the entity does have its own primary key but you want to name the property something other than classnameID or ID.</span></span>

<span data-ttu-id="96785-227">Domyślnie EF traktuje klucz jako niegenerowaną przez nie bazę danych, ponieważ kolumna jest dla relacji identyfikującej.</span><span class="sxs-lookup"><span data-stu-id="96785-227">By default, EF treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="96785-228">Właściwość nawigacji instruktora</span><span class="sxs-lookup"><span data-stu-id="96785-228">The Instructor navigation property</span></span>

<span data-ttu-id="96785-229">Jednostka instruktora ma właściwość nawigacji dopuszczającą wartość null `OfficeAssignment` (ponieważ instruktor może nie mieć przypisania pakietu Office), a jednostka OfficeAssignment ma właściwość nawigacji niedopuszczające wartości null `Instructor` (ponieważ przypisanie pakietu Office nie może istnieć bez użycia instruktora — nie `InstructorID` dopuszcza wartości null).</span><span class="sxs-lookup"><span data-stu-id="96785-229">The Instructor entity has a nullable `OfficeAssignment` navigation property (because an instructor might not have an office assignment), and the OfficeAssignment entity has a non-nullable `Instructor` navigation property (because an office assignment can't exist without an instructor -- `InstructorID` is non-nullable).</span></span> <span data-ttu-id="96785-230">Gdy jednostką instruktora jest powiązana jednostka OfficeAssignment, każda jednostka będzie miała odwołanie do drugiego z nich we właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="96785-230">When an Instructor entity has a related OfficeAssignment entity, each entity will have a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="96785-231">Można umieścić `[Required]` atrybut we właściwości nawigacji instruktora, aby określić, że musi istnieć powiązany instruktor, ale nie trzeba tego robić, ponieważ `InstructorID` klucz obcy (który również jest kluczem do tej tabeli) nie dopuszcza wartości null.</span><span class="sxs-lookup"><span data-stu-id="96785-231">You could put a `[Required]` attribute on the Instructor navigation property to specify that there must be a related instructor, but you don't have to do that because the `InstructorID` foreign key (which is also the key to this table) is non-nullable.</span></span>

## <a name="modify-course-entity"></a><span data-ttu-id="96785-232">Modyfikuj jednostkę kursu</span><span class="sxs-lookup"><span data-stu-id="96785-232">Modify Course entity</span></span>

![Jednostka kursu](complex-data-model/_static/course-entity.png)

<span data-ttu-id="96785-234">W *modelach/kurs. cs* Zastąp kod, który został dodany wcześniej do poniższego kodu.</span><span class="sxs-lookup"><span data-stu-id="96785-234">In *Models/Course.cs* , replace the code you added earlier with the following code.</span></span> <span data-ttu-id="96785-235">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="96785-235">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="96785-236">Jednostka kursu ma właściwość klucza obcego `DepartmentID` , która wskazuje jednostkę powiązanego działu i ma `Department` Właściwość nawigacji.</span><span class="sxs-lookup"><span data-stu-id="96785-236">The course entity has a foreign key property `DepartmentID` which points to the related Department entity and it has a `Department` navigation property.</span></span>

<span data-ttu-id="96785-237">Entity Framework nie wymaga dodawania właściwości klucza obcego do modelu danych, jeśli istnieje właściwość nawigacji dla powiązanej jednostki.</span><span class="sxs-lookup"><span data-stu-id="96785-237">The Entity Framework doesn't require you to add a foreign key property to your data model when you have a navigation property for a related entity.</span></span>  <span data-ttu-id="96785-238">EF automatycznie tworzy klucze obce w bazie danych wszędzie tam, gdzie są potrzebne, i tworzy dla nich [Właściwości cienia](/ef/core/modeling/shadow-properties) .</span><span class="sxs-lookup"><span data-stu-id="96785-238">EF automatically creates foreign keys in the database wherever they're needed and creates [shadow properties](/ef/core/modeling/shadow-properties) for them.</span></span> <span data-ttu-id="96785-239">Jednak klucz obcy w modelu danych może sprawiać, że aktualizacje są prostsze i bardziej wydajne.</span><span class="sxs-lookup"><span data-stu-id="96785-239">But having the foreign key in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="96785-240">Na przykład podczas pobierania jednostki kursu do edycji, jednostka działu ma wartość null, jeśli nie zostanie załadowana, więc po zaktualizowaniu jednostki kursu należy najpierw pobrać jednostkę działu.</span><span class="sxs-lookup"><span data-stu-id="96785-240">For example, when you fetch a course entity to edit, the  Department entity is null if you don't load it, so when you update the course entity, you would have to first fetch the Department entity.</span></span> <span data-ttu-id="96785-241">Gdy właściwość klucza obcego `DepartmentID` jest uwzględniona w modelu danych, nie musisz pobierać jednostki działu przed aktualizacją.</span><span class="sxs-lookup"><span data-stu-id="96785-241">When the foreign key property `DepartmentID` is included in the data model, you don't need to fetch the Department entity before you update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="96785-242">Atrybut DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="96785-242">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="96785-243">`DatabaseGenerated`Atrybut z `None` parametrem `CourseID` właściwości określa, że wartości klucza podstawowego są dostarczane przez użytkownika, a nie wygenerowane przez bazę danych.</span><span class="sxs-lookup"><span data-stu-id="96785-243">The `DatabaseGenerated` attribute with the `None` parameter on the `CourseID` property specifies that primary key values are provided by the user rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="96785-244">Domyślnie, Entity Framework zakłada, że wartości klucza podstawowego są generowane przez bazę danych.</span><span class="sxs-lookup"><span data-stu-id="96785-244">By default, Entity Framework assumes that primary key values are generated by the database.</span></span> <span data-ttu-id="96785-245">To wszystko, czego potrzebujesz w większości scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="96785-245">That's what you want in most scenarios.</span></span> <span data-ttu-id="96785-246">Jednak w przypadku jednostek kursu używany jest numer kursu określony przez użytkownika, taki jak seria 1000 dla jednego działu, seria 2000 dla innego działu itd.</span><span class="sxs-lookup"><span data-stu-id="96785-246">However, for Course entities, you'll use a user-specified course number such as a 1000 series for one department, a 2000 series for another department, and so on.</span></span>

<span data-ttu-id="96785-247">Ten `DatabaseGenerated` atrybut może być również używany do generowania wartości domyślnych, tak jak w przypadku kolumn bazy danych używanych do rejestrowania daty utworzenia lub aktualizacji wiersza.</span><span class="sxs-lookup"><span data-stu-id="96785-247">The `DatabaseGenerated` attribute can also be used to generate default values, as in the case of database columns used to record the date a row was created or updated.</span></span>  <span data-ttu-id="96785-248">Aby uzyskać więcej informacji, zobacz [wygenerowane właściwości](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="96785-248">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="96785-249">Właściwości klucza obcego i nawigacji</span><span class="sxs-lookup"><span data-stu-id="96785-249">Foreign key and navigation properties</span></span>

<span data-ttu-id="96785-250">Właściwości klucza obcego i właściwości nawigacji w jednostce kurs odzwierciedlają następujące relacje:</span><span class="sxs-lookup"><span data-stu-id="96785-250">The foreign key properties and navigation properties in the Course entity reflect the following relationships:</span></span>

<span data-ttu-id="96785-251">Kurs jest przypisywany do jednego działu, dlatego istnieje `DepartmentID` klucz obcy i `Department` Właściwość nawigacji dla powodów wymienionych powyżej.</span><span class="sxs-lookup"><span data-stu-id="96785-251">A course is assigned to one department, so there's a `DepartmentID` foreign key and a `Department` navigation property for the reasons mentioned above.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="96785-252">Kurs może zawierać dowolną liczbę studentów, więc `Enrollments` Właściwość nawigacji jest kolekcją:</span><span class="sxs-lookup"><span data-stu-id="96785-252">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="96785-253">Kurs może być nauczany przez wiele instruktorów, więc `CourseAssignments` Właściwość nawigacji jest kolekcją (typ `CourseAssignment` jest wyjaśniony [później](#many-to-many-relationships)):</span><span class="sxs-lookup"><span data-stu-id="96785-253">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection (the type `CourseAssignment` is explained [later](#many-to-many-relationships)):</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a><span data-ttu-id="96785-254">Utwórz jednostkę działu</span><span class="sxs-lookup"><span data-stu-id="96785-254">Create Department entity</span></span>

![Jednostka działu](complex-data-model/_static/department-entity.png)

<span data-ttu-id="96785-256">Utwórz *modele/dział. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="96785-256">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="96785-257">Atrybut Column</span><span class="sxs-lookup"><span data-stu-id="96785-257">The Column attribute</span></span>

<span data-ttu-id="96785-258">Wcześniej użyto `Column` atrybutu do zmiany mapowania nazw kolumn.</span><span class="sxs-lookup"><span data-stu-id="96785-258">Earlier you used the `Column` attribute to change column name mapping.</span></span> <span data-ttu-id="96785-259">W kodzie podmiotu działu `Column` atrybut jest używany do zmiany mapowania typu danych SQL, tak aby kolumna została zdefiniowana przy użyciu SQL Server typu pieniędzy w bazie danych:</span><span class="sxs-lookup"><span data-stu-id="96785-259">In the code for the Department entity, the `Column` attribute is being used to change SQL data type mapping so that the column will be defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="96785-260">Mapowanie kolumn zazwyczaj nie jest wymagane, ponieważ Entity Framework wybiera odpowiedni SQL Server typ danych oparty na typie CLR zdefiniowanym dla właściwości.</span><span class="sxs-lookup"><span data-stu-id="96785-260">Column mapping is generally not required, because the Entity Framework chooses the appropriate SQL Server data type based on the CLR type that you define for the property.</span></span> <span data-ttu-id="96785-261">Typ CLR jest `decimal` mapowany na typ SQL Server `decimal` .</span><span class="sxs-lookup"><span data-stu-id="96785-261">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="96785-262">Ale w tym przypadku wiesz, że kolumna będzie zawierać kwoty walutowe, a typ danych walutowych jest bardziej odpowiedni dla tego.</span><span class="sxs-lookup"><span data-stu-id="96785-262">But in this case you know that the column will be holding currency amounts, and the money data type is more appropriate for that.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="96785-263">Właściwości klucza obcego i nawigacji</span><span class="sxs-lookup"><span data-stu-id="96785-263">Foreign key and navigation properties</span></span>

<span data-ttu-id="96785-264">Właściwości klucza obcego i nawigacji odzwierciedlają następujące relacje:</span><span class="sxs-lookup"><span data-stu-id="96785-264">The foreign key and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="96785-265">Dział może lub nie ma uprawnienia administratora, a administrator jest zawsze instruktorem.</span><span class="sxs-lookup"><span data-stu-id="96785-265">A department may or may not have an administrator, and an administrator is always an instructor.</span></span> <span data-ttu-id="96785-266">W związku z tym `InstructorID` Właściwość jest dołączana jako klucz obcy do jednostki instruktora, a znak zapytania jest dodawany po `int` wyznaczeniu typu, aby oznaczyć właściwość jako wartość null.</span><span class="sxs-lookup"><span data-stu-id="96785-266">Therefore the `InstructorID` property is included as the foreign key to the Instructor entity, and a question mark is added after the `int` type designation to mark the property as nullable.</span></span> <span data-ttu-id="96785-267">Właściwość nawigacji ma nazwę, `Administrator` ale zawiera jednostkę instruktora:</span><span class="sxs-lookup"><span data-stu-id="96785-267">The navigation property is named `Administrator` but holds an Instructor entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="96785-268">Dział może mieć wiele kursów, więc istnieje właściwość nawigacji kursów:</span><span class="sxs-lookup"><span data-stu-id="96785-268">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> <span data-ttu-id="96785-269">Zgodnie z Konwencją Entity Framework włącza kaskadowe usuwanie kluczy obcych niedopuszczających wartości null oraz relacji wiele-do-wielu.</span><span class="sxs-lookup"><span data-stu-id="96785-269">By convention, the Entity Framework enables cascade delete for non-nullable foreign keys and for many-to-many relationships.</span></span> <span data-ttu-id="96785-270">Może to spowodować cykliczne reguły usuwania kaskadowego, co spowoduje wyjątek podczas próby dodania migracji.</span><span class="sxs-lookup"><span data-stu-id="96785-270">This can result in circular cascade delete rules, which will cause an exception when you try to add a migration.</span></span> <span data-ttu-id="96785-271">Na przykład jeśli nie zdefiniowano właściwości Department. InstructorID jako nullable, EF skonfiguruje regułę usuwania kaskadowego w celu usunięcia działu po usunięciu instruktora, który nie jest tym, co się dzieje.</span><span class="sxs-lookup"><span data-stu-id="96785-271">For example, if you didn't define the Department.InstructorID property as nullable, EF would configure a cascade delete rule to delete the department when you delete the instructor, which isn't what you want to have happen.</span></span> <span data-ttu-id="96785-272">Jeśli reguły biznesowe wymagają `InstructorID` , aby właściwość nie dopuszczać wartości null, należy użyć następującej instrukcji interfejsu API Fluent, aby wyłączyć kaskadowe usuwanie relacji:</span><span class="sxs-lookup"><span data-stu-id="96785-272">If your business rules required the `InstructorID` property to be non-nullable, you would have to use the following fluent API statement to disable cascade delete on the relationship:</span></span>
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a><span data-ttu-id="96785-273">Modyfikuj jednostkę rejestracji</span><span class="sxs-lookup"><span data-stu-id="96785-273">Modify Enrollment entity</span></span>

![Jednostka rejestracji](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="96785-275">W *modelach/rejestracji. cs* Zastąp kod, który został dodany wcześniej do poniższego kodu:</span><span class="sxs-lookup"><span data-stu-id="96785-275">In *Models/Enrollment.cs* , replace the code you added earlier with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="96785-276">Właściwości klucza obcego i nawigacji</span><span class="sxs-lookup"><span data-stu-id="96785-276">Foreign key and navigation properties</span></span>

<span data-ttu-id="96785-277">Właściwości klucza obcego i właściwości nawigacji odzwierciedlają następujące relacje:</span><span class="sxs-lookup"><span data-stu-id="96785-277">The foreign key properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="96785-278">Rekord rejestracji dotyczy pojedynczego kursu, dlatego istnieje `CourseID` Właściwość klucza obcego i `Course` Właściwość nawigacji:</span><span class="sxs-lookup"><span data-stu-id="96785-278">An enrollment record is for a single course, so there's a `CourseID` foreign key property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="96785-279">Rekord rejestracji dotyczy pojedynczego ucznia, dlatego istnieje `StudentID` Właściwość klucza obcego i `Student` Właściwość nawigacji:</span><span class="sxs-lookup"><span data-stu-id="96785-279">An enrollment record is for a single student, so there's a `StudentID` foreign key property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="96785-280">Relacje wiele do wielu</span><span class="sxs-lookup"><span data-stu-id="96785-280">Many-to-Many relationships</span></span>

<span data-ttu-id="96785-281">Istnieje relacja wiele-do-wielu między jednostkami uczniów i kursów, a jednostka rejestracji działa jako tabela sprzężeń wiele-do-wielu *z ładunkiem* w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="96785-281">There's a many-to-many relationship between the Student and Course entities, and the Enrollment entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="96785-282">"Z ładunkiem" oznacza, że tabela rejestracji zawiera dodatkowe dane, a nie klucze obce dla sprzężonych tabel (w tym przypadku klucz podstawowy i właściwość klasy).</span><span class="sxs-lookup"><span data-stu-id="96785-282">"With payload" means that the Enrollment table contains additional data besides foreign keys for the joined tables (in this case, a primary key and a Grade property).</span></span>

<span data-ttu-id="96785-283">Na poniższej ilustracji pokazano, jak wyglądają te relacje w diagramie jednostek.</span><span class="sxs-lookup"><span data-stu-id="96785-283">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="96785-284">(Ten diagram został wygenerowany przy użyciu Entity Framework narzędzia do zarządzania prawami do programu EF 6. x; Tworzenie diagramu nie jest częścią samouczka, a właśnie jest używane w tym miejscu jako ilustracja).</span><span class="sxs-lookup"><span data-stu-id="96785-284">(This diagram was generated using the Entity Framework Power Tools for EF 6.x; creating the diagram isn't part of the tutorial, it's just being used here as an illustration.)</span></span>

![Student-Course wiele do wielu relacji](complex-data-model/_static/student-course.png)

<span data-ttu-id="96785-286">Każda linia relacji ma 1 na jednym końcu i gwiazdkę (\*) na drugim, wskazując relację jeden do wielu.</span><span class="sxs-lookup"><span data-stu-id="96785-286">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="96785-287">Jeśli tabela rejestracji nie zawiera informacji o klasie, musi zawierać tylko dwa klucze obce CourseID i StudentID.</span><span class="sxs-lookup"><span data-stu-id="96785-287">If the Enrollment table didn't include grade information, it would only need to contain the two foreign keys CourseID and StudentID.</span></span> <span data-ttu-id="96785-288">W takim przypadku byłoby to tabela sprzężenia wiele-do-wielu bez ładunku (lub czystej tabeli sprzężeń) w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="96785-288">In that case, it would be a many-to-many join table without payload (or a pure join table) in the database.</span></span> <span data-ttu-id="96785-289">Jednostki instruktora i kursy mają ten rodzaj relacji wiele-do-wielu, a następnym krokiem jest utworzenie klasy jednostki do działania jako tabela sprzężenia bez ładunku.</span><span class="sxs-lookup"><span data-stu-id="96785-289">The Instructor and Course entities have that kind of many-to-many relationship, and your next step is to create an entity class to function as a join table without payload.</span></span>

<span data-ttu-id="96785-290">(Dr 6. x obsługuje niejawne tabele sprzężeń dla relacji wiele-do-wielu, ale EF Core nie.</span><span class="sxs-lookup"><span data-stu-id="96785-290">(EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="96785-291">Aby uzyskać więcej informacji, zobacz [dyskusję w repozytorium EF Core GitHub](https://github.com/aspnet/EntityFramework/issues/1368)).</span><span class="sxs-lookup"><span data-stu-id="96785-291">For more information, see the [discussion in the EF Core GitHub repository](https://github.com/aspnet/EntityFramework/issues/1368).)</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="96785-292">Jednostka CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="96785-292">The CourseAssignment entity</span></span>

![Jednostka CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="96785-294">Utwórz *modele/CourseAssignment. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="96785-294">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a><span data-ttu-id="96785-295">Przyłączanie nazw jednostek</span><span class="sxs-lookup"><span data-stu-id="96785-295">Join entity names</span></span>

<span data-ttu-id="96785-296">Tabela sprzężenia jest wymagana w bazie danych programu dla instruktora "wiele do wielu" i musi być reprezentowana przez zestaw jednostek.</span><span class="sxs-lookup"><span data-stu-id="96785-296">A join table is required in the database for the Instructor-to-Courses many-to-many relationship, and it has to be represented by an entity set.</span></span> <span data-ttu-id="96785-297">Nazwa jednostki sprzężenia jest wspólna w `EntityName1EntityName2` przypadku, gdy jest to możliwe `CourseInstructor` .</span><span class="sxs-lookup"><span data-stu-id="96785-297">It's common to name a join entity `EntityName1EntityName2`, which in this case would be `CourseInstructor`.</span></span> <span data-ttu-id="96785-298">Zalecamy jednak wybranie nazwy opisującej relację.</span><span class="sxs-lookup"><span data-stu-id="96785-298">However, we recommend that you choose a name that describes the relationship.</span></span> <span data-ttu-id="96785-299">Modele danych zaczynają się łatwo i zwiększają, dzięki czemu nie są często odbierane ładunki.</span><span class="sxs-lookup"><span data-stu-id="96785-299">Data models start out simple and grow, with no-payload joins frequently getting payloads later.</span></span> <span data-ttu-id="96785-300">Jeśli zaczynasz od nazwy obiektu opisowego, nie będzie konieczna zmiana nazwy później.</span><span class="sxs-lookup"><span data-stu-id="96785-300">If you start with a descriptive entity name, you won't have to change the name later.</span></span> <span data-ttu-id="96785-301">Najlepiej, aby jednostka sprzężenia miała własną nazwę naturalną (prawdopodobnie jednowyrazową) w domenie biznesowej.</span><span class="sxs-lookup"><span data-stu-id="96785-301">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="96785-302">Na przykład książki i klienci mogą być połączone poprzez klasyfikacje.</span><span class="sxs-lookup"><span data-stu-id="96785-302">For example, Books and Customers could be linked through Ratings.</span></span> <span data-ttu-id="96785-303">Dla tej relacji `CourseAssignment` jest lepszym wyborem niż `CourseInstructor` .</span><span class="sxs-lookup"><span data-stu-id="96785-303">For this relationship, `CourseAssignment` is a better choice than `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="96785-304">Klucz złożony</span><span class="sxs-lookup"><span data-stu-id="96785-304">Composite key</span></span>

<span data-ttu-id="96785-305">Ponieważ klucze obce nie dopuszczają wartości null i jednoznacznie identyfikują każdy wiersz tabeli, nie ma potrzeby oddzielnego klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="96785-305">Since the foreign keys are not nullable and together uniquely identify each row of the table, there's no need for a separate primary key.</span></span> <span data-ttu-id="96785-306">Właściwości *InstructorID* i *CourseID* powinny działać jako złożony klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="96785-306">The *InstructorID* and *CourseID* properties should function as a composite primary key.</span></span> <span data-ttu-id="96785-307">Jedynym sposobem identyfikacji złożonych kluczy podstawowych do EF jest użycie *interfejsu API Fluent* (nie można go wykonać przy użyciu atrybutów).</span><span class="sxs-lookup"><span data-stu-id="96785-307">The only way to identify composite primary keys to EF is by using the *fluent API* (it can't be done by using attributes).</span></span> <span data-ttu-id="96785-308">W następnej sekcji zobaczysz, jak skonfigurować złożony klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="96785-308">You'll see how to configure the composite primary key in the next section.</span></span>

<span data-ttu-id="96785-309">Klucz złożony gwarantuje, że chociaż można mieć wiele wierszy dla jednego kursu i wiele wierszy dla jednego instruktora, nie można mieć wielu wierszy dla tego samego instruktora i kursu.</span><span class="sxs-lookup"><span data-stu-id="96785-309">The composite key ensures that while you can have multiple rows for one course, and multiple rows for one instructor, you can't have multiple rows for the same instructor and course.</span></span> <span data-ttu-id="96785-310">`Enrollment`Jednostka Join definiuje własny klucz podstawowy, więc możliwe jest duplikowanie tego sortowania.</span><span class="sxs-lookup"><span data-stu-id="96785-310">The `Enrollment` join entity defines its own primary key, so duplicates of this sort are possible.</span></span> <span data-ttu-id="96785-311">Aby uniknąć takich duplikatów, można dodać unikatowy indeks do pól klucza obcego lub skonfigurować `Enrollment` przy użyciu podstawowego klucza złożonego podobnego do `CourseAssignment` .</span><span class="sxs-lookup"><span data-stu-id="96785-311">To prevent such duplicates, you could add a unique index on the foreign key fields, or configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="96785-312">Aby uzyskać więcej informacji, zobacz [indeksy](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="96785-312">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="96785-313">Aktualizowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="96785-313">Update the database context</span></span>

<span data-ttu-id="96785-314">Dodaj następujący wyróżniony kod do pliku *Data/SchoolContext. cs* :</span><span class="sxs-lookup"><span data-stu-id="96785-314">Add the following highlighted code to the *Data/SchoolContext.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="96785-315">Ten kod dodaje nowe jednostki i konfiguruje złożony klucz podstawowy jednostki CourseAssignment.</span><span class="sxs-lookup"><span data-stu-id="96785-315">This code adds the new entities and configures the CourseAssignment entity's composite primary key.</span></span>

## <a name="about-a-fluent-api-alternative"></a><span data-ttu-id="96785-316">Informacje o alternatywnym interfejsie API Fluent</span><span class="sxs-lookup"><span data-stu-id="96785-316">About a fluent API alternative</span></span>

<span data-ttu-id="96785-317">Kod w `OnModelCreating` metodzie `DbContext` klasy używa *interfejsu API Fluent* do konfigurowania zachowania EF.</span><span class="sxs-lookup"><span data-stu-id="96785-317">The code in the `OnModelCreating` method of the `DbContext` class uses the *fluent API* to configure EF behavior.</span></span> <span data-ttu-id="96785-318">Interfejs API nosi nazwę "Fluent", ponieważ jest często używany przez ciąg szeregu wywołań metod wraz z pojedynczą instrukcją, jak w poniższym przykładzie z [dokumentacji EF Core](/ef/core/modeling/#use-fluent-api-to-configure-a-model):</span><span class="sxs-lookup"><span data-stu-id="96785-318">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement, as in this example from the [EF Core documentation](/ef/core/modeling/#use-fluent-api-to-configure-a-model):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="96785-319">W tym samouczku korzystasz z interfejsu API Fluent tylko dla mapowania bazy danych, której nie można wykonać przy użyciu atrybutów.</span><span class="sxs-lookup"><span data-stu-id="96785-319">In this tutorial, you're using the fluent API only for database mapping that you can't do with attributes.</span></span> <span data-ttu-id="96785-320">Można jednak również użyć interfejsu API Fluent, aby określić większość reguł formatowania, walidacji i mapowania, które można wykonać przy użyciu atrybutów.</span><span class="sxs-lookup"><span data-stu-id="96785-320">However, you can also use the fluent API to specify most of the formatting, validation, and mapping rules that you can do by using attributes.</span></span> <span data-ttu-id="96785-321">Niektórych atrybutów, takich jak `MinimumLength` nie można zastosować w interfejsie API Fluent.</span><span class="sxs-lookup"><span data-stu-id="96785-321">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="96785-322">Jak wspomniano wcześniej, `MinimumLength` nie zmienia schematu, stosuje tylko regułę walidacji po stronie klienta i serwera.</span><span class="sxs-lookup"><span data-stu-id="96785-322">As mentioned previously, `MinimumLength` doesn't change the schema, it only applies a client and server side validation rule.</span></span>

<span data-ttu-id="96785-323">Niektórzy deweloperzy wolą korzystać z interfejsu API Fluent wyłącznie w taki sposób, aby mogli utrzymać czyste klasy jednostek.</span><span class="sxs-lookup"><span data-stu-id="96785-323">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="96785-324">Jeśli chcesz, możesz mieszać atrybuty i interfejs API Fluent, a istnieje kilka dostosowań, które można wykonać tylko za pomocą interfejsu API Fluent, ale ogólnie zalecane jest, aby wybrać jeden z tych dwóch podejść i użyć go spójnie tak dużo, jak to możliwe.</span><span class="sxs-lookup"><span data-stu-id="96785-324">You can mix attributes and fluent API if you want, and there are a few customizations that can only be done by using fluent API, but in general the recommended practice is to choose one of these two approaches and use that consistently as much as possible.</span></span> <span data-ttu-id="96785-325">Jeśli używasz obu tych metod, pamiętaj, że w przypadku wystąpienia konfliktu, interfejs API Fluent przesłania atrybuty.</span><span class="sxs-lookup"><span data-stu-id="96785-325">If you do use both, note that wherever there's a conflict, Fluent API overrides attributes.</span></span>

<span data-ttu-id="96785-326">Aby uzyskać więcej informacji na temat atrybutów a interfejs API Fluent, zobacz [metody konfiguracji](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="96785-326">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="96785-327">Diagram jednostek przedstawiający relacje</span><span class="sxs-lookup"><span data-stu-id="96785-327">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="96785-328">Na poniższej ilustracji przedstawiono diagram, który Entity Framework narzędzia do tworzenia dla gotowego modelu szkoły.</span><span class="sxs-lookup"><span data-stu-id="96785-328">The following illustration shows the diagram that the Entity Framework Power Tools create for the completed School model.</span></span>

![Diagram jednostek](complex-data-model/_static/diagram.png)

<span data-ttu-id="96785-330">Oprócz linii relacji jeden do wielu (od 1 do \* ) można zobaczyć w tym miejscu linię relacji "jeden do zera" (od 1 do 0.. 1) między jednostkami instruktora i OfficeAssignment oraz wierszem relacji zero-lub-jeden-do-wielu (0.. 1 do \*) między instruktorem a jednostkami działu.</span><span class="sxs-lookup"><span data-stu-id="96785-330">Besides the one-to-many relationship lines (1 to \*), you can see here the one-to-zero-or-one relationship line (1 to 0..1) between the Instructor and OfficeAssignment entities and the zero-or-one-to-many relationship line (0..1 to \*) between the Instructor and Department entities.</span></span>

## <a name="seed-database-with-test-data"></a><span data-ttu-id="96785-331">Wypełnianie bazy danych z danymi testowymi</span><span class="sxs-lookup"><span data-stu-id="96785-331">Seed database with test data</span></span>

<span data-ttu-id="96785-332">Zastąp kod w pliku *Data/dbinitialize. cs* następującym kodem, aby zapewnić dane dotyczące inicjatorów dla nowo utworzonych jednostek.</span><span class="sxs-lookup"><span data-stu-id="96785-332">Replace the code in the *Data/DbInitializer.cs* file with the following code in order to provide seed data for the new entities you've created.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="96785-333">Jak przedstawiono w pierwszym samouczku, większość tego kodu po prostu tworzy nowe obiekty jednostki i ładuje przykładowe dane do właściwości, które są wymagane do testowania.</span><span class="sxs-lookup"><span data-stu-id="96785-333">As you saw in the first tutorial, most of this code simply creates new entity objects and loads sample data into properties as required for testing.</span></span> <span data-ttu-id="96785-334">Zauważ, że relacje wiele-do-wielu są obsługiwane: kod tworzy relacje przez tworzenie jednostek w `Enrollments` `CourseAssignment` zestawach jednostek i.</span><span class="sxs-lookup"><span data-stu-id="96785-334">Notice how the many-to-many relationships are handled: the code creates relationships by creating entities in the `Enrollments` and `CourseAssignment` join entity sets.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="96785-335">Dodawanie migracji</span><span class="sxs-lookup"><span data-stu-id="96785-335">Add a migration</span></span>

<span data-ttu-id="96785-336">Zapisz zmiany i skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="96785-336">Save your changes and build the project.</span></span> <span data-ttu-id="96785-337">Następnie otwórz okno polecenia w folderze projektu i wprowadź `migrations add` polecenie (nie wykonuj jeszcze polecenia Update-Database):</span><span class="sxs-lookup"><span data-stu-id="96785-337">Then open the command window in the project folder and enter the `migrations add` command (don't do the update-database command yet):</span></span>

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

<span data-ttu-id="96785-338">Zostanie wyświetlone ostrzeżenie o możliwej utracie danych.</span><span class="sxs-lookup"><span data-stu-id="96785-338">You get a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="96785-339">Jeśli podjęto próbę uruchomienia `database update` polecenia w tym punkcie (nie rób tego jeszcze), zostanie wyświetlony następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="96785-339">If you tried to run the `database update` command at this point (don't do it yet), you would get the following error:</span></span>

> <span data-ttu-id="96785-340">Instrukcja ALTER TABLE spowodowała konflikt z ograniczeniem klucza obcego "FK_dbo. Course_dbo. Department_DepartmentID ".</span><span class="sxs-lookup"><span data-stu-id="96785-340">The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID".</span></span> <span data-ttu-id="96785-341">Konflikt wystąpił w bazie danych "ContosoUniversity", tabela "dbo. Dział ", kolumna" DepartmentID ".</span><span class="sxs-lookup"><span data-stu-id="96785-341">The conflict occurred in database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.</span></span>

<span data-ttu-id="96785-342">Czasami w przypadku wykonywania migracji z istniejącymi danymi należy wstawić dane szczątkowe do bazy danych w celu spełnienia ograniczeń klucza obcego.</span><span class="sxs-lookup"><span data-stu-id="96785-342">Sometimes when you execute migrations with existing data, you need to insert stub data into the database to satisfy foreign key constraints.</span></span> <span data-ttu-id="96785-343">Wygenerowany kod w `Up` metodzie dodaje klucz obcy DepartmentID niedopuszczający wartości null do tabeli kursów.</span><span class="sxs-lookup"><span data-stu-id="96785-343">The generated code in the `Up` method adds a non-nullable DepartmentID foreign key to the Course table.</span></span> <span data-ttu-id="96785-344">Jeśli w tabeli kursów występuje już wiersz, `AddColumn` operacja nie powiedzie się, ponieważ SQL Server nie wie, jaka wartość należy umieścić w kolumnie, która nie może mieć wartości null.</span><span class="sxs-lookup"><span data-stu-id="96785-344">If there are already rows in the Course table when the code runs, the `AddColumn` operation fails because SQL Server doesn't know what value to put in the column that can't be null.</span></span> <span data-ttu-id="96785-345">Na potrzeby tego samouczka przeprowadzisz migrację do nowej bazy danych, ale w aplikacji produkcyjnej musisz przeprowadzić migrację istniejących danych, więc w poniższych wskazówkach przedstawiono przykład sposobu, w jaki należy to zrobić.</span><span class="sxs-lookup"><span data-stu-id="96785-345">For this tutorial you'll run the migration on a new database, but in a production application you'd have to make the migration handle existing data, so the following directions show an example of how to do that.</span></span>

<span data-ttu-id="96785-346">Aby ta migracja działała z istniejącymi danymi, należy zmienić kod w celu nadania nowej kolumnie wartości domyślnej, a także utworzyć Wydział zastępczy o nazwie "Temp", który będzie pełnił rolę domyślnego działu.</span><span class="sxs-lookup"><span data-stu-id="96785-346">To make this migration work with existing data you have to change the code to give the new column a default value, and create a stub department named "Temp" to act as the default department.</span></span> <span data-ttu-id="96785-347">W związku z tym istniejące wiersze kursu będą ze sobą powiązane z działem "Temp" po `Up` uruchomieniu metody.</span><span class="sxs-lookup"><span data-stu-id="96785-347">As a result, existing Course rows will all be related to the "Temp" department after the `Up` method runs.</span></span>

* <span data-ttu-id="96785-348">Otwórz plik *{timestamp} _ComplexDataModel. cs* .</span><span class="sxs-lookup"><span data-stu-id="96785-348">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>

* <span data-ttu-id="96785-349">Dodaj komentarz do wiersza kodu, który dodaje kolumnę DepartmentID do tabeli kursów.</span><span class="sxs-lookup"><span data-stu-id="96785-349">Comment out the line of code that adds the DepartmentID column to the Course table.</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* <span data-ttu-id="96785-350">Dodaj następujący wyróżniony kod po kodzie, który tworzy tabelę działu:</span><span class="sxs-lookup"><span data-stu-id="96785-350">Add the following highlighted code after the code that creates the Department table:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="96785-351">W aplikacji produkcyjnej Napisz kod lub skrypty w celu dodania wierszy działu i powiązania wierszy kursu z nowym wierszem działu.</span><span class="sxs-lookup"><span data-stu-id="96785-351">In a production application, you would write code or scripts to add Department rows and relate Course rows to the new Department rows.</span></span> <span data-ttu-id="96785-352">W kolumnie kurs. DepartmentID nie będzie już potrzebny żaden dział "Temp" ani wartość domyślna.</span><span class="sxs-lookup"><span data-stu-id="96785-352">You would then no longer need the "Temp" department or the default value on the Course.DepartmentID column.</span></span>

<span data-ttu-id="96785-353">Zapisz zmiany i skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="96785-353">Save your changes and build the project.</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="96785-354">Zmień parametry połączenia</span><span class="sxs-lookup"><span data-stu-id="96785-354">Change the connection string</span></span>

<span data-ttu-id="96785-355">Teraz masz nowy kod w `DbInitializer` klasie, który dodaje dane inicjatora dla nowych jednostek do pustej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96785-355">You now have new code in the `DbInitializer` class that adds seed data for the new entities to an empty database.</span></span> <span data-ttu-id="96785-356">Aby program EF utworzył nową pustą bazę danych, należy zmienić nazwę bazy danych w parametrach połączenia w *:::no-loc(appsettings.json):::* ContosoUniversity3 lub innej nazwie, która nie została użyta na komputerze, którego używasz.</span><span class="sxs-lookup"><span data-stu-id="96785-356">To make EF create a new empty database, change the name of the database in the connection string in *:::no-loc(appsettings.json):::* to ContosoUniversity3 or some other name that you haven't used on the computer you're using.</span></span>

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

<span data-ttu-id="96785-357">Zapisz zmiany w *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="96785-357">Save your change to *:::no-loc(appsettings.json):::* .</span></span>

> [!NOTE]
> <span data-ttu-id="96785-358">Alternatywą dla zmiany nazwy bazy danych jest usunięcie bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96785-358">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="96785-359">Użyj **Eksplorator obiektów SQL Server** (SSOX) lub `database drop` interfejsu wiersza polecenia:</span><span class="sxs-lookup"><span data-stu-id="96785-359">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```dotnetcli
> dotnet ef database drop
> ```

## <a name="update-the-database"></a><span data-ttu-id="96785-360">Aktualizowanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="96785-360">Update the database</span></span>

<span data-ttu-id="96785-361">Po zmianie nazwy bazy danych lub usunięciu bazy danych Uruchom `database update` polecenie w oknie wiersza polecenia, aby wykonać migracje.</span><span class="sxs-lookup"><span data-stu-id="96785-361">After you have changed the database name or deleted the database, run the `database update` command in the command window to execute the migrations.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="96785-362">Uruchom aplikację, aby spowodować `DbInitializer.Initialize` uruchomienie metody i wypełnianie nowej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="96785-362">Run the app to cause the `DbInitializer.Initialize` method to run and populate the new database.</span></span>

<span data-ttu-id="96785-363">Otwórz bazę danych w programie SSOX, tak jak wcześniej, i rozwiń węzeł **tabele** , aby zobaczyć, że wszystkie tabele zostały utworzone.</span><span class="sxs-lookup"><span data-stu-id="96785-363">Open the database in SSOX as you did earlier, and expand the **Tables** node to see that all of the tables have been created.</span></span> <span data-ttu-id="96785-364">(Jeśli nadal masz otwarty SSOX od momentu wcześniejszego, kliknij przycisk **odświeżania** ).</span><span class="sxs-lookup"><span data-stu-id="96785-364">(If you still have SSOX open from the earlier time, click the **Refresh** button.)</span></span>

![Tabele w SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="96785-366">Uruchom aplikację, aby wyzwolić kod inicjatora, który odziarnauje bazę danych.</span><span class="sxs-lookup"><span data-stu-id="96785-366">Run the app to trigger the initializer code that seeds the database.</span></span>

<span data-ttu-id="96785-367">Kliknij prawym przyciskiem myszy tabelę **CourseAssignment** , a następnie wybierz pozycję **Wyświetl dane** , aby sprawdzić, czy zawiera ona dane.</span><span class="sxs-lookup"><span data-stu-id="96785-367">Right-click the **CourseAssignment** table and select **View Data** to verify that it has data in it.</span></span>

![CourseAssignment dane w SSOX](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a><span data-ttu-id="96785-369">Uzyskiwanie kodu</span><span class="sxs-lookup"><span data-stu-id="96785-369">Get the code</span></span>

[<span data-ttu-id="96785-370">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="96785-370">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="96785-371">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="96785-371">Next steps</span></span>

<span data-ttu-id="96785-372">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="96785-372">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="96785-373">Dostosowany model danych</span><span class="sxs-lookup"><span data-stu-id="96785-373">Customized the Data model</span></span>
> * <span data-ttu-id="96785-374">Wprowadzono zmiany w jednostce ucznia</span><span class="sxs-lookup"><span data-stu-id="96785-374">Made changes to Student entity</span></span>
> * <span data-ttu-id="96785-375">Utworzono jednostkę instruktora</span><span class="sxs-lookup"><span data-stu-id="96785-375">Created Instructor entity</span></span>
> * <span data-ttu-id="96785-376">Utworzono jednostkę OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="96785-376">Created OfficeAssignment entity</span></span>
> * <span data-ttu-id="96785-377">Zmodyfikowana jednostka kursu</span><span class="sxs-lookup"><span data-stu-id="96785-377">Modified Course entity</span></span>
> * <span data-ttu-id="96785-378">Utworzona jednostka działu</span><span class="sxs-lookup"><span data-stu-id="96785-378">Created Department entity</span></span>
> * <span data-ttu-id="96785-379">Zmodyfikowano jednostkę rejestracji</span><span class="sxs-lookup"><span data-stu-id="96785-379">Modified Enrollment entity</span></span>
> * <span data-ttu-id="96785-380">Zaktualizowano kontekst bazy danych</span><span class="sxs-lookup"><span data-stu-id="96785-380">Updated the database context</span></span>
> * <span data-ttu-id="96785-381">Wypełnianie bazy danych z danymi testowymi</span><span class="sxs-lookup"><span data-stu-id="96785-381">Seeded database with test data</span></span>
> * <span data-ttu-id="96785-382">Dodano migrację</span><span class="sxs-lookup"><span data-stu-id="96785-382">Added a migration</span></span>
> * <span data-ttu-id="96785-383">Zmieniono parametry połączenia</span><span class="sxs-lookup"><span data-stu-id="96785-383">Changed the connection string</span></span>
> * <span data-ttu-id="96785-384">Zaktualizowano bazę danych</span><span class="sxs-lookup"><span data-stu-id="96785-384">Updated the database</span></span>

<span data-ttu-id="96785-385">Przejdź do następnego samouczka, aby dowiedzieć się więcej o tym, jak uzyskać dostęp do powiązanych danych.</span><span class="sxs-lookup"><span data-stu-id="96785-385">Advance to the next tutorial to learn more about how to access related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="96785-386">Dalej: dostęp do powiązanych danych</span><span class="sxs-lookup"><span data-stu-id="96785-386">Next: Access related data</span></span>](read-related-data.md)
