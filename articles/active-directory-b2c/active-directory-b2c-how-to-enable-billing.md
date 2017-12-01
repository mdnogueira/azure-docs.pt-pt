---
title: "Como ligar uma subscrição do Azure para o Azure AD B2C | Microsoft Docs"
description: "Guia passo a passo para ativar a faturação de inquilino do Azure AD B2C para uma subscrição do Azure."
services: active-directory-b2c
documentationcenter: dev-center-name
author: parakhj
manager: krassk
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2017
ms.author: parja
ms.openlocfilehash: 35fab74abf2c2ba27a8bf99eb93eb53f39b26227
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="linking-an-azure-subscription-to-an-azure-ad-b2c-tenant"></a>Ligar uma subscrição do Azure para um inquilino do Azure AD B2C

> [!IMPORTANT]
> As informações mais recentes na utilização de faturação e preços para o Azure AD B2C é a seguinte página: [preços do Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

Custos de utilização para o Azure AD B2C são cobrados para uma subscrição do Azure. Quando é criado um inquilino do Azure AD B2C, o administrador de inquilino tem de ligar explicitamente o inquilino do Azure AD B2C a uma subscrição do Azure. Este artigo mostra-lhe como.

> [!NOTE]
> Uma subscrição associada a um inquilino do Azure AD B2C só pode ser utilizada para a faturação da utilização do Azure AD B2C. A subscrição não pode ser utilizada para adicionar outros serviços do Azure ou do Office 365 licenças *no inquilino do Azure AD B2C*.

 A ligação de subscrição é conseguida através da criação de um Azure AD B2C "recurso" dentro do subscrição do Azure de destino. Azure AD B2C muitos "recursos" podem ser criados dentro de uma única subscrição do Azure, juntamente com outros recursos do Azure (por exemplo, VMs, o armazenamento de dados, LogicApps). Pode ver todos os recursos dentro da subscrição acedendo ao inquilino do Azure AD que a subscrição está associada.

É necessária uma subscrição do Azure válida para continuar.

## <a name="create-an-azure-ad-b2c-tenant"></a>Criar um inquilino do Azure AD B2C

Deve primeiro [criar um inquilino do Azure AD B2C](active-directory-b2c-get-started.md) que gostaria de ligar uma subscrição. Ignore este passo se já tiver criado um inquilino do Azure AD B2C.

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Portal do Azure aberto no inquilino do Azure AD que mostra a sua subscrição do Azure

Navegue para o inquilino do Azure AD que mostra a sua subscrição do Azure. Abra o [portal do Azure](https://portal.azure.com)e mude para o inquilino do Azure AD que mostra a subscrição do Azure que pretende utilizar.

![Mudar para o inquilino do Azure AD](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>Localizar o Azure AD B2C no Azure Marketplace

Clique no botão **Novo**. No campo **Procurar no Marketplace**, escreva `B2C`.

![Adicionar o texto do Azure AD B2C e botão realçado na pesquisa o campo do marketplace](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

Na lista de resultados, selecione **do Azure AD B2C**.

![O Azure AD B2C selecionado na lista de resultados](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

São apresentados detalhes sobre o Azure AD B2C. Para começar a configurar o seu novo inquilino do Azure Active Directory B2C, clique no botão **Criar**.

No ecrã de criação de recursos, selecione **inquilino de ligação do Azure AD B2C existente, a minha subscrição do Azure**.

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>Crie um recurso do Azure AD B2C dentro da subscrição do Azure

Na caixa de diálogo de criação de recursos, selecione um inquilino do Azure AD B2C na lista pendente. Irá ver todos os inquilinos que é o administrador global e aqueles que já não estiverem ligados a uma subscrição.

O nome de recurso do Azure AD B2C será pré-selecionada corresponder ao nome do domínio do inquilino do Azure AD B2C.

Para a subscrição, selecione uma subscrição do Azure Active Directory que é o administrador.

Selecione um grupo de recursos e localização do grupo de recursos. A seleção aqui não tem impacto na sua localização de inquilino do Azure AD B2C, o desempenho ou o estado de faturação.

![Criar o recurso do B2C](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenent-resources"></a>Gerir os recursos de inquilino do Azure AD B2C

Depois de um recurso do Azure AD B2C foi criado com êxito dentro da subscrição do Azure, verá um novo recurso do tipo "Inquilino B2C" adicionado juntamente com os outros recursos do Azure.

Pode utilizar este recurso:

- Navegue para a subscrição para rever as informações de faturação.
- Aceda ao seu inquilino do Azure AD B2C
- Submeter pedidos de suporte
- Mova os recursos de inquilino do Azure AD B2C para outra subscrição do Azure ou para outro grupo de recursos.

![Definições de recursos do B2C](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>Problemas conhecidos

### <a name="csp-subscriptions"></a>Subscrições de CSP

Atualmente, um inquilino do Azure AD B2C **não é possível** ligação a subscrições do CSP.

### <a name="self-imposed-restrictions"></a>Restrições de Self-impostas

Um utilizador pode estabelecer uma restrição regional para a criação de recursos do Azure. Esta restrição poderá impedir a criação de recursos do Azure AD B2C. Para atenuar, reduzir esta restrição.

## <a name="next-steps"></a>Passos seguintes

Assim que estes passos estiverem concluídos para cada um dos seus inquilinos do Azure AD B2C, a sua subscrição do Azure é faturada de acordo com os detalhes da sua direta do Azure ou contrato Enterprise.

Pode rever os detalhes de faturação e de utilização na sua subscrição do Azure selecionada. Também pode rever os relatórios de utilização por dia detalhada utilizando o [API do relatório de utilização](active-directory-b2c-reference-usage-reporting-api.md).
