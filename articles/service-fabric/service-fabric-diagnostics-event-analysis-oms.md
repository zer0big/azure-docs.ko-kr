---
title: Azure Monitor 로그를 사용 하 여 Azure Service Fabric 이벤트 분석
description: Azure Service Fabric 클러스터의 모니터링 및 진단에 대 한 Azure Monitor 로그를 사용 하 여 이벤트를 시각화 및 분석 하는 방법을 알아봅니다.
author: srrengar
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: srrengar
ms.openlocfilehash: f44426103b8f0fce275f33682edbc3b84a08344b
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92329562"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Azure Monitor 로그를 사용 하 여 이벤트 분석 및 시각화
 Azure Monitor 로그는 클라우드에서 호스팅되는 애플리케이션 및 서비스에서 원격 분석 데이터를 수집 및 분석하고, 가용성과 성능을 최대화하는 데 도움이 되는 분석 도구를 제공합니다. 이 문서에서는 Azure Monitor 로그에서 쿼리를 실행 하 여 정보를 얻고 클러스터에서 발생 하는 문제를 해결 하는 방법을 설명 합니다. 다음과 같은 일반적인 질문을 해결합니다.

* 상태 이벤트 문제는 어떻게 해결하나요?
* 노드 작동이 중단되면 어떻게 알 수 있나요?
* 내 애플리케이션의 서비스가 시작되거나 중지되는지 어떻게 알 수 있나요?

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>Log Analytics 작업 영역 개요

>[!NOTE] 
>클러스터 생성 시 진단 스토리지가 기본으로 사용하도록 설정되어 있는 경우에도 진단 스토리지에서 읽도록 Log Analytics 작업 영역을 설정해야 합니다.

Azure Monitor 로그는 Azure storage 테이블 또는 에이전트를 포함 하 여 관리 되는 리소스에서 데이터를 수집 하 고 중앙 리포지토리에서 유지 관리 합니다. 그런 다음 데이터를 분석, 경고 및 시각화하거나 내보낼 수 있습니다. Azure Monitor 로그는 이벤트, 성능 데이터 또는 기타 사용자 지정 데이터를 지원 합니다. 이벤트 및 단계를 [집계 하도록 진단 확장을 구성](service-fabric-diagnostics-event-aggregation-wad.md) 하는 단계를 확인 하 여 데이터가 Azure Monitor 로그로 전달 되도록 [저장소의 이벤트를 읽을 Log Analytics 작업 영역을 만듭니다](service-fabric-diagnostics-oms-setup.md) .

Azure Monitor 로그가 데이터를 받은 후 Azure는 여러 가지 시나리오에 맞게 사용자 지정 된, 들어오는 데이터를 모니터링 하기 위한 솔루션 또는 작업 대시보드를 미리 패키지 된 하는 여러 *모니터링 솔루션* 을 포함 하 고 있습니다. 여기에는 Service Fabric 클러스터를 사용할 때 진단 및 모니터링과 가장 관련된 두 개의 솔루션인 *Service Fabric 분석* 솔루션과 *컨테이너* 솔루션이 포함됩니다. 이 문서에서는 작업 영역을 사용하여 만든 Service Fabric 분석 솔루션을 사용하는 방법을 설명합니다.

## <a name="access-the-service-fabric-analytics-solution"></a>Service Fabric 분석 솔루션에 액세스

