---
title: "Como utilizar o serviço de correio eletrónico do SendGrid (Java) | Microsoft Docs"
description: "Saiba como enviar correio eletrónico com o serviço de correio eletrónico do SendGrid no Azure. Exemplos de código escrito em Java."
services: 
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: cc75c43e-ede9-492b-98c2-9147fcb92c21
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork
ms.openlocfilehash: 85a0e302626ca14ac039ee6f662f372ddbeb62c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>Como enviar E-mails utilizando a SendGrid de Java
Este guia demonstra como efetuar tarefas de programação comuns com o serviço de correio eletrónico do SendGrid no Azure. Os exemplos são escritos em Java. Os cenários abrangidos incluem **construir e-mail**, **enviar correio eletrónico**, **adicionar anexos**, **utilizando filtros**e **atualizar propriedades**. Para obter mais informações sobre SendGrid e enviar correio eletrónico, consulte o [passos](#next-steps) secção.

## <a name="what-is-the-sendgrid-email-service"></a>O que é o serviço de correio eletrónico do SendGrid?
SendGrid é um [serviço baseado na nuvem e-mail] que fornece fiável [entrega de correio eletrónico transacional], escalabilidade e a análise em tempo real, juntamente com APIs flexíveis que o facilitar a integração personalizados. Cenários comuns de utilização do SendGrid incluem:

* Enviar automaticamente os recibos aos clientes
* Administrar distribuição lista para enviar aos clientes i-fliers mensais e ofertas especiais
* Recolher métricas em tempo real para coisas como bloqueado por correio eletrónico e a capacidade de resposta do cliente
* Gerar relatórios para ajudar a identificar tendências
* Pedidos de cliente de reencaminhamento
* Notificações de correio eletrónico da sua aplicação

Para obter mais informações, consulte <http://sendgrid.com>.

## <a name="create-a-sendgrid-account"></a>Criar uma conta do SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>Como: utilizar as bibliotecas de javax.mail
Obtenha as bibliotecas de javax.mail, por exemplo do <http://www.oracle.com/technetwork/java/javamail> e importe-as para o seu código. A um nível elevado, o processo para utilizar a biblioteca de javax.mail para enviar correio eletrónico através de SMTP é fazer o seguinte:

1. Especifique os valores de SMTP, incluindo o servidor SMTP, que para SendGrid smtp.sendgrid.net.

```
        import java.util.Properties;
        import javax.activation.*;
        import javax.mail.*;
        import javax.mail.internet.*;

        public class MyEmailer {
           private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
           private static final String SMTP_AUTH_USER = "your_sendgrid_username";
           private static final String SMTP_AUTH_PWD = "your_sendgrid_password";

           public static void main(String[] args) throws Exception{
               new MyEmailer().SendMail();
           }

           public void SendMail() throws Exception
           {
              Properties properties = new Properties();
                 properties.put("mail.transport.protocol", "smtp");
                 properties.put("mail.smtp.host", SMTP_HOST_NAME);
                 properties.put("mail.smtp.port", 587);
                 properties.put("mail.smtp.auth", "true");
                 // …
```

1. Expandir o *javax.mail.Authenticator* classe e na sua implementação do *getPasswordAuthentication* método, devolver o nome de utilizador do SendGrid e a palavra-passe.  

       private class SMTPAuthenticator extends javax.mail.Authenticator {
       public PasswordAuthentication getPasswordAuthentication() {
          String username = SMTP_AUTH_USER;
          String password = SMTP_AUTH_PWD;
          return new PasswordAuthentication(username, password);
       }
2. Criar uma sessão de e-mail autenticado através de um *javax.mail.Session* objeto.  

       Authenticator auth = new SMTPAuthenticator();
       Session mailSession = Session.getDefaultInstance(properties, auth);
3. Crie a sua mensagem e atribuir **para**, **de**, **requerente** e valores de conteúdo. Isto é apresentado no [como: criar uma mensagem de E-Mail](#how-to-create-an-email) secção.
4. Enviar a mensagem através de um *javax.mail.Transport* objeto. Isto é apresentado na [como: enviar um E-Mail] [como: enviar um E-Mail] secção.

## <a name="how-to-create-an-email"></a>Como: criar uma mensagem de e-mail
O seguinte mostra como especificar valores para uma mensagem de e-mail.

    MimeMessage message = new MimeMessage(mailSession);
    Multipart multipart = new MimeMultipart("alternative");
    BodyPart part1 = new MimeBodyPart();
    part1.setText("Hello, Your Contoso order has shipped. Thank you, John");
    BodyPart part2 = new MimeBodyPart();
    part2.setContent(
        "<p>Hello,</p>
        <p>Your Contoso order has <b>shipped</b>.</p>
        <p>Thank you,<br>John</br></p>",
        "text/html");
    multipart.addBodyPart(part1);
    multipart.addBodyPart(part2);
    message.setFrom(new InternetAddress("john@contoso.com"));
    message.addRecipient(Message.RecipientType.TO,
       new InternetAddress("someone@example.com"));
    message.setSubject("Your recent order");
    message.setContent(multipart);

## <a name="how-to-send-an-email"></a>Como: enviar uma mensagem de e-mail
O seguinte mostra como enviar uma mensagem de e-mail.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="how-to-add-an-attachment"></a>Como: adicionar um anexo
O código seguinte mostra como adicionar um anexo.

    // Local file name and path.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\\myfiles\\";
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Specify the local file to attach.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // This example uses the local file name as the attachment name.
    // They could be different if you prefer.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Como: utilizar filtros para ativar rodapés de página, controlo e análise
SendGrid fornece funcionalidades de e-mail adicionais através da utilização de *filtros*. Estas são as definições que podem ser adicionadas a uma mensagem de e-mail para ativar a funcionalidade específica, como ativar o controlo de clique, do Google analytics, subscrição de controlo e assim sucessivamente. Para obter uma lista completa de filtros, consulte [definições de filtro][Filter Settings].

* O seguinte mostra como inserir um filtro de rodapé resulta na apresentação na parte inferior da mensagem de e-mail a enviar de texto HTML.

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"footer\":
          {\"settings\":
          {\"enable\":1,\"text/html\":
          \"<html><b>Thank you</b> for your business.</html>\"}}}}");
* Outro exemplo de um filtro é clique controlo. Digamos que o texto de e-mail contém uma hiperligação, tais como o seguinte, e pretende controlar a taxa de clique:

      messagePart.setContent(
          "Hello,
          <p>This is the body of the message. Visit
          <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
          Thank you.",
          "text/html");
* Para ativar o controlo, clique em, utilize o seguinte código:

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"clicktrack\":
          {\"settings\":
          {\"enable\":1}}}}");

## <a name="how-to-update-email-properties"></a>Como: propriedades de correio eletrónico de atualização
Algumas propriedades de correio eletrónico podem ser substituídas utilizando  **definir*propriedade** * ou estar anexados utilizando  **adicionar*propriedade** *.

Por exemplo, para especificar **ReplyTo** endereços, utilize o seguinte:

    InternetAddress addresses[] =
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

Para adicionar um **Cc** destinatário, utilize o seguinte:

    message.addRecipient(Message.RecipientType.CC, new
    InternetAddress("john@contoso.com"));

## <a name="how-to-use-additional-sendgrid-services"></a>Como: utilizar os serviços do SendGrid adicionais
SendGrid oferece APIs baseadas na web que pode utilizar para tirar partido das funcionalidades adicionais do SendGrid da sua aplicação do Azure. Para mais informações, consulte o [documentação da API do SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu as noções básicas do serviço de E-Mail do SendGrid, siga estas ligações para saber mais.

* Amostra que demonstra utilizando SendGrid numa implementação do Azure: [como enviar e-mails utilizando SendGrid de Java numa implementação do Azure](store-sendgrid-java-how-to-send-email-example.md)
* SDK de Java do SendGrid: <https://sendgrid.com/docs/Code_Examples/java.html>
* Documentação da API do SendGrid: <https://sendgrid.com/docs/API_Reference/index.html>
* Oferta especial de SendGrid para clientes do Azure: <https://sendgrid.com/windowsazure.html>

[http://sendgrid.com]: https://sendgrid.com
[http://sendgrid.com/pricing.html]: http://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[http://sendgrid.com/features]: https://sendgrid.com/features
[http://www.oracle.com/technetwork/java/javamail]: http://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[http://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[serviço baseado na nuvem e-mail]: https://sendgrid.com/email-solutions
[entrega de correio eletrónico transacional]: https://sendgrid.com/transactional-email
