---
title: Resolver problemas empresariais custo vistas - Azure | Microsoft Docs
description: "Saiba como resolver quaisquer problemas que poderá ter com vistas de custo organizacional no portal do Azure."
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
ms.openlocfilehash: eca1ac9ed51e6c2243be451a074792fbec2840d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-enterprise-cost-views"></a>Resolver problemas empresariais vistas de custos 

Dentro de inscrições de enterprise, existem várias definições que pode fazer com que os utilizadores dentro da inscrição não conseguir ver os custos.  Estas definições são geridas pelo administrador de inscrição ou pelo parceiro de se a inscrição não está a ser adquirida diretamente com a Microsoft.  Este artigo ajuda-o a compreender quais são as definições e forma afetam a inscrição. Estas definições são independentes do [funções do RBAC do Azure](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure). 

> [!Note]
> Esta funcionalidade está atualmente a ser uma pré-visualização privada. [Inscrever-se aqui](https://forms.office.com/Pages/DesignPage.aspx#FormId=v4j5cvGGr0GRqy180BHbR0YtfU6ham9OsGsPPYdu2xdUNk1BQUwzTkUyOVc5NUpCTFcwR0pIOVFETS4u) ter a associar a pré-visualização a inscrição.     

## <a name="enabling-access-to-costs"></a>Ativar o acesso aos custos

São vir uma mensagem não autorizado, ou *"vistas de custo estão desativadas no seu registo."* Quando à procura de informações de custo? ![não autorizado](media/billing-enterprise-mgmt-groups/unauthorized.png)

Tal poderá dever-se um dos seguintes motivos:

1. Já comprou do Azure através de um parceiro da empresa e, o parceiro não lançadas preços ainda. Para libertar de preços, contacte o seu parceiro para atualizar a definição dentro de [portal da empresa](https://ea.azure.com).
2. Em alternativa, se tiver um cliente EA direta, existem duas possibilidades:
    * São um proprietário da conta e o administrador de inscrição desativou os "pedidos vista encargos" definir.  
    * É um administrador do departamento e o administrador de inscrição desativou os "DA vista encargos" definir.
    * Contacte o administrador de inscrição para obter acesso. O administrador de inscrição pode visitar o [portal da empresa](https://ea.azure.com/manage/enrollment) e atualize a definição de conforme visto aqui:

![Definições de Portal da empresa](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)


## <a name="asset-is-unavailable"></a>Recurso não está disponível? 
Se receber uma mensagem de erro "este recurso não está disponível" quando tenta aceder a um grupo de gestão ou de subscrição, em seguida, que não tem a função correta para ver este item.  

![recurso não encontrado](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Contacte o administrador dos grupos de gestão ou de subscrição para terem acesso.  
* Para as subscrições, referência [controlo de acesso em funções do Azure (RBAC)](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure) documento para obter ajuda nos quais a função é necessária.
* Para grupos de gestão, o acesso do RBAC não está disponível e estará disponível em breve. Contacte o seu portal da empresa administrar a ter acesso atribuído.   
