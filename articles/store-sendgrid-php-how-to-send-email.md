---
title: "Como utilizar o serviço de correio eletrónico do SendGrid (PHP) | Microsoft Docs"
description: "Saiba como enviar correio eletrónico com o serviço de correio eletrónico do SendGrid no Azure. Exemplos de código escrito em PHP."
documentationcenter: php
services: 
manager: sendgrid
editor: mollybos
author: thinkingserious
ms.assetid: 51a9928a-4c9e-4b0a-aca8-9a408aeb6f47
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 10/30/2014
ms.author: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com
ms.openlocfilehash: 523b986f66a2e48685e9707903194856f0dcf4a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>Como utilizar o serviço de E-Mail do SendGrid do PHP
Este guia demonstra como efetuar tarefas de programação comuns com o serviço de correio eletrónico do SendGrid no Azure. Os exemplos são escritos no PHP.
Os cenários abrangidos incluem **construir e-mail**, **enviar correio eletrónico**, e **adicionar anexos**. Para obter mais informações sobre SendGrid e enviar correio eletrónico, consulte o [passos](#next-steps) secção.

## <a name="what-is-the-sendgrid-email-service"></a>O que é o serviço de correio eletrónico do SendGrid?
SendGrid é um [serviço baseado na nuvem e-mail] que fornece fiável [entrega de correio eletrónico transacional], escalabilidade e a análise em tempo real, juntamente com APIs flexíveis que o facilitar a integração personalizados. Cenários comuns de utilização do SendGrid incluem:

* Enviar automaticamente os recibos aos clientes
* Administrar distribuição lista para enviar aos clientes i-fliers mensais e ofertas especiais
* Recolher métricas em tempo real para coisas como bloqueado por correio eletrónico e a capacidade de resposta do cliente
* Gerar relatórios para ajudar a identificar tendências
* Pedidos de cliente de reencaminhamento
* Notificações de correio eletrónico da sua aplicação

Para obter mais informações, consulte [https://sendgrid.com][https://sendgrid.com].

## <a name="create-a-sendgrid-account"></a>Criar uma conta do SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>Utilizar SendGrid da sua aplicação PHP
A utilização de SendGrid numa aplicação PHP do Azure não requer nenhum especial codificação ou de configuração. Uma vez SendGrid é um serviço, pode ser acedido exatamente da mesma forma de uma aplicação na nuvem como pode partir de uma aplicação no local.

## <a name="how-to-send-an-email"></a>Como: enviar uma mensagem de E-Mail
Pode enviar correio eletrónico através de SMTP ou a API Web fornecidos pelo SendGrid.

### <a name="smtp-api"></a>API DE SMTP
Para enviar correio eletrónico através da API de SMTP do SendGrid, utilize *Swift Mailer*, uma biblioteca baseado em componentes para enviar e-mails de aplicações PHP. Pode transferir o *Swift Mailer* biblioteca a partir de [http://swiftmailer.org/download] [ http://swiftmailer.org/download] v5.3.0 (utilizar [compositor] instalar Swift Mailer). Enviar mensagem de e-mail com a biblioteca implica a criação de instâncias do <span class="auto-style2">Swift\_SmtpTransport</span>, <span class="auto-style2">Swift\_Mailer</span>, e <span class="auto-style2">Swift\_mensagem</span> classes, definir as propriedades adequadas e, ao chamar o <span class="auto-style2">Swift\_Mailer::send</span> método.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the receiver is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
     $html = "<html>
           <head></head>
           <body>
               <p>Hi!<br>
                   How are you?<br>
               </p>
           </body>
           </html>";
     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     // Email recipients
     $to = array(
           'john@contoso.com'=>'Destination 1 Name',
           'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';

     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';

     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);

     // Create a message (subject)
     $message = new Swift_Message($subject);

     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');

     // send message
     if ($recipients = $swift->send($message, $failures))
     {
         // This will let us know how many users received this message
         echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
         echo "Something went wrong - ";
         print_r($failures);
     }

### <a name="web-api"></a>API Web
Utilizar do PHP [curl função] [ curl function] para enviar correio eletrónico utilizando a API Web do SendGrid.

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD';

     $params = array(
          'api_user' => $user,
          'api_key' => $pass,
          'to' => 'john@contoso.com',
          'subject' => 'testing from curl',
          'html' => 'testing body',
          'text' => 'testing body',
          'from' => 'anna@contoso.com',
       );

     $request = $url.'api/mail.send.json';

     // Generate curl request
     $session = curl_init($request);

     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);

     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

     // obtain response
     $response = curl_exec($session);
     curl_close($session);

     // print everything out
     print_r($response);

API de Web do SendGrid é muito semelhante a uma API REST, que não esteja verdadeiramente uma API RESTful, uma vez que, na maioria das chamadas, ambos obter e verbos POST podem ser utilizados-no alternadamente.

## <a name="how-to-add-an-attachment"></a>Como: adicionar um anexo
### <a name="smtp-api"></a>API DE SMTP
Enviar um anexo utilizando a API de SMTP envolve um adicionais de linha de código para o script de exemplo para enviar um e-mail com Swift Mailer.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
      $html = "<html>
          <head></head>
          <body>
             <p>Hi!<br>
                How are you?<br>
             </p>
          </body>
          </html>";

     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');

     // Email recipients
     $to = array(
          'john@contoso.com'=>'Destination 1 Name',
          'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';

     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';

     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);

     // Create a message (subject)
     $message = new Swift_Message($subject);

     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));

     // send message
     if ($recipients = $swift->send($message, $failures))
     {
          // This will let us know how many users received this message
          echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
          echo "Something went wrong - ";
          print_r($failures);
     }

