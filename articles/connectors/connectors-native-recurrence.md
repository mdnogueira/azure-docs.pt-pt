---
title: Agenda de tarefas e regularmente executar fluxos de trabalho - Azure Logic Apps | Microsoft Docs
description: "Criar e agendar a execução regularmente tarefas, ações, fluxos de trabalho, processos e cargas de trabalho com logic apps"
services: logic-apps
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: 51dd4f22-7dc5-41af-a0a9-e7148378cd50
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 77567302c529e6e06e58534ffc9db44c9a85bdb7
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="schedule-tasks-and-workflows-that-run-regularly-with-logic-apps"></a>Agendar tarefas e fluxos de trabalho que execute regularmente com logic apps

Para agendar tarefas, ações, cargas de trabalho ou os processos que são executados regularmente, pode criar um fluxo de trabalho de aplicação lógica que começa com o **agenda - periodicidade** [acionador](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts). Com este acionador, pode definir uma data e hora para iniciar a periodicidade e uma agenda de periodicidade para a execução de tarefas, tais como estes exemplos e muito mais:

* Obter dados internos: [executar um procedimento armazenado de SQL](../connectors/connectors-create-api-sqlazure.md) todos os dias.
* Obter dados externos: solicitar Meteorologia relatórios a partir NOAA a cada 15 minutos.
* Dados de relatório: um resumo de todas as ordens de maiores do que uma quantidade específica de correio eletrónico da semana passada.
* Processar os dados: Comprimir hoje carregou imagens de cada dia da semana horas de ponta.
* Limpar dados de: eliminar todos os tweets com mais de três meses.
* Dados de arquivo: Push faturas para um serviço de cópia de segurança de cada mês.

Este acionador suporta muitos padrões, por exemplo:

* Executar imediatamente e repetir a cada  *n*  número de segundos, minutos, horas, dias semanas ou meses.
* Inicie num momento específico, em seguida, executar e repetir a cada  *n*  número de segundos, minutos, horas, dias, semanas ou meses.
* Executar e repita num ou mais vezes por dia, por exemplo, cada 8 horas da Manhã e as 17:00:00.
* Executar e repita todas as semanas, mas apenas para dias específicos, tais como Sábado e Domingo.
* Executar e repita todas as semanas, mas apenas para dias específicos e o tempo, tais como segunda-feira através de sexta-feira, 8 horas da Manhã e as 17:00:00.

Quando o acionador de recorrência é acionado sempre, as Logic Apps cria e executa uma nova instância do seu fluxo de trabalho de aplicação lógica.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [começar com uma conta do Azure gratuita](https://azure.microsoft.com/free/). Caso contrário, pode [inscrever-se numa subscrição Pay As You Go](https://azure.microsoft.com/pricing/purchase-options/).

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/logic-apps-create-a-logic-app.md) 

## <a name="add-a-recurrence-trigger-to-your-logic-app"></a>Adicionar um acionador de recorrência à sua aplicação lógica

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Criar uma aplicação lógica em branco ou Saiba [como criar uma aplicação lógica em branco](../logic-apps/logic-apps-create-a-logic-app.md).

2. Depois de aparecer lógica Designer de aplicações, na caixa de pesquisa, introduza "recurrence" como o filtro. Selecione o **agenda - periodicidade** acionador. 

   ![Agenda - acionador de recorrência](./media/connectors-native-recurrence/add-recurrence-trigger.png)

   Este acionador agora é o primeiro passo na sua aplicação lógica.

3. Defina o intervalo e a frequência da periodicidade. Neste exemplo, defina estas propriedades para executar o fluxo de trabalho todas as semanas. 

   ![Intervalo do conjunto e a frequência](./media/connectors-native-recurrence/recurrence-trigger-details.png)

4. Para obter mais opções de agendamento, escolha **Mostrar opções avançadas**. 

   ![Mais opções](./media/connectors-native-recurrence/recurrence-trigger-more-options.png)

