---
title: "Exigir a transferência segura no armazenamento do Azure | Microsoft Docs"
description: "Saiba mais sobre a funcionalidade \"Seguro transferência necessária\" para o Storage do Azure e como ativá-la."
services: storage
documentationcenter: na
author: fhryo-msft
manager: Jason.Hogg
editor: fhryo-msft
ms.assetid: 
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/20/2017
ms.author: fryu
ms.openlocfilehash: 1bb87cf3e37e486f9a03da43df652442c19fd218
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="require-secure-transfer-in-azure-storage"></a>Exigir a transferência segura no armazenamento do Azure

A opção "Seguro transferência necessária" reforça a segurança da sua conta de armazenamento, permitindo apenas a pedidos para a conta de ligações seguras. Por exemplo, quando está a chamar as APIs REST para aceder à sua conta de armazenamento, tem de ligar através de HTTPS. "Seguro transferência necessária" rejeita pedidos que utilizam HTTP.

Quando utiliza o serviço de ficheiros do Azure, qualquer ligação sem encriptação falhar quando "Seguro transferência necessária" está ativada. Isto inclui cenários que utilizam SMB 2.1, SMB 3.0 sem encriptação e algumas versões do cliente Linux SMB. 

Por predefinição, a opção "Seguro transferência necessária" está desativada.

> [!NOTE]
> Porque o armazenamento do Azure não suporta HTTPS para nomes de domínio personalizado, esta opção não está aplicada quando estiver a utilizar um nome de domínio personalizado. E contas de armazenamento clássicas não são suportadas.

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Ativar a opção "Seguro transferência necessária" no portal do Azure

Pode ativar a "transferência segura necessária" definição quando criar uma conta de armazenamento na [portal do Azure](https://portal.azure.com). Também pode ativá-la para contas do storage existentes.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Exigir a transferência segura para uma nova conta de armazenamento

1. Abra o **criar conta de armazenamento** painel no portal do Azure.
1. Em **Secure transferência necessária**, selecione **ativado**.

  ![Criar Painel de conta de armazenamento](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Exigir a transferência segura para uma conta de armazenamento existente

1. Selecione uma conta de armazenamento existente no portal do Azure.
1. O armazenamento conta em Painel de menu, **definições**, selecione **configuração**.
1. Em **Secure transferência necessária**, selecione **ativado**.

  ![Painel de menu de conta de armazenamento](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>Ativar "Seguro transferência necessária" através de programação

Para exigir a transferência segura através de programação, use a definição _supportsHttpsTrafficOnly_ nas propriedades da conta de armazenamento com a REST API, ferramentas ou bibliotecas:

* [REST API](https://docs.microsoft.com/en-us/rest/api/storagerp/storageaccounts) (versão: 2016-12-01)
* [PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.storage/set-azurermstorageaccount?view=azurermps-4.1.0) (versão: 4.1.0)
* [CLI](https://pypi.python.org/pypi/azure-cli-storage/2.0.11) (versão: 2.0.11)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/) (versão: 1.1.0)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview) (versão: 6.3.0)
* [Python SDK](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0) (versão: 1.1.0)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage) (versão: 0.11.0)

### <a name="enable-secure-transfer-required-setting-with-powershell"></a>Ativar "Transferência segura necessária" definição com o PowerShell

Este exemplo requer o Azure PowerShell versão do módulo 4.1 ou posterior. Executar ` Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps)(Instalar o módulo do Azure PowerShell).

Executar `Login-AzureRmAccount` para criar uma ligação com o Azure.

 Utilize a seguinte linha de comandos para verificar a definição:

```powershell
> Get-AzureRmStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Utilize a seguinte linha de comando para ativar a definição:

```powershell
> Set-AzureRmStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

### <a name="enable-secure-transfer-required-setting-with-cli"></a>Ativar "Transferência segura necessária" definição com a CLI

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Utilize a seguinte linha de comandos para verificar a definição:

```azurecli-interactive
> az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Utilize a seguinte linha de comando para ativar a definição:

```azurecli-interactive
> az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Passos seguintes
Storage do Azure fornece um conjunto completo de capacidades de segurança, que em conjunto permitem aos programadores criarem aplicações seguras. Para obter mais detalhes, visite o [manual de segurança de armazenamento](storage-security-guide.md).
