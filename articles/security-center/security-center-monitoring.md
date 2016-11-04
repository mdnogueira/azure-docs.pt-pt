---
title: Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure | Microsoft Docs
description: Este documento ajuda-o a começar a trabalhar com as capacidades de monitorização no Centro de Segurança do Azure.
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: ''

ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2016
ms.author: yurid

---
# Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure
Este documento ajuda-o a utilizar as capacidades de monitorização no Centro de Segurança do Azure para monitorizar a conformidade com políticas.

## O que é a monitorização do estado de funcionamento de segurança?
Pensamos frequentemente que monitorizar é observar e esperar que um evento ocorra, para que possamos reagir à situação. A monitorização de segurança refere-se ao facto de ter uma estratégia proativa que audita os seus recursos para identificar os sistemas que não cumprem as normas ou melhores práticas organizacionais.

## Monitorizar o estado de funcionamento de segurança
Depois de ativar [políticas de segurança](security-center-policies.md) para os recursos de uma subscrição, o Centro de Segurança irá analisar a segurança dos seus recursos para identificar potenciais vulnerabilidades.  As informações acerca da configuração da sua rede ficam disponíveis imediatamente, mas pode demorar uma hora ou mais até que as informações acerca da configuração da máquina virtual (tais como o estado da atualização de segurança e a configuração do SO) fiquem disponíveis. Pode ver o estado de segurança dos seus recursos, juntamente com quaisquer problemas no painel **Estado de Funcionamento de Segurança de Recursos**. Também pode ver uma lista dos problemas no painel **Recomendações**.

Para mais informações sobre como aplicar recomendações, leia [Implementar recomendações de segurança no Centro de Segurança do Azure](security-center-recommendations.md).

No mosaico **Estado de Funcionamento da Segurança de Recursos** pode monitorizar o estado de segurança dos seus recursos. No exemplo abaixo, pode ver vários problemas com uma gravidade média e alta que necessitam da atenção. As políticas de segurança que estão ativadas terão impacto nos tipos de controlos que são monitorizados.

![Estado de funcionamento de recursos](./media/security-center-monitoring/security-center-monitoring-fig1-new4.png)

Se o Centro de Segurança identificar uma vulnerabilidade que necessita de ser tratada, tal como uma VM com atualizações de segurança em falta ou uma sub-rede sem um [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md), esta será listada aqui.

### Monitorizar máquinas virtuais
Quando clica em **Virtual Machines** no mosaico **Estado de Funcionamento da segurança de recursos**, o painel **Virtual Machines** abre-se com mais detalhes sobre a integração e passos de prevenção, bem como uma lista de todas as VMs que são monitorizadas pelo Centro de Segurança, conforme mostrado abaixo.

![Atualização do sistema em falta em VM](./media/security-center-monitoring/security-center-monitoring-fig2-ga.png)

* Passos de integração
* Recomendações de Virtual Machines
* Virtual Machines

Em cada secção pode selecionar uma opção individual para ver mais detalhes sobre o passo recomendado para resolver o problema. As secções abaixo abordam estas áreas em maior detalhe.

#### Recomendações de monitorização
Esta secção mostra o número total de VMs que foram inicializadas para recolha de dados e o respetivo estado atual. Depois de todas as VMs terem inicializado a recolha de dados, estarão preparadas para receber políticas de segurança do Centro de Segurança. Quando clica nesta entrada, é aberto o painel **Estado da instalação da recolha de dados** e pode ver os nomes das VMs, e o estado atual da recolha de dados na coluna **ESTADO DA INSTALAÇÃO**, como mostrado abaixo.

![Estado de inicialização](./media/security-center-monitoring/security-center-monitoring-fig3-ga.png)

#### Recomendações de Virtual Machines
Esta secção tem um conjunto de [recomendações para cada VM](security-center-virtual-machine-recommendations.md) monitorizada pelo Centro de Segurança do Azure. A primeira coluna inclui a recomendação, a segunda coluna o número total de VMs que são afetadas por essa recomendação e a terceira coluna indica a gravidade do problema conforme ilustrado abaixo.

