---
title: "Introdução ao Azure AD no Visual Studio end WebApi projetos | Microsoft Docs"
description: "Como começar a utilizar o Azure Active Directory nos projetos de end WebApi depois de ligar ou criar um Azure AD com o Visual Studio ligada a serviços"
services: active-directory
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/19/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: a756316054dd3bb63f3b0a9f59621bb1345bc693
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-active-directory-and-visual-studio-connected-services-webapi-projects"></a>Get iniciado com o Azure Active Directory e os serviços do Visual Studio ligado (end WebApi projetos)
> [!div class="op_single_selector"]
> * [Introdução](vs-active-directory-webapi-getting-started.md)
> * [O que aconteceu](vs-active-directory-webapi-what-happened.md)
> 
> 

## <a name="requiring-authentication-to-access-controllers"></a>Exigir autenticação para os controladores de acesso
Foram adorned todos os controladores no seu projeto com o **autorizar** atributo. Este atributo exige que o utilizador ser autenticado antes de aceder a APIs definido por estes controladores. Para permitir que o controlador ser acedida anonimamente, remova este atributo do controlador de. Se pretender definir as permissões de um nível mais granular, aplique o atributo para cada método de que necessita de autorização em vez de aplicá-la para a classe de controlador.

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

