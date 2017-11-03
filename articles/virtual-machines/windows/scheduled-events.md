---
title: Agendado eventos para VMs do Windows no Azure | Microsoft Docs
description: "Agendar eventos utilizando o serviço de metadados do Azure para nas suas máquinas virtuais do Windows."
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2017
ms.author: zivr
ms.openlocfilehash: 7198fa8d1a512d10ca7022078aa2ea7bde3a4c02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-metadata-service-scheduled-events-preview-for-windows-vms"></a>Serviço de metadados do Azure: Agendados eventos (pré-visualização) para VMs do Windows

> [!NOTE] 
> Pré-visualizações ficam disponíveis para si condition que aceita os termos de utilização. Para obter mais informações, consulte [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Eventos agendados é uma das subservices sob o serviço de metadados do Azure. É responsável por analisar informações sobre eventos futuros (por exemplo, reiniciar o computador) para a aplicação possa preparar para os mesmos e limitar interrupção. Está disponível para todos os tipos de Máquina Virtual do Azure, incluindo PaaS e IaaS. Eventos agendados fornece o tempo de Máquina Virtual para efetuar tarefas preventivo para minimizar o efeito de um evento. 

Eventos agendados está disponível para Linux e VMs do Windows. Para obter informações sobre eventos agendada no Linux, consulte [eventos agendados para VMs com Linux](../windows/scheduled-events.md).

## <a name="why-scheduled-events"></a>Por que motivo agendada eventos?

Com eventos agendada, pode tomar medidas para limitar o impacto da plataforma intiated manutenção ou ações iniciadas pelo utilizador no seu serviço. 

Cargas de trabalho de várias instâncias, que utilizam técnicas de replicação para manter o estado, podem estar vulneráveis a acontecer em múltiplas instâncias de falhas. Como falhas poderão resultar em tarefas dispendiosas (por exemplo, os índices reconstrução) ou mesmo uma perda de réplica. 

Em muitos outros casos, o gerais disponibilidade do serviço pode ser melhorada através de uma sequência de encerramento correto, tais como concluir (ou cancelamento) transações em trânsito, reatribuição tarefas para as outras VMs do cluster (ativação pós-falha manual) ou remover a Máquina Virtual de uma rede de conjunto Balanceador de carga. 

Existem casos onde enviar notificações de administrador sobre um evento futuros ou registar um evento deste tipo ajudar a melhorar a possibilidade de assistência das aplicações alojadas na nuvem.

Serviço de metadados do Azure analisa agendada eventos nos seguintes casos de utilização:
-   Plataforma iniciou a manutenção (por exemplo, a implementação de SO do anfitrião)
-   Chamadas iniciada pelo utilizador (por exemplo, os reinícios de utilizador ou redeploys uma VM)


## <a name="the-basics"></a>Noções básicas  

Serviço de metadados do Azure expõe informações sobre como executar máquinas virtuais com um ponto final de REST acessível a partir da VM. As informações estão disponíveis através de um IP não encaminhável, para que não está exposta fora da VM.

### <a name="scope"></a>Âmbito
Estão anexados a eventos agendados para todas as máquinas virtuais num serviço em nuvem ou para todas as máquinas virtuais num conjunto de disponibilidade. Como resultado, deve verificar o `Resources` campo no evento para identificar as VMs que vão ser afetado. 

### <a name="discovering-the-endpoint"></a>Detetar o ponto final
No caso em que é criada uma Máquina Virtual numa rede Virtual (VNet), o serviço de metadados está disponível a partir de um IP estático não encaminhável, `169.254.169.254`.
Se a Máquina Virtual não está a ser criado dentro de uma rede Virtual, os casos predefinido para serviços em nuvem e as VMs clássicas, lógica adicional é necessário para detetar o ponto final a utilizar. Consulte este exemplo para saber como [detetar o ponto final de anfitrião](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="versioning"></a>Controlo de versões 
O serviço de metadados de instância é com a versão. As versões são obrigatórias e a versão atual é `2017-03-01`.

> [!NOTE] 
> Pré-visualização as versões anteriores dos eventos agendadas suportados {mais recente} como a api-version. Este formato já não é suportado e será preterido no futuro.

### <a name="using-headers"></a>Utilizar cabeçalhos
Ao consultar o serviço de metadados, tem de fornecer o cabeçalho `Metadata: true` para garantir que o pedido não foi redirecionado inadvertidamente.

### <a name="enabling-scheduled-events"></a>Ativar eventos agendados
Na primeira vez, que efetue um pedido para eventos agendados, o Azure implicitamente ativa a funcionalidade na sua máquina Virtual. Como resultado, deve esperar uma resposta atrasada na sua primeira chamada de dois minutos.

### <a name="user-initiated-maintenance"></a>Manutenção iniciada pelo utilizador
Utilizador iniciou a manutenção de máquina virtual através do portal do Azure, API, CLI, ou PowerShell resulta num evento agendado. Isto permite-lhe testar a lógica de preparação de manutenção na sua aplicação e permite à aplicação para se preparar para manutenção iniciada pelo utilizador.

Reinício de uma máquina virtual agendas de um evento com o tipo `Reboot`. Voltar a implementar uma máquina virtual agendas de um evento com o tipo `Redeploy`.

> [!NOTE] 
> Atualmente um máximo de 10 operações de manutenção iniciada pelo utilizador pode ser simultaneamente agendado. Este limite irá ser flexibilizada antes de disponibilidade geral eventos agendados.

> [!NOTE] 
> Atualmente manutenção iniciada pelo utilizador, resultando em eventos agendado não é configurável. Capacidade está a ser planeada para uma versão futura.

## <a name="using-the-api"></a>Utilizando a API

### <a name="query-for-events"></a>Consulta de eventos
Pode consultar eventos agendada, simplesmente ao efetuar a seguinte chamada:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01
```

Uma resposta contém uma matriz de eventos agendadas. Uma matriz vazia significa que não existem atualmente não há eventos agendados.
No caso em que existem eventos agendados, a resposta contém uma matriz de eventos: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},              
        }
    ]
}
```

### <a name="event-properties"></a>Propriedades do evento
|Propriedade  |  Descrição |
| - | - |
| EventId | Identificador exclusivo global para este evento. <br><br> Exemplo: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Impacto faz com que a este evento. <br><br> Valores: <br><ul><li> `Freeze`: A Máquina Virtual está agendada para colocar em pausa para alguns segundos. A CPU está suspenso, mas não há nenhum impacto na memória, ficheiros abertos ou ligações de rede. <li>`Reboot`: A Máquina Virtual está agendada para reiniciar o computador (memória não persistentes é perdida). <li>`Redeploy`: A Máquina Virtual está agendado para mover para outro nó (discos efémeras perdem). |
| ResourceType | Tipo de recurso que afeta este evento. <br><br> Valores: <ul><li>`VirtualMachine`|
| Recursos| Lista de recursos que tem impacto este evento. Isto é garantido que contém as máquinas no máximo uma [domínio de atualização](manage-availability.md), mas não pode conter todas as máquinas de UD. <br><br> Exemplo: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Estado do evento | Estado deste evento. <br><br> Valores: <ul><li>`Scheduled`: Este evento está agendado para iniciar após o período de tempo especificado no `NotBefore` propriedade.<li>`Started`: Este evento foi iniciado.</ul> Não `Completed` ou estado semelhante nunca é fornecido; o evento já não será devolvido quando o evento estiver concluído.
| NotBefore| Tempo após o qual este evento pode iniciar. <br><br> Exemplo: <br><ul><li> 2016-09-19T18:29:47Z  |

### <a name="event-scheduling"></a>Agendamento de eventos
Cada evento está agendado uma quantidade mínima de tempo no futuro, com base no tipo de evento. Este tempo é refletido num evento `NotBefore` propriedade. 

|EventType  | Tenha em atenção mínima |
| - | - |
| Fixar| 15 minutos |
| Reiniciar | 15 minutos |
| Voltar a implementar | 10 minutos |

### <a name="starting-an-event"></a>A partir de um evento 

Depois de ter aprendidas de um evento futura e concluir a lógica de encerramento correto, pode aprovar o evento pendente efetuando uma `POST` a chamada para o serviço de metadados com o `EventId`. Isto indica ao Azure que pode a Encurte a notificação mínima de tempo (quando possível). 

```
curl -H Metadata:true -X POST -d '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01
```

> [!NOTE] 
> Confirmar um evento permite que o evento continuar para todos os `Resources` nos eventos, não apenas a máquina virtual que reconhece o evento. Por conseguinte, pode optar por elecionar uma leader para coordenar a confirmação, que pode ser tão simple como primeira máquina no `Resources` campo.


## <a name="powershell-sample"></a>Exemplo do PowerShell 

O exemplo seguinte consulta o serviço de metadados para eventos agendados e aprova cada evento pendente.

```PowerShell
# How to get scheduled events 
function GetScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function ApproveScheduledEvent($eventId, $docIncarnation, $uri)
{    
    # Create the Scheduled Events Approval Document
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests; "DocumentIncarnation" = $docIncarnation} 
    
    # Convert to JSON string
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

function HandleScheduledEvents($scheduledEvents)
{
    # Add logic for handling events here
}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events URI for a VNET-enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-03-01' -f $localHostIP 

# Get events
$scheduledEvents = GetScheduledEvents $scheduledEventURI

# Handle events however is best for your service
HandleScheduledEvents $scheduledEvents

# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
        ApproveScheduledEvent $event.EventId $scheduledEvents.DocumentIncarnation $scheduledEventURI 
    }
}
``` 


## <a name="c-sample"></a>C\# exemplo 

O exemplo seguinte é de um cliente simple que comunica com o serviço de metadados.

```csharp
public class ScheduledEventsClient
{
    private readonly string scheduledEventsEndpoint;
    private readonly string defaultIpAddress = "169.254.169.254"; 

    // Set up the scheduled events URI for a VNET-enabled VM
    public ScheduledEventsClient()
    {
        scheduledEventsEndpoint = string.Format("http://{0}/metadata/scheduledevents?api-version=2017-03-01", defaultIpAddress);
    }

    // Get events
    public string GetScheduledEvents()
    {
        Uri cloudControlUri = new Uri(scheduledEventsEndpoint);
        using (var webClient = new WebClient())
        {
            webClient.Headers.Add("Metadata", "true");
            return webClient.DownloadString(cloudControlUri);
        }   
    }

    // Approve events
    public void ApproveScheduledEvents(string jsonPost)
    {
        using (var webClient = new WebClient())
        {
            webClient.Headers.Add("Content-Type", "application/json");
            webClient.UploadString(scheduledEventsEndpoint, jsonPost);
        }
    }
}
```

Podem ser representados eventos agendados utilizando as estruturas de dados seguintes:

```csharp
public class ScheduledEventsDocument
{
    public string DocumentIncarnation;
    public List<CloudControlEvent> Events { get; set; }
}

public class CloudControlEvent
{
    public string EventId { get; set; }
    public string EventStatus { get; set; }
    public string EventType { get; set; }
    public string ResourceType { get; set; }
    public List<string> Resources { get; set; }
    public DateTime? NotBefore { get; set; }
}

public class ScheduledEventsApproval
{
    public string DocumentIncarnation;
    public List<StartRequest> StartRequests = new List<StartRequest>();
}

public class StartRequest
{
    [JsonProperty("EventId")]
    private string eventId;

    public StartRequest(string eventId)
    {
        this.eventId = eventId;
    }
}
```

O exemplo seguinte consulta o serviço de metadados para eventos agendados e aprova cada evento pendente.

```csharp
public class Program
{
    static ScheduledEventsClient client;

    static void Main(string[] args)
    {
        client = new ScheduledEventsClient();

        while (true)
        {
            string json = client.GetDocument();
            ScheduledEventsDocument scheduledEventsDocument = JsonConvert.DeserializeObject<ScheduledEventsDocument>(json);

            HandleEvents(scheduledEventsDocument.Events);

            // Wait for user response
            Console.WriteLine("Press Enter to approve executing events\n");
            Console.ReadLine();

            // Approve events
            ScheduledEventsApproval scheduledEventsApprovalDocument = new ScheduledEventsApproval()
            {
                DocumentIncarnation = scheduledEventsDocument.DocumentIncarnation
            };
        
            foreach (CloudControlEvent event in scheduledEventsDocument.Events)
            {
                scheduledEventsApprovalDocument.StartRequests.Add(new StartRequest(event.EventId));
            }

            if (scheduledEventsApprovalDocument.StartRequests.Count > 0)
            {
                // Serialize using Newtonsoft.Json
                string approveEventsJsonDocument =
                    JsonConvert.SerializeObject(scheduledEventsApprovalDocument);

                Console.WriteLine($"Approving events with json: {approveEventsJsonDocument}\n");
                client.ApproveScheduledEvents(approveEventsJsonDocument);
            }

            Console.WriteLine("Complete. Press enter to repeat\n\n");
            Console.ReadLine();
            Console.Clear();
        }
    }

    private static void HandleEvents(List<CloudControlEvent> events)
    {
        // Add logic for handling events here
    }
}
```

## <a name="python-sample"></a>Exemplo de Python 

O exemplo seguinte consulta o serviço de metadados para eventos agendados e aprova cada evento pendente.

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01"
headers = "{Metadata:true}"
this_host = socket.gethostname()

def get_scheduled_events():
   req = urllib2.Request(metadata_url)
   req.add_header('Metadata', 'true')
   resp = urllib2.urlopen(req)
   data = json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ","_")
        if this_host in resources:
            print "+ Scheduled Event. This host is scheduled for " + eventype + " not before " + notbefore
            # Add logic for handling events here

def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)
   
if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>Passos seguintes 

- Saiba mais sobre as APIs disponíveis no [metadados de instância de serviço](instance-metadata-service.md).
- Saiba mais sobre [planeada manutenção de máquinas virtuais do Windows no Azure](planned-maintenance.md).

