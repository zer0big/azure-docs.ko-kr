---
title: 복제본 읽기-Azure Database for MySQL-유연한 서버
description: Azure Database for MySQL 유연한 서버에서 복제본을 만들고, 복제본에 연결 하 고, 복제를 모니터링 하 고, 복제를 중지 하는 방법에 대해 알아봅니다.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: 3fe63deb8115c0043023301c6d0dc3731e97743f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492628"
---
# <a name="read-replicas-in-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL-유연한 서버에서 복제본 읽기

> [!IMPORTANT]
> Azure Database for MySQL-유연한 서버에서 복제본 읽기는 미리 보기 상태입니다.

MySQL은 인터넷 규모 웹 및 모바일 애플리케이션을 실행하는 데 널리 사용되는 데이터베이스 엔진 중 하나입니다. 대부분의 고객은 온라인 교육 서비스, 비디오 스트리밍 서비스, 디지털 결제 솔루션, 전자 상거래 플랫폼, 게임 서비스, 뉴스 포털, 정부 및 의료 웹 사이트에서 이 기능을 사용합니다. 이러한 서비스는 웹 또는 모바일 애플리케이션의 트래픽이 늘어남에 따라 서비스를 제공하고 확장해야 합니다.

응용 프로그램 쪽에서 응용 프로그램은 일반적으로 Java 또는 php로 개발 되 고 Azure 가상 머신 확장 집합 또는 Azure 앱 서비스에서 실행 되도록 마이그레이션하거나 AKS (Azure Kubernetes Service)에서 실행 되도록 컨테이너 화 된. 가상 머신 확장 집합, App Service 또는 AKS를 기본 인프라로 사용하면 새 VM을 즉시 프로비저닝하고 애플리케이션의 상태 비저장 구성 요소를 복제하여 요청을 처리함으로써 애플리케이션 확장이 단순화되지만, 데이터베이스는 중앙 집중식 상태 저장 구성 요소로 인해 병목 현상이 발생하는 경우가 많습니다.

읽기 복제본 기능을 사용하면 Azure Database for MySQL 유연한 서버에서 읽기 전용 서버로 데이터를 복제할 수 있습니다. 원본 서버에서 최대 **10 개의** 복제본으로 복제할 수 있습니다. 복제본은 MySQL 엔진의 네이티브 이진 로그(binlog) 파일의 위치 기반 복제 기술을 사용하여 비동기식으로 업데이트됩니다. binlog 복제에 대한 자세히 알려면 [MySQL binlog 복제 개요](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)를 참조합니다.

