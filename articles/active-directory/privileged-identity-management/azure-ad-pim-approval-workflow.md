---
title: "Fluxos de trabalho de aprovação de gestão de identidades privilegiadas do Azure | Microsoft Docs"
description: "Saiba mais sobre fluxos de trabalho de aprovação no Privileged Identity Management (PIM)"
services: active-directory
documentationcenter: 
author: barclayn
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: cf6a9213fa0a1cba8725aabb42abe51b805ece7a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="approvals-preview"></a>Aprovações (pré-visualização)

## <a name="overview"></a>Descrição geral

Com aprovações para Privileged Identity Management, pode configurar funções para exigir a aprovação para a ativação e escolher um ou vários utilizadores ou grupos como aprovadores delegados. Manter a ler para saber como configurar as funções e selecione aprovadores.

>[!NOTE]
. Tenha em atenção que esta funcionalidade ainda está em desenvolvimento e poderá encontrar erros. A funcionalidade, incluindo texto convenções de nomenclatura estão sujeitos a alterações e não devem ser consideradas final.


## <a name="key-terminology"></a>Terminologia-chave

*Elegíveis de função de utilizador* – um utilizador elegível função é um utilizador na sua organização que está atribuído a uma função do Azure AD como elegíveis (função necessita de ativação).

*Delegado aprovador* – um aprovador delegado é um ou vários utilizadores individuais ou grupos no seu Azure AD que são responsáveis pela aprovação de pedidos de ativação de função.

## <a name="scenarios"></a>Cenários

A pré-visualização privada suporta os seguintes cenários:

**Como um privilégio função de administrador (PRA), pode:**

