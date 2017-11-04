---
title: "Configurar a replicação de cluster HBase em redes virtuais do Azure | Microsoft Docs"
description: "Saiba como configurar a replicação do HBase de uma versão do HDInsight para outro para o balanceamento de carga, elevada disponibilidade, a migração de período de indisponibilidade de zero e atualizações e recuperação após desastre."
services: hdinsight,virtual-network
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/09/2017
ms.author: jgao
ms.openlocfilehash: 6d7c2eaf139ddbff46a2fba99bdf5515f64be40c
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="set-up-hbase-cluster-replication-in-azure-virtual-networks"></a>Configurar a replicação de cluster HBase em redes virtuais do Azure

Saiba como configurar a replicação do HBase dentro de uma rede virtual, ou entre duas redes virtuais no Azure.

A replicação de cluster utiliza uma metodologia de push de origem. Um cluster HBase pode ser uma origem ou destino ou pode satisfazer ambas as funções de uma só vez. A replicação é assíncrona. O objetivo da replicação é a consistência eventual. Quando a origem recebe uma edição para uma família de coluna quando a replicação está ativada, a edição é propagada para todos os clusters de destino. Quando os dados são replicados de um cluster para outro, o cluster de origem e de todos os clusters que já tenham consumido os dados são controlados, para evitar os ciclos de replicação.

