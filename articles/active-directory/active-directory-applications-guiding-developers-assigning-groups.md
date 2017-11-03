---
title: "Atribua grupos para aplicações do Azure AD | Microsoft Docs"
description: "Como implementar a atribuição do grupo de aplicações do Azure."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 29b5ba89-a1c7-4f1f-a294-248a40106617
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
robots: noindex
ms.openlocfilehash: e0b0b87a454db96747f024e81882fe83d62fdbe2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="assign-azure-active-directory-groups-to-an-application"></a>Atribua grupos do Active Directory do Azure para uma aplicação
Antes de pode atribuir utilizadores e grupos para uma aplicação, deve necessitar de atribuição de utilizadores. Para saber como requerer a atribuição de utilizadores, consulte o [necessidade de atribuição de utilizador](active-directory-applications-guiding-developers-requiring-user-assignment.md) artigo.

Este artigo pressupõe que já tenha criado grupos no active directory que está a utilizar para esta aplicação.

## <a name="assigning-groups-to-an-application"></a>Atribuição de grupos a uma aplicação
1. Inicie sessão no portal do Azure com uma conta de administrador.
2. Clique em de **todos os itens** item de menu principal.
3. Escolha o diretório que está a utilizar para a aplicação.
4. Clique em de **aplicações** separador.
5. Selecione a aplicação na lista de aplicações associadas a este diretório.
6. Clique em de **utilizadores e grupos** separador.
7. Filtrar a lista de grupos do active Directory utilizando o **grupos** na lista pendente.
8. Selecione o grupo.
9. Clique em **atribuir**.
10. Clique em **Sim** quando lhe for pedido.

## <a name="next-steps"></a>Passos Seguintes
[!INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]
