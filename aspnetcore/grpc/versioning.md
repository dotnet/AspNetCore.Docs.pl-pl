---
title: Przechowywanie wersji usług gRPC
author: jamesnk
description: Dowiedz się, jak w wersji gRPC Services.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
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
uid: grpc/versioning
ms.openlocfilehash: 079cca8a7d47897827c314b82136e9eb10b7a516
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633958"
---
# <a name="versioning-grpc-services"></a><span data-ttu-id="f787d-103">Przechowywanie wersji usług gRPC</span><span class="sxs-lookup"><span data-stu-id="f787d-103">Versioning gRPC services</span></span>

<span data-ttu-id="f787d-104">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="f787d-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="f787d-105">Nowe funkcje dodane do aplikacji mogą wymagać zmiany usług gRPC Services na potrzeby klientów, czasami w nieoczekiwany sposób.</span><span class="sxs-lookup"><span data-stu-id="f787d-105">New features added to an app can require gRPC services provided to clients to change, sometimes in unexpected and breaking ways.</span></span> <span data-ttu-id="f787d-106">Gdy usługi gRPC Services zmienią się:</span><span class="sxs-lookup"><span data-stu-id="f787d-106">When gRPC services change:</span></span>

* <span data-ttu-id="f787d-107">Należy zwrócić uwagę na to, jak zmiany wpływają na klientów.</span><span class="sxs-lookup"><span data-stu-id="f787d-107">Consideration should be given on how changes impact clients.</span></span>
* <span data-ttu-id="f787d-108">Należy zaimplementować strategię obsługi wersji, aby obsługiwać zmiany.</span><span class="sxs-lookup"><span data-stu-id="f787d-108">A versioning strategy to support changes should be implemented.</span></span>

## <a name="backwards-compatibility"></a><span data-ttu-id="f787d-109">Zgodność z poprzednimi wersjami</span><span class="sxs-lookup"><span data-stu-id="f787d-109">Backwards compatibility</span></span>

<span data-ttu-id="f787d-110">Protokół gRPC jest przeznaczony do obsługi usług, które zmieniają się w miarę upływu czasu.</span><span class="sxs-lookup"><span data-stu-id="f787d-110">The gRPC protocol is designed to support services that change over time.</span></span> <span data-ttu-id="f787d-111">Ogólnie rzecz biorąc, dodatki do usług gRPC i metod są nieistotne.</span><span class="sxs-lookup"><span data-stu-id="f787d-111">Generally, additions to gRPC services and methods are non-breaking.</span></span> <span data-ttu-id="f787d-112">Niekrytyczne zmiany umożliwiają istniejącym klientom kontynuowanie pracy bez zmian.</span><span class="sxs-lookup"><span data-stu-id="f787d-112">Non-breaking changes allow existing clients to continue working without changes.</span></span> <span data-ttu-id="f787d-113">Zmiana lub usunięcie usług gRPC powoduje przerwanie zmian.</span><span class="sxs-lookup"><span data-stu-id="f787d-113">Changing or deleting gRPC services are breaking changes.</span></span> <span data-ttu-id="f787d-114">Gdy usługi gRPC mają istotne zmiany, klienci korzystający z tej usługi muszą zostać zaktualizowani i wdrożoni ponownie.</span><span class="sxs-lookup"><span data-stu-id="f787d-114">When gRPC services have breaking changes, clients using that service have to be updated and redeployed.</span></span>

<span data-ttu-id="f787d-115">Wprowadzanie nieistotnych zmian do usługi ma wiele korzyści:</span><span class="sxs-lookup"><span data-stu-id="f787d-115">Making non-breaking changes to a service has a number of benefits:</span></span>

