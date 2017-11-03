---
title: "Iniciar um runbook de automatização do Azure com um webhook | Microsoft Docs"
description: "Webhook que permite que um cliente iniciar um runbook na automatização do Azure a partir de uma chamada HTTP.  Este artigo descreve como criar um webhook e como chamar um para iniciar um runbook."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 9b20237c-a593-4299-bbdc-35c47ee9e55d
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: magoedte;bwren;sngun
ms.openlocfilehash: d384a1f6e0f6bf49cf94020265fe5675ffc0029d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Iniciar um runbook de automatização do Azure com um webhook
A *webhook* permite-lhe iniciar um determinado runbook na automatização do Azure através de um único pedido HTTP. Isto permite que os serviços externos, como o Visual Studio Team Services, o GitHub, análise de registos do Microsoft Operations Management Suite ou aplicações personalizadas para iniciar runbooks sem a implementar uma solução completa utilizando a API de automatização do Azure.  
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Pode comparar webhooks para outros métodos de iniciar um runbook [iniciar um runbook na automatização do Azure](automation-starting-a-runbook.md)

## <a name="details-of-a-webhook"></a>Detalhes de um webhook
A tabela seguinte descreve as propriedades que é necessário configurar para um webhook.

| Propriedade | Descrição |
|:--- |:--- |
| Nome |Pode fornecer um nome que pretende para um webhook, uma vez que este não está exposta ao cliente.  Só é utilizada por si para identificar o runbook na automatização do Azure. <br>  Como melhor prática, deve dar o webhook um nome relacionadas com o cliente que irão utilizá-lo. |
| URL |O URL do webhook é o endereço exclusivo que chama um cliente com um POST de HTTP para iniciar o runbook associado para o webhook.  É gerado automaticamente quando criar o webhook.  Não é possível especificar um URL personalizado. <br> <br>  O URL contém um token de segurança que permite que o runbook seja invocado por um sistema de terceiros sem autenticação adicional. Por este motivo, deve ser tratado como uma palavra-passe.  Por motivos de segurança, só pode ver o URL no portal do Azure no momento que é criar o webhook. Deve ter em consideração o URL numa localização segura para utilização futura. |
| Data de expiração |Como um certificado, cada webhook tem uma data de expiração em que momento que já não pode ser utilizado.  Esta data de expiração pode ser modificada depois de criar o webhook. |
| Ativado |Um webhook está ativado por predefinição, quando é criado.  Se definir como desativado, nenhum cliente será conseguir utilizá-lo.  Pode definir o **ativado** propriedade ao criar o webhook ou em qualquer altura uma vez é criada. |

### <a name="parameters"></a>Parâmetros
Um webhook pode definir valores de parâmetros do runbook que são utilizados quando o runbook for iniciado por essa webhook. O webhook tem de incluir valores para todos os parâmetros obrigatórios do runbook e pode incluir valores de parâmetros opcionais. Um valor de parâmetro configurado para um webhook pode ser modificado, mesmo depois de criar o webhoook. Cada um vários webhooks associados a um único runbook pode utilizar valores de parâmetro diferentes.

Quando um cliente inicia um runbook com um webhook, este não pode substituir os valores de parâmetros definidos no webhook.  Para receber dados do cliente, o runbook pode aceitar um único parâmetro chamado **$WebhookData** do tipo [object], que contêm dados que inclui o cliente no pedido POST.

![Propriedades de Webhookdata](media/automation-webhooks/webhook-data-properties.png)

O **$WebhookData** objeto terá as seguintes propriedades:

| Propriedade | Descrição |
|:--- |:--- |
| WebhookName |O nome do webhook. |
| RequestHeader |Tabela hash que contém os cabeçalhos do pedido POST a receber. |
| RequestBody |O corpo do pedido POST a receber.  Isto irá reter qualquer formatação como cadeia JSON, XML, ou dados codificados do formulário. O runbook deve ser escrito para trabalhar com o formato dos dados que é esperado. |

Não há nenhuma configuração de webhook necessário para suportar o **$WebhookData** parâmetro e o runbook não é necessário aceitá-lo.  Se o runbook não defina o parâmetro, os detalhes do pedido enviados do cliente é ignorada.

