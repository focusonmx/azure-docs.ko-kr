---
title: 데이터베이스 가져오기 및 내보내기 시간이 오래 걸립니다.
description: Azure SQL Database 및 Azure SQL Managed Instance Import/Export 서비스는 데이터베이스를 가져오거나 내보내는 데 시간이 오래 걸립니다.
ms.custom: seo-lt-2019, sqldbrb=1
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: f98cfcd49806061a969a9227f9ade05f70ce79ff
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85982313"
---
# <a name="azure-sql-database-and-managed-instance-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Azure SQL Database 및 Managed Instance Import/Export 서비스는 데이터베이스를 가져오거나 내보내는 데 시간이 오래 걸립니다.

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

가져오기/내보내기 서비스를 사용 하는 경우 프로세스가 예상 보다 오래 걸릴 수 있습니다. 이 문서에서는 이러한 지연 및 대체 해결 방법의 가능한 원인을 설명 합니다.

## <a name="azure-sql-database-importexport-service"></a>Azure SQL Database Import/Export 서비스

Azure SQL Database Import/Export 서비스는 모든 Azure 데이터 센터에서 실행 되는 REST 기반 웹 서비스입니다. 이 서비스는 [데이터베이스 가져오기](database-import.md#using-azure-portal) 또는 [내보내기](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) 옵션을 사용 하 여 Azure Portal에서 데이터베이스를 이동할 때 호출 됩니다. 서비스는 Azure SQL Database와 Azure Blob storage 간에 가져오기 및 내보내기를 수행 하는 무료 요청 큐 및 계산 서비스를 제공 합니다.

가져오기 및 내보내기 작업은 기존 물리적 데이터베이스 백업을 나타내지 않고, 특수 BACPAC 형식을 사용 하는 데이터베이스의 논리적 백업입니다. BACPAC 형식을 사용 하면 Microsoft SQL Server, Azure SQL Database 및 Azure SQL Managed Instance의 버전 마다 다를 수 있는 물리적 형식을 사용 하지 않아도 됩니다.

## <a name="what-causes-delays-in-the-process"></a>프로세스에서 지연이 발생 하는 이유는 무엇 인가요?

Azure SQL Database 가져오기/내보내기 서비스는 가져오기 및 내보내기 작업을 처리 하기 위해 지역 당 제한 된 수의 계산 Vm (가상 머신)을 제공 합니다. 계산 Vm은 지역 마다 호스트 되므로 가져오기 또는 내보내기에서 지역 간 대역폭 지연 및 요금을 피할 수 있습니다. 동일한 지역에서 동시에 너무 많은 요청이 수행 되는 경우 작업을 처리 하는 동안 상당한 지연이 발생할 수 있습니다. 요청을 완료 하는 데 필요한 시간은 몇 초에서 몇 시간까지 달라질 수 있습니다.

> [!NOTE]
> 요청이 4 일 이내에 처리 되지 않으면 서비스에서 자동으로 요청을 취소 합니다.

## <a name="recommended-solutions"></a>권장 해결 방법

데이터베이스 내보내기가 실수로 인 한 데이터 삭제의 복구에만 사용 되는 경우 모든 Azure SQL Database 버전은 시스템 생성 백업에서 셀프 서비스 복원 기능을 제공 합니다. 그러나 다른 이유로 이러한 내보내기가 필요 하며 지속적으로 빠르고 예측 가능한 가져오기/내보내기 성능이 필요한 경우에는 다음 옵션을 고려 하십시오.

* [SQLPackage 유틸리티를 사용 하 여 BACPAC 파일로 내보냅니다](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility).
* [SSMS (SQL Server Management Studio)를 사용 하 여 BACPAC 파일로 내보냅니다](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms).
* Microsoft SQL Server Data-Tier Application Framework (DacFx) API를 사용 하 여 코드에서 직접 BACPAC 가져오기 또는 내보내기를 실행 합니다. 자세한 내용은 다음을 참조하세요.
  * [데이터 계층 응용 프로그램 내보내기](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Microsoft SqlServer 네임 스페이스](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [다운로드 DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-a-database"></a>데이터베이스를 내보내거나 가져올 때 고려해 야 할 사항

* 이 문서에서 설명 하는 모든 메서드는 DTU (데이터베이스 트랜잭션 단위) 할당량을 사용 하 여 Azure SQL Database 서비스에 의해 제한이 발생 합니다. [Azure Portal 데이터베이스에 대 한 DTU 통계를 볼](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#sql-database-resource-monitoring)수 있습니다. 데이터베이스가 리소스 제한에 도달 하면 [서비스 계층을 업그레이드](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) 하 여 더 많은 리소스를 추가 합니다.
* Sqlpackage 유틸리티 또는 사용자 지정 DAC 응용 프로그램과 같은 클라이언트 응용 프로그램을 데이터베이스와 동일한 지역에 있는 VM에서 실행 하는 것이 가장 좋습니다. 그렇지 않으면 네트워크 대기 시간과 관련 된 성능 문제가 발생할 수 있습니다.
* 클러스터형 인덱스가 없는 큰 테이블을 내보내면 매우 느리거나 오류가 발생할 수 있습니다. 이 동작은 테이블을 병렬로 분할 하 고 내보낼 수 없기 때문에 발생 합니다. 대신 단일 트랜잭션으로 내보내야 하며 특히 내보내기 중에 성능이 저하 되 고 잠재적으로 오류가 발생 합니다. 특히 많은 테이블이 필요 합니다.


## <a name="related-documents"></a>관련 문서

[데이터베이스를 내보낼 때의 고려 사항](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
