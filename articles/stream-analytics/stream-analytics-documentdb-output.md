---
title: "Saída JSON para o Stream Analytics | Microsoft Docs"
description: "Saiba como o Stream Analytics pode visar BD do Cosmos do Azure para saída JSON, para o arquivo de dados e consultas de latência baixa em dados não estruturados de JSON."
keywords: "Saída JSON"
documentationcenter: 
services: stream-analytics,documentdb
author: jseb225
manager: jhubbard
editor: cgronlun
ms.assetid: 5d2a61a6-0dbf-4f1b-80af-60a80eb25dd1
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: ca7102f5fd4a5038cee983b5fdd588d41d1b2725
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="target-azure-cosmos-db-for-json-output-from-stream-analytics"></a>BD de Cosmos do destino do Azure para a saída JSON a partir do Stream Analytics
Pode visar do Stream Analytics [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) para saída JSON, permitindo consultas de arquivo e baixa latência de dados em dados não estruturados de JSON. Este documento inclui algumas melhores práticas para implementar esta configuração.

Os utilizadores que não está familiarizado com a base de dados do Cosmos, observe [percurso de aprendizagem da BD do Azure Cosmos](https://azure.microsoft.com/documentation/learning-paths/documentdb/) para começar a utilizar. 

> [!Note]
> Neste momento, o Azure Stream Analytics suporta apenas a ligação para a utilização de CosmosDB **(SQL do DocumentDB) API**.
> APIs de BD do Cosmos outros Azure ainda não são suportadas. Se o ponto de Azure Stream Analytics para as contas de base de dados do Azure Cosmos criado com outras APIs, os dados poderão não ser corretamente armazenados. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Noções básicas da BD do Cosmos como um destino de saída
A saída de BD do Cosmos Azure Stream Analytics permite escrita sua transmissão em fluxo processar os resultados como saída JSON na sua collection(s) de BD do Cosmos. Do Stream Analytics não criar coleções na base de dados, em vez disso, exigir a criação de compromisso. Isto é, para que os custos de faturação da BD do Cosmos coleções são transparentes para si e para que pode otimizar o desempenho, a consistência e a capacidade do seu coleções diretamente utilizando a [APIs de BD do Cosmos](https://msdn.microsoft.com/library/azure/dn781481.aspx). Recomendamos que utilize um Cosmos DB da base de dados por transmissão em fluxo de trabalho para logicamente as coleções separadas para uma tarefa de transmissão em fluxo.

Algumas das opções de recolha de BD do Cosmos detalhadas abaixo.

## <a name="tune-consistency-availability-and-latency"></a>Otimizar a consistência, disponibilidade e a latência
Para fazer corresponder os seus requisitos de aplicações, o Cosmos DB permite-lhe ajustar e otimizar a base de dados e coleções compromissos entre consistência, disponibilidade e a latência. Consoante os níveis de consistência de leitura as necessidades de cenário contra ler e escrever latência, que pode escolher um nível de consistência na sua conta de base de dados. Também por predefinição, a base de dados do Cosmos permite indexação síncrona de cada operação CRUD à sua coleção. Esta é outra opção útil para controlar o desempenho de leitura/escrita na base de dados do Cosmos. Para obter mais informações sobre este tópico, reveja o [alterar os níveis de consistência da base de dados e a consulta](../documentdb/documentdb-consistency-levels.md) artigo.

## <a name="upserts-from-stream-analytics"></a>Upserts a partir do Stream Analytics
Integração de análise de fluxo com base de dados do Cosmos permite-lhe inserir ou atualizar os registos na sua coleção de BD do Cosmos com base numa coluna ID do documento fornecida. Isto também é referido como um *Upsert*.

Do Stream Analytics utiliza uma abordagem de Upsert otimista, onde as atualizações apenas são efetuadas quando insert falha devido a um conflito de ID do documento. Esta atualização é efetuada pelo Stream Analytics como uma correção, pelo que permite que as atualizações parciais o documento, ou seja, a adição de novas propriedades ou substituir que uma propriedade existente é executada de forma incremental. Tenha em atenção que altera os valores das propriedades de matriz na sua resultado de documentos JSON na matriz todo obter substituída, ou seja, a matriz não está a ser intercalada.

## <a name="data-partitioning-in-cosmos-db"></a>Criação de partições de dados na base de dados do Cosmos
BD do cosmos [particionada coleções](../cosmos-db/partition-data.md) são a abordagem recomendada para os dados de criação de partições. 

Para coleções de BD do Cosmos único, Stream Analytics ainda permite-lhe particionar os dados com base no padrões de consulta e necessidades de desempenho da sua aplicação. Cada coleção pode conter até 10GB de dados (máximo) e atualmente não existe nenhuma forma de aumentar verticalmente (ou overflow) uma coleção. Para aumentar horizontalmente, o Stream Analytics permite-lhe escrever a múltiplas coleções com um prefixo especificado (consulte abaixo os detalhes de utilização). Do Stream Analytics utiliza o consistente [Hash partição resolução](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) estratégia com base no utilizador fornecido PartitionKey coluna para os respetivos registos de saída de partição. O número de coleções com o prefixo especificado na hora de início da tarefa de transmissão em fluxo é utilizado como o número de partição de saída, ao qual a tarefa escreve em paralelo (coleções de BD do Cosmos = partições de saída). Para uma única coleção com fazê-lo de indexação lento apenas insere, sobre 0.4 pode esperar débito de escrita de MB/s. Utilizar várias coleções pode permitem obter um maior débito e a capacidade de aumento.

Se tenciona aumentar a contagem da partição no futuro, terá de parar a tarefa, reparticionar os dados da sua coleções existentes para novas coleções e, em seguida, reinicie a tarefa de Stream Analytics. Obter mais detalhes sobre como utilizar PartitionResolver e volte a criação de partições, juntamente com o código de exemplo, será incluído num post de seguimento. O artigo [divisão em partições e o dimensionamento na base de dados do Cosmos](../documentdb/documentdb-partition-data.md) também fornece detalhes sobre este.

## <a name="cosmos-db-settings-for-json-output"></a>Definições de cosmos base de dados de saída JSON
Criar BD do Cosmos como uma saída no Stream Analytics gera uma linha de comandos para obter informações, como mostrado abaixo. Esta secção fornece uma explicação da definição de propriedades.

Coleção particionada | Várias coleções de "Única partição"
---|---
![ecrã de saída do documentdb stream analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png) |  ![ecrã de saída do documentdb stream analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)


  
> [!NOTE]
> O **várias coleções de "Única partição"** cenário requer uma chave de partição e é uma configuração suportada. 

* **Alias de saída** – um alias para fazer referência este resultado da consulta do ASA  
* **O nome de conta** – o nome ou o ponto final o URI da conta de base de dados do Cosmos.  
* **Chave de conta** – a chave de acesso partilhado para a conta de base de dados do Cosmos.  
* **Base de dados** – nome de base de dados a BD do Cosmos.  
* **Padrão de nome de coleção** – o nome da coleção ou os respetivos padrão para as coleções a ser utilizado. O formato de nome de coleção pode ser construído utilizando o token {partition} opcional, onde as partições começam do 0. Seguem-se entradas de exemplo válido:  
  1\) MyCollection – uma coleção designada "MyCollection" tem de existir.  
  2\) MyCollection {partition} – essas coleções têm de ser existir – "MyCollection0", "MyCollection1", "MyCollection2" e assim sucessivamente.  
* **Chave de partição** – opcional. Isto só é necessário se estiver a utilizar um token {partition} no seu padrão de nome de coleção. O nome do campo em eventos de saída utilizado para especificar a chave de partições da saída nas coleções. Para o resultado única coleção, pode ser qualquer coluna de saída arbitrários utilizado por PartitionId.  
* **ID de documento** – opcional. O nome do campo em eventos de saída utilizado para especificar a chave primária na qual insert ou update baseiam-se operações.  
