---
title: 'Tutorial do Azure Cosmos DB: criar, consultar e percorrer na consola Gremlin | Microsoft Docs'
description: "Um guia de introdução do Azure Cosmos DB para criar vértices, margens e consultas com a Azure Cosmos DB Graph API."
services: cosmosdb
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: bf08e031-718a-4a2a-89d6-91e12ff8797d
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: terminal
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fb27ba1a70959ba92fbd021e9e42438081000e45
ms.contentlocale: pt-pt
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cosmos-db-create-query-and-traverse-a-graph-in-the-gremlin-console"></a>Azure Cosmos DB: criar, consultar e percorrer gráficos na consola Gremlin

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB. 

Este guia de introdução demonstra como criar uma conta, uma base de dados e um gráfico (contentor) do Azure Cosmos DB com o portal do Azure e, depois, utilizar a [consola Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) para trabalhar com dados da Graph API (pré-visualização). Neste tutorial, vai criar e consultar vértices e margens, atualizar uma propriedade de vértice, consultar vértices, percorrer o gráfico e eliminar um vértice.

![Azure Cosmos DB a partir da consola Apache Gremlin](./media/create-graph-gremlin-console/gremlin-console.png)

A consola Gremlin é baseada em Groovy/Java e é executada em Linux, Mac e Windows. Pode transferi-la no [site Apache TinkerPop](https://www.apache.org/dyn/closer.lua/tinkerpop/3.2.4/apache-tinkerpop-gremlin-console-3.2.4-bin.zip).

## <a name="prerequisites"></a>Pré-requisitos

Precisa de uma subscrição do Azure para criar uma conta do Azure Cosmos DB neste guia de introdução.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Também tem de instalar a [consola Gremlin](http://tinkerpop.apache.org/). Utilize a versão 3.2.4 ou superior.

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmosdb-create-dbaccount-graph](../../includes/cosmosdb-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Adicionar um gráfico

[!INCLUDE [cosmosdb-create-graph](../../includes/cosmosdb-create-graph.md)]

## <a id="ConnectAppService"></a>Ligar ao seu serviço de aplicações
1. Antes de iniciar a consola Gremlin, crie ou modifique o ficheiro de configuração *remote-secure.yaml* no seu diretório *apache-tinkerpop-gremlin-console-3.2.4/conf*.
2. Preencha as configurações de *anfitrião*, *porta*, *nome de utilizador*, *palavra-passe*, *connectionPool* e *serializador*:

    Definição|Valor sugerido|Descrição
    ---|---|---
    Anfitriões|***.graphs.azure.com|O URI do seu serviço de gráficos, que pode obter no portal do Azure
    Porta|443|Defina como 443
    Nome de utilizador|*O seu nome de utilizador*|O recurso com a forma `/dbs/<db>/colls/<coll>`.
    Palavra-passe|*A chave mestra principal*|A chave mestra principal do Azure Cosmos DB
    ConnectionPool|{enableSsl: true}|A definição de conjunto de ligações para SSL
    Serializador|{ className:org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|Defina como este valor

3. No terminal, execute *bin/gremlin.bat* ou *bin/gremlin.sh* para iniciar a [consola Gremlin](http://tinkerpop.apache.org/docs/3.2.4/tutorials/getting-started/).
4. No terminal, execute *:remote connect tinkerpop.server conf/remote-secure.yaml* para ligar ao seu serviço de aplicações.

Ótimo! Agora que concluímos a configuração, comecemos a executar alguns comandos da consola.

## <a name="create-vertices-and-edges"></a>Criar vértices e margens

Vamos começar por adicionar quatro vértices de pessoas, para *Thomas*, *Mary Kay*, *Robin* e *Ben*.

Entrada (Thomas):

```
:> g.addV('person').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Saída:

```
==>[id:1eb91f79-94d7-4fd4-b026-18f707952f21,label:person,type:vertex,properties:[firstName:[[id:ec5fcfbe-040e-48c3-b961-31233c8b1801,value:Thomas]],lastName:[[id:86e5b580-0bca-4bc2-bc53-a46f92c1a182,value:Andersen]],age:[[id:2caeab3c-c66d-4098-b673-40a8101bb72a,value:44]]]]
```
Entrada (Mary Kay):

```
:> g.addV('person').property('firstName', 'Mary Kay').property('lastName', 'Andersen').property('age', 39)
```

Saída:

```
==>[id:899a9d37-6701-48fc-b0a1-90950be7e0f4,label:person,type:vertex,properties:[firstName:[[id:c79c5599-8646-47d1-9a49-3456200518ce,value:Mary Kay]],lastName:[[id:c1362095-9dcc-479d-ab21-86c1b6d4ffc1,value:Andersen]],age:[[id:0b530408-bfae-4e8f-98ad-c160cd6e6a8f,value:39]]]]
```

Entrada (Robin):

```
:> g.addV('person').property('firstName', 'Robin').property('lastName', 'Wakefield')
```

Saída:

```
==>[id:953aefd9-5a54-4033-9b3a-d4dc3049f720,label:person,type:vertex,properties:[firstName:[[id:bbda02e0-8a96-4ca1-943e-621acbb26824,value:Robin]],lastName:[[id:f0291ad3-05a3-40ec-aabb-6538a7c331e3,value:Wakefield]]]]
```

Entrada (Ben):

```
:> g.addV('person').property('firstName', 'Ben').property('lastName', 'Miller')
```

Saída:

```
==>[id:81c891d9-beca-4c87-9009-13a826c9ed9a,label:person,type:vertex,properties:[firstName:[[id:3a3b53d3-888c-46da-bb54-1c42194b1e18,value:Ben]],lastName:[[id:48c6dd50-79c4-4585-ab71-3bf998061958,value:Miller]]]]
```

Em seguida, vamos adicionar margens para as relações entre estas quatro pessoas.

Entrada (Thomas -> Mary Kay):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Mary Kay'))
```

Saída:

```
==>[id:c12bf9fb-96a1-4cb7-a3f8-431e196e702f,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:0d1fa428-780c-49a5-bd3a-a68d96391d5c,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Entrada (Thomas -> Robin):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Robin'))
```

Saída:

```
==>[id:58319bdd-1d3e-4f17-a106-0ddf18719d15,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:3e324073-ccfc-4ae1-8675-d450858ca116,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Entrada (Robin -> Ben):

```
:> g.V().hasLabel('person').has('firstName', 'Robin').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Ben'))
```

Saída:

```
==>[id:889c4d3c-549e-4d35-bc21-a3d1bfa11e00,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:40fd641d-546e-412a-abcc-58fe53891aab,outV:3e324073-ccfc-4ae1-8675-d450858ca116]
```

## <a name="update-a-vertex"></a>Atualizar um vértice

Vamos atualizar o vértice *Thomas* com uma nova idade de *45*.

Entrada:
```
:> g.V().hasLabel('person').has('firstName', 'Thomas').property('age', 45)
```
Saída:

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

## <a name="query-your-graph"></a>Consultar o gráfico

Agora, vamos executar uma variedade de consultas no seu gráfico.

Em primeiro lugar, vamos tentar uma consulta com um filtro para devolver apenas as pessoas com mais de 40 anos.

Entrada (consulta de filtro):

```
:> g.V().hasLabel('person').has('age', gt(40))
```

Saída:

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

Depois, vamos projetor o nome próprio das pessoas que têm mais de 40 anos.

Entrada (consulta de filtro + projeção):

```
:> g.V().hasLabel('person').has('age', gt(40)).values('firstName')
```

Saída:

```
==>Thomas
```

## <a name="traverse-your-graph"></a>Percorrer o gráfico

Vamos percorrer o gráfico para devolver todos os amigos do Thomas.

Entrada (amigos do Thomas):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person')
```

Saída: 

```
==>[id:f04bc00b-cb56-46c4-a3bb-a5870c42f7ff,label:person,type:vertex,properties:[firstName:[[id:14feedec-b070-444e-b544-62be15c7167c,value:Mary Kay]],lastName:[[id:107ab421-7208-45d4-b969-bbc54481992a,value:Andersen]],age:[[id:4b08d6e4-58f5-45df-8e69-6b790b692e0a,value:39]]]]
==>[id:91605c63-4988-4b60-9a30-5144719ae326,label:person,type:vertex,properties:[firstName:[[id:f760e0e6-652a-481a-92b0-1767d9bf372e,value:Robin]],lastName:[[id:352a4caa-bad6-47e3-a7dc-90ff342cf870,value:Wakefield]]]]
```

Em seguida, vamos obter a próxima camada de vértices. Percorra o gráfico para devolver todos os amigos dos amigos do Thomas.

Entrada (amigos dos amigos do Thomas):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```
Saída:

```
==>[id:a801a0cb-ee85-44ee-a502-271685ef212e,label:person,type:vertex,properties:[firstName:[[id:b9489902-d29a-4673-8c09-c2b3fe7f8b94,value:Ben]],lastName:[[id:e084f933-9a4b-4dbc-8273-f0171265cf1d,value:Miller]]]]
```

## <a name="drop-a-vertex"></a>Eliminar um vértice

Vamos agora eliminar um vértice da base de dados do gráfico.

Entrada (eliminar o vértice do Robin):

```
:> g.V().hasLabel('person').has('firstName', 'Robin').drop()
```

## <a name="clear-your-graph"></a>Limpar o gráfico

Por fim, vamos limpar todos os vértices e margens da base de dados.

Entrada:

```
:> g.V().drop()
```

Parabéns! Concluiu este tutorial do Azure Cosmos DB: Graph API!

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se não pretender continuar a utilizar esta aplicação, elimine todos os recursos criados com este guia de introdução no portal do Azure com os seguintes passos:  

1. No menu do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome de recurso que criou. 
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

No guia de introdução, aprendeu a criar uma conta do Azure Cosmos DB, a criar um gráfico com o Data Explorer, a criar vértices e margens e a percorrer o gráfico com a consola Gremlin. Agora, pode criar consultas mais complexas e implementar lógica poderosa para percorrer gráficos com Gremlin. 

> [!div class="nextstepaction"]
> [Query using Gremlin](tutorial-query-graph.md) (Utilizar Gremlin para consultar)

