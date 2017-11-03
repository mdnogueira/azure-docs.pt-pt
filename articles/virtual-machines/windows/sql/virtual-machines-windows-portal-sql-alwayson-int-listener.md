---
title: "Criar um serviço de escuta do grupo de disponibilidade do SQL Server em virtual machines do Azure | Microsoft Docs"
description: "Instruções passo a passo para criar um serviço de escuta para um grupo de disponibilidade Always On para o SQL Server em virtual machines do Azure"
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/01/2017
ms.author: mikeray
ms.openlocfilehash: 09fed7e785708d4afe64905de973becc188181d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-load-balancer-for-an-always-on-availability-group-in-azure"></a>Configurar um balanceador de carga para um grupo de disponibilidade Always On no Azure
Este artigo explica como criar um balanceador de carga para um grupo de disponibilidade SQL Server Always On em máquinas virtuais do Azure que estejam a executar com o Azure Resource Manager. Um grupo de disponibilidade necessita de um balanceador de carga quando são instâncias do SQL Server em virtual machines do Azure. O Balanceador de carga armazena o endereço IP para o serviço de escuta do grupo de disponibilidade. Se um grupo de disponibilidade abranger várias regiões, cada região necessita de um balanceador de carga.

Para concluir esta tarefa, tem de ter um grupo de disponibilidade do SQL Server implementado em máquinas virtuais do Azure que estejam a executar com o Resource Manager. Máquinas virtuais do SQL Server têm de pertencer ao mesmo conjunto de disponibilidade. Pode utilizar o [modelo Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) para criar automaticamente o grupo de disponibilidade no Gestor de recursos. Este modelo cria automaticamente um balanceador de carga interno para si. 

