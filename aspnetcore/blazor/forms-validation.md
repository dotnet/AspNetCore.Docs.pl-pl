---
<span data-ttu-id="d2433-101">title: "ASP.NET Core Blazor Forms i walidacja" Author: Description: "Dowiedz się, jak używać scenariuszy formularzy i walidacji pól w Blazor ".</span><span class="sxs-lookup"><span data-stu-id="d2433-101">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="d2433-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d2433-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2433-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2433-103">'Blazor'</span></span>
- <span data-ttu-id="d2433-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2433-104">'Identity'</span></span>
- <span data-ttu-id="d2433-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2433-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2433-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2433-106">'Razor'</span></span>
- <span data-ttu-id="d2433-107">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d2433-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="d2433-108">ASP.NET Core Blazor formularzy i walidacji</span><span class="sxs-lookup"><span data-stu-id="d2433-108">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="d2433-109">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d2433-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d2433-110">Formularze i walidacje są obsługiwane w przypadku Blazor używania [adnotacji danych](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="d2433-110">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="d2433-111">Następujący `ExampleModel` Typ definiuje logikę walidacji przy użyciu adnotacji danych:</span><span class="sxs-lookup"><span data-stu-id="d2433-111">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="d2433-112">Formularz jest definiowany przy użyciu <xref:Microsoft.AspNetCore.Components.Forms.EditForm> składnika.</span><span class="sxs-lookup"><span data-stu-id="d2433-112">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="d2433-113">W poniższej formie przedstawiono typowe elementy, składniki i Razor kod:</span><span class="sxs-lookup"><span data-stu-id="d2433-113">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="d2433-114">W poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="d2433-114">In the preceding example:</span></span>

