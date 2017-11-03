---
title: Cluster HPC Pack 2016 no Azure | Microsoft Docs
description: Saiba como implementar um cluster HPC Pack 2016 no Azure
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3dde6a68-e4a6-4054-8b67-d6a90fdc5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 12/15/2016
ms.author: danlep
ms.openlocfilehash: 88d1f4e29f38ba1a6bef57c2da43bee205575eee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-an-hpc-pack-2016-cluster-in-azure"></a>Implementar um cluster HPC Pack 2016 no Azure

Siga os passos neste artigo para implementar um [Microsoft HPC Pack 2016](https://technet.microsoft.com/library/cc514029) cluster em máquinas virtuais do Azure. Pacote HPC é solução HPC gratuita da Microsoft incorporada no Microsoft Azure e o Windows Server tecnologias e suporta um cargas de trabalho de intervalo HPC wide.

Utilize um do [modelos Azure Resource Manager](https://github.com/MsHpcPack/HPCPack2016) para implementar o cluster HPC Pack 2016. Existem várias opções de topologia de cluster com números diferentes de nós principais do cluster e com o Linux ou nós de computação do Windows.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="pfx-certificate"></a>Certificado PFX

Um cluster do Microsoft HPC Pack 2016 requer um certificado de Personal Information (Exchange PFX) para proteger a comunicação entre os nós HPC. O certificado tem de cumprir os seguintes requisitos:

* Tem de ter uma chave privada com capacidade de troca de chaves
* A utilização de chaves inclui a assinatura Digital e cifragem
* Utilização de chave avançada incluem autenticação de cliente e a autenticação de servidor

Se ainda não tiver um certificado que cumpra estes requisitos, pode pedir o certificado de uma autoridade de certificação. Em alternativa, pode utilizar os seguintes comandos para gerar o certificado autoassinado com base no sistema operativo em que executa o comando e exportar o certificado de formato PFX com chave privada.

* **Para Windows 10 ou Windows Server 2016**, execute o incorporado **New-SelfSignedCertificate** cmdlet do PowerShell da seguinte forma:

  ```PowerShell
  New-SelfSignedCertificate -Subject "CN=HPC Pack 2016 Communication" -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2") -CertStoreLocation cert:\CurrentUser\My -KeyExportPolicy Exportable -NotAfter (Get-Date).AddYears(5)
  ```
* **Para sistemas operativos anteriores ao Windows 10 ou Windows Server 2016**, transfira o [gerador do certificado autoassinado](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) do Microsoft Script Center. Extraia os conteúdos e execute os seguintes comandos numa linha de PowerShell:

    ```PowerShell 
    Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
  
    New-SelfSignedCertificateEx -Subject "CN=HPC Pack 2016 Communication" -KeySpec Exchange -KeyUsage "DigitalSignature,KeyEncipherment" -EnhancedKeyUsage "Server Authentication","Client Authentication" -StoreLocation CurrentUser -Exportable -NotAfter (Get-Date).AddYears(5)
    ```

### <a name="upload-certificate-to-an-azure-key-vault"></a>Carregar o certificado para um cofre de chaves do Azure

Antes de implementar o cluster HPC, carregue o certificado para um [Cofre de chaves do Azure](../../key-vault/index.md) como um segredo e as seguintes informações para utilização durante a implementação do registo: **nome do cofre**, **grupo de recursos do cofre**, **URL de certificado**, e **thumbprint do certificado**.

Segue um script do PowerShell de exemplo para carregar o certificado. Para obter mais informações sobre como carregar um certificado para um cofre de chaves do Azure, consulte [introdução ao Cofre de chaves do Azure](../../key-vault/key-vault-get-started.md).

```powershell
#Give the following values
$VaultName = "mytestvault"
$SecretName = "hpcpfxcert"
$VaultRG = "myresourcegroup"
$location = "westus"
$PfxFile = "c:\Temp\mytest.pfx"
$Password = "yourpfxkeyprotectionpassword"
#Validate the pfx file
try {
    $pfxCert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $PfxFile, $Password
}
catch [System.Management.Automation.MethodInvocationException]
{
    throw $_.Exception.InnerException
}
$thumbprint = $pfxCert.Thumbprint
$pfxCert.Dispose()
# Create and encode the JSON object
$pfxContentBytes = Get-Content $PfxFile -Encoding Byte
$pfxContentEncoded = [System.Convert]::ToBase64String($pfxContentBytes)
$jsonObject = @"
{
"data": "$pfxContentEncoded",
"dataType": "pfx",
"password": "$Password"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
#Create an Azure key vault and upload the certificate as a secret
$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
$rg = Get-AzureRmResourceGroup -Name $VaultRG -Location $location -ErrorAction SilentlyContinue
if($null -eq $rg)
{
    $rg = New-AzureRmResourceGroup -Name $VaultRG -Location $location
}
$hpcKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultRG -Location $location -EnabledForDeployment -EnabledForTemplateDeployment
$hpcSecret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secret
"The following Information will be used in the deployment template"
"Vault Name             :   $VaultName"
"Vault Resource Group   :   $VaultRG"
"Certificate URL        :   $($hpcSecret.Id)"
"Certificate Thumbprint :   $thumbprint"

```


## <a name="supported-topologies"></a>Topologias suportadas

Escolha uma do [modelos Azure Resource Manager](https://github.com/MsHpcPack/HPCPack2016) para implementar o cluster HPC Pack 2016. Seguem-se arquiteturas de alto nível das três topologias de cluster suportadas. Topologias de elevada disponibilidade incluem vários nós principais do cluster.

1. Cluster de elevada disponibilidade com o domínio do Active Directory

    ![Cluster do HA no domínio do AD](./media/hpcpack-2016-cluster/haad.png)



2. Cluster de elevada disponibilidade sem o domínio do Active Directory

    ![Cluster do HA sem domínio AD](./media/hpcpack-2016-cluster/hanoad.png)

3. Cluster com um único nó principal

   ![Cluster com o único nó principal](./media/hpcpack-2016-cluster/singlehn.png)


## <a name="deploy-a-cluster"></a>Implementar um cluster

Para criar o cluster, escolha um modelo e clique em **implementar no Azure**. No [portal do Azure](https://portal.azure.com), especifique os parâmetros para o modelo, tal como descrito nos passos seguintes. Cada modelo cria todos os recursos do Azure necessários para a infraestrutura de cluster HPC. Recursos incluem uma Azure virtual network, pública IP endereços, o Balanceador de carga (apenas para um cluster de elevada disponibilidade), interfaces de rede, conjuntos de disponibilidade, contas de armazenamento e máquinas virtuais.

### <a name="step-1-select-the-subscription-location-and-resource-group"></a>Passo 1: Selecione a subscrição, a localização e o grupo de recursos

O **subscrição** e **localização** tem de ser o mesmo que especificou quando carregou o certificado do PFX (consulte pré-requisitos). Recomendamos que crie um **grupo de recursos** para a implementação.

### <a name="step-2-specify-the-parameter-settings"></a>Passo 2: Especificar as definições de parâmetro

Introduza ou altere os valores para os parâmetros do modelo. Clique no ícone junto a cada um dos parâmetros de informações de ajuda. Consulte a documentação de orientação para também [tamanhos VM disponíveis](sizes.md).

Especifique os valores que registou nos pré-requisitos para os seguintes parâmetros: **nome do cofre**, **grupo de recursos do cofre**, **URL de certificado**, e **thumbprint do certificado**.

### <a name="step-3-review-legal-terms-and-create"></a>Passo 3. Reveja os termos legais e criar
Clique em **consultar termos legais** para rever os termos de licenciamento. Se concordar, clique em **Compra**e, em seguida, clique em **criar** para iniciar a implementação.

## <a name="connect-to-the-cluster"></a>Ligar ao cluster
1. Depois do cluster HPC Pack é implementado, avance para o [portal do Azure](https://portal.azure.com). Clique em **grupos de recursos**e localizar o grupo de recursos no qual o cluster foi implementado. Pode encontrar as máquinas virtuais do nó principal.

    ![Nós principais do cluster no portal](./media/hpcpack-2016-cluster/clusterhns.png)

2. Clique num nó principal (num cluster de elevada disponibilidade, clique em qualquer um de nós principais). No **Essentials**, pode encontrar o endereço IP público ou o nome DNS completo do cluster.

    ![Definições de ligação de cluster](./media/hpcpack-2016-cluster/clusterconnect.png)

3. Clique em **Connect** para iniciar sessão no qualquer um de nós principais através de ambiente de trabalho remoto com o seu nome de utilizador de administrador especificadas. Se o cluster é implementado num domínio do Active Directory, o nome de utilizador tem o formato <privateDomainName> \<adminUsername > (por exemplo, hpc.local\hpcadmin).

## <a name="next-steps"></a>Passos seguintes
* Submeta tarefas ao cluster. Consulte [cluster submeter as tarefas HPC um pacote HPC no Azure](hpcpack-cluster-submit-jobs.md) e [gerir um cluster HPC Pack 2016 no Azure utilizando o Azure Active Directory](hpcpack-cluster-active-directory.md).