* <span data-ttu-id="f787d-116">Istniejący klienci będą nadal działać.</span><span class="sxs-lookup"><span data-stu-id="f787d-116">Existing clients continue to run.</span></span>
* <span data-ttu-id="f787d-117">Zapobiega pracy związanym z powiadamianiem klientów o istotnych zmianach i aktualizowanie ich.</span><span class="sxs-lookup"><span data-stu-id="f787d-117">Avoids work involved with notifying clients of breaking changes, and updating them.</span></span>
* <span data-ttu-id="f787d-118">Tylko jedna wersja usługi musi być udokumentowana i utrzymywana.</span><span class="sxs-lookup"><span data-stu-id="f787d-118">Only one version of the service needs to be documented and maintained.</span></span>

### <a name="non-breaking-changes"></a><span data-ttu-id="f787d-119">Niekrytyczne zmiany</span><span class="sxs-lookup"><span data-stu-id="f787d-119">Non-breaking changes</span></span>

<span data-ttu-id="f787d-120">Te zmiany nie są przerywane na poziomie protokołu gRPC i pliku binarnego platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="f787d-120">These changes are non-breaking at a gRPC protocol level and .NET binary level.</span></span>

* <span data-ttu-id="f787d-121">**Dodawanie nowej usługi**</span><span class="sxs-lookup"><span data-stu-id="f787d-121">**Adding a new service**</span></span>
* <span data-ttu-id="f787d-122">**Dodawanie nowej metody do usługi**</span><span class="sxs-lookup"><span data-stu-id="f787d-122">**Adding a new method to a service**</span></span>
* <span data-ttu-id="f787d-123">**Dodawanie pola do komunikatu żądania** — pola dodawane do komunikatu żądania są deserializowane przy użyciu [wartości domyślnej](https://developers.google.com/protocol-buffers/docs/proto3#default) na serwerze, gdy nie jest ustawiony.</span><span class="sxs-lookup"><span data-stu-id="f787d-123">**Adding a field to a request message** - Fields added to a request message are deserialized with the [default value](https://developers.google.com/protocol-buffers/docs/proto3#default) on the server when not set.</span></span> <span data-ttu-id="f787d-124">Aby nastąpić nieprzerwaną zmianę, usługa musi się powieść, jeśli nowe pole nie zostanie ustawione przez starszych klientów.</span><span class="sxs-lookup"><span data-stu-id="f787d-124">To be a non-breaking change, the service must succeed when the new field isn't set by older clients.</span></span>
* <span data-ttu-id="f787d-125">**Dodawanie pola do komunikatu odpowiedzi** — pola dodane do komunikatu odpowiedzi są deserializowane do kolekcji [nieznanych pól](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) na komputerze klienckim.</span><span class="sxs-lookup"><span data-stu-id="f787d-125">**Adding a field to a response message** - Fields added to a response message are deserialized into the message's [unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) collection on the client.</span></span>
* <span data-ttu-id="f787d-126">**Dodawanie wartości do** wyliczenia-wyliczenia jest serializowane jako wartość liczbowa.</span><span class="sxs-lookup"><span data-stu-id="f787d-126">**Adding a value to an enum** - Enums are serialized as a numeric value.</span></span> <span data-ttu-id="f787d-127">Nowe wartości wyliczeniowe są deserializowane na kliencie do wartości wyliczenia bez nazwy wyliczenia.</span><span class="sxs-lookup"><span data-stu-id="f787d-127">New enum values are deserialized on the client to the enum value without an enum name.</span></span> <span data-ttu-id="f787d-128">Aby mieć nieistotną zmianę, starsze komputery klienckie muszą działać poprawnie, gdy otrzymuje nową wartość enum.</span><span class="sxs-lookup"><span data-stu-id="f787d-128">To be a non-breaking change, older clients must run correctly when receiving the new enum value.</span></span>

### <a name="binary-breaking-changes"></a><span data-ttu-id="f787d-129">Zmiany kodu binarnego</span><span class="sxs-lookup"><span data-stu-id="f787d-129">Binary breaking changes</span></span>

<span data-ttu-id="f787d-130">Następujące zmiany nie są rozrywane na poziomie protokołu gRPC, ale klient należy zaktualizować w przypadku uaktualnienia do najnowszej wersji kontraktu *. proto* lub zestawu .NET klienta.</span><span class="sxs-lookup"><span data-stu-id="f787d-130">The following changes are non-breaking at a gRPC protocol level, but the client needs to be updated if it upgrades to the latest *.proto* contract or client .NET assembly.</span></span> <span data-ttu-id="f787d-131">Zgodność binarna jest ważna, jeśli planujesz opublikowanie biblioteki gRPC w programie NuGet.</span><span class="sxs-lookup"><span data-stu-id="f787d-131">Binary compatibility is important if you plan to publish a gRPC library to NuGet.</span></span>

* <span data-ttu-id="f787d-132">**Usuwanie wartości pól** z usuniętego pola jest deserializowane do [nieznanych pól](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)komunikatu.</span><span class="sxs-lookup"><span data-stu-id="f787d-132">**Removing a field** - Values from a removed field are deserialized to a message's [unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns).</span></span> <span data-ttu-id="f787d-133">Nie jest to gRPCa zmiana protokołu, ale klient należy zaktualizować w przypadku uaktualnienia do najnowszego kontraktu.</span><span class="sxs-lookup"><span data-stu-id="f787d-133">This isn't a gRPC protocol breaking change, but the client needs to be updated if it upgrades to the latest contract.</span></span> <span data-ttu-id="f787d-134">Należy pamiętać, że usunięty numer pola nie jest przypadkowo ponownie używany w przyszłości.</span><span class="sxs-lookup"><span data-stu-id="f787d-134">It's important that a removed field number isn't accidentally reused in the future.</span></span> <span data-ttu-id="f787d-135">Aby się upewnić, że to się nie dzieje, określ usunięte numery pól i nazwy w komunikacie przy użyciu [zastrzeżonego](https://developers.google.com/protocol-buffers/docs/proto3#reserved) słowa kluczowego protobuf.</span><span class="sxs-lookup"><span data-stu-id="f787d-135">To ensure this doesn't happen, specify deleted field numbers and names on the message using Protobuf's [reserved](https://developers.google.com/protocol-buffers/docs/proto3#reserved) keyword.</span></span>
* <span data-ttu-id="f787d-136">Zmiana **nazwy** komunikatów nie jest zazwyczaj wysyłana w sieci, więc nie jest to gRPCa.</span><span class="sxs-lookup"><span data-stu-id="f787d-136">**Renaming a message** - Message names aren't typically sent on the network, so this isn't a gRPC protocol breaking change.</span></span> <span data-ttu-id="f787d-137">Po uaktualnieniu do najnowszej kontraktu klient musi zostać zaktualizowany.</span><span class="sxs-lookup"><span data-stu-id="f787d-137">The client will need to be updated if it upgrades to the latest contract.</span></span> <span data-ttu-id="f787d-138">Jedną z sytuacji, w której nazwy komunikatów **są** wysyłane w sieci, są z [dowolnymi](https://developers.google.com/protocol-buffers/docs/proto3#any) polami, gdy nazwa komunikatu jest używana do identyfikowania typu wiadomości.</span><span class="sxs-lookup"><span data-stu-id="f787d-138">One situation where message names **are** sent on the network is with [Any](https://developers.google.com/protocol-buffers/docs/proto3#any) fields, when the message name is used to identify the message type.</span></span>
* <span data-ttu-id="f787d-139">**Zmiana csharp_namespace** -zmiana zmieni `csharp_namespace` przestrzeń nazw wygenerowanych typów .NET.</span><span class="sxs-lookup"><span data-stu-id="f787d-139">**Changing csharp_namespace** - Changing `csharp_namespace` will change the namespace of generated .NET types.</span></span> <span data-ttu-id="f787d-140">Nie jest to gRPCa zmiana protokołu, ale klient należy zaktualizować w przypadku uaktualnienia do najnowszego kontraktu.</span><span class="sxs-lookup"><span data-stu-id="f787d-140">This isn't a gRPC protocol breaking change, but the client needs to be updated if it upgrades to the latest contract.</span></span>

### <a name="protocol-breaking-changes"></a><span data-ttu-id="f787d-141">Zmiany podczas łamania protokołu</span><span class="sxs-lookup"><span data-stu-id="f787d-141">Protocol breaking changes</span></span>

<span data-ttu-id="f787d-142">Poniżej przedstawiono następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="f787d-142">The following items are protocol and binary breaking changes:</span></span>

* <span data-ttu-id="f787d-143">**Zmiana nazwy pola** — z zawartością protobuf, nazwy pól są używane tylko w wygenerowanym kodzie.</span><span class="sxs-lookup"><span data-stu-id="f787d-143">**Renaming a field** - With Protobuf content, the field names are only used in generated code.</span></span> <span data-ttu-id="f787d-144">Numer pola służy do identyfikowania pól w sieci.</span><span class="sxs-lookup"><span data-stu-id="f787d-144">The field number is used to identify fields on the network.</span></span> <span data-ttu-id="f787d-145">Zmiana nazwy pola nie jest zmianą protokołu dla protobuf.</span><span class="sxs-lookup"><span data-stu-id="f787d-145">Renaming a field isn't a protocol breaking change for Protobuf.</span></span> <span data-ttu-id="f787d-146">Jeśli jednak serwer używa zawartości JSON, zmiana nazwy pola jest istotną zmianą.</span><span class="sxs-lookup"><span data-stu-id="f787d-146">However, if a server is using JSON content then renaming a field is a breaking change.</span></span>
* <span data-ttu-id="f787d-147">**Zmiana typu danych pola** — zmiana typu danych pola na [niezgodny typ](https://developers.google.com/protocol-buffers/docs/proto3#updating) spowoduje błędy podczas deserializacji komunikatu.</span><span class="sxs-lookup"><span data-stu-id="f787d-147">**Changing a field data type** - Changing a field's data type to an [incompatible type](https://developers.google.com/protocol-buffers/docs/proto3#updating) will cause errors when deserializing the message.</span></span> <span data-ttu-id="f787d-148">Nawet jeśli nowy typ danych jest zgodny, prawdopodobnie klient musi zostać zaktualizowany do obsługi nowego typu w przypadku uaktualnienia do najnowszego kontraktu.</span><span class="sxs-lookup"><span data-stu-id="f787d-148">Even if the new data type is compatible, it's likely the client needs to be updated to support the new type if it upgrades to the latest contract.</span></span>
* <span data-ttu-id="f787d-149">**Zmiana numeru pola** — przy użyciu ładunków protobuf numer pola służy do identyfikowania pól w sieci.</span><span class="sxs-lookup"><span data-stu-id="f787d-149">**Changing a field number** - With Protobuf payloads, the field number is used to identify fields on the network.</span></span>
* <span data-ttu-id="f787d-150">**Zmiana nazwy pakietu, usługi lub metody** — gRPC używa nazwy pakietu, nazwy usługi i nazwy metody do KOMPILOWANIA adresu URL.</span><span class="sxs-lookup"><span data-stu-id="f787d-150">**Renaming a package, service or method** - gRPC uses the package name, service name, and method name to build the URL.</span></span> <span data-ttu-id="f787d-151">Klient Pobiera stan *NIEZAimplementowany* z serwera.</span><span class="sxs-lookup"><span data-stu-id="f787d-151">The client gets an *UNIMPLEMENTED* status from the server.</span></span>
* <span data-ttu-id="f787d-152">**Usuwanie usługi lub metody** — klient Pobiera stan *niezaimplementowany* z serwera podczas wywoływania usuniętej metody.</span><span class="sxs-lookup"><span data-stu-id="f787d-152">**Removing a service or method** - The client gets an *UNIMPLEMENTED* status from the server when calling the removed method.</span></span>

### <a name="behavior-breaking-changes"></a><span data-ttu-id="f787d-153">Zmiany powodujące przerwanie działania</span><span class="sxs-lookup"><span data-stu-id="f787d-153">Behavior breaking changes</span></span>

<span data-ttu-id="f787d-154">Podczas wprowadzania nieistotnych zmian należy również rozważyć, czy starsze komputery klienckie mogą kontynuować pracę z nowym zachowaniem usługi.</span><span class="sxs-lookup"><span data-stu-id="f787d-154">When making non-breaking changes, you must also consider whether older clients can continue working with the new service behavior.</span></span> <span data-ttu-id="f787d-155">Na przykład Dodaj nowe pole do komunikatu żądania:</span><span class="sxs-lookup"><span data-stu-id="f787d-155">For example, adding a new field to a request message:</span></span>

* <span data-ttu-id="f787d-156">Nie jest to zmiana podziału protokołu.</span><span class="sxs-lookup"><span data-stu-id="f787d-156">Isn't a protocol breaking change.</span></span>
* <span data-ttu-id="f787d-157">Zwrócenie stanu błędu na serwerze, jeśli nowe pole nie zostało ustawione sprawia, że jest to istotna zmiana dla starych klientów.</span><span class="sxs-lookup"><span data-stu-id="f787d-157">Returning an error status on the server if the new field isn't set makes it a breaking change for old clients.</span></span>

<span data-ttu-id="f787d-158">Zgodność z zachowaniem jest określana na podstawie kodu specyficznego dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f787d-158">Behavior compatibility is determined by your app-specific code.</span></span>

## <a name="version-number-services"></a><span data-ttu-id="f787d-159">Usługi numeru wersji</span><span class="sxs-lookup"><span data-stu-id="f787d-159">Version number services</span></span>

<span data-ttu-id="f787d-160">Usługi powinny dążyć do zachowania zgodności z poprzednimi klientami.</span><span class="sxs-lookup"><span data-stu-id="f787d-160">Services should strive to remain backwards compatible with old clients.</span></span> <span data-ttu-id="f787d-161">Ostatecznie zmiany w aplikacji mogą wymagać przerwania zmian.</span><span class="sxs-lookup"><span data-stu-id="f787d-161">Eventually changes to your app may require breaking changes.</span></span> <span data-ttu-id="f787d-162">Przerywanie starych klientów i wymuszanie ich aktualizacji wraz z usługą nie jest dobrym doświadczeniem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="f787d-162">Breaking old clients and forcing them to be updated along with your service isn't a good user experience.</span></span> <span data-ttu-id="f787d-163">Sposób zapewnienia zgodności z poprzednimi wersjami podczas wprowadzania istotnych zmian polega na opublikowaniu wielu wersji usługi.</span><span class="sxs-lookup"><span data-stu-id="f787d-163">A way to maintain backwards compatibility while making breaking changes is to publish multiple versions of a service.</span></span>

<span data-ttu-id="f787d-164">gRPC obsługuje opcjonalny specyfikator [pakietu](https://developers.google.com/protocol-buffers/docs/proto3#packages) , który działa podobnie jak przestrzeń nazw platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="f787d-164">gRPC supports an optional [package](https://developers.google.com/protocol-buffers/docs/proto3#packages) specifier, which functions much like a .NET namespace.</span></span> <span data-ttu-id="f787d-165">W rzeczywistości `package` zostanie użyta jako przestrzeń nazw .NET dla wygenerowanych typów .NET, jeśli `option csharp_namespace` nie jest ustawiona w pliku *. proto* .</span><span class="sxs-lookup"><span data-stu-id="f787d-165">In fact, the `package` will be used as the .NET namespace for generated .NET types if `option csharp_namespace` is not set in the *.proto* file.</span></span> <span data-ttu-id="f787d-166">Pakiet może służyć do określania numeru wersji usługi i jej komunikatów:</span><span class="sxs-lookup"><span data-stu-id="f787d-166">The package can be used to specify a version number for your service and its messages:</span></span>

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

<span data-ttu-id="f787d-167">Nazwa pakietu jest połączona z nazwą usługi, aby zidentyfikować adres usługi.</span><span class="sxs-lookup"><span data-stu-id="f787d-167">The package name is combined with the service name to identify a service address.</span></span> <span data-ttu-id="f787d-168">Adres usługi umożliwia obsługiwanie wielu wersji usługi po stronie:</span><span class="sxs-lookup"><span data-stu-id="f787d-168">A service address allows multiple versions of a service to be hosted side-by-side:</span></span>

* `greet.v1.Greeter`
* `greet.v2.Greeter`

<span data-ttu-id="f787d-169">Implementacje usługi z wersjami są zarejestrowane w *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f787d-169">Implementations of the versioned service are registered in *Startup.cs*:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

<span data-ttu-id="f787d-170">Dołączenie numeru wersji w nazwie pakietu daje możliwość opublikowania w wersji *2* usługi z uwzględnieniem istotnych zmian, przy jednoczesnym dalszym obsłudze starszych klientów, którzy wywołują wersję *V1* .</span><span class="sxs-lookup"><span data-stu-id="f787d-170">Including a version number in the package name gives you the opportunity to publish a *v2* version of your service with breaking changes, while continuing to support older clients who call the *v1* version.</span></span> <span data-ttu-id="f787d-171">Gdy klienci zostali zaktualizowani do korzystania z usługi w *wersji 2* , możesz wybrać opcję usunięcia starej wersji.</span><span class="sxs-lookup"><span data-stu-id="f787d-171">Once clients have updated to use the *v2* service, you can choose to remove the old version.</span></span> <span data-ttu-id="f787d-172">Planując Publikowanie wielu wersji usługi:</span><span class="sxs-lookup"><span data-stu-id="f787d-172">When planning to publish multiple versions of a service:</span></span>

* <span data-ttu-id="f787d-173">Unikaj znaczących zmian, jeśli jest to uzasadnione.</span><span class="sxs-lookup"><span data-stu-id="f787d-173">Avoid breaking changes if reasonable.</span></span>
* <span data-ttu-id="f787d-174">Nie Aktualizuj numeru wersji, chyba że wprowadzasz istotne zmiany.</span><span class="sxs-lookup"><span data-stu-id="f787d-174">Don't update the version number unless making breaking changes.</span></span>
* <span data-ttu-id="f787d-175">Należy zaktualizować numer wersji po wprowadzeniu zmian.</span><span class="sxs-lookup"><span data-stu-id="f787d-175">Do update the version number when you make breaking changes.</span></span>

<span data-ttu-id="f787d-176">Publikowanie wielu wersji usługi duplikuje ją.</span><span class="sxs-lookup"><span data-stu-id="f787d-176">Publishing multiple versions of a service duplicates it.</span></span> <span data-ttu-id="f787d-177">Aby zmniejszyć duplikowanie, rozważ przeniesienie logiki biznesowej z implementacji usługi do scentralizowanej lokalizacji, która może być ponownie używana przez stare i nowe implementacje:</span><span class="sxs-lookup"><span data-stu-id="f787d-177">To reduce duplication, consider moving business logic from the service implementations to a centralized location that can be reused by the old and new implementations:</span></span>

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

<span data-ttu-id="f787d-178">Usługi i komunikaty generowane z różnymi nazwami pakietów są **różnymi typami .NET**.</span><span class="sxs-lookup"><span data-stu-id="f787d-178">Services and messages generated with different package names are **different .NET types**.</span></span> <span data-ttu-id="f787d-179">Przeniesienie logiki biznesowej do scentralizowanej lokalizacji wymaga mapowania komunikatów do typów wspólnych.</span><span class="sxs-lookup"><span data-stu-id="f787d-179">Moving business logic to a centralized location requires mapping messages to common types.</span></span>
