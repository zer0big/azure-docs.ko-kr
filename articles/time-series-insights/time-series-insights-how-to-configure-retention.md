---
title: 환경에서 보존을 구성 하는 방법-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights 환경에서 보존을 구성 하는 방법에 대해 알아봅니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 468b4f7ca7b0af4abc32df5d9ef64a74154d3de1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91569400"
---
# <a name="configuring-retention-in-azure-time-series-insights-gen1"></a>Azure Time Series Insights Gen1에서 보존 구성

> [!CAUTION]
> 이는 Gen1 문서입니다.

이 아티클에서는 Azure Time Series Insights의 **데이터 보존 시간** 및 **스토리지 제한을 초과하는 동작**을 구성하는 방법을 설명합니다.

## <a name="summary"></a>요약

각 Azure Time Series Insights 환경에는 **데이터 보존 시간**을 구성 하는 설정이 있습니다. 값의 범위는 1~400일입니다. 데이터는 환경 스토리지 용량 또는 보존 기간(1-400) 중 더 빠른 시간을 기준으로 삭제됩니다.

각 Azure Time Series Insights 환경에는 **저장소 제한을 초과**하는 추가 설정 동작이 있습니다. 이 설정은 환경의 최대 용량에 도달하면 수신 및 삭제 동작을 제어합니다. 두 가지 동작 중에서 선택할 수 있습니다.

- **이전 데이터 삭제**(기본값)
- **수신 일시 중지**

이러한 설정을 보다 잘 이해 하는 방법에 대 한 자세한 내용은 [Azure Time Series Insights 보존 이해](time-series-insights-concepts-retention.md)를 참조 하세요.  

## <a name="configure-data-retention"></a>데이터 보존 구성하기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 기존 Azure Time Series Insights 환경을 찾습니다. Azure Portal의 왼쪽에 있는 메뉴에서 **모든 리소스**를 선택합니다. Azure Time Series Insights 환경을 선택 합니다.

1. **설정** 제목에서 **저장소 구성**을 선택 합니다.

    [![설정 아래에서 저장소 구성을 선택 합니다.](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

1. 슬라이더 막대를 사용 하 여 보존을 구성 하거나 텍스트 상자에 숫자를 입력 하 여 **데이터 보존 시간 (일)** 을 선택 합니다.

1. **용량** 설정은 데이터 이벤트의 최대 양과 데이터 스토리지에 사용되는 총 스토리지 용량에 영향을 주므로 이 구성을 참고하세요.

1. **스토리지 제한을 초과하는 동작** 설정을 전환합니다. **이전 데이터 삭제** 또는 **수신 일시 중지** 동작을 선택합니다.

    [![수신 일시 중지-수락 및 저장.](media/data-retention/pause-ingress-accept-and-save.png)](media/data-retention/pause-ingress-accept-and-save.png#lightbox)

1. 문서를 검토 하 여 잠재적인 데이터 손실 위험을 파악 합니다. **저장**을 선택하여 변경 내용을 구성합니다.

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [Azure Time Series Insights 보존 이해](time-series-insights-concepts-retention.md)를 참조 하세요.

- [Azure Time Series Insights 환경의 크기를 조정 하는 방법을](time-series-insights-how-to-scale-your-environment.md)알아봅니다.

- [환경 계획](time-series-insights-environment-planning.md)에 대해 알아봅니다.
