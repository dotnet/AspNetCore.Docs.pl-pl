---
title: Następne kroki — DevOps z ASP.NET Core i platformą Azure
author: CamSoper
description: Dodatkowe ścieżki szkoleniowe dla devops z ASP.NET Core i platformy Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/next-steps
ms.openlocfilehash: a775dc42551a43bcce72b5f9ca364ed00b1dc4e6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659475"
---
# <a name="next-steps"></a><span data-ttu-id="b7aae-103">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="b7aae-103">Next steps</span></span>

<span data-ttu-id="b7aae-104">W tym przewodniku utworzono potok DevOps dla przykładowej aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b7aae-104">In this guide, you created a DevOps pipeline for an ASP.NET Core sample app.</span></span> <span data-ttu-id="b7aae-105">Gratulacje!</span><span class="sxs-lookup"><span data-stu-id="b7aae-105">Congratulations!</span></span> <span data-ttu-id="b7aae-106">Mamy nadzieję, że podobało Ci się uczenie się publikowania ASP.NET podstawowych aplikacji sieci web w usłudze Azure App Service i automatyzacji ciągłej integracji zmian.</span><span class="sxs-lookup"><span data-stu-id="b7aae-106">We hope you enjoyed learning to publish ASP.NET Core web apps to Azure App Service and automate the continuous integration of changes.</span></span>

<span data-ttu-id="b7aae-107">Oprócz hostingu sieci Web i DevOps platforma Azure ma szeroką gamę usług Platformy jako usługi (PaaS) przydatnych do ASP.NET deweloperów Core.</span><span class="sxs-lookup"><span data-stu-id="b7aae-107">Beyond web hosting and DevOps, Azure has a wide array of Platform-as-a-Service (PaaS) services useful to ASP.NET Core developers.</span></span> <span data-ttu-id="b7aae-108">W tej sekcji przedstawiono krótki przegląd niektórych najczęściej używanych usług.</span><span class="sxs-lookup"><span data-stu-id="b7aae-108">This section gives a brief overview of some of the most commonly used services.</span></span>

## <a name="storage-and-databases"></a><span data-ttu-id="b7aae-109">Przechowywanie i bazy danych</span><span class="sxs-lookup"><span data-stu-id="b7aae-109">Storage and databases</span></span>

<span data-ttu-id="b7aae-110">[Pamięć podręczna Redis](/azure/redis-cache/) jest buforowanie danych o wysokiej przepływności, o małym opóźnieniu dostępne jako usługa.</span><span class="sxs-lookup"><span data-stu-id="b7aae-110">[Redis Cache](/azure/redis-cache/) is high-throughput, low-latency data caching available as a service.</span></span> <span data-ttu-id="b7aae-111">Może służyć do buforowania danych wyjściowych strony, zmniejszenie żądań bazy danych i zapewnienie ASP.NET podstawowy stan sesji w wielu wystąpieniach aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b7aae-111">It can be used for caching page output, reducing database requests, and providing ASP.NET Core session state across multiple instances of an app.</span></span>

<span data-ttu-id="b7aae-112">[Usługa Azure Storage](/azure/storage/) to masowo skalowalny magazyn w chmurze platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="b7aae-112">[Azure Storage](/azure/storage/) is Azure's massively scalable cloud storage.</span></span> <span data-ttu-id="b7aae-113">Deweloperzy mogą korzystać z [magazynu kolejek](/azure/storage/queues/storage-queues-introduction) dla niezawodnego kolejkowania wiadomości, a [magazyn tabel](/azure/storage/tables/table-storage-overview) to magazyn kluczy-wartość NoSQL zaprojektowany do szybkiego rozwoju przy użyciu ogromnych, częściowo ustrukturyzowanych zestawów danych.</span><span class="sxs-lookup"><span data-stu-id="b7aae-113">Developers can take advantage of [Queue Storage](/azure/storage/queues/storage-queues-introduction) for reliable message queuing, and [Table Storage](/azure/storage/tables/table-storage-overview) is a NoSQL key-value store designed for rapid development using massive, semi-structured data sets.</span></span>

<span data-ttu-id="b7aae-114">[Usługa Azure SQL Database](/azure/sql-database/) udostępnia znane funkcje relacyjnej bazy danych jako usługi przy użyciu aparatu programu Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="b7aae-114">[Azure SQL Database](/azure/sql-database/) provides familiar relational database functionality as a service using the Microsoft SQL Server Engine.</span></span>

