---
title: "Gerir no portal do Azure do Azure DDoS proteção padrão | Microsoft Docs"
description: "Saiba como utilizar o Azure DDoS proteção padrão telemetria no Monitor do Azure para mitigar um ataque."
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2017
ms.author: kumud
ms.openlocfilehash: 5c599b4cc867dbc9a081af3a081195b998f63954
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Gerir no portal do Azure do Azure DDoS proteção padrão

>[!IMPORTANT]
>Azure DDoS proteção Standard (proteção DDoS) está atualmente em pré-visualização. Um número limitado de suporte de recursos do Azure proteção DDoS e num número selecione das regiões. Terá de [registar-se para o serviço](http://aka.ms/ddosprotection) durante a pré-visualização limitada para obter proteção DDoS ativada para a sua subscrição. São contactados pela equipa do Azure DDoS após o registo para ajudá-lo durante o processo de ativação. Proteção DDoS está disponível em regiões EUA leste, EUA oeste e Central EUA oeste. Durante a pré-visualização, não lhe serem cobrados para utilizar o serviço.

Este artigo mostra como utilizar o portal do Azure para ativar a proteção DDoS, desative a proteção DDoS e utilizar a telemetria de correção para atenuar um ataque. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em http://portal.azure.com.

## <a name="enable-ddos-protection"></a>Ativar proteção DDoS

Ative proteção DDoS numa rede virtual nova ou existente.

### <a name="create-a-new-virtual-network-and-enable-ddos-protection"></a>Criar uma nova rede virtual e ativar proteção DDoS

1. Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.
2. Selecione **redes**e, em seguida, selecione **rede Virtual**.
3. Introduza as informações de rede virtual. Em *proteção DDoS*, clique em **ativado**e, em seguida, clique em **criar**.

    ![Criar a rede virtual](./media/ddos-protection-manage-portal/ddos-create-vnet.png)   

Um aviso indica que a ativação da proteção DDoS implica os encargos. Não existem custos de proteção DDoS são cobrados durante a pré-visualização. São cobradas taxas na disponibilidade geral (GA) e os clientes receberão o aviso de 30 dias antes do início de encargos e depois da disponibilidade geral

### <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>Ativar proteção DDoS numa rede virtual existente 

1. Clique em **redes virtuais** no menu do portal do Azure e, em seguida, selecione a rede virtual.
2. Clique em **proteção DDoS**, clique em **ativado** no *proteção DDoS* ecrã e, em seguida, clique em **guardar**. 

Um aviso indica que a ativação da proteção DDoS implica os encargos. Não existem custos de proteção DDoS são cobrados durante a pré-visualização. São cobradas taxas na disponibilidade geral (GA) e os clientes receberão o aviso de 30 dias antes do início de encargos e depois da disponibilidade geral

## <a name="disable-ddos-protection"></a>Desativar proteção DDoS

1. Clique em **redes virtuais** no menu do portal do Azure e, em seguida, selecione a rede virtual.
2. Clique em **proteção DDoS**, clique em **desativado** no *proteção DDoS* ecrã e, em seguida, clique em **guardar**.

## <a name="configure-alerts-on-ddos-protection-metrics"></a>Configure alertas nas métricas de proteção DDoS

Tirar partido da configuração de alerta do Monitor do Azure, pode selecionar qualquer uma das métricas de proteção DDoS disponíveis para o alertar quando há uma mitigação ativa durante um ataque. Quando são satisfeitas as condições, receberá um e-mail de alerta no endereço especificado.

1. Clique em **Monitor**e, em seguida, clique em **métricas**.
2. No *métricas* ecrã, selecione o grupo de recursos, o tipo de recurso dos **endereço IP público**e o IP público do Azure.
3. Para configurar um alerta de e-mail para uma métrica, clique em **clique para adicionar um alerta**. Um alerta de e-mail pode ser criado em qualquer métrica, mas a métrica mais óbvias é **ataques de DDoS em ou não**. Este é um valor booleano 1 ou 0. A **1** significa que está sob ataque. A **0** significa que não está sob ataque.
4. Para ser enviado por e-mail quando sob ataque, definir a métrica para **ataques de DDoS em ou não** e **condição para a maior que zero (0) nos últimos 5 minutos**. Alertas semelhantes podem ser configuradas para outras métricas.

    ![Configurar as métricas](./media/ddos-protection-manage-portal/ddos-metrics.png)

    Dentro de alguns minutos de deteção de ataques, será notificado através de métricas de Monitor do Azure.

    ![Alerta de ataque](./media/ddos-protection-manage-portal/ddos-alert.png) 

Também pode saber mais sobre [configurar webhooks](../monitoring-and-diagnostics/insights-webhooks-alerts.md) e [as logic apps](../logic-apps/logic-apps-what-are-logic-apps.md) para a criação de alertas.

## <a name="configure-logging-on-ddos-protection-metrics"></a>Configurar o registo nas métricas de proteção DDoS

1. Clique em **Monitor**e, em seguida, clique em **definições de diagnóstico**.
2. No *métricas* ecrã, selecione o grupo de recursos, o tipo de recurso dos **endereço IP público**e o IP público do Azure.
3. Clique em **ative os diagnósticos para recolher os seguintes dados**.

Existem três opções disponíveis para o registo:

- **Arquivo para uma conta de armazenamento** – escreve registos para uma conta de armazenamento.
- **Fluxo para um hub de eventos** – permite que um recetor de registo recolher registos com um hub de eventos. Isto permite a integração com Splunk ou outros sistemas SIEM.
- **Enviar ao Log Analytics** – escreve registos ao serviço de análise de registos do Azure OMS.

## <a name="use-ddos-protection-telemetry"></a>Utilize a telemetria de proteção DDoS

Um ataque a telemetria é fornecida por meio do Monitor do Azure em tempo real. A telemetria está disponível apenas para a duração que é um endereço IP público em mitigação. Não verá telemetria antes ou depois de um ataque é atenuado.

1. Clique em **Monitor**e, em seguida, clique em **métricas**. 
2. No *métricas* ecrã, selecione o grupo de recursos, o tipo de recurso dos **endereço IP público**e o IP público do Azure. Uma série de métricas disponíveis é apresentado no lado esquerdo do ecrã. Estas métricas quando selecionado, são graphed no gráfico de métricas de Monitor do Azure no ecrã descrição geral. 

Os nomes de métricos apresentam os tipos de pacote diferente e bytes vs pacotes, com uma construção básica dos nomes de etiqueta em cada métrica da seguinte forma:

- **Nome da tag dropped (por exemplo, de entrada pacotes ignorados DDoS distribuídos):** o número de pacotes ignorados/removida pelo sistema de proteção DDoS.
- **O nome da etiqueta forwarded (por exemplo: DDoS de reencaminhados de pacotes de entrada):** o número de pacotes reencaminhados pelo sistema DDoS para o VIP – tráfego que não foi filtrado de destino.
- **Nenhum nome de etiqueta (por exemplo: DDoS de pacotes de entrada):** o número total de pacotes fornecido para o sistema limpeza – que representa a soma de pacotes ignorados e reencaminhados.

## <a name="next-steps"></a>Passos seguintes

- [Leia mais sobre os registos de diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
- [Analisar os registos do armazenamento do Azure com a análise de registos](../log-analytics/log-analytics-azure-storage.md)
- [Introdução ao Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)