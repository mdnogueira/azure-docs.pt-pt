---
title: "Informações sobre ameaças no Centro de Segurança do Azure | Microsoft Docs"
description: "Saiba como utilizar a capacidade de informações sobre ameaças no Centro de Segurança do Azure para identificar potenciais ameaças nas suas VMs e computadores."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: b5bdeb1c70893c2477413df02c7b1a4a07999e93
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="threat-intelligence-in-azure-security-center"></a>Informações sobre ameaças no Centro de Segurança do Azure
Este artigo ajuda-o a utilizar as informações sobre ameaças do Centro de Segurança do Azure para lidar com problemas relacionados com segurança.

## <a name="what-is-threat-intelligence"></a>O que são as informações sobre ameaças?
Ao utilizar a opção de informações sobre ameaças disponível no Centro de Segurança, os administradores de TI podem identificar ameaças de segurança contra o ambiente. Por exemplo, podem identificar se um determinado computador faz parte de um botnet. Os computadores podem tornar-se nós de um botnet quando os atacantes instalam ilicitamente malware que liga secretamente o computador ao comando e controlo. As informações sobre ameaças também podem identificar potenciais ameaças provenientes de canais de comunicação underground, como a dark web.

Para recolher estas informações de ameaças, o Centro de Segurança utiliza dados provenientes de várias origens na Microsoft. O Centro de Segurança utiliza destes dados para identificar potenciais ameaças contra o seu ambiente. O painel **Informações sobre ameaças** é composto por três opções principais:

- Tipos de ameaças detetadas
- Origem das ameaças
- Mapa das informações sobre ameaças


## <a name="when-should-you-use-threat-intelligence"></a>Quando deve utilizar a informação sobre ameaças?
Um dos passos de um [processo de resposta a incidentes de segurança](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) é identificar a gravidade dos sistemas comprometidos. Nesta fase, deve realizar as tarefas seguintes:

- Determinar a natureza do ataque.
- Determinar o ponto de ataque de origem.
- Determinar a intenção do ataque. O ataque foi direcionado à sua organização, para adquirir informações específicas, ou foi aleatório?
- Identificar os sistemas que foram comprometidos.
- Identificar os ficheiros que foram acedidos e determinar a sensibilidade desses ficheiros.

Pode utilizar os dados de informações sobre ameaças no Centro de Segurança para o ajudar com estas tarefas. 

## <a name="access-the-threat-intelligence"></a>Aceder às informações sobre ameaças
Para visualizar as informações sobre ameaças atuais relativamente ao seu ambiente, primeiro tem de selecionar a área de trabalho onde as suas informações residem. Se só tiver uma área de trabalho, ignore o seletor de áreas de trabalho e aceda diretamente ao dashboard **Informações sobre ameaças**. Para aceder ao dashboard:

1. Abra o dashboard **Centro de Segurança**.

2. No painel do lado esquerdo, em **Deteção**, selecione **Informações sobre ameaças**. É apresentado o dashboard das **Informações Sobre Ameaças**.

    ![Dashboard Informações sobre ameaças](./media/security-center-threat-intel/security-center-threat-intel-fig1.png)

    > [!NOTE]
    > Se a coluna mais à direita mostrar **ATUALIZAR PLANO**, esta área de trabalho está a utilizar a subscrição gratuita. Atualize para o plano Standard para utilizar esta funcionalidade. Se a coluna mais à direita mostrar **PRECISA DE ATUALIZAÇÃO**, atualize o [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) para utilizar esta funcionalidade. Para obter mais informações sobre o plano de preços, leia o artigo sobre os preços do Centro de Segurança do Azure. 
    > 
3. Se tiver mais do que uma área de trabalho para investigar, priorize a investigação de acordo com a coluna **IP malicioso**. Mostra o número atual de IPs maliciosos nesta área de trabalho. Selecione a área de trabalho que pretende utilizar e o dashboard **Informações sobre ameaças** é apresentado.

    ![Dados de informações sobre ameaças](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

4. O dashboard está dividido em quatro mosaicos:

    a.  **Tipos de ameaça**. Resume o tipo de ameaças que foram detetadas na área de trabalho selecionada.

    b.  **País de origem**. Agrega a quantidade de tráfego de acordo com a localização de origem.

    c.  **Localização da ameaça**. Ajuda-o a identificar as localizações atuais em todo o mundo que comunicam com o seu ambiente. No mapa apresentado, as setas laranja (recebido) e vermelhas (enviado) identificam as direções de tráfego. Se selecionar uma destas setas, é apresentado o tipo de ameaça e a direção de tráfego.

    d.  **Detalhes da ameaça**. Mostra mais detalhes sobre a ameaça que selecionou no mapa.

Independentemente do mosaico de opção que selecionar, o dashboard que é apresentado baseia-se na consulta de Pesquisa de Registos. A única diferença é o tipo de consulta e o resultado.

### <a name="threat-types"></a>Tipos de ameaças
Selecione o mosaico **Tipos de ameaça** para abrir o dashboard **Pesquisa de Registos**. As opções de filtro aparecem no lado esquerdo e os resultados da consulta aparecem no lado direito.

![Pesquisa de Registos](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

O resultado da consulta mostra as ameaças por nome. Pode utilizar o painel da esquerda para selecionar o atributo que pretende filtrar. Por exemplo, para ver apenas as ameaças que estão atualmente ligadas às máquinas, em **SESSIONSTATE**, selecione **Ligado** > **Aplicar**.

![Estado da Sessão](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

Relativamente às VMs do Azure, só são apresentados no dashboard **Informações sobre ameaças** os dados da rede que fluem através do agente. As informações sobre ameaças também utilizam os tipos de dados seguintes:

- Dados CEF (Type=CommonSecurityLog)
- WireData (Type= WireData)
- Registos do IIS (Type=W3CIISLog)
- Firewall do Windows (Type=WindowsFirewall)
- Eventos de DNS (Type=DnsEvents)


## <a name="see-also"></a>Consultar também
Neste artigo, aprendeu a utilizar as informações sobre ameaças no Centro de Segurança para ajudar a identificar atividade suspeita. Para saber mais sobre o Centro de Segurança, veja os artigos seguintes:

* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerir alertas e responder a incidentes de segurança no Centro de Segurança.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Compreender os alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de resolução de problemas do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como resolver problemas comuns no Centro de Segurança. 
* [Centro de Segurança do Azure FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md). Encontre respostas para as perguntas mais frequentes sobre o serviço.
* [Blogue de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.

