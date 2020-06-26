---
title: Wprowadzenie do ASP.NET CoreBlazor
author: guardrex
description: Zacznij korzystać z programu Blazor , tworząc Blazor aplikację przy użyciu wybranego przez siebie narzędzia.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 1eabc35175d1b696de99488981b1382d231f5544
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402783"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="06a29-103">Wprowadzenie do ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="06a29-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="06a29-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="06a29-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="06a29-105">Aby rozpocząć pracę z programem Blazor , postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:</span><span class="sxs-lookup"><span data-stu-id="06a29-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06a29-106">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06a29-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="06a29-107">Zainstaluj najnowszą wersję programu [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) z **ASP.NET i programowaniem aplikacji sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="06a29-107">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="06a29-108">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="06a29-108">Create a new project.</span></span>

1. <span data-ttu-id="06a29-109">Wybierz pozycję \*\* Blazor aplikacja\*\*.</span><span class="sxs-lookup"><span data-stu-id="06a29-109">Select **Blazor App**.</span></span> <span data-ttu-id="06a29-110">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="06a29-110">Select **Next**.</span></span>

1. <span data-ttu-id="06a29-111">Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj nazwę domyślną projektu.</span><span class="sxs-lookup"><span data-stu-id="06a29-111">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="06a29-112">Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="06a29-112">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="06a29-113">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="06a29-113">Select **Create**.</span></span>