![Recomendações da VM](./media/security-center-monitoring/security-center-monitoring-fig4-ga.png)

> [!NOTE]
> Apenas são apresentadas as VMs com, pelo menos, um ponto final público no painel Estado de Funcionamento da Rede, na lista de topologias da Rede.
> 
> 

Cada recomendação tem um conjunto de ações que podem ser executadas assim que clicar na mesma. Por exemplo, se clicar em **Atualizações do sistema em falta**, o painel **Atualizações do sistema em falta** abre-se. Indica numa lista as VMs que têm patches em falta e a gravidade da atualização em falta conforme mostrado abaixo.

![Atualizações do sistema em falta](./media/security-center-monitoring/security-center-monitoring-fig5-ga.png)

O painel **Atualizações do sistema em falta** mostrará uma tabela com as seguintes informações:

* **MÁQUINA VIRTUAL**: o nome da máquina virtual que tem atualizações em falta.
* **ATUALIZAÇÕES DO SISTEMA**: o número de atualizações do sistema que estão em falta.
* **HORA DA ÚLTIMA ANÁLISE**: a hora em que o Centro de Segurança analisou pela última vez a VM quanto à existência de atualizações.
* **ESTADO**: o estado atual da recomendação:
  * **Aberto**: a recomendação ainda não foi tratada
  * **Em curso**: a recomendação está a ser aplicada a esses recursos, não tem de tomar nenhuma medida
  * **Resolvida**: a recomendação já foi concluída (quando o problema foi resolvido, a entrada fica a cinzento).
* **GRAVIDADE**: descreve a gravidade dessa recomendação específica:
  * **Elevada**: uma vulnerabilidade existe com um recurso significativo (aplicação, VM, grupo de segurança de rede) e necessita de atenção
  * **Média**: passos não críticos ou adicionais necessários para concluir um processo ou eliminar uma vulnerabilidade
  * **Baixa**: uma vulnerabilidade deve ser tratada, mas não necessita de atenção imediata. (Por predefinição, as recomendações baixas não são apresentadas, mas pode filtrar por recomendações baixas se pretender visualizá-las).

Para ver os detalhes de recomendação, clique no nome da VM. Um novo painel para essa VM abre-se com a lista de atualizações conforme apresentado abaixo.

![Atualizações do sistema em falta por VM](./media/security-center-monitoring/security-center-monitoring-fig6-ga.png)

> [!NOTE]
> As recomendações de segurança aqui são as mesmas que as do painel Recomendações. Veja o artigo [Implementar recomendações de segurança no Centro de Segurança do Azure](security-center-recommendations.md) para obter mais informações sobre como resolver recomendações. Isto é aplicável não só para VMs, mas para todos os recursos que estão disponíveis no mosaico Estado de Funcionamento de Recursos.
> 
> 

#### Secção Virtual Machines
A secção de máquinas virtuais faculta uma descrição geral de todas as VMs e recomendações. Cada coluna representa um conjunto de recomendações como é mostrado abaixo:

![VMs](./media/security-center-monitoring/security-center-monitoring-fig7-ga.png)

No ícone de que é apresentado em cada recomendação ajuda-o a identificar rapidamente quais as VMs que necessitam de atenção e o tipo de recomendação.

No exemplo acima, uma VM tem uma recomendação crítica relativa à proteção do ponto final. Para obter mais informações sobre a VM, clique na mesma. Abre-se um novo painel que representa esta VM, conforme mostrado abaixo.

![Detalhes de Segurança de VM](./media/security-center-monitoring/security-center-monitoring-fig8-ga.png)

Este painel tem os detalhes de segurança para a VM. Na parte inferior deste painel pode ver a ação recomendada e a gravidade de cada problema.

#### Secção Serviços em nuvem (Pré-visualização)
O estado de funcionamento dos serviços em nuvem está incluído no mosaico do estado de funcionamento da segurança das máquinas virtuais. É criada uma recomendação quando a versão do SO está desatualizada, conforme mostrado abaixo: 

