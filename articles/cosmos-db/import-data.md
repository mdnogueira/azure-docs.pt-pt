---
title: "Ferramenta de migração de base de dados para a base de dados do Azure Cosmos | Microsoft Docs"
description: "Saiba como utilizar as ferramentas de migração de dados de base de dados do Azure Cosmos de código aberto para importar dados para a BD do Cosmos Azure de várias origens, incluindo ficheiros MongoDB, SQL Server, Table storage, Amazon DynamoDB, CSV e JSON. CSV para a conversão de JSON."
keywords: "CSV em json, as ferramentas de migração de base de dados, converter csv json"
services: cosmos-db
author: andrewhoh
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: d173581d-782a-445c-98d9-5e3c49b00e25
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: anhoh
ms.custom: mvc
ms.openlocfilehash: a60c47814da2660f17456f5e662f420adbb9158e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-import-data-into-azure-cosmos-db-with-the-documentdb-api"></a>Como importar dados para a base de dados do Azure Cosmos com a API do DocumentDB

Este tutorial fornece instruções sobre como utilizar a base de dados do Azure Cosmos: ferramenta de migração de dados de API do DocumentDB, que pode importar dados a partir de várias origens, incluindo ficheiros JSON, CSV ficheiros, SQL Server, o MongoDB, o Table storage do Azure, Amazon DynamoDB e a API de DocumentDB do Azure Cosmos DB coleções para coleções para utilização com a base de dados do Azure Cosmos e a API do DocumentDB. Também pode ser utilizada a ferramenta de migração de dados quando a migrar de uma coleção de partições únicas para uma coleção de partição multi para a API do DocumentDB.

A ferramenta de migração de dados só funciona quando a importação de dados na base de dados do Azure Cosmos para utilizam com a API do DocumentDB. Importar dados para utilização com a API de tabela ou a Graph API não é suportado neste momento. 

Para importar dados para utilização com a API do MongoDB, consulte [BD do Azure Cosmos: como migrar dados para a API do MongoDB?](mongodb-migrate.md).

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Instalar a ferramenta de migração de dados
> * Importar dados a partir de origens de dados diferentes
> * Exportar do Azure Cosmos DB JSON

## <a id="Prerequisites"></a>Pré-requisitos
Antes de seguir as instruções neste artigo, certifique-se de que tem o seguinte instalado:

