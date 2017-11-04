---
title: Integrar os registos de recursos do Azure para os sistemas SIEM | Microsoft Docs
description: "Saiba mais sobre a integração de registos do Azure, as suas capacidades principais e como funciona."
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/10/2017
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 6c3a2ac18fdb7a7a722447af720b9dee28adef08
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Introdução à integração de registo do Microsoft Azure
Saiba mais sobre a integração de registos do Azure, as suas capacidades principais e como funciona.

## <a name="overview"></a>Descrição geral

Integração de registos do Azure é uma solução livre que permite-lhe integrar os registos em bruto dos seus recursos do Azure aos seus sistemas Security Information and Event Management (SIEM) no local.

Integração de registos do Azure recolhe eventos do Windows a partir de registos do Visualizador de eventos do Windows, [registos de atividade do Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), [alertas do Centro de segurança do Azure](../security-center/security-center-intro.md), e [registos de diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) a partir dos recursos do Azure. Esta integração ajuda a sua solução SIEM fornecer um dashboard unificado para todos os seus recursos, no local ou na nuvem, para que pode agregar, correlacionar, analisar e alerta para eventos de segurança.

>[!NOTE]
Neste momento, as nuvens suportadas apenas são comercial do Azure e Azure Government. Não são suportadas outras nuvens.

![Integração de registos do Azure][1]

## <a name="what-logs-can-i-integrate"></a>Os registos que pode a integrar
Azure produz um vasto conjunto registo para cada serviço do Azure. Estes registos representam três tipos de registos:

* **Registos de controlo/gestão** proporcionam visibilidade a [do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) operações criar, ATUALIZAR e eliminar. Os registos de atividade do Azure é um exemplo deste tipo de registo.
* **Dados plane registos** dar visibilidade sobre os eventos gerados como parte da utilização de um recurso do Azure. Um exemplo deste tipo de registo é o Visualizador de eventos de Windows **sistema**, **segurança**, e **aplicação** canais na máquina virtual do Windows. Outro exemplo é configurada por meio do Monitor do Azure de registo de diagnóstico
* **Processar eventos** fornecer eventos analisados e informações de alerta processados em seu nome. Um exemplo deste tipo de evento é Azure Center alertas de segurança, em que Centro de segurança do Azure tem processado e analisados a sua subscrição para fornecer alertas relevantes para a sua postura de segurança atual.

Integração de registo do Azure suporta ArcSight, QRadar e Splunk. Em todas as circunstâncias, verifique junto do fabricante do SIEM para avaliar se tiverem um conector nativo. Em alguns casos, não terá de utilizar a integração de registo do Azure quando conectores nativos estão disponíveis. Para obter informações adicionais no registo tipos suportados visite as FAQ.

>[!NOTE]
Enquanto a integração de registo do Azure é uma solução livre, existem custos de armazenamento do Azure resultante do armazenamento de informações do ficheiro de registo.

A assistência de Comunidade está disponível através de [fórum MSDN do Azure registo integração](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). O Fórum fornece a Comunidade AzLog a possibilidade de suportar entre si com perguntas, respostas, sugestões e truques sobre como obter o máximo partido da integração do registo do Azure. Além disso, a equipa de integração de registo do Azure monitoriza neste fórum e irão ajudá-lo sempre que possível.

Também pode abrir um [pedido de suporte](../azure-supportability/how-to-create-azure-support-request.md). Para tal, selecione **integração de registo** como o serviço para o qual está a pedir suporte.

## <a name="next-steps"></a>Passos seguintes
Neste documento, foram introduzidas para integração de registos do Azure. Para saber mais sobre a integração de registos do Azure e os tipos de registos suportados, consulte o seguinte:

* [Integração de registo do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=53324) – Centro de transferências para obter detalhes, requisitos de sistema e instalar as instruções na integração de registos do Azure.
* [Introdução à integração de registos do Azure](security-azure-log-integration-get-started.md) - este tutorial orienta-o através da instalação de integração de registos do Azure e integrar os registos do armazenamento do Azure WAD, registos de atividade do Azure, alertas do Centro de segurança do Azure e o Azure Active Directory registos de auditoria.
* [Passos de configuração do parceiro](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – este blogue mostra-lhe como configurar a integração de registos do Azure para trabalhar com soluções de parceiros Splunk, HP ArcSight e IBM QRadar. Este blogue representa nosso posição atual sobre como configurar as soluções de parceiros. Em todos os casos, consulte a documentação da solução de parceiros primeiro.
* [Alertas de atividade e ASC ao longo do syslog para QRadar](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/24/integrate-azure-logs-to-qradar/) – esta mensagem de blogue fornece os passos para enviar alertas de atividade e o Centro de segurança do Azure através do syslog para QRadar
* [Registos do Azure integração perguntas mais frequentes (FAQ) do sobre](security-azure-log-integration-faq.md) -FAQ este respondem a dúvidas sobre a integração de registos do Azure.
* [Integrar o Centro de segurança de alertas com o Azure registo integração](../security-center/security-center-integrating-alerts-with-log-integration.md) – este documento mostra como sincronizar alertas do Centro de segurança do Azure com a integração de registo do Azure.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
