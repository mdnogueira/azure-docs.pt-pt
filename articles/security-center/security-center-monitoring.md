---
title: "Monitorização de segurança no Centro de Segurança do Azure | Microsoft Docs"
description: "Este artigo ajuda-o a começar a trabalhar com as capacidades de monitorização no Centro de Segurança do Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: f7f50c305a5ae6ad7bba4e793f4cb6a0735b80b4
ms.contentlocale: pt-pt
ms.lasthandoff: 05/08/2017


---
# <a name="security-health-monitoring-in-azure-security-center"></a>Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure
Este artigo ajuda-o a utilizar as capacidades de monitorização no Centro de Segurança do Azure para monitorizar a conformidade com políticas.

## <a name="what-is-security-health-monitoring"></a>O que é a monitorização do estado de funcionamento de segurança?
Pensamos frequentemente que monitorizar é observar e esperar que um evento ocorra, para que possamos reagir à situação. A monitorização de segurança refere-se ao facto de ter uma estratégia proativa que audita os seus recursos para identificar os sistemas que não cumprem as normas ou melhores práticas organizacionais.

## <a name="monitoring-security-health"></a>Monitorizar o estado de funcionamento de segurança
Depois de ativar [políticas de segurança](security-center-policies.md) para os recursos de uma subscrição, o Centro de Segurança irá analisar a segurança dos seus recursos para identificar potenciais vulnerabilidades. As informações sobre a configuração da rede estão disponíveis de forma instantânea. Pode demorar uma hora ou mais até que as informações acerca da configuração da máquina virtual, tais como o estado da atualização de segurança e a configuração do sistema operativo, fiquem disponíveis. Pode ver o estado de segurança dos seus recursos, juntamente com quaisquer problemas na secção **Prevenção**. Também pode ver uma lista dos problemas no mosaico **Recomendações**.

Para obter mais informações sobre como aplicar recomendações, leia [Implementing security recommendations in Azure Security Center (Implementar recomendações de segurança no Centro de Segurança do Azure)](security-center-recommendations.md).

Sob a secção **Prevenção**, pode monitorizar o estado de segurança dos seus recursos. No exemplo seguinte, pode ver que em cada mosaico do recurso (Computação, Redes, Armazenamento e dados e Aplicação) tem o número total de problemas que foram identificados.

![Mosaico de estado de funcionamento da segurança dos recursos](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)


### <a name="monitor-compute"></a>Monitorizar a computação
Quando clica no mosaico **Computação**, é aberto o painel **Computação**, que mostra três separadores:

- **Descrição geral**: recomendações de monitorização e de máquina virtual.
- **Máquinas virtuais**: lista todas as máquinas virtuais e o estado de segurança atual das mesmas.
- **Serviços Cloud**: lista de todas as funções Web e de trabalho monitorizadas pelo Centro de Segurança.

![Atualização do sistema em falta na máquina virtual](./media/security-center-monitoring/security-center-monitoring-fig1-new002-2017.png)

Existem várias secções em cada separador e pode selecionar, em cada uma delas, uma opção individual para ver mais detalhes sobre os passos recomendados para resolver esse problema em concreto. 

#### <a name="monitoring-recommendations"></a>Recomendações de monitorização
Esta secção mostra o número total de máquinas virtuais que foram inicializadas para a recolha de dados e os respetivos estados atuais. Depois de todas as máquinas virtuais terem inicializado a recolha de dados, estarão preparadas para receber políticas de segurança do Centro de Segurança. Quando clica nesta entrada, é aberto o painel **O Agente de VM está em falta ou não está a responder**. 

![Atualização do sistema em falta na máquina virtual](./media/security-center-monitoring/security-center-monitoring-fig1-new003-2017.png)


#### <a name="virtual-machine-recommendations"></a>Recomendações de Virtual Machines
Esta secção tem um conjunto de [recomendações para cada máquina virtual](security-center-virtual-machine-recommendations.md) monitorizada pelo Centro de Segurança do Azure. A primeira coluna indica a recomendação. A segunda coluna mostra o número total de máquinas virtuais que são afetadas por essa recomendação. A terceira coluna mostra a gravidade do problema, conforme ilustrado na seguinte captura de ecrã.

![Recomendações de Virtual Machines](./media/security-center-monitoring/security-center-monitoring-fig1-new004-2017.png)

> [!NOTE]
> Apenas são apresentadas as máquinas virtuais com, pelo menos, um ponto final público no painel **Estado de Funcionamento da Rede**, na lista **Topologia da rede**.
>
>

