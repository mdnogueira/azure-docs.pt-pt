---
title: "Controlar o tráfego do App Service do Azure com o Gestor de tráfego do Azure"
description: "Este artigo fornece informações de resumo para o Gestor de tráfego do Azure como se relaciona com o App Service do Azure."
services: app-service\web
documentationcenter: 
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2016
ms.author: cephalin
ms.openlocfilehash: efb732e9be62313eb199cb2cfbb1fa4d2cde0282
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Controlar o tráfego do App Service do Azure com o Gestor de tráfego do Azure
> [!NOTE]
> Este artigo fornece informações de resumo para o Gestor de tráfego do Microsoft Azure como se relaciona com o App Service do Azure. Podem encontrar mais informações sobre o Traffic Manager do Azure em si, visitando as hiperligações no fim deste artigo.
> 
> 

## <a name="introduction"></a>Introdução
Pode utilizar o Gestor de tráfego do Azure para controlar a forma como pedidos de clientes web são distribuídos às aplicações do App Service do Azure. Quando os pontos finais do serviço de aplicações são adicionados a um perfil do Traffic Manager do Azure, o Gestor de tráfego do Azure mantém um registo do estado das suas aplicações de serviço de aplicações (em execução, parado ou eliminada), para que este pode decidir que esses pontos finais deve receber tráfego.

## <a name="routing-methods"></a>Métodos de encaminhamento
Traffic Manager do Azure utiliza quatro métodos de encaminhamento diferentes. Estes métodos são descritos na seguinte lista como que dizem respeito ao App Service do Azure.

* **[Prioridade](#priority):** utilizar uma aplicação principal para todo o tráfego e fornecer cópias de segurança no caso de principal ou as aplicações de cópia de segurança não estão disponíveis.
* **[Ponderado](#weighted):** distribuir o tráfego através de um conjunto de aplicações, uniformemente ou, de acordo com ponderações, o que definir.
* **[Desempenho](#performance):** quando tiver aplicações em diferentes localizações geográficas, utilizar a aplicação "mais próxima" em termos da menor latência de rede.
* **[Geográfica](#geographic):** diretos utilizadores para aplicações específicas com base no que localização geográfica as respetivas consultas DNS tem origem. 

Para obter mais informações, consulte [métodos de encaminhamento do Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="app-service-and-traffic-manager-profiles"></a>Serviço de aplicações e perfis do Traffic Manager
Para configurar o controlo de tráfego de aplicação de serviço de aplicações, criar um perfil no Traffic Manager do Azure que utiliza uma das três carregar balanceamento métodos descritos anteriormente e, em seguida, adicionar pontos finais (neste caso, o serviço de aplicações) para o qual pretende controlar o tráfego para o perfil. O estado da sua aplicação (em execução, parado ou eliminada) é comunicado regularmente para o perfil para que o Gestor de tráfego do Azure pode direcionar o tráfego em conformidade.

Ao utilizar o Gestor de tráfego do Azure com o Azure, tenha em consideração os seguintes pontos:

* Para implementações de aplicações só na mesma região, serviço de aplicações já fornece ativação pós-falha e a funcionalidade de round robin, independentemente do modo de aplicação.
* Para implementações na mesma região que utilizem o serviço de aplicações em conjunto com outro serviço em nuvem do Azure, pode combinar ambos os tipos de pontos finais para ativar cenários híbridos.
* Só é possível especificar um ponto final de serviço de aplicações por região num perfil. Quando seleciona uma aplicação como um ponto final de uma região, as restantes aplicações nessa região ficam indisponíveis para seleção para esse perfil.
* Os pontos finais do serviço de aplicações que especificou um perfil do Traffic Manager do Azure é apresentado sob o **nomes de domínio** secção na página configurar para a aplicação no perfil, mas não é configurável não existe.
* Depois de adicionar uma aplicação a um perfil, o **URL do Site** no Dashboard da página da aplicação de portal apresenta o URL de domínio personalizado da aplicação, se configurou um. Caso contrário, apresenta o URL do perfil do Traffic Manager (por exemplo, `contoso.trafficmanager.net`). Tanto o nome de domínio direta da aplicação e o URL do Gestor de tráfego são visíveis na página de configuração da aplicação sob o **nomes de domínio** secção.
* Os nomes de domínio personalizado funcionam conforme esperado, mas, além de adicioná-las às suas aplicações, também tem de configurar o mapeamento DNS para apontar para o URL do Gestor de tráfego. Para obter informações sobre como configurar um domínio personalizado para uma aplicação de serviço de aplicações, consulte [mapear um nome DNS personalizado existente para Web Apps do Azure](app-service-web-tutorial-custom-domain.md).
* Só é possível adicionar as aplicações que estejam no modo padrão ou premium para um perfil do Traffic Manager do Azure.

## <a name="next-steps"></a>Passos Seguintes
Para uma conceptual e técnica descrição geral do Gestor de tráfego do Azure, consulte [descrição geral do Gestor de tráfego](../traffic-manager/traffic-manager-overview.md).

Para obter mais informações sobre como utilizar o Gestor de tráfego com o App Service, consulte as mensagens de blogue [utilizando o Gestor de tráfego do Azure com o Azure Web Sites](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) e [Traffic Manager do Azure agora pode integrar com o Azure Web Sites](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).

