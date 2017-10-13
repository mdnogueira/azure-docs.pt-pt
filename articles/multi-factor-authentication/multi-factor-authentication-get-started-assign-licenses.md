---
title: "Atribuir Licenças para o MFA do Azure | Microsoft Docs"
description: "Saiba como atribuir licenças para o Multi-Factor Authentication do Microsoft Azure."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 514ef423-8ee6-4987-8a4e-80d5dc394cf9
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/13/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ROBOTS: NOINDEX
ms.openlocfilehash: 45522bf526c4aeab1d6ccc8891a55a0436ff9320
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="assigning-an-azure-mfa-azure-ad-premium-or-enterprise-mobility-license-to-users"></a>Atribuir uma licença do MFA do Azure, Azure AD Premium ou Enterprise Mobility aos utilizadores
Se tiver comprado licenças do MFA do Azure, Azure AD Premium ou Enterprise Mobility Suite, não é necessário criar um fornecedor do Multi-Factor Auth. Depois de atribuir as licenças aos utilizadores, pode começar a ativá-las para a MFA.

## <a name="to-assign-a-license"></a>Para atribuir uma licença
1. Inicie sessão no [portal clássico do Azure](https://manage.windowsazure.com) como administrador.
2. No lado esquerdo, selecione **Active Directory**.
3. Na página do Active Directory, faça duplo clique no diretório que tem os utilizadores que pretende ativar.
4. Na parte superior da página do diretório, selecione **Licenças**.
   ![Atribuir Licenças](./media/multi-factor-authentication-get-started-assign-licenses/assign1.png)
5. Na página Licenças, selecione **Multi-Factor Authentication do Azure**, **Active Directory Premium** ou **Enterprise Mobility Suite**.  Se tiver apenas uma, esta deverá estar selecionada automaticamente.
6. Na parte inferior da página, clique em **Atribuir**.
   ![Atribuir Licenças](./media/multi-factor-authentication-get-started-assign-licenses/assign3.png)
7. Na caixa apresentada, clique junto aos utilizadores ou grupos aos quais pretende atribuir licenças.  Deverá ver uma marca de verificação verde.
8. Clique no ícone da marca de verificação para guardar as alterações.
   ![Atribuir Licenças](./media/multi-factor-authentication-get-started-assign-licenses/assign4.png)
9. Deverá ver uma mensagem a indicar o número de licenças atribuídas e quantas falharam.  Clique em **OK**.
   ![Atribuir Licenças](./media/multi-factor-authentication-get-started-assign-licenses/assign5.png)

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações, consulte [What is Microsoft Azure Active Directory licensing? (O que é o licenciamento do Microsoft Azure Active Directory?)](../active-directory/active-directory-licensing-what-is.md)
