---
title: '빠른 시작: 앱용 웹 API에 액세스 - Microsoft ID 플랫폼 | Azure'
description: 이 빠른 시작에서는 리디렉션 URI, 자격 증명 또는 웹 API에 대한 액세스 권한을 포함하도록 Microsoft 플랫폼에 등록된 앱을 구성합니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/22/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 210ed5b8ad53fd59a46e160fe5fc72633d115d44
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082325"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>빠른 시작: 웹 API에 액세스하는 클라이언트 애플리케이션 구성

이 빠른 시작에서는 리디렉션 URI, 자격 증명 또는 애플리케이션의 웹 API에 대한 액세스 권한을 추가합니다. 웹 또는 기밀 클라이언트 애플리케이션은 인증이 필요한 권한 부여 흐름에 참가하려면 보안 자격 증명을 설정해야 합니다. Azure Portal에서 지원하는 기본 인증 방법은 클라이언트 ID + 비밀 키입니다. 앱은 이 프로세스 중에 액세스 토큰을 얻습니다.

클라이언트가 리소스 애플리케이션(예: Microsoft Graph API)에서 공개한 웹 API에 액세스할 수 있도록, 동의 프레임워크는 클라이언트에서 요청한 권한에 필요한 권한 부여를 제공합니다. 기본적으로 모든 애플리케이션은 Microsoft Graph API에서 권한을 요청할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* [빠른 시작: Microsoft ID 플랫폼에 애플리케이션 등록](quickstart-register-app.md)
* [Microsoft ID 플랫폼 엔드포인트의 권한 및 동의](v2-permissions-and-consent.md) 검토
* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Azure Portal에 로그인하고 앱 선택

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 사용자의 계정으로 둘 이상의 테넌트에 액세스할 수 있는 경우 오른쪽 위 모서리에서 계정을 선택합니다. 포털 세션을 원하는 Azure AD 테넌트로 설정합니다.
1. **Azure Active Directory**를 검색하고 선택합니다. **관리** 아래에서 **앱 등록**을 선택합니다.
1. 구성하려는 애플리케이션을 찾아 선택합니다. 앱을 선택하면 애플리케이션의 **개요** 또는 기본 등록 페이지가 나타납니다.

다음 절차에 따라 웹 API에 액세스하도록 애플리케이션을 구성합니다.

## <a name="add-redirect-uris-to-your-application"></a>애플리케이션에 리디렉션 URI 추가

사용자 지정 리디렉션 URI 및 제안된 리디렉션 URI를 애플리케이션에 추가할 수 있습니다. 웹 및 퍼블릭 클라이언트 애플리케이션의 사용자 지정 리디렉션 URI를 추가하는 방법은 다음과 같습니다.

1. 앱의 **개요** 페이지에서 **인증**을 선택합니다.
1. **리디렉션 URI**를 찾습니다. **이전 환경으로 전환**을 선택해야 할 수도 있습니다.
1. 빌드하려는 애플리케이션 유형을 **웹** 또는 **퍼블릭 클라이언트/네이티브(모바일 및 데스크톱)** 중에 선택합니다.
1. 애플리케이션의 리디렉션 URI를 입력합니다.

   * 웹 애플리케이션의 경우 애플리케이션의 기준 URL을 제공합니다. 예를 들어 `http://localhost:31544`은 로컬 머신에서 실행 중인 웹 애플리케이션의 URL일 수 있습니다. 사용자는 이 URL을 사용하여 웹 클라이언트 애플리케이션에 로그인합니다.
   * 공용 애플리케이션의 경우 Azure AD에서 토큰 응답을 반환하는 데 사용하는 URI를 제공합니다. 애플리케이션 고유의 값을 입력합니다(예: `https://MyFirstApp`).
1. **저장**을 선택합니다.

퍼블릭 클라이언트에 대해 제안된 리디렉션 URI 중에 선택하려면 다음 단계를 따릅니다.

1. 앱의 **개요** 페이지에서 **인증**을 선택합니다.
1. **퍼블릭 클라이언트(모바일, 데스크톱)에 제안된 리디렉션 URI**를 찾습니다. **이전 환경으로 전환**을 선택해야 할 수도 있습니다.
1. 애플리케이션의 리디렉션 URI를 하나 이상 선택합니다. 사용자 지정 리디렉션 URI를 입력할 수 있습니다. 무엇을 사용해야 하는지 확실하지 않으면 라이브러리 설명서를 확인하세요.
1. **저장**을 선택합니다.

