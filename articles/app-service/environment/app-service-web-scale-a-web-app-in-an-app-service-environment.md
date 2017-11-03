---
title: "Como dimensionar uma aplicação num ambiente de serviço de aplicações"
description: "Dimensionamento de uma aplicação num ambiente de serviço de aplicações"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: jimbe
ms.assetid: 78eb1e49-4fcd-49e7-b3c7-f1906f0f22e3
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
ms.openlocfilehash: d04a5fce920dae25507cdf2f64832574e24c51dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="scaling-apps-in-an-app-service-environment"></a>Dimensionar aplicações num Ambiente do Serviço de Aplicações
No App Service do Azure são normalmente três coisas, que pode dimensionar:

* plano de preços
* tamanho de trabalho 
* número de instâncias.

Está num ASE não é necessário para selecionar ou alterar o plano de preços.  Em termos de capacidades já se encontra num nível de capacidade de preços Premium.  

No que respeita à tamanhos de trabalho, o administrador de ASE pode atribuir o tamanho do recurso de computação para ser utilizado para cada conjunto de trabalho.  Isto significa que pode ter 1 de conjunto de trabalho com P4 recursos de computação e recursos, de computação do conjunto de trabalho 2 com P1 se assim o desejar.  Não tem de estar pela ordem de tamanho.  Para mais detalhes em torno os tamanhos e os seus preços, consulte o documento aqui [preços do Azure App Service][AppServicePricing].  Esta opção deixa as opções de dimensionamento de aplicações web e planos de serviço de aplicações num ambiente de serviço de aplicações ser:

* seleção de conjunto de trabalho
* número de instâncias

Alterar o item é feito através da IU adequada apresentada para sua ASE alojado planos do App Service.  

![][1]

Não é possível aumentar verticalmente o ASP para além do número de recursos de computação disponíveis no conjunto de trabalho que está a ser o ASP.  Se precisar de recursos nesse conjunto de trabalho de computação terá de obter o seu administrador de ASE adicioná-los.  Para informações à volta de reconfigurar o ASE leia as informações aqui: [como configurar um ambiente de serviço de aplicações][HowtoConfigureASE].  Também poderá tirar partido das funcionalidades de dimensionamento automático ASE para adicionar capacidade com base numa agenda ou métricas.  Para obter mais detalhes sobre como configurar o dimensionamento automático para o ambiente de ASE próprio consulte [como configurar o dimensionamento automático para um ambiente de serviço de aplicações][ASEAutoscale].

Pode criar aplicações vários planos de serviço com recursos de computação a partir de conjuntos de trabalho diferente ou pode utilizar o mesmo conjunto de trabalho.  Por exemplo, se tiver (10) recursos de computação disponíveis no trabalho conjunto 1, pode optar por criar um plano do app service utilizando (6) recursos de computação e um serviço de aplicação de segundo plano que utiliza (4) recursos de computação.

### <a name="scaling-the-number-of-instances"></a>Dimensionamento do número de instâncias
Quando cria pela primeira vez a aplicação web num ambiente de serviço de aplicações começa com 1 instância.  Em seguida, pode aumentar horizontalmente para instâncias adicionais para fornecer recursos de computação adicional para a sua aplicação.   

Se o seu ASE tem capacidade suficiente e isto é pretty simples.  Aceda ao seu plano do App Service que contém os sites que pretende aumentar verticalmente e selecionar o dimensionamento.  Esta ação abre a IU em que pode manualmente definir a escala do ASP ou configurar regras de dimensionamento automático para o ASP.  Para manualmente dimensionar a sua aplicação simplesmente defina ***Dimensionar por*** para ***uma contagem de instâncias que introduzo manualmente***.  A partir daqui arraste o controlo de deslize para a quantidade pretendida ou introduza-o na caixa junto do controlo de deslize.  

![][2] 

As regras de dimensionamento automático para um ASP está num ASE funcionam da mesma forma que normalmente.  Pode selecionar ***percentagem de CPU*** em ***Dimensionar por*** e crie regras de dimensionamento automático para o ASP com base na percentagem de CPU ou pode criar regras mais complexas utilizando ***regras de agendamento e de desempenho*** .  Para ver os detalhes mais completos sobre a configuração de dimensionamento automático utilize o guia aqui [dimensionar uma aplicação no App Service do Azure][AppScale]. 

### <a name="worker-pool-selection"></a>Seleção de conjunto de trabalho
Conforme indicado anteriormente, a seleção de conjunto de trabalho é acedida a partir da IU do ASP.  Abra o painel de ASP que pretende dimensionar e selecione o conjunto de trabalho.  Irá ver todos os conjuntos de trabalho que tiver configurado no seu ambiente de serviço de aplicações.  Se tiver o conjunto de apenas um trabalho, em seguida, apenas verá um agrupamento listado.  Para alterar o conjunto de trabalho o ASP está a ser, basta selecionar o conjunto de trabalho que pretende que o seu plano do App Service para mover para.  

![][3]

Antes de mover o ASP do conjunto de trabalho de um para outro é importante certificar-se de que tem capacidade suficiente para o ASP.  Na lista de conjuntos de trabalho, não só é o nome do conjunto de trabalho listado, mas também pode ver os trabalhadores quantos estão disponíveis nesse conjunto de trabalho.  Certifique-se de que existem suficientes disponíveis para conter o seu plano do App Service de instâncias.  Se o precisa de mais recursos de computação no conjunto de trabalho que pretende mover para, em seguida, obter o administrador de ASE adicioná-los.  

> [!NOTE]
> Mover que um ASP do conjunto de um trabalho fará com que frio inicia das aplicações em que ASP.  Isto pode provocar pedidos ser executada lentamente, dado que a aplicação é amovíveis iniciado em novos recursos de computação.  A arranque a frio pode ser evitada, utilizando o [aplicação transfira a cópia de segurança capacidade] [ AppWarmup] no App Service do Azure.  O módulo de inicialização de aplicação descrito no artigo também funciona para arranques porque o processo de inicialização também é invocado quando as aplicações são amovíveis iniciado em novos recursos de computação. 
> 
> 

## <a name="getting-started"></a>Introdução
Para começar com ambientes do App Service, consulte o artigo [como para criar uma aplicação ambiente de serviço][HowtoCreateASE]

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ScaleWebapp]: ../web-sites-scale.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[CreateWebappinASE]: app-service-web-how-to-create-a-web-app-in-an-ase.md
[Appserviceplans]: ../azure-web-sites-web-hosting-plans-in-depth-overview.md
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../web-sites-scale.md
[AppWarmup]: http://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
