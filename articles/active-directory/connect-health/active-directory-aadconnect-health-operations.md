---
title: "Operações do Azure Active Directory Connect Health"
description: "Este artigo descreve as operações adicionais que podem ser efetuadas depois de ter implementado o Azure AD Connect Health."
services: active-directory
documentationcenter: 
author: karavar
manager: femila
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 06afc6b4149ea1590a2994d1638d6979a89035e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-connect-health-operations"></a>Operações do Azure Active Directory Connect Health
Este tópico descreve as várias operações, que pode realizar com o Azure Active Directory (Azure AD) Connect Health.

## <a name="enable-email-notifications"></a>Ativar notificações por e-mail
Pode configurar o serviço do Azure AD Connect Health para enviar notificações por e-mail quando alertas indicam que a sua infraestrutura de identidade não está em bom estada. Isto ocorre quando é gerado um alerta e, quando está resolvido.

![Definições de notificação de correio eletrónico do captura de ecrã do Azure AD Connect Health](./media/active-directory-aadconnect-health/email_noti_discover.png)

> [!NOTE]
> Notificações por e-mail estão ativadas por predefinição.
>
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Para ativar notificações de e-mail do Azure AD Connect Health
1. Abra o **alertas** painel para o serviço para os quais pretende receber a notificação por correio eletrónico.
2. Na barra de ação, clique em **as definições de notificação**.
3. No comutador de notificação de correio eletrónico, selecione **ON**.
4. Selecione a caixa de verificação se pretender que todos os administradores globais para receber notificações por e-mail.
5. Se pretender receber notificações de e-mail em outros endereços de correio eletrónico, especifique-os no **destinatários de E-Mail adicionais** caixa. Para remover um endereço de correio eletrónico desta lista, faça duplo clique na entrada e selecione **eliminar**.
6. Para finalizar as alterações, clique em **guardar**. As alterações entram em vigor apenas depois de guardar.

## <a name="delete-a-server-or-service-instance"></a>Eliminar uma instância de servidor ou serviço

Em alguns casos, pode querer remover a um servidor que está a ser monitorizado. Eis o que precisa de saber para remover um servidor do serviço do Azure AD Connect Health.

Quando estiver a eliminar um servidor, tenha em atenção o seguinte:

* Esta ação para recolher quaisquer novos dados a partir desse servidor. Este servidor é removido do serviço de monitorização. Após esta ação não é possível visualizar novos alertas, monitorizar ou dados de análise de utilização para este servidor.
* Esta ação não desinstala o agente de estado de funcionamento do seu servidor. Se não o desinstalou o agente de estado de funcionamento antes de executar este passo, poderá ver erros relacionados com o agente de estado de funcionamento no servidor.
* Esta ação não elimina os dados recolhidos a partir deste servidor. Os dados serem eliminados de acordo com a política de retenção de dados do Azure.
* Depois de efetuar esta ação, se pretender iniciar a monitorização do mesmo servidor novamente, tem de desinstalar e reinstalar o agente de estado de funcionamento neste servidor.

### <a name="to-delete-a-server-from-the-azure-ad-connect-health-service"></a>Para eliminar um servidor do serviço do Azure AD Connect Health
Azure AD Connect Health para serviços de Federação do Active Directory (AD FS) e do Azure AD Connect (sincronização):

1. Abra o **servidor** painel do **lista de servidores** painel ao selecionar o nome do servidor a remover.
2. No **servidor** painel, na barra de ação, clique em **eliminar**.
3. O confirme, escrevendo o nome do servidor na caixa de confirmação.
4. Clique em **Eliminar**.

Azure AD Connect Health para os serviços de domínio do Azure Active Directory:

