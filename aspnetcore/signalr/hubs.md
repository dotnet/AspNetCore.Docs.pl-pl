---
title: Korzystanie z centrów w ASP.NET Core SignalR
author: bradygaster
description: Dowiedz się, jak korzystać z centrów w ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
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
uid: signalr/hubs
ms.openlocfilehash: 4a31c16eb44e2244574d0df49c30e7a44b2bba6e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050943"
---
# <a name="use-hubs-in-no-locsignalr-for-aspnet-core"></a>Korzystanie z koncentratorów w programie SignalR dla ASP.NET Core

Autor [Rachel Appel](https://twitter.com/rachelappel) i [Jan Griffin](https://twitter.com/1kevgriff)

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(jak pobrać)](xref:index#how-to-download-a-sample)

## <a name="what-is-a-no-locsignalr-hub"></a>Co to jest SignalR centrum

SignalRInterfejs API centrów umożliwia wywoływanie metod na podłączonych klientach z serwera. W kodzie serwera należy zdefiniować metody, które są wywoływane przez klienta. W kodzie klienta należy zdefiniować metody, które są wywoływane z serwera programu. SignalR zajmuje się wszystkimi wszystkimi scenami, które zapewniają możliwość komunikacji między klientem i serwerem w czasie rzeczywistym.

## <a name="configure-no-locsignalr-hubs"></a>Konfigurowanie SignalR centrów

SignalROprogramowanie pośredniczące wymaga pewnych usług, które są konfigurowane przez wywołanie metody `services.AddSignalR` .

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

Podczas dodawania SignalR funkcji do aplikacji ASP.NET Core, skonfiguruj SignalR trasy, wywołując wywołanie `endpoint.MapHub` `Startup.Configure` metody `app.UseEndpoints` wywołania zwrotnego.

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Podczas dodawania SignalR funkcji do aplikacji ASP.NET Core, skonfiguruj SignalR trasy, wywołując `app.UseSignalR` `Startup.Configure` metodę.

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a>Tworzenie i używanie centrów

Utwórz centrum, deklarując klasę, która dziedziczy z `Hub` , i Dodaj do niej metody publiczne. Klienci mogą wywoływać metody, które są zdefiniowane jako `public` .

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

Można określić typ zwracany i parametry, w tym typy złożone i tablice, tak jak w przypadku dowolnej metody języka C#. SignalR obsługuje serializacji i deserializacji złożonych obiektów i tablic w parametrach i zwracanych wartości.

> [!NOTE]
> Centra są przejściowe:
>
> * Nie przechowuj stanu we właściwości klasy centrum. Każde wywołanie metody centrum jest wykonywane na nowym wystąpieniu centrum.
> * Używane `await` podczas wywoływania metod asynchronicznych, które są zależne od utrzymywania aktywności centrum. Na przykład metoda, taka jak `Clients.All.SendAsync(...)` może zakończyć się niepowodzeniem, jeśli jest wywoływana bez `await` i zakończy się przed `SendAsync` zakończeniem.

## <a name="the-context-object"></a>Obiekt kontekstu

`Hub`Klasa ma `Context` Właściwość, która zawiera następujące właściwości z informacjami o połączeniu:

| Właściwość | Opis |
| ------ | ----------- |
| `ConnectionId` | Pobiera unikatowy identyfikator połączenia, przypisany przez SignalR . Istnieje jeden identyfikator połączenia dla każdego połączenia.|
| `UserIdentifier` | Pobiera [Identyfikator użytkownika](xref:signalr/groups). Domyślnie program SignalR używa programu `ClaimTypes.NameIdentifier` ze `ClaimsPrincipal` skojarzonego z połączeniem jako identyfikator użytkownika. |
| `User` | Pobiera `ClaimsPrincipal` skojarzone z bieżącym użytkownikiem. |
| `Items` | Pobiera kolekcję klucz/wartość, której można użyć do udostępniania danych w zakresie tego połączenia. Dane można przechowywać w tej kolekcji i będzie ona trwała dla połączenia między różnymi wywołaniami metody centrum. |
| `Features` | Pobiera kolekcję funkcji dostępnych w ramach połączenia. Na razie ta kolekcja nie jest wymagana w większości scenariuszy, więc nie jest jeszcze udokumentowana. |
| `ConnectionAborted` | Pobiera `CancellationToken` powiadomienie, gdy połączenie zostanie przerwane. |

`Hub.Context` zawiera również następujące metody:

| Metoda | Opis |
| ------ | ----------- |
| `GetHttpContext` | Zwraca `HttpContext` dla połączenia lub `null` Jeśli połączenie nie jest skojarzone z żądaniem http. W przypadku połączeń HTTP można użyć tej metody, aby uzyskać informacje takie jak nagłówki HTTP i ciągi zapytań. |
| `Abort` | Przerywa połączenie. |

## <a name="the-clients-object"></a>Obiekt clients

`Hub`Klasa ma `Clients` Właściwość, która zawiera następujące właściwości komunikacji między serwerem i klientem:

| Właściwość | Opis |
| ------ | ----------- |
| `All` | Wywołuje metodę na wszystkich połączonych klientach |
| `Caller` | Wywołuje metodę na kliencie, który wywołał metodę Hub |
| `Others` | Wywołuje metodę na wszystkich połączonych klientach z wyjątkiem klienta, który wywołał metodę |

`Hub.Clients` zawiera również następujące metody:

| Metoda | Opis |
| ------ | ----------- |
| `AllExcept` | Wywołuje metodę na wszystkich połączonych klientach z wyjątkiem określonych połączeń |
| `Client` | Wywołuje metodę na określonym podłączonym kliencie |
| `Clients` | Wywołuje metodę na określonych połączonych klientach |
| `Group` | Wywołuje metodę dla wszystkich połączeń w określonej grupie  |
| `GroupExcept` | Wywołuje metodę dla wszystkich połączeń w określonej grupie, z wyjątkiem określonych połączeń |
| `Groups` | Wywołuje metodę dla wielu grup połączeń  |
| `OthersInGroup` | Wywołuje metodę w grupie połączeń z wyłączeniem klienta, który wywołał metodę Hub  |
| `User` | Wywołuje metodę dla wszystkich połączeń skojarzonych z określonym użytkownikiem |
| `Users` | Wywołuje metodę dla wszystkich połączeń skojarzonych z określonymi użytkownikami |

Każda właściwość lub metoda w powyższych tabelach zwraca obiekt z `SendAsync` metodą. `SendAsync`Metoda umożliwia podanie nazwy i parametrów metody klienta do wywołania.

## <a name="send-messages-to-clients"></a>Wysyłanie komunikatów do klientów

Aby wykonać wywołania do określonych klientów, użyj właściwości `Clients` obiektu. W poniższym przykładzie istnieją trzy metody centralne:

* `SendMessage` wysyła komunikat do wszystkich połączonych klientów przy użyciu programu `Clients.All` .
* `SendMessageToCaller` wysyła komunikat z powrotem do obiektu wywołującego za pomocą polecenia `Clients.Caller` .
* `SendMessageToGroups` wysyła komunikat do wszystkich klientów w `SignalR Users` grupie.

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a>Centra o jednoznacznie określonym typie

Wadą używania jest to `SendAsync` , że opiera się na ciągu Magic, aby określić metodę klienta, która ma zostać wywołana. Spowoduje to pozostawienie kodu otwartego na błędy środowiska uruchomieniowego, jeśli nazwa metody jest błędnie wpisana lub nie została podana w kliencie.

Alternatywą dla użycia `SendAsync` jest silna wartość typu `Hub` with <xref:Microsoft.AspNetCore.SignalR.Hub%601> . W poniższym przykładzie `ChatHub` metody klienta zostały wyodrębnione do interfejsu o nazwie `IChatClient` .

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

Ten interfejs może służyć do refaktoryzacji poprzedniego `ChatHub` przykładu.

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

Użycie `Hub<IChatClient>` umożliwia sprawdzenie w czasie kompilacji metod klienta. Zapobiega to problemom spowodowanym użyciem ciągów Magic, ponieważ `Hub<T>` może zapewnić tylko dostęp do metod zdefiniowanych w interfejsie.

Użycie silnie określonego typu `Hub<T>` wyłącza możliwość użycia `SendAsync` . Wszelkie metody zdefiniowane w interfejsie mogą być nadal zdefiniowane jako asynchroniczne. W rzeczywistości każda z tych metod powinna zwrócić `Task` . Ponieważ jest to interfejs, nie używaj `async` słowa kluczowego. Przykład:

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> `Async`Sufiks nie jest usuwany z nazwy metody. Chyba że metoda klienta nie jest zdefiniowana z `.on('MyMethodAsync')` , nie należy używać `MyMethodAsync` jako nazwy.

## <a name="change-the-name-of-a-hub-method"></a>Zmień nazwę metody centrum

Domyślnie nazwa metody centrum serwera jest nazwą metody .NET. Można jednak użyć atrybutu [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) , aby zmienić to ustawienie domyślne, i ręcznie określić nazwę dla metody. Klient powinien używać tej nazwy zamiast nazwy metody .NET podczas wywoływania metody.

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a>Obsługa zdarzeń dla połączenia

SignalRInterfejs API centrów zapewnia `OnConnectedAsync` `OnDisconnectedAsync` metody wirtualne do zarządzania połączeniami i ich śledzenia. Zastąp `OnConnectedAsync` metodę wirtualną, aby wykonać akcje, gdy klient łączy się z centrum, na przykład dodając go do grupy.

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

Zastąp `OnDisconnectedAsync` metodę wirtualną, aby wykonać akcje po rozłączeniu klienta. Jeśli klient odłączy się celowo ( `connection.stop()` na przykład przez wywołanie), `exception` parametr będzie `null` . Jeśli jednak klient zostanie rozłączony z powodu błędu (takiego jak awaria sieci), `exception` parametr będzie zawierać wyjątek opisujący błąd.

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

[!INCLUDE[](~/includes/connectionid-signalr.md)]

## <a name="handle-errors"></a>Obsługa błędów

Wyjątki zgłoszone w metodach centrum są wysyłane do klienta, który wywołał metodę. W kliencie JavaScript `invoke` Metoda zwraca [obietnicę języka JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). Gdy klient otrzymuje błąd z obsługą dołączoną do obietnicy przy użyciu `catch` , jest wywoływana i przenoszona jako obiekt JavaScript `Error` .

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

Jeśli centrum zgłosi wyjątek, połączenia nie są zamknięte. Domyślnie program SignalR zwraca ogólny komunikat o błędzie do klienta. Przykład:

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

Nieoczekiwane wyjątki często zawierają informacje poufne, takie jak nazwa serwera bazy danych w wyjątku wyzwalanym w przypadku niepowodzenia połączenia z bazą danych. SignalR Domyślnie nie uwidacznia tych szczegółowych komunikatów o błędach jako miary zabezpieczeń. Aby uzyskać więcej informacji o tym, dlaczego szczegóły wyjątku są pomijane, zobacz artykuł dotyczący [zagadnień dotyczących zabezpieczeń](xref:signalr/security#exceptions) .

Jeśli *masz wyjątkowe warunki, które chcesz* propagować do klienta, możesz użyć `HubException` klasy. W przypadku zgłoszenia `HubException` z poziomu metody centrum program SignalR **will** wyśle do klienta cały komunikat, który nie został zmodyfikowany.

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> SignalR tylko wysyła `Message` Właściwość wyjątku do klienta. Ślad stosu i inne właściwości tego wyjątku nie są dostępne dla klienta.

## <a name="related-resources"></a>Powiązane zasoby

* [Wprowadzenie do ASP.NET Core SignalR](xref:signalr/introduction)
* [Klient JavaScript](xref:signalr/javascript-client)
* [Publikowanie na platformie Azure](xref:signalr/publish-to-azure-web-app)
