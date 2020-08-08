---
title: Zewnętrzni dostawcy uwierzytelniania OAuth
author: rick-anderson
description: Odnajdywanie zewnętrznych dostawców uwierzytelniania OAuth, które współpracują z ASP.NET Core aplikacjami.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
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
uid: security/authentication/otherlogins
ms.openlocfilehash: 3de76b303c469341ba022fa3d33d3cd2a17581db
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014909"
---
# <a name="external-oauth-authentication-providers"></a><span data-ttu-id="93ca9-103">Zewnętrzni dostawcy uwierzytelniania OAuth</span><span class="sxs-lookup"><span data-stu-id="93ca9-103">External OAuth authentication providers</span></span>

<span data-ttu-id="93ca9-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd)i [Valeriy Novytskyy](https://github.com/01binary)</span><span class="sxs-lookup"><span data-stu-id="93ca9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd), and [Valeriy Novytskyy](https://github.com/01binary)</span></span>

<span data-ttu-id="93ca9-105">Poniższa lista zawiera typowe dostawcy uwierzytelniania OAuth zewnętrznych, którzy pracują z aplikacjami ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="93ca9-105">The following list includes common external OAuth authentication providers that work with ASP.NET Core apps.</span></span> <span data-ttu-id="93ca9-106">Pakiety NuGet innych firm, takie jak te obsługiwane przez [ASPNET-contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth), mogą służyć do uzupełnienia dostawców uwierzytelniania wdrożonych przez zespół ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="93ca9-106">Third-party NuGet packages, such as the ones maintained by [aspnet-contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth), can be used to complement the authentication providers implemented by the ASP.NET Core team.</span></span>

* <span data-ttu-id="93ca9-107">[LinkedIn](https://www.linkedin.com/developer/apps) ([instrukcje](https://developer.linkedin.com/docs/oauth2))</span><span class="sxs-lookup"><span data-stu-id="93ca9-107">[LinkedIn](https://www.linkedin.com/developer/apps) ([Instructions](https://developer.linkedin.com/docs/oauth2))</span></span>

* <span data-ttu-id="93ca9-108">[Usługi Instagram](https://www.instagram.com/developer/register/) ([instrukcje](https://www.instagram.com/developer/authentication/))</span><span class="sxs-lookup"><span data-stu-id="93ca9-108">[Instagram](https://www.instagram.com/developer/register/) ([Instructions](https://www.instagram.com/developer/authentication/))</span></span>

* <span data-ttu-id="93ca9-109">[Reddit](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) ([instrukcje](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example))</span><span class="sxs-lookup"><span data-stu-id="93ca9-109">[Reddit](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) ([Instructions](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example))</span></span>

* <span data-ttu-id="93ca9-110">[GitHub](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([instrukcje](https://developer.github.com/v3/oauth/))</span><span class="sxs-lookup"><span data-stu-id="93ca9-110">[Github](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([Instructions](https://developer.github.com/v3/oauth/))</span></span>

* <span data-ttu-id="93ca9-111">[Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([instrukcje](https://developer.yahoo.com/bbauth/user.html))</span><span class="sxs-lookup"><span data-stu-id="93ca9-111">[Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([Instructions](https://developer.yahoo.com/bbauth/user.html))</span></span>

* <span data-ttu-id="93ca9-112">[Tumblr](https://www.tumblr.com/oauth/apps) ([instrukcje](https://www.tumblr.com/docs/api/v2#auth))</span><span class="sxs-lookup"><span data-stu-id="93ca9-112">[Tumblr](https://www.tumblr.com/oauth/apps) ([Instructions](https://www.tumblr.com/docs/api/v2#auth))</span></span>

* <span data-ttu-id="93ca9-113">[Pinterest](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([instrukcje](https://developers.pinterest.com/docs/api/overview/?))</span><span class="sxs-lookup"><span data-stu-id="93ca9-113">[Pinterest](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([Instructions](https://developers.pinterest.com/docs/api/overview/?))</span></span>

* <span data-ttu-id="93ca9-114">[Pocket](https://getpocket.com/developer/apps/new) ([instrukcje](https://getpocket.com/developer/docs/authentication))</span><span class="sxs-lookup"><span data-stu-id="93ca9-114">[Pocket](https://getpocket.com/developer/apps/new) ([Instructions](https://getpocket.com/developer/docs/authentication))</span></span>

* <span data-ttu-id="93ca9-115">[Flickr](https://www.flickr.com/services/apps/create) ([instrukcje](https://www.flickr.com/services/api/auth.oauth.html))</span><span class="sxs-lookup"><span data-stu-id="93ca9-115">[Flickr](https://www.flickr.com/services/apps/create) ([Instructions](https://www.flickr.com/services/api/auth.oauth.html))</span></span>

* <span data-ttu-id="93ca9-116">[Dribble](https://dribbble.com/signup) ([instrukcje](https://developer.dribbble.com/v1/oauth/))</span><span class="sxs-lookup"><span data-stu-id="93ca9-116">[Dribble](https://dribbble.com/signup) ([Instructions](https://developer.dribbble.com/v1/oauth/))</span></span>

* <span data-ttu-id="93ca9-117">[Vimeo](https://vimeo.com/join) ([instrukcje](https://developer.vimeo.com/api/authentication))</span><span class="sxs-lookup"><span data-stu-id="93ca9-117">[Vimeo](https://vimeo.com/join) ([Instructions](https://developer.vimeo.com/api/authentication))</span></span>

* <span data-ttu-id="93ca9-118">[SoundCloud](https://soundcloud.com/you/apps/new) ([instrukcje](https://developers.soundcloud.com/blog/we-love-oauth-2))</span><span class="sxs-lookup"><span data-stu-id="93ca9-118">[SoundCloud](https://soundcloud.com/you/apps/new) ([Instructions](https://developers.soundcloud.com/blog/we-love-oauth-2))</span></span>

* <span data-ttu-id="93ca9-119">[VK](https://vk.com/apps?act=manage) ([instrukcje](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites))</span><span class="sxs-lookup"><span data-stu-id="93ca9-119">[VK](https://vk.com/apps?act=manage) ([Instructions](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites))</span></span>

[!INCLUDE[Multiple authentication providers](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]
