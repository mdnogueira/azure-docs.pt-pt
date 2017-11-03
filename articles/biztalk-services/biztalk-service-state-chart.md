---
title: Tarefas permitidas em Estados diferentes ou Estados nos BizTalk Services | Microsoft Docs
description: "As ações/operações permitidas no Estado MABS diferente: parar, iniciar, reiniciar, suspender, retomar, eliminar, dimensionar, atualizar a configuração e efetuar uma cópia de segurança"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: aea738f3-ec76-4099-a41b-e17fea9e252f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2016
ms.author: mandia
ms.openlocfilehash: 918154852cf4ee1b80792eaddb588f5b0237da7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-you-can-and-cant-do-using-the-biztalk-service-state"></a>O que pode e não pode fazer com o estado do BizTalk Service

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Consoante o estado atual do BizTalk service, não existem operações que pode ou não é possível efetuar o BizTalk service.

Por exemplo, aprovisionar um novo serviço BizTalk no portal clássico do Azure. Quando for concluída com êxito, o BizTalk service está a ser `active` estado. O estado do Active Directory, pode parar, suspender e eliminar o BizTalk service. Se parar o BizTalk service e paragem falha, então, o BizTalk service passa para um `StopFailed` estado. No `StopFailed` Estado, pode reiniciar o BizTalk service. Se tentar uma operação que não é permitida, como retomar, ocorre o seguinte erro:

`Operation not allowed`

## <a name="view-the-possible-states"></a>Visualizar os Estados possíveis

As tabelas seguintes listam as operações ou ações que podem ser efetuadas quando o BizTalk Service está num estado específico. Um ✔ significa que a operação é permitida enquanto com esse Estado. Uma entrada em branco significa que não é possível efetuar a operação enquanto com esse Estado.

| Estado do serviço | Iniciar | Parar | Reiniciar | Suspender | Retomar | Eliminar | Escala | Atualizar <br/> Configuração | Cópia de segurança |
| --- | --- | --- | --- | --- | --- | --- |--- | --- | --- |
| Ativa |  | ✔ | ✔ | ✔ |  | ✔ |✔ |✔ |✔ |
| Desativado |  |  |  |  |  | ✔ | |  |  | 
| Suspenso |  |  |  |  | ✔ | ✔ | |  | ✔ |
| Parada | ✔ |  | ✔ |  |  | ✔ | |  | ✔ |
| Falha na atualização de serviço |  |  |  |  |  | ✔ | |  |  | 
| DisableFailed |  |  |  |  |  | ✔ | |  |  | 
| EnableFailed |  |  |  |  |  | ✔ | |  |  | 
| StartFailed <br/> StopFailed <br/> RestartFailed | ✔ | ✔ | ✔ |  |  | ✔ | | ✔ | |
| SuspendedFailed <br/> ResumeFailed|  |  |  | ✔ | ✔ | ✔ | |  |  | 
| CreatedFailed <br/> RestoreFailed |  |  |  |  |  | ✔ | |  |  | 
| ConfigUpdateFailed  |  |  | ✔ |  |  | ✔ | |✔ | |
| ScaleFailed |  |  |  |  |  | ✔ |✔ | |  |  | 



## <a name="see-also"></a>Veja Também
* [Criar um BizTalk Service com o portal clássico do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [O que pode fazer no separadores dashboard, monitorização e dimensionamento em serviços BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [O que obtém com as edições de programador, básica, Standard e Premium nos serviços de BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Como criar cópias de segurança e restaurar um BizTalk Service](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [Limitação explicado nos serviços de BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
* [Obter os Service Bus e controlo de acesso emissor e o nome do emissor valores de chave para o seu BizTalk Service](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
* [Como posso começar a utilizar o SDK dos Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

