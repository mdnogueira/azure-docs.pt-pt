---
title: "Ambiente de serviço de aplicações e de dimensionamento automático v1"
description: "Dimensionamento automático e ambiente de serviço de aplicações"
services: app-service
documentationcenter: 
author: btardif
manager: erikre
editor: 
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.openlocfilehash: 0feb6e4862f643c35a58c0321181bdda22b032e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Ambiente de serviço de aplicações e de dimensionamento automático v1

> [!NOTE]
> Este artigo é sobre o ambiente de serviço de aplicações v1.  Há uma versão mais recente do ambiente de serviço de aplicações que são mais fáceis de utilizar e é executada na infraestrutura mais poderosa. Para saber mais sobre o novo início de versão com o [introdução ao ambiente de serviço de aplicações](intro.md).
> 

Ambientes do App Service do Azure suportam *dimensionamento automático*. Pode conjuntos de trabalho individuais de dimensionamento automático com base nas métricas ou uma agenda.

![Opções de dimensionamento automático para um conjunto de trabalho.][intro]

Dimensionamento automático otimiza a utilização de recursos por automaticamente a crescer e reduzir um ambiente de serviço de aplicações que se adeque ao seu orçamento e ou carregar perfil.

## <a name="configure-worker-pool-autoscale"></a>Configurar o dimensionamento automático do conjunto de trabalho
Pode aceder a funcionalidade de dimensionamento automático do **definições** separador do conjunto de trabalho.

![Separador Definições do conjunto de trabalho.][settings-scale]

A partir daí, a interface deve ser bastante familiar porque esta é a mesma experiência que vê quando a dimensionar um plano de serviço de aplicações. 

![Definições de dimensionamento manual.][scale-manual]

Também pode configurar um perfil de dimensionamento automático.

![Definições de dimensionamento automático.][scale-profile]

Perfis de dimensionamento automático são úteis para definir limites no seu dimensionamento. Desta forma, pode ter um desempenho consistente experiência, definindo um valor de limite inferior de dimensionamento (1) e um limite de spend previsível ao definir um limite superior (2).

![Definições de dimensionamento no perfil.][scale-profile2]

Depois de definir um perfil, pode adicionar regras de dimensionamento automático para aumentar ou reduzir verticalmente o número de instâncias no conjunto de trabalho dentro dos limites definidas pelo perfil de. Regras de dimensionamento automático baseiam-se nas métricas.

![Regra de dimensionamento.][scale-rule]

 Qualquer conjunto de trabalho ou métricas front-end podem ser utilizadas para definir regras de dimensionamento automático. Estas métricas são as mesmas métricas, pode monitorizar em gráficos de painel de recursos ou definir alertas para.

## <a name="autoscale-example"></a>Exemplo de dimensionamento automático
Dimensionamento automático de um ambiente de serviço de aplicações melhor pode ser ilustrado orientando através de um cenário.

Este artigo explica as necessárias considerações quando configurar o dimensionamento automático. O artigo explica as interações que entrem em play quando fator no dimensionamento automático ambientes de serviço de aplicações que estão alojados no ambiente de serviço de aplicações.

### <a name="scenario-introduction"></a>Introdução do cenário
O Frank é um administrador do sistema para uma empresa que tenha migrado uma parte das cargas de trabalho que ele gere para um ambiente de serviço de aplicações.

O ambiente de serviço aplicacional está configurado para escala manual da seguinte forma:

* **Extremidades do FrontPage:** 3
* **O conjunto de trabalho 1**: 10
* **O conjunto de trabalho 2**: 5
* **O conjunto de trabalho 3**: 5

O conjunto de trabalho 1 é utilizado para cargas de trabalho de produção, enquanto o conjunto de trabalho 2 e 3 um conjunto de trabalho são utilizadas para a garantia de qualidade (pergunta e resposta) e cargas de trabalho de desenvolvimento.

