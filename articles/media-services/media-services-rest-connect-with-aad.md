---
title: "Autenticação de utilização do Azure AD para aceder à API de serviços de suporte de dados do Azure com o resto | Microsoft Docs"
description: "Saiba como aceder à API de serviços de suporte de dados do Azure com a autenticação do Azure Active Directory utilizando o resto."
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: willzhan;juliako
ms.openlocfilehash: 1c62857699fb29b3583363e1c6f2dc7874635f40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-ad-authentication-to-access-the-azure-media-services-api-with-rest"></a>Utilize a autenticação do Azure AD para aceder à API de serviços de suporte de dados do Azure com REST

A equipa de Media Services do Azure lançou suporte de autenticação do Azure Active Directory (Azure AD) para acesso de Media Services do Azure. Anunciou também a planos para preterir a autenticação do serviço de controlo de acesso do Azure para o acesso de Media Services. Dado que cada subscrição do Azure e cada conta de Media Services, está ligado a um inquilino do Azure AD, o suporte de autenticação do Azure AD tem muitas vantagens de segurança. Para obter mais informações sobre esta alteração e a migração (se utilizar o SDK do .NET dos serviços de suporte de dados para a sua aplicação), consulte as seguintes mensagens de blogue e artigos:

- [Media Services do Azure announces suporte para o Azure AD e descontinuação de autenticação de controlo de acesso](https://azure.microsoft.com/blog/azure%20media%20service%20aad%20auth%20and%20acs%20deprecation)
- [API dos serviços de suporte de dados de Azure do acesso através da autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md)
- [Utilize a autenticação do Azure AD para aceder à API de serviços de suporte de dados do Azure com o Microsoft .NET](media-services-dotnet-get-started-with-aad.md)
- [Introdução à autenticação do Azure AD através do portal do Azure](media-services-portal-get-started-with-aad.md)

Alguns clientes precisam desenvolver as soluções de Media Services com as seguintes restrições:

*   Utilizam uma linguagem de programação que não é o Microsoft .NET ou c# ou o ambiente de tempo de execução não é Windows.
*   Azure AD bibliotecas, tais como bibliotecas de autenticação do Active Directory não estão disponíveis para a linguagem de programação ou não podem ser utilizadas para o respetivo ambiente de tempo de execução.

Alguns clientes tem programado aplicações utilizando a REST API para autenticação de controlo de acesso e acesso de Media Services do Azure. Para estes clientes, terá de uma forma de utilizar apenas a API de REST de autenticação do Azure AD e subsequente acesso de Media Services do Azure. É necessário não confie em qualquer uma das bibliotecas do Azure AD ou o SDK de .NET de Media Services. Neste artigo, vamos descrevem uma solução e forneça o código de exemplo para este cenário. Porque o código de todas as chamadas da REST API, com nenhuma dependência em qualquer do Azure AD ou biblioteca de Media Services do Azure, o código facilmente pode ser convertido em qualquer linguagem de programação.

> [!IMPORTANT]
> Atualmente, os Media Services suportam o modelo de autenticação de serviços de controlo de acesso do Azure. No entanto, vai ser preterida a autenticação de controlo de acesso 1 de Junho de 2018. Recomendamos a migração para o modelo de autenticação do Azure AD logo que possível.


## <a name="design"></a>Design

Neste artigo, utilizamos a seguinte estrutura de autenticação e autorização:

*  Protocolo de autorização: OAuth 2.0. OAuth 2.0 é uma norma de segurança de web que abrange a autenticação e autorização. É suportada pela Google, Microsoft, Facebook e PayPal. -Foi ratified Outubro de 2012. Microsoft corretas oferece suporte a OAuth 2.0 e o OpenID Connect. Ambos estes padrões são suportados em vários serviços e bibliotecas de cliente, incluindo o Azure Active Directory, Open Web Interface para Katana .NET (OWIN) e bibliotecas do Azure AD.
*  Tipo de conceder: tipo de conceder de credenciais do cliente. Credenciais do cliente é um dos tipos de concessão de quatro OAuth 2.0. Muitas vezes, é utilizado para acesso ao Microsoft Azure AD Graph API.
*  Modo de autenticação: principal de serviço. O modo de autenticação é o utilizador ou a autenticação interativa.

Um total de quatro aplicações ou serviços estão envolvidos na autenticação do Azure AD e autorização fluxo por utilizar os serviços de suporte de dados. As aplicações e serviços e o fluxo, descritas na tabela seguinte:

|Tipo de aplicação |Aplicação |Fluxo|
|---|---|---|
|Cliente | Aplicação de cliente ou solução | Esta aplicação (na realidade, o proxy) está registada no inquilino do Azure AD no qual residem a subscrição do Azure e a conta de serviço de suporte de dados. O principal de serviço da aplicação registada, em seguida, é concedido a função de proprietário ou contribuinte no controlo de acesso (IAM) da conta de serviço de suporte de dados. O principal de serviço é representado por aplicação segredo do cliente e o ID de cliente. |
|Fornecedor de identidade (IDP) | Azure AD como IDP | A aplicação registada principal de serviço (ID de cliente e segredo do cliente) é autenticado pelo Azure AD como IDP. Esta autenticação é efetuada internamente e implicitamente. Como no fluxo de credenciais do cliente, o cliente é autenticado em vez do utilizador. |
|Proteger o serviço de Token (STS) / servidor OAuth | Azure AD como STS | Após a autenticação do IDP (ou o servidor de OAuth em termos de OAuth 2.0), um token de acesso ou um Token Web JSON (JWT) é emitido pelo Azure AD como servidor de STS/OAuth para acesso ao recurso de camada média: no nosso caso, o ponto final de API de REST dos serviços de suporte de dados. |
|Recurso | API REST dos Serviços de Multimédia | Cada chamada de API de REST dos serviços de suporte de dados está autorizada por um token de acesso que é emitido pelo Azure AD como STS ou o servidor de OAuth. |

Se executar o código de exemplo e capturar um JWT ou um token de acesso, o JWT tem os seguintes atributos:

    aud: "https://rest.media.azure.net",

    iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",

    iat: 1497146280,

    nbf: 1497146280,
    exp: 1497150180,

    aio: "Y2ZgYDjuy7SptPzO/muf+uRu1B+ZDQA=",

    appid: "02ed1e8e-af8b-477e-af3d-7e7219a99ac6",

    appidacr: "1",

    idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",

    oid: "a938cfcc-d3de-479c-b0dd-d4ffe6f50f7c",

    sub: "a938cfcc-d3de-479c-b0dd-d4ffe6f50f7c",

    tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",

Seguem-se os mapeamentos entre os atributos a JWT e a quatro aplicações ou serviços na tabela anterior:

|Tipo de aplicação |Aplicação |Atributo JWT |
|---|---|---|
|Cliente |Aplicação de cliente ou solução |AppID: "02ed1e8e-af8b-477e-af3d-7e7219a99ac6". O ID de cliente de uma aplicação que irá registar com o Azure AD na secção seguinte. |
|Fornecedor de identidade (IDP) | Azure AD como IDP |IDP: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/".  O GUID é o inquilino de ID da Microsoft (microsoft.onmicrosoft.com). Cada inquilino tem um ID próprio, exclusivo. |
|Proteger o serviço de Token (STS) / servidor OAuth |Azure AD como STS | iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/". O GUID é o inquilino de ID da Microsoft (microsoft.onmicrosoft.com). |
|Recurso | API REST dos Serviços de Multimédia |aud: "https://rest.media.azure.net". O destinatário ou o público-alvo do token de acesso. |

## <a name="steps-for-setup"></a>Passos de configuração

Para registar e configurar uma aplicação do Azure AD para a autenticação do Azure AD e obter um token de acesso para chamar o ponto final de API de REST dos serviços de suporte de dados do Azure, execute os seguintes passos:

1.  No [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885), registar uma aplicação do Azure AD (por exemplo, wzmediaservice) para o inquilino do Azure AD (por exemplo, microsoft.onmicrosoft.com). Não interessa se registado como aplicação web ou aplicação nativa. Além disso, pode escolher qualquer URL de início de sessão e o URL de resposta (por exemplo, http://wzmediaservice.com para ambos).
2. No [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885), vá para o **configurar** separador da sua aplicação. Tenha em atenção o **ID de cliente**. Em seguida, em **chaves**, gerar uma **chave cliente** (segredo do cliente). 

    > [!NOTE] 
    > Tome nota do segredo do cliente. Este não será apresentado novamente.
    
3.  No [portal do Azure](http://ms.portal.azure.com), aceda a conta de Media Services. Selecione o **controlo de acesso** painel (IAM). Adicione um novo membro que tem o proprietário ou a função de contribuinte. Para o principal, procure o nome da aplicação que registou no passo 1 (neste exemplo, wzmediaservice).

## <a name="info-to-collect"></a>Informações a recolher

Para preparar a codificação de REST, recolha os seguintes pontos de dados a incluir no código:

*   Azure AD como um ponto final STS: https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token. Deste ponto final, é pedido um token de acesso JWT. Para além das que serve como um IDP, o Azure AD também serve como um STS. O Azure AD emite um JWT para acesso a recursos (um token de acesso). Um token JWT tem várias afirmações.
*   API de REST do suporte de dados dos serviços do Azure como recursos ou o público-alvo: https://rest.media.azure.net.
*   ID de cliente: Ver passo 2 [passos para a configuração](#steps-for-setup).
*   Segredo do cliente: ver passo 2 [passos para a configuração](#steps-for-setup).
*   Os serviços de suporte de dados de conta de ponto final de REST API no seguinte formato:

    https://[media_service_account_name].restv2. [data_center].media.azure.net/API 

    Este é o ponto final com que todas as API de REST de serviços de suporte de dados são efetuadas chamadas na sua aplicação. Por exemplo, https://willzhanmswjapan.restv2.japanwest.media.azure.net/API.

Em seguida, pode colocar estes cinco parâmetros no seu ficheiro Web. config ou o App. config, para utilizar no seu código.

## <a name="sample-code"></a>Código de exemplo

Pode encontrar o código de exemplo no [do Azure AD Authentication para acesso de serviços de suporte de dados do Azure: ambos através da REST API](https://github.com/willzhan/WAMSRESTSoln).

O código de exemplo tem duas partes:

*   Um projeto de biblioteca DLL tem todos os o código de REST API para autorização e autenticação do Azure AD. Também tem um método para efetuar chamadas da REST API para o ponto final de API de REST dos serviços de suporte de dados, com o token de acesso.
*   Um cliente de teste da consola, que inicia a autenticação do Azure AD e chama a API de REST de serviços de suporte de dados diferentes.

O projeto de exemplo tem três funcionalidades:

*   Conceder do Azure AD as autenticações através das credenciais do cliente utilizando apenas a API REST.
*   Acesso de Media Services do Azure utilizando apenas a API REST.
*   Acesso de armazenamento do Azure, utilizando apenas a API REST (como utilizado para criar uma conta de Media Services, utilizando a REST API).


## <a name="where-is-the-refresh-token"></a>Onde está o token de atualização?

Poderão pedir-lhe algumas leitores: onde está o token de atualização? Por que motivo não utilizar um token de atualização aqui?

O objetivo de um token de atualização não é um token de acesso de atualização. Em vez disso, foi concebido para ignorar a intervenção do utilizador ou de autenticação de utilizador final e receber um token de acesso válido quando expira um token anteriormente. Um nome melhor para um token de atualização poderá ser algo como "Ignorar re-sessão-no token de utilizador do".

Se utilizar o fluxo (nome de utilizador e palavra-passe, agir em nome de um utilizador) de concessão de autorização do OAuth 2.0, um token de atualização ajuda a obter um acesso renovado token sem pedir intervenção do utilizador. No entanto, para o OAuth 2.0 fluxo dita neste artigo de concessão de credenciais do cliente, o cliente funciona no seu próprio nome. Não precisa de intervenção do utilizador de todo, e o servidor de autorização não precisa (e não serão) dão-lhe um token de atualização. Se a depuração de **GetUrlEncodedJWT** método, tenha em atenção que a resposta do ponto final do token tem um token de acesso, mas nenhum token de atualização.

## <a name="next-steps"></a>Passos seguintes

Introdução ao [carregar ficheiros para a conta](media-services-dotnet-upload-files.md).
