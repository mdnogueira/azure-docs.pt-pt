---
title: "Introdução ao Servidor Multi-Factor Authentication do Azure | Microsoft Docs"
description: "Esta é a página do Multi-Factor Authentication do Azure que descreve como iniciar o Servidor do MFA do Azure."
services: multi-factor-authentication
keywords: "servidor de autenticação, página de ativação da aplicação multi factor authentication do azure, transferência do servidor de autenticação"
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: e94120e4-ed77-44b8-84e4-1c5f7e186a6b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/03/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 0fec7a18e098891374b3b0d7313a72918b630918
ms.openlocfilehash: 7fb107922af9d2316fb7490670002f4255572458

---

# <a name="getting-started-with-the-azure-multi-factor-authentication-server"></a>Introdução ao Servidor Multi-Factor Authentication do Azure
<center>![MFA no local](./media/multi-factor-authentication-get-started-server/server2.png)</center>

Agora que determinamos que vamos utilizar o Servidor Multi-Factor Authentication no local, vamos começar. Esta página abrange uma nova instalação do servidor e a configuração da mesma com o Active Directory no local. Se já tiver o servidor PhoneFactor instalado e quiser atualizar, veja [Atualizar para o Servidor Multi-Factor do Azure](multi-factor-authentication-get-started-server-upgrade.md) ou, se estiver à procura de informações sobre como instalar apenas o serviço Web, veja [Implementar o Serviço Web de Aplicações Móveis do Servidor Multi-Factor Authentication do Azure](multi-factor-authentication-get-started-server-webservice.md).

## <a name="download-the-azure-multi-factor-authentication-server"></a>Transferir o Servidor Multi-Factor Authentication do Azure
Existem duas formas diferentes de transferir o Servidor Multi-Factor Authentication do Azure. Ambas são efetuadas através do portal do Azure. A primeira é através da gestão direta do Fornecedor do Multi-Factor Auth. A segunda é através de definições de serviço. A segunda opção requer um Fornecedor do Multi-Factor Auth ou uma licença MFA do Azure, Azure AD Premium ou Enterprise Mobility Suite.

> [!Important]
> Estas duas opções parecem semelhantes, mas é importante saber qual delas utilizar. Se os utilizadores tiverem licenças provenientes do MFA, não crie um Fornecedor de Multi-Factor Auth para obter a transferência do servidor. Em alternativa, utilize a opção 2 para transferir o servidor a partir da página de definições de serviço. 

### <a name="option-1-download-azure-multi-factor-authentication-server-from-the-azure-classic-portal"></a>Opção 1: Transferir o Servidor Multi-Factor Authentication do Azure a partir do portal clássico do Azure

Utilize esta opção de transferência se já tiver um Fornecedor de Multi-Factor Auth porque paga pelo MFA por utilizador ativado ou por autenticação. 

