---
title: Escala do Cosmos BD do Azure e o teste de desempenho | Microsoft Docs
description: Saiba como efetuar o dimensionamento e desempenho de teste com base de dados do Azure Cosmos
keywords: Teste de desempenho
services: cosmos-db
author: arramac
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f4c96ebd-f53c-427d-a500-3f28fe7b11d0
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: arramac
ms.openlocfilehash: d52df1d1f9a29a6fc2a7a3a5e7a6d9fdeaa865e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Desempenho e dimensionamento de teste com base de dados do Azure Cosmos
Desempenho e dimensionamento de teste é um passo chave no desenvolvimento de aplicações. Para muitas aplicações, a camada de base de dados tem um impacto significativo no desempenho geral e a escalabilidade. Por conseguinte, é um componente crítico de teste de desempenho. [BD do Azure do Cosmos](https://azure.microsoft.com/services/cosmos-db/) é específico para a escala elástica e um desempenho previsível. Estas capacidades tornam uma excelente opção para aplicações que necessitam de uma camada de base de dados de elevado desempenho. 

Este artigo é uma referência para programadores implementar conjuntos de teste de desempenho para as respetivas cargas de trabalho de BD do Cosmos do Azure. Também pode ser utilizado para avaliar a base de dados do Azure Cosmos para cenários de aplicações de elevado desempenho. Concentra-se principalmente nas teste isolado de desempenho da base de dados, mas também inclui as melhores práticas para aplicações de produção.

Depois de ler este artigo, poderá responder às seguintes questões: 

* Onde posso encontrar um exemplo de aplicação de cliente .NET para o teste de desempenho da base de dados do Azure Cosmos? 
* Como alcançar níveis de débito elevado com base de dados do Azure Cosmos a minha aplicação de cliente?

Para começar a utilizar com o código, transfira o projeto de [exemplo de testes de desempenho de base de dados do Azure Cosmos](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [!NOTE]
> É o objetivo desta aplicação para demonstrar como obter o melhor desempenho da base de dados do Azure Cosmos com um pequeno número de computadores cliente. O objetivo de exemplo não é atingir a capacidade de débito de pico da base de dados do Azure Cosmos (que podem sem quaisquer limites).
> 
> 

Se estiver à procura de opções de configuração do lado do cliente melhorar o desempenho de base de dados do Azure Cosmos, consulte o artigo [sugestões de desempenho de base de dados do Azure Cosmos](performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Execute o desempenho, testar a aplicação
A forma mais rápida para começar a utilizar é compilar e executar o exemplo de .NET, conforme descrito nos passos seguintes. Também pode rever o código de origem e implementar configurações semelhantes nas suas próprias aplicações de cliente.

**Passo 1:** transferir o projeto de [exemplo de testes de desempenho de base de dados do Azure Cosmos](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark), ou bifurcar o repositório do GitHub.

**Passo 2:** modificar as definições de EndpointUrl, AuthorizationKey, CollectionThroughput e DocumentTemplate (opcional) no App. config.

> [!NOTE]
> Antes de aprovisionar coleções com débito elevado, consulte o [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para estimar os custos por coleção. BD do Azure do Cosmos faturas armazenamento e débito independentemente numa base horária. Pode reduzir os custos eliminando ou reduzir o débito das coleções de base de dados do Azure Cosmos depois de teste.
> 
> 

**Passo 3:** compilar e executar a aplicação de consola na linha de comandos. Deverá ver um resultado como o seguinte:

    C:\Users\documentdb\Desktop\Benchmark>DocumentDBBenchmark.exe
    Summary:
    ---------------------------------------------------------------------
    Endpoint: https://arramacquerymetrics.documents.azure.com:443/
    Collection : db.data at 100000 request units per second
    Document Template*: Player.json
    Degree of parallelism*: -1
    ---------------------------------------------------------------------

    DocumentDBBenchmark starting...
    Found collection data with 100000 RU/s
    Starting Inserts with 100 tasks
    Inserted 4503 docs @ 4491 writes/s, 47070 RU/s (122B max monthly 1KB reads)
    Inserted 17910 docs @ 8862 writes/s, 92878 RU/s (241B max monthly 1KB reads)
    Inserted 32339 docs @ 10531 writes/s, 110366 RU/s (286B max monthly 1KB reads)
    Inserted 47848 docs @ 11675 writes/s, 122357 RU/s (317B max monthly 1KB reads)
    Inserted 58857 docs @ 11545 writes/s, 120992 RU/s (314B max monthly 1KB reads)
    Inserted 69547 docs @ 11378 writes/s, 119237 RU/s (309B max monthly 1KB reads)
    Inserted 80687 docs @ 11345 writes/s, 118896 RU/s (308B max monthly 1KB reads)
    Inserted 91455 docs @ 11272 writes/s, 118131 RU/s (306B max monthly 1KB reads)
    Inserted 102129 docs @ 11208 writes/s, 117461 RU/s (304B max monthly 1KB reads)
    Inserted 112444 docs @ 11120 writes/s, 116538 RU/s (302B max monthly 1KB reads)
    Inserted 122927 docs @ 11063 writes/s, 115936 RU/s (301B max monthly 1KB reads)
    Inserted 133157 docs @ 10993 writes/s, 115208 RU/s (299B max monthly 1KB reads)
    Inserted 144078 docs @ 10988 writes/s, 115159 RU/s (298B max monthly 1KB reads)
    Inserted 155415 docs @ 11013 writes/s, 115415 RU/s (299B max monthly 1KB reads)
    Inserted 166126 docs @ 10992 writes/s, 115198 RU/s (299B max monthly 1KB reads)
    Inserted 173051 docs @ 10739 writes/s, 112544 RU/s (292B max monthly 1KB reads)
    Inserted 180169 docs @ 10527 writes/s, 110324 RU/s (286B max monthly 1KB reads)
    Inserted 192469 docs @ 10616 writes/s, 111256 RU/s (288B max monthly 1KB reads)
    Inserted 199107 docs @ 10406 writes/s, 109054 RU/s (283B max monthly 1KB reads)
    Inserted 200000 docs @ 9930 writes/s, 104065 RU/s (270B max monthly 1KB reads)

    Summary:
    ---------------------------------------------------------------------
    Inserted 200000 docs @ 9928 writes/s, 104063 RU/s (270B max monthly 1KB reads)
    ---------------------------------------------------------------------
    DocumentDBBenchmark completed successfully.
    Press any key to exit...


**Passo 4 (se necessário):** o débito comunicado (RU/s) da ferramenta deve ser igual ou superior ao débito aprovisionado da coleção. Se não for, aumentar o DegreeOfParallelism pequenos incrementos poderá ajudá-lo atingirá o limite. Se o débito da sua aplicação de cliente plateaus, comece por várias instâncias da aplicação em máquinas de cliente adicionais. Se precisar de ajuda com este passo, e-mail askcosmosdb@microsoft.com ou um pedido de suporte a partir do ficheiro a [portal do Azure](https://portal.azure.com).

Depois de ter a aplicação em execução, pode experimentar diferentes [indexação políticas](indexing-policies.md) e [níveis de consistência](consistency-levels.md) para compreender o impacto na débito e latência. Também pode rever o código de origem e implementar configurações semelhantes para as suas próprias aplicações de produção ou conjuntos de teste.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, vamos consultou como pode efetuar o desempenho e dimensionamento de teste com base de dados do Azure Cosmos utilizando uma aplicação de consola .NET. Para obter mais informações, veja os artigos seguintes:

* [Exemplo de testes de desempenho de base de dados do Cosmos do Azure](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Opções de configuração de cliente para melhorar o desempenho de base de dados do Azure Cosmos](performance-tips.md)
* [Criação de partições do lado do servidor na base de dados do Azure Cosmos](partition-data.md)


