---
title: Hostuj i wdrażaj ASP.NET CoreBlazor
author: guardrex
description: Dowiedz się, jak Blazor hostować i wdrażać aplikacje.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: ddf70da29a82d462422c1bdf74ff45b92bb10b56
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434268"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>Hostuj i wdrażaj ASP.NET Core Blazor

[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)i [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a>Publikowanie aplikacji

Aplikacje są publikowane do wdrożenia w konfiguracji wersji.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

1. Wybierz **polecenie Buduj** > **opublikuj {APLIKACJĘ}** na pasku nawigacyjnym.
1. Wybierz *cel publikowania*. Aby publikować lokalnie, wybierz **folder**.
1. Zaakceptuj lokalizację **domyślną** w polu Wybierz folder lub określ inną lokalizację. Wybierz przycisk **Publikuj**.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

Użyj polecenia [publikowania dotnet,](/dotnet/core/tools/dotnet-publish) aby opublikować aplikację z konfiguracją wersji:

```dotnetcli
dotnet publish -c Release
```

---

Publikowanie aplikacji wyzwala [przywracanie](/dotnet/core/tools/dotnet-restore) zależności projektu i [tworzy](/dotnet/core/tools/dotnet-build) projekt przed utworzeniem zasobów do wdrożenia. W ramach procesu kompilacji nieużywane metody i zestawy są usuwane w celu zmniejszenia rozmiaru pobierania aplikacji i czasu ładowania.

Publikuj lokalizacje:

* BlazorWebAssembly (WebAssembly)
  * &ndash; Samodzielny Aplikacja jest publikowana w folderze */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot.* Aby wdrożyć aplikację jako witrynę statyczną, skopiuj zawartość folderu *wwwroot* do statycznego hosta witryny.
  * Hosted &ndash; Aplikacja Blazor WebAssembly klienta jest publikowana w folderze */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* aplikacji serwera wraz z innymi statycznymi zasobami sieci Web aplikacji serwera. Wdrażanie zawartości folderu *publikowania* na hoście.
* BlazorSerwer &ndash; Aplikacja jest publikowana w folderze */bin/Release/{TARGET FRAMEWORK}/publish.* Wdrażanie zawartości folderu *publikowania* na hoście.

Zasoby w folderze są wdrażane na serwerze sieci web. Wdrożenie może być procesem ręcznym lub zautomatyzowanym w zależności od używanych narzędzi programistycznych.

## <a name="app-base-path"></a>Ścieżka podstawowa aplikacji

*Ścieżka podstawowa aplikacji* to główna ścieżka adresu URL aplikacji. Należy wziąć pod uwagę następujące Blazor ASP.NET aplikacji Core i podkoszuli:

* Nazwa aplikacji ASP.NET `MyApp`Core:
  * Aplikacja fizycznie znajduje się w *d:/MyApp*.
  * Wnioski są `https://www.contoso.com/{MYAPP RESOURCE}`odbierane w punkcie .
* Nazwa Blazor aplikacji `CoolApp` to podnauka: `MyApp`
  * Podaplika fizycznie znajduje się w *d:/MyApp/CoolApp*.
  * Wnioski są `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`odbierane w punkcie .

Bez określania dodatkowej konfiguracji dla `CoolApp`aplikacji , podnauka w tym scenariuszu nie ma wiedzy o tym, gdzie znajduje się na serwerze. Na przykład aplikacja nie może tworzyć poprawnych względnych adresów URL do swoich zasobów, nie wiedząc, że znajduje się przy względnej ścieżce `/CoolApp/`adresu URL .

Aby Blazor zapewnić konfigurację ścieżki podstawowej `https://www.contoso.com/CoolApp/`aplikacji, atrybut `<base>` tagu `href` jest ustawiony na względną ścieżkę główną w plikuBlazor *Pages/_Host.cshtml* (Serwer) lub *wwwroot/index.html* pliku (WebAssembly):Blazor

```html
<base href="/CoolApp/">
```

BlazorAplikacje serwera dodatkowo ustawić ścieżkę bazową po stronie serwera, wywołując <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> w potoku żądania `Startup.Configure`aplikacji:

```csharp
app.UsePathBase("/CoolApp");
```

Udostępniając ścieżkę względnego adresu URL, składnik, który nie znajduje się w katalogu głównym, może tworzyć adresy URL względem ścieżki głównej aplikacji. Składniki na różnych poziomach struktury katalogów można tworzyć łącza do innych zasobów w lokalizacjach w całej aplikacji. Ścieżka podstawowa aplikacji służy również do przechwytywania `href` wybranych hiperłączy, gdzie miejsce docelowe łącza znajduje się w przestrzeni URI ścieżki podstawowej aplikacji. Router Blazor obsługuje nawigację wewnętrzną.

W wielu scenariuszach hostingu względna ścieżka adresu URL do aplikacji jest głównym źródłem aplikacji. W takich przypadkach względna ścieżka bazowa adresu URL`<base href="/" />`aplikacji jest ukośnikiem do przodu ( ), który jest domyślną konfiguracją Blazor aplikacji. W innych scenariuszach hostingu, takich jak Strony GitHub i aplikacje podrzędne usług IIS, ścieżka podstawowa aplikacji musi być ustawiona na ścieżkę względnego adresu URL serwera aplikacji.

Aby ustawić ścieżkę podstawową aplikacji, `<base>` zaktualizuj znacznik w elementach `<head>` tagu pliku *Pages/_Host.cshtml* (Serwer)Blazor lub *wwwroot/index.html* (WebAssembly).Blazor Ustaw `href` wartość atrybutu `/{RELATIVE URL PATH}/` (wymagany jest ukośnik `{RELATIVE URL PATH}` końcowy), gdzie jest pełna ścieżka względnego adresu URL aplikacji.

W Blazor przypadku aplikacji WebAssembly ze ścieżką względnego `<base href="/CoolApp/">`adresu URL niebędącego głównym (na przykład), aplikacja nie może znaleźć swoich zasobów *po uruchomieniu lokalnie*. Aby rozwiązać ten problem podczas lokalnego rozwoju i testowania, `href` można `<base>` podać argument *podstawowy ścieżki,* który odpowiada wartości tagu w czasie wykonywania. Nie dołączaj końcowego ukośnika. Aby przekazać argument podstawowy ścieżki podczas lokalnego `dotnet run` uruchamiania aplikacji, wykonaj polecenie `--pathbase` z katalogu aplikacji z opcją:

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

W Blazor przypadku aplikacji WebAssembly ze `/CoolApp/` ścieżką względnego adresu URL (`<base href="/CoolApp/">`), polecenie brzmi:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

Aplikacja Blazor WebAssembly odpowiada lokalnie `http://localhost:port/CoolApp`na stronie .

## <a name="deployment"></a>Wdrożenie

Aby uzyskać wskazówki dotyczące wdrażania, zobacz następujące tematy:

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
