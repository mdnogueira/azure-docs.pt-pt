---
title: "Criar um módulo de limite de IoT do Azure com o Java | Microsoft Docs"
description: "Este tutorial showcases como escrever um módulo de conversor var dados utilizando os pacotes mais recentes do Azure IoT Edge Maven."
services: iot-hub
author: junyi
manager: timlt
ms.service: iot-hub
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 06/28/2017
ms.author: junyi
ms.openlocfilehash: eddeb5cc13aac7ab33305adcd266465a5b143462
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-iot-edge-module-with-java"></a>Criar um módulo de limite de IoT do Azure com Java

Este tutorial showcases como um poderá criar um módulo de limite de IoT do Azure em Java.

Neste tutorial, iremos irá guiá-configuração do ambiente e como escrever um [var](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) módulo de conversor de dados utilizando os pacotes mais recentes do Azure IoT Edge Maven.

## <a name="prerequisites"></a>Pré-requisitos

Nesta secção, irá configurar o ambiente para o desenvolvimento de módulo de limite de IoT. Aplica-se ambos *Windows de 64 bits* e *64-bit Linux (8 Ubuntu/Debian)* sistemas operativos.

É necessário o seguinte software:

* [Cliente de Git](https://git-scm.com/downloads).
* [**x64** JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* [Maven](https://maven.apache.org/install.html).

Abra uma janela de terminal da linha de comandos e clonar o repositório seguinte:

1. `git clone https://github.com/Azure-Samples/iot-edge-samples.git`.
2. `cd iot-edge-samples/java/simulated_ble`

## <a name="overall-architecture"></a>Arquitetura geral

A plataforma do Azure IoT Edge adopts descontos elevados a [arquitetura Von Neumann](https://en.wikipedia.org/wiki/Von_Neumann_architecture). O que significa que a arquitetura de limite de IoT do Azure completa é um sistema que processa a entrada e produz saída; e cada módulo individuais também é um subsistema de entrada-saída muito pequena. Neste tutorial, iremos introduz os seguintes dois módulos:

1. Um módulo que recebe um simulada [var](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) assinalar e converte-o num formatado [JSON](https://en.wikipedia.org/wiki/JSON) mensagem.
2. Um módulo que imprima o recebida [JSON](https://en.wikipedia.org/wiki/JSON) mensagem.

A imagem seguinte mostra o fluxo de dados ponto-a-ponto típico para este projeto:

![Fluxo de dados entre três módulos](media/iot-hub-iot-edge-create-module/dataflow.png "entrada: Simulated var módulo; Processador: Módulo de conversor; Saída: Módulo de impressora")

## <a name="understanding-the-code"></a>Noções sobre o código

### <a name="maven-project-structure"></a>Estrutura de projeto maven

Uma vez que os pacotes de limite do Azure IoT são baseadas em Maven, temos de criar uma estrutura de projeto Maven típica, que contém um `pom.xml` ficheiro.

O POM herda o `com.microsoft.azure.gateway.gateway-module-base` pacote, o qual declara todas as dependências necessárias para um projeto de módulo que inclui os binários de tempo de execução, o caminho de ficheiro de configuração do gateway e o comportamento de execução. Isto guarda-na muitas de tempo e eliminar a necessidade de escrever e reescrever repetidas centenas de linhas de código.

É necessário atualizar o ficheiro pom.xml por declarar o necessário dependências/plug-ins e o nome do ficheiro de configuração a ser utilizada pela nossa módulo, conforme mostrado no seguinte fragmento de código.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <!-- Inherit from parent -->
  <parent>
    <groupId>com.microsoft.azure.gateway</groupId>
    <artifactId>gateway-module-base</artifactId>
    <version>1.0.1</version>
  </parent>
  
  <groupId>com.microsoft.azure.gateway</groupId>
  <artifactId>ble-converter</artifactId>
  <version>1.0</version>
  <packaging>jar</packaging>

  <!-- Set the filename of the Azure IoT Edge configuration located
       under ./src/main/resources/gateway/ which is used in parent -->
  <properties>
    <gw.config.fileName>gw-config.json</gw.config.fileName>
  </properties>

  <!-- Re-declare dependencies used in parent -->
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.gateway</groupId>
      <artifactId>gateway-java-binding</artifactId>
    </dependency>
    <dependency>
      <groupId>${dependency.runtime.group}</groupId>
      <artifactId>${dependency.runtime.name}</artifactId>
    </dependency>
  </dependencies>

  <!-- Re-declare plugins used in parent -->
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-dependency-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-resources-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```

### <a name="basic-understanding-of-an-azure-iot-edge-module"></a>Compreensão básica de um módulo de limite de IoT do Azure

Pode tratar um módulo do Azure IoT Edge como um processador de dados cujo trabalho consiste em: receber entrada, processá-la e produzir resultados.

A entrada pode ser dados do hardware (por exemplo, um movimento detector), uma mensagem de outros módulos ou qualquer outro (como um número aleatório gerado periodicamente por um temporizador).

O resultado será semelhante para a entrada, poderia acionar comportamento de hardware (como o LED intermitente), uma mensagem para outros módulos ou qualquer outro (como a impressão para a consola).

Módulos comunicam entre si utilizando `com.microsoft.azure.gateway.messaging.Message` classe. O **conteúdo** de um `Message` é uma matriz de bytes que seja capaz de que representa qualquer tipo de dados que quiser. **Propriedades** também estão disponíveis no `Message` e são simplesmente um mapeamento de cadeia-cadeia. Poderá achar de **propriedades** como cabeçalhos de um pedido HTTPS ou os metadados de um ficheiro.

Para desenvolver um módulo do Azure IoT Edge em Java, terá de criar uma nova classe de módulo que herda de `com.microsoft.azure.gateway.core.GatewayModule` e implementar os métodos abstractos necessários `receive()` e `destroy()`. Neste momento, pode também optar por implementar o opcional `start()` ou `create()` métodos bem. O fragmento de código seguinte mostra como começar a criação de um módulo de limite de IoT do Azure.

```java
import com.microsoft.azure.gateway.core.Broker;
import com.microsoft.azure.gateway.core.GatewayModule;
import com.microsoft.azure.gateway.messaging.Message;

public class MyEdgeModule extends GatewayModule {
  public MyEdgeModule(long address, Broker broker, String configuration) {
    /* Let the GatewayModule do the dirty work of initialization. It's also
       a good time to parse your own configuration defined in Azure IoT Edge
       configuration file (typically ./src/main/resources/gateway/gw-config.json) */
    super(address, broker, configuration);
  }

  @Override
  public void start() {
    /* Acquire the resources you need. If you don't
       need any resources, you may omit this method. */
  }

  @Override
  public void destroy() {
    /* It's time to release all resources. This method is required. */
  }

  @Override
  public void receive(Message message) {
    /* Logic to process the input message. This method is required. */
    // ...
    /* Use publish() method to do the output. You are
       allowed to publish your new Message instance. */
    this.publish(message);
  }
}
```

### <a name="converter-module"></a>Módulo de conversor

| Input                    | Processador                              | Saída                 | Ficheiro de origem            |
| ------------------------ | -------------------------------------- | ---------------------- | ---------------------- |
| Mensagens de dados de temperatura | Analisar e construir uma nova mensagem JSON | Mensagem JSON de estrutura | `ConverterModule.java` |

Este módulo é um módulo de limite do Azure IoT típico. Aceita mensagens de temperatura de outros módulos (um módulo de hardware, ou deste caso nosso módulo var simulado); e, em seguida, normaliza a mensagem de temperatura na uma mensagem JSON estruturada (incluindo acrescentar o ID de mensagem, a definição da propriedade dos temos acionar o alerta de temperatura e assim sucessivamente).

```java
@Override
public void receive(Message message) {
  try {
    JSONObject messageFromBle = new JSONObject(new String(message.getContent()));
    double temperature = messageFromBle.getDouble("temperature");
    Map<String, String> inputProperties = message.getProperties();

    HashMap<String, String> properties = new HashMap<>();
    properties.put("source", inputProperties.get("source"));
    properties.put("macAddress", inputProperties.get("macAddress"));
    properties.put("temperatureAlert", temperature > 30 ? "true" : "false");

    String content = String.format(
        "{ \"deviceId\": \"Intel NUC Gateway\", \"messageId\": %d, \"temperature\": %f }",
        ++this.messageCount, temperature);

    this.publish(new Message(content.getBytes(), properties));
  } catch (Exception ex) {
    ex.printStackTrace();
  }
}
```

### <a name="printer-module"></a>Módulo de impressora

| Input                          | Processador | Saída                     | Ficheiro de origem          |
| ------------------------------ | --------- | -------------------------- | -------------------- |
| Qualquer mensagem de outros módulos | N/D       | A mensagem de registo para a consola | `PrinterModule.java` |

Este é um módulo de simple, facilmente compreensíveis, que produz as mensagens recebidas para a janela de terminal.

```java
@Override
public void receive(Message message) {
  System.out.println(message.toString());
}
```

### <a name="azure-iot-edge-configuration"></a>Configuração do limite do IoT do Azure

É o último passo antes de executar os módulos para configurar o limite de IoT do Azure e para estabelecer as ligações entre módulos.

Primeiro é necessário declarar o nosso carregador de Java (desde o limite de IoT do Azure suporta carregadores de idiomas diferentes) que foi referenciada pela respetiva `name` nas secções mais tarde.

```json
"loaders": [{
  "type": "java",
  "name": "java",
  "configuration": {
    "jvm.options": {
      "library.path": "./"
    }
  }
}]
```

Depois de ter declarámos nosso carregadores, será também temos de declarar as nossos módulos bem. Semelhante para declarar os carregadores, estes também podem ser referenciados pelo respetivo `name` atributo. Quando declarar um módulo, é necessário especificar o carregador deve utilizar (o que deve ser o que definimos antes) e o-ponto de entrada (deve ser o nome de classe normalizado do nosso módulo) para cada módulo. O `simulated_device` módulo é um módulo nativo, que está incluído no pacote de tempo de execução de núcleos de limite de IoT do Azure. Deve incluir sempre `args` no JSON de ficheiros, mesmo que esteja `null`.

```json
"modules": [
  {
    "name": "simulated_device",
    "loader": {
      "name": "native",
      "entrypoint": {
        "module.path": "simulated_device"
      }
    },
    "args": {
      "macAddress": "01:02:03:03:02:01",
      "messagePeriod": 500
    }
  },
  {
    "name": "converter",
    "loader": {
      "name": "java",
      "entrypoint": {
        "class.name": "com/microsoft/azure/gateway/ConverterModule",
        "class.path": "./ble-converter-1.0-with-deps.jar"
      }
    },
    "args": null
  },
  {
    "name": "print",
    "loader": {
      "name": "java",
      "entrypoint": {
        "class.name": "com/microsoft/azure/gateway/PrinterModule",
        "class.path": "./ble-converter-1.0-with-deps.jar"
      }
    },
    "args": null
  }
]
```

No final da configuração, vamos estabelecer as ligações. Cada ligação é expresso por `source` e `sink`. Estes deverá ambos referenciar um módulo predefinido. A mensagem de saída de `source` módulo será reencaminhado para a entrada de `sink` módulo.

```json
"links": [
  {
    "source": "simulated_device",
    "sink": "converter"
  },
  {
    "source": "converter",
    "sink": "print"
  }
]
```

## <a name="running-the-modules"></a>Executar os módulos

Utilize `mvn package` tudo para criar o `target/` pasta. `mvn clean package`Também é recomendada para uma compilação limpa.

Utilize `mvn exec:exec` executar o limite de IoT do Azure e deve observar a que os dados de temperatura e todas as propriedades são indicadas na consola uma taxa fixo.

Se pretende terminar a aplicação, prima `<Enter>` chave.

> [!IMPORTANT]
> Não se recomenda utilizar Ctrl + C para terminar a aplicação de gateway do limite de IoT. Como pode fazer com que o processo esteja terminado.