Os planos de serviço de aplicações para a pergunta e resposta e dev estão configurados para escala manual. O plano de serviço de aplicações de produção está definida para o dimensionamento automático para lidar com variações no tráfego e da carga.

O Frank é muito familiarizado com a aplicação. Ele sabe que são as horas de pico de carga entre as 09:00:00 e 18:00:00 porque se trata de um linha de negócio (LOB) de aplicação que os funcionários utilizarem enquanto estão no escritório. Utilização ignora depois disso, quando os utilizadores são efetuados para esse dia. Fora do horário de pico, há alguns carga porque os utilizadores podem aceder à aplicação remotamente utilizando os respetivos dispositivos móveis ou computadores domésticos. O plano de serviço de aplicações de produção já está configurada para dimensionar automaticamente com base na utilização da CPU com as seguintes regras:

![Definições específicas para as aplicações de LOB.][asp-scale]

| **Perfil de dimensionamento automático – dias da semana – plano do App Service** | **Perfil de dimensionamento automático – fins de semana – plano do App Service** |
| --- | --- |
| **Nome:** perfil dia da semana |**Nome:** fim de semana perfil |
| **Escala pelo:** regras de agendamento e de desempenho |**Escala pelo:** regras de agendamento e de desempenho |
| **Perfil:** dias da semana |**Perfil:** fim de semana |
| **Tipo:** periodicidade |**Tipo:** periodicidade |
| **Intervalo de destinos:** instâncias de 5 a 20 |**Intervalo de destinos:** instâncias de 3 a 10 |
| **Dias:** segunda, Terça, quarta-feira, Quinta-feira, sexta-feira |**Dias:** Sábado, Domingo |
| **Hora de início:** 09:00:00 |**Hora de início:** 09:00:00 |
| **Fuso horário:** UTC-08 |**Fuso horário:** UTC-08 |
|  | |
| **Regra de dimensionamento automático (aumentar verticalmente)** |**Regra de dimensionamento automático (aumentar verticalmente)** |
| **Recurso:** produção (ambiente de serviço de aplicações) |**Recurso:** produção (ambiente de serviço de aplicações) |
| **Métrica:** % de CPU |**Métrica:** % de CPU |
| **Operação:** superior a 60% |**Operação:** superior a 80% |
| **Duração:** 5 minutos |**Duração:** 10 minutos |
| **Agregação de tempo:** médio |**Agregação de tempo:** médio |
| **Ação:** aumentar contagem de 2 |**Ação:** aumentar a contagem por 1 |
| **Útil para baixo (minutos):** 15 |**Útil para baixo (minutos):** 20 |
|  | |
| **Regra de dimensionamento automático (escala para baixo)** |**Regra de dimensionamento automático (escala para baixo)** |
| **Recurso:** produção (ambiente de serviço de aplicações) |**Recurso:** produção (ambiente de serviço de aplicações) |
| **Métrica:** % de CPU |**Métrica:** % de CPU |
| **Operação:** inferior a 30% |**Operação:** inferior a 20% |
| **Duração:** 10 minutos |**Duração:** 15 minutos |
| **Agregação de tempo:** médio |**Agregação de tempo:** médio |
| **Ação:** diminuir contagem por 1 |**Ação:** diminuir contagem por 1 |
| **Útil para baixo (minutos):** 20 |**Útil para baixo (minutos):** 10 |

### <a name="app-service-plan-inflation-rate"></a>Taxa de inflation do plano de serviço de aplicações
Planos de serviço de aplicações que estão configurados para dimensionar automaticamente fazê-lo a uma taxa máxima por hora. Taxa deste pode ser calculada com base nos valores fornecidos na regra de dimensionamento automático.

Compreender e calcular o *taxa de inflation do plano de serviço de aplicações* é importante para dimensionamento automático de ambiente de serviço de aplicações porque escala alterações a um conjunto de trabalho não são instantânea.

A taxa de inflation do plano de serviço de aplicações é calculada da seguinte forma:

![Cálculo de taxa de inflation plano de serviço de aplicações.][ASP-Inflation]

