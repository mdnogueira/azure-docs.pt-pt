---
title: "Se ligam aos serviços de análise do Azure com o Excel | Microsoft Docs"
description: Saiba como ligar a um servidor de Analysis Services do Azure utilizando o Excel.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 7597792a525583497ec6a400e668eda2adc9a93e
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="connect-with-excel"></a>Ligar com o Excel

Assim que tiver criado um servidor no Azure e implementar um modelo de tabela, está pronto para ligar e começar a explorar os dados.


## <a name="connect-in-excel"></a>Ligar no Excel

Ligar a um servidor no Excel é suportada utilizando obter dados no Excel 2016. A ligação utilizando o Assistente de importação de tabelas no Power Pivot não é suportada. 

**Para se ligar no Excel 2016**

1. No Excel 2016, no **dados** do Friso, clique em **obter dados externos** > **de outras origens** > **do Analysis Services** .

2. No Assistente de ligação de dados, no **nome do servidor**, introduza o nome do servidor, incluindo o protocolo e o URI. Em seguida, no **credenciais de início de sessão**, selecione **utilize o seguinte nome de utilizador e palavra-passe**e, em seguida, escreva o nome de utilizador organizacionais, por exemplo nancy@adventureworks.come a palavra-passe.

    ![Ligar a partir do início de sessão de Excel](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. No **selecionar base de dados e tabela**, selecione a base de dados e o modelo ou perspetiva e, em seguida, clique em **concluir**.
   
    ![Ligar a partir de modelo selecione do Excel](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Consultar também
[Bibliotecas de cliente](analysis-services-data-providers.md)   
[Gerir o seu servidor](analysis-services-manage.md)     


