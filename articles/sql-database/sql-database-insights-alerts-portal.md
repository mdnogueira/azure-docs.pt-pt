---
title: Utilize o portal do Azure para criar alertas da base de dados SQL | Microsoft Docs
description: "Utilize o portal do Azure para criar alertas de base de dados do SQL Server, que podem acionar notificações ou automatização quando forem cumpridas condições que especificar."
author: aamalvea
manager: jhubbard
editor: 
services: sql-database
documentationcenter: 
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: sql-database
ms.custom: monitor and tune
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: aamalvea
ms.openlocfilehash: fd21c9b5e573ac6a47fef88c2a9d31c52618ecb8
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="use-azure-portal-to-create-alerts-for-azure-sql-database-and-data-warehouse"></a>Utilize o portal do Azure para criar alertas para a SQL Database do Azure e do armazém de dados

## <a name="overview"></a>Descrição geral
Este artigo mostra como configurar alertas de SQL Database do Azure e do armazém de dados no portal do Azure. Este artigo também fornece as melhores práticas para definir períodos de alerta.    

Pode receber um alerta com base na monitorização métricas para ou eventos nos seus serviços do Azure.

* **Valores métricos** -o alerta é acionado quando o valor de uma métrica especificado atravesse um limiar atribuir em qualquer direção. Ou seja, aciona ambas quando a condição for satisfeita primeiro e, em seguida, posteriormente quando condição que é já não está a ser cumprido.    
* **Eventos de registo de atividade** -pode acionar um alerta num *cada* eventos ou, se ocorre um determinado número de eventos.

Pode configurar um alerta para fazer o seguinte quando aciona:

* enviar notificações por e-mail para o administrador de serviços e coadministradores
* envie correio eletrónico para e-mails adicionais que especificar.
* Chamar um webhook

Pode configurar e obter informações sobre regras de alerta utilizando

