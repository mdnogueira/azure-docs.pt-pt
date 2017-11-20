---
title: "Descrição geral da solução de manutenção preditiva - Azure | Microsoft Docs"
description: "Uma descrição da solução pré-configurada de manutenção preditiva do Azure IoT Suite."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: b370b3d7-2ce5-4906-9818-3aeedd471ee3
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 36cae39b7eaa0aff5f47f6a2511c7a0593f70b26
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="predictive-maintenance-preconfigured-solution-overview"></a>Descrição geral da solução pré-configurada de manutenção preditiva

A *solução pré-configurada* de [manutenção preditiva][lnk_preconfigured_solutions] é uma das soluções pré-configuradas do [Microsoft Azure IoT Suite][lnk_iot_suite]. Esta solução integra a coleção de telemetria de dispositivos em tempo real com um modelo preditivo criado com o [Azure Machine Learning][lnk-machine-learning].

Com o Azure IoT Suite, pode ligar e monitorizar rápida e facilmente recursos e analisar telemetria em tempo real em dashboards e visualizações. Na solução de manutenção preditiva, os dashboards e as visualizações proporcionam-lhe informações novas que podem conduzir a eficiências e melhorar as fontes de receitas.

## <a name="the-scenario"></a>O Cenário

A Fabrikam é uma companhia aérea regional que se concentra na satisfação do cliente a preços competitivos. Uma das causas dos atrasos dos voos está relacionada com problemas de manutenção, sendo a manutenção de motores de aeronaves uma tarefa particularmente difícil. A Fabrikam tem de evitar falhas de motores durante os voos custe o que custar, pelo que os inspeciona regularmente e agenda manutenções de acordo com um plano. Contudo, o desgaste dos motores das aeronaves nem sempre é idêntico. É efetuada alguma manutenção desnecessária dos motores. Mais importante ainda, os problemas que surgirem podem manter uma aeronave em terra até ser efetuada a manutenção. Se uma aeronave estiver numa localização em que não haja técnicos ou peças sobressalentes disponíveis, estes problemas podem ser particularmente dispendiosos.

Os motores das aeronaves da Fabrikam são equipados com sensores que controlam o estado do motor durante o voo. A Fabrikam utiliza a solução de manutenção preditiva para recolher os dados de sensor recolhidos durante o voo. Depois de ter acumulado anos de dados sobre as falhas e dados operacionais sobre o motor, os cientistas de dados da Fabrikam modelaram uma maneira de prever a Vida Útil Remanescente (RUL) do motor de uma aeronave. O módulo utiliza uma correlação entre os dados de quatro sensores do motor com o desgaste do motor que pode eventualmente levar a uma falha. Enquanto a Fabrikam continua a fazer inspeções frequentes para garantir a segurança, pode utilizar os modelos para calcular o RUL de cada motor depois de cada voo. O modelo utiliza a telemetria recolhida a partir de motores durante de voo. A Fabrikam pode agora prever futuros pontos de falha e planificar antecipadamente manutenções e reparações.

> [!NOTE]
> O modelo de solução utiliza os dados atuais de desgaste do motor.

Ao prever o momento em que é necessária a manutenção, a Fabrikam pode otimizar as suas operações para reduzir os custos.

Os coordenadores de manutenção trabalham com programadores para:

- Planear a manutenção para coincidir com a paragem de uma aeronave numa localização específica.
- Garantir que existe tempo suficiente para a aeronave estar fora de serviço sem causar uma interrupção na agenda.
- Para agendar com os técnicos para assegurar que a manutenção das aeronaves é feita eficientemente sem tempo de espera.

Os gestores de controlo de inventário recebem os planos de manutenção, para que possam otimizar o seu processo de encomenda e inventário de peças sobressalentes.

Estas atividades permitem à Fabrikam minimizar o período da aeronave em terra e reduzir os custos operacionais, garantindo, simultaneamente, a segurança dos passageiros e da tripulação.

Para compreender como [Azure IoT Suite][lnk_iot_suite] fornece as capacidades de que os clientes precisam para realizar o potencial da manutenção preditiva, reveja este [gráfico informativo][lnk_infographic].

## <a name="how-the-predictive-maintenance-solution-is-built"></a>Como é criada a solução de manutenção preditiva

A solução utiliza um modelo existente do Azure Machine Learning, disponível como um modelo para mostrar estas capacidades de trabalhar a partir da telemetria do dispositivo recolhida através dos serviços do IoT Suite. A Microsoft criou um [modelo de regressão][lnk_regression_model] de um motor de aeronave baseado nos dados publicamente disponíveis<sup>\[1\]</sup> e a documentação de orientação passo a passo sobre a utilização do modelo.

A solução de manutenção preditiva do Azure IoT utiliza o modelo de regressão criado a partir deste modelo. O modelo é implementado na sua subscrição do Azure e exposto através de uma API gerada automaticamente. A solução inclui um subconjunto dos dados de teste que representam 4 (em 100, no total) motores e os 4 (em 21, no total) fluxos de dados do sensor. Estes dados são suficientes para fornecer um resultado preciso do modelo treinado.

*\[1\] A. Saxena e K. Goebel (2008). "Turbofan Engine Degradation Simulation Data Set", NASA Ames Prognostics Data Repository (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), NASA Ames Research Center, Moffett Field, CA*

## <a name="get-started-with-predictive-maintenance"></a>Introdução à manutenção preditiva

