---
title: "Adicionar ou alterar funções de subscrição de administrador do Azure | Microsoft Docs"
description: "Descreve como adicionar ou alterar o Coadministrador do Azure, o administrador de serviço e o administrador de conta"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/19/2017
ms.author: genli
ms.openlocfilehash: 091fe208783a2fe5d5c91abe4ec498bf760a3eb3
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="add-or-change-azure-administrator-roles-that-manage-the-subscription-or-services"></a>Adicionar ou alterar funções de administrador do Azure que gerem a subscrição ou serviços

Pode alterar o administrador do Azure que gere a sua subscrição do Azure ou gere os serviços do Azure utilizados na sua subscrição. Para ver as informações de faturação do Azure e gerir subscrições, tem de iniciar sessão Centro de contas do como o administrador da conta. 

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-admin-for-a-subscription-in-azure-portal"></a>Adicionar um administrador de proprietário do RBAC para uma subscrição no portal do Azure 

Para adicionar um utilizador como um admin para uma subscrição no portal do Azure, é recomendável concedendo-lhes uma [RBAC](../active-directory/role-based-access-control-configure.md) função de proprietário. A função de proprietário pode gerir os recursos na subscrição que atribuídos e não tem privilégios de acesso a outras subscrições. Os proprietários adicionar através de [portal do Azure](https://portal.azure.com) não é possível gerir recursos no [portal clássico do Azure](https://manage.windowsazure.com).

1. Iniciar sessão para o [Ver subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a subscrição que pretende que o administrador de acesso.
1. Selecione **(IAM) do controlo de acesso** no menu.
1. Selecione **adicionar** > **função** > **proprietário**. Escreva o endereço de e-mail do utilizador que pretende adicionar como proprietário, selecione o utilizador e, em seguida, selecione **guardar**.

    ![Captura de ecrã que mostra a função de proprietário selecionada](./media/billing-add-change-azure-subscription-administrator/add-role.png)

## <a name="add-or-change-co-administrator"></a>Adicionar ou alterar o coadministrador

Apenas um proprietário pode ser adicionado como coadministrador. Não não possível adicionar outros utilizadores com funções, tais como o leitor e contribuinte como coadministradores.

1. Se ainda não o fez, adicione alguém como um proprietário seguir instruções de acima.
2. **Clique com botão direito** o utilizador proprietário que acabou de adicionar e, em seguida, selecione **adicionar como coadministrador**. Se não vir o **adicionar como coadministrador** opção actualizado a página ou tente outro browser da Internet. 

     ![Captura de ecrã que adiciona o coadministrador](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    >[!TIP]
    >Terá de adicionar a conta de "Proprietário" como coadministrador, se o utilizador precisa de gerir os serviços do Azure no [portal clássico do Azure](https://manage.windowsazure.com/).

    Para remover a permissão de coadministrador, **com o botão direito** o utilizador de "Coadministrador" e, em seguida, selecione **remover coadministrador**.

    ![Captura de ecrã que remove o coadministrador](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>Alterar o administrador de serviço para uma subscrição do Azure

Apenas o administrador da conta pode alterar o administrador de serviço para uma subscrição. Por predefinição, quando se inscreve, o administrador de serviço é o mesmo que o administrador da conta.

1. Certifique-se de que o seu cenário é suportado ao verificar o [limites para alterar os administradores de serviço](#limits).
1. Inicie sessão no [Centro de contas](https://account.windowsazure.com/subscriptions) como o administrador da conta.
1. Selecione uma subscrição.
1. No lado direito, selecione **editar detalhes da subscrição**.

    ![Captura de ecrã que mostra o botão de subscrição de edição no Centro de contas](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. No **administrador de serviço** box, introduza o endereço de e-mail do administrador de serviço novo.

    ![Captura de ecrã que mostra a caixa para alterar o e-mail do Admin de serviço](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>Limitações para alterar os administradores de serviço

* Cada subscrição está associada um diretório do Azure AD. Para localizar o diretório a subscrição está associada, vá para o [portal clássico do Azure](https://manage.windowsazure.com/), selecione **definições** > **subscrições**. Verifique o ID de subscrição para localizar o diretório.
* Se tem sessão iniciada com uma conta escolar ou profissional, pode adicionar outras contas na sua organização como administrador de serviço. Por exemplo, abby@contoso.com pode adicionar bob@contoso.com como administrador de serviço, mas não é possível adicionar john@notcontoso.com , a menos que john@notcontoso.com tem presença no diretório contoso.com. Utilizadores com sessão iniciada sessão em contas escolar ou profissional podem continuar a adicionar utilizadores de Account Microsoft como o administrador de serviço.

  | Método de início de sessão | Adicionar utilizador Account Microsoft como SA? | Adicionar conta escolar ou profissional na mesma organização como SA? | Adicionar conta escolar ou profissional numa organização diferente como SA? |
  | --- | --- | --- | --- |
  |  Conta Microsoft |Sim |Não |Não |
  |  Conta escolar ou profissional |Sim |Sim |Não |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>Alterar o administrador da conta para uma subscrição do Azure

Para alterar o administrador da conta de uma subscrição, consulte [transferir a propriedade de uma subscrição do Azure para outra conta](billing-subscription-transfer.md).

<a name="check-the-account-administrator-of-the-subscription"></a>

**Não tem a certeza que é o administrador da conta?** Siga estes passos.

1. Iniciar sessão para o [Ver subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a subscrição que pretende verificar e, em seguida, procure em **definições**.
1. Selecione **propriedades**. O administrador da conta da subscrição é apresentado no **administrador da conta** caixa.  

## <a name="types-of-azure-admin-accounts"></a>Tipos de contas de administrador do Azure

 Conta de administrador, administrador de serviço e coadministrador são os três tipos de funções de administrador no Microsoft Azure. A tabela seguinte descreve a diferença entre estas três funções administrativas.

| Função administrativa | Limite | Descrição |
| --- | --- | --- |
| Administrador de conta (AA) |1 por conta do Azure |Esta é a pessoa que inscreveu no ou comprou a subscrições do Azure e está autorizada a aceder a [Centro de contas](https://account.azure.com/Subscriptions) e realizar várias tarefas de gestão. Estes incluem a capacidade de criar subscrições, cancelar subscrições, altere a faturação de uma subscrição e alterar o administrador de serviço. |
| Administrador de serviço (SA) |1 por subscrição do Azure |Esta função está autorizada a gerir serviços no [portal do Azure](https://portal.azure.com). Por predefinição, para uma nova subscrição, o administrador da conta também é o administrador de serviço. |
| Coadministrador (AC) no [portal clássico do Azure](https://manage.windowsazure.com) |200 por subscrição |Esta função tem os mesmos privilégios de acesso do Administrador de Serviços, mas não pode alterar a associação de subscrições a diretórios do Azure. |

Azure baseada em função do Active Directory controlo de acesso (RBAC) permite aos utilizadores a adicionar a várias funções. Para obter mais informações, consulte [controlo de acesso baseado em função do Active Directory do Azure](../active-directory/role-based-access-control-configure.md).


## <a name="learn-more-about-resource-access-control-and-active-directory"></a>Saiba mais sobre o controlo de acesso de recursos e do Active Directory

* Para obter mais informações sobre como o acesso a recursos é controlado no Microsoft Azure, consulte o artigo [compreender o acesso a recursos no Azure](../active-directory/active-directory-understanding-resource-access.md).
* Para mais informações sobre o Azure Active Directory, consulte [subscrições do Azure como estão associadas ao Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md) e [atribuir funções de administrador no Azure Active Directory](../active-directory/active-directory-assign-admin-roles-azure-portal.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.
