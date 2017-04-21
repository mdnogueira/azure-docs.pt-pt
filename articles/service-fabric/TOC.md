
# Descrição geral
## [O que é o Service Fabric?](service-fabric-overview.md)
## [Compreender os microsserviços](service-fabric-overview-microservices.md)
## [Cenários de aplicações](service-fabric-application-scenarios.md)
## [Arquitetura](service-fabric-architecture.md)
## [Terminologia](service-fabric-technical-overview.md)
## [Primer](service-fabric-content-roadmap.md)

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
## [Criar o seu primeiro cluster no Azure](service-fabric-get-started-azure-cluster.md)
## [Criar o seu primeiro cluster autónomo](service-fabric-get-started-standalone-cluster.md)

# Procedimento
## Criar uma aplicação
### [Padrões e cenários](service-fabric-patterns-and-scenarios.md)
### Noções básicas
#### [Modelo de aplicação](service-fabric-application-model.md)
#### [Criar pacote de uma aplicação](service-fabric-package-apps.md)
#### [Modelo de programação suportado](service-fabric-choose-framework.md)
#### [Estado do serviço](service-fabric-concepts-state.md)
#### [Comunicação de serviços](service-fabric-connect-and-communicate-with-services.md)
#### [Adicionar um front-end da Web](service-fabric-add-a-web-frontend.md)
#### [Recursos do manifesto do serviço](service-fabric-service-manifest-resources.md)
#### [Introdução ao plug-in do Eclipse para desenvolvimento Java](service-fabric-get-started-eclipse.md)
#### [Gerir aplicações no Visual Studio](service-fabric-manage-application-in-visual-studio.md)
#### [Configurar ligações seguras no Visual Studio](service-fabric-visualstudio-configure-secure-connections.md)
#### Depurar
##### [Depurar um serviço C# no VS](service-fabric-debugging-your-application.md)
##### [Depurar um serviço Java no Eclipse](service-fabric-debugging-your-application-java.md)
#### Monitorizar e diagnosticar
##### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
##### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
#### [Gerir segredos da aplicação](service-fabric-application-secret-management.md)  
#### [Configurar políticas de segurança para a sua aplicação](service-fabric-application-runas-security.md)  
#### [Configurar a sua aplicação para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md)  
#### [Erros comuns e exceções](service-fabric-errors-and-exceptions.md) 

### Aplicação executável convidada
#### [Implementar um executável convidado](service-fabric-deploy-existing-app.md)
#### [Implementar vários executáveis convidados](service-fabric-deploy-multiple-apps.md)

### Aplicação de contentor
#### [Descrição geral](service-fabric-containers-overview.md)
#### [Implementar o contentor do Windows](service-fabric-deploy-container.md)
#### [Implementar o contentor do Docker](service-fabric-deploy-container-linux.md)

### Aplicação Reliable Services
#### [Descrição geral](service-fabric-reliable-services-introduction.md)
#### Introdução
##### [C# no Windows](service-fabric-reliable-services-quick-start.md)
##### [Java em Linux](service-fabric-reliable-services-quick-start-java.md)
#### [Ciclo de vida do Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)
#### [Ciclo de vida do Reliable Services - Java](service-fabric-reliable-services-lifecycle-java.md)
#### [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)
#### [Utilizar a Reliable Collections](service-fabric-work-with-reliable-collections.md)
#### [Configurar](service-fabric-reliable-services-configuration.md)
#### [Notificações](service-fabric-reliable-services-notifications.md)
#### [Backup e restauro](service-fabric-reliable-services-backup-restore.md)
#### [Comunicar com o Reliable Services](service-fabric-reliable-services-communication.md)
#### [Proteger as comunicações com o Reliable Services - C#](service-fabric-reliable-services-secure-communication.md)
#### [Proteger as comunicações com o Reliable Services - Java](service-fabric-reliable-services-secure-communication-java.md)
##### [Núcleo do ASP.NET](service-fabric-reliable-services-communication-aspnetcore.md)
##### [Comunicação Remota do Serviço - C#](service-fabric-reliable-services-communication-remoting.md)
##### [Comunicação Remota do Serviço - Java](service-fabric-reliable-services-communication-remoting-java.md)
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
#### [Eventos](service-fabric-reliable-actors-events.md) 
#### [Temporizadores e lembretes](service-fabric-reliable-actors-timers-reminders.md)
#### [Gestão de estados](service-fabric-reliable-actors-state-management.md)
#### [Configurar KvsActorStateProvider](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [Serialização do tipo](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)
#### [Configurar dedinições de comunicações](service-fabric-reliable-actors-fabrictransportsettings.md) 
#### [Configurar ReliableDictionaryActorStateProvider](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

## Migrar a partir dos Serviços Cloud
### [Comparar os Serviços Cloud com o Service Fabric](service-fabric-cloud-services-migration-differences.md)
### [Migrar para o Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
### [Práticas recomendadas](/azure/architecture/service-fabric/migrate-from-cloud-services)

## Criar e gerir clusters

### Noções básicas
#### [Descrição geral](service-fabric-deploy-anywhere.md)
#### [Descrever um cluster](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Planeamento de capacidade](service-fabric-cluster-capacity.md)
#### [Visualizar um cluster](service-fabric-visualizing-your-cluster.md)
#### [Ligar a um cluster seguro](service-fabric-connect-to-secure-cluster.md)
#### [Gerir um cluster com o CLI do Azure](service-fabric-azure-cli.md) 
#### [Proteger um cluster](service-fabric-cluster-security.md)
#### [Recuperação após desastre](service-fabric-disaster-recovery.md)
#### [Diferenças de funcionalidades entre o Linux e o Windows](service-fabric-linux-windows-differences.md)

