---
title: Ligar computadores ao OMS utilizando o Gateway do OMS | Microsoft Docs
description: "Ligar os seus dispositivos geridos pelo OMS e computadores monitorizados de Operations Manager com o Gateway do OMS para enviar dados para o serviço do OMS quando não têm acesso à Internet."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: magoedte;banders
ms.openlocfilehash: c09a01af8053feb4d5450b350503484507014765
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="connect-computers-without-internet-access-to-oms-using-the-oms-gateway"></a>Ligar a computadores sem acesso à Internet para OMS utilizando o Gateway do OMS

Este documento descreve a forma como o OMS-geridos e computadores do System Center Operations Manager monitorizados podem enviar dados para o serviço do OMS quando não têm acesso à Internet. O Gateway do OMS, que é um proxy de reencaminhamento de HTTP que suporte o HTTP de túnel utilizando o comando HTTP ligar, pode recolher dados e enviá-lo para o serviço do OMS em nome daqueles.  

O Gateway do OMS suporta:

* Os Runbook Workers híbridos da automatização do Azure  
* Computadores Windows com o Microsoft Monitoring Agent diretamente ligado a uma área de trabalho do OMS
* Computadores com Linux com o agente do OMS para Linux diretamente ligado a uma área de trabalho do OMS  
* System Center Operations Manager 2012 SP1 com UR7, o Operations Manager 2012 R2 UR3 ou o Operations Manager grupo de gestão de 2016 integrado com o OMS.  

Se as políticas de segurança de TI não permitir que os computadores na sua rede para ligar à Internet, tais como ponto de venda (POS) dispositivos ou servidores que suportam serviços de TI, mas é necessário para os ligar às OMS para gerir e monitorizá-las, pode ser configurados para comunicar diretamente com o Gateway do OMS para receber a configuração e reencaminhar dados em seu nome.  Se estes computadores estão configurados com o agente do OMS para ligar diretamente a uma área de trabalho do OMS, todos os computadores em vez disso, irão comunicar com o Gateway do OMS.  O gateway transfere dados dos agentes para OMS diretamente, não analisar quaisquer dados em trânsito.

Quando um grupo de gestão do Operations Manager está integrado com o OMS, os servidores de gestão podem ser configurados para ligar ao Gateway para receber as informações de configuração e enviar os dados recolhidos consoante a solução que tiver ativado o OMS.  Agentes do Operations Manager enviarem alguns dados, tais como alertas do Operations Manager, avaliação de configuração, espaço de instância e dados de capacidade para o servidor de gestão. Outros dados de elevado volume, tais como registos de IIS, o desempenho e eventos de segurança são enviados diretamente para o Gateway do OMS.  Se tiver um ou mais servidores de Gateway do Operations Manager implementados no DMZ ou outra rede isolado, para monitorizar os sistemas não fidedignos, não consegue comunicar com um Gateway do OMS.  Servidores de Gateway do Gestor de operações só podem reportar a um servidor de gestão.  Quando um grupo de gestão do Operations Manager estiver configurado para comunicar com o Gateway do OMS, as informações de configuração de proxy são distribuídas automaticamente para todos os computadores geridos por agente que está configurado para recolher dados para análise de registos, mesmo se a definição está vazia.    

Para fornecer elevada disponibilidade para direta ligado ou grupos de gestão de operações que comunicam com o OMS através do gateway, pode utilizar o balanceamento de carga na rede para redirecionar e distribuir o tráfego por vários servidores de gateway.  Se um servidor de gateway ficar inativo, o tráfego é redirecionado para outro nó disponível.  

Recomenda-se que instale o agente do OMS no computador com o software do OMS Gateway para monitorizar o Gateway do OMS e analisar dados de desempenho ou eventos. Além disso, o agente ajuda a identificar os pontos finais de serviço que necessita para comunicar com o Gateway de OMS.

Cada agente tem de ter conectividade de rede para o gateway para que os agentes podem ser transferidos automaticamente dados de e para o gateway. Não é recomendável instalar o gateway num controlador de domínio.

O diagrama seguinte mostra o fluxo de dados de agentes diretos, OMS através do servidor de gateway.  Agentes tem de ter a configuração de proxy corresponder a mesma porta que o Gateway do OMS está configurado para comunicar com o OMS.  

