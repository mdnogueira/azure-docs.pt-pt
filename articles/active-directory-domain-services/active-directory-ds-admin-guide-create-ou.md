---
title: "Serviços de domínio do Azure Active Directory: Guia de administração | Microsoft Docs"
description: "Criar uma unidade organizacional (UO) em domínios geridos de serviços de domínio do Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: 017a8cabe81743af4c0cbb694098df799a904468
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>Criar uma unidade organizacional (UO) num domínio gerido dos serviços de domínio do Azure AD
Domínios geridos de serviços de domínio do AD do Azure incluem dois contentores incorporadas, denominadas 'AADDC computadores' e 'AADDC utilizadores' respetivamente. O contentor 'AADDC computadores' tem objetos de computador para todos os computadores que estão associados ao domínio gerido. O contentor 'AADDC utilizadores' inclui os utilizadores e grupos no inquilino do Azure AD. Ocasionalmente, poderá ser necessário criar contas de serviço no domínio gerido para implementar cargas de trabalho. Para esta finalidade, pode criar uma unidade organizacional personalizado (UO) no domínio gerido e criar contas de serviço nessa UO. Este artigo mostra como criar uma UO no seu domínio gerido.

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas apresentadas neste artigo, tem de:

1. Um **subscrição do Azure**.
2. Um **diretório do Azure AD** -está sincronizada com um diretório no local ou um diretório apenas na nuvem.
3. **Serviços de domínio do Azure AD** tem de estar ativada para o diretório do Azure AD. Se ainda não o feito, siga todas as tarefas descritas no [guia de introdução](active-directory-ds-getting-started.md).
4. Uma máquina virtual associado a um domínio do qual administrar os serviços de domínio do Azure AD geridos domínio. Se não tiver uma máquina de virtual, siga todas as tarefas descritas no artigo intitulado [associar uma máquina virtual do Windows a um domínio gerido](active-directory-ds-admin-guide-join-windows-vm.md).
5. Terá das credenciais de um **conta de utilizador que pertencem ao grupo de 'AAD DC administradores'** no seu diretório, para criar uma UO personalizada no seu domínio gerido.

## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>Instalar ferramentas de administração do AD numa máquina virtual associado a um domínio para administração remota
Domínios geridos de serviços de domínio do AD do Azure podem ser geridos remotamente através do Active Directory administrativas ferramentas familiares, como o Active Directory Centro de administração (ADAC) ou do AD do PowerShell. Os administradores inquilinos não dispõe de privilégios para ligar aos controladores de domínio no domínio gerido através do ambiente de trabalho remoto. Para administrar o domínio gerido, instale a funcionalidade de ferramentas de administração do AD numa máquina virtual associada ao domínio gerido. Consulte o artigo intitulado [administrar um domínio gerido dos serviços de domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md) para obter instruções.

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>Criar uma unidade organizacional no domínio gerido
Agora que estão instaladas as ferramentas de administração do AD no domínio associado a máquina virtual, podemos utilizar estas ferramentas para criar uma unidade organizacional no domínio gerido. Execute os seguintes passos:

> [!NOTE]
> Apenas os membros do grupo 'AAD DC administradores' tem os privilégios necessários para criar uma UO personalizada. Certifique-se de que efetua os passos seguintes como um utilizador pertence a este grupo.
>
>

1. No ecrã Iniciar, clique em **ferramentas administrativas**. Deverá ver as ferramentas administrativas AD instaladas na máquina virtual.

    ![Ferramentas administrativas instaladas no servidor](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Clique em **Centro de administração do Active Directory**.

    ![Centro de administração do Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Para ver o domínio, clique no nome de domínio no painel da esquerda (por exemplo, ' contoso100.com').

    ![ADAC - domínio de vista](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)
4. No lado direito **tarefas** painel, clique em **novo** sob o nó de nome de domínio. Neste exemplo, clique **novo** sob o nó 'contoso100(local)' no lado direito **tarefas** painel.

    ![ADAC - nova UO](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)
5. Deverá ver a opção para criar uma unidade organizacional. Clique em **unidade organizacional** para iniciar o **criar a unidade organizacional** caixa de diálogo.
6. No **criar a unidade organizacional** caixa de diálogo, especifique um **nome** para o nova UO. Forneça uma breve descrição para a UO. Também pode definir o **gerido por** campo para a UO. Para criar a UO personalizada, clique em **OK**.

    ![ADAC - criar caixa de diálogo de UO](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)
7. A UO recentemente criada deverá agora aparecer no Centro de administração AD (ADAC).

    ![ADAC - UO criado](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="permissionssecurity-for-newly-created-ous"></a>Permissões/segurança criado recentemente UOs
Por predefinição, (membro do grupo 'AAD DC administradores') que criou a UO personalizada é concedido ao utilizador privilégios administrativos (controlo total) através da UO. O utilizador, em seguida, pode avançar e conceder privilégios para outros utilizadores ou para o grupo 'AAD DC administradores' conforme pretendido. Como mostrado na captura de ecrã seguinte, o utilizador 'bob@domainservicespreview.onmicrosoft.com' quem criou nova unidade organizacional 'MyCustomOU' é concedido controlo total sobre os mesmos.

 ![ADAC - novo segurança UO](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)

## <a name="notes-on-administering-custom-ous"></a>Notas sobre o administrar UOs personalizados
Agora que criou uma UO personalizada, pode avançar e criar utilizadores, grupos, computadores e contas de serviço nessa UO. Não é possível mover a utilizadores ou grupos a UO 'AADDC utilizadores para UOs personalizados.

> [!WARNING]
> Contas de utilizador, grupos, as contas de serviço e objetos de computador que criar em UOs personalizados não estão disponíveis no seu inquilino do Azure AD. Por outras palavras, estes objetos não mostram utilizando a API do Azure AD Graph ou na IU do Azure AD. Estes objetos só estão disponíveis no seu domínio gerido dos serviços de domínio do Azure AD.
>
>

## <a name="related-content"></a>Conteúdo relacionado
* [Administrar um domínio gerido dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)
* [Configurar política de grupo um domínio gerido](active-directory-ds-admin-guide-administer-group-policy.md)
* [Centro de administração do Active Directory: Introdução](https://technet.microsoft.com/library/dd560651.aspx)
* [Guia passo a passo de contas de serviço](https://technet.microsoft.com/library/dd548356.aspx)
