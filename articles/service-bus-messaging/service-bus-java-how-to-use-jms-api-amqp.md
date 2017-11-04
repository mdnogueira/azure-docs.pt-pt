---
title: "Como utilizar AMQP 1.0 com a API de barramento de serviço do Java | Microsoft Docs"
description: "Como utilizar o serviço de mensagens de Java (JMS) com o Service Bus do Azure e avançadas mensagem colocação Protodol (AMQP) 1.0."
services: service-bus-messaging
documentationcenter: java
author: sethmanheim
manager: timlt
editor: 
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 0848facd764c4fb0d7f95c1ae89ecb02a32257e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>Como utilizar o Java mensagem de serviço (JMS) API com o Service Bus e AMQP 1.0
O Advanced mensagem colocação protocolo (AMQP) 1.0 é um protocolo de mensagens económicos, fiável e nível de rede que pode utilizar para criar aplicações robustas, entre plataformas de processamento de mensagens.

Suporte para AMQP 1.0 no Service Bus significa que pode utilizar a colocação em fila e publicar/subscrever funcionalidades de mensagens mediadas de uma variedade de plataformas utilizando um protocolo de binário eficiente. Além disso, pode criar aplicações compostas por componentes construido utilizando uma combinação de idiomas, estruturas e sistemas operativos.