* <span data-ttu-id="d2433-115">Formularz sprawdza poprawność danych wejściowych użytkownika w `name` polu przy użyciu walidacji zdefiniowanej w `ExampleModel` typie.</span><span class="sxs-lookup"><span data-stu-id="d2433-115">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="d2433-116">Model jest tworzony w `@code` bloku składnika i przechowywany w prywatnym polu ( `exampleModel` ).</span><span class="sxs-lookup"><span data-stu-id="d2433-116">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="d2433-117">Pole jest przypisane do `Model` atrybutu `<EditForm>` elementu.</span><span class="sxs-lookup"><span data-stu-id="d2433-117">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="d2433-118"><xref:Microsoft.AspNetCore.Components.Forms.InputText> `@bind-Value` Powiązania składnika:</span><span class="sxs-lookup"><span data-stu-id="d2433-118">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="d2433-119">Właściwość modelu ( `exampleModel.Name` ) do <xref:Microsoft.AspNetCore.Components.Forms.InputText> `Value` właściwości składnika.</span><span class="sxs-lookup"><span data-stu-id="d2433-119">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="d2433-120">Aby uzyskać więcej informacji na temat powiązania właściwości, zobacz <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters> .</span><span class="sxs-lookup"><span data-stu-id="d2433-120">For more information on property binding, see <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="d2433-121">Delegowanie zdarzenia zmiany do <xref:Microsoft.AspNetCore.Components.Forms.InputText> `ValueChanged` właściwości składnika.</span><span class="sxs-lookup"><span data-stu-id="d2433-121">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="d2433-122"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Składnik dołącza obsługę walidacji przy użyciu adnotacji danych.</span><span class="sxs-lookup"><span data-stu-id="d2433-122">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="d2433-123"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Składnik podsumowuje komunikaty weryfikacyjne.</span><span class="sxs-lookup"><span data-stu-id="d2433-123">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="d2433-124">`HandleValidSubmit`jest wyzwalany po pomyślnym przesłaniu formularza (kończy walidację).</span><span class="sxs-lookup"><span data-stu-id="d2433-124">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="d2433-125">Zestaw wbudowanych składników wejściowych jest dostępny do odbierania i weryfikowania danych wejściowych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="d2433-125">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="d2433-126">Dane wejściowe są weryfikowane po ich zmianie i po przesłaniu formularza.</span><span class="sxs-lookup"><span data-stu-id="d2433-126">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="d2433-127">W poniższej tabeli przedstawiono dostępne składniki danych wejściowych.</span><span class="sxs-lookup"><span data-stu-id="d2433-127">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="d2433-128">Składnik wejściowy</span><span class="sxs-lookup"><span data-stu-id="d2433-128">Input component</span></span> | <span data-ttu-id="d2433-129">Renderowane jako&hellip;</span><span class="sxs-lookup"><span data-stu-id="d2433-129">Rendered as&hellip;</span></span> |
| ---
<span data-ttu-id="d2433-130">title: "ASP.NET Core Blazor Forms i walidacja" Author: Description: "Dowiedz się, jak używać scenariuszy formularzy i walidacji pól w Blazor ".</span><span class="sxs-lookup"><span data-stu-id="d2433-130">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="d2433-131">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d2433-131">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2433-132">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2433-132">'Blazor'</span></span>
- <span data-ttu-id="d2433-133">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2433-133">'Identity'</span></span>
- <span data-ttu-id="d2433-134">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2433-134">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2433-135">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2433-135">'Razor'</span></span>
- <span data-ttu-id="d2433-136">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d2433-136">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2433-137">title: "ASP.NET Core Blazor Forms i walidacja" Author: Description: "Dowiedz się, jak używać scenariuszy formularzy i walidacji pól w Blazor ".</span><span class="sxs-lookup"><span data-stu-id="d2433-137">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="d2433-138">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d2433-138">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2433-139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2433-139">'Blazor'</span></span>
- <span data-ttu-id="d2433-140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2433-140">'Identity'</span></span>
- <span data-ttu-id="d2433-141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2433-141">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2433-142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2433-142">'Razor'</span></span>
- <span data-ttu-id="d2433-143">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d2433-143">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2433-144">title: "ASP.NET Core Blazor Forms i walidacja" Author: Description: "Dowiedz się, jak używać scenariuszy formularzy i walidacji pól w Blazor ".</span><span class="sxs-lookup"><span data-stu-id="d2433-144">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="d2433-145">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d2433-145">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2433-146">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2433-146">'Blazor'</span></span>
- <span data-ttu-id="d2433-147">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2433-147">'Identity'</span></span>
- <span data-ttu-id="d2433-148">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2433-148">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2433-149">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2433-149">'Razor'</span></span>
- <span data-ttu-id="d2433-150">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d2433-150">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2433-151">title: "ASP.NET Core Blazor Forms i walidacja" Author: Description: "Dowiedz się, jak używać scenariuszy formularzy i walidacji pól w Blazor ".</span><span class="sxs-lookup"><span data-stu-id="d2433-151">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="d2433-152">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d2433-152">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2433-153">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2433-153">'Blazor'</span></span>
- <span data-ttu-id="d2433-154">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2433-154">'Identity'</span></span>
- <span data-ttu-id="d2433-155">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2433-155">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2433-156">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2433-156">'Razor'</span></span>
- <span data-ttu-id="d2433-157">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d2433-157">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2433-158">title: "ASP.NET Core Blazor Forms i walidacja" Author: Description: "Dowiedz się, jak używać scenariuszy formularzy i walidacji pól w Blazor ".</span><span class="sxs-lookup"><span data-stu-id="d2433-158">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="d2433-159">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d2433-159">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2433-160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2433-160">'Blazor'</span></span>
- <span data-ttu-id="d2433-161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2433-161">'Identity'</span></span>
- <span data-ttu-id="d2433-162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2433-162">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2433-163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2433-163">'Razor'</span></span>
- <span data-ttu-id="d2433-164">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d2433-164">'SignalR' uid:</span></span> 

