---
title: "Registar o seu contrato Enterprise do Azure com a gestão de custo do Azure | Microsoft Docs"
description: Utilize o Enterprise Agreement para registar com o Azure custo Management Cloudyn.
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: quickstart
ms.custom: mvc
ms.service: cost-management
manager: carmonm
ms.openlocfilehash: 41a9df712b07253d9f5f9db8542fb9917592320f
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2017
---
# <a name="register-an-azure-enterprise-agreement-and-view-cost-data"></a>Registar um Enterprise Agreement do Azure e a vista de dados de custos

Utilize o Enterprise Agreement do Azure para registar com o Azure custo Management Cloudyn. O registo fornece acesso ao portal do Cloudyn. Este detalhes de início rápido o processo de registo necessárias para criar uma subscrição de avaliação Cloudyn e inicie sessão portal do Cloudyn. Mostra também como iniciar de imediato a ver dados de custo.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

- Inicie sessão no portal do Azure em http://portal.azure.com.

## <a name="create-a-trial-registration"></a>Criar um registo de versão de avaliação

1. No portal do Azure, clique em **custo gestão + faturação** na lista de serviços.
2. Em **descrição geral**, clique em **custo de gestão**  
    ![Página de gestão de custos](./media/quick-register-ea/cost-mgt-billing-service.png)
3. No **custo gestão** página, **aceda a gestão de custo** para abrir a página de registo de Cloudyn numa nova janela.
4. Na página de portal de registo de avaliação Cloudyn, escreva o nome da empresa e, em seguida, selecione **Azure Enterprise inscrição administrador**.  
    ![registo de avaliação](./media/quick-register-ea/trial-reg.png)
5. Introduza a chave de API de inscrição Enterprise Portal. Se não tiver a chave à mão, clique em de [Enterprise Portal](https://ea.azure.com) ligar e efetuar os passos seguintes:
  1. Inicie sessão no Web site da empresa do Azure e clique em **relatórios**, clique em **chave de acesso da API** e, em seguida, copie a chave primária.  
    ![Chave de EA API](./media/quick-register-ea/ea-key.png)
  3. Volte à página de registo e colar na sua chave de API.
6. Aceitar os termos de utilização, em seguida, validar a sua chave. Clique em **seguinte** para autorizar Cloudyn para recolher dados de recursos do Azure. Dados recolhidos incluem a utilização, desempenho, faturação e dados da etiqueta das suas subscrições.  
    ![validação de chave](./media/quick-register-ea/ea-key-validated.png)
7. Em **convidar outros intervenientes**, pode adicionar utilizadores ao escrever os respetivos endereços de e-mail. Quando terminar, clique em **seguinte**. Demora cerca de duas horas para todos os seus dados de faturação para adicionadas ao Cloudyn.
8. Clique em **aceda a Cloudyn** para abrir o portal de Cloudyn e, em seguida, no **gestão de contas de nuvem** página, deverá ver informações da sua conta EA registadas.

Para ver um vídeo tutorial sobre como registar o Enterprise Agreement, consulte o artigo [como encontrar o ID de inscrição EA e a chave de API para utilização na gestão de custo do Azure por Cloudyn](https://youtu.be/u_phLs_udig).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Passos seguintes

Este guia de introdução, utilizou as suas informações de Enterprise Agreement do Azure para registar o custo de gestão. Também com sessão iniciada no portal do Cloudyn e iniciar a visualização de dados de custo. Para saber mais sobre o Azure custo Management Cloudyn, continue para o tutorial para a gestão de custo.

> [!div class="nextstepaction"]
> [Os custos e utilização de revisão](./tutorial-review-usage.md)
