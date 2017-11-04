---
title: Migrar bases de dados existentes para aumentar horizontalmente | Microsoft Docs
description: "Converter a bases de dados para utilizar as ferramentas de base de dados elástica através da criação de um ID de partição horizontal Gestor do mapa"
services: sql-database
documentationcenter: 
author: ddove
manager: jhubbard
editor: 
ms.assetid: 8c851d8e-8fd5-4327-89c1-9178b20ddd69
ms.service: sql-database
ms.custom: scale out apps
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: 356c4223ff3ae844552b7bee40aa3ffc6aad7ea0
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="migrate-existing-databases-to-scale-out"></a>Migrar bases de dados existentes para aumentar horizontalmente
Gerir facilmente a sua expandidos a bases de dados existentes com as ferramentas de base de dados de SQL Database do Azure (tais como o [biblioteca de clientes de base de dados elástica](sql-database-elastic-database-client-library.md)). Comece por converter um conjunto de bases de dados para utilizar o [Gestor de mapa de partições horizontais](sql-database-elastic-scale-shard-map-management.md). 

## <a name="overview"></a>Descrição geral
Para migrar uma base de dados em partição horizontal: 

1. Preparar o [base de dados de Gestor de mapa de partições horizontais](sql-database-elastic-scale-shard-map-management.md).
2. Crie o mapa de partições horizontais.
3. Prepare os shards individuais.  
4. Adicione mapeamentos para o mapa de partições horizontais.

