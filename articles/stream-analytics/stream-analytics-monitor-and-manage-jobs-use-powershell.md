---
title: Monitorizar e gerir tarefas do Stream Analytics com o PowerShell | Microsoft Docs
description: Saiba como utilizar o Azure PowerShell e cmdlets para monitorizar e gerir tarefas do Stream Analytics.
keywords: o Azure powershell, o cmdlets do powershell do azure, o comando do powershell, scripts do powershell
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 514f454e-d18c-4081-8304-ab48577e15e8
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: b8d362a2789c4e1f5594baa2b86a16e523757037
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Monitorizar e gerir tarefas do Stream Analytics com cmdlets do PowerShell do Azure
Saiba como monitorizar e gerir os recursos de Stream Analytics com cmdlets do Azure PowerShell e scripts do powershell que executar tarefas do Stream Analytics básicas.

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Pré-requisitos para executar cmdlets do Azure PowerShell para o Stream Analytics
* Crie um grupo de recursos do Azure na sua subscrição. Segue-se um exemplo de script do PowerShell do Azure. Para obter informações do Azure PowerShell, consulte [instalar e configurar o Azure PowerShell](/powershell/azure/overview);  

O Azure PowerShell 0.9.8:  

         # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group if you have more than one subscription on your account.
        Select-AzureSubscription -SubscriptionName <subscription name>

        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>

O Azure PowerShell 1.0:  

         # Log in to your Azure account
        Login-AzureRmAccount

        # Select the Azure subscription you want to use to create the resource group.
        Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureRmResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureRMResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>



> [!NOTE]
> Tarefas do Stream Analytics criadas através de programação não dispõe de monitorização ativada por predefinição.  Pode ativar manualmente a monitorização no Portal do Azure-lo acedendo à página de Monitor da tarefa e clicar no botão Ativar ou pode fazê-programaticamente, seguindo os passos, localizados em [Azure Stream Analytics - as tarefas de análise do Monitor de fluxo X509securitytokenparameters](stream-analytics-monitor-jobs.md).
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Cmdlets do PowerShell do Azure Stream Analytics
Os seguintes cmdlets PowerShell do Azure pode ser utilizados para monitorizar e gerir tarefas do Azure Stream Analytics. Tenha em atenção que o Azure PowerShell tem versões diferentes. 
**Nos exemplos indicados o primeiro comando é para o Azure PowerShell 0.9.8, é o segundo comando para o Azure PowerShell 1.0.** Os comandos do Azure PowerShell 1.0 terão sempre "AzureRM" no comando.

### <a name="get-azurestreamanalyticsjob--get-azurermstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzureRMStreamAnalyticsJob
Apresenta uma lista de todas as tarefas do Stream Analytics definidas na subscrição do Azure ou do grupo de recursos especificado ou obtém as informações da tarefa sobre uma tarefa específica dentro de um grupo de recursos.

**Exemplo 1**

O Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob

O Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob

Este comando PowerShell devolve informações sobre todas as tarefas do Stream Analytics na subscrição do Azure.

**Exemplo 2**

O Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

O Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Este comando PowerShell devolve informações sobre todas as tarefas do Stream Analytics no grupo de recursos StreamAnalytics-predefinição-Central-US.

**Exemplo 3**

O Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

O Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Este comando PowerShell devolve informações sobre a tarefa de Stream Analytics StreamingJob no grupo de recursos StreamAnalytics-predefinição-Central-US.

### <a name="get-azurestreamanalyticsinput--get-azurermstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzureRMStreamAnalyticsInput
Apresenta uma lista de todas as entradas que são definidas numa tarefa do Stream Analytics especificada ou obtém informações sobre uma entrada específica.

**Exemplo 1**

O Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

O Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Este comando PowerShell devolve informações sobre todas as entradas definidos na tarefa StreamingJob.

**Exemplo 2**

O Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

O Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Este comando PowerShell devolve informações sobre a entrada com o nome EntryStream definido na tarefa StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azurermstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzureRMStreamAnalyticsOutput
Apresenta uma lista de todas as saídas que são definidas numa tarefa do Stream Analytics especificada ou obtém informações sobre uma saída específica.

**Exemplo 1**

O Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

O Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Este comando PowerShell devolve informações sobre as saídas definidos na tarefa StreamingJob.

**Exemplo 2**

O Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

O Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Este comando PowerShell devolve informações sobre a saída com o nome de saída definida na tarefa StreamingJob.

### <a name="get-azurestreamanalyticsquota--get-azurermstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzureRMStreamAnalyticsQuota
Obtém informações sobre a quota de transmissão em fluxo unidades numa região especificada.

**Exemplo 1**

O Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsQuota –Location "Central US" 

O Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsQuota –Location "Central US" 

Este comando PowerShell devolve informações sobre a quota e utilização de unidades de transmissão em fluxo a região EUA Central.

