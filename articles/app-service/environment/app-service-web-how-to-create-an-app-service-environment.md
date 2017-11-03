---
title: "Como criar um serviço de aplicações ambiente v1"
description: "Descrição do fluxo de criação de um v1 de ambiente de serviço de aplicações"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/11/2017
ms.author: ccompy
ms.openlocfilehash: ef0dc1b820f42b73af3af3882085729ecc21230c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-an-app-service-environment-v1"></a>Como criar um serviço de aplicações ambiente v1 

> [!NOTE]
> Este artigo é sobre o ambiente de serviço de aplicações v1. Há uma versão mais recente do ambiente de serviço de aplicações que são mais fáceis de utilizar e é executada na infraestrutura mais poderosa. Para saber mais sobre o novo início de versão com o [introdução ao ambiente de serviço de aplicações](intro.md).
> 

### <a name="overview"></a>Descrição geral
O ambiente de serviço de aplicações (ASE) é uma opção de serviço Premium do serviço de aplicações do Azure que fornece uma capacidade de configuração avançada que não está disponível nos carimbos de multi-inquilinos. A funcionalidade de ASE implementa essencialmente o App Service do Azure numa rede virtual do cliente. Para obter uma maior compreensão das funcionalidades oferecidas pelas ambientes do App Service, leia o [o que é um ambiente de serviço de aplicações] [ WhatisASE] documentação.

### <a name="before-you-create-your-ase"></a>Antes de criar o seu ASE
É importante ter em consideração as coisas que não pode alterar. Os aspetos que não é possível alterar sobre o ASE depois de criado são:

* Localização
* Subscrição
* Grupo de Recursos
* VNet utilizado
* Sub-rede utilizada 
* Tamanho da sub-rede

Ao escolher uma VNet e especificar uma sub-rede, certifique-se é suficientemente grande para contemplar qualquer crescimento futuro. 

### <a name="creating-an-app-service-environment-v1"></a>Criar uma aplicação do serviço ambiente v1
Para criar um v1 de ambiente de serviço de aplicações, terá de procurar no Azure Marketplace para ***v1 de ambiente de serviço de aplicações***, ou acedendo através do novo -> Web + Mobile -> ambiente de serviço de aplicações. Para criar um ASEv1:

1. Forneça o nome do seu ASE. O nome especificado para o ASE será utilizado para as aplicações criadas no ASE. Se o nome do ASE é appsvcenvdemo, em seguida, o nome de subdomínio seria. *appsvcenvdemo.p.azurewebsites.net*. Se, por conseguinte, criou uma aplicação com o nome *mytestapp* , em seguida, seria possível endereçável em *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Não é possível utilizar o espaço em branco no nome das suas ASE. Se utilizar carateres maiúsculos no nome, o nome de domínio será a versão em minúsculas total com esse nome. Se utilizar um ILB, em seguida, o nome da sua ASE não é utilizado na sua subdomínio mas em vez disso, é explicitamente indicado durante a criação de ASE
   
    ![][1]
2. Selecione a sua subscrição. A subscrição utilizada para a sua ASE também é aquele que todas as aplicações em que ASE serão criadas com. Não é possível colocar o seu ASE numa VNet que está a ser outra subscrição
3. Selecione ou especifique um novo grupo de recursos. O grupo de recursos utilizado para a sua ASE tem de ser o mesmo que é utilizado para a sua VNet. Se selecionar uma VNet já existente, em seguida, a seleção de grupo de recursos para o seu ASE será atualizada para refletir que a VNet.
   
    ![][2]
4. Certifique-as com as suas seleções de rede Virtual e a localização. Pode optar por criar uma nova VNet ou selecionar uma VNet já existente. Se selecionar uma nova VNet, em seguida, pode especificar um nome e localização. A nova VNet vai ter de endereço intervalo 192.168.250.0/23 e uma sub-rede designada **predefinido** que está definida como 192.168.250.0/24. Pode também simplesmente selecionar um pré-existente clássico ou do VNet do Resource Manager. A seleção do tipo de VIP determina se o ASE pode ser acedido diretamente da internet (externa) ou se utiliza um balanceador de carga interno (ILB). Para obter mais informações sobre os mesmos ler [através de um balanceador de carga interno com um ambiente de serviço de aplicações][ILBASE]. Se selecionar um tipo de VIP de externo, em seguida, pode selecionar quantos endereços IP externos o sistema é criado com para fins IPSSL. Se selecionar interno, em seguida, tem de especificar o subdomínio que irá utilizar o seu ASE. ASEs podem ser implementados em redes virtuais que utilizam *ou* intervalos de endereços públicos, *ou* RFC1918 espaços de endereços (ou seja, endereços privados). Para utilizar uma rede virtual com um intervalo de endereços públicos, terá de criar a VNet antecedência. Quando seleciona uma VNet já existente, terá de criar uma nova sub-rede durante a criação de ASE. **Não é possível utilizar uma sub-rede previamente criada no portal. Pode criar ASE com uma sub-rede pré-existente se criar a sua ASE utilizando um modelo do resource manager.** Para criar ASE contra a utilização de um modelo aqui, a informação de [criar um ambiente de serviço de aplicações a partir do modelo] [ ILBAseTemplate] e aqui, [criar um ambiente de serviço de aplicações do ILB a partir do modelo][ASEfromTemplate].

### <a name="details"></a>Detalhes
ASE é criado com termina de front-2 e 2 Workers. O front-termina atuar como pontos finais de HTTP/HTTPS e enviar o tráfego para os trabalhadores que são as funções que alojam as suas aplicações. Pode ajustar a quantidade após a criação de ASE e pode ainda configurar regras de dimensionamento automático nestes agrupamentos de recursos. Para obter mais detalhes em torno de dimensionamento manual, gestão e monitorização de um ambiente de serviço de aplicação aceda aqui: [como configurar um ambiente de serviço de aplicações][ASEConfig] 

Pode existir apenas a um ASE na sub-rede utilizada pelo ASE. A sub-rede não pode ser utilizada para tudo o que não seja o ASE

### <a name="after-app-service-environment-v1-creation"></a>Após a criação do ambiente de serviço de aplicações v1
Após a criação de ASE pode ajustar:

* Quantidade de front-termina (mínimo: 2)
* Quantidade de trabalhadores (mínimo: 2)
* Quantidade de endereços IP disponíveis para IP SSL
* Os tamanhos de recursos utilizados pelo termina Front ou trabalhadores de computação (o tamanho mínimo de Front-End é P2)

Existem mais detalhes em torno do manual de dimensionamento, gestão e monitorização dos ambientes do App Service aqui: [como configurar um ambiente de serviço de aplicações][ASEConfig] 

Para obter informações sobre o dimensionamento automático é um guia aqui: [como configurar o dimensionamento automático para um ambiente de serviço de aplicações][ASEAutoscale]

Não existem dependências adicionais que não estão disponíveis para personalização, tais como a base de dados e armazenamento. Estes são processadas pelo Azure e são fornecidos com o sistema. O armazenamento do sistema suporta até 500 GB para o ambiente de serviço de aplicação completo e a base de dados é ajustada pelo Azure, conforme necessário, a escala do sistema.

## <a name="getting-started"></a>Introdução
Para começar a utilizar com o ambiente de serviço de aplicações v1, consulte o artigo [introdução para o ambiente de serviço de aplicações v1][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
[ILBAseTemplate]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
