---
title: Pomocnik tagu pamięci podręcznej w ASP.NET Core MVC
author: pkellner
description: Dowiedz się, jak używać pomocnika tagów pamięci podręcznej.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: ced10a7b7b221188fdac2a4e3c54f66292110ece
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773945"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a><span data-ttu-id="6c2b9-103">Pomocnik tagu pamięci podręcznej w ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="6c2b9-103">Cache Tag Helper in ASP.NET Core MVC</span></span>

<span data-ttu-id="6c2b9-104">Według [Peterowi Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="6c2b9-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="6c2b9-105">Pomocnik tagu pamięci podręcznej umożliwia zwiększenie wydajności aplikacji ASP.NET Core przez buforowanie jej zawartości dla dostawcy wewnętrznej ASP.NET Core pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-105">The Cache Tag Helper provides the ability to improve the performance of your ASP.NET Core app by caching its content to the internal ASP.NET Core cache provider.</span></span>

<span data-ttu-id="6c2b9-106">Aby zapoznać się z omówieniem pomocników tagów, <xref:mvc/views/tag-helpers/intro>Zobacz.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="6c2b9-107">Następujący znacznik Razor buforuje bieżącą datę:</span><span class="sxs-lookup"><span data-stu-id="6c2b9-107">The following Razor markup caches the current date:</span></span>

```cshtml
<cache>@DateTime.Now</cache>
```

<span data-ttu-id="6c2b9-108">Pierwsze żądanie do strony zawierającej pomocnika tagów wyświetla bieżącą datę.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-108">The first request to the page that contains the Tag Helper displays the current date.</span></span> <span data-ttu-id="6c2b9-109">Dodatkowe żądania pokazują wartość buforowaną, aż do momentu wygaśnięcia pamięci podręcznej (domyślnie 20 minut) lub do momentu wykluczenia z pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-109">Additional requests show the cached value until the cache expires (default 20 minutes) or until the cached date is evicted from the cache.</span></span>

## <a name="cache-tag-helper-attributes"></a><span data-ttu-id="6c2b9-110">Atrybuty pomocnika tagu pamięci podręcznej</span><span class="sxs-lookup"><span data-stu-id="6c2b9-110">Cache Tag Helper Attributes</span></span>

### <a name="enabled"></a><span data-ttu-id="6c2b9-111">enabled</span><span class="sxs-lookup"><span data-stu-id="6c2b9-111">enabled</span></span>

| <span data-ttu-id="6c2b9-112">Typ atrybutu</span><span class="sxs-lookup"><span data-stu-id="6c2b9-112">Attribute Type</span></span>  | <span data-ttu-id="6c2b9-113">Przykłady</span><span class="sxs-lookup"><span data-stu-id="6c2b9-113">Examples</span></span>        | <span data-ttu-id="6c2b9-114">Domyślne</span><span class="sxs-lookup"><span data-stu-id="6c2b9-114">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="6c2b9-115">Boolean</span><span class="sxs-lookup"><span data-stu-id="6c2b9-115">Boolean</span></span>         | <span data-ttu-id="6c2b9-116">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="6c2b9-116">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="6c2b9-117">`enabled`Określa, czy zawartość umieszczona w buforze znacznika pamięci podręcznej jest buforowana.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-117">`enabled` determines if the content enclosed by the Cache Tag Helper is cached.</span></span> <span data-ttu-id="6c2b9-118">Wartość domyślna to `true`.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-118">The default is `true`.</span></span> <span data-ttu-id="6c2b9-119">Jeśli jest ustawiona `false`na, renderowane dane wyjściowe **nie** są buforowane.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-119">If set to `false`, the rendered output is **not** cached.</span></span>

<span data-ttu-id="6c2b9-120">Przykład:</span><span class="sxs-lookup"><span data-stu-id="6c2b9-120">Example:</span></span>

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a><span data-ttu-id="6c2b9-121">Data wygaśnięcia</span><span class="sxs-lookup"><span data-stu-id="6c2b9-121">expires-on</span></span>

| <span data-ttu-id="6c2b9-122">Typ atrybutu</span><span class="sxs-lookup"><span data-stu-id="6c2b9-122">Attribute Type</span></span>   | <span data-ttu-id="6c2b9-123">Przykład</span><span class="sxs-lookup"><span data-stu-id="6c2b9-123">Example</span></span>                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

<span data-ttu-id="6c2b9-124">`expires-on`Ustawia bezwzględną datę wygaśnięcia dla elementu w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-124">`expires-on` sets an absolute expiration date for the cached item.</span></span>

<span data-ttu-id="6c2b9-125">Poniższy przykład pamięci podręcznej zawartości pomocnika tagów pamięci podręcznej do 5:02 PM dnia 29 stycznia 2025:</span><span class="sxs-lookup"><span data-stu-id="6c2b9-125">The following example caches the contents of the Cache Tag Helper until 5:02 PM on January 29, 2025:</span></span>

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a><span data-ttu-id="6c2b9-126">wygasa — po</span><span class="sxs-lookup"><span data-stu-id="6c2b9-126">expires-after</span></span>

| <span data-ttu-id="6c2b9-127">Typ atrybutu</span><span class="sxs-lookup"><span data-stu-id="6c2b9-127">Attribute Type</span></span> | <span data-ttu-id="6c2b9-128">Przykład</span><span class="sxs-lookup"><span data-stu-id="6c2b9-128">Example</span></span>                      | <span data-ttu-id="6c2b9-129">Domyślne</span><span class="sxs-lookup"><span data-stu-id="6c2b9-129">Default</span></span>    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | <span data-ttu-id="6c2b9-130">20 minut</span><span class="sxs-lookup"><span data-stu-id="6c2b9-130">20 minutes</span></span> |

<span data-ttu-id="6c2b9-131">`expires-after`Ustawia długość czasu od pierwszego żądania do buforowania zawartości.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-131">`expires-after` sets the length of time from the first request time to cache the contents.</span></span>

<span data-ttu-id="6c2b9-132">Przykład:</span><span class="sxs-lookup"><span data-stu-id="6c2b9-132">Example:</span></span>

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="6c2b9-133">Aparat widoku Razor ustawia wartość domyślną `expires-after` na 20 minut.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-133">The Razor View Engine sets the default `expires-after` value to twenty minutes.</span></span>

### <a name="expires-sliding"></a><span data-ttu-id="6c2b9-134">wygasa — przesuwanie</span><span class="sxs-lookup"><span data-stu-id="6c2b9-134">expires-sliding</span></span>

| <span data-ttu-id="6c2b9-135">Typ atrybutu</span><span class="sxs-lookup"><span data-stu-id="6c2b9-135">Attribute Type</span></span> | <span data-ttu-id="6c2b9-136">Przykład</span><span class="sxs-lookup"><span data-stu-id="6c2b9-136">Example</span></span>                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

<span data-ttu-id="6c2b9-137">Określa czas, w którym wpis pamięci podręcznej powinien zostać wykluczony, jeśli nie uzyskano dostępu do jego wartości.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-137">Sets the time that a cache entry should be evicted if its value hasn't been accessed.</span></span>

<span data-ttu-id="6c2b9-138">Przykład:</span><span class="sxs-lookup"><span data-stu-id="6c2b9-138">Example:</span></span>

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a><span data-ttu-id="6c2b9-139">Zróżnicuj według nagłówka</span><span class="sxs-lookup"><span data-stu-id="6c2b9-139">vary-by-header</span></span>

| <span data-ttu-id="6c2b9-140">Typ atrybutu</span><span class="sxs-lookup"><span data-stu-id="6c2b9-140">Attribute Type</span></span> | <span data-ttu-id="6c2b9-141">Przykłady</span><span class="sxs-lookup"><span data-stu-id="6c2b9-141">Examples</span></span>                                    |
| -------------- | ------------------------------------------- |
| <span data-ttu-id="6c2b9-142">String</span><span class="sxs-lookup"><span data-stu-id="6c2b9-142">String</span></span>         | <span data-ttu-id="6c2b9-143">`User-Agent`, `User-Agent,content-encoding`</span><span class="sxs-lookup"><span data-stu-id="6c2b9-143">`User-Agent`, `User-Agent,content-encoding`</span></span> |

<span data-ttu-id="6c2b9-144">`vary-by-header`akceptuje rozdzielaną przecinkami listę wartości nagłówka, które wyzwalają Odświeżanie pamięci podręcznej, gdy zmieniają się.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-144">`vary-by-header` accepts a comma-delimited list of header values that trigger a cache refresh when they change.</span></span>

<span data-ttu-id="6c2b9-145">Poniższy przykład monitoruje wartość `User-Agent`nagłówka.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-145">The following example monitors the header value `User-Agent`.</span></span> <span data-ttu-id="6c2b9-146">Przykład buforuje zawartość dla każdej innej `User-Agent` prezentowanej na serwerze sieci Web:</span><span class="sxs-lookup"><span data-stu-id="6c2b9-146">The example caches the content for every different `User-Agent` presented to the web server:</span></span>

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a><span data-ttu-id="6c2b9-147">Zróżnicuj według zapytania</span><span class="sxs-lookup"><span data-stu-id="6c2b9-147">vary-by-query</span></span>

| <span data-ttu-id="6c2b9-148">Typ atrybutu</span><span class="sxs-lookup"><span data-stu-id="6c2b9-148">Attribute Type</span></span> | <span data-ttu-id="6c2b9-149">Przykłady</span><span class="sxs-lookup"><span data-stu-id="6c2b9-149">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="6c2b9-150">String</span><span class="sxs-lookup"><span data-stu-id="6c2b9-150">String</span></span>         | <span data-ttu-id="6c2b9-151">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="6c2b9-151">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="6c2b9-152">`vary-by-query`akceptuje listę rozdzielonych przecinkami <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> w ciągu zapytania (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>), które wyzwalają Odświeżanie pamięci podręcznej, gdy zostanie zmieniona wartość dowolnego z wymienionych kluczy.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-152">`vary-by-query` accepts a comma-delimited list of <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> in a query string (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>) that trigger a cache refresh when the value of any listed key changes.</span></span>

<span data-ttu-id="6c2b9-153">Poniższy przykład monitoruje wartości `Make` i. `Model`</span><span class="sxs-lookup"><span data-stu-id="6c2b9-153">The following example monitors the values of `Make` and `Model`.</span></span> <span data-ttu-id="6c2b9-154">Przykład buforuje zawartość dla każdej innej `Make` i `Model` prezentowanej na serwerze sieci Web:</span><span class="sxs-lookup"><span data-stu-id="6c2b9-154">The example caches the content for every different `Make` and `Model` presented to the web server:</span></span>

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a><span data-ttu-id="6c2b9-155">Zróżnicuj według trasy</span><span class="sxs-lookup"><span data-stu-id="6c2b9-155">vary-by-route</span></span>

| <span data-ttu-id="6c2b9-156">Typ atrybutu</span><span class="sxs-lookup"><span data-stu-id="6c2b9-156">Attribute Type</span></span> | <span data-ttu-id="6c2b9-157">Przykłady</span><span class="sxs-lookup"><span data-stu-id="6c2b9-157">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="6c2b9-158">String</span><span class="sxs-lookup"><span data-stu-id="6c2b9-158">String</span></span>         | <span data-ttu-id="6c2b9-159">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="6c2b9-159">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="6c2b9-160">`vary-by-route`akceptuje rozdzielaną przecinkami listę nazw parametrów trasy, które wyzwalają Odświeżanie pamięci podręcznej, gdy wartość parametru dane trasy zostanie zmieniona.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-160">`vary-by-route` accepts a comma-delimited list of route parameter names that trigger a cache refresh when the route data parameter value changes.</span></span>

<span data-ttu-id="6c2b9-161">Przykład:</span><span class="sxs-lookup"><span data-stu-id="6c2b9-161">Example:</span></span>

<span data-ttu-id="6c2b9-162">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6c2b9-162">*Startup.cs*:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

<span data-ttu-id="6c2b9-163">*Index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6c2b9-163">*Index.cshtml*:</span></span>

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-cookie"></a><span data-ttu-id="6c2b9-164">Różne pliki cookie</span><span class="sxs-lookup"><span data-stu-id="6c2b9-164">vary-by-cookie</span></span>

| <span data-ttu-id="6c2b9-165">Typ atrybutu</span><span class="sxs-lookup"><span data-stu-id="6c2b9-165">Attribute Type</span></span> | <span data-ttu-id="6c2b9-166">Przykłady</span><span class="sxs-lookup"><span data-stu-id="6c2b9-166">Examples</span></span>                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| <span data-ttu-id="6c2b9-167">String</span><span class="sxs-lookup"><span data-stu-id="6c2b9-167">String</span></span>         | <span data-ttu-id="6c2b9-168">`.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor`</span><span class="sxs-lookup"><span data-stu-id="6c2b9-168">`.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor`</span></span> |

<span data-ttu-id="6c2b9-169">`vary-by-cookie`akceptuje rozdzielaną przecinkami listę nazw cookie, które wyzwalają Odświeżanie pamięci podręcznej, gdy zmieniają się wartości plików cookie.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-169">`vary-by-cookie` accepts a comma-delimited list of cookie names that trigger a cache refresh when the cookie values change.</span></span>

<span data-ttu-id="6c2b9-170">Poniższy przykład monitoruje plik cookie skojarzony z tożsamością ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-170">The following example monitors the cookie associated with ASP.NET Core Identity.</span></span> <span data-ttu-id="6c2b9-171">Po uwierzytelnieniu użytkownika zmiana w pliku cookie tożsamości wyzwala odświeżenie pamięci podręcznej:</span><span class="sxs-lookup"><span data-stu-id="6c2b9-171">When a user is authenticated, a change in the Identity cookie triggers a cache refresh:</span></span>

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a><span data-ttu-id="6c2b9-172">Zróżnicuj według użytkownika</span><span class="sxs-lookup"><span data-stu-id="6c2b9-172">vary-by-user</span></span>

| <span data-ttu-id="6c2b9-173">Typ atrybutu</span><span class="sxs-lookup"><span data-stu-id="6c2b9-173">Attribute Type</span></span>  | <span data-ttu-id="6c2b9-174">Przykłady</span><span class="sxs-lookup"><span data-stu-id="6c2b9-174">Examples</span></span>        | <span data-ttu-id="6c2b9-175">Domyślne</span><span class="sxs-lookup"><span data-stu-id="6c2b9-175">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="6c2b9-176">Boolean</span><span class="sxs-lookup"><span data-stu-id="6c2b9-176">Boolean</span></span>         | <span data-ttu-id="6c2b9-177">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="6c2b9-177">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="6c2b9-178">`vary-by-user`Określa, czy pamięć podręczna jest resetowana, gdy zostanie zmieniony zalogowany użytkownik (lub podmiot zabezpieczeń kontekstu).</span><span class="sxs-lookup"><span data-stu-id="6c2b9-178">`vary-by-user` specifies whether or not the cache resets when the signed-in user (or Context Principal) changes.</span></span> <span data-ttu-id="6c2b9-179">Bieżący użytkownik jest również znany jako podmiot zabezpieczeń kontekstu żądania i może być wyświetlany w widoku Razor przez odwołanie `@User.Identity.Name`.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-179">The current user is also known as the Request Context Principal and can be viewed in a Razor view by referencing `@User.Identity.Name`.</span></span>

<span data-ttu-id="6c2b9-180">Poniższy przykład monitoruje bieżącego zalogowanego użytkownika, aby wyzwolić Odświeżanie pamięci podręcznej:</span><span class="sxs-lookup"><span data-stu-id="6c2b9-180">The following example monitors the current logged in user to trigger a cache refresh:</span></span>

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="6c2b9-181">Użycie tego atrybutu zachowuje zawartość w pamięci podręcznej przez proces logowania i wylogowywania.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-181">Using this attribute maintains the contents in cache through a sign-in and sign-out cycle.</span></span> <span data-ttu-id="6c2b9-182">Gdy wartość jest ustawiona na `true`, cykl uwierzytelniania unieważnia pamięć podręczną dla uwierzytelnionego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-182">When the value is set to `true`, an authentication cycle invalidates the cache for the authenticated user.</span></span> <span data-ttu-id="6c2b9-183">Pamięć podręczna jest unieważniona, ponieważ podczas uwierzytelniania użytkownika jest generowana Nowa unikatowa wartość cookie.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-183">The cache is invalidated because a new unique cookie value is generated when a user is authenticated.</span></span> <span data-ttu-id="6c2b9-184">Pamięć podręczna jest utrzymywana dla stanu anonimowego, gdy plik cookie nie istnieje lub plik cookie utracił ważność.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-184">Cache is maintained for the anonymous state when no cookie is present or the cookie has expired.</span></span> <span data-ttu-id="6c2b9-185">Jeśli użytkownik **nie** jest uwierzytelniony, pamięć podręczna jest utrzymywana.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-185">If the user is **not** authenticated, the cache is maintained.</span></span>

### <a name="vary-by"></a><span data-ttu-id="6c2b9-186">Zróżnicuj według</span><span class="sxs-lookup"><span data-stu-id="6c2b9-186">vary-by</span></span>

| <span data-ttu-id="6c2b9-187">Typ atrybutu</span><span class="sxs-lookup"><span data-stu-id="6c2b9-187">Attribute Type</span></span> | <span data-ttu-id="6c2b9-188">Przykład</span><span class="sxs-lookup"><span data-stu-id="6c2b9-188">Example</span></span>  |
| -------------- | -------- |
| <span data-ttu-id="6c2b9-189">String</span><span class="sxs-lookup"><span data-stu-id="6c2b9-189">String</span></span>         | `@Model` |

<span data-ttu-id="6c2b9-190">`vary-by`umożliwia dostosowanie danych przechowywanych w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-190">`vary-by` allows for customization of what data is cached.</span></span> <span data-ttu-id="6c2b9-191">Gdy obiekt, do którego odwołuje się wartość ciągu atrybutu, zmienia się zawartość pomocnika tagu pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-191">When the object referenced by the attribute's string value changes, the content of the Cache Tag Helper is updated.</span></span> <span data-ttu-id="6c2b9-192">Często łączenie ciągów wartości modelu jest przypisywane do tego atrybutu.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-192">Often, a string-concatenation of model values are assigned to this attribute.</span></span> <span data-ttu-id="6c2b9-193">Efektywnie jest to scenariusz, w którym aktualizacja dowolnej z połączonych wartości unieważnia pamięć podręczną.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-193">Effectively, this results in a scenario where an update to any of the concatenated values invalidates the cache.</span></span>

<span data-ttu-id="6c2b9-194">W poniższym przykładzie przyjęto założenie, że metoda kontrolera renderuje widok sumuje wartość całkowitą dwóch parametrów `myParam1` trasy `myParam2`, i zwraca sumę jako właściwość pojedynczego modelu.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-194">The following example assumes the controller method rendering the view sums the integer value of the two route parameters, `myParam1` and `myParam2`, and returns the sum as the single model property.</span></span> <span data-ttu-id="6c2b9-195">Po zmianie tej sumy zawartość pomocnika tagu pamięci podręcznej jest renderowana i buforowana ponownie.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-195">When this sum changes, the content of the Cache Tag Helper is rendered and cached again.</span></span>  

<span data-ttu-id="6c2b9-196">Działanie:</span><span class="sxs-lookup"><span data-stu-id="6c2b9-196">Action:</span></span>

```csharp
public IActionResult Index(string myParam1, string myParam2, string myParam3)
{
    int num1;
    int num2;
    int.TryParse(myParam1, out num1);
    int.TryParse(myParam2, out num2);
    return View(viewName, num1 + num2);
}
```

<span data-ttu-id="6c2b9-197">*Index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6c2b9-197">*Index.cshtml*:</span></span>

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a><span data-ttu-id="6c2b9-198">priority</span><span class="sxs-lookup"><span data-stu-id="6c2b9-198">priority</span></span>

| <span data-ttu-id="6c2b9-199">Typ atrybutu</span><span class="sxs-lookup"><span data-stu-id="6c2b9-199">Attribute Type</span></span>      | <span data-ttu-id="6c2b9-200">Przykłady</span><span class="sxs-lookup"><span data-stu-id="6c2b9-200">Examples</span></span>                               | <span data-ttu-id="6c2b9-201">Domyślne</span><span class="sxs-lookup"><span data-stu-id="6c2b9-201">Default</span></span>  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | <span data-ttu-id="6c2b9-202">`High`, `Low`, `NeverRemove`, `Normal`</span><span class="sxs-lookup"><span data-stu-id="6c2b9-202">`High`, `Low`, `NeverRemove`, `Normal`</span></span> | `Normal` |

<span data-ttu-id="6c2b9-203">`priority`zapewnia wskazówki dotyczące wykluczenia pamięci podręcznej dla wbudowanego dostawcy pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-203">`priority` provides cache eviction guidance to the built-in cache provider.</span></span> <span data-ttu-id="6c2b9-204">Serwer sieci Web najpierw wyklucza `Low` wpisy pamięci podręcznej, gdy jest on w trakcie jego naciskania pamięci.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-204">The web server evicts `Low` cache entries first when it's under memory pressure.</span></span>

<span data-ttu-id="6c2b9-205">Przykład:</span><span class="sxs-lookup"><span data-stu-id="6c2b9-205">Example:</span></span>

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="6c2b9-206">Ten `priority` atrybut nie gwarantuje określonego poziomu przechowywania pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-206">The `priority` attribute doesn't guarantee a specific level of cache retention.</span></span> <span data-ttu-id="6c2b9-207">`CacheItemPriority`jest tylko sugestią.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-207">`CacheItemPriority` is only a suggestion.</span></span> <span data-ttu-id="6c2b9-208">Ustawienie tego atrybutu na `NeverRemove` wartość nie gwarantuje, że buforowane elementy są zawsze zachowywane.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-208">Setting this attribute to `NeverRemove` doesn't guarantee that cached items are always retained.</span></span> <span data-ttu-id="6c2b9-209">Aby uzyskać więcej informacji, zobacz tematy w sekcji [dodatkowe zasoby](#additional-resources) .</span><span class="sxs-lookup"><span data-stu-id="6c2b9-209">See the topics in the [Additional Resources](#additional-resources) section for more information.</span></span>

<span data-ttu-id="6c2b9-210">Pomocnik tagu pamięci podręcznej jest zależny od [usługi pamięci podręcznej](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="6c2b9-210">The Cache Tag Helper is dependent on the [memory cache service](xref:performance/caching/memory).</span></span> <span data-ttu-id="6c2b9-211">Pomocnik tagu pamięci podręcznej dodaje usługę, jeśli nie została dodana.</span><span class="sxs-lookup"><span data-stu-id="6c2b9-211">The Cache Tag Helper adds the service if it hasn't been added.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6c2b9-212">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6c2b9-212">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
