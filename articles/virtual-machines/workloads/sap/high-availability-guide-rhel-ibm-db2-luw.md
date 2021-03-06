---
title: RHEL의 Azure Vm (가상 머신)에서 IBM Db2 HADR 설정 Microsoft Docs
description: Azure Vm (가상 머신) RHEL에서 IBM Db2 LUW의 고가용성을 설정 합니다.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2020
ms.author: juergent
ms.openlocfilehash: 527d9e2e43a4003dd5300c26fc58b1e456186351
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077393"
---
# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Red Hat Enterprise Linux Server의 Azure VM에서 IBM DB2 LUW의 고가용성

HADR (고가용성 [및 재해 복구) 구성](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) 에서 LINUX, UNIX 및 WINDOWS 용 IBM DB2 (LUW)는 주 데이터베이스 인스턴스를 실행 하는 노드 하 나와 보조 데이터베이스 인스턴스를 실행 하는 하나 이상의 노드로 구성 됩니다. 주 데이터베이스 인스턴스에 대 한 변경 내용은 구성에 따라 동기식 또는 비동기적으로 보조 데이터베이스 인스턴스에 복제 됩니다. 

이 문서에서는 Azure Vm (가상 머신)을 배포 및 구성 하 고, 클러스터 프레임 워크를 설치 하 고, HADR 구성으로 IBM Db2 LUW을 설치 하는 방법을 설명 합니다. 

이 문서에서는 HADR 또는 SAP 소프트웨어 설치에서 IBM Db2 LUW을 설치 하 고 구성 하는 방법에 대해 다루지 않습니다. 이러한 작업을 수행 하는 데 도움이 되도록 SAP 및 IBM 설치 설명서에 대 한 참조를 제공 합니다. 이 문서에서는 Azure 환경과 관련 된 파트에 대해 집중적으로 설명 합니다. 

지원 되는 IBM Db2 버전은 SAP note [1928533]에 설명 된 대로 10.5 이상입니다.

설치를 시작 하기 전에 다음 SAP 참고 사항 및 설명서를 참조 하세요.

| SAP note | 설명 |
| --- | --- |
| [1928533] | Azure의 SAP 애플리케이션: 지원 제품 및 Azure VM 유형 |
| [2015553] | Azure의 SAP: 지원 필수 조건 |
| [2178632] | Azure의 SAP 용 주요 모니터링 메트릭 |
| [2191498] | Azure 포함 Linux의 SAP: 고급 모니터링 |
| [2243692] | Azure의 Linux (IaaS) VM: SAP 라이선스 문제 |
| [2002167] | Red Hat Enterprise Linux 7.x: 설치 및 업그레이드 |
| [2694118] | Azure에서 HA 추가 기능 Red Hat Enterprise Linux |
| [1999351] | SAP용 고급 Azure 모니터링 문제 해결 |
| [2233094] | DB6: Linux, UNIX 및 Windows 용 IBM Db2를 사용 하는 Azure의 SAP 응용 프로그램-추가 정보 |
| [1612105] | DB6: d b 2에서 HADR에 대 한 FAQ |


