---
title: Tworzenie pomocników tagów w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak tworzyć pomocników tagów w ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: mvc/views/tag-helpers/authoring
ms.openlocfilehash: e0c9f450e4eded49694cbbb0e9fa2614a221ab14
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586829"
---
# <a name="author-tag-helpers-in-aspnet-core"></a><span data-ttu-id="cb89b-103">Tworzenie pomocników tagów w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cb89b-103">Author Tag Helpers in ASP.NET Core</span></span>

<span data-ttu-id="cb89b-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cb89b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cb89b-105">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cb89b-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="get-started-with-tag-helpers"></a><span data-ttu-id="cb89b-106">Wprowadzenie do pomocników tagów</span><span class="sxs-lookup"><span data-stu-id="cb89b-106">Get started with Tag Helpers</span></span>

<span data-ttu-id="cb89b-107">Ten samouczek zawiera wprowadzenie do programowania pomocników tagów.</span><span class="sxs-lookup"><span data-stu-id="cb89b-107">This tutorial provides an introduction to programming Tag Helpers.</span></span> <span data-ttu-id="cb89b-108">[Wprowadzenie do pomocników tagów](intro.md) opisuje zalety zapewniane przez pomocników tagów.</span><span class="sxs-lookup"><span data-stu-id="cb89b-108">[Introduction to Tag Helpers](intro.md) describes the benefits that Tag Helpers provide.</span></span>

<span data-ttu-id="cb89b-109">Pomocnik tagu jest klasą, która implementuje `ITagHelper` interfejs.</span><span class="sxs-lookup"><span data-stu-id="cb89b-109">A tag helper is any class that implements the `ITagHelper` interface.</span></span> <span data-ttu-id="cb89b-110">Jednak podczas tworzenia pomocnika tagów zwykle pochodzi od `TagHelper` , dlatego zapewnia dostęp do `Process` metody.</span><span class="sxs-lookup"><span data-stu-id="cb89b-110">However, when you author a tag helper, you generally derive from `TagHelper`, doing so gives you access to the `Process` method.</span></span>

1. <span data-ttu-id="cb89b-111">Utwórz nowy projekt ASP.NET Core o nazwie **AuthoringTagHelpers**.</span><span class="sxs-lookup"><span data-stu-id="cb89b-111">Create a new ASP.NET Core project called **AuthoringTagHelpers**.</span></span> <span data-ttu-id="cb89b-112">Nie będziesz potrzebować uwierzytelniania dla tego projektu.</span><span class="sxs-lookup"><span data-stu-id="cb89b-112">You won't need authentication for this project.</span></span>

1. <span data-ttu-id="cb89b-113">Utwórz folder do przechowywania pomocników tagów o nazwie *TagHelpers*.</span><span class="sxs-lookup"><span data-stu-id="cb89b-113">Create a folder to hold the Tag Helpers called *TagHelpers*.</span></span> <span data-ttu-id="cb89b-114">Folder *TagHelpers* *nie* jest wymagany, ale jest odpowiednią Konwencją.</span><span class="sxs-lookup"><span data-stu-id="cb89b-114">The *TagHelpers* folder is *not* required, but it's a reasonable convention.</span></span> <span data-ttu-id="cb89b-115">Teraz zacznij pisać kilka prostych pomocników tagów.</span><span class="sxs-lookup"><span data-stu-id="cb89b-115">Now let's get started writing some simple tag helpers.</span></span>

## <a name="a-minimal-tag-helper"></a><span data-ttu-id="cb89b-116">Minimalny pomocnik tagów</span><span class="sxs-lookup"><span data-stu-id="cb89b-116">A minimal Tag Helper</span></span>

<span data-ttu-id="cb89b-117">W tej sekcji napiszesz pomocnika tagu, który aktualizuje tag wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb89b-117">In this section, you write a tag helper that updates an email tag.</span></span> <span data-ttu-id="cb89b-118">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cb89b-118">For example:</span></span>

```html
<email>Support</email>
```

<span data-ttu-id="cb89b-119">Serwer użyje pomocnika tagu poczty e-mail do przekonwertowania tego znacznika na następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="cb89b-119">The server will use our email tag helper to convert that markup into the following:</span></span>

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

<span data-ttu-id="cb89b-120">Oznacza to, że tag zakotwiczony, który tworzy to łącze e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb89b-120">That is, an anchor tag that makes this an email link.</span></span> <span data-ttu-id="cb89b-121">Możesz to zrobić, jeśli piszesz aparat bloga i potrzebujesz wysłać wiadomość e-mail na potrzeby marketingu, pomocy technicznej i innych kontaktów, a wszystko to w tej samej domenie.</span><span class="sxs-lookup"><span data-stu-id="cb89b-121">You might want to do this if you are writing a blog engine and need it to send email for marketing, support, and other contacts, all to the same domain.</span></span>

