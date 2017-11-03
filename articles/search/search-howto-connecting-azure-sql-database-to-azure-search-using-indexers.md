---
title: Ligar a base de dados SQL do Azure na Azure Search utilizando indexadores | Microsoft Docs
description: "Saiba como obter dados do SQL Database do Azure para um índice da Azure Search utilizando indexadores."
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: e9bbf352-dfff-4872-9b17-b1351aae519f
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/13/2017
ms.author: eugenesh
ms.openlocfilehash: 49f614fdf3ba84de238139387ea97ee62077b072
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connecting-azure-sql-database-to-azure-search-using-indexers"></a>Ligação SQL Database do Azure para a Azure Search utilizando indexadores

Antes de pode consultar um [índice da Azure Search](search-what-is-an-index.md), tem preenchê-lo com os seus dados. Se os dados se encontra numa base de dados SQL do Azure, uma **indexador de Azure Search para a SQL Database do Azure** (ou **indexador de SQL do Azure** para abreviar) pode automatizar o processo de indexação, o que significa que menos código para escrever e menos infraestrutura mais importantes para si.

Este artigo abrange mechanics da utilização [indexadores](search-indexer-overview.md), mas também descreve as funcionalidades apenas disponíveis com bases de dados SQL do Azure (por exemplo, registo de alterações integrado). 

