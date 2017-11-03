---
title: "Iniciar e parar nós de cluster para testar micro-serviços do Azure | Microsoft Docs"
description: "Saiba como utilizar a inserção de falhas para testar uma aplicação de Service Fabric por iniciar e parar nós do cluster."
services: service-fabric
documentationcenter: .net
author: LMWF
manager: rsinha
editor: 
ms.assetid: f4e70f6f-cad9-4a3e-9655-009b4db09c6d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/12/2017
ms.author: lemai
ms.openlocfilehash: 850fbc0c74811ec942292da64064dec867cd1b9e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>A substituir o nó de início e paragem nó APIs com a API de transição de nó

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>O que o nó de parar e iniciar APIs de nó fazer?

A API de nó Parar (geridos: [StopNodeAsync()][stopnode], PowerShell: [Stop-ServiceFabricNode][stopnodeps]) deixa de um nó de Service Fabric.  Um nó de Service Fabric processo, não é uma VM ou máquina – VM ou máquina irá ainda ser em execução.  Para o resto do documento "nó" significa nó de Service Fabric.  Parar um nó coloca-o para um *parado* Estado onde não é um membro do cluster e não é possível alojar serviços, deste modo, simulando uma *baixo* nó.  Isto é útil para inserirem falhas no sistema para testar a aplicação.  A API de nó de início (geridos: [StartNodeAsync()][startnode], PowerShell: [início ServiceFabricNode][startnodeps]]) Inverte a API de nó parar  que coloca o nó de volta para um estado normal.

## <a name="why-are-we-replacing-these"></a>Por que razão são iremos substituir estas?

Conforme descrito anteriormente, um *parado* num nó de serviço é um nó intencionalmente visado utilizando a API de nó parar.  A *baixo* nó é um nó que está inativo para qualquer outro motivo (por exemplo, a VM ou a máquina está desativada).  Com a API de nó parar, o sistema não expõe informações para diferenciar entre *parado* nós e *baixo* nós.

Além disso, alguns erros devolvidos por estas APIs não estão como descritivos como podem ser.  Por exemplo, invocar a API de nó parar um já *parado* nó irá devolver o erro *InvalidAddress*.  Esta experiência foi melhorada.

Além disso, a duração de que um nó é parado para é "infinita" até que a API de nó de início é invocada.  Encontrámos Isto pode causar problemas e poderá estar sujeito a erros.  Por exemplo, vamos viu problemas em que um utilizador invocar a API de nó parar um nó e, em seguida, esqueceu acerca do mesmo.  Mais tarde, foi explicito se o nó foi *baixo* ou *parado*.


## <a name="introducing-the-node-transition-apis"></a>Introduzir as APIs de transição de nó

Iremos tiver resolvido estes problemas, acima de um novo conjunto de APIs.  A nova API de transição de nó (geridos: [StartNodeTransitionAsync()][snt]) pode ser utilizado para efetuar a transição de um nó de Service Fabric para um *parado* Estado, ou para efetuar a transição de um *parado* estado para um normal para cima do Estado.  Tenha em atenção que o "Start" nome a API não se referem a partir de um nó.  Refere-se para a partir de uma operação assíncrona que irão executar o sistema para transitar o nó para qualquer *parado* ou estado.

**Utilização**

Se a API de transição de nó não acionar uma excepção ao ser invocado, em seguida, o sistema tem de aceitar a operação assíncrona e será executá-lo.  Uma chamada com êxito não implica que a operação for concluída ainda.  Para obter informações sobre o estado atual da operação, chame a API de progresso de transição do nó (geridos: [GetNodeTransitionProgressAsync()][gntp]) com o guid utilizado ao invocar a API de transição de nó para esta operação.  A API de progresso de transição de nó devolve um objeto de NodeTransitionProgress.  Propriedade de estado deste objeto Especifica o estado da operação atual.  Se o estado é "em execução", está a executar a operação.  Se for concluído, a operação foi concluída sem erros.  Se falhou, Ocorreu um problema ao executar a operação.  O resultado da exceção propriedade irá indicar qual era o problema.  Consulte https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate para obter mais informações sobre a propriedade de estado e a secção "Utilização de exemplo" abaixo para obter exemplos de código.


