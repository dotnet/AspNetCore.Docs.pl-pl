---
title: Tworzenie komunikatów protobuf dla aplikacji platformy .NET
author: jamesnk
description: Dowiedz się, jak tworzyć komunikaty protobuf dla aplikacji .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/protobuf
ms.openlocfilehash: 60af1add9ae2f8b2b94bc19b65667d7af91fb122
ms.sourcegitcommit: 7258e94cf60c16e5b6883138e5e68516751ead0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2020
ms.locfileid: "89102669"
---
# <a name="create-protobuf-messages-for-net-apps"></a><span data-ttu-id="12811-103">Tworzenie komunikatów protobuf dla aplikacji platformy .NET</span><span class="sxs-lookup"><span data-stu-id="12811-103">Create Protobuf messages for .NET apps</span></span>

<span data-ttu-id="12811-104">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk) i [Mark Rendle](https://twitter.com/markrendle)</span><span class="sxs-lookup"><span data-stu-id="12811-104">By [James Newton-King](https://twitter.com/jamesnk) and [Mark Rendle](https://twitter.com/markrendle)</span></span>

<span data-ttu-id="12811-105">gRPC używa [protobuf](https://developers.google.com/protocol-buffers) jako języka definicji interfejsu (IDL).</span><span class="sxs-lookup"><span data-stu-id="12811-105">gRPC uses [Protobuf](https://developers.google.com/protocol-buffers) as its Interface Definition Language (IDL).</span></span> <span data-ttu-id="12811-106">Protobuf IDL to niezależny od języka format służący do określania komunikatów wysyłanych i odbieranych przez usługi gRPC.</span><span class="sxs-lookup"><span data-stu-id="12811-106">Protobuf IDL is a language neutral format for specifying the messages sent and received by gRPC services.</span></span> <span data-ttu-id="12811-107">Komunikaty protobuf są zdefiniowane w `.proto` plikach.</span><span class="sxs-lookup"><span data-stu-id="12811-107">Protobuf messages are defined in `.proto` files.</span></span> <span data-ttu-id="12811-108">W tym dokumencie wyjaśniono, jak koncepcje protobuf są mapowane na platformę .NET.</span><span class="sxs-lookup"><span data-stu-id="12811-108">This document explains how Protobuf concepts map to .NET.</span></span>

## <a name="protobuf-messages"></a><span data-ttu-id="12811-109">Komunikaty Protobuf</span><span class="sxs-lookup"><span data-stu-id="12811-109">Protobuf messages</span></span>

<span data-ttu-id="12811-110">Komunikaty są głównym obiektem transferu danych w protobuf.</span><span class="sxs-lookup"><span data-stu-id="12811-110">Messages are the main data transfer object in Protobuf.</span></span> <span data-ttu-id="12811-111">Są one koncepcyjnie podobne do klas platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="12811-111">They are conceptually similar to .NET classes.</span></span>

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

<span data-ttu-id="12811-112">Poprzednia definicja komunikatu określa trzy pola jako pary nazwa-wartość.</span><span class="sxs-lookup"><span data-stu-id="12811-112">The preceding message definition specifies three fields as name-value pairs.</span></span> <span data-ttu-id="12811-113">Podobnie jak we właściwościach typu .NET, każde pole ma nazwę i typ.</span><span class="sxs-lookup"><span data-stu-id="12811-113">Like properties on .NET types, each field has a name and a type.</span></span> <span data-ttu-id="12811-114">Typ pola może być [typem wartości skalarnej protobuf](#scalar-value-types), np. `int32` lub innym komunikatem.</span><span class="sxs-lookup"><span data-stu-id="12811-114">The field type can be a [Protobuf scalar value type](#scalar-value-types), e.g. `int32`, or another message.</span></span>

<span data-ttu-id="12811-115">Oprócz nazwy, każde pole w definicji komunikatu ma unikatowy numer.</span><span class="sxs-lookup"><span data-stu-id="12811-115">In addition to a name, each field in the message definition has a unique number.</span></span> <span data-ttu-id="12811-116">Numery pól są używane do identyfikowania pól, gdy komunikat jest serializowany do protobuf.</span><span class="sxs-lookup"><span data-stu-id="12811-116">Field numbers are used to identify fields when the message is serialized to Protobuf.</span></span> <span data-ttu-id="12811-117">Serializowanie niewielkiej liczby jest szybsze niż Serializowanie całej nazwy pola.</span><span class="sxs-lookup"><span data-stu-id="12811-117">Serializing a small number is faster than serializing the entire field name.</span></span> <span data-ttu-id="12811-118">Ponieważ numery pól identyfikują pole, ważne jest, aby zachować ostrożność przy ich zmianie.</span><span class="sxs-lookup"><span data-stu-id="12811-118">Because field numbers identify a field it is important to take care when changing them.</span></span> <span data-ttu-id="12811-119">Aby uzyskać więcej informacji na temat zmieniania komunikatów protobuf, zobacz <xref:grpc/versioning> .</span><span class="sxs-lookup"><span data-stu-id="12811-119">For more information about changing Protobuf messages see <xref:grpc/versioning>.</span></span>

<span data-ttu-id="12811-120">Po skompilowaniu aplikacji narzędzia protobuf generują typy platformy .NET na podstawie `.proto` plików.</span><span class="sxs-lookup"><span data-stu-id="12811-120">When an app is built the Protobuf tooling generates .NET types from `.proto` files.</span></span> <span data-ttu-id="12811-121">`Person`Komunikat generuje klasę .NET:</span><span class="sxs-lookup"><span data-stu-id="12811-121">The `Person` message generates a .NET class:</span></span>

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

<span data-ttu-id="12811-122">Więcej informacji na temat komunikatów protobuf można znaleźć w [przewodniku po języku protobuf](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span><span class="sxs-lookup"><span data-stu-id="12811-122">For more information about Protobuf messages see the [Protobuf language guide](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span></span>

## <a name="scalar-value-types"></a><span data-ttu-id="12811-123">Typy wartości skalarnych</span><span class="sxs-lookup"><span data-stu-id="12811-123">Scalar Value Types</span></span>

<span data-ttu-id="12811-124">Protobuf obsługuje zakres natywnych typów wartości skalarnych.</span><span class="sxs-lookup"><span data-stu-id="12811-124">Protobuf supports a range of native scalar value types.</span></span> <span data-ttu-id="12811-125">W poniższej tabeli wymieniono wszystkie ich równoważne typy C#:</span><span class="sxs-lookup"><span data-stu-id="12811-125">The following table lists them all with their equivalent C# type:</span></span>

| <span data-ttu-id="12811-126">Typ protobuf</span><span class="sxs-lookup"><span data-stu-id="12811-126">Protobuf type</span></span> | <span data-ttu-id="12811-127">Typ C#</span><span class="sxs-lookup"><span data-stu-id="12811-127">C# type</span></span>      |
| ------------- | ------------ |
| `double`      | `double`     |
| `float`       | `float`      |
| `int32`       | `int`        |
| `int64`       | `long`       |
| `uint32`      | `uint`       |
| `uint64`      | `ulong`      |
| `sint32`      | `int`        |
| `sint64`      | `long`       |
| `fixed32`     | `uint`       |
| `fixed64`     | `ulong`      |
| `sfixed32`    | `int`        |
| `sfixed64`    | `long`       |
| `bool`        | `bool`       |
| `string`      | `string`     |
| `bytes`       | `ByteString` |

### <a name="dates-and-times"></a><span data-ttu-id="12811-128">Daty i godziny</span><span class="sxs-lookup"><span data-stu-id="12811-128">Dates and times</span></span>

<span data-ttu-id="12811-129">Natywne typy skalarne nie zapewniają wartości daty i godziny, równoważne z. SIECI <xref:System.DateTimeOffset> , <xref:System.DateTime> i <xref:System.TimeSpan> .</span><span class="sxs-lookup"><span data-stu-id="12811-129">The native scalar types don't provide for date and time values, equivalent to .NET's <xref:System.DateTimeOffset>, <xref:System.DateTime>, and <xref:System.TimeSpan>.</span></span> <span data-ttu-id="12811-130">Te typy można określić za pomocą niektórych rozszerzeń *znanych typów* protobuf.</span><span class="sxs-lookup"><span data-stu-id="12811-130">These types can be specified by using some of Protobuf's *Well-Known Types* extensions.</span></span> <span data-ttu-id="12811-131">Rozszerzenia te zapewniają obsługę generowania kodu i środowiska uruchomieniowego dla złożonych typów pól na obsługiwanych platformach.</span><span class="sxs-lookup"><span data-stu-id="12811-131">These extensions provide code generation and runtime support for complex field types across the supported platforms.</span></span>

<span data-ttu-id="12811-132">W poniższej tabeli przedstawiono typy daty i godziny:</span><span class="sxs-lookup"><span data-stu-id="12811-132">The following table shows the date and time types:</span></span>

| <span data-ttu-id="12811-133">Typ .NET</span><span class="sxs-lookup"><span data-stu-id="12811-133">.NET type</span></span>        | <span data-ttu-id="12811-134">Nieznany typ protobuf</span><span class="sxs-lookup"><span data-stu-id="12811-134">Protobuf Well-Known Type</span></span>    |
| ---------------- | --------------------------- |
| `DateTimeOffset` | `google.protobuf.Timestamp` |
| `DateTime`       | `google.protobuf.Timestamp` |
| `TimeSpan`       | `google.protobuf.Duration`  |

```protobuf  
syntax = "proto3"

import "google/protobuf/duration.proto";  
import "google/protobuf/timestamp.proto";

message Meeting {
    string subject = 1;
    google.protobuf.Timestamp start = 2;
    google.protobuf.Duration duration = 3;
}  
```

<span data-ttu-id="12811-135">Wygenerowane właściwości klasy C# nie są typami dat i godzin platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="12811-135">The generated properties in the C# class aren't the .NET date and time types.</span></span> <span data-ttu-id="12811-136">Właściwości używają `Timestamp` `Duration` klas i w `Google.Protobuf.WellKnownTypes` przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="12811-136">The properties use the `Timestamp` and `Duration` classes in the `Google.Protobuf.WellKnownTypes` namespace.</span></span> <span data-ttu-id="12811-137">Klasy te zapewniają metody konwersji do i z `DateTimeOffset` , `DateTime` i `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="12811-137">These classes provide methods for converting to and from `DateTimeOffset`, `DateTime`, and `TimeSpan`.</span></span>

```csharp
// Create Timestamp and Duration from .NET DateTimeOffset and TimeSpan.
var meeting = new Meeting
{
    Time = Timestamp.FromDateTimeOffset(meetingTime), // also FromDateTime()
    Duration = Duration.FromTimeSpan(meetingLength)
};

// Convert Timestamp and Duration to .NET DateTimeOffset and TimeSpan.
var time = meeting.Time.ToDateTimeOffset();
var duration = meeting.Duration?.ToTimeSpan();
```

> [!NOTE]
> <span data-ttu-id="12811-138">`Timestamp`Typ działa z czasem UTC.</span><span class="sxs-lookup"><span data-stu-id="12811-138">The `Timestamp` type works with UTC times.</span></span> <span data-ttu-id="12811-139">`DateTimeOffset` wartości zawsze mają przesunięcie zero, a `DateTime.Kind` Właściwość jest zawsze `DateTimeKind.Utc` .</span><span class="sxs-lookup"><span data-stu-id="12811-139">`DateTimeOffset` values always have an offset of zero, and the `DateTime.Kind` property is always `DateTimeKind.Utc`.</span></span>

### <a name="nullable-types"></a><span data-ttu-id="12811-140">Typy dopuszczające wartości null</span><span class="sxs-lookup"><span data-stu-id="12811-140">Nullable types</span></span>

<span data-ttu-id="12811-141">Generowanie kodu protobuf dla języka C# używa typów natywnych, takich jak `int` for `int32` .</span><span class="sxs-lookup"><span data-stu-id="12811-141">The Protobuf code generation for C# uses the native types, such as `int` for `int32`.</span></span> <span data-ttu-id="12811-142">Dlatego wartości są zawsze uwzględniane i nie mogą być `null` .</span><span class="sxs-lookup"><span data-stu-id="12811-142">So the values are always included and can't be `null`.</span></span>

<span data-ttu-id="12811-143">W przypadku wartości, które wymagają jawności, na przykład `null` używania `int?` w kodzie C#, dobrze znane typy protobuf obejmują otoki, które są kompilowane do typów dopuszczających wartość null.</span><span class="sxs-lookup"><span data-stu-id="12811-143">For values that require explicit `null`, such as using `int?` in C# code, Protobuf's Well-Known Types include wrappers that are compiled to nullable C# types.</span></span> <span data-ttu-id="12811-144">Aby ich użyć, zaimportuj `wrappers.proto` do `.proto` pliku, tak jak w poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="12811-144">To use them, import `wrappers.proto` into your `.proto` file, like the following code:</span></span>

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

<span data-ttu-id="12811-145">Protobuf używa typów platformy .NET dopuszczających wartości null, na przykład `int?` dla wygenerowanej właściwości wiadomości.</span><span class="sxs-lookup"><span data-stu-id="12811-145">Protobuf uses .NET nullable types, for example, `int?`, for the generated message property.</span></span>

<span data-ttu-id="12811-146">W poniższej tabeli przedstawiono pełną listę typów otoki z równoważnym typem języka C#:</span><span class="sxs-lookup"><span data-stu-id="12811-146">The following table shows the complete list of wrapper types with their equivalent C# type:</span></span>

| <span data-ttu-id="12811-147">Typ C#</span><span class="sxs-lookup"><span data-stu-id="12811-147">C# type</span></span>   | <span data-ttu-id="12811-148">Otoka dobrze znanego typu</span><span class="sxs-lookup"><span data-stu-id="12811-148">Well-Known Type wrapper</span></span>       |
| --------- | ----------------------------- |
| `bool?`   | `google.protobuf.BoolValue`   |
| `double?` | `google.protobuf.DoubleValue` |
| `float?`  | `google.protobuf.FloatValue`  |
| `int?`    | `google.protobuf.Int32Value`  |
| `long?`   | `google.protobuf.Int64Value`  |
| `uint?`   | `google.protobuf.UInt32Value` |
| `ulong?`  | `google.protobuf.UInt64Value` |

### <a name="decimals"></a><span data-ttu-id="12811-149">Miejsca dziesiętne</span><span class="sxs-lookup"><span data-stu-id="12811-149">Decimals</span></span>

<span data-ttu-id="12811-150">Protobuf nie obsługuje natywnie typu .NET `decimal` , tylko `double` i `float` .</span><span class="sxs-lookup"><span data-stu-id="12811-150">Protobuf doesn't natively support the .NET `decimal` type, just `double` and `float`.</span></span> <span data-ttu-id="12811-151">W projekcie protobuf istnieje trwająca dyskusja dotycząca możliwości dodawania standardowego typu dziesiętnego do dobrze znanych typów, z obsługą platformy dla języków i struktur, które je obsługują.</span><span class="sxs-lookup"><span data-stu-id="12811-151">There's an ongoing discussion in the Protobuf project about the possibility of adding a standard decimal type to the Well-Known Types, with platform support for languages and frameworks that support it.</span></span> <span data-ttu-id="12811-152">Nic nie zostało jeszcze zaimplementowane.</span><span class="sxs-lookup"><span data-stu-id="12811-152">Nothing has been implemented yet.</span></span>

<span data-ttu-id="12811-153">Istnieje możliwość utworzenia definicji komunikatu reprezentującej `decimal` Typ, który działa w celu bezpiecznej serializacji między klientami i serwerami platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="12811-153">It's possible to create a message definition to represent the `decimal` type that works for safe serialization between .NET clients and servers.</span></span> <span data-ttu-id="12811-154">Jednak deweloperzy na innych platformach będą musieli zrozumieć używany format i zaimplementować ich własny sposób obsługi.</span><span class="sxs-lookup"><span data-stu-id="12811-154">But developers on other platforms would have to understand the format being used and implement their own handling for it.</span></span>

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a><span data-ttu-id="12811-155">Tworzenie niestandardowego typu dziesiętnego dla protobuf</span><span class="sxs-lookup"><span data-stu-id="12811-155">Creating a custom decimal type for Protobuf</span></span>

```protobuf
package CustomTypes;

// Example: 12345.6789 -> { units = 12345, nanos = 678900000 }
message DecimalValue {

    // Whole units part of the amount
    int64 units = 1;

    // Nano units of the amount (10^-9)
    // Must be same sign as units
    sfixed32 nanos = 2;
}
```

<span data-ttu-id="12811-156">`nanos`Pole reprezentuje wartości z `0.999_999_999` do `-0.999_999_999` .</span><span class="sxs-lookup"><span data-stu-id="12811-156">The `nanos` field represents values from `0.999_999_999` to `-0.999_999_999`.</span></span> <span data-ttu-id="12811-157">Na przykład `decimal` wartość `1.5m` będzie reprezentowana jako `{ units = 1, nanos = 500_000_000 }` .</span><span class="sxs-lookup"><span data-stu-id="12811-157">For example, the `decimal` value `1.5m` would be represented as `{ units = 1, nanos = 500_000_000 }`.</span></span> <span data-ttu-id="12811-158">To dlatego, że `nanos` pole w tym przykładzie używa `sfixed32` typu, który koduje bardziej wydajne niż `int32` w przypadku większych wartości.</span><span class="sxs-lookup"><span data-stu-id="12811-158">This is why the `nanos` field in this example uses the `sfixed32` type, which encodes more efficiently than `int32` for larger values.</span></span> <span data-ttu-id="12811-159">Jeśli `units` pole jest ujemne, `nanos` pole powinno również być ujemne.</span><span class="sxs-lookup"><span data-stu-id="12811-159">If the `units` field is negative, the `nanos` field should also be negative.</span></span>

> [!NOTE]
> <span data-ttu-id="12811-160">Istnieje wiele innych algorytmów kodowania `decimal` wartości jako ciągi bajtowe, ale ten komunikat jest łatwiejszy do zrozumienia niż którekolwiek z nich.</span><span class="sxs-lookup"><span data-stu-id="12811-160">There are multiple other algorithms for encoding `decimal` values as byte strings, but this message is easier to understand than any of them.</span></span> <span data-ttu-id="12811-161">Na różnych platformach nie ma to wpływ na wartości big-endian ani little-endian.</span><span class="sxs-lookup"><span data-stu-id="12811-161">The values are not affected by big-endian or little-endian on different platforms.</span></span>

<span data-ttu-id="12811-162">Konwersję między tym typem a typem BCL można `decimal` zaimplementować w języku C# w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="12811-162">Conversion between this type and the BCL `decimal` type might be implemented in C# like this:</span></span>

```csharp
namespace CustomTypes
{
    public partial class DecimalValue
    {
        private const decimal NanoFactor = 1_000_000_000;
        public DecimalValue(long units, int nanos)
        {
            Units = units;
            Nanos = nanos;
        }

        public long Units { get; }
        public int Nanos { get; }

        public static implicit operator decimal(CustomTypes.DecimalValue grpcDecimal)
        {
            return grpcDecimal.Units + grpcDecimal.Nanos / NanoFactor;
        }

        public static implicit operator CustomTypes.DecimalValue(decimal value)
        {
            var units = decimal.ToInt64(value);
            var nanos = decimal.ToInt32((value - units) * NanoFactor);
            return new CustomTypes.DecimalValue(units, nanos);
        }
    }
}
```

## <a name="collections"></a><span data-ttu-id="12811-163">Kolekcje</span><span class="sxs-lookup"><span data-stu-id="12811-163">Collections</span></span>

### <a name="lists"></a><span data-ttu-id="12811-164">Listy</span><span class="sxs-lookup"><span data-stu-id="12811-164">Lists</span></span>

<span data-ttu-id="12811-165">Listy w protobuf są określane za pomocą `repeated` słowa kluczowego prefix w polu.</span><span class="sxs-lookup"><span data-stu-id="12811-165">Lists in Protobuf are specified by using the `repeated` prefix keyword on a field.</span></span> <span data-ttu-id="12811-166">Poniższy przykład pokazuje, jak utworzyć listę:</span><span class="sxs-lookup"><span data-stu-id="12811-166">The following example shows how to create a list:</span></span>

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

<span data-ttu-id="12811-167">W wygenerowanym kodzie `repeated` pola są reprezentowane przez `Google.Protobuf.Collections.RepeatedField<T>` typ ogólny.</span><span class="sxs-lookup"><span data-stu-id="12811-167">In the generated code, `repeated` fields are represented by the `Google.Protobuf.Collections.RepeatedField<T>` generic type.</span></span>

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

<span data-ttu-id="12811-168">`RepeatedField<T>` implementuje <xref:System.Collections.Generic.IList%601> .</span><span class="sxs-lookup"><span data-stu-id="12811-168">`RepeatedField<T>` implements <xref:System.Collections.Generic.IList%601>.</span></span> <span data-ttu-id="12811-169">Aby można było użyć zapytań LINQ lub przekonwertować je na tablicę lub listę.</span><span class="sxs-lookup"><span data-stu-id="12811-169">So you can use LINQ queries or convert it to an array or a list.</span></span> <span data-ttu-id="12811-170">`RepeatedField<T>` Właściwości nie mają publicznej metody ustawiającej.</span><span class="sxs-lookup"><span data-stu-id="12811-170">`RepeatedField<T>` properties don't have a public setter.</span></span> <span data-ttu-id="12811-171">Elementy należy dodać do istniejącej kolekcji.</span><span class="sxs-lookup"><span data-stu-id="12811-171">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a><span data-ttu-id="12811-172">Słowniki</span><span class="sxs-lookup"><span data-stu-id="12811-172">Dictionaries</span></span>

<span data-ttu-id="12811-173">Typ .NET <xref:System.Collections.Generic.IDictionary%602> jest reprezentowany w protobuf przy użyciu `map<key_type, value_type>` .</span><span class="sxs-lookup"><span data-stu-id="12811-173">The .NET <xref:System.Collections.Generic.IDictionary%602> type is represented in Protobuf using `map<key_type, value_type>`.</span></span>

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

<span data-ttu-id="12811-174">W wygenerowanym kodzie .NET `map` pola są reprezentowane przez `Google.Protobuf.Collections.MapField<TKey, TValue>` typ ogólny.</span><span class="sxs-lookup"><span data-stu-id="12811-174">In generated .NET code, `map` fields are represented by the `Google.Protobuf.Collections.MapField<TKey, TValue>` generic type.</span></span> <span data-ttu-id="12811-175">`MapField<TKey, TValue>` implementuje <xref:System.Collections.Generic.IDictionary%602> .</span><span class="sxs-lookup"><span data-stu-id="12811-175">`MapField<TKey, TValue>` implements <xref:System.Collections.Generic.IDictionary%602>.</span></span> <span data-ttu-id="12811-176">Podobnie jak `repeated` właściwości, `map` właściwości nie mają publicznej metody ustawiającej.</span><span class="sxs-lookup"><span data-stu-id="12811-176">Like `repeated` properties, `map` properties don't have a public setter.</span></span> <span data-ttu-id="12811-177">Elementy należy dodać do istniejącej kolekcji.</span><span class="sxs-lookup"><span data-stu-id="12811-177">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Attributes["created_by"] = "James";

// Add all items from another collection.
var attributes = new Dictionary<string, string>
{
    ["last_modified"] = DateTime.UtcNow.ToString()
};
person.Attributes.Add(attributes);
```

## <a name="unstructured-and-conditional-messages"></a><span data-ttu-id="12811-178">Komunikaty niestrukturalne i warunkowe</span><span class="sxs-lookup"><span data-stu-id="12811-178">Unstructured and conditional messages</span></span>

<span data-ttu-id="12811-179">Protobuf to format wiadomości w pierwszej kolejności.</span><span class="sxs-lookup"><span data-stu-id="12811-179">Protobuf is a contract-first messaging format.</span></span> <span data-ttu-id="12811-180">Komunikaty aplikacji, w tym jej pola i typy, muszą być określone w `.proto` plikach podczas kompilowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="12811-180">An app's messages, including its fields and types, must be specified in `.proto` files when the app is built.</span></span> <span data-ttu-id="12811-181">Kontrakt protobuf — pierwszy projekt jest dobry do wymuszania zawartości wiadomości, ale może ograniczyć scenariusze, w których nie jest wymagany ścisły kontrakt:</span><span class="sxs-lookup"><span data-stu-id="12811-181">Protobuf's contract-first design is great at enforcing message content but can limit scenarios where a strict contract isn't required:</span></span>

* <span data-ttu-id="12811-182">Komunikaty z nieznanymi ładunkiem.</span><span class="sxs-lookup"><span data-stu-id="12811-182">Messages with unknown payloads.</span></span> <span data-ttu-id="12811-183">Na przykład komunikat z polem, które może zawierać dowolny komunikat.</span><span class="sxs-lookup"><span data-stu-id="12811-183">For example, a message with a field that could contain any message.</span></span>
* <span data-ttu-id="12811-184">Komunikaty warunkowe.</span><span class="sxs-lookup"><span data-stu-id="12811-184">Conditional messages.</span></span> <span data-ttu-id="12811-185">Na przykład komunikat zwrócony z usługi gRPC może być wynikiem sukcesu lub wynikiem błędu.</span><span class="sxs-lookup"><span data-stu-id="12811-185">For example, a message returned from a gRPC service might be a success result or an error result.</span></span>
* <span data-ttu-id="12811-186">Wartości dynamiczne.</span><span class="sxs-lookup"><span data-stu-id="12811-186">Dynamic values.</span></span> <span data-ttu-id="12811-187">Na przykład komunikat z polem zawierającym kolekcję wartości, podobny do formatu JSON.</span><span class="sxs-lookup"><span data-stu-id="12811-187">For example, a message with a field that contains an unstructured collection of values, similar to JSON.</span></span>

<span data-ttu-id="12811-188">Protobuf oferuje funkcje języka i typy do obsługi tych scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="12811-188">Protobuf offers language features and types to support these scenarios.</span></span>

### <a name="any"></a><span data-ttu-id="12811-189">Dowolne</span><span class="sxs-lookup"><span data-stu-id="12811-189">Any</span></span>

<span data-ttu-id="12811-190">`Any`Typ umożliwia używanie komunikatów jako typów osadzonych bez ich `.proto` definicji.</span><span class="sxs-lookup"><span data-stu-id="12811-190">The `Any` type lets you use messages as embedded types without having their `.proto` definition.</span></span> <span data-ttu-id="12811-191">Aby użyć `Any` typu, zaimportuj `any.proto` .</span><span class="sxs-lookup"><span data-stu-id="12811-191">To use the `Any` type, import `any.proto`.</span></span>

```protobuf
import "google/protobuf/any.proto";

message Status {
    string message = 1;
    google.protobuf.Any detail = 2;
}
```

```csharp
// Create a status with a Person message set to detail.
var status = new ErrorStatus();
status.Detail = Any.Pack(new Person { FirstName = "James" });

// Read Person message from detail.
if (status.Detail.Is(Person.Descriptor))
{
    var person = status.Detail.Unpack<Person>();
    // ...
}
```

### <a name="oneof"></a><span data-ttu-id="12811-192">Oneof</span><span class="sxs-lookup"><span data-stu-id="12811-192">Oneof</span></span>

<span data-ttu-id="12811-193">`oneof` pola są funkcją języka.</span><span class="sxs-lookup"><span data-stu-id="12811-193">`oneof` fields are a language feature.</span></span> <span data-ttu-id="12811-194">Kompilator obsługuje `oneof` słowo kluczowe, gdy generuje klasę komunikatów.</span><span class="sxs-lookup"><span data-stu-id="12811-194">The compiler handles the `oneof` keyword when it generates the message class.</span></span> <span data-ttu-id="12811-195">Użycie `oneof` do określenia komunikatu odpowiedzi, który może zwrócić `Person` lub `Error` może wyglądać następująco:</span><span class="sxs-lookup"><span data-stu-id="12811-195">Using `oneof` to specify a response message that could either return a `Person` or `Error` might look like this:</span></span>

```protobuf
message Person {
    // ...
}

message Error {
    // ...
}

message ResponseMessage {
  oneof result {
    Error error = 1;
    Person person = 2;
  }
}
```

<span data-ttu-id="12811-196">Pola w `oneof` zestawie muszą mieć unikatowe numery pól w ogólnej deklaracji komunikatu.</span><span class="sxs-lookup"><span data-stu-id="12811-196">Fields within the `oneof` set must have unique field numbers in the overall message declaration.</span></span>

<span data-ttu-id="12811-197">W przypadku korzystania `oneof` z programu wygenerowany kod C# zawiera wyliczenie, które określa, które z pól zostały ustawione.</span><span class="sxs-lookup"><span data-stu-id="12811-197">When using `oneof`, the generated C# code includes an enum that specifies which of the fields has been set.</span></span> <span data-ttu-id="12811-198">Możesz przetestować Wyliczenie, aby znaleźć pole, które zostało ustawione.</span><span class="sxs-lookup"><span data-stu-id="12811-198">You can test the enum to find which field is set.</span></span> <span data-ttu-id="12811-199">Pola, które nie ustawiają `null` wartości zwracanej lub domyślnej, zamiast zgłaszać wyjątek.</span><span class="sxs-lookup"><span data-stu-id="12811-199">Fields that aren't set return `null` or the default value, rather than throwing an exception.</span></span>

```csharp
var response = await client.GetPersonAsync(new RequestMessage());

switch (response.ResultCase)
{
    case ResponseMessage.ResultOneofCase.Person:
        HandlePerson(response.Person);
        break;
    case ResponseMessage.ResultOneofCase.Error:
        HandleError(response.Error);
        break;
    default:
        throw new ArgumentException("Unexpected result.");
}
```

### <a name="value"></a><span data-ttu-id="12811-200">Wartość</span><span class="sxs-lookup"><span data-stu-id="12811-200">Value</span></span>

<span data-ttu-id="12811-201">`Value`Typ reprezentuje wartość wpisaną dynamicznie.</span><span class="sxs-lookup"><span data-stu-id="12811-201">The `Value` type represents a dynamically typed value.</span></span> <span data-ttu-id="12811-202">Może to być albo `null` Liczba, ciąg, wartość logiczna, słownik wartości ( `Struct` ) lub lista wartości ( `ValueList` ).</span><span class="sxs-lookup"><span data-stu-id="12811-202">It can be either `null`, a number, a string, a boolean, a dictionary of values (`Struct`), or a list of values (`ValueList`).</span></span> <span data-ttu-id="12811-203">`Value` jest dobrze znanym typem protobuf, który korzysta z wcześniej omówionej `oneof` funkcji.</span><span class="sxs-lookup"><span data-stu-id="12811-203">`Value` is a Protobuf Well-Known Type that uses the previously discussed `oneof` feature.</span></span> <span data-ttu-id="12811-204">Aby użyć `Value` typu, zaimportuj `struct.proto` .</span><span class="sxs-lookup"><span data-stu-id="12811-204">To use the `Value` type, import `struct.proto`.</span></span>

```protobuf
import "google/protobuf/struct.proto";

message Status {
    // ...
    google.protobuf.Value data = 3;
}
```

```csharp
// Create dynamic values.
var status = new Status();
status.Data = Value.FromStruct(new Struct
{
    Fields =
    {
        ["enabled"] = Value.ForBoolean(true),
        ["metadata"] = Value.ForList(
            Value.FromString("value1"),
            Value.FromString("value2"))
    }
});

// Read dynamic values.
switch (status.Data.KindCase)
{
    case Value.KindOneofCase.StructValue:
        foreach (var field in status.Data.StructValue.Fields)
        {
            // Read struct fields...
        }
        break;
    // ...
}
```

<span data-ttu-id="12811-205">`Value`Bezpośrednie używanie może być pełne.</span><span class="sxs-lookup"><span data-stu-id="12811-205">Using `Value` directly can be verbose.</span></span> <span data-ttu-id="12811-206">Alternatywnym sposobem korzystania `Value` z programu jest wbudowana obsługa protobuf na potrzeby mapowania komunikatów do formatu JSON.</span><span class="sxs-lookup"><span data-stu-id="12811-206">An alternative way to use `Value` is with Protobuf's built-in support for mapping messages to JSON.</span></span> <span data-ttu-id="12811-207">Protobuf `JsonFormatter` i `JsonWriter` typy mogą być używane z dowolnym komunikatem protobuf.</span><span class="sxs-lookup"><span data-stu-id="12811-207">Protobuf's `JsonFormatter` and `JsonWriter` types can be used with any Protobuf message.</span></span> <span data-ttu-id="12811-208">`Value` jest szczególnie dobrze dostosowany do konwersji do i z formatu JSON.</span><span class="sxs-lookup"><span data-stu-id="12811-208">`Value` is particularly well suited to being converted to and from JSON.</span></span>

<span data-ttu-id="12811-209">Jest to odpowiednik w formacie JSON poprzedniego kodu:</span><span class="sxs-lookup"><span data-stu-id="12811-209">This is the JSON equivalent of the previous code:</span></span>

```csharp
// Create dynamic values from JSON.
var status = new Status();
status.Data = Value.Parser.ParseJson(@"{
    ""enabled"": true,
    ""metadata"": [ ""value1"", ""value2"" ]
}");

// Convert dynamic values to JSON.
// JSON can be read with a library like System.Text.Json or Newtonsoft.Json
var json = JsonFormatter.Default.Format(status.Metadata);
var document = JsonDocument.Parse(json);
```

## <a name="additional-resources"></a><span data-ttu-id="12811-210">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="12811-210">Additional resources</span></span>

* [<span data-ttu-id="12811-211">Przewodnik po języku protobuf</span><span class="sxs-lookup"><span data-stu-id="12811-211">Protobuf language guide</span></span>](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
