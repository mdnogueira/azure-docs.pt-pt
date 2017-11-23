---
title: "Como adicionar uma origem de evento do Hub de eventos para informações de séries de tempo do Azure | Microsoft Docs"
description: "Este artigo descreve como adicionar uma origem de evento que está ligada a um Hub de eventos para o seu ambiente de informações de séries de tempo."
services: time-series-insights
ms.service: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/21/2017
ms.openlocfilehash: c07c847784eb13c62e350e9c655e027e7df696a3
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="how-to-add-an-event-hub-event-source-to-time-series-insights-environment"></a>Como adicionar uma origem de evento do Hub de eventos para o ambiente de informações de séries de tempo

Este artigo descreve como utilizar o portal do Azure para adicionar uma origem de evento que lê dados a partir de um Hub de eventos para o seu ambiente de informações de séries de tempo.

## <a name="prerequisites"></a>Pré-requisitos
- Crie um ambiente de informações de séries de tempo. Para obter mais informações, consulte [criar um ambiente de informações de séries de tempo do Azure](time-series-insights-get-started.md) 
- Crie um Hub de eventos. Para obter mais informações sobre os Event Hubs, consulte [criar um espaço de nomes de Event Hubs e um hub de eventos no portal do Azure](../event-hubs/event-hubs-create.md)
- O Hub de eventos tem de ter os eventos de mensagem Active Directory que está a ser enviados. Para obter mais informações, consulte [enviar eventos para utilizar o .NET Framework de Event Hubs do Azure](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Crie um grupo de consumidores dedicado no Hub de eventos para o ambiente de conhecimentos aprofundados de séries de tempo para consumir do. Cada origem de evento Insights de séries de tempo tem de ter o seu próprio grupo de consumidores dedicado que não seja partilhado com quaisquer outros consumidores. Se vários leitores consumam eventos a partir do mesmo grupo de consumidores, todos os leitores serão provável que consulte falhas. Tenha em atenção o que há também um limite de 20 grupos de consumidores do Hub de eventos. Para obter mais informações, consulte o [guia de programação de Hubs de eventos](../event-hubs/event-hubs-programming-guide.md).

## <a name="add-a-new-event-source"></a>Adicionar uma nova origem de evento
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Localize o seu ambiente existente do Insights de séries de tempo. Clique em **todos os recursos** no menu no lado esquerdo do portal do Azure. Selecione o seu ambiente do Time Series Insights.

3. Sob o **topologia do ambiente** , clique em **origens de eventos**.

   ![Evento origens + adicionar](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)

4. Clique em **+ adicionar**.

5. Forneça um **nome da origem de evento** exclusivo para este ambiente Insights de séries de tempo, tais como **fluxo de eventos**.

   ![Preencha os primeiros três parâmetros no formulário.](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)

6. Selecione o **origem** como **Hub de eventos**.

7. Selecione as adequadas **importar opção**. 
   - Escolha **Hub de eventos de utilização de subscrições disponíveis** quando já tiver um Hub de eventos existentes das suas subscrições. Esta é a abordagem mais fácil.
   - Escolha **definições de Hub de eventos de fornecer manualmente** quando o Hub de eventos é externo para as suas subscrições ou pretende escolher opções avançadas. 

8. Se tiver selecionado o **Hub de eventos de utilização de subscrições disponíveis** opção, a tabela seguinte explica cada propriedade necessária:

   ![Detalhes de hub de subscrição e do evento](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)

   | Propriedade | Descrição |
   | --- | --- |
   | ID da subscrição | Selecione a subscrição na qual este hub de eventos foi criado.
   | Espaço de nomes de barramento de serviço | Selecione o espaço de nomes de barramento de serviço que contém o Hub de eventos.
   | Nome do hub de eventos | Selecione o nome do Hub de eventos.
   | Nome de política do hub de eventos | Selecione a política de acesso partilhado, que pode ser criada no separador Configurar do Hub de eventos. Cada política de acesso partilhado tem um nome, as permissões que define e chaves de acesso. A política de acesso partilhado para a origem de evento *tem* ter **ler** permissões.
   | Chave de política de hub de eventos | O valor da chave pode ser pré-preenchida.
   | Grupo de consumidores do hub de eventos | O grupo de consumidores para ler eventos do Hub de eventos. Recomenda-se vivamente a utilizar um grupo de consumidores dedicado para a origem de evento. |
   | Formato de serialização de eventos | JSON é a serialização apenas disponível neste momento. As mensagens de eventos tem de estar no formato ou não existem dados podem ser lidos. |
   | Nome da propriedade de carimbo de data/hora | Para determinar este valor, tem de compreender o formato da mensagem dos dados da mensagem enviados para o Hub de eventos. Este valor é o **nome** da propriedade de evento específico dos dados de mensagem que pretende utilizar como o carimbo de eventos. O valor é maiúsculas e minúsculas. Deixado em branco, o **tempo de colocar em fila de eventos** dentro o evento de origem é utilizada como o carimbo de eventos. |


9. Se tiver selecionado o **definições de Hub de eventos de fornecer manualmente** opção, a tabela seguinte explica cada propriedade necessária:

   | Propriedade | Descrição |
   | --- | --- |
   | ID da subscrição | A subscrição na qual este hub de eventos foi criado.
   | Grupo de recursos | O grupo de recursos no qual este hub de eventos foi criado.
   | Espaço de nomes de barramento de serviço | Um espaço de nomes do Service Bus é um contentor para um conjunto de entidades de mensagens. Quando tiver criado um novo Hub de eventos, também criou um espaço de nomes do Service Bus.
   | Nome do hub de eventos | O nome do seu Hub de eventos. Quando criou o seu hub de eventos, também lhe atribuiu um nome específico.
   | Nome de política do hub de eventos | A política de acesso partilhado, que pode ser criada no separador Configurar do Hub de eventos. Cada política de acesso partilhado tem um nome, as permissões que define e chaves de acesso. A política de acesso partilhado para a origem de evento *tem* ter **ler** permissões.
   | Chave de política de hub de eventos | A chave de acesso partilhado utilizada para autenticar o acesso ao espaço de nomes do Service Bus. Escreva a chave primária ou secundária aqui.
   | Grupo de consumidores do hub de eventos | O grupo de consumidores para ler eventos do Hub de eventos. Recomenda-se vivamente a utilizar um grupo de consumidores dedicado para a origem de evento.
   | Formato de serialização de eventos | JSON é a serialização apenas disponível neste momento. As mensagens de eventos tem de estar no formato ou não existem dados podem ser lidos. |
   | Nome da propriedade de carimbo de data/hora | Para determinar este valor, tem de compreender o formato da mensagem dos dados da mensagem enviados para o Hub de eventos. Este valor é o **nome** da propriedade de evento específico dos dados de mensagem que pretende utilizar como o carimbo de eventos. O valor é maiúsculas e minúsculas. Deixado em branco, o **tempo de colocar em fila de eventos** dentro o evento de origem é utilizada como o carimbo de eventos. |


10. Selecione **criar** para adicionar a nova origem de evento.
   
   ![Clique em Criar](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)

   Depois de criar a origem de eventos, o Time Series Insights vai começar a transmitir dados para o seu ambiente automaticamente.


### <a name="add-a-consumer-group-to-your-event-hub"></a>Adicionar um grupo de consumidores para o Hub de eventos
Grupos de consumidores são utilizados por aplicações para solicitar dados a partir de Event Hubs do Azure. Forneça um grupo de consumidores dedicado para utilização por este ambiente de informações de séries de tempo só, fiável ler dados a partir do seu Hub de eventos.

Para adicionar um novo grupo de consumidores do seu Hub de eventos, siga estes passos:
1. No portal do Azure, localize e abra o seu Hub de eventos.

2. Sob o **entidades** cabeçalho, selecione **grupos de consumidores**.

   ![Hub de eventos - adicionar um grupo de consumidores](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)

3. Selecione **+ o grupo de consumidores** para adicionar um novo grupo de consumidores. 

4. No **grupos de consumidores** , indique uma nova exclusivo **nome**.  Ao criar uma nova origem de evento no ambiente de informações de séries de tempo, utilize este mesmo nome.

5. Selecione **criar** para criar o novo grupo de consumidores.

## <a name="next-steps"></a>Passos seguintes
- [Definir políticas de acesso de dados](time-series-insights-data-access.md) para proteger os dados.
- [Enviar eventos](time-series-insights-send-events.md) para a origem do evento.
- Aceder ao seu ambiente no [Explorador Insights de séries de tempo](https://insights.timeseries.azure.com).
