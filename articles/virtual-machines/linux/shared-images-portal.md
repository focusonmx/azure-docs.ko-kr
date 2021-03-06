---
title: 포털을 사용 하 여 공유 Azure Linux VM 이미지 만들기
description: Azure Portal를 사용 하 여 Linux 가상 머신 이미지를 만들고 공유 하는 방법을 알아봅니다.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: d49bbebe4d38783342170693bd06f0dfc645f34d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288400"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>포털을 사용 하 여 Azure 공유 이미지 갤러리 만들기

[공유 이미지 갤러리](shared-image-galleries.md)는 조직 내 사용자 지정 이미지 공유를 간소화합니다. 사용자 지정 이미지는 Marketplace 이미지와 같지만 직접 만듭니다. 애플리케이션 사전 로드, 애플리케이션 구성 및 기타 OS 구성과 같은 부트스트랩 배포 작업에 사용자 지정 이미지를 사용할 수 있습니다. 

공유 이미지 갤러리를 사용하면 조직, 지역, AAD 테넌트의 다른 사용자와 사용자 지정 VM 이미지를 공유할 수 있습니다. 공유할 이미지, 이미지를 제공할 지역, 이미지를 공유할 사람을 선택하세요. 여러 갤러리를 만들어서 공유 이미지 논리적으로 그룹화할 수 있습니다. 

갤러리는 완전한 RBAC(역할 기반 액세스 제어)를 제공하는 최상위 리소스입니다. 이미지 버전을 관리할 수 있으며, 각 이미지 버전을 여러 Azure 지역에 복제하도록 선택할 수 있습니다. 갤러리는 관리되는 이미지에서만 작동합니다.

공유 이미지 갤러리 기능에는 여러 가지 리소스가 있습니다. 이 문서에서는 이러한 리소스를 사용하거나 작성하게 됩니다.


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

<br>





## <a name="before-you-begin"></a>시작하기 전에

이 문서의 예제를 완료 하려면 일반화 된 VM의 관리 되는 기존 이미지 또는 특수 한 VM의 스냅숏을 만들어야 합니다. [자습서: Azure PowerShell을 사용 하 여 AZURE VM의 사용자 지정 이미지 만들기](tutorial-custom-images.md) 또는 특수 한 VM에 대 한 [스냅숏 만들기](../windows/snapshot-copy-managed-disk.md) 를 수행할 수 있습니다. 관리 되는 이미지와 스냅숏의 경우 데이터 디스크 크기는 1TB 이상일 수 없습니다.

이 문서를 진행할 때 필요한 경우 리소스 그룹 및 VM 이름을 바꿉니다.

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms"></a>VM 만들기 

이제 하나 이상의 새 Vm을 만들 수 있습니다. 이 예제에서는 *미국 동부* 데이터 센터에서 *myResourceGroup*에 *myVMfromImage*라는 VM을 만듭니다.

1. 이미지 정의로 이동 합니다. 리소스 필터를 사용 하 여 사용 가능한 모든 이미지 정의를 표시할 수 있습니다.
1. 이미지 정의 페이지의 페이지 맨 위에 있는 메뉴에서 **VM 만들기** 를 선택 합니다.
1. **리소스 그룹**에 대해 **새로 만들기** 를 선택 하 고 이름에 *myresourcegroup* 을 입력 합니다.
1. **가상 컴퓨터 이름**에 *myvm*을 입력 합니다.
1. **지역**에 대해 *미국 동부*를 선택합니다.
1. **가용성 옵션**의 경우에는 기본 *인프라 중복성 필요 없음*을 그대로 둡니다.
1. 이미지 **에 대 한 값** 은 이미지 `latest` 정의에 대해 페이지에서 시작한 경우 이미지 버전으로 자동으로 채워집니다.
1. **크기**의 경우 사용 가능한 크기 목록에서 VM 크기를 선택한 다음 **선택**을 선택 합니다.
1. **관리자 계정**에서 원본 VM이 일반화 된 경우 **사용자 이름** 및 **SSH 공개 키**를 입력 합니다. 원본 VM이 특수화 된 경우 원본 VM의 정보가 사용 되므로 이러한 옵션은 회색으로 표시 됩니다.
1. VM에 대 한 원격 액세스를 허용 하려면 **공용 인바운드 포트**에서 **선택한 포트 허용** 을 선택한 다음 드롭다운에서 **SSH (22)** 를 선택 합니다. VM에 대 한 원격 액세스를 허용 하지 않으려면 **공용 인바운드 포트**에 대해 선택 **안 함** 을 선택 하지 않습니다.
1. 작업이 완료 되 면 페이지 맨 아래에 있는 **검토 + 만들기** 단추를 선택 합니다.
1. VM에서 유효성 검사를 통과 한 후 페이지 맨 아래에서 **만들기** 를 선택 하 여 배포를 시작 합니다.


## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹, 가상 머신 및 모든 관련 리소스가 더 이상 필요 없는 경우 삭제해도 됩니다. 삭제하려면 가상 머신의 리소스 그룹을 선택하고 **삭제**를 선택한 다음, 삭제할 리소스 그룹의 이름을 확인합니다.

개별 리소스를 삭제 하려면 해당 리소스를 역순으로 삭제 해야 합니다. 예를 들어 이미지 정의를 삭제 하려면 해당 이미지에서 만든 모든 이미지 버전을 삭제 해야 합니다.

## <a name="next-steps"></a>다음 단계

또한 템플릿을 사용하여 공유 이미지 갤러리 리소스를 만들 수도 있습니다. 다음의 몇 가지 Azure 빠른 시작 템플릿을 사용할 수 있습니다. 

- [공유 이미지 갤러리 만들기](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [공유 이미지 갤러리에서 이미지 정의 만들기](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [공유 이미지 갤러리에서 이미지 버전 만들기](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [이미지 버전에서 VM 만들기](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

공유 이미지 갤러리에 대한 자세한 내용은 [개요](shared-image-galleries.md)를 참조하세요. 문제 발생 시에는 [공유 이미지 갤러리 문제 해결](troubleshooting-shared-images.md)을 참조하세요.

