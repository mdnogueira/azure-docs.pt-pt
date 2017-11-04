---
title: "Implementar uma VM com palavra-passe armazenada em segurança na pilha do Azure | Microsoft Docs"
description: Saiba como implementar uma VM com uma palavra-passe armazenada no Cofre de chaves de pilha do Azure
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 23322a49-fb7e-4dc2-8d0e-43de8cd41f80
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/08/2017
ms.author: sngun
ms.openlocfilehash: 3292a2dfefc17e5034c66122a3eab24d6c03e694
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-by-retrieving-the-password-stored-in-a-key-vault"></a>Criar uma máquina virtual ao obter a palavra-passe armazenada no Cofre de chaves

Quando precisar de transferir um valor seguro como uma palavra-passe durante a implementação, pode armazenar esse valor como um segredo no Cofre de chaves pilha do Azure e referencie-o nos modelos do Azure Resource Manager. Efetue a não tem de introduzir manualmente o segredo sempre que implementar os recursos, também pode especificar quais os utilizadores ou principais de serviço podem aceder ao segredo. 

Neste artigo, iremos orientá-lo pelos passos necessários para implementar uma máquina virtual do Windows na pilha do Azure ao obter a palavra-passe armazenados no Cofre de chaves. Por conseguinte, a palavra-passe nunca é colocada em texto simples no ficheiro de parâmetro de modelo. Pode utilizar estes passos do Kit de desenvolvimento de pilha do Azure ou de um cliente externo se estiver ligado através de VPN.

## <a name="prerequisites"></a>Pré-requisitos
 
* Tem tem de subscrever uma oferta, que inclui o serviço Cofre de chaves.  
* [Instale o PowerShell para a pilha do Azure.](azure-stack-powershell-install.md)  
* [Configure o Azure pilha ambiente do utilizador do PowerShell.](azure-stack-powershell-configure-user.md)

Os passos seguintes descrevem o processo necessário para criar uma máquina virtual ao obter a palavra-passe armazenada no Cofre de chaves:

1. Crie um cofre de chave secreta.
2. Atualize o ficheiro azuredeploy.parameters.json.
3. Implemente o modelo.

## <a name="create-a-key-vault-secret"></a>Criar um cofre de chave secreta

O script seguinte cria um cofre de chaves e armazena uma palavra-passe no Cofre de chaves como um segredo. Utilize o `-EnabledForDeployment` parâmetro ao criar o Cofre de chaves. Este parâmetro certifica-se de que o Cofre de chaves pode ser referenciado a partir de modelos Azure Resource Manager.

```powershell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location
  -EnabledForTemplateDeployment

$secretValue = ConvertTo-SecureString -String '<Password for your virtual machine>' -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
  -SecretValue $secretValue

```

Quando executar o script anterior, o resultado inclui o URI do segredo. Anote este URI. Tem de referenciá-lo no [máquina virtual de implementar o Windows com palavras-passe no modelo do Cofre de chaves](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv). Transferir o [101-vm--palavra-passe segura](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) pasta no seu computador de desenvolvimento. Esta pasta contém os `azuredeploy.json` e `azuredeploy.parameters.json` ficheiros, que irá precisar nos passos.

Modificar o `azuredeploy.parameters.json` ficheiros, de acordo com os valores de ambiente. Os parâmetros do especial interesse são o nome do cofre, o grupo de recursos do cofre e o segredo do URI (como gerado pelo script anterior). O ficheiro seguinte está um exemplo de um ficheiro de parâmetros:

## <a name="update-the-azuredeployparametersjson-file"></a>Atualizar o ficheiro azuredeploy.parameters.json

Atualize o ficheiro de azuredeploy.parameters.json com o URI de KeyVault, secretName, adminUsername dos valores da máquina virtual, de acordo com o seu ambiente. O ficheiro JSON seguinte mostra um exemplo do ficheiro de parâmetros de modelo: 

```json
{
    "$schema":  "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":  "1.0.0.0",
    "parameters":  {
       "adminUsername":  {
         "value":  "demouser"
          },
         "adminPassword":  {
           "reference":  {
              "keyVault":  {
                "id":  "/subscriptions/xxxxxx/resourceGroups/RgKvPwd/providers/Microsoft.KeyVault/vaults/KvPwd"
                },
              "secretName":  "MySecret"
           }
         },
       "dnsLabelPrefix":  {
          "value":  "mydns123456"
        },
        "windowsOSVersion":  {
          "value":  "2016-Datacenter"
        }
    }
}

```

## <a name="template-deployment"></a>Implementação de modelos

Agora, implemente o modelo utilizando o seguinte script do PowerShell:

```powershell
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```
Quando o modelo é implementado com êxito, o que resulta no seguinte resultado:

![Saída de implementação](media/azure-stack-kv-deploy-vm-with-secret/deployment-output.png)


## <a name="next-steps"></a>Passos seguintes
[Implementar uma aplicação de exemplo com o Cofre de chaves](azure-stack-kv-sample-app.md)

[Implementar uma VM com um certificado do Cofre de chaves](azure-stack-kv-push-secret-into-vm.md)

