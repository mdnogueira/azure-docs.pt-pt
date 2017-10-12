---
title: "Descrição geral da distribuição de dispositivos com o Hub IoT do Azure | Microsoft Docs"
description: "Descrição geral da gestão de dispositivos no Hub IoT do Azure: ciclo de vida de dispositivos da empresa e padrões de gestão do dispositivo como reinício, reposição de fábrica, atualização de firmware, configuração, twins de dispositivo, consultas, tarefas."
services: iot-hub
documentationcenter: 
author: bzurcher
manager: timlt
editor: 
ms.assetid: a367e715-55f6-4593-bd68-7863cbf0eb81
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: briz
ms.openlocfilehash: 6d667d42bfef2ec61b055009210d5621f51c17df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-device-management-with-iot-hub"></a>Descrição geral da gestão de dispositivos com o Hub IoT
## <a name="introduction"></a>Introdução
O Hub IoT do Azure fornece as funcionalidades e um modelo de extensibilidade que permitem ao dispositivo e aos programadores de back-end criarem soluções de gestão de dispositivos robustas. Os dispositivos variam desde sensores restritos e microcontroladores com um objetivo único, a gateways poderosos que encaminham as comunicações para grupos de dispositivos.  Além disso, os casos de utilização e os requisitos para operadores de IoT variam significativamente entre os setores.  Apesar desta variação, a gestão de dispositivos com o Hub IoT fornece as capacidades, os padrões e as bibliotecas de código para atender um conjunto diverso de dispositivos e utilizadores finais.

Uma parte fundamental da criação de uma solução de IoT empresarial bem-sucedida é fornecer uma estratégia para a forma como os operadores vão processar a gestão contínua da coleção de dispositivos. Os operadores de IoT requerem ferramentas simples e fiáveis e aplicações e que lhes permitam concentrar-se em aspetos mais estratégicos das funções deles. Este artigo fornece:

* Uma breve descrição geral da abordagem do Hub IoT perante a gestão de dispositivos.
* Uma descrição dos princípios de gestão de dispositivos comuns.
* Uma descrição do ciclo de vida do dispositivo.
* Uma descrição geral dos padrões de gestão de dispositivos comuns.

## <a name="device-management-principles"></a>Princípios da gestão de dispositivos
O IoT inclui uma série de desafios de gestão de dispositivos únicos e qualquer solução de classe empresarial tem de abordar os seguintes princípios:

![Gráfico dos princípios da gestão de dispositivos][img-dm_principles]

* **Escala e automatização**: as soluções IoT requerem ferramentas simples que consigam automatizar tarefas de rotina e permitam que uma equipa de operações relativamente pequena faça a gestão de milhões de dispositivos. Todos os dias, os operadores esperam processar operações com dispositivos remotamente, em massa, e serem alertados apenas quando ocorrem problemas que precisam da atenção direta deles.
* **Acessibilidade e compatibilidade**: o ecossistema de dispositivos é extremamente diversificado. As ferramentas de gestão têm de ser adaptadas, de modo a acomodarem uma multitude de classes de dispositivos, plataformas e protocolos. Os operadores têm de conseguir suportar muitos tipos de dispositivos, desde os mais restritos chips de processamento único incorporados a computadores poderosos e totalmente funcionais.
* **Perceção do contexto**: os ambientes de IoT são dinâmicos e estão em constante mudança. A fiabilidade do serviço é crucial. As operações de gestão de dispositivos devem ter em consideração os seguintes fatores para assegurar que esse período de indisponibilidade devido a manutenção não afeta operações empresariais críticas nem cria condições perigosas:
    * Janelas de manutenção de SLA
    * Estados de rede e de energia
    * Condições em utilização
    * Geolocalização de dispositivos
* **Dar assistência a muitas funções**: o suporte para os fluxos de trabalho e os processos exclusivos das funções de operações de IoT é crucial. A equipa de operações tem de trabalhar em harmonia com as restrições dadas de departamentos de TI internos.  Ela também tem de encontrar formas sustentáveis de fornecer informações de operações de dispositivos em tempo real a supervisores e outras funções de gestão empresarial.

## <a name="device-lifecycle"></a>Ciclo de vida dos dispositivos
Existe um conjunto de fases de gestão de dispositivos geral que são comuns a todos os projetos de IoT empresarial. No Azure IoT, existem cinco fases do ciclo de vida dos dispositivos:

![As cinco fases do ciclo de vida de dispositivos do Azure IoT: planear, aprovisionar, configurar, monitorizar, extinguir][img-device_lifecycle]

Dentro de cada uma destas cinco fases, existem vários requisitos de operador de dispositivo que devem ser cumpridos para disponibilizar uma solução completa:

* **Planear**: permitir aos operadores criarem um esquema de metadados de dispositivo que lhes possibilite, de forma fácil e precisa, consultarem e segmentarem um grupo de dispositivos para operações de gestão em massa. Pode utilizar os twins do dispositivo para armazenar metadados deste dispositivo sob a forma de etiquetas e propriedades.
  
    *Leitura adicional*: [Introdução aos dispositivos duplos][lnk-twins-getstarted], [Compreender os dispositivos duplos][lnk-twins-devguide], [Como utilizar as propriedades dos dispositivos duplos][lnk-twin-properties].
