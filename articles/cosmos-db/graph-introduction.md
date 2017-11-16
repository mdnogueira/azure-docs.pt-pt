---
title: "Introdução ao Azure Cosmos DB gráfico APIs | Microsoft Docs"
description: "Saiba como pode utilizar o Azure Cosmos DB armazenar, consulta, e atravessar enormes gráficos com baixa latência, utilizando o idioma de consulta de gráfico Gremlin do Apache TinkerPop."
services: cosmos-db
author: dennyglee
documentationcenter: 
ms.assetid: b916644c-4f28-4964-95fe-681faa6d6e08
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/15/2017
ms.author: denlee
ms.openlocfilehash: 71d9d03b45d8c4fcf8acb41871dcf3f1304955aa
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="introduction-to-azure-cosmos-db-graph-api"></a>Introdução ao Azure Cosmos DB: Graph API

[BD do Azure do Cosmos](introduction.md) é o serviço de base de dados globalmente distribuída, multimodel da Microsoft para aplicações fundamentais. BD do Azure do Cosmos fornece as seguintes funcionalidades, que sejam copiadas pelo [líder da indústria SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/):

* [Distribuição global chave na mão](distribute-data-globally.md)
* [Dimensionamento elástico de débito e armazenamento](partition-data.md) em todo o mundo
* Latências de dígito milissegundo, o percentil 99th
* [Cinco níveis de consistência bem definidos](consistency-levels.md)
* Garantida elevada disponibilidade 

O Azure Cosmos DB [indexa automaticamente os dados](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sem que tenha de lidar com a gestão de esquemas e índices. É multimodel e suporta o documento, chave-valor, gráfico e modelos de dados columnar.

Recomendamos que veja o vídeo seguinte, onde Kirill Gavrylyuk explica como começar com gráficos no Azure Cosmos DB:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Graphs-with-Azure-Cosmos-DB-Gremlin-API/player]
> 
> 

Fornece a Graph API do Azure Cosmos DB:

- Modelação da Graph.
- Traversal APIs.
- Chave na mão distribuição global.
- Elástico dimensionamento de armazenamento e débito com menos de 10 ms latências de leitura e inferior a 15 ms, o percentil 99th.
- Automático indexação com disponibilidade de consulta instantânea.
- Níveis de consistência ajustáveis pelo.
- SLA abrangente, incluindo um SLA de disponibilidade de 99,99% de todas as contas de única região e todas as contas de multirregião com consistência simples e 99.999% ler disponibilidade em todas as contas de multirregião base de dados.

