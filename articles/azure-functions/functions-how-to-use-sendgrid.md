---
title: "Como utilizar SendGrid nas funções do Azure | Microsoft Docs"
description: "Mostra como utilizar SendGrid nas funções do Azure"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/31/2017
ms.author: glenga
ms.openlocfilehash: 444e06ff24ea7f909a24d482aba26f890040d980
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-use-sendgrid-in-azure-functions"></a>Como utilizar SendGrid nas funções do Azure

## <a name="sendgrid-overview"></a>Descrição geral do SendGrid

Funções do Azure suporta SendGrid enlaces para ativar as suas funções enviar mensagens de correio eletrónico com algumas linhas de código e uma conta do SendGrid de saída.

Para utilizar a API do SendGrid numa função do Azure, tem de ter um [SendGrid conta](http://SendGrid.com). Além disso, tem de ter uma chave de API do SendGrid. Inicie sessão na sua conta do SendGrid e clique em **definições** , em seguida, **chave de API** para gerar uma chave de API. Manter esta chave disponível como utilizá-lo um passo futuros.

Agora está pronto para criar uma aplicação de função do Azure.

## <a name="create-an-azure-function-app"></a>Criar uma aplicação de Funções do Azure 

As aplicações de função do Azure são contentores para um ou mais funções do Azure. As funções do Azure são apenas que - uma função. Cada função do Azure está associada a um acionador, que é um evento que faz com que a função a executar.
Cada função pode conter qualquer número de entrada ou saída de enlaces. Enlaces são serviços que pode utilizar uma função. SendGrid é um enlace de saída que pode utilizar para enviar correio eletrónico. 

1. Inicie sessão no portal do Azure e [criar uma aplicação de função do Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) ou abrir uma aplicação de função existente. 
2. Crie uma função do Azure. Para manter simples, escolha um acionador manual e c#. 

 ![Criar uma função do Azure](./media/functions-how-to-use-sendgrid/functions-new-function-manual-trigger-page.png)

## <a name="configure-sendgrid-for-use-in-an-azure-function-app"></a>Configurar SendGrid para utilização na aplicação de uma função do Azure

Tem de armazenar a chave de API do SendGrid como uma definição de aplicação para utilizá-lo de uma função. O campo de ApiKey não é a chave de API do SendGrid real, mas uma aplicação definição definir que representa a sua chave de API real. Armazenar a chave desta forma é para segurança, uma vez que está separado do qualquer código ou ficheiros que podem ser verificados no controlo de código fonte.

- Criar um **AppSettings** chave na sua aplicação de função **definições da aplicação**.

 ![Criar uma função do Azure](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-api-key-app-settings.png)

## <a name="configure-sendgrid-output-bindings"></a>Configure os enlaces de saída do SendGrid

SendGrid está disponível como um Azure vínculo de saída da função. Para criar um SendGrid vínculo de saída:

1. Vá para o **integrar** separador da função no portal do Azure.
2. Clique em **nova saída** criar um SendGrid vínculo de saída.
3. Preencha o **chave de API** e **nome do parâmetro mensagem** propriedades. Se quiser, pode introduzir as outras propriedades agora ou code-los em vez disso. Estas definições podem ser utilizadas como predefinições.

 ![Configure os enlaces de saída do SendGrid](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-output-bindings.png)

Adicionar um enlace a uma função cria um ficheiro chamado **function.json** na pasta da função. Este ficheiro contém as mesmas informações que vê na função do Azure **integrar** separador, mas no Json formatar. Definir o **ApiKey**, **mensagem**, e **de** campos criar as seguintes entradas no **function.json** ficheiro: 

```json
{
  "bindings": [    
    {
      "type": "sendGrid",
      "name": "message",
      "apiKey": "SendGridKey",
      "direction": "out",
      "from": "azure@contoso.com"
    }
  ],
  "disabled": false
}
```

Se preferir, pode modificar este ficheiro sozinho diretamente.

Agora que já ter criado e configurado a aplicação de função e a função, pode escrever o código para enviar uma mensagem de e-mail.

## <a name="write-code-that-creates-and-sends-email"></a>Escrever código que cria e envia correio eletrónico

A API do SendGrid contém todos os comandos que precisa para criar e enviar uma mensagem de e-mail.  

- Substitua o código na função com o seguinte código:

```cs
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static void Run(TraceWriter log, string input, out Mail message)
{
    message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    // change to email of recipient
    personalization.AddTo(new Email("MoreEmailPlease@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

Tenha em atenção de que a primeira linha contém o ```#r``` diretiva que referencia a assemblagem do SendGrid. Depois disso, pode utilizar um ```using``` instrução mais facilmente aceder aos objetos esse espaço de nomes. No código, criar instâncias do ```Mail```, ```Personalization```, e ```Content``` objetos a partir da API do SendGrid que compõem o e-mail. Quando voltar a mensagem, o SendGrid disponibiliza-lo. 

A assinatura da função também contém um extra de parâmetro de tipo out ```Mail``` denominado ```message```. Ambos entrada e saída enlaces rápidas pessoalmente como parâmetros de função no código. 

2. Testar o seu código clicando **teste** e introduzir uma mensagem para o **corpo do pedido** campo, em seguida, clicando a **executar** botão.

 ![Testar o seu código](./media/functions-how-to-use-sendgrid/functions-develop-test-sendgrid.png)

3. Verifique o correio eletrónico para verificar que SendGrid enviar o e-mail. Deverá passar para o endereço no código do passo 1 e contém a mensagem do **corpo do pedido**.

## <a name="next-steps"></a>Passos seguintes
Este artigo foi demonstrado como utilizar o serviço do SendGrid para criar e enviar correio eletrónico. Para saber mais sobre como utilizar as Funções de Azure nas suas aplicações, veja os tópicos seguintes: 

- [Melhores práticas para as funções do Azure](functions-best-practices.md) lista algumas melhores práticas para utilizar quando criar as funções do Azure.

- [Referência de programadores das funções do Azure](functions-reference.md) referência de programador para funções de codificação e definição de acionadores e enlaces.

- [Testar as funções do Azure](functions-test-a-function.md) descreve várias ferramentas e técnicas para testar as suas funções.