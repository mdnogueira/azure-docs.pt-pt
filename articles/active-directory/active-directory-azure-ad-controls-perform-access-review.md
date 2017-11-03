---
title: "Rever o acesso ao utilizar o Azure AD aceder revisões | Microsoft Docs"
description: "Saiba como rever o acesso através da utilização de revisões de acesso do Azure Active Directory."
services: active-directory
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 9cd11d41c68c29bfcba44673ae6dbd154fc463f6
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="review-access-with-azure-ad-access-reviews"></a>Rever o acesso com o Azure AD revisões de acesso

Azure Active Directory (Azure AD) simplifica como às empresas gerir o acesso a aplicações e membros dos grupos no Azure AD e outros serviços Online da Microsoft com uma funcionalidade denominada acesso revê. Talvez recebeu uma mensagem de e-mail da Microsoft que pede-lhe para rever o acesso para os membros de um grupo ou os utilizadores com acesso a uma aplicação. 

## <a name="open-an-access-review"></a>Abra uma revisão do acesso

Para ver as revisões de acesso pendente, selecione a hiperligação no e-mail. Se não tiver o correio eletrónico, pode localizar os revisões de acesso, seguindo estes passos:

1. Inicie sessão no [painel de acesso do Azure AD](https://myapps.microsoft.com).

2. Selecione o símbolo de utilizador no canto superior direito da página, que apresenta a sua organização nome e a predefinição. Se mais do que uma organização estiver listada, selecione a organização que pediu uma revisão do acesso.

3. Se a etiqueta de um mosaico **aceder revisões** se encontra no lado direito da página, selecione. Se o mosaico não estiver visível, não existem nenhum revisões de acesso para efetuar para a organização e é necessária nenhuma ação neste momento.

## <a name="fill-out-an-access-review"></a>Preencher uma revisão do acesso

Quando seleciona uma revisão do acesso da lista, pode ver os nomes dos utilizadores que necessitam de ser revistas. Poderá ver apenas um nome – os seus próprios – se o pedido foi para rever o suas próprias acesso.

Para cada linha na lista, pode decidir se pretende aprovar ou negar o acesso do utilizador. Selecione a linha e escolha se pretende aprovar ou negar. (Se não souber o utilizador, pode indicar que, demasiado.)

O revisor poderão exigir que forneça uma justificação de aprovação de acesso contínuo ou membros do grupo.

## <a name="next-steps"></a>Passos seguintes

Acesso negado de um utilizador não é removido imediatamente. Podem ser removido quando a revisão estiver concluída, ou quando um administrador para a revisão. Se pretende alterar a sua resposta e um utilizador anteriormente negado de aprovar ou negar um utilizador anteriormente aprovado, selecione a linha, a resposta de reposição e selecione uma resposta de novo. Pode efetuar este passo até que a revisão do acesso esteja concluída.



