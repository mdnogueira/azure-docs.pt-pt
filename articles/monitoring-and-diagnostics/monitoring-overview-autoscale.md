---
title: "Descrição geral de dimensionamento automático no Microsoft Azure Virtual Machines, Cloud Services e as aplicações Web | Microsoft Docs"
description: "Descrição geral de dimensionamento automático no Microsoft Azure. Se aplica a máquinas virtuais, Cloud Services e as aplicações Web."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 74bf03be-e658-4239-a214-c12424b53e4c
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2016
ms.author: robb
ms.openlocfilehash: 0a30f0c3b799f76858424d97218c5a6e4386e78e
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Descrição geral de dimensionamento automático no Microsoft Azure Virtual Machines, Cloud Services e as aplicações Web
Este artigo descreve o dimensionamento automático de Microsoft Azure está, suas vantagens e como começar a utilizá-la.  

Dimensionamento automático de Monitor do Azure aplicam-se apenas ao [conjuntos de dimensionamento de Máquina Virtual](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [serviços em nuvem](https://azure.microsoft.com/services/cloud-services/), e [Web Apps do App Service -](https://azure.microsoft.com/services/app-service/web/).

> [!NOTE]
> O Azure tem dois métodos de dimensionamento automático. Uma versão antiga do dimensionamento automático se aplica a máquinas virtuais (conjuntos de disponibilidade). Esta funcionalidade tem suporte limitado e recomendamos-lhe migrar para conjuntos de dimensionamento de máquina virtual para o suporte de dimensionamento automático mais rápido e mais fiável. Uma ligação sobre como utilizar a tecnologia anterior está incluída neste artigo.  
>
>

## <a name="what-is-autoscale"></a>O que é o dimensionamento automático?
Dimensionamento automático permite-lhe ter a quantidade certa de recursos em execução para processar a carga na sua aplicação. Permite-lhe adicionar recursos para processar os aumentos de carga e também poupar dinheiro removendo recursos que são junto inativo. Especifique um número mínimo e máximo de instâncias para executar e adicionar ou remover automaticamente com base num conjunto de regras de VMs. Ter um mínimo de torna se a aplicação está sempre em execução, mesmo em nenhuma carga. Ter um máximo limita o total possível custo de hora a hora. Dimensionar automaticamente entre estas duas extremes através de regras que cria.

 ![Explicado de dimensionamento automático. Adicionar e remover VMs](./media/monitoring-overview-autoscale/AutoscaleConcept.png)

Quando forem cumpridas condições da regra, um ou mais ações de dimensionamento automático são acionadas. Pode adicionar e remover VMs ou efetuar outras ações. O diagrama conceptual seguinte mostra esse processo.  

 ![Diagrama de fluxo de dimensionamento automático](./media/monitoring-overview-autoscale/Autoscale_Overview_v4.png)

A explicação seguinte aplica-se para as peças do diagrama anterior.   

## <a name="resource-metrics"></a>Métricas de recurso
Métricas de emissão de recursos, estas métricas mais tarde são processadas pelas regras. As métricas são fornecidos através de métodos diferentes.
Conjuntos de dimensionamento de máquina virtual utilizam dados de telemetria dos agentes de diagnóstico do Azure, enquanto que as aplicações Web e serviços em nuvem a telemetria provém diretamente a partir da infraestrutura do Azure. Algumas estatísticas frequentemente utilizadas incluem a utilização da CPU, utilização de memória, as contagens de threads, comprimento da fila e a utilização de disco. Para obter uma lista dos dados de telemetria que pode utilizar, consulte [métricas comuns de dimensionamento automático](insights-autoscale-common-metrics.md).

## <a name="custom-metrics"></a>Métricas personalizadas
Também pode tirar partido do seus próprio métricas personalizadas que pode ser emitir a sua aplicação (ões). Se tiver configurado a sua aplicação (ões) para enviar as métricas para o Application Insights pode tirar partido dessas métricas para tomar decisões se dimensionar ou não. 

## <a name="time"></a>Hora
Regras baseadas na agenda são baseadas em UTC. Tem de definir o fuso horário corretamente quando configurar as regras.  

## <a name="rules"></a>Regras
O diagrama mostra apenas uma regra de dimensionamento automático, mas pode ter muitos dos mesmos. Pode criar regras sobrepostas complexas conforme necessário para a sua situação.  Tipos de regras incluem  

* **Com base em métrica** -por exemplo, execute esta ação quando a utilização da CPU é superior a 50%.
* **Baseados no tempo** - por exemplo, o acionador um webhook am cada 8 no Sábado num fuso horário especificado.

Regras baseadas em métrica medem a carga da aplicação e adicionar ou remover as VMs com base em que a carga. Regras baseadas na agenda permitem-lhe dimensionar quando Consulte padrões de tempo na sua carga e pretende dimensionar antes de um aumento de carga possível ou diminuir.  

## <a name="actions-and-automation"></a>Ações e automatização
As regras podem acionar um ou mais tipos de ações.

* **Escala** -dimensionamento de VMs ou reduzir
* **E-mail** -enviar e-mail para os administradores da subscrição, coadministradores e/ou endereço de e-mail adicionais que especificar
* **Automatizar através de webhooks** -chamar webhooks, que podem acionar várias ações complexas dentro ou fora do Azure. Dentro do Azure, pode iniciar um runbook da automatização do Azure, a função do Azure ou o Azure Logic App. URL de terceiros de exemplo fora do Azure incluem serviços como o Slack e Twilio.

## <a name="autoscale-settings"></a>Definições de dimensionamento automático
Dimensionamento automático utilize a seguinte terminologia e estrutura.

- Um **definição de dimensionamento automático** é lido pelo motor de dimensionamento automático para determinar se deve aumentar ou reduzir verticalmente. Contém um ou mais perfis, informações sobre o recurso de destino e as definições de notificação.

    - Um **perfil de dimensionamento automático** é uma combinação de r:

        - **definição de capacidade**, que indica o número mínimo, máximo e valores predefinidos para o número de instâncias.
        - **conjunto de regras**, cada um dos quais inclui um acionador (tempo ou métrica) e uma ação de dimensionamento (ou reduzir verticalmente).
        - **periodicidade**, que indica quando o dimensionamento automático deve colocar este perfil em vigor.

        Pode ter vários perfis permitem-lhe asseguramos diferentes requisitos sobrepostos. Pode fazer com perfis de dimensionamento automático diferente para diferentes horas do dia ou dias da semana, por exemplo.

    - A **definição de notificação** define que notificações devem ocorrer quando ocorre um evento de dimensionamento automático com base no que satisfaçam os critérios de um dos perfis de definição de dimensionamento automático. Dimensionamento automático pode notificar um ou mais endereços de e-mail ou efetuar chamadas para um ou mais webhooks.


![Definição de dimensionamento automático do Azure, o perfil e estrutura de regra](./media/monitoring-overview-autoscale/AzureResourceManagerRuleStructure3.png)

A lista completa dos campos configuráveis e descrições está disponível no [API de REST de dimensionamento automático](https://msdn.microsoft.com/library/dn931928.aspx).

Para obter exemplos de código, consulte

* [Configuração avançada de dimensionamento automático utilizando modelos do Resource Manager para conjuntos de dimensionamento de VM](insights-advanced-autoscale-virtual-machine-scale-sets.md)  
* [API de REST de dimensionamento automático](https://msdn.microsoft.com/library/dn931953.aspx)

## <a name="horizontal-vs-vertical-scaling"></a>Dimensionamento vertical do vs horizontais
Dimensionamento automático só dimensiona horizontalmente, que é um aumento ("out") ou diminuir ("em") no número de instâncias de VM.  É mais flexível numa situação de nuvem como permite-lhe executar potencialmente milhares de VMs para processar carga horizontal.

Em contrapartida, dimensionamento vertical é diferente. Mantém o mesmo número de VMs, mas faz com que as VMs mais ("cópia de segurança") ou menos ("desativado") poderosas. Energia é medida em memória, velocidade da CPU, espaço em disco, etc.  Dimensionamento vertical tem limitações mais. É dependente a disponibilidade de hardware maior, que chega a um limite superior e pode variar consoante a região rapidamente. Também normalmente vertical dimensionamento necessita de uma VM para parar e reiniciar.

Para obter mais informações, consulte [aumentar verticalmente a máquina virtual do Azure com a automatização do Azure](../virtual-machines/linux/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="methods-of-access"></a>Métodos de acesso
Pode configurar através de dimensionamento automático

* [Portal do Azure](insights-how-to-scale.md)
* [PowerShell](insights-powershell-samples.md#create-and-manage-autoscale-settings)
* [Interface de linha de comandos de várias plataformas (CLI)](insights-cli-samples.md#autoscale)
* [API de REST de Monitor do Azure](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## <a name="supported-services-for-autoscale"></a>Serviços suportados para o dimensionamento automático
| Serviço | Esquema & Docs |
| --- | --- |
| Aplicações Web |[Dimensionamento de aplicações Web](insights-how-to-scale.md) |
| Serviços Cloud |[O dimensionamento automático num serviço em nuvem](../cloud-services/cloud-services-how-to-scale-portal.md) |
| Máquinas virtuais: clássico |[Conjuntos de disponibilidade do dimensionamento clássico Máquina Virtual](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Máquinas virtuais: Conjuntos de dimensionamento de Windows |[Define o dimensionamento de dimensionamento da máquina virtual no Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md) |
| Máquinas virtuais: Conjuntos de dimensionamento de Linux |[Dimensionamento de dimensionamento da máquina virtual define no Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) |
| Máquinas virtuais: Exemplo de Windows |[Configuração avançada de dimensionamento automático utilizando modelos do Resource Manager para conjuntos de dimensionamento de VM](insights-advanced-autoscale-virtual-machine-scale-sets.md) |

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o dimensionamento automático, utilize as instruções de dimensionamento automático listadas anteriormente ou consulte os seguintes recursos:

* [Azure métricas comuns para o Monitor de dimensionamento automático](insights-autoscale-common-metrics.md)
* [Melhores práticas para o dimensionamento automático de Monitor do Azure](insights-autoscale-best-practices.md)
* [Utilize ações de dimensionamento automático para enviar correio eletrónico e webhook notificações de alerta](insights-autoscale-to-webhook-email.md)
* [API de REST de dimensionamento automático](https://msdn.microsoft.com/library/dn931953.aspx)
* [Resolução de problemas dimensionamento automático do conjuntos de dimensionamento Máquina Virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)
