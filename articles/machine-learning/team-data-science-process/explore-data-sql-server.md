---
title: "Explore os dados na máquina Virtual do SQL Server no Azure | Microsoft Docs"
description: Como explorar os dados armazenados numa VM do SQL Server no Azure.
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ccbb3085-af9e-4ec2-9df2-15dcab261d05
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: bradsev
ms.openlocfilehash: d5079fc9d9a0ccd89664407fcdba9a7958e1407d
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Explorar dados numa máquina Virtual do SQL Server no Azure
Este documento aborda como explorar os dados armazenados numa VM do SQL Server no Azure. Isto pode ser feito por dados wrangling utilizando o SQL Server ou ao utilizar uma linguagem de programação, como o Python.

O seguinte **menu** ligações para tópicos que descrevem como utilizar as ferramentas para explorar dados vários ambientes de armazenamento. Esta tarefa é um passo no processo de análise do Cortana (CAP).

[!INCLUDE [cap-explore-data-selector](../../../includes/cap-explore-data-selector.md)]

> [!NOTE]
> As instruções de SQL de exemplo neste documento partem do princípio de que os dados estão no SQL Server. Se não estiver, consulte o mapa de processo do ciência de dados em nuvem para saber como mover os dados para o SQL Server.
> 
> 

## <a name="sql-dataexploration"></a>Explore os dados do SQL Server com scripts do SQL Server
Seguem-se alguns scripts de SQL de exemplo que podem ser utilizadas para explorar os arquivos de dados no SQL Server.

1. Obter a contagem das observações por dia
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Obter os níveis numa coluna categórico
   
    `select  distinct <column_name> from <databasename>`
3. Obter o número de níveis numa combinação de duas colunas categórico 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Obter a distribuição para colunas numéricos
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> Para obter um exemplo de prático, pode utilizar o [NYC Taxi dataset](http://www.andresmh.com/nyctaxitrips/) e consulte IPNB intitulada [NYC dados wrangling utilizando IPython bloco de notas e o SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) para uma passagem de ponto a ponto.
> 
> 

## <a name="python"></a>Explore os dados do SQL Server com o Python
Com o Python para explorar dados e gerar funcionalidades quando os dados estão no SQL Server é semelhante ao processamento de dados no blob do Azure com o Python, conforme documentado no [dados de Blobs do Azure de processo no seu ambiente de ciência de dados](data-blob.md). Os dados tem de ser carregados da base de dados para um pandas DataFrame e, em seguida, podem ser processados adicional. Iremos documento do processo de ligar à base de dados e carregar os dados para o DataFrame nesta secção.

O seguinte formato de cadeia de ligação pode ser utilizado para ligar a uma base de dados do SQL Server do Python com pyodbc (substitua servername, dbname, nome de utilizador e palavra-passe com os seus valores específicos):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

O [biblioteca Pandas](http://pandas.pydata.org/) Python fornece um conjunto avançado de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação do Python. O seguinte código lê os resultados devolvidos por uma base de dados do SQL Server para um intervalo de dados Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Agora, pode trabalhar com o DataFrame Pandas como descritas na íntegra no tópico [dados de Blobs do Azure de processo no seu ambiente de ciência de dados](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>O processo de ciência de dados de equipa no exemplo de ação
Para obter um exemplo de instruções ponto a ponto do processo de análise de Cortana utilizando um conjunto de dados público, consulte [o processo de ciência de dados de equipa em ação: utilizar o SQL Server](sql-walkthrough.md).

