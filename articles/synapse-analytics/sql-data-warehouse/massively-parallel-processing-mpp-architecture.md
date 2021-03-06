---
title: 전용 SQL 풀 (이전의 SQL DW) 아키텍처
description: Azure Synapse Analytics의 전용 SQL 풀 (이전의 SQL DW)에서 분산 쿼리 처리 기능을 Azure Storage와 결합 하 여 고성능 및 확장성을 구현 하는 방법에 대해 알아봅니다.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 45c7f89f773095a102429c07f7441223de3c2dec
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448257"
---
# <a name="dedicated-sql-pool-formerly-sql-dw-architecture-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 전용 SQL 풀 (이전의 SQL DW) 아키텍처

Azure Synapse Analytics는 엔터프라이즈 데이터 웨어하우징과 빅 데이터 분석을 결합한 분석 서비스입니다. 이를 통해 사용자의 용어로 데이터를 자유롭게 쿼리할 수 있습니다.

> [!NOTE]
>[Azure Synapse Analytics 설명서](../overview-what-is.md)를 탐색 합니다.
>


> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="synapse-sql-architecture-components"></a>Synapse SQL 아키텍처 구성 요소

[전용 sql 풀 (이전의 SQL DW)](sql-data-warehouse-overview-what-is.md) 은 확장 아키텍처를 활용 하 여 여러 노드에 걸친 데이터의 계산 처리를 분산 합니다. 규모 단위는 [데이터 웨어하우스 단위](what-is-a-data-warehouse-unit-dwu-cdwu.md)로 알려진 컴퓨팅 능력의 추상화입니다. 시스템 데이터와 독립적으로 컴퓨팅을 확장할 수 있도록 컴퓨팅이 스토리지에서 분리됩니다.

