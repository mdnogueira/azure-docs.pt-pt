<properties
 pageTitle="Solução pré-configurada de manutenção preditiva | Microsoft Azure"
 description="Uma descrição da solução pré-configurada de manutenção preditiva do Azure IoT."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="05/24/2016"
 ms.author="stevehob"/>

# Descrição geral da solução pré-configurada de manutenção preditiva

A solução pré-configurada de *manutenção preditiva* é uma das [soluções pré-configuradas][lnk_preconfigured_solutions] disponibilizadas como parte do [Microsoft Azure IoT Suite][lnk_iot_suite]. Esta solução integra a coleção de telemetria de dispositivos em tempo real com um modelo preditivo criado com o [Azure Machine Learning][lnk_machine_learning].


Com o Azure IoT Suite, as empresas podem rápida e facilmente estabelecer a ligação a e monitorizar recursos, bem como analisar dados em tempo real. A solução pré-configurada de manutenção preditiva assume esses dados e utiliza dashboards avançados e visualizações para fornecer uma nova inteligência às empresas que pode conduzir a eficiências e melhorar as fontes de receitas.

## O Cenário

A Fabrikam é uma companhia aérea regional que se concentra na satisfação do cliente a preços competitivos. Uma das causas dos atrasos dos voos está relacionada com problemas de manutenção, sendo a manutenção de motores de aeronaves uma tarefa particularmente difícil. Deve ser evitada, a todo o custo, qualquer falha do motor durante o voo. Por isso, a Fabrikam verifica frequentemente os seus motores e segue um programa de manutenção estabelecido. Contudo, o desgaste dos motores das aeronaves nem sempre é idêntico. É efetuada alguma manutenção desnecessária dos motores. Mais importante ainda, os problemas que surgirem podem manter uma aeronave em terra até ser efetuada a manutenção. Tal leva a atrasos dispendiosos, nomeadamente se não estiverem disponíveis técnicos ou peças sobressalentes corretas no local onde se encontrar a aeronave.

Os motores das aeronaves da Fabrikam são equipados com sensores que controlam o estado do motor durante o voo. A Fabrikam utiliza a Azure IoT Suite para recolher os dados que o sensor recolheu durante o voo. Depois de ter acumulado anos de dados sobre as falhas e dados operacionais sobre o motor, os cientistas de dados da Fabrikam modelaram uma maneira de prever a Vida Útil Remanescente (RUL) do motor de uma aeronave. Assim, identificaram uma correlação entre os dados de quatro sensores do motor com o desgaste do motor que pode eventualmente levar a uma falha. Enquanto a Fabrikam continua a fazer inspeções frequentes para garantir a segurança, pode utilizar os modelos para calcular o RUL de cada motor depois de cada voo utilizando a telemetria recolhida dos motores durante o voo. A Fabrikam pode agora prever futuros pontos de falha e planificar antecipadamente manutenções e reparações.

> [AZURE.NOTE] O modelo de solução utiliza os dados atuais de desgaste do motor.

Ao prever o momento em que é necessária a manutenção, a Fabrikam pode otimizar as suas operações para reduzir os custos. Os coordenadores da manutenção trabalham com os agendadores para planificar a manutenção aquando da paragem da aeronave num determinado local, garantindo um tempo de inutilização suficiente da aeronave sem causar quaisquer interrupções na agenda. A Fabrikam pode agendar com os técnicos em conformidade, garantindo que a manutenção das aeronaves é feita eficientemente sem tempo de espera. Os gestores de controlo de inventário recebem os planos de manutenção, para que possam otimizar o seu processo de encomenda e inventário de peças sobressalentes. Tudo isto permite à Fabrikam minimizar o período da aeronave em terra e reduzir os custos operacionais, garantindo simultaneamente a segurança dos passageiros e da tripulação.

Para compreender como [Azure IoT Suite][lnk_iot_suite] fornece as capacidades de que os clientes precisam para realizar o potencial da manutenção preditiva, reveja este [gráfico informativo][lnk_infographic].

## Como é criada a solução de manutenção preditiva

A solução tira partido de um modelo existente do Azure Machine Learning, disponível como um modelo para mostrar estas capacidades de trabalhar a partir da telemetria do dispositivo recolhida através dos serviços do IoT Suite. A Microsoft criou um [modelo de regressão][lnk_regression_model] de um motor de aeronave e publicou o modelo completo, os dados<sup>\[1\]</sup> e as orientações passo a passo sobre a utilização do modelo.

A solução pré-configurada de manutenção preditiva do Azure IoT utiliza o modelo de regressão criado a partir desse modelo; é implementada na sua subscrição do Azure e exposta através de uma API gerada automaticamente. A solução inclui um subconjunto dos dados de teste que representam 4 (em 100, no total) motores e os 4 (em 21, no total) fluxos de dados do sensor que fornecem um resultado preciso do modelo de preparação.

*\[1\] A. Saxena e K. Goebel (2008). "Turbofan Engine Degradation Simulation Data Set", NASA Ames Prognostics Data Repository (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), NASA Ames Research Center, Moffett Field, CA*

## Passos seguintes

Para saber mais sobre como o Azure IoT permite cenários de manutenção preditiva, leia o artigo [Valor de captura da Internet das Coisas][lnk_capture_value].

Siga as [instruções][Ink-predictive-walkthrough] da solução pré-configurada da manutenção preditiva.

[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-overview.md
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF

Também pode explorar algumas das outras funcionalidades e capacidades das soluções pré-configuradas do IoT Suite:

- [Perguntas mais frequentes sobre o IoT Suite][lnk-faq]
- [Segurança de IoT a partir do zero][lnk-security-groundup]

[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md


<!--HONumber=Aug16_HO1-->


