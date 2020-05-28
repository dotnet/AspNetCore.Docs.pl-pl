---
<span data-ttu-id="1c2fa-101">title: "ASP.NET Core Blazor obsługi zdarzeń" Author: Description: "informacje o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki".</span><span class="sxs-lookup"><span data-stu-id="1c2fa-101">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1c2fa-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c2fa-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-103">'Blazor'</span></span>
- <span data-ttu-id="1c2fa-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-104">'Identity'</span></span>
- <span data-ttu-id="1c2fa-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c2fa-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-106">'Razor'</span></span>
- <span data-ttu-id="1c2fa-107">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="1c2fa-108">BlazorObsługa zdarzeń ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1c2fa-108">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="1c2fa-109">Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="1c2fa-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="1c2fa-110">składniki zapewniają funkcje obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-110"> components provide event handling features.</span></span> <span data-ttu-id="1c2fa-111">Dla atrybutu elementu HTML o nazwie [`@on{EVENT}`](xref:mvc/views/razor#onevent) (na przykład `@onclick` ) z wartością typu delegata Razor składnik traktuje wartość atrybutu jako procedurę obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-111">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="1c2fa-112">Poniższy kod wywołuje metodę, `UpdateHeading` gdy przycisk zostanie wybrany w interfejsie użytkownika:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-112">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="1c2fa-113">Poniższy kod wywołuje metodę, `CheckChanged` gdy pole wyboru zostanie zmienione w interfejsie użytkownika:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-113">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="1c2fa-114">Procedury obsługi zdarzeń mogą również być asynchroniczne i zwracać <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="1c2fa-114">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="1c2fa-115">Nie ma potrzeby ręcznego wywoływania [StateHasChanged](xref:blazor/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="1c2fa-115">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="1c2fa-116">Wyjątki są rejestrowane, gdy wystąpią.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-116">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="1c2fa-117">W poniższym przykładzie `UpdateHeading` jest wywoływana asynchronicznie po wybraniu przycisku:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-117">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

## <a name="event-argument-types"></a><span data-ttu-id="1c2fa-118">Typy argumentów zdarzeń</span><span class="sxs-lookup"><span data-stu-id="1c2fa-118">Event argument types</span></span>

<span data-ttu-id="1c2fa-119">W przypadku niektórych zdarzeń dozwolone są typy argumentów zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-119">For some events, event argument types are permitted.</span></span> <span data-ttu-id="1c2fa-120">Określanie typu zdarzenia w wywołaniu metody jest konieczne tylko wtedy, gdy typ zdarzenia jest używany w metodzie.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-120">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="1c2fa-121">Obsługiwane <xref:System.EventArgs> są przedstawione w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-121">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="1c2fa-122">Zdarzenie</span><span class="sxs-lookup"><span data-stu-id="1c2fa-122">Event</span></span>            | <span data-ttu-id="1c2fa-123">Klasa</span><span class="sxs-lookup"><span data-stu-id="1c2fa-123">Class</span></span>                | <span data-ttu-id="1c2fa-124">Zdarzenia i uwagi dotyczące modelu DOM</span><span class="sxs-lookup"><span data-stu-id="1c2fa-124">DOM events and notes</span></span> |
| ---
<span data-ttu-id="1c2fa-125">title: "ASP.NET Core Blazor obsługi zdarzeń" Author: Description: "informacje o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki".</span><span class="sxs-lookup"><span data-stu-id="1c2fa-125">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1c2fa-126">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-126">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c2fa-127">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-127">'Blazor'</span></span>
- <span data-ttu-id="1c2fa-128">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-128">'Identity'</span></span>
- <span data-ttu-id="1c2fa-129">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-129">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c2fa-130">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-130">'Razor'</span></span>
- <span data-ttu-id="1c2fa-131">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-131">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c2fa-132">title: "ASP.NET Core Blazor obsługi zdarzeń" Author: Description: "informacje o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki".</span><span class="sxs-lookup"><span data-stu-id="1c2fa-132">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1c2fa-133">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-133">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c2fa-134">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-134">'Blazor'</span></span>
- <span data-ttu-id="1c2fa-135">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-135">'Identity'</span></span>
- <span data-ttu-id="1c2fa-136">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-136">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c2fa-137">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-137">'Razor'</span></span>
- <span data-ttu-id="1c2fa-138">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-138">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c2fa-139">title: "ASP.NET Core Blazor obsługi zdarzeń" Author: Description: "informacje o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki".</span><span class="sxs-lookup"><span data-stu-id="1c2fa-139">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1c2fa-140">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-140">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c2fa-141">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-141">'Blazor'</span></span>
- <span data-ttu-id="1c2fa-142">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-142">'Identity'</span></span>
- <span data-ttu-id="1c2fa-143">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-143">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c2fa-144">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-144">'Razor'</span></span>
- <span data-ttu-id="1c2fa-145">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-145">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c2fa-146">title: "ASP.NET Core Blazor obsługi zdarzeń" Author: Description: "informacje o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki".</span><span class="sxs-lookup"><span data-stu-id="1c2fa-146">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1c2fa-147">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c2fa-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-148">'Blazor'</span></span>
- <span data-ttu-id="1c2fa-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-149">'Identity'</span></span>
- <span data-ttu-id="1c2fa-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c2fa-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-151">'Razor'</span></span>
- <span data-ttu-id="1c2fa-152">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c2fa-153">title: "ASP.NET Core Blazor obsługi zdarzeń" Author: Description: "informacje o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki".</span><span class="sxs-lookup"><span data-stu-id="1c2fa-153">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1c2fa-154">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c2fa-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-155">'Blazor'</span></span>
- <span data-ttu-id="1c2fa-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-156">'Identity'</span></span>
- <span data-ttu-id="1c2fa-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c2fa-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-158">'Razor'</span></span>
- <span data-ttu-id="1c2fa-159">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c2fa-160">title: "ASP.NET Core Blazor obsługi zdarzeń" Author: Description: "informacje o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki".</span><span class="sxs-lookup"><span data-stu-id="1c2fa-160">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1c2fa-161">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c2fa-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-162">'Blazor'</span></span>
- <span data-ttu-id="1c2fa-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-163">'Identity'</span></span>
- <span data-ttu-id="1c2fa-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c2fa-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-165">'Razor'</span></span>
- <span data-ttu-id="1c2fa-166">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-166">'SignalR' uid:</span></span> 

<span data-ttu-id="1c2fa-167">-------- | ---title: "ASP.NET Core Blazor obsługi zdarzeń" Author: Description: "informacje o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki".</span><span class="sxs-lookup"><span data-stu-id="1c2fa-167">-------- | --- title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1c2fa-168">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c2fa-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-169">'Blazor'</span></span>
- <span data-ttu-id="1c2fa-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-170">'Identity'</span></span>
- <span data-ttu-id="1c2fa-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c2fa-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-172">'Razor'</span></span>
- <span data-ttu-id="1c2fa-173">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c2fa-174">title: "ASP.NET Core Blazor obsługi zdarzeń" Author: Description: "informacje o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki".</span><span class="sxs-lookup"><span data-stu-id="1c2fa-174">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1c2fa-175">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c2fa-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-176">'Blazor'</span></span>
- <span data-ttu-id="1c2fa-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-177">'Identity'</span></span>
- <span data-ttu-id="1c2fa-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c2fa-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-179">'Razor'</span></span>
- <span data-ttu-id="1c2fa-180">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c2fa-181">title: "ASP.NET Core Blazor obsługi zdarzeń" Author: Description: "informacje o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki".</span><span class="sxs-lookup"><span data-stu-id="1c2fa-181">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1c2fa-182">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c2fa-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-183">'Blazor'</span></span>
- <span data-ttu-id="1c2fa-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-184">'Identity'</span></span>
- <span data-ttu-id="1c2fa-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c2fa-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-186">'Razor'</span></span>
- <span data-ttu-id="1c2fa-187">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-187">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c2fa-188">title: "ASP.NET Core Blazor obsługi zdarzeń" Author: Description: "informacje o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki".</span><span class="sxs-lookup"><span data-stu-id="1c2fa-188">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1c2fa-189">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-189">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c2fa-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-190">'Blazor'</span></span>
- <span data-ttu-id="1c2fa-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-191">'Identity'</span></span>
- <span data-ttu-id="1c2fa-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c2fa-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-193">'Razor'</span></span>
- <span data-ttu-id="1c2fa-194">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c2fa-195">title: "ASP.NET Core Blazor obsługi zdarzeń" Author: Description: "informacje o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki".</span><span class="sxs-lookup"><span data-stu-id="1c2fa-195">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1c2fa-196">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-196">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c2fa-197">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-197">'Blazor'</span></span>
- <span data-ttu-id="1c2fa-198">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-198">'Identity'</span></span>
- <span data-ttu-id="1c2fa-199">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-199">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c2fa-200">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-200">'Razor'</span></span>
- <span data-ttu-id="1c2fa-201">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-201">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c2fa-202">title: "ASP.NET Core Blazor obsługi zdarzeń" Author: Description: "informacje o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki".</span><span class="sxs-lookup"><span data-stu-id="1c2fa-202">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1c2fa-203">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-203">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c2fa-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-204">'Blazor'</span></span>
- <span data-ttu-id="1c2fa-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-205">'Identity'</span></span>
- <span data-ttu-id="1c2fa-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c2fa-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-207">'Razor'</span></span>
- <span data-ttu-id="1c2fa-208">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c2fa-209">title: "ASP.NET Core Blazor obsługi zdarzeń" Author: Description: "informacje o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki".</span><span class="sxs-lookup"><span data-stu-id="1c2fa-209">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1c2fa-210">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-210">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c2fa-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-211">'Blazor'</span></span>
- <span data-ttu-id="1c2fa-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-212">'Identity'</span></span>
- <span data-ttu-id="1c2fa-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c2fa-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-214">'Razor'</span></span>
- <span data-ttu-id="1c2fa-215">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-215">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c2fa-216">title: "ASP.NET Core Blazor obsługi zdarzeń" Author: Description: "informacje o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki".</span><span class="sxs-lookup"><span data-stu-id="1c2fa-216">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1c2fa-217">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-217">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c2fa-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-218">'Blazor'</span></span>
- <span data-ttu-id="1c2fa-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-219">'Identity'</span></span>
- <span data-ttu-id="1c2fa-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c2fa-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-221">'Razor'</span></span>
- <span data-ttu-id="1c2fa-222">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-222">'SignalR' uid:</span></span> 

<span data-ttu-id="1c2fa-223">---------- | ---title: "ASP.NET Core Blazor obsługi zdarzeń" Author: Description: "informacje o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki".</span><span class="sxs-lookup"><span data-stu-id="1c2fa-223">---------- | --- title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1c2fa-224">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-224">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c2fa-225">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-225">'Blazor'</span></span>
- <span data-ttu-id="1c2fa-226">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-226">'Identity'</span></span>
- <span data-ttu-id="1c2fa-227">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-227">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c2fa-228">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-228">'Razor'</span></span>
- <span data-ttu-id="1c2fa-229">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-229">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c2fa-230">title: "ASP.NET Core Blazor obsługi zdarzeń" Author: Description: "informacje o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki".</span><span class="sxs-lookup"><span data-stu-id="1c2fa-230">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1c2fa-231">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-231">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c2fa-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-232">'Blazor'</span></span>
- <span data-ttu-id="1c2fa-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-233">'Identity'</span></span>
- <span data-ttu-id="1c2fa-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c2fa-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-235">'Razor'</span></span>
- <span data-ttu-id="1c2fa-236">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c2fa-237">title: "ASP.NET Core Blazor obsługi zdarzeń" Author: Description: "informacje o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki".</span><span class="sxs-lookup"><span data-stu-id="1c2fa-237">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1c2fa-238">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-238">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c2fa-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-239">'Blazor'</span></span>
- <span data-ttu-id="1c2fa-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-240">'Identity'</span></span>
- <span data-ttu-id="1c2fa-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c2fa-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-242">'Razor'</span></span>
- <span data-ttu-id="1c2fa-243">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c2fa-244">title: "ASP.NET Core Blazor obsługi zdarzeń" Author: Description: "informacje o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki".</span><span class="sxs-lookup"><span data-stu-id="1c2fa-244">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1c2fa-245">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-245">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c2fa-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-246">'Blazor'</span></span>
- <span data-ttu-id="1c2fa-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-247">'Identity'</span></span>
- <span data-ttu-id="1c2fa-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c2fa-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-249">'Razor'</span></span>
- <span data-ttu-id="1c2fa-250">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c2fa-251">title: "ASP.NET Core Blazor obsługi zdarzeń" Author: Description: "informacje o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki".</span><span class="sxs-lookup"><span data-stu-id="1c2fa-251">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1c2fa-252">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-252">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c2fa-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-253">'Blazor'</span></span>
- <span data-ttu-id="1c2fa-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-254">'Identity'</span></span>
- <span data-ttu-id="1c2fa-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c2fa-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-256">'Razor'</span></span>
- <span data-ttu-id="1c2fa-257">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c2fa-258">title: "ASP.NET Core Blazor obsługi zdarzeń" Author: Description: "informacje o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki".</span><span class="sxs-lookup"><span data-stu-id="1c2fa-258">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1c2fa-259">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-259">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c2fa-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-260">'Blazor'</span></span>
- <span data-ttu-id="1c2fa-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-261">'Identity'</span></span>
- <span data-ttu-id="1c2fa-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c2fa-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-263">'Razor'</span></span>
- <span data-ttu-id="1c2fa-264">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-264">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c2fa-265">title: "ASP.NET Core Blazor obsługi zdarzeń" Author: Description: "informacje o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki".</span><span class="sxs-lookup"><span data-stu-id="1c2fa-265">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1c2fa-266">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-266">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c2fa-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-267">'Blazor'</span></span>
- <span data-ttu-id="1c2fa-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-268">'Identity'</span></span>
- <span data-ttu-id="1c2fa-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c2fa-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-270">'Razor'</span></span>
- <span data-ttu-id="1c2fa-271">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1c2fa-272">title: "ASP.NET Core Blazor obsługi zdarzeń" Author: Description: "informacje o Blazor funkcjach obsługi zdarzeń, takich jak typy argumentów zdarzeń, wywołania zwrotne zdarzeń i zarządzanie domyślnymi zdarzeniami przeglądarki".</span><span class="sxs-lookup"><span data-stu-id="1c2fa-272">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="1c2fa-273">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-273">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1c2fa-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-274">'Blazor'</span></span>
- <span data-ttu-id="1c2fa-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-275">'Identity'</span></span>
- <span data-ttu-id="1c2fa-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="1c2fa-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1c2fa-277">'Razor'</span></span>
- <span data-ttu-id="1c2fa-278">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-278">'SignalR' uid:</span></span> 

<span data-ttu-id="1c2fa-279">---------- | | Schowek | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` | | Przeciągnij | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`,`ondragend`</span><span class="sxs-lookup"><span data-stu-id="1c2fa-279">---------- | | Clipboard        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` | | Drag             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="1c2fa-280"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer>i <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> przytrzymaj przeciągane dane elementu.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-280"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> <span data-ttu-id="1c2fa-281">| | Błąd | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` | | Zdarzenie | <xref:System.EventArgs>  |  *Ogólne*</span><span class="sxs-lookup"><span data-stu-id="1c2fa-281">| | Error            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` | | Event            | <xref:System.EventArgs> | *General*</span></span><br><span data-ttu-id="1c2fa-282">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="1c2fa-282">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="1c2fa-283">*Schowek*</span><span class="sxs-lookup"><span data-stu-id="1c2fa-283">*Clipboard*</span></span><br><span data-ttu-id="1c2fa-284">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="1c2fa-284">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="1c2fa-285">*Dane wejściowe*</span><span class="sxs-lookup"><span data-stu-id="1c2fa-285">*Input*</span></span><br><span data-ttu-id="1c2fa-286">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="1c2fa-286">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="1c2fa-287">*Media*</span><span class="sxs-lookup"><span data-stu-id="1c2fa-287">*Media*</span></span><br><span data-ttu-id="1c2fa-288">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="1c2fa-288">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="1c2fa-289"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers>przechowuje atrybuty, aby skonfigurować mapowania między nazwami zdarzeń i typami argumentów zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-289"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> <span data-ttu-id="1c2fa-290">| | Fokus | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`,`onfocusout`</span><span class="sxs-lookup"><span data-stu-id="1c2fa-290">| | Focus            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="1c2fa-291">Nie obejmuje obsługi dla `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="1c2fa-291">Doesn't include support for `relatedTarget`.</span></span> <span data-ttu-id="1c2fa-292">| | Dane wejściowe | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` | | Klawiatura | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` | | Mysz | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` | | Wskaźnik myszy | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` | | Kółko myszy | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` | | Postęp | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` | | Dotknięcie | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`,`ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="1c2fa-292">| | Input            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` | | Keyboard         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` | | Mouse            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` | | Mouse pointer    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` | | Mouse wheel      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` | | Progress         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` | | Touch            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="1c2fa-293"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint>reprezentuje pojedynczy punkt kontaktu na urządzeniu z wrażliwym dotknięciem.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-293"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="1c2fa-294">Więcej informacji zawierają następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-294">For more information, see the following resources:</span></span>

* <span data-ttu-id="1c2fa-295">[Klasy EventArgs w źródle odwołania ASP.NET Core (gałąź dotnet/aspnetcore Release/3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="1c2fa-295">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="1c2fa-296">[Powiadomienia MDN Web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): zawiera informacje o tym, które elementy HTML obsługują każde wydarzenie dom.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-296">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="1c2fa-297">Wyrażenia lambda</span><span class="sxs-lookup"><span data-stu-id="1c2fa-297">Lambda expressions</span></span>

<span data-ttu-id="1c2fa-298">[Wyrażenia lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) mogą być również używane:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-298">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="1c2fa-299">Często wygodnie jest blisko dodatkowych wartości, na przykład podczas iteracji na zestawie elementów.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-299">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="1c2fa-300">Poniższy przykład tworzy trzy przyciski, z których każdy wywołuje `UpdateHeading` przekazanie argumentu zdarzenia ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) i jego numer przycisku ( `buttonNumber` ), po wybraniu w interfejsie użytkownika:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-300">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="1c2fa-301">**Nie** używaj zmiennej Loop ( `i` ) w `for` pętli bezpośrednio w wyrażeniu lambda.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-301">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="1c2fa-302">W przeciwnym razie ta sama zmienna jest używana przez wszystkie wyrażenia lambda, co sprawia `i` , że wartość jest taka sama we wszystkich lambdach.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-302">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="1c2fa-303">Zawsze Przechwytuj wartość w zmiennej lokalnej ( `buttonNumber` w poprzednim przykładzie), a następnie użyj jej.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-303">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="1c2fa-304">EventCallback</span><span class="sxs-lookup"><span data-stu-id="1c2fa-304">EventCallback</span></span>

<span data-ttu-id="1c2fa-305">Typowym scenariuszem ze składnikami zagnieżdżonymi jest uruchomienie metody składnika nadrzędnego, gdy wystąpi zdarzenie składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-305">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="1c2fa-306">`onclick`Zdarzenie występujące w składniku podrzędnym jest wspólnym przypadkiem użycia.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-306">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="1c2fa-307">Aby uwidocznić zdarzenia między składnikami, użyj <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="1c2fa-307">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="1c2fa-308">Składnik nadrzędny może przypisać metodę wywołania zwrotnego do składnika podrzędnego <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="1c2fa-308">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="1c2fa-309">`ChildComponent`W aplikacji przykładowej (*Components/ChildComponent. Razor*) pokazano, jak `onclick` program obsługi przycisku został skonfigurowany tak, aby otrzymać <xref:Microsoft.AspNetCore.Components.EventCallback> delegata z przykładu `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="1c2fa-309">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="1c2fa-310"><xref:Microsoft.AspNetCore.Components.EventCallback>Typ ma wartość `MouseEventArgs` , która jest odpowiednia dla `onclick` zdarzenia z urządzenia peryferyjnego:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-310">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="1c2fa-311">`ParentComponent`Ustawia element podrzędny <xref:Microsoft.AspNetCore.Components.EventCallback%601> ( `OnClickCallback` ) na jego `ShowMessage` metodę.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-311">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="1c2fa-312">*Strony/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-312">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="1c2fa-313">Gdy przycisk zostanie wybrany w `ChildComponent` :</span><span class="sxs-lookup"><span data-stu-id="1c2fa-313">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="1c2fa-314">`ParentComponent` `ShowMessage` Metoda jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-314">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="1c2fa-315">`messageText`jest aktualizowany i wyświetlany w `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="1c2fa-315">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="1c2fa-316">Wywołanie [StateHasChanged](xref:blazor/lifecycle#state-changes) nie jest wymagane w metodzie wywołania zwrotnego ( `ShowMessage` ).</span><span class="sxs-lookup"><span data-stu-id="1c2fa-316">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="1c2fa-317"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>jest automatycznie wywoływana w celu odrenderowania `ParentComponent` elementu, podobnie jak zdarzenia podrzędne wyzwala ponowne renderowanie składnika w obsłudze zdarzeń, które są wykonywane w ramach elementu podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-317"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="1c2fa-318"><xref:Microsoft.AspNetCore.Components.EventCallback>i <xref:Microsoft.AspNetCore.Components.EventCallback%601> Zezwalaj na asynchroniczne Delegaty.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-318"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="1c2fa-319"><xref:Microsoft.AspNetCore.Components.EventCallback%601>jest silnie wpisana i wymaga określonego typu argumentu.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-319"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="1c2fa-320"><xref:Microsoft.AspNetCore.Components.EventCallback>jest słabo wpisywany i zezwala na dowolny typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-320"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="1c2fa-321">Wywołaj <xref:Microsoft.AspNetCore.Components.EventCallback> lub <xref:Microsoft.AspNetCore.Components.EventCallback%601> z <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> i await <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="1c2fa-321">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="1c2fa-322">Używaj <xref:Microsoft.AspNetCore.Components.EventCallback> i <xref:Microsoft.AspNetCore.Components.EventCallback%601> dla parametrów składnika Obsługa zdarzeń i powiązania.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-322">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="1c2fa-323">Preferuj silnie wpisaną <xref:Microsoft.AspNetCore.Components.EventCallback%601> wartość <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="1c2fa-323">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="1c2fa-324"><xref:Microsoft.AspNetCore.Components.EventCallback%601>zapewnia lepszą opinię o błędach dla użytkowników składnika.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-324"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="1c2fa-325">Podobnie jak w przypadku innych programów obsługi zdarzeń interfejsu użytkownika, określenie parametru zdarzenia jest opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-325">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="1c2fa-326">Użyj <xref:Microsoft.AspNetCore.Components.EventCallback> w przypadku braku wartości przekazywania do wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-326">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="1c2fa-327">Zapobiegaj akcjom domyślnym</span><span class="sxs-lookup"><span data-stu-id="1c2fa-327">Prevent default actions</span></span>

<span data-ttu-id="1c2fa-328">Użyj [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) atrybutu dyrektywy, aby zapobiec domyślnej akcji dla zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-328">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="1c2fa-329">Po wybraniu klucza na urządzeniu wejściowym, gdy fokus elementu znajduje się w polu tekstowym, przeglądarka zwykle wyświetla znak klucza w polu tekstowym.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-329">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="1c2fa-330">W poniższym przykładzie zachowanie domyślne jest blokowane przez określenie `@onkeypress:preventDefault` atrybutu dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-330">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="1c2fa-331">Licznik jest zwiększany, a **+** klucz nie jest przechwytywany do `<input>` wartości elementu:</span><span class="sxs-lookup"><span data-stu-id="1c2fa-331">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

<span data-ttu-id="1c2fa-332">Określanie `@on{EVENT}:preventDefault` atrybutu bez wartości jest równoważne `@on{EVENT}:preventDefault="true"` .</span><span class="sxs-lookup"><span data-stu-id="1c2fa-332">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="1c2fa-333">Wartość atrybutu może również być wyrażeniem.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-333">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="1c2fa-334">W poniższym przykładzie `shouldPreventDefault` `bool` pole jest ustawione na jedną `true` lub `false` :</span><span class="sxs-lookup"><span data-stu-id="1c2fa-334">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="1c2fa-335">Procedura obsługi zdarzeń nie jest wymagana, aby zapobiec akcji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-335">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="1c2fa-336">Procedury obsługi zdarzeń i zapobiegania domyślnym scenariuszom akcji mogą być używane niezależnie.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-336">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="1c2fa-337">Zatrzymaj propagację zdarzeń</span><span class="sxs-lookup"><span data-stu-id="1c2fa-337">Stop event propagation</span></span>

<span data-ttu-id="1c2fa-338">Użyj [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) atrybutu dyrektywy, aby zatrzymać propagację zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="1c2fa-338">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="1c2fa-339">W poniższym przykładzie, zaznaczając pole wyboru, uniemożliwiają klikanie zdarzeń z drugiego elementu podrzędnego `<div>` od propagowania do obiektu nadrzędnego `<div>` :</span><span class="sxs-lookup"><span data-stu-id="1c2fa-339">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