A linha de código adicional é o seguinte:

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));

Esta linha de código chama o método de anexar no <span class="auto-style2">Swift\_mensagem</span> de objeto e utiliza um método estático <span class="auto-style2">fromPath</span> no <span class="auto-style2">Swift\_anexo</span> classe para obter e anexar um ficheiro para uma mensagem.

### <a name="web-api"></a>API Web
Enviar um anexo utilizando a API Web é muito semelhante a enviar uma mensagem de e-mail utilizando a API Web. No entanto, tenha em atenção que no exemplo que se segue, a matriz de parâmetro tem de conter este elemento:

    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

Exemplo:

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD';

     $fileName = 'myfile';
     $filePath = dirname(__FILE__);

     $params = array(
         'api_user' => $user,
         'api_key' => $pass,
         'to' =>'john@contoso.com',
         'subject' => 'test of file sends',
         'html' => '<p> the HTML </p>',
         'text' => 'the plain text',
         'from' => 'anna@contoso.com',
         'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
     );

     print_r($params);

     $request = $url.'api/mail.send.json';

     // Generate curl request
     $session = curl_init($request);

     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);

     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

     // obtain response
     $response = curl_exec($session);
     curl_close($session);

     // print everything out
     print_r($response);

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Como: utilizar filtros para ativar rodapés de página, controlo e análise
SendGrid fornece funcionalidades de e-mail adicionais através da utilização de 'filtros'. Estas são as definições que podem ser adicionadas a uma mensagem de e-mail para ativar a funcionalidade específica, como ativar o controlo de clique, do Google analytics, subscrição de controlo e assim sucessivamente.

Os filtros podem ser aplicados a uma mensagem utilizando a propriedade de filtros. Cada filtro especificado por um hash que contém definições específicas do filtro. O exemplo seguinte ativa o filtro de rodapé e especifica uma mensagem de texto que será acrescentada a parte inferior da mensagem de e-mail.
Neste exemplo utilizaremos [sendgrid php biblioteca].
Utilize [compositor] para instalar a biblioteca:

    php composer.phar require sendgrid/sendgrid 2.1.1

Exemplo:    

    <?php
     /*
      * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
      */
     include "vendor/autoload.php";

     $email = new SendGrid\Email();
     // The list of addresses this message will be sent to
     // [This list is used for sending multiple emails using just ONE request to SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');

     // Specify the names of the recipients
     $nameList = array('Name 1', 'Name 2');

     // Used as an example of variable substitution
     $timeList = array('4 PM', '5 PM');

     // Set all of the above variables
     $email->setTos($toList);
     $email->addSubstitution('-name-', $nameList);
     $email->addSubstitution('-time-', $timeList);

     // Specify that this is an initial contact message
     $email->addCategory("initial");

     // You can optionally setup individual filters here, in this example, we have
     // enabled the footer filter
     $email->addFilter('footer', 'enable', 1);
     $email->addFilter('footer', "text/plain", "Thank you for your business");
     $email->addFilter('footer', "text/html", "Thank you for your business");

     // The subject of your email
     $subject = 'Example SendGrid Email';

     // Where is this message coming from. For example, this message can be from
     // support@yourcompany.com, info@yourcompany.com
     $from = 'someone@example.com';

     // If you do not specify a sender list above, you can specifiy the user here. If
     // a sender list IS specified above, this email address becomes irrelevant.
     $to = 'john@contoso.com';

     # Create the body of the message (a plain-text and an HTML version).
     # text is your plain-text email
     # html is your html version of the email
     # if the receiver is able to view html emails then only the html
     # email will be displayed

     /*
      * Note the variable substitution here =)
      */
     $text = "
     Hello -name-,
     Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
     Regards,
     Fred";

     $html = "
     <html>
     <head></head>
     <body>
     <p>Hello -name-,<br>
     Thank you for your interest in our products. We have set up an appointment
     to call you at -time- EST to discuss your needs in more detail.

     Regards,

     Fred<br>
     </p>
     </body>
     </html>";

     // set subject
     $email->setSubject($subject);

     // attach the body of the email
     $email->setFrom($from);
     $email->setHtml($html);
     $email->addTo($to);
     $email->setText($text);

     // Your SendGrid account credentials
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';

     // Create SendGrid object
     $sendgrid = new SendGrid($username, $password);

     // send message
     $response = $sendgrid->send($email);

     print_r($response);

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu as noções básicas do serviço de E-Mail do SendGrid, siga estas ligações para saber mais.

* Documentação do SendGrid: <https://sendgrid.com/docs>
* Biblioteca do SendGrid PHP: <https://github.com/sendgrid/sendgrid-php>
* Oferta especial de SendGrid para clientes do Azure: <https://sendgrid.com/windowsazure.html>

Para obter mais informações, consulte também o [Centro para programadores do PHP](/develop/php/).

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
[special offer]: https://www.sendgrid.com/windowsazure.html
[Packaging and Deploying PHP Applications for Azure]: http://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
[http://swiftmailer.org/download]: http://swiftmailer.org/download
[curl function]: http://php.net/curl
[serviço baseado na nuvem e-mail]: https://sendgrid.com/email-solutions
[entrega de correio eletrónico transacional]: https://sendgrid.com/transactional-email
[sendgrid php biblioteca]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
[compositor]: https://getcomposer.org/download/
