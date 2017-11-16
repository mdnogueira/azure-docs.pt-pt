---
title: "Descrição geral de diagnóstico do Azure App Service | Microsoft Docs"
description: "Saiba como pode resolver problemas com a sua aplicação web com o diagnóstico de serviço de aplicações."
keywords: "serviço de aplicações, serviço de aplicações do azure, diagnóstico, suporte, aplicação web, de resolução de problemas, ajuda autónoma"
services: app-service
documentationcenter: 
author: jen7714
manager: cfowler
editor: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.openlocfilehash: f027e7fbc5866a85e7f55460192a1c99a71e368e
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="azure-app-service-diagnostics-overview"></a>Descrição geral de diagnóstico de serviço de aplicações do Azure 

Quando estiver a executar uma aplicação web, que pretende ser preparado para quaisquer problemas que possam surgir, de 500 erros para os seus utilizadores informá-lo de que o site está inativo. Diagnóstico de serviço de aplicações é uma experiência interativa e inteligente para o ajudar a resolver problemas da aplicação de web sem qualquer configuração necessária. Quando depare com problemas com a sua aplicação web, diagnóstico de serviço de aplicações irá apontar o que é incorreto para ajudá-lo para as informações corretas para mais fácil e rápida resolver o problema. 
 
Apesar desta experiência mais úteis quando está a ter problemas com a sua aplicação web nas últimas 24 horas, todos os gráficos diagnóstico estarão disponíveis para que possa analisar permanente. Ferramentas adicionais de resolução de problemas e ligações para documentação útil e fóruns estão localizadas na coluna direita.

![Home page](./media/app-service-diagnostics/Homepage1.png)

## <a name="health-checkup"></a>Checkup de estado de funcionamento

Se não souber o que é incorreto com a sua aplicação web ou não sabe onde iniciar os problemas de resolução de problemas, checkup o estado de funcionamento é um bom local para iniciar. O checkup de estado de funcionamento irá analisar as suas aplicações web para lhe dar uma descrição geral rápida, interativa que aponta para os quais está em bom estado e o que é o problema, informá-lo onde parecer para investigar o problema. A interface inteligente e interativa fornece orientação durante o processo de resolução de problemas.  

![Checkup de estado de funcionamento](./media/app-service-diagnostics/HealthCheckup2.png)

Se for detetado um problema com uma categoria de problema específico nas últimas 24 horas, pode ver o relatório de diagnóstico completa e diagnóstico do serviço de aplicações pode solicitar-lhe ver conselhos resolução de problemas mais e passos para uma experiência assistida mais.

![Passos de resolução de problemas e seguintes](./media/app-service-diagnostics/Troubleshooting3.png)

## <a name="tile-shortcuts"></a>Atalhos de mosaico

Se souber exatamente que tipo de procura de informações de resolução de problemas, os atalhos de mosaico leva-o diretamente para o relatório de diagnóstico completa da categoria de problema que está interessado em. Em comparação com a checkup de estado de funcionamento, os atalhos de mosaico são a mais direta, mas inferior orientado forma de aceder a métricas de diagnóstico.  

![Atalhos de mosaico](./media/app-service-diagnostics/TileShortcuts4.png)

## <a name="diagnostic-report"></a>Relatório de diagnóstico

Se quiser obter mais informações, após a execução um [checkup de estado de funcionamento](#health-checkup) ou clicar do [mosaico atalhos](#tile-shortcuts), o relatório de diagnóstico completa irá mostrar-lhe relevantes métricas graphed das últimas 24 horas. Se a sua aplicação experiências qualquer período de inatividade, é representada por uma barra laranja sob a linha cronológica. Pode selecionar um dos tempos de inatividade para obter as observações analisadas sobre o tempo de inatividade e as soluções sugeridas. 

![Relatório de diagnóstico](./media/app-service-diagnostics/DiagnosticReport5.png)

## <a name="open-app-service-diagnostics"></a>Abra diagnóstico de serviço de aplicações

Para aceder ao diagnóstico do serviço de aplicações, navegue até à sua aplicação web do app Service no [portal do Azure](https://portal.azure.com). 

No painel de navegação esquerdo, clique em **diagnosticar e resolver problemas**.