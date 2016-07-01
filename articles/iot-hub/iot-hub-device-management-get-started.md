<properties
    pageTitle="Introdução à gestão de dispositivos do IoT Hub | Microsoft Azure"
    description="IoT Hub do Azure para a gestão de dispositivos com o tutorial de introdução à C#. Utilize o IoT Hub e a C# do Azure com os SDKs IoT do Microsoft Azure para implementar a gestão de dispositivos."
    services="iot-hub"
    documentationCenter=".net"
    authors="ellenfosborne"
    manager="timlt"
    editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="elfarber"/>

# Introdução à gestão de dispositivos do IoT Hub do Azure com a C# (pré-visualização)

[AZURE.INCLUDE [iot-hub-device-management-get-started-selector](../../includes/iot-hub-device-management-get-started-selector.md)]

## Introdução
Para começar a utilizar a gestão de dispositivos do IoT Hub do Azure, tem de criar um IoT Hub do Azure, aprovisionar dispositivos no IoT Hub e iniciar vários dispositivos simulados. Este tutorial explica-lhe como seguir esses passos.

> [AZURE.NOTE]  Tem de criar um novo IoT Hub para ativar as funcionalidades de gestão de dispositivos, mesmo se tiver um IoT Hub já que os IoT Hubs existentes ainda não dispõem de funcionalidades de gestão de dispositivos. Assim que a gestão de dispositivos estiver geralmente disponível, todos os IoT Hubs existentes serão atualizados para obterem as funcionalidades de gestão de dispositivos.

## Pré-requisitos

Os seguintes elementos terão de estar instalados para concluir os passos:

- Microsoft Visual Studio 2015
- Git
- CMake (versão 2.8 ou posterior). Instale o CMake a partir de <https://cmake.org/download/>. Para um PC com Windows, escolha a opção Windows Installer (. msi). Certifique-se de selecionar a caixa para adicionar o CMake à atual variável de CAMINHO de utilizador.
- Uma subscrição ativa do Azure.

    Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure][lnk-free-trial].

## Criar um dispositivo de gestão ativado para o IoT Hub

Tem de criar um dispositivo de gestão ativado para o IoT Hub aos quais deverá ligar os seus dispositivos. Os passos que se seguem mostram-lhe como concluir esta tarefa no Portal do Azure.

1.  Inicie sessão no [Portal do Azure].
2.  Na Barre de índice, clique em **Novo**, em seguida, clique em **Internet das Coisas** e, em seguida, clique em **IoT Hub do Azure**.

    ![][img-new-hub]

3.  No painel **IoT Hub**, escolha a configuração para o seu IoT Hub.

    ![][img-configure-hub]

  -   Na caixa **Nome**, introduza um nome para o seu IoT Hub. Se o **Nome** for válido e estiver disponível, irá aparecer uma marca de verificação verde na caixa **Nome**.
  -   Selecione um **Escalão de preços e dimensionamento**. Este tutorial não requer nenhum escalão específico.
  -   Em **Grupo de recursos**, crie um novo grupo de recursos ou selecione um existente. Para obter mais informações, consulte [Utilizar grupos de recursos para gerir os seus recursos do Azure].
  -   Selecione a caixa para **Ativar a Gestão de Dispositivos**.
  -   Em **Localização**, selecione a localização para alojar o seu IoT Hub. A gestão de dispositivos do IoT Hub apenas está disponível nos EUA Leste e na Ásia Oriental durante a pré-visualização pública. No futuro, estará disponível em todas as regiões.

    > [AZURE.NOTE]  Se não selecionar a caixa para **Ativar a Gestão de Dispositivos**, os exemplos não irão funcionar.

4.  Quando tiver escolhido as suas opções de configuração para o seu IoT Hub, clique em **Criar**. O Azure poderá levar alguns minutos a criar o seu IoT Hub. Para verificar o estado, pode monitorizar o progresso no **Startboard** ou no painel **Notificações**.

    ![][img-monitor]

5.  Quando tiver criado o IoT Hub com êxito, abra o painel do novo IoT Hub, tome nota do **Nome de anfitrião** e, em seguida, clique no ícone **Chaves**.

    ![][img-keys]

6.  Clique na política **iothubowner** e copie e tome nota da cadeia de ligação no painel **iothubowner**. Copie-a numa localização a que poderá aceder posteriormente uma vez que será necessária para concluir este tutorial.

    > [AZURE.NOTE] Em cenários de produção, certifique-se de não utilizar as credenciais do **iothubowner**.

    ![][img-connection]

Criou um dispositivo de gestão ativado para o IoT Hub Irá precisar da cadeia de ligação para concluir este tutorial.

## Criar exemplos e aprovisionar dispositivos no seu IoT Hub

Nesta secção, irá executar um script que cria o dispositivo simulado e os exemplos e aprovisiona um conjunto de novas identidades de dispositivo no registo do dispositivo do seu IoT Hub. Não é possível ligar um dispositivo ao IoT Hub exceto se tiver uma entrada no registo de dispositivo.

Para criar os exemplos e aprovisionar dispositivos no seu IoT Hub, siga os passos abaixo:

1.  Abra a **Linha de Comandos do Programador para o VS2015**.

