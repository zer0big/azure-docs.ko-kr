---
title: Azure Stack Edge 미니 R 안전 가이드 | Microsoft Docs
description: 안전 규칙, 지침, 고려 사항 및 Azure Stack Edge 미니 R 장치를 안전 하 게 설치 하 고 작동 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 507ceef0f13951eafdcb02d586f35c1d61764c4e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467478"
---
# <a name="azure-stack-edge-mini-r-safety-instructions"></a>Azure Stack Edge 미니 R 안전 지침

![경고 아이콘 1 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ 안전성 확인 표시 아이콘 ](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png)
 **읽기 안전성 및 상태 정보**

Azure Stack Edge 미니 R 장치를 사용 하기 전에이 문서에 있는 모든 안전 정보를 읽고, 한 배터리 팩의 구성, 하나의 a p/DC 전원 공급 장치, 하나의 모듈 전원 어댑터 및 하나의 서버 모듈을 사용 합니다. 지침을 따르지 않으면 속성에 대 한 화재, 감전, 부상 또는 손상이 발생할 수 있습니다. Azure Stack Edge 미니 R을 사용 하기 전에 아래의 모든 안전 정보를 읽으십시오.

## <a name="safety-icon-conventions"></a>안전성 아이콘 표시 규칙

위험 경고 기호에 대 한 다음 신호 단어는 다음과 같습니다.

| 아이콘 | 설명 |
|:--- |:--- |
| ![위험물 기호](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)| **위험:** 피해를 주지 않는 경우에는 유해 또는 심각한 부상이 발생 하는 위험한 상황을 나타냅니다. <br> **경고:** 피해를 주지 않는 경우에는 유해 또는 심각한 부상이 발생할 수 있는 유해 상황을 나타냅니다. <br> **주의:** 피해를 주지 않는 경우 사소한 또는 보통 부상을 일으킬 수 있는 위험한 상황을 나타냅니다.|
|

Azure Stack Edge 미니 R 장치를 설정 하 고 실행할 때 다음 위험 아이콘이 관찰 됩니다.

| 아이콘 | 설명 |
|:--- |:--- |
| ![모든 지침을 먼저 읽을 것](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png) | 모든 지침을 먼저 읽을 것 |
| ![위험물 기호](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) | 위험물 기호 |
| ![감전 아이콘](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) | 전기 충격 위험 |
| ![실내 사용만](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) | 실내 사용만 |
| ![사용자 서비스 가능 부품 없음 아이콘](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) | 사용자가 서비스할 필요가 없습니다. 제대로 교육을 받지 않은 경우 액세스하지 마세요. |
|

## <a name="handling-precautions-and-site-selection"></a>예방 조치 및 사이트 선택 처리

Azure Stack Edge 미니 R 장치에는 다음과 같은 처리 예방 조치 및 사이트 선택 기준이 있습니다.

