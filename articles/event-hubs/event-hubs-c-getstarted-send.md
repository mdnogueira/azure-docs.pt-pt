---
title: Enviar eventos para os Hubs de eventos do Azure utilizando C | Microsoft Docs
description: Enviar eventos para utilizando C de Event Hubs do Azure
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: 25311958314cca049d109ecbe3f46aaaa36b694d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="send-events-to-azure-event-hubs-using-c"></a>Enviar eventos para utilizando C de Event Hubs do Azure

## <a name="introduction"></a>Introdução
Os Event Hubs são um sistema de ingestão altamente dimensionável, que pode ingerir milhões de eventos por segundo, que uma aplicação possa processar e analisar as quantidades enormes de dados produzidos pelos seus dispositivos e aplicações ligados. Depois de recolhidos para um hub de eventos, pode transformar e armazenar dados através de qualquer fornecedor de análise em tempo real ou cluster de armazenamento.

Para obter mais informações, consulte [descrição geral dos Event Hubs] [descrição geral dos Event Hubs].

Neste tutorial, ficará a saber como enviar eventos para um hub de eventos utilizando uma aplicação de consola no C. Para receber eventos, clique o idioma apropriado de receção na tabela da esquerda do conteúdo.

Para concluir este tutorial, irá precisar do seguinte:

* Num ambiente de desenvolvimento de C. Para este tutorial, iremos assumir a pilha de gcc numa VM com Ubuntu 14.04 Linux do Azure.
* [Microsoft Visual Studio](https://www.visualstudio.com/).
* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="send-messages-to-event-hubs"></a>Enviar mensagens para os Event Hubs
Nesta secção, vamos escrever uma aplicação de C para enviar eventos para o hub de eventos. O código utiliza a biblioteca de Proton AMQP do [Apache Qpid projeto](http://qpid.apache.org/). Isto é semelhante a utilização de tópicos e filas do Service Bus com AMQP do C conforme mostrado [aqui](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Para obter mais informações, consulte [documentação Qpid Proton](http://qpid.apache.org/proton/index.html).

1. Do [página Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html), siga as instruções para instalar Qpid Proton, dependendo do seu ambiente.
2. Para compilar a biblioteca de Proton, instale os seguintes pacotes:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Transferir o [biblioteca Qpid Proton](http://qpid.apache.org/proton/index.html)e extrair, por ex.:
   
    ```shell
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Crie um diretório de compilação, a compilação e a instalar:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. No seu diretório de trabalho, crie um novo ficheiro chamado **sender.c** com o seguinte código. Não se esqueça de substituir o valor para o seu nome de hub de eventos e o nome do espaço de nomes. Também deve substituir uma versão com codificação URL da chave para o **SendRule** criada anteriormente. É possível codificar o URL [aqui](http://www.w3schools.com/tags/ref_urlencode.asp).
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. Compilar o ficheiro, partindo do princípio de **gcc**:
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Este código, utilizamos uma janela de saída de 1 para forçar as mensagens de saída logo que possível. Em geral, a aplicação deve tentar mensagens de batch para aumentar o débito. Consulte o [página Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html) para obter informações sobre como utilizar a biblioteca de Qpid Proton neste e noutros ambientes e de plataformas para os quais são fornecidos enlaces (atualmente Perl, PHP, Python, Ruby e o).


## <a name="next-steps"></a>Passos seguintes
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md
)
* [Criar um hub de eventos](event-hubs-create.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
