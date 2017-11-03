---
title: Criar uma regra de Balanceador de carga do Azure para um cluster
description: Configure um balanceador de carga do Azure para abrir as portas para o cluster do Service Fabric do Azure.
services: service-fabric
documentationcenter: na
author: thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/22/2017
ms.author: adegeo
ms.openlocfilehash: d152444f38e7a09b97ce7cb9778d8c67a0a5a421
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Abra portas para um cluster do Service Fabric

O Balanceador de carga com o seu cluster do Azure Service Fabric direciona o tráfego para a aplicação em execução num nó. Se alterar a sua aplicação para utilizar uma porta diferente, tem de expor essa porta (ou uma porta diferente de rotas) no balanceador de carga do Azure.

Quando implementou o cluster do Service Fabric para o Azure, um balanceador de carga foi criado automaticamente para si. Se não tiver um balanceador de carga, consulte [configurar um balanceador de carga para a Internet](..\load-balancer\load-balancer-get-started-internet-portal.md).

## <a name="configure-service-fabric"></a>Configurar recursos de infraestrutura de serviço

A aplicação de Service Fabric **ServiceManifest.xml** ficheiro de configuração define os pontos finais de espera que a aplicação a utilizar. Depois do ficheiro de configuração foi atualizado para definir um ponto final, o Balanceador de carga tem de ser atualizado para expor que (ou outro) porta. Para obter mais informações sobre como criar o ponto final de recursos de infraestrutura de serviço, consulte [configurar um ponto final](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Criar uma regra de Balanceador de carga

Uma regra de Balanceador de carga abre-se uma porta de acesso à internet e reencaminha o tráfego para a porta do nó interno utilizado pela sua aplicação. Se não tiver um balanceador de carga, consulte [configurar um balanceador de carga para a Internet](..\load-balancer\load-balancer-get-started-internet-portal.md).

Para criar uma regra de Balanceador de carga, terá de recolher as seguintes informações:

- Nome do Balanceador de carga.
- Grupo de recursos do cluster de recursos de infraestrutura de serviço e de Balanceador de carga.
- Porta externa.
- Porta interna.

## <a name="azure-cli"></a>CLI do Azure
Demora apenas um único comando para criar uma regra de Balanceador de carga com o **CLI do Azure**. Terá de saber tanto o nome do grupo de recursos e de Balanceador de carga para criar uma nova regra.

>[!NOTE]
>Se precisar de determinar o nome do Balanceador de carga, utilize este comando para obter rapidamente uma lista de todos os balanceadores de carga e os grupos de recursos associados.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

O comando da CLI do Azure tem alguns parâmetros que são descritos na tabela seguinte:

| Parâmetro | Descrição |
| --------- | ----------- |
| `--backend-port`  | A porta a aplicação de Service Fabric está a escutar. |
| `--frontend-port` | A porta do Balanceador de carga expõe para ligações externas. |
| `-lb-name` | O nome do Balanceador de carga para alterar. |
| `-g`       | O grupo de recursos que tenha o Balanceador de carga e o cluster do Service Fabric. |
| `-n`       | O nome pretendido da regra. |


>[!NOTE]
>Para obter mais informações sobre como criar um balanceador de carga com a CLI do Azure, consulte [criar um balanceador de carga com a CLI do Azure](..\load-balancer\load-balancer-get-started-internet-arm-cli.md).

## <a name="powershell"></a>PowerShell

PowerShell é um pouco mais complicada que a CLI do Azure. Siga estes passos para criar uma regra conceptuais:

1. Obter o Balanceador de carga do Azure.
2. Crie uma regra.
3. Adicione a regra de Balanceador de carga.
4. Atualize o Balanceador de carga.

>[!NOTE]
>Se precisar de determinar o nome do Balanceador de carga, utilize este comando para obter rapidamente uma lista de todos os balanceadores de carga e grupos de recursos associados.
>
>`Get-AzureRmLoadBalancer | Select Name, ResourceGroupName`

```powershell
# Get the load balancer
$lb = Get-AzureRmLoadBalancer -Name LB-svcfab3 -ResourceGroupName svcfab_cli

# Create the rule based on information from the load balancer.
$lbrule = New-AzureRmLoadBalancerRuleConfig -Name my-app-rule7 -Protocol Tcp -FrontendPort 39990 -BackendPort 40009 `
                                            -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
                                            -BackendAddressPool  $lb.BackendAddressPools[0] `
                                            -Probe $lb.Probes[0]

# Add the rule to the load balancer
$lb.LoadBalancingRules.Add($lbrule)

# Update the load balancer on Azure
$lb | Set-AzureRmLoadBalancer
```

Sobre o `New-AzureRmLoadBalancerRuleConfig` comando, o `-FrontendPort` representa a porta que expõe o Balanceador de carga para ligações externas, e o `-BackendPort` representa a porta a aplicação de recursos de infraestrutura de serviço está a escutar.

>[!NOTE]
>Para obter mais informações sobre como criar um balanceador de carga com o PowerShell, consulte [criar um balanceador de carga com o PowerShell](..\load-balancer\load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [redes no Service Fabric](service-fabric-patterns-networking.md).