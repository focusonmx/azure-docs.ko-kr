---
title: Recovery Services 자격 증명 모음 개요
description: Recovery Services 자격 증명 모음 및 Azure Backup 자격 증명 모음 간의 개요 및 비교입니다.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 0e1d061c6baf31fad2e937a604098f0baff6086d
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88041904"
---
# <a name="recovery-services-vaults-overview"></a>Recovery Services 자격 증명 모음 개요

이 문서에서는 Recovery Services 자격 증명 모음의 기능을 설명합니다. Recovery Services 자격 증명 모음은 Azure에서 데이터를 저장하는 스토리지 엔터티입니다. 데이터는 일반적으로 데이터의 복사본 또는 가상 머신(VM), 워크로드, 서버 또는 워크스테이션에 대한 구성 정보입니다. Recovery Services 자격 증명 모음을 사용하여 IaaS VM(Linux 또는 Windows) 및 Azure SQL 데이터베이스와 같은 다양한 Azure 서비스에 대한 백업 데이터를 저장할 수 있습니다. Recovery Services 자격 증명 모음은 System Center DPM, Windows Server, Azure Backup Server 등을 지원합니다. Recovery Services 자격 증명 모음을 사용하면 관리 오버헤드를 최소화하면서 백업 데이터를 쉽게 구성할 수 있습니다. Recovery Services 자격 증명 모음은 다음과 같은 기능을 제공 하는 Azure의 Azure Resource Manager 모델을 기반으로 합니다.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Recovery Services 자격 증명 모음 및 Backup 자격 증명 모음 비교

- **백업 데이터 보호 기능 향상**: Recovery Services 자격 증명 모음에서 Azure Backup은 클라우드 백업을 보호하는 보안 기능을 제공합니다. 이러한 보안 기능을 통해 백업을 보호하고 프로덕션 및 백업 서버가 손상된 경우에도 데이터를 안전하게 복구할 수 있습니다. [자세히 알아보기](backup-azure-security-feature.md)

- **하이브리드 IT 환경을 위한 중심 모니터링**: Recovery Services 자격 증명 모음에서 [Azure IaaS VM](backup-azure-manage-vms.md)뿐만 아니라 중앙 포털에서 [온-프레미스 자산](backup-azure-manage-windows-server.md#manage-backup-items)도 모니터링할 수 있습니다. [자세히 알아보기](backup-azure-monitoring-built-in-monitor.md)

- **RBAC(역할 기반 Access Control)**: RBAC는 Azure에서 세밀한 액세스 관리 제어를 제공합니다. [Azure는 다양한 기본 제공 역할을 제공](../role-based-access-control/built-in-roles.md)하고 Azure Backup에는 3가지 [복구 지점을 관리하는 기본 제공 역할](backup-rbac-rs-vault.md)이 포함됩니다. Recovery Services 자격 증명 모음은 RBAC와 호환되어 백업을 제한하고 정의된 집합의 사용자 역할에 대한 액세스를 복원합니다. [자세히 알아보기](backup-rbac-rs-vault.md)

- **일시 삭제**: 일시 삭제를 사용 하면 악의적인 행위자가 백업을 삭제 하는 경우 (또는 백업 데이터가 실수로 삭제 된 경우에도), 백업 데이터는 14 일 동안 보존 되므로 데이터 손실 없이 해당 백업 항목을 복구할 수 있습니다. "일시 삭제" 상태의 백업 데이터에 대 한 추가 14 일 보존은 고객에 게 비용을 부과 하지 않습니다. [자세히 알아보기](backup-azure-security-feature-cloud.md).

- **지역 간 복원**: crr (지역 간 복원)을 사용 하면 azure 쌍을 이루는 지역에 있는 보조 지역에서 azure vm을 복원할 수 있습니다. Azure가 주 지역에서 재해를 선언 하는 경우 보조 지역에서 복제 된 데이터를 보조 지역에서 복원 하 여 환경에 대 한 기본 지역의 실제 가동 중지 시간을 완화할 수 있습니다. [자세히 알아보기](backup-azure-arm-restore-vms.md#cross-region-restore).

## <a name="storage-settings-in-the-recovery-services-vault"></a>Recovery Services 자격 증명 모음의 저장소 설정

Recovery Services 자격 증명 모음은 시간에 따라 생성된 모든 백업과 복구 지점을 저장하는 엔터티입니다. Recovery Services 자격 증명 모음에는 보호된 가상 머신과 연결된 백업 정책도 포함됩니다.

- Azure Backup는 자격 증명 모음에 대 한 저장소를 자동으로 처리 합니다. [저장소 설정을 변경](./backup-create-rs-vault.md#set-storage-redundancy)하는 방법을 참조 하세요.

- 저장소 중복성에 대 한 자세한 내용은 [지역](../storage/common/storage-redundancy.md) 및 [로컬](../storage/common/storage-redundancy.md) 중복성에 대 한 문서를 참조 하세요.

### <a name="additional-resources"></a>추가 리소스

- [자격 증명 모음 지원 및 지원 되지 않는 시나리오](backup-support-matrix.md#vault-support)
- [자격 증명 모음에 대 한 질문과 대답](backup-azure-backup-faq.md)

## <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../advisor/index.yml) 는 Azure 사용을 최적화 하는 데 도움이 되는 개인 설정 된 클라우드 컨설턴트입니다. Azure 사용량을 분석 하 고 배포를 최적화 하 고 보호 하는 데 도움이 되는 적시에 권장 사항을 제공 합니다. 고가용성, 보안, 성능 및 비용의 네 가지 범주로 권장 사항을 제공 합니다.

Azure Advisor는 백업 되지 않은 Vm에 대 한 시간별 [권장 사항을](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion) 제공 하므로 중요 한 vm은 백업 하지 않아도 됩니다. Snoozing 하 여 권장 사항을 제어할 수도 있습니다.  자격 증명 모음 (백업이 저장 되는 위치) 및 백업 정책 (백업 일정 및 백업 복사본의 보존)을 지정 하 여 권장 사항을 선택 하 고 Vm에 대 한 백업을 온라인으로 설정할 수 있습니다.

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="next-steps"></a>다음 단계

다음 문서를 사용하여 해당 작업을 수행하세요.</br>
[IaaS VM 백업](backup-azure-arm-vms-prepare.md)</br>
[Azure Backup Server 백업](backup-azure-microsoft-azure-backup.md)</br>
[Windows Server 백업](backup-windows-with-mars-agent.md)
