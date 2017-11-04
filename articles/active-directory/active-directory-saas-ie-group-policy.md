---
title: "Implementar a extensão de painel de acesso do Azure para o IE com um GPO | Microsoft Docs"
description: "Como utilizar a política de grupo para implementar o suplemento do Internet Explorer para o portal de aplicações My."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 7c2d49c8-5be0-4e7e-abac-332f9dfda736
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 06e55977eb2840c8325d70a9fdfd95023bbf380d
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Como implementar a extensão do painel de acesso para o Internet Explorer utilizando a política de grupo
Este tutorial mostra como utilizar a política de grupo para instalar remotamente a extensão do painel de acesso para o Internet Explorer em máquinas dos seus utilizadores. Esta extensão é necessária para os utilizadores de Internet Explorer que tem de iniciar sessão em aplicações que são configuradas utilizando [baseada em palavra-passe de início de sessão](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

Recomenda-se que os administradores de automatizar a implementação desta extensão. Caso contrário, os utilizadores têm de transferir e instalar a extensão, que é suscetível a erros de utilizador e necessita de permissões de administrador. Este tutorial abrange um método de automatização de implementações de software utilizando a política de grupo. [Saiba mais sobre a política de grupo.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

Também está disponível para a extensão do painel de acesso [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) e [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), nenhum dos quais requer permissões de administrador para instalar.

## <a name="prerequisites"></a>Pré-requisitos
* Configurou [serviços de domínio do Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), e associar máquinas dos utilizadores ao seu domínio.
* Tem de ter a permissão "Definições de editar" para editar o objeto de política de grupo (GPO). Por predefinição, os membros dos grupos de segurança seguinte têm esta permissão: os administradores do domínio, administradores da empresa e proprietários de criador de política de grupo. [Saiba mais.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>Passo 1: Criar o ponto de distribuição
Em primeiro lugar, tem de colocar o pacote de instalador numa localização de rede que pode ser acedida pelas máquinas que pretende instalar remotamente a extensão no. Para tal, siga estes passos:

1. Inicie sessão servidor como administrador
2. No **Gestor de servidor** janela, aceda a **ficheiros e serviços de armazenamento**.
   
    ![Serviços de armazenamento e de ficheiros abertos](./media/active-directory-saas-ie-group-policy/files-services.png)
3. Vá para o **partilhas** separador. Em seguida, clique em **tarefas** > **nova partilha...**
   
    ![Serviços de armazenamento e de ficheiros abertos](./media/active-directory-saas-ie-group-policy/shares.png)
4. Concluir o **Assistente de nova partilha** e definir as permissões para se certificar de que pode ser acedido a partir de computadores dos seus utilizadores. [Saiba mais sobre as partilhas.](https://technet.microsoft.com/library/cc753175.aspx)
5. Transferir o seguinte pacote do Microsoft Windows Installer (ficheiro. msi): [Extension.msi do painel de acesso](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access Panel Extension.msi)
6. Copie o pacote de instalador para uma localização pretendida na partilha.
   
    ![Copie o ficheiro. msi para a partilha.](./media/active-directory-saas-ie-group-policy/copy-package.png)
7. Certifique-se de que as máquinas de cliente são capazes de aceder ao pacote de instalador da partilha. 

## <a name="step-2-create-the-group-policy-object"></a>Passo 2: Criar o objeto de política de grupo
1. Inicie sessão no servidor que aloja a instalação de serviços de domínio do Active Directory (AD DS).
2. No Gestor de servidor, aceda a **ferramentas** > **gestão de políticas de grupo**.
   
    ![Aceda a ferramentas > Gestão de política de grupo](./media/active-directory-saas-ie-group-policy/tools-gpm.png)
3. No painel esquerdo do **gestão de políticas de grupo** janela, visualizar a hierarquia de unidade organizacional (UO) e determinar quais âmbito que gostaria de aplicar a política de grupo. Por exemplo, poderá optar por escolher uma UO pequena para implementar a alguns utilizadores de teste, ou pode escolher uma UO de nível superior para implementar em toda a organização.
   
   > [!NOTE]
   > Se gostaria de criar ou editar a unidades organizacionais (UOs), mude novamente para o Gestor de servidor e aceda a **ferramentas** > **computadores e utilizadores do Active Directory**.
   > 
   > 
4. Assim que tiver selecionado uma UO, faça duplo clique nele e selecione **criar um GPO neste domínio e ligá-lo aqui...**
   
    ![Criar um novo GPO](./media/active-directory-saas-ie-group-policy/create-gpo.png)
5. No **novo GPO** linha de comandos, escreva um nome para o novo objeto de política de grupo.
   
    ![Nome do novo GPO](./media/active-directory-saas-ie-group-policy/name-gpo.png)
6. Clique com o botão direito do objeto de política de grupo que criou e selecione **editar**.
   
    ![Editar o novo GPO](./media/active-directory-saas-ie-group-policy/edit-gpo.png)

## <a name="step-3-assign-the-installation-package"></a>Passo 3: Atribuir o pacote de instalação
1. Determinar se gostaria de implementar a extensão com base no **configuração do computador** ou **configuração do utilizador**. Quando utilizar [configuração do computador](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx), a extensão está instalada no computador, independentemente da que os utilizadores iniciem sessão-lo. Com [configuração do utilizador](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx), os utilizadores têm a extensão instalada para os mesmos, independentemente dos computadores que iniciam sessão no.
2. No painel esquerdo do **Editor de gestão de políticas de grupo** janela, aceda a qualquer um dos seguintes caminhos de pastas, dependendo do tipo de configuração que escolheu:
   
   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`
3. Clique com botão direito **instalação de Software**, em seguida, selecione **novo** > **pacote...**
   
    ![Criar um novo pacote de instalação de software](./media/active-directory-saas-ie-group-policy/new-package.png)
4. Aceda à pasta partilhada que contém o pacote de instalador de [passo 1: criar o ponto de distribuição](#step-1-create-the-distribution-point), selecione o ficheiro. msi e clique em **abra**.
   
   > [!IMPORTANT]
   > Se a partilha estiver localizada no mesmo neste servidor, certifique-se de que estão a aceder a. msi através de caminho de ficheiro de rede, em vez do caminho de ficheiro local.
   > 
   > 
   
    ![Selecione o pacote de instalação na pasta partilhada.](./media/active-directory-saas-ie-group-policy/select-package.png)
5. No **Implementar Software** linha de comandos, selecione **atribuído** para o método de implementação. Em seguida, clique em **OK**.
   
    ![Selecione atribuído e, em seguida, clique em OK.](./media/active-directory-saas-ie-group-policy/deployment-method.png)

A extensão é agora implementada para a UO que selecionou. [Saiba mais sobre a instalação de Software de política de grupo.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Passo 4: Auto-ativar a extensão para o Internet Explorer
Além de executar o instalador, cada extensão para o Internet Explorer deve ser explicitamente ativada antes de poder ser utilizada. Siga os passos abaixo para ativar a extensão do painel de acesso através da política de grupo:

1. No **Editor de gestão de políticas de grupo** janela, aceda a qualquer um dos seguintes caminhos, dependendo do tipo de configuração que selecionou no [passo 3: atribuir o pacote de instalação](#step-3-assign-the-installation-package):
   
   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
2. Clique com botão direito **suplemento lista**e selecione **editar**.
    ![Edite lista de suplemento.](./media/active-directory-saas-ie-group-policy/edit-add-on-list.png)
3. No **suplemento lista** janela, selecione **ativado**. Em seguida, sob o **opções** secção, clique em **mostrar...** .
   
    ![Clique em ativar, em seguida, clique em Mostrar...](./media/active-directory-saas-ie-group-policy/edit-add-on-list-window.png)
4. No **Mostrar conteúdo** janela, execute os seguintes passos:
   
   1. Para a primeira coluna (o **nome do valor** campo), copie e cole o seguinte ID de classe:`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   2. Para a segunda coluna (o **valor** campo), escreva o seguinte valor:`1`
   3. Clique em **OK** para fechar o **Mostrar conteúdo** janela.
      
      ![Preencha os valores especificados acima.](./media/active-directory-saas-ie-group-policy/show-contents.png)
5. Clique em **OK** para aplicar as alterações e fechar o **suplemento lista** janela.

A extensão agora deve ser ativada para as máquinas na UO selecionada. [Saiba mais sobre como utilizar a política de grupo para ativar ou desativar os suplementos do Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>Passo 5 (opcional): desativar a linha de comandos "Memorizar palavra-passe"
Quando os utilizadores início de sessão em Web sites utilizando a extensão do painel de acesso, o Internet Explorer pode mostrar a linha seguinte perguntar "Que pretende armazenar a palavra-passe?"

![](./media/active-directory-saas-ie-group-policy/remember-password-prompt.png)

Se pretender impedir que os utilizadores a ver esta linha de comandos, em seguida, siga os passos abaixo para impedir a conclusão automática de recordar palavras-passe:

1. No **Editor de gestão de políticas de grupo** janela, aceda ao caminho listado abaixo. Esta definição de configuração só está disponível em **configuração do utilizador**.
   
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
2. Localizar a definição denominada **ative a funcionalidade de conclusão automática de nomes de utilizador e palavras-passe em formulários**.
   
   > [!NOTE]
   > Versões anteriores do Active Directory podem listar esta definição com o nome **não permitir a conclusão automática para guardar as palavras-passe**. A configuração para essa definição é diferente da definição descrita neste tutorial.
   > 
   > 
   
    ![Lembre-se procurar este em definições do utilizador.](./media/active-directory-saas-ie-group-policy/disable-auto-complete.png)
3. Clique com o botão direito do rato em definição acima e selecione **editar**.
4. Na janela intitulada **ative a funcionalidade de conclusão automática de nomes de utilizador e palavras-passe em formulários**, selecione **desativado**.
   
    ![Selecione desativar](./media/active-directory-saas-ie-group-policy/disable-passwords.png)
5. Clique em **OK** para aplicar estas alterações e fechar a janela.

Os utilizadores já não será possível armazenar as respetivas credenciais ou utilize a conclusão automática para aceder às credenciais anteriormente armazenadas. No entanto, esta política permitem aos utilizadores continuar a utilizar a conclusão automática de mensagens em fila para outros tipos de campos de formulário, tais como campos de pesquisa.

> [!WARNING]
> Se esta política é ativada depois dos utilizadores tem optado por algumas credenciais, esta política será *não* limpar as credenciais que já tenham sido armazenadas.
> 
> 

## <a name="step-6-testing-the-deployment"></a>Passo 6: Testar a implementação
Siga os passos abaixo para verificar se a implementação de extensão foi concluída com êxito:

1. Se implementou com **configuração do computador**, inicie sessão num computador cliente que pertence à UO selecionada no [passo 2: criar o objeto de política de grupo](#step-2-create-the-group-policy-object). Se implementou com **configuração do utilizador**, certifique-se iniciar sessão como um utilizador que pertença a essa UO.
2. Poderá demorar alguns sessão ins para a política de grupo totalmente é alterado para atualizar com esta máquina. Para forçar a atualização, abra uma **linha de comandos** janela e execute o seguinte comando:`gpupdate /force`
3. Tem de reiniciar a máquina efetuar a instalação. Lido pode demorar significativamente mais tempo do que instala habitual enquanto a extensão.
4. Depois de reiniciar, abra **Internet Explorer**. No canto superior direito da janela, clique em **ferramentas** (ícone engrenagem) e, em seguida, selecione **gerir suplementos**.
   
    ![Aceda a ferramentas > Gerir suplementos](./media/active-directory-saas-ie-group-policy/manage-add-ons.png)
5. No **gerir suplementos** janela, certifique-se de que o **extensão do painel de acesso** foi instalado e de que o **estado** foi definido para **ativado**.
   
    ![Certifique-se de que a extensão do painel de acesso está instalada e ativada.](./media/active-directory-saas-ie-group-policy/verify-install.png)

## <a name="related-articles"></a>Artigos relacionados
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)
* [Acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [A extensão do painel de acesso de resolução de problemas para o Internet Explorer](active-directory-saas-ie-troubleshooting.md)

