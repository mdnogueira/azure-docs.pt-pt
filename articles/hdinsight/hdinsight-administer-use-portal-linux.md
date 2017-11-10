---
title: Gerir clusters do Hadoop no HDInsight com o portal do Azure | Microsoft Docs
description: "Saiba como criar e gerir clusters do HDInsight através do portal do Azure."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5a76f897-02e8-4437-8f2b-4fb12225854a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: jgao
ms.openlocfilehash: 7d5534649595a3109442619e0adf13c0b354cc0f
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Gerir clusters do Hadoop no HDInsight ao utilizar o portal do Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Utilizar o [portal do Azure][azure-portal], pode gerir clusters do Hadoop no Azure HDInsight. Utilize o Seletor de separador acima para obter informações sobre a gestão de clusters do Hadoop no HDInsight utilizando outras ferramentas.

**Pré-requisito**

Para seguir os passos neste artigo, irá necessitar de um **subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="open-the-azure-portal"></a>Abra o portal do Azure
1. Inicie sessão no [https://portal.azure.com](https://portal.azure.com).
2. Depois de abrir o portal, pode:

   * Clique em **novo** no menu à esquerda para criar um novo cluster:

       ![novo botão de cluster do HDInsight](./media/hdinsight-administer-use-portal-linux/azure-portal-new-button.png)
   * Clique em **Clusters do HDInsight** no menu à esquerda para listar os clusters existentes:

       ![Botão de cluster HDInsight portal do Azure](./media/hdinsight-administer-use-portal-linux/azure-portal-hdinsight-button.png)

       Se não vir o **clusters do HDInsight** botão, clique em **mais serviços** na parte inferior da lista e, em seguida, clique em **clusters do HDInsight** sob o  **Intelligence + análise** secção.


## <a name="create-clusters"></a>Criar clusters
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

HDInsight funciona com um intervalo Hadoop wide componentes. Para obter a lista dos componentes que são verificados e suportados, consulte [qual a versão do Hadoop é no Azure HDInsight?](hdinsight-component-versioning.md) Para informações de criação do cluster geral, consulte [clusters do Hadoop criar no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="access-control-requirements"></a>Requisitos do controlo de acesso

Tem de especificar uma subscrição do Azure quando criar um cluster do HDInsight. O cluster pode ser criado num novo grupo de recursos do Azure ou num grupo de recursos existente. Pode utilizar os seguintes passos para verificar as permissões para criar clusters do HDInsight:

- Para criar um novo grupo de recursos:

    1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
    2. Clique em **subscrição** no menu esquerdo. Tem um ícone de chave amarelo. Deverá ver uma lista de subscrições.
    3. Clique na subscrição que utilizar para criar clusters. 
    4. Clique em **as minhas permissões**.  Mostra o [função](../active-directory/role-based-access-control-what-is.md#built-in-roles) na subscrição. É necessário pelo menos acesso de contribuinte ao criar o cluster do HDInsight.

- Para utilizar um grupo de recursos existente:

    1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
    2. Clique em **grupos de recursos** no menu à esquerda para listar os grupos de recursos.
    3. Clique no grupo de recursos que pretende utilizar para criar o cluster do HDInsight.
    4. Clique em **(IAM) do controlo de acesso**e certifique-se de que a (ou um grupo que pertence a) de ter, pelo menos, o acesso de contribuinte ao grupo de recursos.

Se receber o erro NoRegisteredProviderFound ou o erro MissingSubscriptionRegistration, consulte [resolver erros comuns de implementação do Azure com o Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md).

## <a name="list-and-show-clusters"></a>Listar e Mostrar clusters
1. Inicie sessão no [https://portal.azure.com](https://portal.azure.com).
2. Clique em **Clusters do HDInsight** no menu à esquerda para listar os clusters existentes. Se não vir **Clusters do HDInsight**, clique em **mais serviços** primeiro.
3. Clique no nome do cluster. Se a lista de cluster for longa, pode utilizar o filtro no topo da página.
4. Clique em cluster na lista para ver a página de descrição geral:

    ![Azure portais essentials de cluster de HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials.png) **menu de descrição geral:**
    * **Dashboard**: abre o dashboard do cluster, o que é Web do Ambari para clusters baseados em Linux.
    * **Secure Shell**: mostra as instruções para ligar ao cluster através de ligação de Secure Shell (SSH).
    * **Dimensionar o Cluster**: permite-lhe alterar o número de nós de trabalho para este cluster.
    * **Eliminar**: elimina o cluster.

    **Menu à esquerda:**
    * **Registos de atividade**: consulta e mostrar registos de atividade.
    * **(IAM) do controlo de acesso**: Utilize atribuições de funções.  Consulte [utilize atribuições de funções para gerir o acesso aos recursos da sua subscrição do Azure](../active-directory/role-based-access-control-configure.md).
    * **Etiquetas**: permite-lhe definir os pares chave-valor para definir uma taxonomia personalizada dos seus serviços em nuvem. Por exemplo, pode criar uma chave denominada **projeto**e, em seguida, utiliza um valor comum para todos os serviços associados a um projeto específico.
    * **Diagnosticar e resolver problemas**: apresentar informações de resolução de problemas.
    * **Bloqueia**: adicionar um bloqueio para impedir que o cluster que está a ser modificado ou eliminado.
    * **Script de automatização**: apresentar e exportar o modelo Azure Resource Manager para o cluster. Atualmente, apenas pode exportar a conta do storage do Azure dependente. Consulte [clusters do Hadoop baseados em criar Linux no HDInsight com modelos Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
    * **Início Rápido**: apresenta informações que ajuda-o a começar a utilizar o HDInsight.
    * **Ferramentas para o HDInsight**: informações de ajuda para o HDInsight ferramentas relacionadas.
    * **Início de sessão do cluster**: apresentar as informações de início de sessão do cluster.
    * **Utilização de núcleos da subscrição**: apresentar os núcleos utilizados e disponíveis para a sua subscrição.
    * **Dimensionar o Cluster**: aumentar e diminuir o número de nós de trabalho de cluster. Consulte[dimensionar clusters](hdinsight-administer-use-management-portal.md#scale-clusters).
    * **Secure Shell**: mostra as instruções para ligar ao cluster através de ligação de Secure Shell (SSH). Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
    * **Parceiro do HDInsight**: Adicionar/remover atual parceiro do HDInsight.
    * **As Metastores externas**: ver as metastores do Hive e Oozie. As metastores só podem ser configuradas durante o processo de criação do cluster. Consulte [utilizar metastore do Hive/Oozie](hdinsight-hadoop-provision-linux-clusters.md#use-hiveoozie-metastore).
    * **Ações de script**: scripts de Bash executar no cluster. Consulte [clusters do HDInsight baseado em Linux personalizar através da ação de Script](hdinsight-hadoop-customize-cluster-linux.md).
    * **Aplicações**: aplicações do HDInsight Adicionar/remover.  Consulte [instalar aplicações HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).
    * **Propriedades**: ver as propriedades de cluster.
    * **As contas do Storage**: ver as contas de armazenamento e as chaves. As contas de armazenamento são configuradas durante o processo de criação do cluster.
    * **Identidade do AAD do cluster**:
    * **Novo pedido de suporte**: permite-lhe criar um pedido de suporte com o suporte da Microsoft.
    
6. Clique em **propriedades**:

    As propriedades são:

   * **Nome de anfitrião**: nome do Cluster.
   * **URL de cluster**: O URL para a interface de web do Ambari.
   * **Secure shell (SSH)**: O nome de utilizador e nome do anfitrião a utilizar ao aceder ao cluster através de SSH.
   * **Estado**: um dos: abortada, aceite, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, operacional, executar, erro, eliminar, eliminar, ServiceHost DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued ou ClusterCustomization.
   * **Região**: localização do Azure. Para obter uma lista de localizações do Azure suportadas, consulte o **região** caixa de lista pendente no [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Data de criação**: A data do cluster foi implementado.
   * **Sistema operativo**: O **Windows** ou **Linux**.
   * **Tipo**: Hadoop, HBase, Storm, Spark.
   * **Versão**. Consulte [as versões do HDInsight](hdinsight-component-versioning.md).
   * **Subscrição**: nome da subscrição.
   * **Origem de dados predefinida**: O sistema de ficheiros do cluster.
   * **Tamanho de nós de trabalho**: O tamanho selecionado da VM de nós de trabalho.
   * **Tamanho de nó de HEAD**: O tamanho selecionado da VM de nós principais.
   * **Rede virtual**: O nome da rede Virtual e a sub-rede à qual o cluster é implementado, se um tiver sido selecionado no momento da implementação.

## <a name="delete-clusters"></a>Eliminar clusters
Eliminar um cluster não elimina a conta do storage predefinida nem quaisquer contas do storage ligadas. Pode voltar a criar o cluster utilizando as mesmas contas do storage e metastores as mesmas. Recomendamos que utilize um novo contentor de Blob predefinido quando voltar a criar o cluster.

1. Iniciar sessão para o [Portal][azure-portal].
2. Clique em **Clusters do HDInsight** no menu esquerdo. Se não vir **Clusters do HDInsight**, clique em **mais serviços** primeiro.
3. Clique no cluster que pretende eliminar.
4. Clique em **eliminar** no menu superior e, em seguida, siga as instruções.

Consulte também [pausa/encerramento clusters](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Adicionar mais contas de armazenamento

Pode adicionar contas de Azure Data Lake Store e contas de armazenamento do Azure adicionais depois de um cluster é criado. Para obter mais informações, veja [Adicionar mais contas de armazenamento ao HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Dimensionar clusters
O cluster dimensionamento funcionalidade permite-lhe alterar o número de nós de trabalho utilizado por um cluster do HDInsight do Azure, sem ter de voltar a criar o cluster.

> [!NOTE]
> Apenas clusters do hdinsight versão 3.1.3 ou superior, são suportadas. Se não souber a versão do cluster, pode verificar a página de propriedades.  Consulte [clusters lista e mostrar](#list-and-show-clusters).
>
>

Varia consoante o impacto da alteração do número de nós de dados de cada tipo de cluster suportada pelo HDInsight:

* Hadoop

    Perfeitamente pode aumentar o número de nós de trabalho de um cluster de Hadoop está em execução sem afetar quaisquer tarefas pendentes ou em execução. Também podem ser submetidas novas tarefas enquanto a operação está em curso. Falhas na operação de dimensionamento normalmente são processadas, para que o cluster está sempre deixado no estado funcional.

    Quando um cluster do Hadoop é dimensionado para baixo, reduzindo o número de nós de dados, alguns dos serviços em cluster são reiniciados. Este comportamento faz com que todos os em execução e trabalhos pendentes a falhar após a conclusão da operação de dimensionamento. Pode, no entanto, a submeter as tarefas depois de concluída a operação.
* HBase

    Perfeitamente pode adicionar ou remover nós ao HBase cluster enquanto estiver em execução. Servidores regionais são equilibrados automaticamente dentro de alguns minutos de concluir a operação de dimensionamento. No entanto, pode equilibrar também manualmente os servidores regionais pelo início de sessão em headnode do cluster e executando os comandos seguintes a partir de uma janela de linha de comandos:

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

    Para obter mais informações sobre como utilizar a shell de HBase, consulte [começar com um exemplo de Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Storm

    Perfeitamente pode adicionar ou remover nós de dados ao cluster do Storm enquanto estiver em execução. No entanto, após uma conclusão com êxito da operação de dimensionamento, terá de reequilibrar a topologia.

    Reequilíbrio pode ser conseguido de duas formas:

  * IU da web do Storm
  * Ferramenta de interface de linha de comandos (CLI)

    Consulte o [documentação do Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obter mais detalhes.

    A IU da web de Storm está disponível no cluster do HDInsight:

    ![Reequilibre de escala do HDInsight Storm](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Eis um exemplo de comando da CLI reequilibrar a topologia do Storm:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

**Dimensionar clusters**

1. Iniciar sessão para o [Portal][azure-portal].
2. Clique em **Clusters do HDInsight** no menu esquerdo.
3. Clique no cluster que pretende dimensionar.
3. Clique em **dimensionar o Cluster**.
4. Introduza **nós de número de trabalho**. O limite no número de nós de cluster varia entre subscrições do Azure. Pode contactar o suporte à faturação para aumentar o limite.  As informações de custo refletem as alterações efetuadas ao número de nós.

    ![Escala de spark do HDInsight hadoop hbase storm](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster.png)

## <a name="pauseshut-down-clusters"></a>Colocar em pausa/encerramento clusters

A maioria das tarefas do Hadoop são tarefas de lote só são executadas ocasionalmente. Para a maior parte dos clusters do Hadoop, existem grande períodos de tempo que o cluster não está a ser utilizado para processamento. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado.
Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

Existem várias formas pode programar o processo:

* Fábrica de dados do Azure do utilizador. Consulte [clusters de criar a pedido Hadoop baseado em Linux no HDInsight com o Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) para a criação de HDInsight a pedido serviços ligados.
* Utilize o Azure PowerShell.  Consulte [analisar dados de atraso do voo](hdinsight-analyze-flight-delay-data.md).
* Utilize a CLI do Azure. Consulte [gerir clusters do HDInsight ao utilizar a CLI do Azure](hdinsight-administer-use-command-line.md).
* Utilize o SDK .NET do HDInsight. Consulte [tarefas do Hadoop submeter](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Para informações de preços, consulte [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Para eliminar um cluster a partir do Portal, consulte o artigo [eliminar clusters](#delete-clusters)

## <a name="move-cluster"></a>Mover o cluster

Pode mover um cluster do HDInsight para outro grupo de recursos do Azure ou outra subscrição.  Consulte [clusters lista e mostrar](#list-and-show-clusters).

## <a name="upgrade-clusters"></a>Atualizar clusters

Consulte [cluster do HDInsight atualizar para uma versão mais recente](./hdinsight-upgrade-cluster.md).

## <a name="change-passwords"></a>Alterar as palavras-passe
Um cluster do HDInsight pode ter duas contas de utilizador. O HDInsight cluster conta de utilizador (A.K.A. Conta de utilizador HTTP) e a conta de utilizador SSH são criados durante o processo de criação. Pode utilizar a IU da web do Ambari para mudar o nome de utilizador de conta de utilizador de cluster e a palavra-passe e a ações de script para alterar a conta de utilizador do SSH

### <a name="change-the-cluster-user-password"></a>Alterar a palavra-passe de utilizador do cluster
Pode utilizar a IU da Web do Ambari para alterar a palavra-passe de utilizador do Cluster. Para iniciar sessão Ambari, tem de utilizar o nome de utilizador de cluster existentes e a palavra-passe.

> [!NOTE]
> Alterar a palavra-passe de utilizador (administrador) do cluster pode fazer com que ações de script a executar em relação a esta falha do cluster. Se tiver quaisquer ações de script persistentes que nós de trabalho de destino, estes scripts podem falhar ao adicionar nós ao cluster através de operações de redimensionamento. Para obter mais informações sobre ações de script, consulte [HDInsight personalizar clusters com ações de script](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Iniciar sessão para a IU da Web do Ambari utilizando as credenciais de utilizador de cluster do HDInsight. O nome de utilizador predefinido é **admin**. O URL é **https://&lt;nome de Cluster do HDInsight > azurehdinsight.net**.
2. Clique em **Admin** no menu superior e, em seguida, clique em "Gerir Ambari".
3. No menu à esquerda, clique em **utilizadores**.
4. Clique em **Admin**.
5. Clique em **alterar palavra-passe**.

Ambari, em seguida, altera a palavra-passe em todos os nós do cluster.

### <a name="change-the-ssh-user-password"></a>Alterar a palavra-passe de utilizador do SSH
1. Com um editor de texto, guarde o seguinte texto como um ficheiro denominado **changepassword.sh**.

    > [!IMPORTANT]
    > Tem de utilizar um editor que utiliza LF como o fim de linha. Se o editor de utiliza CRLF, em seguida, o script não funciona.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Carregue o ficheiro para uma localização de armazenamento que pode ser acedida a partir do HDInsight utilizando um endereço HTTP ou HTTPS. Por exemplo, um ficheiro pública armazenar como o armazenamento OneDrive ou Blob do Azure. Guarde o URI (endereço HTTP ou HTTPS) para o ficheiro, como este URI é necessária no próximo passo.
3. No portal do Azure, clique em **Clusters do HDInsight**.
4. Clique em cluster do HDInsight.
4. Clique em **ações de Script**.
4. Do **ações de Script** painel, selecione **submeter novo**. Quando o **submeter ação de script** é apresentado o painel, introduza as seguintes informações:

   | Campo | Valor |
   | --- | --- |
   | Nome |Alterar ssh palavra-passe |
   | Scripts de bash URI |O URI para o ficheiro changepassword.sh |
   | Nós (Head, trabalho, Nimbus, Supervisor, Zookeeper, etc.) |✓ para todos os tipos de nó listados |
   | Parâmetros |Introduza o nome de utilizador SSH e, em seguida, a nova palavra-passe. Deve ser um espaço entre o nome de utilizador e a palavra-passe. |
   | Manter esta ação de script... |Deixe este campo desmarcada. |
5. Selecione **criar** para aplicar o script. Assim que o script estiver concluído, é possível estabelecer ligação ao cluster através de SSH com a nova palavra-passe.

## <a name="grantrevoke-access"></a>Conceder/revogar o acesso
Clusters do HDInsight tem os seguintes serviços web HTTP (todos esses serviços tem pontos finais RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Por predefinição, estes serviços são concedidos para o acesso. Pode revogar/conceder o acesso utilizando [CLI do Azure](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) e [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

## <a name="find-the-subscription-id"></a>Localizar o ID de subscrição

**Para localizar a IDs de subscrição do Azure**

1. Iniciar sessão para o [Portal][azure-portal].
2. Clique em **subscrições**. Cada subscrição tem um nome e um ID.

Cada cluster está associado a uma subscrição do Azure. A subscrição do ID é apresentado no cluster **essencial** mosaico. Consulte [clusters lista e mostrar](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Localizar o grupo de recursos
No modo Azure Resource Manager, cada cluster do HDInsight é criado com um grupo do Azure Resource Manager. O grupo de Gestor de recursos que pertence um cluster é apresentado no:

* A lista de cluster tem um **grupo de recursos** coluna.
* Cluster **essencial** mosaico.  

Consulte [clusters lista e mostrar](#list-and-show-clusters).

## <a name="find-the-storage-accounts"></a>Localizar as contas de armazenamento

Clusters do HDInsight utilizam uma conta de armazenamento do Azure ou um Azure Data Lake Store para armazenar dados. Cada cluster do HDInsight pode ter uma conta do storage predefinida e um número de contas do storage ligadas. Para listar as contas de armazenamento, primeiro, abra o cluster a partir do portal do e, em seguida, clique em **contas do Storage**:

![Contas de armazenamento de cluster do HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-storage-accounts.png)

Na captura de ecrã anterior, há um __predefinido__ coluna que indica se a conta é a conta do storage predefinida.

Para listar as contas de Data Lake Store, clique em **acesso de Data Lake Store** na captura de ecrã anterior.

## <a name="run-hive-queries"></a>Executar consultas do Hive
Não é possível executar a tarefa do Hive diretamente a partir do portal do Azure, mas pode utilizar a vista de ramo de registo na IU da Web do Ambari.

**Para executar consultas do Hive com o Ambari Hive vista**

1. Iniciar sessão para a IU da Web do Ambari utilizando as credenciais de utilizador de cluster do HDInsight. O nome de utilizador predefinido é **admin**. O URL é **https://&lt;nome de Cluster do HDInsight > azurehdinsight.net**.
2. Abra a vista do Hive conforme mostrado na captura de ecrã seguinte:  

    ![Vista de ramo de registo do HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-hive-view.png)

3. Clique em **consulta** no menu superior.
4. Introduza uma consulta do Hive no **Editor de consultas**e, em seguida, clique em **executar**.

## <a name="monitor-jobs"></a>Monitorizar trabalhos
Consulte [gerir clusters do HDInsight utilizando a IU da Web do Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="browse-files"></a>Procurar ficheiros
No portal do Azure, pode procurar o conteúdo do contentor predefinido.

1. Inicie sessão no [https://portal.azure.com](https://portal.azure.com).
2. Clique em **Clusters do HDInsight** no menu à esquerda para listar os clusters existentes.
3. Clique no nome do cluster. Se a lista de cluster for longa, pode utilizar o filtro no topo da página.
4. Clique em **contas do Storage** do menu à esquerda do cluster.
5. Clique numa conta de armazenamento.
7. Clique em de **Blobs** mosaico.
8. Clique no nome do contentor predefinido.

## <a name="monitor-cluster-usage"></a>Monitorizar a utilização do cluster
O **utilização** secção do painel do cluster de HDInsight apresenta informações sobre o número de núcleos disponíveis à sua subscrição para utilização com o HDInsight, bem como o número de núcleos alocados para este cluster e como são alocados para os nós dentro deste cluster. Consulte [clusters lista e mostrar](#list-and-show-clusters).

> [!IMPORTANT]
> Para monitorizar os serviços fornecidos pelo cluster do HDInsight, tem de utilizar Web do Ambari ou a API de REST do Ambari. Para obter mais informações sobre a utilização do Ambari, consulte [gerir clusters do HDInsight com o Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Ligar a um cluster

* [Utilizar o Hive com o HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu algumas funções administrativas básicas. Para obter mais informações, consulte os artigos seguintes:

* [Administrar HDInsight com o Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Administrar HDInsight ao utilizar a CLI do Azure](hdinsight-administer-use-command-line.md)
* [Criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Leia mais sobre como utilizar a IU da Web do Ambari](hdinsight-hadoop-manage-ambari.md)
* [Detalhes sobre como utilizar a API de REST do Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Utilizar o Hive no HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilizar o Pig no HDInsight](hadoop/hdinsight-use-pig.md)
* [Utilizar o Sqoop no HDInsight](hadoop/hdinsight-use-sqoop.md)
* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Qual a versão do Hadoop é no Azure HDInsight?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Linha de comandos de Hadoop"
