---
title: Criar um ambiente do Azure Time Series Insights | Microsoft Docs
description: "Este artigo descreve como utilizar o portal do Azure para criar um novo ambiente de informações de séries de tempo."
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
ms.openlocfilehash: 6dba703851161a1eebce0101be8076682f09c76f
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Criar um novo ambiente do Time Series Insights no portal do Azure
Este artigo descreve como criar um novo ambiente de informações de séries de tempo no portal do Azure.

Informações de séries de tempo permite-lhe começar a visualizar e consultar os dados que fluem para os Hubs IoT do Azure e do Event Hubs, em minutos, permitindo a grandes volumes de consulta de dados de séries de tempo em segundos.  É também foi concebido para a escala do internet das coisas (IoT) e pode processar terabytes de dados.

## <a name="steps-to-create-the-environment"></a>Passos para criar o ambiente
Siga estes passos para criar um ambiente:

1.  Inicie sessão no [Portal do Azure](https://portal.azure.com).

2.  Selecione o **+ novo** botão.

3.  Selecione o **Internet das coisas** categoria e selecione **Insights de séries de tempo**.

   ![Crie o ambiente do Time Series Insights](media/time-series-insights-get-started/1-new-tsi.png)

4.  No **Insights de séries de tempo** página, selecione **criar**.

5. Preencha os parâmetros necessários. A tabela seguinte explica cada parâmetro:
   
   ![Crie o grupo de recursos do Time Series Insights](media/time-series-insights-get-started/2-create-tsi.png)
   
   Definição|Valor sugerido|Descrição
   ---|---|---
   Nome do ambiente | Um nome exclusivo | Este nome representa o ambiente no [Explorador de séries de tempo](https://insights.timeseries.azure.com)
   Subscrição | A sua subscrição | Se tiver várias subscrições, selecione a subscrição que contém a origem de evento, de preferência. O Time Series Insights pode detetar automaticamente recursos do Hub IoT do Azure e do Hub de Eventos existentes na mesma subscrição.
   Grupo de recursos | Criar uma nova ou utilize existente | Um grupo de recursos é uma coleção de recursos do Azure utilizados em conjunto. Pode escolher um grupo de recursos existente, por exemplo aquele que contém o seu Hub de eventos ou o IoT Hub. Ou pode tornar um novo se este recurso não está relacionado com os outros recursos.
   Localização | Mais próximo da sua origem de evento | De preferência, escolha a mesma localização do Centro de dados que contenha os dados de origem do evento, em esforço para evitar adicionados os custos de largura de banda por várias regiões e zona em vários locais e adicionados latência ao mover dados fora da região.
   Escalão de preço | S1 | Escolha o débito necessário. Os custos baixos e a capacidade de arranque, selecione S1.
   Capacidade | 1 | A capacidade é que o multiplicador aplica-se para a taxa de entrada, a capacidade de armazenamento e o custo associado o SKU selecionado.  Pode alterar a capacidade de um ambiente após a criação. Para os custos baixos, selecione uma capacidade de 1. 
  
6. Verifique **afixar ao dashboard** melhor facilmente para aceder ao seu ambiente de séries de tempo no futuro.

   ![Crie o marcador do Time Series Insights para o dashboard](media/time-series-insights-get-started/3-pin-create.png)

7. Selecione **criar** para iniciar o processo de aprovisionamento. Pode demorar alguns minutos.

8. Para monitorizar o processo de implementação, selecione o **notificações** símbolo (ícone de campainha).

   ![Ver as notificações](media/time-series-insights-get-started/4-notifications.png)

Quando a implementação for bem sucedida, pode selecionar **aceda a recursos** para configurar outras propriedades, definir a segurança com políticas de acesso de dados, adicione as origens de eventos e outras ações.

## <a name="next-steps"></a>Passos seguintes
* [Definir políticas de acesso de dados](time-series-insights-data-access.md) para proteger o seu ambiente.
* [Adicionar uma origem de evento do Hub de eventos](time-series-insights-how-to-add-an-event-source-eventhub.md) ao seu ambiente de informações de séries de tempo do Azure. 
* [Enviar eventos](time-series-insights-send-events.md) para a origem do evento.
* Ver o seu ambiente no [Explorador Insights de séries de tempo](https://insights.timeseries.azure.com).
