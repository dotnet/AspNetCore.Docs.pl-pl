---
title: 'Używanie filtrów centrum w ASP.NET Core SignalR'
author: brecon
description: 'Dowiedz się, jak używać filtrów centrum w ASP.NET Core SignalR .'
monikerRange: '>= aspnetcore-5.0'
ms.author: brecon
ms.custom: mvc
ms.date: 05/22/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: signalr/hub-filters
ms.openlocfilehash: 5a4cb5122080b72875ac11cf2e682162d017d7b9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052724"
---
# <a name="use-hub-filters-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="120c5-103">Używanie filtrów centrum w ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="120c5-103">Use hub filters in ASP.NET Core SignalR</span></span>

<span data-ttu-id="120c5-104">Filtry centrum:</span><span class="sxs-lookup"><span data-stu-id="120c5-104">Hub filters:</span></span>

* <span data-ttu-id="120c5-105">Są dostępne w ASP.NET Core 5,0 lub nowszych.</span><span class="sxs-lookup"><span data-stu-id="120c5-105">Are available in ASP.NET Core 5.0 or later.</span></span>
* <span data-ttu-id="120c5-106">Zezwalaj na uruchamianie logiki przed wywołaniem i po wywołaniu metody Hub przez klientów.</span><span class="sxs-lookup"><span data-stu-id="120c5-106">Allow logic to run before and after hub methods are invoked by clients.</span></span>

<span data-ttu-id="120c5-107">Ten artykuł zawiera wskazówki dotyczące pisania i używania filtrów centrum.</span><span class="sxs-lookup"><span data-stu-id="120c5-107">This article provides guidance for writing and using hub filters.</span></span>

## <a name="configure-hub-filters"></a><span data-ttu-id="120c5-108">Konfigurowanie filtrów centrum</span><span class="sxs-lookup"><span data-stu-id="120c5-108">Configure hub filters</span></span>

