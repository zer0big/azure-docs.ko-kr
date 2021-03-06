---
title: Azure Disk Encryption을 위한 키 자격 증명 모음 만들기 및 구성
description: 이 문서에서는 Windows VM에서 Azure Disk Encryption 사용할 키 자격 증명 모음을 만들고 구성 하는 단계를 제공 합니다.
ms.service: virtual-machines
ms.subservice: security
ms.topic: how-to
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 9d9d3d8456e0623ea3f1ef17c5f9f7acb28d0ecd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015379"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Azure Disk Encryption을 위한 키 자격 증명 모음 만들기 및 구성

Azure Disk Encryption은 Azure Key Vault를 사용하여 키 디스크 암호화 키와 비밀을 제어하고 관리합니다.  키 자격 증명 모음에 대한 자세한 내용은 [Azure Key Vault 시작](../../key-vault/general/overview.md) 및 [키 자격 증명 모음 보안](../../key-vault/general/secure-your-key-vault.md)을 참조하세요. 

> [!WARNING]
> - 이전에 VM을 암호화하기 위해 Azure AD에서 Azure Disk Encryption을 사용한 적이 있다면 VM을 암호화하는 데 이 옵션을 계속 사용해야 합니다. 자세한 내용은 [Azure AD를 사용하여 Azure Disk Encryption을 위한 키 자격 증명 모음 만들기 및 구성](disk-encryption-key-vault-aad.md)을 참조하세요.

Azure Disk Encryption을 위한 키 자격 증명 모음 만들기 및 구성은 다음과 같이 세 단계로 이루어져 있습니다.

> [!Note]
> Azure Key Vault 액세스 정책 설정에서 옵션을 선택 하 여 볼륨 암호화를 위한 Azure Disk Encryption에 대 한 액세스를 사용 하도록 설정 해야 합니다. 키 자격 증명 모음에서 방화벽을 사용 하도록 설정한 경우 주요 자격 증명 모음에서 네트워킹 탭으로 이동 하 여 Microsoft 신뢰할 수 있는 서비스에 대 한 액세스를 사용 하도록 설정 해야 합니다. 

1. 필요한 경우 리소스 그룹 만들기.
2. 키 자격 증명 모음 만들기. 
3. 키 자격 증명 모음 고급 액세스 정책 설정.

이러한 단계는 다음 빠른 시작에 설명되어 있습니다.

- [Azure CLI를 사용하여 Windows VM 만들기 및 암호화](disk-encryption-cli-quickstart.md)
- [Azure PowerShell을 사용하여 Windows VM 만들기 및 암호화](disk-encryption-powershell-quickstart.md)

원할 경우 KEK(키 암호화 키)를 생성하거나 가져올 수도 있습니다.

> [!Note]
> 이 문서의 단계는 [Azure Disk Encryption 필수 구성 요소 CLI 스크립트](https://github.com/ejarvi/ade-cli-getting-started) 및 [Azure Disk Encryption 필수 구성 요소 PowerShell 스크립트](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)에서 자동화됩니다.

## <a name="install-tools-and-connect-to-azure"></a>도구 설치 및 Azure에 연결

이 문서의 단계는 [Azure CLI](/cli/azure/), [Azure PowerShell Az 모듈](/powershell/azure/) 또는 [Azure Portal](https://portal.azure.com)을 사용하여 완료할 수 있습니다.

브라우저를 통해 포털에 액세스할 수 있지만 Azure CLI 및 Azure PowerShell에는 로컬 설치가 필요합니다. 자세한 내용은 [Windows용 Azure Disk Encryption: 도구 설치](disk-encryption-windows.md#install-tools-and-connect-to-azure)를 참조하세요.

### <a name="connect-to-your-azure-account"></a>Azure 계정에 연결

Azure CLI 또는 Azure PowerShell을 사용하기 전에 먼저 Azure 구독에 연결해야 합니다. 메시지가 표시되면 [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [Azure PowerShell로 로그인](/powershell/azure/authenticate-azureps?view=azps-2.5.0) 또는 자격 증명을 Azure Portal에 제공합니다.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>다음 단계

- [Azure Disk Encryption 필수 구성 요소 CLI 스크립트](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption 필수 조건 PowerShell 스크립트](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Windows VM에 대한 Azure Disk Encryption 시나리오](disk-encryption-windows.md) 알아보기
- [Azure Disk Encryption 문제 해결](disk-encryption-troubleshooting.md) 방법 알아보기
- [Azure Disk Encryption 샘플 스크립트](disk-encryption-sample-scripts.md) 읽기