<span data-ttu-id="d2433-165">-------- | ---title: "ASP.NET Core Blazor formularzy i walidacja" Author: Description: "Dowiedz się, jak używać scenariuszy i walidacji pól w Blazor ".</span><span class="sxs-lookup"><span data-stu-id="d2433-165">-------- | --- title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="d2433-166">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d2433-166">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2433-167">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2433-167">'Blazor'</span></span>
- <span data-ttu-id="d2433-168">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2433-168">'Identity'</span></span>
- <span data-ttu-id="d2433-169">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2433-169">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2433-170">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2433-170">'Razor'</span></span>
- <span data-ttu-id="d2433-171">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d2433-171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2433-172">title: "ASP.NET Core Blazor Forms i walidacja" Author: Description: "Dowiedz się, jak używać scenariuszy formularzy i walidacji pól w Blazor ".</span><span class="sxs-lookup"><span data-stu-id="d2433-172">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="d2433-173">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d2433-173">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2433-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2433-174">'Blazor'</span></span>
- <span data-ttu-id="d2433-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2433-175">'Identity'</span></span>
- <span data-ttu-id="d2433-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2433-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2433-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2433-177">'Razor'</span></span>
- <span data-ttu-id="d2433-178">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d2433-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2433-179">title: "ASP.NET Core Blazor Forms i walidacja" Author: Description: "Dowiedz się, jak używać scenariuszy formularzy i walidacji pól w Blazor ".</span><span class="sxs-lookup"><span data-stu-id="d2433-179">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="d2433-180">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d2433-180">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2433-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2433-181">'Blazor'</span></span>
- <span data-ttu-id="d2433-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2433-182">'Identity'</span></span>
- <span data-ttu-id="d2433-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2433-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2433-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2433-184">'Razor'</span></span>
- <span data-ttu-id="d2433-185">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d2433-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2433-186">title: "ASP.NET Core Blazor Forms i walidacja" Author: Description: "Dowiedz się, jak używać scenariuszy formularzy i walidacji pól w Blazor ".</span><span class="sxs-lookup"><span data-stu-id="d2433-186">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="d2433-187">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d2433-187">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2433-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2433-188">'Blazor'</span></span>
- <span data-ttu-id="d2433-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2433-189">'Identity'</span></span>
- <span data-ttu-id="d2433-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2433-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2433-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2433-191">'Razor'</span></span>
- <span data-ttu-id="d2433-192">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d2433-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2433-193">title: "ASP.NET Core Blazor Forms i walidacja" Author: Description: "Dowiedz się, jak używać scenariuszy formularzy i walidacji pól w Blazor ".</span><span class="sxs-lookup"><span data-stu-id="d2433-193">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="d2433-194">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d2433-194">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2433-195">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2433-195">'Blazor'</span></span>
- <span data-ttu-id="d2433-196">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2433-196">'Identity'</span></span>
- <span data-ttu-id="d2433-197">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2433-197">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2433-198">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2433-198">'Razor'</span></span>
- <span data-ttu-id="d2433-199">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d2433-199">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2433-200">title: "ASP.NET Core Blazor Forms i walidacja" Author: Description: "Dowiedz się, jak używać scenariuszy formularzy i walidacji pól w Blazor ".</span><span class="sxs-lookup"><span data-stu-id="d2433-200">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="d2433-201">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d2433-201">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2433-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2433-202">'Blazor'</span></span>
- <span data-ttu-id="d2433-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2433-203">'Identity'</span></span>
- <span data-ttu-id="d2433-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2433-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2433-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2433-205">'Razor'</span></span>
- <span data-ttu-id="d2433-206">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d2433-206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d2433-207">title: "ASP.NET Core Blazor Forms i walidacja" Author: Description: "Dowiedz się, jak używać scenariuszy formularzy i walidacji pól w Blazor ".</span><span class="sxs-lookup"><span data-stu-id="d2433-207">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="d2433-208">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d2433-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d2433-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d2433-209">'Blazor'</span></span>
- <span data-ttu-id="d2433-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d2433-210">'Identity'</span></span>
- <span data-ttu-id="d2433-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d2433-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="d2433-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d2433-212">'Razor'</span></span>
- <span data-ttu-id="d2433-213">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="d2433-213">'SignalR' uid:</span></span> 

<span data-ttu-id="d2433-214">---------- | | <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |</span><span class="sxs-lookup"><span data-stu-id="d2433-214">---------- | | <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |</span></span>

