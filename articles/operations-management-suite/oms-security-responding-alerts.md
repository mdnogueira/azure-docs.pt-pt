---
title: "Monitorizar e responder a alertas de segurança no Operations Management Suite segurança e a solução de auditoria | Microsoft Docs"
description: "Este documento ajuda-o a utilizar a opção de intelligence de ameaça disponível na auditoria e segurança do OMS para monitorizar e responder a alertas de segurança."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 7d45a32b-1341-4bb5-a436-1f42a8a2590a
ms.service: operations-management-suite
ms.custom: oms-security
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/13/2017
ms.author: yurid
ms.openlocfilehash: df82afab2c38431e134146143524edc080ee38f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-and-responding-to-security-alerts-in-operations-management-suite-security-and-audit-solution"></a>Monitorizar e responder a alertas de segurança no Operations Management Suite segurança e a solução de auditoria
Este documento ajuda-o a utilizar a opção de intelligence de ameaça disponível na auditoria e segurança do OMS para monitorizar e responder a alertas de segurança.

## <a name="what-is-oms"></a>O que é o OMS?
Microsoft Operations Management Suite (OMS) é a solução de gestão de IT que ajuda a gerir e proteger no local e a infraestrutura de nuvem de baseada na nuvem da Microsoft. Para mais informações sobre o OMS, leia o artigo [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="threat-intelligence"></a>Informações sobre ameaças
Num ambiente empresarial em que os utilizadores têm acesso abrangente à rede e utilizar uma variedade de dispositivos para ligar a dados empresariais, é imperativo que pode monitorizar ativamente os recursos e responder rapidamente a incidentes de segurança. Esta é determinado importante da perspetiva de ciclo de vida de segurança porque algumas atuais ameaças não podem gerar alertas ou atividades suspeitas que podem ser identificadas pelos controlos técnicos de segurança tradicionais. 

Utilizando o **ameaças** opção disponível na auditoria e segurança do OMS, os administradores de TI podem identificar ameaças de segurança contra o ambiente, por exemplo, identificar se um determinado computador faz parte de um [ botnet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection). Os computadores podem tornar-se nós de um botnet quando os atacantes instalam ilicitamente malware que liga secretamente este computador ao comando e controlo. Também poderá identificar potenciais ameaças proveniente de canais de comunicação underground, tais como [darknet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection_honeypots_darkents). 

Para criar este ameaças, a solução de auditoria e segurança do OMS utiliza dados provenientes de várias origens na Microsoft. Tirar partido destes dados para identificar potenciais ameaças no ambiente de auditoria e segurança do OMS.

O painel Informações Sobre Ameaças é composto por três opções principais:

* Servidores com tráfego malicioso de saída
* Tipos de ameaças detetadas
* Mapa das informações sobre ameaças

> [!NOTE]
> Para obter uma descrição geral de todas estas opções, leia o artigo [introdução ao Operations Management Suite segurança e a solução de auditoria](oms-security-getting-started.md).
> 
> 

### <a name="responding-to-security-alerts"></a>Responder a alertas de segurança
Um dos passos de uma [resposta a incidentes segurança](https://technet.microsoft.com/library/cc512623.aspx) processo consiste em identificar a gravidade dos sistemas de compromisso. Nesta fase, deve efetuar as seguintes tarefas:

* Determinar a natureza do ataque
* Determinar o ponto de ataque de origem
* Determinar a intenção do ataque. O ataque foi direcionado concretamente à sua organização, para adquirir informações específicas, ou foi aleatório?
* Identificar os sistemas que tenham sido comprometidos
* Identificar os ficheiros que tenham sido acedidos e determinam a sensibilidade desses ficheiros

Pode tirar partido **ameaças** informações na solução de segurança do OMS e de auditoria para o ajudar com estas tarefas. Siga os passos abaixo para aceder a estes **ameaças** opções:

1. No dashboard principal do **Microsoft Operations Management Suite** , clique em mosaico de **Segurança e Auditoria**.
   
    ![Auditoria e segurança](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)
2. No **auditoria e segurança** dashboard, verá o **ameaças** opções no lado direito, conforme mostrado abaixo:
   
    ![Intel de ameaça](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig2-ga.png)

Estes três mosaicos irão dar-lhe uma descrição geral de ameaças atuais. No **servidor com tráfego malicioso de saída** conseguirá identificar se não houver qualquer computador que está a monitorizar (dentro ou fora da rede) que está a enviar o tráfego malicioso à Internet. 

O **detetados tipos de ameaças** mosaico mostra um resumo de ameaças que estão atualizadas "no caráter", se clicar neste mosaico, verá mais detalhes sobre estas ameaças como mostrados abaixo:

![Tipos de ameaças detetadas](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig3.png)

Pode extrair mais informações sobre cada ameaça clicando nela. O exemplo abaixo mostra mais detalhes sobre Botnet:

![obter mais detalhes sobre uma ameaça](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig4.png)

Tal como descrito no início desta secção, estas informações podem ser bastante útil durante um caso de resposta a incidentes. Pode também ser importante durante uma investigação forense, onde tem de localizar a origem do ataque, sistema que foi comprometido e a linha cronológica. Este relatório possa identificar facilmente alguns detalhes chaves sobre o ataque, tais como: a origem do ataque, o IP local que ficou comprometido e o estado da sessão atual da ligação. 

O **mapa de intelligence ameaça** irá ajudá-lo a identificar as localizações atuais em torno de todo o mundo com tráfego malicioso. Existem laranja (recebido) e vermelhos (saídas) setas neste mapa que identificam a direção do tráfego, se clicar destes setas, esta operação irá mostrar o tipo de ameaça e a direção do tráfego conforme mostrado abaixo:

![mapa de informações sobre ameaças](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig5.png)

> [!NOTE]
> Pode ver uma demonstração sobre como utilizar esta capacidade durante um processo de resposta a incidentes por ver a apresentação [mitigar ameaças de segurança do Centro de dados com investigação orientada utilizando o Operations Management Suite](https://myignite.microsoft.com/videos/5000) entregar no Microsoft Ignite.
> 

### <a name="responding-to-distinct-malicious-ip-accessed"></a>Responder a IP malicioso distinto acedido
Em alguns cenários, pode observar um IP potencialmente mal-intencionado que foi acedido a partir de um computador monitorizado:

![mapa de informações sobre ameaças](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig6.PNG)

Este alerta e outros dentro da mesma categoria, são gerados através da Segurança do OMS, ao tirar partido das [Informações sobre Ameaças da Microsoft](https://youtu.be/O4WtxgUrDc8). Os dados das Informações sobre Ameaças são recolhidos pela Microsoft e são comprados aos principais fornecedores de inteligência de ameaças. Estes dados são atualizados com frequência e adaptados para ameaças rápidas. Devido à sua natureza, devem ser combinados com outras fontes de informações de segurança ao [investigar](https://blogs.technet.microsoft.com/msoms/2016/12/08/investigating-suspicious-activity-in-a-hybrid-cloud-with-oms-security/) um alerta de segurança. 

## <a name="customize-alerts-received-via-e-mail"></a>Personalizar alertas recebidas através de correio electrónico

Pode personalizar quais os utilizadores na sua organização serão notificados quando os alertas de segurança são acionados pela segurança do OMS. Esta opção está disponível em Descrição geral / definições no dashboard do OMS:

![E-mail](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig7.png)

## <a name="see-also"></a>Consultar também
Neste documento, aprendeu a utilizar o **ameaças** opção na solução de auditoria e segurança do OMS para responder a alertas de segurança. Para saber mais sobre a Segurança do OMS, veja os artigos seguintes:

* [Descrição geral do Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Introdução ao Operations Management Suite segurança e a solução de auditoria](oms-security-getting-started.md)
* [Recursos de Monitorização na Solução de Segurança e Auditoria do Operations Management Suite](oms-security-monitoring-resources.md)

