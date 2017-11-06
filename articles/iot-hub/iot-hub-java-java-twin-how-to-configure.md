---
title: Utilize as propriedades do IoT Hub do Azure dispositivo duplo (Java) | Microsoft Docs
description: "Como utilizar dispositivos duplos do IoT Hub do Azure para configurar dispositivos. Utilizar o dispositivo IoT do Azure SDK para Java para implementar uma aplicação de dispositivo simulado e o serviço de IoT do Azure SDK para Java para implementar uma aplicação de serviço que modifica uma configuração de dispositivo utilizando um dispositivo duplo."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: dobett
ms.openlocfilehash: 3881e3791c97baf2922722f01f9e6e6ea55ed2e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>Utilize as propriedades do pretendido para configurar dispositivos

[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

No final deste tutorial, tem duas aplicações de consola Java:

* **simulated-device**, uma aplicação de dispositivo simulado que aguarda uma atualização da configuração pretendida e reporta o estado de um processo de atualização da configuração simulada.
* **configuração do conjunto de**, uma aplicação de back-end, que define a configuração pretendida num dispositivo e consulta o processo de atualização da configuração.

> [!NOTE]
> O artigo [SDKs IoT do Azure] [ lnk-hub-sdks] fornece informações sobre os SDKs IoT do Azure que pode utilizar para criar aplicações de dispositivo e o back-end.
> 
> 

Para concluir este tutorial, precisa do seguinte:

* A versão mais recente de [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

Se seguiu o [começar a utilizar dispositivos duplos] [ lnk-twin-tutorial] tutorial, já tiver um IoT hub e uma identidade de dispositivo chamado **myDeviceId**. Nesse caso, pode avançar para o [criar a aplicação de dispositivo simulado] [ lnk-how-to-configure-createapp] secção.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>Criar a aplicação de dispositivo simulada
Nesta secção, pode cria uma aplicação de consola do Java que liga ao seu hub como **myDeviceId**, tem de aguardar durante uma atualização da configuração pretendida e, em seguida, os relatórios de atualizações no processo de atualização de configuração simulada.

1. Crie uma pasta vazia designada dt-get-started.

1. Na pasta dt-get-started, crie um projeto Maven designado **simulated-device** utilizando o seguinte comando na sua linha de comandos. Tenha em atenção que se trata de um comando único, por extenso:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Na linha de comandos, navegue para a pasta simulated-device.

1. Com um editor de texto, abra o ficheiro pom.xml na pasta simulated-device e adicione a seguinte dependência à **dependências** nós. Esta dependência permite-lhe utilizar o pacote de cliente do serviço iot na sua aplicação para comunicar com o seu IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.30</version>
    </dependency>
    ```

    > [!NOTE]
    > Pode verificar a versão mais recente do **cliente do dispositivo iot** utilizando [Maven pesquisa] [lnk-maven-dispositivo-pesquisa].

1. Adicione o seguinte **criar** nó após a **dependências** nós. Esta configuração dá instruções ao Maven utilizar 1.8 do Java para criar a aplicação:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. Guarde e feche o ficheiro pom.xml.

1. Com um editor de texto, abra o ficheiro de origem simulated-device\src\main\java\com\mycompany\app\app.Java.

1. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. Substitua **{yourdeviceconnectionstring}** com a cadeia de ligação do dispositivo que anotou no *criar uma identidade de dispositivo* secção:

    ```java
    private static String deviceId = "myDeviceId";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    private static TelemetryConfig telemetryConfig;
    ```

1. Para implementar um processador de chamada de retorno para eventos de estado do dispositivo duplo (para fins de depuração), adicione a seguinte classe aninhada para o **aplicação** classe:

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback 
    {
        public void execute(IotHubStatusCode status, Object context) 
        {
            //System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

1. Adicione a seguinte classe aninhada para o **aplicação** classe:

    ```java
    private static class TelemetryConfig extends Device 
    {
        private String configId = "0000";
        private String sendFrequency = "45m";

        private Boolean initialRun = true;
        
        private Property telemetryConfig = new Property("telemetryConfig", "{configId=" + configId + ", sendFrequency=" + sendFrequency + "}");

        public void InitTelemetry() throws IOException 
        {
            System.out.println("Report initial telemetry config:");
            System.out.println(this.telemetryConfig);

            this.setReportedProp(this.telemetryConfig);

            client.sendReportedProperties(this.getReportedProp());
        }

        private void UpdateReportedConfiguration() 
        {
            try {
                System.out.println("Initiating config change");
                    
                this.setReportedProp(this.telemetryConfig);
                client.sendReportedProperties(this.getReportedProp());
                    
                System.out.println("Simulating device reset");
                
                Thread.sleep(10000);
                
                System.out.println("Completing config change");
                System.out.println("Config change complete");
            } catch (Exception e) {
                System.out.println("Exception \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
            }
        }

        @Override
        public void PropertyCall(String propertyKey, Object propertyValue, Object context) 
        {
            if (propertyKey.equals("telemetryConfig")) {
                if (!(initialRun)) {
                    System.out.println("Desired property change:");
                    System.out.println(propertyKey + ":" + propertyValue);

                    telemetryConfig.setValue(propertyValue);

                    UpdateReportedConfiguration();
                } else {
                    initialRun = false;
                }
            } 
        }
    }
    ```

    > [!NOTE]
    > Expande a classe de TelemetryConfig o [classe de dispositivo] [ lnk-java-device-class] para obterem acesso às chamadas de retorno de propriedades pretendido.

1. Modificar a assinatura do **principal** método para emitir as seguintes exceções:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Adicione o seguinte código para o **principal** método ao instanciar um **DeviceClient** e **TelemetryConfig**:

    ```java
    client = new DeviceClient(connString, protocol);

    telemetryConfig = new TelemetryConfig();
    ```

1. Adicione o seguinte código para o **principal** método para iniciar serviços do dispositivo duplo:

    ```java
    try 
    {
        System.out.println("Connecting to hub");
        client.open();
        client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, telemetryConfig, null);

        telemetryConfig.InitTelemetry();

        System.out.println("Wait for desired telemetry...");
        client.subscribeToDesiredProperties(telemetryConfig.getDesiredProp());
    }
    catch (Exception e) 
    {
        System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
        telemetryConfig.clean();
        client.close();
        System.out.println("Shutting down...");
    }
    ```

1. Adicione o seguinte código para o **principal** método para encerrar o simulador de dispositivo quando for necessário:

    ```java
    System.out.println("Press enter to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    telemetryConfig.clean();
    client.close();
    ```

1. Guarde e feche o ficheiro simulated-device\src\main\java\com\mycompany\app\app.Java.

1. Criar o **simulated-device** aplicações back-end e corrigir os eventuais erros. Na sua linha de comandos, navegue para a pasta simulated-device e execute o seguinte comando:

    `mvn clean package -DskipTests`

   > [!NOTE]
   > Este tutorial não simular nenhum comportamento de atualizações de configuração em simultâneo. Alguns processos de atualização de configuração poderão acomodar as alterações de configuração de destino enquanto a atualização está a ser executado, alguns poderão ter de fila-los e algumas rejeitá-las com uma condição de erro. Certifique-se a ter em consideração o comportamento pretendido para o processo de configuração específica e adicione a lógica adequada antes de iniciar a alteração da configuração.
   > 
   > 

## <a name="create-the-service-app"></a>Criar a aplicação de serviço
Nesta secção, pode cria uma aplicação de consola do Java que atualiza o *pretendido propriedades* no dispositivo duplo associado **myDeviceId** com um novo objeto de configuração de telemetria. Em seguida, consulta os dispositivos duplos armazenados no IoT hub e mostra a diferença entre as configurações desejadas e comunicadas do dispositivo.

1. Na pasta dt-get-started, crie um projeto Maven designado **set-configuração** utilizando o seguinte comando na sua linha de comandos. Tenha em atenção que se trata de um comando único, por extenso:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=set-configuration -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Na sua linha de comandos, navegue para a pasta de configuração do conjunto.

1. Com um editor de texto, abra o ficheiro pom.xml na pasta de reinício do acionador e adicione a seguinte dependência à **dependências** nós. Esta dependência permite-lhe utilizar o pacote de cliente do serviço iot na sua aplicação para comunicar com o seu IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Pode verificar a versão mais recente do **cliente do serviço iot** utilizando [Maven pesquisa] [lnk-maven-service-pesquisa].

1. Adicione o seguinte **criar** nó após a **dependências** nós. Esta configuração dá instruções ao Maven utilizar 1.8 do Java para criar a aplicação:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. Guarde e feche o ficheiro pom.xml.

1. Com um editor de texto, abra o ficheiro de origem set-configuration\src\main\java\com\mycompany\app\App.java.

1. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

1. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. Substitua **{youriothubconnectionstring}** com a cadeia de ligação do IoT hub que anotou no *criar um IoT Hub* secção:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";
    ```

1. Para consultar e atualizar dispositivos duplos no dispositivo simulado, adicione o seguinte código para o **principal** método:

    ```java
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);

    String sendFrequency= "12h";

    try {
        twinClient.getTwin(device);

        // make sure a differing value exists before calling the updateTwin() method, else a null device exception will be thrown
        // this allows the program to be run multiple times for demonstration purposes
        String desiredProperties = device.desiredPropertiesToString();
        if (desiredProperties.contains("sendFrequency=" + sendFrequency))
        {
            sendFrequency = "8h";
        }
            
        Set<Pair> tags = new HashSet<Pair>();
        tags.add(new Pair("telemetryConfig", "{configId=0001, sendFrequency=" + sendFrequency + "}"));

        twinClient.getTwin(device);
        device.setDesiredProperties(tags);

        System.out.println("Config report for: " + deviceId);   
        System.out.println(device);

        twinClient.updateTwin(device);

        String reportedProperties = device.reportedPropertiesToString();
        Boolean waitFlag = true;

        while (waitFlag) {
            if (!reportedProperties.contains("sendFrequency=" + sendFrequency)) {
                Thread.sleep(10000);
            }
            else 
            {
                waitFlag = false;
            }

            twinClient.getTwin(device);
            reportedProperties = device.reportedPropertiesToString();
        }
            
        SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "properties.reported.telemetryConfig='{configId=0001, sendFrequency=" + sendFrequency + "}'", null);

        Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
        while (twinClient.hasNextDeviceTwin(twinQuery)) {
            DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
            System.out.println(d.getDeviceId() + " found with changed telemetryConfig");
        }

        System.out.println("Config report for: " + deviceId);
        twinClient.getTwin(device);
        System.out.println(device);

        } catch (IotHubException e) {
            System.out.println(e.getMessage());
        } catch (IOException e) {
            System.out.println(e.getMessage());
        } catch (InterruptedException e) {
            System.out.println(e.getMessage());
        }
    ```

1. Guarde e feche o ficheiro set-desired-configuration\src\main\java\com\mycompany\app\App.java.

1. Criar o **set-configuração** aplicações back-end e corrigir os eventuais erros. Na sua linha de comandos, navegue para a pasta de configuração do conjunto e execute o seguinte comando:

    `mvn clean package -DskipTests`
   
    Este código obtém o dispositivo duplo para **myDeviceId**e, em seguida, atualiza as respetivas propriedades pretendidas com um novo objeto de configuração de telemetria.
    Depois disso, este consulta os dispositivos duplos armazenados no IoT hub a cada 10 segundos e imprime as configurações de telemetria pretendido e comunicados. Consulte o [idioma de consulta do IoT Hub] [ lnk-query] para saber como gerar relatórios avançados em todos os seus dispositivos.
   
   > [!IMPORTANT]
   > Esta aplicação consulta IoT Hub cada 10 segundos para fins de ilustrativa até que o dispositivo tiver sido atualizado. Utilize consultas para gerar relatórios de destinada ao utilizador entre vários dispositivos e não para detetar as alterações. Se a sua solução requer notificações em tempo real de eventos do dispositivo, utilize [duplo notificações][lnk-twin-notifications].
   > 

   > [!IMPORTANT]
   > Não há um atraso de até um minuto entre a operação de relatório do dispositivo e o resultado da consulta. Isto é permitir que a infraestrutura de consulta para trabalhar em grande escala.  Para obter vistas consistentes de utilização de duplo um único dispositivo de **getDeviceTwin** método o **DeviceTwin** classe.
   > 
   > 

## <a name="run-the-apps"></a>Executar as aplicações

Já está pronto para executar as aplicações.

1. Numa linha de comandos da pasta simulated-device, execute o seguinte comando para começar a escutar dispositivo duplo chamadas do seu IoT hub:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. Numa linha de comandos na pasta de configuração do conjunto, execute o seguinte comando para consultar e atualizar os dispositivos duplos no seu dispositivo simulado do seu IoT hub:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. O dispositivo simulado responde a chamada de método direta de reinício:

    ![Aplicação de dispositivo simulada Java IoT Hub responde para a chamada do dispositivo duplo][img-deviceconfigured]

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, configurou uma configuração desejada como *pretendido propriedades* da solução de back-end e escreveu uma aplicação de dispositivo para detetar essa alteração e simular um processo de atualização de vários passos comunicar o seu estado através as propriedades que relatados.

Utilize os seguintes recursos para saber como:

* Enviar telemetria a partir de dispositivos com o [introdução ao IoT Hub] [ lnk-iothub-getstarted] tutorial,
* agendar ou efetuar operações em grandes conjuntos de dispositivos Consulte o [agenda e as tarefas de difusão] [ lnk-schedule-jobs] tutorial.
* controlar dispositivos interativamente (como ativar uma ventoinha a partir de uma aplicação controlados pelo utilizador), com o [utilizar métodos diretos] [ lnk-methods-tutorial] tutorial.

<!-- images -->
[img-deviceconfigured]: media/iot-hub-java-java-twin-how-to-configure/deviceconfigured.png


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-java-device-class]: https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.sdk.iot.device._device_twin._device

[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-twin-tutorial]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-how-to-configure-createapp]: iot-hub-csharp-csharp-twin-how-to-configure.md#create-the-simulated-device-app
