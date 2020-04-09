---
title: Strony brzytwy z EF Core w ASP.NET Core - Współbieżność - 8 z 8
author: rick-anderson
description: W tym samouczku pokazano, jak obsługiwać konflikty, gdy wielu użytkowników aktualizuje tę samą jednostkę w tym samym czasie.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/concurrency
ms.openlocfilehash: c4d43f26ba80e7922c3cbd37d9a5f8e1561b11ad
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656913"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---concurrency---8-of-8"></a><span data-ttu-id="4ed38-103">Strony brzytwy z EF Core w ASP.NET Core - Współbieżność - 8 z 8</span><span class="sxs-lookup"><span data-stu-id="4ed38-103">Razor Pages with EF Core in ASP.NET Core - Concurrency - 8 of 8</span></span>

<span data-ttu-id="4ed38-104">Rick [Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra)i Jon P [Smith](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="4ed38-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4ed38-105">W tym samouczku pokazano, jak obsługiwać konflikty, gdy wielu użytkowników aktualizuje jednostkę jednocześnie (w tym samym czasie).</span><span class="sxs-lookup"><span data-stu-id="4ed38-105">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="4ed38-106">Konflikty współbieżności</span><span class="sxs-lookup"><span data-stu-id="4ed38-106">Concurrency conflicts</span></span>

<span data-ttu-id="4ed38-107">Konflikt współbieżności występuje, gdy:</span><span class="sxs-lookup"><span data-stu-id="4ed38-107">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="4ed38-108">Użytkownik przechodzi do strony edycji encji.</span><span class="sxs-lookup"><span data-stu-id="4ed38-108">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="4ed38-109">Inny użytkownik aktualizuje tę samą jednostkę przed zapisaniem pierwszej zmiany w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="4ed38-109">Another user updates the same entity before the first user's change is written to the database.</span></span>

<span data-ttu-id="4ed38-110">Jeśli wykrywanie współbieżności nie jest włączone, kto aktualizuje bazę danych ostatnio zastępuje zmiany innego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4ed38-110">If concurrency detection isn't enabled, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="4ed38-111">Jeśli to ryzyko jest dopuszczalne, koszt programowania współbieżności może przeważyć korzyści.</span><span class="sxs-lookup"><span data-stu-id="4ed38-111">If this risk is acceptable, the cost of programming for concurrency might outweigh the benefit.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="4ed38-112">Współbieżność pesymistyczna (blokowanie)</span><span class="sxs-lookup"><span data-stu-id="4ed38-112">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="4ed38-113">Jednym ze sposobów zapobiegania konfliktom współbieżności jest użycie blokad bazy danych.</span><span class="sxs-lookup"><span data-stu-id="4ed38-113">One way to prevent concurrency conflicts is to use database locks.</span></span> <span data-ttu-id="4ed38-114">Jest to tak zwana współbieżność pesymistyczna.</span><span class="sxs-lookup"><span data-stu-id="4ed38-114">This is called pessimistic concurrency.</span></span> <span data-ttu-id="4ed38-115">Zanim aplikacja odczyta wiersz bazy danych, który zamierza zaktualizować, żąda blokady.</span><span class="sxs-lookup"><span data-stu-id="4ed38-115">Before the app reads a database row that it intends to update, it requests a lock.</span></span> <span data-ttu-id="4ed38-116">Gdy wiersz jest zablokowany dla dostępu do aktualizacji, żaden inny użytkownik nie mogą zablokować wiersz, dopóki nie zostanie zwolniona pierwsza blokada.</span><span class="sxs-lookup"><span data-stu-id="4ed38-116">Once a row is locked for update access, no other users are allowed to lock the row until the first lock is released.</span></span>

<span data-ttu-id="4ed38-117">Zarządzanie blokadami ma wady.</span><span class="sxs-lookup"><span data-stu-id="4ed38-117">Managing locks has disadvantages.</span></span> <span data-ttu-id="4ed38-118">Program może być złożony i może powodować problemy z wydajnością wraz ze wzrostem liczby użytkowników.</span><span class="sxs-lookup"><span data-stu-id="4ed38-118">It can be complex to program and can cause performance problems as the number of users increases.</span></span> <span data-ttu-id="4ed38-119">Entity Framework Core zapewnia nie wbudowaną obsługę dla niego i ten samouczek nie pokazuje, jak go zaimplementować.</span><span class="sxs-lookup"><span data-stu-id="4ed38-119">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="4ed38-120">Optymistyczna współbieżność</span><span class="sxs-lookup"><span data-stu-id="4ed38-120">Optimistic concurrency</span></span>

<span data-ttu-id="4ed38-121">Optymistyczna współbieżność umożliwia konflikty współbieżności się zdarzyć, a następnie reaguje odpowiednio, gdy to robią.</span><span class="sxs-lookup"><span data-stu-id="4ed38-121">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="4ed38-122">Na przykład Jane odwiedza stronę edycji departamentu i zmienia budżet działu angielskiego z 350 000,00 USD na 0,00 USD.</span><span class="sxs-lookup"><span data-stu-id="4ed38-122">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Zmiana budżetu na 0](concurrency/_static/change-budget30.png)

<span data-ttu-id="4ed38-124">Zanim Jane kliknie **przycisk Zapisz,** Jan odwiedza tę samą stronę i zmienia pole Data rozpoczęcia z 1/2007 na 9/1/2013.</span><span class="sxs-lookup"><span data-stu-id="4ed38-124">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Zmiana daty rozpoczęcia na 2013 r.](concurrency/_static/change-date30.png)

<span data-ttu-id="4ed38-126">Jane klika **najpierw przycisk Zapisz** i widzi, że jej zmiana zaczyna obowiązywać, ponieważ przeglądarka wyświetla stronę Indeks z zerem jako kwotę budżetu.</span><span class="sxs-lookup"><span data-stu-id="4ed38-126">Jane clicks **Save** first and sees her change take effect, since the browser displays the Index page with zero as the Budget amount.</span></span>

<span data-ttu-id="4ed38-127">Jan klika **zapisz** na stronie Edycji, która nadal pokazuje budżet 350 000,00 USD.</span><span class="sxs-lookup"><span data-stu-id="4ed38-127">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="4ed38-128">Co dzieje się dalej zależy od sposobu obsługi konfliktów współbieżności:</span><span class="sxs-lookup"><span data-stu-id="4ed38-128">What happens next is determined by how you handle concurrency conflicts:</span></span>

* <span data-ttu-id="4ed38-129">Można śledzić, która właściwość użytkownik zmodyfikował i zaktualizować tylko odpowiednie kolumny w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="4ed38-129">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

  <span data-ttu-id="4ed38-130">W scenariuszu żadne dane nie zostaną utracone.</span><span class="sxs-lookup"><span data-stu-id="4ed38-130">In the scenario, no data would be lost.</span></span> <span data-ttu-id="4ed38-131">Różne właściwości zostały zaktualizowane przez dwóch użytkowników.</span><span class="sxs-lookup"><span data-stu-id="4ed38-131">Different properties were updated by the two users.</span></span> <span data-ttu-id="4ed38-132">Następnym razem, gdy ktoś przegląda angielski dział, zobaczą zmiany zarówno Jane, jak i Johna.</span><span class="sxs-lookup"><span data-stu-id="4ed38-132">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="4ed38-133">Ta metoda aktualizacji może zmniejszyć liczbę konfliktów, które mogą spowodować utratę danych.</span><span class="sxs-lookup"><span data-stu-id="4ed38-133">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="4ed38-134">Takie podejście ma pewne wady:</span><span class="sxs-lookup"><span data-stu-id="4ed38-134">This approach has some disadvantages:</span></span>
 
  * <span data-ttu-id="4ed38-135">Nie można uniknąć utraty danych, jeśli konkurencyjne zmiany są wprowadzane do tej samej właściwości.</span><span class="sxs-lookup"><span data-stu-id="4ed38-135">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="4ed38-136">Ogólnie nie jest praktyczne w aplikacji sieci web.</span><span class="sxs-lookup"><span data-stu-id="4ed38-136">Is generally not practical in a web app.</span></span> <span data-ttu-id="4ed38-137">Wymaga utrzymania istotnego stanu w celu śledzenia wszystkich pobranych wartości i nowych wartości.</span><span class="sxs-lookup"><span data-stu-id="4ed38-137">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="4ed38-138">Utrzymywanie dużych ilości stanu może mieć wpływ na wydajność aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4ed38-138">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="4ed38-139">Może zwiększyć złożoność aplikacji w porównaniu do wykrywania współbieżności w jednostce.</span><span class="sxs-lookup"><span data-stu-id="4ed38-139">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="4ed38-140">Możesz pozwolić, aby zmiana Johna nadpisyła zmianę Jane.</span><span class="sxs-lookup"><span data-stu-id="4ed38-140">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="4ed38-141">Następnym razem, gdy ktoś przegląda dział angielski, zobaczy 9/1/2013 i pobraną wartość $350,000.00.</span><span class="sxs-lookup"><span data-stu-id="4ed38-141">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="4ed38-142">Takie podejście jest nazywany *klient wins* lub *Ostatni w* wins scenariusza.</span><span class="sxs-lookup"><span data-stu-id="4ed38-142">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="4ed38-143">(Wszystkie wartości od klienta mają pierwszeństwo przed tym, co znajduje się w magazynie danych). Jeśli nie wykonasz żadnego kodowania do obsługi współbieżności, wygrywa klient odbywa się automatycznie.</span><span class="sxs-lookup"><span data-stu-id="4ed38-143">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="4ed38-144">Można zapobiec zmiany Jana z aktualizowaniem w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="4ed38-144">You can prevent John's change from being updated in the database.</span></span> <span data-ttu-id="4ed38-145">Zazwyczaj aplikacja będzie:</span><span class="sxs-lookup"><span data-stu-id="4ed38-145">Typically, the app would:</span></span>

  * <span data-ttu-id="4ed38-146">Wyświetl komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="4ed38-146">Display an error message.</span></span>
  * <span data-ttu-id="4ed38-147">Pokaż bieżący stan danych.</span><span class="sxs-lookup"><span data-stu-id="4ed38-147">Show the current state of the data.</span></span>
  * <span data-ttu-id="4ed38-148">Zezwalaj użytkownikowi na ponowne zastosowanie zmian.</span><span class="sxs-lookup"><span data-stu-id="4ed38-148">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="4ed38-149">Jest to tak zwany scenariusz *Wygrane sklepu.*</span><span class="sxs-lookup"><span data-stu-id="4ed38-149">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="4ed38-150">(Wartości magazynu danych mają pierwszeństwo przed wartościami przesłane przez klienta). Zaimplementowanie scenariusza Wygrane sklepu w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="4ed38-150">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="4ed38-151">Ta metoda gwarantuje, że żadne zmiany nie są zastępowane bez użytkownika jest alerty.</span><span class="sxs-lookup"><span data-stu-id="4ed38-151">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="conflict-detection-in-ef-core"></a><span data-ttu-id="4ed38-152">Wykrywanie konfliktów w EF Core</span><span class="sxs-lookup"><span data-stu-id="4ed38-152">Conflict detection in EF Core</span></span>

