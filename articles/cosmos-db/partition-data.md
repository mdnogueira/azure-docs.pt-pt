---
title: "Criação de partições e o dimensionamento horizontal do BD Azure Cosmos | Microsoft Docs"
description: "Saiba mais sobre funciona como criação de partições de BD do Cosmos do Azure, como configurar a criação de partições e chaves de partição e como escolher a chave de partição adequado para a sua aplicação."
services: cosmos-db
author: arramac
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: cac9a8cd-b5a3-4827-8505-d40bb61b2416
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/06/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f7f5e2939ed09c0fbb4eb81f066075553376ff57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="partition-and-scale-in-azure-cosmos-db"></a>Partição e o dimensionamento do BD Azure Cosmos

[BD do Azure do Cosmos](https://azure.microsoft.com/services/cosmos-db/) é um serviço de base de dados globalmente distribuída, multimodel concebido para ajudar a alcançar um desempenho previsível e rápido. -Dimensiona de forma totalmente integrada, juntamente com a sua aplicação à medida que o que aumenta. Este artigo fornece uma descrição geral de como funciona para todos os dados de criação de partições modelos do BD Azure Cosmos. Também descreve como pode configurar a base de dados do Azure Cosmos contentores de forma eficaz dimensionar as suas aplicações.

Divisão em partições e chaves de partição são apresentadas neste Azure sexta-feira vídeo com autoria de Scott Hanselman e o Azure Cosmos BD Principal de engenharia Manager, Shireesh Thota:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-DocumentDB-Elastic-Scale-Partitioning/player]
> 

## <a name="partitioning-in-azure-cosmos-db"></a>A criação de partições no Azure Cosmos DB
BD do Cosmos do Azure, pode armazenar e consultar dados sem esquema com tempos de resposta de ordem de milissegundo em qualquer escala. BD do Azure do Cosmos fornece contentores para armazenar dados denominado *coleções* (para documentos), *gráficos*, ou *tabelas*. Contentores são recursos lógicos e podem abranger uma ou mais partições físicas ou servidores. O número de partições é determinado pelo Azure Cosmos DB com base no tamanho de armazenamento e o débito aprovisionado do contentor. Cada partição do BD Azure Cosmos tem uma quantidade fixa de armazenamento SSD de segurança associada e é replicada para elevada disponibilidade. Gestão de partição é totalmente gerida por base de dados do Azure Cosmos e não tem de escrever código complexo ou gerir as partições. Contentores do Cosmos BD do Azure são ilimitados em termos de armazenamento e débito. 

![A criação de partições de recursos](./media/introduction/azure-cosmos-db-partitioning.png) 

Criação de partições é transparente para a aplicação. BD do Azure do Cosmos suporta rápidas leituras e escritas, consultas, lógica transacional, níveis de consistência e controlo de acesso detalhada através de métodos/APIs a um recurso de contentor único. O serviço processa dados de distribuição em partições e o encaminhamento de pedidos de consulta para a partição à direita. 

Como funciona a criação de partições? Cada item tem de ter uma chave de partição e uma chave de linha, identificar exclusivamente. A chave de partição atua como uma partição lógica para os seus dados e fornece BD do Cosmos do Azure com um limite natural para distribui dados em partições. No brief, eis como criação de partições funciona do BD Azure Cosmos:

* Aprovisionar um contentor de base de dados do Azure Cosmos com `T` débito de pedidos/s.
* Nos bastidores, base de dados do Azure Cosmos Aprovisiona partições necessárias para servir `T` pedidos/s. Se `T` é superior ao débito máximo por partição `t`, em seguida, a base de dados do Azure Cosmos Aprovisiona `N`  =  `T/t` partições.
* BD do Azure do Cosmos aloca uniformemente across hashes de chaves de espaço de chave de partição a `N` partições. Por isso, cada partição (partição física) aloja valores de chave de partição de 1-N (partições lógicas).
* Quando uma partição física `p` atingir o limite de armazenamento, base de dados do Azure Cosmos perfeitamente divide `p` em duas novas partições `p1` e `p2`. Distribui-lo valores que correspondem a meio aproximadamente as chaves para cada uma das partições. Dividir a operação é invisível à sua aplicação.
* Da mesma forma, quando aprovisionar débito superior `t*N`, base de dados do Azure Cosmos divide uma ou mais das suas partições para suportar um maior débito.

