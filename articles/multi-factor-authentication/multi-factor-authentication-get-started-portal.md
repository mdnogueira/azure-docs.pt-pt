---
title: Portal de utilizador do Servidor MFA do Azure | Microsoft Docs
description: "Esta é a página do Multi-Factor Authentication que descreve como iniciar o MFA do Azure e o Portal de Utilizador."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 06b419fa-3507-4980-96a4-d2e3960e1772
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1222223f8c45249402bfdd04c8754074f877e132
ms.openlocfilehash: 1236489212b2a9c421972599a12511d5bc42efdf


---
# <a name="deploy-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>Implementar o portal de utilizador do Servidor Multi-Factor Authentication do Azure
O portal de utilizador permite ao administrador instalar e configurar o Portal de Utilizador do Multi-Factor Authentication do Azure. O portal de utilizador é um Web site do IIS que permite aos utilizadores inscreverem-se no Multi-Factor Authentication do Azure e fazerem a manutenção das contas deles. O utilizador pode alterar o número de telefone, alterar o PIN ou optar por ignorar a verificação de dois passos durante o próximo início de sessão.

Os utilizadores iniciam sessão no portal de utilizador com o nome de utilizador e a palavra-passe habituais e, depois, concluem uma chamada de verificação de dois passos ou respondem a perguntas de segurança para terminar a autenticação. Se a inscrição de utilizadores for permitida, os utilizadores configuraram o número de telefone e o PIN na primeira vez que iniciam sessão no portal de utilizador.

Os Administradores do Portal de Utilizador podem ser configurados e ser-lhes concedida permissão para adicionar novos utilizadores e atualizar utilizadores existentes.