1. Abra o **controladores de domínio** dashboard.
2. Selecione o controlador de domínio a ser removido.
3. Na barra de ação, clique em **eliminar selecionado**.
4. Confirme a ação para eliminar o servidor.
5. Clique em **Eliminar**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Eliminar uma instância de serviço do serviço do Azure AD Connect Health
Em alguns casos, pode querer remover uma instância de serviço. Eis o que precisa de saber para remover uma instância de serviço do serviço do Azure AD Connect Health.

Quando estiver a eliminar uma instância de serviço, tenha em atenção o seguinte:

* Esta ação remove a instância de serviço atual do serviço de monitorização.
* Esta ação não desinstalar ou remover o agente de estado de funcionamento de qualquer um dos servidores que foram monitorizados como parte desta instância de serviço. Se não o desinstalou o agente de estado de funcionamento antes de executar este passo, poderá ver erros relacionados com o agente de estado de funcionamento nos servidores.
* Todos os dados desta instância de serviço são eliminados de acordo com a política de retenção de dados do Azure.
* Depois de efetuar esta ação, se pretender iniciar a monitorização do serviço, desinstale e reinstale o agente de estado de funcionamento de todos os servidores. Depois de efetuar esta ação, se pretender iniciar a monitorização do mesmo servidor novamente, desinstalar, reinstalar e registar o agente de estado de funcionamento nesse servidor.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Para eliminar uma instância de serviço do serviço do Azure AD Connect Health
1. Abra o **serviço** painel do **lista serviço** painel selecionando o identificador de serviço (nome do farm) que pretende remover.
2. No **servidor** painel, na barra de ação, clique em **eliminar**.
3. Confirmar, escrevendo o nome do serviço na caixa de confirmação (por exemplo: sts.contoso.com).
4. Clique em **Eliminar**.
   <br><br>

