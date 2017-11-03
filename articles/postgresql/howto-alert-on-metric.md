---
title: "Configurar alertas de métricas da base de dados do Azure para PostgreSQL no portal do Azure | Microsoft Docs"
description: "Este artigo descreve como configurar e alertas de métrica de acesso para a base de dados do Azure para PostgreSQL do portal do Azure."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 10/24/2017
ms.openlocfilehash: 3a09be8131b57381eb470027a134109c116467ed
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql"></a>Utilizar o portal do Azure para configurar alertas nas métricas da base de dados do Azure para PostgreSQL 

Este artigo mostra como configurar a base de dados do Azure para alertas PostgreSQL no portal do Azure. Pode receber um alerta com base nas métricas para os serviços do Azure de monitorização.

Os acionadores de alerta quando o valor de uma métrica especificado atravesse um limiar que atribuir. Alerta acionadores, quando a condição é cumprida e, em seguida, posteriormente quando condição que é já não está a ser cumprido. 

Pode configurar um alerta para executar as seguintes ações quando aciona:
* Envie notificações por e-mail para o administrador de serviços e coadministradores.
* envie correio eletrónico para e-mails adicionais que especificar.
* Chame um webhook.

Pode configurar e obter informações sobre regras de alertas com:
* [Portal do Azure](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [Interface de linha de comandos (CLI)](../monitoring-and-diagnostics/insights-alerts-command-line-interface.md)
* [API de REST de Monitor do Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Criar uma regra de alerta numa métrica do portal do Azure
1. No [portal do Azure](https://portal.azure.com/), selecione a base de dados do Azure para o servidor de PostgreSQL que pretende monitorizar.

2. Sob o **monitorização** secção da barra lateral, selecione **regras de alerta** conforme mostrado:

   ![Selecionar regras de alerta](./media/howto-alert-on-metric/1-alert-rules.png)

3. Selecione **Adicionar alerta métrica** (+ ícone). 

4. O **Adicionar regra** página abre-se conforme mostrado abaixo.  Preencha as informações necessárias:

   ![Adicionar métrico formulário de alerta](./media/howto-alert-on-metric/2-add-rule-form.png)

   | Definição | Descrição  |
   |---------|---------|
   | Nome | Forneça um nome para a regra de alerta. Este valor é enviado no e-mail de notificação de alertas. |
   | Descrição | Forneça uma breve descrição da regra de alerta. Este valor é enviado no e-mail de notificação de alertas. |
   | Alerta sobre | Escolha **métricas** para este tipo de alerta. |
   | Subscrição | Este campo é pré-preenchida com a subscrição que aloja a base de dados do Azure para PostgreSQL. |
   | Grupo de recursos | Este campo é pré-preenchida com o grupo de recursos da sua base de dados do Azure para PostgreSQL. |
   | Recurso | Este campo é pré-preenchida com o nome da base de dados do Azure para PostgreSQL. |
   | Métrica | Selecione a métrica que pretender emitir um alerta para. Por exemplo, **percentagem de armazenamento**. |
   | Condição | Escolha a condição para a métrica a ser comparada ao. Por exemplo, **maior**. |
   | Limiar | Valor de limiar a métrica, por exemplo 85 (percentagem). |
   | Período | O período de tempo que a regra de métrica deve ser satisfeita antes dos acionadores de alerta. Por exemplo, **nos últimos 30 minutos**. |

   Com base no exemplo, o alerta procura percentagem de armazenamento acima 85% durante um período de 30 minutos. Esse alerta é acionado quando a percentagem média de armazenamento já esteve acima 85% durante 30 minutos. Depois de ocorre o primeiro acionador, este é acionado novamente quando a percentagem média do armazenamento é inferior a 85% mais de 30 minutos.

5. Escolha o método de notificação que pretende para a regra de alerta. 

   Verifique **proprietários, contribuintes e leitores de E-Mail** opção se pretender que os administradores da subscrição e coadministradores para serem enviadas por e-mail quando o alerta é acionado.

   Se pretender que os e-mails adicionais para receber uma notificação quando o alerta é acionado, adicioná-los no **administrador adicionais email(s)** campo. Separe várias mensagens de correio eletrónico com ponto e vírgula -  *email@contoso.com;email2@contoso.com*

   Opcionalmente, forneça um URI válido no **Webhook** campo se quiser chamou quando o alerta é acionado.

6. Selecione **OK** para criar o alerta.

   Dentro de alguns minutos, o alerta está ativo e aciona conforme descrito anteriormente.

## <a name="manage-your-alerts"></a>Gerir os alertas
Assim que tiver criado um alerta, pode selecioná-lo e executar as seguintes ações:

* Ver um gráfico que mostra o limiar de métrico e os valores reais do dia anterior relevantes para este alerta.
* **Editar** ou **eliminar** a regra de alerta.
* **Desativar** ou **ativar** o alerta, se pretender temporariamente interromper ou retomar a receção de notificações.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [configurar webhooks alertas](../monitoring-and-diagnostics/insights-webhooks-alerts.md).
* Obter um [descrição geral da coleção de métricas](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para se certificar de que o serviço está disponível e reativa.
