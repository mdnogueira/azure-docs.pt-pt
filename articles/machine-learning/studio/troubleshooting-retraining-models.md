---
title: "Resolver problemas de reparametrização um serviço web clássico do Azure Machine Learning | Microsoft Docs"
description: "Identificar e corrigir encontrou de problemas comuns quando são reparametrização o modelo para um serviço Web do Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: VDonGlover
manager: raymondl
editor: 
ms.assetid: 75cac53c-185c-437d-863a-5d66d871921e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: v-donglo
ms.openlocfilehash: 85cf9175bb4a5f253c7b47b2edc3ac8b00616ba2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-classic-web-service"></a>Resolução de problemas a reparametrização de um serviço Web clássico do Azure Machine Learning
## <a name="retraining-overview"></a>Reparametrização descrição geral
Ao implementar uma experimentação preditiva como um serviço web classificação é um modelo estático. Como novos dados ficarem disponíveis ou o consumidor da API tem os seus próprios dados, o modelo precise de ser retrained. 

Para instruções completas do processo reparametrização de um serviço Web clássico, consulte [reparametrização dos Machine Learning modelos através de programação](retrain-models-programmatically.md).

## <a name="retraining-process"></a>Reparametrização processo
Quando precisar de reparametrização do serviço Web, tem de adicionar algumas partes adicionais:

* Um serviço Web implementado da experimentação de preparação. A experimentação tem de ter um **saída de serviço Web** módulo ligado à saída do **preparar modelo** módulo.  
  
    ![Anexe a saída de serviço web para o modelo de formação.][image1]
* Um novo ponto de final adicionado ao seu serviço Web classificação.  Pode adicionar o ponto final através da programação com o código de exemplo referenciado nos modelos de Machine Learning reparametrização dos programaticamente tópico ou através do portal clássico do Azure.

Em seguida, pode utilizar o c# código de exemplo da página de ajuda de API do serviço de Web de formação para a reparametrização dos modelo. Depois de ter a avaliar os resultados e estiver satisfeito com os mesmos, atualizar o modelo treinado do serviço web utilizando o novo ponto final que adicionou de classificação.

Com todas as peças no local, os principais passos que deve efetuar para a reparametrização dos modelo são:

1. Chamar o serviço Web de preparação: A chamada é para o Batch execução do serviço (BES), não o pedido de resposta de serviço (RRS). Pode utilizar o c# código de exemplo na página de ajuda de API para efetuar a chamada. 
2. Localizar os valores para o *BaseLocation*, *RelativeLocation*, e *SasBlobToken*: estes valores são apresentados no resultado da sua chamada para o serviço Web de formação. 
   ![Mostrar a saída de exemplo reparametrização e os valores BaseLocation, RelativeLocation e SasBlobToken.][image6]
3. Atualizar o ponto de final adicionado do serviço web de classificação com o novo modelo treinado: utilizando o código de exemplo fornecido nos modelos de Machine Learning reparametrização dos programaticamente, atualize o novo ponto final que adicionou ao modelo de classificação com o modelo treinado recentemente do Serviço Web de formação.

## <a name="common-obstacles"></a>Obstacles comuns
### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>Verifique se tem o URL está correto
O URL de aplicar o PATCH de mensagens em fila que estão a utilizar tem de ser um associado a classificação novo ponto final que adicionou ao serviço Web de classificação. Existem várias formas de obter o URL de aplicar o PATCH:

**Opção 1: X509securitytokenparameters**

Para obter o URL está correto:

1. Execute o [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) código de exemplo.
2. Da saída das AddEndpoint, localizar o *HelpLocation* valor e copie o URL.
   
   ![HelpLocation na saída de exemplo addEndpoint.][image2]
3. Cole o URL de um browser para navegar para uma página que fornece ligações de ajuda para o serviço Web.
4. Clique em de **atualização recursos** ligação para abrir a página de ajuda do patch.

**Opção 2: Utilizar o portal clássico do Azure**

1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. Abra o separador de Machine Learning. ![Separador leaning da máquina.][image4]
3. Clique no nome de área de trabalho, em seguida, **serviços Web**.
4. Clique no serviço de Web de classificação que está a funcionar. (Se não foi possível modificar o nome predefinido do serviço web, vai terminar dentro de [classificação Exp]..)
5. Clique em **adicionar ponto final**.
6. Depois do ponto final é adicionado, clique no nome de ponto final. Em seguida, clique em **atualização recursos** para abrir a página de ajuda de aplicação de patches.

> [!NOTE]
> Se adicionou o ponto final para o serviço Web de formação em vez do serviço Web preditiva, receberá o erro seguinte ao clicar no **recursos de atualização** ligação: Lamentamos, mas esta funcionalidade não é suportada ou disponíveis neste contexto. Este serviço Web tem não existem recursos atualizáveis. Iremos desculpa pelo incómodo e a trabalhar para melhorar este fluxo de trabalho.
> 
> 

![Dashboard de ponto final de novo.][image3]

A página de ajuda de PATCH contém o URL de aplicar o PATCH de mensagens em fila tem de utilizar e fornece o código de exemplo, que pode utilizar para chamá-lo.

![URL de patch.][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>Certifique-se que estão a atualizar o ponto final de classificação correto
* Não aplicar o patch do serviço Web de preparação: A operação de patch tem de ser efetuada do serviço Web classificação.
* Não aplicar o patch o ponto final predefinido no serviço Web: A operação de patch tem de ser efetuada na nova classificação Web ponto final do serviço que adicionou.

Pode verificar o serviço Web, o ponto final está ativada por visitando o portal clássico do Azure. 

> [!NOTE]
> Lembre-se de que está a adicionar o ponto final para o serviço Web preditiva, não o serviço Web de formação. Se tiver implementado corretamente uma formação e um serviço Web preditiva, deverá ver dois serviços Web separados listados. O serviço Web preditiva deve terminar com "[exp preditiva.]".
> 
> 

1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. Abra o separador de Machine Learning. ![Machine learning área de trabalho da IU.][image4]
3. Selecione a sua área de trabalho.
4. Clique em **serviços Web**.
5. Selecione o seu serviço Web preditiva.
6. Certifique-se de que o novo ponto final foi adicionado ao serviço Web.

### <a name="check-the-workspace-that-your-web-service-is-in-to-ensure-it-is-in-the-correct-region"></a>Verifique a área de trabalho que o serviço web está a ser está na região correta
1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. Selecione Machine Learning a partir do menu.
   ![Machine learning região da IU.][image4]
3. Certifique-se a localização da sua área de trabalho.

<!-- Image Links -->

[image1]: ./media/troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/troubleshooting-retraining-a-model/azure-portal-machine-learning-tab.png
[image5]: ./media/troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/troubleshooting-retraining-a-model/web-services-tab.png
