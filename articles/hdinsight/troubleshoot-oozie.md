---
title: Azure HDInsight에서 Apache Oozie 문제 해결
description: Azure HDInsight의 특정 Apache Oozie 오류 문제를 해결 합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: fb795a9d7100019b2b1820c592f87025b77f5878
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045861"
---
# <a name="troubleshoot-apache-oozie-in-azure-hdinsight"></a>Azure HDInsight에서 Apache Oozie 문제 해결

Apache Oozie UI를 사용 하 여 Oozie 로그를 볼 수 있습니다. Oozie UI에는 워크플로에서 시작된 MapReduce 작업에 대한 JobTracker 로그의 링크도 포함되어 있습니다. 문제 해결 패턴은 다음과 같습니다.

1. Oozie 웹 UI에서 작업을 확인합니다.

2. 특정 작업에 대 한 오류 또는 오류가 발생 하는 경우 **오류 메시지** 필드에서 오류에 대 한 자세한 정보를 제공 하는지 확인 하려면 작업을 선택 합니다.

3. 사용 가능한 경우 동작의 URL을 사용하여 해당 동작에 대한 자세한 정보(예: JobTracker 로그)를 확인할 수 있습니다.

다음은 발생할 수 있는 특정 오류 및 해결 방법입니다.

## <a name="ja009-cant-initialize-cluster"></a>JA009: 클러스터를 초기화할 수 없음

### <a name="issue"></a>문제점

작업 상태가 **일시 중단됨**으로 변경되었습니다. 작업에 대한 세부 정보에 `RunHiveScript` 상태가 **START_MANUAL**로 표시됩니다. 동작을 선택하면 다음과 같은 오류 메시지가 표시됩니다.

```output
JA009: Cannot initialize Cluster. Please check your configuration for map
```

### <a name="cause"></a>원인

**job.xml** 파일에 사용된 Azure Blob Storage 주소에 스토리지 컨테이너 또는 스토리지 계정 이름이 포함되어 있지 않습니다. Blob Storage 주소 형식은 `wasbs://containername@storageaccountname.blob.core.windows.net`이어야 합니다.

### <a name="resolution"></a>해결 방법

해당 작업이 사용하는 Blob Storage 주소를 변경합니다.

---

## <a name="ja002-oozie-isnt-allowed-to-impersonate-ltusergt"></a>JA002: Oozie 사용자를 가장할 수 없습니다. &lt;&gt;

### <a name="issue"></a>문제점

작업 상태가 **일시 중단됨**으로 변경되었습니다. 작업에 대한 세부 정보에 `RunHiveScript` 상태가 **START_MANUAL**로 표시됩니다. 동작을 선택하면 다음 오류 메시지가 표시됩니다.

```output
JA002: User: oozie is not allowed to impersonate <USER>
```

### <a name="cause"></a>원인

현재 권한 설정에서 Oozie가 지정된 사용자 계정을 가장하도록 허용하지 않습니다.

### <a name="resolution"></a>해결 방법

Oozie는 그룹의 사용자를 가장할 수 있습니다 **`users`** . `groups USERNAME` 을 사용하여 사용자 계정이 멤버로 속해 있는 그룹을 확인합니다. 사용자가 그룹의 멤버가 아닌 경우 **`users`** 다음 명령을 사용 하 여 사용자를 그룹에 추가 합니다.

```bash
sudo adduser USERNAME users
```

> [!NOTE]  
> HDInsight에서 사용자가 그룹에 추가된 것을 인식하는 데 몇 분 정도 걸릴 수 있습니다.

---

## <a name="launcher-error-sqoop"></a>시작 관리자 오류(Sqoop)

### <a name="issue"></a>문제점

작업 상태가 **중단됨**으로 변경되었습니다. 작업에 대한 세부 정보에 `RunSqoopExport` 상태가 **ERROR**로 표시됩니다. 동작을 선택하면 다음 오류 메시지가 표시됩니다.

```output
Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]
```

### <a name="cause"></a>원인

Sqoop가 데이터베이스에 액세스하는 데 필요한 데이터베이스 드라이버를 로드할 수 없습니다.

### <a name="resolution"></a>해결 방법

Oozie 작업에서 Sqoop를 사용하는 경우 작업이 사용하는 다른 리소스(예: workflow.xml)와 함께 데이터베이스 드라이버를 포함해야 합니다. 또한 workflow.xml의 `<sqoop>...</sqoop>` 섹션에서 데이터베이스 드라이버가 포함된 보관 파일을 참조합니다.

예를 들어 [Hadoop Oozie 워크플로 사용](hdinsight-use-oozie-linux-mac.md)의 작업 예를 보려면 다음 단계를 사용 합니다.

1. `mssql-jdbc-7.0.0.jre8.jar` **/Tutorials/useoozie** 디렉터리에 파일을 복사 합니다.

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. `workflow.xml`을 수정하여 `</sqoop>` 위의 새 줄에 다음 XML을 추가합니다.

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원**을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.
