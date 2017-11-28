---
title: "Refinar um grupo de avaliação com mapeamento de dependência de grupo no Azure migrar | Microsoft Docs"
description: "Descreve como refinar uma avaliação utilizando o mapeamento de grupo de dependência no serviço Azure migrar."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0527e34e-a078-405e-aeb9-c91a5808112a
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: b4d6861f147fbb6e65a9d529f17f78b54075eb90
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2017
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Refinar um grupo utilizando o mapeamento de grupo de dependência

Este artigo descreve como configurar o mapeamento de dependência de grupo para [Azure migrar](migrate-overview.md) avaliação. Normalmente, utiliza este método quando pretender adicione refinar as definições para um grupo existente, a verificação entre dependências de grupo, antes de executar uma avaliação. Grupos para os quais pretende executar o mapeamento de grupo de dependência não devem conter mais de 10 máquinas.  

## <a name="modify-a-group"></a>Modificar um grupo

1. O Azure migrar em projeto, **gerir**, clique em **grupos**e selecione o grupo.
2. Na página de grupo, clique em **dependências de vista**, para abrir o mapa de dependência do grupo. 

     ![Grupo de vista](./media/how-to-create-group-dependencies/create-group.png)

3. Para ver mais granulares dependências, clique em modificar o intervalo de tempo. Por predefinição, o intervalo é uma hora. Pode modificar o intervalo de tempo, ou especificar o início e fim datas e duração.
4. Utilize Ctrl + clique para selecionar máquinas no mapa. Adicionar ou remover máquinas do mapa.
    - Só é possível adicionar máquinas que tenham sido detetadas.
    - Adição e remoção de um grupo de máquinas invalidam passado avaliações do mesmo.
    - Opcionalmente, pode criar uma nova avaliação quando modificar o grupo.
5. Clique em **OK** para guardar o grupo.

    ![Adicionar e remover](./media/how-to-create-group-dependencies/add-remove.png)

Se pretende verificar as dependências de um computador específico que aparece no mapa de dependência do grupo, [configurar o mapeamento de dependência de máquina](how-to-create-group-machine-dependencies.md).


## <a name="next-steps"></a>Passos seguintes

[Saiba mais](concepts-assessment-calculation.md) sobre como avaliações são calculadas.
