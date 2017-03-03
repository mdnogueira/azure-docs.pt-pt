# Descrição geral
## [O que é o Site Recovery?](site-recovery-overview.md)
## [Como funciona o Site Recovery?](site-recovery-components.md)
## [Que cargas de trabalho pode proteger?](site-recovery-workload.md)
## [Matriz de suporte do Site Recovery](site-recovery-support-matrix-to-azure.md)
## [FAQ](site-recovery-faq.md)
## [Veja uma introdução](https://www.youtube.com/watch?v=eOOwMQPBKfM)

# Introdução
## [Replicar VMs VMware para o Azure](site-recovery-vmware-to-azure.md)
## [Replicar VMs VMware para o Azure numa implementação multi-inquilino (CSP)](site-recovery-multi-tenant-support-vmware-using-csp.md)
## [Replicar VMs Hyper-V para o Azure (com VMM)](site-recovery-vmm-to-azure.md)
## [Replicar VMs Hyper-V para o Azure](site-recovery-hyper-v-site-to-azure.md)
## [Replicar VMs VMware e servidores físicos para um site secundário](site-recovery-vmware-to-vmware.md)
## [Replicar VMs Hyper-V para um site secundário (com VMM)](site-recovery-vmm-to-vmm.md)

# Procedimento
## Planear
### [Pré-requisitos da implementação](site-recovery-prereq.md)
### [Considerações de infraestrutura de rede](site-recovery-network-design.md)
### [Utilizar o Site Recovery Capacity Planner](site-recovery-capacity-planner.md)
### [Planear a capacidade e dimensionar a replicação de VMware para o Azure](site-recovery-plan-capacity-vmware.md)
### [Executar o Planeador de Implementação para a replicação do VMware para o Azure](site-recovery-deployment-planner.md)
## Configurar
### [Configurar o ambiente de origem](site-recovery-set-up-vmware-to-azure.md)
### [Configurar o ambiente de destino](site-recovery-prepare-target-vmware-to-azure.md)
### [Configurar as definições de replicação](site-recovery-setup-replication-settings-vmware.md)
### [Implementar o serviço de Mobilidade para a replicação de VMware](site-recovery-vmware-to-azure-install-mob-svc.md)
#### [Implementar o serviço de Mobilidade com o System Center Configuration Manager](site-recovery-install-mobility-service-using-sccm.md)
#### [Implementar o serviço de Mobilidade com o Automation DSC do Azure](site-recovery-automate-mobility-service-install.md)
## Ativação pós-falha e reativação pós-falha
### [Ativação pós-falha no Site Recovery](site-recovery-failover.md)
### [Configurar planos de recuperação](site-recovery-create-recovery-plans.md)
#### [Adicionar runbooks do Azure aos planos de recuperação](site-recovery-runbook-automation.md)
### [Executar uma ativação pós-falha de teste no Azure](site-recovery-test-failover-to-azure.md)
### [Executar uma ativação pós-falha de teste entre dois sites VMM](site-recovery-test-failover-vmm-to-vmm.md)
### [Reativação pós-falha de VMs do VMware/servidores físicos do Azure](site-recovery-how-to-failback-azure-to-vmware.md)
### [Reativação pós-falha de VMs do Hyper-V a partir do Azure](site-recovery-failback-from-azure-to-hyper-v.md)

## Migrar
### [Migrar para o Azure](site-recovery-migrate-to-azure.md)
### [Migrar entre regiões do Azure](site-recovery-migrate-azure-to-azure.md)
### [Migrar instâncias do Windows AWS para o Azure](site-recovery-migrate-aws-to-azure.md)
## Cargas de trabalho
### [Active Directory e DNS](site-recovery-active-directory.md)
### [SQL Server](site-recovery-sql.md)
### [SharePoint](site-recovery-workload.md#protect-sharepoint)
### [Dynamics AX](site-recovery-workload.md#protect-dynamics-ax)
### [RDS](site-recovery-workload.md#protect-rds)
### [Exchange](site-recovery-workload.md#protect-exchange)
### [SAP](site-recovery-workload.md#protect-sap)
### [Outras cargas de trabalho](site-recovery-workload.md#workload-summary)
## Automatizar a replicação
### [Automatizar a replicação de Hyper-V para o Azure (sem VMM)](site-recovery-deploy-with-powershell-resource-manager.md)
### [Automatizar a replicação de Hyper-V para o Azure (com VMM)](site-recovery-vmm-to-azure-powershell-resource-manager.md)
### [Automatizar a replicação de Hyper-V para um site secundário (com VMM)](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
## Gerir
### [Remover servidores e desativar proteção](site-recovery-manage-registration-and-protection.md)
### [Aumentar horizontalmente servidores de processo](site-recovery-vmware-to-azure-manage-scaleout-process-server.md)
### [Monitorizar e resolver problemas](site-recovery-monitoring-and-troubleshooting.md)

# Referência
## [PowerShell](/powershell/resourcemanager/azurerm.siterecovery/v3.2.0/azurerm.siterecovery)
## [PowerShell clássico](/powershell/servicemanagement/azure.siterecovery/v3.1.0/azure.siterecovery)
## [REST](https://msdn.microsoft.com/en-us/library/mt750497)

# Relacionado
## [Automatização do Azure](/azure/automation/)

# Recursos
## [Percurso de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)
## [Fórum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)
## [Blogue](http://azure.microsoft.com/blog/tag/azure-site-recovery/)
## [Preços](https://azure.microsoft.com/pricing/details/site-recovery/)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=site-recovery)
