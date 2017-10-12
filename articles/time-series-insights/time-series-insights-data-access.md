---
title: "Políticas de acesso a dados no Azure Time Series Insights | Microsoft Docs"
description: "Neste tutorial, vai aprender a gerir políticas de acesso a dados no Time Series Insights"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: omravi
ms.openlocfilehash: 5258bf5de6f7aa1ea246f1235e7d362b1b7d0181
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Conceder acesso a dados a um ambiente do Time Series Insights com o portal do Azure

Os ambientes do Time Series Insights têm dois tipos de políticas de acesso independentes:

* Políticas de acesso de gestão
* Políticas de acesso a dados

Ambas as políticas concedem aos principais (utilizadores e aplicações) do Azure Active Directory várias permissões num determinado ambiente. Os principais (utilizadores e aplicações) têm de pertencer ao Active Directory (ou “inquilino do Azure”) associado à subscrição que contém o ambiente.

As políticas de acesso de gestão concedem permissões relacionadas com a configuração do ambiente, como a:
*   Criação e eliminação do ambiente, origens de eventos, conjuntos de dados de referência, e
*   Gestão das políticas de acesso a dados.

As políticas de acesso a dados concedem permissões para emitir consultas de dados, manipular dados de referência no ambiente e partilhar consultas guardadas e perspetivas associadas ao ambiente.

Ambos os tipos de políticas permitem uma clara separação entre o acesso à gestão do ambiente e o acesso aos dados dentro do ambiente. Por exemplo, é possível configurar um ambiente de forma a que o proprietário/criador do mesmo é removido do acesso a dados. Da mesma forma, pode não ser concedido acesso à configuração do ambiente aos utilizadores e serviços que têm permissão para ler dados do mesmo.

## <a name="grant-data-access"></a>Conceder acesso a dados
Os passos seguintes mostram como pode conceder acesso a dados a um principal de utilizador:

1.  Inicie sessão no [Portal do Azure](https://portal.azure.com).
2.  Clique em "Todos os recursos" no menu no lado esquerdo do Portal do Azure.
3.  Selecione o seu ambiente do Time Series Insights.

  ![Gerir a origem do Time Series Insights - ambiente](media/data-access/getstarted-grant-data-access1.png)

4.  Selecione "Acesso do Plano de Dados", clique em "Adicionar"

  ![Gerir a origem do Time Series Insights - adicionar](media/data-access/getstarted-grant-data-access2.png)

5.  Clique em "Selecionar utilizador".
6.  Procure e selecione o utilizador por e-mail.
7.  Clique em “Selecionar”, no painel “Selecionar Utilizador”.

  ![Gerir a origem do Time Series Insights - selecionar utilizador](media/data-access/getstarted-grant-data-access3.png)

8.  Clique em “Selecionar função”.
9.  Selecione “Contribuidor” se quiser permitir que o utilizador altere os dados de referência e partilhe consultas guardadas e perspetivas com outros utilizadores do ambiente. Caso contrário, selecione “Utilizador” para permitir ao utilizador consultar dados no ambiente e guardar consultas pessoais (não partilhadas) no ambiente.
10. Clique em "Ok", no painel "Selecionar função".

  ![Gerir a origem do Time Series Insights - selecionar função](media/data-access/getstarted-grant-data-access4.png)

11. Clique em "Ok", no painel "Selecionar Função do Utilizador".
12. Deverá ver:

  ![Gerir a origem do Time Series Insights - resultados](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Passos seguintes

* [Criar uma origem de eventos](time-series-insights-add-event-source.md)
* [Enviar eventos](time-series-insights-send-events.md) para a origem de eventos
* Ver o seu ambiente no [Portal do Time Series Insights](https://insights.timeseries.azure.com)
