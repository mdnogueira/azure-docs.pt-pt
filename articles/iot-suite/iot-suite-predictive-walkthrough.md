---
title: Instruções de manutenção preditiva | Microsoft Docs
description: Instruções sobre a solução pré-configurada de manutenção preditiva do Azure IoT.
services: ''
suite: iot-suite
documentationcenter: ''
author: aguilaaj
manager: timlt
editor: ''

ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: araguila

---
# Instruções sobre a solução pré-configurada de manutenção preditiva
## Introdução
A solução pré-configurada de manutenção preditiva do IoT Suite é uma solução ponto-a-ponto para um cenário de negócio que prevê o momento em que poderá ocorrer uma falha. Pode utilizar, de forma pró-ativa, esta solução pré-configurada para atividades como a manutenção de otimização. A solução combina os serviços principais do Azure IoT Suite, incluindo uma área de trabalho [Azure Machine Learning][lnk_machine_learning]. Esta área de trabalho contém experimentações, com base num conjunto de dados de exemplo público, para prever a Vida Útil Remanescente (RUL) de um motor de aeronave. A solução implementa completamente o cenário empresarial IoT como um ponto de partida para que possa planear e implementar uma solução que satisfaça os seus próprios requisitos de negócio.

## Arquitetura lógica
O diagrama que se segue descreve os componentes lógicos da solução pré-configurada:

![][img-architecture]

Os itens a azul são os serviços do Azure aprovisionados na localização que selecionou aquando do aprovisionamento da solução pré-configurada. Pode aprovisionar a solução para as regiões EUA Leste, Europa do Norte ou Ásia Oriental.

Alguns recursos não estão disponíveis nas regiões onde aprovisiona a solução pré-configurada. Os itens a laranja no diagrama representam os serviços do Azure aprovisionados na região disponível mais próxima (E.U.A. Centro-Sul, Europa Ocidental ou Sudeste Asiático) tendo em conta a região selecionada.

O item a verde é um dispositivo simulado que representa o motor de uma aeronave. Pode saber mais sobre estes dispositivos simulados na secção seguinte.

Os itens a cinzento representam componentes que implementam capacidades de *administração do dispositivo*. A atual versão da solução pré-configurada de manutenção preditiva não aprovisiona estes recursos. Para saber mais sobre a administração do dispositivo, consulte o artigo [solução pré-configurada de monitorização remota][lnk-remote-monitoring].

## Dispositivos simulados
Na solução pré-configurada, um dispositivo simulado representa o motor de uma aeronave. A solução é aprovisionada com dois motores que mapeiam para uma única aeronave. Cada motor emite quatro tipos de telemetria: Sensor 9, Sensor 11, Sensor 14 e Sensor 15, que fornecem os dados necessários para que o modelo do Machine Learning possa calcular a Vida Útil Remanescente (RUL) desse motor. Cada dispositivo simulado envia as seguintes mensagens de telemetria ao IoT Hub:

*Ciclo de contagem*. Um ciclo representa um voo concluído com duração variável entre 2 e 10 horas, no qual são capturados dados de telemetria a cada meia hora durante o voo.

*Telemetria*. Existem quatro sensores que representam os atributos do motor. Os sensores são geralmente denominados Sensor 9, Sensor 11, Sensor 14 e Sensor 15. Estes 4 sensores representam a telemetria suficiente para obter resultados úteis do modelo do Machine Learning para a RUL. Este modelo é criado a partir de um conjunto de dados públicos, que inclui dados do sensor do motor. Para obter mais informações sobre a criação do modelo a partir do conjunto de dados original, consulte o artigo [Modelo de Manutenção Preditiva do Cortana Intelligence Gallery][lnk-cortana-analytics].

Os dispositivos simulados podem processar os seguintes comandos enviados a partir de um IoT Hub:

| Comando | Descrição |
| --- | --- |
| StartTelemetry |Controla o estado da simulação.<br/>Inicia o dispositivo que envia a telemetria |
| StopTelemetry |Controla o estado da simulação.<br/>Para o dispositivo que envia a telemetria |

O IoT Hub reconhece o comando do dispositivo.

