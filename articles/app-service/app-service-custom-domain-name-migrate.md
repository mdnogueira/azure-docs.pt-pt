---
title: Migrar um nome DNS do Active Directory para o App Service do Azure | Microsoft Docs
description: "Saiba como migrar um nome de domínio DNS personalizado já está atribuído a um site em direto para o App Service do Azure sem qualquer período de inatividade."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: cephalin
ms.openlocfilehash: a1fe545e4a341709232cba36c6e3cf3b4ce82e80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Migrar um nome DNS do Active Directory para o App Service do Azure

Este artigo mostra como migrar um nome DNS do Active Directory para [App Service do Azure](../app-service/app-service-web-overview.md) sem qualquer período de inatividade.

Quando migra um site em direto e o respetivo nome de domínio DNS para o serviço de aplicações, esse nome DNS já está a servir a tráfego em direto. Pode evitar períodos de indisponibilidade na resolução de DNS durante a migração por preventivamente de enlace nome DNS do Active Directory para a sua aplicação de serviço de aplicações.

Se não estiver preocupados sobre o período de indisponibilidade resolução de DNS, consulte [mapear um nome DNS personalizado existente para Web Apps do Azure](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este procedimentos:

- [Certifique-se de que a aplicação de serviço de aplicações não está no escalão gratuito](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Vincular o nome de domínio preventivamente

Quando vincular preventivamente um domínio personalizado, realizar ambos os procedimentos seguintes antes de efetuar quaisquer alterações para os registos DNS:

- Verificar a propriedade de domínio
- Ativar o nome de domínio para a sua aplicação

Quando migra, finalmente, o nome DNS personalizado do site antigo para a aplicação do app Service, não será sem períodos de indisponibilidade na resolução de DNS.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Criar registo de verificação de domínio

Para verificar a propriedade de domínio, adicione um registo TXT. O registo TXT mapas de _awverify.&lt; subdomínio >_ para  _&lt;appname >. azurewebsites.net_. 

O registo TXT que terá depende o registo DNS que pretende migrar. Para obter exemplos, consulte a tabela seguinte (`@` normalmente representa o domínio de raiz):  

| Exemplo de registo DNS | Anfitrião TXT | Valor TXT |
| - | - | - |
| @ (root) | _awverify_ | _&lt;appname >. azurewebsites.net_ |
| www (sub) | _awverify.www_ | _&lt;appname >. azurewebsites.net_ |
| \*(com carateres universais) | _awverify.\*_ | _&lt;appname >. azurewebsites.net_ |

Na sua página de registos DNS, tenha em atenção o tipo de registo de DNS que pretende migrar. Serviço de aplicações suporta mapeamentos de CNAME e registos.

### <a name="enable-the-domain-for-your-app"></a>Ativar o domínio para a sua aplicação

No [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo da página da aplicação, selecione **domínios personalizados**. 

![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

No **domínios personalizados** página, selecione o  **+**  ícone junto a **adicionar hostname**.

![Adicione o nome de anfitrião](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Escreva o nome de domínio completamente qualificado que adicionou o registo TXT, tais como `www.contoso.com`. Para um domínio de caráter universal (como \*. contoso.com), pode utilizar qualquer nome DNS que corresponde ao domínio de caráter universal. 

Selecione **validar**.

O **adicionar hostname** botão está ativado. 

Certifique-se de que **tipo de registo de nome de anfitrião** está definido para o tipo de registo DNS que pretende migrar.

Selecione **adicionar hostname**.

![Adicione o nome DNS para a aplicação](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Poderá demorar algum tempo para o novo nome de anfitrião para serem refletidas na aplicação do **domínios personalizados** página. Tente atualizar o browser para atualizar os dados.

![Registo CNAME adicionado](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

O nome DNS personalizado está ativado na sua aplicação do Azure. 

## <a name="remap-the-active-dns-name"></a>Remapear nome DNS do Active Directory

À esquerda coisa apenas para o fazer é remapeamento o registo de DNS Active Directory para apontar para o serviço de aplicações. Direito agora, ainda aponta para o seu site antigo.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Copie o endereço IP da aplicação (apenas para um registo)

Se o remapeamento de um registo CNAME, ignore esta secção. 

Para remapear um registo, terá de endereço IP externo da aplicação do serviço de aplicações, que é apresentado no **domínios personalizados** página.

Fechar o **adicionar hostname** página selecionando **X** no canto superior direito. 

No **domínios personalizados** página, copie o endereço IP da aplicação.

![Navegação do portal para aplicações do Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Atualize o registo DNS

Na página de registos de DNS do seu fornecedor de domínio, selecione o registo DNS para remapear.

Para o `contoso.com` exemplo de domínio de raiz, Remapeie o registo A ou CNAME, como os exemplos a tabela seguinte: 

| Exemplo FQDN | Tipo de registo | Anfitrião | Valor |
| - | - | - | - |
| contoso.com (root) | A | `@` | Endereço IP de [copie o endereço IP da aplicação](#info) |
| www.contoso.com (sub) | CNAME | `www` | _&lt;appname >. azurewebsites.net_ |
| \*. contoso.com (carateres universais) | CNAME | _\*_ | _&lt;appname >. azurewebsites.net_ |

Guarde as suas definições.

Consultas DNS devem começar a resolver à sua aplicação de serviço de aplicações, imediatamente após a propagação de DNS acontece.

## <a name="next-steps"></a>Passos seguintes

Saiba como vincular um certificado SSL personalizado no App Service.

> [!div class="nextstepaction"]
> [Vincular um certificado SSL personalizado existente para Web Apps do Azure](app-service-web-tutorial-custom-ssl.md)
