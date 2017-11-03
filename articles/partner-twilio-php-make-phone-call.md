---
title: "Como efetuar uma chamada telefónica do Twilio (PHP) | Microsoft Docs"
description: "Saiba como efetuar uma chamada telefónica e enviar uma mensagem SMS com o serviço de API do Twilio no Azure. São exemplos para aplicação PHP."
documentationcenter: php
services: 
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: 44e35adc-be06-4700-beee-8c9e2c20c540
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: 9866a196b3be10548d7a431430e570b41c190fc0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Como efetuar uma chamada telefónica utilizar Twilio numa aplicação PHP no Azure
O exemplo seguinte mostra como pode utilizar Twilio para efetuar uma chamada de uma página web PHP alojado no Azure. A aplicação resultante pedirá ao utilizador para os valores de chamada telefónica, conforme mostrado na captura de ecrã seguinte.

![Formato de chamada do Azure utilizando Twilio e PHP][twilio_php]

Terá de fazer o seguinte para utilizar o código neste tópico:

1. Adquirir uma conta do Twilio e a autenticação de token da sua [Twilio consola][twilio_console]. Para obter uma introdução Twilio, avalie preços em [http://www.twilio.com/pricing][twilio_pricing]. Pode inscrever-se para uma conta de avaliação em [https://www.twilio.com/try-twilio][try_twilio].
2. Obter o [Twilio biblioteca para PHP](https://github.com/twilio/twilio-php) ou instalá-lo como um pacote PEAR. Para obter mais informações, consulte o [ficheiro Leia-me](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Instale o Azure SDK para PHP. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](app-service-web/web-sites-php-mysql-deploy-use-git.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Criar um formulário web para efetuar uma chamada
O código HTML seguinte mostra como criar uma página web (**callform.html**) que obtém dados de utilizador para efetuar uma chamada:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Automated call form</title>
</head>
<body>
  <h1>Automated Call Form</h1>
  <p>Fill in all fields and click <b>Make this call</b>.</p>
  <form action="makecall.php" method="post">
    <table>
      <tr>
        <td>To:</td>
        <td><input name="callTo" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>From:</td>
        <td><input name="callFrom" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>Call message:</td>
        <td><input name="callText" size="100" type="text" value="Hello. This is the call text. Good bye."></td>
      </tr>
      <tr>
        <td colspan="2"><input type="submit" value="Make this call"></td>
      </tr>
    </table>
  </form><br>
</body>
</html>
```

## <a name="create-the-code-to-make-the-call"></a>Criar o código para efetuar a chamada
O código seguinte mostra como criar **makecall.php**, o que é chamado quando o utilizador submete o formulário apresentado pela **callform.html**. O código abaixo cria a mensagem de chamada e gera a chamada. Além disso, não se esqueça de utilizar a conta do Twilio e a autenticação de token do [Twilio consola] [ twilio_console] em vez dos valores de marcador de posição atribuídos a **$sid** e  **$token** no código abaixo.

```html
<html>
<head><title>Making call...</title></head>
<body>
<p>Your call is being made.</p>

<?php
require_once 'path/to/vendor/autoload.php';

$sid   = "your_account_sid";
$token = "your_authentication_token";

$from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
$to_number   = $_POST['callTo'];
$message     = $_POST['callText'];

$client = new Twilio\Rest\Client($sid, $token);

$call = $client->calls->create(
            $to_number,
            $from_number,
            array('url' => http://twimlets.com/message?Message=' . urlencode($message))
        );

echo "Call status: " . $call->status . "<br />";
echo "URI resource: " . $call->uri . "<br />";
?>
</body>
</html>
```

Para além de fazer a chamada, **makecall.php** apresenta alguns metadados de chamada, conforme é mostrado na imagem abaixo. Para mais informações sobre os metadados de chamada, consulte [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties].

![Resposta de chamada do Azure utilizando Twilio e o PHP][twilio_php_response]

## <a name="run-the-application"></a>Executar a aplicação
O passo seguinte consiste em [implementar a aplicação Web Apps do Azure com o Git](app-service/app-service-web-get-started-php.md) (embora não todas as informações de existe são relevantes). 

## <a name="next-steps"></a>Passos seguintes
Este código foi fornecido para lhe mostrar funcionalidades básicas utilizando Twilio no PHP no Azure. Antes de implementar no Azure em produção, poderá querer adicionar mais processamento de erros ou outras funcionalidades. Por exemplo:

* Em vez de utilizar um formulário web, pode utilizar blobs de armazenamento do Azure ou a base de dados SQL para armazenar os números de telefone e chamar texto. Para obter informações sobre como utilizar o blobs storage do Azure no PHP, consulte [utilizando o armazenamento do Azure com as aplicações PHP][howto_blob_storage_php]. Para obter informações sobre como utilizar a base de dados SQL no PHP, consulte [utilizando a base de dados SQL com as aplicações PHP][howto_sql_azure_php].
* O **makecall.php** código utiliza Twilio URL fornecido pelo ([http://twimlets.com/message][twimlet_message_url]) para fornecer uma resposta do Twilio Markup Language (TwiML) que informa como Twilio para continuar com a chamada. Por exemplo, pode conter TwiML que é devolvido um `<Say>` verbo que resulta em texto de autenticação a ser ditas para o destinatário da chamada. Em vez de utilizar o URL do Twilio fornecidos pelo, foi possível criar o seu próprio serviço para responder ao pedido do Twilio; Para obter mais informações, consulte [como Twilio de utilização de voz e capacidades de SMS do PHP][howto_twilio_voice_sms_php]. Obter mais informações sobre TwiML podem ser encontradas em [http://www.twilio.com/docs/api/twiml][twiml]e mais informações sobre `<Say>` e outros verbos Twilio podem ser encontrados em [http:// www.twilio.com/Docs/API/twiml/Say][twilio_say].
* Leia as diretrizes de segurança do Twilio em [https://www.twilio.com/docs/security][twilio_docs_security].

Para obter informações adicionais sobre Twilio, consulte [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Veja Também
* [Como utilizar Twilio de voz e capacidades SMS do PHP](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/docs/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: http://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: http://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[twilio_php_github]: https://github.com/twilio/twilio-php
