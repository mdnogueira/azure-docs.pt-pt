---
title: "Alerta de gestão no Microsoft monitorização produtos | Microsoft Docs"
description: "Um alerta indica algum problema que necessita de atenção a partir de um administrador.  Este artigo descreve as diferenças na forma como alertas são criados e geridos no System Center Operations Manager (SCOM) e análise de registos e apresenta as melhores práticas no tirar partido os dois produtos para uma estratégia de gestão de alertas híbrida."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6572c3f8-78ca-4fa9-8fe1-d0b488590788
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 7df2fd7ef838465a60e3b0ce2f889127b7487684
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="managing-alerts-with-microsoft-monitoring"></a>Gerir alertas com a monitorização da Microsoft
Um alerta indica algum problema que necessita de atenção a partir de um administrador.  Existem diferenças distintas entre o System Center Operations Manager (SCOM) e análise de registos no Operations Management Suite (OMS) em termos de como os alertas são criados, como são geridos e analisados e como são notificados que foi um problema crítico detetada.

## <a name="alerts-in-operations-manager"></a>Alertas no Operations Manager
Os alertas no SCOM são gerados por regras individuais ou monitores para indicar um problema específico.  Um monitor pode gerar um alerta quando os mesmos entrarão num Estado de erro enquanto uma regra pode gerar um alerta que indica algum problema crítico que não está diretamente relacionada com o estado de funcionamento de um objeto gerido.  Pacotes de gestão incluem uma variedade de fluxos de trabalho que criam alertas para a aplicação ou serviço por eles geridos.  Parte do processo de configuração de um novo pacote de gestão é otimização de modo a garantir que não recebe alertas excessivos para problemas que não considerar críticos.

![Vista de alerta do SCOM](media/operations-management-suite-monitoring-alerts/scom-alert-view.png)

SCOM fornece gestão de alertas completa com alertas de estado que pode ser alterado por administradores, à medida que trabalham para resolver o problema.  Quando o problema foi resolvido, o administrador define o alerta a fechado em que momento já não será apresentada nas vistas a apresentar os alertas ativos.  Alertas que são gerados a partir de monitores podem ser resolvidos automaticamente quando a monitorização regressar ao bom estado de funcionamento.

![Estado do alerta](media/operations-management-suite-monitoring-alerts/scom-alert-status.png)

## <a name="alerts-in-log-analytics"></a>Alertas na análise de registos
É criado um alerta na análise de registos por uma consulta de registo que é executada automaticamente em intervalos regulares.  Pode criar uma regra de alerta a partir de qualquer consulta de registo.  Se a consulta devolve resultados correspondentes aos critérios que especificar, em seguida, é criado um alerta.  Isto pode ser uma consulta específica que cria um alerta se for detetado um evento específico, ou pode utilizar uma consulta mais geral que procura qualquer evento de erro relacionados com a uma determinada aplicação.

Alertas de análise do registo são escritas para o repositório do OMS como um evento e podem ser obtidas com uma consulta de registo.  Não têm um Estado como eventos do SCOM para que pode indicar quando o problema foi resolvido.

![Alerta do OMS](media/operations-management-suite-monitoring-alerts/oms-alert.png)

Quando o SCOM é utilizado como uma origem de dados para análise de registos, alertas do SCOM são escritas para o repositório do OMS são criadas e modificadas.  

![Alerta do SCOM](media/operations-management-suite-monitoring-alerts/scom-alert.png)

O [solução de gestão de alertas](http://technet.microsoft.com/library/mt484092.aspx) fornece um resumo dos alertas ativos e várias consultas comuns para obter conjuntos diferentes de alertas.  Este procedimento fornece uma análise mais eficaz dos alertas que um relatório no SCOM.  Pode desagregar dos resumos para dados detalhados e criar consultas ad hoc para obter conjuntos diferentes de alertas.

![Solução de gestão de alertas](media/operations-management-suite-monitoring-alerts/alert-management.png)

## <a name="notifications"></a>Notificações
Notificações no SCOM enviam-lhe um e-mail ou texto em resposta a alertas que correspondam aos critérios específicos.  Pode criar subscrições de notificação diferentes que tenham diferentes pessoas notificadas consoante esses critérios, como o objeto a ser monitorizado, a gravidade do alerta, o tipo de problema detetado ou a hora do dia.

Algumas subscrições podem ser usadas para implementar uma estratégia completa de notificação para um grande número de pacotes de gestão.

![Alertas do SCOM](media/operations-management-suite-monitoring-alerts/alerts-overview-scom.png)

Análise de registos pode notificá-lo através de correio que foi criado um alerta, definindo uma ação de notificação de e-mail em cada [regra de alerta](http://technet.microsoft.com/library/mt614775.aspx).  Não tem a capacidade do SCOM a subscrever para vários alertas com uma única regra.  Também terá de criar as suas próprias regras de alerta, uma vez que o OMS não fornece quaisquer pré-configurada.

![Alertas do Log Analytics](media/operations-management-suite-monitoring-alerts/alerts-overview-oms.png)

Não é possível completamente gerir alertas do SCOM na análise de registos entanto, uma vez que apenas pode modificá-las na consola de operações.  Análise de registos é útil como parte de uma gestão de alertas apesar processar para fornecer ferramentas de análise que SCOM individualmente não tem.

## <a name="alert-remediation"></a>Remediação de alerta
[Remediação](http://technet.microsoft.com/library/mt614775.aspx) refere-se a uma tentativa para corrigir automaticamente o problema identificado por um alerta.

SCOM permite-lhe executar diagnósticos e recuperações em resposta a um monitor de introduzir um mau estado de funcionamento.  Isto acontece simultâneas para o monitor de criação do alerta.  Os diagnósticos e recuperações, normalmente, são implementadas como um script que é executado no agente.  Um diagnóstico tenta recolher mais informações sobre o problema detetado durante uma recuperação tenta corrigir o problema.

Análise de registos permite-lhe iniciar uma [runbook de automatização do Azure](https://azure.microsoft.com/documentation/services/automation/) ou chamar um webhook em resposta a um alerta de análise de registos.  Os Runbooks podem conter lógica complexa implementada no PowerShell.  O script é executado no Azure e pode aceder a quaisquer recursos do Azure ou recursos externos disponíveis da nuvem.  A automatização do Azure têm a capacidade de executar runbooks num servidor no seu datacenter local, mas esta funcionalidade não está atualmente disponível ao iniciar o runbook em resposta a alertas de análise de registos.

Tanto as recuperações no SCOM e runbooks no OMS pode conter scripts do PowerShell, mas as recuperações são mais difíceis de criar e gerir porque tem de estar contidas dentro de um pacote de gestão.  Os Runbooks são armazenados na automatização do Azure que fornece funcionalidades para a criação, teste e gerir runbooks.

Se utilizar o SCOM como uma origem de dados para análise de registos, pode criar um alerta de análise de registos com uma consulta de registo para obter os alertas do SCOM armazenadas no repositório de OMS.  Isto permitiria que a execução de um runbook de automatização do Azure em resposta a um alerta do SCOM.  Obviamente, uma vez que o runbook será executado no Azure, este não seria uma estratégia viável para recuperações de problemas no local.

## <a name="next-steps"></a>Passos seguintes
* Saiba os detalhes de [alertas no System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh212913.aspx).

