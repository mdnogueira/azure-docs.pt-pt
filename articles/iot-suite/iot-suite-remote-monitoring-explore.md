---
title: "Introdução à solução de monitorização remota - Azure | Microsoft Docs"
description: "Este tutorial utiliza cenários simulados para apresentar a solução pré-configurada de monitorização remota. Estes cenários são criados quando implementar a solução pré-configurada de monitorização remota pela primeira vez."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 09/16/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 222050410bfc809991f03a452eb6640e5f9e1802
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="explore-the-capabilities-of-the-remote-monitoring-preconfigured-solution"></a>Explorar as funcionalidades da solução pré-configurada de monitorização remota

Este tutorial mostra as principais capacidades de solução de monitorização remota. Para apresentar estas capacidades, o tutorial showcases cenários comuns do cliente, utilizando uma aplicação de IoT simulada para uma empresa denominada Contoso.

O tutorial ajuda-o a compreende os cenários típicos de IoT a solução de monitorização remota fornece out of box.

Neste tutorial, ficará a saber como:

>[!div class="checklist"]
> * Visualizar e filtrar dispositivos no dashboard
> * Responder a um alarme
> * Atualizar o firmware nos seus dispositivos
> * Organizar os elementos

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma instância implementada da solução de monitorização remota na sua subscrição do Azure.

Se ainda não implementado a solução de monitorização remota ainda, deve efetuar o [implementar a solução pré-configurada de monitorização remota](iot-suite-remote-monitoring-deploy.md) tutorial.

## <a name="the-contoso-sample-iot-deployment"></a>A implementação de IoT de exemplo do Contoso

Pode utilizar a implementação de IoT de exemplo de Contoso para compreender os cenários básicos remoto solução de monitorização fornece out of box. Estes cenários são baseados em implementações de IoT reais. Provavelmente, irá escolher Personalizar a solução de monitorização remota para satisfazer os seus requisitos específicos, mas o exemplo de Contoso ajuda-o a aprender as noções básicas.

> [!NOTE]
> Se utilizou a CLI para implementar a solução pré-configurada, o ficheiro `deployment-{your deployment name}-output.json` contém informações sobre a implementação, tais como o URL para accss o exemplo implementado.

O exemplo de Contoso aprovisiona um conjunto de dispositivos simulados e regras para agir nos mesmos. Assim que compreender os cenários básicos, pode continuar a explorar mais funcionalidades solução [executar avançada de monitorização de dispositivos com a solução de monitorização remota](iot-suite-remote-monitoring-monitor.md).

Contoso é uma empresa que gere uma variedade de recursos em ambientes diferentes. Contoso planeia utilizar a capacidade das aplicações de IoT baseada na nuvem para monitorizar e gerir vários recursos a partir de uma aplicação centralizada remotamente. As secções seguintes fornecem um resumo da configuração inicial do exemplo Contoso:

> [!NOTE]
> Demonstração de Contoso é apenas uma forma para aprovisionar dispositivos simulados e criar regras. Outras opções de aprovisionamento incluem a criação dos seus próprios dispositivos personalizados. Para obter mais informações sobre como criar os seus próprios dispositivos e as regras, consulte [gerir e configurar os seus dispositivos](iot-suite-remote-monitoring-manage.md) e [detetar problemas através de regras baseadas em limiares](iot-suite-remote-monitoring-automate.md).

### <a name="contoso-devices"></a>Dispositivos de contoso

A Contoso utiliza diferentes tipos de dispositivos inteligentes. Estes dispositivos satisfazer diferentes funções da empresa, enviar vários fluxos de telemetria. Além disso, cada tipo de dispositivo tem propriedades diferentes de dispositivos e suportados métodos.

A tabela seguinte mostra um resumo dos tipos de dispositivo aprovisionada:

