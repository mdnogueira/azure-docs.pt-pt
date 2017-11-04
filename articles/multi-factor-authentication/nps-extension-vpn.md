---
title: "Integração de VPN com a MFA do Azure utilizando a extensão NPS | Microsoft Docs"
description: "Este artigo aborda integrar a sua infraestrutura de VPN com a MFA do Azure com a extensão de servidor de políticas de rede (NPS) para o Microsoft Azure."
services: active-directory
keywords: "Azure MFA, integrar VPN do Azure Active Directory, a extensão de servidor de políticas de rede"
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: kgremban
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: 8ac4c5d88e724febf21fe6bcc8ecf939283f361e
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="integrate-your-vpn-infrastructure-with-azure-multi-factor-authentication-mfa-using-the-network-policy-server-nps-extension-for-azure"></a>Integrar a sua infraestrutura de VPN com multi-factor Authentication (MFA) do Azure com a extensão de servidor de políticas de rede (NPS) para o Azure

## <a name="overview"></a>Descrição geral

A extensão de servidor de políticas de rede (NPS) para o Azure permite que as organizações a salvaguardar a autenticação de cliente RADIUS Remote Authentication Dial-In User Service () com baseado na nuvem [Azure multi-factor Authentication (MFA)](multi-factor-authentication-get-started-server-rdg.md), que fornece verificação em dois passos.

Este artigo fornece instruções para integrar a infraestrutura NPS mfa do Azure com a extensão NPS para o Azure para ativar a verificação de dois passos segura para os utilizadores a tentar ligar à sua rede através de uma VPN. 

A política de rede e dos serviços de acesso (NPS) permite às organizações as seguintes capacidades:

* Especifique localizações centrais para a gestão e o controlo de pedidos de rede para especificar quem pode ligar-se, as horas do dia são permitidas ligações, a duração de ligações e o nível de segurança que os clientes devem utilizar para ligar e assim sucessivamente. Em vez de especificar estas políticas em cada servidor VPN ou Gateway de ambiente de trabalho remoto (RD), estas políticas podem ser especificadas uma vez numa localização central. O protocolo RADIUS é utilizado para fornecer a autenticação, autorização e gestão de contas (AAA) centralizada. 
* Estabelecer e impor políticas de estado de funcionamento de cliente de proteção de acesso de rede (NAP) determinam se dispositivos são concedidos o acesso restrito ou sem restrições aos recursos de rede.
* Fornecer um meio para impor a autenticação e autorização para aceder aos 802.1 pontos de acesso sem fios com capacidade de x e comutadores de Ethernet.    

