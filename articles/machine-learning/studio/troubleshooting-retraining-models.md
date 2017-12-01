---
title: "Resolver problemas de reparametrização um serviço web clássico do Azure Machine Learning | Microsoft Docs"
description: "Identificar e corrigir encontrou de problemas comuns quando são reparametrização o modelo para um serviço Web do Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: garyericson
manager: raymondl
editor: 
ms.assetid: 75cac53c-185c-437d-863a-5d66d871921e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 011/01/2017
ms.author: garye
ms.openlocfilehash: 1e5327ad135d9bc8881354679dc3f1b8a472cad3
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-classic-web-service"></a>Resolução de problemas a reparametrização de um serviço web clássico do Azure Machine Learning
## <a name="retraining-overview"></a>Reparametrização descrição geral
Ao implementar uma experimentação preditiva como um serviço web classificação é um modelo estático. Como novos dados ficarem disponíveis ou o consumidor da API tem os seus próprios dados, o modelo precise de ser retrained. 

Para instruções completas do processo de reparametrização de um serviço de web clássico, consulte [reparametrização dos Machine Learning modelos através de programação](retrain-models-programmatically.md).

## <a name="retraining-process"></a>Reparametrização processo
Quando precisar de reparametrização do serviço web, tem de adicionar algumas partes adicionais:

* Um serviço web implementado da experimentação de preparação. A experimentação tem de ter um **saída de serviço Web** módulo ligado à saída do **preparar modelo** módulo.  
  
    ![Anexe a saída de serviço web para o modelo de formação.][image1]
* Um novo ponto de final adicionado ao seu serviço web classificação.  Pode adicionar o ponto final através da programação com o código de exemplo referenciado nos modelos de Machine Learning reparametrização dos programaticamente tópico ou através do portal de serviços Web do Azure Machine Learning.

Em seguida, pode utilizar o c# código de exemplo da página de ajuda de API do serviço de Web de formação para a reparametrização dos modelo. Depois de ter a avaliar os resultados e estiver satisfeito com os mesmos, atualizar o modelo treinado do serviço web utilizando o novo ponto final que adicionou de classificação.

Com todas as peças no local, os principais passos que deve efetuar para a reparametrização dos modelo são:

1. Chamar o serviço Web de preparação: A chamada é para o Batch execução do serviço (BES), não o pedido de resposta de serviço (RRS). Pode utilizar o c# código de exemplo na página de ajuda de API para efetuar a chamada. 
2. Localizar os valores para o *BaseLocation*, *RelativeLocation*, e *SasBlobToken*: estes valores são apresentados no resultado da sua chamada para o serviço Web de formação. 
   ![Mostrar a saída de exemplo reparametrização e os valores BaseLocation, RelativeLocation e SasBlobToken.][image6]
3. Atualizar o ponto de final adicionado do serviço web de classificação com o novo modelo treinado: utilizando o código de exemplo fornecido nos modelos de Machine Learning reparametrização dos programaticamente, atualize o novo ponto final que adicionou ao modelo de classificação com o modelo treinado recentemente do Serviço Web de formação.

## <a name="common-obstacles"></a>Obstacles comuns
### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>Verifique se tem o URL está correto
O URL de aplicar o PATCH de mensagens em fila que estão a utilizar tem de ser um associado a classificação novo ponto final que adicionou ao serviço web de classificação. Existem várias formas de obter o URL de aplicar o PATCH:

**Opção 1: X509securitytokenparameters**

Para obter o URL está correto:

1. Execute o [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) código de exemplo.
2. Da saída das AddEndpoint, localizar o *HelpLocation* valor e copie o URL.
   
   ![HelpLocation na saída de exemplo addEndpoint.][image2]
3. Cole o URL de um browser para navegar para uma página que fornece ligações de ajuda para o serviço web.
4. Clique em de **atualização recursos** ligação para abrir a página de ajuda do patch.

**Opção 2: Utilizar o portal de serviços Web do Azure Machine Learning**

1. Iniciar sessão para o [serviços Web do Azure Machine Learning](https://services.azureml.net/) portal.
2. Clique em **serviços Web** ou **serviços Web clássicos** na parte superior.
4. Clique no serviço web classificação estiver a trabalhar com (se não modificar o nome predefinido do serviço web, vai terminar dentro de "[Scoring Exp.]").
5. Clique em **+ novo**.
6. Depois do ponto final é adicionado, clique no nome de ponto final.
7. Sob o **Patch** URL, clique em **API ajuda** para abrir a página de ajuda de aplicação de patches.

> [!NOTE]
> Se adicionou o ponto final para o serviço Web de formação em vez do serviço Web preditiva, receberá o erro seguinte ao clicar no **atualização recursos** ligação: "Lamentamos, mas esta funcionalidade não é suportada ou disponível no Neste contexto. Este serviço Web tem não existem recursos atualizáveis. Iremos desculpa pelo incómodo e a trabalhar para melhorar este fluxo de trabalho."
> 
> 

A página de ajuda de PATCH contém o URL de aplicar o PATCH de mensagens em fila tem de utilizar e fornece o código de exemplo, que pode utilizar para chamá-lo.

![URL de patch.][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>Certifique-se que estão a atualizar o ponto final de classificação correto
* Não aplicar o patch do serviço web de preparação: A operação de patch tem de ser efetuada do serviço web classificação.
* Não aplicar o patch o ponto final predefinido do serviço web: A operação de patch tem de ser efetuada na nova classificação web ponto final do serviço que adicionou.

Pode verificar o serviço web, o ponto final está ativada por aceder ao portal de serviços Web. 

> [!NOTE]
> Lembre-se de que está a adicionar o ponto final para o serviço Web preditiva, não o serviço Web de formação. Se tiver implementado corretamente uma formação e um serviço Web preditiva, deverá ver dois serviços de web separado listados. O serviço Web preditiva deve terminar com "[exp preditiva.]".
> 
> 

1. Iniciar sessão para o [serviços Web do Azure Machine Learning](https://services.azureml.net/) portal.
2. Clique em **serviços Web** ou **serviços Web clássicos**.
3. Selecione o seu serviço Web preditiva.
4. Certifique-se de que o novo ponto final foi adicionado ao serviço web.

### <a name="check-that-your-workspace-is-in-the-same-region-as-the-web-service"></a>Verifique se a sua área de trabalho está na mesma região que o serviço web
1. Inicie sessão no [Machine Learning Studio](https://studio.azureml.net/).
2. Na parte superior, clique na lista pendente da sua áreas de trabalho.

   ![Machine learning região da IU.][image4]

3. Certifique-se a região na sua área de trabalho.

<!-- Image Links -->

[image1]: ./media/troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/troubleshooting-retraining-a-model/check-workspace-region.png
[image5]: ./media/troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/troubleshooting-retraining-a-model/web-services-tab.png
