---
title: "Serviços de domínio do Azure Active Directory: Implementar o Proxy de aplicações do Azure Active Directory | Microsoft Docs"
description: "Utilizar o Proxy de aplicações do Azure AD nos domínios geridos de serviços de domínio do Active Directory do Azure"
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
ms.date: 11/15/2017
ms.author: maheshu
ms.openlocfilehash: 157a10277f89643245746223f2cd1d73680ac700
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="deploy-azure-ad-application-proxy-on-an-azure-ad-domain-services-managed-domain"></a>Implementar o Proxy de aplicações do Azure AD num domínio gerido dos serviços de domínio do Azure AD
Proxy de aplicações do Azure Active Directory (AD) ajuda-o suporte a funcionários remotos através da publicação de aplicações no local para ser acedido através da internet. Com os serviços de domínio do Azure AD, pode aplicações legadas de comparação de precisão e shift agora em execução no local para serviços de infraestrutura do Azure. Em seguida, pode publicar estas aplicações através do Proxy da aplicação AD do Azure, para fornecer acesso remoto seguro aos utilizadores na sua organização.

Se estiver familiarizado com o Proxy de aplicações do Azure AD, saiba mais sobre esta funcionalidade com o seguinte artigo: [como fornecer acesso remoto seguro a aplicações no local](../active-directory/active-directory-application-proxy-get-started.md).


## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas apresentadas neste artigo, tem de:

1. Um **subscrição do Azure**.
2. Um **diretório do Azure AD** -está sincronizada com um diretório no local ou um diretório apenas na nuvem.
3. Um **licença do Azure AD Basic ou Premium** é necessária para utilizar o Proxy de aplicações do Azure AD.
4. **Serviços de domínio do Azure AD** tem de estar ativada para o diretório do Azure AD. Se ainda não o feito, siga todas as tarefas descritas no [guia de introdução](active-directory-ds-getting-started.md).

<br>

## <a name="task-1---enable-azure-ad-application-proxy-for-your-azure-ad-directory"></a>Tarefa 1 - Proxy de aplicações de ativar o Azure AD para o diretório do Azure AD
Execute os seguintes passos para ativar o Proxy de aplicações do Azure AD para o diretório do Azure AD.

