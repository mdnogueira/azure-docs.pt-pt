---
title: "O registo para serviços web do Machine Learning | Microsoft Docs"
description: "Saiba como ativar o registo de serviços web do Machine Learning. O registo fornece informações adicionais para ajudar a resolver as APIs."
services: machine-learning
documentationcenter: 
author: raymondlaghaeian
manager: jhubbard
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/15/2017
ms.author: raymondl;garye
ms.openlocfilehash: 31497bcae5889590a2261b716574ec4eae21852d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-logging-for-machine-learning-web-services"></a>Ativar o registo para os serviços Web do Machine Learning
Este documento fornece informações sobre a capacidade de registos de serviços web do Machine Learning. O registo fornece informações adicionais, para além de apenas um número de erro e uma mensagem, que pode ajudar a resolver problemas relacionados com as chamadas para as APIs do Machine Learning.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Como ativar o registo de um serviço Web

Ativar o registo do [serviços Web do Azure Machine Learning](https://services.azureml.net) portal. 

1. Inicie sessão no portal de serviços Web do Azure Machine Learning em [https://services.azureml.net](https://services.azureml.net). Para um serviço web clássico, também pode obter no portal, clicando em **nova experiência de serviços Web** na página Serviços Web Machine Learning no Machine Learning Studio.

   ![Nova ligação de experiência de serviços Web](./media/web-services-logging/new-web-services-experience-link.png)

2. Na barra de menu superior, clique em **serviços Web** para um novo serviço web, ou clique em **serviços Web clássicos** para um clássico de serviço web.

   ![Selecione o novo ou clássico serviços web](./media/web-services-logging/select-web-service.png)

3. Para um novo serviço web, clique no nome de serviço web. Para um serviço web clássico, clique no nome de serviço web e, em seguida, na página seguinte, clique no ponto de final adequado.

4. Na barra de menu superior, clique em **configurar**.

5. Definir o **ativar registo** opção para *erro* (para iniciar sessão apenas erros) ou *todos os* (para o registo completo).

   ![Selecione o nível de registo](./media/web-services-logging/enable-logging.png)

6. Clique em **Guardar**.

7. Para os serviços web clássico, crie o **ml diagnóstico** contentor.

   Todos os registos do serviço web são mantidos num contentor do blob denominado **ml diagnóstico** na conta de armazenamento associada ao serviço web. Para novos serviços web, este contentor é criado pela primeira vez, que aceder ao serviço web. Para os serviços web clássico, terá de criar o contentor se ainda não existir. 

   1. No [portal do Azure](https://portal.azure.com), vá para a conta de armazenamento associada ao serviço web.

   2. Em **serviço Blob**, clique em **contentores**.

   3. Se o contentor **ml diagnóstico** não existe, clique em **+ contentor**, dê o contentor o nome "ml-diagnóstico" e selecione o **aceder tipo** como "Blob". Clique em **OK**.

      ![Selecione o nível de registo](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Para um serviço web clássico, o Dashboard de serviços Web do Machine Learning Studio tem também um comutador para ativar o registo. No entanto, porque o registo está agora gerido através do portal de serviços Web, terá de ativar o registo através do portal conforme descrito neste artigo. Se já tiver ativado o registo no Studio, em seguida, no Portal de serviços Web, desative o registo e volte a ativar.


## <a name="the-effects-of-enabling-logging"></a>Os efeitos de ativar o registo
Quando o registo está ativado, os diagnósticos e erros de ponto final do serviço web são registados no **ml diagnóstico** contentor de BLOBs na conta do Storage do Azure ligado com área de trabalho do utilizador. Este contentor contém todas as informações de diagnóstico para todos os as web pontos finais de serviço para todas as áreas de trabalho associados a esta conta de armazenamento.

Os registos podem ser visualizados com qualquer uma das várias ferramentas disponíveis para explorar uma conta de armazenamento do Azure. O mais fácil poderá navegar para a conta do storage no portal do Azure, clique em **contentores**e, em seguida, clique em contentor **ml diagnóstico**.  

## <a name="log-blob-detail-information"></a>Informações de detalhe do registo blob
Cada BLOBs no contentor contém as informações de diagnóstico para exatamente uma das seguintes ações:

* Uma execução do método de execução de lote  
* Uma execução do método de pedido-resposta  
* Inicialização de um contentor de resposta-pedido

O nome de cada blob tem um prefixo com o seguinte formato: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Onde _tipo de registo_ é um dos seguintes valores:  

* Batch  
* pontuação/pedidos  
* pontuação/init  

