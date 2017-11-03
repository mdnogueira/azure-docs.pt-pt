---
title: "Compreender os erros de implementação do Azure | Microsoft Docs"
description: "Descreve como saber mais sobre os erros de implementação do Azure."
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
ms.date: 07/12/2017
ms.author: tomfitz
ms.openlocfilehash: b67bb30fa259fa08e37e11afec724c8b8c3eb633
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="understand-azure-deployment-errors"></a>Compreender os erros de implementação do Azure
Este tópico descreve os erros de implementação e como pode descobrir mais informações sobre o erro. Para resoluções de erros de implementação comuns, consulte [resolver erros comuns de implementação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="two-types-of-errors"></a>Dois tipos de erros
Existem dois tipos de erros que pode receber:

* Erros de validação
* Erros de implementação

A imagem seguinte mostra o registo de atividade para uma subscrição. Representa duas implementações. Numa implementação de um modelo de falha na validação (**validar**) e não foi possível continuar. A implementação, o modelo transmitiram validação mas falhou ao criar os recursos (**escrever implementações**). 

![Mostrar código de erro](./media/resource-manager-troubleshoot-tips/show-activity-log.png)

Erros de validação resultam de cenários que podem ser determinados antes da implementação. Incluem erros de sintaxe no seu modelo, ou tentar implementar recursos que iriam exceder as quotas de subscrição. Erros de implementação resultam de condições que ocorrem durante o processo de implementação. Incluem a tentar aceder a um recurso que está a ser implementado em paralelo.

Ambos os tipos de erros de devolverem um código de erro que utilizar para resolver problemas relacionados com a implementação. Ambos os tipos de erros são apresentados no [registo de atividade](resource-group-audit.md). No entanto, erros de validação não aparecem no seu histórico de implementação porque a implementação nunca foi iniciada.

## <a name="determine-error-code"></a>Determinar o código de erro

Pode saber sobre um erro ao observar a mensagem de erro e o código de erro. O [resolver erros comuns de implementação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md) artigo apresenta uma lista de resoluções por código de erro. Este tópico mostra como utilizar o portal do Azure para detetar o código de erro.

### <a name="validation-errors"></a>Erros de validação

Quando implementar através do portal, verá um erro de validação depois de submeter os seus valores.

![Mostrar Erro de validação de portal](./media/resource-manager-troubleshoot-tips/validation-error.png)

Selecione a mensagem para obter mais detalhes. Na imagem seguinte, verá um **InvalidTemplateDeployment** erro e uma mensagem que indica uma política de implementação está bloqueado.

![Mostrar detalhes de validação](./media/resource-manager-troubleshoot-tips/validation-details.png)

### <a name="deployment-errors"></a>Erros de implementação

Quando a operação ser aprovado na validação, mas falha durante a implementação, consulte o erro nas notificações. Selecione a notificação.

![Erro de notificação](./media/resource-manager-troubleshoot-tips/notification.png)

Consulte mais detalhes sobre a implementação. Selecione a opção para encontrar mais informações sobre o erro.

![falhada de implementação](./media/resource-manager-troubleshoot-tips/deployment-failed.png)

Consulte a mensagem de erro e códigos de erro. Tenha em atenção de que existem dois códigos de erro. O código de erro primeiro (**DeploymentFailed**) é um erro geral que não fornece os detalhes tem de resolver o erro. O segundo código de erro (**StorageAccountNotFound**) fornece os detalhes que precisa. 

![Detalhes do erro](./media/resource-manager-troubleshoot-tips/error-details.png)

## <a name="enable-debug-logging"></a>Ativar o registo de depuração
Por vezes, precisa de mais informações sobre o pedido e resposta para detetar o que aconteceu de errado. Ao utilizar o PowerShell ou a CLI do Azure, pode pedir que informações adicionais são registadas durante uma implementação.

- PowerShell

   No PowerShell, defina o **DeploymentDebugLogLevel** parâmetro a todos os, ResponseContent ou RequestContent.

  ```powershell
  New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All
  ```

   Examine o pedido de conteúdo com o seguinte cmdlet:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json
  ```

   Em alternativa, a resposta conteúda com:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json
  ```

   Estas informações podem ajudar a determinar se um valor no modelo está a ser definido incorretamente.

- CLI do Azure

   Examine as operações de implementação com o seguinte comando:

  ```azurecli
  az group deployment operation list --resource-group ExampleGroup --name vmlinux
  ```

- Modelo aninhado

   Para registar informações de depuração para um modelo aninhado, utilize o **debugSetting** elemento.

  ```json
  {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri": "{template-uri}",
              "contentVersion": "1.0.0.0"
          },
          "debugSetting": {
             "detailLevel": "requestContent, responseContent"
          }
      }
  }
  ```


## <a name="create-a-troubleshooting-template"></a>Criar um modelo de resolução de problemas
Em alguns casos, a forma mais fácil para resolver o seu modelo é testar partes do mesmo. Pode criar um simplificada modelo permite-lhe focar-se na parte que achar está a causar o erro. Por exemplo, suponha que está a receber um erro ao referenciar um recurso. Em vez de lidar com um modelo completo, crie um modelo que devolve a parte que poderá estar a causar o problema. Pode ajudar a determinar se estão a ser transmitidos nos parâmetros do direitos, utilizando as funções de modelo corretamente, e ao obter o recurso que esperava.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

Ou, suponha que tiver com erros de implementação que achar que estão relacionados com incorretamente dependências. Teste o modelo ao eliminá-lo para modelos simplificados. Em primeiro lugar, crie um modelo que implementa apenas um único recurso (como um SQL Server). Quando tiver a certeza de que tem esse recurso corretamente definido, adicione um recurso que depende (tal como uma base de dados do SQL Server). Quando tiver esses dois recursos corretamente definidos, adicione outros recursos dependentes (como políticas de auditoria). Entre cada implementação de teste, elimine o grupo de recursos para se certificar de que tem as dependências de teste. 

## <a name="check-deployment-sequence"></a>Sequência de verificação de implementação

Muitos erros de implementação acontecer quando os recursos são implementados numa sequência inesperada. Estes erros surgem quando dependências não estão corretamente configuradas. Quando estão em falta uma dependência necessária, um recurso tenta utilizar um valor para outro recurso mas outros ainda não existe. Obter um erro a indicar que um recurso não foi encontrado. Pode encontrar este tipo de erro ligados intermitentemente porque o tempo de implementação para cada recurso pode variar. Por exemplo, a sua primeira tentativa de implementar os recursos é bem sucedida porque um recurso necessário aleatoriamente é concluída no tempo. No entanto, a segunda tentativa falha porque o recurso necessário não foi concluída no tempo. 

No entanto, pretende evite definir dependências que não são necessários. Quando tiver dependências desnecessárias, prolongar a duração da implementação ao impedir que os recursos que não são dependentes entre si de que está a ser implementado em paralelo. Além disso, pode criar dependências circulares que bloquear a implementação. O [referência](resource-group-template-functions-resource.md#reference) função cria uma dependência implícita no recurso referenciado, quando esse recurso é implementado no mesmo modelo. Por conseguinte, pode ter mais de dependências que as dependências especificados no **dependsOn** propriedade. O [resourceId](resource-group-template-functions-resource.md#resourceid) função não criar uma dependência implícita ou a validar que o recurso existe.

Quando tiver problemas de dependência, terá de obter conhecimentos aprofundados sobre a ordem de implementação de recursos. Para ver a ordem das operações de implementação:

1. Selecione o histórico de implementação para o grupo de recursos.

   ![Selecione o histórico de implementação](./media/resource-manager-troubleshoot-tips/select-deployment.png)

2. Selecione uma implementação do histórico e selecione **eventos**.

   ![Selecione os eventos de implementação](./media/resource-manager-troubleshoot-tips/select-deployment-events.png)

3. Examine a sequência de eventos para cada recurso. Preste atenção para o estado de cada operação. Por exemplo, a imagem seguinte mostra as três contas do storage implementada em paralelo. Tenha em atenção que as três contas de armazenamento são iniciadas ao mesmo tempo.

   ![Implementação paralela](./media/resource-manager-troubleshoot-tips/deployment-events-parallel.png)

   A imagem seguinte mostra os três contas de armazenamento que não são implementadas em paralelo. A segunda conta de armazenamento depende a primeira conta de armazenamento e a conta de armazenamento terceira depende a segunda conta de armazenamento. Por conseguinte, a primeira conta de armazenamento é iniciada, aceite e concluída antes da próxima é iniciada.

   ![implementação sequencial](./media/resource-manager-troubleshoot-tips/deployment-events-sequence.png)

Mesmo para cenários mais complicados, pode utilizar a mesma técnica para detetar quando a implementação é iniciada e concluída para cada recurso. Examine os eventos de implementação para ver se a sequência é diferente do que seria de esperar. Se assim for, reevaluate as dependências para este recurso.

O Resource Manager identifica dependências circulares durante a validação do modelo. Devolve uma mensagem de erro que indica especificamente uma dependência circular existe. Para resolver uma dependência circular:

1. No seu modelo, localize o recurso identificado na dependência circular. 
2. Para esse recurso, examine o **dependsOn** propriedade e quaisquer utilizações do **referência** função para ver quais os recursos que depende. 
3. Examine esses recursos para ver quais os recursos que dependem. Siga as dependências até que tenha em atenção um recurso que depende o recurso original.
5. Para os recursos envolvidos na uma dependência circular, examine cuidadosamente todas as utilizações do **dependsOn** propriedade para identificar quaisquer dependências que não são necessários. Remova as dependências. Se não souber de que é necessária uma dependência, experimente removê-lo. 
6. Implementar novamente o modelo.

Remover os valores de **dependsOn** propriedade unidades pode provocar erros quando implementar o modelo. Se ocorrer um erro, adicione a dependência no modelo. 

Se esse abordagem não resolver a dependência circular, considere mover a parte da sua lógica de implementação para recursos subordinados (por exemplo, as extensões ou definições de configuração). Configure os recursos subordinados implementar após os recursos envolvidos na uma dependência circular. Por exemplo, suponha que está a implementar duas máquinas virtuais, mas tem de definir propriedades em cada um que fazem referência a si. Pode implementá-las pela seguinte ordem:

1. vm1
2. vm2
3. Extensão vm1 depende vm1 e vm2. A extensão define os valores de vm1 que obtém a partir do vm2.
4. Extensão vm2 depende vm1 e vm2. A extensão define os valores de vm2 que obtém a partir do vm1.

A mesma abordagem funciona para aplicações do App Service. Considere mover valores de configuração para um recurso subordinado do recurso de aplicação. Pode implementar duas aplicações web pela seguinte ordem:

1. webapp1
2. webapp2
3. configuração para o webapp1 depende webapp1 e webapp2. Contém as definições de aplicação com valores de webapp2.
4. configuração para o webapp2 depende webapp1 e webapp2. Contém as definições de aplicação com valores de webapp1.


## <a name="next-steps"></a>Passos seguintes
* Para resoluções de erros de implementação comuns, consulte [resolver erros comuns de implementação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para saber mais sobre as ações de auditoria, consulte [auditar operações com o Resource Manager](resource-group-audit.md).
* Para saber mais sobre as ações para determinar os erros durante a implementação, consulte [ver as operações de implementação](resource-manager-deployment-operations.md).
