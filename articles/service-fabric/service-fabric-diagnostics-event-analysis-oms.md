---
title: "Análise de eventos de recursos de infraestrutura de serviço do Azure com o OMS | Microsoft Docs"
description: "Saiba mais sobre como visualizar e analisar eventos utilizando o OMS para monitorização e diagnóstico de clusters de Service Fabric do Azure."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: f0cefab15a115719ea9c378546a7e6004bd06187
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="event-analysis-and-visualization-with-oms"></a>Análise de eventos e visualização com o OMS

Operations Management Suite (OMS) é uma coleção de serviços de gestão que ajudam na monitorização e diagnóstico para aplicações e serviços alojados na nuvem. Para obter uma descrição mais detalhada do OMS e que oferece, leia [Novidades OMS?](../operations-management-suite/operations-management-suite-overview.md)

## <a name="log-analytics-and-the-oms-workspace"></a>Análise de registos e a área de trabalho do OMS

Análise de registos recolhe dados a partir dos recursos geridos, incluindo uma tabela de armazenamento do Azure ou um agente e mantém-lo num repositório central. Os dados, em seguida, podem ser utilizado para análise, alertas e visualização ou ainda mais a exportar. Análise de registos suporta eventos, dados de desempenho ou quaisquer outros dados personalizados.

Quando é configurado o OMS, terá acesso ao específico *área de trabalho OMS*, a partir de onde os dados podem ser consultados ou visualizados nos dashboards.

Depois de dados são recebidos pelo análise de registos, OMS tem vários *soluções de gestão* que são prepackaged soluções para monitorizar os dados de entrada, personalizados para vários cenários. Estes incluem uma *análise de recursos de infraestrutura de serviço* solução e um *contentores* solução, o que são os dois mais relevante para diagnóstico e monitorização quando utilizar clusters de Service Fabric. Existem várias outras bem que são vale a explorar e OMS também permite a criação de soluções personalizadas, que pode ler mais sobre [aqui](../operations-management-suite/operations-management-suite-solutions.md). Cada solução que pretenda utilizar para um cluster pode ser configurada na mesma OMS área de trabalho, juntamente com a análise de registos. Áreas de trabalho permitem dashboards personalizados e visualização de dados e as modificações de dados que pretende recolher, processo e analisar.

## <a name="setting-up-an-oms-workspace-with-the-service-fabric-analytics-solution"></a>Configurar uma área de trabalho do OMS com a solução de análise de recursos de infraestrutura de serviço
Recomenda-se que incluem a solução de recursos de infraestrutura de serviço na sua área de trabalho do OMS - inclui um dashboard que mostra os vários canais de registo recebidos do nível da plataforma e a aplicação e fornece a capacidade de registos específicos de Service Fabric de consulta. Eis o aspeto de uma solução de recursos de infraestrutura de serviço relativamente simples, com uma única aplicação implementada num cluster:

![Solução SF do OMS](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

Existem duas formas para fornecer e configurar uma área de trabalho do OMS, através de um modelo do Resource Manager ou diretamente a partir do Azure Marketplace. Utilize o anteriores quando estiver a implementar um cluster e a última opção se já tiver um cluster com o diagnóstico ativada.

### <a name="deploying-oms-using-a-resource-management-template"></a>Implementar OMS através de um modelo de gestão de recursos

Quando implementar um cluster com um modelo do Resource Manager, o modelo também pode criar uma nova área de trabalho OMS, adicione a solução de recursos de infraestrutura de serviço e configurá-lo para ler os dados das tabelas de armazenamento adequado.

>[!NOTE]
>Para isto funcionar, tem de estar ativado para que as tabelas de armazenamento do Azure para existem para OMS diagnóstico / análise de registos ao ler as informações do.

[Aqui](https://azure.microsoft.com/resources/templates/service-fabric-oms/) é um modelo de exemplo que pode utilizar e modificar de acordo com o requisito, que executa acima ações. No caso de que pretende optionality mais, existem alguns modelos mais dão-lhe opções diferentes, dependendo de onde o processo poderá ser de configuração de uma área de trabalho do OMS - podem ser encontrados em [modelos de Service Fabric e OMS](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS).

### <a name="deploying-oms-using-through-azure-marketplace"></a>Implementar utilizando o OMS através do Azure Marketplace

Se preferir adicionar uma área de trabalho do OMS depois de implementar um cluster, aceda a Azure Marketplace e procure *"Serviço de análise de recursos de infraestrutura"*.

![Análise de SF OMS no Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

* Clique em **criar**
* Na janela de criação de análise de recursos de infraestrutura de serviço, clique em **Selecione uma área de trabalho** para o *área de trabalho OMS* campo e, em seguida, **criar uma nova área de trabalho**. Preencha as entradas necessárias - o único requisito aqui é que a subscrição para o cluster do Service Fabric e a área de trabalho do OMS deve ser o mesmo. Depois das suas entradas tem sido validadas, a sua área de trabalho do OMS começarão a implementar. Isto deverá demorar apenas alguns minutos.
* Quando terminar, clique em **criar** novamente na parte inferior da janela de criação de análise de recursos de infraestrutura de serviço. Certifique-se de que a nova área de trabalho aparece em *área de trabalho OMS*. Isto irá adicionar a solução para a área de trabalho que acabou de criar.


Embora esta ação adiciona a solução para a área de trabalho, a área de trabalho ainda tem de estar ligados dos dados de diagnóstico provenientes do seu cluster. Navegue para o grupo de recursos que criou da solução de análise de recursos de infraestrutura de serviço. Deverá ver uma *ServiceFabric (\<nameOfOMSWorkspace\>)*.

* Clique na solução de para navegar para a página de descrição geral, a partir de onde pode alterar as definições de solução, definições da área de trabalho e navegue para o portal do OMS.
* No menu de navegação esquerdo, clique em **registos de contas de armazenamento**, em *origens de dados da área de trabalho*.

    ![Solução de análise de recursos de infraestrutura de serviço no Portal](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-portal.png)

* No *os registos da conta de armazenamento* página, clique em **adicionar** na parte superior para adicionar registos do cluster para a área de trabalho.
* Clique em **conta de armazenamento** para adicionar a conta apropriada criada no seu cluster. Se utilizou o nome predefinido, a conta de armazenamento com o nome *sfdg\<resourceGroupName\>*. Também pode confirmar isto, verificando o modelo Azure Resource Manager utilizado para implementar o cluster, ao verificar o valor utilizado para o `applicationDiagnosticsStorageAccountName`. Também poderá ter de se deslocar para baixo e clique em **carregar mais** se o nome da conta não for apresentada. Clique no nome da conta de armazenamento correta quando aparece para o selecionar.
* Em seguida, terá de especificar o *tipo de dados*, que deve ser **eventos de recursos de infraestrutura de serviço**.
* O *origem* automaticamente deve ser definido como *WADServiceFabric\*EventTable*.
* Clique em **OK** para ligar a sua área de trabalho para registos do seu cluster.

    ![Adicionar os registos da conta de armazenamento ao OMS](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

* A conta deve são agora apresentadas como parte da sua *os registos da conta de armazenamento* em origens de dados da sua área de trabalho.

Com esta agora adicionou a solução de análise de recursos de infraestrutura de serviço numa área de trabalho de análise de registos do OMS que agora está corretamente ligada à plataforma do cluster e a tabela de registo de aplicação. Pode adicionar origens adicionais para a área de trabalho da mesma forma.

## <a name="using-the-oms-agent"></a>Com o agente do OMS

É recomendado utilizar EventFlow e WAD como soluções de agregação porque permitem uma abordagem mais modulares para diagnóstico e monitorização. Por exemplo, não se pretender alterar as saídas da EventFlow, requer nenhuma alteração ao seu instrumento real, apenas uma modificação simple ao ficheiro de configuração. Se, no entanto, optar por investir em utilizando OMS e está disposto a continuar a utilizá-la para análise de eventos (tem de ser a única plataforma que utilizar, mas em vez disso que irá ser, pelo menos, um das plataformas), recomendamos que explorar a definição de cópia de segurança a [ag do OMS Ente](../log-analytics/log-analytics-windows-agents.md). Deve utilizar também o agente do OMS quando implementar contentores para o cluster, conforme mostrado abaixo.

O processo para efetuar este procedimento é relativamente fácil, uma vez que apenas tem de adicionar o agente, como um máquina virtual conjunto de dimensionamento de extensão para o modelo do Resource Manager, garantindo que é instalado em cada um dos seus nós. Pode encontrar um modelo de Gestor de recursos de exemplo que implementa a área de trabalho do OMS com a solução de Service Fabric (conforme apresentado acima) e adiciona o agente para os nós de clusters que executem [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) ou [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

As vantagens deste são os seguintes:

* Dados mais rico no lado de métricas e contadores de desempenho
* Fácil de configurar as métricas que estão a ser recolhidas do cluster e sem ter de atualizar a configuração do cluster. Alterações às definições do agente podem ser feitas a partir do portal do OMS e o agente é reiniciado automaticamente para corresponder a configuração necessária. Para configurar o agente do OMS para recolher contadores de desempenho específica, aceda à área de trabalho **home page > Definições > dados > contadores de desempenho do Windows** e escolha os dados que gostaria de ver recolhidos
* Dados aparecem mais rapidamente do que o se ter de ser armazenados antes captado pelo OMS / análise de registos
* Monitorização contentores é mais fácil, uma vez que os pode recolher registos de docker (stdout, stderr) e estatísticas (métricas de desempenho nos níveis de contentor e nó)

A principal consideração aqui é que uma vez que o agente será implementado no seu cluster juntamente com as suas aplicações, poderão existir alguns impacto para o desempenho das suas aplicações no cluster.

## <a name="monitoring-containers"></a>Monitorização contentores

Quando implementar contentores para um cluster do Service Fabric, recomenda-se que o cluster foi configurado com o agente do OMS e que a solução de contentores foi adicionada à sua área de trabalho do OMS para ativar a monitorização e diagnóstico. Eis o aspeto a solução de contentores numa área de trabalho:

![Dashboard do OMS básico](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

O agente permite a recolha de vários registos de contentor específico que pode ser consultada na OMS, ou utilizados para indicadores de desempenho visualizadas. Os tipos de registo que são recolhidos são:

* ContainerInventory: mostra informações sobre imagens, o nome e localização do contentor
* ContainerImageInventory: informações sobre imagens de implementada, incluindo os IDs ou tamanhos
* ContainerLog: registos de erro específico, registos de docker (stdout, etc.) e outras entradas
* ContainerServiceLog: comandos de daemon de docker que foram executados
* Desempenho: os contadores de desempenho incluindo contentor cpu, memória, o tráfego de rede do disco e/s e métricas personalizadas as máquinas de anfitrião

Este artigo aborda os passos necessários para configurar a monitorização do contentor para o cluster. Para saber mais sobre a solução de contentores do OMS, consulte os respetivos [documentação](../log-analytics/log-analytics-containers.md).

Para configurar a solução de contentor na sua área de trabalho, certifique-se de que tem o agente do OMS implementado em nós do cluster, seguindo os passos mencionados acima. Assim que o cluster estiver pronto, implemente um contentor no mesmo. Tenha em consideração que pela primeira vez que uma imagem do contentor é implementada para um cluster demora vários minutos a transferência da imagem, dependendo do seu tamanho.

No Azure Marketplace, procure *solução de monitorização do contentor* e criar o **solução de monitorização do contentor** resultado deverá provir cópias de segurança, sob a monitorização + gestão categoria.

![Adicionar a solução de contentores](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

O passo de criação, pede uma área de trabalho do OMS. Selecione a que foi criado com a implementação acima. Este passo adiciona uma solução de contentores na sua área de trabalho do OMS e é detetado automaticamente pelo agente OMS implementado pelo modelo. O agente irá iniciar a recolha de dados em processos de contentores no cluster e menos de 15 minutos ou por isso, deverá ver a solução leve cópias de segurança com dados, como a imagem do dashboard acima.


## <a name="next-steps"></a>Passos seguintes

Explore as seguintes ferramentas OMS e as opções para personalizar uma área de trabalho para as suas necessidades:

* Para os clusters no local, OMS oferece um Gateway (reencaminhar Proxy HTTP) que pode ser utilizado para enviar dados para OMS. Leia mais sobre o que no [ligar os computadores sem acesso à Internet ao OMS utilizando o Gateway do OMS](../log-analytics/log-analytics-oms-gateway.md)
* Configurar OMS configurar [automatizada alertas](../log-analytics/log-analytics-alerts.md) para ajudar a detetar e diagnóstico
* Obter familiarized com o [de registo de pesquisa e consultar](../log-analytics/log-analytics-log-searches.md) funcionalidades disponibilizadas como parte da análise de registos