[Azure Portal](https://portal.azure.com)에서 Service Fabric 분석 솔루션을 만든 리소스 그룹으로 이동 합니다.

리소스 **ServiceFabric \<nameOfOMSWorkspace\> **를 선택 합니다.

`Summary`에는 Service Fabric용 타일을 포함하여 활성화된 각 솔루션에 대해 그래프 형태의 타일이 표시됩니다. **Service Fabric** 그래프를 클릭하여 Service Fabric 분석 솔루션으로 이동합니다.

![Service Fabric 솔루션](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

다음 이미지에서는 Service Fabric 분석 솔루션의 홈페이지를 보여 줍니다. 이 홈페이지는 클러스터에서 수행되는 작업에 대한 스냅샷 보기를 제공합니다.

![Service Fabric 분석 솔루션의 홈 페이지를 보여 주는 스크린샷](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 클러스터를 만들 때 진단을 활성화하면 다음에 대한 이벤트를 볼 수 있습니다. 

* [Service Fabric 클러스터 이벤트](service-fabric-diagnostics-event-generation-operational.md)
* [Reliable Actors 프로그래밍 모델 이벤트](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services 프로그래밍 모델 이벤트](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>즉시 사용이 가능한 Service Fabric 이벤트 외에도, [진단 확장 프로그램의 구성을 업데이트](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)하여 더 자세한 시스템 이벤트를 수집할 수 있습니다.

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>노드에 대한 작업을 포함한 Service Fabric 이벤트 보기

Service Fabric 분석 페이지에서 **Service Fabric 이벤트**에 대한 그래프를 클릭합니다.

![Service Fabric 솔루션 조작 채널](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

**목록**을 클릭하여 목록에서 이벤트를 봅니다. 일단 여기에 수집된 모든 시스템 이벤트가 표시됩니다. 참고로, 이러한 로그는 Azure Storage 계정의 **WADServiceFabricSystemEventsTable**에서 제공되며, 마찬가지로 다음에 표시되는 Reliable Services 및 Reliable Actors 이벤트는 해당 테이블에서 제공됩니다.
    
![쿼리 조작 채널](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

또는 왼쪽에 있는 돋보기를 클릭하고 Kusto 쿼리 언어를 사용하여 원하는 항목을 찾을 수 있습니다. 예를 들어 클러스터의 노드에서 수행된 모든 작업을 찾으려면 다음 쿼리를 사용하면 됩니다. 아래에 사용된 이벤트 ID는 [운영 채널 이벤트 참조](service-fabric-diagnostics-event-generation-operational.md)에서 찾을 수 있습니다.

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

특정 노드(컴퓨터), 시스템 서비스(TaskName) 같은 더 많은 필드를 쿼리할 수 있습니다.

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Service Fabric Reliable Service 및 Actor 이벤트 보기

Service Fabric 분석 페이지에서 **Reliable Services**에 대한 그래프를 클릭합니다.

![Service Fabric 솔루션 Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

**목록**을 클릭하여 목록에서 이벤트를 봅니다. 여기에서 Reliable Services의 이벤트를 볼 수 있습니다. 일반적으로 배포 및 업그레이드에서 발생하는 서비스 RunAsync가 언제 시작되고 완료되는지에 대한 다양한 이벤트를 볼 수 있습니다. 

![쿼리 Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Reliable Actors 이벤트는 비슷한 방식으로 볼 수 있습니다. Reliable Actors에 대한 자세한 이벤트를 구성하려면 진단 확장(아래 참조)에 대한 구성에서 `scheduledTransferKeywordFilter`를 변경해야 합니다. 이러한 값에 대한 세부 정보는 [Reliable Actors 이벤트 참조](service-fabric-reliable-actors-diagnostics.md#keywords)를 참조하세요.

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

Kusto 쿼리 언어는 강력합니다. 실행 가능한 또 다른 중요한 쿼리는 가장 많은 이벤트를 생성하는 노드를 확인하는 것입니다. 아래 스크린샷의 쿼리는 특정 서비스 및 노드와 통합된 Service Fabric 운영 이벤트를 보여 줍니다.

![노드당 쿼리 이벤트](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>다음 단계

* 인프라 모니터링, 즉 성능 카운터를 사용하려면 [Log Analytics 에이전트 추가](service-fabric-diagnostics-oms-agent.md)를 참조하세요. 에이전트는 성능 카운터를 수집하여 기존 작업 영역에 추가합니다.
* 온-프레미스 클러스터의 경우 Azure Monitor 로그는 Azure Monitor 로그에 데이터를 전송 하는 데 사용할 수 있는 게이트웨이 (HTTP 전달 프록시)를 제공 합니다. 자세한 내용은 [인터넷에 연결 되지 않은 컴퓨터를 Log Analytics 게이트웨이를 사용 하 여 Azure Monitor 로그에 연결](../azure-monitor/platform/gateway.md)하는 방법을 참조 하세요.
* 감지 및 진단에 도움이 되는 [자동 경고](../azure-monitor/platform/alerts-overview.md)를 구성합니다.
* Azure Monitor 로그의 일부로 제공되는 [로그 검색 및 쿼리](../azure-monitor/log-query/log-query-overview.md) 기능을 알아봅니다.
* Azure Monitor 로그 및 제공 되는 내용에 대 한 자세한 개요를 확인 하 고 [Azure Monitor 로그 란?](../azure-monitor/overview.md)을 참조 하세요.