-   [Ativar a aprovação para funções específicas](#enable-approval-for-specific-roles)

-   [Especifique o aprovador utilizadores e/ou grupos para aprovar pedidos](#specify-approver-users-and/or-groups-to-approve-requests)

-   [ver o histórico do pedido e aprovação para funções com todos os privilégios](#view-request-and-approval-history-for-all-privileged-roles)

**Como um aprovador designado, pode:**

-   [Ver pendentes aprovações (pedidos)](#view-pending-approvals-requests)

-   [aprovar ou rejeitar pedidos de elevação de função (único e/ou em massa)](#approve-or-reject-requests-for-role-elevation-single-and/or-bulk)

-   [fornecer a justificação para os meus aprovação/rejeição](#provide-justification-for-my-approval/rejection) 

**Como função de utilizador elegível, pode:**

-   [pedir a ativação de uma função que necessita de aprovação](#request-activation-of-a-role-that-requires-approval)

-   [ver o estado do seu pedido para ativar](#view-the-status-of-your-request-to-activate)

-   [concluir a tarefa no Azure AD, se foi aprovada a ativação](#complete-your-task-in-azure-ad-if-activation-was-approved)

### <a name="navigation"></a>Navegação

Atualizámos o painel de navegação para suportar aprovações

![](media/azure-ad-pim-approval-workflow/image001.png)

A predefinição destino página fornece acesso prático para obter informações sobre o PIM e a nova documentação de aprovações.

![](media/azure-ad-pim-approval-workflow/image002.png)

Adicionámos também uma nova secção para todos os utilizadores do PIM, 'Meu histórico de auditoria'. Aqui pode encontrar todas as informações relevantes para a sua identidade. Isto inclui todos os seus pedidos pendentes e concluídos, as decisões que fez sobre os pedidos de que resolver e todos os seus passado ativações de função numa localização conveniente.

![](media/azure-ad-pim-approval-workflow/image003.png)

### <a name="enable-approval-for-specific-roles"></a>Ativar a aprovação para funções específicas

Para ativar a aprovação para uma função específica, selecione as funções de diretório do painel de navegação esquerdo.

![](media/azure-ad-pim-approval-workflow/image004.png)

Localize e selecione as definições no painel de navegação esquerdo funções de diretório

![](media/azure-ad-pim-approval-workflow/image006.png)

Selecione funções com privilégios:

![](media/azure-ad-pim-approval-workflow/image009.png)

Selecione a opção "Ativar" na exigir a secção de aprovação:

![](media/azure-ad-pim-approval-workflow/image011.png)

Uma vez ativada, o painel será expandido para mostrar os seguintes detalhes:

![](media/azure-ad-pim-approval-workflow/image013.png)

>[!NOTE]
Se fazer não especificou qualquer aprovadores, o PRA(s) tornar-se a approver(s) predefinido. PRA(s) seriam necessários para aprovar todos os pedidos de ativação para esta função.

### <a name="specify-approver-users-andor-groups-to-approve-requests"></a>Especifique o aprovador utilizadores e/ou grupos para aprovar pedidos

Para delegar a aprovação, clique na opção para "Aprovadores selecionadas":

![](media/azure-ad-pim-approval-workflow/image015.png)

Quando carrega o painel de aprovadores selecione, pode procurar um utilizador específico ou um grupo utilizando a barra de pesquisa na parte superior, ou de selecionar da lista pré-preenchidos, em seguida, clique em "Selecionar" quando concluída:

![](media/azure-ad-pim-approval-workflow/image017.png)

Nota: Pode selecionar vários utilizadores ou grupos de cada vez.

A seleção irá aparecer na lista de aprovadores selecionados, como mostrado abaixo:

![](media/azure-ad-pim-approval-workflow/image019.png)

Para remover um aprovador, basta clicar no botão Remover junto do respetivo nome.

Para adicionar aprovadores adicionais, repita o processo.

## <a name="view-request-and-approval-history-for-all-privileged-roles"></a>Ver o histórico do pedido e aprovação para funções com todos os privilégios

Para ver o histórico de pedido e aprovação para funções com todos os privilégios, selecione o histórico de auditorias partir do dashboard:

![](media/azure-ad-pim-approval-workflow/image021.png)

>[!NOTE]
Pode ordenar os dados através da ação e procure "Ativação aprovado"

### <a name="view-pending-approvals-requests"></a>Ver pendentes aprovações (pedidos)

Como um aprovador delegado, receberá notificações por e-mail quando um pedido está a aguardar a aprovação. Para ver estes pedidos no portal do PIM, a partir do dashboard (no painel de navegação nova), selecione o separador "Pedidos de aprovação pendentes" a na barra de navegação esquerdo.

![](media/azure-ad-pim-approval-workflow/image023.png)

A partir daí, verá uma lista dos pedidos com aprovação pendente:

![](media/azure-ad-pim-approval-workflow/image024.png)

### <a name="approve-or-reject-requests-for-role-elevation-single-andor-bulk"></a>Aprovar ou rejeitar pedidos de elevação de função (único e/ou em massa)

Selecione os pedidos que pretende aprovar ou recusar e clique no botão na barra de ação que corresponde com a sua decisão:

![](media/azure-ad-pim-approval-workflow/image025.png)

### <a name="provide-justification-for-my-approvalrejection"></a>Fornecer a justificação para os meus aprovação/rejeição

Esta ação irá abrir um novo painel para aprovar ou recusar vários pedidos em simultâneo. Introduza uma justificação para a sua decisão, e clique em aprovar (ou negar) na parte inferior, ou o painel:

![](media/azure-ad-pim-approval-workflow/image029.png)

Quando o processo de pedido estiver concluído, o símbolo de estado irá refletir a decisão que tiver tomado (neste exemplo, a decisão é aprovar):

![](media/azure-ad-pim-approval-workflow/image031.png)

### <a name="request-activation-of-a-role-that-requires-approval"></a>Ativação de uma função que requeira a aprovação do pedido

Pode ser iniciado a pedir a ativação de uma função que requeira a aprovação do painel de navegação PIM antiga ou nova painel de navegação, como o processo de ativação de função permanece igual. Basta Selecione uma função na lista de funções para ativar:

![](media/azure-ad-pim-approval-workflow/image033.png)

Se precisar de uma função com privilégios multi-factor Authentication, será solicitada a concluir primeiro essa tarefa:

![](media/azure-ad-pim-approval-workflow/image035.png)

Depois de concluído, clique em ativar e forneça uma justificação (se necessário):

![](media/azure-ad-pim-approval-workflow/image037.png)

O requerente verá uma notificação que o pedido está com aprovação pendente:

![](media/azure-ad-pim-approval-workflow/image039.png)

### <a name="view-the-status-of-your-request-to-activate"></a>Ver o estado do seu pedido para ativar

Visualizar o estado de um pedido pendente para ativar deve ser acedido a partir de navegação de novo. Na barra de navegação esquerdo, selecione o separador "Os meus pedidos":

![](media/azure-ad-pim-approval-workflow/image041.png)

O estado do pedido está predefinida como "Pendentes", mas pode alternar para ver todas ou negado pedidos.

### <a name="complete-your-task-in-azure-ad-if-activation-was-approved"></a>Concluir a tarefa no Azure AD, se foi aprovada a ativação

Quando o pedido é aprovado, a função está ativa e pode continuar com o trabalho que requer esta função.

![](media/azure-ad-pim-approval-workflow/image043.png)

## <a name="next-steps"></a>Passos seguintes

Os seus comentários são importantes para nós. Hesite partilhar os seus comentários ou comentários connosco aqui!