![전용 SQL 풀 (이전의 SQL DW) 아키텍처](./media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

전용 SQL 풀 (이전의 SQL DW)은 노드 기반 아키텍처를 사용 합니다. 응용 프로그램은 T-sql 명령을 제어 노드에 연결 하 고이를 실행 합니다. 제어 노드는 병렬 처리를 위한 쿼리를 최적화 하 고 작업을 병렬 작업을 수행 하기 위해 계산 노드에 전달 하는 분산 쿼리 엔진을 호스팅합니다.

컴퓨팅 노드는 모든 사용자 데이터를 Azure Storage에 저장하고 병렬 쿼리를 실행합니다. DMS(데이터 이동 서비스)는 쿼리를 병렬로 실행하고 정확한 결과를 반환하기 위해 필요할 때 노드에서 데이터를 이동시키는 시스템 수준의 내부 서비스입니다.

분리 된 저장소 및 계산을 사용 하 여 전용 SQL 풀 (이전의 SQL DW)을 사용할 경우 다음을 수행할 수 있습니다.

- 스토리지 요구 사항에 관계없이 컴퓨팅 능력을 독립적으로 조정합니다.
- 데이터를 이동 하지 않고 전용 SQL 풀 (이전의 SQL DW) 내에서 계산 능력을 확장 하거나 축소 합니다.
- 데이터를 그대로 둔 채 컴퓨팅 용량을 일시 중지하여 스토리지 비용만 지불합니다.
- 운영 시간 동안 컴퓨팅 용량을 다시 시작합니다.

### <a name="azure-storage"></a>Azure Storage

전용 SQL 풀 SQL (이전의 SQL DW)은 Azure Storage를 활용 하 여 사용자 데이터를 안전 하 게 유지 합니다.  데이터가 Azure Storage에 의해 저장되고 관리되므로 스토리지 사용에 대한 별도 요금이 부과됩니다. 데이터는 시스템의 성능을 최적화하기 위해 **분산** 으로 분할됩니다. 테이블을 정의할 때 데이터 분산에 사용할 분할 패턴을 선택할 수 있습니다. 다음과 같은 분할 패턴이 지원됩니다.

- Hash
- 라운드 로빈
- 복제

### <a name="control-node"></a>제어 노드

제어 노드는 아키텍처의 두뇌입니다. 모든 애플리케이션 및 연결과 상호 작용하는 프런트 엔드입니다. 분산 쿼리 엔진은 제어 노드에서 실행 되어 병렬 쿼리를 최적화 하 고 조정 합니다. T-SQL 쿼리를 제출하면 제어 노드는 이것을 각 분산에 대해 병렬로 실행되는 쿼리로 변환합니다.

### <a name="compute-nodes"></a>컴퓨팅 노드

컴퓨팅 노드는 컴퓨팅 능력을 제공합니다. 분산은 처리를 위해 컴퓨팅 노드로 매핑됩니다. 더 많은 컴퓨팅 리소스에 대한 비용을 지불하는 경우 사용 가능한 컴퓨팅 노드에 분산이 다시 매핑됩니다. 컴퓨팅 노드 수는 1~60 사이이며 Synapse SQL의 서비스 수준에 따라 결정됩니다.

각 컴퓨팅 노드에는 시스템 뷰에 표시되는 노드 ID가 있습니다. 시스템 뷰에서 이름이 sys.pdw_nodes로 시작하는 node_id 열을 검색하여 Compute 노드 ID를 볼 수 있습니다. 이러한 시스템 뷰 목록은 [SYNAPSE SQL system views](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)를 참조 하세요.

### <a name="data-movement-service"></a>데이터 이동 서비스

DMS(데이터 이동 서비스)는 컴퓨팅 노드 간의 데이터 이동을 조정하는 데이터 전송 기술입니다. 일부 쿼리는 병렬 쿼리가 정확한 결과를 반환하도록 하려면 데이터 이동이 필요합니다. 데이터 이동이 필요할 때 DMS는 올바른 데이터가 올바른 위치에 도달하도록 보장합니다.

## <a name="distributions"></a>배포

분산은 분산 데이터에 실행되는 병렬 쿼리의 스토리지 및 처리의 기본 단위입니다. Synapse SQL이 쿼리를 실행하면 병렬로 실행되는 60개의 작은 쿼리로 작업이 나뉩니다.

60개의 작은 쿼리는 각각 데이터 분산 중 하나에서 실행됩니다. 각 컴퓨팅 노드는 60개 중 하나 이상의 분산을 관리합니다. 최대 계산 리소스를 포함 하는 전용 SQL 풀 (이전의 SQL DW)에는 Compute 노드당 하나의 분포가 있습니다. 최소 계산 리소스를 포함 하는 전용 SQL 풀 (이전의 SQL DW)에는 하나의 계산 노드에 있는 모든 배포가 있습니다.  

## <a name="hash-distributed-tables"></a>해시 분산 테이블

해시 분산 테이블은 대형 테이블의 조인 및 집계에 대해 가장 높은 쿼리 성능을 제공할 수 있습니다.

데이터를 해시 분산 테이블로 분할하기 위해 해시 함수를 사용하여 각 행을 결정적으로 하나의 분산에 할당합니다. 테이블 정의에서 열 중 하나는 분산 열로 지정됩니다. 해시 함수는 분산 열의 값을 사용하여 각 행을 분산에 할당합니다.

다음은 전체(분산되지 않은 테이블)가 해시 분산 테이블로 저장되는 방식을 보여 주는 다이어그램입니다.

![분산 테이블](./media/massively-parallel-processing-mpp-architecture/hash-distributed-table.png "분산 테이블")  

- 각 행은 하나의 분산에 속합니다.  
- 결정적 해시 알고리즘은 각 행을 하나의 분산에 할당합니다.  
- 분산당 테이블 행의 수는 테이블 크기에 따라 달라집니다.

분산 열을 선택할 때 고유성, 데이터 기울이기, 시스템에서 실행되는 쿼리 종류 등 성능에 대해 고려할 사항이 있습니다.

## <a name="round-robin-distributed-tables"></a>라운드 로빈 분산 테이블

라운드 로빈 테이블은 만들기 가장 간단한 테이블이며 로드를 위한 준비 테이블로 사용될 때 빠른 성능을 제공합니다.

라운드 로빈 분산 테이블은 데이터를 테이블 전체에 고르게 분산하지만 최적화는 하지 않습니다. 먼저 분산이 무작위로 선택되고 행 버퍼가 순차적으로 분산에 할당됩니다. 데이터는 로드는 라운드 로빈 테이블이 빠르지만 쿼리 성능은 해시 분산 테이블이 더 뛰어난 경우가 많습니다. 라운드 로빈 테이블의 조인에는 데이터 다시 섞기가 필요하며 여기에는 추가 시간이 소요됩니다.

## <a name="replicated-tables"></a>복제된 테이블

복제된 테이블은 작은 테이블에 가장 빠른 쿼리 성능을 제공합니다.

복제된 테이블은 각 컴퓨팅 노드에 테이블의 전체 복사본을 캐시합니다. 결과적으로 테이블을 복제하면 조인 또는 집계 전에 컴퓨팅 노드 간에 데이터를 전송하지 않아도 됩니다. 복제된 테이블은 작은 테이블에서 가장 잘 활용됩니다. 추가 스토리지가 필요하며, 데이터를 쓸 때 발생하는 추가 오버 헤드로 인해 대용량 테이블에는 비실용적입니다.  

아래 다이어그램은 각 컴퓨팅 노드의 첫 번째 분산에 캐시된 복제 테이블을 보여 줍니다.  

![복제 테이블](./media/massively-parallel-processing-mpp-architecture/replicated-table.png "복제 테이블")

## <a name="next-steps"></a>다음 단계

Azure Synapse에 대 한 자세한 내용을 배웠으므로 이제 [전용 sql 풀 (이전의 SQL DW)](create-data-warehouse-portal.md) 을 빠르게 만들고 [샘플 데이터를 로드](load-data-from-azure-blob-storage-using-polybase.md)하는 방법을 알아보세요. Azure을 처음 접하는 경우 새 용어를 발견하면 [Azure 용어집](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 을 유용하게 사용할 수 있습니다. 또는 다음과 같은 기타 Azure Synapse 리소스를 살펴봅니다.  

- [고객 성공 사례](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [블로그](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [기능 요청](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [비디오](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [지원 티켓 만들기](sql-data-warehouse-get-started-create-support-ticket.md)
- [Microsoft Q&A 질문 페이지](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)
- [Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
