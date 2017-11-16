---
title: "Compreender os módulos do limite do Azure IoT | Microsoft Docs"
description: "Saiba mais sobre os módulos de limite de IoT do Azure e como estão configuradas"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 726bbafa9e4ba35cfa4a9cbf4d89056d52fe7963
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="understand-azure-iot-edge-modules---preview"></a>Compreender os módulos de limite de IoT do Azure – pré-visualização

Limite de IoT do Azure permite-lhe implementar e gerir a lógica de negócio na margem sob a forma de *módulos*. Módulos de limite de IoT do Azure são a unidade de computação gerida pelo IoT Edge menor e podem conter os serviços do Azure (por exemplo, o Azure Stream Analytics) ou o seu próprio código específico da solução. Para compreender como módulos são desenvolvidos, implementados e mantidos, ajuda a pensar de quatro partes conceptuais que compõem um módulo de:

* A **imagem módulo** é um pacote que contém o software que define um módulo.
* A **instância do módulo** é a unidade de computação com a imagem de módulo num dispositivo de limite de IoT específica. A instância do módulo é iniciada pelo tempo de execução de limite de IoT.
* A **identidade do módulo** é um elemento de informação (incluindo credenciais de segurança) armazenado no IoT Hub, o que se encontra associada a cada instância do módulo.
* A **duplo módulo** é um documento JSON armazenado no IoT Hub, que contém informações de estado para uma instância do módulo, incluindo os metadados, configurações e condições. 

## <a name="module-images-and-instances"></a>Imagens de módulo e instâncias

Imagens de módulo de limite de IoT contêm aplicações que tirar partido da gestão, segurança e funcionalidades de comunicação do tempo de execução de limite de IoT. Pode desenvolver as suas próprias imagens de módulo ou exportar um de um serviço do Azure suportado, como o Azure Stream Analytics.
As imagens de existem na nuvem e podem ser atualizados, alteradas e implementadas em diferentes soluções. Por exemplo, existe um módulo que utiliza a aprendizagem automática prever a saída da linha de produção como uma imagem separada do que um módulo que utiliza visão de computador para controlar um drone. 

Sempre que uma imagem do módulo é implementada num dispositivo e iniciada pelo runtime IoT Edge, é criada uma nova instância desse módulo. Dois dispositivos em diferentes partes de universo de utilizar a mesma imagem do módulo; No entanto cada teria sua própria instância do módulo quando o módulo é iniciado no dispositivo. 

![Imagens de módulo na nuvem - instâncias do módulo em dispositivos][1]

Numa implementação, imagens de módulos de existir como imagens contentor num repositório e instâncias do módulo são contentores nos dispositivos. À medida que ficam casos de utilização para o Azure IoT limite, serão criados novos tipos de imagens do módulo e instâncias. Por exemplo, dispositivos de recurso restringido não é possível executar contentores assim pode necessitar de imagens de módulo que existam como bibliotecas de ligação dinâmica e instâncias de executáveis. 

## <a name="module-identities"></a>Identidades de módulo

Quando é criada uma nova instância do módulo pelo runtime IoT Edge, a instância está associada uma identidade de módulo correspondente. A identidade do módulo é armazenada no IoT Hub e é utilizada como o âmbito de endereçamento e de segurança para todos os locais e na nuvem de comunicações para essa instância do módulo específico.
A identidade associada à instância de um módulo depende a identidade do dispositivo em que a instância está em execução e o nome que fornecer a esse módulo na sua solução. Por exemplo, se chamar `insight` um módulo que utiliza um Azure Stream Analytics e implementá-la num dispositivo chamado `Hannover01`, o tempo de execução do limite de IoT cria uma identidade de módulo correspondente chamada `/devices/Hannover01/modules/insight`.

Claramente, nos cenários quando precisar de implementar uma imagem de módulo várias vezes no mesmo dispositivo, pode implementar a mesma imagem várias vezes com nomes diferentes.

![Identidades de módulo são exclusivas][2]

## <a name="module-twins"></a>Duplos do módulo

Cada instância do módulo tem também um duplo correspondente do módulo que pode utilizar para configurar a instância do módulo. A instância e o duplo estão associados entre si através de identidade do módulo. 

Duplo um módulo é um documento JSON que armazena as propriedades de informações e a configuração do módulo. Este conceito parallels o [dispositivo duplo] [ lnk-device-twin] conceito do IoT Hub. A estrutura de um duplo do módulo é exatamente o mesmo que um dispositivo duplo. As APIs utilizadas para interagir com os dois tipos de duplos também são os mesmos. A única diferença entre as duas é a identidade utilizada para instanciar o SDK do cliente. 

```
// Create a DeviceClient object. This DeviceClient will act on behalf of a 
// module since it is created with a module’s connection string instead 
// of a device connection string. 
DeviceClient client = new DeviceClient.CreateFromConnectionString(moduleConnectionString, settings); 
await client.OpenAsync(); 
 
// Get the model twin 
Twin twin = await client.GetTwinAsync(); 
```

## <a name="next-steps"></a>Passos seguintes
 - [Compreender o tempo de execução do limite do Azure IoT e respetiva arquitetura][lnk-runtime]

<!-- Images -->
[1]: ./media/iot-edge-modules/image_instance.png
[2]: ./media/iot-edge-modules/identity.png

<!-- Links -->
[lnk-device-identity]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-runtime]: iot-edge-runtime.md