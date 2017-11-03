---
title: "Azure Active Directory dos serviços de domínio: Administrar a política de grupo nos domínios geridos | Microsoft Docs"
description: "Política de grupo de administrador no Azure Active Directory Domain Services domínios geridos"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: aad9e07e040bebe9572af1dd4a2f74b8b384f651
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="administer-group-policy-on-an-azure-ad-domain-services-managed-domain"></a>Administrar a política de grupo num domínio gerido dos serviços de domínio do Azure AD
Os serviços de domínio do Active Directory do Azure inclui incorporada objetos de política de grupo (GPOs) para contentores 'AADDC utilizadores' e 'AADDC computadores'. Pode personalizar estes GPOs incorporados para configurar a política de grupo no domínio gerido. Além disso, os membros do grupo 'AAD DC administradores' podem criar os seus próprios UOs personalizados no domínio gerido. Pode também criar GPOs personalizados e ligá-las para estes UOs personalizados. Os utilizadores que pertencem ao grupo de 'AAD DC administradores' são concedidos privilégios de administração de política de grupo no domínio gerido.

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas apresentadas neste artigo, tem de:

1. Um **subscrição do Azure**.
2. Um **diretório do Azure AD** -está sincronizada com um diretório no local ou um diretório apenas na nuvem.
3. **Serviços de domínio do Azure AD** tem de estar ativada para o diretório do Azure AD. Se ainda não o feito, siga todas as tarefas descritas no [guia de introdução](active-directory-ds-getting-started.md).
4. A **máquinas de virtuais associados a um domínio** partir do qual administrar domínio gerido dos serviços de domínio do Azure AD. Se não tiver uma máquina de virtual, siga todas as tarefas descritas no artigo intitulado [associar uma máquina virtual do Windows a um domínio gerido](active-directory-ds-admin-guide-join-windows-vm.md).
5. Terá das credenciais de um **conta de utilizador que pertencem ao grupo de 'AAD DC administradores'** no seu diretório, para administrar a política de grupo para o seu domínio gerido.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-group-policy-for-the-managed-domain"></a>Tarefa 1 - aprovisionar uma máquina virtual associado a um domínio para administrar remotamente a política de grupo para o domínio gerido
Domínios geridos de serviços de domínio do AD do Azure podem ser geridos remotamente através do Active Directory administrativas ferramentas familiares, como o Active Directory Centro de administração (ADAC) ou do AD do PowerShell. Da mesma forma, a política de grupo para o domínio gerido pode ser administrada remotamente utilizando as ferramentas de administração da política de grupo.

Os administradores no diretório do Azure AD não tem privilégios para ligar aos controladores de domínio no domínio gerido através do ambiente de trabalho remoto. Os membros do grupo 'AAD DC administradores' podem administrar a política de grupo para domínios geridos remotamente. Podem utilizar as ferramentas de política de grupo num computador Windows Server/cliente associado ao domínio gerido. Ferramentas de política de grupo podem ser instaladas como parte da funcionalidade opcional do gestão de políticas de grupo no Windows Server e computadores cliente associados a um domínio gerido.

