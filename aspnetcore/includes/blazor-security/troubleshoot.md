## <a name="troubleshoot"></a><span data-ttu-id="d9c43-101">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="d9c43-101">Troubleshoot</span></span>

### <a name="cookies-and-site-data"></a><span data-ttu-id="d9c43-102">Pliki cookie i dane witryny</span><span class="sxs-lookup"><span data-stu-id="d9c43-102">Cookies and site data</span></span>

<span data-ttu-id="d9c43-103">Pliki cookie i dane witryny mogą utrzymywać się w aktualizacjach aplikacji i zakłócać testowanie i rozwiązywanie problemów.</span><span class="sxs-lookup"><span data-stu-id="d9c43-103">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="d9c43-104">Podczas wprowadzania zmian w kodzie aplikacji, zmian w konfiguracji konta użytkownika w u dostawcy lub dostawcy wyczyść następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="d9c43-104">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="d9c43-105">Pliki cookie logowania użytkownika</span><span class="sxs-lookup"><span data-stu-id="d9c43-105">User sign-in cookies</span></span>
* <span data-ttu-id="d9c43-106">Pliki cookie aplikacji</span><span class="sxs-lookup"><span data-stu-id="d9c43-106">App cookies</span></span>
* <span data-ttu-id="d9c43-107">Buforowane i przechowywane dane lokacji</span><span class="sxs-lookup"><span data-stu-id="d9c43-107">Cached and stored site data</span></span>

<span data-ttu-id="d9c43-108">Jednym z podejść zapobiegających zakłócaniu testowania i rozwiązywania problemów utrzymujących się plików cookie i danych witryny jest:</span><span class="sxs-lookup"><span data-stu-id="d9c43-108">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="d9c43-109">Użyj przeglądarki do testowania, które można skonfigurować, aby usunąć wszystkie pliki cookie i dane witryny za każdym razem, gdy przeglądarka jest zamknięta.</span><span class="sxs-lookup"><span data-stu-id="d9c43-109">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
* <span data-ttu-id="d9c43-110">Zamknij przeglądarkę między wszelkimi zmianami w aplikacji, testuj użytkownika lub konfigurację dostawcy.</span><span class="sxs-lookup"><span data-stu-id="d9c43-110">Close the browser between any change to the app, test user, or provider configuration.</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="d9c43-111">Uruchamianie aplikacji Serwer</span><span class="sxs-lookup"><span data-stu-id="d9c43-111">Run the Server app</span></span>

<span data-ttu-id="d9c43-112">Podczas testowania i rozwiązywania problemów z hostowanymi aplikacjami Blazor upewnij się, że korzystasz z aplikacji z projektu **serwera.**</span><span class="sxs-lookup"><span data-stu-id="d9c43-112">When testing and troubleshooting a hosted Blazor app, make sure that you're running the app from the **Server** project.</span></span> <span data-ttu-id="d9c43-113">Na przykład w programie Visual Studio upewnij się, że projekt serwera jest wyróżniony w **Eksploratorze rozwiązań** przed uruchomieniem aplikacji przy pomocy dowolnej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="d9c43-113">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="d9c43-114">Wybierz przycisk **Uruchom**.</span><span class="sxs-lookup"><span data-stu-id="d9c43-114">Select the **Run** button.</span></span>
* <span data-ttu-id="d9c43-115">Użyj **debugowania** > **start debugowania** z menu.</span><span class="sxs-lookup"><span data-stu-id="d9c43-115">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="d9c43-116">Naciśnij klawisz <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="d9c43-116">Press <kbd>F5</kbd>.</span></span>
