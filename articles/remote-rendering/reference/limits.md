---
title: 제한 사항
description: SDK 기능에 대한 코드 제한 사항
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 17f98f452764abdc8458cdc38661d464ecb0a60d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84808539"
---
# <a name="limitations"></a>제한 사항

여러 기능에는 크기, 수 또는 기타 제한 사항이 있습니다.

## <a name="azure-frontend"></a>Azure Frontend

* 프로세스당 총 AzureFrontend 인스턴스: 16.
* AzureFrontend당 총 AzureSession 인스턴스: 16.

## <a name="objects"></a>개체

* 단일 형식(엔터티, CutPlaneComponent 등)에 대해 허용되는 총 개체: 16,777,215.
* 허용되는 총 활성 평면 잘라내기: 8.

## <a name="geometry"></a>geometry

* 자산에서 허용되는 총 재질: 65,535.
* 단일 질감의 최대 차원: 16384 x 16384. 변환 프로세스를 통해 큰 소스 질감이 축소 됩니다.

## <a name="overall-number-of-polygons"></a>전체 다각형 수

모든 로드된 모델의 허용 가능한 다각형 수는 [세션 관리 REST API](../how-tos/session-rest-api.md#create-a-session)에 전달된 VM의 크기에 따라 다릅니다.

| VM 크기 | 최대 다각형 수 |
|:--------|:------------------|
|표준| 2천만 |
|프리미엄| 제한 없음 |

이 제한 사항에 대 한 자세한 내용은 [VM 크기](../reference/vm-sizes.md) 챕터를 참조 하세요.

## <a name="platform-limitations"></a>플랫폼 제한 사항

**Windows 10 Desktop**

* UWP/x86은 유일하게 지원되는 UWP 플랫폼입니다. UWP/x64는 지원되지 않습니다.
* Win32/x64는 유일 하 게 지원 되는 Win32 플랫폼입니다. Win32/x86은 지원 되지 않습니다.

**Hololens 2**

* [PV 카메라에서 렌더링](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) 기능은 지원되지 않습니다.
