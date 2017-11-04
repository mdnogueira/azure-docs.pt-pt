---
title: Configurar o acesso de WinRM para uma VM do Azure | Microsoft Docs
description: "Configure o acesso de WinRM para utilização com uma máquina virtual do Azure criada no modelo de implementação Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 9718e85b-d360-4621-90b8-0b0b84a21208
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/16/2016
ms.author: kasing
ms.openlocfilehash: 2d6533462400bc1d93d0d3b0227769784e2658a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Configurar o acesso de WinRM para máquinas virtuais no Gestor de recursos do Azure
## <a name="winrm-in-azure-service-management-vs-azure-resource-manager"></a>WinRM na gestão de serviço do Azure vs do Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

* Para obter uma descrição geral do Gestor de recursos do Azure, consulte este [artigo](../../azure-resource-manager/resource-group-overview.md)
* Para as diferenças entre a gestão de serviço do Azure e do Azure Resource Manager, consulte este [artigo](../../resource-manager-deployment-model.md)

A principal diferença na configuração do WinRM uma configuração entre as duas pilhas é como o certificado é instalado na VM. Na pilha do Azure Resource Manager, os certificados são modelados como recursos geridos pelo fornecedor de recursos do Cofre de chave. Por conseguinte, o utilizador tem de fornecer os seus próprios certificados e carregá-la para um cofre de chaves antes de o utilizar numa VM.

Eis os passos que precisa de tomar para configurar uma VM com a conectividade do WinRM

1. Criar um Key Vault
2. Criar um certificado autoassinado
3. Carregue o certificado autoassinado para o Cofre de chaves
4. Obter o URL para o certificado autoassinado no Cofre de chaves
5. Referenciar o seu URL de certificados autoassinados durante a criação de uma VM

## <a name="step-1-create-a-key-vault"></a>Passo 1: Criar um cofre de chaves
Pode utilizar o comando para criar o Cofre de chaves abaixo

```
New-AzureRmKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Passo 2: Criar um certificado autoassinado
Pode criar um certificado autoassinado utilizando este script do PowerShell

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Passo 3: Carregue o certificado autoassinado para o Cofre de chaves
Antes de carregar o certificado para o Cofre de chaves criada no passo 1, precisa de convertida para um formato o fornecedor de recursos Microsoft. Compute consigam compreender. O abaixo PowerShell script permitirá, fazê-lo

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Passo 4: Obter o URL para o certificado autoassinado no Cofre de chaves
O fornecedor de recursos Microsoft. Compute tem um URL para o segredo no Cofre de chaves ao aprovisionamento de VM. Isto permite que o fornecedor de recursos Microsoft. Compute transferir o segredo e criar o certificado equivalente na VM.

> [!NOTE]
> O URL do segredo tem de incluir, bem como a versão. Um URL de exemplo aspeto abaixo https://contosovault.vault.azure.net:443/segredos/contososecret/01h9db0df2cd4300a20ence585a6s7ve
> 
> 

#### <a name="templates"></a>Modelos
Pode obter a ligação para o URL em modelo, utilizando o abaixo código

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
Pode obter este URL a utilizar o comando do PowerShell abaixo

    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Passo 5: Referenciar o seu URL de certificados autoassinados durante a criação de uma VM
#### <a name="azure-resource-manager-templates"></a>Modelos Azure Resource Manager
Ao criar uma VM através de modelos, o certificado obtém referenciado na secção de segredos e a secção de winRM, tal como indicado abaixo:

    "osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

Um modelo de exemplo para o procedimento acima pode ser localizado aqui [201 vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Código de origem para este modelo pode ser encontrado no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzureRmVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzureRmKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Passo 6: Ligar à VM
Antes de poder ligar à VM tem de certificar-se a máquina está configurada para gestão remota do WinRM. Inicie o PowerShell como administrador e execute o comando certificar-se abaixo que estiver configurado.

    Enable-PSRemoting -Force

> [!NOTE]
> Poderá ter de certificar-se de que o serviço WinRM está em execução se o procedimento acima não funciona. Pode fazê-lo que utilizar`Get-Service WinRM`
> 
> 

Depois da configuração estiver concluída, pode ligar à VM utilizando o comando abaixo

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
