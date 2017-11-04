---
title: Carregar ficheiros de dispositivos para o IoT Hub do Azure com o .NET | Microsoft Docs
description: "Como carregar ficheiros a partir de um dispositivo para a nuvem através de dispositivos IoT do Azure SDK para .NET. Ficheiros carregados são armazenados num contentor do blob storage do Azure."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2017
ms.author: elioda
ms.openlocfilehash: f0be85d661807f36890fc79090c3f820ae8c376d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-using-net"></a>Carregar ficheiros do seu dispositivo para a nuvem com o IoT Hub através do .NET

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este tutorial baseia-se o código de [enviar mensagens da nuvem para o dispositivo com o IoT Hub](iot-hub-csharp-csharp-c2d.md) tutorial para mostrar-lhe como utilizar as capacidades de carregamento de ficheiros do IoT Hub. Mostra-lhe como para:

- Fornecer em segurança um dispositivo com um Azure blob URI para carregar um ficheiro.
- Utilize as notificações de carregamento de ficheiros do IoT Hub para acionar a processar o ficheiro na sua aplicação de back-end.

O [introdução ao IoT Hub](iot-hub-csharp-csharp-getstarted.md) e [enviar mensagens da nuvem para o dispositivo com o IoT Hub](iot-hub-csharp-csharp-c2d.md) os tutoriais mostram a dispositivo para nuvem e da nuvem para o dispositivo mensagens funcionalidades básicas do IoT Hub. O [mensagens do dispositivo-nuvem de processo](iot-hub-csharp-csharp-process-d2c.md) tutorial descreve uma forma de forma fiável armazenar mensagens do dispositivo para nuvem no armazenamento de Blobs do Azure. No entanto, em alguns cenários não pode facilmente mapear os dados que os dispositivos as enviam para as mensagens dispositivo-nuvem relativamente pequenas, que aceita o IoT Hub. Por exemplo:

* Ficheiros grandes que contenham imagens
* Vídeos
* Dados vibration amostragem alta frequência
* Algum tipo de dados pré-processados

Estes ficheiros são, normalmente, batch processado na nuvem através de ferramentas, como [do Azure Data Factory](../data-factory/introduction.md) ou [Hadoop](../hdinsight/index.md) pilha. Quando precisar de carregar ficheiros a partir de um dispositivo, pode continuar a utilizar a segurança e fiabilidade do IoT Hub.

No final deste tutorial, executar duas aplicações de consola .NET:

* **SimulatedDevice**, uma versão modificada da aplicação criada no [enviar mensagens da nuvem para o dispositivo com o IoT Hub](iot-hub-csharp-csharp-c2d.md) tutorial. Esta aplicação carrega um ficheiro para o armazenamento através de um URI de SAS fornecida pelo seu IoT hub.
* **ReadFileUploadNotification**, que recebe notificações de carregamento de ficheiros do seu IoT hub.

> [!NOTE]
> IoT Hub suporta várias plataformas de dispositivos e idiomas (incluindo C, Java e Javascript) através de SDKs do dispositivo IoT do Azure. Consulte o [Centro de programadores do IoT do Azure] para obter instruções passo a passo sobre como ligar o seu dispositivo ao IoT Hub do Azure.

Para concluir este tutorial, precisa do seguinte:

* Visual Studio 2015 ou Visual Studio 2017
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Carregar um ficheiro a partir de uma aplicação de dispositivo

Nesta secção, modificar a aplicação de dispositivo que criou no [enviar mensagens da nuvem para o dispositivo com o IoT Hub](iot-hub-csharp-csharp-c2d.md) para receber mensagens da nuvem para dispositivos do IoT hub.

1. No Visual Studio, clique com botão direito do **SimulatedDevice** do projeto, clique em **adicionar**e, em seguida, clique em **Item existente**. Navegue para um ficheiro de imagem e incluí-la no seu projeto. Este tutorial assume que a imagem é denominada `image.jpg`.

1. Faça duplo clique na imagem e, em seguida, clique em **propriedades**. Certifique-se de que **copiar para o diretório de saída** está definido como **Copiar sempre**.

    ![][1]