Cada recomendação tem um conjunto de ações que pode executar assim que clicar na mesma. Por exemplo, se clicar em **Atualizações do sistema em falta**, o painel **Atualizações do sistema em falta** é apresentado. Lista as máquinas virtuais que têm patches em falta e a gravidade da atualização em falta, conforme mostrado na seguinte captura de ecrã.

![Atualizações do sistema em falta nas máquinas virtuais](./media/security-center-monitoring/security-center-monitoring-fig5-ga.png)

O painel **Atualizações do sistema em falta** mostra uma tabela com as seguintes informações:

* **MÁQUINA VIRTUAL**: o nome da máquina virtual que tem atualizações em falta.
* **ATUALIZAÇÕES DO SISTEMA**: o número de atualizações do sistema que estão em falta.
* **HORA DA ÚLTIMA ANÁLISE**: a hora em que o Centro de Segurança analisou pela última vez a máquina virtual quanto à existência de atualizações.
* **ESTADO**: o estado atual da recomendação:
  * **Aberto**: a recomendação ainda não foi tratada.
  * **Em curso**: a recomendação está a ser aplicada a esses recursos e não tem de tomar qualquer medida.
  * **Resolvido**: a recomendação já foi concluída. (Quando o problema for resolvido, a entrada fica a cinzento).
* **GRAVIDADE**: descreve a gravidade dessa recomendação específica:
  * **Elevada**: existe uma vulnerabilidade num recurso significativo (aplicação, máquina virtual ou grupo de segurança de rede) e necessita de atenção.
  * **Média**: são precisos passos não críticos ou adicionais para concluir um processo ou eliminar uma vulnerabilidade.
  * **Baixa**: uma vulnerabilidade deve ser tratada, mas não necessita de atenção imediata. (Por predefinição, as recomendações baixas não são apresentadas, mas pode filtrar por recomendações baixas se pretender visualizá-las).

Para ver os detalhes das recomendações, clique no nome da máquina virtual. É aberto um painel novo para essa máquina virtual com a lista de atualizações, conforme apresentado na seguinte captura de ecrã.

![Atualizações do sistema em falta para uma máquina virtual específica](./media/security-center-monitoring/security-center-monitoring-fig6-ga.png)

> [!NOTE]
> As recomendações de segurança aqui são as mesmas que as do painel **Recomendações**. Para obter mais informações sobre como resolver recomendações, veja o artigo [Implementing security recommendations in Azure Security Center (Implementar recomendações de segurança no Centro de Segurança do Azure)](security-center-recommendations.md). Este artigo é aplicável não só a máquinas virtuais, mas a todos os recursos que estão disponíveis no mosaico **Estado de Funcionamento do Recurso**.
>
>

#### <a name="virtual-machines-section"></a>Secção Virtual Machines
A secção de máquinas virtuais faculta uma descrição geral de todas as máquinas virtuais e recomendações. Cada coluna representa um conjunto de recomendações como é mostrado na seguinte captura de ecrã:

![Descrição geral de todas as máquinas virtuais e recomendações](./media/security-center-monitoring/security-center-monitoring-fig1-new005-2017.png)

O ícone apresentado em cada recomendação ajuda-o a identificar rapidamente as máquinas virtuais que necessitam de atenção e o tipo de recomendação.

No exemplo anterior, uma máquina virtual tem uma recomendação crítica relativa à proteção do ponto final. Para obter mais informações sobre a máquina virtual, clique na mesma. Abre-se um painel novo que representa esta máquina virtual, conforme mostrado na seguinte captura de ecrã.

![Detalhes de segurança da máquina virtual](./media/security-center-monitoring/security-center-monitoring-fig8-ga.png)

Este painel tem os detalhes de segurança da máquina virtual. Na parte inferior deste painel, pode ver a ação recomendada e a gravidade de cada problema.

#### <a name="cloud-services-section"></a>Secção de serviços cloud
Relativamente aos serviços cloud, é criada uma recomendação quando a versão do sistema operativo está desatualizada, conforme mostrado na captura de ecrã seguinte:

![Estado de funcionamento dos serviços em nuvem](./media/security-center-monitoring/security-center-monitoring-fig1-new006-2017.png)

