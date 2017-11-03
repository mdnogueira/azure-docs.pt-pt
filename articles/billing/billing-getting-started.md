---
title: "Impedir que os custos inesperados, gerir faturação - Azure | Microsoft Docs"
description: "Saiba como evitar custos inesperados na sua factura do Azure. Utilize funcionalidades de gestão e o controlo de custo de uma subscrição do Microsoft Azure."
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: tonguyen
ms.openlocfilehash: d74f649a8b5f35ffe16479576959e5ebb2857bf1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Evitar encargos inesperados com o custo de gestão e de faturação do Azure

Quando se inscreve no Azure, existem várias coisas que pode fazer para obter uma ideia mais precisa da sua spend. O [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/) pode fornecer uma estimativa dos custos antes de criar um recurso do Azure. O [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) fornece-lhe a divisão de custo atual e previsão da sua subscrição. Se pretende agrupar e compreender os custos de projetos diferentes ou equipas, observe [recursos etiquetagem](../azure-resource-manager/resource-group-using-tags.md). Se a sua organização tem um sistema de relatórios que preferir utilizar, veja o [APIs de faturação](billing-usage-rate-card-overview.md). 

- Se a sua subscrição está um Enterprise Agreement (EA), a pré-visualização pública para ver os custos no portal do Azure está disponível. Se a sua subscrição é através do fornecedor de solução em nuvem (CSP) ou de Azure Sponsorship, em seguida, algumas das seguintes funcionalidades poderão não se aplicar ao utilizador. Consulte [recursos adicionais para EA, o CSP e o patrocínio](#other-offers) para obter mais informações.

- Se numa avaliação gratuita, a sua subscrição está [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), do Azure no Open (AIO) ou elegíveis do BizSpark, a subscrição é automaticamente desativada quando todos os seus créditos são utilizados. Saiba mais sobre [limites de gastos](#spending-limit) para evitar que a sua subscrição unexpectantly desativada.

## <a name="get-estimated-costs-before-adding-azure-services"></a>Obter os custos estimados antes de adicionar serviços do Azure

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Estimar os custos online utilizando a Calculadora de preços

Veja o [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para obter um custo estimado mensal do serviço estiver interessado em. Pode adicionar terceiros primeiro recurso do Azure para obter um estimativa de custo.

![Captura de ecrã do menu de calculadora de preços](./media/billing-getting-started/pricing-calc.png)

Por exemplo, um A1 Windows Máquina Virtual (VM) é estimada para $66.96 EUR/mês nas horas de computação se deixar a executar o tempo de todo:

![Captura de ecrã da Calculadora de preços que mostra que uma VM do Windows A1 estima-se que custo $66.96 EUR por mês](./media/billing-getting-started/pricing-calcVM.png)

Para obter mais informações sobre preços, consulte este [FAQ](https://azure.microsoft.com/pricing/faq/). Ou, se pretender comunicar com um salesperson do Azure, contacte 1-800-867-1389.

### <a name="review-the-estimated-cost-in-the-azure-portal"></a>Reveja o custo estimado no portal do Azure

Normalmente, quando adicionar um serviço no portal do Azure, não há uma vista que mostra-lhe um custo estimado semelhante por mês. Por exemplo, quando escolher o tamanho da sua VM do Windows, consulte o custo estimado mensalmente durante as horas de computação:

![Exemplo: uma VM do Windows A1 demore custo $66.96 EUR por mês](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="set-up-billing-alerts"></a>Set up billing alerts (Configurar alertas de faturação)

Configure alertas de faturação para receber e-mails quando os custos de utilização excedem um período que especificar. Se tiver de créditos mensais, configure alertas para quando utilizar a segurança de um período especificado. Para obter mais informações, consulte [configurar alertas para as subscrições do Microsoft Azure de faturação](billing-set-up-alerts.md).

![Captura de ecrã de um e-mail de alerta de faturação](./media/billing-getting-started/billing-alert.png)

> [!NOTE]
> Esta funcionalidade ainda está em pré-visualização para deve verificar regularmente a utilização.

Pode querer utilizar a estimativa de custo da Calculadora de preços como orientação para o primeiro alerta.

### <a name="spending-limit"></a>Verifique se tem um limite de gastos

Se tiver uma subscrição que utiliza os créditos, em seguida, o limite de gastos está ativado para si por predefinição. Desta forma, quando gaste os créditos, o cartão de crédito não obter cobrado. Consulte o [lista completa das ofertas do Azure e a disponibilidade de limite de gastos](https://azure.microsoft.com/support/legal/offer-details/).

No entanto, se atingiu o limite de gastos, os serviços de obterem desativados. Isto significa que as VMs estão desalocadas. Para evitar períodos de indisponibilidade do serviço, tem de desativar o limite de gastos. Qualquer excesso obtém cobrado no seu cartão de crédito no ficheiro. 

Para ver se que já obteve limite de gastos no, vá para o [subscrições ver no Centro de contas do](https://account.windowsazure.com/Subscriptions). É apresentada uma faixa se o limite de gastos no:

![Captura de ecrã que mostra um aviso sobre a dedicar muito do limite no Centro de contas do](./media/billing-getting-started/spending-limit-banner.PNG)

Clique na faixa e siga as instruções para remover o limite de gastos. Se não introduzir informações de cartão de crédito aquando da inscrição, tem de introduzir para remover o limite de gastos. Para obter mais informações, consulte [limitar gastos do Azure – como funciona e como ativar ou removê-lo](https://azure.microsoft.com/pricing/spending-limits/).

## <a name="ways-to-monitor-your-costs-when-using-azure-services"></a>Formas de monitorizar os custos ao utilizar os serviços do Azure

### <a name="tags"></a>Adicionar etiquetas aos seus recursos para agrupar os dados de faturação

Pode utilizar etiquetas para agrupar dados de faturação para serviços suportados. Por exemplo, se executar várias VMs para equipas diferentes, em seguida, pode utilizar etiquetas para categorizar os custos pelo centro de custos (RH, de marketing, financeiro) ou de ambiente (teste de pré-produção, de produção,). 

![Captura de ecrã que mostra como configurar as etiquetas no portal](./media/billing-getting-started/tags.PNG)

Os etiquetas são apresentados em todo o custo diferentes vistas de relatórios. Por exemplo, se forem visíveis na sua [vista de análise de custos](#costs) imediato e [. csv de utilização de detalhe](#invoice-and-usage) após o primeiro período de faturação.

Para obter mais informações, consulte [utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a>Regularmente, consulte o portal de divisão de custos e gravado taxa

Depois de obter os serviços em execução, verificar regularmente quanto o está a custos. Pode ver o spend atual e gravado taxa no portal do Azure. 

1. Visite o [painel subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione uma subscrição.

2. Deverá ver a divisão de custos e gravado taxa no painel de pop-up. Não pode ser suportado para a sua oferta (será apresentado um aviso perto da parte superior).

    ![Captura de ecrã da taxa de gravado e repartição no portal do Azure](./media/billing-getting-started/burn-rate.PNG)

3. Clique em **análise de custos** na lista à esquerda para ver a divisão de custo por recurso. Aguarde 24 horas depois de adicionar um serviço para os dados preencher.

    ![Captura de ecrã da vista de análise de custos no portal do Azure](./media/billing-getting-started/cost-analysis.PNG)

4. Pode filtrar por propriedades diferentes, como [etiquetas](#tags), grupo de recursos e timespan. Clique em **aplicar** para confirmar os filtros e **transferir** se pretender exportar a vista para um ficheiro de valores de Comma-Separated (. csv).

5. Além disso, pode clicar num recurso para ver diariamente gastam histórico e quanto o recurso de custos de cada dia.

    ![Captura de ecrã da vista do histórico de spend no portal do Azure](./media/billing-getting-started/costhistory.PNG)

Recomendamos que verifique os custos que consulte estimativas que viu quando tiver selecionado os serviços. Se os custos wildly diferem das estimativas, volte a verificar o plano de preços (vs A1 A0 VM, por exemplo) que selecionou para os seus recursos. 

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>Considere ativar funcionalidades de custo de corte como encerramento automático para as VMs

Dependendo do seu cenário, pode configurar o encerramento automático para as suas VMs no portal do Azure. Para obter mais informações, consulte [encerramento automático para as VMs com o Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Captura de ecrã da opção de encerramento automático no portal](./media/billing-getting-started/auto-shutdown.PNG)

Encerramento automático não é igual a quando encerrar dentro da VM com as opções de energia. Encerramento automático interrompe e deallocates as VMs para parar a custos de utilização adicionais. Para obter mais informações, consulte preços perguntas mais frequentes sobre [VMs com Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [VMs do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) sobre Estados VM.

Para mais de corte de custo de funcionalidades para os seus ambientes de desenvolvimento e teste, consulte [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Ative e veja as recomendações do Assistente do Azure

[Azure Advisor](../advisor/advisor-overview.md) é uma funcionalidade de pré-visualização que o ajuda a reduzir os custos ao identificar recursos com baixa utilização. Ativá-la no portal do Azure:

![Botão de captura de ecrã do Advisor do Azure no portal do Azure](./media/billing-getting-started/advisor-button.PNG)

Em seguida, pode obter recomendações acionáveis **custo** separador no dashboard do Advisor:

![Exemplo de recomendação do custo de captura de ecrã do Advisor](./media/billing-getting-started/advisor-action.PNG)

Para obter mais informações, consulte [recomendações do Advisor custo](../advisor/advisor-cost-recommendations.md).

## <a name="reviewing-costs-at-the-end-of-your-billing-cycle"></a>Rever os custos no final do seu ciclo de faturação

Após o fim do seu ciclo de faturação, sua fatura ficará disponível. Também pode [transferir passado faturas e ficheiros de utilização de detalhe](billing-download-azure-invoice-daily-usage-date.md) para se certificar de que foram cobrados corretamente. Para obter mais informações sobre como comparar a utilização diária com a sua fatura, consulte [compreender a fatura do Microsoft Azure](billing-understand-your-bill.md).

### <a name="billing-api"></a>API de faturação

Utilize a nossa API faturação programaticamente obter dados de utilização. Utilize a API de RateCard e a API de utilização em conjunto para obter a utilização da billed. Para obter mais informações, consulte [obter informações acerca do consumo de recursos do Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a>Recursos adicionais e casos especiais

### <a name="ea-csp-and-sponsorship-customers"></a>Clientes EA, o CSP e o patrocínio
Comunicar com o Gestor de conta ou o parceiro do Azure para começar a utilizar.

| Oferta | Recursos |
|-------------------------------|-----------------------------------------------------------------------------------|
| Enterprise Agreement (EA) | [Portal EA](https://ea.azure.com/), [ajudar docs](https://ea.azure.com/helpdocs), e [relatório do Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Fornecedor de Soluções Cloud (CSP) | Comunicar com o fornecedor |
| Azure Sponsorship | [Portal de patrocínio](https://www.microsoftazuresponsorships.com/) |

Se estiver a gerir IT uma organização de grandes dimensões, recomenda-se leitura [andaime Azure enterprise](../azure-resource-manager/resource-manager-subscription-governance.md) e [enterprise documento técnico de TI](http://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (. pdf transferência, apenas em inglês).

#### <a name="EA"></a>Pré-visualização Enterprise Agreement custo vistas dentro do Portal do Azure 

Vistas de custo de empresa estão atualmente em pré-visualização pública. Itens a ter em atenção:
- Os custos de subscrição baseiam-se em utilização e não conta quantidades pré-pago, excedentes, quantidades incluídas, ajustes e taxas. Custos reais são calculados do nível de inscrição. 
- Quantidades apresentadas no portal do Azure podem sofrer um atraso de compared para valores no portal da empresa.  
- Se não existem custos, tal poderá dever-se um dos seguintes motivos:
    - São um proprietário da conta e o administrador de inscrição desativou os "pedidos vista encargos" definir.  Contacte o administrador de inscrição para obter acesso aos custos. 
    - É um administrador do departamento e o administrador de inscrição desativou os "DA vista encargos" definir.  Contacte o administrador de inscrição para obter acesso. 
    - Compradas do Azure através de um parceiro de canal e o parceiro não lançou informações sobre os preços.  
- Quando as definições relacionadas com o custo de acesso são atualizadas no portal da empresa, não há um atraso de alguns minutos antes das alterações serão refletidas no portal do Azure.
- Limite de gastos, alertas de faturação e orientações fatura não dizem respeito ao EA subscrições.

### <a name="check-your-subscription-and-access"></a>Verifique a sua subscrição e o acesso

Os custos de visualização requerem [acesso de nível de subscrições para as informações de faturação](billing-manage-access.md), mas apenas o administrador de conta pode aceder a [Centro de contas](https://account.azure.com/Subscriptions), alterar as informações de faturação e gerir subscrições. O administrador de conta é a pessoa que ocorreu um erro durante o processo de inscrição. Para obter mais informações, consulte [adicionar ou alterar funções de administrador do Azure que gerem a subscrição ou serviços](billing-add-change-azure-subscription-administrator.md).

Para ver se tiver o administrador de conta, vá para o [painel subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e observe a lista de subscrições que tem acesso. Procure em **meu função**. Se diz *administrador da conta*, em seguida, tiver ok. Se der outro como diz *proprietário*, e não tiver privilégios completos.

![Captura de ecrã da sua função na vista de subscrições no portal do Azure](./media/billing-getting-started/sub-blade-view.PNG)

Se não for o administrador de conta, alguém provavelmente lhe forneceu um acesso parcial através de [controlo de acesso baseado em função do Active Directory do Azure](../active-directory/role-based-access-control-configure.md) (RBAC). Para gerir subscrições e alteração em informação, de faturação [localizar o administrador de conta](billing-subscription-transfer.md#whoisaa) e peça-lhes para executar as tarefas ou [transferir a subscrição para si](billing-subscription-transfer.md).

Se o administrador de conta já não consta com a sua organização e precisa de gerir a faturação, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). 
## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.
