---
title: "Ativar grupos de segurança no Centro de segurança do Azure | Microsoft Docs"
description: "Este documento mostra como implementar a recomendação de centro de segurança do Azure * * ativar rede segurança grupos * *."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: f53ed853-ffaf-4530-a019-1906ba6f341b
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 1e034d59d8847f237fa0d4c772344d45cd618576
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-network-security-groups-in-azure-security-center"></a>Ativar grupos de segurança no Centro de segurança do Azure
Centro de segurança do Azure recomenda que ative um grupo de segurança de rede (NSG) se uma ainda não estiver ativada. Os NSGs contêm uma lista de regras de lista de controlo de acesso (ACL) que permitem ou negam o tráfego de rede para as instâncias de VM numa rede Virtual. Os NSGs podem ser associados a sub-redes ou a instâncias de VM individuais dentro dessa sub-rede. Quando um NSG é associado a uma sub-rede, as regras da ACL são aplicadas a todas as instâncias de VM nessa sub-rede. Além disso, o tráfego para uma VM individual pode ser restringido adicional ao associar um NSG diretamente a essa VM. Para saber mais, consulte [o que é um grupo de segurança de rede (NSG)?](../virtual-network/virtual-networks-nsg.md)

Se não tiver NSGs ativados, o Centro de segurança apresenta dois recomendações para si: Ativar grupos de segurança de rede em sub-redes e ativar grupos de segurança de rede em máquinas virtuais. Escolha qual nível, a sub-rede ou a VM, para aplicar NSGs.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Não se trata de um guia passo-a-passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. No **recomendações** painel, selecione **ativar grupos de segurança de rede** em sub-redes ou máquinas virtuais.
   ![Ativar os Grupos de Segurança de Rede][1]
2. Esta ação abre o painel **configurar grupos de segurança de rede em falta** para sub-redes ou máquinas virtuais, consoante a recomendação que selecionou. Selecione uma sub-rede ou de uma máquina virtual para configurar um NSG.

   ![Configurar o NSG para sub-rede][2]

   ![Configurar o NSG para VM][3]
3. No **escolher grupo de segurança de rede** painel, selecione um NSG existente ou selecione **criar nova** para criar um NSG.

   ![Escolha o grupo de segurança de rede][4]

Se criar um NSG, siga os passos no [como gerir os NSGs no portal do Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md) para criar um NSG e definir as regras de segurança.

## <a name="see-also"></a>Consultar também
Este artigo mostrou como implementar as recomendações do Centro de segurança "Ativar grupos de segurança" para sub-redes ou máquinas virtuais. Para obter mais informações sobre como ativar os NSGs, consulte o seguinte:

* [O que é um Grupo de Segurança de Rede (NSG)? (What is a Network Security Group (NSG)?)](../virtual-network/virtual-networks-nsg.md)
* [Como gerir os NSGs no portal do Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger os seus recursos do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – obtenha as mais recentes notícias de segurança do Azure e as informações.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
