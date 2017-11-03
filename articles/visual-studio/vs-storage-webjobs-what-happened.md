---
title: "O que aconteceu ao meu projeto do trabalho Web (serviço ligado da Visual Studio do armazenamento do Azure)? | Microsoft Docs"
description: "Descreve o que aconteceu num projeto trabalho Web do Azure após a ligação a uma conta de armazenamento com o Visual Studio ligação de serviços"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: kraigb
ms.openlocfilehash: 8891685a99c5ba366b74af0a21396d4a5e499835
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>O que aconteceu ao meu projeto do trabalho Web (serviço ligado da Visual Studio do armazenamento do Azure)?
## <a name="references-added"></a>Referências adicionadas
O pacote NuGet do armazenamento do Azure foi adicionado ou atualizado no seu projeto de Visual Studio.  
Este pacote adiciona as seguintes referências de .NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Configurationmanager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft**
* **Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Cadeia de ligação para o armazenamento de Azure adicionado
No ficheiro App. config do seu projeto, a **AzureWebJobsStorage** e **AzureWebJobsDashboard** entradas foram atualizadas com a cadeia de ligação e a chave da conta de armazenamento selecionada.

Para obter mais informações, consulte [recursos de documentação de WebJobs do Azure](http://go.microsoft.com/fwlink/?linkid=390226).

