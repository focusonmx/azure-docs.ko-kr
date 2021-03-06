---
title: 파일 포함
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5149973fe63f867b49e55c970779c005e12536b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "68780118"
---
1. 가상 네트워크 게이트웨이에 대 한 페이지를 엽니다. 여러 가지 방법으로 이동할 수 있습니다. **VNet의 이름-> 개요-> 연결 된 장치-> 게이트웨이의**이름으로 이동 하 여 게이트웨이로 이동할 수 있습니다.
2. 게이트웨이 페이지에서 **연결**을 클릭 합니다. 연결 페이지의 맨 위에 있는 **+추가**를 클릭하여 **연결 추가** 페이지를 엽니다.

   ![사이트 간 연결 만들기](./media/vpn-gateway-add-site-to-site-connection-portal-include/configure-site-to-site-connection.png)
3. **연결 추가** 페이지에서 연결에 대한 값을 구성합니다.

   - **이름:** 연결의 이름을 지정합니다.
   - **연결 형식:****사이트 간(IPSec)** 을 선택합니다.
   - **가상 네트워크 게이트웨이:** 이 게이트웨이에서 연결하기 때문에 값이 고정됩니다.
   - **로컬 네트워크 게이트웨이:****로컬 네트워크 게이트웨이 선택**을 클릭하고 사용하려는 로컬 네트워크 게이트웨이를 선택합니다.
   - **공유 키:** 여기 값은 온-프레미스 로컬 VPN 디바이스에 사용하는 값과 일치해야 합니다. 예제에서는 'abc123'을 사용하지만 좀 더 복잡한 항목을 사용할 수 있습니다. 중요한 점은 여기에서 지정한 값이 VPN 디바이스를 구성할 때 지정한 값과 동일해야 한다는 것입니다.
   - **구독**, **리소스 그룹** 및 **위치**에 대한 나머지 값이 고정됩니다.

4. **확인** 을 클릭하여 연결을 만듭니다. 화면에서 *연결 만들기* 플래시가 표시됩니다.
5. 가상 네트워크 게이트웨이의 **연결** 페이지에서 연결을 볼 수 있습니다. 상태는 *알 수 없음*에서 *연결 중*으로 변한 다음 *성공함*으로 변니다.
