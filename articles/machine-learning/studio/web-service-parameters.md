---
title: "Utilizar o Azure Machine Learning parâmetros de serviço Web | Microsoft Docs"
description: "Como utilizar os parâmetros de serviço Web do Azure Machine Learning para modificar o comportamento do seu modelo quando o serviço web é acedido."
services: machine-learning
documentationcenter: 
author: raymondlaghaeian
manager: jhubbard
editor: cgronlun
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: raymondl;garye
ms.openlocfilehash: 715ea008b84c1a503661394da14e8af167327941
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-machine-learning-web-service-parameters"></a>Utilizar Parâmetros do Serviço Web do Azure Machine Learning
Um serviço web Azure Machine Learning é criado através da publicação de uma experimentação que contém módulos com parâmetros configuráveis. Em alguns casos, poderá pretender alterar o comportamento de módulo enquanto o serviço web está em execução. *Os parâmetros do serviço de Web* permitem-lhe executar esta tarefa. 

Um exemplo comum é configurar o [importar dados] [ reader] módulo para que o utilizador do serviço web publicada pode especificar uma origem de dados diferente quando o serviço web é acedido. Ou configurar o [exportar dados] [ writer] módulo para que pode ser especificado um destino diferente. Outros exemplos incluem a alterar o número de bits para o [funcionalidade hash] [ feature-hashing] módulo ou o número pretendido de funcionalidades para o [filtro com base na seleção de funcionalidades] [ filter-based-feature-selection] módulo. 

Pode definir os parâmetros do serviço Web e associá-las com um ou mais parâmetros do módulo na sua experimentação e pode especificar se estão necessária ou opcional. O utilizador do serviço web, em seguida, pode fornecer valores para estes parâmetros quando chamar o serviço web. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="how-to-set-and-use-web-service-parameters"></a>Como configurar e utilizar os parâmetros do serviço Web
Definir um parâmetro de serviço Web ao clicar no ícone junto ao parâmetro para um módulo e ao selecionar "Definir como parâmetro de serviço web". Esta ação cria um novo parâmetro do serviço Web e liga a esse parâmetro do módulo. Em seguida, quando o serviço web é acedido, o utilizador pode especificar um valor para o parâmetro de serviço Web e é aplicada para o parâmetro do módulo.

Depois de definir um parâmetro de serviço Web, está disponível para quaisquer outros parâmetros de módulo a experimentação. Se definir um parâmetro de serviço Web associada um parâmetro para um módulo, pode utilizar esse mesmo parâmetro de serviço Web para qualquer outro módulo, desde que o parâmetro espera o mesmo tipo de valor. Por exemplo, se o parâmetro de serviço Web é um valor numérico, em seguida, pode apenas ser utilizado para os parâmetros do módulo que esperam um valor numérico. Quando o utilizador define um valor para o parâmetro de serviço Web, serão aplicada a todos os parâmetros do módulo associado.

Pode decidir se pretende fornecer um valor predefinido para o parâmetro de serviço Web. Se o fizer, o parâmetro é opcional para o utilizador do serviço web. Se não fornecer um valor predefinido, em seguida, o utilizador é necessário introduzir um valor quando o serviço web é acedido.

A documentação da API para o serviço web inclui informações para o utilizador de serviço web sobre como especificar o parâmetro de serviço Web através de programação, ao aceder ao serviço web.

> [!NOTE]
> A documentação da API para um serviço web clássico é fornecida através de **página de ajuda de API** ligação de serviço web **DASHBOARD** no Machine Learning Studio. A documentação da API para um novo serviço web é fornecida através de [serviços Web do Azure Machine Learning](https://services.azureml.net/Quickstart) portal no **Consume** e **API do Swagger** páginas para o seu serviço web.
> 
> 

## <a name="example"></a>Exemplo
Por exemplo, vamos supor que temos uma experimentação com um [exportar dados] [ writer] módulo envia informações para o armazenamento de Blobs do Azure. Iremos definir um parâmetro de serviço Web com o nome "Caminho do Blob" que permite que o utilizador de serviço web alterar o caminho para o armazenamento de BLOBs, quando o serviço é acedido.

1. No Machine Learning Studio, clique em de [exportar dados] [ writer] módulo para o selecionar. As respetivas propriedades são apresentadas no painel de propriedades para a direita da tela de experimentação.
2. Especifique o tipo de armazenamento:
   
   * Em **especifique o destino de dados**, selecione "Blob Storage do Azure".
   * Em **especifique o tipo de autenticação**, selecione "Conta".
   * Introduza as informações de conta para o armazenamento de Blobs do Azure. 
     <p />
3.Clique no ícone para a direita do **caminho para o início com o parâmetro de contentor de BLOBs**. Este aspeto:
   
   ![Ícone de parâmetro de serviço Web][icon]
   
   Selecione "Definir como parâmetro de serviço web".
   
   Uma entrada é adicionada em **parâmetros de serviço Web** na parte inferior do painel de propriedades com o nome "Path para o início com o contentor de blob". Este é o parâmetro de serviço Web que está associada a este [exportar dados] [ writer] parâmetro do módulo.
4. Para mudar o nome de parâmetro de serviço Web, clique no nome, introduza "Caminho do Blob" e prima a **Enter** chave. 
5. Para fornecer um valor predefinido para o parâmetro de serviço Web, clique no ícone à direita do nome, selecione "Fornecer o valor predefinido", introduza um valor (por exemplo, "container1/output1.csv") e prima a **Enter** chave.
   
   ![Parâmetro de serviço Web][parameter]
6. Clique em **Executar**. 
7. Clique em **implementar serviço Web** e selecione **implementar o serviço Web [clássico]** ou **implementar o serviço Web do [New]** para implementar o serviço web.

> [!NOTE] 
> Para implementar um novo serviço web tem de ter permissões suficientes na subscrição aos quais pode implementar o serviço web. Para obter mais informações, consulte [gira um serviço Web utilizando o portal de serviços Web do Azure Machine Learning](manage-new-webservice.md). 

O utilizador do serviço web pode agora especificar um novo destino para o [exportar dados] [ writer] módulo ao aceder ao serviço web.

## <a name="more-information"></a>Mais informações
Para obter um exemplo mais detalhado, consulte o [parâmetros de serviço Web](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) entrada no [blogue do Machine Learning](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Para obter mais informações sobre aceder a um serviço web do Machine Learning, consulte [como consumir um serviço Web do Azure Machine Learning](consume-web-services.md).

<!-- Images -->
[icon]: ./media/web-service-parameters/icon.png
[parameter]: ./media/web-service-parameters/parameter.png


<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