* [O Microsoft .NET Framework 4.51](https://www.microsoft.com/download/developer-tools.aspx) ou superior.

## <a id="Overviewl"></a>Descrição geral da ferramenta de migração de dados
A ferramenta de migração de dados é uma solução de código aberto que importa dados BD do Cosmos do Azure a partir de uma variedade de origens, incluindo:

* Ficheiros JSON
* MongoDB
* SQL Server
* Ficheiros CSV
* Armazenamento de Tabelas do Azure
* Amazon DynamoDB
* HBase
* Coleções do Cosmos BD do Azure

Enquanto a ferramenta de importação inclui uma interface gráfica (dtui.exe), também pode ser controlada na linha de comandos (dt.exe). Na verdade, é uma opção para a saída do comando associado depois de configurar uma importação através da IU. Pode ser transformadas a tabela de origem de dados (por exemplo, SQL Server ou ficheiros CSV) que podem ser criadas relações hierárquicas (subdocuments) durante a importação. Manter a ler para saber mais sobre as opções de origem, linhas de comando para importar a partir de cada origem, as opções de destino e importação de visualizar os resultados de exemplo.

## <a id="Install"></a>Instalar a ferramenta de migração de dados
O código de origem da ferramenta de migração está disponível no GitHub em [este repositório](https://github.com/azure/azure-documentdb-datamigrationtool) e está disponível a partir de uma versão de compilados [Microsoft Download Center](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d). Pode compilar a solução ou simplesmente transferir e extrair a versão compilada para um diretório à sua escolha. Em seguida, execute um:

* **Dtui.exe**: versão de interface gráfica da ferramenta
* **DT.exe**: versão de linha de comandos da ferramenta

## <a name="import-data"></a>Importar dados

Assim que instalou a ferramenta, está na altura de importar os seus dados. Que tipo de dados que pretende importar?

* [Ficheiros JSON](#JSON)
* [MongoDB](#MongoDB)
* [Ficheiros de exportação do MongoDB](#MongoDBExport)
* [SQL Server](#SQL)
* [Ficheiros CSV](#CSV)
* [Armazenamento de tabelas do Azure](#AzureTableSource)
* [Amazon DynamoDB](#DynamoDBSource)
* [Blob](#BlobImport)
* [Coleções do Cosmos BD do Azure](#DocumentDBSource)
* [HBase](#HBaseSource)
* [Importação de em massa do Cosmos BD do Azure](#DocumentDBBulkImport)
* [Importação de registo sequenciais Cosmos BD do Azure](#DocumentDSeqTarget)


## <a id="JSON"></a>Para importar ficheiros JSON
A opção de importador de origem de ficheiro JSON permite-lhe importar um ou mais ficheiros JSON único documento ou ficheiros JSON que contém cada uma matriz de documentos JSON. Ao adicionar pastas que contêm ficheiros JSON para importar, tem a opção de recursivamente a procurar ficheiros em subpastas.

![Opções de origem de ficheiro de captura de ecrã de JSON - ferramentas de migração de bases de dados](./media/import-data/jsonsource.png)

Seguem-se alguns exemplos de linha de comandos para importar ficheiros JSON:

    #Import a single JSON file
    dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory of JSON files
    dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory (including sub-directories) of JSON files
    dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

    #Import a directory (single), directory (recursive), and individual JSON files
    dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

    #Import a single JSON file and partition the data across 4 collections
    dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500

## <a id="MongoDB"></a>Para importar a partir do MongoDB

> [!IMPORTANT]
> Se estiver a importar para uma conta de base de dados do Azure Cosmos com suporte para o MongoDB, siga estes [instruções](mongodb-migrate.md).
> 
> 

A opção de importador de origem do MongoDB permite-lhe importar a partir de uma coleção de MongoDB individuais e, opcionalmente, filtrar documentos através de uma consulta e/ou modificar a estrutura de documento através da utilização de uma projecção.  

![Opções de origem de captura de ecrã do MongoDB](./media/import-data/mongodbsource.png)

A cadeia de ligação se encontra no formato MongoDB padrão:

    mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>

> [!NOTE]
> Utilize o comando de verificar para garantir que a instância do MongoDB especificada no campo de cadeia de ligação pode ser acedida.
> 
> 

Introduza o nome da coleção a partir da qual os dados serão importados. Opcionalmente, pode especificar ou fornecer um ficheiro para uma consulta (por exemplo, {pop: {$gt: 5000}}) e/ou projecção (por exemplo, {loc:0}) para filtrar e formam os dados a serem importados.

Seguem-se alguns exemplos de linha de comandos para importar a partir do MongoDB:

    #Import all documents from a MongoDB collection
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

    #Import documents from a MongoDB collection which match the query and exclude the loc field
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500

## <a id="MongoDBExport"></a>Para importar ficheiros de exportação do MongoDB

> [!IMPORTANT]
> Se estiver a importar para uma conta de base de dados do Azure Cosmos com suporte para o MongoDB, siga estes [instruções](mongodb-migrate.md).
> 
> 

A opção de importador de origem de ficheiro do MongoDB exportação JSON permite-lhe importar um ou mais ficheiros JSON produzidos a partir do utilitário de mongoexport.  

![Opções de origem de exportação de captura de ecrã do MongoDB](./media/import-data/mongodbexportsource.png)

Ao adicionar pastas que contêm ficheiros JSON de exportação de MongoDB para importação, tem a opção de recursivamente a procurar ficheiros em subpastas.

Eis um exemplo de linha de comandos para importar a partir de ficheiros do MongoDB exportação JSON:

    dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500

## <a id="SQL"></a>Para importar a partir do SQL Server
A opção de importador de origem SQL permite-lhe importar a partir de uma base de dados individuais do SQL Server e, opcionalmente, filtre os registos para importar uma consulta. Além disso, pode modificar a estrutura de documento, especificando um separador de aninhamento (mais informações sobre as que dentro de momentos).  

![Opções de origem de captura de ecrã do SQL Server - as ferramentas de migração de base de dados](./media/import-data/sqlexportsource.png)

O formato da cadeia de ligação é o formato de cadeia de ligação do SQL Server padrão.

> [!NOTE]
> Utilize o comando de verificar para garantir que a instância do SQL Server especificada no campo de cadeia de ligação pode ser acedida.
> 
> 

A propriedade de separador aninhamento é utilizada para criar relações hierárquicas (secundárias documentos) durante a importação. Considere a seguinte consulta SQL:

*Selecione CAST (BusinessEntityID AS varchar) como Id, o nome, AddressType como [Address.AddressType], AddressLine1 como [Address.AddressLine1], cidade como [Address.Location.City], StateProvinceName como [Address.Location.StateProvinceName], PostalCode como [ Address.PostalCode] CountryRegionName como [Address.CountryRegionName] do Sales.vStoreWithAddresses onde AddressType = 'Sede'*

Que devolve os seguintes resultados (parciais):

![Resultados da consulta de captura de ecrã do SQL Server](./media/import-data/sqlqueryresults.png)

Tenha em atenção os aliases como Address.AddressType e Address.Location.StateProvinceName. Ao especificar um separador de aninhamento de '.', a ferramenta de importação cria subdocuments endereço e Address.Location durante a importação. Eis um exemplo de um documento do BD Azure Cosmos resultante:

*{"id": "956", "Name": "Melhorar vendas e serviço de", "Endereço": {"AddressType": "Main Office", "AddressLine1": "#500 75 O'Connor Rua", "Localização": {"Cidade": "Ottawa", "StateProvinceName": "Ontario"}, "PostalCode": "K4B 1S2", "CountryRegionName": " Canadá"}}*

Seguem-se alguns exemplos de linha de comandos para importar a partir do SQL Server:

    #Import records from SQL which match a query
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

    #Import records from sql which match a query and create hierarchical relationships
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500

## <a id="CSV"></a>Para importar ficheiros CSV e converter em CSV JSON
A opção de importador de origem de ficheiro CSV permite-lhe importar um ou mais ficheiros CSV. Ao adicionar pastas que contêm ficheiros CSV para importar, tem a opção de recursivamente a procurar ficheiros em subpastas.

![Opções de origem de captura de ecrã do CSV - CSV JSON](media/import-data/csvsource.png)

Semelhante à origem de SQL Server, a propriedade de separador aninhamento pode ser utilizada para criar relações hierárquicas (secundárias documentos) durante a importação. Considere o seguinte cabeçalho CSV linhas de dados e de linha:

![Registos de exemplo de captura de ecrã do CSV - CSV JSON](./media/import-data/csvsample.png)

Tenha em atenção os aliases como DomainInfo.Domain_Name e RedirectInfo.Redirecting. Ao especificar um separador de aninhamento de '.', a ferramenta de importação irá criar subdocuments DomainInfo e RedirectInfo durante a importação. Eis um exemplo de um documento do BD Azure Cosmos resultante:

*{"DomainInfo": {"Nome_domínio": "ACUS.GOV", "Domain_Name_Address": "http://www.ACUS.GOV"}, "Agência Federal": "administrativos conferência do dos Estados Unidos", "RedirectInfo": {"Redirecionar": "0", "Redirect_Destination": ""}, "id": "9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d"}*

A ferramenta de importação irá tentar inferir informações de tipo por valores unquoted ficheiros CSV (delimitado por aspas valores sempre são tratados como cadeias).  Tipos são identificados pela seguinte ordem: número, datetime, booleano.  

Existem dois outros aspetos a ter em atenção sobre importação de CSV:

1. Por predefinição, valores unquoted sempre são recortados para separadores e espaços, enquanto os valores entre aspas são preservados como-é. Este comportamento pode ser substituído com a caixa de verificação de compactação valores entre aspas ou a opção de linha de comandos /s.TrimQuoted.
2. Por predefinição, um nulo unquoted é tratado como um valor nulo. Este comportamento pode ser substituído (ou seja, processe um unquoted nulo como uma cadeia "null") com o Treat unquoted nulo como cadeia caixa de verificação ou a opção de linha de comandos /s.NoUnquotedNulls.

Eis um exemplo de linha de comandos para importação de CSV:

    dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500

## <a id="AzureTableSource"></a>Para importar a partir do Table storage do Azure
A opção de importador de origem de armazenamento de tabelas do Azure permite-lhe importar a partir de uma tabela de armazenamento de tabelas do Azure individual e, opcionalmente, filtre as entidades de tabela a ser importados. Tenha em atenção que não é possível utilizar a ferramenta de migração de dados para importar dados de armazenamento de tabelas do Azure para base de dados do Azure Cosmos para utilização com a API de tabela. Apenas a importação de BD do Cosmos do Azure para utilização com a API do DocumentDB é suportada neste momento.

![Opções de origem do armazenamento de captura de ecrã de tabelas do Azure](./media/import-data/azuretablesource.png)

O formato da cadeia de ligação de armazenamento de tabelas do Azure é:

    DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;

> [!NOTE]
> Utilize o comando de verificar para garantir que a instância de armazenamento de tabelas do Azure especificada no campo de cadeia de ligação pode ser acedida.
> 
> 

Introduza o nome da tabela do Azure de que os dados serão importados. Opcionalmente, pode especificar um [filtro](https://msdn.microsoft.com/library/azure/ff683669.aspx).

A opção de importador de origem de armazenamento de tabelas do Azure tem as seguintes opções adicionais:

1. Incluir campos internos
   1. -Incluem todos os campos internos (PartitionKey, RowKey e Timestamp)
   2. Nenhum - excluir todos os campos internos
   3. RowKey - incluir apenas o campo de RowKey
2. Selecionar colunas
   1. Os filtros de armazenamento do Azure tabela não suportam projeções. Se pretender importar apenas propriedades de entidade de Azure Table específicos, adicione-os à lista de colunas selecionadas. Todas as outras propriedades de entidade serão ignoradas.

Eis um exemplo de linha de comandos para importar a partir do Table storage do Azure:

    dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500

## <a id="DynamoDBSource"></a>Para importar a partir do Amazon DynamoDB
A opção de importador de origem do Amazon DynamoDB permite-lhe importar a partir de uma tabela de Amazon DynamoDB individuais e, opcionalmente, filtre as entidades para ser importado. Vários modelos são fornecidos para que configurar uma importação é tão simples quanto possível.

![Opções de origem de captura de ecrã da Amazon DynamoDB - ferramentas de migração de bases de dados](./media/import-data/dynamodbsource1.png)

![Opções de origem de captura de ecrã da Amazon DynamoDB - ferramentas de migração de bases de dados](./media/import-data/dynamodbsource2.png)

O formato da cadeia de ligação de Amazon DynamoDB é:

    ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;

> [!NOTE]
> Utilize o comando de verificar para garantir que a instância do Amazon DynamoDB especificada no campo de cadeia de ligação pode ser acedida.
> 
> 

Eis um exemplo de linha de comandos para importar a partir do Amazon DynamoDB:

    dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos DB Database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500

## <a id="BlobImport"></a>Para importar ficheiros de armazenamento de Blobs do Azure
O ficheiro JSON, ficheiro de exportação do MongoDB e as opções do importador de origem de ficheiro do CSV permitem-lhe importar um ou mais ficheiros do Blob storage do Azure. Depois de especificar um URL do contentor de Blob e a chave da conta, forneça uma expressão regular para selecionar os ficheiros a importar.

![Opções de origem do ficheiro de captura de ecrã do Blob](./media/import-data/blobsource.png)

Eis um exemplo de linha de comandos para importar ficheiros JSON do Blob storage do Azure:

    dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest

## <a id="DocumentDBSource"></a>Para importar a partir de uma coleção de API de DocumentDB do Azure Cosmos DB
A opção de importador de origem de BD do Cosmos Azure permite-lhe importar dados a partir de uma ou mais coleções da base de dados do Azure Cosmos e, opcionalmente, filtre os documentos através de uma consulta.  

![Opções de origem de BD do Cosmos captura de ecrã do Azure](./media/import-data/documentdbsource.png)

O formato da cadeia de ligação de base de dados do Azure Cosmos é:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

A cadeia de ligação da conta de base de dados do Azure Cosmos pode ser obtida a partir do painel chaves do portal do Azure, conforme descrito em [como gerir uma conta de base de dados do Azure Cosmos](manage-account.md); no entanto, o nome da base de dados tem de ser anexado a ligação cadeia no seguinte formato:

    Database=<CosmosDB Database>;

> [!NOTE]
> Utilize o comando de verificar para garantir que a instância de base de dados do Azure Cosmos especificada no campo de cadeia de ligação pode ser acedida.
> 
> 

Para importar a partir de uma única coleção de BD do Cosmos do Azure, introduza o nome da coleção a partir da qual os dados serão importados. Para importar a partir de várias coleções de base de dados do Azure Cosmos, forneça uma expressão regular para corresponder a um ou mais nomes de coleção (por exemplo, collection01 | collection02 | collection03). Opcionalmente, pode especificar ou fornecer um ficheiro para uma consulta para filtrar e forma as dados a serem importados.

> [!NOTE]
> Uma vez que o campo coleção aceita expressões regulares, se estiver a importar a partir de uma única coleção cujo nome contém carateres de expressão regular, em seguida, esses carateres tem de ser escape em conformidade.
> 
> 

A opção de importador de origem de base de dados do Azure Cosmos tem as seguintes opções avançadas:

1. Incluir campos internos: Especifica se deve ou não incluir as propriedades do sistema de documento de base de dados do Azure Cosmos na exportação (por exemplo, _rid, _ts).
2. Número de tentativas em caso de falha: Especifica o número de vezes para tentar a ligação à base de dados do Azure Cosmos em caso de falhas transitórias (por exemplo, interrupção de conectividade de rede).
3. Intervalo entre tentativas: Especifica o período de tempo de espera entre tentar estabelecer novamente a ligação à base de dados do Azure Cosmos em caso de falhas transitórias (por exemplo, interrupção de conectividade de rede).
4. Modo de ligação: Especifica o modo de ligação para utilizar com a base de dados do Azure Cosmos. As opções disponíveis são DirectTcp, DirectHttps e Gateway. Os modos de ligação direta são mais rápidos, enquanto o modo de gateway é firewall mais amigável como o utiliza apenas a porta 443.

![Origem de captura de ecrã da BD do Cosmos Azure opções avançadas](./media/import-data/documentdbsourceoptions.png)

> [!TIP]
> As predefinições de ferramenta de importação para o modo de ligação DirectTcp. Se ocorrerem problemas de firewall, mude para modo de ligação de Gateway, porque requer apenas a porta 443.
> 
> 

Seguem-se alguns exemplos de linha de comandos para importar da base de dados do Azure Cosmos:

    #Migrate data from one Azure Cosmos DB collection to another Azure Cosmos DB collections
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

    #Migrate data from multiple Azure Cosmos DB collections to a single Azure Cosmos DB collection
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

    #Export an Azure Cosmos DB collection to a JSON file
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionThroughput:2500

> [!TIP]
> A ferramenta de importação de dados do Azure Cosmos DB também suporta a importação de dados a partir de [emulador de BD do Azure Cosmos](local-emulator.md). Ao importar dados a partir de um emulador local, como o ponto final `https://localhost:<port>`. 
> 
> 

## <a id="HBaseSource"></a>Para importar a partir de HBase
A opção de importador de origem do HBase permite-lhe importar dados a partir de uma tabela de HBase e, opcionalmente, filtre os dados. Vários modelos são fornecidos para que configurar uma importação é tão simples quanto possível.

![Opções de origem de captura de ecrã do HBase](./media/import-data/hbasesource1.png)

![Opções de origem de captura de ecrã do HBase](./media/import-data/hbasesource2.png)

O formato da cadeia de ligação de HBase Stargate é:

    ServiceURL=<server-address>;Username=<username>;Password=<password>

> [!NOTE]
> Utilize o comando de verificar para garantir que a instância de HBase especificada no campo de cadeia de ligação pode ser acedida.
> 
> 

Eis um exemplo de linha de comandos para importar a partir do HBase:

    dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport

## <a id="DocumentDBBulkTarget"></a>Para importar a API do DocumentDB (importar em massa)
O importador de em massa de BD do Cosmos Azure permite-lhe importar a partir de qualquer uma das opções de origem disponível, utilizando um procedimento de BD do Cosmos Azure armazenados para uma eficiência. A ferramenta suporta a importação para uma coleção de base de dados do Azure Cosmos particionado em único, bem como a importação na qual os dados estão particionados em várias coleções de base de dados do Azure Cosmos particionado em único. Para obter mais informações sobre a criação de partições de dados, consulte [divisão em partições e o dimensionamento do BD Azure Cosmos](partition-data.md). A ferramenta irá criar, executar e, em seguida, elimine o procedimento armazenado do collection(s) de destino.  

![Opções de em massa de BD do Cosmos captura de ecrã do Azure](./media/import-data/documentdbbulk.png)

O formato da cadeia de ligação de base de dados do Azure Cosmos é:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

A cadeia de ligação da conta de base de dados do Azure Cosmos pode ser obtida a partir do painel chaves do portal do Azure, conforme descrito em [como gerir uma conta de base de dados do Azure Cosmos](manage-account.md); no entanto, o nome da base de dados tem de ser anexado a ligação cadeia no seguinte formato:

    Database=<CosmosDB Database>;

> [!NOTE]
> Utilize o comando de verificar para garantir que a instância de base de dados do Azure Cosmos especificada no campo de cadeia de ligação pode ser acedida.
> 
> 

Para importar a uma única coleção, introduza o nome da coleção para a qual os dados serão importados e clique no botão Adicionar. Para importar a múltiplas coleções, introduza o nome de cada coleção individualmente ou utilize a seguinte sintaxe para especificar várias coleções: *collection_prefix*[iniciar índice - índice de fim]. Ao especificar várias coleções através da sintaxe acima mencionado, tenha em atenção o seguinte:

1. São suportados apenas número inteiro intervalo padrões de nome. Por exemplo, especificar a coleção [0-3] irá produzir as seguintes coleções: collection0, collection1, collection2, collection3.
2. Pode utilizar uma sintaxe abreviada: recolha [3] irá emitir o mesmo conjunto de coleções mencionado no passo 1.
3. Pode ser fornecida mais do que uma substituição. Por exemplo, a coleção [0-1], [0-9] irá gerar 20 nomes de colecção com líderes zeros (collection01, ..02, ..03).

Depois dos nomes de colecção foram especificados, escolhe o débito pretendido o collection(s) (400 RUs para 10 000 RUs). Para melhor desempenho de importação, escolha um maior débito. Para obter mais informações sobre os níveis de desempenho, consulte [níveis de desempenho na base de dados do Azure Cosmos](performance-levels.md).

> [!NOTE]
> A definição de débito de desempenho apenas se aplica a criação de coleção. Se a coleção especificada já existe, o débito não será modificado.
> 
> 

Ao importar em várias coleções, o hash de suporta da ferramenta de importação com base em fragmentação. Neste cenário, especifique a propriedade de documento que pretende utilizar como a chave de partição (se a chave de partição for deixada em branco, documentos será a aleatoriamente entre as coleções de destino).

Opcionalmente, pode especificar o campo na origem de importação deve ser utilizado como a propriedade de id do documento de base de dados do Azure Cosmos durante a importação (tenha em atenção que se documentos não contêm esta propriedade, em seguida, a ferramenta de importação irá gerar um GUID como o valor da propriedade de id).

Existem várias opções avançadas de disponíveis durante a importação. Em primeiro lugar, enquanto a ferramenta inclui um volume de predefinição importar procedimento armazenado (BulkInsert.js), pode optar por especificar o seu próprio procedimento de importação armazenado:

 ![Opção de sproc de inserção de em massa de BD do Cosmos captura de ecrã do Azure](./media/import-data/bulkinsertsp.png)

Além disso, ao importar os tipos de data (por exemplo, a partir de SQL Server ou o MongoDB), pode escolher entre três opções de importação:

 ![Opções de importação do BD do Cosmos captura de ecrã do Azure data hora](./media/import-data/datetimeoptions.png)

* Cadeia: Manter como um valor de cadeia
* Época: Manter como um valor de número de época
* Ambas: Manter a cadeia e valores numéricos época. Esta opção irá criar um subdocument, por exemplo: "date_joined": {"Valor": "2013-10-21T21:17:25.2410000Z", "época": 1382390245}

O importador de em massa de BD do Azure Cosmos tem o seguinte adicionais opções avançadas:

1. Tamanho do lote: A ferramenta por predefinição, um tamanho de lote de 50.  Se os documentos a serem importados são grandes, considere reduzir o tamanho de lote. Por outro lado, se os documentos a serem importados são pequenos, considere aumentar o tamanho de lote.
2. Tamanho máximo de Script (bytes): A ferramenta, será assumida a um tamanho de script máximo de 512KB
3. Desative a geração de Id automática: Se todos os documentos a importar contém um campo de id, em seguida, selecionar esta opção pode aumentar o desempenho. Falta um campo de id exclusivo de documentos não serão importados.
4. Atualização existentes documentos: A ferramenta assume a predefinição não substituir os documentos existentes com conflitos de id. A seleção desta opção permitirá a substituir os documentos existentes com ID correspondentes. Esta funcionalidade é útil para migrações de dados agendada que atualizam documentos existentes.
5. Número de tentativas em caso de falha: Especifica o número de vezes para tentar a ligação à base de dados do Azure Cosmos em caso de falhas transitórias (por exemplo, interrupção de conectividade de rede).
6. Intervalo entre tentativas: Especifica o período de tempo de espera entre tentar estabelecer novamente a ligação à base de dados do Azure Cosmos em caso de falhas transitórias (por exemplo, interrupção de conectividade de rede).
7. Modo de ligação: Especifica o modo de ligação para utilizar com a base de dados do Azure Cosmos. As opções disponíveis são DirectTcp, DirectHttps e Gateway. Os modos de ligação direta são mais rápidos, enquanto o modo de gateway é firewall mais amigável como o utiliza apenas a porta 443.

![Importação em volume de captura de ecrã da BD do Cosmos Azure opções avançadas](./media/import-data/docdbbulkoptions.png)

> [!TIP]
> As predefinições de ferramenta de importação para o modo de ligação DirectTcp. Se ocorrerem problemas de firewall, mude para modo de ligação de Gateway, porque requer apenas a porta 443.
> 
> 

## <a id="DocumentDBSeqTarget"></a>Para importar a API do DocumentDB (importar sequenciais registo)
O importador de sequencial de registo da BD do Cosmos Azure permite-lhe importar a partir de qualquer uma das opções de origem disponíveis numa base de registo ao registo. Poderá escolher esta opção se estiver a importar para uma coleção existente que atingiu a quota de procedimentos armazenados. A ferramenta suporta a importação a uma coleção de Azure Cosmos DB único (single-partição e partição multi), bem como a importação em dados estão particionados em várias coleções de partição única e/ou partição multi da BD do Cosmos do Azure. Para obter mais informações sobre a criação de partições de dados, consulte [divisão em partições e o dimensionamento do BD Azure Cosmos](partition-data.md).

![Opções de importação de registos sequencial de BD do Cosmos captura de ecrã do Azure](./media/import-data/documentdbsequential.png)

O formato da cadeia de ligação de base de dados do Azure Cosmos é:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

A cadeia de ligação da conta de base de dados do Azure Cosmos pode ser obtida a partir do painel chaves do portal do Azure, conforme descrito em [como gerir uma conta de base de dados do Azure Cosmos](manage-account.md); no entanto, o nome da base de dados tem de ser anexado a ligação cadeia no seguinte formato:

    Database=<Azure Cosmos DB Database>;

> [!NOTE]
> Utilize o comando de verificar para garantir que a instância de base de dados do Azure Cosmos especificada no campo de cadeia de ligação pode ser acedida.
> 
> 

Para importar a uma única coleção, introduza o nome da coleção para a qual os dados serão importados e clique no botão Adicionar. Para importar a múltiplas coleções, introduza o nome de cada coleção individualmente ou utilize a seguinte sintaxe para especificar várias coleções: *collection_prefix*[iniciar índice - índice de fim]. Ao especificar várias coleções através da sintaxe acima mencionado, tenha em atenção o seguinte:

1. São suportados apenas número inteiro intervalo padrões de nome. Por exemplo, especificar a coleção [0-3] irá produzir as seguintes coleções: collection0, collection1, collection2, collection3.
2. Pode utilizar uma sintaxe abreviada: recolha [3] irá emitir o mesmo conjunto de coleções mencionado no passo 1.
3. Pode ser fornecida mais do que uma substituição. Por exemplo, a coleção [0-1], [0-9] irá gerar 20 nomes de colecção com líderes zeros (collection01, ..02, ..03).

Depois dos nomes de colecção foram especificados, escolhe o débito pretendido o collection(s) (400 RUs para 250 000 RUs). Para melhor desempenho de importação, escolha um maior débito. Para obter mais informações sobre os níveis de desempenho, consulte [níveis de desempenho na base de dados do Azure Cosmos](performance-levels.md). Qualquer importar coleções com débito > 10 000 RUs necessitarão de uma chave de partição. Se optar por ter mais de 250 000 RUs, terá de um pedido de ficheiros no portal a sua conta aumentada.

> [!NOTE]
> A definição de débito só se aplica à criação de coleção. Se a coleção especificada já existe, o débito não será modificado.
> 
> 

Ao importar em várias coleções, o hash de suporta da ferramenta de importação com base em fragmentação. Neste cenário, especifique a propriedade de documento que pretende utilizar como a chave de partição (se a chave de partição for deixada em branco, documentos será a aleatoriamente entre as coleções de destino).

Opcionalmente, pode especificar o campo na origem de importação deve ser utilizado como a propriedade de id do documento de base de dados do Azure Cosmos durante a importação (tenha em atenção que se documentos não contêm esta propriedade, em seguida, a ferramenta de importação irá gerar um GUID como o valor da propriedade de id).

Existem várias opções avançadas de disponíveis durante a importação. Em primeiro lugar, ao importar os tipos de data (por exemplo, a partir de SQL Server ou o MongoDB), pode escolher entre três opções de importação:

 ![Opções de importação do BD do Cosmos captura de ecrã do Azure data hora](./media/import-data/datetimeoptions.png)

* Cadeia: Manter como um valor de cadeia
* Época: Manter como um valor de número de época
* Ambas: Manter a cadeia e valores numéricos época. Esta opção irá criar um subdocument, por exemplo: "date_joined": {"Valor": "2013-10-21T21:17:25.2410000Z", "época": 1382390245}

O Azure Cosmos DB - importador sequencial de registo tem as seguintes opções avançadas adicionais:

1. Número de pedidos de paralelos: A ferramenta, será assumida a 2 pedidos paralelos. Se os documentos a serem importados são pequenos, considere aumentar o número de pedidos paralelos. Tenha em atenção que se este número é gerado muito, a importação poderá ocorrer limitação.
2. Desative a geração de Id automática: Se todos os documentos a importar contém um campo de id, em seguida, selecionar esta opção pode aumentar o desempenho. Falta um campo de id exclusivo de documentos não serão importados.
3. Atualização existentes documentos: A ferramenta assume a predefinição não substituir os documentos existentes com conflitos de id. A seleção desta opção permitirá a substituir os documentos existentes com ID correspondentes. Esta funcionalidade é útil para migrações de dados agendada que atualizam documentos existentes.
4. Número de tentativas em caso de falha: Especifica o número de vezes para tentar a ligação à base de dados do Azure Cosmos em caso de falhas transitórias (por exemplo, interrupção de conectividade de rede).
5. Intervalo entre tentativas: Especifica o período de tempo de espera entre tentar estabelecer novamente a ligação à base de dados do Azure Cosmos em caso de falhas transitórias (por exemplo, interrupção de conectividade de rede).
6. Modo de ligação: Especifica o modo de ligação para utilizar com a base de dados do Azure Cosmos. As opções disponíveis são DirectTcp, DirectHttps e Gateway. Os modos de ligação direta são mais rápidos, enquanto o modo de gateway é firewall mais amigável como o utiliza apenas a porta 443.

![Importação de registo sequencial de captura de ecrã da BD do Cosmos Azure opções avançadas](./media/import-data/documentdbsequentialoptions.png)

> [!TIP]
> As predefinições de ferramenta de importação para o modo de ligação DirectTcp. Se ocorrerem problemas de firewall, mude para modo de ligação de Gateway, porque requer apenas a porta 443.
> 
> 

## <a id="IndexingPolicy"></a>Especificar uma política de indexação ao criar a base de dados do Azure Cosmos coleções
Ao permitir que a ferramenta de migração para criar coleções durante a importação, pode especificar a política de indexação de coleções. Na secção Opções avançadas da importação em volume de BD do Azure Cosmos e opções de registos do Azure Cosmos DB sequenciais, navegue para a secção de política de indexação.

![Captura de ecrã do Cosmos DB indexação política Azure opções avançadas](./media/import-data/indexingpolicy1.png)

Utilizar a política de indexação avançada opção, pode selecionar um ficheiro de política de indexação, manualmente introduza uma política de indexação ou selecionar a partir de um conjunto de modelos predefinidos (clicando com o botão direito na caixa da política de indexação no texto).

Os modelos de política, que a ferramenta fornece são:

* A predefinição. Esta política é melhor quando estiver a efetuar consultas de igualdade em relação a cadeias e utilizar ORDER BY, intervalo e consultas de igualdade para números. Esta política tem uma tolerância de armazenamento de índice inferior ao intervalo.
* Intervalo. Esta política é melhor que utilizar consultas de ORDER BY, intervalo e igualdade no ambos números e scripts. Esta política tem uma tolerância de armazenamento de índice mais alta que predefinido ou Hash.

![Captura de ecrã do Cosmos DB indexação política Azure opções avançadas](./media/import-data/indexingpolicy2.png)

> [!NOTE]
> Se não especificar uma política de indexação, em seguida, a política predefinida será aplicada. Para obter mais informações sobre as políticas de indexação, consulte [Azure Cosmos DB indexação políticas](indexing-policies.md).
> 
> 

## <a name="export-to-json-file"></a>Exportar para ficheiro JSON
O exportador de JSON de BD do Cosmos Azure permite-lhe exportar qualquer uma das opções disponíveis de origem para um ficheiro JSON que contém uma matriz de documentos JSON. A ferramenta irá processar a exportação para si, ou pode optar por visualizar o comando de migração resultante e execute o comando por si. O ficheiro JSON resultante pode ser armazenado localmente ou no Blob storage do Azure.

![Opção de exportação de ficheiros local de captura de ecrã do Azure Cosmos DB JSON](./media/import-data/jsontarget.png)

![Opção de exportação de armazenamento de captura de ecrã do Blob do Azure Cosmos DB JSON do Azure](./media/import-data/jsontarget2.png)

Opcionalmente, pode optar por prettify resultante JSON, que irá aumentar o tamanho do documento resultante ao tornar o conteúdo mais humanos legível.

    Standard JSON export
    [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]

    Prettified JSON export
    [
     {
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
    }]

## <a name="advanced-configuration"></a>Configuração avançada
No ecrã de configuração avançada, especifique a localização do ficheiro de registo para o qual gostaria de quaisquer erros de escrita. As seguintes regras aplicam-se a esta página:

1. Se não for fornecido um nome de ficheiro, serão devolvidos todos os erros na página de resultados.
2. Se não for fornecido um nome de ficheiro sem um diretório, em seguida, o ficheiro será criado (ou substituído) no diretório do ambiente atual.
3. Se que selecione um ficheiro, em seguida, o ficheiro será substituído, não existe nenhuma opção de acréscimo.

Em seguida, escolha se pretende registar todas, crítico, ou não existem mensagens de erro. Por fim, decida frequência a mensagem de transferência no ecrã será atualizada com o progresso da mesma.

    ![Screenshot of Advanced configuration screen](./media/import-data/AdvancedConfiguration.png)

## <a name="confirm-import-settings-and-view-command-line"></a>Confirme as definições de importação e a linha de comandos de vista
1. Depois de especificar as informações da origem, informações de destino e configuração avançada, reveja a resumo de migração e, opcionalmente, vista/copiar o comando resultante de migração (copiar o comando é útil para automatizar operações de importação):
   
    ![Captura de ecrã do ecrã de resumo](./media/import-data/summary.png)
   
    ![Captura de ecrã do ecrã de resumo](./media/import-data/summarycommand.png)
2. Quando estiver satisfeito com as opções de origem e de destino, clique em **importação**. O tempo decorrido, contagem transferida e informações da falha (se não fornecer um nome de ficheiro de configuração avançada) serão atualizado, a importação está no processo. Depois de concluído, pode exportar os resultados (por exemplo, para lidar com eventuais falhas de importação).
   
    ![Opção de exportação de captura de ecrã do Azure Cosmos DB JSON](./media/import-data/viewresults.png)
3. Também pode iniciar uma importação novo, manter as definições existentes (por exemplo, escolha de informações, de origem e de destino de cadeia de ligação, etc.) ou repor todos os valores.
   
    ![Opção de exportação de captura de ecrã do Azure Cosmos DB JSON](./media/import-data/newimport.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez o seguinte:

> [!div class="checklist"]
> * Instalar a ferramenta de migração de dados
> * Dados importados a partir de origens de dados diferentes
> * Exportados do Cosmos BD do Azure para o JSON

Agora pode avançar para o próximo tutorial e saiba como consultar dados através de BD do Cosmos do Azure. 

> [!div class="nextstepaction"]
>[Como consultar dados?](../cosmos-db/tutorial-query-documentdb.md)
