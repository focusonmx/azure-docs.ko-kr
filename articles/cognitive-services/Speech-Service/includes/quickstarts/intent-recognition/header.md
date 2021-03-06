---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 8d70d282ffb1a39f9ffb3eb6addf694ac9d0d060
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422277"
---
이 빠른 시작에서는 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 및 LUIS(Language Understanding) 서비스를 사용하여 마이크에서 캡처한 오디오 데이터의 의도를 인식합니다. 특히 Speech SDK를 사용하여 음성을 캡처하고 LUIS에서 미리 빌드된 도메인을 사용하여 광원 설정 및 해제와 같은 홈 자동화의 의도를 식별할 수 있습니다. 

몇 가지 필수 구성 요소를 충족한 후 마이크에서 음성을 인식하고 의도를 식별하는 작업은 몇 가지 단계를 수행하면 됩니다.

> [!div class="checklist"]
>
> * 구독 키 및 지역에서 `SpeechConfig` 개체를 만듭니다.
> * 위의 `SpeechConfig` 개체를 사용하여 `IntentRecognizer` 개체를 만듭니다.
> * `IntentRecognizer` 개체를 사용하여 단일 발화에 대한 인식 프로세스를 시작합니다.
> * 반환된 `IntentRecognitionResult`를 검사합니다.
