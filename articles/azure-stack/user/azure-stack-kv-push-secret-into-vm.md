---
title: "Implementar uma máquina virtual com um certificado armazenado em segurança na pilha do Azure | Microsoft Docs"
description: "Saiba como implementar uma máquina virtual e emitir um certificado no mesmo, utilizando um cofre de chaves na pilha do Azure"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/03/2017
ms.author: sngun
ms.openlocfilehash: 29ccdc9eca9911b2f550f9e09da83d0b1d30f9db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-and-include-certificate-retrieved-from-a-key-vault"></a>Criar uma máquina virtual e incluir o certificado obtido a partir de um cofre de chaves

Este artigo ajuda-o a criar uma máquina virtual na pilha do Azure e certificados de emissão no mesmo. 

## <a name="prerequisites"></a>Pré-requisitos

* Tem tem de subscrever uma oferta, que inclui o serviço Cofre de chaves. 
* [Instale o PowerShell para a pilha do Azure.](azure-stack-powershell-install.md)  
* [Configurar o Azure pilha ambiente do utilizador do PowerShell](azure-stack-powershell-configure-user.md)

Um cofre de chaves na pilha do Azure é utilizado para armazenar certificados. Os certificados são úteis em vários cenários diferentes. Por exemplo, considere um cenário em que tenha uma máquina virtual na pilha do Azure que está a executar uma aplicação que necessita de um certificado. Este certificado pode ser utilizado para encriptar, para autenticar no Active Directory ou para SSL num Web site. Ter o certificado numa ajuda-o Cofre de chaves, certifique-se de que é seguro.

Neste artigo, iremos orientá-lo pelos passos necessários para emitir um certificado para uma máquina virtual do Windows na pilha do Azure. Pode utilizar estes passos do Kit de desenvolvimento de pilha do Azure ou de um cliente externo baseados em Windows se estiver ligado através de VPN.

Os passos seguintes descrevem o processo necessário para emitir um certificado para a máquina virtual:

1. Crie um cofre de chave secreta.
2. Atualize o ficheiro azuredeploy.parameters.json.
3. Implementar o modelo

## <a name="create-a-key-vault-secret"></a>Criar um cofre de chave secreta

O script seguinte cria um certificado no formato. pfx, cria um cofre de chaves e armazena o certificado no Cofre de chaves como um segredo. Tem de utilizar o `-EnabledForDeployment` parâmetro ao criar o Cofre de chaves. Este parâmetro certifica-se de que o Cofre de chaves pode ser referenciado a partir de modelos Azure Resource Manager.

```powershell

# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<Certificate Thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret

```

Quando executar o script anterior, o resultado inclui o URI do segredo. Anote este URI. Tem de referenciá-lo no [: certificado Push inválido para o modelo do Windows Resource Manager](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate). Transferir o [modelo de vm push-certificado windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) pasta no seu computador de desenvolvimento. Esta pasta contém os `azuredeploy.json` e `azuredeploy.parameters.json` ficheiros, que irá precisar nos passos.

Modificar o `azuredeploy.parameters.json` ficheiros, de acordo com os valores de ambiente. Os parâmetros do especial interesse são o nome do cofre, o grupo de recursos do cofre e o segredo do URI (como gerado pelo script anterior). O ficheiro seguinte está um exemplo de um ficheiro de parâmetros:

## <a name="update-the-azuredeployparametersjson-file"></a>Atualizar o ficheiro azuredeploy.parameters.json

Atualize o ficheiro de azuredeploy.parameters.json com o vaultName, URI secreta, VmName e outros valores de acordo com o seu ambiente. O ficheiro JSON seguinte mostra um exemplo do ficheiro de parâmetros de modelo: 

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newStorageAccountName": {
      "value": "kvstorage01"
    },
    "vmName": {
      "value": "VM1"
    },
    "vmSize": {
      "value": "Standard_D1_v2"
    },
    "adminUserName": {
      "value": "demouser"
    },
    "adminPassword": {
      "value": "demouser@123"
    },
    "vaultName": {
      "value": "contosovault"
    },
    "vaultResourceGroup": {
      "value": "contosovaultrg"
    },
    "secretUrlWithVersion": {
      "value": "https://testkv001.vault.local.azurestack.external/secrets/testcert002/82afeeb84f4442329ce06593502e7840"
    }
  }
}
```

## <a name="deploy-the-template"></a>Implementar o modelo

Agora, implemente o modelo utilizando o seguinte script do PowerShell:

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Quando o modelo é implementado com êxito, o que resulta no seguinte resultado:

![Saída de implementação](media/azure-stack-kv-push-secret-into-vm/deployment-output.png)

Quando esta máquina virtual é implementada, a pilha do Azure envia o certificado para a máquina virtual. No Windows, o certificado foi adicionado para a localização do certificado LocalMachine, com o arquivo de certificados que o utilizador forneceu. No Linux, o certificado é colocado sob o diretório de /var/lib/waagent, com o nome de ficheiro &lt;UppercaseThumbprint&gt;. crt para o X509 ficheiro de certificado e &lt;UppercaseThumbprint&gt;.prv para a chave privada .

## <a name="retire-certificates"></a>Extinguir os certificados

Na secção anterior, iremos mostrou como emitir um novo certificado para uma máquina virtual. O certificado antigo é ainda na máquina virtual e este não pode ser removido. No entanto, pode desativar a versão mais antiga do segredo do utilizando o `Set-AzureKeyVaultSecretAttribute` cmdlet. Segue-se um exemplo de utilização deste cmdlet. Certifique-se de que substitui o nome do cofre, nome secreto e valores de versão, de acordo com seu ambiente:

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>Passos seguintes

* [Implementar uma VM com uma palavra-passe do Cofre de Chaves](azure-stack-kv-deploy-vm-with-secret.md)
* [Permitir que uma aplicação aceder ao Cofre de chaves](azure-stack-kv-sample-app.md)