* **Aprovisionar**: aprovisionar em segurança dispositivos novos no Hub IoT e permitir aos operadores descobrir imediatamente as capacidades dos dispositivos.  Utilize o registo de dispositivos do Hub para criar identidades e credenciais de dispositivos flexíveis e efetuar esta operação em massa através de uma tarefa. Crie dispositivos para comunicar as respetivas capacidades e condições através de propriedades dos dispositivos no twin do dispositivo.
  
    *Leitura adicional*: [Gerir identidades de dispositivos][lnk-identity-registry], [Gestão em massa de identidades de dispositivos][lnk-bulk-identity], [Como utilizar as propriedades dos dispositivos duplos][lnk-twin-properties].
* **Configurar**: facilitar alterações de configuração e atualizações de firmware em massa nos dispositivos, preservando o estado de funcionamento e as segurança. Efetue estas operações de gestão de dispositivos em massa com as propriedades pretendidas ou com métodos diretos e tarefas de difusão.
  
    *Leitura adicional*: [Utilizar métodos diretos][lnk-c2d-methods], [Invocar um método direto num dispositivo][lnk-methods-devguide], [Como utilizar as propriedades dos dispositivos duplos][lnk-twin-properties], [Agendar e transmitir tarefas][lnk-jobs], [Agendar tarefas em vários dispositivos][lnk-jobs-devguide].
* **Monitorizar**: monitorizar o estado de funcionamento geral da coleção de dispositivos, o estado de operações contínuas e alertar os operadores relativamente a problemas que possam precisar da atenção deles.  Aplique o twin do dispositivo para permitir aos dispositivos comunicarem as condições de funcionamento em tempo real e o estado das operações de atualização. Crie relatórios de dashboards eficientes que emitem os problemas mais imediatos com consultas twin do dispositivo.
  
    *Leitura adicional*: [Como utilizar as propriedades dos dispositivos duplos][lnk-twin-properties], [Linguagem de consulta do Hub IoT para dispositivos duplos, tarefas e encaminhamento de mensagens][lnk-query-language].
* **Extinguir**: substituir ou desativar dispositivos após uma falha, ciclo de atualização ou no fim do ciclo de vida do serviço.  Utilize o twin do dispositivo para manter informações do dispositivo se o dispositivo físico estiver a ser substituído, ou arquivar se estiver a ser retirado. Utilize o registo de identidades do Hub IoT para revogar de forma segura identidades e credenciais de dispositivos.
  
    *Leitura adicional*: [Como utilizar as propriedades dos dispositivos duplos][lnk-twin-properties], [Gerir identidades dos dispositivos][lnk-identity-registry].

## <a name="device-management-patterns"></a>Padrões da gestão de dispositivos
O Hub IoT permite o conjunto de padrões de gestão de dispositivos seguinte.  Os [tutoriais da gestão de dispositivos][lnk-get-started] mostram-lhe mais detalhadamente como pode expandir estes padrões para se ajustarem ao seu cenário exato e como desenhar novos padrões com base nestes modelos de núcleo.

* **Reiniciar** - a aplicação de back-end informa o dispositivo, através de um método que iniciou um reinício.  O dispositivo utiliza as propriedades reportadas no para atualizar o respetivo estado de reinício.
  
    ![Gráfico do padrão de reinício de gestão de dispositivos][img-reboot_pattern]
* **Reposição de Fábrica** - a aplicação de back-end informa o dispositivo, através de um método direto, de que iniciou uma reposição de fábrica.  O dispositivo utiliza as propriedades reportadas para atualizar o respetivo estado da reposição de fábrica.
  
    ![Gráfico do padrão de reposição de fábrica de gestão de dispositivos][img-facreset_pattern]
* **Configuração** - a aplicação de back-end utiliza as propriedades pretendidas para configurar o software em execução no dispositivo.  O dispositivo utiliza as propriedades reportadas para atualizar o respetivo estado de configuração.
  
    ![Gráfico do padrão de configuração de gestão de dispositivos][img-config_pattern]
* **Atualização de Firmware** - a aplicação de back-end informa o dispositivo, através de um método direto, de que iniciou uma atualização de firmware.  O dispositivo inicia um processo de vários passos para transferir a imagem de firmware, aplicá-la e, por último, voltar a ligar ao serviço Hub IoT.  Ao longo do processo de vários passos, o dispositivo utiliza as propriedades reportadas para atualizar o respetivo progresso e estado.
  
    ![Gráfico do padrão de atualização de firmware de gestão de dispositivos][img-fwupdate_pattern]
* **Relatório de estado e progresso** - o back-end da solução executa consultas de “device twin”, num conjunto de dispositivos, para comunicar o estado e o progresso das ações em execução nos dispositivos.
  
    ![Gráfico do padrão de estado e progresso dos relatórios de gestão de dispositivos][img-report_progress_pattern]

## <a name="next-steps"></a>Passos Seguintes
As capacidades, padrões e bibliotecas de código que o Hub IoT fornece para gestão de dispositivos permitem criar aplicações IoT que satisfazem os requisitos do operador de IoT empresarial em cada fase do ciclo de vida do dispositivo.

Para continuar a saber mais sobre as funcionalidades de gestão de dispositivos no Hub IoT, veja o tutorial [Get started with device management (Introdução à gestão de dispositivos)][lnk-get-started].

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-twins-devguide]: iot-hub-devguide-device-twins.md
[lnk-get-started]: iot-hub-node-node-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-hub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-query-language]: iot-hub-devguide-query-language.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-methods-devguide]: iot-hub-devguide-direct-methods.md
[lnk-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-jobs-devguide]: iot-hub-devguide-jobs.md
