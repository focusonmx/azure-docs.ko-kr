---
title: 앱을 게시자 확인으로 표시 - Microsoft ID 플랫폼 | Azure
description: 앱을 게시자 확인으로 표시하는 방법에 대해 설명합니다. 애플리케이션이 게시자 확인으로 표시되어 있으면 게시자가 Microsoft 파트너 네트워크 계정을 사용하여 확인 프로세스를 완료했으며 애플리케이션 등록과 연결된 MPN 계정과 연결된 ID를 확인하고 있는 것입니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: d25a3831897ff8ad2e7dfb0c69910fee246aec21
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85477688"
---
# <a name="mark-your-app-as-publisher-verified-preview"></a>앱을 게시자 확인으로 표시(미리 보기)

애플리케이션이 게시자 확인으로 표시되어 있으면 게시자가 Microsoft 파트너 네트워크(MPN) 계정을 사용하여 애플리케이션 등록과 연결된 MPN 계정과 연결된 ID를 확인하고 있는 것입니다. 이 문서에서는 [게시자 확인(미리 보기)](publisher-verification-overview.md) 프로세스를 완료하는 방법을 설명합니다.

## <a name="quickstart"></a>빠른 시작
이미 Microsoft 파트너 네트워크 (MPN)에 등록되어 있고 [필수 조건](publisher-verification-overview.md#requirements)을 충족한 경우 바로 시작할 수 있습니다. 

1. [앱 등록 포털](https://aka.ms/PublisherVerificationPreview) 미리 보기에 있는 Microsoft 앱 등록 포털로 이동합니다.

1. 앱을 선택하고 **브랜딩**을 클릭합니다. 

1. **MPN ID를 추가하여 게시자 확인**을 클릭하고 나열된 요구사항을 검토합니다.

1. MPN ID를 입력하고 **확인 및 저장**을 클릭합니다.

구체적인 혜택, 요구 사항, 질문과 대답에 대한 자세한 내용은 [개요](publisher-verification-overview.md)를 확인하십시오.


## <a name="mark-your-app-as-publisher-verified"></a>앱을 게시자 확인으로 표시
[필수 조건](publisher-verification-overview.md#requirements)을 충족하는지 확인하고 다음 단계에 따라 앱을 게시자 확인으로 표시합니다.  

1. 게시자 확인으로 표시하고자 하는 앱을 변경할 권한이 있는 조직(Azure AD) 및 파트너 센터의 MPN 계정으로 로그인했는지 확인합니다. 

    - Azure AD에서 이 사용자는 앱의 소유자이거나 애플리케이션 관리자, 클라우드 애플리케이션 관리자, 글로벌 관리자 역할 중 하나가 할당되어 있어야 합니다. 

    - 파트너 센터에서 이 사용자는 MPN 관리자, 계정 관리자 또는 글로벌 관리자(Azure AD의 공유 역할)역할 중 하나가 할당되어 있어야 합니다. 

1. 다음과 같이 앱 등록 포털 미리 보기 버전으로 이동합니다.  

1. 게시자 확인으로 표시하고자 하는 앱을 클릭하고 브랜딩 블레이드를 엽니다. 

1. 앱의 게시자 도메인이 적절하게 설정되어 있는지 확인합니다. 이 도메인은 

    - Azure AD 테넌트에 DNS가 확인된 사용자 지정 도메인으로 추가되어 있으며  

    - MPN 계정 확인 프로세스에서 사용된 이메일 주소의 도메인과 일치해야 합니다. 

1. 페이지 아래쪽의 **MPN ID를 추가하여 게시자 확인**을 클릭합니다. 

1. **MPN ID**를 입력합니다. 이 MPN ID는 

    - 확인 프로세스를 완료한 유효한 Microsoft 파트너 네트워크 계정이어야 합니다.  

    - 조직의 PGA(파트너 글로벌 계정)이어야 합니다. 

1. **확인 및 저장**을 클릭합니다. 

1. 프로세스 요청을 기다려 주세요. 이 작업은 몇 분 정도 걸릴 수 있습니다. 

1. 확인에 성공하면 게시자 확인 창이 닫히고 브랜딩 블레이드로 돌아갑니다. 확인된 **게시자 표시 이름** 옆에 파란색 확인 배지가 표시됩니다. 

1. 시스템 전체에 복제되는 데에는 다소 시간이 걸릴 수 있지만 성공적으로 프로세스를 마치면 앱에 동의한 사용자에게도 배지가 표시됩니다. 

1. 이 기능을 확인하려면 애플리케이션에 로그인하고 동의 화면에 확인 배지가 표시되는지 확인합니다. 이미 앱에 동의를 부여한 사용자로 로그인하면 *prompt=consent* 쿼리 매개 변수를 사용하여 동의 확인 프롬프트가 나타나도록 할 수 있습니다. 

1. 배지가 표시되어야 하는 다른 앱에서도 필요에 따라 이 프로세스를 반복합니다. 대량으로 이를 수행하려면 Microsoft Graph를 사용할 수도 있으며 PowerShell cmdlets는 곧 제공될 예정입니다. 자세한 내용은 [Microsoft API Graph 호출](troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls)을 확인하십시오. 

끝났습니다. 일반적인 프로세스, 결과, 기능에 대한 피드백이 있다면 알려주십시오. 

## <a name="next-steps"></a>다음 단계
문제가 발생할 경우 [문제 해결 정보](troubleshoot-publisher-verification.md)를 읽어보십시오.