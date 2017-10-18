---
title: "Gerir conta de Automatização do Azure | Microsoft Docs"
description: "Este artigo descreve como gerir a configuração da sua conta de Automatização, como uma configuração incorreta, eliminação e renovação de certificado."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/13/2017
ms.author: magoedte
ms.openlocfilehash: fa3109f15cf14a95af35e814fb61a505d3130462
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-automation-account"></a>Gerir conta de Automatização do Azure
Num dado momento antes de a sua conta de Automatização expirar, vai ter de renovar o certificado. Se considerar que a conta Run As tiver sido comprometida, pode eliminá-la e voltar a criá-la. Esta secção mostra como executar estas operações.

## <a name="self-signed-certificate-renewal"></a>Renovação de certificado autoassinado
O certificado autoassinado que criou para a conta Run As expira ao fim de um ano após a data de criação. Pode renová-lo em qualquer altura antes de expirar. Quando o renovar, o certificado atual válido é mantido, para garantir que todos os runbooks que se encontram em fila ou ativamente em execução, e que se autenticam com a conta Run As, não são afetados negativamente. O certificado permanece válido até à data de expiração.

> [!NOTE]
> Se tiver configurado a sua conta Run As de Automatização para utilizar um certificado emitido pela sua autoridade de certificação empresarial e utilizar esta opção, esse certificado empresarial é substituído por um certificado autoassinado.

Para renovar o certificado, faça o seguinte:

1. No portal do Azure, abra a conta de Automatização.

2. Na **Conta de Automatização** 
3. , no painel **Propriedades da conta**, em **Definições da Conta**, selecione **Contas Run As**.

    ![Painel Propriedades da conta de automatização](media/automation-manage-account/automation-account-properties-pane.png)
3. Nas propriedades **Contas Run As**, selecione a conta Run As ou a conta Run As Clássica para a qual pretende renovar o certificado.

4. No painel **Propriedades** da conta selecionada, clique em **Renovar certificado**.

    ![Renovar certificado da conta Run As](media/automation-manage-account/automation-account-renew-runas-certificate.png)

5. Enquanto o certificado estiver a ser renovado, pode acompanhar o progresso em **Notificações** a partir do menu.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Eliminar uma conta Run As ou Run As Clássica
Esta secção descreve como eliminar e recriar uma conta Run As ou Run As Clássica. Quando executar esta ação, a conta de Automatização é mantida. Depois de eliminar uma conta Run As ou Run As Clássica, pode voltar a criá-la no portal do Azure.

1. No portal do Azure, abra a conta de Automatização.

2. Na página **Conta de automatização**, selecione **Contas Run as**.

3. Nas propriedades **Contas Run As**, selecione a conta Run As ou a conta Run As Clássica que quer eliminar. Em seguida, no painel **Propriedades** da conta selecionada, clique em **Eliminar**.

 ![Eliminar a conta Run As](media/automation-manage-account/automation-account-delete-runas.png)

4. Enquanto a conta estiver a ser eliminada, pode acompanhar o progresso em **Notificações** a partir do menu.

5. Assim que a conta tiver sido eliminada, pode voltar a criá-la na página de propriedades **Contas Run As**, ao selecionar a opção de criação **Conta Run As do Azure**.

 ![Recriar a conta Run As de Automatização](media/automation-manage-account/automation-account-create-runas.png)

## <a name="misconfiguration"></a>Configuração incorreta
É possível que alguns itens de configuração necessários para a conta Run As ou Run As Clássica funcionar corretamente tenham sido eliminados ou criados de forma incorreta durante a configuração inicial. Os itens incluem:

* Recurso de certificado
* Recurso de ligação
* A conta Run As foi removida da função de contribuinte
* Principal de serviço ou aplicação no Azure AD

Nas configurações incorretas anteriores e noutras, a conta de Automatização deteta as alterações e apresenta o estado *Não concluída*, no painel de propriedades **Contas Run As** da conta.

![Estado de configuração Não concluída da conta Run As](media/automation-manage-account/automation-account-runas-incomplete-config.png)

Quando selecionar a conta Run As, o painel **Propriedades** da conta apresenta a mensagem de erro seguinte:

![Mensagem de aviso de configuração não concluída de Run As](media/automation-manage-account/automation-account-runas-incomplete-config-msg.png).

Pode resolver rapidamente estes problemas da conta Run As, ao eliminar e recriar a conta.

## <a name="next-steps"></a>Passos seguintes
* Para mais informações sobre Principais de Serviço, consulte a [Objetos da Aplicação e Objetos de Principais de Serviço](../active-directory/active-directory-application-objects.md).
* Para obter mais informações sobre o Controlo de Acesso baseado em Funções na Automatização do Azure, consulte o [Controlo de acesso baseado em funções da Automatização do Azure](automation-role-based-access-control.md).
* Para obter mais informações sobre certificados e serviços do Azure, consulte [Certificates overview for Azure Cloud Services (Descrição geral de certificados para Serviços Cloud do Azure)](../cloud-services/cloud-services-certs-create.md).