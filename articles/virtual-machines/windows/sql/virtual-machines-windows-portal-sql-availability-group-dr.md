---
title: "Recuperação de desastres - máquinas virtuais do Azure - grupos de disponibilidade do SQL Server | Microsoft Docs"
description: "Este artigo explica como configurar um grupo de disponibilidade do SQL Server em virtual machines do Azure com uma réplica numa região diferente."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: 1ce90cf4bae66bfd6387a2698fd9b1ba7fc64595
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-always-on-availability-group-on-azure-virtual-machines-in-different-regions"></a>Configurar um grupo de disponibilidade Always On em máquinas virtuais do Azure em regiões diferentes

Este artigo explica como configurar uma SQL Server Always On disponibilidade réplica de grupo de máquinas virtuais do Azure numa localização remota do Azure. Utilize esta configuração para suportar a recuperação de desastre.

Este artigo aplica-se para máquinas virtuais do Azure no modo Resource Manager.

A imagem seguinte mostra uma implementação comum de um grupo de disponibilidade em máquinas virtuais do Azure:

   ![Grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic.png)

Esta implementação, todas as máquinas virtuais estão numa região do Azure. As réplicas do grupo de disponibilidade podem ter consolidação síncrona com ativação pós-falha automática no SQL Server-1 e 2 do SQL Server. Para criar esta arquitetura, consulte [modelo do grupo de disponibilidade ou tutorial](virtual-machines-windows-portal-sql-availability-group-overview.md).

Esta arquitetura é vulnerável o período de indisponibilidade se a região do Azure ficar inacessível. Para ultrapassar esta vulnerabilidade, adicione uma réplica numa região diferente do Azure. O diagrama seguinte mostra o aspeto que a nova arquitetura seria:

   ![DR do grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic-dr.png)

O diagrama anterior mostra uma nova máquina virtual denominada SQL-3. SQL Server-3 está numa região diferente do Azure. SQL Server-3 é adicionada para o Cluster de ativação pós-falha do Windows Server. 3 do SQL Server pode alojar uma réplica de grupo de disponibilidade. Por fim, tenha em atenção que a região do Azure SQL 3 tem um novo Balanceador de carga do Azure.

>[!NOTE]
> Um conjunto de disponibilidade do Azure é necessário quando mais do que uma máquina virtual na mesma região. Se apenas uma máquina virtual é a região, em seguida, o conjunto de disponibilidade não é necessário. Só é possível colocar uma máquina virtual num conjunto no momento da criação de disponibilidade. Se a máquina virtual já está a ser um conjunto de disponibilidade, pode adicionar uma máquina virtual para uma réplica de adicional mais tarde.

Nesta arquitetura, a réplica na região remota normalmente é configurada com o modo de ativação pós-falha manual e o modo de disponibilidade de consolidação assíncrona.

Quando as réplicas do grupo de disponibilidade estão em máquinas virtuais do Azure em diferentes regiões do Azure, requer que cada região:

* Um gateway de rede virtual
* Uma ligação de gateway de rede virtual

O diagrama seguinte mostra como as redes comunicam entre centros de dados.

   ![Grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-dr/01-vpngateway-example.png)

