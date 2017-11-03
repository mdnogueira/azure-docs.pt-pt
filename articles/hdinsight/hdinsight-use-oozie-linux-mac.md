---
title: Utilizar o Hadoop Oozie fluxos de trabalho no Azure HDInsight baseado em Linux | Microsoft Docs
description: Utilize o Oozie do Hadoop no HDInsight baseado em Linux. Saiba como definir um fluxo de trabalho do Oozie e submeter uma tarefa de Oozie.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d7603471-5076-43d1-8b9a-dbc4e366ce5d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2017
ms.author: larryfr
ms.openlocfilehash: 89b7d48afd72fb424305da6c2cbdcea2ddc52ddd
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Utilizar o Oozie com o Hadoop para definir e executar um fluxo de trabalho no Azure HDInsight baseado em Linux

[!INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Saiba como utilizar o Apache Oozie com o Hadoop no Azure HDInsight. Oozie é um sistema de coordenação e fluxos de trabalho que gere as tarefas do Hadoop. Oozie está integrado com a pilha do Hadoop e suporta as seguintes tarefas:

* Apache MapReduce
* O Apache Pig
* Apache Hive
* O Apache Sqoop

Também pode utilizar o Oozie para agendar tarefas específicas para um sistema, como os programas de Java ou scripts de shell.

> [!NOTE]
> Outra opção para definir fluxos de trabalho com o HDInsight é utilizar o Azure Data Factory. Para saber mais sobre o Data Factory, consulte o artigo [Pig de utilização e o Hive com o Data Factory][azure-data-factory-pig-hive].

> [!IMPORTANT]
> Oozie não está ativado no HDInsight associados a um domínio.

## <a name="prerequisites"></a>Pré-requisitos

**Um cluster do HDInsight**: para obter mais informações, consulte [introdução ao HDInsight no Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

> [!IMPORTANT]
> Os passos neste documento exigem um cluster do HDInsight que utiliza o Linux. Linux é o único sistema operativo utilizado no HDInsight versão 3.4 ou posterior. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a name="example-workflow"></a>Fluxo de trabalho de exemplo

O fluxo de trabalho utilizado neste documento contém duas ações. As ações são definições para as tarefas, como a execução do Hive, Sqoop, MapReduce ou outros processos:

![Diagrama de fluxo de trabalho][img-workflow-diagram]

1. Uma ação de ramo de registo executa um script de HiveQL para extrair os registos da **hivesampletable** que incluiu com o HDInsight. Cada linha de dados descreve a visita de um dispositivo móvel específico. O formato de registo é apresentado como o seguinte texto:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    O script de ramo de registo utilizado neste documento contagens de visitas totais para cada plataforma, tal como Android ou iPhone e armazena o número de uma nova tabela do Hive.

    Para obter mais informações sobre o Hive, consulte [Utilizar o Hive com o HDInsight][hdinsight-use-hive].

2. Uma ação de Sqoop exporta o conteúdo da tabela do Hive novo a uma tabela na base de dados do Azure SQL. Para obter mais informações sobre Sqoop, consulte [utilize Hadoop Sqoop com o HDInsight][hdinsight-use-sqoop].

> [!NOTE]
> Para versões suportadas do Oozie nos clusters do HDInsight, consulte [Novidades nas versões de cluster do Hadoop fornecidas pelo HDInsight][hdinsight-versions].

## <a name="create-the-working-directory"></a>Criar o diretório de trabalho

Oozie espera, pode armazenar todos os recursos necessários para uma tarefa no mesmo diretório. Este exemplo utiliza **wasb: / / / tutoriais/useoozie**. Para criar este diretório, execute os seguintes passos:

1. Ligar ao cluster do HDInsight ao utilizar o SSH:

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Substitua `sshuser` com o nome de utilizador do SSH para o cluster. Substitua `clustername` com o nome do cluster. Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Para criar o diretório, utilize o seguinte comando:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]
    > O `-p` parâmetro faz com que a criação de todos os diretórios no caminho. O **dados** diretório é utilizado para armazenar os dados utilizados pelo **useooziewf.hql** script.

3. Para se certificar de que o Oozie pode representar a conta de utilizador, utilize o seguinte comando:

    ```bash
    sudo adduser username users
    ```

    Substitua `username` com o nome de utilizador SSH.

    > [!NOTE]
    > Pode ignorar erros indicam que o utilizador já for um membro do `users` grupo.

## <a name="add-a-database-driver"></a>Adicionar um controlador de base de dados

Uma vez que este fluxo de trabalho utiliza Sqoop para exportar dados para a base de dados do SQL Server, tem de fornecer uma cópia do controlador JDBC utilizado para interagir com a base de dados do SQL Server. Para copiar o controlador JDBC para o diretório de trabalho, utilize o seguinte comando da sessão SSH:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /tutorials/useoozie/
```

Se o fluxo de trabalho utilizada outros recursos, tais como um jar que contém uma aplicação de MapReduce, terá de adicionar bem esses recursos.

## <a name="define-the-hive-query"></a>Definir a consulta do Hive

Utilize os seguintes passos para criar um script de idioma (HiveQL) de consulta de Hive que define uma consulta. Um fluxo de trabalho do Oozie mais tarde neste documento, será utilizada a consulta.

1. A partir da ligação SSH, utilize o seguinte comando para criar um ficheiro denominado `useooziewf.hql`:

    ```bash
    nano useooziewf.hql
    ```

3. Depois de abre o GNU nano editor, utilize a seguinte consulta como o conteúdo do ficheiro:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Existem duas variáveis utilizadas no script:

    * `${hiveTableName}`: Contém o nome da tabela a ser criado.

    * `${hiveDataFolder}`: Contém a localização para armazenar os ficheiros de dados da tabela.

    O ficheiro de definição de fluxo de trabalho, workflow.xml neste tutorial, transmite estes valores para este script de HiveQL no tempo de execução.

4. Para sair do editor, selecione Ctrl + X. Quando lhe for pedido, selecione `Y` para guardar o ficheiro, introduza `useooziewf.hql` como o nome de ficheiro e, em seguida, selecione **Enter**.

5. Utilize os seguintes comandos para copiar `useooziewf.hql` para `wasb:///tutorials/useoozie/useooziewf.hql`:

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Este comando armazena o `useooziewf.hql` ficheiro no armazenamento compatível com HDFS para o cluster.

## <a name="define-the-workflow"></a>Definir o fluxo de trabalho

Definições de fluxo de trabalho do Oozie são escritas no Hadoop processo linguagem de definição (hPDL), que é uma linguagem de definição de processo XML. Utilize os seguintes passos para definir o fluxo de trabalho:

1. Utilize a seguinte instrução para criar e editar um novo ficheiro:

    ```bash
    nano workflow.xml
    ```

2. Depois de abre o editor nano for apresentado, introduza o seguinte XML como o conteúdo do ficheiro:

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>
        <action name="RunHiveScript">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.job.queue.name</name>
                <value>${queueName}</value>
            </property>
            </configuration>
            <script>${hiveScript}</script>
            <param>hiveTableName=${hiveTableName}</param>
            <param>hiveDataFolder=${hiveDataFolder}</param>
        </hive>
        <ok to="RunSqoopExport"/>
        <error to="fail"/>
        </action>
        <action name="RunSqoopExport">
        <sqoop xmlns="uri:oozie:sqoop-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.compress.map.output</name>
                <value>true</value>
            </property>
            </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveDataFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\t"</arg>
            <archive>sqljdbc41.jar</archive>
            </sqoop>
        <ok to="end"/>
        <error to="fail"/>
        </action>
        <kill name="fail">
        <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

    Existem duas ações definidas no fluxo de trabalho:

   * `RunHiveScript`: Esta ação é a ação de início e executa o `useooziewf.hql` script de ramo de registo.

   * `RunSqoopExport`: Esta ação exporta os dados criados a partir do script de ramo de registo para uma base de dados do SQL Server por utilizar o Sqoop. Esta ação é executada apenas se o `RunHiveScript` ação for concluída com êxito.

     O fluxo de trabalho tem várias entradas, tais como `${jobTracker}`. Irá substituir estas entradas com os valores que utiliza na definição de tarefa. Irá criar a definição de tarefa mais tarde neste documento.

     Tenha também em atenção o `<archive>sqljdbc4.jar</archive>` entrada na secção Sqoop. Esta entrada indica ao Oozie para disponibilizar este arquivo para Sqoop ao executar esta ação.

3. Para guardar o ficheiro, selecione de Ctrl + X, introduza `Y`e, em seguida, selecione **Enter**. 

4. Utilize o seguinte comando para copiar o `workflow.xml` do ficheiro para `/tutorials/useoozie/workflow.xml`:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-the-database"></a>Criar a base de dados

Para criar uma base de dados do SQL, siga os passos a [criar uma base de dados do SQL Server](../sql-database/sql-database-get-started.md) documento. Quando criar a base de dados, utilize `oozietest` como o nome de base de dados. Também anote o nome do servidor de base de dados.

### <a name="create-the-table"></a>Criar a tabela

> [!NOTE]
> Existem várias formas de ligar à base de dados do SQL Server para criar uma tabela. Os passos seguintes utilize [FreeTDS](http://www.freetds.org/) do HDInsight cluster.


1. Utilize o seguinte comando para instalar o FreeTDS no cluster do HDInsight:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Depois do FreeTDS foi instalado, utilize o seguinte comando para ligar ao servidor de base de dados do SQL Server que criou anteriormente:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -P <sqlPassword> -p 1433 -D oozietest
    ```

    Recebe o resultado como o seguinte texto:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

3. Na `1>` solicitar, introduza as seguintes linhas:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    Quando o `GO` instrução é introduzida, são avaliadas as instruções anteriores. As declarações de criar uma tabela, chamada **mobiledata**, que é utilizado pelo fluxo de trabalho.

    Para verificar que a tabela foi criada, utilize os seguintes comandos:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Consulte o resultado como o seguinte texto:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo     mobiledata      BASE TABLE

4. Para sair do utilitário de tsql, introduza `exit` no `1>` linha.

## <a name="create-the-job-definition"></a>Criar a definição de tarefa

A definição de tarefa descreve onde encontrar a workflow.xml. Também descreve onde encontrar outros ficheiros utilizados pelo fluxo de trabalho, tal como `useooziewf.hql`. Além disso, define os valores para as propriedades utilizadas dentro do fluxo de trabalho e os ficheiros associados.

1. Para obter o endereço completo do armazenamento de predefinido, utilize o seguinte comando. Este endereço é utilizado no ficheiro de configuração que cria no próximo passo.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Este comando devolve informações como o XML do seguinte:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]
    > Se o cluster do HDInsight utiliza o armazenamento do Azure como armazenamento de predefinido, o `<value>` conteúdo do elemento de começar por `wasb://`. Se o Azure Data Lake Store é utilizado em vez disso, começa com `adl://`.

    Guardar o conteúdo do `<value>` elemento, dado que é utilizado nos passos.

2. Para criar a configuração de definição de tarefa do Oozie, utilize o seguinte comando:

    ```bash
    nano job.xml
    ```

3. Depois de abre o editor nano for apresentado, utilize o seguinte XML como o conteúdo do ficheiro:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>
        </property>

        <property>
        <name>jobTracker</name>
        <value>headnodehost:8050</value>
        </property>

        <property>
        <name>queueName</name>
        <value>default</value>
        </property>

        <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
        </property>

        <property>
        <name>hiveScript</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=adminLogin;password=adminPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>YourName</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

   * Substitua todas as instâncias de `wasb://mycontainer@mystorageaccount.blob.core.windows.net` com o valor recebido anteriormente para o armazenamento de predefinido.

     > [!WARNING]
     > Se o caminho é um `wasb` caminho, tem de utilizar o caminho completo. Não Encurte para just `wasb:///`.

   * Substitua `YourName` com o nome de início de sessão para o cluster do HDInsight.
   * Substitua `serverName`, `adminLogin`, e `adminPassword` com as informações da base de dados do SQL Server.

     A maioria das informações existentes neste ficheiro é utilizada para preencher os valores utilizados nos ficheiros workflow.xml ou ooziewf.hql, tal como `${nameNode}`.

     > [!NOTE]
     > O `oozie.wf.application.path` entrada define onde encontrar o ficheiro workflow.xml. Este ficheiro contém o fluxo de trabalho que foi executado pela tarefa.

5. Para guardar o ficheiro, selecione de Ctrl + X, introduza `Y`e, em seguida, selecione **Enter**.

## <a name="submit-and-manage-the-job"></a>Submeter e gerir a tarefa

Os seguintes passos utilizem o comando de Oozie para submeter e gerir Oozie fluxos de trabalho no cluster. O comando de Oozie é uma interface amigável através de [API de REST do Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]
> Quando utiliza o comando de Oozie, tem de utilizar o FQDN para o nó principal do HDInsight. Este FQDN só é acessível a partir do cluster, ou se o cluster estiver numa rede virtual do Azure, a partir de outros computadores na mesma rede.


1. Para obter o URL para o serviço do Oozie, utilize o seguinte comando:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Esta ação devolve informações como o XML do seguinte:

    ```xml
    <name>oozie.base.url</name>
    <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    O `http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie` parte é o URL a utilizar com o comando de Oozie.

2. Para criar uma variável de ambiente para o URL, utilize o seguinte, pelo que não tenha de introduzi-la para cada comando:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

    Substitua o URL pelo recebido anteriormente.
3. Para submeter a tarefa, utilize o seguinte:

    ```bash
    oozie job -config job.xml -submit
    ```

    Este comando carrega as informações de tarefa de `job.xml` e envia-a para Oozie, mas não é executado.

    Depois de concluir o comando, deverá devolver o ID da tarefa, por exemplo, `0000005-150622124850154-oozie-oozi-W`. Este ID é utilizado para gerir a tarefa.

4. Para ver o estado da tarefa, utilize o seguinte comando:

    ```bash
    oozie job -info <JOBID>
    ```

    > [!NOTE]
    > Substitua `<JOBID>` com o ID devolvido no passo anterior.

    Esta ação devolve informações como o seguinte texto:

        Job ID : 0000005-150622124850154-oozie-oozi-W
        ------------------------------------------------------------------------------------------------------------------------------------
        Workflow Name : useooziewf
        App Path      : wasb:///tutorials/useoozie
        Status        : PREP
        Run           : 0
        User          : USERNAME
        Group         : -
        Created       : 2015-06-22 15:06 GMT
        Started       : -
        Last Modified : 2015-06-22 15:06 GMT
        Ended         : -
        CoordAction ID: -
        ------------------------------------------------------------------------------------------------------------------------------------

    Esta tarefa tem o estado `PREP`. Este estado indica que a tarefa foi criada, mas não foi iniciada.

5. Para iniciar a tarefa, utilize o seguinte comando:

    ```bash
    oozie job -start JOBID
    ```

    > [!NOTE]
    > Substitua `<JOBID>` com o ID devolvido anteriormente.

    Se verificar o estado após este comando, está num Estado de execução e informações são devolvidas para as ações dentro da tarefa.

6. Depois de concluída a tarefa com êxito, pode verificar que os dados gerados e exportados para a tabela de base de dados do SQL Server utilizando o seguinte comando:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest
    ```

    Na `1>` solicitar, introduza a seguinte consulta:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    As informações devolvidas são como o seguinte texto:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Para obter mais informações sobre o comando de Oozie, consulte [ferramenta da linha de comandos do Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>API de REST do Oozie

Com a API de REST do Oozie, pode criar as suas próprias ferramentas que funcionam com Oozie. Segue-se informações específicas do HDInsight sobre a utilização da API de REST Oozie:

* **URI**: pode aceder a API de REST de fora do cluster em `https://CLUSTERNAME.azurehdinsight.net/oozie`.

* **Autenticação**: para autenticar, utilize a API a conta de cluster HTTP (administrador) e a palavra-passe. Por exemplo:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Para obter mais informações sobre como utilizar a API de REST do Oozie, consulte [API de serviços Web do Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>IU da web do Oozie

A IU da web de Oozie fornece uma vista baseada na web para o estado das tarefas de Oozie no cluster. Com a IU da web pode ver as seguintes informações:

   * Estado da tarefa
   * Definição de tarefa
   * Configuração
   * Um gráfico das ações na tarefa
   * Registos da tarefa

Também pode ver os detalhes para as ações dentro de uma tarefa.

Para aceder a IU da web do Oozie, conclua os seguintes passos:

1. Crie um túnel SSH para o cluster do HDInsight. Para obter mais informações, consulte [utilize túnel SSH com o HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Depois de criar um túnel, abra a IU da web do Ambari no seu browser. O URI para o site do Ambari é `https://CLUSTERNAME.azurehdinsight.net`. Substitua `CLUSTERNAME` com o nome do cluster do HDInsight baseado em Linux.

3. Do lado esquerdo da página, selecione **Oozie** > **ligações rápidas** > **IU da Web do Oozie**.

    ![imagem de menus](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. A IU da web de Oozie predefinições para apresentar as tarefas de fluxo de trabalho em execução. Para ver todas as tarefas de fluxo de trabalho, selecione **todas as tarefas**.

    ![Todas as tarefas apresentadas](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. Para visualizar mais informações sobre uma tarefa, selecione a tarefa.

    ![Informações de tarefa](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. Do **tarefa informações** separador, pode ver as informações básicas de tarefa e as ações individuais dentro da tarefa. Pode utilizar os separadores na parte superior para ver o **definição de tarefa**, **tarefa configuração**, acesso a **registo da tarefa**, ou visualizar um acíclicos direcionados (DAG) da tarefa em **Tarefa DAG**.

   * **Registo da tarefa**: selecione a **obter registos** botão para obter todos os registos para a tarefa ou utilize o **introduzir o filtro de pesquisa** campo para filtrar os registos.

       ![Registo da tarefa](./media/hdinsight-use-oozie-linux-mac/joblog.png)

   * **Tarefa DAG**: O DAG é uma descrição geral do gráfica dos caminhos de dados efetuadas através do fluxo de trabalho.

       ![Tarefa DAG](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. Se selecionar uma das ações do **tarefa informações** separador, implica das informações para a ação. Por exemplo, seleccione o **RunSqoopExport** ação.

    ![Informações de ação](./media/hdinsight-use-oozie-linux-mac/action.png)

8. Pode ver os detalhes para a ação, tal como uma hiperligação para o **URL da consola**. Utilize esta hiperligação para ver as informações de controlador de tarefa para a tarefa.

## <a name="schedule-jobs"></a>Agenda de tarefas

Pode utilizar o coordenador para especificar um início, uma extremidade e a frequência de ocorrência de tarefas. Para definir uma agenda para o fluxo de trabalho, execute os seguintes passos:

1. Utilize o seguinte comando para criar um ficheiro denominado **coordinator.xml**:

    ```bash
    nano coordinator.xml
    ```

    Utilize o seguinte XML como o conteúdo do ficheiro:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
        <workflow>
            <app-path>${workflowPath}</app-path>
        </workflow>
        </action>
    </coordinator-app>
    ```

    > [!NOTE]
    > O `${...}` variáveis são substituídas pelos valores na definição de tarefa no tempo de execução. As variáveis são:
    >
    > * `${coordFrequency}`: O tempo entre instâncias da tarefa em execução.
    > * `${coordStart}`: A hora de início da tarefa.
    > * `${coordEnd}`: A hora de fim da tarefa.
    > * `${coordTimezone}`: As tarefas de coordenador estão num fixo fuso horário com sem hora de Verão, normalmente representado utilizando a UTC. Este fuso horário é referido como a *fuso horário de processamento de Oozie.*
    > * `${wfPath}`: O caminho para o workflow.xml.

2. Para guardar o ficheiro, selecione de Ctrl + X, introduza `Y`e, em seguida, selecione **Enter**.

3. Para copiar o ficheiro para o diretório de trabalho para esta tarefa, utilize o seguinte comando:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Para modificar o `job.xml` ficheiro, utilize o seguinte comando:

    ```
    nano job.xml
    ```

    Efetue as seguintes alterações:

   * Ao instruir o Oozie para executar o ficheiro de coordenador em vez do fluxo de trabalho, alterar `<name>oozie.wf.application.path</name>` para `<name>oozie.coord.application.path</name>`.

   * Para definir o `workflowPath` variável utilizado pelo coordenador, adicione o seguinte XML:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Substitua o `wasb://mycontainer@mystorageaccount.blob.core.windows` texto com o valor utilizado na entradas no ficheiro job.xml.

   * Para definir o início, o fim e a frequência para o coordenador, adicione o seguinte XML:

        ```xml
        <property>
            <name>coordStart</name>
            <value>2017-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2017-05-12T12:00Z</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>1440</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>UTC</value>
        </property>
        ```

       Estes valores de definir a hora de início para 12:00 PM 10 de Maio de 2017 e a hora de fim para 12 de Maio de 2017. O intervalo para executar esta tarefa está definido para diariamente. É a frequência em minutos, por isso, 24 horas, 60 minutos = 1440 minutos. Por fim, está definido o fuso horário UTC.

5. Para guardar o ficheiro, selecione de Ctrl + X, introduza `Y`e, em seguida, selecione **Enter**.

6. Para executar a tarefa, utilize o seguinte comando:

    ```
    oozie job -config job.xml -run
    ```

    Este comando submete e inicia a tarefa.

7. Se passar para o Oozie IU da web e selecione o **coordenador tarefas** separador, pode ver informações como na imagem seguinte:

    ![Separador tarefas da coordenador](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

    O **Materialization seguinte** entrada contém da próxima vez que a tarefa é executada.

8. Como a tarefa de fluxo de trabalho anterior, se selecionar a entrada da tarefa na IU da web apresenta informações sobre a tarefa:

    ![Coordenador tarefa informações](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

    > [!NOTE]
    > Esta imagem mostra apenas é executada com êxito da tarefa, não as ações individuais dentro do fluxo de trabalho agendado. Para ver as ações individuais, selecione uma do **ação** entradas.

    ![Informações de ação](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

## <a name="troubleshooting"></a>Resolução de problemas

Com a IU do Oozie, pode ver os registos de Oozie. A IU do Oozie também contém ligações para os registos de JobTracker para as tarefas de MapReduce que foram iniciadas pelo fluxo de trabalho. Deve ser o padrão para resolução de problemas:

   1. Ver a tarefa na IU da web do Oozie.

   2. Se existir um erro ou falha de uma ação específica, selecione a ação para ver se o **mensagem de erro** campo fornece mais informações sobre a falha.

   3. Se estiver disponível, utilize o URL da ação para ver mais detalhes, tais como os registos de JobTracker, para a ação.

Seguem-se os erros específicos que poderá encontrar e como resolvê-los.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: Não é possível inicializar o cluster

**Sintomas**: muda o estado da tarefa para **suspenso**. Os detalhes para mostrar a tarefa de `RunHiveScript` estado como **START_MANUAL**. Selecionar a ação apresenta a seguinte mensagem de erro:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Causa**: endereços de armazenamento de Blob do Azure a utilizados no **job.xml** o ficheiro não contém o contentor de armazenamento ou o nome de conta de armazenamento. O formato de endereço de Blob de armazenamento tem de ser `wasb://containername@storageaccountname.blob.core.windows.net`.

**Resolução**: alterar os endereços de armazenamento de BLOBs que utiliza a tarefa.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltuser"></a>JA002: Oozie não tem permissão para representar &lt;utilizador >

**Sintomas**: muda o estado da tarefa para **suspenso**. Os detalhes para mostrar a tarefa de `RunHiveScript` estado como **START_MANUAL**. Se selecionar a ação, mostra a seguinte mensagem de erro:

    JA002: User: oozie is not allowed to impersonate <USER>

**Causa**: as definições atuais de permissão não permitem Oozie representar a conta de utilizador especificado.

**Resolução**: Oozie pode representar os utilizadores a **utilizadores** grupo. Utilize o `groups USERNAME` para ver os grupos de que a conta de utilizador é membro. Se o utilizador não for um membro do **utilizadores** grupo, utilize o seguinte comando para adicionar o utilizador ao grupo:

    sudo adduser USERNAME users

> [!NOTE]
> Pode demorar vários minutos antes de HDInsight reconhece que o utilizador foi adicionado ao grupo.

### <a name="launcher-error-sqoop"></a>Iniciador erro (Sqoop)

**Sintomas**: muda o estado da tarefa para **KILLED**. Os detalhes para mostrar a tarefa de `RunSqoopExport` estado como **erro**. Se selecionar a ação, mostra a seguinte mensagem de erro:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Causa**: Sqoop não consegue carregar o controlador de base de dados necessário para aceder à base de dados.

**Resolução**: quando utilizar o Sqoop a partir de uma tarefa do Oozie, tem de incluir o controlador de base de dados com os outros recursos, tais como workflow.xml, as utilizações de tarefa. Além disso, que contém o controlador de base de dados do arquivo de referência a `<sqoop>...</sqoop>` secção o workflow.xml.

Por exemplo, para a tarefa neste documento, teria de utilizar os seguintes passos:

1. Copiar o `sqljdbc4.1.jar` do ficheiro para o **/tutoriais/useoozie** diretório:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /tutorials/useoozie/sqljdbc41.jar
    ```

2. Modificar o `workflow.xml` para adicionar o seguinte XML numa nova linha acima `</sqoop>`:

    ```xml
    <archive>sqljdbc41.jar</archive>
    ```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu como definir um fluxo de trabalho do Oozie e como executar uma tarefa de Oozie. Para obter mais informações sobre como trabalhar com o HDInsight, consulte os artigos seguintes:

* [Utilizar o coordenador de Oozie baseado no tempo com o HDInsight][hdinsight-oozie-coordinator-time]
* [Carregar dados para tarefas do Hadoop no HDInsight][hdinsight-upload-data]
* [Utilizar o Sqoop com o Hadoop no HDInsight][hdinsight-use-sqoop]
* [Utilizar o Hive com o Hadoop no HDInsight][hdinsight-use-hive]
* [Utilizar o Pig com o Hadoop no HDInsight][hdinsight-use-pig]
* [Desenvolver programas de Java MapReduce para o HDInsight][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563
[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: sql-database-create-configure.md
[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/en-us/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
