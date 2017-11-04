---
title: "Ferramentas de ciência de dados e plataformas equipa projetos - Azure | Microsoft Docs"
description: "Itemizes e descreve os recursos de dados e análise disponíveis para as empresas uniformizar do processo de ciência de dados de equipa."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev;
ms.openlocfilehash: 3ec2eaaf4e8d54e7b1ea3d272c47eac96451f317
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="platforms-and-tools-for-data-science-team-projects"></a>Plataformas e ferramentas para projetos de equipa de ciência de dados

A Microsoft fornece um espetro completo de dados e serviços de análise e os recursos de nuvem ou plataformas no local. Estes podem ser implementados para tornar a execução dos seus projetos de ciência de dados eficientes e dimensionáveis. Orientações para implementar projetos de ciência de dados num trackable de equipas, versão controladas e forma colaboração é fornecida pelo [o processo de ciência de dados de equipa](overview.md) (TDSP).  Para uma descrição das funções de técnicos e das respetivas tarefas associadas são processados por um dados ciência equipa uniformizar neste processo, consulte [funções do processo de ciência de dados de equipa e tarefas](roles-tasks.md).

Os serviços de dados e análise disponíveis para as equipas de ciência de dados utilizando o TDSP incluem:

- Máquinas de virtuais para ciência de dados (Windows e Linux CentOS)
- Clusters do HDInsight Spark
- SQL Data Warehouse
- Azure Data Lake
- Clusters do Hive do HDInsight
- File Storage do Azure
- Serviços do SQL Server 2016 R

Neste documento, iremos brevemente descrevem os recursos e fornecem ligações para os tutoriais e instruções as equipas TDSP tem publicado. Podem ajudar a saber como utilizá-los passo a passo e começar a utilizá-los para criar as suas aplicações inteligentes. Obter mais informações sobre estes recursos estão disponíveis no respetivas páginas de produto. 

## <a name="data-science-virtual-machine-dsvm"></a>Máquina de Virtual de ciência de dados (DSVM)

As máquinas de ciência de dados disponibilizadas no Windows e Linux pela Microsoft, contém as ferramentas populares para atividades de desenvolvimento e modelação de ciência de dados. Inclui ferramentas, tais como:

- Edição de programador do Microsoft R 
- Distribuição do Python anaconda
- Blocos de notas do Jupyter para Python e R 
- Edição de Comunidade do Visual Studio com o Python e ferramentas de R no Windows / Eclipse no Linux
- Ambiente de trabalho do Power BI para Windows
- Edição de programador do SQL Server 2016 no Windows / Postgres no Linux

Também inclui **ferramentas ML e AI** como CNTK (um toolkit Learning profunda de origem aberto da Microsoft), xgboost, mxnet e Vowpal Wabbit.

Está atualmente disponível no DSVM **Windows** e **Linux CentOS** sistemas operativos. Escolha o tamanho do seu DSVM (número de núcleos de CPU) e a quantidade de memória com base nas necessidades de projetos de ciência de dados que pretende executar na mesma. 

