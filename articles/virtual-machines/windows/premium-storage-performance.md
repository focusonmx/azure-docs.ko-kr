---
title: Azure Premium Storage-고성능을 위한 디자인
description: Azure 프리미엄 SSD 관리 디스크를 사용하여 고성능 애플리케이션을 설계합니다. Premium Storage는 Azure Virtual Machines에서 실행되는 I/O 사용량이 많은 작업에 대해 대기 시간이 짧은 고성능 디스크 지원을 제공합니다.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 46c917a2d27f5efaa1e902db0d5da5375578b8b8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86526041"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Premium Storage: 고성능을 위한 설계
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> 경우에 따라 디스크 성능 문제로 보이는 것은 실제로 네트워크 병목 현상입니다. 이러한 상황에서 [네트워크 성능](../../virtual-network/virtual-network-optimize-network-bandwidth.md)을 최적화해야 합니다.
>
> 디스크를 벤치마킹하려면 [디스크 벤치마킹](disks-benchmarks.md)의 문서를 참조하세요.
>
> VM에서 가속화된 네트워킹을 지원하는 경우 VM이 활성화되어 있는지 확인해야 합니다. 활성화되어 있지 않으면 [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) 및 [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms) 모두에 이미 배포된 VM에서 활성화할 수 있습니다.

시작하기 전에 Premium Storage를 처음 사용하는 경우 먼저 [IaaS VM에 대한 Azure 디스크 유형 선택](disks-types.md) 및 [프리미엄 페이지 Blob 스토리지 계정의 확장성 목표](../../storage/blobs/scalability-targets-premium-page-blobs.md)를 읽어보세요.

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

디스크를 벤치마킹하려면 [디스크 벤치마킹](disks-benchmarks.md)의 문서를 참조하세요.

사용 가능한 디스크 유형에 대해 자세히 알아봅니다. [디스크 유형 선택](disks-types.md)  

SQL Server 사용자의 경우 SQL Server에 대한 성능 모범 사례의 문서를 읽으세요.

* [Azure Virtual Machines의 SQL Server에 대한 성능 모범 사례](../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md)
* [Azure Premium Storage는 Azure VM의 SQL Server에 대해 가장 높은 성능을 제공합니다](https://cloudblogs.microsoft.com/sqlserver/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm/)
