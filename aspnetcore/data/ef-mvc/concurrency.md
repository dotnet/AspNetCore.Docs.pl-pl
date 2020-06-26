---
title: 'Samouczek: obsługa współbieżności ASP.NET MVC z EF Core'
description: W tym samouczku pokazano, jak obsłużyć konflikty, gdy wielu użytkowników jednocześnie zaktualizuje tę samą jednostkę.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/concurrency
ms.openlocfilehash: 3038ae8f01273013e6c35694583d9674a1668bac
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401561"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a><span data-ttu-id="795b3-103">Samouczek: obsługa współbieżności ASP.NET MVC z EF Core</span><span class="sxs-lookup"><span data-stu-id="795b3-103">Tutorial: Handle concurrency - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="795b3-104">W poprzednich samouczkach przedstawiono sposób aktualizowania danych.</span><span class="sxs-lookup"><span data-stu-id="795b3-104">In earlier tutorials, you learned how to update data.</span></span> <span data-ttu-id="795b3-105">W tym samouczku pokazano, jak obsłużyć konflikty, gdy wielu użytkowników jednocześnie zaktualizuje tę samą jednostkę.</span><span class="sxs-lookup"><span data-stu-id="795b3-105">This tutorial shows how to handle conflicts when multiple users update the same entity at the same time.</span></span>

<span data-ttu-id="795b3-106">Utworzysz strony sieci Web, które współpracują z jednostką działu i obsługują błędy współbieżności.</span><span class="sxs-lookup"><span data-stu-id="795b3-106">You'll create web pages that work with the Department entity and handle concurrency errors.</span></span> <span data-ttu-id="795b3-107">Na poniższych ilustracjach przedstawiono strony edycji i usuwania, w tym niektóre komunikaty, które są wyświetlane w przypadku wystąpienia konfliktu współbieżności.</span><span class="sxs-lookup"><span data-stu-id="795b3-107">The following illustrations show the Edit and Delete pages, including some messages that are displayed if a concurrency conflict occurs.</span></span>

![Strona edycji działu](concurrency/_static/edit-error.png)

![Strona usuwania działu](concurrency/_static/delete-error.png)

<span data-ttu-id="795b3-110">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="795b3-110">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="795b3-111">Informacje o konfliktach współbieżności</span><span class="sxs-lookup"><span data-stu-id="795b3-111">Learn about concurrency conflicts</span></span>
> * <span data-ttu-id="795b3-112">Dodaj właściwość śledzenia</span><span class="sxs-lookup"><span data-stu-id="795b3-112">Add a tracking property</span></span>
> * <span data-ttu-id="795b3-113">Tworzenie kontrolera i widoków działów</span><span class="sxs-lookup"><span data-stu-id="795b3-113">Create Departments controller and views</span></span>
> * <span data-ttu-id="795b3-114">Aktualizuj widok indeksu</span><span class="sxs-lookup"><span data-stu-id="795b3-114">Update Index view</span></span>
> * <span data-ttu-id="795b3-115">Aktualizowanie metod edycji</span><span class="sxs-lookup"><span data-stu-id="795b3-115">Update Edit methods</span></span>
> * <span data-ttu-id="795b3-116">Aktualizuj widok edycji</span><span class="sxs-lookup"><span data-stu-id="795b3-116">Update Edit view</span></span>
> * <span data-ttu-id="795b3-117">Testuj konflikty współbieżności</span><span class="sxs-lookup"><span data-stu-id="795b3-117">Test concurrency conflicts</span></span>
> * <span data-ttu-id="795b3-118">Aktualizowanie strony usuwania</span><span class="sxs-lookup"><span data-stu-id="795b3-118">Update the Delete page</span></span>
> * <span data-ttu-id="795b3-119">Aktualizowanie szczegółów i Tworzenie widoków</span><span class="sxs-lookup"><span data-stu-id="795b3-119">Update Details and Create views</span></span>

## <a name="prerequisites"></a><span data-ttu-id="795b3-120">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="795b3-120">Prerequisites</span></span>

* [<span data-ttu-id="795b3-121">Aktualizowanie powiązanych danych</span><span class="sxs-lookup"><span data-stu-id="795b3-121">Update related data</span></span>](update-related-data.md)

## <a name="concurrency-conflicts"></a><span data-ttu-id="795b3-122">Konflikty współbieżności</span><span class="sxs-lookup"><span data-stu-id="795b3-122">Concurrency conflicts</span></span>

<span data-ttu-id="795b3-123">Konflikt współbieżności występuje, gdy jeden użytkownik wyświetla dane jednostki w celu ich edycji, a następnie inny użytkownik aktualizuje dane tej samej jednostki przed zapisaniem w bazie danych pierwszego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="795b3-123">A concurrency conflict occurs when one user displays an entity's data in order to edit it, and then another user updates the same entity's data before the first user's change is written to the database.</span></span> <span data-ttu-id="795b3-124">Jeśli nie włączysz wykrywania takich konfliktów, osoba, która aktualizuje bazę danych, ostatnio zastępuje zmiany wprowadzone przez innych użytkowników.</span><span class="sxs-lookup"><span data-stu-id="795b3-124">If you don't enable the detection of such conflicts, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="795b3-125">W wielu aplikacjach to ryzyko jest akceptowalne: w przypadku kilku użytkowników lub kilku aktualizacji lub jeśli nie jest to naprawdę krytyczne, jeśli niektóre zmiany zostaną nadpisywane, koszt programowania współbieżności może wznieść korzyści.</span><span class="sxs-lookup"><span data-stu-id="795b3-125">In many applications, this risk is acceptable: if there are few users, or few updates, or if isn't really critical if some changes are overwritten, the cost of programming for concurrency might outweigh the benefit.</span></span> <span data-ttu-id="795b3-126">W takim przypadku nie trzeba konfigurować aplikacji do obsługi konfliktów współbieżności.</span><span class="sxs-lookup"><span data-stu-id="795b3-126">In that case, you don't have to configure the application to handle concurrency conflicts.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="795b3-127">Współbieżność pesymistyczna (blokowanie)</span><span class="sxs-lookup"><span data-stu-id="795b3-127">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="795b3-128">Jeśli aplikacja musi zapobiegać przypadkowej utracie danych w scenariuszach współbieżności, jeden ze sposobów jest używany do blokowania baz danych.</span><span class="sxs-lookup"><span data-stu-id="795b3-128">If your application does need to prevent accidental data loss in concurrency scenarios, one way to do that is to use database locks.</span></span> <span data-ttu-id="795b3-129">Jest to nazywane pesymistyczną współbieżnością.</span><span class="sxs-lookup"><span data-stu-id="795b3-129">This is called pessimistic concurrency.</span></span> <span data-ttu-id="795b3-130">Na przykład przed odczytaniem wiersza z bazy danych należy zażądać blokady dla dostępu tylko do odczytu lub do aktualizacji.</span><span class="sxs-lookup"><span data-stu-id="795b3-130">For example, before you read a row from a database, you request a lock for read-only or for update access.</span></span> <span data-ttu-id="795b3-131">Jeśli zablokujesz wiersz na potrzeby dostępu do aktualizacji, żaden inny użytkownik nie będzie mógł zablokować wiersza dla dostępu tylko do odczytu lub aktualizacji, ponieważ spowodowałoby to skopiowanie danych w procesie.</span><span class="sxs-lookup"><span data-stu-id="795b3-131">If you lock a row for update access, no other users are allowed to lock the row either for read-only or update access, because they would get a copy of data that's in the process of being changed.</span></span> <span data-ttu-id="795b3-132">Jeśli zablokujesz wiersz dla dostępu tylko do odczytu, inne osoby mogą także zablokować dostęp tylko do odczytu, ale nie dla aktualizacji.</span><span class="sxs-lookup"><span data-stu-id="795b3-132">If you lock a row for read-only access, others can also lock it for read-only access but not for update.</span></span>