리디렉션 URI에 적용되는 제한 사항이 있습니다. 자세한 내용은 [리디렉션 URI/회신 URL 제한 및 제한 사항](https://docs.microsoft.com/azure/active-directory/develop/reply-url)을 참조하세요.

> [!NOTE]
> 대상으로 지정하려는 플랫폼 또는 디바이스에 따라 애플리케이션에 대한 설정을 구성할 수 있는 새 **인증** 설정 환경을 사용해 보세요.
>
> 이 보기를 표시하려면 기본 **인증** 페이지에서 **새 환경을 체험해 보세요.** 를 선택합니다.
>
> !["새 환경을 체험해 보세요"를 클릭하여 플랫폼 구성 보기 표시](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> 그러면 [새 **플랫폼 구성** 페이지](#configure-platform-settings-for-your-application)로 이동합니다.

### <a name="configure-advanced-settings-for-your-application"></a>애플리케이션에 대한 고급 설정 구성

등록하는 애플리케이션에 따라 구성해야 하는 몇 가지 추가 설정은 다음과 같습니다.

* **로그아웃 URL**
* 단일 페이지 앱의 경우 **암시적 허용**을 사용하도록 설정하고 권한 부여 엔드포인트에서 발급하려는 토큰을 선택할 수 있습니다.
* **기본 클라이언트 유형** 섹션에서 Windows 통합 인증, 디바이스 코드 흐름 또는 사용자 이름/암호를 사용하여 토큰을 획득하는 데스크톱 앱의 경우 **애플리케이션을 퍼블릭 클라이언트로 처리합니다.** 설정을 **예**로 설정합니다.
* Live SDK를 사용하여 Microsoft 계정 서비스와 통합한 기존 앱의 경우 **Live SDK 지원**을 구성합니다. 새 앱에는 이 설정이 필요하지 않습니다.
* **기본 클라이언트 유형**
* **지원되는 계정 유형**

### <a name="modify-supported-account-types"></a>지원되는 계정 유형 수정

**지원되는 계정 유형**은 애플리케이션을 사용하거나 API에 액세스할 수 있는 사용자를 지정합니다.

애플리케이션을 등록할 때 지원되는 계정 유형을 구성한 경우 다음과 같은 경우에만 애플리케이션 매니페스트 편집기를 사용하여 이 설정을 변경할 수 있습니다.

* 계정 유형을 **AzureADMyOrg** 또는 **AzureADMultipleOrgs**에서 **AzureADandPersonalMicrosoftAccount**로 변경하거나 그 반대로 변경합니다.
* 계정 유형을 **AzureADMyOrg** 에서 **AzureADMultipleOrgs**로 변경하거나 그 반대로 변경합니다.

기존 앱 등록에 지원되는 계정 유형을 변경하려면 `signInAudience` 키를 업데이트합니다. 자세한 내용은 [애플리케이션 매니페스트 구성](reference-app-manifest.md#configure-the-app-manifest)을 참조하세요.

## <a name="configure-platform-settings-for-your-application"></a>애플리케이션에 대한 플랫폼 설정 구성

![플랫폼 또는 디바이스에 따른 앱 설정 구성](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations.png)

플랫폼 또는 디바이스에 따라 애플리케이션 설정을 구성하려면 다음을 목표로 합니다.

1. **플랫폼 구성** 페이지에서 **플랫폼 추가**를 선택하고 사용 가능한 옵션 중에서 선택합니다.

   ![플랫폼 구성 페이지 표시](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. 선택한 플랫폼에 따라 설정 정보를 입력합니다.

   | 플랫폼                | 구성 설정            |
   |-------------------------|-----------------------------------|
   | **Web**              | 애플리케이션의 **리디렉션 URI**를 입력합니다. |
   | **iOS/macOS**              | Info.plist의 XCode 또는 [빌드 설정]에서 찾을 수 있는 앱 **번들 ID**를 입력합니다. 번들 ID를 추가하면 애플리케이션의 리디렉션 URI가 자동으로 만들어집니다. |
   | **Android**          | AndroidManifest.xml 파일에서 찾을 수 있는 앱 **패키지 이름**을 입력합니다.<br/>**서명 해시**를 생성하여 입력합니다. 서명 해시를 추가하면 애플리케이션의 리디렉션 URI가 자동으로 만들어집니다.  |
   | **모바일 및 데스크톱 애플리케이션**  | (선택 사항) 데스크톱 및 디바이스용 애플리케이션을 빌드하는 경우 추천되는 **제안된 리디렉션 URI** 중 하나를 선택합니다.<br/>(선택 사항) Azure AD에서 인증 요청에 대한 응답으로 사용자를 리디렉션하는 위치로 사용되는 **사용자 지정 리디렉션 URI**를 입력합니다. 예를 들어 상호 작용을 원하는 .NET Core 애플리케이션의 경우 `http://localhost`를 사용합니다. |

   > [!NOTE]
   > AD FS(Active Directory Federation Services) 및 Azure AD B2C에서 포트 번호도 지정해야 합니다.  예: `http://localhost:1234` 

   > [!IMPORTANT]
   > 최신 MSAL(Microsoft 인증 라이브러리) 또는 broker를 사용하지 않는 모바일 애플리케이션의 경우 **데스크톱 + 디바이스**에서 이러한 애플리케이션의 리디렉션 URI를 구성해야 합니다.

선택한 플랫폼에 따라 구성할 수 있는 추가 설정이 있을 수 있습니다. **웹앱**의 경우 다음을 수행할 수 있습니다.

* 리디렉션 URI를 추가합니다.
* 권한 부여 엔드포인트에서 발급하려는 토큰을 선택하도록 **암시적 허용**을 구성합니다.

  * 단일 페이지 앱의 경우 **토큰 액세스** 및 **ID 토큰**을 모두 선택합니다.
  * 웹앱의 경우 **ID 토큰**을 선택합니다.

## <a name="add-credentials-to-your-web-application"></a>웹 애플리케이션에 자격 증명 추가

웹 애플리케이션에 자격 증명을 추가하려면 인증서를 추가하거나 클라이언트 암호를 만듭니다. 인증서를 추가하려면:

1. 앱의 **개요** 페이지에서 **인증서 및 비밀** 섹션을 선택합니다.
1. **인증서 업로드**를 선택합니다.
1. 업로드할 파일을 선택합니다. cer, .pem, .crt 중 한 가지 파일 형식이어야 합니다.
1. **추가**를 선택합니다.

클라이언트 암호를 추가하려면 다음을 수행합니다.

1. 앱의 **개요** 페이지에서 **인증서 및 비밀** 섹션을 선택합니다.
1. **새 클라이언트 비밀**을 선택합니다.
1. 클라이언트 비밀에 대한 설명을 추가합니다.
1. 기간을 선택합니다.
1. **추가**를 선택합니다.

> [!NOTE]
> 구성 변경 사항을 저장하면 맨 오른쪽 열에 클라이언트 비밀 값이 포함됩니다. 이 페이지를 벗어나면 액세스할 수 없으므로 클라이언트 애플리케이션 코드에서 사용할 **값을 복사해야 합니다**.

## <a name="add-permissions-to-access-web-apis"></a>웹 API 액세스 권한 추가

[Graph API 로그인 및 읽기 사용자 프로필 권한](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions)은 기본적으로 선택됩니다. 각 웹 API에서 다음과 같은 [두 가지 권한 형식](developer-glossary.md#permissions) 중에 선택할 수 있습니다.

* **애플리케이션 권한**. 클라이언트 애플리케이션이 직접 웹 API에 액세스해야 합니다(사용자 컨텍스트 없음). 이 유형의 권한은 관리자의 동의가 필요합니다. 이 권한은 데스크톱 및 모바일 클라이언트 애플리케이션에 사용할 수 없습니다.
* **위임된 권한**. 클라이언트 애플리케이션이 로그인된 사용자로 웹 API에 액세스해야 하지만 이 액세스는 선택한 권한에 따라 제한됩니다. 이 형식의 사용 권한은 관리자의 동의를 필요로 하지 않는 한 사용자가 부여할 수 있습니다.

  > [!NOTE]
  > 위임된 권한을 애플리케이션에 추가할 경우 테넌트 내의 사용자에게 자동으로 동의를 부여하지 않습니다. 관리자가 모든 사용자를 대신하여 동의를 허락하지 않는 한 사용자는 런타임에 추가로 위임된 사용 권한에 대해 수동으로 동의해야 합니다.

클라이언트에서 리소스 API에 액세스하는 권한을 추가하려면 다음을 수행합니다.

1. 앱 **개요** 페이지에서 **API 권한**을 선택합니다.
1. **구성된 사용 권한** 아래에서 **권한 추가**를 선택합니다.
1. 기본적으로 보기에서는 **Microsoft API**로부터 선택할 수 있습니다. 관심 있는 API 섹션을 선택합니다.

    * **Microsoft API**. Microsoft Graph 등의 Microsoft API에 대한 권한을 선택할 수 있습니다.
    * **내 조직에서 사용하는 API**. 조직에서 공개한 API 또는 조직에서 통합한 API에 대한 권한을 선택할 수 있습니다.
    * **내 API**. 내가 공개한 API에 대한 권한을 선택할 수 있습니다.

1. API를 선택한 후에는 **요청된 API 권한** 페이지가 표시됩니다. API가 위임된 권한과 애플리케이션 권한 모두를 공개할 경우 애플리케이션에 필요한 권한 유형을 선택합니다.
1. 완료되면 **권한 추가**를 선택합니다.

**API 권한** 페이지로 이동됩니다. 권한이 저장되어 테이블에 추가되었습니다.

## <a name="understanding-api-permissions-and-admin-consent-ui"></a>API 권한 및 관리자 동의 UI 이해

### <a name="configured-permissions"></a>구성된 사용 권한

이 섹션에서는 애플리케이션 개체에서 명시적으로 구성된 권한을 보여줍니다. 이러한 권한은 앱의 필수 리소스 액세스 목록에 포함됩니다. 이 표에서 사용 권한을 추가하거나 제거할 수 있습니다. 관리자는 API 권한 또는 개별 권한 세트에 대해 관리자 동의를 부여 또는 철회할 수도 있습니다.

### <a name="other-permissions-granted"></a>부여된 기타 권한

애플리케이션이 테넌트에 등록되어 있는 경우 **테넌트에 부여된 다른 권한**이라는 추가 섹션이 표시될 수 있습니다. 이 섹션에서는 테넌트에 부여되었지만 애플리케이션 개체에서 명시적으로 구성되지 않은 권한을 보여줍니다. 이러한 권한은 동적으로 요청 및 동의됩니다. 이 섹션은 적용되는 사용 권한이 하나 이상 있는 경우에만 표시됩니다.

이 섹션에 표시되는 API의 사용 권한 또는 개별 권한 세트를 **구성된 사용 권한** 섹션에 추가할 수 있습니다. 관리자는 이 섹션에서 개별 API 또는 사용 권한에 대한 관리자 동의를 해지할 수도 있습니다.

### <a name="admin-consent-button"></a>관리자 동의 단추

애플리케이션이 테넌트에 등록된 경우 **테넌트에 대한 관리자 동의 허용** 단추가 표시됩니다. 관리자가 아니거나 애플리케이션에 대해 권한이 구성되지 않은 경우에는 이 단추를 사용할 수 없습니다.
이 단추를 사용하여 관리자는 애플리케이션에 대해 구성된 권한에 대한 관리자 동의를 허용할 수 있습니다. 관리자 동의 단추를 클릭하면 구성된 사용 권한을 모두 보여 주는 동의 프롬프트가 포함된 새 창이 시작됩니다.

> [!NOTE]
> 애플리케이션에 대해 권한이 구성되는 시간과 동의 프롬프트에 해당 권한이 표시되는 시간 사이에 지연이 발생합니다. 동의 프롬프트에 구성된 사용 권한이 일부만 표시되면 닫았다가 다시 시작합니다.

권한이 허용되었으나 구성되지 않은 경우 관리자 동의 단추는 이러한 권한을 처리하라는 메시지를 표시합니다. 사용 권한을 구성된 사용 권한에 추가하거나 제거할 수 있습니다.

동의 프롬프트는 **수락** 또는 **취소** 옵션을 제공합니다. **수락** 을 선택하여 관리자 동의를 부여합니다. **취소**를 선택하면 관리자 동의가 부여되지 않습니다. 동의가 거부되었음을 나타내는 오류 메시지가 표시됩니다.

> [!NOTE]
> 동의 프롬프트에서 **수락**을 선택하여 관리자 동의를 부여하는 시점과 관리자 동의 상태가 포털에 반영되는 시점 사이에 약간의 시차가 있습니다.

## <a name="next-steps"></a>다음 단계

다음 문서로 넘어가서 웹 API를 공개하는 방법을 알아보세요.
> [!div class="nextstepaction"]
> [빠른 시작: 웹 API를 공개하는 애플리케이션 구성](quickstart-configure-app-expose-web-apis.md)

* 등록된 애플리케이션 및 이들 간의 관계를 나타내는 두 개의 Azure AD 개체에 대한 자세한 내용은 [애플리케이션 개체 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조하세요.

* Azure Active Directory를 사용해 애플리케이션을 개발할 때 사용해야 하는 브랜딩 지침에 대해 자세히 알아보려면 [애플리케이션에 대한 브랜딩 지침](howto-add-branding-in-azure-ad-apps.md)을 참조하세요.

* [빠른 시작: Microsoft ID 플랫폼에 애플리케이션 등록](quickstart-register-app.md)

* [빠른 시작: 애플리케이션에서 지원되는 계정 수정](quickstart-modify-supported-accounts.md)

* [빠른 시작: Microsoft ID 플랫폼을 사용하여 등록된 애플리케이션 제거](quickstart-remove-app.md)
