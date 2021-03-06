---
title: Agendadas eventos para VMs com Linux no Azure | Microsoft Docs
description: "Agendar eventos utilizando o serviço de metadados do Azure para em máquinas virtuais Linux."
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2017
ms.author: zivr
ms.openlocfilehash: 2df39c64470e28bdf664d388041ae1b17d80db69
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="azure-metadata-service-scheduled-events-preview-for-linux-vms"></a>Serviço de metadados do Azure: Agendados eventos (pré-visualização) para VMs com Linux

> [!NOTE] 
> Pré-visualizações ficam disponíveis para si condition que aceita os termos de utilização. Para obter mais informações, consulte [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Eventos agendados é um serviço de metadados do Azure que indica a hora da aplicação para se preparar para manutenção de máquina virtual. Fornece informações sobre eventos de manutenções (por exemplo, reiniciar o computador) para a aplicação possa preparar para os mesmos e limitar interrupção. Está disponível para todos os tipos de Máquina Virtual do Azure, incluindo PaaS e IaaS no Windows e Linux. 

Para obter informações sobre eventos agendadas no Windows, consulte [agendada eventos para VMs do Windows](../windows/scheduled-events.md).

## <a name="why-scheduled-events"></a>Por que motivo agendada eventos?

Muitas aplicações podem beneficiar da hora para se preparar para manutenção de máquina virtual. O tempo pode ser utilizado para executar tarefas específicas de aplicações que melhorar a disponibilidade, a fiabilidade e a possibilidade de assistência, incluindo: 

- Ponto de verificação e restauro
- Drenagem de ligação
- Ativação pós-falha de réplica primária 
- Remoção do agrupamento de Balanceador de carga
- Registo de eventos
- Encerramento correto 

A utilização de eventos agendada a aplicação pode detetar quando irá ocorrer e de manutenção acione tarefas para limitar o impacto.  

Eventos agendados fornece eventos nos seguintes casos de utilização:
- Plataforma iniciou a manutenção (por exemplo, atualização de SO de anfitrião)
- Utilizador iniciou a manutenção (por exemplo, utilizador reinicia ou redeploys uma VM)

## <a name="the-basics"></a>Noções básicas  

Serviço de metadados do Azure expõe informações sobre como executar máquinas virtuais com um ponto final de REST acessível a partir da VM. As informações estão disponíveis através de um IP não encaminhável, para que não está exposta fora da VM.

### <a name="scope"></a>Âmbito
Agendada eventos são entregues para:
- Todas as máquinas virtuais num serviço em nuvem
- Todas as máquinas virtuais num conjunto de disponibilidade
- Todas as máquinas virtuais de um grupo de colocação do conjunto de dimensionamento. 

Como resultado, deve verificar o `Resources` campo no evento para identificar as VMs que vão ser afetado.

### <a name="discovering-the-endpoint"></a>Detetar o ponto final
Para VNET ativada VMs, é o ponto final completo para a versão mais recente dos eventos agendada: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

No caso em que é criada uma Máquina Virtual numa rede Virtual (VNet), o serviço de metadados está disponível a partir de um IP estático não encaminhável, `169.254.169.254`.
Se a Máquina Virtual não está a ser criado dentro de uma rede Virtual, os casos predefinido para serviços em nuvem e as VMs clássicas, lógica adicional é necessário para detetar o endereço IP a utilizar. Consulte este exemplo para saber como [detetar o ponto final de anfitrião](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="versioning"></a>Controlo de versões 
O serviço de eventos agendada está com a versão. As versões são obrigatórias e a versão atual é `2017-08-01`.

| Versão | Notas de Versão | 
| - | - | 
| 2017-08-01 | <li> Removido um caráter de sublinhado prepended os nomes de recursos para as VMs do Iaas<br><li>Requisito de cabeçalho de metadados imposto para todos os pedidos | 
| 2017-03-01 | <li>Versão de pré-visualização pública


> [!NOTE] 
> Pré-visualização as versões anteriores dos eventos agendadas suportados {mais recente} como a api-version. Este formato já não é suportado e será preterido no futuro.

### <a name="using-headers"></a>Utilizar cabeçalhos
Ao consultar o serviço de metadados, tem de fornecer o cabeçalho `Metadata:true` para garantir que o pedido não foi redirecionado inadvertidamente. O `Metadata:true` cabeçalho não é necessário para todos os pedidos de eventos agendada. Falha ao incluir o cabeçalho do pedido irá resultar numa resposta pedido incorreto do serviço de metadados.

### <a name="enabling-scheduled-events"></a>Ativar eventos agendados
Na primeira vez, que efetue um pedido para eventos agendados, o Azure implicitamente ativa a funcionalidade na sua máquina Virtual. Como resultado, deve esperar uma resposta atrasada na sua primeira chamada de dois minutos.

> [!NOTE]
> Eventos agendados é desativado automaticamente para o seu serviço se o serviço não chame o ponto final para 1 dia. Depois de eventos agendada está desativado para o seu serviço, não haverá eventos criados para manutenção iniciada pelo utilizador.

### <a name="user-initiated-maintenance"></a>Manutenção iniciada pelo utilizador
Utilizador iniciou a manutenção de máquina virtual através do portal do Azure, API, CLI, ou PowerShell resulta num evento agendado. Isto permite-lhe testar a lógica de preparação de manutenção na sua aplicação e permite à aplicação para se preparar para manutenção iniciada pelo utilizador.

Reinício de uma máquina virtual agendas de um evento com o tipo `Reboot`. Voltar a implementar uma máquina virtual agendas de um evento com o tipo `Redeploy`.

> [!NOTE] 
> Atualmente um máximo de 100 operações de manutenção iniciada pelo utilizador pode ser simultaneamente agendado.

> [!NOTE] 
> Atualmente manutenção iniciada pelo utilizador, resultando em eventos agendado não é configurável. Capacidade está a ser planeada para uma versão futura.

## <a name="using-the-api"></a>Utilizando a API

### <a name="query-for-events"></a>Consulta de eventos
Pode consultar eventos agendada, simplesmente ao efetuar a seguinte chamada:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
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

Segue-se o json esperado o `POST` corpo do pedido. O pedido deve conter uma lista de `StartRequests`. Cada `StartRequest` contém o `EventId` para o evento que pretende agilizar a:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Exemplo de bash
```
curl -H Metadata:true -X POST -d '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> Confirmar um evento permite que o evento continuar para todos os `Resources` nos eventos, não apenas a máquina virtual que reconhece o evento. Por conseguinte, pode optar por elecionar uma leader para coordenar a confirmação, que pode ser tão simple como primeira máquina no `Resources` campo.

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
- Reveja os exemplos de código eventos agendada no [Azure instância metadados agendada eventos repositório do Github](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- Saiba mais sobre as APIs disponíveis no [metadados de instância de serviço](instance-metadata-service.md).
- Saiba mais sobre [a manutenção para computadores virtuais Linux no Azure planeada](planned-maintenance.md).
