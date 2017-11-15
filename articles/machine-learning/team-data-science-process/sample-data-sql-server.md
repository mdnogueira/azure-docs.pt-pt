---
title: Exemplo de dados no SQL Server no Azure | Microsoft Docs
description: Dados de exemplo no SQL Server no Azure
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgeonlun
editor: cgronlun
ms.assetid: 33c030d4-5cca-4cc9-99d7-2bd13a3926af
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: fashah;garye;bradsev
ms.openlocfilehash: fd669f3951b1f7f05932634f039a04e02993399f
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="heading"></a>Dados de exemplo no SQL Server no Azure
Este artigo mostra como os dados armazenados no SQL Server no Azure de exemplo através do SQL Server ou a linguagem de programação do Python. Também mostra como mover amostras de dados no Azure Machine Learning ao guardá-lo para um ficheiro, carregá-lo para um blob do Azure e, em seguida, ao ler para o Azure Machine Learning Studio.

A amostragem de Python utiliza o [pyodbc](https://code.google.com/p/pyodbc/) biblioteca ODBC para ligar ao SQL Server no Azure e o [Pandas](http://pandas.pydata.org/) biblioteca para a amostragem.

> [!NOTE]
> O código de SQL de exemplo neste documento assume que os dados no SQL Server no Azure. Se não estiver, consulte [mover dados para o SQL Server no Azure](move-sql-server-virtual-machine.md) artigo para obter instruções sobre como mover os dados para o SQL Server no Azure.
> 
> 

O seguinte **menu** ligações para artigos que descrevem como dados de vários ambientes de armazenamento de exemplo. 

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

**Os dados de exemplo por que motivo?**
Se o conjunto de dados que pretende analisar for grande, normalmente, é uma boa ideia baixo-sample os dados para reduzir para um tamanho mais pequeno, mas representativo e mais fácil gerir. Isto facilita a compreensão de dados, exploração e engenharia da funcionalidade. A função no [processo de ciência de dados de equipa (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) é permitir o rápido fazer o protótipo das funções de processamento de dados e modelos de machine learning.

Esta tarefa de amostragem é um passo de [processo de ciência de dados de equipa (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="SQL"></a>Utilizar o SQL Server
Esta secção descreve vários métodos com o SQL Server para executar a amostragem aleatória simple contra os dados na base de dados. Escolha um método com base no seu tamanho de dados e a distribuição da mesma.

Os dois itens seguintes mostram como utilizar `newid` no SQL Server para executar a amostragem. O método que escolher depende aleatórias como pretende amostra a ser (pk_id no seguinte código de exemplo é pressupõe-se que uma chave primária gerado automaticamente).

1. Amostra aleatória menos strict
   
        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())
2. Amostra aleatória mais 
   
        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Tablesample pode ser utilizado para os dados, bem como de amostragem. Isto pode ser uma abordagem de melhor se o tamanho de dados for grande (assumindo que não estão correlacionados dados em diferentes páginas) e para a consulta estar concluída num tempo razoável.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

> [!NOTE]
> Pode explorar e gerar funcionalidades a partir destes amostras de dados armazenando-os numa nova tabela
> 
> 

### <a name="sql-aml"></a>A ligar ao Azure Machine Learning
Pode utilizar as consultas de exemplo acima diretamente no Azure Machine Learning [importar dados] [ import-data] módulo para os dados no momento de exemplo para baixo e colocá-lo para uma experimentação do Azure Machine Learning. Uma captura de ecrã da utilização do módulo leitor para ler os dados da amostras é mostrada aqui:

![leitor sql][1]

## <a name="python"></a>Utilizando a linguagem de programação de Python
Esta secção demonstra utilizando o [pyodbc biblioteca](https://code.google.com/p/pyodbc/) para estabelecer um ODBC ligar a uma base de dados do SQL server no Python. A cadeia de ligação de base de dados é o seguinte: (substitua servername, dbname, nome de utilizador e palavra-passe com a sua configuração):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

O [Pandas](http://pandas.pydata.org/) biblioteca no Python fornece um conjunto avançado de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação do Python. O seguinte código lê um exemplo de 0.1% dos dados de uma tabela na base de dados SQL do Azure para dados de Pandas:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

Agora, pode trabalhar com os dados na moldura de dados de Pandas amostras. 

### <a name="python-aml"></a>A ligar ao Azure Machine Learning
Pode utilizar o seguinte código de exemplo para guardar os dados de objeto de amostragem para baixo para um ficheiro e carregá-la para um blob do Azure. Os dados no blob podem ler diretamente para uma experimentação do Azure Machine Learning utilizando o [importar dados] [ import-data] módulo. Os passos são os seguintes: 

1. Escrever o intervalo de dados pandas num ficheiro local
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Carregar ficheiro local para o blob do Azure
   
        from azure.storage import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
        try:
   
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
        except:            
            print ("Something went wrong with uploading blob:"+BLOBNAME)
3. Ler dados de Blobs do Azure utilizando o Azure Machine Learning [importar dados] [ import-data] módulo, conforme mostrado no ecrã o seguinte, copie:

![blob de leitor][2]

## <a name="the-team-data-science-process-in-action-example"></a>O processo de ciência de dados de equipa no exemplo de ação
Para instruções um utilizando um conjunto de dados público, veja um exemplo do processo de ciência de dados de equipa [processo de ciência de dados de equipa em ação: utilizar o SQL Server](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
