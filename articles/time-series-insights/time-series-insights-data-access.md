---
title: "Configurar a segurança para aceder e gerir informações de séries de tempo do Azure | Microsoft Docs"
description: "Este artigo descreve como configurar permissões de segurança e como o acesso de gestão políticas para proteger informações de séries de tempo do Azure de acesso de dados e as políticas."
services: time-series-insights
ms.service: time-series-insights
author: op-ravi
ms.author: omravi
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 22c8e4481f2ba4163a55cc1bbb6b33c10379a605
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Conceder acesso a dados a um ambiente do Time Series Insights com o portal do Azure

Os ambientes do Time Series Insights têm dois tipos de políticas de acesso independentes:

* Políticas de acesso de gestão
* Políticas de acesso a dados

Ambas as políticas concedem aos principais (utilizadores e aplicações) do Azure Active Directory várias permissões num determinado ambiente. Os principais (utilizadores e aplicações) têm de pertencer ao active directory (conhecido como o inquilino do Azure) associado à subscrição com o ambiente.

As políticas de acesso de gestão concedem permissões relacionadas com a configuração do ambiente, como a:
*   Criação e eliminação do ambiente, origens de eventos, conjuntos de dados de referência, e
*   Gestão das políticas de acesso a dados.

As políticas de acesso a dados concedem permissões para emitir consultas de dados, manipular dados de referência no ambiente e partilhar consultas guardadas e perspetivas associadas ao ambiente.

Ambos os tipos de políticas permitem uma clara separação entre o acesso à gestão do ambiente e o acesso aos dados dentro do ambiente. Por exemplo, é possível configurar um ambiente de forma a que o proprietário criador do ambiente é removido do acesso a dados. Além disso, os utilizadores e serviços que têm permissão para ler dados a partir do ambiente podem ser concedidos sem acesso para a configuração do ambiente.

## <a name="grant-data-access"></a>Conceder acesso a dados
Siga estes passos para conceder acesso a dados para um utilizador principal:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Localize o seu ambiente de informações de séries de tempo. Tipo **séries de tempo** no **pesquisa** caixa. Selecione **ambiente de séries de tempo** nos resultados da pesquisa. 

3. Selecione o seu ambiente do Time Series Insights na lista.
   
4. Selecione **políticas de acesso de dados**, em seguida, selecione **+ adicionar**.
  ![Gerir a origem de informações de séries de tempo - ambiente](media/data-access/getstarted-grant-data-access1.png)

5. Selecione **selecionar utilizador**.  Procure o utilizador nome ou endereço de e-mail localizar o utilizador que pretende adicionar. Clique em **selecione** para confirmar a seleção. 

   ![Gerir a origem do Time Series Insights - adicionar](media/data-access/getstarted-grant-data-access2.png)

6. Selecione **função selecione**. Escolha a função de acesso apropriados para o utilizador:
   - Selecione **contribuinte** se pretender permitir ao utilizador alterar dados de referência e partilha guardar consultas e perspetivas com outros utilizadores do ambiente. 
   - Caso contrário, selecione **leitor** para permitir que os dados de consulta do utilizador no ambiente e guardar consultas (não partilhadas) pessoais no ambiente.

   Selecione **Ok** para confirmar a escolha de função.

   ![Gerir a origem do Time Series Insights - selecionar utilizador](media/data-access/getstarted-grant-data-access3.png)

8. Selecione **Ok** no **selecionar função de utilizador** página.

   ![Gerir a origem do Time Series Insights - selecionar função](media/data-access/getstarted-grant-data-access4.png)

9. O **políticas de acesso de dados** página lista os utilizadores e as funções selecionadas para cada utilizador.

   ![Gerir a origem do Time Series Insights - resultados](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba [como adicionar uma origem de evento do Hub de eventos para o seu ambiente de informações de séries de tempo de Azure](time-series-insights-how-to-add-an-event-source-eventhub.md).
* [Enviar eventos](time-series-insights-send-events.md) para a origem do evento.
* Ver o seu ambiente no [Explorador Insights de séries de tempo](https://insights.timeseries.azure.com).
