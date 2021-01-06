---
title: Następne kroki — DevOps z ASP.NET Core i platformą Azure
author: CamSoper
description: Dodatkowe ścieżki szkoleniowe dla DevOps z ASP.NET Core i platformą Azure.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/next-steps
ms.openlocfilehash: 35b0486611cc15f25b1c8b54584c264e4c1298c9
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93056598"
---
# <a name="next-steps"></a>Następne kroki

W tym przewodniku utworzono potok DevOps dla przykładowej aplikacji ASP.NET Core. Gratulacje! Mamy nadzieję, że zachęcasz do publikowania ASP.NET Core aplikacji sieci Web w celu Azure App Service i zautomatyzowania ciągłej integracji zmian.

Poza hostingiem sieci Web i DevOps platforma Azure oferuje szeroką gamę usług platformy jako usługi (PaaS), które są przydatne do ASP.NET Core deweloperów. Ta sekcja zawiera krótkie omówienie niektórych z najczęściej używanych usług.

## <a name="storage-and-databases"></a>Magazyn i bazy danych

[Redis Cache](/azure/redis-cache/) to buforowanie danych o wysokiej przepływności i małych opóźnieniach dostępne jako usługa. Może służyć do buforowania danych wyjściowych stron, zmniejszania żądań bazy danych i zapewniania ASP.NET Core stanu sesji w wielu wystąpieniach aplikacji.

[Usługa Azure Storage](/azure/storage/) to wysoce skalowalny magazyn w chmurze na platformie Azure. Deweloperzy mogą korzystać z [queue storage](/azure/storage/queues/storage-queues-introduction) dla niezawodnej usługi kolejkowania komunikatów, a [Table Storage](/azure/storage/tables/table-storage-overview) to NoSQL klucz-wartość magazynu, który umożliwia szybkie tworzenie aplikacji przy użyciu ogromnych zestawów danych z częściową strukturą.

[Azure SQL Database](/azure/sql-database/) zapewnia znane funkcje relacyjnej bazy danych jako usługi korzystające z aparatu Microsoft SQL Server.

[Cosmos DB](/azure/cosmos-db/) globalnie dystrybuowana, wielomodelowa usługa bazy danych NoSQL. Dostępnych jest wiele interfejsów API, w tym interfejs API SQL (dawniej nazywany DocumentDB), Cassandra i MongoDB.

## Identity

[Azure Active Directory](/azure/active-directory/) i [Azure Active Directory B2C](/azure/active-directory-b2c/) są usługami tożsamości. Azure Active Directory jest zaprojektowana dla scenariuszy korporacyjnych i umożliwia współpracę z usługą Azure AD B2B (Business-to-Business), natomiast Azure Active Directory B2C jest zamierzonymi scenariuszami biznesowymi, w tym logowaniem do sieci społecznościowej.

## <a name="mobile"></a>Komórkowy

[Notification Hubs](/azure/notification-hubs/) to wieloplatformowy, Skalowalny aparat powiadomień wypychanych umożliwiający szybkie wysyłanie milionów komunikatów do aplikacji działających na różnych typach urządzeń.

## <a name="web-infrastructure"></a>Infrastruktura sieci Web

[Azure Container Service](/azure/aks/) zarządza hostowanym środowiskiem Kubernetes, dzięki czemu można szybko i łatwo wdrażać aplikacje z kontenerami bez wiedzy z zakresu aranżacji kontenerów i zarządzać nimi.

[Azure Search](/azure/search/) służy do tworzenia rozwiązania wyszukiwania dla przedsiębiorstw za pośrednictwem prywatnej, heterogenicznych zawartości.

[Service Fabric](/azure/service-fabric/) to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług i kontenerów oraz zarządzanie nimi.
