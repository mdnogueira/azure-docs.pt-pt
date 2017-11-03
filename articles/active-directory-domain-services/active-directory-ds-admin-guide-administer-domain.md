---
title: "Azure Active Directory dos serviços de domínio: Administrar um domínio gerido | Microsoft Docs"
description: "Administrar o Azure Active Directory Domain Services de domínios geridos"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: d4fdbc75-3e6b-4e20-8494-5dcc3bf2220a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: maheshu
ms.openlocfilehash: 8edf01d994e54a8f1af98861cb445a5909b6a1df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="administer-an-azure-active-directory-domain-services-managed-domain"></a>Administrar um domínio gerido pelos Serviços de Domínio do Azure Active Directory
Este artigo mostra como administrar um domínio gerido dos serviços de domínio do Azure Active Directory (AD).

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas apresentadas neste artigo, tem de:

1. Um **subscrição do Azure**.
2. Um **diretório do Azure AD** -está sincronizada com um diretório no local ou um diretório apenas na nuvem.
3. **Serviços de domínio do Azure AD** tem de estar ativada para o diretório do Azure AD. Se ainda não o feito, siga todas as tarefas descritas no [guia de introdução](active-directory-ds-getting-started.md).
4. A **máquinas de virtuais associados a um domínio** partir do qual administrar domínio gerido dos serviços de domínio do Azure AD. Se não tiver uma máquina de virtual, siga todas as tarefas descritas no artigo intitulado [associar uma máquina virtual do Windows a um domínio gerido](active-directory-ds-admin-guide-join-windows-vm.md).
5. Terá das credenciais de um **conta de utilizador que pertencem ao grupo de 'AAD DC administradores'** no seu diretório, para administrar o seu domínio gerido.

<br>

## <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>Tarefas administrativas que pode efetuar um domínio gerido
Os membros do grupo 'AAD DC administradores' são concedidos privilégios no domínio gerido que ativá-los efetuar tarefas, tais como:

* Associe computadores ao domínio gerido.
* Configurar o GPO incorporado para os contentores “Computadores do AAD DC” e “Utilizadores do AAD DC” no domínio gerido.
* Administrar o DNS no domínio gerido.
* Criar e administrar personalizado unidades organizacionais (UOs) no domínio gerido.
* Obter acesso administrativo aos computadores associados ao domínio gerido.

## <a name="administrative-privileges-you-do-not-have-on-a-managed-domain"></a>Privilégios administrativos não dispõe de um domínio gerido
O domínio é gerido pela Microsoft, incluindo atividades tais como a aplicação de patches, monitorização e, efetua cópias de segurança. Por conseguinte, o domínio está bloqueado para baixo e não tem privilégios para efetuar determinadas tarefas administrativas no domínio. Alguns exemplos de tarefas que não é possível efetuar são abaixo.

* Não são concedidos privilégios de administrador do domínio ou de administrador de empresa para o domínio gerido.
* Não é possível expandir o esquema do domínio gerido.
* Não é possível ligar aos controladores de domínio para o domínio gerido utilizando o ambiente de trabalho remoto.
* Não é possível adicionar os controladores de domínio para o domínio gerido.

## <a name="task-1---provision-a-domain-joined-windows-server-virtual-machine-to-remotely-administer-the-managed-domain"></a>Tarefa 1 - aprovisionar uma máquina de virtual associado a um domínio Windows Server para administrar remotamente o domínio gerido
Domínios geridos de serviços de domínio do AD do Azure podem ser geridos através do Active Directory administrativas ferramentas familiares, como o Active Directory Centro de administração (ADAC) ou do AD do PowerShell. Os administradores inquilinos não dispõe de privilégios para ligar aos controladores de domínio no domínio gerido através do ambiente de trabalho remoto. Por conseguinte, os membros do grupo 'AAD DC administradores' podem administrar domínios geridos remotamente utilizando ferramentas administrativas do AD de um computador Windows Server/cliente que está associado ao domínio gerido. Ferramentas de administração do AD podem ser instaladas como parte da funcionalidade ferramentas de administração remota de servidor (FARS) opcional no Windows Server e computadores cliente associados a um domínio gerido.

É o primeiro passo para configurar uma máquina virtual do Windows Server que está associada ao domínio gerido. Para obter instruções, consulte o artigo intitulado [associar uma máquina virtual do Windows a um domínio gerido dos serviços de domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md).

### <a name="remotely-administer-the-managed-domain-from-a-client-computer-for-example-windows-10"></a>Administrar remotamente o domínio gerido a partir de um computador cliente (por exemplo, o Windows 10)
As instruções no utilize artigo uma máquina virtual do Windows para administrar o AAD-DS geridos domínio. No entanto, também pode optar por utilizar a máquina virtual cliente (por exemplo, o Windows 10) do Windows para o fazer.

