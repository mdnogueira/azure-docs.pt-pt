---
title: "Oferta da subscrição do Azure de alteração | Microsoft Docs"
description: "Saiba mais sobre como alterar a sua subscrição do Azure e mudar para uma oferta diferentes, utilizando o Centro de contas do Azure"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: aae227b3-6d64-4550-a5b6-d359f53f0a59
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/30/2017
ms.author: genli
ms.openlocfilehash: 48f5f4db60c104778a6c391d254e0bc0c4c95cee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="change-your-azure-pay-as-you-go-subscription-to-a-different-offer"></a>Alterar a sua subscrição pay as you go do Azure para uma oferta diferentes

Como um [pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) cliente, pode mudar a subscrição do Azure para outra oferta no [Centro de contas](https://account.windowsazure.com/Subscriptions). Por exemplo, pode utilizar esta funcionalidade para tirar partido do [créditos mensais para subscritores do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). 

**Apenas pretende atualizar a partir da versão de avaliação gratuita?** Consulte [atualizar para pay as you go](billing-upgrade-azure-subscription.md).

## <a name="whats-supported"></a>O que é suportado:

| Do | Para |
| --- | --- |
| "Pay As You Go" |[Pay as you go programador/teste](https://azure.microsoft.com/offers/ms-azr-0023p/) |
| "Pay As You Go" |[Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |
| "Pay As You Go" |[Visual Studio Professional de teste](https://azure.microsoft.com/offers/ms-azr-0060p/) |
| "Pay As You Go" |[Plataformas MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/) |
| "Pay As You Go" |[Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |
| "Pay As You Go" |[Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |

> [!NOTE]
> Para outras alterações de oferta [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="switch-subscription-offer"></a>Oferta de subscrição de comutador

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
>
>

1. Inicie sessão em [Centro de contas do Azure](https://account.windowsazure.com/Subscriptions).
1. Selecione a sua subscrição Pay As You Go.
1. Clique em **Mudar para outra oferta**. O botão só está disponível se estiver no Pay As You Go e se tiver terminado o seu primeiro período de faturação.

   ![Tenha em atenção o botão de oferta de comutador no lado direito da página](./media/billing-how-to-switch-azure-offer/switchbutton.png)
1. **Selecione a oferta pretende** da lista de ofertas a sua subscrição pode ser mudada para. Esta lista varia consoante a filiação que está associada à sua conta. Se nada estiver disponível, verifique o [lista de ofertas disponíveis, pode mudar para](#whats-supported) e certifique-se de que tem as direitos associações. 

   ![Selecione uma oferta que pretende mudar para o](./media/billing-how-to-switch-azure-offer/selectoffer.png)
1. Consoante a oferta que está a mudar para, poderá ver uma nota sobre o impacto da mudança. Percorra esta lista cuidadosamente e siga as instruções antes de continuar.

   ![Consulte as notas](./media/billing-how-to-switch-azure-offer/thingstonote.png)
1. Pode mudar o nome da sua subscrição. Por predefinição, podemos defini-lo para o novo nome de oferta. Clique em **comutador oferecem** para concluir o processo.

   ![Clique no botão verde](./media/billing-how-to-switch-azure-offer/confirmpage.png)
1. Êxito! A subscrição agora é mudada para a oferta de novo.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="what-is-an-azure-offer"></a>O que é uma oferta do Azure?

Uma oferta do Azure é a *tipo* da subscrição do Azure tiver. Por exemplo, [pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure no Open](https://azure.microsoft.com/offers/ms-azr-0111p/), e [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) são todas as ofertas do Azure. Cada oferta tem diferentes [termos](https://azure.microsoft.com/support/legal/offer-details/) e têm algumas vantagens especiais. A oferta da sua subscrição pode ser encontrada na página de subscrição de centro de contas. Clique no nome de oferta para obter mais detalhes.

   ![Clique na ligação de oferta no Centro de contas para obter mais detalhes](./media/billing-how-to-switch-azure-offer/offerlink.png)

### <a name="why-dont-i-see-the-button"></a>Por que motivo não vejo o botão?

Não poderá ver o **mudar para outra oferta** botão se:

* Não está no [pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/). Atualmente subscrições de pay as you go só podem ser convertidas para outra oferta.
  * Se estiver em [avaliação gratuita](https://azure.microsoft.com/free/), saiba como [atualizar para pay as you go](billing-upgrade-azure-subscription.md).
  * Para mudar a oferta de uma subscrição diferente, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Está ainda no seu período de faturação primeiro; tem de aguardar que o primeiro período de faturação terminar antes que possa mudar de ofertas.

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>Por que razão vejo "Não existem nenhum ofertas disponíveis na sua região ou país neste momento"?

* Não pode ser elegível para comutadores oferta. Verifique o [lista de ofertas disponíveis, pode mudar para](#whats-supported) e certifique-se de que ativou as vantagens à direita com o Visual Studio ou elegíveis do Bizspark.
* Algumas ofertas poderão não estar disponíveis em todos os países.

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>O que faz mudar efetue ofertas do Azure para o meu serviço e faturação?

Seguem-se os detalhes do que acontece quando que o Azure oferece no Centro de contas do.

#### <a name="no-service-downtime"></a>Sem períodos de indisponibilidade do serviço

Não há nenhum período de indisponibilidade do serviço para os utilizadores associadas à subscrição. No entanto, a oferta que mudar para pode ter restrições. Por exemplo, algumas ofertas proíbem a utilização de produção, pelo que é necessário mover os recursos de produção para outra subscrição.

#### <a name="quota-increases-are-reset"></a>Aumento de quota é reposto

Quando mudar ofertas, qualquer [limite ou quota aumenta supera o limite predefinido](../azure-supportability/resource-manager-core-quotas-request.md) são repostas. Não há nenhum período de indisponibilidade do serviço, mesmo se tiver mais recursos para além do limite predefinido. Por exemplo, estiver a utilizar 200 núcleos na sua subscrição, em seguida, mudar ofertas repõe a sua quota de núcleos para a predefinição de 20 núcleos. As VMs que utilizam os 200 núcleos são afetadas e continuam a ser executado. Se não efetuar outro quota aumentar a pedido, no entanto, não é possível aprovisionar qualquer mais núcleos.

#### <a name="billing"></a>Faturação

No dia que mudar, é gerada uma fatura para todos os encargos pendentes. Em seguida, a sua subscrição é faturada por termos dos preços a oferta de novo. Alterações às seu aniversário da faturação de subscrição para a data em que alterou ofertas. Utilização e faturação dados antes da alteração de oferta não é mantida, pelo que recomendamos que transfira uma cópia antes de mudar.

### <a name="can-i-migrate-from-pay-as-you-go-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>Pode migrar de pay as you go para [provedor de soluções de nuvem](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) ou [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA)?

* Para migrar para o CSP, consulte [migração da subscrição do Azure Pas-como-,-ir para o CSP](https://docs.microsoft.com/en-us/azure/cloud-solution-provider/migration/migration-from-payg-to-csp).
* Para migrar para EA, tem de adicionar a sua conta para o EA o seu administrador de inscrição. Siga as instruções no e-mail de convite para que as suas subscrições movidas em inscrição EA. Para obter mais informações, consulte [associar uma conta existente](https://ea.azure.com/helpdocs/associateExistingAccount) no portal do EA.

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>Posso migrar dados e serviços para uma nova subscrição?

* Pode migrar os recursos diretamente para a nova subscrição, consulte [mover recursos para o novo grupo de recursos ou subscrição](../azure-resource-manager/resource-group-move-resources.md).
* Para transferir a propriedade de uma subscrição do Azure e tudo no mesmo para outra pessoa, consulte o artigo [transferir a propriedade de uma subscrição do Azure](billing-subscription-transfer.md)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se ainda tiver mais perguntas, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.
