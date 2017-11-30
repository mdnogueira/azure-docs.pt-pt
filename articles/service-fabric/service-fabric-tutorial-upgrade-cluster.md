---
title: "Atualize o tempo de execução do Service Fabric do Azure | Microsoft Docs"
description: "Saiba como utilizar o PowerShell para atualizar o tempo de execução de um cluster do Service Fabric alojado no Azure."
services: service-fabric
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/28/2017
ms.author: adegeo
ms.openlocfilehash: 15acfbce3bde585ed2b39762b08733901133a3dd
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="upgrade-the-runtime-of-a-service-fabric-cluster"></a>Atualize o tempo de execução de um cluster do Service Fabric

Este tutorial faz parte quatro de uma série e mostra-lhe como atualizar o tempo de execução do Service Fabric num cluster do Service Fabric do Azure. Esta parte do tutorial foi escrito para clusters de Service Fabric em execução no Azure e não é aplicável a Self-alojada Service Fabric clusters.

> [!WARNING]
> Esta parte do tutorial requer o PowerShell. Suporte para atualizar o tempo de execução do cluster ainda não é suportado pelas ferramentas de CLI do Azure. Em alternativa, um cluster pode ser atualizado no portal. Para obter mais informações, consulte [atualizar um cluster do Azure Service Fabric](service-fabric-cluster-upgrade.md).

