---
title: Runbook에서 Azure Automation 변경 내용 추적 및 인벤토리 사용
description: 이 문서에서는 Runbook에서 변경 내용 추적 및 인벤토리를 사용하도록 설정하는 방법을 설명합니다.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 92149289c6c422179ddb3562274020acf779f10b
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186285"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>Runbook에서 변경 내용 추적 및 인벤토리 사용

이 문서에서는 Runbook을 사용하여 사용자 환경의 VM에 [변경 내용 추적 및 인벤토리](change-tracking.md) 기능을 사용하도록 설정하는 방법을 설명합니다. 대규모로 Azure VM을 사용하도록 설정하려면 변경 내용 추적 및 인벤토리를 사용하여 기존 VM을 사용하도록 설정해야 합니다. 

> [!NOTE]
> 변경 내용 추적 및 인벤토리를 사용하도록 설정할 때 특정 Azure 지역에서만 Log Analytics 작업 영역 및 Automation 계정을 연결할 수 있습니다. 지원되는 매핑 쌍 목록은 [Automation 계정의 지역 매핑 및 Log Analytics 작업 영역](how-to/region-mappings.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* 컴퓨터를 관리하기 위한 [Automation 계정](./index.yml)
* [가상 머신](../virtual-machines/windows/quick-create-portal.md).

## <a name="enable-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리 사용 

1. Azure Portal에서 **Automation 계정**을 선택한 다음, 목록에서 자동화 계정을 선택합니다.
1. **Configuration Management**에서 **인벤토리**를 선택합니다.
1. 기존 Log Analytics 작업 영역을 선택하거나 새로운 작업 영역을 만듭니다. 
1. **사용**을 클릭합니다.

    ![변경 내용 추적 및 인벤토리 사용](media/automation-enable-changes-from-runbook/inventory-onboard.png)

## <a name="select-azure-vm-to-manage"></a>관리할 Azure VM 선택

변경 내용 추적 및 인벤토리를 사용하도록 설정하면 기능별로 관리할 Azure VM을 추가할 수 있습니다.

1. Automation 계정의 **Configuration Management**에서 **변경 내용 추적** 또는 **인벤토리**를 선택합니다.

2. **Azure VM 추가**를 클릭하여 VM을 추가합니다.

3. 목록에서 VM을 선택하고 **사용**을 클릭합니다. 이 작업을 수행하면 VM에 대한 변경 내용 추적 및 인벤토리를 사용할 수 있습니다.

   ![VM에 대한 변경 내용 추적 및 인벤토리 사용](media/automation-enable-changes-from-runbook/enable-change-tracking.png)

    > [!NOTE]
    > 변경 내용 추적 및 인벤토리 설정이 완료되기 전에 다른 기능을 사용하도록 설정하려고 하면 다음 메시지가 표시됩니다. `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>모듈 설치 및 업데이트

VM에 변경 내용 추적 및 인벤토리를 사용하도록 설정하려면 최신 Azure 모듈로 업데이트하고 [Az.OperationalInsights](/powershell/module/az.operationalinsights/?view=azps-3.7.0) 모듈을 가져와야 합니다.

1. Automation 계정의 **공유 리소스**에서 **모듈**을 선택합니다. 
2. **Azure 모듈 업데이트**를 선택하여 Azure 모듈을 최신 버전으로 업데이트합니다. 
3. **예**를 클릭하여 기존의 모든 Azure 모듈을 최신 버전으로 업데이트합니다.

    ![모듈 업데이트](media/automation-enable-changes-from-runbook/update-modules.png)

4. **공유 리소스**에서 **모듈**로 돌아갑니다. 
5. **갤러리 찾아보기**를 선택하여 모듈 갤러리를 엽니다. 
6. Az.OperationalInsights를 검색하고 Automation 계정으로 이 모듈을 가져옵니다.

    ![OperationalInsights 모듈 가져오기](media/automation-enable-changes-from-runbook/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리를 사용하도록 설정하기 위해 Runbook 가져오기

1. Automation 계정의 **프로세스 자동화**에서 **Runbook**을 선택합니다.
2. **갤러리 찾아보기**를 선택합니다.
3. `update and change tracking`를 검색합니다.
4. Runbook을 선택하고 원본 보기 페이지에서 **가져오기**를 클릭합니다. 
5. **확인**을 클릭하여 Runbook을 Automation 계정으로 가져옵니다.

   ![설정할 Runbook 가져오기](media/automation-enable-changes-from-runbook/import-from-gallery.png)

6. Runbook 페이지에서 **편집**을 클릭한 다음, **게시**를 선택합니다. 
7. Runbook 게시 창에서 **예**를 클릭하여 Runbook을 게시합니다.

## <a name="start-the-runbook"></a>Runbook을 시작합니다.

이 Runbook을 시작하려면 Azure VM에 대한 변경 내용 추적 및 인벤토리가 사용하도록 설정되어 있어야 합니다. 기존 VM 및 리소스 그룹의 매개 변수에 이 기능이 사용하도록 설정되어 있어야 합니다.

1. **Enable-MultipleSolution** Runbook을 엽니다.

   ![다중 솔루션 Runbook](media/automation-enable-changes-from-runbook/runbook-overview.png)

1. 시작 단추를 클릭하고 다음 필드에 매개 변수 값을 입력합니다.

   * **VMNAME** - 변경 내용 추적 및 인벤토리에 추가할 기존 VM의 이름입니다. 리소스 그룹의 모든 VM을 추가하려면 이 필드를 비워 두세요.
   * **VMRESOURCEGROUP** - 사용하도록 설정할 VM의 리소스 그룹 이름입니다.
   * **SUBSCRIPTIONID** - 사용하도록 설정할 새 VM의 구독 ID입니다. 작업 영역의 구독을 사용하려면 이 필드를 비워 두세요. 다른 구독 ID를 사용하는 경우 Automation 계정의 실행 계정을 구독의 기여자로 추가합니다.
   * **ALREADYONBOARDEDVM** - 이미 수동으로 업데이트를 사용하도록 설정한 VM의 이름입니다.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - VM이 속한 리소스 그룹의 이름입니다.
   * **SOLUTIONTYPE** - **ChangeTracking**을 입력합니다.

   ![Enable-MultipleSolution Runbook 매개 변수](media/automation-enable-changes-from-runbook/runbook-parameters.png)

1. **확인**을 선택하여 Runbook 작업을 시작합니다.
1. runbook 작업 페이지에서 진행률 및 오류를 모니터링합니다.

## <a name="next-steps"></a>다음 단계

* Runbook을 예약하려면 [Azure Automation에서 일정 관리](shared-resources/schedules.md)를 참조하세요.
* 이 기능을 사용하는 방법에 대한 자세한 내용은 [변경 내용 추적 및 인벤토리 관리](change-tracking-file-contents.md)를 참조하세요.
* 이 기능의 일반적인 문제를 해결하려면 [변경 내용 추적 및 인벤토리 문제 해결](troubleshoot/change-tracking.md)을 참조하세요.
