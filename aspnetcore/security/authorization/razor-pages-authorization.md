---
title: RazorKonwencje autoryzacji stron w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak kontrolować dostęp do stron z konwencjami, które autoryzują użytkowników i umożliwiają anonimowym użytkownikom dostęp do stron lub folderów stron.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 5e0c76ea6c6b4af8e24693b1bfe8ac28bf2ce3a9
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022280"
---
# <a name="no-locrazor-pages-authorization-conventions-in-aspnet-core"></a>RazorKonwencje autoryzacji stron w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Jednym ze sposobów kontroli dostępu w Razor aplikacji stron jest korzystanie z Konwencji autoryzacji podczas uruchamiania. Konwencje te umożliwiają Autoryzowanie użytkowników i Zezwalanie użytkownikom anonimowym na dostęp do poszczególnych stron lub folderów stron. Konwencje opisane w tym temacie automatycznie stosują [filtry autoryzacji](xref:mvc/controllers/filters#authorization-filters) do kontroli dostępu.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykładowa aplikacja używa [ cookie uwierzytelniania bez ASP.NET Core Identity ](xref:security/authentication/cookie). Koncepcje i Przykłady przedstawione w tym temacie dotyczą również aplikacji korzystających z ASP.NET Core Identity . Aby użyć ASP.NET Core Identity , postępuj zgodnie ze wskazówkami w temacie <xref:security/authentication/identity> .

## <a name="require-authorization-to-access-a-page"></a>Wymagaj autoryzacji dostępu do strony

Użyj <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Konwencji, aby dodać <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do strony w określonej ścieżce:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=3)]

Określona ścieżka jest ścieżką aparatu widoku, która jest Razor ścieżką względną głównej ścieżki bez rozszerzenia i zawierającą tylko ukośniki.

Aby określić [zasady autoryzacji](xref:security/authorization/policies), użyj [przeciążenia AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>Można zastosować do klasy modelu strony z `[Authorize]` atrybutem Filter. Aby uzyskać więcej informacji, zobacz temat [Autoryzuj atrybut Filter](xref:razor-pages/filter#authorize-filter-attribute).

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Wymagaj autoryzacji dostępu do folderu stron

Użyj <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Konwencji, aby dodać <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do wszystkich stron w folderze w określonej ścieżce:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=4)]

Określona ścieżka jest ścieżką aparatu widoku, czyli Razor ścieżką względną głównej strony.

Aby określić [zasady autoryzacji](xref:security/authorization/policies), użyj [przeciążenia AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>Wymagaj autoryzacji dostępu do strony obszaru

Użyj <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Konwencji, aby dodać <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do strony obszaru w określonej ścieżce:

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

Nazwa strony jest ścieżką pliku bez rozszerzenia względem katalogu głównego stron określonego obszaru. Na przykład nazwa strony dla *obszaru plik/ Identity /Pages/Manage/accounts.cshtml* to */Manage/accounts*.

Aby określić [zasady autoryzacji](xref:security/authorization/policies), użyj [przeciążenia AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>Wymagaj autoryzacji dostępu do folderu obszarów

Użyj <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Konwencji, aby dodać <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do wszystkich obszarów w folderze w określonej ścieżce:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

Ścieżka folderu to ścieżka folderu względem katalogu głównego stron określonego obszaru. Na przykład ścieżka folderu dla plików w obszarze *obszary/ Identity /Pages/Manage/* to */Manage*.

Aby określić [zasady autoryzacji](xref:security/authorization/policies), użyj [przeciążenia AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>Zezwalaj na dostęp anonimowy do strony

Użyj <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> Konwencji, aby dodać <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> do strony w określonej ścieżce:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=5)]

Określona ścieżka jest ścieżką aparatu widoku, która jest Razor ścieżką względną głównej ścieżki bez rozszerzenia i zawierającą tylko ukośniki.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Zezwalaj na dostęp anonimowy do folderu stron

Użyj <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Konwencji, aby dodać <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> do wszystkich stron w folderze w określonej ścieżce:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=6)]

Określona ścieżka jest ścieżką aparatu widoku, czyli Razor ścieżką względną głównej strony.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>Uwaga na temat łączenia autoryzowanych i anonimowych dostępu

Należy określić, że folder stron wymaga autoryzacji, a następnie określić, że strona w tym folderze zezwala na dostęp anonimowy:

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

Odwrócenie jest jednak nieprawidłowe. Nie można zadeklarować folderu stron dla dostępu anonimowego, a następnie określić stronę w tym folderze, która wymaga autoryzacji:

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

