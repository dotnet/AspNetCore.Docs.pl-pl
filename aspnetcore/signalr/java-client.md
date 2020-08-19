---
title: SignalRKlient Java ASP.NET Core
author: mikaelm12
description: Dowiedz się, jak używać SignalR klienta Java ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
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
uid: signalr/java-client
ms.openlocfilehash: 5351bcf5adb4a79efc993f48acbb959f113c814f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634959"
---
# <a name="aspnet-core-no-locsignalr-java-client"></a>SignalRKlient Java ASP.NET Core

Autor [Mikael Mengistu](https://twitter.com/MikaelM_12)

Klient Java umożliwia łączenie się z serwerem ASP.NET Core SignalR przy użyciu kodu Java, w tym aplikacji dla systemu Android. Podobnie jak [klient JavaScript](xref:signalr/javascript-client) i [Klient platformy .NET](xref:signalr/dotnet-client), klient Java umożliwia odbieranie i wysyłanie komunikatów do centrum w czasie rzeczywistym. Klient Java jest dostępny w ASP.NET Core 2,2 i nowszych.

Przykładowa aplikacja konsoli Java, do której odwołuje się ten artykuł, używa SignalR klienta Java.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="install-the-no-locsignalr-java-client-package"></a>Zainstaluj SignalR pakiet klienta Java

*Sygnał-1.0.0* plik JAR umożliwia klientom łączenie się z SignalR koncentratorami. Aby znaleźć najnowszy numer wersji pliku JAR, zobacz [wyniki wyszukiwania Maven](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).

Jeśli używasz Gradle, Dodaj następujący wiersz do `dependencies` sekcji pliku *Build. Gradle* :

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

Jeśli używasz Maven, Dodaj następujące wiersze wewnątrz `<dependencies>` elementu *pom.xml* pliku:

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a>Nawiązywanie połączenia z centrum

Aby ustanowić `HubConnection` , `HubConnectionBuilder` należy użyć. Podczas tworzenia połączenia można skonfigurować adres URL i poziom dziennika centrum. Skonfiguruj wszystkie wymagane opcje, wywołując dowolną `HubConnectionBuilder` metodę przed `build` . Rozpocznij połączenie z usługą `start` .

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a>Wywoływanie metod centrów z klienta

Wywołanie wywołania `send` metody centrum. Przekaż nazwę metody Hub i wszystkie argumenty zdefiniowane w metodzie Hub do `send` .

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> Wywoływanie metod centrów z poziomu klienta jest obsługiwane tylko w przypadku korzystania z usługi platformy Azure SignalR w trybie *domyślnym* . Aby uzyskać więcej informacji, zobacz [często zadawane pytania (repozytorium Azure-sygnalizujące GitHub)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="call-client-methods-from-hub"></a>Wywoływanie metod klienta z centrum

Służy `hubConnection.on` do definiowania metod na kliencie, które mogą być wywoływane przez centrum. Zdefiniuj metody po kompilacji, ale przed rozpoczęciem połączenia.

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a>Dodawanie rejestrowania

SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania. Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania. Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Można je bezpiecznie zignorować.

## <a name="android-development-notes"></a>Uwagi dotyczące programowania dla systemu Android

W odniesieniu do Android SDK zgodności dla SignalR funkcji klienta należy wziąć pod uwagę następujące elementy podczas określania docelowej wersji Android SDK:

* SignalRKlient Java zostanie uruchomiony na poziomie interfejsu API systemu Android w wersji 16 lub nowszej.
* Połączenie za pośrednictwem SignalR usługi platformy Azure wymaga poziomu interfejsu API systemu Android 20 i nowszego, ponieważ [ SignalR usługa platformy Azure](/azure/azure-signalr/signalr-overview) wymaga protokołu TLS 1,2 i nie obsługuje mechanizmów szyfrowania opartych na algorytmie SHA-1. [Dodano obsługę szyfrowania SHA-256 (i powyżej)](https://developer.android.com/reference/javax/net/ssl/SSLSocket) w systemie Android na poziomie interfejsu API 20.

## <a name="configure-bearer-token-authentication"></a>Konfigurowanie uwierzytelniania tokenów okaziciela

W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając "fabrykę tokenów dostępu" do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html). Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a>Znane ograniczenia

::: moniker range=">= aspnetcore-3.0"

* Obsługiwany jest tylko protokół JSON.
* Transport zdarzeń powrotu i przesyłania do serwera nie jest obsługiwany.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Obsługiwany jest tylko protokół JSON.
* Obsługiwany jest tylko transport gniazd WebSockets.
* Przesyłanie strumieniowe nie jest jeszcze obsługiwane.

::: moniker-end

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Dokumentacja interfejsów API języka Java](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [SignalRDokumentacja bezserwerowa usługi platformy Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)
