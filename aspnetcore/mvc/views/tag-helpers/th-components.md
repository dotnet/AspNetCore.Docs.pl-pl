---
title: Oznacz składniki pomocnika w ASP.NET Core
author: scottaddie
description: Informacje o składnikach pomocników tagów i sposobach ich użycia w ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: fb0bda0cf8d225df4c58ae43f81ed0dce10c1adc
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587154"
---
# <a name="tag-helper-components-in-aspnet-core"></a><span data-ttu-id="c2ac7-103">Oznacz składniki pomocnika w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c2ac7-103">Tag Helper Components in ASP.NET Core</span></span>

<span data-ttu-id="c2ac7-104">Przez [Scott Addie](https://twitter.com/Scott_Addie) i [Fiyaz bin Hasan](https://github.com/fiyazbinhasan)</span><span class="sxs-lookup"><span data-stu-id="c2ac7-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)</span></span>

<span data-ttu-id="c2ac7-105">Składnik pomocnika tagów to pomocnik tagów, który umożliwia warunkowe modyfikowanie lub Dodawanie elementów HTML z kodu po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-105">A Tag Helper Component is a Tag Helper that allows you to conditionally modify or add HTML elements from server-side code.</span></span> <span data-ttu-id="c2ac7-106">Ta funkcja jest dostępna w ASP.NET Core 2,0 lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-106">This feature is available in ASP.NET Core 2.0 or later.</span></span>

<span data-ttu-id="c2ac7-107">ASP.NET Core obejmuje dwa wbudowane składniki pomocnika tagów: `head` i `body` .</span><span class="sxs-lookup"><span data-stu-id="c2ac7-107">ASP.NET Core includes two built-in Tag Helper Components: `head` and `body`.</span></span> <span data-ttu-id="c2ac7-108">Znajdują się one w <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> przestrzeni nazw i mogą być używane zarówno w MVC, jak i Razor stronach.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-108">They're located in the <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> namespace and can be used in both MVC and Razor Pages.</span></span> <span data-ttu-id="c2ac7-109">Składniki pomocnika tagów nie wymagają rejestracji w aplikacji w *_ViewImports. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-109">Tag Helper Components don't require registration with the app in *_ViewImports.cshtml*.</span></span>

<span data-ttu-id="c2ac7-110">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c2ac7-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="use-cases"></a><span data-ttu-id="c2ac7-111">Przypadki zastosowań</span><span class="sxs-lookup"><span data-stu-id="c2ac7-111">Use cases</span></span>

<span data-ttu-id="c2ac7-112">Dwa typowe przypadki użycia składników pomocnika tagów obejmują:</span><span class="sxs-lookup"><span data-stu-id="c2ac7-112">Two common use cases of Tag Helper Components include:</span></span>

1. [<span data-ttu-id="c2ac7-113">Wprowadzanie `<link>` do `<head>` .</span><span class="sxs-lookup"><span data-stu-id="c2ac7-113">Injecting a `<link>` into the `<head>`.</span></span>](#inject-into-html-head-element)
1. [<span data-ttu-id="c2ac7-114">Wprowadzanie `<script>` do `<body>` .</span><span class="sxs-lookup"><span data-stu-id="c2ac7-114">Injecting a `<script>` into the `<body>`.</span></span>](#inject-into-html-body-element)

<span data-ttu-id="c2ac7-115">W poniższych sekcjach opisano te przypadki użycia.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-115">The following sections describe these use cases.</span></span>

### <a name="inject-into-html-head-element"></a><span data-ttu-id="c2ac7-116">Wsuń do elementu głównego HTML</span><span class="sxs-lookup"><span data-stu-id="c2ac7-116">Inject into HTML head element</span></span>

<span data-ttu-id="c2ac7-117">Wewnątrz elementu HTML `<head>` pliki CSS są zwykle importowane z `<link>` elementem HTML.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-117">Inside the HTML `<head>` element, CSS files are commonly imported with the HTML `<link>` element.</span></span> <span data-ttu-id="c2ac7-118">Poniższy kod `<link>` wprowadza element do `<head>` elementu przy użyciu `head` składnika pomocnika tagów:</span><span class="sxs-lookup"><span data-stu-id="c2ac7-118">The following code injects a `<link>` element into the `<head>` element using the `head` Tag Helper Component:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

<span data-ttu-id="c2ac7-119">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="c2ac7-119">In the preceding code:</span></span>

* <span data-ttu-id="c2ac7-120">`AddressStyleTagHelperComponent` implementuje <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent> .</span><span class="sxs-lookup"><span data-stu-id="c2ac7-120">`AddressStyleTagHelperComponent` implements <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>.</span></span> <span data-ttu-id="c2ac7-121">Streszczenie:</span><span class="sxs-lookup"><span data-stu-id="c2ac7-121">The abstraction:</span></span>
  * <span data-ttu-id="c2ac7-122">Umożliwia inicjowanie klasy z <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext> .</span><span class="sxs-lookup"><span data-stu-id="c2ac7-122">Allows initialization of the class with a <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.</span></span>
  * <span data-ttu-id="c2ac7-123">Umożliwia używanie składników pomocnika tagów do dodawania lub modyfikowania elementów HTML.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-123">Enables the use of Tag Helper Components to add or modify HTML elements.</span></span>
* <span data-ttu-id="c2ac7-124"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*>Właściwość definiuje kolejność, w jakiej składniki są renderowane.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-124">The <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> property defines the order in which the Components are rendered.</span></span> <span data-ttu-id="c2ac7-125">`Order` jest konieczne, gdy w aplikacji istnieje wiele użycia składników pomocnika tagów.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-125">`Order` is necessary when there are multiple usages of Tag Helper Components in an app.</span></span>
* <span data-ttu-id="c2ac7-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> porównuje <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> wartość właściwości kontekstu wykonania z `head` .</span><span class="sxs-lookup"><span data-stu-id="c2ac7-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> compares the execution context's <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> property value to `head`.</span></span> <span data-ttu-id="c2ac7-127">Jeśli wynikiem porównania jest wartość true, zawartość `_style` pola jest wstrzykiwana do `<head>` elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-127">If the comparison evaluates to true, the content of the `_style` field is injected into the HTML `<head>` element.</span></span>

### <a name="inject-into-html-body-element"></a><span data-ttu-id="c2ac7-128">Wsuń do elementu treści HTML</span><span class="sxs-lookup"><span data-stu-id="c2ac7-128">Inject into HTML body element</span></span>

<span data-ttu-id="c2ac7-129">`body`Składnik pomocnika tagów może wstrzyknąć `<script>` element do `<body>` elementu.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-129">The `body` Tag Helper Component can inject a `<script>` element into the `<body>` element.</span></span> <span data-ttu-id="c2ac7-130">Poniższy kod ilustruje tę technikę:</span><span class="sxs-lookup"><span data-stu-id="c2ac7-130">The following code demonstrates this technique:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

<span data-ttu-id="c2ac7-131">Do przechowywania elementu używany jest oddzielny plik HTML `<script>` .</span><span class="sxs-lookup"><span data-stu-id="c2ac7-131">A separate HTML file is used to store the `<script>` element.</span></span> <span data-ttu-id="c2ac7-132">Plik HTML sprawia, że kod jest bardziej przejrzysty i bardziej czytelny.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-132">The HTML file makes the code cleaner and more maintainable.</span></span> <span data-ttu-id="c2ac7-133">Poprzedni kod odczytuje zawartość *TagHelpers/templates/AddressToolTipScript.html* i dołącza ją do danych wyjściowych pomocnika tagów.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-133">The preceding code reads the contents of *TagHelpers/Templates/AddressToolTipScript.html* and appends it with the Tag Helper output.</span></span> <span data-ttu-id="c2ac7-134">Plik *AddressToolTipScript.html* zawiera następujące znaczniki:</span><span class="sxs-lookup"><span data-stu-id="c2ac7-134">The *AddressToolTipScript.html* file includes the following markup:</span></span>

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

<span data-ttu-id="c2ac7-135">Poprzedni kod tworzy element [widget etykietki narzędzia Bootstrap](https://getbootstrap.com/docs/3.3/javascript/#tooltips) dla każdego `<address>` elementu, który zawiera `printable` atrybut.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-135">The preceding code binds a [Bootstrap tooltip widget](https://getbootstrap.com/docs/3.3/javascript/#tooltips) to any `<address>` element that includes a `printable` attribute.</span></span> <span data-ttu-id="c2ac7-136">Efekt jest widoczny, gdy wskaźnik myszy znajduje się nad elementem.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-136">The effect is visible when a mouse pointer hovers over the element.</span></span>

## <a name="register-a-component"></a><span data-ttu-id="c2ac7-137">Rejestrowanie składnika</span><span class="sxs-lookup"><span data-stu-id="c2ac7-137">Register a Component</span></span>

<span data-ttu-id="c2ac7-138">Składnik pomocnika tagów należy dodać do kolekcji składników pomocnika tagów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-138">A Tag Helper Component must be added to the app's Tag Helper Components collection.</span></span> <span data-ttu-id="c2ac7-139">Istnieją trzy sposoby dodawania do kolekcji:</span><span class="sxs-lookup"><span data-stu-id="c2ac7-139">There are three ways to add to the collection:</span></span>

* [<span data-ttu-id="c2ac7-140">Rejestracja za pośrednictwem kontenera usług</span><span class="sxs-lookup"><span data-stu-id="c2ac7-140">Registration via services container</span></span>](#registration-via-services-container)
* [<span data-ttu-id="c2ac7-141">Rejestracja za pośrednictwem Razor pliku</span><span class="sxs-lookup"><span data-stu-id="c2ac7-141">Registration via Razor file</span></span>](#registration-via-razor-file)
* [<span data-ttu-id="c2ac7-142">Rejestracja za pośrednictwem modelu lub kontrolera strony</span><span class="sxs-lookup"><span data-stu-id="c2ac7-142">Registration via Page Model or controller</span></span>](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a><span data-ttu-id="c2ac7-143">Rejestracja za pośrednictwem kontenera usług</span><span class="sxs-lookup"><span data-stu-id="c2ac7-143">Registration via services container</span></span>

<span data-ttu-id="c2ac7-144">Jeśli Klasa składnika pomocnika tagów nie jest zarządzana za pomocą programu <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager> , musi być zarejestrowana w systemie [wtrysku zależności (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="c2ac7-144">If the Tag Helper Component class isn't managed with <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, it must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) system.</span></span> <span data-ttu-id="c2ac7-145">Poniższy `Startup.ConfigureServices` kod rejestruje `AddressStyleTagHelperComponent` `AddressScriptTagHelperComponent` klasy i z [przejściowym okresem istnienia](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span><span class="sxs-lookup"><span data-stu-id="c2ac7-145">The following `Startup.ConfigureServices` code registers the `AddressStyleTagHelperComponent` and `AddressScriptTagHelperComponent` classes with a [transient lifetime](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-razor-file"></a><span data-ttu-id="c2ac7-146">Rejestracja za pośrednictwem Razor pliku</span><span class="sxs-lookup"><span data-stu-id="c2ac7-146">Registration via Razor file</span></span>

<span data-ttu-id="c2ac7-147">Jeśli składnik pomocnika tagów nie jest zarejestrowany przy użyciu DI, można go zarejestrować ze Razor strony stron lub widoku MVC.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-147">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page or an MVC view.</span></span> <span data-ttu-id="c2ac7-148">Ta technika służy do kontrolowania wstrzykniętego znacznika i kolejności wykonywania składników z Razor pliku.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-148">This technique is used for controlling the injected markup and the component execution order from a Razor file.</span></span>

<span data-ttu-id="c2ac7-149">`ITagHelperComponentManager` służy do dodawania składników pomocnika tagów lub usuwania ich z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-149">`ITagHelperComponentManager` is used to add Tag Helper Components or remove them from the app.</span></span> <span data-ttu-id="c2ac7-150">Poniższy kod ilustruje tę technikę `AddressTagHelperComponent` :</span><span class="sxs-lookup"><span data-stu-id="c2ac7-150">The following code demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

<span data-ttu-id="c2ac7-151">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="c2ac7-151">In the preceding code:</span></span>

* <span data-ttu-id="c2ac7-152">`@inject`Dyrektywa zawiera wystąpienie `ITagHelperComponentManager` .</span><span class="sxs-lookup"><span data-stu-id="c2ac7-152">The `@inject` directive provides an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="c2ac7-153">Wystąpienie jest przypisane do zmiennej o nazwie w `manager` celu uzyskania dostępu do Razor pliku.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-153">The instance is assigned to a variable named `manager` for access downstream in the Razor file.</span></span>
* <span data-ttu-id="c2ac7-154">Wystąpienie `AddressTagHelperComponent` jest dodawane do kolekcji składników pomocnika tagów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-154">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

<span data-ttu-id="c2ac7-155">`AddressTagHelperComponent` zmodyfikowano, aby pomieścić konstruktora, który akceptuje `markup` `order` Parametry i:</span><span class="sxs-lookup"><span data-stu-id="c2ac7-155">`AddressTagHelperComponent` is modified to accommodate a constructor that accepts the `markup` and `order` parameters:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

<span data-ttu-id="c2ac7-156">Podany `markup` parametr jest używany w `ProcessAsync` następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="c2ac7-156">The provided `markup` parameter is used in `ProcessAsync` as follows:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a><span data-ttu-id="c2ac7-157">Rejestracja za pośrednictwem modelu lub kontrolera strony</span><span class="sxs-lookup"><span data-stu-id="c2ac7-157">Registration via Page Model or controller</span></span>

<span data-ttu-id="c2ac7-158">Jeśli składnik pomocnika tagów nie jest zarejestrowany w programie DI, może być zarejestrowany z poziomu Razor modelu strony stron lub kontrolera MVC.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-158">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page model or an MVC controller.</span></span> <span data-ttu-id="c2ac7-159">Ta technika jest przydatna do oddzielania logiki języka C# z Razor plików.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-159">This technique is useful for separating C# logic from Razor files.</span></span>

<span data-ttu-id="c2ac7-160">Iniekcja konstruktora jest używana w celu uzyskania dostępu do wystąpienia `ITagHelperComponentManager` .</span><span class="sxs-lookup"><span data-stu-id="c2ac7-160">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="c2ac7-161">Składnik pomocnika tagów jest dodawany do kolekcji składników pomocnika tagów wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-161">The Tag Helper Component is added to the instance's Tag Helper Components collection.</span></span> <span data-ttu-id="c2ac7-162">Poniższy Razor model strony przedstawia tę technikę `AddressTagHelperComponent` :</span><span class="sxs-lookup"><span data-stu-id="c2ac7-162">The following Razor Pages page model demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

<span data-ttu-id="c2ac7-163">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="c2ac7-163">In the preceding code:</span></span>

* <span data-ttu-id="c2ac7-164">Iniekcja konstruktora jest używana w celu uzyskania dostępu do wystąpienia `ITagHelperComponentManager` .</span><span class="sxs-lookup"><span data-stu-id="c2ac7-164">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span>
* <span data-ttu-id="c2ac7-165">Wystąpienie `AddressTagHelperComponent` jest dodawane do kolekcji składników pomocnika tagów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-165">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

## <a name="create-a-component"></a><span data-ttu-id="c2ac7-166">Tworzenie składnika</span><span class="sxs-lookup"><span data-stu-id="c2ac7-166">Create a Component</span></span>

<span data-ttu-id="c2ac7-167">Aby utworzyć składnik pomocnika tagów niestandardowych:</span><span class="sxs-lookup"><span data-stu-id="c2ac7-167">To create a custom Tag Helper Component:</span></span>

* <span data-ttu-id="c2ac7-168">Utwórz klasę publiczną wyprowadzaną z <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper> .</span><span class="sxs-lookup"><span data-stu-id="c2ac7-168">Create a public class deriving from <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.</span></span>
* <span data-ttu-id="c2ac7-169">Zastosuj [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) atrybut do klasy.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-169">Apply an [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) attribute to the class.</span></span> <span data-ttu-id="c2ac7-170">Określ nazwę docelowego elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-170">Specify the name of the target HTML element.</span></span>
* <span data-ttu-id="c2ac7-171">*Opcjonalne*: Zastosuj [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) atrybut do klasy, aby pominąć wyświetlanie typu w IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-171">*Optional*: Apply an [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) attribute to the class to suppress the type's display in IntelliSense.</span></span>

<span data-ttu-id="c2ac7-172">Poniższy kod tworzy składnik pomocnika tagów niestandardowych, który jest przeznaczony dla `<address>` elementu HTML:</span><span class="sxs-lookup"><span data-stu-id="c2ac7-172">The following code creates a custom Tag Helper Component that targets the `<address>` HTML element:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

<span data-ttu-id="c2ac7-173">Użyj `address` składnika pomocnika tagów niestandardowych, aby wstrzyknąć znacznik HTML w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="c2ac7-173">Use the custom `address` Tag Helper Component to inject HTML markup as follows:</span></span>

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open(" +
        "'https://binged.it/2AXRRYw')\">" +
        "<span class='glyphicon glyphicon-road' aria-hidden='true'></span>" +
        "</button>";

    public override int Order => 3;

    public override async Task ProcessAsync(TagHelperContext context,
                                            TagHelperOutput output)
    {
        if (string.Equals(context.TagName, "address",
                StringComparison.OrdinalIgnoreCase) &&
            output.Attributes.ContainsName("printable"))
        {
            var content = await output.GetChildContentAsync();
            output.Content.SetHtmlContent(
                $"<div>{content.GetContent()}</div>{_printableButton}");
        }
    }
}
```

<span data-ttu-id="c2ac7-174">Poprzednia `ProcessAsync` metoda wprowadza kod HTML udostępniony do <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> zgodnego `<address>` elementu.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-174">The preceding `ProcessAsync` method injects the HTML provided to <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> into the matching `<address>` element.</span></span> <span data-ttu-id="c2ac7-175">Iniekcja występuje, gdy:</span><span class="sxs-lookup"><span data-stu-id="c2ac7-175">The injection occurs when:</span></span>

* <span data-ttu-id="c2ac7-176">Wartość właściwości kontekstu wykonania `TagName` jest równa `address` .</span><span class="sxs-lookup"><span data-stu-id="c2ac7-176">The execution context's `TagName` property value equals `address`.</span></span>
* <span data-ttu-id="c2ac7-177">Odpowiadający `<address>` element ma `printable` atrybut.</span><span class="sxs-lookup"><span data-stu-id="c2ac7-177">The corresponding `<address>` element has a `printable` attribute.</span></span>

<span data-ttu-id="c2ac7-178">Na przykład, `if` instrukcja daje w wyniku wartość true podczas przetwarzania następującego `<address>` elementu:</span><span class="sxs-lookup"><span data-stu-id="c2ac7-178">For example, the `if` statement evaluates to true when processing the following `<address>` element:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a><span data-ttu-id="c2ac7-179">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="c2ac7-179">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
