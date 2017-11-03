---
title: "Excel suplemento para serviços Web do Machine Learning | Microsoft Docs"
description: "Como utilizar os serviços Web do Azure Machine Learning diretamente no Excel sem escrever qualquer código."
services: machine-learning
documentationcenter: 
author: tedway
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 9618079d-502f-4974-a3e2-8f924042a23f
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/14/2017
ms.author: tedway;garye
ms.openlocfilehash: 4dbb1779bf36be74ee83aeed8e4940902915603a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="excel-add-in-for-azure-machine-learning-web-services"></a>Suplemento do Excel para os serviços Web do Azure Machine Learning
Excel torna mais fácil chamar serviços web diretamente sem a necessidade de escrever qualquer código.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Passos a utilizar um serviço de web existente no livro

1. Abra o [ficheiro do Excel de exemplo](http://aka.ms/amlexcel-sample-2), que contém o suplemento do Excel e os dados sobre passageiros no Titanic.
2. Escolha o serviço web clicando nela-"previsão da receita Survivor Titanic (suplemento do Excel exemplo) [pontuação]" neste exemplo.
   
    ![Selecionar serviço Web][01]
3. Isto leva-o para o **Predict** secção.  Este livro já contém dados de exemplo, mas para um livro em branco que pode selecionar uma célula no Excel e clique em **utilizar dados de exemplo**.
4. Selecione os dados com cabeçalhos e clique no ícone de intervalo de dados de entrada.  Certifique-se de que marca a caixa "os meus dados tem cabeçalhos".
5. Em **saída**, introduza o número de células onde pretende que a saída para ser, por exemplo "H1" aqui.
6. Clique em **prever**.
   
    ![Secção de prever][02]

Implementar um serviço web ou utilizar um serviço Web existente. Para obter mais informações sobre como implementar um serviço web, consulte [instruções passo 5: implementar o serviço Web do Azure Machine Learning](walkthrough-5-publish-web-service.md).

Obter a chave de API para o seu serviço web. Sempre que efetuar esta ação depende se publicou um serviço web do Machine Learning clássico de um serviço web do Machine Learning novo.

**Utilizar um serviço de web clássico** 

1. No Machine Learning Studio, clique em de **serviços WEB** secção no painel esquerdo e, em seguida, selecione o serviço web.
   
    ![Selecione Studio um serviço Web][04]
2. Copie a chave de API para o serviço web.
   
    ![Chave de API do Studio][05]
3. No **DASHBOARD** separador para o serviço web, clique em de **pedido/resposta** ligação.
4. Procure o **URI pedido** secção.  Copie e guarde o URL.

> [!NOTE]
> É agora possível iniciar sessão no [serviços Web do Azure Machine Learning](https://services.azureml.net) portal para obter a chave de API para um serviço web clássico Machine Learning.
> 
> 

**Utilize um novo serviço web**

1. No [serviços Web do Azure Machine Learning](https://services.azureml.net) portal, clique em **serviços Web**, em seguida, selecione o seu serviço web. 
2. Clique em **consumir**.
3. Procure o **informações de consumo básico** secção. Copie e guarde o **chave primária** e **pedido-resposta** URL.

## <a name="steps-to-add-a-new-web-service"></a>Passos para adicionar um novo serviço web

1. Implementar um serviço web ou utilizar um serviço Web existente. Para obter mais informações sobre como implementar um serviço web, consulte [instruções passo 5: implementar o serviço Web do Azure Machine Learning](walkthrough-5-publish-web-service.md).
2. Clique em **consumir**.
3. Procure o **informações de consumo básico** secção. Copie e guarde o **chave primária** e **pedido-resposta** URL.
4. No Excel, aceda ao **serviços Web** secção (se estiver no **Predict** secção, clique na seta back para aceder à lista de serviços web).
   
    ![Aceda a seleção de serviço Web][03]
5. Clique em **Adicionar serviço Web**.
6. Cole o URL com a etiqueta caixa de texto do suplemento do Excel **URL**.
7. Cole a chave de API/primária a caixa de texto com etiqueta **chave de API**.
8. Clique em **Adicionar**.
   
    ![Chave de URL e a API para um serviço Web clássico.][06]
9. Para utilizar o serviço web, siga as instruções anteriores, "Passos a utilizar um serviço de web existentes."

## <a name="sharing-your-workbook"></a>Partilhar o seu livro
Se guardar o livro, a chave de API/principal para os serviços web que adicionou também é guardada. Isto significa que só devem partilhar o livro com indivíduos que confia.

Faça as perguntas na secção seguinte comentário ou no nosso [fórum](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).

[01]: ./media/excel-add-in-for-web-services/image1.png
[02]: ./media/excel-add-in-for-web-services/image2.png
[03]: ./media/excel-add-in-for-web-services/image3.png
[04]: ./media/excel-add-in-for-web-services/image4.png
[05]: ./media/excel-add-in-for-web-services/image5.png
[06]: ./media/excel-add-in-for-web-services/image6.png