Se especificar um valor para $WebhookData ao criar o webhook que valor será substituído quando o webhook inicia o runbook com os dados do pedido POST cliente, mesmo que o cliente não incluir quaisquer dados no corpo do pedido.  Se iniciar um runbook que tenha $WebhookData utilizando um método que não seja um webhook, pode fornecer um valor para $Webhookdata que será reconhecido pelo runbook.  Este valor deve ser um objeto com o mesmo [propriedades](#details-of-a-webhook) como $Webhookdata para que o runbook possam funcionar corretamente com o mesmo como se estava a funcionar com WebhookData real transmitido por um webhook.

Por exemplo, se estiver a iniciar o runbook seguinte do Portal do Azure e pretender passar algumas WebhookData de exemplo para fins de teste, uma vez que WebhookData é um objeto, deve ser transmitida como JSON na IU.

![Parâmetro de WebhookData da IU](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Para o runbook acima, se tem as seguintes propriedades para o parâmetro WebhookData:

1. WebhookName: *MyWebhook*
2. RequestHeader: *de utilizador de teste de =*
3. RequestBody: *["VM1", "VM2"]*

Em seguida, deverá passar o seguinte valor JSON na IU para o parâmetro WebhookData:  

* {"WebhookName": "MyWebhook", "RequestHeader": {"De": "Utilizador de teste"}, "RequestBody": "[\"VM1\",\"VM2\"]"}

![Parâmetro de WebhookData de início da IU](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Os valores de todos os parâmetros de entrada com o qual iniciou a tarefa de runbook.  Isto significa que qualquer entrada fornecida pelo cliente no pedido de webhook será iniciada e disponíveis para qualquer pessoa com acesso para a tarefa de automatização.  Por este motivo, deve ter atenção sobre, incluindo informações confidenciais chamadas para o webhook.
>

## <a name="security"></a>Segurança
A segurança de um webhook depende de privacidade do respetivo URL que contém um token de segurança que permite ser invocado. A automatização do Azure não efetuar qualquer autenticação no pedido, desde é efetuado para o URL correto. Por este motivo, webhooks não deve ser utilizada para runbooks que efetuam funções altamente confidenciais sem utilizar um meio alternativo de validar o pedido.

Pode incluir lógica dentro do runbook para determinar se este foi chamado por um webhook, verificando o **WebhookName** propriedade do parâmetro $WebhookData. O runbook foi possível efetuar mais validação, procurando informações específicas a **RequestHeader** ou **RequestBody** propriedades.

É outra estratégia para que o runbook efetuar algumas validação de uma condição externa quando recebeu um pedido de webhook.  Por exemplo, considere um runbook que é chamado pelo GitHub sempre que há uma novo consolidação para conseguir um repositório do GitHub.  O runbook pode ligar-se ao GitHub para validar que consolidar um novo tinha apenas realmente ocorreu antes de continuar.

## <a name="creating-a-webhook"></a>Criar um webhook
Utilize o procedimento seguinte para criar um novo webhook ligado a um runbook no portal do Azure.

1. Do **Runbooks painel** no portal do Azure, clique o runbook será iniciado o webhook para ver o painel de detalhes.
2. Clique em **Webhook** na parte superior do painel para abrir o **adicionar Webhook** painel. <br>
   ![Botão de Webhooks](media/automation-webhooks/webhooks-button.png)
3. Clique em **criar novo webhook** para abrir o **criar webhook painel**.
4. Especifique um **nome**, **data de expiração** para o webhook e se deve ser ativada. Consulte [detalhes de um webhook](#details-of-a-webhook) para obter mais informações estas propriedades.
5. Clique no ícone de cópia e prima Ctrl + C para copiar o URL do webhook.  Em seguida, registe-o num local seguro.  **Depois de criar o webhook, não é possível obter o URL novamente.** <br>
   ![URL do Webhook](media/automation-webhooks/copy-webhook-url.png)
6. Clique em **parâmetros** para fornecer valores para os parâmetros do runbook.  Se o runbook tiver parâmetros obrigatórios, em seguida, não será possível criar o webhook, a menos que os valores são fornecidos.
7. Clique em **criar** para criar o webhook.

## <a name="using-a-webhook"></a>Utilizar um webhook
Para utilizar um webhook depois de ter sido criado, a aplicação cliente tem de emitir um HTTP POST com o URL para o webhook.  A sintaxe do webhook estará no seguinte formato.

    http://<Webhook Server>/token?=<Token Value>

O cliente irá receber um dos códigos de retorno seguintes no pedido de POST.  

| Código | Texto | Descrição |
|:--- |:--- |:--- |
| 202 |Aceite |O pedido foi aceite e o runbook com êxito foi colocado em fila. |
| 400 |Pedido incorreto |O pedido não foi aceite para uma das seguintes razões. <ul> <li>O webhook expirou.</li> <li>O webhook está desativado.</li> <li>O token no URL é inválido.</li>  </ul> |
| 404 |Não foi encontrado |O pedido não foi aceite para uma das seguintes razões. <ul> <li>Não foi encontrado o webhook.</li> <li>O runbook não foi encontrado.</li> <li>A conta não foi encontrada.</li>  </ul> |
| 500 |Erro interno do servidor |O URL era válido, mas ocorreu um erro.  Volte a submeter o pedido. |

Pressupondo que o pedido for bem sucedido, a resposta do webhook contém o id da tarefa no formato JSON da seguinte forma. Irá conter um id de tarefa única, mas permite que o formato JSON para potenciais melhoramentos futuros.

    {"JobIds":["<JobId>"]}  

O cliente não consegue determinar quando a tarefa de runbook é concluída ou o estado de conclusão do webhook.  Pode determinar estas informações com o id da tarefa com outro método, tais como [do Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) ou [API de automatização do Azure](https://msdn.microsoft.com/library/azure/mt163826.aspx).

### <a name="example"></a>Exemplo
O exemplo seguinte utiliza o Windows PowerShell para iniciar um runbook com um webhook.  Tenha em atenção que qualquer idioma que pode efetuar um pedido HTTP pode utilizar um webhook; Windows PowerShell é utilizado apenas aqui como exemplo.

O runbook está à espera de uma lista de máquinas virtuais formatado em JSON no corpo do pedido. Podemos também são, incluindo informações sobre quem está a iniciar o runbook e a data e hora está a ser iniciada no cabeçalho do pedido.      

    $uri = "https://s1events.azure-automation.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
    $headers = @{"From"="user@contoso.com";"Date"="05/28/2015 15:47:00"}

    $vms  = @(
                @{ Name="vm01";ServiceName="vm01"},
                @{ Name="vm02";ServiceName="vm02"}
            )
    $body = ConvertTo-Json -InputObject $vms

    $response = Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body
    $jobid = ConvertFrom-Json $response


A imagem seguinte mostra as informações de cabeçalho (utilizando um [Fiddler](http://www.telerik.com/fiddler) rastreio) este pedido. Isto inclui os cabeçalhos de padrão de um pedido HTTP para além da data personalizada e de cabeçalhos que adicionámos.  Cada um destes valores está disponível para o runbook no **RequestHeaders** propriedade **WebhookData**.

![Botão de Webhooks](media/automation-webhooks/webhook-request-headers.png)

A imagem seguinte mostra o corpo do pedido (utilizando um [Fiddler](http://www.telerik.com/fiddler) rastreio) que está disponível para o runbook no **RequestBody** propriedade **WebhookData**. Isto é formatado como JSON porque foi que o formato que foi incluído no corpo do pedido.     

![Botão de Webhooks](media/automation-webhooks/webhook-request-body.png)

A imagem seguinte mostra o pedido que está a ser enviado do Windows PowerShell e a resposta resultante.  O id da tarefa é extraído da resposta e convertido numa cadeia.

![Botão de Webhooks](media/automation-webhooks/webhook-request-response.png)

O runbook de exemplo seguintes aceita o pedido de exemplo anterior e inicia as máquinas virtuais especificadas no corpo do pedido.

    workflow Test-StartVirtualMachinesFromWebhook
    {
        param (
            [object]$WebhookData
        )

        # If runbook was called from Webhook, WebhookData will not be null.
        if ($WebhookData -ne $null) {

            # Collect properties of WebhookData
            $WebhookName     =     $WebhookData.WebhookName
            $WebhookHeaders =     $WebhookData.RequestHeader
            $WebhookBody     =     $WebhookData.RequestBody

            # Collect individual headers. VMList converted from JSON.
            $From = $WebhookHeaders.From
            $VMList = ConvertFrom-Json -InputObject $WebhookBody
            Write-Output "Runbook started from webhook $WebhookName by $From."

            # Authenticate to Azure resources
            $Cred = Get-AutomationPSCredential -Name 'MyAzureCredential'
            Add-AzureAccount -Credential $Cred

            # Start each virtual machine
            foreach ($VM in $VMList)
            {
                $VMName = $VM.Name
                Write-Output "Starting $VMName"
                Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName
            }
        }
        else {
            Write-Error "Runbook mean to be started only from webhook."
        }
    }


## <a name="starting-runbooks-in-response-to-azure-alerts"></a>Iniciar os runbooks em resposta a alertas do Azure
Ativado por Webhook runbooks podem ser utilizados para reagir a [alertas do Azure](../monitoring-and-diagnostics/insights-receive-alert-notifications.md). Recursos no Azure podem ser monitorizados através da recolha de estatísticas de desempenho, disponibilidade e utilização com a ajuda de alertas do Azure. Pode receber um alerta com base nas métricas de monitorização ou eventos para os seus recursos do Azure, atualmente as contas de automatização suportam apenas as métricas. Quando o valor de uma métrica especificado excede o limiar atribuído ou se o evento configurado é acionado, em seguida, é enviada uma notificação para o administrador de serviço ou coadministradores para resolver o alerta, para obter mais informações sobre métricas e eventos, consulte [alertas do Azure](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Para além de utilizar alertas do Azure como um sistema de notificação, pode pode também iniciar runbooks em resposta a alertas. A automatização do Azure fornece a capacidade para executar runbooks ativado o webhook com alertas do Azure. Quando uma métrica excede o valor de limiar configurado, em seguida, a regra de alerta fica ativo e aciona o webhook de automatização que por sua vez executa o runbook.

![Webhooks](media/automation-webhooks/webhook-alert.jpg)

### <a name="alert-context"></a>Contexto do alerta
Considere um recurso do Azure como uma máquina virtual, a utilização da CPU desta máquina é uma da métrica de chave de desempenho. Se a utilização da CPU for 100% ou mais do que uma determinada quantidade de longo período de tempo, pode querer reiniciar a máquina virtual para corrigir o problema. Isto pode ser resolvido ao configurar uma regra de alerta para a máquina virtual e esta regra demora a percentagem de CPU, como a métrica. Percentagem de CPU aqui apenas é executada como exemplo, mas existem muitas outras métricas que pode configurar para os recursos do Azure e reiniciar a máquina virtual é uma ação que é executada para corrigir este problema, pode configurar o runbook para efetuar outras ações.

Quando esta regra de alerta fica ativa e aciona o runbook ativado o webhook, envia o contexto do alerta para o runbook. [Contexto do alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) contém detalhes incluindo **SubscriptionID**, **ResourceGroupName**, **ResourceName**, **ResourceType**, **ResourceId** e **Timestamp** que são necessários para o runbook identificar o recurso no qual será possível efetuar ação. Contexto está incorporado na parte do corpo de alertas a **WebhookData** objeto enviado para o runbook e pode ser acedido com **Webhook.RequestBody** propriedade

### <a name="example"></a>Exemplo
Criar uma máquina virtual do Azure na sua subscrição e associar um [alerta para monitorizar a métrica de percentagem de CPU](../monitoring-and-diagnostics/insights-receive-alert-notifications.md). Ao criar o alerta Certifique-se de que preencher o campo de webhook com o URL do webhook que foi gerado ao criar o webhook.

O runbook de exemplo seguinte é acionado quando a regra de alerta fica ativa e recolhe os parâmetros de contexto do alerta que são necessários para o runbook identificar o recurso no qual será possível efetuar ação.

    workflow Invoke-RunbookUsingAlerts
    {
        param (      
            [object]$WebhookData
        )

        # If runbook was called from Webhook, WebhookData will not be null.
        if ($WebhookData -ne $null) {   
            # Collect properties of WebhookData.
            $WebhookName    =   $WebhookData.WebhookName
            $WebhookBody    =   $WebhookData.RequestBody
            $WebhookHeaders =   $WebhookData.RequestHeader

            # Outputs information on the webhook name that called This
            Write-Output "This runbook was started from webhook $WebhookName."


            # Obtain the WebhookBody containing the AlertContext
            $WebhookBody = (ConvertFrom-Json -InputObject $WebhookBody)
            Write-Output "`nWEBHOOK BODY"
            Write-Output "============="
            Write-Output $WebhookBody

            # Obtain the AlertContext     
            $AlertContext = [object]$WebhookBody.context

            # Some selected AlertContext information
            Write-Output "`nALERT CONTEXT DATA"
            Write-Output "==================="
            Write-Output $AlertContext.name
            Write-Output $AlertContext.subscriptionId
            Write-Output $AlertContext.resourceGroupName
            Write-Output $AlertContext.resourceName
            Write-Output $AlertContext.resourceType
            Write-Output $AlertContext.resourceId
            Write-Output $AlertContext.timestamp

            # Act on the AlertContext data, in our case restarting the VM.
            # Authenticate to your Azure subscription using Organization ID to be able to restart that Virtual Machine.
            $cred = Get-AutomationPSCredential -Name "MyAzureCredential"
            Add-AzureAccount -Credential $cred
            Select-AzureSubscription -subscriptionName "Visual Studio Ultimate with MSDN"

            #Check the status property of the VM
            Write-Output "Status of VM before taking action"
            Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
            Write-Output "Restarting VM"

            # Restart the VM by passing VM name and Service name which are same in this case
            Restart-AzureVM -ServiceName $AlertContext.resourceName -Name $AlertContext.resourceName
            Write-Output "Status of VM after alert is active and takes action"
            Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
        }
        else  
        {
            Write-Error "This runbook is meant to only be started from a webhook."  
        }  
    }



## <a name="next-steps"></a>Passos seguintes
* Para obter detalhes sobre diferentes formas de iniciar um runbook, consulte [iniciar um Runbook](automation-starting-a-runbook.md).
* Para obter informações sobre como visualizar o estado de uma tarefa de Runbook, consulte [execução do Runbook na automatização do Azure](automation-runbook-execution.md).
* Para saber como utilizar a automatização do Azure para executar ações em alertas do Azure, consulte [remediar alertas de VM do Azure com Runbooks de automatização](automation-azure-vm-alert-integration.md).