<span data-ttu-id="795b3-133">Zarządzanie blokadami ma wady.</span><span class="sxs-lookup"><span data-stu-id="795b3-133">Managing locks has disadvantages.</span></span> <span data-ttu-id="795b3-134">Może być skomplikowany dla programu.</span><span class="sxs-lookup"><span data-stu-id="795b3-134">It can be complex to program.</span></span> <span data-ttu-id="795b3-135">Wymaga to znaczących zasobów zarządzania bazami danych. może to spowodować problemy z wydajnością w miarę zwiększania się liczby użytkowników aplikacji.</span><span class="sxs-lookup"><span data-stu-id="795b3-135">It requires significant database management resources, and it can cause performance problems as the number of users of an application increases.</span></span> <span data-ttu-id="795b3-136">Z tego względu nie wszystkie systemy zarządzania bazami danych obsługują pesymistyczne współbieżności.</span><span class="sxs-lookup"><span data-stu-id="795b3-136">For these reasons, not all database management systems support pessimistic concurrency.</span></span> <span data-ttu-id="795b3-137">Entity Framework Core nie zapewnia wbudowanej pomocy technicznej i ten samouczek nie pokazuje, jak wdrożyć go.</span><span class="sxs-lookup"><span data-stu-id="795b3-137">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show you how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="795b3-138">Optymistyczna współbieżność</span><span class="sxs-lookup"><span data-stu-id="795b3-138">Optimistic Concurrency</span></span>

<span data-ttu-id="795b3-139">Alternatywą dla pesymistycznej współbieżności jest Optymistyczna współbieżność.</span><span class="sxs-lookup"><span data-stu-id="795b3-139">The alternative to pessimistic concurrency is optimistic concurrency.</span></span> <span data-ttu-id="795b3-140">Współbieżność optymistyczna pozwala na wykonywanie konfliktów współbieżności, a następnie podejmowanie odpowiednich działań.</span><span class="sxs-lookup"><span data-stu-id="795b3-140">Optimistic concurrency means allowing concurrency conflicts to happen, and then reacting appropriately if they do.</span></span> <span data-ttu-id="795b3-141">Na przykład Joanna odwiedzi stronę Edycja działu i zmieni kwotę budżetu dla działu angielskiego z $350 000,00 na $0,00.</span><span class="sxs-lookup"><span data-stu-id="795b3-141">For example, Jane visits the Department Edit page and changes the Budget amount for the English department from $350,000.00 to $0.00.</span></span>

![Zmiana wartości budżetu na 0](concurrency/_static/change-budget.png)

<span data-ttu-id="795b3-143">Przed Janem kliknie przycisk **Zapisz**, Jan odwiedzi tę samą stronę i zmieni pole Data rozpoczęcia z 9/1/2007 na 9/1/2013.</span><span class="sxs-lookup"><span data-stu-id="795b3-143">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Zmiana daty rozpoczęcia na 2013](concurrency/_static/change-date.png)

<span data-ttu-id="795b3-145">Jan klika pozycję **Zapisz** jako pierwszy i widzi zmiany, gdy przeglądarka powraca do strony indeks.</span><span class="sxs-lookup"><span data-stu-id="795b3-145">Jane clicks **Save** first and sees her change when the browser returns to the Index page.</span></span>

![Budżet zmieniony na zero](concurrency/_static/budget-zero.png)

<span data-ttu-id="795b3-147">Następnie Jan klika pozycję **Zapisz** na stronie edytowania, która nadal zawiera budżet $350 000,00.</span><span class="sxs-lookup"><span data-stu-id="795b3-147">Then John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="795b3-148">Co się stanie dalej, zależy od sposobu obsługi konfliktów współbieżności.</span><span class="sxs-lookup"><span data-stu-id="795b3-148">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="795b3-149">Dostępne są następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="795b3-149">Some of the options include the following:</span></span>

* <span data-ttu-id="795b3-150">Można śledzić, która właściwość została zmodyfikowana przez użytkownika i zaktualizować tylko odpowiednie kolumny w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="795b3-150">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

     <span data-ttu-id="795b3-151">W przykładowym scenariuszu żadne dane nie zostaną utracone, ponieważ różne właściwości zostały zaktualizowane przez dwóch użytkowników.</span><span class="sxs-lookup"><span data-stu-id="795b3-151">In the example scenario, no data would be lost, because different properties were updated by the two users.</span></span> <span data-ttu-id="795b3-152">Następnym razem, gdy ktoś przegląda ten dział w języku angielskim, zobaczy zmiany w kategorii Janina i Jan — datę początkową 9/1/2013 i budżet zerowych dolarów.</span><span class="sxs-lookup"><span data-stu-id="795b3-152">The next time someone browses the English department, they will see both Jane's and John's changes -- a start date of 9/1/2013 and a budget of zero dollars.</span></span> <span data-ttu-id="795b3-153">Ta metoda aktualizacji może zmniejszyć liczbę konfliktów, które mogłyby spowodować utratę danych, ale nie może uniknąć utraty danych, jeśli wprowadzono konkurencyjne zmiany w tej samej właściwości jednostki.</span><span class="sxs-lookup"><span data-stu-id="795b3-153">This method of updating can reduce the number of conflicts that could result in data loss, but it can't avoid data loss if competing changes are made to the same property of an entity.</span></span> <span data-ttu-id="795b3-154">Czy Entity Framework działa w ten sposób, zależy od sposobu implementacji kodu aktualizacji.</span><span class="sxs-lookup"><span data-stu-id="795b3-154">Whether the Entity Framework works this way depends on how you implement your update code.</span></span> <span data-ttu-id="795b3-155">Często nie jest to praktyczne w aplikacji sieci Web, ponieważ może wymagać utrzymania dużej ilości danych w celu śledzenia wszystkich oryginalnych wartości właściwości dla jednostki, a także nowych wartości.</span><span class="sxs-lookup"><span data-stu-id="795b3-155">It's often not practical in a web application, because it can require that you maintain large amounts of state in order to keep track of all original property values for an entity as well as new values.</span></span> <span data-ttu-id="795b3-156">Obsługa dużych ilości Stanów może wpłynąć na wydajność aplikacji, ponieważ wymaga zasobów serwera lub musi być uwzględniona na stronie sieci Web (na przykład w ukrytych polach) lub w pliku cookie.</span><span class="sxs-lookup"><span data-stu-id="795b3-156">Maintaining large amounts of state can affect application performance because it either requires server resources or must be included in the web page itself (for example, in hidden fields) or in a cookie.</span></span>

