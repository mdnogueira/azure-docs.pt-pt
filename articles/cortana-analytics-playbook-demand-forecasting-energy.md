---
title: "Manual de modelo do Cortana Intelligence solução para prever a pedido de energia | Microsoft Docs"
description: "Um modelo de solução com o Microsoft Cortana Intelligence que ajuda a prever a pedido para uma empresa de utilitário de energia."
services: cortana-analytics
documentationcenter: 
author: ilanr9
manager: ilanr9
editor: yijichen
ms.assetid: 8855dbb9-8543-45b9-b4c6-aa743a04d547
ms.service: cortana-analytics
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2016
ms.author: ilanr9;yijichen;garye
ms.openlocfilehash: 275e387878900154660d044b26ff5ac03a17a65a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="cortana-intelligence-solution-template-playbook-for-demand-forecasting-of-energy"></a>Manual de modelo do Cortana Intelligence solução para prever a pedido de energia
## <a name="executive-summary"></a>Resumo Executivo
Nos últimos anos alguns, Internet das coisas (IoT), as origens de energia alternativo e macrodados intercalado criar oportunidades vasta no domínio utilitário e energia. Ao mesmo tempo, o utilitário e o setor de energia completo tem visto consumo pelo com os consumidores pedir o seu trabalho melhor formas de controlar a utilização de energia. Por conseguinte, o utilitário e de empresas de grelha inteligente são no precisa excelente inovar e renovar próprios. Além disso, muitos grelhas de ativação e utilitários são tornar-se desatualizados e muito dispendioso manter e gerir. Durante o último ano, a equipa de tem estado a trabalhar num número de ações de envolvimento dentro do domínio de energia. Durante estas ações de envolvimento, encontrámos muitos casos em que os utilitários ou ISVs (os fabricantes independentes de Software) tem sido à procura para previsão para o pedido de energia futuras. Estes previsões desempenham um papel importante na respetiva atuais e futura da empresa e ficam foundation vários casos de utilização. Estes incluem previsão da carga de energia de curto e longo prazo, comerciais, balanceamento de carga, otimização de grelha etc. Macrodados e métodos de análise avançadas (AA) como o Machine Learning (ML) são os ativadores principais para produzir previsões exatas e fiáveis.  

Este manual de comunicação social, iremos juntar empresariais e diretrizes analíticas necessárias para o desenvolvimento com êxito e implementação de procura de energia previsão solução. Podem ajudar a estas diretrizes propostos utilitários, cientistas de dados e engenheiros de dados no estabelecer soluções totalmente operacionalizadas, baseado na nuvem, a pedido de previsão. Para as empresas que estão a iniciar os macrodados e journey análises avançadas, tal uma solução pode representar o seed inicial na respetiva estratégia a longo prazo grelha inteligente.

> [!TIP]
> Para transferir um diagrama que fornece uma descrição geral da arquitetura deste modelo, consulte [arquitetura de modelo de solução do Cortana Intelligence para prever a pedido de energia](cortana-analytics-architecture-demand-forecasting-energy.md).  
> 
> 

## <a name="overview"></a>Descrição geral
Este documento aborda o negócio, dados e aspetos técnicos da utilização do Cortana Intelligence e no específica do Azure Machine Learning (AML) para a implementação e a implementação de soluções de previsão de energia. O documento consiste em três partes principais:  

1. Noções sobre empresas  
2. Compreensão de dados  
3. Implementação técnica

O **compreensão de negócio** parte descreve o aspeto de negócio um tem de compreender e considerar antes de efetuar uma decisão de investimento. Explica como qualificar o problema empresarial em execução para garantir que a Análise Preditiva e a aprendizagem realmente eficaz e aplicável. O documento mais explica as noções básicas do machine learning e como são utilizadas para resolver problemas de energia de previsão. -Descreve os pré-requisitos e os critérios de qualificação de um caso de utilização. Alguns exemplo utilizar casos e cenário de negócio cenários também são fornecidos.

Os dados são o principal ingredient para qualquer solução de aprendizagem. O **dados compreender** parte deste documento abrange alguns aspetos dos dados importantes. -Descreve o tipo de dados que é necessária para a previsão de energia, requisitos de qualidade de dados e, normalmente, existem que origens de dados. Também vamos explicar como os dados não processados são utilizados para preparar as funcionalidades de dados, na verdade, a parte de modelação de unidade.

O terceiro parte do documento abrange o **implementação técnica** aspeto de uma solução. Engenharia da funcionalidade e modelação são o núcleo do processo de ciência de dados e, por conseguinte, estão a ser abordados em detalhe. Abrange o conceito de serviços web, que são um vehicle importante para a implementação de nuvem das soluções de Análise Preditiva. Podemos também descrevem uma arquitetura típica de uma solução de operacionalizado ponto-a-ponto.

Além disso, o documento inclui o material de referência que pode utilizar para obter mais compreensão do domínio e tecnologia.

