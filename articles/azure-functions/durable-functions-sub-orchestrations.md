---
title: "Orchestrations secundárias para funções duráveis - Azure"
description: "Como chamar orchestrations de orchestrations na extensão do durável funções para as funções do Azure."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 5184bef81d1cd6ca7b41c1634def24031a4a5942
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Secundárias orchestrations nas funções duráveis (funções do Azure)

Para chamar as funções de atividade, além de funções do orchestrator podem chamar outras funções do orchestrator. Por exemplo, pode criar uma maior orchestration fora de uma biblioteca de funções do orchestrator. Em alternativa, pode executar várias instâncias de uma função do orchestrator em paralelo.

Uma função do orchestrator pode chamar outra função do orchestrator, chamando o [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) ou [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) método. O [erro processamento & compensação](durable-functions-error-handling.md#automatic-retry-on-failure) artigo tem mais informações sobre a repetição automática.

As funções do orchestrator secundárias comportam-se, tal como as funções de atividade da perspetiva da função invocadora. Estes podem devolver um valor, acionar uma excepção e pode ser aguardado pela função principal do orchestrator.

## <a name="example"></a>Exemplo

O exemplo a seguir ilustra um cenário de IoT ("Internet das coisas") em que existem vários dispositivos que têm de ser aprovisionado. Há um determinado orchestration que deverá ocorrer para cada um dos dispositivos, o que poderão ter um aspeto semelhante a seguinte:

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    string deviceId = ctx.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await ctx.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await ctx.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await ctx.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

Esta função do orchestrator pode ser utilizada como-é para aprovisionamento de dispositivos pontuais ou pode fazer parte de uma orquestração maior. No caso desta última opção, a função do principal do orchestrator pode agendar instâncias de `DeviceProvisioningOrchestration` utilizando o `CallSubOrchestratorAsync` API.

Eis um exemplo que mostra como executar várias funções do orchestrator em paralelo.

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    string[] deviceIds = await ctx.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = ctx.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba quais são os hubs de tarefas e como configurá-las](durable-functions-task-hubs.md)
