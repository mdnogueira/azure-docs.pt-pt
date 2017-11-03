---
title: "Operações de implementação com o Azure Resource Manager | Microsoft Docs"
description: "Descreve como ver as operações de implementação Azure Resource Manager com o portal, o PowerShell, a CLI do Azure e a REST API."
services: azure-resource-manager,virtual-machines
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 01/13/2017
ms.author: tomfitz
ms.openlocfilehash: fb6b3b357fd1f66184e480115a9c863ba31ac193
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="view-deployment-operations-with-azure-resource-manager"></a>Operações de implementação de vista com o Azure Resource Manager


Pode ver as operações de uma implementação através do portal do Azure. Pode ser mais interessado em visualizar as operações quando tiver recebido um erro durante a implementação para que este artigo foca-se sobre a visualização de operações que falharam. O portal fornece uma interface que permite-lhe facilmente encontrar os erros e determinar o potenciais de correções.

[!INCLUDE [resource-manager-troubleshoot-introduction](../../includes/resource-manager-troubleshoot-introduction.md)]

## <a name="portal"></a>Portal
Para ver as operações de implementação, utilize os seguintes passos:

1. Para o grupo de recursos envolvido na implementação, tenha em atenção o estado da última implementação. Pode selecionar este estado para obter mais detalhes.
   
    ![Estado de implementação](./media/resource-manager-deployment-operations/deployment-status.png)
2. Pode ver o histórico recente da implementação. Selecione a implementação falhou.
   
    ![Estado de implementação](./media/resource-manager-deployment-operations/select-deployment.png)
3. Selecione a ligação para ver uma descrição do motivo pelo qual a implementação falhou. Na imagem abaixo, o registo DNS não é exclusivo.  
   
    ![Ver falhas de implementação](./media/resource-manager-deployment-operations/view-error.png)
   
    Esta mensagem de erro deve ser suficiente para que possa começar a resolução de problemas. No entanto, se precisar de mais detalhes sobre as tarefas que foram concluídas, pode ver as operações, conforme mostrado nos passos seguintes.
4. Pode ver todas as operações de implementação no **implementação** painel. Selecione todas as operações para ver mais detalhes.
   
    ![operações de vista](./media/resource-manager-deployment-operations/view-operations.png)
   
    Neste caso, verá que a conta de armazenamento, a rede virtual e o conjunto de disponibilidade foram criados com êxito. O endereço IP público falha e outros recursos não foram tentados.
5. Pode ver eventos para a implementação, selecionando **eventos**.
   
    ![Ver eventos](./media/resource-manager-deployment-operations/view-events.png)
6. Ver todos os eventos para a implementação e selecionar qualquer uma para obter mais detalhes. Tenha em atenção demasiado a correlação de IDs. Este valor pode ser útil ao trabalhar com o suporte técnico a resolver problemas de uma implementação.
   
    ![Ver eventos](./media/resource-manager-deployment-operations/see-all-events.png)

## <a name="powershell"></a>PowerShell
1. Para obter o estado geral de uma implementação, utilize o **Get-AzureRmResourceGroupDeployment** comando. 

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup
  ```

   Em alternativa, pode filtrar os resultados para apenas as implementações que falharam.

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
  ```
   
2. Cada implementação inclui várias operações. Cada operação representa um passo no processo de implementação. Para descobrir o que aconteceu de errado com uma implementação, normalmente, terá de ver detalhes sobre as operações de implementação. Pode ver o estado das operações com **Get-AzureRmResourceGroupDeploymentOperation**.

  ```powershell 
  Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName vmDeployment
  ```

    Que devolve várias operações com cada um no seguinte formato:

  ```powershell
  Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
  OperationId    : A3EB2DA598E0A780
  Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                   duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                   serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
  PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                   serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
  ```