복제본은 사용자의 원본 Azure Database for MySQL 유연한 서버와 유사한 방식으로 관리 하는 새 서버입니다. VCores의 프로 비전 된 계산 및 g b/월 단위로 저장소에서 각 읽기 복제본에 대 한 청구 요금이 부과 됩니다. 자세한 내용은 [가격 책정](./concepts-compute-storage.md#pricing)을 참조 하세요.

MySQL 복제 기능 및 문제에 대한 자세한 내용은 [MySQL 복제 설명서](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)를 참조하세요.

> [!NOTE]
> 바이어스 없는 통신
>
> Microsoft는 다양하고 포용적인 환경을 지원합니다. 이 문서에는 _slave(슬레이브)_ 라는 단어에 대한 참조가 포함되어 있습니다. [바이어스 없는 통신을 위한 Microsoft 스타일 가이드](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md)에서는 이 단어를 '배제(exclusionary)'라는 단어로 인식합니다. 이 단어는 현재 소프트웨어에 표시되는 단어이므로 일관성을 위해 이 문서에서 사용됩니다. 이 단어를 제거하도록 소프트웨어가 업데이트되면 이 문서도 이에 맞춰 업데이트됩니다.
>

## <a name="common-use-cases-for-read-replica"></a>복제본 읽기의 일반적인 사용 사례

읽기 복제본 기능은 읽기 작업이 많은 워크로드의 성능과 규모를 향상시키는 데 도움이 됩니다. 읽기 작업은 복제본에 격리 될 수 있지만 쓰기 작업은 원본으로 이동할 수 있습니다.

일반적인 시나리오는 다음과 같습니다.

* [Proxysql](https://aka.ms/ProxySQLLoadBalanceReplica) 과 같은 경량 연결 프록시를 사용 하거나 마이크로 서비스 기반 패턴을 사용 하 여 응용 프로그램에서 들어오는 읽기 작업을 확장 하 여 복제본을 읽기 위해 응용 프로그램에서 들어오는 읽기 쿼리를 확장 합니다.
* BI 또는 분석 보고 작업은 읽기 복제본을 보고를 위한 데이터 원본으로 사용할 수 있습니다.
* 데이터를 보고 하는 데 여러 개의 읽기 복제본이 사용 되는 동안 원격 분석 정보가 MySQL 데이터베이스 엔진으로 수집 IoT 또는 제조 시나리오

복제본은 읽기 전용 이므로 원본에서 쓰기 용량 부담을 직접 줄이지 않습니다. 이 기능은 쓰기 작업이 많은 워크로드를 대상으로 하지 않습니다.

읽기 복제본 기능은 MySQL 동기식 복제를 사용합니다. 이 기능은 동기식 복제 시나리오를 위한 것이 아닙니다. 원본 및 복제본 간의 지연 시간이 측정 됩니다. 복제본의 데이터는 결국 원본에 있는 데이터와 일치 하 게 됩니다. 이러한 지연 시간을 수용할 수 있는 워크로드에 이 기능을 사용합니다.

## <a name="create-a-replica"></a>복제본 만들기

원본 서버에 기존 복제본 서버가 없는 경우 원본 서버는 복제를 위해 자신을 준비 하기 위해 먼저 다시 시작 됩니다.

복제본 만들기 워크플로를 시작하면 빈 Azure Database for MySQL 서버가 만들어집니다. 새 서버는 원본 서버에 있던 데이터로 채워집니다. 생성 시간은 원본의 데이터 양과 마지막 주간 전체 백업 이후 시간에 따라 달라 집니다. 시간은 몇 분에서 몇 시간까지 걸릴 수 있습니다.

> [!NOTE]
> 읽기 복제본은 원본과 동일한 서버 구성으로 만들어집니다. 복제본이 생성된 후에 복제본 서버 구성을 변경할 수 있습니다. 복제 서버는 항상 원본 서버와 동일한 리소스 그룹 및 동일한 위치에 생성 됩니다. 다른 리소스 그룹 또는 다른 구독에 복제본 서버를 만들려면 복제본 서버를 만든 후에 [이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)할 수 있습니다. 복제본이 원본을 사용할 수 있도록 복제본 서버 구성을 원본과 같거나 큰 값으로 유지 하는 것이 좋습니다.

[Azure Portal에서 읽기 복제본을 만드는 방법](how-to-read-replicas-portal.md)을 알아봅니다.

## <a name="connect-to-a-replica"></a>복제본에 연결

만들 때 복제본은 원본 서버의 연결 방법을 상속 합니다. 복제본의 연결 방법을 변경할 수 없습니다. 예를 들어 원본 서버에 **개인 액세스 (VNet 통합)** 가 있는 경우 복제본은 **공용 액세스 (허용 된 IP 주소)** 가 될 수 없습니다.

복제본이 원본 서버에서 관리자 계정을 상속 합니다. 원본 서버의 모든 사용자 계정이 읽기 복제본에 복제 됩니다. 원본 서버에서 사용할 수 있는 사용자 계정을 사용 하 여 읽기 복제본에만 연결할 수 있습니다.

일반 Azure Database for MySQL 유연한 서버에서와 같이 호스트 이름 및 유효한 사용자 계정을 사용 하 여 복제본에 연결할 수 있습니다. 관리 사용자 이름이 **myadmin** 인 **myreplica** 라는 서버의 경우 mysql CLI를 사용하여 복제본에 연결할 수 있습니다.

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin -p
```

프롬프트가 표시되면 사용자 계정의 암호를 입력합니다.

## <a name="monitor-replication"></a>복제 모니터링

Azure Database for MySQL 유연한 서버는 Azure Monitor에서 **복제 지연 시간 (초)** 을 제공 합니다. 이 메트릭은 복제본에만 사용할 수 있습니다. 이 메트릭은 `seconds_behind_master`MySQL에서 사용할 수 있는 메트릭`SHOW SLAVE STATUS` 명령을 사용하여 계산됩니다. 복제 지연 시간이 워크로드에 적합하지 않은 값에 도달하면 알리도록 경고를 설정합니다.

복제 지연 [시간이 늘어나면 복제 대기 시간 문제 해결](./../howto-troubleshoot-replication-latency.md) 을 참조 하 여 문제를 해결 하 고 가능한 원인을 파악 하십시오.

## <a name="stop-replication"></a>복제 중지

원본 및 복제본 간의 복제를 중지할 수 있습니다. 원본 서버와 읽기 복제본 간에 복제가 중지 된 후에는 복제본이 독립 실행형 서버가 됩니다. 독립 실행형 서버의 데이터는 복제 중지 명령이 시작될 때 복제본에서 사용할 수 있던 데이터입니다. 독립 실행형 서버가 원본 서버를 사용 하 여 catch 하지 않습니다.

복제본에 대 한 복제를 중지 하도록 선택 하면 이전 원본 및 기타 복제본에 대 한 모든 링크가 손실 됩니다. 원본 및 복제본 간에는 자동화 된 장애 조치가 없습니다.

> [!IMPORTANT]
> 독립 실행형 서버를 다시 복제본으로 만들 수 없습니다.
> 읽기 복제본에서 복제를 중지하기 전에 복제본에 필요한 모든 데이터가 있는지 확인하십시오.

[복제본에 대한 복제를 중지](how-to-read-replicas-portal.md)하는 방법을 알아봅니다.

## <a name="failover"></a>장애 조치

원본 서버와 복제 서버 간에는 자동화 된 장애 조치 (failover)가 없습니다. 

읽기 복제본은 읽기 집약적 작업의 크기 조정을 위한 것 이며 서버의 고가용성 요구 사항을 충족 하도록 설계 되지 않았습니다. 원본 서버와 복제 서버 간에는 자동화 된 장애 조치 (failover)가 없습니다. 읽기 복제본에서 복제를 중지 하 여 읽기 쓰기 모드에서 온라인 상태로 전환 하는 것은 수동 장애 조치 (failover)를 수행 하는 방법입니다.

복제는 비동기 이므로 원본과 복제본 사이에 지연이 발생 합니다. 지연 시간은 원본 서버에서 실행 되는 워크 로드의 양과 데이터 센터 간의 대기 시간 등 여러 가지 요소에 의해 영향을 받을 수 있습니다. 대부분의 경우 복제본 지연 시간 범위는 몇 초에서 몇 분 사이입니다. 각 복제본에 대해 사용 가능한 메트릭 *복제본 지연* 시간을 사용 하 여 실제 복제 지연 시간을 추적할 수 있습니다. 이 메트릭은 마지막으로 재생 된 트랜잭션 이후 경과 된 시간을 표시 합니다. 일정 기간 동안 복제본 지연 시간을 관찰 하 여 평균 지연 시간을 확인 하는 것이 좋습니다. 복제본 지연에 대 한 경고를 설정 하 여 예상 범위를 벗어나면 작업을 수행할 수 있습니다.

> [!Tip]
> 복제본으로 장애 조치 (failover) 하는 경우 원본에서 복제본을 연결 취소 하는 시간에 지연 시간이 발생 하면 손실 되는 데이터의 양이 표시 됩니다.

복제본으로 장애 조치 (failover)를 결정 한 후에는 다음을 선택 합니다. 

1. 복제본에 대 한 복제 중지<br/>
   이 단계는 복제본 서버에서 쓰기를 허용할 수 있도록 하는 데 필요 합니다. 이 프로세스의 일부로 복제본 서버가 원본에서 분리 됩니다. 복제 중지를 시작한 후 백 엔드 프로세스는 일반적으로 완료 하는 데 약 2 분이 걸립니다. 이 작업의 의미를 이해 하려면이 문서의 [복제 중지](#stop-replication) 섹션을 참조 하세요.
    
2. 응용 프로그램이 (이전) 복제본을 가리키도록 합니다.<br/>
   각 서버에는 고유한 연결 문자열이 있습니다. 원본 대신 (이전) 복제본을 가리키도록 응용 프로그램을 업데이트 합니다.
    
응용 프로그램이 읽기 및 쓰기를 성공적으로 처리 하면 장애 조치 (failover)를 완료 한 것입니다. 응용 프로그램의 가동 중지 시간은 문제를 감지 하 고 위의 1 단계와 2 단계를 완료 하는 시기에 따라 달라 집니다.

## <a name="considerations-and-limitations"></a>고려 사항 및 제한 사항

| 시나리오 | 제한 사항/고려 사항 |
|:-|:-|
| 영역 중복 HA를 사용 하도록 설정 된 서버의 복제본 | 지원되지 않음 |
| 영역 간 읽기 복제 | 지원되지 않음 |
| 가격 책정 | 복제 서버를 실행 하는 비용은 복제본 서버가 실행 되는 지역을 기반으로 합니다. |
| 원본 서버 다시 시작 | 기존 복제본이 없는 원본에 대 한 복제본을 만드는 경우 원본 복제본이 먼저 다시 시작 되어 복제를 위한 준비가 됩니다. 이를 고려 하 여 사용량이 많지 않은 기간 동안 이러한 작업을 수행 합니다. |
| 새 복제본 | 읽기 복제본은 새 Azure Database for MySQL 유연한 서버로 생성 됩니다. 기존 서버를 복제본으로 만들 수 없습니다. 다른 읽기 복제본의 복제본을 만들 수 없습니다. |
| 복제본 구성 | 복제본은 원본과 동일한 서버 구성을 사용 하 여 만들어집니다. 복제본을 만든 후에는 계산 세대, vCores, 저장소 및 백업 보존 기간 등의 여러 설정을 원본 서버와 독립적으로 변경할 수 있습니다. 계산 계층은 독립적으로 변경할 수도 있습니다.<br> <br> **중요**  <br> -원본 서버 구성을 새 값으로 업데이트 하기 전에 복제본 구성을 같거나 큰 값으로 업데이트 합니다. 이렇게 하면 복제본이 원본에 대한 변경 내용을 유지할 수 있습니다. <br/> 복제본을 만들 때 연결 방법과 매개 변수 설정은 원본 서버에서 복제본으로 상속 됩니다. 나중에 복제본의 규칙은 독립적입니다. |
| 중지된 복제본 | 원본 서버와 읽기 복제본 간의 복제를 중지 하면 중지 된 복제본이 읽기와 쓰기를 모두 수락 하는 독립 실행형 서버가 됩니다. 독립 실행형 서버를 다시 복제본으로 만들 수 없습니다. |
| 삭제 된 원본 및 독립 실행형 서버 | 원본 서버를 삭제 하면 복제가 모든 읽기 복제본으로 중지 됩니다. 이러한 복제본은 자동으로 독립 실행형 서버가 되며 읽기와 쓰기를 모두 허용할 수 있습니다. 원본 서버 자체가 삭제 됩니다. |
| 사용자 계정 | 원본 서버의 사용자는 읽기 복제본에 복제 됩니다. 원본 서버에서 사용할 수 있는 사용자 계정을 사용 하 여 읽기 복제본에만 연결할 수 있습니다. |
| 서버 매개 변수 | 데이터가 동기화되지 않고 데이터가 손실 또는 손상될 가능성으로부터 데이터를 보호하기 위해 읽기 복제본을 사용하는 경우 일부 서버 매개 변수는 업데이트할 수 없도록 잠깁니다. <br> 다음 서버 매개 변수는 원본 서버와 복제 서버 모두에서 잠깁니다.<br> - [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/8.0/en/innodb-file-per-table-tablespaces.html) <br> - [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) <br> [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) 매개 변수가 복제본 서버에서 잠겨 있습니다. <br> 원본 서버에서 위의 매개 변수 중 하나를 업데이트 하려면 복제본 서버를 삭제 하 고, 원본의 매개 변수 값을 업데이트 하 고, 복제본을 다시 만드십시오. |
| 기타 | -복제본의 복제본을 만드는 것은 지원 되지 않습니다. <br> -메모리 내 테이블에서 복제본이 동기화 되지 않을 수 있습니다. MySQL 복제 기술의 제한 사항입니다. 자세한 내용은 [MySQL 참조 문서](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html)를 참조하세요. <br>-원본 서버 테이블에 기본 키가 있는지 확인 합니다. 기본 키가 없으면 원본과 복제본 간의 복제 대기 시간이 발생할 수 있습니다.<br>- [Mysql 설명서](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) 에서 mysql 복제 제한 사항의 전체 목록을 검토 하세요. |

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 사용하여 읽기 복제본 생성 및 관리](how-to-read-replicas-portal.md) 방법 알아보기
- [Azure CLI를 사용하여 읽기 복제본 생성 및 관리](how-to-read-replicas-cli.md) 방법 알아보기