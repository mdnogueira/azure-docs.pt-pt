---
title: Autorizar a contas de programador com o Azure Active Directory - API Management do Azure | Microsoft Docs
description: Saiba como autorizar os utilizadores com o Azure Active Directory na API Management.
services: api-management
documentationcenter: API Management
author: vladvino
manager: erikre
editor: 
ms.assetid: 33a69a83-94f2-4e4e-9cef-f2a5af3c9732
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 2e8bc33ddf38657fe2d0d84a7bf64d4177d51572
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
> [!WARNING]
> Integração do Active Directory do Azure está disponível no [programador e Premium](https://azure.microsoft.com/en-us/pricing/details/api-management/) camadas apenas.

# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>Como autorizar contas de programador com o Azure Active Directory na API Management do Azure
## <a name="overview"></a>Descrição geral
Este guia mostra como ativar o acesso ao portal do programador para os utilizadores do Azure Active Directory. Este guia mostra também como gerir grupos de utilizadores do Active Directory do Azure através da adição de grupos externos que contenham os utilizadores de um Azure Active Directory.

> Para concluir os passos neste guia primeiro tem de ter um Azure Active Directory no qual pretende criar uma aplicação.
> 

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>Como autorizar contas de programador com o Azure Active Directory
Para começar, clique em **portal do publicador** no portal do Azure para o seu serviço de API Management. Isto leva-o para o portal do publicador da API Management.

![Portal do publicador][api-management-management-console]

> Se ainda não criou uma instância de serviço de Gestão de API, consulte [Criar uma instância de serviço de Gestão de API][Create an API Management service instance] no tutorial [Introdução à Gestão de API do Azure][Get started with Azure API Management].
> 
> 

Clique em **segurança** do **API Management** menu à esquerda e clique em **identidades externo**.

![Identidades externas][api-management-security-external-identities]

Clique em **do Azure Active Directory**. Anote o **URL de redirecionamento** e mude ao longo para o Azure Active Directory no Portal clássico do Azure.

![Identidades externas][api-management-security-aad-new]

Clique em de **adicionar** botão para criar uma nova aplicação do Azure Active Directory e escolha **adicionar uma aplicação que a minha organização está a desenvolver**.

![Adicionar nova aplicação do Azure Active Directory][api-management-new-aad-application-menu]

Introduza um nome para a aplicação, selecione **Web de aplicação e/ou Web API**e clique no botão seguinte.

![Nova aplicação do Azure Active Directory][api-management-new-aad-application-1]

Para **URL de início de sessão**, introduza o URL de início de sessão do portal do programador. Neste exemplo, o **URL de início de sessão** é `https://aad03.portal.current.int-azure-api.net/signin`. 

Para o **URL de ID de aplicação**, introduza o domínio predefinido ou um domínio personalizado para o Azure Active Directory e acrescentar uma cadeia exclusiva. Neste exemplo, o domínio predefinido do **https://contoso5api.onmicrosoft.com** é utilizado com o sufixo de **/api** especificado.

![Novas propriedades de aplicações do Azure Active Directory][api-management-new-aad-application-2]

Clique no botão de verificação para guardar e criar a aplicação e mude para o **configurar** separador para configurar a nova aplicação.

![Nova aplicação do Azure Active Directory criada][api-management-new-aad-app-created]

Se vários diretórios de Active Directory do Azure vai ser utilizado para esta aplicação, clique em **Sim** para **aplicação é a multi-inquilino**. A predefinição é **não**.

![A aplicação está a multi-inquilino][api-management-aad-app-multi-tenant]

Cópia o **URL de redirecionamento** do **do Azure Active Directory** secção o **identidades externo** separador no portal do publicador e cole-o para o **URL de resposta** caixa de texto. 

![URL de resposta][api-management-aad-reply-url]

Desloque-se para a parte inferior do separador de configuração, selecione o **permissões de aplicação** pendente e verifique **ler os dados de diretório**.

![Permissões de aplicação][api-management-aad-app-permissions]

Selecione o **delegar permissões** pendente e verifique **ativar início de sessão e ler os perfis dos utilizadores**.

![Permissões delegadas][api-management-aad-delegated-permissions]

> Para obter mais informações sobre a aplicação e as permissões delegadas, consulte [aceder a Graph API][Accessing the Graph API].
> 
> 

Copiar o **Id de cliente** para a área de transferência.

![Id de cliente][api-management-aad-app-client-id]

Regresse ao portal do publicador e cole o **Id de cliente** copiados da configuração de aplicação do Azure Active Directory.

![Id de cliente][api-management-client-id]

Mude para a configuração do Azure Active Directory e clique em de **selecione duração** pendente no **chaves** secção e especificar um intervalo. Neste exemplo, **1 ano** é utilizado.

![Chave][api-management-aad-key-before-save]

Clique em **guardar** para guardar a configuração e apresentar a chave. Copie a chave para a área de transferência.

> Tome nota desta chave. Depois de fechar a janela de configuração do Azure Active Directory, a chave não pode ser apresentada.
> 
> 

![Chave][api-management-aad-key-after-save]

Regresse ao portal do publicador e cole a chave para o **segredo do cliente** caixa de texto.

![Segredo do cliente][api-management-client-secret]

**Permitido inquilinos** Especifica os diretórios têm acesso a APIs da instância do serviço de API Management. Especifique os domínios das instâncias do Azure Active Directory ao qual pretende conceder acesso. Pode separar vários domínios com newlines, espaços ou vírgulas.

![Inquilinos permitidos][api-management-client-allowed-tenants]


Depois da configuração pretendida for especificada, clique em **guardar**.

![Guardar][api-management-client-allowed-tenants-save]

Depois das alterações são guardadas, os utilizadores especificados do Azure Active Directory podem iniciar sessão no portal do programador, seguindo os passos no [inicie sessão no portal do programador, utilizando uma conta do Azure Active Directory][Log in to the Developer portal using an Azure Active Directory account].

Podem ser especificados vários domínios a **permitido inquilinos** secção. Antes de qualquer utilizador pode iniciar sessão a partir de um domínio diferente do domínio original em que a aplicação foi registada, um administrador global do outro domínio tem de conceder permissão para a aplicação para aceder a dados de diretório. Para conceder permissão, o administrador global deve passar para `https://<URL of your developer portal>/aadadminconsent` (por exemplo, https://contoso.portal.azure-api.net/aadadminconsent), introduza o nome de domínio de inquilino do Active Directory que pretendem conceder acesso e clique em submeter. No exemplo seguinte, um administrador global de `miaoaad.onmicrosoft.com` está a tentar dar permissão para este portal para programadores específico. 

![Permissões][api-management-aad-consent]

No ecrã seguinte, será solicitado para confirmar a conceder a permissão de administrador global. 

![Permissões][api-management-permissions-form]

> Se tentar um não-administrador global iniciar sessão antes de são concedidas permissões por um administrador global, a tentativa de início de sessão falha e é apresentado um ecrã de erro.
> 
> 

## <a name="how-to-add-an-external-azure-active-directory-group"></a>Como adicionar um grupo externo para o Azure Active Directory
Depois de ativar o acesso aos utilizadores do Azure Active Directory, pode adicionar grupos do Azure Active Directory para gestão de API para gerir mais facilmente a associação de programadores do grupo de produtos pretendidos.

> Para configurar um grupo externo do Azure Active Directory, o Azure Active Directory tem primeiro de ser configurado no separador de identidades, seguindo o procedimento na secção anterior. 
> 
> 

Grupos externos do Azure Active Directory são adicionados a partir de **visibilidade** separador do produto para o qual pretende conceder acesso ao grupo. Clique em **produtos**e, em seguida, clique no nome do produto pretendido.

![Configurar o produto][api-management-configure-product]

Mudar para o **visibilidade** separador e clique em **adicionar grupos do Azure Active Directory**.

![Adicionar grupos][api-management-add-groups]

Selecione o **inquilino do Azure Active Directory** da lista pendente lista e, em seguida, escreva o nome do grupo pretendido no **grupos** ser adicionado a caixa de texto.

![Selecione o grupo][api-management-select-group]

Este nome de grupo pode ser encontrado no **grupos** lista para o Azure Active Directory, conforme mostrado no exemplo seguinte.

![Lista de grupos do Azure Active Directory][api-management-aad-groups-list]

Clique em **adicionar** para validar o nome do grupo e adicionar o grupo. Neste exemplo, o **Contoso 5 programadores** grupo externo é adicionado. 

![Grupo adicionado][api-management-aad-group-added]

Clique em **guardar** para guardar a nova seleção de grupo.

Depois de um grupo do Azure Active Directory tiver sido configurado a partir de um produto, fica disponível para ser marcada no **visibilidade** separador para outros produtos na instância do serviço de API Management.

Para rever e configurar as propriedades de grupos externos assim que tiverem sido adicionados, clique no nome do grupo do **grupos** separador.

![Gerir grupos][api-management-groups]

Aqui pode editar o **nome** e **Descrição** do grupo.

![Editar grupo][api-management-edit-group]

Os utilizadores do configurado do Azure Active Directory podem iniciar sessão para o portal do programador e ver e subscrever a quaisquer grupos para o qual têm visibilidade ao seguir as instruções na secção seguinte.

## <a name="how-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a>Como iniciar sessão portal do programador com uma conta do Azure Active Directory
Para iniciar sessão no portal do programador, utilizando uma conta do Azure Active Directory configurada nas secções anteriores, abra uma nova utilizando de janela de browser de **URL de início de sessão** na configuração da aplicação do Active Directory e clique em **do Azure Active Directory**.

![Portal do Programador][api-management-dev-portal-signin]

Introduza as credenciais de um dos utilizadores no Azure Active Directory e clique em **sessão**.

![Iniciar sessão][api-management-aad-signin]

Poderá ser pedido com um formulário de registo se são necessárias quaisquer informações adicionais. Preencha o formulário de registo e clique em **inscrever**.

![Registo][api-management-complete-registration]

Agora, o utilizador é registado no portal do programador para a instância de serviço de API Management.

![Registo completo][api-management-registration-complete]

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-security-external-identities.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-aad-consent]: ./media/api-management-howto-aad/api-management-aad-consent.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account

