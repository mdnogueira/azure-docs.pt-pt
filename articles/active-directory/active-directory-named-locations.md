---
title: "Com o nome localizações no Azure Active Directory | Microsoft Docs"
description: "Ao configurar a chamada localizações, pode evitar a necessidade de endereços IP que são proprietário pela sua organização geram falsos positivos para o Impossible viajam para localizações atípicas tipo de evento de risco."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: da437908509e40386ed23863648bd6956b308186
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="named-locations-in-azure-active-directory"></a>Localizações nomeadas no Azure Active Directory

Com a funcionalidade de localizações com o nome do Azure Active Directory, pode Etiquetar intervalos de endereços IP fidedignos nas suas organizações. No seu ambiente, pode utilizar localizações chamadas no contexto da deteção de [eventos de risco](active-directory-reporting-risk-events.md). A funcionalidade de ajuda a reduzir o número de comunicado falsos positivos para o *Impossible viajar para localizações atípicas* o risco de tipo de evento. 

## <a name="configuration"></a>Configuração

Para configurar uma localização com nome:

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com) como administrador global.

2. No painel esquerdo, clique em **do Azure Active Directory**.

    ![A ligação do Azure Active Directory no painel esquerdo](./media/active-directory-named-locations/01.png)

3. No **do Azure Active Directory** painel, no **segurança** secção, clique em **acesso condicional**.

    ![O comando de acesso condicional](./media/active-directory-named-locations/05.png)


4. No **acesso condicional** painel, no **gerir** secção, clique em **denominado localizações**.

    ![O comando de localizações denominado](./media/active-directory-named-locations/06.png)


5. No **denominado localizações** painel, clique em **nova localização**.

    ![O novo comando de localização](./media/active-directory-named-locations/07.png)


6. No **novo** painel, efetue o seguinte procedimento:

    ![O novo painel](./media/active-directory-named-locations/56.png)

    a. No **nome** caixa, escreva um nome para a sua localização com nome.

    b. No **intervalos IP** caixa, escreva um intervalo de IP. O intervalo de IP tem de ser o *encaminhamento-Classless Inter-Domain* formato (CIDR).  

    c. Clique em **Criar**.



## <a name="what-you-should-know"></a>O que deve conhecer

**Em massa atualizações**: ao criar ou atualizar localizações com nome, para as atualizações em massa, pode carregar ou transferir um ficheiro CSV com os intervalos de IP. Um carregamento adiciona os intervalos de IP no ficheiro de lista em vez de substituir a lista.

![As ligações de carregamento e transferência](./media/active-directory-named-locations/09.png)


**Limitações**: pode definir um máximo de 60 localizações com nome, com um intervalo IP atribuído a cada um deles. Se tiver apenas uma localização com nome configurada, pode definir até 500 intervalos IP para o mesmo.


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre eventos de risco, consulte [eventos de risco do Azure Active Directory](active-directory-reporting-risk-events.md).

