---
title: "Como configurar a autenticação do Azure Active Directory para a sua aplicação de serviços aplicacionais"
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
ms.openlocfilehash: 25f0578a9e273c30ecc98af5b66c6dd43305aa03
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-your-app-service-application-to-use-azure-active-directory-login"></a>Como configurar a sua aplicação do Serviço de Aplicações para utilizar o início de sessão do Azure Active Directory
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar os serviços de aplicações do Azure para utilizar o Azure Active Directory como um fornecedor de autenticação.

## <a name="express"></a>Configurar o Azure Active Directory utilizando as definições rápidas
1. No [portal do Azure], navegue até à sua aplicação. Clique em **definições**e, em seguida, **autenticação/autorização**.
2. Se a autenticação / autorização funcionalidade não está ativada, ative o comutador **no**.
3. Clique em **do Azure Active Directory**e, em seguida, clique em **Express** em **modo de gestão**.
4. Clique em **OK** para registar a aplicação no Azure Active Directory. Esta ação irá criar um novo registo. Se pretender escolher um registo existente em vez disso, clique em **selecionar uma aplicação existente** e, em seguida, procure o nome de um registo criado anteriormente no seu inquilino.
   Clique no registo para selecioná-lo e clique em **OK**. Em seguida, clique em **OK** no painel de definições do Azure Active Directory.
   Por predefinição, o serviço de aplicações fornece autenticação mas não restringir o acesso autorizado ao conteúdo do site e APIs. Tem de autorizar os utilizadores no seu código de aplicação.
5. (Opcional) Para restringir o acesso ao seu site apenas aos utilizadores autenticados pelo Azure Active Directory, defina **ação a tomar quando o pedido não é autenticado** para **iniciar sessão com o Azure Active Directory**. Isto requer que todos os pedidos de ser autenticados e todos os pedidos não autenticados são redirecionados para o Azure Active Directory para autenticação.
6. Clique em **Guardar**.

Agora está pronto a utilizar o Azure Active Directory para autenticação na sua aplicação.

## <a name="advanced"></a>(Método alternativo) configurar manualmente o Azure Active Directory com definições avançadas
Também pode optar por fornecer as definições de configuração manualmente. Esta é a solução preferida, se pretender utilizar o inquilino do AAD é diferente do inquilino com o qual poderá iniciar sessão no Azure. Para concluir a configuração, terá primeiro de criar um registo no Azure Active Directory e, em seguida, tem de fornecer alguns dos detalhes de registo para o App Service.

### <a name="register"></a>Registar a sua aplicação no Azure Active Directory
1. Inicie sessão no [portal do Azure]e navegue até à sua aplicação. Copie a sua aplicação **URL**. Irá utilizá-lo para configurar a sua aplicação do Azure Active Directory.
2. Navegue para **do Active Directory**, em seguida, selecione o **registos de aplicação**, em seguida, clique em **novo registo de aplicação** na parte superior para iniciar um novo registo de aplicação. 
3. A criar aplicações registo caixa de diálogo, introduza um **nome** para a sua aplicação, selecione o **API de aplicação Web** escreva, pelo **URL de início de sessão** caixa cole o URL da aplicação (a partir de passo 1). Em seguida, clique para **criar**.
4. Em alguns segundos, deve ver o novo registo de aplicação que acabou de criar aparecer.
5. Assim que a aplicação foi adicionada, clique no nome do registo de aplicação, clique em **definições** na parte superior, em seguida, clique em **propriedades** 
6. No **URI de ID de aplicação** caixa, cole o URL da aplicação (a partir do passo 1), também no **Home Page do URL** cole o URL da aplicação (a partir do passo 1), em seguida, clique em **guardar**
7. Agora, clique em de **URLs de resposta**, edite o **URL de resposta**, cole o URL da aplicação (a partir do passo 1), modifique o protocolo para se certificar de que tem **https://** protocolo (http:// não), em seguida, é acrescentado ao fim do URL, */.auth/login/aad/callback* . (Por exemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`.) Clique em **Guardar**.   
8.  Neste momento, copie o **ID da aplicação** para a aplicação. Manter esta opção para utilização posterior. Terá de esta opção para configurar a sua aplicação web.
9. Feche o painel de detalhes de registo de aplicação. Deve ser devolvido para o resumo de registo de aplicações do Azure Active Directory, clique em de **pontos finais** botão na parte superior, em seguida, copie o **documento de metadados de Federação** URL. 
10. Abra uma nova janela do browser e navegue para o URL, colar e navegar para a página XML. Na parte superior do documento será um **EntityDescriptor** elemento, deverá haver um **entityID** atributo do formulário `https://sts.windows.net/` seguido de um GUID específico no seu inquilino (denominado um "ID do inquilino"). Copie este valor - que irá servir como a **URL do emissor**. Irá configurar a sua aplicação para utilizá-lo mais tarde.

