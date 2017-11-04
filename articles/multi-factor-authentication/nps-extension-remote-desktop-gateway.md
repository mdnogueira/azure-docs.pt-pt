---
title: "Integração do Gateway de ambiente de trabalho remota com a extensão de NPS de MFA do Azure | Microsoft Docs"
description: "Este artigo aborda integrar a sua infraestrutura de Gateway de ambiente de trabalho remoto com a MFA do Azure com a extensão de servidor de políticas de rede (NPS) para o Microsoft Azure."
services: active-directory
keywords: "Azure MFA, integrar o Gateway de ambiente de trabalho remoto, Azure Active Directory, a extensão de servidor de políticas de rede"
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
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: 01c5284a609a2246e32052985ad3a8c0475eafa5
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
#  <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integrar a sua infraestrutura de Gateway de ambiente de trabalho remoto utilizando a extensão de servidor de políticas de rede (NPS) e o Azure AD

Este artigo fornece detalhes para integrar a sua infraestrutura de Gateway de ambiente de trabalho remoto com o Azure multi-factor Authentication (MFA) com a extensão de servidor de políticas de rede (NPS) para o Microsoft Azure. 

A extensão de servidor de políticas de rede (NPS) para o Azure permite aos clientes salvaguardar a autenticação de cliente RADIUS Remote Authentication Dial-In User Service () a utilizar o Azure do baseado na nuvem [multi-factor Authentication (MFA)](multi-factor-authentication.md). Esta solução fornece a verificação de dois passos para adicionar uma segunda camada de segurança para inícios de sessão de utilizador e de transações.

Este artigo fornece instruções passo a passo para integrar a infraestrutura NPS mfa do Azure com a extensão NPS para o Azure. Isto permite que uma verificação segura para os utilizadores tentam iniciar sessão para um Gateway de ambiente de trabalho remoto. 

A política de rede e dos serviços de acesso (NPS) permite que as organizações a fazer o seguinte:
* Defina localizações centrais para a gestão e o controlo de pedidos de rede, especificando que pode ligar-se, os tempos de ligações de dia são permitidos, a duração de ligações e o nível de segurança que os clientes devem utilizar para ligar e assim sucessivamente. Em vez de especificar estas políticas em cada servidor VPN ou Gateway de ambiente de trabalho remoto (RD), estas políticas podem ser especificadas uma vez numa localização central. O protocolo RADIUS fornece a autenticação, autorização e gestão de contas (AAA) centralizada. 
* Estabelecer e impor políticas de estado de funcionamento de cliente de proteção de acesso de rede (NAP) determinam se dispositivos são concedidos o acesso restrito ou sem restrições aos recursos de rede.
* Fornecer um meio para impor a autenticação e autorização para aceder aos 802.1 pontos de acesso sem fios com capacidade de x e comutadores de Ethernet.    

Normalmente, as organizações utilize NPS (RADIUS) para simplificar e centralizar a gestão de VPN políticas. No entanto, muitas organizações também utilizam o NPS para simplificar e centralizar a gestão de políticas de autorização de ligação de ambiente de trabalho de RD (CAPs de RD). 

Organizações também podem integrar o MFA do Azure para melhorar a segurança e fornecem um elevado nível de compatibilidade do NPS. Isto ajuda a garantir que os utilizadores estabelecer a verificação de dois passos para iniciar sessão no Gateway de ambiente de trabalho remoto. Para os utilizadores ser concedido acesso, tem de fornecer as respetivas combinação de nome de utilizador/palavra-passe, juntamente com informações que o utilizador tem no seu controlo. Estas informações tem de ser fidedigna e não facilmente duplicadas, tais como um número de telemóvel, o número fixo, a aplicação num dispositivo móvel e assim sucessivamente.

Antes da disponibilidade da extensão NPS para o Azure, os clientes que wished para implementar a verificação de dois passos para ambientes de NPS e o MFA do Azure integradas tinham que configurar e manter um servidor MFA separado no ambiente no local, conforme documentado no [ Gateway de ambiente de trabalho remoto e de servidor de autenticação do multi-factor do Azure com o RADIUS](multi-factor-authentication-get-started-server-rdg.md).

