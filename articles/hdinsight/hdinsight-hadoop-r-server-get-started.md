---
title: "Introdução ao R Server no HDInsight - Azure | Microsoft Docs"
description: Saiba como criar um Apache Spark num cluster do HDInsight que inclui o R Server e submeter um script R no cluster.
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 07/13/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: 818f7756189ed4ceefdac9114a0b89ef9ee8fb7a
ms.openlocfilehash: ee4298b91f4e2b215b5faabaad96323f4ef234b8
ms.contentlocale: pt-pt
ms.lasthandoff: 07/14/2017

---
# <a name="get-started-using-r-server-on-hdinsight"></a>Introdução à utilização do R Server no HDInsight

O HDInsight inclui uma opção para integrar o R Server no seu cluster do HDInsight. Esta opção permite que os scripts R utilizem o Spark e o MapReduce para executar cálculos distribuídos. Neste documento, vai aprender a criar um R Server num cluster do HDInsight e, em seguida, executar um script R que demonstre a utilização do Spark para cálculos de R distribuídos.


## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**: antes de começar este tutorial, tem de ter uma subscrição do Azure. Veja o artigo [Get Microsoft Azure free trial](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Obter uma avaliação gratuita do Microsoft Azure) para obter mais informações.
* **Um cliente Secure Shell (SSH)**: é utilizado um cliente SSH para ligar remotamente ao cluster do HDInsight e executar comandos diretamente no mesmo. Para obter mais informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight).](hdinsight-hadoop-linux-use-ssh-unix.md)
* **Chaves SSH (opcionais)**: pode proteger a conta do SSH utilizada para a ligação ao cluster com uma palavra-passe ou uma chave pública. É mais fácil utilizar uma palavra-passe, a qual lhe permite começar sem que seja necessário criar um par de chaves públicas/privadas. Contudo, a utilização de chaves é mais segura.

> [!NOTE]
> Os passos neste documento partem do princípio de que está a utilizar uma palavra-passe.


## <a name="automated-cluster-creation"></a>Criação de clusters automatizada

Pode automatizar a criação de HDInsight R Servers com modelos do Azure Resource Manager, do SDK e também do PowerShell.

