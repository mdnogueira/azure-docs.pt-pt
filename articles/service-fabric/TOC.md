# Descrição geral
## [O que é o Service Fabric?](service-fabric-overview.md)
## [Compreender os microsserviços](service-fabric-overview-microservices.md)
## [Cenários de aplicações](service-fabric-application-scenarios.md)
## [Arquitetura](service-fabric-architecture.md)
## [Terminologia](service-fabric-technical-overview.md)

# Introdução
## Configurar o ambiente de desenvolvimento
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)
## Criar a sua primeira aplicação
### [C# no Windows](service-fabric-create-your-first-application-in-visual-studio.md)
### [Java em Linux](service-fabric-create-your-first-linux-application-with-java.md)
### [C# em Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
## [Implementar aplicações num cluster local](service-fabric-get-started-with-a-local-cluster.md)

# Procedimento
## Criar uma aplicação
### Noções básicas
#### [Modelo de programação](service-fabric-choose-framework.md)
#### [Modelo de aplicação](service-fabric-application-model.md)
#### [Comunicação de serviços](service-fabric-connect-and-communicate-with-services.md)
#### [Ferramentas](service-fabric-manage-application-in-visual-studio.md)
#### [Depurar](service-fabric-debugging-your-application.md)
#### Monitorizar e diagnosticar
##### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
##### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
#### [Configurar políticas de segurança para a sua aplicação](service-fabric-application-runas-security.md)
#### [Configurar a sua aplicação para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md)

### Aplicação Reliable Services
#### [Descrição geral](service-fabric-reliable-services-introduction.md)
#### Introdução
##### [C# no Windows](service-fabric-reliable-services-quick-start.md)
##### [Java em Linux](service-fabric-reliable-services-quick-start-java.md)
#### [Arquitetura](service-fabric-reliable-services-platform-architecture.md)
#### [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)
#### [Utilizar a Reliable Collections](service-fabric-work-with-reliable-collections.md)
#### [Configurar](service-fabric-reliable-services-configuration.md)
#### [Notificações](service-fabric-reliable-services-notifications.md)
#### [Backup e restauro](service-fabric-reliable-services-backup-restore.md)
#### [Comunicar com o Reliable Services](service-fabric-reliable-services-communication.md)
##### [ASP.NET](service-fabric-reliable-services-communication-webapi.md)
##### [Comunicação remota do serviço](service-fabric-reliable-services-communication-remoting.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [Proxy inverso](service-fabric-reverseproxy.md)
#### [Utilização avançada](service-fabric-reliable-services-advanced-usage.md)

### Aplicação do Reliable Actors
#### [Descrição geral](service-fabric-reliable-actors-introduction.md)
#### Introdução
##### [C# no Windows](service-fabric-reliable-actors-get-started.md)
##### [Java em Linux](service-fabric-reliable-actors-get-started-java.md)
#### [Arquitetura](service-fabric-reliable-actors-platform.md)
#### [Ciclo de vida e libertação da memória](service-fabric-reliable-actors-lifecycle.md)
#### [Polimorfismo](service-fabric-reliable-actors-polymorphism.md)
#### [Reentrada](service-fabric-reliable-actors-reentrancy.md)
#### [Temporizadores e lembretes](service-fabric-reliable-actors-timers-reminders.md)
#### [Eventos](service-fabric-reliable-actors-events.md)
#### [Gestão de estados](service-fabric-reliable-actors-state-management.md)
#### [Configurar fornecedor de estado](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [Serialização do tipo](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

### Aplicação executável convidada
#### [Implementar um executável convidado](service-fabric-deploy-existing-app.md)
#### [Implementar vários executáveis convidados](service-fabric-deploy-multiple-apps.md)

### Aplicação de contentor
#### [Descrição geral](service-fabric-containers-overview.md)
#### [Implementar o contentor do Windows](service-fabric-deploy-container.md)
#### [Implementar o contentor do Docker](service-fabric-deploy-container-linux.md)

## Migrar a partir dos Serviços Cloud
### [Comparar os Serviços Cloud com o Service Fabric](service-fabric-cloud-services-migration-differences.md)
### [Migrar para o Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

## Criar e gerir clusters

### Noções básicas
#### [Descrição geral](service-fabric-deploy-anywhere.md)
#### [Descrever um cluster](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Planeamento de capacidade](service-fabric-cluster-capacity.md)
#### [Visualizar um cluster](service-fabric-visualizing-your-cluster.md)
#### [Ligar a um cluster seguro](service-fabric-connect-to-secure-cluster.md)
#### [Segurança](service-fabric-cluster-security.md)
#### [Recuperação após desastre](service-fabric-disaster-recovery.md)

### Clusters no Azure
#### Criar um cluster no Azure
##### [Portal do Azure](service-fabric-cluster-creation-via-portal.md)
##### [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
#### [Tipos de nós e Conjuntos de Dimensionamento de VMs](service-fabric-cluster-nodetypes.md)
#### [Dimensionar um cluster](service-fabric-cluster-scale-up-down.md)
#### [Atualizar um cluster](service-fabric-cluster-upgrade.md)
#### [Eliminar um cluster](service-fabric-cluster-delete.md)
#### [Controlo de acesso](service-fabric-cluster-security-roles.md)
#### [Configurar um cluster](service-fabric-cluster-fabric-settings.md)
#### [Experimentar gratuitamente um cluster de grupo](http://aka.ms/tryservicefabric)

