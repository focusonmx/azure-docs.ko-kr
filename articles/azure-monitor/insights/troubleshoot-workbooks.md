---
title: 통합 문서 기반 정보 Azure Monitor 문제 해결
description: Azure Key Vault, Azure CosmosDB, Azure Storage, Azure Cache for Redis 등의 서비스에 대 한 통합 문서 기반 정보를 Azure Monitor에 대 한 문제 해결 지침을 제공 합니다.
services: azure-monitor
ms.author: mbullwin
author: mrbullwinkle
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 90743a213092aacfa10139c12f47bc11faf0d86d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86498715"
---
# <a name="troubleshooting-workbook-based-insights"></a>통합 문서 기반 정보 문제 해결

이 문서는 Azure Monitor 통합 문서 기반 정보를 사용 하는 경우 발생할 수 있는 몇 가지 일반적인 문제를 진단 하 고 해결 하는 데 도움이 됩니다.


## <a name="why-can-i-only-see-200-resources"></a>200 리소스만 볼 수 있는 이유는 무엇 인가요?

선택한 리소스의 수는 선택한 구독 수에 관계 없이 200 개로 제한 됩니다.

## <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>대시보드에서 최근에 고정 된 타일을 클릭 하면 어떻게 되나요?

* 타일에서 아무 곳이나 클릭하면 타일이 고정된 탭으로 이동됩니다. 예를 들어 "개요" 탭에서 그래프를 고정 하면 대시보드에서 해당 타일을 클릭 하면 해당 기본 보기가 열립니다. 그러나 저장 된 복사본에서 그래프를 고정 하면 저장 된 복사본의 보기가 열립니다.
* 제목 왼쪽 위에 있는 필터 아이콘을 클릭하면 "타일 설정 구성" 탭이 열립니다.
* 오른쪽 위의 타원 아이콘은 "제목 데이터 사용자 지정", "사용자 지정", "새로 고침" 및 "대시보드에서 제거" 옵션을 제공합니다.

## <a name="what-happens-when-i-save-a-workbook"></a>통합 문서를 저장 하면 어떻게 되나요?

* 통합 문서를 저장 하면 편집 내용이 포함 된 통합 문서의 새 복사본을 만들고 제목을 변경할 수 있습니다. 저장은 통합 문서를 덮어쓰지 않으며 현재 통합 문서는 항상 기본 보기가 됩니다.
* **저장되지 않은** 통합 문서는 기본 보기일 뿐입니다.

## <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>포털에서 내 구독이 모두 표시 되지 않는 이유는 무엇 인가요?

포털 실행에는 선택한 구독에 대한 데이터만 표시됩니다. 선택한 구독을 변경하려면 오른쪽 위로 이동하여 필터 아이콘이 있는 Notebook을 클릭합니다. 이 옵션은 **디렉터리 + 구독** 탭을 표시 합니다.

![디렉터리 + 구독](./media/storage-insights-overview/fqa3.png)

## <a name="what-is-time-range"></a>시간 범위 정의

시간 범위는 특정 시간 프레임의 데이터를 표시합니다. 예를 들어 시간 범위가 24시간인 경우 지난 24시간 동안의 데이터를 표시합니다.

## <a name="what-is-time-granularity-time-grain"></a>시간 세분성 이란? (시간 세분화)

시간 세분성은 두 데이터 요소 간의 시간 차이입니다. 예를 들어 시간 조직을 1초로 설정하면 메트릭이 초당 수집됩니다.

## <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>통합 문서의 일부를 대시보드에 고정 하면 시간 세분성은 무엇 인가요?

기본 시간 세분성은 자동으로 설정되며 현재 변경할 수 없습니다.

## <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>내 대시보드에서 통합 문서 단계의 시간 범위/시간 범위를 변경 어떻게 할까요?

기본적으로 대시보드 타일의 시간 범위는 24시간으로 설정되며 이 값을 변경하려면 오른쪽 위에 있는 줄임표를 클릭하여 **타일 데이터 사용자 지정**을 선택하고 "제목 수준에서 대시보드 시간 설정 재정의" 확인란을 선택한 다음 드롭다운 메뉴를 사용하여 시간 범위를 선택합니다.  

![타일의 오른쪽 모서리에 있는 줄임표를 선택하고 이 데이터 사용자 지정을 선택합니다.](./media/storage-insights-overview/fqa-data-settings.png)

![타일 설정 구성에서 timespan 드롭다운을 선택 하 여 시간 범위/시간 범위를 변경 합니다.](./media/storage-insights-overview/fqa-timespan.png)

## <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>대시보드에 고정 된 통합 문서 또는 통합 문서 단계의 제목을 변경 어떻게 할까요?

대시보드에 고정된 통합 문서 또는 통합 문서 단계의 제목은 통합 문서에 있던 것과 동일한 이름을 유지합니다. 제목을 변경하려면 사용자의 통합 문서 복사본을 저장해야 합니다. 그런 다음 저장을 누르기 전에 통합 문서 이름을 지정할 수 있습니다.

![위쪽에서 저장을 선택하여 통합 문서의 복사본을 저장하고 이름을 변경합니다.](./media/storage-insights-overview/fqa-change-workbook-name.png)

저장 된 통합 문서의 단계 이름을 변경 하려면 단계에서 편집을 선택 하 고 설정 아래에서 기어를 선택 합니다.

![통합 문서 단계 아래쪽에서 편집을 선택하여 설정 열기](./media/storage-insights-overview/fqa-edit.png)
![설정 맨 아래에서 기어를 선택하여 단계 이름을 변경할 수 있음](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>다음 단계

통합 문서를 지원 하도록 디자인 된 시나리오, 새 보고서를 작성 하 고 기존 보고서를 사용자 지정 하는 방법 등에 대 한 자세한 내용은 [Azure Monitor 통합 문서를 사용 하 여 대화형 보고서 만들기](../platform/workbooks-overview.md)를 검토 하세요.
