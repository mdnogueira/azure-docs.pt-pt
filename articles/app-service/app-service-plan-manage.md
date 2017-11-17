---
title: "Gerir um plano de serviço de aplicações no Azure | Microsoft Docs"
description: "Saiba como no App Service planos efetuar tarefas diferentes para gerir um plano de serviço de aplicações."
keywords: "do serviço de aplicações, o serviço de aplicações do azure, o dimensionamento, o plano do app service, alterar, criar, gerir, gestão"
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.openlocfilehash: 5369946b1e8a4851ee940cf6fe91a1bdb94db5f3
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="manage-an-app-service-plan-in-azure"></a>Gerir um plano de serviço de aplicações no Azure

Um [plano do App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md) fornece os recursos de uma aplicação do app Service tem de executar. Este guia de procedimentos mostra como gerir um plano de serviço de aplicações. 

## <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

> [!TIP]
> Se tiver um ambiente de serviço de aplicações, consulte [criar um plano de serviço de aplicações num ambiente de serviço de aplicações](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Pode criar um plano de serviço de aplicações vazio ou como parte da criação da aplicação.

No [portal do Azure](https://portal.azure.com), clique em **novo** > **Web + móvel**e, em seguida, selecione **aplicação Web** ou outro tipo de aplicação de serviço de aplicações.

![Crie uma aplicação no portal do Azure.][createWebApp]

Em seguida, pode selecionar um plano de serviço aplicacional existente ou criar um plano para a nova aplicação.

 ![Crie um plano de serviço de aplicações.][createASP]

Para criar um plano de serviço de aplicações, clique em **[+] criar novos**, tipo de **plano do App Service** nome e, em seguida, selecione um adequado **localização**. Clique em **escalão de preço**e, em seguida, selecione um escalão de preço adequado para o serviço. Selecione **ver todos os** para ver mais preços opções, tais como **livres** e **partilhados**. 

Depois de selecionar o escalão de preço, clique em de **selecione** botão.

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Mover uma aplicação para outro plano do App Service

Pode mover uma aplicação para outro plano do App Service, desde que o plano de origem e o plano de destino estiverem no _mesmo grupo de recursos e a região geográfica_.

Para mover uma aplicação para outro plano, navegue para a aplicação que pretende mover no [portal do Azure](https://portal.azure.com).

No **Menu**, procure o **plano do App Service** secção.

Selecione **plano de serviço de aplicações de alteração** para iniciar o processo.

**Plano de serviço aplicacional alteração** abre o **plano do App Service** Seletor. Selecione um plano existente para mover esta aplicação para. 

> [!IMPORTANT]
> O **plano do App Service/selecionar** página é filtrada pelos seguintes critérios: 
> - Existe no mesmo grupo de recursos 
> - Existe na mesma região geográfica 
> - Existe o mesmo espaço de Web  
> 
> A _espaço Web_ é uma construção lógica no App Service, que define um agrupamento de recursos do servidor. Uma região geográfica (por exemplo, EUA oeste) contém muitas webspaces para atribuir clientes que utilizam o serviço de aplicações. Atualmente, os recursos de serviço de aplicações não são capazes de ser movidos entre webspaces. 
> 

![Seletor do plano de serviço de aplicações.][change]

Cada plano tem o seu próprio escalão de preço. Por exemplo, mover um site a partir um **livres** camada para um **padrão** camada, permite que todas as aplicações atribuídas para utilizar as funcionalidades e recursos do **padrão** camada. No entanto, a mover uma aplicação de um plano em camadas superior a um plano em camadas inferior significa que já não tem acesso a algumas funcionalidades. Se a sua aplicação utiliza uma funcionalidade que não está disponível no plano de destino, receberá um erro que indica qual o recurso está em utilização que não está disponível. Por exemplo, se uma das suas aplicações utiliza certificados SSL, poderá ver a mensagem de erro: `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`neste caso, terá de aumentar verticalmente o escalão de preço do plano de destino para **básico** ou superior, ou se tiver de remover todas as ligações SSL a aplicação, antes de pode mover a aplicação para o plano de destino.

## <a name="move-an-app-to-a-different-region"></a>Mover uma aplicação numa região diferente

A região na qual a aplicação é executada é a região do plano de serviço de aplicações está a ser. No entanto, não é possível alterar a região de um plano de serviço de aplicações. Se pretender executar a aplicação numa região diferente, uma alternativa é aplicação clonagem. A clonagem efetua uma cópia da sua aplicação num plano de serviço aplicacional novo ou existente em qualquer região.

Pode encontrar **Clone aplicação** no **ferramentas de desenvolvimento** secção do menu.

> [!IMPORTANT]
> A clonagem tem algumas limitações que pode ler sobre em [clonagem de aplicação do serviço de aplicações do Azure](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Dimensionar um plano de serviço de aplicações

Para aumentar verticalmente a um serviço de aplicações plano do escalão de preço, consulte [aumentar verticalmente a uma aplicação no Azure](web-sites-scale.md).

Para ampliar a contagem de instâncias de uma aplicação, consulte [dimensionar a contagem de instâncias manual ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Eliminar um plano de serviço de aplicações

Para evitar encargos inesperados, ao eliminar a última aplicação num plano do serviço de aplicações, o serviço de aplicações também elimina o plano por predefinição. Se optar por manter o plano em vez disso, deve alterar o plano para **livres** de camada de modo a que não obter-lhe cobrados.

> [!IMPORTANT]
> **Planos do App Service** que não tem aplicações associadas aos mesmos ainda pagar, uma vez que continuam a reserva de instâncias VM configuradas.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Aumentar verticalmente a uma aplicação no Azure](web-sites-scale.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
