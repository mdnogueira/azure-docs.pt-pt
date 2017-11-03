---
title: "Proteger um back-end de Web API com o Azure Active Directory e a gestão de API | Microsoft Docs"
description: Saiba como proteger um back-end de Web API com o Azure Active Directory e a API Management.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: f856ff03-64a1-4548-9ec4-c0ec4cc1600f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 3dd583c47fd2d9133c8a07e7bedcd49750ffdce4
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="how-to-protect-a-web-api-backend-with-azure-active-directory-and-api-management"></a>Como pretende proteger um back-end de Web API com o Azure Active Directory e a API Management
O vídeo seguinte mostra como criar um back-end de Web API e protegê-lo através do protocolo de OAuth 2.0 com o Azure Active Directory e a API Management.  Este artigo fornece uma descrição geral e informações adicionais para obter os passos as vídeo. Este vídeo de minuto 24 mostra-lhe como para:

* Criar um back-end de Web API e proteja-a com o AAD - começando em 1:30
* Importar a API na API Management - começando 7:10
* Configurar o portal de programador para chamar a API - começando 9:09
* Configurar uma aplicação de ambiente de trabalho para chamar a API - começando 18:08
* Configurar a política de validação de JWT para a pré-autorizar pedidos - começando 20:47

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Protecting-Web-API-Backend-with-Azure-Active-Directory-and-API-Management/player]
> 
> 

