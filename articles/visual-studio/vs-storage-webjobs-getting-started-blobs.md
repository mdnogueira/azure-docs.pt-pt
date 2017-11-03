---
title: "Introdução ao blob storage e o Visual Studio ligado serviços (WebJob projetos) | Microsoft Docs"
description: "Como começar a utilizar o Blob storage num projeto WebJob depois de ligar a um armazenamento do Azure com o Visual Studio ligado serviços."
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 324c9376-0225-4092-9825-5d1bd5550058
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: kraigb
ms.openlocfilehash: 7d683f950e8847a18f38158a8f8727b1274fc711
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Introdução ao Blob do Azure Visual Studio e de armazenamento ligado serviços (projetos de trabalho Web)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Descrição geral
Este artigo fornece c# exemplos de código que mostram como acionar um processo quando um blob do Azure é criado ou atualizado. O código de amostras a utilizar o [SDK de WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki) versão 1. x. Quando adicionar uma conta de armazenamento para um projeto do trabalho Web utilizando o Visual Studio **adicionar serviços ligados** caixa de diálogo, é instalado o pacote NuGet do armazenamento de Azure adequado, as referências de .NET adequadas são adicionadas ao projeto, e cadeias de ligação para a conta de armazenamento são atualizadas no ficheiro App. config.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Como acionar a uma função quando um blob é criado ou atualizado
Esta secção mostra como utilizar o **BlobTrigger** atributo.

 **Nota:** o SDK de WebJobs analisa os ficheiros de registo a observar blobs novos ou alterados. Este processo é inerentemente lento; uma função pode não obter uma acionada até vários minutos ou já depois de criado o blob.  Se a aplicação necessita de processar blobs imediatamente, o método recomendado consiste em criar uma mensagem de fila quando criar o blob e utilizar o **QueueTrigger** atributo em vez do **BlobTrigger** atributo da função que processa o blob.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Marcador de posição único para o nome do blob com a extensão
O exemplo de código seguinte copia blobs de texto que aparece no *entrada* contentor para o *saída* contentor:

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

O construtor de atributos assume um parâmetro de cadeia que especifica o nome do contentor e um marcador de posição para o nome do blob. Neste exemplo, se o nome de um blob *Blob1.txt* é criado no *entrada* contentor, a função cria um blob com o nome *Blob1.txt* no *saída* contentor.

Pode especificar um padrão de nome com o marcador de posição do nome de blob, conforme mostrado no exemplo de código seguinte:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Este código copia apenas blobs que têm nomes a partir do "original-". Por exemplo, *original Blob1.txt* no *entrada* contentor é copiado para *cópia Blob1.txt* no *saída* contentor.

Se tiver de especificar um padrão de nome para nomes de blob ter chavetas no nome, faça duplo chavetas. Por exemplo, se pretender localizar os blobs no *imagens* contentor que têm nomes como esta:

        {20140101}-soundfile.mp3

Utilize esta opção para o seu padrão de:

        images/{{20140101}}-{name}

No exemplo, o *nome* valor do marcador de posição seria *soundfile.mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Marcadores de posição de blob separado, nome e extensão
O exemplo de código seguinte altera a extensão de ficheiro, como copia blobs que aparecem no *entrada* contentor para o *saída* contentor. O código regista a extensão do *entrada* blob e define a extensão do *saída* blob para *. txt*.

        public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
            [Blob("output/{name}.txt")] out string output,
            string name,
            string ext,
            TextWriter logger)
        {
            logger.WriteLine("Blob name:" + name);
            logger.WriteLine("Blob extension:" + ext);
            output = input.ReadToEnd();
        }

## <a name="types-that-you-can-bind-to-blobs"></a>Tipos de que é possível vincular para blobs
Pode utilizar o **BlobTrigger** atributo nos seguintes tipos:

