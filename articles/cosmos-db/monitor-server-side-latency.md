---
title: Azure Cosmos DB에서 작업에 대 한 서버 쪽 대기 시간을 모니터링 하는 방법
description: Azure Cosmos DB 계정 또는 컨테이너에서 작업에 대 한 서버 대기 시간을 모니터링 하는 방법에 대해 알아봅니다. Azure Cosmos DB 계정의 소유자는 Azure Cosmos 계정에 대 한 서버 쪽 대기 시간 문제를 이해할 수 있습니다.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 5be2365fb5850c3f45b320d66c114fb791b22c3e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85262704"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>Azure Cosmos DB 컨테이너 또는 계정에서 작업에 대 한 서버 쪽 대기 시간을 모니터링 하는 방법

Azure Cosmos DB용 Azure Monitor는 계정을 모니터링하고 대시보드를 만들 수 있는 메트릭 보기를 제공합니다. Azure Cosmos DB 메트릭은 기본적으로 수집되며, 아무것도 명시적으로 활성화하거나 구성할 필요가 없습니다. 서버 쪽 대기 시간 메트릭은 작업의 서버 쪽 대기 시간을 확인 하는 데 사용 됩니다. Azure Cosmos DB는 직접 연결을 사용 하는 지점 읽기/쓰기 작업에 대해 10 밀리초 미만의 SLA를 제공 합니다. 시점 읽기 및 쓰기 작업의 경우 sla [문서](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)에 설명 된 대로 sla가 계산 됩니다.

다음과 같은 시점 작업에 대해 비정상적으로 긴 대기 시간이 표시 되는 경우

* 직접 모드에서 파티션 키 및 ID를 사용 하는 get 또는 set 작업
* 읽기 또는 쓰기 작업 또는
* 쿼리

진단 로그를 조회 하 여 반환 되는 데이터의 크기를 확인할 수 있습니다. 쿼리 작업에 대해 지속적으로 긴 대기 시간이 표시 되는 경우 반환 되는 데이터 크기, [처리량 또는 r u/초](cosmosdb-monitor-resource-logs.md#diagnostic-queries) 또는 지정 된 기간에 이러한 작업 수에 대 한 진단 로그를 조회할 수 있습니다. 이러한 방식으로 서버 쪽 대기 시간 문제를 디버그할 수 있습니다.

## <a name="view-the-server-side-latency-metric"></a>서버 쪽 대기 시간 메트릭 보기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 왼쪽 탐색 모음에서 **모니터** 를 선택 하 고 **메트릭**을 선택 합니다.

   :::image type="content" source="./media/monitor-server-side-latency/monitor-metrics-blade.png" alt-text="Azure Monitor의 메트릭 창":::

1. **메트릭** 창 > **리소스 선택**에서 필요한 **구독** 및 **리소스 그룹**을 선택합니다. **리소스 유형**으로 **Azure Cosmos DB 계정**을 선택하고, 기존 Azure Cosmos 계정 중 하나를 선택한 후 **적용**을 선택합니다.
   
   :::image type="content" source="./media/monitor-server-side-latency/select-cosmos-db-account.png" alt-text="Azure Cosmos DB 계정을 선택 하 여 메트릭 보기":::

1. 다음으로 사용 가능한 메트릭 목록에서 **서버 쪽 대기 시간** 메트릭을 선택 합니다. 이 목록에서 사용 가능한 모든 메트릭에 대해 자세히 알아 보려면 [범주별 메트릭](monitor-cosmos-db-reference.md) 문서를 참조하세요. 이 예제에서는 **서버 쪽 대기 시간** 및 **평균** 을 집계 값으로 선택 하겠습니다. 이러한 세부 정보 외에 메트릭의 **시간 범위**와 **시간 단위**를 선택할 수도 있습니다. 최대는 지난 30일 동안의 메트릭을 볼 수 있습니다.  필터를 적용하면 필터에 기반하여 차트가 표시됩니다. 선택한 기간에 분당 서버 쪽 대기 시간을 확인할 수 있습니다.  

   :::image type="content" source="./media/monitor-server-side-latency/server-side-latency-metric.png" alt-text="Azure Portal에서 서버 쪽 대기 시간 메트릭을 선택 합니다.":::

## <a name="filters-for-server-side-latency"></a>서버 쪽 대기 시간에 대 한 필터

메트릭을 필터링 하 고 특정 **CollectionName**, **connectionmode**, **DatabaseName**, **OperationType**, **Region**및 **PublicAPIType**에 의해 표시 되는 차트를 가져올 수도 있습니다. 

메트릭을 필터링 하려면 **필터 추가** 를 선택 하 고 **PublicAPIType** 와 같은 필수 속성을 선택한 다음 **sql**값을 선택 합니다. **OperationType**에 대 한 다른 필터를 추가 합니다. 그러면 그래프는 선택한 기간 동안 여러 작업에 대 한 서버 쪽 대기 시간을 표시 합니다. 저장 프로시저를 통해 실행되는 작업은 기록되지 않으므로 OperationType 메트릭 아래에서 사용할 수 없습니다.

각 작업에 대 한 **서버 쪽 대기 시간** 메트릭은 다음 이미지와 같이 표시 됩니다.

:::image type="content" source="./media/monitor-server-side-latency/server-side-latency-filters.png" alt-text="서버 쪽 대기 시간 메트릭에 대 한 필터":::

**분할 적용** 옵션을 사용 하 여 메트릭을 그룹화 할 수도 있습니다.  

## <a name="next-steps"></a>다음 단계

* Azure에서 [진단 설정](cosmosdb-monitor-resource-logs.md)을 사용하여 Azure Cosmos DB 데이터를 모니터링합니다.
* [Azure Cosmos DB 컨트롤 플레인 작업 감사](audit-control-plane-logs.md)
