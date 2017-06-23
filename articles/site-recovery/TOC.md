# Descrição geral
## [O que é o Site Recovery?](site-recovery-overview.md)
## Como funciona a Recuperação de Sites?
### [Arquitetura de Azure para o Azure](site-recovery-azure-to-azure-architecture.md)
### [Arquitetura de VMware para o Azure](site-recovery-architecture-vmware-to-azure.md)
### [Arquitetura de Hyper-V para o Azure](site-recovery-architecture-hyper-v-to-azure.md)
### [Arquitetura de replicação para um site secundário](site-recovery-architecture-to-secondary-site.md)
## [Que cargas de trabalho pode proteger?](site-recovery-workload.md)
## Matriz de suporte do Site Recovery
### [Suporte do Azure para o Azure](site-recovery-support-matrix-azure-to-azure.md)
### [Suporte de local para o Azure](site-recovery-support-matrix-to-azure.md)
### [Suporte de local para site secundário](site-recovery-support-matrix-to-sec-site.md)
## [FAQ](site-recovery-faq.md)
## [Veja uma introdução](https://azure.microsoft.com/resources/videos/index/?services=site-recovery)

# Introdução
## [Replicar VMs do Azure (pré-visualização)](site-recovery-azure-to-azure.md)
## [Replicar servidores físicos para o Azure](site-recovery-physical-servers-to-azure.md)
## [Replicar VMs Hyper-V para o Azure (com VMM)](site-recovery-vmm-to-azure.md)
## [Replicar VMs Hyper-V para o Azure](site-recovery-hyper-v-site-to-azure.md)
## [Replicar VMs Hyper-V para um site secundário (com VMM)](site-recovery-vmm-to-vmm.md)
## [Replicar VMs VMware e servidores físicos para um site secundário](site-recovery-vmware-to-vmware.md)
## [Replicar VMs VMware para o Azure numa implementação multi-inquilino (CSP)](site-recovery-multi-tenant-support-vmware-using-csp.md)

# Procedimento
## Planear
### [Pré-requisitos para a replicação do Azure](site-recovery-azure-to-azure-prereq.md)
### Planear a rede
#### [Planear a rede para replicação do Azure para o Azure (pré-visualização)](site-recovery-azure-to-azure-networking-guidance.md)
#### [Planear a rede para replicação de computador no local](site-recovery-network-design.md)
#### [Planear o mapeamento de rede para replicação de VMs do Azure](site-recovery-network-mapping-azure-to-azure.md)
#### [Planear o mapeamento de rede para replicação de VMs de Hyper-V](site-recovery-network-mapping.md)
### Planear a capacidade e a escalabilidade
#### [Planear a capacidade da replicação de VMware para o Azure](site-recovery-plan-capacity-vmware.md)
#### [Deployment Planner para a replicação do VMware para o Azure](site-recovery-deployment-planner.md)
#### [Capacity Planner para a replicação de Hyper-V](site-recovery-capacity-planner.md)
### [Planear o acesso baseado em funções para a replicação de VMs](site-recovery-role-based-linked-access-control.md)
## Implementação
### [Replicar VMs VMware para o Azure](vmware-walkthrough-overview.md)
#### [Passo 1: Rever a arquitetura](vmware-walkthrough-architecture.md)
#### [Passo 2: Verificar os pré-requisitos e as limitações](vmware-walkthrough-prerequisites.md)
#### [Passo 3: Planear a capacidade](vmware-walkthrough-capacity.md)
#### [Passo 4: Planear a rede](vmware-walkthrough-network.md)
#### [Passo 5: Preparar o Azure](vmware-walkthrough-prepare-azure.md)
#### [Passo 6: Preparar o VMware](vmware-walkthrough-prepare-vmware.md)
#### [Passo 7: Criar um cofre](vmware-walkthrough-create-vault.md)
#### [Passo 8: Configurar a origem e o destino](vmware-walkthrough-source-target.md)
#### [Passo 9: Configurar uma política de replicação](vmware-walkthrough-replication.md)
#### [Passo 10: Instalar o Serviço de mobilidade ](vmware-walkthrough-install-mobility.md)
#### [Passo 11: Ativar a replicação](vmware-walkthrough-enable-replication.md)
#### [Passo 12: Executar uma ativação pós-falha de teste](vmware-walkthrough-test-failover.md)
## Configurar
### Configurar o ambiente de origem
#### [Ambiente de origem para VMware para o Azure](site-recovery-set-up-vmware-to-azure.md)
#### [Ambiente de origem para físico para o Azure](site-recovery-set-up-physical-to-azure.md)
### Configurar o ambiente de destino
#### [Ambiente de destino para VMware para o Azure](site-recovery-prepare-target-vmware-to-azure.md)
#### [Ambiente de destino para replicação física para o Azure](site-recovery-prepare-target-physical-to-azure.md)
### [Configurar as definições de replicação](site-recovery-setup-replication-settings-vmware.md)
### [Implementar o serviço de Mobilidade para a replicação de VMware](site-recovery-vmware-to-azure-install-mob-svc.md)
#### [Implementar o serviço de Mobilidade com o System Center Configuration Manager](site-recovery-install-mobility-service-using-sccm.md)
#### [Implementar o serviço de Mobilidade com o Automation DSC do Azure](site-recovery-automate-mobility-service-install.md)
### Ativar a replicação
#### [Ativar a replicação do Azure para o Azure](site-recovery-replicate-azure-to-azure.md)
#### [Ativar a replicação do VMware para o Azure](site-recovery-replicate-vmware-to-azure.md)
## Ativação e reativação pós-falha
### [Configurar planos de recuperação](site-recovery-create-recovery-plans.md)
#### [Adicionar runbooks do Azure aos planos de recuperação](site-recovery-runbook-automation.md)
### Executar uma ativação pós-falha de teste
#### [Executar uma ativação pós-falha de teste no Azure](site-recovery-test-failover-to-azure.md)
#### [Executar uma ativação pós-falha de teste entre duas clouds de VMM](site-recovery-test-failover-vmm-to-vmm.md)
### [Máquinas protegidas por ativação pós-falha](site-recovery-failover.md)
### Voltar a proteger máquinas após a ativação pós-falha
#### [Voltar a proteger de uma região secundária do Azure para uma principal](site-recovery-how-to-reprotect-azure-to-azure.md)
#### [Voltar a proteger do Azure para o local](site-recovery-how-to-reprotect.md)
### Reativação pós-falha a partir do Azure
#### [Reativação pós-falha do Azure para o VMware](site-recovery-failback-azure-to-vmware.md)
#### [Reativação pós-falha do Azure para Hyper-V](site-recovery-failback-from-azure-to-hyper-v.md)
## Migrar
### [Migrar para o Azure](site-recovery-migrate-to-azure.md)
### [Migrar entre regiões do Azure](site-recovery-migrate-azure-to-azure.md)
### [Migrar instâncias do Windows AWS para o Azure](site-recovery-migrate-aws-to-azure.md)
### [Replicar máquinas migradas para outra região do Azure](site-recovery-azure-to-azure-after-migration.md)
## Cargas de trabalho
### [Active Directory e DNS](site-recovery-active-directory.md)
### [Replicação do SQL Server](site-recovery-sql.md)
### [SharePoint](site-recovery-sharepoint.md)
### [Dynamics AX](site-recovery-dynamicsax.md)
### [RDS](site-recovery-workload.md#protect-rds)
### [Exchange](site-recovery-workload.md#protect-exchange)
### [SAP](site-recovery-workload.md#protect-sap)
### [Aplicações Web com base em IIS](site-recovery-iis.md)
### [Citrix XenApp e XenDesktop](site-recovery-citrix-xenapp-and-xendesktop.md)
### [Outras cargas de trabalho](site-recovery-workload.md#workload-summary)
## Automatizar a replicação
### [Automatizar a replicação de Hyper-V para o Azure (sem VMM)](site-recovery-deploy-with-powershell-resource-manager.md)
### [Automatizar a replicação de Hyper-V para o Azure (com VMM)](site-recovery-vmm-to-azure-powershell-resource-manager.md)
### [Automatizar a replicação de Hyper-V para um site secundário (com VMM)](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
## Gerir
### [Gerir servidores de processos no Azure](site-recovery-vmware-setup-azure-ps-resource-manager.md)
### [Gerir o servidor de configuração](site-recovery-vmware-to-azure-manage-configuration-server.md)
### [Gerir servidores de processos aumentados horizontalmente](site-recovery-vmware-to-azure-manage-scaleout-process-server.md)
### [Gerir servidores vCenter](site-recovery-vmware-to-azure-manage-vCenter.md)
### [Remover servidores e desativar proteção](site-recovery-manage-registration-and-protection.md)

## Monitorizar e resolver problemas
### [Problemas da replicação do Azure para o Azure](site-recovery-azure-to-azure-troubleshoot-errors.md)
### [Problemas de replicação de VMs do local para o Azure](site-recovery-vmware-to-azure-protection-troubleshoot.md)
### [Recolher registos e resolver problemas no local](site-recovery-monitoring-and-troubleshooting.md)

# Referência
## [PowerShell](/powershell/module/azurerm.siterecovery)
## [PowerShell clássico](/powershell/module/azure/?view=azuresmps-3.7.0)
## [REST](https://msdn.microsoft.com/en-us/library/mt750497)

# Relacionado
## [Automatização do Azure](/azure/automation/)

# Recursos
## [Percurso de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)
## [Fórum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)
## [Blogue](http://azure.microsoft.com/blog/tag/azure-site-recovery/)
## [Preços](https://azure.microsoft.com/pricing/details/site-recovery/)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=site-recovery)
