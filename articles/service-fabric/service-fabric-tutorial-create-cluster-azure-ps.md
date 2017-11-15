---
title: Criar um cluster do Service Fabric no Azure | Microsoft Docs
description: Saiba como criar um cluster do Windows ou Linux Service Fabric no Azure utilizando o PowerShell
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/03/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 217b9f2f0dfed5b095e1bac1c8146abf4753fadc
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="create-a-secure-cluster-in-azure-by-using-powershell"></a>Criar um cluster seguro no Azure utilizando o PowerShell
Este artigo é o primeiro de uma série de tutoriais que mostram como mover uma aplicação .NET para a nuvem utilizando o Azure Service Fabric clusters e contentores. Passos seguintes, irá aprender a criar um cluster de Service Fabric (Windows ou Linux) que é executada no Azure. Quando tiver terminado, tiver um cluster seguro que é executado na nuvem para o qual pode implementar aplicações.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial:
- Se não tiver uma subscrição do Azure, crie um [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Instalar o [Service Fabric SDK](service-fabric-get-started.md).
- Instalar [Azure Powershell versão 4.1 ou superior do módulo](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). (Se necessário, [instalar o Azure PowerShell](/powershell/azure/overview) ou [atualizar para uma versão mais recente](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0#update-azps).)


## <a name="create-a-service-fabric-cluster"></a>Criar um cluster do Service Fabric

Este script cria um nó único, a pré-visualização de recursos de infraestrutura do serviço de cluster. Um certificado autoassinado protege o cluster. O script cria o certificado, juntamente com o cluster e, em seguida, coloca o certificado no Cofre de chaves. Não é possível aumentar clusters de nó único, para além de uma máquina virtual e não é possível atualizar clusters de pré-visualização para versões mais recentes.

Para calcular o custo tarifas através da execução de um cluster do Service Fabric no Azure, utilize o [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/).
Para obter mais informações sobre como criar clusters do Service Fabric, consulte [criar um cluster do Service Fabric com o Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Abra uma consola do PowerShell, inicie sessão no Azure e selecione a subscrição que pretende implementar o cluster no:

   ```PowerShell
   Login-AzureRmAccount
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```

## <a name="cluster-parameters"></a>Parâmetros de cluster

   Este script utiliza os seguintes parâmetros e conceitos. Personalize os parâmetros para satisfazer as suas necessidades.

   | Parâmetro       | Descrição | Valor sugerido |
   | --------------- | ----------- | --------------- |
   | Localização | A região do Azure onde implementou o cluster. | Por exemplo, *westeurope*, *eastasia*, ou *eastus* |
   | Nome     | O nome do cluster que pretende criar. O nome tem de ter 4-23 carateres e pode ter apenas letras minúsculas, números e hífenes. | Por exemplo, *bobs sfpreviewcluster* |
   | resourceGroupName   | O nome do grupo de recursos no qual pretende criar o cluster. | Por exemplo, *myresourcegroup* |
   | VmSku  | A máquina virtual SKU a utilizar para os nós. | Qualquer máquina virtual válida SKU |
   | SO  | A máquina virtual SO a utilizar para os nós. | Qualquer máquina virtual válida SO |
   | keyVaultName | O nome do novo cofre de chaves para associar o cluster. | Por exemplo, *mykeyvault* |
   | ClusterSize | O número de máquinas virtuais no seu cluster (pode ser *1* ou *3-99*).| Especifique apenas uma máquina virtual para um cluster de pré-visualização |
   | CertificateSubjectName | O nome do requerente do certificado a ser criado. | Segue-se o formato:  *<name>* . *<location>* . cloudapp.azure.com |

### <a name="default-parameter-values"></a>Valores de parâmetro predefinidos
**Máquina virtual**: definições opcionais. Se não especificá-los, o nome de utilizador admin será assumida a *vmadmin* e PowerShell pede-lhe uma palavra-passe da máquina virtual antes de criar o cluster.

**Portas**: por predefinição para as portas 80 e 8081. Pode especificar portas adicionais ao seguir as orientações para [portas nos clusters de Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/create-load-balancer-rule).

**Diagnóstico**: ativado por predefinição.

**Serviço DNS**: não ativada por predefinição.

**Proxy inverso**: não ativada por predefinição.

## <a name="create-the-cluster-with-your-parameters"></a>Criar o cluster com os parâmetros

Depois de decidir os parâmetros que se adequam às suas necessidades, execute o seguinte comando para gerar um cluster do Service Fabric seguro e o respetivo certificado.

Pode modificar este script para incluir parâmetros adicionais. Para obter mais informações sobre os parâmetros para a criação do cluster, consulte o [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/new-azurermservicefabriccluster.md) cmdlet.

>[!NOTE]
>Antes de executar este comando, tem de criar uma pasta onde pode armazenar o certificado de cluster.

```PowerShell

# Set the certificate variables. This creates and encrypts a password that Service Fabric will use.
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force

# You must create the folder where you want to store the certificate on your machine before you start this step.
$certfolder="c:\mycertificates\"

# Set the variables for common values. Change the values to fit your needs.
$clusterloc="WestUS"
$clustername = "mysfcluster"
$groupname="mysfclustergroup"       
$vmsku = "Standard_D2_v2"
$vaultname = "mykeyvault"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Set the number of cluster nodes. The possible values are 1 and 3-99.
$clustersize=1

# Create the Service Fabric cluster and its self-signed certificate. The OS you specify here lets you use this cluster with any applications that are also using containers.
New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
-ClusterSize $clustersize -CertificateSubjectName $subname `
-CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
-OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname
```

O processo de criação pode demorar alguns minutos. Depois de concluída a configuração, produz informações sobre o cluster criada no Azure. Também copia o certificado de cluster para o diretório - CertificateOutputFolder no caminho especificado para este parâmetro.

Tome nota do **ManagementEndpoint** URL para o cluster, o que pode ser, como o seguinte URL: https://mycluster.westeurope.cloudapp.azure.com:19080.

## <a name="import-the-certificate"></a>Importe o certificado

Quando o cluster é criado com êxito, execute o seguinte comando para se certificar de que pode utilizar o certificado autoassinado:

```PowerShell

# To connect to the cluster, install the certificate into the Personal (My) store of the current user on your computer.
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
-FilePath C:\mycertificates\mysfclustergroup20170531104310.pfx `
-Password $certpwd
```

Este comando instala o certificado no utilizador atual do seu computador. É necessário este certificado para aceder ao Service Fabric Explorer e ver o estado de funcionamento do cluster.


## <a name="view-your-cluster-optional"></a>Ver o seu cluster (opcional)

Depois de ter o cluster e o certificado importado, pode ligar ao cluster e ver o estado de funcionamento. Existem várias formas de ligar, através do PowerShell ou de Service Fabric Explorer.

### <a name="service-fabric-explorer"></a>Service Fabric Explorer
Pode ver o estado de funcionamento do seu cluster através do Service Fabric Explorer. Para tal, navegue para o **ManagementEndpoint** URL para o cluster e, em seguida, selecione o certificado guardado no seu computador.

>[!NOTE]
>Quando abrir o Explorador de recursos de infraestrutura de serviço, verá um erro de certificado, tal como está a utilizar um certificado autoassinado. No Edge, poderá ter de clicar em **detalhes**e, em seguida, clique em de **avance para a página Web** ligação. No Chrome, poderá ter de clicar em **avançadas**e, em seguida, clique em de **continuar** ligação.

### <a name="powershell"></a>PowerShell

O módulo do PowerShell de Service Fabric fornece vários cmdlets para gerir clusters, aplicações e serviços do Service Fabric. Utilize o [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) cmdlet para ligar ao cluster seguro. Os detalhes de ponto final de ligação e o thumbprint do certificado podem ser encontrados na saída do passo anterior.

```PowerShell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
-KeepAliveIntervalInSec 10 `
-X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
-FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
-StoreLocation CurrentUser -StoreName My
```

Também pode verificar que o se estiver ligado e que o cluster está em bom estado, utilizando o [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) cmdlet.

```PowerShell
Get-ServiceFabricClusterHealth
```

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a criar um cluster do Service Fabric seguro no Azure utilizando o PowerShell.

Em seguida, avançar para o tutorial seguinte para saber como implementar uma aplicação existente:
> [!div class="nextstepaction"]
> [Implementar uma aplicação .NET existente com o Docker Compose](service-fabric-host-app-in-a-container.md)

 
