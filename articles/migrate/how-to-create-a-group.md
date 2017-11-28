---
title: "Grupo de máquinas para avaliação com o Azure migrar | Microsoft Docs"
description: "Descreve como agrupar máquinas antes de executar uma avaliação com o serviço Azure migrar."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5c279804-aa30-4946-a222-6b77c7aac508
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: 966e0aebf56ab049e898d1787bfdfbb2ef23635e
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2017
---
# <a name="group-machines-for-assessment"></a>Grupo de máquinas para avaliação

Este artigo descreve como criar um grupo de computadores para avaliação por [Azure migrar](migrate-overview.md). Migrar do Azure avalia máquinas do grupo para verificar se serem adequados para a migração para o Azure e fornece estimativas de dimensionamento e custos de execução da máquina no Azure.


## <a name="create-a-group"></a>Criar um grupo

1. No **Dashboard** do projeto do Azure migrar, clique em **grupos** > **+ grupo**e especifique um nome de grupo.
2. Adicione uma ou mais máquinas para o grupo e clique em **criar**. 
3. Opcionalmente, pode optar por executar uma nova avaliação para o grupo. 

    ![Criar um grupo](./media/how-to-create-a-group/create-group.png)

Depois do grupo for criado, pode modificá-la ao selecionar o grupo no **grupos** página e adição ou remoção de máquinas.

## <a name="next-steps"></a>Passos seguintes

- Saiba como utilizar [mapeamento de dependência de máquina](how-to-create-group-machine-dependencies.md) para criar grupos mais detalhados.
- [Saiba mais](concepts-assessment-calculation.md) sobre como avaliações são calculadas.
