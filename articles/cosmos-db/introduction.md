---
title: "Introdução ao Azure Cosmos DB | Microsoft Docs"
description: "Saiba mais sobre o Azure Cosmos DB. Esta base de dados de distribuição global com vários modelos foi concebida para baixa latência, escalabilidade elástica e elevada disponibilidade."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: a855183f-34d4-49cc-9609-1478e465c3b7
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: f34790ad670b488159e945be9bf4ba378cc5e94a
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="welcome-to-azure-cosmos-db"></a>Bem-vindo ao Azure Cosmos DB

O Azure Cosmos DB é uma base de dados com vários modelos distribuída globalmente. Com o clique de um botão, o Azure Cosmos DB permite-lhe dimensionar de forma elástica e independente o débito e o armazenamento em qualquer número de regiões geográficas do Azure. Oferece débito, latência, disponibilidade e garante a consistência com abrangentes [contratos de nível de serviço](https://aka.ms/acdbsla) (SLAs), que nenhum outro serviço da base de dados oferece. Pode [tente BD do Azure do Cosmos gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição do Azure, sem encargos e compromissos.

![BD do Azure do Cosmos é serviço de base de dados globalmente distribuída da Microsoft com horizontal elástico, garantido baixa latência, cinco modelos de consistência e abrangentes garantida SLAs](./media/introduction/azure-cosmos-db.png)

> [!div class="nextstepaction"]
> [Experimente gratuitamente o Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)

## <a name="key-capabilities"></a>Principais capacidades
Como um serviço de base de dados de distribuição global, o Azure Cosmos DB proporciona as seguintes capacidades para ajudar a criar aplicações dimensionáveis e com elevada capacidade de resposta:

* **Distribuição global chave na mão**
    * Pode [distribuir os seus dados](distribute-data-globally.md) para qualquer número de [regiões do Azure](https://azure.microsoft.com/regions/) com um mero [clique num botão](tutorial-global-distribution-documentdb.md). Desta forma, pode colocar os dados onde os seus utilizadores estão, garantindo a menor latência possível aos seus clientes. 
    * APIs multi homing, sempre a aplicação através da BD do Azure Cosmos sabe onde a região mais próximo é e envia pedidos ao centro de dados mais próximo. Tudo isto é possível sem alterações de configuração. Definir a região de escrita e regiões de leitura tantos conforme pretendem e o restante é processado por si.

* **Vários modelos de dados e APIs populares para aceder e consultar os dados**
    * O modelo de dados baseado em sequência de registo atom (ARS) sob o qual o Azure Cosmos DB é criado suporta nativamente vários modelos de dados, incluindo, entre outros, modelos de documentos, gráficos, chaves-valores, tabela e dados em colunas.
    * As APIs para os modelos de dados seguintes são suportadas com os SDKs disponíveis em várias linguagens:
        * [API de DocumentDB](documentdb-introduction.md): um motor de base de dados JSON sem esquema com capacidades de consulta de SQL.
        * [API de MongoDB](mongodb-introduction.md): serviço de base de dados de A MongoDB desenvolvida com base de dados do Cosmos. É compatível com existente MongoDB bibliotecas, controladores, ferramentas e aplicações.
        * [API de tabela](table-introduction.md): um serviço de base de dados de chave-valor criado para fornecer capacidades de premium para aplicações de armazenamento de tabelas do Azure.
        * [Graph API (Gremlin)](graph-introduction.md): um seguinte de serviço criado de base de dados de gráfico a [especificação do Apache TinkerPop](http://tinkerpop.apache.org/).
        * [API de Cassandra](cassandra-introduction.md): um arquivo de chave/valor incorporado no [Apache Cassandra](https://cassandra.apache.org/) implementação. 
        * Modelos de dados adicionais disponíveis em breve!

* **Dimensionar de forma elástica o débito e o armazenamento a pedido, em todo o mundo**
    * Dimensionar débito de base de dados em facilmente um [por segundo](request-units.md) granularidade e alterá-la sempre que quiser. 
    * Dimensione o tamanho do armazenamento de modo [transparente e automático](partition-data.md) para satisfazer os seus requisitos de tamanho de forma permanente.

* **Criar aplicações com elevada capacidade de resposta e críticas para a missão**
    * O Azure Cosmos DB garante baixa latência ponto a ponto no percentil 99 aos seus clientes. 
    * Para um item de 1 KB típico, o Cosmos DB garante a latência ponto a ponto das leituras abaixo dos 10 ms e das escritas indexadas abaixo dos 15 ms no percentil 99, dentro da mesma região do Azure. As latências medianas são significativamente inferiores (abaixo dos 5 ms).

* **Garantia de disponibilidade “always on”**
    * SLA de 99,99% disponibilidade para todas as contas de única região e todas as contas de multirregião com consistência simples e 99.999% de disponibilidade em todas as contas de base de dados de multirregião leitura.
    * Para maior disponibilidade, implemente em qualquer número de [regiões do Azure](https://azure.microsoft.com/regions).
    * [Simule uma falha](regional-failover.md) de uma ou mais regiões com garantia de zero perda de dados. 

* **Escrever aplicações de distribuição global, da forma correta**
    * Cinco [modelos de consistência](consistency-levels.md) oferecem um espetro que vai desde consistência forte semelhante ao SQL até consistência eventual semelhante ao NoSQL e todas as outras consistências entre estas duas. 
  
* **Garantia de devolução do dinheiro**
    * Os seus dados chegam rápido ou recebe o seu dinheiro de volta. 
    * [Contratos de nível de serviço](https://aka.ms/acdbsla) para disponibilidade, latência, débito e consistência. 

* **Sem gestão de esquema/índices de bases de dados**
    * Deixe de ter de se preocupar em manter o esquema e os índices das bases de dados em sincronização com o esquema da aplicação. Não utilizamos esquemas. 
    * O motor de base de dados do Azure Cosmos DB é totalmente sem esquema. Indexa automaticamente todos os dados que ingere sem necessidade de qualquer esquema ou índice e serve consultas muito rápidas. 

* **Baixo custo de propriedade**
    * Cinco para 10 vezes [mais económico](https://aka.ms/cosmos-db-tco-paper) que uma solução não gerida.
    * Três vezes mais barata que a DynamoDB.

## <a name="capability-comparison"></a>Comparação das capacidades

O Azure Cosmos DB fornece as melhores capacidades das bases de dados relacionais e não relacionais.

| Capacidades | Bases de dados relacionais   | Bases de dados não relacionais (NoSQL) |    Azure Cosmos DB |
| --- | --- | --- | --- |
| Distribuição global | Não | Não | Sim, distribuição chave na mão em mais de 30 regiões, com APIs multi-homing|
| Dimensionamento horizontal | Não | Sim | Sim, pode dimensionar o armazenamento e o débito de forma independente | 
| Garantias de latência | Não | Sim | Sim, 99% de leituras em < 10 ms e de escritas em < 15 ms | 
| Elevada disponibilidade | Não | Sim | Sim, o Cosmos DB está sempre ativado (“always on”), tem compromissos PACELC e disponibiliza opções de ativação pós-falha automáticas e manuais|
| Modelo de dados + API | Relacional + SQL | Vários modelos e API OSS | Vários modelos + SQL + API OSS (mais em breve) |
| SLAs | Sim | Não | Sim, SLAs abrangentes para latência, débito, consistência e disponibilidade |

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Soluções que tiram partido do Azure Cosmos DB

Qualquer [aplicação Web, móvel, de jogos e de IoT](use-cases.md) que tenha de lidar com quantidades gigantescas de leituras e escritas numa escala [global](distribute-data-globally.md) com tempos de resposta curtos para os mais variados dados tira partido da disponibilidade [garantida](https://azure.microsoft.com/support/legal/sla/cosmos-db/), do elevado débito, da baixa latência e da consistência ajustável do Azure Cosmos DB. Saiba mais sobre como pode ser aplicado CosmosDB [IoT e telematics](use-cases.md#iot-and-telematics), [revenda e marketing](use-cases.md#retail-and-marketing), [jogos](use-cases.md#gaming) e [aplicações móveis e Web](use-cases.md#web-and-mobile-applications) .

## <a name="next-steps"></a>Passos seguintes
Introdução ao Azure Cosmos DB com um dos nossos manuais de introdução:

* [Introdução à API DocumentDB do Azure Cosmos DB](create-documentdb-dotnet.md)
* [Introdução à API MongoDB do Azure Cosmos DB](create-mongodb-nodejs.md)
* [Introdução à Graph API do Azure Cosmos DB](create-graph-dotnet.md)
* [Introdução à API de Tabela do Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Experimente gratuitamente o Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)
