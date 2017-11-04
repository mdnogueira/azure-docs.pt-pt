---
title: "Contadores de desempenho do gestor de mapas de partições horizontais"
description: ShardMapManager classe e dados dependentes encaminhamento contadores de desempenho
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: b090aba0-2e30-454c-96b3-dffa281f539a
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: ddove
ms.openlocfilehash: 4a7a2e0b4f2be852c61fc3ccdd4dea2034b80918
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="performance-counters-for-shard-map-manager"></a>Contadores de desempenho do gestor de mapas de partições horizontais
Pode capturar o desempenho de um [Gestor de mapa de partições horizontais](sql-database-elastic-scale-shard-map-management.md), especialmente quando utilizar [dados dependentes encaminhamento](sql-database-elastic-scale-data-dependent-routing.md). Contadores são criados com métodos da classe Microsoft.Azure.SqlDatabase.ElasticScale.Client.  

Contadores são utilizados para monitorizar o desempenho da [dados dependentes encaminhamento](sql-database-elastic-scale-data-dependent-routing.md) operações. Estes contadores são acessíveis no Monitor de desempenho, sob a categoria de "Gestão de partições horizontais de: base de dados elásticas".

**Para a versão mais recente:** aceda a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Consulte também [atualizar uma aplicação para utilizar a biblioteca de cliente mais recente da base de dados elásticas](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Pré-requisitos
* Para criar a categoria de desempenho e contadores, o utilizador tem de ser uma parte no local **administradores** grupo no computador que aloja a aplicação.  
* Para criar uma instância do contador de desempenho e atualizar os contadores, o utilizador tem de ser membro do **administradores** ou **utilizadores do Monitor de desempenho** grupo. 

## <a name="create-performance-category-and-counters"></a>Criar categoria de desempenho e contadores
Para criar os contadores, chame o método de CreatePeformanceCategoryAndCounters do [ShardMapManagmentFactory classe](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx). Só os administradores podem executar o método: 

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Também pode utilizar [isto](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) script do PowerShell para executar o método. O método cria os seguintes contadores de desempenho:  

* **Em cache mapeamentos**: número de mapeamentos colocados em cache para o mapa de partições horizontais.
* **DDR operações por segundo**: velocidade das operações encaminhamento dependente dos dados para o mapa de partições horizontais. Este contador é atualizado quando uma chamada para [OpenConnectionForKey()](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) resulta numa ligação com êxito para a partição horizontal de destino. 
* **Mapeamento de cache de pesquisa pedidos/seg**: velocidade das operações de pesquisa de cache com êxito para mapeamentos no mapa de partições horizontais. 
* **Mapeamento de pedidos sem êxito na cache de pesquisa/segundo**: velocidade das operações de pesquisa de cache com falhas para mapeamentos no mapa de partições horizontais.
* **Mapeamentos de adicionados ou atualizados na cache por segundo**: taxa mapeamentos de que estão a ser adicionados ou atualizada na cache para o mapa de partições horizontais. 
* **Mapeamentos removidos da cache por segundo**: taxa à qual mapeamentos são removidos da cache para o mapa de partições horizontais. 

Contadores de desempenho são criados para cada mapeamento de partições horizontais em cache por processo.  

## <a name="notes"></a>Notas
Os seguintes eventos acionam a criação dos contadores de desempenho:  

* Inicializar o [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) com [carregamento eager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy.aspx), se o ShardMapManager contém os mapas de partições horizontais. Estes incluem a [GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx?f=255&MSPPError=-2147217396#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerFactory.GetSqlShardMapManager%28System.String,Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerLoadPolicy%29) e [TryGetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx) métodos.
* Pesquisa com êxito de um mapa de partições horizontais (utilizando [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) ou [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)). 
* Criação com êxito de mapa de partições horizontais CreateShardMap() a utilizar.

Os contadores de desempenho serão atualizados por todas as operações de cache efetuadas no mapa de partições horizontais e mapeamentos. Remoção com êxito do mapa de partições horizontais utilizando DeleteShardMap () reults na eliminação da instância de contadores de desempenho.  

## <a name="best-practices"></a>Melhores práticas
* Criação de categoria de desempenho e contadores deve ser efetuada apenas uma vez antes da criação do objeto de ShardMapManager. Cada execução do comando CreatePerformanceCategoryAndCounters() limpa os contadores anteriores (perda de dados comunicados por todas as instâncias) e cria novas.  
* Instâncias do contador de desempenho são criadas por processo. Quaisquer falhas de aplicação ou a remoção de um mapa de partições horizontais da cache resultará na eliminação das instâncias de contadores de desempenho.  

### <a name="see-also"></a>Consultar também
[Descrição geral de funcionalidades de base de dados elástica](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->