Com base no dimensionamento automático – aumentar verticalmente regra para o perfil de dia da semana do plano de serviço de aplicações de produção:

![Taxa de inflation do plano do serviço de aplicações para dias da semana com base no dimensionamento automático – aumentar verticalmente regra.][Equation1]

No caso do dimensionamento automático – aumentar verticalmente regra para o perfil de fim de semana de produção plano do App Service, a fórmula seria resolver para:

![Taxa de inflation do plano do serviço de aplicações para fins de semana com base no dimensionamento automático – aumentar verticalmente regra.][Equation2]

Este valor também pode ser calculado para operações de escala pendente.

Com base no dimensionamento automático – regra escala para baixo para o perfil de dia da semana de produção plano do App Service, este deverá ter o seguinte aspeto:

![Taxa de inflation do plano do serviço de aplicações para dias da semana com base no dimensionamento automático – regra de dimensionamento para baixo.][Equation3]

No caso do dimensionamento automático – regra escala para baixo para o perfil de fim de semana de produção plano do App Service, a fórmula seria resolver para:  

![Taxa de inflation do plano do serviço de aplicações para fins de semana com base no dimensionamento automático – regra de dimensionamento para baixo.][Equation4]

O plano de serviço de aplicações de produção pode aumentar a uma taxa de máxima de oito instâncias por hora durante a semana e quatro instâncias por hora durante o fim de semana. Pode de versão de instâncias de uma taxa máximo de quatro instâncias por hora durante a semana e seis instâncias por hora durante o fim de semana.

Se vários planos de serviço de aplicações estão a ser alojados num conjunto de trabalho, é necessário calcular o *taxa do total inflation* como a soma da taxa de inflation para todos os planos de serviço de aplicações que estão a ser alojamento nesse conjunto de trabalho.