1. Inicie sessão como administrador no [portal do Azure](http://portal.azure.com).

2. Clique em **do Azure Active Directory** para trazer a descrição geral de diretório. Clique em **aplicações empresariais**.

    ![Selecione o diretório do Azure AD](./media/app-proxy/app-proxy-enable-start.png)
3. Clique em **proxy de aplicações**. Se não tiver uma subscrição do Azure AD Basic ou do Azure AD Premium, verá uma opção para ativar uma versão de avaliação. Ativar/desativar **ativar Proxy de aplicações?** para **ativar** e clique em **guardar**.

    ![Ativar o Proxy da aplicação](./media/app-proxy/app-proxy-enable-proxy-blade.png)
4. Para transferir o conector, clique em de **conector** botão.

    ![Transferir o conector](./media/app-proxy/app-proxy-enabled-download-connector.png)
5. Na página de transferência, aceite o contrato de privacidade e termos de licenciamento e clique em de **transferir** botão.

    ![Confirme a transferência](./media/app-proxy/app-proxy-enabled-confirm-download.png)


## <a name="task-2---provision-domain-joined-windows-servers-to-deploy-the-azure-ad-application-proxy-connector"></a>Tarefa 2 - aprovisionar servidores associados a um domínio do Windows para implementar o conector do Proxy de aplicações do Azure AD
Terá de associados a um domínio Windows Server máquinas virtuais nos quais pode instalar o conector do Proxy de aplicações do Azure AD. Para algumas aplicações, pode optar por aprovisionar vários servidores em que o conector está instalado. Esta opção de implementação dá-lhe maior disponibilidade e ajuda a lidar com cargas mais pesadas de autenticação.

Aprovisione os servidores do conector na mesma rede virtual (ou uma rede virtual ligado/emparelhado), em que tiver ativado o seu domínio gerido dos serviços de domínio do Azure AD. Da mesma forma, os servidores que alojam aplicações que publicar através do Proxy de aplicações têm de ser instalados na mesma rede virtual do Azure.

Para aprovisionar servidores do conector, siga as tarefas descritas no artigo intitulado [associar uma máquina virtual do Windows a um domínio gerido](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-3---install-and-register-the-azure-ad-application-proxy-connector"></a>Tarefa 3 - instale e registe o conector de Proxy de aplicações do Azure AD
Anteriormente, pode aprovisionar uma máquina virtual do Windows e associados a um domínio gerido. Nesta tarefa, é possível instalar o conector do Proxy de aplicações do Azure AD nesta máquina virtual.

1. Copie o pacote de instalação do conector para a VM no qual instala o conector do Proxy de aplicações Web do Azure AD.

2. Executar **AADApplicationProxyConnectorInstaller.exe** na máquina virtual. Aceite os termos de licenciamento de software.

    ![Aceitar os termos de licenciamento para instalar](./media/app-proxy/app-proxy-install-connector-terms.png)
3. Durante a instalação, é solicitado que registe o conector com o Proxy da aplicação do seu diretório do Azure AD.
    * Forneça o **credenciais de administrador global do Azure AD**. O inquilino do administrador global pode ser diferente das suas credenciais do Microsoft Azure.
    * A conta de administrador utilizada para registar o conector tem de pertencer ao mesmo diretório onde ativou o serviço de Proxy de aplicações. Por exemplo, se o domínio inquilino for contoso.com, o administrador deve ser admin@contoso.com ou qualquer outro alias nesse domínio válido.
    * Se a configuração de segurança avançada do IE está ativada para o servidor onde está a instalar o conector, o ecrã de registo poderá ser bloqueado. Para permitir o acesso, siga as instruções na mensagem de erro. Certifique-se de que a Segurança Avançada do Internet Explorer está desativada.
    * Se o registo do conetor falhar, veja [Resolver Problemas da Proxy da Aplicação](../active-directory/active-directory-application-proxy-troubleshoot.md).

    ![Conector instalado](./media/app-proxy/app-proxy-connector-installed.png)
4. Para garantir que o conector funciona corretamente, execute o Troubleshooter de conector do Proxy de aplicações do Azure AD. Deverá ver um relatório com êxito após a execução de troubleshooter.

    ![Êxito Troubleshooter](./media/app-proxy/app-proxy-connector-troubleshooter.png)
5. Deverá ver o conector recém-instalado listado na página de proxy de aplicação no diretório do Azure AD.

    ![](./media/app-proxy/app-proxy-connector-page.png)

> [!NOTE]
> Pode optar por instalar os conectores em vários servidores para garantir a elevada disponibilidade para autenticar as aplicações publicadas através do Proxy de aplicações do Azure AD. Execute os mesmos passos indicados acima para instalar o conector noutros servidores associados ao seu domínio gerido.
>
>

## <a name="next-steps"></a>Passos Seguintes
Tem de configurar o Proxy de aplicações do Azure AD e integrado-lo no seu domínio gerido dos serviços de domínio do Azure AD.

* **Migrar as suas aplicações para máquinas virtuais do Azure:** pode comparação de precisão-e-shift as suas aplicações de servidores no local para máquinas virtuais do Azure associadas ao seu domínio gerido. Se o fizer, ajuda-o a remover os custos de infraestrutura da execução de servidores no local.

* **Publicar aplicações através do Proxy de aplicações do Azure AD:** publicar aplicações em execução nas suas máquinas virtuais do Azure utilizando o Proxy de aplicações do Azure AD. Para obter mais informações, consulte [publicar aplicações através do Proxy de aplicações do Azure AD](../active-directory/application-proxy-publish-azure-portal.md)


## <a name="deployment-note---publish-iwa-integrated-windows-authentication-applications-using-azure-ad-application-proxy"></a>Nota de implementação - aplicações de publicar IWA (autenticação integrada do Windows) com o Proxy de aplicações do Azure AD
Ative início de sessão às suas aplicações utilizando a autenticação integrada de Windows (IWA) ao conceder permissão de conectores do Proxy de aplicação para representar os utilizadores e enviar e receber tokens em nome daqueles. Configure a delegação restringida de Kerberos (KCD) para o conector conceder as permissões necessárias para aceder a recursos no domínio gerido. Utilize o mecanismo KCD baseada em recursos em domínios geridos para maior segurança.


### <a name="enable-resource-based-kerberos-constrained-delegation-for-the-azure-ad-application-proxy-connector"></a>Ativar baseada em recursos a delegação restringida de Kerberos para o conector do Proxy de aplicações do Azure AD
O conector do Proxy de aplicações do Azure deve ser configurado para delegação restringida de Kerberos (KCD), pelo que pode representar os utilizadores no domínio gerido. Num domínio gerido dos serviços de domínio do Azure AD, não tiver privilégios de administrador de domínio. Por conseguinte, **tradicional KCD de nível de conta não é possível configurar um domínio gerido**.

Utilize KCD baseada em recursos, conforme descrito neste [artigo](active-directory-ds-enable-kcd.md).

> [!NOTE]
> Tem de ser membro do grupo 'AAD DC administradores', para administrar o domínio gerido utilizando cmdlets do PowerShell do AD.
>
>

Utilize o cmdlet do PowerShell Get-ADComputer para obter as definições para o computador no qual está instalado o conector do Proxy de aplicações do Azure AD.
```powershell
$ConnectorComputerAccount = Get-ADComputer -Identity contoso100-proxy.contoso100.com
```

Depois disso, utilize o cmdlet Set-ADComputer configurar KCD baseada em recursos para o servidor de recurso.
```powershell
Set-ADComputer contoso100-resource.contoso100.com -PrincipalsAllowedToDelegateToAccount $ConnectorComputerAccount
```

Se tiver implementado vários conectores de Proxy de aplicações no seu domínio gerido, terá de configurar KCD baseada em recursos para cada instância esse conector.


## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de domínio do Azure AD - guia de introdução](active-directory-ds-getting-started.md)
* [Configurar a delegação restrita de Kerberos num domínio gerido](active-directory-ds-enable-kcd.md)
* [Descrição geral da delegação restrita de Kerberos](https://technet.microsoft.com/library/jj553400.aspx)
