---
title: Hostowanie i wdrażanie ASP.NET CoreBlazor
author: guardrex
description: Dowiedz się, jak hostować i wdrażać Blazor aplikacje.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 993c1e49aed2efb4283753ab184506e5ae33a3b2
ms.sourcegitcommit: 6318d2bdd63116e178c34492a904be85ec9ac108
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82604821"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>Hostowanie i wdrażanie ASP.NET Core Blazor

[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)i [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a>Publikowanie aplikacji

Aplikacje są publikowane do wdrożenia w konfiguracji wydania.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Wybierz pozycję **kompilacja** > **Opublikuj aplikację {aplikacja}** na pasku nawigacyjnym.
1. Wybierz *element docelowy publikowania*. Aby opublikować lokalnie, wybierz pozycję **folder**.
1. Zaakceptuj lokalizację domyślną w polu **Wybierz folder** lub określ inną lokalizację. Wybierz przycisk **Publikuj**.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Wybierz pozycję **Kompiluj** > **Publikowanie do folderu**.
1. Potwierdź, że folder otrzymuje opublikowane zasoby, a następnie wybierz pozycję **Publikuj**.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

Użyj [dotnet Publish](/dotnet/core/tools/dotnet-publish) polecenia, aby opublikować aplikację z konfiguracją wydania:

```dotnetcli
dotnet publish -c Release
```

---

Opublikowanie aplikacji wyzwala [przywracanie](/dotnet/core/tools/dotnet-restore) zależności projektu i [kompiluje](/dotnet/core/tools/dotnet-build) projekt przed utworzeniem zasobów do wdrożenia. W ramach procesu kompilacji nieużywane metody i zestawy są usuwane w celu zmniejszenia rozmiaru pobierania aplikacji i czasów ładowania.

Lokalizacje publikowania:

* BlazorZestaw webassembly
  * Autonomiczna &ndash; aplikacja jest publikowana w folderze */bin/Release/{Target Framework}/Publish/wwwroot* . Aby wdrożyć aplikację jako lokację statyczną, skopiuj zawartość folderu *wwwroot* do hosta lokacji statycznej.
  * Hostowana &ndash; aplikacja Blazor webassembly klienta jest publikowana w folderze */bin/Release/{Target Framework}/Publish/wwwroot* aplikacji serwera, wraz z wszelkimi innymi statycznymi zasobami sieci Web aplikacji serwera. Wdróż zawartość folderu *publikowania* na hoście.
* BlazorSerwer &ndash; aplikacja została opublikowana w folderze */bin/Release/{Target Framework}/Publish* . Wdróż zawartość folderu *publikowania* na hoście.

Zasoby w folderze są wdrażane na serwerze sieci Web. Wdrożenie może być procesem ręcznym lub zautomatyzowanym w zależności od używanych narzędzi programistycznych.

## <a name="app-base-path"></a>Ścieżka podstawowa aplikacji

*Ścieżka podstawowa aplikacji* jest ścieżką głównego adresu URL aplikacji. Weź pod uwagę następujące ASP.NET Core aplikacji Blazor i aplikacji podrzędnej:

* Aplikacja ASP.NET Core ma nazwę `MyApp`:
  * Aplikacja fizycznie znajduje się na *d:/MojaApl*.
  * Żądania są odbierane `https://www.contoso.com/{MYAPP RESOURCE}`o.
* Blazor Aplikacja o nazwie `CoolApp` jest aplikacją podrzędną `MyApp`:
  * Aplikacja podrzędna fizycznie znajduje się na *d:/MojaApl/CoolApp*.
  * Żądania są odbierane `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`o.

Bez określania dodatkowej konfiguracji programu `CoolApp`aplikacja podrzędna w tym scenariuszu nie ma informacji o tym, gdzie znajduje się na serwerze. Na przykład aplikacja nie może utworzyć prawidłowych względnych adresów URL do swoich zasobów, nie wiedząc, że znajduje się w względnej ścieżce `/CoolApp/`adresu URL.

Aby zapewnić konfigurację ścieżki podstawowej Blazor aplikacji `https://www.contoso.com/CoolApp/`, `<base>` `href` atrybut tagu jest ustawiany na ścieżkę względną katalogu głównego w pliku */_Host. cshtml* (Blazor serwer) lub pliku *wwwroot/index.html* (Blazor webassembly):

```html
<base href="/CoolApp/">
```

BlazorAplikacje serwera dodatkowo ustawiają ścieżkę bazową po stronie serwera, wywołując <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> w potoku żądania aplikacji `Startup.Configure`:

```csharp
app.UsePathBase("/CoolApp");
```

Dostarczając względną ścieżkę adresu URL, składnik, który nie znajduje się w katalogu głównym, może konstruować adresy URL względem ścieżki katalogu głównego aplikacji. Składniki na różnych poziomach struktury katalogów mogą tworzyć linki do innych zasobów w lokalizacji w całej aplikacji. Ścieżka podstawowa aplikacji służy również do przechwytywania wybranych hiperłączy, w których `href` miejsce docelowe łącza znajduje się w obszarze URI ścieżki podstawowej aplikacji. Blazor Router obsługuje nawigację wewnętrzną.

W wielu scenariuszach hostingu względna ścieżka URL do aplikacji jest katalogiem głównym aplikacji. W takich przypadkach Ścieżka bazowa względnego adresu URL aplikacji jest ukośnikiem (`<base href="/" />`), który jest domyślną konfiguracją dla Blazor aplikacji. W innych scenariuszach hostingu, takich jak strony GitHub i aplikacje podrzędne IIS, ścieżka podstawowa aplikacji musi być ustawiona na względną ścieżkę URL serwera aplikacji.

Aby ustawić ścieżkę bazową aplikacji, zaktualizuj `<base>` tag wewnątrz elementów `<head>` tagów *stron/_Host. cshtml* (Blazor serwer) lub plik *wwwroot/index.html* (Blazor webassembly). Ustaw wartość `href` atrybutu na `/{RELATIVE URL PATH}/` (wymagany jest końcowy ukośnik), gdzie `{RELATIVE URL PATH}` jest pełną względną ścieżkę adresu URL aplikacji.

Blazor W przypadku aplikacji webassembly z ścieżką względnego adresu URL niegłówną (na przykład `<base href="/CoolApp/">`) aplikacja nie będzie mogła znaleźć zasobów w *przypadku uruchamiania lokalnego*. Aby rozwiązać ten problem podczas lokalnego tworzenia i testowania, można podać podstawowy argument *ścieżki* , który jest zgodny z `href` wartością `<base>` tagu w czasie wykonywania. Nie dołączaj końcowego ukośnika. Aby przekazać argument podstawowy ścieżki podczas lokalnego uruchamiania aplikacji, należy wykonać `dotnet run` polecenie z katalogu aplikacji z `--pathbase` opcją:

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Blazor W przypadku aplikacji webassembly ze względną ścieżką URL `/CoolApp/` (`<base href="/CoolApp/">`) polecenie to:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

Blazor Aplikacja webassembly reaguje lokalnie na `http://localhost:port/CoolApp`.

## <a name="deployment"></a>Wdrożenie

Aby uzyskać wskazówki dotyczące wdrażania, zobacz następujące tematy:

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
