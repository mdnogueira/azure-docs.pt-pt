---
title: "Gestão de dispositivos na solução de monitorização remota - Azure | Microsoft Docs"
description: "Este tutorial mostra como gerir dispositivos ligados à solução de monitorização remota."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 09/06/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: e0b16a30bfd3b7ed711bcb1cc955d4eccf09fac2
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="manage-and-configure-your-devices"></a>Gerir e configurar os seus dispositivos

Este tutorial mostra o dispositivo capacidades de gestão de solução de monitorização remota. Para apresentar estas capacidades, o tutorial utiliza um cenário na aplicação Contoso IoT.

Contoso tem ordenadas fábrica, a nova para expandir um dos respetivos instalações para aumentar a saída. Enquanto a aguardar ser entregue fábrica, a nova, que pretende executar uma simulação para verificar o comportamento da sua solução. Como um operador, que pretende gerir e configurar os dispositivos na solução de monitorização remota.

Para fornecer uma forma extensível para gerir e configurar dispositivos, a solução de monitorização remota utiliza funcionalidades do IoT Hub, tais como [tarefas](../iot-hub/iot-hub-devguide-jobs.md) e [direcionar métodos](../iot-hub/iot-hub-devguide-direct-methods.md). Para saber como um programador de dispositivo implementa métodos num dispositivo físico, consulte [personalizar a solução pré-configurada de monitorização remota](iot-suite-remote-monitoring-customize.md).

Neste tutorial, ficará a saber como:

>[!div class="checklist"]
> * Aprovisione um dispositivo simulado.
> * Teste o dispositivo simulado.
> * Chame os métodos de dispositivos da solução.
> * Reconfigure um dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este tutorial, precisa de uma instância implementada da solução de monitorização remota na sua subscrição do Azure.

Se ainda não implementado a solução de monitorização remota ainda, deve efetuar o [implementar a solução pré-configurada de monitorização remota](iot-suite-remote-monitoring-deploy.md) tutorial.

## <a name="provision-a-simulated-device"></a>Aprovisionar um dispositivo simulado

Navegue para o **dispositivos** página na solução e, em seguida, escolha **aprovisionar**. No **aprovisionar** painel, escolha **Simulated**:

![Aprovisionar um dispositivo simulado](media/iot-suite-remote-monitoring-manage/devicesprovision.png)

Deixe o número de dispositivos para aprovisionar definido como **1**. Escolha **motor** como o **modelo do dispositivo**e, em seguida, escolha **aplicar** para criar o dispositivo simulado:

![Aprovisionar um dispositivo simulado motor](media/iot-suite-remote-monitoring-manage/devicesprovisionengine.png)

Para saber como aprovisionar um *físico* dispositivo, consulte [ligar o seu dispositivo à solução pré-configurada de monitorização remota](iot-suite-connecting-devices-node.md).

## <a name="test-the-simulated-device"></a>Testar o dispositivo simulado

Para ver os detalhes do seu novo dispositivo simulado, selecione-o na lista de dispositivos no **dispositivos** página. Apresenta informações sobre o seu dispositivo no **detalhes do dispositivo** painel:

![Ver o novo dispositivo simulado motor](media/iot-suite-remote-monitoring-manage/devicesviewnew.png)

No **detalhes do dispositivo**, certifique-se de que o novo dispositivo está a enviar telemetria. Para ver os fluxos de telemetria diferente do seu dispositivo, escolha um nome de telemetria como **vibration**:

![Selecione um fluxo de telemetria para ver](media/iot-suite-remote-monitoring-manage/devicesvibration.png)

O **detalhes do dispositivo** painel apresenta outras informações sobre o dispositivo como valores de etiqueta, os métodos que suporta e as propriedades comunicadas pelo dispositivo.

Para ver o diagnóstico detalhado, desloque para baixo para a vista **diagnóstico**.

## <a name="act-on-a-device"></a>Agir num dispositivo

Para funcionar num dispositivo, selecione-o na lista de dispositivos e, em seguida, escolha **agenda**. O **motor** o modelo de dispositivo especifica quatro métodos de um dispositivo tem de suportar:

![Métodos de motor](media/iot-suite-remote-monitoring-manage/devicesmethods.png)

Escolha **reiniciar**, defina o nome da tarefa **RestartEngine**e, em seguida, escolha **aplicar**:

![Agendar o método de reinício](media/iot-suite-remote-monitoring-manage/devicesrestartengine.png)

Para controlar o estado da tarefa no **manutenção** página, escolha **estado do sistema**:

![Monitorizar a tarefa de agendas](media/iot-suite-remote-monitoring-manage/maintenancerestart.png)

### <a name="methods-in-other-devices"></a>Métodos em outros dispositivos

Quando a explorar os tipos de dispositivo simulado diferente, pode ver que outros tipos de dispositivo suportam métodos diferentes. Numa implementação com dispositivos físicos, o modelo de dispositivo Especifica os métodos que deve suportar o dispositivo. Normalmente, o Programador de dispositivo é responsável por desenvolver o código que faz com que o dispositivo agir em resposta a uma chamada de método.

Para agendar um método para ser executado em vários dispositivos, pode selecionar vários dispositivos na lista no **dispositivos** página. O **agenda** painel mostra os tipos de método comuns a todos os dispositivos selecionados.

## <a name="reconfigure-a-device"></a>Reconfigurar um dispositivo

Para alterar a configuração de um dispositivo, selecione-o na lista de dispositivos no **dispositivos** página e, em seguida, escolha **reconfigurar**. O painel de reconfigure mostra os valores de propriedade para o dispositivo selecionado que podem ser alteradas:

![Reconfigurar um dispositivo](media/iot-suite-remote-monitoring-manage/devicesreconfigure.png)

Efetuar uma alteração, adicionar um nome para a tarefa, atualize os valores de propriedade e escolha **aplicar**:

![Atualizar um valor de propriedade do dispositivo](media/iot-suite-remote-monitoring-manage/devicesreconfigurephysical.png)

Para controlar o estado da tarefa no **manutenção** página, escolha **estado do sistema**.

## <a name="next-steps"></a>Passos seguintes

Este tutorial mostrou, como para:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Aprovisione um dispositivo simulado.
> * Teste o dispositivo simulado.
> * Chame os métodos de dispositivos da solução.
> * Reconfigure um dispositivo.

Agora que aprendeu como gerir os seus dispositivos, os passos sugeridos são saber como:

* [Resolver problemas e resolver problemas de dispositivo](iot-suite-remote-monitoring-maintain.md).
* [Testar a sua solução com dispositivos simulados](iot-suite-remote-monitoring-test.md).
* [Ligar o seu dispositivo à solução pré-configurada de monitorização remota](iot-suite-connecting-devices-node.md).

<!-- Next tutorials in the sequence -->