<properties
    pageTitle="Introdução à gestão de dispositivos do IoT Hub | Microsoft Azure"
    description="IoT Hub do Azure para a gestão de dispositivos com o tutorial de introdução à C#. Utilize o IoT Hub e a C# do Azure com os SDKs IoT do Microsoft Azure para implementar a gestão de dispositivos."
    services="iot-hub"
    documentationCenter=".net"
    authors="juanjperez"
    manager="timlt"
    editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/11/2016"
 ms.author="juanpere"/>

# Introdução à gestão de dispositivos do IoT Hub do Azure com a C# (pré-visualização)

[AZURE.INCLUDE [iot-hub-device-management-get-started-selector](../../includes/iot-hub-device-management-get-started-selector.md)]

## Introdução
Para começar a utilizar a gestão de dispositivos do Hub IoT do Azure, tem de criar um Hub IoT do Azure, aprovisionar dispositivos no Hub IoT, iniciar vários dispositivos simulados e ver estes dispositivos na IU de amostra de gestão de dispositivos. Este tutorial explica-lhe como seguir esses passos.

> [AZURE.NOTE]  Tem de criar um novo IoT Hub para ativar as funcionalidades de gestão de dispositivos mesmo se já tiver um, já que os IoT Hubs existentes ainda não dispõem destas funcionalidades. Assim que a gestão de dispositivos estiver geralmente disponível, todos os IoT Hubs existentes serão atualizados para obterem as funcionalidades de gestão de dispositivos.

## Pré-requisitos

Este tutorial parte do pressuposto que está a utilizar uma máquina de desenvolvimento Windows.

Os seguintes elementos terão de estar instalados para concluir os passos:

- Microsoft Visual Studio 2015

- Git

- CMake (versão 2.8 ou posterior). Instale o CMake a partir de <https://cmake.org/download/>. Para um PC Windows, escolha a opção Windows Installer (.msi). Certifique-se de selecionar a caixa para adicionar o CMake à atual variável de CAMINHO de utilizador.

- Node.js 6.1.0 ou superior.  Instale o Node.js para a sua plataforma a partir de <https://nodejs.org/>.

- Uma subscrição ativa do Azure. Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure][lnk-free-trial].

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

    > [AZURE.NOTE]  Se não marcar a caixa para **Ativar Gestão do Dispositivo**, os exemplos não funcionarão.<br/>Ao marcar a caixa **Ativar Gestão do Dispositivo**, cria uma pré-visualização do IoT Hub suportada apenas nos EUA Leste, na Europa do Norte e na Ásia Oriental e não destinada a cenários de produção. Não é possível migrar dispositivos para dentro e fora de hubs de gestão de dispositivos ativados.

4.  Quando tiver escolhido as suas opções de configuração para o seu IoT Hub, clique em **Criar**. O Azure poderá levar alguns minutos a criar o seu IoT Hub. Para verificar o estado, pode monitorizar o progresso no **Startboard** ou no painel **Notificações**.

    ![][img-monitor]

5.  Quando tiver criado o IoT Hub com êxito, abra o painel do novo IoT Hub, tome nota do **Nome de anfitrião** e, em seguida, clique em **Políticas de acesso partilhado**.

    ![][img-keys]

6.  Clique na política **iothubowner** e copie e tome nota da cadeia de ligação no painel **iothubowner**. Copie-a para uma localização à qual poderá aceder posteriormente, uma vez que é necessária para concluir este tutorial.

    > [AZURE.NOTE] Em cenários de produção, certifique-se de não utilizar as credenciais do **iothubowner**.

    ![][img-connection]

Criou um dispositivo de gestão ativado para o IoT Hub Vai precisar da cadeia de ligação para concluir este tutorial.

## Criar exemplos e aprovisionar dispositivos no seu IoT Hub

Nesta secção, vai executar um script que cria o dispositivo simulado e os exemplos e aprovisiona um conjunto de novas identidades do dispositivo no registo do dispositivo do seu IoT Hub. Não é possível ligar um dispositivo ao IoT Hub exceto se tiver uma entrada no registo de dispositivo.

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

Este script executa uma instância de **iotdm\_simples\_sample.exe** para cada dispositivo listado no ficheiro **devicecreds.txt**. O dispositivo simulado continua a ser executado até fechar a janela de comandos.

A aplicação de exemplo **iotdm\_simple\_sample** é compilada com a biblioteca de cliente de gestão de dispositivos do IoT Hub do Azure para C, que permite criar dispositivos de IoT que podem ser geridos pelo IoT Hub do Azure. Os criadores de dispositivos podem utilizar esta biblioteca para comunicar as propriedades do dispositivo e implementar as ações executáveis requeridas pelas tarefas do dispositivo. Esta biblioteca é um componente fornecido como parte dos SDKs de open source para IoT Hub do Azure.

