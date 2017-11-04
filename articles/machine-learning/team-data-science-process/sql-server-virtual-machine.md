---
title: "Explore os dados numa máquina virtual do SQL Server no Azure | Microsoft Docs"
description: "Explore os dados e gerar funcionalidades numa máquina virtual do SQL Server no Azure"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: 
ms.assetid: 3949fb2c-ffab-49fb-908d-27d5e42f743b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: fashah;garye;bradsev
ms.openlocfilehash: 063709a22540e22d1eb6f1c6a6ff777e95f6a29a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="heading"></a>Processamento de dados na máquina Virtual do servidor SQL no Azure
Este documento aborda como explorar dados e gerar funcionalidades para os dados armazenados numa VM do SQL Server no Azure. Isto pode ser feito por dados wrangling utilizando o SQL Server ou ao utilizar uma linguagem de programação, como o Python.

> [!NOTE]
> As instruções de SQL de exemplo neste documento partem do princípio de que os dados estão no SQL Server. Se não estiver, consulte o mapa de processo do ciência de dados em nuvem para saber como mover os dados para o SQL Server.
> 
> 

## <a name="SQL"></a>Utilizar o SQL Server
Vamos descrever as seguintes tarefas wrangling dados nesta secção utilizando o SQL Server:

1. [Exploração de dados](#sql-dataexploration)
2. [Geração de funcionalidade](#sql-featuregen)

### <a name="sql-dataexploration"></a>Exploração de dados
Seguem-se alguns scripts de SQL de exemplo que podem ser utilizadas para explorar os arquivos de dados no SQL Server.

> [!NOTE]
> Para obter um exemplo de prático, pode utilizar o [NYC Taxi dataset](http://www.andresmh.com/nyctaxitrips/) e consulte IPNB intitulada [NYC dados wrangling utilizando IPython bloco de notas e o SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) para uma passagem de ponto a ponto.
> 
> 

1. Obter a contagem das observações por dia
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Obter os níveis numa coluna categórico
   
    `select  distinct <column_name> from <databasename>`
3. Obter o número de níveis numa combinação de duas colunas categórico 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Obter a distribuição para colunas numéricos
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="sql-featuregen"></a>Geração de funcionalidade
Nesta secção, iremos descrevem formas de geração de funcionalidades, utilizando o SQL Server:  

1. [Contagem baseada em funcionalidade geração](#sql-countfeature)
2. [A discretização geração de funcionalidade](#sql-binningfeature)
3. [Disponibilizando as funcionalidades de uma única coluna](#sql-featurerollout)

> [!NOTE]
> Depois de gerar funcionalidades adicionais, pode adicioná-los como colunas à tabela existente ou criar uma nova tabela com as funcionalidades adicionais e a chave primária, o que pode ser associado com a tabela original. 
> 
> 

### <a name="sql-countfeature"></a>Contagem baseada em funcionalidade geração
Os exemplos seguintes demonstram duas formas de gerar funcionalidades de contagem. O primeiro método utiliza condicional sum e o segundo método utiliza a cláusula 'where'. Estes podem, em seguida, ser associados com a tabela original (utilizando as colunas chave primárias) com funcionalidades de contagem juntamente com os dados originais.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="sql-binningfeature"></a>A discretização geração de funcionalidade
O exemplo seguinte mostra como gerar funcionalidades binned pela discretização (utilizando intervalos cinco binários), uma coluna numérica que pode ser utilizada como uma funcionalidade em vez disso:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Disponibilizando as funcionalidades de uma única coluna
Nesta secção, iremos demonstrar como implementar uma única coluna numa tabela para gerar funcionalidades adicionais. O exemplo assume que existe uma coluna de latitude ou longitude na tabela a partir do qual está a tentar gerar funcionalidades.

Eis um breve manual em dados de localização de latitude/longitude (resourced de stackoverflow [como medir a exatidão da latitude e longitude?](http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Isto é útil para entender antes featurizing do campo location:

* O início de sessão indica-nos se estamos Norte ou sul, Leste ou oeste em todo o mundo.
* Um centenas diferente de zero dígito informa-nos que estamos a utilizar longitude, não latitude!
* As dezenas dígito fornece uma posição para cerca de 1.000 kilometers. Fornece informações úteis sobre que continente ou ocean estamos no-nos.
* Os dígitos de unidades (um grau decimal) fornece uma posição até 111 kilometers (60 nautical quilómetros sobre 69 quilómetros). Se gostaria de nos dizer aproximadamente o estado de grandes dimensões ou país que estamos no.
* O primeiro local decimal é, mas até 11.1 km: poder diferenciar a posição de uma cidade grande de uma cidade grande circundantes.
* É o local de decimais segundo, mas até 1.1 km:-lo pode separar um village da seguinte.
* O terceiro local da vírgula decimal é, m até 110: possa identificar um campo agricultural grande ou institutional campus.
* O local de decimais quarto é, m até 11: possa identificar um parcel do telefone. É comparável em termos de exatidão típico de uma unidade GPS uncorrected com nenhuma interferências.
* O local de decimais quinto é, m: 1.1 até que distingue árvores uns dos outros. Precisão para este nível com comerciais unidades GPS só pode ser conseguido com a correção diferencial.
* O local de decimais sexto é, m: 0.11 até que pode utilizar esta opção para distribuir as estruturas em detalhe, para estruturar landscapes, criação roads. Deve ser mais do que um bom suficiente para movimentos de glaciers e rivers de controlo. Isto pode ser alcançado, efetuando painstaking medidas com GPS, tais como differentially corrigida GPS.

As informações de localização podem ser featurized forma, separando os região, localização e informações de cidade. Tenha em atenção que também pode ligar a um ponto final REST, tais como a API do Bing Maps disponíveis em [encontrar uma localização pelo ponto](https://msdn.microsoft.com/library/ff701710.aspx) para obter as informações de região/regional.

    select 
        <location_columnname>
        ,round(<location_columnname>,0) as l1        
        ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
        ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
        ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
        ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
        ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
        ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
    from <tablename>

Estas funcionalidades com base na localização podem ser mais utilizadas para gerar as funcionalidades de contagem adicionais conforme descrito anteriormente. 

> [!TIP]
> Pode inserir programaticamente os registos utilizando o idioma à escolha. Poderá ter de inserir os dados em partes para melhorar a eficiência de escrita (para obter um exemplo de como fazê-lo utilizando pyodbc, consulte [Olámundo um exemplo para aceder à SQLServer com o python](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Outra alternativa é inserir dados na base de dados com o [o utilitário BCP](https://msdn.microsoft.com/library/ms162802.aspx).
> 
> 

### <a name="sql-aml"></a>A ligar ao Azure Machine Learning
A funcionalidade recentemente criada pode ser adicionada como uma coluna para uma tabela existente ou armazenada numa tabela nova e associada com a tabela original para o machine learning. Funcionalidades podem ser geradas ou aceder ao mesmo se já tiver criado, utilizando o [importar dados] [ import-data] módulo no Azure Machine Learning, conforme mostrado abaixo:

![leitores de azureml][1] 

## <a name="python"></a>Utilizar uma linguagem de programação, como o Python
Com o Python para explorar dados e gerar funcionalidades quando os dados estão no SQL Server é semelhante ao processamento de dados no blob do Azure com o Python conforme documentado no [dados de Blobs do Azure de processo no seu ambiente de ciência de dados](data-blob.md). Os dados tem de ser carregados da base de dados para um intervalo de dados pandas e, em seguida, podem ser processados adicional. Iremos documento do processo de ligar à base de dados e carregar os dados para o intervalo de dados nesta secção.

O seguinte formato de cadeia de ligação pode ser utilizado para ligar a uma base de dados do SQL Server do Python com pyodbc (substitua servername, dbname, nome de utilizador e palavra-passe com os seus valores específicos):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

O [biblioteca Pandas](http://pandas.pydata.org/) Python fornece um conjunto avançado de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação do Python. O código abaixo lê os resultados devolvidos por uma base de dados do SQL Server para um intervalo de dados Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Agora, pode trabalhar com o intervalo de dados Pandas como abrangidos no artigo [dados de Blobs do Azure de processo no seu ambiente de ciência de dados](data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Ciência de dados do Azure no exemplo de ação
Para obter um exemplo de instruções ponto a ponto do processo de ciência de dados do Azure utilizando um conjunto de dados público, consulte [processo de ciência de dados do Azure em ação](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

