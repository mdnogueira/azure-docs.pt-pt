---
title: "Configurar o Monitor de desempenho de rede para circuitos do ExpressRoute do Azure (pré-visualização) | Microsoft Docs"
description: "Configure NPM para circuitos do ExpressRoute do Azure. (Pré-visualização)"
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/01/2017
ms.author: cherylmc
ms.openlocfilehash: 35dd3c6be2fb2fa5ec4d14eefce1c16005210364
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="configure-network-performance-monitor-for-expressroute-preview"></a>Configurar o Monitor de desempenho de rede para o ExpressRoute (pré-visualização)

Monitor de desempenho de rede (NPM) é uma solução que monitoriza a conectividade entre as implementações de nuvem do Azure e localizações no local (sucursais, etc.) de monitorização de rede baseado na nuvem. NPM faz parte do Microsoft Operations Management Suite (OMS). Agora, NPM oferece uma extensão para o ExpressRoute permite-lhe monitorizar o desempenho de rede através de circuitos ExpressRoute que estão configurados para utilizar o Peering privado. Quando configurar NPM para o ExpressRoute, pode detetar problemas de rede para identificar e eliminar.

Pode:

* Monitorizar a perda e latência em várias VNets e definir alertas

* Monitorizar todos os caminhos (incluindo caminhos redundantes) na rede

* Resolver problemas de rede transitórios e ponto no tempo que são difíceis de replicar

* Ajudar a determinar um segmento específico na rede que é responsável por degradação do desempenho

* Obter o débito por rede virtual (se tiver agentes instalados em cada VNet)

* Consulte o estado do sistema ExpressRoute de um ponto anterior no tempo

**Como funciona?**

Se encontram instalados agentes de monitorização em vários servidores, no local e no Azure. Os agentes de comunicarem entre si, mas não enviam dados, enviam pacotes de handshake TCP. A comunicação entre os agentes permite ao Azure mapear a topologia de rede e o caminho que o tráfego pode demorar algum.

**Fluxo de trabalho**

1. Crie uma área de trabalho de NPM na região Central EUA oeste. Atualmente, esta é a única região onde esta pré-visualização é suportada.
2. Instalar e configurar agentes de software: 
    * Instale agentes de monitorização de servidores no local e as VMs do Azure.
    * Configure as definições nos servidores do agente de monitorização para permitir que os agentes de monitorização comunicar. (As portas de firewall aberta, etc.)
3. Configurar regras para permitir que o agente de monitorização instalado em VMs do Azure para comunicar com no local (NSG) do grupo de segurança de rede os agentes de monitorização.
4. Pedido de lista branca sua área de trabalho de NPM
5. Configurar a monitorização: detetar automaticamente e gerir quais as redes são visíveis no NPM.

Se já estiver a utilizar o Monitor de desempenho de rede para monitorizar os outros objetos ou os serviços e já tiver a área de trabalho Central EUA oeste, pode ignorar o passo 1 e o passo 2 e iniciar a configuração com o passo 3.

## <a name="configure"></a>Passo 1: Criar uma área de trabalho

