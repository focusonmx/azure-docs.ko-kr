---
title: 'Azure AD Connect: 자동 업그레이드 | Microsoft Docs'
description: 이 항목에서는 Azure AD Connect 동기화의 기본 제공 자동 업그레이드 기능을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcc6de1ce50e86f177023a0a66c436633c8d502c
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88053289"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: 자동 업그레이드
이 기능은 빌드 [1.1.105.0(2016년 2월에 발표됨)](reference-connect-version-history.md)에서 도입되었습니다.  이 기능은 [빌드 1.1.561](reference-connect-version-history.md)에서 업데이트되었고 이제 이전에 지원되지 않던 추가 시나리오를 지원합니다.

## <a name="overview"></a>개요
**자동 업그레이드** 기능을 통해 아주 쉽게 Azure AD Connect 설치를 항상 최신 상태로 유지할 수 있습니다. 이 기능은 Express 설치 및 DirSync 업그레이드에 대해 기본적으로 사용되도록 설정되어 있습니다. 새 버전이 출시되면 설치가 자동으로 업그레이드됩니다.
다음과 같은 경우에 기본적으로 자동 업그레이드가 사용됩니다.

* Express 설정 설치 및 DirSync 업그레이드
* SQL Express LocalDB(Express 설정에서 항상 사용)를 사용하는 경우 SQL Express를 사용한 DirSync에도 LocalDB가 사용됩니다.
* AD 계정이 Express 설정 및 DirSync에 의해 만들어지는 기본 MSOL_ 계정인 경우
* 메타버스에 10만 개 미만의 개체가 있는 경우

자동 업그레이드의 현재 상태는 PowerShell cmdlet `Get-ADSyncAutoUpgrade`로 확인할 수 있습니다. 아래와 같은 상태가 표시됩니다.

| 시스템 상태 | 주석 |
| --- | --- |
| 사용 |자동 업그레이드를 사용할 수 있습니다. |
| 일시 중단 |시스템에서만 설정합니다. 시스템이 **현재** 자동 업그레이드를 받을 수 없습니다. |
| 사용 안 함 |자동 업그레이드를 사용할 수 없습니다. |

`Set-ADSyncAutoUpgrade`(으)로 **사용**과 **사용 안 함** 사이를 전환할 수 있습니다. 시스템에서만 **일시 중단**상태를 설정합니다.  1\.1.750.0 이전 버전에서는 자동 업그레이드 상태가 일시 중단됨으로 설정되어 있으면 Set-ADSyncAutoUpgrade cmdlet이 자동 업그레이드를 차단합니다. 이제 이 기능이 자동 업그레이드를 차단하지 않도록 변경되었습니다.

자동 업그레이드는 Azure AD Connect Health를 업그레이드 인프라로 사용합니다. 자동 업그레이드가 작동하도록 **Office 365 URL 및 IP 주소 범위** 에서 설명하는 대로 [Azure AD Connect Health](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)용 프록시 서버에서 URL을 열었는지 확인합니다.


**Synchronization Service Manager** UI가 서버에서 실행되고 있는 경우에는 UI를 닫을 때까지 업그레이드가 일시 중단됩니다.

## <a name="troubleshooting"></a>문제 해결
Connect 설치 자체가 예상대로 업그레이드되지 않는 경우 다음 단계에 따라 문제점을 확인합니다.

첫째, 새 버전이 출시되는 첫 날에는 자동 업그레이드 시도를 기대할 수 없습니다. 업그레이드를 시도하기 전에 의도적인 임의성이 있으므로 설치가 즉시 업그레이드되지 않더라도 경고가 표시되지 않습니다.

뭔가 잘못되었다고 생각되면 우선 `Get-ADSyncAutoUpgrade` 를 실행하여 자동 업그레이드가 사용하도록 설정되어 있는지 확인합니다.

상태가 일시 중지 됨 이면를 사용 하 여 이유를 확인할 수 있습니다 `Get-ADSyncAutoUpgrade -Detail` .  일시 중단 이유는 임의의 문자열 값을 포함할 수 있지만 일반적으로 UpgradeResult의 문자열 값 (또는)을 포함 `UpgradeNotSupportedNonLocalDbInstall` 합니다 `UpgradeAbortedAdSyncExeInUse` .  복합 값 (예:)도 반환 될 수 있습니다 `UpgradeFailedRollbackSuccess-GetPasswordHashSyncStateFailed` .

