---
title: Configurar PremiumV2 camada para o App Service do Azure | Microsoft Docs
description: "Saiba como uma melhor desempenho para a web, móveis e aplicação API no App Service do Azure por dimensionamento para o escalão de preço PremiumV2 novo."
keywords: "serviço de aplicações, serviço de aplicações do azure, dimensionar, dimensionável, plano do serviço de aplicações, custo do serviço de aplicações"
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: cephalin
ms.openlocfilehash: 92cc8d8b0f67dde95ea2e3fc2f0f083bd8ac8aab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Configurar PremiumV2 camada para o App Service do Azure

A nova **PremiumV2** escalão de preço fornece [Dv2 série VMs](../virtual-machines/windows/sizes-general.md#dv2-series) com processadores mais rápidos, o armazenamento SSD e o rácio de memória de núcleo duplo em comparação comparada **padrão** camada. Neste artigo, irá aprender a criar uma aplicação no **PremiumV2** escalão ou aumentar verticalmente a uma aplicação para **PremiumV2** camada.

## <a name="prerequisites"></a>Pré-requisitos

Para uma aplicação web para vertical **PremiumV2**, tem de ter uma aplicação Web no App Service do Azure é executado no escalão de preço inferior **PremiumV2**.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>Disponibilidade de PremiumV2

O escalão de PremiumV2 está atualmente disponível para o serviço de aplicações no _Windows_ apenas. Os contentores de Linux ainda não são suportados.

PremiumV2 já se encontra disponível no Azure mais regiões e crescente. Para ver se está disponível na sua região, execute o seguinte comando da CLI do Azure no [Shell de nuvem do Azure](../cloud-shell/overview.md):

```azurecli-interactive
az appservice list-locations --sku P1V2
```

Se receber um erro durante a criação da aplicação ou serviço de aplicações plano criação, em seguida, **PremiumV2** situação foi provavelmente não está disponível para a região de eleição.

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Criar uma aplicação na camada PremiumV2

O escalão de preço de uma aplicação de serviço de aplicações está definido no [plano do App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md) que é executada no. Pode criar um plano de serviço de aplicações por si só ou como parte da criação da aplicação Web.

Quando configurar plano do App Service no <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, selecione **escalão de preço**. 

Escolha uma do **PremiumV2** opções e clique em **selecione**.

![](media/app-service-configure-premium-tier/pick-premium-tier.png)

> [!IMPORTANT] 
> Se não vir **P1V2**, **P2V2**, e **P3V2** como opções, quer **PremiumV2** não está disponível na sua região escolhidas, ou é configurar um plano de serviço de aplicações do Linux, o que não suporta **PremiumV2**.

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Aumentar verticalmente a uma aplicação existente para PremiumV2 camada

Antes de dimensionamento de uma aplicação existente para **PremiumV2** camada, certifique-se de que **PremiumV2** está disponível na sua região. Para informações, consulte [PremiumV2 disponibilidade](#availability). Se não estiver disponível na sua região, consulte o artigo [aumentar verticalmente a partir de uma região não suportada](#unsupported).

Dependendo do ambiente de alojamento, como aumentar verticalmente pode necessitar de passos adicionais. 

No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, abra a página da aplicação de serviço de aplicações.

No painel de navegação esquerdo da sua página de aplicação de serviço de aplicações, selecione **aumentar verticalmente (plano do App Service)**.

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Selecione uma do **PremiumV2** tamanhos, em seguida, clique em **selecione**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Se a operação ser concluída com êxito, a página de descrição geral da sua aplicação mostra que agora está a ser um **PremiumV2** camada.

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Se obtiver um erro

Alguns planos de serviço de aplicações não é possível aumentar verticalmente para a camada de PremiumV2. Se a operação de dimensionamento dá-lhe um erro, terá um novo plano de serviço de aplicações para a sua aplicação.

Criar um _Windows_ plano do App Service do mesmo grupo de recursos e a região que a sua aplicação de serviço aplicacional existente. Siga os passos indicados em [criar uma aplicação na camada PremiumV2](#create) para o definir como **PremiumV2** camada. Se assim o desejar, utilize a mesma configuração de escalamento horizontal como o seu plano de serviço aplicacional existente (número de instâncias, dimensionamento automático e assim sucessivamente).

Abra a página da aplicação do serviço de aplicações de novo. No painel de navegação esquerdo do seu serviço de aplicações, selecione **plano de serviço de aplicações de alteração**.

![](media/app-service-configure-premium-tier/change-plan.png)

Selecione o plano de serviço de aplicações que acabou de criar.

![](media/app-service-configure-premium-tier/select-plan.png)

Uma vez concluída a operação de alteração, a aplicação está em execução **PremiumV2** camada.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-region"></a>Aumentar verticalmente a partir de uma região não suportada

Se a aplicação for executada numa região onde **PremiumV2** é ainda não está disponível, pode mover a aplicação numa região diferente para tirar partido das **PremiumV2**. Tem duas opções:

- Criar uma aplicação no novo **PremiumV2** planear, em seguida, volte a implementar o código da aplicação. Siga os passos indicados em [criar uma aplicação na camada PremiumV2](#create) para o definir como **PremiumV2** camada. Se assim o desejar, utilize a mesma configuração de escalamento horizontal como o seu plano de serviço aplicacional existente (número de instâncias, dimensionamento automático e assim sucessivamente).
- Se a aplicação já for executada no existente **Premium** camada, em seguida, pode clonar a sua aplicação com todas as definições de aplicação, cadeias de ligação e configuração de implementação.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    No **Clone aplicação** página, pode criar um novo plano de serviço de aplicações na região que pretende e especifique as definições que pretende clonar.

## <a name="automate-with-scripts"></a>Automatizar com scripts

Pode automatizar a criação de aplicações no **PremiumV2** camada com scripts, utilizando o [CLI do Azure](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>CLI do Azure

O comando seguinte cria um plano de serviço de aplicações no _P1V2_. Pode executá-lo na Shell de nuvem. As opções para `--sku` são P1V2, _P2V2_, e _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

O comando seguinte cria um plano de serviço de aplicações no _P1V2_. As opções para `-WorkerSize` são _pequeno_, _média_, e _grande_.

```PowerShell
New-AzureRmAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>Mais recursos

[Aumentar verticalmente a uma aplicação no Azure](web-sites-scale.md)  
[Scale instance count manually or automatically](../monitoring-and-diagnostics/insights-how-to-scale.md) (Dimensionar a contagem de instâncias manual ou automaticamente)