---
title: 'Samouczek: Obsługa współbieżności - ASP.NET MVC z EF Core'
description: W tym samouczku pokazano, jak obsługiwać konflikty, gdy wielu użytkowników aktualizuje tę samą jednostkę w tym samym czasie.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/concurrency
ms.openlocfilehash: 6839e383093b993ff55095f26cf88cd68708f001
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657396"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a><span data-ttu-id="43d80-103">Samouczek: Obsługa współbieżności - ASP.NET MVC z EF Core</span><span class="sxs-lookup"><span data-stu-id="43d80-103">Tutorial: Handle concurrency - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="43d80-104">We wcześniejszych samouczkach dowiesz się, jak zaktualizować dane.</span><span class="sxs-lookup"><span data-stu-id="43d80-104">In earlier tutorials, you learned how to update data.</span></span> <span data-ttu-id="43d80-105">W tym samouczku pokazano, jak obsługiwać konflikty, gdy wielu użytkowników aktualizuje tę samą jednostkę w tym samym czasie.</span><span class="sxs-lookup"><span data-stu-id="43d80-105">This tutorial shows how to handle conflicts when multiple users update the same entity at the same time.</span></span>

<span data-ttu-id="43d80-106">Utworzysz strony sieci web, które współpracują z jednostką Dział i obsługują błędy współbieżności.</span><span class="sxs-lookup"><span data-stu-id="43d80-106">You'll create web pages that work with the Department entity and handle concurrency errors.</span></span> <span data-ttu-id="43d80-107">Na poniższych ilustracjach przedstawiono strony Edytowanie i usuwanie, w tym niektóre komunikaty, które są wyświetlane w przypadku wystąpienia konfliktu współbieżności.</span><span class="sxs-lookup"><span data-stu-id="43d80-107">The following illustrations show the Edit and Delete pages, including some messages that are displayed if a concurrency conflict occurs.</span></span>

![Strona Edycji działu](concurrency/_static/edit-error.png)

![Strona Usuwania działów](concurrency/_static/delete-error.png)

<span data-ttu-id="43d80-110">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="43d80-110">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="43d80-111">Dowiedz się więcej o konfliktach współbieżności</span><span class="sxs-lookup"><span data-stu-id="43d80-111">Learn about concurrency conflicts</span></span>
> * <span data-ttu-id="43d80-112">Dodawanie właściwości śledzenia</span><span class="sxs-lookup"><span data-stu-id="43d80-112">Add a tracking property</span></span>
> * <span data-ttu-id="43d80-113">Tworzenie kontrolera i widoków działów</span><span class="sxs-lookup"><span data-stu-id="43d80-113">Create Departments controller and views</span></span>
> * <span data-ttu-id="43d80-114">Aktualizuj widok indeksu</span><span class="sxs-lookup"><span data-stu-id="43d80-114">Update Index view</span></span>
> * <span data-ttu-id="43d80-115">Aktualizuj metody edycji</span><span class="sxs-lookup"><span data-stu-id="43d80-115">Update Edit methods</span></span>
> * <span data-ttu-id="43d80-116">Aktualizuj widok edycji</span><span class="sxs-lookup"><span data-stu-id="43d80-116">Update Edit view</span></span>
> * <span data-ttu-id="43d80-117">Testowanie konfliktów współbieżności</span><span class="sxs-lookup"><span data-stu-id="43d80-117">Test concurrency conflicts</span></span>
> * <span data-ttu-id="43d80-118">Aktualizowanie strony Usuwanie</span><span class="sxs-lookup"><span data-stu-id="43d80-118">Update the Delete page</span></span>
> * <span data-ttu-id="43d80-119">Aktualizowanie szczegółów i tworzenie widoków</span><span class="sxs-lookup"><span data-stu-id="43d80-119">Update Details and Create views</span></span>

## <a name="prerequisites"></a><span data-ttu-id="43d80-120">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="43d80-120">Prerequisites</span></span>

* [<span data-ttu-id="43d80-121">Aktualizowanie powiązanych danych</span><span class="sxs-lookup"><span data-stu-id="43d80-121">Update related data</span></span>](update-related-data.md)

## <a name="concurrency-conflicts"></a><span data-ttu-id="43d80-122">Konflikty współbieżności</span><span class="sxs-lookup"><span data-stu-id="43d80-122">Concurrency conflicts</span></span>

<span data-ttu-id="43d80-123">Konflikt współbieżności występuje, gdy jeden użytkownik wyświetla dane jednostki w celu jej edycji, a następnie inny użytkownik aktualizuje dane tej samej jednostki przed zapisaniem pierwszej zmiany użytkownika w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="43d80-123">A concurrency conflict occurs when one user displays an entity's data in order to edit it, and then another user updates the same entity's data before the first user's change is written to the database.</span></span> <span data-ttu-id="43d80-124">Jeśli nie włączysz wykrywania takich konfliktów, ten, kto aktualizuje bazę danych ostatnio, zastępuje zmiany innego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="43d80-124">If you don't enable the detection of such conflicts, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="43d80-125">W wielu aplikacjach to ryzyko jest dopuszczalne: jeśli istnieje kilka użytkowników lub kilka aktualizacji lub jeśli nie jest naprawdę krytyczne, jeśli niektóre zmiany są zastępowane, koszt programowania współbieżności może przeważyć korzyści.</span><span class="sxs-lookup"><span data-stu-id="43d80-125">In many applications, this risk is acceptable: if there are few users, or few updates, or if isn't really critical if some changes are overwritten, the cost of programming for concurrency might outweigh the benefit.</span></span> <span data-ttu-id="43d80-126">W takim przypadku nie trzeba skonfigurować aplikację do obsługi konfliktów współbieżności.</span><span class="sxs-lookup"><span data-stu-id="43d80-126">In that case, you don't have to configure the application to handle concurrency conflicts.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="43d80-127">Współbieżność pesymistyczna (blokowanie)</span><span class="sxs-lookup"><span data-stu-id="43d80-127">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="43d80-128">Jeśli aplikacja musi zapobiec przypadkowej utracie danych w scenariuszach współbieżności, jednym ze sposobów, aby to zrobić, jest użycie blokad bazy danych.</span><span class="sxs-lookup"><span data-stu-id="43d80-128">If your application does need to prevent accidental data loss in concurrency scenarios, one way to do that is to use database locks.</span></span> <span data-ttu-id="43d80-129">Jest to tak zwana współbieżność pesymistyczna.</span><span class="sxs-lookup"><span data-stu-id="43d80-129">This is called pessimistic concurrency.</span></span> <span data-ttu-id="43d80-130">Na przykład przed odczytaniem wiersza z bazy danych, należy zażądać blokady tylko do odczytu lub dostępu do aktualizacji.</span><span class="sxs-lookup"><span data-stu-id="43d80-130">For example, before you read a row from a database, you request a lock for read-only or for update access.</span></span> <span data-ttu-id="43d80-131">Jeśli zablokujesz wiersz dostępu do aktualizacji, żaden inny użytkownik nie może zablokować wiersza tylko do odczytu lub dostępu do aktualizacji, ponieważ otrzymają kopię danych, które są w trakcie zmiany.</span><span class="sxs-lookup"><span data-stu-id="43d80-131">If you lock a row for update access, no other users are allowed to lock the row either for read-only or update access, because they would get a copy of data that's in the process of being changed.</span></span> <span data-ttu-id="43d80-132">Jeśli zablokujesz wiersz dla dostępu tylko do odczytu, inne osoby mogą również zablokować go w celu uzyskania dostępu tylko do odczytu, ale nie do aktualizacji.</span><span class="sxs-lookup"><span data-stu-id="43d80-132">If you lock a row for read-only access, others can also lock it for read-only access but not for update.</span></span>