![Serviços Cloud](./media/security-center-monitoring/security-center-monitoring-fig8-new2.png)

Terá de seguir os passos da recomendação para atualizar a versão do SO. Por exemplo, se clicar no alerta vermelho numa função da Web (executa o Windows Server com a sua aplicação Web automaticamente implementada no IIS) ou numa função de Trabalho (executa o Windows Server com a sua aplicação Web automaticamente implementada no IIS), irá abrir um novo painel com mais detalhes sobre estas recomendações, conforme mostrado abaixo:

![Detalhes do Serviço Cloud](./media/security-center-monitoring/security-center-monitoring-fig8-new3.png) 

Para ver uma explicação mais prescritiva sobre esta recomendação, clique em Atualizar **versão do SO** na coluna **DESCRIÇÃO**. O painel **Atualizar versão do SO (Pré-visualização)** irá abrir com mais detalhes.

![Recomendações dos Serviços Cloud](./media/security-center-monitoring/security-center-monitoring-fig8-new4.png)  

### Monitorizar redes virtuais
Quando clica em **Redes** no mosaico **Estado de funcionamento da segurança de recursos**, o painel **Redes** abre-se com mais detalhes, como é mostrado abaixo:

![Redes](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

#### Recomendações de redes
À semelhança das informações de estado de funcionamento de recursos de máquinas virtuais, este painel fornece uma lista resumida dos problemas na parte superior do painel e uma lista das redes monitorizadas na parte inferior.

A secção da divisão de estado de funcionamento de redes apresenta uma lista de potenciais problemas de segurança e oferece [recomendações](security-center-network-recommendations.md). Os possíveis problemas podem incluir:

* Firewall da Próxima Geração (NGFW) não instalada
* Grupos de Segurança de Rede (NSGs) em sub-redes não ativadas
* NSGs em VMs não ativadas
* Restringir o acesso externo através do ponto final externo público
* Pontos finais com acesso à Internet com bom estado de funcionamento

Quando clica numa dessas recomendações, um novo painel abre-se com informações mais detalhadas sobre a recomendação conforme mostrado no exemplo abaixo.

![Restringir ponto final](./media/security-center-monitoring/security-center-monitoring-fig9-ga.png)

Neste exemplo, o painel **Configurar Grupos de Segurança de Rede em Falta para Sub-redes** tem uma lista de sub-redes e máquinas virtuais com os quais a proteção de NSG está em falta. Se clicar na sub-rede em que pretende aplicar o NSG, irá abrir-se outro painel.

No painel **Escolher grupo de segurança de rede** irá selecionar o Grupo de Segurança de Rede mais adequado para a sub-rede ou pode criar um novo Grupo de Segurança de Rede. 

#### Secção pontos finais com acesso à Internet
Na secção **Pontos finais com acesso à Internet**, poderá ver as VMs que estão atualmente configuradas com um ponto final com acesso à Internet e o respetivo estado atual.

![Ponto final com acesso à Internet](./media/security-center-monitoring/security-center-monitoring-fig10-ga.png)

Esta tabela tem o nome do ponto final que representa a VM, o endereço IP com acesso à Internet, o estado de gravidade atual do NSG e da NGFW. A tabela está ordenada por gravidade, conforme descrito abaixo:

* Vermelho (na parte superior): alta prioridade e deve ser resolvido imediatamente 
* Cor de laranja: prioridade média e deve ser resolvido com a maior brevidade possível
* Verde (por último): estado de funcionamento

#### Secção Topologia de redes
Na secção **Topologia de redes**, existe uma vista hierárquica dos recursos, conforme mostrado abaixo:

![Topologia de redes](./media/security-center-monitoring/security-center-monitoring-fig121-new4.png)

Esta tabela está ordenada (VMs e Sub-redes) por gravidade, conforme descrito abaixo:

* Vermelho (na parte superior): alta prioridade e deve ser resolvido imediatamente 
* Cor de laranja: prioridade média e deve ser resolvido com a maior brevidade possível
* Verde (por último): estado de funcionamento

Nesta vista de topologia, o primeiro nível tem [Virtual Networks](../virtual-network/virtual-networks-overview.md), [Gateways da Virtual Network](../vpn-gateway/vpn-gateway-site-to-site-create.md) e [(Virtual Network clássica)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). O segundo nível tem sub-redes e o terceiro nível tem as VMs que pertencem a essas sub-redes. A coluna da direita tem o estado atual do Grupo de Segurança de Rede (NSG) para esses recursos, conforme mostrado no exemplo seguinte:

![Árvore de rede](./media/security-center-monitoring/security-center-monitoring-fig12-ga.png)

A parte inferior deste painel tem as recomendações para esta VM, semelhantes ao que está descrito acima. Pode clicar numa recomendação para obter mais informações ou aplicar o controlo/configuração de segurança necessário.

### Monitorizar dados
Quando clica em **Dados** no mosaico **Estado de funcionamento da segurança de recursos**, o painel **SQL** abre-se com recomendações para problemas como auditoria, encriptação de dados transparente não ativada, entre outros. Também tem [recomendações](security-center-sql-service-recommendations.md) para o estado de funcionamento geral da base de dados.

![Estado de funcionamento de Recursos SQL](./media/security-center-monitoring/security-center-monitoring-fig13-ga.png)

Pode clicar em qualquer uma destas recomendações e obter mais detalhes sobre mais ações para resolver o problema. O exemplo abaixo mostra a expansão da recomendação **Auditoria de Base de Dados não ativada**.

![Estado de funcionamento de Recursos SQL](./media/security-center-monitoring/security-center-monitoring-fig14-ga.png)

O painel **Ativar Auditoria nas bases de dados SQL** tem as seguintes informações:

* Uma lista de bases de dados SQL
* O servidor no qual estão localizadas
* Informações sobre se esta definição foi herdada a partir do servidor ou se é exclusiva desta base de dados
* O estado atual
* A gravidade do problema

Quando clica na base de dados para abordar esta recomendação, o painel **Deteção de Ameaças e Auditoria** abre-se conforme mostrado abaixo.

![Estado de funcionamento de Recursos SQL](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

Para ativar a auditoria, basta selecionar **ATIVAR** sob a opção **Auditoria**.

### Monitorizar aplicações
Se a carga de trabalho do Azure tiver aplicações localizadas nas [VMs do gestor de recursos](../resource-manager-deployment-model.md) com portas Web expostas (as portas TCP 80 e 443), o Centro de Segurança pode monitorizá-las para identificar potenciais problemas de segurança e recomendar passos de remediação. Quando clica no mosaico **Aplicações**, o painel **Aplicações** abre-se com uma série de recomendações na secção passos de prevenção. Também mostra a divisão das aplicações por anfitrião/IP virtual conforme mostrado abaixo.

![Estado de funcionamento da segurança de aplicações](./media/security-center-monitoring/security-center-monitoring-fig16-ga.png)

Tal como fez com as outras recomendações, pode clicar na mesma para ver mais detalhes sobre o problema e para saber como corrigir. O exemplo apresentado na figura abaixo é uma aplicação que foi identificada como uma aplicação Web não segura. Quando seleciona a aplicação que foi considerada não segura, abre-se outro painel com a seguinte opção disponível:

![Aplicações](./media/security-center-monitoring/security-center-monitoring-fig17-ga.png)

Este painel apresentará uma lista de todas as recomendações para esta aplicação. Ao clicar na recomendação **Adicionar uma firewall de aplicações Web**, o painel **Adicionar uma firewall de aplicações Web** será aberto com opções para que instale uma WAF (firewall de aplicações Web) de terceiros, conforme mostrado abaixo.

![Adicionar WAF](./media/security-center-monitoring/security-center-monitoring-fig18-ga.png)

## Consultar também
Neste documento, aprendeu a utilizar as capacidades de monitorização no Centro de Segurança do Azure. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](security-center-policies.md) – Saiba como configurar definições de segurança no Centro de Segurança do Azure
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure

<!--HONumber=Sep16_HO4-->


