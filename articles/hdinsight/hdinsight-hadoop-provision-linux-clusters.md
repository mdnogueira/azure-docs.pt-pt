---
title: "A configuração de cluster de Hadoop, Spark, Kafka, HBase ou servidor R - Azure HDInsight | Microsoft Docs"
description: Defina Hadoop, Kafka, Spark, HBase, R Server ou clusters de Storm para o HDInsight de um browser, a CLI do Azure, Azure PowerShell, REST ou SDK.
keywords: "configuração de cluster do hadoop, kafka cluster configuração, a configuração de cluster do spark, o que é o cluster de hadoop"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 23a01938-3fe5-4e2e-8e8b-3368e1bbe2ca
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: jgao
ms.openlocfilehash: c0f89f98c26b80e8b71c58fc89ea7ecebe734f71
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="set-up-clusters-in-hdinsight-with-hadoop-spark-kafka-and-more"></a>Configurar clusters no HDInsight com o Hadoop, Spark, Kafka e muito mais

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Saiba como configurar clusters no HDInsight com o Hadoop, Spark, Kafka, consulta interativa, HBase, servidor R ou Storm. Além disso, saiba como personalizar clusters e adicionam segurança ao associá-las a um domínio.

Um cluster do Hadoop é composta por várias máquinas virtuais (nós) são utilizadas para processamento distribuído de tarefas. O Azure HDInsight processa os detalhes de implementação da instalação e configuração de nós individuais, pelo que apenas terá de fornecer informações de configuração geral. 

> [!IMPORTANT]
>Faturação de cluster do HDInsight é iniciado depois de um cluster é criado e para quando o cluster é eliminado. Faturação é calculada por minuto, pelo que deve sempre eliminar o cluster quando já não está a ser utilizado. Saiba como [eliminar um cluster.](hdinsight-delete-cluster.md)
>

## <a name="cluster-setup-methods"></a>Métodos de configuração de cluster
A tabela seguinte mostra os diferentes métodos que pode utilizar para configurar um cluster do HDInsight.