## Tarefa do Azure Stream Analytics
**Tarefa: Telemetria** intervém no fluxo de telemetria de entrada do dispositivo utilizando duas instruções. A primeira seleciona todas as telemetrias dos dispositivos e envia esses dados ao Blob Storage, onde serão visualizados na aplicação Web. A segunda instrução calcula os valores médios do sensor numa janela deslizante e envia esses dados através do Event Hub a um **processador de eventos**.

## Processador de eventos
O **processador de eventos** obtém os valores médios do sensor para um ciclo concluído. Transmite esses valores para uma API que expõe o modelo formado do Machine Learning para calcular a RUL de um motor.

## Azure Machine Learning
Para obter mais informações sobre a criação do modelo a partir do conjunto de dados original, consulte o artigo [Modelo de Manutenção Preditiva do Cortana Intelligence Gallery][lnk-cortana-analytics].

## Comecemos o nosso percurso
Esta secção explica-lhe os componentes da solução, descreve o caso de utilização prevista e fornece exemplos.

### Dashboard de Manutenção Preventiva
Esta página na aplicação Web utiliza os controlos do PowerBI JavaScript (consulte o [repositório PoweBI-visuals][Ink powerbi]) para visualizar:

* Os dados de saída das tarefas do Stream Analytics no Blob Storage.
* A RUL e contagem de ciclo por motor de aeronave.

### Observar o comportamento da solução na nuvem
No Portal do Azure, navegue para o grupo de recursos com o nome da solução que escolheu para ver os recursos aprovisionados.

![][img-resource-group]

Quando aprovisiona a solução pré-configurada, recebe um e-mail com uma ligação para a área de trabalho do Machine Learning. Pode ainda navegar para a área de trabalho do Machine Learning a partir da página [azureiotsuite.com][lnk-azureiotsuite] para a sua solução aprovisionada quando estiver no estado **Pronto**.

![][img-machine-learning]

No portal de solução, pode ver que o exemplo é aprovisionado com quatro dispositivos simulados para representar duas aeronaves com dois motores por aeronave, cada um com quatro sensores. Quando navega pela primeira vez no portal de solução, a simulação é interrompida.

![][img-simulation-stopped]

Clique em **Iniciar simulação** para começar a simulação onde verá o histórico do sensor, a RUL, os Ciclos e o histórico da RUL a preencher o dashboard.

![][img-simulation-running]

Quando a RUL for inferior a 160 (limiar arbitrário escolhido para fins de demonstração), o portal de solução apresentará um símbolo de aviso junto da apresentação da RUL e o motor da aeronave aparecerá realçado a amarelo. Repare como os valores da RUL têm uma tendência descendente geral, tendendo no entanto a saltar para cima e para baixo. Este comportamento decorre das durações variáveis dos ciclos e da precisão do modelo.

![][img-simulation-warning]

A simulação completa demora cerca de 35 minutos a concluir 148 ciclos. O limiar da RUL 160 é atingido pela primeira vez em cerca de 5 minutos e ambos os motores atingem o limiar em cerca de 8 minutos.

A simulação é executada através do conjunto de dados completos para 148 ciclos e assenta nos valores da RUL final e ciclo.

Pode parar a simulação a qualquer momento mas, se clicar em **Iniciar Simulação**, a simulação partirá novamente do início do conjunto de dados.

## Passos seguintes
Agora que já executou a solução pré-configurada de manutenção preditiva, poderá querer modificá-la. Para tal, consulte [Orientações sobre a personalização de soluções pré-configuradas][lnk-customize].

A mensagem de blogue [Manutenção Preditiva - Sob Definições Avançadas - IoT Suite](http://social.technet.microsoft.com/wiki/contents/articles/33527.iot-suite-under-the-hood-predictive-maintenance.aspx) de TechNet fornece detalhes adicionais sobre a solução pré-configurada de manutenção preditiva.

Também pode explorar algumas das outras funcionalidades e capacidades das soluções pré-configuradas do IoT Suite:

* [Perguntas mais frequentes sobre o IoT Suite][lnk-faq]
* [Segurança de IoT a partir do zero][lnk-security-groundup]

[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-suite-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-suite-predictive-walkthrough/machine-learning.png
[img-simulation-stopped]: media/iot-suite-predictive-walkthrough/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-walkthrough/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-walkthrough/simulation-warning.png

[Ink powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md



<!--HONumber=Sep16_HO3-->