<span data-ttu-id="d2433-215">Wszystkie składniki danych wejściowych, w tym <xref:Microsoft.AspNetCore.Components.Forms.EditForm> , obsługują dowolne atrybuty.</span><span class="sxs-lookup"><span data-stu-id="d2433-215">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="d2433-216">Dowolny atrybut, który nie jest zgodny z parametrem składnika, jest dodawany do renderowanego elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="d2433-216">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="d2433-217">Składniki wejściowe zapewniają domyślne zachowanie podczas sprawdzania poprawności edycji i zmiany ich klasy CSS, aby odzwierciedlały stan pola.</span><span class="sxs-lookup"><span data-stu-id="d2433-217">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="d2433-218">Niektóre składniki obejmują przydatne logiki analizy.</span><span class="sxs-lookup"><span data-stu-id="d2433-218">Some components include useful parsing logic.</span></span> <span data-ttu-id="d2433-219">Na przykład i bezproblemowo <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> obsłużyć wartości, które można przeanalizować, rejestrując je jako błędy walidacji.</span><span class="sxs-lookup"><span data-stu-id="d2433-219">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="d2433-220">Typy, które mogą akceptować wartości null, obsługują również wartość null pola docelowego (na przykład `int?` ).</span><span class="sxs-lookup"><span data-stu-id="d2433-220">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="d2433-221">Następujący `Starship` Typ definiuje logikę walidacji przy użyciu większego zestawu właściwości i adnotacji danych niż wcześniej `ExampleModel` :</span><span class="sxs-lookup"><span data-stu-id="d2433-221">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

