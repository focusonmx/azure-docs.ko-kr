---
title: 저장 프로시저 사용
description: 솔루션 개발을 위한 Synapse SQL 풀 (데이터 웨어하우스)에서 저장 프로시저를 구현 하기 위한 팁입니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 2b106a28514179d9be43b3c56ea8030eaf7ba13f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494998"
---
# <a name="use-stored-procedures-in-sql-pool"></a>SQL 풀에서 저장 프로시저 사용

솔루션 개발을 위한 Synapse SQL 풀 (데이터 웨어하우스)에서 저장 프로시저를 구현 하기 위한 팁입니다.

## <a name="what-to-expect"></a>예상 프로그램

SQL 풀은 SQL Server에서 사용 되는 다양 한 T-sql 기능을 지원 합니다. 무엇보다도 솔루션의 성능을 최대화하기 위해 사용할 수 있는 특정 기능 확장 사항이 있습니다.

그러나 SQL 풀의 규모와 성능을 유지 하기 위해 동작 차이가 있는 일부 기능과 기능 및 지원 되지 않는 다른 기능도 있습니다.

## <a name="stored-procedures-in-sql-pool"></a>SQL 풀의 저장 프로시저

저장된 프로시저는 SQL 코드를 캡슐화하여 데이터 웨어하우스의 데이터에 가까이 저장하는 좋은 방법입니다. 관리 가능한 단위로 코드를 캡슐화하여 저장 프로시저를 사용하면 개발자가 솔루션을 모듈화하여 코드의 더 큰 재유용성을 용이하게 합니다. 각 저장된 프로시저로 매개 변수를 더 유연하게 할 수도 있습니다.

SQL 풀은 간소화 되 고 간소화 된 저장 프로시저 구현을 제공 합니다. SQL Server와 비교하여 가장 큰 차이점은 저장된 프로시저가 미리 컴파일된 코드가 아닙니다. 데이터 웨어하우스에서 컴파일 시간은 대용량 데이터에 대해 쿼리를 실행하는 데 걸리는 시간에 비해 작습니다. 저장 프로시저 코드가 대형 쿼리에 적절하게 최적화되었는지 확인하는 것은 더욱 중요합니다. 시간, 분 및 초(밀리초가 아닌)를 저장하는 것이 목표입니다. 그러므로 저장된 프로시저를 SQL 논리에 대한 컨테이너로 생각하는 것이 더욱 도움이 됩니다.

SQL 풀에서 저장 프로시저를 실행할 때 SQL 문은 런타임에 구문 분석, 변환 및 최적화 됩니다. 이 프로세스 중 각 명령문이 분산 쿼리로 변환됩니다. 데이터에 대해 실행되는 SQL 코드는 전송된 쿼리와 다릅니다.

## <a name="nesting-stored-procedures"></a>중첩 저장 프로시저

저장 프로시저가 다른 저장 프로시저를 호출하거나 동적 SQL을 실행하면 내부 저장 프로시저 또는 코드 호출을 중첩된 것이라고 합니다.

SQL 풀은 최대 8 개의 중첩 수준을 지원 합니다. 이는 SQL Server와 약간 다릅니다. SQL Server의 중첩 수준은 32입니다.

상위 수준 저장 프로시저 호출은 중첩 수준 1과 같습니다.

```sql
EXEC prc_nesting
```

또한 저장 프로시저가 다른 EXEC 호출을 만드는 경우 중첩 수준이 2로 증가합니다.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```

그 다음 두 번째 프로시저가 일부 동적 SQL을 실행하면 중첩 수준이 3으로 증가합니다.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

> [!NOTE]
> SQL 풀은 현재 [@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)을 지원 하지 않습니다. 중첩 수준을 추적해야 합니다. 8개의 중첩 수준을 초과할 가능성은 없지만 초과하는 경우 이 한도 내에서 중첩 수준에 맞도록 코드를 다시 작업해야 합니다.

## <a name="insertexecute"></a>INSERT..EXECUTE

SQL 풀에서는 INSERT 문을 사용 하 여 저장 프로시저의 결과 집합을 사용할 수 없습니다. 그러나 대체 방법을 사용할 수 있습니다. 예를 들어 [임시 테이블](develop-tables-temporary.md)의 문서를 참조하세요.

## <a name="limitations"></a>제한 사항

SQL 풀에서 구현 되지 않은 Transact-sql 저장 프로시저의 몇 가지 측면이 있습니다.

아래에 이 계정과 키의 예제가 나와 있습니다.

* 임시 저장 프로시저
* 숫자가 매겨진 저장 프로시저
* 확장된 저장 프로시저
* CLR 저장 프로시저
* 암호화 옵션
* 복제 옵션
* 테이블 반환 매개 변수
* 읽기 전용 매개 변수
* 기본 매개 변수
* 실행 컨텍스트
* return 문

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [개발 개요](develop-overview.md)를 참조하세요.
