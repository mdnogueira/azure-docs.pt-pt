---
title: "Tutorial de segurança de x. 509 no IoT Hub do Azure | Microsoft Docs"
description: "Introdução da segurança de x. 509 com base no seu hub IoT do Azure num ambiente simulado."
services: iot-hub
documentationcenter: 
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2017
ms.author: dkshir
ms.openlocfilehash: 93f9099d7aef1161f7789e7b21a88a8691cb2a8e
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Como configurar a segurança de x. 509 do seu hub IoT do Azure

Este tutorial simula os passos necessários para proteger o seu hub IoT do Azure a utilizar o *autenticação de certificado x. 509*. Para efeitos de ilustração, vamos mostrar como utilizar a ferramenta de código aberto OpenSSL para criar certificados localmente no seu computador Windows. Recomendamos que utilize este tutorial para fins de teste. Para o ambiente de produção, deve comprar os certificados de um *autoridade de certificação (AC) de raiz*. 

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial, necessita que tem os seguintes recursos prontos:

- Criou um IoT hub com a sua subscrição do Azure. Consulte [criar um hub IoT através do portal](iot-hub-create-through-portal.md) para obter passos detalhados. 
- Tiver [Visual Studio 2015 ou Visual Studio 2017](https://www.visualstudio.com/vs/) instalado no seu computador. 

<a id="getcerts"></a>

## <a name="get-x509-ca-certificates"></a>Obter certificados de AC de x. 509
A segurança baseada em certificados de x. 509 no IoT Hub requer começar com uma [cadeia de certificados x. 509](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), que inclui o certificado de raiz, bem como quaisquer certificados intermediários até o certificado de folha. 

Pode escolher uma das formas seguintes para obter os certificados:
- Comprar certificados x. 509 de um *autoridade de certificação (AC) de raiz*. É recomendado para ambientes de produção.
OU,
- Criar os seus próprios certificados x. 509 utilizando uma ferramenta de terceiros, tais como [OpenSSL](https://www.openssl.org/). Este será bem para fins de desenvolvimento e teste. As secções intituladas *criar os certificados x. 509* e *cadeia de certificados x. 509 criar* no artigo [como utilizar o PowerShell para criar certificados x. 509](iot-hub-security-x509-create-certificates.md) guiá-lo através de um script do PowerShell de exemplo para criar os certificados através de OpenSSL e PowerShell. Se preferir utilizar **Bash** shell em vez do PowerShell, consulte as secções relacionadas de [gerir exemplo de certificados de AC](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md). O resto deste tutorial, irá utilizar o ambiente de OpenSSL configurar deste *como* guia para ajudá-lo a segurança de x. 509 do ponto-a-ponto no IoT Hub do Azure.


<a id="registercerts"></a>

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Registar certificados de AC de x. 509 ao seu IoT hub

Estes passos mostram como adicionar uma nova autoridade de certificação ao seu IoT hub através do portal.

1. No portal do Azure, navegue até ao seu IoT hub e abra o **definições** > **certificados** menu. 
2. Clique em **adicionar** para adicionar um novo certificado.
3. Introduza um nome a apresentar amigável ao certificado. Selecione o ficheiro de certificado de raiz denominado *RootCA.cer* criado na secção anterior, a partir do seu computador. Clique em **Carregar**.
4. Depois de obter uma notificação que o certificado é carregado com êxito, clique em **guardar**.

    ![Carregar certificado](./media/iot-hub-security-x509-get-started/add-new-cert.png)  

   Isto irá mostrar o certificado no **certificado Explorer** lista. Tenha em atenção o **estado** deste certificado está *Unverified*.

5. Clique no certificado que adicionou no passo anterior.

6. No **detalhes do certificado** painel, clique em **gerar código de verificação**.

7. Cria um **código de verificação** para validar a propriedade de certificado. Copie o código para a sua área de transferência. 

   ![Verificar o certificado](./media/iot-hub-security-x509-get-started/verify-cert.png)  

8. Agora, tem de se inscrever este *código de verificação* com a chave privada associar com o certificado x. 509 AC, o que gera uma assinatura. Existem ferramentas disponíveis para efetuar este processo de assinatura, por exemplo, OpenSSL. Isto é conhecido como o [prova de posse](https://tools.ietf.org/html/rfc5280#section-3.1). Se tiver utilizado o nosso scripts do PowerShell de exemplo na secção anterior, em seguida, execute o script mencionado na secção intitulada [prova de posse do seu certificado de AC de x. 509](iot-hub-security-x509-create-certificates.md#signverificationcode).
 
9. Carregue a assinatura resultante do passo 8 acima ao seu IoT hub no portal. No **detalhes do certificado** painel no portal do Azure, navegue para o **ficheiro. pem ou. cer de certificado de verificação**e selecione a assinatura, por exemplo, *VerifyCert4.cer*criados utilizando o exemplo do PowerShell comando o _Explorador de ficheiros_ ícone para além do mesmo.

10. Depois do certificado é carregado com êxito, clique em **verifique**. O **estado** das alterações de certificado para  **_Verified_**  no **certificados** painel. Clique em **atualizar** se não atualizar automaticamente.

   ![Carregar a verificação de certificado](./media/iot-hub-security-x509-get-started/upload-cert-verification.png)  


<a id="createdevice"></a>

## <a name="create-an-x509-device-for-your-iot-hub"></a>Criar um dispositivo de x. 509 do IoT hub

1. No portal do Azure, navegue até ao seu IoT hub **Explorador de dispositivo**.

2. Clique em **adicionar** para adicionar um novo dispositivo. 

3. Dê um nome a apresentar amigável para o **ID de dispositivo**e selecione  **_x. 509 AC assinado_**  como o **tipo de autenticação**. Clique em **Guardar**.

   ![Criar dispositivo x. 509 no portal](./media/iot-hub-security-x509-get-started/create-x509-device.png)



<a id="authenticatedevice"></a>

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Autenticar o dispositivo de x. 509 com os certificados x. 509

Para autenticar o dispositivo de x. 509, terá de sessão pela primeira vez o dispositivo com o certificado da AC. Assinatura de dispositivos de folha normalmente é realizada à maquinaria de fabrico, onde as ferramentas de fabrico foram ativadas em conformidade. Como o dispositivo fica a partir de um fabricante para outro, ação de assinatura de cada fabricante é capturada como uma intermediária na cadeia. O resultado final é uma cadeia de certificados do certificado da AC para o certificado de folha do dispositivo. Se utiliza o nosso scripts do PowerShell nas secções anteriores, em seguida, pode executar o script mencionado na secção intitulada *certificado x. 509 de folha de criar para o seu dispositivo* no artigo [scripts do PowerShell para gerir certificados x. 509 assinado para AC](iot-hub-security-x509-create-certificates.md) para simular este processo.

Em seguida, vamos mostrar como criar uma aplicação c# para simular o dispositivo de x. 509 registado para o seu IoT hub. Iremos enviar relativos à temperatura e humidade valores do dispositivo simulado para o seu hub. Tenha em atenção que neste tutorial, iremos criar apenas a aplicação de dispositivo. For deixado como um exercício para leitores para criar a aplicação de serviço do IoT Hub que vai enviar a resposta a eventos enviados por este dispositivo simulado. A aplicação c# parte do princípio de que seguiu os scripts do PowerShell mencionados no artigo [scripts do PowerShell para gerir os certificados x. 509 assinado para AC](iot-hub-security-x509-create-certificates.md)

1. No Visual Studio, crie um novo projeto do Visual c# clássico ambiente de trabalho Windows utilizando o modelo de projeto de aplicação de consola. Nomeie o projeto **SimulateX509Device**.
   ![Criar projeto do dispositivo de x. 509 no Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project.png)

2. No Explorador de soluções, clique com botão direito do **SimulateX509Device** projeto e, em seguida, clique em **gerir pacotes NuGet...** . Na janela do Gestor de pacotes NuGet, selecione **procurar** e procure **microsoft.azure.devices.client**. Selecione **instalar** para instalar o **Microsoft.Azure.Devices.Client** do pacote e aceite os termos de utilização. Este procedimento transfere, instala e adiciona uma referência para o pacote de SDK NuGet do dispositivo IoT do Azure e as respetivas dependências.
   ![Adicionar o pacote NuGet do SDK de dispositivo no Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

3. Adicione as seguintes linhas de código na parte superior do *Program.cs* ficheiro:
    
    ```CSharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

4. Adicione as seguintes linhas de código dentro do **programa** classe:
    
    ```CSharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```
   Utilize o nome amigável do dispositivo que utilizou na secção anterior em vez de _< your_device_id >_ marcador de posição.

5. Adicione a seguinte função para criar números aleatórios para relativos à temperatura e humidade e enviar estes valores ao hub:
    ```CSharp
    static async Task SendEvent(DeviceClient deviceClient)
    {
        string dataBuffer;
        Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

        for (int count = 0; count < MESSAGE_COUNT; count++)
        {
            temperature = rnd.Next(20, 35);
            humidity = rnd.Next(60, 80);
            dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
            Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
            eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
            Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

            await deviceClient.SendEventAsync(eventMessage);
        }
    }
    ```

6. Por fim, adicione as seguintes linhas de código para o **Main** função, substituindo os marcadores de posição _id de dispositivo_, _-iot-hub-nome do seu_ e  _Absolute-path-to-your-Device-PFX-File_ conforme requerido pelo seu programa de configuração.
    ```CSharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "123");
        var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
        var deviceClient = DeviceClient.Create("<your-iot-hub-name>.azure-devices.net", auth, TransportType.Amqp_Tcp_Only);

        if (deviceClient == null)
        {
            Console.WriteLine("Failed to create DeviceClient!");
        }
        else
        {
            Console.WriteLine("Successfully created DeviceClient!");
            SendEvent(deviceClient).Wait();
        }

        Console.WriteLine("Exiting...\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
   Este código liga-se ao seu IoT hub através da criação de cadeia de ligação para o seu dispositivo de x. 509. Assim que estiver ligado com êxito, este, em seguida, envia eventos relativos à temperatura e humidade para o hub e aguarda a respetiva resposta. 
7. Uma vez que acede a esta aplicação uma *. pfx* ficheiros, terá de executar este no *Admin* modo. Compilar a solução do Visual Studio. Abra uma nova janela de comandos como um **administrador**e navegue para a pasta que contém esta solução. Navegue para o *bin/depuração* caminho dentro da pasta de solução. Executar a aplicação **SimulateX509Device.exe** do _Admin_ janela de comandos. Deverá ver o seu dispositivo com êxito a ligação ao hub e o envio de eventos. 
   ![Executar a aplicação de dispositivo](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="see-also"></a>Consultar também
Para obter mais informações sobre como proteger a sua solução de IoT, consulte:

* [Práticas recomendadas de segurança de IoT][lnk-security-best-practices]
* [Arquitetura de segurança de IoT][lnk-security-architecture]
* [Proteger a implementação de IoT][lnk-security-deployment]

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Implementar o AI para dispositivos de limite com limite de IoT do Azure][lnk-iotedge]

[lnk-security-best-practices]: iot-hub-security-best-practices.md
[lnk-security-architecture]: iot-hub-security-architecture.md
[lnk-security-deployment]: iot-hub-security-deployment.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
