---
title: Co nowego w ASP.NET Core 3,1
author: rick-anderson
description: Dowiedz się więcej o nowych funkcjach w ASP.NET Core 3,1.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: 67fc972676549a02265035c129c513f11d303d51
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774050"
---
# <a name="whats-new-in-aspnet-core-31"></a>Co nowego w ASP.NET Core 3,1

W tym artykule przedstawiono najbardziej znaczące zmiany w ASP.NET Core 3,1 z linkami do odpowiedniej dokumentacji.

## <a name="partial-class-support-for-razor-components"></a>Obsługa częściowej klasy Razor dla składników

Razorskładniki są teraz generowane jako klasy częściowe. Kod Razor składnika można napisać przy użyciu pliku powiązanego z kodem zdefiniowanego jako Klasa częściowa zamiast definiować cały kod dla składnika w pojedynczym pliku. Aby uzyskać więcej informacji, zobacz temat [Obsługa klasy częściowej](xref:blazor/components#partial-class-support).

## <a name="blazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>BlazorPomocnik tagu składnika i przekazywanie parametrów do składników najwyższego poziomu

W Blazor programie z ASP.NET Core 3,0 składniki były renderowane na stronach i w widokach za pomocą pomocnika HTML (`Html.RenderComponentAsync`). W ASP.NET Core 3,1 Renderuj składnik ze strony lub widoku przy użyciu nowego pomocnika tagów składnika:

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

Pomocnik HTML pozostaje obsługiwany w ASP.NET Core 3,1, ale zaleca się pomocnika tagów składnika.

BlazorAplikacje serwera mogą teraz przekazywać parametry do składników najwyższego poziomu podczas początkowego renderowania. Wcześniej można było przekazać parametry do składnika najwyższego poziomu za pomocą elementu [RenderMode. static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static). W tej wersji obsługiwane są zarówno metody [RenderMode. Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) , jak i [RenderModel. ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) . Wszystkie określone wartości parametrów są serializowane jako kod JSON i zawarte w początkowej odpowiedzi.

Na przykład wyprerender `Counter` składnik o wartości przyrostowej (`IncrementAmount`):

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

Aby uzyskać więcej informacji, zobacz [integrowanie Razor składników na stronach i aplikacjach MVC](xref:blazor/integrate-components).

## <a name="support-for-shared-queues-in-httpsys"></a>Obsługa kolejek udostępnionych w pliku HTTP. sys

[Http. sys](xref:fundamentals/servers/httpsys) obsługuje tworzenie anonimowych kolejek żądań. W ASP.NET Core 3,1 dodaliśmy do możliwości tworzenia lub dołączania istniejącej nazwanej kolejki żądań HTTP. sys. Utworzenie lub dołączenie istniejącej kolejki żądań HTTP. sys umożliwia scenariusze, w których proces kontrolera HTTP. sys, który jest właścicielem kolejki, jest niezależny od procesu odbiornika. Ta niezależność umożliwia zachowywanie istniejących połączeń i zakolejce żądań między ponownymi uruchomieniami procesu odbiornika:

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a>Istotne zmiany plików cookie SameSite

Zachowanie plików cookie SameSite zostało zmienione w celu odzwierciedlenia przyszłych zmian przeglądarki. Może to mieć wpływ na scenariusze uwierzytelniania, takie jak AzureAd, OpenIdConnect lub WsFederation. Aby uzyskać więcej informacji, zobacz <xref:security/samesite>.

## <a name="prevent-default-actions-for-events-in-blazor-apps"></a>Zapobiegaj domyślnym akcjom dla Blazor zdarzeń w aplikacjach

Użyj atrybutu `@on{EVENT}:preventDefault` dyrektywy, aby zapobiec domyślnej akcji dla zdarzenia. W poniższym przykładzie jest blokowane domyślne działanie wyświetlania znaku klucza w polu tekstowym:

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

Aby uzyskać więcej informacji, zobacz [zapobieganie domyślnym akcjom](xref:blazor/event-handling#prevent-default-actions).

## <a name="stop-event-propagation-in-blazor-apps"></a>Zatrzymaj propagację zdarzeń w Blazor aplikacjach

Użyj atrybutu `@on{EVENT}:stopPropagation` dyrektywy, aby zatrzymać propagację zdarzeń. W poniższym przykładzie, zaznaczając pole wyboru, Zapobiegaj kliknięciu zdarzeń z elementu `<div>` podrzędnego od propagowania do `<div>`elementu nadrzędnego:

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

Aby uzyskać więcej informacji, zobacz sekcję [Zatrzymaj propagację zdarzeń](xref:blazor/event-handling#stop-event-propagation).

## <a name="detailed-errors-during-blazor-app-development"></a>Szczegóły błędów podczas Blazor opracowywania aplikacji

Gdy Blazor aplikacja nie działa prawidłowo podczas opracowywania, otrzymuje szczegółowe informacje o błędzie z aplikacji, które pomagają w rozwiązywaniu problemów i rozwiązaniu problemu. Gdy wystąpi błąd, Blazor aplikacje wyświetlają złoty pasek u dołu ekranu:

* W trakcie programowania złoty pasek kieruje użytkownika do konsoli przeglądarki, gdzie można zobaczyć wyjątek.
* W środowisku produkcyjnym złoty pasek powiadamia użytkownika o wystąpieniu błędu i zaleca odświeżenie przeglądarki.

Aby uzyskać więcej informacji, zobacz [szczegóły błędów podczas opracowywania](xref:blazor/handle-errors#detailed-errors-during-development).
