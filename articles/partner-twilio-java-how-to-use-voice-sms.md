---
title: Como utilizar Twilio de voz e SMS (Java) | Microsoft Docs
description: "Saiba como efetuar uma chamada telefónica e enviar uma mensagem SMS com o serviço de API do Twilio no Azure. Exemplos de código escrito em Java."
services: 
documentationcenter: java
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: f3508965-5527-4255-9d51-5d5f926f4d43
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: 5a1b2ffa160a31b639605242b651dc8d14e7a01b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Como utilizar Twilio de voz e funcionalidades SMS em Java
Este guia demonstra como efetuar tarefas de programação comuns com o serviço de API do Twilio no Azure. Os cenários abrangidos incluem a efetuar uma chamada telefónica e enviar uma mensagem de serviço de mensagens curto (SMS). Para obter mais informações sobre Twilio e a utilização de voz e SMS nas suas aplicações, consulte o [passos](#NextSteps) secção.

## <a id="WhatIs"></a>O que é Twilio?
Twilio é uma API de serviço web de telefonia que lhe permite utilizar os idiomas de web existente e competências para criar aplicações de SMS e voz. Twilio é um serviço de terceiros (não uma funcionalidade do Azure e não um produto Microsoft).

**Voz do Twilio** permite que as aplicações tornar e receber chamadas telefónicas. **Twilio SMS** permite que as aplicações tornar e receber mensagens SMS. **Cliente do Twilio** permite que as aplicações ativar a comunicação de voz através de ligações de Internet existentes, incluindo ligações móveis.

## <a id="Pricing"></a>Preços do Twilio e ofertas especiais
Estão disponíveis informações sobre preços do Twilio no [preços do Twilio][twilio_pricing]. Clientes do Azure recebem um [oferta especial][special_offer]: um crédito livre de textos 1000 ou 1000 minutos de entrada. Para inscrever-se nesta oferta ou obter mais informações, visite [http://ahoy.twilio.com/azure][special_offer].

## <a id="Concepts"></a>Conceitos
A API do Twilio é uma API RESTful que fornece uma funcionalidade SMS de voz e de aplicações. Bibliotecas de cliente estão disponíveis em vários idiomas; Para obter uma lista, consulte [Twilio as bibliotecas API][twilio_libraries].

Aspetos fundamentais da Twilio API são verbos do Twilio e Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio verbos
A API permite a utilização de Twilio verbos; Por exemplo, o  **&lt;indiquem&gt;**  verbo dá instruções ao Twilio audibly entregar uma mensagem sobre uma chamada.

Segue-se uma lista de verbos do Twilio.

* **&lt;Marcação&gt;**: liga-se o autor da chamada para outro telemóvel.
* **&lt;Recolher&gt;**: recolhe dígitos numéricos introduzidos no teclado do telefone.
* **&lt;Hangup&gt;**: termina uma chamada.
* **&lt;Reproduzir&gt;**: desempenha um ficheiro de áudio.
* **&lt;Fila&gt;**: adicionar o para uma fila dos chamadores.
* **&lt;Colocar em pausa&gt;**: silenciosamente aguarda que um número de segundos especificado.
* **&lt;Registo&gt;**: regista a voz do emissor e devolve um URL de um ficheiro que contém a gravação.
* **&lt;Redirecionar&gt;**: transfere o controlo de uma chamada ou SMS para o TwiML um URL diferente.
* **&lt;Rejeitar&gt;**: rejeita uma chamada para o seu número de Twilio sem a faturação.
* **&lt;Diga&gt;**: converte texto para reconhecimento de voz que é efetuado numa chamada.
* **&lt;SMS&gt;**: envia uma mensagem SMS.

### <a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções baseado em XML com base nos verbos do Twilio que o informam Twilio como processar uma chamada ou um SMS.

Por exemplo, o seguinte TwiML seria converter o texto **Olá, mundo!** a voz.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

Quando a aplicação chama a API do Twilio, um dos parâmetros de API é o URL que devolve a resposta de TwiML. Para fins de desenvolvimento, pode utilizar os URLs do Twilio fornecidos para fornecer as respostas de TwiML utilizadas pelas suas aplicações. Também pode alojar os suas próprias URLs para produzir as respostas de TwiML e outra opção consiste em utilizar o **TwiMLResponse** objeto.

Para obter mais informações sobre Twilio verbos, os respetivos atributos e TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API do Twilio, consulte [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Criar uma conta do Twilio
Quando estiver pronto para obter uma conta do Twilio, inscreva-se em [tente Twilio][try_twilio]. Pode começar com uma conta gratuita e atualize a sua conta mais tarde.

Quando se inscreve para uma conta do Twilio, receberá um ID de conta e um token de autenticação. Ambos serão necessários para efetuar chamadas de API do Twilio. Para impedir o acesso não autorizado à sua conta, mantenha o seu token de autenticação segura. O ID de conta e a autenticação de token são visíveis no [Twilio consola][twilio_console], nos campos com a etiqueta **SID da conta** e **TOKEN de autenticação**, respetivamente.

## <a id="create_app"></a>Criar uma aplicação de Java
1. Obtenha o JAR do Twilio e adicioná-lo para o caminho de compilação de Java e à assemblagem de implementação WAR. Em [https://github.com/twilio/twilio-java][twilio_java], pode transferir as origens de GitHub e criar a suas próprias JAR ou transferir um JAR pré-criadas (com ou sem dependências).
2. Certifique-se a sua JDK **cacerts** keystore contém o certificado de autoridade de certificação segura Equifax com MD5 identificação digital 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (o número de série está 35:DE:F4:CF e a identificação digital SHA1 D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Este é o certificado de autoridade (AC) de certificado para o [https://api.twilio.com] [ twilio_api_service] serviço, que é chamado quando utilizar APIs do Twilio. Para obter informações sobre a garantir o JDK **cacerts** keystore contém o certificado da AC correto, consulte [a adição de um certificado para o arquivo de certificados de AC de Java][add_ca_cert].

Estão disponíveis instruções detalhadas para utilizar a biblioteca de clientes do Twilio para Java em [como tornar a Twilio de utilização de uma chamada telefónica numa aplicação de Java no Azure][howto_phonecall_java].

## <a id="configure_app"></a>Configurar a sua aplicação utilizar bibliotecas do Twilio
Dentro do seu código, pode adicionar **importar** declarações na parte superior dos seus ficheiros de origem para os pacotes de Twilio ou classes que pretende utilizar na sua aplicação.

Para ficheiros de origem do Java:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

Página de servidor de Java (JSP) para ficheiros de origem:

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
 ```
 
Consoante os pacotes do Twilio ou classes que pretende utilizar, o **importar** instruções poderão ser diferentes.

## <a id="howto_make_call"></a>Como: efetuar uma chamada de saída
O seguinte procedimento mostra como efetuar uma saída chamada utilizando o **chamar** classe. Este código também utiliza um site do Twilio fornecidos para devolver a resposta do Twilio Markup Language (TwiML). Substitua os valores para o **de** e **para** números de telefone e certifique-se de que verifique a **de** número para a sua conta do Twilio antes de executar o código de telefone.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    URI uri = new URI("http://twimlets.com/message" +
            "?Message%5B0%5D=Hello%20World%21");

    // Declare To and From numbers
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Para obter mais informações sobre os parâmetros transmitidos para o **Call.creator** método, consulte [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Conforme mencionado, este código utiliza um site do Twilio fornecidos para devolver a resposta de TwiML. Em vez disso, poderia utilizar o seu próprio site para fornecer a resposta de TwiML; Para obter mais informações, consulte [como fornecer respostas TwiML numa aplicação de Java no Azure](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Como: enviar uma mensagem SMS
O seguinte procedimento mostra como enviar uma mensagem SMS utilizando o **mensagem** classe. O **de** número, **4155992671**, é fornecida pela Twilio para contas de avaliação para enviar mensagens SMS. O **para** número tem de ser verificado para a sua conta do Twilio antes da execução do código.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Declare To and From numbers and the Body of the SMS message
    PhoneNumber to = new PhoneNumber("+14159352345"); // Replace with a valid phone number for your account.
    PhoneNumber from = new PhoneNumber("+14158141829"); // Replace with a valid phone number for your account.
    String body = "Where's Wallace?";

    // Create a Message creator passing From, To and Body values
    // then send the SMS message by calling the create() method
    Message sms = Message.creator(to, from, body).create();
```

Para obter mais informações sobre os parâmetros transmitidos para o **Message.creator** método, consulte [http://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

## <a id="howto_provide_twiml_responses"></a>Como: forneça TwiML respostas a partir do seu próprio site
Quando a aplicação inicia uma chamada à API do Twilio, por exemplo através do **CallCreator.create** método, Twilio enviará o seu pedido para um URL que deverá devolver uma resposta de TwiML. O exemplo acima utiliza o URL do Twilio fornecidos pelo [http://twimlets.com/message][twimlet_message_url]. (Embora TwiML foi concebido para utilização pelos serviços Web, pode ver o TwiML no seu browser. Por exemplo, clique em [http://twimlets.com/message] [ twimlet_message_url] para ver vazio  **&lt;resposta&gt;**  elemento; como outro exemplo, clique em [http://twimlets.com/message?Message%5B0%5D=Hello%20World%21] [ twimlet_message_url_hello_world] para ver um  **&lt;resposta&gt;**  elemento que contenha um  **&lt;indiquem&gt;**  elemento.)

Em vez de depender do Twilio URL fornecido pelo, pode criar o seu próprio site URL que devolve as respostas de HTTP. Pode criar o site em qualquer idioma que devolve as respostas de HTTP Este tópico parte do princípio de que irá alojar o URL numa página de JSP.

A seguinte página JSP resulta numa resposta TwiML que indica que **Olá, mundo!** na chamada.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

A seguinte página JSP resulta numa resposta TwiML que indica algum texto, tem várias coloca em pausa e indica informações sobre a versão da API do Twilio e o nome da função do Azure.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello from Azure!</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>
```

O **ApiVersion** parâmetro está disponível em pedidos de voz do Twilio (não pedidos SMS). Para ver os parâmetros do pedido disponíveis para a voz do Twilio e pedidos SMS, consulte <https://www.twilio.com/docs/api/twiml/twilio_request> e <https://www.twilio.com/docs/api/twiml/sms/twilio_request>, respetivamente. O **RoleName** variável de ambiente está disponível como parte de uma implementação do Azure. (Se pretender adicionar variáveis de ambiente personalizadas, pelo que podem ser captados **System.getenv**, consulte a secção de variáveis de ambiente no [diversas definições de configuração de função][misc_role_config_settings].)

Assim que tiver a sua página JSP configurado para dar respostas TwiML, utilizar o URL da página de JSP como o URL transmitido a **Call.creator** método. Por exemplo, se tiver uma aplicação Web com o nome, serviço, alojado no MyTwiML implementada do Azure e o nome da página JSP é mytwiml.jsp, o URL pode ser transferido para **Call.creator** conforme é mostrado a seguir:

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Outra opção para responder com TwiML é através de **VoiceResponse** classe, que está disponível no **com.twilio.twiml** pacote.

Para obter informações adicionais sobre a utilização do Twilio no Azure com Java, consulte [como tornar a Twilio de utilização de uma chamada telefónica numa aplicação de Java no Azure][howto_phonecall_java].

## <a id="AdditionalServices"></a>Como: utilizar os serviços do Twilio adicionais
Para além dos exemplos mostrados aqui, Twilio oferece APIs baseadas na web que pode utilizar para tirar partido das funcionalidades adicionais do Twilio da sua aplicação do Azure. Para mais informações, consulte o [documentação da API do Twilio][twilio_api_documentation].

## <a id="NextSteps"></a>Passos Seguintes
Agora que aprendeu as noções básicas do serviço do Twilio, siga estas ligações para saber mais:

* [Diretrizes de segurança do Twilio][twilio_security_guidelines]
* [De Twilio HowTo e código de exemplo][twilio_howtos]
* [Tutoriais de início rápido do Twilio][twilio_quickstarts]
* [Twilio no GitHub][twilio_on_github]
* [Comunicar com o suporte do Twilio][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World%21
[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: http://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/docs
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
