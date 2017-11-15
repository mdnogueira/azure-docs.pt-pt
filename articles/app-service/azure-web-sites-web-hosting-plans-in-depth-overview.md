---
title: "Descrição geral de plano de serviço de aplicações do Azure | Microsoft Docs"
description: "Saiba como planos do App Service para o trabalho do App Service do Azure e, como a sua experiência de gestão possam beneficiam."
keywords: "serviço de aplicações, o serviço de aplicações do azure, o dimensionamento, dimensionável, escalabilidade, o plano do app service, o custo do serviço de aplicações"
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.openlocfilehash: 0815c4d826d9ee09f2e787d9b27258149c55d400
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="azure-app-service-plan-overview"></a>Descrição geral de plano de serviço de aplicações do Azure

No App Service, uma aplicação é executada num _plano do App Service_. Um plano de serviço aplicacional define um conjunto de recursos de computação para uma aplicação web ser executada. Estes de recursos são análogos a computação o [ _farm de servidores_ ](https://wikipedia.org/wiki/Server_farm) no alojamento web convencional. Uma ou mais aplicações podem ser configuradas para executar os mesmos recursos informáticos (ou no mesmo plano de serviço de aplicações). 

Quando cria um plano de serviço de aplicações numa determinada região (por exemplo, Europa Ocidental), é criado um conjunto de recursos de computação para esse plano nessa região. Qualquer aplicações inserida este plano de serviço de aplicações com estes recursos de computação conforme definido pelo seu plano de serviço de aplicações. Define a cada plano do App Service:

- Região (EUA oeste, EUA leste, etc.)
- Número de instâncias VM
- Tamanho de instâncias VM (pequena, média, grande)
- Escalão de preço (gratuito, partilhados, básico, Standard, Premium, PremiumV2, Isolated, consumo de)

O _escalão de preço_ de um serviço de aplicações plano determina quais as funcionalidades de serviço de aplicações, obter e quanto paga para o plano. Existem algumas categorias de escalões de preço:

- **Computação partilhados**: **livres** e **partilhados**, as duas camadas, executa uma aplicação na VM do Azure mesmo como outras aplicações de serviço de aplicações, incluindo aplicações de outros clientes de base. Estas camadas alocar quotas de CPU para cada aplicação que é executado nos recursos partilhados e os recursos não é possível aumentar horizontalmente.
- **Computação dedicada**: O **básico**, **padrão**, **Premium**, e **PremiumV2** camadas executam as aplicações no Azure dedicado VMs. Apenas as aplicações no mesmo plano do App Service partilham os mesmos recursos de computação. Quanto maior for a camada, as instâncias de VM mais estão disponíveis para si para Escalamento horizontal.
- **Isolado**: esta camada é executado dedicado VMs do Azure dedicada redes virtuais do Azure, que fornece isolamento de rede por cima de isolamento de computação para as suas aplicações. Fornece as capacidades de escalamento horizontal máximas.
- **Consumo**: só está disponível para esta camada [funcionar aplicações](../azure-functions/functions-overview.md). -Dimensiona as funções de forma dinâmica consoante a carga de trabalho. Para obter mais informações, consulte [que alojam as funções do Azure planos de comparação](../azure-functions/functions-scale.md).

Cada camada também fornece um subconjunto específico de funcionalidades do App Service. Estas funcionalidades incluem os domínios personalizados e certificados SSL, dimensionamento automático, as ranhuras de implementação, as cópias de segurança, integração do Gestor de tráfego e muito mais. Quanto maior for a camada, as funcionalidades mais estão disponíveis. Para saber quais as funcionalidades são suportadas em cada escalão de preço, consulte [detalhes do plano de serviço de aplicações](https://azure.microsoft.com/pricing/details/app-service/plans/).

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> A nova **PremiumV2** escalão de preço fornece [Dv2 série VMs](../virtual-machines/windows/sizes-general.md#dv2-series) com processadores mais rápidos, o armazenamento SSD e o rácio de memória de núcleo duplo em comparação comparada **padrão** camada. **PremiumV2** também suporta uma escala maior através do aumento de instâncias, proporcionando ainda, todas as funcionalidades avançadas encontradas no plano de padrão. Todas as funcionalidades disponíveis no existente **Premium** camada estão incluídos no **PremiumV2**.
>
> Semelhantes para outros escalões dedicados, três tamanhos VM estão disponíveis para esta camada de:
>
> - Breve (um núcleo de CPU, 3.5 GiB de memória) 
> - Média (dois núcleos de CPU, GiB 7 de memória) 
> - Grande (quatro núcleos de CPU, 14 GiB de memória)  
>
> Para **PremiumV2** obter informações sobre preços, consulte [preços do App Service](/pricing/details/app-service/).
>
> Para começar a utilizar com o novo **PremiumV2** escalão de preço, consulte [configurar PremiumV2 camada de serviço de aplicações](app-service-configure-premium-tier.md).

## <a name="how-does-my-app-run-and-scale"></a>Como que a minha aplicação é executada e dimensionar?

No **livres** e **partilhados** camadas, uma aplicação recebe minutos de CPU numa instância de VM partilhada e não é possível aumentar horizontalmente. Outros escalões, uma aplicação é executada e dimensiona de forma.

Quando criar uma aplicação no App Service, é colocado para um plano de serviço de aplicações. Quando a aplicação é executada, é executado em todas as instâncias VM configuradas no plano de serviço de aplicações. Se várias aplicações estão no mesmo plano de serviço de aplicações, todos os partilham as mesmas instâncias VM. Se tiver várias ranhuras de implementação para uma aplicação, todas as ranhuras de implementação também ser executados as mesmas instâncias VM. Se ativar registos de diagnóstico, efetuar cópias de segurança ou executar WebJobs, também utilizam ciclos da CPU e memória estas instâncias de VM.

Desta forma, o plano de serviço de aplicações é a unidade de escala das aplicações do serviço de aplicações. Se o plano é configurado para executar cinco instâncias de VM, então, todas as aplicações no plano de executam em todas as instâncias de cinco. Se o plano está configurado para efetuar o dimensionamento automático, em seguida, todas as aplicações no plano de são ampliar em conjunto com base nas definições de dimensionamento automático.

Para obter informações sobre como aumentar horizontalmente uma aplicação, consulte [dimensionar a contagem de instâncias manual ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Quanto custo meu plano do App Service?

Esta secção descreve como aplicações do App Service são cobradas. Para informações de preços detalhadas, a região específicos, consulte [preços do App Service](https://azure.microsoft.com/pricing/details/app-service/).

Com exceção do **livres** camada, um plano de serviço aplicacional acarreta um custo por hora nos recursos de computação utiliza.

- No **partilhados** cada aplicação de camada recebe uma quota de minutos de CPU, por isso, _cada aplicação_ é cobrada de hora a hora para a quota de CPU.
- O dedicado em camadas de computação (**básico**, **padrão**, **Premium**, **PremiumV2**), plano do App Service define o número de VM instâncias de aplicações são ampliadas, pelo que _cada instância VM_ no App Service plano tem um custo por hora. Estas instâncias VM são-lhe cobradas as mesmas, independentemente como muitas aplicações estão em execução nos mesmos. Para evitar encargos inesperados, consulte [limpar um plano de serviço aplicacional](app-service-plan-manage.md#delete).
- No **Isolated** camada, ambiente de serviço de aplicação define o número de isolado trabalhadores que executam as suas aplicações e _cada trabalho_ é cobrada por hora. Além disso, não há uma taxa por hora base para a executar o ambiente de serviço de aplicação em si. 
- (Apenas funções do azure) O **consumo** camada dinamicamente aloca instâncias VM para a carga de trabalho de uma aplicação de função de serviço e é-lhe cobrada dinamicamente por segundo pelo Azure. Para obter mais informações, consulte [preços de funções do Azure](https://azure.microsoft.com/pricing/details/functions/).

Não obter-lhe cobrados para utilizar as funcionalidades do serviço de aplicações que estão disponíveis para si (configurar domínios personalizados, certificados SSL, as ranhuras de implementação, as cópias de segurança, etc.). As exceções são:

- Domínios de serviço de aplicações - paga ao adquirir um no Azure e quando, renove-o por ano.
- Certificados de serviço de aplicações - paga ao adquirir um no Azure e quando, renove-o por ano.
- Ligações de SSL baseada em IP - daí de um custo de hora a hora para cada ligação de SSL baseado em IP, mas algumas **padrão** escalão ou superior dá-lhe uma ligação SSL baseado em IP gratuitamente. Ligações de SSL com base em SNI estão livres.

> [!NOTE]
> Se integrar o serviço de aplicações com outro serviço do Azure, poderá ter em consideração os encargos destes outros serviços. Por exemplo, se utilizar o Traffic Manager do Azure para dimensionar a sua aplicação geograficamente, Gestor de tráfego do Azure também os encargos, baseada na sua utilização. Para estimar os custos de entre serviços no Azure, consulte o artigo [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/). 
>
>

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>E se a minha aplicação necessita de mais funções ou funcionalidades?

O plano de serviço de aplicações pode ser escalado para cima e para baixo em qualquer altura. É tão simple como alterar o escalão de preço do plano. Pode escolher um escalão de preço inferior em primeiro lugar e aumentar verticalmente mais tarde quando precisar de mais funcionalidades do serviço de aplicações.

Por exemplo, pode começar a testar a sua aplicação web num **livres** do serviço de aplicações do plano e paga nada. Quando pretender adicionar o [nome DNS personalizado](app-service-web-tutorial-custom-domain.md) na aplicação web, basta dimensione o seu plano até **partilhados** camada. Posteriormente, quando pretender adicionar um [certificado SSL personalizado](app-service-web-tutorial-custom-ssl.md), dimensionar o seu plano até **básico** camada. Quando pretender ter [ambientes de teste](web-sites-staged-publishing.md), dimensionar até **padrão** camada. Quando precisar de mais núcleos, memória ou o armazenamento, aumentar verticalmente para um maior tamanho da VM na mesma camada.

O mesmo funciona a reversão. Quando considerar já não necessita das capacidades ou funcionalidades de uma camada superior, pode reduzir verticalmente a uma camada inferior, que poupa dinheiro.

Para obter informações sobre como aumentar verticalmente plano do App Service, consulte [aumentar verticalmente a uma aplicação no Azure](web-sites-scale.md).

Se a aplicação fica no mesmo plano de serviço de aplicações com outras aplicações, poderá pretender melhorar o desempenho da aplicação ao isolar os recursos de computação. Pode fazê-lo ao mover a aplicação para um plano do App Service separado. Para obter mais informações, consulte [mover uma aplicação para outro plano do App Service](app-service-plan-manage.md#move).

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Deve colocar uma aplicação de um novo plano ou um plano existente?

Uma vez que lhe pagar os recursos de computação seu plano de serviço aplicacional aloca (consulte [que é que o meu custo de plano do App Service?](#cost)), potencialmente pode poupar dinheiro colocando a várias aplicações para um plano de serviço aplicacional. Pode continuar a adicionar aplicações a um plano existente, desde que o plano tem recursos suficientes para processar a carga. No entanto, tenha em atenção que as aplicações no plano do App Service mesmo que todos partilham os mesmos recursos de computação. Para determinar se a nova aplicação tem os recursos necessários, terá de compreender a capacidade do plano de serviço de aplicações existente e a carga esperada para a nova aplicação. A sobrecarga de um plano de serviço aplicacional pode provocar, potencialmente, o período de indisponibilidade para as suas aplicações novas e existentes.

Isole quando planear a sua aplicação para um novo serviço de aplicações:

- A aplicação está consome.
- Pretende dimensionar a aplicação de forma independente de outras aplicações do plano existente.
- A aplicação precisa de recursos na região geográfica diferente.

Desta forma pode atribuir um novo conjunto de recursos para a sua aplicação e obter maior controlo das suas aplicações.

## <a name="manage-an-app-service-plan"></a>Gerir um plano de serviço de aplicações

> [!div class="nextstepaction"]
> [Aumentar verticalmente a uma aplicação no Azure](app-service-plan-manage.md)
