---
title: 가상 컴퓨터의 전체 재해 복구
description: 이 문서에서는 Azure VMware 솔루션을 사용 하 여 가상 머신의 재해 복구를 완료 하는 방법을 보여 줍니다.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 688d91bc181e1479f5090a10af4b3b262d7ddb7f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779614"
---
# <a name="complete-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>Azure VMware 솔루션을 사용 하 여 가상 머신의 전체 재해 복구

이 문서에는 VMware HCX 솔루션을 사용 하는 Vm (가상 머신)의 재해 복구를 완료 하 고 Azure VMware Solution 사설 클라우드를 복구 또는 대상 사이트로 사용 하는 프로세스가 포함 되어 있습니다.

VMware HCX는 복제 정책에서 세밀 하 게 제어 하 고 세분성을 제공 하는 다양 한 작업을 제공 합니다. 사용 가능한 작업은 다음과 같습니다.

- **역방향** – 재해가 발생 한 후 역방향을 사용 하면 사이트 B가 원본 사이트와 사이트 A를 사용 하 여 보호 된 VM이 현재 위치 하 게 됩니다.

- **일시 중지** – 선택한 VM과 연결 된 현재 복제 정책을 일시 중지 합니다.

- **Resume** -선택한 VM과 연결 된 현재 복제 정책을 다시 시작 합니다.

- **제거** -선택한 VM에 연결 된 현재 복제 정책을 제거 합니다.

- **지금 동기화** – 바인딩된 동기화 원본 vm을 보호 된 vm으로 체크 아웃 합니다.

이 가이드에서는 다음과 같은 복제 시나리오에 대해 설명 합니다.

- VM 또는 vm 그룹을 보호 합니다.

- VM 또는 vm 그룹의 테스트 복구를 완료 합니다.

- VM 또는 vm 그룹을 복구 합니다.

- VM 또는 vm 그룹의 역방향 보호

## <a name="protect-vms"></a>VM 보호

1. 원본 사이트에서 **Vsphere 클라이언트** 에 로그인 하 고 **hcx 플러그 인** 에 액세스 합니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/hcx-vsphere.png" alt-text="VSphere HCX 옵션" border="true":::

1. **재해 복구** 영역을 입력 하 고 **vm 보호** 를 선택 합니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png" alt-text="VSphere HCX 옵션" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png":::

1. 원본 및 원격 사이트를 선택 합니다. 이 경우 원격 사이트는 Azure VMware 솔루션 사설 클라우드 여야 합니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machines.png" alt-text="VSphere HCX 옵션" border="true":::

1. 필요한 경우 **기본 복제** 옵션을 선택 합니다.

   - **압축 사용:** 낮은 처리량 시나리오에 권장 됩니다.

   - **정지 사용:** VM을 일시 중지 하 여 일관 된 복사본이 원격 사이트와 동기화 되도록 합니다.

   - **대상 저장소:** 보호 된 Vm 및 Azure VMware 솔루션 사설 클라우드의 원격 데이터 저장소 (vSAN 데이터 저장소)

   - **계산 컨테이너:** 원격 vSphere 클러스터 또는 리소스 풀.

   - **대상 폴더:** 선택 사항인 원격 대상 폴더입니다. 폴더를 선택 하지 않으면 Vm은 선택한 클러스터 바로 아래에 배치 됩니다.

   - **RPO:** 원본 VM과 보호 된 VM 간의 동기화 간격입니다. 5 분에서 24 시간 사이에 있을 수 있습니다.

   - **스냅숏 간격:** 스냅숏 사이의 간격입니다.

   - **스냅숏 수:** 구성 된 스냅숏 간격 내에 있는 총 스냅숏 수입니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png" alt-text="VSphere HCX 옵션" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png":::

1. 목록에서 하나 이상의 Vm을 선택 하 고 필요에 따라 복제 옵션을 구성 합니다.

   기본적으로 Vm은 기본 복제 옵션에 구성 된 전역 설정 정책을 상속 합니다. 선택한 VM의 각 네트워크 인터페이스에 대해 원격 **네트워크 포트 그룹** 을 구성 하 고 **마침** 을 선택 하 여 보호 프로세스를 시작 합니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/network-interface-options.png" alt-text="VSphere HCX 옵션" border="true" lightbox="./media/disaster-recovery-virtual-machines/network-interface-options.png":::

1. 동일한 재해 복구 영역에서 선택한 각 Vm에 대 한 프로세스를 모니터링 합니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png" alt-text="VSphere HCX 옵션" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png":::

1. VM이 보호 된 후에는 **스냅숏** 탭에서 다른 스냅숏을 볼 수 있습니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/list-of-snapshots.png" alt-text="VSphere HCX 옵션" border="true" lightbox="./media/disaster-recovery-virtual-machines/list-of-snapshots.png":::

   노란색 삼각형은 스냅숏 및 가상 컴퓨터가 테스트 복구 작업에서 테스트 되지 않았음을 의미 합니다.

   전원이 꺼진 VM과 전원이 켜진 VM 간에는 중요 한 차이점이 있습니다. 이미지에는 전원이 켜진 VM에 대 한 동기화 프로세스가 표시 됩니다. VM의 전체 복사본 인 첫 번째 스냅숏이 완료 될 때까지 동기화 프로세스를 시작 하 고 구성 된 간격으로 다음 단계를 완료 합니다. 전원이 꺼진 VM에 대 한 복사본을 동기화 한 다음 VM이 비활성으로 표시 되 고 보호 작업이 완료로 표시 됩니다.  VM이 켜지 면 원격 사이트에 대 한 동기화 프로세스를 시작 합니다.