Num cenário em que tiver uma recomendação (que não é o caso do exemplo anterior), tem de seguir os passos da recomendação para atualizar a versão do sistema operativo. Quando está disponível uma atualização, verá um alerta (vermelho ou cor de laranja, dependendo da gravidade do problema). Se clicar neste alerta nas linhas WebRole1 (executa o Windows Server com a sua aplicação Web automaticamente implementada no IIS) ou WorkerRole1 (executa o Windows Server com a sua aplicação Web automaticamente implementada no IIS), é aberto um painel novo com mais detalhes sobre esta recomendação, conforme mostrado na captura de ecrã seguinte:

![Detalhes do serviço em nuvem](./media/security-center-monitoring/security-center-monitoring-fig8-new3.png)

Para ver uma explicação mais prescritiva sobre esta recomendação, clique em **Atualizar versão do SO** na coluna **DESCRIÇÃO**. O painel **Atualizar versão do SO (Pré-visualização)** será apresentado com mais detalhes.

![Recomendações dos serviços em nuvem](./media/security-center-monitoring/security-center-monitoring-fig8-new4.png)  

### <a name="monitor-virtual-networks"></a>Monitorizar redes virtuais
Quando clica no mosaico **Redes**, o painel **Redes** é apresentado com mais detalhes, conforme mostrado na seguinte captura de ecrã:

![Painel Redes](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

#### <a name="networking-recommendations"></a>Recomendações de redes
À semelhança das informações de estado de funcionamento do recurso da máquina virtual, este painel proporciona uma lista resumida dos problemas na parte superior do painel e uma lista das redes monitorizadas na parte inferior.

A secção da divisão de estado de funcionamento de redes apresenta uma lista de potenciais problemas de segurança e oferece [recomendações](security-center-network-recommendations.md). Os possíveis problemas podem incluir:

* Firewall da Próxima Geração (NGFW) não instalada
* Grupos de segurança de rede em sub-redes não ativados
* Grupos de segurança de rede em máquinas virtuais não ativados
* Restringir o acesso externo através do ponto final externo público
* Pontos finais com acesso à Internet com bom estado de funcionamento

Quando clica numa recomendação, é aberto um novo painel com mais detalhes sobre a recomendação, conforme mostrado no exemplo seguinte.

![Detalhes de uma recomendação no painel Redes](./media/security-center-monitoring/security-center-monitoring-fig9-ga.png)

Neste exemplo, o painel **Configurar Grupos de Segurança de Rede em Falta para Sub-redes** tem uma lista de sub-redes e máquinas virtuais nas quais está em falta a proteção de grupo de segurança de rede. Se clicar na sub-rede na qual pretende aplicar o grupo de segurança de rede, é aberto outro painel.

No painel **Escolher grupo de segurança de rede**, pode selecionar o grupo de segurança de rede mais adequado para a sub-rede ou pode criar um novo grupo de segurança de rede.

#### <a name="internet-facing-endpoints-section"></a>Secção pontos finais com acesso à Internet
Na secção **Pontos finais com acesso à Internet**, pode ver as máquinas virtuais que estão atualmente configuradas com um ponto final com acesso à Internet e o respetivo estado atual.

![Máquinas virtuais configuradas com estado e ponto final com acesso à Internet](./media/security-center-monitoring/security-center-monitoring-fig10-ga.png)

Esta tabela tem o nome do ponto final que representa a máquina virtual, o endereço IP com acesso à Internet, o estado de gravidade atual do grupo de segurança de rede e da NGFW. A tabela está ordenada por gravidade:

* Vermelho (na parte superior): alta prioridade e deve ser resolvido imediatamente
* Cor de laranja: prioridade média e deve ser resolvido com a maior brevidade possível
* Verde (por último): bom estado de funcionamento

#### <a name="networking-topology-section"></a>Secção Topologia de redes
Na secção **Topologia de redes**, existe uma vista hierárquica dos recursos, conforme mostrado na seguinte captura de ecrã:

![Vista hierárquica dos recursos na secção de Topologia de redes](./media/security-center-monitoring/security-center-monitoring-fig121-new4.png)

Esta tabela está ordenada (máquinas virtuais e sub-redes) por gravidade:

* Vermelho (na parte superior): alta prioridade e deve ser resolvido imediatamente
* Cor de laranja: prioridade média e deve ser resolvido com a maior brevidade possível
* Verde (por último): bom estado de funcionamento

Nesta vista de topologia, o primeiro nível tem [máquinas virtuais](../virtual-network/virtual-networks-overview.md), [gateways da rede virtual](/vpn-gateway/vpn-gateway-site-to-site-create.md) e [redes virtuais (clássicas)](/virtual-network/virtual-networks-create-vnet-classic-pportal.md). O segundo nível tem sub-redes e o terceiro nível tem as máquinas virtuais que pertencem a essas sub-redes. A coluna da direita tem o estado atual do grupo de segurança de rede para esses recursos, conforme mostrado no exemplo seguinte:

![Estado do grupo de segurança de rede na secção Topologia de redes](./media/security-center-monitoring/security-center-monitoring-fig12-ga.png)

A parte inferior deste painel tem as recomendações para esta máquina virtual, semelhantes ao que foi descrito anteriormente. Pode clicar numa recomendação para obter mais informações ou aplicar a configuração ou o controlo de segurança necessário.

### <a name="monitor-storage--data"></a>Monitorizar o armazenamento e dados

Quando clica em **Armazenamento e dados** na secção **Prevenção**, o painel **Recursos de Dados** abre-se com recomendações para SQL e Armazenamento. Também tem [recomendações](security-center-sql-service-recommendations.md) para o estado de funcionamento geral da base de dados. Para obter mais informações sobre a encriptação de armazenamento, leia o artigo [Ativar a encriptação para a conta de armazenamento do Azure no Centro de Segurança do Azure](security-center-enable-encryption-for-storage-account.md).

![Recursos de Dados](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

Em **Recomendações SQL**, pode clicar em qualquer recomendação e obter mais detalhes sobre mais ações para resolver um problema. O exemplo seguinte mostra a expansão da recomendação **Auditoria de Base de Dados e Deteção de ameaças em bases de dados SQL**.

![Detalhes sobre uma recomendação de SQL](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

O painel **Ativar Auditoria e Deteção de ameaças nas bases de dados SQL** contém as seguintes informações:

* Uma lista de bases de dados SQL
* O servidor no qual estão localizadas
* Informações sobre se esta definição foi herdada a partir do servidor ou se é exclusiva desta base de dados
* O estado atual
* A gravidade do problema

Quando clica na base de dados para abordar esta recomendação, o painel **Deteção de Ameaças e Auditoria** abre-se conforme mostrado no ecrã seguinte.

![Painel Deteção de Ameaças e Auditoria](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

Para ativar a auditoria, selecione **ATIVAR**, na opção **Auditoria**.

### <a name="monitor-applications"></a>Monitorizar aplicações

Se a carga de trabalho do Azure tiver aplicações localizadas nas [máquinas virtuais (criadas através do Azure Resource Manager)](../azure-resource-manager/resource-manager-deployment-model.md) com portas Web expostas (as portas TCP 80 e 443), o Centro de Segurança poderá monitorizá-las para identificar potenciais problemas de segurança e recomendar passos de remediação. Quando clica no mosaico **Aplicações**, o painel **Aplicações** abre-se com uma série de recomendações apresentadas na secção **Recomendações da aplicação**. De igual modo, mostra a divisão das aplicações por anfitrião/IP virtual, conforme mostrado na seguinte captura de ecrã.

![Estado de funcionamento da segurança de aplicações](./media/security-center-monitoring/security-center-monitoring-fig16-ga.png)

Tal como fez com as outras recomendações, pode clicar na recomendação para ver mais detalhes sobre o problema e para saber como corrigir. O exemplo apresentado na figura seguinte é uma aplicação que foi identificada como uma aplicação Web não segura. Quando seleciona a aplicação que foi considerada não segura, abre-se outro painel com a seguinte opção disponível:

![Detalhes sobre uma aplicação que não é segura](./media/security-center-monitoring/security-center-monitoring-fig17-ga.png)

Este painel terá uma lista de todas as recomendações para esta aplicação. Quando clica na recomendação **Adicionar uma firewall de aplicações Web**, o painel **Adicionar uma Firewall de Aplicações Web** abre-se com opções para a instalação de uma firewall de aplicações Web (WAF) a partir de um parceiro, conforme mostrado na seguinte captura de ecrã.

![Caixa de diálogo Adicionar Firewall de Aplicações Web](./media/security-center-monitoring/security-center-monitoring-fig18-ga.png)

## <a name="see-also"></a>Consultar também
Neste artigo, aprendeu a utilizar as capacidades de monitorização no Centro de Segurança do Azure. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](security-center-policies.md): saiba como configurar definições de segurança no Centro de Segurança do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md): saiba como gerir e responder a alertas de segurança.
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md): saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md): encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): encontre publicações no blogue acerca da segurança e conformidade do Azure.

