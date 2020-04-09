---
title: Co nowego w ASP.NET Core 3.1
author: rick-anderson
description: Dowiedz się więcej o nowych funkcjach w ASP.NET Core 3.1.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: f375022ad3ebdea2990f626320ef295926f88c22
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662709"
---
# <a name="whats-new-in-aspnet-core-31"></a>Co nowego w ASP.NET Core 3.1

W tym artykule przedstawiono najważniejsze zmiany w ASP.NET Core 3.1 z łączami do odpowiedniej dokumentacji.

## <a name="partial-class-support-for-razor-components"></a>Częściowa obsługa klas dla komponentów Razor

Komponenty maszynki do golenia są teraz generowane jako klasy częściowe. Kod dla składnika Razor mogą być zapisywane przy użyciu pliku pozoru kodu zdefiniowane jako klasy częściowej, a nie definiowanie całego kodu dla składnika w jednym pliku. Aby uzyskać więcej informacji, zobacz [Częściowa obsługa klas](xref:blazor/components#partial-class-support).

## <a name="opno-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>BlazorPomocnik znaczników składników i przekazuje parametry do komponentów najwyższego poziomu

W Blazor przypadku ASP.NET Core 3.0 składniki były renderowane na strony i`Html.RenderComponentAsync`widoki za pomocą pomocnika HTML ( ). W ASP.NET Core 3.1 renderuj składnik ze strony lub widoku za pomocą nowego pomocnika tagu komponentu:

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

Pomocnik HTML pozostaje obsługiwany w ASP.NET Core 3.1, ale zaleca się pomocnika tagu składnika.

BlazorAplikacje serwera mogą teraz przekazywać parametry do składników najwyższego poziomu podczas renderowania początkowego. Wcześniej można było przekazywać parametry tylko do składnika najwyższego poziomu za pomocą [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static). W tej wersji obsługiwane są zarówno [RenderMode.Server,](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) jak i [RenderModel.ServerPrerendered.](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) Wszystkie określone wartości parametrów są serializowane jako JSON i uwzględniane w odpowiedzi początkowej.

Na przykład prierender `Counter` składnik z kwotą`IncrementAmount`przyrostu ( ):

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

Aby uzyskać więcej informacji, zobacz [Integrowanie składników ze stronami Razor i aplikacjami MVC](xref:blazor/integrate-components).

## <a name="support-for-shared-queues-in-httpsys"></a>Obsługa kolejek udostępnionych w pliku HTTP.sys

[Http.sys](xref:fundamentals/servers/httpsys) obsługuje tworzenie anonimowych kolejek żądań. W ASP.NET Core 3.1 dodaliśmy możliwość tworzenia lub dołączania do istniejącej kolejki żądań HTTP.sys o nazwie HTTP.sys. Tworzenie lub dołączanie do istniejącej kolejki żądań HTTP.sys o nazwie HTTP.sys umożliwia scenariusze, w których proces kontrolera HTTP.sys, który jest właścicielem kolejki, jest niezależny od procesu odbiornika. Ta niezależność umożliwia zachowanie istniejących połączeń i żądań w kolejce między ponownym uruchomieniem procesu odbiornika:

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a>Przełomowe zmiany dla plików cookie SameSite

Zachowanie plików cookie SameSite uległo zmianie, aby odzwierciedlić nadchodzące zmiany w przeglądarce. Może to mieć wpływ na scenariusze uwierzytelniania, takie jak AzureAd, OpenIdConnect lub WsFederation. Aby uzyskać więcej informacji, zobacz <xref:security/samesite>.

## <a name="prevent-default-actions-for-events-in-opno-locblazor-apps"></a>Zapobieganie domyślnym działaniom zdarzeń w Blazor aplikacjach

Użyj `@on{EVENT}:preventDefault` atrybutu dyrektywy, aby zapobiec akcji domyślnej dla zdarzenia. W poniższym przykładzie domyślna akcja wyświetlania znaku klucza w polu tekstowym jest uniemożliwiona:

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

Aby uzyskać więcej informacji, zobacz [Zapobieganie działaniom domyślnym](xref:blazor/event-handling#prevent-default-actions).

## <a name="stop-event-propagation-in-opno-locblazor-apps"></a>Zatrzymywać propagację zdarzeń w Blazor aplikacjach

Użyj `@on{EVENT}:stopPropagation` atrybutu dyrektywy, aby zatrzymać propagację zdarzeń. W poniższym przykładzie zaznaczenie pola wyboru zapobiega `<div>` propagacji zdarzeń kliknięć od strony podrzędnej do nadrzędnego: `<div>`

```razor
<input @bind="_stopPropagation" type="checkbox" />

<div @onclick="OnSelectParentDiv">
    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        ...
    </div>
</div>

@code {
    private bool _stopPropagation = false;
}
```

Aby uzyskać więcej informacji, zobacz [Stop propagacji zdarzeń](xref:blazor/event-handling#stop-event-propagation).

## <a name="detailed-errors-during-opno-locblazor-app-development"></a>Szczegółowe błędy Blazor podczas tworzenia aplikacji

Gdy Blazor aplikacja nie działa poprawnie podczas tworzenia, otrzymywanie szczegółowych informacji o błędzie z aplikacji pomaga w rozwiązywaniu problemów i rozwiązywaniu problemu. W przypadku wystąpienia Blazor błędu aplikacje wyświetlają złoty pasek u dołu ekranu:

* Podczas tworzenia złotego paska kieruje cię do konsoli przeglądarki, gdzie można zobaczyć wyjątek.
* W produkcji złoty pasek powiadamia użytkownika o wystąpieniu błędu i zaleca odświeżenie przeglądarki.

Aby uzyskać więcej informacji, zobacz [Szczegółowe błędy podczas opracowywania](xref:blazor/handle-errors#detailed-errors-during-development).