![경고 아이콘 2 감전 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ 아이콘 ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ 사용자가 서비스할 필요가 없는 파트 아이콘 ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **주의:**

* *받은* 디바이스의 손상을 검사합니다. 장치 엔클로저가 손상 되 면 [Microsoft 지원에 문의](azure-stack-edge-placeholder.md) 하 여 교체품을 얻으십시오. 디바이스를 작동하려 하지 마세요.
* 장치가 제대로 작동 하지 않는 것으로 의심 되 면 [Microsoft 지원에 문의](azure-stack-edge-placeholder.md) 하 여 교체품을 구하십시오. 디바이스를 정비하려 하지 마세요.
* 디바이스에 사용자 서비스 가능 부품이 없습니다. 내부에 위험한 수준의 전압, 전류 및 에너지가 있습니다. 열지 마세요. 디바이스를 Microsoft로 보내서 점검을 받으세요.

![경고 아이콘 3 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **주의:**

시스템을 운영 하는 것이 좋습니다.

* 직접 햇빛 및 radiators을 포함 하 여 열 원본에서 멀리.
* 습기 또는 비에 게 노출 되지 않는 위치
* 진동 및 물리적인 충격을 최소화 하는 공간에 있습니다.  시스템은 MIL-표준-810G에 따라 충격 및 진동 용으로 설계 되었습니다.
* 전기 장치로 생성 된 강력한 전자기 필드에서 격리 됩니다.
* 액체 또는 외래 개체가 시스템에 들어가지 못하게 합니다. 음료 나 기타 액체 컨테이너를 시스템 또는 근처에 두지 마십시오.

![경고 아이콘 4 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ 사용자가 서비스할 필요가 없는 파트 아이콘 ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **주의:**

* 이 장비에는 리튬 배터리가 포함 되어 있습니다. 배터리 팩 서비스를 시도 하지 마십시오. 이 장비의 배터리는 사용자가 사용할 수 없습니다. 배터리가 잘못 된 유형으로 대체 된 경우 폭발의 위험.

![경고 아이콘 5 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **주의:**

배터리 팩이 Azure Stack Edge 미니 R 장치의 일부일 때만 요금을 청구 하 고 별도의 장치로 요금을 청구 하지 않습니다.

![경고 아이콘 6 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **주의:**

* 배터리 팩의 켜기/끄기 스위치는 배터리를 서버 모듈로만 방전 하기 위한 것입니다. 전원 어댑터가 배터리 팩에 꽂혀 있으면 스위치가 OFF 위치에 있더라도 서버 모듈에 전원이 전달 됩니다.

![경고 아이콘 7 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **주의:**

* 배터리 팩을 굽지 않거나 짧게 만들지 마십시오. 재생 하거나 제대로 삭제 해야 합니다.

![경고 아이콘 8 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **주의:**

* 제공 된 AC/DC 전원 공급 장치를 사용 하는 대신이 시스템에는 2590 배터리 형식의 필드를 사용할 수 있는 옵션도 제공 됩니다. 이 경우 최종 사용자는 적용 가능한 모든 안전, 교통, 환경 및 기타 국가/지역 규정을 충족 하는지 확인 해야 합니다.
* 유형 2590 배터리를 사용 하 여 시스템을 운영 하는 경우 배터리 제조업체에서 지정한 사용 조건 내에서 배터리를 작동 합니다.

![경고 아이콘 9 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **주의:**

이 장치에는 광학 트랜시버와 함께 사용할 수 있는 두 개의 SFP + 포트가 있습니다. 위험한 레이저 방사선을 방지 하려면 클래스 1 트랜시버만 사용 합니다.

## <a name="electrical-precautions"></a>전기 주의 사항

Azure Stack Edge 미니 R 장치에는 다음과 같은 전기적 예방 조치가 있습니다.

![경고 아이콘 10 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) ![ 감전 아이콘 ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **경고:**

전원 공급 어댑터와 함께 사용 하는 경우:

* 전원 공급 코드에 안전한 전기 접지 연결을 제공하세요. 대체 전류 (AC) 코드에는 3 선 접지 플러그 (접지 핀이 있는 플러그)가 있습니다. 이 플러그는 접지된 AC 콘센트에만 적합합니다. 접지 핀의 용도를 무시하지 마세요.
* 전원 공급 디바이스 코드의 플러그가 기본 차단 디바이스라면, 소켓 콘센트가 디바이스 근처에 있고 쉽게 액세스할 수 있는지 확인하세요.
* 다음 조건 중 하나에 해당 하는 경우 전원 코드를 분리 하 고, 코드가 아닌 플러그를 당겨 분리 하 고, 모든 케이블의 연결을 끊습니다.

  * 전원 코드 또는 플러그가 해어지거나 손상되었습니다.
  * 장치는 비, 과도 한 습기 또는 기타 liquids에 노출 됩니다.
  * 장치가 삭제 되었으며 장치 대/소문자가 손상 되었습니다.
  * 디바이스를 점검하거나 수리를 받아야 할 것 같습니다.
* 이동하기 전이나 어떤 방식으로든 손상될 것이라고 판단되는 장치 플러그를 영구적으로 뽑으십시오.

* 다음 전원 사양을 충족하도록 전기 과부하 방지 기능이 있는 적절한 전원을 제공하세요.

* 전압: 100-240 볼트 AC
* 현재: 1.7 암페어
* 빈도: 50 ~ 60 Hz

![경고 아이콘 11 감전 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ 아이콘 ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **경고:**

* 장치와 함께 제공 되는 것 외에는 AC 전원 코드를 수정 하거나 사용 하지 마세요.

![경고 아이콘 12 감전 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ 아이콘 ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ 실내 사용 전용 ](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) **경고:**

* 이 기호를 사용 하 여 레이블이 지정 된 전원 공급 장치는 실내 사용에 대해서만 등급이 지정 됩니다.

## <a name="regulatory-information"></a>규정 정보

다음은 Azure Stack Edge 미니 R 장치, 규정 모델 번호: TMA01에 대 한 규정 정보를 포함 합니다.

Azure Stack Edge 미니 R 장치는 NRTL (UL, CSA, ETL 등) 및 IEC/EN 60950-1 또는 IEC/EN 62368-1 규격 (CE로 표시 됨) 정보 기술 장비와 함께 사용 하도록 설계 되었습니다.

미국 및 캐나다 이외의 국가에서 네트워크 케이블 (장치와 함께 제공 되지 않음)은 길이가 3 미터를 초과 하는 경우이 장비와 함께 설치 되지 않습니다.

장비는 다음과 같은 환경에서 작동 하도록 설계 되었습니다.

| 환경 | 사양 |
|:---  |:--- |
| 시스템 온도 사양 | <ul><li>저장소 온도: – 20 &deg; c – 50 &deg; C (-4 &deg; f-122 &deg; f)</li><li>연속 작업: 0 &deg; c – 40 &deg; c (32 &deg; f – 104 &deg; f)</li></ul> |
| 상대 습도 (RH) 사양 | <ul><li>저장소: 5% ~ 95% 상대 습도</li><li>운영: 10% ~ 90% 상대 습도</li></ul>|
| 최대 고도 사양 | <ul><li>운영: 15000 피트 (4572 미터)</li><li>비작동: 4만 피트 (12192 미터)</li></ul>|

> ![알림 아이콘 ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **알림:** &nbsp; Microsoft에서 명시적으로 승인 하지 않은 장비에 대 한 변경 또는 수정은 사용자의 장비 작동에 대 한 사용자의 권한을 무효로 만들 수 있습니다.

캐나다 및 미국:

알림:이 장비는 테스트 되었으며, FCC 규칙의 15 부분에 따라 클래스의 디지털 장치에 대 한 제한을 준수 하기 위해 발견 되었습니다. 이러한 제한은 장비가 상용 환경에서 작동할 때 유해한 간섭을 적절하게 차단할 수 있도록 설계되었습니다. 이 장비는 무선 주파수 에너지를 생성 및 사용하고 방출할 수 있으며, 사용 설명서에 따라 설치하고 사용하지 않을 경우 무선 통신에 해로운 간섭을 일으킬 수 있습니다. 상설 지역에서이 장비를 운영 하는 경우 사용자가 자신의 비용에 간섭을 수정 해야 하는 경우 유해한 간섭을 일으킬 수 있습니다.

이 디바이스는 FCC 규칙 파트 15 및 Industry Canada 라이선스 면제 RSS 표준을 따릅니다. 작동 시 다음 두 조건이 적용됩니다. (1) 이 디바이스는 해로운 간섭을 일으키지 않을 것입니다. (2) 이 디바이스는 오작동을 일으킬 수 있는 간섭을 포함하여 모든 간섭을 받습니다.

![규정 정보 경고 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)

SERVICES (A)/NMB-3 (A) Microsoft Corporation, 한 가지 Microsoft 수단, Redmond, WA 98052, USA를 사용할 수 있습니다.
미국: (800) 426-9400 캐나다: (800) 933-4750

유럽 연합: EU 규정 준수 선언의 복사본을 요청 합니다.

> ![경고 아이콘 13 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) 제품 클래스입니다. 국내 환경에서 이 제품은 무선 간섭을 일으킬 수 있으며 이 경우 사용자는 적절한 조치를 취해야 합니다.

폐 배터리와 전기 및 전자 장비의 폐기:

![경고 아이콘 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

제품, 제품의 배터리 또는 제품 패키지에 표시된 이 기호는 제품과 배터리를 일반 쓰레기와 함께 폐기하면 안 된다는 것을 의미합니다. 사용자에게는 배터리와 전기 및 전자 제품을 재활용할 수 있도록 해당 수거 지점에 가져다 놓을 책임이 있습니다. 이렇게 별도로 수거 및 재활용하면 자연 리소스를 절약할 수 있으며, 적절하게 폐기하지 않을 시 배터리와 전기 및 전자 제품에 함유된 유해 물질이 사람의 건강과 환경에 미칠 수 있는 악영향을 차단할 수 있습니다. 폐 배터리와 전기 및 전자 폐기물을 수거하는 위치는 현지 시청/지방 자치 사무실, 가정 폐기물 폐기 서비스 부서 또는 제품을 구입한 상점에 문의하세요. WEEE에 대한 추가 정보는 erecycle@microsoft.com에 문의하세요.

이 제품은 코인 셀을 포함하고 있습니다.
Microsoft 아일랜드 Sandyford Ind Est 더블린 D18 KX32 IRL 전화 번호: + 353 1 295 3826 팩스 번호: + 353 1 706 4110

## <a name="next-steps"></a>다음 단계

- [Edge 미니 R Azure Stack 배포 준비](azure-stack-edge-mini-r-deploy-prep.md)
