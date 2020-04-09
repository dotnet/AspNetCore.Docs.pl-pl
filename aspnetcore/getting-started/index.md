---
title: Wprowadzenie do platformy ASP.NET Core
author: rick-anderson
description: Krótki samouczek, który tworzy i uruchamia podstawową aplikację Hello World przy użyciu ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
uid: getting-started
ms.openlocfilehash: 86a0c8d017138a949fddc0356f3de548d368a4c0
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417611"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="3fa1d-103">Samouczek: Wprowadzenie do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3fa1d-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="3fa1d-104">W tym samouczku pokazano, jak utworzyć i uruchomić aplikację sieci web ASP.NET Core przy użyciu interfejsu wiersza polecenia .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3fa1d-104">This tutorial shows how to create and run an ASP.NET Core web app using the .NET Core CLI.</span></span>

<span data-ttu-id="3fa1d-105">Omawiane tematy:</span><span class="sxs-lookup"><span data-stu-id="3fa1d-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3fa1d-106">Tworzenie projektu aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3fa1d-106">Create a web app project.</span></span>
> * <span data-ttu-id="3fa1d-107">Zaufaj certyfikatowi dewelopera.</span><span class="sxs-lookup"><span data-stu-id="3fa1d-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="3fa1d-108">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="3fa1d-108">Run the app.</span></span>
> * <span data-ttu-id="3fa1d-109">Edytuj stronę Razor.</span><span class="sxs-lookup"><span data-stu-id="3fa1d-109">Edit a Razor page.</span></span>

<span data-ttu-id="3fa1d-110">Na końcu będziesz mieć działającą aplikację sieci web uruchomiającą na komputerze lokalnym.</span><span class="sxs-lookup"><span data-stu-id="3fa1d-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Strona główna aplikacji internetowej](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="3fa1d-112">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="3fa1d-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a><span data-ttu-id="3fa1d-113">Tworzenie projektu aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="3fa1d-113">Create a web app project</span></span>

<span data-ttu-id="3fa1d-114">Otwórz powłokę polecenia i wprowadź następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="3fa1d-114">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

<span data-ttu-id="3fa1d-115">Poprzednie polecenie:</span><span class="sxs-lookup"><span data-stu-id="3fa1d-115">The preceding command:</span></span>

* <span data-ttu-id="3fa1d-116">Tworzy nową aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="3fa1d-116">Creates a new web app.</span></span>  
* <span data-ttu-id="3fa1d-117">Parametr `-o aspnetcoreapp` tworzy katalog o nazwie *aspnetcoreapp* z plikami źródłowymi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3fa1d-117">The `-o aspnetcoreapp` parameter creates a directory named *aspnetcoreapp* with the source files for the app.</span></span>

### <a name="trust-the-development-certificate"></a><span data-ttu-id="3fa1d-118">Ufaj certyfikatowi dewelopera</span><span class="sxs-lookup"><span data-stu-id="3fa1d-118">Trust the development certificate</span></span>

<span data-ttu-id="3fa1d-119">Ufaj certyfikatowi programistycznego HTTPS:</span><span class="sxs-lookup"><span data-stu-id="3fa1d-119">Trust the HTTPS development certificate:</span></span>

# <a name="windows"></a>[<span data-ttu-id="3fa1d-120">Windows</span><span class="sxs-lookup"><span data-stu-id="3fa1d-120">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="3fa1d-121">Poprzednie polecenie wyświetla następujące okno dialogowe:</span><span class="sxs-lookup"><span data-stu-id="3fa1d-121">The preceding command displays the following dialog:</span></span>

![Okno dialogowe z ostrzeżeniem o zabezpieczeniach](~/getting-started/_static/cert.png)

<span data-ttu-id="3fa1d-123">Wybierz **opcję Tak,** jeśli zgadzasz się ufać certyfikatowi dewelopera.</span><span class="sxs-lookup"><span data-stu-id="3fa1d-123">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macos"></a>[<span data-ttu-id="3fa1d-124">macOS</span><span class="sxs-lookup"><span data-stu-id="3fa1d-124">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="3fa1d-125">Poprzednie polecenie wyświetla następujący komunikat:</span><span class="sxs-lookup"><span data-stu-id="3fa1d-125">The preceding command displays the following message:</span></span>

<span data-ttu-id="3fa1d-126">*Zażądano ufania certyfikatowi dewelopera HTTPS. Jeśli certyfikat nie jest jeszcze zaufany, uruchomimy następujące polecenie:*`'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="3fa1d-126">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted, we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="3fa1d-127">To polecenie może monitować o zainstalowanie certyfikatu na pęku kluczy systemowych.</span><span class="sxs-lookup"><span data-stu-id="3fa1d-127">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="3fa1d-128">Wprowadź hasło, jeśli zgadzasz się zaufać certyfikatowi dewelopera.</span><span class="sxs-lookup"><span data-stu-id="3fa1d-128">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linux"></a>[<span data-ttu-id="3fa1d-129">Linux</span><span class="sxs-lookup"><span data-stu-id="3fa1d-129">Linux</span></span>](#tab/linux)

<span data-ttu-id="3fa1d-130">Zobacz dokumentację dystrybucji systemu Linux, jak ufać certyfikatowi programistycznemu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3fa1d-130">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="3fa1d-131">Aby uzyskać więcej informacji, zobacz [Ufanie ASP.NET podstawowym certyfikatem https](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span><span class="sxs-lookup"><span data-stu-id="3fa1d-131">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="3fa1d-132">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="3fa1d-132">Run the app</span></span>

<span data-ttu-id="3fa1d-133">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="3fa1d-133">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

<span data-ttu-id="3fa1d-134">Po tym, jak powłoka poleceń wskaże, że aplikacja została uruchomiona, przejdź do . `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="3fa1d-134">After the command shell indicates that the app has started, browse to `https://localhost:5001`.</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="3fa1d-135">Edytowanie strony Razor</span><span class="sxs-lookup"><span data-stu-id="3fa1d-135">Edit a Razor page</span></span>

<span data-ttu-id="3fa1d-136">Otwórz *strony/Index.cshtml* i zmodyfikuj i zapisz stronę z następującymi wyróżnionymi znacznikami:</span><span class="sxs-lookup"><span data-stu-id="3fa1d-136">Open *Pages/Index.cshtml* and modify and save the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="3fa1d-137">Przejdź do `https://localhost:5001`, odśwież stronę i sprawdź, czy zmiany są wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="3fa1d-137">Browse to `https://localhost:5001`, refresh the page, and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3fa1d-138">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="3fa1d-138">Next steps</span></span>

<span data-ttu-id="3fa1d-139">W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="3fa1d-139">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3fa1d-140">Tworzenie projektu aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3fa1d-140">Create a web app project.</span></span>
> * <span data-ttu-id="3fa1d-141">Zaufaj certyfikatowi dewelopera.</span><span class="sxs-lookup"><span data-stu-id="3fa1d-141">Trust the development certificate.</span></span>
> * <span data-ttu-id="3fa1d-142">Uruchom projekt.</span><span class="sxs-lookup"><span data-stu-id="3fa1d-142">Run the project.</span></span>
> * <span data-ttu-id="3fa1d-143">Dokonaj zmiany.</span><span class="sxs-lookup"><span data-stu-id="3fa1d-143">Make a change.</span></span>

<span data-ttu-id="3fa1d-144">Aby dowiedzieć się więcej o ASP.NET Core, zobacz zalecaną ścieżkę uczenia się we wstępie:</span><span class="sxs-lookup"><span data-stu-id="3fa1d-144">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
