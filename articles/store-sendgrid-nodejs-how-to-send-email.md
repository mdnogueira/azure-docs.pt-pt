---
title: "Como utilizar o serviço de correio eletrónico do SendGrid (Node.js) | Microsoft Docs"
description: "Saiba como enviar correio eletrónico com o serviço de correio eletrónico do SendGrid no Azure. Exemplos de código escrito utilizando a API do Node.js."
services: 
documentationcenter: nodejs
author: erikre
manager: wpickett
editor: 
ms.assetid: cac444b4-26b0-45ea-9c3d-eca28d57dacb
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 01/05/2016
ms.author: erikre
ms.openlocfilehash: 327cea3a24cc47a9cc463b37cc2346ebc475ef7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>Como enviar E-mails utilizando a SendGrid do Node.js
Este guia demonstra como efetuar tarefas de programação comuns com o serviço de correio eletrónico do SendGrid no Azure. Os exemplos são escritos utilizando a API do Node.js. Os cenários abrangidos incluem **construir e-mail**, **enviar correio eletrónico**, **adicionar anexos**, **utilizando filtros**e **atualizar propriedades**. Para obter mais informações sobre SendGrid e enviar correio eletrónico, consulte o [passos](#next-steps) secção.

## <a name="what-is-the-sendgrid-email-service"></a>O que é o serviço de correio eletrónico do SendGrid?
SendGrid é um [serviço baseado na nuvem e-mail] que fornece fiável [entrega de correio eletrónico transacional], escalabilidade e a análise em tempo real, juntamente com APIs flexíveis que o facilitar a integração personalizados. Cenários comuns de utilização do SendGrid incluem:

* Enviar automaticamente os recibos aos clientes
* Administrar distribuição lista para enviar aos clientes i-fliers mensais e ofertas especiais
* Recolher métricas em tempo real para coisas como bloqueado por correio eletrónico e a capacidade de resposta do cliente
* Gerar relatórios para ajudar a identificar tendências
* Pedidos de cliente de reencaminhamento
* Notificações de correio eletrónico da sua aplicação

Para obter mais informações, consulte [https://sendgrid.com](https://sendgrid.com).

## <a name="create-a-sendgrid-account"></a>Criar uma conta do SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>O módulo de Node.js SendGrid de referência
O módulo do SendGrid para Node.js pode ser instalado através do Gestor de pacote do nó (npm) utilizando o seguinte comando:

    npm install sendgrid

Após a instalação, pode exigir o módulo na sua aplicação utilizando o seguinte código:

    var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);

O módulo do SendGrid exporta o **SendGrid** e **E-Mail** funções.
**SendGrid** é responsável pelo envio de correio eletrónico através da Web API, enquanto **E-Mail** encapsula uma mensagem de e-mail.

## <a name="how-to-create-an-email"></a>Como: criar uma mensagem de E-Mail
Criar uma mensagem de e-mail utilizando o módulo do SendGrid envolve criar primeiro uma mensagem de e-mail utilizando a função de E-Mail e, em seguida, enviando-a utilizando a função do SendGrid. Segue-se um exemplo de como criar uma nova mensagem utilizando a função de E-Mail:

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

Também pode especificar uma mensagem HTML para clientes que suportam definindo a propriedade de html. Por exemplo:

    html: This is a sample <b>HTML<b> email message.

Definir propriedades de texto e html fornece correto contingência para conteúdo de texto para clientes que não suportem a mensagens HTML.

Para obter mais informações sobre todas as propriedades suportadas pela função de correio eletrónico, consulte [sendgrid nodejs][sendgrid-nodejs].

## <a name="how-to-send-an-email"></a>Como: enviar uma mensagem de E-Mail
Depois de criar uma mensagem de e-mail utilizando a função de E-Mail, pode enviá-la utilizando a API Web fornecidos pelo SendGrid. 

### <a name="web-api"></a>API Web
    sendgrid.send(email, function(err, json){
        if(err) { return console.error(err); }
        console.log(json);
    });

> [!NOTE]
> Enquanto os exemplos acima mostram transmitir numa função de objeto e a chamada de retorno de e-mail, pode também diretamente de invocar a função de envio especificando propriedades de correio eletrónico diretamente. Por exemplo:  
> 
> `````
> sendgrid.send({
> to: 'john@contoso.com',
> from: 'anna@contoso.com',
> subject: 'test mail',
> text: 'This is a sample email message.'
> });
> `````
> 
> 

## <a name="how-to-add-an-attachment"></a>Como: adicionar um anexo
Anexos podem ser adicionados a uma mensagem, especificando os nomes de ficheiro e path(s) no **ficheiros** propriedade. O exemplo seguinte demonstra o envio de um anexo:

    sendgrid.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        files: [
            {
                filename:     '',           // required only if file.content is used.
                contentType:  '',           // optional
                cid:          '',           // optional, used to specify cid for inline content
                path:         '',           //
                url:          '',           // == One of these three options is required
                content:      ('' | Buffer) //
            }
        ],
    });

