---
title: "A criação de pontos finais do serviço Web no Machine Learning | Microsoft Docs"
description: "A criação de pontos finais do serviço Web no Azure Machine Learning"
services: machine-learning
documentationcenter: 
author: hiteshmadan
manager: padou
editor: cgronlun
ms.assetid: 4657fc1b-5228-4950-a29e-bc709259f728
ms.service: machine-learning
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 10/04/2016
ms.author: himad
ms.openlocfilehash: 6de83042779a1a4edae57499f108dcddc9d68309
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="creating-endpoints"></a>Criação de pontos finais
> [!NOTE]
>  Este tópico descreve as técnicas aplicáveis a um **clássico** serviço Web do Machine Learning.
> 
> 

Quando criar serviços Web que lhe propor reencaminhar para os seus clientes, terá de fornecer modelos de formação para cada cliente que ainda estão ligados a experimentação a partir do qual o serviço Web foi criado. Além disso, as atualizações para a experimentação devem ser aplicadas seletivamente para um ponto final sem substituir as personalizações.

Para tal, o Azure Machine Learning permite-lhe criar vários pontos finais para um serviço Web implementado. Cada ponto final de serviço Web independentemente é resolvido, limitado e gerido. Cada ponto final é um URL único e a chave de autorização que pode distribuir aos seus clientes.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="adding-endpoints-to-a-web-service"></a>Adicionar pontos finais para um serviço Web
Existem duas formas de adicionar um ponto final para um serviço Web.

* Através de Programação
* Através do portal de serviços Web do Azure Machine Learning

Quando o ponto final for criado, pode consumi-lo através de APIs síncronas, batch APIs e folhas de cálculo do excel. Para além de adicionar pontos finais através deste IU, também pode utilizar as APIs de gestão do ponto final para programaticamente adicionar pontos finais.

> [!NOTE]
> Se tiver adicionado os pontos finais adicionais para o serviço Web, não é possível eliminar o ponto final predefinido.
> 
> 

## <a name="adding-an-endpoint-programmatically"></a>Adicionar um ponto final através de programação
Pode adicionar um ponto final ao seu serviço Web através de programação utilizando o [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) código de exemplo.

## <a name="adding-an-endpoint-using-the-azure-machine-learning-web-services-portal"></a>Adicionar um ponto final com o portal de serviços Web do Azure Machine Learning
1. No Machine Learning Studio, a coluna de navegação esquerdo, clique em serviços Web.
2. Na parte inferior do dashboard do serviço Web, clique em **gerir pontos finais**. O portal de serviços Web do Azure Machine Learning abre-se para a página pontos finais para o serviço Web.
3. Clique em **Novo**.
4. Escreva um nome e descrição para o novo ponto final. Os nomes de ponto final tem de ser 24 caráter ou menos de comprimento e devem ser constituídos por europeus minúsculas ou números. Selecione o nível de registo e se os dados de exemplo estão ativados. Para obter mais informações sobre o registo, consulte [ativar o registo de serviços Web do Machine Learning](web-services-logging.md).

## <a name="next-steps"></a>Passos seguintes
[Como consumir um serviço Web do Azure Machine Learning](consume-web-services.md).

