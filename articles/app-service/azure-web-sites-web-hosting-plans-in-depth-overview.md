---
title: Planos do App Service nas Web Apps do Azure App Service | Microsoft Docs
description: "Saiba como planos do App Service para o trabalho do App Service do Azure e, como a sua experiência de gestão possam beneficiam."
keywords: "serviço de aplicações, serviço de aplicações do azure, dimensionar, dimensionável, plano do serviço de aplicações, custo do serviço de aplicações"
services: app-service
documentationcenter: 
author: btardif
manager: erikre
editor: 
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: byvinyal
ms.openlocfilehash: fb5b782f09bdd8c8a862eddfbd65b0f86ef8d08c
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="app-service-plans-in-azure-app-service-web-apps"></a>Planos do App Service nas Web Apps do Azure App Service

Planos de serviços aplicacionais representam a coleção de recursos físicos utilizados para alojar as suas aplicações.

Os planos do Serviço de Aplicações definem:

- Região (EUA oeste, EUA leste, etc.)
- Contagem de escalas (um, dois, três instâncias, etc.)
- Tamanho da instância (pequena, média, grande)
- SKU (livres partilhado, básico, Standard, Premium, PremiumV2, isolada)

Web Apps, Mobile Apps, API Apps, aplicações de função (ou funções), no [App Service do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) todos os execução num plano do serviço de aplicações.  Aplicações na mesma subscrição e região podem partilhar um plano de serviço de aplicações. 

Todas as aplicações atribuídas a um **plano do App Service** partilhar recursos definidos por. Esta partilha poupa dinheiro quando alojar várias aplicações num único plano de serviço de aplicações.

O **plano do App Service** pode dimensionar desde **livres** e **partilhados** camadas para **básico**, **padrão**,  **Premium**, e **Isolated** camadas. Cada escalão superior dá-lhe acesso às mais recursos e funcionalidades.

Se o seu plano de serviço de aplicações está definido como **básico** escalão ou superior, em seguida, pode controlar o **tamanho** e dimensionar a contagem das VMs.

Por exemplo, se o seu plano está configurado para utilizar duas instâncias "pequenas" no **padrão** camada, todas as aplicações no plano de que executam em ambas as instâncias. As aplicações têm também acesso para o **padrão** camada funcionalidades. Instâncias do plano em que aplicações estão em execução são completamente gerido e altamente disponível.

> [!IMPORTANT]
> O escalão de preço (SKU) do plano do App Service determina o custo e não o número de aplicações alojadas no mesmo.

Este artigo explicar as características de chaves de um plano de serviço de aplicações, tais como preços camadas e dimensionamento e como funcionam quando gere as suas aplicações.

## <a name="new-pricing-tier-premiumv2"></a>Novo escalão de preço: PremiumV2