>[!IMPORTANT]
>Esta arquitetura incorreu encargos associados à saída de dados para dados replicados entre regiões do Azure. Consulte [preços de largura de banda](http://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Criar a réplica remota

Para criar uma réplica num centro de dados remota, efetue os seguintes passos:

1. [Criar uma rede virtual na região novo](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

1. [Configurar uma ligação VNet a VNet no portal do Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >Em alguns casos, poderá ter de utilizar o PowerShell para criar a ligação VNet a VNet. Por exemplo, se utilizar diferentes contas do Azure não consegue configurar a ligação no portal. Neste caso, consulte, [configurar uma ligação VNet a VNet no portal do Azure](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Criar um controlador de domínio na região novo](../../../active-directory/active-directory-new-forest-virtual-machine.md).

   Este controlador de domínio fornece autenticação se o controlador de domínio no site primário não estiver disponível.

1. [Criar uma máquina virtual do SQL Server na região novo](virtual-machines-windows-portal-sql-server-provision.md).

1. [Crie um balanceador de carga do Azure na rede na nova região](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

   Este Balanceador de carga tem de:

   - Estar na mesma rede e sub-rede, como a nova máquina virtual.
   - Ter um endereço IP estático para o serviço de escuta do grupo de disponibilidade.
   - Incluem um conjunto de back-end constituída apenas as máquinas virtuais na mesma região que o Balanceador de carga.
   - Utilize uma sonda de porta TCP específica para o endereço IP.
   - Ter uma regra específica para o SQL Server na mesma região de balanceamento de carga.  

1. [Adicione a funcionalidade Clustering de ativação pós-falha para o novo servidor de SQL](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Associar o novo servidor de SQL ao domínio](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

1. [Definir a nova conta de serviço do SQL Server para utilizar uma conta de domínio](virtual-machines-windows-portal-sql-availability-group-prereq.md#setServiceAccount).

1. [Adicione o novo servidor de SQL Server para o Cluster de ativação pós-falha do Windows Server](virtual-machines-windows-portal-sql-availability-group-tutorial.md#addNode).

1. Crie um recurso de endereço IP no cluster.

   Pode criar o recurso de endereço IP no Gestor de clusters de ativação pós-falha. A função do grupo de disponibilidade com o botão direito, clique em **adicionar recursos**, **mais recursos**e clique em **endereço IP**.

   ![Criar endereço IP](./media/virtual-machines-windows-portal-sql-availability-group-dr/20-add-ip-resource.png)

   Configure este endereço IP da seguinte forma:

   - Utilize a rede a partir do Centro de dados remota.
   - Atribua o endereço IP do Balanceador de carga do Azure de novo. 

1. No novo servidor SQL no SQL Server Configuration Manager, [ativar grupos de disponibilidade Always](http://msdn.microsoft.com/library/ff878259.aspx).

1. [Abrir portas de firewall no SQL Server novo](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

   Os números de porta que é necessário abrir dependem do seu ambiente. Sonda de estado de funcionamento do Balanceador de carga portas abertas para o ponto final de espelhamento e o Azure.

1. [Adicionar uma réplica para o grupo de disponibilidade no novo servidor SQL](http://msdn.microsoft.com/library/hh213239.aspx).

   Para uma réplica numa região do Azure remota, tem de defini-lo para replicação assíncrona com ativação pós-falha manual.  

1. Adicione o recurso de endereço IP como uma dependência para o cluster de (nome de rede) do ponto de acesso de cliente do serviço de escuta.

   A seguinte captura de ecrã mostra um recurso de cluster de endereço IP está corretamente configurado:

   ![Grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-dr/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >O grupo de recursos do cluster inclui os dois endereços IP. Ambos os endereços IP são dependências para o ponto de acesso de cliente do serviço de escuta. Utilize o **ou** operador na configuração de dependência do cluster.

1. [Definir os parâmetros de cluster no PowerShell](virtual-machines-windows-portal-sql-availability-group-tutorial.md#setparam).

Execute o script do PowerShell com o nome de rede de cluster, o endereço IP e a porta da sonda que configurou no balanceador de carga na região de novo.

   ```PowerShell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = “<n.n.n.n>” # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

## <a name="set-connection-for-multiple-subnets"></a>Ligação de conjunto para várias sub-redes

A réplica no Centro de dados remoto faz parte do grupo de disponibilidade, mas é numa sub-rede diferente. Se esta réplica torna-se a réplica primária, poderão ocorrer tempos limite de ligação de aplicação. Este comportamento é o mesmo que um grupo de disponibilidade local numa implementação de sub-redes múltiplas. Para permitir ligações de cliente aplicações, atualize a ligação de cliente ou configurar a resolução de nome, a colocação em cache no recurso de nome de rede de cluster.

De preferência, atualizar as cadeias de ligação de cliente para definir `MultiSubnetFailover=Yes`. Consulte [ligar com MultiSubnetFailover](http://msdn.microsoft.com/library/gg471494#Anchor_0).

Se não é possível modificar as cadeias de ligação, pode configurar a colocação em cache de resolução de nome. Consulte [tempos limite de ligação no grupo de disponibilidade de sub-redes múltiplas](http://blogs.msdn.microsoft.com/alwaysonpro/2014/06/03/connection-timeouts-in-multi-subnet-availability-group/).

## <a name="fail-over-to-remote-region"></a>Efetuar a ativação pós-falha remoto região

Para testar a conectividade de serviço de escuta para a região remota, pode falhar a réplica para a região remota. Enquanto a réplica é assíncrona, a ativação pós-falha é vulnerável a potencial perda de dados. Para efetuar a ativação pós-falha sem perda de dados, altere o modo de disponibilidade para síncrona e definir o modo de ativação pós-falha para automático. Utilize os passos seguintes:

1. No **Object Explorer**, ligar à instância do SQL Server que aloja a réplica primária.
1. Em **grupos de Disponibilidade AlwaysOn**, **grupos de disponibilidade**, clique no seu grupo de disponibilidade e clique em **propriedades**.
1. No **geral** página **réplicas de disponibilidade**, defina a réplica secundária no site de DR para utilizar **consolidação síncrona** do modo de disponibilidade e  **Automática** modo de ativação pós-falha.
1. Se tiver uma réplica secundária no mesmo site que a réplica primária para elevada disponibilidade, como esta réplica **consolidação assíncrona** e **Manual**.
1. Clique em OK.
1. No **Object Explorer**, clique no grupo de disponibilidade e, em **Mostrar Dashboard**.
1. No dashboard, certifique-se de que a réplica no site de DR está sincronizada.
1. No **Object Explorer**, clique no grupo de disponibilidade e, em **ativação pós-falha...** . Estúdios de gestão do SQL Server é aberto um Assistente para efetuar a ativação pós-falha do SQL Server.  
1. Clique em **seguinte**e selecione a instância do SQL Server no site de DR. Clique em **seguinte** novamente.
1. Ligue à instância do SQL Server no site de DR e clique em **seguinte**.
1. No **resumo** página, verifique as definições e clique em **concluir**.

Depois de testar a conectividade, regresse ao seu centro de dados principal a réplica primária e definir o modo de disponibilidade para as respetivas definições de funcionamento normais. A tabela seguinte mostra as definições operacionais normais para a arquitetura descrita neste documento:

| Localização | Instância do servidor | Função | Modo de disponibilidade | Modo de ativação pós-falha
| ----- | ----- | ----- | ----- | -----
| Centro de dados primária | SQL SERVER-1 | Primária | Síncrona | Automática
| Centro de dados primária | 2 DO SQL SERVER | Secundária | Síncrona | Automática
| Centro de dados secundária ou remoto | SQL SERVER-3 | Secundária | Assíncrona | Manual


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Obter mais informações sobre a ativação de pós-falha manual planeada e forçada

Para obter mais informações, consulte os seguintes tópicos:

- [Efetuar uma ativação pós-falha Manual planeada de um grupo de disponibilidade (SQL Server)](http://msdn.microsoft.com/library/hh231018.aspx)
- [Efetuar uma ativação pós-falha Manual forçada de um grupo de disponibilidade (SQL Server)](http://msdn.microsoft.com/library/ff877957.aspx)

## <a name="additional-links"></a>Ligações adicionais

* [Always On nos grupos de disponibilidade](http://msdn.microsoft.com/library/hh510230.aspx)
* [Máquinas virtuais do Azure](http://docs.microsoft.com/azure/virtual-machines/windows/)
* [Balanceadores de carga do Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)
* [Conjuntos de disponibilidade do Azure](../manage-availability.md)