**Differentiating entre um nó de paragem e um nó para baixo** se for um nó *parado* utilizando a API de transição de nó, o resultado de uma consulta do nó (geridos: [GetNodeListAsync()] [ nodequery], PowerShell: [Get-ServiceFabricNode][nodequeryps]) irá mostrar que este nó tem um *IsStopped* valor da propriedade de true.  Tenha em atenção de que isto é diferente do valor de *NodeStatus* propriedade, o que indicará *baixo*.  Se o *NodeStatus* propriedade tem um valor de *baixo*, mas *IsStopped* é false, em seguida, o nó não foi parado com a API de transição de nó, não sendo *para baixo*  por algum motivo.  Se o *IsStopped* propriedade for verdadeira e o *NodeStatus* propriedade é *para baixo*, em seguida, foi parado com a API de transição de nó.

Iniciar um *parado* utilizando a API de transição do nó de nó devolverá-o para funcionar como um membro do cluster normal novamente.  O resultado da consulta nó API apresentará *IsStopped* como FALSO, e *NodeStatus* como algo que não estiver em baixo (por exemplo, cópia de segurança).


**Limitada a duração** ao utilizar a API de transição do nó para parar um nó de um dos parâmetros necessários, *stopNodeDurationInSeconds*, representa o período de tempo em segundos, para manter o nó *parado*.  Este valor deve estar no intervalo permitido, o que tem um mínimo de 600 e um máximo de 14400.  Após este período de tempo expira, o nó será reiniciado em si para segurança do estado automaticamente.  Consulte a 1 de exemplo abaixo para obter um exemplo de utilização.

> [!WARNING]
> Evite a combinação de APIs de transição do nó e o nó de parar e iniciar APIs de nó.  A recomendação é utilizar apenas a API de transição de nó.  > Se um nó foi já foi parado com a API de nó parar, que deve ser iniciado utilizando a API de nó de início antes de as utilizar de > APIs de transição de nó.

> [!WARNING]
> Várias chamadas de APIs de transição de nó não podem ser efetuadas no mesmo nó em paralelo.  Numa situação, a API de transição do nó irão > acionar um FabricException com um valor de propriedade ErrorCode de NodeTransitionInProgress.  Assim que tiver uma transição de nó num determinado nó > foi iniciado, deve aguardar até a operação de atinge um Estado terminal (concluído, Faulted ou ForceCancelled) antes de iniciar uma > nova transition no mesmo nó.  São permitidas chamadas de transição de nó paralela em nós diferentes.


#### <a name="sample-usage"></a>Utilização de exemplo


**Exemplo 1** -o exemplo seguinte utiliza a API de transição do nó para parar um nó.

