---
title: Criar alertas no OMS Log Analytics | Microsoft Docs
description: "Alertas na análise de registos identificar informações importantes no seu repositório do OMS e podem proativamente notificá-lo de problemas ou da invocação ações para tentar corrigir as entradas.  Este artigo descreve como criar uma regra de alerta e detalhes de ações diferentes que podem realizar."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2017
ms.author: bwren
ms.openlocfilehash: c34fb7295e8f386f0e7cf2c1db6b26a3e49eae98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="working-with-alert-rules-in-log-analytics"></a>Trabalhar com regras de alertas na análise de registos
Os alertas são criados pelas regras de alerta que execute automaticamente pesquisas de registo em intervalos regulares.  É possível criar um registo de alerta se os resultados correspondentes aos critérios de específicos.  A regra pode, em seguida, execute automaticamente uma ou mais ações para notificá-lo do alerta proativamente ou da invocação do outro processo.   

Este artigo descreve os processos para criar e editar regras de alertas utilizando o portal do OMS.  Para obter detalhes sobre as diferentes definições e como implementar a lógica necessária, consulte [alertas de compreender na análise de registos](log-analytics-alerts.md).

>[!NOTE]
> Atualmente não é possível criar ou modificar uma regra de alerta no portal do Azure. 

## <a name="create-an-alert-rule"></a>Criar uma regra de alerta

Para criar uma regra de alerta através do portal do OMS, pode começa por criar uma pesquisa de registo dos registos que deve invocar o alerta.  O **alerta** botão estará disponível para que possa criar e configurar a regra de alerta.

>[!NOTE]
> Um máximo de 250 regras de alertas pode atualmente possível criar uma área de trabalho do OMS. 