É a primeira tarefa aprovisionar uma máquina virtual do Windows Server que está associada ao domínio gerido. Para obter instruções, consulte o artigo intitulado [associar uma máquina virtual do Windows a um domínio gerido dos serviços de domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-group-policy-tools-on-the-virtual-machine"></a>Tarefa 2 - ferramentas de política de grupo de instalação na máquina virtual
Execute os seguintes passos para instalar as ferramentas de administração da política de grupo na máquina virtual associado a um domínio.

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
9. No **funcionalidades** página, selecione o **gestão de políticas de grupo** funcionalidade.

    ![Página de funcionalidades](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)
10. No **confirmação** página, clique em **instalar** para instalar a funcionalidade de gestão de políticas de grupo na máquina virtual. Quando a instalação da funcionalidade for concluída com êxito, clique em **fechar** para sair do **para adicionar funções e funcionalidades** assistente.

    ![Página de confirmação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management-confirmation.png)

## <a name="task-3---launch-the-group-policy-management-console-to-administer-group-policy"></a>Tarefa 3 - inicie a consola de gestão de política de grupo para administrar a política de grupo
Pode utilizar a consola de gestão de política de grupo na máquina virtual associado a um domínio para administrar a política de grupo no domínio gerido.

> [!NOTE]
> Tem de ser um membro do grupo 'AAD DC administradores', para administrar a política de grupo no domínio gerido.
>
>

1. No ecrã Iniciar, clique em **ferramentas administrativas**. Deverá ver o **gestão de políticas de grupo** consola instalada na máquina virtual.

    ![Gestão de políticas de grupo de iniciação](./media/active-directory-domain-services-admin-guide/gp-management-installed.png)
2. Clique em **gestão de políticas de grupo** para iniciar a consola de gestão de política de grupo.

    ![Consola de política de grupo](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

## <a name="task-4---customize-built-in-group-policy-objects"></a>Tarefa 4 - Personalizar objetos de política de grupo incorporado
Existem dois incorporada política objetos de grupo (GPOs) - um cada para os contentores 'AADDC computadores' e 'AADDC utilizadores' no seu domínio gerido. Pode personalizar estes GPOs para configurar a política de grupo no domínio gerido.

1. No **gestão de políticas de grupo** consola, clique para expandir o **floresta: contoso100.com** e **domínios** nós para ver as políticas de grupo para o seu domínio gerido.

    ![GPOs incorporadas](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)
2. Pode personalizar estes GPOs incorporados para configurar políticas de grupo no seu domínio gerido. Faça duplo clique no GPO e clique em **editar...**  para personalizar o GPO incorporado. A ferramenta de Editor de configuração de políticas de grupo permite-lhe personalizar o GPO.

    ![Editar o GPO incorporada](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)
3. Agora, pode utilizar o **Editor de gestão de políticas de grupo** consola para editar o GPO incorporado. Por exemplo, a seguinte captura de ecrã mostra como personalizar o GPO 'AADDC computadores' incorporado.

    ![Personalizar o GPO](./media/active-directory-domain-services-admin-guide/gp-editor.png)

## <a name="task-5---create-a-custom-group-policy-object-gpo"></a>Tarefa 5 - criar um objeto de política de grupo (GPO) personalizado
Pode criar ou importar os seus próprios objetos de política de grupo personalizada. Também pode ligar GPOs personalizadas a uma UO personalizada que criou no seu domínio gerido. Para obter mais informações sobre a criação de unidades organizacionais personalizadas, consulte [criar uma UO personalizada um domínio gerido](active-directory-ds-admin-guide-create-ou.md).

> [!NOTE]
> Tem de ser um membro do grupo 'AAD DC administradores', para administrar a política de grupo no domínio gerido.
>
>

1. No **gestão de políticas de grupo** consola, clique para selecionar a sua personalizada unidade organizacional (UO). Faça duplo clique na UO e clique em **criar um GPO neste domínio e ligá-lo aqui...** .

    ![Criar um GPO personalizado](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)
2. Especifique um nome para o novo GPO e clique em **OK**.

    ![Especifique um nome para o GPO](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)
3. Um novo GPO está criado e ligado ao seu UO personalizado. Faça duplo clique no GPO e clique em **editar...**  no menu.

    ![GPO recém-criado](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)
4. Pode personalizar o através de GPO recém-criado o **Editor de gestão de políticas de grupo**.

    ![Personalizar o novo GPO](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)


Obter mais informações sobre como utilizar [consola de gestão de políticas de grupo](https://technet.microsoft.com/library/cc753298.aspx) está disponível no Technet.

## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de domínio do Azure AD - guia de introdução](active-directory-ds-getting-started.md)
* [Associar uma máquina virtual do Windows a um domínio gerido dos serviços de domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Administrar um domínio gerido dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)
* [Consola de gestão de políticas de grupo](https://technet.microsoft.com/library/cc753298.aspx)