<center>![Configuração](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## <a name="deploy-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>Implementar o portal de utilizador no mesmo servidor do Servidor Multi-Factor Authentication do Azure
Os pré-requisitos seguintes são necessários para instalar o portal de utilizador no mesmo servidor do Servidor Multi-Factor Authentication do Azure:

* IIS, incluindo compatibilidade base meta para asp.net e IIS 6 (para IIS 7 ou posterior)
* O utilizador com sessão iniciada deve ter direitos de administrador para o computador e Domínio, se aplicável.  Tal deve-se ao facto de a conta necessitar de permissões para criar grupos de segurança do Active Directory.

### <a name="to-deploy-the-user-portal"></a>Para implementar o portal de utilizador
1. No Servidor Multi-Factor Authentication do Azure, clique no ícone do **Portal de Utilizador**, no menu à esquerda e clique em **Instalar o Portal de Utilizador**.
2. Clique em **Seguinte**.
3. Clique em **Seguinte**.
4. Se o computador estiver associado a um domínio e o Active Directory não estiver configurado para proteger a comunicação entre o portal de utilizador e o serviço Multi-Factor Authentication do Azure, é apresentado o passo do Active Directory. Clique no botão **Seguinte** para concluir esta configuração automaticamente.
5. Clique em **Seguinte**.
6. Clique em **Seguinte**.
7. Clique em **Fechar**.
8. Abra um browser em qualquer computador e navegue para o URL em que o portal de utilizador foi instalado (por exemplo, https://www.publicwebsite.com/MultiFactorAuth). Certifique-se de que não são apresentados erros ou avisos de certificado.

<center>![Configuração](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## <a name="deploy-the-azure-multi-factor-authentication-server-user-portal-on-a-separate-server"></a>Implementar o portal de utilizador do Servidor Multi-Factor Authentication do Azure num Servidor Separado
Para permitir que a aplicação Microsoft Authenticator comunique com o portal de utilizador, conclua os requisitos seguintes: 

* Tem de utilizar v6.0 ou superior do Servidor Multi-Factor Authentication do Azure.
* O portal de utilizador tem de estar instalado num servidor Web com acesso à Internet com Serviços de Informação Internet (IIS) da Microsoft® 6.x, IIS 7.x ou superior.
* Ao utilizar o IIS 6.x, certifique-se de que ASP.NET v2.0.50727 está instalado, registado e definido como **Permitido**.
* Os serviços de função necessários ao utilizar o IIS 7.x ou superior incluem ASP.NET e compatibilidade com Metabase do IIS 6.
* O portal de utilizador deve ser protegido por um certificado SSL.
* O SDK do Serviço Web do Multi-Factor Authentication do Azure tem de estar instalado no IIS 6.x, IIS 7.x ou superior no servidor no qual o Servidor Multi-Factor Authentication do Azure está instalado.
* O SDK do Serviço Web do Multi-Factor Authentication do Azure tem de estar protegido por um certificado SSL.
* O portal de utilizador tem de conseguir ligar ao SDK do Serviço Web do Multi-Factor Authentication do Azure através de SSL.
* O portal de utilizador tem de conseguir comunicar com o SDK do Serviço Web do Multi-Factor Authentication do Azure com as credenciais de uma conta de serviço no grupo de segurança “PhoneFactor Admins”. Esta conta de serviço e o grupo existem no Active Directory se o Servidor Multi-Factor Authentication do Azure estiver em execução num servidor associado a um domínio. Esta conta de serviço e o grupo existem localmente no Servidor Multi-Factor Authentication do Azure se não estiver associado a um domínio.

Instalar o portal de utilizador num servidor diferente do Servidor Multi-Factor Authentication do Azure requer os seguintes três passos:

1. Instalar o SDK do serviço Web
2. Instalar o portal de utilizador
3. Configurar as Definições do Portal de Utilizador no Servidor Multi-Factor Authentication do Azure

### <a name="step-1-install-the-web-service-sdk"></a>Passo 1: Instalar o SDK do serviço Web
Se o SDK do Serviço Web do Multi-Factor Authentication do Azure não estiver já instalado no Servidor Multi-Factor Authentication do Azure, aceda a esse servidor e abra o Servidor Multi-Factor Authentication do Azure. Clique no ícone de **SDK do Serviço Web** e clique em **Instalar SDK do Serviço Web**. Siga as instruções apresentadas. 

O SDK do Serviço Web tem de estar protegido por um certificado SSL. Pode ser utilizado um certificado autoassinado para esta finalidade, mas tem de ser importado para o arquivo de “Autoridades de Certificação de Raiz Fidedigna” da conta Computador Local no servidor. Agora, o SDK do Serviço Web pode confiar nesse certificado quando iniciar a ligação SSL.

<center>![Configuração](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### <a name="step-2-install-the-user-portal"></a>Passo 2: Instalar o portal de utilizador
Antes de instalar o portal de utilizador num servidor separado, tenha em atenção as melhores práticas seguintes:

* Facilita se abrir um browser no servidor Web com acesso à Internet e navegar para o URL do SDK do Serviço Web que foi introduzido no ficheiro web.config. Se o browser conseguir aceder ao serviço Web com êxito, este deve solicitar-lhe as credenciais. Introduza o nome de utilizador e a palavra-passe que foram introduzidos no ficheiro web.config, tal como é apresentado no ficheiro. Certifique-se de que não são apresentados erros ou avisos de certificado.
* Se uma firewall ou um proxy inverso estiver junto do servidor Web do portal de utilizador e a fazer a descarga de SSL, pode editar o ficheiro web.config do portal de utilizador e adicionar a chave seguinte à secção `<appSettings>`, para que o portal de utilizador possa utilizar http em vez de https:

    `<add key="SSL_REQUIRED" value="false"/>`

#### <a name="to-install-the-user-portal"></a>Para instalar o portal de utilizador
1. Abra o Explorador do Windows no servidor do Servidor Multi-Factor Authentication do Azure e navegue para a pasta onde o Servidor Multi-Factor Authentication do Azure está instalado (por exemplo, C:\Programas\Microsoft Files\Servidor Multi-Factor Authentication). Escolha a versão de 32 bits ou 64 bits do ficheiro de instalação MultiFactorAuthenticationUserPortalSetup, conforme adequado para o servidor que será instalado no Portal de Utilizador. Copie o ficheiro de instalação para o servidor com acesso à Internet.
2. No servidor Web com acesso à Internet, o ficheiro de configuração tem de ser executado com direitos de administrador. A forma mais fácil de o fazer consiste em abrir uma linha de comandos como administrador e navegar para a localização onde o ficheiro de instalação foi copiado.
3. Execute o ficheiro de instalação MultiFactorAuthenticationUserPortalSetup64, altere o nome do Site e do Diretório Virtual, se assim o desejar.
4. Depois de concluir a instalação do Portal de Utilizador, navegue para C:\inetpub\wwwroot\MultiFactorAuth (ou o diretório adequado, com base no nome do diretório virtual) e edite o ficheiro web.config.
5. Localize a chave USE_WEB_SERVICE_SDK e altere o valor de falso para verdadeiro. Localize as chaves WEB_SERVICE_SDK_AUTHENTICATION_USERNAME e WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD e defina os valores como o nome de utilizador e a palavra-passe da conta de serviço no grupo de segurança PhoneFactor Admins (veja a secção Requisitos). Certifique-se de que introduz o Nome de Utilizador e a Palavra-Passe entre aspas no final da linha, (valor=””/>). Deve utilizar um nome de utilizador qualificado (por exemplo, domínio\nomedeutilizador ou máquina\nomedeutilizador)
6. Localize a definição pfup_pfwssdk_PfWsSdk e altere o valor de “http://localhost:4898/PfWsSdk.asmx” para o URL do SDK do serviço Web que está em execução no Servidor Multi-Factor Authentication do Azure (por exemplo, https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Uma vez que o SSL é utilizado nesta ligação, faça referência ao SDK do Serviço Web pelo nome do servidor, não pelo endereço IP, pois o certificado SSL foi emitido para o nome do servidor. Se o nome do servidor não resolver para um endereço IP do servidor com acesso à Internet, adicione uma entrada ao ficheiro de anfitriões nesse servidor para mapear o nome do Servidor Multi-Factor Authentication do Azure para o endereço IP. Guarde o ficheiro web.config após terem sido efetuadas alterações.

    Aceda a [Proteger os recursos através do Servidor Multi-Factor Authentication do Azure com o AD FS](multi-factor-authentication-get-started-adfs-w2k12.md#edit-the-multifactorauthenticationadfsadapterconfig-file) para obter mais detalhes sobre como editar o ficheiro de configuração.

7. Se o site no qual o portal de utilizador foi instalado (por exemplo, Web Site Predefinido) ainda não tiver sido vinculado com um certificado assinado publicamente, instale o certificado no servidor, abra o Gestor do IIS e vincule o certificado ao Web site.
8. Abra um browser em qualquer computador e navegue para o URL em que o portal de utilizador foi instalado (por exemplo, https://www.publicwebsite.com/MultiFactorAuth). Certifique-se de que não são apresentados erros ou avisos de certificado.

### <a name="step-3-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Passo 3: Configurar as definições do portal de utilizador no Servidor Multi-Factor Authentication do Azure
Agora que o portal de utilizador está instalado, tem de configurar o Servidor Multi-Factor Authentication do Azure para trabalhar com o mesmo.

O Servidor Multi-Factor Authentication do Azure fornece várias opções para o portal de utilizador.  A tabela seguinte disponibiliza uma lista destas opções e uma explicação das respetivas utilizações.

| Definições do Portal de Utilizador | Descrição |
|:--- |:--- |
| URL do Portal de Utilizador | Introduzir o URL no qual o portal está alojado. |
| Autenticação primária | Especifique o tipo de autenticação a utilizar quando iniciar sessão no portal.  Autenticação do Windows, Radius ou LDAP. |
| Permitir que os utilizadores iniciem sessão | Permitir aos utilizadores introduzir um nome de utilizador e palavra-passe na página de início de sessão do portal de utilizador.  Se não estiver selecionado, as caixas estarão esbatidas. |
| Permitir a inscrição de utilizador | Permitir ao utilizador inscrever-se na Multi-Factor Authentication, ao redirecioná-lo para um ecrã de configuração que lhe pede informações adicionais, como o número de telefone. O pedido do telefone alternativo permite aos utilizadores especificarem um número de telefone secundário. Pedir o token OATH de terceiros permite aos utilizadores especificar um token OATH de terceiros. |
| Permitir que os utilizadores iniciem a Omissão de Uso Individual | Permitir que os utilizadores iniciem uma omissão de uso individual.  Se um utilizador definir esta ação, a mesma terá efeito da próxima vez que o utilizador iniciar sessão. O pedido de segundos de omissão fornece uma caixa ao utilizador, para que este possa alterar a predefinição de 300 segundos. Caso contrário, a omissão de uso individual só é adequada para 300 segundos. |
| Permitir que os utilizadores selecionem o método | Permitir aos utilizadores especificar o método de contacto principal.  Isto pode ser chamada telefónica, mensagem de texto, aplicação móvel ou token OATH. |
| Permitir que os utilizadores selecionem o idioma | Permitir ao utilizador alterar o idioma que é utilizado para a chamada telefónica, mensagem de texto, aplicação móvel ou token OATH. |
| Permitir que os utilizadores ativem a aplicação móvel | Permitir aos utilizadores gerar um código de ativação para concluir o processo de ativação da aplicação móvel que é utilizada com o servidor.  Também pode definir o número de dispositivos em que os utilizadores podem ativar a aplicação, entre um e dez. |
| Utilizar perguntas de segurança para contingência | Permitir perguntas de segurança no caso de falha da verificação em dois passos.  Pode especificar o número de perguntas de segurança que têm de ser respondidas com êxito. |
| Permitir aos utilizadores associar token OATH de terceiros | Permitir aos utilizadores especificar um token OATH de terceiros. |
| Utilizar token OATH para contingência | Permitir a utilização de um token OATH caso a verificação de dois passos falhe. Também pode especificar o tempo limite de sessão em minutos. |
| Ativar registo | Ativar o registo no portal de utilizador. Os ficheiros de registo estão localizados em: C:\Programas\Microsoft Files\Servidor Multi-Factor Authentication\Registos. |

A maior parte destas definições fica visível para o utilizador assim que forem ativadas e o utilizador iniciar sessão no portal de utilizador.

![Definições do portal de utilizador](./media/multi-factor-authentication-get-started-portal/portalsettings.png)

### <a name="to-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Para configurar as definições do portal de utilizador no Servidor Multi-Factor Authentication do Azure
1. No Servidor Multi-Factor Authentication do Azure, clique no ícone do **Portal de Utilizador**. No separador Definições, introduza o URL do portal de utilizador na caixa de texto **URL do Portal de Utilizador**. Este URL vai ser incluído nos e-mails que são enviados aos utilizadores quando forem importados para o Servidor Multi-Factor Authentication do Azure, se a funcionalidade de e-mail tiver sido ativada.
2. Escolha as definições que pretende utilizar no Portal de Utilizador. Por exemplo, se os utilizadores tiverem permissão para controlar os métodos de autenticação deles, certifique-se de que **Permitir que os utilizadores selecionem o método** está marcado, juntamente com os métodos que podem escolher.
3. Clique na ligação **Ajuda**, no canto superior direito, para obter ajuda na compreensão de qualquer uma das definições apresentadas.

<center>![Configuração](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## <a name="administrators-tab"></a>Separador Administradores
Este separador permite simplesmente adicionar utilizadores que terão privilégios administrativos.  Ao adicionar um administrador, pode ajustar as permissões que ele recebe. Clique no botão **Adicionar**, selecione um utilizador e as respetivas permissões e, em seguida, clicar em **Adicionar**.

![Administradores do portal de utilizador](./media/multi-factor-authentication-get-started-portal/admin.png)

## <a name="security-questions"></a>Perguntas de Segurança
Este separador permite-lhe especificar as perguntas de segurança às quais os utilizadores terão de responder, se a opção **Utilizar perguntas de segurança para contingência** estiver selecionada.  O Servidor Multi-Factor Authentication do Azure inclui perguntas predefinidas que pode utilizar. Pode alterar a ordem ou adicionar as suas próprias perguntas.  Se adicionar as suas próprias perguntas, também pode especificar o idioma no qual pretende que as mesmas sejam apresentadas.

![Perguntas de segurança do portal de utilizador](./media/multi-factor-authentication-get-started-portal/secquestion.png)

## <a name="saml"></a>SAML
Utilize este separador para configurar o portal de utilizador para aceitar instruções de um fornecedor de identidade através de SAML.  Pode especificar o tempo limite da sessão, o certificado de verificação e o URL de redirecionamento de fim de sessão.

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## <a name="trusted-ips"></a>IPs Fidedignos
Este separador permite-lhe especificar endereços IP individuais ou intervalos de endereços IP, que podem ser adicionados para que os utilizadores que iniciem sessão a partir de um desses endereços não tenha de concluir a verificação de dois passos.

![IPs fidedignos do portal de utilizador](./media/multi-factor-authentication-get-started-portal/trusted.png)

## <a name="self-service-user-enrollment"></a>Inscrição de Utilizador Self-Service
Se pretende que os utilizadores iniciem sessão e se inscrevam, tem de selecionar as opções **Permitir que os utilizadores iniciem sessão** e** Permitir a inscrição de utilizador**, no separador Definições. Lembre-se de que as definições que selecionar afetam a experiência de início de sessão do utilizador.

Por exemplo, quando um utilizador inicia sessão no portal de utilizador pela primeira vez, é direcionado para a página de Configuração de Utilizador do Multi-Factor Authentication do Azure.  Dependendo de como tiver configurado o Multi-Factor Authentication do Azure, o utilizador poderá conseguir selecionar o método de autenticação.  

Se o utilizador selecionar o método de autenticação Chamada de Voz ou tiver sido pré-configurado para utilizar este método, a página pedir-lhe-á para introduzir o respetivo número de telefone principal e uma extensão, se aplicável.  Também poderá ser permitida a introdução de um número de telefone de cópia de segurança.  

![IPs fidedignos do portal de utilizador](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Se for pedido ao utilizador para utilizar um PIN quando fizer a autenticação, a página também lhe pedirá que crie um PIN.  Após introduzir o número ou números de telefone e o PIN (se aplicável), o utilizador clica no botão **Telefonar-me Agora para Autenticação**.  O Multi-Factor Authentication do Azure irá efetuar uma chamada telefónica de autenticação para o número de telefone principal do utilizador.  O utilizador tem de atender a chamada e introduzir o PIN (se aplicável), e premir # para avançar para o próximo passo do processo de autoinscrição.   

Se o utilizador selecionar o método de autenticação por Mensagem SMS ou tiver sido pré-configurado para utilizar este método, a página pedir-lhe-á o respetivo número de telemóvel.  Se for pedido ao utilizador para utilizar um PIN quando fizer a autenticação, a página também lhe pedirá para introduzir um PIN.  Após introduzir o número de telefone e o PIN (se aplicável), o utilizador clica no botão **Enviar-me SMS Agora para Autenticação**.  O Multi-Factor Authentication do Azure irá efetuar uma verificação por SMS para o telemóvel do utilizador.  O utilizador recebe a mensagem de texto com um código de acesso de uso individual (OTP) e, em seguida, responde à mensagem com esse OTP e o respetivo PIN (se aplicável).

![SMS do portal de utilizador](./media/multi-factor-authentication-get-started-portal/text.png)   

Se o utilizador selecionar o método de verificação de Aplicação Móvel, a página pedir-lhe-á que instale a aplicação Microsoft Authenticator no dispositivo e que gere um código de ativação.  Depois de instalar a aplicação, o utilizador clica no botão Gerar Código de Ativação.    

> [!NOTE]
> Para utilizar a aplicação Microsoft Authenticator, o utilizador tem de ativar as notificações push no dispositivo.

A página apresenta então um código de ativação e um URL, juntamente com uma imagem de código de barras.  Se for pedido ao utilizador para utilizar um PIN quando fizer a autenticação, a página também lhe pedirá para introduzir um PIN.  O utilizador introduz o código de ativação e o URL na aplicação Microsoft Authentication ou utiliza o leitor de código de barras para ler a imagem de código de barras e clica no botão Ativar.    

Após a ativação estar concluída, o utilizador clica no botão **Autenticar-me Agora**.  O Multi-Factor Authentication do Azure irá efetuar uma verificação para a aplicação móvel do utilizador.  O utilizador tem de introduzir o PIN (se aplicável), e premir o botão Autenticar na aplicação móvel para avançar para o próximo passo do processo de autoinscrição.  

Se os administradores tiverem configurado o Servidor Multi-Factor Authentication do Azure para recolher perguntas e respostas de segurança, o utilizador é então direcionado para a página Perguntas de Segurança.  O utilizador tem de selecionar quatro perguntas de segurança e fornecer respostas às perguntas selecionadas.    

![Perguntas de segurança do portal de utilizador](./media/multi-factor-authentication-get-started-portal/secq.png)  

A autoinscrição do utilizador está agora concluída e o utilizador tem sessão iniciada no portal de utilizador.  Os utilizadores podem voltar a iniciar sessão no portal de utilizador em qualquer altura no futuro para alterar os números de telefone, PINs, métodos de autenticação e perguntas de segurança, se tal for permitido pelos administradores deles.




<!--HONumber=Jan17_HO1-->


