---
title: Ferramentas do Data Lake para Visual Studio com a Hortonworks Sandbox - Azure HDInsight | Microsoft Docs
description: "Saiba como utilizar as ferramentas do Azure Data Lake para Visual Studio com o sandbox Hortonworks em execução numa local VM. Com estas ferramentas, pode criar e executar tarefas do Hive e do Pig no sandbox e o resultado da tarefa de vista e o histórico."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: e3434c45-95d1-4b96-ad4c-fb59870e2ff0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/09/2017
ms.author: larryfr
ms.openlocfilehash: 904bbfcd02984b301d9eb9faaa6203f16139eba5
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Utilizar as ferramentas do Azure Data Lake para Visual Studio com a Hortonworks Sandbox

Azure Data Lake inclui ferramentas para trabalhar com clusters do Hadoop genéricos. Este documento fornece os passos necessários para utilizar as ferramentas do Data Lake com o Hortonworks Sandbox em execução numa máquina virtual local.

Utilizar o Hortonworks Sandbox permite-lhe trabalhar com o Hadoop localmente no seu ambiente de desenvolvimento. Depois de ter programado uma solução e pretende implementá-la à escala, em seguida, pode mover para um cluster do HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* A Hortonworks Sandbox, em execução numa máquina virtual no seu ambiente de desenvolvimento. Este documento foi escrito e testado com o sandbox em execução no Oracle VirtualBox. Para obter informações sobre como configurar o sandbox, consulte o [começar com a Hortonworks sandbox.](hadoop/apache-hadoop-emulator-get-started.md) documento.

* Visual Studio 2013, Visual Studio 2015 ou Visual Studio 2017 (qualquer edição).

