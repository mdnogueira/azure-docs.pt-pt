---
title: "Azure Active Directory dos serviços de domínio: Associar a VM do Windows Server a um domínio gerido | Microsoft Docs"
description: "Associar uma máquina virtual do Windows nos serviços de domínio do Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 29316313-c76c-4fb9-8954-5fa5ec82609e
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: maheshu
ms.openlocfilehash: 5f661dba2e647ac905e7d84927fdbf6dbc76094f
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Associar uma máquina virtual do Windows Server a um domínio gerido
Este artigo mostra como implementar uma máquina virtual de servidor Windows utilizando o portal do Azure. Em seguida, mostra como associar a máquina virtual a um domínio gerido dos serviços de domínio do Azure AD.

## <a name="step-1-create-the-windows-server-virtual-machine"></a>Passo 1: Criar a máquina virtual do Windows Server
Execute os seguintes passos para criar uma máquina virtual do Windows associada à rede virtual em que tiver ativado os serviços de domínio do Azure AD.

1. Inicie sessão no portal do Azure em [http://portal.azure.com](http://portal.azure.com).
2. Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.
3. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**.

    ![Selecione a imagem](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)
4. Configure as definições básicas para a máquina virtual no **Noções básicas** página do assistente.

    ![Configurar as definições básicas para VM](./media/active-directory-domain-services-admin-guide/create-windows-vm-basics.png)

    > [!TIP]
    > O nome de utilizador e palavra-passe que introduzir aqui são para uma conta de administrador local utilizada para iniciar sessão máquina virtual. Escolha uma palavra-passe segura para proteger a máquina virtual contra ataques de força bruta de palavra-passe. Introduza aqui a credenciais de uma conta de utilizador de domínio.
    >

5. Selecione um **tamanho** para a máquina virtual. Para ver mais tamanhos, selecione **Visualizar todos** ou altere o filtro **Tipo de disco suportado**.

    ![Selecione o tamanho da VM](./media/active-directory-domain-services-admin-guide/create-windows-vm-size.png)

6. No **definições** página do assistente, selecione a rede virtual em que os serviços de domínio do Azure AD geridos domínio é implementada. Escolha outra sub-rede do seu domínio gerido está implementado para. Para as outras definições, mantenha as predefinições e clique em **OK**.

    ![Selecione a rede virtual para a máquina virtual](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [!TIP]
    > **Escolha a rede virtual correta e a sub-rede.**
    > Selecione ou a rede virtual em que o seu domínio gerido for implementado ou uma rede virtual que está ligada a ela com o virtual peering de rede. Se selecionar uma rede virtual desligada, não é possível associar a máquina virtual para o domínio gerido.
    > Recomendamos que implementar o seu domínio gerido a uma sub-rede dedicada. Por conseguinte, não escolha a sub-rede em que tiver ativado o seu domínio gerido.

7. No **Compra** página, reveja as definições e clique em **OK** para implementar a máquina virtual.
8. A implementação de VM está afixada ao dashboard do portal do Azure.

    ![Concluído](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
9. Depois de concluída a implementação, pode ver informações sobre a VM com o **descrição geral** página.


## <a name="step-2-connect-to-the-windows-server-virtual-machine-using-the-local-administrator-account"></a>Passo 2: Ligar à máquina virtual do Windows Server com a conta de administrador local
Agora, ligar para a máquina virtual do Windows Server criada recentemente, a respetiva associação ao domínio. Utilize as credenciais de administrador local que especificou ao criar a máquina virtual.

Execute os seguintes passos para ligar à máquina virtual.

1. Clique em de **Connect** botão no **descrição geral** página. Um ficheiro de protocolo de ambiente de trabalho remoto (RDP) é criado e transferido.

    ![Ligar à máquina virtual do Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
2. Para ligar à sua VM, abra o ficheiro RDP transferido. Se lhe for solicitado, clique em **Ligar**.
3. Na linha de início de sessão, introduza o **credenciais de administrador local**, que especificou ao criar a máquina virtual. Por exemplo, tiver utilizámos 'localhost\mahesh' neste exemplo.
4. Poderá receber um aviso de certificado durante o processo de início de sessão. Clique em Sim ou continuar a prosseguir com a ligação.

Neste momento, a deve ter sessão iniciada para a máquina virtual do Windows recém-criado utilizando as credenciais de administrador locais. O passo seguinte é para associar a máquina virtual ao domínio.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-aad-ds-managed-domain"></a>Passo 3: Associar a máquina virtual do Windows Server para o domínio gerido AAD DS
Execute os seguintes passos para associar a máquina virtual do Windows Server para o domínio gerido AAD DS.

1. Ligar ao servidor do Windows, conforme mostrado no passo 2. A partir do ecrã Iniciar, abra **Gestor de servidor**.
2. Clique em **servidor Local** no painel esquerdo da janela do Gestor de servidor.

    ![Inicie o Gestor de servidor numa máquina virtual](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)
3. Clique em **WORKGROUP** sob o **propriedades** secção. No **propriedades do sistema** página de propriedades, clique em **alteração** para aderir ao domínio.

    ![Página de propriedades do sistema](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)
4. Especifique o nome de domínio do seu domínio gerido dos serviços de domínio do Azure AD no **domínio** caixa de texto e clique em **OK**.

    ![Especifique o domínio a ser associada](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)
5. Lhe for pedido para introduzir as suas credenciais para aderir ao domínio. Certifique-se de que **especificar as credenciais para um utilizador que pertencem aos administradores de DC do AAD** grupo. Apenas os membros deste grupo têm privilégios para associar computadores ao domínio gerido.

    ![Especifique as credenciais de associação a um domínio](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)
6. Pode especificar credenciais em qualquer uma das seguintes formas:

   * **Formato UPN: (recomendado)** especifique o sufixo UPN para a conta de utilizador, conforme configurado no Azure AD. Neste exemplo, o sufixo UPN do utilizador 'Bernardo' é 'bob@domainservicespreview.onmicrosoft.com'.
   * **Formato de SAMAccountName:** pode especificar o nome de conta no formato SAMAccountName. Neste exemplo, o utilizador 'Bernardo' seria necessário introduza 'CONTOSO100\bob'.

     > [!TIP]
     > **Recomendamos que utilize o formato UPN para especificar as credenciais.**
     > Se o prefixo UPN do utilizador é demasiado longo (por exemplo, 'joehasareallylongname'), o SAMAccountName pode ser gerada automaticamente. Se vários utilizadores têm o mesmo prefixo UPN (por exemplo, "João') no seu inquilino do Azure AD, o respetivo formato SAMAccountName pode ser gerada automaticamente pelo serviço. Nestes casos, o formato UPN pode ser utilizado de forma fiável para iniciar sessão no domínio.
     >

7. Depois de associação a um domínio for bem sucedida, verá a seguinte mensagem de boas-vindas ao domínio. Reinicie a máquina virtual para a conclusão da operação de associação do domínio.

    ![Bem-vindo ao domínio](./media/active-directory-domain-services-admin-guide/join-domain-done.png)


## <a name="troubleshooting-domain-join"></a>Resolução de problemas de associação a um domínio
### <a name="connectivity-issues"></a>Problemas de conectividade
Se a máquina virtual não é possível localizar o domínio, consulte os seguintes passos de resolução de problemas:

* Certifique-se de que a máquina virtual está ligada à mesma rede virtual que ativou os serviços de domínio no. Caso contrário, a máquina virtual não conseguiu estabelecer ligação ao domínio e, por conseguinte, não é possível associar ao domínio.
* Certifique-se de que a máquina virtual está numa rede virtual que por sua vez esteja ligada à rede virtual em que tiver ativado os serviços de domínio.
* Tente enviar um ping ao domínio utilizando o nome de domínio do domínio gerido (por exemplo, ' ping contoso100.com'). Se não for possível para o fazer, tente enviar um ping os endereços IP para o domínio apresentado na página onde ativou os serviços de domínio do Azure AD (por exemplo, ' ping 10.0.0.4'). Se tiver a capacidade para enviar um ping o endereço IP, mas não o domínio, DNS pode estar incorretamente configurado. Verifique se os endereços IP do domínio estão configurados como servidores DNS para a rede virtual.
* Tente libertar a cache de resolução DNS na máquina virtual ('ipconfig /flushdns').

Se aceder à caixa de diálogo que pede-lhe credenciais aderir ao domínio, não tem problemas de conectividade.

### <a name="credentials-related-issues"></a>Problemas relacionados com as credenciais
Consulte os seguintes passos se tiver problemas com as credenciais e não conseguir aderir ao domínio.

* Tente utilizar o formato UPN para especificar as credenciais. Se existirem vários utilizadores com o mesmo prefixo UPN no seu inquilino, ou se o prefixo UPN é demasiado longo, SAMAccountName para a sua conta pode ser gerada automaticamente. Por conseguinte, o formato de SAMAccountName para a sua conta pode ser diferente das que se espera ou utiliza no seu domínio no local.
* Tente utilizar as credenciais de uma conta de utilizador que pertence ao grupo de 'AAD DC administradores'.
* Certifique-se de que [ativou a sincronização de palavras-passe](active-directory-ds-getting-started-password-sync.md), em conformidade com os passos descritos no Guia de Introdução.
* Certifique-se de que utiliza o UPN do utilizador conforme configurado no Azure AD (por exemplo, 'bob@domainservicespreview.onmicrosoft.com') para iniciar sessão.
* Certifique-se de que tem aguardaram suficientemente longa para a sincronização de palavra-passe concluir conforme especificado no guia de introdução.

## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de domínio do Azure AD - guia de introdução](active-directory-ds-getting-started.md)
* [Administrar um domínio gerido dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)