| Clusters criados com | Browser da Web | Linha de comandos | API REST | SDK | 
| --- |:---:|:---:|:---:|:---:|
| [Portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [CLI do Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [SDK do .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |&nbsp; |&nbsp; |&nbsp; |✔ |
| [Modelos Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

## <a name="quick-create-basic-cluster-setup"></a>Criar rápido: configuração de cluster básico
Este artigo explica como a configuração no [portal do Azure](https://portal.azure.com), onde pode criar um cluster do HDInsight utilizando *criação rápida* ou *personalizada*. 

Siga as instruções no ecrã para efetuar uma configuração de cluster básico. Os detalhes são fornecidos abaixo para:

* [Nome do grupo de recursos](#resource-group-name)
* [Tipos de cluster e a configuração](#cluster-types) 
* [Início de sessão do cluster e o nome de utilizador do SSH](#cluster-login-and-ssh-username)
* [Localização](#location)

> [!IMPORTANT]
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, consulte [HDInsight 3.3 extinção](hdinsight-component-versioning.md#hdinsight-windows-retirement).
>

## <a name="resource-group-name"></a>Nome do grupo de recursos 

[O Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) ajuda a trabalhar com os recursos na sua aplicação como um grupo, referido como um grupo de recursos do Azure. Pode implementar, atualizar, monitorizar ou eliminar todos os recursos para a sua aplicação numa única operação coordenada.

## <a name="cluster-types"></a>Tipos de cluster e a configuração
O Azure HDInsight fornece atualmente os seguintes tipos de cluster, cada um com um conjunto de componentes para fornecer determinadas funcionalidades.

> [!IMPORTANT]
> Clusters do HDInsight estão disponíveis em vários tipos, cada para uma carga de trabalho única ou tecnologia. Não há nenhum método suportado para criar um cluster que combina vários tipos, por exemplo, Storm e HBase num cluster. Se a sua solução requer tecnologias que são distribuídas por vários tipos de cluster do HDInsight, uma [rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network) pode ligar os tipos de cluster necessários. 
>
>

| Tipo de cluster | Funcionalidade |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Análise dos dados armazenados e consulta de batch |
| [HBase](hbase/apache-hbase-overview.md) |Processamento de grandes quantidades de dados de NoSQL sem esquemas, |
| [Storm](storm/apache-storm-overview.md) |Processamento de eventos em tempo real |
| [Spark](spark/apache-spark-overview.md) |Processamento de memória, consultas interativas, processamento de fluxo micro batch |
| [Kafka (Pré-visualização)](kafka/apache-kafka-introduction.md) | Uma plataforma de transmissão em fluxo distribuída que pode ser utilizada para criar pipelines de dados de transmissão em fluxo em tempo real e aplicações |
| [R Server](r-server/r-server-overview.md) |Vários estatísticas de macrodados, modelação preditiva e capacidades de aprendizagem |
| [Consulta interativa](./interactive-query/apache-interactive-query-get-started.md) |Dentro da memória colocação em cache para mais rápidas e interativas consultas do Hive |

### <a name="number-of-nodes-for-each-cluster-type"></a>Número de nós para cada tipo de cluster
Cada tipo de cluster tem o suas próprias número de nós, terminologia para nós e o tamanho predefinido da VM. A tabela seguinte, é o número de nós para cada tipo de nó parênteses.

| Tipo | Nós | Diagrama |
| --- | --- | --- |
| Hadoop |Nó principal (2), o nó de dados (1 +) |![Nós de cluster do HDInsight Hadoop](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |Servidor HEAD (2), servidor de região (1 +), nó mestre/ZooKeeper (3) |![Nós de cluster HBase do HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nó nimbus (2), servidor supervisor (1 +), o nó de ZooKeeper (3) |![Nós de cluster do HDInsight Storm](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |Nó principal (2), o nó de trabalho (1 +), nós de ZooKeeper (3) (gratuita para o tamanho de VM de ZooKeeper A1) |![Nós de cluster do Spark do HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

Para obter mais informações, consulte [predefinido tamanhos de configuração e a máquina virtual do nó para clusters](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) no "Quais são os componentes do Hadoop e versões no HDInsight?"

### <a name="hdinsight-version"></a>Versão do HDInsight
Escolha a versão do HDInsight para este cluster. Para obter mais informações, consulte [as versões do HDInsight suportado](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="cluster-tiers"></a>Camada do cluster: camadas de serviços do HDInsight

O Azure HDInsight fornece ofertas de nuvem de macrodados em duas camadas de serviços: Standard e Premium.  Para obter mais informações, consulte [HDInsight Standard e HDInsight Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium).

A seguinte captura de ecrã mostra as informações do portal do Azure para escolher os tipos de cluster.

![Configuração do HDInsight premium](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-type-configuration.png)


## <a name="cluster-login-and-ssh-user-name"></a>Início de sessão do cluster e o nome de utilizador SSH
Com clusters do HDInsight, pode configurar duas contas de utilizador durante a criação do cluster:

* Utilizador HTTP: O nome de utilizador predefinido é *admin*. Utiliza a configuração básica no portal do Azure. Por vezes denominado "Utilizador em Cluster".
* Utilizador do SSH (clusters do Linux): utilizada para ligar ao cluster através de SSH. Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="location"></a>Localização (regiões) para armazenamento e clusters

Não precisa de especificar a localização do cluster explicitamente: O cluster está na mesma localização do armazenamento de predefinido. Para obter uma lista de regiões suportadas, clique o **região** na lista pendente no [preços do HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

## <a name="storage-endpoints-for-clusters"></a>Pontos finais de armazenamento para clusters

Embora uma instalação local do Hadoop utiliza o distribuídas ficheiro sistema Hadoop (HDFS) para o armazenamento de cluster, a nuvem utiliza pontos finais de armazenamento ligados ao cluster. A utilização de clusters do HDInsight uma [Azure Data Lake Store](hdinsight-hadoop-use-data-lake-store.md) ou [blobs no armazenamento do Azure](hdinsight-hadoop-use-blob-storage.md). A utilização do Storage do Azure ou de Data Lake Store significa que pode eliminar em segurança os clusters do HDInsight utilizados para o cálculo mantendo os dados. 

> [!WARNING]
> Não é suportada através de uma conta de armazenamento adicional numa localização diferente do HDInsight cluster.

Durante a configuração, para o ponto final de armazenamento de predefinido, especificar um contentor do blob de uma conta de armazenamento do Azure ou uma Data Lake Store. O armazenamento de predefinido contém aplicações e do sistema registos. Opcionalmente, pode especificar contas de armazenamento do Azure ligadas adicionais e contas de Data Lake Store que o cluster possa aceder. O cluster do HDInsight e as contas do storage dependente tem de ser na mesma localização do Azure.

![As definições de armazenamento de cluster: pontos finais de armazenamento compatível com HDFS](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-creation-storage.png)

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


### <a name="optional-metastores"></a>Metastores opcionais
Pode criar metastores Hive ou Oozie opcionais. No entanto, nem todos os tipos de cluster suportam metastores e Azure SQL Data Warehouse não é compatível com metastores. 

> [!IMPORTANT]
> Quando cria um metastore personalizado, não utilize travessões, hífenes ou espaços no nome de base de dados. Isto pode provocar a falha no processo de criação de cluster.

### <a name="use-hiveoozie-metastore"></a>Metastore do Hive

Se pretende manter as tabelas do Hive, depois de eliminar um cluster do HDInsight, utilize um metastore personalizado. Em seguida, pode anexar o metastore para outro cluster de HDInsight.

Não é possível partilhar um metastore do HDInsight que é criado para uma versão de cluster do HDInsight nas diferentes versões de cluster do HDInsight. Para obter uma lista das versões do HDInsight, consulte [as versões do HDInsight suportado](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="oozie-metastore"></a>Metastore do Oozie

Para melhorar o desempenho ao utilizar o Oozie, utilize um metastore personalizado. Um metastore também pode fornecer acesso aos dados da tarefa Oozie depois de eliminar o cluster. 

> [!IMPORTANT]
> Não é possível reutilizar um metastore do Oozie personalizado. Para utilizar um metastore do Oozie personalizado, tem de fornecer uma base de dados de SQL do Azure vazia ao criar o cluster do HDInsight.

## <a name="configure-cluster-size"></a>Configure um tamanho de cluster

É-lhe faturado para utilização do nó para, desde que o cluster existe. A faturação é iniciada quando um cluster é criado e para quando o cluster é eliminado. Clusters não não possível anular atribuídos ou colocar em espera.

O custo de clusters do HDInsight é determinado pelo número de nós e os tamanhos de máquinas virtuais para os nós. 

Tipos de cluster diferentes têm tipos de nó diferente, número de nós e tamanhos de nó:
* Predefinição do tipo de cluster de Hadoop: 
    * Dois *nós principais*  
    * Quatro *nós de dados*
* Predefinição de tipo de cluster do Storm: 
    * Dois *nós Nimbus*
    * Três *nós de ZooKeeper*
    * Quatro *nós supervisor* 

Se estiver apenas a tentar terminar o HDInsight, recomendamos que utilize um nó de dados. Para obter mais informações sobre preços do HDInsight, consulte [preços do HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]
> O limite de tamanho de cluster varia entre subscrições do Azure. Contacte [ao apoio de faturação do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) para aumentar o limite.
>

Quando utilizar o portal do Azure para configurar o cluster, o tamanho de nó está disponível através de **escalões de preço de nó** painel. No portal, também pode ver o custo associado os tamanhos de nó diferente. 

![HDInsight nó dos tamanhos da VM](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-node-sizes.png)

### <a name="virtual-machine-sizes"></a>Tamanhos de máquinas virtuais 
Quando implementar clusters, escolha os recursos de computação com base na solução de que pretende implementar. As VMs seguintes são utilizadas para clusters do HDInsight:
* A e as VMs de série do D1-4: [tamanhos de VM de Linux General-purpose](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* D11 14 série VM: [tamanhos de VM do Linux com otimização de memória](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Para saber o que valor que deverá utilizar para especificar um tamanho VM ao criar um cluster com os SDKs diferentes ou ao utilizar o Azure PowerShell, consulte [tamanhos de VM a utilizar para os clusters do HDInsight](../cloud-services/cloud-services-sizes-specs.md#size-tables). A partir deste artigo ligado, utilize o valor no **tamanho** coluna das tabelas.

> [!IMPORTANT]
> Se precisar de mais do que 32 nós de trabalho num cluster, tem de selecionar um tamanho de nó principal pelo menos 8 núcleos e 14 GB de RAM.
>
>

Para obter mais informações, consulte [tamanhos das virtual machines](../virtual-machines/windows/sizes.md). Para obter informações sobre preços dos tamanhos diversos, consulte [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).   

## <a name="custom-cluster-setup"></a>Configuração de cluster personalizado
Baseiam-se da configuração de cluster personalizado no rápido criar as definições e adiciona as seguintes opções:
- [Aplicações do HDInsight](#hdinsight-applications)
- [Tamanho do cluster](#cluster-size)
- Definições avançadas
  - [Ações de script](#customize-clusters-using-script-action)
  - [Rede virtual](#use-virtual-network)

## <a name="install-hdinsight-applications-on-clusters"></a>Instalar aplicações do HDInsight em clusters

Uma aplicação HDInsight é uma aplicação que os utilizadores podem instalar num cluster do HDInsight baseado em Linux. Pode utilizar aplicações fornecida pela Microsoft, terceiros ou que desenvolver por si. Para obter mais informações, consulte [instalar aplicações de terceiros do Hadoop no Azure HDInsight](hdinsight-apps-install-applications.md).

A maioria das aplicações do HDInsight é instalada num nó de extremidade vazio.  Um nó de extremidade vazia é uma máquina virtual do Linux com as mesmo ferramentas de cliente instalado e configurado como no nó principal. Pode utilizar o nó de extremidade para aceder ao cluster, teste as aplicações de cliente e que aloja as aplicações de cliente. Para obter mais informações, consulte [utilizar nós edge vazio no HDInsight](hdinsight-apps-use-edge-node.md).

## <a name="advanced-settings-script-actions"></a>As definições avançadas: ações de Script

Pode instalar componentes adicionais ou personalizar a configuração de cluster utilizando scripts durante a criação. Scripts são invocados através de **ação de Script**, que é uma opção de configuração que pode ser utilizada a partir do portal do Azure, cmdlets do HDInsight Windows PowerShell ou o SDK .NET do HDInsight. Para obter mais informações, consulte [através da ação de Script de cluster de HDInsight personalizar](hdinsight-hadoop-customize-cluster-linux.md).

Alguns componentes de Java nativas, como o Mahout e Cascading, podem ser executados no cluster como ficheiros de arquivo de Java (JAR). Estes ficheiros JAR podem ser distribuídos ao Storage do Azure e submetidos a clusters do HDInsight com mecanismos de submissão de tarefas do Hadoop. Para obter mais informações, consulte [Hadoop submeter as tarefas através de programação](hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]
> Se tiver problemas de implementação de ficheiros JAR em clusters do HDInsight, ou chamar JAR ficheiros nos clusters do HDInsight, contacte [Microsoft Support](https://azure.microsoft.com/support/options/).
>
> Cascata não é suportada pelo HDInsight e não é elegível para Support da Microsoft. Para apresenta uma lista dos componentes suportados, consulte [Novidades nas versões do cluster fornecidas pelo HDInsight](hdinsight-component-versioning.md).
>
>

Por vezes, pretende configurar os seguintes ficheiros de configuração durante o processo de criação:

* clusterIdentity.xml
* Core-site.XML
* gateway.XML
* hbase env.xml
* hbase site.xml
* hdfs site.xml
* env.xml do Hive
* site.xml do Hive
* mapred site
* oozie site.xml
* oozie env.xml
* Storm site.xml
* tez site.xml
* webhcat site.xml
* yarn site.xml

Para obter mais informações, consulte [HDInsight personalizar clusters com o arranque](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="advanced-settings-extend-clusters-with-a-virtual-network"></a>As definições avançadas: Expandir clusters com uma rede virtual
Se a sua solução requer tecnologias que são distribuídas por vários tipos de cluster do HDInsight, uma [rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network) pode ligar os tipos de cluster necessários. Esta configuração permite que os clusters e qualquer código que implementar aos mesmos, para comunicar diretamente entre si.

Para obter mais informações sobre como utilizar uma rede virtual do Azure com o HDInsight, consulte [expandir HDInsight com redes virtuais do Azure](hdinsight-extend-hadoop-virtual-network.md).

Para obter um exemplo de utilização de dois tipos de cluster dentro de uma rede virtual do Azure, consulte [analisar dados de sensor com o Storm e HBase](storm/apache-storm-sensor-data-analysis.md). Para obter mais informações sobre como utilizar o HDInsight com uma rede virtual, incluindo requisitos de configuração específicos para a rede virtual, consulte [capacidades de expandir HDInsight utilizando a Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).

## <a name="troubleshoot-access-control-issues"></a>Resolver problemas de controlo de acesso

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Passos seguintes

- [Quais são o HDInsight, ecossistema do Hadoop, e clusters do Hadoop?](hadoop/apache-hadoop-introduction.md)
- [Introdução à utilização do Hadoop no HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
- [Hadoop no HDInsight a partir do Windows PC de trabalho](hdinsight-hadoop-windows-tools.md)
