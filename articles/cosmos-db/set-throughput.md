---
title: "Débito de aprovisionamento para a base de dados do Azure Cosmos | Microsoft Docs"
description: "Saiba como definir o débito de base de dados do Azure Cosmos containsers, coleções, gráficos e tabelas."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2017
ms.author: mimig
ms.openlocfilehash: d541bb19ba7e5ecb44c9fe91b1e232d4d9c2170e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="set-throughput-for-azure-cosmos-db-containers"></a>Definir o débito para contentores de base de dados do Azure Cosmos

Pode definir o débito para os contentores de BD do Cosmos do Azure no portal do Azure ou ao utilizar o SDKs do cliente. 

A tabela seguinte lista o débito disponível para contentores:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Contentor de partições únicas</strong></p></td>
            <td valign="top"><p><strong>Contentor particionada</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Débito mínimo</p></td>
            <td valign="top"><p>400 unidades de pedido por segundo</p></td>
            <td valign="top"><p>2500 unidades de pedido por segundo</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Débito máximo</p></td>
            <td valign="top"><p>10 000 unidades de pedido por segundo</p></td>
            <td valign="top"><p>Ilimitado</p></td>
        </tr>
    </tbody>
</table>

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Para definir o débito utilizando o portal do Azure

1. Numa nova janela, abra o [portal do Azure](https://portal.azure.com).
2. Na barra da esquerda, clique em **Azure Cosmos DB**, ou clique em **mais serviços** na parte inferior, em seguida, desloque-se para **bases de dados**e, em seguida, clique em **Azure Cosmos DB**.
3. Selecione a sua conta de base de dados do Cosmos.
4. Na nova janela, clique em **Explorador de dados (pré-visualização)** no menu de navegação.
5. Na nova janela, expanda a base de dados e o contentor e, em seguida, clique em **definições de dimensionamento &**.
6. Na nova janela, escreva o novo valor de débito no **débito** caixa e, em seguida, clique em **guardar**.

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-documentdb-api-for-net"></a>Para definir o débito, utilizando a API do DocumentDB para .NET

```C#
//Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 12,000 request units per second
offer = new OfferV2(offer, 12000);

//Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

## <a name="throughput-faq"></a>Débito FAQ

**Pode definir o meu débito para menos de 400 RU/s?**

400 RU/s é o débito mínimo disponível em coleções de partições únicas de BD do Cosmos (2500 RU/s é o mínimo para coleções particionadas). O pedido unidades estão definidas em 100 intervalos de RU/s, mas o débito não é possível definir 100 RU/s ou qualquer valor inferior a 400 RU/s. Se estiver à procura de um método económico desenvolver e testar Cosmos DB, pode utilizar o livre [emulador de BD do Azure Cosmos](local-emulator.md), que pode implementar localmente, sem qualquer custo. 

**Como definir o througput utilizando a API do MongoDB**

Não há nenhuma extensão de API do MongoDB para definir o débito. A recomendação é utilizar a API do DocumentDB, conforme mostrado no [para definir o débito, utilizando a API do DocumentDB para .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o aprovisionamento e a escala planet contínuo com Cosmos DB, consulte o artigo [divisão em partições e o dimensionamento com Cosmos DB](partition-data.md).
