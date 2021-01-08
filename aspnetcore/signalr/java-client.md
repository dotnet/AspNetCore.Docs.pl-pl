---
title: SignalRKlient Java ASP.NET Core
author: mikaelm12
description: Dowiedz się, jak używać SignalR klienta Java ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/java-client
ms.openlocfilehash: 92941d21820de90eb2ae8fb76c21c588ed9f1ffb
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024759"
---
# <a name="aspnet-core-no-locsignalr-java-client"></a><span data-ttu-id="70fe8-103">SignalRKlient Java ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="70fe8-103">ASP.NET Core SignalR Java client</span></span>

<span data-ttu-id="70fe8-104">Autor [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="70fe8-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="70fe8-105">Klient Java umożliwia łączenie się z serwerem ASP.NET Core SignalR przy użyciu kodu Java, w tym aplikacji dla systemu Android.</span><span class="sxs-lookup"><span data-stu-id="70fe8-105">The Java client enables connecting to an ASP.NET Core SignalR server from Java code, including Android apps.</span></span> <span data-ttu-id="70fe8-106">Podobnie jak [klient JavaScript](xref:signalr/javascript-client) i [Klient platformy .NET](xref:signalr/dotnet-client), klient Java umożliwia odbieranie i wysyłanie komunikatów do centrum w czasie rzeczywistym.</span><span class="sxs-lookup"><span data-stu-id="70fe8-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="70fe8-107">Klient Java jest dostępny w ASP.NET Core 2,2 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="70fe8-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="70fe8-108">Przykładowa aplikacja konsoli Java, do której odwołuje się ten artykuł, używa SignalR klienta Java.</span><span class="sxs-lookup"><span data-stu-id="70fe8-108">The sample Java console app referenced in this article uses the SignalR Java client.</span></span>

<span data-ttu-id="70fe8-109">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="70fe8-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-java-client-package"></a><span data-ttu-id="70fe8-110">Zainstaluj SignalR pakiet klienta Java</span><span class="sxs-lookup"><span data-stu-id="70fe8-110">Install the SignalR Java client package</span></span>

<span data-ttu-id="70fe8-111">*Sygnał-1.0.0* plik JAR umożliwia klientom łączenie się z SignalR koncentratorami.</span><span class="sxs-lookup"><span data-stu-id="70fe8-111">The *signalr-1.0.0* JAR file allows clients to connect to SignalR hubs.</span></span> <span data-ttu-id="70fe8-112">Aby znaleźć najnowszy numer wersji pliku JAR, zobacz [wyniki wyszukiwania Maven](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span><span class="sxs-lookup"><span data-stu-id="70fe8-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span></span>

<span data-ttu-id="70fe8-113">Jeśli używasz Gradle, Dodaj następujący wiersz do `dependencies` sekcji pliku *Build. Gradle* :</span><span class="sxs-lookup"><span data-stu-id="70fe8-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

<span data-ttu-id="70fe8-114">Jeśli używasz Maven, Dodaj następujące wiersze wewnątrz `<dependencies>` elementu *pom.xml* pliku:</span><span class="sxs-lookup"><span data-stu-id="70fe8-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="70fe8-115">Nawiązywanie połączenia z centrum</span><span class="sxs-lookup"><span data-stu-id="70fe8-115">Connect to a hub</span></span>

<span data-ttu-id="70fe8-116">Aby ustanowić `HubConnection` , `HubConnectionBuilder` należy użyć.</span><span class="sxs-lookup"><span data-stu-id="70fe8-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="70fe8-117">Podczas tworzenia połączenia można skonfigurować adres URL i poziom dziennika centrum.</span><span class="sxs-lookup"><span data-stu-id="70fe8-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="70fe8-118">Skonfiguruj wszystkie wymagane opcje, wywołując dowolną `HubConnectionBuilder` metodę przed `build` .</span><span class="sxs-lookup"><span data-stu-id="70fe8-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="70fe8-119">Rozpocznij połączenie z usługą `start` .</span><span class="sxs-lookup"><span data-stu-id="70fe8-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="70fe8-120">Wywoływanie metod centrów z klienta</span><span class="sxs-lookup"><span data-stu-id="70fe8-120">Call hub methods from client</span></span>

<span data-ttu-id="70fe8-121">Wywołanie wywołania `send` metody centrum.</span><span class="sxs-lookup"><span data-stu-id="70fe8-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="70fe8-122">Przekaż nazwę metody Hub i wszystkie argumenty zdefiniowane w metodzie Hub do `send` .</span><span class="sxs-lookup"><span data-stu-id="70fe8-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> <span data-ttu-id="70fe8-123">Wywoływanie metod centrów z poziomu klienta jest obsługiwane tylko w przypadku korzystania z usługi platformy Azure SignalR w trybie *domyślnym* .</span><span class="sxs-lookup"><span data-stu-id="70fe8-123">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="70fe8-124">Aby uzyskać więcej informacji, zobacz [często zadawane pytania (repozytorium Azure-sygnalizujące GitHub)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="70fe8-124">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="70fe8-125">Wywoływanie metod klienta z centrum</span><span class="sxs-lookup"><span data-stu-id="70fe8-125">Call client methods from hub</span></span>

<span data-ttu-id="70fe8-126">Służy `hubConnection.on` do definiowania metod na kliencie, które mogą być wywoływane przez centrum.</span><span class="sxs-lookup"><span data-stu-id="70fe8-126">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="70fe8-127">Zdefiniuj metody po kompilacji, ale przed rozpoczęciem połączenia.</span><span class="sxs-lookup"><span data-stu-id="70fe8-127">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a><span data-ttu-id="70fe8-128">Dodawanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="70fe8-128">Add logging</span></span>

<span data-ttu-id="70fe8-129">SignalRKlient Java używa biblioteki [SLF4J](https://www.slf4j.org/) do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="70fe8-129">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="70fe8-130">Jest to interfejs API rejestrowania wysokiego poziomu, który umożliwia użytkownikom biblioteki wybranie własnych implementacji rejestrowania przez nałożenie określonej zależności rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="70fe8-130">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="70fe8-131">Poniższy fragment kodu przedstawia sposób korzystania z programu `java.util.logging` z SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="70fe8-131">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="70fe8-132">Jeśli nie skonfigurujesz rejestrowania w zależnościach, SLF4J ładuje domyślny Rejestrator braku operacji z następującym komunikatem ostrzegawczym:</span><span class="sxs-lookup"><span data-stu-id="70fe8-132">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="70fe8-133">Można je bezpiecznie zignorować.</span><span class="sxs-lookup"><span data-stu-id="70fe8-133">This can safely be ignored.</span></span>

## <a name="android-development-notes"></a><span data-ttu-id="70fe8-134">Uwagi dotyczące programowania dla systemu Android</span><span class="sxs-lookup"><span data-stu-id="70fe8-134">Android development notes</span></span>

<span data-ttu-id="70fe8-135">W odniesieniu do Android SDK zgodności dla SignalR funkcji klienta należy wziąć pod uwagę następujące elementy podczas określania docelowej wersji Android SDK:</span><span class="sxs-lookup"><span data-stu-id="70fe8-135">With regards to Android SDK compatibility for the SignalR client features, consider the following items when specifying your target Android SDK version:</span></span>

* <span data-ttu-id="70fe8-136">SignalRKlient Java zostanie uruchomiony na poziomie interfejsu API systemu Android w wersji 16 lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="70fe8-136">The SignalR Java Client will run on Android API Level 16 and later.</span></span>
* <span data-ttu-id="70fe8-137">Połączenie za pośrednictwem SignalR usługi platformy Azure wymaga poziomu interfejsu API systemu Android 20 i nowszego, ponieważ [ SignalR usługa platformy Azure](/azure/azure-signalr/signalr-overview) wymaga protokołu TLS 1,2 i nie obsługuje mechanizmów szyfrowania opartych na algorytmie SHA-1.</span><span class="sxs-lookup"><span data-stu-id="70fe8-137">Connecting through the Azure SignalR Service will require Android API Level 20 and later because the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) requires TLS 1.2 and doesn't support SHA-1-based cipher suites.</span></span> <span data-ttu-id="70fe8-138">[Dodano obsługę szyfrowania SHA-256 (i powyżej)](https://developer.android.com/reference/javax/net/ssl/SSLSocket) w systemie Android na poziomie interfejsu API 20.</span><span class="sxs-lookup"><span data-stu-id="70fe8-138">Android [added support for SHA-256 (and above) cipher suites](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API Level 20.</span></span>

## <a name="configure-bearer-token-authentication"></a><span data-ttu-id="70fe8-139">Konfigurowanie uwierzytelniania tokenów okaziciela</span><span class="sxs-lookup"><span data-stu-id="70fe8-139">Configure bearer token authentication</span></span>

<span data-ttu-id="70fe8-140">W SignalR kliencie Java można skonfigurować token okaziciela do użycia na potrzeby uwierzytelniania, dostarczając "fabrykę tokenów dostępu" do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="70fe8-140">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an "access token factory" to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="70fe8-141">Użyj [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , aby dostarczyć [RxJava](https://github.com/ReactiveX/RxJava) [jeden \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="70fe8-141">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="70fe8-142">Wywołanie metody [Single. Ustąp](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)umożliwia zapisanie logiki w celu utworzenia tokenów dostępu dla klienta.</span><span class="sxs-lookup"><span data-stu-id="70fe8-142">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

::: moniker range=">= aspnetcore-5.0"

### <a name="passing-class-information-in-java"></a><span data-ttu-id="70fe8-143">Przekazywanie informacji o klasie w języku Java</span><span class="sxs-lookup"><span data-stu-id="70fe8-143">Passing Class information in Java</span></span>

<span data-ttu-id="70fe8-144">Podczas wywoływania `on` , `invoke` , lub `stream` metod `HubConnection` w kliencie Java, użytkownicy powinni przekazać `Type` obiekt, a nie `Class<?>` obiekt, aby opisać wszelkie ogólne `Object` przekazane do metody.</span><span class="sxs-lookup"><span data-stu-id="70fe8-144">When calling the `on`, `invoke`, or `stream` methods of `HubConnection` in the Java client, users should pass a `Type` object rather than a `Class<?>` object to describe any generic `Object` passed to the method.</span></span> <span data-ttu-id="70fe8-145">`Type`Można uzyskać za pomocą podanej `TypeReference` klasy.</span><span class="sxs-lookup"><span data-stu-id="70fe8-145">A `Type` can be acquired using the provided `TypeReference` class.</span></span> <span data-ttu-id="70fe8-146">Na przykład przy użyciu niestandardowej klasy generycznej o nazwie `Foo<T>` , poniższy kod pobiera `Type` :</span><span class="sxs-lookup"><span data-stu-id="70fe8-146">For example, using a custom generic class named `Foo<T>`, the following code gets the `Type`:</span></span>

```java
Type fooType = new TypeReference<Foo<String>>() { }).getType();
```

<span data-ttu-id="70fe8-147">W przypadku elementów innych niż ogólne, takich jak elementy podstawowe lub inne niesparametryzowane typy, takie jak `String` , można po prostu użyć wbudowanej wartości `.class` .</span><span class="sxs-lookup"><span data-stu-id="70fe8-147">For non-generics, such as primitives or other non-parameterized types like `String`, you can simply use the built-in `.class`.</span></span>

<span data-ttu-id="70fe8-148">Podczas wywoływania jednej z tych metod z co najmniej jednym typem obiektu należy użyć składni generycznej podczas wywoływania metody.</span><span class="sxs-lookup"><span data-stu-id="70fe8-148">When calling one of these methods with one or more object types, use the generics syntax when invoking the method.</span></span> <span data-ttu-id="70fe8-149">Na przykład podczas rejestrowania `on` procedury obsługi dla metody o nazwie `func` , która przyjmuje jako argumenty ciąg i `Foo<String>` obiekt, użyj następującego kodu, aby ustawić akcję do drukowania argumentów:</span><span class="sxs-lookup"><span data-stu-id="70fe8-149">For example, when registering an `on` handler for a method named `func`, which takes as arguments a String and a `Foo<String>` object, use the following code to set an action to print the arguments:</span></span>

```java
hubConnection.<String, Foo<String>>on("func", (param1, param2) ->{
    System.out.println(param1);
    System.out.println(param2);
}, String.class, fooType);
```

<span data-ttu-id="70fe8-150">Ta konwencja jest konieczna, ponieważ nie można pobrać pełnych informacji o typach złożonych przy użyciu `Object.getClass` metody z powodu usunięcia typu w języku Java.</span><span class="sxs-lookup"><span data-stu-id="70fe8-150">This convention is necessary because we can not retrieve complete information about complex types with the `Object.getClass` method due to type erasure in Java.</span></span> <span data-ttu-id="70fe8-151">Na przykład wywołanie metody `getClass` `ArrayList<String>` nie zwróci `Class<ArrayList<String>>` , ale raczej nie `Class<ArrayList>` daje deserializacji wystarczającej ilości informacji do poprawnego deserializacji komunikatu przychodzącego.</span><span class="sxs-lookup"><span data-stu-id="70fe8-151">For example, calling `getClass` on an `ArrayList<String>` would not return `Class<ArrayList<String>>`, but rather `Class<ArrayList>`, which does not give the deserializer enough information to correctly deserialize an incoming message.</span></span> <span data-ttu-id="70fe8-152">Ten sam element ma wartość true w przypadku obiektów niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="70fe8-152">The same is true for custom objects.</span></span>

::: moniker-end

## <a name="known-limitations"></a><span data-ttu-id="70fe8-153">Znane ograniczenia</span><span class="sxs-lookup"><span data-stu-id="70fe8-153">Known limitations</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="70fe8-154">Transport zdarzeń powrotu i przesyłania do serwera nie jest obsługiwany.</span><span class="sxs-lookup"><span data-stu-id="70fe8-154">Transport fallback and the Server Sent Events transport aren't supported.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* <span data-ttu-id="70fe8-155">Transport zdarzeń powrotu i przesyłania do serwera nie jest obsługiwany.</span><span class="sxs-lookup"><span data-stu-id="70fe8-155">Transport fallback and the Server Sent Events transport aren't supported.</span></span>
* <span data-ttu-id="70fe8-156">Obsługiwany jest tylko protokół JSON.</span><span class="sxs-lookup"><span data-stu-id="70fe8-156">Only the JSON protocol is supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="70fe8-157">Obsługiwany jest tylko protokół JSON.</span><span class="sxs-lookup"><span data-stu-id="70fe8-157">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="70fe8-158">Obsługiwany jest tylko transport gniazd WebSockets.</span><span class="sxs-lookup"><span data-stu-id="70fe8-158">Only the WebSockets transport is supported.</span></span>
* <span data-ttu-id="70fe8-159">Przesyłanie strumieniowe nie jest jeszcze obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="70fe8-159">Streaming isn't supported yet.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="70fe8-160">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="70fe8-160">Additional resources</span></span>

* [<span data-ttu-id="70fe8-161">Dokumentacja interfejsów API języka Java</span><span class="sxs-lookup"><span data-stu-id="70fe8-161">Java API reference</span></span>](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [<span data-ttu-id="70fe8-162">SignalRDokumentacja bezserwerowa usługi platformy Azure</span><span class="sxs-lookup"><span data-stu-id="70fe8-162">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