A semântica para as chaves de partição é ligeiramente diferente para corresponder a semântica de cada API, conforme mostrado na seguinte tabela:

| API | Chave de partição | Chave de linha |
| --- | --- | --- |
| Azure Cosmos DB | caminho da chave de partição personalizado | fixo`id` | 
| MongoDB | Chave partilhada personalizada  | fixo`_id` | 
| Graph | propriedade de chave de partição personalizado | fixo`id` | 
| Tabela | fixo`PartitionKey` | fixo`RowKey` | 

BD do Azure do Cosmos utiliza a criação de partições com base em hash. Quando escreve um item, base de dados do Azure Cosmos codifica o valor da chave de partição e utiliza o resultado com hash para determinar que partição que pretende armazenar no item. BD do Azure do Cosmos armazena todos os itens com a mesma chave de partição na mesma partição física. A escolha da chave de partição é uma decisão importante que terá de fazer no momento da concepção. Tem de escolher um nome de propriedade que tenha uma vasta gama de valores e tem o mesmo padrões de acesso.

> [!NOTE]
> É uma melhor prática de ter uma chave de partição com vários valores distintos (centenas e os milhares, no mínimo).
>

Contentores do Cosmos BD do Azure podem ser criados como *fixo* ou *ilimitados*. Os contentores de tamanho fixo têm um limite máximo de 10 GB e 10 000 de RU/s débito. Algumas APIs que permitem que a chave de partição ser omitidas para contentores de tamanho fixo. Para criar um contentor como ilimitado, tem de especificar um débito mínimo de 2500 RU/s.

É uma boa ideia confirmar a forma como os seus dados são distribuídos em partições. Para verificar isto no portal, aceda à sua conta de base de dados do Azure Cosmos e clique em **métricas** no **monitorização** secção e, em seguida, no painel direito, clique em **armazenamento** separador para ver como os seus dados são partições na partição física diferente.

![A criação de partições de recursos](./media/partition-data/partitionkey-example.png)

A imagem à esquerda mostra o resultado de uma chave de partição inválido e a imagem correta mostra o resultado de uma chave de partição. Na imagem à esquerda, pode ver que os dados não são distribuídos uniformemente entre as partições. Deve esforçar-nos distribuir os seus dados pelo seu gráfico semelhante a imagem correta.

## <a name="partitioning-and-provisioned-throughput"></a>Débito aprovisionado e criação de partições
BD do Azure do Cosmos foi concebida para um desempenho previsível. Quando cria um contentor, reservar débito em termos de  *[unidades de pedido](request-units.md) (RU) por segundo*. Cada pedido está atribuído um custo de RU é proporcional para a quantidade de recursos do sistema, tais como CPU, memória e consumidas pela operação de e/s. Uma leitura de um documento de 1 KB com consistência de sessão consome 1 RU. Uma leitura é 1 RU independentemente do número de itens armazenados ou o número de pedidos simultâneos em execução ao mesmo tempo. Itens de maior requerem RUs superiores, dependendo do tamanho. Se souber o tamanho do seu entidades e o número de leituras que tiver de suportar para a sua aplicação, pode aprovisionar a quantidade exata de débito necessário para a aplicação do leia necessidades. 

> [!NOTE]
> Para alcançar o débito total do contentor, tem de escolher uma chave de partição permite-lhe distribuir uniformemente pedidos entre alguns valores de chave de partição distintos.
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="work-with-the-azure-cosmos-db-apis"></a>Trabalhar com o Azure Cosmos APIs de base de dados
Pode utilizar o portal do Azure ou a CLI do Azure para criar contentores e dimensioná-los em qualquer altura. Esta secção mostra como criar contentores e especifique a definição de chave de partição e débito em cada uma das APIs suportados.

