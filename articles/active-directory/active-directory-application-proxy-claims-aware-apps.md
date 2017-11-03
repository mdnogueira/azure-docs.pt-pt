---
title: "Aplicações com suporte para afirmações - Proxy de aplicações do Azure AD | Microsoft Docs"
description: "Como publicar as aplicações de ASP.NET que aceitam afirmações do ADFS para proteger o acesso remoto, os utilizadores no local."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: 91e6211b-fe6a-42c6-bdb3-1fff0312db15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: kgremban
ms.openlocfilehash: 5784222608b01509fc4ff84b1a8792cbcfea89e6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Trabalhar com aplicações com suporte para afirmações no Proxy de aplicações
[Aplicações com suporte para afirmações](https://msdn.microsoft.com/library/windows/desktop/bb736227.aspx) efetuar um redirecionamento para serviço de Token segurança (STS). O STS os pedidos de credenciais do utilizador in exchange for um token e, em seguida, redireciona o utilizador para a aplicação. Existem algumas formas de ativar o Proxy da aplicação trabalhar com estes redirecionamentos. Utilize este artigo para configurar a sua implementação para aplicações com suporte para afirmações. 

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que o STS que a aplicação com suporte para afirmações redireciona para está disponível fora da rede no local. Pode disponibilizar o STS pela sua exposição através de um proxy ou ao permitir ligações fora. 

## <a name="publish-your-application"></a>Publicar a aplicação

1. Publicar a aplicação, de acordo com as instruções descritas em [publicar aplicações com o Proxy de aplicações](application-proxy-publish-azure-portal.md).
2. Navegue para a página da aplicação no portal e selecione **de sessão único-**.
3. Se tiver escolhido **do Azure Active Directory** como seu **método de pré-autenticação**, selecione **do Azure AD-início de sessão único desativada** como o **método de autenticação interno**. Se tiver escolhido **Passthrough** como seu **método de pré-autenticação**, não precisa de alterar nada.

## <a name="configure-adfs"></a>A configuração do ADFS

Pode configurar o AD FS para aplicações com suporte para afirmações de uma das duas formas. A primeira é através da utilização de domínios personalizados. A segundo é com WS-Federation. 

### <a name="option-1-custom-domains"></a>Opção 1: Domínios personalizados

Se todos os URLs internos para as suas aplicações são completamente qualificado (FQDN) de nomes de domínio, em seguida, pode configurar [domínios personalizados](active-directory-application-proxy-custom-domains.md) para as suas aplicações. Utilize os domínios personalizados para criar URLs externos que são os mesmos que os URLs internos. Quando os URLs externos corresponder ao seu URLs internos, em seguida, de redirecionamentos de STS funcionam se os seus utilizadores estão no local ou remoto. 

### <a name="option-2-ws-federation"></a>Opção 2: WS-Federation

1. Abra a gestão do AD FS.
2. Aceda a **confianças da entidade Confiadora**, faça duplo clique na aplicação que está a publicar com o Proxy da aplicação e escolha **propriedades**.  

   ![Confianças da entidade confiadora faça duplo clique no nome da aplicação – captura de ecrã](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  

3. No **pontos finais** separador em **tipo de ponto final**, selecione **WS-Federation**.
4. Em **fidedigna URL**, introduza o URL que introduziu no Proxy da aplicação em **URL externo** e clique em **OK**.  

   ![Adicionar um ponto final - defina o valor do URL fidedigna - captura de ecrã](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Passos seguintes
* [Ativar o início de sessão único em](application-proxy-sso-overview.md) para aplicações que não suporte para afirmações
* [Permitir que as aplicações de cliente nativo interagir com aplicações de proxy](active-directory-application-proxy-native-client.md)