1. No [portal do Azure](https://portal.azure.com), pesquise a lista de serviços no **Marketplace** 'Monitor de desempenho de rede'. No retorno, clique para abrir o **Monitor de desempenho de rede** página.

  ![portal](.\media\how-to-npm\3.png)<br><br>
2. Na parte inferior do principal **Monitor de desempenho de rede** página, clique em **criar** para abrir **Monitor de desempenho de rede - criar nova solução** página. Clique em **área de trabalho do OMS - Selecione uma área de trabalho** para abrir a página de áreas de trabalho. Clique em **+ criar nova área de trabalho** para abrir a página da área de trabalho.
3. No **área de trabalho OMS** página, selecione **criar novo** e configure as seguintes definições:

  * Área de trabalho OMS - escreva um nome para a sua área de trabalho.
  * Subscrição - se tiver várias subscrições, selecione aquela que pretende associar à área de trabalho de novo.
  * Grupo de recursos - criar um grupo de recursos ou utilize uma já existente.
  * Localização - tem de selecionar Central EUA oeste para esta pré-visualização
  * Escalão de preço - Selecione 'livres'

  ![Área de trabalho](.\media\how-to-npm\4.png)<br><br>
4. Clique em **OK** para guardar e implementar o modelo de definições. Assim que valida o modelo, clique em **criar** para implementar a área de trabalho.
5. Depois da área de trabalho ter sido implementada, navegue para o **NetworkMonitoring(name)** recursos que criou. Valide as definições, em seguida, clique em **solução requer configuração adicional**.

  ![configuração adicional](.\media\how-to-npm\5.png)
6. No **bem-vindo ao Monitor de desempenho de rede** página, selecione **TCP de utilização para transações sintéticas**, em seguida, clique em **submeter**. As transações de TCP são utilizadas apenas para tornar e quebrar a ligação. Não são enviados dados através destas ligações de TCP.

  ![TCP para transações sintéticas](.\media\how-to-npm\6.png)

## <a name="agents"></a>Passo 2: Instalar e configurar agentes

### <a name="download"></a>2.1: Transfira o ficheiro de configuração do agente

1. No **configuração de Monitor de desempenho de rede - página de configuração de TCP** para o seu recurso no **instalar agentes do OMS** secção, clique no agente que corresponde ao processador e a transferência do servidor de ficheiro de configuração.

  >[!NOTE]
  >O agente Linux não é atualmente suportado para o ExpressRoute monitorização.
  >
  >
2. Em seguida, copie o **ID da área de trabalho** e **chave primária** para bloco de notas.
3. No **configurar agentes** secção, transfira o Script do Powershell. O script do PowerShell ajuda-o a abrir a porta de firewall relevantes para as transações de TCP.

  ![Script do PowerShell](.\media\how-to-npm\7.png)

### <a name="installagent"></a>2.2: instalar um agente de monitorização em cada servidor de monitorização

1. Executar **configuração** para instalar o agente em cada servidor que pretende utilizar para a monitorização do ExpressRoute. O servidor que utiliza para a monitorização pode ser uma VM ou no local e tem de ter acesso à Internet. Terá de instalar pelo menos um agente no local e um agente em cada segmento de rede que pretende monitorizar no Azure.
2. No **boas-vindas** página, clique em **seguinte**.
3. No **termos de licenciamento** página, leia a licença e, em seguida, clique em **concordo**.
4. No **pasta de destino** página, altere ou mantenha a pasta de instalação predefinida e, em seguida, clique em **seguinte**.
5. No **opções de configuração do agente** página, pode optar por ligar o agente à análise de registos do Azure (OMS) ou o Operations Manager. Em alternativa, pode deixar as escolhas em branco se pretender configurar o agente mais tarde. Depois de efetuar o selection(s), clique em **seguinte**.

  * Se tiver optado por ligar para **análise de registos do Azure (OMS)**, cole o **ID da área de trabalho** e **chave da área de trabalho** (chave primária) que copiou no bloco de notas na secção anterior. Clique depois em **Seguinte**.

    ![ID e a chave](.\media\how-to-npm\8.png)
  * Se tiver optado por ligar para **do Operations Manager**, no **configuração do grupo de gestão** , escreva o **Management Group Name**, **servidor de gestão** e o **porta do servidor de gestão**. Clique depois em **Seguinte**.

    ![Operations Manager](.\media\how-to-npm\9.png)
  * No **conta de ação do agente** página, escolha o o **Sistema Local** conta, ou **domínio ou conta de computador Local**. Clique depois em **Seguinte**.

    ![Conta](.\media\how-to-npm\10.png)
6. No **pronto para instalar** página, reveja as suas opções e, em seguida, clique em **instalar**.
7. No **configuração concluída com sucesso** página, clique em **concluir**.
8. Quando terminar, o Microsoft Monitoring Agent é apresentado no painel de controlo. Pode rever a configuração não existe e certifique-se de que o agente está ligado ao Operational Insights (OMS). Quando estiver ligado à OMS, o agente apresenta uma mensagem a indicar: **o Microsoft Monitoring Agent foi ligado com êxito para o serviço do Microsoft Operations Management Suite**.

### <a name="proxy"></a>2.3: Configurar definições de proxy (opcionais)

Se estiver a utilizar um proxy web para aceder à Internet, utilize os seguintes passos para configurar definições de proxy para o Microsoft Monitoring Agent. Execute estes passos para cada servidor. Se tiver vários servidores que necessita configurar, poderá considerar mais fácil utilizar um script para automatizar este processo. Se Sim, consulte [para configurar definições de proxy para o Microsoft Monitoring Agent utilizando um script](../log-analytics/log-analytics-windows-agents.md#to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script).

Para configurar definições de proxy para o Microsoft Monitoring Agent através do painel de controlo:

1. Abra o **painel de controlo**.
2. Abra o **Agente de Monitorização da Microsoft**.
3. Clique no separador **Definições de Proxy**.
4. Selecione **utilizar um servidor proxy** e escreva o URL e a porta número, se necessitar de um. Se o servidor proxy requer autenticação, escreva o nome de utilizador e a palavra-passe para aceder ao servidor proxy.

  ![Proxy](.\media\how-to-npm\11.png)

### <a name="verifyagent"></a>2.4: verificar a conectividade de agente

Pode facilmente verificar se os agentes estão a comunicar.

1. Num servidor com o agente de monitorização, abra o **painel de controlo**.
2. Abra o **Microsoft Monitoring Agent**.
3. Clique em de **análise de registos do Azure (OMS)** separador.
4. No **estado** coluna, deverá ver que o agente ligado com êxito para o serviço do Operations Management Suite.

  ![status](.\media\how-to-npm\12.png)

### <a name="firewall"></a>2.5: abrir as portas de firewall nos servidores de agente de monitorização

Para utilizar o protocolo TCP, é necessário abrir as portas de firewall para se certificar de que os agentes de monitorização podem comunicar.

Pode executar um script do PowerShell que cria as chaves do registo necessárias para o Monitor de desempenho de rede, bem como a criação de regras de Firewall do Windows para permitir a monitorização de agentes para criar ligações TCP entre si. As chaves de registo criadas pelo script também especificar se pretende registar os registos de depuração e o caminho para o ficheiro de registos. Também define a porta TCP de agente utilizada para comunicação. Os valores para estas chaves são configurados automaticamente pelo script de, pelo que não deve alterar manualmente estas chaves.

Porta 8084 está aberta por predefinição. Pode utilizar uma porta personalizada, fornecendo o parâmetro 'portNumber' para o script. No entanto, se o fizer, tem de especificar a mesma porta para todos os servidores em que executa o script.

>[!NOTE]
>O script do PowerShell 'EnableRules' configura regras de Firewall do Windows apenas no servidor onde o script é executado. Se tiver uma firewall de rede, deve certificar-se de que permite o tráfego destinado a porta TCP que está a ser utilizada pelo Monitor de desempenho de rede.
>
>

Nos servidores de agente, abra uma janela do PowerShell com privilégios administrativos. Execute o [EnableRules](https://gallery.technet.microsoft.com/OMS-Network-Performance-04a66634) script do PowerShell (que transferiu anteriormente). Não utilize parâmetros.

  ![PowerShell_Script](.\media\how-to-npm\script.png)

## <a name="opennsg"></a>Passo 3: Configurar regras de grupo de segurança de rede

Para monitorizar os servidores de agente que se encontrem no Azure, tem de configurar regras para permitir tráfego TCP uma porta utilizada pelo NPM para transações sintéticas (NSG) do grupo de segurança de rede. A porta predefinida é 8084. Isto permite que um agente de monitorização instalado na VM do Azure para comunicar com um local agente de monitorização.

Para mais informações sobre o NSG, consulte [grupos de segurança de rede](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

## <a name="whitelist"></a>Passo 4: Pedido lista branca área de trabalho

>[!NOTE]
>Certifique-se de que instalou os agentes (o agente de servidor no local e o agente do servidor do Azure) e ter a executar o script do PowerShell antes de continuar com este passo.
>
>

Antes de começar a utilizar a funcionalidade de monitorização do ExpressRoute de NPM, tem de solicitar para ter a sua área de trabalho na lista de permissões. [Clique aqui para ir para a página e preencher o formulário de pedido](https://go.microsoft.com/fwlink/?linkid=862263). (Sugestão: pretende abrir esta ligação numa nova janela ou separador). O processo de adicionar à lista branca pode demorar um dia útil ou mais. Depois do adicionar à lista branca estiver concluída, receberá uma mensagem de e-mail.

## <a name="setupmonitor"></a>Passo 5: Configurar NPM para monitorização do ExpressRoute

>[!WARNING]
>Não continue até que a sua área de trabalho foi na lista de permissões e receber um e-mail de confirmação.
>
>

Depois de concluir as secções anteriores e certifique-se de que foram na lista de permissões, pode configurar monitorização.

1. Navegue para o mosaico de descrição geral do Monitor de desempenho de rede, acedendo ao **todos os recursos** página e clicar na na lista de permissões NPM área de trabalho.

  ![área de trabalho de npm](.\media\how-to-npm\npm.png)
2. Clique em de **Monitor de desempenho de rede** mosaico de descrição geral para trazer o dashboard. O dashboard contém uma página do ExpressRoute, que mostra que o ExpressRoute é um 'Estado não configurado'. Clique em **funcionalidade configuração** para abrir a página de configuração de Monitor de desempenho de rede.

  ![configuração de funcionalidade](.\media\how-to-npm\npm2.png)
3. Na página de configuração, navegue até ao separador 'ExpressRoute Peerings', localizado no painel do lado esquerdo. Clique em **detetar agora**.

  ![Detetar](.\media\how-to-npm\13.png)
4. Quando tiver concluído a deteção, consulte as regras para o nome exclusivo do circuito e nome da VNet. Inicialmente, estas regras estão desativadas. Ative as regras de, em seguida, selecione os agentes de monitorização e os valores de limiar.

  ![regras](.\media\how-to-npm\14.png)
5. Depois de ativar as regras e selecionar os valores e os agentes que pretende monitorizar, há um Aguarde aproximadamente 30-60 minutos para os valores começar a preencher e **ExpressRoute monitorização** mosaicos fique disponível. Depois de ver os mosaicos de monitorização, os circuitos ExpressRoute e recursos de ligação estão a ser monitorizados pelo NPM.

  ![monitorização de mosaicos](.\media\how-to-npm\15.png)

## <a name="explore"></a>Passo 6: Ver mosaicos de monitorização

### <a name="dashboard"></a>Página de Monitor de desempenho de rede

A página NPM contém uma página para o ExpressRoute que mostra uma descrição geral do Estado de funcionamento dos circuitos ExpressRoute e peerings.

  ![Dashboard](.\media\how-to-npm\dashboard.png)

### <a name="circuits"></a>Lista de circuitos

Para ver uma lista de todos os circuitos ExpressRoute monitorizados, clique no **circuitos ExpressRoute** mosaico. Pode selecionar um circuito e ver o estado de funcionamento, gráficos de tendência de perda de pacotes, utilização de largura de banda e latência. Os gráficos são interativos. Pode selecionar uma janela de tempo personalizadas para representar os gráficos. Pode arrastar o rato através de uma área no gráfico para ampliar e ver os pontos de dados de detalhado.

  ![circuit_list](.\media\how-to-npm\circuits.png)

#### <a name="trend"></a>Tendência de perda, débito e latência

Os gráficos de largura de banda, a latência e perda são interativos. Pode ampliar em qualquer secção nestes gráficos, utilizar controlos de rato. Também pode ver as largura de banda, a latência e perda de dados para outros intervalos clicando **data/hora**, localizado abaixo do botão de ações no canto superior esquerdo.

![tendência](.\media\how-to-npm\16.png)

### <a name="peerings"></a>Lista de Peerings

Clicar no **privada Peerings** mosaico no dashboard apresenta uma lista de todas as ligações a redes virtuais através de peering privado. Aqui, pode selecionar um virtual ligação de rede e ver o estado de funcionamento, gráficos de tendência de perda de pacotes, utilização de largura de banda e latência.

  ![lista de circuito](.\media\how-to-npm\peerings.png)

### <a name="topology"></a>Topologia de circuito

Para visualizar a topologia de circuito, clique no **topologia** mosaico. Isto leva-o para a vista de topologia de selecionado circuito ou peering. O diagrama de topologia fornece a latência de cada segmento na rede e cada salto de camada 3 é representado por um nó do diagrama. Clicar num salto de revela mais detalhes sobre o salto.
Pode aumentar o nível de visibilidade para incluir saltos no local, movendo a barra do controlo de deslize abaixo **filtros**. Mover a barra do controlo de deslize para a esquerda ou direita, aumenta/diminuições o número de saltos no gráfico de topologia. A latência em cada segmento está visível, que permite isolamento mais rápido de segmentos de latência elevada na sua rede.

![filtros](.\media\how-to-npm\topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Vista detalhada da topologia de um circuito

Esta vista mostra as ligações VNet.
![topologia de detalhado](.\media\how-to-npm\17.png)
