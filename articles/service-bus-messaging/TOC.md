# Descrição geral
## [O que são as Mensagens do Service Bus?](service-bus-messaging-overview.md)
## [Noções básicas sobre o Service Bus](service-bus-fundamentals-hybrid-solutions.md)
## [Arquitetura do Service Bus](service-bus-architecture.md)
## [FAQ](service-bus-faq.md)

# Introdução
## [Criar um espaço de nomes](service-bus-create-namespace-portal.md)
### [Filas, tópicos e subscrições](service-bus-queues-topics-subscriptions.md)
## Utilizar as filas
### [.NET](service-bus-dotnet-get-started-with-queues.md)
### [Java](service-bus-java-how-to-use-queues.md)
### [Node.js](service-bus-nodejs-how-to-use-queues.md)
### [PHP](service-bus-php-how-to-use-queues.md)
### [python](service-bus-python-how-to-use-queues.md)
### [Ruby](service-bus-ruby-how-to-use-queues.md)
### [REST](/rest/api/servicebus/queues)
## Utilizar tópicos e subscrições
### [.NET](service-bus-dotnet-how-to-use-topics-subscriptions.md)
### [Java](service-bus-java-how-to-use-topics-subscriptions.md)
### [Node.js](service-bus-nodejs-how-to-use-topics-subscriptions.md)
### [PHP](service-bus-php-how-to-use-topics-subscriptions.md)
### [python](service-bus-python-how-to-use-topics-subscriptions.md)
### [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md)
## [Criar uma aplicação do Service Bus de multicamadas](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)

# Procedimento
## Planear e conceber
### [Mensagens premium](service-bus-premium-messaging.md)
### [Comparar Filas do Azure e filas do Service Bus](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
### [Otimizar o desempenho](service-bus-performance-improvements.md)
### [Recuperação após desastre a nível geográfico e georreplicação](service-bus-geo-dr.md)
### [Mensagens assíncronas e elevada disponibilidade](service-bus-async-messaging.md)
### [Lidar com interrupções e desastres](service-bus-outages-disasters.md)

## Programar
### Processamento de mensagens
#### [Mensagens, payloads e serialização](service-bus-messages-payloads.md)
#### [Transferências de mensagens, bloqueios e acordo](message-transfers-locks-settlement.md)
#### [Sequência de mensagens e carimbos de data/hora](message-sequencing.md)
#### [Expiração de mensagem (TTL)](message-expiration.md)
### [Autenticação e autorização](service-bus-authentication-and-authorization.md)
#### [Migrar do ACS para SAS](service-bus-migrate-acs-sas.md)
#### [Autenticação com Assinaturas de Acesso Partilhado](service-bus-sas.md)
### [Filtros de tópico e ações](topic-filters.md)
### [Filas e tópicos particionados](service-bus-partitioning.md)
### [Sessões de mensagens](message-sessions.md)
### AMQP
#### [Descrição geral do AMQP](service-bus-amqp-overview.md)
#### [.NET](service-bus-amqp-dotnet.md)
#### [Java](service-bus-amqp-java.md)
#### [Java Message Service e AMQP](service-bus-java-how-to-use-jms-api-amqp.md)
#### [Guia do protocolo AMQP](service-bus-amqp-protocol-guide.md)
#### [Operações de resposta baseada em pedidos AMQP](service-bus-amqp-request-response.md)
### Funcionalidades avançadas
#### [Filas de mensagens não entregues](service-bus-dead-letter-queues.md)
#### [Obtenção prévia de mensagens](service-bus-prefetch.md)
#### [Deteção de mensagens duplicadas](duplicate-detection.md)
#### [Contadores de mensagens](message-counters.md)
#### [Diferimento de mensagens](message-deferral.md)
#### [navegação de mensagens](message-browsing.md)
#### [Encadear entidades com o reencaminhamento automático](service-bus-auto-forwarding.md)
#### [Processamento de transações](service-bus-transactions.md)
#### [Implementação do espaço de nomes emparelhado](service-bus-paired-namespaces.md)
## Gerir
### [Monitorizar o Service Bus com a Monitorização do Azure (Monitor Service Bus with Azure Monitoring)](service-bus-metrics-azure-monitor.md)
### [Bibliotecas de gestão do Service Bus](service-bus-management-libraries.md)
### [Registos de diagnóstico](service-bus-diagnostic-logs.md)
### [Suspender e reativar as entidades de mensagens](entity-suspend.md)
### [Utilizar modelos do Azure Resource Manager](service-bus-resource-manager-overview.md)
#### [Criar um espaço de nomes](service-bus-resource-manager-namespace.md)
#### [Criar um espaço de nomes e uma fila](service-bus-resource-manager-namespace-queue.md)
#### [Criar um espaço de nomes com o tópico e uma subscrição](service-bus-resource-manager-namespace-topic.md)
#### [Criar uma regra de autorização para o espaço de nomes e para a fila](service-bus-resource-manager-namespace-auth-rule.md)
#### [Criar um espaço de nomes com o tópico, uma subscrição e regra](service-bus-resource-manager-namespace-topic-with-rule.md)
#### 
### [Utilizar o Azure PowerShell para aprovisionar entidades](service-bus-manage-with-ps.md)

# Referência
## .NET
### [Microsoft.ServiceBus.Messaging (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging)
### [Microsoft.Azure.ServiceBus (.NET Standard)](/dotnet/api/microsoft.azure.servicebus)
## [Java](/java/api/overview/azure/servicebus)
## [Azure PowerShell](/powershell/module/azurerm.servicebus)
## [REST](/rest/api/servicebus)
## [Exceções](service-bus-messaging-exceptions.md)
## [Quotas](service-bus-quotas.md)
## [Sintaxe de SQLFilter](service-bus-messaging-sql-filter.md)
## [Sintaxe de SQLRuleAction](service-bus-messaging-sql-rule-action.md)

# Recursos
## [Mapa do Azure](https://azure.microsoft.com/roadmap/?category=enterprise-integration)
## [Blogue](https://blogs.msdn.microsoft.com/servicebus/)
## [Fóruns do MSDN](https://social.msdn.microsoft.com/forums/home?forum=servbus)
## [Preços](https://azure.microsoft.com/pricing/details/service-bus/)
## [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/)
## [Detalhes dos preços](service-bus-pricing-billing.md)
## [Amostras](service-bus-samples.md)
## [ServiceBus360](https://www.servicebus360.com/)
## [Explorador do Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=service-bus)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azureservicebus)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=service-bus)


