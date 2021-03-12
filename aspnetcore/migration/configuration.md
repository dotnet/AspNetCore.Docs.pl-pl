---
title: Migrowanie konfiguracji do ASP.NET Core
author: ardalis
description: Dowiedz się, jak przeprowadzić migrację konfiguracji z projektu ASP.NET MVC do projektu ASP.NET Core MVC.
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
uid: migration/configuration
ms.openlocfilehash: c3957bf45dddcead24f7bb0f2702bf1a08950bdd
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587947"
---
# <a name="migrate-configuration-to-aspnet-core"></a>Migrowanie konfiguracji do ASP.NET Core

[Steve Kowalski](https://ardalis.com/) i [Scott Addie](https://scottaddie.com)

W poprzednim artykule rozpocząłmy [migrację projektu ASP.NET MVC do ASP.NET Core MVC](xref:migration/mvc). W tym artykule Migrujemy konfigurację.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/migration/configuration/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="setup-configuration"></a>Konfiguracja konfiguracji

ASP.NET Core nie używa już plików *Global. asax* i *web.config* , które zostały wykorzystane przez poprzednie wersje ASP.NET. We wcześniejszych wersjach programu ASP.NET logika uruchamiania aplikacji została umieszczona w `Application_StartUp` metodzie w *Global. asax*. Później w ASP.NET MVC plik *Startup.cs* został uwzględniony w katalogu głównym projektu; i, został wywołany podczas uruchamiania aplikacji. ASP.NET Core częściowo przyjęła to podejście, umieszczając w pliku *Startup.cs* wszystkie logikę uruchamiania.

Plik *web.config* został również zastąpiony przez ASP.NET Core. Konfigurację można teraz skonfigurować w ramach procedury uruchamiania aplikacji opisanej w *Startup.cs*. Konfiguracja może nadal korzystać z plików XML, ale zazwyczaj projekty ASP.NET Core umieściją wartości konfiguracyjne w pliku w formacie JSON, na przykład *appsettings.json* . System konfiguracji ASP.NET Core może również łatwo uzyskać dostęp do zmiennych środowiskowych, co może zapewnić bezpieczniejsze [i niezawodne lokalizację](xref:security/app-secrets) dla wartości specyficznych dla środowiska. Jest to szczególnie prawdziwe w przypadku wpisów tajnych, takich jak parametry połączenia i klucze interfejsu API, które nie powinny być zaewidencjonowane do kontroli źródła. Zobacz [Konfiguracja](xref:fundamentals/configuration/index) , aby dowiedzieć się więcej o konfiguracji w ASP.NET Core.

W tym artykule zaczynamy od częściowo zmigrowanego projektu ASP.NET Core z [poprzedniego artykułu](xref:migration/mvc). Aby skonfigurować konfigurację, Dodaj następujący Konstruktor i właściwość do pliku *Startup.cs* znajdującego się w katalogu głównym projektu:

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

Należy pamiętać, że w tym momencie plik *Startup.cs* nie zostanie skompilowany, ponieważ nadal będziemy musieli dodać następującą `using` instrukcję:

```csharp
using Microsoft.Extensions.Configuration;
```

Dodaj *appsettings.json* plik do katalogu głównego projektu przy użyciu odpowiedniego szablonu elementu:

![Dodawanie pliku JSON AppSettings](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a>Migrowanie ustawień konfiguracji z web.config

Nasze projekty MVC ASP.NET zawierają wymagane parametry połączenia z bazą danych w *web.config* w `<connectionStrings>` elemencie. W naszym ASP.NET Core projekcie będziemy przechowywać te informacje w *appsettings.json* pliku. Otwórz *appsettings.json* program i zwróć uwagę, że zawiera on już następujące elementy:

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

W wyróżnionym wierszu poniżej Zmień nazwę bazy danych z **_CHANGE_ME** na nazwę bazy danych.

## <a name="summary"></a>Podsumowanie

ASP.NET Core umieszcza wszystkie logikę uruchamiania aplikacji w pojedynczym pliku, w którym można zdefiniować i skonfigurować wymagane usługi i zależności. Zastępuje plik *web.config* elastyczną funkcją konfiguracji, która może korzystać z różnych formatów plików, takich jak JSON, a także zmiennych środowiskowych.