Para obter mais informações, consulte [servidor de políticas de rede (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top). 

Para melhorar a segurança e fornecer o elevado nível de compatibilidade, as organizações podem integrar NPS mfa do Azure para garantir que os utilizadores utilizar a verificação de dois passos para poder ligar à porta virtual no servidor VPN. Para os utilizadores ser concedido acesso, tem de fornecer as respetivas combinação de nome de utilizador/palavra-passe com informações que o utilizador tem no seu controlo. Estas informações tem de ser fidedigna e não facilmente duplicadas, tais como um número de telemóvel, o número fixo, a aplicação num dispositivo móvel e assim sucessivamente.

Antes da disponibilidade da extensão NPS para o Azure, os clientes que wished para implementar a verificação de dois passos para ambientes de NPS e o MFA do Azure integradas tinham que configurar e manter um servidor MFA separado no ambiente no local, conforme documentado no Gateway de ambiente de trabalho remoto e servidor de autenticação do multi-factor do Azure com o RADIUS.

A disponibilidade da extensão NPS para o Azure fornece as organizações a opção de implementar uma solução MFA com base no local ou uma solução MFA baseado na nuvem para autenticação de cliente RADIUS segura.
 
## <a name="authentication-flow"></a>Fluxo de autenticação
Quando um utilizador liga a uma porta virtual no servidor VPN, devem primeiro autenticar utilizando uma variedade de protocolos, que permite a utilização de uma combinação de nome de utilizador/palavra-passe e métodos de autenticação baseada em certificado. 

Para além de autenticação e a verificar a identidade, os utilizadores tem de ter as permissões de acesso telefónico adequadas. Implementações simples, estas permissões de acesso telefónico permitirem o acesso são definidas diretamente nos objetos de utilizador do Active Directory. 

 ![Propriedades do utilizador](./media/nps-extension-vpn/image1.png)

Para implementações simples, cada servidor VPN concede ou nega o acesso com base nas políticas definidas em cada servidor VPN local.

Em implementações maiores e mais dimensionáveis, as políticas que conceder ou negar o acesso VPN são centralizada em servidores RADIUS. Neste caso, o servidor VPN age como um servidor de acesso (cliente RADIUS), que reencaminha os pedidos de ligação e mensagens de conta para um servidor RADIUS. Para ligar à porta virtual no servidor VPN, os utilizadores têm de ser autenticados e cumprem as condições definidas centralmente nos servidores RADIUS. 

Quando a extensão NPS para o Azure está integrada com o NPS, o fluxo de autenticação com êxito é o seguinte:

1. O servidor VPN recebe um pedido de autenticação de um utilizador VPN que inclui o nome de utilizador e palavra-passe para ligar a um recurso, tal como uma sessão de ambiente de trabalho remoto. 
2. A atuar como um cliente RADIUS, o servidor VPN converte o pedido a uma mensagem de pedido de acesso RADIUS e envia a mensagem (a palavra-passe está encriptada) para o servidor RADIUS (NPS) onde está instalada a extensão NPS. 
3. A combinação de nome de utilizador e palavra-passe é verificada no Active Directory. Se o nome de utilizador / palavra-passe está incorreta, o servidor RADIUS envia uma mensagem de acesso rejeitar. 
4. Se forem satisfeitas todas as condições conforme especificado no pedido de ligação de NPS e políticas de rede (por exemplo, a hora do dia ou grupo de restrições de associação), a extensão NPS aciona um pedido de autenticação secundária com a MFA do Azure. 
5. Azure MFA comunica com o Azure Active Directory, obtém os detalhes do utilizador e efetua a autenticação secundária utilizando o método configurado pelo utilizador (mensagem de texto, aplicação móvel e assim sucessivamente). 
6. Depois de concluído com sucesso o desafio MFA, o MFA do Azure comunica o resultado para a extensão NPS.
7. Depois da tentativa de ligação é autenticada e autorizada, o servidor NPS de onde está instalada a extensão envia uma mensagem de aceitar de acesso RADIUS para o servidor VPN (cliente RADIUS).
8. O utilizador é concedido acesso à porta virtual no servidor VPN e estabelece um túnel VPN encriptado.

## <a name="prerequisites"></a>Pré-requisitos
Esta secção fornece detalhes sobre os pré-requisitos necessários antes de integrar o MFA do Azure com o Gateway de ambiente de trabalho remoto. Antes de começar, tem de ter os seguintes pré-requisitos no local.

* Infraestrutura de VPN
* Função de política de rede e dos serviços de acesso (NPS)
* Licença do MFA do Azure
* Software Windows Server
* Bibliotecas
* Synched do Azure AD com o AD no local 
* ID de GUID do Azure Active Directory

### <a name="vpn-infrastructure"></a>Infraestrutura de VPN
Este artigo pressupõe que tem uma infraestrutura VPN de trabalhar com o Microsoft Windows Server 2016 no local e que o servidor VPN atualmente não está configurado para pedidos de ligação direta para um servidor RADIUS. Irá configurar a infraestrutura VPN para utilizar um servidor RADIUS central neste guia.

Se não tiver uma infraestrutura de trabalho no local, pode criar rapidamente esta infraestrutura ao seguir as orientações fornecidas várias tutoriais de configuração VPN que pode encontrar no Microsoft e sites de terceiros. 

### <a name="network-policy-and-access-services-nps-role"></a>Função de política de rede e dos serviços de acesso (NPS)

O serviço de função NPS fornece a funcionalidade de cliente e servidor RADIUS. Este artigo pressupõe que já instalou a função NPS num servidor membro ou controlador de domínio no seu ambiente. Irá configurar RADIUS para uma configuração de VPN neste guia. Instalar a função NPS num servidor _outros_ ao seu servidor VPN.

Para obter informações sobre como instalar a função NPS de serviço do Windows Server 2012 ou superior, consulte [instalar um servidor de políticas de estado de funcionamento de NAP](https://technet.microsoft.com/library/dd296890.aspx). Política de acesso de rede (NAP) foi preterido no Windows Server 2016. Para obter uma descrição das melhores práticas para NPS, incluindo a recomendação para instalar NPS num controlador de domínio, consulte [melhores práticas para NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-mfa-license"></a>Licença do MFA do Azure

Necessário destina-se a uma licença do MFA do Azure, que está disponível através de um Azure AD Premium, o Enterprise Mobility plus segurança (EMS) ou uma subscrição de MFA. Para obter mais informações, consulte [como obter o Azure multi-factor Authentication](multi-factor-authentication-versions-plans.md). Para fins de teste, pode utilizar uma subscrição de avaliação.

### <a name="windows-server-software"></a>Software Windows Server

A extensão NPS requer o Windows Server 2008 R2 SP1 ou posterior com o serviço de função NPS instalado. Todos os passos neste guia foram efetuados com o Windows Server 2016.

### <a name="libraries"></a>Bibliotecas

Estas bibliotecas são instaladas automaticamente com a extensão.

-   [Visual C++ Redistributable Packages for Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Microsoft Azure módulo Active Directory para Windows PowerShell versão 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

O Microsoft Azure Active Directory módulo para o Windows PowerShell está instalado, se ainda não estiver presente, através de um script de configuração executado como parte do processo de configuração. Não é necessário para instalar este módulo antecedência se não estiver já instalado.

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Synched do Azure Active Directory com o Active Directory no local 

Para utilizar a extensão NPS, os utilizadores no local tem de ser sincronizados com o Azure Active Directory e ativados para o multi-factor Authentication. Este guia pressupõe que os utilizadores no local são synched com o Azure Active Directory com o AD Connect. São fornecidas instruções para permitir que os utilizadores para a MFA abaixo.
Para obter informações sobre o Azure AD connect, consulte [integrar os diretórios no local ao Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md). 

### <a name="azure-active-directory-guid-id"></a>ID de GUID do Azure Active Directory 
Para instalar a extensão NPS, terá de saber o GUID do Azure Active Directory. Na secção seguinte, são fornecidas instruções para localizar o GUID do Azure Active Directory.

## <a name="configure-radius-for-vpn-connections"></a>Configurar RADIUS para ligações VPN

Se instalou a função de servidor NPS num servidor membro, terá de configurar para autenticar e autorizar que ligações de VPN do pedido de cliente VPN. 

Esta secção assume que instalou a função de servidor de políticas de rede, mas não tiver configurado-lo para utilização na sua infraestrutura.

>[!NOTE]
>Se já tiver um servidor VPN de trabalho que utiliza um servidor RADIUS centralizado para autenticação, pode ignorar esta secção.
>

### <a name="register-server-in-active-directory"></a>Registar servidor no Active Directory
Para funcionarem corretamente neste cenário, o servidor NPS tem de ser registado no Active Directory.

1. Abra o Gestor de servidor.
2. No Gestor de servidor, clique em **ferramentas**e, em seguida, clique em **servidor de políticas de rede**. 
3. Na consola do servidor de políticas de rede, faça duplo clique **NPS (Local)**e, em seguida, clique em **registar servidor no Active Directory**. Clique em **OK** duas vezes.

 ![Servidor de políticas de rede](./media/nps-extension-vpn/image2.png)

4. Deixe a consola aberta para o procedimento seguinte.

### <a name="use-wizard-to-configure-radius-server"></a>Utilize o Assistente para configurar o servidor RADIUS
Pode utilizar um padrão (baseada em Assistente) ou avançadas a opção de configuração para configurar o servidor RADIUS. Esta secção assume que a utilização da opção baseada no Assistente de configuração padrão.

1. Na consola do servidor de políticas de rede, clique em **NPS (Local)**.
2. Em configuração padrão, selecione **servidor RADIUS para ligações VPN ou de marcação telefónica**e, em seguida, clique em **configurar VPN ou de marcação telefónica**.

 ![Configurar a VPN](./media/nps-extension-vpn/image3.png)

3. Na página o tipo de ligações de rede privada Virtual ou de marcação telefónica selecione, selecione **ligações de rede privada Virtual**e clique em **seguinte**.

 ![Rede privada virtual](./media/nps-extension-vpn/image4.png)

4. Na página especificar acesso telefónico ou VPN servidor, clique em **adicionar**.
5. No **cliente RADIUS novo** caixa de diálogo, forneça um nome amigável, introduza o nome resolúvel ou endereço IP do servidor VPN e introduza uma palavra-passe secreta partilhada. Tornar esta partilhado secreta palavra-passe e complexa. Registe esta palavra-passe, conforme necessário para obter os passos na secção seguinte.

 ![Novo cliente RADIUS](./media/nps-extension-vpn/image5.png)

6. Clique em **OK**e, em seguida, **seguinte**.
7. No **configurar métodos de autenticação** página, aceite a seleção predefinida (Microsoft encriptados autenticação versão 2 (MS-CHAPv2) ou escolha outra opção e clique em **seguinte**.

  >[!NOTE]
  >Se configurar o protocolo de autenticação extensível (EAP), tem de utilizar MS CHAPv2 ou PEAP. Não existem outro EAP é suportada.
 
8. Na página especificar grupos de utilizadores, clique em **adicionar** e selecione um grupo adequado, se existir. Caso contrário, deixe em branco para conceder acesso a todos os utilizadores a seleção.

 ![Especifique grupos de utilizadores](./media/nps-extension-vpn/image7.png)

9. Clique em **Seguinte**.
10. Na página especificar filtros de IP, clique em **seguinte**.
11. Na página especificar definições de encriptação, aceite as predefinições e clique em **seguinte**.

 ![Especifique Encryption](./media/nps-extension-vpn/image8.png)

12. Em especificar um nome do Realm, deixe o nome em branco, aceite a predefinição e, em **seguinte**.

 ![Especifique um nome do Realm](./media/nps-extension-vpn/image9.png)

13. Na página de clientes de ligações de rede privada Virtual e RADIUS ou telefónico concluir novo, clique em **concluir**.

 ![Ligações concluídas](./media/nps-extension-vpn/image10.png)

### <a name="verify-radius-configuration"></a>Verifique a configuração RADIUS
Esta secção fornece detalhes sobre a configuração que criou utilizando o assistente.

1. No servidor NPS, na consola do NPS (Local), expanda os clientes RADIUS e selecione **clientes RADIUS**.
2. No painel de detalhes, faça duplo clique o cliente RADIUS que criou utilizando o assistente e clique em **propriedades**. As propriedades para o cliente RADIUS (o servidor VPN) devem ser semelhantes às mostrado abaixo.

 ![Propriedades VPN](./media/nps-extension-vpn/image11.png)

3. Clique em **Cancelar**.
4. No servidor NPS, na consola do NPS (Local), expanda **políticas**e selecione **políticas de pedido de ligação**. Deverá ver a política de ligações VPN que se assemelha a imagem abaixo.

 ![Pedidos de ligação](./media/nps-extension-vpn/image12.png)

5. Em políticas, selecione **políticas de rede**. Tem uma política de ligações de rede privada Virtual (VPN) que se assemelha a imagem abaixo.

 ![Propriedades da rede](./media/nps-extension-vpn/image13.png)

## <a name="configure-vpn-server-to-use-radius-authentication"></a>Configurar o servidor VPN para utilizar a autenticação RADIUS
Nesta secção, configure o servidor VPN para utilizar a autenticação RADIUS. Esta secção assume que tiver uma configuração de trabalho do servidor VPN, mas não tiver configurado o servidor VPN para utilizar a autenticação RADIUS. Depois de configurar o servidor VPN, confirmar que a configuração está a funcionar conforme esperado.

>[!NOTE]
>Se já tiver uma configuração de servidor VPN que utiliza a autenticação RADIUS, pode ignorar esta secção.
>

### <a name="configure-authentication-provider"></a>Configurar o fornecedor de autenticação
1. No servidor VPN, abra o Gestor de servidor.
2. No Gestor de servidor, clique em **ferramentas**e, em seguida, **encaminhamento e acesso remoto**.
3. Na consola de encaminhamento e acesso remoto, clique com botão direito  **\[nome do servidor\] (local)**e, em seguida, clique em **propriedades**.

 ![Encaminhamento e acesso remoto](./media/nps-extension-vpn/image14.png)
 
4. No **[Propriedades de nome de servidor} (local)** caixa de diálogo, clique em de **segurança** separador. 
5. No **segurança** separador, no fornecedor de autenticação, clique em **autenticação RADIUS**e, em seguida, **configurar**.

 ![Autenticação RADIUS](./media/nps-extension-vpn/image15.png)
 
6. Na caixa de diálogo de autenticação RADIUS, clique em **adicionar**.
7. A adicionar servidor RADIUS, no nome do servidor, adicione o nome ou o endereço IP do servidor RADIUS que configurou na secção anterior.
8. No segredo partilhado, clique em **alteração** e adicione a partilhado secreta palavra-passe criado e registado anteriormente.
9. No tempo limite (segundos), altere o valor para um valor entre **30** e **60**. Isto é necessário para permitir tempo suficiente concluir o segundo fator de autenticação.
 
 ![Adicionar servidor RADIUS](./media/nps-extension-vpn/image16.png)
 
10. Clique em **OK** até que conclua fechar todas as caixas de diálogo.

### <a name="test-vpn-connectivity"></a>Testar a conectividade VPN
Nesta secção, confirme que o cliente VPN é autenticado e autorizado pelo servidor RADIUS quando tenta ligar à porta virtual da VPN. Esta secção assume que está a utilizar o Windows 10 como um cliente VPN. 

>[!NOTE]
>Se já tiver configurado um cliente VPN para estabelecer ligação ao servidor VPN e guardar as definições, pode ignorar os passos relacionadas com a configurar e guardar um objeto de ligação VPN.
>

1. No computador cliente VPN, clique em **iniciar**e, em seguida, **definições** (engrenagem ícone).
2. Nas definições da janela, clique em **rede e Internet**.
3. Clique em **VPN**.
4. Clique em **adicionar uma ligação VPN**.
5. Em Adicionar uma ligação VPN, especificar Windows (incorporada) como o fornecedor VPN, em seguida, preencha os campos restantes, conforme adequado e clique em **guardar**. 

 ![Adicionar ligação VPN](./media/nps-extension-vpn/image17.png)
 
6. Abra o **de rede e partilha Center** no painel de controlo.
7. Clique em **alterar as definições da placa**.

 ![Alterar definições do adaptador](./media/nps-extension-vpn/image18.png)

8. Faça duplo clique na ligação de rede VPN e clique em propriedades. 

 ![Propriedades de rede VPN](./media/nps-extension-vpn/image19.png)

9. Na caixa de diálogo Propriedades de VPN, clique o **segurança** separador. 
10. No separador segurança, certifique-se de que apenas **Microsoft CHAP Version 2 (MS-CHAP v2)** está selecionada e clique em OK.

 ![Permitir que os protocolos](./media/nps-extension-vpn/image20.png)

11. Faça duplo clique na ligação VPN e clique em **Connect**.
12. Na página Definições, clique em **Connect**.

É apresentada uma ligação com êxito no registo de segurança no servidor RADIUS como 6272 de ID de evento, conforme mostrado abaixo.

 ![Propriedades do evento](./media/nps-extension-vpn/image21.png)

## <a name="troubleshoot-guide"></a>Guia de resolução de problemas
Partem do princípio de que a configuração de VPN estava a funcionar antes de configurar o servidor VPN para utilizar um servidor RADIUS centralizado para autenticação e autorização. Neste caso, é provável que o problema pode ser causado por uma configuração incorreta do servidor RADIUS ou a utilização de uma palavra-passe ou nome de utilizador inválido. Por exemplo, se utilizar o sufixo UPN alternativo no nome de utilizador, a tentativa de início de sessão poderá falhar (deve utilizar o mesmo nome de conta para obter os melhores resultados). 

Para resolver estes problemas, um local ideal para começar, é examinar os registos de eventos de segurança no servidor RADIUS. Para guardar o tempo a procurar eventos, pode utilizar a baseada em funções política de rede e servidor de acesso vista personalizada no Visualizador de eventos, como mostrados abaixo. ID de evento 6273 indica onde o servidor de políticas de rede negado o acesso a um utilizador de eventos. 

 ![Visualizador de eventos](./media/nps-extension-vpn/image22.png)
 
## <a name="configure-multi-factor-authentication"></a>Configurar a autenticação Multifator
Esta secção fornece instruções para permitir que os utilizadores para a MFA e para configurar contas para verificação de dois passos. 

### <a name="enable-multi-factor-authentication"></a>Ativar a autenticação multifator
Nesta secção, ativar a contas do Azure AD para o MFA. Utilize o **portal clássico** para permitir aos utilizadores para a MFA. 

1. Abra um browser e navegue para [https://manage.windowsazure.com](https://manage.windowsazure.com). 
2. Inicie sessão como administrador.
3. No portal, no painel de navegação esquerdo, clique em **do Active Directory**.

 ![Diretório predefinido](./media/nps-extension-vpn/image23.png)

4. A coluna de nome, clique em **diretório predefinido** (ou outro diretório, se apropriado).
5. Na página de início rápido, clique em **configurar**.

 ![Configurar predefinições](./media/nps-extension-vpn/image24.png)

6. Na página configurar, desloque para baixo e, na secção autenticação multifator, clique em **gerir definições do serviço**.

 ![Gerir as definições da MFA](./media/nps-extension-vpn/image25.png)
 
7. Na página de autenticação multifator, reveja as definições de serviço predefinido e, em seguida, clique em **utilizadores**. 

 ![Utilizadores do MFA](./media/nps-extension-vpn/image26.png)
 
8. Na página de utilizadores, selecione os utilizadores que pretende ativar para a MFA e, em seguida, clique em **ativar**.

 ![Propriedades](./media/nps-extension-vpn/image27.png)
 
9. Quando lhe for pedido, clique em **ativar autenticação multifator**.

 ![Ativar a MFA](./media/nps-extension-vpn/image28.png)
 
10. Clique em **Fechar**. 
11. Atualize a página. O estado MFA é alterado para ativado.

Para obter informações sobre como permitir que os utilizadores de multi-factor Authentication, consulte [introdução ao Azure multi-factor Authentication na nuvem](multi-factor-authentication-get-started-cloud.md). 

### <a name="configure-accounts-for-two-step-verification"></a>Configurar contas para verificação de dois passos
Depois de uma conta tiver sido ativada para a MFA, os utilizadores não têm capacidade para iniciar sessão em recursos regidos pela política da MFA até configuradas com êxito um dispositivo fidedigno para utilizar para o segundo fator de autenticação ter utilizado a verificação de dois passos.

Nesta secção, é possível configurar um dispositivo fidedigno para utilização com verificação de dois passos. Existem várias opções disponíveis para configurá-los, incluindo o seguinte:

* **Aplicação móvel**. Instalar a aplicação Authenticator da Microsoft num dispositivo Windows Phone, Android ou iOS. Consoante as políticas da sua organização, tem de utilizar a aplicação dos dois modos: receber notificações para verificações (será enviada uma notificação para o seu dispositivo) ou utilizar o código de verificação (é necessário introduzir um código de verificação de atualizações cada 30 segundos). 
* **Chamada de telemóvel ou texto**. Pode optar por receber uma chamada telefónica automática ou mensagem de texto. Com a opção de chamada telefónica, responda a chamada e prima a tecla # para se autenticar. Com a opção de texto, pode responder à mensagem de texto ou introduza o código de verificação para a interface de início de sessão.
* **Office Telefonema**. Este processo é igual à que é descrito para automatizada chamadas telefónicas acima.

Siga estas instruções para configurar um dispositivo para utilizar a aplicação móvel para receber a notificação push para verificação.

1. Inicie sessão no [https://aka.ms/mfasetup](https://aka.ms/mfasetup) ou de qualquer site, tais como [https://portal.azure.com](https://portal.azure.com), onde é necessário para autenticar com as suas credenciais de ativar a MFA. 
2. Após iniciar sessão com o nome de utilizador e palavra-passe, é-lhe apresentado um ecrã que pede-lhe configurar a conta para verificação de segurança adicional.

 ![Segurança adicional](./media/nps-extension-vpn/image29.png)

3. Clique em **configurá-lo agora**.
4. Na página de verificação adicional de segurança, selecione um tipo de contacto (telefone de autenticação, telefone do escritório ou aplicação móvel). Em seguida, selecione um país ou região e selecionar um método. O método varia consoante o tipo de contacto que selecionar. Por exemplo, se escolher aplicação móvel, pode selecionar se pretende receber notificações de verificação ou utilizar um código de verificação. Os passos que se seguem partem do princípio de que escolher **aplicação móvel** como o tipo de contacto.

 ![Autenticação de telefone](./media/nps-extension-vpn/image30.png)

5. Selecione aplicação móvel, clique em **receber notificações de verificação**e, em seguida, **configurar**. 

 ![Verificação da aplicação móvel](./media/nps-extension-vpn/image31.png)
 
6. Se não tiver o feito, instale a aplicação móvel de autenticação no seu dispositivo. 
7. Siga as instruções na aplicação móvel para digitalizar o código de barras apresentado ou introduzir manualmente as informações e, em seguida, clique em **feito**.

 ![Configurar aplicação móvel](./media/nps-extension-vpn/image32.png)

8. Na página de verificação de segurança adicionais, clique em **contactar-me** e responder à notificação enviada para o seu dispositivo.
9. Na página de verificação de segurança adicional, introduza um número de contacto no caso de perder o acesso para a aplicação móvel e clique em **seguinte**.

 ![Número de telemóvel](./media/nps-extension-vpn/image33.png)
 
10. A verificação de segurança adicional, clique em **feito**.

O dispositivo está agora configurado para fornecer um segundo método de verificação. Para obter informações sobre como configurar contas para verificação de dois passos, consulte [configurar a minha conta para a verificação de dois passos](./end-user/multi-factor-authentication-end-user-first-time.md).

## <a name="install-and-configure-nps-extension"></a>Instalar e configurar a extensão NPS

Esta secção fornece instruções para configurar a VPN para utilizar o MFA do Azure para a autenticação de cliente com o servidor VPN.

Depois de instalar e configurar a extensão NPS, toda a autenticação com base no RADIUS cliente que é processada por este servidor é necessária para utilizar o MFA do Azure. Se não estão inscritos todos os utilizadores VPN na MFA do Azure, pode configurar o outro servidor RADIUS para autenticar utilizadores que não estão configurados para utilizar a MFA. Ou pode criar uma entrada de registo que permite aos utilizadores solicitados fornecer um segundo fator de autenticação, apenas se estiverem inscritos na MFA. 

Criar um novo valor de cadeia denominado _REQUIRE_USER_MATCH no HKLM\SOFTWARE\Microsoft\AzureMfa_e defina o valor como TRUE ou FALSE. 

 ![Exigir correspondência de utilizador](./media/nps-extension-vpn/image34.png)
 
Se o valor é definido como TRUE ou não definido, todos os pedidos de autenticação estão sujeitos a um desafio MFA. Se o valor estiver definido como FALSE, são emitidos desafios MFA apenas a utilizadores que estão inscritos na MFA. Utilize apenas a definição de falsa no teste ou em ambientes de produção durante um período de integração.

### <a name="acquire-azure-active-directory-guid-id"></a>Adquirir ID de GUID do Azure Active Directory

Como parte da configuração da extensão NPS, terá de fornecer credenciais de administrador e o ID do Active Directory do Azure para o seu inquilino do Azure AD. Os passos abaixo mostram como obter o ID do inquilino.

1. Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com) como administrador global do inquilino do Azure.
2. No painel de navegação esquerdo, clique em de **do Azure Active Directory** ícone.
3. Clique em **Propriedades**.
4. Para copiar o seu ID de diretório para a área de transferência, selecione o **cópia** ícone.
 
 ![ID de diretório](./media/nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>Instale a extensão NPS
A extensão NPS tem de ser instalado num servidor que tenha a política de rede e a função de serviços de acesso (NPS) instalado e que funções como o servidor RADIUS na sua estrutura. Não instale a extensão do NPS no servidor de ambiente de trabalho remoto.

1. Transfira a extensão NPS do [https://aka.ms/npsmfa](https://aka.ms/npsmfa). 
2. Copie o ficheiro executável do programa de configuração (NpsExtnForAzureMfaInstaller.exe) para o servidor NPS.
3. No servidor NPS, faça duplo clique em **NpsExtnForAzureMfaInstaller.exe**. Se lhe for pedido, clique em **executar**.
4. Na caixa de diálogo de extensão de NPS para o Azure MFA configuração, reveja os termos de licenciamento de software, consulte **concordo com os termos de licenciamento e as condições**e clique em **instalar**.

 ![Extensão NPS](./media/nps-extension-vpn/image36.png)
 
5. Na caixa de diálogo de extensão de NPS para o Azure MFA configuração, clique em **fechar**.  

 ![Configuração com êxito](./media/nps-extension-vpn/image37.png) 
 
### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Configurar certificados para utilização com a extensão NPS utilizando um script do PowerShell
Para se certificar de comunicações seguro e garantia, terá de configurar certificados para utilização pela extensão do NPS. Os componentes NPS incluem um script do Windows PowerShell que configura um certificado autoassinado para utilização com NPS. 

O script executa as seguintes ações:

* Cria um certificado autoassinado
* Associa a chave pública do certificado de serviço principal no Azure AD
* Armazena o certificado no arquivo do computador local
* Concede acesso a chave privada do certificado de utilizador de rede
* Reinicia o serviço de servidor de políticas de rede

Se pretender utilizar os seus próprios certificados, tem de associar a chave pública do certificado para o principal de serviço no Azure AD e assim sucessivamente.
Para utilizar o script, forneça a extensão com as credenciais administrativas do Azure Active Directory e o ID de inquilino do Azure Active Directory que copiou anteriormente. Execute o script em cada servidor NPS onde instalar a extensão NPS.

1. Abra uma linha de comandos do Windows PowerShell administrativa.
2. Na linha de comandos do PowerShell, escreva _cd 'c:\Programas\Microsoft Files\Microsoft\AzureMfa\Config'_e prima **ENTER**.
3. Tipo _.\AzureMfsNpsExtnConfigSetup.ps1_e prima **ENTER**. 
 * O script verifica se o módulo Azure Active Directory PowerShell está instalado. Se não estiver instalado, o script instala o módulo para si.
 
 ![PowerShell](./media/nps-extension-vpn/image38.png)
 
4. Depois do script verifica a instalação do módulo do PowerShell, apresenta a caixa de diálogo de módulo do Azure Active Directory PowerShell. Na caixa de diálogo, introduza as credenciais de administrador do Azure AD e a palavra-passe e clique em **sessão**. 
 
 ![Início de sessão do PowerShell](./media/nps-extension-vpn/image39.png)
 
5. Quando lhe for pedido, cole o ID do inquilino que copiou anteriormente para a área de transferência e prima **ENTER**. 

 ![ID do inquilino](./media/nps-extension-vpn/image40.png)

6. O script cria um certificado autoassinado e efetua outras alterações de configuração. O resultado é como a imagem abaixo.

 ![Gestão personalizada assinou o certificado](./media/nps-extension-vpn/image41.png)

7. Reinicie o servidor.
 
### <a name="verify-configuration"></a>Verifique a configuração
Para verificar a configuração, tem de estabelecer uma nova ligação de VPN com o servidor VPN. Após introduzir com êxito as suas credenciais para autenticação primária, a ligação VPN aguarda que a autenticação secundária com êxito antes da ligação for estabelecida, conforme mostrado abaixo. 

 ![Verifique a configuração](./media/nps-extension-vpn/image42.png)

Se autenticar com êxito com o método de verificação secundário que configurou anteriormente na MFA do Azure, estão ligados ao recurso. No entanto, se a autenticação secundária não for bem sucedida, for negado o acesso ao recurso. 

No exemplo abaixo, a aplicação de autenticador num Windows phone é utilizada para fornecer a autenticação secundária.

 ![Verificar conta](./media/nps-extension-vpn/image43.png)

Depois de ter autenticadas com êxito utilizando o método secundário, é concedido o acesso à porta virtual no servidor VPN. No entanto, uma vez que tinham de utilizar um método de autenticação secundária utilizando uma aplicação móvel num dispositivo fidedigno, o processo de início de sessão é mais seguro do que seria utilizar apenas um nome de utilizador / combinação de palavra-passe.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Ver registos do Visualizador de eventos para eventos de início de sessão com êxito
Para ver os eventos de início de sessão bem-sucedidos nos registos do Visualizador de eventos do Windows, pode emitir o comando do Windows PowerShell seguinte para consultar o registo de segurança do Windows no servidor NPS.

Para consultar os eventos de início de sessão bem-sucedidos nos registos do Visualizador de eventos de segurança, utilize o seguinte comando,
* _Get-WinEvent - Logname segurança_ | em que {$ . ID - eq '6272'} | FL 

 ![Visualizador de eventos de segurança](./media/nps-extension-vpn/image44.png)
 
Também pode ver o registo de segurança ou a vista personalizada serviços de acesso e política de rede, como mostrado abaixo:

 ![Acesso de política de rede](./media/nps-extension-vpn/image45.png)

No servidor onde instalou a extensão NPS para o MFA do Azure, pode encontrar os registos de aplicações do Visualizador de eventos específicos para a extensão no **aplicações e serviços Logs\Microsoft\AzureMfa**. 

* _Get-WinEvent - Logname segurança_ | em que {$ . ID - eq '6272'} | FL

 ![Número de eventos](./media/nps-extension-vpn/image46.png)

## <a name="troubleshoot-guide"></a>Guia de resolução de problemas
Se a configuração não está a funcionar conforme esperado, é um bom local para iniciar a resolução de problemas para verificar se o utilizador está configurado para utilizar o MFA do Azure. Tem do utilizador ligar ao [https://portal.azure.com](https://portal.azure.com). Se for pedidas para a autenticação secundária e com êxito podem autenticar, pode eliminar uma configuração incorreta do MFA do Azure.

Se o MFA do Azure está a funcionar para o es, deve rever os registos de eventos relevantes. Estes incluem os eventos de segurança, Gateway operacional e registos de MFA do Azure que são abordados na secção anterior. 

Segue-se um exemplo da saída de registo de segurança que mostra um evento de início de sessão falhadas (evento ID 6273):

 ![Registo de segurança](./media/nps-extension-vpn/image47.png)

Segue-se um evento dos registos de AzureMFA relacionado:

 ![Registos MFA do Azure](./media/nps-extension-vpn/image48.png)

Para efetuar avançadas de opções de resolução de problemas, consulte os ficheiros de registo do formato do NPS da base de dados onde o serviço NPS está instalado. Estes ficheiros de registo são criados no _%SystemRoot%\System32\Logs_ pasta ficheiros de texto delimitado por vírgulas. Para obter uma descrição destes ficheiros de registo, consulte [interpretar NPS base de dados de registo de ficheiros de formato](https://technet.microsoft.com/library/cc771748.aspx). 

As entradas nestes ficheiros de registo são difíceis de interpretar sem os importar para uma folha de cálculo ou uma base de dados. Pode encontrar um número de IAS parsers online para ajudá-lo a interpretar os ficheiros de registo. Segue-se a saída de um desses transferível [shareware aplicação](http://www.deepsoftware.com/iasviewer): 

 ![Aplicação de shareware](./media/nps-extension-vpn/image49.png)

Por fim, para mais opções de resolução de problemas, pode utilizar um analisador de protocolos como Wireshark ou [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). A imagem seguinte do Wireshark mostra as mensagens RADIUS entre o servidor VPN e o servidor NPS.

 ![Analisador de mensagens da Microsoft](./media/nps-extension-vpn/image50.png)

Para obter mais informações, consulte [integrar a infraestrutura existente do NPS com o Azure multi-factor Authentication](multi-factor-authentication-nps-extension.md).  

## <a name="next-steps"></a>Passos seguintes
[How to get Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md) (Como obter a Multi-Factor Authentication do Azure)

[Gateway de Ambiente de Trabalho Remoto e Servidor Multi-Factor Authentication do Azure com o RADIUS](multi-factor-authentication-get-started-server-rdg.md)

[Integrar os diretórios no local ao Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md)

