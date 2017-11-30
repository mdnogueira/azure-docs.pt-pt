---
title: "Níveis de desempenho de API do DocumentDB | Microsoft Docs"
description: "Saiba mais sobre como níveis de desempenho do DocumentDB API permitem-lhe reservar débito numa base por contentor."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 7dc21c71-47e2-4e06-aa21-e84af52866f4
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: mimig
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 62767163213383c577e74e0aa8fbd07f891cb694
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Extinguir os níveis de desempenho S1, S2 e S3

> [!IMPORTANT] 
> Os níveis de desempenho S1, S2 e S3 abordados neste artigo estão a ser extinto e já não estão disponíveis para novas contas de API do DocumentDB.
>

Este artigo fornece uma descrição geral dos níveis de desempenho S1, S2 e S3 e descreve a forma como as coleções que utilizam estes níveis de desempenho serão migradas para coleções de partições únicas de 2017. Depois de ler este artigo, poderá responder às seguintes questões:

- [Por que razão são o desempenho S1, S2 e S3 níveis vai ser reformado?](#why-retired)
- [Como coleções de partições únicas e coleções particionadas comparar com S1, S2, níveis de desempenho de S3?](#compare)
- [O que é necessário para garantir acesso ininterrupto aos meus dados?](#uninterrupted-access)
- [Como o meu coleção será alterado após a migração?](#collection-change)
- [Como os meus faturação será alterado depois de posso estou migrado para coleções de partições únicas?](#billing-change)
- [E se for necessário mais de 10 GB de armazenamento?](#more-storage-needed)
- [Posso alterar entre o S1, S2 e S3 níveis de desempenho antes da migração planeada?](#change-before)
- [Como posso saber quando foi migrada a minha coleção?](#when-migrated)
- [Como posso migrar de S1, S2, níveis de desempenho de S3 para coleções de partições únicas no meu próprio?](#migrate-diy)
- [Como estou posso afetado se sou um cliente EA?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Por que razão são o desempenho S1, S2 e S3 níveis vai ser reformado?

Os níveis de desempenho S1, S2 e S3 não oferecem a flexibilidade que oferta de coleções de API do DocumentDB. Com S1, S2, S3 níveis de desempenho, capacidade de débito e de armazenamento foram previamente definido e não foi possível oferecem elasticidade. BD do Azure do Cosmos oferece a capacidade para personalizar o débito e armazenamento, oferta muito mais flexibilidade na sua capacidade para dimensionar conforme alterar às suas necessidades.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Como coleções de partições únicas e coleções particionadas comparar com S1, S2, níveis de desempenho de S3?

A tabela seguinte compara as opções de débito e armazenamento disponíveis em coleções de partições únicas, coleções particionadas e S1, S2, níveis de desempenho de S3. Eis um exemplo de região dos EUA Leste 2:

|   |Coleção particionada|Coleção de partições únicas|S1|S2|S3|
|---|---|---|---|---|---|
|Débito máximo|Ilimitado|10 mil RU/s|250 RU/s|1 mil RU/s|2.5 mil RU/s|
|Débito mínimo|2.5 mil RU/s|400 RU/s|250 RU/s|1 mil RU/s|2.5 mil RU/s|
|Armazenamento máximo|Ilimitado|10 GB|10 GB|10 GB|10 GB|
|Preço (mensalmente)|Débito: $6 / 100 RU/s<br><br>Armazenamento: $0.25/ GB|Débito: $6 / 100 RU/s<br><br>Armazenamento: $0.25/ GB|$25 EUR|$50 EUR|$100 EUR|

Tem a um cliente EA? Se Sim, consulte [como estou posso afetado os se sou um cliente EA?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>O que é necessário para garantir acesso ininterrupto aos meus dados?

Nada, Cosmos DB processa a migração para si. Se tiver uma coleção S1, S2 ou S3, a coleção atual será migrada para uma coleção de partições únicas de 2017. 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Como o meu coleção será alterado após a migração?

Se tiver uma coleção de S1, será migrada para uma coleção de partições únicas com débito do 400 RU/s. 400 RU/s é o débito mais baixo disponível com coleções de partições únicas. No entanto, o custo de 400 RU/s de uma coleção de partições únicas é aproximadamente idêntica à foram pagante com a coleção de S1 e 250 RU/s –, pelo que não são pagar para o extra 150 RU/s disponíveis para si.

Se tiver uma coleção de S2, será migrada para uma coleção de partições únicas com 1 mil RU/s. Não verá nenhuma alteração para o nível de débito.

Se tiver uma coleção de S3, será migrada para uma coleção de partições únicas com 2,5 mil RU/s. Não verá nenhuma alteração para o nível de débito.

Em cada um nestes casos, depois da coleção é migrada, poderá personalizar o nível de débito ou dimensioná-lo e reduzir verticalmente conforme necessário para fornecer acesso de latência baixa aos seus utilizadores. Para alterar o nível de débito após a coleção foi migrada, basta abra a sua conta de base de dados do Cosmos no portal do Azure, clique em escala, escolha a sua coleção e, em seguida, ajustar o nível de débito, conforme mostrado na captura de ecrã seguinte:

![Como dimensionar débito no portal do Azure](./media/performance-levels/portal-scale-throughput.png)

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-im-migrated-to-the-single-partition-collections"></a>Como os meus faturação será alterado depois de posso estou a migrar para as coleções de partições únicas?

Partindo do princípio de tem 10 S1 coleções, 1 GB de armazenamento para cada região dos EUA leste, e migrar destas coleções S1 10 para 10 coleções de partições únicas em 400 RU/seg (o nível mínimo). A fatura irão ter o seguinte aspeto se mantiver as 10 coleções de partições únicas durante um mês completo:

![Como S1 preços para 10 coleções com 10 coleções utilizando preços para uma coleção de partições únicas](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-10-gb-of-storage"></a>E se for necessário mais de 10 GB de armazenamento?

Se tem uma coleção com um nível de desempenho S1, S2 ou S3 ou tem uma coleção de partições únicas, dos quais têm 10 GB de armazenamento disponível, pode utilizar a ferramenta de migração de dados de base de dados do Cosmos para migrar os dados para uma coleção particionada com armazenamento virtualmente ilimitados. Para obter informações sobre as vantagens de uma coleção particionada, consulte [divisão em partições e o dimensionamento do BD Azure Cosmos](documentdb-partition-data.md). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Posso alterar entre o S1, S2 e S3 níveis de desempenho antes da migração planeada?

Apenas as contas existentes com um desempenho S1, S2 e S3, poderá alterar e alterar camadas de nível de desempenho através do portal ou através de programação. Se alterar do S1, S3 ou S3 para uma coleção de partições únicas, não é possível regressar para os níveis de desempenho S1, S2 ou S3.

<a name="when-migrated"></a>

## <a name="how-will-i-know-when-my-collection-has-migrated"></a>Como posso saber quando foi migrada a minha coleção?

A migração irá ocorrer de 2017. Se tiver uma coleção que utiliza o S1, S2 ou S3 desempenho níveis, a equipa de BD do Cosmos irão contactá-lo por e-mail antes da migração direta. Assim que a migração estiver concluída, o portal do Azure irá mostrar que a sua coleção utiliza o preço padrão.

![Como confirmar a coleção foi migrada para o escalão de preço padrão](./media/performance-levels/portal-standard-pricing-applied.png)

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Como posso migrar de S1, S2, níveis de desempenho de S3 para coleções de partições únicas no meu próprio?

Pode migrar de níveis de desempenho S1, S2 e S3 para coleções de partições únicas no portal do Azure ou através de programação. Pode fazê-lo no seu próprio antes da migração planeada para tirar partido das opções disponíveis com coleções de partições únicas débito flexível ou, iremos migrar as coleções para si de 2017.

**Para migrar para coleções de partições únicas no portal do Azure**

1. No [ **portal do Azure**](https://portal.azure.com), clique em **Azure Cosmos DB**, em seguida, selecione a conta de base de dados do Cosmos a modificar. 
 
    Se **Azure Cosmos DB** é não no Jumpbar, clique em >, desloque-se para **bases de dados**, selecione **Azure Cosmos DB**e, em seguida, selecione a conta.  

2. No menu de recursos, sob **contentores**, clique em **escala**, selecione a coleção para modificar a partir da lista pendente e, em seguida, clique em **escalão de preço**. As contas utilizando o débito predefinido têm um escalão de preço de S1, S2 ou S3.  No **escolher o escalão de preço** página, clique em **padrão** para alterar a débito definido pelo utilizador e, em seguida, clique em **selecione** para guardar a alteração.

    ![Captura de ecrã da página de definições que mostra onde alterar o valor de débito](./media/performance-levels/change-performance-set-thoughput.png)

3. Volta a **escala** página, o **escalão de preço** é alterado para **padrão** e o **débito (RU/s)** é apresentada a caixa com uma predefinição valor de 400. Definir o débito entre 400 e 10 000 [unidades de pedido](request-units.md)/second (RU/s). O **fatura mensal estimada** na parte inferior das atualizações de página automaticamente para fornecer uma estimativa do custo mensal. 

    >[!IMPORTANT] 
    > Depois de guardar as alterações e mover para o escalão de preço padrão, não é possível reverter para os níveis de desempenho S1, S2 ou S3.

4. Clique em **guardar** para guardar as alterações.

    Se determinar que precisa de mais de débito (superior a 10.000 RU/s) ou o armazenamento mais (superior a 10 GB) pode criar uma coleção particionada. Para migrar uma coleção de partições únicas para uma coleção particionada, consulte [migrar de partição única para coleções particionadas](documentdb-partition-data.md#migrating-from-single-partition).

    > [!NOTE]
    > A alteração do S1, S2 ou S3 para Standard pode demorar até dois minutos.
    > 
    > 

**Para migrar para coleções de partições únicas utilizando o SDK .NET**

Outra opção para alterar os níveis de desempenho dos seus coleções é através dos SDKs do Azure Cosmos DB. Esta secção abrange apenas a alterar o desempenho de uma coleção de nível de utilizar o [API .NET do DocumentDB](documentdb-sdk-dotnet.md), mas o processo é semelhante para os nossos SDKs outros.

Eis um fragmento de código para alterar o débito de coleção para 5000 unidades de pedido por segundo:
    
```C#
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

Visite [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) para ver exemplos adicionais e saber mais sobre a nossa oferta métodos:

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Como estou posso afetado se sou um cliente EA?

Clientes EA estarão preços protegidos até ao fim do respetivo contrato atual.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre preços e gestão de dados com o Azure Cosmos DB, explore estes recursos:

1.  [A criação de partições de dados na base de dados do Cosmos](documentdb-partition-data.md). Compreenda a diferença entre o contentor de partições únicas e contentores particionadas, bem como sugestões na implementação de uma estratégia de criação de partições de dimensionamento de forma totalmente integrada.
2.  [Preços do cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Saiba mais sobre os custos de aprovisionamento de débito e o consumo de armazenamento.
3.  [Unidades de pedido](request-units.md). Compreenda o consumo de débito para tipos de operação diferentes, por exemplo, leitura, escrita, consulta.
