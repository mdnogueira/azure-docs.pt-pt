---
title: Configurar o Balanceador de carga para SQL Server sempre no | Microsoft Docs
description: "Configurar o Balanceador de carga para trabalhar com o SQL Server sempre no e como tirar partido do powershell para criar Balanceador de carga para a implementação do SQL Server"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: d7bc3790-47d3-4e95-887c-c533011e4afd
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 3ebbf1c4009d89b1f18b2ff8ff5dd243c456dff8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-load-balancer-for-sql-always-on"></a>Configurar o Balanceador de carga para SQL Server sempre no

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Grupos de Disponibilidade AlwaysOn do SQL Server podem agora ser executados com o ILB. Grupo de disponibilidade está a solução de flagship do SQL Server para elevada disponibilidade e recuperação após desastre. O serviço de escuta do grupo de disponibilidade permite aplicações liguem de forma totalmente integrada a réplica primária, independentemente do número de réplicas na configuração de cliente.

O nome do serviço de escuta (DNS) está mapeado para um endereço IP com balanceamento de carga e o Balanceador de carga do Azure direciona o tráfego de entrada para apenas o servidor primário no conjunto de réplicas.

Pode utilizar o suporte do ILB para pontos finais do SQL Server AlwaysOn (serviço de escuta). Agora tem controlo sobre a acessibilidade do serviço de escuta e pode escolher o endereço IP com balanceamento de carga de uma sub-rede específica na sua rede Virtual (VNet).

Utilizando o ILB no serviço de escuta, o ponto final do SQL server (por exemplo, servidor = tcp:ListenerName, 1433; base de dados = DatabaseName) é acessível apenas a:

* Os serviços e VMs na mesma rede Virtual
* Os serviços e VMs a partir da rede ligados ao local
* Os serviços e as VMs de VNets interligadas

![ILB_SQLAO_NewPic](./media/load-balancer-configure-sqlao/sqlao1.png)

Figura 1 - AlwaysOn de SQL Server configurada com Balanceador de carga para a Internet

## <a name="add-internal-load-balancer-to-the-service"></a>Adicionar balanceador de carga interno para o serviço

1. O exemplo seguinte, irá configurar uma rede Virtual que contém uma sub-rede denominada 'Subnet-1':

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. Adicionar pontos finais de balanceamento de carga para o ILB em cada VM

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    No exemplo acima, tiver chamado "sqlsvc1" e "sqlsvc2" execução 2 VM na nuvem de serviço "Sqlsvc". Depois de criar o ILB com `DirectServerReturn` comutador, adicionar pontos finais de balanceamento de carga para o ILB para permitir que o SQL Server configurar os serviços de escuta para os grupos de disponibilidade.

Para obter mais informações sobre o SQL AlwaysOn, consulte [configurar um balanceador de carga interno para um grupo de Disponibilidade AlwaysOn no Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

## <a name="see-also"></a>Veja Também
[Começar a configurar um balanceador de carga com acesso de Internet](load-balancer-get-started-internet-arm-ps.md)

[Começar a configurar um balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição de Balanceador de carga](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite TCP inativo para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