Este tutorial mostra-lhe como aprovisionar a solução de manutenção preditiva. Também fornece orientações sobre as funcionalidades básicas da solução de monitorização preditiva. Pode aceder a muitas destas funcionalidades através do dashboard da solução pré-configurada que é implementado juntamente com a mesma.

Para concluir este tutorial, precisa de uma subscrição ativa do Azure.

> [!NOTE]
> Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Azure Free Trial (Avaliação Gratuita do Azure)][lnk_free_trial].

1. Inicie sessão em [azureiotsuite.com][lnk-azureiotsuite] com as credenciais da sua conta Azure e clique em **+** para criar uma solução.
1. Clique em **Selecionar** no mosaico **Manutenção preditiva**.
1. Introduza o **Nome da solução** para a sua solução pré-configurada de monitorização preditiva.
1. Selecione a **Região** e a **Subscrição** que pretende utilizar para aprovisionar a solução.
1. Clique em **Criar Solução** para iniciar o processo de aprovisionamento. Este processo ação demora vários minutos para ser executado.

### <a name="wait-for-the-provisioning-process-to-complete"></a>Aguarde até a conclusão do processo de aprovisionamento

1. Clique no mosaico da sua solução com o estado **Aprovisionamento**.
1. Tenha em atenção os **Estados de aprovisionamento** uma vez que os serviços do Azure estão implementados na sua subscrição do Azure.
1. Depois de ter concluído o aprovisionamento, o estado passa para **Pronto**.
1. Clique no mosaico para ver os detalhes da sua solução no painel da direita. Neste painel pode iniciar o dashboard de solução e aceder à área de trabalho do Machine Learning.

> [!NOTE]
> Se tiver problemas com a implementação da solução pré-configurada, consulte [Permissions on the azureiotsuite.com site (Permissões no site azureiotsuite.com)][lnk-permissions] e as [FAQ][lnk-faq]. Se os problemas persistirem, crie um pedido de serviço no [portal][lnk-portal].

Pretendia ver certos detalhes que não se encontram listados para a sua solução? Sugira funcionalidades no [User Voice](https://feedback.azure.com/forums/321918-azure-iot).

## <a name="view-the-solution"></a>Ver a solução

Esta secção orienta-o ao longo da IU da solução.

### <a name="predictive-maintenance-dashboard"></a>Dashboard de Manutenção Preventiva

Esta página na aplicação Web utiliza os controlos do PowerBI JavaScript (consulte o [repositório PowerBI-visuals][lnk-powerbi]) para visualizar:

* Os dados de saída das tarefas do Stream Analytics no Blob Storage.
* A RUL e contagem de ciclo por motor de aeronave.

### <a name="observing-the-behavior-of-the-cloud-solution"></a>Observar o comportamento da solução na nuvem

No Portal do Azure, navegue para o grupo de recursos com o nome da solução que escolheu para ver os recursos aprovisionados.

![][img-resource-group]

Quando aprovisiona a solução pré-configurada, recebe um e-mail com uma ligação para a área de trabalho do Machine Learning. Também pode navegar para a área de trabalho do Machine Learning a partir da página [azureiotsuite.com][lnk-azureiotsuite] da sua solução aprovisionada. Quando a solução estiver no estado **Pronto**, estará disponível um mosaico nesta página.

![][img-machine-learning]

No portal de solução, pode ver que o exemplo é aprovisionado com quatro dispositivos simulados para representar duas aeronaves com dois motores por aeronave, cada um com quatro sensores. Quando navega pela primeira vez no portal de solução, a simulação é interrompida.

![][img-simulation-stopped]

Clique em **Iniciar simulação** para começar a simulação. O dashboard é preenchido pelo histórico dos sensores, pela RUL, pelos Ciclos e pelo histórico da RUL.

![][img-simulation-running]

Quando a RUL for inferior a 160 (limiar arbitrário escolhido para fins de demonstração), o portal de solução apresenta um símbolo de aviso junto da apresentação da RU. O portal da solução também realça o motor de aeronave a amarelo. Repare como os valores da RUL têm uma tendência descendente geral, tendendo no entanto a saltar para cima e para baixo. Este comportamento decorre das durações variáveis dos ciclos e da precisão do modelo.

![][img-simulation-warning]

A simulação completa demora cerca de 35 minutos a concluir 148 ciclos. O limiar da RUL 160 é atingido pela primeira vez em cerca de 5 minutos e ambos os motores atingem o limiar em cerca de 8 minutos.

A simulação é executada através do conjunto de dados completos para 148 ciclos e assenta nos valores da RUL final e ciclo.

Pode parar a simulação a qualquer momento mas, se clicar em **Iniciar Simulação**, a simulação partirá novamente do início do conjunto de dados.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como o Azure IoT permite cenários de manutenção preditiva, leia o artigo [Capture value from the Internet of Things (Valor de captura da Internet das Coisas)][lnk_capture_value].

Siga as [instruções][lnk-predictive-walkthrough] da solução de manutenção preditiva.

Também pode explorar algumas das outras funcionalidades e capacidades das soluções pré-configuradas do IoT Suite:

* [Perguntas mais frequentes sobre o IoT Suite][lnk-faq]
* [Segurança de IoT desde o início][lnk-security-groundup]

[img-resource-group]: media/iot-suite-predictive-overview/resource-group.png
[img-simulation-stopped]: media/iot-suite-predictive-overview/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-overview/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-overview/simulation-warning.png
[img-machine-learning]: media/iot-suite-predictive-overview/machine-learning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-options.md
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3

[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF
[lnk-faq]: iot-suite-v1-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-portal]: http://portal.azure.com/
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/