<span data-ttu-id="d2433-222">W powyższym przykładzie `Description` jest opcjonalne, ponieważ nie są obecne adnotacje danych.</span><span class="sxs-lookup"><span data-stu-id="d2433-222">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="d2433-223">Następujący formularz sprawdza poprawność danych wejściowych użytkownika przy użyciu weryfikacji zdefiniowanej w `Starship` modelu:</span><span class="sxs-lookup"><span data-stu-id="d2433-223">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="d2433-224"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>Tworzy <xref:Microsoft.AspNetCore.Components.Forms.EditContext> jako [wartość kaskadową](xref:blazor/components#cascading-values-and-parameters) , która śledzi metadane dotyczące procesu edycji, w tym pola, które zostały zmodyfikowane i bieżące komunikaty weryfikacyjne.</span><span class="sxs-lookup"><span data-stu-id="d2433-224">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="d2433-225"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>Zapewnia również wygodne zdarzenia dla prawidłowych i nieprawidłowych przesyłania ( <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> , <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit> ).</span><span class="sxs-lookup"><span data-stu-id="d2433-225">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> also provides convenient events for valid and invalid submits (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>).</span></span> <span data-ttu-id="d2433-226">Alternatywnie można użyć <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> do wyzwolenia walidacji i sprawdzenia wartości pól z niestandardowym kodem walidacji.</span><span class="sxs-lookup"><span data-stu-id="d2433-226">Alternatively, use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="d2433-227">W poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="d2433-227">In the following example:</span></span>

* <span data-ttu-id="d2433-228">`HandleSubmit`Metoda jest uruchamiana po wybraniu przycisku **Prześlij** .</span><span class="sxs-lookup"><span data-stu-id="d2433-228">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="d2433-229">Formularz zostanie sprawdzony przy użyciu formularza <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="d2433-229">The form is validated using the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
* <span data-ttu-id="d2433-230">Formularz jest weryfikowany przez przekazanie <xref:Microsoft.AspNetCore.Components.Forms.EditContext> do `ServerValidate` metody, która wywołuje punkt końcowy interfejsu API sieci Web na serwerze (*niepokazywany*).</span><span class="sxs-lookup"><span data-stu-id="d2433-230">The form is further validated by passing the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="d2433-231">Dodatkowy kod jest uruchamiany w zależności od wyniku sprawdzenia poprawności po stronie klienta i serwera `isValid` .</span><span class="sxs-lookup"><span data-stu-id="d2433-231">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

```razor
<EditForm EditContext="@editContext" OnSubmit="HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate() && 
            await ServerValidate(editContext);

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }

    private async Task<bool> ServerValidate(EditContext editContext)
    {
        var serverChecksValid = ...

        return serverChecksValid;
    }
}
```

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="d2433-232">InputText na podstawie zdarzenia wejściowego</span><span class="sxs-lookup"><span data-stu-id="d2433-232">InputText based on the input event</span></span>

<span data-ttu-id="d2433-233">Użyj <xref:Microsoft.AspNetCore.Components.Forms.InputText> składnika, aby utworzyć niestandardowy składnik, który używa `input` zdarzenia zamiast `change` zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="d2433-233">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="d2433-234">Utwórz składnik z następującą adiustacją i użyj składnika, tak jak <xref:Microsoft.AspNetCore.Components.Forms.InputText> jest używany:</span><span class="sxs-lookup"><span data-stu-id="d2433-234">Create a component with the following markup, and use the component just as <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="d2433-235">Pracuj z przyciskami radiowymi</span><span class="sxs-lookup"><span data-stu-id="d2433-235">Work with radio buttons</span></span>

<span data-ttu-id="d2433-236">Podczas pracy z przyciskami radiowymi w formularzu powiązanie danych jest obsługiwane inaczej niż inne elementy, ponieważ przyciski radiowe są oceniane jako Grupa.</span><span class="sxs-lookup"><span data-stu-id="d2433-236">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="d2433-237">Wartość każdego przycisku radiowego jest stała, ale wartość grupy przycisków radiowych jest wartością wybranego przycisku radiowego.</span><span class="sxs-lookup"><span data-stu-id="d2433-237">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="d2433-238">Poniższy przykład pokazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="d2433-238">The following example shows how to:</span></span>

* <span data-ttu-id="d2433-239">Obsługa powiązania danych dla grupy przycisków radiowych.</span><span class="sxs-lookup"><span data-stu-id="d2433-239">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="d2433-240">Obsługa walidacji przy użyciu `InputRadio` składnika niestandardowego.</span><span class="sxs-lookup"><span data-stu-id="d2433-240">Support validation using a custom `InputRadio` component.</span></span>

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

<span data-ttu-id="d2433-241">Poniższe <xref:Microsoft.AspNetCore.Components.Forms.EditForm> składniki używają powyższego `InputRadio` składnika do uzyskania i zweryfikowania klasyfikacji od użytkownika:</span><span class="sxs-lookup"><span data-stu-id="d2433-241">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

    private void HandleValidSubmit()
    {
        Console.WriteLine("valid");
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

## <a name="validation-support"></a><span data-ttu-id="d2433-242">Obsługa walidacji</span><span class="sxs-lookup"><span data-stu-id="d2433-242">Validation support</span></span>

<span data-ttu-id="d2433-243"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Składnik dołącza obsługę walidacji przy użyciu adnotacji danych do kaskadowo <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="d2433-243">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="d2433-244">Włączenie obsługi walidacji przy użyciu adnotacji danych wymaga tego jawnego gestu.</span><span class="sxs-lookup"><span data-stu-id="d2433-244">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="d2433-245">Aby użyć innego systemu sprawdzania poprawności niż adnotacje danych, Zastąp zmienną <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> implementacją niestandardową.</span><span class="sxs-lookup"><span data-stu-id="d2433-245">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="d2433-246">Implementacja ASP.NET Core jest dostępna do inspekcji w źródle referencyjnym: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="d2433-246">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="d2433-247">wykonuje dwa typy walidacji:</span><span class="sxs-lookup"><span data-stu-id="d2433-247"> performs two types of validation:</span></span>

* <span data-ttu-id="d2433-248">*Sprawdzanie poprawności pola* jest wykonywane, gdy użytkownik wyprowadzi karty z pola.</span><span class="sxs-lookup"><span data-stu-id="d2433-248">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="d2433-249">Podczas sprawdzania poprawności pola <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnik kojarzy wszystkie zgłoszone wyniki walidacji z polem.</span><span class="sxs-lookup"><span data-stu-id="d2433-249">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="d2433-250">*Sprawdzanie poprawności modelu* jest wykonywane, gdy użytkownik prześle formularz.</span><span class="sxs-lookup"><span data-stu-id="d2433-250">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="d2433-251">Podczas walidacji modelu <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnik próbuje określić pole na podstawie nazwy elementu członkowskiego, który raportuje wynik sprawdzania poprawności.</span><span class="sxs-lookup"><span data-stu-id="d2433-251">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="d2433-252">Wyniki walidacji, które nie są skojarzone z poszczególnymi elementami członkowskimi, są skojarzone z modelem, a nie polem.</span><span class="sxs-lookup"><span data-stu-id="d2433-252">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="d2433-253">Składniki podsumowania walidacji i komunikatów weryfikacji</span><span class="sxs-lookup"><span data-stu-id="d2433-253">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="d2433-254"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Składnik podsumowuje wszystkie komunikaty weryfikacyjne podobne do [pomocnika tagów podsumowania weryfikacji](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="d2433-254">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="d2433-255">Wyprowadź komunikaty weryfikacji dla określonego modelu z `Model` parametrem:</span><span class="sxs-lookup"><span data-stu-id="d2433-255">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="d2433-256"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Składnik wyświetla komunikaty sprawdzania poprawności dla określonego pola, które jest podobne do [pomocnika tagów komunikatu weryfikacji](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="d2433-256">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="d2433-257">Określ pole do walidacji z `For` atrybutem i wyrażeniem lambda, które nazywa właściwość modelu:</span><span class="sxs-lookup"><span data-stu-id="d2433-257">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="d2433-258"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Składniki i <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> obsługują dowolne atrybuty.</span><span class="sxs-lookup"><span data-stu-id="d2433-258">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="d2433-259">Dowolny atrybut, który nie jest zgodny z parametrem składnika, jest dodawany do wygenerowanego `<div>` `<ul>` elementu or.</span><span class="sxs-lookup"><span data-stu-id="d2433-259">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="d2433-260">Niestandardowe atrybuty walidacji</span><span class="sxs-lookup"><span data-stu-id="d2433-260">Custom validation attributes</span></span>

<span data-ttu-id="d2433-261">Aby upewnić się, że wynik walidacji jest prawidłowo skojarzony z polem przy użyciu [niestandardowego atrybutu walidacji](xref:mvc/models/validation#custom-attributes), należy przekazać kontekst walidacji <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> podczas tworzenia <xref:System.ComponentModel.DataAnnotations.ValidationResult> :</span><span class="sxs-lookup"><span data-stu-id="d2433-261">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class MyCustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

### <a name="blazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="d2433-262">Pakiet weryfikacji adnotacji danych</span><span class="sxs-lookup"><span data-stu-id="d2433-262"> data annotations validation package</span></span>

<span data-ttu-id="d2433-263">[Microsoft. AspNetCore. Components. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) to pakiet, który wypełnia luki w środowisku walidacji przy użyciu <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnika.</span><span class="sxs-lookup"><span data-stu-id="d2433-263">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="d2433-264">Pakiet jest obecnie *eksperymentalny*.</span><span class="sxs-lookup"><span data-stu-id="d2433-264">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="d2433-265">[CompareProperty] — atrybut</span><span class="sxs-lookup"><span data-stu-id="d2433-265">[CompareProperty] attribute</span></span>

<span data-ttu-id="d2433-266"><xref:System.ComponentModel.DataAnnotations.CompareAttribute>Nie działa dobrze ze <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> składnikiem, ponieważ nie kojarzy wyniku walidacji z określonym elementem członkowskim.</span><span class="sxs-lookup"><span data-stu-id="d2433-266">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="d2433-267">Może to spowodować niespójne zachowanie między walidacją na poziomie pola i po sprawdzeniu poprawności całego modelu podczas przesyłania.</span><span class="sxs-lookup"><span data-stu-id="d2433-267">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="d2433-268">Pakiet *eksperymentalny* [Microsoft. AspNetCore. Components. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) wprowadza dodatkowy atrybut sprawdzania poprawności, `ComparePropertyAttribute` który działa wokół tych ograniczeń.</span><span class="sxs-lookup"><span data-stu-id="d2433-268">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="d2433-269">W Blazor aplikacji, `[CompareProperty]` jest bezpośrednią wymianą dla [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) atrybutu.</span><span class="sxs-lookup"><span data-stu-id="d2433-269">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="d2433-270">Modele zagnieżdżone, typy kolekcji i typy złożone</span><span class="sxs-lookup"><span data-stu-id="d2433-270">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="d2433-271">zapewnia obsługę sprawdzania poprawności formularza przy użyciu adnotacji danych z wbudowaną <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="d2433-271"> provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="d2433-272">Jednak program <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> sprawdza poprawność właściwości najwyższego poziomu modelu powiązanego z formularzem, który nie jest właściwościami typu Collection-lub złożonego.</span><span class="sxs-lookup"><span data-stu-id="d2433-272">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="d2433-273">Aby sprawdzić poprawność całego grafu obiektów modelu powiązanego, w tym właściwości kolekcji i typu złożonego, użyj `ObjectGraphDataAnnotationsValidator` dostarczonej przez *eksperymentalny* pakiet [Microsoft. AspNetCore. Components. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :</span><span class="sxs-lookup"><span data-stu-id="d2433-273">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="d2433-274">Dodawanie adnotacji do właściwości modelu za pomocą `[ValidateComplexType]` .</span><span class="sxs-lookup"><span data-stu-id="d2433-274">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="d2433-275">W poniższych klasach modelu `ShipDescription` Klasa zawiera dodatkowe adnotacje danych do zweryfikowania, gdy model jest powiązany z formularzem:</span><span class="sxs-lookup"><span data-stu-id="d2433-275">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="d2433-276">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="d2433-276">*Starship.cs*:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; }

    ...
}
```

<span data-ttu-id="d2433-277">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="d2433-277">*ShipDescription.cs*:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }
    
    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="d2433-278">Włączanie przycisku przesyłania na podstawie walidacji formularza</span><span class="sxs-lookup"><span data-stu-id="d2433-278">Enable the submit button based on form validation</span></span>

<span data-ttu-id="d2433-279">Aby włączyć i wyłączyć przycisk Prześlij na podstawie walidacji formularza:</span><span class="sxs-lookup"><span data-stu-id="d2433-279">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="d2433-280">Użyj formularza, <xref:Microsoft.AspNetCore.Components.Forms.EditContext> Aby przypisać model, gdy składnik zostanie zainicjowany.</span><span class="sxs-lookup"><span data-stu-id="d2433-280">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="d2433-281">Sprawdź poprawność formularza w <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> wywołaniu zwrotnym kontekstu, aby włączyć i wyłączyć przycisk Prześlij.</span><span class="sxs-lookup"><span data-stu-id="d2433-281">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="d2433-282">Odpinanie programu obsługi zdarzeń w `Dispose` metodzie.</span><span class="sxs-lookup"><span data-stu-id="d2433-282">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="d2433-283">Aby uzyskać więcej informacji, zobacz <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="d2433-283">For more information, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

<span data-ttu-id="d2433-284">W poprzednim przykładzie ustaw wartość `formInvalid` `false` :</span><span class="sxs-lookup"><span data-stu-id="d2433-284">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="d2433-285">Formularz jest wstępnie załadowany z prawidłowymi wartościami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="d2433-285">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="d2433-286">Przycisk Prześlij ma być włączony podczas ładowania formularza.</span><span class="sxs-lookup"><span data-stu-id="d2433-286">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="d2433-287">Efektem ubocznym poprzedniego podejścia jest to, że <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> składnik jest wypełniany przy użyciu nieprawidłowych pól, gdy użytkownik przeprowadzi interakcję z jednym polem.</span><span class="sxs-lookup"><span data-stu-id="d2433-287">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="d2433-288">Ten scenariusz można rozwiązać w jeden z następujących sposobów:</span><span class="sxs-lookup"><span data-stu-id="d2433-288">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="d2433-289">Nie używaj <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> składnika w formularzu.</span><span class="sxs-lookup"><span data-stu-id="d2433-289">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="d2433-290">Ustaw <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> składnik jako widoczny po wybraniu przycisku Prześlij (na przykład w `HandleValidSubmit` metodzie).</span><span class="sxs-lookup"><span data-stu-id="d2433-290">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```
