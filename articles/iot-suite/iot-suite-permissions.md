---
title: Azure IoT Suite e Azure Active Directory | Microsoft Docs
description: "Descreve como o Azure IoT Suite utiliza o Azure Active Directory para gerir permissões."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 246228ba-954a-4d96-b6d6-e53e4590cb4f
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: 3eb23ccde4522fdf6dee81c6404dfc5fdf0d0acf
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Permissões no site azureiotsuite.com

## <a name="what-happens-when-you-sign-in"></a>O que acontece quando inicia sessão

Na primeira vez que iniciar sessão em [azureiotsuite.com][lnk-azureiotsuite], o site determina os níveis de permissão com base no inquilino do Azure Active Directory (AAD) atualmente selecionado e subscrição do Azure.

1. Em primeiro lugar, para preencher a lista de inquilinos junto ao seu nome de utilizador, o site localiza a partir do Azure que os inquilinos do AAD pertence. Atualmente, o site apenas pode obter os tokens de utilizador para um inquilino cada vez. Por conseguinte, quando os inquilinos através da lista pendente no canto superior direito, o site iniciar a sua sessão nesse inquilino para obter os tokens para esse inquilino.

2. Em seguida, através do Azure, o site localiza as subscrições às quais o inquilino selecionado está associado. Pode ver as subscrições disponíveis quando cria uma nova solução pré-configurada.

3. Por último, o site obtém todos os recursos nas subscrições e nos grupos de recursos marcados como soluções pré-configuradas e preenche os mosaicos na home page.

As secções seguintes descrevem as funções que controlam o acesso às soluções pré-configuradas.

## <a name="aad-roles"></a>Funções do AAD

As funções do AAD controlam a capacidade de aprovisionar soluções pré-configuradas e gerir utilizadores numa solução pré-configurada.

Pode encontrar mais informações sobre funções de administrador no AAD em [atribuir funções de administrador no Azure AD][lnk-aad-admin]. O atual artigo incida no **Administrador Global** e **utilizador** funções de diretório, conforme utilizadas pelas soluções pré-configuradas.

### <a name="global-administrator"></a>Administrador global

Podem existir vários administradores globais por inquilino do AAD:

* Quando cria um inquilino do AAD, por predefinição, é o administrador global desse inquilino.
* O administrador global pode aprovisionar um soluções pré-configuradas básico e padrão.

### <a name="domain-user"></a>Utilizador de domínio

Podem existir vários utilizadores de domínio por inquilino do AAD:

* Um utilizador de domínio pode aprovisionar uma solução pré-configurada básica através de [azureiotsuite.com] [ lnk-azureiotsuite] site.
* Um utilizador de domínio pode criar uma solução pré-configurada básica utilizando a CLI.

### <a name="guest-user"></a>Utilizador convidado

Podem existir vários utilizadores convidados por inquilino do AAD. Os utilizadores convidados têm um conjunto limitado de direitos no inquilino do AAD. Como resultado, os utilizadores convidados não podem aprovisionar uma solução pré-configurada no inquilino do AAD.

Para obter mais informações sobre utilizadores e funções no AAD, consulte os seguintes recursos:

* [Criar utilizadores no Azure AD][lnk-create-edit-users]
* [Atribuir utilizadores a aplicações][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Funções de administrador de subscrição do Azure

As funções de administrador do Azure controlam a capacidade de mapear uma subscrição do Azure para um inquilino do AD.

Saber mais sobre as funções de administrador do Azure no artigo [como adicionar ou alterar o Coadministrador do Azure, o administrador de serviço e o administrador de conta][lnk-admin-roles].

## <a name="faq"></a>FAQ

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Sou administrador de serviço e gostaria de alterar o mapeamento de diretório entre a minha subscrição e um inquilino do AAD específico. Como concluir esta tarefa?

Consulte [para adicionar uma subscrição existente ao diretório do Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md#to-add-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>Quero alterar um Administrador de Serviço ou Coadministrador depois de iniciar sessão com uma conta institucional.

Consulte o artigo de suporte [alterar o administrador de serviço e Coadministrador depois da sessão iniciada com uma conta institucional][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Porque estou a ver este erro? "A sua conta não tem as permissões adequadas para criar uma solução. Consulte o seu administrador de conta ou tente com uma conta diferente."

Observe o diagrama seguinte para obter orientações sobre:

![][img-flowchart]

> [!NOTE]
> Se continuar a ver o erro depois de confirmar que é um administrador global no inquilino do AAD e coadministrador na subscrição, ter o seu administrador de conta, remova o utilizador e reatribuir as permissões necessárias pela seguinte ordem. Em primeiro lugar, adicione o utilizador como um administrador global e, em seguida, adicione o utilizador como coadministrador na subscrição do Azure. Se os problemas persistirem, contacte [ajuda e suporte][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Porque estou a ver este erro quando tenho uma subscrição do Azure? "Uma subscrição do Azure é necessário para criar soluções pré-configuradas. Pode criar uma conta de avaliação gratuita em apenas alguns minutos."

Se tem a certeza de que tem uma subscrição do Azure, valide o mapeamento do inquilino da sua subscrição e certifique-se de que o inquilino correto está selecionado na lista pendente. Se confirmou o inquilino pretendido está correto, siga o diagrama anterior e valide o mapeamento da sua subscrição e este inquilino do AAD.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o IoT Suite, veja como pode [personalizar uma solução pré-configurada][lnk-customize].

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]: ../active-directory/active-directory-assign-admin-roles-azure-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-create-edit-users]: ../active-directory/active-directory-create-users.md
[lnk-assign-app-roles]: ../active-directory/active-directory-coreapps-assign-user-azure-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
