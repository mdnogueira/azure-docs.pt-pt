---
title: Ativar o Proxy da Aplicação do Azure AD | Microsoft Docs
description: Ativar o Proxy da Aplicação no Portal Clássico do Azure e instalar os Conectores para o proxy reverso.
services: active-directory
documentationcenter: ''
author: kgremban
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/19/2016
ms.author: kgremban

---
# Ativar o Proxy da Aplicação no Portal do Azure
Este artigo explica como ativar a Proxy da Aplicação do Microsoft Azure AD para o diretório em nuvem no Azure AD.

Se não estiver familiarizado com o que o Proxy da Aplicação fazer por si, saiba mais sobre [Como fornecer acesso remoto seguro a aplicações no local](active-directory-application-proxy-get-started.md).

## Pré-requisitos do Proxy da Aplicação
Para poder ativar e utilizar os serviços do Proxy da Aplicação, terá de ter:

* Uma [subscrição Basic ou Premium do Microsoft Azure AD](active-directory-editions.md) e um diretório do Azure AD para o qual terá de ser um administrador global.
* Um servidor a executar o Windows Server 2012 R2 ou o Windows 8.1 ou superior, em que poderá instalar o Conector do Proxy da Aplicação. O servidor envia pedidos para os serviços da Proxy da Aplicação na nuvem, e necessita de uma ligação HTTP ou HTTPS para as aplicações que está a publicar.
  
  * Para obter o início de sessão único nas aplicações publicadas, esta máquina deverá ser associada ao mesmo domínio do AD como as aplicações que está a publicar.
* Se estiver colocada uma firewall no caminho, certifique-se de que está aberta, para que o Conector possa fazer pedidos HTTPS (TCP) para a Proxy da Aplicação. O Conector utiliza estas portas, em conjunto com os subdomínios que fazem parte dos domínios de alto nível msappproxy.net e servicebus.windows.net. Certifique-se de que abre as seguintes portas seguintes para tráfego de **saída**:
  
      | Número de porta | Descrição |
      | --- | --- |
      | 80 | Ativar o tráfego de saída HTTP para a validação de segurança. |
      | 443 | Ativar a autenticação de utilizador no Azure AD (apenas necessário para o processo de registo do Conetor) |
      | 10100–10120 | Ativar as respostas LOB HTTP enviadas para a proxy |
      | 9352, 5671 | Ativar a comunicação entre o Conetor para o serviço do Azure para pedidos recebidos. |
      | 9350 | Opcional, para ativar um melhor desempenho para pedidos recebidos |
      | 8080 | Ativar a sequência de arranque do Conetor e a atualização automática do Conetor |
      | 9090 | Ativar o registo do Conetor (apenas necessário para o processo de registo do Conetor) |
      | 9091 | Ativar a renovação automática do certificado de confiança do Conetor |
  
    Se a firewall impuser tráfego de acordo com os utilizadores de origem, abra estas portas para o tráfego dos serviços do Windows que são executados com um Serviço de Rede. Além disso, certifique-se de ativar a porta 8080 para NT Authority\System.
* Se a organização utilizar servidores proxy para estabelecer ligação à Internet, veja a publicação no blogue [Trabalhar com servidores proxy no local existentes](https://blogs.technet.microsoft.com/applicationproxyblog/2016/03/07/working-with-existing-on-prem-proxy-servers-configuration-considerations-for-your-connectors/) para obter detalhes sobre como configurá-los.

## Passo 1: Ativar o Proxy da Aplicação no Azure AD
1. Inicie sessão como administrador no [Portal Clássico do Azure](https://manage.windowsazure.com/).
2. Aceda ao Active Directory e selecione o diretório no qual pretende ativar o Proxy da Aplicação.
   
    ![Active Directory – ícone](./media/active-directory-application-proxy-enable/ad_icon.png)
3. Selecione **Configurar** na página do diretório e desloque o ecrã para baixo até **Proxy da Aplicação**.
4. Mude **Ativar Serviços do Proxy da Aplicação deste Diretório** para **Ativado**.
   
    ![Ativar o Proxy da Aplicação](./media/active-directory-application-proxy-enable/app_proxy_enable.png)
5. Selecione **Transferir agora**. Será apresentada a página **Transferir Conetor da Proxy da Aplicação do Azure AD**. Leia e aceite os termos de licenciamento e clique em **Transferir** para guardar o ficheiro (.exe) do Windows Installer do conetor.

## Passo 2: Instalar e registar o Conector
1. Execute **AADApplicationProxyConnectorInstaller.exe** no servidor que preparou de acordo com os pré-requisitos.
2. Siga as instruções do assistente de instalação.
3. Durante a instalação, ser-lhe-á solicitado que registe o conetor com a Proxy da Aplicação do inquilino do Azure AD.
   
   * Indique as suas credenciais de administrador global do Azure AD. O inquilino do administrador global pode ser diferente das suas credenciais do Microsoft Azure.
   * Certifique-se o administrador que regista o conetor está no mesmo diretório onde ativou o serviço da Proxy da Aplicação. Por exemplo, se o domínio de inquilino for contoso.com, o administrador deve ser admin@contoso.com ou qualquer outro alias nesse domínio.
   * Se **Configuração de Segurança Avançada do IE** estiver definida como **Ativada** no servidor onde está a instalar o conetor, o ecrã de registo poderá ser bloqueado. Siga as instruções na mensagem de erro para permitir o acesso. Certifique-se de que a Segurança Avançada do Internet Explorer está desativada.
   * Se o registo do conetor falhar, veja [Resolver Problemas da Proxy da Aplicação](active-directory-application-proxy-troubleshoot.md).  
4. Quando a instalação estiver concluída, serão adicionados dois novos serviços ao servidor:
   
   * O **Conector do Proxy da Aplicação do Microsoft AAD** ativa a conectividade
   * O **Atualizador do Conetor da Proxy da Aplicação do Microsoft AAD** é um serviço de atualização automática, que verifica periodicamente a existência de novas versões do conetor e atualiza-o conforme necessário.
     
     ![Serviços do Conector do Proxy da Aplicação – captura de ecrã](./media/active-directory-application-proxy-enable/app_proxy_services.png)
5. Clique em **Concluir** na janela de instalação.

Para fins de elevada disponibilidade, deve implementar pelo menos dois conetores. Para implementar mais conetores, repita os passos 2 e 3 acima. Cada conetor tem de ser registado separadamente.

Se pretender desinstalar o Conetor, desinstale o serviço Conetor e o serviço Atualizador. Reinicie o computador para remover completamente o serviço.

## Passos seguintes
Está agora pronto para [Publicar aplicações com o Proxy da Aplicação](active-directory-application-proxy-publish.md).

Se tiver aplicações que estão em redes separadas ou em diferentes localizações, pode utilizar grupos de conetor para organizar os diferentes conetores em unidades lógicas. Saiba mais sobre [Trabalhar com conetores da Proxy da Aplicação](active-directory-application-proxy-connectors.md).

<!--HONumber=Sep16_HO3-->


