---
title: "Autenticar com APIs REST do Mobile Engagement - configuração manual"
description: "Descreve como configurar manualmente a autenticação para as APIs REST do Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 9d6132e1a01be489b8e8e28a0219cf8a0b50b318
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Autenticar com APIs REST do Mobile Engagement - configuração manual
Esta é uma documentação apêndice para [autenticar com as APIs REST do Mobile Engagement](mobile-engagement-api-authentication.md). Certifique-se de que lê-lo primeiro para obter o contexto. Descreve uma maneira alternativa a única configuração para configurar a autenticação para as APIs REST do Mobile Engagement através do Portal do Azure. 

> [!NOTE]
> As instruções abaixo são baseadas nisto [Guia do Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md) e personalizada para o que é necessário para a autenticação de APIs do Mobile Engagement. Por isso, consulte-la se pretender que a compreender os passos abaixo em detalhe. 
> 
> 

1. Início de sessão à sua conta do Azure através de [portal clássico](https://manage.windowsazure.com/).
2. Selecione **do Active Directory** no painel esquerdo.
   
     ![Selecione o Active Directory][1]
3. Escolha o **predefinido do Active Directory** no portal do Azure. 
   
     ![Selecione o diretório][2]
   
   > [!IMPORTANT]
   > Esta abordagem funciona apenas quando estiver a trabalhar no predefinido do Active Directory da sua conta e não funcionará se estão a fazer isto num Active Directory que criou na sua conta. 
   > 
   > 
4. Para ver as aplicações no seu diretório, clique em **aplicações**.
   
     ![Ver aplicações][3]
5. Clique em **adicionar**. 
   
     ![Adicionar aplicação][4]
6. Clique em **adicionar uma aplicação que a minha organização está a desenvolver**
   
     ![nova aplicação][5]
7. Preencha o nome da aplicação e selecione o tipo de aplicação como **aplicação WEB e/ou API WEB** e clique no botão seguinte.
   
     ![aplicação de nome][6]
8. Pode fornecer os URLs fictícias para **URL de início de sessão no** e **URI de ID de aplicação**. Não são utilizados para o nosso cenário e os URLs próprios não são validados.  
   
     ![Propriedades da aplicação][7]
9. No final desta situação, terá uma aplicação AAD com o nome que forneceu anteriormente semelhante ao seguinte. Este é o **AD\_aplicação\_nome** e tome nota do mesmo.  
   
     ![Nome da aplicação][8]
10. Clique no nome da aplicação e clique em **configurar**.
    
      ![Configure a aplicação][9]
11. Anote o ID de cliente que serão utilizadas como **cliente\_ID** para a API chamadas. 
    
     ![Configure a aplicação][10]
12. Desloque para baixo até o **chaves** secção e adicionar uma chave com duração de 2 anos (expiração), de preferência e clique em **guardar**. 
    
     ![Configure a aplicação][11]
13. Imediatamente, copie o valor que é mostrado para a chave, como apenas é apresentado agora e não são armazenada, pelo que não serão apresentadas alguma vez novamente. Se perder terá de gerar uma nova chave. Este será o **CLIENT_SECRET** para a API chamadas. 
    
     ![Configure a aplicação][12]
    
    > [!IMPORTANT]
    > Esta chave irá expirar no final da duração que especificou, pelo que certifique-se renová-lo quando o tempo vem caso contrário, a autenticação de API deixa de funcionar. Também pode eliminar e recriar esta chave, se pensa que o se tiver sido comprometido.
    > 
    > 
14. Clique em **ver pontos finais** botão agora que abre-se a **pontos finais de aplicação** caixa de diálogo. 
    
    ![][13]
15. Na caixa de diálogo pontos finais de aplicação, copie o **ponto final de TOKENS do OAUTH 2.0**. 
    
    ![][14]
16. Este ponto final será o seguinte formato em que é o GUID no URL do seu **TENANT_ID** , por isso, tome nota do mesmo: 
    
        https://login.microsoftonline.com/<GUID>/oauth2/token
17. Agora iremos irá continuar a configurar as permissões nesta aplicação. Para este terá de abrir o [portal do Azure](https://portal.azure.com). 
18. Clique em **grupos de recursos** e localize o **Mobile Engagement** grupo de recursos.  
    
    ![][15]
19. Clique em de **Mobile Engagement** recursos de grupo e navegue para o **definições** painel aqui. 
    
    ![][16]
20. Clique em **utilizadores** no painel de definições e, em seguida, clique em **adicionar** para adicionar um utilizador. 
    
    ![][17]
21. Clique em **selecionar uma função**
    
    ![][18]
22. Clique em **proprietário**
    
    ![][19]
23. Procure o nome da sua aplicação **AD\_aplicação\_nome** na caixa de pesquisa. Não verá esta por predefinição aqui. Depois de localizar, selecione-o e clique em **selecione** na parte inferior do painel. 
    
    ![][20]
24. No **adicionar acesso** painel,-irá aparecer como **1 utilizador, grupos de 0**. Clique em **OK** neste painel para confirmar a alteração. 
    
    ![][21]

Agora concluiu a configuração necessária do AAD e está todos definidos para chamar as APIs. 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client_secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png



