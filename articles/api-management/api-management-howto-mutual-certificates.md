---
title: "Proteger serviços de back-end utilizando o cliente de autenticação de certificado - API Management do Azure | Microsoft Docs"
description: "Saiba como proteger serviços de back-end utilizando a autenticação de certificado de cliente na API Management do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 43453331-39b2-4672-80b8-0a87e4fde3c6
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 196a91c21afb8c1596c9766f6a2a5d373b828f60
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Como proteger serviços de back-end utilizando o cliente de autenticação de certificado na API Management do Azure
API Management fornece a capacidade para proteger o acesso ao serviço de back-end de uma API utilizando certificados de cliente. Este guia mostra como gerir certificados no portal do publicador API e como configurar uma API para utilizar um certificado para aceder ao seu serviço de back-end.

Para obter informações sobre como gerir certificados utilizando a API de REST de gestão de API, consulte [entidade de certificado de API do REST de gestão do Azure API][Azure API Management REST API Certificate entity].

## <a name="prerequisites"></a>Pré-requisitos
Este guia mostra como configurar a sua instância do serviço de API Management para utilizar a autenticação de certificado de cliente para aceder ao serviço de back-end de uma API. Antes de seguir os passos neste tópico, deve ter o serviço de back-end configurado para autenticação de certificados de cliente ([para configurar a autenticação de certificados de Web sites do Azure, consulte a este artigo][to configure certificate authentication in Azure WebSites refer to this article]), e têm acesso para o certificado e a palavra-passe para o certificado para carregar no portal do publicador da API Management.

## <a name="step1"></a>Carregar um certificado de cliente
Para começar, clique em **Portal do editor** no Portal do Azure para o seu serviço de Gestão de API. Isto leva-o para o portal do publicador da API Management.

![Portal do publicador da API][api-management-management-console]

> Se ainda não criou uma instância de serviço de Gestão de API, consulte [Criar uma instância de serviço de Gestão de API][Create an API Management service instance] no tutorial [Introdução à Gestão de API do Azure][Get started with Azure API Management].
> 
> 

Clique em **segurança** do **API Management** menu à esquerda e clique em **certificados de cliente**.

![Certificados de cliente][api-management-security-client-certificates]

Para carregar um novo certificado, clique em **carregar certificado**.

![Carregar certificado][api-management-upload-certificate]

Navegue para o certificado e, em seguida, introduza a palavra-passe do certificado.

> O certificado tem de constar **. pfx** formato. Certificados autoassinados são permitidos.
> 
> 

![Carregar certificado][api-management-upload-certificate-form]

Clique em **carregar** para carregar o certificado.

> A palavra-passe do certificado é validada neste momento. Se estiver incorreto é apresentada uma mensagem de erro.
> 
> 

![Certificado carregado][api-management-certificate-uploaded]

Quando o certificado é carregado, aparecerá no **certificados de cliente** separador. Certifique-se tiver vários certificados, uma nota do assunto ou os últimos quatro carateres do impressão digital, que são utilizados para selecionar o certificado quando configurar uma API para utilizar certificados, como abordadas a seguir [configurar uma API para utilizar um certificado de cliente para autenticação de gateway] [ Configure an API to use a client certificate for gateway authentication] secção.

> Para desativar a validação da cadeia de certificados quando utilizar, por exemplo, um certificado autoassinado, siga os passos descritos neste FAQ [item](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end).
> 
> 

## <a name="step1a"></a>Eliminar um certificado de cliente
Para eliminar um certificado, clique em **eliminar** junto do certificado pretendido.

![Eliminar o certificado][api-management-certificate-delete]

Clique em **Sim, elimine-o** para confirmar.

![Confirmar eliminação][api-management-confirm-delete]

Se o certificado está a ser utilizado por uma API, um ecrã de aviso é apresentado. Ao eliminar o certificado tem de remover o certificado a partir de qualquer APIs que estão configuradas para utilizá-lo.

![Confirmar eliminação][api-management-confirm-delete-policy]

## <a name="step2"></a>Configurar uma API a utilizar um certificado de cliente para autenticação de gateway
Clique em **APIs** do **API Management** menu à esquerda, clique no nome da API pretendida e clique em de **segurança** separador.

![Segurança de API][api-management-api-security]

Selecione **certificados de cliente** do **com credenciais** na lista pendente.

![Certificados de cliente][api-management-mutual-certificates]

Selecione o certificado pretendido o **certificado de cliente** na lista pendente. Se existirem vários certificados, que pode examinar o assunto ou os últimos quatro carateres do thumbprint conforme indicado na secção anterior para determinar o certificado correto.

![Selecione o certificado][api-management-select-certificate]

Clique em **guardar** para guardar a alteração de configuração para a API.

> Esta alteração tem efeita imediato e chamadas para operações dessa API vai utilizar o certificado para autenticação no servidor back-end.
> 
> 

![Guardar as alterações de API][api-management-save-api]

> Quando é especificado um certificado para autenticação de gateway para o serviço de back-end de uma API, este torna-se parte da política para essa API e pode ser visualizado no editor de políticas.
> 
> 

![Política de certificado][api-management-certificate-policy]

## <a name="self-signed-certificates"></a>Certificados autoassinados

Se estiver a utilizar certificados autoassinados, terá de desativar a validação da cadeia de certificado por ordem para a gestão de API comunicar com o sistema de back-end, caso contrário, irá devolver um código de 500 Erro. Para configurar, pode utilizar o [ `New-AzureRmApiManagementBackend` ](https://docs.microsoft.com/en-us/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (para o novo back-end) ou [ `Set-AzureRmApiManagementBackend` ](https://docs.microsoft.com/en-us/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (para o back-end existente) cmdlets do PowerShell e defina o `-SkipCertificateChainValidation` parâmetro `True`.

```
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="next-steps"></a>Passos seguintes
Para mais informações sobre outras formas de proteger o seu serviço de back-end, tal como HTTP autenticação básica ou partilhado secreta, veja o vídeo seguinte.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Last-mile-Security/player]
> 
> 

[api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
[api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
[api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
[api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
[api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
[api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
[api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
[api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
[api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
[api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
[api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
[api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
[api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png



[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[Azure API Management REST API Certificate entity]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps



