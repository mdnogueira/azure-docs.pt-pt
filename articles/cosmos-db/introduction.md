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
ms.topic: get-started-article
ms.date: 05/10/2017
ms.author: mimig
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 49eb2e4f7d57de44a3b7a877dfdd138f4c374436
ms.contentlocale: pt-pt
ms.lasthandoff: 06/28/2017

---

<a id="welcome-to-azure-cosmos-db" class="xliff"></a>

# Bem-vindo ao Azure Cosmos DB

O Azure Cosmos DB é uma base de dados com vários modelos distribuída globalmente. Com o clique de um botão, o Azure Cosmos DB permite-lhe dimensionar de forma elástica e independente o débito e o armazenamento em qualquer número de regiões geográficas do Azure. Oferece débito, latência, disponibilidade e garante a consistência com abrangentes [contratos de nível de serviço](https://aka.ms/acdbsla) (SLAs), que nenhum outro serviço da base de dados oferece.

![O Azure Cosmos DB é um serviço de base de dados de distribuição global da Microsoft com aumento horizontal elástico, baixa latência garantida, cinco modelos de consistência e SLAs abrangentes garantidos](./media/introduction/azure-cosmos-db.png)

O Azure Cosmos DB contém um motor de base de dados de escrita otimizada, regido por recursos e sem esquema que suporta nativamente vários modelos de dados: chave-valor, documentos, gráficos e colunas. Também suporta várias APIs para aceder aos dados incluindo [MongoDB](mongodb-introduction.md), [SQL do DocumentDB](documentdb-introduction.md), [Gremlin](graph-introduction.md) (pré-visualização) e [Tabelas do Azure](table-introduction.md) (pré-visualização), de forma extensível. 

O Azure Cosmos DB teve início em finais de 2010 para lidar com os pontos de tensão dos programados em aplicações de grande escala no interior da Microsoft. Uma vez que a criação de aplicações distribuídas globalmente não é um problema exclusivo da Microsoft, disponibilizámos o serviço externamente para todos os Programadores do Azure sob a forma do Azure DocumentDB. O Azure Cosmos DB é o próximo grande salto na evolução do DocumentDB e estamos agora a disponibilizá-lo para utilização. Como parte desta versão do Azure Cosmos DB, os clientes do DocumentDB (juntamente com os seus dados) são automaticamente clientes do Azure Cosmos DB. A transição é totalmente integrada e os clientes têm agora acesso a um leque mais abrangente de novas capacidades oferecidas pelo Azure Cosmos DB. 

<a id="capability-comparison" class="xliff"></a>

## Comparação das capacidades

O Azure Cosmos DB fornece as melhores capacidades das bases de dados relacionais e não relacionais.

| Capacidades | BDs relacionais | BDs não relacionais (NoSQL) |  Azure Cosmos DB |
| --- | --- | --- | --- |
| Distribuição global | x | x | ✓ Chave na mão, mais 30 regiões, multi-homing |
| Dimensionamento horizontal | x | ✓ | ✓ Dimensionamento do armazenamento e do débito independente | 
| Garantias de latência | x | ✓ | ✓ < 10 ms para leituras, < 15 ms para escritas em p99 | 
| Elevada disponibilidade | x | ✓ | ✓ Sempre ativo, compromissos PACELC, ativação pós-falha automática e manual |
| Modelo de dados + API | Relacional + SQL | Vários modelos e API OSS | Vários modelos + SQL + API OSS (mais em breve) |
| SLAs | ✓ | x | ✓ SLAs abrangentes para latência, débito, consistência e disponibilidade |

<a id="key-capabilities" class="xliff"></a>

## Principais capacidades
Como um serviço de base de dados de distribuição global, o Azure Cosmos DB fornece as seguintes capacidades para ajudar a criar aplicações escaláveis, de distribuição global e com elevada capacidade de resposta:

* [**Distribuição global de chave na mão**](#global-distribution)
    * A aplicação está imediatamente disponível para os seus utilizadores, em qualquer lugar. Agora, os dados também podem estar.
    * Não se preocupe com o hardware nem com a adição de nós, MVs ou núcleos. Basta apontar e clicar e os dados aparecem. 

* [**Vários modelos de dados e APIs populares para aceder e consultar os dados**](#data-models)
    * Suporte para vários modelos de dados, incluindo valor-chave, documentos, gráfico e colunas.
    * APIs extensíveis para Node.js, Java, .NET, .NET Core, Python e MongoDB.
    * SQL e Gremlin para consultas. 

* [**Dimensionamento elástico do débito e do armazenamento a pedido, em todo o mundo**](#horizontal-scale)
    * Dimensionamento d débito é fácil nas granularidades por [segundo](request-units.md) e [minuto](https://aka.ms/acdbrupm) e pode ser alterado sempre que pretender. 
    * Dimensionamento do armazenamento de forma [transparente e automática](partition-data.md) para abranger os requisitos de tamanho de forma permanente.

* [**Criação de aplicações com elevada capacidade de resposta e críticas para a missão**](#low-latency) 
    * Obtenha acesso aos seus dados com latências de milissegundo de dígito único no percentil 99, em qualquer parte do mundo. 

* [**Garantia de disponibilidade “sempre ativa”**](#high-availability)
    * 99,99% de disponibilidade numa única região.
    * Para maior disponibilidade, implemente em qualquer número de [regiões do Azure](https://azure.microsoft.com/regions).
    * [Simule uma falha](regional-failover.md) de uma ou mais regiões com garantia de zero perda de dados. 

* [**Escrita de aplicações de distribuição global, da forma correta**](#consistency)
    * [Cinco modelos de consistência](consistency-levels.md) para oferecer desde consistência forte semelhante ao SQL até consistência eventual semelhante ao NoSQL, e tudo o que está entre as duas. 
  
* [**Garantia de devolução do dinheiro**](#sla) 
    * Os seus dados chegam rápido ou recebe o seu dinheiro de volta. 
    * [Contratos de nível de serviço](https://aka.ms/acdbsla) para disponibilidade, latência, débito e consistência. 

* [**Sem gestão de esquema/índices de bases de dados**](#schema-free)
    * Deixe de ter de se preocupar em manter o esquema e os índices das bases de dados em sincronização com o esquema da aplicação. Não utilizamos esquemas. 

* [**Baixo custo de propriedade**](#tco)
    * Cinco a dez vezes [mais económica](https://aka.ms/documentdb-tco-paper) que uma solução não gerida.
    * Três vezes mais barata que a DynamoDB.

<a id="global-distribution"></a>

<a id="global-distribution" class="xliff"></a>

## Distribuição global
Os contentores do Azure Cosmos DB são distribuídos em duas dimensões: 

1. Dentro de uma determinada região, todos os recursos são particionados horizontalmente através das partições de recursos (distribuição local). 
2. Cada partição de recursos também é replicada nas regiões geográficas (distribuição global). 

![Diagrama a ilustrar a distribuição global do Azure Cosmos DB](./media/introduction/azure-cosmos-db-global-distribution.png) 

Quando o armazenamento e o débito têm de ser dimensionados, o Cosmos DB executa de forma transparente as operações de gestão das partições em todas as regiões. Independentemente do dimensionamento, da distribuição ou das falhas, o Cosmos DB continua a fornecer uma imagem de sistema único dos recursos de distribuição global. 

A distribuição global dos recursos no Cosmos DB é [chave na mão](distribute-data-globally.md). Em qualquer altura, com alguns cliques de botão (ou através de programação com uma chamada de API única), pode associar qualquer número de regiões geográficas à sua conta de base de dados. 

Independentemente da quantidade de dados ou do número de regiões, o Cosmos DB garante a cada região recentemente associada o início do processamento de pedidos dos clientes dentro de uma hora no percentil 99. Este procedimento é realizado pela paralelização da propagação e da cópia dos dados de todas as partições de recursos de origem com a região recentemente associada. Os clientes também podem remover uma região existente ou pegar numa região que foi previamente associada à respetiva conta de base de dados offline.

<a id="data-models"></a>
<a id="multi-model-multi-api-support" class="xliff"></a>

## Suporte para vários modelos e várias APIs
 O Azure Cosmos DB suporta de forma nativa vários modelos de dados, incluindo documentos, chave-valor, gráfico e família de colunas. O modelo de conteúdo principal do motor de base de dados do Cosmos DB baseia-se em ARS (sequência de registo atom). Os atoms são compostos por um pequeno conjunto de tipos primitivos como cadeia, booleano e número. Os registos são estruturas compostas por estes tipos. As sequências são matrizes constituídas por atoms, registos ou sequências. 
 
 O motor de base de dados pode converter e projetar de forma eficiente vários modelos de dados no modelo de dados baseado em ARS. O modelo de dados principal do Cosmos DB está nativamente acessível a partir de linguagens de programação de escrita dinâmica e pode ser exposto, tal como está, como JSON. 
 
 O serviço também suporta as APIs de bases de dados populares de acesso e consulta de dados. O motor de base de dados do Cosmos DB suporta [SQL do DocumentDB](documentdb-introduction.md), [MongoDB](mongodb-introduction.md), [Tabelas do Azure](table-introduction.md) (pré-visualização) e [Gremlin](graph-introduction.md) (pré-visualização). Pode continuar a criar aplicações através das APIs OSS populares e obter todos os benefícios de um serviço de base de dados de distribuição global e totalmente gerido que foi testado em cenários reais. 

<a id="horizontal-scale"></a>
<a id="horizontal-scaling-of-storage-and-throughput" class="xliff"></a>

## Dimensionamento horizontal do armazenamento e do débito
Todos os dados dentro de um contentor do Cosmos DB (por exemplo, uma coleção de documentos, tabela ou gráfico) estão particionados horizontalmente e são geridos de forma transparente pelas partições de recursos. Uma partição de recursos é um contentor consistente e de elevada disponibilidade dos dados particionados por uma [chave de partição especificada do cliente](partition-data.md). Fornece uma imagem de sistema única do conjunto de recursos que gere e é uma unidade de escalabilidade e distribuição fundamental. O Cosmos DB foi concebido para permitir dimensionar de forma elástica o débito com base nos padrões de tráfego da aplicação em regiões geográficas diferentes para suportar as cargas de trabalho flutuantes que variam tanto em geografia como em tempo. O serviço gere as partições de forma transparente sem comprometer a disponibilidade, a consistência, a latência ou o débito de um contentor do Cosmos DB.  
 
![O Azure Cosmos DB é dimensionável horizontalmente](./media/introduction/azure-cosmos-db-partitioning.png) 

Pode dimensionar de forma elástica o débito de um contentor do Azure Cosmos DB, pelo aprovisionamento do débito através de programação, com as [unidades por segundo (RU/s) do pedido](request-units.md). Internamente, o serviço gere de forma transparente as partições de recursos para distribuir o débito num contentor especificado. O Cosmos DB assegura que o débito está disponível para utilização em todas as regiões associadas ao contentor. O novo débito é aplicado dentro de cinco segundos após a alteração do valor de débito configurado. 

Pode aprovisionar o débito num contentor do Cosmos DB nas duas granularidades, por segundo e [por minuto (RU/m)](request-units-per-minute.md). O débito aprovisionado na granularidade por minuto é utilizado para gerir os picos inesperados na carga de trabalho que ocorre na granularidade por segundo. 

<a id="low-latency"></a>
<a id="low-latency-guarantees-at-the-99th-percentile" class="xliff"></a>

## Garantias de baixa latência no percentil 99
Como parte dos SLAs, o Cosmos DB garante baixa latência ponto a ponto no percentil 99 aos seus clientes. Para um item de 1 KB típico, o Cosmos DB garante a latência ponto a ponto das leituras abaixo dos 10 ms e das escritas indexadas abaixo dos 15 ms no percentil 99, dentro da mesma região do Azure. As latências medianas são significativamente inferiores (abaixo dos 5 ms).  Com um limite superior de processamento do pedidos em cada transação da base de dados, o Cosmos DB permite que os clientes distingam claramente entre as transações com latência elevada e uma base de dados indisponível.

<a id="high-availability"></a>
<a id="transparent-multi-homing-and-9999-high-availability" class="xliff"></a>

## Multi-homing transparente e disponibilidade elevada de 99,99%
Pode associar dinamicamente “prioridades” às regiões associadas à sua conta de base de dados do Azure Cosmos DB. As prioridades são utilizadas para direcionar os pedidos para regiões específicas, em caso de falhas regionais. No caso pouco provável de um desastre regional, o Cosmos DB faz automaticamente as ativações pós-falha por ordem de prioridade.

Para testar a disponibilidade ponto a ponto da aplicação, pode [acionar manualmente a ativação pós-falha](regional-failover.md) (taxa limitada a duas operações no espaço de uma hora). O Cosmos DB garante uma perda nula de dados durante as ativações pós-falha regionais manuais. No caso de um desastre regional, o Cosmos DB garante um limite superior na perda de dados durante a ativação pós-falha automática iniciada no sistema. Não precisa de voltar a implementar a aplicação após uma ativação pós-falha regional, dado que os SLAs ea disponibilidade são guardados no Azure Cosmos DB. 

Neste cenário, o Cosmos DB permite-lhe interagir com os recursos através dos pontos finais lógicos (região desconhecida) ou físicos (específicos da região). A primeira hipótese garante que a aplicação pode ser alojada em vários locais, de forma transparente, em caso de ativação pós-falha. A segunda hipótese proporciona um controlo detalhado da aplicação para redirecionar as leituras e escritas para regiões específicas. O Cosmos DB garante SLA com 99,99% de disponibilidade para cada conta de base de dados. As garantias de disponibilidade são desconhecidas em relação ao dimensionamento ( débito e armazenamento aprovisionados), ao número de regiões ou à distância geográfica entre regiões associadas a uma base de dados especificada. 

<a id="consistency"></a>
<a id="multiple-well-defined-consistency-models" class="xliff"></a>

## Vários modelos de consistência bem definida
As bases de dados comerciais distribuídas enquadram-se em duas categorias: bases de dados que não oferecem escolhas de consistência prováveis e bem definidas e bases de dados que oferecem duas opções de programação extremas (consistência forte versus eventual). A primeira opção sobrecarrega os programadores de aplicações com a minúcia dos seus protocolos de replicação e exige que haja compromissos difíceis entre consistência, disponibilidade, latência e débito. A segunda opção obriga a que se tenha de escolher entre dois extremos. Não obstante a abundância de pesquisa e propostas para mais de 50 modelos de consistência, a comunidade da base de dados distribuída não conseguiu comercializar níveis de consistência para além da consistência forte e da consistência eventual. 

O Cosmos DB permite-lhe escolher entre [cinco modelos de consistência bem definidos](consistency-levels.md) ao longo do espetro de consistência – forte, estagnação limitada, [sessão](http://dl.acm.org/citation.cfm?id=383631), prefixo consistente e eventual. 

![O Azure Cosmos DB proporciona vários modelos de consistência (flexíveis) bem definidos à escolha](media/introduction/azure-cosmos-db-consistency-levels.png)

A tabela seguinte ilustra as garantias específicas proporcionadas por cada nível de consistência.
 
**Níveis de Consistência e garantias**

| Nível de Consistência | Garantias |
| --- | --- |
| Forte | Transação Atómica |
| Estagnação Limitada | Prefixo Consistente. Tempo de desfasamento de leituras em escritas por prefixos k ou intervalo t |
| Sessão   | Prefixo Consistente. Leituras monotónicas, escritas monotónicas, leitura das próprias escritas, escrita de acordo com leituras |
| Prefixo Consistente | As atualizações devolvidas são alguns prefixos de todas as atualizações, sem intervalos |
| Eventual  | Leituras fora de ordem |

Pode configurar o nível predefinido de consistência na sua conta do Cosmos DB (e, mais tarde, substituir a consistência num pedido de leitura específico). Internamente, o nível predefinido de consistência aplica-se aos dados dentro dos conjuntos de partição que podem abranger várias regiões. 


<a id="sla"></a>
<a id="guaranteed-service-level-agreements" class="xliff"></a>

## Contratos de nível de serviço garantidos

O Cosmos DB é o primeiro serviço de base de dados gerido a oferecer 99,99% de [garantias de SLA ](https://aka.ms/acdbsla) relativamente a disponibilidade, débito, baixa latência e consistência.
* Disponibilidade: SLA com 99,99% de disponibilidade de tempo de atividade para cada uma das operações do painel de controlo e de dados.
* Débito: 99,99% de pedidos concluídos com êxito 
* Latência: 99,99% de latências <10 ms no percentil 99
* Consistência: 100% de pedidos de leitura irão cumprir a garantia de consistência para o nível de consistência pedido por si.


<a id="schema-free"></a>
<a id="schema-free" class="xliff"></a>

## Sem esquemas

As bases de dados relacionais e NoSQL obrigam-no a lidar com a gestão de esquemas e índices, controlo de versões e migração; tudo isto é um grande desafio numa configuração de distribuição global. Mas não se preocupe, o Cosmos DB faz com que este problema desapareça! Com o Cosmos DB, não precisa de gerir índices nem esquemas, lidar com controlo de versões de esquemas ou preocupar-se com o tempo de inatividade da aplicação durante a migração de esquemas. O motor de base de dados do Cosmos DB é totalmente sem esquema. Indexa automaticamente todos os dados que ingere sem necessidade de qualquer esquema ou índice e serve consultas muito rápidas. 

<a id="tco"></a>
<a id="low-cost-of-ownership" class="xliff"></a>

## Baixo custo de propriedade

 Tendo em conta todas as considerações de custo total de propriedade (TCO), os serviços cloud geridos como o Azure Cosmos DB pode ser cinco a dez vezes mais económico do que os OSS equivalentes em execução em máquinas virtuais ou no local. E o Azure Cosmos DB é duas a três vezes mais barato do que o DynamoDB para cargas de trabalho de elevado volume. Saiba mais no [documento técnico sobre o TCO](https://aka.ms/documentdb-tco-paper). 

<a id="next-steps" class="xliff"></a>

## Passos seguintes
Introdução ao Azure Cosmos DB com um dos nossos manuais de introdução:

* [Introdução à API DocumentDB do Azure Cosmos DB](create-documentdb-dotnet.md)
* [Introdução à API MongoDB do Azure Cosmos DB](create-mongodb-nodejs.md)
* [Introdução à Graph API do Azure Cosmos DB](create-graph-dotnet.md)
* [Introdução à API de Tabela do Azure Cosmos DB](create-table-dotnet.md)

