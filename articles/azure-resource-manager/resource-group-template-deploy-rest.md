---
title: Implementar recursos com a REST API e modelo | Microsoft Docs
description: "Utilize o Azure Resource Manager e a API de REST do Resource Manager para implementar um recursos no Azure. Os recursos são definidos num modelo do Resource Manager."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/10/2017
ms.author: tomfitz
ms.openlocfilehash: 46856a25fb57bb2c5a3c1aeae13c11655e1a58a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Implementar recursos com modelos do Resource Manager e API REST do Resource Manager
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [CLI do Azure](resource-group-template-deploy-cli.md)
> * [Portal](resource-group-template-deploy-portal.md)
> * [API REST](resource-group-template-deploy-rest.md)
> 
> 

Este artigo explica como utilizar a API de REST do Gestor de recursos com modelos do Resource Manager para implementar os recursos no Azure.  

> [!TIP]
> Para obter ajuda com a depuração de um erro durante a implementação, consulte:
> 
> * [Ver as operações de implementação](resource-manager-deployment-operations.md) para saber mais sobre como obter as informações que ajuda a resolver o erro
> * [Resolver erros comuns ao implementar recursos para o Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md) para saber como resolver erros comuns de implementação
> 
> 

O modelo pode ser um ficheiro local ou um ficheiro externo que está disponível através de um URI. Quando o modelo reside numa conta do storage, pode restringir o acesso ao modelo e fornecer um token de assinatura (SAS) de acesso partilhado durante a implementação.

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy-with-the-rest-api"></a>Implementar com a API REST
1. Definir [cabeçalhos e os parâmetros comuns](https://docs.microsoft.com/rest/api/index), incluindo os tokens de autenticação.
2. Se não tiver um grupo de recursos existente, crie um grupo de recursos. Forneça o ID de subscrição, o nome do novo grupo de recursos e localização que precisa para a sua solução. Para obter mais informações, consulte [criar um grupo de recursos](https://docs.microsoft.com/rest/api/resources/resourcegroups#ResourceGroups_CreateOrUpdate).
   
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
          <common headers>
          {
            "location": "West US",
            "tags": {
               "tagname1": "tagvalue1"
            }
          }
3. Validar a sua implementação antes de executá-lo executando o [validar uma implementação de modelo](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Validate) operação. Quando a implementação de teste, fornece os parâmetros exatamente tal como faria ao executar a implementação (mostrada no próximo passo).
4. Crie uma implementação. Forneça o ID de subscrição, o nome do grupo de recursos, o nome da implementação e uma hiperligação para o seu modelo. Para obter informações sobre o ficheiro de modelo, consulte [ficheiro de parâmetros](#parameter-file). Para obter mais informações sobre a API REST para criar um grupo de recursos, consulte [criar uma implementação de modelo](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_CreateOrUpdate). Tenha em atenção o **modo** está definido como **Incremental**. Para executar uma implementação completa, defina **modo** para **concluída**. Seja cuidadoso ao utilizar o modo de conclusão inadvertidamente pode eliminar os recursos que não estão no seu modelo.
   
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0"
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0"
              }
            }
          }
   
      Se pretender iniciar o conteúdo da resposta, o conteúdo do pedido ou ambos, incluir **debugSetting** no pedido.
   
        "debugSetting": {
          "detailLevel": "requestContent, responseContent"
        }
   
      Pode configurar a sua conta de armazenamento para utilizar um token de assinatura (SAS) de acesso partilhado. Para obter mais informações, consulte [delegar o acesso com uma assinatura de acesso partilhado](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature).
5. Obter o estado da implementação do modelo. Para obter mais informações, consulte [obter informações sobre uma implementação de modelo](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Get).
   
          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

## <a name="parameter-file"></a>Ficheiro de parâmetros

[!INCLUDE [resource-manager-parameter-file](../../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre como lidar com as operações assíncronas REST, consulte [controlar as operações do Azure assíncronas](resource-manager-async-operations.md).
* Para obter um exemplo de implementar recursos através da biblioteca de cliente .NET, consulte [implementar recursos através de um modelo e bibliotecas de .NET](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para definir os parâmetros de modelo, consulte o artigo [criação de modelos](resource-group-authoring-templates.md#parameters).
* Para obter orientações sobre a implementação da sua solução em ambientes diferentes, consulte [Ambientes de desenvolvimento e teste no Microsoft Azure](solution-dev-test-environments.md).
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](resource-manager-subscription-governance.md).

