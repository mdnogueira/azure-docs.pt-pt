---
title: "Como utilizar o serviço de correio eletrónico do SendGrid (.NET) | Microsoft Docs"
description: "Saiba como enviar correio eletrónico com o serviço de correio eletrónico do SendGrid no Azure. Exemplos de código escrito em c# e utilizam a API .NET."
services: 
documentationcenter: .net
author: thinkingserious
manager: erikre
editor: 
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.author: dx@sendgrid.com
ms.openlocfilehash: 14161a0747add43a99e301eacf700ab79c77c767
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Como enviar E-mails utilizando SendGrid com o Azure
## <a name="overview"></a>Descrição geral
Este guia demonstra como efetuar tarefas de programação comuns com o serviço de correio eletrónico do SendGrid no Azure. Os exemplos são escritos no C\# e suporta 1.3 padrão do .NET. Os cenários abrangidos incluem a construção de correio eletrónico, enviar correio eletrónico, adicionar anexos e ativar vários correio e definições de registo. Para obter mais informações sobre SendGrid e enviar correio eletrónico, consulte o [passos] [ Next steps] secção.

## <a name="what-is-the-sendgrid-email-service"></a>O que é o serviço de correio eletrónico do SendGrid?
SendGrid é um [serviço baseado na nuvem e-mail] que fornece fiável [entrega de correio eletrónico transacional], escalabilidade e a análise em tempo real, juntamente com APIs flexíveis que o facilitar a integração personalizados. Casos de utilização do SendGrid comuns incluem:

* Enviar automaticamente os recibos ou compra confirmações aos clientes.
* Administrar distribuição lista para enviar aos clientes fliers mensais e promoções.
* Recolher métricas em tempo real para coisas, como e-mail bloqueado e o engagement de cliente.
* Pedidos de cliente de reencaminhamento.
* Processamento de mensagens de correio eletrónico recebidas.