* <span data-ttu-id="795b3-157">Możesz pozwolić, aby zmiana została zastąpiona przez Jan.</span><span class="sxs-lookup"><span data-stu-id="795b3-157">You can let John's change overwrite Jane's change.</span></span>

     <span data-ttu-id="795b3-158">Następnym razem, gdy ktoś przegląda dział w języku angielskim, zobaczy 9/1/2013 i przywrócona wartość $350 000,00.</span><span class="sxs-lookup"><span data-stu-id="795b3-158">The next time someone browses the English department, they will see 9/1/2013 and the restored $350,000.00 value.</span></span> <span data-ttu-id="795b3-159">Jest to tzw. *klient WINS* lub *ostatni w scenariuszu usługi WINS* .</span><span class="sxs-lookup"><span data-stu-id="795b3-159">This is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="795b3-160">(Wszystkie wartości z klienta mają pierwszeństwo przed tym, co znajduje się w magazynie danych). Jak zostało to opisane we wprowadzeniu do tej sekcji, jeśli nie wykonasz kodowania na potrzeby obsługi współbieżności, zostanie to wykonane automatycznie.</span><span class="sxs-lookup"><span data-stu-id="795b3-160">(All values from the client take precedence over what's in the data store.) As noted in the introduction to this section, if you don't do any coding for concurrency handling, this will happen automatically.</span></span>

* <span data-ttu-id="795b3-161">Można zapobiec aktualizacji firmy Jan ze zmian w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="795b3-161">You can prevent John's change from being updated in the database.</span></span>

     <span data-ttu-id="795b3-162">Zwykle zostanie wyświetlony komunikat o błędzie, wyświetlenie go w bieżącym stanie danych i umożliwienie mu ponownego zastosowania zmian w przypadku, gdy nadal chce je wprowadzić.</span><span class="sxs-lookup"><span data-stu-id="795b3-162">Typically, you would display an error message, show him the current state of the data, and allow him to reapply his changes if he still wants to make them.</span></span> <span data-ttu-id="795b3-163">Jest to tzw. scenariusz *magazynu usługi WINS* .</span><span class="sxs-lookup"><span data-stu-id="795b3-163">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="795b3-164">(Wartości ze sklepu danych mają pierwszeństwo przed wartościami przesyłanymi przez klienta). W tym samouczku zostanie wdrożony scenariusz sklepu WINS.</span><span class="sxs-lookup"><span data-stu-id="795b3-164">(The data-store values take precedence over the values submitted by the client.) You'll implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="795b3-165">Ta metoda zapewnia, że żadne zmiany nie są zastępowane bez alertu użytkownika o tym, co się dzieje.</span><span class="sxs-lookup"><span data-stu-id="795b3-165">This method ensures that no changes are overwritten without a user being alerted to what's happening.</span></span>

### <a name="detecting-concurrency-conflicts"></a><span data-ttu-id="795b3-166">Wykrywanie konfliktów współbieżności</span><span class="sxs-lookup"><span data-stu-id="795b3-166">Detecting concurrency conflicts</span></span>

<span data-ttu-id="795b3-167">Konflikty można rozwiązać przez obsługę `DbConcurrencyException` wyjątków zgłaszanych przez Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="795b3-167">You can resolve conflicts by handling `DbConcurrencyException` exceptions that the Entity Framework throws.</span></span> <span data-ttu-id="795b3-168">Aby dowiedzieć się, kiedy należy zgłosić te wyjątki, Entity Framework musi mieć możliwość wykrywania konfliktów.</span><span class="sxs-lookup"><span data-stu-id="795b3-168">In order to know when to throw these exceptions, the Entity Framework must be able to detect conflicts.</span></span> <span data-ttu-id="795b3-169">W związku z tym należy odpowiednio skonfigurować bazę danych i model danych.</span><span class="sxs-lookup"><span data-stu-id="795b3-169">Therefore, you must configure the database and the data model appropriately.</span></span> <span data-ttu-id="795b3-170">Dostępne są następujące opcje włączania wykrywania konfliktów:</span><span class="sxs-lookup"><span data-stu-id="795b3-170">Some options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="795b3-171">W tabeli bazy danych Dołącz kolumnę śledzenia, której można użyć do określenia, kiedy wiersz został zmieniony.</span><span class="sxs-lookup"><span data-stu-id="795b3-171">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span> <span data-ttu-id="795b3-172">Następnie można skonfigurować Entity Framework, aby uwzględnić tę kolumnę w klauzuli WHERE polecenia SQL Update lub DELETE.</span><span class="sxs-lookup"><span data-stu-id="795b3-172">You can then configure the Entity Framework to include that column in the Where clause of SQL Update or Delete commands.</span></span>

     <span data-ttu-id="795b3-173">Typ danych kolumny śledzenia jest zwykle `rowversion` .</span><span class="sxs-lookup"><span data-stu-id="795b3-173">The data type of the tracking column is typically `rowversion`.</span></span> <span data-ttu-id="795b3-174">`rowversion`Wartość jest kolejnym numerem, który jest zwiększany za każdym razem, gdy wiersz zostanie zaktualizowany.</span><span class="sxs-lookup"><span data-stu-id="795b3-174">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="795b3-175">W przypadku polecenia Update lub DELETE klauzula WHERE zawiera oryginalną wartość kolumny śledzenia (oryginalną wersję wiersza).</span><span class="sxs-lookup"><span data-stu-id="795b3-175">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version) .</span></span> <span data-ttu-id="795b3-176">Jeśli aktualizowany wiersz został zmieniony przez innego użytkownika, wartość w `rowversion` kolumnie jest inna niż oryginalna wartość, więc instrukcja UPDATE lub DELETE nie może znaleźć wiersza do zaktualizowania z powodu klauzuli WHERE.</span><span class="sxs-lookup"><span data-stu-id="795b3-176">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value, so the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="795b3-177">Gdy Entity Framework stwierdzi, że żadne wiersze nie zostały zaktualizowane przez polecenie Update lub Delete (oznacza to, że gdy liczba odnośnych wierszy wynosi zero), interpretuje to jako konflikt współbieżności.</span><span class="sxs-lookup"><span data-stu-id="795b3-177">When the Entity Framework finds that no rows have been updated by the Update or Delete command (that is, when the number of affected rows is zero), it interprets that as a concurrency conflict.</span></span>

