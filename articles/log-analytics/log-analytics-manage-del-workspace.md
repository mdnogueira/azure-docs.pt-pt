---
title: "Eliminar uma área de trabalho do Log Analytics do Azure | Microsoft Docs"
description: "Saiba como eliminar a sua área de trabalho de análise de registos, se tiver criado uma numa subscrição pessoal ou reestruturar o seu modelo da área de trabalho."
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 3d99f9869dfdfbe18f82e873bd367cc85f9414f1
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>Eliminar uma área de trabalho do Log Analytics do Azure com o portal do Azure
Este tópico mostra como utilizar o portal do Azure para eliminar um workpace de análise de registos que poderá deixar de necessitar. 

## <a name="to-delete-a-workspace"></a>Para eliminar uma área de trabalho 
Quando elimina uma área de trabalho de análise de registos, todos os dados relacionados com a sua área de trabalho é eliminado do serviço no prazo de 30 dias.  Quer tenha cuidado quando elimina uma área de trabalho porque poderá haver dados importantes e de configuração que pode afetar negativamente as operações de serviço.  
 
Se for um administrador e existirem vários utilizadores associados à área de trabalho, a associação entre esses utilizadores e a área de trabalho é interrompida. Se os utilizadores estão associados a outras áreas de trabalho, em seguida, podem continuar a utilizar a análise de registos com as outras áreas de trabalho. No entanto, se não estão associados com outras áreas de trabalho, em seguida, precisam de criar uma área de trabalho para utilizar a análise de registos. 

1. Inicie sessão no [Portal do Azure](http://portal.azure.com). 
2. No portal do Azure, clique em **mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Iniciar análise**.
3. No painel de subscrições de análise de registos, selecione uma área de trabalho e, em seguida, clique em **eliminar** da parte superior do painel do meio.<br><br> ![Eliminar a opção do painel de propriedades da área de trabalho](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace.png)<br>  
4. Quando for apresentada a janela da mensagem de confirmação pedir-lhe para confirmar a eliminação da área de trabalho, clique em **Sim**.<br><br> ![Confirmar eliminação da área de trabalho](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace-confirm.png)