<span data-ttu-id="43d80-133">Zarządzanie blokadami ma wady.</span><span class="sxs-lookup"><span data-stu-id="43d80-133">Managing locks has disadvantages.</span></span> <span data-ttu-id="43d80-134">Program może być złożony.</span><span class="sxs-lookup"><span data-stu-id="43d80-134">It can be complex to program.</span></span> <span data-ttu-id="43d80-135">Wymaga znacznych zasobów zarządzania bazami danych i może powodować problemy z wydajnością wraz ze wzrostem liczby użytkowników aplikacji.</span><span class="sxs-lookup"><span data-stu-id="43d80-135">It requires significant database management resources, and it can cause performance problems as the number of users of an application increases.</span></span> <span data-ttu-id="43d80-136">Z tych powodów nie wszystkie systemy zarządzania bazami danych obsługują pesymistyczną współbieżność.</span><span class="sxs-lookup"><span data-stu-id="43d80-136">For these reasons, not all database management systems support pessimistic concurrency.</span></span> <span data-ttu-id="43d80-137">Entity Framework Core zapewnia nie wbudowaną obsługę dla niego i ten samouczek nie pokazuje, jak go zaimplementować.</span><span class="sxs-lookup"><span data-stu-id="43d80-137">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show you how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="43d80-138">Optymistyczna współbieżność</span><span class="sxs-lookup"><span data-stu-id="43d80-138">Optimistic Concurrency</span></span>

<span data-ttu-id="43d80-139">Alternatywą dla pesymistycznej współbieżności jest optymistyczna współbieżność.</span><span class="sxs-lookup"><span data-stu-id="43d80-139">The alternative to pessimistic concurrency is optimistic concurrency.</span></span> <span data-ttu-id="43d80-140">Optymistyczna współbieżność oznacza, że zezwala na konflikty współbieżności, a następnie odpowiednio reaguje, jeśli tak się stanie.</span><span class="sxs-lookup"><span data-stu-id="43d80-140">Optimistic concurrency means allowing concurrency conflicts to happen, and then reacting appropriately if they do.</span></span> <span data-ttu-id="43d80-141">Na przykład Jane odwiedza stronę Edycja działu i zmienia kwotę budżetu dla działu angielskiego z 350 000,00 USD na 0,00 USD.</span><span class="sxs-lookup"><span data-stu-id="43d80-141">For example, Jane visits the Department Edit page and changes the Budget amount for the English department from $350,000.00 to $0.00.</span></span>

![Zmiana budżetu na 0](concurrency/_static/change-budget.png)

<span data-ttu-id="43d80-143">Zanim Jane kliknie **przycisk Zapisz,** Jan odwiedza tę samą stronę i zmienia pole Data rozpoczęcia z 1/2007 na 9/1/2013.</span><span class="sxs-lookup"><span data-stu-id="43d80-143">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Zmiana daty rozpoczęcia na 2013 r.](concurrency/_static/change-date.png)

<span data-ttu-id="43d80-145">Jane klika **najpierw przycisk Zapisz** i widzi jej zmianę, gdy przeglądarka powróci do strony Indeks.</span><span class="sxs-lookup"><span data-stu-id="43d80-145">Jane clicks **Save** first and sees her change when the browser returns to the Index page.</span></span>

![Budżet zmieniony na zero](concurrency/_static/budget-zero.png)

<span data-ttu-id="43d80-147">Następnie Jan klika **zapisz** na stronie Edycji, która nadal pokazuje budżet 350 000,00 USD.</span><span class="sxs-lookup"><span data-stu-id="43d80-147">Then John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="43d80-148">Co dzieje się dalej zależy od sposobu obsługi konfliktów współbieżności.</span><span class="sxs-lookup"><span data-stu-id="43d80-148">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="43d80-149">Niektóre z opcji są następujące:</span><span class="sxs-lookup"><span data-stu-id="43d80-149">Some of the options include the following:</span></span>

* <span data-ttu-id="43d80-150">Można śledzić, która właściwość użytkownik zmodyfikował i zaktualizować tylko odpowiednie kolumny w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="43d80-150">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

     <span data-ttu-id="43d80-151">W przykładowym scenariuszu żadne dane nie zostaną utracone, ponieważ różne właściwości zostały zaktualizowane przez dwóch użytkowników.</span><span class="sxs-lookup"><span data-stu-id="43d80-151">In the example scenario, no data would be lost, because different properties were updated by the two users.</span></span> <span data-ttu-id="43d80-152">Następnym razem, gdy ktoś przegląda angielski dział, zobaczy zmiany zarówno Jane, jak i Johna - datę rozpoczęcia 1/013 i budżet zero dolarów.</span><span class="sxs-lookup"><span data-stu-id="43d80-152">The next time someone browses the English department, they will see both Jane's and John's changes -- a start date of 9/1/2013 and a budget of zero dollars.</span></span> <span data-ttu-id="43d80-153">Ta metoda aktualizacji może zmniejszyć liczbę konfliktów, które mogą spowodować utratę danych, ale nie można uniknąć utraty danych, jeśli konkurencyjne zmiany są wprowadzane do tej samej właściwości jednostki.</span><span class="sxs-lookup"><span data-stu-id="43d80-153">This method of updating can reduce the number of conflicts that could result in data loss, but it can't avoid data loss if competing changes are made to the same property of an entity.</span></span> <span data-ttu-id="43d80-154">Czy Entity Framework działa w ten sposób zależy od sposobu zaimplementowania kodu aktualizacji.</span><span class="sxs-lookup"><span data-stu-id="43d80-154">Whether the Entity Framework works this way depends on how you implement your update code.</span></span> <span data-ttu-id="43d80-155">Często nie jest praktyczne w aplikacji sieci web, ponieważ może wymagać, aby zachować duże ilości stanu w celu śledzenia wszystkich wartości oryginalnej właściwości dla jednostki, jak również nowe wartości.</span><span class="sxs-lookup"><span data-stu-id="43d80-155">It's often not practical in a web application, because it can require that you maintain large amounts of state in order to keep track of all original property values for an entity as well as new values.</span></span> <span data-ttu-id="43d80-156">Utrzymywanie dużych ilości stanu może mieć wpływ na wydajność aplikacji, ponieważ wymaga zasobów serwera lub musi być uwzględniony w samej stronie sieci web (na przykład w ukrytych polach) lub w pliku cookie.</span><span class="sxs-lookup"><span data-stu-id="43d80-156">Maintaining large amounts of state can affect application performance because it either requires server resources or must be included in the web page itself (for example, in hidden fields) or in a cookie.</span></span>