### <a name="secrets"></a>Informações adicionar do Azure Active Directory para a aplicação
1. Volta a [portal do Azure], navegue até à sua aplicação. Clique em **autenticação/autorização**. Se não estiver ativada a funcionalidade de autenticação/autorização, ative o comutador para **no**. Clique em **do Azure Active Directory**, em fornecedores de autenticação, para configurar a sua aplicação. (Opcional) Por predefinição, o serviço de aplicações fornece autenticação mas não restringir o acesso autorizado ao conteúdo do site e APIs. Tem de autorizar os utilizadores no seu código de aplicação. Selecione o **ação a tomar quando o pedido não é autenticado**, defina esta opção para **iniciar sessão com o Azure Active Directory**. Isto requer que todos os pedidos de ser autenticados e todos os pedidos não autenticados são redirecionados para o Azure Active Directory para autenticação.
2 na configuração da autenticação do Active Directory, clique em **avançadas** em **modo de gestão**. Cole o ID da aplicação na caixa de ID de cliente (a partir do passo 8) e cole no entityId (a partir do passo 10) o valor do URL do emissor. Em seguida, clique em **OK**.
3. No painel de configuração de autenticação do Active Directory, clique em **guardar**.

Agora está pronto a utilizar o Azure Active Directory para autenticação na sua aplicação.

## <a name="optional-configure-a-native-client-application"></a>(Opcional) Configurar uma aplicação cliente nativa
Azure Active Directory também permite-lhe registar clientes nativos, que proporciona maior controlo sobre as permissões de mapeamento. Precisa disto se de que deseja realizar inícios de sessão utilizando uma biblioteca, tais como o **Active Directory Authentication Library**.

1. Navegue para **do Active Directory** no [portal clássico do Azure].
2. Selecione o diretório e, em seguida, selecione o **aplicações** separador no topo. Clique em **adicionar** na parte inferior para criar um novo registo de aplicação.
3. Clique em **adicionar uma aplicação que a minha organização está a desenvolver**.
4. No Assistente para adicionar aplicação, introduza um **nome** para a sua aplicação e clique em de **aplicação cliente nativa** tipo. Em seguida, clique para continuar.
5. No **URI de redirecionamento** box, introduza o seu site */.auth/login/done* ponto final, utilizando o esquema de HTTPS. Este valor deve ser semelhante *https://contoso.azurewebsites.net/.auth/login/done*. Se criar uma aplicação do Windows, em vez disso, utilize o [SID do pacote](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) como o URI.
6. Depois da aplicação nativa foi adicionada, clique o **configurar** separador. Localizar o **ID de cliente** e tome nota deste valor.
7. Desloque-se a página para baixo para a **permissões para outras aplicações** secção e clique em **Adicionar aplicação**.
8. Procure a aplicação web que registou anteriormente e clique no ícone de adição. Em seguida, clique em verificação para fechar a caixa de diálogo. Se a aplicação web não pode ser encontrada, navegue para o registo e adicione um novo URL de resposta (por exemplo, a versão HTTP do URL atual), clique em Guardar e, em seguida, repita estes passos - a aplicação deve aparecer na lista.
9. Na nova entrada que acabou de adicionar, abra o **permissões delegadas** pendente e selecione **acesso (appName)**. Em seguida, clique em **Guardar**.

Agora que configurou uma aplicação de cliente nativo que pode aceder à aplicação do serviço de aplicações.

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
[portal clássico do Azure]: https://manage.windowsazure.com/
[alternative method]:#advanced
