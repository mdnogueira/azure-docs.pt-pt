---
title: "Criar uma área de trabalho do Machine Learning | Microsoft Docs"
description: "Como criar uma área de trabalho para o Azure Machine Learning Studio"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: aa96b784-ac6c-44bc-a28a-85d49fbe90a2
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: garye;bradsev;ahgyger
ms.openlocfilehash: 4e1fa0a9abd4721d15a94923263ff2f521bceee8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-share-an-azure-machine-learning-workspace"></a>Criar e partilhar uma área de trabalho do Azure Machine Learning
Ligações neste menu para tópicos que descrevem como configurar os vários ambientes de ciência de dados utilizados pelo processo de análise do Cortana (CAPS).

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

Para utilizar o Azure Machine Learning Studio, tem de ter uma área de trabalho do Machine Learning. Esta área de trabalho contém as ferramentas que necessárias para criar, gerir e publicar experimentações.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

### <a name="to-create-a-workspace"></a>Para criar uma área de trabalho
1. Iniciar sessão para o [portal do Azure](https://portal.azure.com/)

    > [!NOTE]
    > Para iniciar sessão e criar uma área de trabalho, tem de ser um administrador de subscrição do Azure. 
    >
    > 

2. Clique em **+ nova**

3. Selecione **Intelligence + análise**, clique em **área de trabalho do Machine Learning**, em seguida, clique em **criar**

4. Introduza as informações da sua área de trabalho

    - O *nome da área de trabalho* poderá até 260 carateres, não que termine num espaço. O nome não pode incluir os seguintes carateres:`< > * % & : \ ? + /`
    - O *plano de serviço web* escolher (ou crie), juntamente com associada *escalão de preço* selecione, é utilizada se implementar serviços web desta área de trabalho.

    ![Criar uma nova área de trabalho](./media/create-workspace/create-new-workspace.png)

5. Clique em **Criar**.

Depois da área de trabalho for implementada, pode abri-lo no Machine Learning Studio.

1. Procurar ao Machine Learning Studio no [https://studio.azureml.net/](https://studio.azureml.net/).

2. Selecione a sua área de trabalho no canto superior-direito-mão.

    ![Selecione a área de trabalho](./media/create-workspace/open-workspace.png)

3. Clique em **as minhas experimentações**.

    ![Abra experimentações](./media/create-workspace/my-experiments.png)

Para obter informações sobre como gerir a sua área de trabalho, consulte [gerir uma área de trabalho do Azure Machine Learning](manage-workspace.md).
Se ocorrer um problema ao criar a sua área de trabalho, consulte [guia de resolução de problemas: criar e ligar a uma área de trabalho do Machine Learning](troubleshooting-creating-ml-workspace.md).


## <a name="sharing-an-azure-machine-learning-workspace"></a>Partilha de uma área de trabalho do Azure Machine Learning
Uma vez Machine Learning é criada a área de trabalho, pode convidar os utilizadores à sua área de trabalho para partilhar o acesso à sua área de trabalho e todos os respetivos experimentações, conjuntos de dados, blocos de notas, etc. Pode adicionar utilizadores de uma das seguintes duas funções:

* **Utilizador** -um utilizador de área de trabalho pode criar, abrir, modificar e eliminar experimentações, conjuntos de dados, etc. na área de trabalho.
* **Proprietário** - pode convidar um proprietário e remover utilizadores na área de trabalho, para além do que um utilizador podem fazer.

> [!NOTE]
> A conta de administrador que cria a área de trabalho é automaticamente adicionada à área de trabalho como proprietário da área de trabalho. No entanto, os outros administradores ou utilizadores nessa subscrição não automaticamente concedidos acesso à área de trabalho - necessário convidá-los explicitamente.
> 
> 

### <a name="to-share-a-workspace"></a>Para partilhar uma área de trabalho

1. Inicie sessão no Machine Learning Studio no [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. No painel esquerdo, clique em **definições**

3. Clique em de **utilizadores** separador

4. Clique em **CONVIDAR utilizadores mais** na parte inferior da página

    ![Definições do Studio](./media/create-workspace/settings.png)

5. Introduza um ou mais endereços de e-mail. Os utilizadores necessitam de uma conta Microsoft válida ou uma conta institucional (a partir do Azure Active Directory).

6. Selecione se pretende adicionar os utilizadores como proprietário ou utilizador.

7. Clique em de **OK** botão de marca de verificação.

Cada utilizador que adiciona irão receber um e-mail com instruções sobre como iniciar sessão para a área de trabalho partilhada.

> [!NOTE]
> Para que os utilizadores possam implementar ou gerir os serviços web nesta área de trabalho, têm de ser um Contribuidor ou o administrador da subscrição do Azure. 