| 설명서 | 
| --- |
| [Sap Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Linux에 필요한 모든 sap note |
| [Linux에서 SAP 용 Azure Virtual Machines 계획 및 구현][planning-guide] 가이드 |
| [Linux에서 SAP 용 Azure Virtual Machines 배포][deployment-guide] (이 문서) |
| [Linux에서 SAP 용 Azure VIRTUAL MACHINES DBMS (데이터베이스 관리 시스템) 배포][dbms-guide] 가이드 |
| [Azure의 SAP 워크로드 계획 및 배포 검사 목록][azr-sap-plancheck] |
| [Red Hat Enterprise Linux 7에 대 한 고가용성 추가 기능 개요][rhel-ha-addon] |
| [High Availability Add-On Administration][rhel-ha-admin](고가용성 추가 기능 관리) |
| [High Availability Add-On Reference][rhel-ha-ref](고가용성 추가 기능 참조) |
| [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members][rhel-azr-supp](RHEL 고가용성 클러스터용 지원 정책 - Microsoft Azure Virtual Machines(클러스터 멤버))
| [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure][rhel-azr-inst](Microsoft Azure에서 Red Hat Enterprise Linux 7.4 이상 고가용성 클러스터 설치 및 구성)
| [SAP 워크로드용 IBM DB2 Azure Virtual Machines DBMS 배포][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR 10.5][db2-hadr-10.5] |
| [RHEL 고가용성 클러스터에 대 한 지원 정책-클러스터에서 Linux, Unix 및 Windows 용 IBM Db2 관리][rhel-db2-supp]



## <a name="overview"></a>개요
고가용성을 위해 HADR을 사용 하는 IBM Db2 LUW는 [azure 가용성 집합](../../windows/tutorial-availability-sets.md) 또는 [Azure 가용성 영역](./sap-ha-availability-zones.md)에 배포 된 두 개 이상의 azure virtual machines에 설치 됩니다. 

다음 그래픽은 두 데이터베이스 서버 Azure Vm의 설정을 표시 합니다. 데이터베이스 서버 Azure Vm 모두에는 자체 저장소가 연결 되어 있고 실행 되 고 있습니다. HADR에서 Azure Vm 중 하나에 있는 하나의 데이터베이스 인스턴스에는 주 인스턴스의 역할이 있습니다. 모든 클라이언트는 주 인스턴스에 연결 됩니다. 데이터베이스 트랜잭션의 모든 변경 내용은 Db2 트랜잭션 로그에서 로컬로 유지 됩니다. 트랜잭션 로그 레코드가 로컬로 유지 되 면 레코드는 TCP/IP를 통해 두 번째 데이터베이스 서버, 대기 서버 또는 대기 인스턴스의 데이터베이스 인스턴스로 전송 됩니다. 대기 인스턴스는 전송 된 트랜잭션 로그 레코드를 롤포워드 하 여 로컬 데이터베이스를 업데이트 합니다. 이러한 방식으로 대기 서버는 주 서버와 동기화 된 상태로 유지 됩니다.

HADR은 복제 기능입니다. 오류 검색 및 자동 인수 또는 장애 조치 (failover) 기능이 없습니다. 데이터베이스 관리자는 대기 서버에 대 한 인수 또는 전송을 수동으로 시작 해야 합니다. 자동 인수 및 오류 감지를 수행 하기 위해 Linux Pacemaker 클러스터링 기능을 사용할 수 있습니다. Pacemaker는 두 데이터베이스 서버 인스턴스를 모니터링 합니다. 주 데이터베이스 서버 인스턴스가 충돌 하면 Pacemaker는 대기 서버에서 *자동* HADR 인수를 시작 합니다. 또한 Pacemaker는 가상 IP 주소가 새 주 서버에 할당 되었는지 확인 합니다.

![IBM Db2 고가용성 개요](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

SAP 응용 프로그램 서버를 주 데이터베이스에 연결 하려면 가상 호스트 이름 및 가상 IP 주소가 필요 합니다. 장애 조치 (failover) 시 SAP 응용 프로그램 서버가 새 주 데이터베이스 인스턴스에 연결 됩니다. Azure 환경에서 [azure 부하 분산 장치](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) 는 IBM DB2의 HADR에 필요한 방식으로 가상 IP 주소를 사용 하는 데 필요 합니다. 

IBM db2를 사용 하는 IBM Db2 LUW 및 Pacemaker가 항상 사용 가능한 SAP 시스템 설정에 어떻게 부합 하는지 완전히 이해할 수 있도록 다음 이미지는 IBM Db2 데이터베이스를 기반으로 하는 SAP 시스템의 항상 사용 가능한 설정에 대 한 개요를 제공 합니다. 이 문서에서는 IBM Db2에 대해서만 설명 하지만 SAP 시스템의 다른 구성 요소를 설정 하는 방법에 대 한 다른 문서에 대 한 참조를 제공 합니다.

![IBM DB2 고가용성 전체 환경 개요](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>필요한 단계에 대 한 개략적인 개요
IBM Db2 구성을 배포 하려면 다음 단계를 수행 해야 합니다.

  + 환경을 계획 합니다.
  + VM을 배포합니다.
  + RHEL Linux를 업데이트 하 고 파일 시스템을 구성 합니다.
  + Pacemaker를 설치 하 고 구성 합니다.
  + [Glusterfs 클러스터][glusterfs] 또는 [Azure NetApp Files][anf-rhel] 설정
  + [별도의 클러스터에 Ascs/ERS][ascs-ha-rhel]를 설치 합니다.
  + Distributed/High Availability 옵션 (SWPM)을 사용 하 여 IBM Db2 데이터베이스를 설치 합니다.
  + 보조 데이터베이스 노드 및 인스턴스를 설치 하 고 만들고 HADR을 구성 합니다.
  + HADR이 작동 하는지 확인 합니다.
  + Pacemaker 구성을 적용 하 여 IBM Db2를 제어 합니다.
  + Azure Load Balancer를 구성 합니다.
  + 기본 및 대화 상자 응용 프로그램 서버를 설치 합니다.
  + SAP 응용 프로그램 서버의 구성을 확인 하 고 조정 합니다.
  + 장애 조치 (failover) 및 인수 테스트를 수행 합니다.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>HADR을 사용 하 여 IBM Db2 LUW를 호스팅하기 위한 Azure 인프라 계획

배포를 실행 하기 전에 계획 프로세스를 완료 합니다. 계획은 Azure에서 HADR을 사용 하 여 Db2 구성을 배포 하기 위한 토대를 구축 합니다. IMB Db2 LUW (SAP 환경의 데이터베이스 부분) 계획에 포함 해야 하는 주요 요소는 다음 표에 나와 있습니다.

| 토픽 | 간단한 설명 |
| --- | --- |
| Azure 리소스 그룹 정의 | VM, VNet, Azure Load Balancer 및 기타 리소스를 배포 하는 리소스 그룹입니다. 기존 또는 신규 일 수 있습니다. |
| 가상 네트워크/서브넷 정의 | IBM Db2 및 Azure Load Balancer에 대 한 Vm이 배포 되 고 있습니다. 기존 또는 새로 만들 수 있습니다. |
| IBM Db2 LUW을 호스트 하는 가상 머신 | VM 크기, 저장소, 네트워킹, IP 주소입니다. |
| IBM Db2 데이터베이스용 가상 호스트 이름 및 가상 IP| SAP 응용 프로그램 서버 연결에 사용 되는 가상 IP 또는 호스트 이름입니다. **virt-hostname**, **db-virt-ip**. |
| Azure 펜스 | 분할을 방지 하는 메서드를 사용 하는 것이 금지 됩니다. |
| Azure Load Balancer | 기본 또는 표준 (권장)을 사용 하 고 Db2 데이터베이스용 프로브 포트 (권장 62500) **프로브 포트**를 사용 합니다. |
| 이름 확인| 환경에서 이름 확인이 작동 하는 방식입니다. DNS 서비스는 매우 권장 됩니다. 로컬 호스트 파일을 사용할 수 있습니다. |
    
Azure의 Linux Pacemaker에 대 한 자세한 내용은 [azure에서 Pacemaker on Red Hat Enterprise Linux 설정][rhel-pcs-azr]을 참조 하세요.

## <a name="deployment-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux에 배포

IBM Db2 LUW의 리소스 에이전트는 Red Hat Enterprise Linux Server HA 추가 기능에 포함 되어 있습니다. 이 문서에서 설명 하는 설치의 경우 SAP 용 Red Hat Enterprise Linux를 사용 해야 합니다. Azure Marketplace에는 새 Azure 가상 컴퓨터를 배포 하는 데 사용할 수 있는 SAP 용 Red Hat Enterprise Linux 7.4의 이미지가 포함 되어 있습니다. Azure VM Marketplace에서 VM 이미지를 선택할 때 Azure Marketplace를 통해 Red Hat에서 제공 하는 다양 한 지원 또는 서비스 모델에 대해 알고 있어야 합니다.

### <a name="hosts-dns-updates"></a>호스트: DNS 업데이트
호스트 이름 확인을 위한 적절 한 IP 주소를 사용 하도록 DNS 서버를 업데이트 하 고 가상 호스트 이름을 비롯 한 모든 호스트 이름 목록을 만듭니다. DNS 서버가 없거나 DNS 항목을 업데이트 하거나 만들 수 없는 경우이 시나리오에 참여 하는 개별 Vm의 로컬 호스트 파일을 사용 해야 합니다. 호스트 파일 항목을 사용 하는 경우에는 SAP 시스템 환경의 모든 Vm에 항목이 적용 되는지 확인 합니다. 그러나 이상적으로는 Azure로 확장 되는 DNS를 사용 하는 것이 좋습니다.


### <a name="manual-deployment"></a>수동 배포

선택한 OS가 ibm Db2 용 IBM/SAP LUW에서 지원 되는지 확인 합니다. Azure Vm 및 Db2 릴리스에 대해 지원 되는 OS 버전 목록은 SAP note [1928533]에서 제공 됩니다. 개별 Db2 릴리스의 OS 릴리스 목록은 SAP 제품 가용성 매트릭스에서 확인할 수 있습니다. 이 이상 Red Hat Enterprise Linux 버전의 Azure 관련 성능 향상으로 인해 SAP에 대해 최소 Red Hat Enterprise Linux 7.4을 권장 합니다.

1. 리소스 그룹을 만들거나 선택 합니다.
1. 가상 네트워크 및 서브넷을 만들거나 선택 합니다.
1. Azure 가용성 집합을 만들거나 가용성 영역을 배포 합니다.
    + 가용성 집합의 경우 최대 업데이트 도메인을 2로 설정 합니다.
1. 가상 컴퓨터 1을 만듭니다.
    + Azure Marketplace에서 SAP 이미지에 대 한 Red Hat Enterprise Linux를 사용 합니다.
    + 3 단계에서 만든 Azure 가용성 집합을 선택 하거나 가용성 영역을 선택 합니다.
1.  가상 컴퓨터 2를 만듭니다.
    + Azure Marketplace에서 SAP 이미지에 대 한 Red Hat Enterprise Linux를 사용 합니다.
    + 3 단계에서 만든 Azure 가용성 집합을 선택 하거나 가용성 영역 (3 단계와 동일한 영역이 아님)을 선택 합니다.
1. Vm에 데이터 디스크를 추가한 다음 [SAP 워크 로드에 대 한 IBM Db2 Azure VIRTUAL MACHINES DBMS 배포][dbms-db2]문서에서 파일 시스템 설정에 대 한 권장 사항을 확인 합니다.

## <a name="create-the-pacemaker-cluster"></a>Pacemaker 클러스터 만들기
    
이 IBM Db2 서버에 대 한 기본 Pacemaker 클러스터를 만들려면 [Azure에서 Pacemaker on Red Hat Enterprise Linux 설정][rhel-pcs-azr]을 참조 하세요. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>IBM Db2 LUW 및 SAP 환경 설치

IBM Db2 LUW을 기반으로 SAP 환경 설치를 시작 하기 전에 다음 문서를 검토 하세요.

+ Azure 설명서
+ SAP 설명서
+ IBM 설명서

이 설명서에 대 한 링크는이 문서의 소개 단원에서 제공 됩니다.

IBM Db2 LUW에 NetWeaver 기반 응용 프로그램을 설치 하는 방법에 대 한 SAP 설치 설명서를 확인 하세요.
Sap [설치 가이드 Finder][sap-instfind]를 사용 하 여 sap 도움말 포털에서 가이드를 찾을 수 있습니다.

다음 필터를 설정 하 여 포털에 표시 되는 가이드 수를 줄일 수 있습니다.
- "새 시스템 설치"를 선택 합니다.
- 내 데이터베이스: "Linux, Unix 및 Windows 용 IBM Db2"
- SAP NetWeaver 버전, 스택 구성 또는 운영 체제에 대 한 추가 필터

#### <a name="red-hat-firewall-rules"></a>Red Hat 방화벽 규칙
Red Hat Enterprise Linux는 기본적으로 방화벽이 사용 하도록 설정 되어 있습니다. 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>HADR을 사용한 IBM Db2 LUW 설정에 대 한 설치 힌트

기본 IBM Db2 LUW 데이터베이스 인스턴스를 설정 하려면 다음을 수행 합니다.

- 고가용성 또는 분산 옵션을 사용 합니다.
- SAP ASCS/ERS 및 데이터베이스 인스턴스를 설치 합니다.
- 새로 설치 된 데이터베이스의 백업을 수행 합니다.

> [!IMPORTANT] 
> 설치 하는 동안 설정 된 "데이터베이스 통신 포트"를 적어 씁니다. 이는 두 데이터베이스 인스턴스 모두에 대해 동일한 포트 번호 여야 합니다.
>![SAP SWPM 포트 정의](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>Azure에 대 한 IBM Db2 HADR 설정

   Azure Pacemaker 펜스 에이전트를 사용 하는 경우 다음 매개 변수를 설정 합니다.

   - HADR 피어 기간 (초) (HADR_PEER_WINDOW) = 240  
   - HADR 시간 제한 값 (HADR_TIMEOUT) = 45

초기 장애 조치/인수 테스트에 따라 앞의 매개 변수를 사용 하는 것이 좋습니다. 이러한 매개 변수 설정을 사용 하 여 장애 조치 (failover) 및 인수의 적절 한 기능을 테스트 해야 합니다. 개별 구성은 다를 수 있으므로 매개 변수를 조정 해야 할 수도 있습니다. 

> [!NOTE]
> 일반 시작이 포함 된 HADR 구성을 사용한 IBM Db2와 관련 하 여 주 데이터베이스 인스턴스를 시작 하기 전에 보조 또는 대기 데이터베이스 인스턴스가 실행 중 이어야 합니다.

   
> [!NOTE]
> Azure 및 Pacemaker와 관련 된 설치 및 구성의 경우: SAP 소프트웨어 프로 비전 관리자를 통해 설치 절차를 진행 하는 동안 IBM Db2 LUW의 고가용성에 대 한 명시적인 질문이 있습니다.
>+ **IBM Db2 pureScale**는 선택 하지 마세요.
>+ **다중 플랫폼용 IBM Tivoli 시스템 자동화 설치**를 선택 하지 마십시오.
>+ **클러스터 구성 파일 생성**을 선택 하지 않습니다.
>![SAP SWPM-DB2 HA 옵션](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


SAP 유형이 같은 시스템 복사 프로시저를 사용 하 여 대기 데이터베이스 서버를 설정 하려면 다음 단계를 실행 합니다.

1. **대상 시스템** **System copy**  >  **배포**  >  **데이터베이스 인스턴스**> 시스템 복사 옵션을 선택 합니다.
1. Backup을 사용 하 여 대기 서버 인스턴스에서 백업을 복원할 수 있도록 복사 방법으로 같은 **시스템** 을 선택 합니다.
1. 동일한 시스템 복사를 위해 데이터베이스를 복원 하는 종료 단계에 도달 하면 설치 관리자를 종료 합니다. 주 호스트의 백업에서 데이터베이스를 복원 합니다. 주 데이터베이스 서버에서 모든 후속 설치 단계가 이미 실행 되었습니다.

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>DB2 HADR에 대 한 red Hat 방화벽 규칙
D b 2에 대 한 트래픽을 허용 하는 방화벽 규칙을 추가 하 고 d b 2에서 작업을 수행 합니다.
+ 데이터베이스 통신 포트입니다. 파티션을 사용 하는 경우 해당 포트도 추가 합니다.
+ HADR 포트 (DB2 매개 변수 HADR_LOCAL_SVC의 값)
+ Azure 프로브 포트
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR 검사
데모용 및이 문서에 설명 된 절차에 대 한 데이터베이스 SID는 **ID2**입니다.

HADR을 구성 하 고 상태가 피어 이며 기본 및 대기 노드에 연결 된 후에는 다음 검사를 수행 합니다.

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
Database Member 0 -- Database ID2 -- Active -- Up 1 days 15:45:23 -- Date 2019-06-25-10.55.25.349375

                            <b>HADR_ROLE = PRIMARY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 1
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.076494 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 5
                   HEARTBEAT_EXPECTED = 52
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 5
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 369280
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 132242668
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 300
                      PEER_WINDOW_END = 06/25/2019 11:12:03.000000 (1561461123)
             READS_ON_STANDBY_ENABLED = N



#Secondary output:
Database Member 0 -- Database ID2 -- Standby -- Up 1 days 15:45:18 -- Date 2019-06-25-10.56.19.820474

                            <b>HADR_ROLE = STANDBY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 0
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.078116 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 0
                   HEARTBEAT_EXPECTED = 10
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 1
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 367360
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 0
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 1000
                      PEER_WINDOW_END = 06/25/2019 11:12:59.000000 (1561461179)
             READS_ON_STANDBY_ENABLED = N

</code></pre>



## <a name="db2-pacemaker-configuration"></a>Db2 Pacemaker 구성

노드 오류가 발생할 경우 자동 장애 조치 (failover)에 Pacemaker를 사용 하는 경우 Db2 인스턴스 및 Pacemaker를 적절 하 게 구성 해야 합니다. 이 섹션에서는 이러한 유형의 구성에 대해 설명 합니다.

다음 항목에는 접두사가 추가 됩니다.

- **[A]**: 모든 노드에 적용 가능
- **[1]**: 노드 1에만 적용 가능 
- **[2]**: 노드 2에만 적용 가능

**[A]** Pacemaker 구성에 대 한 필수 구성 요소:
1. Db2stop를 사용 하 여 사용자 db2를 사용 하는 두 데이터베이스 서버를 종료 \<sid> 합니다.
1. Db2 사용자에 대 한 셸 환경을 \<sid> */bin/ksh*로 변경 합니다.
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>Pacemaker 구성

**[1]** IBM Db2 HADR 관련 Pacemaker 구성:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]** IBM Db2 리소스 만들기:
<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' master meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-master meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-master

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-master then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

**[1]** IBM Db2 리소스를 시작 합니다.
* Pacemaker를 유지 관리 모드로 전환 합니다.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]** 클러스터 상태가 양호 이며 모든 리소스가 시작 되었는지 확인 합니다. 리소스가 실행 되는 노드는 중요 하지 않습니다.
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

온라인: [az-idb01 az-idb02]

전체 리소스 목록:

 rsc_st_azure (stonith: fence_azure_arm): 시작 az-idb01 Master/슬레이브 Set: Db2_HADR_ID2-Master [Db2_HADR_ID2] Masters: [az-idb01] 슬레이브: [az-idb02] 리소스 그룹: g_ipnc_db2id2_ID2 vip_db2id2_ID2 (ocf:: 하트 비트: IPaddr2): Started az-idb01 nc_db2id2_ID2 (ocf:: 하트 비트: azure-lb): Started az-idb01

디먼 상태: corosync: 활성/사용 안 함 pacemaker: 활성/사용 안 함 pcsd: 활성/사용
</pre>

> [!IMPORTANT]
> Pacemaker tools를 사용 하 여 Pacemaker 클러스터형 Db2 인스턴스를 관리 해야 합니다. Db2stop와 같은 db2 명령을 사용 하면 Pacemaker에서 작업을 리소스 실패로 검색 합니다. 유지 관리를 수행 하는 경우 노드 또는 리소스를 유지 관리 모드로 전환할 수 있습니다. Pacemaker는 모니터링 리소스를 일시 중단 하 고 일반 db2 관리 명령을 사용할 수 있습니다.


### <a name="configure-azure-load-balancer"></a>Azure Load Balancer 구성
Azure Load Balancer를 구성 하려면 [Azure 표준 LOAD BALANCER SKU](../../../load-balancer/load-balancer-overview.md) 를 사용 하 고 다음을 수행 하는 것이 좋습니다.

> [!NOTE]
> 표준 Load Balancer SKU는 Load Balancer 아래의 노드에서 공용 IP 주소에 액세스할 때 제한이 있습니다. [SAP 고가용성 시나리오에서 Azure 표준 Load Balancer를 사용 하 Virtual Machines에 대 한 공용 끝점 연결](./high-availability-guide-standard-load-balancer-outbound-connections.md) 문서에서는 이러한 노드가 공용 IP 주소에 액세스할 수 있도록 하는 방법에 대해 설명 합니다.



1. 프런트 엔드 IP 풀을 만듭니다.

   a. Azure Portal에서 Azure Load Balancer를 열고 **프런트 엔드 IP 풀**을 선택한 다음 **추가**를 선택 합니다.

   b. 새 프런트 엔드 IP 풀의 이름을 입력 합니다 (예: **Db2 연결**).

   c. **할당** 을 **정적**으로 설정 하 고, 시작 부분에 정의 된 ip 주소 **가상 ip** 를 입력 합니다.

   d. **확인**을 선택합니다.

   e. 새 프런트 엔드 IP 풀을 만든 후, 풀 IP 주소를 적어 둡니다.

1. 백 엔드 풀을 만듭니다.

   a. Azure Portal에서 Azure Load Balancer를 열고 **백 엔드 풀**을 선택한 다음 **추가**를 선택 합니다.

   b. 새 백 엔드 풀의 이름 (예: **Db2-백**엔드)을 입력 합니다.

   c. **가상 머신 추가**를 선택합니다.

   d. 이전 단계에서 만든 IBM Db2 데이터베이스를 호스트 하는 가용성 집합 또는 가상 머신을 선택 합니다.

   e. IBM Db2 클러스터의 가상 머신을 선택 합니다.

   f. **확인**을 선택합니다.

1. 상태 프로브를 만듭니다.

   a. Azure Portal에서 Azure Load Balancer를 열고 **상태 프로브**를 선택한 다음 **추가**를 선택 합니다.

   b. 새 상태 프로브 (예: **Db2-hp**)의 이름을 입력 합니다.

   c. 프로토콜 및 포트 **62500**로 **TCP** 를 선택 합니다. **간격** 값을 **5**로 유지 하 고 **비정상 임계값** 을 **2**로 설정 된 상태로 유지 합니다.

   d. **확인**을 선택합니다.

1. 부하 분산 규칙을 만듭니다.

   a. Azure Portal에서 Azure Load Balancer를 열고 **부하 분산 규칙**을 선택한 다음 **추가**를 선택 합니다.

   b. 새 Load Balancer 규칙의 이름 (예: **Db2-SID**)을 입력 합니다.

   c. 앞에서 만든 프런트 엔드 IP 주소, 백 엔드 풀 및 상태 프로브 (예: **Db2-프런트 엔드**)를 선택 합니다.

   d. **프로토콜** 을 **TCP**로 설정 된 상태로 유지 하 고 포트 *데이터베이스 통신 포트*를 입력 합니다.

   e. **유휴 상태 시간 제한**을 30분으로 증가시킵니다.

   f. **부동 IP를 사용하도록 설정**했는지 확인합니다.

   g. **확인**을 선택합니다.

**[A]** 프로브 포트에 대 한 방화벽 규칙 추가:
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>연결에 가상 IP를 사용 하도록 SAP 프로필 변경
HADR 구성의 기본 인스턴스에 연결 하려면 SAP 응용 프로그램 계층에서 Azure Load Balancer에 대해 정의 하 고 구성한 가상 IP 주소를 사용 해야 합니다. 다음과 같이 변경 해야 합니다.

/sapmnt/ \<SID> /profile/DEFAULT. DEFAULT.PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/ \<SID> /global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>기본 및 대화 상자 응용 프로그램 서버 설치

Db2 HADR 구성에 대해 기본 및 대화 상자 응용 프로그램 서버를 설치 하는 경우 구성에 대해 선택한 가상 호스트 이름을 사용 합니다. 

Db2 HADR 구성을 만들기 전에 설치를 수행한 경우 이전 섹션에 설명 된 대로 또는 SAP Java 스택에 대해 다음과 같이 변경 합니다.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP + Java 또는 Java stack 시스템 JDBC URL 검사

J2EE 구성 도구를 사용 하 여 JDBC URL을 확인 하거나 업데이트할 수 있습니다. J2EE 구성 도구는 그래픽 도구 이므로 X 서버를 설치 해야 합니다.
 
1. J2EE 인스턴스의 기본 응용 프로그램 서버에 로그인 하 고 다음을 실행 합니다.
    
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>  
    
1. 왼쪽 프레임에서 **보안 저장소**를 선택 합니다.
1. 오른쪽 프레임에서 키를 선택 `jdbc/pool/\<SAPSID>/url` 합니다.
1. JDBC URL의 호스트 이름을 가상 호스트 이름으로 변경 합니다.
    
    <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>  
    
1. **추가**를 선택합니다.
1. 변경 내용을 저장 하려면 왼쪽 위에서 디스크 아이콘을 선택 합니다.
1. 구성 도구를 닫습니다.
1. Java 인스턴스를 다시 시작 합니다.

## <a name="configure-log-archiving-for-hadr-setup"></a>HADR 설치를 위한 로그 보관 구성
HADR 설치를 위해 Db2 로그 보관을 구성 하려면 모든 로그 보관 위치에서 자동 로그 검색 기능을 갖도록 주 데이터베이스와 대기 데이터베이스를 모두 구성 하는 것이 좋습니다. 주 데이터베이스와 대기 데이터베이스는 모두 데이터베이스 인스턴스 중 하나가 로그 파일을 보관할 수 있는 모든 로그 보관 위치에서 로그 보관 파일을 검색할 수 있어야 합니다. 

로그 보관은 주 데이터베이스 에서만 수행 됩니다. 데이터베이스 서버의 HADR 역할을 변경 하거나 오류가 발생 한 경우 새 주 데이터베이스는 로그 보관을 담당 합니다. 여러 로그 보관 위치를 설정한 경우 로그는 두 번 보관 될 수 있습니다. 로컬 또는 원격으로 로그인 하는 경우 이전 주 서버에서 보관 된 로그를 새 주 서버의 활성 로그 위치에 수동으로 복사 해야 할 수도 있습니다.

두 노드에서 로그가 기록 되는 일반적인 NFS 공유 또는 GlusterFS를 구성 하는 것이 좋습니다. NFS 공유 또는 GlusterFS는 항상 사용 가능 해야 합니다. 

전송 또는 프로필 디렉터리에 대해 기존의 항상 사용 가능한 NFS 공유 또는 GlusterFS를 사용할 수 있습니다. 자세한 내용은 다음을 참조하세요.

- [SAP NetWeaver에 대한 Red Hat Enterprise Linux에 있는 Azure VM의 GlusterFS][glusterfs] 
- [SAP 응용 프로그램용 Azure NetApp Files를 사용 하 Red Hat Enterprise Linux의 Azure Vm에서 SAP NetWeaver에 대 한 고가용성][anf-rhel]
- [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) (NFS 공유를 만들려면)

## <a name="test-the-cluster-setup"></a>클러스터 설정 테스트

이 섹션에서는 Db2 HADR 설치 프로그램을 테스트 하는 방법을 설명 합니다. 모든 테스트에서는 *az-idb01* 가상 머신에서 IBM Db2 주 데이터베이스가 실행 되 고 있다고 가정 합니다. Sudo 권한 또는 루트 (권장 하지 않음)를 사용 하는 사용자를 사용 해야 합니다.

모든 테스트 사례의 초기 상태는 (crm_mon-r 또는 pc 상태)에 설명 되어 있습니다.

- **pc 상태** 는 실행 시 Pacemaker 상태에 대 한 스냅숏입니다. 
- **crm_mon-r** 은 Pacemaker 상태의 연속 출력입니다.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

SAP 시스템의 원래 상태는 다음 이미지와 같이 트랜잭션 DBACOCKPIT > 구성 > 개요에 설명 되어 있습니다.

![DBACockpit-마이그레이션 전](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>IBM Db2의 인수 테스트


> [!IMPORTANT] 
> 테스트를 시작 하기 전에 다음을 확인 합니다.
> * Pacemaker에 실패 한 작업 (pc 상태)이 없습니다.
> * 위치 제약 조건이 없습니다 (마이그레이션 테스트의 leftovers).
> * IBM Db2 HADR 동기화가 작동 중입니다. 사용자 db2로 확인\<sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


다음 명령을 실행 하 여 기본 Db2 데이터베이스를 실행 하는 노드를 마이그레이션합니다.
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

마이그레이션이 완료 되 면 crm 상태 출력은 다음과 같습니다.
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

</code></pre>

SAP 시스템의 원래 상태는 다음 이미지와 같이 트랜잭션 DBACOCKPIT > 구성 > 개요에 설명 되어 있습니다.

![DBACockpit-마이그레이션 후](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

"Pc 리소스 이동"을 사용 하는 리소스 마이그레이션은 위치 제약 조건을 만듭니다. 이 경우 위치 제약 조건이 az-idb01에서 IBM Db2 인스턴스를 실행 하는 것을 방지 합니다. 위치 제약 조건이 삭제 되지 않은 경우 리소스를 장애 복구 (failback) 할 수 없습니다.

위치 제한 및 대기 노드는 az-idb01에서 시작 됩니다.
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
및 클러스터 상태가 다음으로 변경:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

![DBACockpit-제거 되는 위치 제약](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


리소스를 *az-idb01* 로 다시 마이그레이션하고 location 제약 조건을 지웁니다.
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **pc 리소스 이동 \<res_name> <host> :** 위치 제약 조건을 만들며 인수와 관련 된 문제를 일으킬 수 있습니다.
- **pc 리소스 지우기 \<res_name> **: 위치 제약 조건 지우기
- **pc 리소스 정리 \<res_name> **: 리소스의 모든 오류를 지웁니다.

### <a name="test-a-manual-takeover"></a>수동 인수 테스트

*Az-idb01* Node에서 Pacemaker 서비스를 중지 하 여 수동 인수을 테스트할 수 있습니다.
<pre><code>systemctl stop pacemaker</code></pre>

*az-ibdb02* 에 대 한 상태
<pre><code>2 nodes configured
5 resources configured

Node az-idb01: pending
Online: [ az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled</code></pre>

장애 조치 (failover) 후에는 *az-idb01*에서 서비스를 다시 시작할 수 있습니다.
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>HADR 주 데이터베이스를 실행 하는 노드에서 Db2 프로세스를 중지 합니다.

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

Db2 인스턴스가 실패 하 고 Pacemaker가 마스터 노드를 이동 하 고 다음 상태로 보고 됩니다.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=49, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 09:57:35 2019', queued=0ms, exec=362ms</code></pre>

Pacemaker는 동일한 노드에서 Db2 주 데이터베이스 인스턴스를 다시 시작 하거나 보조 데이터베이스 인스턴스를 실행 하는 노드로 장애 조치 (failover) 하 고 오류가 보고 됩니다.

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>보조 데이터베이스 인스턴스를 실행 하는 노드에서 Db2 프로세스를 중지 합니다.

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

노드가 실패 한 것으로 보고 되 고 오류가 보고 되었습니다.
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Failed Actions:
* Db2_HADR_ID2_monitor_20000 on az-idb02 'not running' (7): call=144, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 10:02:09 2019', queued=0ms, exec=0ms</code></pre>

Db2 인스턴스는 이전에 할당 한 보조 역할에서 다시 시작 됩니다.

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>HADR 주 데이터베이스 인스턴스를 실행 하는 노드에서 db2stop force를 통해 DB를 중지 합니다.

사용자 db2 \<sid> 실행 명령 db2stop force:
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

검색 된 오류:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Slaves: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Stopped
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Stopped

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</code></pre>

Db2 HADR 보조 데이터베이스 인스턴스가 주 역할로 승격 되었습니다.
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</pre></code>


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>"중지"로 HADR 주 데이터베이스 인스턴스를 실행 하는 VM의 작동이 중단 됩니다.

<pre><code>#Linux kernel panic. 
sudo echo b > /proc/sysrq-trigger</code></pre>

이 경우 Pacemaker는 주 데이터베이스 인스턴스를 실행 하는 노드가 응답 하지 않는다는 것을 감지 합니다.

<pre><code>2 nodes configured
5 resources configured

Node az-idb01: UNCLEAN (online)
Online: [ az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01</code></pre>

다음 단계는 *분할 된 두뇌* 상황을 확인 하는 것입니다. 주 데이터베이스 인스턴스를 마지막으로 실행 한 노드가 중단 된 것을 확인 한 후에는 리소스 장애 조치 (failover)가 실행 됩니다.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb02 ]
OFFLINE: [ az-idb01 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02 </code></pre>


커널에 영향을 주는 경우 실패 한 노드는 펜스 에이전트로 restared 됩니다. 실패 한 노드가 다시 온라인 상태가 되 면 pacemaker 클러스터를 시작 해야 합니다.
<pre><code>sudo pcs cluster start</code></pre> 보조 역할로 Db2 인스턴스를 시작 합니다.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

## <a name="next-steps"></a>다음 단계
- [SAP NetWeaver에 대한 고가용성 아키텍처 및 시나리오](./sap-high-availability-architecture-scenarios.md)
- [Azure의 Red Hat Enterprise Linux에서 Pacemaker 설정][rhel-pcs-azr]

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105
[2694118]:https://launchpad.support.sap.com/#/notes/2694118


[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm

[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[rhel-ha-addon]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index
[rhel-ha-admin]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index
[rhel-ha-ref]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index
[rhel-azr-supp]:https://access.redhat.com/articles/3131341
[rhel-azr-inst]:https://access.redhat.com/articles/3252491
[rhel-db2-supp]:https://access.redhat.com/articles/3144221
[ascs-ha-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel
[glusterfs]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs
[rhel-pcs-azr]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker
[anf-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist
