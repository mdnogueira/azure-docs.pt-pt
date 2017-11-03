# Arquitetura

## Noções Básicas da Nuvem

### [Conceber aplicações resilientes](guidance-resiliency-overview.md)
#### [Lista de verificação de resiliência](guidance-resiliency-checklist.md)
#### [Análise do modo de falhas](guidance-resiliency-failure-mode-analysis.md)
#### [Recuperação após desastre](..\resiliency\resiliency-disaster-recovery-azure-applications.md)
#### [Recuperação após desastre e elevada disponibilidade](..\resiliency\resiliency-disaster-recovery-high-availability-azure-applications.md)
#### [Elevada disponibilidade](..\resiliency\resiliency-high-availability-azure-applications.md)
#### [Lista de verificação de elevada disponibilidade](..\resiliency\resiliency-high-availability-checklist.md)
#### [Documentação de orientação de resiliência de serviços específicos do Azure](..\resiliency\resiliency-service-guidance-index.md)
#### [Recuperação após danos em dados ou eliminação acidental](..\resiliency\resiliency-technical-guidance-recovery-data-corruption.md)
#### [Recuperação de falhas de locais](..\resiliency\resiliency-technical-guidance-recovery-local-failures.md)
#### [Recuperação de uma interrupção de serviço ao nível da região](..\resiliency\resiliency-technical-guidance-recovery-loss-azure-region.md)
#### [Recuperação do local para o Azure](..\resiliency\resiliency-technical-guidance-recovery-on-premises-azure.md)
#### [Orientações técnicas sobre resiliência do Azure](..\resiliency\resiliency-technical-guidance.md)


## Arquiteturas de Referência

### [Executar cargas de trabalho de VMs no Azure](guidance-ra-compute.md)
#### [Executar uma VM do Linux no Azure](guidance-compute-single-vm-linux.md)
#### [Executar uma VM do Windows no Azure](guidance-compute-single-vm.md)
#### [Executar várias VMs no Azure para escalabilidade e disponibilidade](guidance-compute-multi-vm.md)
#### [Executar VMs do Linux para uma arquitetura de N camadas](guidance-compute-n-tier-vm-linux.md)
#### [Executar VMs do Windows para uma arquitetura de N camadas](guidance-compute-n-tier-vm.md)
#### [Executar VMs do Linux em várias regiões para elevada disponibilidade](guidance-compute-multiple-datacenters-linux.md)
#### [Executar VMs do Windows em várias regiões para elevada disponibilidade](guidance-compute-multiple-datacenters.md)

### [Ligar a sua rede no local ao Azure](guidance-ra-hybrid-networking.md)
#### [Arquitetura de rede híbrida com o Azure ExpressRoute](guidance-hybrid-network-expressroute.md)
#### [Arquitetura de rede híbrida com o Azure e a VPN no Local](guidance-hybrid-network-vpn.md)
#### [Arquitetura de rede híbrida de elevada disponibilidade](guidance-hybrid-network-expressroute-vpn-failover.md)

### [Proteger o Limite da Cloud no Azure](guidance-ra-network-security.md)
#### [Arquitetura de rede híbrida segura no Azure](guidance-iaas-ra-secure-vnet-hybrid.md)
#### [DMZ entre o Azure e a Internet](guidance-iaas-ra-secure-vnet-dmz.md)

### [Gerir identidades no Azure](guidance-ra-identity.md)
#### [Implementar o Azure Active Directory](guidance-identity-aad.md)
#### [Expandir o ADDS para o Azure](guidance-identity-adds-extend-domain.md)
#### [Criar uma floresta de recursos do ADDS no Azure](guidance-identity-adds-resource-forest.md)
#### [Implementar o ADFS no Azure](guidance-identity-adfs.md)

### [Arquiteturas de aplicações Web para o Serviço de Aplicações do Azure](guidance-ra-app-service.md)
#### [Aplicação Web básica](guidance-web-apps-basic.md)
#### [Aplicação Web com elevada disponibilidade](guidance-web-apps-multi-region.md)
#### [Melhorar a escalabilidade em aplicações Web](guidance-web-apps-scalability.md)


## Melhores práticas para Aplicações na Nuvem