1. No **Program.cs** ficheiro, adicione as seguintes declarações na parte superior do ficheiro:

    ```csharp
    using System.IO;
    ```

1. Adicione o seguinte método à classe **Programa**:

    ```csharp
    private static async void SendToBlobAsync()
    {
        string fileName = "image.jpg";
        Console.WriteLine("Uploading file: {0}", fileName);
        var watch = System.Diagnostics.Stopwatch.StartNew();

        using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
        {
            await deviceClient.UploadToBlobAsync(fileName, sourceData);
        }

        watch.Stop();
        Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
    }
    ```

    O `UploadToBlobAsync` método aceita a origem de nome e o fluxo do ficheiro do ficheiro para ser carregado e processa o carregamento para o armazenamento. A aplicação de consola mostra o tempo que demora a carregar o ficheiro.

1. Adicione o seguinte método no **Main** método, imediatamente antes do `Console.ReadLine()` linha:

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Para sake do simplicidade, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como término exponencial), como sugerido no artigo do MSDN [processamento de erros transitórios].

## <a name="receive-a-file-upload-notification"></a>Receber uma notificação de carregamento de ficheiros

Nesta secção, escreve uma aplicação de consola .NET que recebe mensagens de notificação de carregamento de ficheiros a partir do IoT Hub.

1. A atual solução do Visual Studio, crie um projeto de Visual c# Windows utilizando o **aplicação de consola** modelo de projeto. Nomeie o projeto **ReadFileUploadNotification**.

    ![Novo projeto no Visual Studio][2]

1. No Explorador de soluções, clique com botão direito do **ReadFileUploadNotification** projeto e, em seguida, clique em **gerir pacotes NuGet...** .

1. No **Gestor de pacotes NuGet** janela, procure **Microsoft.Azure.Devices**, clique em **instalar**e aceite os termos de utilização.

    Esta ação transfere, instala e adiciona uma referência para o [pacote NuGet do SDK do Azure IoT serviço] no **ReadFileUploadNotification** projeto.

1. No **Program.cs** ficheiro, adicione as seguintes declarações na parte superior do ficheiro:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação do hub IoT do [introdução ao IoT Hub]:

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. Adicione o seguinte método à classe **Programa**:

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();

        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    Tenha em atenção de que este padrão de receção é a mesma utilizado para receber mensagens da nuvem para o dispositivo da aplicação de dispositivo.

1. Por fim, adicione as seguintes linhas ao método **Main**:

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Executar as aplicações

Agora pode executar as aplicações.

1. No Visual Studio, clique com o botão direito a solução e selecione **definir projetos de arranque**. Selecione **vários projetos de arranque**, em seguida, selecione o **iniciar** ação para **ReadFileUploadNotification** e **SimulatedDevice**.

1. Prima **F5**. Devem começar a ambas as aplicações. Deverá ver o carregamento foi concluída numa aplicação de consola e a mensagem de notificação de carregamento recebidas por outra aplicação de consola. Pode utilizar o [portal do Azure] ou Explorador de servidores do Visual Studio para verificar a presença do ficheiro carregado na sua conta do Storage do Azure.

    ![][50]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a utilizar as capacidades de carregamento de ficheiros do IoT Hub para simplificar os carregamentos de ficheiros a partir de dispositivos. Pode continuar a explorar as funcionalidades do IoT hub e cenários nos seguintes artigos:

* [Criar um hub IoT através de programação][lnk-create-hub]
* [Introdução ao C SDK][lnk-c-sdk]
* [SDKs IoT do Azure][lnk-sdks]

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Simulando um dispositivo com o limite de IoT][lnk-iotedge]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png

<!-- Links -->

[portal do Azure]: https://portal.azure.com/

[Centro de programadores do IoT do Azure]: http://www.azure.com/develop/iot

[processamento de erros transitórios]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[pacote NuGet do SDK do Azure IoT serviço]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: iot-hub-windows-iot-edge-simulated-device.md
