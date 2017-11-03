---
title: "Solução de Monitor de desempenho no Log Analytics do Azure de rede | Microsoft Docs"
description: Monitor de desempenho de rede no Log Analytics do Azure ajuda a monitorizar o desempenho das suas redes-in quase real-time-to detetar e localizar congestionamentos de desempenho de rede.
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: banders
ms.openlocfilehash: 10e8eeaade5d51b1a15c30802b28600bcf6c72d9
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="network-performance-monitor-solution-in-log-analytics"></a>Solução de Monitor de desempenho na análise de registos de rede

![Símbolo de Monitor de desempenho de rede](./media/log-analytics-network-performance-monitor/npm-symbol.png)

Este documento descreve como configurar e utilizar a solução de Monitor de desempenho de rede na análise de registos, que ajuda a monitorizar o desempenho das suas redes-in quase real-time-to Deteta e localizar rede congestionamentos de desempenho. Com a solução de Monitor de desempenho de rede, pode monitorizar a perda e latência entre duas redes, servidores ou sub-redes. Monitor de desempenho de rede Deteta problemas de rede, como tráfego blackholing, encaminhamento erros e problemas que métodos de monitorização de rede convencional não são possível detetar. Monitor de desempenho de rede gera alertas e notifica como e quando um limiar é quebrado para uma ligação de rede. Estes limiares podem ser adquiridos automaticamente pelo sistema, ou pode configurar para utilizar as regras de alerta personalizadas. Monitor de desempenho de rede assegura uma deteção atempada do problemas de desempenho de rede e localizes a origem do problema para um segmento de rede específico ou o dispositivo.

Pode detetar problemas de rede com o dashboard de solução que apresenta as informações resumidas sobre a sua rede, incluindo eventos de estado de funcionamento de rede recentes, ligações de rede mau estado de funcionamento e ligações de sub-rede que estão a enfrentar perda de pacotes elevado e a latência. Pode desagregar para uma ligação de rede para ver o estado de funcionamento atual de ligações de sub-rede, bem como ligações de nó de nó. Também pode ver a tendência histórica de perda e latência de rede, a sub-rede e o nível de nó de nó. Pode detetar problemas de rede transitórios visualizando gráficos de tendências históricas para a perda de pacotes e a latência e localizar congestionamentos de rede num mapa de topologia. O gráfico de topologia interativa permite-lhe visualizar as rotas de rede de salto-por-hop e determinar a origem do problema. Como outras soluções, pode utilizar o registo de pesquisa para vários requisitos de análise para criar relatórios personalizados baseados nos dados recolhidos pelo Monitor de desempenho de rede.

A solução utiliza transações sintéticas como um mecanismo de principal para detetar falhas de rede. Por isso, pode utilizá-la sem regard de fabricante ou modelo de um dispositivo de rede específico. Funciona em ambientes híbridos, em nuvem (IaaS) e no local. A solução Deteta automaticamente a topologia de rede e várias rotas na sua rede.

Produtos de monitorização de rede típica focar-se sobre como monitorizar o estado de funcionamento do dispositivo (routers, comutadores, etc.) de rede, mas não fornecem informações sobre a qualidade real de conectividade de rede entre dois pontos, que é o Monitor de desempenho de rede.

### <a name="using-the-solution-standalone"></a>Utilizar a solução autónoma
Se pretender monitorizar a qualidade de ligações de rede entre as respetivas cargas de trabalho críticas, redes, centros de dados ou sites do office, em seguida, pode utilizar a solução de Monitor de desempenho de rede por si só para monitorizar o estado de funcionamento da conetividade entre:

* vários sites de centros de dados ou do office que são ligados através de uma rede pública ou privada
* cargas de trabalho críticas que estejam a executar aplicações empresariais de linha de
* Serviços de nuvem pública como o Microsoft Azure ou Amazon Web Services (AWS) e redes no local, se tiver IaaS (VM) disponível e se tiver gateways configurados para permitir a comunicação entre redes no local e redes em nuvem
* Redes do Azure e no local ao utilizar o Expressroute

### <a name="using-the-solution-with-other-networking-tools"></a>Utilizando a solução com outras ferramentas de rede
Se pretender monitorizar uma aplicação de linha de negócio, pode utilizar a solução de Monitor de desempenho de rede como uma solução de complemento para outras ferramentas de rede. Uma rede lenta pode levar a aplicações lentas e o Monitor de desempenho de rede pode ajudar a investigar problemas de desempenho de aplicações que são causados por problemas de rede subjacentes. Porque a solução não requer qualquer acesso aos dispositivos de rede, o administrador da aplicação não tem dependem de uma equipa de rede para fornecer informações sobre como a rede está a afetar as aplicações.

Além disso, se já investir em outra ferramentas de monitorização de rede, em seguida, a solução pode complementar essas ferramentas porque mais tradicionais soluções de monitorização de rede não fornecem informações sobre as métricas do desempenho de rede ponto a ponto, como a perda e latência.  A solução de Monitor de desempenho de rede pode ajudar a preencher desse intervalo.

