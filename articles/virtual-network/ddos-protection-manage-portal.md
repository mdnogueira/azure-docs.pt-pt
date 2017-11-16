---
title: "Gerir no portal do Azure do Azure DDoS proteção padrão | Microsoft Docs"
description: "Saiba como utilizar o Azure DDoS proteção padrão telemetria no Monitor do Azure para mitigar um ataque."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: 019d4ba9124173a7de555c46d32881ecf639a34c
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Gerir no portal do Azure do Azure DDoS proteção padrão

Saiba como ativar e desativar distribuída recusa de protection service (DDoS distribuídos) e utilizar a telemetria de correção para atenuar um ataque DDoS com o Azure DDoS proteção Standard. Padrão de proteção DDoS protege os recursos do Azure como máquinas virtuais, balanceadores de carga e gateways de aplicação com um Azure [endereço IP público](virtual-network-public-ip-address.md) atribuída. Para saber mais sobre DDoS proteção padrão e das respetivas funções, consulte o artigo [descrição geral do padrão de proteção DDoS](ddos-protection-overview.md). 

>[!IMPORTANT]
>Azure DDoS proteção Standard (proteção DDoS) está atualmente em pré-visualização. Proteção DDoS de suportar um número limitado de recursos do Azure e está disponível apenas num número selecionado de regiões. Para obter uma lista de regiões disponíveis, consulte [descrição geral do padrão de proteção DDoS](ddos-protection-overview.md). Terá de [registar-se para o serviço](http://aka.ms/ddosprotection) durante a pré-visualização limitada para obter proteção DDoS ativada para a sua subscrição. Depois de registar, são contactados pela equipa do Azure DDoS que irá guiá-lo durante o processo de ativação. 

## <a name="enable-ddos-protection-standard---new-virtual-network"></a>Ativar o padrão de proteção DDoS - nova rede virtual

1. Inicie sessão no portal do Azure em http://portal.azure.com. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
2. Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.
3. Selecione **redes**e, em seguida, selecione **rede Virtual**.
4. Crie uma rede virtual com as definições que escolheu. Para obter mais informações sobre a criação de redes virtuais, consulte [criar uma rede virtual](virtual-networks-create-vnet-arm-pportal.md). Em *proteção DDoS*, clique em **ativado**e, em seguida, clique em **criar**.

    ![Criar a rede virtual](./media/ddos-protection-manage-portal/ddos-create-vnet.png)   

    > [!WARNING]
    > Quando seleciona uma região, escolha uma região suportada na lista na [descrição geral do Azure DDoS proteção padrão](ddos-protection-overview.md).

    Um aviso indica que a ativação da proteção DDoS implica os encargos. Não existem custos de proteção DDoS são cobrados durante a pré-visualização. Os encargos serão cobrado na disponibilidade geral. Receberá aviso de 30 dias, antes do início de encargos e disponibilidade geral.

## <a name="enable-ddos-protection-standard---existing-virtual-network"></a>Ativar o padrão de proteção DDoS - rede virtual existente 

1. Clique em **redes virtuais** no menu do portal do Azure e, em seguida, selecione a rede virtual.
2. Clique em **proteção DDoS**, clique em **ativado** no *proteção DDoS* ecrã e, em seguida, clique em **guardar**. 

    > [!WARNING]
    > A rede virtual tem de existir numa região suportada. Para obter uma lista de regiões suportadas, consulte [descrição geral do Azure DDoS proteção padrão](ddos-protection-overview.md).

    Um aviso indica que a ativação da proteção DDoS implica os encargos. Não existem custos de proteção DDoS são cobrados durante a pré-visualização. Os encargos serão cobrado na disponibilidade geral. Receberá aviso de 30 dias, antes do início de encargos e disponibilidade geral.

## <a name="disable-ddos-protection-on-a-virtual-network"></a>Desativar proteção DDoS numa rede virtual

1. Clique em **redes virtuais** no menu do portal do Azure e, em seguida, selecione a rede virtual.
2. Clique em **proteção DDoS**, clique em **desativado** no *proteção DDoS* ecrã e, em seguida, clique em **guardar**.

## <a name="configure-alerts-on-ddos-protection-metrics"></a>Configure alertas nas métricas de proteção DDoS

Pode selecionar qualquer uma das métricas de proteção DDoS disponíveis para o alertar quando há uma mitigação durante um ataque ao utilizar a configuração de alerta de Monitor do Azure Active Directory. Quando as condições são cumpridas, o endereço especificado recebe um e-mail de alerta.

1. Clique em **Monitor**e, em seguida, clique em **métricas**.
2. No *métricas* ecrã, selecione o grupo de recursos, o tipo de recurso dos **endereço IP público**e o endereço IP público do Azure.
3. Para configurar um alerta de e-mail para uma métrica, clique em **Adicionar alerta métrica**. Um alerta de e-mail pode ser criado em qualquer métrica, mas a métrica mais óbvias é **ataques de DDoS em ou não**. Este é um valor booleano 1 ou 0. A **1** significa que está sob ataque. A **0** significa que não está sob ataque.
4. Para ser enviado por e-mail quando sob ataque, definir a métrica para **ataques de DDoS em ou não** e **condição para a maior que zero (0) nos últimos 5 minutos**. Alertas semelhantes podem ser configuradas para outras métricas.

    ![Configurar as métricas](./media/ddos-protection-manage-portal/ddos-metrics.png)

    Dentro de alguns minutos de deteção de ataques, será notificado através de métricas de Monitor do Azure.

    ![Alerta de ataque](./media/ddos-protection-manage-portal/ddos-alert.png) 

Também pode saber mais sobre [configurar webhooks](../monitoring-and-diagnostics/insights-webhooks-alerts.md) e [as logic apps](../logic-apps/logic-apps-what-are-logic-apps.md) para a criação de alertas.

## <a name="configure-logging-on-ddos-protection-standard-metrics"></a>Configurar o registo no métricas DDoS proteção padrão

1. Clique em **Monitor**e, em seguida, clique em **definições de diagnóstico**.
2. No *métricas* ecrã, selecione o grupo de recursos, o tipo de recurso dos **endereço IP público**e o endereço IP público do Azure.
3. Clique em **ative os diagnósticos para recolher os seguintes dados**.

Existem três opções disponíveis para o registo:

- **Arquivo para uma conta de armazenamento**: escreve registos para uma conta de armazenamento.
- **Fluxo para um hub de eventos**: permite que um recetor de registo recolher registos com um hub de eventos. Isto permite a integração com Splunk ou outros sistemas SIEM.
- **Enviar ao Log Analytics**: escreve registos para o serviço de análise de registos do Azure OMS.

## <a name="use-ddos-protection-telemetry"></a>Utilize a telemetria de proteção DDoS

Um ataque a telemetria é fornecida por meio do Monitor do Azure em tempo real. A telemetria está disponível apenas para a duração que é um endereço IP público em mitigação. Não verá telemetria antes ou depois de um ataque é atenuado.

1. Clique em **Monitor**e, em seguida, clique em **métricas**. 
2. No *métricas* ecrã, selecione o grupo de recursos, o tipo de recurso dos **endereço IP público**e o endereço IP público do Azure. Uma série de **as métricas disponíveis** aparece no lado esquerdo do ecrã. Estas métricas, quando selecionado, são graphed no **gráfico de métricas de Monitor de Azure** no ecrã descrição geral. 

Os nomes de métricos apresentam tipos de pacote diferente e bytes vs pacotes, com uma construção básica dos nomes de etiqueta em cada métrica da seguinte forma:

- **Nome da tag dropped (por exemplo, de entrada pacotes ignorados DDoS distribuídos)**: O número de pacotes ignorados/removida pelo sistema de proteção DDoS.
- **O nome da etiqueta forwarded (por exemplo: DDoS de reencaminhados de pacotes de entrada)**: O número de pacotes reencaminhados pelo sistema DDoS para o VIP – tráfego que não foi filtrado de destino.
- **Nenhum nome de etiqueta (por exemplo: DDoS de pacotes de entrada):** o número total de pacotes fornecido para o sistema limpeza – que representa a soma de pacotes ignorados e reencaminhados.

## <a name="next-steps"></a>Passos seguintes

- [Leia mais sobre os registos de diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Analisar os registos do armazenamento do Azure com a análise de registos](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Introdução ao Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md?toc=%2fazure%2fvirtual-network%2ftoc.json)