Para consultar a base de dados do Azure Cosmos, pode utilizar o [Apache TinkerPop](http://tinkerpop.apache.org) idioma transversal, da graph [Gremlin](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps), ou outros sistemas de gráfico compatível com a TinkerPop como [Apache Spark GraphX](spark-connector-graph.md).

Este artigo fornece uma descrição geral do Azure Cosmos DB Graph API e explica como pode utilizá-lo para armazenar em massa e gráficos com billions de vértices e contornos. Pode consultar os gráficos com latência milissegundo e evoluir facilmente a estrutura do gráfico e o esquema.

## <a name="graph-database"></a>Base de dados do gráfico
Dados conforme é apresentado no mundo real naturalmente estão ligados. Modelação de dados tradicionais está centrado nas entidades. Para muitas aplicações, há também uma necessidade para modelar ou para modelar naturalmente entidades e relações.

A [gráfico](http://mathworld.wolfram.com/Graph.html) é uma estrutura que é composta por [vértices](http://mathworld.wolfram.com/GraphVertex.html) e [contornos](http://mathworld.wolfram.com/GraphEdge.html). Vértices e contornos podem ter um número de propriedades arbitrário. Vértices denotam discretos objetos, tais como uma pessoa, um local ou um evento. Margens denotam relações entre vértices. Por exemplo, uma pessoa poderá saber outra pessoa, ser envolvida num evento e foram recentemente numa localização. Propriedades express informações sobre a vértices e contornos. As propriedades de exemplo incluem um vértice que tem um nome, a idade e o limite, o que tem um carimbo de hora e/ou uma ponderação. Mais formally, este modelo é conhecido como um [gráfico de propriedade](http://tinkerpop.apache.org/docs/current/reference/#intro). BD do Azure do Cosmos suporta o modelo de gráfico de propriedade.

Por exemplo, o gráfico de exemplo seguinte mostra as relações entre as pessoas, dispositivos móveis, interesses e sistemas operativos:

![Base de dados de exemplo que mostra pessoas, dispositivos e interesses](./media/graph-introduction/sample-graph.png)

Gráficos são úteis para compreender uma vasta gama de conjuntos de ciência, tecnologia e empresariais. Bases de dados do gráfico permitem-lhe modelar e armazenar gráficos naturalmente e de forma eficiente, o que torna-lhes útil para vários cenários. Bases de dados do gráfico são normalmente bases de dados NoSQL porque estes utilizado frequentemente também casos flexibilidade de esquema necessário e rápida iteração.

Gráficos oferecem um novel e poderosos dados modelação técnica. Mas este facto por si só não é um motivo suficiente para utilizar uma base de dados do gráfico. Para muitos casos de utilização e padrões que envolvem traversals gráfico, gráficos superam o desempenho dos tradicionais bases de dados NoSQL e SQL pelas ordens de grandeza. Esta diferença no desempenho é ainda mais amplified quando mais de uma relação, como friend-de-a-friend a atravessar.

Pode combinar os traversals rápidos que bases de dados do gráfico com algoritmos de gráfico, como profundidade primeira pesquisa, procura primeiro leque e algoritmo da Dijkstra, para resolver problemas em vários domínios como redes sociais, gestão de conteúdos, geoespacial, e recomendações.

## <a name="planet-scale-graphs-with-azure-cosmos-db"></a>Gráficos de escala planet com base de dados do Azure Cosmos
BD do Cosmos do Azure é uma base de dados do gráfico completamente gerido que oferece distribuição global, dimensionamento do armazenamento e débito, automática indexação e consulta, níveis de consistência ajustáveis pelo e suporte para a norma TinkerPop elástica.

![Arquitetura de gráfico do Cosmos BD do Azure](./media/graph-introduction/cosmosdb-graph-architecture.png)

BD do Azure do Cosmos oferece as seguintes capacidades diferenciadas quando comparado com outras bases de dados de gráfico no mercado:

* Débito e armazenamento aprovisionadas dimensionável

 Gráficos no mundo real tem de dimensionar para além da capacidade de um único servidor. Com base de dados do Azure Cosmos, pode dimensionar os gráficos de forma totalmente integrada em vários servidores. Também pode aumentar o débito do seu gráfico independentemente com base nos seus padrões de acesso. BD do Azure do Cosmos suporta bases de dados do gráfico que podem ser dimensionados para tamanhos de armazenamento virtualmente ilimitados e débito aprovisionado.

* Replicação de multirregião

 BD do Azure do Cosmos replica de forma transparente os dados de gráfico para todas as regiões que associou à sua conta. Replicação permite-lhe desenvolver aplicações que necessitam acesso global aos dados. Existem fala nas áreas de consistência, disponibilidade, desempenho e garantias correspondentes. BD do Azure do Cosmos fornece ativação pós-falha regional transparente com APIs multi homing. Pode dimensionar o débito e armazenamento no globo.

* As consultas rápidas e traversals com a sintaxe familiar do Gremlin

 Armazenar vértices heterogéneas e as margens e consultar estes documentos através de uma sintaxe familiar do Gremlin. BD do Azure do Cosmos utiliza uma tecnologia de indexação altamente em simultâneo, livres de bloqueio, estruturada em registos para indexar automaticamente todos os conteúdos. Esta capacidade permite consultas em tempo real e traversals sem a necessidade de especificar sugestões de esquema, índices secundários ou vistas. Saiba mais em [consultar gráficos utilizando Gremlin](gremlin-support.md).

* Totalmente gerida

 BD do Azure do Cosmos elimina a necessidade de gerir a base de dados e recursos de máquinas. Como um serviço completamente gerido do Microsoft Azure, que não precisa de gerir máquinas virtuais, implementar e configurar o software, gerir o dimensionamento, ou lidar com atualizações de camada de dados complexas. Cada gráfico automaticamente é uma cópia de segurança e protegido contra falhas regionais. Pode facilmente adicionar uma conta de base de dados do Azure Cosmos e aprovisionar capacidade conforme necessário, para se poder concentrar na sua aplicação em vez de explorar e gerir a sua base de dados.

* A indexação automática

 Por predefinição, base de dados do Azure Cosmos automaticamente índices todas as propriedades dentro de nós e contornos no gráfico e não espera ou requer nenhum esquema ou criação de índices secundários.

* Compatibilidade com Apache TinkerPop

 BD do Azure do Cosmos nativamente suporta padrão Apache TinkerPop open source e pode ser integrado com outros sistemas de gráfico TinkerPop-ativado. Por isso, pode facilmente migrar a partir de outro gráfico da base de dados, como Titan ou Neo4j, ou utilizar a base de dados do Azure Cosmos com estruturas de análise do gráfico como [Apache Spark GraphX](spark-connector-graph.md).

* Níveis de consistência ajustáveis pelo

 Selecione de cinco níveis de consistência bem definidos para alcançar o compromisso ideal entre a consistência e desempenho. Para consultas e operações de leitura, o Azure Cosmos DB oferece cinco níveis de consistência distintos: forte, consistência vinculada, sessão, prefixo de consistência e eventual. Estes níveis de consistência granular e bem definidos permitem-lhe efetuar fala som entre consistência, disponibilidade e a latência. Saiba mais em [Utilizar níveis de consistência para maximizar a disponibilidade e desempenho no DocumentDB](consistency-levels.md).

BD do Azure do Cosmos também pode utilizar vários modelos, como o documento e do graph, dentro do mesmos contentores/bases de dados. Pode utilizar uma coleção de documentos para armazenar dados de gráfico side by side with documentos. Pode utilizar consultas de SQL Server em JSON e consultas de Gremlin para consultar os mesmos dados que um gráfico.

## <a name="get-started"></a>Introdução
Pode utilizar a interface de linha de comandos (CLI) do Azure, Azure PowerShell ou o portal do Azure com suporte para a graph API para criar contas de base de dados do Azure Cosmos. Depois de criar contas, o portal do Azure fornece um ponto final de serviço, como `https://<youraccount>.graphs.azure.com`, que fornece um WebSocket front-end para Gremlin. Pode configurar as ferramentas de TinkerPop compatível, como o [Gremlin consola](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console), para ligar a este ponto final e criar aplicações Java, Node.js, ou qualquer controlador de cliente Gremlin.

A tabela seguinte mostra controladores Gremlin populares, que pode utilizar em relação a BD do Cosmos do Azure:

| Transferência | Documentação |
| --- | --- |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) |[Gremlin JavaDoc](http://tinkerpop.apache.org/javadocs/current/full/) |
| [Node.js](https://www.npmjs.com/package/gremlin) |[Gremlin-JavaScript no Github](https://github.com/jbmusso/gremlin-javascript) |
| [Consola gremlin](https://tinkerpop.apache.org/downloads.html) |[TinkerPop docs](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |

BD do Azure do Cosmos também fornece uma biblioteca de .NET que possui métodos de extensão Gremlin do [SDKs de BD do Azure Cosmos](documentdb-sdk-dotnet.md) através do NuGet. Esta biblioteca fornece um servidor de Gremlin "no processo" que pode utilizar para ligar diretamente a partições de dados do DocumentDB.

| Transferência | Documentação |
| --- | --- |
| [.NET](https://www.nuget.org/packages/Microsoft.Azure.Graphs/) |[Microsoft.Azure.Graphs](https://msdn.microsoft.com/library/azure/dn948556.aspx) |

Utilizando o [emulador de BD do Azure Cosmos](local-emulator.md), pode utilizar a API .NET do gráfico acima para desenvolver e testar localmente, sem criar uma subscrição do Azure ou incorrer em custos. Quando estiver satisfeito com a forma como a aplicação está a funcionar no emulador, pode mudar para utilizar uma conta de base de dados do Azure Cosmos na nuvem.

> [!NOTE]
> Suporte para Validar consultas Gremlin [emulador de BD do Azure Cosmos](local-emulator.md) só está disponível através do .NET Graph API.

## <a name="scenarios-for-graph-support-of-azure-cosmos-db"></a>Cenários de suporte de gráfico de base de dados do Azure Cosmos
Seguem-se alguns cenários onde o suporte de gráfico de BD do Cosmos Azure pode ser utilizado:

* Redes sociais

 Ao combinar dados sobre os seus clientes e os respetivos interações com outras pessoas, pode desenvolver experiências personalizadas, prever o comportamento do cliente ou ligar a pessoas com outras pessoas com interesses semelhantes. BD do Azure do Cosmos pode ser utilizado para gerir redes sociais e controlar as preferências de cliente e dados.

* Motores de recomendação

 Este cenário é frequentemente utilizado na indústria de revenda. Ao combinar as informações sobre produtos, os utilizadores e as interações do utilizador, como a compra, navegação ou classificação um item, pode criar recomendações personalizadas. A latência baixa, escala elástica e nativo suporte de gráfico de base de dados do Azure Cosmos é ideal para estes interações de modelação.

* Geoespacial

 Muitas aplicações telecomunicações, logística e planeamento levar tem de encontrar uma localização de interesse numa área ou localizar a rota mais curta/ideal entre duas localizações. BD do Cosmos do Azure é uma opção natural para estes problemas.

* Internet das Coisas

 Com a rede e as ligações entre dispositivos de IoT modelados como um gráfico, pode criar uma melhor compreensão do estado dos seus dispositivos e recursos. Também pode saber como as alterações numa parte da rede podem potencialmente afetar parte de outro.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o suporte do gráfico do BD Azure Cosmos, consulte:

* Começar a utilizar o [tutorial de gráfico de base de dados do Azure Cosmos](create-graph-dotnet.md).
* Saiba mais sobre como [consultar gráficos do BD Azure Cosmos utilizando Gremlin](gremlin-support.md).