### <a name="get-azurestreamanalyticstransformation--getazurermstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | GetAzureRMStreamAnalyticsTransformation
Obtém informações sobre uma transformação específica definida numa tarefa de Stream Analytics.

**Exemplo 1**

O Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

O Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Este comando PowerShell devolve informações sobre a transformação chamada StreamingJob na tarefa StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azurermstreamanalyticsinput"></a>Novo AzureStreamAnalyticsInput | Novo AzureRMStreamAnalyticsInput
Cria uma nova entrada dentro de uma tarefa de Stream Analytics ou atualiza uma entrada existente especificada.

O nome da entrada pode ser especificado no ficheiro de. JSON ou na linha de comandos. Se ambas são especificadas, o nome na linha de comandos tem de ser o mesmo que no ficheiro.

Se especificar uma entrada que já existe e se não especificar o parâmetro – Force, o cmdlet irá pedir-substitua a entrada existente ou não.

Se especificar – Force parâmetro e especifique existente introduzir o nome, a entrada será substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura do ficheiro JSON e o conteúdo, consulte o [entrada criar (Azure Stream Analytics)] [ msdn-rest-api-create-stream-analytics-input] secção o [referência de API do REST de gestão do Stream Analytics Biblioteca][stream.analytics.rest.api.reference].

**Exemplo 1**

O Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

O Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Este comando PowerShell cria uma nova entrada do ficheiro Input. Se já está definida uma entrada existente com o nome especificado no ficheiro de definição de entrada, o cmdlet irá perguntar se deve ou não substituí-lo.

**Exemplo 2**

O Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

O Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Este comando PowerShell cria uma nova entrada na tarefa denominada EntryStream. Se uma entrada existente com este nome já está definida, o cmdlet irá perguntar se deve ou não substituí-lo.

**Exemplo 3**

O Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

O Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Este comando PowerShell substitui a definição da origem de entrada existente chamada EntryStream com a definição do ficheiro.

### <a name="new-azurestreamanalyticsjob--new-azurermstreamanalyticsjob"></a>Novo AzureStreamAnalyticsJob | Novo AzureRMStreamAnalyticsJob
Cria uma nova tarefa de Stream Analytics no Microsoft Azure ou atualiza a definição de uma tarefa especificada existente.

O nome da tarefa pode ser especificado no ficheiro de. JSON ou na linha de comandos. Se ambas são especificadas, o nome na linha de comandos tem de ser o mesmo que no ficheiro.

Se especificar um nome de tarefa já existe e se não especificar o parâmetro – Force, o cmdlet irá pedir-substitua a tarefa existente ou não.

Se especificar – Force parâmetro e especifique um nome de tarefa existente, a definição de tarefa será substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura do ficheiro JSON e o conteúdo, consulte o [criar tarefa do Stream Analytics] [ msdn-rest-api-create-stream-analytics-job] secção o [Stream Analytics Management REST API biblioteca de referência] [stream.analytics.rest.api.reference].

**Exemplo 1**

O Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

O Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Este comando PowerShell cria uma nova tarefa de definição em JobDefinition.json. Se uma tarefa existente com o nome especificado no ficheiro de definição de tarefa já está definida, o cmdlet irá perguntar se deve ou não substituí-lo.

**Exemplo 2**

O Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

O Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Este comando PowerShell substitui a definição de tarefa para StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azurermstreamanalyticsoutput"></a>Novo AzureStreamAnalyticsOutput | Novo AzureRMStreamAnalyticsOutput
Cria uma saída de novo dentro de uma tarefa de Stream Analytics ou atualiza uma saída existente.  

O nome de saída pode ser especificado no ficheiro de. JSON ou na linha de comandos. Se ambas são especificadas, o nome na linha de comandos tem de ser o mesmo que no ficheiro.

Se especificar um resultado que já existe e se não especificar o parâmetro – Force, o cmdlet irá pedir-substitua a saída existente ou não.

Se especificar – forçar o parâmetro e especifique o nome de saída de um existente, o resultado será substituído sem confirmação.

Para obter informações detalhadas sobre a estrutura do ficheiro JSON e o conteúdo, consulte o [saída criar (Azure Stream Analytics)] [ msdn-rest-api-create-stream-analytics-output] secção o [referência de API do REST de gestão do Stream Analytics Biblioteca][stream.analytics.rest.api.reference].

**Exemplo 1**

O Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

O Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Este comando PowerShell cria uma nova saída chamada de "saída" na tarefa StreamingJob. Se uma saída existente com este nome já está definida, o cmdlet irá perguntar se deve ou não substituí-lo.

**Exemplo 2**

O Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

O Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Este comando PowerShell substitui a definição de "saída" na tarefa StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azurermstreamanalyticstransformation"></a>Novo AzureStreamAnalyticsTransformation | Novo AzureRMStreamAnalyticsTransformation
Cria uma transformação de novo dentro de uma tarefa de Stream Analytics ou atualiza a transformação existente.

