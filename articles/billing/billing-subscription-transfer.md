---
title: "Transferir a propriedade de subscrição do Azure para outra conta | Microsoft Docs"
description: "Descreve como transferir uma subscrição do Azure para outro utilizador e algumas perguntas mais frequentes (FAQ) sobre o processo"
keywords: "transferência de subscrição de transferência da subscrição do azure, do azure, mover subscrição do azure para outro proprietário de subscrição de alteração da conta, do azure, a transferência de subscrição do azure para outra conta"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: c8ecdc1e-c9c5-468c-a024-94ae41e64702
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/15/2017
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9beef44b3fbaf26d49757544f32b97c7ef2cf425
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="transfer-ownership-of-an-azure-subscription-to-another-account"></a>Transferir a propriedade de uma subscrição do Azure para outra conta

Pode transferir a sua subscrição para outro utilizador no Centro de contas do. Utilize esta funcionalidade para entregar através de propriedade para alguém de faturação da subscrição, alterar a conta de início de sessão ou mover subscrição para um diretório diferente. Para alterar a sua subscrição para uma oferta diferentes, consulte [mudar a sua subscrição do Azure para outra oferta](billing-how-to-switch-azure-offer.md).

> [!IMPORTANT]
> 
> Atualmente não suportamos das transferências de subscrições de avaliação gratuita ou [do Azure no Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) subscrições. Para obter uma solução, consulte [mover recursos para o novo grupo de recursos ou subscrição](../azure-resource-manager/resource-group-move-resources.md).

<a id="supported"></a>

## <a name="whats-supported"></a>O que é suportado:

Transferência de subscrição de gestão personalizada está disponível para o ofertas ou tipos de subscrição listados na seguinte tabela. Para transferir a outras subscrições, como [patrocínio](https://azure.microsoft.com/offers/ms-azr-0036p/) ou suportam esquemas, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

| Nome da Oferta                                                                             | Número da oferta |
|----------------------------------------------------------------------------------------|--------------|
| [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*|MS-AZR - 0017P        |
| [Rede de parceiro da Microsoft](https://azure.microsoft.com/offers/ms-azr-0025p/)          | MS-AZR - 0025P        |
| [Plataformas MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)                     | MS-AZR - 0062P        |
| [Pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/)                      | MS-AZR - 0003P        |
| [Pay as you go programador/teste](https://azure.microsoft.com/offers/ms-azr-0023p/)             | MS-AZR - 0023P        |
| [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)           | MS-AZR - 0063P        |
| [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/) | MS-AZR - 0064P        |
| [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)         | MS-AZR - 0059P        |
| [Visual Studio Professional de teste](https://azure.microsoft.com/offers/ms-azr-0060p/)    | MS-AZR - 0060P        |

\*[Portal através de EA](#EA)

## <a name="transfer-ownership-of-an-azure-subscription"></a>Transferir a propriedade de uma subscrição do Azure

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Transfer-an-Azure-subscription/player]
>
>

1. Inicie sessão em [Centro de contas do Azure](https://account.windowsazure.com/Subscriptions) como o administrador da conta. Para saber quem é o administrador da conta da subscrição, consulte [perguntas mais frequentes](#faq).

1. Selecione a subscrição a transferência.

1. Verifique se a sua subscrição é elegível para a transferência de gestão personalizada, verificando o **oferecem** e **oferecem ID** com o [lista de ofertas suportados](#supported).

   ![Verificar o ID de oferta da subscrição no Centro de contas](./media/billing-subscription-transfer/image0.png)
1. Clique em **Transferir subscrição**.

   ![Separador de subscrições de conta do Azure](./media/billing-subscription-transfer/image1.png)
1. Especifique o destinatário.

   ![Caixa de diálogo de subscrição de transferência](./media/billing-subscription-transfer/image2.PNG)
1. O destinatário recebe automaticamente um e-mail com uma ligação de aceitação.

   ![E-mail de transferência de subscrição para o destinatário](./media/billing-subscription-transfer/image3.png)
1. O destinatário clica na ligação e segue as instruções, o que inclui a introdução das respetivas informações de pagamento.

   ![Primeira página de web de transferência da subscrição](./media/billing-subscription-transfer/image4.png)

   ![Segunda página de web de transferência da subscrição](./media/billing-subscription-transfer/image5.png)
1. Êxito! A subscrição agora é transferida.

<a id="EA"></a>

## <a name="transfer-subscription-ownership-for-enterprise-agreement-ea-customers"></a>Transferir a propriedade de subscrição para clientes do Enterprise Agreement (EA)

O administrador de empresa pode transferir a propriedade das subscrições dentro de inscrição. Para começar a utilizar, consulte o artigo [transferir propriedade de conta](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) no portal do EA.

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>Passos seguintes após a aceitação de propriedade de uma subscrição

1. É agora o administrador de conta. Rever e atualizar o administrador de serviços e Coadministradores. Gerir administradores no [portal clássico do Azure](https://manage.windowsazure.com) acedendo às definições. [Saiba mais sobre as funções de administrador](billing-add-change-azure-subscription-administrator.md).
1. Também pode utilizar o controlo de acesso baseado em funções (RBAC) para a sua subscrição e serviços. Visite o [Portal do Azure](https://portal.azure.com). [Saiba mais sobre o RBAC](../active-directory/role-based-access-control-configure.md)
1. Atualize as credenciais associadas com serviços nesta subscrição, incluindo:
   1. Certificados de gestão que conceder ao utilizador direitos de administrador aos recursos da subscrição. Para obter mais informações, consulte [criar e carregar um gestão de certificados para o Azure](../cloud-services/cloud-services-certs-create.md)
   1. Chaves de acesso para serviços como o armazenamento. Para obter mais informações, consulte [contas do storage do Azure sobre](../storage/common/storage-create-storage-account.md)
   1. Credenciais de acesso remoto para os serviços, como máquinas virtuais do Azure. 
1. [Atualizar faturação alertas para esta subscrição](billing-set-up-alerts.md) no [Centro de contas do Azure](https://account.windowsazure.com/Subscriptions). 
1. Se estiver a trabalhar com um parceiro, considere atualizar o ID de parceiro nesta subscrição. Pode atualizar o ID de parceiro no [Centro de contas do Azure](https://account.windowsazure.com/Subscriptions).

<a id="faq"></a>

## <a name="frequently-asked-questions-faq"></a>Perguntas mais frequentes (FAQ)

### <a name="whoisaa"></a>Quem é o administrador da conta da subscrição?

O administrador da conta é a pessoa que inscreveu no ou comprou a subscrição do Azure. Estão autorizados a aceder a [Centro de contas](https://account.azure.com/Subscriptions) e realizar várias tarefas de gestão, como criar subscrições, cancelar subscrições, altere a faturação de uma subscrição ou alterar o administrador de serviço. Se não tiver a certeza de que o administrador de conta é para uma subscrição, utilize os seguintes passos para descobrir.

1. Visite o [página de subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a subscrição que pretende verificar e, em seguida, procure em **definições**.
1. Selecione **propriedades**. O administrador da conta da subscrição é apresentado no **administrador da conta** caixa.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Tudo transferência? Incluindo grupos de recursos, VMs, discos e outros serviços em execução?

Sim, todos os recursos, como VMs, discos e transferência de Web sites para o novo proprietário. No entanto, quaisquer [funções de administrador](billing-add-change-azure-subscription-administrator.md) e [controlo de acesso baseado em funções (RBAC)](../active-directory/role-based-access-control-configure.md) configurou as políticas não são transferidos em diretórios diferentes.

### <a id="no-button"></a>Por que motivo não vejo o botão "Transferir a subscrição"?

Infelizmente, transferência de subscrição de gestão personalizada não está disponível para o país ou a oferta. Para transferir a sua subscrição, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Transferência de subscrição resulta em qualquer período de indisponibilidade do serviço?

Não há nenhum impacto sobre o serviço. Transferir a subscrição cancela a subscrição sob a conta atual administrador e cria uma subscrição em conta o destinatário. A nova subscrição está associada aos serviços do Azure subjacentes. O ID de subscrição permanece igual.

### <a name="how-do-i-use-this-process-to-change-the-directory-for-subscription"></a>Como utilizar este processo para alterar o diretório da subscrição?

Uma subscrição do Azure é criada no diretório que pertence o administrador da conta. Para alterar o diretório, transferir a subscrição para uma conta de utilizador no diretório de destino. Quando esse utilizador concluir os passos para aceitar a transferência, a subscrição é automaticamente movida para o diretório de destino.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-organization-do-they-continue-to-have-access-to-my-resources"></a>Se posso assumir a propriedade da faturação de uma subscrição de outra organização, continuam a ter acesso para os meus recursos?

Se a subscrição é transferida para outro inquilino, os utilizadores associados ao inquilino anterior perdem o acesso à subscrição. Mesmo se um utilizador não é um administrador de serviço ou coadministrador, poderá ainda têm acesso à subscrição por outros mecanismos de segurança, incluindo:

* Certificados de gestão que conceder ao utilizador direitos de administrador aos recursos da subscrição. Para obter mais informações, consulte [criar e carregar um certificado de gestão para o Azure](../cloud-services/cloud-services-certs-create.md).
* Chaves de acesso para serviços como o armazenamento. Para obter mais informações, consulte [contas do storage do Azure sobre](../storage/common/storage-create-storage-account.md).
* Credenciais de acesso remoto para os serviços, como máquinas virtuais do Azure.

Se o destinatário necessita restringir o acesso aos respetivos recursos, deve considerar a atualização a qualquer segredos associados ao serviço. A maioria dos recursos podem ser atualizados, utilizando os seguintes passos:

  1. Aceda ao [Portal do Azure](https://portal.azure.com).
  2. No Hub menu, selecione **todos os recursos**.
  3. Selecione o recurso.
  4. No painel de recursos, clique em **definições**. Aqui pode ver e atualizar segredos existentes.

### <a name="if-i-transfer-the-subscription-in-the-middle-of-the-billing-cycle-does-the-recipient-pay-for-the-entire-billing-cycle"></a>Se transferir a subscrição no meio do ciclo de faturação, o pagamento de destinatário para a faturação todo ciclo?

O remetente é responsável por pagamento para qualquer utilização que foi comunicado até ao ponto em que a transferência estiver concluída. O destinatário é responsável pela utilização comunicada a partir do momento da transferência e superior. Poderão existir alguns utilização que demorou local antes da transferência, mas foi comunicada posteriormente. A utilização está incluída na fatura do destinatário.

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>O destinatário tem acesso à utilização e o histórico de faturação?

  As únicas informações disponíveis para o destinatário estão a quantidade de último factura ou se a subscrição foi transferida antes da fatura primeiro foi gerado, o equilíbrio atual. O resto da utilização e histórico de faturação não transferir com a subscrição.

### <a name="can-the-offer-be-changed-during-a-transfer"></a>A oferta pode ser alterada durante uma transferência?

A oferta têm de permanecer o mesmo. Para alterar a sua oferta, consulte [mudar a sua subscrição do Azure para outra oferta](billing-how-to-switch-azure-offer.md).

### <a name="can-i-transfer-a-subscription-to-a-user-account-in-another-country"></a>Pode transferir uma subscrição para uma conta de utilizador noutro país?

Não, a transferência de uma subscrição para uma conta de utilizador noutro país não é suportada. Conta de utilizador do destinatário tem de estar no mesmo país.

### <a name="can-the-recipient-use-a-different-payment-method"></a>O destinatário pode utilizar um método de pagamento diferente?

Sim. Mas o histórico de faturação da subscrição é dividido em duas contas.  

### <a name="is-the-payment-method-impacted-after-i-transferred-an-azure-subscription"></a>O método de pagamento é afetado depois posso transferidos uma subscrição do Azure?

Para aceitar uma transferência de subscrição, um cartão de crédito ou um método de pagamento semelhante tem de fornecer aos quais deve prestar da subscrição. Por exemplo, se Bernardo transfere uma subscrição, a Joana e a Joana aceita a transferência, a Joana tem de fornecer um método de pagamento para a subscrição paga. Após a transferência estiver concluída, a Joana é faturada para a subscrição não Bernardo.

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Como migrar dados e serviços para a minha subscrição do Azure para a nova subscrição?

Se não é possível transferir a propriedade de subscrição, pode migrar os recursos manualmente. Consulte [mover recursos para o novo grupo de recursos ou subscrição](../azure-resource-manager/resource-group-move-resources.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.