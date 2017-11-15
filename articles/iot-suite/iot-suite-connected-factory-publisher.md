---
title: "Utilizar o fabricante do Azure IoT Suite ligado fábrica OPC | Microsoft Docs"
description: "Como criar e implementar a implementação de referência do publicador OPC fábrica ligado."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 934f4deb3139c136c871ab0125ba45267e1d1b05
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="opc-publisher-for-azure-iot-edge"></a>OPC publicador para o limite de IoT do Azure

Este artigo descreve como utilizar a implementação de referência OPC publicador. A implementação de referência demonstra como utilizar o Azure IoT periferia para:

- Ligar a servidores de OPC UA existentes.
- Publicar dados de telemetria JSON codificado destes servidores no OPC UA *Pub/Sub* formatar, utilizando um payload JSON, ao IoT Hub do Azure. Pode utilizar qualquer um dos protocolos de transporte que suporte o limite de IoT do Azure.

Esta aplicação de referência inclui:

- Um UA OPC *cliente* para se ligar aos servidores de OPC UA existentes na sua rede.
- Um UA OPC *servidor* escutar na porta 62222 que pode utilizar para gerir o que é publicado.

A aplicação é implementada utilizando o .NET Core e pode ser executado nas plataformas suportadas pelo .NET Core.

O publicador implementa lógica de repetição quando estabelecer ligações a pontos finais. O publicador espera pontos finais para responder dentro de um número especificado de pedidos de keep alive. Esta lógica de repetição permite que o publicador para detetar alterações nas condições como uma falha de energia para um servidor de OPC UA.

Para cada intervalo de publicação distinto para um servidor de OPC UA, cria uma subscrição separada durante o qual são atualizados a todos os nós com este intervalo de publicação.

Para reduzir a carga de rede, o publicador suporta a criação de batches de dados enviados ao IoT Hub. Um batch é enviado ao IoT Hub apenas quando é atingido o tamanho do pacote configurada.

Esta aplicação utiliza a pilha de referência do fundações OPC OPC UA e, por conseguinte, aplicam restrições de licenciamento. Visite http://opcfoundation.github.io/UA-.NETStandardLibrary/ para documentação de OPC UA e os termos de licenciamento.

