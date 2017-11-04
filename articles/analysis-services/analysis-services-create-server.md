---
title: Criar um servidor de Analysis Services no Azure | Microsoft Docs
description: "Saiba como criar uma instância de servidor do Analysis Services no Azure."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 7f560216-8a9a-4d06-852e-48cf24deab19
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 10f34fe17c6b8faad3bcb7bcffe9d9c3c0d8b10a
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="create-an-azure-analysis-services-server-in-azure-portal"></a>Criar um servidor de Analysis Services do Azure no portal do Azure
Este artigo explica como criar um recurso de servidor do Analysis Services na sua subscrição do Azure.

## <a name="before-you-begin"></a>Antes de começar
Para concluir este guia de início rápido, necessita de:

* **Subscrição do Azure**: aceda à [Avaliação Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) para criar uma conta.
* **Azure Active Directory**: sua subscrição tem de ser associada a um inquilino do Azure Active Directory. E, terá de iniciar sessão no Azure com uma conta no diretório do Azure Active Directory. Não são suportadas contas Microsoft. Para saber mais,v eja [Authentication and user permissions](analysis-services-manage-users.md) (Autenticação e permissões de utilizador).
* **Grupo de recursos**: Utilize um grupo de recursos já tiver ou [criar um novo](../azure-resource-manager/resource-group-overview.md).

> [!NOTE]
> A criação de um servidor poderá resultar num novo serviço sujeito a faturação. Para obter mais informações, veja [Preços do Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).
> 
> 

## <a name="to-create-a-server-in-azure-portal"></a>Para criar um servidor no portal do Azure
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).  
2. Clique em **+ novo** > **dados + análise** > **Analysis Services**.
3. No **Analysis Services** painel, preencha os campos obrigatórios e, em seguida, prima **criar**.
   
    ![Criar servidor do](./media/analysis-services-create-server/aas-create-server-blade.png)
   
   * **Nome do servidor**: escreva um nome exclusivo utilizado para referenciar o servidor.
   * **Subscrição**: selecione a subscrição faturas este servidor a.
   * **Grupo de recursos**: Estes contentores foram concebidos para ajudar a gerir uma coleção de recursos do Azure. Para obter mais informações, consulte [grupos de recursos](../azure-resource-manager/resource-group-overview.md).
   * **Localização**: localização de centro de dados do Azure este aloja o servidor. Escolha uma localização mais próximo da sua base de utilizadores maior.
   * **Escalão de preço**: selecione um escalão de preço. Tabela modelos até 400 GB são suportados. Para obter mais informações, consulte [preços do Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).
4. Clique em **Criar**.

Criar normalmente tem num minuto; muitas vezes, apenas alguns segundos. Se tiver selecionado **adicionar ao Portal**, navegue até ao seu portal para ver o novo servidor. Ou, navegue para **mais serviços** > **Analysis Services** para ver se o servidor está pronto.

 ![Dashboard](./media/analysis-services-create-server/aas-create-server-dashboard.png)


## <a name="next-steps"></a>Passos seguintes
Assim que tiver criado o seu servidor, pode [implementar um modelo](analysis-services-deploy.md) ao mesmo utilizando o SSDT ou com o SSMS.

Se um modelo de implementação para o servidor se liga a origens de dados no local, terá de instalar um [gateway de dados no local](analysis-services-gateway.md) num computador na sua rede.

