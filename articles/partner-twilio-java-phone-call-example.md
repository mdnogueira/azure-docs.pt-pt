---
title: "Como efetuar uma chamada telefónica do Twilio (Java) | Microsoft Docs"
description: "Saiba como efetuar uma chamada telefónica de uma página web utilizando Twilio numa aplicação de Java no Azure."
services: 
documentationcenter: java
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: 0381789e-e775-41a0-a784-294275192b1d
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: 04ecb80a2a9e15b549b47138caf71c7e64bda500
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Como efetuar uma chamada telefónica utilizar Twilio numa aplicação Java no Azure
O exemplo seguinte mostra como pode utilizar Twilio para efetuar uma chamada de uma página web alojado no Azure. A aplicação resultante pedirá ao utilizador para os valores de chamada telefónica, conforme mostrado na captura de ecrã seguinte.

![Formato de chamada do Azure utilizando Twilio e o Java][twilio_java]

Terá de fazer o seguinte para utilizar o código neste tópico:

1. Adquirir uma conta do Twilio e a autenticação de token. Para obter uma introdução Twilio, avalie preços em [http://www.twilio.com/pricing][twilio_pricing]. Pode inscrever-se em [https://www.twilio.com/try-twilio][try_twilio]. Para obter informações sobre a API fornecida pelo Twilio, consulte [http://www.twilio.com/api][twilio_api].
2. Obter o Twilio JAR. Em [https://github.com/twilio/twilio-java][twilio_java_github], pode transferir as origens de GitHub e criar a suas próprias JAR ou transferir um JAR pré-criadas (com ou sem dependências).
   O código neste tópico foi escrito utilizando o JAR TwilioJava 3.3.8 com dependências previamente concebido.
3. Adicione o JAR para o caminho de compilação de Java.
4. Se estiver a utilizar Eclipse para criar esta aplicação de Java, incluem o Twilio JAR no seu ficheiro de implementação de aplicação (WAR) utilizando a funcionalidade de assemblagem de implementação do Eclipse. Se não estiver a utilizar Eclipse para criar esta aplicação de Java, certifique-se de que o JAR do Twilio é incluído na mesma função do Azure que a aplicação de Java e adicionada para o caminho de classe da sua aplicação.
5. Certifique-se de que o keystore cacerts contém o certificado de autoridade de certificação segura Equifax com MD5 identificação digital 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (o número de série é 35:DE:F4:CF e a identificação digital SHA1 é D2:32:09:AD:23:D 3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Este é o certificado de autoridade (AC) de certificado para o [https://api.twilio.com] [ twilio_api_service] serviço, que é chamado quando utilizar APIs do Twilio. Para obter informações sobre como adicionar este certificado da AC para arquivo do seu JDK cacert, consulte [a adição de um certificado para o arquivo de certificados de AC do Java][add_ca_cert].

Além disso, familiaridade com as informações no [criar um Olá mundo aplicação utilizando o Toolkit do Azure para o Eclipse][azure_java_eclipse_hello_world], ou com outras técnicas para alojar aplicações Java no Azure, se for não utilizar Eclipse, é altamente recomendável.

## <a name="create-a-web-form-for-making-a-call"></a>Criar um formulário web para efetuar uma chamada
O código seguinte mostra como criar um formulário web para obter dados de utilizador para efetuar uma chamada. Para efeitos deste exemplo, um novo projeto web dinâmico, com o nome **TwilioCloud**, foi criado, e **callform.jsp** foi adicionado como um ficheiro JSP.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>Criar o código para efetuar a chamada
O código seguinte, o que é chamado quando o utilizador conclui o formulário apresentado pela callform.jsp, cria a mensagem de chamada e gera a chamada. Para efeitos deste exemplo, o ficheiro JSP é denominado **makecall.jsp** e foi adicionada para o **TwilioCloud** projeto. (Utilize a conta do Twilio e a autenticação de token em vez dos valores de marcador de posição atribuídos a **accountSID** e **authToken** no código abaixo.)

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";

         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");

         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");

         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");

         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");

         // Create a URL using the Twilio message and the user-entered text.
         String Url="http://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;

         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");

         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);

         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

Para além de efetuar a chamada, makecall.jsp apresenta o ponto final do Twilio, versão de API e o estado de chamada. Um exemplo é a captura de ecrã seguinte:

![Resposta de chamada do Azure utilizando Twilio e o Java][twilio_java_response]

## <a name="run-the-application"></a>Executar a aplicação
Seguem-se os passos de alto nível para executar a sua aplicação; os detalhes para estes passos podem ser encontrados em [criar um Olá mundo aplicação utilizando o Toolkit do Azure para o Eclipse][azure_java_eclipse_hello_world].

1. Exportar WAR sua TwilioCloud para o Azure **approot** pasta. 
2. Modificar **startup.cmd** para descomprimir o WAR TwilioCloud.
3. Compile a aplicação para o emulador de computação.
4. Inicie a implementação no emulador de computação.
5. Abra um browser e execute **http://localhost:8080/TwilioCloud/callform.jsp**.
6. Introduza valores no formulário, clique em **tornar esta chamada**e, em seguida, ver os resultados no makecall.jsp.

Quando estiver pronto para implementar no Azure, recompilação para implementação na nuvem, implementar no Azure e execute http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp no browser (substitua o valor para  *your_hosted_name*).

## <a name="next-steps"></a>Passos seguintes
Este código foi fornecido para lhe mostrar funcionalidades básicas utilizando Twilio em Java no Azure. Antes de implementar no Azure em produção, poderá querer adicionar mais processamento de erros ou outras funcionalidades. Por exemplo:

* Em vez de utilizar um formulário web, pode utilizar blobs de armazenamento do Azure ou a base de dados SQL para armazenar os números de telefone e chamar texto. Para obter informações sobre a utilização de blobs de armazenamento do Azure em Java, consulte [como utilizar o serviço de armazenamento de BLOBs de Java][howto_blob_storage_java]. Para obter informações sobre como utilizar a base de dados do SQL em Java, consulte [utilizando a base de dados SQL em Java][howto_sql_azure_java].
* Pode utilizar **RoleEnvironment.getConfigurationSettings** para obter o Twilio ID da conta e a autenticação de token de definições de configuração da implementação, em vez de pré-programar os valores existentes na makecall.jsp. Para obter informações sobre o **RoleEnvironment** classe, consulte [utilizando a biblioteca de tempo de execução do serviço do Azure no JSP] [ azure_runtime_jsp] e a documentação do pacote de Runtime de serviço do Azure em [http://dl.windowsazure.com/javadoc][azure_javadoc].
* O código de makecall.jsp atribui um URL Twilio fornecidos pelo [http://twimlets.com/message][twimlet_message_url], como o **Url** variável. Este URL fornece uma resposta do Twilio Markup Language (TwiML) que informa Twilio como continuar com a chamada. Por exemplo, pode conter TwiML que é devolvido um  **&lt;indiquem&gt;**  verbo que resulta em texto de autenticação a ser ditas para o destinatário da chamada. Em vez de utilizar o URL do Twilio fornecidos pelo, foi possível criar o seu próprio serviço para responder ao pedido do Twilio; Para obter mais informações, consulte [como Twilio de utilização de voz e funcionalidades de SMS em Java][howto_twilio_voice_sms_java]. Obter mais informações sobre TwiML podem ser encontradas em [http://www.twilio.com/docs/api/twiml][twiml]e mais informações sobre  **&lt;indiquem&gt;**  e outros verbos Twilio podem ser encontrados em [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Leia as diretrizes de segurança do Twilio em [https://www.twilio.com/docs/security][twilio_docs_security].

Para obter informações adicionais sobre Twilio, consulte [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Veja Também
* [Como utilizar Twilio de voz e funcionalidades SMS em Java][howto_twilio_voice_sms_java]
* [Adicionar um certificado para o arquivo de certificados de AC de Java][add_ca_cert]

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: http://github.com/twilio/twilio-java
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: http://msdn.microsoft.com/library/windowsazure/hh690944.aspx
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: http://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: http://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: http://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[azure_javadoc]: http://dl.windowsazure.com/javadoc
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