* <span data-ttu-id="795b3-178">Skonfiguruj Entity Framework, aby uwzględnić oryginalne wartości każdej kolumny w tabeli w klauzuli WHERE poleceń Update i DELETE.</span><span class="sxs-lookup"><span data-stu-id="795b3-178">Configure the Entity Framework to include the original values of every column in the table in the Where clause of Update and Delete commands.</span></span>

     <span data-ttu-id="795b3-179">Jak w pierwszej opcji, jeśli coś w wierszu uległo zmianie od momentu pierwszego odczytu wiersza, klauzula WHERE nie zwraca wiersza do zaktualizowania, który Entity Framework interpretuje jako konflikt współbieżności.</span><span class="sxs-lookup"><span data-stu-id="795b3-179">As in the first option, if anything in the row has changed since the row was first read, the Where clause won't return a row to update, which the Entity Framework interprets as a concurrency conflict.</span></span> <span data-ttu-id="795b3-180">W przypadku tabel bazy danych z wieloma kolumnami takie podejście może skutkować bardzo dużymi klauzulami WHERE i może wymagać utrzymania dużej ilości danych.</span><span class="sxs-lookup"><span data-stu-id="795b3-180">For database tables that have many columns, this approach can result in very large Where clauses, and can require that you maintain large amounts of state.</span></span> <span data-ttu-id="795b3-181">Jak wspomniano wcześniej, utrzymanie dużej ilości stanu może wpłynąć na wydajność aplikacji.</span><span class="sxs-lookup"><span data-stu-id="795b3-181">As noted earlier, maintaining large amounts of state can affect application performance.</span></span> <span data-ttu-id="795b3-182">W związku z tym takie podejście zwykle nie jest zalecane i nie jest to metoda używana w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="795b3-182">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

     <span data-ttu-id="795b3-183">Jeśli chcesz zaimplementować to podejście do współbieżności, musisz oznaczyć wszystkie właściwości klucza niepodstawowego w jednostce, dla której chcesz śledzić współbieżność, dodając `ConcurrencyCheck` do nich atrybut.</span><span class="sxs-lookup"><span data-stu-id="795b3-183">If you do want to implement this approach to concurrency, you have to mark all non-primary-key properties in the entity you want to track concurrency for by adding the `ConcurrencyCheck` attribute to them.</span></span> <span data-ttu-id="795b3-184">Ta zmiana umożliwia Entity Framework uwzględnienie wszystkich kolumn w klauzuli SQL WHERE instrukcji UPDATE i DELETE.</span><span class="sxs-lookup"><span data-stu-id="795b3-184">That change enables the Entity Framework to include all columns in the SQL Where clause of Update and Delete statements.</span></span>

<span data-ttu-id="795b3-185">W pozostałej części tego samouczka dodasz `rowversion` Właściwość śledzenia do jednostki działu, utworzysz kontroler i widoki i testujesz, aby sprawdzić, czy wszystko działa prawidłowo.</span><span class="sxs-lookup"><span data-stu-id="795b3-185">In the remainder of this tutorial you'll add a `rowversion` tracking property to the Department entity, create a controller and views, and test to verify that everything works correctly.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="795b3-186">Dodaj właściwość śledzenia</span><span class="sxs-lookup"><span data-stu-id="795b3-186">Add a tracking property</span></span>

<span data-ttu-id="795b3-187">W obszarze *modele/dział. cs*Dodaj właściwość śledzenia o nazwie rowversion:</span><span class="sxs-lookup"><span data-stu-id="795b3-187">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="795b3-188">Ten `Timestamp` atrybut określa, że ta kolumna zostanie uwzględniona w klauzuli WHERE poleceń Update i DELETE wysyłanych do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="795b3-188">The `Timestamp` attribute specifies that this column will be included in the Where clause of Update and Delete commands sent to the database.</span></span> <span data-ttu-id="795b3-189">Ten atrybut jest wywoływany, `Timestamp` ponieważ poprzednie wersje SQL Server używały `timestamp` typu danych SQL przed zastąpieniem go przez program SQL Server `rowversion` .</span><span class="sxs-lookup"><span data-stu-id="795b3-189">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` replaced it.</span></span> <span data-ttu-id="795b3-190">Typ .NET dla `rowversion` jest tablicą bajtów.</span><span class="sxs-lookup"><span data-stu-id="795b3-190">The .NET type for `rowversion` is a byte array.</span></span>

<span data-ttu-id="795b3-191">Jeśli wolisz używać interfejsu API Fluent, możesz użyć `IsConcurrencyToken` metody (w *danych/SchoolContext. cs*), aby określić właściwość śledzenia, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="795b3-191">If you prefer to use the fluent API, you can use the `IsConcurrencyToken` method (in *Data/SchoolContext.cs*) to specify the tracking property, as shown in the following example:</span></span>

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

<span data-ttu-id="795b3-192">Przez dodanie właściwości, która zmieniła model bazy danych, należy wykonać kolejną migrację.</span><span class="sxs-lookup"><span data-stu-id="795b3-192">By adding a property you changed the database model, so you need to do another migration.</span></span>

<span data-ttu-id="795b3-193">Zapisz zmiany i skompiluj projekt, a następnie wprowadź następujące polecenia w oknie polecenia:</span><span class="sxs-lookup"><span data-stu-id="795b3-193">Save your changes and build the project, and then enter the following commands in the command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
```

