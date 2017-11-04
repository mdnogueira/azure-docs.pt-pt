---
title: "Aceder à API de serviços de suporte de dados do Azure com a autenticação do Azure Active Directory | Microsoft Docs"
description: "Saiba mais sobre conceitos e os passos para utilizar o Azure Active Directory (Azure AD) para autenticar o acesso à API de serviços de suporte de dados do Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: 0e1217afb0a37353793c64ae927b741d9fee4954
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Aceder à API de serviços de suporte de dados do Azure com a autenticação do Azure AD
 
A API de serviços de suporte de dados do Azure é uma API RESTful. Pode utilizá-lo para efetuar operações nos recursos de suporte de dados utilizando uma API REST ou ao utilizar SDKs do cliente disponíveis. Media Services do Azure oferece um SDK do cliente de Media Services para Microsoft .NET. Para ser autorizadas a aceder a recursos de Media Services e a API de serviços de suporte de dados, tem primeiro de ser autenticado. 

Os Media Services suportam [Azure Active Directory (Azure AD)-autenticação com base no](../active-directory/active-directory-whatis.md). O serviço de REST de suporte de dados do Azure requer que o utilizador ou aplicação que faz com que a API REST pedidos de ter o **contribuinte** ou **proprietário** função para aceder aos recursos. Para obter mais informações, consulte [introdução ao controlo de acesso baseado em funções no portal do Azure](../active-directory/role-based-access-control-what-is.md).  

> [!IMPORTANT]
> Atualmente, os Media Services suportam o modelo de autenticação do serviço de controlo de acesso do Azure. No entanto, a autorização de controlo de acesso vai ser preterida no dia 1 de Junho de 2018. Recomendamos a migração para o modelo de autenticação do Azure AD logo que possível.

Este documento fornece uma descrição geral de como pode aceder a API de serviços de suporte de dados através de REST ou de APIs de .NET.

## <a name="access-control"></a>Controlo de acesso

Para o pedido de REST de suporte de dados do Azure com êxito, o utilizador chamado tem de ter uma função de contribuinte ou proprietário para a conta de Media Services está a tentar aceder.  
Apenas um utilizador com a função de proprietário pode dar acesso a recursos (conta) suporte de dados para os novos utilizadores ou aplicações. A função de contribuinte pode aceder apenas o recurso de multimédia.
Pedidos não autorizados falharam com o código de estado de 401. Se vir este código de erro, verifique se o seu utilizador tem a função de contribuinte ou proprietário atribuída para a conta de utilizador dos Media Services. Pode verificar isto no portal do Azure. Procure a conta de media e, em seguida, clique em de **controlo de acesso** separador. 

