---
title: "Enlaces de funções SendGrid do Azure | Microsoft Docs"
description: "Referência de enlaces de funções SendGrid do Azure"
services: functions
documentationcenter: na
author: rachelappel
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: rachelap
ms.openlocfilehash: 4cdafbe05e29d8b483c6b0e1daf41a36583d7b5e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-sendgrid-bindings"></a>Enlaces de funções SendGrid do Azure

Este artigo explica como configurar e trabalhar com enlaces SendGrid nas funções do Azure. Com SendGrid, pode utilizar as funções do Azure para enviar mensagens de e-mail personalizadas através de programação.

Este artigo é informações de referência para programadores das funções do Azure. Se estiver familiarizado com as funções do Azure, comece com os seguintes recursos:

[Criar a sua primeira função do Azure](functions-create-first-azure-function.md). 
[C#](functions-reference-csharp.md), [F #](functions-reference-fsharp.md), ou [nó](functions-reference-node.md) referências de programação.

## <a name="functionjson-for-sendgrid-bindings"></a>Function.JSON para SendGrid enlaces

As funções do Azure fornece um enlace de saída para SendGrid. A saída do SendGrid enlace permite criar e enviar correio eletrónico através de programação. 

O enlace do SendGrid suporta as seguintes propriedades:

|Propriedade  |Descrição  |
|---------|---------|
|**nome**| Necessário - o nome da variável utilizado no código de função para o corpo do pedido ou o pedido. Este valor é ```$return``` quando existe apenas um valor de retorno. |
|**tipo**| Necessário - tem de ser definido como `sendGrid`.|
|**direção**| Necessário - tem de ser definido como `out`.|
|**apiKey**| Necessário - tem de ser definido com o nome da sua chave de API armazenado nas definições de aplicação a aplicação de função. |
|**para**| endereço de e-mail do destinatário. |
|**do**| endereço de correio eletrónico do remetente. |
|**requerente**| o assunto do e-mail. |
|**texto**| o conteúdo de e-mail. |

Exemplo de **function.json**:

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

> [!NOTE]
> As funções do Azure armazena as informações de ligação e chaves de API como as definições de aplicação para que estes não são verificados para o repositório de controlo de origem. Esta ação salvaguarda as suas informações confidenciais.
>
>

## <a name="c-example-of-the-sendgrid-output-binding"></a>Enlace de saída de exemplo c# do SendGrid

```csharp
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static Mail Run(TraceWriter log, string input, out Mail message)
{
     message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    personalization.AddTo(new Email("recipient@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

## <a name="node-example-of-the-sendgrid-output-binding"></a>Exemplo de nó do SendGrid vínculo de saída

```javascript
module.exports = function (context, input) {    
    var message = {
         "personalizations": [ { "to": [ { "email": "sample@sample.com" } ] } ],
        from: { email: "sender@contoso.com" },        
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};

```

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre outras enlaces e acionadores das funções do Azure, consulte 
- [Azure funções acionadores e enlaces de referência para programadores](functions-triggers-bindings.md)

- [Melhores práticas para as funções do Azure](functions-best-practices.md) lista algumas melhores práticas para utilizar quando criar as funções do Azure.

- [Referência de programadores das funções do Azure](functions-reference.md) referência de programador para funções de codificação e definição de acionadores e enlaces.
