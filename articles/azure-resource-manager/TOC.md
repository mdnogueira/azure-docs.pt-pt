# Descrição geral
## [O que é o Resource Manager?](resource-group-overview.md)
## [Fornecedores e tipos de recursos](resource-manager-supported-services.md)
## [Implementação clássica e Resource Manager](resource-manager-deployment-model.md)
## [Governação de subscrições](resource-manager-subscription-governance.md)

# Introdução
## [Criar e implementar modelo](resource-manager-create-first-template.md)
## [Extensão do VS Code para modelos](resource-manager-vscode-extension.md)
## [Visual Studio com o Resource Manager](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# Amostras
## [Exemplos de código](https://azure.microsoft.com/en-us/resources/samples/?service=azure-resource-manager)
## Azure PowerShell
### [Implementar modelo](resource-manager-samples-powershell-deploy.md)

## CLI do Azure
### [Implementar modelo](resource-manager-samples-cli-deploy.md)

# Procedimento
## Criar modelos
### [Secções de modelos](resource-group-authoring-templates.md)
### [Melhores práticas de modelos](resource-manager-template-best-practices.md)
### [Ligar a outros modelos](resource-group-linked-templates.md)
### [Definir a dependência entre recursos](resource-group-define-dependencies.md)
### [Criar múltiplas instâncias](resource-group-create-multiple.md)
### [Definir localização](resource-manager-template-location.md)
### [Atribuir etiquetas](resource-manager-template-tags.md)
### [Definir nome e tipo do recurso subordinado](resource-manager-template-child-resource.md)
### [Atualizar recurso](resource-manager-update.md)
### [Utilizar objetos para parâmetros](resource-manager-objects-as-parameters.md)
### [Partilhar o estado entre modelos ligados](best-practices-resource-manager-state.md)
### [Padrões para conceber modelos](best-practices-resource-manager-design-templates.md)


## Implementação
### Azure PowerShell
#### [Implementar modelo](resource-group-template-deploy.md)
#### [Implementar um modelo privado com o token SAS](resource-manager-powershell-sas-token.md)
#### [Exportar modelo e reimplementar](resource-manager-export-template-powershell.md)
### CLI do Azure
#### [Implementar modelo](resource-group-template-deploy-cli.md)
#### [Implementar um modelo privado com o token SAS](resource-manager-cli-sas-token.md)
#### [Exportar modelo e reimplementar](resource-manager-export-template-cli.md)
### Portal do Azure
#### [Implementar recursos](resource-group-template-deploy-portal.md)
#### [Exportar modelo](resource-manager-export-template.md)
### [API REST](resource-group-template-deploy-rest.md)
### [Implementação entre grupos de recursos](resource-manager-cross-resource-group-deployment.md)
### [Integração Contínua com o Visual Studio Team Services](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Transmitir valores seguros durante a implementação](resource-manager-keyvault-parameter.md)

## Gerir
### [Azure PowerShell](powershell-azure-resource-manager.md)
### [CLI do Azure](xplat-cli-azure-resource-manager.md)
### [Portal do Azure](resource-group-portal.md)
### [API REST](resource-manager-rest-api.md)
### [Utilizar etiquetas para organizar recursos](resource-group-using-tags.md)
### [Mover recursos para um grupo novo ou uma subscrição nova](resource-group-move-resources.md)
### [Organizar subscrições com grupos de gestão](../billing/billing-enterprise-mgmt-group-overview.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Exemplos de governação](resource-manager-subscription-examples.md)
### [Aplicações geridas](../managed-applications/overview.md)

## Controlar Acesso
### Criar um principal de serviço
#### [Azure PowerShell](resource-group-authenticate-service-principal.md)
#### [CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Portal do Azure](resource-group-create-service-principal-portal.md)
### [API de Autenticação para aceder a subscrições](resource-manager-api-authentication.md)
### [Bloquear recursos](resource-group-lock-resources.md)

## Auditoria
### [Ver registos de atividades](resource-group-audit.md)
### [Ver as operações de implementação](resource-manager-deployment-operations.md)

## Resolução de problemas
### [Erros de implementação comuns](resource-manager-common-deployment-errors.md)
### [Compreender os erros de implementação](resource-manager-troubleshoot-tips.md)
### Resolver erros
#### [AccountNameInvalid](resource-manager-storage-account-name-errors.md)
#### [InvalidTemplate](resource-manager-invalid-template-errors.md)
#### [NoRegisteredProviderFound](resource-manager-register-provider-errors.md)
#### [NotFound](resource-manager-not-found-errors.md)
#### [ParentResourceNotFound](resource-manager-parent-resource-errors.md)
#### [RequestDisallowedByPolicy](resource-manager-policy-requestdisallowedbypolicy-error.md)
#### [ReservedResourceName](resource-manager-reserved-resource-name.md)
#### [ResourceQuotaExceeded](resource-manager-quota-errors.md)
#### [SkuNotAvailable](resource-manager-sku-not-available-errors.md)
### Erros de implementação da Máquina Virtual
#### Linux
##### [Problemas de implementação](../virtual-machines/linux/troubleshoot-deploy-vm.md)
##### [Problemas de aprovisionamento e de alocação](../virtual-machines/linux/troubleshoot-deployment-new-vm.md)
##### [Mensagens de erro comuns](../virtual-machines/linux/error-messages.md)
#### Windows
##### [Problemas de implementação](../virtual-machines/windows/troubleshoot-deploy-vm.md)
##### [Problemas de aprovisionamento e de alocação](../virtual-machines/windows/troubleshoot-deployment-new-vm.md)
##### [Mensagens de erro comuns](../virtual-machines/windows/error-messages.md)

# Referência
## [Formato de modelo](/azure/templates/)
## [Funções de modelos](resource-group-template-functions.md)
### [Funções de matriz e objeto](resource-group-template-functions-array.md)
### [Funções de comparação](resource-group-template-functions-comparison.md)
### [Funções de implementação](resource-group-template-functions-deployment.md)
### [Funções lógicas](resource-group-template-functions-logical.md)
### [Funções numéricas](resource-group-template-functions-numeric.md)
### [Funções de recursos](resource-group-template-functions-resource.md)
### [Funções de cadeia](resource-group-template-functions-string.md)
## [PowerShell](/powershell/module/azurerm.resources)
## [CLI do Azure](/cli/azure/resource)
## [.NET](/dotnet/api/microsoft.azure.management.resourcemanager)
## [Java](/java/api/com.microsoft.azure.management.resources)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagement.html)
## [REST](/rest/api/resources/)

# Recursos
## [Mapa do Azure](https://azure.microsoft.com/roadmap/?category=monitoring-management)
## [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=azure-resource-manager)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-resource-manager)
## [Limitar pedidos](resource-manager-request-limits.md)
## [Monitorizar operações assíncronas](resource-manager-async-operations.md)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)
