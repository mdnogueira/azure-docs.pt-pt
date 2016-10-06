<properties
    pageTitle="Introdução ao SDK do Gateway do Hub IoT | Microsoft Azure"
    description="Instruções do SDK do Gateway do Hub IoT do Azure que utilizam o Linux para ilustrar os conceitos-chave que deve compreender ao utilizar o SDK do Gateway do Hub IoT do Azure."
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>



# SDK do Gateway de IoT (beta) – Introdução à utilização do Linux

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## Como criar o exemplo

Antes de começar, tem de [configurar o seu ambiente de desenvolvimento][lnk-setupdevbox] para trabalhar com o SDK no Linux.

1. Abra uma shell.
2. Navegue até à pasta raiz na sua cópia local do repositório **azure-iot-gateway-sdk**.
3. Execute o script **tools/build.sh**. Este script utiliza o utilitário **cmake** para criar uma pasta denominada **build** na pasta raiz da sua cópia local do repositório **azure-iot-gateway-sdk** e gerar um makefile. Em seguida, o script compila a solução e executa os testes.

> [AZURE.NOTE]  Sempre que executar o script **build.sh**, este elimina e, em seguida, recria a pasta **build** na pasta raiz da sua cópia local do repositório **azure-iot-gateway-sdk**.

## Como executar o exemplo

1. O script **build.sh** gera o respetivo resultado na pasta **build** na sua cópia local do repositório **azure-iot-gateway-sdk**. Isto inclui os dois módulos utilizados neste exemplo.

    O script build coloca **liblogger_hl.so** na pasta **build/modules/logger/** e **libhello_world_hl.so** na pasta **build/modules/hello_world/**. Utilize estes caminhos para o valor **caminho do módulo**, conforme mostrado no ficheiro de definições JSON abaixo.

2. O ficheiro **hello_world_lin.json** na pasta **samples/hello_world/src** é um ficheiro de definições JSON de exemplo para o Linux que pode utilizar para executar o exemplo. As definições JSON de exemplo apresentadas abaixo assumem que irá executar o exemplo a partir da raiz da sua cópia local do repositório **azure-iot-gateway-sdk**.

3. Para o módulo **logger_hl**, na secção **args**, defina o valor **filename** para o nome e o caminho do ficheiro que irá conter os dados de registo.

    Este é um exemplo de um ficheiro de definições JSON para Linux que irá escrever em **log.txt** na pasta em que o exemplo é executado.

    ```
    {
      "modules" :
      [ 
        {
          "module name" : "logger_hl",
          "module path" : "./build/modules/logger/liblogger_hl.so",
          "args" : 
          {
            "filename":"./log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "./build/modules/hello_world/libhello_world_hl.so",
          "args" : null
        }
      ],
      "links" :
      [
        {
          "source": "hello_world",
          "sink": "logger_hl"
        }
      ]
    }
    ```

3. Na sua shell, navegue até à pasta **azure-iot-gateway-sdk**.
4. Execute o seguinte comando:
  
  ```
  ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
  ``` 

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md



<!--HONumber=Sep16_HO4-->