* **cadeia**
* **TextReader**
* **Fluxo**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Outros tipos de serialização anulados pelo [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Se pretende trabalhar diretamente com a conta de armazenamento do Azure, também pode adicionar um **CloudStorageAccount** parâmetro para a assinatura de método.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Ao obter conteúdo de blob de texto por enlace cadeia
Se os blobs de texto são esperados, **BlobTrigger** podem ser aplicadas a um **cadeia** parâmetro. O exemplo de código seguinte associa um blob de texto para uma **cadeia** com o nome de parâmetro **logMessage**. A função utiliza esse parâmetro para escrever o conteúdo do blob para o dashboard do SDK de WebJobs.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Introdução ao serializar o conteúdo de blob utilizando ICloudBlobStreamBinder
O exemplo de código seguinte utiliza uma classe que implementa **ICloudBlobStreamBinder** para ativar o **BlobTrigger** atributo vincular um blob para o **WebImage** tipo.

        public static void WaterMark(
            [BlobTrigger("images3/{name}")] WebImage input,
            [Blob("images3-watermarked/{name}")] out WebImage output)
        {
            output = input.AddTextWatermark("WebJobs SDK",
                horizontalAlign: "Center", verticalAlign: "Middle",
                fontSize: 48, opacity: 50);
        }
        public static void Resize(
            [BlobTrigger("images3-watermarked/{name}")] WebImage input,
            [Blob("images3-resized/{name}")] out WebImage output)
        {
            var width = 180;
            var height = Convert.ToInt32(input.Height * 180 / input.Width);
            output = input.Resize(width, height);
        }

O **WebImage** enlace código é fornecido num **WebImageBinder** classe que deriva de **ICloudBlobStreamBinder**.

        public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
        {
            public Task<WebImage> ReadFromStreamAsync(Stream input,
                System.Threading.CancellationToken cancellationToken)
            {
                return Task.FromResult<WebImage>(new WebImage(input));
            }
            public Task WriteToStreamAsync(WebImage value, Stream output,
                System.Threading.CancellationToken cancellationToken)
            {
                var bytes = value.GetBytes();
                return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
            }
        }

## <a name="how-to-handle-poison-blobs"></a>Como processar nocivas blobs
Quando um **BlobTrigger** função falhar, o SDK denomina-novo, no caso da falha foi causada por um erro transitório. Se a falha é causada pelo conteúdo do blob, a função falha sempre que tentar processar o blob. Por predefinição, o SDK chama uma função de até 5 vezes para um blob especificado. Se a quinta tente falhar, o SDK adiciona uma mensagem para uma fila com o nome *webjobs-blobtrigger-poison*.

O número máximo de tentativas é configurável. O mesmo **MaxDequeueCount** definição é utilizada para processar de blob nocivas e processamento de mensagens de fila nocivas.

A mensagem da fila para blobs nocivas é um objeto JSON que contém as seguintes propriedades:

* FunctionId (no formato *{nome do WebJob}*. Funções. *{Nome da função}*, por exemplo: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

No seguinte exemplo de código, o **CopyBlob** função tem o código que provoca a falha sempre que é denominado. Depois do SDK denomina-o para o número máximo de tentativas, é criada uma mensagem na fila nocivas blob e essa mensagem é processada pelo **LogPoisonBlob** função.

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("textblobs/output-{name}")] out string output)
        {
            throw new Exception("Exception for testing poison blob handling");
            output = input.ReadToEnd();
        }

        public static void LogPoisonBlob(
        [QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
            TextWriter logger)
        {
            logger.WriteLine("FunctionId: {0}", message.FunctionId);
            logger.WriteLine("BlobType: {0}", message.BlobType);
            logger.WriteLine("ContainerName: {0}", message.ContainerName);
            logger.WriteLine("BlobName: {0}", message.BlobName);
            logger.WriteLine("ETag: {0}", message.ETag);
        }

O SDK deserializes automaticamente a mensagem JSON. Eis o **PoisonBlobMessage** classe:

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Algoritmo de consulta de blob
O SDK de WebJobs analisa todos os contentores especificados pelo **BlobTrigger** atributos no início da aplicação. Numa conta do storage grande esta análise pode demorar algum tempo, pelo que poderá ser tempo antes de encontram-se os novos blobs e **BlobTrigger** funções são executadas.

Para detetar novos ou alterados blobs após o início de aplicações, o SDK lê periodicamente dos registos de armazenamento de Blobs. Os registos de blob são colocado na memória intermédia e só fisicamente escrever cada 10 minutos ou por isso, por isso, poderá existir atraso significativo depois de um blob é criado ou atualizado antes do correspondente **BlobTrigger** executa a função.

Há uma exceção para blobs que criar utilizando o **Blob** atributo. Quando o SDK de WebJobs cria um novo blob, estes passam o novo blob imediatamente para qualquer correspondência **BlobTrigger** funções. Por conseguinte, se tiver uma cadeia de blob entradas e saídas, o SDK pode processá-los com eficiência. Mas se pretender executar o blob de funções de processamento para blobs que são criados ou atualizados através de outros meios de latência baixa, recomendamos que utilize **QueueTrigger** vez **BlobTrigger**.

### <a name="blob-receipts"></a>Recibos de blob
O SDK de WebJobs certifica-se de que nenhum **BlobTrigger** função obtém chamada mais do que uma vez para o mesmo blob novo ou atualizado. Fazê-lo, mantendo *blob recibos* para determinar se uma versão de blob especificado foi processada.

Blob recibos são armazenados num contentor com o nome *anfitriões de webjobs do azure* na conta do storage do Azure especificada pela cadeia de ligação de AzureWebJobsStorage. Um recibo de blob tem as seguintes informações:

* A função que foi chamada para o blob ("*{nome do WebJob}*. Funções. *{Nome da função}*", por exemplo:"WebJob1.Functions.CopyBlob")
* O nome do contentor
* O tipo de blob ("BlockBlob" ou "PageBlob")
* O nome do blob
* O ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

Se pretender forçar o reprocessamento de um blob, pode eliminar manualmente a receção de BLOBs para esse blob do *anfitriões de webjobs do azure* contentor.

## <a name="related-topics-covered-by-the-queues-article"></a>Tópicos relacionados abrangidos pelo artigo de filas
Para obter informações sobre como lidar com o processamento de blob acionado por uma mensagem de fila ou para o SDK de WebJobs não específicos para o blob de cenários de processamento, consulte [como utilizar o armazenamento de filas do Azure com o SDK de WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).

Tópicos relacionados abordados esse artigo incluem o seguinte:

* Async funções
* Várias instâncias
* Encerramento correto
* Utilizar o SDK de WebJobs atributos no corpo de uma função
* Defina as cadeias de ligação do SDK no código.
* Definir valores para o SDK de WebJobs os parâmetros do construtor no código
* Configurar **MaxDequeueCount** para processamento de blob nocivas.
* Acionar manualmente uma função
* Escrever registos

## <a name="next-steps"></a>Passos seguintes
Este artigo forneceu exemplos de código que mostram como lidar com cenários comuns para trabalhar com blobs do Azure. Para obter mais informações sobre como utilizar WebJobs do Azure e o SDK de WebJobs, consulte [recursos de documentação de WebJobs do Azure](http://go.microsoft.com/fwlink/?linkid=390226).

