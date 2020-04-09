---
title: Microsoft.AspNetCore.All metapakiet dla ASP.NET Core 2.0
author: Rick-Anderson
description: Metapakiet Microsoft.AspNetCore.All nie jest zalecany dla ASP.NET Core 2.1 i nowszych.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/25/2018
uid: fundamentals/metapackage
ms.openlocfilehash: cc00c075909da5c17a4aa2fd252c9e662e5a0fc9
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511070"
---
# <a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-20"></a>Microsoft.AspNetCore.All metapakiet dla ASP.NET Core 2.0

::: moniker range=">= aspnetcore-3.0"

Metapakiet `Microsoft.AspNetCore.All` nie jest zawarty w ASP.NET Core 3.0 i nowszych. Aby uzyskać więcej informacji, zobacz [ten problem z githubem](https://github.com/aspnet/Announcements/issues/314).

::: moniker-end

> [!NOTE]
> Zalecamy aplikacje przeznaczone ASP.NET Core 2.1 i później użyć [microsoft.AspNetCore.App metapakiet,](xref:fundamentals/metapackage-app) a nie tego pakietu. Zobacz [Migrowanie z pliku Microsoft.AspNetCore.All do pliku Microsoft.AspNetCore.App](#migrate) w tym artykule.

Ta funkcja wymaga ASP.NET celu Core 2.x .NET Core 2.x.

[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) jest metapakiet, który odnosi się do udostępnionej struktury. *Współużytkowana struktura* to zestaw zestawów (plików*dll),* które nie znajdują się w folderach aplikacji. Aby uruchomić aplikację, należy zainstalować platformę współużytkową. Aby uzyskać więcej informacji, zobacz [Współużytkowane struktury](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Współużytkowane ramy, które `Microsoft.AspNetCore.All` się odwołują, obejmują:

* Wszystkie obsługiwane pakiety przez zespół ASP.NET Core.
* Wszystkie obsługiwane pakiety przez entity framework core.
* Wewnętrzne i zewnętrzne zależności używane przez ASP.NET Core i Entity Framework Core.

Wszystkie funkcje ASP.NET Core 2.x i Entity Framework Core 2.x `Microsoft.AspNetCore.All` są zawarte w pakiecie. Domyślne szablony projektów przeznaczone dla ASP.NET Core 2.0 używają tego pakietu.

Numer wersji `Microsoft.AspNetCore.All` metapakietu reprezentuje minimalną ASP.NET wersję Core i wersję Core programu Entity Framework.

Następujący plik *csproj* odwołuje `Microsoft.AspNetCore.All` się do metapakiety dla ASP.NET Core:

[!code-xml[](metapackage/samples/Metapackage.All.Example.csproj?highlight=8)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implicit-versioning"></a>Niejawne przechowywanie wersji

W ASP.NET Core 2.1 lub nowszym `Microsoft.AspNetCore.All` można określić odwołanie do pakietu bez wersji. Gdy wersja nie jest określona, wersja niejawna jest`Microsoft.NET.Sdk.Web`określona przez SDK ( ). Zaleca się poleganie na wersji niejawnej określonej przez SDK i nie jawnie ustawienie numeru wersji na odwołanie do pakietu. Jeśli masz pytania dotyczące tego podejścia, pozostaw komentarz gitHub w [dyskusji dla wersji niejawnej Microsoft.AspNetCore.App](https://github.com/dotnet/AspNetCore.Docs/issues/6430).

Wersja niejawna `major.minor.0` jest ustawiona na aplikacje przenośne. Mechanizm przekazywania udostępnionej struktury uruchamia aplikację w najnowszej zgodnej wersji wśród zainstalowanych udostępnionych struktur. Aby zagwarantować, że ta sama wersja jest używana w programach, testach i produkcji, upewnij się, że ta sama wersja udostępnionej struktury jest zainstalowana we wszystkich środowiskach. W przypadku aplikacji samodzielnych numer wersji niejawnej jest ustawiany na `major.minor.patch` platformę udostępnionej w pakiecie z zainstalowanym zestawem SDK.

Określenie numeru wersji w `Microsoft.AspNetCore.All` odwołaniu do pakietu **nie** gwarantuje, że wybrano wersję udostępnionej struktury. Załóżmy na przykład, że określono wersję "2.1.1", ale jest zainstalowana "2.1.3". W takim przypadku aplikacja użyje "2.1.3". Chociaż nie jest to zalecane, można wyłączyć roll forward (łatka i/lub drobne). Aby uzyskać więcej informacji dotyczących roll-forward hosta dotnet i sposobu konfigurowania jego zachowania, zobacz [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).

Zestaw SDK projektu musi być `Microsoft.NET.Sdk.Web` ustawiony w pliku projektu, `Microsoft.AspNetCore.All`aby użyć niejawnej wersji pliku . Po `Microsoft.NET.Sdk` określeniu SDK (w`<Project Sdk="Microsoft.NET.Sdk">` górnej części pliku projektu) generowane jest następujące ostrzeżenie:

*Ostrzeżenie NU1604: Zależność projektu Microsoft.AspNetCore.All nie zawiera włącznie dolnej granicy. Dołącz dolną granicę w wersji zależności, aby zapewnić spójne wyniki przywracania.*

Jest to znany problem z .NET Core 2.1 SDK i zostanie rozwiązany w .NET Core 2.2 SDK.

::: moniker-end

<a name="migrate"></a>

## <a name="migrating-from-microsoftaspnetcoreall-to-microsoftaspnetcoreapp"></a>Migracja z pliku Microsoft.AspNetCore.All do pliku Microsoft.AspNetCore.App

Następujące pakiety są `Microsoft.AspNetCore.All` zawarte w `Microsoft.AspNetCore.App` pakiecie, ale nie.

* `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServices.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServicesIntegration`
* `Microsoft.AspNetCore.DataProtection.AzureKeyVault`
* `Microsoft.AspNetCore.DataProtection.AzureStorage`
* `Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`
* `Microsoft.AspNetCore.SignalR.Redis`
* `Microsoft.Data.Sqlite`
* `Microsoft.Data.Sqlite.Core`
* `Microsoft.EntityFrameworkCore.Sqlite`
* `Microsoft.EntityFrameworkCore.Sqlite.Core`
* `Microsoft.Extensions.Caching.Redis`
* `Microsoft.Extensions.Configuration.AzureKeyVault`
* `Microsoft.Extensions.Logging.AzureAppServices`
* `Microsoft.VisualStudio.Web.BrowserLink`

Aby przejść `Microsoft.AspNetCore.All` `Microsoft.AspNetCore.App`z do , jeśli aplikacja używa żadnych interfejsów API z powyższych pakietów lub pakietów wniesionych przez te pakiety, dodaj odwołania do tych pakietów w projekcie.

Wszelkie zależności poprzednich pakietów, które w przeciwnym `Microsoft.AspNetCore.App` razie nie są zależności nie są uwzględniane niejawnie. Przykład:

* `StackExchange.Redis`jako zależność od`Microsoft.Extensions.Caching.Redis`
* `Microsoft.ApplicationInsights`jako zależność od`Microsoft.AspNetCore.ApplicationInsights.HostingStartup`

## <a name="update-aspnet-core-21"></a>Aktualizacja ASP.NET Core 2.1

Zalecamy migrację `Microsoft.AspNetCore.App` do metapakiety dla 2.1 i nowsze. Aby nadal `Microsoft.AspNetCore.All` korzystać z metapakiety i upewnić się, że najnowsza wersja poprawki jest wdrożona:

* Na komputerach deweloperskich i serwerach kompilacji: Zainstaluj najnowszy [pakiet .NET Core SDK](https://dotnet.microsoft.com/download).
* Na serwerach wdrażania: Zainstaluj najnowsze [środowisko uruchomieniowe .NET Core](https://dotnet.microsoft.com/download).
 Aplikacja zostanie przerzuci do najnowszej zainstalowanej wersji po ponownym uruchomieniu aplikacji.