## <a name="complete-a-test-recover-of-vms"></a>Vm의 테스트 복구 완료

1. Azure VMware 솔루션 사설 클라우드 인 원격 사이트의 **Vsphere 클라이언트** 에 로그인 합니다. 
1. **Hcx 플러그 인** 에서 재해 복구 영역에 있는 모든 vm의 세로 줄임표를 선택 하 여 작업 메뉴를 표시 한 후 **복구 vm 테스트** 를 선택 합니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/test-recover-virtual-machine.png" alt-text="VSphere HCX 옵션" border="true":::

1. VM의 다른 상태를 테스트 하는 데 사용 하려는 테스트 및 스냅숏에 대 한 옵션을 선택 합니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/choose-snapshot.png" alt-text="VSphere HCX 옵션" border="true":::

1. **테스트** 를 선택 하면 복구 작업이 시작 됩니다.

1. 완료 되 면 Azure VMware 솔루션 사설 클라우드 vCenter에서 새 VM을 확인할 수 있습니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/verify-test-recovery.png" alt-text="VSphere HCX 옵션" border="true" lightbox="./media/disaster-recovery-virtual-machines/verify-test-recovery.png":::

1. VM 또는 VM에서 실행 중인 응용 프로그램에 대해 테스트가 완료 된 후에는 정리를 수행 하 여 테스트 인스턴스를 삭제 합니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png" alt-text="VSphere HCX 옵션" border="true" lightbox="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png":::

## <a name="recover-vms"></a>Vm 복구

1. Azure VMware 솔루션 사설 클라우드 인 원격 사이트의 **Vsphere 클라이언트** 에 로그인 하 고 **hcx 플러그 인** 에 액세스 합니다.

   복구 시나리오의 경우이 예제에 사용 되는 Vm 그룹입니다.

1. 목록에서 복구할 VM을 선택 하 고 **작업** 메뉴를 연 다음 **복구 vm** 을 선택 합니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines.png" alt-text="VSphere HCX 옵션" border="true":::

1. 각 인스턴스에 대 한 복구 옵션을 구성 하 고 복구를 선택 하 여 복구 **작업을 시작** 합니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines-confirm.png" alt-text="VSphere HCX 옵션" border="true":::

1. 복구 작업이 완료 되 면 원격 vCenter Server 인벤토리에 새 Vm이 표시 됩니다.

## <a name="complete-a-reverse-replication-on-vms"></a>Vm에서 역방향 복제 완료

1. Azure VMware 솔루션 사설 클라우드의 **Vsphere 클라이언트** 에 로그인 하 고 **hcx 플러그 인** 에 액세스 합니다.
   
   >[!NOTE]
   > 역방향 복제를 시작 하기 전에 원본 사이트의 원래 Vm이 꺼져 있는지 확인 합니다. Vm의 전원이 꺼진 경우 작업이 실패 합니다.

1. 목록에서 원본 사이트로 다시 복제할 Vm을 선택 하 고 **작업** 메뉴를 연 다음 **역방향** 을 선택 합니다. 
1. **역방향** 을 선택 하 여 복제를 시작 합니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/reverse-operation-virtual-machines.png" alt-text="VSphere HCX 옵션" border="true":::

1. 각 VM의 세부 정보 섹션을 모니터링 합니다.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/review-reverse-operation.png" alt-text="VSphere HCX 옵션" border="true" lightbox="./media/disaster-recovery-virtual-machines/review-reverse-operation.png":::

## <a name="disaster-recovery-plan-automation"></a>재해 복구 계획 자동화

VMware HCX에는 현재 재해 복구 계획을 만들고 자동화 하는 메커니즘이 없습니다. 그러나 VMware HCX는 재해 복구 작업을 위한 Api를 포함 하 여 일련의 REST Api를 제공 합니다. API 사양은 URL의 VMware HCX Manager 내에서 액세스할 수 있습니다.

이러한 Api는 재해 복구의 다음 작업을 다룹니다.

- 보호

- 복구

- 복구 테스트

- 계획 된 복구

- Reverse

- 쿼리

- 테스트 정리

- 일시 중지

- 다시 시작

- 보호 제거

- 다시 구

JSON에서 복구 작업 페이로드의 예는 다음과 같습니다.

```json
[

    {

        "replicationId": "string",

        "needPowerOn": true,

        "instanceId": "string",

        "source": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "destination": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "placement": [

            {

                "containerType": "string",

                "containerId": "string"

            }

        ],

        "resourceId": "string",

        "forcePowerOff": true,

        "isTest": true,

        "forcePowerOffAfterTimeout": true,

        "isPlanned": true

    }

]
```

이러한 Api를 사용 하 여 재해 복구 계획의 생성 및 실행을 자동화 하는 사용자 지정 메커니즘을 작성할 수 있습니다.
