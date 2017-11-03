---
title: "Adicionar uma firewall da próxima geração no Centro de segurança do Azure | Microsoft Docs"
description: "Este documento mostra como implementar as recomendações do Centro de segurança do Azure * * adicionar um próxima geração Firewall * * e * * rota traffice através da NGFW apenas * *."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 48b99015-4db8-4ce8-85e4-b544c0fa203e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 30589d0a943517c03394a3aae7c03c8094e78c1f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Adicionar uma Firewall da próxima geração no Centro de segurança do Azure
Centro de segurança do Azure poderá recomendar que adicionar uma firewall da próxima geração (NGFW) de um parceiro da Microsoft para aumentar as proteções de segurança. Este documento explica-lhe como um exemplo de como fazê-lo.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Não se trata de um guia passo-a-passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. No **recomendações** painel, selecione **adicionar uma Firewall da próxima geração**.
   ![Adicionar uma Firewall da Próxima Geração][1]
2. No **adicionar uma Firewall da próxima geração** painel, selecione um ponto final.
   ![Selecione um ponto final][2]
3. Um segundo **adicionar uma Firewall da próxima geração** abre o painel. Pode optar por utilizar uma solução existente, se disponível ou pode criar um novo. Neste exemplo, não existem soluções existentes de nenhuma disponível pelo que criamos uma NGFW.
   ![Criar Firewall da próxima geração][3]
4. Para criar um NGFW, selecione uma solução da lista de parceiros integrados. Neste exemplo, selecione **ponto de verificação**.
   ![Selecione a solução de Firewall da próxima geração][4]
5. O **ponto de verificação** painel abre fornecendo-lhe informações sobre a solução de parceiros. Selecione **criar** no painel de informações.
   ![Painel de informações de firewall][5]
6. O **criar máquina virtual** abre o painel. Aqui pode introduzir informações necessárias para rotação de uma máquina virtual (VM) que é executada da NGFW. Siga os passos e forneça as informações de NGFW necessárias. Selecione OK para aplicar.
   ![Criar máquina virtual para executar NGFW][6]

## <a name="route-traffic-through-ngfw-only"></a>Encaminhar o tráfego apenas através da NGFW
Volte à **recomendações** painel. Uma nova entrada foi gerada depois adicionou um NGFW através do Centro de segurança, denominado **encaminhar o tráfego através da NGFW apenas**. Esta recomendação é criada apenas se tiver instalado o NGFW através do Centro de segurança. Se tiver pontos finais de acesso à Internet, o Centro de segurança recomenda que configura as regras do grupo de segurança de rede que forçar o tráfego de entrada à VM através do seu NGFW.

1. No **painel recomendações**, selecione **encaminhar o tráfego através da NGFW apenas**.
   ![Encaminhar o tráfego apenas através da NGFW][7]
2. Esta ação abre o painel **encaminhar o tráfego através da NGFW apenas**, que apresenta uma lista de VMs que pode encaminhar o tráfego. Selecione uma VM a partir da lista.
   ![Selecione uma VM][8]
3. É aberto um painel para a VM selecionada, que apresenta as regras de entrada relacionadas. Uma descrição fornece-lhe obter mais informações sobre passos possíveis. Selecione **editar regras de entrada** para continuar com a edição de uma regra de entrada. As expectativas são de que **origem** não está definido como **qualquer** para os pontos finais de acesso à Internet ligados da NGFW. Para saber mais sobre as propriedades da regra de entrada, consulte o artigo [regras do NSG](../virtual-network/virtual-networks-nsg.md#nsg-rules).
   ![Configurar regras para limitar o acesso][9]
   ![Editar regra de entrada][10]

## <a name="see-also"></a>Consultar também
Este documento mostrou como implementar a recomendação de centro de segurança "Adicionar uma Firewall da próxima geração." Para saber mais sobre NGFWs e a solução de parceiros do ponto de verificação, consulte o seguinte:

* [Firewall da próxima geração](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
* [Ponto de verificação vSEC](https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger os seus recursos do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre mensagens do Blogue acerca da segurança do Azure e conformidade.

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png
[7]: ./media/security-center-add-next-gen-firewall/route-traffic-through-ngfw.png
[8]: ./media/security-center-add-next-gen-firewall/select-vm.png
[9]: ./media/security-center-add-next-gen-firewall/configure-rules-to-limit-access.png
[10]: ./media/security-center-add-next-gen-firewall/edit-inbound-rule.png
