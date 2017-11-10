---
title: Criar as tabelas do Hive e carregar dados do Blob Storage do Azure | Microsoft Docs
description: Criar as tabelas do Hive e carregar os dados no blob para tabelas de ramo de registo
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: cff9280d-18ce-4b66-a54f-19f358d1ad90
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: 6d9df88d6047fbe674c216dacc6fa01bad8451ec
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Criar as tabelas do Hive e carregar dados do Blob Storage do Azure
Este tópico apresenta genéricas consultas do Hive que criar tabelas do Hive e carregar dados do blob storage do Azure. Algumas orientações também são fornecidas na criação de partições de tabelas do Hive e sobre como utilizar o otimizada linha Columnar (ORC) formatação para melhorar o desempenho de consulta.

Isto **menu** ligações para tópicos que descrevem como a ingestão de dados em ambientes de destino onde os dados podem ser armazenados e processados durante o processo de ciência do equipa dados (TDSP).

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que tem:

* Criar uma conta de armazenamento do Azure. Se precisar de instruções, consulte [contas do storage do Azure sobre](../../storage/common/storage-create-storage-account.md).
* Aprovisionar um cluster de Hadoop personalizado com o serviço de HDInsight.  Se precisar de instruções, consulte [personalizar o Azure HDInsight Hadoop clusters para análise avançada](customize-hadoop-cluster.md).
* Acesso remoto ativado para o cluster, tem sessão iniciada e abrir a consola da linha de comandos do Hadoop. Se precisar de instruções, consulte [aceder a Head nó de Cluster do Hadoop](customize-hadoop-cluster.md).

## <a name="upload-data-to-azure-blob-storage"></a>Carregar dados para armazenamento de Blobs do Azure
Se criar uma máquina virtual do Azure ao seguir as instruções fornecidas [configurar uma máquina virtual do Azure para análise avançada](../data-science-virtual-machine/setup-virtual-machine.md), este ficheiro de script deve ter sido transferido para o *c:\\utilizadores \\ \<nome de utilizador\>\\documentos\\Scripts de ciência de dados* diretório na máquina virtual. Estas consultas do Hive apenas requerem que ligue no seu próprio esquema de dados e a configuração de armazenamento de Blobs do Azure no campos apropriados para estar preparado para submissão.

Iremos partem do princípio de que os dados para as tabelas do Hive estão a ser um **descomprimidos** formato tabular, e que os dados terem sido carregados para a predefinição (ou mais) contentor da conta de armazenamento utilizada pelo cluster de Hadoop.

Se pretender prática no **NYC Taxi viagem dados**, tem de:

* **Transferir** a 24 [NYC Taxi viagem dados](http://www.andresmh.com/nyctaxitrips) ficheiros (12 viagem e 12 Fare ficheiros),
* **deszipe** todos os ficheiros para os ficheiros. csv e, em seguida,
* **carregar** -las para a predefinição (ou o contentor adequado) da conta do storage do Azure que foi criada com o procedimento descrito no [personalizar o Azure HDInsight Hadoop clusters para o processo de análise avançada e tecnologia](customize-hadoop-cluster.md)tópico. O processo para carregar os ficheiros. csv para o contentor predefinido na conta de armazenamento pode ser encontrado no [página](hive-walkthrough.md#upload).

## <a name="submit"></a>Como submeter consultas do Hive
Consultas do Hive podem ser submetidas utilizando:

1. [Submeter consultas do Hive através da linha de comandos do Hadoop no headnode do cluster de Hadoop](#headnode)
2. [Submeter consultas do Hive com o Editor do Hive](#hive-editor)
3. [Submeter consultas do Hive com comandos do PowerShell do Azure](#ps)

Consultas do Hive são como o SQL Server. Se estiver familiarizado com o SQL Server, pode encontrar o [ramo de registo para a folha de Cheat utilizadores do SQL Server](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) útil.

Ao submeter uma consulta do Hive, também pode controlar o destino da saída de consultas do Hive, se possível, no ecrã para um ficheiro local no nó principal ou para um blob do Azure.

### <a name="headnode"></a> 1. Submeter consultas do Hive através da linha de comandos do Hadoop no headnode do cluster de Hadoop
Se a consulta do Hive é complexa, submetê-lo diretamente no nó principal do Hadoop cluster normalmente leva a ativar mais rapidamente em torno de submetê-lo com scripts de Editor do Hive ou do Azure PowerShell.

Inicie sessão no nó principal do cluster de Hadoop, abra a linha de comandos do Hadoop no ambiente de trabalho do nó principal e introduza o comando `cd %hive_home%\bin`.

Tem três formas para submeter consultas do Hive da linha de comandos do Hadoop:

* diretamente
* utilizar .hql ficheiros
* com a consola de comandos do Hive

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Submeta consultas do Hive diretamente no Hadoop linha de comandos.
Pode executar o comando como `hive -e "<your hive query>;` para submeter consultas do Hive simples diretamente no Hadoop linha de comandos. Eis um exemplo, em que a caixa vermelha descreve o comando que envia a consulta do Hive e a caixa de verde descreve o resultado da consulta do Hive.

![Criar a área de trabalho](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Submeter consultas do Hive nos ficheiros de .hql
Quando a consulta do Hive é mais complicada e tem várias linhas, a edição de consultas na linha de comandos ou consola de comandos do ramo de registo não é prática. Uma alternativa é utilizar um editor de texto no nó principal do cluster de Hadoop para guardar as consultas do Hive num ficheiro .hql num diretório local do nó principal. Em seguida, pode ser submetida a consulta do Hive no ficheiro .hql utilizando o `-f` argumento da seguinte forma:

    hive -f "<path to the .hql file>"

![Criar a área de trabalho](./media/move-hive-tables/run-hive-queries-3.png)

**Suprimir a impressão de ecrã de estado de progresso de consultas do Hive**

Por predefinição, depois de consulta do Hive é submetida numa linha de comandos do Hadoop, o progresso da tarefa de mapa/reduza está impresso no ecrã. Para suprimir a impressão de ecrã de progresso da tarefa de mapa/reduza, pode utilizar um argumento `-S` ("S" em maiúsculas) no comando de linha da seguinte forma:

    hive -S -f "<path to the .hql file>"
.    ramo de registo -S -i "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Submeta consultas do Hive na consola de comandos do ramo de registo.
Pode introduzir também primeiro a consola de comandos do ramo de registo ao executar o comando `hive` na linha de comandos do Hadoop e, em seguida, submeter consultas do Hive na consola de comandos do ramo de registo. Eis um exemplo. Neste exemplo, as duas caixas vermelhos Realce os comandos utilizados para introduzir a consola de comandos do ramo de registo e a consulta do Hive submeteu na consola de comandos do ramo de registo, respetivamente. A caixa de verde realça o resultado da consulta do Hive.

![Criar a área de trabalho](./media/move-hive-tables/run-hive-queries-2.png)

Os exemplos anteriores diretamente os resultados da consulta do Hive no ecrã de saída. Também pode escrever a saída para um ficheiro local, no nó principal ou para um blob do Azure. Em seguida, pode utilizar outras ferramentas para analisar ainda mais a saída de consultas do Hive.

**Exporta os resultados da consulta do Hive num ficheiro local.**
Para exportar os resultados da consulta do Hive para um diretório local no nó principal, tem de submeter a consulta do Hive da linha de comandos do Hadoop da seguinte forma:

    hive -e "<hive query>" > <local path in the head node>

No exemplo seguinte, o resultado da consulta do Hive é escrito num ficheiro `hivequeryoutput.txt` no diretório `C:\apps\temp`.

![Criar a área de trabalho](./media/move-hive-tables/output-hive-results-1.png)

**Resultados de consulta do Hive de saída para um blob do Azure**

Também pode apresentar os resultados da consulta do Hive para um blob do Azure, no contentor predefinido do cluster de Hadoop. A consulta do Hive para este é o seguinte:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

No exemplo seguinte, o resultado da consulta do Hive é escrito para um diretório de blob `queryoutputdir` no contentor predefinido do cluster de Hadoop. Aqui, basta indicar o nome de diretório, sem o nome do blob. Emitido um erro se fornecer nomes de diretório e BLOBs, tais como `wasb:///queryoutputdir/queryoutput.txt`.

![Criar a área de trabalho](./media/move-hive-tables/output-hive-results-2.png)

Se abrir o contentor predefinido do cluster de Hadoop através do Explorador de armazenamento do Azure, pode ver o resultado da consulta do Hive, conforme mostrado na figura seguinte. Pode aplicar o filtro (realçado por caixa vermelha) apenas obter o blob com especificado letras nos nomes.

![Criar a área de trabalho](./media/move-hive-tables/output-hive-results-3.png)

### <a name="hive-editor"></a> 2. Submeter consultas do Hive com o Editor do Hive
Também pode utilizar a consola de consulta (Editor do Hive) ao introduzir um URL do formulário *https://&#60; Nome do cluster de Hadoop >.azurehdinsight.net/Home/HiveEditor* num browser. Tem de ser registadas na ver esta consola e, por isso, terá das credenciais de cluster do Hadoop.

### <a name="ps"></a> 3. Submeter consultas do Hive com comandos do PowerShell do Azure
Também pode utilizar o PowerShell para submeter consultas do Hive. Para obter instruções, consulte [tarefas submeter o Hive com o PowerShell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-tables"></a>Criar tabelas e a base de dados do Hive
As consultas do Hive são partilhadas no [repositório do GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) , podendo ser transferido a partir daí.

Segue-se a consulta de Hive que cria uma tabela do Hive.

    create database if not exists <database name>;
    CREATE EXTERNAL TABLE if not exists <database name>.<table name>
    (
        field1 string,
        field2 int,
        field3 float,
        field4 double,
        ...,
        fieldN string
    )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
    STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Seguem-se as descrições dos campos que precisa de plug-in e outras configurações:

* **&#60; nome de base de dados >**: o nome da base de dados que pretende criar. Se pretender utilizar a predefinição da base de dados, a consulta *criar base de dados...*  pode ser omitido.
* **&#60; nome da tabela >**: o nome da tabela que pretende criar na base de dados especificado. Se pretender utilizar a base de dados predefinido, a tabela pode ser diretamente referida pela *&#60; nome da tabela >* sem &#60; nome de base de dados >.
* **&#60; o separador de campo >**: o separador delimits campos no ficheiro de dados para ser carregado para a tabela do Hive.
* **&#60; o separador de linha >**: o separador delimits linhas no ficheiro de dados.
* **&#60; localização de armazenamento >**: a localização de armazenamento do Azure para guardar os dados de tabelas do Hive. Se não especificar *localização &#60; localização de armazenamento >*, a base de dados e tabelas são armazenadas no *hive/Armazém/* diretório no contentor predefinido do cluster do ramo de registo por predefinição. Se pretender especificar a localização de armazenamento, a localização de armazenamento tem de estar dentro do contentor predefinido para a base de dados e tabelas. Esta localização tem de ser referido como localização relativo para o contentor predefinido do cluster no formato de *' wasb: / / / &#60; diretório 1 > /'* ou *' wasb: / / / &#60; diretório 1 > / &#60; diretório 2 > /'*, etc. Depois da consulta é executada, são criados os diretórios relativos no contentor predefinido.
* **TBLPROPERTIES("Skip.Header.line.Count"="1")**: se o ficheiro de dados tem uma linha de cabeçalho, terá de adicionar esta propriedade **no final** do *criar tabela* consulta. Caso contrário, a linha de cabeçalho foi carregada como um registo à tabela. Se o ficheiro de dados não tiver uma linha de cabeçalho, esta configuração pode ser omitida na consulta.

## <a name="load-data"></a>Carregar dados para as tabelas do Hive
Segue-se a consulta de Hive que carrega dados para uma tabela do Hive.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* **&#60; o caminho para os dados de blob >**: se o ficheiro de BLOBs para ser carregado para a tabela do Hive no contentor predefinido do cluster do HDInsight Hadoop, o *&#60; o caminho para os dados de blob >* deve estar no formato *' wasb: / / / &#60; diretório neste contentor > / &#60; nome de ficheiro do blob >'*. O ficheiro de blob também pode ter um contentor adicional do cluster do HDInsight Hadoop. Neste caso, *&#60; o caminho para os dados de blob >* deve estar no formato *' wasb: / / &#60; nome do contentor > @&#60; nome da conta de armazenamento >.blob.core.windows.net/ &#60; nome de ficheiro do blob >'*.

  > [!NOTE]
  > Os dados de BLOBs para ser carregado para a tabela do Hive tem de ser a predefinição ou contentor adicional da conta do storage para o cluster de Hadoop. Caso contrário, o *carga dados* consulta falha complaining que não pode aceder os dados.
  >
  >

## <a name="partition-orc"></a>Tópicos de avançadas: particionar a tabela e o arquivo de dados do Hive no formato ORC
Se os dados forem grandes, a tabela de criação de partições é vantajoso para consultas que apenas necessitam de analisar algumas partições da tabela. Por exemplo, é razoável particionar os dados de registo de um web site por datas.

Para além de criação de partições as tabelas do Hive, também é vantajoso armazenar os dados do Hive no formato otimizada linha Columnar (ORC). Para obter mais informações sobre ORC formatação, consulte <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">ficheiros ORC utilizando melhora o desempenho ao ramo de registo é ler, escrever e processar dados</a>.

### <a name="partitioned-table"></a>Tabela particionada
Segue-se a consulta de Hive que cria uma tabela particionada e carrega dados para a mesma.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Ao consultar tabelas particionadas, recomenda-se para adicionar a condição de partição no **início** do `where` cláusula deste melhora efficacy de procura significativamente.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Armazenar dados do Hive no formato ORC
Diretamente não é possível carregar os dados a partir do blob storage para as tabelas do Hive, que é armazenada no formato ORC. Eis os passos que o precisa de tomar para carregar dados do Azure de blobs para as tabelas do Hive armazenadas num formato ORC.

Criar uma tabela externa **ARMAZENADOS TEXTFILE de AS** e carregar dados do armazenamento de BLOBs para a tabela.

        CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
            lines terminated by '<line separator>' STORED AS TEXTFILE
            LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

        LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

Criar uma tabela interna com o mesmo esquema como da tabela externa no passo 1, com o mesmo delimitador de campos e armazenar os dados do Hive no formato de ORC.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Selecione os dados da tabela externa no passo 1 e inserir na tabela ORC

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> Se a tabela TEXTFILE *&#60; nome de base de dados >. &#60; nome da tabela externa textfile >* tem partições, no passo 3, o `SELECT * FROM <database name>.<external textfile table name>` comando seleciona a variável de partição como um campo no conjunto de dados devolvido. A inserir no *&#60; nome de base de dados >. &#60; nome da tabela ORC >* falhar desde *&#60; nome de base de dados >. &#60; nome da tabela ORC >* não tem a variável de partição como um campo o esquema de tabela. Neste caso, tem de selecionar especificamente os campos a inserir a *&#60; nome de base de dados >. &#60; nome da tabela ORC >* da seguinte forma:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

É seguro remover o *&#60; nome da tabela externa textfile >* quando utilizar a seguinte consulta depois de todos os dados foi inserida na *&#60; nome de base de dados >. &#60; nome da tabela ORC >*:

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Depois de seguir este procedimento, deve ter uma tabela com dados no formato ORC pronto a utilizar.  
