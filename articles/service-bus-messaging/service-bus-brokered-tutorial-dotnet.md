---
title: Tutorial de .NET de mensagens mediadas do e Service Bus | Microsoft Docs
description: Tutorial de .NET de mensagens mediadas.
services: service-bus
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 964e019a-8abe-42f3-8314-867010cb2608
ms.service: service-bus
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/27/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 57aec98a681e1cb5d75f910427975c6c3a1728c3
ms.openlocfilehash: d437ad6300970bd1f015413b8ad70620e2e7fd04


---
# <a name="service-bus-brokered-messaging-net-tutorial"></a>Tutorial de .NET de mensagens mediadas do Service Bus
O Service Bus do Azure fornece duas soluções de mensagens abrangentes – uma através de um serviço de “reencaminhamento” centralizado em execução na nuvem que suporta uma variedade de diferentes protocolos de transporte e padrões de serviços Web, incluindo SOAP, WS-* e REST. O cliente não precisa de uma ligação direta para o serviço no local, nem precisa de saber onde reside o serviço, e o serviço no local não precisa de nenhuma porta de entrada aberta na firewall.

A segunda solução de mensagens permite capacidades de mensagens “mediadas”. Estas podem ser consideradas assíncronas ou funcionalidades de mensagens desacopladas que suportam cenários de publicação-subscrição, desacoplamento temporal e balanceamento de carga através da infraestrutura de mensagens do Service Bus. A comunicação desacoplada tem muitas vantagens; por exemplo, os clientes e os servidores podem ligar-se conforme necessário e efetuar as operações de forma assíncrona.

Este tutorial destina-se a fornecer uma descrição geral e uma experiência prática com as filas, um dos principais componentes das mensagens mediadas do Service Bus. Após completar a sequência de tópicos deste tutorial, terá uma aplicação que preenche uma lista de mensagens, cria uma fila e envia mensagens para essa fila. Por fim, a aplicação recebe e apresenta as mensagens a partir da fila e, em seguida, limpa os respetivos recursos e sai. Para um tutorial correspondente que descreve como criar uma aplicação que utiliza o Reencaminhamento de WCF do Service Bus, veja o [Service Bus relayed messaging tutorial (Tutorial das mensagens retransmitidas do Service Bus)](../service-bus-relay/service-bus-relay-tutorial.md).

## <a name="introduction-and-prerequisites"></a>Introdução e pré-requisitos
As filas oferecem uma entrega de mensagem primeiro a entrar, primeiro a sair (FIFO) para um ou mais consumidores concorrentes. FIFO significa que, normalmente, espera-se que as mensagens sejam recebidas e processadas pelos recetores na ordem temporal em que foram colocadas em fila e cada mensagem será recebida e processada por apenas um consumidor de mensagens. Uma vantagem principal de utilizar as filas é obter o *desacoplamento temporal* dos componentes da aplicação: por outras palavras, os produtores e os consumidores não precisam de enviar e receber mensagens ao mesmo tempo, uma vez que as mensagens são armazenadas de forma duradoura na fila. Uma vantagem relacionada é o *nivelamento de carga*, que permite aos produtores e aos consumidores enviar e receber mensagens a taxas diferentes.

Seguem-se alguns passos administrativos e pré-requisitos que deverá seguir antes de iniciar o tutorial. O primeiro passo é criar um espaço de nomes de serviço e obter uma chave de assinatura de acesso partilhado (SAS). Um espaço de nomes proporciona um limite de aplicação para cada aplicação exposta através do Service Bus. O sistema gera uma chave SAS automaticamente quando se cria um espaço de nomes de serviço. A combinação do espaço de nomes de serviço e da chave SAS fornece uma credencial com a qual o Service Bus autentica o acesso a uma aplicação.