<span data-ttu-id="4ed38-153">EF Core `DbConcurrencyException` zgłasza wyjątki, gdy wykryje konflikty.</span><span class="sxs-lookup"><span data-stu-id="4ed38-153">EF Core throws `DbConcurrencyException` exceptions when it detects conflicts.</span></span> <span data-ttu-id="4ed38-154">Model danych musi być skonfigurowany tak, aby włączyć wykrywanie konfliktów.</span><span class="sxs-lookup"><span data-stu-id="4ed38-154">The data model has to be configured to enable conflict detection.</span></span> <span data-ttu-id="4ed38-155">Opcje włączania wykrywania konfliktów są następujące:</span><span class="sxs-lookup"><span data-stu-id="4ed38-155">Options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="4ed38-156">Skonfiguruj EF Core, aby uwzględnić oryginalne wartości kolumn skonfigurowanych jako [tokeny współbieżności](/ef/core/modeling/concurrency) w gdzie klauzuli Update i Delete poleceń.</span><span class="sxs-lookup"><span data-stu-id="4ed38-156">Configure EF Core to include the original values of columns configured as [concurrency tokens](/ef/core/modeling/concurrency) in the Where clause of Update and Delete commands.</span></span>

  <span data-ttu-id="4ed38-157">Gdy `SaveChanges` jest wywoływana, Where klauzuli wyszukuje oryginalne wartości wszystkich właściwości z adnotacjami z [atrybutem ConcurrenceCheck.](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute)</span><span class="sxs-lookup"><span data-stu-id="4ed38-157">When `SaveChanges` is called, the Where clause looks for the original values of any properties annotated with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) attribute.</span></span> <span data-ttu-id="4ed38-158">Instrukcja aktualizacji nie znajdzie wiersza do aktualizacji, jeśli którykolwiek z właściwości tokenu współbieżności zmienił się od czasu pierwszego odczytu wiersza.</span><span class="sxs-lookup"><span data-stu-id="4ed38-158">The update statement won't find a row to update if any of the concurrency token properties changed since the row was first read.</span></span> <span data-ttu-id="4ed38-159">EF Core interpretuje to jako konflikt współbieżności.</span><span class="sxs-lookup"><span data-stu-id="4ed38-159">EF Core interprets that as a concurrency conflict.</span></span> <span data-ttu-id="4ed38-160">W przypadku tabel bazy danych, które mają wiele kolumn, takie podejście może spowodować bardzo duże klauzule Where i może wymagać dużych ilości stanu.</span><span class="sxs-lookup"><span data-stu-id="4ed38-160">For database tables that have many columns, this approach can result in very large Where clauses, and can require large amounts of state.</span></span> <span data-ttu-id="4ed38-161">W związku z tym takie podejście nie jest ogólnie zalecane i nie jest to metoda używana w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="4ed38-161">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

* <span data-ttu-id="4ed38-162">W tabeli bazy danych dołącz kolumnę śledzenia, która może służyć do określenia, kiedy wiersz został zmieniony.</span><span class="sxs-lookup"><span data-stu-id="4ed38-162">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span>

  <span data-ttu-id="4ed38-163">W bazie danych programu SQL Server typ `rowversion`danych kolumny śledzenia to .</span><span class="sxs-lookup"><span data-stu-id="4ed38-163">In a SQL Server database, the data type of the tracking column is `rowversion`.</span></span> <span data-ttu-id="4ed38-164">Wartość `rowversion` jest numerem sekwencyjnym, który jest zwiększany za każdym razem, gdy wiersz jest aktualizowany.</span><span class="sxs-lookup"><span data-stu-id="4ed38-164">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="4ed38-165">W poleceniu Aktualizuj lub Usuń klauzula Where zawiera oryginalną wartość kolumny śledzenia (oryginalny numer wersji wiersza).</span><span class="sxs-lookup"><span data-stu-id="4ed38-165">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version number).</span></span> <span data-ttu-id="4ed38-166">Jeśli aktualizowany wiersz został zmieniony przez innego użytkownika, wartość w kolumnie `rowversion` jest inna niż oryginalna wartość.</span><span class="sxs-lookup"><span data-stu-id="4ed38-166">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value.</span></span> <span data-ttu-id="4ed38-167">W takim przypadku Update lub Delete instrukcji nie można znaleźć wiersz do aktualizacji z powodu Where klauzuli.</span><span class="sxs-lookup"><span data-stu-id="4ed38-167">In that case, the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="4ed38-168">EF Core zgłasza wyjątek współbieżności, gdy żadne wiersze nie są dotknięte update lub delete polecenia.</span><span class="sxs-lookup"><span data-stu-id="4ed38-168">EF Core throws a concurrency exception when no rows are affected by an Update or Delete command.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="4ed38-169">Dodawanie właściwości śledzenia</span><span class="sxs-lookup"><span data-stu-id="4ed38-169">Add a tracking property</span></span>

<span data-ttu-id="4ed38-170">W *models/department.cs*dodaj właściwość śledzenia o nazwie RowVersion:</span><span class="sxs-lookup"><span data-stu-id="4ed38-170">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

