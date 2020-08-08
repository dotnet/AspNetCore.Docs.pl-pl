---
title: Dostawcy tymczasowej ochrony danych w ASP.NET Core
author: rick-anderson
description: Zapoznaj się ze szczegółami implementacji ASP.NET Core tymczasowych dostawców ochrony danych.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: f51553385d9481a1e96fe3e1a14e51e470b0e735
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018263"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a>Dostawcy tymczasowej ochrony danych w ASP.NET Core

<a name="data-protection-implementation-key-storage-ephemeral"></a>

Istnieją scenariusze, w których aplikacja wymaga throwaway `IDataProtectionProvider` . Na przykład deweloper może po prostu eksperymentować w jednostronnej aplikacji konsolowej lub sama sama aplikacja jest przejściowa (jest to skrypt lub projekt testu jednostkowego). Aby można było obsługiwać te scenariusze, pakiet [Microsoft. AspNetCore. dataprotection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) zawiera typ `EphemeralDataProtectionProvider` . Ten typ zapewnia podstawową implementację, `IDataProtectionProvider` której repozytorium kluczy jest przechowywane wyłącznie w pamięci i nie jest zapisywane w magazynie zapasowym.

Każde wystąpienie programu `EphemeralDataProtectionProvider` używa własnego unikatowego klucza głównego. W związku z tym, jeśli `IDataProtector` w przypadku `EphemeralDataProtectionProvider` wygenerowania chronionego ładunku ten ładunek może być niechroniony tylko przez odpowiedniki `IDataProtector` (w tym samym łańcuchu [celu](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) ) w tym samym `EphemeralDataProtectionProvider` wystąpieniu.

Poniższy przykład demonstruje Tworzenie wystąpienia `EphemeralDataProtectionProvider` i używanie go do ochrony i wyochronowania danych.

```csharp
using System;
using Microsoft.AspNetCore.DataProtection;

public class Program
{
    public static void Main(string[] args)
    {
        const string purpose = "Ephemeral.App.v1";

        // create an ephemeral provider and demonstrate that it can round-trip a payload
        var provider = new EphemeralDataProtectionProvider();
        var protector = provider.CreateProtector(purpose);
        Console.Write("Enter input: ");
        string input = Console.ReadLine();

        // protect the payload
        string protectedPayload = protector.Protect(input);
        Console.WriteLine($"Protect returned: {protectedPayload}");

        // unprotect the payload
        string unprotectedPayload = protector.Unprotect(protectedPayload);
        Console.WriteLine($"Unprotect returned: {unprotectedPayload}");

        // if I create a new ephemeral provider, it won't be able to unprotect existing
        // payloads, even if I specify the same purpose
        provider = new EphemeralDataProtectionProvider();
        protector = provider.CreateProtector(purpose);
        unprotectedPayload = protector.Unprotect(protectedPayload); // THROWS
    }
}

/*
* SAMPLE OUTPUT
*
* Enter input: Hello!
* Protect returned: CfDJ8AAAAAAAAAAAAAAAAAAAAA...uGoxWLjGKtm1SkNACQ
* Unprotect returned: Hello!
* << throws CryptographicException >>
*/
```