![comunicação do agente direta com diagrama do OMS](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

O diagrama seguinte mostra o fluxo de dados de um grupo de gestão do Operations Manager para OMS.   

![Comunicações do Gestor de operações com diagrama do OMS](./media/log-analytics-oms-gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>Pré-requisitos

Ao designar um computador a executar o Gateway do OMS, este computador tem de ter o seguinte:

* Windows 10, Windows 8.1, Windows 7
* Windows Server 2016, o Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, Windows Server 2008
* .NET framework 4.5
* Mínimo de 8 GB de memória e 4 núcleos processador 

### <a name="language-availability"></a>Disponibilidade de idioma

O Gateway do OMS está disponível nos seguintes idiomas:

- Chinês (simplificado)
- Chinês (tradicional)
- Checo
- Neerlandês
- Português
- Francês
- Alemão
- Húngaro
- Italiano
- Japonês
- Coreano
- Polaco
- Português (Brasil)
- Português (Portugal)
- Russo
- Espanhol (internacional)

### <a name="supported-encryption-protocols"></a>Protocolos de encriptação suportados
O Gateway do OMS só suporta Transport Layer Security (TLS) 1.0, 1.1 e 1.2.  Não suporta Secure Sockets Layer (SSL).

## <a name="download-the-oms-gateway"></a>Transfira o Gateway do OMS

Existem três formas de obter a versão mais recente do ficheiro de configuração do Gateway OMS.

1. Transferir a partir de [Centro de transferências da Microsoft](https://www.microsoft.com/download/details.aspx?id=54443).

2. Transferir a partir do portal do OMS.  Depois de iniciar sessão sua área de trabalho do OMS, navegue para **definições** > **origens ligadas** > **servidores Windows** e clique em **transferir Gateway do OMS**.

3. Transferir a partir de [portal do Azure](https://portal.azure.com).  Após iniciar sessão:  

   1. Procurar a lista de serviços e, em seguida, selecione **Log Analytics**.  
   2. Selecione uma área de trabalho.
   3. No painel da área de trabalho em **geral**, clique em **início rápido**.
   4. Em **escolha uma origem de dados para ligar à área de trabalho**, clique em **computadores**.
   5. No **agente direta** painel, clique em **transferir Gateway do OMS**.<br><br> ![Transfira o Gateway do OMS](./media/log-analytics-oms-gateway/download-gateway.png)


## <a name="install-the-oms-gateway"></a>Instalar o Gateway do OMS

Para instalar um gateway, execute os seguintes passos.  Se tiver instalado uma versão anterior, anteriormente denominadas *reencaminhador de análise do registo*, será atualizada para esta versão.  

1. A partir da pasta de destino, faça duplo clique em **OMS Gateway.msi**.
2. No **boas-vindas** página, clique em **seguinte**.<br><br> ![Assistente de configuração do gateway](./media/log-analytics-oms-gateway/gateway-wizard01.png)<br> 
3. No **contrato de licença** página, selecione **aceito os termos no contrato de licença** para aceitar o EULA e, em seguida, clique em **seguinte**.
4. No **porta e o proxy de endereço** página:
   1. Escreva o número da porta TCP para ser utilizado para o gateway. Programa de configuração configura uma regra de entrada com este número de porta na firewall do Windows.  O valor predefinido é 8080.
      O intervalo válido do número de porta é 1 e 65535. Se a entrada não se enquadram este intervalo, é apresentada uma mensagem de erro.
   2. Opcionalmente, se o servidor onde o gateway está instalado tem de comunicar através de um proxy, escreva o endereço de proxy onde o gateway tem de ser ligada. Por exemplo, `http://myorgname.corp.contoso.com:80`.  Se deixado em branco, o gateway irá tentar estabelecer ligação à Internet diretamente.  Se o servidor proxy requer autenticação, introduza um nome de utilizador e palavra-passe.<br><br> ![Configuração de proxy do Assistente de gateway](./media/log-analytics-oms-gateway/gateway-wizard02.png)<br>   
   3. Clique em **Seguinte**.
5. Se não tiver o Microsoft Update ativado, a Microsoft Update é apresentada a página onde pode optar por ativá-la. Fazer uma seleção e, em seguida, clique em **seguinte**. Caso contrário, avance para o passo seguinte.
6. No **pasta de destino** página, deixe a pasta predefinida c:\Programas\Microsoft Files\OMS Gateway ou escreva a localização onde pretende instalar o gateway e, em seguida, clique em **seguinte**.
7. No **pronto para instalar** página, clique em **instalar**. Controlo de conta de utilizador podem aparecer pedir permissão para instalar. Se assim for, clique em **Sim**.
8. Após a conclusão da configuração, clique em **concluir**. Pode verificar que o serviço está em execução ao abrir o snap-in services.msc e certifique-se de que **OMS Gateway** aparece na lista de serviços e o estado é **executar**.<br><br> ![Serviços – o Gateway do OMS](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>Configurar o balanceamento de carga de rede 
Pode configurar o gateway de elevada disponibilidade utilizando a rede balanceamento de carga (NLB da) Microsoft rede balanceamento de carga (NLB) ou balanceadores de carga baseada em hardware a utilizar.  O Balanceador de carga gere tráfego redirecionar as pedido ligações dos servidores de gestão de agentes do OMS ou o Operations Manager em todos os nós. Se um servidor de Gateway ficar inativo, o tráfego redirecionado para outros nós.

Para saber como estruturar e implementar uma cluster de balanceamento de carga na rede do Windows Server 2016, consulte [balanceamento de carga na rede](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  Os passos seguintes descrevem como configurar uma cluster de balanceamento de carga na rede Microsoft.  

1.  Inicie sessão no servidor do Windows que seja membro do cluster NLB com uma conta de administrador.  
2.  Abra o Gestor de balanceamento de carga na rede no Gestor de servidores, clique em **ferramentas**e, em seguida, clique em **Gestor de balanceamento de carga na rede**.
3. Para ligar um servidor de Gateway do OMS com o Microsoft Monitoring Agent instalada, clique no endereço IP do cluster e, em seguida, clique em **Adicionar anfitrião ao Cluster**.<br><br> ![Carga de rede Gestor de balanceamento – Adicionar anfitrião ao Cluster](./media/log-analytics-oms-gateway/nlb02.png)<br> 
4. Introduza o endereço IP do servidor de gateway que pretende ligar.<br><br> ![Rede do Gestor de balanceamento de carga – Adicionar anfitrião ao Cluster: ligar](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="configure-oms-agent-and-operations-manager-management-group"></a>Configurar o agente do OMS e o grupo de gestão do Operations Manager
A secção seguinte inclui os passos sobre como configurar ligadas diretamente agentes do OMS, um grupo de gestão do Operations Manager ou do Azure automatização de Runbook híbridos com o Gateway do OMS para comunicar com o OMS.  

Para compreender os requisitos e passos sobre como instalar o agente do OMS em computadores Windows ligar diretamente à OMS, consulte [computadores Windows ligar ao OMS](log-analytics-windows-agents.md) ou para ver de computadores Linux [computadores Linux ligar ao OMS](log-analytics-linux-agents.md). 

### <a name="configuring-the-oms-agent-and-operations-manager-to-use-the-oms-gateway-as-a-proxy-server"></a>Configurar o agente do OMS e o Operations Manager para utilizar o Gateway do OMS como um servidor proxy

### <a name="configure-standalone-oms-agent"></a>Configurar o agente do OMS autónomo
Consulte [configurar definições de proxy e de firewall com o Microsoft Monitoring Agent](log-analytics-proxy-firewall.md) para obter informações sobre como configurar um agente para utilizar um servidor proxy, que neste caso, é o gateway.  Se tiver implementado por trás de um balanceador de carga de rede de vários servidores de gateway, a configuração de proxy de agente do OMS é o endereço IP virtual do NLB:<br><br> ![Microsoft Monitoring Agent propriedades – definições do Proxy](./media/log-analytics-oms-gateway/nlb04.png)

### <a name="configure-operations-manager---all-agents-use-the-same-proxy-server"></a>Configurar o Operations Manager - todos os agentes utilizam o mesmo servidor proxy
Configurar o Operations Manager para adicionar o servidor de gateway.  A configuração de proxy do Operations Manager é aplicada automaticamente a todos os agentes que reportam ao Operations Manager, mesmo se a definição está vazia.

Para utilizar o Gateway para suportar o Operations Manager, tem de ter:

* O Microsoft Monitoring Agent (versão do agente – **8.0.10900.0** e posterior) instalado no servidor de Gateway e configurado para as áreas de trabalho do OMS com a qual pretende comunicar.
* O gateway tem de ter conectividade à Internet ou estar ligado a um servidor proxy que suporta.

> [!NOTE]
> Se não especificar um valor para o gateway, os valores em branco são enviadas por push para todos os agentes.


1. Abra a consola do Operations Manager e, em **Operations Management Suite**, clique em **ligação** e, em seguida, clique em **configurar o servidor de Proxy**.<br><br> ![O Operations Manager – configurar o servidor Proxy](./media/log-analytics-oms-gateway/scom01.png)<br> 
2. Selecione **utilizar um servidor proxy para aceder ao Operations Management Suite** e, em seguida, escreva o endereço IP do servidor de Gateway do OMS ou endereço IP virtual do NLB. Certifique-se de que inicie com o `http://` prefixo.<br><br> ![O Operations Manager – endereço do servidor proxy](./media/log-analytics-oms-gateway/scom02.png)<br> 
3. Clique em **Concluir**. O servidor de Operations Manager está ligado à sua área de trabalho do OMS.

### <a name="configure-operations-manager---specific-agents-use-proxy-server"></a>Configurar o Operations Manager - agentes específicos utilizam servidor proxy
Para ambientes de grandes ou complexas, só poderá servidores específicos (ou grupos) para utilizar o servidor de Gateway do OMS.  Para estes servidores, não é possível atualizar o agente do Operations Manager diretamente como este valor é substituído pelo valor global para o grupo de gestão.  Em vez disso, terá de substituir a regra utilizada para emitir estes valores.

> [!NOTE] 
> Esta mesma técnica de configuração pode ser utilizada para permitir a utilização de vários servidores de Gateway do OMS no seu ambiente.  Por exemplo, poderá ser necessário servidores de Gateway do OMS específicos especificado numa base por região.

1. Abra a consola do Operations Manager e selecione o **criação** área de trabalho.  
2. Na área de trabalho de criação de conteúdos, selecione **regras** e clique em de **âmbito** botão na barra de ferramentas do Operations Manager. Se este botão não estiver disponível, certifique-se certificar de que tem um objeto, não uma pasta, selecionado no painel de monitorização. O **objetos de pacote de gestão do âmbito** caixa de diálogo apresenta uma lista de classes de destino comuns, grupos ou objetos. 
3. Tipo **serviço de integridade** no **procure** campo e selecione-a partir da lista.  Clique em **OK**.  
4. Procure a regra **regra de definição de Proxy do Advisor** e na barra de ferramentas da consola de operações, clique em **substitui** e, em seguida, aponte para **o Rule\For um objeto específico da classe de substituição: serviço de integridade** e selecione um objeto específico da lista.  Opcionalmente, pode criar um grupo personalizado que contém o objeto de serviço do Estado de funcionamento dos servidores que pretende aplicar esta substituição para e, em seguida, aplicar a substituição para esse grupo.
5. No **propriedades da substituição** caixa de diálogo, clique para colocar uma marca de verificação no **substituir** coluna seguinte para o **WebProxyAddress** parâmetro.  No **valor de substituição** campo, introduza o URL do servidor de Gateway do OMS garantir que comece com o `http://` prefixo.
   >[!NOTE]
   > Não é necessário ativar a regra porque esta já é gerida automaticamente com uma substituição contida no pacote de gestão Microsoft System Center Advisor Secure referência Override direcionada para o Microsoft System Center Advisor monitorização grupo do servidor.
   > 
6. Selecione um pacote de gestão a **selecionar pacote de gestão de destino** lista ou crie um novo pacote de gestão não selado, clicando em **novo**. 
7. Quando concluir as suas alterações, clique em **OK**. 

### <a name="configure-for-automation-hybrid-workers"></a>Configurar para híbridos de automatização
Se tiver de automatização de Runbook híbridos no seu ambiente, os passos seguintes fornecem soluções manuais, temporárias para configurar o Gateway para os suportar.

Nos passos seguintes, tem de saber a região do Azure onde reside a conta de automatização. Para localizar a localização:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione o serviço de automatização do Azure.
3. Selecione a conta de automatização do Azure adequada.
4. Ver a região em **localização**.<br><br> ![Portal do Azure – localização da conta de automatização](./media/log-analytics-oms-gateway/location.png)  

Utilize as tabelas seguintes para identificar o URL para cada localização:

**URLs do serviço de dados de tempo de execução de tarefa**

| **localização** | **URL** |
| --- | --- |
| EUA Centro-Norte |ncus-jobruntimedata-prod-su1.azure-automation.net |
| Europa Ocidental |we-jobruntimedata-prod-su1.azure-automation.net |
| EUA Centro-Sul |scus-jobruntimedata-prod-su1.azure-automation.net |
| EUA Leste 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Canadá central |cc-jobruntimedata-prod-su1.azure-automation.net |
| Europa do Norte |ne-jobruntimedata-prod-su1.azure-automation.net |
| Sudeste Asiático |sea-jobruntimedata-prod-su1.azure-automation.net |
| Índia Central |cid-jobruntimedata-prod-su1.azure-automation.net |
| Japão |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Austrália |ase-jobruntimedata-prod-su1.azure-automation.net |

**URLs do serviço de agente**

| **localização** | **URL** |
| --- | --- |
| EUA Centro-Norte |ncus-agentservice-prod-1.azure-automation.net |
| Europa Ocidental |Iremos-agentservice-prod-1.azure-automation.net |
| EUA Centro-Sul |scus-agentservice-prod-1.azure-automation.net |
| EUA Leste 2 |eus2-agentservice-prod-1.azure-automation.net |
| Canadá central |Cc-agentservice-prod-1.azure-automation.net |
| Europa do Norte |ne-agentservice-prod-1.azure-automation.net |
| Sudeste Asiático |SEA-agentservice-prod-1.azure-automation.net |
| Índia Central |CID-agentservice-prod-1.azure-automation.net |
| Japão |jpe-agentservice-prod-1.azure-automation.net |
| Austrália |ase-agentservice-prod-1.azure-automation.net |

Se o computador está registado como um Runbook Worker híbrido automaticamente para a aplicação de patches utilizando a solução de gestão de atualizações, siga estes passos:

1. Adicione os URLs do serviço de dados de tempo de execução da tarefa para a lista de anfitriões permitido no Gateway do OMS. Por exemplo: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. Reinicie o serviço de Gateway do OMS, utilizando o cmdlet PowerShell seguinte:`Restart-Service OMSGatewayService`

Se o computador está integrada para a automatização do Azure utilizando o cmdlet de registo do Runbook Worker híbrido, siga estes passos:

1. Adicione o URL de registo do serviço de agente para a lista de anfitriões permitido no Gateway do OMS. Por exemplo: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. Adicione os URLs do serviço de dados de tempo de execução da tarefa para a lista de anfitriões permitido no Gateway do OMS. Por exemplo: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. Reinicie o serviço de Gateway do OMS.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Cmdlets do PowerShell útil
Os cmdlets podem ajudar a concluir as tarefas que são necessárias para atualizar as definições de configuração do Gateway OMS. Antes de utilizá-los, não se esqueça de:

1. Instale o Gateway do OMS (MSI).
2. Abra uma janela da consola do PowerShell.
3. Para importar o módulo, escreva este comando:`Import-Module OMSGateway`
4. Se nenhum erro no passo anterior, o módulo foi importado com êxito e os cmdlets podem ser utilizados. Tipo`Get-Module OMSGateway`
5. Depois de efetuar alterações ao utilizar os cmdlets, certifique-se de que reinicie o serviço de Gateway.

Se obtiver um erro no passo 3, o módulo não foi importado. O erro pode ocorrer quando não consegue localizar o módulo PowerShell. Pode encontrá-lo no caminho de instalação do Gateway: *C:\Program Files\Microsoft OMS Gateway\PowerShell*.

| **Cmdlet** | **Parâmetros** | **Descrição** | **Exemplo** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Chave |Obtém a configuração do serviço |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Chave (obrigatório) <br> Valor |Altera a configuração do serviço |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Obtém o endereço do reencaminhamento de proxy (montante) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Endereço<br> Nome de utilizador<br> Palavra-passe |Define o endereço (e a credencial) de proxy (montante) de reencaminhamento |1. Defina um proxy de reencaminhamento e da credencial:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Defina um proxy de reencaminhamento que não necessita de autenticação:`Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Desmarque a definição de proxy de reencaminhamento:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Obtém o anfitrião atualmente permitido (apenas localmente configurado permitido anfitrião, não inclui anfitriões permitidos transferidos automaticamente) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Anfitrião (obrigatório) |Adiciona o anfitrião à lista de permitidos |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Anfitrião (obrigatório) |Remove o anfitrião da lista de permitidos |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Assunto (obrigatório) |Adiciona o certificado de cliente sujeitos a lista de permitidos |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Assunto (obrigatório) |Remove o requerente do certificado de cliente da lista de permitidos |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Obtém o cliente atualmente permitido assuntos de certificado (apenas localmente configurado permitido assuntos, não inclua assuntos permitidos transferidos automaticamente) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Resolução de problemas
Para recolher eventos registados pelo gateway, tem também de ter instalado o agente do OMS.<br><br> ![Visualizador de eventos – registo de Gateway do OMS](./media/log-analytics-oms-gateway/event-viewer.png)

**Os IDs de evento de Gateway do OMS e descrições**

A tabela seguinte mostra os IDs de eventos e as descrições de eventos de registo de Gateway do OMS.

| **ID** | **Descrição** |
| --- | --- |
| 400 |Qualquer erro de aplicação que não tenha um ID específico |
| 401 |Configuração errada. Por exemplo: listenPort = "text", em vez de um número inteiro |
| 402 |Excepção na análise de mensagens de handshake TLS |
| 403 |Erro de rede. Por exemplo: não é possível ligar ao servidor de destino |
| 100 |Informações gerais |
| 101 |Serviço foi iniciado |
| 102 |Serviço foi parado |
| 103 |Foi recebido um comando HTTP ligar a partir do cliente |
| 104 |Não um comando HTTP ligar |
| 105 |Servidor de destino não está na lista permitida ou a porta de destino não é porta segura (443) <br> <br> Certifique-se de que o agente MMA no seu servidor de Gateway e os agentes de comunicar com o Gateway está ligado à mesma área de trabalho de análise de registos. |
| 105 |Erro TcpConnection – certificado de cliente inválido: CN = Gateway <br><br> Certifique-se de que: <br>    <br> &#149; Está a utilizar um Gateway com o número de versão 1.0.395.0 ou superior. <br> &#149; O agente MMA no seu servidor de Gateway e os agentes de comunicar com o Gateway está ligado ao mesmo espaço de trabalho de análise de registos. |
| 106 |O Gateway do OMS só suporta TLS 1.0, TLS 1.1 e 1.2.  Não suporta SSL. Para qualquer versão de protocolo do TLS/SSL não suportada, o OMS Gateway gera 106 de ID de evento.|
| 107 |A sessão TLS foi verificada |

**Contadores de desempenho para recolher**

A tabela seguinte mostra os contadores de desempenho disponíveis para o Gateway do OMS. Pode adicionar os contadores de Monitor de desempenho a utilizar.

| **Nome** | **Descrição** |
| --- | --- |
| Ligação de cliente de Gateway/ativo do OMS |Número de ligações de rede (TCP) do Active Directory do cliente |
| Contagem de erros/Gateway do OMS |Número de erros |
| Cliente de Gateway/ligado do OMS |Número de clientes ligados |
| Contagem de Gateway/rejeição do OMS |Número de rejeições devido a algum erro de validação de TLS |

![Contadores de desempenho do Gateway do OMS](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>Obter assistência
Quando que está a sessão iniciada no portal do Azure, pode criar um pedido de assistência com o Gateway do OMS ou qualquer outro serviço do Azure ou a funcionalidade de um serviço.
Para pedir assistência, clique no símbolo de ponto de interrogação por no canto superior direito do portal e, em seguida, clique em **novo pedido de suporte**. Em seguida, conclua o formulário de pedido de suporte de novo.

![Novo pedido de suporte](./media/log-analytics-oms-gateway/support.png)

Também pode deixar comentários sobre OMS ou análise de registos no [fórum de comentários do Microsoft Azure](https://feedback.azure.com/forums/267889).

## <a name="next-steps"></a>Passos seguintes
* [Adicionar origens de dados](log-analytics-data-sources.md) para recolher dados a partir de origens ligadas na sua área de trabalho de análise de registos e armazená-lo no repositório de análise de registos.
