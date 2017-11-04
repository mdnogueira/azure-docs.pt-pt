Pode tirar partido das muitas oportunidades para monitorizar as suas VMs ao recolher, visualizar e analisar o diagnóstico e dados de registo. Para fazer simples [monitorização](../articles/monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) da sua VM, pode utilizar o ecrã descrição geral para a VM no portal do Azure. Pode utilizar [extensões](../articles/virtual-machines/windows/extensions-features.md) para configurar diagnósticos nas VMs para recolher dados de métricos adicionais. Também pode utilizar as opções de monitorização mais avançadas, tais como [Application Insights](../articles/application-insights/app-insights-overview.md) e [Log Analytics](../articles/log-analytics/log-analytics-overview.md).

## <a name="diagnostics-and-metrics"></a>Os diagnósticos e métricas 

Pode configurar e monitorizar a coleção de [dados de diagnóstico](https://docs.microsoft.com/cli/azure/vm/diagnostics) utilizando [métricas](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) no portal do Azure, a CLI do Azure, Azure PowerShell e aplicações de programação Interfaces de programação (APIs). Pode, por exemplo:

- **Observe métricas básicas para a VM.** No ecrã descrição geral do portal do Azure, as métricas básicas apresentadas incluem a utilização da CPU, utilização de rede, total de bytes de disco e operações de disco por segundo.

- **Ativar a recolha de diagnóstico de arranque e vê-la no portal do Azure.** Quando colocar a sua própria imagem para o Azure ou mesmo arrancar uma das imagens da plataforma, pode ser muitos motivos por que motivo uma VM obtém num Estado não arranque. Pode ativar facilmente diagnóstico de arranque quando criar uma VM clicando **ativado** para diagnóstico de arranque na secção monitorização do ecrã de definições.

    Como efetuar o arranque de VMs, o agente de diagnóstico de arranque captura a saída de arranque e armazena-os no armazenamento do Azure. Estes dados podem ser utilizados para resolver problemas de arranque VM. Diagnóstico de arranque não é ativado automaticamente quando criar uma VM a partir de ferramentas de linha de comandos. Antes de ativar o diagnóstico de arranque, uma conta de armazenamento tem de ser criada para armazenar os registos de arranque. Se ativar o diagnóstico de arranque no portal do Azure, uma conta de armazenamento é criada automaticamente para si.

    Se não ativar o diagnóstico de arranque quando a VM foi criada, pode sempre ativá-la mais tarde utilizando [CLI do Azure](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmbootdiagnostics), ou um [modelo Azure Resource Manager](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Ative a recolha de dados de diagnóstico de SO convidado.** Quando cria uma VM, terá a oportunidade no ecrã de definições para ativar o diagnóstico de SO convidado. Quando ativar a recolha de dados de diagnóstico, o [IaaSDiagnostics extensão para Linux](../articles/virtual-machines/linux/diagnostic-extension.md) ou [IaaSDiagnostics extensão para Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) é adicionado à VM, que permite-lhe recolher adicionais dados de disco, memória e CPU.

    Pode utilizar os dados de diagnóstico recolhidos, para configurar o dimensionamento automático para as suas VMs. Também pode configurar os registos para armazenar os dados e configurar alertas para informá-lo quando o desempenho não seja apresentado corretamente.

## <a name="alerts"></a>Alertas

Pode criar [alertas](../articles/monitoring-and-diagnostics/monitoring-overview-alerts.md) com base nas métricas de desempenho específicos. Os problemas que pode ser alertado de exemplos de quando a utilização média da CPU excede um determinado limiar ou espaço em disco livre disponíveis descerem abaixo de um determinado período. Alertas podem ser configurados na ferramenta de [portal do Azure](../articles/monitoring-and-diagnostics/insights-alerts-portal.md), utilizando [Azure PowerShell](../articles/monitoring-and-diagnostics/insights-alerts-powershell.md), ou o [CLI do Azure](../articles/monitoring-and-diagnostics/insights-alerts-command-line-interface.md).

## <a name="azure-service-health"></a>Azure Service Health

[Estado de funcionamento de serviço do Azure](../articles/service-health/service-health-overview.md) fornece suporte e documentação de orientação personalizada quando afetam problemas nos serviços do Azure e ajuda-o a preparar-se para futuras a manutenção planeada. Estado de funcionamento de serviço do Azure alertas e as equipas utilizar notificações de destino e flexíveis.

## <a name="azure-resource-health"></a>Azure Resource Health

[Estado de funcionamento de recursos do Azure](../articles/service-health/resource-health-overview.md) ajuda-o a diagnosticar e obter suporte, quando um problema do Azure tem impacto sobre os recursos. Este serviço informa-o do estado de funcionamento atual e antigo dos seus recursos e ajuda-o a mitigar problemas. O estado de funcionamento dos recursos fornece suporte técnico quando precisa de ajuda para resolver problemas relacionados com o serviço do Azure.

## <a name="logs"></a>Registos

O [registo de atividade do Azure](../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md) é um registo de subscrição que fornece informações sobre os eventos de nível de subscrição ocorridos no Azure. O registo inclui uma série de dados, a partir dos dados operacionais do Azure Resource Manager para atualizações de eventos de estado de funcionamento do serviço. Pode clicar em registo de atividade no portal do Azure para ver o registo para a VM.

Algumas das ações que pode fazer com o registo de atividade incluem:

- Criar um [alerta sobre um evento de registo de atividade](../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md).
- [Transmiti-lo para um Hub de eventos](../articles/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md) para ingestão por um serviço independente ou uma solução de análise personalizada, tais como o Power BI.
- Analisá-lo no Power BI utilizando o [pacote de conteúdos do Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Guarde-o para uma conta de armazenamento](../articles/monitoring-and-diagnostics/monitoring-archive-activity-log.md) de inspeção de arquivo ou manual. Pode especificar o período de retenção (em dias) com o perfil de registo.

Também pode aceder a dados de registo de atividade, utilizando [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), a [CLI do Azure](https://docs.microsoft.com/cli/azure/monitor), ou [APIs REST do Monitor](https://docs.microsoft.com/rest/api/monitor/).

[Os registos de diagnóstico do Azure](../articles/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) são registos emitidos por sua VM, que fornecem dados avançados, frequentes sobre o respetivo funcionamento. Os registos de diagnóstico diferirem do registo de atividade, fornecendo conhecimentos aprofundados sobre operações que foram executadas dentro da VM.

Algumas das ações que pode fazer com os registos de diagnóstico incluem:

- [Guardá-las para uma conta de armazenamento](../articles/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md) de inspeção de auditoria ou manual. Pode especificar o período de retenção (em dias) utilizando as definições de diagnóstico do recurso.
- [Transmiti-las para os Event Hubs](../articles/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) para ingestão por um serviço independente ou uma solução de análise personalizada, tais como o Power BI.
- Analisá-los com [análise de registos do OMS](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Monitorização avançada

- [Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/) fornece capacidades de remediação de monitorização, alertas e alertas na nuvem e de recursos no local. Pode instalar uma extensão num [VM com Linux](../articles/virtual-machines/linux/extensions-oms.md) ou um [VM do Windows](../articles/virtual-machines/windows/extensions-oms.md) que instala o agente do OMS e inscreve a VM para uma área de trabalho existente do OMS.

- [Análise de registo](../articles/log-analytics/log-analytics-overview.md) é um serviço no OMS que monitoriza a sua nuvem e no local ambientes para manter a respetiva disponibilidade e desempenho. Recolhe dados gerados por recursos nos seus ambientes na cloud e no local e de outras ferramentas de monitorização, para disponibilizar análises relativas a várias origens.

    Para o Windows e VMs com Linux, o método recomendado para recolher métricas e registos é instalar o agente de análise de registos. A forma mais fácil para instalar o agente de análise de registos numa VM é efetuada através de [extensão de VM de análise do registo](../articles/log-analytics/log-analytics-azure-vm-extension.md). Com a extensão simplifica o processo de instalação e configura automaticamente o agente para enviar dados para a área de trabalho de análise de registos que especificar. O agente é também atualizado automaticamente, assegurando que tem as funcionalidades e correções mais recentes.

- [Observador de rede](../articles/network-watcher/network-watcher-monitoring-overview.md) permite-lhe monitorizar a VM e os respetivos recursos associados, como se relacionam com a rede que estão a ser. Pode instalar a extensão de agente do observador de rede num [VM com Linux](../articles/virtual-machines/linux/extensions-nwa.md) ou um [VM do Windows](../articles/virtual-machines/windows/extensions-nwa.md).

## <a name="next-steps"></a>Passos seguintes
- Siga os passos no [monitorizar uma Máquina Virtual do Windows com o Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) ou [monitorize máquinas virtuais Linux com a CLI do Azure](../articles/virtual-machines/linux/tutorial-monitoring.md).
- Saiba mais sobre as melhores práticas em torno [monitorização e diagnóstico](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
