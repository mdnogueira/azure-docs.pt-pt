---
title: "Reparametrização de um modelo de Machine Learning | Microsoft Docs"
description: "Saiba como reparametrização de um modelo e atualizar o serviço Web para utilizar o modelo treinado recentemente no Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: d1cb6088-4f7c-4c32-94f2-f7523dad9059
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: v-donglo
ms.openlocfilehash: 0270a43f79202d102a8cdfe9cba7011a882e8117
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="retrain-a-machine-learning-model"></a>Reparametrização de um modelo de Machine Learning
Como parte do processo de operationalization de modelos de machine learning no Azure Machine Learning, o seu modelo está preparado e guardou. Pode, em seguida, utilizado para criar um serviço Web predicative. O serviço Web, em seguida, pode ser utilizado em web sites, dashboards e as aplicações móveis. 

Modelos criados com Machine Learning não são normalmente estáticos. Como novos dados ficarem disponíveis ou quando o consumidor da API tem os seus próprios dados o modelo precise de ser retrained. 

Reparametrização pode ocorrer com frequência. Com a funcionalidade de API reparametrização programática, pode programaticamente reparametrização do modelo, utilizando APIs para a reparametrização e atualizar o serviço Web com o modelo treinado recentemente. 

Este documento descreve o processo de reparametrização e mostra-lhe como utilizar os APIs para a reparametrização.

## <a name="why-retrain-defining-the-problem"></a>Por que motivo reparametrização dos: definir o problema
Como parte da aprendizagem de processo de formação, um modelo é preparado utilizando um conjunto de dados. Modelos criados com Machine Learning não são normalmente estáticos. Como novos dados ficarem disponíveis ou quando o consumidor da API tem os seus próprios dados o modelo precise de ser retrained.

Nestes cenários, uma API programática fornece uma maneira conveniente para permitir ao utilizador ou o consumidor das suas APIs criar um cliente que pode, com base numa única ou regular, reparametrização do modelo utilizando os seus próprios dados. Em seguida, podem avaliar os resultados da reparametrização e atualizar a API do serviço Web para utilizar o modelo treinado recentemente.

> [!NOTE]
> Se tiver uma experimentação de preparação existentes e do serviço Web novas, poderá pretender verificar reparametrização de um serviço Web preditiva existente em vez de seguir as instruções mencionadas na secção seguinte.
> 
> 

## <a name="end-to-end-workflow"></a>Fluxo de trabalho ponto a ponto
O processo de envolve os seguintes componentes: uma experimentação de preparação e de uma experimentação preditiva publicado como um serviço Web. Para ativar a reparametrização de um modelo preparado, a experimentação de preparação tem de ser publicada como um serviço Web com o resultado de um modelo preparado. Isto permite o acesso a API para o modelo para a reparametrização. 

Os seguintes passos aplicam-se aos serviços novo e Web clássicos:

Crie o serviço Web preditiva inicial:

* Criar uma experimentação de preparação
* Criar uma experimentação preditiva web
* Implementar um serviço web preditiva

Reparametrização do serviço Web:

* Experimentação de preparação de atualização para permitir a reparametrização
* Implementar o serviço web reparametrização
* Utilizar o código do serviço de execução de Batch para a reparametrização do modelo

Para obter instruções sobre precedente os passos, consulte [Machine Learning reparametrização dos modelos programáticos](retrain-models-programmatically.md).

> [!NOTE] 
> Para implementar um novo serviço web tem de ter permissões suficientes na subscrição aos quais pode implementar o serviço web. Para obter mais informações, consulte [gira um serviço Web utilizando o portal de serviços Web do Azure Machine Learning](manage-new-webservice.md). 

Se tiver implementado um serviço Web clássico:

* Criar um novo ponto final do serviço Web preditiva
* Obter o URL de aplicar o PATCH e o código
* Utilizar o URL de aplicar o PATCH para apontar o novo ponto final para o modelo retrained 

Para obter instruções sobre precedente os passos, consulte [reparametrização de um serviço Web clássico](retrain-a-classic-web-service.md).

Caso se depare com dificuldades reparametrização um serviço Web clássico, consulte [resolução de problemas a reparametrização de um serviço Web clássico do Azure Machine Learning](troubleshooting-retraining-models.md).

Se tiver implementado um serviço Web novo:

* Inicie sessão na sua conta do Azure Resource Manager
* Obter a definição de serviço Web
* Exportar a definição de serviço Web como JSON
* Atualizar a referência para o `ilearner` blob no JSON
* Importar o JSON para uma definição de serviço Web
* Atualizar o serviço Web com a nova definição de serviço Web

Para obter instruções sobre precedente os passos, consulte [reparametrização de um serviço Web de novo utilizando os cmdlets do PowerShell de gestão do Machine Learning](retrain-new-web-service-using-powershell.md).

O processo para configurar a reparametrização para um serviço Web clássico envolve os seguintes passos:

![Reparametrização descrição geral do processo][1]

O processo para configurar a reparametrização para um serviço Web novo envolve os seguintes passos:

![Reparametrização descrição geral do processo][7]

## <a name="other-resources"></a>Outros Recursos
* [Modelos reparametrização e atualizar o Azure Machine Learning com o Azure Data Factory](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/)
* [Criar vários modelos de Machine Learning e web pontos finais de serviço a partir de uma experimentação através do PowerShell](create-models-and-endpoints-with-powershell.md)
* O [AML reparametrização modelos utilizando APIs](https://www.youtube.com/watch?v=wwjglA8xllg) vídeo mostra como reparametrização dos modelos de Machine Learning criados no Azure Machine Learning usando a reparametrização APIs e PowerShell.

<!--image links-->
[1]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png

