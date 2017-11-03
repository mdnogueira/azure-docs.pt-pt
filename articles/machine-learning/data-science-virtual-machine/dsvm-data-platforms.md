---
title: "Plataformas de dados para dados de ciência de Máquina Virtual - Azure | Microsoft Docs"
description: "Plataformas de dados para a máquina de Virtual de ciência de dados."
keywords: "ferramentas de ciência de dados, a máquina de virtual de ciência de dados, as ferramentas de ciência de dados, ciência de dados do linux"
services: machine-learning
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
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 9b8beb51c555c6125fa3b0abbad892d627a180b9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="data-platforms"></a>Plataformas de dados

A Máquina Virtual de ciência de dados (DSVM) permite-lhe criar a sua análise contra uma ampla gama de plataformas de dados. Para além das interfaces a plataforma de dados remota, o DSVM fornece uma instância local para um desenvolvimento rápido e fazer o protótipo. 

Seguem-se as ferramentas de plataforma de dados suportadas no DSVM. 

## <a name="sql-server-2016-developer-edition"></a>Edição do SQL Server 2016 para programadores

| | |
| ------------- | ------------- |
| O que é?   | Uma instância de base de dados relacional local      |
| Edições de DSVM suportados      | Windows      |
| Utilizações comuns      | Desenvolvimento rápido localmente com mais pequeno conjunto de dados <br/> Executar R na base de dados   |
| Ligações para amostras      |    Um exemplo de conjunto de dados de Nova Iorque cidade pequeno é carregado para a base de dados do SQL Server `nyctaxi`. <br/> Exemplo do Jupyter com Microsoft R e análise de na base de dados pode ser encontrado em:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Ferramentas relacionadas no DSVM       | SQL Server Management Studio <br/> Controladores ODBC/JDBC<br/> pyodbc, RODBC<br />Apache desagregação      |

> [!NOTE]
> A edição do SQL Server 2016 programador só pode ser utilizada para desenvolvimento e fins de teste. Precisa de uma licença ou um das VMs de SQL Server para executá-la na produção. 


### <a name="setup"></a>Configurar

O servidor de base de dados já está configurado previamente e relacionadas com os serviços do Windows para o SQL Server (como `SQL Server (MSSQLSERVER)`) estão definidos para serem executados automaticamente. O passo manual apenas para ser executado é para ativar a análise na base de dados com o Microsoft R. Pode fazê-lo executando o seguinte comando, como uma uma hora ação no SQL Server Management Studio (SSMS) depois de iniciar sessão na qualidade de administrador do computador, abra "Nova consulta" no SSMS, certifique-se de que a base de dados selecionada `master` e, em seguida, execute: 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)
       
Para executar o SQL Server Management Studio, pode procurar "SQL Server Management Studio" na lista de programa ou utilizar a procura do Windows para localizar e executá-la. Quando lhe forem pedidas as credenciais, escolha "Autenticação do Windows" e utilize o nome do computador ou ```localhost``` no nome do SQL Server. 

### <a name="how-to-use--run-it"></a>Como utilizar / executá-lo?  

O servidor de base de dados com a instância de base de dados predefinida está em execução automaticamente por predefinição. Pode utilizar ferramentas como o SQL Server Management Studio na VM para aceder a base de dados do SQL Server localmente. Conta local de administradores tem acesso de administrador na base de dados. 

Também o DSVM vem com controladores de controladores ODBC e JDBC para falar com SQL Server, bases de dados SQL do Azure e o Azure SQL Data Warehouse a partir de aplicações escritas em vários idiomas, incluindo o Python, R. 

### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Como é mesmo configurado / instalado o DSVM? 