5. Agora pode definir estas opções: 

   * Defina uma data de início e a hora para que despoletou o acionador. 
   Se especificar uma data de início e a hora, também pode aplicar um fuso horário. 

   * Se selecionar "Dia" ou "Semana" para a frequência, pode selecionar a horas específicas para a periodicidade. 

   * Se selecionar "Semanas", pode selecionar demasiado dias específicos da semana.
   
   ![Opções de agendamento avançadas](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   Por exemplo, suponha que hoje é segunda-feira, 4 de Setembro de 2017. 
   O acionador de recorrência seguintes não acionados *qualquer define* que a data de início e a hora, que é segunda-feira, 18 de Setembro de 2017 às 8:00 AM PST. 
   No entanto, a agenda de periodicidade está definida para 10:30 AM, 12:30 PM e 2:30 PM segundas apenas. O primeiro tempo que o acionador desencadeado e cria uma instância de fluxo de trabalho de aplicação lógica estão em 10:30 AM. 
   Para saber mais sobre como iniciar vezes trabalho, consulte estes [iniciar exemplos de tempo](#start-time).
   Executa futuras acontecer às 12:30 PM e 2:30 PM no mesmo dia. 
   Cada periodicidade cria as seus próprios instância de fluxo de trabalho. Depois disso, a agenda completa repete-se todos os através de novamente seguinte segunda-feira. 
   [*Quais são alguns outras ocorrências exemplo?*](#example-recurrences)

   ![Exemplo de agendamento avançado](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > O acionador mostra uma pré-visualização para a periodicidade especificada só quando selecionar "Dia" ou "Semana" como a frequência.
   
6. Agora, criar o fluxo de trabalho restantes com ações ou instruções de controlo de fluxo. Para obter mais ações que pode adicionar, ver [conectores](../connectors/apis-list.md). 

## <a name="trigger-details"></a>Detalhes de Acionador

Pode configurar estas propriedades para o acionador de periodicidade.

| Nome | Necessário | Nome da propriedade | Tipo | Descrição | 
|----- | -------- | ------------- | ---- | ----------- | 
| **Frequência** | Sim | frequência | Cadeia | A unidade de tempo para a periodicidade: **segundo**, **minuto**, **hora**, **dia**, **semana**, ou  **Mês** | 
| **Intervalo** | Sim | intervalo | Número inteiro | Um número inteiro que descreve com que frequência o fluxo de trabalho é executada com base na frequência de. <p>O intervalo predefinido é 1. Seguem-se os intervalos de mínimos e máximo: <p>-Mês: 1-16 meses </br>-Dia: 1-500 dias </br>-Hora: 1-12 000 horas </br>-Minuto: 1-72,000 minutos </br>-Segundo: segundos de 1-9,999,999<p>Por exemplo, se o intervalo é de 6 e a frequência é de "Mês", em seguida, a periodicidade é a cada 6 meses. | 
| **Fuso horário** | Não | Fuso horário | Cadeia | Aplica-se apenas quando especificar uma hora de início porque este acionador não aceita [Desvio UTC](https://en.wikipedia.org/wiki/UTC_offset). Selecione o fuso horário que pretende aplicar. | 
| **Hora de início** | Não | startTime | Cadeia | Forneça uma hora de início neste formato: <p>AAAA-MM-ddTHH se Selecionou um fuso horário <p>-ou- <p>AAAA-MM-Aaaathh se não selecionar um fuso horário <p>Por isso, por exemplo, se quiser 18 de Setembro de 2017 às 2:00 PM, em seguida, especifique "2017-09-18T14:00:00" e selecione um fuso horário, como a hora do Pacífico. Em alternativa, especificar "2017-09-18T14:00:00Z" sem um fuso horário. <p>**Nota:** esta hora de início tem de seguir a [especificação de tempo de data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de hora UTC data](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mas sem um [Desvio UTC](https://en.wikipedia.org/wiki/UTC_offset). Se não selecionar um fuso horário, tem de adicionar a letra "Z" no final sem quaisquer espaços. Este "Z" refere-se para o equivalente [tempo nautical](https://en.wikipedia.org/wiki/Nautical_time). <p>Para agendas simples, a hora de início é a primeira ocorrência, enquanto para agendas complexas, o acionador não acionados qualquer define à hora de início. [*Quais são as opções que pode a utilizar a data de início e a hora?*](#start-time) | 
| **Nestes dias** | Não | weekDays | Cadeia ou matriz de cadeia | Se selecionar "Semanas", pode selecionar um ou mais dias em que pretende executar o fluxo de trabalho: **segunda-feira**, **Terça-feira**, **quarta-feira**, **Quinta-feira** , **Sexta-feira**, **Sábado**, e **Domingo** | 
| **Nestas horas** | Não | hours | Um número inteiro ou matriz de número inteiro | Se selecionar "Dia" ou "Semanas", pode selecionar números inteiros de um ou mais entre 0 e 23 horas do dia em que pretende executar o fluxo de trabalho. <p>Por exemplo, se especificar "10", "12" e "14", receberá 10 AM, 12 PM e 2 PM como as marcas de hora. | 
| **A estes minutos** | Não | minutes | Um número inteiro ou matriz de número inteiro | Se selecionar "Dia" ou "Semanas", pode selecionar números inteiros de um ou mais entre 0 e 59 minutos da hora em que pretende executar o fluxo de trabalho. <p>Por exemplo, pode especificar "30" como a marca de minuto e utilizando o exemplo anterior de horas do dia, receber 10:30 AM, 12:30 PM e 2:30 PM. | 
||||| 

## <a name="json-example"></a>Exemplo JSON

Segue-se uma definição de Acionador de periodicidade de exemplo:

``` json
{
    "triggers": {
        "Recurrence": {
            "type": "Recurrence",
            "recurrence": {
                "frequency": "Week",
                "interval": 1,
                "schedule": {
                    "hours": [
                        10,
                        12,
                        14
                    ],
                    "minutes": [
                        30
                    ],
                    "weekDays": [
                        "Monday"
                    ]
                },
               "startTime": "2017-09-07T14:00:00",
               "timeZone": "Pacific Standard Time"
            }
        }
    }
}
```

## <a name="faq"></a>FAQ

<a name="example-recurrences"></a>

**P:** quais são as agendas de periodicidade outro exemplo? </br>
**R:** Eis mais exemplos:

| Recorrência | intervalo | Frequência | Hora de início | Nestes dias | Nestas horas | A estes minutos | Nota |
| ---------- | -------- | --------- | ---------- | ------------- | -------------- | ---------------- | ---- |
| Execute a cada 15 minutos (sem data de início e a hora) | 15 | Minuto | {none} | {indisponível} | {none} | {none} | Esta agenda inicia imediatamente, em seguida, calcula recurrences futuras com base no tempo de execução último. | 
| Execute a cada 15 minutos (com a data e hora início) | 15 | Minuto | *startDate*T*startTime*Z | {indisponível} | {none} | {none} | Esta agenda não comece *qualquer define* à data de início especificado e à hora, em seguida, calcula recurrences futuras com base no tempo de execução último. | 
| Executada a cada hora, na hora (com a data e hora início) | 1 | Hora | *startDate*Thh:00:00Z | {indisponível} | {none} | {none} | Esta agenda não comece *qualquer define* especificado ao iniciar a data e hora. Recurrences futuras executam a cada hora na marca de minuto "00". <p>Se a frequência é "Semana" ou "Mês", esta agenda de execução respetivamente apenas um dia por semana ou um dia por mês. | 
| Executada a cada hora, diariamente (sem data de início e a hora) | 1 | Hora | {none} | {indisponível} | {none} | {none} | Esta agenda inicia imediatamente e calcula recurrences futuras com base no tempo de execução último. <p>Se a frequência é "Semana" ou "Mês", esta agenda de execução respetivamente apenas um dia por semana ou um dia por mês. | 
| Executada a cada hora, diariamente (com a data e hora início) | 1 | Hora | *startDate*T*startTime*Z | {indisponível} | {none} | {none} | Esta agenda não comece *qualquer define* à data de início especificado e à hora, em seguida, calcula recurrences futuras com base no tempo de execução último. <p>Se a frequência é "Semana" ou "Mês", esta agenda de execução respetivamente apenas um dia por semana ou um dia por mês. | 
| Execute a cada 15 minutos decorridos desde a hora, a cada hora (com a data e hora início) | 1 | Hora | *startDate*T00:15:00Z | {indisponível} | {none} | {none} | Esta agenda não comece *qualquer define* de iniciar a data e hora, em execução às 00:15, 1:15:00, 2:15 AM e assim sucessivamente especificado. | 
| Execute a cada 15 minutos decorridos desde a hora, a cada hora (sem data de início e a hora) | 1 | Dia | {none} | {indisponível} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Esta agenda de execução às 00:15, 1:15:00, 2:15 AM, e assim sucessivamente. Além disso, esta agenda é equivalente a uma frequência de "Hora" e uma hora de início com "15" minutos. | 
| Execute a cada 15 minutos na marca de 15 minutos (sem data de início e a hora) | 1 | Dia | {none} | {indisponível} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Esta agenda não iniciado até a próxima especificado marca de 15 minutos. | 
| Executar todos os dias (sem data de início e a hora) às 8:00 | 1 | Dia | {none} | {indisponível} | 8 | {none} | Esta agenda é executada diariamente, às 8:00 com base na agenda especificada. | 
| Executar todos os dias (com a data de início e a hora) às 8:00 | 1 | Dia | *startDate*T08:00:00Z | {indisponível} | {none} | {none} | Esta agenda de execução 8 horas da Manhã diariamente, com base na hora de início especificada. | 
| Executar às 8:30 AM todos os dias (sem data de início e a hora) | 1 | Dia | {none} | {indisponível} | 8 | 30 | Esta agenda de execução às 8:30 AM diariamente, com base na agenda especificada. | 
| Executar às 8:30 AM todos os dias (com a data e hora início) | 1 | Dia | *startDate*T08:30:00Z | {indisponível} | {none} | {none} | Esta agenda é iniciada a data de início especificada às 8:30 AM. | 
| Executar às 8:30 AM e 4:30 PM todos os dias | 1 | Dia | {none} | {indisponível} | 8, 16 | 30 | | 
| Executar às 8:30 AM, 8:45 AM, 4:30 PM e 4:45 PM todos os dias | 1 | Dia | {none} | {indisponível} | 8, 16 | 30, 45 | | 
| Executar todos os sábados às 17: 00 (sem data de início e a hora) | 1 | Semana | {none} | "Sábado" | 17 | 00 | Esta agenda executa todos os sábados às 17:00:00. | 
| Executar todos os sábados PM 5 (com a data e hora início) | 1 | Semana | *startDate*T17:00:00Z | "Sábado" | {none} | {none} | Esta agenda não comece *qualquer define* especificado ao iniciar a data e hora, neste caso, 9 de Setembro de 2017 nas 17:00:00. Recurrences futuras executam todos os sábados as 17:00:00. | 
| Executar cada terça-feira, Quinta-feira, as 17: 00 | 1 | Semana | {none} | "Terça-feira", "Quinta-feira" | 17 | {none} | Esta agenda executa cada terça-feira e a quinta-feira, as 17:00:00. | 
| Executada a cada hora durante o horário de trabalho | 1 | Semana | {none} | Selecione todos os dias, exceto Sábado e Domingo. | Selecione as horas do dia em que pretende. | Selecione qualquer minutos da hora em que pretende. | Por exemplo, se o seu horário de trabalho é 8 horas da Manhã para 5:00 PM, em seguida, selecione "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" como as horas do dia. <p>Se o seu horário de trabalho é 8:30 AM para 5:30 da Tarde, selecione as horas do dia "30" anteriores como minutos da hora. | 
| Executar uma vez para cada dia no fim de semana | 1 | Semana | {none} | "Sábado", "Domingo" | Selecione as horas do dia em que pretende. | Selecione qualquer minutos da hora conforme apropriado. | Esta agenda executa todos os sábados e domingo no horário especificado. | 
| Executar a cada 15 minutos bissemanais segundas apenas | 2 | Semana | {none} | "Segunda-feira" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Esta agenda de execução todos os outros segunda-feira na marca cada 15 minutos. | 
| Execute a cada hora para um dia por mês | 1 | Mês | {Ver nota} | {indisponível} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {Ver nota} | Se não especificar uma data de início e a hora, utiliza esta agenda a data de criação e a hora. Para controlar os minutos para a agenda de periodicidade, especifique os minutos da hora, uma hora de início, ou utilizar a hora de criação. Por exemplo, se o início de tempo ou o tempo de criação de 8:25 AM, esta agenda é executada às 8:25 AM, 9:25 AM, 10:25 AM, e assim sucessivamente. | 
||||||||| 

<a name="start-time"></a>

**P:** quais são as opções que pode a utilizar a data de início e a hora? </br>
**R:** aqui são alguns padrões que mostram como pode controlar periodicidade com a data de início e a hora e a forma como o motor de Logic Apps executa estas recurrences:

| Hora de início | Periodicidade sem agenda | Periodicidade com agenda | 
| ---------- | --------------------------- | ------------------------ | 
| {none} | Executa a carga de trabalho primeiro instantaneamente. <p>Executa futuras cargas de trabalho com base no tempo de execução último. | Executa a carga de trabalho primeiro instantaneamente. <p>Executa futuras cargas de trabalho com base na agenda especificada. | 
| Hora de início no passado | Calcula tempos de execução com base na hora de início especificada e rejeições passado executar vezes. É executada a carga de trabalho primeiro num futuro próximo tempo de execução. <p>Executa futuras cargas de trabalho com base nos cálculos a partir do último tempo de execução. <p>Para obter explicações mais, consulte o exemplo que segue nesta tabela. | Executa a carga de trabalho primeiro *não mais cedo* à hora de início, com base numa agenda calculada a partir da hora de início. <p>Executa futuras cargas de trabalho com base na agenda especificada. <p>**Nota:** se especificar uma periodicidade com base numa agenda, mas não especificar horas ou minutos para que a agenda, em seguida, tempos de execução futuros são calculados a utilizar as horas ou minutos, respetivamente, desde a primeira vez execute. | 
| Hora de início no presente ou no futuro | Executa a carga de trabalho primeiro o momento de início especificada. <p>Executa futuras cargas de trabalho com base nos cálculos a partir do último tempo de execução. | Executa a carga de trabalho primeiro *não mais cedo* à hora de início, com base numa agenda calculada a partir da hora de início. <p>Executa futuras cargas de trabalho com base na agenda especificada. <p>**Nota:** se especificar uma periodicidade com base numa agenda, mas não especificar horas ou minutos para que a agenda, em seguida, tempos de execução futuros são calculados a utilizar as horas ou minutos, respetivamente, desde a primeira vez execute. | 
||||

**Exemplo de uma hora de início passado com periodicidade, mas nenhuma agenda** 

| Hora de início | Hora atual | Recorrência | Agenda |
| ---------- | ------------ | ---------- | -------- | 
| 2017-09 -**07**T14:00:00Z | 2017-09 -**08**T13:00:00Z | Cada 2 dias | {none} | 
||||| 

Neste cenário, as Logic Apps motor calcula executar vezes com base na hora de início, rejeita passado dos tempos de execução e utiliza o futuro próximo hora de início para a primeira execução. Depois de executar essa primeiro, executa futuras baseiam-se na agenda calculada a partir da hora de início. Eis o aspeto esta recorrência tem:

| Hora de início | Tempo de execução pela primeira vez | Futuro executar vezes | 
| ---------- | ------------ | ---------- | 
| 2017-09 -**07** às 2:00 PM | 2017-09 -**09** às 2:00 PM | 2017-09 -**11** às 2:00 PM </br>2017-09 -**13** às 2:00 PM </br>2017-09 -**15** às 2:00 PM </br>e assim sucessivamente...
||||| 

Por isso, para este cenário, independentemente do até que ponto no passado, especificar o início do tempo, por exemplo, 2017-09 -**05** às 2:00 PM ou 2017-09 -**01** às 2:00 PM, o primeiro tempo de execução é o mesmo.

## <a name="next-steps"></a>Passos seguintes

* [Ações de fluxo de trabalho e acionadores](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)
* [Conectores](../connectors/apis-list.md)
