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
# <a name="whats-new-in-aspnet-core-31"></a><span data-ttu-id="4fcf8-103">Co nowego w ASP.NET Core 3.1</span><span class="sxs-lookup"><span data-stu-id="4fcf8-103">What's new in ASP.NET Core 3.1</span></span>

<span data-ttu-id="4fcf8-104">W tym artykule przedstawiono najważniejsze zmiany w ASP.NET Core 3.1 z łączami do odpowiedniej dokumentacji.</span><span class="sxs-lookup"><span data-stu-id="4fcf8-104">This article highlights the most significant changes in ASP.NET Core 3.1 with links to relevant documentation.</span></span>

## <a name="partial-class-support-for-razor-components"></a><span data-ttu-id="4fcf8-105">Częściowa obsługa klas dla komponentów Razor</span><span class="sxs-lookup"><span data-stu-id="4fcf8-105">Partial class support for Razor components</span></span>

<span data-ttu-id="4fcf8-106">Komponenty maszynki do golenia są teraz generowane jako klasy częściowe.</span><span class="sxs-lookup"><span data-stu-id="4fcf8-106">Razor components are now generated as partial classes.</span></span> <span data-ttu-id="4fcf8-107">Kod dla składnika Razor mogą być zapisywane przy użyciu pliku pozoru kodu zdefiniowane jako klasy częściowej, a nie definiowanie całego kodu dla składnika w jednym pliku.</span><span class="sxs-lookup"><span data-stu-id="4fcf8-107">Code for a Razor component can be written using a code-behind file defined as a partial class rather than defining all the code for the component in a single file.</span></span> <span data-ttu-id="4fcf8-108">Aby uzyskać więcej informacji, zobacz [Częściowa obsługa klas](xref:blazor/components#partial-class-support).</span><span class="sxs-lookup"><span data-stu-id="4fcf8-108">For more information, see [Partial class support](xref:blazor/components#partial-class-support).</span></span>

## <a name="opno-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Blazor<span data-ttu-id="4fcf8-109">Pomocnik znaczników składników i przekazuje parametry do komponentów najwyższego poziomu</span><span class="sxs-lookup"><span data-stu-id="4fcf8-109"> Component Tag Helper and pass parameters to top-level components</span></span>

<span data-ttu-id="4fcf8-110">W Blazor przypadku ASP.NET Core 3.0 składniki były renderowane na strony i`Html.RenderComponentAsync`widoki za pomocą pomocnika HTML ( ).</span><span class="sxs-lookup"><span data-stu-id="4fcf8-110">In Blazor with ASP.NET Core 3.0, components were rendered into pages and views using an HTML Helper (`Html.RenderComponentAsync`).</span></span> <span data-ttu-id="4fcf8-111">W ASP.NET Core 3.1 renderuj składnik ze strony lub widoku za pomocą nowego pomocnika tagu komponentu:</span><span class="sxs-lookup"><span data-stu-id="4fcf8-111">In ASP.NET Core 3.1, render a component from a page or view with the new Component Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="4fcf8-112">Pomocnik HTML pozostaje obsługiwany w ASP.NET Core 3.1, ale zaleca się pomocnika tagu składnika.</span><span class="sxs-lookup"><span data-stu-id="4fcf8-112">The HTML Helper remains supported in ASP.NET Core 3.1, but the Component Tag Helper is recommended.</span></span>

Blazor<span data-ttu-id="4fcf8-113">Aplikacje serwera mogą teraz przekazywać parametry do składników najwyższego poziomu podczas renderowania początkowego.</span><span class="sxs-lookup"><span data-stu-id="4fcf8-113"> Server apps can now pass parameters to top-level components during the initial render.</span></span> <span data-ttu-id="4fcf8-114">Wcześniej można było przekazywać parametry tylko do składnika najwyższego poziomu za pomocą [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span><span class="sxs-lookup"><span data-stu-id="4fcf8-114">Previously you could only pass parameters to a top-level component with [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span></span> <span data-ttu-id="4fcf8-115">W tej wersji obsługiwane są zarówno [RenderMode.Server,](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) jak i [RenderModel.ServerPrerendered.](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered)</span><span class="sxs-lookup"><span data-stu-id="4fcf8-115">With this release, both [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) and [RenderModel.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) are supported.</span></span> <span data-ttu-id="4fcf8-116">Wszystkie określone wartości parametrów są serializowane jako JSON i uwzględniane w odpowiedzi początkowej.</span><span class="sxs-lookup"><span data-stu-id="4fcf8-116">Any specified parameter values are serialized as JSON and included in the initial response.</span></span>

<span data-ttu-id="4fcf8-117">Na przykład prierender `Counter` składnik z kwotą`IncrementAmount`przyrostu ( ):</span><span class="sxs-lookup"><span data-stu-id="4fcf8-117">For example, prerender a `Counter` component with an increment amount (`IncrementAmount`):</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="4fcf8-118">Aby uzyskać więcej informacji, zobacz [Integrowanie składników ze stronami Razor i aplikacjami MVC](xref:blazor/integrate-components).</span><span class="sxs-lookup"><span data-stu-id="4fcf8-118">For more information, see [Integrate components into Razor Pages and MVC apps](xref:blazor/integrate-components).</span></span>

## <a name="support-for-shared-queues-in-httpsys"></a><span data-ttu-id="4fcf8-119">Obsługa kolejek udostępnionych w pliku HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="4fcf8-119">Support for shared queues in HTTP.sys</span></span>

<span data-ttu-id="4fcf8-120">[Http.sys](xref:fundamentals/servers/httpsys) obsługuje tworzenie anonimowych kolejek żądań.</span><span class="sxs-lookup"><span data-stu-id="4fcf8-120">[HTTP.sys](xref:fundamentals/servers/httpsys) supports creating anonymous request queues.</span></span> <span data-ttu-id="4fcf8-121">W ASP.NET Core 3.1 dodaliśmy możliwość tworzenia lub dołączania do istniejącej kolejki żądań HTTP.sys o nazwie HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="4fcf8-121">In ASP.NET Core 3.1, we've added to ability to create or attach to an existing named HTTP.sys request queue.</span></span> <span data-ttu-id="4fcf8-122">Tworzenie lub dołączanie do istniejącej kolejki żądań HTTP.sys o nazwie HTTP.sys umożliwia scenariusze, w których proces kontrolera HTTP.sys, który jest właścicielem kolejki, jest niezależny od procesu odbiornika.</span><span class="sxs-lookup"><span data-stu-id="4fcf8-122">Creating or attaching to an existing named HTTP.sys request queue enables scenarios where the HTTP.sys controller process that owns the queue is independent of the listener process.</span></span> <span data-ttu-id="4fcf8-123">Ta niezależność umożliwia zachowanie istniejących połączeń i żądań w kolejce między ponownym uruchomieniem procesu odbiornika:</span><span class="sxs-lookup"><span data-stu-id="4fcf8-123">This independence makes it possible to preserve existing connections and enqueued requests between listener process restarts:</span></span>

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a><span data-ttu-id="4fcf8-124">Przełomowe zmiany dla plików cookie SameSite</span><span class="sxs-lookup"><span data-stu-id="4fcf8-124">Breaking changes for SameSite cookies</span></span>

<span data-ttu-id="4fcf8-125">Zachowanie plików cookie SameSite uległo zmianie, aby odzwierciedlić nadchodzące zmiany w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="4fcf8-125">The behavior of SameSite cookies has changed to reflect upcoming browser changes.</span></span> <span data-ttu-id="4fcf8-126">Może to mieć wpływ na scenariusze uwierzytelniania, takie jak AzureAd, OpenIdConnect lub WsFederation.</span><span class="sxs-lookup"><span data-stu-id="4fcf8-126">This may affect authentication scenarios like AzureAd, OpenIdConnect, or WsFederation.</span></span> <span data-ttu-id="4fcf8-127">Aby uzyskać więcej informacji, zobacz <xref:security/samesite>.</span><span class="sxs-lookup"><span data-stu-id="4fcf8-127">For more information, see <xref:security/samesite>.</span></span>

## <a name="prevent-default-actions-for-events-in-opno-locblazor-apps"></a><span data-ttu-id="4fcf8-128">Zapobieganie domyślnym działaniom zdarzeń w Blazor aplikacjach</span><span class="sxs-lookup"><span data-stu-id="4fcf8-128">Prevent default actions for events in Blazor apps</span></span>

<span data-ttu-id="4fcf8-129">Użyj `@on{EVENT}:preventDefault` atrybutu dyrektywy, aby zapobiec akcji domyślnej dla zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="4fcf8-129">Use the `@on{EVENT}:preventDefault` directive attribute to prevent the default action for an event.</span></span> <span data-ttu-id="4fcf8-130">W poniższym przykładzie domyślna akcja wyświetlania znaku klucza w polu tekstowym jest uniemożliwiona:</span><span class="sxs-lookup"><span data-stu-id="4fcf8-130">In the following example, the default action of displaying the key's character in the text box is prevented:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

<span data-ttu-id="4fcf8-131">Aby uzyskać więcej informacji, zobacz [Zapobieganie działaniom domyślnym](xref:blazor/event-handling#prevent-default-actions).</span><span class="sxs-lookup"><span data-stu-id="4fcf8-131">For more information, see [Prevent default actions](xref:blazor/event-handling#prevent-default-actions).</span></span>

## <a name="stop-event-propagation-in-opno-locblazor-apps"></a><span data-ttu-id="4fcf8-132">Zatrzymywać propagację zdarzeń w Blazor aplikacjach</span><span class="sxs-lookup"><span data-stu-id="4fcf8-132">Stop event propagation in Blazor apps</span></span>

<span data-ttu-id="4fcf8-133">Użyj `@on{EVENT}:stopPropagation` atrybutu dyrektywy, aby zatrzymać propagację zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="4fcf8-133">Use the `@on{EVENT}:stopPropagation` directive attribute to stop event propagation.</span></span> <span data-ttu-id="4fcf8-134">W poniższym przykładzie zaznaczenie pola wyboru zapobiega `<div>` propagacji zdarzeń kliknięć od strony podrzędnej do nadrzędnego: `<div>`</span><span class="sxs-lookup"><span data-stu-id="4fcf8-134">In the following example, selecting the check box prevents click events from the child `<div>` from propagating to the parent `<div>`:</span></span>

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

<span data-ttu-id="4fcf8-135">Aby uzyskać więcej informacji, zobacz [Stop propagacji zdarzeń](xref:blazor/event-handling#stop-event-propagation).</span><span class="sxs-lookup"><span data-stu-id="4fcf8-135">For more information, see [Stop event propagation](xref:blazor/event-handling#stop-event-propagation).</span></span>

## <a name="detailed-errors-during-opno-locblazor-app-development"></a><span data-ttu-id="4fcf8-136">Szczegółowe błędy Blazor podczas tworzenia aplikacji</span><span class="sxs-lookup"><span data-stu-id="4fcf8-136">Detailed errors during Blazor app development</span></span>

<span data-ttu-id="4fcf8-137">Gdy Blazor aplikacja nie działa poprawnie podczas tworzenia, otrzymywanie szczegółowych informacji o błędzie z aplikacji pomaga w rozwiązywaniu problemów i rozwiązywaniu problemu.</span><span class="sxs-lookup"><span data-stu-id="4fcf8-137">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="4fcf8-138">W przypadku wystąpienia Blazor błędu aplikacje wyświetlają złoty pasek u dołu ekranu:</span><span class="sxs-lookup"><span data-stu-id="4fcf8-138">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="4fcf8-139">Podczas tworzenia złotego paska kieruje cię do konsoli przeglądarki, gdzie można zobaczyć wyjątek.</span><span class="sxs-lookup"><span data-stu-id="4fcf8-139">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="4fcf8-140">W produkcji złoty pasek powiadamia użytkownika o wystąpieniu błędu i zaleca odświeżenie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="4fcf8-140">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="4fcf8-141">Aby uzyskać więcej informacji, zobacz [Szczegółowe błędy podczas opracowywania](xref:blazor/handle-errors#detailed-errors-during-development).</span><span class="sxs-lookup"><span data-stu-id="4fcf8-141">For more information, see [Detailed errors during development](xref:blazor/handle-errors#detailed-errors-during-development).</span></span>
