---
title: 'Azure AD Connect: 단계적 롤아웃을 통해 클라우드 인증 | Microsoft Docs'
description: 이 문서에서는 단계적 롤아웃을 사용하여 페더레이션 인증에서 클라우드 인증으로 마이그레이션하는 방법을 설명합니다.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/03/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd1c7f5dec57127f92da52be908bd6faa2c90e85
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500226"
---
# <a name="migrate-to-cloud-authentication-using-staged-rollout-preview"></a>단계적 롤아웃을 사용하여 클라우드 인증으로 마이그레이션(미리 보기)

단계적 롤아웃을 사용 하면 도메인을 자르기 전에 Azure AD Multi-Factor Authentication (MFA), 조건부 액세스, 누출 된 자격 증명에 대 한 Id 보호, Id 관리 등의 클라우드 인증 기능을 통해 사용자 그룹을 선택적으로 테스트할 수 있습니다.  이 문서에서는 전환 방법을 설명합니다. 단계적 롤아웃을 시작하기 전에, 다음 조건 중 하나 이상에 해당할 때 미치게 되는 영향을 고려해야 합니다.
    
-  현재 온-프레미스 Multi-Factor Authentication 서버를 사용하고 있습니다. 
-  인증에 스마트 카드를 사용하고 있습니다. 
-  현재 서버는 특정 페더레이션 전용 기능을 제공합니다.