Este artigo explica como utilizar o Service Bus (filas e tópicos de publicação/subscrição) de funcionalidades de mensagens de aplicações Java utilizando o populares Java mensagem de serviço (JMS) API padrão. Não existe um [artigo complementar](service-bus-amqp-dotnet.md) que explica como para o fazer utilizando a API .NET do Service Bus. Pode utilizar estes guias de dois em conjunto para saber mais sobre a plataforma de mensagens através de AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Introdução ao Service Bus
Este guia pressupõe que já tem um espaço de nomes de barramento de serviço que contém uma fila com o nome **queue1**. Se não o fizer, em seguida, pode [criar o espaço de nomes e fila](service-bus-create-namespace-portal.md) utilizando o [portal do Azure](https://portal.azure.com). Para obter mais informações sobre como criar espaços de nomes do Service Bus e pelas filas, consulte [introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Particionada filas e tópicos também suportam AMQP. Para obter mais informações, consulte [entidades de mensagens Particionadas](service-bus-partitioning.md) e [AMQP 1.0 suporte para o Service Bus particionada filas e tópicos](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Transferir a biblioteca de cliente AMQP 1.0 JMS
Para obter informações sobre onde pode transferir a versão mais recente da biblioteca de clientes do Apache Qpid JMS AMQP 1.0, visite [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html).

Tem de adicionar os seguintes ficheiros JAR quatro do arquivo de distribuição Apache Qpid JMS AMQP 1.0 para o Java CLASSPATH quando criar e executar aplicações de JMS com o Service Bus:

* geronimo jms\_1.1\_spec 1.0.jar
* qpid-amqp-1-0-Client-[Version].JAR
* qpid-amqp-1-0-Client-jms-[Version].JAR
* qpid-amqp-1-0-Common-[Version].JAR

## <a name="coding-java-applications"></a>Programação de aplicações Java
### <a name="java-naming-and-directory-interface-jndi"></a>Atribuição de nomes de Java e Interface de diretório (JNDI)
JMS utiliza a atribuição de nomes de Java e o diretório de Interface (JNDI) para criar uma separação entre nomes de lógicos e físico. Dois tipos de objetos JMS são resolvidos utilizando JNDI: ConnectionFactory e de destino. JNDI utiliza um modelo de fornecedor na qual pode fixação serviços de diretório diferente para processar as tarefas de resolução do nome. Formato do Apache Qpid JMS AMQP 1.0 biblioteca é fornecido com um simples propriedades fornecedor JNDI baseados em ficheiros, que é configurado através de um ficheiro de propriedades dos seguintes:

```
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="configure-the-connectionfactory"></a>Configurar o ConnectionFactory
A entrada utilizada para definir um **ConnectionFactory** no ficheiro de propriedades de Qpid fornecedor JNDI é o seguinte formato:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Onde **[jndi_name]** e **[ConnectionURL]** ter significados os seguintes:

* **[jndi_name]** : O nome lógico do ConnectionFactory. Este é o nome que irá ser resolvido na aplicação Java utilizando o método JNDI IntialContext.lookup().
* **[ConnectionURL]** : Um URL que fornece a biblioteca JMS com as informações necessárias para o mediador AMQP.

O formato do **ConnectionURL** é o seguinte:

```
amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
```
Onde **[espaço de nomes]**, **[SASPolicyName]** e **[SASPolicyKey]** ter significados os seguintes:

* **[espaço de nomes]** : Espaço de nomes do Service Bus.
* **[SASPolicyName]** : Nome da política de assinatura de acesso partilhado a fila.
* **[SASPolicyKey]** : Chave de política de assinatura de acesso partilhado a fila.

> [!NOTE]
> Tem de codificar o URL a palavra-passe manualmente. Um utilitário de codificação do URL útil está disponível em [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).
> 
> 

#### <a name="configure-destinations"></a>Configurar destinos
A entrada utilizada para definir um destino no fornecedor JNDI do ficheiro de propriedades de Qpid é o seguinte formato:

```
queue.[jndi_name] = [physical_name]
```

ou

```
topic.[jndi_name] = [physical_name]
```

Onde **[jndi\_nome]** e **[físico\_nome]** ter significados os seguintes:

* **[jndi_name]** : O nome lógico de destino. Este é o nome que irá ser resolvido na aplicação Java utilizando o método JNDI IntialContext.lookup().
* **[physical_name]** : O nome da entidade de barramento de serviço para a qual a aplicação envia ou recebe mensagens.

> [!NOTE]
> Se receber de uma subscrição de tópico de barramento de serviço, o nome físico especificado na JNDI deve ser o nome do tópico. O nome da subscrição é fornecido quando é criada a subscrição durável no código da aplicação JMS. O [guia para programadores 1.0 AMQP Bus Service](service-bus-amqp-dotnet.md) fornece mais detalhes sobre como trabalhar com tópicos do Service Bus do JMS.
> 
> 

### <a name="write-the-jms-application"></a>Escreva a aplicação de JMS
Não são especial APIs ou opções obrigatório quando usar JMS com o Service Bus. No entanto, existem algumas restrições que vai ser abordadas mais tarde. Tal como em qualquer aplicação JMS, é primeiro thing necessários, configuração do ambiente JNDI, para conseguir resolver um **ConnectionFactory** e destinos.

#### <a name="configure-the-jndi-initialcontext"></a>Configurar o InitialContext JNDI
O ambiente de JNDI é configurado através da transmissão de uma tabela hash de informações de configuração para o construtor de classe javax.naming.InitialContext. As duas necessário elementos na tabela hash de são o nome de classe da fábrica de contexto inicial e o URL do fornecedor. O código seguinte mostra como configurar o ambiente de JNDI utilizar Qpid baseada em ficheiros de propriedades JNDI fornecedor com um ficheiro de propriedades com o nome **servicebus.properties**.

```java
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Uma aplicação de JMS simples utilizando uma fila do Service Bus
O programa de exemplo seguintes envia JMS TextMessages para uma fila do Service Bus com o nome lógico JNDI da fila e recebe as mensagens de volta.

```java
// SimpleSenderReceiver.java

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Hashtable;
import java.util.Random;

public class SimpleSenderReceiver implements MessageListener {
    private static boolean runReceiver = true;
    private Connection connection;
    private Session sendSession;
    private Session receiveSession;
    private MessageProducer sender;
    private MessageConsumer receiver;
    private static Random randomGenerator = new Random();

    public SimpleSenderReceiver() throws Exception {
        // Configure JNDI environment
        Hashtable<String, String> env = new Hashtable<String, String>();
        env.put(Context.INITIAL_CONTEXT_FACTORY, 
                   "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
        env.put(Context.PROVIDER_URL, "servicebus.properties");
        Context context = new InitialContext(env);

        // Look up ConnectionFactory and Queue
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        Destination queue = (Destination) context.lookup("QUEUE");

        // Create Connection
        connection = cf.createConnection();

        // Create sender-side Session and MessageProducer
        sendSession = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        sender = sendSession.createProducer(queue);

        if (runReceiver) {
            // Create receiver-side Session, MessageConsumer,and MessageListener
            receiveSession = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            receiver = receiveSession.createConsumer(queue);
            receiver.setMessageListener(this);
            connection.start();
        }
    }

    public static void main(String[] args) {
        try {

            if ((args.length > 0) && args[0].equalsIgnoreCase("sendonly")) {
                runReceiver = false;
            }

            SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
            System.out.println("Press [enter] to send a message. Type 'exit' + [enter] to quit.");
            BufferedReader commandLine = new java.io.BufferedReader(new InputStreamReader(System.in));

            while (true) {
                String s = commandLine.readLine();
                if (s.equalsIgnoreCase("exit")) {
                    simpleSenderReceiver.close();
                    System.exit(0);
                } else {
                    simpleSenderReceiver.sendMessage();
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private void sendMessage() throws JMSException {
        TextMessage message = sendSession.createTextMessage();
        message.setText("Test AMQP message from JMS");
        long randomMessageID = randomGenerator.nextLong() >>>1;
        message.setJMSMessageID("ID:" + randomMessageID);
        sender.send(message);
        System.out.println("Sent message with JMSMessageID = " + message.getJMSMessageID());
    }

    public void close() throws JMSException {
        connection.close();
    }

    public void onMessage(Message message) {
        try {
            System.out.println("Received message with JMSMessageID = " + message.getJMSMessageID());
            message.acknowledge();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}    
```

### <a name="run-the-application"></a>Executar a aplicação
Executar a aplicação produz saída com o formato:

```
> java SimpleSenderReceiver
Press [enter] to send a message. Type 'exit' + [enter] to quit.

Sent message with JMSMessageID = ID:2867600614942270318
Received message with JMSMessageID = ID:2867600614942270318

Sent message with JMSMessageID = ID:7578408152750301483
Received message with JMSMessageID = ID:7578408152750301483

Sent message with JMSMessageID = ID:956102171969368961
Received message with JMSMessageID = ID:956102171969368961
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>Plataforma de mensagens entre JMS e .NET
Este guia mostrou como enviar e receber mensagens e de barramento de serviço utilizando JMS. No entanto, uma das principais vantagens de AMQP 1.0 é que permite que as aplicações que serão criados dos componentes escritos em idiomas diferentes, com mensagens trocadas em fidelidade completa e fiável.

Utilizar a aplicação de JMS exemplo descrito acima e uma aplicação .NET semelhante obtidas a partir de um artigo de complementar [utilizando o Service Bus a partir do .NET com AMQP 1.0](service-bus-amqp-dotnet.md), podem trocar mensagens entre .NET e Java. Leia este artigo para obter mais informações sobre os detalhes da plataforma mensagens utilizando o Service Bus e AMQP 1.0.

### <a name="jms-to-net"></a>JMS para .NET
Para demonstrar JMS para .NET de mensagens:

* Inicie a aplicação de exemplo .NET sem quaisquer argumentos da linha de comandos.
* Inicie a aplicação de exemplo de Java com o argumento da linha de comandos "sendonly". Neste modo, a aplicação não irá receber mensagens da fila, só irá enviar.
* Prima **Enter** algumas vezes na consola de aplicação Java, que fará com que as mensagens a enviar.
* Estas mensagens são recebidas pela aplicação .NET.

#### <a name="output-from-jms-application"></a>Resultado da aplicação JMS
```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### <a name="output-from-net-application"></a>Resultado da aplicação .NET
```
> SimpleSenderReceiver.exe    
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### <a name="net-to-jms"></a>.NET para JMS
Para demonstrar .NET para JMS mensagens:

* Inicie a aplicação de exemplo .NET com o argumento da linha de comandos "sendonly". Neste modo, a aplicação não irá receber mensagens da fila, só irá enviar.
* Inicie a aplicação de exemplo de Java sem quaisquer argumentos da linha de comandos.
* Prima **Enter** algumas vezes na consola de aplicação .NET, que fará com que as mensagens a enviar.
* Estas mensagens são recebidas pela aplicação de Java.

#### <a name="output-from-net-application"></a>Resultado da aplicação .NET
```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3    
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### <a name="output-from-jms-application"></a>Resultado da aplicação JMS
```
> java SimpleSenderReceiver    
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## <a name="unsupported-features-and-restrictions"></a>Restrições e funcionalidades não suportadas
Existem as seguintes restrições ao utilizar JMS através de AMQP 1.0 com o Service Bus, nomeadamente:

* Apenas um **MessageProducer** ou **MessageConsumer** é permitida por **sessão**. Se precisar de criar vários **MessageProducers** ou **MessageConsumers** numa aplicação, crie um dedicado **sessão** para cada um deles.
* Subscrições de tópico voláteis não são atualmente suportadas.
* **MessageSelectors** não são atualmente suportadas.
* Destinos temporários; Por exemplo, **TemporaryQueue**, **TemporaryTopic** não são atualmente suportadas, along com o **QueueRequestor** e **TopicRequestor** APIs que as utilizam.
* Sessões transacionadas e transações distribuídas não são suportadas.

## <a name="summary"></a>Resumo
Este guia de procedimentos mostrou como utilizar mediadas do Service Bus mensagens funcionalidades (filas e tópicos de publicação/subscrição) de Java utilizando a API de JMS populares e AMQP 1.0.

Também pode utilizar o Service Bus AMQP 1.0 de outros idiomas, incluindo .NET, C, Python e PHP. Componentes construido utilizando estes idiomas diferentes podem trocar mensagens fiável e em fidelidade completa através de AMQP 1.0 suportar no Service Bus.

## <a name="next-steps"></a>Passos seguintes
* [Suporte de AMQP 1.0 no Service Bus do Azure](service-bus-amqp-overview.md)
* [Como utilizar AMQP 1.0 com a API .NET do Service Bus](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus AMQP 1.0 Guia para programadores](service-bus-amqp-dotnet.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Centro de Programadores do Java](https://azure.microsoft.com/develop/java/)

