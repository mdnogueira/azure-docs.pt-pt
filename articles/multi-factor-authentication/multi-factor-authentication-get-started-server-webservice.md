---
title: "Serviço Web da Aplicação Móvel do Servidor MFA do Azure | Microsoft Docs"
description: "A aplicação Microsoft Authenticator oferece uma opção de autenticação fora de banda adicional.  Permite ao servidor MFA utilizar notificações push para os utilizadores."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 6c8d6fcc-70f4-4da4-9610-c76d66635b8b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: joflore
ms.reviewer: alexwe
ms.custom: it-pro
ms.openlocfilehash: bf758d1241f2a56eba4d5c92ace713d6e563df65
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Ativar a autenticação de aplicação móvel com o Servidor Multi-Factor Authentication do Azure

A aplicação Microsoft Authenticator oferece uma opção de verificação fora de banda adicional. Em vez de fazer uma chamada telefónica automática ou enviar um SMS ao utilizador durante o início de sessão, o Multi-Factor Authentication do Azure envia uma notificação push para a aplicação Microsoft Authenticator no smartphone ou tablet do utilizador. O utilizador toca simplesmente em **Verificar** (ou introduz um PIN e toca em "Autenticar") na aplicação para concluir o início de sessão.

É preferível utilizar uma aplicação móvel para a verificação de dois passos quando a receção de telefone é pouco fiável. Se utilizar a aplicação como um gerador de token OATH, não é precisa qualquer ligação de rede ou à Internet.

Dependendo do seu ambiente, poderá implementar o serviço Web de aplicação móvel no mesmo servidor que o Servidor Multi-Factor Authentication ou noutro servidor com acesso à Internet.

## <a name="requirements"></a>Requisitos

Para utilizar a aplicação Microsoft Authenticator, é necessário o seguinte para que a aplicação consiga comunicar com o Serviço Web da Aplicação Móvel com êxito:

* Servidor Multi-Factor Authentication do Azure v6.0 ou superior
* Instalar o Serviço Web da Aplicação Móvel num servidor Web com acesso à Internet com [Serviços de Informação Internet (IIS) IIS 7.x ou superior](http://www.iis.net/) da Microsoft®
* O ASP.NET v4.0.30319 estar instalado, registado e definido como Permitido
* Os serviços de função necessários incluem o ASP.NET e a Compatibilidade com Metabase do IIS 6
* O Serviço Web da Aplicação Móvel estar acessível através de um URL público
* O Serviço Web da Aplicação Móvel estar protegido por um certificado SSL.
* Instalar o SDK do Serviço Web do Multi-Factor Authentication do Azure no IIS 7.x ou superior no **mesmo servidor no qual o Servidor Multi-Factor Authentication do Azure está instalado**
* O SDK do Serviço Web do Multi-Factor Authentication do Azure estar protegido por um certificado SSL.
* O Serviço Web da Aplicação Móvel conseguir ligar ao SDK do Serviço Web do Multi-Factor Authentication do Azure através de SSL
* O Serviço Web da Aplicação Móvel conseguir autenticar-se no SDK do Serviço Web do MFA do Azure com as credenciais de uma conta de serviço que seja membro do grupo de segurança "PhoneFactor Admins". Esta conta de serviço e o grupo existem no Active Directory se o Servidor Multi-Factor Authentication do Azure estiver num servidor associado a um domínio. Esta conta de serviço e o grupo existem localmente no Servidor Multi-Factor Authentication do Azure se não estiver associado a um domínio.

## <a name="install-the-mobile-app-web-service"></a>Instalar o serviço Web da aplicação móvel

Antes de instalar o serviço Web da aplicação móvel, tenha em atenção os seguintes detalhes:

* Precisa de uma Conta de Serviço que faz parte do Grupo "PhoneFactor Admins". Esta conta pode ser a mesma que utilizou para a instalação do Portal de Utilizador.
* Facilita se abrir um browser no servidor Web com acesso à Internet e navegar para o URL do SDK do Serviço Web que foi introduzido no ficheiro web.config. Se o browser conseguir aceder ao serviço Web com êxito, este deve solicitar-lhe as credenciais. Introduza o nome de utilizador e a palavra-passe que foram introduzidos no ficheiro web.config, tal como é apresentado no ficheiro. Certifique-se de que não são apresentados erros ou avisos de certificado.
* Se uma firewall ou um proxy inverso estiver junto do servidor Web do Serviço Web da Aplicação Móvel e a fazer o descarregamento de SSL, pode editar o ficheiro web.config do Serviço Web da Aplicação Móvel para que o Serviço Web da Aplicação Móvel possa utilizar http em vez de https. Ainda é necessário utilizar o SSL a partir da Aplicação Móvel para a firewall/proxy inverso. Adicione a chave seguinte à secção \<appSettings\>:

        <add key="SSL_REQUIRED" value="false"/>

### <a name="install-the-web-service-sdk"></a>Instalar o SDK do serviço Web

Em ambos os cenários, se o SDK do Serviço Web do Multi-Factor Authentication do Azure ainda **não** estiver instalado no Servidor Multi-Factor Authentication (MFA) do Azure, conclua os passos que se seguem.

1. Abra a consola do Servidor Multi-Factor Authentication.
2. Aceda ao **SDK do Serviço Web** e selecione **Instalar SDK do Serviço Web**.
3. Conclua a instalação com as predefinições, a menos que tenha de as alterar por algum motivo.
4. Vincule um Certificado SSL ao site no IIS.

Se tiver dúvidas sobre como configurar um certificado SSL num servidor IIS, veja o artigo [How to Set Up SSL on IIS](https://docs.microsoft.com/en-us/iis/manage/configuring-security/how-to-set-up-ssl-on-iis) (Como realizar uma Cópia de Segurança do SSL no IIS).

O SDK do Serviço Web tem de estar protegido por um certificado SSL. Pode utilizar um certificado autoassinado para este fim. Importe o certificado para o arquivo “Autoridades de Certificação de Raiz Fidedigna” da conta de Computador Local no servidor Web do Portal de Utilizador, para que este confie no certificado quando inicia a ligação SSL.

![SDK do Serviço Web de configuração do Servidor MFA](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)

### <a name="install-the-service"></a>Instalar o serviço

1. **No Servidor MFA**, navegue para o caminho de instalação.
2. Navegue para a pasta onde o servidor MFA do Azure está instalado, a predefinição é **C:\Ficheiros de Programas\Multi-Factor Authentication do Azure**.
3. Localize o ficheiro de instalação **MultiFactorAuthenticationMobileAppWebServiceSetup64**. Se o servidor **não** tiver acesso à Internet, copie o ficheiro de instalação para o servidor com acesso à Internet.
4. Se o servidor MFA **não** tiver acesso à Internet, mude para o **servidor com acesso à Internet**.
5. Execute o ficheiro de instalação **MultiFactorAuthenticationMobileAppWebServiceSetup64** como administrador, altere o Site e o diretório Virtual para um nome abreviado, se o pretender.
6. Depois de concluir a instalação, navegue para **C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService** (ou para o diretório adequado, com base no nome do diretório virtual) e edite o ficheiro Web.Config.

   * Encontre a chave **"WEB_SERVICE_SDK_AUTHENTICATION_USERNAME"** e altere **value=""** para **value="DOMAIN\User"**, onde DOMAIN\User é uma Conta de Serviço que pertence ao Grupo "PhoneFactor Admins".
   * Encontre a chave **"WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD"** e altere **value=""** para **value="Password"**, onde a Palavra-passe é a palavra-passe da Conta de Serviço introduzida na linha anterior.
   * Encontre a definição **pfMobile App Web Service_pfwssdk_PfWsSdk** e altere o valor de **http://localhost:4898/PfWsSdk.asmx** para o URL do SDK do Serviço Web (Exemplo: https://mfa.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx).
   * Guarde o ficheiro Web.Config e feche o Bloco de notas.

   > [!NOTE]
   > Uma vez que o SSL é utilizado para esta ligação, tem de fazer referência ao SDK do Serviço Web por **nome de domínio completamente qualificado (FQDN)** e **não por endereço IP**. O certificado SSL teria sido emitido para o FQDN e o URL utilizado tem de corresponder ao nome do certificado.

7. Se o site no qual o Serviço Web da Aplicação Móvel foi instalado não tiver já sido vinculado a um certificado assinado publicamente, instale o certificado no servidor, abra o Gestor do IIS e vincule o certificado ao site.
8. Abra um browser a partir de qualquer computador e navegue para o URL no qual o Serviço Web da Aplicação Móvel foi instalado (Exemplo: https://mfa.contoso.com/MultiFactorAuthMobileAppWebService). Certifique-se de que não são apresentados erros ou avisos de certificado.

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Configurar as definições da aplicação móvel no Servidor Multi-Factor Authentication do Azure

Agora que o serviço Web da aplicação móvel está instalado, terá de configurar o Servidor Multi-Factor Authentication do Azure para trabalhar com o portal.

1. Na consola do Servidor Multi-Factor Authentication, clique no ícone do Portal de Utilizador. Se os utilizadores tiverem permissão para controlar os respetivos métodos de autenticação, selecione **Aplicação Móvel** no separador Definições, em **Permitir que os utilizadores selecionem o método**. Sem esta funcionalidade ativada, os utilizadores finais têm de contactar o Suporte Técnico para concluir a ativação da Aplicação Móvel.
2. Selecione a caixa **Permitir que os utilizadores ativem a aplicação móvel**.
3. Selecione a caixa **Permitir a inscrição de utilizadores**.
4. Clique no ícone **Aplicação Móvel**.
5. Introduza o URL que está a ser utilizado com o diretório virtual que foi criado ao instalar o MultiFactorAuthenticationMobileAppWebServiceSetup64 (Exemplo: https://mfa.contoso.com/MultiFactorAuthMobileAppWebService/) no campo **URL do Serviço Web da Aplicação Móvel:**.
6. Preencha o campo **Nome da conta** com o nome da empresa ou organização a apresentar na aplicação móvel desta conta.
   ![Definições da Aplicação Móvel de configuração do Servidor MFA](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)

## <a name="next-steps"></a>Passos seguintes

- [Cenários avançados com Multi-Factor Authentication do Azure e VPNs de terceiros](multi-factor-authentication-advanced-vpn-configurations.md).