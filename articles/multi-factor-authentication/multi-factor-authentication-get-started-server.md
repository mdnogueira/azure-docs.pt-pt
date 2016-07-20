<properties 
    pageTitle="Introdução ao Servidor Multi-Factor Authentication do Azure" 
    description="Esta é a página do Multi-Factor Authentication do Azure que descreve como iniciar o Servidor do MFA do Azure." 
    services="multi-factor-authentication"
    keywords="authentication server, azure multi factor authentication app activation page, authentication server download" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtand"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="05/12/2016" 
    ms.author="billmath"/>

# Introdução ao Servidor Multi-Factor Authentication do Azure




<center>![Nuvem](./media/multi-factor-authentication-get-started-server/server2.png)</center>

Agora que determinamos que vamos utilizar a autenticação multifator no local, vamos começar. Esta página abrange uma nova instalação do servidor e como configurá-lo com o Active Directory no local. Se já tiver o servidor PhoneFactor instalado e quiser atualizar, veja [Atualizar para o Servidor Multi-Factor do Azure](multi-factor-authentication-get-started-server-upgrade.md) ou, se estiver à procura de informações sobre como instalar apenas o serviço Web, veja [Implementar o Serviço Web de Aplicações Móveis do Servidor Multi-Factor Authentication do Azure](multi-factor-authentication-get-started-server-webservice.md).


## Transferir o Servidor Multi-Factor Authentication do Azure



Existem duas formas diferentes de transferir o Servidor Multi-Factor Authentication do Azure. Ambas são efetuadas através do portal do Azure. A primeira é através da gestão direta do Fornecedor do Multi-Factor Auth. A segunda é através de definições de serviço. A segunda opção requer um Fornecedor do Multi-Factor Auth ou uma licença MFA do Azure, o Azure AD Premium ou o Enterprise Mobility Suite.


### Para transferir o Servidor Multi-Factor Authentication do Azure a partir do portal do Azure
--------------------------------------------------------------------------------

1. Inicie sessão no Portal do Azure como Administrador.
2. No lado esquerdo, selecione Active Directory.
3. Na página do Active Directory, clique em **Fornecedores do Multi-Factor Auth** na parte superior
4. Na parte inferior, clique em **Gerir**
5. Esta ação irá abrir uma nova página.  Clique em **Transferências.**
![Transferir](./media/multi-factor-authentication-sdk/download.png)
6. Por cima de **Gerar Credenciais de Ativação**, clique em **Transferência.**
![Transferir](./media/multi-factor-authentication-get-started-server/download4.png)
7. Guarde a transferência.



### Para transferir o Servidor Multi-Factor Authentication do Azure através das definições de serviço


1. Inicie sessão no Portal do Azure como Administrador.
2. No lado esquerdo, selecione Active Directory.
3. Faça duplo clique na sua instância do Azure AD.
4. Na parte superior, clique em **Configurar**
![Transferir](./media/multi-factor-authentication-sdk/download2.png)
5. Em autenticação multifator, selecione **Gerir definições do serviço**
6. Na página de definições do serviço, na parte inferior do ecrã, clique em **Ir para o portal**.
![Transferência](./media/multi-factor-authentication-get-started-server/servicesettings.png)
7. Esta ação irá abrir uma nova página. Clique em **Transferências.**
8. Por cima de **Gerar Credenciais de Ativação**, clique em **Transferência.**
9. Guarde a transferência.




## Instalar e Configurar o Servidor Multi-Factor Authentication do Azure
Agora que transferiu o servidor, pode instalá-lo e configurá-lo.  Certifique-se de que o servidor no qual o está a instalar cumpre os seguintes requisitos:



Requisitos do Servidor Multi-Factor Authentication do Azure|Descrição|
:------------- | :------------- | 
Hardware|<li>200 MB de espaço no disco rígido</li><li>processador com capacidade de 32 ou 64 bits</li><li>1 GB de RAM ou superior</li>
Software|<li>Windows Server 2008 ou superior, se o anfitrião for um SO servidor</li><li>Windows 7 ou superior, se o anfitrião for um SO cliente</li><li>Microsoft .NET 4.0 Framework</li><li>O IIS 7.0 ou superior, se estiver a instalar o portal de utilizador ou o SDK do serviço Web</li>

### Requisitos da firewall do Servidor Multi-Factor Authentication do Azure
--------------------------------------------------------------------------------
Cada servidor MFA tem de conseguir comunicar na porta 443 de saída ao seguinte:

- https://pfd.phonefactor.net
- https://pfd2.phonefactor.net
- https://css.phonefactor.net

Se as firewalls de saída estiverem restritas na porta 443, os seguintes intervalos de endereços IP têm de ser abertos:

Subrede IP|Máscara de rede|Intervalo de IP
:------------- | :------------- | :------------- |
134.170.116.0/25|255.255.255.128|134.170.116.1 – 134.170.116.126
134.170.165.0/25|255.255.255.128|134.170.165.1 – 134.170.165.126
70.37.154.128/25|255.255.255.128|70.37.154.129 – 70.37.154.254

Se não estiver a utilizar funcionalidades de Confirmação de Eventos do Multi-Factor Authentication do Azure e se os utilizadores não estiverem a autenticar com as aplicações móveis do Multi-Factor Auth de dispositivos na rede da empresa, os intervalos de IP podem ser reduzidos aos seguintes:


Subrede IP|Máscara de rede|Intervalo de IP
:------------- | :------------- | :------------- |
134.170.116.72/29|255.255.255.248|134.170.116.72 – 134.170.116.79
134.170.165.72/29|255.255.255.248|134.170.165.72 – 134.170.165.79
70.37.154.200/29|255.255.255.248|70.37.154.201 – 70.37.154.206


### Para instalar e configurar o servidor Multi-Factor Authentication do Azure
--------------------------------------------------------------------------------


1. Faça duplo clique no executável. Isto irá iniciar a instalação.
2. No ecrã Selecionar Pasta de Instalação, certifique-se de que a pasta está correta e clique em Seguinte.
3. Quando concluir a instalação, clique em Concluir.  Este procedimento inicia o assistente de configuração.
4. No ecrã de boas-vindas do assistente de configuração, marque **Ignorar com o Assistente de Configuração da Autenticação** e clique em **Seguinte**.  Este procedimento irá fechar o assistente e iniciar o servidor.
![Nuvem](./media/multi-factor-authentication-get-started-server/skip2.png)

5. Na página a partir da qual transferimos o servidor, clique no botão **Gerar Credenciais de Ativação**.  Copie estas informações para o servidor MFA do Azure nas caixas fornecidas e clique em **Ativar**.


Os passos acima mostram uma configuração rápida com o assistente de configuração.  Pode executar novamente o assistente de autenticação, selecionando-o no menu Ferramentas do servidor.



##Importar utilizadores do Active Directory

Agora que o servidor está instalado e configurado, pode rapidamente importar utilizadores para o Servidor MFA no Azure. 

### Para importar utilizadores do Active Directory
--------------------------------------------------------------------------------


1. No Servidor MFA do Azure, à esquerda, selecione **Utilizadores**.
2. Na parte inferior, selecione **Importar do Active Directory**.
3. Agora pode pesquisar utilizadores individuais ou pesquisar UOs no diretório do AD com utilizadores nas mesmas.  Neste caso, iremos especificar a UO dos utilizadores.
4. Realce todos os utilizadores à direita e clique em **Importar**.  Deverá receber um pop-up a indicar que a importação foi bem-sucedida.  Feche a janela de importação.

![Nuvem](./media/multi-factor-authentication-get-started-server/import2.png)

## Enviar um e-mail aos utilizadores
Agora que importou os seus utilizadores para o servidor Multi-Factor Authentication do Azure, é aconselhado enviar aos utilizadores um e-mail a informá-los de que foram inscritos na autenticação multifator.

Com o Servidor Multi-Factor Authentication do Azure, existem várias formas de configurar os seus utilizadores para utilizarem a autenticação multifator.  Por exemplo, se souber os números de telefone dos utilizadores ou se conseguiu importar os números de telefone para o Servidor Multi-Factor Authentication do Azure a partir do diretório da empresa, o e-mail irá permitir que os utilizadores saibam que foram configurados para utilizar o Multi-Factor Authentication do Azure, fornecerá algumas instruções sobre a utilização do Multi-Factor Authentication do Azure e informará o utilizador do número de telefone no qual irá receber as autenticações.  

O conteúdo do e-mail irá variar consoante o método de autenticação definido para o utilizador (por exemplo, chamada telefónica, SMS, aplicação móvel).  Por exemplo, se for pedido ao utilizador para utilizar um PIN quando fizer a autenticação, o e-mail irá informá-lo do PIN inicial definido.  Normalmente, é pedido as utilizadores para alterarem o PIN durante a primeira autenticação.

Se os números de telefone de utilizadores não tiverem sido configurados ou importados para o Servidor Multi-Factor Authentication do Azure, ou se os utilizadores estiverem pré-configurados para utilizar a aplicação móvel de autenticação, pode enviar-lhes um e-mail informando-os de que foram configurados para utilizar o Multi-Factor Authentication do Azure e que irá direcioná-los para concluir a respetiva inscrição na conta através do Portal de Utilizador do Multi-Factor Authentication do Azure.  Será incluída uma hiperligação, na qual o utilizador clica para aceder ao Portal de Utilizador. Quando o utilizador clica na ligação, o browser abrir-se-á, direcionando-o para o Portal de Utilizador do Multi-Factor Authentication do Azure da empresa.   