* [Portal do Azure](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [interface de linha de comandos (CLI)](../monitoring-and-diagnostics/insights-alerts-command-line-interface.md)
* [API de REST de Monitor do Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Criar uma regra de alerta numa métrica com o portal do Azure
1. No [portal](https://portal.azure.com/), localize o recurso estiver interessado na monitorização e selecione-o.
2. Este passo é diferente para a base de dados SQL e conjuntos elásticos em comparação com o armazém de dados do SQL Server: 

   - **& Apenas para conjuntos elásticos SQL DB**: selecione **alertas** ou **regras de alerta** na secção monitorização. Ícone de texto e podem variar devido às ligeiramente diferentes recursos.  
   
     ![Monitorização](../monitoring-and-diagnostics/media/insights-alerts-portal/AlertRulesButton.png)
  
   - **Armazém de dados SQL só**: selecione **monitorização** na secção tarefas comuns. Clique em de **DWU utilização** gráfico.

     ![TAREFAS COMUNS](../monitoring-and-diagnostics/media/insights-alerts-portal/AlertRulesButtonDW.png)

3. Selecione o **Adicionar alerta** de comandos e preencha os campos.
   
    ![Adicionar o alerta](../monitoring-and-diagnostics/media/insights-alerts-portal/AddDBAlertPage.png)
4. **Nome** o alerta de regra e escolha um **Descrição**, que também mostra nos e-mails de notificação.
5. Selecione o **métrica** que pretende monitorizar, em seguida, escolha um **condição** e **limiar** valor para a métrica. Também escolher o **período** de tempo que a regra de métrica deve ser satisfeita antes dos acionadores de alerta. Por isso, por exemplo, se utilizar o período "PT5M" e o alerta procura CPU superior a 80%, o alerta é acionado quando o **médio** CPU já esteve acima 80% para 5 minutos. Depois de ocorre o primeiro acionador, novamente acionado quando a CPU média é inferior a 80% mais de 5 minutos. A medição de CPU ocorre a cada 1 minuto. Consulte a tabela abaixo para intervalos de tempo suportado e a agregação escreva que cada alerta não utiliza todos os alertas de utilizam o valor médio.   
6. Verifique **proprietários de E-Mail...**  se pretender que os administradores e coadministradores para serem enviadas por e-mail quando o alerta é acionado.
7. Se pretender que os e-mails adicionais para receber uma notificação quando o alerta é acionado, adicioná-los no **administrador adicionais email(s)** campo. Separe várias mensagens de correio eletrónico com ponto e vírgula -  *email@contoso.com;email2@contoso.com*
8. Colocar um URI válido no **Webhook** campo se quiser chamou quando o alerta é acionado.
9. Selecione **OK** quando pronto para criar o alerta.   

Dentro de alguns minutos, o alerta está ativo e aciona conforme descrito anteriormente.

## <a name="managing-your-alerts"></a>Gerir os alertas
Assim que tiver criado um alerta, pode selecionar e:

* Ver um gráfico que mostra o limiar de métrico e os valores reais do dia anterior.
* Editar ou eliminá-lo.
* **Desativar** ou **ativar** -se de que pretende temporariamente interromper ou retomar a receção de notificações para esse alerta.


## <a name="sql-database-alert-values"></a>Valores de alerta de base de dados SQL

| Tipo de Recurso | Nome da métrica | Nome amigável | Tipo de agregação | Janela de tempo mínimo de alerta|
| --- | --- | --- | --- | --- |
| Base de dados SQL | cpu_percent | Percentagem de CPU | Média | 5 minutos |
| Base de dados SQL | physical_data_read_percent | Percentagem de ES de Dados | Média | 5 minutos |
| Base de dados SQL | log_write_percent | Percentagem de es do registo | Média | 5 minutos |
| Base de dados SQL | dtu_consumption_percent | Percentagem de DTU | Média | 5 minutos |
| Base de dados SQL | Armazenamento | Tamanho total da base de dados | Máximo | 30 minutos |
| Base de dados SQL | connection_successful | Ligações com êxito | Total | 10 minutos |
| Base de dados SQL | connection_failed | Falha de ligações | Total | 10 minutos |
| Base de dados SQL | blocked_by_firewall | Bloqueado pela Firewall | Total | 10 minutos |
| Base de dados SQL | impasse | Impasses | Total | 10 minutos |
| Base de dados SQL | storage_percent | Percentagem de tamanho da Base de Dados | Máximo | 30 minutos |
| Base de dados SQL | xtp_storage_percent | Percent(Preview) de armazenamento do OLTP dentro da memória | Média | 5 minutos |
| Base de dados SQL | workers_percent | Percentagem de trabalhadores | Média | 5 minutos |
| Base de dados SQL | sessions_percent | Percentagem de sessões | Média | 5 minutos |
| Base de dados SQL | dtu_limit | Limite DTU | Média | 5 minutos |
| Base de dados SQL | dtu_used | DTU utilizado | Média | 5 minutos |
||||||
| Agrupamento elástico | cpu_percent | Percentagem de CPU | Média | 10 minutos |
| Agrupamento elástico | physical_data_read_percent | Percentagem de ES de Dados | Média | 10 minutos |
| Agrupamento elástico | log_write_percent | Percentagem de es do registo | Média | 10 minutos |
| Agrupamento elástico | dtu_consumption_percent | Percentagem de DTU | Média | 10 minutos |
| Agrupamento elástico | storage_percent | Percentagem de armazenamento | Média | 10 minutos |
| Agrupamento elástico | workers_percent | Percentagem de trabalhadores | Média | 10 minutos |
| Agrupamento elástico | eDTU_limit | limite de eDTU | Média | 10 minutos |
| Agrupamento elástico | storage_limit | Limite de armazenamento | Média | 10 minutos |
| Agrupamento elástico | eDTU_used | eDTU utilizado | Média | 10 minutos |
| Agrupamento elástico | storage_used | Armazenamento utilizado | Média | 10 minutos |
||||||               
| O SQL data warehouse | cpu_percent | Percentagem de CPU | Média | 10 minutos |
| O SQL data warehouse | physical_data_read_percent | Percentagem de ES de Dados | Média | 10 minutos |
| O SQL data warehouse | Armazenamento | Tamanho total da base de dados | Máximo | 10 minutos |
| O SQL data warehouse | connection_successful | Ligações com êxito | Total | 10 minutos |
| O SQL data warehouse | connection_failed | Falha de ligações | Total | 10 minutos |
| O SQL data warehouse | blocked_by_firewall | Bloqueado pela Firewall | Total | 10 minutos |
| O SQL data warehouse | service_level_objective | Objetivo de nível de serviço da base de dados | Total | 10 minutos |
| O SQL data warehouse | dwu_limit | limite de dwu | Máximo | 10 minutos |
| O SQL data warehouse | dwu_consumption_percent | Percentagem DWU | Média | 10 minutos |
| O SQL data warehouse | dwu_used | DWU utilizado | Média | 10 minutos |
||||||


## <a name="next-steps"></a>Passos seguintes
* [Obter uma descrição geral da monitorização do Azure](../monitoring-and-diagnostics/monitoring-overview.md) , incluindo os tipos de informações que pode recolher e monitorizar.
* Saiba mais sobre [configurar webhooks alertas](../monitoring-and-diagnostics/insights-webhooks-alerts.md).
* Obter um [descrição geral dos registos de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) e recolher métricas de alta frequência detalhadas do seu serviço.
* Obter um [descrição geral da coleção de métricas](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para se certificar de que o serviço está disponível e reativa.