1. Inicie sessão no [portal clássico do Azure](https://manage.windowsazure.com) como administrador.
2. No lado esquerdo, selecione **Active Directory**.
3. Na página do Active Directory, clique em **Fornecedores do Multi-Factor Auth**
    ![Fornecedores do Multi-Factor Auth](./media/multi-factor-authentication-get-started-server/authproviders.png)
4. Na parte inferior, clique em **Gerir**. Uma nova página é aberta.
5. Clique em **Transferências**.
6. Clique na hiperligação **Transferir** acima de **Gerar Credenciais de Ativação**.
   ![Transferência](./media/multi-factor-authentication-get-started-server/download4.png)
7. Guarde a transferência.

### <a name="option-2-download-azure-multi-factor-authentication-server-from-the-service-settings"></a>Opção 2: Transferir o Servidor Multi-Factor Authentication do Azure a partir das definições de serviço

Utilize esta opção de transferência se tiver licenças Enterprise Mobility Suite, Azure AD Premium ou Enterprise Cloud Suite. 

1. Inicie sessão no [portal clássico do Azure](https://manage.windowsazure.com) como administrador.
2. No lado esquerdo, selecione **Active Directory**.
3. Faça duplo clique na sua instância do Azure AD.
4. Na parte superior, clique em **Configurar**
5. Desloque o ecrã para baixo até à secção **multi-factor authentication** e selecione **Gerir definições de serviço**
6. Na página de definições do serviço, na parte inferior do ecrã, clique em **Ir para o portal**. Uma nova página é aberta.
   ![Transferência](./media/multi-factor-authentication-get-started-server/servicesettings.png)
7. Clique em **Transferências.**
8. Clique na hiperligação **Transferir** acima de **Gerar Credenciais de Ativação**.
    ![Transferência](./media/multi-factor-authentication-get-started-server/download4.png)
9. Guarde a transferência.

## <a name="install-and-configure-the-azure-multi-factor-authentication-server"></a>Instalar e Configurar o Servidor Multi-Factor Authentication do Azure
Agora que transferiu o servidor, pode instalá-lo e configurá-lo.  Certifique-se de que o servidor no qual o está a instalar cumpre os seguintes requisitos:

| Requisitos do Servidor Multi-Factor Authentication do Azure | Descrição |
|:--- |:--- |
| Hardware |<li>200 MB de espaço no disco rígido</li><li>processador com capacidade de 32 ou 64 bits</li><li>1 GB de RAM ou superior</li> |
| Software |<li>Windows Server 2008 ou superior, se o anfitrião for um SO servidor</li><li>Windows 7 ou superior, se o anfitrião for um SO cliente</li><li>Microsoft .NET 4.0 Framework</li><li>O IIS 7.0 ou superior, se estiver a instalar o portal de utilizador ou o SDK do serviço Web</li> |

### <a name="azure-multi-factor-authentication-server-firewall-requirements"></a>Requisitos da firewall do Servidor Multi-Factor Authentication do Azure
- - -
Cada servidor MFA tem de conseguir comunicar na porta 443 de saída com os seguintes endereços:

* https://pfd.phonefactor.net
* https://pfd2.phonefactor.net
* https://css.phonefactor.net

Se as firewalls de saída estiverem restritas na porta 443, abra os seguintes intervalos de endereços IP:

| Subrede IP | Máscara de rede | Intervalo de IP |
|:--- |:--- |:--- |
| 134.170.116.0/25 |255.255.255.128 |134.170.116.1 – 134.170.116.126 |
| 134.170.165.0/25 |255.255.255.128 |134.170.165.1 – 134.170.165.126 |
| 70.37.154.128/25 |255.255.255.128 |70.37.154.129 – 70.37.154.254 |

Se não estiver a utilizar a funcionalidade Confirmação de Eventos e os utilizadores não estiverem a utilizar aplicações móveis para verificar a partir de dispositivos na rede da empresa, os endereços IP podem ser reduzidos para os seguintes intervalos:

| Subrede IP | Máscara de rede | Intervalo de IP |
|:--- |:--- |:--- |
| 134.170.116.72/29 |255.255.255.248 |134.170.116.72 – 134.170.116.79 |
| 134.170.165.72/29 |255.255.255.248 |134.170.165.72 – 134.170.165.79 |
| 70.37.154.200/29 |255.255.255.248 |70.37.154.201 – 70.37.154.206 |

### <a name="to-install-and-configure-the-azure-multi-factor-authentication-server"></a>Para instalar e configurar o servidor Multi-Factor Authentication do Azure

1. Faça duplo clique no executável. Isto iniciará a instalação.
2. No ecrã Selecionar Pasta de Instalação, certifique-se de que a pasta está correta e clique em **Seguinte**.
3. Quando concluir a instalação, clique em **Concluir**.  O assistente de configuração é iniciado.
4. No ecrã de boas-vindas do assistente de configuração, assinale **Não utilizar o Assistente de Configuração da Autenticação** e clique em **Seguinte**.  Isto fecha o assistente e inicia o servidor.
    ![Cloud](./media/multi-factor-authentication-get-started-server/skip2.png)
5. Na página a partir da qual transferimos o servidor, clique no botão **Gerar Credenciais de Ativação**. Copie estas informações para o servidor MFA do Azure nas caixas fornecidas e clique em **Ativar**.

Os passos acima mostram uma configuração rápida com o assistente de configuração.  Pode executar novamente o assistente de autenticação, selecionando-o no menu Ferramentas do servidor.

## <a name="import-users-from-active-directory"></a>Importar utilizadores do Active Directory
Agora que o servidor está instalado e configurado, pode rapidamente importar utilizadores para o Servidor MFA no Azure.

1. No Servidor MFA do Azure, à esquerda, selecione **Utilizadores**.
2. Na parte inferior, selecione **Importar do Active Directory**.
3. Agora pode pesquisar utilizadores individuais ou pesquisar UOs no diretório do AD com utilizadores nas mesmas.  Neste caso, especificamos a UO dos utilizadores.
4. Realce todos os utilizadores à direita e clique em **Importar**.  Deverá receber um pop-up a indicar que a importação foi bem-sucedida.  Feche a janela de importação.

![Nuvem](./media/multi-factor-authentication-get-started-server/import2.png)

## <a name="send-users-an-email"></a>Enviar uma mensagem de e-mail aos utilizadores
Agora que importou os seus utilizadores para o Servidor MFA, recomendamos que envie uma mensagem de e-mail a informá-los que foram inscritos na verificação de dois passos.

A mensagem de e-mail que envia deve ser determinada através de como configurou os seus utilizadores para a verificação de dois passos. Por exemplo, se conseguiu importar os números de telefone dos seus utilizadores a partir do diretório da empresa, a mensagem de e-mail deve incluir os números de telefone predefinidos, de modo a que os utilizadores saibam o que esperar. Do mesmo modo, se os números de telefone dos utilizadores não tiverem sido importados ou os utilizadores estejam configurados para utilizar a aplicação móvel, envie-lhes uma mensagem de e-mail que os direcione para concluírem a inscrição da respetiva conta através de uma hiperligação para o Portal de Utilizador Multi-Factor Authentication do Azure.

O conteúdo da mensagem de e-mail também varia consoante o método de verificação definido para o utilizador (chamada telefónica, SMS ou aplicação móvel).  Por exemplo, se for exigido ao utilizador que utilize um PIN quando fizer a autenticação, a mensagem de e-mail irá informá-lo de que o PIN inicial foi definido.  É pedido as utilizadores que alterem o PIN durante a primeira verificação.


### <a name="configure-email-and-email-templates"></a>Configurar o e-mail e modelos de e-mail
Clique no ícone de e-mail à esquerda para configurar as definições de envio destas mensagens de e-mail. É aqui que pode introduzir as informações de SMTP do seu servidor de correio e enviar mensagens de e-mail, assinalando a caixa de verificação **Enviar e-mails aos utilizadores**.

![Definições de E-mail](./media/multi-factor-authentication-get-started-server/email1.png)

No separador Conteúdo do E-mail, poderá ver os modelos de e-mail que estão disponíveis para escolha. Dependendo de como configurou os seus utilizadores para realizarem a verificação de dois passos, escolha o modelo mais adequado.

![Modelos de E-mail](./media/multi-factor-authentication-get-started-server/email2.png)

## <a name="how-the-azure-multi-factor-authentication-server-handles-user-data"></a>Como o Servidor Multi-Factor Authentication do Azure gere dados de utilização
Quando utiliza o Servidor Multi-Factor Authentication (MFA) no local, os dados de utilizador são armazenados nos servidores no local. Nenhuns dados de utilizador persistentes são armazenados na nuvem. Quando o utilizador executa uma verificação de dois passos, o Servidor MFA envia dados para o serviço cloud do MFA do Azure para executar a verificação. Quando estes pedidos de autenticação são enviados para o serviço em nuvem, os campos seguintes são enviados no pedido e nos registos para que estejam disponíveis nos relatórios de autenticação/utilização do cliente. Alguns dos campos são opcionais, pelo que podem ser ativados ou desativados no Servidor Multi-Factor Authentication. A comunicação entre o Servidor MFA e o serviço de nuvem MFA utiliza SSL/TLS através da porta 443 de saída. Estes campos são:

* ID exclusivo - nome de utilizador ou ID interno do servidor MFA
* Nome próprio e apelido (opcional)
* Endereço de e-mail (opcional)
* Número de telefone - ao realizar uma chamada de voz ou autenticação por SMS
* Token do dispositivo - ao efetuar a autenticação da aplicação móvel
* Modo de autenticação
* Resultado da autenticação
* Nome do Servidor MFA
* IP do Servidor MFA
* Cliente IP - se disponível

Além dos campos anteriores, o resultado da verificação (êxito/rejeição) e o motivo para quaisquer rejeições também são armazenados com os dados da autenticação e estão disponíveis através de relatórios de autenticação/utilização.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a configuração avançada e informações de configuração, utilize as hiperligações na tabela seguinte:

| Método | Descrição |
|:--- |:--- |
| [Portal de Utilizador](multi-factor-authentication-get-started-portal.md) |Informações sobre a configuração e como configurar o portal de Utilizador, incluindo a implementação e o utilizador self-service. |
| [Serviço de Federação do Active Directory](multi-factor-authentication-get-started-adfs.md) |Informações sobre a configuração do Multi-Factor Authentication do Azure com o AD FS. |
| [Autenticação RADIUS](multi-factor-authentication-get-started-server-radius.md) |Informações sobre a configuração e como configurar o Servidor MFA do Azure com RADIUS. A utilização de RADIUS permite-lhe integrar vários sistemas de terceiros no Servidor MFA do Azure. |
| [Autenticação do IIS](multi-factor-authentication-get-started-server-iis.md) |Informações sobre a configuração e como configurar o Servidor MFA do Azure com ISS. A utilização de IIS permite-lhe integrar vários sistemas de terceiros no Servidor MFA do Azure. |
| [Autenticação do Windows](multi-factor-authentication-get-started-server-windows.md) |Informações sobre a configuração e como configurar o Servidor MFA do Azure com a Autenticação do Windows. |
| [Autenticação LDAP](multi-factor-authentication-get-started-server-ldap.md) |Informações sobre a configuração e como configurar o Servidor MFA do Azure com a Autenticação LDAP. A utilização de LDAP permite-lhe integrar vários sistemas de terceiros no Servidor MFA do Azure. |
| [Gateway de Ambiente de Trabalho Remoto e Servidor Multi-Factor Authentication do Azure com o RADIUS](multi-factor-authentication-get-started-server-rdg.md) |Informações sobre a configuração e como configurar o Servidor MFA do Azure com o Gateway de Ambiente de Trabalho Remoto com o RADIUS. |
| [Sincronizar com o Windows Server Active Directory](multi-factor-authentication-get-started-server-dirint.md) |Informações sobre a configuração e como configurar a sincronização entre o Active Directory e o Servidor MFA do Azure. |
| [Implementar o Serviço Web de Aplicações Móveis do Servidor Multi-Factor Authentication do Azure](multi-factor-authentication-get-started-server-webservice.md) |Informações sobre a configuração e como configurar o serviço Web do servidor MFA do Azure. |
| [Cenários avançados com Multi-Factor Authentication do Azure e VPNs de terceiros](multi-factor-authentication-advanced-vpn-configurations.md) | Guias de configuração passo a passo para aparelhos VPN Cisco, Citrix e Juniper. |



<!--HONumber=Jan17_HO1-->


