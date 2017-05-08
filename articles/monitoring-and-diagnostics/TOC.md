# Descrição geral
## [Descrição Geral do Azure Monitor](monitoring-overview.md)
## [Métricas](monitoring-overview-metrics.md)
## [Alertas](monitoring-overview-alerts.md)
## [Dimensionamento Automático](monitoring-overview-autoscale.md)
## [Registo de atividades](monitoring-overview-activity-logs.md)
## [Grupos de Ação](monitoring-action-groups.md)
## [Registos de Diagnóstico](monitoring-overview-of-diagnostic-logs.md)
## [Integrações de Parceiros](monitoring-partners.md)
## [Diagnóstico do Azure](azure-diagnostics.md)


# Introdução
## [Introdução ao Azure Monitor](monitoring-get-started.md)
## [Permissões e Segurança de Funções](monitoring-roles-permissions-security.md)

# Procedimento
## Utilizar alertas
### [Configurar alertas no Portal do Azure](insights-alerts-portal.md)
### [Configurar alertas com a CLI](insights-alerts-command-line-interface.md)
### [Configurar alertas com o PowerShell](insights-alerts-powershell.md)
### [Configurar um webhook num alerta de métrica](insights-webhooks-alerts.md)
### [Criar um alerta de métrica com um modelo do Resource Manager](monitoring-enable-alerts-using-template.md)
## Utilizar dimensionamento automático
### [Melhores Práticas do dimensionamento automático](insights-autoscale-best-practices.md)
### [Métricas comuns do dimensionamento automático](insights-autoscale-common-metrics.md)
### [Dimensionar automaticamente os Conjuntos de Dimensionamento de VMs com modelos do Resource Manager](insights-advanced-autoscale-virtual-machine-scale-sets.md)
### [Dimensionar automaticamente máquinas num conjunto de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
### [Configurar webhooks e notificações de e-mail no dimensionamento automático](insights-autoscale-to-webhook-email.md)
## Utilizar o registo de atividades
### [Ver eventos no registo de atividades](insights-debugging-with-events.md)
### [Configurar alertas num evento do registo de atividades](monitoring-activity-log-alerts.md)
### [Arquivar registo de atividades](monitoring-archive-activity-log.md)
### [Transmitir em fluxo o registo de atividades para o Hubs de Eventos](monitoring-stream-activity-logs-event-hubs.md)
### [Auditar operações com o Resource Manager](../azure-resource-manager/resource-group-audit.md)
### [Criar alertas do registo de atividades com o Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
## Notificações do serviço
### [Ver notificações do serviço](monitoring-service-notifications.md)
### [Configurar alertas nas notificações do serviço](monitoring-activity-log-alerts-on-service-notifications.md)
## Grupos de Ação
### [Saiba mais sobre o esquema de webhook](monitoring-activity-log-alerts-webhook.md)
### [Comportamento do Alerta por SMS](monitoring-sms-alert-behavior.md)
### [Limitar a Taxa de Alertas](monitoring-alerts-rate-limiting.md)
### [Criar grupos de ação com o Resource Manager](monitoring-create-action-group-with-resource-manager-template.md)
## Gerir registos de diagnóstico
### [Arquivo](monitoring-archive-diagnostic-logs.md)
### [Transmitir em Fluxo nos Hubs de Eventos](monitoring-stream-diagnostic-logs-to-event-hubs.md)
### [Ativar as Definições de Diagnóstico com modelos do Resource Manager](monitoring-enable-diagnostic-logs-using-template.md)
## Utilizar a API REST
### [Guia de utilização da API REST](monitoring-rest-api-walkthrough.md)
## Utilizar o Diagnóstico do Azure
### [Enviar para o Application Insights](azure-diagnostics-configure-application-insights.md)
### [Enviar para os Hubs de Eventos](azure-diagnostics-streaming-event-hubs.md)
### [Resolução de problemas](azure-diagnostics-troubleshooting.md)

# Referência
## [Lista de Métricas Suportadas](monitoring-supported-metrics.md)
## [Origens dos dados de monitorização](monitoring-data-sources.md)
## [PowerShell](/powershell/module/azurerm.insights)
## [.NET](https://msdn.microsoft.com/library/azure/dn802153)
## [REST](/rest/api/monitor/)
## [Histórico de versões da extensão do Diagnóstico do Azure](azure-diagnostics-versioning-history.md)
## [Esquema da extensão do Diagnóstico do Azure](azure-diagnostics-schema.md)
### [1.0](azure-diagnostics-schema-1dot0.md)
### [1.2](azure-diagnostics-schema-1dot2.md)
### [1.3 a 1.7](azure-diagnostics-schema-1dot3-and-later.md)

# Recursos
## [Exemplos do PowerShell](insights-powershell-samples.md)
## [Exemplos da CLI 1.0 do Azure](insights-cli-samples.md)
## [Vídeos](https://azure.microsoft.com/resources/videos/index/?services=monitor)