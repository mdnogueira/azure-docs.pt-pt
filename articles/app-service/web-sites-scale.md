---
title: "Aumentar verticalmente a uma aplicação no Azure | Microsoft Docs"
description: "Saiba como aumentar verticalmente a uma aplicação no serviço de aplicações do Azure para adicionar capacidade e funcionalidades."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: cephalin
ms.openlocfilehash: 248b96cc97367ca2cb3fd82c9824d43dfee43c0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="scale-up-an-app-in-azure"></a>Aumentar verticalmente a uma aplicação no Azure

> [!NOTE]
> A nova **PremiumV2** camada dá-lhe CPUs mais rápidas, o armazenamento SSD e faça duplo o rácio de memória para núcleos de escalões de preços existente. Dimensionar até **PremiumV2** camada, consulte [configurar PremiumV2 camada de serviço de aplicações](app-service-configure-premium-tier.md).
>

Este artigo mostra como dimensionar a sua aplicação no App Service do Azure. Existem dois fluxos de trabalho de dimensionamento de dimensionamento, segurança e Escalamento horizontal e este artigo explica o fluxo de trabalho de trabalho de dimensionamento.

* [Aumentar verticalmente](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Obtenha mais CPU, memória, espaço em disco e funcionalidades adicionais, como dedicadas máquinas virtuais (VMs), domínios e certificados personalizados, ranhuras, dimensionamento automático e muito mais de teste. Aumentar verticalmente ao alterar o escalão de preço do plano do App Service, que pertence a aplicação.
* [Aumentar horizontalmente](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): aumentar o número de instâncias VM que executam a aplicação.
  Pode aumentar horizontalmente a 20 máximo de instâncias, consoante o escalão de preço. [Ambientes do App Service](environment/intro.md) no **Isolated** camada adicional aumenta a contagem de escalamento horizontal para 100 instâncias. Para obter mais informações sobre como aumentar horizontalmente, consulte [dimensionar a contagem de instâncias manual ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md). Não existe a saber como utilizar o dimensionamento automático, que está a dimensionar automaticamente com base em regras predefinidas e agendas de contagem de instâncias.

As definições de dimensionamento demorar apenas segundos para aplicar e afetar todas as aplicações na sua [plano do App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
Não necessitam de alterar o seu código ou voltar a implementar a aplicação.

Para obter informações sobre as funcionalidades dos planos do serviço de aplicações individuais e preços, consulte [detalhes de preços do App Service](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Antes de mudar um plano de serviço de aplicações do **livres** camada, tem primeiro de remover o [limites de gastos](https://azure.microsoft.com/pricing/spending-limits/) no local para a sua subscrição do Azure. Para ver ou alterar as opções para a sua subscrição do serviço de aplicações do Microsoft Azure, consulte [subscrições do Microsoft Azure][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Dimensionar o escalão de preço
1. No seu browser, abra o [portal do Azure][portal].
2. Na página da aplicação do serviço de aplicações, clique em **todas as definições**e, em seguida, clique em **aumentar verticalmente**.
   
    ![Navegue para aumentar verticalmente a sua aplicação do Azure.][ChooseWHP]
3. Escolha o escalão e, em seguida, clique em **selecione**.
   
    O **notificações** separador irá flash um verde **êxito** depois de concluída a operação.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Dimensionar os recursos relacionados
Se a aplicação depende de outros serviços, como SQL Database do Azure ou o Storage do Azure, pode dimensionar a estes recursos em separado. Estes recursos não são geridos pelo plano do App Service.

1. No **Essentials**, clique em de **grupo de recursos** ligação.
   
    ![Aumentar verticalmente recursos relacionados da sua aplicação do Azure](./media/web-sites-scale/RGEssentialsLink.png)
2. No **resumo** fazem parte do **grupo de recursos** página, clique num recurso que pretende dimensionar. A seguinte captura de ecrã mostra um recurso de base de dados do SQL Server e um recurso de armazenamento do Azure.
   
    ![Navegue para a página de grupo de recursos para aumentar verticalmente a sua aplicação do Azure](./media/web-sites-scale/ResourceGroup.png)
3. Para um recurso de base de dados do SQL Server, clique em **definições** > **escalão de preço** dimensionar o escalão de preço.
   
    ![Dimensionar o back-end de base de dados SQL para a sua aplicação do Azure](./media/web-sites-scale/ScaleDatabase.png)
   
    Pode também ativar o [georreplicação](../sql-database/sql-database-geo-replication-overview.md) para a instância de base de dados SQL.
   
    Para um recurso de armazenamento do Azure, clique em **definições** > **configuração** para aumentar verticalmente as opções de armazenamento.
   
    ![Aumentar verticalmente a conta de armazenamento do Azure utilizada pela sua aplicação do Azure](./media/web-sites-scale/ScaleStorage.png)

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Comparar escalões de preços
Para obter informações detalhadas, por exemplo, tamanhos de VM para cada escalão de preço, consulte [detalhes de preços do App Service](https://azure.microsoft.com/pricing/details/web-sites/).
Para uma tabela de limites de serviço, quotas e restrições e funcionalidades suportadas em cada camada, consulte [limites do App Service](../azure-subscription-service-limits.md#app-service-limits).

> [!NOTE]
> Se pretender começar com o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [experimentar o App Service](https://azure.microsoft.com/try/app-service/) onde, pode criar imediatamente uma aplicação web de arranque de curta duração no App Service. Sem cartões de crédito são necessários e sem compromissos.
> 
> 

<a name="Next Steps"></a>

## <a name="next-steps"></a>Passos seguintes
* Para informações sobre preços, de suporte e SLA, visite as seguintes ligações:
  
    [Detalhes dos preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)
  
    [Planos de suporte do Azure da Microsoft](https://azure.microsoft.com/support/plans/)
  
    [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/)
  
    [Detalhes de preços de base de dados do SQL Server](https://azure.microsoft.com/pricing/details/sql-database/)
  
    [Máquina virtual e tamanhos do serviço em nuvem do Microsoft Azure][vmsizes]
  
* Para obter informações sobre o App Service do Azure melhores práticas, incluindo a criação de uma arquitetura resiliente e dimensionável, consulte [melhores práticas: Web Apps do Azure App Service](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).
* Para vídeos sobre o dimensionamento de aplicações do App Service, consulte os seguintes recursos:
  
  * [Quando dimensionar Web sites do Azure – com Stefan Schackow](https://azure.microsoft.com/resources/videos/azure-web-sites-free-vs-standard-scaling/)
  * [Auto dimensionamento para Web sites do Azure, a CPU ou agendadas - com Stefan Schackow](https://azure.microsoft.com/resources/videos/auto-scaling-azure-web-sites/)
  * [Escala de Web sites como do Azure - com Stefan Schackow](https://azure.microsoft.com/resources/videos/how-azure-web-sites-scale/)

<!-- LINKS -->
[vmsizes]:/pricing/details/app-service/
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