Se o cluster já está a executar o tempo de execução mais recente do Service Fabric, não é necessário efetuar este passo. No entanto, este artigo pode ser utilizado para instalar qualquer tempo de execução suportado num cluster do Service Fabric do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ler a versão de cluster
> * Definir a versão de cluster

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial:
- Se não tiver uma subscrição do Azure, crie um [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Instalar o [Azure Powershell versão 4.1 ou superior do módulo](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) ou [Azure CLI 2.0](/cli/azure/install-azure-cli).
- Criar segura [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou [Linux cluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md) no Azure
- Se implementar um cluster do Windows, configure um ambiente de desenvolvimento do Windows. Instalar [Visual Studio 2017](http://www.visualstudio.com) e **programação do Azure**, **desenvolvimento ASP.NET e web**, e **desenvolvimento de plataformas cruzadas .NET Core**cargas de trabalho.  Em seguida, configure um [ambiente de desenvolvimento de .NET](service-fabric-get-started.md).
- Se implementar um cluster do Linux, configurar um ambiente de desenvolvimento Java no [Linux](service-fabric-get-started-linux.md) ou [MacOS](service-fabric-get-started-mac.md).  Instalar o [Service Fabric CLI](service-fabric-cli.md). 

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão na sua conta do Azure selecione a sua subscrição antes de executar os comandos do Azure.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="get-the-runtime-version"></a>Obter a versão de tempo de execução

Depois de ligar para o Azure, selecionar a subscrição que contém o cluster do Service Fabric, pode obter a versão de tempo de execução do cluster.

```powershell
Get-AzureRmServiceFabricCluster -ResourceGroupName SFCLUSTERTUTORIALGROUP -Name aztestcluster `
    | Select-Object ClusterCodeVersion
```

Em alternativa, apenas obter uma lista de todos os clusters na sua subscrição com o seguinte:

```powershell
Get-AzureRmServiceFabricCluster | Select-Object Name, ClusterCodeVersion
```

Tenha em atenção o **ClusterCodeVersion** valor. Este valor será utilizado na secção seguinte.

## <a name="upgrade-the-runtime"></a>O tempo de execução de atualização

Utilize o valor do **ClusterCodeVersion** da secção anterior com a `Get-ServiceFabricRuntimeUpgradeVersion` cmdlet para identificar quais as versões disponíveis para atualizar para o. Este cmdlet só pode ser executado a partir de um computador ligado à internet. Por exemplo, se pretendesse ver quais as versões de runtime foi possível atualizar a partir da versão `5.7.198.9494`, utilize o seguinte comando:

```powershell
Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion "5.7.198.9494"
```

Com uma lista das versões, pode dizer o cluster de Service Fabric do Azure para atualizar para um tempo de execução mais recente. Por exemplo, se versão `6.0.219.9494` está disponível para atualizar para o, utilize o seguinte comando para atualizar o seu cluster.

```powershell
Set-AzureRmServiceFabricUpgradeType -ResourceGroupName SFCLUSTERTUTORIALGROUP `
                                    -Name aztestcluster `
                                    -UpgradeMode Manual `
                                    -Version "6.0.219.9494"
```

> [!IMPORTANT]
> A atualização de tempo de execução do cluster pode demorar muito tempo a concluir. PowerShell está bloqueado enquanto a atualização está em execução. Pode utilizar outra sessão de PowerShell para verificar o estado da atualização.

O estado da atualização pode ser monitorizado com o PowerShell ou o `sfctl` CLI.

Primeiro liga ao cluster com o certificado SSL criado na primeira parte do tutorial. Utilize o `Connect-ServiceFabricCluster` cmdlet ou `sfctl cluster upgrade-status`.

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
                             -KeepAliveIntervalInSec 10 `
                             -X509Credential -ServerCertThumbprint $thumbprint `
                             -FindType FindByThumbprint -FindValue $thumbprint `
                             -StoreLocation CurrentUser -StoreName My
```

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Em seguida, utilize `Get-ServiceFabricClusterUpgrade` ou `sfctl cluster upgrade-status` para apresentar o estado. Algo semelhante ao seguinte resultado é apresentado.

```powershell
Get-ServiceFabricClusterUpgrade

TargetCodeVersion                          : 6.0.219.9494
TargetConfigVersion                        : 3
StartTimestampUtc                          : 11/28/2017 3:09:48 AM
UpgradeState                               : RollingForwardPending
UpgradeDuration                            : 00:09:00
CurrentUpgradeDomainDuration               : 00:09:00
NextUpgradeDomain                          : 1
UpgradeDomainsStatus                       : { "0" = "Completed";
                                             "1" = "Pending";
                                             "2" = "Pending";
                                             "3" = "Pending";
                                             "4" = "Pending" }
UpgradeKind                                : Rolling
RollingUpgradeMode                         : Monitored
FailureAction                              : Rollback
ForceRestart                               : False
UpgradeReplicaSetCheckTimeout              : 37201.09:59:01
HealthCheckWaitDuration                    : 00:05:00
HealthCheckStableDuration                  : 00:05:00
HealthCheckRetryTimeout                    : 00:45:00
UpgradeDomainTimeout                       : 02:00:00
UpgradeTimeout                             : 12:00:00
ConsiderWarningAsError                     : False
MaxPercentUnhealthyApplications            : 0
MaxPercentUnhealthyNodes                   : 100
ApplicationTypeHealthPolicyMap             : {}
EnableDeltaHealthEvaluation                : True
MaxPercentDeltaUnhealthyNodes              : 0
MaxPercentUpgradeDomainDeltaUnhealthyNodes : 0
ApplicationHealthPolicyMap                 : {}
```

```azurecli
sfctl cluster upgrade-status

{
  "codeVersion": "6.0.219.9494",
  "configVersion": "3",

... item cut to save space ...

  },
  "upgradeDomains": [
    {
      "name": "0",
      "state": "Completed"
    },
    {
      "name": "1",
      "state": "Pending"
    },
    {
      "name": "2",
      "state": "Pending"
    },
    {
      "name": "3",
      "state": "Pending"
    },
    {
      "name": "4",
      "state": "Pending"
    }
  ],
  "upgradeDurationInMilliseconds": "PT1H2M4.63889S",
  "upgradeState": "RollingForwardPending"
}
```

## <a name="conclusion"></a>Conclusão
Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Obter a versão do tempo de execução do cluster
> * Atualize o tempo de execução do cluster
> * Monitorizar a atualização
