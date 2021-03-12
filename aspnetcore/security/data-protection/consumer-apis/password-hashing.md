---
title: Hasła skrótu w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak skrócić hasła przy użyciu interfejsów API ochrony danych ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: b88abd076febae95f8990bcd730ce0d4517e92d2
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587765"
---
# <a name="hash-passwords-in-aspnet-core"></a><span data-ttu-id="3ba4a-103">Hasła skrótu w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3ba4a-103">Hash passwords in ASP.NET Core</span></span>

<span data-ttu-id="3ba4a-104">Baza kodu ochrony danych zawiera pakiet *Microsoft. AspNetCore. Cryptography.* Key, który zawiera funkcje wyprowadzania klucza kryptograficznego.</span><span class="sxs-lookup"><span data-stu-id="3ba4a-104">The data protection code base includes a package *Microsoft.AspNetCore.Cryptography.KeyDerivation* which contains cryptographic key derivation functions.</span></span> <span data-ttu-id="3ba4a-105">Ten pakiet jest składnikiem autonomicznym i nie ma żadnych zależności w pozostałej części systemu ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="3ba4a-105">This package is a standalone component and has no dependencies on the rest of the data protection system.</span></span> <span data-ttu-id="3ba4a-106">Może być używany całkowicie niezależnie.</span><span class="sxs-lookup"><span data-stu-id="3ba4a-106">It can be used completely independently.</span></span> <span data-ttu-id="3ba4a-107">Źródło istnieje wraz z bazą kodu ochrony danych jako wygoda.</span><span class="sxs-lookup"><span data-stu-id="3ba4a-107">The source exists alongside the data protection code base as a convenience.</span></span>

<span data-ttu-id="3ba4a-108">Pakiet oferuje obecnie metodę `KeyDerivation.Pbkdf2` , która umożliwia mieszanie hasła przy użyciu [algorytmu PBKDF2](https://tools.ietf.org/html/rfc2898#section-5.2).</span><span class="sxs-lookup"><span data-stu-id="3ba4a-108">The package currently offers a method `KeyDerivation.Pbkdf2` which allows hashing a password using the [PBKDF2 algorithm](https://tools.ietf.org/html/rfc2898#section-5.2).</span></span> <span data-ttu-id="3ba4a-109">Ten interfejs API jest bardzo podobny do istniejącego [typu Rfc2898DeriveBytes](/dotnet/api/system.security.cryptography.rfc2898derivebytes).NET Framework, ale istnieją trzy ważne rozróżnienia:</span><span class="sxs-lookup"><span data-stu-id="3ba4a-109">This API is very similar to the .NET Framework's existing [Rfc2898DeriveBytes type](/dotnet/api/system.security.cryptography.rfc2898derivebytes), but there are three important distinctions:</span></span>

1. <span data-ttu-id="3ba4a-110">`KeyDerivation.Pbkdf2`Metoda obsługuje zużywanie wielu PRFs (obecnie `HMACSHA1` , `HMACSHA256` i `HMACSHA512` ), podczas gdy `Rfc2898DeriveBytes` Typ obsługuje tylko `HMACSHA1` .</span><span class="sxs-lookup"><span data-stu-id="3ba4a-110">The `KeyDerivation.Pbkdf2` method supports consuming multiple PRFs (currently `HMACSHA1`, `HMACSHA256`, and `HMACSHA512`), whereas the `Rfc2898DeriveBytes` type only supports `HMACSHA1`.</span></span>

2. <span data-ttu-id="3ba4a-111">`KeyDerivation.Pbkdf2`Metoda wykrywa bieżący system operacyjny i próbuje wybrać najbardziej zoptymalizowaną implementację procedury, zapewniając znacznie lepszą wydajność w niektórych przypadkach.</span><span class="sxs-lookup"><span data-stu-id="3ba4a-111">The `KeyDerivation.Pbkdf2` method detects the current operating system and attempts to choose the most optimized implementation of the routine, providing much better performance in certain cases.</span></span> <span data-ttu-id="3ba4a-112">(W systemie Windows 8 oferuje około 10X przepływności `Rfc2898DeriveBytes` .)</span><span class="sxs-lookup"><span data-stu-id="3ba4a-112">(On Windows 8, it offers around 10x the throughput of `Rfc2898DeriveBytes`.)</span></span>

3. <span data-ttu-id="3ba4a-113">`KeyDerivation.Pbkdf2`Metoda wymaga, aby obiekt wywołujący określił wszystkie parametry (sól, PRF i liczba iteracji).</span><span class="sxs-lookup"><span data-stu-id="3ba4a-113">The `KeyDerivation.Pbkdf2` method requires the caller to specify all parameters (salt, PRF, and iteration count).</span></span> <span data-ttu-id="3ba4a-114">`Rfc2898DeriveBytes`Typ zawiera wartości domyślne dla tych.</span><span class="sxs-lookup"><span data-stu-id="3ba4a-114">The `Rfc2898DeriveBytes` type provides default values for these.</span></span>

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

<span data-ttu-id="3ba4a-115">Zobacz [kod źródłowy](https://github.com/dotnet/AspNetCore/blob/main/src/Identity/Extensions.Core/src/PasswordHasher.cs) typu dla ASP.NET Core Identity `PasswordHasher` rzeczywistego przypadku użycia.</span><span class="sxs-lookup"><span data-stu-id="3ba4a-115">See the [source code](https://github.com/dotnet/AspNetCore/blob/main/src/Identity/Extensions.Core/src/PasswordHasher.cs) for ASP.NET Core Identity's `PasswordHasher` type for a real-world use case.</span></span>