<span data-ttu-id="120c5-109">Filtry centrów mogą być stosowane globalnie lub według typu centrum.</span><span class="sxs-lookup"><span data-stu-id="120c5-109">Hub filters can be applied globally or per hub type.</span></span> <span data-ttu-id="120c5-110">Kolejność, w której dodawane są filtry, to kolejność, w jakiej są uruchamiane filtry.</span><span class="sxs-lookup"><span data-stu-id="120c5-110">The order in which filters are added is the order in which the filters run.</span></span> <span data-ttu-id="120c5-111">Globalne filtry centrów działają przed filtrami centrum lokalnego.</span><span class="sxs-lookup"><span data-stu-id="120c5-111">Global hub filters run before local hub filters.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(options =>
    {
        // Global filters will run first
        options.AddFilter<CustomFilter>();
    }).AddHubOptions<ChatHub>(options =>
    {
        // Local filters will run second
        options.AddFilter<CustomFilter2>();
    });
}
```

<span data-ttu-id="120c5-112">Filtr centrum można dodać w jeden z następujących sposobów:</span><span class="sxs-lookup"><span data-stu-id="120c5-112">A hub filter can be added in one of the following ways:</span></span>

* <span data-ttu-id="120c5-113">Dodaj filtr według typu konkretnego:</span><span class="sxs-lookup"><span data-stu-id="120c5-113">Add a filter by concrete type:</span></span>

    ```csharp
    hubOptions.AddFilter<TFilter>();
    ```

    <span data-ttu-id="120c5-114">Zostanie on rozwiązany przez iniekcję zależności (DI) lub typ aktywowany.</span><span class="sxs-lookup"><span data-stu-id="120c5-114">This will be resolved from dependency injection (DI) or type activated.</span></span>

* <span data-ttu-id="120c5-115">Dodaj filtr według typu środowiska uruchomieniowego:</span><span class="sxs-lookup"><span data-stu-id="120c5-115">Add a filter by runtime type:</span></span>

    ```csharp
    hubOptions.AddFilter(typeof(TFilter));
    ```

    <span data-ttu-id="120c5-116">Zostanie on rozwiązany z typem "włączone" lub "aktywowany".</span><span class="sxs-lookup"><span data-stu-id="120c5-116">This will be resolved from DI or type activated.</span></span>

* <span data-ttu-id="120c5-117">Dodaj filtr według wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="120c5-117">Add a filter by instance:</span></span>

    ```csharp
    hubOptions.AddFilter(new MyFilter());
    ```

    <span data-ttu-id="120c5-118">To wystąpienie zostanie użyte jako pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="120c5-118">This instance will be used like a singleton.</span></span> <span data-ttu-id="120c5-119">Wszystkie wywołania metody centrum będą używać tego samego wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="120c5-119">All hub method invocations will use the same instance.</span></span>

<span data-ttu-id="120c5-120">Filtry centrów są tworzone i usuwane według wywołania centrum.</span><span class="sxs-lookup"><span data-stu-id="120c5-120">Hub filters are created and disposed per hub invocation.</span></span> <span data-ttu-id="120c5-121">Jeśli chcesz przechowywać stan globalny w filtrze lub bez tego stanu, Dodaj typ filtru centrum do DI jako pojedyncze w celu zwiększenia wydajności.</span><span class="sxs-lookup"><span data-stu-id="120c5-121">If you want to store global state in the filter, or no state, add the hub filter type to DI as a singleton for better performance.</span></span> <span data-ttu-id="120c5-122">Alternatywnie Dodaj filtr jako wystąpienie, jeśli to możliwe.</span><span class="sxs-lookup"><span data-stu-id="120c5-122">Alternatively, add the filter as an instance if you can.</span></span>

## <a name="create-hub-filters"></a><span data-ttu-id="120c5-123">Tworzenie filtrów centrum</span><span class="sxs-lookup"><span data-stu-id="120c5-123">Create hub filters</span></span>

<span data-ttu-id="120c5-124">Utwórz filtr, deklarując klasę, która dziedziczy z `IHubFilter` , i Dodaj `InvokeMethodAsync` metodę.</span><span class="sxs-lookup"><span data-stu-id="120c5-124">Create a filter by declaring a class that inherits from `IHubFilter`, and add the `InvokeMethodAsync` method.</span></span> <span data-ttu-id="120c5-125">Istnieje również `OnConnectedAsync` `OnDisconnectedAsync` możliwość zaimplementowania programu i opcjonalnie zapakowania `OnConnectedAsync` `OnDisconnectedAsync` odpowiednio metody i.</span><span class="sxs-lookup"><span data-stu-id="120c5-125">There is also `OnConnectedAsync` and `OnDisconnectedAsync` that can optionally be implemented to wrap the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods respectively.</span></span>

```csharp
public class CustomFilter : IHubFilter
{
    public async ValueTask<object> InvokeMethodAsync(
        HubInvocationContext invocationContext, Func<HubInvocationContext, ValueTask<object>> next)
    {
        Console.WriteLine($"Calling hub method '{invocationContext.HubMethodName}'");
        try
        {
            return await next(invocationContext);
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Exception calling '{invocationContext.HubMethodName}'");
            throw ex;
        }
    }

    // Optional method
    public Task OnConnectedAsync(HubLifetimeContext context, Func<HubLifetimeContext, Task> next)
    {
        return next(context);
    }