| Tipo de dispositivo        | Telemetria                                  | Propriedades                                  | Etiquetas                    | Métodos                                                                                      |
| ------------------ | ------------------------------------------ | ------------------------------------------- | ----------------------- | -------------------------------------------------------------------------------------------- |
| Chiller            | Pressão de temperatura, humidade,            | Tipo, versão de Firmware, de modelo               | Localização, piso, Campus | Reiniciar o computador, pressão de aumento de atualização, versão de emergência Valve, de Firmware                          |
| Dispositivo de fazer o protótipo | Temperatura, pressão, geolocalização        | Tipo, versão de Firmware, de modelo               | Localização, modo          | Reiniciar o computador, atualização de Firmware, o dispositivo de movimentação, paragem dispositivo, versão de temperatura, aumento de temperatura |
| Motor             | Tank fuel nível, Coolant sensor, Vibration | Tipo, versão de Firmware, de modelo               | Localização, piso, Campus | Reiniciar, atualização de Firmware, tank vazio, tank de preenchimento                                              |
| Camião              | Temperatura de carga de geolocalização, velocidade,     | Tipo, versão de Firmware, de modelo               | Localização de carga          | Inferior temperatura de carga, a temperatura do aumento de carga, a atualização de Firmware                         |
| Abordagem           | Piso, Vibration, temperatura              | Tipo, versão de Firmware, o modelo, geolocalização | Localização, Campus        | Abordagem de parar, a abordagem de início, a atualização de Firmware                                               |

> [!NOTE]
> Os Contoso demonstração Aprovisiona dois dispositivos de exemplo por tipo. Para cada tipo, um funciona corretamente dentro dos limites definidos como normal Contoso e um tem algum tipo de funcionar incorretamente ocorrer sistematicamente. Na secção seguinte, pode obter informações sobre as regras que Contoso define para os dispositivos. Estas regras definem os limites de comportamento correto.

### <a name="contoso-rules"></a>Regras de contoso

Operadores contoso sabe os limiares que determinam se um dispositivo está a funcionar corretamente. Por exemplo, um chiller não está a funcionar corretamente se o pressão esta comunica for superior a 250 PSI. A tabela seguinte mostra as regras baseadas em limiares que Contoso define para cada tipo de dispositivo:

| Nome da Regra | Descrição | Limiar | Gravidade | Dispositivos afetados |
| --------- | ----------- | --------- | -------- | ---------------- |
| Pressão chiller demasiado elevada | Alerta se chillers alcançar superior do que os níveis de pressão normal   |P > 250 psi       | Crítico | Chillers            |
| Temp de dispositivo de fazer o protótipo demasiado elevada  | Alerta se os dispositivos de fazer o protótipo alcancem superior do que os níveis de temperatura normal  |T > 80&deg; F |Crítico | Dispositivos de fazer o protótipo |
| Tank motor vazio  | Alertas se o motor fuel tank ficar vazio                     | F < 5 gallons | Informações     | Motores             |
| Superior a temperatura de carga normal | Alerta se a temperatura de carga do camião é superior à normal                 | T < 45&deg; F |Aviso  | Trucks              |
| Vibration abordagem parado      | Alertas se abordagem parar completamente (com base no nível de vibration)                     | V < 0.1 mm |Aviso  | Elevators           |

### <a name="operate-the-contoso-sample-deployment"></a>Operar a implementação de exemplo do Contoso

Constatou agora a configuração inicial do exemplo Contoso. As secções seguintes descrevem os três cenários de exemplo da Contoso que ilustram como um operador pode utilizar a solução pré-configurada.

## <a name="respond-to-a-pressure-alarm"></a>Responder a um alarme pressão

Este cenário mostra como identificar e responder a um alarme que é acionado por um dispositivo chiller. O chiller está localizado em Redmond, na compilação 43, piso 2.

Como um operador, vê no dashboard que há um alarme relacionadas com a pressão de um chiller. Pode efetuar um deslocamento panorâmico e zoom no mapa para ver mais detalhes.

1. No **Dashboard** na página a **sistema alarmes** grelha, pode ver o **pressão Chiller demasiado elevada** alarme. O chiller também é realçado no mapa:

    ![Dashboard mostra alarme pressão e dispositivos no mapa](media/iot-suite-remote-monitoring-explore/dashboardalarm.png)

1. Para ver os detalhes do dispositivo e a telemetria, clique o chiller realçado no mapa. A telemetria mostra um pico de pedidos de pressão:

    ![Escolha o dispositivo no mapa para ver detalhes](media/iot-suite-remote-monitoring-explore/dashboarddetail.png)

1. Fechar **detalhes do dispositivo**.

1. Para navegar para o **manutenção** página, clique em **...**  no **explorar alarme** coluna junto ao alarme na grelha alarme.