Pode encontrar o código de origem no publicador OPC o [OPC publicador para o Azure IoT Edge](https://github.com/Azure/iot-edge-opc-publisher) repositório do GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Para criar a aplicação, terá do [1.1 do .NET Core SDK.](https://docs.microsoft.com/dotnet/core/sdk) para o seu sistema operativo.

## <a name="build-the-application"></a>Criar a aplicação

### <a name="as-native-windows-application"></a>Como aplicação nativa do Windows

Abra o `OpcPublisher.sln` projeto com o Visual Studio 2017 e compile a solução ao atingir F7.

### <a name="as-docker-container"></a>Como contentor de Docker

Para criar a aplicação como um contentor de Docker do Windows, utilize o `Dockerfile.Windows` ficheiro de configuração.

Para criar a aplicação como um contentor de Linux Docker, utilize o `Dockerfile` ficheiro de configuração.

A partir da raiz do repositório no computador de desenvolvimento, escreva o seguinte comando numa janela de consola:

`docker build -f <docker-configfile-to-use> -t <your-container-name> .`

O `-f` opção para `docker build` é opcional e é a predefinição para utilizar o `Dockerfile` ficheiro de configuração.

Docker também lhe permite criar diretamente a partir de um repositório de git. Pode criar um contentor de Linux Docker com o seguinte comando:

`docker build -t <your-container-name> .https://github.com/Azure/iot-edge-opc-publisher`

## <a name="configure-the-opc-ua-nodes-to-publish"></a>Configurar os nós de OPC UA para publicar

Para configurar os nos quais OPC UA devem ter os respetivos valores publicado para o IoT Hub do Azure, crie um ficheiro de configuração com formato JSON. O nome predefinido para este ficheiro de configuração é `publishednodes.json`. A aplicação de atualizações e guarda este ficheiro de configuração quando utiliza o UA OPC métodos de servidor **PublishNode** ou **UnpublishNode**.

A sintaxe do ficheiro de configuração é o seguinte:

```json
[
    {
        // example for an EnpointUrl is: opc.tcp://win10iot:51210/UA/SampleServer
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "OpcNodes": [
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised publishing interval).
            {
                // The identifier specifies the NamespaceUri and the node identifier in XML notation as specified in Part 6 of the OPC UA specification in the XML Mapping section.
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258"
            },
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with an OPC publishing interval of 4 seconds.
            // Publisher will use for each dinstinct publishing interval (of nodes on the same EndpointUrl) a separate subscription. All nodes without a publishing interval setting,
            // will be on the same subscription and the OPC UA stack will publish with the lowest sampling interval of a node.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                "OpcPublishingInterval": 4000
            },
            // Publisher will request the server at EndpointUrl to sample the node with the given sampling interval of 1 second
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised interval).
            // If the OPC publishing interval is set to a lower value, Publisher will adjust the OPC publishing interval of the subscription to the OPC sampling interval value.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                // the OPC sampling interval to use for this node.
                "OpcSamplingInterval": 1000
            }
        ]
    },

    // the format below is only supported for backward compatibility. you need to ensure that the
    // OPC UA server on the configured EndpointUrl has the namespaceindex you expect with your configuration.
    // please use the ExpandedNodeId syntax instead.
    {
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "NodeId": {
            "Identifier": "ns=0;i=2258"
        }
    }
    // please consult the OPC UA specification for details on how OPC monitored node sampling interval and OPC subscription publishing interval settings are handled by the OPC UA stack.
    // the publishing interval of the data to Azure IoT Hub is controlled by the command line settings (or the default: publish data to IoT Hub at least each 1 second).
]
```

## <a name="run-the-application"></a>Executar a aplicação

### <a name="command-line-options"></a>Opções da linha de comandos

Para ver a utilização da aplicação concluída, utilize o `--help` opção da linha de comandos. O exemplo seguinte mostra a estrutura de um comando:

```cmd/sh
OpcPublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]
```

`applicationname`é o nome de aplicação de OPC UA a utilizar. Este parâmetro é necessário. O nome da aplicação também é utilizado para registar o publicador no registo de dispositivos do IoT Hub.

`IoT Hubconnectionstring`é a cadeia de ligação do IoT Hub proprietário. Este parâmetro é opcional.

São suportadas as seguintes opções:

```cmd/sh
--pf, --publishfile=VALUE
  the filename to configure the nodes to publish.
    Default: './publishednodes.json'
--sd, --shopfloordomain=VALUE
  the domain of the shopfloor. if specified this
    domain is appended (delimited by a ':' to the '
    ApplicationURI' property when telemetry is
    sent to IoT Hub.
    The value must follow the syntactical rules of a
    DNS hostname.
    Default: not set
--sw, --sessionconnectwait=VALUE
  specify the wait time in seconds publisher is
    trying to connect to disconnected endpoints and
    starts monitoring unmonitored items
    Min: 10
    Default: 10
--vc, --verboseconsole=VALUE
  the output of publisher is shown on the console.
    Default: False
--ih, --IoT Hubprotocol=VALUE
  the protocol to use for communication with Azure
    IoT Hub (allowed values: Amqp, Http1, Amqp_
    WebSocket_Only, Amqp_Tcp_Only, Mqtt, Mqtt_
    WebSocket_Only, Mqtt_Tcp_Only).
    Default: Mqtt
--ms, --IoT Hubmessagesize=VALUE
  the max size of a message which can be send to
    IoT Hub. when telemetry of this size is available
    it will be sent.
    0 will enforce immediate send when telemetry is
    available
    Min: 0
    Max: 256 * 1024
    Default: 4096
--si, --IoT Hubsendinterval=VALUE
  the interval in seconds when telemetry should be
    send to IoT Hub. If 0, then only the
    IoT Hubmessagesize parameter controls when
    telemetry is sent.
    Default: '1'
--lf, --logfile=VALUE
  the filename of the logfile to use.
    Default: './Logs/<applicationname>.log.txt'
--pn, --portnum=VALUE
  the server port of the publisher OPC server
    endpoint.
    Default: 62222
--pa, --path=VALUE
  the endpoint URL path part of the publisher OPC
    server endpoint.
    Default: '/UA/Publisher'
--lr, --ldsreginterval=VALUE
  the LDS(-ME) registration interval in ms. If 0,
    then the registration is disabled.
    Default: 0
--ot, --operationtimeout=VALUE
  the operation timeout of the publisher OPC UA
    client in ms.
    Default: 120000
--oi, --opcsamplinginterval=VALUE
  the publisher is using this as default value in
    milliseconds to request the servers to sample
    the nodes with this interval
    this value might be revised by the OPC UA
    servers to a supported sampling interval.
    please check the OPC UA specification for
    details how this is handled by the OPC UA stack.
    a negative value will set the sampling interval
    to the publishing interval of the subscription
    this node is on.
    0 will configure the OPC UA server to sample in
    the highest possible resolution and should be
    taken with care.
    Default: 1000
--op, --opcpublishinginterval=VALUE
  the publisher is using this as default value in
    milliseconds for the publishing interval setting
    of the subscriptions established to the OPC UA
    servers.
    please check the OPC UA specification for
    details how this is handled by the OPC UA stack.
    a value less than or equal zero will let the
    server revise the publishing interval.
    Default: 0
--ct, --createsessiontimeout=VALUE
  specify the timeout in seconds used when creating
    a session to an endpoint. On unsuccessful
    connection attemps a backoff up to 5 times the
    specified timeout value is used.
    Min: 1
    Default: 10
--ki, --keepaliveinterval=VALUE
  specify the interval in seconds the publisher is
    sending keep alive messages to the OPC servers
    on the endpoints it is connected to.
    Min: 2
    Default: 2
--kt, --keepalivethreshold=VALUE
  specify the number of keep alive packets a server
    can miss, before the session is disconneced
    Min: 1
    Default: 5
--st, --opcstacktracemask=VALUE
  the trace mask for the OPC stack. See github OPC .
    NET stack for definitions.
    To enable IoT Hub telemetry tracing set it to 711.
    Default: 285  (645)
--as, --autotrustservercerts=VALUE
  the publisher trusts all servers it is
    establishing a connection to.
    Default: False
--tm, --trustmyself=VALUE
  the publisher certificate is put into the trusted
    certificate store automatically.
    Default: True
--fd, --fetchdisplayname=VALUE
  enable to read the display name of a published
    node from the server. this will increase the
    runtime.
    Default: False
--at, --appcertstoretype=VALUE
  the own application cert store type.
    (allowed values: Directory, X509Store)
    Default: 'X509Store'
--ap, --appcertstorepath=VALUE
  the path where the own application cert should be
    stored
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/own'
--tt, --trustedcertstoretype=VALUE
  the trusted cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--tp, --trustedcertstorepath=VALUE
  the path of the trusted cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/trusted'
--rt, --rejectedcertstoretype=VALUE
  the rejected cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--rp, --rejectedcertstorepath=VALUE
  the path of the rejected cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/rejected'
--it, --issuercertstoretype=VALUE
  the trusted issuer cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--ip, --issuercertstorepath=VALUE
  the path of the trusted issuer cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/issuers'
--dt, --devicecertstoretype=VALUE
  the IoT Hub device cert store type.
    (allowed values: Directory, X509Store)
    Default: X509Store
--dp, --devicecertstorepath=VALUE
  the path of the iot device cert store
    Default Default (depends on store type):
    X509Store: 'My'
    Directory: 'CertificateStores/IoT Hub'
-h, --help
  show this message and exit
```

Pode utilizar as seguintes variáveis de ambiente para controlar a aplicação:
- `_HUB_CS`: define a cadeia de ligação do IoT Hub proprietário
- `_GW_LOGP`: define o nome de ficheiro do ficheiro de registo para utilizar
- `_TPC_SP`: define o caminho para armazenar certificados fidedignos estações
- `_GW_PNFP`: define o nome de ficheiro o publicação do ficheiro de configuração

Os argumentos da linha de comandos overrule definições de variáveis de ambiente.

Normalmente, especifique a cadeia de ligação de proprietário do IoT Hub apenas no primeiro início da aplicação. A cadeia de ligação é encriptada e armazenada no arquivo de certificados da plataforma.

As chamadas subsequentes, a cadeia de ligação é ler a partir do arquivo de certificados da plataforma em reutilizadas. Se especificar a cadeia de ligação em cada início, o dispositivo no registo de dispositivos do IoT Hub é removido e recriado cada vez.

### <a name="native-on-windows"></a>Nativo no Windows

Para executar a aplicação nativamente no Windows, abra o `OpcPublisher.sln` projeto com o Visual Studio 2017, compilar a solução e publicá-lo. Pode iniciar a aplicação no **diretório de destino** tiver publicado a com:

```cmd
dotnet OpcPublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-self-built-container"></a>Utilizar um contentor de Self-incorporado

Para executar a aplicação num contentor automática incorporado, criar e, em seguida, iniciar o suas próprias contentor:

```cmd/sh
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-container-from-hubdockercom"></a>Utilizar um contentor de hub.docker.com

Não há um contentor prebuilt disponíveis DockerHub. Para iniciar, execute o seguinte comando:

```cmd/sh
docker run microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="important-when-using-a-container"></a>Importante quando utilizar um contentor

#### <a name="access-to-the-publisher-opc-ua-server"></a>Acesso ao servidor do publicador OPC UA

O servidor do publicador OPC UA por predefinição escuta na porta 62222. Para expor esta porta de entrada num contentor, tem de utilizar o `docker run` opção `-p`:

```cmd/sh
docker run -p 62222:62222 microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="enable-intercontainer-name-resolution"></a>Permitir a resolução de nome intercontainer

Para ativar a resolução do nome de no contentor para outros contentores, tem de:

- Crie uma rede de bridge de docker definido pelo utilizador.
- Ligar o contentor para a rede utilizando o `--network`opção.
- O contentor de atribuir um nome, utilizando o `--name` opção.

O exemplo seguinte mostra estas opções de configuração:

```cmd/sh
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

O contentor agora pode ser acedido por outros contentores através da rede utilizando o nome `publisher`.

#### <a name="assign-a-hostname"></a>Atribua um nome de anfitrião

Publicador utiliza o nome do anfitrião da máquina que é executada para a geração de certificados e o ponto final. Docker escolhe um nome de anfitrião aleatória, a menos que definiu uma com a `-h` opção. Aqui um exemplo para definir o nome de anfitrião interno do contentor para `publisher`:

```cmd/sh
docker run -h publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="using-bind-mounts-shared-filesystem"></a>Utilizar o enlace monta (sistema de ficheiros partilhado)

Em alguns cenários, que pretende ler informações de configuração, ou escrever ficheiros de registo, localizações no anfitrião em vez de utilizar o sistema de ficheiros de contentor. Para configurar este comportamento, utilize o `-v` opção `docker run` no modo de montagem de enlace. Por exemplo:

```cmd/sh
-v //D/docker:/build/out/Logs
-v //D/docker:/build/out/CertificateStores
-v //D/docker:/shared
-v //D/docker:/root/.dotnet/corefx/cryptography/x509stores
```

#### <a name="store-for-x509-certificates"></a>Arquivo de X509 certificados

Armazenar X509 certificados não funciona com o enlace monta, porque as permissões do caminho para o arquivo tem de ser `rw` para o proprietário. Em vez disso, tem de utilizar o `-v` opção `docker run` no modo de volume.

## <a name="debug-the-application"></a>Depurar a aplicação

### <a name="native-on-windows"></a>Nativo no Windows

Abra o `OpcPublisher.sln` projeto com o Visual Studio 2017 e iniciar a depuração da aplicação por atingir F5.

### <a name="in-a-docker-container"></a>Um contentor de docker

Visual Studio 2017 suporta a depuração de aplicações num contentor Docker utilizando `docker-compose`. No entanto, este método não permitem-lhe passar os parâmetros da linha de comandos.

Uma alternativa depuração opção VS2017 suporta é através de depuração `ssh`. Pode utilizar o ficheiro de configuração de compilação do docker `Dockerfile.ssh` na raiz do repositório para criar um contentor SSH ativada:

```cmd/sh
docker build -f .\Dockerfile.ssh -t publisherssh .
```

É agora possível iniciar o contentor para o publicador de depuração:

```cmd/sh
docker run -it publisherssh
```

No contentor, tem de começar a **ssh** daemon manualmente:

```cmd/sh
service ssh start
```

Neste momento, pode criar um **ssh** sessão como utilizador `root` com palavra-passe `Passw0rd`.

Para preparar a depurar a aplicação no contentor, conclua os seguintes passos adicionais:

1. No lado do anfitrião, crie um `launch.json` ficheiro:

    ```json
    {
      "version": "0.2.0",
      "adapter": "<path>\\plink.exe",
      "adapterArgs": "root@localhost -pw Passw0rd -batch -T ~/vsdbg/vsdbg --interpreter=vscode",
      "languageMappings": {
        "C#": {
          "languageId": "3F5162F8-07C6-11D3-9053-00C04FA302A1",
          "extensions": [ "*" ]
        }
      },
      "exceptionCategoryMappings": {
        "CLR": "449EC4CC-30D2-4032-9256-EE18EB41B62B",
        "MDA": "6ECE07A9-0EDE-45C4-8296-818D8FC401D4"
      },
      "configurations": [
        {
          "name": ".NET Core Launch",
          "type": "coreclr",
          "cwd": "~/publisher",
          "program": "Opc.Ua.Publisher.dll",
          "args": "<put-the-publisher-command-line-options-here>",

          "request": "launch"
        }
      ]
    }
    ```

1. Compilar o projeto e publicá-lo para um diretório à sua escolha.

1. Utilizar uma ferramenta como `WinSCP` para copiar os ficheiros publicados para o diretório `/root/publisher` no contentor. Se optar por utilizar um diretório diferente, atualize o `cdw` propriedade no `launch.json` ficheiro.

Agora pode começar a depuração utilizando o seguinte comando na janela de comandos do Visual Studio:

```cmd
DebugAdapterHost.Launch /LaunchJson:"<path-to-the-launch.json-file-you-saved>"
```

## <a name="next-steps"></a>Passos seguintes

Sugestões de um passo seguinte é saber como [implementar um gateway no Windows ou Linux para a solução de fábrica ligado pré-configurada](iot-suite-connected-factory-gateway-deployment.md).