    // Optional method
    public Task OnDisconnectedAsync(
        HubLifetimeContext context, Exception exception, Func<HubLifetimeContext, Exception, Task> next)
    {
        return next(context, exception);
    }
}
```

<span data-ttu-id="120c5-126">Filtry są bardzo podobne do oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="120c5-126">Filters are very similar to middleware.</span></span> <span data-ttu-id="120c5-127">`next`Metoda wywołuje następny filtr.</span><span class="sxs-lookup"><span data-stu-id="120c5-127">The `next` method invokes the next filter.</span></span> <span data-ttu-id="120c5-128">Filtr końcowy wywoła metodę centrum.</span><span class="sxs-lookup"><span data-stu-id="120c5-128">The final filter will invoke the hub method.</span></span> <span data-ttu-id="120c5-129">Filtry mogą również przechowywać wyniki oczekiwania `next` i uruchamiania logiki po wywołaniu metody Hub przed zwróceniem wyniku z `next` .</span><span class="sxs-lookup"><span data-stu-id="120c5-129">Filters can also store the result from awaiting `next` and run logic after the hub method has been called before returning the result from `next`.</span></span>

<span data-ttu-id="120c5-130">Aby pominąć wywołanie metody centrum w filtrze, Zgłoś wyjątek typu `HubException` zamiast wywoływania `next` .</span><span class="sxs-lookup"><span data-stu-id="120c5-130">To skip a hub method invocation in a filter, throw an exception of type `HubException` instead of calling `next`.</span></span> <span data-ttu-id="120c5-131">Klient otrzyma błąd, jeśli oczekiwano wyniku.</span><span class="sxs-lookup"><span data-stu-id="120c5-131">The client will receive an error if it was expecting a result.</span></span>

## <a name="use-hub-filters"></a><span data-ttu-id="120c5-132">Korzystanie z filtrów centrum</span><span class="sxs-lookup"><span data-stu-id="120c5-132">Use hub filters</span></span>

<span data-ttu-id="120c5-133">Podczas pisania logiki filtru spróbuj uczynić ją ogólną przy użyciu atrybutów metod centralnych zamiast sprawdzać nazwy metod centrów.</span><span class="sxs-lookup"><span data-stu-id="120c5-133">When writing the filter logic, try to make it generic by using attributes on hub methods instead of checking for hub method names.</span></span>

<span data-ttu-id="120c5-134">Należy wziąć pod uwagę filtr, który będzie sprawdzać argument metody centrum dla zabronionych fraz i zastępować wszystkie frazy Znalezione za pomocą `***` .</span><span class="sxs-lookup"><span data-stu-id="120c5-134">Consider a filter that will check a hub method argument for banned phrases and replace any phrases it finds with `***`.</span></span>
<span data-ttu-id="120c5-135">W tym przykładzie Załóżmy, że `LanguageFilterAttribute` Klasa jest zdefiniowana.</span><span class="sxs-lookup"><span data-stu-id="120c5-135">For this example, assume a `LanguageFilterAttribute` class is defined.</span></span> <span data-ttu-id="120c5-136">Klasa ma właściwość o nazwie `FilterArgument` , którą można ustawić przy użyciu atrybutu.</span><span class="sxs-lookup"><span data-stu-id="120c5-136">The class has a property named `FilterArgument` that can be set when using the attribute.</span></span>

1. <span data-ttu-id="120c5-137">Umieść atrybut w metodzie centrum, która ma argument ciągu do oczyszczenia:</span><span class="sxs-lookup"><span data-stu-id="120c5-137">Place the attribute on the hub method that has a string argument to be cleaned:</span></span>

    ```csharp
    public class ChatHub
    {
        [LanguageFilter(filterArgument: 0)]
        public async Task SendMessage(string message, string username)
        {
            await Clients.All.SendAsync("SendMessage", $"{username} says: {message}");
        }
    }
    ```

1. <span data-ttu-id="120c5-138">Zdefiniuj filtr centrum, aby wyszukać atrybut i zamienić zabronione frazy w argumencie metody centrum z `**_` :</span><span class="sxs-lookup"><span data-stu-id="120c5-138">Define a hub filter to check for the attribute and replace banned phrases in a hub method argument with `**_`:</span></span>

    ```csharp
    public class LanguageFilter : IHubFilter
    {
        // populated from a file or inline
        private List<string> bannedPhrases = new List<string> { "async void", ".Result" };

        public async ValueTask<object> InvokeMethodAsync(HubInvocationContext invocationContext, 
            Func<HubInvocationContext, ValueTask<object>> next)
        {
            var languageFilter = (LanguageFilterAttribute)Attribute.GetCustomAttribute(
                invocationContext.HubMethod, typeof(LanguageFilterAttribute));
            if (languageFilter != null &&
                invocationContext.HubMethodArguments.Count > languageFilter.FilterArgument &&
                invocationContext.HubMethodArguments[languageFilter.FilterArgument] is string str)
            {
                foreach (var bannedPhrase in bannedPhrases)
                {
                    str = str.Replace(bannedPhrase, "_**");
                }

                arguments = invocationContext.HubMethodArguments.ToArray();
                arguments[languageFilter.FilterArgument] = str;
                invocationContext = new HubInvocationContext(invocationContext.Context,
                    invocationContext.ServiceProvider,
                    invocationContext.Hub,
                    invocationContext.HubMethod,
                    arguments);
            }

            return await next(invocationContext);
        }
    }
    ```

1. <span data-ttu-id="120c5-139">Zarejestruj filtr centrum w `Startup.ConfigureServices` metodzie.</span><span class="sxs-lookup"><span data-stu-id="120c5-139">Register the hub filter in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="120c5-140">Aby uniknąć ponownego inicjowania listy zabronionych wyrażeń dla każdego wywołania, filtr centrum jest rejestrowany jako pojedynczy:</span><span class="sxs-lookup"><span data-stu-id="120c5-140">To avoid reinitializing the banned phrases list for every invocation, the hub filter is registered as a singleton:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR(hubOptions =>
        {
            hubOptions.AddFilter<LanguageFilter>();
        });
    
        services.AddSingleton<LanguageFilter>();
    }
    ```

