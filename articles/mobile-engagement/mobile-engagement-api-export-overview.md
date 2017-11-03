---
title: "Descrição geral da API de exportação de Mobile Engagement"
description: "Aprender as noções básicas sobre a exportação de dados não processados gerados pelos dispositivos do utilizador para aproveitam as suas próprias ferramentas"
services: mobile-engagement
documentationcenter: mobile
author: kpiteira
manager: erikre
editor: 
ms.assetid: 9380d47b-d7fa-4d4c-888f-97e6482196bb
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 04/26/2016
ms.author: kapiteir
ms.openlocfilehash: 346e0e480ff84ee849f135a7605d27df9e32f966
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="mobile-engagement-export-api-overview"></a>Descrição geral da API de exportação de Mobile Engagement
## <a name="introduction"></a>Introdução
Neste documento, irá aprender as noções básicas sobre a exportação de dados não processados gerados pelos dispositivos do utilizador para aproveitam as suas próprias ferramentas.

## <a name="pre-requisites"></a>Pré-requisitos
Exportar os dados não processados do Mobile Engagement requer:

* A configuração de autenticação de API para poder utilizar as APIs (consulte [configuração manual de autenticação](mobile-engagement-api-authentication-manual.md)),
* Utilizar REST APIs ou o [.net SDK](mobile-engagement-dotnet-sdk-service-api.md),
* Uma conta de armazenamento do Azure.

> [!NOTE]
> Também Aconselhamos os excelente [Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com/), pelo menos, durante a fase de desenvolvimento, dado que fornece uma IU fáceis de utilizar para interagir com o Storage do Azure.
> 
> 

## <a name="what-can-be-exported"></a>O que podem ser exportado?
O Mobile Engagement permite que os seus utilizadores recolher muitos tipos de dados e, por conseguinte, tem vários tipos de exportação adequada para estes tipos de dados diferentes.
Existem 2 tipos essenciais de exportação:

* Instantâneos: utilizada normalmente para exportar os dados que representa um Estado de e para o qual o Mobile Engagement não tem um histórico. Isto inclui etiquetas (app-info). o comentário de campanha de emissão ou tokens, por exemplo. Como um consequence estes tipos de exportação não estão relacionadas com uma data.
* histórico: este tipo de exportação é utilizado para os dados que acumula ao longo do tempo, tais como actividades ou eventos, por exemplo.

A tabela abaixo descreve exhaustively todas as possíveis exportações:

| Tipo de exportação | Tipo de dados | Descrição |
| --- | --- | --- |
| Instantâneo |Push |Gera uma exportação do comentário de campanhas Push numa base por deviceid/userid |
| Instantâneo |Etiqueta |Gera uma exportação das etiquetas (app-info) associados a cada dispositivos |
| Instantâneo |Dispositivo |Gera uma exportação da maioria dos dados sobre dispositivos como technicals (modelo, região, fuso horário,...), as etiquetas, a primeira vista,... |
| Instantâneo |Token |Gera uma exportação dos tokens válidos |
| Histórico |Atividade |Gera uma exportação de todas as atividades para cada dispositivo num determinado período de tempo |
| Histórico |Evento |Gera uma exportação de todas as atividades para cada dispositivo num determinado período de tempo |
| Histórico |Tarefa |Gera uma exportação todas as tarefas de cada dispositivo num determinado período de tempo |
| Histórico |Erro |Gera uma exportação de todos os erros para cada dispositivo num determinado período de tempo |

## <a name="how-does-it-work"></a>Como funciona?
Exportações são muito a executar as tarefas que pode produzir ficheiros de dados de grandes dimensões. Por esse motivo, não é possível invocar a devolver imediatamente um ficheiro para transferir.
Para exportar dados do Mobile Engagement, terá de criar um **tarefa exportar** através de API onde geralmente especificar:

* O tipo de exportação (instantâneo histórico ou),
* O tipo de dados
* O **contentor de armazenamento do Azure** (incluindo uma SAS válida com acesso de escrita) onde o resultado da exportação será escrito.
* Por exemplo, o parâmetro de URL do contentor de exemplo seria https://[StorageAccountName].blob.core.windows.net/[ContainerName]? [SASWritePermissionsToken]  

Eis um exemplo do mundo real. https://testazmeexport.blob.Core.Windows.NET/test1234azme?SV=2015-12-11&SS=b&SRT=SCO&SP=rwdlac&se=2016-12-17T04:59:26Z & st = 2016-12-16T20:59:26Z & spr = https & sig = KRF3aVWjp2NEJDzjlmoplmu0M9HHlLdkBWRPAFmw90Q % 3D

Tenha em atenção que poderá demorar alguns minutos para que a tarefa a ser iniciado e, em seguida, pode ser executado de alguns segundos para aplicações muito pequena a várias horas para aplicações com muitos utilizadores ou de atividade.

Depois da tarefa é criada, é possível verificar o respetivo estado para ver se ainda está em execução ou se foi concluída.

Depois da tarefa é concluída com êxito, o ficheiro de dados resultante está disponível no contentor de armazenamento fornecidos.

