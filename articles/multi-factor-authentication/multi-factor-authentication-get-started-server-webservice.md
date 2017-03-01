---
title: "Serviço Web da Aplicação Móvel do Servidor MFA do Azure | Microsoft Docs"
description: "A aplicação Microsoft Authenticator oferece uma opção de autenticação fora de banda adicional.  Permite ao servidor MFA utilizar notificações push para os utilizadores."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 6c8d6fcc-70f4-4da4-9610-c76d66635b8b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/15/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 999361daa2faebe3e88cab0b6085a938d6f40e9d
ms.openlocfilehash: d3a122d7d26635e13281b1cba450937519ed4be6


---
# <a name="getting-started-with-the-mfa-server-mobile-app-web-service"></a>Introdução ao Serviço Web da Aplicação Móvel do Servidor MFA
A aplicação Microsoft Authenticator oferece uma opção de verificação fora de banda adicional. Em vez de fazer uma chamada telefónica automática ou enviar um SMS ao utilizador durante o início de sessão, o Multi-Factor Authentication do Azure envia uma notificação push para a aplicação Microsoft Authenticator no smartphone ou tablet do utilizador. O utilizador toca simplesmente em **Verificar** (ou introduz um PIN e toca em "Autenticar") na aplicação para concluir o início de sessão. 

É preferível utilizar uma aplicação móvel para a verificação de dois passos quando a receção de telefone é pouco fiável. Se utilizar a aplicação como um gerador de token OATH, não é precisa qualquer ligação de rede ou à Internet. 

Instalar o portal de utilizador num servidor diferente do Servidor Multi-Factor Authentication do Azure requer os seguintes passos:

1. Instalar o SDK do serviço Web
2. Instalar o serviço Web da aplicação móvel
3. Configurar as definições da aplicação móvel no Servidor Multi-Factor Authentication do Azure
4. Ativar a aplicação Microsoft Authenticator para os utilizadores finais

## <a name="requirements"></a>Requisitos

Para utilizar a aplicação Microsoft Authenticator, é necessário o seguinte para que a aplicação consiga comunicar com o Serviço Web da Aplicação Móvel com êxito:

