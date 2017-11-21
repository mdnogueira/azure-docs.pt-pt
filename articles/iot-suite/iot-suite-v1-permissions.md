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
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 17d9e496aedfdf98423e821e7f379dc481ffa0a5
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
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
* O administrador global pode aprovisionar uma solução pré-configurada e é atribuído um **Admin** função para a aplicação no interior do seu inquilino do AAD.
* Se outro utilizador no mesmo inquilino do AAD criar uma aplicação, a função predefinida concedida ao administrador global é **ReadOnly**.
* Um administrador global pode atribuir utilizadores a funções para aplicações utilizando o [portal do Azure][lnk-portal].

### <a name="domain-user"></a>Utilizador de domínio

Podem existir vários utilizadores de domínio por inquilino do AAD:

* Um utilizador de domínio pode aprovisionar uma solução pré-configurada através de [azureiotsuite.com] [ lnk-azureiotsuite] site. Por predefinição, o utilizador de domínio é concedido a **Admin** função na aplicação aprovisionada.
* Um utilizador de domínio pode criar uma aplicação através do script build.cmd no [azure-iot-remote-monitoring][lnk-rm-github-repo], [azure-iot-predictive-maintenance][lnk-pm-github-repo], ou [do azure-iot-ligado-factory] [ lnk-cf-github-repo] repositório. No entanto, a função predefinida concedida para o utilizador de domínio é **ReadOnly**, porque um utilizador de domínio não tem permissão para atribuir funções.
* Se outro utilizador no inquilino do AAD criar uma aplicação, o utilizador de domínio está atribuído a **ReadOnly** função por predefinição para essa aplicação.
* Um utilizador de domínio não é possível atribuir funções para aplicações; Por conseguinte, um utilizador de domínio não é possível adicionar utilizadores ou funções de utilizador para uma aplicação, mesmo que a tenha aprovisionado.

### <a name="guest-user"></a>Utilizador convidado

Podem existir vários utilizadores convidados por inquilino do AAD. Os utilizadores convidados têm um conjunto limitado de direitos no inquilino do AAD. Como resultado, os utilizadores convidados não podem aprovisionar uma solução pré-configurada no inquilino do AAD.

Para obter mais informações sobre utilizadores e funções no AAD, consulte os seguintes recursos:

* [Criar utilizadores no Azure AD][lnk-create-edit-users]
* [Atribuir utilizadores a aplicações][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Funções de administrador de subscrição do Azure

As funções de administrador do Azure controlam a capacidade de mapear uma subscrição do Azure para um inquilino do AD.

Saber mais sobre as funções de administrador do Azure no artigo [como adicionar ou alterar o Coadministrador do Azure, o administrador de serviço e o administrador de conta][lnk-admin-roles].

## <a name="application-roles"></a>Funções da aplicação

As funções de aplicação controlam o acesso a dispositivos na sua solução pré-configurada.

Existem duas funções definidas e uma função implícita definidos numa aplicação aprovisionada:

* **Administrador:** tem controlo total para adicionar, gerir, remova os dispositivos e modificar as definições.
* **Só de leitura:** podem visualizar dispositivos, regras, ações, tarefas e telemetria.

Pode encontrar as permissões atribuídas a cada função de [Rolepermissions] [ lnk-resource-cs] ficheiro de origem.

### <a name="changing-application-roles-for-a-user"></a>Alterar funções da aplicação para um utilizador

Pode utilizar o procedimento seguinte para tornar um utilizador do seu Active Directory administrador da solução pré-configurada.

Tem de ser um administrador global do AAD para alterar as funções de um utilizador:

1. Aceda ao [portal do Azure][lnk-portal].
2. Selecione **do Azure Active Directory**.
3. Certifique-se de que está a utilizar o diretório que escolheu em azureiotsuite.com Quando aprovisionou a sua solução. Se tiver vários diretórios associados à subscrição, pode alternar entre elas se clicar em seu nome de conta na parte superior direita do portal.
4. Clique em **aplicações empresariais**, em seguida, **todas as aplicações**.
4. Mostrar **todas as aplicações** com **qualquer** estado. Em seguida, procure uma aplicação com o nome da sua solução pré-configurada.
5. Clique no nome da aplicação que corresponde ao nome da sua solução pré-configurada.
6. Clique em **utilizadores e grupos**.
7. Selecione o utilizador cuja função pretende alterar.
8. Clique em **Atribuir**, selecione a função que pretende atribuir ao utilizador (tal como **Administrador**) e clique na marca de verificação.

## <a name="faq"></a>FAQ

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Sou administrador de serviço e gostaria de alterar o mapeamento de diretório entre a minha subscrição e um inquilino do AAD específico. Como concluir esta tarefa?

Consulte [para adicionar uma subscrição existente ao diretório do Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md#to-add-an-existing-subscription-to-your-azure-ad-directory)

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>Sou utilizador/membro do domínio no inquilino do AAD e criei uma solução pré-configurada. Como posso obter a atribuição de uma função para a minha aplicação?

Peça a um administrador global para fazer com que um administrador global no inquilino do AAD e, em seguida, atribuir funções a utilizadores pessoalmente. Em alternativa, peça ao administrador global para lhe atribuir uma função diretamente. Se pretender alterar o inquilino do AAD no qual a sua solução pré-configurada foi implementada, consulte a questão seguinte.

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>Como posso alternar o inquilino do AAD atribuído à minha aplicação e solução pré-configurada de monitorização remota?

Pode executar uma implementação de nuvem a partir de <https://github.com/Azure/azure-iot-remote-monitoring> e voltar a implementar com um inquilino do AAD recém-criado. Uma vez que é, por predefinição, um administrador global quando cria um inquilino do AAD, não tem permissões para adicionar utilizadores e atribuir funções aos mesmos.

1. Criar um diretório do AAD no [portal do Azure][lnk-portal].
2. Aceda a <https://github.com/Azure/azure-iot-remote-monitoring>.
3. Execute `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` (Por exemplo, `build.cmd cloud debug myRMSolution`)
4. Quando solicitado, defina o **tenantid** para ser o inquilino recém-criado em vez do inquilino anterior.

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

[img-flowchart]: media/iot-suite-v1-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]: ../active-directory/active-directory-assign-admin-roles.md
[lnk-portal]: https://portal.azure.com/
[lnk-create-edit-users]: ../active-directory/active-directory-create-users.md
[lnk-assign-app-roles]: ../active-directory/active-directory-coreapps-assign-user-azure-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
