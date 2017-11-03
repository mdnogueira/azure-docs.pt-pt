---
title: "Como ligar uma subscrição do Azure para o Azure AD B2C | Microsoft Docs"
description: "Guia passo a passo para ativar a faturação de inquilino do Azure AD B2C para uma subscrição do Azure."
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: joroja
ms.openlocfilehash: 5b9955b2af7f20a79981315fa33a0eb5380a5465
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="linking-an-azure-subscription-to-an-azure-b2c-tenant-to-pay-for-usage-charges"></a>Ligar uma subscrição do Azure para um inquilino do Azure B2C pagar para custos de utilização

Custos de utilização em curso para o Azure Active Directory B2C (ou Azure AD B2C) são cobrados para uma subscrição do Azure. É necessário para o administrador de inquilino ligar explicitamente o inquilino do Azure AD B2C a uma subscrição do Azure depois de criar o inquilino do B2C.  Esta ligação é conseguida através da criação de um Azure AD "Inquilino B2C" recursos no destino da subscrição do Azure. Muitos inquilinos de B2C, podem ser associados a uma única subscrição do Azure, juntamente com outros recursos do Azure (por exemplo, VMs, armazenamento de dados, LogicApps)


> [!IMPORTANT]
> As informações mais recentes na utilização de faturação e preços para B2C é a seguinte página: [preços do Azure AD B2C](
https://azure.microsoft.com/pricing/details/active-directory-b2c/)

## <a name="step-1---create-an-azure-ad-b2c-tenant"></a>Passo 1 – criar um inquilino do Azure AD B2C
A criação de inquilino do B2C tem de ser concluída primeiro. Ignore este passo se já tiver criado o seu inquilino do B2C de destino. [Introdução ao Azure AD B2C](active-directory-b2c-get-started.md)

## <a name="step-2---open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Passo 2 - abra portal do Azure no inquilino do Azure AD que mostra a sua subscrição do Azure
Navegue para o [portal do Azure](https://portal.azure.com). Mudar para o inquilino do Azure AD que mostra a subscrição do Azure que pretende utilizar. Este inquilino do Azure AD é diferente do inquilino do B2C. No portal do Azure, clique no nome de conta no canto superior direito do dashboard para selecionar o inquilino do Azure AD. É necessária uma subscrição do Azure para continuar. [Obter uma subscrição do Azure](https://account.windowsazure.com/signup?showCatalog=True)

![Mudar para o inquilino do Azure AD](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="step-3---create-a-b2c-tenant-resource-in-azure-marketplace"></a>Passo 3 – criar um recurso de inquilino do B2C no Azure Marketplace
Abra o Marketplace clicando no ícone de Marketplace ou selecionar a verde "+" no canto superior esquerdo do dashboard.  Procure e selecione o Azure Active Directory B2C. Selecione criar.

![Selecione o Marketplace](./media/active-directory-b2c-how-to-enable-billing/marketplace.png)

![Pesquisa AD B2C](./media/active-directory-b2c-how-to-enable-billing/searchb2c.png)

O recurso do Azure AD B2C criar caixa de diálogo abrange os seguintes parâmetros:

1. Azure AD B2C inquilino – selecione um inquilino do Azure AD B2C, na lista pendente.  Mostram apenas de elegíveis inquilinos do Azure AD B2C.  Elegíveis B2C inquilinos cumprirem seguintes condições: é o administrador global do inquilino B2C e o inquilino do B2C não está atualmente associado a uma subscrição do Azure

2. Nome de recurso de AD B2C do Azure - está pré-selecionada corresponder ao nome do domínio de inquilino do B2C

3. Subscrição - uma subscrição do Azure Active Directory em que é um administrador ou coadministrador.  Vários inquilinos do Azure AD B2C podem ser adicionados a uma subscrição do Azure

4. Localização do grupo de recursos e o grupo de recursos - este artefactos ajudam a organizar os vários recursos do Azure.  Esta opção não tem impacto na sua localização de inquilino do B2C, o desempenho ou o estado de faturação

5. Afixar ao dashboard para mais fácil acesso às suas informações de faturação de inquilino B2C e as definições de inquilino do B2C ![criar recursos do B2C](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="step-4---manage-your-b2c-tenant-resources-optional"></a>Passo 4 – gerir os recursos de inquilino do B2C (opcionais)
Após a conclusão da implementação, um novo recurso de "Inquilino B2C" é criado no grupo de recursos de destino e relacionadas com subscrição do Azure.  Deverá ver um novo recurso do tipo "Inquilino B2C" adicionado juntamente com os outros recursos do Azure.

![Criar o recurso do B2C](./media/active-directory-b2c-how-to-enable-billing/b2cresourcedashboard.png)

Ao clicar em recursos de inquilino do B2C, conseguir
- Clique no nome de subscrição para rever as informações de faturação. Consulte a faturação e de utilização.
- Clique em definições do Azure AD B2C para abrir um novo separador do browser diretamente no seu inquilino do B2C painel Definições
- Submeter pedidos de suporte
- Mova os recursos de inquilino do B2C para outra subscrição do Azure ou para outro grupo de recursos.  Este alterações à escolha a subscrição do Azure recebe custos de utilização.

![Definições de recursos do B2C](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>Problemas conhecidos
- Eliminação de inquilino do B2C. Se for criado um inquilino do B2C, eliminado e recriado com o mesmo nome de domínio, também elimine e recrie o recurso "Linking" com o mesmo nome de domínio.  Irá encontrar este recurso de "Linking" em "Todos os recursos" no inquilino da subscrição através do portal do Azure.
- Restrições de Self-impostas na localização de recursos regional.  Em casos raros, um utilizador pode estabelecer uma restrição regional para a criação de recursos do Azure.  Esta restrição poderá impedir a criação da ligação entre uma subscrição do Azure e um inquilino do B2C. Para atenuar, reduzir esta restrição.

## <a name="next-steps"></a>Passos seguintes
Assim que estes passos estiverem concluídos para cada um dos seus inquilinos de B2C, a sua subscrição do Azure é faturada de acordo com os detalhes da sua direta do Azure ou contrato Enterprise.
- Reveja a utilização e faturação na sua subscrição do Azure selecionada
- Reveja os relatórios de utilização por dia detalhada utilizando o [API de relatórios de utilização](active-directory-b2c-reference-usage-reporting-api.md)