O nome da transformação pode ser especificado no ficheiro de. JSON ou na linha de comandos. Se ambas são especificadas, o nome na linha de comandos tem de ser o mesmo que no ficheiro.

Se especificar uma transformação que já existe e se não especificar o parâmetro – Force, o cmdlet irá pedir-substitua a transformação existente ou não.

Se especificar – Force parâmetro e especifique um nome de transformação existente, a transformação será substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura do ficheiro JSON e o conteúdo, consulte o [criar transformação (Azure Stream Analytics)] [ msdn-rest-api-create-stream-analytics-transformation] secção o [API de REST de gestão do Stream Analytics Biblioteca de referência][stream.analytics.rest.api.reference].

**Exemplo 1**

O Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

O Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Este comando PowerShell cria uma nova transformação chamada StreamingJobTransform na tarefa StreamingJob. Se uma transformação existente já está definida com este nome, o cmdlet irá perguntar se deve ou não substituí-lo.

**Exemplo 2**

O Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

O Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

 Este comando PowerShell substitui a definição de StreamingJobTransform na tarefa StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azurermstreamanalyticsinput"></a>Remover AzureStreamAnalyticsInput | Remover AzureRMStreamAnalyticsInput
No modo assíncrono elimina uma entrada específica de uma tarefa de Stream Analytics no Microsoft Azure.  
Se especificar o parâmetro – Force, a entrada será eliminada sem confirmação.

**Exemplo 1**

O Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

O Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Este comando PowerShell remove a entrada EventStream na tarefa StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azurermstreamanalyticsjob"></a>Remover AzureStreamAnalyticsJob | Remover AzureRMStreamAnalyticsJob
No modo assíncrono elimina uma tarefa de Stream Analytics específica no Microsoft Azure.  
Se especificar o parâmetro – Force, a tarefa será eliminada sem confirmação.

**Exemplo 1**

O Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

O Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Este comando PowerShell remove a tarefa StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azurermstreamanalyticsoutput"></a>Remover AzureStreamAnalyticsOutput | Remover AzureRMStreamAnalyticsOutput
No modo assíncrono elimina uma saída específica de uma tarefa de Stream Analytics no Microsoft Azure.  
Se especificar o parâmetro – Force, o resultado será eliminado sem confirmação.

**Exemplo 1**

O Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

O Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Este remove de comando do PowerShell de saída do resultado na tarefa StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azurermstreamanalyticsjob"></a>Início AzureStreamAnalyticsJob | Início AzureRMStreamAnalyticsJob
No modo assíncrono implementa e inicia uma tarefa de Stream Analytics no Microsoft Azure.

**Exemplo 1**

O Azure PowerShell 0.9.8:  

    Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

O Azure PowerShell 1.0:  

    Start-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Este comando PowerShell inicia a tarefa StreamingJob com uma hora de início de saída personalizado como 12 de Dezembro de 2012, 12:12:12 UTC.

### <a name="stop-azurestreamanalyticsjob--stop-azurermstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzureRMStreamAnalyticsJob
No modo assíncrono para uma tarefa de Stream Analytics sejam executadas no Microsoft Azure e anula a alocação recursos que foram que estavam a ser utilizados. A definição de tarefa e os metadados permanecerá disponíveis na sua subscrição através do portal do Azure e a gestão de APIs, para que a tarefa pode ser editada e reiniciada. Não será cobrada para uma tarefa no estado de paragem.

**Exemplo 1**

O Azure PowerShell 0.9.8:  

    Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

O Azure PowerShell 1.0:  

    Stop-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Este comando do PowerShell para a tarefa StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azurermstreamanalyticsinput"></a>Teste AzureStreamAnalyticsInput | Teste AzureRMStreamAnalyticsInput
Testa a capacidade do Stream Analytics para ligar a uma entrada especificada.

**Exemplo 1**

O Azure PowerShell 0.9.8:  

    Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

O Azure PowerShell 1.0:  

    Test-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Este comando PowerShell testa o estado da ligação de EntryStream a entrada no StreamingJob.  

### <a name="test-azurestreamanalyticsoutput--test-azurermstreamanalyticsoutput"></a>Teste AzureStreamAnalyticsOutput | Teste AzureRMStreamAnalyticsOutput
Testa a capacidade do Stream Analytics para estabelecer a ligação para uma saída especificada.

**Exemplo 1**

O Azure PowerShell 0.9.8:  

    Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

O Azure PowerShell 1.0:  

    Test-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Este testes de comando do PowerShell o estado da ligação do resultado de saída no StreamingJob.  

## <a name="get-support"></a>Obter suporte
Para obter mais assistência, experimente a nossa [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

[msdn-switch-azuremode]: http://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

