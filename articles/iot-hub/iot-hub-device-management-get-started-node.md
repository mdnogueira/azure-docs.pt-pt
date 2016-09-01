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

# Introdução à gestão de dispositivos do IoT Hub do Azure com o node.js (pré-visualização)

[AZURE.INCLUDE [iot-hub-device-management-get-started-selector](../../includes/iot-hub-device-management-get-started-selector.md)]

## Introdução
Para começar a utilizar a gestão de dispositivos do Hub IoT do Azure, tem de criar um Hub IoT do Azure, aprovisionar dispositivos no Hub IoT, iniciar vários dispositivos simulados e ver estes dispositivos na IU de amostra de gestão do dispositivo. Este tutorial explica-lhe como seguir esses passos.

> [AZURE.NOTE]  Tem de criar um novo Hub IoT para ativar as funcionalidades de gestão de dispositivos mesmo que tenha um Hub IoT, já que os Hubs IoT existentes ainda não dispõem destas funcionalidades. Assim que a gestão de dispositivos estiver geralmente disponível, todos os IoT Hubs existentes serão atualizados para obterem as funcionalidades de gestão de dispositivos.

## Pré-requisitos

Este tutorial parte do pressuposto que está a utilizar uma máquina de desenvolvimento Ubuntu Linux.

É necessário instalar o seguinte software para concluir os passos:

- Git

- gcc (versão 4.9 ou posterior). Pode verificar a versão atual instalada no seu ambiente utilizando o comando `gcc --version`. Para obter informações sobre como atualizar a sua versão do gcc no Ubuntu 14.04, veja o artigo <http://askubuntu.com/questions/466651/how-do-i-use-the-latest-gcc-4-9-on-ubuntu-14-04>.

