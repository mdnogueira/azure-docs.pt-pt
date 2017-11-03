---
title: "Como configurar uma aplicação de Proxy de aplicações | Microsoft Docs"
description: "Saiba como criar uma configurar uma aplicação de Proxy de aplicações em alguns passos simples"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: c8f98536048a85ebb3f061d840457130579196d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-an-application-proxy-application"></a>Como configurar uma aplicação de Proxy de aplicações

Este artigo ajuda-o a compreender como configurar uma aplicação de Proxy de aplicações dentro do Azure AD para expor as suas aplicações no local para a nuvem.

## <a name="recommended-documents"></a>Documentos recomendados 

Para saber mais sobre as configurações iniciais e a criação de uma aplicação de Proxy de aplicações através do Portal de administração, siga o [publicar aplicações através do Proxy de aplicações do Azure AD](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal).

Para obter mais informações sobre como configurar conectores, consulte [ativar o Proxy da aplicação no portal do Azure](active-directory-application-proxy-enable.md).

Para informações sobre como carregar certificados e a utilização de domínios personalizados, consulte [trabalhar com domínios personalizados no Proxy de aplicações do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains).

## <a name="create-the-applicationsetting-the-urls"></a>Criar a aplicação/definição os URLs

Se estiver a seguir os passos a [publicar aplicações através do Proxy de aplicações do Azure AD](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) tem e a documentação de obter um erro ao criar a aplicação, consulte os detalhes do erro para obter informações e sugestões para saber como corrigir a aplicação. A maioria das mensagens de erro incluem uma correção sugerida. Para evitar erros comuns, certifique-se:

-   É um administrador com permissão para criar uma aplicação de Proxy de aplicações

-   O URL interno é exclusivo

-   O URL externo é exclusivo

-   Os URLs começar a utilizar http ou https e terminar com um "/"

-   O URL deve ser um nome de domínio, não um endereço IP

A mensagem de erro deverá apresentar no canto superior direito ao criar a aplicação. Também pode selecionar o ícone de notificação para ver as mensagens de erro.

   ![Pedido de notificação](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Configurar grupos de conectores/conector

Se estiver a ter dificuldade em configurar a sua aplicação devido a aviso sobre os conectores e grupos de conector, consulte as instruções sobre como ativar o Proxy da aplicação para obter detalhes sobre como transferir conectores. Se pretender obter mais informações sobre conectores, consulte o [documentação de conectores](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).

Se os conectores estão inativos, isto significa que não conseguem alcançar o serviço. Isto é, muitas vezes, porque todas as portas necessárias não estão abertas. Para ver uma lista das portas necessárias, consulte a secção de pré-requisitos da documentação ativar Proxy de aplicações.

## <a name="upload-certificates-for-custom-domains"></a>Carregar certificados para domínios personalizados

Domínios personalizados permitem-lhe especificar o domínio da sua URLs externos. Para utilizar domínios personalizados, terá de carregar o certificado para esse domínio. Para obter informações sobre como utilizar domínios e certificados personalizados, consulte [trabalhar com domínios personalizados no Proxy de aplicações do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains). 

Se tiver problemas com a carregar o certificado, procure as mensagens de erro no portal para obter informações adicionais sobre o problema com o certificado. Problemas de certificado comuns incluem:

-   Certificado expirado

-   O certificado é autoassinado

-   Certificado está em falta a chave privada

Apresentar a mensagem de erro no canto superior direito, como a tenta carregar o certificado. Também pode selecionar o ícone de notificação para ver as mensagens de erro.

   ![Pedido de notificação](./media/application-proxy-config-how-to/error-message2.png)

## <a name="next-steps"></a>Passos seguintes
[Publicar aplicações através do Proxy de aplicações do Azure AD](application-proxy-publish-azure-portal.md)
