---
title: Ligar a pilha do Azure com a CLI | Microsoft Docs
description: Saiba como utilizar a interface de linha de comandos (CLI) de plataforma para gerir e implementar os recursos na pilha do Azure
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: sngun
ms.openlocfilehash: 5ef64e727615d17ae550efbc7ea427936d7d4c3b
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="install-and-configure-cli-for-use-with-azure-stack"></a>Instalar e configurar a CLI para utilização com a pilha do Azure

Neste artigo, vamos ajudá-lo durante o processo de utilizar a interface de linha de comandos do Azure (CLI) para gerir os recursos do Azure pilha Development Kit do Linux e plataformas de cliente Mac. 

## <a name="export-the-azure-stack-ca-root-certificate"></a>Exportar o certificado de raiz de AC de pilha do Azure

Se estiver a utilizar o CLI de uma máquina virtual que está a executar o ambiente de trabalho do Kit de desenvolvimento de pilha do Azure, o certificado de raiz de pilha do Azure já está instalado na máquina virtual, para que possa obtê-los diretamente. Se utilizar a CLI de uma estação de trabalho fora do kit de desenvolvimento, tem de exportar o certificado de raiz de AC de pilha do Azure do kit de desenvolvimento e adicioná-lo ao arquivo de certificados Python da sua estação de trabalho de desenvolvimento (plataforma externa de Linux ou Mac). 

Para exportar o certificado de raiz de pilha do Azure no formato PEM, inicie sessão no kit de desenvolvimento e execute o seguinte script:

```powershell
   $label = "AzureStackSelfSignedRootCert"
   Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
   $root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
   if (-not $root)
   {
       Log-Error "Certificate with subject CN=$label not found"
       return
   }

   Write-Host "Exporting certificate"
   Export-Certificate -Type CERT -FilePath root.cer -Cert $root

   Write-Host "Converting certificate to PEM format"
   certutil -encode root.cer root.pem
```

## <a name="install-cli"></a>Instalar CLI

Em seguida, inicie sessão na estação de trabalho de desenvolvimento e instalar a CLI. Pilha do Azure requer a versão 2.0 do CLI do Azure. Pode instalar que utilizando os passos descritos no [instalar o Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) artigo. Para verificar se a instalação foi concluída com êxito, abra um terminal ou uma janela de linha de comandos e execute o seguinte comando:

```azurecli
az --version
```

Deverá ver a versão da CLI do Azure e outras bibliotecas dependentes que estão instaladas no seu computador.

## <a name="trust-the-azure-stack-ca-root-certificate"></a>Confiar no certificado de raiz de AC de pilha do Azure

Para confiar no certificado de raiz de AC de pilha do Azure, anexe o certificado existente do Python. Se estiver a executar o CLI de uma máquina de Linux que é criada no ambiente de pilha do Azure, execute o seguinte comando bash:

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

Se estiver a executar o CLI de uma máquina fora do ambiente do Azure Sack, tem primeiro de configurar [conectividade VPN do Azure pilha](azure-stack-connect-azure-stack.md). Agora copie o certificado PEM que exportou anteriormente para a estação de trabalho de desenvolvimento e execute os comandos seguintes, dependendo do SO do seu desenvolvimento estação de trabalho.

### <a name="linux"></a>Linux

```bash
sudo cat PATH_TO_PEM_FILE >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

### <a name="macos"></a>macOS

```bash
sudo cat PATH_TO_PEM_FILE >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

### <a name="windows"></a>Windows

```powershell
$pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

$root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$root.Import($pemFile)

Write-Host "Extracting needed information from the cert file"
$md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
$sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
$sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

$issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
$subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
$labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
$serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
$md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
$sha1Entry    = [string]::Format("# SHA1 Finterprint: {0}", $sha1Hash)
$sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
$certText = (Get-Content -Path root.pem -Raw).ToString().Replace("`r`n","`n")

$rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
$serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

Write-Host "Adding the certificate content to Python Cert store"
Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

