---
title: "Documentação da ajuda várias Georreplicação | Microsoft Docs"
description: "Saiba como criar uma área de trabalho e publicar um serviço web numa região do Azure diferente do Sul Central dos Estados Unidos (SCUS) região do Azure."
services: machine-learning
documentationcenter: 
author: tedway
manager: jhubbard
editor: rmca14
tags: 
ms.assetid: ed0ca8a8-fa53-4e56-b824-2d7e44641967
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/6/2017
ms.author: tedway; neerajkh
ms.openlocfilehash: e4941ccf8c6d7a0c77527e9c1d722bc3a770114a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="multi-geo-help-documentation"></a>Multi-Geo Help documentation (Documentação de Ajuda para Publicar em Várias Regiões)
Este artigo descreve como pode criar uma área de trabalho e publicar um serviço web em diferentes regiões do Azure.  O [Azure produtos pela página de região](https://azure.microsoft.com/en-us/regions/services/) apresenta uma lista de regiões onde o Azure Machine Learning está disponível.

## <a name="create-a-workspace"></a>Criar áreas de trabalho
1. Inicie sessão no Portal clássico do Azure.
2. Clique em **+ novo** > **serviços de dados** > **aprendizagem** > **criação rápida**.  Em **localização** selecione noutra região, tal como **Sudeste asiático**.
   ![Ajudar a Georreplicação várias imagem 1][1]
3. Selecione a área de trabalho e, em seguida, clique em **início de sessão para ML Studio**.
   ![Imagem de ajudar a Georreplicação múltipla 2][2]
4. Tem agora uma área de trabalho noutra região que poderá utilizar, tal como qualquer outra área de trabalho. Alternar entre as áreas de trabalho, procure para o canto superior direito do ecrã. Clique em lista pendente, selecione a região e, em seguida, selecione a área de trabalho. Tudo é local para a região de área de trabalho.  Por exemplo, todos os seus serviços web criados a partir de uma área de trabalho serão na área de trabalho está localizada na mesma região.
   ![Imagem de ajudar a Georreplicação várias 3][3]

## <a name="open-an-experiment-from-gallery"></a>Abrir uma experimentação da Galeria
Se abrir uma experimentação da galeria, também pode selecionar que pretende copiar a experimentação para região.

![Imagem de ajudar a Georreplicação várias 4][4a]

## <a name="web-service-management"></a>Gestão do serviço Web
Para gerir de forma programática serviços web, tal como para a reparametrização, utilize o endereço de específico da região:

* https://asiasoutheast.Management.azureml.NET
* https://europewest.Management.azureml.NET

### <a name="things-to-note"></a>Coisas a salientar
1. Só pode copiar experimentações entre as áreas de trabalho que pertencem à mesma região desta forma. Se pretender copiar experimentação em áreas de trabalho em regiões diferentes, pode utilizar o [PowerShell](http://aka.ms/amlps) commandlet [ *cópia AmlExperiment* ](https://github.com/hning86/azuremlps/blob/master/README.md#copy-amlexperiment) fazer. Outra solução é para publicar a experimentação para da galeria no modo não listado, em seguida, abri-la na área de trabalho a partir da outra região.
2. O Seletor de região mostrará apenas áreas de trabalho para uma região de cada vez.  
3. Uma área de trabalho gratuita ou a área de trabalho (anónima) do acesso de convidado será criada e alojada no Sul Central E.U.A.  
4. Serviços Web implementados a partir de uma área de trabalho Sudeste asiático também serão alojados no Sudeste asiático.  

## <a name="more-information"></a>Mais informações
Fazer uma pergunta sobre a [fórum do Azure Machine Learning](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning).

<!--Image references-->
[1]: ./media/multi-geo/multi-geo_1.png
[2]: ./media/multi-geo/multi-geo_2.png
[3]: ./media/multi-geo/multi-geo_3.png
[4a]: ./media/multi-geo/multi-geo_4a.png