1. <span data-ttu-id="cb89b-122">Dodaj następującą `EmailTagHelper` klasę do folderu *TagHelpers* .</span><span class="sxs-lookup"><span data-stu-id="cb89b-122">Add the following `EmailTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]

   * <span data-ttu-id="cb89b-123">Pomocnicy tagów używają konwencji nazewnictwa, która jest przeznaczona dla elementów nazwy klasy głównej (minus *TagHelper* część nazwy klasy).</span><span class="sxs-lookup"><span data-stu-id="cb89b-123">Tag helpers use a naming convention that targets elements of the root class name (minus the *TagHelper* portion of the class name).</span></span> <span data-ttu-id="cb89b-124">W tym przykładzie nazwą główną **EmailTagHelper** jest *adres e-mail*, więc `<email>` tag będzie przeznaczony.</span><span class="sxs-lookup"><span data-stu-id="cb89b-124">In this example, the root name of **EmailTagHelper** is *email*, so the `<email>` tag will be targeted.</span></span> <span data-ttu-id="cb89b-125">Ta konwencja nazewnictwa powinna współpracować z większością pomocników tagów, w dalszej części zaprezentowano sposób jego zastąpienia.</span><span class="sxs-lookup"><span data-stu-id="cb89b-125">This naming convention should work for most tag helpers, later on I'll show how to override it.</span></span>

   * <span data-ttu-id="cb89b-126">`EmailTagHelper`Klasa pochodzi od `TagHelper` .</span><span class="sxs-lookup"><span data-stu-id="cb89b-126">The `EmailTagHelper` class derives from `TagHelper`.</span></span> <span data-ttu-id="cb89b-127">`TagHelper`Klasa zawiera metody i właściwości do pisania pomocników tagów.</span><span class="sxs-lookup"><span data-stu-id="cb89b-127">The `TagHelper` class provides methods and properties for writing Tag Helpers.</span></span>

   * <span data-ttu-id="cb89b-128">Zastąpiona `Process` Metoda kontroluje, co pomocnik tagów wykonuje po wykonaniu.</span><span class="sxs-lookup"><span data-stu-id="cb89b-128">The overridden `Process` method controls what the tag helper does when executed.</span></span> <span data-ttu-id="cb89b-129">`TagHelper`Klasa udostępnia również asynchroniczną wersję ( `ProcessAsync` ) z tymi samymi parametrami.</span><span class="sxs-lookup"><span data-stu-id="cb89b-129">The `TagHelper` class also provides an asynchronous version (`ProcessAsync`) with the same parameters.</span></span>

   * <span data-ttu-id="cb89b-130">Parametr kontekstowy do `Process` (i `ProcessAsync` ) zawiera informacje skojarzone z wykonywaniem bieżącego tagu HTML.</span><span class="sxs-lookup"><span data-stu-id="cb89b-130">The context parameter to `Process` (and `ProcessAsync`) contains information associated with the execution of the current HTML tag.</span></span>

   * <span data-ttu-id="cb89b-131">Parametr danych wyjściowych do `Process` (i `ProcessAsync` ) zawiera stanowy element HTML reprezentatywny dla oryginalnego źródła używanego do generowania tagu HTML i zawartości.</span><span class="sxs-lookup"><span data-stu-id="cb89b-131">The output parameter to `Process` (and `ProcessAsync`) contains a stateful HTML element representative of the original source used to generate an HTML tag and content.</span></span>

   * <span data-ttu-id="cb89b-132">Nasza nazwa klasy ma sufiks **TagHelper**, co *nie* jest wymagane, ale jest uznawana za Konwencję najlepszych rozwiązań.</span><span class="sxs-lookup"><span data-stu-id="cb89b-132">Our class name has a suffix of **TagHelper**, which is *not* required, but it's considered a best practice convention.</span></span> <span data-ttu-id="cb89b-133">Można zadeklarować klasę jako:</span><span class="sxs-lookup"><span data-stu-id="cb89b-133">You could declare the class as:</span></span>

   ```csharp
   public class Email : TagHelper
   ```

1. <span data-ttu-id="cb89b-134">Aby udostępnić `EmailTagHelper` klasę wszystkim naszym Razor widokom, Dodaj `addTagHelper` dyrektywę do pliku *views/_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="cb89b-134">To make the `EmailTagHelper` class available to all our Razor views, add the `addTagHelper` directive to the *Views/_ViewImports.cshtml* file:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]

   <span data-ttu-id="cb89b-135">W powyższym kodzie użyto składni wieloznacznej do określenia wszystkich pomocników tagów w naszym zestawie.</span><span class="sxs-lookup"><span data-stu-id="cb89b-135">The code above uses the wildcard syntax to specify all the tag helpers in our assembly will be available.</span></span> <span data-ttu-id="cb89b-136">Pierwszy ciąg po `@addTagHelper` określa pomocnika tagów do załadowania (Użyj znaku "\*" dla wszystkich pomocników tagów), a drugi ciąg "AuthoringTagHelpers" określa zestaw, w którym znajduje się pomocnik tagów.</span><span class="sxs-lookup"><span data-stu-id="cb89b-136">The first string after `@addTagHelper` specifies the tag helper to load (Use "\*" for all tag helpers), and the second string "AuthoringTagHelpers" specifies the assembly the tag helper is in.</span></span> <span data-ttu-id="cb89b-137">Należy również pamiętać, że drugi wiersz zawiera ASP.NET Core pomocników tagów MVC przy użyciu składni wieloznacznej (Ci pomocnicy są omawiane w temacie [wprowadzenie do pomocników tagów](intro.md)). Jest to `@addTagHelper` dyrektywa, która sprawia, że pomocnik tagów jest dostępny dla Razor widoku.</span><span class="sxs-lookup"><span data-stu-id="cb89b-137">Also, note that the second line brings in the ASP.NET Core MVC tag helpers using the wildcard syntax (those helpers are discussed in [Introduction to Tag Helpers](intro.md).) It's the `@addTagHelper` directive that makes the tag helper available to the Razor view.</span></span> <span data-ttu-id="cb89b-138">Alternatywnie można podać w pełni kwalifikowaną nazwę (FQN) pomocnika tagów, jak pokazano poniżej:</span><span class="sxs-lookup"><span data-stu-id="cb89b-138">Alternatively, you can provide the fully qualified name (FQN) of a tag helper as shown below:</span></span>

```csharp
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<!--
the following snippet uses TagHelpers3 and should use TagHelpers (not the 3)
    [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
-->

<span data-ttu-id="cb89b-139">Aby dodać pomocnika tagów do widoku przy użyciu FQN, należy najpierw dodać FQN ( `AuthoringTagHelpers.TagHelpers.EmailTagHelper` ), a następnie **nazwę zestawu** (*AuthoringTagHelpers*, niekoniecznie `namespace` ).</span><span class="sxs-lookup"><span data-stu-id="cb89b-139">To add a tag helper to a view using a FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the **assembly name** (*AuthoringTagHelpers*, not necessarily the `namespace`).</span></span> <span data-ttu-id="cb89b-140">Większość deweloperów woli używać składni wieloznacznej.</span><span class="sxs-lookup"><span data-stu-id="cb89b-140">Most developers will prefer to use the wildcard syntax.</span></span> <span data-ttu-id="cb89b-141">[Wprowadzenie do](intro.md) pomocników tagów prowadzi do szczegółów dotyczących dodawania, usuwania, hierarchii i symboli wieloznacznych.</span><span class="sxs-lookup"><span data-stu-id="cb89b-141">[Introduction to Tag Helpers](intro.md) goes into detail on tag helper adding, removing, hierarchy, and wildcard syntax.</span></span>

1. <span data-ttu-id="cb89b-142">Zaktualizuj znaczniki w pliku *views/Home/Contact. cshtml* przy użyciu następujących zmian:</span><span class="sxs-lookup"><span data-stu-id="cb89b-142">Update the markup in the *Views/Home/Contact.cshtml* file with these changes:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. <span data-ttu-id="cb89b-143">Uruchom aplikację i użyj ulubionej przeglądarki, aby wyświetlić źródło HTML, aby sprawdzić, czy Tagi poczty e-mail zostały zamienione na znaczniki zakotwiczenia (na przykład `<a>Support</a>` ).</span><span class="sxs-lookup"><span data-stu-id="cb89b-143">Run the app and use your favorite browser to view the HTML source so you can verify that the email tags are replaced with anchor markup (For example, `<a>Support</a>`).</span></span> <span data-ttu-id="cb89b-144">*Obsługa* i *Marketing* są renderowane jako linki, ale nie mają `href` atrybutu, aby uczynić je funkcjonalnymi.</span><span class="sxs-lookup"><span data-stu-id="cb89b-144">*Support* and *Marketing* are rendered as a links, but they don't have an `href` attribute to make them functional.</span></span> <span data-ttu-id="cb89b-145">Naprawimy to w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="cb89b-145">We'll fix that in the next section.</span></span>

## <a name="setattribute-and-setcontent"></a><span data-ttu-id="cb89b-146">SetAttribute i SetContent</span><span class="sxs-lookup"><span data-stu-id="cb89b-146">SetAttribute and SetContent</span></span>

<span data-ttu-id="cb89b-147">W tej sekcji zaktualizujemy, `EmailTagHelper` Aby utworzyć prawidłowy tag kotwicy dla poczty e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb89b-147">In this section, we'll update the `EmailTagHelper` so that it will create a valid anchor tag for email.</span></span> <span data-ttu-id="cb89b-148">Zaktualizujemy go, aby pobierał informacje z Razor widoku (w formie `mail-to` atrybutu) i używać go podczas generowania zakotwiczenia.</span><span class="sxs-lookup"><span data-stu-id="cb89b-148">We'll update it to take information from a Razor view (in the form of a `mail-to` attribute) and use that in generating the anchor.</span></span>

<span data-ttu-id="cb89b-149">Zaktualizuj `EmailTagHelper` klasę, wykonując następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="cb89b-149">Update the `EmailTagHelper` class with the following:</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

* <span data-ttu-id="cb89b-150">Nazwy klas i właściwości w przypadku Pascalów dla pomocników tagów są tłumaczone na ich [Kebab przypadku](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span><span class="sxs-lookup"><span data-stu-id="cb89b-150">Pascal-cased class and property names for tag helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="cb89b-151">W związku z tym, aby użyć `MailTo` atrybutu, użyjesz `<email mail-to="value"/>` równoważnej.</span><span class="sxs-lookup"><span data-stu-id="cb89b-151">Therefore, to use the `MailTo` attribute, you'll use `<email mail-to="value"/>` equivalent.</span></span>

* <span data-ttu-id="cb89b-152">Ostatni wiersz ustawia kompletną zawartość dla naszego pomocnika tagów w minimalnym stopniu funkcjonalnym.</span><span class="sxs-lookup"><span data-stu-id="cb89b-152">The last line sets the completed content for our minimally functional tag helper.</span></span>

* <span data-ttu-id="cb89b-153">Wyróżniony wiersz pokazuje składnię dodawania atrybutów:</span><span class="sxs-lookup"><span data-stu-id="cb89b-153">The highlighted line shows the syntax for adding attributes:</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

<span data-ttu-id="cb89b-154">To podejście działa dla atrybutu "href", o ile nie istnieje obecnie w kolekcji atrybutów.</span><span class="sxs-lookup"><span data-stu-id="cb89b-154">That approach works for the attribute "href" as long as it doesn't currently exist in the attributes collection.</span></span> <span data-ttu-id="cb89b-155">Można również użyć metody, `output.Attributes.Add` Aby dodać atrybut pomocnika tagów na końcu kolekcji atrybutów tagu.</span><span class="sxs-lookup"><span data-stu-id="cb89b-155">You can also use the `output.Attributes.Add` method to add a tag helper attribute to the end of the collection of tag attributes.</span></span>

1. <span data-ttu-id="cb89b-156">Zaktualizuj znaczniki w pliku *views/Home/Contact. cshtml* przy użyciu następujących zmian:</span><span class="sxs-lookup"><span data-stu-id="cb89b-156">Update the markup in the *Views/Home/Contact.cshtml* file with these changes:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

1. <span data-ttu-id="cb89b-157">Uruchom aplikację i sprawdź, czy wygeneruje ona odpowiednie linki.</span><span class="sxs-lookup"><span data-stu-id="cb89b-157">Run the app and verify that it generates the correct links.</span></span>

<a name="self-closing"></a>

   > [!NOTE]
   > <span data-ttu-id="cb89b-158">Jeśli użytkownik zapisał tag poczty e-mail w sposób samozamykający ( `<email mail-to="Rick" />` ), ostateczne dane wyjściowe również będą zamykane automatycznie.</span><span class="sxs-lookup"><span data-stu-id="cb89b-158">If you were to write the email tag self-closing (`<email mail-to="Rick" />`), the final output would also be self-closing.</span></span> <span data-ttu-id="cb89b-159">Aby umożliwić zapisanie znacznika tylko za pomocą tagu początkowego ( `<email mail-to="Rick">` ), należy oznaczyć klasę następującymi:</span><span class="sxs-lookup"><span data-stu-id="cb89b-159">To enable the ability to write the tag with only a start tag (`<email mail-to="Rick">`) you must mark the class with the following:</span></span>
   >
   > [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]

   <span data-ttu-id="cb89b-160">W przypadku pomocnika tagów poczty e-mail z samym zamknięciem dane wyjściowe byłyby `<a href="mailto:Rick@contoso.com" />` .</span><span class="sxs-lookup"><span data-stu-id="cb89b-160">With a self-closing email tag helper, the output would be `<a href="mailto:Rick@contoso.com" />`.</span></span> <span data-ttu-id="cb89b-161">Tagi zakotwiczenia samozamykające nie są prawidłowym kodem HTML, więc nie chcesz go utworzyć, ale możesz chcieć utworzyć pomocnika tagu, który jest samozamykany.</span><span class="sxs-lookup"><span data-stu-id="cb89b-161">Self-closing anchor tags are not valid HTML, so you wouldn't want to create one, but you might want to create a tag helper that's self-closing.</span></span> <span data-ttu-id="cb89b-162">Pomocnicy tagów ustawiają typ `TagMode` właściwości po odczytaniu znacznika.</span><span class="sxs-lookup"><span data-stu-id="cb89b-162">Tag helpers set the type of the `TagMode` property after reading a tag.</span></span>

### <a name="processasync"></a><span data-ttu-id="cb89b-163">ProcessAsync</span><span class="sxs-lookup"><span data-stu-id="cb89b-163">ProcessAsync</span></span>

<span data-ttu-id="cb89b-164">W tej sekcji zapiszemy pomocnika asynchronicznej poczty e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb89b-164">In this section, we'll write an asynchronous email helper.</span></span>

1. <span data-ttu-id="cb89b-165">Zastąp `EmailTagHelper` klasę następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="cb89b-165">Replace the `EmailTagHelper` class with the following code:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

   <span data-ttu-id="cb89b-166">**Uwagi:**</span><span class="sxs-lookup"><span data-stu-id="cb89b-166">**Notes:**</span></span>

   * <span data-ttu-id="cb89b-167">Ta wersja używa metody asynchronicznej `ProcessAsync` .</span><span class="sxs-lookup"><span data-stu-id="cb89b-167">This version uses the asynchronous `ProcessAsync` method.</span></span> <span data-ttu-id="cb89b-168">Asynchroniczny `GetChildContentAsync` zwrot `Task` zawiera `TagHelperContent` .</span><span class="sxs-lookup"><span data-stu-id="cb89b-168">The asynchronous `GetChildContentAsync` returns a `Task` containing the `TagHelperContent`.</span></span>

   * <span data-ttu-id="cb89b-169">Użyj `output` parametru, aby pobrać zawartość elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="cb89b-169">Use the `output` parameter to get contents of the HTML element.</span></span>

1. <span data-ttu-id="cb89b-170">Wprowadź następujące zmiany w pliku *views/Home/Contact. cshtml* , aby pomocnik tagów mógł uzyskać docelowy adres e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb89b-170">Make the following change to the *Views/Home/Contact.cshtml* file so the tag helper can get the target email.</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. <span data-ttu-id="cb89b-171">Uruchom aplikację i sprawdź, czy generuje ona prawidłowe linki do poczty e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb89b-171">Run the app and verify that it generates valid email links.</span></span>

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a><span data-ttu-id="cb89b-172">Elementy SetHtmlContent i PostContent. SetHtmlContent</span><span class="sxs-lookup"><span data-stu-id="cb89b-172">RemoveAll, PreContent.SetHtmlContent and PostContent.SetHtmlContent</span></span>

1. <span data-ttu-id="cb89b-173">Dodaj następującą `BoldTagHelper` klasę do folderu *TagHelpers* .</span><span class="sxs-lookup"><span data-stu-id="cb89b-173">Add the following `BoldTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

   * <span data-ttu-id="cb89b-174">Ten `[HtmlTargetElement]` atrybut przekazuje parametr atrybutu, który określa, że każdy element HTML, który zawiera ATRYBUT HTML o nazwie "Bold", jest zgodny, a `Process` Metoda override w klasie zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="cb89b-174">The `[HtmlTargetElement]` attribute passes an attribute parameter that specifies that any HTML element that contains an HTML attribute named "bold" will match, and the `Process` override method in the class will run.</span></span> <span data-ttu-id="cb89b-175">W naszym przykładzie `Process` Metoda usuwa atrybut "Bold" i otacza znaczniki zawierające `<strong></strong>` .</span><span class="sxs-lookup"><span data-stu-id="cb89b-175">In our sample, the `Process` method removes the "bold" attribute and surrounds the containing markup with `<strong></strong>`.</span></span>

   * <span data-ttu-id="cb89b-176">Ponieważ nie chcesz zamieniać istniejącej zawartości tagu, należy napisać `<strong>` tag otwierając z `PreContent.SetHtmlContent` metodą i tagiem zamykającym `</strong>` przy użyciu `PostContent.SetHtmlContent` metody.</span><span class="sxs-lookup"><span data-stu-id="cb89b-176">Because you don't want to replace the existing tag content, you must write the opening `<strong>` tag with the `PreContent.SetHtmlContent` method and the closing `</strong>` tag with the `PostContent.SetHtmlContent` method.</span></span>

1. <span data-ttu-id="cb89b-177">Zmodyfikuj widok *about. cshtml* , aby zawierał `bold` wartość atrybutu.</span><span class="sxs-lookup"><span data-stu-id="cb89b-177">Modify the *About.cshtml* view to contain a `bold` attribute value.</span></span> <span data-ttu-id="cb89b-178">Ukończony kod jest przedstawiony poniżej.</span><span class="sxs-lookup"><span data-stu-id="cb89b-178">The completed code is shown below.</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

1. <span data-ttu-id="cb89b-179">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="cb89b-179">Run the app.</span></span> <span data-ttu-id="cb89b-180">Możesz użyć ulubionej przeglądarki, aby sprawdzić źródło i sprawdzić adiustację.</span><span class="sxs-lookup"><span data-stu-id="cb89b-180">You can use your favorite browser to inspect the source and verify the markup.</span></span>

   <span data-ttu-id="cb89b-181">`[HtmlTargetElement]`Atrybut powyżej dotyczy tylko znacznika HTML, który zawiera nazwę atrybutu "Bold".</span><span class="sxs-lookup"><span data-stu-id="cb89b-181">The `[HtmlTargetElement]` attribute above only targets HTML markup that provides an attribute name of "bold".</span></span> <span data-ttu-id="cb89b-182">`<bold>`Element nie został zmodyfikowany przez pomocnika tagów.</span><span class="sxs-lookup"><span data-stu-id="cb89b-182">The `<bold>` element wasn't modified by the tag helper.</span></span>

1. <span data-ttu-id="cb89b-183">Dodaj komentarz do `[HtmlTargetElement]` wiersza atrybutu, który zostanie domyślnie przypisany do `<bold>` tagów, czyli znaczników HTML w formularzu `<bold>` .</span><span class="sxs-lookup"><span data-stu-id="cb89b-183">Comment out the `[HtmlTargetElement]` attribute line and it will default to targeting `<bold>` tags, that is, HTML markup of the form `<bold>`.</span></span> <span data-ttu-id="cb89b-184">Należy pamiętać, że domyślna konwencja nazewnictwa będzie zgodna z nazwą klasy **pogrubioną** TagHelper do `<bold>` tagów.</span><span class="sxs-lookup"><span data-stu-id="cb89b-184">Remember, the default naming convention will match the class name **Bold** TagHelper to `<bold>` tags.</span></span>

1. <span data-ttu-id="cb89b-185">Uruchom aplikację i sprawdź, czy `<bold>` tag jest przetwarzany przez pomocnika tagów.</span><span class="sxs-lookup"><span data-stu-id="cb89b-185">Run the app and verify that the `<bold>` tag is processed by the tag helper.</span></span>

<span data-ttu-id="cb89b-186">Dekorowania nazwy klasę z wieloma `[HtmlTargetElement]` atrybutami powoduje logiczne lub docelowe elementy.</span><span class="sxs-lookup"><span data-stu-id="cb89b-186">Decorating a class with multiple `[HtmlTargetElement]` attributes results in a logical-OR of the targets.</span></span> <span data-ttu-id="cb89b-187">Na przykład przy użyciu poniższego kodu, tag pogrubiony lub pogrubiony atrybut będzie pasować.</span><span class="sxs-lookup"><span data-stu-id="cb89b-187">For example, using the code below, a bold tag or a bold attribute will match.</span></span>

[!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

<span data-ttu-id="cb89b-188">Gdy wiele atrybutów jest dodawanych do tej samej instrukcji, środowisko uruchomieniowe traktuje je jako logiczne-i.</span><span class="sxs-lookup"><span data-stu-id="cb89b-188">When multiple attributes are added to the same statement, the runtime treats them as a logical-AND.</span></span> <span data-ttu-id="cb89b-189">Na przykład w poniższym kodzie element HTML musi mieć nazwę "Bold" z atrybutem o nazwie "Bold" ( `<bold bold />` ), aby dopasować.</span><span class="sxs-lookup"><span data-stu-id="cb89b-189">For example, in the code below, an HTML element must be named "bold" with an attribute named "bold" (`<bold bold />`) to match.</span></span>

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

<span data-ttu-id="cb89b-190">Można również użyć, `[HtmlTargetElement]` Aby zmienić nazwę elementu wskazywanego.</span><span class="sxs-lookup"><span data-stu-id="cb89b-190">You can also use the `[HtmlTargetElement]` to change the name of the targeted element.</span></span> <span data-ttu-id="cb89b-191">Na przykład, jeśli chcesz `BoldTagHelper` , aby Tagi były docelowe `<MyBold>` , użyj następującego atrybutu:</span><span class="sxs-lookup"><span data-stu-id="cb89b-191">For example if you wanted the `BoldTagHelper` to target `<MyBold>` tags, you would use the following attribute:</span></span>

```csharp
[HtmlTargetElement("MyBold")]
```

## <a name="pass-a-model-to-a-tag-helper"></a><span data-ttu-id="cb89b-192">Przekaż model do pomocnika tagów</span><span class="sxs-lookup"><span data-stu-id="cb89b-192">Pass a model to a Tag Helper</span></span>

1. <span data-ttu-id="cb89b-193">Dodawanie folderu *models* .</span><span class="sxs-lookup"><span data-stu-id="cb89b-193">Add a *Models* folder.</span></span>

1. <span data-ttu-id="cb89b-194">Dodaj następującą `WebsiteContext` klasę do folderu *models* :</span><span class="sxs-lookup"><span data-stu-id="cb89b-194">Add the following `WebsiteContext` class to the *Models* folder:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

1. <span data-ttu-id="cb89b-195">Dodaj następującą `WebsiteInformationTagHelper` klasę do folderu *TagHelpers* .</span><span class="sxs-lookup"><span data-stu-id="cb89b-195">Add the following `WebsiteInformationTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]

   * <span data-ttu-id="cb89b-196">Jak wspomniano wcześniej, pomocników tagów tłumaczy nazwy klas i właściwości w języku C# z uwzględnieniem wielkości liter dla pomocników tagów w [przypadku Kebab](https://wiki.c2.com/?KebabCase).</span><span class="sxs-lookup"><span data-stu-id="cb89b-196">As mentioned previously, tag helpers translates Pascal-cased C# class names and properties for tag helpers into [kebab case](https://wiki.c2.com/?KebabCase).</span></span> <span data-ttu-id="cb89b-197">W związku z tym, aby użyć `WebsiteInformationTagHelper` w programie Razor , należy napisać `<website-information />` .</span><span class="sxs-lookup"><span data-stu-id="cb89b-197">Therefore, to use the `WebsiteInformationTagHelper` in Razor, you'll write `<website-information />`.</span></span>

   * <span data-ttu-id="cb89b-198">Nie można jednoznacznie zidentyfikować elementu docelowego z `[HtmlTargetElement]` atrybutem, więc wartość domyślna to `website-information` .</span><span class="sxs-lookup"><span data-stu-id="cb89b-198">You are not explicitly identifying the target element with the `[HtmlTargetElement]` attribute, so the default of `website-information` will be targeted.</span></span> <span data-ttu-id="cb89b-199">Jeśli zastosowano następujący atrybut (należy zauważyć, że nie jest on Kebab, ale dopasowuje nazwę klasy):</span><span class="sxs-lookup"><span data-stu-id="cb89b-199">If you applied the following attribute (note it's not kebab case but matches the class name):</span></span>

   ```csharp
   [HtmlTargetElement("WebsiteInformation")]
   ```

   <span data-ttu-id="cb89b-200">Tag przypadku Kebab nie jest `<website-information />` zgodny.</span><span class="sxs-lookup"><span data-stu-id="cb89b-200">The kebab case tag `<website-information />` wouldn't match.</span></span> <span data-ttu-id="cb89b-201">Jeśli chcesz użyć `[HtmlTargetElement]` atrybutu, użyj przypadku Kebab, jak pokazano poniżej:</span><span class="sxs-lookup"><span data-stu-id="cb89b-201">If you want use the `[HtmlTargetElement]` attribute, you would use kebab case as shown below:</span></span>

   ```csharp
   [HtmlTargetElement("Website-Information")]
   ```

   * <span data-ttu-id="cb89b-202">Elementy, które są przez siebie zamykane, nie mają zawartości.</span><span class="sxs-lookup"><span data-stu-id="cb89b-202">Elements that are self-closing have no content.</span></span> <span data-ttu-id="cb89b-203">Na potrzeby tego przykładu znaczniki Razor będą używały tagów samozamykających, ale pomocnik tagów będzie tworzyć element [sekcji](https://www.w3.org/TR/html5/sections.html#the-section-element) (który nie jest zamykany i zapisujesz zawartość wewnątrz `section` elementu).</span><span class="sxs-lookup"><span data-stu-id="cb89b-203">For this example, the Razor markup will use a self-closing tag, but the tag helper will be creating a [section](https://www.w3.org/TR/html5/sections.html#the-section-element) element (which isn't self-closing and you are writing content inside the `section` element).</span></span> <span data-ttu-id="cb89b-204">W związku z tym należy ustawić, aby `TagMode` `StartTagAndEndTag` zapisywać dane wyjściowe.</span><span class="sxs-lookup"><span data-stu-id="cb89b-204">Therefore, you need to set `TagMode` to `StartTagAndEndTag` to write output.</span></span> <span data-ttu-id="cb89b-205">Alternatywnie możesz dodać komentarz do ustawienia wiersza `TagMode` i napisać znacznik z tagiem zamykającym.</span><span class="sxs-lookup"><span data-stu-id="cb89b-205">Alternatively, you can comment out the line setting `TagMode` and write markup with a closing tag.</span></span> <span data-ttu-id="cb89b-206">(Przykładowe znaczniki są podane w dalszej części tego samouczka).</span><span class="sxs-lookup"><span data-stu-id="cb89b-206">(Example markup is provided later in this tutorial.)</span></span>

   * <span data-ttu-id="cb89b-207">`$`(Znak dolara) w następującym wierszu używa [interpolowanego ciągu](/dotnet/csharp/language-reference/keywords/interpolated-strings):</span><span class="sxs-lookup"><span data-stu-id="cb89b-207">The `$` (dollar sign) in the following line uses an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings):</span></span>

   ```cshtml
   $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
   ```

1. <span data-ttu-id="cb89b-208">Dodaj następujący znacznik do widoku *Informacje o. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="cb89b-208">Add the following markup to the *About.cshtml* view.</span></span> <span data-ttu-id="cb89b-209">Wyróżnione znaczniki wyświetlają informacje o witrynie sieci Web.</span><span class="sxs-lookup"><span data-stu-id="cb89b-209">The highlighted markup displays the web site information.</span></span>

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,4-8, 18-999)]

   > [!NOTE]
   > <span data-ttu-id="cb89b-210">W Razor poniższym znaczniku:</span><span class="sxs-lookup"><span data-stu-id="cb89b-210">In the Razor markup shown below:</span></span>
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=18-18)]
   >
   > <span data-ttu-id="cb89b-211">Razor wie, że `info` atrybut jest klasą, a nie ciągiem, i chcesz napisać kod w języku C#.</span><span class="sxs-lookup"><span data-stu-id="cb89b-211">Razor knows the `info` attribute is a class, not a string, and you want to write C# code.</span></span> <span data-ttu-id="cb89b-212">Dowolny atrybut pomocnika tagów niebędących ciągami powinien być zapisany bez `@` znaku.</span><span class="sxs-lookup"><span data-stu-id="cb89b-212">Any non-string tag helper attribute should be written without the `@` character.</span></span>

