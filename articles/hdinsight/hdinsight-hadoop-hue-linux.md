---
title: Hue com o Hadoop em clusters baseados no HDInsight Linux - Azure | Microsoft Docs
description: "Saiba como instalar a Hue nos clusters do HDInsight e utilizar o túnel para encaminhar os pedidos para Hue. Utilize Hue para procurar o armazenamento e execute o ramo de registo ou Pig."
keywords: hue hadoop
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 9e57fcca-e26c-479d-a745-7b80a9290447
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: bf68e9af7592d5abe5a4c9aba8c5061819cbc97a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Instalar e utilizar Hue em clusters do HDInsight Hadoop

Saiba como instalar a Hue nos clusters do HDInsight e utilizar o túnel para encaminhar os pedidos para Hue.

> [!IMPORTANT]
> Os passos neste documento exigem um cluster do HDInsight que utiliza o Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a name="what-is-hue"></a>O que é Hue?
Hue é um conjunto de aplicações Web utilizado para interagir com um cluster do Hadoop. Pode utilizar Hue para procurar o armazenamento associado um cluster do Hadoop (WASB, no caso dos clusters do HDInsight), executar tarefas do Hive e Pig scripts e assim sucessivamente. Os seguintes componentes estão disponíveis com instalações Hue num cluster do HDInsight Hadoop.

* Editor de ramo de registo beeswax
* Pig
* Gestor de Metastore
* Oozie
* FileBrowser (que sua contentor predefinido WASB)
* Browser de tarefa

