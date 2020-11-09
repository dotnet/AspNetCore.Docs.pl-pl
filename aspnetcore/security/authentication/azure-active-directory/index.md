---
title: Platforma tożsamości firmy Microsoft i Azure Active Directory z ASP.NET Core
author: rick-anderson
description: Odkryj tematy związane z uwierzytelnianiem za pomocą usługi Microsoft Identity platform Azure Active Directory dla aplikacji sieci Web i interfejsów API w programie ASP.NET Core.
ms.author: riande
ms.date: 01/21/2020
ms.custom: mvc, seodec18
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/azure-active-directory/index
ms.openlocfilehash: c8a3d6838b9b93ff58dfaff8423bd6946b5ca743
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061408"
---
# <a name="azure-active-directory-with-aspnet-core"></a><span data-ttu-id="ff9ff-103">Azure Active Directory z ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ff9ff-103">Azure Active Directory with ASP.NET Core</span></span>

<span data-ttu-id="ff9ff-104">Te samouczki i przykłady przedstawiają uwierzytelnianie w ASP.NET Core przy użyciu platformy tożsamości firmy Microsoft i Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="ff9ff-104">These tutorials and samples demonstrate authentication in ASP.NET Core using Microsoft identity platform and Azure Active Directory.</span></span> <span data-ttu-id="ff9ff-105">Aby uzyskać dodatkowe samouczki i przykłady dotyczące korzystania z ASP.NET Core z usługą Azure AD, zobacz [Microsoft Identity platform](/azure/active-directory/develop/).</span><span class="sxs-lookup"><span data-stu-id="ff9ff-105">For additional tutorials and samples using ASP.NET Core with Azure AD, see [Microsoft identity platform](/azure/active-directory/develop/).</span></span>

## <a name="application-scenarios"></a><span data-ttu-id="ff9ff-106">Scenariusze aplikacji</span><span class="sxs-lookup"><span data-stu-id="ff9ff-106">Application Scenarios</span></span>

* [<span data-ttu-id="ff9ff-107">Szybki Start: Dodawanie logowania z firmą Microsoft do aplikacji internetowej ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ff9ff-107">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="ff9ff-108">Aplikacja internetowa z możliwością logowania użytkowników</span><span class="sxs-lookup"><span data-stu-id="ff9ff-108">Web app that signs in users</span></span>](/azure/active-directory/develop/scenario-web-app-sign-user-overview?tabs=aspnetcore)
* [<span data-ttu-id="ff9ff-109">Aplikacja internetowa wywołująca internetowe interfejsy API</span><span class="sxs-lookup"><span data-stu-id="ff9ff-109">Web app that calls web APIs</span></span>](/azure/active-directory/develop/scenario-web-app-call-api-overview)
* [<span data-ttu-id="ff9ff-110">Chroniony internetowy interfejs API</span><span class="sxs-lookup"><span data-stu-id="ff9ff-110">Protected web API</span></span>](/azure/active-directory/develop/scenario-protected-web-api-overview)
* [<span data-ttu-id="ff9ff-111">Internetowy interfejs API, który wywołuje inne internetowe interfejsy API</span><span class="sxs-lookup"><span data-stu-id="ff9ff-111">Web API that calls other web APIs</span></span>](/azure/active-directory/develop/scenario-web-api-call-api-overview)
* [<span data-ttu-id="ff9ff-112">Aplikacja internetowa, która loguje użytkowników z Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="ff9ff-112">Web app that signs in users with Azure AD B2C</span></span>](xref:security/authentication/azure-ad-b2c)

## <a name="samples"></a><span data-ttu-id="ff9ff-113">Samples</span><span class="sxs-lookup"><span data-stu-id="ff9ff-113">Samples</span></span>

* <span data-ttu-id="ff9ff-114">[Zezwól aplikacji ASP.NET Core na logowanie użytkowników i wywoływanie interfejsów API sieci Web przy użyciu usługi Azure AD v2](/samples/azure-samples/active-directory-aspnetcore-webapp-openidconnect-v2/enable-webapp-signin/):</span><span class="sxs-lookup"><span data-stu-id="ff9ff-114">[Enable your ASP.NET Core app to sign-in users and call web APIs using Azure AD V2](/samples/azure-samples/active-directory-aspnetcore-webapp-openidconnect-v2/enable-webapp-signin/):</span></span> 
  * <span data-ttu-id="ff9ff-115">Obejrzyj [to powiązane wideo](https://channel9.msdn.com/Events/Build/2018/THR5001)</span><span class="sxs-lookup"><span data-stu-id="ff9ff-115">See [this associated video](https://channel9.msdn.com/Events/Build/2018/THR5001)</span></span>
* <span data-ttu-id="ff9ff-116">[Wywoływanie internetowego interfejsu API ASP.NET Core 2,0 z aplikacji WPF przy użyciu usługi Azure AD v2](/samples/azure-samples/active-directory-dotnet-native-aspnetcore-v2/calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2/):</span><span class="sxs-lookup"><span data-stu-id="ff9ff-116">[Calling an ASP.NET Core 2.0 Web API from a WPF application using Azure AD V2](/samples/azure-samples/active-directory-dotnet-native-aspnetcore-v2/calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2/):</span></span> 
  * <span data-ttu-id="ff9ff-117">Obejrzyj [to powiązane wideo](https://channel9.msdn.com/Events/Build/2018/THR5000)</span><span class="sxs-lookup"><span data-stu-id="ff9ff-117">See [this associated video](https://channel9.msdn.com/Events/Build/2018/THR5000)</span></span>
* [<span data-ttu-id="ff9ff-118">Aplikacja sieci Web ASP.NET Core z Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="ff9ff-118">An ASP.NET Core web app with Azure AD B2C</span></span>](/samples/azure-samples/active-directory-b2c-dotnetcore-webapp/an-aspnet-core-web-app-with-azure-ad-b2c/)
