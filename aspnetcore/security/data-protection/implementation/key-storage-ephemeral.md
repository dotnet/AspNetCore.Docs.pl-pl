---
title: Dostawcy tymczasowej ochrony danych w ASP.NET Core
author: rick-anderson
description: Zapoznaj się ze szczegółami implementacji ASP.NET Core tymczasowych dostawców ochrony danych.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: ed0fca88ecf2b002a4421fb120d90adff1b5b12e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052702"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a><span data-ttu-id="da214-103">Dostawcy tymczasowej ochrony danych w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="da214-103">Ephemeral data protection providers in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-ephemeral"></a>

<span data-ttu-id="da214-104">Istnieją scenariusze, w których aplikacja wymaga throwaway `IDataProtectionProvider` .</span><span class="sxs-lookup"><span data-stu-id="da214-104">There are scenarios where an application needs a throwaway `IDataProtectionProvider`.</span></span> <span data-ttu-id="da214-105">Na przykład deweloper może po prostu eksperymentować w jednostronnej aplikacji konsolowej lub sama sama aplikacja jest przejściowa (jest to skrypt lub projekt testu jednostkowego).</span><span class="sxs-lookup"><span data-stu-id="da214-105">For example, the developer might just be experimenting in a one-off console application, or the application itself is transient (it's scripted or a unit test project).</span></span> <span data-ttu-id="da214-106">Aby można było obsługiwać te scenariusze, pakiet [Microsoft. AspNetCore. dataprotection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) zawiera typ `EphemeralDataProtectionProvider` .</span><span class="sxs-lookup"><span data-stu-id="da214-106">To support these scenarios the [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) package includes a type `EphemeralDataProtectionProvider`.</span></span> <span data-ttu-id="da214-107">Ten typ zapewnia podstawową implementację, `IDataProtectionProvider` której repozytorium kluczy jest przechowywane wyłącznie w pamięci i nie jest zapisywane w magazynie zapasowym.</span><span class="sxs-lookup"><span data-stu-id="da214-107">This type provides a basic implementation of `IDataProtectionProvider` whose key repository is held solely in-memory and isn't written out to any backing store.</span></span>

<span data-ttu-id="da214-108">Każde wystąpienie programu `EphemeralDataProtectionProvider` używa własnego unikatowego klucza głównego.</span><span class="sxs-lookup"><span data-stu-id="da214-108">Each instance of `EphemeralDataProtectionProvider` uses its own unique master key.</span></span> <span data-ttu-id="da214-109">W związku z tym, jeśli `IDataProtector` w przypadku `EphemeralDataProtectionProvider` wygenerowania chronionego ładunku ten ładunek może być niechroniony tylko przez odpowiedniki `IDataProtector` (w tym samym łańcuchu [celu](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) ) w tym samym `EphemeralDataProtectionProvider` wystąpieniu.</span><span class="sxs-lookup"><span data-stu-id="da214-109">Therefore, if an `IDataProtector` rooted at an `EphemeralDataProtectionProvider` generates a protected payload, that payload can only be unprotected by an equivalent `IDataProtector` (given the same [purpose](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) chain) rooted at the same `EphemeralDataProtectionProvider` instance.</span></span>

<span data-ttu-id="da214-110">Poniższy przykład demonstruje Tworzenie wystąpienia `EphemeralDataProtectionProvider` i używanie go do ochrony i wyochronowania danych.</span><span class="sxs-lookup"><span data-stu-id="da214-110">The following sample demonstrates instantiating an `EphemeralDataProtectionProvider` and using it to protect and unprotect data.</span></span>

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
