---
title: Azure 사용자 지정 경로를 사용하여 강제 터널링으로 KMS 정품 인증 | Microsoft Docs
description: Azure에서 Azure 사용자 지정 경로를 사용하여 강제 터널링으로 KMS 정품 인증을 수행하는 방법을 보여줍니다.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 12/20/2018
ms.author: genli
ms.openlocfilehash: 1c2050969e95b521554bba100b688add3a987a80
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86526744"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>강제 터널링 시나리오에서 Windows 정품 인증 실패

이 문서에서는 사이트 간 VPN 연결 또는 ExpressRoute 시나리오에서 강제 터널링을 사용하도록 설정했을 때 발생할 수 있는 KMS 정품 인증 문제를 해결하는 방법을 설명합니다.

## <a name="symptom"></a>증상

Azure Virtual Network 서브넷에서 [강제 터널링](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)을 사용하도록 설정하여 모든 인터넷 바인딩 트래픽을 온-프레미스 네트워크로 다시 전송합니다. 이 시나리오에서 Windows를 실행 하는 Azure virtual machines (Vm)는 Windows를 정품 인증 하지 못합니다.

## <a name="cause"></a>원인

Azure Windows VM은 Windows 정품 인증을 위해 Azure KMS 서버에 연결해야 합니다. 이러한 정품 인증을 위해서는 Azure 공용 IP 주소에서 정품 인증 요청을 수행해야 합니다. 강제 터널링 시나리오에서는 정품 인증 요청이 Azure 공용 IP 주소가 아닌 온-프레미스 네트워크에서 수행되므로 정품 인증에 실패합니다.

## <a name="solution"></a>솔루션

이 문제를 해결하려면 Azure 사용자 지정 경로를 사용하여 정품 인증 트래픽을 Azure KMS 서버로 보냅니다.

Azure 글로벌 클라우드용 KMS 서버의 IP 주소는 23.102.135.246입니다. 해당 DNS 이름은 kms.core.windows.net입니다. Azure Germany 등의 다른 Azure 플랫폼을 사용하는 경우 해당 KMS 서버의 IP 주소를 사용해야 합니다. 자세한 내용은 다음 표를 참조하세요.

|플랫폼| KMS DNS|KMS IP|
|------|-------|-------|
|Azure 글로벌|kms.core.windows.net|23.102.135.246|
|Azure 독일|kms.core.cloudapi.de|51.4.143.248|
|Azure 미국 정부|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure 중국 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


사용자 지정 경로를 추가하려면 다음 단계를 수행합니다.

### <a name="for-resource-manager-vms"></a>Resource Manager VM

 

> [!NOTE] 
> 활성화는 공용 IP 주소를 사용 하며 표준 SKU Load Balancer 구성의 영향을 받습니다. [Azure에서 아웃 바운드 연결](../../load-balancer/load-balancer-outbound-connections.md) 을 신중 하 게 검토 하 여 요구 사항에 대해 알아보세요.

1. Azure PowerShell을 연 다음, [Azure 구독에 로그인](/powershell/azure/authenticate-azureps)합니다.
2. 다음 명령을 실행합니다.

    ```powershell
    # First, get the virtual network that hosts the VMs that have activation problems. In this case, we get virtual network ArmVNet-DM in Resource Group ArmVNet-DM:

    $vnet = Get-AzVirtualNetwork -ResourceGroupName "ArmVNet-DM" -Name "ArmVNet-DM"

    # Next, create a route table and specify that traffic bound to the KMS IP (23.102.135.246) will go directly out:

    $RouteTable = New-AzRouteTable -Name "ArmVNet-DM-KmsDirectRoute" -ResourceGroupName "ArmVNet-DM" -Location "centralus"

    Add-AzRouteConfig -Name "DirectRouteToKMS" -AddressPrefix 23.102.135.246/32 -NextHopType Internet -RouteTable $RouteTable

    Set-AzRouteTable -RouteTable $RouteTable

    # Next, attach the route table to the subnet that hosts the VMs

    Set-AzVirtualNetworkSubnetConfig -Name "Subnet01" -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/24" -RouteTable $RouteTable

    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```
3. 정품 인증 문제가 있는 VM으로 이동합니다. [PsPing](/sysinternals/downloads/psping)을 사용하여 KMS 서버에 연결할 수 있는지 테스트합니다.

    ```console
    psping kms.core.windows.net:1688
    ```

4. Windows 정품 인증을 시도하여 문제가 해결되었는지 확인합니다.

### <a name="for-classic-vms"></a>클래식 VM

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

1. Azure PowerShell을 연 다음, [Azure 구독에 로그인](/powershell/azure/authenticate-azureps)합니다.
2. 다음 명령을 실행합니다.

    ```powershell
    # First, create a new route table:
    New-AzureRouteTable -Name "VNet-DM-KmsRouteGroup" -Label "Route table for KMS" -Location "Central US"

    # Next, get the route table that was created:
    $rt = Get-AzureRouteTable -Name "VNet-DM-KmsRouteTable"

    # Next, create a route:
    Set-AzureRoute -RouteTable $rt -RouteName "AzureKMS" -AddressPrefix "23.102.135.246/32" -NextHopType Internet

    # Apply the KMS route table to the subnet that hosts the problem VMs (in this case, we apply it to the subnet that's named Subnet-1):
    Set-AzureSubnetRouteTable -VirtualNetworkName "VNet-DM" -SubnetName "Subnet-1" 
    -RouteTableName "VNet-DM-KmsRouteTable"
    ```

3. 정품 인증 문제가 있는 VM으로 이동합니다. [PsPing](/sysinternals/downloads/psping)을 사용하여 KMS 서버에 연결할 수 있는지 테스트합니다.

    ```console
    psping kms.core.windows.net:1688
    ```

4. Windows 정품 인증을 시도하여 문제가 해결되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

- [KMS 클라이언트 설정 키](/windows-server/get-started/kmsclientkeys)
- [정품 인증 방법 검토 및 선택](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj134256(v=ws.11))