### Configurar e-mail e modelos de e-mail

Ao clicar no ícone de-email à esquerda pode configurar as definições de envio destes e-mails.  É onde pode introduzir as informações de SMTP do seu servidor de correio e permite-lhe enviar um e-mail amplo aberto, marcando a caixa de verificação Enviar mails a utilizadores.

![Definições de E-mail](./media/multi-factor-authentication-get-started-server/email1.png)

No separador Conteúdo de E-mail, verá os vários modelos de e-mail que estão disponíveis para escolha.  Dependendo de como tiver configurado os seus utilizadores para utilizarem a autenticação multifator, pode escolher o modelo mais adequado.

![Modelos de E-mail](./media/multi-factor-authentication-get-started-server/email2.png)

## Como o Servidor Multi-Factor Authentication do Azure gere dados de utilização

Quando utiliza o Servidor Multi-Factor Authentication (MFA) no local, os dados de utilizador são armazenados nos servidores no local. Nenhuns dados de utilizador persistentes são armazenados na nuvem. Quando o utilizador efetua uma autenticação de dois fatores, o Servidor MFA envia dados para o serviço de nuvem do MFA do Azure para efetuar a autenticação. Quando estes pedidos de autenticação são enviados para o serviço em nuvem, os campos seguintes são enviados no pedido e nos registos para que estejam disponíveis nos relatórios de autenticação/utilização do cliente. Alguns dos campos são opcionais, pelo que podem ser ativados ou desativados no Servidor Multi-Factor Authentication. A comunicação entre o Servidor MFA e o serviço de nuvem MFA utiliza SSL/TLS através da porta 443 de saída. Estes campos são:

- ID exclusivo - nome de utilizador ou ID interno do servidor MFA
- Nome Próprio e Apelido - opcional
- Endereço de E-mail - opcional
- Número de telefone - ao efetuar uma chamada de voz ou autenticação por SMS
- Token do dispositivo - ao efetuar a autenticação da aplicação móvel
- Modo de Autenticação 
- Resultado da Autenticação 
- Nome do Servidor MFA 
- IP do Servidor MFA 
- Cliente IP - se disponível



Além dos campos anteriores, o resultado da autenticação (êxito/rejeição) e o motivo para quaisquer rejeições também são armazenados com os dados da autenticação e estão disponíveis através de relatórios de autenticação/utilização.


## Configurações Avançadas do Servidor Multi-Factor Authentication do Azure
Para obter mais informações sobre a configuração avançada e informações de configuração, utilize a tabela abaixo.

Método|Descrição
:------------- | :------------- | 
[Portal de Utilizador](multi-factor-authentication-get-started-portal.md)|  Informações sobre a configuração e como configurar o portal de Utilizador, incluindo a implementação e o utilizador self-service.
[Serviço de Federação do Active Directory](multi-factor-authentication-get-started-adfs.md)|Informações sobre a configuração do Multi-Factor Authentication do Azure com o AD FS.
[Autenticação RADIUS](multi-factor-authentication-get-started-server-radius.md)|  Informações sobre a configuração e como configurar o Servidor MFA do Azure com RADIUS.
[Autenticação do IIS](multi-factor-authentication-get-started-server-iis.md)|Informações sobre a configuração e como configurar o Servidor MFA do Azure com ISS.
[Autenticação do Windows](multi-factor-authentication-get-started-server-windows.md)|  Informações sobre a configuração e como configurar o Servidor MFA do Azure com a Autenticação do Windows.
[Autenticação LDAP](multi-factor-authentication-get-started-server-ldap.md)|Informações sobre a configuração e como configurar o Servidor MFA do Azure com a Autenticação LDAP.
[Gateway de Ambiente de Trabalho Remoto e Servidor Multi-Factor Authentication do Azure com o RADIUS](multi-factor-authentication-get-started-server-rdg.md)|  Informações sobre a configuração e como configurar o Servidor MFA do Azure com o Gateway de Ambiente de Trabalho Remoto com o RADIUS.
[Sincronizar com o Windows Server Active Directory](multi-factor-authentication-get-started-server-dirint.md)|Informações sobre a configuração e como configurar a sincronização entre o Active Directory e o Servidor MFA do Azure.
[Implementar o Serviço Web de Aplicações Móveis do Servidor Multi-Factor Authentication do Azure](multi-factor-authentication-get-started-server-webservice.md)|Informações sobre a configuração e como configurar o serviço Web do servidor MFA do Azure.



<!--HONumber=Jun16_HO2-->