No **manutenção** página, pode ver os detalhes da regra que acionou o alarme de pressão chiller.

1. Pode ver o número de vezes que o alarme foi acionada, em que as confirmações e alarmes abertos e fechados:

    ![Página de manutenção mostra a lista de alarmes ter acionado](media/iot-suite-remote-monitoring-explore/maintenancealarmlist.png)

1. O alarme primeiro na lista é a mais recente. Clique em de **Chiller pressão** alarme para ver a telemetria e de dispositivos associados. A telemetria mostra um pico de pedidos de pressão para o chiller:

    ![Página de manutenção mostra a telemetria alarme selecionado](media/iot-suite-remote-monitoring-explore/maintenancetelemetry.png)

Agora ter identificado o problema que acionou o alarme e o dispositivo associado. Como um operador, os passos seguintes são reconhecer o alarme e mitigar o problema.

1. Para indicar que está a trabalhar agora no alarme, altere o **alarme estado** para **confirmado**:

    ![Selecione e confirmar o alarme](media/iot-suite-remote-monitoring-explore/maintenanceacknowledge.png)

1. Para agir sobre o chiller, selecione-o e, em seguida, escolha **agenda**. Selecione **EmergencyValveRelease**, adicionar um nome de tarefa **ChillerPressureRelease**e escolha **aplicar**. Estas definições de criar uma tarefa que é executada imediatamente:

    ![Selecione o dispositivo e agendar uma ação](media/iot-suite-remote-monitoring-explore/maintenanceschedule.png)

1. Para ver o estado da tarefa, voltar para o **manutenção** e ver a lista de tarefas na página a **estado do sistema** vista. Pode ver que a tarefa foi executada para libertar o pressão valve no chiller:

    ![O estado das tarefas de estado do sistema](media/iot-suite-remote-monitoring-explore/maintenancerunningjob.png)

Por fim, confirme que os valores de telemetria do chiller são novamente para normal.

1. Para ver a grelha de alarmes, navegue para o **Dashboard** página.

1. Para ver a telemetria do dispositivo, selecione o dispositivo para o alarme original no mapa e confirme que é a normal.

1. Para fechar o incidente, navegue para o **manutenção** página, selecione de alarme e definir o estado **fechado**:

    ![Selecione e fechar o alarme](media/iot-suite-remote-monitoring-explore/maintenanceclose.png)

## <a name="update-device-firmware"></a>Atualizar o firmware do dispositivo

Contoso é testar um novo tipo de dispositivo no campo. Como parte do ciclo de teste, tem de garantir que firmware do dispositivo de atualizações de trabalho corretamente. Os passos seguintes mostram como utilizar a solução de monitorização remota para atualizar o firmware em vários dispositivos.

Para efetuar as tarefas de gestão de dispositivo necessários, utilize o **dispositivos** página. Comece por filtrar para todos os dispositivos de fazer o protótipo:

1. Navegue para o **dispositivos** página. Escolha o **dispositivos de fazer o protótipo** filtrar no **filtros** pendente:

    ![Utilize um filtro na página de dispositivos](media/iot-suite-remote-monitoring-explore/devicesprototypingfilter.png)

    > [!TIP]
    > Clique em **gerir** para gerir os filtros disponíveis.

1. Selecione um dos dispositivos de fazer o protótipo:

    ![Selecione um dispositivo na página de dispositivos](media/iot-suite-remote-monitoring-explore/devicesselect.png)

1. Clique em de **agenda** botão e, em seguida, escolha **atualização de Firmware**. Introduza os valores para **nome da tarefa** e **URI de Firmware**. Escolha **aplicar** para agendar a tarefa para executar agora:

    ![Agendar a atualização de firmware no dispositivo](media/iot-suite-remote-monitoring-explore/devicesschedulefirmware.png)

    > [!NOTE]
    > Com os dispositivos simulados pode utilizar qualquer URL que quiser como o **Firemware URI** valor. Os dispositivos simulados não aceder ao URL.

1. Tenha em atenção o número de dispositivos afeta a tarefa e escolha **aplicar**:

    ![Submeter a tarefa de atualização de firmware](media/iot-suite-remote-monitoring-explore/devicessubmitupdate.png)

Pode utilizar o **manutenção** página para controlar a tarefa que é executada.

