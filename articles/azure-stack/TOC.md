# Descrição geral
## [O que é o Azure Stack?](azure-stack-poc.md)
## [Novidades](azure-stack-whats-new.md)
## [Funcionalidades e conceitos principais](azure-stack-key-features.md)
## [Arquitetura de POC](azure-stack-architecture.md)

# Avaliar e implementar
## Introdução
### [Pré-requisitos da implementação](azure-stack-deploy.md)
### [Implementar](azure-stack-run-powershell-script.md)
### [Registar](azure-stack-register.md)
## Procedimento
### [Ligar ao Azure Stack POC](azure-stack-connect-azure-stack.md)
### [Adicionar imagem predefinida](azure-stack-add-default-image.md)
### [Aprovisionar uma máquina virtual](azure-stack-provision-vm.md)
### [Criar uma conta de armazenamento](azure-stack-provision-storage-account.md)
### [Adicionar um inquilino do Azure Stack](azure-stack-add-new-user-aad.md)
### [Implementar novamente o Azure Stack POC](azure-stack-redeploy.md)

# Gerir
## Descrição geral
### [Gestão da região](azure-stack-region-management.md)
### [Utilizar os portais](azure-stack-manage-portals.md)
## Introdução
### [Ligar ao Azure Stack](azure-stack-connect-azure-stack.md)
### Configurar o ambiente de gestão
#### [Instalar o PowerShell](azure-stack-powershell-install.md)
#### [Transferir ferramentas](azure-stack-powershell-download.md)
#### [Configurar o PowerShell](azure-stack-powershell-configure.md)
## Procedimento
### [Gerir atualizações](azure-stack-updates.md)
### [Monitorizar alertas e estado de funcionamento](azure-stack-monitor-health.md)
### [Gerir recursos de rede](azure-stack-viewing-public-ip-address-consumption-in-tp2.md)
### [Gerir recursos de armazenamento](azure-stack-manage-storage-accounts.md)
### [Gerir VMs do Windows Azure Pack](azure-stack-manage-windows-azure-pack.md)

# Security & Compliance
## Procedimento
### [Gerir o RBAC](azure-stack-manage-permissions.md)
### [Adicionar utilizadores ao AD FS](azure-stack-add-users-adfs.md)
### [Criar principais de serviço](Azure-stack-create-service-principals.md)

# Oferta de serviços
## Introdução
### Criar planos, ofertas e quotas
#### [Definir quotas](azure-stack-setting-quotas.md)
#### [Criar um plano](azure-stack-create-plan.md)
#### [Criar uma oferta](azure-stack-create-offer.md)
#### [Subscrever uma oferta](azure-stack-subscribe-plan-provision-vm.md)
#### [Delegar ofertas no Azure Stack](azure-stack-delegated-provider.md)
## Procedimento
### Oferecer o SQL ou MySQL como PaaS
#### [Implementar um fornecedor de recursos MySQL](azure-stack-mysql-resource-provider-deploy.md)
#### [Implementar um fornecedor de recursos SQL](azure-stack-sql-resource-provider-deploy.md)
### Oferecer Serviço de Aplicações como PaaS
#### [Descrição geral do Serviço de Aplicações no Azure Stack](azure-stack-app-service-overview.md)
#### [Antes de começar](azure-stack-app-service-before-you-get-started.md)
#### [Implementar o fornecedor de recursos do Serviço de Aplicações](azure-stack-app-service-deploy.md)
#### [Adicionar mais funções de trabalho Web](azure-stack-app-service-add-worker-roles.md)
#### [Configurar origens de implementação](azure-stack-app-service-configure-deployment-sources.md)
#### [Ativar o FTP no Serviço de Aplicações no Azure Stack](azure-stack-app-service-enable-ftp.md)
### Povoar o Marketplace
#### [Descrição geral do Marketplace](azure-stack-marketplace.md)
#### [Transferir itens do Marketplace](azure-stack-download-azure-marketplace-item.md)
#### [Criar e publicar um item do Marketplace](azure-stack-create-and-publish-marketplace-item.md)
#### [Adicionar uma imagem de máquina virtual personalizada](azure-stack-add-vm-image.md)
#### [Implementar máquinas virtuais do Linux](azure-stack-linux.md)
### Faturação e estorno
#### [Descrição geral da faturação e do estorno](azure-stack-billing-and-chargeback.md)
#### [API de utilização do recurso do fornecedor](azure-stack-provider-resource-api.md)
#### [API de utilização do recurso do inquilino](azure-stack-tenant-resource-usage-api.md)
#### [FAQ de utilização](azure-stack-usage-related-faq.md)

# Utilizar serviços
## Computação
### [Adicionar imagem de VM](azure-stack-add-vm-image.md)
### [Utilizar convidados do Linux](azure-stack-linux.md)
## Armazenamento
### [Descrição geral](azure-stack-storage-overview.md)
### [Diferenças e considerações](azure-stack-acs-differences-tp2.md)
## Rede
### [iDNS para o Azure Stack](azure-stack-understanding-dns-in-tp2.md)
### [DNS no Azure Stack](azure-stack-dns.md)
### [Compreender as ligações de rede de VPNs](azure-stack-create-vpn-connection-one-node-tp2.md)
## Cofre de Chaves
### [Descrição geral](azure-stack-kv-intro.md)
### Introdução
#### [Gerir com o portal](azure-stack-kv-manage-portal.md)
#### [Gerir com o PowerShell](azure-stack-kv-manage-powershell.md)
### Procedimento
#### [Criar uma VM com um certificado](azure-stack-kv-push-secret-into-vm.md)
#### [Aplicação de exemplo do Cofre de Chaves](azure-stack-kv-sample-app.md)

# Criar aplicações
## Descrição geral
### [Desenvolver para o Azure Stack](azure-stack-developer.md)
## Introdução
### [Ligar ao Azure Stack](azure-stack-connect-azure-stack.md)
### Configurar o ambiente de desenvolvimento
#### [Instalar o PowerShell](azure-stack-powershell-install.md)
#### [Transferir ferramentas](azure-stack-powershell-download.md)
#### [Configurar o PowerShell](azure-stack-powershell-configure.md)
#### [Instalar o Visual Studio](azure-stack-install-visual-studio.md)
## Procedimento
### [Utilizar o módulo de política](azure-stack-policy-module.md)
### Utilizar modelos
#### [Descrição geral dos modelos](azure-stack-arm-templates.md)
#### [Desenvolver modelos](azure-stack-develop-templates.md)
#### [Ver modelos](azure-stack-validate-templates.md)
#### [Implementar com o portal](azure-stack-deploy-template-portal.md)
#### [Implementar com o PowerShell](azure-stack-deploy-template-powershell.md)
#### [Implementar com o Visual Studio](azure-stack-deploy-template-visual-studio.md)

# Resolução de problemas
## [Problemas conhecidos](azure-stack-troubleshooting.md)
## [Diagnósticos no Azure Stack](azure-stack-diagnostics.md)

# Referência
## [Gerir perfis de versão de API](azure-stack-version-profiles.md)

# Recursos
## [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStack)  
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-stack)

