---
title: "Como adicionar uma origem de evento do IoT Hub às informações de séries de tempo do Azure | Microsoft Docs"
description: "Este artigo descreve como adicionar uma origem de evento que está ligada a um IoT Hub para o seu ambiente de informações de séries de tempo"
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
ms.openlocfilehash: 0469c35056d1d02457c162b8540af472b84f1e92
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="how-to-add-an-iot-hub-event-source-to-time-series-insights-environment"></a>Como adicionar uma origem de evento do IoT Hub para o ambiente de informações de séries de tempo
Este artigo descreve como utilizar o portal do Azure para adicionar uma origem de evento que lê dados a partir de um IoT Hub para o seu ambiente de informações de séries de tempo.

## <a name="prerequisites"></a>Pré-requisitos
- Crie um ambiente de informações de séries de tempo. Para obter mais informações, consulte [criar um ambiente de informações de séries de tempo do Azure](time-series-insights-get-started.md) 
- Crie um Hub IoT. Para obter mais informações sobre os Hubs IoT, consulte [criar um IoT Hub no portal do Azure](../iot-hub/iot-hub-create-through-portal.md)
- O IoT Hub tem de ter os eventos de mensagem Active Directory que está a ser enviados.
- Crie um grupo de consumidores dedicado no IoT Hub para o ambiente de conhecimentos aprofundados de séries de tempo para consumir do. Cada origem de evento Insights de séries de tempo tem de ter o seu próprio grupo de consumidores dedicado que não seja partilhado com quaisquer outros consumidores. Se vários leitores consumam eventos a partir do mesmo grupo de consumidores, todos os leitores serão provável que consulte falhas. Para obter mais informações, consulte o [guia para programadores do IoT Hub](../iot-hub/iot-hub-devguide.md).

## <a name="add-a-new-event-source"></a>Adicionar uma nova origem de evento
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Localize o seu ambiente existente do Insights de séries de tempo. Clique em **todos os recursos** no menu no lado esquerdo do portal do Azure. Selecione o seu ambiente do Time Series Insights.

3. Sob o **topologia do ambiente** , clique em **origens de eventos**.
   ![Evento origens + adicionar](media/time-series-insights-how-to-add-an-event-source-iothub/1-event-sources.png)

4. Clique em **+ adicionar**.

5. Forneça um **nome da origem de evento** exclusivo para este ambiente Insights de séries de tempo, tais como **fluxo de eventos**.

   ![Preencha os primeiros três parâmetros no formulário.](media/time-series-insights-how-to-add-an-event-source-iothub/2-import-option.png)

6. Selecione o **origem** como **IoT Hub**.

7. Selecione as adequadas **importar opção**. 
   - Escolha **utilize o IoT Hub a partir de subscrições disponíveis** quando já tiver um IoT Hub nas suas subscrições. Esta é a abordagem mais fácil.
   - Escolha **definições de fornecer o IoT Hub manualmente** quando o IoT Hub é externo para as suas subscrições ou pretende escolher opções avançadas. 

8. Se tiver selecionado o **utilize o IoT Hub a partir de subscrições disponíveis** opção, a tabela seguinte explica cada propriedade necessária:

   ![Detalhes de hub de subscrição e do evento](media/time-series-insights-how-to-add-an-event-source-iothub/3-new-event-source.png)

   | Propriedade | Descrição |
   | --- | --- |
   | ID da subscrição | Selecione a subscrição na qual este IoT Hub foi criado.
   | Nome do IoT Hub | Selecione o nome do IoT Hub.
   | Nome da política de IoT Hub | Selecione a política de acesso partilhado, o que pode ser encontrada no separador de definições do IoT Hub. Cada política de acesso partilhado tem um nome, as permissões que define e chaves de acesso. A política de acesso partilhado para a origem de evento *tem* ter **serviço ligar** permissões.
   | Chave de política do IoT Hub | A chave é pré-preenchida.
   | Grupo de consumidores do IoT Hub | O grupo de consumidores para ler eventos do IoT Hub. Recomenda-se vivamente a utilizar um grupo de consumidores dedicado para a origem de evento.
   | Formato de serialização de eventos | JSON é a serialização apenas disponível neste momento. As mensagens de eventos tem de estar no formato ou não existem dados podem ser lidos. |
   | Nome da propriedade de carimbo de data/hora | Para determinar este valor, tem de compreender o formato da mensagem dos dados da mensagem enviados para o IoT Hub. Este valor é o **nome** da propriedade de evento específico dos dados de mensagem que pretende utilizar como o carimbo de eventos. O valor é maiúsculas e minúsculas. Deixado em branco, o **tempo de colocar em fila de eventos** dentro o evento de origem é utilizada como o carimbo de eventos. |