Se preferir, pode [configurar manualmente a um grupo de disponibilidade](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Este artigo requer que os grupos de disponibilidade já estão configurados.  

Tópicos relacionados incluem:

* [Configurar grupos de disponibilidade Always On na VM do Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
* [Configurar uma ligação VNet a VNet através do Azure Resource Manager e o PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Orientando através deste artigo, pode criar e configurar um balanceador de carga no portal do Azure. Depois do processo estiver concluído, configurar o cluster para utilizar o endereço IP do Balanceador de carga para o serviço de escuta do grupo de disponibilidade.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Criar e configurar o Balanceador de carga no portal do Azure
Nesta parte da tarefa, efetue o seguinte:

1. No portal do Azure, crie o Balanceador de carga e configurar o endereço IP.
2. Configure o conjunto de back-end.
3. Crie a sonda. 
4. Defina a regras de balanceamento de carga.

> [!NOTE]
> Se as instâncias do SQL Server estão em vários grupos de recursos e regiões, execute cada passo duas vezes, uma vez em cada grupo de recursos.
> 
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>Passo 1: Criar o Balanceador de carga e configurar o endereço IP
Em primeiro lugar, crie o Balanceador de carga. 

1. No portal do Azure, abra o grupo de recursos que contém as máquinas virtuais do SQL Server. 

2. No grupo de recursos, clique em **adicionar**.

3. Procurar **Balanceador de carga** e, em seguida, nos resultados da pesquisa, selecione **Load Balancer**, que é publicada pelo **Microsoft**.

4. No **Balanceador de carga** painel, clique em **criar**.

5. No **criar Balanceador de carga** diálogo caixa, configure o Balanceador de carga da seguinte forma:

   | Definição | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto que representa o Balanceador de carga. Por exemplo, **sqlLB**. |
   | **Tipo** |**Interno**: maioria das implementações de utilizar um balanceador de carga interno, que permite que aplicações dentro da mesma rede virtual ligar ao grupo de disponibilidade.  </br> **Externo**: permite que as aplicações ligar ao grupo de disponibilidade através de uma ligação à Internet pública. |
   | **Rede virtual** |Selecione a rede virtual que estão as intances do SQL Server. |
   | **Sub-rede** |Selecione a sub-rede que instâncias do SQL Server estão em. |
   | **Atribuição de endereços IP** |**Estática** |
   | **Endereço IP privado** |Especifique um endereço IP disponível da sub-rede. Utilize este endereço IP quando criar um serviço de escuta no cluster. Num script do PowerShell, neste artigo, utilize este endereço para o `$ILBIP` variável. |
   | **Subscrição** |Se tiver várias subscrições, poderá ser apresentado este campo. Selecione a subscrição que pretende associar este recurso. Normalmente, é a mesma subscrição que todos os recursos para o grupo de disponibilidade. |
   | **Grupo de recursos** |Selecione o grupo de recursos que são instâncias do SQL Server no. |
   | **Localização** |Selecione a localização do Azure que as instâncias do SQL Server estão em. |

6. Clique em **Criar**. 

O Azure cria o Balanceador de carga. O Balanceador de carga pertence a uma rede específicas, sub-rede, grupo de recursos e localização. Depois de Azure concluída a tarefa, verifique as definições do Balanceador de carga no Azure. 

### <a name="step-2-configure-the-back-end-pool"></a>Passo 2: Configurar o conjunto de back-end
Azure chama o conjunto de endereços de back-end *conjunto back-end*. Neste caso, o conjunto de back-end é os endereços de duas instâncias de SQL Server no seu grupo de disponibilidade. 

1. No seu grupo de recursos, clique o Balanceador de carga que criou. 

2. No **definições**, clique em **conjuntos back-end**.

3. No **conjuntos back-end**, clique em **adicionar** para criar um conjunto de endereços de back-end. 

4. No **adicionar conjunto back-end**, em **nome**, escreva um nome para o conjunto de back-end.

5. Em **máquinas virtuais**, clique em **adicionar uma máquina virtual**. 

6. Em **escolher as máquinas virtuais**, clique em **escolher um conjunto de disponibilidade**e, em seguida, especifique o conjunto de disponibilidade que as máquinas virtuais do SQL Server pertence ao.

7. Depois de escolher o conjunto de disponibilidade, clique em **escolher as máquinas virtuais**, selecione as duas máquinas virtuais que alojem instâncias do SQL Server no grupo de disponibilidade e, em seguida, clique em **selecione**. 

8. Clique em **OK** para fechar os painéis para **escolher as máquinas virtuais**, e **adicionar conjunto back-end**. 

Azure atualiza as definições para o conjunto de endereços de back-end. O conjunto de disponibilidade tem agora um agrupamento de duas instâncias do SQL Server.

### <a name="step-3-create-a-probe"></a>Passo 3: Criar uma sonda
A sonda define a forma como o Azure verifica que instâncias do SQL Server atualmente possui o serviço de escuta do grupo de disponibilidade. O serviço com base no endereço IP de uma porta que definem quando criar a sonda as sondas Azure.

1. No balanceador de carga **definições** painel, clique em **sondas de estado de funcionamento**. 

2. No **sondas de estado de funcionamento** painel, clique em **adicionar**.

3. Configurar a sonda no **adicionar sonda** painel. Utilize os seguintes valores para configurar a sonda:

   | Definição | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto que representa a sonda. Por exemplo, **SQLAlwaysOnEndPointProbe**. |
   | **Protocolo** |**TCP** |
   | **Porta** |Pode utilizar qualquer porta disponível. Por exemplo, *59999*. |
   | **Intervalo** |*5* |
   | **Limiar de mau estado de funcionamento** |*2* |

4.  Clique em **OK**. 

> [!NOTE]
> Certifique-se de que a porta especificada está aberta na firewall de ambas as instâncias do SQL Server. Ambas as instâncias requerem uma regra de entrada para a porta TCP que utiliza. Para obter mais informações, consulte [adicionar ou Editar regra de Firewall](http://technet.microsoft.com/library/cc753558.aspx). 
> 
> 

O Azure cria a pesquisa e, em seguida, utiliza-o para testar a instância do SQL Server tem o serviço de escuta para o grupo de disponibilidade.

### <a name="step-4-set-the-load-balancing-rules"></a>Passo 4: Definir a regras de balanceamento de carga
As regras de balanceamento de carga configurar como o load balancer encaminha o tráfego para instâncias do SQL Server. Para este Balanceador de carga, ativar a devolução direta do servidor porque apenas uma das duas instâncias do SQL Server é o proprietário do recurso de serviço de escuta do grupo de disponibilidade cada vez.

1. No balanceador de carga **definições** painel, clique em **as regras de balanceamento de carga**. 

2. No **as regras de balanceamento de carga** painel, clique em **adicionar**.

3. No **as regras de balanceamento de carga de adicionar** painel, configurar a regra de balanceamento de carga. Utilize as seguintes definições: 

   | Definição | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto que representa a regras de balanceamento de carga. Por exemplo, **SQLAlwaysOnEndPointListener**. |
   | **Protocolo** |**TCP** |
   | **Porta** |*1433* |
   | **Porta de back-end** |*1433*. Este valor é ignorado porque esta regra utiliza **IP flutuante (devolução direta do servidor)**. |
   | **Sonda** |Utilize o nome da sonda que criou para este Balanceador de carga. |
   | **Persistência da sessão** |**Nenhum** |
   | **Tempo limite de inatividade (minutos)** |*4* |
   | **Vírgula flutuante (devolução direta do servidor) de IP** |**Ativado** |

   > [!NOTE]
   > Poderá ter de se deslocar para baixo do painel para ver todas as definições.
   > 

4. Clique em **OK**. 
5. Azure configura a regra de balanceamento de carga. Agora, o Balanceador de carga está configurado para encaminhar o tráfego para a instância do SQL Server que aloja o serviço de escuta para o grupo de disponibilidade. 

Neste momento, o grupo de recursos possui um balanceador de carga que se liga a ambas as máquinas do SQL Server. O Balanceador de carga também contém um endereço IP para a SQL Server Always On escuta do grupo disponibilidade, para que a máquina pode responder a pedidos para os grupos de disponibilidade.

> [!NOTE]
> Se as instâncias do SQL Server estão em duas regiões separadas, repita os passos na outra região. Cada região necessita de um balanceador de carga. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configurar o cluster para utilizar o endereço IP do Balanceador de carga
O passo seguinte consiste em configurar o serviço de escuta no cluster e colocar o serviço de escuta online. Faça o seguinte: 

1. Crie o serviço de escuta do grupo de disponibilidade no cluster de ativação pós-falha. 

2. Coloque online o serviço de escuta.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>Passo 5: Criar o serviço de escuta do grupo de disponibilidade no cluster de ativação pós-falha
Neste passo, cria manualmente o serviço de escuta do grupo de disponibilidade no Gestor de clusters de ativação pós-falha e o SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>Verificar a configuração do serviço de escuta

Se os recursos do cluster e as dependências estão configuradas corretamente, deverá conseguir ver o serviço de escuta do SQL Server Management Studio. Para definir a porta do serviço de escuta, efetue o seguinte:

1. Iniciar o SQL Server Management Studio e, em seguida, ligue para a réplica primária.

2. Aceda a **AlwaysOn alta disponibilidade** > **grupos de disponibilidade** > **serviços de escuta do grupo de disponibilidade**.  
    Deverá ver o nome do serviço de escuta que criou no Gestor de clusters de ativação pós-falha. 

3. O nome do serviço de escuta com o botão direito e, em seguida, clique em **propriedades**.

4. No **porta** caixa, especifique o número de porta para o serviço de escuta do grupo de disponibilidade com $EndpointPort que utilizou anteriormente (era a predefinição de 1433) e, em seguida, clique em **OK**.

Tem agora um grupo de disponibilidade em máquinas virtuais do Azure em execução no modo Resource Manager. 

## <a name="test-the-connection-to-the-listener"></a>Testar a ligação ao serviço de escuta
Teste a ligação da seguinte forma:

1. RDP para uma instância do SQL Server que está a ser a mesma rede virtual, mas não possui a réplica. Este servidor pode ser outra instância do SQL Server no cluster.

2. Utilize **sqlcmd** utilitário para testar a ligação. Por exemplo, o script seguinte estabelece uma **sqlcmd** ligação para a réplica primária através do serviço de escuta com autenticação do Windows:
   
        sqlcmd -S <listenerName> -E

A ligação de SQLCMD automaticamente estabelece ligação à instância do SQL Server que aloja a réplica primária. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Criar um endereço IP para um grupo de disponibilidade adicionais

Cada grupo de disponibilidade utiliza um serviço de escuta separado. Cada serviço de escuta possui o seu próprio endereço IP. Utilize o mesmo Balanceador de carga para conter o endereço IP para as escutas de adicionais. Depois de criar um grupo de disponibilidade, adicione o endereço IP para o Balanceador de carga e, em seguida, configure o serviço de escuta.

Para adicionar um endereço IP a um balanceador de carga com o portal do Azure, efetue o seguinte:

1. No portal do Azure, abra o grupo de recursos que contém o Balanceador de carga e, em seguida, clique o Balanceador de carga. 

2. Em **definições**, clique em **conjunto IP de front-end**e, em seguida, clique em **adicionar**. 

3. Em **adicionar endereço IP de front-end**, atribua um nome para o front-end. 

4. Certifique-se de que o **rede Virtual** e **sub-rede** são os mesmos que as instâncias do SQL Server.

5. Defina o endereço IP para o serviço de escuta. 
   
   >[!TIP]
   >Pode definir o endereço IP estático e escreva um endereço que não é atualmente utilizado na sub-rede. Em alternativa, pode definir o endereço IP dinâmico e guarde o novo conjunto IP Front-end. Se o fizer, o portal do Azure atribui automaticamente um endereço IP disponível para o conjunto. Pode, em seguida, volte a abrir o conjunto IP Front-end e alterar a atribuição de para estático. 

6. Guarde o endereço IP para o serviço de escuta. 

7. Adicione uma pesquisa de estado de funcionamento, utilizando as seguintes definições:

   |Definição |Valor
   |:-----|:----
   |**Nome** |Um nome para identificar a pesquisa.
   |**Protocolo** |TCP
   |**Porta** |Uma porta não utilizada TCP, que tem de estar disponível em todas as máquinas virtuais. Não pode ser utilizado para qualquer outra finalidade. Não existem dois serviços de escuta podem utilizar a mesma porta de pesquisa. 
   |**Intervalo** |A quantidade de tempo entre tentativas da sonda. Utilize a predefinição (5).
   |**Limiar de mau estado de funcionamento** |O número de limiares consecutivos que devem falhar antes de uma máquina virtual é considerada em mau estado de funcionamento.

8. Clique em **OK** para guardar a sonda. 

9. Crie uma regra de balanceamento de carga. Clique em **as regras de balanceamento de carga**e, em seguida, clique em **adicionar**.

10. Configure a carga nova regra de balanceamento utilizando as seguintes definições:

   |Definição |Valor
   |:-----|:----
   |**Nome** |Um nome para identificar a regra de balanceamento de carga. 
   |**Endereço IP de front-end** |Selecione o endereço IP que criou. 
   |**Protocolo** |TCP
   |**Porta** |Utilize a porta que instâncias do SQL Server estiver a utilizar. Uma instância predefinida utiliza a porta 1433, a menos que o alterado. 
   |**Porta de back-end** |Utilizar o mesmo valor que **porta**.
   |**Conjunto back-end** |O conjunto que contém as máquinas virtuais com as instâncias do SQL Server. 
   |**Sonda de estado de funcionamento** |Escolha a sonda que criou.
   |**Persistência da sessão** |Nenhuma
   |**Tempo limite de inatividade (minutos)** |Predefinição (4)
   |**Vírgula flutuante (devolução direta do servidor) de IP** | Ativado

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>Configurar o grupo de disponibilidade para utilizar o novo endereço IP

Para concluir a configuração do cluster, repita os passos que seguiu quando é efetuado o primeiro grupo de disponibilidade. Ou seja, configura o [cluster utilizar o novo endereço IP](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

Depois de ter adicionado um endereço IP para o serviço de escuta, configure o grupo de disponibilidade adicionais da seguinte forma: 

1. Certifique-se de que a porta de pesquisa para o novo endereço IP está aberta em ambas as máquinas virtuais do SQL Server. 

2. [No Gestor de clusters, adicionar o ponto de acesso de cliente](#addcap).

3. [Configurar o recurso IP para o grupo de disponibilidade](#congroup).

   >[!IMPORTANT]
   >Quando cria o endereço IP, utilize o endereço IP que adicionou ao balanceador de carga.  

4. [Se o recurso do grupo de disponibilidade do SQL Server dependentes no ponto de acesso de cliente](#dependencyGroup).

5. [Marca o recurso de ponto de acesso de cliente dependente do endereço IP](#listname).
 
6. [Definir os parâmetros de cluster no PowerShell](#setparam).

Depois de configurar o grupo de disponibilidade para utilizar o novo endereço IP, configure a ligação ao serviço de escuta. 

## <a name="next-steps"></a>Passos seguintes

- [Configurar um grupo de disponibilidade SQL Server Always On em máquinas virtuais do Azure em regiões diferentes](virtual-machines-windows-portal-sql-availability-group-dr.md)