### <a name="create-a-service-namespace-and-obtain-a-sas-key"></a>Criar um espaço de nomes de serviço e obter uma chave SAS
O primeiro passo é criar um espaço de nomes de serviço e obter uma chave de [Assinatura de Acesso Partilhado](service-bus-sas-overview.md) (SAS). Um espaço de nomes fornece um limite de aplicação para cada aplicação exposta através do Service Bus. O sistema gera uma chave SAS automaticamente quando se cria um espaço de nomes de serviço. A combinação do espaço de nomes de serviço e da chave SAS fornece uma credencial do Service Bus para autenticar o acesso a uma aplicação.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

O passo seguinte consiste em criar um projeto do Visual Studio e escrever duas funções de programa auxiliar que carregam uma lista delimitada por vírgulas num objeto [Lista](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) [BrokeredMessage](https://msdn.microsoft.com/library/6sh2ey19.aspx) de .NET de tipo seguro.

### <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio
1. Abra o Visual Studio como administrador, para tal clique com o botão direito no menu Iniciar e clique em **Executar como administrador**.
2. Crie um novo projeto de aplicação de consola. Clique no menu **Ficheiro**, selecione **Novo** e clique em **Projeto**. Na caixa de diálogo **Novo Projeto**, clique em **Visual C#** (caso **Visual C#** não seja apresentado, procure em **Outras Linguagens**), clique no modelo **Aplicação de Consola** e atribua-lhe o nome **QueueSample**. Utilize a **Localização** predefinida. Clique em **OK** para criar o projeto.
3. Utilize o gestor de pacote NuGet para adicionar as bibliotecas do Service Bus ao seu projeto:

   1. No Explorador de Soluções, clique com o botão direito no projeto **QueueSample** e, em seguida, clique em **Gerir Pacotes NuGet**.
   2. Na caixa de diálogo **Gerir Pacotes Nuget**, clique no separador **Procurar** e localize o **Service Bus do Azure**. Em seguida, clique em **Instalar**.
      <br />
4. No Explorador de Soluções, faça duplo clique no ficheiro Program.cs para abri-lo no editor do Visual Studio. Altere o nome do espaço de nomes a partir do respetivo nome predefinido de `QueueSample` para `Microsoft.ServiceBus.Samples`.

    ```
    Microsoft.ServiceBus.Samples
    {
        ...
    ```
5. Modifique as instruções `using` conforme mostrado no código seguinte.

    ```
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;
    ```
6. Crie um ficheiro de texto com o nome Data.csv e copie o seguinte texto delimitado por vírgulas.

    ```
    IssueID,IssueTitle,CustomerID,CategoryID,SupportPackage,Priority,Severity,Resolved
    1,Package lost,1,1,Basic,5,1,FALSE
    2,Package damaged,1,1,Basic,5,1,FALSE
    3,Product defective,1,2,Premium,5,2,FALSE
    4,Product damaged,2,2,Premium,5,2,FALSE
    5,Package lost,2,2,Basic,5,2,TRUE
    6,Package lost,3,2,Basic,5,2,FALSE
    7,Package damaged,3,7,Premium,5,3,FALSE
    8,Product defective,3,2,Premium,5,3,FALSE
    9,Product damaged,4,6,Premium,5,3,TRUE
    10,Package lost,4,8,Basic,5,3,FALSE
    11,Package damaged,5,4,Basic,5,4,FALSE
    12,Product defective,5,4,Basic,5,4,FALSE
    13,Package lost,6,8,Basic,5,4,FALSE
    14,Package damaged,6,7,Premium,5,5,FALSE
    15,Product defective,6,2,Premium,5,5,FALSE
    ```

    Guarde e feche o ficheiro Data.csv e lembre-se da localização onde o guardou.
7. No Explorador de Soluções, clique com o botão direito no nome do projeto (neste exemplo, **QueueSample**), clique em **Adicionar** e em **Item Existente**.
8. Navegue para o ficheiro Data.csv criado no passo 6. Clique no ficheiro e, em seguida, clique em **Adicionar**. Certifique-se de que a opção **Todos os ficheiros (*.*)** está selecionada na lista de tipos de ficheiro.

### <a name="create-a-method-that-parses-a-list-of-messages"></a>Criar um método de análise de uma lista de mensagens
1. Na classe `Program` antes do método `Main()`, declare duas variáveis: uma do tipo **DataTable** para conter a lista de mensagens em Data.csv. A outra deverá ser do tipo objeto Lista, vivamente introduzida para [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Esta última opção é a lista de mensagens mediadas utilizada nos passos subsequentes no tutorial.

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        class Program
        {

            private static DataTable issues;
            private static List<BrokeredMessage> MessageList;
    ```
2. Fora de `Main()`, defina um método `ParseCSV()` que analisa a lista de mensagens em Data.csv e carrega as mensagens numa tabela [DataTable](https://msdn.microsoft.com/library/azure/system.data.datatable.aspx), conforme mostrado aqui. O método devolve um objeto **DataTable**.

    ```
    static DataTable ParseCSVFile()
    {
        DataTable tableIssues = new DataTable("Issues");
        string path = @"..\..\data.csv";
        try
        {
            using (StreamReader readFile = new StreamReader(path))
            {
                string line;
                string[] row;

                // create the columns
                line = readFile.ReadLine();
                foreach (string columnTitle in line.Split(','))
                {
                    tableIssues.Columns.Add(columnTitle);
                }

                while ((line = readFile.ReadLine()) != null)
                {
                    row = line.Split(',');
                    tableIssues.Rows.Add(row);
                }
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Error:" + e.ToString());
        }

        return tableIssues;
    }
    ```
3. No método `Main()`, adicione uma instrução que chame o método `ParseCSVFile()`:

    ```
    public static void Main(string[] args)
    {

        // Populate test data
        issues = ParseCSVFile();

    }
    ```

### <a name="create-a-method-that-loads-the-list-of-messages"></a>Criar um método de carregamento da lista de mensagens
1. Fora de `Main()`, defina um método `GenerateMessages()` que tenha em conta o objeto **DataTable** devolvido por `ParseCSVFile()` e carregue a tabela numa lista de tipo seguro de mensagens mediadas. O método devolve o objeto **Lista**, como no exemplo seguinte.

    ```
    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
        // Instantiate the brokered list object
        List<BrokeredMessage> result = new List<BrokeredMessage>();

        // Iterate through the table and create a brokered message for each row
        foreach (DataRow item in issues.Rows)
        {
            BrokeredMessage message = new BrokeredMessage();
            foreach (DataColumn property in issues.Columns)
            {
                message.Properties.Add(property.ColumnName, item[property]);
            }
            result.Add(message);
        }
        return result;
    }
    ```
2. Em `Main()`, imediatamente após a chamada para `ParseCSVFile()`, adicione uma instrução que chame o método `GenerateMessages()` com o valor devolvido de `ParseCSVFile()` como um argumento:

    ```
    public static void Main(string[] args)
    {

        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);
    }
    ```

### <a name="obtain-user-credentials"></a>Obter credenciais de utilizador
1. Em primeiro lugar, crie três variáveis de cadeia globais para armazenar estes valores. Declare essas variáveis imediatamente após as declarações de variáveis anteriores; por exemplo:

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        public class Program
        {

            private static DataTable issues;
            private static List<BrokeredMessage> MessageList;

            // Add these variables
            private static string ServiceNamespace;
            private static string sasKeyName = "RootManageSharedAccessKey";
            private static string sasKeyValue;
            …
    ```
2. Em seguida, crie uma função que aceite e armazene o espaço de nomes de serviço e a chave SAS. Adicione este método fora de `Main()`. Por exemplo:

    ```
    static void CollectUserInput()
    {
        // User service namespace
        Console.Write("Please enter the namespace to use: ");
        ServiceNamespace = Console.ReadLine();

        // Issuer key
        Console.Write("Enter the SAS key to use: ");
        sasKeyValue = Console.ReadLine();
    }
    ```
3. Em `Main()`, imediatamente após a chamada para `GenerateMessages()`, adicione uma instrução que chame o método `CollectUserInput()`:

    ```
    public static void Main(string[] args)
    {

        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);

        // Collect user input
        CollectUserInput();
    }
    ```

### <a name="build-the-solution"></a>Compilar a solução
No menu **Compilar** no Visual Studio, pode clicar em **Compilar Solução** ou premir **Ctrl+Shift+B** para confirmar a precisão do seu trabalho até ao momento.

## <a name="create-management-credentials"></a>Criar credenciais de gestão
Neste passo, defina as opções de gestão que utilizará para criar credenciais de assinatura de acesso partilhado (SAS) com as quais autorizará a sua aplicação.

1. Para efeitos de clareza, este tutorial coloca todas as operações em fila num método separado. Crie um método `Queue()` assíncrono na classe `Program` após o método `Main()`. Por exemplo:

    ```
    public static void Main(string[] args)
    {
    …
    }
    static async Task Queue()
    {
    }
    ```
2. O passo seguinte consiste em criar uma credencial de SAS através de um objeto [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx). O método de criação processa o nome da chave SAS e o valor obtido no método `CollectUserInput()`. Adicione o seguinte código ao método `Queue()`:

    ```
    static async Task Queue()
    {
        // Create management credentials
        TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
    }
    ```
3. Crie um novo objeto de gestão de espaço de nomes, com um URI com o nome do espaço de nomes e as credenciais de gestão obtidos no passo anterior como argumentos. Adicione este código imediatamente depois do código adicionado no passo anterior. Certifique-se de que substitui `<yourNamespace>` pelo nome do espaço de nomes de serviço:

    ```
    NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```

### <a name="example"></a>Exemplo
Neste momento, o código deverá ter um aspeto semelhante ao seguinte:

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  public class Program
  {
    private static DataTable issues;
    private static List<BrokeredMessage> MessageList;
    private static string ServiceNamespace;
    private static string sasKeyName = "RootManageSharedAccessKey";
    private static string sasKeyValue;

    static void Main(string[] args)
    {
      // Populate test data
      issues = ParseCSVFile();
      MessageList = GenerateMessages(issues);

      // Collect user input
      CollectUserInput();

      // Add this call
      Task.WaitAll(Queue());
    }

    static async Task Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    }

    static DataTable ParseCSVFile()
    {
      DataTable tableIssues = new DataTable("Issues");
      string path = @"..\..\data.csv";
      try
      {
        using (StreamReader readFile = new StreamReader(path))
        {
          string line;
          string[] row;

          // create the columns
          line = readFile.ReadLine();
          foreach (string columnTitle in line.Split(','))
          {
            tableIssues.Columns.Add(columnTitle);
          }

          while ((line = readFile.ReadLine()) != null)
          {
            row = line.Split(',');
            tableIssues.Rows.Add(row);
          }
        }
      }
      catch (Exception e)
      {
        Console.WriteLine("Error:" + e.ToString());
      }

      return tableIssues;
    }

    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
      // Instantiate the brokered list object
      List<BrokeredMessage> result = new List<BrokeredMessage>();

      // Iterate through the table and create a brokered message for each row
      foreach (DataRow item in issues.Rows)
      {
        BrokeredMessage message = new BrokeredMessage();
        foreach (DataColumn property in issues.Columns)
        {
          message.Properties.Add(property.ColumnName, item[property]);
        }
        result.Add(message);
      }
      return result;
    }

    static void CollectUserInput()
    {
      // User service namespace
      Console.Write("Please enter the service namespace to use: ");
      ServiceNamespace = Console.ReadLine();

      // Issuer key
      Console.Write("Please enter the issuer key to use: ");
      sasKeyValue = Console.ReadLine();
    }
  }
}
```

## <a name="send-messages-to-the-queue"></a>Enviar mensagens para a fila
Neste passo, cria uma fila e, em seguida, envia as mensagens contidas na lista de mensagens mediadas para essa fila.

### <a name="create-queue-and-send-messages-to-the-queue"></a>Criar uma fila e enviar mensagens para a fila
1. Em primeiro lugar, crie a fila. Por exemplo, atribua o nome `myQueue` e declare a mesma imediatamente após as operações de gestão adicionadas no método `Queue()` no último passo:

    ```
    QueueDescription myQueue;

    if (namespaceClient.QueueExists("IssueTrackingQueue"))
    {
        namespaceClient.DeleteQueue("IssueTrackingQueue");
    }

    myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
    ```
2. No método `Queue()`, crie um objeto de fábrica de mensagens com um URI do Service Bus recentemente criado como argumento. Adicione o seguinte código imediatamente depois das operações de gestão adicionadas no último passo. Certifique-se de que substitui `<yourNamespace>` pelo nome do espaço de nomes de serviço:

    ```
    MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```
3. Em seguida, crie o objeto de fila utilizando a classe [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx). Adicione o seguinte código imediatamente depois do código adicionado no último passo:

    ```
    QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");
    ```
4. Em seguida, adicione o código repetido ao longo da lista de mensagens mediadas criadas anteriormente, enviando cada uma para a fila. Adicione o seguinte código imediatamente depois da instrução `CreateQueueClient()` no passo anterior:

    ```
    // Send messages
    Console.WriteLine("Now sending messages to the queue.");
    for (int count = 0; count < 6; count++)
    {
        var issue = MessageList[count];
        issue.Label = issue.Properties["IssueTitle"].ToString();
        await myQueueClient.SendAsync(issue);
        Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
    }
    ```

## <a name="receive-messages-from-the-queue"></a>Receber mensagens da fila
Neste passo, obtenha a lista de mensagens da fila criada no passo anterior.

### <a name="create-a-receiver-and-receive-messages-from-the-queue"></a>Criar um recetor e receber mensagens da fila
No método `Queue()`, itere através da fila e receba as mensagens utilizando o método [QueueClient.ReceiveAsync](https://msdn.microsoft.com/library/azure/dn130423.aspx), imprimindo cada mensagem para a consola. Adicione o seguinte código imediatamente depois do código adicionado no passo anterior:

```
Console.WriteLine("Now receiving messages from Queue.");
BrokeredMessage message;
while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

