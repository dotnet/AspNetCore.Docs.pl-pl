---
title: "Manage Users and Groups in SignalR " Author: bradygaster Description: "Overview of ASP.NET Core SignalR Zarządzanie użytkownikami i grupami".
monikerRange: ">= aspnetcore-2,1" MS. Author: bradyg MS. Custom: MVC MS. Date: 05/17/2020 No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- " SignalR UID: sygnał/grupy

---

# <a name="manage-users-and-groups-in-signalr"></a>Zarządzanie użytkownikami i grupami w programieSignalR

Autor [Brennan Conroy](https://github.com/BrennanConroy)

SignalRumożliwia wysyłanie komunikatów do wszystkich połączeń skojarzonych z określonym użytkownikiem, a także do nazwanych grup połączeń.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(jak pobrać)](xref:index#how-to-download-a-sample)

## <a name="users-in-signalr"></a>Użytkownicy wSignalR

Pojedynczy użytkownik w programie SignalR może mieć wiele połączeń z aplikacją. Na przykład użytkownik może być połączony na swoim komputerze, a także na telefonie. Każde urządzenie ma oddzielne SignalR połączenie, ale wszystkie są skojarzone z tym samym użytkownikiem. Jeśli wiadomość jest wysyłana do użytkownika, wszystkie połączenia skojarzone z tym użytkownikiem otrzymują komunikat. Do identyfikatora użytkownika dla połączenia można uzyskać dostęp do `Context.UserIdentifier` właściwości w centrum.

Domyślnie program SignalR używa programu `ClaimTypes.NameIdentifier` ze `ClaimsPrincipal` skojarzonego z połączeniem jako identyfikator użytkownika. Aby dostosować to zachowanie, zobacz [Używanie oświadczeń do dostosowywania obsługi tożsamości](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).

Wyślij wiadomość do określonego użytkownika, przekazując identyfikator użytkownika do `User` funkcji w metodzie centrum, jak pokazano w następującym przykładzie:

> [!NOTE]
> W identyfikatorze użytkownika jest rozróżniana wielkość liter.

[!code-csharp[Configure service](groups/sample/Hubs/ChatHub.cs?range=29-32)]

## <a name="groups-in-signalr"></a>Grupy wSignalR

Grupa jest kolekcją połączeń skojarzonych z nazwą. Komunikaty mogą być wysyłane do wszystkich połączeń w grupie. Grupy są zalecanym sposobem wysyłania do połączenia lub wielu połączeń, ponieważ grupy są zarządzane przez aplikację. Połączenie może być członkiem wielu grup. Grupy są idealnym rozwiązaniem dla aplikacji czatu, gdzie każde pomieszczenie może być reprezentowane jako Grupa. Połączenia są dodawane lub usuwane z grup za pośrednictwem `AddToGroupAsync` `RemoveFromGroupAsync` metod i.

[!code-csharp[Hub methods](groups/sample/Hubs/ChatHub.cs?range=15-27)]

Członkostwo w grupie nie jest zachowywane po ponownym nawiązaniu połączenia. Połączenie musi ponownie dołączyć do grupy po jej ponownym ustanowieniu. Nie jest możliwe liczenie członków grupy, ponieważ te informacje są niedostępne, jeśli aplikacja jest skalowana na wielu serwerach.

Aby chronić dostęp do zasobów przy użyciu grup, użyj funkcji [uwierzytelniania i autoryzacji](xref:signalr/authn-and-authz) w ASP.NET Core. Jeśli użytkownik zostanie dodany do grupy tylko wtedy, gdy poświadczenia są prawidłowe dla tej grupy, komunikaty wysyłane do tej grupy będą przechodzenie tylko do autoryzowanych użytkowników. Jednak grupy nie są funkcją zabezpieczeń. Oświadczenia uwierzytelniania mają funkcje, które nie są, takie jak wygaśnięcie i odwoływanie. Jeśli uprawnienie użytkownika do uzyskiwania dostępu do grupy zostało odwołane, aplikacja musi jawnie usunąć użytkownika z grupy.

> [!NOTE]
> W nazwach grup jest rozróżniana wielkość liter.

## <a name="related-resources"></a>Powiązane zasoby

* [Wprowadzenie](xref:tutorials/signalr)
* [Centra](xref:signalr/hubs)
* [Publikowanie na platformie Azure](xref:signalr/publish-to-azure-web-app)