* Para criar um R Server com um modelo do Azure Resource Manager, veja [Deploy an R server HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/) (Implementar um cluster do HDInsight no R Server).
* Para criar um R Server com o SDK do .NET, consulte [create Linux-based clusters in HDInsight using the .NET SDK (criar clusters baseados em Linux no HDInsight com o SDK do .NET).](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* Para implementar o R Server com o powershell, veja o artigo [creating an R Server on HDInsight with PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) (Criar um R Server no HDInsight com o PowerShell).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>Criar o cluster com o portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **NOVO** -> **Informações + Análise**, -> **HDInsight**.

    ![Imagem da criação de um cluster novo](./media/hdinsight-hadoop-r-server-get-started/newcluster.png)

3. Na experiência **Criação rápida**, introduza um nome para o cluster no campo **Nome do cluster**. Se tiver várias subscrições do Azure, utilize a entrada **Subscrição** para selecionar a que pretende utilizar.

    ![Seleção do nome do cluster e da subscrição](./media/hdinsight-hadoop-r-server-get-started/clustername.png)

4. Selecione **Tipo de cluster** para abrir o painel **Configuração do cluster**. No painel **Configuração do cluster**, selecione as seguintes opções:

    * **Tipo de Cluster**: R Server
    * **Versão**: selecione a versão do R Server a instalar no cluster. A versão disponível atualmente é ***R Server 9.1 (HDI 3.6)***. Estão disponíveis [aqui](https://msdn.microsoft.com/microsoft-r/notes/r-server-notes) as notas de versão das versões do R Server disponíveis.
    * **Edição de comunidade do R Studio para R Server**: este IDE baseado no browser está instalado por predefinição no nó de extremidade. Se preferir não instalá-lo, desmarque a caixa de verificação. Se optar por instalar, o URL para aceder ao início de sessão do RStudio Server está disponível num painel de aplicação do portal do seu cluster, depois de ser criado.
    * Deixe os valores predefinidos nas outras opções e utilize o botão **Selecionar** para guardar o tipo de cluster.

        ![Captura de ecrã do painel do tipo de cluster](./media/hdinsight-hadoop-r-server-get-started/clustertypeconfig.png)

5. Introduza um **Nome de utilizador de início de sessão do cluster** e uma **Palavra-passe de início de sessão do cluster**.

    Especifique um **Nome de utilizador SSH**. O SSH é utilizado para ligar remotamente ao cluster através de um cliente **Secure Shell (SSH)**. Pode especificar o utilizador SSH nesta caixa de diálogo ou após o cluster ter sido criado (no separador Configuração do cluster). O R Server é configurado para esperar o **Nome de utilizador SSH** ”utilizadorremoto”.  **Se utilizar um nome de utilizador diferente, tem de realizar um passo adicional depois de o cluster ser criado.**

    Deixe a caixa selecionada para **Utilizar a mesma palavra-passe como início de sessão do cluster** para utilizar **PALAVRA-PASSE** como o tipo de autenticação, a não ser que prefira utilizar uma chave pública.  Se quiser aceder ao R Server no cluster por meio de um cliente remoto, como, por exemplo, o RTVS, o RStudio ou outro IDE de ambiente de trabalho, precisa de um par de chaves públicas/privadas. Se instalar a edição de comunidade do RStudio Server, tem de escolher uma palavra-passe SSH.     

    Para criar e utilizar um par de chaves públicas/privadas, desmarque **Utilizar a mesma palavra-passe do início de sessão do cluster**, selecione **CHAVE PÚBLICA** e proceda da seguinte forma. Estas instruções pressupõem que tem o Cygwin com ssh-keygen ou equivalente instalado.

    * Gere um par de chaves públicas/privadas a partir da linha de comandos do seu computador portátil:

        ssh-keygen -t rsa -b 2048

    * Siga a mensagem para nomear um ficheiro de chaves e introduza uma frase de acesso, para mais segurança. O ecrã deverá ser parecido à imagem seguinte:

        ![Linha de comandos SSH no Windows](./media/hdinsight-hadoop-r-server-get-started/sshcmdline.png)

    * Este comando cria um ficheiro de chave privada e um ficheiro de chave pública com o nome <private-key-filename>.pub, omo, por exemplo, “furiosa” e “furiosa.pub”.

        ![Diretório SSH](./media/hdinsight-hadoop-r-server-get-started/dir.png)

    * Em seguida, especifique o ficheiro de chave pública (&#42;.pub) quando atribuir as credenciais de cluster do HDI e, por fim, confirme o grupo de recursos e a região e selecione **Seguinte**.

        ![Painel Credenciais](./media/hdinsight-hadoop-r-server-get-started/publickeyfile.png)  

   * Altere as permissões no ficheiro de chave privada no computador portátil:

        chmod 600 <private-key-filename>

   * Utilize o ficheiro de chave privada com SSH para início de sessão remoto:

        ssh –i <private-key-filename> remoteuser@<hostname public ip>

      Ou como parte da definição do seu contexto de cálculo do Hadoop Spark para R Server no cliente. Veja a subsecção **Using Microsoft R Server as a Hadoop Client** (Utilizar o Microsoft R Server como Cliente do Hadoop), em [Create a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) (Criar Um Contexto de Cálculo para o Spark).

6. A criação rápida leva-o para o painel **Armazenamento**, para selecionar as definições da conta de armazenamento que vão ser utilizadas para a localização principal do sistema de ficheiros HDFS que o cluster utiliza. Selecione uma conta de Armazenamento do Azure nova ou já existente ou uma conta do Data Lake Store já existente.

    - Se optar por uma conta de Armazenamento do Azure, pode selecionar uma já existente ao escolher **Selecionar uma conta de armazenamento** e, em seguida, selecioná-la. Utilize a ligação **Criar Nova**, na secção **Selecionar uma conta de armazenamento**, para criar uma conta nova.

      > [!NOTE]
      > Se selecionar **Nova**, tem de introduzir um nome para essa conta de armazenamento. Se o nome for aceite, aparece uma marca de verificação verde.

      O **Contentor Predefinido** é predefinido como o nome do cluster. Deixe esta predefinição como o valor.

      Se tiver sido selecionada a opção de conta de armazenamento nova, aparece um pedido para selecionar a **Localização**, para escolher em que região criar a conta de armazenamento.  

         ![Painel Origem de dados](./media/hdinsight-getting-started-with-r/datastore.png)  

      > [!IMPORTANT]
      > Selecionar a localização da origem de dados predefinida também define a localização do cluster do HDInsight. O cluster e a origem de dados predefinida têm de estar na mesma região.

    - Se quiser utilizar uma conta do Data Lake Store já existente, selecione a conta de armazenamento do ADLS que vai ser utilizada e adicione a identidade do *AAD* do cluster ao seu cluster, para permitir acesso ao arquivo. Para obter mais informações sobre este processo, veja [Creating HDInsight cluster with Data Lake Store using Azure portal](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal) (Utilizar o portal do Azure para criar clusters do HDInsight com o Data Lake Store).

    Utilize o botão **Selecionar** para guardar a configuração da origem de dados.


7. O painel **Resumo** é, depois, apresentado, para validar todas as definições. Aqui, pode alterar o **Tamanho do cluster**, para modificar o número de servidores do cluster, bem como especificar **Ações de script** que queira executar. A menos que saiba que precisa de um cluster maior, deixe o número de nós de trabalho no valor predefinido de `4`. O custo estimado do cluster é apresentado no painel.

    ![resumo do cluster](./media/hdinsight-hadoop-r-server-get-started/clustersummary.png)

   > [!NOTE]
   > Se necessário, pode redimensionar o cluster mais tarde, no Portal (**Cluster** -> **Definições** -> **Dimensionar Cluster**), de modo a aumentar ou diminuir o número de nós de trabalho.  Este redimensionamento pode ser útil para reduzir a atividade do cluster quando não está a ser utilizado ou para adicionar capacidade, para dar resposta a tarefas maiores.
   >
   >

   Alguns fatores a ter em conta no dimensionamento do cluster, dos nós de dados e do nó de extremidade incluem:  

   * O desempenho das análises distribuídas do R Server no Spark é proporcional ao número de nós de trabalho quando a quantidade de dados é grande.  

   * O desempenho das análises do R Server é linear quanto ao tamanho dos dados que estão a ser analisados. Por exemplo:  

     * Relativamente a quantidades de dados pequenas ou modestas, o desempenho é superior se forem analisados num contexto de cálculo local no nó de extremidade.  Para obter mais informações sobre os cenários em que os contextos de cálculo local e do Spark funcionam melhor, veja Compute context options for R Server on HDInsight (Opções de contexto de cálculo para o R Server no HDInsight).<br>
     * Se iniciar sessão no nó de extremidade e executar o script R, são executadas <strong>localmente</strong> todas as funções naquele nó, menos as funções ScaleR rx. Desta forma, a memória e o número de núcleos do nó de extremidade devem ser redimensionados em conformidade. O mesmo se aplica se utilizar o R Server no HDI como um contexto de cálculo remoto no seu computador portátil.

     ![Painel Escalões de preço do nó](./media/hdinsight-hadoop-r-server-get-started/pricingtier.png)

     Utilize o botão **Selecionar** para guardar a configuração do preço do nó.

   Também existe uma ligação para **Transferir modelo e parâmetros**. Clique nesta ligação para ver scripts que podem ser utilizados para automatizar a criação de um cluster com a configuração selecionada. Estes scripts também estão disponíveis a partir da entrada do portal do Azure do cluster depois de este ter sido criado.

   > [!NOTE]
   > A criação do cluster demora algum tempo; aproximadamente 20 minutos. Para verificar o processo de criação, utilize o mosaico no Startboard ou a entrada **Notificações** no lado esquerdo da página.
   >
   >

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Ligar ao RStudio Server

Se tiver escolhido incluir a edição de comunidade do RStudio Server na sua instalação, pode aceder ao início de sessão do RStudio através de dois métodos diferentes.

1. Aceda ao URL seguinte (em que **CLUSTERNAME** é o nome do cluster que criou):

    https://**CLUSTERNAME**.azurehdinsight.net/rstudio/

2. Abra a entrada do seu cluster no portal do Azure, selecione a ligação rápida **Dashboards do R Server** e, em seguida, selecione o **Dashboard do R Studio**:

     ![Aceder ao dashboard do R Studio](./media/hdinsight-getting-started-with-r/rstudiodashboard1.png)

     ![Aceder ao dashboard do R Studio](./media/hdinsight-getting-started-with-r/rstudiodashboard2.png)

   > [!IMPORTANT]
   > Independentemente do método utilizado, vai ter de se autenticar duas vezes da primeira vez que iniciar sessão.  Na primeira autenticação, indique o *userID* e a *palavra-passe de administrador do cluster*. Na segunda mensagem, indique o *userID* e a *palavra-passe de SSH*. Os inícios de sessão seguintes só precisam do *userid* e da *palavra-passe de SSH*.

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-r-server-edge-node"></a>Ligar ao nó de extremidade do R Server

Utilize SSH para ligar ao nó de extremidade do R Server do cluster do HDInsight com o comando seguinte:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

> [!NOTE]
> Pode localizar o endereço `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` no portal do Azure ao selecionar o seu cluster e **Todas as Definições** -> **Aplicações** -> **RServer**. São apresentadas as informações do Ponto Final de SSH relativas ao nó de extremidade.
>
> ![Imagem do Ponto Final de SSH do nó de extremidade](./media/hdinsight-hadoop-r-server-get-started/sshendpoint.png)
>
>

Se utilizou uma palavra-passe para proteger a sua conta de utilizador do SSH, é solicitado que a introduza. Se utilizou uma chave pública, poderá ter de utilizar o parâmetro `-i` para especificar a chave privada correspondente. Por exemplo:

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Para obter mais informações, veja [Ligar ao HDInsight (Hadoop) através de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

Quando estiver ligado, vai deparar-se com uma mensagem semelhante à seguinte:

    sername@ed00-myrser:~$

<a name="enable-concurrent-users"></a>
## <a name="enable-multiple-concurrent-users"></a>Permitir vários utilizadores em simultâneo

Pode adicionar mais utilizadores ao nó de extremidade em que a versão de comunidade do RStudio é executada para permitir vários utilizadores em simultâneo.

Quando criar um cluster do HDInsight, tem de fornecer dois utilizadores -- um utilizador HTTP e outro SSH:

![Utilizador simultâneo 1](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-1.png)

- **Nome de utilizador do cluster**: um utilizador HTTP utilizado para autenticação através do gateway do HDInsight que é utilizado para proteger os clusters do HDInsight que criou. Este utilizador HTTP é utilizado para aceder à IU do Ambari, à IU do YARN, bem como a outros componentes da IU.
- **Nome de utilizador Secure Shell (SSH)**: um utilizador SSH para aceder ao cluster através de secure shell. Este utilizador é um utilizador no sistema Linux para todos os nós principais, nós de trabalho e nós de extremidade. Desta forma, pode utilizar o secure shell para aceder a qualquer nó num cluster remoto.

A versão de comunidade do R Studio Server utilizada no Microsoft R Server no cluster do tipo HDInsight só aceita como mecanismo de início de sessão os nomes de utilizador e as palavras-passe do Linux. Não suporta a transmissão de tokens. Assim, se tiver criado um cluster novo e quiser utilizar o R Studio para aceder ao mesmo, tem de iniciar sessão duas vezes.

- Primeiro, inicie sessão com as credenciais de utilizador HTTP através do Gateway do HDInsight: 

    ![Utilizador simultâneo 2a](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-2a.png)

- Depois, utilize as credenciais de utilizador SSH para iniciar sessão no RStudio:
  
    ![Utilizador simultâneo 2b](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-2b.png)

Atualmente, só é possível criar uma conta de utilizador SSH quando são aprovisionados clusters do HDInsight. Consequentemente, para permitir que vários utilizadores acedam ao Microsoft R Server em clusters do HDInsight, temos de criar mais utilizadores no sistema Linux.

Uma vez que a versão de comunidade do RStudio Server está em execução no nó de extremidade do cluster, há vários passos envolvidos:

1. Utilizar o utilizador SSH criado para iniciar sessão no nó de extremidade
2. Adicionar mais utilizadores do Linux ao nó de extremidade
3. Utilizar a versão de comunidade do RStudio com o utilizador criado

### <a name="step-1-use-the-created-ssh-user-to-log-in-to-the-edge-node"></a>Passo 1: Iniciar sessão no nó de extremidade com o utilizador SSH criado

Transfira uma ferramenta de SSH (como o Putty) e utilize o utilizador SSH existente para iniciar sessão. Depois, siga as instruções indicadas em [Ligar ao HDInsight (Hadoop) através de SSH](hdinsight-hadoop-linux-use-ssh-unix.md) para aceder ao nó de extremidade. O endereço do nó de extremidade do R Server no cluster do HDInsight é: *clustername-ed-ssh.azurehdinsight.net*


### <a name="step-2-add-more-linux-users-in-edge-node"></a>Passo 2: Adicionar mais utilizadores do Linux ao nó de extremidade

Para adicionar um utilizador ao nó de extremidade, execute os comandos:

    sudo useradd yournewusername -m
    sudo passwd yourusername

Deverá ver serem devolvidos os itens seguintes: 

![Utilizador simultâneo 3](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-3.png)

Quando for pedida a “Palavra-passe de Kerberos atual:”, basta premir **Enter** para ignorar. A opção `-m` no comando `useradd` indica que o sistema vai criar uma pasta raiz para o utilizador, que é necessária na versão de comunidade do RStudio.


### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Passo 3: Utilizar a versão de comunidade do RStudio com o utilizador criado

Inicie sessão no RStudio com o utilizador criado:

![Utilizador simultâneo 4](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-4.png)

Repare que o RStudio indica que está a iniciar sessão com o utilizador novo (aqui, por exemplo, *sshuser6*). 

![Utilizador simultâneo 5](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-5.png)

Também pode iniciar sessão com as credenciais originais (por predefinição, é o *sshuser*) simultaneamente a partir de outra janela do browser.

Pode utilizar funções scaleR para submeter tarefas. Eis um exemplo dos comandos utilizados para executar uma tarefa:

    # Set the HDFS (WASB) location of example data.
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storaging data temporarily.
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder.
    remoteDir <- "http://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))

    # Set directory in bigDataDirRoot to load the data.
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory.
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input.
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (WASB) file system.
    hdfsFS <- RxHdfsFileSystem()

    # Create info list for the airline data.
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names.
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS.
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in local system.
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use.
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

    # Define the Spark compute context.
    mySparkCluster <- RxSpark()

    # Set the compute context.
    rxSetComputeContext(mySparkCluster)

    # Run a logistic regression.
    system.time(
        modelSpark <- rxLogit(formula, data = airOnTimeData)
    )

    # Display a summary.
    summary(modelSpark)


Repare que as tarefas submetidas têm nomes de utilizadores diferentes na IU do YARN:

![Utilizador simultâneo 6](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-6.png)

Repare também que os utilizadores adicionados recentemente não têm privilégios de raiz no sistema Linux, mas têm acesso a todos os ficheiros no armazenamento remoto do HDFS e do WASB.


<a name="use-r-console"></a>
## <a name="use-the-r-console"></a>Utilizar a consola de R

1. Na sessão SSH, utilize o comando seguinte para iniciar a consola do R:  

        R

2. Deverá ver um resultado semelhante ao seguinte:
    
    R version 3.2.2 (2015-08-14) -- "Fire Safety"  Copyright (C) 2015 The R Foundation for Statistical Computing  Platform: x86_64-pc-linux-gnu (64-bit)

    R is free software and comes with ABSOLUTELY NO WARRANTY.
    You are welcome to redistribute it under certain conditions.
    Type 'license()' or 'licence()' for distribution details.

    Natural language support but running in an English locale

    R is a collaborative project with many contributors.
    Type 'contributors()' for more information and 'citation()' on how to cite R or R packages in publications.

    Type 'demo()' for some demos, 'help()' for on-line help, or 'help.start()' for an HTML browser interface to help.
    Type 'q()' to quit R.

    Microsoft R Server version 8.0: an enhanced distribution of R  Microsoft packages Copyright (C) 2016 Microsoft Corporation

    Type 'readme()' for release notes.
    >

3. Na linha de comandos `>`, pode introduzir o código R. O R Server inclui pacotes que lhe permitem interagir facilmente com o Hadoop e executar cálculos distribuídos. Por exemplo, utilize o comando seguinte para ver a raiz do sistema de ficheiros predefinido do cluster do HDInsight:

    rxHadoopListFiles("/")

4. Também pode utilizar o endereçamento de estilo WASB.

    rxHadoopListFiles("wasbs:///")


## <a name="using-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>Utilizar o R Server no HDI numa instância remota do Microsoft R Server ou do Microsoft R Client

É possível configurar o acesso ao contexto de cálculo do HDI Hadoop Spark a partir de uma instância remota do Microsoft R Server ou do cliente do Microsoft R em execução num computador portátil ou de secretária. Veja a subsecção **Using Microsoft R Server as a Hadoop Client** (Utilizar o Microsoft R Server como Cliente do Hadoop), em [Creating a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started.md) (Criar Um Contexto de Cálculo para o Spark). Para tal, tem de especificar as opções seguintes quando definir o contexto de cálculo RxSpark no computador portátil: hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches e sshProfileScript. Por exemplo:


    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- 'rkrrehdi1-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- 'remoteuser'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )


## <a name="use-a-compute-context"></a>Utilizar um contexto de cálculo

O contexto de cálculo permite-lhe controlar se a computação é feita localmente no nó de extremidade ou distribuída pelos nós do cluster do HDInsight.

1. A partir do RStudio Server ou da consola de R (numa sessão SSH), utilize o código seguinte para carregar dados de exemplo para o armazenamento predefinido do HDInsight:

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"

        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)

        # Download data to the tmp folder
        remoteDir <- "http://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
        download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
        download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
        download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
        download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
        download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
        download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
        download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
        download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
        download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
        download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
        download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
        download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))

        # Set directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

        # Make the directory
        rxHadoopMakeDir(inputDir)

        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. Em seguida, vamos criar algumas informações de dados e definir duas origens de dados, para que possamos trabalhar com os mesmos.

        # Define the HDFS (WASB) file system
        hdfsFS <- RxHdfsFileSystem()

        # Create info list for the airline data
        airlineColInfo <- list(
             DAY_OF_WEEK = list(type = "factor"),
             ORIGIN = list(type = "factor"),
             DEST = list(type = "factor"),
             DEP_TIME = list(type = "integer"),
             ARR_DEL15 = list(type = "logical"))

        # get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in hdfs
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

        # Define the text data source in local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. Vamos utilizar o contexto de cálculo local para executar um regressão logística nos dados.

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    Deverá ver um resultado que acaba com linhas semelhantes às seguintes:

        Data: airOnTimeDataLocal (RxTextData Data Source)
        File name: /tmp/AirOnTimeCSV2012
        Dependent variable(s): ARR_DEL15
        Total independent variables: 634 (Including number dropped: 3)
        Number of valid observations: 6005381
        Number of missing observations: 91381
        -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)

        Coefficients:
                         Estimate Std. Error z value Pr(>|z|)
         (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
         ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
         ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
         ......
         DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
         DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
         DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
         DEST=BPT         Dropped    Dropped Dropped  Dropped

         ---

         Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

         Condition number of final variance-covariance matrix: 11904202
         Number of iterations: 7

4. Em seguida, vamos executar a mesma regressão logística com o contexto do Spark, O contexto do Spark distribui o processamento por todos os nós de trabalho do cluster do HDInsight.

        # Define the Spark compute context
        mySparkCluster <- RxSpark()

        # Set the compute context
        rxSetComputeContext(mySparkCluster)

        # Run a logistic regression
        system.time(  
           modelSpark <- rxLogit(formula, data = airOnTimeData)
        )
        
        # Display a summary
        summary(modelSpark)


   > [!NOTE]
   > Também pode utilizar o MapReduce para distribuir a computação por vários nós dos cluster. Para obter mais informações sobre o contexto de cálculo, veja [Compute context options for R Server on HDInsight (Opções do contexto de cálculo para o R Server no HDInsight)](hdinsight-hadoop-r-server-compute-contexts.md).


## <a name="distribute-r-code-to-multiple-nodes"></a>Distribuir o código R por vários nós

Com o R Server, pode utilizar facilmente código R já existente e utilizar `rxExec` para executá-lo em vários nós do cluster. Esta função é útil para fazer varrimentos ou simulações de parâmetros. O código abaixo é um exemplo de como utilizar `rxExec`:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Se ainda estiver a utilizar o contexto do Spark ou do MapReduce, este comando devolve o valor nodename para os nós de trabalho em que o código `(Sys.info()["nodename"])` é executado. Por exemplo, num cluster de quatro nós, é de esperar que vai receber um resultado semelhante ao seguinte:

    $rxElem1
        nodename
    "wn3-myrser"

    $rxElem2
        nodename
    "wn0-myrser"

    $rxElem3
        nodename
    "wn3-myrser"

    $rxElem4
        nodename
    "wn3-myrser"


## <a name="accessing-data-in-hive-and-parquet"></a>Aceder a Dados no Hive e no Parquet

Está disponível uma funcionalidade no R Server 9.1 que permite acesso direto a dados no Hive e no Parquet, para utilização por funções ScaleR no contexto de cálculo do Spark. Estas capacidades estão disponíveis através de novas funções de origem de dados de ScaleR chamadas RxHiveData e RxParquetData, as quais funcionam com a utilização do Spark SQL para carregar dados diretamente para um DataFrame do Spark para análise por parte do ScaleR.  

O código abaixo disponibiliza alguns códigos de exemplos de utilização das funções novas:

    #Create a Spark compute context:
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model:
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model:
    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)

    #Check on Spark data objects, cleanup, and close the Spark session:
    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)


