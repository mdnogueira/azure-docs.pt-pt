---
title: "Glossário de ferramentas de base de dados elástico | Microsoft Docs"
description: "Explicação dos termos utilizados para ferramentas de base de dados elástica"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: a23a4e81-6706-452d-afc1-a550e5e47af9
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: db8ce257479888db63758e681393c0244af01ce7
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="elastic-database-tools-glossary"></a>Glossário de ferramentas de base de dados elástico
Os termos seguintes são definidos para o [ferramentas de base de dados elástica](sql-database-elastic-scale-introduction.md), uma funcionalidade da SQL Database do Azure. As ferramentas são utilizadas para gerir [mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md)e incluir o [biblioteca de clientes](sql-database-elastic-database-client-library.md), a [ferramenta de intercalação de divisão](sql-database-elastic-scale-overview-split-and-merge.md), [conjuntos elásticos](sql-database-elastic-pool.md), e [consultas](sql-database-elastic-query-overview.md). 

Estes termos utilizados no [adição de uma partição horizontal com as ferramentas de base de dados elástica](sql-database-elastic-scale-add-a-shard.md) e [utilizando a classe de RecoveryManager para corrigir problemas de mapa de partições horizontais](sql-database-elastic-database-recovery-manager.md).

![Termos de dimensionamento elásticos][1]

**Base de dados**: uma SQL database do Azure. 

**Dados dependentes encaminhamento**: A funcionalidade que permite uma aplicação ligar a uma partição horizontal dada uma chave de fragmentação específico. Consulte [dados dependentes encaminhamento](sql-database-elastic-scale-data-dependent-routing.md). Comparar a  **[consulta de partições horizontais Multi](sql-database-elastic-scale-multishard-querying.md)**.

**Mapa de partições horizontais global**: O mapa entre chaves de fragmentação e os respetivos shards dentro de um **conjunto de partições horizontais**. O mapa de partições horizontais global é armazenado no **Gestor de mapa de partições horizontais**. Comparar a **mapa de partições horizontais local**.

**Mapa de partições horizontais lista**: um mapa de partições horizontais no qual fragmentação chaves estão mapeadas individualmente. Comparar a **intervalo mapa de partições horizontais**.   

**Mapa de partições horizontais local**: armazenado num ID de partição horizontal, o mapa de partições horizontais local contém os mapeamentos de shardlets que residem as partições horizontais.

**Consulta de partições horizontais Multi**: A capacidade de emitir uma consulta contra vários shards; conjuntos de resultados são devolvidos utilizando UNION ALL semântica (também conhecido como "consulta da fan-out"). Comparar a **dados dependentes encaminhamento**.

**Multi-inquilino** e **único inquilino**: mostra uma base de dados de inquilino único e uma base de dados do multi-inquilino:

![Bases de dados único e multi-inquilinos](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Eis uma representação de **em partição horizontal** bases de dados único e multi-inquilinos. 

![Bases de dados único e multi-inquilinos](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Mapa de partições horizontais intervalo**: um mapa de partições horizontais na qual a estratégia de distribuição de partições horizontais baseia-se em vários intervalos de valores contíguos. 

**As tabelas de referência**: as tabelas que não estão em partição horizontal, mas são replicadas através de partições horizontais. Por exemplo, códigos de zip podem ser armazenados numa tabela de referência. 

**ID de partição horizontal**: uma SQL database do Azure que armazena dados a partir de um conjunto de dados em partição horizontal. 

**Elasticidade de partições horizontais**: A capacidade para efetuar ambos **dimensionamento horizontal** e **dimensionamento vertical**.

**Tabelas em partição horizontal**: as tabelas que estão em partição horizontal, ou seja, cujos dados são distribuídos por shards com base nos respetivos valores de chave de fragmentação. 

**Chave de fragmentação**: um valor de coluna que determina a forma como os dados são distribuídos por shards. O tipo de valor pode ser um dos seguintes: **int**, **bigint**, **varbinary**, ou **uniqueidentifier**. 

**Conjunto de partições horizontais**: A coleção de partições horizontais de para o mapa de partições horizontais mesmo no Gestor de mapa de partições horizontais.  

**Shardlet**: todos os dados associados a um valor único de uma chave de fragmentação num ID de partição horizontal. Um shardlet é a unidade mais pequena de movimento de dados possíveis quando redistribuir tabelas em partição horizontal. 

**Mapa de partições horizontais**: O conjunto de mapeamentos entre chaves de fragmentação e os respetivos shards.

**Gestor de mapa de partições horizontais**: um arquivo de dados e de objeto de gestão que contém aos mapas precedentes de partições horizontais, localizações de partições horizontais e mapeamentos para um ou mais conjuntos de partições horizontais.

![Mapeamentos de][2]

## <a name="verbs"></a>Verbos
**Dimensionamento horizontal**: O ato de dimensionamento de saída (ou em) numa coleção de partições horizontais adicionando ou removendo partições horizontais para um mapa de partições horizontais, conforme mostrado abaixo.

![Dimensionamento horizontal e vertical][3]

**Intercalar**: O ato de mover shardlets de dois shards para um ID de partição horizontal e atualizar o mapa de partições horizontais em conformidade.

**Mover Shardlet**: O ato de mover um shardlet único para um ID de partição horizontal diferentes. 

**ID de partição horizontal**: O ato de horizontalmente criação de partições de forma idêntica estruturados dados em várias bases de dados com base numa chave de fragmentação.

**Divisão**: O ato de mover vários shardlets de partições um horizontais para outro ID de partição horizontal (normalmente novo). Uma chave de fragmentação é fornecida pelo utilizador como o ponto de divisão.

**Dimensionamento vertical**: O ato de dimensionamento (ou reduzir verticalmente) o nível de desempenho de um ID de partição horizontal individuais. Por exemplo, alterar um ID de partição horizontal do padrão para Premium (o que resulta em mais recursos informáticos). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

