---
title: "Azure Active Directory dos serviços de domínio: Administrar DNS nos domínios geridos | Microsoft Docs"
description: "Administrar DNS nos domínios geridos de serviços de domínio do Active Directory do Azure"
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
ms.date: 09/23/2017
ms.author: maheshu
ms.openlocfilehash: 3747f87cad4b3701129b7460321b50ee54336585
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="administer-dns-on-an-azure-ad-domain-services-managed-domain"></a>Administrar o DNS num domínio gerido dos serviços de domínio do Azure AD
Os serviços de domínio do Active Directory do Azure inclui um servidor DNS (resolução de nomes de domínio) que fornece a resolução de DNS para o domínio gerido. Ocasionalmente, poderá ter de configurar o DNS no domínio gerido. Poderá ter de criar registos DNS para as máquinas que não estão associados ao domínio, configure os endereços IP virtuais para balanceadores de carga ou programa de configuração de reencaminhadores DNS externos. Por este motivo, os utilizadores que pertencem ao grupo de 'AAD DC administradores' são concedidos privilégios de administração de DNS no domínio gerido.

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas apresentadas neste artigo, tem de:

1. Um **subscrição do Azure**.
2. Um **diretório do Azure AD** -está sincronizada com um diretório no local ou um diretório apenas na nuvem.
3. **Serviços de domínio do Azure AD** tem de estar ativada para o diretório do Azure AD. Se ainda não o feito, siga todas as tarefas descritas no [guia de introdução](active-directory-ds-getting-started.md).
4. A **máquinas de virtuais associados a um domínio** partir do qual administrar domínio gerido dos serviços de domínio do Azure AD. Se não tiver uma máquina de virtual, siga todas as tarefas descritas no artigo intitulado [associar uma máquina virtual do Windows a um domínio gerido](active-directory-ds-admin-guide-join-windows-vm.md).
5. Terá das credenciais de um **conta de utilizador que pertencem ao grupo de 'AAD DC administradores'** no seu diretório, para administrar o DNS para o seu domínio gerido.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-dns-for-the-managed-domain"></a>Tarefa 1 - aprovisionar uma máquina virtual associado a um domínio para administrar remotamente o DNS para o domínio gerido
Domínios geridos de serviços de domínio do AD do Azure podem ser geridos remotamente através do Active Directory administrativas ferramentas familiares, como o Active Directory Centro de administração (ADAC) ou do AD do PowerShell. Da mesma forma, o DNS para o domínio gerido pode ser administrado remotamente utilizando as ferramentas de administração do servidor DNS.

Os administradores no diretório do Azure AD não tem privilégios para ligar aos controladores de domínio no domínio gerido através do ambiente de trabalho remoto. Os membros do grupo 'AAD DC administradores' podem administrar DNS para domínios geridos remotamente utilizando as ferramentas de servidor DNS de um computador Windows Server/cliente que está associado ao domínio gerido. Ferramentas de servidor DNS podem ser instaladas como parte da funcionalidade ferramentas de administração remota de servidor (FARS) opcional no Windows Server e computadores cliente associados a um domínio gerido.

É a primeira tarefa aprovisionar uma máquina virtual do Windows Server que está associada ao domínio gerido. Para obter instruções, consulte o artigo intitulado [associar uma máquina virtual do Windows a um domínio gerido dos serviços de domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-dns-server-tools-on-the-virtual-machine"></a>Tarefa 2 - ferramentas de instalação do servidor DNS na máquina virtual
Execute os seguintes passos para instalar as ferramentas de administração de DNS na máquina virtual associado a um domínio. Para mais informações sobre [instalar e utilizar as ferramentas de administração remota do servidor](https://technet.microsoft.com/library/hh831501.aspx), consulte o Technet.

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
9. No **funcionalidades** página, clique para expandir o **ferramentas de administração remota do servidor** nós e, em seguida, clique para expandir o **ferramentas de administração de funções** nó. Selecione **ferramentas do servidor DNS** funcionalidade da lista de ferramentas de administração de funções.

    ![Página de funcionalidades](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)
10. No **confirmação** página, clique em **instalar** para instalar a funcionalidade de ferramentas do servidor DNS na máquina virtual. Quando a instalação da funcionalidade for concluída com êxito, clique em **fechar** para sair do **para adicionar funções e funcionalidades** assistente.

    ![Página de confirmação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)

## <a name="task-3---launch-the-dns-management-console-to-administer-dns"></a>Tarefa 3 - inicie a consola de gestão do DNS para administrar o DNS
Agora que a funcionalidade de ferramentas do servidor DNS é instalada num domínio associado a máquina virtual, podemos utilizar as ferramentas DNS para administrar o DNS no domínio gerido.

> [!NOTE]
> Tem de ser um membro do grupo 'AAD DC administradores', para administrar o DNS no domínio gerido.
>
>

1. No ecrã Iniciar, clique em **ferramentas administrativas**. Deverá ver o **DNS** consola instalada na máquina virtual.

    ![Ferramentas administrativas - consola DNS](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)
2. Clique em **DNS** para iniciar a consola de gestão de DNS.
3. No **ligar ao servidor DNS** caixa de diálogo, clique na opção intitulada **computador seguinte**e introduza o nome de domínio DNS do domínio gerido (por exemplo, ' contoso100.com').

    ![Consola DNS - ligar ao domínio](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)
4. A consola DNS estabelece ligação ao domínio gerido.

    ![Consola DNS - administrar domínio](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)
5. Agora, pode utilizar a consola DNS para adicionar entradas DNS para os computadores dentro da rede virtual em que ativou a serviços de domínio do AAD.

> [!WARNING]
> Seja cuidadoso ao administrar o DNS para o domínio gerido utilizando as ferramentas de administração de DNS. Certifique-se de que **não eliminar ou modificar os registos DNS incorporados, que são utilizados pelos serviços de domínio no domínio**. Registos DNS incorporados incluem registos DNS do domínio, registos do servidor de nome e outros registos utilizados para a localização de DC. Se modificar estes registos, os serviços de domínio são interrompidos na rede virtual.
>
>

Consulte o [Ferramentas DNS artigo no Technet](https://technet.microsoft.com/library/cc753579.aspx) para obter mais informações sobre a gestão de DNS.

## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de domínio do Azure AD - guia de introdução](active-directory-ds-getting-started.md)
* [Associar uma máquina virtual do Windows a um domínio gerido dos serviços de domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Administrar um domínio gerido dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)
* [Ferramentas de administração de DNS](https://technet.microsoft.com/library/cc753579.aspx)
