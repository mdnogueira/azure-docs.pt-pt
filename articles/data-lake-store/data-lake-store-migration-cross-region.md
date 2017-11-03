---
title: "Migração de por várias regiões do Azure Data Lake Store | Microsoft Docs"
description: "Saiba mais sobre a migração por várias regiões do Azure Data Lake Store."
services: data-lake-store
documentationcenter: 
author: swums
manager: amitkul
editor: swums
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/27/2017
ms.author: stewu
ms.openlocfilehash: b04cca6e551a15a31bbebc4932ea05dd39e8e916
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-data-lake-store-across-regions"></a>Migrar o Data Lake Store em regiões

Como o Azure Data Lake Store e fica disponível regiões novo, pode optar por efetuar uma migração de uso individual, para tirar partido da região de novo. Saiba o que deve considerar quando planear e concluir a migração.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Para obter mais informações, consulte [hoje a criar a conta do Azure gratuita](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de Data Lake Store em duas regiões diferentes**. Para obter mais informações, consulte [introdução ao Azure Data Lake Store](data-lake-store-get-started-portal.md).
* **Fábrica de dados do Azure**. Para obter mais informações, consulte [introdução ao Azure Data Factory](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Considerações sobre a migração

Em primeiro lugar, identifique a estratégia de migração que funciona melhor para a sua aplicação que escreve, lê ou processa dados no Data Lake Store. Ao escolher uma estratégia, considere os requisitos de disponibilidade da aplicação e o período de indisponibilidade ocorre durante a migração. Por exemplo, poderá ser a abordagem mais simples utilizar o modelo de migração de cloud "comparação de precisão e shift". Esta abordagem, colocar em pausa a aplicação na sua região existente enquanto todos os seus dados são copiados para a região de novo. Quando o processo de cópia estiver concluído, retome a aplicação na região novo e, em seguida, elimine a conta antiga do Data Lake Store. É necessário o período de indisponibilidade durante a migração.

Para reduzir o período de indisponibilidade, pode iniciar imediatamente a ingestão de dados de novo na região de novo. Quando tiver os dados mínimos necessários, execute a sua aplicação na região de novo. Em segundo plano, continue a copiar os dados antigos da conta do Data Lake Store existente para a nova conta de Data Lake Store na região de novo. Ao utilizar esta abordagem, pode efetuar o comutador para a região novo com pouco tempo de inatividade. Quando todos os dados mais antigos foi copiado, elimine a conta antiga do Data Lake Store.

Outros detalhes importantes a considerar quando planear a migração são:

* **Volume de dados**. O volume de dados (em gigabytes, o número de ficheiros e pastas e assim sucessivamente) afeta o tempo e os recursos que necessários para a migração.

* **Nome da conta do Data Lake Store**. O novo nome de conta na região novo deve ser globalmente exclusivo. Por exemplo, o nome da sua conta do Data Lake Store antigo nos EUA Leste 2 poderão contosoeastus2.azuredatalakestore.net. Pode dar o nome da nova conta de Data Lake Store contosonortheu.azuredatalakestore.net EU Norte.

* **Ferramentas**. Recomendamos que utilize o [atividade de cópia do Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) copiar os ficheiros do Data Lake Store. Fábrica de dados suporta o movimento de dados com elevado desempenho e fiabilidade. Tenha em atenção que o Data Factory copia apenas a hierarquia de pastas e o conteúdo dos ficheiros. Tem de aplicar manualmente quaisquer listas de controlo de acesso (ACLs) que utilizar a conta antiga para a nova conta. Para obter mais informações, incluindo metas de desempenho para cenários mais favorável, consulte o [guia Otimização e de desempenho de atividade de cópia](../data-factory/copy-activity-performance.md). Se pretender que os dados copiados mais rapidamente, poderá ter de utilizar unidades de movimento de dados de nuvem adicionais. Algumas outras ferramentas, como AdlCopy, não suportam a cópia de dados entre regiões.  

* **Custos de largura de banda**. [Custos de largura de banda](https://azure.microsoft.com/en-us/pricing/details/bandwidth/) aplicar porque os dados são transferidos para fora de uma região do Azure.

* **As ACLs nos seus dados**. Proteja os seus dados na região novo aplicando ACLs de ficheiros e pastas. Para obter mais informações, consulte [proteger os dados armazenados no Azure Data Lake Store](data-lake-store-secure-data.md). Recomendamos que utilize a migração para atualizar e ajustar as ACLs. Pode querer utilizar definições semelhantes às suas definições atuais. Pode ver as ACLs que são aplicadas a qualquer ficheiro com o portal do Azure, [cmdlets do PowerShell](/powershell/module/azurerm.datalakestore/get-azurermdatalakestoreitempermission), ou SDKs.  

* **Localização de serviços de análise**. Para melhor desempenho, os serviços de análise, como o Azure Data Lake Analytics ou o Azure HDInsight, devem estar na mesma região que os dados.  

## <a name="next-steps"></a>Passos seguintes
* [Descrição geral do Azure Data Lake Store](data-lake-store-overview.md)