Wymaganie autoryzacji na stronie prywatnej kończy się niepowodzeniem. Gdy obie <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> i <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> są stosowane do strony, <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> ma pierwszeństwo i kontroluje dostęp.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Jednym ze sposobów kontroli dostępu w Razor aplikacji stron jest korzystanie z Konwencji autoryzacji podczas uruchamiania. Konwencje te umożliwiają Autoryzowanie użytkowników i Zezwalanie użytkownikom anonimowym na dostęp do poszczególnych stron lub folderów stron. Konwencje opisane w tym temacie automatycznie stosują [filtry autoryzacji](xref:mvc/controllers/filters#authorization-filters) do kontroli dostępu.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykładowa aplikacja używa [ cookie uwierzytelniania bez ASP.NET Core Identity ](xref:security/authentication/cookie). Koncepcje i Przykłady przedstawione w tym temacie dotyczą również aplikacji korzystających z ASP.NET Core Identity . Aby użyć ASP.NET Core Identity , postępuj zgodnie ze wskazówkami w temacie <xref:security/authentication/identity> .

## <a name="require-authorization-to-access-a-page"></a>Wymagaj autoryzacji dostępu do strony

Użyj <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Konwencji przez, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Aby dodać <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do strony w określonej ścieżce:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

Określona ścieżka jest ścieżką aparatu widoku, która jest Razor ścieżką względną głównej ścieżki bez rozszerzenia i zawierającą tylko ukośniki.

Aby określić [zasady autoryzacji](xref:security/authorization/policies), użyj [przeciążenia AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>Można zastosować do klasy modelu strony z `[Authorize]` atrybutem Filter. Aby uzyskać więcej informacji, zobacz temat [Autoryzuj atrybut Filter](xref:razor-pages/filter#authorize-filter-attribute).

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Wymagaj autoryzacji dostępu do folderu stron

Użyj <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Konwencji przez, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Aby dodać <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do wszystkich stron w folderze w określonej ścieżce:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

Określona ścieżka jest ścieżką aparatu widoku, czyli Razor ścieżką względną głównej strony.

Aby określić [zasady autoryzacji](xref:security/authorization/policies), użyj [przeciążenia AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>Wymagaj autoryzacji dostępu do strony obszaru

Użyj <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Konwencji przez, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Aby dodać <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do strony obszaru w określonej ścieżce:

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

Nazwa strony jest ścieżką pliku bez rozszerzenia względem katalogu głównego stron określonego obszaru. Na przykład nazwa strony dla *obszaru plik/ Identity /Pages/Manage/accounts.cshtml* to */Manage/accounts*.

Aby określić [zasady autoryzacji](xref:security/authorization/policies), użyj [przeciążenia AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>Wymagaj autoryzacji dostępu do folderu obszarów

Użyj <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Konwencji przez, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Aby dodać <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do wszystkich obszarów w folderze w określonej ścieżce:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

Ścieżka folderu to ścieżka folderu względem katalogu głównego stron określonego obszaru. Na przykład ścieżka folderu dla plików w obszarze *obszary/ Identity /Pages/Manage/* to */Manage*.

Aby określić [zasady autoryzacji](xref:security/authorization/policies), użyj [przeciążenia AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>Zezwalaj na dostęp anonimowy do strony

Użyj <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> Konwencji przez, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Aby dodać <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> do strony w określonej ścieżce:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

Określona ścieżka jest ścieżką aparatu widoku, która jest Razor ścieżką względną głównej ścieżki bez rozszerzenia i zawierającą tylko ukośniki.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Zezwalaj na dostęp anonimowy do folderu stron

Użyj <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Konwencji przez, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Aby dodać <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> do wszystkich stron w folderze w określonej ścieżce:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

Określona ścieżka jest ścieżką aparatu widoku, czyli Razor ścieżką względną głównej strony.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>Uwaga na temat łączenia autoryzowanych i anonimowych dostępu

Prawidłowe jest określenie, że folder stron, które wymagają autoryzacji, a nie określa, że strona w tym folderze zezwala na dostęp anonimowy:

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

Odwrócenie jest jednak nieprawidłowe. Nie można zadeklarować folderu stron dla dostępu anonimowego, a następnie określić stronę w tym folderze, która wymaga autoryzacji:

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

Wymaganie autoryzacji na stronie prywatnej kończy się niepowodzeniem. Gdy obie <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> i <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> są stosowane do strony, <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> ma pierwszeństwo i kontroluje dostęp.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