- [CMake](https://cmake.org/download/) (versão 2.8 ou posterior). Pode verificar a versão atual instalada no seu ambiente utilizando o comando `cmake --version`.

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
  -   Selecione a caixa para **Ativar a Gestão de Dispositivos**. Se não selecionar a caixa para **Ativar a Gestão de Dispositivos**, os exemplos não funcionarão. Ao marcar a caixa **Ativar a Gestão de Dispositivos**, cria uma pré-visualização do Hub IoT suportada apenas nos E.U.A. Leste, na Europa do Norte e na Ásia Oriental e que não se destina a cenários de produção. Não pode migrar dispositivos para dentro e fora de hubs de gestão de dispositivos ativados.
  -   Em **Localização**, selecione a localização para alojar o seu IoT Hub. A gestão de dispositivos do IoT Hub apenas está disponível nos EUA Leste e na Ásia Oriental durante a pré-visualização pública. No futuro, estará disponível em todas as regiões.

4.  Quando tiver escolhido as suas opções de configuração para o seu IoT Hub, clique em **Criar**. O Azure poderá levar alguns minutos a criar o seu IoT Hub. Para verificar o estado, pode monitorizar o progresso no **Startboard** ou no painel **Notificações**.

    ![][img-monitor]

5.  Após criar o Hub IoT com êxito, abra o painel do novo Hub IoT, tome nota do **Nome de anfitrião** e, em seguida, clique em **Políticas de acesso partilhado**.

    ![][img-keys]

6.  Clique na política **iothubowner** e copie e tome nota da cadeia de ligação no painel **iothubowner**. Copie-a para uma localização à qual poderá aceder posteriormente, uma vez que é necessária para concluir este tutorial.

    > [AZURE.NOTE] Em cenários de produção, certifique-se de não utilizar as credenciais do **iothubowner**.

    ![][img-connection]

Criou um dispositivo de gestão ativado para o IoT Hub Irá precisar da cadeia de ligação para concluir este tutorial.

## Criar exemplos e aprovisionar dispositivos no seu IoT Hub

Nesta secção, irá executar um script que cria o dispositivo simulado e os exemplos e aprovisiona um conjunto de novas identidades de dispositivo no registo do dispositivo do seu Hub IoT. Não é possível ligar um dispositivo ao IoT Hub exceto se tiver uma entrada no registo de dispositivo.

Para criar os exemplos e aprovisionar dispositivos no seu Hub IoT, siga estes passos:

1.  Abra uma shell.

2.  Clone o repositório do github. **Certifique-se de clonar num diretório sem quaisquer espaços.**

      ```
      git clone --recursive --branch dmpreview https://github.com/Azure/azure-iot-sdks.git
      ```

3.  A partir da pasta raiz onde clonou o repositório **azure-iot-sdks**, navegue para o diretório **azure-iot-sdks/c/build_all/linux** e execute o seguinte comando para instalar os pacotes de pré-requisitos e as bibliotecas dependentes:

      ```
      ./setup.sh
      ```


4.  A partir da pasta raiz onde clonou o repositório **azure-iot-sdks** repository, navegue para o diretório **azure-iot-sdks/node/service/samples** e execute o seguinte comando, substituindo o valor do marcador de posição pela cadeia de ligação da secção anterior:

      ```
      ./setup.sh <IoT Hub Connection String>
      ```

Este script faz o seguinte:

1.  Executa o **cmake** para que os ficheiros de criação necessários criem o dispositivo simulado. O ficheiro executável está localizado em **azure-iot-sdks/node/service/samples/cmake/iotdm\_client/samples/iotdm\_simple\_sample**. Tenha em atenção que os ficheiros de origem estão na pasta **azure-iot-sdks/c/iotdm\_client/samples/iotdm\_simple\_sample**.

2.  Cria o ficheiro executável **iotdm\_simple\_sample** do dispositivo simulado.

3.  Executa `npm install` para instalar os pacotes necessários.

4.  Executa `node generate_devices.js` para aprovisionar as identidades de dispositivo no seu Hub IoT. Os dispositivos encontram-se descritos em **sampledevices.json**. Depois de os dispositivos terem sido aprovisionados, as credenciais são armazenadas no ficheiro **devicecreds.txt** (localizado no diretório **azure-iot-sdks/node/service/samples**).

## Iniciar os seus dispositivos simulados

Agora que os dispositivos foram adicionados ao registo do dispositivo, pode iniciar dispositivos simulados geridos. Tem de iniciar um dispositivo simulado para cada identidade de dispositivo aprovisionada no Hub IoT do Azure.

Utilizando uma shell, navegue para o diretório **azure-iot-sdks/node/service/samples** e execute:

  ```
  ./simulate.sh
  ```

Este script produz os comandos de que precisa para executar e iniciar **iotdm\_simple\_sample** para cada dispositivo listado no ficheiro **devicecreds.txt**. Execute os comandos individualmente a partir de uma janela de terminal separada para cada dispositivo simulado. O dispositivo simulado continua a ser executado até fechar a janela de comandos.

A aplicação **iotdm\_simple\_sample** é compilada com a biblioteca de cliente de gestão de dispositivos do IoT Hub do Azure para C, que permite criar dispositivos de IoT que podem ser geridos pelo IoT Hub do Azure. Os criadores de dispositivos podem utilizar esta biblioteca para comunicar as propriedades do dispositivo e implementar as ações executáveis requeridas pelas tarefas do dispositivo. Esta biblioteca é um componente fornecido como parte dos SDKs de open source para IoT Hub do Azure.

Quando executa **simulate.sh**, verá um fluxo de dados na janela de resultados. Esta janela de resultados mostra o tráfego de entrada e de saída, bem como as declarações **printf** nas funções de chamada de retorno específicas da aplicação. Esta janela permite-lhe ver o tráfego de entrada e saída, juntamente com a forma como a aplicação de exemplo está a processar os pacotes descodificados. Quando o dispositivo estabelece ligação ao IoT Hub, o serviço começa automaticamente a observar os recursos no dispositivo. A biblioteca de cliente de DM do IoT Hub invoca as chamadas de retorno do dispositivo para obter os valores mais recentes do dispositivo.

Encontrará abaixo o resultado da aplicação de exemplo **iotdm\_simple\_sample**. Na parte superior, é apresentada uma mensagem **REGISTADO** com êxito, que mostra o dispositivo com o Id **Device11-7ce4a850** a estabelecer ligação ao Hub IoT.

> [AZURE.NOTE]  Para obter um resultado menos descritivo, crie e execute a configuração de retalho.

![][img-output]

Certifique-se de que deixa todos os dispositivos simulados em execução à medida que concluir as seguintes secções.

## Executar a IU de amostra de gestão de dispositivos

Agora que aprovisionou um Hub IoT e tem vários dispositivos simulados em execução e registados para gestão, pode implementar a IU de amostra da gestão de dispositivos. A IU de amostra de gestão de dispositivos fornece-lhe um exemplo de como utilizar as APIs de gestão de dispositivos para criar uma experiência de IU interativa.  Para obter mais informações sobre a IU de amostra de gestão de dispositivos, incluindo [problemas conhecidos](https://github.com/Azure/azure-iot-device-management#knownissues), veja o repositório GitHub [IU da gestão de dispositivos IoT do Azure][lnk-dm-github].

Para obter, criar e executar a IU de amostra de gestão de dispositivos, siga estes passos:

1. Abra uma shell.

2. Confirme que instalou o Node.js 6.1.0 ou superior de acordo com a secção de pré-requisitos, ao escrever `node --version`.

3. Clone o repositório GitHub da IU de gestão de dispositivos IoT do Azure executando o seguinte comando na shell:

    ```
    git clone https://github.com/Azure/azure-iot-device-management.git
    ```
    
4. Na pasta raiz da sua cópia clonada do repositório de IU de gestão de dispositivos do IoT do Azure, execute o seguinte comando para obter os pacotes dependentes:

    ```
    npm install
    ```

5. Quando o comando de instalação de npm for concluído, execute o seguinte comando na shell para criar o código:

    ```
    npm run build
    ```

6. Utilize um editor de texto para abrir o ficheiro user-config.json na raiz da pasta clonada. Substitua o texto "&lt;A SUA CADEIA DE LIGAÇÃO AQUI&gt;" pela cadeia de ligação do Hub IoT da secção anterior e guarde o ficheiro.

7. Na shell, execute o seguinte comando para iniciar a aplicação UX de gestão de dispositivos:

    ```
    npm run start
    ```

8. Quando a linha de comandos indicar "Os serviços foram iniciados", abra um browser e navegue para a aplicação de gestão de dispositivos no seguinte URL para ver os seus dispositivos simulados: <http://127.0.0.1:3003>.

    ![][img-dm-ui]

Deixe os dispositivos simulados e a aplicação de gestão de dispositivos em execução à medida que avança para o próximo tutorial de gestão de dispositivos.


## Passos seguintes

Para continuar a utilizar o Hub IoT, veja [Como utilizar o SDK Gateway][lnk-gateway-SDK].

Para saber mais sobre as funcionalidades de gestão de dispositivos do Hub IoT do Azure, veja o tutorial [Explorar a gestão de dispositivos do Hub IoT do Azure utilizando a IU de amostra][lnk-sample-ui].

<!-- images and links -->
[img-new-hub]: media/iot-hub-device-management-get-started-node/image1.png
[img-configure-hub]: media/iot-hub-device-management-get-started-node/image2.png
[img-monitor]: media/iot-hub-device-management-get-started-node/image3.png
[img-keys]: media/iot-hub-device-management-get-started-node/image4.png
[img-connection]: media/iot-hub-device-management-get-started-node/image5.png
[img-output]: media/iot-hub-device-management-get-started-node/image6.png
[img-dm-ui]: media/iot-hub-device-management-get-started-node/dmui.png

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Portal do Azure]: https://portal.azure.com/
[Utilizar grupos de recursos para gerir os seus recursos do Azure]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-sample-ui]: iot-hub-device-management-ui-sample.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md


<!--HONumber=ago16_HO4-->