Write-Host "Python Cert store was updated for allowing the azure stack CA root certificate"
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Configurar o ponto final aliases de máquina virtual

Antes dos utilizadores podem criar máquinas virtuais ao utilizar a CLI, o administrador da nuvem deve configurar um ponto final acessível publicamente que contém os aliases de imagem de máquina virtual e registe este ponto final com a nuvem. O `endpoint-vm-image-alias-doc` parâmetro o `az cloud register` comando é utilizado para esta finalidade. Os administradores de nuvem tem de transferir a imagem para o mercado de pilha do Azure antes de poderem adicioná-lo para o ponto final de aliases de imagem.
   
Por exemplo, o Azure utiliza o URI seguinte: https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json. O administrador da nuvem deve configurar um ponto final semelhante para pilha do Azure com as imagens que estão disponíveis no mercado pilha do Azure.

## <a name="connect-to-azure-stack"></a>Ligar ao Azure Stack

Utilize os seguintes passos para ligar a pilha do Azure:

1. Registar o seu ambiente de pilha do Azure executando o `az cloud register` comando.
   
   a. Para registar o *nuvem administrativa* ambiente, utilize:

      ```azurecli
      az cloud register \ 
        -n AzureStackAdmin \ 
        --endpoint-resource-manager "https://adminmanagement.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".adminvault.local.azurestack.external" \ 
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" \
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

   b. Para registar o *utilizador* ambiente, utilize:

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" \
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

2. Defina o ambiente do Active Directory utilizando os seguintes comandos.

   a. Para o *nuvem administrativa* ambiente, utilize:

      ```azurecli
      az cloud set \
        -n AzureStackAdmin
      ```

   b. Para o *utilizador* ambiente, utilize:

      ```azurecli
      az cloud set \
        -n AzureStackUser
      ```

3. Atualize a configuração do ambiente para utilizar o perfil de versão de API específico da pilha do Azure. Para atualizar a configuração, execute o seguinte comando:

   ```azurecli
   az cloud update \
     --profile 2017-03-09-profile
   ```

4. Inicie sessão no seu ambiente de pilha do Azure utilizando o `az login` comando. Pode iniciar sessão para o ambiente de pilha do Azure como um utilizador ou como um [principal de serviço](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-application-objects). 

   * Inicie sessão como um *utilizador*: pode especificar o nome de utilizador e palavra-passe diretamente dentro do `az login` comando ou autenticar utilizando um browser. Tem de fazer a última opção se a sua conta tem de multi-factor authentication ativada.

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

      > [!NOTE]
      > Se a sua conta de utilizador tiver a autenticação multifator ativada, pode utilizar o `az login command` sem fornecer o `-u` parâmetro. Executar o comando dá-lhe um URL e um código que tem de utilizar a autenticação.
   
   * Inicie sessão como um *principal de serviço*: antes de iniciar sessão, [criar um principal de serviço através do portal do Azure](azure-stack-create-service-principals.md) ou a CLI e atribua-lhe uma função. Agora, inicie sessão com o seguinte comando:

      ```azurecli
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```

## <a name="test-the-connectivity"></a>Testar a conectividade

Agora que já obtivemos tudo o programa de configuração, vamos utilizar a CLI para criar recursos na pilha do Azure. Por exemplo, pode criar um grupo de recursos para uma aplicação e adicionar uma máquina virtual. Utilize o seguinte comando para criar um grupo de recursos com o nome "MyResourceGroup":

```azurecli
az group create \
  -n MyResourceGroup -l local
```

Se o grupo de recursos é criado com êxito, o comando anterior produz as seguintes propriedades do recurso recentemente criado:

![Saída de criar grupo de recursos](media/azure-stack-connect-cli/image1.png)

## <a name="next-steps"></a>Passos seguintes

[Implementar modelos com a CLI do Azure](azure-stack-deploy-template-command-line.md)

[Gerir permissões de utilizador](azure-stack-manage-permissions.md)