![Total de cálculo de taxa de inflation para vários planos de serviço de aplicações alojadas num conjunto de trabalho.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Utilize a taxa de inflation do plano de serviço de aplicações para definir regras de dimensionamento automático do conjunto de trabalho
Trabalho agrupamentos que alojam planos de serviço de aplicações que estão configurados para dimensionar automaticamente tem de ser atribuída uma memória intermédia de capacidade. Permite que a memória intermédia para as operações de dimensionamento automático aumentar e diminuir o plano de serviço de aplicações conforme necessário. A memória intermédia mínimo seria a calculada aplicação serviço planear Inflation velocidade Total.

Porque as operações de dimensionamento do ambiente de serviço de aplicações demorar algum tempo para aplicar, qualquer alteração deve ter em conta ainda mais a pedido as alterações que pode ter ocorrido enquanto está em curso uma operação de escala. Para contemplar esta latência, recomendamos que utilize a calculada aplicação serviço planear Inflation velocidade Total como o número mínimo de instâncias que são adicionadas para cada operação de dimensionamento automático.

Com esta informação, Frank pode definir o seguinte perfil de dimensionamento automático e as regras:

![Regras de perfil de dimensionamento automático para o exemplo LOB.][Worker-Pool-Scale]

| **Perfil de dimensionamento automático – dias da semana** | **Perfil de dimensionamento automático – fins de semana** |
| --- | --- |
| **Nome:** perfil dia da semana |**Nome:** fim de semana perfil |
| **Escala pelo:** regras de agendamento e de desempenho |**Escala pelo:** regras de agendamento e de desempenho |
| **Perfil:** dias da semana |**Perfil:** fim de semana |
| **Tipo:** periodicidade |**Tipo:** periodicidade |
| **Intervalo de destinos:** 13 para 25 instâncias |**Intervalo de destinos:** instâncias de 6 a 15 |
| **Dias:** segunda, Terça, quarta-feira, Quinta-feira, sexta-feira |**Dias:** Sábado, Domingo |
| **Hora de início:** 7 horas da Manhã |**Hora de início:** 09:00:00 |
| **Fuso horário:** UTC-08 |**Fuso horário:** UTC-08 |
|  | |
| **Regra de dimensionamento automático (aumentar verticalmente)** |**Regra de dimensionamento automático (aumentar verticalmente)** |
| **Recurso:** conjunto de trabalho 1 |**Recurso:** conjunto de trabalho 1 |
| **Métrica:** WorkersAvailable |**Métrica:** WorkersAvailable |
| **Operação:** inferior a 8 |**Operação:** inferior a 3 |
| **Duração:** 20 minutos |**Duração:** 30 minutos |
| **Agregação de tempo:** médio |**Agregação de tempo:** médio |
| **Ação:** aumentar a contagem por 8 |**Ação:** aumentar o número, 3 |
| **Útil para baixo (minutos):** 180 |**Útil para baixo (minutos):** 180 |
|  | |
| **Regra de dimensionamento automático (escala para baixo)** |**Regra de dimensionamento automático (escala para baixo)** |
| **Recurso:** conjunto de trabalho 1 |**Recurso:** conjunto de trabalho 1 |
| **Métrica:** WorkersAvailable |**Métrica:** WorkersAvailable |
| **Operação:** maior 8 |**Operação:** superior a 3 |
| **Duração:** 20 minutos |**Duração:** 15 minutos |
| **Agregação de tempo:** médio |**Agregação de tempo:** médio |
| **Ação:** diminuir contagem por 2 |**Ação:** diminuir contagem por 3 |
| **Útil para baixo (minutos):** 120 |**Útil para baixo (minutos):** 120 |

O intervalo de destino definido no perfil é calculado por instâncias mínimo definidas no perfil para o plano do App Service + a memória intermédia.

O intervalo máximo seria a soma de todos os intervalos máximos para todos os planos de serviço de aplicações alojados no conjunto de trabalho.

A contagem de aumento das regras de dimensionamento deve ser configurada para, pelo menos, 1 X a taxa de Inflation de plano ao serviço de aplicação para dimensionamento.

Contagem de diminuir pode ser ajustada para algo entre 1/2 X ou 1 X a taxa de Inflation de plano ao serviço de aplicações para escala para baixo.

### <a name="autoscale-for-front-end-pool"></a>Dimensionamento automático para o conjunto de front-end
As regras de dimensionamento automático de front-end são mais simples do que para os conjuntos de trabalho. Principalmente, deve  
Certifique-se de que a duração da medição e os temporizadores de cooldown considerar que as operações de dimensionamento um plano de serviço de aplicações não são instantânea.

Para este cenário, o Frank sabe que a taxa de erros aumenta depois de front-ends atingir 80% de utilização da CPU e define a regra de dimensionamento automático para aumentar as instâncias da seguinte forma:

![Definições de dimensionamento automático para o conjunto de front-end.][Front-End-Scale]

| **O perfil de dimensionamento automático – frente termina** |
| --- |
| **Nome:** termina de frente de dimensionamento automático – |
| **Escala pelo:** regras de agendamento e de desempenho |
| **Perfil:** uso corrente |
| **Tipo:** periodicidade |
| **Intervalo de destinos:** instâncias de 3 a 10 |
| **Dias:** uso corrente |
| **Hora de início:** 09:00:00 |
| **Fuso horário:** UTC-08 |
|  |
| **Regra de dimensionamento automático (aumentar verticalmente)** |
| **Recurso:** conjunto de front-end |
| **Métrica:** % de CPU |
| **Operação:** superior a 60% |
| **Duração:** 20 minutos |
| **Agregação de tempo:** médio |
| **Ação:** aumentar o número, 3 |
| **Útil para baixo (minutos):** 120 |
|  |
| **Regra de dimensionamento automático (escala para baixo)** |
| **Recurso:** conjunto de trabalho 1 |
| **Métrica:** % de CPU |
| **Operação:** inferior a 30% |
| **Duração:** 20 minutos |
| **Agregação de tempo:** médio |
| **Ação:** diminuir contagem por 3 |
| **Útil para baixo (minutos):** 120 |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png
