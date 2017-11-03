---
title: Gerir administradores de servidor no Azure Analysis Services | Microsoft Docs
description: Saiba como gerir administradores de servidor para um servidor de Analysis Services no Azure.
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
ms.openlocfilehash: 6a42baf9fd880264e4130c009f27935a4743de21
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="manage-server-administrators"></a>Gerir administradores de servidor
Os administradores de servidores tem de ser um utilizador ou grupo válido no Azure Active Directory (Azure AD) para o inquilino em que reside o servidor. Pode utilizar **administradores de serviços de análise** no painel de controlo para o servidor no portal do Azure ou propriedades do servidor no SSMS para gerir os administradores de servidores. 

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Para adicionar os administradores de servidores utilizando o portal do Azure
1. No painel de controlo para o servidor, clique em **administradores de serviços de análise**.
2. No  **\<servername >-administradores de serviços de análise** painel, clique em **adicionar**.
3. No **adicionar administradores de servidor** painel, selecione as contas de utilizador do seu Azure AD ou convidar utilizadores externos ao endereço de correio eletrónico.

    ![Administradores de servidor no portal do Azure](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Para adicionar os administradores de servidores com o SSMS
1. Clique com o botão direito do servidor > **propriedades**.
2. No **propriedades do Analysis Server**, clique em **segurança**.
3. Clique em **adicionar**e, em seguida, introduza o endereço de e-mail para um utilizador ou grupo no Azure AD.
   
    ![Adicionar administradores de servidor no SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="next-steps"></a>Passos seguintes 
[Autenticação e permissões de utilizador](analysis-services-manage-users.md)  
[Gerir utilizadores e funções de base de dados](analysis-services-database-users.md)  
[Controlo de Acesso Baseado em Funções](../active-directory/role-based-access-control-what-is.md)  

