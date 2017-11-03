---
title: Elevada disponibilidade para o Hadoop - Azure HDInsight | Microsoft Docs
description: "Saiba como o clusters do HDInsight melhorar a fiabilidade e disponibilidade, utilizando um nó principal adicional. Saiba como este tem impacto nos serviços do Hadoop, como Ambari e do Hive, bem como ligar individualmente para cada nó principal utilizando SSH."
services: hdinsight
editor: cgronlun
manager: jhubbard
author: Blackmist
documentationcenter: 
tags: azure-portal
keywords: hadoop elevada disponibilidade
ms.assetid: 99c9f59c-cf6b-4529-99d1-bf060435e8d4
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/19/2017
ms.author: larryfr
ms.openlocfilehash: 39894ba73c691ad547d8b5ab67ec9d5786a5229c
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="availability-and-reliability-of-hadoop-clusters-in-hdinsight"></a>Disponibilidade e fiabilidade dos clusters Hadoop no HDInsight.

Clusters do HDInsight fornecem dois nós principais para aumentar a disponibilidade e fiabilidade dos serviços do Hadoop e tarefas em execução.

Hadoop permite elevada disponibilidade e fiabilidade através da replicação de serviços e dados em vários nós num cluster. No entanto distribuições padrão do Hadoop têm, normalmente, apenas um único nó principal. Qualquer falha de nó único principal pode fazer com que o cluster para parar de funcionar. O HDInsight fornece dois headnodes para melhorar a disponibilidade e fiabilidade do Hadoop.

> [!IMPORTANT]
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a name="availability-and-reliability-of-nodes"></a>Disponibilidade e fiabilidade de nós

Nós no cluster do HDInsight são implementados através de Virtual Machines do Azure. As secções seguintes abordam os tipos de nó individual utilizados com o HDInsight. 

> [!NOTE]
> Nem todos os tipos de nó são utilizados para um tipo de cluster. Por exemplo, um tipo de cluster do Hadoop não tem quaisquer nós Nimbus. Para obter mais informações sobre nós utilizada pelos tipos de cluster do HDInsight, consulte a secção de tipos de Cluster do [clusters do Hadoop baseados em criar Linux no HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types) documento.

### <a name="head-nodes"></a>Nós de cabeçalho

Para garantir a elevada disponibilidade dos serviços do Hadoop, o HDInsight fornece dois nós principais. Ambos os nós principais são Active Directory e em execução no cluster do HDInsight em simultâneo. Alguns serviços, tais como HDFS ou YARN, apenas são 'active', no nó principal de um determinado momento. Outros serviços, tais como HiveServer2 ou MetaStore do Hive estão ativos em ambos os nós principais ao mesmo tempo.

Nós HEAD (e outros nós do HDInsight) tem um valor numérico como parte do nome de anfitrião do nó. Por exemplo, `hn0-CLUSTERNAME` ou `hn4-CLUSTERNAME`.

> [!IMPORTANT]
> Não associe o valor numérico se um nó é primário ou secundário. O valor numérico encontra-se apenas para fornecer um nome exclusivo para cada nó.

### <a name="nimbus-nodes"></a>Nós do Nimbus

Nós nimbus estão disponíveis com clusters de Storm. Os nós Nimbus fornecem uma funcionalidade semelhante ao JobTracker do Hadoop através de distribuição e monitorização processamento através de nós de trabalho. O HDInsight fornece dois nós Nimbus para Storm clusters

### <a name="zookeeper-nodes"></a>Nós de zookeeper

