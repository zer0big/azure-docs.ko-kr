---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 760bb5b62e9bba9b7a83f99760f7fe5d8c399dfb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "67182154"
---
1. [장애 조치(Failover) 클러스터 관리자]에서 **역할**을 펼친 다음 가용성 그룹을 강조 표시합니다.  

2. **리소스** 탭에서 수신기 이름을 마우스 오른쪽 단추로 클릭한 다음 **속성**을 클릭합니다.

3. **종속성** 탭을 클릭 합니다. 여러 리소스가 나열 되는 경우 IP 주소에 AND가 아니라 종속성이 있는지 확인 합니다.  

4. **확인**을 클릭합니다.

5. 수신기 이름을 마우스 오른쪽 단추로 클릭한 다음 **온라인 상태로 전환**을 클릭합니다.

6. 수신기가 온라인 상태로 전환되면 **리소스** 탭에서 가용성 그룹을 마우스 오른쪽 단추로 클릭한 다음 **속성**을 클릭합니다.
   
    ![가용성 그룹 리소스 구성](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. 수신기 이름 리소스(IP 주소 리소스 이름이 아님)에 대한 종속성을 만든 다음 **확인**을 클릭합니다.
   
    ![수신기 이름에 대한 종속성 추가](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. SQL Server Management Studio를 시작한 다음 주 복제본에 연결합니다.

9. **AlwaysOn 고가용성**  >  **가용성 그룹**  >  **\<AvailabilityGroupName\>**  >  **가용성 그룹 수신기**로 이동 합니다.  
    [장애 조치(Failover) 클러스터 관리자]에서 만든 수신기 이름이 표시됩니다.

10. 수신기 이름을 마우스 오른쪽 단추로 클릭한 다음 **속성**을 클릭합니다.

11. **포트** 상자에서 이전에 사용한 $EndpointPort를 사용하여 가용성 그룹 수신기에 대한 포트 번호(이 자습서에서는 1433이 기본값임)를 지정한 다음 **확인**을 클릭합니다.