## <a name="create-an-azure-ad-directory"></a>Criar um diretório do Azure AD
Para proteger a API Web de segurança utilizando o Azure Active Directory, primeiro tem de ter um um inquilino do AAD. Neste vídeo um inquilino com o nome **APIMDemo** é utilizado. Para criar um inquilino do AAD, início de sessão para o [Portal clássico do Azure](https://manage.windowsazure.com) e clique em **novo**->**serviços aplicacionais**->**do Active Directory**  -> **Diretório**->**criação personalizada**. 

![Azure Active Directory][api-management-create-aad-menu]

Neste exemplo, um diretório com o nome **APIMDemo** é criada com um domínio predefinido denominado **DemoAPIM.onmicrosoft.com**. Este diretório é utilizado em todo o vídeo.

![Azure Active Directory][api-management-create-aad]

## <a name="create-a-web-api-service-secured-by-azure-active-directory"></a>Criar um serviço de Web API protegido pelo Azure Active Directory
Neste passo, um back-end de Web API é criado utilizando o Visual Studio 2013. Neste passo do vídeo comece em 1:30. Para criar o projeto de back-end de Web API no, clique em Visual Studio **ficheiro**->**novo**->**projeto**e escolha **ASP.NET Web Aplicação** do **Web** lista de modelos. O projeto com o nome neste vídeo **APIMAADDemo**. Clique em **OK** para criar o projeto. 

![Visual Studio][api-management-new-web-app]

Clique em **Web API** do **seleccionar uma lista de modelo** para criar um projeto Web API. Para configurar clique de autenticação do Azure **alterar autenticação**.

![Novo projeto][api-management-new-project]

Clique em **contas institucionais**e especifique o **domínio** do seu inquilino do AAD. Neste exemplo é o domínio **DemoAPIM.onmicrosoft.com**. O domínio do seu diretório pode ser obtido a partir de **domínios** separador do seu diretório.

![Domínios][api-management-aad-domains]

Configure as definições pretendidas no **alterar autenticação** caixa de diálogo e clique em **OK**.

![Alterar autenticação][api-management-change-authentication]

Ao clicar em **OK** Visual Studio irá tentar registar a sua aplicação com o seu diretório do Azure AD e poderá ser-lhe pedido para iniciar sessão pelo Visual Studio. Inicie sessão com uma conta de administrador para o seu diretório.

![Inicie sessão no Visual Studio][api-management-sign-in-vidual-studio]

Para configurar este projeto como uma API Web do Azure selecione a caixa para **anfitrião na nuvem** e, em seguida, clique em **OK**.

![Novo projeto][api-management-new-project-cloud]

Poderá ser-lhe pedido para iniciar sessão no Azure e, em seguida, pode configurar a aplicação Web.

![Configurar][api-management-configure-web-app]

Neste exemplo, um novo **plano do App Service** denominado **APIMAADDemo** está especificado.

Clique em **OK** para configurar a aplicação Web e criar o projeto.

## <a name="add-the-code-to-the-web-api-project"></a>Adicione o código para o projeto Web API
O passo seguinte no vídeo adiciona o código para o projeto Web API. Este passo é iniciado em 4:35.

A API Web neste exemplo implementa um serviço de calculadora básica utilizando um modelo e um controlador. Para adicionar o modelo para o serviço, faça duplo clique **modelos** no **Explorador de soluções** e escolha **adicionar**, **classe**. Designar a classe `CalcInput` e clique em **adicionar**.

Adicione o seguinte `using` declaração na parte superior do `CalcInput.cs` ficheiro.

```c#
using Newtonsoft.Json;
```

Substitua a classe gerada pelo código seguinte.

```c#
public class CalcInput
{
    [JsonProperty(PropertyName = "a")]
    public int a;

    [JsonProperty(PropertyName = "b")]
    public int b;
}
```

Clique com botão direito **controladores** no **Explorador de soluções** e escolha **adicionar**->**controlador**. Escolha **controlador no Web API 2 - vazio** e clique em **adicionar**. Tipo **CalcController** para o controlador de nome e clique em **adicionar**.

![Adicionar controlador][api-management-add-controller]

Adicione o seguinte `using` declaração na parte superior do `CalcController.cs` ficheiro.

```c#
using System.IO;
using System.Web;
using APIMAADDemo.Models;
```

Substitua a classe de controlador gerado pelo código seguinte. Este código implementa o `Add`, `Subtract`, `Multiply`, e `Divide` operações da API de calculadora básica.

```c#
[Authorize]
public class CalcController : ApiController
{
    [Route("api/add")]
    [HttpGet]
    public HttpResponseMessage GetSum([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a + b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/sub")]
    [HttpGet]
    public HttpResponseMessage GetDiff([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a - b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/mul")]
    [HttpGet]
    public HttpResponseMessage GetProduct([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a * b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/div")]
    [HttpGet]
    public HttpResponseMessage GetDiv([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a / b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }
}
```

Prima **F6** para compilar e certifique-se a solução.

## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure
Neste passo do Visual Studio o projeto é publicado para o Azure. Neste passo do vídeo inicia-se em 5:45.

Para publicar o projeto no Azure, clique com botão direito do **APIMAADDemo** projeto no Visual Studio e escolha **publicar**. Manter as predefinições no **publicar Web** caixa de diálogo e clique em **publicar**.

![Publicar Web][api-management-web-publish]

## <a name="grant-permissions-to-the-azure-ad-backend-service-application"></a>Conceder permissões para a aplicação de serviço de back-end do Azure AD
É criada uma nova aplicação para o serviço de back-end no seu diretório do Azure AD como parte do processo de configuração e publicação do seu projeto Web API. Neste passo do vídeo, começando 6:13, são concedidas permissões para o back-end de Web API.

![Aplicação][api-management-aad-backend-app]

Clique no nome da aplicação para configurar as permissões necessárias. Navegue para o **configurar** separador e desloque para baixo até o **permissões para outras aplicações** secção. Clique em de **permissões de aplicação** pendente junto a **Windows** **do Azure Active Directory**, marque a caixa de **ler os dados de diretório** e clique em **guardar**.

![Adicionar permissões][api-management-aad-add-permissions]

> [!NOTE]
> Se **Windows** **do Azure Active Directory** não é listados na permissões para outras aplicações, clique em **Adicionar aplicação** e adicioná-la a partir da lista.
> 
> 

Anote o **URI de Id de aplicação** para utilização num passo subsequente quando uma aplicação do Azure AD está configurada para o portal do Programador de API Management.

![URI de Id de aplicação][api-management-aad-sso-uri]

## <a name="import-the-web-api-into-api-management"></a>Importar a API Web para gestão de API
As APIs são configuradas a partir do portal de publicador da API, que é acedido através do Portal do Azure. Para acedê-lo, clique em **portal do publicador** da barra de ferramentas do seu serviço de API Management. Se ainda não criou uma instância de serviço de API Management, consulte [criar uma instância de serviço de API Management] [ Create an API Management service instance] no [gerir a sua primeira API] [ Manage your first API] tutorial.

![Portal do publicador][api-management-management-console]

As operações podem ser [adicionados manualmente a APIs](api-management-howto-add-operations.md), ou podem ser importados. Neste vídeo, as operações são importadas no formato Swagger começando 6:40.

Crie um ficheiro denominado `calcapi.json` com o seguinte conteúdo e guarde-o para o seu computador. Certifique-se de que o `host` atributo pontos para o back-end de Web API. Neste exemplo `"host": "apimaaddemo.azurewebsites.net"` é utilizado.

```json
{
  "swagger": "2.0",
  "info": {
    "title": "Calculator",
    "description": "Arithmetics over HTTP!",
    "version": "1.0"
  },
  "host": "apimaaddemo.azurewebsites.net",
  "basePath": "/api",
  "schemes": [
    "http"
  ],
  "paths": {
    "/add?a={a}&b={b}": {
      "get": {
        "description": "Responds with a sum of two numbers.",
        "operationId": "Add two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>51</code>.",
            "required": true,
            "type": "string",
            "default": "51",
            "enum": [
              "51"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>49</code>.",
            "required": true,
            "type": "string",
            "default": "49",
            "enum": [
              "49"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/sub?a={a}&b={b}": {
      "get": {
        "description": "Responds with a difference between two numbers.",
        "operationId": "Subtract two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>100</code>.",
            "required": true,
            "type": "string",
            "default": "100",
            "enum": [
              "100"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>50</code>.",
            "required": true,
            "type": "string",
            "default": "50",
            "enum": [
              "50"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/div?a={a}&b={b}": {
      "get": {
        "description": "Responds with a quotient of two numbers.",
        "operationId": "Divide two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>100</code>.",
            "required": true,
            "type": "string",
            "default": "100",
            "enum": [
              "100"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>20</code>.",
            "required": true,
            "type": "string",
            "default": "20",
            "enum": [
              "20"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/mul?a={a}&b={b}": {
      "get": {
        "description": "Responds with a product of two numbers.",
        "operationId": "Multiply two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>20</code>.",
            "required": true,
            "type": "string",
            "default": "20",
            "enum": [
              "20"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>5</code>.",
            "required": true,
            "type": "string",
            "default": "5",
            "enum": [
              "5"
            ]
          }
        ],
        "responses": { }
      }
    }
  }
}
```

Para importar a API de calculadora, clique em **APIs** no menu **API Management** à esquerda e, em seguida, clique em **Importar API**.

![Botão Importar API][api-management-import-api]

Execute os seguintes passos para configurar a API de calculadora.

1. Clique em **do ficheiro**, navegue para o `calculator.json` ficheiro que guardou e clique em de **Swagger** botão de opção.
2. Tipo **calc** para o **sufixo do URL da API Web** caixa de texto.
3. Clique na caixa **Produtos (opcionais)** e selecione **Inicial**.
4. Clique em **Guardar** para importar a API.

![Adicionar nova API][api-management-import-new-api]

Quando a API for importada, a página de resumo da API é apresentada no portal do publicador.

## <a name="call-the-api-unsuccessfully-from-the-developer-portal"></a>Chamar a API sem êxito a partir do portal do Programador
Neste momento, a API foi importada para a API Management, mas não pode ainda ser chamada com êxito do portal do programador porque o serviço de back-end está protegido com a autenticação do Azure AD. Isto é demonstrado no vídeo começando 7:40 utilizando os seguintes passos.

Clique em **portal do programador** do lado direito da parte superior do portal do publicador.

![Portal do programador][api-management-developer-portal-menu]

Clique em **APIs** e clique em de **Calculadora** API.

![Portal do programador][api-management-dev-portal-apis]

Clique em **experimente**.

![Experimente][api-management-dev-portal-try-it]

Clique em **enviar** e tenha em atenção o estado de resposta de **não autorizado 401**.

![Enviar][api-management-dev-portal-send-401]

O pedido não está autorizado porque o API de back-end está protegido pelo Azure Active Directory. Antes de chamar com êxito a API do programador portal tem de ser configurado para autorizar os programadores a utilização de OAuth 2.0. Este processo é descrito nas secções seguintes.

## <a name="register-the-developer-portal-as-an-aad-application"></a>Registar o portal do programador como uma aplicação AAD
É o primeiro passo para configurar o portal do programador para autorizar os programadores a utilização de OAuth 2.0 para registar o portal do programador como uma aplicação AAD. Isto é demonstrado começando 8:27 as vídeo.

Navegue para o inquilino do Azure AD do primeiro passo neste vídeo, neste exemplo **APIMDemo** e navegue para o **aplicações** separador.

![nova aplicação][api-management-aad-new-application-devportal]

Clique em de **adicionar** botão para criar uma nova aplicação do Azure Active Directory e escolha **adicionar uma aplicação que a minha organização está a desenvolver**.

![nova aplicação][api-management-new-aad-application-menu]

Escolha **Web de aplicação e/ou Web API**, introduza um nome e clique na seta seguinte. Neste exemplo **APIMDeveloperPortal** é utilizado.

![nova aplicação][api-management-aad-new-application-devportal-1]

Para **URL de início de sessão** introduza o URL do seu serviço de API Management e de acréscimo `/signin`. Neste exemplo `https://contoso5.portal.azure-api.net/signin` é utilizado.

Para **URL de Id de aplicação** introduza o URL do seu serviço de API Management e de acréscimo alguns carateres exclusivos. Estes podem ser quaisquer carateres pretendidos e, neste exemplo `https://contoso5.portal.azure-api.net/dp` é utilizado. Quando o pretendido **as propriedades da aplicação** são configurados, clique na marca de verificação para criar a aplicação.

![nova aplicação][api-management-aad-new-application-devportal-2]

## <a name="configure-an-api-management-oauth-20-authorization-server"></a>Configurar um servidor de autorização de API Management OAuth 2.0
O passo seguinte consiste em configurar um servidor de autorização do OAuth 2.0 na API Management. Este passo é demonstrado as vídeo começando 9:43.

Clique em **segurança** a gestão de API no menu à esquerda, clique em **OAuth 2.0**e, em seguida, clique em **adicionar autorização** servidor.

![Adicionar servidor de autorização][api-management-add-authorization-server]

Introduza um nome e uma descrição opcional no **nome** e **Descrição** campos. Estes campos são utilizados para identificar o servidor de autorização do OAuth 2.0 na instância do serviço de API Management. Neste exemplo **demonstração de servidor de autorização** é utilizado. Mais tarde, quando especificar um servidor de OAuth 2.0 para ser utilizado para autenticação de uma API, irá selecionar este nome.

Para o **URL de página de registo de cliente** introduza um valor do marcador de posição como `http://localhost`.  O **URL de página de registo de cliente** aponta para a página que os utilizadores podem utilizar para criar e configurar as suas próprias contas para fornecedores de OAuth 2.0 que suportem gestão de contas de utilizadores. Neste exemplo os utilizadores não criar e configurar as suas próprias contas, pelo que é utilizado um marcador de posição.

![Adicionar servidor de autorização][api-management-add-authorization-server-1]

Em seguida, especifique **URL de ponto final de autorização** e **URL de ponto final do Token**.

![servidor de autorização][api-management-add-authorization-server-1a]

Estes valores podem ser obtidos a partir de **pontos finais de aplicação** página da aplicação AAD que criou para o portal do programador. Para aceder os pontos finais, navegue para o **configurar** separador para a aplicação AAD e clique em **ver pontos finais**.

![Aplicação][api-management-aad-devportal-application]

![Pontos finais de vista][api-management-aad-view-endpoints]

Copiar o **ponto final de autorização do OAuth 2.0** e cole-o para o **URL de ponto final de autorização** caixa de texto.

![Adicionar servidor de autorização][api-management-add-authorization-server-2]

Copiar o **ponto final de tokens de OAuth 2.0** e cole-o para o **URL de ponto final do Token** caixa de texto.

![Adicionar servidor de autorização][api-management-add-authorization-server-2a]

Para além de colar no ponto final do token, adicionar um parâmetro de corpo adicionais com o nome **recursos** e a utilização de valor de **URI de Id de aplicação** da aplicação AAD para o serviço de back-end que foi criada quando o Projeto do Visual Studio foi publicado.

![URI de Id de aplicação][api-management-aad-sso-uri]

Em seguida, especifique as credenciais de cliente. Estas são as credenciais para o recurso que pretende aceder, neste caso o portal do programador.

![Credenciais do cliente][api-management-client-credentials]

Para obter o **Id de cliente**, navegue para o **configurar** separador da aplicação AAD para o portal do programador e copie o **Id de cliente**.

Para obter o **segredo do cliente** clique o **selecione duração** pendente no **chaves** secção e especificar um intervalo. Neste exemplo é utilizado a 1 ano.

![ID de cliente][api-management-aad-client-id]

Clique em **guardar** para guardar a configuração e apresentar a chave. 

> [!IMPORTANT]
> Tome nota desta chave. Depois de fechar a janela de configuração do Azure Active Directory, a chave não pode ser apresentada.
> 
> 

Copie a chave para a área de transferência, regresse ao portal do publicador, cole a chave para o **segredo do cliente** caixa de texto e clique em **guardar**.

![Adicionar servidor de autorização][api-management-add-authorization-server-3]

Imediatamente a seguir as credenciais do cliente é uma concessão do código de autorização. Copiar este código de autorização e o comutador de volta para a aplicação de portal para programadores do Azure AD Configurar página e cole a concessão de autorização para o **URL de resposta** campo e clique em **guardar** novamente.

![URL de resposta][api-management-aad-reply-url]

O passo seguinte é configurar as permissões para o portal de programador da aplicação AAD. Clique em **permissões de aplicação** e marque a caixa de **ler os dados de diretório**. Clique em **guardar** guardar esta alteração e, em seguida, clique em **Adicionar aplicação**.

![Adicionar permissões][api-management-add-devportal-permissions]

Clique no ícone de pesquisa, tipo **APIM** para iniciar com caixa, selecione **APIMAADDemo**e clique na marca de verificação para guardar.

![Adicionar permissões][api-management-aad-add-app-permissions]

Clique em **permissões delegadas** para **APIMAADDemo** e marque a caixa de **acesso APIMAADDemo**e clique em **guardar**. Isto permite que o programador da aplicação do portal para aceder ao serviço de back-end.

![Adicionar permissões][api-management-aad-add-delegated-permissions]

## <a name="enable-oauth-20-user-authorization-for-the-calculator-api"></a>Ativar a autorização de utilizador de OAuth 2.0 para a API de Calculadora
Agora que o servidor de OAuth 2.0 está configurado, pode especificá-la nas definições de segurança para a API. Este passo é demonstrado as vídeo começando 14:30.

Clique em **APIs** no menu à esquerda e clique **Calculadora** para ver e configurar as respetivas definições.

![API de Calculadora][api-management-calc-api]

Navegue para o **segurança** separador, verifique o **OAuth 2.0** caixa de verificação, selecione o servidor de autorização pretendido do **servidor autorização** pendente e clique em  **Guardar**.

![API de Calculadora][api-management-enable-aad-calculator]

## <a name="successfully-call-the-calculator-api-from-the-developer-portal"></a>Chamar com êxito a API de Calculadora a partir do portal do Programador
Agora que a autorização do OAuth 2.0 está configurada na API, as suas operações podem ser chamadas com êxito do Centro de programadores. Este passo é demonstrado as vídeo começando 15:00.

Navegue de volta para o **adicionar dois números inteiros** operação do serviço Calculadora no portal do programador e clique **experimente**. Tenha em atenção o novo item no **autorização** secção correspondente para o servidor de autorização que acabou de adicionar.

![API de Calculadora][api-management-calc-authorization-server]

Selecione **código de autorização** da autorização pendente lista e introduza as credenciais da conta a utilizar. Se já tem sessão iniciada com a conta que poderá não ser-lhe solicitado.

![API de Calculadora][api-management-devportal-authorization-code]

Clique em **enviar** e tenha em atenção o **estado de resposta** de **200 OK** e os resultados da operação no conteúdo de resposta.

![API de Calculadora][api-management-devportal-response]

## <a name="configure-a-desktop-application-to-call-the-api"></a>Configurar uma aplicação de ambiente de trabalho para chamar a API
O procedimento seguinte no vídeo inicia à 16:30 e configura uma aplicação de ambiente de trabalho simple para chamar a API. O primeiro passo consiste em registar a aplicação de ambiente de trabalho no Azure AD e conceda-lhe acesso para o diretório e o serviço de back-end. Às 18:25 há uma demonstração da aplicação de ambiente de trabalho chamar uma operação a API de calculadora.

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configurar a política de validação de JWT para autorizar previamente pedidos
O procedimento final as vídeo começa às 20:48 e mostra-lhe como utilizar o [validar JWT](https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT) política para autorizar previamente pedidos ao validar os tokens de acesso de cada pedido recebido. Se o pedido não é validado pela política de validar JWT, o pedido está bloqueado por API Management e não é transmitido para o back-end.

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
        </claim>
    </required-claims>
</validate-jwt>
```

Para outra demonstração de configurar e utilizar esta política, consulte [nuvem abrangem episódio 177: mais funcionalidades de gestão de API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e fast-forward a 13:50 carateres. Reencaminhe rápido para 15:00 para ver as políticas configuradas no editor de política e, em seguida, para 18:50 para uma demonstração de chamar uma operação a partir do portal do programador com e sem o token de autorização necessário.

## <a name="next-steps"></a>Passos seguintes
* Consulte mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre a API Management.
* Para outras formas de proteger o seu serviço de back-end, consulte [autenticação de certificados mútuos](api-management-howto-mutual-certificates.md).

[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[api-management-developer-portal-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-developer-portal-menu.png
[api-management-dev-portal-apis]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-apis.png
[api-management-dev-portal-try-it]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-try-it.png
[api-management-dev-portal-send-401]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-send-401.png
[api-management-aad-new-application-devportal]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal.png
[api-management-aad-new-application-devportal-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-1.png
[api-management-aad-new-application-devportal-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-2.png
[api-management-aad-devportal-application]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-devportal-application.png
[api-management-add-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server.png
[api-management-aad-sso-uri]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-sso-uri.png
[api-management-aad-view-endpoints]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-view-endpoints.png
[api-management-aad-client-id]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-client-id.png
[api-management-add-authorization-server-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1.png
[api-management-add-authorization-server-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2.png
[api-management-add-authorization-server-2a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2a.png
[api-management-add-authorization-server-3]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-3.png
[api-management-aad-reply-url]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-reply-url.png
[api-management-add-devportal-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-devportal-permissions.png
[api-management-aad-add-app-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-app-permissions.png
[api-management-aad-add-delegated-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-delegated-permissions.png
[api-management-calc-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-api.png
[api-management-enable-aad-calculator]: ./media/api-management-howto-protect-backend-with-aad/api-management-enable-aad-calculator.png
[api-management-devportal-authorization-code]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-authorization-code.png
[api-management-devportal-response]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-response.png
[api-management-calc-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-authorization-server.png
[api-management-add-authorization-server-1a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1a.png
[api-management-client-credentials]: ./media/api-management-howto-protect-backend-with-aad/api-management-client-credentials.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-aad-application-menu.png

[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Manage your first API]: api-management-get-started.md