```csharp
        // Helper function to get information about a node
        static Node GetNodeInfo(FabricClient fc, string node)
        {
            NodeList n = null;
            while (n == null)
            {
                n = fc.QueryManager.GetNodeListAsync(node).GetAwaiter().GetResult();
                Task.Delay(TimeSpan.FromSeconds(1)).GetAwaiter();
            };

            return n.FirstOrDefault();
        }

        static async Task WaitForStateAsync(FabricClient fc, Guid operationId, TestCommandProgressState targetState)
        {
            NodeTransitionProgress progress = null;

            do
            {
                bool exceptionObserved = false;
                try
                {
                    progress = await fc.TestManager.GetNodeTransitionProgressAsync(operationId, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                    exceptionObserved = true;
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                    exceptionObserved = true;
                }

                if (!exceptionObserved)
                {
                    Console.WriteLine("Current state of operation '{0}': {1}", operationId, progress.State);

                    if (progress.State == TestCommandProgressState.Faulted)
                    {
                        // Inspect the progress object's Result.Exception.HResult to get the error code.
                        Console.WriteLine("'{0}' failed with: {1}, HResult: {2}", operationId, progress.Result.Exception, progress.Result.Exception.HResult);

                        // ...additional logic as required
                    }

                    if (progress.State == targetState)
                    {
                        Console.WriteLine("Target state '{0}' has been reached", targetState);
                        break;
                    }
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (true);
        }

        static async Task StopNodeAsync(FabricClient fc, string nodeName, int durationInSeconds)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            // Create a Guid
            Guid guid = Guid.NewGuid();

            // Create a NodeStopDescription object, which will be used as a parameter into StartNodeTransition
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, durationInSeconds);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStopDescription from above, which will stop the target node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    // This is retryable
                }
                catch (FabricTransientException fte)
                {
                    // This is retryable
                }

                // Backoff
                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until hte desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Exemplo 2** -o seguinte exemplo inicia uma *parado* nós.  Utiliza alguns métodos de programa auxiliar do primeiro exemplo.

```csharp
        static async Task StartNodeAsync(FabricClient fc, string nodeName)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = n.NodeInstanceId;

            // Create a NodeStartDescription object, which will be used as a parameter into StartNodeTransition
            NodeStartDescription description = new NodeStartDescription(guid, n.NodeName, nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until hte desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Exemplo 3** -o exemplo seguinte mostra a utilização incorreta.  Esta utilização está incorreta porque a *stopDurationInSeconds* fornece é superior do intervalo permitido.  Uma vez que StartNodeTransitionAsync() irá falhar com um erro fatal, a operação não foi aceite e não deve ser chamada para a API de progresso.  Este exemplo utiliza alguns métodos de programa auxiliar do primeiro exemplo.

```csharp
        static async Task StopNodeWithOutOfRangeDurationAsync(FabricClient fc, string nodeName)
        {
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();

            // Use an out of range value for stopDurationInSeconds to demonstrate error
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, 99999);

            try
            {
                await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
            }

            catch (FabricException e)
            {
                Console.WriteLine("Caught {0}", e);
                Console.WriteLine("ErrorCode {0}", e.ErrorCode);
                // Output:
                // Caught System.Fabric.FabricException: System.Runtime.InteropServices.COMException (-2147017629)
                // StopDurationInSeconds is out of range ---> System.Runtime.InteropServices.COMException: Exception from HRESULT: 0x80071C63
                // << Parts of exception omitted>>
                //
                // ErrorCode InvalidDuration
            }
        }
```

**Exemplo 4** -o exemplo seguinte mostra as informações de erro que vai ser devolvidas a partir da API de progresso de transição de nó quando a operação iniciada pela API de transição do nó é aceite, mas falha posteriormente durante a execução.  No caso de falha porque a API de transição de nó tenta iniciar um nó que não existe.  Este exemplo utiliza alguns métodos de programa auxiliar do primeiro exemplo.

```csharp
        static async Task StartNodeWithNonexistentNodeAsync(FabricClient fc)
        {
            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = 12345;

            // Intentionally use a nonexistent node
            NodeStartDescription description = new NodeStartDescription(guid, "NonexistentNode", nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.  In this case, it will end up in the Faulted state since the node does not exist.
            // When StartNodeTransitionProgressAsync()'s returned progress object has a State if Faulted, inspect the progress object's Result.Exception.HResult to get the error code.
            // In this case, it will be NodeNotFound.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Faulted).ConfigureAwait(false);
        }
```

[stopnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN#System_Fabric_FabricClient_FaultManagementClient_StopNodeAsync_System_String_System_Numerics_BigInteger_System_Fabric_CompletionMode_
[stopnodeps]: https://msdn.microsoft.com/library/mt125982.aspx
[startnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN#System_Fabric_FabricClient_FaultManagementClient_StartNodeAsync_System_String_System_Numerics_BigInteger_System_String_System_Int32_System_Fabric_CompletionMode_System_Threading_CancellationToken_
[startnodeps]: https://msdn.microsoft.com/library/mt163520.aspx
[nodequery]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient#System_Fabric_FabricClient_QueryClient_GetNodeListAsync_System_String_
[nodequeryps]: https://docs.microsoft.com/powershell/servicefabric/vlatest/Get-ServiceFabricNode?redirectedfrom=msdn
[snt]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient#System_Fabric_FabricClient_TestManagementClient_StartNodeTransitionAsync_System_Fabric_Description_NodeTransitionDescription_System_TimeSpan_System_Threading_CancellationToken_
[gntp]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient#System_Fabric_FabricClient_TestManagementClient_GetNodeTransitionProgressAsync_System_Guid_System_TimeSpan_System_Threading_CancellationToken_