## <a name="installing-and-configuring-agents-for-the-solution"></a>Instalar e configurar agentes para a solução
Utilize os processos de básico para instalar agentes em [computadores Windows ligar ao Log Analytics](log-analytics-windows-agents.md) e [estabelecer a ligação do Operations Manager ao Log Analytics](log-analytics-om-agents.md).

> [!NOTE]
> Terá de instalar pelo menos 2 agentes para tem dados suficientes para detetar e monitorizar os recursos de rede. Caso contrário, a solução irá permanecer num Estado de configuração depois de instalar e configurar agentes adicionais.
>
>

### <a name="where-to-install-the-agents"></a>Onde pretende instalar os agentes
Antes de instalar os agentes, considere a topologia da sua rede e as partes da rede que pretende monitorizar. Recomendamos que instale mais do que um agente para cada sub-rede que pretende monitorizar. Por outras palavras, para cada sub-rede que pretende monitorizar, escolha a dois ou mais servidores ou VMs e instalar o agente nos mesmos.

Se não souber sobre a topologia da sua rede, instale os agentes em servidores com cargas de trabalho críticas em que pretende monitorizar o desempenho da rede. Por exemplo, poderá pretender controlar uma ligação de rede entre um servidor Web e um servidor com o SQL Server. Neste exemplo, deverá instalar um agente em ambos os servidores.

Os agentes monitorizam a conectividade de rede (ligações) entre anfitriões – não os anfitriões próprios. Por isso, para monitorizar uma ligação de rede, tem de instalar os agentes em ambos os pontos finais dessa ligação.

### <a name="configure-agents"></a>Configurar os agentes

