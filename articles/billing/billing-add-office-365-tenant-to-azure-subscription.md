---
title: "Utilizar um inquilino do Office 365 com uma subscrição do Azure | Microsoft Docs"
description: "Saiba como adicionar um diretório do Office 365 (inquilino) para uma subscrição do Azure."
services: 
documentationcenter: 
author: JiangChen79
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: cc9c57c6-7bfd-4dea-9027-c75ef3737589
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/13/2017
ms.author: cjiang
ms.openlocfilehash: e6300932d044ec9a4f88eb5bd5977220ed11d513
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="link-an-office-365-tenant-to-an-azure-subscription"></a>Ligar um inquilino do Office 365 para uma subscrição do Azure
Ligar as suas subscrições do Azure e o Office 365 separadas para que possam aceder ao inquilino do Office 365 da sua subscrição do Azure. Para ligar as suas subscrições, inicie sessão no Azure com a conta de administrador de serviço do Azure, adicionar um diretório e adicione o Office 365 ou escola às contas para o inquilino do Azure Active Directory.

**Pretende mover a sua subscrição do Azure existente ao seu trabalho do Office 365 conta escolar ou profissional?** Se se inscreveu no Azure com uma conta Microsoft pessoal e pretende utilizar, ou inicie sessão com a sua conta Office 365, recomendamos vivamente que transferir a subscrição. Consulte [propriedade de subscrição de transferência do Azure para outra conta](billing-subscription-transfer.md). 

**Pretende iniciar sessão no Azure utilizando o Office 365?** Consulte [inscrever-se no Azure com a conta do Office 365](billing-use-existing-office-365-account-azure-subscription.md). 

## <a name="before-you-begin"></a>Antes de começar
* Tem de ter as credenciais de administrador de serviço da subscrição do Azure. As contas de administrador conjunta não podem efetuar alguns dos passos neste artigo. Para alterar o seu administrador do serviço, consulte [como adicionar ou alterar funções de administrador do Azure](billing-add-change-azure-subscription-administrator.md#change-service-administrator-for-a-subscription).
* Tem de ter as credenciais de um administrador global do inquilino do Office 365.
* O endereço de e-mail do administrador do serviço não tem de ser no inquilino do Office 365.
* O endereço de e-mail do administrador do serviço não pode corresponder ao que qualquer administrador global do inquilino do Office 365.
* Se utilizar um endereço de correio eletrónico que é uma conta Microsoft e uma conta institucional, altere temporariamente o administrador de serviço da sua subscrição do Azure para utilizar outra conta Microsoft. Pode criar uma conta Microsoft no [página de inscrição da conta Microsoft](https://signup.live.com/).

## <a name="link-office-365-tenant-to-azure-subscription"></a>Inquilino do Office 365 de ligação à subscrição do Azure
Para associar o inquilino do Office 365 para a subscrição do Azure, siga estes passos:

### <a name="step-1-add-office-365-tenant-to-your-azure-subscription"></a>Passo 1: Adicionar o inquilino do Office 365 à sua subscrição do Azure

1. Iniciar sessão para o [portal clássico do Azure](https://manage.windowsazure.com/) com as credenciais de administrador de serviço.

    ![Captura de ecrã do Azure início de sessão](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)
2. No painel esquerdo, selecione **do Active Directory**. Não os deveriam ver o inquilino do Office 365. Se vir, avance para o [passo 2: Altere o diretório associado à subscrição do Azure](#Step2).
   
   ![Entrada de captura de ecrã do Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)
3. Selecione **novo** > **DIRETÓRIO** > **criação personalizada**.
   
    ![Criação personalizada de captura de ecrã do Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)
4. No **Adicionar diretório** página **DIRETÓRIO**, selecione **utilizar diretório existente**. Em seguida, selecione **estou pronto para terminar sessão agora**e selecione **concluída** ![ícone concluir](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).
   
    ![Captura de ecrã da "Diretório existente utilizar"](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)
5. Depois de são terminada, inicie sessão com credenciais de administrador global do seu inquilino do Office 365.
   
    ![Captura de ecrã do Office 365 administrador global início de sessão](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)
6. Selecione **continuar**.
   
    ![Captura de ecrã de verificação](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)
7. Selecione **terminar sessão agora**.
   
    ![Captura de ecrã de início de sessão](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)
8. Iniciar sessão para o [portal clássico do Azure](https://manage.windowsazure.com/) com as credenciais de administrador de serviço.
   
    ![Captura de ecrã do Azure início de sessão](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)
9. Deverá ver o seu inquilino do Office 365 no dashboard.
   
    ![Captura de ecrã do dashboard](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

### <a name="Step2"></a>Passo 2: Altere o diretório associado à subscrição do Azure
   
1. Selecione **definições**.
   
    ![Ícone de definições do portal clássico de captura de ecrã do Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)
2. Selecione a sua subscrição do Azure e, em seguida, selecione **editar DIRETÓRIO**.

    ![Diretório de edição de subscrição de captura de ecrã do Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)
3. Selecione **seguinte** ![ícone seguinte](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png).
   
    ![Captura de ecrã da "Alterar o diretório associado"](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)
4. Reveja as contas afetadas. Todos os coadministradores e [controlo de acesso baseado em funções (RBAC)](../active-directory/role-based-access-control-configure.md) utilizadores com acesso atribuído nos grupos de recursos existentes são removidos. O aviso que receberá menciona suportadas apenas a remoção de coadministradores.
      
    ![Captura de ecrã que mostra as contas de coadministrador a serem removidos.](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)
   
    ![Captura de ecrã que mostra uma conta de utilizador de exemplo para ser removido.](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)
5. Selecione **concluída** ![ícone concluir](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

### <a name="step-3-add-your-office-365-organizational-accounts-as-admins-to-the-azure-subscription"></a>Passo 3: Adicionar as contas organizacionais do Office 365 como admins à subscrição do Azure
   
Para adicionar um administrador a sua subscrição do Azure, consulte [adicionar ou alterar funções de administrador do Azure que gerem a subscrição ou serviços](billing-add-change-azure-subscription-administrator.md)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.

