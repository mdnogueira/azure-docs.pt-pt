---
title: "Resolver problemas com os registos de operações do BizTalk Services | Microsoft Docs"
description: "Resolver problemas dos BizTalk Services utilizando os registos de operações. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 1081a9c6-58cc-4a76-8ac8-11e5e7a6ea27
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 7d3a357e1a3929153288a9d99e21f2379bcac891
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="biztalk-services-troubleshoot-using-operation-logs"></a>BizTalk Services: Resolver problemas com os registos de operações

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="what-are-the-operation-logs"></a>Quais são os registos de operação
Os registos de operações é uma funcionalidade de serviços de gestão que lhe permite ver o histórico de registos de operações executadas nos seus serviços do Azure, incluindo os BizTalk Services. Isto permite-lhe visualizar dados históricos relacionadas com operações de gestão na sua subscrição do BizTalk Service até 180 dias.

> [!NOTE]
> Esta funcionalidade só captura registos para operações de Gestão BizTalk Services, tal como quando o serviço foi iniciado, efetuada até, e assim sucessivamente. Operações são controladas utilizando o [APIs REST do BizTalk Service](http://msdn.microsoft.com/library/azure/dn232347.aspx). Para obter uma lista completa de operações que são controlados utilizando os serviços de gestão, consulte [operações registadas utilizando serviços do Azure gestão](#bizops).<br/><br/>
> Isto não capturar os registos para atividades relacionadas com o tempo de execução do BizTalk Service (como de mensagens processados pelo pontes e etc.). Para ver estes registos, utilize a vista de controlo do portal do BizTalk Services. Para obter mais informações, consulte [controlo mensagens](http://msdn.microsoft.com/library/azure/hh949805.aspx).
> 
> 

## <a name="view-biztalk-services-operation-logs"></a>Ver os registos de operações de serviços BizTalk
1. No portal, selecione **dos serviços de gestão**e, em seguida, selecione o **os registos de operações** separador.
2. Pode filtrar os registos com base nos parâmetros diferentes, como a subscrição, intervalo de datas, o tipo de serviço (por exemplo, os BizTalk Services), o nome do serviço ou o estado da operação (com êxito, falha).
3. Selecione a marca de verificação para ver a lista filtrada. A imagem seguinte mostra as atividades relacionadas com testbiztalkservice: ![ver os registos de operações][ViewLogs] 
4. Para ver mais sobre uma operação específica, selecione a linha e, em **detalhes** na barra de tarefas na parte inferior.

## <a name="bizops"></a>Operações registadas utilizando os serviços de gestão do Azure
A tabela seguinte lista as operações que são controladas utilizando os serviços de gestão do Azure:

| Nome da operação | Tarefa |
| --- | --- |
| CreateBizTalkService |Operação para criar um novo serviço BizTalk |
| DeleteBizTalkService |Operação para eliminar um BizTalk Service |
| RestartBizTalkService |Operação para reiniciar um BizTalk Service |
| StartBizTalkService |Operação para iniciar um BizTalk Service |
| StopBizTalkService |Operação para parar um serviço BizTalk |
| DisableBizTalkService |Operação para desativar um BizTalk Service |
| EnableBizTalkService |Operação para ativar um BizTalk Service |
| BackupBizTalkService |Operação de cópia de segurança de um BizTalk Service |
| RestoreBizTalkService |Operação para restaurar um BizTalk Service a partir de cópia de segurança especificada |
| SuspendBizTalkService |Operação para suspender um BizTalk Service |
| ResumeBizTalkService |Operação para retomar um BizTalk Service |
| ScaleBizTalkService |Operação de dimensionamento um BizTalk Service ou reduzir verticalmente |
| ConfigUpdateBizTalkService |Operação para atualizar a configuração de um serviço BizTalk |
| ServiceUpdateBizTalkService |Operação para atualizar ou mudar um BizTalk Service para uma versão diferente |
| PurgeBackupBizTalkService |Operação para remover as cópias de segurança do serviço BizTalk fora do período de retenção |

## <a name="see-also"></a>Veja Também
* [Serviço de cópia de segurança de BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
* [Restaurar o BizTalk Service a partir de cópia de segurança](http://go.microsoft.com/fwlink/p/?LinkID=325582)
* [Os BizTalk Services: Programador, básicas, Standard e Premium gráfico de edições](http://go.microsoft.com/fwlink/p/?LinkID=302279)
* [Os BizTalk Services: aprovisionamento](http://go.microsoft.com/fwlink/p/?LinkID=302280)
* [Serviços BizTalk: Gráfico de Estado de Aprovisionamento](http://go.microsoft.com/fwlink/p/?LinkID=329870)
* [Serviços BizTalk: Separadores Dashboard, Monitorizar e Dimensionar](http://go.microsoft.com/fwlink/p/?LinkID=302281)
* [Serviços BizTalk: limitação](http://go.microsoft.com/fwlink/p/?LinkID=302282)
* [Serviços BizTalk: Nome e Chave do Emissor](http://go.microsoft.com/fwlink/p/?LinkID=303941)
* [Como posso começar a utilizar o SDK dos Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png