이 기능을 시도하기 전에, 적절한 인증 방법 선택에 대한 가이드를 검토하는 것이 좋습니다. 자세한 내용은 [Azure Active Directory 하이브리드 ID 솔루션용으로 올바른 인증 방법 선택](./choose-ad-authn.md#comparing-methods)의 "방법 비교" 표를 참조하세요.

기능 개요는 이 "Azure Active Directory: 단계적 롤아웃이란?" 비디오를 시청하세요.

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>사전 요구 사항

-   도메인이 페더레이션된 Azue AD(Azure Active Directory) 테넌트가 있습니다.

-   다음 두 옵션 중 하나로 전환하기로 결정했습니다.
    - **옵션 A**  -  *암호 해시 동기화 (동기화)*  +  *SSO (원활한 Single Sign-On)*.  자세한 내용은 [암호 해시 동기화 란 무엇](whatis-phs.md) 이며 [원활한 SSO 란?](how-to-connect-sso.md) 을 참조 하세요.
    - **옵션 B**  -  *통과 인증*  +  *원활한 SSO*.  자세한 내용은 [통과 인증 이란?](how-to-connect-pta.md) 을 참조 하세요.  
    
    *Seamless SSO* 는 선택 사항이지만, 이 옵션을 사용하여 회사 네트워크 내부에서 도메인에 조인된 머신을 실행하는 사용자에게 자동 로그인 환경을 구현하는 것이 좋습니다.

-   클라우드 인증으로 마이그레이션되는 사용자에게 필요한 모든 테넌트 브랜딩 및 조건부 액세스 정책을 적절하게 구성했습니다.

-   Azure AD Multi-Factor Authentication를 사용 하려는 경우 [SSPR (셀프 서비스 암호 재설정)에 대해 결합 된 등록](../authentication/concept-registration-mfa-sspr-combined.md) 을 사용 하 고 사용자가 인증 방법을 한 번 등록 하도록 Multi-Factor Authentication 하는 것이 좋습니다. 참고-SSPR를 사용 하 여 암호를 재설정 하거나, MyProfile을 사용 하 여 암호를 변경할 때 준비 된 롤아웃 중에 Azure AD Connect는 다시 설정 후 최대 2 분이 걸릴 수 있는 새 암호 해시를 동기화 해야 합니다.

-   단계적 롤아웃 기능을 사용하려면 테넌트의 전역 관리자여야 합니다.

-   특정 Active Directory 포리스트에서 *Seamless SSO* 를 사용하도록 설정하려면 도메인 관리자여야 합니다.

-  하이브리드 Azure AD 또는 Azure AD 조인을 배포 하는 경우 Windows 10 1903 업데이트로 업그레이드 해야 합니다.


## <a name="supported-scenarios"></a>지원되는 시나리오

다음은 단계적 롤아웃이 가능한 시나리오입니다. 이 기능은 다음에 대해서만 작동합니다.

- Azure AD Connect를 사용하여 Azure AD에 프로비저닝된 사용자. 클라우드 전용 사용자에게는 적용되지 않습니다.

- 브라우저 및 *최신 인증* 클라이언트의 사용자 로그인 트래픽. 레거시 인증을 사용하는 애플리케이션 또는 클라우드 서비스는 페더레이션 인증 흐름으로 대체됩니다. 최신 인증을 사용하지 않는 Exchange 온라인 또는 최신 인증을 지원하지 않는 Outlook 2010을 예로 들 수 있습니다.
- 그룹 크기는 현재 사용자 50,000명으로 제한되어 있습니다.  사용자 수가 50,000명을 초과하는 그룹이 있는 경우 해당 그룹을 여러 그룹으로 분할하여 단계적으로 롤아웃하는 것이 좋습니다.

## <a name="unsupported-scenarios"></a>지원되지 않는 시나리오

다음은 단계적 롤아웃이 불가능한 시나리오입니다.

- 응용 프로그램 또는 클라우드 서비스는 POP3 및 SMTP와 같은 레거시 인증을 사용 합니다.

- 인증하는 동안 특정 애플리케이션이 Azure AD에 "domain_hint" 쿼리 매개 변수를 보냅니다. 이러한 흐름이 계속되며, 단계적 롤아웃을 사용하도록 설정된 사용자는 계속해서 인증에 페더레이션을 사용합니다.

<!-- -->

- 관리자는 보안 그룹을 사용하여 클라우드 인증을 롤아웃할 수 있습니다. 온-프레미스 Active Directory 보안 그룹을 사용할 때 동기화 대기 시간을 방지하려면 클라우드 보안 그룹을 사용하는 것이 좋습니다. 다음 조건이 적용됩니다.

    - 기능당 최대 10개의 그룹을 사용할 수 있습니다. 즉, *암호 해시 동기화*, *통과 인증* 및 *Seamless SSO* 각각에 10개 그룹을 사용할 수 있습니다.
    - 중첩 그룹은 *지원되지 않습니다*. 이 범위는 공개 미리 보기에도 적용됩니다.
    - 단계적 롤아웃에는 동적 그룹이 *지원되지 않습니다*.
    - 그룹 내의 연락처 개체는 그룹이 추가되는 것을 차단합니다.

- 여전히 Azure AD Connect 또는 PowerShell을 사용하여 페더레이션 인증에서 클라우드 인증으로 최종적으로 전환해야 합니다. 단계적 롤아웃은 도메인을 페더레이션에서 관리형으로 전환하지 않습니다.  도메인에 대 한 자세한 내용은 [페더레이션에서 암호 해시 동기화로 마이그레이션](plan-migrate-adfs-password-hash-sync.md) 및 [페더레이션에서 통과 인증으로 마이그레이션](plan-migrate-adfs-pass-through-authentication.md) 을 참조 하세요.



- 단계적 롤아웃에 대한 보안 그룹을 처음 추가하면 UX 시간 제한을 방지하기 위해 사용자 수가 200명으로 제한됩니다. 그룹을 추가한 후에는 필요한 만큼 사용자를 그룹에 추가할 수 있습니다.

- 사용자가 준비 된 롤아웃 상태에서 EnforceCloudPasswordPolicyForPasswordSyncedUsers을 사용 하는 경우 암호 만료 정책은 사용자 지정 옵션 없이 90 일로 설정 됩니다. 


## <a name="get-started-with-staged-rollout"></a>단계적 롤아웃 시작

단계적 롤아웃을 사용하여 *암호 해시 동기화* 로그인을 테스트하려면 다음 섹션의 사전 작업 지침을 따릅니다.

사용할 PowerShell cmdlet에 대한 자세한 내용은 [Azure AD 2.0 미리 보기](/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)를 참조하세요.

## <a name="pre-work-for-password-hash-sync"></a>암호 해시 동기화를 위한 사전 작업

1. Azure AD Connect의 [선택적 기능](how-to-connect-install-custom.md#optional-features) 페이지에서 *암호 해시 동기화* 를 사용 하도록 설정 합니다. 

   ![Azure Active Directory Connect의 "선택적 기능" 페이지 스크린샷](media/how-to-connect-staged-rollout/sr1.png)

1. 모든 사용자의 암호 해시가 Azure AD와 동기화되도록 전체 *암호 해시 동기화* 주기가 실행되었는지 확인합니다. *암호 해시 동기화* 상태를 확인하려면 [Azure AD Connect 동기화를 사용하여 암호 해시 동기화 문제 해결](tshoot-connect-password-hash-synchronization.md)의 PowerShell 진단을 사용하면 됩니다.

   ![AADConnect 문제 해결 로그의 스크린샷](./media/how-to-connect-staged-rollout/sr2.png)

단계적 롤아웃을 사용하여 *통과 인증* 로그인을 테스트하려면 다음 섹션의 사전 작업 지침에 따라 단계적 롤아웃을 사용하도록 설정합니다.

## <a name="pre-work-for-pass-through-authentication"></a>통과 인증을 위한 사전 작업

1. Windows Server 2012 R2 이상 버전을 실행하는 서버 중에서 *통과 인증* 에이전트를 실행할 서버를 찾습니다. 

   Azure AD Connect 서버를 선택하지 *마세요*.  서버가 도메인에 조인되었고, 선택한 사용자를 Active Directory로 인증할 수 있고, 아웃바운드 포트 및 URL에서 Azure AD와 통신할 수 있는지 확인합니다. 자세한 내용은 [빠른 시작: Azure AD Seamless Single Sign-On](how-to-connect-sso-quick-start.md)의 "1단계: 필수 구성 요소 확인" 섹션을 참조하세요.

1. 서버에 [Azure AD Connect 인증 에이전트를 다운로드](https://aka.ms/getauthagent)하여 설치합니다. 

1. [고가용성을 사용 하도록 설정 하려면](how-to-connect-sso-quick-start.md)다른 서버에 추가 인증 에이전트를 설치 합니다.

1. [스마트 잠금 설정](../authentication/howto-password-smart-lockout.md)을 적절하게 구성했는지 확인합니다. 이렇게 하면 사용자의 온-프레미스 Active Directory 계정이 악의적 행위자에 의해 잠기는 일을 방지하는 데 도움이 됩니다.

단계적 롤아웃에 대해 선택하는 로그인 방법(*암호 해시 동기화* 또는 *통과 인증*)에 관계없이 *Seamless SSO* 를 사용하도록 설정하는 것이 좋습니다. *Seamless SSO* 를 사용하도록 설정하려면 다음 섹션의 사전 작업 지침을 따르세요.

## <a name="pre-work-for-seamless-sso"></a>Seamless SSO를 위한 사전 작업

PowerShell을 사용 하 여 Active Directory 포리스트에서 *원활한 SSO* 를 사용 하도록 설정 합니다. Active Directory 포리스트가 둘 이상 있는 경우 각 포리스트에 대해 개별적으로 사용 하도록 설정 합니다. *원활한 SSO* 는 준비 된 롤아웃에 대해 선택 된 사용자에 대해서만 트리거됩니다. 기존 페더레이션 설정에는 영향을 주지 않습니다.

다음을 수행하여 *Seamless SSO* 를 사용하도록 설정합니다.

1. Azure AD Connect 서버에 로그인합니다.

2. *% Programfiles% \\ Microsoft Azure Active Directory Connect* 폴더로 이동 합니다.

3. 다음 명령을 실행하여 *Seamless SSO* PowerShell 모듈을 가져옵니다. 

   `Import-Module .\AzureADSSO.psd1`

4. PowerShell을 관리자 권한으로 실행합니다. PowerShell에서 `New-AzureADSSOAuthenticationContext`를 호출합니다. 이 명령은 테넌트의 전역 관리자 자격 증명을 입력할 수 있는 창을 엽니다.

5. `Get-AzureADSSOStatus | ConvertFrom-Json`을 호출합니다. 이 명령은 이 기능을 사용하도록 설정된 Active Directory 포리스트 목록("도메인" 목록 참조)을 표시합니다. 기본적으로 테넌트 수준에서 false로 설정됩니다.

   ![Windows PowerShell 출력의 예](./media/how-to-connect-staged-rollout/sr3.png)

6. `$creds = Get-Credential`을 호출합니다. 프롬프트에 원하는 Active Directory 포리스트의 도메인 관리자 자격 증명을 입력합니다.

7. `Enable-AzureADSSOForest -OnPremCredentials $creds`을 호출합니다. 이 명령은 *Seamless SSO* 에 필요한 Active Directory 포리스트에 대한 온-프레미스 도메인 컨트롤러에서 AZUREADSSOACC 컴퓨터 계정을 만듭니다.

8. *Seamless SSO* 의 URL이 인트라넷 영역에 있어야 합니다. 그룹 정책을 사용하여 이러한 URL을 배포하려면 [빠른 시작: Azure AD Seamless Single Sign-On](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature)을 참조하세요.

9. 완전한 연습을 원하신다면 *Seamless SSO* 를 위한 [배포 계획](https://aka.ms/SeamlessSSODPDownload)을 다운로드하세요.

## <a name="enable-staged-rollout"></a>단계별 롤아웃을 사용하도록 설정

특정 기능(*통과 인증*, *암호 해시 동기화* 또는 *Seamless SSO*)을 그룹의 특정 사용자 세트에 롤아웃하려면 다음 섹션의 지침을 따르세요.

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>테넌트에서 특정 기능의 단계별 롤아웃 사용

다음 옵션 중 하나를 롤아웃할 수 있습니다.

- **옵션 A** - *암호 해시 동기화* + *Seamless SSO*
- **옵션 B** - *통과 인증* + *Seamless SSO*
- **지원되지 않음** - *암호 해시 동기화* + *통과 인증* + *Seamless SSO*

다음을 수행합니다.

1. 미리 보기 UX에 액세스하려면 [Azure AD 포털](https://aka.ms/stagedrolloutux)에 로그인합니다.

2. **관리형 사용자 로그인에 대해 단계적 출시 사용(미리 보기)** 링크를 선택합니다.

   예를 들어 *옵션 A* 를 사용하도록 설정하려면 다음 이미지처럼 **암호 해시 동기화** 및 **Seamless Single Sign-On** 컨트롤을 **켜기** 로 밉니다.

   ![Azure AD Connect 페이지](./media/how-to-connect-staged-rollout/sr4.png)

   !["단계적 롤아웃 기능 사용(미리 보기)" 페이지](./media/how-to-connect-staged-rollout/sr5.png)

3. *통과 인증* 및 *Seamless SSO* 를 사용하도록 설정하려는 기능에 그룹을 추가합니다. UX 시간 제한을 방지하기 위해, 처음에는 보안 그룹의 멤버 수가 200개를 넘지 않게 합니다.

   !["암호 해시 동기화를 위한 그룹 관리(미리 보기)" 페이지](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >그룹의 멤버는 단계적 롤아웃을 사용하도록 자동으로 설정됩니다. 중첩 그룹 및 동적 그룹은 단계적 롤아웃을 지원하지 않습니다.
   >새 그룹을 추가 하는 경우 그룹의 사용자 (새 그룹에 대 한 최대 200 명의 사용자)가 관리 되는 auth immidiatly를 사용 하도록 업데이트 됩니다. 사용자를 추가 하거나 제거 하는 그룹을 편집 하면 변경 내용이 적용 되는 데 최대 24 시간이 걸릴 수 있습니다.
   >원활한 SSO는 사용자가 원활한 SSO 그룹 뿐만 아니라 PTA 또는 PHS 그룹에 있는 경우에만 적용 됩니다.

## <a name="auditing"></a>감사

단계적 롤아웃을 위해 수행하는 다양한 작업에 대해 다음과 같은 감사 이벤트를 사용하도록 설정했습니다.

- *암호 해시 동기화*, *통과 인증* 또는 *Seamless SSO* 에 대한 단계적 롤아웃을 사용하도록 설정할 때의 감사 이벤트

  >[!NOTE]
  >단계적 롤아웃을 사용하여 *Seamless SSO* 를 켜면 감사 이벤트가 기록됩니다.

  !["기능에 대한 롤아웃 정책 만들기" 창 - 작업 탭](./media/how-to-connect-staged-rollout/sr7.png)

  !["기능에 대한 롤아웃 정책 만들기" 창 - 수정된 속성 탭](./media/how-to-connect-staged-rollout/sr8.png)

- *암호 해시 동기화*, *통과 인증* 또는 *Seamless SSO* 에 그룹이 추가될 때의 감사 이벤트

  >[!NOTE]
  >단계적 롤아웃을 위해 *암호 해시 동기화* 에 그룹이 추가될 때 감사 이벤트가 기록됩니다.

  !["기능 롤아웃에 그룹 추가" 창 - 작업 탭](./media/how-to-connect-staged-rollout/sr9.png)

  !["기능 롤아웃에 그룹 추가" 창 - 수정된 속성 탭](./media/how-to-connect-staged-rollout/sr10.png)

- 그룹에 추가된 사용자가 단계적 롤아웃을 사용하도록 설정될 때의 감사 이벤트

  !["기능 롤아웃에 사용자 추가" 창 - 작업 탭](media/how-to-connect-staged-rollout/sr11.png)

  !["기능 롤아웃에 사용자 추가" 창 - 대상 탭](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>유효성 검사

*암호 해시 동기화* 또는 *통과 인증*(사용자 이름 및 암호 로그인)을 사용하여 로그인을 테스트하려면 다음을 수행합니다.

1. 엑스트라넷에서 프라이빗 브라우저 섹션의 [앱 페이지](https://myapps.microsoft.com)로 이동한 다음, 단계적 롤아웃을 위해 선택한 사용자 계정의 UPN(UserPrincipalName)을 입력합니다.

   단계적 롤아웃 대상으로 지정된 사용자는 페더레이션 로그인 페이지로 리디렉션되지 않습니다. 대신, Azure AD 테넌트 브랜드 로그인 페이지에서 로그인하라는 메시지가 표시됩니다.

1. UserPrincipalName으로 필터링하여 [Azure AD 로그인 활동 보고서](../reports-monitoring/concept-sign-ins.md)에 로그인이 성공적으로 나타나는지 확인합니다.

*Seamless SSO* 를 사용하여 로그인을 테스트하려면 다음을 수행합니다.

1. 인트라넷에서 프라이빗 브라우저 섹션의 [앱 페이지](https://myapps.microsoft.com)로 이동한 다음, 단계적 롤아웃을 위해 선택한 사용자 계정의 UPN(UserPrincipalName)을 입력합니다.

   *Seamless SSO* 의 단계적 롤아웃 대상으로 지정된 사용자에게는 자동으로 로그인되기 전에 "로그인하는 중..." 메시지가 표시됩니다.

1. UserPrincipalName으로 필터링하여 [Azure AD 로그인 활동 보고서](../reports-monitoring/concept-sign-ins.md)에 로그인이 성공적으로 나타나는지 확인합니다.

   선택한 단계적 롤아웃 사용자에 대해 AD FS(Active Directory Federation Services)에서 여전히 발생하는 사용자 로그인을 추적하려면 [AD FS 문제 해결: 이벤트 및 로깅](/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events)의 지침을 따릅니다. 타사 페더레이션 공급자에서 이를 확인하는 방법은 공급자 설명서를 참조하세요.

## <a name="remove-a-user-from-staged-rollout"></a>단계적 롤아웃에서 사용자 제거

그룹에서 사용자를 제거하면 해당 사용자에 대한 단계적 롤아웃이 해제됩니다. 단계적 롤아웃 기능을 사용하지 않도록 설정하려면 컨트롤을 다시 **끄기** 로 밉니다.

## <a name="frequently-asked-questions"></a>질문과 대답

**Q: 이 기능을 프로덕션 환경에서 사용할 수 있나요?**

A: 예, 프로덕션 테넌트에서 이 기능을 사용할 수 있습니다. 하지만 테스트 테넌트에서 먼저 사용해 보는 것이 좋습니다.

**Q: 일부 사용자는 페더레이션 인증을 사용하고 나머지 사용자는 클라우드 인증을 사용하는 영구적 "공존"을 유지하는 데 이 기능을 사용할 수 있나요?**

A: 아니요,이 기능은 클라우드 인증 테스트를 위해 설계 되었습니다. 몇 명의 사용자 그룹을 성공적으로 테스트 한 후에는 클라우드 인증으로 이동 해야 합니다. 영구적 혼합 상태를 사용하면 예기치 않은 인증 흐름이 발생할 수 있으므로 사용하지 않는 것이 좋습니다.

**Q: PowerShell을 사용하여 단계적 롤아웃을 수행할 수 있나요?**

A: 예. PowerShell을 사용하여 단계적 롤아웃을 수행하는 방법을 알아보려면 [Azure AD Preview](/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [Azure AD 2.0 미리 보기](/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
