---
title: "Foi encontrado nenhum grupo de conector de trabalho para uma aplicação de Proxy de aplicações | Microsoft Docs"
description: "Resolver problemas que poderão surgir quando não existe nenhum trabalho conector num grupo de conector para a sua aplicação com o Proxy de aplicações do Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 4945958deedc8a1d9989ff901192c03a5363b4dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Nenhum grupo de conector de trabalho encontrado para uma aplicação de Proxy de aplicações

Ajuda neste artigo para resolver os problemas comuns deparam quando não existe um conector detetado para uma aplicação de Proxy de aplicações integradas com o Azure Active Directory.

## <a name="overview-of-steps"></a>Descrição geral dos passos
Se não houver nenhum trabalho conector num grupo de conector para a sua aplicação, existem algumas formas de resolver o problema:

-   Se tiver não conectores no grupo, pode:

    -   Transfira um novo conector no servidor de direita no local e atribua-a este grupo

    -   Mova um conector do Active Directory para o grupo

-   Se tiver não conectores Active Directory no grupo, pode:

    -   Identificar o motivo pelo qual que o conector está inativo e resolver

    -   Mova um conector do Active Directory para o grupo

Para saber qual deles é o problema, abra o menu de "Proxy da aplicação" na sua aplicação e observe a mensagem de aviso do grupo de conector. -Especifique o grupo tem de, pelo menos, um conector (tiver nenhum grupo) ou que não tem nenhum conectores Active Directory (embora provavelmente tiver conectores Inativos).

   ![Seleção de grupo do conector no Portal do Azure](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Para obter detalhes sobre cada uma destas opções, consulte a secção correspondente abaixo. Cada um destes parte do princípio de que estão a começar a página de gestão do conector. Se estiver à procura a mensagem de erro acima, pode aceder a esta página ao clicar na mensagem de aviso. Caso contrário, isto pode ser encontrado acedendo a **do Azure Active Directory**, clicando no **aplicações empresariais**, em seguida, **Proxy de aplicações.**

   ![Gestão de grupo do conector no Portal do Azure](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Transfira um novo conector

Para transferir um novo Conetor, utilize o botão "Transferir o conector" na parte superior da página.

tenha em atenção o conector tem de ser instalado num computador com a linha direta de visão, para a aplicação de back-end e, normalmente é colocada no mesmo servidor que a aplicação. Depois de transferir, o conector deve aparecer neste menu. Clique o conector e utilize o menu pendente "conector do grupo" para se certificar de que pertença ao grupo à direita. Guarde a alteração.

   ![Transferir o conector do Portal do Azure](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Mover um conector do Active Directory

Se tiver um conector do Active Directory deve pertencer ao grupo e com a linha de visão para a aplicação de back-end de destino, pode mover o conector para o grupo atribuído. Para tal, clique o conector. No campo "Grupo de conector", utilize a lista pendente para selecionar o grupo correto e clique em Guardar.

## <a name="resolve-an-inactive-connector"></a>Resolver um conector inativo

Se os conectores apenas no grupo estão inativos, que são provável num computador que não tem todas as portas necessárias desbloqueadas.

consulte o documento de resolução de problemas de portas para obter detalhes sobre a investigar o problema.

## <a name="next-steps"></a>Passos seguintes
[Compreender os conectores de Proxy de aplicações do Azure AD](application-proxy-understand-connectors.md)


