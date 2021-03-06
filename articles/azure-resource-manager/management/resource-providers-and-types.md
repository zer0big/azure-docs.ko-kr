---
title: 리소스 공급자 및 리소스 종류
description: Azure Resource Manager를 지 원하는 리소스 공급자에 대해 설명 합니다. 해당 스키마, 사용 가능한 API 버전 및 리소스를 호스팅할 수 있는 지역을 설명 합니다.
ms.topic: conceptual
ms.date: 12/04/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6d114fdfae12dd9ee96a23e4dafc3847c6429d0c
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745119"
---
# <a name="azure-resource-providers-and-types"></a>Azure 리소스 공급자 및 종류

리소스를 배포할 때는 리소스 공급자 및 형식에 대한 정보를 자주 검색하게 됩니다. 예를 들어 키와 암호를 저장하려는 경우 Microsoft.KeyVault 리소스 공급자로 작업합니다. 이 리소스 공급자는 키 자격 증명 모음을 만드는 데 자격 증명 모음이라는 리소스 유형을 제공합니다.

리소스 종류의 이름은 **{resource-provider}/{resource-type}** 양식입니다. 키 자격 증명 모음의 리소스 유형은 **Microsoft.KeyVault/vaults** 입니다.

이 문서에서는 다음 방법을 설명합니다.

* Azure의 모든 리소스 공급자 보기
* 리소스 공급자의 등록 상태 확인
* 리소스 공급자 등록
* 리소스 공급자에 대한 리소스 종류 보기
* 리소스 종류에 대한 유효한 위치 보기
* 리소스 종류에 대한 유효한 API 버전 보기

Azure Portal, Azure PowerShell 또는 Azure CLI를 통해 이러한 단계를 수행할 수 있습니다.

리소스 공급자를 Azure 서비스로 매핑하는 목록은 [Azure 서비스용 리소스 공급자](azure-services-resource-providers.md)를 참조하세요.

## <a name="register-resource-provider"></a>리소스 공급자 등록

리소스 공급자를 사용 하기 전에 리소스 공급자에 대 한 Azure 구독을 등록 해야 합니다. 등록은 리소스 공급자와 함께 작동 하도록 구독을 구성 합니다. 일부 리소스 공급자는 기본적으로 등록 됩니다. 다른 리소스 공급자는 특정 작업을 수행할 때 자동으로 등록 됩니다. 예를 들어 포털을 통해 리소스를 만드는 경우 리소스 공급자가 일반적으로 등록 됩니다. 다른 시나리오의 경우 리소스 공급자를 수동으로 등록 해야 할 수 있습니다. 기본적으로 등록 된 리소스 공급자 목록은 [Azure 서비스에 대 한 리소스 공급자](azure-services-resource-providers.md)를 참조 하세요.

이 문서에서는 리소스 공급자의 등록 상태를 확인 하 고 필요에 따라 등록 하는 방법을 보여 줍니다. `/register/action`리소스 공급자에 대 한 작업을 수행할 수 있는 권한이 있어야 합니다. 이 사용 권한은 참가자 및 소유자 역할에 포함 되어 있습니다.

> [!IMPORTANT]
> 사용할 준비가 된 경우에만 리소스 공급자를 등록 합니다. 등록 단계를 통해 구독 내에서 최소의 권한을 유지할 수 있습니다. 악의적인 사용자는 등록 되지 않은 리소스 공급자를 사용할 수 없습니다.

응용 프로그램 코드는 **등록** 상태에 있는 리소스 공급자에 대 한 리소스 생성을 차단 하지 않아야 합니다. 리소스 공급자를 등록 하면 지원 되는 각 지역에 대해 작업이 개별적으로 수행 됩니다. 지역에서 리소스를 만들려면 해당 지역 에서만 등록을 완료 해야 합니다. 등록 상태에서 리소스 공급자를 차단 하지 않으면 응용 프로그램은 모든 지역이 완료 될 때까지 대기 하는 것 보다 훨씬 더 빨리 계속할 수 있습니다.

구독에서 해당 리소스 공급자의 리소스 형식이 여전히 있는 경우 리소스 공급자를 등록 취소할 수 없습니다.

## <a name="azure-portal"></a>Azure portal

### <a name="register-resource-provider"></a>리소스 공급자 등록

모든 리소스 공급자와 구독 등록 상태를 보려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure Portal 메뉴에서 **구독** 을 검색 합니다. 사용 가능한 옵션에서 선택합니다.

   :::image type="content" source="./media/resource-providers-and-types/search-subscriptions.png" alt-text="구독 검색":::

1. 보려는 구독을 선택 합니다.

   :::image type="content" source="./media/resource-providers-and-types/select-subscription.png" alt-text="구독 선택":::

1. 왼쪽 메뉴의 **설정** 에서 **리소스 공급자** 를 선택합니다.

   :::image type="content" source="./media/resource-providers-and-types/select-resource-providers.png" alt-text="리소스 공급자 선택":::

6. 등록할 리소스 공급자를 찾고 **등록** 을 선택 합니다. 구독에서 최소 권한을 유지 하려면 사용할 준비가 된 리소스 공급자만 등록 합니다.

   :::image type="content" source="./media/resource-providers-and-types/register-resource-provider.png" alt-text="리소스 공급자 등록":::

### <a name="view-resource-provider"></a>리소스 공급자 보기

특정 리소스 공급자에 대한 정보를 보려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure Portal 메뉴에서 **모든 서비스** 를 선택합니다.
3. **모든 서비스** 상자에서 **리소스 탐색기** 를 입력한 다음, **리소스 탐색기** 를 선택합니다.

    ![모든 서비스 선택](./media/resource-providers-and-types/select-resource-explorer.png)

