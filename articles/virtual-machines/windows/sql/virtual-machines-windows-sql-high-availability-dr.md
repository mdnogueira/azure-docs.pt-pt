---
title: "Elevada disponibilidade e recuperação de desastres do SQL Server | Microsoft Docs"
description: "Ver um debate dos vários tipos de estratégias HADR para SQL Server em execução em máquinas virtuais do Azure."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: mikeray
ms.openlocfilehash: a81b956107ef82f40ad5304808068a7573ca7d27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Elevada disponibilidade e recuperação após desastre para SQL Server em Máquinas Virtuais do Azure

Microsoft Azure as máquinas virtuais (VMs) com o SQL Server pode ajudá-lo a reduzir o custo de uma elevada disponibilidade e após desastre (HADR) da base de dados solução de recuperação. A maioria das soluções de HADR do SQL Server são suportadas em máquinas de virtuais do Azure, como só de Azure e como soluções híbridas. Uma solução apenas de Azure, todo o sistema HADR é executada no Azure. Numa configuração híbrida, parte da solução é executado no Azure e a outro parte é executado no local na sua organização. A flexibilidade de ambiente do Azure permite-lhe mover parcialmente ou completamente para o Azure para satisfazer os requisitos de HADR dos sistemas de base de dados de SQL Server e orçamento.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>Noções sobre a necessidade de uma solução HADR
Trata-se até que para se certificar de que o sistema de base de dados tem as capacidades HADR que requer que o contrato de nível de serviço (SLA). O facto de que o Azure oferece mecanismos de elevada disponibilidade, tais como o serviço de recuperação para serviços em nuvem e de deteção de falha de recuperação para máquinas virtuais, não próprio garantir que pode cumpre o SLA de pretendido. Estes mecanismos de proteger a elevada disponibilidade das VMs, mas não a elevada disponibilidade do SQL Server em execução nas VMs. É possível que a instância do SQL Server irão falhar enquanto a VM está online e em bom estado de funcionamento. Além disso, mesmo a elevada disponibilidade mecanismos fornecidos pelo Azure permitem o período de indisponibilidade das VMs devido a eventos, tal como a recuperação de falhas de hardware e software ou a atualização do sistema operativo.

