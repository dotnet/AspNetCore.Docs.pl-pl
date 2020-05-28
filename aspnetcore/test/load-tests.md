---
<span data-ttu-id="e6d7d-101">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e6d7d-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e6d7d-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e6d7d-102">'Blazor'</span></span>
- <span data-ttu-id="e6d7d-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e6d7d-103">'Identity'</span></span>
- <span data-ttu-id="e6d7d-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e6d7d-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="e6d7d-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e6d7d-105">'Razor'</span></span>
- <span data-ttu-id="e6d7d-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e6d7d-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="e6d7d-107">ASP.NET Core testowanie obciążenia/obciążeniowego</span><span class="sxs-lookup"><span data-stu-id="e6d7d-107">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="e6d7d-108">Testowanie obciążeniowe i testowanie obciążeniowe są ważne, aby zapewnić, że aplikacja sieci Web jest wydajna i skalowalna.</span><span class="sxs-lookup"><span data-stu-id="e6d7d-108">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="e6d7d-109">Ich cele są różne, chociaż często korzystają z podobnych testów.</span><span class="sxs-lookup"><span data-stu-id="e6d7d-109">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="e6d7d-110">**Testy obciążenia**: należy sprawdzić, czy aplikacja może obsłużyć określone obciążenie użytkownikami w pewnym scenariuszu, zachowując jednocześnie cel odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="e6d7d-110">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="e6d7d-111">Aplikacja jest uruchamiana w normalnych warunkach.</span><span class="sxs-lookup"><span data-stu-id="e6d7d-111">The app is run under normal conditions.</span></span>

<span data-ttu-id="e6d7d-112">**Testy obciążeniowe**: testowanie stabilności aplikacji podczas uruchamiania w skrajnych warunkach, często przez długi czas.</span><span class="sxs-lookup"><span data-stu-id="e6d7d-112">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="e6d7d-113">Testy zapewniają duże obciążenie użytkownikami, przerastają lub stopniowo zwiększają obciążenie, w aplikacji lub ograniczają zasoby obliczeniowe aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e6d7d-113">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="e6d7d-114">Testy obciążeniowe określają, czy aplikacja poddawana obciążeniom może odzyskać sprawność po awarii i bezpiecznie wrócić do oczekiwanego zachowania.</span><span class="sxs-lookup"><span data-stu-id="e6d7d-114">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="e6d7d-115">W obszarze naprężenie aplikacja nie jest uruchamiana w normalnych warunkach.</span><span class="sxs-lookup"><span data-stu-id="e6d7d-115">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="e6d7d-116">Program Visual Studio 2019 to Ostatnia wersja programu Visual Studio z funkcjami testów obciążenia.</span><span class="sxs-lookup"><span data-stu-id="e6d7d-116">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="e6d7d-117">W przypadku klientów wymagających narzędzi testowania obciążenia w przyszłości zalecamy używanie alternatywnych narzędzi, takich jak Apache JMeter, Akamai CloudTest i BlazeMeter.</span><span class="sxs-lookup"><span data-stu-id="e6d7d-117">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="e6d7d-118">Aby uzyskać więcej informacji, zobacz [Informacje o wersji programu Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="e6d7d-118">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="e6d7d-119">Narzędzia programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6d7d-119">Visual Studio tools</span></span>

<span data-ttu-id="e6d7d-120">Program Visual Studio umożliwia użytkownikom tworzenie, opracowywanie i debugowanie testów wydajności i obciążenia sieci Web.</span><span class="sxs-lookup"><span data-stu-id="e6d7d-120">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="e6d7d-121">Dostępna jest opcja tworzenia testów przez rejestrowanie akcji w przeglądarce internetowej.</span><span class="sxs-lookup"><span data-stu-id="e6d7d-121">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="e6d7d-122">Aby uzyskać informacje na temat sposobu tworzenia, konfigurowania i uruchamiania projektów testów obciążenia przy użyciu programu Visual Studio 2017, zobacz [Szybki Start: Tworzenie projektu testu obciążenia](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="e6d7d-122">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="e6d7d-123">Testy obciążenia można skonfigurować do uruchamiania lokalnego lub uruchamiania w chmurze przy użyciu usługi Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="e6d7d-123">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="e6d7d-124">Narzędzia innych firm</span><span class="sxs-lookup"><span data-stu-id="e6d7d-124">Third-party tools</span></span>

<span data-ttu-id="e6d7d-125">Poniższa lista zawiera narzędzia do oceny wydajności sieci Web innych firm z różnymi zestawami funkcji:</span><span class="sxs-lookup"><span data-stu-id="e6d7d-125">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="e6d7d-126">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="e6d7d-126">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="e6d7d-127">ApacheBench (AB)</span><span class="sxs-lookup"><span data-stu-id="e6d7d-127">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="e6d7d-128">Gatling</span><span class="sxs-lookup"><span data-stu-id="e6d7d-128">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="e6d7d-129">k6</span><span class="sxs-lookup"><span data-stu-id="e6d7d-129">k6</span></span>](https://k6.io)
* [<span data-ttu-id="e6d7d-130">Chleb</span><span class="sxs-lookup"><span data-stu-id="e6d7d-130">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="e6d7d-131">Moje przepięcio zachodnie wiatru</span><span class="sxs-lookup"><span data-stu-id="e6d7d-131">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="e6d7d-132">W ramach</span><span class="sxs-lookup"><span data-stu-id="e6d7d-132">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="e6d7d-133">Vegeta</span><span class="sxs-lookup"><span data-stu-id="e6d7d-133">Vegeta</span></span>](https://github.com/tsenart/vegeta)

