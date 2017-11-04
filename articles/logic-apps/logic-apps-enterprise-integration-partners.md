---
title: "Crie parceiros para mensagens de empresa-empresa (B2B) – Azure Logic Apps | Microsoft Docs"
description: "Saiba como adicionar parceiros à sua conta de integração com o pacote de integração do Enterprise e Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 950cb449b53f400f0f0f860caf5415bbb5212269
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-or-update-partners-in-business-to-business-agreements-in-your-workflow"></a>Adicionar ou atualizar parceiros nos contratos de empresa-empresa no seu fluxo de trabalho

Os parceiros são entidades que participam na transações do empresa-empresa (B2B) e trocam mensagens entre si. Antes de poder criar parceiros que representam e outra organização estas transações, tem ambos partilhar informações que identificam e valida as mensagens enviadas por si. Depois de abordar estes detalhes e estiver pronto para começar a relação de negócio, pode criar parceiros na sua conta de integração para representar a ambos.

## <a name="what-roles-do-partners-have-in-your-integration-account"></a>As funções às quais é necessário parceiros na sua conta de integração?

Para definir os detalhes sobre as mensagens trocadas entre parceiros, criar contratos entre essas parceiros. No entanto, antes de poder criar um contrato, tem de ter adicionado, pelo menos, dois parceiros à sua conta de integração. Organização deve fazer parte do contrato, como o **parceiro anfitrião**. O outro parceiro ou **parceiro convidado** representa a organização que trocas de mensagens com a sua organização. O parceiro de convidado pode ser outra empresa, ou até mesmo um departamento na sua própria organização.

Depois de adicionar estas parceiros, pode criar um contrato.

Receber e enviar as definições são orientado por do ponto de vista do parceiro de alojado. Por exemplo, as definições de receção de um contrato de determinam como o parceiro alojado recebe mensagens enviadas a partir de um parceiro de convidado. Da mesma forma, as definições de envio no contrato indicam como o parceiro alojado envia mensagens para o parceiro de convidado.

## <a name="how-to-create-a-partner"></a>Como criar um parceiro?

1. No portal do Azure, selecione **procurar**.

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. Na caixa de filtro de pesquisa, introduza **integração**, em seguida, selecione **contas de automatização** na lista de resultados.

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. Selecione a conta de integração em que pretende adicionar os seus parceiros.

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Selecione o **parceiros** mosaico.

    ![](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. No painel de parceiros, escolha **adicionar**.

    ![](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. Introduza um nome para o seu parceiro, em seguida, selecione um **qualificador**. Por fim, introduza um **valor** para ajudar a identificar os documentos que entrem nas suas aplicações.

    ![](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. Para ver o progresso para o processo de criação de parceiro, selecione o *sino* ícone de notificação.

    ![](./media/logic-apps-enterprise-integration-partners/partner-4.png)

8. Para confirmar que os parceiros novo foram adicionados com sucesso, selecione o **parceiros** mosaico.

    ![](./media/logic-apps-enterprise-integration-partners/partner-5.png)

    Depois de selecionar o mosaico de parceiros, também verá parceiros adicionados recentemente no painel de parceiros.

    ![](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="how-to-edit-existing-partners-in-your-integration-account"></a>Como editar parceiros existentes na sua conta de integração

1. Selecione o **parceiros** mosaico.
2. Depois de abre o painel de parceiros, selecione o parceiro que pretende editar.
3. No **atualização parceiro** mosaico, efetue as alterações.
4. Depois de terminar, escolha **guardar**, ou para cancelar as alterações, selecione **rejeitar**.

    ![](./media/logic-apps-enterprise-integration-partners/edit-1.png)

## <a name="how-to-delete-a-partner"></a>Como eliminar um parceiro

1. Selecione o **parceiros** mosaico.
2. Depois do parceiro é aberto o painel, selecione o parceiro que pretende eliminar.
3. Escolha **eliminar**.

    ![](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>Passos seguintes
* [Saiba mais sobre contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md "Saiba mais sobre contratos de integração do enterprise")  

