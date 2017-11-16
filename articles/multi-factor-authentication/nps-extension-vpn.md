---
title: "Integrar VPN com a MFA do Azure utilizando a extensão de servidor de políticas de rede | Microsoft Docs"
description: "Este artigo aborda integrar a sua infraestrutura de VPN com a MFA do Azure utilizando a extensão de servidor de políticas de rede do Microsoft Azure."
services: active-directory
keywords: "Azure MFA, integrar VPN do Azure Active Directory, a extensão de servidor de políticas de rede"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: bf83952b29759ff601685513ace31828404c7b1a
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Integrar a sua infraestrutura de VPN com a MFA do Azure utilizando a extensão de servidor de políticas de rede do Azure

## <a name="overview"></a>Descrição geral

A extensão de servidor de políticas de rede (NPS) para o Azure permite que as organizações a salvaguardar a autenticação de cliente RADIUS Remote Authentication Dial-In User Service () com baseado na nuvem [Azure multi-factor Authentication (MFA)](multi-factor-authentication-get-started-server-rdg.md), que fornece verificação em dois passos.

Este artigo fornece instruções para integrar a infraestrutura NPS com a MFA, utilizando a extensão NPS para o Azure. Este processo permite que a verificação de dois passos segura para os utilizadores que tentam estabelecer ligação à sua rede através da utilização de uma VPN. 

Serviços de acesso de política de rede e permite que as organizações a:

* Atribua uma localização central para a gestão e o controlo de pedidos de rede para especificar:

    * Quem pode ligar 
    
    * São permitidos os tempos de ligações de dia 
    
    * A duração de ligações
    
    * O nível de segurança que os clientes devem utilizar para ligar

    Em vez de especificar políticas em cada servidor VPN ou Gateway de ambiente de trabalho remoto, fazê após estiverem numa localização central. O protocolo RADIUS é utilizado para fornecer autenticação, autorização e gestão de contas (AAA) centralizada. 

* Estabelecer e impor políticas de estado de funcionamento de cliente de proteção de acesso de rede (NAP) determinam se dispositivos são concedidos o acesso restrito ou sem restrições aos recursos de rede.

* Fornecer uma forma para impor a autenticação e autorização para aceder aos 802.1 pontos de acesso sem fios com capacidade de x e comutadores de Ethernet.   
Para obter mais informações, consulte [servidor de políticas de rede](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top). 

Para melhorar a segurança e fornecem um elevado nível de compatibilidade, as organizações podem integrar NPS Azure multi-factor Authentication para se certificar de que os utilizadores utilizar a verificação de dois passos para ligar à porta virtual no servidor VPN. Para os utilizadores ser concedido acesso, têm de fornecer o nome de utilizador e a combinação de palavra-passe e outras informações que controlarem. Estas informações tem de ser fidedigna e não facilmente duplicadas. Pode incluir um número de telemóvel, um número fixo ou uma aplicação num dispositivo móvel.

Antes da disponibilidade da extensão NPS para o Azure, os clientes que pretendem implementar a verificação de dois passos para integrado NPS e ambientes de MFA tinham que configurar e manter o servidor MFA separado num ambiente no local. Este tipo de autenticação é oferecido pelo Gateway de ambiente de trabalho remoto e o servidor de autenticação do multi-factor do Azure com o RADIUS.

Com a extensão NPS para o Azure, as organizações podem proteger autenticação de cliente RADIUS ao implementar uma solução MFA com base no local ou uma solução MFA baseado na nuvem.
 
## <a name="authentication-flow"></a>Fluxo de autenticação
Quando os utilizadores ligam a uma porta virtual no servidor VPN, devem primeiro autenticar utilizando uma variedade de protocolos. Os protocolos de permitem a utilização de uma combinação de nome de utilizador e palavra-passe e métodos de autenticação baseada em certificado. 

Para além de autenticação e a verificar a sua identidade, os utilizadores tem de ter as permissões de acesso telefónico adequadas. Implementações simples, acesso telefónico definir as permissões que permitem acesso são diretamente nos objetos de utilizador do Active Directory. 

![Propriedades do utilizador](./media/nps-extension-vpn/image1.png)

Implementações simples, cada servidor VPN concede ou nega o acesso com base nas políticas que estão definidas em cada servidor VPN local.

Em implementações maiores e mais dimensionáveis, as políticas que conceder ou negarem acesso VPN são centralizadas em servidores RADIUS. Nestes casos, o servidor VPN atua como um servidor de acesso (cliente RADIUS), que reencaminha os pedidos de ligação e mensagens de conta para um servidor RADIUS. Para ligar à porta virtual no servidor VPN, os utilizadores têm de ser autenticados e cumprem as condições que são definidas centralmente nos servidores RADIUS. 