```dotnetcli
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a><span data-ttu-id="795b3-194">Tworzenie kontrolera i widoków działów</span><span class="sxs-lookup"><span data-stu-id="795b3-194">Create Departments controller and views</span></span>

<span data-ttu-id="795b3-195">Tworzy szkielet na kontrolerze i widokach działów jak wcześniej dla studentów, kursów i instruktorów.</span><span class="sxs-lookup"><span data-stu-id="795b3-195">Scaffold a Departments controller and views as you did earlier for Students, Courses, and Instructors.</span></span>

![Dział szkieletu](concurrency/_static/add-departments-controller.png)

<span data-ttu-id="795b3-197">W pliku *DepartmentsController.cs* Zmień wszystkie cztery wystąpienia elementu "FirstMidName" na "FullName", dzięki czemu listy rozwijane administrator działu będą zawierać pełną nazwę instruktora, a nie tylko nazwisko.</span><span class="sxs-lookup"><span data-stu-id="795b3-197">In the *DepartmentsController.cs* file, change all four occurrences of "FirstMidName" to "FullName" so that the department administrator drop-down lists will contain the full name of the instructor rather than just the last name.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a><span data-ttu-id="795b3-198">Aktualizuj widok indeksu</span><span class="sxs-lookup"><span data-stu-id="795b3-198">Update Index view</span></span>

<span data-ttu-id="795b3-199">Aparat tworzenia szkieletów utworzył kolumnę RowVersion w widoku indeks, ale to pole nie powinno być wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="795b3-199">The scaffolding engine created a RowVersion column in the Index view, but that field shouldn't be displayed.</span></span>

<span data-ttu-id="795b3-200">Zastąp kod w *widokach/działach/index. cshtml* następującym kodem.</span><span class="sxs-lookup"><span data-stu-id="795b3-200">Replace the code in *Views/Departments/Index.cshtml* with the following code.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

<span data-ttu-id="795b3-201">Spowoduje to zmianę nagłówka na "działy", usunięcie kolumny RowVersion i wyświetlenie pełnej nazwy zamiast imienia administratora.</span><span class="sxs-lookup"><span data-stu-id="795b3-201">This changes the heading to "Departments", deletes the RowVersion column, and shows full name instead of first name for the administrator.</span></span>

## <a name="update-edit-methods"></a><span data-ttu-id="795b3-202">Aktualizowanie metod edycji</span><span class="sxs-lookup"><span data-stu-id="795b3-202">Update Edit methods</span></span>

<span data-ttu-id="795b3-203">W `Edit` metodzie narzędzia HttpGet i `Details` metodzie Dodaj `AsNoTracking` .</span><span class="sxs-lookup"><span data-stu-id="795b3-203">In both the HttpGet `Edit` method and the `Details` method, add `AsNoTracking`.</span></span> <span data-ttu-id="795b3-204">W `Edit` metodzie narzędzia HttpGet Dodaj eager ładowania dla administratora.</span><span class="sxs-lookup"><span data-stu-id="795b3-204">In the HttpGet `Edit` method, add eager loading for the Administrator.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="795b3-205">Zastąp istniejący kod `Edit` metody HTTPPOST następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="795b3-205">Replace the existing code for the HttpPost `Edit` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

<span data-ttu-id="795b3-206">Kod rozpoczyna się od próby odczytu działu do zaktualizowania.</span><span class="sxs-lookup"><span data-stu-id="795b3-206">The code begins by trying to read the department to be updated.</span></span> <span data-ttu-id="795b3-207">Jeśli `FirstOrDefaultAsync` Metoda zwraca wartość null, dział został usunięty przez innego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="795b3-207">If the `FirstOrDefaultAsync` method returns null, the department was deleted by another user.</span></span> <span data-ttu-id="795b3-208">W takim przypadku kod używa opublikowanych wartości formularza do utworzenia jednostki działu, aby można było ponownie wyświetlić stronę edytowania z komunikatem o błędzie.</span><span class="sxs-lookup"><span data-stu-id="795b3-208">In that case the code uses the posted form values to create a department entity so that the Edit page can be redisplayed with an error message.</span></span> <span data-ttu-id="795b3-209">Alternatywnie nie trzeba ponownie tworzyć jednostki działu, jeśli zostanie wyświetlony komunikat o błędzie bez ponownego wyświetlania pól działu.</span><span class="sxs-lookup"><span data-stu-id="795b3-209">As an alternative, you wouldn't have to re-create the department entity if you display only an error message without redisplaying the department fields.</span></span>

<span data-ttu-id="795b3-210">Widok przechowuje oryginalną `RowVersion` wartość w ukrytym polu, a ta metoda otrzymuje tę wartość w `rowVersion` parametrze.</span><span class="sxs-lookup"><span data-stu-id="795b3-210">The view stores the original `RowVersion` value in a hidden field, and this method receives that value in the `rowVersion` parameter.</span></span> <span data-ttu-id="795b3-211">Przed wywołaniem należy `SaveChanges` umieścić tę oryginalną `RowVersion` wartość właściwości w `OriginalValues` kolekcji dla jednostki.</span><span class="sxs-lookup"><span data-stu-id="795b3-211">Before you call `SaveChanges`, you have to put that original `RowVersion` property value in the `OriginalValues` collection for the entity.</span></span>

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

<span data-ttu-id="795b3-212">Następnie, gdy Entity Framework tworzy polecenie SQL UPDATE, to polecenie będzie zawierać klauzulę WHERE, która szuka wiersza, który ma oryginalną `RowVersion` wartość.</span><span class="sxs-lookup"><span data-stu-id="795b3-212">Then when the Entity Framework creates a SQL UPDATE command, that command will include a WHERE clause that looks for a row that has the original `RowVersion` value.</span></span> <span data-ttu-id="795b3-213">Jeśli nie ma żadnych wierszy, których dotyczy polecenie aktualizacji (żadne wiersze nie mają oryginalnej `RowVersion` wartości), Entity Framework zgłasza `DbUpdateConcurrencyException` wyjątek.</span><span class="sxs-lookup"><span data-stu-id="795b3-213">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value),  the Entity Framework throws a `DbUpdateConcurrencyException` exception.</span></span>

<span data-ttu-id="795b3-214">Kod w bloku catch dla tego wyjątku pobiera jednostkę działu, której dotyczy usterka, która ma zaktualizowane wartości z `Entries` właściwości obiektu wyjątku.</span><span class="sxs-lookup"><span data-stu-id="795b3-214">The code in the catch block for that exception gets the affected Department entity that has the updated values from the `Entries` property on the exception object.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

<span data-ttu-id="795b3-215">`Entries`Kolekcja będzie zawierać tylko jeden `EntityEntry` obiekt.</span><span class="sxs-lookup"><span data-stu-id="795b3-215">The `Entries` collection will have just one `EntityEntry` object.</span></span>  <span data-ttu-id="795b3-216">Można użyć tego obiektu, aby pobrać nowe wartości wprowadzone przez użytkownika i bieżące wartości bazy danych.</span><span class="sxs-lookup"><span data-stu-id="795b3-216">You can use that object to get the new values entered by the user and the current database values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

<span data-ttu-id="795b3-217">Kod dodaje niestandardowy komunikat o błędzie dla każdej kolumny, która ma wartości bazy danych inne niż wprowadzone przez użytkownika na stronie edytowania (w tym miejscu jest wyświetlane tylko jedno pole dla zwięzłości).</span><span class="sxs-lookup"><span data-stu-id="795b3-217">The code adds a custom error message for each column that has database values different from what the user entered on the Edit page (only one field is shown here for brevity).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

<span data-ttu-id="795b3-218">Na koniec kod ustawia `RowVersion` wartość `departmentToUpdate` do nowej wartości pobranej z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="795b3-218">Finally, the code sets the `RowVersion` value of the `departmentToUpdate` to the new value retrieved from the database.</span></span> <span data-ttu-id="795b3-219">Ta nowa `RowVersion` wartość zostanie zapisana w ukrytym polu po ponownym wyświetleniu strony edycji, a przy następnym kliknięciu przycisku **Zapisz**zostaną przechwycone tylko błędy współbieżności, które zachodzą od momentu wyświetlenia ekranu edycji.</span><span class="sxs-lookup"><span data-stu-id="795b3-219">This new `RowVersion` value will be stored in the hidden field when the Edit page is redisplayed, and the next time the user clicks **Save**, only concurrency errors that happen since the redisplay of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

<span data-ttu-id="795b3-220">`ModelState.Remove`Instrukcja jest wymagana, ponieważ `ModelState` ma starą `RowVersion` wartość.</span><span class="sxs-lookup"><span data-stu-id="795b3-220">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="795b3-221">W widoku `ModelState` wartość pola ma pierwszeństwo przed wartościami właściwości modelu, gdy są obecne oba typy.</span><span class="sxs-lookup"><span data-stu-id="795b3-221">In the view, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-edit-view"></a><span data-ttu-id="795b3-222">Aktualizuj widok edycji</span><span class="sxs-lookup"><span data-stu-id="795b3-222">Update Edit view</span></span>

<span data-ttu-id="795b3-223">W obszarze *widoki/działy/Edit. cshtml*wprowadź następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="795b3-223">In *Views/Departments/Edit.cshtml*, make the following changes:</span></span>

* <span data-ttu-id="795b3-224">Dodaj ukryte pole, aby zapisać `RowVersion` wartość właściwości, bezpośrednio po ukrytym polu `DepartmentID` właściwości.</span><span class="sxs-lookup"><span data-stu-id="795b3-224">Add a hidden field to save the `RowVersion` property value, immediately following the hidden field for the `DepartmentID` property.</span></span>

* <span data-ttu-id="795b3-225">Dodaj opcję "Wybierz administratora" do listy rozwijanej.</span><span class="sxs-lookup"><span data-stu-id="795b3-225">Add a "Select Administrator" option to the drop-down list.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a><span data-ttu-id="795b3-226">Testuj konflikty współbieżności</span><span class="sxs-lookup"><span data-stu-id="795b3-226">Test concurrency conflicts</span></span>

<span data-ttu-id="795b3-227">Uruchom aplikację i przejdź do strony indeks działów.</span><span class="sxs-lookup"><span data-stu-id="795b3-227">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="795b3-228">Kliknij prawym przyciskiem myszy hiperłącze **Edytuj** dla działu angielskiego i wybierz polecenie **Otwórz na nowej karcie**, a następnie kliknij hiperłącze **Edytuj** dla działu angielskiego.</span><span class="sxs-lookup"><span data-stu-id="795b3-228">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**, then click the **Edit** hyperlink for the English department.</span></span> <span data-ttu-id="795b3-229">Dwie karty przeglądarki zawierają teraz te same informacje.</span><span class="sxs-lookup"><span data-stu-id="795b3-229">The two browser tabs now display the same information.</span></span>

<span data-ttu-id="795b3-230">Zmień pole na pierwszej karcie przeglądarki, a następnie kliknij przycisk **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="795b3-230">Change a field in the first browser tab and click **Save**.</span></span>

![Edycja działu Strona 1 po zmianie](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="795b3-232">W przeglądarce zostanie wyświetlona strona indeks o zmienionej wartości.</span><span class="sxs-lookup"><span data-stu-id="795b3-232">The browser shows the Index page with the changed value.</span></span>

<span data-ttu-id="795b3-233">Zmień pole na drugiej karcie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="795b3-233">Change a field in the second browser tab.</span></span>

![Edycja działu Strona 2 po zmianie](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="795b3-235">Kliknij pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="795b3-235">Click **Save**.</span></span> <span data-ttu-id="795b3-236">Zostanie wyświetlony komunikat o błędzie:</span><span class="sxs-lookup"><span data-stu-id="795b3-236">You see an error message:</span></span>

![Komunikat o błędzie strony edytowania działu](concurrency/_static/edit-error.png)

<span data-ttu-id="795b3-238">Kliknij przycisk **Zapisz** ponownie.</span><span class="sxs-lookup"><span data-stu-id="795b3-238">Click **Save** again.</span></span> <span data-ttu-id="795b3-239">Wartość wprowadzona na drugiej karcie przeglądarki jest zapisywana.</span><span class="sxs-lookup"><span data-stu-id="795b3-239">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="795b3-240">Zapisane wartości są wyświetlane, gdy zostanie wyświetlona strona indeks.</span><span class="sxs-lookup"><span data-stu-id="795b3-240">You see the saved values when the Index page appears.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="795b3-241">Aktualizowanie strony usuwania</span><span class="sxs-lookup"><span data-stu-id="795b3-241">Update the Delete page</span></span>

<span data-ttu-id="795b3-242">Na stronie Usuwanie Entity Framework wykrywa konflikty współbieżności spowodowane przez inną osobę edytującą dział w podobny sposób.</span><span class="sxs-lookup"><span data-stu-id="795b3-242">For the Delete page, the Entity Framework detects concurrency conflicts caused by someone else editing the department in a similar manner.</span></span> <span data-ttu-id="795b3-243">Gdy metoda narzędzia HttpGet `Delete` wyświetla widok potwierdzenia, widok zawiera oryginalną `RowVersion` wartość w ukrytym polu.</span><span class="sxs-lookup"><span data-stu-id="795b3-243">When the HttpGet `Delete` method displays the confirmation view, the view includes the original `RowVersion` value in a hidden field.</span></span> <span data-ttu-id="795b3-244">Ta wartość jest następnie dostępna dla metody HttpPost `Delete` , która jest wywoływana, gdy użytkownik potwierdzi usunięcie.</span><span class="sxs-lookup"><span data-stu-id="795b3-244">That value is then available to the HttpPost `Delete` method that's called when the user confirms the deletion.</span></span> <span data-ttu-id="795b3-245">Gdy Entity Framework tworzy polecenie SQL DELETE, zawiera klauzulę WHERE z oryginalną `RowVersion` wartością.</span><span class="sxs-lookup"><span data-stu-id="795b3-245">When the Entity Framework creates the SQL DELETE command, it includes a WHERE clause with the original `RowVersion` value.</span></span> <span data-ttu-id="795b3-246">Jeśli w poleceniu wyniki są równe zero (oznacza to, że wiersz został zmieniony po wyświetleniu strony potwierdzenia usunięcia), zgłaszany jest wyjątek współbieżności, a `Delete` Metoda narzędzia HttpGet jest wywoływana z flagą błędu ustawioną na wartość true, aby ponownie wyświetlić stronę potwierdzenia z komunikatem o błędzie.</span><span class="sxs-lookup"><span data-stu-id="795b3-246">If the command results in zero rows affected (meaning the row was changed after the Delete confirmation page was displayed), a concurrency exception is thrown, and the HttpGet `Delete` method is called with an error flag set to true in order to redisplay the confirmation page with an error message.</span></span> <span data-ttu-id="795b3-247">Istnieje również możliwość, że zero wierszy miało wpływ, ponieważ wiersz został usunięty przez innego użytkownika, więc w takim przypadku nie jest wyświetlany komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="795b3-247">It's also possible that zero rows were affected because the row was deleted by another user, so in that case no error message is displayed.</span></span>

### <a name="update-the-delete-methods-in-the-departments-controller"></a><span data-ttu-id="795b3-248">Aktualizowanie metod DELETE w kontrolerze działu</span><span class="sxs-lookup"><span data-stu-id="795b3-248">Update the Delete methods in the Departments controller</span></span>

<span data-ttu-id="795b3-249">W *DepartmentsController.cs*Zastąp metodę narzędzia HttpGet `Delete` następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="795b3-249">In *DepartmentsController.cs*, replace the HttpGet `Delete` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

<span data-ttu-id="795b3-250">Metoda przyjmuje opcjonalny parametr, który wskazuje, czy strona jest ponownie wyświetlana po wystąpieniu błędu współbieżności.</span><span class="sxs-lookup"><span data-stu-id="795b3-250">The method accepts an optional parameter that indicates whether the page is being redisplayed after a concurrency error.</span></span> <span data-ttu-id="795b3-251">Jeśli flaga ma wartość true, a określony dział już nie istnieje, został usunięty przez innego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="795b3-251">If this flag is true and the department specified no longer exists, it was deleted by another user.</span></span> <span data-ttu-id="795b3-252">W takim przypadku kod przekieruje się do strony indeksu.</span><span class="sxs-lookup"><span data-stu-id="795b3-252">In that case, the code redirects to the Index page.</span></span>  <span data-ttu-id="795b3-253">Jeśli ta flaga ma wartość true, a dział istnieje, został zmieniony przez innego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="795b3-253">If this flag is true and the Department does exist, it was changed by another user.</span></span> <span data-ttu-id="795b3-254">W takim przypadku kod wysyła komunikat o błędzie do widoku przy użyciu `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="795b3-254">In that case, the code sends an error message to the view using `ViewData`.</span></span>