### [Documentação de orientação de design da API](..\best-practices-api-design.md)
### [Documentação de orientação de implementação da API](..\best-practices-api-implementation.md)
### [Documentação de orientação sobre dimensionamento automático](..\best-practices-auto-scaling.md)
### [Lista de verificação de disponibilidade](..\best-practices-availability-checklist.md)
### [Documentação de orientação sobre tarefas em segundo plano](..\best-practices-background-jobs.md)
### [Pares de regiões do Azure](..\best-practices-availability-paired-regions.md)
### [Documentação de orientação sobre a colocação em cache](..\best-practices-caching.md)
### [Orientações sobre a Rede de Entrega de Conteúdos](..\best-practices-cdn.md)
### [Documentação de orientação para a criação de partições de dados](..\best-practices-data-partitioning.md)
### [Documentação de orientação sobre monitorização e diagnósticos](..\best-practices-monitoring.md)
### [Serviços cloud da Microsoft e segurança de rede](..\best-practices-network-security.md)
### [Padrões para conceber modelos do Azure Resource Manager](..\best-practices-resource-manager-design-templates.md)
### [Convenções de nomenclatura recomendadas para recursos do Azure](guidance-naming-conventions.md)
### [Estado de partilha nos modelos do Azure Resource Manager](..\best-practices-resource-manager-state.md)
### [Documentação de orientação geral para repetição](..\best-practices-retry-general.md)
### [Documentação de orientação específica para repetição do serviço](..\best-practices-retry-service-specific.md)
### [Lista de verificação de escalabilidade](..\best-practices-scalability-checklist.md)


## Guias do Cenário

### [Elasticsearch na Documentação de Orientação do Azure](guidance-elasticsearch.md)
#### [Executar Elasticsearch no Azure](guidance-elasticsearch-running-on-azure.md)
#### [Otimizar o desempenho da ingestão de dados](guidance-elasticsearch-tuning-data-ingestion-performance.md)
#### [Otimizar o desempenho de consulta e de agregação de dados](guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md)
#### [Configurar a resiliência e a recuperação](guidance-elasticsearch-configuring-resilience-and-recovery.md)
#### [Criar um ambiente de teste de desempenho](guidance-elasticsearch-creating-performance-testing-environment.md)
#### [Implementar um plano de teste JMeter](guidance-elasticsearch-implementing-jmeter-test-plan.md)
#### [Implementar um sampler JUnit em JMeter](guidance-elasticsearch-deploying-jmeter-junit-sampler.md)
#### [Executar os testes de resiliência automatizados](guidance-elasticsearch-running-automated-resilience-tests.md)
#### [Executar os testes de desempenho automatizados](guidance-elasticsearch-running-automated-performance-tests.md)

### [Gestão de identidades para aplicações multi-inquilino](guidance-multitenant-identity.md)
#### [Descrição geral](guidance-multitenant-identity-intro.md)
#### [Aplicação Tailspin Surveys](guidance-multitenant-identity-tailspin.md)
#### [Autenticação com o Azure AD e o OpenID Connect](guidance-multitenant-identity-authenticate.md)
#### [Identidades baseadas em afirmações](guidance-multitenant-identity-claims.md)
#### [Inscrição e integração de inquilinos](guidance-multitenant-identity-signup.md)
#### [Funções de aplicação](guidance-multitenant-identity-app-roles.md)
#### [Autorização baseada em funções e em recursos](guidance-multitenant-identity-authorize.md)
#### [Proteger uma API Web de back-end](guidance-multitenant-identity-web-api.md)
#### [Tokens de acesso da cache](guidance-multitenant-identity-token-cache.md)
#### [Federação com o AD FS de um cliente](guidance-multitenant-identity-adfs.md)
#### [Utilizar a asserção do cliente para obter os tokens de acesso](guidance-multitenant-identity-client-assertion.md)
#### [Utilizar o Cofre de Chaves do Azure para proteger segredos da aplicação](guidance-multitenant-identity-keyvault.md)
#### [Implementar aparelhos virtuais de elevada disponibilidade](guidance-nva-ha.md)
# Recursos
## [Mapa do Azure](https://azure.microsoft.com/roadmap/)
## [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/)