A nova **PremiumV2** escalão de preço fornece [Dv2 série VMs](../virtual-machines/windows/sizes-general.md#dv2-series) com processadores mais rápidos, o armazenamento SSD e o rácio de memória de núcleo duplo em comparação comparada **padrão** camada. **PremiumV2** também suporta uma escala maior através do aumento de instâncias, proporcionando ainda, todas as funcionalidades avançadas encontradas no plano de padrão. Todas as funcionalidades disponíveis no existente **Premium** camada estão incluídos no **PremiumV2**.

Semelhantes para outros escalões dedicados, três tamanhos VM estão disponíveis para esta camada de:

- Breve (1 núcleo de CPU, memória do 3.5 GiB) 
- Média (2 núcleos de CPU, memória de GiB 7) 
- Grande (4 núcleos de CPU, memória 14 de GiB)  

Para **PremiumV2** obter informações sobre preços, consulte [preços do App Service](/pricing/details/app-service/).

Para começar a utilizar com o novo **PremiumV2** escalão de preço, consulte [configurar PremiumV2 camada de serviço de aplicações](app-service-configure-premium-tier.md).

## <a name="apps-and-app-service-plans"></a>As aplicações e os planos de serviços aplicacionais

Uma aplicação no App Service pode ser associada com apenas um plano de serviço de aplicações em qualquer momento.

As aplicações e planos estão contidos num **grupo de recursos**. Um grupo de recursos serve como o limite de ciclo de vida para todos os recursos que se encontra dentro do mesmo. Pode utilizar grupos de recursos para gerir todas as partes de uma aplicação em conjunto.

Devido um grupo de recursos única pode ter vários planos de serviço de aplicações, pode alocar aplicações diferentes em diferentes recursos físicos.

Por exemplo, pode separar recursos entre ambientes de desenvolvimento, teste e produção. Ter ambientes separados para produção e de desenvolvimento/teste permite-lhe isolar os recursos. Desta forma, com uma nova versão das suas aplicações de teste de carga não competem para ter os mesmos recursos que as aplicações de produção, que estão a funcionar clientes reais.

Quando tiver vários planos de um grupo de recursos única, também pode definir uma aplicação que abranja regiões geográficas.

Por exemplo, uma aplicação de disponibilidade elevada em execução em duas regiões inclui planos de, pelo menos, dois, um para cada região e uma aplicação associada a cada plano. Numa situação, todas as cópias da aplicação, em seguida, estão contidas num grupo de recursos única. Ter um grupo de recursos com vários esquemas e várias aplicações torna mais fácil de gerir, controlar e ver o estado de funcionamento da aplicação.

## <a name="create-an-app-service-plan-or-use-existing-one"></a>Criar um plano de serviço de aplicações ou utilizar um existente

Ao criar uma nova aplicação Web no App Service, pode partilhar recursos alojamento colocando a aplicação para um plano de serviço aplicacional existente. Para determinar que se a nova aplicação tiver os recursos necessários, tem de compreender a capacidade do plano de serviço de aplicações existente e a carga esperada para a nova aplicação. Excessiva atribuir recursos pode provocar, potencialmente, o período de indisponibilidade para as suas aplicações novas e existentes.

Recomendamos isolar a sua aplicação para um novo serviço de aplicações quando planear:

- A aplicação está consome.
- Aplicação Web tem diferentes fatores de dimensionamento de outras aplicações alojadas num plano existente.
- Aplicação precisa de recursos na região geográfica diferente.

Desta forma pode atribuir um novo conjunto de recursos para a sua aplicação e obter maior controlo das suas aplicações.

## <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

> [!TIP]
> Se tiver um ambiente de serviço de aplicações, consulte [criar um plano de serviço de aplicações num ambiente de serviço de aplicações](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Pode criar um plano de serviço de aplicações vazio ou como parte da criação da aplicação.

No [portal do Azure](https://portal.azure.com), clique em **novo** > **Web + móvel**e, em seguida, selecione **aplicação Web** ou outro tipo de aplicação de serviço de aplicações.

![Crie uma aplicação no portal do Azure.][createWebApp]

Em seguida, pode selecionar ou criar o plano de serviço de aplicações para a nova aplicação.

 ![Crie um plano de serviço de aplicações.][createASP]

Para criar um plano de serviço de aplicações, clique em **[+] criar novos**, tipo de **plano do App Service** nome e, em seguida, selecione um adequado **localização**. Clique em **escalão de preço**e, em seguida, selecione um escalão de preço adequado para o serviço. Selecione **ver todos os** para ver mais preços opções, tais como **livres** e **partilhados**. Depois de selecionar o escalão de preço, clique em de **selecione** botão.

## <a name="move-an-app-to-a-different-app-service-plan"></a>Mover uma aplicação para um plano de serviço aplicacional diferente

Pode mover uma aplicação para um plano de serviço aplicacional diferente no [portal do Azure](https://portal.azure.com). Pode mover as aplicações entre planos, desde que os planos estão no _mesmo grupo de recursos e a região geográfica_.

Para mover uma aplicação para outro plano:

- Navegue para a aplicação que pretende mover.
- No **Menu**, procure o **plano do App Service** secção.
- Selecione **plano de serviço de aplicações de alteração** para iniciar o processo.

**Plano de serviço aplicacional alteração** abre o **plano do App Service** Seletor. Neste momento, pode escolher um plano existente para mover esta aplicação para. São apresentados apenas planos no mesmo grupo de recursos e região.

![Seletor do plano de serviço de aplicações.][change]

Cada plano tem o seu próprio escalão de preço. Por exemplo, mover um site de um escalão gratuito para um escalão Standard, permite que todas as aplicações atribuídas para utilizar as funcionalidades e recursos do escalão Standard.

## <a name="clone-an-app-to-a-different-app-service-plan"></a>Clonar uma aplicação para um plano de serviço de aplicações diferente

Se pretender mover a aplicação numa região diferente, uma alternativa é aplicação clonagem. A clonagem efetua uma cópia da sua aplicação num plano de serviço aplicacional novo ou existente em qualquer região.

Pode encontrar **Clone aplicação** no **ferramentas de desenvolvimento** secção do menu.

> [!IMPORTANT]
> A clonagem tem algumas limitações que pode ler sobre em [clonagem de aplicação do serviço de aplicações do Azure](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Dimensionar um plano de serviço de aplicações

Existem três formas de um plano de escala:

- **O plano de alteração do escalão de preço**. Um plano na camada básica pode ser convertido para Standard e todas as aplicações atribuídas para utilizar as funcionalidades do escalão Standard.
- **Alterar tamanho da instância do plano**. Por exemplo, um plano na camada básica que utiliza instâncias pequenas pode ser alterado para utilizar instâncias grandes. Todas as aplicações que estão associadas esse plano agora podem utilizar a memória adicional e os recursos de CPU que oferece o maior tamanho da instância.
- **Alterar a contagem de instâncias do plano**. Por exemplo, um plano padrão que aumentados horizontalmente para três instâncias pode ser ampliado para 10 instâncias. Um plano de Premium pode ser ampliado para 20 instâncias (sujeitos a disponibilidade). Todas as aplicações que estão associadas esse plano agora podem utilizar a memória adicional e os recursos de CPU que oferece a maior contagem de instâncias.

Pode alterar o tamanho da instância e o escalão de preço clicando a **aumentar verticalmente** item em definições para a aplicação ou o plano de serviço de aplicações. As alterações se aplicam ao plano do App Service e afetam todas as aplicações nele alojado.

 ![Definir valores para aumentar verticalmente a uma aplicação.][pricingtier]

## <a name="app-service-plan-cleanup"></a>Limpeza do plano de serviço de aplicações

> [!IMPORTANT]
> **Planos do App Service** que não tem aplicações associadas aos mesmos ainda pagar, uma vez que podem continuar para a capacidade de cálculo de reserva.

Para evitar encargos inesperados, ao eliminar a última aplicação alojada num plano do serviço de aplicações, o plano de serviço de aplicações vazio resultante também é eliminado por predefinição.

## <a name="summary"></a>Resumo

Planos de serviços aplicacionais representam um conjunto de funcionalidades e capacidades que pode partilhar entre as aplicações. Planos de serviço de aplicações dão-lhe a flexibilidade para alocar aplicações específicas a um conjunto de recursos e otimizar ainda mais a sua utilização de recursos do Azure. Desta forma, se quiser poupar dinheiro no seu ambiente de teste, pode partilhar um plano por várias aplicações. Também pode maximizar o débito para o seu ambiente de produção ao aumentá-lo em várias regiões e planos.

## <a name="whats-changed"></a>O que mudou

- Para obter um guia da alteração de Web sites para o serviço de aplicações, consulte: [App Service do Azure e o respetivo impacto nos serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

[pricingtier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/appserviceplan-pricingtier.png
[assign]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/assing-appserviceplan.png
[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
