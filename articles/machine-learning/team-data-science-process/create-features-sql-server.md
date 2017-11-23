---
title: Criar as funcionalidades de dados no SQL Server com o SQL e Python | Microsoft Docs
description: Processamento de dados do SQL Azure
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: 
ms.assetid: bf1f4a6c-7711-4456-beb7-35fdccd46a44
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: bradsev;fashah;garye
ms.openlocfilehash: dd919e7f87080b8c4ad1f8d3de26d6f71470a264
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Criar características para dados no SQL Server com SQL e Python
Este documento mostra como gerar funcionalidades para os dados armazenados numa VM do SQL Server no Azure que ajudam a algoritmos Saiba de forma mais eficiente a partir dos dados. Pode utilizar o SQL Server ou uma linguagem de programação, como o Python para realizar esta tarefa. Ambas as abordagens são demonstradas aqui.

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

Isto **menu** ligações para tópicos que descrevem como criar funcionalidades para os dados em vários ambientes. Esta tarefa é um passo de [processo de ciência de dados de equipa (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

> [!NOTE]
> Para obter um exemplo de prático, pode consultar o [NYC Taxi dataset](http://www.andresmh.com/nyctaxitrips/) e consulte IPNB intitulada [NYC dados wrangling utilizando IPython bloco de notas e o SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) para uma passagem de ponto a ponto.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que tem:

* Criar uma conta de armazenamento do Azure. Se precisar de instruções, consulte [criar uma conta de armazenamento do Azure](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* Armazenados os dados no SQL Server. Se não tiver, consulte [mover dados para uma base de dados do SQL do Azure para o Azure Machine Learning](move-sql-azure.md) para obter instruções sobre como mover os dados não existe.

## <a name="sql-featuregen"></a>Geração de funcionalidade com o SQL Server
Nesta secção, iremos descrevem formas de geração de funcionalidades, utilizando o SQL Server:  

1. [Contagem baseada em funcionalidade geração](#sql-countfeature)
2. [A discretização geração de funcionalidade](#sql-binningfeature)
3. [Disponibilizando as funcionalidades de uma única coluna](#sql-featurerollout)

> [!NOTE]
> Depois de gerar funcionalidades adicionais, pode adicioná-los como colunas à tabela existente ou criar uma nova tabela com as funcionalidades adicionais e a chave primária, o que pode ser associado com a tabela original.
> 
> 

### <a name="sql-countfeature"></a>Contagem de geração de baseada em funcionalidade
Este documento demonstra duas formas de gerar funcionalidades de contagem. O primeiro método utiliza condicional sum e o segundo método utiliza a cláusula 'where'. Estes podem, em seguida, ser associados com a tabela original (utilizando as colunas chave primárias) com funcionalidades de contagem juntamente com os dados originais.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="sql-binningfeature"></a>A discretização geração de funcionalidade
O exemplo seguinte mostra como gerar funcionalidades binned pela discretização (utilizando 5 intervalos binários), uma coluna numérica que pode ser utilizada como uma funcionalidade em vez disso:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Disponibilizando as funcionalidades de uma única coluna
Nesta secção, iremos demonstrar como implementar uma única coluna numa tabela para gerar funcionalidades adicionais. O exemplo assume que existe uma coluna de latitude ou longitude na tabela a partir do qual está a tentar gerar funcionalidades.

Eis um breve manual em dados de localização de latitude/longitude (resourced de stackoverflow `http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Eis algumas coisas útil compreender sobre dados de localização antes de criar as funcionalidades do campo:

* O início de sessão indica se estamos Norte ou sul, Leste ou oeste em todo o mundo.
* Um centenas diferente de zero dígito indica longitude, latitude não está a ser utilizado.
* As dezenas dígito fornece uma posição para cerca de 1.000 kilometers. Fornece informações úteis sobre que continente ou ocean estamos no.
* Os dígitos de unidades (um grau decimal) fornece uma posição até 111 kilometers (60 nautical quilómetros sobre 69 quilómetros). Indica, aproximadamente, que o estado de grandes dimensões ou país estamos no.
* O primeiro local decimal é, mas até 11.1 km: poder diferenciar a posição de uma cidade grande de uma cidade grande circundantes.
* É o local de decimais segundo, mas até 1.1 km:-lo pode separar um village da seguinte.
* O terceiro local da vírgula decimal é, m até 110: possa identificar um campo agricultural grande ou institutional campus.
* O local de decimais quarto é, m até 11: possa identificar um parcel do telefone. É comparável em termos de exatidão típico de uma unidade GPS uncorrected com nenhuma interferências.
* O local de decimais quinto é, m: 1.1 até que distingue árvores uns dos outros. Precisão para este nível com comerciais unidades GPS só pode ser conseguido com a correção diferencial.
* O local de decimais sexto é, m: 0.11 até que pode utilizar esta opção para distribuir as estruturas em detalhe, para estruturar landscapes, criação roads. Deve ser mais do que um bom suficiente para movimentos de glaciers e rivers de controlo. Isto pode ser alcançado, efetuando painstaking medidas com GPS, tais como differentially corrigida GPS.

As informações de localização podem ser featurized, separando os região, localização e informações de cidade. Tenha em atenção que uma vez também pode chamar um ponto final REST, tais como a API do Bing Maps disponíveis em `https://msdn.microsoft.com/library/ff701710.aspx` para obter as informações de região/regional.

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

Estas funcionalidades de localização com base podem ser mais utilizadas para gerar funcionalidades contagem adicionais, conforme descrito anteriormente.

> [!TIP]
> Pode inserir programaticamente os registos utilizando o idioma à escolha. Poderá ter de inserir os dados em partes para melhorar a eficiência de escrita. [Eis um exemplo de como fazê-lo utilizando pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Outra alternativa é inserir dados na base de dados com [o utilitário BCP](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="sql-aml"></a>A ligar ao Azure Machine Learning
A funcionalidade recentemente criada pode ser adicionada como uma coluna para uma tabela existente ou armazenada numa tabela nova e associada com a tabela original para o machine learning. Funcionalidades podem ser geradas ou aceder ao mesmo se já tiver criado, utilizando o [importar dados](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) módulo no Azure ML conforme mostrado abaixo:

![Leitores do Azure ML](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="python"></a>Utilizar uma linguagem de programação, como o Python
Com o Python para gerar funcionalidades quando os dados estão no SQL Server é semelhante ao processamento de dados no blob do Azure com o Python. Para comparação, consulte [dados de Blobs do Azure de processo no seu ambiente de ciência de dados](data-blob.md). Carregar os dados da base de dados para um intervalo de dados pandas processá-la ainda mais. O processo de ligar à base de dados e carregar os dados para o intervalo de dados é descrito nesta secção.

O seguinte formato de cadeia de ligação pode ser utilizado para ligar a uma base de dados do SQL Server do Python com pyodbc (substitua servername, dbname, nome de utilizador e palavra-passe com os seus valores específicos):

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

O [biblioteca Pandas](http://pandas.pydata.org/) Python fornece um conjunto avançado de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação do Python. O seguinte código lê os resultados devolvidos por uma base de dados do SQL Server para um intervalo de dados Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Agora, pode trabalhar com o intervalo de dados Pandas como abordadas nos tópicos [criar funcionalidades para dados de armazenamento de Blobs do Azure utilizando Panda](create-features-blob.md).