<span data-ttu-id="795b3-255">Zastąp kod w `Delete` metodzie HTTPPOST (o nazwie `DeleteConfirmed` ) następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="795b3-255">Replace the code in the HttpPost `Delete` method (named `DeleteConfirmed`) with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

<span data-ttu-id="795b3-256">W kodzie szkieletowym, który właśnie został zastąpiony, ta metoda akceptuje tylko identyfikator rekordu:</span><span class="sxs-lookup"><span data-stu-id="795b3-256">In the scaffolded code that you just replaced, this method accepted only a record ID:</span></span>

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

<span data-ttu-id="795b3-257">Ten parametr został zmieniony do wystąpienia jednostki działu utworzonego przez spinacz modelu.</span><span class="sxs-lookup"><span data-stu-id="795b3-257">You've changed this parameter to a Department entity instance created by the model binder.</span></span> <span data-ttu-id="795b3-258">Zapewnia to EF dostęp do wartości właściwości RowVersion oprócz klucza rekordu.</span><span class="sxs-lookup"><span data-stu-id="795b3-258">This gives EF access to the RowVersion property value in addition to the record key.</span></span>

```csharp
public async Task<IActionResult> Delete(Department department)
```

<span data-ttu-id="795b3-259">Zmieniono także nazwę metody akcji z `DeleteConfirmed` na `Delete` .</span><span class="sxs-lookup"><span data-stu-id="795b3-259">You have also changed the action method name from `DeleteConfirmed` to `Delete`.</span></span> <span data-ttu-id="795b3-260">Kod szkieletu użył nazwy, `DeleteConfirmed` Aby nadać metodzie HTTPPOST unikatową sygnaturę.</span><span class="sxs-lookup"><span data-stu-id="795b3-260">The scaffolded code used the name `DeleteConfirmed` to give the HttpPost method a unique signature.</span></span> <span data-ttu-id="795b3-261">(Środowisko CLR wymaga przeciążonych metod, aby mieć inne parametry metody). Teraz, gdy podpisy są unikatowe, można naklejić do Konwencji MVC i używać tej samej nazwy dla metod Delete HttpPost i narzędzia HttpGet.</span><span class="sxs-lookup"><span data-stu-id="795b3-261">(The CLR requires overloaded methods to have different method parameters.) Now that the signatures are unique, you can stick with the MVC convention and use the same name for the HttpPost and HttpGet delete methods.</span></span>

