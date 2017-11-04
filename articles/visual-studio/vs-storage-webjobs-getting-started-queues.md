---
title: "Introdução ao armazenamento de filas e o Visual Studio ligado serviços (WebJob projetos) | Microsoft Docs"
description: "Como começar a utilizar o armazenamento de filas do Azure num projeto WebJob depois de ligar a uma conta de armazenamento com o Visual Studio ligado serviços."
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 5c3ef267-2a67-44e9-ab4a-1edd7015034f
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: kraigb
ms.openlocfilehash: efd2f1e471f67396d35f11f2eb1044a8afa469af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Introdução ao armazenamento de filas do Azure e o Visual Studio ligado serviços (projetos de trabalho Web)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral
Este artigo descreve como começar a utilizar o armazenamento de filas do Azure num projeto trabalho de Web do Visual Studio do Azure, depois de ter criado ou referenciada uma conta de armazenamento do Azure utilizando o Visual Studio **adicionar serviços ligados** caixa de diálogo. Quando adicionar uma conta de armazenamento para um projeto do trabalho Web utilizando o Visual Studio **adicionar serviços ligados** caixa de diálogo, os pacotes de NuGet de armazenamento do Azure adequados são instalados, as referências de .NET adequadas são adicionadas ao projeto, e cadeias de ligação para a conta de armazenamento são atualizadas no ficheiro App. config.  

Este artigo fornece c# exemplos de código que mostram como utilizar a versão do SDK de WebJobs do Azure 1. x com o serviço de armazenamento de filas do Azure.

