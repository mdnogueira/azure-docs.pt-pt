---
title: Criar um cluster do Service Fabric do Azure a partir de um modelo | Microsoft Docs
description: "Este artigo descreve como configurar um cluster do Service Fabric seguro no Azure utilizando o Azure Resource Manager, o Cofre de chaves do Azure e o Azure Active Directory (Azure AD) para autenticação de cliente."
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: chackdan
ms.openlocfilehash: 47152d05eb7e31e7fe1f35e33a10fe8e903e21e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>Criar um cluster do Service Fabric com o Azure Resource Manager
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Portal do Azure](service-fabric-cluster-creation-via-portal.md)
>
>

Este guia passo a passo explica como configurar um cluster do Azure Service Fabric seguro no Azure utilizando o Gestor de recursos do Azure. Iremos confirmar que o artigo é longo. Contudo, a menos que já estejam exaustivamente familiarizado com o conteúdo, lembre-se de que seguir os passos cuidadosamente.

O guia abrange os procedimentos seguintes:

* Configurar um cofre de chaves do Azure para carregar certificados para a segurança do cluster e da aplicação
* Criar um cluster protegido no Azure utilizando o Gestor de recursos do Azure
* Autenticação de utilizadores utilizando o Azure Active Directory (Azure AD) para gestão de clusters

Um cluster seguro é um cluster que impede o acesso não autorizado às operações de gestão. Isto inclui a implementar, atualizar e eliminar os dados que contêm, serviços e aplicações. Um cluster não seguro é um cluster que qualquer pessoa pode ligar a qualquer momento e efetuar operações de gestão. Embora seja possível criar um cluster não seguro, recomendamos vivamente que crie um cluster seguro proposto. Devido um cluster não seguro não pode ser protegido mais tarde, tem de ser criado um novo cluster.

