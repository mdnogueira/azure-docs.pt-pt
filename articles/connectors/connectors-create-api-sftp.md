---
title: Saiba como utilizar o conector SFTP nas suas logic apps | Microsoft Docs
description: "Crie aplicações lógicas com o App service do Azure. Ligar à API do SFTP para enviar e receber ficheiros. Pode realizar várias operações, tais como criar, atualizar, obterem ou eliminar ficheiros."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/20/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 31253d8daee1581167a96a20ba8ad529a04b3e92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-sftp-connector"></a>Começar a utilizar o conector SFTP
Utilize o conector SFTP para aceder a uma conta SFTP para enviar e receber ficheiros. Pode realizar várias operações, tais como criar, atualizar, obterem ou eliminar ficheiros.  

Para utilizar [qualquer conector](apis-list.md), terá primeiro de criar uma aplicação lógica. Pode começar a utilizar pelo [criar uma aplicação lógica agora](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-sftp"></a>Ligar ao SFTP
Antes da aplicação lógica pode aceder a qualquer serviço, terá primeiro de criar um *ligação* ao serviço. A [ligação](connectors-overview.md) fornece conectividade entre uma aplicação lógica e outro serviço.  

### <a name="create-a-connection-to-sftp"></a>Criar uma ligação ao SFTP
> [!INCLUDE [Steps to create a connection to SFTP](../../includes/connectors-create-api-sftp.md)]
> 
> 

## <a name="use-an-sftp-trigger"></a>Utilizar um acionador SFTP
Um acionador é um evento que pode ser utilizado para iniciar o fluxo de trabalho definido numa aplicação lógica. [Saiba mais sobre acionadores](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

Neste exemplo, o **SFTP - quando um ficheiro é adicionado ou modificado** acionador é utilizado para iniciar um fluxo de trabalho de aplicação lógica, quando um ficheiro é adicionado à ou modificado num servidor SFTP. Também pode adiciona uma condição que verifica o conteúdo do ficheiro novo ou modificado e efetua uma decisão para extrair o ficheiro se os respetivos conteúdos indicarem que deve ser extraído antes de utilizar o conteúdo. Por fim, adicionar uma ação para extrair os conteúdos de um ficheiro e coloque o conteúdo extraído numa pasta no servidor de SFTP. 

Um exemplo de empresa, pode utilizar este acionador para monitorizar uma pasta SFTP para novos ficheiros que representam as ordens de cliente.  Pode, em seguida, utilizar uma ação de conector SFTP, tais como **obter o conteúdo do ficheiro**, para obter os conteúdos da ordem de processamento adicional e de armazenamento de uma base de dados das ordens.

> [!INCLUDE [Steps to create an SFTP trigger](../../includes/connectors-create-api-sftp-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>Adicionar uma condição
> [!INCLUDE [Steps to add a condition](../../includes/connectors-create-api-sftp-condition.md)]
> 
> 

## <a name="use-an-sftp-action"></a>Utilizar uma ação de SFTP
Uma ação é uma operação levada a cabo pelo fluxo de trabalho definido numa aplicação lógica. [Saiba mais sobre as ações](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!INCLUDE [Steps to create an SFTP action](../../includes/connectors-create-api-sftp-action.md)]
> 
> 

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver todos os acionadores e ações definidas no swagger e consulte também os limites no [detalhes do conector](/connectors/sftpconnector/).

## <a name="more-connectors"></a>Mais conectores
Volte à [lista APIs](apis-list.md).