A disponibilidade da extensão NPS para o Azure fornece as organizações a opção de implementar uma solução MFA com base no local ou uma solução MFA baseado na nuvem para autenticação de cliente RADIUS segura.

## <a name="authentication-flow"></a>Fluxo de autenticação

Para os utilizadores ser concedido acesso a recursos de rede através de um Gateway de ambiente de trabalho remoto, tem de cumprir as condições especificadas na política de autorização uma ligação de RD (CAP de RD) e na política de autorização de um recurso de RD (RAP de RD). CAPs de RD Especifique quem está autorizado a estabelecer ligação com Gateways de RD. RAPs de RD especificar os recursos de rede, tais como ambientes de trabalho remotos ou aplicações remotas, que o utilizador tem permissão para ligar através do Gateway de RD. 

Um Gateway de RD pode ser configurado para utilizar um arquivo de política central de CAPs de RD. RAPs de RD não é possível utilizar uma política central, como são processados no Gateway de RD. Um exemplo de um Gateway de RD configurados para utilizar um arquivo de política central de CAPs de RD é um cliente RADIUS para outro servidor NPS que funciona como o arquivo de política central.

Quando a extensão NPS para o Azure está integrada com o NPS e o Gateway de ambiente de trabalho remoto, o fluxo de autenticação com êxito é o seguinte:

1. O servidor de Gateway de ambiente de trabalho remoto recebe um pedido de autenticação de um utilizador de ambiente de trabalho remoto para ligar a um recurso, tal como uma sessão de ambiente de trabalho remoto. A atuar como um cliente RADIUS, o servidor de Gateway de ambiente de trabalho remoto converte o pedido a uma mensagem de pedido de acesso RADIUS e envia a mensagem para o servidor RADIUS (NPS) onde está instalada a extensão NPS. 
2. A combinação de nome de utilizador e palavra-passe é verificada no Active Directory e o utilizador ser autenticado.
3. Se forem satisfeitas as condições conforme especificado no pedido de ligação de NPS e as políticas de rede (por exemplo, a hora do dia ou grupo de restrições de associação), a extensão NPS aciona um pedido de autenticação secundária com a MFA do Azure. 
4. Azure MFA comunica com o Azure AD, obtém os detalhes do utilizador e efetua a autenticação secundária utilizando o método configurado pelo utilizador (mensagem de texto, aplicação móvel e assim sucessivamente). 
5. Depois de concluído com sucesso o desafio MFA, o MFA do Azure comunica o resultado para a extensão NPS.
6. O servidor NPS, onde está instalada a extensão, envia uma mensagem de aceitar de acesso RADIUS de mensagens em fila para a política de CAP de RD para o servidor de Gateway de ambiente de trabalho remoto.
7. O utilizador é concedido acesso ao recurso de rede pedido através do Gateway de RD.

## <a name="prerequisites"></a>Pré-requisitos
Esta secção fornece detalhes sobre os pré-requisitos necessários antes de integrar o MFA do Azure com o Gateway de ambiente de trabalho remoto. Antes de começar, tem de ter os seguintes pré-requisitos no local.  

* Infraestrutura de serviços de ambiente de trabalho (RDS) remoto
* Licença do MFA do Azure
* Software Windows Server
* Função de política de rede e dos serviços de acesso (NPS)
* Synched do Azure Active Directory com o Active Directory no local
* ID de GUID do Azure Active Directory

### <a name="remote-desktop-services-rds-infrastructure"></a>Infraestrutura de serviços de ambiente de trabalho (RDS) remoto
Tem de ter uma infraestrutura de serviços de ambiente de trabalho remoto (RDS) no local. Se não o fizer, em seguida, pode criar rapidamente esta infraestrutura no Azure utilizando o modelo seguinte do início rápido: [implementação criar coleção de sessões de ambiente de trabalho remoto](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment). 

Se pretender criar manualmente uma infraestrutura RDS no local rapidamente para fins de teste, siga os passos para implementar uma. 
**Saiba mais**: [implementar RDS com início rápido do Azure](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure) e [implementação da infraestrutura de RDS básica](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure). 

