---
title: "Relatório entre bases de dados de nuvem de escalamento horizontal (criação de partições horizontais) | Microsoft Docs"
description: "Utilize base de dados entre consultas de base de dados ao relatório em várias bases de dados."
services: sql-database
documentationcenter: 
manager: jhubbard
author: MladjoA
ms.assetid: c81ef5e3-41e9-4fd2-8631-868f2e168147
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: mlandzic
ms.openlocfilehash: 996ad1d47ece592dcf03a6eb8ed1c1916ceba374
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Comunicar entre bases de dados de nuvem de escalamento horizontal (pré-visualização)
Pode criar relatórios a partir de várias bases de dados SQL do Azure a partir de um ponto de ligação única utilizando um [consulta elástica](sql-database-elastic-query-overview.md). As bases de dados têm de ser particionados horizontalmente (também conhecido como "a").

Se tiver uma base de dados existente, consulte [Migrar bases de dados existentes bases de dados de escalamento horizontal](sql-database-elastic-convert-to-use-elastic-tools.md).

Para compreender os objetos SQL necessários à consulta, consulte [consulta em bases de dados horizontalmente particionadas](sql-database-elastic-query-horizontal-partitioning.md).

## <a name="prerequisites"></a>Pré-requisitos
Transfira e execute o [introdução com exemplo de ferramentas de base de dados elástica](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Criar um ID de partição horizontal manager mapa utilizando a aplicação de exemplo
Aqui, irá criar um mapa de partições horizontais manager juntamente com vários shards, seguido de inserção de dados para os shards. Se já configurou shards com dados fragmentados nos mesmos, pode ignorar os passos seguintes e mover para a secção seguinte.

1. Compilar e executar o **introdução com ferramentas de base de dados elástica** aplicação de exemplo. Siga os passos até que o passo 7 na secção [transferir e executar a aplicação de exemplo](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). No final do passo 7, verá a seguinte linha de comandos:

    ![linha de comandos][1]
2. Na janela de comandos, escreva "1" e prima **Enter**. Isto cria as partições horizontais Gestor de mapa e adiciona dois shards para o servidor. Em seguida, escreva "3" e prima **Enter**; repita a ação de quatro horas. Isto insere linhas de dados de exemplo na sua shards.
3. O [portal do Azure](https://portal.azure.com) deve mostrar três novas bases de dados no seu servidor:

   ![Confirmação do Visual Studio][2]

   Neste momento, são suportadas consultas de base de dados em vários locais através das bibliotecas de cliente de base de dados elástica. Por exemplo, utilize a opção 4 a janela de comandos. Os resultados por uma consulta de partições horizontais multi são sempre um **UNION ALL** dos resultados de todas as partições horizontais.

   Na secção seguinte, vamos criar um ponto de final de base de dados de exemplo que suporta a consulta mais rico dos dados através de partições horizontais.

## <a name="create-an-elastic-query-database"></a>Criar uma base de dados elásticas consulta
1. Abra o [portal do Azure](https://portal.azure.com) e iniciar sessão.
2. Crie uma nova base de dados SQL do Azure no mesmo servidor que a configuração de partições horizontais. Nome da base de dados "ElasticDBQuery."

    ![Portal do Azure e o escalão de preço][3]

    > [!NOTE]
    > Pode utilizar uma base de dados existente. Se o fizer, tem de ser um dos shards que gostaria de executar as suas consultas em não. Esta base de dados será utilizado para criar os objetos de metadados para uma consulta de base de dados elásticas.
    >

## <a name="create-database-objects"></a>Criar objetos de base de dados
### <a name="database-scoped-master-key-and-credentials"></a>Chave mestra do âmbito de base de dados e as credenciais
Estas são utilizadas para estabelecer ligação com o Gestor de mapa de partições horizontais e os shards:

1. Abra o SQL Server Management Studio ou o SQL Server Data Tools no Visual Studio.
2. Ligar à base de dados ElasticDBQuery e execute os seguintes comandos de T-SQL:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>';

        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';

    "nomedeutilizador" e "password" devem ser o mesmo que as informações de início de sessão utilizadas no passo 6 do [transferir e executar a aplicação de exemplo](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app) no [introdução com ferramentas de base de dados elástica](sql-database-elastic-scale-get-started.md).

### <a name="external-data-sources"></a>Origens de dados externas
Para criar uma origem de dados externa, execute o seguinte comando na base de dados ElasticDBQuery:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
      CREDENTIAL = ElasticDBQueryCred,
       SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 "CustomerIDShardMap" é o nome do mapa do ID de partição horizontal, se tiver criado o mapa de partições horizontais e o Gestor de mapa de partições horizontais utilizando o exemplo de ferramentas de base de dados elásticas. No entanto, se utilizou a sua configuração personalizada para este exemplo, em seguida, este deve ser o nome do mapa de partições horizontais que escolheu na sua aplicação.

### <a name="external-tables"></a>Tabelas externas
Crie uma tabela externa que corresponde a tabela de clientes as shards executando o seguinte comando na base de dados ElasticDBQuery:

    CREATE EXTERNAL TABLE [dbo].[Customers]
    ( [CustomerId] [int] NOT NULL,
      [Name] [nvarchar](256) NOT NULL,
      [RegionId] [int] NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
    ) ;

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Executar uma consulta de T-SQL da base de dados elástica de exemplo
Assim que definiu a origem de dados externos e as tabelas externas podem agora utilizar o T-SQL completo sobre as tabelas externas.

Execute esta consulta na base de dados ElasticDBQuery:

    select count(CustomerId) from [dbo].[Customers]

Irá reparar que a consulta agrega os resultados de todas as partições horizontais e fornece o seguinte resultado:

![Detalhes de saída][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Importar os resultados da consulta de base de dados elástica para Excel
 Pode importar os resultados de uma consulta para um ficheiro do Excel.

1. Inicie o Excel 2013.
2. Navegue para o **dados** Friso.
3. Clique em **de outras origens** e clique em **do SQL Server**.

   ![Importação de Excel de outras origens][5]
4. No **Assistente de ligação de dados** escreva as credenciais de início de sessão e nome do servidor. Clique depois em **Seguinte**.
5. Na caixa de diálogo **selecione a base de dados que contém os dados que pretende**, selecione o **ElasticDBQuery** base de dados.
6. Selecione o **clientes** uma tabela na vista de lista e clique em **seguinte**. Em seguida, clique em **concluir**.
7. No **importar dados** formulário em **Selecione como pretende ver estes dados no seu livro**, selecione **tabela** e clique em **OK**.

Todas as linhas da **clientes** tabela, armazenada em diferentes shards preencher a folha do Excel.

Agora, pode utilizar as funções de visualização de dados de elevado desempenho do Excel. Pode utilizar a cadeia de ligação com o nome do servidor, nome de base de dados e as credenciais para ligar as ferramentas de integração do BI e os dados na base de dados elásticas consulta. Certifique-se de que o SQL Server é suportada como uma origem de dados para a ferramenta. Pode consultar a base de dados elásticas consultas e tabelas externas, tal como qualquer outra base de dados do SQL Server e tabelas de SQL Server que seria se ligam a com a ferramenta.

### <a name="cost"></a>Custo
Não há sem encargos adicionais para utilizar a funcionalidade de consulta de base de dados elásticas.

Para obter informações sobre preços, consulte [detalhes de preços de base de dados do SQL Server](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Passos seguintes

* Para obter uma descrição geral da consulta elástica, consulte [descrição geral de consulta elástico](sql-database-elastic-query-overview.md).
* Para um tutorial de criação de partições vertical, consulte [introdução à consulta de base de dados em vários locais (criação de partições vertical)](sql-database-elastic-query-getting-started-vertical.md).
* Para consultas de sintaxe e exemplos de dados verticalmente particionadas, consulte [consultar verticalmente particionada dados)](sql-database-elastic-query-vertical-partitioning.md)
* Para consultas de sintaxe e exemplos de dados particionadas horizontalmente, consulte [consultar horizontalmente particionada dados)](sql-database-elastic-query-horizontal-partitioning.md)
* Consulte [sp\_executar \_remoto](https://msdn.microsoft.com/library/mt703714) para um procedimento armazenado que executa uma instrução de Transact-SQL num único remoto SQL Database do Azure ou um conjunto de bases de dados que servem como shards num esquema de partições horizontal.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