Pode [instalar ferramentas de administração remota de servidor (FARS)](http://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) na máquina virtual do cliente Windows ao seguir as instruções no TechNet.

## <a name="task-2---install-active-directory-administration-tools-on-the-virtual-machine"></a>Tarefa 2 - ferramentas de administração do Active Directory de instalação na máquina virtual
Execute os seguintes passos para instalar as ferramentas de administração do Active Directory na máquina virtual associado a um domínio. Consulte o Technet para obter mais informações [informações sobre como instalar e utilizar as ferramentas de administração remota do servidor](https://technet.microsoft.com/library/hh831501.aspx).

1. Navegue para o portal do Azure. Clique em **todos os recursos** no painel esquerdo. Localize e clique na máquina virtual que criou na tarefa 1.
2. Clique em de **Connect** botão no separador de descrição geral. Um ficheiro de protocolo de ambiente de trabalho remoto (RDP) é criado e transferido.

    ![Ligar à máquina virtual do Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Para ligar à sua VM, abra o ficheiro RDP transferido. Se lhe for solicitado, clique em **Ligar**. Na linha de início de sessão, utilize as credenciais de utilizadores que pertençam ao grupo de 'AAD DC administradores'. Por exemplo, utilizamos 'bob@domainservicespreview.onmicrosoft.com' no nosso caso. Poderá receber um aviso de certificado durante o processo de início de sessão. Clique em Sim ou continuar a prosseguir com a ligação.
4. A partir do ecrã Iniciar, abra **Gestor de servidor**. Clique em **para adicionar funções e funcionalidades** no painel central da janela do Gestor de servidor.

    ![Inicie o Gestor de servidor numa máquina virtual](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. No **antes de começar** página do **Assistente Adicionar funções e funcionalidades**, clique em **seguinte**.

    ![Antes de começar a página](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. No **tipo de instalação** página, deixe o **instalação baseada em funções ou baseada em funcionalidade** opção selecionada e clique em **seguinte**.

    ![Página tipo de instalação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. No **seleção de servidor** página, selecione a máquina virtual atual no agrupamento de servidores e clique em **seguinte**.

    ![Página de seleção de servidor](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. No **funções de servidor** página, clique em **seguinte**. Vamos ignorar esta página, uma vez que vamos não estiver a instalar quaisquer funções no servidor.
9. No **funcionalidades** página, clique para expandir o **ferramentas de administração remota do servidor** nós e, em seguida, clique para expandir o **ferramentas de administração de funções** nó. Selecione **AD DS e AD LDS ferramentas** funcionalidade da lista de ferramentas de administração de funções.

    ![Página de funcionalidades](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)
10. No **confirmação** página, clique em **instalar** para instalar o AD e a funcionalidade de ferramentas do AD LDS na máquina virtual. Quando a instalação da funcionalidade for concluída com êxito, clique em **fechar** para sair do **para adicionar funções e funcionalidades** assistente.

    ![Página de confirmação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)

## <a name="task-3---connect-to-and-explore-the-managed-domain"></a>Tarefa 3 - Ligar a e explorar o domínio gerido
Agora que estão instaladas as ferramentas de administração do AD no domínio associado a máquina virtual, podemos utilizar estas ferramentas para explorar e administrar o domínio gerido.

> [!NOTE]
> Tem de ser membro do grupo 'AAD DC administradores', para administrar o domínio gerido.
>
>

1. No ecrã Iniciar, clique em **ferramentas administrativas**. Deverá ver as ferramentas administrativas AD instaladas na máquina virtual.

    ![Ferramentas administrativas instaladas no servidor](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Clique em **Centro de administração do Active Directory**.

    ![Centro de administração do Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Para explorar o domínio, clique no nome de domínio no painel da esquerda (por exemplo, ' contoso100.com'). Tenha em atenção dois contentores chamados 'AADDC computadores' e 'AADDC utilizadores' respetivamente.

    ![ADAC - domínio de vista](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)
4. Clique em de contentor denominado **AADDC utilizadores** para ver todos os utilizadores e grupos que pertençam ao domínio gerido. Deverá ver as contas de utilizador e grupos do seu Azure AD inquilino Mostrar cópias de segurança neste contentor. Observe que, neste exemplo, uma conta de utilizador para o utilizador chamado 'Bernardo' e um grupo chamado 'AAD DC administradores' estão disponíveis neste contentor.

    ![ADAC - utilizadores de domínio](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)
5. Clique em de contentor denominado **AADDC computadores** para ver os computadores associados a este domínio gerido. Deverá ver uma entrada para a máquina virtual atual, que está associada ao domínio. Contas de computador para todos os computadores que estão associados ao domínio gerido dos serviços de domínio do Azure AD são armazenadas neste contentor 'AADDC computadores'.

    ![ADAC - computadores associados ao domínio](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de domínio do Azure AD - guia de introdução](active-directory-ds-getting-started.md)
* [Associar uma máquina virtual do Windows a um domínio gerido dos serviços de domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Implementar ferramentas de administração do servidor remoto](https://technet.microsoft.com/library/hh831501.aspx)
