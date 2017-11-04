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
ms.openlocfilehash: 821ad87fc10b2380e5ed89c037c335bc7747009e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="creating-endpoints"></a>Criar Pontos Finais
> [!NOTE]
>  Este tópico descreve as técnicas aplicáveis a um **clássico** serviço Web do Machine Learning.
> 
> 

Quando criar serviços Web que lhe propor reencaminhar para os seus clientes, terá de fornecer modelos de formação para cada cliente que ainda estão ligados a experimentação a partir do qual o serviço Web foi criado. Além disso, as atualizações para a experimentação devem ser aplicadas seletivamente para um ponto final sem substituir as personalizações.

Para tal, o Azure Machine Learning permite-lhe criar vários pontos finais para um serviço Web implementado. Cada ponto final de serviço Web independentemente é resolvido, limitado e gerido. Cada ponto final é um URL único e a chave de autorização que pode distribuir aos seus clientes.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="adding-endpoints-to-a-web-service"></a>Adicionar pontos finais para um serviço Web
Existem três maneiras de adicionar um ponto final para um serviço Web.

* Através de Programação
* Através do portal de serviços Web do Azure Machine Learning
* Apesar do portal clássico do Azure

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

## <a name="adding-an-endpoint-using-the-azure-classic-portal"></a>Adicionar um ponto final utilizando o portal clássico do Azure
1. Iniciar sessão para o [portal clássico do Azure](http://manage.windowsazure.com), clique em **Machine Learning** na coluna esquerda. Clique em da área de trabalho que contém o serviço Web na qual está interessado.
   
    ![Navegue até à área de trabalho](./media/create-endpoint/figure-1.png)
2. Clique em **serviços Web**.
   
    ![Navegue para serviços Web](./media/create-endpoint/figure-2.png)
3. Clique no serviço de Web que está interessado para ver a lista de pontos finais disponíveis.
   
    ![Navegue até ao ponto final](./media/create-endpoint/figure-3.png)
4. Na parte inferior da página, clique em **adicionar ponto final**. Escreva um nome e uma descrição, certifique-se de que existem não existem pontos finais com o mesmo nome neste serviço Web. Deixe o nível de limitação com o respetivo valor predefinido, a menos que tenha requisitos especiais. Para obter mais informações sobre limitação, consulte [Dimensionar pontos finais da API](scaling-webservice.md).
   
    ![Criar o ponto final](./media/create-endpoint/figure-4.png)

## <a name="next-steps"></a>Passos Seguintes
[Como consumir um serviço Web do Azure Machine Learning](consume-web-services.md).