* <span data-ttu-id="43d80-157">Możesz pozwolić, aby zmiana Johna nadpisyła zmianę Jane.</span><span class="sxs-lookup"><span data-stu-id="43d80-157">You can let John's change overwrite Jane's change.</span></span>

     <span data-ttu-id="43d80-158">Następnym razem, gdy ktoś przegląda dział angielski, zobaczy 9/1/2013 i przywróconą wartość $350,000.00.</span><span class="sxs-lookup"><span data-stu-id="43d80-158">The next time someone browses the English department, they will see 9/1/2013 and the restored $350,000.00 value.</span></span> <span data-ttu-id="43d80-159">Jest to tak zwany *scenariusz Wins klienta* lub Ostatni *w* wins.</span><span class="sxs-lookup"><span data-stu-id="43d80-159">This is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="43d80-160">(Wszystkie wartości od klienta mają pierwszeństwo przed tym, co znajduje się w magazynie danych). Jak wspomniano we wstępie do tej sekcji, jeśli nie zrobisz żadnego kodowania do obsługi współbieżności, nastąpi to automatycznie.</span><span class="sxs-lookup"><span data-stu-id="43d80-160">(All values from the client take precedence over what's in the data store.) As noted in the introduction to this section, if you don't do any coding for concurrency handling, this will happen automatically.</span></span>

* <span data-ttu-id="43d80-161">Można zapobiec zmiany Jana z aktualizowaniem w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="43d80-161">You can prevent John's change from being updated in the database.</span></span>

     <span data-ttu-id="43d80-162">Zazwyczaj wyświetlany jest komunikat o błędzie, pokazywać mu bieżący stan danych i umożliwiać ponowne zastosowanie zmian, jeśli nadal chce je wprowadzić.</span><span class="sxs-lookup"><span data-stu-id="43d80-162">Typically, you would display an error message, show him the current state of the data, and allow him to reapply his changes if he still wants to make them.</span></span> <span data-ttu-id="43d80-163">Jest to tak zwany scenariusz *Wygrane sklepu.*</span><span class="sxs-lookup"><span data-stu-id="43d80-163">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="43d80-164">(Wartości magazynu danych mają pierwszeństwo przed wartościami przesłane przez klienta). W tym samouczku zaimplementujesz scenariusz Wygrane sklepu.</span><span class="sxs-lookup"><span data-stu-id="43d80-164">(The data-store values take precedence over the values submitted by the client.) You'll implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="43d80-165">Ta metoda gwarantuje, że żadne zmiany nie są zastępowane bez użytkownika jest powiadamiany o tym, co się dzieje.</span><span class="sxs-lookup"><span data-stu-id="43d80-165">This method ensures that no changes are overwritten without a user being alerted to what's happening.</span></span>

### <a name="detecting-concurrency-conflicts"></a><span data-ttu-id="43d80-166">Wykrywanie konfliktów współbieżności</span><span class="sxs-lookup"><span data-stu-id="43d80-166">Detecting concurrency conflicts</span></span>

<span data-ttu-id="43d80-167">Można rozwiązać konflikty, obsługując `DbConcurrencyException` wyjątki, które zgłasza Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="43d80-167">You can resolve conflicts by handling `DbConcurrencyException` exceptions that the Entity Framework throws.</span></span> <span data-ttu-id="43d80-168">Aby wiedzieć, kiedy zgłosić te wyjątki, entity Framework musi być w stanie wykryć konflikty.</span><span class="sxs-lookup"><span data-stu-id="43d80-168">In order to know when to throw these exceptions, the Entity Framework must be able to detect conflicts.</span></span> <span data-ttu-id="43d80-169">W związku z tym należy odpowiednio skonfigurować bazę danych i model danych.</span><span class="sxs-lookup"><span data-stu-id="43d80-169">Therefore, you must configure the database and the data model appropriately.</span></span> <span data-ttu-id="43d80-170">Oto niektóre opcje włączania wykrywania konfliktów:</span><span class="sxs-lookup"><span data-stu-id="43d80-170">Some options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="43d80-171">W tabeli bazy danych dołącz kolumnę śledzenia, która może służyć do określenia, kiedy wiersz został zmieniony.</span><span class="sxs-lookup"><span data-stu-id="43d80-171">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span> <span data-ttu-id="43d80-172">Następnie można skonfigurować Entity Framework, aby uwzględnić tę kolumnę w Where klauzuli SQL Update lub Delete poleceń.</span><span class="sxs-lookup"><span data-stu-id="43d80-172">You can then configure the Entity Framework to include that column in the Where clause of SQL Update or Delete commands.</span></span>

     <span data-ttu-id="43d80-173">Typ danych kolumny śledzenia jest `rowversion`zazwyczaj .</span><span class="sxs-lookup"><span data-stu-id="43d80-173">The data type of the tracking column is typically `rowversion`.</span></span> <span data-ttu-id="43d80-174">Wartość `rowversion` jest numerem sekwencyjnym, który jest zwiększany za każdym razem, gdy wiersz jest aktualizowany.</span><span class="sxs-lookup"><span data-stu-id="43d80-174">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="43d80-175">W poleceniu Aktualizuj lub Usuń klauzula Where zawiera oryginalną wartość kolumny śledzenia (oryginalna wersja wiersza).</span><span class="sxs-lookup"><span data-stu-id="43d80-175">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version) .</span></span> <span data-ttu-id="43d80-176">Jeśli zaktualizowany wiersz został zmieniony przez innego użytkownika, wartość w `rowversion` kolumnie jest inna niż oryginalna wartość, więc update lub Delete instrukcji nie można znaleźć wiersz do aktualizacji z powodu Where klauzuli.</span><span class="sxs-lookup"><span data-stu-id="43d80-176">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value, so the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="43d80-177">Gdy entity Framework stwierdzi, że żadne wiersze nie zostały zaktualizowane przez update lub Delete polecenia (to znaczy, gdy liczba wierszy dotyczy wynosi zero), interpretuje to jako konflikt współbieżności.</span><span class="sxs-lookup"><span data-stu-id="43d80-177">When the Entity Framework finds that no rows have been updated by the Update or Delete command (that is, when the number of affected rows is zero), it interprets that as a concurrency conflict.</span></span>

