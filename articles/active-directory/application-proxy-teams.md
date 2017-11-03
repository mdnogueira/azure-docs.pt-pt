---
title: "Aceder a aplicações de Proxy de aplicações do Azure AD no equipas | Microsoft Docs"
description: "Utilize o Proxy de aplicações do Azure AD para aceder a sua aplicação no local através de Teams da Microsoft."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: df2ffb8958a7d4b881f0a6904fb9ca13c3614040
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Aceder as aplicações no local através de Teams da Microsoft

Proxy de aplicações do Active Directory do Azure dá-lhe o início de sessão único para aplicações no local, independentemente de onde estiver. Microsoft Teams simplifica os esforços de colaboração num único local. Integrar as duas em conjunto, significa que os utilizadores podem ser produtivos com os respetivos teammates em qualquer situação. 

Os utilizadores podem adicionar aplicações em nuvem para os seus canais de equipas [utilizando separadores](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US), mas e sobre os SharePoint sites ou a ferramenta de planeamento que estão alojados no local? Proxy de aplicações é a solução. Podem ser adicionados a aplicações publicadas através do Proxy de aplicações para os seus canais utilizando os mesmos URLs externos utilizam sempre aceder remotamente as suas aplicações. E porque o Proxy de aplicações efetua a autenticação através do Azure Active Directory, os utilizadores obtêm uma experiência de início de sessão único.


## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Instalar o conector do Proxy de aplicações e publicar a aplicação

Se ainda não o fez, [configurar o Proxy de aplicações para o seu inquilino e instalar o conector](active-directory-application-proxy-enable.md). Em seguida, [publicar a aplicação no local](application-proxy-publish-azure-portal.md) para acesso remoto. Quando estiver a publicar a aplicação, anote o URL externo porque é utilizado para adicionar a aplicação para equipas.

Se já tiver as aplicações publicadas, mas não se lembra da respetiva URLs externos, procure-os [portal do Azure](https://portal.azure.com). A iniciar sessão, em seguida, navegue para **do Azure Active Directory** > **aplicações empresariais** > **todas as aplicações** > selecione a sua aplicação >  **Proxy de aplicações**.

## <a name="add-your-app-to-teams"></a>Adicionar a sua aplicação para equipas

Depois de publicar a aplicação através do Proxy de aplicações, informar os utilizadores que pode adicioná-lo como um separador diretamente os canais de equipas e, em seguida, a aplicação está disponível para todos os membros da equipa a utilizar. Este que seguir estes três passos:

1. Navegue para o canal de equipas para adicionar esta aplicação e selecione onde pretende  **+**  para adicionar um separador.

   ![Selecionar adicionar um separador](./media/application-proxy-teams/add-tab.png)

2. Selecione **Web site** entre as opções do separador.

   ![Adicionar um Web site](./media/application-proxy-teams/website.png)

3. Atribua o separador um nome e defina o URL para o URL externo de Proxy de aplicações. 

   ![Configurar URL e o nome do separador](./media/application-proxy-teams/tab-name-url.png)

Depois de um membro de um agrupamento adiciona o separador, aparece para todos os membros do canal. Os utilizadores que têm acesso à aplicação aceder único início de sessão com as credenciais que utilizam para Teams da Microsoft. Os utilizadores que não tem acesso à aplicação podem ver o separador equipas, mas estão bloqueados até, dar-lhes permissões para a aplicação no local e a versão publicada da aplicação do portal do Azure. 

## <a name="next-steps"></a>Passos seguintes

- Saiba como [publicar sites do SharePoint no local](application-proxy-enable-remote-access-sharepoint.md) com o Proxy da aplicação.
- Configurar as suas aplicações a utilizar [domínios personalizados](active-directory-application-proxy-custom-domains.md) para as respetivas URL externo. 