Além disso, a Georreplicação redundante armazenamento (GRS) no Azure, o que é implementado com uma funcionalidade denominada georreplicação, não pode ser uma solução de recuperação de desastre adequado para as bases de dados. Uma vez georreplicação envia dados de forma assíncrona, atualizações recentes podem ser perdidas em caso de desastre. Obter mais informações sobre limitações georreplicação são abrangidos os [a georreplicação não é suportada para ficheiros de registo e de dados em discos separados](#geo-replication-support) secção.

## <a name="hadr-deployment-architectures"></a>Arquiteturas de implementação HADR
Tecnologias de HADR do SQL Server que são suportadas no Azure incluem:

* [Always On nos grupos de disponibilidade](https://technet.microsoft.com/library/hh510230.aspx)
* [Sempre em instâncias de Cluster de ativação pós-falha](https://technet.microsoft.com/library/ms189134.aspx)
* [Envio de registo](https://technet.microsoft.com/library/ms187103.aspx)
* [Cópia de segurança do SQL Server e de restauro com o serviço de armazenamento de Blobs do Azure](https://msdn.microsoft.com/library/jj919148.aspx)
* [O espelhamento da base de dados](https://technet.microsoft.com/library/ms189852.aspx) - preterido no SQL Server 2016

É possível combinar as tecnologias para implementar uma solução do SQL Server que tenha elevada disponibilidade e funcionalidades de recuperação após desastre. Consoante a tecnologia que utilizar, uma implementação híbrida pode necessitar de um túnel VPN com a rede virtual do Azure. As secções abaixo mostram algumas das arquiteturas de implementação de exemplo.

## <a name="azure-only-high-availability-solutions"></a>Apenas para Azure: soluções de elevada disponibilidade

Pode ter uma solução de elevada disponibilidade para o SQL Server a um nível de base de dados com grupos de disponibilidade Always - denominados grupos de disponibilidade. Também pode criar uma solução de elevada disponibilidade ao nível de uma instância com sempre na ativação pós-falha Cluster instâncias - ativação pós-falha instâncias de cluster. Para proporcionar uma redundância adicional, pode criar redundância em ambos os níveis através da criação de grupos de disponibilidade na ativação pós-falha instâncias de cluster. 

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de disponibilidade** |As réplicas de disponibilidade em execução em VMs do Azure na mesma região fornecem elevada disponibilidade. É necessário configurar um controlador de domínio VM, porque o clustering de ativação pós-falha Windows requer um domínio do Active Directory.<br/> ![Grupos de disponibilidade](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_always_on.gif)<br/>Para obter mais informações, consulte [configurar grupos de disponibilidade no Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Instâncias de cluster de ativação pós-falha** |Ativação pós-falha Cluster instâncias (FCI), o que exige armazenamento partilhado, podem ser criadas 3 formas diferentes.<br/><br/>1. Um cluster de ativação pós-falha de dois nós em execução em VMs do Azure com a utilização do armazenamento ligado [Windows Server 2016 espaços de armazenamento direto \(S2D\) ](virtual-machines-windows-portal-sql-create-failover-cluster.md) para fornecer uma SAN virtual baseado em software.<br/><br/>2. Um cluster de ativação pós-falha de dois nós em execução em VMs do Azure com o armazenamento suportado por uma solução de clustering de terceiros. Para obter um exemplo específico que utiliza SIOS DataKeeper, consulte [elevada disponibilidade para uma partilha de ficheiros utilizando o clustering de ativação pós-falha e o software de terceiros 3rd SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>3. Um cluster de ativação pós-falha de dois nós em execução em VMs do Azure com o armazenamento de blocos de destino partilhado iSCSI remoto através do ExpressRoute. Por exemplo, armazenamento privada NetApp (NPS) expõe um destino de iSCSI através do ExpressRoute com Equinix para VMs do Azure.<br/><br/>Para armazenamento partilhado de terceiros e soluções de replicação de dados, deverá contactar o fornecedor para quaisquer problemas relacionados com o acesso a dados de ativação pós-falha.<br/><br/>Tenha em atenção que utilizar a FCI de [File storage do Azure](https://azure.microsoft.com/services/storage/files/) ainda não é suportada, porque esta solução não utilizar o armazenamento Premium. Estamos a trabalhar para suportar isto em breve. |

## <a name="azure-only-disaster-recovery-solutions"></a>Apenas para Azure: soluções de recuperação de desastre
Pode ter uma solução de recuperação após desastre para as bases de dados do SQL Server no Azure através de grupos de disponibilidade, o espelhamento da base de dados ou a cópia de segurança e restaurar com blobs de armazenamento.

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de disponibilidade** |Réplicas de disponibilidade em execução em vários datacenters em VMs do Azure para recuperação após desastre. Esta solução por várias regiões protege contra a falha completa do local. <br/> ![Grupos de disponibilidade](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_alwayson.png)<br/>Numa região, todas as réplicas devem ser o mesmo serviço de nuvem e a mesma VNet. Porque cada região terá uma VNet separada, tais soluções exigiam VNet a VNet conectividade. Para obter mais informações, consulte [configurar uma ligação VNet a VNet no portal do Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Para obter instruções detalhadas, consulte [configurar um grupo de disponibilidade do SQL Server em Azure Virtual Machines em regiões diferentes](virtual-machines-windows-portal-sql-availability-group-dr.md).|
| **O espelhamento da base de dados** |Principal e espelho e servidores que executam em diferentes centros de dados para recuperação após desastre. Tem de implementar através de certificados de servidor porque um domínio do Active Directory não pode abranger vários centros de dados.<br/>![O espelhamento da base de dados](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_dbmirroring.gif) |
| **Cópia de segurança e restauro com o serviço de armazenamento de Blobs do Azure** |Bases de dados de produção cópias de segurança diretamente ao armazenamento de BLOBs no Centro de dados diferente para recuperação após desastre.<br/>![Cópia de Segurança e Restauro](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_backup_restore.gif)<br/>Para obter mais informações, consulte [cópia de segurança e restaurar para o SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md). |
| **A replicação e ativação pós-falha de SQL Server para o Azure com o Azure Site Recovery** |SQL Server de produção de um datacenter do Azure replicado diretamente para o armazenamento do Azure do Centro de dados do Azure diferente para a recuperação de desastre.<br/>![Replicar utilizando o Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_standalone_sqlserver-asr.png)<br/>Para obter mais informações, consulte [proteger SQL Server com a recuperação de desastres do SQL Server e o Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>TI híbridos: Soluções de recuperação após desastre
Pode ter uma solução de recuperação após desastre para as bases de dados do SQL Server num ambiente híbrido-IT utilizando grupos de disponibilidade, o espelhamento da base de dados, de envio de registos e cópia de segurança e restaurar com armazenamento do blogue do Azure.

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de disponibilidade** |Algumas réplicas de disponibilidade em execução em VMs do Azure e outras réplicas em execução no local para a recuperação de desastre em vários sites. O site de produção pode ser no local ou num datacenter do Azure.<br/>![Grupos de disponibilidade](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_alwayson.gif)<br/>Porque todas as réplicas de disponibilidade tem de estar no mesmo cluster de ativação pós-falha, o cluster tem span ambas as redes (um cluster de ativação pós-falha de sub-redes múltiplas). Esta configuração requer uma ligação VPN entre o Azure e a rede no local.<br/><br/>Para a recuperação de desastre com êxito das bases de dados, deve também instalar um controlador de domínio de réplica no site de recuperação de desastre.<br/><br/>É possível utilizar o Assistente para Adicionar réplica no SSMS para adicionar uma réplica do Azure para um existente sempre no grupo de disponibilidade. Para obter mais informações, consulte o Tutorial: expandir o seu sempre no grupo de disponibilidade para o Azure. |
| **O espelhamento da base de dados** |Um parceiro em execução na VM do Azure e a outro em execução no local para a recuperação de desastre entre sites utilizando certificados de servidor. Os parceiros não têm de estar no mesmo domínio do Active Directory e não é necessária nenhuma ligação VPN.<br/>![O espelhamento da base de dados](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_dbmirroring.gif)<br/>Outra base de dados espelhamento cenário envolve um parceiro em execução na VM do Azure e a outro em execução no local no mesmo domínio do Active Directory para a recuperação de desastre em vários sites. A [ligação VPN entre a rede virtual do Azure e a rede no local](../../../vpn-gateway/vpn-gateway-site-to-site-create.md) é necessária.<br/><br/>Para a recuperação de desastre com êxito das bases de dados, deve também instalar um controlador de domínio de réplica no site de recuperação de desastre. |
| **Envio de registo** |Um servidor em execução na VM do Azure e a outro em execução no local para a recuperação de desastre em vários sites. O envio de registo depende em partilha de ficheiros do Windows, pelo que não é necessária uma ligação VPN entre a rede virtual do Azure e a rede no local.<br/>![Envio de registo](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_log_shipping.gif)<br/>Para a recuperação de desastre com êxito das bases de dados, deve também instalar um controlador de domínio de réplica no site de recuperação de desastre. |
| **Cópia de segurança e restauro com o serviço de armazenamento de Blobs do Azure** |No local bases de dados de produção uma cópia de segurança diretamente ao armazenamento de Blobs do Azure para recuperação após desastre.<br/>![Cópia de Segurança e Restauro](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_backup_restore.gif)<br/>Para obter mais informações, consulte [cópia de segurança e restaurar para o SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md). |
| **A replicação e ativação pós-falha de SQL Server para o Azure com o Azure Site Recovery** |No local do SQL Server replicados diretamente ao armazenamento do Azure para recuperação após desastre de produção.<br/>![Replicar utilizando o Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_standalone_sqlserver-asr.png)<br/>Para obter mais informações, consulte [proteger SQL Server com a recuperação de desastres do SQL Server e o Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Considerações importantes sobre o SQL Server HADR no Azure
VMs do Azure, armazenamento e redes têm características diferentes de operacionais que no local, infraestrutura de TI não virtualizados. Uma implementação efetuada com êxito de uma solução de HADR SQL Server no Azure requer que compreender estas diferenças e estruturar a solução para acomodá-los.

### <a name="high-availability-nodes-in-an-availability-set"></a>Nós de elevada disponibilidade num conjunto de disponibilidade
Os conjuntos de disponibilidade do Azure permitem-lhe colocar os nós de elevada disponibilidade em separado domínios de falhas (FDs) e domínios de atualização (UDs). Para as VMs do Azure ser colocados no mesmo conjunto de disponibilidade, tem de implementá-los no mesmo serviço em nuvem. Apenas nós no mesmo serviço em nuvem podem participar no mesmo conjunto de disponibilidade. Para obter mais informações, consulte o artigo [Gerir a Disponibilidade das Virtual Machines](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="failover-cluster-behavior-in-azure-networking"></a>Comportamento de cluster de ativação pós-falha no funcionamento em rede do Azure
O serviço DHCP não conformes RFC do Azure pode fazer com que a criação de ativação pós-falha de determinadas configurações de cluster a falhar devido a nome de rede do cluster que está a ser atribuído um endereço IP duplicado, tais como o mesmo endereço IP que um de nós do cluster. Este é um problema ao implementar grupos de disponibilidade, o qual depende a funcionalidade de cluster de ativação pós-falha do Windows.

Quando um cluster de dois nós é criado e colocado online, considere o cenário:

1. O cluster estiver online, em seguida, Nó1 pedidos de um endereço IP dinamicamente atribuído para o nome de rede de cluster.
2. Nenhum endereço IP que não seja o endereço IP do Nó1 é fornecido pelo serviço de DHCP, uma vez que o serviço DHCP reconhece que o pedido provenientes de Nó1 próprio.
3. Windows Deteta que é atribuído um endereço de duplicados para Nó1 e o nome de rede do cluster de ativação pós-falha e o grupo de cluster predefinido falha fique online.
4. Move o grupo de cluster predefinido para Nó2, que processa o endereço IP do Nó1 como endereço IP do cluster e coloca o grupo de cluster predefinido online.
5. Quando tenta estabelecer conectividade com Nó1 Nó2, pacotes direcionados em Nó1 nunca saem Nó2 porque resolve endereços IP do Nó1 para si próprio. Em seguida, Nó2 não é possível estabelecer a conectividade com Nó1 perder o quórum e encerra o cluster.
6. Entretanto, Nó1 podem enviar pacotes para Nó2, mas não pode responder Nó2. Nó1 perder o quórum e encerramento do cluster.

Este cenário pode ser evitado, atribuindo um não utilizado endereço IP estático, tal como um endereço IP local de ligação como 169.254.1.1, para o nome de rede de cluster para colocar o nome da rede de cluster online. Para simplificar este processo, consulte [cluster de ativação pós-falha do Windows configurar no Azure para grupos de disponibilidade](http://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Para obter mais informações, consulte [configurar grupos de disponibilidade no Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Suporte de serviço de escuta do grupo de disponibilidade
Escuta do grupo de disponibilidade é suportada em VMs do Azure com o Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016. Este suporte é possibilitado pela utilização de pontos finais com balanceamento de carga ativada nas VMs do Azure que são nós do grupo de disponibilidade. Tem de seguir os passos de configuração especial para as escutas de funcionar para ambas as aplicações de cliente que estão em execução no Azure, bem como os em execução no local.

Existem duas opções principais para configurar o serviço de escuta: externo (públicas) ou interno. O serviço de escuta externo (público) utiliza um internet com o Balanceador de carga e está associado com um IP Virtual público (VIP) que seja acessível através da internet. Um serviço de escuta interno utiliza um balanceador de carga interno e só suporta clientes na mesma rede Virtual. Para o tipo de Balanceador de carga, tem de ativar a devolução direta do servidor. 

Se o grupo de disponibilidade abranger várias sub-redes do Azure (por exemplo, uma implementação que atravesse regiões do Azure), a cadeia de ligação de cliente tem de incluir "**MultisubnetFailover = True**". Isto resulta em tentativas de ligação paralelas às réplicas em sub-redes diferentes. Para obter instruções sobre como configurar um serviço de escuta, consulte

* [Configurar um serviço de escuta do ILB para grupos de disponibilidade no Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
* [Configurar um serviço de escuta externo para grupos de disponibilidade no Azure](../classic/ps-sql-ext-listener.md).

Pode ainda ligar para cada réplica de disponibilidade em separado, ligando-se diretamente à instância de serviço. Além disso, uma vez que os grupos de disponibilidade são compatíveis com versões anteriores com clientes de espelhamento da base de dados, pode ligar às réplicas de disponibilidade como parceiros de espelhamento, desde que as réplicas são configuradas semelhante à base de dados de espelhamento da base de dados:

* Uma réplica primária e uma réplica secundária
* A réplica secundária está configurada como não legível (**secundário legível** opção definida como **não**)

Abaixo é uma cadeia de ligação de cliente de exemplo que corresponde a esta configuração de como o espelhamento da base de dados ADO.NET ou SQL Server Native Client a utilizar:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Para obter mais informações sobre a conectividade de clientes, consulte:

* [Utilizar palavras-chave de cadeia de ligação com o SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
* [Ligar a clientes para uma base de dados (SQL Server) de sessão de espelhamento](https://technet.microsoft.com/library/ms175484.aspx)
* [A ligar ao serviço de escuta do grupo de disponibilidade em TI híbridos](http://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Serviços de escuta do grupo de disponibilidade, a conectividade de clientes e a ativação pós-falha de aplicação (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Utilizar cadeias de ligação de espelhamento da base de dados com grupos de disponibilidade](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Latência de rede em TI híbridos
Deve implementar a solução HADR com pressuposto de que podem ser períodos de tempo com uma latência de rede elevado entre a rede no local e o Azure. Quando implementar réplicas para o Azure, deve utilizar consolidação assíncrona, em vez de consolidação síncrona para o modo de sincronização. Quando implementar servidores de espelhamento da base de dados no local tanto no Azure, utilize o modo de elevado desempenho, em vez do modo de alta segurança.

### <a name="geo-replication-support"></a>Suporte de georreplicação
Replicação geográfica dos discos do Azure não suporta o ficheiro de dados e o ficheiro de registo da mesma base de dados para ser armazenados em separado. GRS replica as alterações em cada disco independentemente e no modo assíncrono. Este mecanismo garante a ordem de escrita dentro de um único disco na copiar georreplicação, mas não em georreplicação cópias de vários discos. Se configurar uma base de dados para armazenar o ficheiro de dados e o respetivo ficheiro de registo em discos separados, os discos recuperados após desastres podem conter uma mais atualizadas à sua cópia do ficheiro de dados que o ficheiro de registo, o que interrompe o registo no SQL Server e as propriedades de transações ACID escrita antecipada. Se não tiver a opção para desativar a georreplicação na conta de armazenamento, deve manter todos os dados de ficheiros de registo e para uma determinada base de dados no mesmo disco. Se tiver de utilizar mais do que um disco porque o tamanho da base de dados, terá de implementar uma das soluções de recuperação após desastre listadas acima, para garantir redundância de dados.

## <a name="next-steps"></a>Passos seguintes
Se precisar de criar uma máquina virtual do Azure com o SQL Server, consulte [aprovisionamento de uma Máquina Virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

Para obter o melhor desempenho do SQL Server em execução numa VM do Azure, consulte a documentação de orientação [práticas recomendadas do SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

Para outros tópicos relacionados com a executar o SQL Server em VMs do Azure, consulte [do SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Outros recursos
* [Instalar uma nova floresta do Active Directory no Azure](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Criar o Cluster de ativação pós-falha para grupos de disponibilidade na VM do Azure](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

