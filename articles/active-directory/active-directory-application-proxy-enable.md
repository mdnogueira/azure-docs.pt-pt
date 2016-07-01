<properties
    pageTitle="Ativar o Proxy da Aplicação do Azure AD | Microsoft Azure"
    description="Ativar o Proxy da Aplicação no Portal Clássico do Azure e instalar os Conectores para o proxy reverso."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="StevenPo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/01/2016"
    ms.author="kgremban"/>

# Ativar o Proxy da Aplicação no Portal do Azure

Este artigo explica como ativar o Proxy da Aplicação do Microsoft Azure AD para o diretório em nuvem no Azure AD. Isto envolve a instalação do Conector do Proxy da Aplicação na sua rede privada, que mantém a ligação da sua rede com o serviço de proxy. Também deverá registar o Conector com a sua subscrição de inquilino do Microsoft Azure AD. Se não estiver familiarizado com o que o Proxy da Aplicação fazer por si, saiba mais sobre [Como fornecer acesso remoto seguro a aplicações no local](active-directory-application-proxy-get-started.md).

Quando tiver concluído estas instruções para ativar o Proxy da Aplicação do Azure AD, estará pronto para publicar as suas aplicações no local para acesso remoto.

> [AZURE.NOTE] O Proxy da Aplicação é uma funcionalidade que só estar disponível se tiver efetuado a atualização para a edição Premium ou Basic do Azure Active Directory. Para obter mais informações, consulte [Edições do Azure Active Directory](active-directory-editions.md).

## Pré-requisitos do Proxy da Aplicação
Para poder ativar e utilizar os serviços do Proxy da Aplicação, terá de ter:

- Uma [subscrição Basic ou Premium do Microsoft Azure AD](active-directory-editions.md) e um diretório do Azure AD para o qual terá de ser um administrador global.
- Um servidor a executar o Windows Server 2012 R2 ou o Windows 8.1 ou superior, em que poderá instalar o Conector do Proxy da Aplicação. O servidor enviará pedidos HTTPS aos serviços do Proxy da Aplicação na nuvem, e necessita de uma ligação HTTPS para as aplicações que pretende publicar.
- Se estiver colocada uma firewall no caminho, certifique-se de que a firewall está aberta, para que o Conector possa fazer pedidos HTTPS (TCP) ao Proxy da Aplicação. O Conector utiliza estas portas, juntamente com os subdomínios que fazem parte dos domínios de alto nível: *msappproxy.net* e *servicebus.windows.net*. Certifique-se de abrir **todas** as portas seguintes para tráfego de **saída**:

  	| Número de porta | Descrição |
  	| --- | --- |
  	| 80 | Para ativar o tráfego de saída HTTP para a validação de segurança. |
  	| 443 | Para ativar a autenticação de utilizador no Azure AD (apenas necessário para o processo de registo do Conector) |
  	| 10100 – 10120 | Para ativar as respostas LOB HTTP enviadas ao proxy |
  	| 9352, 5671 | Para ativar a comunicação entre o Conector para o serviço do Azure para pedidos recebidos. |
  	| 9350 | Opcional, para ativar um melhor desempenho para pedidos recebidos |
  	| 8080 | Para ativar a sequência de arranque do Conector e a atualização automática do Conector |
  	| 9090 | Para ativar o registo do Conector (apenas necessário para o processo de registo do Conector) |
  	| 9091 | Para ativar a renovação automática do certificado de confiança do Conector |

Se a firewall impuser tráfego de acordo com os utilizadores de origem, abra estas portas para o tráfego dos serviços do Windows que são executados com um Serviço de Rede. Além disso, certifique-se de ativar a porta 8080 para NT Authority\System.


## Passo 1: Ativar o Proxy da Aplicação no Azure AD
1. Inicie sessão como administrador no [Portal Clássico do Azure](https://manage.windowsazure.com/).
2. Aceda ao Active Directory e selecione o diretório no qual pretende ativar o Proxy da Aplicação.

    ![Active Directory – ícone](./media/active-directory-application-proxy-enable/ad_icon.png)

3. Selecione **Configurar** na página do diretório e desloque o ecrã para baixo até **Proxy da Aplicação**.
4. Mude **Ativar Serviços do Proxy da Aplicação deste Diretório** para **Ativado**.

    ![Ativar o Proxy da Aplicação](./media/active-directory-application-proxy-enable/app_proxy_enable.png)

5. Selecione **Transferir agora**. Será apresentada a página **Transferir Conector do Proxy da Aplicação do Azure AD**. Leia e aceite os termos de licenciamento e clique em **Transferir** para guardar o ficheiro (.exe) do Windows Installer do Conector do Proxy da Aplicação.

## Passo 2: Instalar e registar o Conector
1. Execute *AADApplicationProxyConnectorInstaller.exe* no servidor que preparou de acordo com os pré-requisitos acima.
2. Siga as instruções do assistente de instalação.
3. Durante a instalação, ser-lhe-á solicitado que registe o Conector com o Proxy da Aplicação do inquilino do Azure AD.

  - Indique as suas credenciais de administrador global do Azure AD. O inquilino do administrador global pode ser diferente das suas credenciais do Microsoft Azure.
  - Certifique-se de que o administrador que regista o Conector está no mesmo diretório em que ativou o serviço do Proxy da Aplicação. Por exemplo, se o domínio inquilino for contoso.com, o administrador deve ser admin@contoso.com ou qualquer outro alias nesse domínio.
  - Se **Configuração de Segurança Avançada do IE** estiver definida para **Ativada** no servidor onde está a instalar o Conector do Azure AD, o ecrã de registo poderá ser bloqueado. Se isto acontecer, siga as instruções na mensagem de erro para permitir o acesso. Certifique-se de que a Segurança Avançada do Internet Explorer está desativada.
  - Se o registo do Conector falhar, consulte [Resolver problemas do Proxy da Aplicação](active-directory-application-proxy-troubleshoot.md).  

4. Quando a instalação estiver concluída, serão adicionados dois novos serviços ao servidor, conforme mostrado abaixo.

    - O **Conector do Proxy da Aplicação do Microsoft AAD** ativa a conectividade
    - O **Atualizador do Conector do Proxy da Aplicação do Microsoft AAD** é um serviço de atualização automática, que verifica periodicamente a existência de novas versões do Conector e atualiza o Conector conforme necessário.

    ![Serviços do Conector do Proxy da Aplicação – captura de ecrã](./media/active-directory-application-proxy-enable/app_proxy_services.png)

5. Clique em **Concluir** na janela de instalação para concluir a instalação.

Está agora pronto para [Publicar aplicações com o Proxy da Aplicação](active-directory-application-proxy-publish.md).

Para fins de elevada disponibilidade, deve implementar pelo menos um Conector adicional. Para implementar mais Conectores, repita os passos 2 e 3 acima. Cada Conector tem de ser registado separadamente.

Se pretender desinstalar o Conector, desinstale ambos os serviços do Conector e do Atualizador e, em seguida, certifique-se de reiniciar o computador para remover completamente o serviço.


## Passos seguintes

- [Publicar aplicações com o Proxy da Aplicação](active-directory-application-proxy-publish.md)
- [Publicar aplicações com o seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)
- [Ativar o início de sessão único](active-directory-application-proxy-sso-using-kcd.md)
- [Resolver problemas com o Proxy da Aplicação](active-directory-application-proxy-troubleshoot.md)

Para obter as notícias e atualizações mais recentes, consulte o [blogue do Proxy da Aplicação](http://blogs.technet.com/b/applicationproxyblog/)



<!--HONumber=Jun16_HO2-->


