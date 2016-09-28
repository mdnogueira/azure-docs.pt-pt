<properties 
    pageTitle="Introdução ao Serviço Web da Aplicação Móvel do Servidor MFA"
    description="A Aplicação Multi-Factor Authentication do Azure oferece uma opção de autenticação fora de banda adicional.  Permite ao servidor MFA utilizar notificações push para os utilizadores."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>


# Introdução ao Serviço Web da Aplicação Móvel do Servidor MFA

A Aplicação Multi-Factor Authentication do Azure oferece uma opção de autenticação fora de banda adicional. Em vez de fazer uma chamada telefónica automática ou enviar um SMS ao utilizador durante o início de sessão, o Multi-Factor Authentication do Azure envia uma notificação push para a Aplicação Multi-Factor Authentication do Azure no smartphone ou tablet do utilizador. O utilizador toca simplesmente em "Autenticar" (ou introduzi um PIN e toca em "Autenticar") na aplicação para iniciar sessão.

Para utilizar a Aplicação Multi-Factor Authentication do Azure, é necessário o seguinte para que a aplicação consiga comunicar com o Serviço Web da Aplicação Móvel com êxito:

- Veja Requisitos de Hardware e Software, para obter os requisitos de hardware e software
- Tem de utilizar o Servidor Multi-Factor Authentication do Azure v6.0 ou superior.
- O Serviço Web da Aplicação Móvel tem de estar instalado num servidor Web com acesso à Internet com Serviços de Informação Internet (IIS) da Microsoft® 7.x ou superior.  Para obter mais informações sobre o IIS, veja [IIS.NET](http://www.iis.net/).
- Certifique-se de que o ASP.NET v4.0.30319 está instalado, registado e definido como Permitido
- Os serviços de função necessários incluem o ASP.NET e a Compatibilidade com Metabase do IIS 6
- O Serviço Web da Aplicação Móvel tem de estar acessível através de um URL público
- O Serviço Web da Aplicação Móvel tem de estar protegido por um certificado SSL.
- O SDK do Serviço Web do Multi-Factor Authentication do Azure tem de estar instalado no IIS 7.x ou superior no servidor no qual o Servidor Multi-Factor Authentication do Azure está instalado.
- O SDK do Serviço Web do Multi-Factor Authentication do Azure tem de estar protegido por um certificado SSL.
- O Serviço Web da Aplicação Móvel tem de conseguir ligar ao SDK do Serviço Web do Multi-Factor Authentication do Azure através de SSL
- O Serviço Web da Aplicação Móvel tem de conseguir autenticar para o SDK do Serviço Web do Multi-Factor Authentication do Azure com as credenciais de uma conta de serviço que seja um membro de um grupo de segurança denominado “PhoneFactor Admins”. Esta conta de serviço e o grupo existem no Active Directory se o Servidor Multi-Factor Authentication do Azure estiver em execução num servidor associado a um domínio. Esta conta de serviço e o grupo existem localmente no Servidor Multi-Factor Authentication do Azure se não estiver associado a um domínio.


Instalar o portal de utilizador num servidor diferente do Servidor Multi-Factor Authentication do Azure requer os seguintes três passos:

1. Instalar o SDK do serviço Web
2. Instalar o serviço Web da aplicação móvel
3. Configurar as definições da aplicação móvel no Servidor Multi-Factor Authentication do Azure
4. Ativar a Aplicação Multi-Factor Authentication do Azure para os utilizadores finais

## Instalar o SDK do serviço Web

Se o SDK do Serviço Web do Multi-Factor Authentication do Azure não estiver já instalado no Servidor Multi-Factor Authentication do Azure, aceda a esse servidor e abra o Servidor Multi-Factor Authentication do Azure. Clique no ícone de SDK do serviço Web, clique no botão Instalar SDK do Serviço Web... e siga as instruções apresentadas. O SDK do Serviço Web tem de estar protegido por um certificado SSL. Um certificado autoassinado pode ser utilizado para esta finalidade, mas tem de ser importado para o arquivo de “Autoridades de Certificação de Raiz Fidedigna” da conta de Computador Local no servidor Web do Portal de Utilizador, para que este confie no certificado quando inicia a ligação SSL.

<center>![Configurar](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)</center>

## Instalar o serviço Web da aplicação móvel
Antes de instalar o serviço Web da aplicação móvel, tenha em atenção o seguinte:

- Se o Portal de Utilizador do Multi-Factor Authentication do Azure já estiver instalado no servidor com acesso à Internet, o nome de utilizador, a palavra-passe e o URL para o SDK do Serviço Web podem ser copiados do ficheiro web.config do Portal de Utilizador.
- Facilita se abrir um browser no servidor Web com acesso à Internet e navegar para o URL do SDK do Serviço Web que foi introduzido no ficheiro web.config. Se o browser conseguir aceder ao serviço Web com êxito, este deve solicitar-lhe as credenciais. Introduza o nome de utilizador e a palavra-passe que foram introduzidos no ficheiro web.config, tal como é apresentado no ficheiro. Certifique-se de que não são apresentados erros ou avisos de certificado.
- Se uma firewall ou um proxy inverso estiver junto do servidor Web do Serviço Web da Aplicação Móvel e a fazer a descarga de SSL, pode editar o ficheiro web.config do Serviço Web da Aplicação Móvel ou adicionar a seguinte chave à secção <appSettings>, para que o Serviço Web da Aplicação Móvel possa utilizar http em vez de https. No entanto, ainda é necessário utilizar o SSL a partir da Aplicação Móvel para a firewall/proxy inverso. <add key="SSL_REQUIRED" value="false"/>

### Para instalar o serviço Web da aplicação móvel

<ol>
<li>Abra o Explorador do Windows no Servidor Multi-Factor Authentication do Azure e navegue para a pasta onde o Servidor Multi-Factor Authentication do Azure está instalado (por exemplo, C:\Programas\Azure Multi-Factor Authentication). Escolha a versão de 32 ou 64 bits do ficheiro de instalação Azure Multi-Factor AuthenticationPhoneAppWebServiceSetup conforme adequado para o servidor no qual será instalado o Serviço Web da Aplicação Móvel. Copie o ficheiro de instalação para o servidor com acesso à Internet.</li>

<li>No servidor Web com acesso à Internet, o ficheiro de configuração tem de ser executado com direitos de administrador. A forma mais fácil de o fazer consiste em abrir uma linha de comandos como administrador e navegar para a localização onde o ficheiro de instalação foi copiado.</li>  

<li>Execute o ficheiro de instalação AuthenticationMobileAppWebServiceSetup, do Multi-Factor, altere o Site, se o pretender, e altere o diretório Virtual para um nome abreviado, como "PA". É recomendado um nome de diretório virtual curto, uma vez que os utilizadores têm de introduzir o URL do Serviço Web da Aplicação Móvel num dispositivo móvel durante a ativação.</li>

<li>Depois de concluir a instalação de Azure Multi-Factor AuthenticationMobileAppWebServiceSetup, navegue para C:\inetpub\wwwroot\PA (ou o diretório adequado, com base no nome do diretório virtual) e edite o ficheiro web.config.</li>  

<li>Localize as chaves WEB_SERVICE_SDK_AUTHENTICATION_USERNAME e WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD e defina os valores para o nome de utilizador e a palavra-passe da conta de serviço que é um membro do grupo de segurança PhoneFactor Admins (veja a secção Requisitos acima). Poderá ser a mesma conta que está a ser utilizada como Identidade do Portal de Utilizador do Multi-Factor Authentication do Azure, se tiver sido instalado anteriormente. Certifique-se de que introduz o Nome de Utilizador e a Palavra-Passe entre aspas no final da linha, (valor=””/>). É recomendado utilizar um nome de utilizador qualificado (por exemplo, domínio\nomedeutilizador ou máquina\nomedeutilizador).</li>  

<li>Localize a definição pfMobile App Web Service_pfwssdk_PfWsSdk e altere o valor de “http://localhost:4898/PfWsSdk.asmx” para o URL do SDK do Serviço Web que está em execução no Servidor Multi-Factor Authentication do Azure (por exemplo, https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Uma vez que o SSL é utilizado para esta ligação, tem de fazer referência ao SDK do Serviço Web pelo nome do servidor e não pelo endereço IP, pois o certificado SSL terá sido emitido para o nome do servidor e o URL utilizado tem de corresponder ao nome no certificado. Se o nome do servidor não resolver para um endereço IP do servidor com acesso à Internet, adicione uma entrada ao ficheiro de anfitriões nesse servidor para mapear o nome do Servidor Multi-Factor Authentication do Azure para o endereço IP. Guarde o ficheiro web.config após terem sido efetuadas alterações.</li>  

<li>Se o site no qual o Serviço Web da Aplicação Móvel foi instalado (por exemplo, site predefinido) não tiver já sido vinculado a um certificado assinado publicamente, instale o certificado no servidor se não estiver já instalado, abra o Gestor do IIS e vincule o certificado ao site.</li>  

<li>Abra um browser a partir de qualquer computador e navegue para o URL em que o Serviço Web da Aplicação Móvel foi instalado (por exemplo, https://www.publicwebsite.com/PA). Certifique-se de que não são apresentados erros ou avisos de certificado.</li>

### Configurar as definições da aplicação móvel no Servidor Multi-Factor Authentication do Azure
Agora que o serviço Web da aplicação móvel está instalado, terá de configurar o Servidor Multi-Factor Authentication do Azure para trabalhar com o portal.

#### Para configurar as definições da aplicação móvel no Servidor Multi-Factor Authentication do Azure

1. No Servidor Multi-Factor Authentication do Azure, clique no ícone do Portal de Utilizador. Se os utilizadores tiverem permissão para controlar os respetivos métodos de autenticação, no separador Definições, em Permitir que os utilizadores selecionem o método, selecione Aplicação Móvel. Sem esta funcionalidade ativada, os utilizadores finais terão de contactar o Suporte Técnico para concluir a ativação da Aplicação Móvel.
2. Selecione a caixa Permitir que os utilizadores ativem a Aplicação Móvel.
3. Selecione a caixa Permitir a Inscrição de Utilizadores.
4. Clique no ícone de Aplicação Móvel.
5. Introduza o URL que está a ser utilizado com o diretório virtual criado ao instalar o Azure Multi-Factor AuthenticationMobileAppWebServiceSetup. Pode ser introduzido um Nome de Conta no espaço fornecido. Este nome de empresa será apresentado na aplicação móvel. Se tiver sido deixado em branco, será apresentado o nome do Fornecedor de Autenticação Multifator criado no Portal de Gestão do Azure.



<center>![Configurar](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>



<!--HONumber=Sep16_HO3-->


