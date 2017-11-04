---
title: "A obtenção de informações de estado para uma tarefa de importação/exportação do Azure | Microsoft Docs"
description: "Saiba como obter informações de estado de tarefas do serviço de importação/exportação do Microsoft Azure."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 22d7e5f0-94da-49b4-a1ac-dd4c14a423c2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: muralikk
ms.openlocfilehash: 13169716c47cf9389c8f2651393ac744441bdd6f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="retrieving-state-information-for-an-importexport-job"></a>A obtenção de informações de estado para uma tarefa de importação/exportação
Pode chamar o [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) operação para obter informações sobre ambos importar e exportar tarefas. As informações devolvidas incluem:

-   O estado atual da tarefa.

-   A percentagem de aproximado que cada tarefa foi concluída.

-   O estado atual de cada unidade.

-   URIs para blobs que contém os registos de erros e informações de registo verboso (se estiver ativada).

As secções seguintes explicam as informações devolvidas pelo `Get Job` operação.

## <a name="job-states"></a>Estados de tarefa
A tabela e o diagrama de estado abaixo descrevem os Estados que uma tarefa passa através de durante o ciclo de vida. O estado atual da tarefa pode ser determinado ao chamar o `Get Job` operação.

![JobStates](./media/storage-import-export-retrieving-state-info-for-a-job/JobStates.png "JobStates")

A tabela seguinte descreve cada Estado de uma tarefa pode pass-through.

|Estado da tarefa|Descrição|
|---------------|-----------------|
|`Creating`|Depois de chamar a operação de colocar a tarefa, é criada uma tarefa e respetivo estado é definido como `Creating`. Enquanto a tarefa está no `Creating` Estado, o serviço de importação/exportação pressupõe que as unidades não foram fornecidas para o Centro de dados. Uma tarefa pode permanecer no `Creating` para até duas semanas, após o qual é automaticamente eliminado pelo serviço de estado.<br /><br /> Se chamar a operação de propriedades da tarefa de atualização enquanto a tarefa está no `Creating` Estado, a tarefa continuará a ser o `Creating` estado e o intervalo de tempo limite é reposto para duas semanas.|
|`Shipping`|Depois de enviar o pacote, deve chamar a atualização de operação de propriedades da tarefa de atualizar o estado da tarefa para `Shipping`. O estado de envio pode ser definido apenas se o `DeliveryPackage` (postal carrier e número de controlo) e o `ReturnAddress` propriedades foram definidas para a tarefa.<br /><br /> A tarefa irá permanecer no estado de envio para duas semanas. Se duas semanas passaram e as unidades não foram recebidas, os operadores do serviço de importação/exportação serão notificados.|
|`Received`|Depois de todas as unidades foram recebidas no Centro de dados, o estado da tarefa será definido para o estado de funcionamento recebidos.|
|`Transferring`|Depois das unidades foram recebidas no Centro de dados e pelo menos uma unidade começou a processar, o estado da tarefa será definido para o `Transferring` estado. Consulte o `Drive States` secção abaixo para obter informações detalhadas.|
|`Packaging`|Depois de todas as unidades concluiu o processamento, a tarefa será colocada no `Packaging` até que as unidades são fornecidas para o cliente de estado.|
|`Completed`|Depois de todas as unidades foram fornecidas para o cliente, se a tarefa tiver sido concluída sem erros, em seguida, a tarefa será definida para o `Completed` estado. A tarefa será automaticamente eliminada após 90 dias no `Completed` estado.|
|`Closed`|Depois de todas as unidades foram fornecidas para o cliente, se tiverem sido erros durante o processamento da tarefa, em seguida, a tarefa será definida para o `Closed` estado. A tarefa será automaticamente eliminada após 90 dias no `Closed` estado.|

Pode cancelar uma tarefa apenas em determinados Estados. Uma tarefa cancelada ignore o passo de cópia de dados, mas, caso contrário, segue as mesmo transições de estado como uma tarefa que não foi cancelada.

A tabela seguinte descreve os erros que podem ocorrer para cada Estado de tarefa, bem como o efeito na tarefa quando ocorre um erro.

