---
title: "Descrição geral de dimensionamento automático com conjuntos de dimensionamento de máquina virtual do Azure | Microsoft Docs"
description: "Saiba mais sobre as diferentes formas que pode dimensionar automaticamente um conjunto com base no desempenho ou numa agenda fixa de dimensionamento de máquina virtual do Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 868523a3aca441a47218297be2ce9f9e46dd84a1
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Define a descrição geral de dimensionamento automático com uma escala de máquina virtual do Azure
Um conjunto de dimensionamento da máquina virtual do Azure automaticamente pode aumentar ou reduzir o número de instâncias VM que executar a sua aplicação. Este comportamento automatizado e elástico reduz os custos de gestão para monitorizar e otimizar o desempenho da sua aplicação. Criar regras que definem o desempenho minimamente aceitável para uma experiência de cliente positivo. Quando os limiares definidos são cumpridos, regras de dimensionamento automático tomar medidas para ajustar a capacidade do seu conjunto de dimensionamento. Também pode agendar eventos automaticamente aumentar ou diminuir a capacidade do seu conjunto de dimensionamento em fixo vezes. Este artigo fornece uma descrição geral do que desempenho métricas estão disponíveis e pode efetuar o dimensionamento automático de ações.


## <a name="benefits-of-autoscale"></a>Vantagens de dimensionamento automático
Se aumentar o seu pedido de aplicação, a carga sobre as instâncias de VM no seu dimensionamento definir aumenta. Se este aumento de carga é consistente, em vez de apenas um breve pedido, pode configurar regras de dimensionamento automático para aumentar o número de instâncias VM no conjunto de dimensionamento.

Quando estas instâncias VM são criadas e as suas aplicações são implementadas, inicia o conjunto de dimensionamento distribuir o tráfego aos mesmos através do Balanceador de carga. Controlar as métricas para monitorizar, tais como CPU ou memória, quanto a carga da aplicação têm de cumprir um determinado limiar, e definir quantas instâncias VM para adicionar à escala.

Num evening ou fim de semana, pode diminuir o seu pedido de aplicação. Se este carregamento menor consistente durante um período de tempo, pode configurar regras de dimensionamento automático para reduzir o número de instâncias VM no conjunto de dimensionamento. Esta ação de dimensionamento reduz o custo para executar a escala definida como executar apenas o número de instâncias necessário para satisfazer a procura atual.


## <a name="use-host-based-metrics"></a>Utilizar métricas baseada no anfitrião
Pode criar regras de dimensionamento automático métricas esse anfitrião incorporadas disponíveis das instâncias de VM. Métricas de anfitrião dão-lhe visibilidade para o desempenho das instâncias da VM na escala definir sem a necessidade de instalar ou configurar agentes adicionais e coleções de dados. Regras de dimensionamento automático que utilizam estas métricas podem dimensionar o ou ao número de instâncias de VM em resposta a utilização da CPU, a pedido de memória ou aceder ao disco.

Podem ser criadas regras de dimensionamento automático que utilizem métricas baseadas no anfitrião com uma das seguintes ferramentas:

- [Portal do Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md)
- [CLI 2.0 do Azure](virtual-machine-scale-sets-autoscale-cli.md)

