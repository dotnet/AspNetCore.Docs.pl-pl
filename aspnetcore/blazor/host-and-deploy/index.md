---
title: Hostowanie i wdrażanie ASP.NET CoreBlazor
author: guardrex
description: Dowiedz się, jak hostować i wdrażać Blazor aplikacje.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/index
ms.openlocfilehash: d8c1d340a05ebdddffcdebad400b44e31159bc28
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87818901"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor"></a>Hostowanie i wdrażanie ASP.NET CoreBlazor

[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)i [Daniel Roth](https://github.com/danroth27)

## <a name="publish-the-app"></a>Publikowanie aplikacji

Aplikacje są publikowane do wdrożenia w konfiguracji wydania.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

1. Wybierz pozycję **kompilacja**  >  **Opublikuj aplikację {aplikacja}** na pasku nawigacyjnym.
1. Wybierz *element docelowy publikowania*. Aby opublikować lokalnie, wybierz pozycję **folder**.
1. Zaakceptuj lokalizację domyślną w polu **Wybierz folder** lub określ inną lokalizację. Wybierz **`Publish`** przycisk.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Wybierz pozycję **Kompiluj**  >  **Publikowanie do folderu**.
1. Potwierdź folder, w którym mają zostać odebrane opublikowane zasoby, a następnie wybierz opcję **`Publish`** .

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

Użyj [`dotnet publish`](/dotnet/core/tools/dotnet-publish) polecenia, aby opublikować aplikację z konfiguracją wydania:

```dotnetcli
dotnet publish -c Release
```

---

Opublikowanie aplikacji wyzwala [przywracanie](/dotnet/core/tools/dotnet-restore) zależności projektu i [kompiluje](/dotnet/core/tools/dotnet-build) projekt przed utworzeniem zasobów do wdrożenia. W ramach procesu kompilacji nieużywane metody i zestawy są usuwane w celu zmniejszenia rozmiaru pobierania aplikacji i czasów ładowania.

Lokalizacje publikowania:

* Blazor WebAssembly
  * Autonomiczne: aplikacja jest publikowana w `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folderze. Aby wdrożyć aplikację jako lokację statyczną, skopiuj zawartość `wwwroot` folderu do hosta lokacji statycznej.
  * Hostowane: aplikacja kliencka Blazor WebAssembly jest publikowana w `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folderze aplikacji serwerowej wraz ze wszystkimi innymi statycznymi zasobami sieci Web aplikacji serwera. Wdróż zawartość `publish` folderu na hoście.
* Blazor Server: Aplikacja jest publikowana w `/bin/Release/{TARGET FRAMEWORK}/publish` folderze. Wdróż zawartość `publish` folderu na hoście.

Zasoby w folderze są wdrażane na serwerze sieci Web. Wdrożenie może być procesem ręcznym lub zautomatyzowanym w zależności od używanych narzędzi programistycznych.

## <a name="app-base-path"></a>Ścieżka podstawowa aplikacji

*Ścieżka podstawowa aplikacji* jest ścieżką głównego adresu URL aplikacji. Weź pod uwagę następujące ASP.NET Core aplikacji i aplikacji Blazor podrzędnej:

* Aplikacja ASP.NET Core ma nazwę `MyApp` :
  * Aplikacja fizycznie znajduje się w lokalizacji `d:/MyApp` .
  * Żądania są odbierane o `https://www.contoso.com/{MYAPP RESOURCE}` .
* BlazorAplikacja o nazwie `CoolApp` jest aplikacją podrzędną `MyApp` :
  * Aplikacja podrzędna fizycznie znajduje się w `d:/MyApp/CoolApp` .
  * Żądania są odbierane o `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` .

Bez określania dodatkowej konfiguracji programu `CoolApp` aplikacja podrzędna w tym scenariuszu nie ma informacji o tym, gdzie znajduje się na serwerze. Na przykład aplikacja nie może utworzyć prawidłowych względnych adresów URL do swoich zasobów, nie wiedząc, że znajduje się w względnej ścieżce adresu URL `/CoolApp/` .

Aby zapewnić konfigurację Blazor ścieżki podstawowej aplikacji `https://www.contoso.com/CoolApp/` , `<base>` `href` atrybut znacznika jest ustawiany na względną ścieżkę katalogu głównego w `Pages/_Host.cshtml` pliku ( Blazor Server ) lub `wwwroot/index.html` pliku ( Blazor WebAssembly ):

```html
<base href="/CoolApp/">
```

Blazor ServerPonadto aplikacje ustawiają ścieżkę bazową po stronie serwera, wywołując <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> w potoku żądania aplikacji `Startup.Configure` :

```csharp
app.UsePathBase("/CoolApp");
```

Dostarczając względną ścieżkę adresu URL, składnik, który nie znajduje się w katalogu głównym, może konstruować adresy URL względem ścieżki katalogu głównego aplikacji. Składniki na różnych poziomach struktury katalogów mogą tworzyć linki do innych zasobów w lokalizacji w całej aplikacji. Ścieżka podstawowa aplikacji służy również do przechwytywania wybranych hiperłączy, w których miejsce `href` docelowe łącza znajduje się w obszarze URI ścieżki podstawowej aplikacji. BlazorRouter obsługuje nawigację wewnętrzną.

W wielu scenariuszach hostingu względna ścieżka URL do aplikacji jest katalogiem głównym aplikacji. W takich przypadkach Ścieżka bazowa względnego adresu URL aplikacji jest ukośnikiem ( `<base href="/" />` ), który jest domyślną konfiguracją dla Blazor aplikacji. W innych scenariuszach hostingu, takich jak strony GitHub i aplikacje podrzędne IIS, ścieżka podstawowa aplikacji musi być ustawiona na względną ścieżkę URL serwera aplikacji.

Aby ustawić ścieżkę bazową aplikacji, zaktualizuj `<base>` tag wewnątrz `<head>` elementów tagów `Pages/_Host.cshtml` pliku ( Blazor Server ) lub `wwwroot/index.html` pliku ( Blazor WebAssembly ). Ustaw `href` wartość atrybutu na `/{RELATIVE URL PATH}/` (wymagany jest końcowy ukośnik), gdzie `{RELATIVE URL PATH}` jest pełną WZGLĘDNĄ ścieżkę adresu URL aplikacji.

W przypadku Blazor WebAssembly aplikacji z ścieżką względną adresu URL niegłówną (na przykład `<base href="/CoolApp/">` ) aplikacja nie będzie mogła znaleźć zasobów w *przypadku uruchamiania lokalnego*. Aby rozwiązać ten problem podczas lokalnego tworzenia i testowania, można podać podstawowy argument *ścieżki* , który jest zgodny z `href` wartością `<base>` tagu w czasie wykonywania. Nie dołączaj końcowego ukośnika. Aby przekazać argument podstawowy ścieżki podczas lokalnego uruchamiania aplikacji, należy wykonać `dotnet run` polecenie z katalogu aplikacji z `--pathbase` opcją:

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

W przypadku Blazor WebAssembly aplikacji ze względną ścieżką URL `/CoolApp/` ( `<base href="/CoolApp/">` ) polecenie to:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

Blazor WebAssemblyAplikacja reaguje lokalnie o `http://localhost:port/CoolApp` .

**Blazor Server`MapFallbackToPage`Konfiguracja**

Przekaż następującą ścieżkę do <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> programu w programie `Startup.Configure` :

```csharp
endpoints.MapFallbackToPage("/{RELATIVE PATH}/{**path:nonfile}");
```

Symbol zastępczy `{RELATIVE PATH}` jest ścieżką spoza katalogu głównego na serwerze. Na przykład, `CoolApp` jest segmentem symbolu zastępczego, jeśli nie jest to adres URL dla aplikacji `https://{HOST}:{PORT}/CoolApp/` :

```csharp
endpoints.MapFallbackToPage("/CoolApp/{**path:nonfile}");
```

**Hostowanie wielu Blazor WebAssembly aplikacji**

Aby uzyskać więcej informacji na temat hostowania wielu Blazor WebAssembly aplikacji w hostowanym Blazor rozwiązaniu, zobacz <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps> .

## <a name="deployment"></a>Wdrożenie

Aby uzyskać wskazówki dotyczące wdrażania, zobacz następujące tematy:

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>
