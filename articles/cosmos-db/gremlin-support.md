---
title: Suporte do Azure do Cosmos DB Gremlin | Microsoft Docs
description: "Saiba mais sobre o idioma de Gremlin do Apache TinkerPop, quais as funcionalidades e os passos e estão disponíveis do BD Azure Cosmos"
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
tags: 
ms.assetid: 6016ccba-0fb9-4218-892e-8f32a1bcc590
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 11/15/2017
ms.author: denlee
ms.openlocfilehash: 746cf8f88f84c81ff76340f2cfbfa11609c6483a
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="azure-cosmos-db-gremlin-graph-support"></a>Suporte de gráfico do Cosmos Gremlin de BD do Azure
BD do Azure do Cosmos suporta [do Apache Tinkerpop](http://tinkerpop.apache.org) idioma transversal, da graph [Gremlin](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps), que é uma API de gráfico para criar entidades do gráfico e efetuar operações de consulta do gráfico. Pode utilizar o idioma de Gremlin para criar entidades do gráfico (vértices e contornos), modifique propriedades dentro desses entidades, executar consultas e traversals e eliminar entidades. 

BD do Azure do Cosmos oferece funcionalidades preparada para empresa para bases de dados do gráfico. Isto inclui distribuição global, independente de dimensionamento do armazenamento e débito, latências previsíveis dígito milissegundo, indexação automática, SLA, leia a disponibilidade para contas de base de dados expansão dois ou mais regiões do Azure. Porque a base de dados do Azure Cosmos suporta TinkerPop/Gremlin, pode migrar facilmente aplicações escritas utilizando outra base de dados do gráfico sem ter de efetuar alterações de código. Além disso, em virtude suporte Gremlin, base de dados do Azure Cosmos perfeitamente integra-se com estruturas de análise TinkerPop ativado como [Apache Spark GraphX](http://spark.apache.org/graphx/). 

Neste artigo, vamos fornecem instruções rápida de Gremlin e enumerar as funcionalidades de Gremlin e passos que são suportados na pré-visualização do suporte da Graph API.

## <a name="gremlin-by-example"></a>Gremlin exemplo
Vamos utilizar um gráfico de exemplo para compreender a forma como as consultas podem ser expressos numa Gremlin. A figura seguinte mostra uma aplicação empresarial que gere dados sobre utilizadores, interesses e dispositivos sob a forma de um gráfico.  

![Base de dados de exemplo que mostra pessoas, dispositivos e interesses](./media/gremlin-support/sample-graph.png) 

Este gráfico tem os seguintes tipos de vértice (denominados "etiqueta" no Gremlin):

- Pessoas: o gráfico tem três pessoas, Round Robin, blogue e Bernardo
- Interesses: seus interesses, neste exemplo, jogos de futebol
- Dispositivos: os dispositivos que as pessoas utilização
- Sistemas operativos: os sistemas operativos de dispositivos

Iremos representam as relações entre estas entidades através de tipos/etiquetas de limite seguintes:

- Conheça: por exemplo, "blogue sabe Round Robin"
- Interessado: Para representar de interesses as pessoas no nosso gráfico, por exemplo, "Bernardo está interessado em futebol"
- RunsOS: Portátil executa o sistema operativo Windows
- Utiliza: Para representar o dispositivo uma pessoa que utiliza. Por exemplo, o Round Robin utiliza um telefone Motorola com o número de série 77

Vamos execute algumas operações em relação a este gráfico utilizando o [Gremlin consola](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console). Também pode efetuar estas operações utilizando controladores Gremlin na plataforma à sua escolha (Java, Node.js, Python ou .NET).  Vamos ver alguns exemplos para se familiarizar com a sintaxe antes vamos ver o que é suportado do BD Azure Cosmos.

Primeiro vamos ver CRUD. A seguinte instrução Gremlin insere o vertex "Blogue" no gráfico:

```
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Em seguida, a seguinte instrução Gremlin insere um limite de "sabe" entre blogue e Round Robin.

```
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

A seguinte consulta devolve "pessoa" vértices por ordem descendente dos respetivos nomes primeiro:
```
:> g.V().hasLabel('person').order().by('firstName', decr)
```

Onde gráficos shine quando necessita de responder a perguntas como "que sistemas operativos amigos de blogue utiliza?". Pode executar este transversal Gremlin simple para obter informações do gráfico:

```
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```
Agora vamos ver que BD do Cosmos do Azure fornece para programadores de Gremlin.

## <a name="gremlin-features"></a>Funcionalidades de gremlin
TinkerPop é uma norma que abrange uma vasta gama de tecnologias de gráfico. Por conseguinte, tem padrão terminologia para descrever o que são fornecidas por um fornecedor de gráfico. BD do Azure do Cosmos fornece uma simultaneidade persistente, elevada, a base de dados do gráfico gravável que pode ser particionado em vários servidores ou clusters. 

A tabela seguinte lista as funcionalidades de TinkerPop que são implementadas pelo Azure Cosmos DB: 

| Categoria | Implementação do Cosmos BD do Azure |  Notas | 
| --- | --- | --- |
| Funcionalidades de gráfico | Fornece a persistência e ConcurrentAccess em pré-visualização. Foi concebido para suportar transações | Métodos de computador podem ser implementados através do conector do Spark. |
| Funcionalidades de variável | Suporta booleano, número inteiro, Byte, faça duplo, Float, Integer, longa, cadeia | Suporta tipos primitivos, é compatível com tipos complexos através do modelo de dados |
| Funcionalidades de vértice | Suporta RemoveVertices MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty, RemoveProperty  | Suporta a criar, modificar e eliminar vértices |
| Funcionalidades de propriedade de vértice | StringIds UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Suporta a criar, modificar e eliminar as propriedades de vértice |
| Funcionalidades de limite | AddEges RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | Suporta a criar, modificar e eliminar contornos |
| Funcionalidades de propriedade de limite | Propriedades, BooleanValues ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Suporta a criar, modificar e eliminar as propriedades de limite |

## <a name="gremlin-wire-format-graphson"></a>Formato de wire gremlin: GraphSON

BD do Cosmos do Azure utiliza o [GraphSON formato](https://github.com/thinkaurelius/faunus/wiki/GraphSON-Format) quando devolver resultados do Gremlin operations. GraphSON é o formato de padrão Gremlin para representar vértices, margens e propriedades (único e com múltiplos valores propriedades) utilizando JSON. 

Por exemplo, o fragmento seguinte mostra uma representação GraphSON de um vértice *devolvida ao cliente* da base de dados do Azure Cosmos. 

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

As propriedades utilizadas por GraphSON para vértices são os seguintes:

| Propriedade | Descrição |
| --- | --- |
| ID | O ID do vértice. Tem de ser exclusivo (em combinação com o valor de _partition se aplicável) |
| Etiqueta | A etiqueta do vértice. Isto é opcional e utilizados para descrever o tipo de entidade. |
| tipo | Utilizada para distinguir vértices do gráfico não documentos |
| propriedades | Matriz de propriedades definida pelo utilizador associado o vértice. Cada propriedade pode ter vários valores. |
| _partition (configurável) | A chave de partição do vértice. Podem ser utilizados para aumentar horizontalmente gráficos para vários servidores |
| outE | Contém uma lista de saída extremidades de um vértice. Armazenar as informações de adjacency com vértice permite a execução rápida de traversals. Margens são agrupadas com base no respetivas etiquetas. |

E o limite contém as seguintes informações para ajudar a navegação para outras partes do gráfico.

| Propriedade | Descrição |
| --- | --- |
| ID | O ID de limite. Tem de ser exclusivo (em combinação com o valor de _partition se aplicável) |
| Etiqueta | A etiqueta de limite. Esta propriedade é opcional e utilizados para descrever o tipo de relação. |
| inV | Contém uma lista nos vértices para um limite. Armazenar as informações de adjacency com limite permite a execução rápida de traversals. Vértices são agrupados com base no respetivas etiquetas. |
| propriedades | Matriz de propriedades definida pelo utilizador associado limite. Cada propriedade pode ter vários valores. |

Cada propriedade pode armazenar vários valores dentro de uma matriz. 

| Propriedade | Descrição |
| --- | --- |
| valor | O valor da propriedade

## <a name="gremlin-partitioning"></a>A criação de partições de gremlin

Na base de dados do Azure Cosmos, gráficos são armazenados no contentores que podem ser dimensionados independentemente em termos de débito (expressado em normalizado pedidos por segundo) e de armazenamento. Cada contentor tem de definir opcional mas recomendado a propriedade de chave de partição que determina um limite de lógica de partição para dados relacionados. Cada vértice/contorno tem de ter um `id` propriedade que seja exclusiva para entidades dentro desse valor de chave de partição. Os detalhes são abordados em [criação de partições do BD Azure Cosmos](partition-data.md).

Operações de gremlin funcionam sem problemas em dados de gráfico que abrangem várias partições do BD Azure Cosmos. No entanto, é recomendado para escolher uma chave de partição para os gráficos que costuma é utilizada como um filtro em consultas, tem vários valores distintos e frequência semelhante de aceder a estes valores. 

## <a name="gremlin-steps"></a>Passos de gremlin
Agora vamos ver os passos de Gremlin suportados pelo Azure Cosmos DB. Para uma referência completa no Gremlin, consulte [TinkerPop referência](http://tinkerpop.apache.org/docs/current/reference).

| Passo | Descrição | Documentação de TinkerPop 3.2 | Notas |
| --- | --- | --- | --- |
| `addE` | Adiciona uma extremidade entre duas vértices | [passo addE](http://tinkerpop.apache.org/docs/current/reference/#addedge-step) | |
| `addV` | Adiciona um vértice ao gráfico | [passo addV](http://tinkerpop.apache.org/docs/current/reference/#addvertex-step) | |
| `and` | Garante que todos os traversals devolvem um valor | [e passo](http://tinkerpop.apache.org/docs/current/reference/#and-step) | |
| `as` | Modulator um passo para atribuir uma variável para o resultado de um passo | [como passo](http://tinkerpop.apache.org/docs/current/reference/#as-step) | |
| `by` | Um modulator passo utilizado com `group` e`order` | [por passo](http://tinkerpop.apache.org/docs/current/reference/#by-step) | |
| `coalesce` | Devolve o primeiro transversal que devolve um resultado | [Unir passo](http://tinkerpop.apache.org/docs/current/reference/#coalesce-step) | |
| `constant` | Devolve um valor constante. Utilizado com`coalesce`| [passo constante](http://tinkerpop.apache.org/docs/current/reference/#constant-step) | |
| `count` | Devolve a contagem da transversal | [passo de contagem](http://tinkerpop.apache.org/docs/current/reference/#count-step) | |
| `dedup` | Devolve os valores com os duplicados removidos | [passo de eliminação de duplicados](http://tinkerpop.apache.org/docs/current/reference/#dedup-step) | |
| `drop` | Ignora os valores (vértice/edge) | [remover passo](http://tinkerpop.apache.org/docs/current/reference/#drop-step) | |
| `fold` | Atos como um barreira que calcula a agregação de resultados| [passo de subconjuntos de validação](http://tinkerpop.apache.org/docs/current/reference/#fold-step) | |
| `group` | Grupos de valores com base nas etiquetas especificadas| [passo grupo](http://tinkerpop.apache.org/docs/current/reference/#group-step) | |
| `has` | Utilizado para filtrar as propriedades, vértices e contornos. Suporta `hasLabel`, `hasId`, `hasNot`, e `has` variantes. | [tem de passo](http://tinkerpop.apache.org/docs/current/reference/#has-step) | |
| `inject` | Inserir valores para uma transmissão em fluxo| [Inserir passo](http://tinkerpop.apache.org/docs/current/reference/#inject-step) | |
| `is` | Utilizado para efetuar um filtro utilizando uma expressão booleana | [é passo](http://tinkerpop.apache.org/docs/current/reference/#is-step) | |
| `limit` | Utilizado para limitar o número de itens na transversal| [passo de limite](http://tinkerpop.apache.org/docs/current/reference/#limit-step) | |
| `local` | Local encapsula num wrapper uma secção de transversal, semelhante a uma subconsulta | [passo local](http://tinkerpop.apache.org/docs/current/reference/#local-step) | |
| `not` | Utilizadas para produzir a negação de um filtro | [não passo](http://tinkerpop.apache.org/docs/current/reference/#not-step) | |
| `optional` | Devolve o resultado do transversal especificado se o gera um resultado senão devolve o elemento de chamada | [passo opcional](http://tinkerpop.apache.org/docs/current/reference/#optional-step) | |
| `or` | Garante que, pelo menos, um dos traversals devolve um valor | [passo ou](http://tinkerpop.apache.org/docs/current/reference/#or-step) | |
| `order` | Devolve resultados na sequência de ordenação especificada | [passo de sequência](http://tinkerpop.apache.org/docs/current/reference/#order-step) | |
| `path` | Devolve o caminho completo da transversal | [passo de caminho](http://tinkerpop.apache.org/docs/current/reference/#path-step) | |
| `project` | As propriedades como um mapa de projetos | [passo do projeto](http://tinkerpop.apache.org/docs/current/reference/#project-step) | |
| `properties` | Devolve as propriedades para as etiquetas especificadas | [passo de propriedades](http://tinkerpop.apache.org/docs/current/reference/#properties-step) | |
| `range` | Filtros para o intervalo de valores especificado| [passo de intervalo](http://tinkerpop.apache.org/docs/current/reference/#range-step) | |
| `repeat` | Repete o passo para o número de vezes especificado. Utilizado para criar ciclos | [Repita o passo](http://tinkerpop.apache.org/docs/current/reference/#repeat-step) | |
| `sample` | Utilizado para resultados de exemplo a partir da transversal | [passo de exemplo](http://tinkerpop.apache.org/docs/current/reference/#sample-step) | |
| `select` | Utilizado para resultados de projeto a partir da transversal |  [Selecione o passo](http://tinkerpop.apache.org/docs/current/reference/#select-step) | |
| `store` | Utilizado para bloquear não agregados da transversal | [passo de arquivo](http://tinkerpop.apache.org/docs/current/reference/#store-step) | |
| `tree` | Caminhos de agregação de um vértice para uma árvore | [passo de árvore](http://tinkerpop.apache.org/docs/current/reference/#tree-step) | |
| `unfold` | Unroll um iterator como um passo| [unfold passo](http://tinkerpop.apache.org/docs/current/reference/#unfold-step) | |
| `union` | Intercalar os resultados de vários traversals| [passo Union](http://tinkerpop.apache.org/docs/current/reference/#union-step) | |
| `V` | Inclui os passos necessários para traversals entre vértices e contornos `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV` , `bothV`, e `otherV` para | [passos de vértice](http://tinkerpop.apache.org/docs/current/reference/#vertex-steps) | |
| `where` | Utilizado para filtrar os resultados da transversal. Suporta `eq`, `neq`, `lt`, `lte`, `gt`, `gte`, e `between` operadores  | [onde passo](http://tinkerpop.apache.org/docs/current/reference/#where-step) | |

O motor com otimização de escrita da DB Cosmos do Azure suporta a indexação automática de todas as propriedades dentro de vértices e contornos por predefinição. Por conseguinte, a consulta com filtros, consultas, ordenação, o intervalo ou os agregados no qualquer propriedade são processados desde o índice e servidos de forma eficiente. Para obter mais informações sobre como indexação funciona do BD Azure Cosmos, consulte o nosso documento no [desconhecidas do esquema de indexação](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

## <a name="next-steps"></a>Passos Seguintes
* Introdução à criação de uma aplicação de gráfico [utilizando os nossos SDKs](create-graph-dotnet.md) 
* Saiba mais sobre [suporte do Azure Cosmos base de dados gráfico](graph-introduction.md)
