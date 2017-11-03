---
title: "Desativar os serviços de domínio do Active Directory do Azure | Microsoft Docs"
description: Desativar o Azure Active Directory Domain Services no portal do Azure
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: maheshu
ms.openlocfilehash: 32db916b319c531816a935fcfe3bc4fe82997fdd
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Desativar o Azure Active Directory Domain Services no portal do Azure
Este artigo mostra como utilizar o portal do Azure para desativar os serviços de domínio do Azure Active Directory (AD) para o diretório do Azure AD.

> [!WARNING]
> **Eliminação é permanente e não pode ser invertida.**
> Continuar com cuidado! Se eliminar o domínio gerido:
  * Os controladores de domínio para o domínio gerido são anular aprovisionados e removidos da rede virtual.
  * Dados no domínio gerido são eliminados permanentemente. Isto inclui UOs personalizados, GPOs, registos DNS personalizados, principais de serviço, etc. GMSAs que criou no domínio gerido.
  * Computadores associados a um domínio gerido perdem a respetiva relação de confiança com o domínio e tem de ser retirado do domínio.
  * Não é possível iniciar sessão para estas máquinas utilizando credenciais empresariais do AD. Utilize as credenciais de administrador local para a máquina, como alternativa.
A eliminar o domínio gerido não eliminar o diretório do Azure AD ou caso contrário, afetar de forma adversa o diretório.
>

Execute os seguintes passos para eliminar o seu domínio gerido dos serviços de domínio do Azure AD:
1. Navegue para o [extensão dos serviços de domínio do Azure AD](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) no portal do Azure.
2. Clique no nome do seu domínio gerido.

    ![Selecione o domínio para eliminar](./media/getting-started/domain-services-delete-select-domain.png)

3. No **descrição geral** página, clique em de **eliminar** botão.

    ![Eliminar o domínio](./media/getting-started/domain-services-delete-domain.png)

4. Para confirmar a eliminação, escreva o nome de domínio DNS do domínio gerido. Clique em de **eliminar** botão quando tiver terminado.

    ![Eliminar a confirmação de domínio](./media/getting-started/domain-services-delete-domain-confirm.png)

O domínio gerido for eliminado do cerca de 15 a 20 minutos.

Considere [partilha comentários](active-directory-ds-contact-us.md) para o ajudar-na compreender o que iriam ajudar funcionalidades que escolheu serviços de domínio do Azure AD no futuro. Estes comentários ajudam-na evoluir o serviço de melhor se adequarem às suas necessidades de implementação e casos de utilização.
