---
title: 개인 링크-Azure Database for PostgreSQL 단일 서버
description: 개인 링크가 Azure Database for PostgreSQL 단일 서버에서 작동 하는 방식에 대해 알아봅니다.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 773815cd64af2a398001bac91e14cca0b204ca8e
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87832070"
---
# <a name="private-link-for-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL에 대 한 개인 링크-단일 서버

개인 링크를 사용 하면 Azure Database for PostgreSQL 단일 서버에 대 한 개인 끝점을 만들 수 있으므로 VNet (개인 Virtual Network) 내에 Azure 서비스를 사용할 수 있습니다. 개인 끝점은 VNet의 다른 리소스와 마찬가지로 데이터베이스 서버에 연결 하는 데 사용할 수 있는 개인 IP를 노출 합니다.

개인 링크 기능을 지 원하는 PaaS 서비스 목록을 보려면 개인 링크 [설명서](https://docs.microsoft.com/azure/private-link/index)를 검토 하세요. 프라이빗 엔드포인트는 특정 [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 및 서브넷 내의 개인 IP 주소입니다.

> [!NOTE]
> 개인 링크 기능은 범용 또는 메모리 액세스에 최적화 된 가격 책정 계층의 Azure Database for PostgreSQL 서버에만 사용할 수 있습니다. 데이터베이스 서버가 이러한 가격 책정 계층 중 하나에 있는지 확인 합니다.

## <a name="data-exfiltration-prevention"></a>데이터 반출 방지

Azure Database for PostgreSQL 단일 서버에서 데이터를 필터링 하는 것은 데이터베이스 관리자와 같은 권한 있는 사용자가 한 시스템에서 데이터를 추출 하 여 조직 외부의 다른 위치나 시스템으로 이동할 수 있는 경우입니다. 예를 들어 사용자는 데이터를 타사 소유의 스토리지 계정으로 이동합니다.

미국 서 부에 프로 비전 된 Azure Database for PostgreSQL 단일 서버에 연결 하는 Azure VM (가상 머신) 내에서 PGAdmin을 실행 하는 사용자가 있는 시나리오를 고려해 보세요. 아래 예제에서는 네트워크 액세스 제어를 사용 하 여 Azure Database for PostgreSQL 단일 서버에서 공용 끝점에 대 한 액세스를 제한 하는 방법을 보여 줍니다.

* *Azure 서비스 허용* 을 OFF로 설정 하 여 공용 끝점을 통해 Azure Database for PostgreSQL 단일 서버에 대 한 모든 azure 서비스 트래픽을 사용 하지 않도록 설정 합니다. [방화벽 규칙](https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules) 또는 [가상 네트워크 서비스 끝점](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet)을 통해 서버에 액세스할 수 있는 IP 주소 또는 범위가 없어야 합니다.

* VM의 개인 IP 주소를 사용 하 여 Azure Database for PostgreSQL 단일 서버에 대 한 트래픽만 허용 합니다. 자세한 내용은 [서비스 끝점](concepts-data-access-and-security-vnet.md) 및 [VNet 방화벽 규칙](howto-manage-vnet-using-portal.md) 에 대 한 문서를 참조 하세요.

* Azure VM에서 다음과 같이 NSG(네트워크 보안 그룹) 및 서비스 태그를 사용하여 나가는 연결의 범위를 좁힙니다.

    * 서비스 태그 = SQL에 대 한 트래픽을 허용 하도록 NSG 규칙을 지정 *합니다. WestUS* -미국 서 부에서 Azure Database for PostgreSQL 단일 서버에만 연결할 수 있습니다.
    * *서비스 태그 = SQL* -모든 지역에서 PostgreSQL 데이터베이스에 대 한 연결을 거부 하는 nsg 규칙 (우선 순위가 높음)을 지정 합니다.</br></br>

이 설정이 완료 되 면 Azure VM은 미국 서 부 지역의 Azure Database for PostgreSQL 단일 서버에만 연결할 수 있습니다. 그러나 단일 Azure Database for PostgreSQL 단일 서버로의 연결은 제한 되지 않습니다. VM은 구독에 포함 되지 않은 데이터베이스를 포함 하 여 미국 서 부 지역의 모든 Azure Database for PostgreSQL 단일 서버에 계속 연결할 수 있습니다. 위의 시나리오에서 데이터 반출의 범위를 특정 지역으로 좁혔지만 완전히 제거하지는 않았습니다.</br>

이제 개인 링크를 사용 하 여 개인 끝점에 대 한 액세스를 제한 하는 NSGs와 같은 네트워크 액세스 제어를 설정할 수 있습니다. 그러면 개별 Azure PaaS 리소스가 특정 프라이빗 엔드포인트에 매핑됩니다. 악의적인 참가자는 매핑된 PaaS 리소스 (예: 단일 서버 Azure Database for PostgreSQL)에만 액세스할 수 있으며 다른 리소스는 액세스할 수 없습니다.

## <a name="on-premises-connectivity-over-private-peering"></a>프라이빗 피어링을 통한 온-프레미스 연결

온-프레미스 컴퓨터에서 공용 끝점에 연결 하는 경우 서버 수준 방화벽 규칙을 사용 하 여 ip 기반 방화벽에 IP 주소를 추가 해야 합니다. 이 모델은 개발 또는 테스트 워크로드를 위해 개별 머신에 액세스할 수 있도록 하는 데 효과적이지만 프로덕션 환경에서는 관리하기가 어렵습니다.

개인 링크를 사용 하 여 ER ( [Express Route](https://azure.microsoft.com/services/expressroute/) ), 개인 피어 링 또는 [VPN 터널](https://docs.microsoft.com/azure/vpn-gateway/)을 사용 하 여 개인 끝점에 대 한 프레미스 간 액세스를 사용 하도록 설정할 수 있습니다. 이후에는 공용 끝점을 통해 모든 액세스를 사용 하지 않도록 설정 하 고 IP 기반 방화벽을 사용 하지 않을 수 있습니다.

> [!NOTE]
> Azure Database for PostgreSQL 및 VNet 서브넷이 서로 다른 구독에 있는 경우도 있습니다. 이러한 경우에는 다음과 같은 구성을 확인해야 합니다.
> - 두 구독 모두에 **DBforPostgreSQL** 리소스 공급자가 등록 되어 있는지 확인 합니다. 자세한 내용은 [resource-manager-registration][resource-manager-portal]을 참조하세요.

## <a name="configure-private-link-for-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL 단일 서버에 대 한 개인 링크 구성

### <a name="creation-process"></a>만들기 프로세스

개인 끝점은 개인 링크를 사용 하도록 설정 하는 데 필요 합니다. 다음 방법 가이드를 사용 하 여이 작업을 수행할 수 있습니다.

* [Azure Portal](https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal)
* [CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-cli)

### <a name="approval-process"></a>승인 프로세스
네트워크 관리자가 PE (개인 끝점)를 만든 후에는 PostgreSQL 관리자가 PEC (개인 끝점 연결)를 관리 하 여 Azure Database for PostgreSQL 수 있습니다. 네트워크 관리자와 DBA 간의 이러한 업무 분리는 Azure Database for PostgreSQL 연결을 관리 하는 데 유용 합니다. 

* Azure Portal에서 Azure Database for PostgreSQL 서버 리소스로 이동 합니다. 
    * 왼쪽 창에서 개인 끝점 연결을 선택 합니다.
    * 모든 개인 끝점 연결의 목록을 표시 합니다 (PECs).
    * 만든 해당 개인 끝점 (PE)

![개인 끝점 포털을 선택 합니다.](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* 목록에서 개별 PEC를 선택합니다.

![개인 끝점 승인 보류 중을 선택 합니다.](media/concepts-data-access-and-security-private-link/select-private-link.png)

* PostgreSQL 서버 관리자는 PEC를 승인 또는 거부 하 고 필요에 따라 짧은 텍스트 응답을 추가 하도록 선택할 수 있습니다.

![개인 끝점 메시지 선택](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* 승인 또는 거부 후에는 목록에 응답 텍스트와 함께 적절 한 상태가 반영 됩니다.

![개인 끝점 최종 상태를 선택 합니다.](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-postgresql"></a>Azure Database for PostgreSQL에 대 한 개인 링크 사용 사례

클라이언트는 동일한 VNet, 동일한 지역의 피어 링 VNet 또는 지역 간에 VNet 간 연결을 통해 개인 끝점에 연결할 수 있습니다. 또한 클라이언트는 ExpressRoute, 프라이빗 피어링 또는 VPN 터널링을 사용하여 온-프레미스에서 연결할 수 있습니다. 다음은 일반적인 사용 사례를 보여 주는 간소화된 다이어그램입니다.

![개인 끝점 개요를 선택 합니다.](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>피어링된 VNet(가상 네트워크)의 Azure VM에서 연결
[Vnet 피어 링](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) 을 구성 하 여 피어 링 Vnet의 Azure VM에서 Azure Database for PostgreSQL 단일 서버에 대 한 연결을 설정 합니다.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>VNet 간 환경의 Azure VM에서 연결
[Vnet 간 VPN gateway 연결](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) 을 구성 하 여 다른 지역 또는 구독의 Azure VM에서 Azure Database for PostgreSQL 단일 서버에 대 한 연결을 설정 합니다.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>VPN을 통해 온-프레미스 환경에서 연결
온-프레미스 환경에서 Azure Database for PostgreSQL 단일 서버로의 연결을 설정 하려면 다음 옵션 중 하나를 선택 하 여 구현 합니다.

* [지점 및 사이트 간 연결](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [사이트 간 VPN 연결](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [ExpressRoute 회로](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>방화벽 규칙과 결합된 Private Link

Private Link를 방화벽 규칙과 함께 사용하면 다음과 같은 상황 및 결과가 발생할 수 있습니다.

* 방화벽 규칙을 구성 하지 않으면 기본적으로 트래픽이 Azure Database for PostgreSQL 단일 서버에 액세스할 수 없게 됩니다.

* 공용 트래픽 또는 서비스 엔드포인트를 구성하고 프라이빗 엔드포인트를 만들면 해당 유형의 방화벽 규칙이 다양한 유형의 수신 트래픽 유형에 권한을 부여합니다.

* 공용 트래픽 또는 서비스 끝점을 구성 하지 않고 개인 끝점을 만들 경우 Azure Database for PostgreSQL 단일 서버는 전용 끝점을 통해서만 액세스할 수 있습니다. 공용 트래픽 또는 서비스 끝점을 구성 하지 않은 경우 승인 된 모든 개인 끝점을 거부 하거나 삭제 한 후에는 모든 트래픽이 Azure Database for PostgreSQL 단일 서버에 액세스할 수 없게 됩니다.

## <a name="deny-public-access-for-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL 단일 서버에 대 한 공용 액세스 거부

Azure Database for PostgreSQL 단일 서버에 액세스 하기 위한 전용 끝점만 사용 하려면 데이터베이스 서버에서 **공용 네트워크 액세스 거부** 구성을 설정 하 여 모든 공용 끝점 ([방화벽 규칙](concepts-firewall-rules.md) 및 [VNet 서비스 끝점](concepts-data-access-and-security-vnet.md))의 설정을 사용 하지 않도록 설정할 수 있습니다. 

이 설정이 *예* 로 설정 된 경우 개인 끝점을 통한 연결만 Azure Database for PostgreSQL 허용 됩니다. 이 설정을로 설정 하면 클라이언트는 방화벽 또는 VNet 서비스 끝점 설정에 따라 Azure Database for PostgreSQL에 연결할 수 *없습니다* . 또한 개인 네트워크 액세스 값이 설정 되 면 고객은 기존 ' 방화벽 규칙 ' 및 ' VNet 서비스 끝점 규칙 '을 추가 및/또는 업데이트할 수 없습니다.

> [!Note]
> 이 기능은 Azure Database for PostgreSQL 단일 서버가 범용 및 메모리 액세스에 최적화 된 가격 책정 계층을 지 원하는 모든 Azure 지역에서 사용할 수 있습니다.
>
> 이 설정은 Azure Database for PostgreSQL 단일 서버에 대 한 SSL 및 TLS 구성에 영향을 주지 않습니다.

Azure Portal에서 Azure Database for PostgreSQL 단일 서버에 대 한 **공용 네트워크 액세스 거부** 를 설정 하는 방법을 알아보려면 [공용 네트워크 액세스 거부를 구성 하는 방법](howto-deny-public-network-access.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure Database for PostgreSQL 단일 서버 보안 기능에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

* 단일 서버 Azure Database for PostgreSQL에 대 한 방화벽을 구성 하려면 [방화벽 지원](https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules)을 참조 하세요.

* Azure Database for PostgreSQL 단일 서버에 대 한 가상 네트워크 서비스 끝점을 구성 하는 방법을 알아보려면 [가상 네트워크에서 액세스 구성](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet)을 참조 하세요.

* 단일 서버 연결 Azure Database for PostgreSQL에 대 한 개요는 [Azure Database for PostgreSQL 연결 아키텍처](https://docs.microsoft.com/azure/postgresql/concepts-connectivity-architecture) 를 참조 하세요.

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md