Tenha em atenção que `Thread.Sleep` é apenas utilizado para simular o processamento de mensagens e, provavelmente, não será necessário numa aplicação de mensagens real.

### <a name="end-the-queue-method-and-clean-up-resources"></a>Concluir o método Fila e limpar os recursos
Diretamente depois do código anterior, adicione o código seguinte para limpar a fábrica de mensagens e os recursos da fila:

```
factory.Close();
myQueueClient.Close();
namespaceClient.DeleteQueue("IssueTrackingQueue");
```

### <a name="call-the-queue-method"></a>Chamar o método Fila
O último passo consiste em adicionar uma instrução que chame o método `Queue()` a partir de `Main()`. Adicione a seguinte linha de código realçada no final de Main():

```
public static void Main(string[] args)
{
    // Collect user input
    CollectUserInput();

    // Populate test data
    issues = ParseCSVFile();
    MessageList = GenerateMessages(issues);

    // Add this call
    Queue();
}
```

### <a name="example"></a>Exemplo
O código seguinte contém a aplicação **QueueSample** completa.

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
    public class Program
    {
        private static DataTable issues;
        private static List<BrokeredMessage> MessageList;

        // Add these variables
        private static string ServiceNamespace;
        private static string sasKeyName = "RootManageSharedAccessKey";
        private static string sasKeyValue;

        static void Main(string[] args)
        {

            // Populate test data
            issues = ParseCSVFile();
            MessageList = GenerateMessages(issues);

            // Collect user input
            CollectUserInput();

            Queue();

        }

        static async Task Queue()
        {
            // Create management credentials
            TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
            NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueDescription myQueue;

            if (namespaceClient.QueueExists("IssueTrackingQueue"))
            {
                namespaceClient.DeleteQueue("IssueTrackingQueue");
            }

            myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");

            MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");

            // Send messages
            Console.WriteLine("Now sending messages to the queue.");
            for (int count = 0; count < 6; count++)
            {
                var issue = MessageList[count];
                issue.Label = issue.Properties["IssueTitle"].ToString();
                await myQueueClient.SendAsync(issue);
                Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
            }

            Console.WriteLine("Now receiving messages from Queue.");
            BrokeredMessage message;
            while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
            {
                Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
                message.Complete();

                Console.WriteLine("Processing message (sleeping...)");
                Thread.Sleep(1000);
            }

            factory.Close();
            myQueueClient.Close();
            namespaceClient.DeleteQueue("IssueTrackingQueue");


        }

        static void CollectUserInput()
        {
            // User service namespace
            Console.Write("Please enter the namespace to use: ");
            ServiceNamespace = Console.ReadLine();

            // Issuer key
            Console.Write("Enter the SAS key to use: ");
            sasKeyValue = Console.ReadLine();
        }

        static List<BrokeredMessage> GenerateMessages(DataTable issues)
        {
            // Instantiate the brokered list object
            List<BrokeredMessage> result = new List<BrokeredMessage>();

            // Iterate through the table and create a brokered message for each row
            foreach (DataRow item in issues.Rows)
            {
                BrokeredMessage message = new BrokeredMessage();
                foreach (DataColumn property in issues.Columns)
                {
                    message.Properties.Add(property.ColumnName, item[property]);
                }
                result.Add(message);
            }
            return result;
        }

        static DataTable ParseCSVFile()
        {
            DataTable tableIssues = new DataTable("Issues");
            string path = @"..\..\data.csv";
            try
            {
                using (StreamReader readFile = new StreamReader(path))
                {
                    string line;
                    string[] row;

                    // create the columns
                    line = readFile.ReadLine();
                    foreach (string columnTitle in line.Split(','))
                    {
                        tableIssues.Columns.Add(columnTitle);
                    }

                    while ((line = readFile.ReadLine()) != null)
                    {
                        row = line.Split(',');
                        tableIssues.Rows.Add(row);
                    }
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Error:" + e.ToString());
            }

            return tableIssues;
        }
    }
}
```

## <a name="build-and-run-the-queuesample-application"></a>Compilar e executar a aplicação QueueSample
Agora que concluiu os passos anteriores, pode compilar e executar a aplicação **QueueSample**.

### <a name="build-the-queuesample-application"></a>Compilar a aplicação QueueSample
No Visual Studio, a partir do menu **Compilar**, clique em **Compilar Solução** ou prima **Ctrl+Shift+B**. Se encontrar erros, verifique se o código está correto com base no exemplo completo apresentado no final do passo anterior.

## <a name="next-steps"></a>Passos seguintes
Este tutorial mostrou como criar um serviço e uma aplicação cliente do Service Bus, utilizando as capacidades de mensagens mediadas do Service Bus. Para um tutorial semelhante que utiliza o [Reencaminhamento de WCF](service-bus-messaging-overview.md#service-bus-relay) do Service Bus, veja o [Service Bus relayed messaging tutorial (Tutorial das mensagens retransmitidas do Service Bus)](../service-bus-relay/service-bus-relay-tutorial.md).

Para obter mais informações sobre o [Service Bus](https://azure.microsoft.com/services/service-bus/), consulte os seguintes tópicos.

* [Descrição geral das mensagens do Service Bus](service-bus-messaging-overview.md)
* [Noções básicas sobre o Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Arquitetura do Service Bus](service-bus-architecture.md)



<!--HONumber=Nov16_HO2-->