Estes técnicas podem ser implementadas utilizando o [biblioteca de cliente do .NET Framework](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/), ou localizar os scripts do PowerShell [BD SQL do Azure - scripts de ferramentas de base de dados elástica](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Os exemplos aqui utilizam os scripts do PowerShell.

Para mais informações sobre o ShardMapManager, consulte [gestão de mapa de partições horizontais](sql-database-elastic-scale-shard-map-management.md). Para obter uma descrição geral das ferramentas de base de dados elásticas, consulte [descrição geral de funcionalidades de base de dados elástica](sql-database-elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Preparar a base de dados do Gestor de mapa do ID de partição horizontal
O Gestor de mapa de partições horizontais é uma base de dados especial que contém os dados para gerir bases de dados de escalamento horizontal. Pode utilizar uma base de dados existente ou criar uma nova base de dados. Uma base de dados a agir como o Gestor de mapa de ID de partição horizontal não deve ser a mesma base de dados como um ID de partição horizontal. O script do PowerShell não criar a base de dados para si. 

## <a name="step-1-create-a-shard-map-manager"></a>Passo 1: criar um ID de partição horizontal Gestor do mapa
    # Create a shard map manager. 
    New-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 
    #<server_name> and <smm_db_name> are the server name and database name 
    # for the new or existing database that should be used for storing 
    # tenant-database mapping information.

### <a name="to-retrieve-the-shard-map-manager"></a>Para obter o Gestor de mapa de partições horizontais
Após a criação, pode obter o Gestor de mapa de partições horizontais com este cmdlet. Este passo é necessário sempre que tem de utilizar o objeto de ShardMapManager.

    # Try to get a reference to the Shard Map Manager  
    $ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 


## <a name="step-2-create-the-shard-map"></a>Passo 2: criar o mapa de partições horizontais
Selecione o tipo de mapa de partições horizontais criar. A opção depende a arquitetura de base de dados: 

1. Inquilino único por base de dados (para os termos, consulte o [glossário](sql-database-elastic-scale-glossary.md).) 
2. Vários inquilinos por base de dados (dois tipos):
   1. Mapeamento de lista
   2. Mapeamento de intervalo

Para um modelo de inquilino único, crie um **mapeamento de lista** mapa de partições horizontais. O modelo de inquilino único atribui uma base de dados por inquilino. Este é um modelo eficiente para programadores de SaaS que simplifica a gestão.

![Mapeamento de lista][1]

O modelo de multi-inquilino atribui vários inquilinos para uma base de dados (e pode distribuir grupos de inquilinos através de várias bases de dados). Utilize este modelo quando espera cada inquilino ter necessidades de dados pequeno. Neste modelo, atribuir um intervalo de inquilinos para uma base de dados utilizando **mapeamento de intervalo**. 

![Mapeamento de intervalo][2]

Ou pode implementar um modelo de base de dados do multi-inquilino utilizando um *mapeamento de lista* atribuir vários inquilinos para uma base de dados. Por exemplo, DB1 é utilizado para armazenar informações sobre o inquilino ID 1 e 5 e DB2 armazena os dados para o inquilino 7 e de inquilino 10. 

![Vários inquilinos na base de dados único][3] 

**Com base na sua escolha, escolha uma destas opções:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Opção 1: criar um mapa de partições horizontais para um mapeamento de lista
Crie um mapa de partições horizontais utilizando o objeto de ShardMapManager. 

    # $ShardMapManager is the shard map manager object. 
    $ShardMap = New-ListShardMap -KeyType $([int]) 
    -ListShardMapName 'ListShardMap' 
    -ShardMapManager $ShardMapManager 


### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Opção 2: criar um mapa de partições horizontais para um mapeamento de intervalo
Para utilizar este padrão de mapeamento, valores de ID de inquilino tem de ser intervalos contínuos e é aceitável para que o intervalo nos intervalos por ignorar o intervalo durante a criação de bases de dados.

    # $ShardMapManager is the shard map manager object 
    # 'RangeShardMap' is the unique identifier for the range shard map.  
    $ShardMap = New-RangeShardMap 
    -KeyType $([int]) 
    -RangeShardMapName 'RangeShardMap' 
    -ShardMapManager $ShardMapManager 

### <a name="option-3-list-mappings-on-a-single-database"></a>Opção 3: Lista de mapeamentos de uma base de dados
Também configurar este padrão requer a criação de um mapa de lista, conforme mostrado no passo 2, a opção 1.

## <a name="step-3-prepare-individual-shards"></a>Passo 3: Preparar shards individuais
Adicione cada ID de partição horizontal (base de dados) para o Gestor de mapa de partições horizontais. Isto prepara as bases de dados individuais para armazenar informações de mapeamento. Execute este método em cada partição horizontal.

    Add-Shard 
    -ShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>'
    # The $ShardMap is the shard map created in step 2.


## <a name="step-4-add-mappings"></a>Passo 4: Adicionar mapeamentos
A adição de mapeamentos depende do tipo de mapa de partições horizontais que criou. Se tiver criado um mapa de lista, adicionar mapeamentos de lista. Se tiver criado um mapa de intervalo, adicione os mapeamentos de intervalo.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Opção 1: mapear os dados para um mapeamento de lista
Mapear os dados ao adicionar um mapeamento de lista para cada inquilino.  

    # Create the mappings and associate it with the new shards 
    Add-ListMapping 
    -KeyType $([int]) 
    -ListPoint '<tenant_id>' 
    -ListShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Opção 2: mapear os dados para um mapeamento de intervalo
Adicione os mapeamentos de intervalo para todos os inquilinos ID intervalo - associações de base de dados:

    # Create the mappings and associate it with the new shards 
    Add-RangeMapping 
    -KeyType $([int]) 
    -RangeHigh '5' 
    -RangeLow '1' 
    -RangeShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 


### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-a-single-database"></a>Passo 4 opção 3: mapear os dados de vários inquilinos numa única base de dados
Para cada inquilino, execute Add-ListMapping (opção 1). 

## <a name="checking-the-mappings"></a>A verificar os mapeamentos de
Informações sobre os shards existentes e os mapeamentos de associados aos mesmos podem ser consultadas utilizando os seguintes comandos:  

    # List the shards and mappings 
    Get-Shards -ShardMap $ShardMap 
    Get-Mappings -ShardMap $ShardMap 

## <a name="summary"></a>Resumo
Depois de concluir a configuração, pode começar a utilizar a biblioteca de clientes de base de dados elásticas. Também pode utilizar [encaminhamento de dados dependentes](sql-database-elastic-scale-data-dependent-routing.md) e [consulta de partições horizontais Multi](sql-database-elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Passos seguintes
Obter os scripts do PowerShell do [scripts de ferramentas de base de dados do Azure SQL DB elástico](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

As ferramentas são também no GitHub: [Azure/elástico-db-tools](https://github.com/Azure/elastic-db-tools).

Utilize a ferramenta de intercalação de divisão para mover dados de um modelo de multi-inquilino ou para um modelo de inquilino único. Consulte [ferramenta de intercalação de divisão](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>Recursos adicionais
Para obter informações sobre os padrões da arquitetura de dados comuns de aplicações de base de dados de software como um serviço (Saas) de multi-inquilino, consulte [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database (Padrões de estrutura para Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure)](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="questions-and-feature-requests"></a>Perguntas e pedidos de funcionalidades
Para perguntas, utilize o [fórum de base de dados SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) e para pedidos de funcionalidades, adicioná-los para o [fórum de comentários da base de dados SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png

