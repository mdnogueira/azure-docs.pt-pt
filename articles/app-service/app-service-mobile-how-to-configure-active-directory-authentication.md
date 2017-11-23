---
title: "Configurar a autenticação do Azure Active Directory para a sua aplicação de serviços aplicacionais"
description: "Saiba como configurar a autenticação do Azure Active Directory para a sua aplicação de serviços de aplicações."
author: mattchenderson
services: app-service
documentationcenter: 
manager: syntaxc4
editor: 
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 990fab9aeea71b8cf344b9a49a5ed438db6663c0
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-login"></a>Configure a sua aplicação de serviço de aplicações a utilizar o início de sessão do Azure Active Directory
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este artigo mostra como configurar os serviços de aplicações do Azure para utilizar o Azure Active Directory como um fornecedor de autenticação.

## <a name="express"></a>Configurar o Azure Active Directory utilizando as definições rápidas
1. No [portal do Azure], navegue para a sua aplicação de serviço de aplicações. No painel de navegação esquerdo, selecione **autenticação / autorização**.
2. Se **autenticação / autorização** não estiver ativada, selecione **no**.
3. Selecione **do Azure Active Directory**e, em seguida, selecione **Express** em **modo de gestão**.
4. Selecione **OK** para registar a aplicação de serviço de aplicações no Azure Active Directory. Esta ação cria um novo registo de aplicação. Se pretender escolher um registo de aplicação existentes em vez disso, clique em **selecionar uma aplicação existente** e, em seguida, procure o nome de um registo de aplicação criado anteriormente no seu inquilino.
   Clique no registo de aplicação para selecioná-lo e clique em **OK**. Em seguida, clique em **OK** na página de definições do Azure Active Directory.
   Por predefinição, o serviço de aplicações fornece autenticação mas não restringir o acesso autorizado ao conteúdo do site e APIs. Tem de autorizar os utilizadores no seu código de aplicação.
5. (Opcional) Para restringir o acesso ao seu site apenas aos utilizadores autenticados pelo Azure Active Directory, defina **ação a tomar quando o pedido não é autenticado** para **iniciar sessão com o Azure Active Directory**. Isto requer que todos os pedidos de ser autenticados e todos os pedidos não autenticados são redirecionados para o Azure Active Directory para autenticação.
6. Clique em **Guardar**.

Agora está pronto a utilizar o Azure Active Directory para autenticação na sua aplicação de serviço de aplicações.

## <a name="advanced"></a>(Método alternativo) configurar manualmente o Azure Active Directory com definições avançadas
Também pode optar por fornecer as definições de configuração manualmente. Esta é a solução preferida, se pretender utilizar o inquilino do AAD é diferente do inquilino com o qual poderá iniciar sessão no Azure. Para concluir a configuração, terá primeiro de criar um registo no Azure Active Directory e, em seguida, tem de fornecer alguns dos detalhes de registo para o App Service.

### <a name="register"></a>Registar a aplicação de serviço de aplicações com o Azure Active Directory
1. Inicie sessão no [portal do Azure]e navegue para a sua aplicação de serviço de aplicações. Copie a sua aplicação **URL**. Irá utilizá-lo para configurar o registo de aplicações do Azure Active Directory.
2. Navegue para **do Active Directory**, em seguida, selecione o **registos de aplicação**, em seguida, clique em **novo registo de aplicação** na parte superior para iniciar um novo registo de aplicação. 
3. No **criar** página, introduza um **nome** para registo da aplicação, selecione o **aplicação Web / API** escreva, pelo **URL de início de sessão** caixa colar o URL da aplicação (a partir do passo 1). Em seguida, clique para **criar**.
4. Em alguns segundos, deverá ver o novo registo de aplicação que acabou de criar.
5. Assim que o registo de aplicação foi adicionado, clique no nome do registo de aplicação, clique em **definições** na parte superior, em seguida, clique em **propriedades** 
6. No **URI de ID de aplicação** caixa, cole o URL da aplicação (a partir do passo 1), também no **Home Page do URL** cole o URL da aplicação (a partir do passo 1), em seguida, clique em **guardar**
7. Agora, clique em de **URLs de resposta**, edite o **URL de resposta**, cole o URL da aplicação (a partir do passo 1), modifique o protocolo para se certificar de que tem **https://** protocolo (http:// não), em seguida, é acrescentado ao fim do URL, */.auth/login/aad/callback* (por exemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`). Clique em **Guardar**.   
8.  Neste momento, copie o **ID da aplicação** para a aplicação. Mantenha-a para utilização posterior. Que será necessária para configurar a sua aplicação de serviço de aplicações.
9. Fechar o **aplicação registada** página. No **registos de aplicação** página, clique em de **pontos finais** botão na parte superior, em seguida, copie o **documento de metadados de Federação** URL. 
10. Abra uma nova janela do browser e navegue para o URL, colar e navegar para a página XML. Na parte superior do documento é um **EntityDescriptor** elemento, deverá haver um **entityID** atributo do formulário `https://sts.windows.net/` seguido de um GUID específico no seu inquilino (denominado um "ID do inquilino"). Copie este valor - serve como a **URL do emissor**. Irá configurar a sua aplicação para utilizá-lo mais tarde.

