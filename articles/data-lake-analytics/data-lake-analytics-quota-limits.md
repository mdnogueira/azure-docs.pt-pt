---
title: Limites de Quota do Azure Data Lake Analytics | Microsoft Docs
description: Saiba como ajuste e aumentar os limites de quota em contas do Azure Data Lake Analytics (ADLA).
services: data-lake-analytics
keywords: Azure Data Lake Analytics
documentationcenter: 
author: omidm1
editor: omidm1
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/18/2017
ms.author: omidm
ms.openlocfilehash: 957f306ea0e80b5830ad64e5ef06c6d122d9eccc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-data-lake-analytics-quota-limits"></a>Limites de Quota do Azure Data Lake Analytics

Saiba como ajuste e aumentar os limites de quota em contas do Azure Data Lake Analytics (ADLA). Saber estes limites pode ajudá-lo a compreender o comportamento da tarefa U-SQL. Todos os limites de quota são recuperável, pelo que pode aumentar os limites máximos ao atingir para nós.

## <a name="azure-subscriptions-limits"></a>Limites de subscrições do Azure

**Número máximo de ADLA contas por subscrição:** 5

 Este é o número máximo de contas ADLA que pode criar por subscrição. Se tentar criar uma conta ADLA sexto, obterá um erro "Atingiu o número máximo de contas de Data Lake Analytics permitidas (5) na região em nome da subscrição". Neste caso, um eliminar quaisquer contas ADLA não utilizadas ou entrar-nos por [abrir um pedido de suporte](#increase-maximum-quota-limits).

## <a name="adla-account-limits"></a>Limites de conta ADLA

**Número máximo de unidades de análise (AUs) por conta:** 250

Este é o número máximo de AUs que podem ser executados em simultâneo na sua conta. Se o total execução AUs em todas as tarefas excede este limite, as tarefas mais recentes são colocados em fila automaticamente. Por exemplo:

* Se tiver apenas uma tarefa em execução com 250 AUs, quando submete uma segunda tarefa-irá aguardar na fila de tarefas até que a primeira tarefa é concluída.
* Se já tiver cinco tarefas em execução e se cada utiliza 50 AUs, quando submete uma tarefa de sexto que necessita de 20 AUs espera na fila de tarefas até não existirem 20 AUs disponíveis.

**Número máximo de tarefas U-SQL em simultâneo por conta:** 20

Este é o número máximo de tarefas que podem ser executados em simultâneo na sua conta. Acima este valor, as tarefas mais recentes são colocados em fila automaticamente.

## <a name="adjust-adla-quota-limits-per-account"></a>Ajuste os limites de quota ADLA por conta

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Escolha uma conta ADLA existente.
3. Clique em **Propriedades**.
4. Ajustar **paralelismo** e **tarefas simultâneas** de acordo com as suas necessidades.

    ![Painel do portal da Análise do Azure Data Lake](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

## <a name="increase-maximum-quota-limits"></a>Aumentar os limites de quota máxima

1. Abra um pedido de suporte no Portal do Azure.

    ![Painel do portal da Análise do Azure Data Lake](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Painel do portal da Análise do Azure Data Lake](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Selecione o tipo de problema **Quota**.
3. Selecione o **subscrição** (Certifique-se de que não se trata de uma subscrição de "avaliação").
4. Selecione o tipo de quota **Data Lake Analytics**.

    ![Painel do portal da Análise do Azure Data Lake](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. No painel do problema, explicam o limite de pedido de aumento com **detalhes** de porque precisa esta capacidade extra.

    ![Painel do portal da Análise do Azure Data Lake](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Verifique as informações de contacto e criar o pedido de suporte.

A Microsoft revê o pedido e tenta acomodar as suas necessidades empresariais logo que possível.

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Gerir o Azure Data Lake Analytics com o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Monitorizar e resolver problemas das tarefas de Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
