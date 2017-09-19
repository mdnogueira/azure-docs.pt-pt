---
title: "Informações Sobre Ameaças no Centro de Segurança do Azure| Microsoft Docs"
description: "Este documento ajuda-o a utilizar a capacidade de informações sobre ameaças no Centro de Segurança do Azure para identificar potenciais ameaças nas suas VMs e nos seus computadores."
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
ms.date: 09/13/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: c492662aa3ee444347c55d9771790573ad969150
ms.contentlocale: pt-pt
ms.lasthandoff: 09/14/2017

---
# <a name="threat-intelligence-in-azure-security-center"></a>Informações Sobre Ameaças no Centro de Segurança do Azure
Este documento ajuda-o a utilizar as Informações Sobre Ameaças do Centro de Segurança do Azure para lidar com problemas relacionados com segurança.

## <a name="what-is-threat-intelligence"></a>O que são as informações sobre ameaças?
Ao utilizar a opção de Informações de Ameaças disponível no Centro de Segurança, os administradores de TI podem identificar ameaças de segurança contra o ambiente, como, por exemplo, identificar se um determinado computador faz parte de um botnet. Os computadores podem tornar-se nós de um botnet quando os atacantes instalam ilicitamente malware que liga secretamente este computador ao comando e controlo. Também poderá identificar potenciais ameaças provenientes de canais de comunicação underground, como o darknet.

De modo a recolher as informações de ameaças, o Centro de Segurança utiliza dados provenientes de várias origens na Microsoft. O Centro de Segurança tira partido destes dados para identificar potenciais ameaças contra o seu ambiente. O painel Informações Sobre Ameaças é composto por três opções principais:

- Tipos de ameaças detetadas
- Origem das ameaças
- Mapa das informações sobre ameaças


## <a name="when-should-i-use-threat-intelligence"></a>Quando devo utilizar a informação sobre ameaças?
Um dos passos de um [processo de resposta a incidentes de segurança](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) é identificar a gravidade dos sistemas comprometidos. Nesta fase, deve realizar as tarefas seguintes:

- Determinar a natureza do ataque
- Determinar o ponto de ataque de origem
- Determinar a intenção do ataque. O ataque foi direcionado concretamente à sua organização, para adquirir informações específicas, ou foi aleatório?
- Identificar os sistemas que tenham sido comprometidos
- Identificar os ficheiros que foram acedidos e determinar a confidencialidade dos mesmos. As Informações Sobre Ameaças no Centro de Segurança podem ajudá-o a realizar estas tarefas. 

## <a name="how-to-access-the-threat-intelligence"></a>Como posso aceder às informações sobre ameaças?
Para ver as informações sobre ameaças atuais relativamente ao seu ambiente, tem de, primeiro, selecionar a estação de trabalho na qual as suas informações residem. Se só tiver uma área de trabalho, não verá o seletor de áreas de trabalho e irá diretamente para o dashboard das **Informações Sobre Ameaças**. Siga os passos abaixo para aceder ao dashboard das informações sobre ameaças:

1.  Abra o dashboard do **Centro de Segurança**.
2.  No painel do lado esquerdo, em **Deteção**, clique em **Informações Sobre Ameaças**. É apresentado o dashboard das **Informações Sobre Ameaças**.

    ![Informações sobre Ameaças](./media/security-center-threat-intel/security-center-threat-intel-fig1.png)

    > [!NOTE]
    > Se a última coluna mostrar **ATUALIZAR PLANO**, tal deve-se ao facto de a área de trabalho utilizar a subscrição gratuita e terá de atualizar para a versão standard para utilizar esta funcionalidade. Se mostrar REQUER ATUALIZAÇÃO, tem de atualizar para o [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) para a utilizar. Para obter mais informações sobre o plano de preços, leia o artigo sobre os preços do Centro de Segurança do Azure. 
    > 
3. Se tiver mais de uma área de trabalho a investigar, pode priorizar a investigação de acordo com a coluna **IP MALICIOSO**, que mostra o número atual de IPs maliciosos nessa área de trabalho. Selecione a área de trabalho que pretende utilizar e o dashboard das **Informações Sobre Ameaças** é apresentado.

    ![Informações sobre Ameaças](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

4. Este dashboard está dividido em quatro mosaicos:
    * **Tipos de ameaças**: resume o tipo de ameaças que foram detetadas na área de trabalho selecionada.
    * **País de origem**: agrega a quantidade de tráfego de acordo com a localização de origem.
    * **Localização da ameaça**: ajuda-o a identificar as localizações atuais em todo o mundo que comunicam com o seu ambiente. Este mapa tem setas cor de laranja (de entrada) e vermelhas (de saída), as quais identificam a direção do tráfego. Se clicar numa destas setas, verá o tipo de ameaça e a direção do tráfego.
    * **Detalhes da ameaça**: mostra mais detalhes sobre a ameaça que selecionou no mapa.

Independentemente do mosaico de opção que selecionar, o dashboard que vai ser apresentado baseia-se na consulta [Pesquisa de Registos](https://docs.microsoft.com/azure/security-center/security-center-search), sendo que a única diferença é o tipo de consulta e o resultado.

### <a name="threat-types"></a>Tipos de ameaças
Quando clicar no mosaico **Tipos de ameaças**, o dashboard da **Pesquisa de Registos** abre-se e disponibiliza as opções de filtro à esquerda e os resultados da consulta à direita.

![Pesquisa das informações sobre ameaças](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

O resultado da pesquisa agrega as ameaças por nome. Pode utilizar o painel do lado esquerdo para selecionar o atributo que pretende filtrar. Por exemplo, se quiser ver apenas as ameaças que estão atualmente ligadas às máquinas, selecione **Ligadas**, em **SESSIONSTATE** e clique no botão **Aplicar**,

![Estado da sessão](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

Relativamente às VMs do Azure, só são apresentados no dashboard das informações sobre ameaças os dados da rede que fluem através do agente. As informações sobre ameaças também utilizam os tipos de dados seguintes:

- Dados CEF (Type=CommonSecurityLog)
- WireData (Type= WireData)
- Registos do IIS (Type=W3CIISLog)
- Firewall do Windows (Type=WindowsFirewall)
- Eventos de DNS (Type=DnsEvents)


## <a name="see-also"></a>Consultar também
Neste documento, aprendeu a utilizar as informações sobre ameaças no Centro de Segurança para ajudar a identificar a atividade suspeita. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerir alertas e responder a incidentes de segurança no Centro de Segurança.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Compreender os alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de Resolução de Problemas do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como resolver problemas comuns no Centro de Segurança. 
* [Centro de Segurança do Azure FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md). Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.


