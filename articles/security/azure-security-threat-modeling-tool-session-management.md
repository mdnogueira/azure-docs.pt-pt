---
title: "Azure de gestão - ferramenta de modelação de ameaça Microsoft - sessão | Microsoft Docs"
description: "Mitigações ameaças exposta na ferramenta de modelação de ameaça"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 56471d8ef68eacacb3ecebad5056d7e7a9f3ca40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="security-frame-session-management--articles"></a>Segurança moldura: Gestão de sessão | Artigos 
| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Implementar a fim de sessão correto utilizando métodos ADAL quando utilizar o Azure AD](#logout-adal)</li></ul> |
| Dispositivo IoT | <ul><li>[Utilizar durações finita para os tokens SaS gerados](#finite-tokens)</li></ul> |
| **Base de dados de documento do Azure** | <ul><li>[Utilizar durações token mínimo para os tokens de recurso gerados](#resource-tokens)</li></ul> |
| **AD FS** | <ul><li>[Implementar a fim de sessão correto utilizando métodos de WsFederation ao utilizar o AD FS](#wsfederation-logout)</li></ul> |
| **Servidor de identidades** | <ul><li>[Implementar a fim de sessão adequada ao utilizar o servidor de identidades](#proper-logout)</li></ul> |
| **Aplicação Web** | <ul><li>[Aplicações disponíveis através de HTTPS tem de utilizar cookies seguros](#https-secure-cookies)</li><li>[Todas as aplicações de http com base devem especificar http apenas para a definição de cookie](#cookie-definition)</li><li>[Mitigar contra ataques de falsificação de pedidos entre sites (CSRF) em páginas web ASP.NET](#csrf-asp)</li><li>[Configurar sessão para a duração de inatividade](#inactivity-lifetime)</li><li>[Implementar a fim de sessão correto da aplicação](#proper-app-logout)</li></ul> |
| **API Web** | <ul><li>[Mitigar contra ataques de falsificação de pedidos entre sites (CSRF) nas APIs da Web ASP.NET](#csrf-api)</li></ul> |

## <a id="logout-adal"></a>Implementar a fim de sessão correto utilizando métodos ADAL quando utilizar o Azure AD

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Se a aplicação depende do token de acesso emitido pelo Azure AD, o processador de eventos de fim de sessão deve chamar |

### <a name="example"></a>Exemplo
```C#
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Exemplo
-Também deve destruir a sessão do utilizador ao chamar o método Session.Abandon(). Método a seguir mostra a implementação segura de fim de sessão do utilizador:
```C#
    [HttpPost]
        [ValidateAntiForgeryToken]
        public void LogOff()
        {
            string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
            AuthenticationContext authContext = new AuthenticationContext(Authority + TenantId, new NaiveSessionCache(userObjectID));
            authContext.TokenCache.Clear();
            Session.Clear();
            Session.Abandon();
            Response.SetCookie(new HttpCookie("ASP.NET_SessionId", string.Empty));
            HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
        } 
```

## <a id="finite-tokens"></a>Utilizar durações finita para os tokens SaS gerados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Os tokens SaS gerados para autenticar no IoT Hub do Azure devem ter um período de expiração finita. Mantenha as durações de token SaS para um mínimo para limitar a quantidade de tempo que podem ser reproduzidos, caso sejam comprometidos os tokens.|

## <a id="resource-tokens"></a>Utilizar durações token mínimo para os tokens de recurso gerados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de dados de documento do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Reduza o período de tempo do token de recursos para um valor mínimo necessário. Tokens de recursos possui um timespan válido predefinido de 1 hora.|

## <a id="wsfederation-logout"></a>Implementar a fim de sessão correto utilizando métodos de WsFederation ao utilizar o AD FS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | ADFS | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Se a aplicação depende do token de STS emitido pelo AD FS, o processador de eventos de fim de sessão deve chamar o método de WSFederationAuthenticationModule.FederatedSignOut() para terminar o utilizador. Também deve ser destruída a sessão atual e o valor de token de sessão deve ser reposto e nullified.|

### <a name="example"></a>Exemplo
```C#
        [HttpPost, ValidateAntiForgeryToken]
        [Authorization]
        public ActionResult SignOut(string redirectUrl)
        {
            if (!this.User.Identity.IsAuthenticated)
            {
                return this.View("LogOff", null);
            }

            // Removes the user profile.
            this.Session.Clear();
            this.Session.Abandon();
            HttpContext.Current.Response.Cookies.Add(new System.Web.HttpCookie("ASP.NET_SessionId", string.Empty)
                {
                    Expires = DateTime.Now.AddDays(-1D),
                    Secure = true,
                    HttpOnly = true
                });

            // Signs out at the specified security token service (STS) by using the WS-Federation protocol.
            Uri signOutUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Issuer);
            Uri replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm);
            if (!string.IsNullOrEmpty(redirectUrl))
            {
                replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm + redirectUrl);
            }
           //     Signs out of the current session and raises the appropriate events.
            var authModule = FederatedAuthentication.WSFederationAuthenticationModule;
            authModule.SignOut(false);
        //     Signs out at the specified security token service (STS) by using the WS-Federation
        //     protocol.            
            WSFederationAuthenticationModule.FederatedSignOut(signOutUrl, replyUrl);
            return new RedirectResult(redirectUrl);
        }
```

## <a id="proper-logout"></a>Implementar a fim de sessão adequada ao utilizar o servidor de identidades

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de identidades | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Federadas IdentityServer3 terminar](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Passos** | IdentityServer suporta a capacidade para federar com fornecedores de identidade externas. Quando um utilizador inicia sessão fora de um fornecedor de identidade a montante, consoante o protocolo utilizado, poderá ser possível receber uma notificação quando o utilizador termina a sessão. Permite IdentityServer notificar os seus clientes para que eles podem também termine o utilizador. Consulte a documentação na secção de referências para obter os detalhes de implementação.|

## <a id="https-secure-cookies"></a>Aplicações disponíveis através de HTTPS tem de utilizar cookies seguros

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | EnvironmentType - OnPrem |
| **Referências**              | [httpCookies elemento (esquema de definições do ASP.NET)](http://msdn.microsoft.com/library/ms228262(v=vs.100).aspx), [HttpCookie.Secure propriedade](http://msdn.microsoft.com/library/system.web.httpcookie.secure.aspx) |
| **Passos** | Os cookies são normalmente apenas acessíveis para o domínio para os quais foram âmbito. Infelizmente, a definição de "domínio" não inclui o protocolo para que cookies que são criados através de HTTPS estão acessíveis através de HTTP. O atributo "seguro" indica o browser que o cookie deve apenas ser disponibilizado através de HTTPS. Certifique-se de que todos os cookies definido através da utilização HTTPS de **segura** atributo. O requisito pode ser imposto no ficheiro Web. config, definindo o atributo requireSSL como verdadeiro. É a abordagem preferida porque irá impor o **segura** atributo para todos os cookies atuais e futuros sem a necessidade de efetuar quaisquer alterações de código adicional.|

### <a name="example"></a>Exemplo
```C#
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
A definição é imposta, mesmo se HTTP é utilizado para aceder à aplicação. Se HTTP é utilizado para aceder à aplicação, a definição interrompe a aplicação porque os cookies estão definidos com o atributo seguro e o browser não enviará-los para a aplicação.

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Formulários Web, MVC5 |
| **Atributos**              | EnvironmentType - OnPrem |
| **Referências**              | N/D  |
| **Passos** | Quando a aplicação web é a entidade Confiadora e IdP é o servidor do ADFS, atributo segura do token FedAuth pode ser configurado através da definição requireSSL como True no `system.identityModel.services` secção da Web. config:|

### <a name="example"></a>Exemplo
```C#
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:20:0" />
    ....  
    </federationConfiguration>
  </system.identityModel.services>
```

## <a id="cookie-definition"></a>Todas as aplicações de http com base devem especificar http apenas para a definição de cookie

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Atributo de Cookie seguro](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Passos** | Para mitigar o risco de divulgação de informações com um ataque de processamento de scripts entre sites (XSS), um novo atributo - httpOnly - introduzido para cookies e é suportado por todos os browsers principais. O atributo Especifica que um cookie não está acessível através do script. Ao utilizar HttpOnly cookies, uma aplicação web reduz a possibilidade de que as informações confidenciais contidas no cookie roubadas através do script e enviadas para o site de um atacante. |

### <a name="example"></a>Exemplo
Todas as aplicações baseados em HTTP que utilize cookies devem especificar HttpOnly na definição do cookie, implementando a seguinte configuração em Web. config:
```XML
<system.web>
.
.
   <httpCookies requireSSL="false" httpOnlyCookies="true"/>
.
.
</system.web>
```

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Formulários Web |
| **Atributos**              | N/D  |
| **Referências**              | [Propriedade de FormsAuthentication.RequireSSL](https://msdn.microsoft.com/library/system.web.security.formsauthentication.requiressl.aspx) |
| **Passos** | O valor da propriedade RequireSSL está definido no ficheiro de configuração para uma aplicação ASP.NET, utilizando o atributo requireSSL do elemento de configuração. Pode especificar o ficheiro Web. config para a sua aplicação ASP.NET se é necessário o SSL (Secure Sockets Layer) para devolver o cookie de autenticação de formulários para o servidor ao definir o atributo requireSSL.|

### <a name="example"></a>Exemplo 
Exemplo de código seguinte define o atributo requireSSL no ficheiro Web. config.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5 |
| **Atributos**              | EnvironmentType - OnPrem |
| **Referências**              | [Configuração do Windows Identity Foundation (WIF) – parte II](https://blogs.msdn.microsoft.com/alikl/2011/02/01/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler/) |
| **Passos** | Definir o atributo de httpOnly para FedAuth cookies, o valor do atributo hideFromCsript deve ser definido como True. |

### <a name="example"></a>Exemplo
Configuração seguinte mostra a configuração correta:
```XML
<federatedAuthentication>
<cookieHandler mode="Custom"
                       hideFromScript="true"
                       name="FedAuth"
                       path="/"
                       requireSsl="true"
                       persistentSessionLifetime="25">
</cookieHandler>
</federatedAuthentication>
```

## <a id="csrf-asp"></a>Mitigar contra ataques de falsificação de pedidos entre sites (CSRF) em páginas web ASP.NET

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Falsificação de pedidos entre sites (CSRF ou XSRF) é um tipo de ataque em que um atacante pode executar ações no contexto de segurança de sessão estabelecida de um utilizador diferente num web site. O objetivo é modificar ou eliminar conteúdo, se o web site de destino baseia-se exclusivamente no cookies de sessão para autenticar o pedido recebido. Um atacante pode explorar esta vulnerabilidade obtendo o browser de um utilizador diferente para carregar um URL com um comando a partir de um site vulnerável no qual o utilizador já está registado no. Existem várias formas para fazê-lo, tal como alojar um web site diferente que carrega um recurso do servidor vulnerável ou o utilizador a obter um atacante que cliquem numa ligação. Ataque pode ser impedido se o servidor envia um token adicional para o cliente, exige que o cliente incluir esse token em todos os pedidos futuros e verifica se todos os pedidos futuros incluem um token que diz respeito à sessão atual, tal como utilizando o ASP.NET AntiForgeryToken ou ViewState. |

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Prevenção de XSRF/CSRF no ASP.NET MVC e páginas Web](http://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Passos** | Anti CSRF e formulários do ASP.NET MVC - utilize o `AntiForgeryToken` método de programa auxiliar em vistas; put um `Html.AntiForgeryToken()` no formulário, por exemplo,|

### <a name="example"></a>Exemplo
```C#
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
```

### <a name="example"></a>Exemplo
```C#
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Exemplo
Ao mesmo tempo, Html.AntiForgeryToken() fornece o visitante um cookie de chamada __RequestVerificationToken, com o mesmo valor que o valor de oculto aleatório mostrado acima. Em seguida, para validar uma mensagem de formulário de entrada, adicione o filtro [ValidateAntiForgeryToken] para o método de ação de destino. Por exemplo:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtro de autorização que verifica se:
* O pedido recebido tem um cookie de chamada __RequestVerificationToken
* O pedido recebido tem um `Request.Form` entrada chamada __RequestVerificationToken
* Estes cookies e `Request.Form` correspondência de valores, partindo do princípio de todos os está corretamente, o pedido passa por como normal. Mas se não estiver, em seguida, uma falha de autorização com a mensagem "um token antifalsificação necessário não foi fornecido ou era inválido". 

### <a name="example"></a>Exemplo
Anti CSRF e AJAX: O token de formulário pode ser um problema para pedidos de AJAX, porque um pedido de AJAX pode enviar dados JSON, dados do formulário não HTML. É uma solução enviar os tokens de um cabeçalho HTTP personalizado. O seguinte código utiliza sintaxe Razor para gerar os tokens e, em seguida, adiciona os tokens para um pedido de AJAX. 
```C#
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }

    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Exemplo
Ao processar o pedido, extraia os tokens do cabeçalho do pedido. Em seguida, chame o método de AntiForgery.Validate para validar os tokens. O método Validate emite uma exceção se os tokens não são válidos.
```C#
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Formulários Web |
| **Atributos**              | N/D  |
| **Referências**              | [Tirar partido das funcionalidades de ASP.NET incorporado para Fend desativar ataques da Web](https://msdn.microsoft.com/library/ms972969.aspx#securitybarriers_topic2) |
| **Passos** | Ataques CSRF WebForm com base em aplicações podem ser mitigados definindo ViewStateUserKey como uma cadeia de aleatória varia para cada utilizador - ID de utilizador ou, ainda, melhor ID de sessão. Para uma série de razões técnicos e redes sociais, o ID de sessão é uma opção muito melhor porque um ID da sessão é imprevisível, o tempo limite e varia numa base por utilizador.|

### <a name="example"></a>Exemplo
Eis o código que tem de ter em todas as páginas:
```C#
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a id="inactivity-lifetime"></a>Configurar sessão para a duração de inatividade

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Propriedade de HttpSessionState.Timeout](https://msdn.microsoft.com/library/system.web.sessionstate.httpsessionstate.timeout(v=vs.110).aspx) |
| **Passos** | Tempo limite da sessão representa o evento ocorrer quando um utilizador não efetuar qualquer ação num web site durante um intervalo (definido pelo servidor web). O evento, no lado do servidor, altere o estado da sessão de utilizador para 'inválido' (por exemplo "não utilizado deixam de ser") e dar instruções ao servidor web para destruir a (eliminar todos os dados contidos no mesmo). Exemplo de código seguinte define o atributo de sessão de tempo limite para 15 minutos no ficheiro Web. config.|

### <a name="example"></a>Exemplo
' ' Código XML <configuration> < System. Web > <sessionState mode="InProc" cookieless="true" timeout="15" /> </System. Web ></configuration>
```

## <a id="threat-detection"></a>Enable Threat detection on Azure SQL
```

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Formulários Web |
| **Atributos**              | N/D  |
| **Referências**              | [Elemento de formulários para autenticação (esquema de definições do ASP.NET)](https://msdn.microsoft.com/library/1d3t3c61(v=vs.100).aspx) |
| **Passos** | Definir o limite de tempo do cookie de permissão de autenticação de formulários para 15 minutos|

### <a name="example"></a>Exemplo
' ' Código XML<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Title                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Web Application | 
| **SDL Phase**               | Build |  
| **Applicable Technologies** | Web Forms, MVC5 |
| **Attributes**              | EnvironmentType - OnPrem |
| **References**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Steps** | When the web application is Relying Party and ADFS is the STS, the lifetime of the authentication cookies - FedAuth tokens - can be set by the following configuration in web.config:|

### Example
```XML
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:15:0" />
      <!-- Set requireHttps=true; -->
      <wsFederation passiveRedirectEnabled="true" issuer="http://localhost:39529/" realm="https://localhost:44302/" reply="https://localhost:44302/" requireHttps="true"/>
      <!--
      Use the code below to enable encryption-decryption of claims received from ADFS. Thumbprint value varies based on the certificate being used.
      <serviceCertificate>
        <certificateReference findValue="4FBBBA33A1D11A9022A5BF3492FF83320007686A" storeLocation="LocalMachine" storeName="My" x509FindType="FindByThumbprint" />
      </serviceCertificate>
      -->
    </federationConfiguration>
  </system.identityModel.services>
```

### <a name="example"></a>Exemplo
Também os ADFS emitidos SAML afirmação duração do token deve ser definida como 15 minutos, executando o seguinte comando do powershell no servidor do AD FS:
```C#
Set-ADFSRelyingPartyTrust -TargetName “<RelyingPartyWebApp>” -ClaimsProviderName @(“Active Directory”) -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a id="proper-app-logout"></a>Implementar a fim de sessão correto da aplicação

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Efetue adequada terminar sessão da aplicação, quando o utilizador presses terminar botão. Após a fim de sessão, aplicação deve destruir a sessão do utilizador e também repor e anula o valor do cookie de sessão, juntamente com a reposição e nullifying valor do cookie de autenticação. Além disso, quando várias sessões estão associadas a uma identidade de utilizador único, estes tem de ser coletivamente terminadas no lado do servidor no tempo limite ou de fim de sessão. Por último, certifique-se de que a funcionalidade de fim de sessão está disponível em todas as páginas. |

## <a id="csrf-api"></a>Mitigar contra ataques de falsificação de pedidos entre sites (CSRF) nas APIs da Web ASP.NET

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Falsificação de pedidos entre sites (CSRF ou XSRF) é um tipo de ataque em que um atacante pode executar ações no contexto de segurança de sessão estabelecida de um utilizador diferente num web site. O objetivo é modificar ou eliminar conteúdo, se o web site de destino baseia-se exclusivamente no cookies de sessão para autenticar o pedido recebido. Um atacante pode explorar esta vulnerabilidade obtendo o browser de um utilizador diferente para carregar um URL com um comando a partir de um site vulnerável no qual o utilizador já está registado no. Existem várias formas para fazê-lo, tal como alojar um web site diferente que carrega um recurso do servidor vulnerável ou o utilizador a obter um atacante que cliquem numa ligação. Ataque pode ser impedido se o servidor envia um token adicional para o cliente, exige que o cliente incluir esse token em todos os pedidos futuros e verifica se todos os pedidos futuros incluem um token que diz respeito à sessão atual, tal como utilizando o ASP.NET AntiForgeryToken ou ViewState. |

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Impedir ataques de falsificação (CSRF) de pedidos entre sites na API Web do ASP.NET](http://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Passos** | Anti CSRF e AJAX: O token de formulário pode ser um problema para pedidos de AJAX, porque um pedido de AJAX pode enviar dados JSON, dados do formulário não HTML. É uma solução enviar os tokens de um cabeçalho HTTP personalizado. O seguinte código utiliza sintaxe Razor para gerar os tokens e, em seguida, adiciona os tokens para um pedido de AJAX. |

### <a name="example"></a>Exemplo
```Javascript
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }
    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Exemplo
Ao processar o pedido, extraia os tokens do cabeçalho do pedido. Em seguida, chame o método de AntiForgery.Validate para validar os tokens. O método Validate emite uma exceção se os tokens não são válidos.
```C#
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

### <a name="example"></a>Exemplo
Anti CSRF e formulários do ASP.NET MVC - utilize o método de programa auxiliar de AntiForgeryToken em vistas; colocar um Html.AntiForgeryToken() no formulário, por exemplo,
```C#
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Exemplo
O exemplo acima serão saída algo como o seguinte:
```C#
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Exemplo
Ao mesmo tempo, Html.AntiForgeryToken() fornece o visitante um cookie de chamada __RequestVerificationToken, com o mesmo valor que o valor de oculto aleatório mostrado acima. Em seguida, para validar uma mensagem de formulário de entrada, adicione o filtro [ValidateAntiForgeryToken] para o método de ação de destino. Por exemplo:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtro de autorização que verifica se:
* O pedido recebido tem um cookie de chamada __RequestVerificationToken
* O pedido recebido tem um `Request.Form` entrada chamada __RequestVerificationToken
* Estes cookies e `Request.Form` correspondência de valores, partindo do princípio de todos os está corretamente, o pedido passa por como normal. Mas se não estiver, em seguida, uma falha de autorização com a mensagem "um token antifalsificação necessário não foi fornecido ou era inválido".

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | Fornecedor de identidade do fornecedor - ADFS, identidade - do Azure AD |
| **Referências**              | [Proteger uma API Web com contas individuais e início de sessão Local na API 2.2 Web do ASP.NET](http://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Passos** | Se estiver a API Web protegidas através de OAuth 2.0, em seguida,-espera um token de portador no cabeçalho de pedido de autorização e concede acesso ao pedido apenas se o token é válido. Ao contrário de autenticação de cookie com base, os browsers não ligue os tokens de portador para pedidos. O cliente do requerente tem de anexar explicitamente o token de portador no cabeçalho do pedido. Por conseguinte, para APIs da Web do ASP.NET protegidos através do OAuth 2.0, os tokens de portador são considerados como uma defesa contra ataques CSRF. Tenha em atenção que se a parte do MVC da aplicação utiliza a autenticação de formulários (ou seja, utiliza cookies), antifalsificação tokens têm de ser utilizadas pela aplicação de web MVC. |

### <a name="example"></a>Exemplo
A API Web tem de ser informado confiar apenas tokens de portador on e não em cookies. Pode ser feito na configuração da seguinte no `WebApiConfig.Register` método: ' ' C Sharp configuração de código. SuppressDefaultHostAuthentication(); Config. Filters.Add (novo HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```
The SuppressDefaultHostAuthentication method tells Web API to ignore any authentication that happens before the request reaches the Web API pipeline, either by IIS or by OWIN middleware. That way, we can restrict Web API to authenticate only using bearer tokens.