---
title: "Ativação pós-falha na base de dados do Azure Cosmos | Microsoft Docs"
description: "Saiba mais sobre como manual e automático funciona de ativação pós-falha com o Azure Cosmos DB."
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 446e2580-ff49-4485-8e53-ae34e08d997f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 93a9bf568b1047e1af4e7825c3ca99bf11945560
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="automatic-regional-failover-for-business-continuity-in-azure-cosmos-db"></a>Ativação pós-falha regional automática para a continuidade do negócio do BD Azure Cosmos
BD do Azure do Cosmos simplifica a distribuição dos dados global por de oferta totalmente gerido, [contas de base de dados de multirregião](distribute-data-globally.md) que fornecem limpar responsabilidades entre consistência, disponibilidade e desempenho, tudo com garantias correspondentes. Contas do cosmos DB oferecem elevada disponibilidade, latências de ms único dígito, [níveis de consistência bem definidos](consistency-levels.md), ativação pós-falha regional transparente com APIs multi homing e a capacidade e dimensionar débito e armazenamento no globo. 

BD do cosmos suporta explícito e política orientadas pelas ativações pós-falha que permitem controlar o comportamento do sistema de ponto a ponto em caso de falhas. Neste artigo, vamos ver:

* Como funcionam as ativações pós-falha manual na base de dados do Cosmos?
* Como trabalho as ativações pós-falha automática na base de dados do Cosmos e o que acontece quando uma data center ficar inativo?
* Como pode utilizar as ativações pós-falha manual em arquiteturas de aplicações?

Pode também saber sobre as ativações pós-falha regional neste Azure sexta-feira vídeo com autoria de Scott Hanselman e o Gestor de engenharia do Principal Karthik Raman.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Planet-Scale-NoSQL-with-DocumentDB/player]  

## <a id="ConfigureMultiRegionApplications"></a>Configurar aplicações de multirregião
Antes de Iremos aprofundar modos de ativação pós-falha, vamos ver como pode configurar uma aplicação para tirar partido de disponibilidade de multirregião e sejam resilientes face as ativações pós-falha regional.

