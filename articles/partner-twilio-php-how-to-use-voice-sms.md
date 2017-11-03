---
title: Como utilizar Twilio de voz e SMS (PHP) | Microsoft Docs
description: "Saiba como efetuar uma chamada telefónica e enviar uma mensagem SMS com o serviço de API do Twilio no Azure. Exemplos de código escrito em PHP."
documentationcenter: php
services: 
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: 007f22e3-ac75-4868-8315-da000c2e0dd0
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: bd50eac7390e8639f77894689388e6926cdb619c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Como utilizar Twilio de voz e capacidades SMS do PHP
Este guia demonstra como efetuar tarefas de programação comuns com o serviço de API do Twilio no Azure. Os cenários abrangidos incluem a efetuar uma chamada telefónica e enviar uma mensagem de serviço de mensagens curto (SMS). Para obter mais informações sobre Twilio e a utilização de voz e SMS nas suas aplicações, consulte o [passos](#NextSteps) secção.

## <a id="WhatIs"></a>O que é Twilio?
Twilio está a ligar ao futuro da comunicações de empresa, permitindo aos programadores incorporar de voz, VoIP e de mensagens em aplicações. Estes virtualizar todos os infraestrutura necessária num ambiente baseado na nuvem, global, expor-lo através da plataforma de comunicações API do Twilio. As aplicações são simples criar e dimensionável. Desfrute de mais flexibilidade consigo pay-como vá preços e beneficiar da fiabilidade da nuvem.

**Voz do Twilio** permite que as aplicações tornar e receber chamadas telefónicas. **Twilio SMS** permite que a aplicação envie e receba mensagens de texto. **Cliente do Twilio** permite-lhe efetuar chamadas de VoIP de qualquer telefone, o tablet ou o browser e suporta WebRTC.

## <a id="Pricing"></a>Preços do Twilio e ofertas especiais
Clientes do Azure recebem um [oferta especial](http://www.twilio.com/azure): complimentary $10 do Twilio crédito ao atualizar a sua conta do Twilio. Este crédito do Twilio podem ser aplicado a qualquer utilização do Twilio (crédito $10 equivalente ao máximo de 1000 mensagens SMS a enviar ou receber até 1000 entrados minutos de voz, consoante a localização do destino de número e a mensagem ou a chamada da telefone). Resgatar este crédito do Twilio e começar a utilizar em: [http://ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

Twilio é um serviço pay as you go. Existem não existem taxas de configuração e pode fechar a sua conta em qualquer altura. Pode encontrar mais detalhes em [preços do Twilio][twilio_pricing].

## <a id="Concepts"></a>Conceitos
A API do Twilio é uma API RESTful que fornece uma funcionalidade SMS de voz e de aplicações. Bibliotecas de cliente estão disponíveis em vários idiomas; Para obter uma lista, consulte [Twilio as bibliotecas API][twilio_libraries].

Aspetos fundamentais da Twilio API são verbos do Twilio e Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio verbos
A API permite a utilização de Twilio verbos; Por exemplo, o  **&lt;indiquem&gt;**  verbo dá instruções ao Twilio audibly entregar uma mensagem sobre uma chamada.

Segue-se uma lista de verbos do Twilio. Saiba mais sobre os outros verbos e as capacidades através de [documentação do Twilio Markup Language](http://www.twilio.com/docs/api/twiml).

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

## <a id="create_app"></a>Criar uma aplicação PHP
Uma aplicação PHP que utiliza o serviço do Twilio e está em execução no Azure é não diferente de qualquer outra aplicação PHP que utiliza o serviço do Twilio. Enquanto os serviços de Twilio são baseado em REST e podem ser chamados a partir do PHP de várias formas, este artigo foca-se sobre como utilizar os serviços do Twilio com [Twilio biblioteca para que o PHP a partir do GitHub][twilio_php]. Para obter mais informações sobre como utilizar a biblioteca do Twilio para PHP, consulte [http://readthedocs.org/docs/twilio-php/en/latest/index.html][twilio_lib_docs].

Instruções detalhadas para criar e implementar uma aplicação do Twilio/PHP para o Azure estão disponíveis em [como tornar a Twilio de utilização de uma chamada telefónica numa aplicação PHP no Azure][howto_phonecall_php].

## <a id="configure_app"></a>Configurar a sua aplicação utilizar bibliotecas do Twilio
Pode configurar a sua aplicação para utilizar a biblioteca de Twilio para PHP de duas formas:

1. Transferir a biblioteca do Twilio para PHP a partir do GitHub ([https://github.com/twilio/twilio-php][twilio_php]) e adicione o **serviços** diretório à sua aplicação.
   
    -OU-
2. Instale a biblioteca do Twilio do PHP como um pacote PEAR. Podem ser instalado com os seguintes comandos:
   
        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Depois de instalar a biblioteca do Twilio para PHP, em seguida, pode adicionar um **require_once** declaração na parte superior dos seus ficheiros PHP para fazer referência a biblioteca:

        require_once 'Services/Twilio.php';

Para obter mais informações, consulte [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Como: efetuar uma chamada de saída
O seguinte procedimento mostra como efetuar uma saída chamada utilizando o **Services_Twilio** classe. Este código também utiliza um site do Twilio fornecidos para devolver a resposta do Twilio Markup Language (TwiML). Substitua os valores para o **de** e **para** números de telefone e certifique-se de que verifique a **de** número para a sua conta do Twilio antes de executar o código de telefone.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the the call.
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "http://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
              $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Conforme mencionado, este código utiliza um site do Twilio fornecidos para devolver a resposta de TwiML. Em vez disso, poderia utilizar o seu próprio site para fornecer a resposta de TwiML; Para obter mais informações, consulte [como fornecer respostas de TwiML da sua própria Web Site](#howto_provide_twiml_responses).

* **Tenha em atenção**: para resolver erros de validação de certificado SSL, consulte [http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 

## <a id="howto_send_sms"></a>Como: enviar uma mensagem SMS
O seguinte procedimento mostra como enviar uma mensagem SMS utilizando o **Services_Twilio** classe. O **de** número é fornecido pela Twilio para contas de avaliação para enviar mensagens SMS. O **para** número tem de ser verificado para a sua conta do Twilio antes da execução do código.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->$client->account->messages->sendMessage($from_number, $to_number, $message);
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

## <a id="howto_provide_twiml_responses"></a>Como: forneça TwiML respostas a partir do seu próprio site
Quando inicia a sua aplicação de uma chamada à API do Twilio, Twilio irá enviar o pedido para um URL que deverá devolver uma resposta de TwiML. O exemplo acima utiliza o URL do Twilio fornecidos pelo [http://twimlets.com/message][twimlet_message_url]. (Embora TwiML está concebido para utilização por Twilio, pode ver o it no seu browser. Por exemplo, clique em [http://twimlets.com/message] [ twimlet_message_url] para ver vazio `<Response>` elemento; como outro exemplo, clique em [http://twimlets.com/message? Mensagem % 5B0 %5 D = Olá % 20World] [ twimlet_message_url_hello_world] para ver um `<Response>` elemento que contenha um `<Say>` elemento.)

Em vez de depender do Twilio URL fornecido pelo, pode criar o seu próprio site, que devolve as respostas de HTTP. Pode criar o site em qualquer idioma que devolve respostas XML; Este tópico parte do princípio de que irá utilizar PHP para criar o TwiML.

A página seguinte do PHP resulta numa resposta TwiML que indica que **Olá, mundo** na chamada.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

Como pode ver do exemplo acima, a resposta de TwiML é simplesmente um documento XML. A biblioteca do Twilio para PHP contém classes que irão gerar TwiML por si. O exemplo abaixo produz a resposta equivalente, conforme mostrado acima, mas utiliza o **serviços\_Twilio\_Twiml** classe na biblioteca do Twilio para PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Para mais informações sobre TwiML, consulte [https://www.twilio.com/docs/api/twiml][twiml_reference]. 

Assim que tiver a sua página PHP configurado para dar respostas TwiML, utilize o URL da página PHP como o URL transmitido a `Services_Twilio->account->calls->create` método. Por exemplo, se tiver uma aplicação Web com o nome **MyTwiML** implementada para um Azure serviço alojado e o nome da página PHP é **mytwiml.php**, o URL pode ser transferido para **Services_Twilio -> conta -> chamadas -> criar** conforme mostrado no exemplo seguinte:

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
              $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Para obter informações adicionais sobre a utilização do Twilio no Azure com o PHP, consulte [como tornar a Twilio de utilização de uma chamada telefónica numa aplicação PHP no Azure][howto_phonecall_php].

## <a id="AdditionalServices"></a>Como: utilizar os serviços do Twilio adicionais
Para além dos exemplos mostrados aqui, Twilio oferece APIs baseadas na web que pode utilizar para tirar partido das funcionalidades adicionais do Twilio da sua aplicação do Azure. Para mais informações, consulte o [documentação da API do Twilio][twilio_api_documentation].

## <a id="NextSteps"></a>Passos Seguintes
Agora que aprendeu as noções básicas do serviço do Twilio, siga estas ligações para saber mais:

* [Diretrizes de segurança do Twilio][twilio_security_guidelines]
* [De Twilio HowTo e código de exemplo][twilio_howtos]
* [Tutoriais de início rápido do Twilio][twilio_quickstarts] 
* [Twilio no GitHub][twilio_on_github]
* [Comunicar com o suporte do Twilio][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_php]: partner-twilio-php-make-phone-call.md
[twilio_voice_request]: https://www.twilio.com/docs/api/twiml/twilio_request
[twilio_sms_request]: https://www.twilio.com/docs/api/twiml/sms/twilio_request
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
