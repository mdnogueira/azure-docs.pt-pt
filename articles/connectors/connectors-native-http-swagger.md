---
title: Chamar pontos finais REST com HTTP + Swagger connector para Azure Logic Apps | Microsoft Docs
description: "Ligar a pontos finais REST a partir das logic apps através de Swagger com o HTTP + Swagger connector"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: eccfd87c-c5fe-4cf7-b564-9752775fd667
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: 3e9229d94e96aad7b769d0e55d208d856e3b80bc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-http--swagger-action"></a>Começar a utilizar o HTTP + Swagger ação

Pode criar um conector de primeira classe para qualquer ponto final REST através de um [documento Swagger](https://swagger.io) ao utilizar o HTTP + Swagger ação no seu fluxo de trabalho de aplicação lógica. Também pode expandir as logic apps para chamar a qualquer ponto final REST com uma experiência primeira classe do Designer de aplicação lógica.

Para saber como criar aplicações lógicas com conectores, consulte [criar uma nova aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>Utilizar HTTP + Swagger como um acionador ou uma ação

O HTTP + Swagger acionar e ação funcionar da mesma, como o [ação HTTP](connectors-native-http.md) mas fornece uma melhor experiência no Designer de aplicação lógica resultariam da exposição a estrutura de API e saídas do [metadados do Swagger](https://swagger.io). Também pode utilizar o HTTP + Swagger connector como um acionador. Se pretender implementar um acionador de consulta, seguem o padrão de consulta que é descrito no [criar APIs personalizadas para chamar outros APIs, serviços e sistemas a partir das logic apps](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

Saiba mais sobre [logic app acionadores e ações](connectors-overview.md).

Eis um exemplo de como a utilização de HTTP + Swagger operação como uma ação num fluxo de trabalho numa aplicação lógica.

1. Selecione o **novo passo** botão.
2. Selecione **adicionar uma ação**.
3. Na caixa de pesquisa de ação, escreva **swagger** a lista de HTTP + Swagger ação.
   
    ![Selecione HTTP + Swagger ação](./media/connectors-native-http-swagger/using-action-1.png)
4. Escreva o URL para um documento Swagger:
   
   * Para trabalhar a partir do Designer de aplicação lógica, o URL tem de ser um ponto final de HTTPS e tiver ativado o CORS.
   * Se o documento Swagger não cumpre este requisito, pode utilizar [Storage do Azure com a CORS ativada](#hosting-swagger-from-storage) para armazenar o documento.
5. Clique em **seguinte** para ler e compor do documento Swagger.
6. Adicione quaisquer parâmetros que são necessários para a chamada HTTP.
   
    ![Ação de HTTP concluída](./media/connectors-native-http-swagger/using-action-2.png)
7. Para guardar e publicar a sua aplicação lógica, clique em **guardar** na barra de ferramentas estruturador.

### <a name="host-swagger-from-azure-storage"></a>Anfitrião de Swagger do armazenamento do Azure
Pode querer fazer referência a um documento Swagger que não fique alojada ou que não cumprem os requisitos de várias origens para o estruturador e segurança. Para resolver este problema, pode armazenar o documento Swagger no armazenamento do Azure e ativar o CORS para referenciar o documento.  

Eis os passos para criar, configurar e armazenar documentos Swagger no armazenamento do Azure:

1. [Criar uma conta de armazenamento do Azure com o Blob storage do Azure](../storage/common/storage-create-storage-account.md). Para efetuar este passo, defina as permissões para **acesso público**.

2. Ative a CORS no blob. 

   Para configurar automaticamente esta definição, pode utilizar [este script do PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

3. Carregue o ficheiro Swagger para o blob. 

   Pode efetuar este passo do [portal do Azure](https://portal.azure.com) ou a partir de uma ferramenta como o [Explorador de armazenamento do Azure](http://storageexplorer.com/).

4. Referência a uma ligação HTTPS para o documento no Blob storage do Azure. 

   A ligação utiliza este formato:

   `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`

## <a name="technical-details"></a>Detalhes técnicos
Seguem-se os detalhes de acionadores e ações que este HTTP + Swagger connector suporta.

## <a name="http--swagger-triggers"></a>HTTP + Swagger acionadores
Um acionador é um evento que pode ser utilizado para iniciar o fluxo de trabalho que está definido uma aplicação lógica. [Saiba mais sobre acionadores.](connectors-overview.md) O HTTP + Swagger connector tem um acionador.

| Acionador | Descrição |
| --- | --- |
| HTTP + Swagger |Efetuar uma chamada HTTP e devolver o conteúdo de resposta |

## <a name="http--swagger-actions"></a>HTTP + Swagger ações
Uma ação é uma operação que é efetuada pelo fluxo de trabalho que está definido uma aplicação lógica. [Saiba mais sobre as ações.](connectors-overview.md) O HTTP + Swagger connector tem uma ação possíveis.

| Ação | Descrição |
| --- | --- |
| HTTP + Swagger |Efetuar uma chamada HTTP e devolver o conteúdo de resposta |

### <a name="action-details"></a>Detalhes de ação
O HTTP + Swagger connector vem com uma ação possíveis. Segue-se informações sobre cada um das ações, os campos de entrada necessários e opcionais e os detalhes de resultado correspondente que estão associados a respetiva utilização.

#### <a name="http--swagger"></a>HTTP + Swagger
Efetue um pedido de saída HTTP com a assistência de metadados Swagger.
Um asterisco (*) significa que um campo obrigatório.

| Nome a apresentar | Nome da propriedade | Descrição |
| --- | --- | --- |
| Método * |Método |Verbo HTTP a utilizar. |
| URI * |URI |URI do pedido de HTTP. |
| Cabeçalhos |Cabeçalhos |Um objeto JSON de cabeçalhos de HTTP para incluir. |
| Corpo |Corpo |O corpo do pedido HTTP. |
| Autenticação |Autenticação |Autenticação a utilizar para o pedido. Para obter mais informações, consulte o [conetor HTTP](connectors-native-http.md#authentication). |

**Detalhes de saída**

Resposta de HTTP

| Nome da propriedade | Tipo de dados | Descrição |
| --- | --- | --- |
| Cabeçalhos |objeto |Cabeçalhos de resposta |
| Corpo |objeto |Objeto de resposta |
| Código de estado |Int |Código de estado HTTP |

### <a name="http-responses"></a>Respostas de HTTP
Quando efetuar chamadas para várias ações, poderá obter determinadas respostas. Segue-se uma tabela que descreve as respostas correspondentes e descrições.

| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 202 |Aceite |
| 400 |Pedido incorreto |
| 401 |Não autorizado |
| 403 |Proibido |
| 404 |Não foi encontrado |
| 500 |Erro de servidor interno. Ocorreu um erro desconhecido. |

- - -
## <a name="next-steps"></a>Passos seguintes

* [Criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md)
* [Localizar outros conectores](apis-list.md)