Neste tutorial, configurou uma replicação de destino de origem. Para outras topologias de cluster, consulte o [guia de referência Apache HBase](http://hbase.apache.org/book.html#_cluster_replication).

Seguem-se HBase casos de utilização de replicação para uma única rede virtual:

* Balanceamento de carga. Por exemplo, pode executar análises ou as tarefas do MapReduce no cluster de destino e inserir dados no cluster de origem.
* A adicionar a elevada disponibilidade.
* Migrar dados de um cluster HBase para outro.
* Atualizar um cluster de Azure HDInsight a partir de uma versão para outro.

Seguem-se HBase casos de utilização de replicação para duas redes virtuais:

* Configurar a recuperação após desastre.
* Balanceamento de carga e a aplicação de criação de partições.
* A adicionar a elevada disponibilidade.

Pode replicar clusters utilizando [ação de script](../hdinsight-hadoop-customize-cluster-linux.md) scripts de [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter uma subscrição do Azure. Consulte [obtenha uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>Configurar os ambientes

Tem três opções de configuração:

- Dois clusters de HBase numa rede virtual do Azure.
- Dois clusters de HBase em duas redes virtuais diferentes na mesma região.
- Dois clusters de HBase em duas redes virtuais diferentes em duas regiões diferentes (georreplicação).

Para o ajudar a configurar os ambientes, criámos algumas [modelos Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Se preferir configurar os ambientes através da utilização de outros métodos, consulte:

- [Criar clusters do Hadoop no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Criar clusters do HBase no Azure Virtual Network](apache-hbase-provision-vnet.md)

### <a name="set-up-one-virtual-network"></a>Configurar uma rede virtual

Para criar dois clusters de HBase na mesma rede virtual, selecione a imagem seguinte. O modelo é armazenado em [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-one-vnet/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-one-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

### <a name="set-up-two-virtual-networks-in-the-same-region"></a>Configurar as duas redes virtuais na mesma região

Para criar duas redes virtuais com dois clusters de HBase na mesma região e o peering de rede virtual, selecione a imagem seguinte. O modelo é armazenado em [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-two-vnets-same-region/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-two-vnets-same-region%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>



Este cenário requer [peering de rede virtual](../../virtual-network/virtual-network-peering-overview.md). O modelo permite o peering de rede virtual.   

A replicação do HBase utiliza endereços IP das ZooKeeper VMs. Tem de configurar endereços IP estáticos para o destino HBase ZooKeeper nós.

**Para configurar endereços IP estáticos**

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No menu da esquerda, selecione **grupos de recursos**.
3. Selecione o grupo de recursos que tem o cluster de HBase de destino. Este é o grupo de recursos que especificou quando utilizou o modelo do Resource Manager para criar o ambiente. Pode utilizar o filtro para restringir a lista. Pode ver uma lista de recursos que contêm as duas redes virtuais.
4. Selecione a rede virtual que contém o cluster de HBase de destino. Por exemplo, seleccione **xxxx-vnet2**. Três dispositivos com nomes que começam com **nic-zookeepermode -** estão listados. Esses dispositivos são as VMs de ZooKeeper três.
5. Selecione uma das ZooKeeper VMs.
6. Selecione **configurações de IP**.
7. Na lista, selecione **ipConfig1**.
8. Selecione **estático**e copie ou anote o endereço IP real. Terá do endereço IP quando executar a ação de script para ativar a replicação.

  ![Replicação do HBase do HDInsight endereço IP estático ZooKeeper](./media/apache-hbase-replication/hdinsight-hbase-replication-zookeeper-static-ip.png)

9. Repita o passo 6 para definir o endereço IP estático para os outros dois nós de ZooKeeper.

Para o cenário de rede entre virtual, tem de utilizar o **- ip** mudar quando chamar o `hdi_enable_replication.sh` ação de script.

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Configurar as duas redes virtuais em duas regiões diferentes

Para criar duas redes virtuais em duas regiões diferentes e a ligação VPN entre as VNets, clique na imagem seguinte. O modelo é armazenado no [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-geo%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

Alguns dos valores no modelo hard-coded:

**1 de VNet**

| Propriedade | Valor |
|----------|-------|
| Localização | EUA Oeste |
| Nome da VNet | &lt;ClusterNamePrevix >-vnet1 |
| Prefixo de espaço de endereço | 10.1.0.0/16 |
| Nome da sub-rede | sub-rede 1 |
| Prefixo de sub-rede | 10.1.0.0/24 |
| Nome da sub-rede (gateway) | GatewaySubnet (não é possível alterar) |
| Prefixo de sub-rede (gateway) | 10.1.255.0/27 |
| Nome do gateway | vnet1gw |
| Tipo de gateway | Vpn |
| Tipo de VPN do gateway | RouteBased |
| SKU de gateway | Básica |
| IP do gateway | vnet1gwip |
| Nome do cluster | &lt;ClusterNamePrefix > 1 |
| Versão do cluster | 3.6 |
| Tipo de cluster | hbase |
| Número de nós de trabalho de cluster | 2 |


**2 de VNet**

| Propriedade | Valor |
|----------|-------|
| Localização | EUA Leste |
| Nome da VNet | &lt;ClusterNamePrevix >-vnet2 |
| Prefixo de espaço de endereço | 10.2.0.0/16 |
| Nome da sub-rede | sub-rede 1 |
| Prefixo de sub-rede | 10.2.0.0/24 |
| Nome da sub-rede (gateway) | GatewaySubnet (não é possível alterar) |
| Prefixo de sub-rede (gateway) | 10.2.255.0/27 |
| Nome do gateway | vnet2gw |
| Tipo de gateway | Vpn |
| Tipo de VPN do gateway | RouteBased |
| SKU de gateway | Básica |
| IP do gateway | vnet1gwip |
| Nome do cluster | &lt;ClusterNamePrefix > 2 |
| Versão do cluster | 3.6 |
| Tipo de cluster | hbase |
| Número de nós de trabalho de cluster | 2 |

A replicação do HBase utiliza os endereços IP das ZooKeeper VMs. Tem de configurar endereços IP estáticos para o destino HBase ZooKeeper nós. Definir o IP estático, consulte [configurar duas redes virtuais na mesma região](#set-up-two-virtual-networks-in-the-same-region) neste artigo.

Para o cenário de rede entre virtual, tem de utilizar o **- ip** mudar quando chamar o `hdi_enable_replication.sh` ação de script.

## <a name="load-test-data"></a>Dados de teste de carga

Quando se replica um cluster, tem de especificar as tabelas que pretende replicar. Nesta secção, pode carrega alguns dados para o cluster de origem. Na secção seguinte, irá ativar a replicação entre dois clusters.

Para criar um **contactos** tabela e inserir alguns dados na tabela, siga as instruções apresentadas em [tutorial do HBase: introdução à utilização do Apache HBase no HDInsight](apache-hbase-tutorial-get-started-linux.md).

## <a name="enable-replication"></a>Ativar a replicação

Os passos seguintes descrevem como chamar o script de ação de script do portal do Azure. Para obter informações sobre como executar uma ação de script através do Azure PowerShell e a ferramenta de linha de comandos do Azure (CLI do Azure), consulte [clusters do HDInsight personalizar utilizando a ação de script](../hdinsight-hadoop-customize-cluster-linux.md).

**Para ativar a replicação do HBase do portal do Azure**

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Abra o cluster de HBase de origem.
3. No menu do cluster, selecione **ações de Script**.
4. Na parte superior da página, selecione **submeter novo**.
5. Selecione ou introduza as seguintes informações:

  1. **Nome**: introduza **ativar a replicação**.
  2. **URL de scripts de bash**: introduza **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**.
  3.  **HEAD**: Certifique-se de que esta opção está selecionada. Desmarque os outros tipos de nó.
  4. **Os parâmetros**: os parâmetros de exemplo seguintes ativar a replicação para todas as tabelas existentes e, em seguida, copie todos os dados do cluster de origem para o cluster de destino:

          -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
    >[!note]
    >
    > Utilize nome de anfitrião em vez do FQDN do nome DNS de cluster de origem e de destino.

6. Selecione **Criar**. O script pode demorar algum tempo para executar, especialmente quando utiliza o **- copydata** argumento.

Argumentos necessários:

|Nome|Descrição|
|----|-----------|
|-s, - src-cluster | Especifica o nome DNS de cluster de HBase de origem. Por exemplo: hbsrccluster -s, de cluster – src = hbsrccluster |
|-d, – dst-cluster | Especifica o nome DNS de cluster de HBase de destino (réplica). Por exemplo: dsthbcluster -s, de cluster – src = dsthbcluster |
|-sp, – src-ambari-palavra-passe | Especifica a palavra-passe de administrador para Ambari no cluster de HBase de origem. |
|-ponto de distribuição, – dst-ambari-palavra-passe | Especifica a palavra-passe de administrador para Ambari no cluster de HBase de destino.|

Argumentos opcionais:

|Nome|Descrição|
|----|-----------|
|-su, – src-ambari-utilizador | Especifica o nome de utilizador de administrador para Ambari no cluster de HBase de origem. O valor predefinido é **admin**. |
|-du, – dst-ambari-utilizador | Especifica o nome de utilizador de administrador para Ambari no cluster de HBase de destino. O valor predefinido é **admin**. |
|-t, - lista de tabela | Especifica as tabelas replicadas. Por exemplo: – tabela lista = "tabela1; table2; table3". Se não especificar as tabelas, todas as tabelas de HBase existentes são replicadas.|
|-m, – máquina | Especifica o nó principal, onde é executada a ação de script. O valor é **hn1** ou **hn0**. Porque o **hn0** normalmente nó principal é busier, recomendamos que utilize **hn1**. Utilize esta opção se estiver a executar o script de $0 como uma ação de script do HDInsight portal ou do Azure PowerShell.|
|-ip | É necessário quando estiver a ativar a replicação entre duas redes virtuais. Este argumento atua como um comutador para utilizar os endereços IP estáticos de nós de ZooKeeper de clusters de réplica em vez de nomes do FQDN. Tem de pré-configurar os endereços IP estáticos antes de ativar a replicação. |
|-cp, - copydata | Permite a migração dos dados existentes em tabelas em que a replicação está ativada. |
|-rpm, - replicar-phoenix-meta | Ativa a replicação em tabelas de sistema Phoenix. <br><br>*Utilize esta opção com cuidado.* Recomendamos que crie novamente as tabelas de Phoenix em clusters de réplica antes de utilizar este script. |
|-h, – ajuda | Mostra informações de utilização. |

O `print_usage()` secção o [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) tem uma explicação detalhada de parâmetros.

Depois da ação de script é implementada com êxito, pode utilizar o SSH para ligar ao cluster de HBase de destino e, em seguida, certifique-se de que os dados foram replicados.

### <a name="replication-scenarios"></a>Cenários de replicação

A lista seguinte mostra alguns casos de utilização geral e as respetivas definições de parâmetro:

- **Ativar replicação de todas as tabelas entre dois clusters**. Este cenário não necessita de copiar ou migração de dados existentes nas tabelas e não utilize tabelas Phoenix. Utilize os seguintes parâmetros:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Ativar a replicação em tabelas específicas**. Para ativar a replicação tabela1, table2 e table3, utilize os seguintes parâmetros:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Ativar a replicação em tabelas específicas e copiar os dados existentes**. Para ativar a replicação tabela1, table2 e table3, utilize os seguintes parâmetros:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Ativar replicação de todas as tabelas e replicar Phoenix metadados a partir da origem para destino**. O Phoenix a replicação de metadados não é perfeita. Utilizá-lo com cuidado. Utilize os seguintes parâmetros:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Copiar e migrar dados

Existem dois scripts de ação de script separado disponíveis para copiar ou migração de dados após a replicação está ativada:

- [Script para tabelas pequenas](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (tabelas que sejam alguns na cópia de tamanho e global deverá concluir em menos de uma hora)

- [Script para tabelas grandes](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (tabelas que devem demorar mais de uma hora para copiar)

Pode seguir o mesmo procedimento descrito [ativar a replicação](#enable-replication) para chamar a ação de script. Utilize os seguintes parâmetros:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

O `print_usage()` secção o [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) tem uma descrição detalhada dos parâmetros.

### <a name="scenarios"></a>Cenários

- **Copie a tabelas específicas (test1, test2 e test3) para todas as linhas editá-lo até agora (carimbo de hora atual)**:

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  Ou:

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Copie a tabelas específicas com um intervalo de tempo especificado**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Desative a replicação

Para desativar a replicação, utilize outro script de ação de script de [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Pode seguir o mesmo procedimento descrito [ativar a replicação](#enable-replication) para chamar a ação de script. Utilize os seguintes parâmetros:

    -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

O `print_usage()` secção o [script](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) tem uma explicação detalhada de parâmetros.

### <a name="scenarios"></a>Cenários

- **Desative a replicação em todas as tabelas**:

        -m hn1 -s <source cluster DNS name> -sp Mypassword\!789 -all
  ou

        --src-cluster=<source cluster DNS name> --dst-cluster=<destination cluster DNS name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **Desative a replicação em tabelas especificadas (table1, table2 e table3)**:

        -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a configurar a replicação do HBase dentro de uma rede virtual, ou entre duas redes virtuais. Para saber mais sobre o HBase e o HDInsight, consulte estes artigos:

* [Introdução ao Apache HBase no HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Descrição geral do HBase do HDInsight](./apache-hbase-overview.md)
* [Criar clusters do HBase no Azure Virtual Network](./apache-hbase-provision-vnet.md)
* [Analisar dados de sensor com o Storm e HBase no HDInsight (Hadoop)](../storm/apache-storm-sensor-data-analysis.md)
