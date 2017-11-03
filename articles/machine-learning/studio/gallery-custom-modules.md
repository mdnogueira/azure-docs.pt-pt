---
title: "Módulos personalizados galeria da Cortana Intelligence | Microsoft Docs"
description: "Detete módulos de aprendizagem máquina personalizada na galeria da Cortana Intelligence."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 16037a84-dad0-4a8c-9874-a1d3bd551cf0
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.author: roopalik;garye
ms.openlocfilehash: 4bab94c04f09261eaa88b9e6a225c05f57992ab0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="discover-custom-machine-learning-modules-in-cortana-intelligence-gallery"></a>Detetar módulos de aprendizagem máquina personalizada na galeria da Cortana Intelligence
[!INCLUDE [machine-learning-gallery-item-selector](../../../includes/machine-learning-gallery-item-selector.md)]

## <a name="custom-modules-for-machine-learning-studio"></a>Módulos personalizados para o Machine Learning Studio
Galeria da Cortana Intelligence oferece várias [módulos personalizados](https://gallery.cortanaintelligence.com/customModules) que expandir as capacidades do Azure Machine Learning Studio. Pode importar os módulos possa utilizar nas suas experimentações, pode desenvolver soluções de Análise Preditiva ainda mais avançadas.

Atualmente, a Galeria oferece módulos no *análise de séries de tempo*, *regras de associação*, *clustering algoritmos* (para além das k-significa), e  *visualizações*e outros módulos de utilitário workhorse.


## <a name="discover"></a>Descobrir
Para procurar nos módulos personalizados [na galeria](http://gallery.cortanaintelligence.com), em **mais**, selecione **módulos personalizados**.

![Selecione módulos personalizados na home page da Galeria](./media/gallery-custom-modules/select-custom-modules-in-gallery.png)

O  **[módulos personalizados](https://gallery.cortanaintelligence.com/customModules)**  página apresenta uma lista de módulos recentemente adicionados e populares. Para ver todos os módulos personalizados, selecione o **ver todos os** botão. Para procurar um módulo personalizado específico, selecione **ver todos os**e os critérios de filtro, em seguida, selecione. Também pode introduzir termos de pesquisa no **pesquisa** caixa na parte superior da página da galeria.

![Selecione "ver todos os" para procurar todos os módulos personalizados](./media/gallery-custom-modules/click-see-all-for-all-custom-modules.png)

### <a name="understand"></a>Compreender

Para compreender como funciona um módulo personalizado publicado, selecione o módulo personalizado para abrir a página de detalhes do módulo. A página de detalhes oferece uma experiência consistente e informativo learning. Por exemplo, a página de detalhes realça o objetivo do módulo e lista esperadas de entradas, saídas e parâmetros. A página de detalhes tem também uma ligação para o código de origem subjacente, o que pode examinar e personalizar.

### <a name="comment-and-share"></a>Comentário e partilha
Num módulo personalizado página de detalhes, além de **comentários** secção, pode comente, fornecer comentários ou fazer perguntas sobre o módulo. Mesmo podem partilhar o módulo com amigos ou colegas no Twitter ou LinkedIn. Também pode enviar por e-mail uma ligação para a página de detalhes do módulo, para convidar outros utilizadores para ver a página.

![Partilhar este item com amigos](./media/gallery-how-to-use-contribute-publish/share-links.png)

![Adicionar os seus comentários](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="import"></a>Importar
Pode importar qualquer módulo personalizado na Galeria para as seus próprios experimentações.

Galeria da Cortana Intelligence oferece duas formas para importar uma cópia do módulo:

* **Na galeria do**. Quando importar um módulo personalizado a partir da galeria, também obtém uma experimentação de exemplo que dá-lhe um exemplo de como utilizar o módulo.
* **A partir do Machine Learning Studio**. Pode importar qualquer módulo personalizado enquanto estiver a trabalhar no Machine Learning Studio (neste caso, não a obter a experimentação de exemplo).

### <a name="from-the-gallery"></a>Na galeria do

1. Na galeria, abra a página de detalhes do módulo. 
2. Selecione **abrir no Studio**.
   
    ![Abra módulo personalizado na galeria do](./media/gallery-custom-modules/open-custom-module-from-gallery.png)
   
Cada módulo personalizado inclui uma experimentação de exemplo que demonstra como utilizar o módulo. Quando seleciona **abrir no Studio**, abre a experimentação de exemplo na sua área de trabalho do Machine Learning Studio. (Se ainda não tem sessão iniciada Studio, lhe for pedido para primeiro início de sessão utilizando a sua conta Microsoft.)

Para além da experimentação de exemplo, o módulo personalizado é copiado para a área de trabalho. -Também é colocada na paleta do módulo, com todos os seus incorporadas ou personalizadas Machine Learning Studio módulos. Agora pode utilizar nas suas experimentações, tal como qualquer outro módulo na sua área de trabalho.

### <a name="from-within-machine-learning-studio"></a>A partir do Machine Learning Studio

1. No Machine Learning Studio, selecione **novo**.
2. Selecione **módulo**. Pode escolher entre uma lista de módulos da galeria, ou localizar um módulo específico utilizando o **pesquisa** caixa.
3. Apontar o rato para um módulo e, em seguida, selecione **Import Module**. (Para obter informações sobre o módulo, selecione **ver na galeria**. Isto leva-o para a página de detalhes do módulo na galeria.)
   
    ![Importe o módulo personalizado para o Machine Learning Studio](./media/gallery-custom-modules/add-custom-module-in-studio.png)

O módulo personalizado é copiado para a sua área de trabalho e colocado na paleta do módulo, com os módulos de Machine Learning Studio incorporados ou personalizados. Agora pode utilizar nas suas experimentações, tal como qualquer outro módulo na sua área de trabalho.

## <a name="use"></a>Utilizar

Independentemente de qual o método que escolher para importar um módulo personalizado, ao importar o módulo, o módulo é colocado na paleta do módulo no Machine Learning Studio. Da paleta do módulo, pode utilizar o módulo personalizado em qualquer experimentação na sua área de trabalho, tal como qualquer outro módulo.

Para utilizar um módulo importado:

1. Criar uma experimentação ou abrir uma experimentação existente.
2. Para expandir a lista de módulos personalizados na área de trabalho, na paleta do módulo, selecione **personalizado**. Paleta do módulo é para a esquerda da tela de experimentação.
   
    ![Lista de módulos personalizados na paleta do Studio](./media/gallery-custom-modules/custom-module-in-studio-palette.png)
3. Selecione o módulo que importou e arraste-o à sua experimentação.


**[Aceda à Galeria](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

