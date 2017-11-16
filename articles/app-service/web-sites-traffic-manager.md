---
title: "Controlar o tráfego da aplicação Web do Azure com o Gestor de Tráfego do Azure"
description: "Este artigo fornece informações de resumo para o Gestor de tráfego do Azure como se relaciona com aplicações web do Azure."
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
ms.openlocfilehash: 5bf687afa8f42292a3b21b19a572c76926fef1cd
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="controlling-azure-web-app-traffic-with-azure-traffic-manager"></a>Controlar o tráfego da aplicação Web do Azure com o Gestor de Tráfego do Azure
> [!NOTE]
> Este artigo fornece informações de resumo para o Gestor de tráfego do Microsoft Azure no que respeita à Web Apps do Azure. Podem encontrar mais informações sobre o Traffic Manager do Azure em si, visitando as hiperligações no fim deste artigo.
> 
> 

## <a name="introduction"></a>Introdução
Pode utilizar o Gestor de tráfego do Azure para controlar a forma como pedidos de clientes web são distribuídos para web apps no App Service do Azure. Quando pontos finais de aplicação web são adicionados a um perfil do Traffic Manager do Azure, o Gestor de tráfego do Azure mantém um registo do estado das suas aplicações web (em execução, parado ou eliminada), para que este pode decidir que esses pontos finais deve receber tráfego.

## <a name="routing-methods"></a>Métodos de encaminhamento
Traffic Manager do Azure utiliza três métodos de encaminhamento diferentes. Estes métodos são descritos na seguinte lista como que dizem respeito a aplicações web do Azure.

* **[Prioridade](#priority):** utilizar uma aplicação web primário para todo o tráfego e fornecer cópias de segurança no caso de principal ou as aplicações web de cópia de segurança não estão disponíveis.
* **[Ponderado](#weighted):** distribuir o tráfego através de um conjunto de aplicações web, uniformemente ou, de acordo com ponderações, o que definir.
* **[Desempenho](#performance):** quando tiver aplicações web em diferentes localizações geográficas, utilize a aplicação web "mais próxima" em termos da menor latência de rede.
* **[Geográfica](#geographic):** diretos aos utilizadores as aplicações web específicos com base no que localização geográfica as respetivas consultas DNS tem origem. 

Para obter mais informações, consulte [métodos de encaminhamento do Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="web-apps-and-traffic-manager-profiles"></a>As Web Apps e perfis do Traffic Manager
Para configurar o controlo de tráfego de aplicações web, criar um perfil no Traffic Manager do Azure que utiliza uma das três carregar balanceamento métodos descritos anteriormente e, em seguida, adicionar pontos finais (neste caso, as aplicações web) para o qual pretende controlar o tráfego para o perfil. O estado da aplicação web (em execução, parado ou eliminada) é comunicado regularmente para o perfil para que o Gestor de tráfego do Azure pode direcionar o tráfego em conformidade.

Ao utilizar o Gestor de tráfego do Azure com o Azure, tenha em consideração os seguintes pontos:

* Para web apenas as implementações das aplicações na mesma região, aplicações Web já fornece ativação pós-falha e a funcionalidade de round robin, independentemente do modo de aplicação web.
* Para implementações na mesma região que utilizem as aplicações Web em conjunto com outro serviço em nuvem do Azure, pode combinar ambos os tipos de pontos finais para ativar cenários híbridos.
* Só é possível especificar um endpoint de aplicação web por região num perfil. Quando seleciona uma aplicação web como um ponto final de uma região, as aplicações web restantes nessa região ficam indisponíveis para seleção para esse perfil.
* Os pontos finais de aplicação web que especifique um perfil do Traffic Manager do Azure é apresentado sob o **nomes de domínio** secção na página configurar para a aplicação web no perfil, mas não é configurável não existe.
* Depois de adicionar uma aplicação web a um perfil, o **URL do Site** no Dashboard da web página da aplicação de portal apresenta o URL do domínio personalizado da aplicação web se configurou um. Caso contrário, apresenta o URL do perfil do Traffic Manager (por exemplo, `contoso.trafficmanager.net`). Tanto o nome de domínio direta da aplicação web e o URL do Gestor de tráfego são visíveis na página de configurar a aplicação web no **nomes de domínio** secção.
* Os nomes de domínio personalizado funcionam conforme esperado, mas, além de adicioná-las às suas aplicações web, também tem de configurar o mapeamento DNS para apontar para o URL do Gestor de tráfego. Para obter informações sobre como configurar um domínio personalizado para uma aplicação web do Azure, consulte [configurar um nome de domínio personalizado para um web site do Azure](app-service-web-tutorial-custom-domain.md).
* Só é possível adicionar as aplicações web que estejam no modo padrão ou premium para um perfil do Traffic Manager do Azure.

## <a name="next-steps"></a>Passos Seguintes
Para uma conceptual e técnica descrição geral do Gestor de tráfego do Azure, consulte [descrição geral do Gestor de tráfego](../traffic-manager/traffic-manager-overview.md).

Para obter mais informações sobre como utilizar o Gestor de tráfego com as Web Apps, consulte as mensagens de blogue [utilizando o Gestor de tráfego do Azure com o Azure Web Sites](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) e [Traffic Manager do Azure agora pode integrar com o Azure Web Sites](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).