* O [Azure SDK para .NET](https://azure.microsoft.com/downloads/) 2.7.1 ou posterior.

* O [do Azure Data Lake tools para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Configurar palavras-passe para a sandbox

Certifique-se de que a Hortonworks Sandbox está em execução. Em seguida, siga os passos a [começar a utilizar na Hortonworks Sandbox](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) documento. Estes passos configurar a palavra-passe para o SSH `root` conta e o Ambari `admin` conta. Estas palavras-passe é utilizados quando se liga ao sandbox do Visual Studio.

## <a name="connect-the-tools-to-the-sandbox"></a>Ligar as ferramentas para a sandbox

1. Abra o Visual Studio, selecione **vista**e, em seguida, selecione **Explorador de servidores**.

2. De **Explorador de servidores**, clique com botão direito do **HDInsight** entrada e, em seguida, selecione **ligar para o emulador do HDInsight**.

    ![Captura de ecrã do Explorador de servidores, com ligar para o emulador do HDInsight realçado](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. Do **ligar para o emulador do HDInsight** caixa de diálogo, introduza a palavra-passe que configurou para Ambari.

    ![Captura de ecrã da caixa de diálogo, com a caixa de texto de palavra-passe realçada](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Selecione **seguinte** para continuar.

4. Utilize o **palavra-passe** campo para introduzir a palavra-passe que configurou para o `root` conta. Deixe os outros campos no valor predefinido.

    ![Captura de ecrã da caixa de diálogo, com a caixa de texto de palavra-passe realçada](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    Selecione **seguinte** para continuar.

5. Aguarde que a validação de serviços para concluir. Em alguns casos, validação pode falhar e que lhe pede para atualizar a configuração. Se a validação falhar, selecione **atualização**e aguarde que a configuração e verificação para o serviço concluir.

    ![Captura de ecrã da caixa de diálogo, com o botão de atualização realçado](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [!NOTE]
    > O processo de atualização utiliza Ambari para modificar a configuração do Hortonworks Sandbox para o que é esperado pelas ferramentas do Data Lake para Visual Studio.

6. Depois de concluir a validação, selecione **concluir** para toda a configuração.
    ![Captura de ecrã da caixa de diálogo, com o botão Concluir realçado](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

     >[!NOTE]
     > Consoante a velocidade do seu ambiente de desenvolvimento e a quantidade de memória atribuída à máquina virtual, pode demorar alguns minutos para configurar e validar os serviços.

Depois de seguir estes passos, tem agora um **HDInsight local cluster** entrada no Explorador de servidores, sob o **HDInsight** secção.

## <a name="write-a-hive-query"></a>Escrever uma consulta do Hive

Ramo de registo fornece um idioma de consulta como o SQL Server (HiveQL) para trabalhar com dados estruturados. Utilize os seguintes passos para saber como executar consultas da pedido no local cluster.

1. No **Explorador de servidores**, com o botão direito a entrada para o cluster local que adicionou anteriormente e, em seguida, selecione **escrever uma consulta do Hive**.

    ![Captura de ecrã do Explorador de servidores, com uma consulta do Hive realçado de escrita](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    É apresentada uma nova janela de consulta. Aqui, pode rapidamente escrever e submeter uma consulta ao local cluster.

2. Na nova janela de consulta, introduza o seguinte comando:

        select count(*) from sample_08;

    Para executar a consulta, selecione **submeter** na parte superior da janela. Deixe os outros valores (**Batch** e nome do servidor), os valores predefinidos.

    ![Captura de ecrã da janela de consulta, com o botão para submeter realçado](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    Também pode utilizar o menu pendente junto a **submeter** para selecionar **avançadas**. Opções avançadas permitem-lhe fornecem opções adicionais ao submeter a tarefa.

    ![Caixa de diálogo de captura de ecrã de submeter Script](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. Depois de submeter a consulta, é apresentado o estado da tarefa. O estado da tarefa apresenta informações sobre a tarefa como processado por Hadoop. **Estado da tarefa** fornece o estado da tarefa. O estado é atualizado periodicamente, ou pode utilizar o ícone de atualização para atualizar manualmente o estado.

    ![Caixa de diálogo de captura de ecrã da vista de tarefas, com o estado da tarefa realçado](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    Depois do **estado da tarefa** alterações **concluído**, é apresentado um direcionado Acíclico gráfico (DAG). Este diagrama descreve o caminho de execução foi determinado pelo Tez ao processar a consulta do Hive. Tez é o motor de execução para o ramo de registo predefinido no local cluster.

    > [!NOTE]
    > Tez também é a predefinição quando estiver a utilizar clusters do HDInsight baseado em Linux. Não é a predefinição no HDInsight baseado em Windows. Para utilizá-lo, tem de adicionar a linha `set hive.execution.engine = tez;` para o início da sua consulta do Hive.

    Utilize o **resultado da tarefa** ligação para ver o resultado. Neste caso, é 823, o número de linhas na tabela sample_08. Pode ver informações de diagnóstico sobre a tarefa utilizando o **registo da tarefa** e **transferir o registo de YARN** ligações.

4. Também pode executar tarefas do Hive interativamente alterando o **Batch** campo para **interativo**. Em seguida, selecione **executar**.

    ![Captura de ecrã de interativa e executar botões realçados](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    Uma consulta interativa fluxos o registo de saída gerado durante o processamento para a **HiveServer2 saída** janela.

    > [!NOTE]
    > As informações são as mesmas que está disponível a partir de **registo da tarefa** ligação depois de terminar uma tarefa.

    ![Captura de ecrã do registo de saída](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## <a name="create-a-hive-project"></a>Criar um projeto do Hive

Também pode criar um projeto que contém vários scripts Hive. Utilize um projeto quando ter relacionados scripts ou se pretende armazenar os scripts de um sistema de controlo de versão.

1. No Visual Studio, selecione **ficheiro**, **novo**e, em seguida, **projeto**.

2. Na lista de projetos, expanda **modelos**, expanda **do Azure Data Lake**e, em seguida, selecione **ramo de registo (HDInsight)**. Na lista de modelos, selecione **do Hive de exemplo**. Introduza um nome e localização e, em seguida, selecione **OK**.

    ![Janela de captura de ecrã do novo projeto, com o Azure Data Lake, HIVE, do Hive de exemplo e OK realçado](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

O **do Hive de exemplo** projeto contém dois scripts, **WebLogAnalysis.hql** e **SensorDataAnalysis.hql**. Pode submeter estes scripts ao utilizar o mesmo **submeter** botão na parte superior da janela.

## <a name="create-a-pig-project"></a>Criar um projeto do Pig

Enquanto o ramo de registo fornece uma linguagem semelhante a SQL para trabalhar com dados estruturados, Pig funciona efetuando transformações de dados. PIg fornece um idioma (Pig Latin) que permite-lhe desenvolver um pipeline de transformações. Para utilizar o Pig com o cluster local, siga estes passos:

1. Abra o Visual Studio e selecione **ficheiro**, **novo**e, em seguida, **projeto**. Na lista de projetos, expanda **modelos**, expanda **do Azure Data Lake**e, em seguida, selecione **Pig (HDInsight)**. Na lista de modelos, selecione **Pig aplicação**. Introduza um nome, localização e, em seguida, selecione **OK**.

    ![Janela de captura de ecrã do novo projeto, com o Azure Data Lake, Pig, Pig aplicação e OK realçado](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. Introduza o seguinte texto como o conteúdo a **script.pig** ficheiro que foi criado com este projeto.

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int,
            ip_address:chararray,
            date:chararray,
            time:chararray,
            landing_page:chararray,
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Enquanto o Pig utiliza um idioma diferente de ramo de registo, como executar as tarefas é consistente entre ambos os idiomas, através de **submeter** botão. Na lista pendente junto a seleção **submeter** apresenta uma caixa de diálogo de submissão avançada para o Pig.

    ![Caixa de diálogo de captura de ecrã de submeter Script](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)

3. Também é apresentado o estado da tarefa e de saída, o mesmo que uma consulta do Hive.

    ![Captura de ecrã de uma tarefa Pig concluída](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## <a name="view-jobs"></a>Ver tarefas

Ferramentas do Data Lake também permitem-lhe visualizar facilmente as informações sobre as tarefas que foram executadas no Hadoop. Utilize os seguintes passos para ver as tarefas que foram executadas no local cluster.

1. De **Explorador de servidores**, faça duplo clique no cluster local e, em seguida, selecione **ver tarefas**. É apresentada uma lista de tarefas que foram submetidos para o cluster.

    ![Captura de ecrã do Explorador de servidores, com as tarefas da vista realçados](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. Na lista de tarefas, selecione uma para ver os detalhes da tarefa.

    ![Captura de ecrã da tarefa de Browser, com uma das tarefas realçadas](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    As informações apresentadas são semelhantes à veem depois de executar uma consulta do Hive ou Pig, incluindo ligações para ver o resultado e registar informações.

3. Também pode modificar e volte a submeter a tarefa a partir daqui.

## <a name="view-hive-databases"></a>Ver as bases de dados do Hive

1. No **Explorador de servidores**, expanda o **HDInsight local cluster** entrada e, em seguida, expanda **bases de dados do Hive**. O **predefinido** e **xademo** bases de dados no local cluster são apresentados. Uma base de dados de expansão mostra as tabelas na base de dados.

    ![Captura de ecrã do Explorador de servidores, com bases de dados expandidos](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. Expandir uma tabela mostra as colunas de nessa tabela. Para ver rapidamente os dados, uma tabela com o botão direito e selecione **vista primeiras 100 linhas**.

    ![Captura de ecrã do Explorador de servidores, com a tabela expandido e vista primeiras 100 linhas selecionadas](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### <a name="database-and-table-properties"></a>Propriedades de base de dados e tabela

Pode ver as propriedades de uma base de dados ou tabela. Selecionar **propriedades** apresenta detalhes para o item selecionado na janela Propriedades. Por exemplo, consulte as informações mostradas na captura de ecrã seguinte:

![Janela de captura de ecrã de propriedades](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### <a name="create-a-table"></a>Criar uma tabela

Para criar uma tabela, faça duplo clique uma base de dados e, em seguida, selecione **Create Table**.

![Captura de ecrã do Explorador de servidores, com Create Table realçado](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

Em seguida, pode criar a tabela utilizando um formulário. Na parte inferior da captura de ecrã seguinte, pode ver o HiveQL não processado que é utilizado para criar a tabela.

![Captura de ecrã com o formato utilizado para criar uma tabela](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## <a name="next-steps"></a>Passos seguintes

* [Learning ropes de Hortonworks Sandbox](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Tutorial do Hadoop - introdução HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
