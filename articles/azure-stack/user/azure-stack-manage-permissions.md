---
title: "Gerir permissões para recursos por utilizador na pilha do Azure | Microsoft Docs"
description: "Como um administrador de serviço ou o inquilino, saiba como gerir RBAC permissões."
services: azure-stack
documentationcenter: 
author: Heathl17
manager: byronr
editor: 
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 1e0ced4b2d65456034ba3f8543152702c089a0b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-role-based-access-control"></a>Gerir o controlo de acesso baseado em funções

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Um utilizador na pilha do Azure pode ser um leitor, proprietário ou contribuinte para cada instância de um serviço, grupo de recursos ou subscrição. Por exemplo, o utilizador A poderá ter permissões de leitor à subscrição 1, mas tem permissões de proprietário para 7 de Máquina Virtual.

* Leitor: O utilizador pode ver tudo, mas não é possível efetuar quaisquer alterações.
* Contribuidor: Utilizador pode gerir tudo, exceto acesso aos recursos.
* O proprietário: Utilizador pode gerir tudo, incluindo o acesso a recursos.

## <a name="set-access-permissions-for-a-user"></a>Definir permissões de acesso para um utilizador
1. Inicie sessão com uma conta que tenha permissões de proprietário para o recurso que pretende gerir.
2. No painel de recursos, clique em de **acesso** ícone ![](media/azure-stack-manage-permissions/image1.png).
3. No **utilizadores** painel, clique em **funções**.
4. No **funções** painel, clique em **adicionar** ao adicionar permissões para o utilizador.

## <a name="next-steps"></a>Passos seguintes


