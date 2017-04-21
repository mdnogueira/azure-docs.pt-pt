# Descrição geral
## [O que é o Resource Manager?](resource-group-overview.md)
## [Serviços, regiões e versões de API suportados](resource-manager-supported-services.md)
## [Compreender a implementação Clássica e a implementação Resource Manager](resource-manager-deployment-model.md)
## [Governação de subscrições prescritiva](resource-manager-subscription-governance.md)
## [Exemplos de governação para empresas](resource-manager-subscription-examples.md)

# Introdução
## [Exportar modelo](resource-manager-export-template.md)
## [Criar o seu primeiro modelo](resource-manager-create-first-template.md)
## [Visual Studio com o Resource Manager](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# Procedimento
## Criar modelos
### [Melhores práticas de modelos](resource-manager-template-best-practices.md)
### [Secções de modelos](resource-group-authoring-templates.md)
### [Ligar a outros modelos](resource-group-linked-templates.md)
### [Definir a dependência entre recursos](resource-group-define-dependencies.md)
### Copiar ciclo para criar múltiplas instâncias
#### [Sintaxe básica](resource-group-create-multiple.md)
#### [Ciclo sequencial](resource-manager-sequential-loop.md)
#### [Cópia de propriedades](resource-manager-property-copy.md)
### [Definir localização](resource-manager-template-location.md)
### [Atribuir etiquetas](resource-manager-template-tags.md)
### [Definir nome e tipo do recurso subordinado](resource-manager-template-child-resource.md)
### [Partilhar o estado entre modelos ligados](best-practices-resource-manager-state.md)
### [Padrões para conceber modelos](best-practices-resource-manager-design-templates.md)
## Implementação
### [PowerShell](resource-group-template-deploy.md)
### [CLI do Azure](resource-group-template-deploy-cli.md)
### [Portal](resource-group-template-deploy-portal.md)
### [API REST](resource-group-template-deploy-rest.md)
### [Integração Contínua com o Visual Studio Team Services](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Transmitir valores seguros durante a implementação](resource-manager-keyvault-parameter.md)
## Gerir
### [PowerShell](powershell-azure-resource-manager.md)
### [CLI do Azure](xplat-cli-azure-resource-manager.md)
### [Portal](resource-group-portal.md)
### [API REST](resource-manager-rest-api.md)
### [Utilizar etiquetas para organizar recursos](resource-group-using-tags.md)
### [Mover recursos para um grupo novo ou uma subscrição nova](resource-group-move-resources.md)
## Controlar Acesso
### [Criar principal de serviço com o PowerShell](resource-group-authenticate-service-principal.md)
### [Criar principal de serviço com a CLI 2.0 do Azure](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Criar principal de serviço com a CLI 1.0 do Azure](resource-group-authenticate-service-principal-cli.md)
### [Criar principal de serviço com o portal](resource-group-create-service-principal-portal.md)
### [API de Autenticação para aceder a subscrições](resource-manager-api-authentication.md)
### [Bloquear recursos](resource-group-lock-resources.md)
### [Considerações de segurança](best-practices-resource-manager-security.md)
## Definir políticas de recursos
### [O que são políticas de recursos?](resource-manager-policy.md)
### [Atribuição da política de portal](resource-manager-policy-portal.md)
### [Atribuição da política de script](resource-manager-policy-create-assign.md)
### [Políticas de etiqueta de recursos](resource-manager-policy-tags.md)
### [Políticas de armazenamento](resource-manager-policy-storage.md)
### [Políticas de VM do Linux](../virtual-machines/linux/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Políticas de VM do Windows](../virtual-machines/windows/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
## Auditoria e Resolução de Problemas
### [Resolver erros de implementação comuns](resource-manager-common-deployment-errors.md)
### [Ver registos de atividades](resource-group-audit.md)
### [Ver as operações de implementação](resource-manager-deployment-operations.md)

# Referência
## [Funções de modelos](resource-group-template-functions.md)
## [PowerShell](/powershell/resourcemanager/azurerm.resources/v3.5.0/azurerm.resources)
## [CLI 2.0 do Azure](/cli/azure/resource)
## [.NET](/dotnet/api/microsoft.azure.management.resourcemanager)
## [Java](/java/api/com.microsoft.azure.management.resources)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagement.html)
## [Formato de modelo](/azure/templates/)
## [REST](/rest/api/resources/)

# Recursos
## [Limitar pedidos](resource-manager-request-limits.md)
## [Monitorizar operações assíncronas](resource-manager-async-operations.md)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-resource-manager)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=azure-resource-manager)
