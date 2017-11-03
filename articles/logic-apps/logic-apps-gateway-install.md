---
title: Instalar o gateway de dados no local - Azure Logic Apps | Microsoft Docs
description: "Antes de aceder a origens de dados no local, instale o gateway de dados no local para a transferência de dados rápida e encriptação entre origens de dados no local e as logic apps"
keywords: "aceder a dados no local, a transferência de dados, a encriptação e origens de dados"
services: logic-apps
documentationcenter: 
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 47e3024e-88a0-4017-8484-8f392faec89d
ms.service: logic-apps
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/14/2017
ms.author: LADocs; millopis; estfan
ms.openlocfilehash: b3c1e2afadea91f010c3e4b43206b6d30a75ec38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="install-the-on-premises-data-gateway-for-azure-logic-apps"></a>Instalar o gateway de dados no local para Azure Logic Apps

As logic apps podem aceder a origens de dados no local, tem de instalar e configurar o gateway de dados no local. O gateway funciona como uma ponte que fornece a transferência de dados rápida e encriptação entre sistemas no local e as logic apps. O gateway transmite dados a partir de origens do local em canais encriptados através do Service Bus do Azure. Todo o tráfego origina como tráfego de saída seguro do agente de gateway. Saiba mais sobre [como funciona o gateway de dados](#gateway-cloud-service).

O gateway suporta ligações para estas origens de dados no local:

*   BizTalk Server 2016
*   DB2  
*   Sistema de Ficheiros
*   Informix
*   MQ
*   MySQL
*   Base de dados Oracle
*   PostgreSQL
*   Servidor de aplicações SAP 
*   Servidor de mensagens SAP
*   SharePoint
*   SQL Server
*   Teradata

Estes passos mostram como primeiro de instalar o gateway de dados no local antes de [configurar uma ligação entre o gateway e as logic apps](./logic-apps-gateway-connection.md). Para obter mais informações sobre conectores suportados, consulte [conectores para o Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list). 

Para obter informações sobre como utilizar o gateway com outros serviços, consulte estes artigos:

*   [Gateway de dados do Microsoft Power BI no local](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Gateway de dados no local do Analysis Services do Azure](../analysis-services/analysis-services-gateway.md)
*   [Gateway de dados do Microsoft Flow no local](https://flow.microsoft.com/documentation/gateway-manage/)
*   [Gateway de dados do Microsoft PowerApps no local](https://powerapps.microsoft.com/tutorials/gateway-management/)

<a name="requirements"></a>

## <a name="requirements"></a>Requisitos

**Mínimo**:

* 4.5 do .NET framework
* versão de 64 bits do Windows 7 ou Windows Server 2008 R2 (ou posterior)

**Recomendado**:

* CPU de 8 núcleos
* 8 GB de memória
* versão de 64 bits do Windows 2012 R2 (ou posterior)

**Considerações importantes sobre**:

* Instale o gateway de dados no local apenas num computador local.
Não é possível instalar o gateway num controlador de domínio.

   > [!TIP]
   > Não tem de instalar o gateway no mesmo computador como a origem de dados. Para minimizar a latência, pode instalar o gateway como fechar quanto possível para a origem de dados, ou no mesmo computador, partindo do princípio que tem permissões.

* Não instale o gateway num computador que o se desligar, entra em suspensão, ou não ligar à Internet, porque o gateway não é possível executar esses circunstâncias. Além disso, o desempenho de gateway poderão afetado através de uma rede sem fios.

* Durante a instalação, tem de iniciar sessão com um [conta escolar ou profissional](https://docs.microsoft.com/azure/active-directory/sign-up-organization) que é gerido pelo Azure Active Directory (Azure AD), não uma conta Microsoft.

  > [!TIP]
  > Se pretender utilizar uma conta Microsoft que tem um Visual Studio com a subscrição do MSDN, primeiro [criar um diretório (inquilino) no Azure Active Directory](../active-directory/develop/active-directory-howto-tenant.md) com a sua conta Microsoft, ou utilize o diretório predefinido. Adicionar um utilizador com uma palavra-passe para o diretório e, em seguida, atribuir esse acesso de utilizador à sua subscrição. Em seguida, pode iniciar sessão durante a instalação do gateway com este nome de utilizador e palavra-passe.

  Tem de utilizar a mesma conta escolar ou profissional mais tarde no portal do Azure ao criar e associar um recurso de gateway com a instalação do gateway. Em seguida, selecione este recurso de gateway ao criar a ligação entre a sua aplicação lógica e a origem de dados no local. [Por que motivo deve utilizar um trabalho do Azure AD ou escola conta?](#why-azure-work-school-account)

  > [!TIP]
  > Se se inscreveu no Office 365 uma oferta de serviço e não fornecer o seu e-mail de trabalho real, o seu endereço de início de sessão poderá aspeto jeff@contoso.onmicrosoft.com. 

* Se tiver um gateway existente que configurou com um programa de instalação que seja anterior à versão 14.16.6317.4, não é possível alterar a localização do seu gateway ao executar o programa de instalação mais recente. No entanto, pode utilizar o instalador mais recente para configurar um gateway novo com a localização que pretende que o em vez disso.
  
  Se tiver um instalador do gateway que seja anterior à versão 14.16.6317.4, mas ainda não instalou o gateway ainda, pode transferir e utilizar o programa de instalação mais recente.

<a name="install-gateway"></a>

## <a name="install-the-data-gateway"></a>Instalar o gateway de dados

1.  [Transfira e execute o instalador do gateway num computador local](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

2. Reveja e aceite os termos de utilização e declaração de privacidade.

3. Especifique o caminho no seu computador local onde pretende instalar o gateway.

4. Quando lhe for solicitado, inicie sessão com o trabalho do Azure ou conta profissional, não uma conta Microsoft.

   ![Inicie sessão com o trabalho do Azure conta escolar ou profissional](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. Agora registar o gateway instalado com o [serviço em nuvem gateway](#gateway-cloud-service). Escolha **registar um novo gateway neste computador**.

   O serviço de nuvem do gateway encripta e armazena as credenciais da origem de dados e os detalhes do gateway. 
   O serviço também encaminha consultas e os respetivos resultados entre a sua aplicação lógica, o gateway de dados no local e a origem de dados no local.

6. Forneça um nome para a sua instalação do gateway. Criar uma chave de recuperação, em seguida, confirme a sua chave de recuperação. 

   > [!IMPORTANT] 
   > A chave de recuperação tem de conter, pelo menos, oito carateres. Certifique-se de que guarde e manter a chave num local seguro. Também precisa desta chave quando se pretende migrar, restaure ou assuma um gateway existente.

   1. Para alterar a região predefinida para o serviço em nuvem gateway e utilizadas pela sua instalação do gateway de Service Bus do Azure, escolha **alterar região**.

      ![Região de alteração](./media/logic-apps-gateway-install/change-region-gateway-install.png)

      A região predefinida é a região associada do seu inquilino do Azure AD.

   2. No painel seguinte, abra o **selecione região** para escolher uma região diferente.

      ![Selecione outra região](./media/logic-apps-gateway-install/select-region-gateway-install.png)

      Por exemplo, poderá selecionar a mesma região que a sua aplicação lógica, ou selecione a região mais próxima da sua origem de dados no local, pelo que pode reduzir a latência. A aplicação de recursos e a lógica do gateway pode ter diferentes localizações.

      > [!IMPORTANT]
      > Não é possível alterar esta região após a instalação. Esta região também determina e restringe a localização onde pode criar o recurso do Azure para o seu gateway. Por isso, ao criar o recurso do gateway no Azure, certifique-se de que a localização do recurso corresponde à região que selecionou durante a instalação do gateway.
      > 
      > Se pretender utilizar uma região diferente para o seu gateway mais tarde, tem de configurar um novo gateway.

   3. Quando estiver pronto, selecione **feito**.

7. Agora, siga estes passos no portal do Azure para poder [criar um recurso do Azure para o seu gateway](../logic-apps/logic-apps-gateway-connection.md). 

Saiba mais sobre [como funciona o gateway de dados](#gateway-cloud-service).

## <a name="migrate-restore-or-take-over-an-existing-gateway"></a>Migre, restaure ou assuma um gateway existente

Para efetuar estas tarefas, tem de ter a chave de recuperação que foi especificada quando o gateway foi instalado.

1. No menu de início do seu computador, escolha **gateway de dados no local**.

2. Depois do instalador abre, inicie sessão com o trabalho do Azure mesmo conta escolar ou profissional que foi utilizada anteriormente a instalação do gateway.

3. Escolha **migrar, restaure ou assuma um gateway existente**.

4. Forneça a chave de recuperação e o nome do gateway que pretende migrar, restaure ou assuma.

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Conta de serviço do Windows

O gateway de dados no local é executado como um serviço do Windows e está configurado para utilizar `NT SERVICE\PBIEgwService` para o Windows service credenciais de início de sessão. Por predefinição, o gateway tem o direito "Iniciar sessão como um serviço" para a máquina em que a instalação do gateway. Para criar e manter o gateway no portal do Azure, a conta de serviço do Windows tem de ter, pelo menos, **contribuinte** permissões. 

> [!NOTE]
> A conta de serviço do Windows é diferente da conta utilizada para ligar aos dados no local origens e do Azure conta escolar ou profissional utilizada para iniciar sessão em serviços em nuvem.

<a name="restart-gateway"></a>

## <a name="restart-the-gateway"></a>Reinicie o gateway

Como qualquer outro serviço do Windows, pode iniciar e parar o serviço de várias formas. Por exemplo, pode abrir uma linha de comandos com permissões elevadas no computador onde o gateway está em execução e execute ambos estes comandos:

* Para parar o serviço, execute este comando:
  
    `net stop PBIEgwService`

* Para iniciar o serviço, execute este comando:
  
    `net start PBIEgwService`

## <a name="configure-a-firewall-or-proxy"></a>Configurar uma firewall ou proxy

O gateway cria uma ligação de saída para [Service Bus do Azure](https://azure.microsoft.com/services/service-bus/). Para fornecer informações de proxy para o seu gateway, consulte [configurar definições de proxy](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy/).

Para verificar se a sua firewall ou proxy, poderá bloquear as ligações, confirme se a máquina, na verdade, pode ligar à internet e o [Service Bus do Azure](https://azure.microsoft.com/services/service-bus/). A partir da linha de comandos do PowerShell, execute este comando:

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> Este comando só testa a conectividade de rede e a conectividade para o Service Bus do Azure. Por isso, o comando não tem nada a fazer com o gateway ou o serviço de nuvem do gateway que encripta e armazena as credenciais e os detalhes do gateway. 
>
> Além disso, este comando só está disponível no Windows Server 2012 R2 ou posterior e o Windows 8.1 ou posterior. Em versões anteriores do SO, pode utilizar o Telnet para testar a conectividade. Saiba mais sobre [soluções de Service Bus do Azure e híbridas](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

Os resultados devem ter um aspeto semelhantes para este exemplo:

```text
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Se **TcpTestSucceeded** não está definido como **verdadeiro**, poderá ser bloqueado por uma firewall. Se pretender ser abrangente, substitua o **ComputerName** e **porta** valores com os valores listados em [configurar portas](#configure-ports) neste tópico.

A firewall também poderá bloquear as ligações que o Service Bus do Azure torna dos centros de dados do Azure. Se acontecer neste cenário, aprovar (desbloquear) todos os endereços IP para os centros de dados na sua região. Para esses endereços IP, [obter a lista de endereços IP de Azure aqui](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="configure-ports"></a>Configurar portas

O gateway cria uma ligação de saída para [Service Bus do Azure](https://azure.microsoft.com/services/service-bus/) e comunica nas portas de saída: TCP 443 (predefinição), 5671, 5672, 9350 através de 9354. O gateway não necessita de porta de entrada. Saiba mais sobre [soluções de Service Bus do Azure e híbridas](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

| NOMES DE DOMÍNIO | PORTAS DE SAÍDA | DESCRIÇÃO |
| --- | --- | --- |
| *. analysis.windows.net | 443 | HTTPS | 
| *. login.windows.net | 443 | HTTPS | 
| *. servicebus.windows.net | 5671-5672 | (AMQP) do protocolo de colocação de mensagens de avançadas | 
| *. servicebus.windows.net | 443, 9350-9354 | Serviços de escuta barramento de serviço de reencaminhamento através de TCP (443 é necessária para a aquisição de token de controlo de acesso) | 
| *. frontend.clouddatahub.net | 443 | HTTPS | 
| *. core.windows.net | 443 | HTTPS | 
| login.microsoftonline.com | 443 | HTTPS | 
| *. msftncsi.com | 443 | Utilizado para testar a conectividade à internet quando o gateway não está acessível pelo serviço Power BI. | 

Se tiver de aprovar endereços IP em vez dos domínios, pode transferir e utilizar o [lista de intervalos de IP de centro de dados do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Em alguns casos, as ligações de barramento de serviço do Azure são efetuadas com endereço IP, em vez de nomes de domínio completamente qualificado.

<a name="gateway-cloud-service"></a>
## <a name="how-does-the-data-gateway-work"></a>Como funciona o gateway de dados?

O gateway de dados facilita a comunicação de um processo rápida e segura entre a sua aplicação lógica, o serviço de nuvem de gateway e a origem de dados no local. 

![Diagram-for-On-Premises-data-gateway-flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

Assim, quando o utilizador na nuvem interage com um elemento que está ligado à sua origem de dados no local:

1. O serviço de nuvem de gateway cria uma consulta, juntamente com as credenciais para a origem de dados encriptadas e envia a consulta para a fila para o gateway processar.

2. O serviço de nuvem do gateway analisa a consulta e envia o pedido para o Service Bus do Azure.

3. O gateway de dados no local consulta o Service Bus do Azure para pedidos pendentes.

4. O gateway obtém a consulta, desencripta as credenciais e estabelece ligação à origem de dados com essas credenciais.

5. O gateway envia a consulta para a origem de dados para execução.

6. Os resultados são enviados da origem de dados, de volta para o gateway e, em seguida, ao serviço de nuvem do gateway. O serviço de gateway de nuvem, em seguida, utiliza os resultados.

<a name="faq"></a>
## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="general"></a>Geral

**Q**: É necessário um gateway para origens de dados na nuvem, como o SQL Azure? <br/>
**A**: não. Um gateway se ligar a origens de dados no local apenas.

**Q**: ao gateway tem de ser instalado no mesmo computador como origem de dados? <br/>
**A**: não. O gateway se ligar à origem de dados utilizando as informações de ligação que foi fornecidas. Considere o gateway como uma aplicação de cliente neste sentido. O gateway tem apenas a capacidade para estabelecer ligação com o nome do servidor que foi fornecido.

<a name="why-azure-work-school-account"></a>

**Q**: por que motivo deve posso utilizar um trabalho do Azure conta escolar ou profissional para iniciar sessão? <br/>
**A**: pode apenas utilizar um trabalho do Azure conta escolar ou profissional quando a instalação do gateway de dados no local. A conta de início de sessão é armazenada num inquilino gerida pelo Azure Active Directory (Azure AD). Normalmente, o nome de principal de utilizador (UPN) da sua conta do Azure AD corresponde o endereço de e-mail.

**Q**: onde são as minhas credenciais armazenadas? <br/>
**A**: as credenciais que introduziu para uma origem de dados são encriptadas e armazenadas no serviço de nuvem do gateway. As credenciais são desencriptadas no gateway de dados no local.

**Q**: existem quaisquer requisitos de largura de banda de rede? <br/>
**A**: Recomendamos que a ligação de rede tem boa débito. Cada ambiente é diferente e a quantidade de dados enviados afeta os resultados. Com o ExpressRoute pode ajudar a garantir um nível de débito no local e os datacenters do Azure.
Pode utilizar a aplicação de teste de velocidade do Azure da ferramenta de terceiros para ajudar a avaliar o débito.

**Q**: qual é a latência de execução de consultas para uma origem de dados do gateway? O que é a arquitetura melhor? <br/>
**A**: para reduzir a latência de rede, instalar o gateway tão próximos a origem de dados quanto possível. Se pode instalar o gateway na origem de dados real, este proximidade minimiza a latência introduzida. Considere demasiado dos centros de dados. Por exemplo, se o serviço utiliza o Centro de dados de EUA oeste e tiver o SQL Server alojado numa VM do Azure, a VM do Azure deve estar no EUA oeste demasiado. Este proximidade minimiza a latência e evita os encargos associados à saída na VM do Azure.

**Q**: como são resultados enviados para a nuvem? <br/>
**A**: os resultados são enviados através do Service Bus do Azure.

**Q**: existem quaisquer ligações de entrada para o gateway da nuvem? <br/>
**A**: não. O gateway utiliza ligações de saída para o Service Bus do Azure.

**Q**: E se bloquear o ligações de saída? O que é necessário abrir? <br/>
**A**: consulte as portas e os anfitriões que utiliza o gateway.

**Q**: o que o serviço do Windows real denomina?<br/>
**A**: nos serviços, o gateway denomina-serviço do Power BI Enterprise Gateway.

**Q**: o serviço Windows do gateway pode executar com uma conta do Azure Active Directory? <br/>
**A**: não. O serviço do Windows tem de ter uma conta do Windows válida. Por predefinição, o serviço seja executado com o SID de serviço, NT SERVICE\PBIEgwService.

### <a name="high-availability-and-disaster-recovery"></a>Elevada disponibilidade e recuperação após desastre

**Q**: que opções estão disponíveis para recuperação após desastre? <br/>
**A**: pode utilizar a chave de recuperação para restaurar ou mover um gateway. Quando a instalação do gateway, especifique a chave de recuperação.

**Q**: qual é a vantagem da chave de recuperação? <br/>
**A**: A chave de recuperação fornece uma forma para migrar ou recuperar as definições do gateway após desastres.

**Q**: existem quaisquer planos para ativar cenários de elevada disponibilidade com o gateway? <br/>
**A**: estes cenários estão no plano, mas ainda não temos uma linha cronológica.

## <a name="troubleshooting"></a>Resolução de problemas

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**Q**: como posso ver quais os consultas que estão a ser enviados para a origem de dados no local? <br/>
**A**: pode ativar o rastreio de consulta, que inclui as consultas que são enviadas. Lembre-se alterar a consulta novamente rastreio para o valor original quando terminar de resolução de problemas. Abandonar o fileparser o rastreio de consulta ativado cria registos de maior.

Também pode ver as ferramentas que tenha a sua origem de dados para consultas de rastreio. Por exemplo, pode utilizar o evento expandido ou gerador de perfis do SQL Server para o SQL Server e do Analysis Services.

**Q**: onde estão os registos do gateway? <br/>
**A**: Consulte ferramentas posterior deste tópico.

### <a name="update-to-the-latest-version"></a>Atualizar para a versão mais recente

Muitos problemas podem superfície quando a versão do gateway fica desatualizada. Como uma boa prática geral, certifique-se de que utiliza a versão mais recente. Se o gateway ainda não atualizado durante um mês ou mais, que considere instalar a versão mais recente do gateway e veja se pode reproduzir o problema.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Erro: Falha ao adicionar o utilizador ao grupo. (-2147463168 PBIEgwService desempenho registo utilizadores)

Pode obter este erro se tentar instalar o gateway num controlador de domínio, que não é suportado. Certifique-se de que implementar o gateway num computador que não é um controlador de domínio.

## <a name="tools"></a>Ferramentas

### <a name="collect-logs-from-the-gateway-configurer"></a>Recolher registos de configurer o gateway

Pode recolher vários registos para o gateway. Inicie sempre com os registos!

#### <a name="installer-logs"></a>Registos do instalador

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>Registos de configuração

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>Registos do serviço de gateway de empresa

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>Registos de eventos

Pode encontrar os registos do Data Management Gateway e PowerBIGateway em **registos de serviços e aplicações**.

### <a name="fiddler-trace"></a>Rastreio fiddler

[Fiddler](http://www.telerik.com/fiddler) é uma ferramenta gratuita da Telerik que monitoriza o tráfego HTTP. Pode ver este tráfego com o serviço de Power BI a partir do computador cliente. Este serviço pode mostrar erros e outras informações relacionadas.

## <a name="next-steps"></a>Passos seguintes
    
* [Ligar a dados no local a partir das logic apps](../logic-apps/logic-apps-gateway-connection.md)
* [Funcionalidades de integração do Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Conectores para aplicações lógicas do Azure](../connectors/apis-list.md)
