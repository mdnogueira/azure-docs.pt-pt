---
title: Criar um inquilino do Azure Active Directory B2C | Microsoft Docs
description: "Um tópico sobre como criar um inquilino do Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: patricka
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 06/07/2017
ms.author: parja
ms.openlocfilehash: 0a81b8717f9cd78b9e5c39267ff187656b0b2827
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="create-an-azure-active-directory-b2c-tenant-in-the-azure-portal"></a>Criar um inquilino do Azure Active Directory B2C no portal do Azure

Este guia de introdução ajuda-o a criar um inquilino do Microsoft Azure Active Directory (Azure AD) B2C em apenas alguns minutos. Quando tiver terminado, tem um inquilino do B2C (também conhecido como um diretório) a utilizar para registar as aplicações do B2C.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Criar um inquilino do Azure AD B2C

Os inquilinos existentes não podem ser ativadas funcionalidades do B2C. Terá de criar um inquilino do Azure AD B2C.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

Parabéns, criou um inquilino do Azure Active Directory B2C. É um Administrador Global do inquilino. Pode adicionar outros administradores globais conforme necessário. Para mudar para o seu inquilino novo, clique em de *gerir a nova ligação de inquilino*.

![Gerir a nova ligação de inquilino](./media/active-directory-b2c-get-started/manage-new-b2c-tenant-link.png)

> [!IMPORTANT]
> Se estiver a planear utilizar um inquilino do B2C para uma aplicação de produção, leia o artigo sobre [escala de produção vs inquilinos de pré-visualização B2C](active-directory-b2c-reference-tenant-type.md). Não existe são problemas conhecidos quando eliminar um inquilino do B2C existente e volte a criá-la com o mesmo nome de domínio. Terá de criar um inquilino do B2C com um nome de domínio diferente.
>
>

## <a name="link-your-tenant-to-your-subscription"></a>Ligar o seu inquilino para a sua subscrição

Terá de ligar o seu inquilino do Azure AD B2C a sua subscrição do Azure para ativar todas as funcionalidades do B2C e paga custos de utilização. Para obter mais informações, leia [neste artigo](active-directory-b2c-how-to-enable-billing.md). Se não ligar o seu inquilino do Azure AD B2C a sua subscrição do Azure, algumas funcionalidades é bloqueada e, se vir uma mensagem de aviso ("nenhuma subscrição associado a este inquilino do B2C ou as necessidades de subscrição sua atenção.") nas definições do B2C. É importante que efetuar este passo antes das suas aplicações são enviados para a produção.

## <a name="easy-access-to-settings"></a>Facilitar o acesso às definições

[!INCLUDE [active-directory-b2c-find-service-settings](../../includes/active-directory-b2c-find-service-settings.md)]

Também pode aceder ao painel introduzindo `Azure AD B2C` no **procurar recursos** na parte superior do portal. Na lista de resultados, selecione **do Azure AD B2C** aceder ao painel de definições do B2C.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Registar a aplicação de B2C num inquilino do B2C](active-directory-b2c-app-registration.md)