Para obter informações adicionais sobre a utilização destas funções novas, utilize a ajuda online do R Server com os comandos `?RxHivedata` e `?RxParquetData`.  


## <a name="install-additional-r-packages-on-the-edge-node"></a>Instalar pacotes de R adicionais no nó de extremidade

Se quiser instalar pacotes de R adicionais no nó de extremidade, pode utilizar `install.packages()` diretamente na consola de R quando estiver ligado ao nó de extremidade através de SSH. No entanto, se tiver de instalar os pacotes de R em nós de trabalho do cluster, tem de utilizar uma Ação de Script.

As Ações de Script são scripts de Bash utilizadas para fazer alterações de configuração ao cluster do HDInsight ou para instalar mais software, como pacotes de R adicionais. Para instalar pacotes adicionais com uma Ação de Script, utilize os passos seguintes:

> [!IMPORTANT]
> Só é possível utilizar as Ações de Script para instalar pacotes de R adicionais depois de o cluster ter sido criado. Não utilize este procedimento durante a criação do cluster, pois o script depende de o R Server estar completamente instalado e configurado.
>
>

1. No [portal do Azure](https://portal.azure.com), selecione o R Server no cluster do HDInsight.

2. No painel **Definições**, selecione **Ações de Script** e **Submeter Nova**, para submeter uma Ação de Script nova.

   ![Imagem do painel de ações de script](./media/hdinsight-hadoop-r-server-get-started/scriptaction.png)

3. No painel **Submeter ação de script**, indique as informações seguintes:

   * **Nome**: um nome amigável para identificar este script

   * **URI do script de Bash**: `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **Cabeçalho**: este item deve estar **desmarcado**

   * **Nó de trabalho**: este item deve estar **marcado**

   * **Nós de extremidade**: este item deve estar **desmarcado**

   * **Zookeeper**: este item deve estar **desmarcado**

   * **Parâmetros**: os pacotes de R que vão ser instalados. Por exemplo, `bitops stringr arules`

   * **Manter este script...**: este item deve estar **marcado**  

   > [!NOTE]
   > 1. Por predefinição, todos os pacotes de R são instalados a partir de um instantâneo do repositório Microsoft MRAN consistente com a versão do R Server que foi instalada. Se quiser instalar versões novas dos pacotes, existe algum risco de incompatibilidades. Contudo, este tipo de instalação é possível mediante a especificação de `useCRAN` como o primeiro elemento da lista do pacote, como, por exemplo, `useCRAN bitops, stringr, arules`.  
   > 2. Alguns pacotes de R requerem bibliotecas do sistema Linux adicionais. Para sua comodidade, pré-instalámos as dependências de que os cem pacotes de R mais populares precisam. No entanto, se o pacote ou pacotes de R que instalar requeiram mais bibliotecas para além destas, tem de transferir o script de base utilizado aqui e adicionar passos para instalar as bibliotecas do sistema. Em seguida, tem de carregar o script modificado para um contentor de blobs público no armazenamento do Azure e utilizá-lo para instalar os pacotes.
   >    Para obter mais informações sobre como programar Ações de Script, veja [Script Action development (Programação de Ações de Script)](hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Adicionar uma ação de script](./media/hdinsight-getting-started-with-r/submitscriptaction.png)

4. Selecione **Criar** para executar o script. Após a conclusão do script, os pacotes de R estão disponíveis em todos os nós de trabalho.


## <a name="using-microsoft-r-server-operationalization"></a>Utilizar a Operacionalização do Microsoft R Server

Quando a modelação de dados estiver concluída, pode operacionalizar o modelo para fazer previsões. Para configurar a operacionalização do Microsoft R Server, siga os passos abaixo:

Primeiro, aceda ao Nó de extremidade através de ssh. Por exemplo, 

    ssh -L USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Depois de utilizar ssh, altere o diretório da versão relevante e utilize a elevação sudo do dll do dotnet: 

- Para o Microsoft R Server 9.1:

    cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0   sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

- Para o Microsoft R Server 9.0:

    cd /usr/lib64/microsoft-deployr/9.0.1   sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

Para configurar a operacionalização do Microsoft R Server com uma configuração One box, faça o seguinte:

1. Selecione “Configurar o R Server para Operacionalização”
2. Selecione “A. One-box (Web + nós de computação)”
3. Introduza uma palavra-passe para o utilizador **administrador**

![operacionalização de one-box](./media/hdinsight-hadoop-r-server-get-started/admin-util-one-box-.png)

Como passo opcional, pode executar um teste de diagnóstico, conforme mostrado abaixo, para realizar verificações de Diagnóstico:

1. Selecione "6. Executar testes de diagnóstico"
2. Selecione “A. Testar configuração”
3. Introduza Nome de utilizador = "administrador" e a palavra-passe do passo de configuração anterior
4. Confirmar o Estado de Funcionamento Geral = aprovado
5. Saia do Utilitário de Administração
6. Saia do SSH

![Diagnóstico de operacionalização](./media/hdinsight-hadoop-r-server-get-started/admin-util-diagnostics.png)

Nesta fase, a configuração da operacionalização está concluída. Agora, pode utilizar o pacote “mrsdeploy” no seu RClient para se ligar à Operacionalização no nó de extremidade e começar a utilizar as funcionalidades do mesmo, como a [execução remota](https://msdn.microsoft.com/microsoft-r/operationalize/remote-execution) e os [serviços Web](https://msdn.microsoft.com/microsoft-r/mrsdeploy/mrsdeploy-websrv-vignette). Consoante o seu cluster estar ou não configurado numa rede virtual, poderá ter de configurar o túnel de encaminhamento de portas através do início de sessão SSH. As secções a seguir explicam como configurar este túnel.

### <a name="rserver-cluster-on-virtual-network"></a>Cluster do RServer numa rede virtual

Confirme que permite o tráfego pela porta 12800 para o nó de extremidade. Desta forma, pode utilizar o nó de extremidade para ligar à funcionalidade Operacionalização.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Se `remoteLogin()` não se conseguir ligar ao nó de extremidade, mas conseguir aceder a este através de SSH, tem de verificar se a regra para permitir o tráfego na porta 12800 foi devidamente configurada ou não. Se o problema persistir, pode utilizar uma solução, que consiste em configurar o túnel de encaminhamento de portas através de SSH. Veja a secção seguinte para obter instruções.

### <a name="rserver-cluster-not-set-up-on-virtual-network"></a>Cluster do RServer não configurado numa rede virtual

Se o seu cluster não estiver configurado numa VNet ou se estiver com problemas de conectividade através da VNet, pode utilizar o túnel de encaminhamento de portas SSH.

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Também pode configurá-lo no Putty.

![ligação ssh ao putty](./media/hdinsight-hadoop-r-server-get-started/putty.png)

Quando a sessão SSH estiver ativa, o tráfego da porta 12800 do computador é encaminhado para a porta 12800 do nó de extremidade através dessa sessão. Confirme que utiliza `127.0.0.1:12800` no método `remoteLogin()`. Desta forma, é iniciada sessão na operacionalização do nó de extremidade através do encaminhamento de porta.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="how-to-scale-microsoft-r-server-operationalization-compute-nodes-on-hdinsight-worker-nodes"></a>Como dimensionar nós de computação da Operacionalização do Microsoft R Server em nós de trabalho do HDInsight

### <a name="decommission-the-worker-nodes"></a>Desativar o nó ou nós de trabalho

Atualmente, o Microsoft R Server não pode ser gerido no Yarn. Se os nós de trabalho não estiverem desativados, o Yarn Resource Manager não vai funcionar conforme esperado, porque não terá conhecimento dos recursos que o servidor está a consumir. Para evitar esta situação, recomendamos a desativação dos nós de trabalho antes de aumentar horizontalmente os nós de computação.

Passos para desativar os nós de trabalho:

* Inicie sessão na consola do Ambari do cluster do HDI e clique no separador “hosts” (“anfitriões”).
* Selecione os nós de trabalho (a desativar), clique em "Actions” (“Ações") > "Selected Hosts” (“Anfitriões Selecionados") > "Hosts" (“Anfitriões”) > clique em “Turn ON Maintenance Mode” (“Ativar o Modo de Manutenção”). Por exemplo, na imagem seguinte, selecionámos a desativação de wn3 e wn4.  

   ![desativar os nós de trabalho](./media/hdinsight-hadoop-r-server-get-started/get-started-operationalization.png)  

* Selecione **Actions (Ações)** > **Selected Hosts (Anfitriões Selecionados)** > **DataNodes** > clique em **Decommission (Descontinuar)**
* Selecione **Actions (Ações)** > **Selected Hosts (Anfitriões Selecionados)** > **NodeManagers** > clique em **Decommission (Descontinuar)**
* Selecione **Actions (Ações)** > **Selected Hosts (Anfitriões Selecionados)** > **DataNodes** > clique em **Stop (Parar)**
* Selecione **Actions (Ações)** > **Selected Hosts (Anfitriões Selecionados)** > **NodeManagers** > clique em **Stop (Parar)**
* Selecione **Actions (Ações)** > **Selected Hosts (Anfitriões Selecionados)** > **Hosts (Anfitriões)** > clique em **Stop All Components (Parar Todos os Componentes)**
* Desmarque os nós de trabalho e selecione os nós principais
* Selecione **Actions (Ações)** > **Selected Hosts (Anfitriões Selecionados)** > "**Hosts (Anfitriões)** > **Restart All Components (Reiniciar Todos os Componentes)**

### <a name="configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Configurar os Nós de computação em cada nó de trabalho desativado

1. Aceda através de SSH a cada nó de trabalho desativado.
2. Utilize `dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll` para executar o utilitário de administração.
3. Introduza “1” para selecionar a opção “Configurar o R Server para Operacionalização”.
4. Introduza "c" para selecionar a opção "C. Nó de computação”. Desta forma, o nó de computação é configurado no nó de trabalho.
5. Saia do Utilitário de Administração.

### <a name="add-compute-nodes-details-on-web-node"></a>Adicionar detalhes dos nós de computação no Nó da Web

Assim que todos os nós de trabalho desativados estiverem configurados para executar o nó de computação, regresse ao nó de extremidade e adicione os endereços IP dos nós de trabalho desativados à configuração do nó da Web do Microsoft R Server:

* Aceda através de SSH ao nó de extremidade.
* Execute `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.
* Procure a secção "URIs" e adicione o IP e os detalhes da porta do nó de trabalho.

    ![linha de comandos dos nós de trabalho de desativação](./media/hdinsight-hadoop-r-server-get-started/get-started-op-cmd.png)


## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](hdinsight-administer-use-portal-linux.md#create-clusters).


## <a name="next-steps"></a>Passos seguintes

Agora, deve saber como criar um cluster do HDInsight novo que inclui o R Server, bem como as noções básicas da utilização da consola de R numa sessão SSH. Os tópicos seguintes explicam outras maneiras de gerir e trabalhar com o R Server no HDInsight:

* [Add RStudio Server to HDInsight (if not installed during cluster creation) [Adicionar o RStudio Server ao HDInsight (se não for instalado durante a criação do cluster)]](hdinsight-hadoop-r-server-install-r-studio.md)
* [Compute context options for R Server on HDInsight (Opções do contexto de cálculo para o R Server no HDInsight)](hdinsight-hadoop-r-server-compute-contexts.md)
* [Azure Storage options for R Server on HDInsight (Opções do Armazenamento do Azure para o R Server no HDInsight)](hdinsight-hadoop-r-server-storage.md)

