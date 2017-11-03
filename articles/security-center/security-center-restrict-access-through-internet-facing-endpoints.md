---
title: "Restringir o acesso através de pontos finais de acesso à Internet no Centro de segurança do Azure | Microsoft Docs"
description: "Este documento mostra como implementar a recomendação de centro de segurança do Azure * * restringir o acesso através da Internet com o ponto final * *."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 727d88c9-163b-4ea0-a4ce-3be43686599f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: terrylan
ms.openlocfilehash: f7309c617f1705205e2c9f1b1b48d141391d45da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="restrict-access-through-internet-facing-endpoints-in-azure-security-center"></a>Restringir o acesso através de pontos finais de acesso à Internet no Centro de segurança do Azure
Centro de segurança do Azure recomendará que restringir o acesso através de pontos finais de acesso à Internet se qualquer um dos seus grupos de segurança de rede (NSGs) tem uma ou mais regras de entrada que permite o acesso de "qualquer" endereço IP de origem. Abrir o acesso a "nenhum" pode permitir que os atacantes aceder aos recursos. Centro de segurança irá recomendar que editar estas regras de entrada para restringir o acesso aos endereços IP de origem que, na verdade, precisam de acesso.

Esta recomendação é gerada para qualquer porta de web não tem "qualquer" como origem.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação. Não se trata de um guia passo-a-passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. No **painel recomendações**, selecione **restringir o acesso através da Internet com o ponto final**.

   ![Restringir o acesso através de um ponto final com acesso à Internet][1]
2. Esta ação abre o painel **restringir o acesso através da Internet com o ponto final**. Este painel lista as máquinas virtuais (VMs) com as regras de entrada que criam um potencial problema de segurança. Selecione uma VM.

   ![Selecione uma VM][2]
3. O **NSG** painel mostra informações do grupo de segurança de rede, relacionadas com as regras de entrada e a VM associada. Selecione **editar regras de entrada** para continuar com a edição de uma regra de entrada.

   ![Painel do grupo de segurança de rede][3]
4. No **regras de segurança de entrada** painel selecione a regra de entrada para editar. Neste exemplo, vamos selecione **AllowWeb**.

   ![Regras de segurança de entrada][4]

   Tenha em atenção, também pode selecionar **predefinido regras** para ver o conjunto de regras de predefinidas contido por todos os NSGs. Não não possível eliminar as regras predefinidas, mas como lhes é atribuída uma prioridade mais baixa, podem ser substituídas pelas regras que criar. Saiba mais sobre [predefinido regras](../virtual-network/virtual-networks-nsg.md#default-rules).

   ![Regras predefinidas][5]
5. No **AllowWeb** painel, editar as propriedades da regra de entrada para que o **origem** é um endereço IP ou o bloco de endereços IP. Para saber mais sobre as propriedades da regra de entrada, consulte o artigo [regras do NSG](../virtual-network/virtual-networks-nsg.md#nsg-rules).

   ![Editar regra de entrada][6]

## <a name="see-also"></a>Consultar também
Este artigo mostrou como implementar a recomendação de centro de segurança "Restringir o acesso através de ponto de final com acesso à Internet." Para obter mais informações sobre como ativar regras e os NSGs, consulte o seguinte:

* [O que é um Grupo de Segurança de Rede (NSG)? (What is a Network Security Group (NSG)?)](../virtual-network/virtual-networks-nsg.md)
* [Como gerir os NSGs no portal do Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir recomendações de segurança no Centro de Segurança do Azure](security-center-recommendations.md) – Saiba como as recomendações o ajudam a proteger os seus recursos do Azure.
* [Monitorização do estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [FAQ do Centro de Segurança do Azure](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – Obtenha as notícias e informações mais recentes de segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png
