---
title: Korzystanie z centrów w ASP.NET CoreSignalR
author: bradygaster
description: Dowiedz się, jak korzystać z SignalRcentrów w ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/hubs
ms.openlocfilehash: 6ea8a8e9ffb6549a285f320eb0a4a2e5d218483a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775222"
---
# <a name="use-hubs-in-signalr-for-aspnet-core"></a>Użyj centrów w sygnalizacji dla ASP.NET Core

Autor [Rachel Appel](https://twitter.com/rachelappel) i [Jan Griffin](https://twitter.com/1kevgriff)

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(jak pobrać)](xref:index#how-to-download-a-sample)

## <a name="what-is-a-signalr-hub"></a>Co to jest centrum sygnałów

Interfejs API centrów sygnałów umożliwia wywoływanie metod na podłączonych klientach z serwera. W kodzie serwera należy zdefiniować metody, które są wywoływane przez klienta. W kodzie klienta należy zdefiniować metody, które są wywoływane z serwera programu. Program sygnalizujący zajmuje się wszystkimi wszystkimi scenami, które umożliwiają komunikację między klientem i serwerem a klientem w czasie rzeczywistym.

## <a name="configure-signalr-hubs"></a>Konfigurowanie centrów sygnałów

Oprogramowanie pośredniczące sygnalizujące wymaga pewnych usług, które są konfigurowane przez wywołanie `services.AddSignalR`.

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

Podczas dodawania funkcji sygnału do aplikacji ASP.NET Core, trasy sygnałów Instalatora są `endpoint.MapHub` `Startup.Configure` wywoływane przez wywołanie `app.UseEndpoints` wywołania zwrotnego metody.

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Podczas dodawania funkcji sygnału do aplikacji ASP.NET Core, trasy sygnałów Instalatora są wywoływane przez wywołanie `app.UseSignalR` `Startup.Configure` metody.

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a>Tworzenie i używanie centrów

Utwórz centrum, deklarując klasę, która dziedziczy z `Hub`, i Dodaj do niej metody publiczne. Klienci mogą wywoływać metody, które są `public`zdefiniowane jako.

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

Można określić typ zwracany i parametry, w tym typy złożone i tablice, tak jak w przypadku dowolnej metody języka C#. Sygnalizujący obsługuje serializacji i deserializacji złożonych obiektów i tablic w parametrach i zwracanych wartości.

> [!NOTE]
> Centra są przejściowe:
>
> * Nie przechowuj stanu we właściwości klasy centrum. Każde wywołanie metody centrum jest wykonywane na nowym wystąpieniu centrum.
> * Używane `await` podczas wywoływania metod asynchronicznych, które są zależne od utrzymywania aktywności centrum. Na przykład metoda, taka jak `Clients.All.SendAsync(...)` może zakończyć się niepowodzeniem, jeśli jest `await` wywoływana bez i zakończy się `SendAsync` przed zakończeniem.

## <a name="the-context-object"></a>Obiekt kontekstu

`Hub` Klasa ma `Context` właściwość, która zawiera następujące właściwości z informacjami o połączeniu:

| Właściwość | Opis |
| ------ | ----------- |
| `ConnectionId` | Pobiera unikatowy identyfikator połączenia przypisany przez Sygnalizującer. Istnieje jeden identyfikator połączenia dla każdego połączenia.|
| `UserIdentifier` | Pobiera [Identyfikator użytkownika](xref:signalr/groups). Domyślnie program sygnalizujący używa programu `ClaimTypes.NameIdentifier` ze `ClaimsPrincipal` skojarzonego z połączeniem jako identyfikator użytkownika. |
| `User` | Pobiera `ClaimsPrincipal` skojarzone z bieżącym użytkownikiem. |
| `Items` | Pobiera kolekcję klucz/wartość, której można użyć do udostępniania danych w zakresie tego połączenia. Dane można przechowywać w tej kolekcji i będzie ona trwała dla połączenia między różnymi wywołaniami metody centrum. |
| `Features` | Pobiera kolekcję funkcji dostępnych w ramach połączenia. Na razie ta kolekcja nie jest wymagana w większości scenariuszy, więc nie jest jeszcze udokumentowana. |
| `ConnectionAborted` | Pobiera powiadomienie `CancellationToken` , gdy połączenie zostanie przerwane. |

`Hub.Context`zawiera również następujące metody:

| Metoda | Opis |
| ------ | ----------- |
| `GetHttpContext` | Zwraca `HttpContext` dla połączenia lub `null` Jeśli połączenie nie jest skojarzone z żądaniem http. W przypadku połączeń HTTP można użyć tej metody, aby uzyskać informacje takie jak nagłówki HTTP i ciągi zapytań. |
| `Abort` | Przerywa połączenie. |

## <a name="the-clients-object"></a>Obiekt clients

`Hub` Klasa ma `Clients` właściwość, która zawiera następujące właściwości komunikacji między serwerem i klientem:

| Właściwość | Opis |
| ------ | ----------- |
| `All` | Wywołuje metodę na wszystkich połączonych klientach |
| `Caller` | Wywołuje metodę na kliencie, który wywołał metodę Hub |
| `Others` | Wywołuje metodę na wszystkich połączonych klientach z wyjątkiem klienta, który wywołał metodę |

`Hub.Clients`zawiera również następujące metody:

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

Każda właściwość lub metoda w powyższych tabelach zwraca obiekt z `SendAsync` metodą. `SendAsync` Metoda umożliwia podanie nazwy i parametrów metody klienta do wywołania.

## <a name="send-messages-to-clients"></a>Wysyłanie komunikatów do klientów

Aby wykonać wywołania do określonych klientów, użyj właściwości `Clients` obiektu. W poniższym przykładzie istnieją trzy metody centralne:

* `SendMessage`wysyła komunikat do wszystkich połączonych klientów przy użyciu programu `Clients.All`.
* `SendMessageToCaller`wysyła komunikat z powrotem do obiektu wywołującego za `Clients.Caller`pomocą polecenia.
* `SendMessageToGroups`wysyła komunikat do wszystkich klientów w `SignalR Users` grupie.

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a>Centra o jednoznacznie określonym typie

Wadą używania `SendAsync` jest to, że opiera się na ciągu Magic, aby określić metodę klienta, która ma zostać wywołana. Spowoduje to pozostawienie kodu otwartego na błędy środowiska uruchomieniowego, jeśli nazwa metody jest błędnie wpisana lub nie została podana w kliencie.

Alternatywą dla użycia `SendAsync` jest silna wartość `Hub` typu with <xref:Microsoft.AspNetCore.SignalR.Hub%601>. W poniższym przykładzie metody `ChatHub` klienta zostały wyodrębnione do interfejsu o nazwie. `IChatClient`

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

Ten interfejs może służyć do refaktoryzacji poprzedniego `ChatHub` przykładu.

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

Użycie `Hub<IChatClient>` umożliwia sprawdzenie w czasie kompilacji metod klienta. Zapobiega to problemom spowodowanym użyciem ciągów Magic, `Hub<T>` ponieważ może zapewnić tylko dostęp do metod zdefiniowanych w interfejsie.

Użycie silnie określonego `Hub<T>` typu wyłącza możliwość użycia `SendAsync`. Wszelkie metody zdefiniowane w interfejsie mogą być nadal zdefiniowane jako asynchroniczne. W rzeczywistości każda z tych metod powinna zwrócić `Task`. Ponieważ jest to interfejs, nie używaj `async` słowa kluczowego. Przykład:

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> `Async` Sufiks nie jest usuwany z nazwy metody. Chyba że metoda klienta nie jest zdefiniowana `.on('MyMethodAsync')`z, nie należy `MyMethodAsync` używać jako nazwy.

## <a name="change-the-name-of-a-hub-method"></a>Zmień nazwę metody centrum

Domyślnie nazwa metody centrum serwera jest nazwą metody .NET. Można jednak użyć atrybutu [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) , aby zmienić to ustawienie domyślne, i ręcznie określić nazwę dla metody. Klient powinien używać tej nazwy zamiast nazwy metody .NET podczas wywoływania metody.

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a>Obsługa zdarzeń dla połączenia

Interfejs SignalR API centrów zapewnia `OnConnectedAsync` metody `OnDisconnectedAsync` wirtualne do zarządzania połączeniami i ich śledzenia. Zastąp `OnConnectedAsync` metodę wirtualną, aby wykonać akcje, gdy klient łączy się z centrum, na przykład dodając go do grupy.

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

Zastąp `OnDisconnectedAsync` metodę wirtualną, aby wykonać akcje po rozłączeniu klienta. Jeśli klient odłączy się celowo (na przykład przez `connection.stop()`wywołanie), `exception` parametr będzie `null`. Jeśli jednak klient zostanie rozłączony z powodu błędu (takiego jak awaria sieci), `exception` parametr będzie zawierać wyjątek opisujący błąd.

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

[!INCLUDE[](~/includes/connectionid-signalr.md)]

## <a name="handle-errors"></a>Obsługa błędów

Wyjątki zgłoszone w metodach centrum są wysyłane do klienta, który wywołał metodę. W kliencie JavaScript `invoke` Metoda zwraca [obietnicę języka JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). Gdy klient otrzymuje błąd z obsługą dołączoną do obietnicy przy użyciu `catch`, jest wywoływana i przenoszona jako obiekt JavaScript `Error` .

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

Jeśli centrum zgłosi wyjątek, połączenia nie są zamknięte. Domyślnie program SignalR zwraca ogólny komunikat o błędzie do klienta. Przykład:

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

Nieoczekiwane wyjątki często zawierają informacje poufne, takie jak nazwa serwera bazy danych w wyjątku wyzwalanym w przypadku niepowodzenia połączenia z bazą danych. SignalRDomyślnie nie uwidacznia tych szczegółowych komunikatów o błędach jako miary zabezpieczeń. Aby uzyskać więcej informacji o tym, dlaczego szczegóły wyjątku są pomijane, zobacz artykuł dotyczący [zagadnień dotyczących zabezpieczeń](xref:signalr/security#exceptions) .

Jeśli *masz wyjątkowe warunki, które chcesz* propagować do klienta, możesz użyć `HubException` klasy. W przypadku zgłoszenia `HubException` z poziomu metody SignalR centrum program wyśle do klienta cały komunikat, który nie został zmodyfikowany. **will**

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> SignalRtylko wysyła `Message` Właściwość wyjątku do klienta. Ślad stosu i inne właściwości tego wyjątku nie są dostępne dla klienta.

## <a name="related-resources"></a>Powiązane zasoby

* [Wprowadzenie do ASP.NET CoreSignalR](xref:signalr/introduction)
* [Klient JavaScript](xref:signalr/javascript-client)
* [Publikowanie na platformie Azure](xref:signalr/publish-to-azure-web-app)
