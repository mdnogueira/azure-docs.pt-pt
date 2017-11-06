---
title: Como utilizar Twilio de voz e SMS (.NET) | Microsoft Docs
description: "Saiba como efetuar uma chamada telefónica e enviar uma mensagem SMS com o serviço de API do Twilio no Azure. Exemplos de código escrito no .NET."
services: 
documentationcenter: .net
author: devinrader
manager: twilio
editor: 
ms.assetid: 74d4f3c9-f1cb-4968-b744-36b32cd0e834
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/24/2015
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: 1442e3af26ae87e645cf207228ed1197b2afdd4d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Como utilizar Twilio de voz e funcionalidades SMS a partir do Azure
Este guia demonstra como efetuar tarefas de programação comuns com o serviço de API do Twilio no Azure. Os cenários abrangidos incluem a efetuar uma chamada telefónica e enviar uma mensagem de serviço de mensagens curto (SMS). Para obter mais informações sobre Twilio e a utilização de voz e SMS nas suas aplicações, consulte o [passos](#NextSteps) secção.

## <a id="WhatIs"></a>O que é Twilio?
Twilio está a ligar ao futuro da comunicações de empresa, permitindo aos programadores incorporar de voz, VoIP e de mensagens em aplicações. Estes virtualizar todos os infraestrutura necessária num ambiente baseado na nuvem, global, expor-lo através da plataforma de comunicações API do Twilio. As aplicações são simples criar e dimensionável. Desfrute de mais flexibilidade com pay as you go preços e beneficiar da fiabilidade da nuvem.

**Voz do Twilio** permite que as aplicações tornar e receber chamadas telefónicas. **Twilio SMS** permite às aplicações enviar e receber mensagens SMS. **Cliente do Twilio** permite-lhe efetuar chamadas de VoIP de qualquer telefone, o tablet ou o browser e suporta WebRTC.

## <a id="Pricing"></a>Preços do Twilio e ofertas especiais
Clientes do Azure recebem um [oferta especial](http://www.twilio.com/azure): complimentary $10 do Twilio crédito ao atualizar a sua conta do Twilio. Este crédito do Twilio podem ser aplicado a qualquer utilização do Twilio (crédito $10 equivalente ao máximo de 1000 mensagens SMS a enviar ou receber até 1000 entrados minutos de voz, consoante a localização do destino de número e a mensagem ou a chamada da telefone). Resgatar este crédito do Twilio e começar a utilizar em [ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

Twilio é um serviço pay as you go. Existem não existem taxas de configuração e pode fechar a sua conta em qualquer altura. Pode encontrar mais detalhes em [preços do Twilio](http://www.twilio.com/voice/pricing).

## <a id="Concepts"></a>Conceitos
A API do Twilio é uma API RESTful que fornece uma funcionalidade SMS de voz e de aplicações. Bibliotecas de cliente estão disponíveis em vários idiomas; Para obter uma lista, consulte [Twilio as bibliotecas API][twilio_libraries].

Aspetos fundamentais da Twilio API são verbos do Twilio e Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio verbos
A API permite a utilização de Twilio verbos; Por exemplo, o  **&lt;indiquem&gt;**  verbo dá instruções ao Twilio audibly entregar uma mensagem sobre uma chamada.

Segue-se uma lista de verbos do Twilio.  Saiba mais sobre os outros verbos e as capacidades através de [documentação do Twilio Markup Language](http://www.twilio.com/docs/api/twiml).

* **&lt;Marcação&gt;**: liga-se o autor da chamada para outro telemóvel.
* **&lt;Recolher&gt;**: recolhe dígitos numéricos introduzidos no teclado do telefone.
* **&lt;Hangup&gt;**: termina uma chamada.
* **&lt;Reproduzir&gt;**: desempenha um ficheiro de áudio.
* **&lt;Colocar em pausa&gt;**: silenciosamente aguarda que um número de segundos especificado.
* **&lt;Registo&gt;**: regista a voz do emissor e devolve um URL de um ficheiro que contém a gravação.
* **&lt;Redirecionar&gt;**: transfere o controlo de uma chamada ou SMS para o TwiML um URL diferente.
* **&lt;Rejeitar&gt;**: rejeita uma chamada para o seu número de Twilio sem a faturação
* **&lt;Diga&gt;**: converte texto para reconhecimento de voz que é efetuado numa chamada.
* **&lt;SMS&gt;**: envia uma mensagem SMS.

### <a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções baseado em XML com base nos verbos do Twilio que o informam Twilio como processar uma chamada ou um SMS.

Por exemplo, o seguinte TwiML seria converter o texto **Olá, mundo** para reconhecimento de voz.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Quando a aplicação chama a API do Twilio, um dos parâmetros de API é o URL que devolve a resposta de TwiML. Para fins de desenvolvimento, pode utilizar os URLs do Twilio fornecidos para fornecer as respostas de TwiML utilizadas pelas suas aplicações. Também pode alojar os suas próprias URLs para produzir as respostas de TwiML e outra opção consiste em utilizar o **TwiMLResponse** objeto.

Para obter mais informações sobre Twilio verbos, os respetivos atributos e TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API do Twilio, consulte [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Criar uma conta do Twilio
Quando estiver pronto para obter uma conta do Twilio, inscreva-se em [tente Twilio][try_twilio]. Pode começar com uma conta gratuita e atualize a sua conta mais tarde.

Quando se inscreve para uma conta do Twilio, receberá um ID de conta e um token de autenticação. Ambos serão necessários para efetuar chamadas de API do Twilio. Para impedir o acesso não autorizado à sua conta, mantenha o seu token de autenticação segura. O ID de conta e a autenticação de token são visíveis no [página de conta do Twilio][twilio_account], nos campos com a etiqueta **SID da conta** e **AUTH TOKEN**, respetivamente.

## <a id="create_app"></a>Criar uma aplicação do Azure
Uma aplicação do Azure que aloja uma aplicação do Twilio ativada é igual a partir de qualquer outra aplicação do Azure. Adicionar a biblioteca .NET do Twilio e configurar a função para utilizar as bibliotecas de .NET do Twilio.
Para obter informações sobre como criar um projeto inicial do Azure, consulte [criar um projeto do Azure com o Visual Studio][vs_project].

## <a id="configure_app"></a>Configurar a sua aplicação utilizar bibliotecas do Twilio
Twilio fornece um conjunto de bibliotecas de programa auxiliar de .NET que moldam diferentes aspetos do Twilio para fornecer formas simples e fácil de interagir com a API de REST do Twilio e o cliente do Twilio para gerar TwiML respostas.

Twilio fornece bibliotecas de cinco para programadores de .NET:
Biblioteca|Descrição
---|---
Twilio.API|A biblioteca principal do Twilio que encapsula num wrapper a API de REST do Twilio numa biblioteca .NET amigável. Esta biblioteca está disponível para .NET, o Silverlight e o Windows Phone 7.
Twilio.TwiML|Fornece uma forma amigável do .NET para gerar TwiML markup.
Twilio.MVC|Para programadores com ASP.NET MVC, esta biblioteca inclui um TwilioController, TwiML ActionResult e o atributo de validação do pedido.
Twilio.WebMatrix|Para programadores utilizando a ferramenta de desenvolvimento do WebMatrix gratuita da Microsoft, esta biblioteca contém programas de ajuda do Razor sintaxe para várias ações do Twilio.
Twilio.Client.Capability|Contém o gerador de token de capacidade para utilização com o SDK de JavaScript de cliente do Twilio.

Tenha em atenção que todas as bibliotecas requerem .NET 3.5, o Silverlight 4 ou o Windows Phone 7 ou posterior.

Os exemplos fornecidos neste guia utilizam a biblioteca de Twilio.API.

As bibliotecas podem ser [instalado utilizando a extensão de Gestor de pacote NuGet](http://www.twilio.com/docs/csharp/install) disponível para Visual Studio 2010 até 2015.  O código de origem estiver alojado num [GitHub][twilio_github_repo], que inclui um Wiki que contém a documentação completa para utilizar as bibliotecas.

Por predefinição, o Microsoft Visual Studio 2010 instala a versão 1.2 do NuGet. Instalar as bibliotecas do Twilio requer a versão 1.6 do NuGet ou superior. Para obter informações sobre instalar ou atualizar NuGet, consulte [http://nuget.org/][nuget].

> [!NOTE]
> Para instalar a versão mais recente do NuGet, tem primeiro de desinstalar a versão carregá-la utilizando o Gestor de extensões do Visual Studio. Para tal, tem de executar o Visual Studio como administrador. Caso contrário, o botão de desinstalação está desativado.
>
>

### <a id="use_nuget"></a>Para adicionar as bibliotecas do Twilio ao seu projeto de Visual Studio:
1. Abra a solução no Visual Studio.
2. Clique com botão direito **referências**.
3. Clique em **gerir pacotes NuGet...**
4. Clique em **Online**.
5. Na caixa de pesquisa online, escreva *twilio*.
6. Clique em **instalar** no pacote Twilio.

## <a id="howto_make_call"></a>Como: efetuar uma chamada de saída
O seguinte procedimento mostra como efetuar uma saída chamada utilizando o **CallResource** classe. Este código também utiliza um site do Twilio fornecidos para devolver a resposta do Twilio Markup Language (TwiML). Substitua os valores para o **para** e **de** números de telefone e certifique-se de que verifique a **de** phone número para a sua conta do Twilio antes de executar o código.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    const string accountSID = "your_twilio_account";
    const string authToken = "your_twilio_authentication_token";

    // Initialize the TwilioClient.
    TwilioClient.Init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    var url = "http://twimlets.com/message";
    url = $"{url}?Message%5B0%5D=Hello%20World";

    // Set the call From, To, and URL values to use for the call.
    // This sample uses the sandbox number provided by
    // Twilio to make the call.
    var call = CallResource.Create(
        to: new PhoneNumber("+NNNNNNNNNN"),
        from: new PhoneNumber("NNNNNNNNNN"),
        url: new Uri(url));
        }

Para obter mais informações sobre os parâmetros transmitidos para o **CallResource.Create** método, consulte [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Conforme mencionado, este código utiliza um site do Twilio fornecidos para devolver a resposta de TwiML. Em vez disso, pode utilizar os seus sites para fornecer a resposta de TwiML. Para obter mais informações, consulte [como: TwiML fornecer respostas a partir do seu próprio site](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Como: enviar uma mensagem SMS
Captura de ecrã seguinte mostra como enviar uma mensagem SMS utilizando o **MessageResource** classe. O **de** número é fornecido pela Twilio para contas de avaliação para enviar mensagens SMS. O **para** número deve ser verificado para a sua conta do Twilio antes de executar o código.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    const string accountSID = "your_twilio_account";
    const string authToken = "your_twilio_authentication_token";

    // Initialize the TwilioClient.
    TwilioClient.Init(accountSID, authToken);

    try
    {
        // Send an SMS message.
        var message = MessageResource.Create(
            to: new PhoneNumber("+12069419717"),
            from: new PhoneNumber("+14155992671"),
            body: "This is my SMS message.");
    }
    catch (TwilioException ex)
    {
        // An exception occurred making the REST call
        Console.WriteLine(ex.Message);
    }

## <a id="howto_provide_twiml_responses"></a>Como: forneça TwiML respostas a partir do seu próprio site
Quando a aplicação inicia uma chamada à API do Twilio - por exemplo, através de **CallResource.Create** método - Twilio envia o pedido para um URL que deverá devolver uma resposta de TwiML. O exemplo na [como: efetuar uma chamada de saída](#howto_make_call) utiliza o URL do Twilio fornecidos pelo [http://twimlets.com/message] [ twimlet_message_url] para devolver a resposta.

> [!NOTE]
> Enquanto TwiML foi concebido para utilização pelos serviços web, pode ver o TwiML no seu browser. Por exemplo, clique em [http://twimlets.com/message] [ twimlet_message_url] para ver vazio &lt;resposta&gt; elemento; como outro exemplo, clique em [http://twimlets.com/message? Mensagem 5B0 de %% 5 D = Olá % 20World](http://twimlets.com/message?Message%5B0%5D=Hello%20World) para ver um &lt;resposta&gt; elemento que contenha um &lt;indiquem&gt; elemento.
>
>

Em vez de depender do Twilio URL fornecido pelo, pode criar o seu próprio site URL que devolve as respostas de HTTP. Pode criar o site em qualquer idioma que devolve as respostas de HTTP. Este tópico parte do princípio de que irá alojar o URL de um processador genérico do ASP.NET.

O processador do ASP.NET seguinte cria uma resposta de TwiML indica **Olá, mundo** na chamada.

    using System.Text;
    using System.Web;

    namespace WebRole1
    {
        /// <summary>
        /// Summary description for Handler1
        /// </summary>
        public class Handler1 : IHttpHandler
        {
            public void ProcessRequest(HttpContext context)
            {
                const string twiMLResponse =
                    "<Response><Say>Hello World.</Say></Response>";
                
                context.Response.Clear();
                context.Response.ContentType = "text/xml";
                context.Response.ContentEncoding = Encoding.UTF8;
                context.Response.Write(twiMLResponse);
                context.Response.End();
            }

            public bool IsReusable
            {
                get
                {
                    return false;
                }
            }
        }
    }
    
Como pode ver do exemplo acima, a resposta de TwiML é simplesmente um documento XML. A biblioteca de Twilio.TwiML contém classes que irão gerar TwiML por si. O exemplo abaixo produz a resposta equivalente, conforme mostrado acima, mas utiliza o **VoiceResponse** classe.

    using System.Web;
    using Twilio.TwiML;

    namespace WebRole1
    {
        /// <summary>
        /// Summary description for Handler1
        /// </summary>
        public class Handler1 : IHttpHandler
        {

            public void ProcessRequest(HttpContext context)
            {
                var twiml = new VoiceResponse();
                twiml.Say("Hello World.");

                context.Response.Clear();
                context.Response.ContentType = "text/xml";
                context.Response.Write(twiml.ToString());
                context.Response.End();
            }

            public bool IsReusable
            {
                get
                {
                    return false;
                }
            }
        }
    }

Para mais informações sobre TwiML, consulte [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml).

Assim que tiver configurado uma forma de fornecer respostas TwiML, pode passar esse URL para o **CallResource.Create** método. Por exemplo, se tiver uma aplicação web com o nome MyTwiML implementada num serviço em nuvem do Azure e o nome do seu processador do ASP.NET é mytwiml.ashx, o URL pode ser transferido para **CallResource.Create** conforme mostrado no exemplo de código seguinte:

    // This sample uses the sandbox number provided by Twilio to make the call.
    // Place the call.
    var call = CallResource.Create(
        to: new PhoneNumber("+NNNNNNNNNN"),
        from: new PhoneNumber("NNNNNNNNNN"),
        url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
        }

Para obter informações adicionais sobre a utilização do Twilio no Azure com o ASP.NET, consulte [como efetuar uma chamada telefónica utilizando Twilio numa função da web no Azure][howto_phonecall_dotnet].

[!INCLUDE [twilio-additional-services-and-next-steps](../includes/twilio-additional-services-and-next-steps.md)]

[howto_phonecall_dotnet]: partner-twilio-cloud-services-dotnet-phone-call-web-role.md

[twimlet_message_url]: http://twimlets.com/message

[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[vs_project]:http://msdn.microsoft.com/library/windowsazure/ee405487.aspx
[nuget]:http://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
