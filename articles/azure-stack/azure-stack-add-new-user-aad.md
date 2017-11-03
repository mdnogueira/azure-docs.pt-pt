---
title: Adicionar uma nova conta de inquilino do Azure pilha no Azure Active Directory | Microsoft Docs
description: "Depois de implementar o Kit de desenvolvimento de pilha do Microsoft Azure, terá de criar a conta de utilizador, pelo menos, um inquilino, pelo que pode explorar o portal de inquilinos."
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 91d1c52c0abf14656e08b511e4f6c8041e319020
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
*Aplica-se a: Azure da pilha Kit de desenvolvimento*

# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Adicionar uma nova conta de inquilino do Azure pilha no Azure Active Directory
Depois de [implementar o Kit de desenvolvimento de pilha do Azure](azure-stack-run-powershell-script.md), terá de uma conta de utilizador de inquilino para que possa explorar o portal de inquilinos e testar as suas ofertas e planos. Pode criar uma conta de inquilino por [no portal do Azure](#create-an-azure-stack-tenant-account-using-the-azure-portal) ou pelo [através do PowerShell](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Criar uma conta de inquilino de pilha do Azure no portal do Azure
Tem de ter uma subscrição do Azure para utilizar o portal do Azure.

1. Inicie sessão no [Azure](http://manage.windowsazure.com).
2. Na barra de navegação esquerdo do Microsoft Azure, clique em **do Active Directory**.
3. Na lista de diretório, clique no diretório que pretende utilizar para a pilha do Azure ou criar um novo.
4. Nesta página do diretório, clique em **utilizadores**.
5. Clique em **adicionar utilizador**.
6. No **adicionar utilizador** assistente, no **tipo de utilizador** lista, escolha **novo utilizador na sua organização**.
7. No **nome de utilizador** caixa, escreva um nome para o utilizador.
8. No  **@**  caixa, selecione a entrada adequada.
9. Clique na seta seguinte.
10. No **perfil de utilizador** página do assistente, escreva um **nome próprio**, **Apelido**, e **nome a apresentar**.
11. No **função** lista, escolha **utilizador**.
12. Clique na seta seguinte.
13. No **Get palavra-passe temporária** página, clique em **criar**.
14. Copiar o **nova palavra-passe**.
15. Inicie sessão no Microsoft Azure com a nova conta. Altere a palavra-passe quando lhe for pedido.
16. Inicie sessão no `https://portal.local.azurestack.external` com a nova conta para ver o portal de inquilinos.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>Criar uma conta de inquilino de pilha do Azure com o PowerShell
Se não tiver uma subscrição do Azure, não é possível utilizar o portal do Azure para adicionar uma conta de utilizador de inquilino. Neste caso, pode utilizar em vez disso, o módulo Azure Active Directory para Windows PowerShell.

> [!NOTE]
> Se estiver a utilizar Account Microsoft (Live ID) para implementar o Kit de desenvolvimento de pilha do Azure, não é possível utilizar o AAD PowerShell para criar a conta de inquilino. 
> 
> 

1. Instalar o [Assistente de início de sessão para profissionais de TI RTW Microsoft Online Services](https://www.microsoft.com/en-us/download/details.aspx?id=41950).
2. Instalar o [módulo Azure Active Directory para Windows PowerShell (versão de 64 bits)](http://go.microsoft.com/fwlink/p/?linkid=236297) e abri-lo.
3. Execute os seguintes cmdlets:

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Inicie sessão no Microsoft Azure com a nova conta. Altere a palavra-passe quando lhe for pedido.
2. Inicie sessão no `https://portal.local.azurestack.external` com a nova conta para ver o portal de inquilinos.

