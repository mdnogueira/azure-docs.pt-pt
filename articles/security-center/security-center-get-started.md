---
title: "Guia de introdução do Centro de segurança do Azure | Microsoft Docs"
description: "Este artigo ajuda-o a começar rapidamente a utilizar o Centro de Segurança do Azure, ao servir de orientação para os componentes de monitorização de segurança e de gestão de políticas e ligá-lo aos passos seguintes."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: c28f92af96f31d1c386cf072f83fc142b9a7f588
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-quick-start-guide"></a>Guia de introdução ao Centro de Segurança do Azure
Este artigo ajuda-o a começar rapidamente a utilizar o Centro de Segurança do Azure, ao servir de orientação para os componentes de monitorização de segurança e de gestão de políticas do Centro de Segurança.

## <a name="prerequisites"></a>Pré-requisitos
Para começar a utilizar o Centro de Segurança, tem de possuir uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

O escalão gratuito do Centro de segurança é ativado automaticamente em todas as subscrições do Azure e fornece a política de segurança, avaliação de segurança contínua e recomendações de segurança passíveis de ação para o ajudar a proteger os seus recursos do Azure.

Pode aceder ao Centro de Segurança a partir do [Portal do Azure](https://azure.microsoft.com/features/azure-portal/). Para obter mais informações sobre o portal do Azure, veja a [documentação do portal](https://azure.microsoft.com/documentation/services/azure-portal/).

## <a name="permissions"></a>Permissões
No Centro de segurança, verá apenas informações relacionadas com a um recurso quando são atribuídas a função de proprietário, Contribuidor ou leitor para a subscrição ou grupo de recursos que um recurso pertence. Consulte [permissões no Centro de segurança do Azure](security-center-permissions.md) para saber mais sobre as funções e as ações permitidas no Centro de segurança.

## <a name="data-collection"></a>Recolha de dados
Centro de segurança recolhe dados de máquinas de virtuais (VMs) do Azure e computadores não do Azure para monitorizar vulnerabilidades de segurança e ameaças. Os dados são recolhidos através do Microsoft Monitoring Agent, que lê várias configurações relacionadas com segurança e registos de eventos a partir da máquina e copia os dados para a sua área de trabalho para análise. Aprovisiona do Centro de segurança do Microsoft Monitoring Agent em todos os existentes suportadas VMs do Azure e quaisquer novos que são criados. Consulte [ativar a recolha de dados](security-center-enable-data-collection.md) para saber mais sobre como funciona a recolha de dados.

Aprovisionamento Automático é vivamente recomendado, não sendo necessário para subscrições na camada padrão do Centro de segurança. Desativar automática aprovisionamento limites monitorização de segurança para os seus recursos.

Consulte [preços do Centro de segurança](security-center-pricing.md) para saber mais sobre os gratuito e Standard escalões de preço.

Os passos seguintes descrevem como aceder e utilizar os componentes do Centro de Segurança.

> [!NOTE]
> Este artigo apresenta o serviço ao utilizar um exemplo de implementação. Este artigo não é um guia passo-a-passo.
>
>

## <a name="access-security-center"></a>Aceder ao Centro de Segurança
No portal, siga estes passos para aceder ao Centro de Segurança:

1. No menu **Microsoft Azure**, selecione **Centro de Segurança**.

   ![Menu do Azure][1]
2. Se estiver a aceder ao Centro de Segurança pela primeira vez, o painel de **Boas-Vindas** abre. Selecione **Centro de segurança de iniciação** para abrir **Centro de segurança**.
   ![Ecrã de Boas-Vindas][10]
3. Depois de iniciar o Centro de segurança a partir do painel de boas-vindas ou centro de segurança, selecione o menu de Microsoft Azure, **Centro de segurança** abre. Para facilitar o acesso ao painel **Centro de Segurança** no futuro, selecione a opção **Afixar painel ao dashboard** (canto superior direito).
   ![Opção Afixar painel ao dashboard][2]

## <a name="use-security-center"></a>Utilizar o Centro de Segurança
Pode configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure. Vamos configurar uma política de segurança para a sua subscrição:

1. No menu principal do Centro de segurança, selecione **política de segurança**.
2. Em **Centro de segurança - política de segurança**, selecione uma subscrição.
3. Em **política de segurança – recolha de dados**, **aprovisionamento automático** está ativada. Aprovisiona do Centro de segurança do Microsoft Monitoring Agent em todos os existentes suportadas VMs do Azure e quaisquer novos que são criados.

    ![Política de segurança][12]

4. No **política de segurança** painel, selecione o componente de política **política de segurança**.

     ![Política de segurança][11]

5. Em **Mostrar recomendações para**, ative as recomendações que pretende ver como parte da sua política de segurança. Exemplos:

   * Definição **atualizações do sistema** para **no** todas as análises suportado VMs para atualizações do SO em falta.
   * Definição **vulnerabilidades do SO** para **no** suportado de análises todas as VMs para identificar quaisquer configurações de SO que podem tornar a VM mais vulnerável a ataques.

6. Selecione **Guardar**.

### <a name="view-recommendations"></a>Ver recomendações
1. Volte ao painel **Centro de Segurança** e selecione o mosaico **Recomendações**. O Centro de Segurança analisa periodicamente o estado de segurança dos seus recursos do Azure. Quando o Centro de Segurança identifica potenciais vulnerabilidades de segurança, mostra recomendações no painel **Recomendações**.
   ![Recomendações no Centro de Segurança do Azure][5]
2. Selecione uma recomendação no painel **Recomendações** para visualizar mais informações e/ou para tomar medidas de resolução do problema.

### <a name="view-the-security-state-of-your-resources"></a>Ver o estado de segurança dos seus recursos
1. Volte ao painel **Centro de Segurança**. O **prevenção** secção do dashboard contém indicadores de estado de segurança para VMs, funcionamento em rede, dados e aplicações.
2. Selecione **computação** para visualizar mais informações. O **computação** painel abre-se que mostra três separadores:

  - **Descrição geral** -contém a monitorização e recomendações da VM.
  - **VMs e computadores** -apresenta o estado atual de segurança de todas as VMs e computadores.
  - **Serviços em nuvem** -apresenta uma lista de funções web e de trabalho monitorizadas pelo centro de segurança.

    ![Estado de funcionamento de segurança de computação][6]

3. No **descrição geral** separador, selecione uma recomendação em para ver mais informações e/ou tomar medidas para configurar os controlos necessários.
4. No **VMs e computadores** separador, selecione um recurso para ver detalhes adicionais.

### <a name="view-security-alerts"></a>Ver alertas de segurança
1. Volte ao painel **Centro de Segurança** e selecione o mosaico **Alertas de segurança**. O painel **Alertas de segurança** abre e é apresentada uma lista dos alertas. A análise do Centro de Segurança dos seus registos de segurança e atividade de rede gera estes alertas. Os alertas de soluções de parceiros integradas são incluídos.
   ![Alertas de segurança no Centro de Segurança do Azure][7]

2. Selecione um alerta para ver informações adicionais. Neste exemplo, vamos selecione **modificado binário de sistema detetado num filtro de captura**. Este procedimento abre painéis que fornecem detalhes adicionais sobre o alerta.
   ![Detalhes de alertas de segurança no Centro de Segurança do Azure][8]

### <a name="view-the-health-of-your-partner-solutions"></a>Ver o estado de funcionamento das suas soluções de parceiros
1. Volte ao painel **Centro de Segurança**. O **soluções de segurança** mosaico permite-lhe monitorizar, de forma rápida, o estado de funcionamento das suas soluções de parceiros integradas na sua subscrição do Azure.
2. Selecione o **soluções de segurança** mosaico. Abre-se um painel a apresentar uma lista das suas soluções de parceiros ligadas ao Centro de Segurança.
   ![Soluções de parceiros][9]
3. Selecione uma solução de parceiros. Abre-se um painel que mostra o estado da solução de parceiros e os recursos associados à solução. Selecione **Consola de soluções** para abrir a experiência de gestão de parceiros desta solução.

   ![Soluções de parceiros][13]

## <a name="next-steps"></a>Passos seguintes
Este artigo apresentou-lhe os componentes de monitorização de segurança e de gestão de políticas do Centro de Segurança. Agora que está familiarizado com o Centro de Segurança, experimente os seguintes passos:

* Configurar uma política de segurança para a sua subscrição do Azure, consulte [definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md).
* Utilize as recomendações no Centro de segurança para o ajudar a proteger os seus recursos do Azure, consulte [gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md).
* Reveja e gerir os alertas de segurança atual, consulte [gerir e responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md).
- Saiba mais sobre a integração com parceiros para melhorar a segurança geral, consulte [parceiro e soluções de integração](security-center-partner-integration.md).
- Saiba como os dados são geridos e salvaguardados no Centro de segurança, consulte [segurança de dados do Centro de segurança do Azure](security-center-data-security.md).
* Saiba mais sobre as [funcionalidades avançadas de deteção de ameaças](security-center-detection-capabilities.md) incluídas no [escalão Standard](security-center-pricing.md) do Centro de segurança. O escalão Standard é gratuito durante os 60 dias iniciais.
* Se tiver perguntas sobre como utilizar o Centro de Segurança, veja [Perguntas mais frequentes do Centro de Segurança do Azure](security-center-faq.md).

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png
[11]: ./media/security-center-get-started/show-recommendations-for.png
[12]: ./media/security-center-get-started/automatic-provisioning.png
[13]: ./media/security-center-get-started/partner-solutions-detail.png