1. <span data-ttu-id="06a29-114">Aby zapoznać się z Blazor WebAssembly doświadczeniem, wybierz szablon \*\* Blazor WebAssembly aplikacji\*\* .</span><span class="sxs-lookup"><span data-stu-id="06a29-114">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="06a29-115">Aby zapoznać się z Blazor Server doświadczeniem, wybierz szablon \*\* Blazor Server aplikacji\*\* .</span><span class="sxs-lookup"><span data-stu-id="06a29-115">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="06a29-116">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="06a29-116">Select **Create**.</span></span>

   <span data-ttu-id="06a29-117">Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* i *Blazor Server* , zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="06a29-117">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="06a29-118">Naciśnij klawisz <kbd>Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="06a29-118">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="06a29-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="06a29-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="06a29-120">Zainstaluj najnowszą wersję [zestawu SDK programu .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="06a29-120">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="06a29-121">Jeśli wcześniej zainstalowano zestaw SDK, możesz określić zainstalowaną wersję, wykonując następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="06a29-121">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="06a29-122">Zainstaluj najnowszą wersję programu [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="06a29-122">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="06a29-123">Zainstaluj najnowszą wersję [języka C# dla rozszerzenia Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) oraz rozszerzenie [JavaScript Debugger (nocne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) z `debug.javascript.usePreview` ustawionym na `true` .</span><span class="sxs-lookup"><span data-stu-id="06a29-123">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

  <span data-ttu-id="06a29-124">Aby ustawić `debug.javascript.usePreview` `true` przy użyciu interfejsu użytkownika vs Code, Otwórz **File**  >  **Preferences**  >  **Ustawienia** preferencji plików i Wyszukaj `debug javascript use preview` .</span><span class="sxs-lookup"><span data-stu-id="06a29-124">To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`.</span></span> <span data-ttu-id="06a29-125">Zaznacz pole wyboru **Użyj nowego debugera JavaScript w wersji zapoznawczej dla Node.js i programu Chrome**.</span><span class="sxs-lookup"><span data-stu-id="06a29-125">Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.</span></span>

1. <span data-ttu-id="06a29-126">Aby uzyskać Blazor WebAssembly środowisko, wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="06a29-126">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="06a29-127">Aby uzyskać Blazor Server środowisko, wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="06a29-127">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="06a29-128">Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* i *Blazor Server* , zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="06a29-128">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="06a29-129">Otwórz `WebApplication1` folder w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="06a29-129">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="06a29-130">Żądania IDE służące do dodawania zasobów do kompilowania i debugowania projektu.</span><span class="sxs-lookup"><span data-stu-id="06a29-130">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="06a29-131">Wybierz pozycję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="06a29-131">Select **Yes**.</span></span>

1. <span data-ttu-id="06a29-132">Naciśnij klawisz <kbd>Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="06a29-132">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="06a29-133">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="06a29-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="06a29-134">Zainstaluj [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="06a29-134">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="06a29-135">Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.</span><span class="sxs-lookup"><span data-stu-id="06a29-135">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="06a29-136">Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="06a29-136">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="06a29-137">Aby zapoznać się z Blazor WebAssembly doświadczeniem, wybierz szablon \*\* Blazor WebAssembly aplikacji\*\* .</span><span class="sxs-lookup"><span data-stu-id="06a29-137">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="06a29-138">Aby zapoznać się z Blazor Server doświadczeniem, wybierz szablon \*\* Blazor Server aplikacji\*\* .</span><span class="sxs-lookup"><span data-stu-id="06a29-138">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="06a29-139">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="06a29-139">Select **Next**.</span></span>

   <span data-ttu-id="06a29-140">Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* i *Blazor Server* , zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="06a29-140">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="06a29-141">Potwierdź następujące konfiguracje:</span><span class="sxs-lookup"><span data-stu-id="06a29-141">Confirm the following configurations:</span></span>

   * <span data-ttu-id="06a29-142">**Platforma docelowa** jest ustawiona na **platformę .NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="06a29-142">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="06a29-143">**Uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="06a29-143">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="06a29-144">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="06a29-144">Select **Next**.</span></span>

1. <span data-ttu-id="06a29-145">W polu **Nazwa projektu** Nadaj nazwę aplikacji `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="06a29-145">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="06a29-146">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="06a29-146">Select **Create**.</span></span>

1. <span data-ttu-id="06a29-147">Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację *bez debugera*.</span><span class="sxs-lookup"><span data-stu-id="06a29-147">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="06a29-148">Uruchom **aplikację z**  >  przyciskiem Uruchom**debugowanie** lub Uruchom (&#9654;), aby uruchomić aplikację *z debugerem*.</span><span class="sxs-lookup"><span data-stu-id="06a29-148">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="06a29-149">Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="06a29-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="06a29-150">Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="06a29-150">The user and keychain passwords are required to trust the certificate.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="06a29-151">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="06a29-151">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="06a29-152">Zainstaluj najnowszą wersję [zestawu SDK programu .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="06a29-152">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="06a29-153">Jeśli wcześniej zainstalowano zestaw SDK, możesz określić zainstalowaną wersję, wykonując następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="06a29-153">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="06a29-154">Aby uzyskać Blazor WebAssembly środowisko, wykonaj następujące polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="06a29-154">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="06a29-155">Aby uzyskać Blazor Server środowisko, wykonaj następujące polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="06a29-155">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="06a29-156">Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* i *Blazor Server* , zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="06a29-156">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="06a29-157">W przeglądarce przejdź do `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="06a29-157">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="06a29-158">Na pasku bocznym są dostępne wiele stron:</span><span class="sxs-lookup"><span data-stu-id="06a29-158">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="06a29-159">Strona główna</span><span class="sxs-lookup"><span data-stu-id="06a29-159">Home</span></span>
* <span data-ttu-id="06a29-160">Licznik</span><span class="sxs-lookup"><span data-stu-id="06a29-160">Counter</span></span>
* <span data-ttu-id="06a29-161">Pobieranie danych</span><span class="sxs-lookup"><span data-stu-id="06a29-161">Fetch data</span></span>

<span data-ttu-id="06a29-162">Na stronie licznik wybierz przycisk, aby zwiększyć licznik bez odświeżania strony.</span><span class="sxs-lookup"><span data-stu-id="06a29-162">On the Counter page, select the button to increment the counter without a page refresh.</span></span> <span data-ttu-id="06a29-163">Zwiększenie licznika na stronie sieci Web zwykle wymaga pisania kodu JavaScript, ale przy użyciu Blazor języka C#.</span><span class="sxs-lookup"><span data-stu-id="06a29-163">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="06a29-164">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="06a29-164">`Pages/Counter.razor`:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="06a29-165">Żądanie `/counter` w przeglądarce, zgodnie z definicją w `@page` dyrektywie u góry, powoduje, że `Counter` składnik renderuje jego zawartość.</span><span class="sxs-lookup"><span data-stu-id="06a29-165">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="06a29-166">Składniki są renderowane w postaci reprezentacji drzewa renderowania, która może być następnie używana do aktualizowania interfejsu użytkownika w elastyczny i wydajny sposób.</span><span class="sxs-lookup"><span data-stu-id="06a29-166">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="06a29-167">Za każdym razem, gdy przycisk jest zaznaczony:</span><span class="sxs-lookup"><span data-stu-id="06a29-167">Each time the button is selected:</span></span>

* <span data-ttu-id="06a29-168">`onclick`Zdarzenie jest wyzwalane.</span><span class="sxs-lookup"><span data-stu-id="06a29-168">The `onclick` event is fired.</span></span>
* <span data-ttu-id="06a29-169">`IncrementCount`Metoda jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="06a29-169">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="06a29-170">Wartość `currentCount` jest zwiększana.</span><span class="sxs-lookup"><span data-stu-id="06a29-170">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="06a29-171">Składnik jest ponownie renderowany.</span><span class="sxs-lookup"><span data-stu-id="06a29-171">The component is rendered again.</span></span>

<span data-ttu-id="06a29-172">Środowisko uruchomieniowe porównuje nową zawartość z poprzednią zawartością i stosuje tylko zmienioną zawartość do Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="06a29-172">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="06a29-173">Dodaj składnik do innego składnika przy użyciu składni języka HTML.</span><span class="sxs-lookup"><span data-stu-id="06a29-173">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="06a29-174">Na przykład Dodaj `Counter` składnik do strony głównej aplikacji przez dodanie `<Counter />` elementu do `Index` składnika.</span><span class="sxs-lookup"><span data-stu-id="06a29-174">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="06a29-175">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="06a29-175">`Pages/Index.razor`:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="06a29-176">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="06a29-176">Run the app.</span></span> <span data-ttu-id="06a29-177">Strona główna ma swój własny licznik dostarczony przez `Counter` składnik.</span><span class="sxs-lookup"><span data-stu-id="06a29-177">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="06a29-178">Parametry składnika są określone przy użyciu atrybutów lub [zawartości podrzędnej](xref:blazor/components/index#child-content), które umożliwiają ustawianie właściwości składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="06a29-178">Component parameters are specified using attributes or [child content](xref:blazor/components/index#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="06a29-179">Aby dodać parametr do `Counter` składnika, zaktualizuj `@code` blok składnika:</span><span class="sxs-lookup"><span data-stu-id="06a29-179">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="06a29-180">Dodaj właściwość publiczną dla `IncrementAmount` [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atrybutu.</span><span class="sxs-lookup"><span data-stu-id="06a29-180">Add a public property for `IncrementAmount` with a [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
* <span data-ttu-id="06a29-181">Zmień `IncrementCount` metodę, aby użyć `IncrementAmount` podczas zwiększania wartości `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="06a29-181">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="06a29-182">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="06a29-182">`Pages/Counter.razor`:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="06a29-183">Określ `IncrementAmount` `Index` element w elemencie składnika `<Counter>` przy użyciu atrybutu.</span><span class="sxs-lookup"><span data-stu-id="06a29-183">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="06a29-184">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="06a29-184">`Pages/Index.razor`:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="06a29-185">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="06a29-185">Run the app.</span></span> <span data-ttu-id="06a29-186">`Index`Składnik ma swój własny licznik, który zwiększa się o dziesięć za każdym razem, gdy przycisk jest zaznaczony.</span><span class="sxs-lookup"><span data-stu-id="06a29-186">The `Index` component has its own counter that increments by ten each time the button is selected.</span></span> <span data-ttu-id="06a29-187">`Counter`Składnik ( `Pages/Counter.razor` ) w `/counter` dalszym ciągu zwiększa się o jeden.</span><span class="sxs-lookup"><span data-stu-id="06a29-187">The `Counter` component (`Pages/Counter.razor`) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="06a29-188">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="06a29-188">Next steps</span></span>

<span data-ttu-id="06a29-189">Tworzenie Blazor aplikacji krok po kroku:</span><span class="sxs-lookup"><span data-stu-id="06a29-189">Build a Blazor app step-by-step:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="06a29-190">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="06a29-190">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
