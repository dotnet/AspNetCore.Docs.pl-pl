---
title: Wprowadzenie do platformy ASP.NET Core
author: rick-anderson
description: Krótki samouczek, który tworzy i uruchamia podstawową aplikację Hello world przy użyciu ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: getting-started
ms.openlocfilehash: b88460cdff5d8c30c6a28afdb4f67e8e0b6b819c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403368"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="20832-103">Samouczek: wprowadzenie do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="20832-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="20832-104">W tym samouczku pokazano, jak utworzyć i uruchomić aplikację sieci Web ASP.NET Core przy użyciu interfejs wiersza polecenia platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="20832-104">This tutorial shows how to create and run an ASP.NET Core web app using the .NET Core CLI.</span></span>

<span data-ttu-id="20832-105">Omawiane tematy:</span><span class="sxs-lookup"><span data-stu-id="20832-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="20832-106">Utwórz projekt aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="20832-106">Create a web app project.</span></span>
> * <span data-ttu-id="20832-107">Zaufaj certyfikatowi Deweloperskiemu.</span><span class="sxs-lookup"><span data-stu-id="20832-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="20832-108">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="20832-108">Run the app.</span></span>
> * <span data-ttu-id="20832-109">Edytuj Razor stronę.</span><span class="sxs-lookup"><span data-stu-id="20832-109">Edit a Razor page.</span></span>

<span data-ttu-id="20832-110">Na końcu będziesz mieć działającą aplikację sieci Web na komputerze lokalnym.</span><span class="sxs-lookup"><span data-stu-id="20832-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Strona główna aplikacji internetowej](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="20832-112">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="20832-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a><span data-ttu-id="20832-113">Tworzenie projektu aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="20832-113">Create a web app project</span></span>

<span data-ttu-id="20832-114">Otwórz powłokę poleceń i wprowadź następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="20832-114">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

<span data-ttu-id="20832-115">Poprzednie polecenie:</span><span class="sxs-lookup"><span data-stu-id="20832-115">The preceding command:</span></span>

* <span data-ttu-id="20832-116">Tworzy nową aplikację sieci Web.</span><span class="sxs-lookup"><span data-stu-id="20832-116">Creates a new web app.</span></span>  
* <span data-ttu-id="20832-117">`-o aspnetcoreapp`Parametr tworzy katalog o nazwie *aspnetcoreapp* z plikami źródłowymi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="20832-117">The `-o aspnetcoreapp` parameter creates a directory named *aspnetcoreapp* with the source files for the app.</span></span>

### <a name="trust-the-development-certificate"></a><span data-ttu-id="20832-118">Ufanie certyfikatowi Deweloperskiemu</span><span class="sxs-lookup"><span data-stu-id="20832-118">Trust the development certificate</span></span>

<span data-ttu-id="20832-119">Ufaj certyfikatowi deweloperskim HTTPS:</span><span class="sxs-lookup"><span data-stu-id="20832-119">Trust the HTTPS development certificate:</span></span>

# <a name="windows"></a>[<span data-ttu-id="20832-120">Windows</span><span class="sxs-lookup"><span data-stu-id="20832-120">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="20832-121">Poprzednie polecenie wyświetla następujące okno dialogowe:</span><span class="sxs-lookup"><span data-stu-id="20832-121">The preceding command displays the following dialog:</span></span>

![Okno dialogowe ostrzeżenia o zabezpieczeniach](~/getting-started/_static/cert.png)

<span data-ttu-id="20832-123">Wybierz opcję **tak** , jeśli zgadzasz się ufać certyfikatowi Deweloperskiemu.</span><span class="sxs-lookup"><span data-stu-id="20832-123">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macos"></a>[<span data-ttu-id="20832-124">macOS</span><span class="sxs-lookup"><span data-stu-id="20832-124">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="20832-125">Poprzednie polecenie wyświetla następujący komunikat:</span><span class="sxs-lookup"><span data-stu-id="20832-125">The preceding command displays the following message:</span></span>

<span data-ttu-id="20832-126">*Zażądano zaufania certyfikatu deweloperskiego https. Jeśli certyfikat nie jest już zaufany, zostanie uruchomione następujące polecenie:*`'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="20832-126">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted, we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="20832-127">To polecenie może wyświetlać monit o podanie hasła w celu zainstalowania certyfikatu w łańcuchu kluczy systemu.</span><span class="sxs-lookup"><span data-stu-id="20832-127">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="20832-128">Wprowadź hasło, jeśli zgadzasz się ufać certyfikatowi Deweloperskiemu.</span><span class="sxs-lookup"><span data-stu-id="20832-128">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linux"></a>[<span data-ttu-id="20832-129">Linux</span><span class="sxs-lookup"><span data-stu-id="20832-129">Linux</span></span>](#tab/linux)

<span data-ttu-id="20832-130">Zapoznaj się z dokumentacją dystrybucji systemu Linux dotyczącą sposobu zaufania certyfikatu deweloperskiego HTTPS.</span><span class="sxs-lookup"><span data-stu-id="20832-130">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="20832-131">Aby uzyskać więcej informacji, zobacz artykuł [ufanie certyfikatowi Deweloperskiemu protokołu HTTPS ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span><span class="sxs-lookup"><span data-stu-id="20832-131">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="20832-132">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="20832-132">Run the app</span></span>

<span data-ttu-id="20832-133">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="20832-133">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

<span data-ttu-id="20832-134">Po powłoce poleceń wskazuje, że aplikacja została uruchomiona, przejdź do `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="20832-134">After the command shell indicates that the app has started, browse to `https://localhost:5001`.</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="20832-135">Edytowanie Razor strony</span><span class="sxs-lookup"><span data-stu-id="20832-135">Edit a Razor page</span></span>

<span data-ttu-id="20832-136">Otwórz stronę *Pages/index. cshtml* i zmodyfikuj ją i Zapisz przy użyciu następującego wyróżnionego znacznika:</span><span class="sxs-lookup"><span data-stu-id="20832-136">Open *Pages/Index.cshtml* and modify and save the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="20832-137">Przejdź do `https://localhost:5001` strony, Odśwież stronę i sprawdź, czy są wyświetlane zmiany.</span><span class="sxs-lookup"><span data-stu-id="20832-137">Browse to `https://localhost:5001`, refresh the page, and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="20832-138">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="20832-138">Next steps</span></span>

<span data-ttu-id="20832-139">W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="20832-139">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="20832-140">Utwórz projekt aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="20832-140">Create a web app project.</span></span>
> * <span data-ttu-id="20832-141">Zaufaj certyfikatowi Deweloperskiemu.</span><span class="sxs-lookup"><span data-stu-id="20832-141">Trust the development certificate.</span></span>
> * <span data-ttu-id="20832-142">Uruchom projekt.</span><span class="sxs-lookup"><span data-stu-id="20832-142">Run the project.</span></span>
> * <span data-ttu-id="20832-143">Wprowadź zmianę.</span><span class="sxs-lookup"><span data-stu-id="20832-143">Make a change.</span></span>

<span data-ttu-id="20832-144">Aby dowiedzieć się więcej na temat ASP.NET Core, zapoznaj się ze zalecaną ścieżką szkoleniową we wprowadzeniu:</span><span class="sxs-lookup"><span data-stu-id="20832-144">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
