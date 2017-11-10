---
title: Mover dados para e do armazenamento de Blobs do Azure | Microsoft Docs
description: Mover dados de e para o Armazenamento de Blobs do Azure
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: d6681e30-ab45-45ea-a9fb-ac8acefe544d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev;sachouks
ms.openlocfilehash: f282705b6d5d1f6867ea87737f19b5550054789a
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Mover dados para e do armazenamento de Blobs do Azure
[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

<!-- just in case, adding this to separate these two include references -->

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

O método é melhor para si depende do seu cenário. O [cenários para análise avançada no Azure Machine Learning](plan-sample-scenarios.md) artigo ajuda-o a determinar os recursos que necessita de uma variedade de fluxos de trabalho do ciência de dados utilizados no processo de análise avançadas.

> [!NOTE]
> Para obter uma introdução completa para o armazenamento de Blobs do Azure, consulte [Noções básicas de Blob do Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) e [serviço Blob do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

Como alternativa, pode utilizar [do Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para: 

* criar e agendar um pipeline que transfere dados a partir do blob storage do Azure, 
* transmita-a um serviço web Azure Machine Learning publicado, 
* receber os resultados de Análise Preditiva, e 
* carregar os resultados para o armazenamento. 

Para obter mais informações, consulte [Criar pipelines preditivos com o Azure Data Factory e o Azure Machine Learning](../../data-factory/v1/data-factory-azure-ml-batch-execution-activity.md).

## <a name="prerequisites"></a>Pré-requisitos
Este documento parte do princípio de que tem uma subscrição do Azure, uma conta de armazenamento e a chave de armazenamento correspondente para essa conta. Antes de carregar/transferência de dados, tem de conhecer a chave de conta e o nome da conta de armazenamento do Azure.

* Para configurar uma subscrição do Azure, consulte [um mês avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Para obter instruções sobre como criar uma conta de armazenamento e para obter a conta e informações da chave, consulte [contas do storage do Azure sobre](../../storage/common/storage-create-storage-account.md).

