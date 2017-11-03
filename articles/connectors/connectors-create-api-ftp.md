---
title: Saiba como utilizar o conector FTP nas logic apps | Microsoft Docs
description: "Crie aplicações lógicas com o App service do Azure. Ligar ao servidor FTP para gerir os seus ficheiros. Pode efetuar várias ações, como o carregamento, atualizar, obter e eliminar ficheiros no servidor de FTP."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: d83c55fe-eb59-4b7b-a5ec-afac5c772616
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/22/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 61bfbedfd4f1e84b6976099323a32f3a720634c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-ftp-connector"></a>Começar a utilizar o conector FTP
Utilize o conector FTP para monitorizar, gerir e criar ficheiros num servidor FTP. 

Para utilizar [qualquer conector](apis-list.md), terá primeiro de criar uma aplicação lógica. Pode começar a utilizar pelo [criar uma aplicação lógica agora](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-ftp"></a>Ligar para FTP
Antes da aplicação lógica pode aceder a qualquer serviço, terá primeiro de criar um *ligação* ao serviço. A [ligação](connectors-overview.md) fornece conectividade entre uma aplicação lógica e outro serviço.  

### <a name="create-a-connection-to-ftp"></a>Criar uma ligação para FTP
> [!INCLUDE [Steps to create a connection to FTP](../../includes/connectors-create-api-ftp.md)]
> 
> 

## <a name="use-a-ftp-trigger"></a>Utilizar um acionador FTP
Um acionador é um evento que pode ser utilizado para iniciar o fluxo de trabalho definido numa aplicação lógica. [Saiba mais sobre acionadores](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!IMPORTANT]
> O conector FTP requer um servidor FTP que está acessível a partir da Internet e está configurado para funcionar com o modo passivo. Além disso, o conector FTP é **não é compatível com FTPS implícita (FTP através de SSL)**. O conector FTP só suporta FTPS explícita (FTP através de SSL).  
> 
> 

Neste exemplo, posso irá mostrar-lhe como utilizar o **FTP - quando um ficheiro é adicionado ou modificado** acionar para iniciar um fluxo de trabalho de aplicação lógica, quando um ficheiro é adicionado ou modificado no servidor de FTP. Um exemplo de empresa, pode utilizar este acionador para monitorizar uma pasta FTP para os novos ficheiros que representam as ordens de clientes.  Pode, em seguida, utilizar uma ação de conector FTP, tais como **obter o conteúdo do ficheiro** ao obter o conteúdo da ordem de processamento adicional e de armazenamento na sua base de dados das ordens.

1. Introduza *ftp* na caixa de pesquisa no designer de aplicações lógicas, em seguida, selecione o **FTP - quando um ficheiro é adicionado ou modificado** acionador   
   ![Imagem de Acionador FTP 1](./media/connectors-create-api-ftp/ftp-trigger-1.png)  
   O **quando um ficheiro é adicionado ou modificado** controlo abre-se  
   ![Imagem de Acionador FTP 2](./media/connectors-create-api-ftp/ftp-trigger-2.png)  
2. Selecione o **...**  localizado no lado direito de controlo. Esta ação abre o controlo de selecionador de pasta  
   ![Imagem de Acionador FTP 3](./media/connectors-create-api-ftp/ftp-trigger-3.png)  
3. Selecione o  **>**  (seta para a direita) e procurar para localizar a pasta que pretende monitorizar os ficheiros novos ou modificados. Selecione a pasta e repare que a pasta é apresentada no **pasta** controlo.  
   ![Imagem de Acionador FTP 4](./media/connectors-create-api-ftp/ftp-trigger-4.png)   

Neste momento, a aplicação lógica foi configurada com um acionador que irá iniciar a execução dos outros acionadores e ações no fluxo de trabalho quando um ficheiro é modificado ou criado na pasta de FTP específica. 

> [!NOTE]
> Para uma aplicação lógica que seja funcional, tem de conter pelo menos um acionador e uma ação. Siga os passos na secção seguinte para adicionar uma ação.  
> 
> 

## <a name="use-a-ftp-action"></a>Utilizar uma ação de FTP
Uma ação é uma operação levada a cabo pelo fluxo de trabalho definido numa aplicação lógica. [Saiba mais sobre as ações](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

Agora que adicionou um acionador, siga estes passos para adicionar uma ação que irá obter os conteúdos do ficheiro novo ou modificado encontrado pelo acionador.    

1. Selecione **+ novo passo** para adicionar a ação para obter os conteúdos do ficheiro no servidor de FTP  
2. Selecione o **adicionar uma ação** ligação.  
   ![Imagem da ação de FTP 1](./media/connectors-create-api-ftp/ftp-action-1.png)  
3. Introduza *FTP* para procurar em todas as ações relacionadas com o FTP.
4. Selecione **FTP - obter o conteúdo do ficheiro** como a ação a tomar quando é encontrado um ficheiro de novo ou modificado na pasta de FTP.      
   ![Imagem da ação de FTP 2](./media/connectors-create-api-ftp/ftp-action-2.png)  
   O **obter o conteúdo do ficheiro** controlar é aberto. **Tenha em atenção**: lhe-á para autorizar a aplicação lógica para aceder à sua conta de servidor FTP, se não o fez, anteriormente.  
   ![Imagem da ação de FTP 3](./media/connectors-create-api-ftp/ftp-action-3.png)   
5. Selecione o **ficheiro** controlo (o espaço em branco localizado abaixo **ficheiro***). Aqui, pode utilizar qualquer uma das várias propriedades do ficheiro de novo ou modificado encontrado no servidor de FTP.  
6. Selecione o **conteúdo do ficheiro** opção.  
   ![Imagem da ação de FTP 4](./media/connectors-create-api-ftp/ftp-action-4.png)   
7. O controlo é atualizado, indicando que o **FTP - obter o conteúdo do ficheiro** ação obterá o *conteúdo do ficheiro* do ficheiro novo ou modificado no servidor de FTP.      
   ![Imagem da ação de FTP 5](./media/connectors-create-api-ftp/ftp-action-5.png)     
8. Guardar o seu trabalho, em seguida, adicione um ficheiro para a pasta FTP para testar o seu fluxo de trabalho.    

Neste momento, a aplicação lógica foi configurada com um acionador para monitorizar uma pasta num servidor FTP e iniciar o fluxo de trabalho quando encontra um novo ficheiro ou um ficheiro modificado no servidor de FTP. 

A aplicação lógica também foi configurada com uma ação para obter os conteúdos do ficheiro novo ou modificado.

Agora pode adicionar outra ação, tal como o [do SQL Server - Inserir linha](connectors-create-api-sqlazure.md) ação para inserir o conteúdo do ficheiro novo ou modificado para uma tabela de base de dados do SQL Server.  

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver todos os acionadores e ações definidas no swagger e consulte também os limites no [detalhes do conector](/connectors/ftpconnector/). 

## <a name="next-steps"></a>Passos Seguintes
[Criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md)