Para obter mais informações, visite [https://sendgrid.com](https://sendgrid.com) ou do SendGrid [c# biblioteca] [ sendgrid-csharp] repositório do GitHub.

## <a name="create-a-sendgrid-account"></a>Criar uma conta do SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Referência da biblioteca de classes do SendGrid .NET
O [pacote NuGet do SendGrid](https://www.nuget.org/packages/Sendgrid) é a forma mais fácil de obter a API do SendGrid e para configurar a sua aplicação com todas as dependências. É uma extensão do Visual Studio incluída com o Microsoft Visual Studio 2015 com NuGet e acima que torna mais fácil instalar e bibliotecas e ferramentas de atualização.

> [!NOTE]
> Para instalar NuGet, se estiver a executar uma versão do Visual Studio anterior ao Visual Studio 2015, visite [http://www.nuget.org](http://www.nuget.org)e clique em de **instalar NuGet** botão.
>
>

Para instalar o pacote NuGet do SendGrid na sua aplicação, efetue o seguinte:

1. Clique em **novo projeto** e selecione um **modelo**.

   ![Criar um novo projeto][create-new-project]
2. No **Explorador de soluções**, faça duplo clique **referências**, em seguida, clique em **gerir pacotes NuGet**.

   ![Pacote NuGet do SendGrid][SendGrid-NuGet-package]
3. Procurar **SendGrid** e selecione o **SendGrid** item da lista de resultados.
4. Selecione a versão estável mais recente do pacote Nuget na lista pendente versão para poder trabalhar com o modelo de objeto e APIs demonstrada neste artigo.

   ![Pacote de SendGrid][sendgrid-package]
5. Clique em **instalar** para concluir a instalação e, em seguida, feche esta caixa de diálogo.

Denomina-se a biblioteca de classe de .NET do SendGrid **SendGrid**. Esta secção inclui os seguintes espaços de nomes:

* **SendGrid** para comunicação com a API do SendGrid.
* **SendGrid.Helpers.Mail** para métodos de programa auxiliar criar facilmente SendGridMessage objetos que especificar como enviar e-mails.

Adicione as seguintes declarações de espaço de nomes de código na parte superior de qualquer ficheiro c# no qual pretende aceder programaticamente ao serviço de correio eletrónico de SendGrid.

    using SendGrid;
    using SendGrid.Helpers.Mail;

## <a name="how-to-create-an-email"></a>Como: criar uma mensagem de E-Mail
Utilize o **SendGridMessage** objeto para criar uma mensagem de correio eletrónico. Quando o objeto de mensagem for criado, pode definir as propriedades e métodos, incluindo o remetente do e-mail, se o destinatário de correio eletrónico e o assunto e corpo da mensagem de e-mail.

O exemplo seguinte demonstra como criar um objeto de correio eletrónico totalmente preenchidos:

    var msg = new SendGridMessage();

    msg.SetFrom(new EmailAddress("dx@example.com", "SendGrid DX Team"));

    var recipients = new List<EmailAddress>
    {
        new EmailAddress("jeff@example.com", "Jeff Smith"),
        new EmailAddress("anna@example.com", "Anna Lidman"),
        new EmailAddress("peter@example.com", "Peter Saddow")
    };
    msg.AddTos(recipients);

    msg.SetSubject("Testing the SendGrid C# Library");

    msg.AddContent(MimeType.Text, "Hello World plain text!");
    msg.AddContent(MimeType.Html, "<p>Hello World!</p>");

Para mais informações sobre todas as propriedades e métodos suportados pelo **SendGrid** tipo, consulte [sendgrid csharp] [ sendgrid-csharp] no GitHub.

## <a name="how-to-send-an-email"></a>Como: enviar uma mensagem de E-Mail
Depois de criar uma mensagem de e-mail, pode enviá-lo a utilizar a API do SendGrid. Em alternativa, pode utilizar [. NET incorporadas na biblioteca][NET-library].

Enviar correio eletrónico requer que forneça a chave de API do SendGrid. Se precisar de obter detalhes sobre como configurar as chaves de API, visite chaves de API do SendGrid [documentação][documentation].

Pode armazenar estas credenciais através do Portal do Azure, clicando em definições da aplicação e adicionar os pares chave/valor em definições de aplicação.

 ![Definições de aplicações do Azure][azure_app_settings]

 Em seguida, pode acedê-los da seguinte forma:

    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
    var client = new SendGridClient(apiKey);

Os exemplos seguintes mostram como enviar uma mensagem através da API Web.

    using System;
    using System.Threading.Tasks;
    using SendGrid;
    using SendGrid.Helpers.Mail;

    namespace Example
    {
        internal class Example
        {
            private static void Main()
            {
                Execute().Wait();
            }

            static async Task Execute()
            {
                var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
                var client = new SendGridClient(apiKey);
                var msg = new SendGridMessage()
                {
                    From = new EmailAddress("test@example.com", "DX Team"),
                    Subject = "Hello World from the SendGrid CSharp SDK!",
                    PlainTextContent = "Hello, Email!",
                    HtmlContent = "<strong>Hello, Email!</strong>"
                };
                msg.AddTo(new EmailAddress("test@example.com", "Test User"));
                var response = await client.SendEmailAsync(msg);
            }
        }
    }

## <a name="how-to-add-an-attachment"></a>Como: adicionar um anexo
Anexos podem ser adicionados a uma mensagem ao chamar o **AddAttachment** método e minimamente especificando o nome de ficheiro e codificado em Base64 de conteúdo pretendem ligar. Pode incluir múltiplos anexos, uma vez para cada ficheiro que pretende anexar ao chamar este método ou utilizando o **AddAttachments** método. O exemplo seguinte mostra como adicionar um anexo a uma mensagem:

    var banner2 = new Attachment()
    {
        Content = Convert.ToBase64String(raw_content),
        Type = "image/png",
        Filename = "banner2.png",
        Disposition = "inline",
        ContentId = "Banner 2"
    };
    msg.AddAttachment(banner2);

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Como: utilizar as definições de correio para permitir rodapés de página, registo e análise
SendGrid fornece funcionalidades de e-mail adicionais através da utilização de definições de correio e definições de controlo. Estas definições podem ser adicionadas a uma mensagem de e-mail para ativar funcionalidades específicas, tais como controlo de clique, do Google analytics, subscrição de controlo e assim sucessivamente. Para obter uma lista completa de aplicações, consulte o [definições documentação][settings-documentation].

As aplicações podem ser aplicadas a **SendGrid** utilizando métodos implementados como parte de mensagens de e-mail a **SendGridMessage** classe. Os exemplos seguintes demonstram rodapé e clique em filtros do registo:

Os exemplos seguintes demonstram rodapé e clique em filtros do registo:

### <a name="footer-settings"></a>Definições de rodapé
    msg.SetFooterSetting(
                         true,
                         "Some Footer HTML",
                         "<strong>Some Footer Text</strong>");

### <a name="click-tracking"></a>Clique em controlo
    msg.SetClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Como: utilizar os serviços do SendGrid adicionais
SendGrid oferece várias APIs e webhooks que pode utilizar para tirar partido das funcionalidades adicionais na sua aplicação do Azure. Para obter mais detalhes, consulte o [referência da API do SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu as noções básicas do serviço de E-Mail do SendGrid, siga estas ligações para saber mais.

* SendGrid C\# repositório de biblioteca: [sendgrid csharp][sendgrid-csharp]
* Documentação da API do SendGrid: <https://sendgrid.com/docs>

[Next steps]: #next-steps
[What is the SendGrid Email Service?]: #whatis
[Create a SendGrid Account]: #createaccount
[Reference the SendGrid .NET Class Library]: #reference
[How to: Create an Email]: #createemail
[How to: Send an Email]: #sendemail
[How to: Add an Attachment]: #addattachment
[How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
[How to: Use Additional SendGrid Services]: #useservices

[create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/new-project.png
[SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/reference.png
[sendgrid-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid-package.png
[azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/azure-app-settings.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
[App Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/api_v3.html
[NET-library]: https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html#-Using-NETs-Builtin-SMTP-Library
[documentation]: https://sendgrid.com/docs/Classroom/Send/api_keys.html
[settings-documentation]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html

[serviço baseado na nuvem e-mail]: https://sendgrid.com/solutions
[entrega de correio eletrónico transacional]: https://sendgrid.com/use-cases/transactional-email