O conceito de criação de clusters seguros é os mesmos, quer sejam Linux ou clusters do Windows. Para mais informações e do programa auxiliar de scripts para criar clusters Linux seguros consulte [criação de clusters seguros no Linux](#secure-linux-clusters).

## <a name="sign-in-to-your-azure-account"></a>Inicie sessão na sua conta do Azure
Este guia utiliza [Azure PowerShell][azure-powershell]. Quando inicia uma nova sessão do PowerShell, inicie sessão na sua conta do Azure e selecionar a sua subscrição antes de executar os comandos do Azure.

Inicie sessão sua conta do Azure:

```powershell
Login-AzureRmAccount
```

Selecione a sua subscrição:

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-a-key-vault"></a>Configurar um cofre de chaves
Esta secção explica como criar um cofre de chaves para um cluster do Service Fabric no Azure e para aplicações de Service Fabric. Para obter um guia completo, ao Cofre de chaves do Azure, consulte o [Cofre de chaves guia de introdução][key-vault-get-started].

O Service Fabric utiliza certificados x. 509 para proteger um cluster e fornecer funcionalidades de segurança da aplicação. Utilizar o Cofre de chaves para gerir os certificados para clusters de Service Fabric no Azure. Quando um cluster é implementado no Azure, o fornecedor de recursos do Azure que é responsável pela criação de clusters de Service Fabric obtém certificados a partir do Cofre de chaves e instala-los em VMs do cluster.

O diagrama seguinte ilustra a relação entre o Cofre de chaves do Azure, um cluster do Service Fabric e o fornecedor de recursos do Azure que utiliza certificados armazenados no Cofre de chaves quando cria um cluster:

![Diagrama de instalação do certificado][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
O primeiro passo é criar um grupo de recursos especificamente para o seu Cofre de chaves. Recomendamos que colocar o Cofre de chaves no seu próprio grupo de recursos. Esta ação permite-lhe remover os grupos de recursos de armazenamento e computação, incluindo o grupo de recursos que contém o cluster do Service Fabric, sem perder as chaves e segredos. O grupo de recursos que contém o seu Cofre de chaves _tem de estar na mesma região_ como o cluster que está a ser utilizado.

Se planeia implementar clusters em várias regiões, sugerimos que é o nome do grupo de recursos e a chave do Cofre de uma forma que indica a que região pertence a.  

```powershell

    New-AzureRmResourceGroup -Name westus-mykeyvault -Location 'West US'
```
A saída deve ter o seguinte aspeto:

```powershell

    WARNING: The output object type of this cmdlet is going to be modified in a future release.

    ResourceGroupName : westus-mykeyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/westus-mykeyvault

```
<a id="new-key-vault"></a>

### <a name="create-a-key-vault-in-the-new-resource-group"></a>Criar um cofre de chaves no novo grupo de recursos
O Cofre de chaves _tem de estar ativado para a implementação_ para permitir que o fornecedor de recursos de computação obter certificados a partir do mesmo e instalá-lo em instâncias de máquina virtual:

```powershell

    New-AzureRmKeyVault -VaultName 'mywestusvault' -ResourceGroupName 'westus-mykeyvault' -Location 'West US' -EnabledForDeployment

```

A saída deve ter o seguinte aspeto:

```powershell

    Vault Name                       : mywestusvault
    Resource Group Name              : westus-mykeyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/westus-mykeyvault/providers/Microsoft.KeyVault/vaults/mywestusvault
    Vault URI                        : https://mywestusvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all


    Tags                             :
```
<a id="existing-key-vault"></a>

## <a name="use-an-existing-key-vault"></a>Utilize um cofre de chaves

Para utilizar um cofre de chaves, _necessário ativá-la para implementação_ para permitir que o fornecedor de recursos de computação obter certificados a partir do mesmo e a instalar em nós de cluster:

```powershell

Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

```

<a id="add-certificate-to-key-vault"></a>

## <a name="add-certificates-to-your-key-vault"></a>Adicione certificados à seu Cofre de chaves

Os certificados são utilizados no Service Fabric para fornecer autenticação e encriptação para proteger diferentes aspetos de um cluster e as respetivas aplicações. Para obter mais informações sobre como os certificados são utilizados no Service Fabric, consulte [cenários de segurança de cluster do Service Fabric][service-fabric-cluster-security].

### <a name="cluster-and-server-certificate-required"></a>Cluster e servidor de certificado (obrigatório)
Este certificado é necessário para proteger um cluster e impedir o acesso não autorizado ao mesmo. Fornece segurança de cluster de duas formas:

* Autenticação do cluster: autentica o nó de nó de comunicação para a Federação de cluster. Apenas nós que podem provar a sua identidade com este certificado podem aderirem ao cluster.
* Autenticação de servidor: autentica pontos finais de gestão de cluster para um cliente de gestão, para que o cliente de gestão sabe o que é falar com o cluster real. Este certificado fornece também um SSL para a API de gestão HTTPS e para o Service Fabric Explorer através de HTTPS.

Para efetuar estes fins, o certificado tem de cumprir os seguintes requisitos:

* O certificado tem de conter uma chave privada.
* O certificado tem de ser criado para a troca de chaves, que é exportável para um ficheiro Personal Information Exchange (. pfx).
* Nome do requerente do certificado deve corresponder ao domínio que utilizar para aceder ao cluster do Service Fabric. Esta correspondência é necessário fornecer um SSL para o cluster pontos finais de gestão HTTPS e Service Fabric Explorer. Não é possível obter um certificado SSL de uma autoridade de certificação (AC) para o. cloudapp.azure.com domínio. Tem de obter um nome de domínio personalizado para o cluster. Quando solicitar um certificado a partir de uma AC, o nome de requerente do certificado tem de corresponder ao nome de domínio personalizado que utilizar para o cluster.

### <a name="application-certificates-optional"></a>Certificados de aplicação (opcionais)
Qualquer número de certificados adicionais pode ser instalado num cluster por motivos de segurança da aplicação. Antes de criar o cluster, considere os cenários de segurança de aplicações que necessitam de um certificado para ser instalada em nós, tais como:

* A encriptação e desencriptação de valores de configuração de aplicação.
* Encriptação dos dados em nós durante a replicação.

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Formatação certificados para utilização do fornecedor de recursos do Azure
Pode adicionar e utilizar privados ficheiros de chave (. pfx) diretamente através do seu Cofre de chaves. No entanto, o fornecedor de recursos de computação do Azure requer que sejam armazenadas num formato JavaScript Object Notation (JSON) especial chaves. O formato inclui o ficheiro. pfx como uma cadeia com codificação 64 base e a palavra-passe da chave privada. Para acomodar estes requisitos, as chaves tem de ser colocadas numa cadeia JSON e, em seguida, armazenadas como "segredos" no Cofre de chaves.

Para facilitar este processo, um [módulo do PowerShell está disponível no GitHub][service-fabric-rp-helpers]. Para utilizar o módulo, efetue o seguinte:

1. Transferir todo o conteúdo do repositório para um diretório local.
2. Vá para o diretório local.
2. Importe o módulo de ServiceFabricRPHelpers na janela do PowerShell:

```powershell

 Import-Module "C:\..\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

```

O `Invoke-AddCertToKeyVault` comando neste módulo PowerShell automaticamente formatos uma chave privada do certificado numa cadeia JSON e carrega-o para o Cofre de chaves. Utilize o comando para adicionar o certificado de cluster e quaisquer certificados de aplicação adicional para o Cofre de chaves. Repita este passo para todos os certificados adicionais que pretende instalar no seu cluster.

#### <a name="uploading-an-existing-certificate"></a>Carregar um certificado existente

```powershell

 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName westus-mykeyvault -Location "West US" -VaultName mywestusvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"

```

Se obtiver um erro, tal como a indicada aqui, normalmente, significa que tem um conflito de URL de recurso. Para resolver o conflito, altere o nome do Cofre de chaves.

```
Set-AzureKeyVaultSecret : The remote name could not be resolved: 'westuskv.vault.azure.net'
At C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1:440 char:11
+ $secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $Certif ...
+           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureKeyVaultSecret], WebException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.KeyVault.SetAzureKeyVaultSecret

```

Depois do conflito é resolvido, a saída deve ter o seguinte aspeto:

```

    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup westus-mykeyvault in West US
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    Using existing value mywestusvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to mywestusvault in vault mywestusvault


Name  : CertificateThumbprint
Value : E21DBC64B183B5BF355C34C46E03409FEEAEF58D

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/westus-mykeyvault/providers/Microsoft.KeyVault/vaults/mywestusvault

Name  : CertificateURL
Value : https://mywestusvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

>[!NOTE]
>Terá dos três anterior cadeias, CertificateThumbprint, SourceVault e CertificateURL, configurar um cluster de Service Fabric seguro e obter quaisquer certificados de aplicação que possam estar a utilizar para a segurança da aplicação. Se não guarde as cadeias, pode ser difícil recuperar consultando o Cofre de chaves mais tarde.

<a id="add-self-signed-certificate-to-key-vault"></a>

#### <a name="creating-a-self-signed-certificate-and-uploading-it-to-the-key-vault"></a>Criar um certificado autoassinado e carregá-lo para o Cofre de chaves

Se já tiver carregado os certificados para o Cofre de chaves, ignore este passo. Este passo é para gerar um novo certificado autoassinado e carregá-lo para o seu Cofre de chaves. Depois de alterar os parâmetros no seguinte script e, em seguida, executá-lo, deve ser pedido para uma palavra-passe do certificado.  

```powershell

$ResourceGroup = "chackowestuskv"
$VName = "chackokv2"
$SubID = "6c653126-e4ba-42cd-a1dd-f7bf96ae7a47"
$locationRegion = "westus"
$newCertName = "chackotestcertificate1"
$dnsName = "www.mycluster.westus.mydomain.com" #The certificate's subject name must match the domain used to access the Service Fabric cluster.
$localCertPath = "C:\MyCertificates" # location where you want the .PFX to be stored

 Invoke-AddCertToKeyVault -SubscriptionId $SubID -ResourceGroupName $ResourceGroup -Location $locationRegion -VaultName $VName -CertificateName $newCertName -CreateSelfSignedCertificate -DnsName $dnsName -OutputPath $localCertPath

```

Se obtiver um erro, tal como a indicada aqui, normalmente, significa que tem um conflito de URL de recurso. Para resolver o conflito, altere o nome do Cofre de chaves, RG nome e assim sucessivamente.

```
Set-AzureKeyVaultSecret : The remote name could not be resolved: 'westuskv.vault.azure.net'
At C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1:440 char:11
+ $secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $Certif ...
+           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureKeyVaultSecret], WebException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.KeyVault.SetAzureKeyVaultSecret

```

Depois do conflito é resolvido, a saída deve ter o seguinte aspeto:

```
PS C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers> Invoke-AddCertToKeyVault -SubscriptionId $SubID -ResourceGroupName $ResouceGroup -Location $locationRegion -VaultName $VName -CertificateName $newCertName -Password $certPassword -CreateSelfSignedCertificate -DnsName $dnsName -OutputPath $localCertPath
Switching context to SubscriptionId 6c343126-e4ba-52cd-a1dd-f8bf96ae7a47
Ensuring ResourceGroup chackowestuskv in westus
WARNING: The output object type of this cmdlet will be modified in a future release.
Creating new vault westuskv1 in westus
Creating new self signed certificate at C:\MyCertificates\chackonewcertificate1.pfx
Reading pfx file from C:\MyCertificates\chackonewcertificate1.pfx
Writing secret to chackonewcertificate1 in vault westuskv1


Name  : CertificateThumbprint
Value : 96BB3CC234F9D43C25D4B547sd8DE7B569F413EE

Name  : SourceVault
Value : /subscriptions/6c653126-e4ba-52cd-a1dd-f8bf96ae7a47/resourceGroups/chackowestuskv/providers/Microsoft.KeyVault/vaults/westuskv1

Name  : CertificateURL
Value : https://westuskv1.vault.azure.net:443/secrets/chackonewcertificate1/ee247291e45d405b8c8bbf81782d12bd

```

>[!NOTE]
>Terá dos três anterior cadeias, CertificateThumbprint, SourceVault e CertificateURL, configurar um cluster de Service Fabric seguro e obter quaisquer certificados de aplicação que possam estar a utilizar para a segurança da aplicação. Se não guarde as cadeias, pode ser difícil recuperar consultando o Cofre de chaves mais tarde.

 Nesta fase, deve ter os seguintes elementos no local:

* O grupo de recursos do Cofre de chaves.
* O Cofre de chaves e o respetivo URL (SourceVault chamado no resultado do PowerShell anterior).
* O certificado de autenticação de servidor de cluster e o respetivo URL no Cofre de chaves.
* Os certificados de aplicação e os URLs no Cofre de chaves.


<a id="add-AAD-for-client"></a>

## <a name="set-up-azure-active-directory-for-client-authentication"></a>Configurar o Azure Active Directory para autenticação de cliente

Azure AD permite às organizações (conhecidas como inquilinos) para gerir o acesso de utilizador para aplicações. As aplicações são divididas aqueles com uma conta baseada na web IU de início de sessão e as pessoas com uma experiência de cliente nativo. Neste artigo, partimos do pressuposto que já criou um inquilino. Se não tiver, comece por ler [como obter um inquilino do Azure Active Directory][active-directory-howto-tenant].

Um cluster do Service Fabric oferece vários pontos de entrada para a funcionalidade de gestão, incluindo baseada na web [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] e [Visual Studio][service-fabric-manage-application-in-visual-studio]. Como resultado, crie duas aplicações do Azure AD para controlar o acesso ao cluster, uma aplicação web e uma aplicação nativa.

Para simplificar a alguns dos passos envolvidos na configuração do Azure AD com um cluster do Service Fabric, foi criado um conjunto de scripts do Windows PowerShell.

> [!NOTE]
> Tem de concluir os passos seguintes antes de criar o cluster. Uma vez que os scripts de esperam os nomes de clusters e os pontos finais, os valores deve ser planeadas e não os valores que já criou.

1. [Transfira os scripts] [ sf-aad-ps-script-download] para o seu computador.
2. Clique no ficheiro zip, selecione **propriedades**, selecione o **desbloqueio** caixa de verificação e, em seguida, clique em **aplicar**.
3. Extraia o ficheiro zip.
4. Executar `SetupApplications.ps1`e forneça o TenantId, ClusterName e WebApplicationReplyUrl como parâmetros. Por exemplo:

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    Pode encontrar o TenantId executando o comando do PowerShell `Get-AzureSubscription`. Executar este comando apresenta o TenantId para cada subscrição.

    ClusterName é utilizado para o prefixo de aplicações do Azure AD que são criadas pelo script. Não é necessário o nome do cluster real de corresponder exatamente. Destina-se apenas a tornar mais fácil mapear artefactos do Azure AD para o cluster do Service Fabric que está a ser utilizados com.

    WebApplicationReplyUrl é o ponto final predefinido, que devolve do Azure AD para os seus utilizadores, depois de concluir a iniciar sessão. Defina este ponto final como o ponto final do Service Fabric Explorer para o cluster, o que, por predefinição, está:

    https://&lt;cluster_domain&gt;: 19080/Explorer

    Lhe for pedido para iniciar sessão para uma conta que tenha privilégios administrativos para o inquilino do Azure AD. Depois de iniciar sessão, o script cria o web e aplicações nativas para representar o cluster do Service Fabric. Se observar aplicações do inquilino no [portal clássico do Azure][azure-classic-portal], deverá ver duas novas entradas:

   * *ClusterName*\_Cluster
   * *ClusterName*\_cliente

   O script imprime o JSON exigido pelo modelo Azure Resource Manager, ao criar o cluster na secção seguinte, pelo que é uma boa ideia mantenha a janela do PowerShell aberta.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Criar um modelo de Gestor de recursos de cluster do Service Fabric
Nesta secção, as saídas dos comandos anteriores do PowerShell são utilizadas num modelo de Gestor de recursos de cluster do Service Fabric.

Modelos de Gestor de recursos de exemplo estão disponíveis no [Galeria de modelo de início rápido do Azure no GitHub][azure-quickstart-templates]. Estes modelos, podem ser utilizados como um ponto de partida para o modelo de cluster.

### <a name="create-the-resource-manager-template"></a>Criar o modelo do Resource Manager
Este guia utiliza o [5-nó cluster segura] [ service-fabric-secure-cluster-5-node-1-nodetype] modelo de exemplo e os parâmetros do modelo. Transferir `azuredeploy.json` e `azuredeploy.parameters.json` para o seu computador e abra ambos os ficheiros no seu editor de texto favorito.

### <a name="add-certificates"></a>Adicione certificados
Adicione certificados para um modelo de Gestor de recursos do cluster ao consultar o Cofre de chaves que contém as chaves de certificado. Recomendamos que coloque os valores do Cofre de chaves num ficheiro de parâmetros de modelo do Resource Manager. Se o fizer, mantém o Gestor de recursos ficheiro de modelo reutilizáveis e livre dos valores específicos para uma implementação.

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Adicionar todos os certificados para o osProfile de conjunto de dimensionamento de máquina virtual
Cada certificado que está instalado no cluster deve ser configurado na secção osProfile o recurso de conjunto de dimensionamento (Microsoft.Compute/virtualMachineScaleSets). Esta ação instrui o fornecedor de recursos para instalar o certificado nas VMs. Esta instalação inclui o certificado de cluster e quaisquer certificados de segurança de aplicação que pretende utilizar para as suas aplicações:

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-the-service-fabric-cluster-certificate"></a>Configurar o certificado de cluster do Service Fabric
O certificado de autenticação do cluster tem de ser configurado em ambos os o Service Fabric recurso de cluster (Microsoft.ServiceFabric/clusters) e a extensão de recursos de infraestrutura do serviço de dimensionamento da máquina virtual define no recurso de conjunto de dimensionamento de máquina virtual. Esta disposição permite que o fornecedor de recursos do Service Fabric configurá-la para utilização para a autenticação do cluster e a autenticação de servidor para pontos finais de gestão.

##### <a name="virtual-machine-scale-set-resource"></a>Recurso de conjunto de dimensionamento de máquina virtual:
```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="service-fabric-resource"></a>Recurso de infraestrutura de serviço:
```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="insert-azure-ad-configuration"></a>Inserir a configuração do Azure AD
A configuração do Azure AD que criou anteriormente pode ser inserida diretamente no seu modelo do Resource Manager. No entanto, recomendamos que primeiro extrair os valores para um ficheiro de parâmetros para manter o Gestor de recursos do modelo reutilizáveis e gratuitos dos valores específicos para uma implementação.

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### < um "Configurar-braço" ></a>parâmetros de modelo do Resource Manager configurar
<!--- Loc Comment: It seems that <a "configure-arm" > must be replaced with <a name="configure-arm"></a> since the link seems not to be redirecting correctly --->
Por fim, utilize os valores de saída do Cofre de chaves e os comandos do Azure AD PowerShell para povoar o ficheiro de parâmetros:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```
Nesta fase, deve ter os seguintes elementos no local:

* Grupo de recursos do Cofre de chaves
  * Key Vault
  * Certificado de autenticação de servidor de cluster
  * Certificado de encriptação de dados
* Inquilino do Azure Active Directory
  * Aplicação Azure AD para a gestão baseada na web e o Service Fabric Explorer
  * Aplicação Azure AD para a gestão de cliente nativo
  * Os utilizadores e as respetivas funções atribuídas
* Modelo de Gestor de recursos de cluster do Service Fabric
  * Certificados configurados por meio do Cofre de chaves
  * Azure Active Directory configurado

O diagrama seguinte ilustra onde ajustam o seu Cofre de chaves e a configuração do AD do Azure ao seu modelo do Resource Manager.

![Mapa de dependência do Gestor de recursos][cluster-security-arm-dependency-map]

## <a name="create-the-cluster"></a>Criar o cluster
Agora, está pronto para criar o cluster utilizando [implementação do modelo de recursos do Azure][resource-group-template-deploy].

#### <a name="test-it"></a>Testá-lo
Utilize o seguinte comando do PowerShell para testar o modelo do Resource Manager com um ficheiro de parâmetros:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### <a name="deploy-it"></a>Implementá-la
Se passar o teste de modelo do Resource Manager, utilize o seguinte comando do PowerShell para implementar o modelo do Resource Manager com um ficheiro de parâmetros:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Atribuir utilizadores a funções
Depois de criar as aplicações para representar o cluster, atribuir os seus utilizadores a funções suportadas pelo Service Fabric: só de leitura e administrador. Pode atribuir as funções utilizando o [portal clássico do Azure][azure-classic-portal].

1. No portal do Azure, aceda ao seu inquilino e, em seguida, selecione **aplicações**.
2. Selecione a aplicação web, que tem um nome como `myTestCluster_Cluster`.
3. Clique em de **utilizadores** separador.
4. Selecione um utilizador e, em seguida, clique em de **atribuir** na parte inferior do ecrã.

    ![Atribuir utilizadores a botão de funções][assign-users-to-roles-button]
5. Selecione a função para atribuir ao utilizador.

    ![Caixa de diálogo "Atribuir utilizadores"][assign-users-to-roles-dialog]

> [!NOTE]
> Para obter mais informações sobre as funções no Service Fabric, consulte [controlo de acesso baseado em funções para clientes de Service Fabric](service-fabric-cluster-security-roles.md).
>
>

 <a name="secure-linux-clusters"></a>
 <!--- Loc Comment: It seems that letter S in cluster was missing, which caused the wrong redirection of the link --->

## <a name="create-secure-clusters-on-linux"></a>Criar clusters seguros no Linux
Para facilitar o processo, fornecemos uma [script de programa auxiliar](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux). Antes de utilizar este script de programa auxiliar, certifique-se de que já tem interface de linha de comandos do Azure (CLI) instalado e é no seu caminho. Certifique-se de que o script tem permissões para executar executando `chmod +x cert_helper.py` após transferindo-a. O primeiro passo é para iniciar sessão sua conta do Azure ao utilizar a CLI com o `azure login` comando. Após o início de sessão na sua conta do Azure, utilize o script de programa auxiliar com o certificado assinado pela AC, tal como mostra o seguinte comando:

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]
```

O parâmetro - ifile pode demorar um ficheiro. pfx ou um ficheiro. pem como entrada, com o tipo de certificado (pfx ou pem ou ss se se tratar de um certificado autoassinado).
O parâmetro -h imprime terminar o texto de ajuda.


Este comando devolve as cadeias de três seguintes como resultado:

* SourceVaultID, que é o ID para o novo KeyVault ResourceGroup-criado para si
* CertificateUrl para aceder ao certificado
* CertificateThumbprint, que é utilizado para autenticação

O exemplo seguinte mostra como utilizar o comando:

```sh
./cert_helper.py pfx -sub "fffffff-ffff-ffff-ffff-ffffffffffff"  -rgname "mykvrg" -kv "mykevname" -ifile "/home/test/cert.pfx" -sname "mycert" -l "East US" -p "pfxtest"
```
Executar o comando anterior dá-lhe as cadeias de três da seguinte forma:

```sh
SourceVault: /subscriptions/fffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/mykvrg/providers/Microsoft.KeyVault/vaults/mykvname
CertificateUrl: https://myvault.vault.azure.net/secrets/mycert/00000000000000000000000000000000
CertificateThumbprint: 0xfffffffffffffffffffffffffffffffffffffffff
```

Nome do requerente do certificado deve corresponder ao domínio que utilizar para aceder ao cluster do Service Fabric. Esta correspondência é obrigada a fornecer um SSL para o cluster pontos finais de gestão HTTPS e Service Fabric Explorer. Não é possível obter um certificado SSL a partir de uma AC para o `.cloudapp.azure.com` domínio. Tem de obter um nome de domínio personalizado para o cluster. Quando solicitar um certificado a partir de uma AC, o nome de requerente do certificado tem de corresponder ao nome de domínio personalizado que utilizar para o cluster.

Estes nomes de requerente são entradas tem de criar um cluster do Service Fabric seguro (do Azure AD), conforme descrito em [parâmetros de modelo do Resource Manager configurar](#configure-arm). Pode ligar ao cluster seguro ao seguir as instruções para [autenticar o acesso de cliente para um cluster](service-fabric-connect-to-secure-cluster.md). Clusters do Linux não suportam a autenticação do Azure AD. Pode atribuir funções de administrador e cliente conforme descrito no [atribuir funções aos utilizadores](#assign-roles) secção. Quando especificar as funções de administrador e o cliente para um cluster do Linux, tem de fornecer thumbprints de certificado para autenticação. Não forneça o nome do requerente, porque não está a ser efetuada nenhuma validação da cadeia ou revogação.

Se pretender utilizar um certificado autoassinado para fins de teste, pode utilizar o mesmo script para gerar um. Pode, em seguida, carregue o certificado no seu Cofre de chaves, fornecendo o sinalizador `ss` em vez de fornecer o caminho do certificado e o nome do certificado. Por exemplo, consulte o seguinte comando para criar e carregar um certificado autoassinado:

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net"
```
Este comando devolve as cadeias de três mesmas: SourceVault, CertificateUrl e CertificateThumbprint. Em seguida, pode utilizar as cadeias para criar um cluster com Linux seguro e uma localização onde o certificado autoassinado está colocado. É necessário o certificado autoassinado para ligar ao cluster. Pode ligar ao cluster seguro ao seguir as instruções para [autenticar o acesso de cliente para um cluster](service-fabric-connect-to-secure-cluster.md).