<span data-ttu-id="795b3-262">Jeśli dział został już usunięty, `AnyAsync` Metoda zwraca wartość false, a aplikacja po prostu wraca do metody index.</span><span class="sxs-lookup"><span data-stu-id="795b3-262">If the department is already deleted, the `AnyAsync` method returns false and the application just goes back to the Index method.</span></span>

<span data-ttu-id="795b3-263">W przypadku przechwyconego błędu współbieżności kod ponownie wyświetla stronę potwierdzenia usuwania i zawiera flagę wskazującą, że powinien zostać wyświetlony komunikat o błędzie współbieżności.</span><span class="sxs-lookup"><span data-stu-id="795b3-263">If a concurrency error is caught, the code redisplays the Delete confirmation page and provides a flag that indicates it should display a concurrency error message.</span></span>

### <a name="update-the-delete-view"></a><span data-ttu-id="795b3-264">Aktualizowanie widoku usuwania</span><span class="sxs-lookup"><span data-stu-id="795b3-264">Update the Delete view</span></span>

<span data-ttu-id="795b3-265">W obszarze *widoki/działy/Delete. cshtml*Zamień kod szkieletowy na następujący kod, który dodaje pole komunikatu o błędzie i ukryte pola dla właściwości DepartmentID i rowversion.</span><span class="sxs-lookup"><span data-stu-id="795b3-265">In *Views/Departments/Delete.cshtml*, replace the scaffolded code with the following code that adds an error message field and hidden fields for the DepartmentID and RowVersion properties.</span></span> <span data-ttu-id="795b3-266">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="795b3-266">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

<span data-ttu-id="795b3-267">Powoduje to wprowadzenie następujących zmian:</span><span class="sxs-lookup"><span data-stu-id="795b3-267">This makes the following changes:</span></span>

* <span data-ttu-id="795b3-268">Dodaje komunikat o błędzie między `h2` nagłówki i `h3` .</span><span class="sxs-lookup"><span data-stu-id="795b3-268">Adds an error message between the `h2` and `h3` headings.</span></span>

