---
title: "Tutorial de distribuição global do Cosmos BD do Azure para a API de tabela | Microsoft Docs"
description: "Saiba como configurar a base de dados do Azure Cosmos distribuição global utilizando a API de tabela."
services: cosmos-db
keywords: "distribuição global, tabela"
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: mimig
ms.openlocfilehash: e9cb4b5b886cec46c0483287460c720855867f38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-table-api"></a>Como configurar a base de dados do Azure Cosmos distribuição global utilizando a API de tabela

Neste artigo, vamos mostrar como utilizar o portal do Azure para a configuração distribuição global da BD do Cosmos do Azure e, em seguida, ligue-se utilizando a API de tabela (pré-visualização).

Este artigo abrange as seguintes tarefas: 

> [!div class="checklist"]
> * Configurar a distribuição global com o portal do Azure
> * Configurar a distribuição global utilizando o [API de tabela](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Ligar a uma região preferencial utilizando a API de tabela

Para tirar o máximo partido das [distribuição global](distribute-data-globally.md), aplicações de cliente podem especificar a lista de preferência ordenada de regiões para ser utilizado para executar operações de documento. Isto pode ser feito através da definição de `TablePreferredLocations` valor de configuração na configuração de aplicação para a pré-visualização do SDK de armazenamento do Azure. Com base na configuração da conta de base de dados do Azure Cosmos, disponibilidade regional atual e a lista de preferência especificado, o ponto final ideal será selecionado pelo SDK de armazenamento do Azure para efetuar a escrita e operações de leitura.

O `TablePreferredLocations` deve conter uma lista separada por vírgulas das localizações (multi homing) preferenciais para leituras. Cada instância de cliente pode especificar um subconjunto destas regiões pela ordem preferencial para leituras de latência baixa. As regiões devem ter o nome a utilizar as respetivas [nomes a apresentar](https://msdn.microsoft.com/library/azure/gg441293.aspx), por exemplo, `West US`.

Todas as leituras serão enviadas para a região disponível primeiro no `TablePreferredLocations` lista. Se falhar o pedido, o cliente irá falhar para baixo a lista para a região seguinte e assim sucessivamente.

O SDK só irá tentar ler a partir de regiões especificadas no `TablePreferredLocations`. Sim, por exemplo, se a conta de base de dados está disponível em três regiões, mas o cliente especifica apenas dois as regiões de escrita para `TablePreferredLocations`, em seguida, nenhum leituras serão servidas fora da região de escrita, mesmo em caso de ativação pós-falha.

O SDK irá enviar automaticamente todas as operações de escrita para o atual escrever região.

Se o `TablePreferredLocations` não for definida, serão servidos todos os pedidos de região de escrita atual.

```xml
    <appSettings>
      <add key="TablePreferredLocations" value="East US, West US, North Europe"/>           
    </appSettings>
```

Que é, que conclui neste tutorial. Pode saber como gerir a consistência da sua conta replicada globalmente o lendo [níveis de consistência na base de dados do Azure Cosmos](consistency-levels.md). E para obter mais informações sobre a replicação de base de dados como global funciona do BD Azure Cosmos, consulte [distribuir dados globalmente com a base de dados do Azure Cosmos](distribute-data-globally.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez o seguinte:

> [!div class="checklist"]
> * Configurar a distribuição global com o portal do Azure
> * Configurar a distribuição global com as APIs do DocumentDB

Agora pode avançar para o próximo tutorial para saber como desenvolver localmente, utilizando o emulador local da base de dados do Azure Cosmos.

> [!div class="nextstepaction"]
> [Desenvolver localmente com o emulador](local-emulator.md)