* Em primeiro lugar, implementar a aplicação em várias regiões
* Para garantir o acesso de latência baixa de cada região a aplicação for implementada, configure o correspondente [lista de regiões preferencial](https://msdn.microsoft.com/library/microsoft.azure.documents.client.connectionpolicy.preferredlocations.aspx#P:Microsoft.Azure.Documents.Client.ConnectionPolicy.PreferredLocations) para cada região através de uma dos SDKs suportados.

O fragmento seguinte mostra como iniciar uma aplicação de multirregião. Aqui, a conta de base de dados do Azure Cosmos `contoso.documents.azure.com` está configurado com duas regiões - EUA oeste e Europa do Norte. 

* A aplicação é implementada na região EUA oeste (utilizando os serviços de aplicações do Azure por exemplo) 
* Configurado com `West US` como a região preferencial primeiro de latência baixa lê
* Configurado com `North Europe` como a região preferencial segundo (para elevada disponibilidade durante falhas regionais)

Na API do DocumentDB, esta configuração se pareça com o seguinte fragmento:

```cs
ConnectionPolicy usConnectionPolicy = new ConnectionPolicy 
{ 
    ConnectionMode = ConnectionMode.Direct,
    ConnectionProtocol = Protocol.Tcp
};

usConnectionPolicy.PreferredLocations.Add(LocationNames.WestUS);
usConnectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

DocumentClient usClient = new DocumentClient(
    new Uri("https://contosodb.documents.azure.com"),
    "memf7qfF89n6KL9vcb7rIQl6tfgZsRt5gY5dh3BIjesarJanYIcg2Edn9uPOUIVwgkAugOb2zUdCR2h0PTtMrA==",
    usConnectionPolicy);
```

A aplicação for também implementada na região Europa do Norte com a ordem das regiões preferenciais invertido. Ou seja, a região Europa do Norte especificada pela primeira vez para leituras de latência baixa. Em seguida, a região EUA oeste está especificada como a região preferencial segundo para elevada disponibilidade durante falhas regionais.

O diagrama de arquitetura seguinte mostra uma implementação de aplicação de multirregião onde a BD do Cosmos e a aplicação estão configurados para estar disponíveis em quatro regiões geográficas do Azure.  

![Implementação de aplicação distribuída globalmente com base de dados do Azure Cosmos](./media/regional-failover/app-deployment.png)

Agora, vamos ver como o serviço de base de dados do Cosmos processa falhas regionais através de ativações pós-falha automáticas. 

## <a id="AutomaticFailovers"></a>Ativações pós-falha automáticas
No evento raro de uma falha regional do Azure ou a falha do Centro de dados, base de dados do Cosmos aciona automaticamente as ativações pós-falha de todas as contas de base de dados do Cosmos com uma presença na região afetada. 

**O que acontece se uma região leitura tem uma falha?**

As contas de BD do cosmos com uma região de leitura de uma das regiões do afetados são automaticamente desligadas da respetiva região de escrita e marcada como offline. Os SDKs de BD do Cosmos implementa um protocolo de deteção regionais que permite-lhes para detetar automaticamente quando uma região estiver disponível e redirecionamento de leitura de chamadas para a região disponível seguinte na lista a região preferencial. Se nenhuma das regiões na lista a região preferencial estiver disponível, as chamadas automaticamente revertam para a região de escrita atual. Não são necessárias alterações no código da aplicação para processar as ativações pós-falha regional. Durante este processo completo, garantias de consistência continuam a ser cumpridas pela base de dados do Cosmos.

![Falhas de região de leitura do BD Azure Cosmos](./media/regional-failover/read-region-failures.png)

Depois da região afetada recupera a partir da interrupção, todas as contas de base de dados do Cosmos afetadas na região são recuperadas automaticamente pelo serviço. Contas do cosmos DB que tinham uma região de leitura na região afetada, em seguida, sincronizar com a região de escrita atual e automaticamente ativar online. Os SDKs de BD do Cosmos detetar a disponibilidade da região do novo e avaliar se a região deve ser selecionada como a região de leitura atual com base na lista a região preferencial configuradas pela aplicação. Leituras subsequentes são redirecionadas para a região recuperada sem necessidade de alterações ao código da aplicação.

**O que acontece se uma região de escrita tem uma falha?**

Se a região afetada é a região atual de escrita e ativação pós-falha automática está ativada para a conta de base de dados do Azure Cosmos, em seguida, a região é marcada automaticamente como offline. Em seguida, uma região alternativa é promovida como a região de escrita para a conta de base de dados do Azure Cosmos afetada. Pode ativar a ativação pós-falha automática e controlar totalmente a ordem de seleção de região para as contas de base de dados do Azure Cosmos através do portal do Azure ou [programaticamente](https://docs.microsoft.com/rest/api/documentdbresourceprovider/databaseaccounts#DatabaseAccounts_FailoverPriorityChange). 

![Prioridades de ativação pós-falha para a base de dados do Azure Cosmos](./media/regional-failover/failover-priorities.png)

Durante as ativações pós-falha automáticas, base de dados do Azure Cosmos escolhe automaticamente a próxima escrever região para uma conta de base de dados do Azure Cosmos determinada com base na ordem de prioridade especificado. As aplicações podem utilizar a propriedade de WriteEndpoint da classe de DocumentClient para detetar a alteração na região de escrita.

![Falhas de região de escrita na base de dados do Azure Cosmos](./media/regional-failover/write-region-failures.png)

Depois da região afetada recupera a partir da interrupção, todas as contas de base de dados do Cosmos afetadas na região são recuperadas automaticamente pelo serviço. 

* Dados presentes na região de escrita anterior que não foi replicada para ler regiões durante a interrupção for publicados como um feed de conflito. Aplicações podem ler o feed de conflito, resolva os conflitos com base na lógica específica da aplicação e repetição de escrita de dados atualizados para a conta de base de dados do Azure Cosmos conforme apropriado. 
* A região de escrita anterior é recriada como uma região de leitura e colocada online automaticamente. 
* Pode reconfigurar a região de leitura que foi colocado online automaticamente como a região de escrita ao efetuar uma ativação pós-falha manual através do portal do Azure ou [programaticamente](https://docs.microsoft.com/rest/api/documentdbresourceprovider/databaseaccounts#DatabaseAccounts_CreateOrUpdate).

O fragmento de código seguinte ilustra como processar conflitos depois da região afetada recupera a partir da interrupção.

```cs
string conflictsFeedContinuationToken = null;
do
{
    FeedResponse<Conflict> conflictsFeed = client.ReadConflictFeedAsync(collectionLink,
        new FeedOptions { RequestContinuation = conflictsFeedContinuationToken }).Result;

    foreach (Conflict conflict in conflictsFeed)
    {
        Document doc = conflict.GetResource<Document>();
        Console.WriteLine("Conflict record ResourceId = {0} ResourceType= {1}", conflict.ResourceId, conflict.ResourceType);

        // Perform application specific logic to process the conflict record / resource
    }

    conflictsFeedContinuationToken = conflictsFeed.ResponseContinuation;
} while (conflictsFeedContinuationToken != null);
```

## <a id="ManualFailovers"></a>Ativações pós-falha manual

Para além das ativações pós-falha automáticas, a região atual de escrita de uma conta de base de dados do Cosmos especificada pode ser manualmente alterada dinamicamente para uma das regiões leitura existentes. As ativações pós-falha manual podem ser iniciadas através do portal do Azure ou [programaticamente](https://docs.microsoft.com/rest/api/documentdbresourceprovider/databaseaccounts#DatabaseAccounts_CreateOrUpdate). 

Certifique-se das ativações pós-falha manual **zero perda de dados** e **zero disponibilidade** perda e transições de estado de escrita de transferência do antigo escrever região para a nova para a conta de base de dados do Cosmos especificada. Como em ativações pós-falha automáticas, o SDK de BD do Cosmos automaticamente processa as alterações de região de escrita durante as ativações pós-falha manual e assegura que as chamadas automaticamente são redirecionadas para a região de escrita de novo. Sem alterações de configuração ou de código são necessários na sua aplicação para gerir as ativações pós-falha. 

![Ativações pós-falha manual do BD Azure Cosmos](./media/regional-failover/manual-failovers.png)

Alguns dos cenários comuns em que a ativação pós-falha manual pode ser útil são:

**Siga o modelo de relógio**: se as suas aplicações tem padrões de tráfego previsível, com base na hora do dia, periodicamente pode alterar o estado de escrita para a região geográfica mais ativa com base na hora do dia.

**A atualização do serviço**: determinada implementação de aplicação distribuída globalmente poderá envolver o método redirecionamento de tráfego para a região diferente através do Gestor de tráfego durante a respetiva atualização de serviço planeada. Tipo implementação de aplicação agora pode utilizar a ativação pós-falha manual para manter o estado de escrita para a região onde existe vai ser tráfego Active Directory durante o período de atualização de serviço.

**Continuidade do negócio e recuperação após desastre (BCDR) e simulações de elevada disponibilidade e recuperação após desastre (HADR)**: a maioria das aplicações da empresa incluem testes de continuidade do negócio como parte do seu processo de desenvolvimento e versão. BCDR e HADR testar é frequentemente um passo importante certificações de conformidade e a disponibilidade do serviço guaranteeing no caso de falhas regionais. Pode testar a preparação BCDR das suas aplicações que utilizam Cosmos DB para o armazenamento ao acionar uma ativação pós-falha manual da sua conta de base de dados do Cosmos e/ou adicionar e remover dinamicamente uma região.

Neste artigo, vamos rever o trabalho de ativações pós-falha de forma manual e automático na base de dados do Cosmos e como pode configurar as contas de base de dados do Cosmos e aplicações para ser globalmente disponível. Utilizando o suporte de replicação global do Cosmos DB, pode melhorar a latência de ponto a ponto e certifique-se de que estão altamente disponíveis, mesmo em caso de falhas de região. 

## <a id="NextSteps"></a>Passos Seguintes
* Saiba mais sobre como base de dados do Cosmos suporta [distribuição global](distribute-data-globally.md)
* Saiba mais sobre [global consistência com a base de dados do Azure Cosmos](consistency-levels.md)
* Desenvolver com várias regiões através da BD do Azure Cosmos [API do DocumentDB](../cosmos-db/tutorial-global-distribution-documentdb.md)
* Saiba como compilar [arquiteturas de escritor de multirregião](multi-region-writers.md) com o Azure DocumentDB