### <a name="secrets"></a>Informações adicionar do Azure Active Directory para a sua aplicação de serviço de aplicações
1. Volta a [portal do Azure], navegue para a sua aplicação de serviço de aplicações. Clique em **autenticação/autorização**. Se não estiver ativada a funcionalidade de autenticação/autorização, ative o comutador para **no**. Clique em **do Azure Active Directory**, em fornecedores de autenticação, para configurar a sua aplicação. (Opcional) Por predefinição, o serviço de aplicações fornece autenticação mas não restringir o acesso autorizado ao conteúdo do site e APIs. Tem de autorizar os utilizadores no seu código de aplicação. Definir **ação a tomar quando o pedido não é autenticado** para **iniciar sessão com o Azure Active Directory**. Esta opção requer que todos os pedidos de ser autenticados e todos os pedidos não autenticados são redirecionados para o Azure Active Directory para autenticação.
2 na configuração da autenticação do Active Directory, clique em **avançadas** em **modo de gestão**. Cole o ID da aplicação na caixa de ID de cliente (a partir do passo 8) e cole no entityId (a partir do passo 10) o valor do URL do emissor. Em seguida, clique em **OK**.
3. Na página de configuração da autenticação do Active Directory, clique em **guardar**.

Agora está pronto a utilizar o Azure Active Directory para autenticação na sua aplicação de serviço de aplicações.

## <a name="optional-configure-a-native-client-application"></a>(Opcional) Configurar uma aplicação cliente nativa
Azure Active Directory também permite-lhe registar clientes nativos, que proporciona maior controlo sobre as permissões de mapeamento. Precisa disto se de que deseja realizar inícios de sessão utilizando uma biblioteca, tais como o **Active Directory Authentication Library**.

1. Navegue para **do Azure Active Directory** no [portal do Azure].
2. No painel de navegação esquerdo, selecione **registos de aplicação**. Clique em **novo registo de aplicação** na parte superior.
4. No **criar** página, introduza um **nome** para registo da aplicação. Selecione **nativo** no **tipo de aplicação**.
5. No **URI de redirecionamento** box, introduza o seu site */.auth/login/done* ponto final, utilizando o esquema de HTTPS. Este valor deve ser semelhante *https://contoso.azurewebsites.net/.auth/login/done*. Se criar uma aplicação do Windows, em vez disso, utilize o [SID do pacote](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) como o URI.
5. Clique em **Criar**.
6. Depois do registo de aplicação foi adicionado, selecione-o para abri-lo. Localizar o **ID da aplicação** e tome nota deste valor.
7. Clique em **todas as definições** > **as permissões necessárias** > **adicionar** > **selecionar um API**.
8. Escreva o nome da aplicação de serviço de aplicações que registou anteriormente para procurá-lo, em seguida, selecione-o e clique em **selecione**. 
9. Selecione **acesso \<APP_NAME>.azurewebsites.NET >**. Em seguida, clique em **selecione**. Em seguida, clique em **Guardar**.

Agora que configurou uma aplicação cliente nativa que pode aceder à sua aplicação de serviço de aplicações.

## <a name="related-content"></a>Relacionados com o conteúdo
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-url.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app_registration.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-create.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-appidurl.png
[4]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-replyurl.png
[5]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints.png
[6]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints-fedmetadataxml.png
[7]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth.png
[8]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth-config.png



<!-- URLs. -->

[portal do Azure]: https://portal.azure.com/
[alternative method]:#advanced