> [!NOTE]
> Ao utilizar o **ficheiros** propriedade, o ficheiro tem de estar acessível através de [fs.readFile](http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Se o ficheiro que pretende anexar está alojado no armazenamento do Azure, como um contentor de BLOBs, terá primeiro de copiar o ficheiro para o armazenamento local ou a uma unidade do Azure antes de pode ser enviado como um anexo utilizando o **ficheiros** propriedade.
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>Como: utilizar filtros para ativar rodapés de página e controlo
SendGrid fornece funcionalidades de e-mail adicionais através da utilização de filtros. Estas são as definições que podem ser adicionadas a uma mensagem de e-mail para ativar a funcionalidade específica, como ativar o controlo de clique, do Google analytics, subscrição de controlo e assim sucessivamente. Para obter uma lista completa de filtros, consulte [definições de filtro][Filter Settings].

Os filtros podem ser aplicados a uma mensagem utilizando o **filtros** propriedade.
Cada filtro especificado por um hash que contém definições específicas do filtro.
Os exemplos seguintes demonstram rodapé e clique em filtros do registo:

### <a name="footer"></a>Rodapé
    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

    email.setFilters({
        'footer': {
            'settings': {
                'enable': 1,
                'text/plain': 'This is a text footer.'
            }
        }
    });

    sendgrid.send(email);

### <a name="click-tracking"></a>Clique em controlo
    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

    email.setFilters({
        'clicktrack': {
            'settings': {
                'enable': 1
            }
        }
    });

    sendgrid.send(email);

## <a name="how-to-update-email-properties"></a>Como: atualizar as propriedades de E-Mail
Algumas propriedades de correio eletrónico podem ser substituídas utilizando  **definir*propriedade** * ou estar anexados utilizando  **adicionar*propriedade** *. Por exemplo, pode adicionar destinatários adicionais utilizando

    email.addTo('jeff@contoso.com');

ou definir um filtro com o

    email.addFilter('footer', 'enable', 1);
    email.addFilter('footer', 'text/html', '<strong>boo</strong>');

Para obter mais informações, consulte [sendgrid nodejs][sendgrid-nodejs].

## <a name="how-to-use-additional-sendgrid-services"></a>Como: utilizar os serviços do SendGrid adicionais
SendGrid oferece APIs baseadas na web que pode utilizar para tirar partido das funcionalidades adicionais do SendGrid da sua aplicação do Azure. Para mais informações, consulte o [documentação da API do SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu as noções básicas do serviço de E-Mail do SendGrid, siga estas ligações para saber mais.

* Repositório de módulo do SendGrid Node.js: [sendgrid nodejs][sendgrid-nodejs]
* Documentação da API do SendGrid: <https://sendgrid.com/docs>
* Oferta especial de SendGrid para clientes do Azure: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[serviço baseado na nuvem e-mail]: https://sendgrid.com/email-solutions
[entrega de correio eletrónico transacional]: https://sendgrid.com/transactional-email
