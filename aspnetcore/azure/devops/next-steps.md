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
# <a name="next-steps"></a>Następne kroki

W tym przewodniku utworzono potok DevOps dla przykładowej aplikacji ASP.NET Core. Gratulacje! Mamy nadzieję, że podobało Ci się uczenie się publikowania ASP.NET podstawowych aplikacji sieci web w usłudze Azure App Service i automatyzacji ciągłej integracji zmian.

Oprócz hostingu sieci Web i DevOps platforma Azure ma szeroką gamę usług Platformy jako usługi (PaaS) przydatnych do ASP.NET deweloperów Core. W tej sekcji przedstawiono krótki przegląd niektórych najczęściej używanych usług.

## <a name="storage-and-databases"></a>Przechowywanie i bazy danych

[Pamięć podręczna Redis](/azure/redis-cache/) jest buforowanie danych o wysokiej przepływności, o małym opóźnieniu dostępne jako usługa. Może służyć do buforowania danych wyjściowych strony, zmniejszenie żądań bazy danych i zapewnienie ASP.NET podstawowy stan sesji w wielu wystąpieniach aplikacji.

[Usługa Azure Storage](/azure/storage/) to masowo skalowalny magazyn w chmurze platformy Azure. Deweloperzy mogą korzystać z [magazynu kolejek](/azure/storage/queues/storage-queues-introduction) dla niezawodnego kolejkowania wiadomości, a [magazyn tabel](/azure/storage/tables/table-storage-overview) to magazyn kluczy-wartość NoSQL zaprojektowany do szybkiego rozwoju przy użyciu ogromnych, częściowo ustrukturyzowanych zestawów danych.

[Usługa Azure SQL Database](/azure/sql-database/) udostępnia znane funkcje relacyjnej bazy danych jako usługi przy użyciu aparatu programu Microsoft SQL Server.

[Cosmos DB](/azure/cosmos-db/) globalnie rozproszona, wielomodelowa usługa bazy danych NoSQL. Dostępnych jest wiele interfejsów API, w tym interfejs API SQL (dawniej documentDB), Cassandra i MongoDB.

## <a name="identity"></a>Tożsamość

[Usługa Azure Active Directory](/azure/active-directory/) i [usługa Azure Active Directory B2C](/azure/active-directory-b2c/) są usługami tożsamości. Usługa Azure Active Directory jest przeznaczona do scenariuszy korporacyjnych i umożliwia współpracę usługi Azure AD B2B (business-to-business), podczas gdy usługa Azure Active Directory B2C jest przeznaczona dla scenariuszy między klientami, w tym logowanie do sieci społecznościowej.

## <a name="mobile"></a>Urządzenia przenośne

[Notification Hubs](/azure/notification-hubs/) to wieloplatformowy, skalowalny silnik powiadomień wypychanych, który szybko wysyła miliony wiadomości do aplikacji działających na różnych typach urządzeń.

## <a name="web-infrastructure"></a>Infrastruktura internetowa

[Usługa Azure Container Service](/azure/aks/) zarządza hostowanym środowiskiem Kubernetes, dzięki czemu można szybko i łatwo wdrażać konteneryzowane aplikacje i zarządzać nimi bez wiedzy specjalistycznej w zakresie aranżacji kontenerów.

[Usługa Azure Search](/azure/search/) służy do tworzenia rozwiązania wyszukiwania w przedsiębiorstwie za pomocą prywatnej, heterogeniitarnej zawartości.

[Usługa Fabric](/azure/service-fabric/) to platforma systemów rozproszonych, która ułatwia pakowanie, wdrażanie i zarządzanie skalowalnymi i niezawodnymi mikrousługami i kontenerami.
