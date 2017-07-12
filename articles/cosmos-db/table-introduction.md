---
title: "Introdução à Table API do Azure Cosmos DB | Microsoft Docs"
description: "Saiba como pode utilizar o Azure Cosmos DB para armazenar e consultar grandes volumes de dados de chaves-valores com baixa latência através da utilização das populares APIs OSS MongoDB."
services: cosmos-db
author: bhanupr
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/09/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 07dc5e1520cf60e19a1961ad85d2aea0cc7bd15f
ms.contentlocale: pt-pt
ms.lasthandoff: 06/09/2017


---
<a id="introduction-to-azure-cosmos-db-table-api" class="xliff"></a>

# Introdução ao Azure Cosmos DB: Table API

O [Azure Cosmos DB](introduction.md) é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft para aplicações críticas para atividades. O Azure Cosmos DB proporciona [distribuição global chave na mão](distribute-data-globally.md), [dimensionamento elástico de débito e armazenamento](partition-data.md) em todo o mundo, latências de milissegundos de um só dígito no percentil 99, [cinco níveis de consistência bem definidos](consistency-levels.md) e elevada disponibilidade garantida, tudo com o suporte de [SLAs líderes da indústria](https://azure.microsoft.com/support/legal/sla/cosmos-db/). O Azure Cosmos DB [indexa automaticamente os dados](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sem que tenha de lidar com a gestão de esquemas e índices. É multimodal e suporte modelos de dados em documentos, chaves-valores, gráficos e em colunas. 

![API do Armazenamento de Tabelas do Azure e Azure Cosmos DB](./media/table-introduction/premium-tables.png) 

O Azure Cosmos DB fornece a API de tabela (pré-visualização) para aplicações que precisam de um repositório de chave-valor com esquema flexível, desempenho previsível, distribuição global e alta taxa de transferência. A API de tabela fornece a mesma funcionalidade de armazenamento de tabela do Azure, mas aproveita os benefícios do mecanismo do Azure Cosmos DB. 

Pode continuar a utilizar o Armazenamento de tabelas do Azure para tabelas que tenham requisitos de armazenamento elevado e débito mais baixo. O Azure Cosmos DB vai introduzir suporte para tabelas otimizadas para armazenamento numa atualização futura e as contas existentes e novas do Armazenamento de tabelas do Azure serão atualizadas para o Azure Cosmos DB.

<a id="premium-and-standard-table-apis" class="xliff"></a>

## APIs de Tabela premium e standard
Se utilizar atualmente o Armazenamento de tabelas do Azure, beneficia das vantagens seguintes se mudar para a pré-visualização de “tabelas premium” do Azure Cosmos DB:

|  | Table Storage do Azure | Azure Cosmos DB: Armazenamento de tabelas (pré-visualização) |
| --- | --- | --- |
| Latência | Rápida, mas sem limites superiores | Latência de milissegundos de um só dígito para leituras e escritas, suportada por leituras de latência inferiores a 10 ms e a escritas de latência inferiores a 15 ms no percentil 99, em qualquer escala e em qualquer parte do mundo. |
| Débito | Altamente dimensionável, mas sem modelo de débito dedicado. As tabelas têm um limite de escalabilidade de 20 000 operações/s. | Altamente dimensionável, com [débito reservado dedicado por tabela](request-units.md), com suporte dos SLAs. As contas não têm limite superior relativamente ao débito e suportam mais de dez milhões de operações/s por tabela. |
| Distribuição Global | Região única com uma região de leitura secundária opcional para HA. Não pode iniciar ativações pós-falha. | [Distribuição global chave na mão](distribute-data-globally.md) entre uma a mais de 30 regiões, suporte para [ativações pós-falha e manuais](regional-failover.md) em qualquer altura e em qualquer parte do mundo. |
| Indexação | Apenas índice primário em PartitionKey e RowKey. Sem índices secundários | Indexação automática e completa em todas as propriedades, sem gestão de índices |
| Consulta | A execução de consultas utiliza o índice para a chave primária e analisa, se for caso disso. | As consultas podem tirar partido da indexação automática nas propriedades para tempos de consulta rápidos. O motor de bases de dados do Azure Cosmos DB é capaz de suportar agregados, geoespacial e ordenação. |
| Consistência | Forte na região primária, Eventual na secundária | [cinco níveis de consistência bem definidos](consistency-levels.md) para alternar entre disponibilidade, latência, débito e consistência com base nas necessidades da sua aplicação |
| Preços | Otimizado para armazenamento  | Otimizado para débito |
| SLAs | 99,9% de disponibilidade | 99,99% de disponibilidade numa única região e capacidade para adicionar mais regiões, para maior disponibilidade. [SLAs abrangentes líderes da indústria](https://azure.microsoft.com/support/legal/sla/cosmos-db/) na disponibilidade geral |

<a id="how-to-get-started" class="xliff"></a>

## Como começar

Crie uma conta do Azure Cosmos DB no [portal do Azure](https://portal.azure.com) e utilize os nossos [Guias de Introdução para a API de Tabela com .NET](create-table-dotnet.md). 

<a id="next-steps" class="xliff"></a>

## Passos seguintes

Eis alguns sítios por onde começar:
* Get started with [Azure Cosmos DB's Table API](create-table-dotnet.md) using existing NET Table SDK (Introdução à API de Tabela do Azure Cosmos DB com o SDK de Tabela NET existente).
* Saiba mais sobre [Global distribution with Azure Cosmos DB](distribute-data-globally.md) (Distribuição global com o Azure Cosmos DB).
* Saiba mais sobre [Provisioned throughput in Azure Cosmos DB](request-units.md) (Débito aprovisionado no Azure Cosmos DB).

