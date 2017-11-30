---
title: Configurar portas de disponibilidade elevada para o Balanceador de carga do Azure | Microsoft Docs
description: "Saiba como utilizar as portas de elevada disponibilidade para o tráfego interno em todas as portas de balanceamento de carga"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/02/2017
ms.author: kumud
ms.openlocfilehash: 646ade828e96810bdc3b07d4dc5c0276a1621969
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="how-to-configure-high-availability-ports-for-internal-load-balancer"></a>Como configurar portas de elevada disponibilidade para o Balanceador de carga interno

Este artigo fornece um exemplo de implementação de elevada disponibilidade (HA) portas num Balanceador de carga interno. Para aplicações de rede virtuais (NVAs) configurações específicas, consulte os sites correspondentes do fornecedor.

>[!NOTE]
> Funcionalidade de elevada disponibilidade portas está atualmente em pré-visualização. Durante a pré-visualização, a funcionalidade poderá não ter o mesmo nível de disponibilidade e fiabilidade oferecido na versão de disponibilidade geral. Para obter mais informações, consulte [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Figura 1 ilustra o exemplo de implementação descrita neste artigo, a seguinte configuração:
- Os NVAs são implementadas no conjunto back-end de um balanceador de carga interno subjacente à configuração de portas do HA. 
- O UDR aplicado as rotas de sub-rede DMZ todo o tráfego para os NVAs efetuando o salto seguinte como lista IP Virtual de Balanceador de carga interno. 
- Balanceador de carga interno distribui o tráfego para um os NVAs Active Directory, de acordo com o algoritmo LB.
- NVA processa o tráfego e reencaminha-lo para o destino original da sub-rede de back-end.
- O caminho de retorno também pode tirar a rota mesma se um UDR correspondente estiver configurada na sub-rede de back-end. 

![ha portas exemplo de implementação](./media/load-balancer-configure-ha-ports/haports.png)

Figura 1 - aparelhos de rede Virtual implementado por trás de um balanceador de carga interno com portas de elevada disponibilidade 

## <a name="preview-sign-up"></a>Pré-visualização inscrição

Participar na pré-visualização da funcionalidade de portas HA no padrão de Balanceador de carga, registe a sua subscrição para obter acesso utilizando 2.0 do Azure CLI ou PowerShell. Registar a sua subscrição para [pré-visualização do padrão de Balanceador de carga](https://aka.ms/lbpreview#preview-sign-up).

>[!NOTE]
>Registo de pré-visualizações do padrão de Balanceador de carga pode demorar até uma hora.

## <a name="configuring-ha-ports"></a>Configurar as portas HA

A configuração das portas HA envolve a configurar um balanceador de carga interno, com os NVAs no conjunto back-end, uma correspondente configuração Balanceador de carga estado de funcionamento sonda para detetar o estado de funcionamento NVA e a regra de Balanceador de carga com portas HA. A configuração de relacionados do Balanceador de carga geral é abordada [começar](load-balancer-get-started-ilb-arm-portal.md). Este artigo realça a configuração de portas do HA.

A configuração envolve essencialmente definindo o valor de porta de porta e o back-end front-end como **0**e o valor de protocolo para **todos os**. Este artigo descreve como configurar portas de elevada disponibilidade com o portal do Azure, PowerShell e do Azure CLI 2.0.

### <a name="configure-ha-ports-load-balancer-rule-with-the-azure-portal"></a>Configurar a regra de Balanceador de carga do HA portas com o portal do Azure

O portal do Azure inclui o **HA portas** opção através de uma caixa de verificação para esta configuração. Quando selecionada, a configuração de porta e protocolo relacionada é preenchida automaticamente. 

![ha portas configuração através do portal do Azure](./media/load-balancer-configure-ha-ports/haports-portal.png)

Figura 2 - HA configuração de portas através do Portal

### <a name="configure-ha-ports-lb-rule-via-resource-manager-template"></a>Configurar HA portas LB regra através do modelo do Resource Manager

Pode configurar as portas HA com a versão de API de 2017-08-01 para Microsoft.Network/loadBalancers no recurso de Balanceador de carga. O fragmento JSON seguinte ilustra as alterações na configuração de Balanceador de carga para HA portas através da REST API.

```json
    {
        "apiVersion": "2017-08-01",
        "type": "Microsoft.Network/loadBalancers",
        ...
        "sku":
        {
            "name": "Standard"
        },
        ...
        "properties": {
            "frontendIpConfigurations": [...],
            "backendAddressPools": [...],
            "probes": [...],
            "loadBalancingRules": [
             {
                "properties": {
                    ...
                    "protocol": "All",
                    "frontendPort": 0,
                    "backendPort": 0
                }
             }
            ],
       ...
       }
    }
```

### <a name="configure-ha-ports-load-balancer-rule-with-powershell"></a>Configurar a regra de Balanceador de carga do HA portas com o PowerShell

Utilize o seguinte comando para criar a regra de HA portas Balanceador de carga durante a criação do Balanceador de carga interno com o PowerShell:

```powershell
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-ha-ports-load-balancer-rule-with-azure-cli-20"></a>Configurar a regra de Balanceador de carga do HA portas com o Azure CLI 2.0

No passo n. º 4 de [criar um conjunto de Balanceador de carga interno](load-balancer-get-started-ilb-arm-cli.md), utilize o seguinte comando para criar as portas HA regra de Balanceador de carga.

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [portas de elevada disponibilidade](load-balancer-ha-ports-overview.md)