### <a name="azure-mfa-license"></a>Licença do MFA do Azure
Necessário destina-se a uma licença do MFA do Azure, que está disponível através de um Azure AD Premium, o Enterprise Mobility plus segurança (EMS) ou uma subscrição de MFA. Para obter mais informações, consulte [como obter o Azure multi-factor Authentication](multi-factor-authentication-versions-plans.md). Para fins de teste, pode utilizar uma subscrição de avaliação.

### <a name="windows-server-software"></a>Software Windows Server
A extensão NPS requer o Windows Server 2008 R2 SP1 ou posterior com o serviço de função NPS instalado. Todos os passos nesta secção foram efetuados com o Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Função de política de rede e dos serviços de acesso (NPS)
O serviço de função NPS fornece o servidor RADIUS e o cliente funcionalidade, bem como o serviço de estado de funcionamento da política de acesso de rede. Esta função tem de ser instalada em, pelo menos, dois computadores na sua infraestrutura: O Gateway de ambiente de trabalho remoto e outro servidor membro ou controlador de domínio. Por predefinição, a função já está presente no computador configurado como o Gateway de ambiente de trabalho remoto.  Tem também de instalar a função NPS em, pelo menos, noutro computador, tal como um controlador de domínio ou servidor membro.

Para obter informações sobre como instalar a função NPS de serviço do Windows Server 2012 ou anterior, consulte [instalar um servidor de políticas de estado de funcionamento de NAP](https://technet.microsoft.com/library/dd296890.aspx). Para obter uma descrição das melhores práticas para NPS, incluindo a recomendação para instalar NPS num controlador de domínio, consulte [melhores práticas para NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Synched do Azure Active Directory com o Active Directory no local
Para utilizar a extensão NPS, os utilizadores no local tem de ser sincronizados com o Azure AD e ativados para a MFA. Esta secção assume que os utilizadores no local são synched com o Azure AD com o AD Connect. Para obter informações sobre o Azure AD connect, consulte [integrar os diretórios no local ao Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md). 

### <a name="azure-active-directory-guid-id"></a>ID de GUID do Azure Active Directory
Para instalar a extensão de NPS, terá de saber o GUID do Azure AD. Abaixo, são fornecidas instruções para localizar o GUID do Azure AD.

## <a name="configure-multi-factor-authentication"></a>Configurar a autenticação Multifator 
Esta secção fornece instruções para integrar o MFA do Azure com o Gateway de ambiente de trabalho remoto. Como administrador, tem de configurar o serviço MFA do Azure antes dos utilizadores Self-podem registar os seus dispositivos multifator ou aplicações.

Siga os passos no [introdução ao Azure multi-factor Authentication na nuvem](multi-factor-authentication-get-started-cloud.md) para ativar a MFA para os utilizadores do Azure AD. 

### <a name="configure-accounts-for-two-step-verification"></a>Configurar contas para verificação de dois passos
Depois de uma conta tiver sido ativada para a MFA, não é possível iniciar sessão no recursos regidos pela política da MFA, até que configurou com êxito um dispositivo fidedigno para utilizar para o segundo fator de autenticação e ter autenticado utilizando a verificação de dois passos.

Siga os passos no [que Azure multi-factor Authentication significa para mim?](./end-user/multi-factor-authentication-end-user.md) para compreender e configurar corretamente os seus dispositivos para a MFA com a sua conta de utilizador.

## <a name="install-and-configure-nps-extension"></a>Instalar e configurar a extensão NPS
Esta secção fornece instruções para configurar a infraestrutura RDS para utilizar o MFA do Azure para a autenticação de cliente com o Gateway de ambiente de trabalho remoto.

### <a name="acquire-azure-active-directory-guid-id"></a>Adquirir ID de GUID do Azure Active Directory

Como parte da configuração da extensão NPS, terá de fornecer credenciais de administrador e o ID do AD do Azure para o seu inquilino do Azure AD. Os passos seguintes mostram como obter o ID do inquilino.

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com) como administrador global do inquilino do Azure.
2. No painel de navegação esquerdo, selecione o **do Azure Active Directory** ícone.
3. Selecione **propriedades**.
4. No painel de propriedades, para além do ID de diretório, clique em de **cópia** ícone, conforme mostrado abaixo, para copiar o ID para a área de transferência.

 ![Propriedades](./media/nps-extension-remote-desktop-gateway/image1.png)

