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
ms.date: 08/29/2017
ms.author: arramac
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 3ccc3b176df2f0a5d864554a74508292d272bd5a
ms.contentlocale: pt-pt
ms.lasthandoff: 08/30/2017

---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Introdução ao Azure Cosmos DB: Table API

O [Azure Cosmos DB](introduction.md) fornece a API de Tabela (pré-visualização) para aplicações escritas para o serviços de armazenamento de Tabelas do Azure e necessita de capacidades premium como [distribuição global turn-key](distribute-data-globally.md), [débito dedicado](partition-data.md), latências de milissegundo de digito único em todo o mundo ao percentil 99, elevada disponibilidade garantida e [indexação secundária automática](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). Estas aplicações podem migrar para o Azure Cosmos BD utilizando a API de Tabela sem alterações de código e tirar partido das funcionalidades premium.

Recomendamos que comece por ver o vídeo seguinte, onde Aravind Ramachandran explica como começar a utilizar com a API de Tabela para a o Azure Cosmos DB.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Table-API-for-Azure-Cosmos-DB/player]
> 
> 

## <a name="premium-and-standard-table-apis"></a>APIs de Tabela premium e standard
Se utilizar atualmente o Armazenamento de tabelas do Azure, beneficia das vantagens seguintes se mudar para a pré-visualização de “tabelas premium” do Azure Cosmos DB:

|  | Table Storage do Azure | Azure Cosmos DB: Armazenamento de tabelas (pré-visualização) |
| --- | --- | --- |
| Latência | Rápida, mas sem limites superiores | Latência de milissegundos de um só dígito para leituras e escritas, suportada por leituras de latência inferiores a 10 ms e a escritas de latência inferiores a 15 ms no percentil 99, em qualquer escala e em qualquer parte do mundo |
| Débito | modelo de débito variável. As tabelas têm um limite de escalabilidade de 20 000 operações/s. | Altamente dimensionável, com [débito reservado dedicado por tabela](request-units.md), com suporte dos SLAs. As contas não têm limite superior relativamente ao débito e suportam mais de dez milhões de operações/s por tabela. |
| Distribuição Global | Região única com uma região de leitura secundária opcional para HA. Não pode iniciar ativações pós-falha. | [Distribuição global chave na mão](distribute-data-globally.md) entre uma a mais de 30 regiões, suporte para [ativações pós-falha e manuais](regional-failover.md) em qualquer altura e em qualquer parte do mundo. |
| Indexação | Apenas índice primário em PartitionKey e RowKey. Sem índices secundários | Indexação automática e completa em todas as propriedades, sem gestão de índices |
| Consulta | A execução de consultas utiliza o índice para a chave primária e analisa, se for caso disso. | As consultas podem tirar partido da indexação automática nas propriedades para tempos de consulta rápidos. O motor de bases de dados do Azure Cosmos DB é capaz de suportar agregados, geoespacial e ordenação. |
| Consistência | Forte na região primária, Eventual na secundária | [Cinco níveis de consistência bem definidos](consistency-levels.md) para alternar entre disponibilidade, latência, débito e consistência com base nas necessidades da sua aplicação |
| Preços | Otimizado para armazenamento  | Otimizado para débito |
| SLAs | 99,9% de disponibilidade | 99,99% de disponibilidade numa única região e capacidade para adicionar mais regiões, para maior disponibilidade. [SLAs abrangentes líderes da indústria](https://azure.microsoft.com/support/legal/sla/cosmos-db/) na disponibilidade geral |

## <a name="how-to-get-started"></a>Como começar

Crie uma conta do Azure Cosmos DB no [portal do Azure](https://portal.azure.com) e utilize os nossos [Guias de Introdução para a API de Tabela com .NET](create-table-dotnet.md). 

## <a name="next-steps"></a>Passos seguintes

Eis alguns sítios por onde começar:
* [Criar uma aplicação .NET com a API de Tabela](create-table-dotnet.md)
* [Desenvolver com a API de Tabela no .NET](tutorial-develop-table-dotnet.md)
* [Consulta de dados de tabela utilizando a API de Tabela](tutorial-query-table.md)
* [Como configurar a distribuição global do Azure Cosmos DB utilizando a API de Tabela](tutorial-global-distribution-table.md)
* [SDK da API de Tabela do Azure Cosmos DB para .NET](table-sdk-dotnet.md)

