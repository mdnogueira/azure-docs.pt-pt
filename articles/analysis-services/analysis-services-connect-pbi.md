---
title: "Se ligam aos serviços de análise do Azure com o Power BI | Microsoft Docs"
description: Saiba como ligar a um servidor de Analysis Services do Azure utilizando o Power BI.
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
ms.openlocfilehash: 5f3ad1c4df2c21a8d52a8a3c4d7d311af32720a9
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="connect-with-power-bi"></a>Estabelecer ligação com o Power BI

Assim que tiver criado um servidor no Azure e implementar um modelo de tabela, os utilizadores na sua organização, estará pronto ligar e começar a explorar os dados. 

> [!TIP]
> Lembre-se de que utiliza a versão mais recente do [Power BI Desktop](https://powerbi.microsoft.com/desktop/).
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Ligar no Power BI Desktop

1. No ambiente de trabalho do Power BI, clique em **obter dados** > **Azure** > **base de dados do Azure Analysis Services**.

2. No **servidor**, introduza o nome do servidor. 
    
    Lembre-se de que inclui o URL completo. Por exemplo, asazure://westcentralus.asazure.windows.net/advworks.

3. No **base de dados**, se souber o nome da base de dados em tabela do modelo ou perspetiva que pretende ligar, cole-a aqui. Caso contrário, pode deixar este campo em branco e selecione uma base de dados ou de uma perspetiva mais tarde.

4. Deixe a predefinição **ligar em direto** opção, em seguida, prima **Connect**. 

5. Se lhe for solicitado, introduza as credenciais de início de sessão. 

6. No **navegador**, expanda o servidor, em seguida, selecione o modelo ou perspetiva que pretende ligar, em seguida, clique em **Connect**. Clique num modelo ou perspetiva para mostrar todos os objetos nessa vista.

    O modelo é aberto no Power BI Desktop com um relatório na vista de relatório em branco. A lista de campos apresenta todos os objetos de modelo não oculto. Estado de ligação é apresentado no canto inferior direito.

## <a name="connect-in-power-bi-service"></a>Ligar no Power BI (serviço)

1. Crie um ficheiro do Power BI Desktop que tenha uma ligação em direto para o seu modelo no seu servidor.
2. No [Power BI](https://powerbi.microsoft.com), clique em **obter dados** > **ficheiros**. Localize e selecione o ficheiro.



## <a name="see-also"></a>Consultar também
[Ligar ao Azure Analysis Services](analysis-services-connect.md)   
[Bibliotecas de cliente](analysis-services-data-providers.md)

