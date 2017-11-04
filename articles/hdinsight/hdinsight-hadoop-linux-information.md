---
title: "Sugestões para utilizar o Hadoop no HDInsight baseado em Linux - Azure | Microsoft Docs"
description: "Obter sugestões de implementação para a utilização de clusters do HDInsight (Hadoop) baseado em Linux num ambiente familiar do Linux em execução na nuvem do Azure."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c41c611c-5798-4c14-81cc-bed1e26b5609
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/04/2017
ms.author: larryfr
ms.openlocfilehash: befd03d94f816cb2b59219cd9f1f9af238949592
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="information-about-using-hdinsight-on-linux"></a>Informações sobre como utilizar o HDInsight no Linux

Os clusters do HDInsight do Azure fornecem Hadoop sobre um ambiente familiar do Linux, em execução na nuvem do Azure. Para a maioria das coisas, deve funciona exatamente como qualquer outra instalação do Hadoop no Linux. Este documento chama-se a conhecer as diferenças específicas que deve ter em consideração.

> [!IMPORTANT]
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a name="prerequisites"></a>Pré-requisitos

Muitos dos passos neste documento utilizam os seguintes utilitários, poderão ter de ser instalado no seu sistema.

* [cURL](https://curl.haxx.se/) - utilizado para comunicar com serviços baseados na web
* [jq](https://stedolan.github.io/jq/) - utilizado para analisar documentos JSON
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) (pré-visualização) - utilizado para gerir remotamente serviços do Azure

## <a name="users"></a>Utilizadores

A menos que [associados a um domínio](./domain-joined/apache-domain-joined-introduction.md), HDInsight deve ser considerado um **utilizador único** sistema. É criada uma única conta de utilizador do SSH com o cluster, com as permissões ao nível do administrador. Contas SSH adicionais podem ser criadas, mas têm também acesso de administrador para o cluster.

Associado a um domínio HDInsight suporta vários utilizadores e definições de permissão e função mais granulares. Para obter mais informações, consulte [clusters do HDInsight associados a um domínio gerir](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Nomes de domínio

O nome de domínio completamente qualificado (FQDN) a utilizar ao ligar ao cluster a partir da internet é  **&lt;clustername >. azurehdinsight.net** ou (SSH)  **&lt;clustername-ssh >. azurehdinsight.NET**.

Internamente, cada nó do cluster tem um nome atribuído durante a configuração de cluster. Para localizar os nomes de cluster, consulte o **anfitriões** página na IU da Web do Ambari. Também pode utilizar o seguinte para devolver uma lista de anfitriões da API REST do Ambari:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Substitua **CLUSTERNAME** pelo nome do cluster. Quando lhe for pedido, introduza a palavra-passe da conta de administrador. Este comando devolve um documento JSON que contém uma lista de anfitriões no cluster. Jq é utilizado para extrair o `host_name` o valor do elemento para cada anfitrião.

Se precisar de localizar o nome do nó de um serviço específico, pode consultar Ambari desse componente. Por exemplo, para localizar os anfitriões para o nó de nome do HDFS, utilize o seguinte comando:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Este comando devolve um documento JSON que descrevem o serviço e, em seguida, jq obtém apenas o `host_name` valor para os anfitriões.

## <a name="remote-access-to-services"></a>Acesso remoto aos serviços

* **Ambari (web)** -https://&lt;clustername >. azurehdinsight.net

    Autenticar com o utilizador de administrador do cluster e a palavra-passe e, em seguida, inicie sessão no Ambari.

    Autenticação de texto simples - utilizam sempre HTTPS para ajudar a garantir que a ligação é segura.

    > [!IMPORTANT]
    > Algumas das web UIs disponíveis através do Ambari aceder a nós utilizando um nome de domínio interno. Os nomes de domínio interno não estão acessíveis publicamente através da internet. Poderá receber erros de "do servidor não encontrada" quando tenta aceder a algumas funcionalidades através da Internet.
    >
    > Para utilizar a funcionalidade completa da IU da web do Ambari, utilize um túnel SSH para o tráfego de web de proxy para o nó principal do cluster. Consulte [utilize túnel SSH para aceder à IU da web do Ambari, ResourceManager, JobHistory, NameNode, Oozie e outras web UIs](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** -https://&lt;clustername >.azurehdinsight.net/ambari

    > [!NOTE]
    > Autenticar com o utilizador de administrador do cluster e a palavra-passe.
    >
    > Autenticação de texto simples - utilizam sempre HTTPS para ajudar a garantir que a ligação é segura.

* **WebHCat (Templeton)** -https://&lt;clustername >.azurehdinsight.net/templeton

    > [!NOTE]
    > Autenticar com o utilizador de administrador do cluster e a palavra-passe.
    >
    > Autenticação de texto simples - utilizam sempre HTTPS para ajudar a garantir que a ligação é segura.

* **SSH** - &lt;clustername >-h na porta 22 ou 23. A porta 22 é utilizada para ligar à headnode primário, enquanto 23 é utilizada para ligar para o secundário. Para obter mais informações sobre os nós principais, consulte [Disponibilidade e fiabilidade de clusters do Hadoop no HDInsight](hdinsight-high-availability-linux.md).

    > [!NOTE]
    > Só pode aceder os nós principais do cluster através de SSH a partir de uma máquina cliente. Assim que estiver ligado, em seguida, pode aceder a nós de trabalho utilizando SSH de um headnode.

## <a name="file-locations"></a>Localizações de ficheiros

Podem encontrar os ficheiros relacionados com o Hadoop em nós de cluster em `/usr/hdp`. Este diretório contém subdiretórios os seguintes:

* **2.2.4.9-1**: O nome do diretório é a versão de plataforma de dados do Hortonworks utilizado pelo HDInsight. O número no seu cluster poderão ser diferente dos listados aqui.
* **atual**: este diretório contém ligações para subdiretórios o **2.2.4.9-1** diretório. Este diretório existe para que não tenham de memorizar o número de versão.

Dados de exemplo e ficheiros JAR podem ser encontrados no Hadoop sistema de ficheiros distribuído no `/example` e`/HdiSamples`

## <a name="hdfs-azure-storage-and-data-lake-store"></a>HDFS, o Storage do Azure e o arquivo Data Lake

Na maioria das distribuições de Hadoop, HDFS segurança efetuado pelo armazenamento local nas máquinas do cluster. Utilizar o armazenamento local pode ser dispendiosa para uma solução baseada na nuvem em que são-lhe cobrados hora a hora ou por minuto para recursos de computação.

O HDInsight utiliza o blobs Storage do Azure ou do Azure Data Lake Store como o arquivo predefinido. Estes serviços fornecem as seguintes vantagens:

* Armazenamento de longa duração cheap
* Acessibilidade de serviços externos como Web sites, utilitários de carregamento/transferência de ficheiros, vários SDKs de idioma e browsers da web

> [!WARNING]
> HDInsight suporta apenas __para fins gerais__ contas de armazenamento do Azure. Não suporta atualmente o __armazenamento de BLOBs__ tipo de conta.

Uma conta de armazenamento do Azure pode conter até 4.75 TB, apesar dos blobs individuais (ou ficheiros de uma perspetiva de HDInsight) só podem aceder 195 GB. O Azure Data Lake Store podem aumentar dinamicamente para conter biliões de ficheiros, com maiores do que um petabyte de ficheiros individuais. Para obter mais informações, consulte [compreender os blobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) e [Data Lake Store](https://azure.microsoft.com/services/data-lake-store/).

Ao utilizar o Storage do Azure ou de Data Lake Store, não têm de fazer nada especial do HDInsight para aceder aos dados. Por exemplo, o comando seguinte apresenta uma lista de ficheiros a `/example/data` pasta independentemente se são armazenado no Storage do Azure ou de Data Lake Store:

    hdfs dfs -ls /example/data

### <a name="uri-and-scheme"></a>URI e o esquema

Alguns comandos podem necessitar de especificar o esquema como parte do URI ao aceder a um ficheiro. Por exemplo, o componente de Storm-HDFS requer que especifique o esquema. Ao utilizar o armazenamento não predefinidas (adicionado como armazenamento "adicional" para o cluster de armazenamento), tem de utilizar sempre o esquema como parte do URI.

Quando utilizar __Storage do Azure__, utilize um dos esquemas URI seguintes:

* `wasb:///`: Armazenamento de predefinido acesso através de comunicação sem encriptação.

* `wasbs:///`: Armazenamento de predefinido acesso utilizando comunicações encriptadas.  O esquema de wasbs é suportado apenas a partir do HDInsight versão 3.6 e superior.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Utilizada ao comunicar com uma conta de armazenamento não predefinidas. Por exemplo, se tiver uma conta de armazenamento adicional ou quando aceder aos dados armazenados numa conta do storage acessível publicamente.

Quando utilizar __Data Lake Store__, utilize um dos esquemas URI seguintes:

* `adl:///`: A predefinição Data Lake Store para o cluster de acesso.

* `adl://<storage-name>.azuredatalakestore.net/`: Utilizada quando comunica com um não predefinidas Data Lake Store. Também é utilizado para aceder a dados fora do diretório de raiz do cluster do HDInsight.

> [!IMPORTANT]
> Ao utilizar o Data Lake Store como o arquivo predefinido para o HDInsight, tem de especificar um caminho num arquivo para utilizar como a raiz de armazenamento do HDInsight. O caminho predefinido é `/clusters/<cluster-name>/`.
>
> Quando utilizar `/` ou `adl:///` para aceder aos dados, só podem aceder a dados armazenados na raiz (por exemplo, `/clusters/<cluster-name>/`) do cluster. Para aceder aos dados em qualquer lugar no arquivo, utilize o `adl://<storage-name>.azuredatalakestore.net/` formato.

### <a name="what-storage-is-the-cluster-using"></a>O armazenamento é utilizando o cluster

Pode utilizar Ambari para obter a configuração de armazenamento predefinido para o cluster. Utilize o seguinte comando para obter informações de configuração de HDFS utilizando curl e filtrá-la utilizando [jq](https://stedolan.github.io/jq/):

```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'```

> [!NOTE]
> Este comando devolve a primeira configuração aplicada ao servidor (`service_config_version=1`), que contém esta informação. Precisa de listar todas as versões de configuração para localizar um mais recente.

Este comando devolve um valor semelhante para o URI seguinte:

* `wasb://<container-name>@<account-name>.blob.core.windows.net`Se utilizar uma conta de armazenamento do Azure.

    O nome da conta é o nome da conta do Storage do Azure. O nome do contentor é o contentor de blob que é a raiz de armazenamento de cluster.

* `adl://home`Se utilizar o Azure Data Lake Store. Para obter o nome do Data Lake Store, utilize a seguinte chamada REST:

    ```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'```

    Este comando devolve o nome de anfitrião seguintes: `<data-lake-store-account-name>.azuredatalakestore.net`.

    Para obter o diretório no arquivo que seja a raiz para o HDInsight, utilize a seguinte chamada REST:

    ```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'```

    Este comando devolve um caminho semelhante ao seguinte caminho: `/clusters/<hdinsight-cluster-name>/`.

Também pode encontrar as informações do armazenamento no portal do Azure, utilizando os seguintes passos:

1. No [portal do Azure](https://portal.azure.com/), selecione o cluster do HDInsight.

2. Do **propriedades** secção, selecione **contas do Storage**. As informações do armazenamento para o cluster são apresentadas.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Como aceder a ficheiros do HDInsight externa

Existem uma várias formas de aceder a dados de fora do HDInsight cluster. Seguem-se algumas hiperligações para utilitários e SDKs que podem ser utilizados para trabalhar com os seus dados:

Se utilizar __Storage do Azure__, consulte as seguintes ligações para que pode aceder aos seus dados de formas:

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2): comandos de interface de linha de comandos para trabalhar com o Azure. Depois de instalar, utilizar o `az storage` comando para obter ajuda sobre a utilização do armazenamento, ou `az storage blob` para comandos específicos de blob.
* [blobxfer.PY](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): um python do script para trabalhar com blobs no armazenamento do Azure.
* Vários SDKs:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [API REST de Armazenamento](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Se utilizar __Azure Data Lake Store__, consulte as seguintes ligações para que pode aceder aos seus dados de formas:

* [Browser da Web](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [CLI 2.0 do Azure](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [WebHDFS REST API](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Ferramentas do Data Lake para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling"></a>Dimensionamento do cluster

O cluster dimensionamento funcionalidade permite-lhe alterar dinamicamente o número de nós de dados utilizado por um cluster. Pode efetuar operações de dimensionamento ao outras tarefas ou processos em execução num cluster.

Os tipos de outro cluster são afetados pelo dimensionamento da seguinte forma:

* **Hadoop**: quando o dimensionamento para baixo o número de nós num cluster, alguns dos serviços em cluster são reiniciados. Operações de dimensionamento pode provocar tarefas em execução ou pendentes a falhar após a conclusão da operação de dimensionamento. Pode submeter as tarefas depois de concluída a operação.
* **HBase**: servidores Regional são equilibrados automaticamente dentro de alguns minutos, depois de concluída a operação de dimensionamento. Para balancear manualmente servidores regionais, utilize os seguintes passos:

    1. Ligar ao cluster do HDInsight através de SSH. Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    2. Utilize o seguinte para iniciar a shell de HBase:

            hbase shell

    3. Assim que carregou a shell de HBase, utilize o seguinte para equilibrar manualmente os servidores regionais:

            balancer

* **Storm**: deve rebalancear as topologias Storm em execução depois de ter sido efetuada uma operação de dimensionamento. Reequilíbrio permite que a topologia readjust definições de paralelismo com base no número de nós no cluster novo. Reequilibrar as topologias em execução, utilize uma das seguintes opções:

    * **SSH**: ligar ao servidor e utilize o seguinte comando para rebalancear uma topologia:

            storm rebalance TOPOLOGYNAME

        Também pode especificar parâmetros para substituir as sugestões de paralelismo originalmente fornecidas a topologia. Por exemplo, `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` reconfigura a topologia 5 processos de trabalho, 3 executor para o componente de spout azul e 10 executor para o componente de amarelo bolt.

    * **Storm IU**: utilizar os seguintes passos para rebalancear uma topologia utilizando a IU do Storm.

        1. Abra **https://CLUSTERNAME.azurehdinsight.net/stormui** no seu browser, em que CLUSTERNAME é o nome do cluster do Storm. Se lhe for solicitado, introduza o nome de cluster do HDInsight administrador (administrador) e a palavra-passe que especificou quando criar o cluster.
        2. Selecione a topologia que pretende rebalancear, em seguida, selecione o **rebalancear** botão. Introduza o atraso antes da operação de reequilibre é executada.

Para obter informações específicas sobre como aumentar o cluster do HDInsight, consulte:

* [Gerir clusters do Hadoop no HDInsight ao utilizar o portal do Azure](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Gerir clusters do Hadoop no HDInsight ao utilizar o Azure PowerShell](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Como instalar Hue (ou outros componentes do Hadoop)?

O HDInsight é um serviço gerido. Se o Azure Deteta um problema com o cluster, pode eliminar o nó falhar e criar um nó para substituí-lo. Se instalar manualmente coisas no cluster, não são mantidas quando ocorre a esta operação. Em alternativa, utilize [ações de Script do HDInsight](hdinsight-hadoop-customize-cluster.md). Uma ação de script pode ser utilizada para efetuar as seguintes alterações:

* Instalar e configurar um web site ou serviço.
* Instalar e configurar um componente que necessita de alterações de configuração em vários nós no cluster.

Ações de script são scripts de Bash. Os scripts são executadas durante a criação do cluster e são utilizados para instalar e configurar os componentes adicionais. Scripts de exemplo são fornecidas para instalar os componentes seguintes:

* [Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)

Para obter informações sobre o desenvolvimento das suas próprias Ações de Script, consulte [Desenvolvimento de Ações de Script com o HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="jar-files"></a>Ficheiros JAR

Algumas tecnologias de Hadoop são fornecidas nos ficheiros jar autónomo que contêm funções utilizadas como parte de uma tarefa de MapReduce, ou a partir de dentro de Pig ou Hive. Muitas vezes, não necessitam de qualquer configuração e podem ser carregados para o cluster após a criação e utilizados diretamente. Se pretender certificar-se de que o componente survives reprocessamento de imagem do cluster, pode armazenar o ficheiro jar no armazenamento de predefinido para o seu cluster (WASB ou ADL).

Por exemplo, se quiser utilizar a versão mais recente do [DataFu](http://datafu.incubator.apache.org/), pode transferir um jar que contém o projeto e carregá-la para o cluster do HDInsight. Em seguida, siga a documentação de DataFu no como utilizá-la a partir do Pig ou Hive.

> [!IMPORTANT]
> Alguns componentes que são autónomo jar ficheiros são fornecidos com o HDInsight, mas não estão no caminho. Se estiver à procura de um componente específico, pode utilizar a seguir para procurá-lo no seu cluster:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Este comando devolve o caminho de ficheiros jar correspondente.

Para utilizar uma versão diferente de um componente, carregue a versão que precisa e utilizá-lo nas suas tarefas.

> [!WARNING]
> Componentes fornecidos com o cluster do HDInsight são totalmente suportados e Microsoft Support ajuda a isolar e resolver problemas relacionados com estes componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para ajudar a resolver o problema. Isto pode resultar na resolução do problema ou pedir-lhe para interagir com os canais disponíveis para as tecnologias de open source para onde se encontra profundo conhecimentos para que a tecnologia. Por exemplo, existem vários sites de Comunidade que podem ser utilizadas, como: [fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Também projetos do Apache tem sites de projeto no [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/).

## <a name="next-steps"></a>Passos seguintes

* [Migrar do HDInsight baseado em Windows para baseado em Linux](hdinsight-migrate-from-windows-to-linux.md)
* [Utilizar o Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilizar o Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Utilizar tarefas de MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)