Nome do requerente do certificado deve corresponder ao domínio que utilizar para aceder ao cluster do Service Fabric. Esta correspondência é obrigada a fornecer um SSL para o cluster pontos finais de gestão HTTPS e Service Fabric Explorer. Não é possível obter um certificado SSL a partir de uma AC para o `.cloudapp.azure.com` domínio. Tem de obter um nome de domínio personalizado para o cluster. Quando solicitar um certificado a partir de uma AC, o nome de requerente do certificado tem de corresponder ao nome de domínio personalizado que utilizar para o cluster.

Pode preencher os parâmetros do script do programa auxiliar no portal do Azure, conforme descrito no [criar um cluster no portal do Azure](service-fabric-cluster-creation-via-portal.md#create-cluster-in-the-azure-portal) secção.

## <a name="next-steps"></a>Passos seguintes
Neste momento, tiver um cluster seguro com fornecer autenticação de gestão do Azure Active Directory. Em seguida, [ligar ao cluster](service-fabric-connect-to-secure-cluster.md) e saber como [gerir segredos de aplicação](service-fabric-application-secret-management.md).

## <a name="troubleshoot-setting-up-azure-active-directory-for-client-authentication"></a>Resolver problemas de definição de cópia de segurança do Azure Active Directory para autenticação de cliente
Caso se depare com um problema enquanto estiver a configurar do Azure AD para autenticação de cliente, reveja as possíveis soluções nesta secção.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer pede-lhe para selecionar um certificado
#### <a name="problem"></a>Problema
Depois de iniciar sessão com êxito para o Azure AD no Service Fabric Explorer, o browser devolve à home page, mas uma mensagem pede-lhe para selecionar um certificado.

![Caixa de diálogo do SFX selecione o certificado][sfx-select-certificate-dialog]

#### <a name="reason"></a>Razão
O utilizador não está atribuído uma função na aplicação de cluster do Azure AD. Assim, a autenticação do Azure AD falha no cluster do Service Fabric. Service Fabric Explorer retrocede para autenticação de certificados.

#### <a name="solution"></a>Solução
Siga as instruções para configurar o Azure AD e atribuir funções de utilizador. Além disso, recomendamos que ative "Atribuição do utilizador necessária para aceder à aplicação," como `SetupApplications.ps1` does.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Falha da ligação com o PowerShell com um erro: "as credenciais especificadas são inválidas"
#### <a name="problem"></a>Problema
Quando utilizar o PowerShell para ligar ao cluster utilizando o modo de segurança "AzureActiveDirectory", depois de iniciar sessão com êxito para o Azure AD, a ligação falhar com um erro: "as credenciais especificadas são inválidas."

#### <a name="solution"></a>Solução
Esta solução é idêntica à anterior.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer devolve uma falha ao iniciar sessão: "AADSTS50011"
#### <a name="problem"></a>Problema
Ao tentar iniciar sessão Azure AD no Service Fabric Explorer, a página devolve uma falha: "AADSTS50011: O endereço de resposta &lt;url&gt; não coincide com os endereços de resposta configurados para a aplicação: &lt;guid&gt;."

![Não corresponde ao endereço de resposta SFX][sfx-reply-address-not-match]

#### <a name="reason"></a>Razão
A aplicação de cluster (web) que representa o Service Fabric Explorer tenta a autenticação no Azure AD e como parte do pedido fornece o URL de retorno de redirecionamento. Mas o URL não está listado na aplicação do Azure AD **URL de resposta** lista.

#### <a name="solution"></a>Solução
No **configurar** separador da aplicação (web) do cluster, adicione o URL do Service Fabric Explorer para o **URL de resposta** lista ou substituir um dos itens na lista. Quando tiver terminado, guarde as alterações.

![Url de resposta de aplicação Web][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Ligue o cluster utilizando a autenticação do Azure AD através do PowerShell
Para ligar o cluster do Service Fabric, utilize o seguinte exemplo de comando do PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Para saber mais sobre o cmdlet do Connect-ServiceFabricCluster, consulte [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Pode reutilizar o mesmo inquilino do Azure AD em vários clusters?
Sim. Mas não se esqueça de adicionar o URL do Service Fabric Explorer à sua aplicação de cluster (web). Caso contrário, o Service Fabric Explorer não funciona.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Por que motivo é ainda necessário um certificado de servidor ao Azure AD está ativado?
FabricClient e FabricGateway executará uma autenticação mútua. Durante a autenticação do Azure AD, a integração do Azure AD fornece uma identidade do cliente para o servidor e o certificado de servidor é utilizado para verificar a identidade do servidor. Para obter mais informações sobre certificados de Service Fabric, consulte [certificados x. 509 e o Service Fabric][x509-certificates-and-service-fabric].

<!-- Links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png