[//]: # (Start of RBAC section)
## <a name="manage-access-with-role-based-access-control"></a>Gerir o acesso com controlo de acesso baseado em funções
[Controlo de acesso baseado em funções (RBAC)](../role-based-access-control-configure.md) para o Azure AD Connect Health fornece acesso aos utilizadores e grupos que não sejam administradores globais. RBAC atribui funções para os utilizadores pretendidos e grupos e fornece um mecanismo para limitar os administradores globais no seu diretório.

### <a name="roles"></a>Funções
O Azure AD Connect Health suporta as seguintes funções incorporadas:

| Função | Permissões |
| --- | --- |
| Proprietário |Os proprietários podem *gerir o acesso* (por exemplo, atribua uma função a um utilizador ou grupo), *ver todas as informações* (por exemplo, ver alertas) no portal, e *alterar definições* (por exemplo, notificações por correio eletrónico) no Azure AD Connect Health. <br>Por predefinição, os administradores globais do Azure AD são atribuídos esta função e não pode ser alterado. |
| Contribuinte |Os contribuintes podem *ver todas as informações* (por exemplo, ver alertas) no portal, e *alterar definições* (por exemplo, notificações por correio eletrónico) no Azure AD Connect Health. |
| Leitor |Os leitores podem *ver todas as informações* (por exemplo, ver alertas) no portal no Azure AD Connect Health. |

Todas as outras funções (por exemplo, os administradores de acesso de utilizador ou utilizadores do DevTest Labs) tem sem afetar a acesso no Azure AD Connect Health, mesmo que as funções estão disponíveis na experiência do portal.

### <a name="access-scope"></a>Âmbito de acesso
O Azure AD Connect Health suporta acesso de gestão em dois níveis:

* **Todas as instâncias de serviço**: Este é o caminho recomendado na maioria dos casos. Controla o acesso para todas as instâncias de serviço (por exemplo, um farm do AD FS) em todos os tipos de função que estão a ser monitorizados pelo Azure AD Connect Health.
* **Instância de serviço**: em alguns casos, poderá ter de segregar acesso com base nos tipos de função ou por uma instância de serviço. Neste caso, pode gerir o acesso ao nível da instância de serviço.  

Permissão é concedida se um utilizador final tem acesso no serviço de diretório ou nível de instância.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Permitir que os utilizadores ou de acesso a grupos para o Azure AD Connect Health
Os passos seguintes mostram como permitir o acesso.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Passo 1: Selecione o âmbito de acesso apropriadas
Para permitir um acesso de utilizador no *todas as instâncias de serviço* nível no Azure AD Connect Health, abra o painel principal no Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Passo 2: Adicionar utilizadores e grupos e atribuir funções
1. Do **configurar** secção, clique em **utilizadores**.<br>
   ![Painel, da principal de ligar RBAC de estado de funcionamento de captura de ecrã do Azure AD com utilizadores realçado](./media/active-directory-aadconnect-health/RBAC_main_blade.png)
2. Selecione **Adicionar**.
3. No **selecionar uma função** painel, selecione uma função (por exemplo, **proprietário**).<br>
   ![Janela de ligar os utilizadores RBAC de estado de funcionamento de captura de ecrã do Azure AD](./media/active-directory-aadconnect-health/RBAC_add.png)
4. Escreva o nome ou o identificador do destino de utilizador ou grupo. Pode selecionar um ou mais utilizadores ou grupos ao mesmo tempo. Clique em **Selecionar**.
   ![Janela de ligar os utilizadores RBAC de estado de funcionamento de captura de ecrã do Azure AD](./media/active-directory-aadconnect-health/RBAC_select_users.png)
5. Selecione **OK**.<br>
6. Depois de concluída a atribuição de função, os utilizadores e grupos são apresentados na lista.<br>
   ![Janela de ligar utilizadores RBAC do Estado de funcionamento de captura de ecrã do Azure AD, com os novos utilizadores realçado](./media/active-directory-aadconnect-health/RBAC_user_list.png)

Agora listados utilizadores e grupos têm acesso, de acordo com as respetivas funções atribuídas.

> [!NOTE]
> * Os administradores globais sempre dispor de acesso completo a todas as operações, mas as contas de administrador global não estão presentes na lista anterior.
> * A funcionalidade de convidar os utilizadores não é suportada no Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Passo 3: Partilhar a localização do painel com utilizadores ou grupos
1. Depois de atribuir permissões, um utilizador pode aceder do Azure AD Connect Health acedendo [aqui](http://aka.ms/aadconnecthealth).
2. No painel, o utilizador pode afixar o painel ou diferentes partes do mesmo ao dashboard. Basta clicar o **afixar ao dashboard** ícone.<br>
   ![Captura de ecrã do Azure AD Connect RBAC de estado de funcionamento afixar painel, com o ícone de pino realçado](./media/active-directory-aadconnect-health/RBAC_pin_blade.png)

> [!NOTE]
> Um utilizador com a função de leitor atribuído não é possível obter a extensão do Azure AD Connect Health do Azure Marketplace. O utilizador não é possível efetuar o necessário "Criar" operação para o fazer. O utilizador pode receber para o painel acedendo a hiperligação anterior. Para utilização subsequente, o utilizador pode afixar painel ao dashboard.
>
>

### <a name="remove-users-or-groups"></a>Remover utilizadores ou grupos
Pode remover um utilizador ou um grupo adicionado ao RBAC de estado de funcionamento ligar do Azure AD. Basta com o botão direito do utilizador ou grupo e selecione **remover**.<br>
![Janela de ligar os utilizadores RBAC de estado de funcionamento de captura de ecrã do Azure AD, com remover realçado](./media/active-directory-aadconnect-health/RBAC_remove.png)

[//]: # (End of RBAC section)

## <a name="next-steps"></a>Passos seguintes
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalação do Agente do Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Utilizar o Azure AD Connect Health com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Utilizar o Azure AD Connect Health para sincronização](active-directory-aadconnect-health-sync.md)
* [Utilizar o Azure AD Connect Health com o AD DS](active-directory-aadconnect-health-adds.md)
* [FAQ do Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Histórico de versões do Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