* <span data-ttu-id="795b3-269">Zastępuje FirstMidName z FullName w polu **administrator** .</span><span class="sxs-lookup"><span data-stu-id="795b3-269">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>

* <span data-ttu-id="795b3-270">Usuwa pole RowVersion.</span><span class="sxs-lookup"><span data-stu-id="795b3-270">Removes the RowVersion field.</span></span>

* <span data-ttu-id="795b3-271">Dodaje ukryte pole dla `RowVersion` właściwości.</span><span class="sxs-lookup"><span data-stu-id="795b3-271">Adds a hidden field for the `RowVersion` property.</span></span>

<span data-ttu-id="795b3-272">Uruchom aplikację i przejdź do strony indeks działów.</span><span class="sxs-lookup"><span data-stu-id="795b3-272">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="795b3-273">Kliknij prawym przyciskiem myszy hiperłącze **Usuń** dla działu angielskiego i wybierz polecenie **Otwórz na nowej karcie**, a następnie na pierwszej karcie kliknij hiperłącze **Edytuj** dla działu angielskiego.</span><span class="sxs-lookup"><span data-stu-id="795b3-273">Right-click the **Delete** hyperlink for the English department and select **Open in new tab**, then in the first tab click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="795b3-274">W pierwszym oknie Zmień jedną z wartości, a następnie kliknij przycisk **Zapisz**:</span><span class="sxs-lookup"><span data-stu-id="795b3-274">In the first window, change one of the values, and click **Save**:</span></span>

![Strona Edycja działu po zmianie przed usunięciem](concurrency/_static/edit-after-change-for-delete.png)

<span data-ttu-id="795b3-276">Na drugiej karcie kliknij pozycję **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="795b3-276">In the second tab, click **Delete**.</span></span> <span data-ttu-id="795b3-277">Zobaczysz komunikat o błędzie współbieżności, a wartości działu są odświeżane z aktualną wartością w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="795b3-277">You see the concurrency error message, and the Department values are refreshed with what's currently in the database.</span></span>

![Strona potwierdzenia usunięcia działu z błędem współbieżności](concurrency/_static/delete-error.png)

<span data-ttu-id="795b3-279">Jeśli klikniesz przycisk **Usuń** ponownie, nastąpi przekierowanie do strony indeks, która pokazuje, że dział został usunięty.</span><span class="sxs-lookup"><span data-stu-id="795b3-279">If you click **Delete** again, you're redirected to the Index page, which shows that the department has been deleted.</span></span>

## <a name="update-details-and-create-views"></a><span data-ttu-id="795b3-280">Aktualizowanie szczegółów i Tworzenie widoków</span><span class="sxs-lookup"><span data-stu-id="795b3-280">Update Details and Create views</span></span>

<span data-ttu-id="795b3-281">Opcjonalnie można oczyścić kod szkieletowy w szczegółach i w widokach.</span><span class="sxs-lookup"><span data-stu-id="795b3-281">You can optionally clean up scaffolded code in the Details and Create views.</span></span>

<span data-ttu-id="795b3-282">Zastąp kod w *widokach/działach/details. cshtml* , aby usunąć kolumnę rowversion i wyświetlić pełną nazwę administratora.</span><span class="sxs-lookup"><span data-stu-id="795b3-282">Replace the code in *Views/Departments/Details.cshtml* to delete the RowVersion column and show the full name of the Administrator.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

<span data-ttu-id="795b3-283">Zastąp kod w *widokach/działach/Create. cshtml* , aby dodać opcję Select do listy rozwijanej.</span><span class="sxs-lookup"><span data-stu-id="795b3-283">Replace the code in *Views/Departments/Create.cshtml* to add a Select option to the drop-down list.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a><span data-ttu-id="795b3-284">Uzyskiwanie kodu</span><span class="sxs-lookup"><span data-stu-id="795b3-284">Get the code</span></span>

[<span data-ttu-id="795b3-285">Pobierz lub Wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="795b3-285">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="795b3-286">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="795b3-286">Additional resources</span></span>

 <span data-ttu-id="795b3-287">Aby uzyskać więcej informacji o sposobie obsługi współbieżności w EF Core, zobacz [konflikty współbieżności](/ef/core/saving/concurrency).</span><span class="sxs-lookup"><span data-stu-id="795b3-287">For more information about how to handle concurrency in EF Core, see [Concurrency conflicts](/ef/core/saving/concurrency).</span></span>

## <a name="next-steps"></a><span data-ttu-id="795b3-288">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="795b3-288">Next steps</span></span>

<span data-ttu-id="795b3-289">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="795b3-289">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="795b3-290">Informacje o konfliktach współbieżności</span><span class="sxs-lookup"><span data-stu-id="795b3-290">Learned about concurrency conflicts</span></span>
> * <span data-ttu-id="795b3-291">Dodano Właściwość śledzenia</span><span class="sxs-lookup"><span data-stu-id="795b3-291">Added a tracking property</span></span>
> * <span data-ttu-id="795b3-292">Utworzono kontroler i widok wydziałów</span><span class="sxs-lookup"><span data-stu-id="795b3-292">Created Departments controller and views</span></span>
> * <span data-ttu-id="795b3-293">Zaktualizowany widok indeksu</span><span class="sxs-lookup"><span data-stu-id="795b3-293">Updated Index view</span></span>
> * <span data-ttu-id="795b3-294">Zaktualizowane metody edycji</span><span class="sxs-lookup"><span data-stu-id="795b3-294">Updated Edit methods</span></span>
> * <span data-ttu-id="795b3-295">Zaktualizowano widok edycji</span><span class="sxs-lookup"><span data-stu-id="795b3-295">Updated Edit view</span></span>
> * <span data-ttu-id="795b3-296">Przetestowane konflikty współbieżności</span><span class="sxs-lookup"><span data-stu-id="795b3-296">Tested concurrency conflicts</span></span>
> * <span data-ttu-id="795b3-297">Zaktualizowano stronę usuwania</span><span class="sxs-lookup"><span data-stu-id="795b3-297">Updated the Delete page</span></span>
> * <span data-ttu-id="795b3-298">Zaktualizowane szczegóły i Tworzenie widoków</span><span class="sxs-lookup"><span data-stu-id="795b3-298">Updated Details and Create views</span></span>

<span data-ttu-id="795b3-299">Przejdź do następnego samouczka, aby dowiedzieć się, jak zaimplementować dziedziczenie na poziomie tabeli dla instruktorów i jednostek ucznia.</span><span class="sxs-lookup"><span data-stu-id="795b3-299">Advance to the next tutorial to learn how to implement table-per-hierarchy inheritance for the Instructor and Student entities.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="795b3-300">Dalej: Implementuj dziedziczenie w hierarchii na poziomie tabeli</span><span class="sxs-lookup"><span data-stu-id="795b3-300">Next: Implement table-per-hierarchy inheritance</span></span>](inheritance.md)
