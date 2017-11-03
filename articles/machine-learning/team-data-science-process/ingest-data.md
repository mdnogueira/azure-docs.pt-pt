---
title: "Carregar dados para ambientes de armazenamento do Azure para a análise | Microsoft Docs"
description: Mover dados de e para o Armazenamento de Blobs do Azure
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: bradsev
ms.openlocfilehash: 3a4e8214043ece060cfabae8f74ebf21344777d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Carregar dados para ambientes de armazenamento para análise
O processo de ciência de dados do agrupamento requer que dados ser ingeridos ou carregados uma variedade de ambientes de armazenamento diferentes para ser processada ou analisados da forma mais adequado em cada fase do processo. Os destinos de dados utilizados para processamento incluem o Blob Storage do Azure, bases de dados do SQL Azure, SQL Server numa VM do Azure, o HDInsight (Hadoop) e o Azure Machine Learning. 

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

Isto **menu** ligações para tópicos que descrevem como a ingestão de dados para estes ambientes onde os dados são armazenados e processados de destino.

Técnica e necessidades de negócio, bem como a localização inicial, formatar e tamanho dos dados irá determinar os ambientes de destino no qual os dados têm de ser ingeridos para atingir os objetivos da sua análise. Não é invulgar para um cenário de dados para ser movidos entre vários ambientes para alcançar a variedade de tarefas necessárias para construir um modelo preditivo. Esta sequência de tarefas pode incluir, por exemplo, exploração de dados, pré- processamento de, limpeza, baixo amostragem e formação de modelo.

