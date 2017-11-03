---
title: "Registar a sua subscrição do Azure com a gestão de custo do Azure | Microsoft Docs"
description: "Utilize a sua subscrição do Azure para registar com o Azure custo Management Cloudyn."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: quickstart
ms.custom: mvc
ms.service: cost-management
manager: carmonm
ms.openlocfilehash: 4fed181a004ba6a5ea35dcbe1366e7d48342c96d
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2017
---
# <a name="register-an-individual-azure-subscription-and-view-cost-data"></a>Registar uma subscrição do Azure individuais e a vista de dados de custos

Utilizar a sua subscrição do Azure para registar com o Azure custo Management Cloudyn. O registo fornece acesso ao portal do Cloudyn. Este detalhes de início rápido o processo de registo necessárias para criar uma subscrição de avaliação Cloudyn e inicie sessão portal do Cloudyn. Mostra também como iniciar de imediato a ver dados de custo.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

- Inicie sessão no portal do Azure em http://portal.azure.com.

## <a name="create-a-trial-registration"></a>Criar um registo de versão de avaliação

1. No portal do Azure, clique em **custo gestão + faturação** na lista de serviços.
2. Em **descrição geral**, clique em **custo de gestão**  
    ![Página de gestão de custos](./media/quick-register-azure-sub/cost-mgt-billing-service.png)
3. No **custo gestão** página, clique em **aceda a gestão de custo** para abrir a página de registo de Cloudyn numa nova janela.
4. Na página de portal de registo de avaliação Cloudyn, escreva o nome da empresa e, em seguida, selecione **individuais proprietário da subscrição do Azure** e, em seguida, clique em **seguinte**. O nome de conta e o ID do inquilino é automaticamente adicionado ao formulário.  
    ![registo de avaliação](./media/quick-register-azure-sub/trial-reg-ind.png)
5. Selecione o **oferecem ID - nome** associados à subscrição. Se não tiver a certeza de qual é o ID de velocidade para a sua subscrição, pode ver a fatura do Azure e procure **oferecem ID**.
6. Aceitar os termos de utilização, em seguida, validar as suas informações e, em seguida, clique em **seguinte**.
7. No **recolher dados adicionais** página, clique em **seguinte** para autorizar Cloudyn para recolher dados de recursos do Azure. Dados recolhidos incluem a utilização, desempenho, faturação e dados da etiqueta das suas subscrições.  
    ![recolher dados adicionais](./media/quick-register-azure-sub/gather-additional.png)
8. O browser leva-o para o página de início de sessão para Cloudyn. Inicie sessão com as credenciais da sua subscrição do Azure.
9. Clique em **aceda a Cloudyn** para abrir o portal de Cloudyn e, em seguida, no **gestão de contas** página, deverá ver as informações da conta de subscrição do Azure.  
    ![Gestão de contas](./media/quick-register-azure-sub/accounts-mgt.png)

Para ver um vídeo tutorial sobre como registar a sua subscrição do Azure, consulte o artigo [ao localizar o GUID de diretório e o ID de velocidade para utilização na gestão de custo do Azure por Cloudyn](https://youtu.be/PaRjnyaNGMI).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Passos seguintes

Este guia de introdução, utilizou as suas informações de subscrição do Azure para registar o custo de gestão. Também com sessão iniciada no portal do Cloudyn e iniciar a visualização de dados de custo. Para saber mais sobre o Azure custo Management Cloudyn, continue para o tutorial para a gestão de custo.

> [!div class="nextstepaction"]
> [Os custos e utilização de revisão](./tutorial-review-usage.md)
