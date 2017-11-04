---
title: "Interação de inquilino características do Azure Active Directory | Microsoft Docs"
description: Gerir os seus inquilinos de inquilino do Azure Active por compreender os seus inquilinos como recursos totalmente independentes
services: active-tenant
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 2b862b75-14df-45f2-a8ab-2a3ff1e2eb08
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/10/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: 5c9225212e51d03ae40e3a55a6b9e6c0e74b5ced
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>Compreender a interagem como vários inquilinos do Azure Active Directory

No Azure Active Directory (Azure AD), cada inquilino é um recurso totalmente independente: um elemento de rede que é logicamente independente de outros inquilinos que gere. Não há nenhuma relação principal-subordinado entre inquilinos. Esta independência entre inquilinos inclui independência de recursos, independência administrativa e independência de sincronização.

## <a name="resource-independence"></a>Independência de recursos
* Se criar ou eliminar um recurso de um inquilino, esta não tem impacto em qualquer recurso no outro inquilino, com a exceção parcial dos utilizadores externos. 
* Se utilizar um dos seus nomes de domínio com um inquilino, não pode ser utilizado com nenhum outro inquilino.

## <a name="administrative-independence"></a>Independência administrativa
Se um utilizador não administrativo do inquilino "Contoso" Criar um inquilino de teste "Test", então:

* Por predefinição, o utilizador que cria um inquilino é adicionado como um utilizador externo nesse novo inquilino e atribuído a função de administrador global desse inquilino.
* Os administradores do inquilino 'Contoso' tem sem privilégios administrativos diretos para o inquilino "Teste", a menos que um administrador de "Teste" lhes concede esses privilégios. No entanto, os administradores da "Contoso" podem controlar o acesso ao inquilino "Teste" se controlarem a conta de utilizador que criou o "Teste".
* Se a adição/remoção de uma função de administrador de um utilizador num inquilino, a alteração não afeta as funções de administrador que o utilizador tem no outro inquilino.

## <a name="synchronization-independence"></a>Independência de sincronização
Pode configurar cada inquilino do Azure AD independentemente para obter os dados sincronizados a partir de uma única instância:

* A ferramenta do Azure AD Connect, para sincronizar dados com uma única floresta do AD.
* O inquilino do Azure Active conector para o Forefront Identity Manager, para sincronizar dados com um ou mais florestas no local e/ou as origens de dados do AD não do Azure.

## <a name="add-an-azure-ad-tenant"></a>Adicionar um inquilino do Azure AD
Para adicionar um inquilino do Azure AD no portal do Azure, inicie sessão na [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do Azure AD e no lado esquerdo, selecione **novo**.

> [!NOTE]
> Ao contrário de outros recursos do Azure, os seus inquilinos não são recursos subordinados de uma subscrição do Azure. Se a sua subscrição do Azure é cancelada ou expirada, pode continuar a aceder os dados do inquilino com o Azure PowerShell, da Graph API do Azure ou centro de administração do Office 365. Também pode [associar outra subscrição ao inquilino](active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Passos seguintes
Para uma perspetiva geral das melhores práticas e os problemas de licenciamento do Azure AD, consulte [o que é inquilino do Azure Active licenciamento?](active-directory-licensing-whatis-azure-portal.md).
