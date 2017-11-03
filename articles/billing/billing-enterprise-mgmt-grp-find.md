---
title: "Localizar um grupo de gestão ou de subscrição do Azure - Azure | Microsoft Docs"
description: "Como navegar entre vários diretórios para ver os grupos de gestão e as subscrições"
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: rithorn
ms.openlocfilehash: df4d85d0556a62311c112f24431b54d042333c7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="find-an-azure-subscription-or-management-group"></a>Localizar um grupo de gestão ou de subscrição do Azure

Se tiver problemas ao localizar uma subscrição ou grupo de gestão no Azure, que poderá à procura no diretório errado. Esta situação pode acontecer quando a sua conta existe em vários diretórios de Active Directory do Azure. Cada [do Active Directory é independente](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-licensing-directory-independence) e o acesso não é herdado em diretórios.      

![Menu do diretório de comutador](media/billing-enterprise-mgmt-groups/mgempty.png)



## <a name="switch-directories"></a>Diretórios de comutador 
Pode facilmente mudar diretórios no portal do Azure.
1.  Inicie sessão no [Portal do Azure](https://portal.azure.com).
2.  Selecione o nome na parte superior direita do ecrã. 
3.  A parte inferior do menu de lista todos os diretórios que tem acesso.
4.  Selecione o nome de um diretório para aceder. 

![Menu do diretório de comutador](media/billing-enterprise-mgmt-groups/switch-directory.png)

## <a name="asset-is-unavailable"></a>Recurso não está disponível? 
Se receber uma mensagem de erro "este recurso não está disponível" quando tenta aceder a um grupo de gestão ou de subscrição, em seguida, que não tem a função correta para ver este item.  

![recurso não encontrado](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Contacte o administrador dos grupos de gestão ou de subscrição para terem acesso.  
* Para as subscrições, referência [controlo de acesso em funções do Azure (RBAC)](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure) documento para obter ajuda nos quais a função é necessária.
* Para grupos de gestão, o acesso do RBAC não está disponível e estará disponível em breve. Contacte o seu portal da empresa administrar a ter acesso atribuído.   

## <a name="improve-your-experience-with-management-groups-and-subscriptions-in-the-same-directory"></a>Melhorar a sua experiência com grupos de gestão e subscrições no mesmo diretório 
Pode transferir as suas subscrições ou os grupos de gestão para o diretório que escolher, para que tudo existe no mesmo local.  A consolidação de subscrições e grupos de gestão para o mesmo diretório ajuda a reduzir a necessidade de mudar de diretórios e permitir que as políticas para ser herdadas.  


### <a name="transfer-your-subscriptions"></a>Transferir as suas subscrições 
Pode mover uma subscrição para o diretório associado os grupos de gestão. Pode ser alcançada movimentação, fazendo com que o administrador de inscrição transferir a sua subscrição para uma conta no diretório de destino. Para obter mais informações, inicie sessão no [portal da empresa](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription).


 