UpgradeResult가 아닌 결과 (예: ' AADHealthEndpointNotDefined ' 또는 ' DirSyncInPlaceUpgradeNonLocalDb ')를 가져올 수도 있습니다.

그런 다음 프록시 또는 방화벽에서 필요한 URL을 열었는지 확인합니다. 자동 업데이트는 [개요](#overview)에서 설명된 대로 Azure AD Connect Health를 사용합니다. 프록시를 사용하는 경우 [프록시 서버](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)를 사용하기 위해 상태가 구성되었는지 확인합니다. 또한 Azure AD에 대한 [상태 연결](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) 을 테스트합니다.

Azure AD에 연결이 확인되면, 이벤트 로그를 살펴볼 차례입니다. 이벤트 뷰어를 시작하고 **애플리케이션** 이벤트 로그를 확인합니다. **Azure AD Connect 업그레이드** 원본 및 이벤트 ID 범위 **300-399**에 대한 이벤트 로그 필터를 추가합니다.  
![자동 업그레이드에 대한 이벤트 로그 필터](./media/how-to-connect-install-automatic-upgrade/eventlogfilter.png)  

이제 자동 업그레이드 상태와 관련된 이벤트 로그를 볼 수 있습니다.  
![자동 업그레이드에 대한 이벤트 로그 필터](./media/how-to-connect-install-automatic-upgrade/eventlogresult.png)  

결과 코드의 상태 개요 앞에는 접두사가 붙습니다.

| 결과 코드 접두사 | Description |
| --- | --- |
| Success |설치가 성공적으로 업그레이드되었습니다. |
| UpgradeAborted |일시적인 현상으로 업그레이드가 중지되었습니다. 다시 시도하면 나중에 업그레이드됩니다. |
| UpgradeNotSupported |시스템이 자동으로 업그레이드되지 않도록 차단하는 구성이 시스템에 있습니다. 상태가 변경되었는지 다시 시도하겠지만 시스템을 수동으로 업그레이드해야 할 것으로 보입니다. |

가장 일반적인 메시지 목록은 다음과 같습니다. 전부가 나열된 것은 아니지만 결과 메시지를 통해 문제점이 무엇인지 파악할 수 있습니다.

| 결과 메시지 | Description |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |레지스트리에 쓸 수 없습니다. |
| UpgradeAbortedInsufficientDatabasePermissions |기본 제공 관리자 그룹에 데이터베이스에 대한 권한이 없습니다. 이 문제를 해결하려면 수동으로 Azure AD Connect의 최신 버전으로 업그레이드합니다. |
| UpgradeAbortedInsufficientDiskSpace |업그레이드를 지원하기 위한 디스크 공간이 충분하지 않습니다. |
| UpgradeAbortedSecurityGroupsNotPresent |동기화 엔진에서 사용되는 모든 보안 그룹을 찾아 확인할 수는 없습니다. |
| UpgradeAbortedServiceCanNotBeStarted |NT 서비스 **Microsoft Azure AD Sync** 를 시작하지 못했습니다. |
| UpgradeAbortedServiceCanNotBeStopped |NT 서비스 **Microsoft Azure AD Sync** 를 중지하지 못했습니다. |
| UpgradeAbortedServiceIsNotRunning |NT 서비스 **Microsoft Azure AD Sync** 가 실행되고 있지 않습니다. |
| UpgradeAbortedSyncCycleDisabled |[스케줄러](how-to-connect-sync-feature-scheduler.md) 에 SyncCycle 옵션이 사용하지 않도록 설정되어 있습니다. |
| UpgradeAbortedSyncExeInUse |[Synchronization Service Manager UI](how-to-connect-sync-service-manager-ui.md) 가 서버에 열려 있습니다. |
| UpgradeAbortedSyncOrConfigurationInProgress |설치 마법사가 실행 중이거나 동기화가 스케줄러 외부에서 예약되었습니다. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedCustomizedSyncRules |사용자 지정 규칙을 구성에 추가했습니다. |
| UpgradeNotSupportedInvalidPersistedState |설치가 Express 설정 또는 DirSync 업그레이드가 아닙니다. |
| UpgradeNotSupportedNonLocalDbInstall |SQL Server Express LocalDB 데이터베이스를 사용하고 있지 않습니다. |
|UpgradeNotSupportedLocalDbSizeExceeded|로컬 DB 크기가 8gb 보다 크거나 같습니다.|
|UpgradeNotSupportedAADHealthUploadDisabled|포털에서 상태 데이터 업로드가 사용 하지 않도록 설정 되었습니다.|

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