> [!WARNING]
> Componentes fornecidos com o cluster do HDInsight são totalmente suportados e Support da Microsoft irá ajudá-lo para isolar e resolver problemas relacionados com estes componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para ajudar a resolver o problema. Isto pode resultar na resolução do problema ou pedir-lhe para interagir com os canais disponíveis para as tecnologias de open source para onde se encontra profundo conhecimentos para que a tecnologia. Por exemplo, existem vários sites de Comunidade que podem ser utilizadas, como: [fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Também projetos do Apache tem sites de projeto no [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/).
>
>

## <a name="install-hue-using-script-actions"></a>Instalar a Hue utilizando ações de Script

O script para instalar a Hue num cluster do HDInsight baseado em Linux está disponível em https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. Pode utilizar este script para instalar a Hue em clusters com Blobs de armazenamento do Azure (WASB) ou do Azure Data Lake Store, como armazenamento de predefinido.

Esta secção fornece instruções sobre como utilizar o script quando aprovisionar o cluster utilizando o portal do Azure.

> [!NOTE]
> O Azure PowerShell, a CLI do Azure, o SDK .NET do HDInsight ou modelos Azure Resource Manager também podem ser utilizados para aplicar ações de script. Também pode aplicar ações de script para clusters já em execução. Para obter mais informações, consulte [HDInsight personalizar clusters com ações de Script](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Começar a aprovisionar um cluster, utilizando os passos no [aprovisionar clusters do HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md), mas não concluir o aprovisionamento.

   > [!NOTE]
   > Para instalar a Hue nos clusters do HDInsight, o tamanho de headnode recomendada é, pelo menos, A4 (8 núcleos, 14 GB de memória).
   >
   >
2. No **configuração opcional** painel, selecione **ações de Script**e forneça as informações, como mostrado abaixo:

    ![Fornecer parâmetros de ação de script para Hue](./media/hdinsight-hadoop-hue-linux/hue-script-action.png "fornecer parâmetros de ação de script para Hue")

   * **NOME**: introduza um nome amigável para a ação de script.
   * **URI de SCRIPT**: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
   * **HEAD**: esta opção de verificação
   * **TRABALHO**: deixar isto em branco.
   * **ZOOKEEPER**: deixar isto em branco.
   * **Os parâmetros**: deixar isto em branco.
3. Na parte inferior do **ações de Script**, utilize o **selecione** botão para guardar a configuração. Por último, utilize o **selecione** na parte inferior do **configuração opcional** painel para guardar as informações de configuração opcional.
4. Continuar o aprovisionamento de cluster, conforme descrito em [aprovisionar clusters do HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="use-hue-with-hdinsight-clusters"></a>Utilizar Hue com clusters do HDInsight

Túnel SSH é a única forma de aceder a Hue num cluster assim que estiver em execução. Túnel através de SSH permite o tráfego ir diretamente para o headnode do cluster onde Hue está em execução. Após o cluster de terminar o aprovisionamento, utilize os seguintes passos para utilizar a Hue num cluster do HDInsight com Linux.

> [!NOTE]
> Recomendamos que utilize o Firefox browser da web siga as instruções abaixo.
>
>

1. Utilize as informações em [utilize túnel SSH para aceder à IU da web do Ambari, ResourceManager, JobHistory, NameNode, Oozie e outras IUS da web](hdinsight-linux-ambari-ssh-tunnel.md) para criar um túnel SSH do sistema de cliente para o cluster do HDInsight e, em seguida, configure Web browser para utilizar o túnel SSH como um proxy.

2. Depois de ter criado um túnel SSH e configurar o seu browser para tráfego de proxy através do mesmo, tem de localizar o nome de anfitrião do nó principal primário. Pode fazê-lo ao ligar ao cluster através de SSH na porta 22. Por exemplo, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` onde **USERNAME** é o nome de utilizador SSH e **CLUSTERNAME** é o nome do cluster.

    Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Assim que estiver ligado, utilize o seguinte comando para obter o nome de domínio completamente qualificado do headnode primário:

        hostname -f

    Esta ação irá devolver um nome semelhante ao seguinte:

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Este é o nome de anfitrião do headnode primário onde está localizado o Web site Hue.
4. Utilize o browser para abrir o portal da Hue em http://HOSTNAME:8888. Substitua o nome de anfitrião com o nome que obteve no passo anterior.

   > [!NOTE]
   > Quando inicia sessão pela primeira vez, será solicitado para criar uma conta para iniciar sessão no portal da Hue. As credenciais que especificar aqui serão limitadas para o portal e não estão relacionadas com o administrador ou as credenciais de utilizador SSH que especificou ao aprovisionar o cluster.
   >
   >

    ![Iniciar sessão no portal da Hue](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "especificar credenciais para o portal da Hue")

### <a name="run-a-hive-query"></a>Executar uma consulta do Hive
1. No portal da Hue, clique em **editores de consulta**e, em seguida, clique em **Hive** para abrir o editor do Hive.

    ![Utilizar o Hive](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "utilizar o Hive")
2. No **ajudar** separador em **base de dados**, deverá ver **hivesampletable**. Esta é uma tabela de exemplo que vem incluída com todos os clusters do Hadoop no HDInsight. Introduza uma consulta de exemplo no painel da direita e ver o resultado no **resultados** separador no painel inferior, conforme mostrado na captura de ecrã.

    ![Executar consulta do Hive](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "executar Hive consulta")

    Também pode utilizar o **gráfico** separador para ver uma representação visual de resultado.

### <a name="browse-the-cluster-storage"></a>Procurar o armazenamento de cluster
1. No portal da Hue, clique em **ficheiro Browser** no canto superior direito da barra de menus.
2. Por predefinição, o browser de ficheiro abre no **/utilizador/myuser** diretório. Clique em barra à direita antes do diretório de utilizador no caminho para ir para a raiz do contentor de armazenamento do Azure associada com o cluster.

    ![Browser de ficheiros de utilização](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "browser de ficheiros de utilização")
3. Faça duplo clique num ficheiro ou pasta para ver as operações disponíveis. Utilize o **carregar** botão no canto direito para carregar ficheiros para o diretório atual. Utilize o **novo** botão para criar novos ficheiros ou diretórios.

> [!NOTE]
> O browser de ficheiro Hue pode mostrar apenas o conteúdo do contentor predefinido associado o cluster do HDInsight. Quaisquer contas/contentores de armazenamento adicional que poderá ter associado o cluster não serão acessíveis com o browser de ficheiro. No entanto, os contentores adicionais associados com o cluster será sempre acessíveis para as tarefas do Hive. Por exemplo, se introduzir o comando `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` no editor do Hive, pode ver o conteúdo, bem como contentores adicionais. Neste comando, **newcontainer** não é o contentor predefinido associado um cluster.
>
>

## <a name="important-considerations"></a>Considerações importantes sobre o
1. O script utilizado para instalar a Hue instala-o apenas no headnode principal do cluster.

2. Durante a instalação, são reiniciados vários serviços do Hadoop (HDFS YARN, MR2, Oozie) para a atualização da configuração. Após o script de instalar a Hue, poderá demorar algum tempo para outros serviços do Hadoop iniciar a cópia de segurança. Isto poderá afetar o desempenho da Hue inicialmente. Depois de todos os serviços de arranque, Hue estará totalmente funcional.
3. Hue não compreende tarefas Tez, que é a atual predefinição do Hive. Se pretender utilizar o MapReduce como o motor de execução do Hive, atualize o script a utilizar o seguinte comando no script:

        set hive.execution.engine=mr;

4. Com clusters do Linux, pode ter um cenário em que os serviços estão em execução no headnode primário enquanto o Gestor de recursos pode estar em execução no secundário. Cenário poderá resultar em erros (mostrados abaixo) quando utilizar Hue para ver os detalhes das tarefas em execução no cluster. No entanto, pode ver os detalhes da tarefa quando a tarefa foi concluída.

   ![Erro portal da Hue](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "erro portal da Hue")

   Isto acontece devido a um problema conhecido. Como solução, modifique o Ambari para que o Gestor de recursos do Active Directory também é executada no headnode primário.
5. Hue compreende WebHDFS ao utilizam de clusters do HDInsight utilizando o armazenamento do Azure `wasb://`. Por isso, o script personalizado utilizado com a ação de script instala WebWasb, que é um serviço compatível com WebHDFS para falar com WASB. Sim, apesar do portal da Hue indica HDFS em locais (como ao mover o rato o **ficheiro Browser**), deve ser interpretado como WASB.

## <a name="next-steps"></a>Passos seguintes
* [Instalar Giraph nos clusters do HDInsight](hdinsight-hadoop-giraph-install-linux.md). Utilize personalização de cluster para instalar Giraph em clusters do HDInsight Hadoop. Giraph permite-lhe efetuar o processamento de gráfico com o Hadoop e pode ser utilizado com o Azure HDInsight.
* [Instalar Solr nos clusters do HDInsight](hdinsight-hadoop-solr-install-linux.md). Utilize personalização de cluster para instalar Solr em clusters do HDInsight Hadoop. Solr permite-lhe efetuar operações de pesquisa poderosas em dados armazenados.
* [Instalar o R nos clusters do HDInsight](hdinsight-hadoop-r-scripts-linux.md). Utilize a personalização de cluster para instalar o R nos clusters do HDInsight Hadoop. R é um idioma de open source e o ambiente para o cálculo estatístico. Fornece centenas de incorporada funções estatísticas e a suas próprias linguagem de programação que combina aspetos funciona e orientado para objetos de programação. Também fornece extensas capacidades de gráficas.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