É importante ter em atenção que não pretendemos abrangem neste documento, o processo de ciência de dados mais aprofundado, os aspetos de matemática e técnicos. Estes detalhes podem ser encontrados na [documentação do Azure ML](http://azure.microsoft.com/services/machine-learning/) e [blogues](http://blogs.microsoft.com/blog/tag/azure-machine-learning/).

### <a name="target-audience"></a>Público-alvo
O público-alvo para este documento é empresariais e técnicos de técnicos que gostaria de obter dados de conhecimento e compreensão do Machine Learning com base em soluções e como estas estão a ser utilizadas especificamente dentro do domínio de energia de previsão.

Cientistas de dados também podem beneficiar de ler este documento para obter uma melhor compreensão do processo de alto nível que unidades a implementação de uma solução de previsão de energia. Neste contexto, pode também ser utilizado para estabelecer uma linha de base boa e o ponto de partida para obter mais informações detalhadas e avançadas material.

### <a name="industry-trends"></a>Tendências de setor
Nos últimos anos alguns, IoT, origens de energia alternativo e macrodados intercalado criar oportunidades vasta no espaço de utilitário e energia. Ao mesmo tempo, o utilitário e setores de energia completo tem visto consumo pelo com os consumidores pedir o seu trabalho melhor formas de controlar a utilização de energia.

Muitos utilitário e de empresas de energia inteligente tem sido pioneering o [grelha inteligente](https://en.wikipedia.org/wiki/Smart_grid) ao implementar um número de utilização casos que utilizam os dados gerados pela grelha. Muitos casos de utilização está centrada as características de produção de eletricidade inerentes no: não é possível ser acumulado nem reservados armazenada como o inventário. Por isso, o que é produzido tem de ser utilizado. Tem de utilitários de que pretendem tornar-se mais eficiente prever o consumo de energia simplesmente porque que irá conceder-lhes maior capacidade de **equilibrar procura e oferta**, impedindo wastage de energia, **reduzir petróleo greenhouse emission**e controlar os custos.

Quando se fala dos custos, há outro aspeto importante, o que é o preço. Novas capacidades para compromissos energia entre utilitários tem colocado numa excelente precisa **previsão pedido futuro e preços futuros de eletricidade**. Isto pode ajudar as empresas determinar os volumes de produção.

Quando utilizamos a palavra 'inteligente', mas, na verdade, consulte uma grelha que pode saber mais e, em seguida, fazer predições. Pode antecipa sazonais alterações no consumo, bem como **previr situações de sobrecarga temporária e ajustar automaticamente para a mesma**. Por remotamente regular consumo (com a ajuda destes medidores inteligentes), podem ser processadas situações de sobrecarga localizada. **Ao prever primeiro e, em seguida, agir**, grelha torna-se mais inteligentes ao longo do tempo.

Para o resto deste documento incidirá numa família específica de casos de utilização que abrangem a previsão de futuro, curta e longa duração pedido de energia. Estamos a ter sido funcionar nas seguintes áreas para alguns meses e ter adquirido alguns dados de conhecimento e skill permitem-nos produzir resultados de nível da indústria. Outros casos de utilização vai ser abordados, bem como o documento num futuro próximo.

## <a name="business-understanding"></a>Compreensão Empresarial
### <a name="business-goals"></a>Objetivos de negócio
O **demonstração de energia** objetivo é para demonstrar uma análise preditiva típicas e a solução que pode ser implementada num intervalo de tempo muito curto de aprendizagem. Especificamente, o nosso foco atual é ativar soluções de previsão de energia a pedido para que o valor de negócio pode ser realizado e aproveitado após rapidamente. As informações neste manual de comunicação social podem ajudar o cliente realizar os seguintes objetivos:

* Solução baseada na curto período de tempo ao valor de aprendizagem
* Capacidade para expandir um piloto utilizar maiúsculas e minúsculas para outros casos de utilização ou a um âmbito mais amplo com base na respetiva necessidade de negócio
* Obter rapidamente conhecimento do produto Cortana Intelligence Suite

Com estes objetivos em mente, este manual de comunicação social visa na entrega do negócio e conhecimentos técnicos que irão ajudar a alcançar estes objetivos.

### <a name="power-load-and-demand-forecasting"></a>Carga de energia e prever a pedido
Dentro do setor de energia, podem existir várias formas em que a pedido previsão pode ajudar a resolver problemas de negócio crítico. Na verdade, de previsão pedido pode ser considerada foundation vários núcleos casos de utilização da indústria. Em geral, podemos considerar dois tipos de previsões de pedido de energia: curto e longo prazo. Cada um deles pode servem um objetivo diferente e utilizar uma abordagem diferente. A principal diferença entre as duas é a previsão horizon, que significa que o intervalo de tempo no futuro para o qual podemos seria previsão.

#### <a name="short-term-load-forecasting"></a>A curto prazo carga previsão
No contexto de pedido de energia, a curto prazo carregar previsão (STLF) está definido como a carga agregada que é prevista no futuro near em várias partes da grelha (ou a grelha como um todo). Neste contexto, curta está definida para ser horizon de tempo dentro do intervalo de 1 hora 24 horas. Em alguns casos, um horizon de 48 horas também é possível. Por conseguinte, STLF é muito comum num cenário de utilização operacional da grelha. Seguem-se alguns exemplos de STLF orientadas por casos de utilização:

* Procura e oferta balanceamento
* Suporte de comercial de energia
* Efetuar mercado (preço de energia definição)
* Otimização de operacional de grelha
* [Resposta a pedido](https://en.wikipedia.org/wiki/Demand_response)
* Pico de previsão a pedido
* Gestão de lado de pedido
* Balanceamento de carga e a prevenção de sobrecarga
* Longo prazo previsão de carga
* Deteção de anomalias e de falhas
* Curtailment/nivelamento das horas de ponta 

Modelo STLF baseiam-se principalmente no passado near (últimos dias ou semanas) os dados de consumo e utilize previstosm temperatura como uma previsão da receita importante. Obter temperatura exata previsão para a hora seguinte e cópia de segurança para 24 horas é tornar-se inferior de um desafio dias agora. Estes modelos são menos sensíveis sazonais padrões ou tendências de consumo de longo prazo.

Soluções SLTF também são provável que gerar elevado volume de chamadas de predição (pedidos de serviço), uma vez que estão a ser invocados numa base horária e em alguns casos, mesmo com maior frequência. Também é muito comum para ver implantation onde cada substation individuais ou transformador é representada como um modelo de autónomo e, por conseguinte, o volume de predição pedidos são maiores.

#### <a name="long-term-load-forecasting"></a>Longo prazo previsão de carga
O objetivo de longo prazo carga previsão (LTLF) é a previsão pedido de energia com um horizon tempo vão de 1 semana, meses vários (e, em alguns casos, para um número de anos). Este intervalo de horizon principalmente é aplicável para o planeamento e casos de utilização do investimento.

Para cenários de longa duração, é importante ter dados de alta qualidade que abrange um intervalo de vários anos (mínimo 3 anos). Estes modelos normalmente extrair padrões de sazonalidade dos dados históricos e utilizar predicators externos, tais como meteorologia e climático padrões.

É importante esclarecer que mais tempo a previsão horizon, a menos exato poderá de previsão. Consequentemente, é importante produzir alguns intervalos de confiança, juntamente com a previsão real que lhe permita humans para fator a variação possíveis para o seu processo de planeamento.

Uma vez que o cenário de consumo para LTLF principalmente está a planear, pode Esperamos muito inferiores volumes de predição (em comparação com STLF). É, normalmente, poderá ver estas predições incorporadas nas ferramentas de visualização, tais como o Excel ou do Power BI e ser invocados manualmente pelo utilizador.

### <a name="short-term-vs-long-term-prediction"></a>Curto prazo vs. Predição de longo prazo
A tabela seguinte compara STLF e LTLF no que respeita aos atributos de mais importantes:

| Atributo | Previsão da carga de curto prazo | Previsão da carga de longo prazo |
| --- | --- | --- |
| Previsão Horizon |A partir de 1 hora 48 horas |Entre 1 a 6 meses ou mais |
| Granularidade de dados |Por Hora |Hora ou diária |
| Casos de utilização típica |<ul><li>A pedido/alimentação balanceamento</li><li>Escolha a hora de previsão</li><li>Resposta a pedido</li></ul> |<ul><li>De longo prazo planeamento</li><li>Planeamento de recursos de grelha</li><li>Planeamento de recursos</li></ul> |
| Predictors típicas |<ul><li>Dia ou semana</li><li>Hora do dia</li><li>Temperatura por hora</li></ul> |<ul><li>Último mês do ano</li><li>Dia do mês</li><li>Temperatura de longo prazo e climático</li></ul> |
| Intervalo de dados históricos |Anos duas a três de dados |Anos cinco para 10 de dados |
| Precisão típica |MAPE * de 5% ou inferior |MAPE * de 25% ou inferior |
| Frequência de previsão |Produzidos de hora a hora ou a cada 24 horas |Produzidos depois mensal, trimestral ou anuais |

\*[MAPE](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error) – significa erros percentagem média

Como podem ser vistos desta tabela, é bastante importante distinguir entre o curto e longo prazo previsão cenários como estas representam as necessidades de negócio diferentes e poderão ter de implementação diferentes e padrões de consumo.

### <a name="example-use-case-1-esmart-systems--overload-optimization"></a>Caso de utilização de exemplo 1: eSmart sistemas – otimização de sobrecarga
Uma função importante de um [grelha inteligente](https://en.wikipedia.org/wiki/Smart_grid) é dinamicamente constantemente otimizar e ajustar os padrões de consumo de alteração. Consumo de energia pode ser afetado por alterações de curto prazo que principalmente são causadas por flutuações de temperatura (*por exemplo,*, maior potência de é utilizada para a condição de ar ou aquecimento). Ao mesmo tempo, consumo de energia também deve influenciado pelos tendências de longo prazo. Estes podem incluir efeitos sazonalidade, feriados national, longo prazo crescimento de consumo e até mesmo económico fatores, tais como o índice de consumidor, o preço de petróleo e GDP.

Neste caso de utilização, [eSmart](http://www.esmartsystems.com/) pretendia implementar uma solução baseada na nuvem que permite a prever propensity de uma situação de sobrecarga em qualquer substation determinado da grelha. Em particular, eSmart pretendia identificar substations que se prevê a sobrecarga dentro de uma hora, pelo que uma ação imediata foi direcionada para evitar ou resolver nessa situação.

Um exata e rápido efetuar predição requer a implementação de três modelos preditivos:

* Tempo durante o qual o modelo de termo que permite a previsão de consumo de energia em cada substation durante o próximo algumas semanas ou meses
* Modelo de curto prazo, que permite a predição de sobrecarga situação em que um determinado substation durante a hora seguinte
* Modelo de temperatura que fornece a previsão de temperatura futura ao longo de vários cenários

O objetivo do modelo de longo prazo é classificar que o substations pelo respetivo propensity de sobrecarga (fornecido a respetiva capacidade de transmissão de energia) durante a semana ou mês seguinte. Isto permite a criação de uma lista de curto período de substations serviria como entrada para a predição a curto prazo. Como a temperatura for uma previsão da receita importante para o modelo de longa duração, for necessário para constantemente produzir previsões de temperatura de cenário multi e feed-los como entrada para o modelo de longo prazo. A previsão de curta duração, em seguida, é invocado para prever que substation é provável que a sobrecarga sobre a hora seguinte.

Os modelos de curto e longo prazo são implementados individualmente por cada substation. Por conseguinte, a execução prática estes modelos exige um vasto conjunto orchestration. Para obter maior precisão de predição a curto prazo, um modelo mais granular encontra dedicado para cada hora do dia. Todos os estes modelos são executados a cada hora e concluir a execução dentro de alguns minutos para permitir tempo suficiente para responder e tome medidas preventivo se for necessário. Esta coleção de modelos é mantida atualizada utilizando periodical reparametrização os dados mais recentes.

Podem encontrar mais informações sobre este caso de utilização [aqui](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18945).

#### <a name="use-case-qualification-criteria--prerequisites"></a>Utilizar critérios de qualificação maiúsculas – pré-requisitos
A segurança principal do Cortana Intelligence é na respetiva poderosa capacidade de implementar e dimensionar soluções centrado em do machine learning. Foi concebido para suportar milhares das predições que são executados em simultâneo. Pode dimensionar automaticamente para satisfazer um padrão de consumo de alteração. Foco uma solução por conseguinte, é precisão e o desempenho de capacidade de cálculo. Por exemplo, uma empresa de utilitário está interessada em produção a pedido de energia precisos para a hora seguinte e para cada hora do dia de previsão. Por outro lado, mas é menos interessadas em resposta à pergunta sobre por que motivo é prever o pedido seja dado que é (próprio modelo tratará do que).

Consequentemente, é importante compreender que nem todos os casos de utilização e problemas empresariais possam ser resolvidos de forma eficaz a aprendizagem a utilizar.

Cortana Intelligence e de aprendizagem não foi possível altamente eficazes no resolver um problema empresarial fornecido quando são cumpridos os seguintes critérios:

* O problema empresarial no mão é **preditiva** natureza. Um exemplo de cenário de utilização preditiva é uma empresa de utilitário que gostaria de prever a carga de energia de um determinado substation durante a hora seguinte. Por outro lado, analisar e classificação controladores de procura histórica seria **descritivo** natureza e, por conseguinte, menos aplicável.
* Há um eliminar **caminho de ação** a ser executadas assim que a predição estiver disponível. Por exemplo, prever uma sobrecarga num substation durante a hora seguinte pode acionar uma ação proativa de reduzir a carga que está associada esse substation e, potencialmente, impedindo uma sobrecarga.
* Representa o caso de utilização de um **típico tipo de problema** que quando resolvidos pode pave a forma como para resolver outros semelhante casos de utilização.
* O cliente pode definir **objetivos quantitativos e qualitativa** para demonstrar uma implementação de solução concluída com êxito. Por exemplo, um objetivo quantitativo boa para previsão de pedido de energia seria o limiar de precisão necessária (*por exemplo,*, é permitida a cópia de segurança para 5% erro) ou ao prever substation sobrecarga, em seguida, a precisão (taxa de positivos true) e devolução de chamada (extensão de positivos verdadeiros) deve ser acima de um determinado limiar. Estes objetivos devem ser derivados de objetivos de negócio do cliente.
* Há um eliminar **o cenário de integração** com o fluxo de trabalho de empresas da empresa. Por exemplo, a previsão da carga substation pode ser integrada no Centro de controlo de grelha para permitir que as atividades de prevenção de sobrecarga.
* O cliente tem prontos a utilizar **dados com qualidade suficiente** para suportar o caso de utilização (ver mais na secção seguinte, **qualidade dos dados**, neste manual de comunicação social).
* A cliente engloba centrado em dados arquitetura da nuvem ou **aprendizagem baseado na nuvem**, incluindo do Azure ML e outros componentes do Cortana Intelligence Suite.
* O cliente está disposto a estabelecer **um fluxo de dados de ponto a ponto** esse instalações a entrega de dados para a nuvem de forma contínua e está disposta a **operacionalizar** a solução.
* O cliente está preparado para **dedicar recursos** que irá ser ativamente envolvidos durante a implementação piloto inicial, para que os dados de conhecimento e propriedade da solução podem ser transferidos para o cliente após a conclusão com êxito.
* O recurso de cliente deve ser um **professional dados intruso qualificado**, preferencialmente, uma scientist de dados.

Qualificação de um caso de utilização com base nos critérios acima pode significativamente melhorar as taxas de êxito de um caso de utilização e estabelecer uma boa beachhead para a implementação de casos de utilização futura.

### <a name="cloud-based-solutions"></a>Soluções baseadas na nuvem
Cortana Intelligence Suite no Azure é um ambiente integrado que reside na nuvem. A implementação de uma solução de análise avançadas num ambiente de nuvem contém vantagens significativas para empresas e ao mesmo tempo podem significar que alteração grande para as empresas que ainda utilize no local soluções de TI. Dentro do setor de energia, há uma tendência clara da migração gradual de operações para a nuvem. Esta tendência passa por manualmente no lado juntamente com o desenvolvimento de grelha inteligente conforme mencionado acima, no **tendências da indústria**. Como este manual de comunicação social concentra-se numa solução baseada na nuvem no domínio de energia, é importante explicar as vantagens e outras considerações de implementação de uma solução baseada na nuvem.

Talvez a vantagem maior de uma solução baseada na nuvem é o custo. Como uma solução utilizam componentes de implementação de nuvem, não os custos de compromisso ou os custos de componente de COGS (custo de bens vendida) associados ao mesmo. Isto significa que não é necessário para investir em hardware, software e manutenção IT, e, por conseguinte, existe uma redução substancial no risco de negócio.

Outra vantagem importante é a estrutura de custo pay as you go das soluções baseado na nuvem. Servidores baseados na nuvem para armazenamento ou de informática podem ser implementadas e dimensionados de forma just-como-necessário. Isto representa a vantagem da eficiência de custos de uma solução baseada na nuvem.

Por fim, há sem necessidade de investir no desenvolvimento de infraestrutura futuras ou manutenção IT como tudo isto faz parte de oferta baseado na nuvem. Para essa extensão, Cortana Intelligence Suite inclui o melhor nos serviços de classe e mantém evolução de mapa da estrada. Novas funcionalidades, componentes e as capacidades estão constantemente a ser introduzidas e evoluem.

Para uma empresa que está a iniciar a transição para a nuvem, são altamente Recomendamos a adotar uma abordagem gradual através da implementação do mapa da estrada uma nuvem migração. Acreditamos que para utilitários e as empresas no domínio de energia, os casos de utilização que são debatidos neste manual de comunicação social representam uma excelente oportunidade para testes de implementação no soluções de Análise Preditiva na nuvem.

#### <a name="business-case-justification-considerations"></a>Considerações de negócios justificação maiúsculas
Em muitos casos, o cliente pode estar interessado em efetuar uma justificação de negócio para um caso de utilização especificado em que uma solução baseada na nuvem e o Machine Learning são componentes importantes. Ao contrário de uma solução no local, no caso de uma solução baseada na nuvem, o componente de custo de compromisso é o mínimo e a maioria dos elementos de custos associados a utilização real. Quando for necessário para implementar uma solução no Cortana Intelligence Suite de previsão de energia, vários serviços podem ser integrados com uma estrutura de custo único comuns. Por exemplo, as bases de dados (*por exemplo,*, SQL Azure) pode ser utilizado para armazenar os dados não processados e, em seguida, para o real previsões do Azure ML é utilizada para alojar os serviços de previsão. Neste exemplo, a estrutura de custo pode incluir o armazenamento e componentes transacionais.

Por outro lado, um deve ter uma boa compreensão do valor de negócio de um pedido de energia (curto ou longo prazo) de previsão a funcionar. Na verdade, é importante compreender o valor de negócio de cada operação de previsão. Por exemplo, com precisão previsão carga de energia para as próximas 24 horas pode impedir overproduction ou pode ajudar a evitar sobrecargas na grelha e isto pode ser quantified em termos de reduções financeiras numa base diária.

Uma fórmula básica para calcular o benefício de procura financeiro previsão solução seria: ![fórmula básica para calcular o benefício de procura financeiro previsão solução](media/cortana-analytics-playbook-demand-forecasting-energy/financial-benefit-formula.png)

Uma vez que o Cortana Intelligence Suite fornece um modelo de preços pay as you go, não é necessário para um componente de custo fixo para esta fórmula a incorrer em. Esta fórmula pode ser calculada numa base diária, mensais ou anuais.

É possível encontrar atual Cortana Intelligence Suite e planos de preços do Azure ML [aqui](http://azure.microsoft.com/pricing/details/machine-learning/).

### <a name="solution-development-process"></a>Processo de desenvolvimento da solução
O ciclo de desenvolvimento de um pedido de energia previsão solução, normalmente, envolve 4 fases, em todos os que se a utilização de tecnologias baseadas na nuvem e serviços no Cortana Intelligence Suite.

Esta situação é ilustrada no diagrama seguinte:

![Ciclo de grelha inteligente](media/cortana-analytics-playbook-demand-forecasting-energy/smart-grid-cycle.png)

O parágrafo seguinte descreve este processo 4 passo:

1. **Recolha de dados** – avançadas qualquer solução de análise com base se baseia nos dados (consulte **compreensão de dados**). Especificamente, quando se trata de Análise Preditiva e previsão, precisamos de fluxo em curso e dinâmico de dados. No caso de energia a pedido previsão, estes dados podem ser obtidos diretamente a partir de medidores inteligentes ou já agregados numa base de dados no local. Podemos também dependem de outras origens de dados, tais como meteorologia e temperatura externas. Este fluxo contínuo de dados deve ser orquestrado, agendado e armazenado. [O Azure Data Factory](http://azure.microsoft.com/services/data-factory/) (ADF) é a nossa workhorse principal para realizar esta tarefa.
2. **Modelação** – previsões de energia exata e fiável, um tem desenvolver (formação) e manter um modelo excelente que utilizam a dos dados históricos e extrai os padrões significativos e preditivos nos dados. A área de Machine Learning (ML) tem sido crescer rapidamente com algoritmos mais avançados que está a ser desenvolvidos regularmente. Azure ML Studio fornece uma experiência de utilizador excelente que ajuda a utilizar os algoritmos de ML mais avançados dentro de um fluxo de trabalho completa. Esse fluxo de trabalho é ilustrado no diagrama de fluxo de intuitivo e inclui a preparação de dados, extração de funcionalidade, modelação e avaliação de modelo. O utilizador pode solicitar em centenas de vários modelos que estão incluídos neste ambiente. No final de nesta fase, um scientist dados terão um modelo de trabalho que é completamente avaliado e pronto para a implementação.
   
   O diagrama seguinte é uma ilustração de um fluxo de trabalho normal:
   
   ![Modelação de fluxo de trabalho](media/cortana-analytics-playbook-demand-forecasting-energy/modeling-workflow.png)
3. **Implementação** – com um modelo de trabalho em execução, o próximo passo é a implementação. Aqui, o modelo é convertido para um serviço web que expõe uma API RESTful que pode ser invocado em simultâneo através da Internet a partir de vários clientes de consumo. Azure ML fornece uma forma simple de implementar um modelo diretamente a partir do Azure ML Studio com um único clique do botão para um. O processo de implementação de todo acontece sob definições avançadas. Esta solução possa ser automaticamente dimensionada para satisfazer o consumo de necessário.
4. **Consumo** – nesta fase, iremos, na verdade, certifique-utiliza o modelo de previsão para produzir as predições. O consumo de pode conduzido a partir de uma aplicação de utilizador (*por exemplo,*, dashboard) ou diretamente a partir de um sistema operacional, tais como a pedido/forneça balanceamento de sistema ou uma solução de otimização de grelha. Podem ser orientadas por vários casos de utilização de um único modelo.

## <a name="data-understanding"></a>Dados de conhecimento
Após que abrangem as considerações de negócio (consulte **compreensão de negócio**) de um pedido de energia previsão solução, vamos agora está prontos para abordar a parte de dados. Qualquer solução de Análise Preditiva baseia-se em dados fiáveis. Para a previsão de pedido de energia, precisamos de dados de consumo histórico com vários níveis de granularidade. Os dados históricos são utilizados como o material de raw. -Vai sofrer uma análise cuidada no qual o scientist dados identifiquem predictors (também referidos como funcionalidades) que podem ser colocados num modelo que eventualmente irá gerar os previsões necessárias.

As restantes desta secção, iremos irá descreve os vários passos e considerações para compreender os dados e como colocá-lo a um formulário utilizável.

### <a name="the-model-development-cycle"></a>O ciclo de desenvolvimento do modelo
Produzir boa previsão modelos requer alguma preparação cuidada e planeamento. Interrompendo para baixo o processo de modelação em vários passos e concentrar-se num único passo num momento podem melhorar significativamente o resultado de todo o processo.

O diagrama seguinte ilustra a forma como o processo de modelação foi ser dividido em vários passos:

![Ciclo de desenvolvimento do modelo](media/cortana-analytics-playbook-demand-forecasting-energy/model-development-cycle.png)

Como podem ser vistos que o ciclo de consiste em seis passos:

* Formulação do problema
* Ingestão de dados e exploração de dados
* Preparação de dados e de engenharia da funcionalidade
* Modelação
* Modelo de avaliação
* Desenvolvimento

No resto nesta secção, irá descrevem os passos individuais e itens a considerar cada passo.

### <a name="problem-formulation"></a>Problema formulação
Iremos pode considerar formulação o problema que o passo mais crítico um tem de tomar antes de implementar qualquer solução de Análise Preditiva. Aqui iremos seria transformar o problema empresarial e decompor-lo para elementos específicos que possam ser resolvidos através da utilização de dados e técnicas de modelação. É uma boa prática formular o problema como um conjunto de perguntas que gostaria de responder. Eis algumas questões possíveis que podem ser aplicáveis no âmbito da previsão de pedido de energia:

* O que é a carga esperada num substation individuais na hora ou dia seguinte?
* Em que altura do dia em que a minha grelha verificará picos de procura?
* Qual é a probabilidade meu grelha para Suster a carga máxima esperada?
* Quanto energia deve gerar a estação de energia durante a cada hora do dia?

Formulating estas perguntas permite-nos para se focarem em obter os dados adequados e implementar uma solução que é completamente alinhada com o problema de negócio em questão. Além disso, vamos, em seguida, pode definir alguns as métricas-chave que permitem-nos avaliar o desempenho do modelo. Por exemplo, como precisos a previsão estará e o que é o intervalo de erro que ainda seja aceitável da empresa?

### <a name="data-sources"></a>Origens de Dados
Grelha inteligente moderna recolhe dados de várias partes e dos componentes de grelha. Estes dados representam diferentes aspetos da operação e a utilização de grelha de energia. Dentro do âmbito a procura de energia previsão, iremos são limitar o debate sobre origens de dados que refletem o consumo de pedido real. Uma origem importante de consumo de energia são medidores inteligentes. Utilitários à volta de todo o mundo são implementar rapidamente medidores inteligentes para os seus consumidores. Medidores inteligentes registam o consumo de energia real e constantemente estes dados para a empresa do utilitário de reencaminhamento. Dados são recolhidos e enviados, o intervalo fixo, entre cada 5 minutos e 1 hora. Mais avançados medidores inteligentes podem ser programados remotamente para controlar e equilibrar o consumo real de dentro de um núcleo. Dados de medição inteligente é relativamente fiáveis e incluem um carimbo de data / hora. Isso permite uma ingredient importante para o pedido de previsão. Dados de medição podem ser agregados (summed) em vários níveis na topologia grelha: transformador, substation, região, *etc*. Vamos, em seguida, pode escolher o nível de agregação necessárias para criar um modelo de previsão para o mesmo. Por exemplo, se pretende que a empresa utilitário prever futura carga em cada um dos respetivos substations de grelha, em seguida, dados todos os medidores podem ser agregados para cada substation individuais e utilizados como uma entrada para o modelo de previsão. Denominamos medidores inteligentes uma origem de dados interno.

Uma previsão de pedido de energia fiável será também dependem de outras origens de dados externas. Um fator importante que afeta o consumo de energia é a meteorologia ou mais precisamente a temperatura. Os dados históricos mostram forte correlação entre a temperatura externa e consumo de energia. Durante dias summer frequente, certifique-consumidores utilizar do respetivos conditioners ondas eletromagnéticas e durante a ativação Inverno heating sistemas. Uma origem fiável de históricos temperatures na localização de grelha, por conseguinte, é chave. Além disso, também precisamos de previsão exata de temperatura como uma previsão da receita do consumo de energia.

Também podem ajudar na criação de modelos de previsão de pedido de energia a outras origens de dados externas. Estes podem incluir a longo prazo climático alterações, índices económico (*por exemplo,*, GDP) entre outros. Neste documento, não irá incluir estes outras origens de dados.

### <a name="data-structure"></a>Estrutura de dados
Depois de identificar as origens de dados necessários, gostaríamos de Certifique-se de que os dados não processados que tenham sido recolhidos incluem as funcionalidades de dados correto. Para criar um modelo de previsão pedido fiável, precisamos de garantir que os dados recolhidos incluem elementos de dados que podem ajudar a prever a procura futura. Seguem-se alguns requisitos básicos que dizem respeito à estrutura de dados (esquema) dos dados não processados.

Os dados não processados é composta por linhas e colunas. Cada medida é representada como uma única linha de dados. Cada linha de dados inclui várias colunas (também referidas como funcionalidades ou campos).

1. **Carimbo de hora** – o campo de timestamp representa a hora real, quando a medição foi registada. -Deve estar em conformidade com um dos formatos de data/hora comuns. Data e hora partes devem ser incluídas. Na maioria dos casos, não é necessário para o tempo para serem gravados até o segundo nível de granularidade. É importante especificar o fuso horário em que os dados registados.
2. **ID de medidor** -este campo identifica o medidor ou o dispositivo de medida. É uma variável categórico e pode ser uma combinação de dígitos e carateres.
3. **Valor de consumo** – este é o consumo real de um determinado momento data /. O consumo de pode ser medido em kWh (kilowatt-hour) ou qualquer outro preferencial unidades. É importante ter em atenção que a unidade de medida deve permanecer consistente em todos os valores de dados. Em alguns casos, pode ser fornecido consumo mais 3 fases de energia. Nesse caso seria temos de recolher todas as fases de consumo independentes.
4. **Temperatura** – a temperatura normalmente é recolhida a partir de uma origem independente. No entanto, deve ser compatível com os dados de consumo. Deve incluir um timestamp, tal como descrito acima, que irão permiti-lhe ser sincronizados com os dados de consumo real. O valor de temperatura pode ser especificado em graus Celsius ou Fahrenheit, mas deve permanecer consistente em todos os valores.
5. **Localização –** do campo location é normalmente associado com o local onde os dados de temperatura tenham sido recolhidos. Pode ser representado como um número de código postal ou no formato (lat/longa) de latitude/longitude.

As tabelas seguintes mostra exemplos de um formato de dados de consumo e temperatura boa:

| **Data** | **Tempo** | **ID de medidor** | **Fase 1** | **Fase 2** | **Fase 3** |
| --- | --- | --- | --- | --- | --- |
| 7/1/2015 |10:00:00 |ABC1234 |7.0 |2.1 |5.3 |
| 7/1/2015 |10:00:01 |ABC1234 |7.1 |2.2 |4.3 |
| 7/1/2015 |10:00:02 |ABC1234 |6.0 |2.1 |4.0 |

| **Data** | **Tempo** | **Localização** | **Temperatura** |
| --- | --- | --- | --- |
| 7/1/2015 |10:00:00 |11242 |24.4 |
| 7/1/2015 |10:00:01 |11242 |24.4 |
| 7/1/2015 |10:00:02 |11242 |24.5 |

Como podem ser vistos acima, este exemplo inclui 3 valores diferentes para consumo associado 3 fases de energia. Além disso, tenha em atenção que os campos de data e hora estão separadas, no entanto também podem ser combinados numa única coluna. Neste caso, a coluna de localização é representada um formato de código postal de 5 dígitos e a temperatura num formato grau Celsius.

### <a name="data-format"></a>Formato de dados
Cortana Intelligence Suite pode suportar os formatos de data mais comuns, como CSV, TSV, JSON, *etc*. É importante que o formato de dados situa-se consistente para o ciclo de vida completo do projeto.

### <a name="data-ingestion"></a>Ingestion de Dados
Uma vez que a previsão de pedido de energia é prever a constantemente e frequentemente, iremos tem de garantir que os dados não processados é que fluem através de um processo de ingestão de dados sólida e fiável. O processo de ingestão tem de garantir que os dados não processados estão disponíveis para o processo de previsão ao tempo necessário. Que indica que a frequência de ingestão de dados deve ser superior à frequência da previsão.

Por exemplo: se a nossa pedido previsão solução irá gerar uma nova previsão às 8:00 numa base diária, em seguida, é necessário para se certificar de que incluem todos os dados que tenham sido recolhidos durante as últimas 24 horas tem sido totalmente ingeridos até que ponto e tem o mesmo na última hora de  dados.

Para tal, o Cortana Intelligence Suite oferece várias formas para suportar um processo de ingestão de dados fiável. Isto será mais abordado no **implementação** secção deste documento.

### <a name="data-quality"></a>Qualidade de dados
A origem de dados não processados que são necessária para efetuar a previsão de procura fiável e precisa tem de cumprir critérios de qualidade alguns dados básicos. Apesar de métodos de análises avançados podem ser utilizados para compensar algum problema de qualidade de dados possível, ainda temos para se certificar de são sobreutilização algumas limiar de qualidade de base de dados quando a ingestão de dados de novo. Seguem-se algumas considerações que dizem respeito à qualidade de dados não processados:

* **Falta o valor** -Isto refere-se à situação quando medida específica não foi recolhida. O requisito de básico aqui é que a taxa de valor em falta não deve ser superior a 10% durante qualquer período de tempo especificado. No caso que um valor único está em falta-deve ser indicado através da utilização de um valor predefinido (por exemplo: '9999') e não '0', que pode ser uma medida válido.
* **Precisão medida** – o valor real do consumo ou temperatura deve ser registado com exatidão. Medidas incorretas produzirá previsões incorretas. Normalmente, o erro da medida deve ser inferior a 1 de % relativo ao valor verdadeiro.
* **Hora da medida** – é necessário que o timestamp real dos dados recolhidos serão não desvio por mais de 10 segundos em relação ao momento true da medição real.
* **Sincronização** – quando estão a ser utilizadas várias origens de dados (*por exemplo,*, consumo e temperatura), tem de garantir que não existem sem sincronização de hora problemas entre eles. Isto significa que a diferença de tempo entre o carimbo recolhido quaisquer dois independente das origens de dados não deve exceder mais de 10 segundos.
* **Latência** - conforme mencionado acima, no **ingestão de dados**, estamos dependentes de um processo de fluxo e ingestão de dados fiável. Para controlar o que tem garantimos que iremos controlar a latência de dados. Isto é especificado como a diferença de tempo entre a hora que foi feita a medição real e a hora em que foi carregado para o armazenamento do Cortana Intelligence Suite e está pronto a utilizar. Carga de curto prazo a previsão a latência total não deve ser superior a 30 minutos. Carga de longo prazo a previsão a latência total não deve ser maior que 1 dia.

### <a name="data-preparation-and-feature-engineering"></a>Preparação de dados e de engenharia da funcionalidade
Depois dos dados não processados foi ingeridos (consulte **ingestão de dados**) e foi em segurança armazenada, está pronto para ser processado. Fase de preparação de dados é basicamente colocar os dados não processados e a conversão (transformar, reformulação) para um formulário para a fase de modelação. Que podem incluir operações simples, tais como utilizar a coluna de dados não processados como é com o respetivo valor de medida real, valores normalizados, operações mais complexas, tais como [tempo lagging](https://en.wikipedia.org/wiki/Lag_operator)entre outros. As colunas de dados recentemente criado são referidas como funcionalidades de dados e o processo de gerar estes é referido como engenharia da funcionalidade. No final deste processo, iremos teria um novo conjunto de dados que tenha sido derivado os dados não processados e pode ser utilizado para a modelação. Além disso, a fase de preparação de dados precisa de tratar dos valores em falta (consulte **qualidade dos dados**) bem como compensá-los. Em alguns casos, deverá também temos normalizar os dados para se certificar de que todos os valores são representados na mesma escala.

Nesta secção, que iremos lista algumas das funcionalidades de dados comuns que estão incluídas de energia a pedido previsão modelos.

**Tempo suscitada pelo departamento de funcionalidades:** estas funcionalidades são derivadas dos dados de carimbo de data /. Estes são extraídos e convertidos categórico funcionalidades como:

* Hora do dia – esta é a hora do dia que aceita valores entre 0 e 23
* Dia da semana – Isto representa o dia da semana e aceita valores entre 1 (Domingo) para 7 (Sábado)
* Dia do mês – Isto representa a data real e pode demorar valores entre 1 e 31
* Mês do ano – Isto representa o mês e aceita valores entre 1 (Janeiro) e 12 (Dezembro)
* Fim de semana – esta é uma funcionalidade do valor binário que assume os valores de 0 para dias da semana ou 1 para o fim de semana
* Feriado - esta é uma funcionalidade do valor binário que assume os valores de 0 para um dia regular ou 1 para um feriado
* Termos de Fourier – os termos de Fourier são ponderações que são derivadas do timestamp e são utilizadas para capturar da sazonalidade (cycles) dos dados. Uma vez que vamos pode ter vários seasons nos nossos dados temos vários Fourier termos. Por exemplo, valores de pedido podem ter anuais, semanais e diários seasons/ciclos que resultará em termos de Fourier 3.

**Funcionalidades de medida independente:** as funcionalidades independentes incluem todos os elementos de dados pretende utilizar como predictors no nosso modelo. Aqui iremos excluir a funcionalidade de dependente que precisamos para prever.

* Funcionalidade de desfasamento – estes são tempo desviado valores a procura real. Por exemplo, as funcionalidades de desfasamento 1 armazena o valor a pedido a hora anterior (partindo do princípio de dados por hora), relativos a timestamp atual. Da mesma forma, vamos adicionar desfasamento 2, lag 3, *etc*. A combinação de funcionalidades de desfasamento que são utilizadas real são determinados durante a fase de modelação durante a avaliação dos resultados de modelo.
* De longo prazo tendências – esta funcionalidade representa o crescimento linear no pedido entre anos.

**Funcionalidade dependente:** a funcionalidade dependente é a coluna de dados que Gostaríamos o nosso modelo para prever. Com [supervisionado aprendizagem](https://en.wikipedia.org/wiki/Supervised_learning), temos de primeiro preparar o modelo utilizando as funcionalidades dependentes (que também é referido como etiquetas). Isto permite que o modelo saber os padrões de dados associados com a funcionalidade dependente. Em termos de energia previsão, normalmente, queremos prever a utilização real e, por isso, vamos utilizá-lo como a funcionalidade de dependente.

**Processamento de valores em falta:** durante a fase de preparação de dados, seria precisamos determinar a melhor estratégia para lidar com os valores em falta. Isto é feito principalmente através da utilização de análises várias [os métodos de imputation dados](https://en.wikipedia.org/wiki/Imputation_\(statistics\)). No caso de energia a pedido previsão, iremos normalmente impute valores em falta utilizando média móvel anterior disponíveis dos pontos de dados.

**Dados de normalização:** normalização de dados é outro tipo de transformação que é utilizada para assegurar a todos os dados numéricos, tais como a pedido previsão uma escala semelhante. Isto normalmente ajuda a melhorar a precisão do modelo e a precisão. É, normalmente, isto seria feito, dividindo o valor real por intervalo dos dados.
Isto irá reduzir o valor original verticalmente para um intervalo mais pequeno, normalmente, entre -1 e 1.

## <a name="modeling"></a>Modelação
A fase de modelação é onde ocorre a conversão de dados para um modelo. No principal deste processo existe avançado algoritmos de analisar os dados históricos (dados de formação), extrair padrões e criar um modelo. Esse modelo pode ser posteriormente utilizado para prever em novos dados que não tem sido utilizados para criar o modelo.

Assim que tivermos um modelo de fiável de trabalho, em seguida, pode utilizá-lo para pontuar novos dados estruturados para incluir as funcionalidades necessárias (X). O processo de classificação faz com que utilizam o modelo persistente (objeto de fase de formação) e prever a variável de destino que está em falta que por Ŷ.

### <a name="demand-forecasting-modeling-techniques"></a>Previsão técnicas de modelação de pedido
No caso da pedido, iremos tornar de previsão a utilização de dados históricos, que estão ordenados por hora. É, geralmente, consulte os dados que inclui a dimensão de hora como [série de tempo](https://en.wikipedia.org/wiki/Time_series). O objetivo no modelação de séries de tempo é localizar tendências relacionadas, sazonalidade, auto-correlação (correlação ao longo do tempo), de hora e formular os para um modelo.

Nos últimos anos algoritmos avançados tem foi desenvolvidos para acomodar a previsão de série de tempo e para melhorar a exatidão da previsão. Resumidamente, vamos discutir alguns deles aqui.

> [!NOTE]
> Esta secção não se destina a ser utilizado como uma máquina de aprendizagem e a descrição geral de previsão, mas em vez disso, como um curto inquérito de modelação técnicas que são frequentemente utilizadas para a pedido de previsão. Para obter mais informações e material educational sobre previsão de série de tempo, recomendamos vivamente o livro online [previsão: princípios e prática](https://www.otexts.org/book/fpp).
> 
> 

#### <a name="ma-moving-averagehttpswwwotextsorgfpp62"></a>[**MA (média móvel)**](https://www.otexts.org/fpp/6/2)
Média móvel está as primeira analíticas de técnicas de que foi utilizado para a previsão de série de tempo e é ainda uma das mais frequentemente utilizadas técnicas a partir de hoje. Também é o alicerce da mais avançadas técnicas de previsão. Com média iremos são previsão o próximo ponto de dados por uma média através de pontos de mais recentes K, onde K indica a ordem da média móvel.

A mover técnica média tem o efeito de suavização a previsão e, por conseguinte, não poderá processar volatility bem grande nos dados.

#### <a name="ets-exponential-smoothinghttpswwwotextsorgfpp75"></a>[**ETS (Suavização exponencial)**](https://www.otexts.org/fpp/7/5)
Suavização exponencial (ETS) é uma família de vários métodos que utilizam uma média ponderada recentes de pontos de dados para prever o próximo ponto de dados. A ideia é atribuir ponderações superiores para valores mais recentes e diminuir gradualmente este importância para os valores de medida mais antigos. Existem vários métodos diferentes com esta família, algumas delas incluem processamento de sazonalidade nos dados, tais como [método sazonais Holt Winters](https://www.otexts.org/fpp/7/5).

Alguns destes métodos também pesar da sazonalidade dos dados.

#### <a name="arima-auto-regression-integrated-moving-averagehttpswwwotextsorgfpp8"></a>[**ARIMA (automática regressão integrado mover média)**](https://www.otexts.org/fpp/8)
Auto regressão integrada mover médio (ARIMA) é outra família dos métodos que é normalmente utilizada para a previsão de série de tempo. Praticamente combina os métodos de regressão automática com média. Métodos de regressão automática utilizam modelos de regressão, efetuando os valores de séries de tempo anterior para calcular o próximo ponto de data. Métodos ARIMA também se aplicam os métodos de diferenciação que incluem a calcular a diferença entre os pontos de dados e a utilização dos mesmos em vez do valor monitorizado original. Por fim, ARIMA também utiliza as técnicas de média móvel que são analisadas acima. A combinação de todos estes métodos de várias formas é que constrói a família de métodos ARIMA.

ETS e ARIMA são amplamente utilizado hoje para previsão de pedido de energia e muitos outros problemas de previsão. Em muitos casos estes são combinados em conjunto para fornecer resultados muito exatos.

**Regressão múltipla geral** modelos de regressão dever a abordagem mais importante de modelação dentro do domínio de machine learning e estatísticas. No contexto de séries de tempo utilizamos regressão para prever futuros valores (*por exemplo,*, de procura). Regressão iremos tirar uma combinação de linear dos predictors e saber as ponderações (também referidas como coefficients) desses predictors durante o processo de preparação. O objetivo é para produzir uma linha de regressão será previsão nosso valor previsto. Os métodos de regressão são adequados quando a variável de destino é um valor numérico e, por conseguinte, também se adequa a previsão de série de tempo. Há uma grande variedade de métodos de regressão, incluindo modelos de regressão muito simples, como [regressão Linear](https://en.wikipedia.org/wiki/Linear_regression) e mais avançadas aqueles como árvores de decisões, [aleatório florestas](https://en.wikipedia.org/wiki/Random_forest), [Neural Redes](https://en.wikipedia.org/wiki/Artificial_neural_network)e elevada árvores de decisões.

Construir a pedido de energia previsão como um problema de regressão proporciona muita flexibilidade na seleção de funcionalidades nossos dados que podem ser combinadas dos dados de série de tempo do pedido real e fatores externos, como temperatura. Obter mais informações sobre as funcionalidades selecionadas são abordados na engenharia da funcionalidade (consulte **preparação de dados e de engenharia da funcionalidade**) secção este manual de comunicação social.

Da nossa experiência com a implementação e a implementação do piloto de previsões de pedido de energia, ter Descobrimos que os modelos de regressão avançadas que estão disponíveis no Azure ML têm tendência produzir os melhores resultados e iremos tornar utilizar deles.

## <a name="model-evaluation"></a>Modelo de avaliação
Avaliação do modelo tem um papel fundamental no **ciclo de desenvolvimento de modelo**. Neste passo, vamos ver para validar o modelo e o desempenho com dados de vida real. Durante o passo de modelação utilizamos uma parte dos dados disponíveis para o modelo de preparação. Durante a fase de avaliação, iremos tirar o resto dos dados para testar o modelo. Praticamente significa que podemos são feeding os dados de novo modelo que tenham sido restructured e contém as mesmas funcionalidades como o conjunto de dados de formação. No entanto, durante o processo de validação, utilizamos o modelo para prever a variável de destino em vez de fornecer a variável de destino disponíveis. Vamos referir-se frequentemente a este processo como modelo de classificação. Em seguida, iremos seria utilizar os valores de destino for VERDADEIRO e compará-los com as previsto. O objetivo aqui é medir e minimizar o erro de previsão, que significa que a diferença entre as predições e o valor for VERDADEIRO. Quantifying a medição de erro é a chave, uma vez que Gostaríamos otimizar o modelo e validar se o erro, na verdade, está a diminuir. Fine-Tuning o modelo pode ser feito ao modificar os parâmetros de modelo que controlam o processo de aprendizagem ou adicionando ou removendo as funcionalidades de dados (referido como [parâmetros paramétrico](https://channel9.msdn.com/Blogs/Azure/Data-Science-Series-Building-an-Optimal-Model-With-Parameter-Sweep)). Praticamente que significa que poderá temos de iterar entre a engenharia da funcionalidade, modelação de e modelo várias vezes as fases de avaliação até que é possível reduzir o erro para o nível necessário.

É importante ênfase que o erro de previsão nunca será zero que nunca se um modelo que perfeitamente possível prever a cada resultado. No entanto, há um determinado magnitude erro que é aceitável da empresa. Durante o processo de validação, gostaríamos de Certifique-se de que a nossa erro de previsão do modelo ao nível do ou melhor do que o nível de tolerância de negócio. Consequentemente, é importante definir o nível de erro tolerable no início do ciclo durante o **problema formulação** fase.

### <a name="typical-evaluation-techniques"></a>Técnicas de avaliação típica
Existem várias formas em que a predição de erro pode ser medido e quantified. Nesta secção, irá focar-se o debate sobre técnicas de avaliação relevantes à série de tempo e no específicas para a pedido de energia previsão.

#### <a name="mapehttpsenwikipediaorgwikimeanabsolutepercentageerror"></a>[**MAPE**](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
MAPE representa significa erro percentagem absoluto. Com MAPE iremos são informática a diferença entre cada previstos ponto e o valor real desse ponto. Em seguida, iremos quantificar o erro por ponto calculando a proporção entre a diferença relativo ao valor real. No último passo, vamos médio estes valores. A fórmula matemática utilizada para MAPE é o seguinte:

![A fórmula MAPE](media/cortana-analytics-playbook-demand-forecasting-energy/mape-formula.png)
*onde A<sub>t</sub> é o valor real, F<sub>t</sub> é o valor previsto e n é a previsão horizon.*

## <a name="deployment"></a>Implementação
Assim que podemos ter nailed para baixo a fase de modelação e validar o desempenho do modelo que está prontos Ir para a fase de implementação. Neste contexto, a implementação significa ativar o cliente consumir o modelo executando predições reais no mesmo escala maior. O conceito de implementação é a chave no Azure ML, uma vez que o nosso objetivo principal é a invocar constantemente predições, por oposição a apenas obter as informações a partir dos dados. A fase de implementação é a parte onde iremos ativar o modelo a ser consumidos em grande escala.

No contexto de energia a pedido previsão, nosso objetivo é a invocar previsões contínuas e periódicas garantindo que os novos dados estão disponíveis para o modelo e que os dados previstos são enviados para o cliente de consumo.

### <a name="web-services-deployment"></a>Implementação de serviços Web
O bloco modular implementável principal no Azure ML é o serviço web. Esta é a forma mais eficiente para ativar o consumo de um modelo preditivo na nuvem. O serviço Web encapsula o modelo e encapsula na wrapper cópias de segurança com um [RESTful](http://www.restapitutorial.com/) API (Application Programming Interface). A API pode ser utilizada como parte de qualquer código de cliente conforme ilustrado no diagrama abaixo.

![Estamos a implementação de serviço e o consumo de](media/cortana-analytics-playbook-demand-forecasting-energy/web-service-deployment-and-consumption.png)

Como podem ser vistos, o serviço web está implementado na nuvem Cortana Intelligence Suite e pode ser invocado ao longo do respetivo exposta ponto final da REST API. Um tipo diferente de clientes em vários domínios pode invocar o serviço através da API Web em simultâneo. Também pode dimensionar o serviço web para suportar a milhares de chamadas em simultâneo.

### <a name="a-typical-solution-architecture"></a>Uma arquitetura de solução típica
Quando implementar um pedido de energia previsão solução, estamos interessados em implementar uma solução ponto a ponto que vai mais além do serviço web de predição e facilita o fluxo de dados completo. No momento que invocar uma previsão novo, temos de certificar-se de que o modelo sejam fornecido com as funcionalidades de dados atualizados. Que indica que os dados não processados recentemente recolhidos constantemente ingeridos, processados e transformados a funcionalidade necessária definida no qual o modelo foi criado. Ao mesmo tempo, gostaríamos de disponibilizar os dados previstos para o end consumir os clientes. Um exemplo dados de ciclo de fluxo (ou o pipeline de dados) é ilustrado no diagrama abaixo:

![Pedido de energia previsão de fluxo de dados de ponto a ponto](media/cortana-analytics-playbook-demand-forecasting-energy/energy-demand-forecase-end-data-flow.png)

Estes são os passos que efetuar como parte do ciclo de previsão a pedido de energia:

1. Milhões de medidores de dados implementado constantemente geraram os dados de consumo de energia em tempo real.
2. Estes dados está a ser recolhidos e carregada para um repositório de nuvem (*por exemplo,*, Blob do Azure).
3. Antes de ser processado, os dados não processados são agregados para um nível regional ou substation tal como definido pela empresa.
4. O processamento da funcionalidade (consulte **preparação de dados e a funcionalidade de processamento**), em seguida, ocorra e produz os que é necessários para o modelo de dados de preparação ou de classificação – os dados de conjunto de funcionalidade são armazenados numa base de dados (*por exemplo,*, SQL Azure).
5. O serviço novamente training é invocado para voltar a preparar o modelo de previsão – essa versão atualizada do modelo é persistente, para que possa ser utilizado pelo serviço web de classificação.
6. O serviço web de classificação é invocado com base numa agenda que se adequa a frequência de previsão necessária.
7. Os dados previstos são armazenados numa base de dados que pode ser acedido pelo cliente de consumo de fim.
8. O cliente de consumo obtém os previsões, aplica-se Efetue uma cópia na grelha e consome-lo de acordo com o caso de utilização necessária.

É importante ter em atenção que este ciclo completo é totalmente automatizado e é executada com base numa agenda. A orquestração toda deste ciclo de dados pode ser feita utilizando ferramentas como [do Azure Data Factory](http://azure.microsoft.com/services/data-factory/).

### <a name="end-to-end-deployment-architecture"></a>Arquitetura de implementação de ponto a ponto
Para praticamente implementar uma solução de previsão de pedido de energia no Cortana Intelligence, precisamos de garantir que os componentes necessários são estabelecidos e configurados corretamente.

O diagrama seguinte ilustra uma arquitetura de Intelligence Cortana com base típica que implementa e orquestra o ciclo de fluxo de dados que é descrito acima:

![Arquitetura de implementação de ponto a ponto](media/cortana-analytics-playbook-demand-forecasting-energy/architecture.png)

Para obter mais informações sobre cada um dos componentes e a arquitetura de toda consulte o modelo de solução de energia.

