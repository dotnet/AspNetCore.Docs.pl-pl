---
title: Zarządzanie użytkownikami i grupami w programieSignalR
author: bradygaster
description: Przegląd ASP.NET Core SignalR zarządzania użytkownikami i grupami.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 05/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/groups
ms.openlocfilehash: dfa989654069bc85f0a936751dbabe384c63e917
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2020
ms.locfileid: "83550001"
---
# <a name="manage-users-and-groups-in-signalr"></a><span data-ttu-id="e430c-103">Zarządzanie użytkownikami i grupami w programieSignalR</span><span class="sxs-lookup"><span data-stu-id="e430c-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="e430c-104">Autor [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="e430c-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

SignalR<span data-ttu-id="e430c-105">umożliwia wysyłanie komunikatów do wszystkich połączeń skojarzonych z określonym użytkownikiem, a także do nazwanych grup połączeń.</span><span class="sxs-lookup"><span data-stu-id="e430c-105"> allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="e430c-106">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(jak pobrać)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="e430c-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-signalr"></a><span data-ttu-id="e430c-107">Użytkownicy wSignalR</span><span class="sxs-lookup"><span data-stu-id="e430c-107">Users in SignalR</span></span>

<span data-ttu-id="e430c-108">Pojedynczy użytkownik w programie SignalR może mieć wiele połączeń z aplikacją.</span><span class="sxs-lookup"><span data-stu-id="e430c-108">A single user in SignalR can have multiple connections to an app.</span></span> <span data-ttu-id="e430c-109">Na przykład użytkownik może być połączony na swoim komputerze, a także na telefonie.</span><span class="sxs-lookup"><span data-stu-id="e430c-109">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="e430c-110">Każde urządzenie ma oddzielne SignalR połączenie, ale wszystkie są skojarzone z tym samym użytkownikiem.</span><span class="sxs-lookup"><span data-stu-id="e430c-110">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="e430c-111">Jeśli wiadomość jest wysyłana do użytkownika, wszystkie połączenia skojarzone z tym użytkownikiem otrzymują komunikat.</span><span class="sxs-lookup"><span data-stu-id="e430c-111">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="e430c-112">Do identyfikatora użytkownika dla połączenia można uzyskać dostęp do `Context.UserIdentifier` właściwości w centrum.</span><span class="sxs-lookup"><span data-stu-id="e430c-112">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in the hub.</span></span>

<span data-ttu-id="e430c-113">Domyślnie program SignalR używa programu `ClaimTypes.NameIdentifier` ze `ClaimsPrincipal` skojarzonego z połączeniem jako identyfikator użytkownika.</span><span class="sxs-lookup"><span data-stu-id="e430c-113">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="e430c-114">Aby dostosować to zachowanie, zobacz [Używanie oświadczeń do dostosowywania obsługi tożsamości](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).</span><span class="sxs-lookup"><span data-stu-id="e430c-114">To customize this behavior, see [Use claims to customize identity handling](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).</span></span>

<span data-ttu-id="e430c-115">Wyślij wiadomość do określonego użytkownika, przekazując identyfikator użytkownika do `User` funkcji w metodzie centrum, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="e430c-115">Send a message to a specific user by passing the user identifier to the `User` function in a hub method, as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="e430c-116">W identyfikatorze użytkownika jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="e430c-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/Hubs/ChatHub.cs?range=29-32)]

## <a name="groups-in-signalr"></a><span data-ttu-id="e430c-117">Grupy wSignalR</span><span class="sxs-lookup"><span data-stu-id="e430c-117">Groups in SignalR</span></span>

<span data-ttu-id="e430c-118">Grupa jest kolekcją połączeń skojarzonych z nazwą.</span><span class="sxs-lookup"><span data-stu-id="e430c-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="e430c-119">Komunikaty mogą być wysyłane do wszystkich połączeń w grupie.</span><span class="sxs-lookup"><span data-stu-id="e430c-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="e430c-120">Grupy są zalecanym sposobem wysyłania do połączenia lub wielu połączeń, ponieważ grupy są zarządzane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="e430c-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="e430c-121">Połączenie może być członkiem wielu grup.</span><span class="sxs-lookup"><span data-stu-id="e430c-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="e430c-122">Grupy są idealnym rozwiązaniem dla aplikacji czatu, gdzie każde pomieszczenie może być reprezentowane jako Grupa.</span><span class="sxs-lookup"><span data-stu-id="e430c-122">Groups are ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="e430c-123">Połączenia są dodawane lub usuwane z grup za pośrednictwem `AddToGroupAsync` `RemoveFromGroupAsync` metod i.</span><span class="sxs-lookup"><span data-stu-id="e430c-123">Connections are added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/Hubs/ChatHub.cs?range=15-27)]

<span data-ttu-id="e430c-124">Członkostwo w grupie nie jest zachowywane po ponownym nawiązaniu połączenia.</span><span class="sxs-lookup"><span data-stu-id="e430c-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="e430c-125">Połączenie musi ponownie dołączyć do grupy po jej ponownym ustanowieniu.</span><span class="sxs-lookup"><span data-stu-id="e430c-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="e430c-126">Nie jest możliwe liczenie członków grupy, ponieważ te informacje są niedostępne, jeśli aplikacja jest skalowana na wielu serwerach.</span><span class="sxs-lookup"><span data-stu-id="e430c-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="e430c-127">Aby chronić dostęp do zasobów przy użyciu grup, użyj funkcji [uwierzytelniania i autoryzacji](xref:signalr/authn-and-authz) w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e430c-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="e430c-128">Jeśli użytkownik zostanie dodany do grupy tylko wtedy, gdy poświadczenia są prawidłowe dla tej grupy, komunikaty wysyłane do tej grupy będą przechodzenie tylko do autoryzowanych użytkowników.</span><span class="sxs-lookup"><span data-stu-id="e430c-128">If a user is added to a group only when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="e430c-129">Jednak grupy nie są funkcją zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="e430c-129">However, groups are not a security feature.</span></span> <span data-ttu-id="e430c-130">Oświadczenia uwierzytelniania mają funkcje, które nie są, takie jak wygaśnięcie i odwoływanie.</span><span class="sxs-lookup"><span data-stu-id="e430c-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="e430c-131">Jeśli uprawnienie użytkownika do uzyskiwania dostępu do grupy zostało odwołane, aplikacja musi jawnie usunąć użytkownika z grupy.</span><span class="sxs-lookup"><span data-stu-id="e430c-131">If a user's permission to access the group is revoked, the app must remove the user from the group explicitly.</span></span>

> [!NOTE]
> <span data-ttu-id="e430c-132">W nazwach grup jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="e430c-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="e430c-133">Powiązane zasoby</span><span class="sxs-lookup"><span data-stu-id="e430c-133">Related resources</span></span>

* [<span data-ttu-id="e430c-134">Rozpoczęcie pracy</span><span class="sxs-lookup"><span data-stu-id="e430c-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="e430c-135">Centra</span><span class="sxs-lookup"><span data-stu-id="e430c-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="e430c-136">Publikowanie na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="e430c-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
