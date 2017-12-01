---
title: "Gestão de credenciais na biblioteca de cliente da base de dados elástica | Microsoft Docs"
description: "Como definir o nível adequado de credenciais, administrador só de leitura, para aplicações de base de dados elástica"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: 72e0edaf-795e-4856-84a5-6594f735fb7e
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: ddove
ms.openlocfilehash: 0f266f3be8bf8c1699b3b19bea96c83d32f1bd69
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Credenciais utilizadas para aceder a biblioteca de clientes de base de dados elástica
O [biblioteca de clientes de base de dados elástica](sql-database-elastic-database-client-library.md) utiliza três diferentes tipos de credenciais para aceder a [Gestor de mapa de partições horizontais](sql-database-elastic-scale-shard-map-management.md). Consoante a necessidade de utilizar a credencial com o nível mais baixo de acesso possíveis.

* **Credenciais de gestão**: para criar ou manipular um Gestor de mapa de partições horizontais. (Consulte o [glossário](sql-database-elastic-scale-glossary.md).) 
* **Aceder às credenciais**: para aceder a um Gestor de mapa de partições horizontais existente para obter informações sobre shards.
* **As credenciais de ligação**: ligar ao shards. 

Consulte também [gerir bases de dados e inícios de sessão SQL Database do Azure](sql-database-manage-logins.md). 

## <a name="about-management-credentials"></a>Sobre as credenciais de gestão
Credenciais de gestão são utilizadas para criar um **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)) objeto para as aplicações que manipular mapas de partições horizontais. (Por exemplo, consulte [adição de uma partição horizontal com as ferramentas de base de dados elástica](sql-database-elastic-scale-add-a-shard.md) e [encaminhamento de dados dependentes](sql-database-elastic-scale-data-dependent-routing.md)). O utilizador da biblioteca de clientes de dimensionamento elástico cria os utilizadores do SQL Server e os inícios de sessão do SQL Server e certifica-se de que cada um é concedida as permissões de leitura/escrita na base de dados de mapa de partições horizontais global e todas as partições horizontais bases de dados bem. Estas credenciais são utilizadas para manter o mapa de partições horizontais global e os maps de partições horizontais local quando forem efetuadas alterações para o mapa de partições horizontais. Por exemplo, utilize as credenciais de gestão para criar o objeto de Gestor de mapa de partições horizontais (utilizando **GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.getsqlshardmapmanager), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)): 

```
// Obtain a shard map manager. 
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy); 
```

A variável **smmAdminConnectionString** é uma cadeia de ligação que contém as credenciais de gestão. O ID de utilizador e palavra-passe fornecem acesso de leitura/escrita à base de dados de mapa de partições horizontais e shards individuais. A cadeia de ligação de gestão também inclui o nome do servidor e o nome de base de dados para identificar a base de dados de mapa de partições horizontais global. Eis uma cadeia de ligação típico para essa finalidade:

```
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;” 
```

Não utilize valores no formato "username@server" — em vez disso, utilize apenas o valor de "nomedeutilizador".  Isto acontece porque as credenciais tem de trabalhar contra base de dados de Gestor de mapa de partições horizontais e shards individuais, o que poderão estar em servidores diferentes.

## <a name="access-credentials"></a>Credenciais de acesso
Quando criar um ID de partição horizontal mapa manager numa aplicação que não administrar partições horizontais maps, utilize as credenciais que têm permissões só de leitura no mapa de partições horizontais global. As informações obtidas o mapa de partições horizontais global nestas credenciais são utilizadas para [encaminhamento de dados dependentes](sql-database-elastic-scale-data-dependent-routing.md) e ao povoar a cache de mapa de partições horizontais no cliente. As credenciais são fornecidas através do mesmo padrão de chamada para **GetSqlShardMapManager**: 

```
// Obtain shard map manager. 
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

Tenha em atenção a utilização do **smmReadOnlyConnectionString** para refletir a utilização de credenciais diferentes para este acesso em nome de **não admin** utilizadores: estas credenciais não devem fornecer permissões de escrita no o mapa de partições horizontais global. 

## <a name="connection-credentials"></a>Credenciais de ligação
Credenciais adicionais necessários ao utilizar o **OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper._list_shard_mapper.openconnectionforkey), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx)) método para aceder a uma partição horizontal associado com uma chave de fragmentação. Estas credenciais têm de fornecer permissões para acesso só de leitura para as tabelas de mapa de partições horizontais local que reside nas partições horizontais. Isto é necessário efetuar a validação da ligação de dados dependentes encaminhamento de partições horizontais. Este fragmento de código permite o acesso de dados no contexto de encaminhamento de dados dependentes: 

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 
```

Neste exemplo, **smmUserConnectionString** contém a cadeia de ligação para as credenciais do utilizador. Para a BD SQL do Azure, é uma cadeia de ligação típicos para as credenciais de utilizador: 

```
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

Conforme com as credenciais de administrador, não utilize valores no formato "username@server". Em alternativa, utilize "nomedeutilizador" apenas.  Note também que a cadeia de ligação não contém um nome de servidor e um nome de base de dados. Isto acontece porque o **OpenConnectionForKey** chamada direciona automaticamente a ligação para o ID de partição horizontal correto com base na chave. Por conseguinte, o nome de base de dados e o nome do servidor não são fornecidos. 

## <a name="see-also"></a>Consultar também
[Gerir bases de dados e inícios de sessão na Base de Dados SQL do Azure](sql-database-manage-logins.md)

[Securing your SQL Database (Proteger a sua Base de Dados SQL)](sql-database-security-overview.md)

[Introdução às tarefas de base de dados elástica](sql-database-elastic-jobs-getting-started.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