9. Se tiver selecionado o **definições de fornecer o IoT Hub manualmente** opção, a tabela seguinte explica cada propriedade necessária:

   | Propriedade | Descrição |
   | --- | --- |
   | ID da subscrição | A subscrição na qual este IoT Hub foi criado.
   | Grupo de recursos | O nome do grupo de recursos no qual este IoT Hub foi criado.
   | Nome do IoT Hub | O nome do seu IoT Hub. Quando criou o seu IoT Hub, também lhe atribuiu um nome específico.
   | Nome da política de IoT Hub | A política de acesso partilhado, que pode ser criada no separador de definições do IoT Hub. Cada política de acesso partilhado tem um nome, as permissões que define e chaves de acesso. A política de acesso partilhado para a origem de evento *tem* ter **serviço ligar** permissões.
   | Chave de política do IoT Hub | A chave de acesso partilhado utilizada para autenticar o acesso ao espaço de nomes do Service Bus. Escreva a chave primária ou secundária aqui.
   | Grupo de consumidores do IoT Hub | O grupo de consumidores para ler eventos do IoT Hub. Recomenda-se vivamente a utilizar um grupo de consumidores dedicado para a origem de evento.
   | Formato de serialização de eventos | JSON é a serialização apenas disponível neste momento. As mensagens de eventos tem de estar no formato ou não existem dados podem ser lidos. |
   | Nome da propriedade de carimbo de data/hora | Para determinar este valor, tem de compreender o formato da mensagem dos dados da mensagem enviados para o IoT Hub. Este valor é o **nome** da propriedade de evento específico dos dados de mensagem que pretende utilizar como o carimbo de eventos. O valor é maiúsculas e minúsculas. Deixado em branco, o **tempo de colocar em fila de eventos** dentro o evento de origem é utilizada como o carimbo de eventos. |

10. Selecione **criar** para adicionar a nova origem de evento.

   ![Clique em Criar](media/time-series-insights-how-to-add-an-event-source-iothub/4-create-button.png)

   Depois de criar a origem de eventos, o Time Series Insights vai começar a transmitir dados para o seu ambiente automaticamente.

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicionar um grupo de consumidores ao seu IoT Hub
Grupos de consumidores são utilizados por aplicações para solicitar dados a partir dos Hubs IoT do Azure. Forneça um grupo de consumidores dedicado para utilização por este ambiente de informações de séries de tempo só, fiável ler dados a partir do seu IoT Hub.

Para adicionar um novo grupo de consumidores ao seu IoT Hub, siga estes passos:
1. No portal do Azure, localize e abra o seu IoT Hub.

2. Sob o **mensagens** cabeçalho, selecione **pontos finais**. 

   ![Adicionar um grupo de consumidores](media/time-series-insights-how-to-add-an-event-source-iothub/5-add-consumer-group.png)

3. Selecione o **eventos** ponto final e o **propriedades** é aberta a página.

4. Sob o **grupos de consumidores** cabeçalho, forneça um novo nome exclusivo para o grupo de consumidores. Utilize este mesmo nome num ambiente de informações de séries de tempo ao criar uma nova origem de evento.

5. Selecione **guardar** para guardar o novo grupo de consumidores.

## <a name="next-steps"></a>Passos seguintes
- [Definir políticas de acesso de dados](time-series-insights-data-access.md) para proteger os dados.
- [Enviar eventos](time-series-insights-send-events.md) para a origem do evento.
- Aceder ao seu ambiente no [Explorador Insights de séries de tempo](https://insights.timeseries.azure.com).
