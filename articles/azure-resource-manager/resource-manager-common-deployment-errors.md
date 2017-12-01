---
title: "Resolver erros comuns de implementação do Azure | Microsoft Docs"
description: Descreve como resolver erros comuns ao implementar recursos do Azure com o Azure Resource Manager.
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "Erro de implementação, a implementação do azure, implementar no azure"
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2017
ms.author: tomfitz
ms.openlocfilehash: db7561c31c0748ae5c1500ba8c39dfa79274901e
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Resolver erros comuns de implementação do Azure com o Azure Resource Manager

Este artigo descreve alguns erros de implementação do Azure comuns que poderá encontrar e fornece informações para resolver os erros. Se não é possível localizar o código de erro para o erro de implementação, consulte o artigo [localizar o código de erro](#find-error-code).

## <a name="error-codes"></a>Códigos de erro

| Código de erro | Mitigação | Mais informações |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Siga as restrições de nomenclatura para contas de armazenamento. | [Resolver o nome de conta de armazenamento](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | Verifique as propriedades da conta de armazenamento disponível. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | O cluster ou a região não dispõe de recursos disponíveis ou não suporta o tamanho da VM pedido. Repetir o pedido num momento posterior, ou peça um tamanho VM diferente. | [Problemas de aprovisionamento e a atribuição de Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md) e [problemas de aprovisionamento e a alocação para Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) |
| AnotherOperationInProgress | Aguarde pela conclusão da operação em simultâneo. | |
| AuthorizationFailed | A conta ou principal de serviço não tem acesso suficiente para concluir a implementação. Verifique a sua conta pertencer a função e o acesso para o âmbito da implementação. | [Controlo de acesso baseado em funções do Azure](../active-directory/role-based-access-control-configure.md) |
| BadRequest | Enviado valores de implementação que não corresponde ao que é esperado pelo Gestor de recursos. Consulte a mensagem de estado interna para obter ajuda na resolução de problemas. | [Referência de modelo](/azure/templates/) e [suportado localizações](resource-manager-template-location.md) |
| Conflito | Está a pedir uma operação que não é permitida no estado atual do recurso. Por exemplo, redimensionamento de disco é permitido apenas quando criar uma VM ou quando a VM é desalocada. | |
| DeploymentActive | Aguarde pela implementação simultânea para este grupo de recursos para concluir. | |
| DnsRecordInUse | O nome do registo DNS tem de ser exclusivo. Fornecer um nome diferente ou modifique o registo existente. | |
| ImageNotFound | Verifique as definições de imagem VM. | [Resolver problemas de imagens de Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md) e [imagens do Windows de resolução de problemas](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) |
| InUseSubnetCannotBeDeleted | Pode encontrar este erro quando tentar atualizar um recurso, mas o processamento do pedido por eliminar e criar o recurso. Certifique-se especificar todos os valores não alterados. | [Atualizar recurso](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Obter acesso token para o inquilino adequado. Só pode obter o token do inquilino que a conta pertence. | |
| InvalidContentLink | É muito provável que foi efetuada uma tentativa ligar a um modelo aninhado que não está disponível. Verifique novamente o URI fornecido para o modelo aninhado. Se o modelo existe numa conta do storage, certificar-se de que o URI está acessível. Poderá ter de transmitir um token SAS. | [Modelos ligados](resource-group-linked-templates.md) |
| InvalidParameter | Um dos valores fornecidos para um recurso não corresponde ao valor esperado. Este erro poderá resultar de várias condições diferentes. Por exemplo, uma palavra-passe pode ser insuficiente, ou um nome de blob poderá estar incorreto. Consulte a mensagem de erro para determinar qual o valor tem de ser corrigido. | |
| InvalidRequestContent | Os valores de implementação a incluir valores que não são esperados ou estão em falta necessário valores. Confirme os valores para o tipo de recurso. | [Referência de modelo](/azure/templates/) |
| InvalidRequestFormat | Ativar o registo de depuração ao executar a implementação e verificar o conteúdo do pedido. | [O registo de depuração](resource-manager-troubleshoot-tips.md#enable-debug-logging) |
| InvalidResourceNamespace | Verificar o espaço de nomes de recurso especificado no **tipo** propriedade. | [Referência de modelo](/azure/templates/) |
| InvalidResourceReference | O recurso ainda não existe ou está incorretamente referenciado. Verifique se tem de adicionar uma dependência. Certifique-se de que a utilização de **referência** função inclui os parâmetros necessários para o seu cenário. | [Resolver dependências do](resource-manager-not-found-errors.md) |
| InvalidResourceType | Verifique o recurso de tipo que especificou no **tipo** propriedade. | [Referência de modelo](/azure/templates/) |
| InvalidTemplate | Verifique a sintaxe do modelo de erros. | [Resolver modelo inválido](resource-manager-invalid-template-errors.md) |
| LinkedAuthorizationFailed | Verifique se a sua conta pertencer ao mesmo inquilino como o grupo de recursos que está a implementar. | |
| LinkedInvalidPropertyId | O ID de recurso para um recurso não está a resolver corretamente. Certifique-se de que forneça valores todos requeridos para o ID de recurso, incluindo o ID de subscrição, nome do grupo de recursos, tipo de recurso, nome de recurso principal (se necessário) e nome do recurso. | |
| LocationRequired | Forneça uma localização para o seu recurso. | [Definir localização](resource-manager-template-location.md) |
| MissingRegistrationForLocation | Verifique o estado de registo do fornecedor de recursos e localizações suportadas. | [Resolver o registo](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | Registe a sua subscrição com o fornecedor de recursos. | [Resolver o registo](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | Verifique o estado de registo do fornecedor de recursos. | [Resolver o registo](resource-manager-register-provider-errors.md) |
| NãoLocalizado | Poderá estar a tentar implementar um recurso dependente em paralelo com um recurso principal. Verifique se tem de adicionar uma dependência. | [Resolver dependências do](resource-manager-not-found-errors.md) |
| OperationNotAllowed | A implementação está a tentar uma operação que exceda a quota para a subscrição, o grupo de recursos ou a região. Se for possível, de rever a implementação para se manter nas quotas. Caso contrário, considere solicitar uma alteração à sua quotas. | [Resolver quotas](resource-manager-quota-errors.md) |
| ParentResourceNotFound | Certifique-se de que existe um recurso de principal antes de criar o elemento subordinado recursos. | [Resolver o recurso principal](resource-manager-parent-resource-errors.md) |
| PrivateIPAddressInReservedRange | O endereço IP especificado inclui um intervalo de endereços necessário pelo Azure. Alterar o endereço IP para evitar o intervalo reservado. | [Endereços IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | O endereço IP especificado está fora do intervalo de sub-rede. Alterar o endereço IP para coincidir com o intervalo de sub-rede. | [Endereços IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Algumas propriedades não podem ser alteradas num recurso implementado. Ao atualizar um recurso, limite as suas alterações às propriedades permitidas. | [Atualizar recurso](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | A subscrição inclui uma política de recurso que impeça uma ação que está a tentar efetuar durante a implementação. Localize a política que bloqueia a ação. Se possível, modifique a sua implementação para satisfazer as limitações da política. | [Resolver políticas](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | Forneça um nome de recurso que não inclua um nome reservado. | [Nomes de recursos reservados](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Aguarde pela eliminação concluir. | |
| ResourceGroupNotFound | Verifique o nome do grupo de recursos de destino para a implementação. Já deve existir na sua subscrição. Verifique o contexto de subscrição. | [CLI do Azure](/cli/azure/account?#az_account_set) [PowerShell](/powershell/module/azurerm.profile/set-azurermcontext) |
| ResourceNotFound | A implementação referencia um recurso que não é possível resolver. Certifique-se de que a utilização de **referência** função inclui os parâmetros necessários para o seu cenário. | [Resolver referências](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | A implementação está a tentar criar recursos excedem a quota para a subscrição, o grupo de recursos ou a região. Se for possível, rever a sua infraestrutura de permanecer nas quotas. Caso contrário, considere solicitar uma alteração à sua quotas. | [Resolver quotas](resource-manager-quota-errors.md) |
| SkuNotAvailable | Selecione o SKU (por exemplo, o tamanho da VM) que está disponível para a localização que selecionou. | [Resolver SKU](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | Forneça um nome exclusivo para a conta de armazenamento. | [Resolver o nome de conta de armazenamento](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | Forneça um nome exclusivo para a conta de armazenamento. | [Resolver o nome de conta de armazenamento](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | Verifique a subscrição, o grupo de recursos e o nome da conta de armazenamento que está a tentar utilizar. | |
| SubnetsNotInSameVnet | Uma máquina virtual só pode ter uma rede virtual. Quando implementar vários NICs, certifique-se de que pertencem à mesma rede virtual. | [Vários NICs](../virtual-machines/windows/multiple-nics.md) |

## <a name="find-error-code"></a>Localizar o código de erro

Quando encontrar um erro durante a implementação, o Gestor de recursos devolve um código de erro. Pode ver a mensagem de erro através do portal, o PowerShell ou a CLI do Azure. A mensagem de erro externa pode ser demasiado geral para resolução de problemas. Procure a mensagem interna que contém informações detalhadas sobre o erro. Para obter mais informações, consulte [determinar o código de erro](resource-manager-troubleshoot-tips.md#determine-error-code).

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre as ações de auditoria, consulte [auditar operações com o Resource Manager](resource-group-audit.md).
* Para saber mais sobre as ações para determinar os erros durante a implementação, consulte [ver as operações de implementação](resource-manager-deployment-operations.md).
