---
title: "Administração e desenvolvimento de tarefas lista nos BizTalk Services | Microsoft Docs"
description: Planeamento e a tarefa ajudar para implementar os BizTalk Services do Azure.
services: biztalk-services
documentationcenter: 
author: msftman
manager: erikre
editor: 
ms.assetid: 0ab70b5b-1a88-4ba5-b329-ec51b785010e
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 7d4532daf5e4b8f45de94bbec230633978814a6e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="administration-and-development-task-list-in-biztalk-services"></a>Administração e a lista de tarefas de desenvolvimento nos serviços de BizTalk

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

## <a name="getting-started"></a>Introdução
Ao trabalhar com os BizTalk Services do Microsoft Azure, existem várias no local e baseado na nuvem componentes a ter em consideração. Para começar a utilizar, considere o seguinte fluxo de processo:  

| Passo | Quem é responsável | Tarefa | Ligações relacionadas |
| --- | --- | --- | --- |
| 1. |Administrador |Criar subscrição do Microsoft Azure utilizando uma conta Microsoft ou uma conta organizacional |[Portal Clássico do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885) |
| 2. |Administrador |Criar ou o aprovisionar um BizTalk Service. |[Criar um BizTalk Service com o portal clássico do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280) |
| 3. |Administrador |Registar, ou a implementação de serviços de BizTalk da sua empresa |[Registar e atualizar uma implementação de serviço BizTalk no Portal de serviços BizTalk](https://msdn.microsoft.com/library/azure/hh689837.aspx) |
| 4. |Administrador |Aplica-se a aplicação utiliza o BizTalk Adapter Service para ligar a um sistema de linha de negócio (LOB) no local ou utiliza uma fila ou um tópico de destino.  Crie o espaço de nomes de barramento de serviço do Azure. Dê este espaço de nomes, nome do emissor de barramento de serviço e valores de chave do emissor de barramento de serviço ao programador. |[Como: criar ou modificar um espaço de nomes de serviço do Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) e [valores de obter o nome do emissor e chave do emissor](biztalk-issuer-name-issuer-key.md) |
| 5. |Programador |Instalar o SDK e criar o projeto dos BizTalk Services no Visual Studio. |[Instalar os BizTalk Services do Azure SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx) e [criar mensagens avançado pontos finais no Azure](https://msdn.microsoft.com/library/azure/hh689766.aspx) |
| 6. |Programador |Implemente o seu BizTalk Service projeto para o seu BizTalk Service alojado no Azure. |[Implementar e atualizar o projeto de serviços BizTalk](https://msdn.microsoft.com/library/azure/hh689881.aspx) |
| 7. |Administrador |Aplica-se de que se estiver a utilizar EDI.  Pode adicionar parceiros e criar contratos no Portal de serviços do Microsoft Azure BizTalk. Quando cria um contrato, pode adicionar a bridge de e/ou as transformações criadas pelo programador para as definições de contrato. |[Configurar EDI, AS2 e EDIFACT no Portal de serviços BizTalk](https://msdn.microsoft.com/library/azure/hh689853.aspx) |
| 8. |Administrador |Utilizar o Portal clássico do Azure, monitorize o estado de funcionamento do seu BizTalk Service, incluindo as métricas do desempenho. |[Serviços BizTalk: Separadores Dashboard, Monitorizar e Dimensionar](http://go.microsoft.com/fwlink/p/?LinkID=302281) |
| 9. |Administrador |Utilizar o Portal de serviços do Microsoft Azure BizTalk, gerir os artefactos utilizados pelo BizTalk Services e as mensagens de controlar como são processados pelos ficheiros de bridge. |[Utilizar o Portal de serviços BizTalk](https://msdn.microsoft.com/library/azure/dn874043.aspx) |
| 10. |Administrador |Crie um plano de cópia de segurança para criar cópias de segurança o BizTalk Service. |[Continuidade do negócio e recuperação de desastres nos serviços de BizTalk](https://msdn.microsoft.com/library/azure/dn509557.aspx) |

## <a name="next-steps"></a>Passos Seguintes
[Tutoriais e amostras](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[Criar o projeto no Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[Instalar os BizTalk Services do Azure SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## <a name="concepts"></a>Conceitos
[Criar o projeto no Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)  
[EDI, AS2, EDIFACT mensagens e (negócio para empresas)](https://msdn.microsoft.com/library/azure/hh689898.aspx)  

## <a name="other-resources"></a>Outros Recursos
[Adicionar origem, destino e Bridge de pontos finais de mensagens](https://msdn.microsoft.com/library/azure/hh689877.aspx)  
[Saiba e criar mapas de mensagem e transformações](https://msdn.microsoft.com/library/azure/hh689905.aspx)  
[Utilizar o BizTalk Adapter Service (BAS)](https://msdn.microsoft.com/library/azure/hh689889.aspx)  
[BizTalk Services do Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)