Quando executa **simulate.bat**, verá um fluxo de dados na janela de resultados. Esta janela de resultados mostra o tráfego de entrada e de saída, bem como as declarações **printf** nas funções de chamada de retorno específicas da aplicação. Esta janela permite-lhe ver o tráfego de entrada e de saída, juntamente com a forma como a aplicação de exemplo está a processar os pacotes descodificados. Quando o dispositivo estabelece ligação ao IoT Hub, o serviço começa automaticamente a observar os recursos no dispositivo. A biblioteca de cliente de DM do IoT Hub invoca as chamadas de retorno do dispositivo para obter os valores mais recentes do dispositivo.

Encontrará abaixo o resultado da aplicação de exemplo **iotdm\_simple\_sample**. Na parte superior, é apresentada uma mensagem **REGISTADO** com êxito, que mostra o dispositivo com o ID **Device11 7ce4a850** a estabelecer ligação ao IoT Hub.

> [AZURE.NOTE]  Para obter um resultado menos descritivo, crie e execute a configuração de retalho.

![][img-output]

Certifique-se de que deixa todos os dispositivos simulados em execução à medida que concluir as seguintes secções.

## Executar a IU de amostra de gestão de dispositivos

Agora que aprovisionou um Hub IoT e tem vários dispositivos simulados em execução e registados para gestão, pode implementar a IU de amostra da gestão de dispositivos. A IU de amostra de gestão de dispositivos fornece-lhe um exemplo de como utilizar as APIs de gestão de dispositivos para criar uma experiência de IU interativa.  Para obter mais informações sobre a IU de amostra de gestão de dispositivos, incluindo [problemas conhecidos](https://github.com/Azure/azure-iot-device-management#knownissues), veja o repositório GitHub [IU da gestão de dispositivos IoT do Azure][lnk-dm-github].

Para obter, criar e executar a IU de amostra de gestão de dispositivos, siga estes passos:

1. Abra uma **linha de comandos**.

2. Confirme que instalou o Node.js 6.1.0 ou superior de acordo com a secção de pré-requisitos ao escrever `node --version`.

3. Clone o repositório GitHub da IU de gestão de dispositivos IoT do Azure executando o seguinte comando:

    ```
    git clone https://github.com/Azure/azure-iot-device-management.git
    ```
    
4. Na pasta raiz da sua cópia clonada do repositório de IU de gestão de dispositivos do IoT do Azure, execute o seguinte comando para obter os pacotes dependentes:

    ```
    npm install
    ```

5. Quando o comando de instalação de npm for concluído, execute o seguinte comando para criar o código:

    ```
    npm run build
    ```

6. Utilize um editor de texto para abrir o ficheiro user-config.json na raiz da pasta clonada. Substitua o texto "&lt;A SUA CADEIA DE LIGAÇÃO AQUI&gt;" pela cadeia de ligação do Hub IoT da secção anterior e guarde o ficheiro.

7. Na linha de comandos, execute o seguinte comando para iniciar a aplicação UX de gestão de dispositivos:

    ```
    npm run start
    ```

8. Quando a linha de comandos indicar "Os serviços foram iniciados", abra um browser (Edge/IE 11+/Safari/Chrome são suportados) e navegue para a aplicação de gestão de dispositivos no seguinte URL para ver os seus dispositivos simulados: <http://127.0.0.1:3003>.

    ![][img-dm-ui]

Deixe os dispositivos simulados e a aplicação de gestão de dispositivos em execução à medida que avança para o próximo tutorial de gestão de dispositivos.


## Passos seguintes

Para continuar a utilizar o Hub IoT, veja [Como utilizar o SDK Gateway][lnk-gateway-SDK].

Para saber mais sobre as funcionalidades de gestão de dispositivos do Hub IoT do Azure, veja o tutorial [Explorar a gestão de dispositivos do Hub IoT do Azure utilizando a IU de amostra][lnk-sample-ui].

<!-- images and links -->
[img-new-hub]: media/iot-hub-device-management-get-started/image1.png
[img-configure-hub]: media/iot-hub-device-management-get-started/image2.png
[img-monitor]: media/iot-hub-device-management-get-started/image3.png
[img-keys]: media/iot-hub-device-management-get-started/image4.png
[img-connection]: media/iot-hub-device-management-get-started/image5.png
[img-output]: media/iot-hub-device-management-get-started/image6.png
[img-dm-ui]: media/iot-hub-device-management-get-started/dmui.png

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Portal do Azure]: https://portal.azure.com/
[Utilizar grupos de recursos para gerir os seus recursos do Azure]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-sample-ui]: iot-hub-device-management-ui-sample.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md



<!---HONumber=ago16_HO4-->