Para criar regras de dimensionamento automático que utilizem métricas de desempenho mais detalhadas, pode [instalar e configurar a extensão de diagnóstico do Azure](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) em instâncias VM, ou [configurar a utilização da aplicação App Insights](#application-level-metrics-with-app-insights).

Regras de dimensionamento automático que utilizem métricas baseada no anfitrião, as métricas VM no convidado com a extensão de diagnóstico do Azure e informações de aplicação podem utilizar as seguintes definições de configuração.

### <a name="metric-sources"></a>Métricas origens
Regras de dimensionamento automático podem utilizar as métricas de uma das seguintes origens:

| Origem métrica        | Caso de utilização                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Conjunto de dimensionamento atual    | Baseada no anfitrião com base nas métricas que não necessitam de agentes adicionais a ser instalados ou configurados.                                  |
| Conta de armazenamento      | A extensão de diagnóstico do Azure escreve métricas de desempenho para o storage do Azure que, em seguida, é consumido para acionar as regras de dimensionamento automático. |
| Fila de barramento de serviço    | A aplicação ou outros componentes, podem transmitir mensagens numa fila do Service Bus do Azure para regras de Acionador.                   |
| Application Insights | Um pacote de instrumentação instalado na sua aplicação que fluxos métricas diretamente a partir da aplicação.                         |


### <a name="autoscale-rule-criteria"></a>Critérios de regra de dimensionamento automático
As métricas seguintes baseada no anfitrião estão disponíveis para utilização quando criar regras de dimensionamento automático. Se utilizar a extensão de diagnóstico do Azure ou o App Insights, é possível definir as métricas para monitorizar e utilizar com as regras de dimensionamento automático.

| Nome da métrica               |
|---------------------------|
| Percentagem de CPU            |
| Rede no                |
| Limite de rede               |
| Bytes de leitura do disco           |
| Bytes de escrita do disco          |
| Disco lidos/seg de operações  |
| Operações de escrita de disco/seg |
| Créditos de CPU restante     |
| Créditos de CPU consumidos      |

Quando criar regras de dimensionamento automático para monitorizar uma métrica fornecida, as regras de observar uma das ações de agregação de métricas seguintes:

| Tipo de agregação |
|------------------|
| Média          |
| Mínimo          |
| Máximo          |
| Total            |
| última             |
| Contagem            |

As regras de dimensionamento automático, em seguida, são acionadas quando as métricas são comparadas com o limiar definido com um dos seguintes operadores:

| operador                 |
|--------------------------|
| Mais do que             |
| Maior ou igual a |
| Menos do que                |
| Menor ou igual a    |
| Igual a                 |
| Não é igual a             |


### <a name="actions-when-rules-trigger"></a>Ações ao acionam as regras
Quando um acionadores de regra de dimensionamento automático, o conjunto de dimensionamento pode dimensionar automaticamente de uma das seguintes formas:

| Operação de dimensionamento     | Caso de utilização                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Aumentar a contagem por   | Um número fixo de instâncias VM para criar. É útil em conjuntos de dimensionamento com um número mais pequeno de VMs.                                           |
| Percentagem de aumento por | Um aumento de instâncias VM com base em percentagem. Boa para escala maior define onde um aumento fixo poderá não intensas em termos melhorar o desempenho. |
| Aumentar a contagem a   | Crie várias instâncias de VM são necessários para atingir o período máximo pretendido.                                                            |
| Contagem de diminuir a   | Um número fixo de instâncias VM para remover. É útil em conjuntos de dimensionamento com um número mais pequeno de VMs.                                           |
| Diminuir por cento por | Um decréscimo baseado em percentagem de instâncias VM. Boa para escala maior define onde um aumento fixo pode não intensas em termos reduzir o consumo de recursos e os custos. |
| Contagem de diminuir a   | Remova como várias instâncias de VM são necessários para atingir uma quantidade mínima pretendida.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>Métricas VM no convidado com a extensão de diagnóstico do Azure
A extensão de diagnóstico do Azure é um agente que é executado dentro de uma instância VM. O agente monitoriza e guarda as métricas do desempenho ao armazenamento do Azure. Estas métricas de desempenho contenham informações mais detalhadas sobre o estado da VM, como *AverageReadTime* para discos ou *PercentIdleTime* para a CPU. Pode criar regras de dimensionamento automático com base numa deteção mais detalhada do desempenho de VM, não apenas a percentagem de consumo de memória ou a utilização da CPU.

Para utilizar a extensão de diagnóstico do Azure, deve criar contas de armazenamento do Azure para as instâncias de VM, instale o agente de diagnóstico do Azure e configurar as VMs para contadores de desempenho específicos de fluxo para a conta de armazenamento.

Para obter mais informações, veja os artigos para saber como ativar a extensão de diagnóstico do Azure numa [VM do Linux](../virtual-machines/linux/diagnostic-extension.md) ou numa [VM do Windows](../virtual-machines/windows/ps-extensions-diagnostics.md).


## <a name="application-level-metrics-with-app-insights"></a>Métricas de nível de aplicação com o App Insights
Para obter mais visibilidade para o desempenho das suas aplicações, pode utilizar o Application Insights. Instalar um pacote de instrumentação pequeno na sua aplicação que monitoriza a aplicação e envia a telemetria para o Azure. Pode monitorizar métricas, tais como os tempos de resposta da aplicação, o desempenho da carga de página, contagens e da sessão. Estas métricas de aplicação podem ser utilizadas para criar regras de dimensionamento automático num nível granular e incorporado como acionar as regras com base em conhecimentos acionáveis que possam afetar a experiência do cliente.

Para obter mais informações sobre o App Insights, veja [O que é o Application Insights](../application-insights/app-insights-overview.md).


## <a name="scheduled-autoscale"></a>Dimensionamento automático agendado
Também pode criar regras de dimensionamento automático com base no agendas. Estas regras baseadas na agenda permitem-lhe automaticamente o número de instâncias VM em fixo tempos de escala. Com as regras com base no desempenho, poderá existir um impacto no desempenho da aplicação antes do acionador de regras de dimensionamento automático e as novas instâncias de VM são aprovisionadas. Se pode prevê esse pedido, as instâncias de VM adicionais são aprovisionados e pronto para a procura de utilização e aplicação de cliente adicionais.

Os exemplos seguintes são cenários que podem tirar partido da utilização de regras de dimensionamento automático baseadas na agenda:

- Ampliar automaticamente o número de instâncias VM no início do dia de trabalho quando aumenta a pedido do cliente. No fim do dia de trabalho, dimensione o número de instâncias VM para minimizar os custos de recurso noite para o dia quando a utilização de aplicações é baixa.
- Se um departamento utiliza uma aplicação descontos elevados no algumas partes do mês ou fiscal ciclo, dimensione automaticamente o número de instâncias VM para acomodar as exigências adicionais.
- Quando existe um evento de marketing, promoção ou venda de férias, pode dimensionar o número de instâncias VM à frente da pedido do cliente previsto. 


## <a name="next-steps"></a>Passos seguintes
Pode criar regras de dimensionamento automático que utilizem métricas baseada no anfitrião com uma das seguintes ferramentas:

- [Portal do Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md)
- [CLI 2.0 do Azure](virtual-machine-scale-sets-autoscale-cli.md)

Esta descrição geral detalhada como utilizar regras de dimensionamento automático para aumentar horizontalmente e aumentar ou diminuir o *número* de instâncias VM no seu dimensionamento definido. Também pode reduzir verticalmente para aumentar ou diminuir a instância VM *tamanho*. Para obter mais informações, consulte [Vertical dimensionamento automático com conjuntos de dimensionamento da Máquina Virtual](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Para obter informações sobre como gerir as instâncias de VM, consulte [conjuntos de dimensionamento de máquina virtual de gerir com o Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Para saber como gerar alertas quando o dimensionamento automático regras acionador, consulte [utilize ações de dimensionamento automático para enviar correio eletrónico e webhook notificações de alertas no Monitor de Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Também pode [registos de auditoria de Utilize para enviar correio eletrónico e webhook notificações de alertas no Monitor de Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).