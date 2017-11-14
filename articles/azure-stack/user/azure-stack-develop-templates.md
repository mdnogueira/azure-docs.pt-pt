---
title: Desenvolver modelos para a pilha do Azure | Microsoft Docs
description: "Saiba Azure pilha modelo melhores práticas"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: 8a5bc713-6f51-49c8-aeed-6ced0145e07b
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: helaw
ms.openlocfilehash: b9109c58b29d5f09f1a86068a87c5e7f839228af
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="azure-resource-manager-template-considerations"></a>Considerações sobre os modelos Azure Resource Manager

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Como desenvolver a sua aplicação, é importante certificar-se a portabilidade de modelo entre o Azure e pilha do Azure.  Este tópico fornece considerações para o desenvolvimento do Azure Resource Manager [modelos](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf), para poder criar protótipos a implementação de aplicação e teste no Azure sem acesso a um ambiente de pilha do Azure.

## <a name="resource-provider-availability"></a>Disponibilidade do fornecedor de recursos
O modelo que estiver a planear implementar têm de utilizar um serviço do Microsoft Azure que já se encontra disponível ou em pré-visualização na pilha do Azure.

## <a name="public-namespaces"></a>Espaços de nomes públicos
Porque a pilha do Azure está alojada no seu centro de dados, tem espaços de nomes de ponto final de outro serviço de nuvem pública do Azure. Como resultado, os pontos finais públicos codificado em modelos do Resource Manager falharem quando tenta implementá-las à pilha do Azure. Em vez disso, pode utilizar o *referência* e *concatenar* função para criar dinamicamente o ponto final de serviço com base nos valores obtidos a partir do fornecedor de recursos durante a implementação. Por exemplo, em vez de especificar *w* no seu modelo, obter o [primaryEndpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-simple-windows-vm/azuredeploy.json#L201) definir dinamicamente o *osDisk.URI* ponto final:

     "osDisk": {"name": "osdisk","vhd": {"uri": 
     "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
      '/',variables('OSDiskName'),'.vhd')]"}}

## <a name="api-versioning"></a>Controlo de versões de API
Versões do serviço do Azure podem divergir entre o Azure e pilha do Azure. Cada recurso requer o atributo apiVersion, que define as capacidades disponibilizadas. Para garantir a compatibilidade da versão de API na pilha do Azure, seguem-se válido versões de API para cada fornecedor de recursos:

| Fornecedor de Recursos | apiVersion |
| --- | --- |
| Computação |`'2015-06-15'` |
| Rede |`'2015-06-15'`, `'2015-05-01-preview'` |
| Armazenamento |`'2016-01-01'`, `'2015-06-15'`, `'2015-05-01-preview'` |
| KeyVault | `'2015-06-01'` |
| Serviço de Aplicações |`'2015-08-01'` |
| MySQL |`'2015-09-01'` |
| SQL |`'2014-04-01-preview'` |

## <a name="template-functions"></a>Funções de modelos
Gestor de recursos [funções](../../azure-resource-manager/resource-group-template-functions.md) fornecem capacidades de necessários para criar modelos dinâmicos. Por exemplo, pode utilizar as funções para tarefas como:

* CONCATENAR ou corte de cadeias 
* Valores de referência de outros recursos
* Iterating nos recursos para implementar várias instâncias 

Como criar os modelos, algumas funções não estão disponíveis no Kit de desenvolvimento de pilha do Azure e não devem ser utilizadas. Estas funções são:

* Ignorar
* tirar

## <a name="resource-location"></a>Localização do recurso
Modelos do Resource Manager utilizam um atributo de localização para colocar recursos durante a implementação. No Azure, localizações de fazer referência a uma região EUA Oeste ou América do Sul. Na pilha do Azure, localizações são diferentes, porque a pilha do Azure no seu centro de dados.  Para garantir que os modelos são transferíveis entre o Azure e Azure pilha, deverá referenciar a localização do grupo de recursos, à medida que implementa recursos individuais. Isto pode ser feito utilizando `[resourceGroup().Location]` para garantir que todos os recursos herdam a localização do grupo de recursos.  A seguinte excerpt de modelo do Resource Manager é um exemplo de como utilizar esta função durante a implementação de uma conta de armazenamento:

    "resources": [
    {
      "name": "[variables('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "[variables('apiVersionStorage')]",
      "location": "[resourceGroup().location]",
      "comments": "This storage account is used to store the VM disks",
      "properties": {
      "accountType": "Standard_GRS"
      }
    }
    ]

## <a name="next-steps"></a>Passos seguintes
* [Implementar modelos com o PowerShell](azure-stack-deploy-template-powershell.md)
* [Implementar modelos com a CLI do Azure](azure-stack-deploy-template-command-line.md)
* [Implementar modelos com o Visual Studio](azure-stack-deploy-template-visual-studio.md)

