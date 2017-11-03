---
title: "Criar uma aplicação web no v1 ambiente de serviço de aplicações"
description: "Saiba como criar aplicações web e aplicações planos de serviço no v1 ambiente de serviço de aplicações"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/11/2017
ms.author: ccompy
ms.openlocfilehash: b031807073313e9e093dbc7576ecfd3d2a970abe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Criar uma aplicação web no v1 ambiente de serviço de aplicações

> [!NOTE]
> Este artigo é sobre o ambiente de serviço de aplicações v1.  Há uma versão mais recente do ambiente de serviço de aplicações que são mais fáceis de utilizar e é executada na infraestrutura mais poderosa. Para saber mais sobre o novo início de versão com o [introdução ao ambiente de serviço de aplicações](intro.md).
> 

## <a name="overview"></a>Descrição geral
Este tutorial mostra como criar aplicações web e planos do App Service num [v1 de ambiente de serviço de aplicações](app-service-app-service-environment-intro.md) (ASE). 

> [!NOTE]
> Se pretender saber como criar uma aplicação web, mas não precisa de fazê-lo num ambiente de serviço de aplicações, consulte [criar uma aplicação web do .NET](../app-service-web-get-started-dotnet.md) ou um dos tutoriais para outros idiomas e arquiteturas relacionados.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial parte do princípio de que criou um ambiente de serviço de aplicações. Se ainda não o fez que ainda, consulte [criar um ambiente de serviço de aplicações](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Criar uma aplicação Web
1. No [Portal do Azure](https://portal.azure.com/), clique em **novo > Web + móvel > aplicação Web**. 
   
    ![][1]
2. Selecione a sua subscrição.  
   
    Se tiver várias subscrições, lembre-se de que para criar uma aplicação no seu ambiente de serviço de aplicações, terá de utilizar a mesma subscrição que utilizou ao criar o ambiente. 
3. Selecione ou crie um grupo de recursos.
   
    *Grupos de recursos* permitem-lhe gerir recursos relacionados do Azure como uma unidade e são úteis quando estabelecer *controlo de acesso baseado em funções* regras (RBAC) para as suas aplicações. Para obter mais informações, consulte [descrição geral do Azure Resource Manager][ResourceGroups]. 
4. Selecione ou crie um plano de serviço de aplicações.
   
    *Planos do App Service* gerido conjuntos de aplicações web.  Normalmente, quando seleciona preços, o preço cobrado é aplicado para o plano de serviço de aplicações, em vez de aplicações individuais. Está num ASE pagar para as instâncias de computação alocadas para o ASE em vez do que listados com o ASP.  Para aumentar verticalmente o número de instâncias de uma aplicação web dimensionar as instâncias do seu serviço de aplicações do plano e afeta todas as aplicações web no que plano.  Algumas funcionalidades, tais como ranhuras de site ou integração de VNET também tem restrições quantidade dentro do plano.  Para obter mais informações, consulte [descrição geral dos planos do App Service do Azure](../azure-web-sites-web-hosting-plans-in-depth-overview.md)
   
    Pode identificar os planos de serviço de aplicações no seu ASE observando a localização que é apresentada sob o nome do plano.  
   
    ![][5]
   
    Se pretender utilizar um plano de serviço de aplicações que já existe no seu ambiente de serviço de aplicações, selecione esse plano. Se pretender criar um novo plano de serviço de aplicações, consulte a secção seguinte deste tutorial, [criar um plano de serviço de aplicações num ambiente de serviço de aplicações](#createplan).
5. Introduza o nome da sua aplicação web e, em seguida, clique em **criar**. 
   
    Se a sua ASE utiliza um VIP externo é o URL de uma aplicação está num ASE: [*sitename*]. [ *nome do ambiente de serviço de aplicações*]. p.azurewebsites.net em vez de [*sitename*]. azurewebsites.net
   
    Se a sua ASE utiliza um VIP interno, em seguida, o URL de uma aplicação na qual que ASE é: [*sitename*]. [ *subdomínio especificado durante a criação de ASE*]   
    Depois de selecionar o ASP durante a criação de ASE, verá o subdomínio atualizar abaixo **nome**

## <a name="createplan"></a>Criar um plano de serviço de aplicações
Quando cria um plano de serviço de aplicações num ambiente de serviço de aplicações, as opções de trabalho são diferentes, porque não existem nenhum trabalhadores partilhados num ASE.  Os trabalhadores que tiver de utilizar são aqueles que teriam sido alocados para o ASE pelo administrador.  Isto significa que para criar um novo plano, tem de ter mais trabalhadores alocados ao agrupamento de trabalho de ASE que o número total de instâncias em todos os seus planos já em que conjunto de trabalho.  Se não tiver suficiente trabalhadores do conjunto de trabalho ASE para criar o seu plano, terá de trabalhar com o administrador de ASE para obtê-los adicionado.

Diferença outra com os planos de serviço de aplicações alojadas por um ambiente de serviço de aplicações é a falta de seleção de preço.  Quando tiver um ambiente de serviço de aplicações pagar com recursos de computação utilizados pelo sistema e não tem custos adicionados para os planos nesse ambiente.  Normalmente, quando criar um plano de serviço de aplicações, selecionar um plano de preços que determina a faturação.  Um ambiente de serviço de aplicações é, essencialmente, uma localização privada onde pode criar conteúdo.  Paga para o ambiente e não para alojar o seu conteúdo.

As instruções seguintes mostram como criar um plano de serviço de aplicações, enquanto estiver a criar uma aplicação web, tal como explicado na secção anterior do tutorial.

1. Clique em **criar novo** na IU de seleção de plano e forneça um nome para o plano, tal como faria normalmente fora ASE.
2. Selecione o ASE que pretende utilizar na sua selecionador de localização.
   
    Como um ambiente de serviço de aplicações é, essencialmente, uma localização de implementação privada, mostra na localização. 
   
    ![][2]
   
    Após a seleção de ASE no selecionador de localização, a criação do plano de serviço de aplicações IU de atualizações.  A localização mostra agora o nome do sistema ASE e a região-no, e o preço selecionador de plano é substituído por um Seletor de conjunto de trabalho.  
   
    ![][3]

### <a name="selecting-a-worker-pool"></a>Selecionar um conjunto de trabalho
Normalmente no App Service do Azure e fora de um ambiente de serviço de aplicações, existem 3 tamanhos de computação que estão disponíveis com a seleção de um plano de preços dedicado.  De forma semelhante, para ASE pode definir até 3 conjuntos dos trabalhadores e especificar o tamanho de computação que é utilizado para esse conjunto de trabalho.  O que significa que para inquilinos do ASE é, em vez de selecionar um plano de preços com tamanho de computação para que o plano de serviço de aplicações, selecionar o que é chamado um *conjunto de trabalho*.  

A seleção de conjunto de trabalho a IU apresenta o tamanho de cálculo utilizado para esse conjunto de trabalho abaixo o nome.  A quantidade disponível refere-se a computação quantas instâncias estão disponíveis para utilização nesse agrupamento.  O conjunto total, na verdade, pode ter mais de instâncias que este número, mas este valor se refere a simplesmente quantos não estão em utilização.  Se precisar de ajustar o ambiente de serviço de aplicações para adicionar mais recursos de computação consulte [configurar o ambiente de serviço de aplicações](app-service-web-configure-an-app-service-environment.md).

![][4]

Neste exemplo, pode ver apenas dois conjuntos de trabalho disponíveis. Isto acontece porque o administrador de ASE alocado apenas anfitriões para os conjuntos de trabalho de dois.  O terceiro seria aparecer quando existem VMs alocadas para a mesma.  

## <a name="after-web-app-creation"></a>Após a criação da aplicação web
Existem algumas considerações para executar aplicações web e a gerir planos de serviço de aplicações está num ASE que precisam de ser levados em consideração.  

Conforme indicado anteriormente, o proprietário do ASE é responsável pelo tamanho do sistema e como consequência também são responsáveis por garantir que não há capacidade suficiente para alojar os planos do App Service pretendidos. Se não houver nenhuma disponíveis trabalhadores, não será possível criar o seu plano de serviço de aplicações.  Isto também é verdadeiro para como aumentar verticalmente a sua aplicação web.  Se precisar de mais instâncias, em seguida, terá de obter o seu administrador de ambiente de serviço de aplicações para adicionar mais workers.

Depois de criar a sua aplicação web e o plano do App Service é uma boa ideia dimensione-lo.  Está num ASE sempre tem de ter, pelo menos, 2 instâncias do plano de serviço de aplicações para fornecer tolerância a falhas para as suas aplicações.  Um plano de serviço de aplicações de dimensionamento num ASE é o mesmo que o normal através da IU do plano do App Service.  Para obter mais informações sobre dimensionamento, [como dimensionar uma aplicação web num ambiente de serviço de aplicações](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../azure-web-sites-web-hosting-plans-in-depth-overview.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[ResourceGroups]: ../../azure-resource-manager/resource-group-overview.md
[AzurePowershell]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
