---
title: "Introdução ao duplos de dispositivos do IoT Hub do Azure (Java) | Microsoft Docs"
description: "Como utilizar dispositivos duplos do IoT Hub do Azure para adicionar etiquetas e, em seguida, utilizar uma consulta do IoT Hub. Utilizar o dispositivo IoT do Azure SDK para Java para implementar a aplicação de dispositivo e o serviço de IoT do Azure SDK para Java para implementar uma aplicação de serviço que adiciona as etiquetas e executa a consulta do IoT Hub."
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
ms.date: 07/04/2017
ms.author: dobett
ms.openlocfilehash: 6d306d4742a53789d8e69c80d7fbdfc4e1ade4bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-device-twins-java"></a>Começar a utilizar dispositivos duplos (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Neste tutorial, crie duas aplicações de consola Java:

* **Adicionar-etiquetas-consulta**, uma aplicação de back-end do Java que adiciona as etiquetas e a consulta dispositivos duplos.
* **simulated-device**, uma aplicação de dispositivo do Java que liga ao seu IoT hub e reporta a conectividade através de uma propriedade que relatados de condição.

> [!NOTE]
> O artigo [SDKs IoT do Azure](iot-hub-devguide-sdks.md) fornece informações sobre os SDKs IoT do Azure que pode utilizar para criar aplicações de dispositivo e o back-end.

Para concluir este tutorial, precisa de:

* A versão mais recente de [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar um [conta gratuita](http://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

Se preferir para criar a identidade do dispositivo através de programação, leia a secção correspondente do [ligar o seu dispositivo ao seu IoT hub utilizando Java](iot-hub-java-java-getstarted.md#create-a-device-identity) artigo.

## <a name="create-the-service-app"></a>Criar a aplicação de serviço

Nesta secção, pode cria uma aplicação de Java que adiciona os metadados de localização como uma etiqueta para o dispositivo duplo no IoT Hub associados com **myDeviceId**. A aplicação primeiro consulta o IoT hub para dispositivos localizados nos E.U.A. e, em seguida, para dispositivos que reportam uma ligação de rede celular.

1. No computador de desenvolvimento, crie uma pasta vazia designada `iot-java-twin-getstarted`.

1. No `iot-java-twin-getstarted` pasta, crie um projeto Maven designado **adicionar-etiquetas-consulta** utilizando o seguinte comando na sua linha de comandos. Tenha em atenção que se trata de um comando único, por extenso:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Na sua linha de comandos, navegue para o `add-tags-query` pasta.

1. Com um editor de texto, abra o `pom.xml` ficheiros o `add-tags-query` pasta e adicione a seguinte dependência à **dependências** nós. Esta dependência permite-lhe utilizar o **cliente do serviço iot** pacote na sua aplicação para comunicar com o seu IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Pode verificar a versão mais recente do **cliente do serviço iot** utilizando [pesquisa Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

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

1. Guarde e feche o `pom.xml` ficheiro.

1. Com um editor de texto, abra o `add-tags-query\src\main\java\com\mycompany\app\App.java` ficheiro.

1. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

1. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. Substitua `{youriothubconnectionstring}` com a cadeia de ligação do IoT hub que anotou no *criar um IoT Hub* secção:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

1. Atualização do **principal** assinatura de método para incluir o seguinte `throws` cláusula:

    ```java
    public static void main( String[] args ) throws IOException
    ```

1. Adicione o seguinte código para o **principal** método para criar o **DeviceTwin** e **DeviceTwinDevice** objetos. O **DeviceTwin** objeto processa a comunicação com o seu IoT hub. O **DeviceTwinDevice** objeto representa o dispositivo duplo com as respetivas etiquetas e propriedades:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

1. Adicione o seguinte `try/catch` bloquear para o **principal** método:

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

1. Para atualizar o **região** e **maquinaria** etiquetas de duplo de dispositivo no seu dispositivo duplo, adicione o seguinte código no `try` blocos:

    ```java
    // Get the device twin from IoT Hub
    System.out.println("Device twin before update:");
    twinClient.getTwin(device);
    System.out.println(device);

    // Update device twin tags if they are different
    // from the existing values
    String currentTags = device.tagsToString();
    if ((!currentTags.contains("region=" + region) && !currentTags.contains("plant=" + plant))) {
      // Create the tags and attach them to the DeviceTwinDevice object
      Set<Pair> tags = new HashSet<Pair>();
      tags.add(new Pair("region", region));
      tags.add(new Pair("plant", plant));
      device.setTags(tags);

      // Update the device twin in IoT Hub
      System.out.println("Updating device twin");
      twinClient.updateTwin(device);
    }

    // Retrieve the device twin with the tag values from IoT Hub
    System.out.println("Device twin after update:");
    twinClient.getTwin(device);
    System.out.println(device);
    ```

1. Para consultar os dispositivos duplos no IoT hub, adicione o seguinte código para o `try` bloco depois do código adicionado no passo anterior. O código seja executado duas consultas. Cada consulta devolve um máximo de 100 dispositivos:

    ```java
    // Query the device twins in IoT Hub
    System.out.println("Devices in Redmond:");

    // Construct the query
    SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43'", null);

    // Run the query, returning a maximum of 100 devices
    Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }

    System.out.println("Devices in Redmond using a cellular network:");

    // Construct the query
    sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43' AND properties.reported.connectivityType = 'cellular'", null);

    // Run the query, returning a maximum of 100 devices
    twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 3);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }
    ```

1. Guarde e feche o `add-tags-query\src\main\java\com\mycompany\app\App.java` ficheiro

1. Criar o **adicionar-etiquetas-consulta** aplicação e corrigir os eventuais erros. Na sua linha de comandos, navegue para o `add-tags-query` pasta e execute o seguinte comando:

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>Criar uma aplicação de dispositivo

Nesta secção, crie uma aplicação de consola do Java que define um valor de propriedade comunicados que é enviado ao IoT Hub.

1. No `iot-java-twin-getstarted` pasta, crie um projeto Maven designado **simulated-device** utilizando o seguinte comando na sua linha de comandos. Tenha em atenção que se trata de um comando único, por extenso:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Na sua linha de comandos, navegue para o `simulated-device` pasta.

1. Com um editor de texto, abra o `pom.xml` ficheiros o `simulated-device` pasta e adicione as seguintes dependências para o **dependências** nó. Esta dependência permite-lhe utilizar o **cliente do dispositivo iot** pacote na sua aplicação para comunicar com o seu IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Pode verificar a versão mais recente do **cliente do dispositivo iot** utilizando [pesquisa Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

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

1. Guarde e feche o `pom.xml` ficheiro.

1. Com um editor de texto, abra o `simulated-device\src\main\java\com\mycompany\app\App.java` ficheiro.

1. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. Substituir `{youriothubname}` com o nome do seu IoT hub e `{yourdevicekey}` com a chave de dispositivo valor que gerou no *criar uma identidade de dispositivo* secção:

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    Esta aplicação de exemplo utiliza a variável de **protocolo** para instanciar um objeto **DeviceClient**. 

1. Adicione o seguinte código para o **principal** método para:
    * Crie um cliente de dispositivo para comunicar com IoT Hub.
    * Criar um **dispositivo** objeto para armazenar as propriedades do dispositivo duplo.

    ```java
    DeviceClient client = new DeviceClient(connString, protocol);

    // Create a Device object to store the device twin properties
    Device dataCollector = new Device() {
      // Print details when a property value changes
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context) {
        System.out.println(propertyKey + " changed to " + propertyValue);
      }
    };
    ```

1. Adicione o seguinte código para o **principal** método para criar um **connectivityType** comunicadas propriedade e enviá-lo ao IoT Hub:

    ```java
    try {
      // Open the DeviceClient and start the device twin services.
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);

      // Create a reported property and send it to your IoT hub.
      dataCollector.setReportedProp(new Property("connectivityType", "cellular"));
      client.sendReportedProperties(dataCollector.getReportedProp());
    }
    catch (Exception e) {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Adicione o seguinte código ao fim do **principal** método. A aguardar o **Enter** chave permite tempo para o IoT Hub comunicar o estado das operações dispositivo duplo:

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

1. Guarde e feche o `simulated-device\src\main\java\com\mycompany\app\App.java` ficheiro.

1. Criar o **simulated-device** aplicação e corrigir os eventuais erros. Na sua linha de comandos, navegue para o `simulated-device` pasta e execute o seguinte comando:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Executar as aplicações

Agora está pronto para executar as aplicações de consola.

1. Numa linha de comandos do `add-tags-query` pasta, execute o seguinte comando para executar o **adicionar-etiquetas-consulta** do app service:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Serviço de aplicações Java IoT Hub para atualizar os valores de etiqueta e executar consultas de dispositivo](media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    Pode ver o **maquinaria** e **região** etiquetas adicionadas para o dispositivo duplo. A primeira consulta devolve o seu dispositivo, mas não o segundo.

1. Numa linha de comandos do `simulated-device` pasta, execute o seguinte comando para adicionar o **connectivityType** comunicadas propriedade para o dispositivo duplo:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![O cliente do dispositivo adiciona o * * connectivityType * * comunicadas propriedade](media/iot-hub-java-java-twin-getstarted/device-app-1.png)

1. Numa linha de comandos do `add-tags-query` pasta, execute o seguinte comando para executar o **adicionar-etiquetas-consulta** uma segunda vez do app service:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Serviço de aplicações Java IoT Hub para atualizar os valores de etiqueta e executar consultas de dispositivo](media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Agora o seu dispositivo enviou o **connectivityType** propriedade ao IoT Hub, a segundo consulta devolve o seu dispositivo.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Adicionar metadados do dispositivo como etiquetas a partir de uma aplicação de back-end e escreveu uma aplicação de dispositivo para informações de conectividade do dispositivo de relatório no dispositivo duplo. Também aprendeu como consultar as informações do dispositivo duplo utilizando a linguagem de consulta do SQL Server como o IoT Hub.

Utilize os seguintes recursos para saber como:

* Enviar telemetria a partir de dispositivos com o [introdução ao IoT Hub](iot-hub-java-java-getstarted.md) tutorial.
* Controlar dispositivos interativamente (como ativar uma ventoinha a partir de uma aplicação controlados pelo utilizador) com o [utilizar métodos diretos](iot-hub-java-java-direct-methods.md) tutorial.

<!-- Images. -->
[7]: ./media/iot-hub-java-java-twin-getstarted/invoke-method.png
[8]: ./media/iot-hub-java-java-twin-getstarted/device-listen.png
[9]: ./media/iot-hub-java-java-twin-getstarted/device-respond.png

<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