3. Para obter mais detalhes sobre operações, obter as propriedades de operações com **falha** estado.

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
  ```
   
    Que devolve todas as operações com falha com cada um no seguinte formato:

  ```powershell
  provisioningOperation : Create
  provisioningState     : Failed
  timestamp             : 2016-06-14T21:54:55.1468068Z
  duration              : PT3.1449887S
  trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
  serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
  statusCode            : BadRequest
  statusMessage         : @{error=}
  targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                          Microsoft.Network/publicIPAddresses/myPublicIP;
                          resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
  ```

    Tenha em atenção o serviceRequestId e trackingId para a operação. O serviceRequestId pode ser útil ao trabalhar com o suporte técnico a resolver problemas de uma implementação. Irá utilizar o trackingId no próximo passo para se focarem numa operação específica.
4. Para obter a mensagem de estado de uma operação falha específica, utilize o seguinte comando:

  ```powershell
  ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
  ```

    Que devolve:

  ```powershell
  code           message                                                                        details
  ----           -------                                                                        -------
  DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
  ```
4. Cada operação de implementação no Azure inclui conteúdo do pedido e resposta. O conteúdo do pedido é o que lhe enviados para o Azure, durante a implementação (por exemplo, criar uma VM, o disco do SO e outros recursos). O conteúdo da resposta é o Azure enviado novamente a partir do seu pedido de implementação. Durante a implementação, pode utilizar **DeploymentDebugLogLevel** paramenter para especificar que o pedido de e/ou resposta é mantida no registo. 

  Obter informações do registo e guardá-lo localmente ao utilizar os seguintes comandos do PowerShell:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
  ```

## <a name="azure-cli"></a>CLI do Azure

1. Obter o estado geral de uma implementação com o **mostrar de implementação de grupo do azure** comando.

  ```azurecli
  azure group deployment show --resource-group ExampleGroup --name ExampleDeployment --json
  ```
  
  Um dos valores devolvidos é o **correlationId**. Este valor é utilizado para controlar eventos relacionados e pode ser útil ao trabalhar com o suporte técnico a resolver problemas de uma implementação.

  ```azurecli
  "properties": {
    "provisioningState": "Failed",
    "correlationId": "4002062a-a506-4b5e-aaba-4147036b771a",
  ```

2. Para ver as operações de uma implementação, utilize:

  ```azurecli
  azure group deployment operation list --resource-group ExampleGroup --name ExampleDeployment --json
  ```

## <a name="rest"></a>REST

1. Obter informações sobre uma implementação com o [obter informações sobre uma implementação de modelo](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Get) operação.

  ```http
  GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
  ```

    Na resposta, tenha em atenção em particular o **provisioningState**, **correlationId**, e **erro** elementos. O **correlationId** é utilizado para controlar eventos relacionados e pode ser útil ao trabalhar com o suporte técnico a resolver problemas de uma implementação.

  ```json
  { 
    ...
    "properties": {
      "provisioningState":"Failed",
      "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
      ...
      "error":{
        "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
        "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
      }  
    }
  }
  ```

2. Obter informações sobre as operações de implementação com o [lista todas as operações de implementação do modelo](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_List) operação. 

  ```http
  GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
  ```
   
    A resposta inclui informações de pedido de e/ou resposta com base no que especificou no **debugSetting** propriedade durante a implementação.

  ```json
  {
    ...
    "properties": 
    {
      ...
      "request":{
        "content":{
          "location":"West US",
          "properties":{
            "accountType": "Standard_LRS"
          }
        }
      },
      "response":{
        "content":{
          "error":{
            "message":"Conflict","code":"Conflict"
          }
        }
      }
    }
  }
  ```


## <a name="next-steps"></a>Passos seguintes
* Para obter ajuda com resolver erros de implementação específicos, consulte [resolver erros comuns ao implementar recursos para o Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para saber mais sobre como utilizar os registos de atividade para monitorizar os outros tipos de ações, consulte [ver registos de atividade para gerir recursos do Azure](resource-group-audit.md).
* Para validar a sua implementação antes de executá-lo, consulte [implementar um grupo de recursos com o modelo Azure Resource Manager](resource-group-template-deploy.md).

