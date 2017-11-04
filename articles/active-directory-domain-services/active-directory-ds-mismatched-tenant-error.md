---
title: "Resolver erros de diretório sem correspondência para existentes domínios geridos de serviços de domínio do Azure AD | Microsoft Docs"
description: "Compreender e resolver erros de diretório sem correspondência para domínios geridos de serviços de domínio do Azure AD existentes"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: maheshu
ms.openlocfilehash: 9c9a47e9b3050eb7f41202d6a4b9202ba0f379df
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Resolver erros de diretório sem correspondência para domínios geridos de serviços de domínio do Azure AD existentes
Tem um domínio gerido existente que estava ativado utilizando o portal clássico do Azure. Quando navegar para o novo portal do Azure e ver o domínio gerido, consulte a seguinte mensagem de erro:

![Erro no diretório sem correspondência](.\media\getting-started\mismatched-tenant-error.png)

Não é possível administrar este domínio gerido até que o erro está resolvido.


## <a name="whats-causing-this-error"></a>O que está a causar este erro?
Este erro deve-se ao seu domínio gerido e a rede virtual que está ativada no pertencem a diferentes dois inquilinos do Azure AD. Por exemplo, se tiver um domínio gerido chamado 'contoso.com' e tiver sido ativado para o inquilino do Azure AD da Contoso. No entanto, a Azure virtual network na qual foi ativado o domínio gerido pertence a Fabrikam - outro inquilino do Azure AD.

O novo portal do Azure (e especificamente a extensão de serviços de domínio do Azure AD), são criadas no Azure Resource Manager. No ambiente do Azure Resource Manager moderna, determinadas restrições são aplicadas para fornecer maior segurança e de acesso baseado em funções controlar (RBAC) aos recursos. Ativar os serviços de domínio do Azure AD para um inquilino do Azure AD é uma operação confidencial, uma vez que faz com que os hashes de credencial a ser sincronizadas para o domínio gerido. Esta operação requer que seja um administrador de inquilino para o diretório. Além disso, tem de ter privilégios administrativos através da rede virtual na qual ativar domínio gerido. Para verificações RBAC para trabalhar de forma consistente, o domínio gerido e a rede virtual devem pertencer ao mesmo inquilino do Azure AD.

Em suma, não é possível ativar um domínio gerido para um inquilino do Azure AD 'contoso.com' numa rede virtual que pertencem a uma subscrição do Azure pertencente a outro inquilino do Azure AD 'fabrikam.com'. Portal clássico do Azure não é desenvolvido com base na plataforma de Gestor de recursos e não impõe essas restrições.

**Configuração válida**: neste cenário de implementação, o domínio gerido Contoso está ativado para o inquilino Contoso do Azure AD. O domínio gerido está exposto numa rede virtual que pertencem a uma subscrição do Azure pertencente ao inquilino Contoso do Azure AD. Por conseguinte, tanto o domínio gerido, bem como a rede virtual pertence ao mesmo inquilino do Azure AD. Esta configuração é válido e totalmente suportados.

![Configuração de inquilino válidos](./media/getting-started/valid-tenant-config.png)

**Configuração de inquilinos sem correspondência**: neste cenário de implementação, o domínio gerido Contoso está ativado para o inquilino Contoso do Azure AD. No entanto, o domínio gerido está exposto numa rede virtual que pertença a uma subscrição do Azure pertencente ao inquilino Fabrikam do Azure AD. Por conseguinte, o domínio gerido e a rede virtual pertencem a duas diferentes inquilinos do Azure AD. Esta configuração é a configuração de erros de correspondência de inquilino e não é suportada. A rede virtual têm de ser movida para o mesmo inquilino do Azure AD (ou seja, Contoso) como o domínio gerido. Consulte o [resolução](#resolution) secção para obter detalhes.

![Configuração de inquilinos sem correspondência](./media/getting-started/mismatched-tenant-config.png)

Por conseguinte, quando o domínio gerido e a rede virtual que está ativada no pertencem a duas diferentes inquilinos do Azure AD, consulte o erro.

As seguintes regras aplicam-se no ambiente do Gestor de recursos:
- Um diretório do Azure AD pode ter várias subscrições do Azure.
- Uma subscrição do Azure pode ter vários recursos, tais como redes virtuais.
- Um único domínio gerido dos serviços de domínio do Azure AD está ativado para um diretório do Azure AD.
- Um domínio gerido dos serviços de domínio do Azure AD pode ser ativado numa rede virtual que pertencem a nenhum das subscrições do Azure no mesmo inquilino do Azure AD.


## <a name="resolution"></a>Resolução
Tem duas opções para resolver o erro de diretório não correspondentes. O utilizador pode:

- Clique em de **eliminar** botão para eliminar o existente gerido domínio. Voltar a criar utilizando o [portal do Azure](https://portal.azure.com), para que o domínio gerido e rede virtual está disponível no pertencer ao diretório do Azure AD. Associe todos os computadores anteriormente associados ao domínio eliminado recentemente criado domínio gerido.

- Mova a subscrição do Azure que contém a rede virtual para o diretório do Azure AD, à qual pertence o domínio gerido. Siga os passos a [transferir a propriedade de uma subscrição do Azure para outra conta](../billing/billing-subscription-transfer.md) artigo.


## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de domínio do Azure AD - guia de introdução](active-directory-ds-getting-started.md)
* [Serviços de domínio do Azure AD - guia de resolução de problemas](active-directory-ds-troubleshooting.md)