O Armazenamento de Filas do Azure é um serviço para armazenar um grande número de mensagens que podem ser acedidas a partir de qualquer local no mundo através de chamadas autenticadas com HTTP ou HTTPS. Uma mensagem de fila única pode ter até 64 KB e uma fila pode conter milhões de mensagens, até ao limite da capacidade total de uma conta de armazenamento. Consulte [introdução ao armazenamento de filas do Azure através do .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) para obter mais informações. Para mais informações sobre o ASP.NET, consulte [ASP.NET](http://www.asp.net).

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Como acionar a uma função quando é recebida uma mensagem de fila
Para escrever uma função que o SDK de WebJobs chama quando é recebida uma mensagem de fila, utilize o **QueueTrigger** atributo. O construtor de atributos assume um parâmetro de cadeia que especifica o nome da fila para consultar. Para ver como definir o nome da fila de forma dinâmica, consulte [como definir opções de configuração](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Mensagens de filas de cadeia
No exemplo seguinte, a fila contém uma mensagem de cadeia, por isso, **QueueTrigger** é aplicado a um parâmetro de cadeia denominado **logMessage** que contém o conteúdo da mensagem de fila. A função [escreve uma mensagem de registo para o Dashboard](#how-to-write-logs).

        public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            logger.WriteLine(logMessage);
        }

Besides **cadeia**, o parâmetro pode ser uma matriz de bytes, um **CloudQueueMessage** objeto ou um POCO por si.

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(simples objeto antigo de CLR](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) mensagens da fila
No exemplo seguinte, a mensagem da fila contém um JSON para um **BlobInformation** objeto que inclui um **BlobName** propriedade. O SDK deserializes automaticamente o objeto.

        public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

O SDK utiliza o [pacote NuGet newtonsoft](http://www.nuget.org/packages/Newtonsoft.Json) para serializar e anular a serialização de mensagens. Se criar a fila de mensagens num programa que não utiliza o SDK de WebJobs, pode escrever o código semelhante ao seguinte exemplo para criar uma mensagem de fila POCO pode analisar o SDK.

        BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

### <a name="async-functions"></a>Async funções
A seguinte função async [escreve um registo para o Dashboard](#how-to-write-logs).

        public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            await logger.WriteLineAsync(logMessage);
        }

Funções de assíncrona poderão demorar um [token de cancelamento](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), conforme mostrado no exemplo seguinte, que copia um blob. (Para obter uma explicação sobre o **queueTrigger** marcador de posição, consulte o [Blobs](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message) secção.)

        public async static Task ProcessQueueMessageAsyncCancellationToken(
            [QueueTrigger("blobcopyqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
            CancellationToken token)
        {
            await blobInput.CopyToAsync(blobOutput, 4096, token);
        }

## <a name="types-the-queuetrigger-attribute-works-with"></a>O atributo QueueTrigger funciona com os tipos
Pode utilizar **QueueTrigger** com os seguintes tipos:

* **cadeia**
* Um tipo POCO serializado como JSON
* **byte]**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Algoritmo de consulta
O SDK implementa um aleatório exponencial término algoritmo para minimizar o efeito da fila de inatividade de consulta em custos de transação de armazenamento.  Quando é encontrada uma mensagem, o SDK tem de aguardar dois segundos e, em seguida, verifica a existência de outra mensagem; Quando não é encontrada nenhuma mensagem aguarda cerca de quatro segundos antes de tentar novamente. Depois de tentativas falhadas subsequentes para receber uma mensagem de fila, o tempo de espera continua a aumentar até atingir o tempo de espera máximo, que está predefinida para um minuto. [O tempo de espera máximo é configurável](#how-to-set-configuration-options).

## <a name="multiple-instances"></a>Várias instâncias
Se a sua aplicação web é executada em várias instâncias, executa um WebJobs contínuos em cada máquina e cada máquina será Aguarde acionadores e tente executar funções. Alguns cenários em que esta funcionalidade pode originar algumas funções de processar os mesmos dados duas vezes, por isso, funções devem ser idempotent (escrito para que repetidamente ao chamá-los com os mesmos dados de entrada não produz resultados duplicados).  

## <a name="parallel-execution"></a>Execução paralela
Se tiver várias funções que está a escutar filas diferentes, o SDK chamam-los em paralelo quando as mensagens são recebidas em simultâneo.

O mesmo se aplica quando várias mensagens são recebidas para uma fila única. Por predefinição, o SDK obtém um lote de 16 mensagens de fila de cada vez e executa a função que processa-os em paralelo. [O tamanho do lote é configurável](#how-to-set-configuration-options). Quando o número a ser processado obtém-se para baixo para metade do tamanho do lote, o SDK obtém outro lote e começa a processar as mensagens. Por conseguinte, o número máximo de mensagens em simultâneo a ser processado por função é um e um meio vezes o tamanho de lote. Este limite aplica-se em separado para cada função que tenha um **QueueTrigger** atributo. Se não quiser execução paralela de mensagens recebidas uma fila, defina o tamanho de lote para 1.

## <a name="get-queue-or-queue-message-metadata"></a>Obter a fila ou metadados de mensagem de fila
Pode obter as seguintes propriedades da mensagem ao adicionar parâmetros para a assinatura de método:

* **DateTimeOffset** expirationTime
* **DateTimeOffset** insertionTime
* **DateTimeOffset** nextVisibleTime
* **cadeia** queueTrigger (contém o texto da mensagem)
* **cadeia** id
* **cadeia** popReceipt
* **Int** dequeueCount

Se pretende trabalhar diretamente com o armazenamento do Azure API, também pode adicionar um **CloudStorageAccount** parâmetro.

O exemplo seguinte escreve todos estes metadados um registo de informações da aplicação. No exemplo, logMessage e queueTrigger contêm o conteúdo da mensagem de fila.

        public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
            DateTimeOffset expirationTime,
            DateTimeOffset insertionTime,
            DateTimeOffset nextVisibleTime,
            string id,
            string popReceipt,
            int dequeueCount,
            string queueTrigger,
            CloudStorageAccount cloudStorageAccount,
            TextWriter logger)
        {
            logger.WriteLine(
                "logMessage={0}\n" +
            "expirationTime={1}\ninsertionTime={2}\n" +
                "nextVisibleTime={3}\n" +
                "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
                "queue endpoint={7} queueTrigger={8}",
                logMessage, expirationTime,
                insertionTime,
                nextVisibleTime, id,
                popReceipt, dequeueCount,
                cloudStorageAccount.QueueEndpoint,
                queueTrigger);
        }

Segue-se um registo de exemplo escrito pelo código de exemplo:

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

## <a name="graceful-shutdown"></a>Encerramento correto
Uma função que é executado num WebJob contínuo pode aceitar um **CancellationToken** parâmetro que permite que o sistema operativo notificar a função quando o trabalho Web está prestes a ser terminada. Pode utilizar esta notificação para se certificar de que a função não terminar inesperadamente de uma forma que mantém os dados num estado inconsistente.

O exemplo seguinte mostra como procurar iminente WebJob terminação de uma função.

    public static void GracefulShutdownDemo(
                [QueueTrigger("inputqueue")] string inputText,
                TextWriter logger,
                CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(1000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }

**Nota:** Dashboard não poderá mostrar corretamente o estado e a saída de funções que foi encerrado.

Para obter mais informações, consulte [encerramento correto WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Como criar uma mensagem de fila ao processar uma mensagem de fila
Para escrever uma função que cria uma nova mensagem da fila, utilize o **fila** atributo. Como **QueueTrigger**, passa o nome da fila como uma cadeia ou pode [definir o nome da fila dinamicamente](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Mensagens de filas de cadeia
O exemplo de código não async seguinte cria uma nova mensagem da fila na fila com o nome "outputqueue" com os mesmos conteúdos, como a mensagem de fila recebida da fila com o nome "inputqueue". (Assíncrona utilizam funções **IAsyncCollector<T>**  conforme mostrado posteriormente nesta secção.)

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] out string outputQueueMessage )
        {
            outputQueueMessage = queueMessage;
        }

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(simples objeto antigo de CLR](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) mensagens da fila
Para criar uma mensagem de fila que contém um POCO em vez de uma cadeia, passar o tipo POCO como um parâmetro de saída para o **fila** construtor de atributos.

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
            [Queue("outputqueue")] out BlobInformation blobInfoOutput )
        {
            blobInfoOutput = blobInfoInput;
        }

O SDK serializa automaticamente o objeto em JSON. Uma mensagem de fila é criada sempre, mesmo que o objecto é nulo.

### <a name="create-multiple-messages-or-in-async-functions"></a>Criar várias mensagens ou nas funções de assíncrona
Para criar várias mensagens, se o tipo de parâmetro para a fila de saída **ICollector<T>**  ou **IAsyncCollector<T>**, conforme mostrado no exemplo seguinte.

        public static void CreateQueueMessages(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

Cada mensagem de fila é criada imediatamente quando o **adicionar** método é chamado.

### <a name="types-that-the-queue-attribute-works-with"></a>Tipos de que o atributo de fila funciona com o
Pode utilizar o **fila** atributo nos seguintes tipos de parâmetro:

* **saída cadeia** (cria a mensagem da fila se o valor do parâmetro for não nulo quando a função termina)
* **saída byte []** (funciona como **cadeia**)
* **saída CloudQueueMessage** (funciona como **cadeia**)
* **saída POCO** (um tipo serializável, cria uma mensagem com um objeto nulo se o parâmetro é nulo quando a função termina)
* **ICollector**
* **IAsyncCollector**
* **CloudQueue** (para criar mensagens manualmente utilizando a API de armazenamento do Azure diretamente)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>Utilizar o SDK de WebJobs atributos no corpo de uma função
Se precisar de fazer alguns cálculos na sua função antes de o utilizar como um atributo do SDK de WebJobs **fila**, **Blob**, ou **tabela**, pode utilizar o **IBinder**interface.

O exemplo seguinte utiliza uma mensagem de fila de entrada e cria uma nova mensagem com o mesmo conteúdo de uma fila de saída. O nome da fila de saída está definido por código no corpo da função.

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] string queueMessage,
            IBinder binder)
        {
            string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
            QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
            CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
            outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
        }

O **IBinder** interface também pode ser utilizada com o **tabela** e **Blob** atributos.

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Como de leitura e escrita de blobs e tabelas ao processar uma mensagem de fila
O **Blob** e **tabela** atributos permitem-lhe ler e escrever os blobs e tabelas. Os exemplos nesta secção aplicam-se para blobs. Para exemplos de código que mostram como acionar a processos quando blobs são criados ou atualizados, consulte [como utilizar o blob storage do Azure com o SDK de WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>Mensagens de filas de cadeia acionar operações de BLOBs
Para uma mensagem de fila que contém uma cadeia, **queueTrigger** é um marcador de posição pode utilizar o **Blob** do atributo **blobPath** parâmetro contém o conteúdo do mensagem.

O exemplo seguinte utiliza **fluxo** objetos a leitura e escrita de blobs. A mensagem da fila é o nome de um blob localizado no contentor do textblobs. Uma cópia do blob com "-novo" acrescentado para o nome é criado no mesmo contentor.

        public static void ProcessQueueMessage(
            [QueueTrigger("blobcopyqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

O **Blob** demora de construtor de atributos um **blobPath** parâmetro que especifica o nome de contentor e BLOBs. Para mais informações sobre esta marcador de posição, consulte [como utilizar o blob storage do Azure com o SDK de WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).

Quando o atributo decorates um **fluxo** objeto, de outro construtor parâmetro especifica o **FileAccess** modo como a leitura, escrita ou leitura/escrita.

O exemplo seguinte utiliza uma **CloudBlockBlob** objeto para eliminar um blob. A mensagem da fila é o nome do blob.

        public static void DeleteBlob(
            [QueueTrigger("deleteblobqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
        {
            blobToDelete.Delete();
        }

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(simples objeto antigo de CLR](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) mensagens da fila
Para um POCO armazenado como JSON na mensagem de fila, pode utilizar os marcadores de posição pelo nome propriedades do objeto no **fila** do atributo **blobPath** parâmetro. Também pode utilizar nomes de propriedade de metadados de filas como marcadores de posição. Consulte [obter a fila ou metadados de mensagem de fila](#get-queue-or-queue-message-metadata).

O exemplo seguinte copia um blob para um blob novo com uma extensão diferente. A mensagem de fila é uma **BlobInformation** objeto inclui **BlobName** e **BlobNameWithoutExtension** propriedades. Os nomes de propriedade são utilizados como marcadores de posição no caminho do blob para o **Blob** atributos.

        public static void CopyBlobPOCO(
            [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
            [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

O SDK utiliza o [pacote NuGet newtonsoft](http://www.nuget.org/packages/Newtonsoft.Json) para serializar e anular a serialização de mensagens. Se criar a fila de mensagens num programa que não utiliza o SDK de WebJobs, pode escrever o código semelhante ao seguinte exemplo para criar uma mensagem de fila POCO pode analisar o SDK.

        BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

Se precisar de fazer alguns cálculos na sua função antes de um blob do enlace a um objeto, pode utilizar o atributo no corpo da função, conforme mostrado no [atributos de utilizar o SDK de WebJobs no corpo de uma função](#use-webjobs-sdk-attributes-in-the-body-of-a-function).

### <a name="types-you-can-use-the-blob-attribute-with"></a>Tipos que pode utilizar o atributo de Blob com o
O **Blob** atributo pode ser utilizado com os seguintes tipos:

* **Fluxo** (leitura ou escrita, utilizando o parâmetro de construtor FileAccess)
* **TextReader**
* **TextWriter**
* **cadeia** (leitura)
* **saída cadeia** (escrever; cria um blob apenas se o parâmetro de cadeia não nulo quando a função devolve)
* POCO (leitura)
* saída POCO (escrever; sempre cria um blob, cria como um objeto nulo se POCO parâmetro é nulo quando a função devolve)
* **CloudBlobStream** (escrita)
* **ICloudBlob** (ler ou escrever)
* **CloudBlockBlob** (ler ou escrever)
* **CloudPageBlob** (ler ou escrever)

## <a name="how-to-handle-poison-messages"></a>Como processar mensagens nocivas
Mensagens cujo conteúdo faz com que uma função de falha são denominadas *poison mensagens*. Quando a função falha, a mensagem da fila não é eliminada e, eventualmente, é captada novamente, fazendo com que o ciclo de ser repetido. O SDK automaticamente pode interromper o ciclo após um número limitado de iterações ou pode fazê-lo manualmente.

### <a name="automatic-poison-message-handling"></a>Processamento de mensagens nocivas automática
O SDK ligará para uma função de até 5 vezes para processar uma mensagem de fila. Se o tentar quinto falhar, a mensagem é movida para uma fila nocivas. Pode ver como configurar o número máximo de tentativas em [como definir opções de configuração](#how-to-set-configuration-options).

A fila nocivas é denominada *{originalqueuename}*-nocivas. Pode escrever uma função para processar mensagens da fila nocivas pelo registá-los ou enviar uma notificação que atenção manual é necessária.

No exemplo a seguir a **CopyBlob** função irão falhar quando uma mensagem de fila contém o nome de um blob que não existe. Quando isso acontece, a mensagem é movida da fila copyblobqueue para a fila de copyblobqueue poison. O **ProcessPoisonMessage** , em seguida, regista a mensagem corrompida foi.

        public static void CopyBlob(
            [QueueTrigger("copyblobqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

        public static void ProcessPoisonMessage(
            [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
        {
            logger.WriteLine("Failed to copy blob, name=" + blobName);
        }

A ilustração seguinte mostra o resultado destas funções consola quando uma mensagem corrompida foi for processada.

![Resultado da consola para processamento de mensagens nocivas](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Processamento de mensagens nocivas manual
Pode obter o número de vezes que uma mensagem foi captada para processamento através da adição de um **int** com o nome de parâmetro **dequeueCount** a sua função. Em seguida, pode verificar a contagem de dequeue no código de função e executar a sua própria mensagem corrompida foi processamento quando o número exceder um limiar, conforme mostrado no exemplo seguinte.

        public static void CopyBlob(
            [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
            [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
            TextWriter logger)
        {
            if (dequeueCount > 3)
            {
                logger.WriteLine("Failed to copy blob, name=" + blobName);
            }
            else
            {
            blobInput.CopyTo(blobOutput, 4096);
            }
        }

## <a name="how-to-set-configuration-options"></a>Como definir opções de configuração
Pode utilizar o **JobHostConfiguration** tipo para definir as opções de configuração seguinte:

* Defina as cadeias de ligação do SDK no código.
* Configurar **QueueTrigger** definições, tais como o máximo anular contagem.
* Obter os nomes de fila de configuração.

### <a name="set-sdk-connection-strings-in-code"></a>Definir cadeias de ligação do SDK no código
Definir as cadeias de ligação do SDK no código permite utilizar os seus próprios nomes de cadeia de ligação em ficheiros de configuração ou variáveis de ambiente, conforme mostrado no exemplo seguinte.

        static void Main(string[] args)
        {
            var _storageConn = ConfigurationManager
                .ConnectionStrings["MyStorageConnection"].ConnectionString;

            var _dashboardConn = ConfigurationManager
                .ConnectionStrings["MyDashboardConnection"].ConnectionString;

            var _serviceBusConn = ConfigurationManager
                .ConnectionStrings["MyServiceBusConnection"].ConnectionString;

            JobHostConfiguration config = new JobHostConfiguration();
            config.StorageConnectionString = _storageConn;
            config.DashboardConnectionString = _dashboardConn;
            config.ServiceBusConnectionString = _serviceBusConn;
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

### <a name="configure-queuetrigger--settings"></a>Configurar definições de QueueTrigger
Pode configurar as seguintes definições que se aplicam ao processamento de mensagens de fila:

* O número máximo de fila de mensagens que são captado em simultâneo para ser executado em paralelo (a predefinição é 16).
* O número máximo de tentativas antes do envio de uma mensagem de fila para uma fila nocivas (a predefinição é 5).
* O número máximo de tempo antes de consulta novamente quando uma fila está vazia de espera (a predefinição é 1 minuto).

O exemplo seguinte mostra como configurar estas definições:

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.Queues.BatchSize = 8;
            config.Queues.MaxDequeueCount = 4;
            config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>Definir valores para o SDK de WebJobs os parâmetros do construtor no código
Por vezes, pretende especificar um nome de fila, um nome de blob ou contentor ou nome de uma tabela no código em vez de codificá-lo. Por exemplo, poderá pretender especificar o nome da fila para **QueueTrigger** numa variável de ambiente ou ficheiro de configuração.

Pode fazê-lo mediante a transmissão num **NameResolver** de objeto para o **JobHostConfiguration** tipo. Incluir marcadores de posição especiais rodeados por sinais de sinal de percentagem () nos parâmetros de construtor de atributo do SDK de WebJobs e os seus **NameResolver** código especifica os valores reais para ser utilizado em vez dos marcadores de posição.

Por exemplo, suponha que pretende utilizar uma fila com o nome logqueuetest no ambiente de teste e uma logqueueprod nomeado em produção. Em vez de um nome de fila hard-coded, pretender especificar o nome de uma entrada no **appSettings** coleção que teria o nome da fila real. Se o **appSettings** chave é logqueue, a função foi ter um aspeto semelhante ao seguinte exemplo.

        public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
        {
            Console.WriteLine(logMessage);
        }

O **NameResolver** classe, em seguida, foi possível obter o nome da fila de **appSettings** conforme mostrado no exemplo seguinte:

        public class QueueNameResolver : INameResolver
        {
            public string Resolve(string name)
            {
                return ConfigurationManager.AppSettings[name].ToString();
            }
        }

Passa o **NameResolver** de classe para o **JobHost** objeto conforme mostrado no exemplo seguinte.

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.NameResolver = new QueueNameResolver();
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

**Nota:** fila, tabela e dos nomes blob são resolvidos sempre que uma função é chamada, mas os nomes de contentor do blob são resolvidos apenas quando iniciar a aplicação. Não é possível alterar o nome do contentor de blob enquanto a tarefa está em execução.

## <a name="how-to-trigger-a-function-manually"></a>Como acionar manualmente uma função
Para acionar manualmente uma função, utilize o **chamar** ou **CallAsync** método no **JobHost** objeto e o **NoAutomaticTrigger** atributo da função, conforme mostrado no exemplo seguinte.

        public class Program
        {
            static void Main(string[] args)
            {
                JobHost host = new JobHost();
                host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
            }

            [NoAutomaticTrigger]
            public static void CreateQueueMessage(
                TextWriter logger,
                string value,
                [Queue("outputqueue")] out string message)
            {
                message = value;
                logger.WriteLine("Creating queue message: ", message);
            }
        }

## <a name="how-to-write-logs"></a>Como escrever os registos
O Dashboard apresenta os registos em dois locais: a página para o trabalho Web e a página de uma invocação de WebJob específica.

![Registos na página do trabalho Web](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Registos na página de invocação de função](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Saída de métodos de consola que chamam de uma função ou no **main ()** método é apresentado na página do Dashboard para o trabalho Web e não na página para um determinado método de invocação. Saída do objeto TextWriter que obtém a partir de um parâmetro na sua assinatura do método é apresentado na página do Dashboard para um método de invocação.

Resultado da consola não pode ser associado a um determinado método de invocação porque a consola é single-threaded, enquanto muitas funções de tarefas poderão estar em execução ao mesmo tempo. É por esse motivo o SDK fornece cada invocação de função com o seu próprio objeto de escritor do registo exclusivo.

Escrever [registos de rastreio de aplicação](../app-service/web-sites-dotnet-troubleshoot-visual-studio.md#logsoverview), utilize **Console.Out** (cria registos marcados como informação) e **Console.Error** (cria registos marcados como erro). Uma alternativa consiste em utilizar [rastreio ou TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), que fornece verboso, aviso, e os níveis de crítico para além das informações e o erro. Os registos de rastreio de aplicações são apresentados nos ficheiros de registo de aplicação web, as tabelas do Azure, ou de blobs do Azure, dependendo de como configurar a sua aplicação web do Azure. Como é verdadeiro de todos os resultados da consola, os registos da 100 aplicação mais recentes também aparecem na página do Dashboard para o trabalho Web, não a página de uma invocação de função.

Resultado da consola é apresentado no Dashboard do apenas se o programa está em execução num trabalho Web do Azure, não se o programa é executada localmente ou alguns outros ambiente.

Pode desativar o registo ao definir a cadeia de ligação do Dashboard como nulo. Para obter mais informações, consulte [como definir opções de configuração](#how-to-set-configuration-options).

O exemplo seguinte mostra várias formas de escrever os registos:

        public static void WriteLog(
            [QueueTrigger("logqueue")] string logMessage,
            TextWriter logger)
        {
            Console.WriteLine("Console.Write - " + logMessage);
            Console.Out.WriteLine("Console.Out - " + logMessage);
            Console.Error.WriteLine("Console.Error - " + logMessage);
            logger.WriteLine("TextWriter - " + logMessage);
        }

No Dashboard de SDK de WebJobs, o resultado do **TextWriter** objeto mostra cópias de segurança quando aceda à página para um determinado invocação de função e selecione **alternar saída**:

![Ligação de invocação](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Registos na página de invocação de função](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

No Dashboard do SDK de WebJobs, as linhas de 100 mais recentes da consola de saída Mostrar cópias de segurança quando, visite a página para o trabalho Web (e não para a invocação de função) e selecionar **alternar saída**.

![Saída de ativar/desativar](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

Num WebJob contínuo, os registos de aplicações apresentada na/dados/tarefas/contínua/*{webjobname}*/job_log.txt no sistema de ficheiros de aplicação web.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

Num Azure blob o aspeto de registos de aplicações como esta: 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Olá, mundo!, 2014-09-26T21:01:13, erro, contosoadsnew, 491e54, 635473620738373502,0,17404,19,Console.Error - Olá, mundo!, 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Olá, mundo!,

E uma tabela do Azure a **Console.Out** e **Console.Error** registos tem o seguinte aspeto:

![Registo de informações na tabela](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Registo de erros na tabela](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>Passos seguintes
Este artigo forneceu exemplos de código que mostram como lidar com cenários comuns para trabalhar com as filas do Azure. Para obter mais informações sobre como utilizar WebJobs do Azure e o SDK de WebJobs, consulte [recursos de documentação de WebJobs do Azure](http://go.microsoft.com/fwlink/?linkid=390226).

