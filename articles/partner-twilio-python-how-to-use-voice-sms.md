---
title: Como utilizar Twilio de voz e SMS (Python) | Microsoft Docs
description: "Saiba como efetuar uma chamada telefónica e enviar uma mensagem SMS com o serviço de API do Twilio no Azure. Exemplos de código escrito no Python."
services: 
documentationcenter: python
author: devinrader
manager: twilio
editor: 
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: f4a02bb7a7c46e7a0e3c75b870c522eae8294339
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Como utilizar Twilio de voz e funcionalidades SMS no Python
Este guia demonstra como efetuar tarefas de programação comuns com o serviço de API do Twilio no Azure. Os cenários abrangidos incluem a efetuar uma chamada telefónica e enviar uma mensagem de serviço de mensagens curto (SMS). Para obter mais informações sobre Twilio e a utilização de voz e SMS nas suas aplicações, consulte o [passos](#NextSteps) secção.

## <a id="WhatIs"></a>O que é Twilio?
Twilio está a ligar ao futuro da comunicações de empresa, permitindo aos programadores incorporar de voz, VoIP e de mensagens em aplicações. Estes virtualizar todos os infraestrutura necessária num ambiente baseado na nuvem, global, expor-lo através da plataforma de comunicações API do Twilio. As aplicações são simples criar e dimensionável. Desfrute de mais flexibilidade consigo pay-como vá preços e beneficiar da fiabilidade da nuvem.

**Voz do Twilio** permite que as aplicações tornar e receber chamadas telefónicas.
**Twilio SMS** permite que a aplicação envie e receba mensagens de texto.
**Cliente do Twilio** permite-lhe efetuar chamadas de VoIP de qualquer telefone, o tablet ou o browser e suporta WebRTC.

## <a id="Pricing"></a>Preços do Twilio e ofertas especiais
Clientes do Azure recebem um [oferta especial] [ special_offer] $10 do Twilio crédito ao atualizar a sua conta do Twilio. Este crédito do Twilio podem ser aplicado a qualquer utilização do Twilio (crédito $10 equivalente ao máximo de 1000 mensagens SMS a enviar ou receber até 1000 entrados minutos de voz, consoante a localização do destino de número e a mensagem ou a chamada da telefone). Resgatar isto [Twilio crédito] [ special_offer] e começar a utilizar.

Twilio é um serviço pay as you go. Existem não existem taxas de configuração e pode fechar a sua conta em qualquer altura. Pode encontrar mais detalhes em [preços do Twilio][twilio_pricing].

## <a id="Concepts"></a>Conceitos
A API do Twilio é uma API RESTful que fornece uma funcionalidade SMS de voz e de aplicações. Bibliotecas de cliente estão disponíveis em vários idiomas; Para obter uma lista, consulte [Twilio as bibliotecas API][twilio_libraries].

Aspetos fundamentais da Twilio API são verbos do Twilio e Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio verbos
A API permite a utilização de Twilio verbos; Por exemplo, o  **&lt;indiquem&gt;**  verbo dá instruções ao Twilio audibly entregar uma mensagem sobre uma chamada.

Segue-se uma lista de verbos do Twilio. Saiba mais sobre os outros verbos e as capacidades através de [documentação do Twilio Markup Language][twiml].

* **&lt;Marcação&gt;**: liga-se o autor da chamada para outro telemóvel.
* **&lt;Recolher&gt;**: recolhe dígitos numéricos introduzidos no teclado do telefone.
* **&lt;Hangup&gt;**: termina uma chamada.
* **&lt;Colocar em pausa&gt;**: silenciosamente aguarda que um número de segundos especificado.
* **&lt;Reproduzir&gt;**: desempenha um ficheiro de áudio.
* **&lt;Fila&gt;**: adicionar o para uma fila dos chamadores.
* **&lt;Registo&gt;**: regista a voz do chamador e devolve um URL de um ficheiro que contém a gravação.
* **&lt;Redirecionar&gt;**: transfere o controlo de uma chamada ou SMS para o TwiML um URL diferente.
* **&lt;Rejeitar&gt;**: rejeita uma chamada para o seu número de Twilio sem a faturação.
* **&lt;Diga&gt;**: converte texto para reconhecimento de voz que é efetuado numa chamada.
* **&lt;SMS&gt;**: envia uma mensagem SMS.

### <a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções baseado em XML com base nos verbos do Twilio que o informam Twilio como processar uma chamada ou um SMS.

Por exemplo, o seguinte TwiML seria converter o texto **Olá, mundo** para reconhecimento de voz.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Quando a aplicação chama a API do Twilio, um dos parâmetros de API é o URL que devolve a resposta de TwiML. Para fins de desenvolvimento, pode utilizar os URLs do Twilio fornecidos para fornecer as respostas de TwiML utilizadas pelas suas aplicações. Também pode alojar os suas próprias URLs para produzir as respostas de TwiML e outra opção consiste em utilizar o `TwiMLResponse` objeto.

Para obter mais informações sobre Twilio verbos, os respetivos atributos e TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API do Twilio, consulte [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Criar uma conta do Twilio
Quando estiver pronto para obter uma conta do Twilio, inscreva-se em [tente Twilio][try_twilio]. Pode começar com uma conta gratuita e atualize a sua conta mais tarde.

Quando se inscreve para uma conta do Twilio, receberá uma SID da conta e um token de autenticação. Ambos serão necessários para efetuar chamadas de API do Twilio. Para impedir o acesso não autorizado à sua conta, mantenha o seu token de autenticação segura. O SID de conta e o token de autenticação estão visíveis a [Twilio consola][twilio_console], nos campos etiquetados **SID da conta** e **AUTH TOKEN**, respetivamente.

## <a id="create_app"></a>Criar uma aplicação de Python
Uma aplicação de Python que utiliza o serviço do Twilio e está em execução no Azure é não diferente de qualquer outra aplicação de Python que utiliza o serviço do Twilio. Enquanto os serviços de Twilio são baseado em REST e podem ser chamados a partir do Python de várias formas, este artigo foca-se sobre como utilizar os serviços do Twilio com [Twilio biblioteca para Python a partir do GitHub][twilio_python]. Para obter mais informações sobre como utilizar a biblioteca do Twilio para o Python, consulte [http://readthedocs.org/docs/twilio-python/en/latest/index.html][twilio_lib_docs].

Primeiro, [configuração uma nova VM do Linux do Azure] [azure_vm_setup] para atuar como um anfitrião para o Python nova aplicação web. Assim que a Máquina Virtual está em execução, será necessário expor a aplicação numa porta pública conforme descrito abaixo.

### <a name="add-an-incoming-rule"></a>Adicionar uma regra de entrada
  1. Ir para a página [grupo de segurança] [azure_nsg].
  2. Selecione o grupo de segurança que corresponde com a Máquina Virtual.
  3. Adicionar e **regra de saída** para **porta 80**. Lembre-se de que permitem a entrada de qualquer endereço.

### <a name="set-the-dns-name-label"></a>Defina a etiqueta de nome DNS
  1. Ir para a página [o público IP Adresses] [azure_ips].
  2. Selecione o IP público que correspends com a Máquina Virtual.
  3. Definir o **etiqueta de nome DNS** no **configuração** secção. No caso deste exemplo terá um aspeto semelhante ao seguinte *etiqueta do domínio*. centralus.cloudapp.azure.com

Assim que estiver conseguir estabelecer ligação através de SSH para a Máquina Virtual pode instalar o Framework Web à sua escolha (os dois mais bem conhecidos no Python que está a ser [Flask](http://flask.pocoo.org/) e [Django](https://www.djangoproject.com)). Pode instalar um deles executando apenas o `pip install` comando.

Tenha em atenção que configurámos a Máquina Virtual para permitir o tráfego apenas na porta 80. Por isso, certifique-se configurar a aplicação para utilizar esta porta.

## <a id="configure_app"></a>Configurar a sua aplicação utilizar bibliotecas do Twilio
Pode configurar a sua aplicação para utilizar a biblioteca de Twilio para o Python de duas formas:

* Instale a biblioteca do Twilio para o Python como um pacote do Pip. Podem ser instalado com os seguintes comandos:
   
        $ pip install twilio

    -OU-

* Transferir a biblioteca do Twilio para Python a partir do GitHub ([https://github.com/twilio/twilio-python][twilio_python]) e instale-o como esta:

        $ python setup.py install

Depois de instalar a biblioteca do Twilio para o Python, pode, em seguida, `import` -lo nos seus ficheiros de Python:

        import twilio

Para obter mais informações, consulte [https://github.com/twilio/twilio-python/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Como: efetuar uma chamada de saída
O seguinte mostra como efetuar uma chamada de saída. Este código também utiliza um site do Twilio fornecidos para devolver a resposta do Twilio Markup Language (TwiML). Substitua os valores para o **from_number** e **to_number** números de telefone e certifique-se de que tiver confirmado que a **from_number** número para a sua conta do Twilio de telefone antes de executar o código.

    from urllib.parse import urlencode

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from_number = "NNNNNNNNNNN"

    # The number of the phone receiving call.
    to_number = "NNNNNNNNNNN"

    # Use the Twilio-provided site for the TwiML response.
    url = "http://twimlets.com/message?"

    # The phone message text.
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url + urlencode({'Message': message}))
    print(call.sid)

Conforme mencionado, este código utiliza um site do Twilio fornecidos para devolver a resposta de TwiML. Em vez disso, poderia utilizar o seu próprio site para fornecer a resposta de TwiML; Para obter mais informações, consulte [como fornecer respostas de TwiML da sua própria Web Site](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Como: enviar uma mensagem SMS
O seguinte procedimento mostra como enviar uma mensagem SMS utilizando o `TwilioRestClient` classe. O **from_number** número é fornecido pela Twilio para contas de avaliação para enviar mensagens SMS. O **to_number** número deve ser verificado para a sua conta do Twilio antes de executar o código.

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    from_number = "NNNNNNNNNNN"  # With trial account, texts can only be sent from your Twilio number.
    to_number = "NNNNNNNNNNN"
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Send the SMS message.
    message = client.messages.create(to=to_number,
                                     from_=from_number,
                                     body=message)

## <a id="howto_provide_twiml_responses"></a>Como: forneça TwiML respostas a partir do seu próprio site
Quando inicia a sua aplicação de uma chamada à API do Twilio, Twilio irá enviar o pedido para um URL que deverá devolver uma resposta de TwiML. O exemplo acima utiliza o URL do Twilio fornecidos pelo [http://twimlets.com/message][twimlet_message_url]. (Embora TwiML está concebido para utilização por Twilio, pode vê-la no seu browser. Por exemplo, clique em [http://twimlets.com/message] [ twimlet_message_url] para ver vazio `<Response>` elemento; como outro exemplo, clique em [http://twimlets.com/message? Mensagem % 5B0 %5 D = Olá % 20World] [ twimlet_message_url_hello_world] para ver um `<Response>` elemento que contenha um `<Say>` elemento.)

Em vez de depender do Twilio URL fornecido pelo, pode criar o seu próprio site, que devolve as respostas de HTTP. Pode criar o site em qualquer idioma que devolve respostas XML; Este tópico parte do princípio de que irá utilizar Python para criar o TwiML.

Os exemplos seguintes irão emitir uma resposta de TwiML indica **Olá, mundo** na chamada.

Com Flask:

    from flask import Response
    @app.route("/")
    def hello():
        xml = '<Response><Say>Hello world.</Say></Response>'
        return Response(xml, mimetype='text/xml')

Com o Django:

    from django.http import HttpResponse
    def hello(request):
        xml = '<Response><Say>Hello world.</Say></Response>'
        return HttpResponse(xml, content_type='text/xml')

Como pode ver do exemplo acima, a resposta de TwiML é simplesmente um documento XML. A biblioteca do Twilio para Python contém classes que irão gerar TwiML por si. O exemplo abaixo produz a resposta equivalente, conforme mostrado acima, mas utiliza o `twiml` módulo na biblioteca do Twilio para Python:

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

Para mais informações sobre TwiML, consulte [https://www.twilio.com/docs/api/twiml][twiml_reference].

Assim que tiver a aplicação de Python configurado para dar respostas TwiML, utilize o URL da aplicação, como o URL transmitido a `client.calls.create` método. Por exemplo, se tiver uma aplicação Web com o nome **MyTwiML** implementada para um Azure alojado serviço, pode utilizar o respetivo url como webhook conforme mostrado no exemplo seguinte:

    from twilio.rest import TwilioRestClient

    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"
    from_number = "NNNNNNNNNNN"
    to_number = "NNNNNNNNNNN"
    url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url)
    print(call.sid)

## <a id="AdditionalServices"></a>Como: utilizar os serviços do Twilio adicionais
Para além dos exemplos mostrados aqui, Twilio oferece APIs baseadas na web que pode utilizar para tirar partido das funcionalidades adicionais do Twilio da sua aplicação do Azure. Para mais informações, consulte o [documentação da API do Twilio][twilio_api].

## <a id="NextSteps"></a>Passos Seguintes
Agora que aprendeu as noções básicas do serviço do Twilio, siga estas ligações para saber mais:

* [Diretrizes de segurança do Twilio][twilio_security_guidelines]
* [Guias de HowTo do Twilio e código de exemplo][twilio_howtos]
* [Tutoriais de início rápido do Twilio][twilio_quickstarts]
* [Twilio no GitHub][twilio_on_github]
* [Comunicar com o suporte do Twilio][twilio_support]

[special_offer]: http://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-python/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