Quando a extensão NPS para o Azure está integrada com o NPS, um fluxo de autenticação com êxito resulta, da seguinte forma:

1. O servidor VPN recebe um pedido de autenticação de um utilizador VPN que inclui o nome de utilizador e palavra-passe para ligar a um recurso, tal como uma sessão de ambiente de trabalho remoto. 

2. A atuar como um cliente RADIUS, o servidor VPN converte o pedido para um RADIUS *pedido de acesso* mensagem e envia-(com uma palavra-passe encriptada) para o servidor RADIUS onde está instalada a extensão NPS. 

3. A combinação de nome de utilizador e palavra-passe é verificada no Active Directory. Se o nome de utilizador ou palavra-passe está incorreta, o servidor RADIUS envia um *acesso rejeitar* mensagem. 

4. Se forem cumpridas todas as condições, conforme especificado no pedido de ligação de NPS e políticas de rede (por exemplo, a hora do dia ou grupo de restrições de associação), a extensão NPS aciona um pedido de autenticação secundária com multi-factor Authentication do Azure. 

5. Multi-factor Authentication do Azure comunica com o Azure Active Directory, obtém os detalhes do utilizador e efetua a autenticação secundária utilizando o método que é configurado pelo utilizador (chamada telefónica de célula, mensagem de texto, aplicação móvel ou). 

6. Quando o desafio MFA for bem sucedido, o Azure multi-factor Authentication comunica o resultado para a extensão NPS.

7. Depois da tentativa de ligação é autenticada e autorizada, o NPS onde está instalada a extensão envia um RADIUS *aceitar o acesso* mensagem para o servidor VPN (cliente RADIUS).

8. O utilizador é concedido acesso à porta virtual no servidor VPN e estabelece um túnel VPN encriptado.

## <a name="prerequisites"></a>Pré-requisitos
Esta secção fornece detalhes sobre os pré-requisitos que devem ser concluídos antes de pode integrar MFA com o Gateway de ambiente de trabalho remoto. Antes de começar, tem de ter os seguintes pré-requisitos no local:

* Infraestrutura de VPN
* Função Serviços de acesso e política de rede
* Licença de multi-factor Authentication do Azure
* Software Windows Server
* Bibliotecas
* Azure Active Directory (Azure AD) sincronizadas com no local do Active Directory 
* ID de GUID do Azure Active Directory

### <a name="vpn-infrastructure"></a>Infraestrutura de VPN
Este artigo pressupõe que tem uma infraestrutura VPN de trabalho que utiliza o Microsoft Windows Server 2016 e que o servidor VPN não estão configurado para pedidos de ligação direta para um servidor RADIUS. O artigo, deve configurar a infraestrutura VPN para utilizar um servidor RADIUS central.

Se não tiver uma infraestrutura VPN de trabalho no local, pode criar rapidamente um ao seguir as orientações nos vários tutoriais de configuração VPN que pode encontrar no Microsoft e sites de terceiros. 
            
### <a name="the-network-policy-and-access-services-role"></a>A função Serviços de acesso e política de rede

Serviços de acesso de política de rede e fornece a funcionalidade de servidor e cliente RADIUS. Este artigo pressupõe que já instalou a função Serviços de acesso e política de rede num servidor membro ou controlador de domínio no seu ambiente. Neste guia, configure RADIUS para uma configuração de VPN. Instalar a função Serviços de acesso e política de rede num servidor *diferente de* seu servidor VPN.

