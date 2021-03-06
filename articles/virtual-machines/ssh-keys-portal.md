---
title: Azure Portal에서 SSH 키 만들기
description: Linux Vm을 연결 하기 위해 Azure Portal에 SSH 키를 생성 하 고 저장 하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/31/2020
ms.author: cynthn
ms.openlocfilehash: 17ab7ee75e335d686bf308c4b15a53dc4e2e6b0e
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88041751"
---
# <a name="generate-and-store-ssh-keys-in-the-azure-portal"></a>SSH 키를 생성 하 고 Azure Portal에 저장 합니다.

자주 포털을 사용 하 여 Linux Vm을 배포 하는 경우 포털에서 직접 만들거나 컴퓨터에서 업로드 하 여 SSH 키를 보다 간단 하 게 사용할 수 있습니다.

먼저 VM을 만들 때 SSH 키를 만들고 다른 vm에 다시 사용할 수 있습니다. 또는 조직 요구에 맞게 Azure에 저장 된 키 집합을 갖도록 SSH 키를 별도로 만들 수 있습니다. 

기존 키가 있는 경우 포털에서이 키를 사용 하면 해당 키를 업로드 하 고 Azure에 저장 하 여 다시 사용할 수 있습니다.

Linux Vm에서 SSH 키를 만들고 사용 하는 방법에 대 한 자세한 내용은 [ssh 키를 사용 하 여 Linux vm에 연결](./linux/ssh-from-windows.md)을 참조 하세요.

## <a name="generate-new-keys"></a>새 키 생성

1. [Azure Portal](https://portal.azure.com)을 엽니다.

1. 페이지 맨 위에 있는 *SSH* 를 입력 하 여 검색 합니다. **Marketplace*에서 **SSH 키**를 선택 합니다.

1. **SSH 키** 페이지에서 **만들기**를 선택 합니다.

   :::image type="content" source="./media/ssh-keys/portal-sshkey.png" alt-text="새 리소스 그룹을 만들고 SSH 키 쌍을 생성 합니다.":::

1. **리소스 그룹** 에서 **새로 만들기** 를 선택 하 여 키를 저장할 새 리소스 그룹을 만듭니다. 리소스 그룹의 이름을 입력 하 고 **확인**을 선택 합니다.

1. **지역** 에서 키를 저장할 지역을 선택 합니다. 모든 지역에서 키를 사용할 수 있으며,이는 저장 되는 지역에 불과합니다.

1. 키 **쌍 이름**에 키의 이름을 입력 합니다.

1. **SSH 공개 키 원본**에서 **공개 키 원본 생성**을 선택 합니다. 

1. 완료되면 **검토 + 만들기**를 선택합니다.

1. 유효성 검사를 통과하면 **만들기**를 선택합니다.

1. 그런 다음 팝업 창이 표시 되 면 **개인 키 다운로드 및 리소스 만들기**를 선택 합니다. 그러면 SSH 키가 pem 파일로 다운로드 됩니다.

   :::image type="content" source="./media/ssh-keys/download-key.png" alt-text="개인 키를 pem 파일로 다운로드":::

1. Pem 파일을 다운로드 한 후에는 SSH 클라이언트에서 쉽게 가리킬 수 있는 컴퓨터의 위치로 이동할 수 있습니다.


## <a name="connect-to-the-vm"></a>VM에 연결

로컬 컴퓨터에서 PowerShell 프롬프트를 열고 다음을 입력 합니다.

```powershell
ssh -i <path to the .pem file> username@<ipaddress of the VM>
```

예를 들어 다음과 같이 입력합니다. `ssh -i /Downloads/mySSHKey.pem azureuser@123.45.67.890`


## <a name="upload-an-ssh-key"></a>SSH 키 업로드

또한 Azure에 저장할 공용 SSH 키를 업로드할 수 있습니다. SSH 키 쌍을 만드는 방법에 대 한 자세한 내용은 [ssh 키를 사용 하 여 Linux vm에 연결](./linux/ssh-from-windows.md)을 참조 하세요.

1. [Azure Portal](https://portal.azure.com)을 엽니다.

1. 페이지 맨 위에 있는 *SSH* 를 입력 하 여 검색 합니다. **Marketplace*에서 **SSH 키**를 선택 합니다.

1. **SSH 키** 페이지에서 **만들기**를 선택 합니다.

   :::image type="content" source="./media/ssh-keys/upload.png" alt-text="Azure에 저장할 SSH 공개 키 업로드":::

1. **리소스 그룹** 에서 **새로 만들기** 를 선택 하 여 키를 저장할 새 리소스 그룹을 만듭니다. 리소스 그룹의 이름을 입력 하 고 **확인**을 선택 합니다.

1. **지역** 에서 키를 저장할 지역을 선택 합니다. 모든 지역에서 키를 사용할 수 있으며,이는 저장 되는 지역에 불과합니다.

1. 키 **쌍 이름**에 키의 이름을 입력 합니다.

1. **SSH 공개 키 원본**에서 **기존 공개 키 업로드**를 선택 합니다. 

1. 공개 키의 전체 콘텐츠를 **업로드 키** 에 붙여 넣고 **검토 + 만들기**를 선택 합니다.

1. 유효성 검사가 완료되면 **만들기**를 선택합니다. 

키를 업로드 한 후에는 VM을 만들 때 사용 하도록 선택할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Vm에서 SSH 키를 사용 하는 방법에 대해 자세히 알아보려면 [ssh 키를 사용 하 여 Linux vm에 연결](./linux/ssh-from-windows.md)을 참조 하세요.
