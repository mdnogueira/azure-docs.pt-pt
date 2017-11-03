---
title: "Adicionar uma partição horizontal com as ferramentas de base de dados elástica | Microsoft Docs"
description: "Definir como utilizar os APIs de dimensionamento flexível para adicionar novos shards para um ID de partição horizontal."
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: 62a349db-bebe-406f-a120-2f1986f2b286
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: 7432bf00aa4d97d8dbc4b92a6a836698ee2b84d7
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Adicionar uma partição horizontal com as ferramentas de base de dados elástica
## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Para adicionar um para um novo intervalo ou chave de partição horizontal
Muitas vezes necessitam de aplicações para simplesmente adicionar nova shards para processar os dados que são esperados dos novas chaves ou intervalos de chaves, para um mapa de partições horizontais que já existe. Por exemplo, uma aplicação em partição horizontal por ID de inquilino poderá ter de aprovisionar um ID de partição horizontal novo para um novo inquilino ou mensal em partição horizontal de dados poderá ter um ID de partição horizontal novo aprovisionado antes do início de cada mês de novo. 

Se o novo intervalo de valores de chave já não faz parte de um mapeamento existente, é muito simple adicionar o ID de partição horizontal novo e associar a nova chave ou um intervalo a esse ID de partição horizontal. 

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Exemplo: adicionar um ID de partição horizontal e respetivo intervalo para um mapa de partições horizontais existente
Este exemplo utiliza o [TryGetShard](https://msdn.microsoft.com/library/azure/dn823929.aspx) o [CreateShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx), [CreateRangeMapping](https://msdn.microsoft.com/library/azure/dn807221.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeShardMap`1.CreateRangeMapping\(Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeMappingCreationInfo{`0}\)) métodos e cria uma instância do [ShardLocation](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation.shardlocation.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardLocation.) classe. No exemplo abaixo, uma base de dados com o nome **sample_shard_2** e todos os objetos de esquema necessário dentro do mesmo tem sido criados para conter o intervalo [300, 400).  

    // sm is a RangeShardMap object.
    // Add a new shard to hold the range being added. 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Create the mapping and associate it with the new shard 
    sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                            (new Range<long>(300, 400), shard2, MappingStatus.Online)); 


Como alternativa, pode utilizar o Powershell para criar um novo Gestor de mapa de partições horizontais. Um exemplo está disponível [aqui](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Para adicionar um ID de partição horizontal para uma parte de um intervalo existente vazia
Em algumas circunstâncias, poderá ter já mapeado um intervalo para um ID de partição horizontal e parcialmente preenchido este com dados, mas agora pretende futuros dados para ser direcionado para um ID de partição horizontal diferentes. Por exemplo, a partições horizontais pelo intervalo de dias e têm 50 dias já alocados um ID de partição horizontal, mas no dia 24, pretende que os dados futuros encaminhado para um ID de partição horizontal diferentes. A base de dados elástica [ferramenta de intercalação de divisão](sql-database-elastic-scale-overview-split-and-merge.md) podem efetuar esta operação, mas se o movimento de dados não é necessário (por exemplo, dados para o intervalo de dias [25, 50), ou seja, dia 25 inclusive para 50 exclusivo, ainda existe) pode efetuar esta inteiramente diretamente a utilizar as APIs de gestão de mapa de partições horizontais.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Exemplo: dividir um intervalo e atribuir a parte vazia para uma partição horizontal recentemente adicionado
Uma base de dados com o nome "sample_shard_2" e todos os objetos de esquema necessário dentro do mesmo ter sido criado.  

    // sm is a RangeShardMap object.
    // Add a new shard to hold the range we will move 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 

        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Split the Range holding Key 25 

    sm.SplitMapping(sm.GetMappingForKey(25), 25); 

    // Map new range holding [25-50) to different shard: 
    // first take existing mapping offline 
    sm.MarkMappingOffline(sm.GetMappingForKey(25)); 
    // now map while offline to a different shard and take online 
    RangeMappingUpdate upd = new RangeMappingUpdate(); 
    upd.Shard = shard2; 
    sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd)); 

**Importante**: utilizar esta técnica apenas se tiver determinado que o intervalo para o mapeamento atualizado está vazio.  Os métodos acima não verificar os dados para o intervalo que está a ser movido, pelo que é melhor incluir verificações no seu código.  Se existirem linhas no intervalo de que está a ser movido, a distribuição de dados real não irá corresponder ao mapa de partições horizontais atualizado. Utilize o [ferramenta de intercalação de divisão](sql-database-elastic-scale-overview-split-and-merge.md) para efetuar a operação em vez disso, nestes casos.  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