Para obter mais informações sobre a edição do Windows de DSVM, consulte [máquinas de virtuais de ciência de dados Microsoft](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) no Azure marketplace. Para a edição do Linux do DSVM, consulte [máquinas de virtuais de ciência de dados de Linux](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

Para saber como executar algumas das tarefas de ciência de dados comuns no DSVM forma eficiente, consulte o artigo [dez coisas que pode fazer na ciência de dados de Máquina Virtual](../data-science-virtual-machine/vm-do-ten-things.md)


## <a name="azure-hdinsight-spark-clusters"></a>Clusters do HDInsight Spark do Azure

Apache Spark é um paralelo open source processamento arquitetura que suporta o processamento dentro da memória para melhorar o desempenho de aplicações de análise de macrodados. O motor de processamento do Spark foi concebido para velocidade, facilidade de utilização e efetuar análises sofisticadas. Capacidades de computação dentro da memória do Spark tornam uma boa opção para algoritmos iterativos no machine learning e para cálculos de gráfico. O Spark também é compatível com o Blob storage do Azure (WASB), pelo que os dados existentes armazenados no Azure podem ser facilmente processados através de Spark.

Quando cria um cluster do Spark no HDInsight, está a criar recursos de computação do Azure com o Spark instalado e configurado. Demora cerca de 10 minutos para criar um cluster do Spark no HDInsight. Armazene os dados a ser processados no Blob storage do Azure. Para obter informações sobre como utilizar o Blob Storage do Azure com um cluster, consulte [utilize compatível com HDFS Blob storage do Azure com o Hadoop no HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

Equipa TDSP da Microsoft publicou duas instruções ponto a ponto sobre como utilizar o Azure HDInsight Spark Clusters para criar soluções de ciência de dados, utilizando um Python e a outro Scala. Para mais informações sobre o Azure HDInsight **Clusters do Spark**, consulte [descrição geral: Apache Spark no HDInsight Linux](../../hdinsight/spark/apache-spark-overview.md). Para saber como criar um ciência de dados solução utilizando **Python** num Cluster do Spark HDInsight do Azure, consulte [descrição geral de ciência de dados com o Spark no Azure HDInsight](spark-overview.md). Para saber como criar um ciência de dados solução utilizando **Scala** num Cluster do Spark HDInsight do Azure, consulte [ciência de dados utilizando Scala e o Spark no Azure](scala-walkthrough.md). 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

O Azure SQL Data Warehouse permite-lhe dimensionar recursos de computação em segundos e facilmente sem sobre-aprovisionar ou pagar excessiva. Também oferece a opção exclusiva para colocar em pausa a utilização de recursos de computação, o que lhe confere a liberdade para melhor gerir os custos de nuvem. A capacidade de implementar os recursos de computação escalável possibilita a colocar todos os seus dados no Azure SQL Data Warehouse. Os custos de armazenamento são mínimos e pode efetuar a computação apenas as partes de conjuntos de dados que pretende analisar. 

Para obter mais informações sobre o Azure SQL Data Warehouse, consulte o [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse) Web site. Para saber como criar soluções de análise avançadas ponto a ponto com o SQL Data Warehouse, consulte o artigo [o processo de ciência de dados de equipa em ação: utilizar o SQL Data Warehouse](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

É lake de dados do Azure como um repositório de toda a empresa de cada tipo de dados recolhidos numa única localização, antes de quaisquer requisitos formais ou o esquema que está a ser impostas. Esta flexibilidade permite que cada tipo de dados que serão guardadas no data lake, independentemente do respetivo tamanho ou a estrutura ou a rapidez é ingerido. As organizações podem, em seguida, utilizar o Hadoop ou padrões de análise avançada para localizar estes dados lagos. Lagos dados também podem servir como um repositório de preparação de dados de custo inferior antes de moderação os dados e movê-los para um armazém de dados.

Para obter mais informações sobre o Azure Data Lake, consulte [Introducing do Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Para saber como criar uma solução de ciência de dados de ponto a ponto dimensionável com o Azure Data Lake, veja [dimensionável ciência de dados no Azure Data Lake: uma instruções ponto a ponto](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Clusters do Azure ramo de registo do HDInsight (Hadoop)

Apache Hive é um sistema de armazém de dados para o Hadoop, que permite a sumarização de dados, consulta e a análise de dados utilizando o HiveQL, uma linguagem de consulta semelhante ao SQL Server. Ramo de registo pode ser utilizado para interativamente explorar os dados ou para criar as tarefas de processamento de lote reutilizável.

Ramo de registo permite-lhe a estrutura de projeto dados em grande parte não estruturados. Depois de definir a estrutura, pode utilizar o Hive para consultar os dados num cluster de Hadoop sem ter de utilizar, ou mesmo souber, Java ou MapReduce. HiveQL (o idioma de consulta do Hive) permite-lhe escrever consultas com as instruções que são semelhantes às T-SQL.

Para cientistas de dados, o ramo de registo pode executar funções Python User-Defined (UDFs) em consultas do Hive para processar registos. Esta capacidade expande consideravelmente a capacidade de consultas do Hive na análise de dados. Especificamente, permite que os cientistas de dados realizar a engenharia da funcionalidade dimensionável idiomas principalmente estiver familiarizados com: o HiveQL como o SQL Server e Python. 

Para obter mais informações sobre Clusters de ramo de registo do Azure HDInsight, consulte [utilizar o Hive e o HiveQL com o Hadoop no HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md). Para saber como construir uma solução de ciência de dados de ponto a ponto dimensionável com Clusters de ramo de registo do Azure HDInsight, consulte o artigo [o processo de ciência de dados de equipa em ação: com clusters do HDInsight Hadoop](hive-walkthrough.md).


## <a name="azure-file-storage"></a>File Storage do Azure 

File Storage do Azure é um serviço que oferece partilhas de ficheiros na nuvem utilizando o protocolo Server Message Block (SMB) padrão. O SMB 2.1 e o SMB 3.0 são suportados. Com o Armazenamento de ficheiros do Azure, pode migrar aplicações antigas que se baseiam em partilhas de ficheiros para o Azure rapidamente e sem reescritas dispendiosas. As aplicações em execução em máquinas virtuais ou serviços em nuvem do Azure ou em clientes no local podem montar uma partilha de ficheiros na nuvem, tal como uma aplicação de ambiente de trabalho monta uma partilha SMB típica. Em seguida, quaisquer componentes da aplicação podem montar e aceder à partilha do File Storage em simultâneo.

Especialmente útil para projetos de ciência de dados é a capacidade de criar um arquivo de ficheiros do Azure como o local para partilhar os dados do projeto com os membros da equipa de projeto. Em seguida, cada um deles tem acesso para a mesma cópia dos dados no file storage do Azure. Também podem utilizar este armazenamento de ficheiros para partilhar os conjuntos de funcionalidades gerados durante a execução do projeto. Se o projeto é o engagement um cliente, os clientes podem criar um file storage do Azure na sua própria subscrição do Azure para partilhar os dados de projeto e funcionalidades consigo. Desta forma, o cliente tem controlo total sobre os recursos de dados do projeto. Para obter mais informações sobre o File Storage do Azure, consulte [introdução ao File storage do Azure no Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) e [como utilizar o File Storage do Azure com o Linux](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2016-r-services"></a>Serviços do SQL Server 2016 R

Serviços de R (na base de dados) fornecem uma plataforma para desenvolver e implementar aplicações inteligentes, que podem desvendar informações de novo. Pode utilizar a linguagem de R avançada e poderosa, incluindo o número de pacotes fornecido pela Comunidade R, para criar modelos e gerar predições dos seus dados do SQL Server. Porque os serviços de R (na base de dados) integram o idioma de R com o SQL Server, análise é mantida próximo os dados, que elimina os custos e riscos de segurança associados a mover os dados.

Serviços de R (na base de dados) suporta o idioma de open source para R com um conjunto abrangente de tecnologias e ferramentas do SQL Server. Oferecem desempenho superior, segurança, fiabilidade e capacidade de gestão. Pode implementar soluções de R com ferramentas familiares e convenientes. As aplicações de produção podem chamar o tempo de execução do R e obter predições e os visuais que utilizam o Transact-SQL. Também é utilizar as bibliotecas de ScaleR para melhorar a escala e desempenho das suas soluções de R. Para obter mais informações, consulte [serviços do SQL Server R](https://msdn.microsoft.com/library/mt604845.aspx)

A equipa TDSP da Microsoft publicou duas instruções ponto-a-ponto que mostram como criar soluções de ciência de dados no SQL Server 2016 R Services: um para programadores de R e outro para programadores do SQL Server. Para **R programadores**, consulte [ciência de dados ponto-a-ponto instruções](https://msdn.microsoft.com/library/mt612857.aspx). Para **programadores SQL**, consulte [na base de dados avançadas de análise para programadores do SQL Server (Tutorial)](https://msdn.microsoft.com/library/mt683480.aspx).


## <a name="appendix"></a>Apêndice: Ferramentas definir projetos de ciência de dados

### <a name="install-git-credential-manager-on-windows"></a>Instalar o Gestor de credenciais do Git no Windows

Se estiver a seguir a TDSP **Windows**, tem de instalar o **Gestor de credenciais de Git (GCM)** para comunicar com os repositórios de Git. Para instalar o GCM, terá primeiro de instalar **Chocolaty**. Para instalar Chocolaty e o GCM, execute os seguintes comandos do Windows PowerShell como um **administrador**:  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>Instalar o Git em máquinas Linux (CentOS)

Execute o seguinte comando para instalar o Git em máquinas Linux (CentOS) bash:

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Gerar a chave SSH pública em máquinas Linux (CentOS)

Se estiver a utilizar máquinas Linux (CentOS) para executar os comandos de git, terá de adicionar a chave SSH pública da sua máquina ao seu servidor VSTS, para que esta máquina é reconhecida pelo servidor VSTS. Em primeiro lugar, tem de gerar uma chave SSH pública e adicione a chave para as chaves públicas SSH na sua página de definição de segurança VSTS. 

- Para gerar a chave SSH, execute os seguintes dois comandos: 

        ssh-keygen
        cat .ssh/id_rsa.pub

![](./media/platforms-and-tools/resources-1-generate_ssh.png)

- Copie todo o ssh incluindo chave *ssh-rsa*. 
- Inicie sessão no seu servidor VSTS. 
- Clique em **< nome do seu\>**  no canto superior direito da página e clique em **segurança**. 
    
    ![](./media/platforms-and-tools/resources-2-user-setting.png)

- Clique em **das chaves públicas SSH**e clique em **+ adicionar**. 

    ![](./media/platforms-and-tools/resources-3-add-ssh.png)

- Colar o ssh chave acabou de ser copiada para a caixa de texto e guarde.


## <a name="next-steps"></a>Passos seguintes

Total de instruções ponto-a-ponto que demonstram todos os passos no processo de **cenários específicos** também são fornecidos. São listados e ligados com descrições de miniaturas no [instruções de exemplo](walkthroughs.md) tópico. Estes mostram como combinar em nuvem, ferramentas no local e serviços para um fluxo de trabalho ou pipeline para criar uma aplicação inteligente. 

Para obter exemplos executar os passos no processo de ciência de dados de equipa que utilizam o Azure Machine Learning Studio, consulte o [com o Azure ML](http://aka.ms/datascienceprocess) percurso de aprendizagem.