### <a name="install-the-nps-extension"></a>Instale a extensão NPS
Instale a extensão NPS no servidor que tenha a função de política de rede e dos serviços de acesso (NPS) instalada. Isto funciona como servidor RADIUS para o design. 

>[!Important]
> Lembre-se de que não instale a extensão NPS no seu servidor de Gateway de ambiente de trabalho remoto.
> 

1. Transferir o [extensão NPS](https://aka.ms/npsmfa). 
2. Copie o ficheiro executável do programa de configuração (NpsExtnForAzureMfaInstaller.exe) para o servidor NPS.
3. No servidor NPS, faça duplo clique em **NpsExtnForAzureMfaInstaller.exe**. Se lhe for pedido, clique em **executar**.
4. Na caixa de diálogo de extensão de NPS para o Azure MFA configuração, reveja os termos de licenciamento de software, consulte **concordo com os termos de licenciamento e as condições**e clique em **instalar**.
 
  ![Configuração do MFA do Azure](./media/nps-extension-remote-desktop-gateway/image2.png)

5. Na caixa de diálogo de extensão de NPS para o Azure MFA configuração, clique em **fechar**. 

  ![Extensão NPS para o MFA do Azure](./media/nps-extension-remote-desktop-gateway/image3.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Configurar certificados para utilização com a extensão NPS utilizando um script do PowerShell
Em seguida, terá de configurar certificados para utilização pela extensão do NPS para se certificar de comunicações seguro e garantia. Os componentes NPS incluem um script do Windows PowerShell que configura um certificado autoassinado para utilização com NPS. 

O script executa as seguintes ações:

* Cria um certificado autoassinado
* Associa a chave pública do certificado de serviço principal no Azure AD
* Armazena o certificado no arquivo do computador local
* Concede acesso a chave privada do certificado de utilizador de rede
* Reinicia o serviço de servidor de políticas de rede

Se pretender utilizar os seus próprios certificados, tem de associar a chave pública do certificado para o principal de serviço no Azure AD e assim sucessivamente.

Para utilizar o script, forneça a extensão com as credenciais de administrador do Azure AD e o ID de inquilino do Azure AD que copiou anteriormente. Execute o script em cada servidor NPS onde instalou a extensão NPS. Em seguida, faça o seguinte:

1. Abra uma linha de comandos do Windows PowerShell administrativa.
2. Na linha de comandos do PowerShell, escreva **cd 'c:\Programas\Microsoft Files\Microsoft\AzureMfa\Config'**e prima **ENTER**.
3. Tipo _.\AzureMfsNpsExtnConfigSetup.ps1_e prima **ENTER**. O script verifica se o módulo Azure Active Directory PowerShell está instalado. Se não instalado, o script instala o módulo para si.

  ![O Azure AD PowerShell](./media/nps-extension-remote-desktop-gateway/image4.png)
  
4. Depois do script verifica a instalação do módulo do PowerShell, apresenta a caixa de diálogo de módulo do Azure Active Directory PowerShell. Na caixa de diálogo, introduza as credenciais de administrador do Azure AD e a palavra-passe e clique em **sessão**.

  ![Abra a conta do Powershell](./media/nps-extension-remote-desktop-gateway/image5.png)

5. Quando lhe for pedido, cole o ID do inquilino que copiou anteriormente para a área de transferência e prima **ENTER**.

  ![Introduza o ID do inquilino](./media/nps-extension-remote-desktop-gateway/image6.png)

6. O script cria um certificado autoassinado e efetua outras alterações de configuração. O resultado deve ser, como a imagem abaixo.

  ![Certificado autoassinado](./media/nps-extension-remote-desktop-gateway/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Configurar componentes do NPS no Gateway de ambiente de trabalho remoto
Nesta secção, irá configurar as políticas de autorização de ligação do Gateway de ambiente de trabalho remoto e outras definições de RADIUS.

O fluxo de autenticação necessita que as mensagens de RADIUS ser trocadas entre o Gateway de ambiente de trabalho remoto e o servidor NPS de onde está instalado o servidor NPS. Isto significa que tem de configurar as definições de cliente RADIUS no Gateway de ambiente de trabalho remoto e o servidor NPS de onde está instalada a extensão NPS. 

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Configurar políticas de autorização de ligação de Gateway de ambiente de trabalho remoto para utilizar o arquivo central
Políticas de autorização de ligações de ambiente de trabalho remoto (CAPs de RD) especificar os requisitos para ligar a um servidor de Gateway de ambiente de trabalho remoto. CAPs de RD pode ser armazenado localmente (predefinição) ou podem ser armazenados num arquivo central de CAP de RD que executa o NPS. Para configurar a integração do MFA do Azure com o RDS, tem de especificar a utilização de um arquivo central.

1. No servidor de Gateway de RD, abra **Gestor de servidor**. 
2. No menu, clique em **ferramentas**, aponte para **dos serviços de ambiente de trabalho remoto**e, em seguida, clique em **Gestor de Gateway de ambiente de trabalho remoto**.

  ![Serviços de Ambiente de Trabalho Remoto](./media/nps-extension-remote-desktop-gateway/image8.png)

3. Do Configuration Manager Gateway de RD, faça duplo clique  **\[nome do servidor\] (Local)**e clique em **propriedades**.

  ![Nome do servidor](./media/nps-extension-remote-desktop-gateway/image9.png)

4. Na caixa de diálogo Propriedades, selecione o **arquivo de CAPs de RD** separador.
5. No separador arquivo de CAPs de RD, selecione **servidor Central com NPS**. 
6. No **introduza um nome ou endereço IP para o servidor com NPS** campo, escreva o nome de servidor ou endereço IP do servidor onde instalou a extensão NPS.

  ![Introduza o nome ou endereço IP](./media/nps-extension-remote-desktop-gateway/image10.png)
  
7. Clique em **Adicionar**.
8. No **segredo partilhado** caixa de diálogo, introduza um segredo partilhado e, em seguida, clique em **OK**. Certifique-se de que registe este segredo partilhado e guarde o registo de forma segura.

 >[!NOTE]
 >Segredo partilhado é utilizado para estabelecer fidedignidade entre os servidores RADIUS e clientes. Crie um segredo e complexo.
 >

 ![Segredo partilhado](./media/nps-extension-remote-desktop-gateway/image11.png)

9. Clique em **OK** para fechar a caixa de diálogo.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Configurar o valor de tempo limite RADIUS no NPS de Gateway de ambiente de trabalho remoto
Para garantir que não há tempo para validar as credenciais dos utilizadores, efetuar a verificação de dois passos, receber respostas e responder a mensagens RADIUS, é necessário ajustar o valor de tempo limite RADIUS.

1. No servidor de Gateway de RD, abra o Gestor de servidor. No menu, clique em **ferramentas**e, em seguida, clique em **servidor de políticas de rede**. 
2. No **NPS (Local)** consola, expanda **clientes RADIUS e servidores**e selecione **servidor RADIUS remoto**.

 ![Servidor RADIUS remotos](./media/nps-extension-remote-desktop-gateway/image12.png)

3. No painel de detalhes, faça duplo clique **grupo de servidor de GATEWAY TS**.

 >[!NOTE]
 >Este grupo de servidor RADIUS foi criado quando configurou o servidor central para políticas NPS. O Gateway de RD reencaminha as mensagens RADIUS para este servidor ou o grupo de servidores, se mais do que um grupo.
 >

4. No **propriedades do grupo de servidor de GATEWAY de TS** diálogo caixa, selecione o endereço IP ou nome do servidor NPS que configurou para armazenar CAPs de RD e, em seguida, clique em **editar**. 

 ![Grupo de servidor de Gateway de TS](./media/nps-extension-remote-desktop-gateway/image13.png)

5. No **Editar servidor RADIUS** caixa de diálogo, selecione o **balanceamento de carga** separador.
6. No **balanceamento de carga** separador o **ignorados do número de segundos sem resposta antes do pedido é considerado** campo, altere o valor predefinido de 3 para um valor entre 30 e 60 segundos.
7. No **número de segundos entre pedidos quando o servidor é identificado como não disponível** campo, altere o valor predefinido de 30 segundos para um valor que seja igual ou superior ao valor que especificou no passo anterior.

 ![Editar servidor Radius](./media/nps-extension-remote-desktop-gateway/image14.png)

8.  Clique em **OK** duas vezes para fechar as caixas de diálogo.

### <a name="verify-connection-request-policies"></a>Certifique-se de políticas de pedido de ligação 
Por predefinição, quando configurar o Gateway de RD para utilizar um arquivo de política central de políticas de autorização de ligação, o Gateway de RD está configurado para reencaminhar pedidos de extremidade para o servidor NPS. O servidor NPS com o MFA do Azure de extensão instaladas, processa o pedido de acesso RADIUS. Os passos seguintes mostram como verificar a política de pedido de ligação predefinido. 

1. No Gateway de RD, na consola do NPS (Local), expanda **políticas**e selecione **políticas de pedido de ligação**.
2. Clique com botão direito **ligar políticas de pedido**e faça duplo clique **política de autorização do GATEWAY TS**.
3. No **propriedades da política de autorização do GATEWAY TS** caixa de diálogo, clique em de **definições** separador.
4. No **definições** separador, em ligação de reencaminhamento pedido, clique em **autenticação**. Cliente RADIUS está configurado para reencaminhar pedidos de autenticação.

 ![Definições de autenticação](./media/nps-extension-remote-desktop-gateway/image15.png)
 
5. Clique em **Cancelar**. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Configurar o NPS no servidor onde está instalada a extensão NPS
O servidor NPS de onde está instalada a extensão NPS deve ser capaz de trocar mensagens RADIUS com o servidor NPS no Gateway de ambiente de trabalho remoto. Para ativar o exchange esta mensagem, terá de configurar os componentes NPS no servidor onde está instalado o serviço de extensão NPS. 

### <a name="register-server-in-active-directory"></a>Registar servidor no Active Directory
Para funcionarem corretamente neste cenário, o servidor NPS tem de ser registado no Active Directory.

1. No servidor NPS, abra **Gestor de servidor**.
2. No Gestor de servidor, clique em **ferramentas**e, em seguida, clique em **servidor de políticas de rede**. 
3. Na consola do servidor de políticas de rede, faça duplo clique **NPS (Local)**e, em seguida, clique em **registar servidor no Active Directory**. 
4. Clique em **OK** duas vezes.

 ![Registar servidor no AD](./media/nps-extension-remote-desktop-gateway/image16.png)

5. Deixe a consola aberta para o procedimento seguinte.

### <a name="create-and-configure-radius-client"></a>Criar e configurar o cliente RADIUS 
O Gateway de ambiente de trabalho remoto tem de ser configurado como um cliente RADIUS para o servidor NPS. 

1. No servidor NPS em que a extensão NPS é instalada, além de **NPS (Local)** consola, faça duplo clique **clientes RADIUS** e clique em **novo**.

 ![Novos clientes RADIUS](./media/nps-extension-remote-desktop-gateway/image17.png)

2. No **novo cliente RADIUS** diálogo caixa, forneça um nome amigável, tais como _Gateway_e o endereço IP ou nome DNS do servidor de Gateway de ambiente de trabalho remoto. 
3. No **segredo partilhado** e **segredo partilhado confirmar** campos, introduza o mesmo segredo que usou antes.

 ![Nome e endereço](./media/nps-extension-remote-desktop-gateway/image18.png)

4. Clique em **OK** para fechar a caixa de diálogo novo cliente RADIUS.

### <a name="configure-network-policy"></a>Configurar a política de rede
Recuperar-se de que o servidor NPS com a extensão de MFA do Azure é o arquivo de designado política central para a política de autorização de ligação (CAP). Por conseguinte, terá de implementar um limite no servidor NPS para autorizar os pedidos de ligações válido.  

1. Na consola do NPS (Local), expanda **políticas**e clique em **políticas de rede**.
2. Clique com botão direito **ligações para outros servidores de acesso**e clique em **duplicado política**. 

 ![Política de duplicação](./media/nps-extension-remote-desktop-gateway/image19.png)

3. Clique com botão direito **cópia de ligações para outros servidores de acesso**e clique em **propriedades**.

 ![Propriedades da rede](./media/nps-extension-remote-desktop-gateway/image20.png)

4. No **cópia de ligações para outros servidores de acesso** caixa de diálogo **nome da política**, introduza um nome adequado, como _RDG_CAP_. Verifique **política ativada**e selecione **conceder acesso**. Opcionalmente, na **tipo de servidor de acesso de rede**, selecione **Gateway de ambiente de trabalho remoto**, ou pode deixá-lo como **não especificado**.

 ![Cópia de ligações](./media/nps-extension-remote-desktop-gateway/image21.png)

5.  Clique em de **restrições** separador e verifique **permitir aos clientes ligar sem negociar um método de autenticação**.

 ![Permitir que os clientes ligar](./media/nps-extension-remote-desktop-gateway/image22.png)

6. Opcionalmente, clique em de **condições** separador e adicionar condições que devem ser satisfeitas para a ligação ser autorizado, por exemplo, a associação num grupo específico do Windows.

 ![Condições](./media/nps-extension-remote-desktop-gateway/image23.png)

7. Clique em **OK**. Quando lhe for pedido para ver o tópico de ajuda correspondente, clique em **não**.
8. Certifique-se de que a nova política é, na parte superior da lista, que a política for ativada, e concede acesso.

 ![Políticas de rede](./media/nps-extension-remote-desktop-gateway/image24.png)

## <a name="verify-configuration"></a>Verifique a configuração
Para verificar a configuração, tem de iniciar sessão para o Gateway de ambiente de trabalho remoto com um cliente RDP adequado. Lembre-se de que utilize uma conta que é permitida por políticas de autorização de ligação e está ativada para o MFA do Azure. 

Como mostrar na imagem abaixo, pode utilizar o **acesso Web de ambiente de trabalho remoto** página.

![Acesso Web a ambientes de trabalho remotos](./media/nps-extension-remote-desktop-gateway/image25.png)

Após introduzir com êxito as suas credenciais para autenticação primária, a caixa de diálogo ligação de ambiente de trabalho remoto mostra um Estado de iniciar a ligação remota, conforme mostrado abaixo. 

Se autenticar com êxito com o método de autenticação secundária que configurou anteriormente na MFA do Azure, estão ligados ao recurso. No entanto, se a autenticação secundária não for bem sucedida, for negado o acesso ao recurso. 

![Iniciar a ligação remota](./media/nps-extension-remote-desktop-gateway/image26.png)

No exemplo abaixo, a aplicação de autenticador num Windows phone é utilizada para fornecer a autenticação secundária.

![Contas](./media/nps-extension-remote-desktop-gateway/image27.png)

Depois de ter autenticadas com êxito utilizando o método de autenticação secundária, são registadas no Gateway de ambiente de trabalho remoto como habitualmente. No entanto, porque são necessários para utilizar um método de autenticação secundária utilizando uma aplicação móvel num dispositivo fidedigno, o processo de início de sessão é mais seguro do que seria, caso contrário.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Ver registos do Visualizador de eventos para eventos de início de sessão com êxito
Para ver os eventos de início de sessão com êxito nos registos do Visualizador de eventos do Windows, pode emitir o comando do Windows PowerShell seguinte para consultar os registos de serviços de Terminal do Windows e a segurança do Windows.

Para consultar os eventos de início de sessão com êxito nos registos operacionais Gateway _(Visualizador de eventos e serviços Logs\Microsoft\Windows\TerminalServices-Gateway\Operational)_, utilize os seguintes comandos do PowerShell:

* _Get-WinEvent - Logname Microsoft-Windows-TerminalServices-Gateway/operacional_ | em que {$ . ID - eq '300'} | FL 
* Este comando apresenta os eventos do Windows que mostram o cumpridos os requisitos de política de autorização de recursos (RAP de RD) e o utilizador foi concedido acesso.

![Visualizador de eventos de vista](./media/nps-extension-remote-desktop-gateway/image28.png)

* _Get-WinEvent - Logname Microsoft-Windows-TerminalServices-Gateway/operacional_ | em que {$ . ID - eq '200'} | FL
* Este comando apresenta os eventos que mostram quando o utilizador cumpridos os requisitos de política de autorização de ligação.

![Autorização de ligação](./media/nps-extension-remote-desktop-gateway/image29.png)

Também pode ver este registo e o filtro de evento IDs, 300 e 200. Para consultar os eventos de início de sessão bem-sucedidos nos registos do Visualizador de eventos de segurança, utilize o seguinte comando:

* _Get-WinEvent - Logname segurança_ | em que {$ . ID - eq '6272'} | FL 
* Este comando pode ser executado o NPS central ou o servidor de Gateway de RD. 

![Eventos de início de sessão com êxito](./media/nps-extension-remote-desktop-gateway/image30.png)

Também pode ver o registo de segurança ou a vista personalizada serviços de acesso e política de rede, como mostrado abaixo:

![Serviços de acesso e política de rede](./media/nps-extension-remote-desktop-gateway/image31.png)

No servidor onde instalou a extensão NPS para o MFA do Azure, pode encontrar os registos de aplicações do Visualizador de eventos específicos para a extensão no _aplicações e serviços Logs\Microsoft\AzureMfa_. 

![Registos de aplicações do Visualizador de eventos](./media/nps-extension-remote-desktop-gateway/image32.png)

## <a name="troubleshoot-guide"></a>Guia de resolução de problemas

Se a configuração não está a funcionar conforme esperado, o primeiro local para iniciar a resolução de problemas é verificar se o utilizador está configurado para utilizar o MFA do Azure. Tem do utilizador ligar à [portal do Azure](https://portal.azure.com). Se os utilizadores recebem um pedido de verificação secundária e com êxito podem autenticar, pode eliminar uma configuração incorreta do MFA do Azure.

Se o MFA do Azure está a funcionar para o es, deve rever os registos de eventos relevantes. Estes incluem os eventos de segurança, Gateway operacional e registos de MFA do Azure que são abordados na secção anterior. 

Segue-se um exemplo da saída de registo de segurança que mostra um evento de início de sessão falhadas (evento ID 6273).

![Eventos de início de sessão falhadas](./media/nps-extension-remote-desktop-gateway/image33.png)

Segue-se um evento dos registos de AzureMFA relacionado:

![Registo do MFA do Azure](./media/nps-extension-remote-desktop-gateway/image34.png)

Para efetuar avançadas de opções de resolução de problemas, consulte os ficheiros de registo do formato do NPS da base de dados onde o serviço NPS está instalado. Estes ficheiros de registo são criados no _%SystemRoot%\System32\Logs_ pasta ficheiros de texto delimitado por vírgulas. 

Para obter uma descrição destes ficheiros de registo, consulte [interpretar NPS base de dados de registo de ficheiros de formato](https://technet.microsoft.com/library/cc771748.aspx). As entradas nestes ficheiros de registo podem ser difícil interpretar sem os importar para uma folha de cálculo ou uma base de dados. Pode encontrar várias parsers de IAS online para ajudá-lo a interpretar os ficheiros de registo. 

A imagem abaixo mostra a saída de um desses transferível [shareware aplicação](http://www.deepsoftware.com/iasviewer). 

![Aplicação de shareware](./media/nps-extension-remote-desktop-gateway/image35.png)

Por fim, para mais opções de resolução de problemas, pode usar um analisador de protocolos, essas [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). 

A imagem abaixo da Microsoft Message Analyzer mostra o tráfego de rede filtrado no protocolo RADIUS que contém o nome de utilizador **Contoso\AliceC**.

![Analisador de mensagens da Microsoft](./media/nps-extension-remote-desktop-gateway/image36.png)

## <a name="next-steps"></a>Passos seguintes
[How to get Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md) (Como obter a Multi-Factor Authentication do Azure)

[Gateway de Ambiente de Trabalho Remoto e Servidor Multi-Factor Authentication do Azure com o RADIUS](multi-factor-authentication-get-started-server-rdg.md)

[Integrar os diretórios no local ao Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md)