### <a name="azure-cosmos-db-api"></a>API do Azure Cosmos DB
O exemplo seguinte mostra como criar um contentor (coleção), utilizando a API de BD do Cosmos do Azure. 

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

Pode ler um item (documento), utilizando o `GET` método na REST API ou utilizando `ReadDocumentAsync` dos SDKs.

```csharp
// Read document. Needs the partition key and the ID to be specified
DeviceReading document = await client.ReadDocumentAsync<DeviceReading>(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

### <a name="mongodb-api"></a>API do MongoDB
Com a API do MongoDB, pode criar uma coleção em partição horizontal através da sua ferramenta favorita, controlador ou SDK. Neste exemplo, utilizamos a Shell do Mongo para a criação de coleção.

Na Shell do Mongo:

```
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
Resultados:

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

### <a name="table-api"></a>API de Tabela

Com a API de tabela, especifique o débito para as tabelas na configuração appSettings para a sua aplicação.

```xml
<configuration>
    <appSettings>
      <!--Table creation options -->
      <add key="TableThroughput" value="700"/>
    </appSettings>
</configuration>
```

Em seguida, criar uma tabela ao utilizar o Table storage do Azure SDK. A chave de partição implicitamente é criada como o `PartitionKey` valor. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists();
```

Pode obter uma única entidade utilizando o seguinte fragmento:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
Para obter mais informações, consulte [desenvolver com a API de tabela](tutorial-develop-table-dotnet.md).

### <a name="graph-api"></a>Graph API

Com a Graph API, tem de utilizar o portal do Azure ou a CLI do Azure para criar contentores. Em alternativa, porque a base de dados do Azure Cosmos é multimodel, pode utilizar um dos outros modelos para criar e dimensionar o contentor do gráfico.

Pode ler qualquer vértice ou o Microsoft edge, utilizando a chave de partição e o ID no Gremlin. Por exemplo, para um gráfico com a região ("EUA") como a chave de partição e "Seattle" como a chave de linha, pode encontrar um vértice utilizando a seguinte sintaxe:

```
g.V(['USA', 'Seattle'])
```

Pode referenciar um limite utilizando a chave de partição e a chave de linha.

```
g.E(['USA', 'I5'])
```

Para obter mais informações, consulte [suporte Gremlin para a base de dados do Azure Cosmos](gremlin-support.md).


<a name="designing-for-partitioning"></a>
## <a name="design-for-partitioning"></a>Conceção para criação de partições
Para dimensionadas de forma eficaz com BD do Cosmos do Azure, terá de escolher uma chave de partição ao criar o contentor. Existem duas principais considerações sobre a escolha de uma chave de partição:

* **Limite da consulta e transações**. À sua escolha de chave de partição deve balancear a necessidade de ativar a utilização de transações com o requisito de distribuir as entidades por várias chaves de partição para garantir uma solução dimensionável. Em uma Alpine, pode definir a mesma chave de partição para todos os seus itens, mas esta opção pode limitar a escalabilidade da sua solução. Em outra Alpine, pode atribuir uma chave de partição exclusiva para cada item. Esta opção é altamente dimensionável, mas impede-o de utilizando transações entre documentos através de acionadores e procedimentos armazenados. Uma chave de partição ideal permite-lhe utilizar consultas eficiente e tem cardinalidade suficiente para garantir a sua solução escalável. 
* **Nenhum congestionamentos de desempenho e armazenamento**. É importante escolher uma propriedade que lhe permite escritas ser distribuída em vários valores distintos. Pedidos para a mesma chave de partição não podem exceder o débito de uma única partição e são limitados. Por isso, é importante escolher uma chave de partição não resulta num "frequente oportunidades" na sua aplicação. Uma vez que todos os dados para uma chave de partição única tem de ser armazenados dentro de uma partição, deve evitar chaves de partição com elevada volumes de dados para o mesmo valor. 

Vamos ver alguns cenários no mundo real e chaves de partição para cada:
* Se estiver a implementar um utilizador perfil back-end, o ID de utilizador é uma boa opção para a chave de partição.
* Se estiver a armazenar dados de IoT, por exemplo, estado do dispositivo, um ID de dispositivo é uma boa opção para a chave de partição.
* Se estiver a utilizar a base de dados do Azure Cosmos para registo de dados de séries de tempo, o ID de nome de anfitrião ou o processo é uma boa opção para a chave de partição.
* Se tiver uma arquitetura multi-inquilino, o ID de inquilino é uma boa opção para a chave de partição.

Em alguns casos, como perfis de utilizador e de IoT de utilização, a chave de partição pode ser o mesmo que o ID (chave do documento). Em outras pessoas, como os dados de séries de tempo, poderá ter uma chave de partição que é diferente do ID.

### <a name="partitioning-and-loggingtime-series-data"></a>Data de criação de partições e de registo /-série de tempo da
Um dos casos de utilização comum da base de dados do Azure Cosmos é telemetria e de registo. É importante escolher uma chave de partição, pois pode necessitar de leitura/escrita vasta volumes de dados. A opção depende as taxas de leitura e escrita e os tipos de consultas que pretende executar. Eis algumas sugestões sobre como escolher uma chave de partição:

* Se o caso de utilização envolve uma taxa pequena de gravações que acumular durante muito tempo e tem de consultar por intervalos de carimbos de data / hora e outros filtros, utilize um rollup do carimbo de hora. Por exemplo, uma boa abordagem é utilizar o data como uma chave de partição. Com esta abordagem, pode consultar ao longo de todos os dados de uma data a partir de uma única partição. 
* Se a carga de trabalho é escrita pesado, que é mais comum, utilize uma chave de partição não é baseada em carimbo de data / hora. Com esta abordagem, base de dados do Azure Cosmos pode distribuir escritas uniformemente entre várias partições. Um nome de anfitrião, o ID do processo, o ID de atividade ou outra propriedade com cardinalidade elevada Eis uma boa opção. 
* Outra abordagem é uma versão híbrida um onde tiver vários contentores, um para cada dia/mês, e a chave de partição é uma propriedade granular como o nome do anfitrião. Esta abordagem tem a vantagem que pode definir débito diferentes com base na janela de tempo. Por exemplo, o contentor para o mês atual está aprovisionado com um maior débito porque serve leituras e escritas. Meses anteriores são aprovisionadas com débito inferior porque servem apenas operações de leitura.

### <a name="partitioning-and-multitenancy"></a>Criação de partições e arquitetura "multitenancy"
Se estiver a implementar uma aplicação multi-inquilino através da utilização de base de dados do Azure Cosmos, existem dois padrões de populares: chave de uma partição por inquilino e um contentor por inquilino. Seguem-se a profissionais e contras para cada:

* **Chave de uma partição por inquilino**. Neste modelo, os inquilinos estão colocalizados dentro de um único contentor. Mas, consultas e inserções de itens dentro de um único inquilino podem executar uma única partição. Também pode implementar a lógica transacional em todos os itens dentro de um inquilino. Como vários inquilinos partilham um contentor, pode guardar os custos de armazenamento e débito por agrupamento de recursos para os inquilinos dentro de um único contentor, em vez de aprovisionamento headroom adicional para cada inquilino. A desvantagem é que não tem o isolamento de desempenho por inquilino. Os aumentos de desempenho/débito aplicam-se para o contentor inteiro versus aumenta direcionado para os inquilinos.
* **Um contentor por inquilino**. Neste modelo, cada inquilino tem a suas próprias contentor e reserva de desempenho por inquilino. Com a BD do Cosmos Azure aprovisionamento novos preços, este modelo é mais económico para aplicações multi-inquilino com alguns inquilinos.

Também pode utilizar uma abordagem de combinação/camadas de mensagens em fila que collocates inquilinos pequenos e migra inquilinos maior para os seus próprios contentor.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, fornecemos-lhe uma descrição geral dos conceitos e procedimentos recomendados para criação de partições com qualquer API de BD do Cosmos do Azure. 

* Saiba mais sobre [débito aprovisionado do BD Azure Cosmos](request-units.md).
* Saiba mais sobre [distribuição global do BD Azure Cosmos](distribute-data-globally.md).



