---
title: Używanie filtrów centrum w ASP.NET CoreSignalR
author: brecon
description: Dowiedz się, jak używać filtrów centrum w ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-5.0'
ms.author: brecon
ms.custom: mvc
ms.date: 05/22/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/hub-filters
ms.openlocfilehash: db6439ab4e61d0412148c5ea72b52d429367491b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022150"
---
# <a name="use-hub-filters-in-aspnet-core-no-locsignalr"></a>Używanie filtrów centrum w ASP.NET CoreSignalR

Filtry centrum:

* Są dostępne w ASP.NET Core 5,0 lub nowszych.
* Zezwalaj na uruchamianie logiki przed wywołaniem i po wywołaniu metody Hub przez klientów.

Ten artykuł zawiera wskazówki dotyczące pisania i używania filtrów centrum.

## <a name="configure-hub-filters"></a>Konfigurowanie filtrów centrum

Filtry centrów mogą być stosowane globalnie lub według typu centrum. Kolejność, w której dodawane są filtry, to kolejność, w jakiej są uruchamiane filtry. Globalne filtry centrów działają przed filtrami centrum lokalnego.

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

Filtr centrum można dodać w jeden z następujących sposobów:

* Dodaj filtr według typu konkretnego:

    ```csharp
    hubOptions.AddFilter<TFilter>();
    ```

    Zostanie on rozwiązany przez iniekcję zależności (DI) lub typ aktywowany.

* Dodaj filtr według typu środowiska uruchomieniowego:

    ```csharp
    hubOptions.AddFilter(typeof(TFilter));
    ```

    Zostanie on rozwiązany z typem "włączone" lub "aktywowany".

* Dodaj filtr według wystąpienia:

    ```csharp
    hubOptions.AddFilter(new MyFilter());
    ```

    To wystąpienie zostanie użyte jako pojedyncze. Wszystkie wywołania metody centrum będą używać tego samego wystąpienia.

Filtry centrów są tworzone i usuwane według wywołania centrum. Jeśli chcesz przechowywać stan globalny w filtrze lub bez tego stanu, Dodaj typ filtru centrum do DI jako pojedyncze w celu zwiększenia wydajności. Alternatywnie Dodaj filtr jako wystąpienie, jeśli to możliwe.

## <a name="create-hub-filters"></a>Tworzenie filtrów centrum

Utwórz filtr, deklarując klasę, która dziedziczy z `IHubFilter` , i Dodaj `InvokeMethodAsync` metodę. Istnieje również `OnConnectedAsync` `OnDisconnectedAsync` możliwość zaimplementowania programu i opcjonalnie zapakowania `OnConnectedAsync` `OnDisconnectedAsync` odpowiednio metody i.

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

Filtry są bardzo podobne do oprogramowania pośredniczącego. `next`Metoda wywołuje następny filtr. Filtr końcowy wywoła metodę centrum. Filtry mogą również przechowywać wyniki oczekiwania `next` i uruchamiania logiki po wywołaniu metody Hub przed zwróceniem wyniku z `next` .

Aby pominąć wywołanie metody centrum w filtrze, Zgłoś wyjątek typu `HubException` zamiast wywoływania `next` . Klient otrzyma błąd, jeśli oczekiwano wyniku.

## <a name="use-hub-filters"></a>Korzystanie z filtrów centrum

Podczas pisania logiki filtru spróbuj uczynić ją ogólną przy użyciu atrybutów metod centralnych zamiast sprawdzać nazwy metod centrów.

Należy wziąć pod uwagę filtr, który będzie sprawdzać argument metody centrum dla zabronionych fraz i zastępować wszystkie frazy Znalezione za pomocą `***` .
W tym przykładzie Załóżmy, że `LanguageFilterAttribute` Klasa jest zdefiniowana. Klasa ma właściwość o nazwie `FilterArgument` , którą można ustawić przy użyciu atrybutu.

1. Umieść atrybut w metodzie centrum, która ma argument ciągu do oczyszczenia:

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

1. Zdefiniuj filtr centrum, aby wyszukać atrybut i zamienić zabronione frazy w argumencie metody centrum z `***` :

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
                    str = str.Replace(bannedPhrase, "***");
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

1. Zarejestruj filtr centrum w `Startup.ConfigureServices` metodzie. Aby uniknąć ponownego inicjowania listy zabronionych wyrażeń dla każdego wywołania, filtr centrum jest rejestrowany jako pojedynczy:

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

## <a name="the-hubinvocationcontext-object"></a>Obiekt HubInvocationContext

`HubInvocationContext`Zawiera informacje dotyczące bieżącego wywołania metody centrum.

| Właściwość | Opis | Typ |
| ------ | ------ | ----------- |
| `Context ` | `HubCallerContext`Zawiera informacje o połączeniu. | `HubCallerContext` |
| `Hub` | Wystąpienie centrum używane na potrzeby tego wywołania metody centrum. | `Hub` |
| `HubMethodName` | Nazwa wywoływanej metody centrum. | `string` |
| `HubMethodArguments` | Lista argumentów, które są przekazane do metody centrum. | `IReadOnlyList<string>` |
| `ServiceProvider` | Dostawca usługi w zakresie dla tego wywołania metody centrum. | `IServiceProvider` |
| `HubMethod` | Informacje o metodzie centrum. | `MethodInfo` |

## <a name="the-hublifetimecontext-object"></a>Obiekt HubLifetimeContext

`HubLifetimeContext`Zawiera informacje dotyczące `OnConnectedAsync` `OnDisconnectedAsync` metod i metody centrum.

| Właściwość | Opis | Typ |
| ------ | ------ | ----------- |
| `Context ` | `HubCallerContext`Zawiera informacje o połączeniu. | `HubCallerContext` |
| `Hub` | Wystąpienie centrum używane na potrzeby tego wywołania metody centrum. | `Hub` |
| `ServiceProvider` | Dostawca usługi w zakresie dla tego wywołania metody centrum. | `IServiceProvider` |

## <a name="authorization-and-filters"></a>Autoryzacja i filtry

[Autoryzuj atrybuty w metodach centrum](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) uruchamianych przed filtrami centrum.