### Clusters no Azure
#### Criar um cluster no Azure
##### [Portal do Azure](service-fabric-cluster-creation-via-portal.md)
##### [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
##### [Visual Studio e Azure Resource Manager](service-fabric-cluster-creation-via-visual-studio.md)
#### [Padrões de redes de cluster](service-fabric-patterns-networking.md)
#### [Tipos de nós e Conjuntos de Dimensionamento de VMs](service-fabric-cluster-nodetypes.md)
#### [Dimensionar um cluster](service-fabric-cluster-scale-up-down.md)
#### [Dimensionar um cluster através de programação](service-fabric-cluster-programmatic-scaling.md)
#### [Atualizar um cluster](service-fabric-cluster-upgrade.md)
#### [Eliminar um cluster](service-fabric-cluster-delete.md)
#### [Controlo de acesso](service-fabric-cluster-security-roles.md)
#### [Configurar um cluster](service-fabric-cluster-fabric-settings.md)
#### [Adicionar ou fazer rollover de certificados de cluster](service-fabric-cluster-security-update-certs-azure.md) 

### Clusters autónomos
#### [Planear e preparar para a implementação](service-fabric-cluster-standalone-deployment-preparation.md)
#### [Conteúdos do pacote autónomo do Service Fabric](service-fabric-cluster-standalone-package-contents.md)
#### [Criar um cluster autónomo](service-fabric-cluster-creation-for-windows-server.md)
#### [Criar um cluster autónomo nas Máquinas Virtuais do Azure](service-fabric-cluster-creation-with-windows-azure-vms.md)
#### [Dimensionar um cluster](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [Atualizar um cluster](service-fabric-cluster-upgrade-windows-server.md)
#### [Controlo de acesso](service-fabric-cluster-security-roles.md)
#### [Configurar um cluster](service-fabric-cluster-manifest.md)
#### [Proteger um cluster com certificados](service-fabric-windows-cluster-x509-security.md)  
#### [Proteger um cluster com a segurança do Windows](service-fabric-windows-cluster-windows-security.md) 

## Gerir o ciclo de vida da aplicação
### [Descrição geral](service-fabric-application-lifecycle.md)
### [Configurar a integração contínua com o VSTS](service-fabric-set-up-continuous-integration.md)
### [Implementar a aplicação Java para Linux com o Jenkins](service-fabric-cicd-your-linux-java-application-with-jenkins.md)
### [Compreender a definição ImageStoreConnectionString](service-fabric-image-store-connection-string.md)
### Implementar ou remover aplicações
#### [PowerShell](service-fabric-deploy-remove-applications.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
#### [APIs FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)
### [Descrição geral da atualização da aplicação](service-fabric-application-upgrade.md)
### [Configurar a atualização da aplicação](service-fabric-visualstudio-configure-upgrade.md)
### [Parâmetros da atualização da aplicação](service-fabric-application-upgrade-parameters.md)
### Atualizar uma aplicação
#### [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
#### [Visual Studio](service-fabric-application-upgrade-tutorial.md)
### [Resolver problemas da atualização da aplicação](service-fabric-application-upgrade-troubleshooting.md)
### [Serialização de dados em atualizações da aplicação](service-fabric-application-upgrade-data-serialization.md)
### [Tópicos avançados das atualizações da aplicação](service-fabric-application-upgrade-advanced.md)

## Inspecionar estado de funcionamento da aplicação e do cluster
### [Monitorizar o estado de funcionamento do Service Fabric](service-fabric-health-introduction.md)
### [Comunicar e verificar o estado de funcionamento dos serviços](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
### [Adicionar relatórios de estado de funcionamento personalizados](service-fabric-report-health.md)
### [Resolver problemas com relatórios de estado de funcionamento do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
### [Ver relatórios de estado de funcionamento](service-fabric-view-entities-aggregated-health.md)

## Monitorizar e diagnosticar
### [Monitorizar e diagnosticar aplicações](service-fabric-diagnostics-overview.md)
### Monitorizar e diagnosticar os serviços localmente
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
### Registos do Diagnóstico do Azure
#### [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
#### [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
### [Recolher registos a partir de um processo de serviço](service-fabric-diagnostic-collect-logs-without-an-agent.md)
### [Diagnósticos no Reliable Services com monitorização de estado](service-fabric-reliable-services-diagnostics.md)
### [Diagnósticos no Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
### [Resolver problemas do seu cluster local](service-fabric-troubleshoot-local-cluster-setup.md)

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
#### [Utilizar Cenários de teste](service-fabric-testability-scenarios.md)
#### [Utilizar as APIs de transição de nó](service-fabric-node-transition-apis.md)
### [Teste de carga da sua aplicação](service-fabric-vso-load-test.md)

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

# Referência
## [PowerShell](/powershell/module/ServiceFabric/)
## [API de Java](/java/api/)
## [.NET](/dotnet/api/)
## [REST](/rest/api/servicefabric)

# Recursos
## [Perguntas frequentes sobre o Service Fabric](service-fabric-common-questions.md)
## [Opções de suporte do Service Fabric](service-fabric-support.md)
## [Código de exemplo](http://aka.ms/servicefabricsamples)
## [Percurso de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [Preços](https://azure.microsoft.com/pricing/details/service-fabric/)
## [Atualizações de Serviço](https://azure.microsoft.com/updates/?product=service-fabric)
## [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)
