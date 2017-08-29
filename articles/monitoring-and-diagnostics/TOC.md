# Descrição geral
## [Ferramentas de monitorização no Azure](monitoring-overview.md)
## [Azure Monitor](monitoring-overview-azure-monitor.md)
## [Métricas](monitoring-overview-metrics.md)
## [Alertas](monitoring-overview-alerts.md)
## [Dimensionamento Automático](monitoring-overview-autoscale.md)
## [Registo de atividades](monitoring-overview-activity-logs.md)
## [Grupos de Ação](monitoring-action-groups.md)
## [Registos de Diagnóstico](monitoring-overview-of-diagnostic-logs.md)
## [Integrações de Parceiros](monitoring-partners.md)
## [Extensão do Diagnóstico do Azure](azure-diagnostics.md)


# Introdução
## [Introdução ao Azure Monitor](monitoring-get-started.md)
## [Introdução ao Dimensionamento Automático](monitoring-autoscale-get-started.md)
## [Permissões e Segurança de Funções](monitoring-roles-permissions-security.md)


# Procedimento
## Utilizar alertas
### [Configurar alertas no Portal do Azure](insights-alerts-portal.md)
### [Configurar alertas com a CLI](insights-alerts-command-line-interface.md)
### [Configurar alertas com o PowerShell](insights-alerts-powershell.md)
### [Chamar um webhook através de um alerta de métrica](insights-webhooks-alerts.md)
### [Criar um alerta de métrica com um modelo do Resource Manager](monitoring-enable-alerts-using-template.md)
## Utilizar dimensionamento automático
### [Melhores Práticas](insights-autoscale-best-practices.md)
### [Métricas comuns](insights-autoscale-common-metrics.md)
### [Padrões comuns](monitoring-autoscale-common-scale-patterns.md)
### [Dimensionamento automático através de uma métrica personalizada](monitoring-autoscale-scale-by-custom-metric.md)
### [Dimensionar automaticamente os conjuntos de dimensionamento de VMs com modelos do Resource Manager](insights-advanced-autoscale-virtual-machine-scale-sets.md)
### [Dimensionar automaticamente máquinas num conjunto de dimensionamento de VMs](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
### [Configurar webhooks e notificações de e-mail no dimensionamento automático](insights-autoscale-to-webhook-email.md)
## Utilizar o registo de atividades
### [Ver eventos no registo de atividades](../azure-resource-manager/resource-group-audit.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
### [Configurar alertas num evento do registo de atividades](monitoring-activity-log-alerts.md)
### [Arquivar registo de atividades](monitoring-archive-activity-log.md)
### [Transmitir em fluxo o registo de atividades para o Hubs de Eventos](monitoring-stream-activity-logs-event-hubs.md)
### [Auditar operações com o Resource Manager](../azure-resource-manager/resource-group-audit.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
### [Criar alertas do registo de atividades com o Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
### [Alertas de Migração para o Registo de Atividades](monitoring-migrate-management-alerts.md)
## Utilizar as Notificações do serviço
### [Ver notificações do serviço](monitoring-service-notifications.md)
### [Configurar alertas nas notificações do serviço](monitoring-activity-log-alerts-on-service-notifications.md)
## Utilizar os Grupos de Ação
### [Saiba mais sobre o esquema de webhook](monitoring-activity-log-alerts-webhook.md)
### [Comportamento do Alerta por SMS](monitoring-sms-alert-behavior.md)
### [Limitar a Taxa de Alertas](monitoring-alerts-rate-limiting.md)
### [Criar grupos de ação com o Resource Manager](monitoring-create-action-group-with-resource-manager-template.md)
## Gerir registos de diagnóstico
### [Arquivo](monitoring-archive-diagnostic-logs.md)
### [Transmitir em Fluxo nos Hubs de Eventos](monitoring-stream-diagnostic-logs-to-event-hubs.md)
### [Ativar as definições de Diagnóstico com modelos do Resource Manager](monitoring-enable-diagnostic-logs-using-template.md)
## Utilizar a API REST
### [Guia de utilização da API REST](monitoring-rest-api-walkthrough.md)
## Utilizar a extensão do Diagnóstico do Azure
### [Enviar para o Application Insights](azure-diagnostics-configure-application-insights.md)
### [Enviar para os Hubs de Eventos](azure-diagnostics-streaming-event-hubs.md)
### [Resolução de problemas](azure-diagnostics-troubleshooting.md)

# Referência
## [Exemplos de código](https://azure.microsoft.com/en-us/resources/samples/?service=monitor)
## [Origens dos dados de monitorização](monitoring-data-sources.md)
## [Lista de Métricas Suportadas](monitoring-supported-metrics.md)
## [Esquema de eventos do Registo de Atividades](monitoring-activity-log-schema.md)
## [Serviços, categorias e esquemas suportados dos registos de diagnóstico](monitoring-diagnostic-logs-schema.md)
## [PowerShell](/powershell/module/azurerm.insights)
## [.NET](https://msdn.microsoft.com/library/azure/dn802153)
## [REST](/rest/api/monitor/)
## [Esquema da extensão do Diagnóstico do Azure](azure-diagnostics-schema.md)
### [1.0](azure-diagnostics-schema-1dot0.md)
### [1.2](azure-diagnostics-schema-1dot2.md)
### [1.3 e posterior](azure-diagnostics-schema-1dot3-and-later.md)

# Recursos
## [Exemplos da CLI 1.0 do Azure](insights-cli-samples.md)
## [Mapa do Azure](https://azure.microsoft.com/roadmap/?category=monitoring-management)
## [Exemplos do PowerShell](insights-powershell-samples.md)
## [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/)
## [Vídeos](https://azure.microsoft.com/resources/videos/index/?services=monitor)
## [Modelos de início rápido](https://azure.microsoft.com/en-us/resources/templates/?resourceType=Microsoft.Insights)