* <span data-ttu-id="43d80-178">Skonfiguruj entity framework, aby uwzględnić oryginalne wartości każdej kolumny w tabeli w Where klauzuli Update i Delete poleceń.</span><span class="sxs-lookup"><span data-stu-id="43d80-178">Configure the Entity Framework to include the original values of every column in the table in the Where clause of Update and Delete commands.</span></span>

     <span data-ttu-id="43d80-179">Podobnie jak w pierwszej opcji, jeśli cokolwiek w wierszu uległa zmianie od czasu pierwszego odczytu wiersza, klauzula Where nie zwróci wiersza do aktualizacji, który entity framework interpretuje jako konflikt współbieżności.</span><span class="sxs-lookup"><span data-stu-id="43d80-179">As in the first option, if anything in the row has changed since the row was first read, the Where clause won't return a row to update, which the Entity Framework interprets as a concurrency conflict.</span></span> <span data-ttu-id="43d80-180">W przypadku tabel bazy danych, które mają wiele kolumn, takie podejście może spowodować bardzo duże klauzule Where i może wymagać utrzymania dużych ilości stanu.</span><span class="sxs-lookup"><span data-stu-id="43d80-180">For database tables that have many columns, this approach can result in very large Where clauses, and can require that you maintain large amounts of state.</span></span> <span data-ttu-id="43d80-181">Jak wspomniano wcześniej, utrzymanie dużych ilości stanu może mieć wpływ na wydajność aplikacji.</span><span class="sxs-lookup"><span data-stu-id="43d80-181">As noted earlier, maintaining large amounts of state can affect application performance.</span></span> <span data-ttu-id="43d80-182">W związku z tym takie podejście nie jest ogólnie zalecane i nie jest to metoda używana w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="43d80-182">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

     <span data-ttu-id="43d80-183">Jeśli chcesz zaimplementować to podejście do współbieżności, należy oznaczyć wszystkie właściwości klucza niepodstawowego w jednostce, dla której chcesz śledzić współbieżność, dodając do nich `ConcurrencyCheck` atrybut.</span><span class="sxs-lookup"><span data-stu-id="43d80-183">If you do want to implement this approach to concurrency, you have to mark all non-primary-key properties in the entity you want to track concurrency for by adding the `ConcurrencyCheck` attribute to them.</span></span> <span data-ttu-id="43d80-184">Ta zmiana umożliwia Entity Framework do uwzględnienia wszystkich kolumn w SQL Where klauzuli Update i Delete instrukcji.</span><span class="sxs-lookup"><span data-stu-id="43d80-184">That change enables the Entity Framework to include all columns in the SQL Where clause of Update and Delete statements.</span></span>

<span data-ttu-id="43d80-185">W pozostałej części tego samouczka `rowversion` dodasz właściwość śledzenia do jednostki Dział, utworzysz kontroler i widoki oraz przetestujesz, aby sprawdzić, czy wszystko działa poprawnie.</span><span class="sxs-lookup"><span data-stu-id="43d80-185">In the remainder of this tutorial you'll add a `rowversion` tracking property to the Department entity, create a controller and views, and test to verify that everything works correctly.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="43d80-186">Dodawanie właściwości śledzenia</span><span class="sxs-lookup"><span data-stu-id="43d80-186">Add a tracking property</span></span>

<span data-ttu-id="43d80-187">W *models/department.cs*dodaj właściwość śledzenia o nazwie RowVersion:</span><span class="sxs-lookup"><span data-stu-id="43d80-187">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="43d80-188">Atrybut `Timestamp` określa, że ta kolumna zostanie uwzględniona w where klauzuli Update i Delete polecenia wysyłane do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="43d80-188">The `Timestamp` attribute specifies that this column will be included in the Where clause of Update and Delete commands sent to the database.</span></span> <span data-ttu-id="43d80-189">Atrybut jest wywoływany, `Timestamp` ponieważ poprzednie wersje `timestamp` programu SQL Server `rowversion` używały typu danych SQL, zanim sql zastąpił go.</span><span class="sxs-lookup"><span data-stu-id="43d80-189">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` replaced it.</span></span> <span data-ttu-id="43d80-190">Typ .NET `rowversion` jest tablicą bajtów.</span><span class="sxs-lookup"><span data-stu-id="43d80-190">The .NET type for `rowversion` is a byte array.</span></span>

<span data-ttu-id="43d80-191">Jeśli wolisz używać płynnego interfejsu API, `IsConcurrencyToken` możesz użyć metody (w *Data/SchoolContext.cs),* aby określić właściwość śledzenia, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="43d80-191">If you prefer to use the fluent API, you can use the `IsConcurrencyToken` method (in *Data/SchoolContext.cs*) to specify the tracking property, as shown in the following example:</span></span>

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

<span data-ttu-id="43d80-192">Dodając właściwość zmieniono model bazy danych, więc należy wykonać inną migrację.</span><span class="sxs-lookup"><span data-stu-id="43d80-192">By adding a property you changed the database model, so you need to do another migration.</span></span>

<span data-ttu-id="43d80-193">Zapisz zmiany i skompiluj projekt, a następnie wprowadź następujące polecenia w oknie polecenia:</span><span class="sxs-lookup"><span data-stu-id="43d80-193">Save your changes and build the project, and then enter the following commands in the command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
```