O SQL Server está instalado o modo padrão. Podem ser encontrado em `C:\Program Files\Microsoft SQL Server`. A instância de base de dados em R encontra-se em `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. O DSVM também tem uma instância de servidor R autónomo separado instalada no `C:\Program Files\Microsoft\R Server\R_SERVER`. Estas instâncias de dois-R não partilham as bibliotecas.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2 (autónomo)

| | |
| ------------- | ------------- |
| O que é?   | Uma instância autónoma (único nó no processo) de plataforma do Apache Spark popular, um sistema de processamento de dados rápido em grande escala e a aprendizagem     |
| Edições de DSVM suportados      | Linux <br /> Windows (Experimental)      |
| Utilizações comuns      | * Rápido desenvolvimento de aplicações do Spark/PySpark localmente com o conjunto de dados mais pequeno e mais tarde implementá-la em grandes clusters do Spark, tais como o Azure HDInsight<br/> * Microsoft R Server Spark contexto de teste <br />* Utilizar SparkML ou da Microsoft open source para [MMLSpark](https://github.com/Azure/mmlspark) biblioteca para criar aplicações de ML  |
| Ligações para amostras      |    Exemplo de Jupyter: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> O servidor de Microsoft R (contexto do Spark): /dsvm/samples/MRS/MRSSparkContextSample.R |
| Ferramentas relacionadas no DSVM       | PySpark, Scala<br/>Jupyter (Spark/PySpark Kernels)<br/>Sparklyr de servidor, SparkR, Microsoft R <br />Apache desagregação      |

### <a name="how-to-use-it"></a>Como utilizá-la
É possível executar o Spark ao submeter as tarefas do Spark na linha de comandos com `spark-submit` ou `pyspark` comandos. Também pode criar um bloco de notas do Jupyter ao criar um novo bloco de notas com o kernel do Spark. 

Pode utilizar o Spark do R utilizando bibliotecas, como SparkR, Sparklyr ou Microsoft R Server que estão disponíveis no DSVM. Consulte ponteiros para exemplos na tabela anterior. 

> [!NOTE]
> Com o Microsoft R Server no contexto de Spark do DSVM só é suportada na edição Ubuntu Linux DSVM. 



### <a name="setup"></a>Configurar
Antes de executar no contexto do Spark no Microsoft R Server na edição Ubuntu Linux DSVM, precisa de executar uma uma vez que o passo de configuração para ativar um único nó HDFS do Hadoop e o Yarn instância local. Por predefinição, serviços do Hadoop são instalados, mas o DSVM desativados. Para ativá-la, terá de executar os comandos seguintes como raiz na primeira vez:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Pode parar o Hadoop relacionados com serviços quando não precisar deles executando ````systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```` uma amostra que demonstra como desenvolver e testar MRS no contexto de Spark remoto (que é a instância de Spark autónomo no DSVM) é fornecida e disponível na `/dsvm/samples/MRS` diretório. 


### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Como é mesmo configurado / instalado o DSVM? 
|Plataforma|Instalar localização ($SPARK_HOME)|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/Tools/spark-X.X.X-bin-hadoopX.X|


Bibliotecas para aceder a dados de Blobs do Azure ou armazenamento do Azure Data Lake (ADLS) e utilização de bibliotecas de aprendizagem MMLSpark da Microsoft são pré-instalado na $SPARK_HOME/v7. Estes v7 é carregados automaticamente quando é iniciado Spark. Por predefinição o Spark utiliza os dados no disco local. Para a instância de Spark DSVM para aceder a dados armazenados no blob do Azure ou ADLS é necessário criar/configurar a `core-site.xml` ficheiro baseado num modelo, foi encontrado no $SPARK_HOME/conf/core-site.xml.template (em que existem marcadores de posição de Blob e ADLS configurações) com as credenciais corretas ao blob do Azure e o armazenamento do Azure Data Lake. Encontrará mais passos sobre como criar as credenciais do serviço ADLS [aqui](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory#create-an-active-directory-application). Depois das credenciais de Blobs do Azure ou ADLS são introduzidas no ficheiro Core-site.XML, pode referenciar os dados armazenados dessas origens com o prefixo URI wasb: / / ou adl: / /. 

