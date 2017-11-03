---
title: "Não foram encontradas subscrições erro quando tentarem iniciar sessão portal do Azure ou centro de contas do Azure | Microsoft Docs"
description: "Fornece a solução para um problema em que não foram encontradas subscrições erro ocorre quando iniciar sessão no portal do Azure ou centro de contas do Azure."
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/30/2017
ms.author: genli
ms.openlocfilehash: 19f27aa81d7b5e03828f18f5a38cf3362df17694
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="no-subscriptions-found-error-in-azure-portal-or-azure-account-center"></a>Não foram encontradas subscrições erro no portal do Azure ou centro de contas do Azure

Poderá receber uma mensagem de erro "Não foram localizadas subscrições" ao tentar iniciar sessão para o [portal do Azure](https://portal.azure.com/) ou [Centro de contas do Azure](https://account.windowsazure.com/Subscriptions). Este artigo fornece uma solução para este problema.

## <a name="symptom"></a>Sintoma

Quando tenta iniciar sessão para o [portal do Azure](https://portal.azure.com/) ou [Centro de contas do Azure](https://account.windowsazure.com/Subscriptions), receber a seguinte mensagem de erro: "Não foram localizadas subscrições".

## <a name="cause"></a>Causa

Este problema ocorre se tiver selecionado no diretório errado ou se a sua conta não tem permissões suficientes. 

## <a name="solution"></a>Solução

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>Cenário 1: Mensagem de erro é recebida no [portal do Azure](https://portal.azure.com)

Para corrigir este problema:

* Certifique-se de que o diretório do Azure correto está selecionado clicando a sua conta no canto superior direito.

  ![Selecione o diretório na parte superior direita do portal do Azure](./media/billing-no-subscriptions-found/directory-switch.png)
* Se o diretório à direita do Azure está selecionado, mas continuar a receber a mensagem de erro, [ter a sua conta adicionada como um proprietário](billing-add-change-azure-subscription-administrator.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>Cenário 2: Mensagem de erro é recebida no [Centro de contas do Azure](https://account.windowsazure.com/Subscriptions)

Verifique se a conta que utilizou o administrador da conta. Para verificar que o administrador de conta, siga estes passos:

1. Iniciar sessão para o [Ver subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a subscrição que pretende verificar e, em seguida, procure em **definições**.
1. Selecione **propriedades**. O administrador da conta da subscrição é apresentado no **administrador da conta** caixa.  

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se ainda precisar de ajuda, [contacte o suporte](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409) para obter o seu problema resolvido rapidamente. 