* Servidor Multi-Factor Authentication do Azure v6.0 ou superior
* Instalar o Serviço Web da Aplicação Móvel num servidor Web com acesso à Internet com [Serviços de Informação Internet (IIS) IIS 7.x ou superior](http://www.iis.net/) da Microsoft®
* O ASP.NET v4.0.30319 estar instalado, registado e definido como Permitido
* Os serviços de função necessários incluem o ASP.NET e a Compatibilidade com Metabase do IIS 6
* O Serviço Web da Aplicação Móvel estar acessível através de um URL público
* O Serviço Web da Aplicação Móvel estar protegido por um certificado SSL.
* Instalar o SDK do Serviço Web do Multi-Factor Authentication do Azure no IIS 7.x ou superior no mesmo servidor no qual o Servidor Multi-Factor Authentication do Azure está instalado
* O SDK do Serviço Web do Multi-Factor Authentication do Azure estar protegido por um certificado SSL.
* O Serviço Web da Aplicação Móvel conseguir ligar ao SDK do Serviço Web do Multi-Factor Authentication do Azure através de SSL
* O Serviço Web da Aplicação Móvel conseguir autenticar-se no SDK do Serviço Web do MFA do Azure com as credenciais de uma conta de serviço que seja membro do grupo de segurança "PhoneFactor Admins". Esta conta de serviço e o grupo existem no Active Directory se o Servidor Multi-Factor Authentication do Azure estiver num servidor associado a um domínio. Esta conta de serviço e o grupo existem localmente no Servidor Multi-Factor Authentication do Azure se não estiver associado a um domínio.


## <a name="install-the-web-service-sdk"></a>Instalar o SDK do serviço Web
Se o SDK do Serviço Web do Multi-Factor Authentication do Azure ainda não estiver instalado no Servidor Multi-Factor Authentication (MFA) do Azure, aceda a esse servidor e abra o Servidor MFA do Azure. 

1. Clique no ícone SDK do Serviço Web.
2. Clique em **Instalar SDK do Serviço Web** e siga as instruções apresentadas. 

O SDK do Serviço Web tem de estar protegido por um certificado SSL. Pode utilizar um certificado autoassinado para este fim. Importe o certificado para o arquivo “Autoridades de Certificação de Raiz Fidedigna” da conta de Computador Local no servidor Web do Portal de Utilizador, para que este confie no certificado quando inicia a ligação SSL.

![Configurar](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)

## <a name="install-the-mobile-app-web-service"></a>Instalar o serviço Web da aplicação móvel
Antes de instalar o serviço Web da aplicação móvel, tenha em atenção os seguintes detalhes:

* Se o Portal de Utilizador do MFA do Azure já estiver instalado no servidor com acesso à Internet, o nome de utilizador, a palavra-passe e o URL para o SDK do Serviço Web podem ser copiados do ficheiro web.config do Portal de Utilizador.
* Facilita se abrir um browser no servidor Web com acesso à Internet e navegar para o URL do SDK do Serviço Web que foi introduzido no ficheiro web.config. Se o browser conseguir aceder ao serviço Web com êxito, este deve solicitar-lhe as credenciais. Introduza o nome de utilizador e a palavra-passe que foram introduzidos no ficheiro web.config, tal como é apresentado no ficheiro. Certifique-se de que não são apresentados erros ou avisos de certificado.
* Se uma firewall ou um proxy inverso estiver junto do servidor Web do Serviço Web da Aplicação Móvel e a fazer o descarregamento de SSL, pode editar o ficheiro web.config do Serviço Web da Aplicação Móvel para que o Serviço Web da Aplicação Móvel possa utilizar http em vez de https. Ainda é necessário utilizar o SSL a partir da Aplicação Móvel para a firewall/proxy inverso. Adicione a chave seguinte à secção \<appSettings\>: 

        <add key="SSL_REQUIRED" value="false"/>

### <a name="install-the-service"></a>Instalar o serviço

1. Abra o Explorador do Windows no Servidor Multi-Factor Authentication do Azure e navegue para a pasta onde o Servidor MFA do Azure está instalado (normalmente, C:\Programas\Azure Multi-Factor Authentication). Escolha a versão de 32 bits ou 64 bits do ficheiro de instalação AuthenticationPhoneAppWebServiceSetup do Multi-Factor do Azure. Copie o ficheiro de instalação para o servidor com acesso à Internet.

2. No servidor Web com acesso à Internet, execute o ficheiro de configuração com direitos de administrador. Abra uma linha de comandos como administrador e navegue para a localização onde o ficheiro de instalação foi copiado.

3. Execute o ficheiro de instalação AuthenticationMobileAppWebServiceSetup, do Multi-Factor, altere o Site, se o pretender, e altere o diretório Virtual para um nome abreviado, como “PA”.

  É recomendado um nome de diretório virtual curto, uma vez que os utilizadores têm de introduzir o URL do Serviço Web da Aplicação Móvel num dispositivo móvel durante a ativação.

4. Depois de concluir a instalação de Azure Multi-Factor AuthenticationMobileAppWebServiceSetup, navegue para C:\inetpub\wwwroot\PA (ou o diretório adequado, com base no nome do diretório virtual) e edite o ficheiro web.config. 

5. Localize as chaves WEB_SERVICE_SDK_AUTHENTICATION_USERNAME e WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD. Defina os valores para o nome de utilizador e palavra-passe da conta de serviço que faça parte do grupo de segurança PhoneFactor Admins. Poderá ser a mesma conta que está a ser utilizada como Identidade do Portal de Utilizador do Multi-Factor Authentication do Azure, se tiver sido instalado anteriormente. Certifique-se de que introduz o Nome de Utilizador e a Palavra-Passe entre aspas no final da linha, (valor=””/>). Utilize um nome de utilizador qualificado, como domínio\nomedeutilizador ou máquina\nomedeutilizador.  

6. Localize a definição pfMobile App Web Service_pfwssdk_PfWsSdk. Altere o valor de *http://localhost:4898/PfWsSdk.asmx* para o URL do SDK do Serviço Web que está em execução no Servidor Multi-Factor Authentication do Azure (como https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). 

  Uma vez que o SSL é utilizado para esta ligação, tem de fazer referência ao SDK do Serviço Web por nome de servidor e não por endereço IP. O certificado SSL teria sido emitido para o nome do servidor e o URL utilizado tem de corresponder ao nome do certificado. O nome do servidor não pode ser resolvido para um endereço IP do servidor com acesso à Internet. Se for este o caso, adicione uma entrada ao ficheiro de anfitriões nesse servidor para mapear o nome do Servidor Multi-Factor Authentication do Azure para o respetivo endereço IP. Guarde o ficheiro web.config após terem sido efetuadas alterações.

7. Se o site no qual o Serviço Web da Aplicação Móvel foi instalado não tiver já sido vinculado a um certificado assinado publicamente, instale o certificado no servidor, abra o Gestor do IIS e vincule o certificado ao site.

8. Abra um browser a partir de qualquer computador e navegue para o URL no qual o Serviço Web da Aplicação Móvel foi instalado (como https://www.publicwebsite.com/PA). Certifique-se de que não são apresentados erros ou avisos de certificado.

### <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Configurar as definições da aplicação móvel no Servidor Multi-Factor Authentication do Azure
Agora que o serviço Web da aplicação móvel está instalado, terá de configurar o Servidor Multi-Factor Authentication do Azure para trabalhar com o portal.

1. No Servidor MFA do Azure, clique no ícone de Portal de Utilizador. Se os utilizadores tiverem permissão para controlar os respetivos métodos de autenticação, selecione **Aplicação Móvel** no separador Definições, em **Permitir que os utilizadores selecionem o método**. Sem esta funcionalidade ativada, os utilizadores finais terão de contactar o Suporte Técnico para concluir a ativação da Aplicação Móvel.
2. Selecione a caixa **Permitir que os utilizadores ativem a aplicação móvel**.
3. Selecione a caixa **Permitir a inscrição de utilizadores**.
4. Clique no ícone de Aplicação Móvel.
5. Introduza o URL que está a ser utilizado com o diretório virtual que foi criado ao instalar o Azure Multi-Factor AuthenticationMobileAppWebServiceSetup. Pode ser introduzido um Nome de Conta no espaço fornecido. Este nome de empresa será apresentado na aplicação móvel. Se tiver sido deixado em branco, será apresentado o nome do Fornecedor de Multi-Factor Auth criado no portal clássico do Azure.

<center>![Configuração](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>



<!--HONumber=Feb17_HO3-->