Para obter informações sobre como instalar a função Serviços de acesso e política de rede do serviço do Windows Server 2012 ou posterior, consulte [instalar um servidor de políticas de estado de funcionamento de NAP](https://technet.microsoft.com/library/dd296890.aspx). NAP foi preterida no Windows Server 2016. Para obter uma descrição das melhores práticas para NPS, incluindo a recomendação para instalar NPS num controlador de domínio, consulte [melhores práticas para NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-mfa-license"></a>Licença do MFA do Azure

É necessária uma licença para o multi-factor Authentication do Azure e está disponível através de um Azure AD Premium, o Enterprise Mobility + segurança ou uma subscrição de multi-factor Authentication. Com base no consumo de licenças do MFA do Azure, como por utilizador ou por licenças de autenticação não são compatíveis com a extensão NPS. Para obter mais informações, consulte [como obter o Azure multi-factor Authentication](multi-factor-authentication-versions-plans.md). Para fins de teste, pode utilizar uma subscrição de avaliação.

### <a name="windows-server-software"></a>Software Windows Server

A extensão NPS requer o Windows Server 2008 R2 SP1 ou posterior, com a função Serviços de acesso e política de rede instalados. Todos os passos neste guia foram executados com o Windows Server 2016.

### <a name="libraries"></a>Bibliotecas

As bibliotecas seguintes são automaticamente instaladas com a extensão NPS:

-   [Visual C++ Redistributable Packages for Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Microsoft Azure módulo Active Directory para Windows PowerShell versão 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Se o Microsoft Azure módulo Active Directory PowerShell já não estiver presente, é instalado com um script de configuração que é executado como parte do processo de configuração. Não é necessário para instalar o módulo antecedência se não estiver já instalado.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Azure Active Directory sincronizado com o Active Directory no local 

Para utilizar a extensão NPS, os utilizadores no local tem de ser sincronizados com o Azure Active Directory e ativados para a MFA. Este guia pressupõe que os utilizadores no local são sincronizados com o Azure Active Directory através do Azure AD Connect. São fornecidas instruções para permitir que os utilizadores para a MFA abaixo.

Para obter informações sobre o Azure AD Connect, consulte [integrar os diretórios no local ao Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md). 

### <a name="azure-active-directory-guid-id"></a>ID de GUID do Azure Active Directory 

Para instalar a extensão NPS, terá de saber o GUID do Azure Active Directory. Na secção seguinte, são fornecidas instruções para localizar o GUID do Azure Active Directory.

## <a name="configure-radius-for-vpn-connections"></a>Configurar RADIUS para ligações VPN

Se instalou a função NPS no servidor membro, terá de configurá-lo para autenticar e autorizar o cliente VPN se as ligações VPN de pedidos. 

Esta secção assume que instalou a função Serviços de acesso e política de rede, mas não tiver configurado-lo para utilização na sua infraestrutura.

> [!NOTE]
> Se já tiver um servidor VPN de trabalho que utiliza um servidor RADIUS centralizado para autenticação, pode ignorar esta secção.
>

### <a name="register-server-in-active-directory"></a>Registar servidor no Active Directory
Para funcionarem corretamente neste cenário, o servidor NPS tem de ser registado no Active Directory.

1. Abra o Gestor de servidor.

2. No Gestor de servidor, selecione **ferramentas**e, em seguida, selecione **servidor de políticas de rede**. 

3. Na consola do servidor de políticas de rede, faça duplo clique **NPS (Local)**e, em seguida, selecione **registar servidor no Active Directory**. Selecione **OK** duas vezes.

    ![Servidor de políticas de rede](./media/nps-extension-vpn/image2.png)

4. Deixe a consola aberta para o procedimento seguinte.

### <a name="use-wizard-to-configure-the-radius-server"></a>Utilize o Assistente para configurar o servidor RADIUS
Pode utilizar um padrão (baseada em Assistente) ou avançadas a opção de configuração para configurar o servidor RADIUS. Esta secção assume que está a utilizar a opção de configuração padrão com base no assistente.

1. Na consola do servidor de políticas de rede, selecione **NPS (Local)**.

2. Em **configuração padrão**, selecione **servidor RADIUS para ligações VPN ou de marcação telefónica**e, em seguida, selecione **configurar VPN ou de marcação telefónica**.

    ![Configurar a VPN](./media/nps-extension-vpn/image3.png)

3. No **selecione o tipo de ligações de rede privada Virtual ou de marcação telefónica** janela, selecione **ligações de rede privada Virtual**e, em seguida, selecione **seguinte**.

    ![Rede privada virtual](./media/nps-extension-vpn/image4.png)

4. No **especificar acesso telefónico ou VPN servidor** janela, selecione **adicionar**.

5. No **cliente RADIUS novo** janela, forneça um nome amigável, introduza o nome resolúvel ou endereço IP do servidor VPN e, em seguida, introduza uma palavra-passe secreta partilhada.  
    Tornar a partilhado secreta palavra-passe e complexa. Registe, porque terá na secção seguinte.

    ![Novo cliente RADIUS](./media/nps-extension-vpn/image5.png)

6. Selecione **OK**e, em seguida, selecione **seguinte**.

7. No **configurar métodos de autenticação** janela, aceite a seleção predefinida (**Microsoft encriptados autenticação versão 2 [MS-CHAPv2])** ou escolha outra opção e selecione **seguinte** .

    > [!NOTE]
    > Se configurar o protocolo de autenticação extensível (EAP), tem de utilizar o Microsoft Challenge Handshake Authentication Protocol (CHAPv2) ou autenticação protocolo PEAP (Protected Extensible). Não existem outro EAP é suportada.
 
8. No **especificar grupos de utilizadores** janela, selecione **adicionar**e, em seguida, selecione um grupo adequado.  
    Não se existir nenhum grupo, deixe a seleção em branco para conceder acesso a todos os utilizadores.

    ![A janela de especificar grupos de utilizadores](./media/nps-extension-vpn/image7.png)

9. Selecione **seguinte**.

10. No **especificar filtros de IP** janela, selecione **seguinte**.

11. No **especificar definições de encriptação** janela, aceite as predefinições e, em seguida, selecione **seguinte**.

    ![A janela de especificar as definições de encriptação](./media/nps-extension-vpn/image8.png)

12. No **Especifique um nome do Realm** janela, deixe o nome do realm em branco, aceite a predefinição e, em seguida, selecione **seguinte**.

    ![As especifique uma janela de nome do Realm](./media/nps-extension-vpn/image9.png)

13. No **clientes telefónico concluir novo ou ligações de rede privada Virtual e RADIUS** janela, selecione **concluir**.

    ![A janela de "clientes concluir novo acesso telefónico ou ligações de rede privada Virtual e RADIUS"](./media/nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>Verificar a configuração RADIUS
Esta secção fornece detalhes sobre a configuração que criou utilizando o assistente.

1. No servidor de políticas de rede, na consola do NPS (local), expanda **clientes RADIUS**e, em seguida, selecione **clientes RADIUS**.

2. No painel de detalhes, clique no cliente RADIUS que criou e, em seguida, selecione **propriedades**.  
    As propriedades do cliente RADIUS (o servidor VPN) devem ser, como os que são mostradas aqui:

    ![Propriedades VPN](./media/nps-extension-vpn/image11.png)

3. Selecione **Cancelar**.

4. No servidor de políticas de rede, na consola do NPS (local), expanda **políticas**e, em seguida, selecione **políticas de pedido de ligação**.  
    A política de ligações VPN é apresentada, conforme mostrado na imagem seguinte:

    ![Pedidos de ligação](./media/nps-extension-vpn/image12.png)

5. Em **políticas**, selecione **políticas de rede**.  
    Deverá ver uma política de ligações de rede privada Virtual (VPN) que se assemelha a política mostrada na imagem seguinte:

    ![Políticas de rede](./media/nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>Configurar o seu servidor VPN para utilizar a autenticação RADIUS
Nesta secção, configure o seu servidor VPN para utilizar a autenticação RADIUS. As instruções partem do princípio de que tiver uma configuração de um servidor VPN, mas não tiver configurado para utilizar a autenticação RADIUS. Depois de configurar o servidor VPN, confirme que a configuração está a funcionar conforme esperado.

> [!NOTE]
> Se já tiver uma configuração de servidor VPN que utiliza a autenticação RADIUS, pode ignorar esta secção.
>

### <a name="configure-authentication-provider"></a>Configurar o fornecedor de autenticação
1. No servidor VPN, abra o Gestor de servidor.

2. No Gestor de servidor, selecione **ferramentas**e, em seguida, selecione **encaminhamento e acesso remoto**.

3. No **encaminhamento e acesso remoto** janela, clique com botão direito  **\<nome do servidor > (local)**e, em seguida, selecione **propriedades**.

    ![O período de encaminhamento e acesso remoto](./media/nps-extension-vpn/image14.png)
 
4. No  **\<nome do servidor > propriedades (locais)** janela, selecione o **segurança** separador. 

5. No **segurança** separador em **fornecedor de autenticação**, selecione **autenticação RADIUS**e, em seguida, selecione **configurar**.

    ![Autenticação RADIUS](./media/nps-extension-vpn/image15.png)
 
6. No **autenticação RADIUS** janela, selecione **adicionar**.

7. No **Adicionar servidor RADIUS** janela, efetue o seguinte procedimento:

    a. No **nome do servidor** box, introduza o nome ou endereço IP do servidor RADIUS que configurou na secção anterior.

    b. Para o **segredo partilhado**, selecione **alteração**e, em seguida, introduza o partilhado secreta palavra-passe que criou e registou anteriormente.

    c. No **tempo limite (segundos)** caixa, selecione um valor **30** através de **60**.  
    O valor de tempo limite é necessário para permitir tempo suficiente concluir o segundo fator de autenticação.
 
    ![A janela de adicionar servidor RADIUS](./media/nps-extension-vpn/image16.png)
 
8. Selecione **OK**.

### <a name="test-vpn-connectivity"></a>Testar a conectividade VPN
Nesta secção, confirme que o cliente VPN é autenticado e autorizado pelo servidor RADIUS, quando tentar estabelecer ligação com a porta virtual da VPN. As instruções partem do princípio de que está a utilizar o Windows 10 como um cliente VPN. 

> [!NOTE]
> Se já tiver configurado um cliente VPN para estabelecer ligação ao servidor VPN e guardar as definições, pode ignorar os passos relacionadas com a configurar e guardar um objeto de ligação VPN.
>

1. No seu computador de cliente VPN, selecione o **iniciar** botão e, em seguida, selecione o **definições** botão.

2. No **definições do Windows** janela, selecione **rede e Internet**.

3. Selecione **VPN**.

4. Selecione **adicionar uma ligação VPN**.

5. No **adicionar uma ligação VPN** janela, no **fornecedor VPN** caixa, selecione **Windows (incorporada)**, preencha os campos restantes, conforme adequado e, em seguida, selecione **Guardar**. 

    ![A janela "Adicionar uma ligação VPN"](./media/nps-extension-vpn/image17.png)
 
6. Aceda a **painel de controlo**e, em seguida, selecione **Centro de partilha de rede e**.

7. Selecione **alterar as definições da placa**.

    ![Alterar definições do adaptador](./media/nps-extension-vpn/image18.png)

8. Faça duplo clique na ligação de rede VPN e, em seguida, selecione **propriedades**. 

    ![Propriedades de rede VPN](./media/nps-extension-vpn/image19.png)

9. Na janela de propriedades de VPN, selecione o **segurança** separador. 

10. No **segurança** separador, certifique-se de que apenas **Microsoft CHAP Version 2 (MS-CHAP v2)** está selecionado e, em seguida, selecione **OK**.

    ![A opção "Permitir que estes protocolos"](./media/nps-extension-vpn/image20.png)

11. Faça duplo clique na ligação VPN e, em seguida, selecione **Connect**.

12. No **definições** janela, selecione **Connect**.  
    É apresentada uma ligação com êxito no registo de segurança no servidor RADIUS como 6272 de ID de evento, conforme mostrado aqui:

    ![A janela de propriedades do evento](./media/nps-extension-vpn/image21.png)

## <a name="troubleshooting-guide"></a>Guia de resolução de problemas
Partem do princípio de que a configuração de VPN estava a funcionar antes de configurar o servidor VPN para utilizar um servidor RADIUS centralizado para autenticação e autorização. Se a configuração estava a funcionar, é provável que o problema é causado por uma configuração incorreta do servidor RADIUS ou a utilização de uma palavra-passe ou nome de utilizador inválido. Por exemplo, se utilizar o sufixo UPN alternativo no nome de utilizador, a tentativa de início de sessão poderá falhar. Utilize o mesmo nome de conta para obter os melhores resultados. 

Para resolver estes problemas, um local ideal para começar, é examinar os registos de eventos de segurança no servidor RADIUS. Para guardar o tempo a procurar eventos, pode utilizar a baseada em funções política de rede e servidor de acesso vista personalizada no Visualizador de eventos, conforme mostrado aqui. "ID de evento 6273" indica onde o NPS negado o acesso a um utilizador de eventos. 

![Visualizador de eventos](./media/nps-extension-vpn/image22.png)
 
## <a name="configure-multi-factor-authentication"></a>Configurar a autenticação multifator
Esta secção fornece instruções para permitir que os utilizadores para a MFA e para configurar contas para verificação de dois passos. 

### <a name="enable-multi-factor-authentication"></a>Ativar a autenticação multifator
Nesta secção, ativar a contas do Azure AD para o MFA. Utilize o portal clássico do Azure para permitir que os utilizadores para a MFA. 

1. Vá para o [Microsoft Azure](https://manage.windowsazure.com) Web site. 

2. Inicie sessão como administrador.

3. No painel esquerdo, selecione **do Active Directory**.

    ![Diretório predefinido](./media/nps-extension-vpn/image23.png)

4. No **nome** coluna, selecione **diretório predefinido** (ou outro diretório, se apropriado).

5. No **diretório predefinido** janela, selecione **configurar**.

    ![Configurar o diretório predefinido](./media/nps-extension-vpn/image24.png)

6. No **configurar** janela em **autenticação multifator**, selecione **gerir definições do serviço**.

    ![Gerir definições de autenticação multifator](./media/nps-extension-vpn/image25.png)
 
7. No **autenticação multifator** janela, reveja a predefinição definições do serviço e, em seguida, selecione o **utilizadores** separador. 

    ![O separador de utilizadores de autenticação multifator](./media/nps-extension-vpn/image26.png)
 
8. No **utilizadores** separador, selecione os utilizadores a quem pretende ativar a MFA e, em seguida, selecione **ativar**.

    ![Propriedades](./media/nps-extension-vpn/image27.png)
 
9. Quando lhe for pedido, selecione **ativar autenticação multifator**.

    ![Ativar a autenticação multifator](./media/nps-extension-vpn/image28.png)
 
10. Selecione **fechar**. 

11. Atualize a página.  
    O estado da autenticação multifator é alterado para *ativado*.

Para obter informações sobre como permitir que os utilizadores para a MFA, consulte [introdução ao Azure multi-factor Authentication na nuvem](multi-factor-authentication-get-started-cloud.md). 

### <a name="configure-accounts-for-two-step-verification"></a>Configurar contas para verificação de dois passos
Depois de uma conta tiver sido ativada para a MFA, os utilizadores não conseguem iniciar sessão em recursos que são regulados pela política da MFA até configuradas com êxito um dispositivo fidedigno para utilizar para o segundo fator de autenticação.

Nesta secção, é possível configurar um dispositivo fidedigno para utilização com verificação de dois passos. Tem várias opções de dispositivo, incluindo o seguinte:

* **Aplicação móvel**: instalar a aplicação Authenticator da Microsoft num dispositivo Windows Phone, Android ou iOS. Consoante as políticas da sua organização, tem de utilizar a aplicação dos dois modos: 
    * Receba notificações para verificações (será enviada uma notificação para o seu dispositivo).
    * Utilize um código de verificação (é necessário introduzir um código de verificação que atualiza a cada 30 segundos). 

* **Chamada de telemóvel ou texto**: pode receber uma chamada telefónica automática ou uma mensagem de texto. Com a opção de chamada telefónica, responda a chamada e selecione o sinal de cardinal (#) para se autenticar. Com a opção de texto, pode responder à mensagem de texto ou introduza o código de verificação na interface de início de sessão.

* **Office Telefonema**: Este processo é o mesmo que o processo descrito anteriormente para automatizada chamadas telefónicas.

Para configurar um dispositivo para utilizar a aplicação móvel para receber a notificação push para a verificação, efetue o seguinte:

1. Inicie sessão no [Microsoft Azure](https://aka.ms/mfasetup) ou de qualquer site, tais como o [portal do Azure](https://portal.azure.com), nos quais está a necessário para autenticar com as suas credenciais de ativar a MFA.  
    Precisará de configurar a conta para a verificação de segurança adicional, conforme mostrado aqui:

    ![Segurança adicional](./media/nps-extension-vpn/image29.png)

2. Selecione **configurá-lo agora**.

3. No **verificação adicional de segurança** janela, selecione um contacto escreva (**telefone de autenticação**, **telefone do escritório**, ou **aplicação móvel** ), selecione um país ou região e, em seguida, selecione um método. Não selecione **contactar-me** ainda.  
    O método varia consoante o tipo de contacto. Por exemplo, se escolher **aplicação móvel**, pode selecionar se pretende receber notificações de verificação ou utilizar um código de verificação. 

    ![A janela de "verificação adicional de segurança"](./media/nps-extension-vpn/image30.png)

    Os passos que se seguem partem do princípio de que escolheu **aplicação móvel** como o tipo de contacto.

4. Selecione **aplicação móvel**, selecione **receber notificações de verificação**e, em seguida, selecione **configurar**. 

    ![A janela de "verificação adicional de segurança"](./media/nps-extension-vpn/image31.png)
 
5. Se não tiver o feito, instale a aplicação móvel Microsoft Authenticator no seu dispositivo. 

6. Na aplicação móvel, digitalizar o código de barras apresentado ou introduzir manualmente as informações e, em seguida, selecione **feito**.

    ![Configurar aplicação móvel Microsoft Authenticator](./media/nps-extension-vpn/image32.png)

7. No **verificação adicional de segurança** janela, selecione **contactar-me**e, em seguida, responder à notificação que é enviada para o seu dispositivo.

8. No **verificação adicional de segurança** janela em **passo 3: no caso de perder o acesso para a aplicação móvel**, introduza um número de contacto para chamar se perder o acesso para a aplicação móvel e, em seguida, selecione  **Seguinte**.

    ![A janela de "verificação adicional de segurança"](./media/nps-extension-vpn/image33.png)
 
9. No **verificação adicional de segurança** janela, selecione **feito**.

O dispositivo está agora configurado para fornecer um segundo método de verificação. Para obter informações sobre como configurar contas para verificação de dois passos, consulte [configurar a minha conta para a verificação de dois passos](./end-user/multi-factor-authentication-end-user-first-time.md).

## <a name="install-and-configure-the-nps-extension"></a>Instalar e configurar a extensão NPS

Esta secção fornece instruções para configurar a VPN para utilizar a MFA para autenticação de cliente com o servidor VPN.

Depois de instalar e configurar a extensão NPS, toda a autenticação com base no RADIUS cliente que é processada por este servidor é necessário utilizar a MFA. Se todos os utilizadores VPN não estão inscritos no multi-factor Authentication do Azure, pode efetuar um dos seguintes procedimentos:

* Configure outro servidor RADIUS para autenticar utilizadores que não estão configurados para utilizar a MFA. 

* Crie uma entrada de registo que permite aos utilizadores for fornecer um segundo fator de autenticação, se estiverem inscritos no Azure multi-factor Authentication. 

Criar um novo valor de cadeia denominado _REQUIRE_USER_MATCH no HKLM\SOFTWARE\Microsoft\AzureMfa_e defina o valor *verdadeiro* ou *falso*. 

![A definição de "Exigir correspondência do utilizador"](./media/nps-extension-vpn/image34.png)
 
Se o valor estiver definido como *verdadeiro* ou está em branco, todos os pedidos de autenticação estão sujeitos a um desafio MFA. Se o valor estiver definido como *falso*, desafios MFA forem emitidos apenas a utilizadores que estão inscritos no Azure multi-factor Authentication. Utilize o *falso* definir apenas no teste ou em ambientes de produção durante um período de integração.

### <a name="obtain-the-azure-active-directory-guid-id"></a>Obter o ID de GUID do Azure Active Directory

Como parte da configuração da extensão NPS, tem de fornecer credenciais de administrador e o ID de inquilino do Azure AD. Obter o ID da seguinte forma:

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com) como administrador global do inquilino do Azure.

2. No painel esquerdo, selecione o **do Azure Active Directory** botão.

3. Selecione **propriedades**.

4. Para copiar o seu ID de AD do Azure, selecione o **cópia** botão.
 
    ![O ID do Azure AD](./media/nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>Instale a extensão NPS
A extensão NPS deve ser instalada num servidor que tenha a política de rede e a função Serviços de acesso instalada e que funções como servidor RADIUS na sua estrutura. Efetue *não* instale a extensão do NPS no servidor do ambiente de trabalho remoto.

1. Transfira a extensão NPS do [Microsoft Download Center](https://aka.ms/npsmfa). 

2. Copie o ficheiro executável do programa de configuração (*NpsExtnForAzureMfaInstaller.exe*) para o servidor NPS.

3. No servidor NPS, faça duplo clique em **NpsExtnForAzureMfaInstaller.exe** e, se lhe for pedido, selecione **executar**.

4. No **NPS extensão para o Azure MFA configuração** janela, reveja os termos de licenciamento de software, selecione o **concordo com os termos de licenciamento e as condições** caixa de verificação e, em seguida, selecione **instalar**.

    ![A janela "NPS extensão para o Azure MFA programa de configuração"](./media/nps-extension-vpn/image36.png)
 
5. No **NPS extensão para o Azure MFA configuração** janela, selecione **fechar**.  

    ![A janela de confirmação "Configuração concluída com êxito"](./media/nps-extension-vpn/image37.png) 
 
### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Configurar certificados para utilização com a extensão NPS, utilizando um script do PowerShell
Para se certificar de comunicações seguro e garantia, configure certificados para utilização pela extensão do NPS. Os componentes NPS incluem um script do Windows PowerShell que configura um certificado autoassinado para utilização com NPS. 

O script executa as seguintes ações:

* Cria um certificado autoassinado.
* Associa a chave pública do certificado para o serviço principal no Azure AD.
* Armazena o certificado no arquivo do computador local.
* Concede o acesso de utilizador de rede a chave privada do certificado.
* Reinicia o serviço NPS.

Se pretender utilizar os seus próprios certificados, tem de associar a chave pública do certificado com o principal de serviço no Azure AD e assim sucessivamente.

Para utilizar o script, forneça a extensão com as credenciais administrativas do Azure Active Directory e o ID de inquilino do Azure Active Directory que copiou anteriormente. Execute o script em cada servidor NPS onde instalar a extensão NPS.

1. Execute o Windows PowerShell como administrador.

2. Na linha de comandos do PowerShell, introduza **cd c:\Programas\Microsoft Files\Microsoft\AzureMfa\Config**e, em seguida, selecione Enter.

3. A seguinte linha de comandos, introduza **.\AzureMfsNpsExtnConfigSetup.ps1**e, em seguida, selecione Enter.  
    O script verifica se o módulo Azure AD PowerShell está instalado. Se não estiver instalado, o script instala o módulo para si.
 
    ![PowerShell](./media/nps-extension-vpn/image38.png)
 
    Depois do script verifica a instalação do módulo do PowerShell, apresenta a Azure Active Directory PowerShell módulo início de sessão janela. 

4. Introduza as credenciais de administrador do Azure AD e a palavra-passe e, em seguida, selecione **sessão**. 
 
    ![A janela de início de sessão do PowerShell](./media/nps-extension-vpn/image39.png)
 
5. Na linha de comandos, cole o ID do inquilino que copiou anteriormente e, em seguida, selecione Enter. 

    ![ID do inquilino](./media/nps-extension-vpn/image40.png)

    O script cria um certificado autoassinado e efetua outras alterações de configuração. O resultado é que como na imagem seguinte:

    ![Certificado autoassinado](./media/nps-extension-vpn/image41.png)

6. Reinicie o servidor.

### <a name="verify-the-configuration"></a>Verificar se
Para verificar a configuração, tem de estabelecer uma nova ligação de VPN com o servidor VPN. Depois de introduzir com êxito as suas credenciais para autenticação primária, a ligação VPN aguarda que a autenticação secundária com êxito antes da ligação for estabelecida, conforme mostrado abaixo. 

![A janela de VPN de definições do Windows](./media/nps-extension-vpn/image42.png)

Se autenticar com êxito com o método de verificação secundário que configurou anteriormente no MFA do Azure, estão ligados ao recurso. No entanto, se a autenticação secundária for bem-sucedida, for negado o acesso ao recurso. 

No exemplo seguinte, a aplicação Microsoft Authenticator num Windows Phone fornece a autenticação secundária:

![Verificar conta](./media/nps-extension-vpn/image43.png)

Depois de ter autenticadas com êxito utilizando o método secundário, é concedido o acesso à porta virtual no servidor VPN. Porque era necessário para utilizar um método de autenticação secundária com uma aplicação móvel num dispositivo fidedigno, o processo de início de sessão é mais seguro do que o se estava a utilizar apenas uma combinação de nome de utilizador e palavra-passe.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Ver registos do Visualizador de eventos para eventos de início de sessão com êxito
Para ver eventos de início de sessão com êxito nos registos do Visualizador de eventos do Windows, a consulta a segurança do Windows registo no servidor NPS, introduzindo o seguinte comando do PowerShell:

    _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL 

![Segurança de PowerShell Visualizador de eventos](./media/nps-extension-vpn/image44.png)
 
Também pode ver o registo de segurança ou a vista personalizada serviços de acesso e política de rede, conforme mostrado aqui:

![Registo do servidor de políticas de rede](./media/nps-extension-vpn/image45.png)

No servidor onde instalou a extensão NPS para o multi-factor Authentication do Azure, pode encontrar os registos de aplicações do Visualizador de eventos que são específicos para a extensão no *aplicações e serviços Logs\Microsoft\AzureMfa*. 

    _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL

![Visualizador de eventos "Número de eventos" painel](./media/nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Guia de resolução de problemas
Se a configuração não está a funcionar conforme esperado, comece por verificar que o utilizador está configurado para utilizar a MFA de resolução de problemas. Tem do utilizador ligar à [portal do Azure](https://portal.azure.com). Se o utilizador é o pedido de autenticação secundária e com êxito pode autenticar, pode eliminar uma configuração incorreta do MFA como um problema.

Se a MFA está a funcionar para o utilizador, consulte os registos do Visualizador de eventos relevantes. Os registos incluir os eventos de segurança, Gateway operacional e registos de multi-factor Authentication do Azure que são abordados na secção anterior. 

Um exemplo de um registo de segurança que apresenta um evento de início de sessão falhado (evento ID 6273) é mostrado aqui:

![Registo de segurança que mostra um evento de início de sessão falhado](./media/nps-extension-vpn/image47.png)

Um evento do registo de multi-factor Authentication do Azure relacionado é mostrado aqui:

![Os registos de multi-factor Authentication do Azure](./media/nps-extension-vpn/image48.png)

Para efetuar a resolução de problemas avançada, consulte os ficheiros de registo do formato do NPS da base de dados onde o serviço NPS está instalado. Os ficheiros de registo são criados no _%SystemRoot%\System32\Logs_ pasta ficheiros de texto delimitado por vírgulas. Para obter uma descrição dos ficheiros de registo, consulte [interpretar NPS base de dados de registo de ficheiros de formato](https://technet.microsoft.com/library/cc771748.aspx). 

As entradas nestes ficheiros de registo são difíceis de interpretar, a menos que exportá-las para uma folha de cálculo ou uma base de dados. Pode encontrar muitas autenticação serviço Internet (IAS) análise online ferramentas para ajudá-lo a interpretar os ficheiros de registo. O resultado de um desses transferível [shareware aplicação](http://www.deepsoftware.com/iasviewer) é mostrada aqui: 

![Aplicação de shareware](./media/nps-extension-vpn/image49.png)

Para fazer adicionais de resolução de problemas, pode utilizar um analisador de protocolos como Wireshark ou [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). A imagem seguinte do Wireshark mostra as mensagens RADIUS entre o servidor VPN e o NPS.

![Analisador de mensagens da Microsoft](./media/nps-extension-vpn/image50.png)

Para obter mais informações, consulte [integrar a infraestrutura existente do NPS com o Azure multi-factor Authentication](multi-factor-authentication-nps-extension.md). 

## <a name="next-steps"></a>Passos seguintes
[Obter o multi-factor Authentication do Azure](multi-factor-authentication-versions-plans.md)

[Gateway de Ambiente de Trabalho Remoto e Servidor Multi-Factor Authentication do Azure com o RADIUS](multi-factor-authentication-get-started-server-rdg.md)

[Integrar os diretórios no local ao Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md)