1. <span data-ttu-id="cb89b-213">Uruchom aplikację i przejdź do widoku informacje, aby wyświetlić informacje o witrynie sieci Web.</span><span class="sxs-lookup"><span data-stu-id="cb89b-213">Run the app, and navigate to the About view to see the web site information.</span></span>

   > [!NOTE]
   > <span data-ttu-id="cb89b-214">Można użyć następującego znacznika z tagiem zamykającym i usunąć wiersz za pomocą `TagMode.StartTagAndEndTag` pomocnika tagów:</span><span class="sxs-lookup"><span data-stu-id="cb89b-214">You can use the following markup with a closing tag and remove the line with `TagMode.StartTagAndEndTag` in the tag helper:</span></span>
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=20-21)]

## <a name="condition-tag-helper"></a><span data-ttu-id="cb89b-215">Pomocnik tagów warunku</span><span class="sxs-lookup"><span data-stu-id="cb89b-215">Condition Tag Helper</span></span>

<span data-ttu-id="cb89b-216">Pomocnik tagów warunku renderuje dane wyjściowe, gdy przeszedł wartość true.</span><span class="sxs-lookup"><span data-stu-id="cb89b-216">The condition tag helper renders output when passed a true value.</span></span>

1. <span data-ttu-id="cb89b-217">Dodaj następującą `ConditionTagHelper` klasę do folderu *TagHelpers* .</span><span class="sxs-lookup"><span data-stu-id="cb89b-217">Add the following `ConditionTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

1. <span data-ttu-id="cb89b-218">Zastąp zawartość pliku *viewss/Home/index. cshtml* następującym znacznikiem:</span><span class="sxs-lookup"><span data-stu-id="cb89b-218">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Index.cshtml)]

1. <span data-ttu-id="cb89b-219">Zastąp `Index` metodę w `Home` kontrolerze następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="cb89b-219">Replace the `Index` method in the `Home` controller with the following code:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

1. <span data-ttu-id="cb89b-220">Uruchom aplikację i przejdź do strony głównej.</span><span class="sxs-lookup"><span data-stu-id="cb89b-220">Run the app and browse to the home page.</span></span> <span data-ttu-id="cb89b-221">Adiustacja w warunku `div` nie będzie renderowana.</span><span class="sxs-lookup"><span data-stu-id="cb89b-221">The markup in the conditional `div` won't be rendered.</span></span> <span data-ttu-id="cb89b-222">Dołącz ciąg zapytania `?approved=true` do adresu URL (na przykład `http://localhost:1235/Home/Index?approved=true` ).</span><span class="sxs-lookup"><span data-stu-id="cb89b-222">Append the query string `?approved=true` to the URL (for example, `http://localhost:1235/Home/Index?approved=true`).</span></span> <span data-ttu-id="cb89b-223">`approved` jest ustawiona na wartość true i zostanie wyświetlona Adiustacja warunkowa.</span><span class="sxs-lookup"><span data-stu-id="cb89b-223">`approved` is set to true and the conditional markup will be displayed.</span></span>

