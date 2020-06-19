---
title: Hostowanie i wdrażanie ASP.NET CoreBlazor
author: guardrex
description: Dowiedz się, jak hostować i wdrażać Blazor aplikacje.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/index
ms.openlocfilehash: 3a3c5ab5365e5b4312dd3fd516f4906155911cc9
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103818"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>Hostowanie i wdrażanie ASP.NET CoreBlazor

[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)i [Daniel Roth](https://github.com/danroth27)

## <a name="publish-the-app"></a>Publikowanie aplikacji

Aplikacje są publikowane do wdrożenia w konfiguracji wydania.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

1. Wybierz pozycję **kompilacja**  >  **Opublikuj aplikację {aplikacja}** na pasku nawigacyjnym.
1. Wybierz *element docelowy publikowania*. Aby opublikować lokalnie, wybierz pozycję **folder**.
1. Zaakceptuj lokalizację domyślną w polu **Wybierz folder** lub określ inną lokalizację. Wybierz przycisk **Publikuj**.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Wybierz pozycję **Kompiluj**  >  **Publikowanie do folderu**.
1. Potwierdź, że folder otrzymuje opublikowane zasoby, a następnie wybierz pozycję **Publikuj**.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

Użyj [dotnet Publish](/dotnet/core/tools/dotnet-publish) polecenia, aby opublikować aplikację z konfiguracją wydania:

```dotnetcli
dotnet publish -c Release
```

---

Opublikowanie aplikacji wyzwala [przywracanie](/dotnet/core/tools/dotnet-restore) zależności projektu i [kompiluje](/dotnet/core/tools/dotnet-build) projekt przed utworzeniem zasobów do wdrożenia. W ramach procesu kompilacji nieużywane metody i zestawy są usuwane w celu zmniejszenia rozmiaru pobierania aplikacji i czasów ładowania.

Lokalizacje publikowania:

* BlazorZestaw webassembly
  * Autonomiczne: aplikacja jest publikowana w folderze */bin/Release/{Target Framework}/Publish/wwwroot* . Aby wdrożyć aplikację jako lokację statyczną, skopiuj zawartość folderu *wwwroot* do hosta lokacji statycznej.
  * Hostowane: Blazor aplikacja webassembly klienta jest publikowana w folderze */bin/Release/{Target Framework}/Publish/wwwroot* aplikacji serwera, wraz ze wszystkimi innymi statycznymi zasobami sieci Web aplikacji serwera. Wdróż zawartość folderu *publikowania* na hoście.
* BlazorSerwer: aplikacja jest publikowana w folderze */bin/Release/{Target Framework}/Publish* . Wdróż zawartość folderu *publikowania* na hoście.

Zasoby w folderze są wdrażane na serwerze sieci Web. Wdrożenie może być procesem ręcznym lub zautomatyzowanym w zależności od używanych narzędzi programistycznych.

## <a name="app-base-path"></a>Ścieżka podstawowa aplikacji

*Ścieżka podstawowa aplikacji* jest ścieżką głównego adresu URL aplikacji. Weź pod uwagę następujące ASP.NET Core aplikacji i aplikacji Blazor podrzędnej:

* Aplikacja ASP.NET Core ma nazwę `MyApp` :
  * Aplikacja fizycznie znajduje się na *d:/MojaApl*.
  * Żądania są odbierane o `https://www.contoso.com/{MYAPP RESOURCE}` .
* BlazorAplikacja o nazwie `CoolApp` jest aplikacją podrzędną `MyApp` :
  * Aplikacja podrzędna fizycznie znajduje się na *d:/MojaApl/CoolApp*.
  * Żądania są odbierane o `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` .

Bez określania dodatkowej konfiguracji programu `CoolApp` aplikacja podrzędna w tym scenariuszu nie ma informacji o tym, gdzie znajduje się na serwerze. Na przykład aplikacja nie może utworzyć prawidłowych względnych adresów URL do swoich zasobów, nie wiedząc, że znajduje się w względnej ścieżce adresu URL `/CoolApp/` .

Aby zapewnić konfigurację Blazor ścieżki podstawowej aplikacji `https://www.contoso.com/CoolApp/` , `<base>` `href` atrybut tagu jest ustawiany na ścieżkę względną root w pliku *Pages/_Host. cshtml* ( Blazor serwer) lub katalogu *wwwroot/index.html* ( Blazor webassembly):

```html
<base href="/CoolApp/">
```

BlazorAplikacje serwera dodatkowo ustawiają ścieżkę bazową po stronie serwera, wywołując <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> w potoku żądania aplikacji `Startup.Configure` :

```csharp
app.UsePathBase("/CoolApp");
```

Dostarczając względną ścieżkę adresu URL, składnik, który nie znajduje się w katalogu głównym, może konstruować adresy URL względem ścieżki katalogu głównego aplikacji. Składniki na różnych poziomach struktury katalogów mogą tworzyć linki do innych zasobów w lokalizacji w całej aplikacji. Ścieżka podstawowa aplikacji służy również do przechwytywania wybranych hiperłączy, w których miejsce `href` docelowe łącza znajduje się w obszarze URI ścieżki podstawowej aplikacji. BlazorRouter obsługuje nawigację wewnętrzną.

W wielu scenariuszach hostingu względna ścieżka URL do aplikacji jest katalogiem głównym aplikacji. W takich przypadkach Ścieżka bazowa względnego adresu URL aplikacji jest ukośnikiem ( `<base href="/" />` ), który jest domyślną konfiguracją dla Blazor aplikacji. W innych scenariuszach hostingu, takich jak strony GitHub i aplikacje podrzędne IIS, ścieżka podstawowa aplikacji musi być ustawiona na względną ścieżkę URL serwera aplikacji.

Aby ustawić ścieżkę bazową aplikacji, zaktualizuj `<base>` tag wewnątrz `<head>` elementów tagów *stron/_Host. cshtml* ( Blazor serwer) lub pliku *wwwroot/index.html* ( Blazor webassembly). Ustaw `href` wartość atrybutu na `/{RELATIVE URL PATH}/` (wymagany jest końcowy ukośnik), gdzie `{RELATIVE URL PATH}` jest pełną WZGLĘDNĄ ścieżkę adresu URL aplikacji.

W przypadku Blazor aplikacji webassembly z ścieżką względnego adresu URL niegłówną (na przykład `<base href="/CoolApp/">` ) aplikacja nie będzie mogła znaleźć zasobów w *przypadku uruchamiania lokalnego*. Aby rozwiązać ten problem podczas lokalnego tworzenia i testowania, można podać podstawowy argument *ścieżki* , który jest zgodny z `href` wartością `<base>` tagu w czasie wykonywania. Nie dołączaj końcowego ukośnika. Aby przekazać argument podstawowy ścieżki podczas lokalnego uruchamiania aplikacji, należy wykonać `dotnet run` polecenie z katalogu aplikacji z `--pathbase` opcją:

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

W przypadku Blazor aplikacji webassembly ze względną ścieżką URL `/CoolApp/` ( `<base href="/CoolApp/">` ) polecenie to:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

BlazorAplikacja webassembly reaguje lokalnie na `http://localhost:port/CoolApp` .

## <a name="deployment"></a>Wdrożenie

Aby uzyskać wskazówki dotyczące wdrażania, zobacz następujące tematy:

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>
