---
title: App Service Mobile Apps와 Notification Hubs 통합
description: Azure Notification Hubs가 Azure App Service Mobile Apps와 어떻게 작동하는지 알아봅니다.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: ''
ms.assetid: 83132dff-a01d-4b31-a426-b57496852b81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 647c5f40c7b02b8d9b488ce0812f27c0c0dde1a7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87022143"
---
# <a name="integration-with-app-service-mobile-apps"></a>App Service Mobile Apps와 통합

Azure 서비스 전반에서 원활하고 일관적인 사용 환경을 조성하기 위하여 Notification Hubs를 사용한 푸시 알림이 [App Service Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop)에 기본적으로 지원됩니다. [App Service Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop)는 엔터프라이즈 개발자 및 시스템 통합자를 위해 확장성이 크고 전 세계에서 사용 가능한 모바일 애플리케이션 개발 플랫폼을 제공합니다. 이 플랫폼은 모바일 개발자에게 풍부한 기능 집합을 제공합니다.

Mobile Apps 개발자는 다음 워크플로에서 Notification Hubs를 활용할 수 있습니다.

1. 디바이스 PNS 핸들을 검색합니다.
2. 편리한 Mobile Apps 클라이언트 SDK 등록 API를 통해 디바이스를 Notification Hubs에 등록합니다.

    > [!NOTE]
    > Mobile Apps는 등록 시 보안을 목적으로 모든 태그를 제거합니다. 백 엔드에서 Notification Hubs와 직접 작업하여 디바이스와 태그를 연결합니다.

3. Notification Hubs를 통해 앱 백 엔드에서 알림을 보냅니다.

이 통합으로 인해 개발자에게 다음과 같은 편리한 기능이 제공됩니다.

- **Mobile Apps 클라이언트 SDK**: 이러한 다중 플랫폼 SDK는 등록을 위한 간단한 API를 제공하며 모바일 앱과 자동으로 연결되는 알림 허브와 통신합니다. 개발자는 Notification Hubs 자격 증명을 심도있게 분석하고 추가 서비스로 작업할 필요가 없습니다.
  - *사용자에게 푸시*: SDK는 Mobile Apps 인증 사용자 ID로 지정된 디바이스에 태그를 자동으로 지정하여 사용자 시나리오에 푸시할 수 있습니다.
  - *디바이스에 푸시*: SDK는 자동으로 Mobile Apps 설치 ID를 GUID로 사용하여 Notification Hubs에 등록하여 개발자가 여러 서비스 GUID를 유지 관리하는 문제를 해결합니다.
- **설치 모델**: Mobile Apps는 Notification Hubs의 최신 푸시 모델과 함께 작동하여 푸시 알림 서비스와 일치하고 사용하기 쉬운 JSON 설치의 디바이스와 연결된 모든 푸시 속성을 나타냅니다.
- **유연성**: 개발자는 통합되어 있더라도 Notification Hubs와 직접 작동하도록 언제든지 선택할 수 있습니다.
- **[Azure Portal](https://portal.azure.com)에 통합된 환경**: 푸시 기능은 Mobile Apps에서 시각적으로 표현되므로 개발자가 Mobile Apps를 통해 연결된 알림 허브를 사용하여 쉽게 작업할 수 있습니다.
