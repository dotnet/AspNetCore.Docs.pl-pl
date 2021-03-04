---
title: Przykłady uwierzytelniania dla ASP.NET Core
author: rick-anderson
description: Zawiera łącza do przykładów uwierzytelniania w repozytorium ASP.NET Core.
ms.author: riande
ms.date: 02/21/2021
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
uid: security/authentication/samples
ms.openlocfilehash: e7fb2ac32f57cf4ecd3c5db294bd0df8e186b6c6
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110121"
---
# <a name="authentication-samples-for-aspnet-core"></a>Przykłady uwierzytelniania dla ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

[Repozytorium ASP.NET Core](https://github.com/dotnet/aspnetcore) zawiera następujące przykłady uwierzytelniania ( `main` gałąź):

* [Przekształcanie oświadczeń](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/ClaimsTransformation)
* [Cookie ponowne](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Cookies)
* [Niestandardowa odpowiedź na niepowodzenie autoryzacji](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomAuthorizationFailureResponse)
* [Niestandardowy dostawca zasad — IAuthorizationPolicyProvider](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomPolicyProvider)
* [Dynamiczne schematy i opcje uwierzytelniania](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/DynamicSchemes)
* [Oświadczenia zewnętrzne](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Identity.ExternalClaims)
* [Wybieranie między cookie i innym schematem uwierzytelniania opartym na żądaniu](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/PathSchemeSelection)
* [Ogranicza dostęp do plików statycznych](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/StaticFilesAuth)

## <a name="obtain-and-run-the-samples"></a>Pobierz i uruchom przykłady

Przykładowe linki podane w tym artykule zawierają przykłady dla nadchodzącej wersji ASP.NET Core. Aby uzyskać przykład dla bieżącej wersji lub wcześniejszej wersji, wykonaj następujące czynności:

* Wybierz gałąź wydania [repozytorium ASP.NET Core](https://github.com/dotnet/aspnetcore)] ( https://github.com/dotnet/aspnetcore) . Na przykład `release/5.0` gałąź zawiera przykłady dla wydania ASP.NET Core 5,0.
* Klonuj lub Pobierz repozytorium ASP.NET Core.
* Sprawdź, czy w systemie lokalnym jest zainstalowany [zestaw .NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) wersja zgodna z klonem ASP.NET Core repozytorium.
* Przejdź do przykładowego `aspnetcore/src/Security/samples` folderu w folderze, a następnie uruchom próbkę za pomocą [ `dotnet run` polecenia](/dotnet/core/tools/dotnet-run).
