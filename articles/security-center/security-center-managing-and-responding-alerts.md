---
title: "Gerir alertas de segurança no Centro de Segurança do Azure | Microsoft Docs"
description: "Este documento ajuda-o a utilizar as capacidades de Centro de Segurança do Azure para gerir e responder a alertas de segurança."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2017
ms.author: yurid
ms.openlocfilehash: a1ea4007f7399dff6da9d66931e1f5366942be0c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="managing-and-responding-to-security-alerts-in-azure-security-center"></a>Gerir e responder a alertas de segurança no Centro de Segurança do Azure
Este documento ajuda-o a utilizar o Centro de Segurança do Azure para gerir e responder a alertas de segurança.

> [!NOTE]
> Para ativar as deteções avançadas, atualize para o Centro de Segurança do Azure Standard. Está disponível uma avaliação gratuita de 60 dias. Para atualizar, selecione Escalão de Preço na [Política de Segurança](security-center-policies.md). Veja [Preços do Centro de Segurança do Azure](security-center-pricing.md) para saber mais.
>
>

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?
O Centro de Segurança recolhe, analisa e integra automaticamente dados de registo a partir dos seus recursos do Azure, da rede e soluções de parceiros ligadas, tal como soluções de proteção de ponto final e firewall, para detetar ameaças reais e reduzir os falsos positivos. Uma lista de alertas de segurança prioritários é apresentada no Centro de Segurança juntamente com as informações necessárias para investigar rapidamente o problema e fornecer recomendações sobre como remediar um ataque.


> [!NOTE]
> Para obter mais informações sobre como funcionam as capacidades de deteção do Centro de Segurança, leia [Capacidades de Deteção do Centro de Segurança do Azure](security-center-detection-capabilities.md).
>
>

## <a name="managing-security-alerts"></a>Gerir alertas de segurança
Pode rever os alertas atuais ao observar o mosaico **Alertas de segurança**. Siga os passos abaixo para ver mais detalhes sobre cada alerta:

1. No dashboard do Centro de Segurança, verá o mosaico **Alertas de segurança**.

    ![Mosaico Alertas de segurança no Centro de Segurança](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-ga.png)

2. Clique no mosaico para abrir os **Alertas de segurança** para ver mais detalhes sobre os alertas.

   ![Os Alertas de segurança no Centro de Segurança](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-ga.png)

Na parte inferior desta página encontram-se os detalhes de cada alerta. Para ordenar, clique na coluna pela qual pretende ordenar. A definição para cada coluna é indicada abaixo:

* **Descrição**: uma breve explicação do alerta.
* **Contagem**: uma lista de todos os alertas deste tipo específico que foram detetados num dia específico.
* **Detetado por**: o serviço que foi responsável por ter acionado o alerta.
* **Data**: a data em que o evento ocorreu.
* **Estado**: o estado atual para esse alerta. Existem dois tipos de estados:
  * **Ativo**: o alerta de segurança foi detetado.
* **Gravidade**: o nível de gravidade, que pode ser alta, média ou baixa.

### <a name="filtering-alerts"></a>Filtragem de alertas
Pode filtrar os alertas com base na data, no estado e na gravidade. A filtragem de alertas pode ser útil para cenários onde necessita de limitar o âmbito dos alertas de segurança mostrados. Por exemplo, pode pretender resolver alertas de segurança que ocorreram nas últimas 24 horas, porque está a investigar uma potencial violação no sistema.

1. Clique em **Filtrar** nos **Alertas de Segurança**. O **Filtro** abre-se e o utilizador seleciona os valores de data, de estado e de gravidade que pretende ver.

    ![Filtragem de alertas no Centro de Segurança](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-2017.png)

### <a name="respond-to-security-alerts"></a>Responder a alertas de segurança
Selecione um alerta de segurança para obter mais informações sobre o(s) evento(s) que acionaram o alerta e quais os passos (se existirem) necessários para remediar um ataque. Os alertas de segurança estão agrupados por tipo e data. Ao clicar num alerta de segurança abre-se uma página que contém uma lista dos alertas agrupados.

![Responder a alertas de segurança no Centro de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

Neste caso, os alertas que foram acionados referem-se a atividade suspeita do protocolo RDP (Remote Desktop Protocol). A primeira coluna mostra quais os recursos que foram atacados, a segunda mostra o número de vezes que o recurso foi atacado, a terceira mostra a hora do ataque, a quarta mostra o estado do alerta e a quinta mostra a gravidade do ataque. Depois de rever estas informações, clique no recurso que foi atacado.

![Sugestões para o que fazer sobre alertas de segurança no Centro de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

No campo **Descrição** irá encontrar mais detalhes sobre este evento. Estes detalhes adicionais facultam informações aprofundadas sobre o que acionou o alerta de segurança, o recurso de destino, quando aplicável, o endereço IP de origem e as recomendações sobre como remediar.  Em certos casos, o endereço IP de origem está vazio (não disponível) porque nem todos os registos de eventos de segurança do Windows incluem o endereço IP.

A remediação sugerida pelo Centro de Segurança varia de acordo com o alerta de segurança. Em certos casos, poderá ter de utilizar outras capacidades do Azure para implementar a remediação recomendada. Por exemplo, a remediação para este ataque é colocar na lista de proibições o endereço IP que está a gerar este ataque ao utilizar um [ACL de rede](../virtual-network/virtual-networks-acl.md) ou uma regra de [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md). Para obter mais informações sobre os diferentes tipos de alertas, leia [Alertas de Segurança por Tipo no Centro de Segurança do Azure](security-center-alerts-type.md).

> [!NOTE]
> O Centro de Segurança lançou uma pré-visualização limitada de um novo conjunto de deteções que tiram partido dos registos de auditoria, um framework de auditoria comum, para detetar comportamentos maliciosos em computadores Linux. [Envie-nos](mailto:ASC_linuxdetections@microsoft.com) um e-mail com os seus IDs de subscrição para aderir à pré-visualização.


## <a name="see-also"></a>Consultar também
Neste documento, aprendeu a configurar as políticas de segurança no Centro de Segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Lidar com Incidentes de Segurança no Centro de Segurança do Azure](security-center-incident.md)
* [Capacidades de Deteção do Centro de Segurança do Azure](security-center-detection-capabilities.md)
* [Guia de Operações e Planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md)
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
