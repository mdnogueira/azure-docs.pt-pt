---
title: "Registar informações do parceiro de CSP a utilizar com a gestão de custo do Azure | Microsoft Docs"
description: "Utilize as informações do parceiro de CSP para registar com o Azure custo Management Cloudyn."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: quickstart
ms.custom: mvc
ms.service: cost-management
manager: carmonm
ms.openlocfilehash: bcb072a2f2ab8c0e5097fca2c95309464483cb53
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>Registar com o programa de parceiro de CSP e a vista de dados de custos

Como um parceiro CSP, pode registar com o Azure custo Management Cloudyn. O registo fornece acesso ao portal do Cloudyn. Este detalhes de início rápido o processo de registo necessárias para criar uma subscrição de avaliação Cloudyn e inicie sessão portal do Cloudyn. Mostra também como iniciar de imediato a ver dados de custo.


>[!NOTE]
>Para concluir o registo, tem de ser um administrador do programa de parceiros com acesso à API de centro de parceiros. Não é necessária para a autenticação e acesso a dados a configuração da API de centro de parceiros. Para obter mais informações, consulte [ligar para a API do Centro de parceiros](https://msdn.microsoft.com/library/partnercenter/mt709136.aspx). Além disso, os utilizadores CSP indiretos só podem utilizar Cloudyn quando o seu revendedor a partir do CSP direta regista com Cloudyn para fornecer acesso aos respetivos clientes e subscrições.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

- Inicie sessão no portal do Azure em http://portal.azure.com.

## <a name="create-a-trial-registration"></a>Criar um registo de versão de avaliação

1. No portal do Azure, clique em **custo gestão + faturação** na lista de serviços.
2. Em **descrição geral**, clique em **custo de gestão**  
    ![Página de gestão de custos](./media/quick-register-csp/cost-mgt-billing-service.png)
3. No **custo gestão** página, clique em **aceda a gestão de custo** para abrir a página de registo de Cloudyn numa nova janela.
4. Na página de portal de registo de avaliação Cloudyn, escreva o nome da empresa, selecionar **Microsoft CSP parceiro programa administrador**e, em seguida, clique em **seguinte**.  
5. Introduza um **ID da aplicação**, **ID de comércio**, **chave de segredo da aplicação**e selecione o **predefinido preços planear**. Se não tiver as informações à mão, inicie sessão no portal do Centro de parceiros em [https://partnercenter.microsoft.com](https://partnercenter.microsoft.com) com o seu administrador principal da conta e efetue os seguintes passos:
  1. Aceda a **Dashboard** > **definições da conta** > **gestão de aplicações**.
  2. Se anteriormente tiver criado uma aplicação Web, ignore este passo. Caso contrário, clique em **Adicionar nova aplicação de web** no **aplicação Web** secção.
  3. Copiar o **ID da aplicação** GUID da sua aplicação web.
  4. Copiar o **ID de comércio** GUID da sua aplicação web.
  5. Selecione a duração de validade da chave como um ou dois anos, conforme necessário. Selecione **Adicionar chave** e, em seguida, copie e guarde o valor da chave secreto.  
    ![Centro de parceiros de CSP](./media/quick-register-csp/csp-partner-center.png)
  6. Volte à página de registo e cole as informações.  
      ![Credenciais da conta CSP](./media/quick-register-csp/csp-reg.png)
6. Aceitar os termos de utilização, em seguida, validar as suas informações. Clique em **seguinte** para autorizar Cloudyn para recolher dados de recursos do Azure. Dados recolhidos incluem a utilização, desempenho, faturação e dados da etiqueta das suas subscrições.  
7. Em **convidar outros intervenientes**, pode adicionar utilizadores ao escrever os respetivos endereços de e-mail. Quando terminar, clique em **seguinte**. Demora cerca de duas horas para todos os seus dados de faturação para adicionadas ao Cloudyn.
8. Clique em **aceda a Cloudyn** para abrir o portal de Cloudyn e, em seguida, no **gestão de contas de nuvem** página, deverá ver informações da sua conta CSP registadas.

## <a name="configure-indirect-csp-access-in-cloudyn"></a>Configurar o acesso CSP indireto no Cloudyn

Por predefinição, a API do Centro de parceiros só estão acessível para direcionar os CSPs. No entanto, um fornecedor CSP direto pode configurar o acesso para os respetivos indiretos CSP clientes ou parceiros utilizar grupos de entidade em Cloudyn.

Para ativar o acesso ao indiretos CSP clientes ou parceiros, siga os passos no [criar um registo de versão de avaliação](#create-a-trial-registration) para configurar um registo de versão de avaliação. Em seguida, conclua os seguintes passos para dados CSP indiretos segmento utilizando grupos de entidade Cloudyn. Em seguida, atribua as permissões de utilizador adequada para os grupos de entidade.

1. Criar um grupo de entidade com as informações no [criar entidades](tutorial-user-access.md#create-entities).
2. Siga os passos indicados em [atribuir subscrições para entidades de custo](https://support.cloudyn.com/hc/en-us/articles/115005139425-Video-Assigning-subscriptions-to-Cost-Entities). Associe conta do cliente CSP indireta e as suas subscrições do Azure para a entidade que criou anteriormente.
3. Siga os passos indicados em [criar um utilizador com acesso de administrador](tutorial-user-access.md#create-a-user-with-admin-access) para criar uma conta de utilizador com acesso de administrador. Em seguida, certifique-se que a conta de utilizador tem acesso de administrador para entidades específicas que criou anteriormente para a conta indireta.

Parceiros CSP indiretos a iniciar sessão no portal de Cloudyn utilizando as contas que criou para os mesmos.


[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Passos seguintes

Este guia de introdução, utilizou as suas informações de CSP para registar o custo de gestão. Também com sessão iniciada no portal do Cloudyn e iniciar a visualização de dados de custo. Para saber mais sobre o Azure custo Management Cloudyn, continue para o tutorial para a gestão de custo.

> [!div class="nextstepaction"]
> [Os custos e utilização de revisão](./tutorial-review-usage.md)