<span data-ttu-id="4ed38-171">[Atrybut sygnatury czasowej](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) jest tym, co identyfikuje kolumnę jako kolumnę śledzenia współbieżności.</span><span class="sxs-lookup"><span data-stu-id="4ed38-171">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute is what identifies the column as a concurrency tracking column.</span></span> <span data-ttu-id="4ed38-172">Płynny interfejs API jest alternatywnym sposobem określenia właściwości śledzenia:</span><span class="sxs-lookup"><span data-stu-id="4ed38-172">The fluent API is an alternative way to specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[<span data-ttu-id="4ed38-173">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ed38-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4ed38-174">W przypadku bazy danych `[Timestamp]` programu SQL Server atrybut właściwości jednostki zdefiniowanej jako tablica bajtów:</span><span class="sxs-lookup"><span data-stu-id="4ed38-174">For a SQL Server database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="4ed38-175">Powoduje, że kolumna ma być uwzględniona w delete i UPDATE WHERE klauzul.</span><span class="sxs-lookup"><span data-stu-id="4ed38-175">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="4ed38-176">Ustawia typ kolumny w bazie danych na [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="4ed38-176">Sets the column type in the database to [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span></span>

<span data-ttu-id="4ed38-177">Baza danych generuje kolejny numer wersji wiersza, który jest zwiększany za każdym razem, gdy wiersz jest aktualizowany.</span><span class="sxs-lookup"><span data-stu-id="4ed38-177">The database generates a sequential row version number that's incremented each time the row is updated.</span></span> <span data-ttu-id="4ed38-178">W `Update` `Delete` lub polecenia `Where` klauzuli zawiera pobraną wartość wersji wiersza.</span><span class="sxs-lookup"><span data-stu-id="4ed38-178">In an `Update` or `Delete` command, the `Where` clause includes the fetched row version value.</span></span> <span data-ttu-id="4ed38-179">Jeśli aktualizowany wiersz uległ zmianie od momentu pobrania:</span><span class="sxs-lookup"><span data-stu-id="4ed38-179">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="4ed38-180">Bieżąca wartość wersji wiersza nie jest zgodna z pobraną wartością.</span><span class="sxs-lookup"><span data-stu-id="4ed38-180">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="4ed38-181">Polecenia `Update` `Delete` lub polecenia nie znajdują wiersza, ponieważ klauzula `Where` wyszukuje pobraną wartość wersji wiersza.</span><span class="sxs-lookup"><span data-stu-id="4ed38-181">The `Update` or `Delete` commands don't find a row because the `Where` clause looks for the fetched row version value.</span></span>
* <span data-ttu-id="4ed38-182">A `DbUpdateConcurrencyException` jest wyrzucany.</span><span class="sxs-lookup"><span data-stu-id="4ed38-182">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="4ed38-183">Poniższy kod przedstawia część T-SQL generowane przez EF Core, gdy nazwa działu jest aktualizowana:</span><span class="sxs-lookup"><span data-stu-id="4ed38-183">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

<span data-ttu-id="4ed38-184">Poprzedni wyróżniony kod przedstawia `WHERE` klauzulę `RowVersion`zawierającą .</span><span class="sxs-lookup"><span data-stu-id="4ed38-184">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="4ed38-185">Jeśli baza `RowVersion` danych nie `RowVersion` jest`@p2`równa parametrowi ( ), żadne wiersze nie są aktualizowane.</span><span class="sxs-lookup"><span data-stu-id="4ed38-185">If the database `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="4ed38-186">Poniższy wyróżniony kod pokazuje T-SQL, który weryfikuje dokładnie jeden wiersz został zaktualizowany:</span><span class="sxs-lookup"><span data-stu-id="4ed38-186">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

<span data-ttu-id="4ed38-187">[@@ROWCOUNT ](/sql/t-sql/functions/rowcount-transact-sql) zwraca liczbę wierszy, których dotyczy ostatnia instrukcja.</span><span class="sxs-lookup"><span data-stu-id="4ed38-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="4ed38-188">Jeśli żadne wiersze nie są aktualizowane, EF Core zgłasza . `DbUpdateConcurrencyException`</span><span class="sxs-lookup"><span data-stu-id="4ed38-188">If no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4ed38-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ed38-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4ed38-190">W przypadku bazy danych `[Timestamp]` SQLite atrybut właściwości jednostki zdefiniowanej jako tablica bajtów:</span><span class="sxs-lookup"><span data-stu-id="4ed38-190">For a SQLite database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="4ed38-191">Powoduje, że kolumna ma być uwzględniona w delete i UPDATE WHERE klauzul.</span><span class="sxs-lookup"><span data-stu-id="4ed38-191">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="4ed38-192">Mapuje do typu kolumny obiektu BLOB.</span><span class="sxs-lookup"><span data-stu-id="4ed38-192">Maps to a BLOB column type.</span></span>

<span data-ttu-id="4ed38-193">Wyzwalacze bazy danych aktualizują kolumnę RowVersion o nową tablicę bajtów losowych za każdym razem, gdy wiersz jest aktualizowany.</span><span class="sxs-lookup"><span data-stu-id="4ed38-193">Database triggers update the RowVersion column with a new random byte array whenever a row is updated.</span></span> <span data-ttu-id="4ed38-194">W `Update` `Delete` lub polecenia `Where` klauzuli zawiera pobraną wartość RowVersion kolumny.</span><span class="sxs-lookup"><span data-stu-id="4ed38-194">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of the RowVersion column.</span></span> <span data-ttu-id="4ed38-195">Jeśli aktualizowany wiersz uległ zmianie od momentu pobrania:</span><span class="sxs-lookup"><span data-stu-id="4ed38-195">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="4ed38-196">Bieżąca wartość wersji wiersza nie jest zgodna z pobraną wartością.</span><span class="sxs-lookup"><span data-stu-id="4ed38-196">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="4ed38-197">Polecenie `Update` `Delete` lub nie znajduje wiersza, `Where` ponieważ klauzula wyszukuje oryginalną wartość wersji wiersza.</span><span class="sxs-lookup"><span data-stu-id="4ed38-197">The `Update` or `Delete` command doesn't find a row because the `Where` clause looks for the original row version value.</span></span>
* <span data-ttu-id="4ed38-198">A `DbUpdateConcurrencyException` jest wyrzucany.</span><span class="sxs-lookup"><span data-stu-id="4ed38-198">A `DbUpdateConcurrencyException` is thrown.</span></span>

---

### <a name="update-the-database"></a><span data-ttu-id="4ed38-199">Aktualizowanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="4ed38-199">Update the database</span></span>

<span data-ttu-id="4ed38-200">Dodanie `RowVersion` właściwości zmienia model danych, który wymaga migracji.</span><span class="sxs-lookup"><span data-stu-id="4ed38-200">Adding the `RowVersion` property changes the data model, which requires a migration.</span></span>

<span data-ttu-id="4ed38-201">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="4ed38-201">Build the project.</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="4ed38-202">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ed38-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4ed38-203">Uruchom następujące polecenie w pmc:</span><span class="sxs-lookup"><span data-stu-id="4ed38-203">Run the following command in the PMC:</span></span>

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="4ed38-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ed38-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4ed38-205">Uruchom następujące polecenie w terminalu:</span><span class="sxs-lookup"><span data-stu-id="4ed38-205">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef migrations add RowVersion
  ```

---

<span data-ttu-id="4ed38-206">To polecenie:</span><span class="sxs-lookup"><span data-stu-id="4ed38-206">This command:</span></span>

* <span data-ttu-id="4ed38-207">Tworzy plik *migracji/{sygnatura czasowa}_RowVersion.cs.*</span><span class="sxs-lookup"><span data-stu-id="4ed38-207">Creates the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="4ed38-208">Aktualizuje plik *Migrations/SchoolContextModelSnapshot.cs.*</span><span class="sxs-lookup"><span data-stu-id="4ed38-208">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="4ed38-209">Aktualizacja dodaje następujący wyróżniony kod `BuildModel` do metody:</span><span class="sxs-lookup"><span data-stu-id="4ed38-209">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[<span data-ttu-id="4ed38-210">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ed38-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4ed38-211">Uruchom następujące polecenie w pmc:</span><span class="sxs-lookup"><span data-stu-id="4ed38-211">Run the following command in the PMC:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="4ed38-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ed38-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4ed38-213">Otwórz `Migrations/<timestamp>_RowVersion.cs` plik i dodaj podświetlony kod:</span><span class="sxs-lookup"><span data-stu-id="4ed38-213">Open the `Migrations/<timestamp>_RowVersion.cs` file and add the highlighted code:</span></span>

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  <span data-ttu-id="4ed38-214">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="4ed38-214">The preceding code:</span></span>

  * <span data-ttu-id="4ed38-215">Aktualizuje istniejące wiersze o losowe wartości obiektów blob.</span><span class="sxs-lookup"><span data-stu-id="4ed38-215">Updates existing rows with random blob values.</span></span>
  * <span data-ttu-id="4ed38-216">Dodaje wyzwalacze bazy danych, które ustawiają kolumnę RowVersion na losową wartość obiektu blob za każdym razem, gdy wiersz jest aktualizowany.</span><span class="sxs-lookup"><span data-stu-id="4ed38-216">Adds database triggers that set the RowVersion column to a random blob value whenever a row is updated.</span></span>

* <span data-ttu-id="4ed38-217">Uruchom następujące polecenie w terminalu:</span><span class="sxs-lookup"><span data-stu-id="4ed38-217">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a><span data-ttu-id="4ed38-218">Strony działu rusztowania</span><span class="sxs-lookup"><span data-stu-id="4ed38-218">Scaffold Department pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4ed38-219">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ed38-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4ed38-220">Postępuj zgodnie z instrukcjami na [stronach Scaffold Student](xref:data/ef-rp/intro#scaffold-student-pages) z następującymi wyjątkami:</span><span class="sxs-lookup"><span data-stu-id="4ed38-220">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

* <span data-ttu-id="4ed38-221">Tworzenie folderu *Strony/Działy.*</span><span class="sxs-lookup"><span data-stu-id="4ed38-221">Create a *Pages/Departments* folder.</span></span>  
* <span data-ttu-id="4ed38-222">Użyj `Department` dla klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="4ed38-222">Use `Department` for the model class.</span></span>
  * <span data-ttu-id="4ed38-223">Użyj istniejącej klasy kontekstu zamiast tworzenia nowej.</span><span class="sxs-lookup"><span data-stu-id="4ed38-223">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4ed38-224">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ed38-224">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4ed38-225">Tworzenie folderu *Strony/Działy.*</span><span class="sxs-lookup"><span data-stu-id="4ed38-225">Create a *Pages/Departments* folder.</span></span>

* <span data-ttu-id="4ed38-226">Uruchom następujące polecenie, aby smikować strony działu.</span><span class="sxs-lookup"><span data-stu-id="4ed38-226">Run the following command to scaffold the Department pages.</span></span>

  <span data-ttu-id="4ed38-227">**W systemie Windows:**</span><span class="sxs-lookup"><span data-stu-id="4ed38-227">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  <span data-ttu-id="4ed38-228">**W systemie Linux lub macOS:**</span><span class="sxs-lookup"><span data-stu-id="4ed38-228">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="4ed38-229">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="4ed38-229">Build the project.</span></span>

## <a name="update-the-index-page"></a><span data-ttu-id="4ed38-230">Aktualizowanie strony Indeks</span><span class="sxs-lookup"><span data-stu-id="4ed38-230">Update the Index page</span></span>

<span data-ttu-id="4ed38-231">Narzędzie do tworzenia szkieletów `RowVersion` utworzyło kolumnę dla strony Indeks, ale to pole nie będzie wyświetlane w aplikacji produkcyjnej.</span><span class="sxs-lookup"><span data-stu-id="4ed38-231">The scaffolding tool created a `RowVersion` column for the Index page, but that field wouldn't be displayed in a production app.</span></span> <span data-ttu-id="4ed38-232">W tym samouczku ostatni bajt `RowVersion` jest wyświetlany, aby pokazać, jak działa obsługa współbieżności.</span><span class="sxs-lookup"><span data-stu-id="4ed38-232">In this tutorial, the last byte of the `RowVersion` is displayed to help show how concurrency handling works.</span></span> <span data-ttu-id="4ed38-233">Ostatni bajt nie jest gwarantowany jako unikatowy sam w sobie.</span><span class="sxs-lookup"><span data-stu-id="4ed38-233">The last byte isn't guaranteed to be unique by itself.</span></span>

<span data-ttu-id="4ed38-234">Strona Aktualizuj *strony\Działy\Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="4ed38-234">Update *Pages\Departments\Index.cshtml* page:</span></span>

* <span data-ttu-id="4ed38-235">Zamień indeks na działy.</span><span class="sxs-lookup"><span data-stu-id="4ed38-235">Replace Index with Departments.</span></span>
* <span data-ttu-id="4ed38-236">Zmień kod zawierający, `RowVersion` aby wyświetlić tylko ostatni bajt tablicy bajtowej.</span><span class="sxs-lookup"><span data-stu-id="4ed38-236">Change the code containing `RowVersion` to show just the last byte of the byte array.</span></span>
* <span data-ttu-id="4ed38-237">Zastąp Imię FirstMidname na fullname.</span><span class="sxs-lookup"><span data-stu-id="4ed38-237">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="4ed38-238">Następujący kod pokazuje zaktualizowaną stronę:</span><span class="sxs-lookup"><span data-stu-id="4ed38-238">The following code shows the updated page:</span></span>

[!code-html[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a><span data-ttu-id="4ed38-239">Aktualizowanie modelu strony Edycja</span><span class="sxs-lookup"><span data-stu-id="4ed38-239">Update the Edit page model</span></span>

<span data-ttu-id="4ed38-240">Aktualizuj *strony\Działy\Edit.cshtml.cs* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="4ed38-240">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

<span data-ttu-id="4ed38-241">[OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) jest aktualizowana `rowVersion` o wartość z jednostki, gdy `OnGet` został pobrany w metodzie.</span><span class="sxs-lookup"><span data-stu-id="4ed38-241">The [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity when it was fetched in the `OnGet` method.</span></span> <span data-ttu-id="4ed38-242">EF Core generuje polecenie SQL UPDATE z klauzulą `RowVersion` WHERE zawierającą oryginalną wartość.</span><span class="sxs-lookup"><span data-stu-id="4ed38-242">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="4ed38-243">Jeśli polecenie UPDATE nie ma wpływu na żadne wiersze `RowVersion` (żadne `DbUpdateConcurrencyException` wiersze nie mają oryginalnej wartości), zostanie zgłoszony wyjątek.</span><span class="sxs-lookup"><span data-stu-id="4ed38-243">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

<span data-ttu-id="4ed38-244">W poprzednim wyróżnionym kodzie:</span><span class="sxs-lookup"><span data-stu-id="4ed38-244">In the preceding highlighted code:</span></span>

* <span data-ttu-id="4ed38-245">Wartość w `Department.RowVersion` jest to, co było w jednostce, gdy został pierwotnie pobrany w Get żądanie dla edytuj strony.</span><span class="sxs-lookup"><span data-stu-id="4ed38-245">The value in `Department.RowVersion` is what was in the entity when it was originally fetched in the Get request for the Edit page.</span></span> <span data-ttu-id="4ed38-246">Wartość jest dostarczana `OnPost` do metody przez ukryte pole na stronie Razor, które wyświetla encję, która ma być edytowana.</span><span class="sxs-lookup"><span data-stu-id="4ed38-246">The value is provided to the `OnPost` method by a hidden field in the Razor page that displays the entity to be edited.</span></span> <span data-ttu-id="4ed38-247">Wartość pola ukrytego jest `Department.RowVersion` kopiowana przez spinacza modelu.</span><span class="sxs-lookup"><span data-stu-id="4ed38-247">The hidden field value is copied to `Department.RowVersion` by the model binder.</span></span>
* <span data-ttu-id="4ed38-248">`OriginalValue`jest tym, co EF Core użyje w klauzuli Where.</span><span class="sxs-lookup"><span data-stu-id="4ed38-248">`OriginalValue` is what EF Core will use in the Where clause.</span></span> <span data-ttu-id="4ed38-249">Przed wyróżniony wiersz kodu wykonuje, ma wartość, która była w bazie danych, `OriginalValue` gdy `FirstOrDefaultAsync` został wywołany w tej metodzie, która może się różnić od tego, co zostało wyświetlone na edit strony.</span><span class="sxs-lookup"><span data-stu-id="4ed38-249">Before the highlighted line of code executes, `OriginalValue` has the value that was in the database when `FirstOrDefaultAsync` was called in this method, which might be different from what was displayed on the Edit page.</span></span>
* <span data-ttu-id="4ed38-250">Wyróżniony kod zapewnia, że EF Core `RowVersion` używa oryginalnej `Department` wartości z wyświetlanej jednostki w instrukcji SQL UPDATE Where klauzuli.</span><span class="sxs-lookup"><span data-stu-id="4ed38-250">The highlighted code makes sure that EF Core uses the original `RowVersion` value from the displayed `Department` entity in the SQL UPDATE statement's Where clause.</span></span>

<span data-ttu-id="4ed38-251">W przypadku wystąpienia błędu współbieżności następujący wyróżniony kod pobiera wartości klienta (wartości zaksięgowane w tej metodzie) i wartości bazy danych.</span><span class="sxs-lookup"><span data-stu-id="4ed38-251">When a concurrency error happens, the following highlighted code gets the client values (the values posted to this method) and the database values.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

<span data-ttu-id="4ed38-252">Poniższy kod dodaje niestandardowy komunikat o błędzie dla każdej kolumny, która ma wartości bazy danych inne niż to, co zostało zaksięgowane do: `OnPostAsync`</span><span class="sxs-lookup"><span data-stu-id="4ed38-252">The following code adds a custom error message for each column that has database values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

<span data-ttu-id="4ed38-253">Poniższy wyróżniony kod `RowVersion` ustawia wartość na nową wartość pobraną z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="4ed38-253">The following highlighted code sets the `RowVersion` value to the new value retrieved from the database.</span></span> <span data-ttu-id="4ed38-254">Następnym razem, gdy użytkownik kliknie **przycisk Zapisz,** zostaną przechwycone tylko błędy współbieżności, które wystąpią od ostatniego wyświetlenia strony Edytuj.</span><span class="sxs-lookup"><span data-stu-id="4ed38-254">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

<span data-ttu-id="4ed38-255">Instrukcja `ModelState.Remove` jest wymagana, `ModelState` ponieważ `RowVersion` ma starą wartość.</span><span class="sxs-lookup"><span data-stu-id="4ed38-255">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="4ed38-256">Na stronie Razor `ModelState` wartość pola ma pierwszeństwo przed wartościami właściwości modelu, gdy oba są obecne.</span><span class="sxs-lookup"><span data-stu-id="4ed38-256">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

### <a name="update-the-razor-page"></a><span data-ttu-id="4ed38-257">Aktualizowanie strony Razor</span><span class="sxs-lookup"><span data-stu-id="4ed38-257">Update the Razor page</span></span>

<span data-ttu-id="4ed38-258">Zaktualizuj *strony/działy/edit.cshtml* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="4ed38-258">Update *Pages/Departments/Edit.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="4ed38-259">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="4ed38-259">The preceding code:</span></span>

* <span data-ttu-id="4ed38-260">Aktualizuje `page` dyrektywę z `@page` do `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="4ed38-260">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="4ed38-261">Dodaje ukrytą wersję wiersza.</span><span class="sxs-lookup"><span data-stu-id="4ed38-261">Adds a hidden row version.</span></span> <span data-ttu-id="4ed38-262">`RowVersion`należy dodać, więc zaksięguj z powrotem wiąże wartość.</span><span class="sxs-lookup"><span data-stu-id="4ed38-262">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="4ed38-263">Wyświetla ostatni bajt `RowVersion` do celów debugowania.</span><span class="sxs-lookup"><span data-stu-id="4ed38-263">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="4ed38-264">`ViewData` Zastępuje silnie wpisane `InstructorNameSL`.</span><span class="sxs-lookup"><span data-stu-id="4ed38-264">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

### <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="4ed38-265">Współbieżność testu powoduje konflikty ze stroną Edytuj</span><span class="sxs-lookup"><span data-stu-id="4ed38-265">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="4ed38-266">Otwórz dwie przeglądarki wystąpień Edytuj w dziale angielskim:</span><span class="sxs-lookup"><span data-stu-id="4ed38-266">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="4ed38-267">Uruchom aplikację i wybierz pozycję Działy.</span><span class="sxs-lookup"><span data-stu-id="4ed38-267">Run the app and select Departments.</span></span>
* <span data-ttu-id="4ed38-268">Kliknij prawym przyciskiem myszy **hiperłącze Edytuj** dla działu angielskiego i wybierz pozycję Otwórz na **nowej karcie**.</span><span class="sxs-lookup"><span data-stu-id="4ed38-268">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="4ed38-269">Na pierwszej karcie kliknij **hiperłącze Edytowanie** dla działu angielskiego.</span><span class="sxs-lookup"><span data-stu-id="4ed38-269">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="4ed38-270">Dwie karty przeglądarki wyświetlają te same informacje.</span><span class="sxs-lookup"><span data-stu-id="4ed38-270">The two browser tabs display the same information.</span></span>

<span data-ttu-id="4ed38-271">Zmień nazwę na pierwszej karcie przeglądarki i kliknij przycisk **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="4ed38-271">Change the name in the first browser tab and click **Save**.</span></span>

![Edycja działu strona 1 po zmianie](concurrency/_static/edit-after-change-130.png)

<span data-ttu-id="4ed38-273">Przeglądarka pokazuje stronę Indeks ze zmienioną wartością i zaktualizowanym wskaźnikiem wierszVersion.</span><span class="sxs-lookup"><span data-stu-id="4ed38-273">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="4ed38-274">Zwróć uwagę na zaktualizowany wskaźnik wierszWyjaśny, jest wyświetlany na drugim odświeżeniu zwrotnym na drugiej karcie.</span><span class="sxs-lookup"><span data-stu-id="4ed38-274">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="4ed38-275">Zmień inne pole na drugiej karcie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="4ed38-275">Change a different field in the second browser tab.</span></span>

![Edycja działu strona 2 po zmianie](concurrency/_static/edit-after-change-230.png)

<span data-ttu-id="4ed38-277">Kliknij przycisk **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="4ed38-277">Click **Save**.</span></span> <span data-ttu-id="4ed38-278">Są widoczne komunikaty o błędach dla wszystkich pól, które nie są zgodne z wartościami bazy danych:</span><span class="sxs-lookup"><span data-stu-id="4ed38-278">You see error messages for all fields that don't match the database values:</span></span>

![Komunikat o błędzie strony edycji działu](concurrency/_static/edit-error30.png)

<span data-ttu-id="4ed38-280">W tym oknie przeglądarki nie ma zamiaru zmieniać pola Nazwa.</span><span class="sxs-lookup"><span data-stu-id="4ed38-280">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="4ed38-281">Skopiuj i wklej bieżącą wartość (Języki) do pola Nazwa.</span><span class="sxs-lookup"><span data-stu-id="4ed38-281">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="4ed38-282">Wyjmowanie karty. Sprawdzanie poprawności po stronie klienta usuwa komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="4ed38-282">Tab out. Client-side validation removes the error message.</span></span>

<span data-ttu-id="4ed38-283">Kliknij **pozycję Zapisz** ponownie.</span><span class="sxs-lookup"><span data-stu-id="4ed38-283">Click **Save** again.</span></span> <span data-ttu-id="4ed38-284">Wartość wprowadzona na drugiej karcie przeglądarki zostanie zapisana.</span><span class="sxs-lookup"><span data-stu-id="4ed38-284">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="4ed38-285">Zapisane wartości są widoczne na stronie Indeks.</span><span class="sxs-lookup"><span data-stu-id="4ed38-285">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="4ed38-286">Aktualizowanie strony Usuwanie</span><span class="sxs-lookup"><span data-stu-id="4ed38-286">Update the Delete page</span></span>

<span data-ttu-id="4ed38-287">Zaktualizuj *strony/działy/usuń.cshtml.cs* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="4ed38-287">Update *Pages/Departments/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="4ed38-288">Strona Usuń wykrywa konflikty współbieżności, gdy jednostka została zmieniona po pobraniu.</span><span class="sxs-lookup"><span data-stu-id="4ed38-288">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="4ed38-289">`Department.RowVersion`jest wersją wiersza, gdy jednostka została pobrana.</span><span class="sxs-lookup"><span data-stu-id="4ed38-289">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="4ed38-290">Gdy EF Core tworzy polecenie SQL DELETE, `RowVersion`zawiera klauzulę WHERE z plikiem .</span><span class="sxs-lookup"><span data-stu-id="4ed38-290">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="4ed38-291">Jeśli polecenie SQL DELETE powoduje zero wierszy, których to dotyczy:</span><span class="sxs-lookup"><span data-stu-id="4ed38-291">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="4ed38-292">Polecenie `RowVersion` SQL DELETE w programie `RowVersion` SQL nie jest zgodne w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="4ed38-292">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the database.</span></span>
* <span data-ttu-id="4ed38-293">A DbUpdateConcurrencyException wyjątek jest zgłaszany.</span><span class="sxs-lookup"><span data-stu-id="4ed38-293">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="4ed38-294">`OnGetAsync`nazywa się `concurrencyError`z .</span><span class="sxs-lookup"><span data-stu-id="4ed38-294">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-razor-page"></a><span data-ttu-id="4ed38-295">Aktualizowanie strony Usuń maszynkę do golenia</span><span class="sxs-lookup"><span data-stu-id="4ed38-295">Update the Delete Razor page</span></span>

<span data-ttu-id="4ed38-296">Zaktualizuj *strony/działy/usuń.cshtml* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="4ed38-296">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="4ed38-297">Poprzedni kod wprowadza następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="4ed38-297">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="4ed38-298">Aktualizuje `page` dyrektywę z `@page` do `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="4ed38-298">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="4ed38-299">Dodaje komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="4ed38-299">Adds an error message.</span></span>
* <span data-ttu-id="4ed38-300">Zastępuje program FirstMidName na fullname w polu **Administrator.**</span><span class="sxs-lookup"><span data-stu-id="4ed38-300">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="4ed38-301">Zmiany `RowVersion` w celu wyświetlenia ostatniego bajtu.</span><span class="sxs-lookup"><span data-stu-id="4ed38-301">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="4ed38-302">Dodaje ukrytą wersję wiersza.</span><span class="sxs-lookup"><span data-stu-id="4ed38-302">Adds a hidden row version.</span></span> <span data-ttu-id="4ed38-303">`RowVersion`należy dodać, aby postgit add back powiązać wartość.</span><span class="sxs-lookup"><span data-stu-id="4ed38-303">`RowVersion` must be added so postgit add back binds the value.</span></span>

### <a name="test-concurrency-conflicts"></a><span data-ttu-id="4ed38-304">Testowanie konfliktów współbieżności</span><span class="sxs-lookup"><span data-stu-id="4ed38-304">Test concurrency conflicts</span></span>

<span data-ttu-id="4ed38-305">Tworzenie działu testowego.</span><span class="sxs-lookup"><span data-stu-id="4ed38-305">Create a test department.</span></span>

<span data-ttu-id="4ed38-306">Otwórz dwie przeglądarki wystąpień Delete w dziale testowym:</span><span class="sxs-lookup"><span data-stu-id="4ed38-306">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="4ed38-307">Uruchom aplikację i wybierz pozycję Działy.</span><span class="sxs-lookup"><span data-stu-id="4ed38-307">Run the app and select Departments.</span></span>
* <span data-ttu-id="4ed38-308">Kliknij prawym przyciskiem myszy **usuń** hiperłącze dla działu testowego i wybierz pozycję Otwórz na **nowej karcie**.</span><span class="sxs-lookup"><span data-stu-id="4ed38-308">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="4ed38-309">Kliknij **hiperłącze Edytuj** dla działu testowego.</span><span class="sxs-lookup"><span data-stu-id="4ed38-309">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="4ed38-310">Dwie karty przeglądarki wyświetlają te same informacje.</span><span class="sxs-lookup"><span data-stu-id="4ed38-310">The two browser tabs display the same information.</span></span>

<span data-ttu-id="4ed38-311">Zmień budżet na pierwszej karcie przeglądarki i kliknij przycisk **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="4ed38-311">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="4ed38-312">Przeglądarka pokazuje stronę Indeks ze zmienioną wartością i zaktualizowanym wskaźnikiem wierszVersion.</span><span class="sxs-lookup"><span data-stu-id="4ed38-312">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="4ed38-313">Zwróć uwagę na zaktualizowany wskaźnik wierszWyjaśny, jest wyświetlany na drugim odświeżeniu zwrotnym na drugiej karcie.</span><span class="sxs-lookup"><span data-stu-id="4ed38-313">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="4ed38-314">Usuń dział testowy z drugiej karty. Błąd współbieżności jest wyświetlany z bieżącymi wartościami z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="4ed38-314">Delete the test department from the second tab. A concurrency error is display with the current values from the database.</span></span> <span data-ttu-id="4ed38-315">Kliknięcie **przycisku Usuń** powoduje `RowVersion` usunięcie encji, chyba że została usunięta..</span><span class="sxs-lookup"><span data-stu-id="4ed38-315">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.department has been deleted.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4ed38-316">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="4ed38-316">Additional resources</span></span>

* [<span data-ttu-id="4ed38-317">Tokeny współbieżności w EF Core</span><span class="sxs-lookup"><span data-stu-id="4ed38-317">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="4ed38-318">Obsługa współbieżności w EF Core</span><span class="sxs-lookup"><span data-stu-id="4ed38-318">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="4ed38-319">Debugowanie ASP.NET core 2.x źródło</span><span class="sxs-lookup"><span data-stu-id="4ed38-319">Debugging ASP.NET Core 2.x source</span></span>](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a><span data-ttu-id="4ed38-320">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="4ed38-320">Next steps</span></span>

<span data-ttu-id="4ed38-321">Jest to ostatni samouczek z serii.</span><span class="sxs-lookup"><span data-stu-id="4ed38-321">This is the last tutorial in the series.</span></span> <span data-ttu-id="4ed38-322">Dodatkowe tematy są omówione w [wersji MVC z tej serii samouczków](xref:data/ef-mvc/index).</span><span class="sxs-lookup"><span data-stu-id="4ed38-322">Additional topics are covered in the [MVC version of this tutorial series](xref:data/ef-mvc/index).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="4ed38-323">Poprzedni samouczek</span><span class="sxs-lookup"><span data-stu-id="4ed38-323">Previous tutorial</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4ed38-324">W tym samouczku pokazano, jak obsługiwać konflikty, gdy wielu użytkowników aktualizuje jednostkę jednocześnie (w tym samym czasie).</span><span class="sxs-lookup"><span data-stu-id="4ed38-324">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span> <span data-ttu-id="4ed38-325">Jeśli napotkasz problemy, których nie możesz rozwiązać, [pobierz lub wyświetl ukończoną aplikację.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="4ed38-325">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="4ed38-326">[Pobierz instrukcje](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="4ed38-326">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="4ed38-327">Konflikty współbieżności</span><span class="sxs-lookup"><span data-stu-id="4ed38-327">Concurrency conflicts</span></span>

<span data-ttu-id="4ed38-328">Konflikt współbieżności występuje, gdy:</span><span class="sxs-lookup"><span data-stu-id="4ed38-328">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="4ed38-329">Użytkownik przechodzi do strony edycji encji.</span><span class="sxs-lookup"><span data-stu-id="4ed38-329">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="4ed38-330">Inny użytkownik aktualizuje tę samą jednostkę przed zapisaniem pierwszej zmiany użytkownika w db.</span><span class="sxs-lookup"><span data-stu-id="4ed38-330">Another user updates the same entity before the first user's change is written to the DB.</span></span>

<span data-ttu-id="4ed38-331">Jeśli wykrywanie współbieżności nie jest włączone, gdy występują równoczesne aktualizacje:</span><span class="sxs-lookup"><span data-stu-id="4ed38-331">If concurrency detection isn't enabled, when concurrent updates occur:</span></span>

* <span data-ttu-id="4ed38-332">Ostatnia aktualizacja wygrywa.</span><span class="sxs-lookup"><span data-stu-id="4ed38-332">The last update wins.</span></span> <span data-ttu-id="4ed38-333">Oznacza to, że ostatnie wartości aktualizacji są zapisywane w bazy danych.</span><span class="sxs-lookup"><span data-stu-id="4ed38-333">That is, the last update values are saved to the DB.</span></span>
* <span data-ttu-id="4ed38-334">Pierwsza z bieżących aktualizacji zostanie utracona.</span><span class="sxs-lookup"><span data-stu-id="4ed38-334">The first of the current updates are lost.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="4ed38-335">Optymistyczna współbieżność</span><span class="sxs-lookup"><span data-stu-id="4ed38-335">Optimistic concurrency</span></span>

<span data-ttu-id="4ed38-336">Optymistyczna współbieżność umożliwia konflikty współbieżności się zdarzyć, a następnie reaguje odpowiednio, gdy to robią.</span><span class="sxs-lookup"><span data-stu-id="4ed38-336">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="4ed38-337">Na przykład Jane odwiedza stronę edycji departamentu i zmienia budżet działu angielskiego z 350 000,00 USD na 0,00 USD.</span><span class="sxs-lookup"><span data-stu-id="4ed38-337">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Zmiana budżetu na 0](concurrency/_static/change-budget.png)

<span data-ttu-id="4ed38-339">Zanim Jane kliknie **przycisk Zapisz,** Jan odwiedza tę samą stronę i zmienia pole Data rozpoczęcia z 1/2007 na 9/1/2013.</span><span class="sxs-lookup"><span data-stu-id="4ed38-339">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Zmiana daty rozpoczęcia na 2013 r.](concurrency/_static/change-date.png)

<span data-ttu-id="4ed38-341">Jane klika **najpierw przycisk Zapisz** i widzi jej zmianę, gdy przeglądarka wyświetla stronę Indeks.</span><span class="sxs-lookup"><span data-stu-id="4ed38-341">Jane clicks **Save** first and sees her change when the browser displays the Index page.</span></span>

![Budżet zmieniony na zero](concurrency/_static/budget-zero.png)

<span data-ttu-id="4ed38-343">Jan klika **zapisz** na stronie Edycji, która nadal pokazuje budżet 350 000,00 USD.</span><span class="sxs-lookup"><span data-stu-id="4ed38-343">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="4ed38-344">Co dzieje się dalej zależy od sposobu obsługi konfliktów współbieżności.</span><span class="sxs-lookup"><span data-stu-id="4ed38-344">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="4ed38-345">Optymistyczna współbieżność obejmuje następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="4ed38-345">Optimistic concurrency includes the following options:</span></span>

* <span data-ttu-id="4ed38-346">Można śledzić, która właściwość użytkownik zmodyfikował i zaktualizować tylko odpowiednie kolumny w db.</span><span class="sxs-lookup"><span data-stu-id="4ed38-346">You can keep track of which property a user has modified and update only the corresponding columns in the DB.</span></span>

  <span data-ttu-id="4ed38-347">W scenariuszu żadne dane nie zostaną utracone.</span><span class="sxs-lookup"><span data-stu-id="4ed38-347">In the scenario, no data would be lost.</span></span> <span data-ttu-id="4ed38-348">Różne właściwości zostały zaktualizowane przez dwóch użytkowników.</span><span class="sxs-lookup"><span data-stu-id="4ed38-348">Different properties were updated by the two users.</span></span> <span data-ttu-id="4ed38-349">Następnym razem, gdy ktoś przegląda angielski dział, zobaczą zmiany zarówno Jane, jak i Johna.</span><span class="sxs-lookup"><span data-stu-id="4ed38-349">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="4ed38-350">Ta metoda aktualizacji może zmniejszyć liczbę konfliktów, które mogą spowodować utratę danych.</span><span class="sxs-lookup"><span data-stu-id="4ed38-350">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="4ed38-351">Podejście to:</span><span class="sxs-lookup"><span data-stu-id="4ed38-351">This approach:</span></span>
 
  * <span data-ttu-id="4ed38-352">Nie można uniknąć utraty danych, jeśli konkurencyjne zmiany są wprowadzane do tej samej właściwości.</span><span class="sxs-lookup"><span data-stu-id="4ed38-352">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="4ed38-353">Ogólnie nie jest praktyczne w aplikacji sieci web.</span><span class="sxs-lookup"><span data-stu-id="4ed38-353">Is generally not practical in a web app.</span></span> <span data-ttu-id="4ed38-354">Wymaga utrzymania istotnego stanu w celu śledzenia wszystkich pobranych wartości i nowych wartości.</span><span class="sxs-lookup"><span data-stu-id="4ed38-354">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="4ed38-355">Utrzymywanie dużych ilości stanu może mieć wpływ na wydajność aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4ed38-355">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="4ed38-356">Może zwiększyć złożoność aplikacji w porównaniu do wykrywania współbieżności w jednostce.</span><span class="sxs-lookup"><span data-stu-id="4ed38-356">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="4ed38-357">Możesz pozwolić, aby zmiana Johna nadpisyła zmianę Jane.</span><span class="sxs-lookup"><span data-stu-id="4ed38-357">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="4ed38-358">Następnym razem, gdy ktoś przegląda dział angielski, zobaczy 9/1/2013 i pobraną wartość $350,000.00.</span><span class="sxs-lookup"><span data-stu-id="4ed38-358">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="4ed38-359">Takie podejście jest nazywany *klient wins* lub *Ostatni w* wins scenariusza.</span><span class="sxs-lookup"><span data-stu-id="4ed38-359">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="4ed38-360">(Wszystkie wartości od klienta mają pierwszeństwo przed tym, co znajduje się w magazynie danych). Jeśli nie wykonasz żadnego kodowania do obsługi współbieżności, wygrywa klient odbywa się automatycznie.</span><span class="sxs-lookup"><span data-stu-id="4ed38-360">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="4ed38-361">Można zapobiec zmiany Jana z aktualizowaniem w db.</span><span class="sxs-lookup"><span data-stu-id="4ed38-361">You can prevent John's change from being updated in the DB.</span></span> <span data-ttu-id="4ed38-362">Zazwyczaj aplikacja będzie:</span><span class="sxs-lookup"><span data-stu-id="4ed38-362">Typically, the app would:</span></span>

  * <span data-ttu-id="4ed38-363">Wyświetl komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="4ed38-363">Display an error message.</span></span>
  * <span data-ttu-id="4ed38-364">Pokaż bieżący stan danych.</span><span class="sxs-lookup"><span data-stu-id="4ed38-364">Show the current state of the data.</span></span>
  * <span data-ttu-id="4ed38-365">Zezwalaj użytkownikowi na ponowne zastosowanie zmian.</span><span class="sxs-lookup"><span data-stu-id="4ed38-365">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="4ed38-366">Jest to tak zwany scenariusz *Wygrane sklepu.*</span><span class="sxs-lookup"><span data-stu-id="4ed38-366">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="4ed38-367">(Wartości magazynu danych mają pierwszeństwo przed wartościami przesłane przez klienta). Zaimplementowanie scenariusza Wygrane sklepu w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="4ed38-367">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="4ed38-368">Ta metoda gwarantuje, że żadne zmiany nie są zastępowane bez użytkownika jest alerty.</span><span class="sxs-lookup"><span data-stu-id="4ed38-368">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="handling-concurrency"></a><span data-ttu-id="4ed38-369">Obsługa współbieżności</span><span class="sxs-lookup"><span data-stu-id="4ed38-369">Handling concurrency</span></span> 

<span data-ttu-id="4ed38-370">Gdy właściwość jest skonfigurowana jako [token współbieżności:](/ef/core/modeling/concurrency)</span><span class="sxs-lookup"><span data-stu-id="4ed38-370">When a property is configured as a [concurrency token](/ef/core/modeling/concurrency):</span></span>

* <span data-ttu-id="4ed38-371">EF Core sprawdza, że właściwość nie została zmodyfikowana po pobraniu.</span><span class="sxs-lookup"><span data-stu-id="4ed38-371">EF Core verifies that property has not been modified after it was fetched.</span></span> <span data-ttu-id="4ed38-372">Sprawdzanie występuje, gdy [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) lub [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="4ed38-372">The check occurs when [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) or [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span>
* <span data-ttu-id="4ed38-373">Jeśli właściwość została zmieniona po pobraniu, [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) jest generowany.</span><span class="sxs-lookup"><span data-stu-id="4ed38-373">If the property has been changed after it was fetched, a [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) is thrown.</span></span> 

<span data-ttu-id="4ed38-374">Baza danych i model danych muszą być `DbUpdateConcurrencyException`skonfigurowane do obsługi rzucania .</span><span class="sxs-lookup"><span data-stu-id="4ed38-374">The DB and data model must be configured to support throwing `DbUpdateConcurrencyException`.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-property"></a><span data-ttu-id="4ed38-375">Wykrywanie konfliktów współbieżności we właściwości</span><span class="sxs-lookup"><span data-stu-id="4ed38-375">Detecting concurrency conflicts on a property</span></span>

<span data-ttu-id="4ed38-376">Konflikty współbieżności można wykryć na poziomie właściwości za pomocą [atrybutu ConcurrencyCheck.](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0)</span><span class="sxs-lookup"><span data-stu-id="4ed38-376">Concurrency conflicts can be detected at the property level with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0) attribute.</span></span> <span data-ttu-id="4ed38-377">Atrybut można zastosować do wielu właściwości modelu.</span><span class="sxs-lookup"><span data-stu-id="4ed38-377">The attribute can be applied to multiple properties on the model.</span></span> <span data-ttu-id="4ed38-378">Aby uzyskać więcej informacji, zobacz [Annotacje danych-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span><span class="sxs-lookup"><span data-stu-id="4ed38-378">For more information, see [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span></span>

<span data-ttu-id="4ed38-379">Atrybut `[ConcurrencyCheck]` nie jest używany w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="4ed38-379">The `[ConcurrencyCheck]` attribute isn't used in this tutorial.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-row"></a><span data-ttu-id="4ed38-380">Wykrywanie konfliktów współbieżności w wierszu</span><span class="sxs-lookup"><span data-stu-id="4ed38-380">Detecting concurrency conflicts on a row</span></span>

<span data-ttu-id="4ed38-381">Aby wykryć konflikty współbieżności, kolumna śledzenia [wierszy](/sql/t-sql/data-types/rowversion-transact-sql) jest dodawany do modelu.</span><span class="sxs-lookup"><span data-stu-id="4ed38-381">To detect concurrency conflicts, a [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) tracking column is added to the model.</span></span>  <span data-ttu-id="4ed38-382">`rowversion` :</span><span class="sxs-lookup"><span data-stu-id="4ed38-382">`rowversion` :</span></span>

* <span data-ttu-id="4ed38-383">Czy sql server specyficzne.</span><span class="sxs-lookup"><span data-stu-id="4ed38-383">Is SQL Server specific.</span></span> <span data-ttu-id="4ed38-384">Inne bazy danych mogą nie zapewniać podobnej funkcji.</span><span class="sxs-lookup"><span data-stu-id="4ed38-384">Other databases may not provide a similar feature.</span></span>
* <span data-ttu-id="4ed38-385">Służy do określenia, że jednostka nie została zmieniona, ponieważ została pobrana z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="4ed38-385">Is used to determine that an entity has not been changed since it was fetched from the DB.</span></span> 

<span data-ttu-id="4ed38-386">Baza danych generuje `rowversion` kolejny numer, który jest zwiększany za każdym razem, gdy wiersz jest aktualizowany.</span><span class="sxs-lookup"><span data-stu-id="4ed38-386">The DB generates a sequential `rowversion` number that's incremented each time the row is updated.</span></span> <span data-ttu-id="4ed38-387">W `Update` poleceniu lub `Delete` w poleceniu `Where` `rowversion`klauzula zawiera pobraną wartość .</span><span class="sxs-lookup"><span data-stu-id="4ed38-387">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of `rowversion`.</span></span> <span data-ttu-id="4ed38-388">Jeśli zaktualizowany wiersz uległ zmianie:</span><span class="sxs-lookup"><span data-stu-id="4ed38-388">If the row being updated has changed:</span></span>

* <span data-ttu-id="4ed38-389">`rowversion`nie pasuje do pobranej wartości.</span><span class="sxs-lookup"><span data-stu-id="4ed38-389">`rowversion` doesn't match the fetched value.</span></span>
* <span data-ttu-id="4ed38-390">Polecenia `Update` `Delete` lub polecenia nie znajdują wiersza, ponieważ `Where` klauzula `rowversion`zawiera pobrane .</span><span class="sxs-lookup"><span data-stu-id="4ed38-390">The `Update` or `Delete` commands don't find a row because the `Where` clause includes the fetched `rowversion`.</span></span>
* <span data-ttu-id="4ed38-391">A `DbUpdateConcurrencyException` jest wyrzucany.</span><span class="sxs-lookup"><span data-stu-id="4ed38-391">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="4ed38-392">W EF Core, gdy żadne wiersze `Update` `Delete` nie zostały zaktualizowane przez polecenie lub, wyjątek współbieżności jest zgłaszany.</span><span class="sxs-lookup"><span data-stu-id="4ed38-392">In EF Core, when no rows have been updated by an `Update` or `Delete` command, a concurrency exception is thrown.</span></span>

### <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="4ed38-393">Dodawanie właściwości śledzenia do encji Dział</span><span class="sxs-lookup"><span data-stu-id="4ed38-393">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="4ed38-394">W *models/department.cs*dodaj właściwość śledzenia o nazwie RowVersion:</span><span class="sxs-lookup"><span data-stu-id="4ed38-394">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="4ed38-395">[Atrybut sygnatury czasowej](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) określa, że `Where` ta `Update` kolumna znajduje się w klauzuli i `Delete` polecenia.</span><span class="sxs-lookup"><span data-stu-id="4ed38-395">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute specifies that this column is included in the `Where` clause of `Update` and `Delete` commands.</span></span> <span data-ttu-id="4ed38-396">Atrybut jest wywoływany, `Timestamp` ponieważ poprzednie wersje `timestamp` programu SQL Server `rowversion` używały typu danych SQL, zanim typ SQL zastąpił go.</span><span class="sxs-lookup"><span data-stu-id="4ed38-396">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` type replaced it.</span></span>

<span data-ttu-id="4ed38-397">Płynny interfejs API można również określić właściwości śledzenia:</span><span class="sxs-lookup"><span data-stu-id="4ed38-397">The fluent API can also specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

<span data-ttu-id="4ed38-398">Poniższy kod przedstawia część T-SQL generowane przez EF Core, gdy nazwa działu jest aktualizowana:</span><span class="sxs-lookup"><span data-stu-id="4ed38-398">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

<span data-ttu-id="4ed38-399">Poprzedni wyróżniony kod przedstawia `WHERE` klauzulę `RowVersion`zawierającą .</span><span class="sxs-lookup"><span data-stu-id="4ed38-399">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="4ed38-400">Jeśli baza `RowVersion` danych nie jest `RowVersion` równa`@p2`parametrowi ( ), żadne wiersze nie są aktualizowane.</span><span class="sxs-lookup"><span data-stu-id="4ed38-400">If the DB `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="4ed38-401">Poniższy wyróżniony kod pokazuje T-SQL, który weryfikuje dokładnie jeden wiersz został zaktualizowany:</span><span class="sxs-lookup"><span data-stu-id="4ed38-401">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

<span data-ttu-id="4ed38-402">[@@ROWCOUNT ](/sql/t-sql/functions/rowcount-transact-sql) zwraca liczbę wierszy, których dotyczy ostatnia instrukcja.</span><span class="sxs-lookup"><span data-stu-id="4ed38-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="4ed38-403">W żadnym wierszu nie są aktualizowane, EF Core rzuca . `DbUpdateConcurrencyException`</span><span class="sxs-lookup"><span data-stu-id="4ed38-403">In no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="4ed38-404">Można zobaczyć T-SQL EF Core generuje w oknie danych wyjściowych programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4ed38-404">You can see the T-SQL EF Core generates in the output window of Visual Studio.</span></span>

### <a name="update-the-db"></a><span data-ttu-id="4ed38-405">Aktualizowanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="4ed38-405">Update the DB</span></span>

<span data-ttu-id="4ed38-406">Dodanie `RowVersion` właściwości zmienia model bazy danych, który wymaga migracji.</span><span class="sxs-lookup"><span data-stu-id="4ed38-406">Adding the `RowVersion` property changes the DB model, which requires a migration.</span></span>

<span data-ttu-id="4ed38-407">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="4ed38-407">Build the project.</span></span> <span data-ttu-id="4ed38-408">W oknie polecenia wprowadź następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="4ed38-408">Enter the following in a command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

<span data-ttu-id="4ed38-409">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="4ed38-409">The preceding commands:</span></span>

* <span data-ttu-id="4ed38-410">Dodaje plik *migracji/{sygnatura czasowa}_RowVersion.cs.*</span><span class="sxs-lookup"><span data-stu-id="4ed38-410">Adds the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="4ed38-411">Aktualizuje plik *Migrations/SchoolContextModelSnapshot.cs.*</span><span class="sxs-lookup"><span data-stu-id="4ed38-411">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="4ed38-412">Aktualizacja dodaje następujący wyróżniony kod `BuildModel` do metody:</span><span class="sxs-lookup"><span data-stu-id="4ed38-412">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* <span data-ttu-id="4ed38-413">Uruchamia migracje, aby zaktualizować bazę danych.</span><span class="sxs-lookup"><span data-stu-id="4ed38-413">Runs migrations to update the DB.</span></span>

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a><span data-ttu-id="4ed38-414">Rusztowanie modelu działów</span><span class="sxs-lookup"><span data-stu-id="4ed38-414">Scaffold the Departments model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4ed38-415">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ed38-415">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="4ed38-416">Postępuj zgodnie z instrukcjami w [rusztowania modelu ucznia](xref:data/ef-rp/intro#scaffold-student-pages) i używać `Department` dla klasy modelu.</span><span class="sxs-lookup"><span data-stu-id="4ed38-416">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Department` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4ed38-417">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ed38-417">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="4ed38-418">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="4ed38-418">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="4ed38-419">Poprzednie polecenie scaffolds `Department` modelu.</span><span class="sxs-lookup"><span data-stu-id="4ed38-419">The preceding command scaffolds the `Department` model.</span></span> <span data-ttu-id="4ed38-420">Otwórz projekt w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4ed38-420">Open the project in Visual Studio.</span></span>

<span data-ttu-id="4ed38-421">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="4ed38-421">Build the project.</span></span>

### <a name="update-the-departments-index-page"></a><span data-ttu-id="4ed38-422">Aktualizowanie strony Indeks działów</span><span class="sxs-lookup"><span data-stu-id="4ed38-422">Update the Departments Index page</span></span>

<span data-ttu-id="4ed38-423">Aparat szkieletu utworzył kolumnę `RowVersion` dla strony Indeks, ale to pole nie powinno być wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="4ed38-423">The scaffolding engine created a `RowVersion` column for the Index page, but that field shouldn't be displayed.</span></span> <span data-ttu-id="4ed38-424">W tym samouczku ostatni bajt `RowVersion` jest wyświetlany, aby ułatwić zrozumienie współbieżności.</span><span class="sxs-lookup"><span data-stu-id="4ed38-424">In this tutorial, the last byte of the `RowVersion` is displayed to help understand concurrency.</span></span> <span data-ttu-id="4ed38-425">Ostatni bajt nie jest gwarantowany jako unikatowy.</span><span class="sxs-lookup"><span data-stu-id="4ed38-425">The last byte isn't guaranteed to be unique.</span></span> <span data-ttu-id="4ed38-426">Prawdziwa aplikacja nie będzie `RowVersion` wyświetlana lub `RowVersion`ostatni bajt .</span><span class="sxs-lookup"><span data-stu-id="4ed38-426">A real app wouldn't display `RowVersion` or the last byte of `RowVersion`.</span></span>

<span data-ttu-id="4ed38-427">Zaktualizuj stronę Indeks:</span><span class="sxs-lookup"><span data-stu-id="4ed38-427">Update the Index page:</span></span>

* <span data-ttu-id="4ed38-428">Zamień indeks na działy.</span><span class="sxs-lookup"><span data-stu-id="4ed38-428">Replace Index with Departments.</span></span>
* <span data-ttu-id="4ed38-429">Zastąp znaczniki zawierające `RowVersion` ostatni `RowVersion`bajt .</span><span class="sxs-lookup"><span data-stu-id="4ed38-429">Replace the markup containing `RowVersion` with the last byte of `RowVersion`.</span></span>
* <span data-ttu-id="4ed38-430">Zastąp Imię FirstMidname na fullname.</span><span class="sxs-lookup"><span data-stu-id="4ed38-430">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="4ed38-431">Zaktualizowana strona pokazuje zaktualizowaną stronę:</span><span class="sxs-lookup"><span data-stu-id="4ed38-431">The following markup shows the updated page:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a><span data-ttu-id="4ed38-432">Aktualizowanie modelu strony Edycja</span><span class="sxs-lookup"><span data-stu-id="4ed38-432">Update the Edit page model</span></span>

<span data-ttu-id="4ed38-433">Aktualizuj *strony\Działy\Edit.cshtml.cs* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="4ed38-433">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="4ed38-434">Aby wykryć problem współbieżności, [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) `rowVersion` jest aktualizowany o wartość z jednostki, która została pobrana.</span><span class="sxs-lookup"><span data-stu-id="4ed38-434">To detect a concurrency issue, the [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity it was fetched.</span></span> <span data-ttu-id="4ed38-435">EF Core generuje polecenie SQL UPDATE z klauzulą `RowVersion` WHERE zawierającą oryginalną wartość.</span><span class="sxs-lookup"><span data-stu-id="4ed38-435">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="4ed38-436">Jeśli polecenie UPDATE nie ma wpływu na żadne wiersze `RowVersion` (żadne `DbUpdateConcurrencyException` wiersze nie mają oryginalnej wartości), zostanie zgłoszony wyjątek.</span><span class="sxs-lookup"><span data-stu-id="4ed38-436">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

<span data-ttu-id="4ed38-437">W poprzednim kodzie `Department.RowVersion` jest wartość, gdy jednostka została pobrana.</span><span class="sxs-lookup"><span data-stu-id="4ed38-437">In the preceding code, `Department.RowVersion` is the value when the entity was fetched.</span></span> <span data-ttu-id="4ed38-438">`OriginalValue`jest wartością w DB, gdy `FirstOrDefaultAsync` został wywołany w tej metodzie.</span><span class="sxs-lookup"><span data-stu-id="4ed38-438">`OriginalValue` is the value in the DB when `FirstOrDefaultAsync` was called in this method.</span></span>

<span data-ttu-id="4ed38-439">Następujący kod pobiera wartości klienta (wartości zaksięgowane w tej metodzie) i wartości bazy danych:</span><span class="sxs-lookup"><span data-stu-id="4ed38-439">The following code gets the client values (the values posted to this method) and the DB values:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

<span data-ttu-id="4ed38-440">Poniższy kod dodaje niestandardowy komunikat o błędzie dla każdej kolumny, `OnPostAsync`która ma wartości bazy danych różni się od tego, co zostało zaksięgowane do:</span><span class="sxs-lookup"><span data-stu-id="4ed38-440">The following code adds a custom error message for each column that has DB values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

<span data-ttu-id="4ed38-441">Poniższy wyróżniony kod `RowVersion` ustawia wartość na nową wartość pobraną z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="4ed38-441">The following highlighted code sets the `RowVersion` value to the new value retrieved from the DB.</span></span> <span data-ttu-id="4ed38-442">Następnym razem, gdy użytkownik kliknie **przycisk Zapisz,** zostaną przechwycone tylko błędy współbieżności, które wystąpią od ostatniego wyświetlenia strony Edytuj.</span><span class="sxs-lookup"><span data-stu-id="4ed38-442">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

<span data-ttu-id="4ed38-443">Instrukcja `ModelState.Remove` jest wymagana, `ModelState` ponieważ `RowVersion` ma starą wartość.</span><span class="sxs-lookup"><span data-stu-id="4ed38-443">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="4ed38-444">Na stronie Razor `ModelState` wartość pola ma pierwszeństwo przed wartościami właściwości modelu, gdy oba są obecne.</span><span class="sxs-lookup"><span data-stu-id="4ed38-444">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="4ed38-445">Aktualizowanie strony edycji</span><span class="sxs-lookup"><span data-stu-id="4ed38-445">Update the Edit page</span></span>

<span data-ttu-id="4ed38-446">Zaktualizuj *strony/działy/edit.cshtml* za pomocą następujących znaczników:</span><span class="sxs-lookup"><span data-stu-id="4ed38-446">Update *Pages/Departments/Edit.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="4ed38-447">Powyższe znaczniki:</span><span class="sxs-lookup"><span data-stu-id="4ed38-447">The preceding markup:</span></span>

* <span data-ttu-id="4ed38-448">Aktualizuje `page` dyrektywę z `@page` do `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="4ed38-448">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="4ed38-449">Dodaje ukrytą wersję wiersza.</span><span class="sxs-lookup"><span data-stu-id="4ed38-449">Adds a hidden row version.</span></span> <span data-ttu-id="4ed38-450">`RowVersion`należy dodać, więc zaksięguj z powrotem wiąże wartość.</span><span class="sxs-lookup"><span data-stu-id="4ed38-450">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="4ed38-451">Wyświetla ostatni bajt `RowVersion` do celów debugowania.</span><span class="sxs-lookup"><span data-stu-id="4ed38-451">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="4ed38-452">`ViewData` Zastępuje silnie wpisane `InstructorNameSL`.</span><span class="sxs-lookup"><span data-stu-id="4ed38-452">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

## <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="4ed38-453">Współbieżność testu powoduje konflikty ze stroną Edytuj</span><span class="sxs-lookup"><span data-stu-id="4ed38-453">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="4ed38-454">Otwórz dwie przeglądarki wystąpień Edytuj w dziale angielskim:</span><span class="sxs-lookup"><span data-stu-id="4ed38-454">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="4ed38-455">Uruchom aplikację i wybierz pozycję Działy.</span><span class="sxs-lookup"><span data-stu-id="4ed38-455">Run the app and select Departments.</span></span>
* <span data-ttu-id="4ed38-456">Kliknij prawym przyciskiem myszy **hiperłącze Edytuj** dla działu angielskiego i wybierz pozycję Otwórz na **nowej karcie**.</span><span class="sxs-lookup"><span data-stu-id="4ed38-456">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="4ed38-457">Na pierwszej karcie kliknij **hiperłącze Edytowanie** dla działu angielskiego.</span><span class="sxs-lookup"><span data-stu-id="4ed38-457">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="4ed38-458">Dwie karty przeglądarki wyświetlają te same informacje.</span><span class="sxs-lookup"><span data-stu-id="4ed38-458">The two browser tabs display the same information.</span></span>

<span data-ttu-id="4ed38-459">Zmień nazwę na pierwszej karcie przeglądarki i kliknij przycisk **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="4ed38-459">Change the name in the first browser tab and click **Save**.</span></span>

![Edycja działu strona 1 po zmianie](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="4ed38-461">Przeglądarka pokazuje stronę Indeks ze zmienioną wartością i zaktualizowanym wskaźnikiem wierszVersion.</span><span class="sxs-lookup"><span data-stu-id="4ed38-461">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="4ed38-462">Zwróć uwagę na zaktualizowany wskaźnik wierszWyjaśny, jest wyświetlany na drugim odświeżeniu zwrotnym na drugiej karcie.</span><span class="sxs-lookup"><span data-stu-id="4ed38-462">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="4ed38-463">Zmień inne pole na drugiej karcie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="4ed38-463">Change a different field in the second browser tab.</span></span>

![Edycja działu strona 2 po zmianie](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="4ed38-465">Kliknij przycisk **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="4ed38-465">Click **Save**.</span></span> <span data-ttu-id="4ed38-466">Są widoczne komunikaty o błędach dla wszystkich pól, które nie są zgodne z wartościami bazy danych:</span><span class="sxs-lookup"><span data-stu-id="4ed38-466">You see error messages for all fields that don't match the DB values:</span></span>

![Komunikat o błędzie strony edycji działu](concurrency/_static/edit-error.png)

<span data-ttu-id="4ed38-468">W tym oknie przeglądarki nie ma zamiaru zmieniać pola Nazwa.</span><span class="sxs-lookup"><span data-stu-id="4ed38-468">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="4ed38-469">Skopiuj i wklej bieżącą wartość (Języki) do pola Nazwa.</span><span class="sxs-lookup"><span data-stu-id="4ed38-469">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="4ed38-470">Wyjmowanie karty. Sprawdzanie poprawności po stronie klienta usuwa komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="4ed38-470">Tab out. Client-side validation removes the error message.</span></span>

![Komunikat o błędzie strony edycji działu](concurrency/_static/cv.png)

<span data-ttu-id="4ed38-472">Kliknij **pozycję Zapisz** ponownie.</span><span class="sxs-lookup"><span data-stu-id="4ed38-472">Click **Save** again.</span></span> <span data-ttu-id="4ed38-473">Wartość wprowadzona na drugiej karcie przeglądarki zostanie zapisana.</span><span class="sxs-lookup"><span data-stu-id="4ed38-473">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="4ed38-474">Zapisane wartości są widoczne na stronie Indeks.</span><span class="sxs-lookup"><span data-stu-id="4ed38-474">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="4ed38-475">Aktualizowanie strony Usuwanie</span><span class="sxs-lookup"><span data-stu-id="4ed38-475">Update the Delete page</span></span>

<span data-ttu-id="4ed38-476">Zaktualizuj model strony Usuń o następujący kod:</span><span class="sxs-lookup"><span data-stu-id="4ed38-476">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="4ed38-477">Strona Usuń wykrywa konflikty współbieżności, gdy jednostka została zmieniona po pobraniu.</span><span class="sxs-lookup"><span data-stu-id="4ed38-477">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="4ed38-478">`Department.RowVersion`jest wersją wiersza, gdy jednostka została pobrana.</span><span class="sxs-lookup"><span data-stu-id="4ed38-478">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="4ed38-479">Gdy EF Core tworzy polecenie SQL DELETE, `RowVersion`zawiera klauzulę WHERE z plikiem .</span><span class="sxs-lookup"><span data-stu-id="4ed38-479">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="4ed38-480">Jeśli polecenie SQL DELETE powoduje zero wierszy, których to dotyczy:</span><span class="sxs-lookup"><span data-stu-id="4ed38-480">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="4ed38-481">Polecenie `RowVersion` SQL DELETE nie jest `RowVersion` zgodne w bazy danych.</span><span class="sxs-lookup"><span data-stu-id="4ed38-481">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the DB.</span></span>
* <span data-ttu-id="4ed38-482">A DbUpdateConcurrencyException wyjątek jest zgłaszany.</span><span class="sxs-lookup"><span data-stu-id="4ed38-482">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="4ed38-483">`OnGetAsync`nazywa się `concurrencyError`z .</span><span class="sxs-lookup"><span data-stu-id="4ed38-483">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="4ed38-484">Aktualizowanie strony Usuwanie</span><span class="sxs-lookup"><span data-stu-id="4ed38-484">Update the Delete page</span></span>

<span data-ttu-id="4ed38-485">Zaktualizuj *strony/działy/usuń.cshtml* za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="4ed38-485">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="4ed38-486">Poprzedni kod wprowadza następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="4ed38-486">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="4ed38-487">Aktualizuje `page` dyrektywę z `@page` do `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="4ed38-487">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="4ed38-488">Dodaje komunikat o błędzie.</span><span class="sxs-lookup"><span data-stu-id="4ed38-488">Adds an error message.</span></span>
* <span data-ttu-id="4ed38-489">Zastępuje program FirstMidName na fullname w polu **Administrator.**</span><span class="sxs-lookup"><span data-stu-id="4ed38-489">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="4ed38-490">Zmiany `RowVersion` w celu wyświetlenia ostatniego bajtu.</span><span class="sxs-lookup"><span data-stu-id="4ed38-490">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="4ed38-491">Dodaje ukrytą wersję wiersza.</span><span class="sxs-lookup"><span data-stu-id="4ed38-491">Adds a hidden row version.</span></span> <span data-ttu-id="4ed38-492">`RowVersion`należy dodać, więc zaksięguj z powrotem wiąże wartość.</span><span class="sxs-lookup"><span data-stu-id="4ed38-492">`RowVersion` must be added so post back binds the value.</span></span>

### <a name="test-concurrency-conflicts-with-the-delete-page"></a><span data-ttu-id="4ed38-493">Współbieżność testu powoduje konflikty ze stroną Usuwanie</span><span class="sxs-lookup"><span data-stu-id="4ed38-493">Test concurrency conflicts with the Delete page</span></span>

<span data-ttu-id="4ed38-494">Tworzenie działu testowego.</span><span class="sxs-lookup"><span data-stu-id="4ed38-494">Create a test department.</span></span>

<span data-ttu-id="4ed38-495">Otwórz dwie przeglądarki wystąpień Delete w dziale testowym:</span><span class="sxs-lookup"><span data-stu-id="4ed38-495">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="4ed38-496">Uruchom aplikację i wybierz pozycję Działy.</span><span class="sxs-lookup"><span data-stu-id="4ed38-496">Run the app and select Departments.</span></span>
* <span data-ttu-id="4ed38-497">Kliknij prawym przyciskiem myszy **usuń** hiperłącze dla działu testowego i wybierz pozycję Otwórz na **nowej karcie**.</span><span class="sxs-lookup"><span data-stu-id="4ed38-497">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="4ed38-498">Kliknij **hiperłącze Edytuj** dla działu testowego.</span><span class="sxs-lookup"><span data-stu-id="4ed38-498">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="4ed38-499">Dwie karty przeglądarki wyświetlają te same informacje.</span><span class="sxs-lookup"><span data-stu-id="4ed38-499">The two browser tabs display the same information.</span></span>

<span data-ttu-id="4ed38-500">Zmień budżet na pierwszej karcie przeglądarki i kliknij przycisk **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="4ed38-500">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="4ed38-501">Przeglądarka pokazuje stronę Indeks ze zmienioną wartością i zaktualizowanym wskaźnikiem wierszVersion.</span><span class="sxs-lookup"><span data-stu-id="4ed38-501">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="4ed38-502">Zwróć uwagę na zaktualizowany wskaźnik wierszWyjaśny, jest wyświetlany na drugim odświeżeniu zwrotnym na drugiej karcie.</span><span class="sxs-lookup"><span data-stu-id="4ed38-502">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="4ed38-503">Usuń dział testowy z drugiej karty. Błąd współbieżności jest wyświetlany z bieżącymi wartościami z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="4ed38-503">Delete the test department from the second tab. A concurrency error is display with the current values from the DB.</span></span> <span data-ttu-id="4ed38-504">Kliknięcie **przycisku Usuń** powoduje `RowVersion` usunięcie encji, chyba że została usunięta..</span><span class="sxs-lookup"><span data-stu-id="4ed38-504">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.department has been deleted.</span></span>

<span data-ttu-id="4ed38-505">Zobacz [Dziedziczenie,](xref:data/ef-mvc/inheritance) jak dziedziczyć model danych.</span><span class="sxs-lookup"><span data-stu-id="4ed38-505">See [Inheritance](xref:data/ef-mvc/inheritance) on how to inherit a data model.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="4ed38-506">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="4ed38-506">Additional resources</span></span>

* [<span data-ttu-id="4ed38-507">Tokeny współbieżności w EF Core</span><span class="sxs-lookup"><span data-stu-id="4ed38-507">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="4ed38-508">Obsługa współbieżności w EF Core</span><span class="sxs-lookup"><span data-stu-id="4ed38-508">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="4ed38-509">Wersja YouTube tego samouczka(Obsługa konfliktów współbieżności)</span><span class="sxs-lookup"><span data-stu-id="4ed38-509">YouTube version of this tutorial(Handling Concurrency Conflicts)</span></span>](https://youtu.be/EosxHTFgYps)
* [<span data-ttu-id="4ed38-510">Wersja YouTube tego samouczka (część 2)</span><span class="sxs-lookup"><span data-stu-id="4ed38-510">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [<span data-ttu-id="4ed38-511">Wersja YouTube tego samouczka(Część 3)</span><span class="sxs-lookup"><span data-stu-id="4ed38-511">YouTube version of this tutorial(Part 3)</span></span>](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [<span data-ttu-id="4ed38-512">Wstecz</span><span class="sxs-lookup"><span data-stu-id="4ed38-512">Previous</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