```dotnetcli
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a><span data-ttu-id="43d80-194">Tworzenie kontrolera i widoków działów</span><span class="sxs-lookup"><span data-stu-id="43d80-194">Create Departments controller and views</span></span>

<span data-ttu-id="43d80-195">Szkielet kontrolera działów i widoki, jak to miało miejsce wcześniej dla studentów, kursów i instruktorów.</span><span class="sxs-lookup"><span data-stu-id="43d80-195">Scaffold a Departments controller and views as you did earlier for Students, Courses, and Instructors.</span></span>

![Dział rusztowania](concurrency/_static/add-departments-controller.png)

<span data-ttu-id="43d80-197">W pliku *DepartmentsController.cs* zmień wszystkie cztery wystąpienia "FirstMidName" na "FullName", aby listy rozwijane administratora działu zawierały pełną nazwę instruktora, a nie tylko nazwisko.</span><span class="sxs-lookup"><span data-stu-id="43d80-197">In the *DepartmentsController.cs* file, change all four occurrences of "FirstMidName" to "FullName" so that the department administrator drop-down lists will contain the full name of the instructor rather than just the last name.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a><span data-ttu-id="43d80-198">Aktualizuj widok indeksu</span><span class="sxs-lookup"><span data-stu-id="43d80-198">Update Index view</span></span>

<span data-ttu-id="43d80-199">Aparat szkieletu utworzył kolumnę RowVersion w widoku Indeks, ale to pole nie powinno być wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="43d80-199">The scaffolding engine created a RowVersion column in the Index view, but that field shouldn't be displayed.</span></span>

<span data-ttu-id="43d80-200">Zastąp kod w *views/departments/index.cshtml* następującym kodem.</span><span class="sxs-lookup"><span data-stu-id="43d80-200">Replace the code in *Views/Departments/Index.cshtml* with the following code.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

<span data-ttu-id="43d80-201">Spowoduje to zmianę nagłówka na "Działy", powoduje usunięcie kolumny RowVersion i pokazuje pełną nazwę zamiast imienia administratora.</span><span class="sxs-lookup"><span data-stu-id="43d80-201">This changes the heading to "Departments", deletes the RowVersion column, and shows full name instead of first name for the administrator.</span></span>

## <a name="update-edit-methods"></a><span data-ttu-id="43d80-202">Aktualizuj metody edycji</span><span class="sxs-lookup"><span data-stu-id="43d80-202">Update Edit methods</span></span>

<span data-ttu-id="43d80-203">Zarówno w httpget `Edit` metody `Details` i `AsNoTracking`metody, dodaj .</span><span class="sxs-lookup"><span data-stu-id="43d80-203">In both the HttpGet `Edit` method and the `Details` method, add `AsNoTracking`.</span></span> <span data-ttu-id="43d80-204">W HttpGet `Edit` metody, dodaj wczesne ładowanie dla administratora.</span><span class="sxs-lookup"><span data-stu-id="43d80-204">In the HttpGet `Edit` method, add eager loading for the Administrator.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="43d80-205">Zastąp istniejący `Edit` kod metody HttpPost następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="43d80-205">Replace the existing code for the HttpPost `Edit` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

<span data-ttu-id="43d80-206">Kod rozpoczyna się od próby odczytania działu, który ma zostać zaktualizowany.</span><span class="sxs-lookup"><span data-stu-id="43d80-206">The code begins by trying to read the department to be updated.</span></span> <span data-ttu-id="43d80-207">Jeśli `FirstOrDefaultAsync` metoda zwraca wartość null, dział został usunięty przez innego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="43d80-207">If the `FirstOrDefaultAsync` method returns null, the department was deleted by another user.</span></span> <span data-ttu-id="43d80-208">W takim przypadku kod używa zaksięgowanych wartości formularza do utworzenia encji działu, dzięki czemu strona Edytuj może zostać ponownie wyświetlona za pomocą komunikatu o błędzie.</span><span class="sxs-lookup"><span data-stu-id="43d80-208">In that case the code uses the posted form values to create a department entity so that the Edit page can be redisplayed with an error message.</span></span> <span data-ttu-id="43d80-209">Alternatywnie nie trzeba będzie ponownie utworzyć jednostki działu, jeśli wyświetlany jest tylko komunikat o błędzie bez ponownego wyświetlania pól działu.</span><span class="sxs-lookup"><span data-stu-id="43d80-209">As an alternative, you wouldn't have to re-create the department entity if you display only an error message without redisplaying the department fields.</span></span>

<span data-ttu-id="43d80-210">Widok przechowuje `RowVersion` oryginalną wartość w polu ukrytym, a `rowVersion` ta metoda odbiera tę wartość w parametrze.</span><span class="sxs-lookup"><span data-stu-id="43d80-210">The view stores the original `RowVersion` value in a hidden field, and this method receives that value in the `rowVersion` parameter.</span></span> <span data-ttu-id="43d80-211">Przed wywołaniem `SaveChanges`, należy umieścić `RowVersion` tę oryginalną wartość właściwości w `OriginalValues` kolekcji dla jednostki.</span><span class="sxs-lookup"><span data-stu-id="43d80-211">Before you call `SaveChanges`, you have to put that original `RowVersion` property value in the `OriginalValues` collection for the entity.</span></span>

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

<span data-ttu-id="43d80-212">Następnie, gdy entity framework tworzy polecenie SQL UPDATE, to polecenie będzie zawierać klauzulę WHERE, która wyszukuje wiersz, który ma oryginalną `RowVersion` wartość.</span><span class="sxs-lookup"><span data-stu-id="43d80-212">Then when the Entity Framework creates a SQL UPDATE command, that command will include a WHERE clause that looks for a row that has the original `RowVersion` value.</span></span> <span data-ttu-id="43d80-213">Jeśli polecenie UPDATE nie ma wpływu na żadne wiersze `RowVersion` (żadne wiersze nie `DbUpdateConcurrencyException` mają oryginalnej wartości), entity framework zgłasza wyjątek.</span><span class="sxs-lookup"><span data-stu-id="43d80-213">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value),  the Entity Framework throws a `DbUpdateConcurrencyException` exception.</span></span>

<span data-ttu-id="43d80-214">Kod w bloku catch dla tego wyjątku pobiera jednostkę departamentu, którego dotyczy problem, która ma zaktualizowane wartości z `Entries` właściwości na obiekcie wyjątku.</span><span class="sxs-lookup"><span data-stu-id="43d80-214">The code in the catch block for that exception gets the affected Department entity that has the updated values from the `Entries` property on the exception object.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

<span data-ttu-id="43d80-215">Kolekcja `Entries` będzie miała `EntityEntry` tylko jeden obiekt.</span><span class="sxs-lookup"><span data-stu-id="43d80-215">The `Entries` collection will have just one `EntityEntry` object.</span></span>  <span data-ttu-id="43d80-216">Można użyć tego obiektu, aby uzyskać nowe wartości wprowadzone przez użytkownika i bieżące wartości bazy danych.</span><span class="sxs-lookup"><span data-stu-id="43d80-216">You can use that object to get the new values entered by the user and the current database values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

<span data-ttu-id="43d80-217">Kod dodaje niestandardowy komunikat o błędzie dla każdej kolumny, która ma wartości bazy danych inne niż to, co użytkownik wprowadził na stronie Edycja (tylko jedno pole jest wyświetlane tutaj dla zwięzłości).</span><span class="sxs-lookup"><span data-stu-id="43d80-217">The code adds a custom error message for each column that has database values different from what the user entered on the Edit page (only one field is shown here for brevity).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

<span data-ttu-id="43d80-218">Na koniec kod ustawia `RowVersion` wartość `departmentToUpdate` do nowej wartości pobranej z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="43d80-218">Finally, the code sets the `RowVersion` value of the `departmentToUpdate` to the new value retrieved from the database.</span></span> <span data-ttu-id="43d80-219">Ta `RowVersion` nowa wartość będzie przechowywana w polu ukrytym po ponownym wyświetleniu strony Edytuj, a następnym kliknięciem przez użytkownika **przycisku Zapisz**zostaną przechwycone tylko błędy współbieżności, które wystąpią od czasu ponownego wyświetlenia strony Edytuj.</span><span class="sxs-lookup"><span data-stu-id="43d80-219">This new `RowVersion` value will be stored in the hidden field when the Edit page is redisplayed, and the next time the user clicks **Save**, only concurrency errors that happen since the redisplay of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

<span data-ttu-id="43d80-220">Instrukcja `ModelState.Remove` jest wymagana, `ModelState` ponieważ `RowVersion` ma starą wartość.</span><span class="sxs-lookup"><span data-stu-id="43d80-220">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="43d80-221">W widoku `ModelState` wartość pola ma pierwszeństwo przed wartościami właściwości modelu, gdy oba są obecne.</span><span class="sxs-lookup"><span data-stu-id="43d80-221">In the view, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-edit-view"></a><span data-ttu-id="43d80-222">Aktualizuj widok edycji</span><span class="sxs-lookup"><span data-stu-id="43d80-222">Update Edit view</span></span>

<span data-ttu-id="43d80-223">W *widokach/działach/edit.cshtml*wykonuj następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="43d80-223">In *Views/Departments/Edit.cshtml*, make the following changes:</span></span>

* <span data-ttu-id="43d80-224">Dodaj ukryte pole, `RowVersion` aby zapisać wartość właściwości, natychmiast `DepartmentID` po ukrytym polu właściwości.</span><span class="sxs-lookup"><span data-stu-id="43d80-224">Add a hidden field to save the `RowVersion` property value, immediately following the hidden field for the `DepartmentID` property.</span></span>

* <span data-ttu-id="43d80-225">Dodaj opcję "Wybierz administratora" do listy rozwijanej.</span><span class="sxs-lookup"><span data-stu-id="43d80-225">Add a "Select Administrator" option to the drop-down list.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a><span data-ttu-id="43d80-226">Testowanie konfliktów współbieżności</span><span class="sxs-lookup"><span data-stu-id="43d80-226">Test concurrency conflicts</span></span>

<span data-ttu-id="43d80-227">Uruchom aplikację i przejdź do strony Indeks działów.</span><span class="sxs-lookup"><span data-stu-id="43d80-227">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="43d80-228">Kliknij prawym przyciskiem myszy **hiperłącze Edytuj** dla działu angielskiego i wybierz pozycję Otwórz na **nowej karcie**, a następnie kliknij polecenie Edytuj **hiperłącze** dla działu angielskiego.</span><span class="sxs-lookup"><span data-stu-id="43d80-228">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**, then click the **Edit** hyperlink for the English department.</span></span> <span data-ttu-id="43d80-229">Dwie karty przeglądarki wyświetlają teraz te same informacje.</span><span class="sxs-lookup"><span data-stu-id="43d80-229">The two browser tabs now display the same information.</span></span>

<span data-ttu-id="43d80-230">Zmień pole na pierwszej karcie przeglądarki i kliknij przycisk **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="43d80-230">Change a field in the first browser tab and click **Save**.</span></span>

![Edycja działu strona 1 po zmianie](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="43d80-232">Przeglądarka pokazuje stronę Indeks ze zmienioną wartością.</span><span class="sxs-lookup"><span data-stu-id="43d80-232">The browser shows the Index page with the changed value.</span></span>

<span data-ttu-id="43d80-233">Zmienianie pola na drugiej karcie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="43d80-233">Change a field in the second browser tab.</span></span>

![Edycja działu strona 2 po zmianie](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="43d80-235">Kliknij przycisk **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="43d80-235">Click **Save**.</span></span> <span data-ttu-id="43d80-236">Zostanie wyświetlony komunikat o błędzie:</span><span class="sxs-lookup"><span data-stu-id="43d80-236">You see an error message:</span></span>

![Komunikat o błędzie strony edycji działu](concurrency/_static/edit-error.png)

<span data-ttu-id="43d80-238">Kliknij **pozycję Zapisz** ponownie.</span><span class="sxs-lookup"><span data-stu-id="43d80-238">Click **Save** again.</span></span> <span data-ttu-id="43d80-239">Wartość wprowadzona na drugiej karcie przeglądarki zostanie zapisana.</span><span class="sxs-lookup"><span data-stu-id="43d80-239">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="43d80-240">Po wyświetleniu strony Indeksu są wyświetlane zapisane wartości.</span><span class="sxs-lookup"><span data-stu-id="43d80-240">You see the saved values when the Index page appears.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="43d80-241">Aktualizowanie strony Usuwanie</span><span class="sxs-lookup"><span data-stu-id="43d80-241">Update the Delete page</span></span>

<span data-ttu-id="43d80-242">Dla strony Usuń Entity Framework wykrywa konflikty współbieżności spowodowane przez kogoś innego edycji działu w podobny sposób.</span><span class="sxs-lookup"><span data-stu-id="43d80-242">For the Delete page, the Entity Framework detects concurrency conflicts caused by someone else editing the department in a similar manner.</span></span> <span data-ttu-id="43d80-243">Gdy HttpGet `Delete` metoda wyświetla widok potwierdzenia, widok `RowVersion` zawiera oryginalną wartość w polu ukrytym.</span><span class="sxs-lookup"><span data-stu-id="43d80-243">When the HttpGet `Delete` method displays the confirmation view, the view includes the original `RowVersion` value in a hidden field.</span></span> <span data-ttu-id="43d80-244">Ta wartość jest następnie dostępna `Delete` dla metody HttpPost, która jest wywoływana, gdy użytkownik potwierdzi usunięcie.</span><span class="sxs-lookup"><span data-stu-id="43d80-244">That value is then available to the HttpPost `Delete` method that's called when the user confirms the deletion.</span></span> <span data-ttu-id="43d80-245">Gdy entity framework tworzy polecenie SQL DELETE, zawiera klauzulę WHERE z oryginalną `RowVersion` wartością.</span><span class="sxs-lookup"><span data-stu-id="43d80-245">When the Entity Framework creates the SQL DELETE command, it includes a WHERE clause with the original `RowVersion` value.</span></span> <span data-ttu-id="43d80-246">Jeśli polecenie powoduje zero wierszy dotyczy (co oznacza, że wiersz został zmieniony po delete stronie potwierdzenia został wyświetlony), wyjątek współbieżności jest generowany, a HttpGet `Delete` metoda jest wywoływana z flagą błędu ustawioną na true w celu ponownego wyświetlenia strony potwierdzenia z komunikatem o błędzie.</span><span class="sxs-lookup"><span data-stu-id="43d80-246">If the command results in zero rows affected (meaning the row was changed after the Delete confirmation page was displayed), a concurrency exception is thrown, and the HttpGet `Delete` method is called with an error flag set to true in order to redisplay the confirmation page with an error message.</span></span> <span data-ttu-id="43d80-247">Jest również możliwe, że zero wierszy zostały naruszone, ponieważ wiersz został usunięty przez innego użytkownika, więc w tym przypadku nie jest wyświetlany żaden komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="43d80-247">It's also possible that zero rows were affected because the row was deleted by another user, so in that case no error message is displayed.</span></span>

### <a name="update-the-delete-methods-in-the-departments-controller"></a><span data-ttu-id="43d80-248">Aktualizowanie metod usuwania w kontrolerze działów</span><span class="sxs-lookup"><span data-stu-id="43d80-248">Update the Delete methods in the Departments controller</span></span>

<span data-ttu-id="43d80-249">W *DepartmentsController.cs*, zastąp `Delete` HttpGet metody z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="43d80-249">In *DepartmentsController.cs*, replace the HttpGet `Delete` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

<span data-ttu-id="43d80-250">Metoda akceptuje opcjonalny parametr, który wskazuje, czy strona jest ponownie świetlana po błędzie współbieżności.</span><span class="sxs-lookup"><span data-stu-id="43d80-250">The method accepts an optional parameter that indicates whether the page is being redisplayed after a concurrency error.</span></span> <span data-ttu-id="43d80-251">Jeśli ta flaga jest true i dział określony już nie istnieje, został usunięty przez innego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="43d80-251">If this flag is true and the department specified no longer exists, it was deleted by another user.</span></span> <span data-ttu-id="43d80-252">W takim przypadku kod przekierowuje do indeksu strony.</span><span class="sxs-lookup"><span data-stu-id="43d80-252">In that case, the code redirects to the Index page.</span></span>  <span data-ttu-id="43d80-253">Jeśli ta flaga jest true i Dział istnieje, został zmieniony przez innego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="43d80-253">If this flag is true and the Department does exist, it was changed by another user.</span></span> <span data-ttu-id="43d80-254">W takim przypadku kod wysyła komunikat o `ViewData`błędzie do widoku za pomocą .</span><span class="sxs-lookup"><span data-stu-id="43d80-254">In that case, the code sends an error message to the view using `ViewData`.</span></span>

<span data-ttu-id="43d80-255">Zastąp kod `Delete` w metodzie HttpPost (o nazwie) `DeleteConfirmed`następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="43d80-255">Replace the code in the HttpPost `Delete` method (named `DeleteConfirmed`) with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

<span data-ttu-id="43d80-256">W kodzie szkieletowym, który właśnie został zastąpiony, ta metoda zaakceptowała tylko identyfikator rekordu:</span><span class="sxs-lookup"><span data-stu-id="43d80-256">In the scaffolded code that you just replaced, this method accepted only a record ID:</span></span>

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

<span data-ttu-id="43d80-257">Ten parametr został zmieniony na wystąpienie jednostki dział utworzone przez spinacza modelu.</span><span class="sxs-lookup"><span data-stu-id="43d80-257">You've changed this parameter to a Department entity instance created by the model binder.</span></span> <span data-ttu-id="43d80-258">Daje to EF dostęp do wartości właściwości RowVersion oprócz klucza rekordu.</span><span class="sxs-lookup"><span data-stu-id="43d80-258">This gives EF access to the RowVersion property value in addition to the record key.</span></span>

```csharp
public async Task<IActionResult> Delete(Department department)
```

<span data-ttu-id="43d80-259">Zmieniono również nazwę metody `DeleteConfirmed` akcji `Delete`na .</span><span class="sxs-lookup"><span data-stu-id="43d80-259">You have also changed the action method name from `DeleteConfirmed` to `Delete`.</span></span> <span data-ttu-id="43d80-260">Szkieletowy kod użył nazwy, `DeleteConfirmed` aby nadać metodzie HttpPost unikatowy podpis.</span><span class="sxs-lookup"><span data-stu-id="43d80-260">The scaffolded code used the name `DeleteConfirmed` to give the HttpPost method a unique signature.</span></span> <span data-ttu-id="43d80-261">(CLR wymaga przeciążonych metod, aby mieć różne parametry metody.) Teraz, gdy podpisy są unikatowe, można trzymać się konwencji MVC i używać tej samej nazwy dla HttpPost i HttpGet delete metody.</span><span class="sxs-lookup"><span data-stu-id="43d80-261">(The CLR requires overloaded methods to have different method parameters.) Now that the signatures are unique, you can stick with the MVC convention and use the same name for the HttpPost and HttpGet delete methods.</span></span>

<span data-ttu-id="43d80-262">Jeśli dział jest już `AnyAsync` usunięty, metoda zwraca false i aplikacja po prostu wraca do Index metody.</span><span class="sxs-lookup"><span data-stu-id="43d80-262">If the department is already deleted, the `AnyAsync` method returns false and the application just goes back to the Index method.</span></span>

<span data-ttu-id="43d80-263">Jeśli zostanie przechwycony błąd współbieżności, kod ponownie wyświetla stronę potwierdzenia usuń i udostępnia flagę, która wskazuje, że powinien wyświetlać komunikat o błędzie współbieżności.</span><span class="sxs-lookup"><span data-stu-id="43d80-263">If a concurrency error is caught, the code redisplays the Delete confirmation page and provides a flag that indicates it should display a concurrency error message.</span></span>

### <a name="update-the-delete-view"></a><span data-ttu-id="43d80-264">Aktualizowanie widoku Usuń</span><span class="sxs-lookup"><span data-stu-id="43d80-264">Update the Delete view</span></span>

<span data-ttu-id="43d80-265">W *widokach/departments/delete.cshtml*zastąp kod szkieletu następującym kodem, który dodaje pole komunikatu o błędzie i ukryte pola dla właściwości DepartmentID i RowVersion.</span><span class="sxs-lookup"><span data-stu-id="43d80-265">In *Views/Departments/Delete.cshtml*, replace the scaffolded code with the following code that adds an error message field and hidden fields for the DepartmentID and RowVersion properties.</span></span> <span data-ttu-id="43d80-266">Zmiany są wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="43d80-266">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

<span data-ttu-id="43d80-267">Powoduje to następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="43d80-267">This makes the following changes:</span></span>

* <span data-ttu-id="43d80-268">Dodaje komunikat o `h2` błędzie `h3` między nagłówkami a nagłówkami.</span><span class="sxs-lookup"><span data-stu-id="43d80-268">Adds an error message between the `h2` and `h3` headings.</span></span>

* <span data-ttu-id="43d80-269">Zastępuje program FirstMidName na fullname w polu **Administrator.**</span><span class="sxs-lookup"><span data-stu-id="43d80-269">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>

* <span data-ttu-id="43d80-270">Usuwa pole WierszVersion.</span><span class="sxs-lookup"><span data-stu-id="43d80-270">Removes the RowVersion field.</span></span>

* <span data-ttu-id="43d80-271">Dodaje ukryte pole `RowVersion` dla właściwości.</span><span class="sxs-lookup"><span data-stu-id="43d80-271">Adds a hidden field for the `RowVersion` property.</span></span>

<span data-ttu-id="43d80-272">Uruchom aplikację i przejdź do strony Indeks działów.</span><span class="sxs-lookup"><span data-stu-id="43d80-272">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="43d80-273">Kliknij prawym przyciskiem myszy **usuń** hiperłącze dla działu angielskiego i wybierz pozycję **Otwórz na nowej karcie,** a następnie na pierwszej karcie kliknij polecenie Edytuj hiperłącze dla działu angielskiego. **Edit**</span><span class="sxs-lookup"><span data-stu-id="43d80-273">Right-click the **Delete** hyperlink for the English department and select **Open in new tab**, then in the first tab click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="43d80-274">W pierwszym oknie zmień jedną z wartości i kliknij przycisk **Zapisz**:</span><span class="sxs-lookup"><span data-stu-id="43d80-274">In the first window, change one of the values, and click **Save**:</span></span>

![Strona Edycji działu po zmianie przed usunięciem](concurrency/_static/edit-after-change-for-delete.png)

<span data-ttu-id="43d80-276">Na drugiej karcie kliknij pozycję **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="43d80-276">In the second tab, click **Delete**.</span></span> <span data-ttu-id="43d80-277">Zostanie wyświetlony komunikat o błędzie współbieżności, a wartości działu są odświeżane z tym, co jest obecnie w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="43d80-277">You see the concurrency error message, and the Department values are refreshed with what's currently in the database.</span></span>

![Strona potwierdzenia usunięcia departamentu z błędem współbieżności](concurrency/_static/delete-error.png)

<span data-ttu-id="43d80-279">Jeśli klikniesz ponownie **przycisk Usuń,** zostaniesz przekierowany do strony Indeks, która pokazuje, że dział został usunięty.</span><span class="sxs-lookup"><span data-stu-id="43d80-279">If you click **Delete** again, you're redirected to the Index page, which shows that the department has been deleted.</span></span>

## <a name="update-details-and-create-views"></a><span data-ttu-id="43d80-280">Aktualizowanie szczegółów i tworzenie widoków</span><span class="sxs-lookup"><span data-stu-id="43d80-280">Update Details and Create views</span></span>

<span data-ttu-id="43d80-281">Opcjonalnie można oczyścić szkieletu kodu w Szczegóły i Tworzenie widoków.</span><span class="sxs-lookup"><span data-stu-id="43d80-281">You can optionally clean up scaffolded code in the Details and Create views.</span></span>

<span data-ttu-id="43d80-282">Zastąp kod w *widokach/departamentach/details.cshtml,* aby usunąć kolumnę RowVersion i wyświetlić pełną nazwę administratora.</span><span class="sxs-lookup"><span data-stu-id="43d80-282">Replace the code in *Views/Departments/Details.cshtml* to delete the RowVersion column and show the full name of the Administrator.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

<span data-ttu-id="43d80-283">Zastąp kod w *widokach/działach/create.cshtml,* aby dodać opcję Wybierz do listy rozwijanej.</span><span class="sxs-lookup"><span data-stu-id="43d80-283">Replace the code in *Views/Departments/Create.cshtml* to add a Select option to the drop-down list.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a><span data-ttu-id="43d80-284">Uzyskiwanie kodu</span><span class="sxs-lookup"><span data-stu-id="43d80-284">Get the code</span></span>

[<span data-ttu-id="43d80-285">Pobierz lub wyświetl ukończoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="43d80-285">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="43d80-286">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="43d80-286">Additional resources</span></span>

 <span data-ttu-id="43d80-287">Aby uzyskać więcej informacji na temat obsługi współbieżności w EF Core, zobacz [Konflikty współbieżności](/ef/core/saving/concurrency).</span><span class="sxs-lookup"><span data-stu-id="43d80-287">For more information about how to handle concurrency in EF Core, see [Concurrency conflicts](/ef/core/saving/concurrency).</span></span>

## <a name="next-steps"></a><span data-ttu-id="43d80-288">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="43d80-288">Next steps</span></span>

<span data-ttu-id="43d80-289">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="43d80-289">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="43d80-290">Dowiedz się więcej o konfliktach współbieżności</span><span class="sxs-lookup"><span data-stu-id="43d80-290">Learned about concurrency conflicts</span></span>
> * <span data-ttu-id="43d80-291">Dodano właściwość śledzenia</span><span class="sxs-lookup"><span data-stu-id="43d80-291">Added a tracking property</span></span>
> * <span data-ttu-id="43d80-292">Utworzony kontroler i widoki działów</span><span class="sxs-lookup"><span data-stu-id="43d80-292">Created Departments controller and views</span></span>
> * <span data-ttu-id="43d80-293">Zaktualizowany widok indeksu</span><span class="sxs-lookup"><span data-stu-id="43d80-293">Updated Index view</span></span>
> * <span data-ttu-id="43d80-294">Zaktualizowane metody edycji</span><span class="sxs-lookup"><span data-stu-id="43d80-294">Updated Edit methods</span></span>
> * <span data-ttu-id="43d80-295">Zaktualizowany widok edycji</span><span class="sxs-lookup"><span data-stu-id="43d80-295">Updated Edit view</span></span>
> * <span data-ttu-id="43d80-296">Przetestowane konflikty współbieżności</span><span class="sxs-lookup"><span data-stu-id="43d80-296">Tested concurrency conflicts</span></span>
> * <span data-ttu-id="43d80-297">Zaktualizowano stronę Usuwanie</span><span class="sxs-lookup"><span data-stu-id="43d80-297">Updated the Delete page</span></span>
> * <span data-ttu-id="43d80-298">Zaktualizowano szczegóły i tworzenie widoków</span><span class="sxs-lookup"><span data-stu-id="43d80-298">Updated Details and Create views</span></span>

<span data-ttu-id="43d80-299">Przejdź do następnego samouczka, aby dowiedzieć się, jak zaimplementować dziedziczenie tabeli na hierarchię dla jednostek Instruktor i Student.</span><span class="sxs-lookup"><span data-stu-id="43d80-299">Advance to the next tutorial to learn how to implement table-per-hierarchy inheritance for the Instructor and Student entities.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="43d80-300">Dalej: Implementowanie dziedziczenia tabeli na hierarchię</span><span class="sxs-lookup"><span data-stu-id="43d80-300">Next: Implement table-per-hierarchy inheritance</span></span>](inheritance.md)