1. Para ver a lista de tarefas, navegue para o **manutenção** página e clique em **estado do sistema**.

1. Localize os eventos relacionados com a tarefa que criou. Certifique-se de que o processo de atualização de firmware foi iniciado corretamente.

Pode criar um filtro para verificar a atualização da versão de firmware corretamente.

1. Para criar um filtro, navegue para o **dispositivos** página e selecione **gerir**:

    ![Gerir filtros de dispositivo](media/iot-suite-remote-monitoring-explore/devicesmanagefilters.png)

1. Crie um filtro que inclui apenas os dispositivos com a nova versão de firmware:

    ![Criar o filtro do dispositivo](media/iot-suite-remote-monitoring-explore/devicescreatefilter.png)

1. Volte à **dispositivos** Certifique-se de que o dispositivo tem a nova versão de firmware e da página.

## <a name="organize-your-assets"></a>Organizar os elementos

Contoso tem duas equipas diferentes para as atividades do serviço de campo:

* A equipa de criação de Smart gere chillers, elevators e motores.
* A equipa de Vehicle inteligente gere trucks e dispositivos de fazer o protótipo.

Para facilitar como um operador para organizar e gerir os seus dispositivos, quer marcá-los com o nome do agrupamento adequado.

Pode criar os nomes de etiqueta para utilizar com dispositivos.

1. Para apresentar todos os dispositivos, navegue para o **dispositivos** página e escolha o **todos os dispositivos** filtro:

    ![Mostrar todos os dispositivos](media/iot-suite-remote-monitoring-explore/devicesalldevices.png)

1. Selecione o **Trucks** e **fazer o protótipo** dispositivos. Em seguida, escolha **Tag**:

    ![Selecione os dispositivos protótipo e camião](media/iot-suite-remote-monitoring-explore/devicesmultiselect.png)

1. Escolha **Tag** e, em seguida, crie uma nova etiqueta de cadeia denominada **FieldService** com um valor **ConnectedVehicle**. Escolha um nome para a tarefa. Em seguida, clique em **aplicar**:

    ![Adicionar etiquetas a dispositivos protótipo e camião](media/iot-suite-remote-monitoring-explore/devicesaddtag.png)

1. Selecione o **Chiller**, **abordagem**, e **motor** dispositivos. Em seguida, escolha **Tag**:

    ![Selecione dispositivos chiller, motor e a abordagem](media/iot-suite-remote-monitoring-explore/devicesmultiselect2.png)

1. Escolha **Tag** e, em seguida, crie uma nova etiqueta chamada **FieldService** com um valor **SmartBuilding**. Escolha um nome para a tarefa. Em seguida, clique em **guardar**:

    ![Adicionar etiquetas ao chiller, motor e a abordagem de dispositivos](media/iot-suite-remote-monitoring-explore/devicesaddtag2.png)

Pode utilizar os valores de etiqueta para criar filtros.

1. No **dispositivos** página, escolha **gerir filtros**:

    ![Gerir filtros de dispositivo](media/iot-suite-remote-monitoring-explore/devicesmanagefilters.png)

1. Criar um novo filtro que utiliza o nome de etiqueta **FieldService** e valor **SmartBuilding**. Guardar o filtro como **Smart edifício**.

1. Criar um novo filtro que utiliza o nome de etiqueta **FieldService** e valor **ConnectedVehicle**. Guardar o filtro como **ligado Vehicle**.

Agora, o operador de Contoso pode consultar dispositivos com base na equipa de sistema operativo sem a necessidade de alterar nada nos dispositivos.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a:

>[!div class="checklist"]
> * Visualizar e filtrar dispositivos no dashboard
> * Responder a um alarme
> * Atualizar o firmware nos seus dispositivos
> * Organizar os elementos

Agora que tem explorou a solução de monitorização remota, se os passos sugeridos saber mais sobre as funcionalidades avançadas de solução de monitorização remota:

* [Monitorizar os seus dispositivos](./iot-suite-remote-monitoring-monitor.md).
* [Gerir os seus dispositivos](./iot-suite-remote-monitoring-manage.md).
* [Automatizar a sua solução com regras](./iot-suite-remote-monitoring-automate.md).
* [Manter a sua solução](./iot-suite-remote-monitoring-maintain.md).