![Separador de controlo de acesso](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>Tipos de autenticação 
 
Quando utiliza a autenticação do Azure AD com Media Services do Azure, tem duas opções de autenticação:

- **Autenticação de utilizador**. Autentica uma pessoa que está a utilizar a aplicação para interagir com recursos de Media Services. A aplicação interativa deve primeiro solicitar ao utilizador as credenciais do utilizador. Um exemplo é uma aplicação de consola de gestão utilizada por utilizadores autorizados para monitorizar as tarefas de codificação ou transmissão em direto. 
- **Autenticação principal de serviço**. Autenticar-se um serviço. As aplicações que utilizam frequentemente este método de autenticação são aplicações que executem serviços de daemon, serviços de camada média ou tarefas agendadas. Os exemplos são as web apps, aplicações de função, as logic apps, API e micro-serviços.

### <a name="user-authentication"></a>Autenticação de utilizador 

As aplicações que devem utilizar o método de autenticação de utilizador são gestão ou de monitorização de aplicações nativas: as aplicações móveis, as aplicações do Windows e aplicações de consola. Este tipo de solução é útil quando pretender que uma interação humana com o serviço dos seguintes cenários:

- Dashboard de monitorização para os trabalhos de codificação.
- Dashboard de monitorização para os fluxos em direto.
- Aplicação de gestão para os utilizadores de ambiente de trabalho ou de dispositivo móveis administrar recursos numa conta de Media Services.

> [!NOTE]
> Este método de autenticação não deve ser utilizado para aplicações direcionadas para o consumidor. 

Uma aplicação nativa tem de primeiro adquirir um token de acesso do Azure AD e, em seguida, utilizá-lo quando efetuar pedidos de HTTP para a API de REST de serviços de suporte de dados. Adicione o token de acesso para o cabeçalho do pedido. 

O diagrama seguinte mostra um fluxo de autenticação da aplicação interativa típica: 

![Diagrama de aplicações nativas](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

No diagrama anterior, os números representam o fluxo de pedidos por ordem cronológica.

> [!NOTE]
> Quando utiliza o método de autenticação de utilizador, todas as aplicações partilham o mesmo ID de cliente de aplicação nativa (predefinição) e o URI de redirecionamento de aplicação nativa. 

1. Solicita credenciais um utilizador.
2. Pedir um token de acesso do Azure AD com os seguintes parâmetros:  

    * Azure AD ponto final de inquilino.

        As informações de inquilino podem ser obtidas a partir do portal do Azure. Coloque o cursor sobre o nome de utilizador com sessão iniciada na parte superior direito.
    * URI do recurso dos Media Services. 

        Este URI é o mesmo para contas de serviços de suporte de dados que estão no mesmo ambiente do Azure (por exemplo, https://rest.media.azure.net).

    * ID de cliente de serviços de suporte de dados (nativo) de aplicação.
    * URI de redirecionamento da aplicação de Media Services (nativo).
    * URI para serviços de suporte de dados REST do recurso.
        
        O URI representa o ponto final de REST API (por exemplo, https://test03.restv2.westus.media.azure.net/api/).

    Para obter os valores para estes parâmetros, consulte [utilizar o portal do Azure para aceder às definições de autenticação do Azure AD](media-services-portal-get-started-with-aad.md) utilizando a opção de autenticação de utilizador.

3. O token de acesso do Azure AD é enviado para o cliente.
4. O cliente envia um pedido para a API de REST de suporte de dados do Azure com o token de acesso do Azure AD.
5. O cliente anterior obtém os dados dos serviços de suporte de dados.

Para obter informações sobre como utilizar a autenticação do Azure AD para comunicar com pedidos de REST utilizando o SDK do cliente do .NET dos Media Services, consulte [autenticação de utilização do Azure AD para aceder à API de serviços do suporte de dados com o .NET](media-services-dotnet-get-started-with-aad.md). 

Se não estiver a utilizar o SDK do cliente do .NET dos Media Services, tem de criar manualmente um pedido de token de acesso do Azure AD, utilizando os parâmetros descritos no passo 2. Para obter mais informações, consulte [como utilizar o Azure AD Authentication Library para obter o Azure AD token](../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="service-principal-authentication"></a>Autenticação do principal de serviço

As aplicações que utilizam frequentemente este método de autenticação são as aplicações que executam os serviços de camada média e tarefas agendadas: web apps, aplicações de função, as logic apps, APIs e micro-serviços. Este método de autenticação também é adequado para aplicações interativas na qual pode querer utilizar uma conta de serviço para gerir os recursos.

Quando utiliza o método de autenticação principal de serviço para criar cenários de consumidor, autenticação, normalmente, é processada na camada média (através de alguns API) e não diretamente numa aplicação móvel ou ambiente de trabalho. 

Para utilizar este método, crie uma aplicação do Azure AD e o serviço principal no seu próprio inquilino. Depois de criar a aplicação, atribua a aplicação de acesso à função de contribuinte ou proprietário para a conta de Media Services. Para fazer isto no portal do Azure, utilizando a CLI do Azure, ou com um script do PowerShell. Também pode utilizar uma aplicação do Azure AD existente. Pode registar e gerir a sua aplicação Azure AD e o principal de serviço [no portal do Azure](media-services-portal-get-started-with-aad.md). Também pode fazer isto utilizando [Azure CLI 2.0](media-services-use-aad-auth-to-access-ams-api.md) ou [PowerShell](media-services-powershell-create-and-configure-aad-app.md). 

![Aplicações de camada média](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

Depois de criar a aplicação do Azure AD, pode obter valores para as seguintes definições. Necessitar destes valores para a autenticação:

- ID de cliente 
- Segredo do cliente 

Na figura anterior, os números representam o fluxo de pedidos por ordem cronológica:
    
1. Uma aplicação de camada média (web API ou aplicação web) solicita um token de acesso do Azure AD que tenha os seguintes parâmetros:  

    * Azure AD ponto final de inquilino.

        As informações de inquilino podem ser obtidas a partir do portal do Azure. Coloque o cursor sobre o nome de utilizador com sessão iniciada na parte superior direito.
    * URI do recurso dos Media Services. 

        Este URI é o mesmo para contas de serviços de suporte de dados que estão localizadas no mesmo ambiente do Azure (por exemplo, https://rest.media.azure.net).

    * URI para serviços de suporte de dados REST do recurso.

        O URI representa o ponto final de REST API (por exemplo, https://test03.restv2.westus.media.azure.net/api/).

    * Os valores de aplicação do Azure AD: o ID de cliente e o segredo do cliente.
    
    Para obter os valores para estes parâmetros, consulte [utilizar o portal do Azure para aceder às definições de autenticação do Azure AD](media-services-portal-get-started-with-aad.md) utilizando a opção de autenticação principal de serviço.

2. O token de acesso do Azure AD é enviado para a camada média.
4. A camada média envia o pedido para a API de REST de suporte de dados do Azure com o token do Azure AD.
5. A camada média anterior obtém os dados dos serviços de suporte de dados.

Para obter mais informações sobre como utilizar a autenticação do Azure AD para comunicar com pedidos de REST utilizando o SDK do cliente do .NET dos Media Services, consulte [autenticação de utilização do Azure AD para aceder à API de serviços de suporte de dados do Azure com o .NET](media-services-dotnet-get-started-with-aad.md). 

Se não estiver a utilizar o SDK do cliente do .NET dos Media Services, tem de criar manualmente um pedido de token do Azure AD através da utilização de parâmetros descritos no passo 1. Para obter mais informações, consulte [como utilizar o Azure AD Authentication Library para obter o Azure AD token](../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="troubleshooting"></a>Resolução de problemas

Exceção: "o servidor remoto devolveu um erro: não autorizado (401)."

Solução: Para o pedido de REST de serviços de suporte de dados tenha êxito, o utilizador chamado tem de ser uma função de contribuinte ou o proprietário da conta de Media Services está a tentar aceder. Para obter mais informações, consulte o [controlo de acesso](media-services-use-aad-auth-to-access-ams-api.md#access-control) secção.

## <a name="resources"></a>Recursos

Os seguintes artigos-se descrições gerais de conceitos de autenticação do Azure AD: 

- [Cenários de autenticação endereçados através do Azure AD](../active-directory/develop/active-directory-authentication-scenarios.md#basics-of-authentication-in-azure-ad)
- [Adicionar, atualizar ou remover uma aplicação no Azure AD](../active-directory/develop/active-directory-integrating-applications.md)
- [Configurar e gerir o controlo de acesso baseado em funções utilizando o PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)

## <a name="next-steps"></a>Passos seguintes

* Utilizar o portal do Azure para [autenticação de acesso do Azure AD para consumir API de serviços de suporte de dados do Azure](media-services-portal-get-started-with-aad.md).
* Autenticação de utilização do Azure AD para [aceder à API de serviços de suporte de dados do Azure com o .NET](media-services-dotnet-get-started-with-aad.md).