<span data-ttu-id="b7aae-115">[Cosmos DB](/azure/cosmos-db/) globalnie rozproszona, wielomodelowa usługa bazy danych NoSQL.</span><span class="sxs-lookup"><span data-stu-id="b7aae-115">[Cosmos DB](/azure/cosmos-db/) globally distributed, multi-model NoSQL database service.</span></span> <span data-ttu-id="b7aae-116">Dostępnych jest wiele interfejsów API, w tym interfejs API SQL (dawniej documentDB), Cassandra i MongoDB.</span><span class="sxs-lookup"><span data-stu-id="b7aae-116">Multiple APIs are available, including SQL API (formerly called DocumentDB), Cassandra, and MongoDB.</span></span>

## <a name="identity"></a><span data-ttu-id="b7aae-117">Tożsamość</span><span class="sxs-lookup"><span data-stu-id="b7aae-117">Identity</span></span>

<span data-ttu-id="b7aae-118">[Usługa Azure Active Directory](/azure/active-directory/) i [usługa Azure Active Directory B2C](/azure/active-directory-b2c/) są usługami tożsamości.</span><span class="sxs-lookup"><span data-stu-id="b7aae-118">[Azure Active Directory](/azure/active-directory/) and [Azure Active Directory B2C](/azure/active-directory-b2c/) are both identity services.</span></span> <span data-ttu-id="b7aae-119">Usługa Azure Active Directory jest przeznaczona do scenariuszy korporacyjnych i umożliwia współpracę usługi Azure AD B2B (business-to-business), podczas gdy usługa Azure Active Directory B2C jest przeznaczona dla scenariuszy między klientami, w tym logowanie do sieci społecznościowej.</span><span class="sxs-lookup"><span data-stu-id="b7aae-119">Azure Active Directory is designed for enterprise scenarios and enables Azure AD B2B (business-to-business) collaboration, while Azure Active Directory B2C is intended business-to-customer scenarios, including social network sign-in.</span></span>

## <a name="mobile"></a><span data-ttu-id="b7aae-120">Urządzenia przenośne</span><span class="sxs-lookup"><span data-stu-id="b7aae-120">Mobile</span></span>

<span data-ttu-id="b7aae-121">[Notification Hubs](/azure/notification-hubs/) to wieloplatformowy, skalowalny silnik powiadomień wypychanych, który szybko wysyła miliony wiadomości do aplikacji działających na różnych typach urządzeń.</span><span class="sxs-lookup"><span data-stu-id="b7aae-121">[Notification Hubs](/azure/notification-hubs/) is a multi-platform, scalable push-notification engine to quickly send millions of messages to apps running on various types of devices.</span></span>

## <a name="web-infrastructure"></a><span data-ttu-id="b7aae-122">Infrastruktura internetowa</span><span class="sxs-lookup"><span data-stu-id="b7aae-122">Web infrastructure</span></span>

<span data-ttu-id="b7aae-123">[Usługa Azure Container Service](/azure/aks/) zarządza hostowanym środowiskiem Kubernetes, dzięki czemu można szybko i łatwo wdrażać konteneryzowane aplikacje i zarządzać nimi bez wiedzy specjalistycznej w zakresie aranżacji kontenerów.</span><span class="sxs-lookup"><span data-stu-id="b7aae-123">[Azure Container Service](/azure/aks/) manages your hosted Kubernetes environment, making it quick and easy to deploy and manage containerized apps without container orchestration expertise.</span></span>

<span data-ttu-id="b7aae-124">[Usługa Azure Search](/azure/search/) służy do tworzenia rozwiązania wyszukiwania w przedsiębiorstwie za pomocą prywatnej, heterogeniitarnej zawartości.</span><span class="sxs-lookup"><span data-stu-id="b7aae-124">[Azure Search](/azure/search/) is used to create an enterprise search solution over private, heterogenous content.</span></span>

<span data-ttu-id="b7aae-125">[Usługa Fabric](/azure/service-fabric/) to platforma systemów rozproszonych, która ułatwia pakowanie, wdrażanie i zarządzanie skalowalnymi i niezawodnymi mikrousługami i kontenerami.</span><span class="sxs-lookup"><span data-stu-id="b7aae-125">[Service Fabric](/azure/service-fabric/) is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers.</span></span>