|Estado da tarefa|Evento|Resolução / próximos passos|
|---------------|-----------|------------------------------|
|`Creating or Undefined`|Chegaram um ou mais unidades para uma tarefa, mas a tarefa não está no `Shipping` Estado ou há um registo da tarefa no serviço.|A equipa de operações do serviço de importação/exportação irá tentar contactar o cliente para criar ou atualizar a tarefa com as informações necessárias para avançar a tarefa.<br /><br /> Se a equipa de operações não é possível contactar o cliente no prazo de duas semanas, a equipa de operações irá tentar devolver os discos.<br /><br /> No caso de não podem ser devolvidas as unidades e o cliente não pode ser alcançado, as unidades irão ser destruídas em segurança nos 90 dias.<br /><br /> Tenha em atenção que não é possível processar uma tarefa, até que o respetivo estado é atualizado para `Shipping`.|
|`Shipping`|O pacote para uma tarefa não chegou durante mais de duas semanas.|A equipa de operações notificará o cliente do pacote em falta. Com base na resposta do cliente, a equipa de operações serão expandir o intervalo deve aguardar para que o pacote para chegam, ou cancele a tarefa.<br /><br /> No caso do cliente não pode ser contactado ou não responde dentro de 30 dias, a equipa de operações irá iniciar a ação mover a tarefa do `Shipping` Estado diretamente para o `Closed` estado.|
|`Completed/Closed`|As unidades nunca foi atingido o endereço do remetente ou foram danificadas no shipment (aplica-se apenas a uma tarefa de exportação).|Se as unidades não conseguirem contactar o endereço do remetente, o cliente deve chamar a operação Get Job pela primeira vez ou verifique o estado da tarefa no portal para se certificar de que as unidades foram fornecidas. Se as unidades foram fornecidas, o cliente deve contactar o fornecedor de envio e localizar as unidades.<br /><br /> Se as unidades estão danificadas durante shipment, o cliente pode querer outra tarefa de exportação, ou um pedido de transferir os blobs em falta.|
|`Transferring/Packaging`|Tarefa tem um incorreto ou em falta o endereço do remetente.|A equipa de operações será entrar à pessoa contacto para a tarefa para obter o endereço correto.<br /><br /> No caso do cliente não pode ser alcançado, as unidades irão ser destruídas em segurança nos 90 dias.|
|`Creating / Shipping/ Transferring`|Uma unidade que não aparecem na lista de unidades importar está incluída no pacote de envio.|As unidades adicionais não serão processadas e serão devolvidas ao cliente quando a tarefa estiver concluída.|

## <a name="drive-states"></a>Estados de unidade
A tabela e o diagrama a seguir descrevem o ciclo de vida de uma unidade individuais passa através de uma tarefa de importação ou exportação. Pode obter o estado atual da unidade ao chamar o `Get Job` operação e analisando a `State` elemento do `DriveList` propriedade.

![DriveStates](./media/storage-import-export-retrieving-state-info-for-a-job/DriveStates.png "DriveStates")

A tabela seguinte descreve cada Estado de uma unidade poderá pass-through.

|Unidade de estado|Descrição|
|-----------------|-----------------|
|`Specified`|Para uma tarefa de importação, quando a tarefa é criada com a operação de colocar a tarefa, o estado inicial para uma unidade é o `Specified` estado. Para uma tarefa de exportação, uma vez que não existem unidade é especificada quando a tarefa é criada, o estado do disco inicial é o `Received` estado.|
|`Received`|A unidade passa para o `Received` quando o operador de serviço de importação/exportação processou as unidades que foram recebidas a partir da empresa de envio para uma tarefa de importação de estado. Para uma tarefa de exportação, o estado da unidade inicial é o `Received` estado.|
|`NeverReceived`|A unidade irá mudar para o `NeverReceived` quando chega o pacote para uma tarefa, mas o pacote não contém a unidade de estado. Pode também mover uma unidade neste estado se duas semanas, uma vez que o serviço recebeu as informações de envio, mas o pacote ainda não chegaram do Centro de dados.|
|`Transferring`|Uma unidade irá mudar para o `Transferring` estado quando o serviço começa a transferência de dados da unidade para armazenamento do Microsoft Azure.|
|`Completed`|Uma unidade irá mudar para o `Completed` estado quando o serviço foi transferido com êxito todos os dados sem erros.|
|`CompletedMoreInfo`|Uma unidade irá mudar para o `CompletedMoreInfo` quando o serviço detetou alguns problemas ao copiar dados a partir de ou para a unidade de estado. As informações podem incluir erros, avisos e mensagens informativas sobre substituir blobs.|
|`ShippedBack`|A unidade irá mudar para o `ShippedBack` estado quando foi enviada a partir de cópia de segurança de centro de dados para o endereço do remetente.|

A tabela seguinte descreve os Estados de falha de unidade e as ações executadas para cada Estado.

|Unidade de estado|Evento|Resolução / passo seguinte|
|-----------------|-----------|-----------------------------|
|`NeverReceived`|Uma unidade que está marcada como `NeverReceived` (porque não foi recebida como parte da shipment da tarefa), que são recebidos na shipment outro.|A equipa de operações irá mover a unidade para o `Received` estado.|
|`N/A`|Uma unidade que não faz parte de qualquer tarefa chega ao centro de dados como parte de outra tarefa.|A unidade será marcada como uma unidade adicional e será devolvida ao cliente quando a tarefa associada ao pacote original estiver concluída.|

## <a name="faulted-states"></a>Estados com falhas
Quando uma tarefa ou a unidade não consegue progresso normalmente através do respetivo ciclo de vida esperado, a tarefa ou a unidade será movida para um `Faulted` estado. Nessa altura, a equipa de operações irá contactar o cliente por e-mail ou telefone. Assim que o problema estiver resolvido, a tarefa com falhas ou a unidade será efetuada fora do `Faulted` estado e movido para o estado de adequado.

## <a name="next-steps"></a>Passos seguintes

* [Utilizar o REST API do serviço de importação/exportação](storage-import-export-using-the-rest-api.md)
