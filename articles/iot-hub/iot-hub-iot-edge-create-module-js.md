---
title: "Criar um módulo de limite de IoT do Azure com o Node.js | Microsoft Docs"
description: "Este tutorial showcases como escrever um módulo de conversor var dados utilizando os pacotes de NPM de limite de IoT do Azure mais recentes e Yeoman gerador de dimensões."
services: iot-hub
author: sushi
manager: timlt
ms.service: iot-hub
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: js
ms.topic: article
ms.date: 06/28/2017
ms.author: sushi
ms.openlocfilehash: e23c4aa7bb3eb4fab18d5a13cbad28e07c18d8f2
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-iot-edge-module-with-nodejs"></a>Criar um módulo de limite de IoT do Azure com o Node.js

Este tutorial showcases como criar um módulo para o limite de IoT do Azure em JS.

Neste tutorial, iremos guiá-configuração do ambiente e como escrever um [var](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) módulo de conversor de dados utilizando os pacotes de NPM de limite de IoT do Azure mais recentes.

## <a name="prerequisites"></a>Pré-requisitos

Nesta secção, iremos configurar o ambiente para o desenvolvimento de módulo de limite de IoT. Aplica-se ambos *Windows de 64 bits* e *Linux de 64 bits (Ubuntu 14 +)* sistemas operativos.