2.  Clone o repositório do github. **Certifique-se de clonar num diretório sem quaisquer espaços.**

      ```
      git clone --recursive --branch dmpreview https://github.com/Azure/azure-iot-sdks.git
      ```

3.  A partir da pasta raiz onde clonou o repositório **azure-iot-sdks**, navegue para a pasta **\\azure-iot-sdks\\csharp\\service\\samples** e execute, substituindo o valor do marcador de posição pela sua cadeia de ligação da secção anterior:

      ```
      setup.bat <IoT Hub Connection String>
      ```

Este script faz o seguinte:

1.  Executa o **cmake** para criar uma solução do Visual Studio 2015 para o dispositivo simulado. Este ficheiro de projeto é o **azure-iot-sdks\\csharp\\service\\samples\\cmake\\iotdm\_client\\samples\\iotdm\_simple\_sample\\iotdm\_simple\_sample.vcxproj**. Tenha em atenção que os ficheiros de origem estão na pasta ***azure-iot-sdks\\c\\iotdm\_client\\samples\\iotdm\_simple\_sample**.

2.  Cria o projeto do dispositivo simulado **iotdm\_simple\_sample.vcxproj**.

3.  Cria os exemplos de gestão do dispositivo **azure-iot-sdks\\csharp\\service\\samples\\GetStartedWithIoTDM\\GetStartedWithIoTDM.sln**.

4.  Executa o **GenerateDevices.exe** para aprovisionar as identidades do dispositivo no seu IoT Hub. Os dispositivos encontram-se descritos em **sampledevices.json** (localizado na pasta **azure-iot-sdks\\node\\service\\samples**). As credenciais serão armazenadas no ficheiro **devicecreds.txt** (localizado na pasta **azure-iot-sdks\\csharp\\service\\samples\\bin**) depois de os dispositivos terem sido aprovisionados.

## Iniciar os seus dispositivos simulados

Agora que os dispositivos foram adicionados ao registo do dispositivo, pode iniciar dispositivos simulados geridos. É iniciado um dispositivo simulado para cada identidade de dispositivo aprovisionada no IoT Hub do Azure.

Com a linha de comandos do programador, na pasta **\\azure-iot-sdks\\csharp\\service\\samples\\bin**, execute:

  ```
  simulate.bat
  ```

Este script executa uma instância de **iotdm\_simples\_sample.exe** para cada dispositivo listado no ficheiro **devicecreds.txt**. O dispositivo simulado continuará a ser executado até fechar a janela de comandos.

A aplicação de exemplo **iotdm\_simple\_sample** é compilada com a biblioteca de cliente de gestão de dispositivos do IoT Hub do Azure para C, que permite criar dispositivos de IoT que podem ser geridos pelo IoT Hub do Azure. Os criadores de dispositivos podem utilizar esta biblioteca para comunicar as propriedades do dispositivo e implementar as ações executáveis requeridas pelas tarefas do dispositivo. Esta biblioteca é um componente fornecido como parte dos SDKs de open source para IoT Hub do Azure.

Quando executa **simulate.bat**, verá um fluxo de dados na janela de resultados. Esta janela de resultados mostra o tráfego de entrada e de saída, bem como as declarações **printf** nas funções de chamada de retorno específicas da aplicação. Tal permite-lhe ver o tráfego de entrada e saída, juntamente com a forma como a aplicação de exemplo está a processar os pacotes descodificados. Quando o dispositivo estabelece ligação ao IoT Hub, o serviço começa automaticamente a observar os recursos no dispositivo. A biblioteca de cliente de DM do IoT Hub invoca as chamadas de retorno do dispositivo para obter os valores mais recentes do dispositivo.

Encontrará abaixo o resultado da aplicação de exemplo **iotdm\_simple\_sample**. Na parte superior, é apresentada uma mensagem **REGISTADO** com êxito, que mostra o dispositivo com o Id **Device11 7ce4a850** a estabelecer ligação ao IoT Hub.

> [AZURE.NOTE]  Para obter um resultado menos descritivo, crie e execute a configuração de retalho.

![][img-output]

Certifique-se de que todos os dispositivos simulados são executados quando concluir os tutoriais nos "Passos seguintes".

## Passos seguintes

Para saber mais sobre as funcionalidades de gestão do dispositivo IoT Hub do Azure, pode percorrer os tutoriais:

- [Como utilizar o dispositivo twin][Ink-tutorial-twin]

- [Como localizar o dispositivo twins através de consultas][Ink-tutorial-queries]

- [Como utilizar tarefas do dispositivo para atualizar o firmware do dispositivo][lnk-tutorial-jobs]

<!-- images and links -->
[img-new-hub]: media/iot-hub-device-management-get-started/image1.png
[img-configure-hub]: media/iot-hub-device-management-get-started/image2.png
[img-monitor]: media/iot-hub-device-management-get-started/image3.png
[img-keys]: media/iot-hub-device-management-get-started/image4.png
[img-connection]: media/iot-hub-device-management-get-started/image5.png
[img-output]: media/iot-hub-device-management-get-started/image6.png

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Portal do Azure]: https://portal.azure.com/
[Utilizar grupos de recursos para gerir os seus recursos do Azure]: ../azure-portal/resource-group-portal.md
[Ink-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md



<!--HONumber=Jun16_HO2-->