Se pretender utilizar o protocolo ICMP para transações sintéticas, terá de ativar as seguintes regras de firewall para a utilização de forma fiável ICMP:

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow
```


Se pretender utilizar o protocolo TCP, que é necessário abrir as portas de firewall para os computadores para se certificar de que os agentes podem comunicar. Terá de transferir e, em seguida, execute o [script do EnableRules.ps1 PowerShell](https://gallery.technet.microsoft.com/OMS-Network-Performance-04a66634) sem quaisquer parâmetros numa janela do PowerShell com privilégios administrativos.

O script cria as chaves de registo necessárias para o Monitor de desempenho de rede e cria as regras de firewall do Windows para permitir que os agentes para criar ligações TCP entre si. As chaves de registo criadas pelo script também especificar se pretende registar os registos de depuração e o caminho para o ficheiro de registos. Também define a porta TCP de agente utilizada para comunicação. Os valores para estas chaves são configurados automaticamente pelo script de, pelo que não deve alterar manualmente estas chaves.

A porta aberta por predefinição é 8084. Pode utilizar uma porta personalizada, fornecendo o parâmetro `portNumber` para o script. No entanto, a mesma porta deve ser utilizada em todos os computadores em que o script é executado.

> [!NOTE]
> O script de EnableRules.ps1 configura regras de firewall do Windows apenas no computador onde o script é executado. Se tiver uma firewall de rede, deve certificar-se de que permite o tráfego destinado a porta TCP que está a ser utilizada pelo Monitor de desempenho de rede.
>
>

## <a name="configuring-the-solution"></a>Configuração da solução
Utilize as seguintes informações para instalar e configurar a solução.

1. A solução de Monitor de desempenho de rede adquire dados a partir de computadores que executam o Windows Server 2008 SP 1 ou posterior ou Windows 7 SP1 ou posterior, que são os mesmos requisitos como o Microsoft Monitoring Agent (MMA). Agentes NPM também podem executar em sistemas de operativos de ambiente de trabalho/cliente Windows (Windows 10, Windows 8.1, Windows 8 e Windows 7).
    >[!NOTE]
    >Os agentes para sistemas operativos Windows server suportam tanto TCP como ICMP como os protocolos de transações sintéticas. No entanto, os agentes para sistemas operativos cliente Windows suportam apenas ICMP como o protocolo para transações sintéticas.

2. Adicionar a solução de Monitor de desempenho de rede para a sua área de trabalho de [do Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) ou utilizando o processo descrito no [soluções de análise de registos adicionar da galeria do soluções](log-analytics-add-solutions.md).<br><br> ![Símbolo de Monitor de desempenho de rede](./media/log-analytics-network-performance-monitor/npm-symbol.png)  
3. No portal do OMS, verá um novo mosaico intitulado **Monitor de desempenho de rede** com a mensagem *solução requer configuração adicional*. Clique no mosaico para navegar para o **implementação** separador e selecione o protocolo a utilizar para efetuar as transações sintéticas para monitorização da rede.  Reveja [escolher o direito de ICMP de protocolo ou TCP](#choose-the-right-protocol-icmp-or-tcp) para ajudar a escolher o protocolo correto adequada para a sua rede.<br><br> ![a solução requer a seleção do protocolo](media/log-analytics-network-performance-monitor/log-analytics-netmon-perf-welcome.png)<br><br>

4. Depois de escolher o protocolo será redirecionado para o **descrição geral do OMS** página. Enquanto a solução agrega os dados da sua rede, o mosaico de descrição geral do Monitor de desempenho de rede irá apresentar a mensagem a indicar *agregação de dados em curso*.<br><br> ![solução é agregar dados](media/log-analytics-network-performance-monitor/log-analytics-netmon-tile-status-01.png)<br><br>
5. Depois dos dados são recolhidos e indexados, o mosaico de descrição geral irá alterar e indicar que necessita de efetuar uma configuração adicional.<br><br> ![mosaico de solução requer configuração adicional](media/log-analytics-network-performance-monitor/log-analytics-netmon-tile-status-02.png)<br><br>
6. Clique no mosaico e começar a configurar a solução ao seguir os passos abaixo.

### <a name="create-new-networks"></a>Criar novas redes
Uma rede no Monitor de desempenho de rede é um contentor para sub-redes lógico. Pode criar uma rede com um nome amigável e adicionar sub-redes ao mesmo, de acordo com a lógica de negócio. Por exemplo, pode criar uma rede com o nome *Londres* e adicione todas as sub-redes no seu centro de dados de Londres ou numa rede com o nome *ContosoFrontEnd* e adicione todas as sub-redes que serve o front-end da sua aplicação com o nome Contoso para esta rede.
Na página de configuração, verá uma rede com o nome **predefinido** no separador de redes. Se ainda não criou quaisquer redes, todas as sub-redes automaticamente detetados são colocadas na rede de predefinição.
Sempre que cria uma rede, adicionar uma sub-rede para a mesma e essa sub-rede é removida da rede predefinida. Se eliminar uma rede, todas as respetivas sub-redes automaticamente reencaminhadas para a rede predefinida.
Assim, a rede predefinida age como um contentor para todas as sub-redes que não estão contidos em qualquer rede definida pelo utilizador. Não é possível editar ou eliminar a rede predefinida. Permanece sempre no sistema. No entanto, pode criar tantas redes personalizadas conforme necessário.
Na maioria dos casos, as sub-redes existentes na sua organização irão ser dispostas numa mais de uma rede e deve criar uma ou mais redes para agrupar as sub-redes de acordo com a lógica de negócio

#### <a name="to-create-a-new-network"></a>Para criar uma nova rede
1. Clique em **adicionar rede** e, em seguida, escreva o nome de rede e a descrição.
2. Selecione uma ou mais sub-redes e, em seguida, clique em **adicionar**.
3. Clique em **guardar** para guardar a configuração.<br><br> ![adicionar a rede](./media/log-analytics-network-performance-monitor/npm-add-network.png)

### <a name="wait-for-data-aggregation"></a>Aguarde a agregação de dados
Após ter de guardar a configuração pela primeira vez, a solução inicia a recolha de informações de perda e latência de pacotes de rede entre os nós em que se encontram instalados agentes. Este processo pode demorar algum tempo, por vezes, através de 30 minutos. Durante este estado, o mosaico de Monitor de desempenho de rede na página de descrição geral apresenta uma mensagem a indicar *agregação de dados no processo*.

![agregação de dados em curso](./media/log-analytics-network-performance-monitor/npm-aggregation.png)

Quando os dados terem sido carregados, verá os dados que mostra o mosaico atualizado do Monitor de desempenho de rede.

![Mosaico de Monitor de desempenho de rede](./media/log-analytics-network-performance-monitor/npm-tile.png)

Clique no mosaico para ver o dashboard de monitorização de desempenho de rede.

![Dashboard de monitorização de desempenho de rede](./media/log-analytics-network-performance-monitor/npm-dash01.png)

### <a name="edit-monitoring-settings-for-subnets"></a>Editar definições de monitorização para sub-redes
Todas as sub-redes em que, pelo menos, um agente foi instalado são listadas no **sub-redes** separador na página de configuração.

#### <a name="to-enable-or-disable-monitoring-for-particular-subnetworks"></a>Para ativar ou desativar a monitorização para uma determinada sub-redes
1. Selecione ou desmarque a caixa junto a **ID de sub-rede** e, em seguida, certifique-se de que **utilização para a monitorização** está selecionado ou desmarcada, conforme adequado. Pode selecionar ou desmarcar várias sub-redes. Quando desativado, sub-redes não são monitorizados como os agentes serão atualizados para parar a outros agentes de envio de ping.
2. Escolha os nós que pretende monitorizar para uma determinada sub-rede, selecionando a sub-rede da lista e mover nós necessários entre as listas de nós não monitorizados e monitorizados.
   Pode adicionar um personalizado **Descrição** para sub-rede, se pretender.
3. Clique em **guardar** para guardar a configuração.<br><br> ![Editar sub-rede](./media/log-analytics-network-performance-monitor/npm-edit-subnet.png)

### <a name="choose-nodes-to-monitor"></a>Escolher nós para monitorizar
Todos os nós que tenha um agente instalado nos mesmos são listados no **nós** separador.

#### <a name="to-enable-or-disable-monitoring-for-nodes"></a>Para ativar ou desativar a monitorização para nós
1. Selecione ou desmarque os nós que pretende monitorizar ou parar a monitorização.
2. Clique em **utilização para a monitorização**, ou limpá-lo, conforme apropriado.
3. Clique em **Guardar**.<br><br> ![Ativar a monitorização de nó](./media/log-analytics-network-performance-monitor/npm-enable-node-monitor.png)

### <a name="set-monitoring-rules"></a>Conjunto de regras de monitorização
Monitor de desempenho de rede gera eventos de estado de funcionamento quando o limiar de desempenho de ligações de rede entre 2 sub-redes ou entre 2 redes é quebrado. Estes limiares podem ser adquiridos automaticamente pelo sistema, ou pode fornecer limiares personalizados.
O sistema cria automaticamente uma regra predefinida que gera um evento de estado de funcionamento sempre perda ou latência entre qualquer par de rede/sub-rede liga falhas limiar aprendidas de sistema. Isto ajuda a solução de monitorizar a infraestrutura de rede até que ainda não criou quaisquer regras de monitorização explicitamente. Se a regra predefinida está ativada, todos os nós enviam transações sintéticas para todos os outros nós que tiver ativado para monitorização. A regra predefinida é útil em caso de redes pequenos, por exemplo, num cenário em que tem um pequeno número de servidores a executar um microsserviço e deve certificar-se de que o todos os servidores têm conectividade entre si.

>[!NOTE]
>Recomenda-se vivamente que depois de desativar a regra predefinida e criar regras personalizadas de monitorização, especialmente em caso de redes de grandes dimensões em que estiver a utilizar um grande número de nós para monitorização. Isto irá reduzir o tráfego gerado pela solução e ajudam a organizar os a monitorização da rede.

Crie regras personalizadas de monitorização, de acordo com a lógica de negócio. Por exemplo, se pretender monitorizar o desempenho de conectividade de rede de 2 office sites a headquarter, em seguida, agrupe todas as sub-redes no office site1 na rede O1, todas as sub-redes no office site2 na rede O2 e todas as sub-redes na headquarter na rede criar H. 2 monitorização regras-um entre O1 e H e outros entre O2 e H.


#### <a name="to-create-custom-monitoring-rules"></a>Para criar regras de monitorização personalizadas
1. Clique em **Adicionar regra** no **Monitor** separador e introduza o nome da regra e a descrição.
2. Selecione o par de ligações de sub-rede ou de rede para a monitorização a partir da lista.
3. Primeiro selecione a rede na qual o primeira sub-rede/s de interesse está incluído na lista pendente rede e, em seguida, selecione o sub-rede/s na lista pendente sub-rede correspondente.
   Selecione **todas as sub-redes** se pretender monitorizar todas as sub-redes uma ligação de rede. Da mesma forma, selecione os outros sub-rede/s de interesse. E, pode clicar em **adicionar exceção** para excluir a monitorização para ligações de sub-rede específica da seleção efetuadas.
4. [Escolher entre os protocolos ICMP e TCP](#choose-the-right-protocol-icmp-or-tcp) para executar transações sintéticas.
5. Se não pretender criar eventos de estado de funcionamento para os itens que selecionou, em seguida, desmarque **ativar a monitorização de estado de funcionamento nas ligações abrangidas por esta regra**.
6. Escolha a monitorização de condições.
   Pode definir limiares personalizados para a geração de eventos de estado de funcionamento, escrevendo os valores de limiar. Sempre que o valor da condição ultrapassar o limiar selecionado para o par sub-rede selecionada rede, é gerado um evento de estado de funcionamento.
7. Clique em **guardar** para guardar a configuração.<br><br> ![Criar regra de monitorização personalizada](./media/log-analytics-network-performance-monitor/npm-monitor-rule.png)

Depois de guardar uma regra de monitorização, pode integrar dessa regra com a gestão de alertas, clicando em **criar alertas**. Uma regra de alerta é criada automaticamente com a consulta de pesquisa e outros necessários parâmetros preenchidos na automaticamente. Utilizar uma regra de alerta, pode receber alertas baseados em e-mail, para além dos alertas existentes dentro do NPM. Alertas também podem acionar toma ações com runbooks ou podem integrar com soluções de gestão de serviço existente com webhooks. Pode clicar em **gerir alerta** para editar as definições de alerta.

### <a name="choose-the-right-protocol-icmp-or-tcp"></a>Escolher o direito de ICMP de protocolo ou TCP

Monitor de desempenho de rede (NPM) utiliza transações sintéticas para calcular as métricas de desempenho de rede, como a latência de perda e a ligação do pacote. Para melhor compreender isto, considere um agente NPM ligado a uma extremidade de uma ligação de rede. Este agente NPM envia pacotes de sonda para um agente NPM segundo ligado a outro final da rede. O segundo agente responde com pacotes de resposta. Este processo é repetido algumas vezes. Ao medir o número de respostas e o tempo decorrido para receber cada resposta, o agente NPM primeiro avalia ligação latência e perda de pacotes.

O formato, o tamanho e a sequência destes pacotes é determinado pelo protocolo que escolheu ao criar regras de monitorização. Com base no protocolo de pacotes, o intermédio dispositivos de rede (routers, comutadores, etc.) pode processar estes pacotes de forma diferente. Por conseguinte, à sua escolha de protocolo afeta a precisão dos resultados. Além disso, a opção de protocolo também determina se tem de efetuar quaisquer passos manuais depois de implementar a solução NPM.

NPM oferece a opção entre os protocolos ICMP e TCP para a executing transações sintéticas.
Se escolher o ICMP quando criar uma regra de transação sintética, os agentes NPM utilizam mensagens de eco ICMP para calcular a latência de rede e a perda de pacotes. ECO de ICMP utiliza a mesma mensagem que é enviada pelo utilitário de Ping convencional. Quando utilizar TCP como protocolo, agentes NPM enviam pacotes de TCP SIN através da rede. Isto é seguido de um handshake TCP conclusão e, em seguida, remover a ligação utilizando pacotes RST.

#### <a name="points-to-consider-before-choosing-the-protocol"></a>Pontos a considerar antes de escolher o protocolo
Antes de escolher um protocolo a utilizar, considere as seguintes informações:

##### <a name="discovering-multiple-network-routes"></a>Detetar várias rotas de rede
TCP é mais exata quando detetar várias rotas e necessita de menos agentes em cada sub-rede. Por exemplo, um ou dois agentes através de TCP podem detetar todos os caminhos redundantes entre sub-redes. No entanto, terá de vários agentes utilizando o ICMP para alcançar os resultados semelhantes. Utilizando o ICMP, se tiver *N* número de rotas entre duas sub-redes, precisa de mais de 5*N* agentes na sub-rede de uma origem ou de destino.

##### <a name="accuracy-of-results"></a>Exatidão dos resultados
Routers e comutadores tendem a atribuir prioridade mais baixa para pacotes de eco ICMP em comparação comparadas pacotes TCP. Em determinadas situações, quando os dispositivos de rede são bastante carregados, refletem os dados obtidos por TCP mais detalhadamente a perda e latência teve por aplicações. Isto ocorre porque a maioria do tráfego de aplicação flui através de TCP. Nestes casos, o ICMP fornece resultados menos exatos em comparação comparados o TCP.

##### <a name="firewall-configuration"></a>Configuração da firewall
Protocolo TCP requer que os pacotes TCP são enviados para uma porta de destino. A porta predefinida utilizada por agentes NPM for 8084, no entanto, pode alterar isto quando configurar os agentes. Por isso, tem de garantir que as firewalls de rede ou as regras do NSG (no Azure) estão a permitir tráfego na porta. Terá também de certificar-se de que a firewall local nos computadores onde se encontram instalados agentes está configurada para permitir tráfego esta porta.

Pode utilizar scripts do PowerShell para configurar regras de firewall nos seus computadores com Windows, no entanto, terá de configurar manualmente a firewall da rede.

Em contrapartida, o ICMP não funciona através da porta. Na maioria dos cenários de empresa, o tráfego ICMP é permitido através de firewalls para permitir a utilização de ferramentas de diagnóstico de rede, como o utilitário de Ping. Por isso, se pode enviar Ping a uma máquina a partir de outra, em seguida, pode utilizar o protocolo ICMP sem ter de configurar manualmente as firewalls.

> [!NOTE]
> Alguns firewalls podem bloquear ICMP, que pode levar a retransmissão resultando num grande número de eventos no seu sistema de gestão de informações e o evento de segurança. Certifique-se de que o protocolo que escolheu não está bloqueado por um firewall de rede/NSG, caso contrário NPM não será possível monitorizar o segmento de rede.  Por este motivo, recomendamos que utilize TCP para monitorização. Deve utilizar ICMP em cenários onde não são capazes de utilizar o TCP, tais como:
> * Estiver a utilizar nós de cliente baseada em Windows, uma vez que sockets em bruto de TCP não são permitidos no cliente Windows
> * Os blocos de firewall/NSG de rede TCP


#### <a name="how-to-switch-the-protocol"></a>Como mudar o protocolo

Se optar por utilizar o ICMP durante a implementação, pode mudar para TCP em qualquer altura, editando as predefinições de monitorização de regra.

##### <a name="to-edit-the-default-monitoring-rule"></a>Para editar as predefinições de monitorização de regra
1.  Navegue para **desempenho da rede** > **Monitor** > **configurar** > **Monitor** e, em seguida, clique em **regra predefinida**.
2.  Desloque-se para o **protocolo** secção e selecione o protocolo que pretende utilizar.
3.  Clique em **guardar** para aplicar a definição.

Mesmo que a regra predefinida está a utilizar um protocolo específico, pode criar novas regras com um protocolo diferente. Pode ainda criar uma mistura de regras de onde algumas das regras de utilizam o ICMP e outro utiliza TCP.


## <a name="data-collection-details"></a>Detalhes de recolha de dados
Monitor de desempenho de rede utiliza pacotes de handshake TCP SIN-SYNACK-ACK quando é escolhido TCP e resposta de eco ICMP do ICMP ECHO quando ICMP é escolhido como o protocolo para recolher informações de perda e latência. Traceroute também é utilizado para obter informações de topologia.

A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para o Monitor de desempenho de rede.

| Plataforma | Direcionar o agente | Agente do SCOM | Storage do Azure | SCOM necessário? | Dados de agente do SCOM enviados através do grupo de gestão | Frequência de recolha |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |Mensagens de eco handshakes/ICMP do TCP a cada cinco segundos, dados enviados a cada 3 minutos |

A solução utiliza transações sintéticas para avaliar o estado de funcionamento da rede. Agentes OMS instalados vários num momento os pacotes TCP do exchange de rede ou eco de ICMP (dependendo do protocolo selecionado para a monitorização) com outro. No processo de agentes saiba a perda de tempo e pacote reportadas round-trip, se aplicável. Periodicamente, cada agente também executa uma rota de rastreio por outros agentes para localizar todas as rotas vários na rede que deve ser testada. Utilizando estes dados, os agentes podem deduzir a latência de rede e números de perda de pacotes. Os testes são repetidos a cada cinco segundos e os dados são agregadas para um período de três minutos pelos agentes antes de o carregar para o serviço de análise de registos.

> [!NOTE]
> Embora os agentes comunicam entre si com frequência, estas não geram uma grande quantidade de tráfego de rede ao realizar os testes. Agentes baseiam-se apenas nos pacotes de handshake de TCP SIN-SYNACK-ACK para determinar a perda e latência – não existem dados trocados pacotes. Durante este processo, agentes comunicam entre si apenas quando necessário e a topologia de comunicação do agente está otimizada para reduzir o tráfego de rede.
>
>

## <a name="using-the-solution"></a>Utilizar a solução
Esta secção explica o dashboard de funções e como utilizá-los.

### <a name="solution-overview-tile"></a>Mosaico de descrição geral da solução
Depois de ativar a solução de Monitor de desempenho de rede, o mosaico de solução na página de descrição geral do OMS fornece uma descrição geral rápida do Estado de funcionamento de rede. Apresenta um gráfico de anel que mostra o número de ligações de sub-rede de bom estado de funcionamento e mau estado de funcionamento. Quando clica no mosaico, abre o dashboard de solução.

![Mosaico de Monitor de desempenho de rede](./media/log-analytics-network-performance-monitor/npm-tile.png)

### <a name="network-performance-monitor-solution-dashboard"></a>Dashboard de solução de Monitor de desempenho de rede
O **resumo de rede** painel mostra um resumo das redes, juntamente com o respetivo tamanho relativo. Isto é seguido de mosaicos que mostra o número total de ligações de rede, ligações de sub-rede e caminhos no sistema (um caminho composto os endereços IP dos dois anfitriões com agentes e todos os saltos entre eles).

O **eventos de estado de funcionamento de rede superior** painel fornece uma lista dos eventos mais recentes do Estado de funcionamento e alertas no sistema e a hora, uma vez que o evento foi Active Directory. Um evento de estado de funcionamento ou o alerta é gerado sempre que a latência de uma ligação de rede ou sub-rede ou perda de pacotes excede um limiar.

O **ligações de rede danificado superior** painel mostra uma lista de ligações de rede danificado. Estas são as ligações de rede que tenham um ou mais eventos de estado de funcionamento negativo para os mesmos neste momento.

O **ligações de sub-rede de principais com mais perda** e **ligações de sub-rede com a latência mais** painéis mostrar as ligações de sub-rede superior perda de pacotes e ligações de sub-rede de cima à latência, respetivamente. Latência elevada ou alguma quantidade de perda de pacotes pode ser esperada em determinados ligações de rede. Essas ligações são apresentados nas listas de dez principais, mas não marcado como danificadas.

O **consultas comuns** painel contém um conjunto de consultas de pesquisa obter diretamente de dados de monitorização de rede não processado. Pode utilizar estas consultas como um ponto de partida para criar as suas próprias consultas para relatórios personalizados.

![Dashboard de monitorização de desempenho de rede](./media/log-analytics-network-performance-monitor/npm-dash01.png)

### <a name="drill-down-for-depth"></a>Desagregar para profundidade
Pode clicar em várias ligações no dashboard da solução para desagregar mais aprofundada para qualquer área de interesse. Por exemplo, quando vir um alerta ou uma ligação de rede danificado apareça no dashboard, pode clicar para investigação mais aprofundada. Será direcionado para uma página que apresenta uma lista de todas as ligações de sub-rede para a ligação de rede específico. Poderá ver o estado de perda, latência e estado de funcionamento de cada hiperligação de sub-rede e rapidamente descobrir que ligações de sub-rede estão a causar o problema. Em seguida, pode clicar em **ver ligações de nó** para ver todas as ligações de nó para a ligação de sub-rede mau estado de funcionamento. Em seguida, pode ver ligações de nó para nó individuais e localizar as ligações de nó mau estado de funcionamento.

Pode clicar em **topologia de vista** para visualizar a topologia de salto por salto de rotas entre os nós de origem e de destino. As rotas mau estado de funcionamento ou saltos são mostrados a vermelho, para que as possa identificar rapidamente o problema para uma determinada parte da rede.

![dados de desagregar](./media/log-analytics-network-performance-monitor/npm-drill.png)

### <a name="network-state-recorder"></a>Gravador de estado de rede

Cada vista apresenta um instantâneo do Estado de funcionamento do rede num determinado ponto no tempo. Por predefinição, é apresentado o estado mais recente. A barra na parte superior da página mostra o ponto no tempo para o qual o estado é apresentado. Pode optar por voltar atrás no tempo e ver o instantâneo do Estado de funcionamento do rede clicando na barra de na **ações**. Também pode optar por ativar ou desativar a atualização automática para qualquer página, ao visualizar o estado mais recente.

![Estado de rede](./media/log-analytics-network-performance-monitor/network-state.png)

#### <a name="trend-charts"></a>Gráficos de tendência
Em cada nível que desagregar, pode ver a tendência de perda e latência para uma ligação de rede. Gráficos de tendência também estão disponíveis para as ligações de sub-rede e o nó. Pode alterar o intervalo de tempo para o gráfico desenhar utilizando o controlo de tempo na parte superior do gráfico.

Gráficos de tendência mostram-lhe uma perspetiva de histórico do desempenho de uma ligação de rede. Alguns problemas de rede são transitórios natureza e seriam difíceis de detetar apenas ao observar o estado atual da rede. Isto acontece porque problemas podem superfície rapidamente e desaparecer antes que qualquer pessoa avisos, apenas a reaparecer tiver posteriormente no tempo. Tais problemas transitórios também podem ser difícil para os administradores de aplicação porque a superfície, muitas vezes, os problemas à medida que aumenta unexplained no tempo de resposta da aplicação, mesmo quando são apresentados todos os componentes da aplicação executar facilmente.

Pode facilmente detetar esses tipos de problemas ao observar um gráfico de tendência onde o problema irá aparecer como um pico de pedidos repentino na latência de rede ou perda de pacotes.

![gráfico de tendência](./media/log-analytics-network-performance-monitor/npm-trend.png)

#### <a name="hop-by-hop-topology-map"></a>mapa de topologia de salto por salto
Monitor de desempenho de rede mostra a topologia de salto por salto de rotas entre dois nós num mapa de topologia interativo. Pode ver o mapa de topologia selecionando uma ligação de nó e, em seguida, clicando em **topologia de vista**. Além disso, pode ver o mapa de topologia clicando **caminhos** mosaico no dashboard. Ao clicar em **caminhos** no dashboard, tem de selecionar os nós de origem e de destino a partir do painel do lado esquerdo e, em seguida, clique em **desenho** para desenhar as rotas entre os dois nós.

O mapa de topologia apresenta rotas quantos são entre dois nós e o que caminhos demorar os pacotes de dados. Congestionamentos de desempenho de rede são assinalados a vermelho no mapa de topologia. Pode localizar uma ligação de rede defeituoso ou um dispositivo de rede defeituoso observando elementos coloridos vermelhos no mapa de topologia.

Ao clicar num nó ou paire sobre os mesmos no mapa de topologia, verá as propriedades do nó, como o endereço IP e FQDN. Clique num salto para ver é o endereço IP. Pode optar por filtrar rotas específicas utilizando os filtros no painel de ações expansíveis. Além disso, também pode simplificar as topologias de rede por ocultar os saltos intermédios utilizando o controlo de deslize no painel de ações. Pode zoom-in ou fora do mapa de topologia, utilizando a roda do rato.

Tenha em atenção que a topologia apresentada no mapa de topologia de camada 3 e não contém ligações e dispositivos de camada 2.

![mapa de topologia de salto por salto](./media/log-analytics-network-performance-monitor/npm-topology.png)

#### <a name="fault-localization"></a>Localização de falhas
Monitor de desempenho de rede é capaz de determinar os congestionamentos de rede sem ligar aos dispositivos de rede. Com base nos dados que recolher da rede e ao aplicar algoritmos avançados no gráfico de rede, o Monitor de desempenho de rede faz com que uma estimativa probabilistic das partes de rede que são provavelmente a origem do problema.

Esta abordagem é útil para determinar os congestionamentos de rede quando o acesso ao saltos não está disponível porque não requer quaisquer dados para ser reunidas a partir de dispositivos de rede, tais como comutadores ou routers. Isto também é útil quando os saltos entre dois nós não estão no seu controlo administrativo. Por exemplo, os saltos podem ser routers do ISP.

### <a name="log-analytics-search"></a>Análise de registos de pesquisa
Todos os dados que é páginas graficamente expostas através do dashboard de monitorização de desempenho de rede e desagregar também está disponível nativamente no pesquisa de análise de registos. Pode consultar os dados utilizando a linguagem de consulta de pesquisa e criar relatórios personalizados ao exportar os dados para Excel ou do Power BI. O **consultas comuns** painel no dashboard tem algumas consultas útil que pode utilizar como ponto de partida para criar as suas próprias consultas e relatórios.

![consultas de pesquisa](./media/log-analytics-network-performance-monitor/npm-queries.png)

## <a name="investigate-the-root-cause-of-a-health-alert"></a>Investigue a causa de raiz de um alerta de estado de funcionamento
Vamos ver agora que já leu sobre o Monitor de desempenho de rede, uma investigação simple para a causa de raiz de um evento de estado de funcionamento.

1. Na página Descrição geral, obterá um instantâneo rápido do Estado de funcionamento da sua rede ao observar o **Monitor de desempenho de rede** mosaico. Tenha em atenção que fora as ligações de sub-6 redes a ser monitorizadas, 2 estão danificados. Isto warrants investigação. Clique no mosaico para ver o dashboard de solução.<br><br> ![Mosaico de Monitor de desempenho de rede](./media/log-analytics-network-performance-monitor/npm-investigation01.png)  
2. A imagem de exemplo abaixo, irá notar que há um evento de estado de funcionamento uma ligação de rede que está danificada. Decidir a investigar o problema e clique em de **DMZ2 DMZ1** ligação de rede para determinar a raiz do problema.<br><br> ![exemplo de ligação de rede mau estado de funcionamento](./media/log-analytics-network-performance-monitor/npm-investigation02.png)  
3. A página de desagregação mostra todas as ligações de sub-rede no **DMZ2 DMZ1** ligação de rede. Irá notar que, para ambas as ligações de sub-rede, a latência ultrapassou o limiar de efetuar a ligação de rede em mau estado de funcionamento. Também pode ver as tendências de latência de ambas as ligações de sub-rede. Pode utilizar a seleção do tempo controlo no gráfico focar-se no intervalo de tempo necessário. Pode ver a hora do dia quando latência atingiu o respetivo horário de pico. Pode procurar posteriormente os registos para este período de tempo investigar o problema. Clique em **ver ligações de nó** para desagregar ainda mais.<br><br> ![exemplo de ligações de sub-rede mau estado de funcionamento](./media/log-analytics-network-performance-monitor/npm-investigation03.png) 
4. Semelhante à página anterior, a página de desagregar para a ligação de determinada sub-rede lista pendente a ligações de nó que constituem. Pode efetuar ações semelhantes aqui, tal como fez no passo anterior. Clique em **topologia de vista** para visualizar a topologia entre os nós de 2.<br><br> ![exemplo de ligações de nó mau estado de funcionamento](./media/log-analytics-network-performance-monitor/npm-investigation04.png)  
5. Todos os caminhos entre 2 nós selecionados são desenhados no mapa de topologia. Pode visualizar a topologia de salto por salto de rotas entre dois nós no mapa de topologia. Dá-lhe uma fotografia limpar rotas quantos existam entre dois nós e o que os caminhos de pacotes de dados estão a utilizar. Congestionamentos de desempenho de rede são marcados na cor vermelho. Pode localizar uma ligação de rede defeituoso ou um dispositivo de rede defeituoso observando elementos coloridos vermelhos no mapa de topologia.<br><br> ![exemplo de vista de topologia mau estado de funcionamento](./media/log-analytics-network-performance-monitor/npm-investigation05.png)  
6. O número de saltos em cada caminho, a latência e perda podem ser revistas no **ação** painel. Utilize a barra de deslocamento para ver os detalhes de nesses caminhos mau estado de funcionamento.  Utilize os filtros para selecionar os caminhos com o salto mau estado de funcionamento para que a topologia para apenas os caminhos selecionados é representada. Pode utilizar a roda do rato para aplicar zoom dentro ou fora do mapa de topologia.

   No abaixo imagem claramente pode ver a causa raiz das áreas de problema para a secção específica da rede ao observar os caminhos e saltos na cor vermelho. Clicar num nó no mapa de topologia de revela as propriedades do nó, incluindo o FQDN e o endereço IP. Clicar num salto mostra o endereço IP do salto.<br><br> ![topologia de mau estado de funcionamento - exemplo de detalhes do caminho](./media/log-analytics-network-performance-monitor/npm-investigation06.png)

## <a name="provide-feedback"></a>Enviar comentários

- **UserVoice** -pode publicar as suas ideias para funcionalidades de Monitor de desempenho de rede que pretende que sejam-nos para funcionar no. Visite a nossa [página do UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring).
- **Associar o nosso coorte** -sempre estamos interessados em novos clientes aderir ao nosso coorte a ter. Como parte do mesmo, irá obter acesso atempado a novas funcionalidades e ajude-na melhorar o Monitor de desempenho de rede. Se estiver interessado em associar, preenchimento Escalamento isto [inquérito Rápido](https://aka.ms/npmcohort).

## <a name="next-steps"></a>Passos seguintes
* [Pesquisar registos](log-analytics-log-searches.md) para ver registos de dados de desempenho de rede detalhada.