É necessário o seguinte software:
* [Cliente de Git](https://git-scm.com/downloads).
* [Nó LTS](https://nodejs.org).
* `npm install -g yo`.
* `npm install -g generator-az-iot-gw-module`

## <a name="architecture"></a>Arquitetura

A plataforma do Azure IoT Edge adopts descontos elevados a [arquitetura Von Neumann](https://en.wikipedia.org/wiki/Von_Neumann_architecture). O que significa que a arquitetura de limite de IoT do Azure completa é um sistema que processa a entrada e produz saída; e cada módulo individuais também é um subsistema de entrada-saída muito pequena. Neste tutorial, iremos introduzir os seguintes dois módulos:

1. Um módulo que recebe um simulada [var](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) assinalar e converte-o num formatado [JSON](https://en.wikipedia.org/wiki/JSON) mensagem.
2. Um módulo que imprima o recebida [JSON](https://en.wikipedia.org/wiki/JSON) mensagem.

A imagem seguinte mostra o fluxo de dados de ponto a ponto típico para este projeto:

![Fluxo de dados entre três módulos](media/iot-hub-iot-edge-create-module/dataflow.png "entrada: Simulated var módulo; Processador: Módulo de conversor; Saída: Módulo de impressora")

## <a name="set-up-the-environment"></a>Configurar o ambiente
Abaixo vamos mostrar-lhe como configurar rapidamente ambiente para começar a escrever o módulo de conversor var primeiro com JS.

### <a name="create-module-project"></a>Criar projeto do módulo
1. Abra uma janela da linha de comandos, execute `yo az-iot-gw-module`.
2. Siga os passos no ecrã para concluir a inicialização do seu projeto de módulo.

### <a name="project-structure"></a>Estrutura do projeto
Um projeto de módulo JS é composta pelos seguintes componentes:

`modules`-Os personalizado JS módulo ficheiros de origem. Substituir a predefinição `sensor.js` e `printer.js` com os seus próprios ficheiros de módulo.

`app.js`-O ficheiro de entrada para iniciar a instância de limite.

`gw.config.json`-O ficheiro de configuração para personalizar os módulos carregados pelo limite.

`package.json`-As informações de metadados para o projeto de módulo.

`README.md`-A documentação básica para o projeto de módulo.


### <a name="package-file"></a>Ficheiro de pacote

Isto `package.json` declara todas as informações de metadados necessárias para um projeto de módulo que inclui as dependências de nome, versão, entrada, scripts, tempo de execução e desenvolvimento.

O fragmento de código seguinte mostra como configurar para o projeto de exemplo de conversor var.
```json
{
  "name": "converter",
  "version": "1.0.0",
  "description": "BLE data converter sample for Azure IoT Edge.",
  "repository": {
    "type": "git",
    "url": "https://github.com/Azure-Samples/iot-edge-samples"
  },
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "author": "Microsoft Corporation",
  "license": "MIT",
  "dependencies": {
  },
  "devDependencies": {
    "azure-iot-gateway": "~1.1.3"
  }
}
```


### <a name="entry-file"></a>Ficheiro de entrada
O `app.js` define a forma de inicializar a instância de limite. Aqui não temos de fazer qualquer alteração.

```javascript
(function() {
  'use strict';

  const Gateway = require('azure-iot-gateway');
  let config_path = './gw.config.json';

  // node app.js
  if (process.argv.length < 2) {
    throw 'Calling pattern should be node app.js.';
  }

  const gw = new Gateway(config_path);
  gw.run();
})();
```

### <a name="interface-of-module"></a>Interface do módulo
Pode tratar um módulo do Azure IoT Edge como um processador de dados cujo trabalho consiste em: receber entrada, processá-la e produzir resultados.

A entrada pode ser dados do hardware (por exemplo, um movimento detector), uma mensagem de outros módulos ou qualquer outro (como um número aleatório gerado periodicamente por um temporizador).

O resultado será semelhante para a entrada, poderia acionar comportamento de hardware (como o LED intermitente), uma mensagem para outros módulos ou qualquer outro (como a impressão para a consola).

Módulos comunicam entre si utilizando `message` objeto. O **conteúdo** de um `message` é uma matriz de bytes que seja capaz de que representa qualquer tipo de dados que quiser. **Propriedades** também estão disponíveis no `message` e são simplesmente um mapeamento de cadeia-cadeia. Poderá achar de **propriedades** como cabeçalhos de um pedido HTTPS ou os metadados de um ficheiro.

Para desenvolver um módulo do Azure IoT Edge no JS, terá de criar um novo objeto de módulo que implementa os métodos necessários `receive()`. Neste momento, pode também optar por implementar o opcional `create()` ou `start()`, ou `destroy()` métodos bem. O fragmento de código seguinte mostra o andaime do objeto de módulo JS.

```javascript
'use strict';

module.exports = {
  broker: null,
  configuration: null,

  create: function (broker, configuration) {
    // Default implementation.
    this.broker = broker;
    this.configuration = configuration;

    return true;
  },

  start: function () {
    // Produce
  },

  receive: function (message) {
    // Consume
  },

  destroy: function () {
  }
};
```

### <a name="converter-module"></a>Módulo de conversor
| Input                    | Processador                              | Saída                 | Ficheiro de origem            |
| ------------------------ | -------------------------------------- | ---------------------- | ---------------------- |
| Mensagens de dados de temperatura | Analisar e construir uma nova mensagem JSON | Mensagem JSON de estrutura | `converter.js` |

Este módulo é um módulo de limite do Azure IoT típico. Aceita mensagens de temperatura de outros módulos (um módulo de hardware, ou deste caso nosso módulo var simulado); e, em seguida, normaliza a mensagem de temperatura na uma mensagem JSON estruturada (incluindo acrescentar o ID de mensagem, a definição da propriedade dos temos acionar o alerta de temperatura e assim sucessivamente).

```javascript
receive: function (message) {
  // Initialize the messageCount in global object at first time.
  if (!global.messageCount) {
    global.messageCount = 0;
  }

  // Read the content and properties objects from message.
  let rawContent = JSON.parse(Buffer.from(message.content).toString('utf8'));
  let rawProperties = message.properties;

  // Generate new properties object.
  let newProperties = {
    source: rawProperties.source,
    macAddress: rawProperties.macAddress,
    temperatureAlert: rawContent.temperature > 30 ? 'true' : 'false'
  };

  // Generate new content object.
  let newContent = {
    deviceId: 'Intel NUC Gateway',
    messageId: ++global.messageCount,
    temperature: rawContent.temperature
  };

  // Publish the new message to broker.
  this.broker.publish(
    {
      properties: newProperties,
      content: new Uint8Array(Buffer.from(JSON.stringify(newContent), 'utf8'))
    }
  );
},
```

### <a name="printer-module"></a>Módulo de impressora
| Input                          | Processador | Saída                     | Ficheiro de origem          |
| ------------------------------ | --------- | -------------------------- | -------------------- |
| Qualquer mensagem de outros módulos | N/D       | A mensagem de registo para a consola | `printer.js` |

Este módulo é simples, facilmente compreensíveis, o que produz as mensagens recebidas (propriedade, conteúdo) para a janela de terminal.

```javascript
receive: function (message) {
  let properties = JSON.stringify(message.properties);
  let content = Buffer.from(message.content).toString('utf8');

  console.log(`printer.receive.properties - ${properties}`);
  console.log(`printer.receive.content - ${content}\n`);
}
```

### <a name="configuration"></a>Configuração
É o último passo antes de executar os módulos para configurar o limite de IoT do Azure e para estabelecer as ligações entre módulos.

Primeiro é necessário declarar nosso `node` carregador (desde o limite de IoT do Azure suporta carregadores de idiomas diferentes) que pode ser referenciada pelo respetivo `name` nas secções mais tarde.

```json
"loaders": [
  {
    "type": "node",
    "name": "node"
  }
]
```

Depois de ter declarámos nosso carregadores, também é necessário declarar o nosso módulos bem. Semelhante para declarar os carregadores, estes também podem ser referenciados pelo respetivo `name` atributo. Quando declarar um módulo, é necessário especificar o carregador deve utilizar (o que deve ser o que definimos antes) e o-ponto de entrada (deve ser o nome de classe normalizado do nosso módulo) para cada módulo. O `simulated_device` módulo é um módulo nativo, que está incluído no pacote de tempo de execução de núcleos de limite de IoT do Azure. Incluir `args` no JSON de ficheiros, mesmo que esteja `null`.

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
      "name": "node",
      "entrypoint": {
        "main.path": "modules/converter.js"
      }
    },
    "args": null
  },
  {
    "name": "printer",
    "loader": {
      "name": "node",
      "entrypoint": {
        "main.path": "modules/printer.js"
      }
    },
    "args": null
  }
]
```

No final da configuração, vamos estabelecer as ligações. Cada ligação é expresso por `source` e `sink`. Estes deverá ambos referenciar um módulo predefinido. A mensagem de saída de `source` módulo seja reencaminhado para a entrada de `sink` módulo.

```json
"links": [
  {
    "source": "simulated_device",
    "sink": "converter"
  },
  {
    "source": "converter",
    "sink": "printer"
  }
]
```

## <a name="running-the-modules"></a>Executar os módulos
1. `npm install`
2. `npm start`

Se pretende terminar a aplicação, prima `<Enter>` chave.

> [!IMPORTANT]
> Não se recomenda utilizar Ctrl + C para terminar a aplicação de limite de IoT. Como desta forma pode fazer com que o processo esteja terminado.