[ZooKeeper](http://zookeeper.apache.org/) nós são utilizados para eleição leader principal serviços em nós principais. Também são utilizados para assegurar que os serviços, nós de dados (trabalho) e gateways saber que nó principal mestra do serviço está ativo no. Por predefinição, o HDInsight fornece três nós de ZooKeeper.

### <a name="worker-nodes"></a>Nós de trabalho

Nós de trabalho efetuar a análise de dados real, quando uma tarefa for submetida para o cluster. Se falhar um nó de trabalho, a tarefa que foi efetuar é submetida para outro nó de trabalho. Por predefinição, o HDInsight cria quatro nós de trabalho. Pode alterar este número de acordo com as suas necessidades, durante e após a criação do cluster.

### <a name="edge-node"></a>Nó de extremidade

Um nó de extremidade não participar ativamente na análise de dados do cluster. É utilizado pelos programadores ou cientistas de dados ao trabalhar com o Hadoop. O nó de extremidade se encontra no mesmo Azure Virtual Network dos outros nós do cluster e pode aceder diretamente a todos os outros nós. O nó de extremidade pode ser utilizado sem colocar os recursos na direção oposta ao serviços críticos do Hadoop ou as tarefas de análise.

Atualmente, o servidor R no HDInsight é o único tipo de cluster que fornece um nó de extremidade por predefinição. Para o servidor R no HDInsight, é utilizado o nó de extremidade teste R código localmente no nó antes de submetê-lo para o cluster de processamento distribuído.

Para obter informações sobre a utilização de um nó de extremidade com tipos de cluster que não seja o R Server, consulte o [utilizar nós edge no HDInsight](hdinsight-apps-use-edge-node.md) documento.

## <a name="accessing-the-nodes"></a>Aceder a nós

Acesso ao cluster através da internet é fornecido através de um gateway público. O acesso é limitado para ligar a nós principais e (se existir) do nó de extremidade. Acesso a serviços em execução em nós de principais não é afetado por meio de vários nós principais. O gateway público encaminha os pedidos para o nó principal que aloja o serviço pedido. Por exemplo, se Ambari estar atualmente alojado num nó principal secundário, o gateway encaminha os pedidos recebidos para Ambari para esse nó.

O acesso através do gateway público é limitado para a porta 443 (HTTPS), 22 e 23.

* Porta __443__ é utilizado para aceder a Ambari e outro web da IU ou APIs REST alojados em nós principais.

* Porta __22__ é utilizado para aceder ao nó principal primário ou um nó de extremidade com SSH.

* Porta __23__ é utilizado para aceder ao nó principal secundário com SSH. Por exemplo, `ssh username@mycluster-ssh.azurehdinsight.net` liga-se ao nó principal do principal do cluster com o nome **mycluster**.

Para obter mais informações sobre como utilizar o SSH, consulte o [utilizar o SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) documento.

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Nomes de interno de domínio completamente qualificado (FQDN)

Nós num HDInsight cluster têm um endereço IP e o FQDN que só pode ser acedido a partir do cluster interno. Ao aceder a serviços em cluster utilizando o endereço IP ou FQDN interno, deve utilizar Ambari para verificar o IP ou FQDN, para utilizar quando aceder ao serviço.

Por exemplo, o serviço de Oozie só pode executar um nó principal e utilizar o `oozie` comando a partir de uma sessão SSH requer o URL para o serviço. Este URL é possível obter do Ambari, utilizando o seguinte comando:

    curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

Este comando devolve um valor semelhante para o comando seguinte, que contém o URL interno para utilizar com o `oozie` comando:

    "oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

Para obter mais informações sobre como trabalhar com a API de REST do Ambari, consulte [monitorizar e gerir HDInsight utilizando a API de REST do Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Aceder a outros tipos de nó

Pode ligar a nós que não estão acessíveis diretamente através da internet utilizando os seguintes métodos:

* **SSH**: assim que estiver ligado a um nó principal através do SSH, pode utilizar, em seguida, o SSH do nó principal para ligar a outros nós do cluster. Para obter mais informações, veja o documento [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* **Túnel SSH**: Se precisar de aceder a um serviço web alojado num de nós que não está exposta à internet, tem de utilizar um túnel SSH. Para obter mais informações, consulte o [utilizar um túnel SSH com o HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) documento.

* **Rede Virtual do Azure**: se o cluster do HDInsight faz parte de uma rede Virtual do Azure, qualquer recurso na mesma rede Virtual pode aceder diretamente ao todos os nós do cluster. Para obter mais informações, consulte o [expandir HDInsight utilizando a Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md) documento.

## <a name="how-to-check-on-a-service-status"></a>Como verificar o estado do serviço

Para verificar o estado dos serviços que são executadas em nós principais, utilize a IU da Web do Ambari ou a API de REST do Ambari.

### <a name="ambari-web-ui"></a>IU da Web do Ambari

IU da Web do Ambari é visualizável em https://CLUSTERNAME.azurehdinsight.net. Substitua **CLUSTERNAME** pelo nome do cluster. Se lhe for solicitado, introduza as credenciais de utilizador HTTP para o cluster. O nome de utilizador HTTP predefinido é **admin** e a palavra-passe é a palavra-passe que introduziu ao criar o cluster.

Quando chegar a página do Ambari, os serviços instalados estão listados no lado esquerdo da página.

![Serviços instalados](./media/hdinsight-high-availability-linux/services.png)

Há uma série de ícones que pode ser apresentado junto a um serviço para indicar o estado. Todos os alertas relacionados com um serviço podem ser visualizados utilizando o **alertas** ligação na parte superior da página. Pode selecionar cada serviço para ver mais informações.

Enquanto a página de serviço fornece informações sobre o estado e a configuração de cada serviço, não fornece informações sobre o nó principal de serviço está em execução. Para ver estas informações, utilize o **anfitriões** ligação na parte superior da página. Esta página apresenta os anfitriões do cluster, incluindo os nós principais.

![lista de anfitriões](./media/hdinsight-high-availability-linux/hosts.png)

Selecionar a ligação para um de nós principais apresenta os serviços e componentes em execução nesse nó.

![Estado do componente](./media/hdinsight-high-availability-linux/nodeservices.png)

Para obter mais informações sobre a utilização do Ambari, consulte [monitorizar e gerir HDInsight utilizando a IU da Web do Ambari](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>API de REST do Ambari

A API de REST do Ambari está disponível através da internet. O gateway público HDInsight processa pedidos de encaminhamento ao nó principal que está atualmente a alojar a API REST.

Pode utilizar o seguinte comando para verificar o estado de um serviço através da API de REST do Ambari:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* Substitua **palavra-passe** com a palavra-passe da conta do utilizador (administrador) de HTTP.
* Substitua **CLUSTERNAME** pelo nome do cluster.
* Substitua **SERVICENAME** com o nome do serviço que pretende verificar o estado de.

Por exemplo, para verificar o estado do **HDFS** serviço num cluster com o nome **mycluster**, com uma palavra-passe **palavra-passe**, teria de utilizar o seguinte comando:

    curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

A resposta é semelhante ao seguinte JSON:

    {
      "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
      "ServiceInfo" : {
        "cluster_name" : "mycluster",
        "service_name" : "HDFS",
        "state" : "STARTED"
      }
    }

O URL indica-nos que o serviço está atualmente em execução num nó principal com o nome **hn0 CLUSTERNAME**.

O estado indica-nos que o serviço está atualmente em execução, ou **iniciado**.

Se não souber os serviços que estão instalados no cluster, pode utilizar o seguinte comando para obter uma lista:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

Para obter mais informações sobre como trabalhar com a API de REST do Ambari, consulte [monitorizar e gerir HDInsight utilizando a API de REST do Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Componentes do serviço

Serviços podem conter componentes que pretende verificar o estado de individualmente. Por exemplo, o HDFS contém o componente de NameNode. Para ver informações sobre um componente, o comando seria:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

Se não souber que componentes são fornecidos por um serviço, pode utilizar o seguinte comando para obter uma lista:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Como aceder a ficheiros de registo de nós principais

### <a name="ssh"></a>SSH

Enquanto estiver ligado a um nó principal através de SSH, ficheiros de registo podem ser encontrados na **/var/registo**. Por exemplo, **/var/log/hadoop-yarn/yarn** contém registos para YARN.

Cada nó principal pode ter entradas de registo exclusivo, pelo que deve consultar os registos em ambos.

### <a name="sftp"></a>SFTP

Também pode ligar ao nó principal a utilizar o SSH transferência de protocolo de ficheiros ou o ficheiro de transferência de protocolo sftp (Secure Protocol) e transferir os ficheiros de registo diretamente.

Semelhante à utilização de um cliente SSH, quando a ligação ao cluster tem de fornecer o nome de conta de utilizador SSH e o endereço SSH do cluster. Por exemplo, `sftp username@mycluster-ssh.azurehdinsight.net`. Forneça a palavra-passe da conta quando lhe for pedido, ou forneça um público chave, utilizando o `-i` parâmetro.

Assim que estiver ligado, é-lhe apresentado um `sftp>` linha. Esta linha, pode alterar diretórios, carregar e transferir ficheiros. Por exemplo, os seguintes comandos altere os diretórios para o **/var/log/hadoop/hdfs** diretório e, em seguida, transferir todos os ficheiros no diretório.

    cd /var/log/hadoop/hdfs
    get *

Para obter uma lista de comandos disponíveis, introduza `help` no `sftp>` linha.

> [!NOTE]
> Também existem interfaces gráficas que permitem-lhe visualizar o sistema de ficheiros quando estiver ligado através de SFTP. Por exemplo, [MobaXTerm](http://mobaxterm.mobatek.net/) permite-lhe procurar o sistema de ficheiros através de uma interface semelhante ao Explorador do Windows.

### <a name="ambari"></a>Ambari

> [!NOTE]
> Para aceder aos ficheiros de registo com o Ambari, tem de utilizar um túnel SSH. As interfaces web para os serviços individuais não são expostas publicamente na Internet. Para obter informações sobre como utilizar um túnel SSH, consulte o [utilize túnel SSH](hdinsight-linux-ambari-ssh-tunnel.md) documento.

Na IU da Web do Ambari, selecione o serviço que pretende ver registos de (por exemplo, YARN). Em seguida, utilize **ligações rápidas** para selecionar o nó principal para ver os registos para.

![Utilizar ligações rápidas para ver registos](./media/hdinsight-high-availability-linux/viewlogs.png)

## <a name="how-to-configure-the-node-size"></a>Como configurar o tamanho de nó

O tamanho de um nó só pode ser selecionado durante a criação do cluster. Pode encontrar uma lista de tamanhos VM diferentes disponíveis para o HDInsight no [página de preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Quando criar um cluster, pode especificar o tamanho de nós. As seguintes informações fornecem orientações sobre como especificar o tamanho a utilizar o [portal do Azure][preview-portal], [Azure PowerShell][azure-powershell]e o [CLI do azure][azure-cli]:

* **Portal do Azure**: ao criar um cluster, pode definir o tamanho de nós utilizados pelo cluster:

    ![Imagem do Assistente de criação de cluster com a seleção de tamanho de nó](./media/hdinsight-high-availability-linux/headnodesize.png)

* **CLI do Azure**: ao utilizar o `azure hdinsight cluster create` comando, pode definir o tamanho do cabeçalho, worker e nós de ZooKeeper utilizando o `--headNodeSize`, `--workerNodeSize`, e `--zookeeperNodeSize` parâmetros.

* **O Azure PowerShell**: ao utilizar o `New-AzureRmHDInsightCluster` cmdlet, pode definir o tamanho do cabeçalho, worker e nós de ZooKeeper utilizando o `-HeadNodeVMSize`, `-WorkerNodeSize`, e `-ZookeeperNodeSize` parâmetros.

## <a name="next-steps"></a>Passos seguintes

Utilize as seguintes ligações para saber mais sobre coisas mencionadas neste documento.

* [Referência REST do Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Instalar e configurar a CLI do Azure](../cli-install-nodejs.md)
* [Instale e configure o Azure PowerShell](/powershell/azure/overview).
* [Gerir HDInsight com o Ambari](hdinsight-hadoop-manage-ambari.md)
* [Aprovisionar clusters do HDInsight baseado em Linux](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: /powershell/azureps-cmdlets-docs
[azure-cli]: ../cli-install-nodejs.md
