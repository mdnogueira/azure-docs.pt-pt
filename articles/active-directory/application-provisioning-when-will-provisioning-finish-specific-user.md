---
title: "Descobrir quando um utilizador específico será capaz de aceder a uma aplicação | Microsoft Docs"
description: "Como saber quando um utilizador extremamente importante ser capaz de aceder a uma aplicação que configurou para o aprovisionamento de utilizador com o Azure AD"
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
ms.openlocfilehash: fcefb31904cfb77022db0358e9feee6a0479db81
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="find-out-when-a-specific-user-will-be-able-to-access-an-application"></a>Saber quando um utilizador específico que será capaz de aceder a uma aplicação
Ao utilizar o aprovisionamento de utilizadores automática com uma aplicação, do Azure AD automaticamente aprovisionar e a atualização de contas de utilizador numa aplicação com base no coisas como [atribuição de utilizador e grupo](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) num intervalo de tempo agendadas regularmente, normalmente, a cada 10 minutos.

## <a name="how-long-does-it-take"></a>Quanto tempo demora?

O tempo que demora para um determinado utilizador a ser aprovisionado depende principalmente se já tiver ocorrido uma sincronização inicial "completa".

A primeira sincronização entre o Azure AD e uma aplicação pode tomar entre 20 minutos ou várias horas, consoante o tamanho do diretório do Azure AD e o número de utilizadores no âmbito de aprovisionamento. 

As sincronizações posteriores após a sincronização inicial ser mais rápida (por exemplo, dentro de 10 minutos), como o serviço de aprovisionamento armazena as marcas d'água que representam o estado de ambos os sistemas após a sincronização inicial, melhorando o desempenho de sincronizações subsequentes.

## <a name="how-to-check-the-status-of-a-user"></a>Como verificar o estado de um utilizador

Para ver o estado de aprovisionamento de um utilizador selecionado, consulte os registos de auditoria no Azure AD.

Os registos de auditoria aprovisionamento podem ser acedidos no portal do Azure, no **do Azure Active Directory &gt; aplicações da empresa &gt; \[nome da aplicação\] &gt; registos de auditoria** separador. Filtrar os registos de **aprovisionamento da conta** categoria para ver apenas os eventos de aprovisionamento para essa aplicação. Pode procurar utilizadores com base no "ID correspondente" que foi configurado para os mesmos nos mapeamentos de atributos. 

Por exemplo, se tiver configurado o "nome principal de utilizador" ou "endereço de correio eletrónico" como o atributo correspondente no lado do Azure AD e o utilizador está a ser aprovisionamento não tem um valor de "audrey@contoso.com", em seguida, procure os registos de auditoria para "audrey@contoso.com" e consulte, em seguida, as entradas devolvidas.

Os registos de auditoria aprovisionamento registam todas as operações executadas pelo serviço de aprovisionamento, incluindo:

* Consultar o Azure AD para atribuído aos utilizadores que se encontrem no âmbito de aprovisionamento
* Consultar a aplicação de destino a existência desses utilizadores
* Os objetos de utilizador entre o sistema de comparação
* Adicionar, atualizar ou desativar a conta de utilizador no sistema de destino com base na comparação

## <a name="next-steps"></a>Passos seguintes
[Automatizar utilizador aprovisionamento e desaprovisionamento para aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)'
