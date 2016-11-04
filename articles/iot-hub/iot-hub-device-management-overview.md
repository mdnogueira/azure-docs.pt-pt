---
title: Descrição Geral da gestão de dispositivos | Microsoft Docs
description: 'Descrição geral da gestão de dispositivos do Hub IoT do Azure: dispositivos duplos, consultas de dispositivo, tarefas de dispositivo'
services: iot-hub
documentationcenter: ''
author: juanjperez
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/29/2016
ms.author: juanpere

---
# Descrição geral da gestão de dispositivos do Hub IoT (pré-visualização)
A gestão de dispositivos do Hub IoT do Azure permite a gestão de dispositivos da IoT baseada em normas para que possa gerir remotamente, configurar e atualizar os seus dispositivos.

Existem três conceitos principais para a gestão de dispositivos no Azure IoT:

1. **Dispositivo duplo:** a representação do dispositivo físico no Hub IoT.
2. **Consultas de dispositivo**: permitem-lhe encontrar dispositivos duplos e gerar uma compreensão agregada de vários dispositivos duplos. Por exemplo, é possível executar uma consulta para encontrar todos os dispositivos duplos com a versão de firmware 1.0.
3. **Tarefas de dispositivo**: uma ação para executar num ou mais dispositivos físicos, como a atualização de firmware, o reinício e a reposição de fábrica.

## Dispositivo duplo
O dispositivo duplo é a representação de um dispositivo físico no Azure IoT. O objeto **Microsoft.Azure.Devices.Device** é utilizado para representar o dispositivo duplo.

![][img-twin]

O dispositivo duplo tem os seguintes componentes:

1. **Campos do Dispositivo:** os campos do dispositivo são propriedades predefinidas utilizadas para as mensagens do Hub IoT e para a gestão de dispositivos. Estes campos ajudam o Hub IoT a identificar e ligar-se a dispositivos físicos. Os campos do dispositivo não estão sincronizados no dispositivo e são armazenados exclusivamente no dispositivo duplo. Os campos do dispositivo incluem o ID do dispositivo e as informações da autenticação.
2. **Propriedades do Dispositivo:** as propriedades do dispositivo são um dicionário predefinido das propriedades que descrevem o dispositivo físico. O dispositivo físico é o mestre de cada propriedade do dispositivo e é o arquivo de autoridade de cada valor correspondente. É armazenada uma representação eventualmente consistente destas propriedades no dispositivo duplo na nuvem. A coerência e a atualização estão sujeitos a definições de sincronização, descritas no [Tutorial: como utilizar o dispositivo duplo][Ink-tutorial-twin]. Alguns exemplos de propriedades dos dispositivos incluem a versão de firmware, o nível da bateria e o nome do fabricante.
3. **Propriedades do Serviço:** as propriedades do serviço são os pares **&lt;valor, chave&gt;** que o programador adiciona ao dicionário de propriedades do serviço. Estas propriedades expandem o modelo de dados para o dispositivo duplo, permitindo-lhe caraterizar melhor o seu dispositivo. As propriedades de serviço não estão sincronizadas no dispositivo e são armazenadas exclusivamente no dispositivo duplo na nuvem. Um exemplo de uma propriedade de serviço é **&lt;NextServiceDate, 12/11/2017&gt;**, que poderia ser utilizado para encontrar dispositivos pela próxima data do serviço.
4. **Etiquetas:** as etiquetas são um subconjunto de propriedades do serviço que são cadeias arbitrárias, em vez de propriedades do dicionário. Podem ser utilizadas para anotar dispositivos duplos ou organizar os dispositivos em grupos. As etiquetas não estão sincronizadas no dispositivo e são armazenadas exclusivamente no dispositivo duplo. Por exemplo, se o seu dispositivo duplo representa um camião físico, pode adicionar uma etiqueta para cada tipo de carga no camião – **maçãs**, **laranjas** e **bananas**.

## Consultas de Dispositivo
Na secção anterior, aprendeu sobre os diferentes componentes do dispositivo duplo. Agora, iremos explicar como pode encontrar dispositivos duplos no registo de dispositivos do Hub IoT com base nas propriedades dos dispositivos, nas propriedade do serviço ou nas etiquetas. Um exemplo de quando deverá utilizar uma consulta é ao encontrar dispositivos que necessitam de ser atualizados. Pode consultar todos os dispositivos com uma versão de firmware especificada e direcionar o resultado para uma ação específica (conhecido no Hub IoT como uma tarefa de dispositivo, que é explicada na secção seguinte).

Pode consultar ao utilizar etiquetas e propriedades:

* Para consultar os dispositivos duplos com etiquetas, passa uma matriz de cadeias e a consulta devolve o conjunto de dispositivos que estão marcados com todas essas cadeias.
* Para consultar dispositivos duplos com as propriedades do serviço ou dos dispositivos, pode utilizar uma expressão de consulta JSON. O exemplo abaixo mostra como poderia consultar todos os dispositivos com a propriedade do dispositivo com a chave **FirmwareVersion** e valor o **1.0**. Pode ver que o **tipo** da propriedade é o **dispositivo**, com a indicação de que estamos a consultar com base nas propriedades dos dispositivos, e não nas propriedades do serviço:
  
  ```
  {                           
    "filter": {                  
      "property": {                
        "name": "FirmwareVersion",   
        "type": "device"             
      },                           
      "value": "1.0",              
      "comparisonOperator": "eq",  
      "type": "comparison"         
    },                           
    "project": null,             
    "aggregate": null,           
    "sort": null                 
  }
  ```

## Tarefas de Dispositivo
O próximo conceito na gestão de dispositivos são as tarefas de dispositivo, que permitem a coordenação de orquestrações com vários passos em vários dispositivos.

Existem seis tipos de tarefas de dispositivo que são fornecidas pela gestão de dispositivos do Hub IoT do Azure no presente (serão adicionadas tarefas adicionais conforme a necessidade dos clientes):

* **Atualização de firmware**: atualiza o firmware (ou imagem do SO) no dispositivo físico.
* **Reinício**: reinicia o dispositivo físico.
* **Reposição de fábrica**: reverte o firmware (ou a imagem do SO) do dispositivo físico para uma imagem de cópia de segurança da fábrica fornecida e armazenada no dispositivo.
* **Atualização da configuração**: configura o agente de cliente do Hub IoT em execução no dispositivo físico.
* **Ler a propriedade do dispositivo**: obtém o valor mais recente de uma propriedade do dispositivo no dispositivo físico.
* **Escrever a propriedade do dispositivo:** altera uma propriedade do dispositivo no dispositivo físico.

Para obter detalhes sobre como utilizar cada uma destas tarefas, consulte a [documentação da API para C\# e node.js][Ink apidocs].

Uma tarefa pode operar em vários dispositivos. Quando iniciar uma tarefa, é criada uma tarefa subordinada associada em cada um desses dispositivos. Uma tarefa subordinada funciona num único dispositivo. Cada tarefa subordinada tem um ponteiro para a sua tarefa principal. A tarefa principal é apenas um contentor das tarefas subordinadas, não implementa qualquer lógica para distinguir entre tipos de dispositivos (por exemplo, ao atualizar um Intel Edison versus um Raspberry Pi). O diagrama seguinte ilustra a relação entre uma tarefa principal, a sua subordinada e os dispositivos físicos associados.

![][img-jobs]

Pode consultar o histórico de tarefas para compreender o estado das tarefas iniciadas. Para algumas consultas de exemplo, consulte [a nossa biblioteca de consultas][lnk-query-samples].

## Implementação de dispositivo
Agora que abrangemos os conceitos do lado do serviço, vamos discutir sobre como criar um dispositivo físico gerido. A biblioteca cliente do DM do Hub IoT do Azure permite-lhe gerir dispositivos com o Hub IoT do Azure. "Gerir" inclui ações como o reinício, a reposição de fábrica e a atualização do firmware.  Hoje em dia fornecemos uma biblioteca de C independente da plataforma, mas será adicionado suporte para outros idiomas em breve.  

A biblioteca cliente do DM tem duas responsabilidades principais na gestão de dispositivos:

* Sincronizar propriedades no dispositivo físico com o dispositivo duplo correspondente no Hub IoT
* Coreografar tarefas do dispositivo enviadas pelo Hub IoT para o dispositivo

Para saber mais sobre estas responsabilidades e a implementação no dispositivo físico, consulte [Introdução da biblioteca de cliente de gestão de dispositivos do Hub IoT do Azure para C][lnk-library-c].

## Passos seguintes
Para implementar aplicações de cliente em várias plataformas de hardware e sistemas operativos para dispositivos, pode utilizar os SDKs do dispositivo IoT. Os SDKs do dispositivo IoT incluem bibliotecas que facilitam o envio de telemetria a um IoT Hub e a receção de comandos da nuvem para o dispositivo. Quando utiliza os SDKs, pode escolher entre vários protocolos de rede para comunicar com o IoT Hub. Para saber mais, consulte [informações sobre os SDKs do dispositivo][Ink-dispositivo-sdks].

Para saber mais sobre as funcionalidades de gestão de dispositivos do Hub IoT do Azure, consulte o tutorial [Introdução à gestão de dispositivos do Hub IoT do Azure][lnk-get-started].

<!-- Images and links -->
[img-twin]: media/iot-hub-device-management-overview/image1.png
[img-jobs]: media/iot-hub-device-management-overview/image2.png
[img-client]: media/iot-hub-device-management-overview/image3.png

[lnk-lwm2m]: http://technical.openmobilealliance.org/Technical/technical-information/release-program/current-releases/oma-lightweightm2m-v1-0
[lnk-library-c]: iot-hub-device-management-library.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[Ink-tutorial-twin]: iot-hub-device-management-device-twin.md
[Ink apidocs]: http://azure.github.io/azure-iot-sdks/
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md
[Ink-dispositivo-sdks]: https://github.com/Azure/azure-iot-sdks



<!--HONumber=Aug16_HO1-->