## <a name="the-hubinvocationcontext-object"></a><span data-ttu-id="120c5-141">Obiekt HubInvocationContext</span><span class="sxs-lookup"><span data-stu-id="120c5-141">The HubInvocationContext object</span></span>

<span data-ttu-id="120c5-142">`HubInvocationContext`Zawiera informacje dotyczące bieżącego wywołania metody centrum.</span><span class="sxs-lookup"><span data-stu-id="120c5-142">The `HubInvocationContext` contains information for the current hub method invocation.</span></span>

| <span data-ttu-id="120c5-143">Właściwość</span><span class="sxs-lookup"><span data-stu-id="120c5-143">Property</span></span> | <span data-ttu-id="120c5-144">Opis</span><span class="sxs-lookup"><span data-stu-id="120c5-144">Description</span></span> | <span data-ttu-id="120c5-145">Typ</span><span class="sxs-lookup"><span data-stu-id="120c5-145">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="120c5-146">`HubCallerContext`Zawiera informacje o połączeniu.</span><span class="sxs-lookup"><span data-stu-id="120c5-146">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="120c5-147">Wystąpienie centrum używane na potrzeby tego wywołania metody centrum.</span><span class="sxs-lookup"><span data-stu-id="120c5-147">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `HubMethodName` | <span data-ttu-id="120c5-148">Nazwa wywoływanej metody centrum.</span><span class="sxs-lookup"><span data-stu-id="120c5-148">The name of the hub method being invoked.</span></span> | `string` |
| `HubMethodArguments` | <span data-ttu-id="120c5-149">Lista argumentów, które są przekazane do metody centrum.</span><span class="sxs-lookup"><span data-stu-id="120c5-149">The list of arguments being passed to the hub method.</span></span> | `IReadOnlyList<string>` |
| `ServiceProvider` | <span data-ttu-id="120c5-150">Dostawca usługi w zakresie dla tego wywołania metody centrum.</span><span class="sxs-lookup"><span data-stu-id="120c5-150">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |
| `HubMethod` | <span data-ttu-id="120c5-151">Informacje o metodzie centrum.</span><span class="sxs-lookup"><span data-stu-id="120c5-151">The hub method information.</span></span> | `MethodInfo` |

## <a name="the-hublifetimecontext-object"></a><span data-ttu-id="120c5-152">Obiekt HubLifetimeContext</span><span class="sxs-lookup"><span data-stu-id="120c5-152">The HubLifetimeContext object</span></span>

<span data-ttu-id="120c5-153">`HubLifetimeContext`Zawiera informacje dotyczące `OnConnectedAsync` `OnDisconnectedAsync` metod i metody centrum.</span><span class="sxs-lookup"><span data-stu-id="120c5-153">The `HubLifetimeContext` contains information for the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods.</span></span>

| <span data-ttu-id="120c5-154">Właściwość</span><span class="sxs-lookup"><span data-stu-id="120c5-154">Property</span></span> | <span data-ttu-id="120c5-155">Opis</span><span class="sxs-lookup"><span data-stu-id="120c5-155">Description</span></span> | <span data-ttu-id="120c5-156">Typ</span><span class="sxs-lookup"><span data-stu-id="120c5-156">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="120c5-157">`HubCallerContext`Zawiera informacje o połączeniu.</span><span class="sxs-lookup"><span data-stu-id="120c5-157">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="120c5-158">Wystąpienie centrum używane na potrzeby tego wywołania metody centrum.</span><span class="sxs-lookup"><span data-stu-id="120c5-158">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `ServiceProvider` | <span data-ttu-id="120c5-159">Dostawca usługi w zakresie dla tego wywołania metody centrum.</span><span class="sxs-lookup"><span data-stu-id="120c5-159">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |

## <a name="authorization-and-filters"></a><span data-ttu-id="120c5-160">Autoryzacja i filtry</span><span class="sxs-lookup"><span data-stu-id="120c5-160">Authorization and filters</span></span>

<span data-ttu-id="120c5-161">[Autoryzuj atrybuty w metodach centrum](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) uruchamianych przed filtrami centrum.</span><span class="sxs-lookup"><span data-stu-id="120c5-161">[Authorize attributes on hub methods](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) run before hub filters.</span></span>