4. 오른쪽 화살표를 선택하여 **공급자** 를 확장합니다.

    ![공급자 선택](./media/resource-providers-and-types/select-providers.png)

5. 보려는 리소스 공급자 및 리소스 종류를 펼칩니다.

    ![리소스 종류 선택](./media/resource-providers-and-types/select-resource-type.png)

6. 리소스 관리자는 모든 지역에서 지원되지만 배포한 리소스는 모든 지역에서 지원되지 않을 수 있습니다. 또한 구독에 리소스를 지 원하는 일부 지역을 사용 하지 못하도록 하는 제한 사항이 있을 수 있습니다. 리소스 탐색기에는 리소스 종류에 대한 유효한 위치가 표시됩니다.

    ![위치 표시](./media/resource-providers-and-types/show-locations.png)

7. API 버전은 리소스 공급자가 릴리스하는 REST API 작업의 버전에 해당합니다. 리소스 공급자는 새 기능을 사용하도록 설정할 때 새 버전의 REST API를 릴리스합니다. 리소스 탐색기에는 리소스 종류에 대한 유효한 API 버전이 표시됩니다.

    ![API 버전 표시](./media/resource-providers-and-types/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

Azure의 모든 리소스 공급자와 구독에 대한 등록 상태를 보려면 다음을 사용합니다.

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

반환 결과는 다음과 비슷합니다.

```output
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

구독에 대해 등록 된 모든 리소스 공급자를 보려면 다음을 사용 합니다.

```azurepowershell-interactive
 Get-AzResourceProvider -ListAvailable | Where-Object RegistrationState -eq "Registered" | Select-Object ProviderNamespace, RegistrationState | Sort-Object ProviderNamespace
```

구독에서 최소 권한을 유지 하려면 사용할 준비가 된 리소스 공급자만 등록 합니다. 리소스 공급자를 등록하려면 다음을 사용합니다.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

반환 결과는 다음과 비슷합니다.

```output
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

특정 리소스 공급자에 대한 정보를 보려면 다음을 사용합니다.

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

반환 결과는 다음과 비슷합니다.

```output
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

리소스 공급자에 대한 리소스 종류를 보려면 다음을 사용합니다.

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

반환하는 내용은 다음과 같습니다.

```output
batchAccounts
operations
locations
locations/quotas
```

API 버전은 리소스 공급자가 릴리스하는 REST API 작업의 버전에 해당합니다. 리소스 공급자는 새 기능을 사용하도록 설정할 때 새 버전의 REST API를 릴리스합니다.

리소스 종류의 사용 가능한 API 버전을 가져오려면 다음을 사용합니다.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

반환하는 내용은 다음과 같습니다.

```output
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

리소스 관리자는 모든 지역에서 지원되지만 배포한 리소스는 모든 지역에서 지원되지 않을 수 있습니다. 또한 구독에 리소스를 지 원하는 일부 지역을 사용 하지 못하도록 하는 제한 사항이 있을 수 있습니다.

리소스 종류의 지원되는 위치를 가져오려면 다음을 사용합니다.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

반환하는 내용은 다음과 같습니다.

```output
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Azure CLI

Azure의 모든 리소스 공급자와 구독에 대한 등록 상태를 보려면 다음을 사용합니다.

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

반환 결과는 다음과 비슷합니다.

```output
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

구독에 대해 등록 된 모든 리소스 공급자를 보려면 다음을 사용 합니다.

```azurecli-interactive
az provider list --query "sort_by([?registrationState=='Registered'].{Provider:namespace, Status:registrationState}, &Provider)" --out table
```

구독에서 최소 권한을 유지 하려면 사용할 준비가 된 리소스 공급자만 등록 합니다. 리소스 공급자를 등록하려면 다음을 사용합니다.

```azurecli-interactive
az provider register --namespace Microsoft.Batch
```

등록이 진행 중인 메시지를 반환합니다.

특정 리소스 공급자에 대한 정보를 보려면 다음을 사용합니다.

```azurecli-interactive
az provider show --namespace Microsoft.Batch
```

반환 결과는 다음과 비슷합니다.

```output
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

리소스 공급자에 대한 리소스 종류를 보려면 다음을 사용합니다.

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

반환하는 내용은 다음과 같습니다.

```output
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

API 버전은 리소스 공급자가 릴리스하는 REST API 작업의 버전에 해당합니다. 리소스 공급자는 새 기능을 사용하도록 설정할 때 새 버전의 REST API를 릴리스합니다.

리소스 종류의 사용 가능한 API 버전을 가져오려면 다음을 사용합니다.

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

반환하는 내용은 다음과 같습니다.

```output
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

리소스 관리자는 모든 지역에서 지원되지만 배포한 리소스는 모든 지역에서 지원되지 않을 수 있습니다. 또한 구독에 리소스를 지 원하는 일부 지역을 사용 하지 못하도록 하는 제한 사항이 있을 수 있습니다.

리소스 종류의 지원되는 위치를 가져오려면 다음을 사용합니다.

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

반환하는 내용은 다음과 같습니다.

```output
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>다음 단계

* 리소스 관리자 템플릿을 만드는 방법에 대 한 자세한 내용은 [Azure Resource Manager 템플릿 작성](../templates/template-syntax.md)을 참조 하세요. 
* 리소스 공급자 템플릿 스키마를 보려면 [템플릿 참조](/azure/templates/)를 참조하세요.
* 리소스 공급자를 Azure 서비스로 매핑하는 목록은 [Azure 서비스용 리소스 공급자](azure-services-resource-providers.md)를 참조하세요.
* 리소스 공급자에 대한 작업을 보려면 [Azure REST API](/rest/api/)를 참조하세요.