> [!NOTE]
> <span data-ttu-id="cb89b-224">Użyj operatora [nameof](/dotnet/csharp/language-reference/keywords/nameof) , aby określić atrybut docelowy zamiast określać ciąg, jak za pomocą pomocnika tagów pogrubionych:</span><span class="sxs-lookup"><span data-stu-id="cb89b-224">Use the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator to specify the attribute to target rather than specifying a string as you did with the bold tag helper:</span></span>
>
> [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
> <span data-ttu-id="cb89b-225">Operator [nameof](/dotnet/csharp/language-reference/keywords/nameof) będzie chronić kod w przypadku jego ponownego zamiaru (możemy zmienić nazwę na `RedCondition` ).</span><span class="sxs-lookup"><span data-stu-id="cb89b-225">The [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator will protect the code should it ever be refactored (we might want to change the name to `RedCondition`).</span></span>

### <a name="avoid-tag-helper-conflicts"></a><span data-ttu-id="cb89b-226">Unikaj konfliktów pomocników tagów</span><span class="sxs-lookup"><span data-stu-id="cb89b-226">Avoid Tag Helper conflicts</span></span>

<span data-ttu-id="cb89b-227">W tej sekcji napiszesz parę pomocników tagów AutoLink.</span><span class="sxs-lookup"><span data-stu-id="cb89b-227">In this section, you write a pair of auto-linking tag helpers.</span></span> <span data-ttu-id="cb89b-228">Pierwszy zastąpi znaczniki zawierające adres URL zaczynający się od protokołu HTTP do tagu kotwicy HTML zawierającego ten sam adres URL (i w związku z tym spowoduje nakazanie linku do adresu URL).</span><span class="sxs-lookup"><span data-stu-id="cb89b-228">The first will replace markup containing a URL starting with HTTP to an HTML anchor tag containing the same URL (and thus yielding a link to the URL).</span></span> <span data-ttu-id="cb89b-229">Druga będzie taka sama dla adresu URL rozpoczynającego się od strony WWW.</span><span class="sxs-lookup"><span data-stu-id="cb89b-229">The second will do the same for a URL starting with WWW.</span></span>

<span data-ttu-id="cb89b-230">Ponieważ te dwa pomocnicy są ściśle powiązane i w przyszłości mogą się z nimi odnowić, zostaną zachowane w tym samym pliku.</span><span class="sxs-lookup"><span data-stu-id="cb89b-230">Because these two helpers are closely related and you may refactor them in the future, we'll keep them in the same file.</span></span>

1. <span data-ttu-id="cb89b-231">Dodaj następującą `AutoLinkerHttpTagHelper` klasę do folderu *TagHelpers* .</span><span class="sxs-lookup"><span data-stu-id="cb89b-231">Add the following `AutoLinkerHttpTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

   >[!NOTE]
   ><span data-ttu-id="cb89b-232">`AutoLinkerHttpTagHelper`Klasa wskazuje `p` elementy i używa [wyrażenia regularnego](/dotnet/standard/base-types/regular-expression-language-quick-reference) do utworzenia zakotwiczenia.</span><span class="sxs-lookup"><span data-stu-id="cb89b-232">The `AutoLinkerHttpTagHelper` class targets `p` elements and uses [Regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) to create the anchor.</span></span>

1. <span data-ttu-id="cb89b-233">Dodaj następujący znacznik na końcu pliku *views/Home/Contact. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="cb89b-233">Add the following markup to the end of the *Views/Home/Contact.cshtml* file:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

1. <span data-ttu-id="cb89b-234">Uruchom aplikację i sprawdź, czy pomocnik tagów prawidłowo renderuje kotwicę.</span><span class="sxs-lookup"><span data-stu-id="cb89b-234">Run the app and verify that the tag helper renders the anchor correctly.</span></span>

1. <span data-ttu-id="cb89b-235">Zaktualizuj `AutoLinker` klasę, aby uwzględnić, `AutoLinkerWwwTagHelper` która spowoduje przekonwertowanie tekstu www na tag kotwicy, który zawiera także oryginalny tekst www.</span><span class="sxs-lookup"><span data-stu-id="cb89b-235">Update the `AutoLinker` class to include the `AutoLinkerWwwTagHelper` which will convert www text to an anchor tag that also contains the original www text.</span></span> <span data-ttu-id="cb89b-236">Zaktualizowany kod jest wyróżniony poniżej:</span><span class="sxs-lookup"><span data-stu-id="cb89b-236">The updated code is highlighted below:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

1. <span data-ttu-id="cb89b-237">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="cb89b-237">Run the app.</span></span> <span data-ttu-id="cb89b-238">Zauważ, że tekst www jest renderowany jako link, ale tekst HTTP nie jest.</span><span class="sxs-lookup"><span data-stu-id="cb89b-238">Notice the www text is rendered as a link but the HTTP text isn't.</span></span> <span data-ttu-id="cb89b-239">Jeśli umieścisz punkt przerwania w obu klasach, zobaczysz, że Klasa pomocnika tagów HTTP jest uruchamiana jako pierwsza.</span><span class="sxs-lookup"><span data-stu-id="cb89b-239">If you put a break point in both classes, you can see that the HTTP tag helper class runs first.</span></span> <span data-ttu-id="cb89b-240">Problem polega na tym, że dane wyjściowe pomocnika tagów są buforowane, a kiedy pomocnik tagów WWW jest uruchomiony, zastępuje zbuforowane dane wyjściowe z pomocnika tagów HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb89b-240">The problem is that the tag helper output is cached, and when the WWW tag helper is run, it overwrites the cached output from the HTTP tag helper.</span></span> <span data-ttu-id="cb89b-241">W dalszej części tego samouczka zobaczymy, jak sterować kolejnością, w której są uruchamiane Tagi pomocników.</span><span class="sxs-lookup"><span data-stu-id="cb89b-241">Later in the tutorial we'll see how to control the order that tag helpers run in.</span></span> <span data-ttu-id="cb89b-242">Naprawimy kod w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="cb89b-242">We'll fix the code with the following:</span></span>

   [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

   > [!NOTE]
   > <span data-ttu-id="cb89b-243">W pierwszej wersji pomocników tagów autolinku otrzymasz zawartość elementu docelowego z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="cb89b-243">In the first edition of the auto-linking tag helpers, you got the content of the target with the following code:</span></span>
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
   >
   > <span data-ttu-id="cb89b-244">Oznacza to, że wywoływanie `GetChildContentAsync` przy użyciu `TagHelperOutput` metody zakończono `ProcessAsync` .</span><span class="sxs-lookup"><span data-stu-id="cb89b-244">That is, you call `GetChildContentAsync` using the `TagHelperOutput` passed into the `ProcessAsync` method.</span></span> <span data-ttu-id="cb89b-245">Jak wspomniano wcześniej, ponieważ dane wyjściowe są buforowane, ostatni pomocnik tagu do uruchomienia usługi WINS.</span><span class="sxs-lookup"><span data-stu-id="cb89b-245">As mentioned previously, because the output is cached, the last tag helper to run wins.</span></span> <span data-ttu-id="cb89b-246">Rozwiązano problem z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="cb89b-246">You fixed that problem with the following code:</span></span>
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
   >
   > <span data-ttu-id="cb89b-247">Powyższy kod sprawdza, czy zawartość została zmodyfikowana, a jeśli ma, pobiera zawartość z bufora wyjściowego.</span><span class="sxs-lookup"><span data-stu-id="cb89b-247">The code above checks to see if the content has been modified, and if it has, it gets the content from the output buffer.</span></span>

1. <span data-ttu-id="cb89b-248">Uruchom aplikację i sprawdź, czy dwa linki działają zgodnie z oczekiwaniami.</span><span class="sxs-lookup"><span data-stu-id="cb89b-248">Run the app and verify that the two links work as expected.</span></span> <span data-ttu-id="cb89b-249">Mimo że pomocnika tagów autokonsolidatora jest poprawna i kompletna, ma delikatny problem.</span><span class="sxs-lookup"><span data-stu-id="cb89b-249">While it might appear our auto linker tag helper is correct and complete, it has a subtle problem.</span></span> <span data-ttu-id="cb89b-250">Jeśli pomocnik tagów WWW zostanie uruchomiony w pierwszej kolejności, linki www nie będą poprawne.</span><span class="sxs-lookup"><span data-stu-id="cb89b-250">If the WWW tag helper runs first, the www links won't be correct.</span></span> <span data-ttu-id="cb89b-251">Zaktualizuj kod, dodając `Order` Przeciążenie, aby kontrolować kolejność, w której jest uruchamiany tag.</span><span class="sxs-lookup"><span data-stu-id="cb89b-251">Update the code by adding the `Order` overload to control the order that the tag runs in.</span></span> <span data-ttu-id="cb89b-252">`Order`Właściwość określa kolejność wykonywania względem innych pomocników tagów ukierunkowanych na ten sam element.</span><span class="sxs-lookup"><span data-stu-id="cb89b-252">The `Order` property determines the execution order relative to other tag helpers targeting the same element.</span></span> <span data-ttu-id="cb89b-253">Domyślna wartość kolejności wynosi zero, a wystąpienia o niższych wartościach są wykonywane jako pierwsze.</span><span class="sxs-lookup"><span data-stu-id="cb89b-253">The default order value is zero and instances with lower values are executed first.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]

   <span data-ttu-id="cb89b-254">Poprzedni kod gwarantuje, że pomocnik tagów HTTP jest uruchamiany przed pomocnikiem tagów WWW.</span><span class="sxs-lookup"><span data-stu-id="cb89b-254">The preceding code guarantees that the HTTP tag helper runs before the WWW tag helper.</span></span> <span data-ttu-id="cb89b-255">Zmień `Order` na `MaxValue` i sprawdź, czy znaczniki wygenerowane dla tagu www są nieprawidłowe.</span><span class="sxs-lookup"><span data-stu-id="cb89b-255">Change `Order` to `MaxValue` and verify that the markup generated for the WWW tag is incorrect.</span></span>

## <a name="inspect-and-retrieve-child-content"></a><span data-ttu-id="cb89b-256">Inspekcja i pobieranie zawartości podrzędnej</span><span class="sxs-lookup"><span data-stu-id="cb89b-256">Inspect and retrieve child content</span></span>

<span data-ttu-id="cb89b-257">Pomocnicy tagów udostępniają kilka właściwości do pobierania zawartości.</span><span class="sxs-lookup"><span data-stu-id="cb89b-257">The tag helpers provide several properties to retrieve content.</span></span>

* <span data-ttu-id="cb89b-258">Wynik `GetChildContentAsync` może być dołączany do `output.Content` .</span><span class="sxs-lookup"><span data-stu-id="cb89b-258">The result of `GetChildContentAsync` can be appended to `output.Content`.</span></span>
* <span data-ttu-id="cb89b-259">Wyniki z programu można sprawdzić `GetChildContentAsync` `GetContent` .</span><span class="sxs-lookup"><span data-stu-id="cb89b-259">You can inspect the result of `GetChildContentAsync` with `GetContent`.</span></span>
* <span data-ttu-id="cb89b-260">Jeśli zmodyfikujesz `output.Content` , treść TagHelper nie zostanie wykonana ani renderowana, chyba że wywołasz metodę `GetChildContentAsync` , jak w przypadku przykładu autokonsolidatora:</span><span class="sxs-lookup"><span data-stu-id="cb89b-260">If you modify `output.Content`, the TagHelper body won't be executed or rendered unless you call `GetChildContentAsync` as in our auto-linker sample:</span></span>

[!code-csharp[](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

* <span data-ttu-id="cb89b-261">Wiele wywołań `GetChildContentAsync` zwraca tę samą wartość i nie ponownie wykonuje `TagHelper` treści, chyba że przekażesz fałszywy parametr wskazujący, że nie należy używać buforowanego wyniku.</span><span class="sxs-lookup"><span data-stu-id="cb89b-261">Multiple calls to `GetChildContentAsync` returns the same value and doesn't re-execute the `TagHelper` body unless you pass in a false parameter indicating not to use the cached result.</span></span>

## <a name="load-minified-partial-view-taghelper"></a><span data-ttu-id="cb89b-262">Załaduj widok częściowy zminimalizowanego TagHelper</span><span class="sxs-lookup"><span data-stu-id="cb89b-262">Load minified partial view TagHelper</span></span>

<span data-ttu-id="cb89b-263">W środowiskach produkcyjnych można ulepszyć wydajność, ładując częściowe widoki zminimalizowanego.</span><span class="sxs-lookup"><span data-stu-id="cb89b-263">In production environments, performance can be improved by loading minified partial views.</span></span> <span data-ttu-id="cb89b-264">Aby skorzystać z zminimalizowanego widoku częściowego w środowisku produkcyjnym:</span><span class="sxs-lookup"><span data-stu-id="cb89b-264">To take advantage of minified partial view in production:</span></span>

* <span data-ttu-id="cb89b-265">Utwórz/Skonfiguruj proces przed kompilacją, który minifies częściowe widoki.</span><span class="sxs-lookup"><span data-stu-id="cb89b-265">Create/set up a pre-build process that minifies partial views.</span></span>
* <span data-ttu-id="cb89b-266">Użyj poniższego kodu, aby załadować zminimalizowanego częściowe widoki w środowiskach innych niż programistyczne.</span><span class="sxs-lookup"><span data-stu-id="cb89b-266">Use the following code to load  minified partial views in non-development environments.</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/MinifiedVersionTagHelper.cs?name=snippet)]