Para além das bases de dados SQL do Azure, pesquisa do Azure fornece indexadores para [Azure Cosmos DB](search-howto-index-documentdb.md), [Blob storage do Azure](search-howto-indexing-azure-blob-storage.md), e [table storage do Azure](search-howto-indexing-azure-tables.md). Para pedir suporte para outras origens de dados, fornecer os seus comentários sobre o [fórum de comentários da Azure Search](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indexadores e origens de dados

A **origem de dados** Especifica os dados para o índice, as credenciais de acesso a dados e políticas de forma eficiente identificam as alterações nos dados (novas, modificadas ou eliminadas linhas). Está definido como um recurso independente para que possa ser utilizado por vários indexadores.

Um **indexador** é um recurso que estabelece ligação uma única origem de dados com um índice de pesquisa de destino. Um indexador é utilizado das seguintes formas:

* Execute uma única cópia dos dados para preencher um índice.
* Atualize um índice com alterações da origem de dados com base numa agenda.
* Execute a pedido para atualizar um índice, conforme necessário.

Um indexador única só pode consumir uma tabela ou vista, mas pode criar vários indexadores para preencher vários índices de pesquisa. Para obter mais informações sobre conceitos, consulte [as operações de indexador: fluxo de trabalho normal](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations#typical-workflow).

Pode configurar e configurar um indexador de SQL do Azure utilizando:

* Assistente de importação de dados no [portal do Azure](https://portal.azure.com)
* A pesquisa do Azure [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)
* A pesquisa do Azure [REST API](https://docs.microsoft.com/en-us/rest/api/searchservice/indexer-operations)

Neste artigo, iremos utilizar a API REST para criar **indexadores** e **origens de dados**.

## <a name="when-to-use-azure-sql-indexer"></a>Quando utilizar o indexador de SQL do Azure
Dependendo de vários fatores relacionados com os dados, a utilização do indexador de SQL do Azure pode ou não poderá ser apropriada. Se os dados preencher os seguintes requisitos, pode utilizar o indexador de SQL do Azure.

| Critérios | Detalhes |
|----------|---------|
| Origem de dados de uma única tabela ou vista | Se os dados é dispersos por em várias tabelas, pode criar uma vista única dos dados. No entanto, se utilizar uma vista, não poderá utilizar a deteção de alterações integrado do SQL Server para atualizar um índice com as alterações incrementais. Para obter mais informações, consulte [capturar alterar e eliminar linhas](#CaptureChangedRows) abaixo. |
| Tipos de dados são compatíveis | A maioria das mas não todos os tipos de SQL Server são suportados num índice da Azure Search. Para obter uma lista, consulte [mapeamento de tipos de dados](#TypeMapping). |
| Não é necessária a sincronização de dados em tempo real | Um indexador novamente pode indexar a tabela no máximo a cada cinco minutos. Se precisam de frequentemente as alterações de dados e as alterações sejam refletidas no índice em segundos ou minutos únicos, recomendamos que utilize o [REST API](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) ou [.NET SDK](search-import-data-dotnet.md) para emitir linhas atualizadas diretamente. |
| A indexação incremental é possível | Se tiver um grande conjunto de dados e o plano para executar o indexador com base numa agenda, pesquisa do Azure tem de ser capaz de forma eficiente identificar linhas novas, alteradas ou eliminadas. A indexação não incremental só é permitida se estiver a indexação a pedido (não numa agenda) ou indexação menos de 100 000 linhas. Para obter mais informações, consulte [capturar alterar e eliminar linhas](#CaptureChangedRows) abaixo. |

## <a name="create-an-azure-sql-indexer"></a>Criar um indexador SQL do Azure

1. Crie a origem de dados:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   Pode obter a cadeia de ligação do [portal do Azure](https://portal.azure.com); utilize o `ADO.NET connection string` opção.

2. Se ainda não tiver uma, crie o índice de pesquisa do Azure de destino. Pode criar um índice utilizando o [portal](https://portal.azure.com) ou [criar índice API](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Certifique-se de que o esquema do seu índice de destino é compatível com o esquema da tabela de origem - consulte [mapeamento entre o SQL Server e a pesquisa do Azure, os tipos de dados](#TypeMapping).

3. Crie o indexador, atribua um nome e referenciar o índice de origem e destino de dados:

    ```
    POST https://myservice.search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
    ```

Um indexador criaram não tem uma agenda. Executa automaticamente uma vez quando é criado. Pode executá-lo novamente em qualquer altura utilizando um **executar o indexador** pedido:

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2016-09-01
    api-key: admin-key

Pode personalizar vários aspetos de comportamento do indexador, tais como o tamanho do lote, quantos documentos podem ser ignorados antes de falha de execução de um indexador. Para obter mais informações, consulte [criar API indexador](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

Terá de permitir que os serviços do Azure ligar à base de dados. Consulte [ligar do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) para obter instruções sobre como fazê-lo.

Para monitorizar o histórico de estado e a execução do indexador (número de itens indexadas, falhas, etc.), utilize um **Estado indexador** pedido:

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2016-09-01
    api-key: admin-key

A resposta deve ter um aspeto semelhante ao seguinte:

    {
        "@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }

Histórico de execução contém até 50 de execuções recentemente completadas, o que são ordenadas pela ordem cronológica inversa (de modo a que a execução mais recente vem primeira na resposta).
Informações adicionais sobre a resposta podem ser encontradas na [obter estado do indexador](http://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Executar indexadores com base numa agenda
Também pode dispor o indexador para executar periodicamente com base numa agenda. Para tal, adicione o **agenda** propriedade quando criar ou atualizar o indexador. O exemplo abaixo mostra um pedido PUT para atualizar o indexador:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

O **intervalo** é necessário o parâmetro. O intervalo refere-se para o tempo entre o início do dois execuções de indexador consecutivos. O menor intervalo permitido é de 5 minutos; mais longo-é um dia. Tem de ser formatado como um valor de "dayTimeDuration" XSD (um subconjunto restrito de um [duração ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) valor). O padrão para isto é: `P(nD)(T(nH)(nM))`. Exemplos: `PT15M` para a cada 15 minutos, `PT2H` para cada 2 horas.

O opcional **startTime** indica quando devem começar as execuções agendadas. Se for omitido, é utilizada a hora UTC atual. Neste momento pode pertencer ao passado – nesse caso a primeira execução está agendado como se a execução do indexador continuamente desde a startTime.  

Pode executar apenas uma execução de um indexador de cada vez. Se um indexador está em execução quando a execução estiver agendada, a execução será adiada até a próxima hora agendada.

Vejamos um exemplo para tornar esta mais concretas. Suponha que estamos a agenda de hora a hora seguinte configurada:

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Eis o que acontece:

1. A primeira execução do indexador inicia no ou à volta de 1 de Março de 2015 as 00:00 UTC.
2. Assumir que a execução do demora 20 minutos (ou em qualquer altura inferior a 1 hora).
3. A segunda execução inicia no ou à volta de 1 de Março de 2015 1:00:00.
4. Agora, suponha que a execução do demora mais de uma hora – por exemplo, 70 minutos – para que a sua conclusão aproximadamente 2:10 a.m.
5. É agora as 2:00, hora para a execução terceira iniciar. No entanto, porque a segunda execução de 1: 00. está ainda em execução, a execução terceira é ignorada. A execução terceira inicia no 3 a.m.

Pode adicionar, alterar ou eliminar uma agenda para um indexador existente utilizando um **PUT indexador** pedido.

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Captura de nova, alteração e eliminação de linhas

Utiliza a Azure Search **indexação incremental** para evitar a necessidade de nova indexação toda a tabela ou vista sempre que um indexador é executado. A pesquisa do Azure fornece que dois alterar as políticas de deteção para suportar a indexação incremental. 

### <a name="sql-integrated-change-tracking-policy"></a>Política de controlo de alterações integrado do SQL
Se a base de dados do SQL Server suporta [alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server), recomendamos que utilize **SQL integrada Alterar controlo política**. Esta é a política mais eficiente. Além disso, permite identificar linhas eliminadas sem ter de adicionar uma coluna de "eliminação de forma recuperável" explícito para a tabela da pesquisa do Azure.

#### <a name="requirements"></a>Requisitos 

+ Requisitos da versão da base de dados:
  * SQL Server 2012 SP3 e posteriormente, se estiver a utilizar o SQL Server em VMs do Azure.
  * Azure SQL Database V12, se estiver a utilizar a SQL Database do Azure.
+ Tabelas apenas (não existem vistas). 
+ Na base de dados, [ativar registo de alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) da tabela. 
+ Nenhuma composto chave primária (um primário chave que contém mais do que uma coluna) na tabela.  

#### <a name="usage"></a>Utilização

Para utilizar esta política, criar ou atualizar a origem de dados como esta:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

Quando utilizar a política de registo de alterações integrado do SQL não especificar uma política de deteção de eliminação de dados separada - esta política tem suporte incorporado para identificar o eliminar linhas. No entanto, para eliminações ser detetado "automagically", a chave do documento no seu índice de pesquisa tem de ser o mesmo que a chave primária na tabela de SQL. 

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Política de deteção de alteração do nível máximo

Esta política de deteção de alteração depende de uma coluna de "nível máximo" capturar a versão ou a hora quando uma linha foi atualizada pela última vez. Se estiver a utilizar uma vista, tem de utilizar uma política de nível máximo. A coluna de nível máximo tem de cumprir os seguintes requisitos.

#### <a name="requirements"></a>Requisitos 

* Inserções de todos os especificar um valor para a coluna.
* Todas as atualizações de um item também alterar o valor da coluna.
* O valor desta coluna aumenta com cada insert ou update.
* As consultas com o seguinte onde e ORDER BY cláusulas podem ser executadas de forma eficiente:`WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> Recomendamos vivamente a utilização de [rowversion](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) tipo de dados para a coluna de nível máximo. Se for utilizado a qualquer outro tipo de dados, controlo de alterações não é assegurada para capturar todas as alterações na presença de transações executar simultaneamente com uma consulta do indexador. Quando utilizar **rowversion** numa configuração com réplicas só de leitura, tem de apontar o indexador na réplica primária. Apenas uma réplica primária pode ser utilizada para cenários de sincronização de dados.

#### <a name="usage"></a>Utilização

Para utilizar uma política de nível de níveis máximos, criar ou atualizar a origem de dados como esta:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }

> [!WARNING]
> Se a tabela de origem não tem um índice na coluna de nível de níveis máximos, consultas utilizadas pelo indexador SQL poderão tempo limite. Em particular, a `ORDER BY [High Water Mark Column]` cláusula requer um índice para executar com eficiência quando a tabela contém o número de linhas.
>
>

Se ocorrerem erros de tempo limite, pode utilizar o `queryTimeout` definição de configuração de indexador para definir o tempo limite de consulta para um valor superior ao tempo limite predefinido 5 minutos. Por exemplo, para definir o tempo limite para 10 minutos, criar ou atualizar o indexador com a seguinte configuração:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

Também pode desativar o `ORDER BY [High Water Mark Column]` cláusula. No entanto, isto não é recomendado porque se a execução do indexador for interrompida devido a um erro, o indexador tem de processar novamente todas as linhas se for executada mais tarde - mesmo que o indexador já foi processado quase todas as linhas dentro do tempo que foi interrompida. Para desativar o `ORDER BY` cláusula, utilize o `disableOrderByHighWaterMarkColumn` definição na definição do indexador:  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Política de deteção de eliminação de coluna eliminar de forma recuperável
Quando as linhas são eliminadas da tabela de origem, provavelmente pretende eliminar as linhas da, bem como o índice de pesquisa. Se utilizar a política do registo de alterações integrado do SQL, isto está Tratado por si. No entanto, a política de controlo de alterações de nível máximo não ajudá-lo com linhas eliminadas. O que fazer?

Se as linhas fisicamente são removidas da tabela, pesquisa do Azure tem nenhuma forma para inferir a presença de registos que já não existe.  No entanto, pode utilizar a técnica de "eliminar de forma recuperável" logicamente eliminar linhas sem removê-los a partir da tabela. Adicione uma coluna para as linhas da tabela ou vista e marca como eliminada utilizando nessa coluna.

Quando utilizar a técnica de eliminação de forma recuperável, pode especificar a eliminação de forma recuperável política da seguinte forma quando criar ou atualizar a origem de dados:

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

O **softDeleteMarkerValue** tem de ser uma cadeia – utilize a representação de cadeia do seu valor real. Por exemplo, se tiver uma coluna de número inteiro onde linhas eliminadas são marcadas com o valor de 1, utilize `"1"`. Se tiver uma coluna de bits onde linhas eliminadas são marcadas com o valor de true booleano, utilize `"True"`.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-search-data-types"></a>Mapeamento entre os tipos de dados do SQL Server e da Azure Search
| Tipo de dados do SQL Server | Permitido tipos de campo de índice de destino | Notas |
| --- | --- | --- |
| bits |Boolean, EDM | |
| int, smallint, tinyint |EDM Edm.Int32, Edm.Int64, | |
| bigint |Edm.Int64, EDM | |
| número de vírgula flutuante real, |Edm.Double, EDM | |
| em smallmoney, numérica decimal dinheiro |Edm.String |A pesquisa do Azure não suporta a conversão de tipos decimais numa Edm.Double porque este perderia precisão |
| char, nchar, varchar, nvarchar |Edm.String<br/>Coleção (Edm.String) |Uma cadeia de SQL Server pode ser utilizada para preencher um campo Collection(Edm.String) se a cadeia representa uma matriz de cadeias de JSON:`["red", "white", "blue"]` |
| smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, EDM | |
| uniqueidentifer |Edm.String | |
| Geografia |Edm.GeographyPoint |São suportadas apenas instâncias de geografia de tipo de ponto de 4326 SRID (que é a predefinição) |
| ROWVERSION |N/D |Colunas de versão de linha não podem ser armazenadas no índice de pesquisa, mas podem ser utilizadas para controlo de alterações |
| tempo timespan, binary, varbinary, imagem, xml, geometria, os tipos CLR |N/D |Não suportado |

## <a name="configuration-settings"></a>Definições de configuração
Indexador SQL expõe várias definições de configuração:

| Definição | Tipo de dados | Objetivo | Valor predefinido |
| --- | --- | --- | --- |
| queryTimeout |Cadeia |Define o tempo limite para a execução da consulta SQL |5 minutos ("00: 05:00") |
| disableOrderByHighWaterMarkColumn |bool |Faz com que a consulta SQL utilizada pela política de nível máximo para omita a cláusula ORDER BY. Consulte [política de nível máximo](#HighWaterMarkPolicy) |FALSO |

Estas definições são utilizadas no `parameters.configuration` objeto na definição do indexador. Por exemplo, para definir o tempo limite de consulta para 10 minutos, criar ou atualizar o indexador com a seguinte configuração:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="faq"></a>FAQ

**P: Posso utilizar o indexador de Azure SQL com bases de dados do SQL Server em execução em VMs de IaaS no Azure?**

Sim. No entanto, terá de permitir que o serviço de pesquisa ligar à base de dados. Para obter mais informações, consulte [configurar uma ligação de um indexador de Azure Search para SQL Server numa VM do Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

**P: Posso utilizar o indexador de Azure SQL com bases de dados do SQL Server em execução no local?**

Não diretamente. Não é recomendável ou suportar uma ligação direta, como fazê-lo, por isso, precisaria de abrir as bases de dados para o tráfego de Internet. Os clientes têm efetuada com êxito com este cenário através de bridge de tecnologias como o Azure Data Factory. Para obter mais informações, consulte [enviar dados para um índice da Azure Search utilizando o Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-azure-search-connector).

**P: Posso utilizar o indexador de Azure SQL com bases de dados diferente do SQL Server em execução no IaaS do Azure?**

Não. Não é suportada neste cenário, porque ainda não testámos o indexador com quaisquer bases de dados diferente do SQL Server.  

**P: Posso criar vários indexadores em execução com base numa agenda?**

Sim. No entanto, apenas um indexador pode estar em execução num nó em simultâneo. Se precisar de vários indexadores em execução em simultâneo, considere como aumentar verticalmente o serviço de pesquisa a mais do que uma unidade de pesquisa.

**P: é executar um efeito de indexador meu carga de trabalho de consulta?**

Sim. Indexador for executado num de nós no seu serviço de pesquisa e recursos desse nó são partilhados entre a indexação e que serve o tráfego de consulta e de outros pedidos de API. Se executar cargas de trabalho que consomem muita indexação e a consulta e encontrar uma taxa elevada de 503 erros ou crescente de tempos de resposta, considere [como aumentar verticalmente o serviço de pesquisa](search-capacity-planning.md).

**P: Posso utilizar uma réplica secundária num [cluster de ativação pós-falha](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) como uma origem de dados?**

Depende. Para indexação completo de uma tabela ou vista, pode utilizar uma réplica secundária. 

Para indexação incremental, suporta a pesquisa do Azure dois altera as políticas de Deteção: SQL integrado Alterar controlo e de elevado nível máximo.

Em réplicas só de leitura, base de dados do SQL Server não suporta o registo de alterações integrado. Por conseguinte, tem de utilizar a política de nível máximo. 

A nossa recomendação padrão consiste em utilizar o tipo de dados rowversion para a coluna de nível máximo. No entanto, utilizar rowversion baseia-se na SQL Database `MIN_ACTIVE_ROWVERSION` função, o que não é suportada em réplicas só de leitura. Por conseguinte, tem de apontar o indexador para uma réplica primária se estiver a utilizar rowversion.

Se tentar utilizar rowversion numa réplica só de leitura, verá o erro seguinte: 

    "Using a rowversion column for change tracking is not supported on secondary (read-only) availability replicas. Please update the datasource and specify a connection to the primary availability replica.Current database 'Updateability' property is 'READ_ONLY'".

**P: pode utilizar uma coluna não rowversion alternativo, máximo de nível de registo de alterações?**

Não é recomendado. Apenas **rowversion** permite a sincronização de dados fiável. No entanto, dependendo da lógica da sua aplicação, poderá ser segura se:

+ Pode certificar-se de que quando o indexador for executado, não existem não existem transações pendentes na tabela que está a ser indexada (por exemplo, todos os tabela acontecer porque se encontra definido um lote de uma agenda e o agendamento do indexador de Azure Search para evitar a sobreposição com a atualização de tabela agendar atualizações).  

+ Periodicamente efetuar um reindex completa para recolher as linhas em falta. 