### Clusters autónomos
#### [Criar um cluster autónomo](service-fabric-cluster-creation-for-windows-server.md)
#### [Dimensionar um cluster](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [Atualizar um cluster](service-fabric-cluster-upgrade-windows-server.md)
#### [Proteger um cluster](service-fabric-windows-cluster-x509-security.md)
#### [Controlo de acesso](service-fabric-cluster-security-roles.md)
#### [Configurar um cluster](service-fabric-cluster-manifest.md)

## Gerir e organizar recursos do cluster
### [Descrição geral do Resource Manager do Cluster](service-fabric-cluster-resource-manager-introduction.md)
### [Arquitetura do Resource Manager do Cluster](service-fabric-cluster-resource-manager-architecture.md)
### [Descrever um cluster](service-fabric-cluster-resource-manager-cluster-description.md)
### [Descrição geral dos grupos de aplicações](service-fabric-cluster-resource-manager-application-groups.md)
### [Configurar as definições do Resource Manager do Cluster](service-fabric-cluster-resource-manager-configure-services.md)
### [Métricas de consumo de recursos](service-fabric-cluster-resource-manager-metrics.md)
### [Utilizar as afinidades de serviço](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
### [Políticas de posicionamento de serviços](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
### [Gerir um cluster](service-fabric-cluster-resource-manager-management-integration.md)
### [Desfragmentação do cluster](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
### [Balancear um cluster](service-fabric-cluster-resource-manager-balancing.md)
### [Limitação](service-fabric-cluster-resource-manager-advanced-throttling.md)
### [Movimento de serviço](service-fabric-cluster-resource-manager-movement-cost.md)

## Gerir o ciclo de vida da aplicação
### [Descrição geral](service-fabric-application-lifecycle.md)
### [Configurar a integração contínua](service-fabric-set-up-continuous-integration.md)
### Implementar ou remover aplicações
#### [PowerShell](service-fabric-deploy-remove-applications.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
### [Descrição geral da atualização da aplicação](service-fabric-application-upgrade.md)
### [Configurar a atualização da aplicação](service-fabric-visualstudio-configure-upgrade.md)
### [Parâmetros da atualização da aplicação](service-fabric-application-upgrade-parameters.md)
### Atualizar uma aplicação
#### [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
#### [Visual Studio](service-fabric-application-upgrade-tutorial.md)
### [Resolver problemas da atualização da aplicação](service-fabric-application-upgrade-troubleshooting.md)
### [Serialização de dados em atualizações da aplicação](service-fabric-application-upgrade-data-serialization.md)
### [Tópicos avançados das atualizações da aplicação](service-fabric-application-upgrade-advanced.md)
### [Exemplo de ciclo de vida da aplicação baseada em REST](service-fabric-rest-based-application-lifecycle-sample.md)

## Inspecionar estado de funcionamento da aplicação e do cluster
### [Monitorizar o estado de funcionamento do Service Fabric](service-fabric-health-introduction.md)
### [Comunicar e verificar o estado de funcionamento dos serviços](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
### [Adicionar relatórios de estado de funcionamento personalizados](service-fabric-report-health.md)
### [Resolver problemas com relatórios de estado de funcionamento do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
### [Ver relatórios de estado de funcionamento](service-fabric-view-entities-aggregated-health.md)

## Monitorizar e diagnosticar
### Monitorizar e diagnosticar os serviços localmente
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
### Registos do Diagnóstico do Azure
#### [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
#### [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
### [Rastreio de aplicação do Service Fabric](service-fabric-diagnostic-how-to-use-elasticsearch.md)
### [Diagnósticos no Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
### [Diagnósticos no Reliable Services com monitorização de estado](service-fabric-reliable-services-diagnostics.md)
### [Resolver problemas do seu cluster local](service-fabric-troubleshoot-local-cluster-setup.md)
### [Resolver problemas comuns](service-fabric-diagnostics-troubleshoot-common-scenarios.md)

## Dimensionar aplicações
### [Particionar o Reliable Services](service-fabric-concepts-partitioning.md)
### [Disponibilidade dos serviços](service-fabric-availability-services.md)
### [Dimensionar aplicações](service-fabric-concepts-scalability.md)
### [Planear a capacidade das aplicações](service-fabric-capacity-planning.md)

## Testar aplicações e serviços
### [Descrição geral da Análise de Falhas](service-fabric-testability-overview.md)
### [Testar a comunicação entre serviços](service-fabric-testability-scenarios-service-communication.md)
### Simular falhas
#### [Utilizar Chaos controlado](service-fabric-controlled-chaos.md)
#### [Utilizar Ações de teste](service-fabric-testability-actions.md)
#### [Durante cargas de trabalho](service-fabric-testability-workload-tests.md)
#### [Ao invocar a perda de dados](service-fabric-use-data-loss-api.md)
#### [Utilizar Cenários de teste](service-fabric-testability-scenarios.md)
### [Teste de carga da sua aplicação](service-fabric-vso-load-test.md)

# Referência
## [PowerShell](//powershell/servicefabric/vlatest/servicefabric)
## [API de Java](/java/api/microsoft.servicefabric.services)
## [.NET](/dotnet/api/microsoft.servicefabric.services)
## [REST](/rest/api/servicefabric)

# Recursos
## [Código de exemplo](http://aka.ms/servicefabricsamples)
## [Percurso de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [Preços](https://azure.microsoft.com/pricing/details/service-fabric/)
## [Atualizações de Serviço](https://azure.microsoft.com/updates/?product=service-fabric)
## [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)


<!--HONumber=Dec16_HO1-->