1. Na página de descrição geral do OMS, clique em **pesquisa registo**.
2. Crie uma nova consulta de pesquisa de registo ou selecionar uma pesquisa de registo guardado. 
3. Clique em **alerta** na parte superior da página para abrir o **Adicionar regra de alerta** ecrã.
4. Configurar a regra de alerta utilizando informações [detalhes de regras de alerta](#details-of-alert-rules) abaixo.
6. Clique em **guardar** para concluir a regra de alerta.  Será iniciada imediatamente a executar.


## <a name="edit-an-alert-rule"></a>Editar uma regra de alerta
Pode obter uma lista de todas as regras de alerta no **alertas** menu na análise de registos **definições**.  

![Gerir alertas](./media/log-analytics-alerts/configure.png)

1. No OMS consola selecione o **definições** mosaico.
2. Selecione **alertas**.

Pode efetuar várias ações desta vista.

* Desativar uma regra selecionando **desativar** junto ao mesmo.
* Edite uma regra de alerta clicando no ícone de lápis junto ao mesmo.
* Remover uma regra de alerta clicando a **X** ícone junto ao mesmo. 

## <a name="details-of-alert-rules"></a>Detalhes de regras de alerta
Quando criar ou editar uma regra de alerta no portal do OMS, trabalhar com o **Adicionar regra de alerta** ou **Editar regra de alerta** página.  As tabelas seguintes descrevem os campos neste ecrã.

![Regra de alerta](media/log-analytics-alerts/add-alert-rule.png)

### <a name="alert-information"></a>Informações de alerta
Estas são as definições básicas para a regra de alerta e os alertas que cria.

| Propriedade | Descrição |
|:--- |:---|
| Nome | Nome exclusivo para identificar a regra de alerta. Este nome está incluído em qualquer alertas criados pela regra.  |
| Descrição | Descrição opcional da regra de alerta. |
| Gravidade |Gravidade de quaisquer alertas criados por esta regra. |

### <a name="search-query-and-time-window"></a>Janela de consulta e a hora de pesquisa
A janela de consulta e a hora de pesquisa devolver os registos que são avaliadas para determinar se todos os alertas devem ser criados.

| Propriedade | Descrição |
|:--- |:---|
| Consulta de pesquisa | Esta é a consulta que será executada.  Os registos devolvidos por esta consulta serão utilizados para determinar se é criado um alerta.<br><br>Selecione **consulta de pesquisa atual utilize** a utilizar a consulta atual ou selecione um existente já guardado pesquisa da lista.  A sintaxe de consulta é fornecida na caixa de texto, onde pode modificar, se necessário. |
| Janela de tempo |Especifica o intervalo de tempo para a consulta.  A consulta devolve apenas os registos que foram criados dentro deste intervalo da hora atual.  Isto pode ser qualquer valor entre 5 minutos e 24 horas.  Deve ser maior que ou igual a frequência de alerta.  <br><br> Por exemplo, se a janela de tempo é definida como 60 minutos e a consulta é executada em 1:15 PM, apenas os registos criados entre 12:15 PM e 1:15 PM vai ser devolvidos. |

Quando indicar a janela de tempo para a regra de alerta, será apresentado o número de registos existentes que correspondem aos critérios de pesquisa nessa janela de tempo.  Isto pode ajudar a determinar a frequência com que irá dar-lhe o número de resultados esperado.

### <a name="schedule"></a>Agenda
Define a frequência de execução da consulta de pesquisa.

| Propriedade | Descrição |
|:--- |:---|
| Frequência de alerta | Especifica a frequência com que a consulta deve ser executada. Pode ser qualquer valor entre 5 minutos e 24 horas. Deve ser igual ou menor do que a janela de tempo.  Se o valor é maior do que a janela de tempo, em seguida, o risco de registos que está a ser omitidos.<br><br>Por exemplo, considere uma janela de tempo de 30 minutos e uma frequência de 60 minutos.  Se a consulta é executada em 1:00, devolve registos entre 12:30 e 1:00 PM.  Da próxima vez que executar a consulta é 2:00 quando devolvam registos entre 1:30 e 2:00.  Nunca deverá ser avaliados de quaisquer registos criados entre 1:00 e 1:30. |


### <a name="generate-alert-based-on"></a>Gerar alerta com base no
Define os critérios que irão ser avaliados em comparação com os resultados da consulta de pesquisa para determinar se deve ser criado um alerta.  Estes detalhes será diferentes consoante o tipo de regra de alerta que selecionar.  Pode obter os detalhes para os tipos de regra de alerta diferente do [alertas de compreender na análise de registos](log-analytics-alerts.md).

| Propriedade | Descrição |
|:--- |:---|
| Suprimir os alertas | Quando ativar a supressão para a regra de alerta, ações para a regra estão desativadas por um período de tempo depois de criar um novo alerta definido. A regra ainda está em execução e vai criar registos de alerta se os critérios forem satisfeitos. Isto é permitir que o tempo para corrigir o problema sem executar ações duplicadas. |

#### <a name="number-of-results-alert-rules"></a>Número de regras de alerta de resultados

| Propriedade | Descrição |
|:--- |:---|
| Número de resultados |É criado um alerta se o número de registos devolvidos pela consulta **maior** ou **menor** o valor que fornecer.  |

#### <a name="metric-measurement-alert-rules"></a>Regras de alerta de métrica de medida

| Propriedade | Descrição |
|:--- |:---|
| Valor de agregação | Valor de limiar que cada valor de agregação nos resultados de exceder para ser considerado uma violação. |
| Alerta de Acionador com base em | O número de falhas de um alerta a ser criado.  Pode especificar **Total de falhas** para qualquer combinação de falhas em todos os resultados definido ou **falhas consecutivas** para exigir que as falhas tem de ocorrer amostras consecutivas. |

### <a name="actions"></a>Ações
Regras de alertas sempre criará um [alerta de registo](#alert-records) quando é cumprido o limiar.  Também pode definir uma ou mais respostas para ser executado como enviar uma mensagem de e-mail ou iniciar um runbook.



#### <a name="email-actions"></a>Ações de e-mail
Ações de e-mail enviam um e-mail com os detalhes do alerta para um ou mais destinatários.

| Propriedade | Descrição |
|:--- |:---|
| Notificação por e-mail |Especifique **Sim** se pretender que as mensagens de correio eletrónico seja enviado quando o alerta é acionado. |
| Assunto |Do assunto do e-mail.  Não é possível modificar o corpo da mensagem. |
| Destinatários |Endereços de todos os destinatários de correio eletrónico.  Se especificar mais de um endereço, separe os endereços com um ponto e vírgula (;). |

#### <a name="webhook-actions"></a>Ações de Webhook
As ações de Webhook permitem-lhe invocar um processo externo através de um pedido de HTTP POST único.

| Propriedade | Descrição |
|:--- |:---|
| Webhook |Especifique **Sim** se pretender que possa invocar um webhook quando o alerta é acionado. |
| URL do Webhook |O URL do webhook. |
| Incluir payload JSON personalizado |Selecione esta opção se pretende substituir o payload de predefinição com um payload personalizado. |
| Introduza o payload JSON personalizado |O payload personalizado para o webhook.  Consulte a secção anterior para obter mais detalhes. |

#### <a name="runbook-actions"></a>Ações de Runbook
Ações de Runbook inicia um runbook na automatização do Azure. 

>[!NOTE]
> Tem de ter a solução de automatização instalada na sua área de trabalho para esta ação seja ativada. 


| Propriedade | Descrição |
|:--- |:---|
| Runbook | Especifique **Sim** se pretender iniciar um runbook de automatização do Azure quando o alerta é acionado.  |
| Conta de automatização | Especifica a conta de automatização que os runbooks são selecionados.  Esta é a conta de ação que está ligada à área de trabalho. |
| Selecione um runbook | Selecione o runbook que pretende iniciar quando é criado um alerta. |
| Executar em | Selecione **Azure** para executar o runbook na nuvem.  Selecione **worker híbrido** para executar o runbook num agente com [Runbook Worker híbrido](../automation/automation-hybrid-runbook-worker.md ) instalado.  |




## <a name="next-steps"></a>Passos seguintes
* Instalar o [solução de gestão de alertas](log-analytics-solution-alert-management.md) para analisar alertas criados na análise de registos, juntamente com os alertas recolhidas a partir do System Center Operations Manager (SCOM).
* Leia mais sobre [pesquisas de registo](log-analytics-log-searches.md) que pode gerar alertas.
* Conclua uma explicação passo a passo para [configurar um webook](log-analytics-alerts-webhooks.md) com uma regra de alerta.  
* Aprender a escrever [runbooks na automatização do Azure](https://azure.microsoft.com/documentation/services/automation) para remediar problemas identificados através de alertas.

