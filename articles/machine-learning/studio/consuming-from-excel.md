---
title: "Consumir um serviço Web do Machine Learning a partir do Excel | Microsoft Docs"
description: "Consumir um serviço Web do Azure Machine Learning a partir do Excel"
services: machine-learning
documentationcenter: 
author: tedway
manager: jhubbard
editor: cgronlun
ms.assetid: 3f3cdd2f-1816-487e-ab78-530e01e9788f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/13/2017
ms.author: tedway
ms.openlocfilehash: 9a8b39853e1a90815758af1d8fd772db7cc18ac5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="consuming-an-azure-machine-learning-web-service-from-excel"></a>Consumir um serviço Web do Azure Machine Learning a partir do Excel
 Azure Machine Learning Studio facilita chamar serviços web diretamente a partir do Excel sem a necessidade de escrever qualquer código.

Se estiver a utilizar o Excel 2013 (ou posterior) ou o Excel Online, em seguida, recomendamos que utilize o Excel [suplemento do Excel](excel-add-in-for-web-services.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="steps"></a>Passos
Publica um serviço web. [Esta página](walkthrough-5-publish-web-service.md) explica como fazê-lo. Atualmente, a funcionalidade de livro do Excel só é suportada para os serviços de pedido/resposta com uma saída único (ou seja, uma classificação simples). 

Depois de ter um serviço web, clique em de **serviços WEB** secção à esquerda do studio e, em seguida, selecione o serviço web para consumir a partir do Excel.

**Serviço Web clássico**

1. No **DASHBOARD** separador para o serviço web é uma linha para o **pedido/resposta** serviço. Se este serviço tinha de saída única, deverá ver o **Transferir livro do Excel** ligação nessa linha.
   
    ![][1]
2. Clique em **transferir o livro do Excel**.

**Novo serviço Web**

1. No portal do serviço Web do Azure Machine Learning, selecione **Consume**.
2. Na página Consume, no **Web opções de consumo do serviço** secção, clique no ícone do Excel.

**Utilizar o livro**

1. Abra o livro.
2. É apresentado um aviso de segurança; Clique em de **ativar editar** botão.
   
    ![][2]
3. É apresentado um aviso de segurança. Clique em de **ativar conteúdo** botão para executar macros no seu folha de cálculo.
   
    ![][3]
4. Depois de macros estiverem ativadas, é gerada uma tabela. As colunas na azul são necessárias como entrada para o serviço web RRS, ou **parâmetros**. Tenha em atenção a saída do serviço RRS, **PREVER valores** verde. Quando todas as colunas para uma determinada linha estão preenchidas, o livro automaticamente chama a API de classificação e apresenta os resultados classificados.
   
    ![][4]
5. Para mais de uma linha de pontuação, são produzidos preenchimento a segunda linha com dados e os valores previstos. Ainda pode colar linhas várias em simultâneo.

Pode utilizar qualquer uma das funcionalidades Excel (gráficos, mapa de energia, formatação condicional, etc.) com os valores previstos para ajudar a visualizar os dados.    

## <a name="sharing-your-workbook"></a>Partilhar o seu livro
Para as macros funcione, a chave de API têm de constar da folha de cálculo. Isto significa que devem partilhar o livro apenas com entidades/indivíduos que confia.

## <a name="automatic-updates"></a>Atualizações automáticas
É efetuada uma chamada RRS nestas duas situações:

1. Na primeira vez uma linha possui conteúdo em todos os respetivos **parâmetros**
2. Sempre que qualquer um do **parâmetros** alterações numa linha que tinha todos os respetivos **parâmetros** introduzido.

[1]: ./media/consuming-from-excel/excellink.png
[2]: ./media/consuming-from-excel/enableeditting.png
[3]: ./media/consuming-from-excel/enablecontent.png
[4]: ./media/consuming-from-excel/sampletable.png
