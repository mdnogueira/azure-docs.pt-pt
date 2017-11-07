---
title: "Azure AD v2 ASP.NET Web iniciada - testar a introdução de servidor | Microsoft Docs"
description: "Implementar Microsoft início de sessão numa solução ASP.NET com uma aplicação de baseadas no browser web tradicional utilizando o padrão de OpenID Connect"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: bfa2563a6a58370d9a611440017441a751b46244
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
## <a name="test-your-code"></a>Testar o seu código

Prima `F5` para executar o projeto no Visual Studio. O browser irá abrir e direcioná-lo para *http://localhost: {porta}* onde irá ver o *iniciar sessão com a Microsoft* botão. Avançar e clique nele para iniciar sessão.

Quando estiver pronto para testar, utilize um trabalho ou escola (Azure Active Directory) ou uma conta pessoal (live.com, outlook.com) para iniciar sessão. 

![Inicie sessão com a janela do browser Microsoft](media/active-directory-serversidewebapp-aspnetwebappowin-test/aspnetbrowsersignin.png)

![Inicie sessão com a janela do browser Microsoft](media/active-directory-serversidewebapp-aspnetwebappowin-test/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>Resultados esperados
Após o início de sessão, o utilizador é redirecionado para a home page do web site que é o URL HTTPS especificado nas informações de registo de aplicação no Portal de registo de aplicações da Microsoft. Esta página mostra agora *Olá {utilizador}* e uma hiperligação para o fim de sessão e uma ligação para ver as afirmações do utilizador – que é uma hiperligação para o controlador de autorizar criada anteriormente.

### <a name="see-users-claims"></a>Consulte afirmações do utilizador
Selecione a hiperligação para ver as afirmações do utilizador. Isto orienta-o para o controlador e a vista que está disponível apenas para os utilizadores que são autenticados.

#### <a name="expected-results"></a>Resultados esperados
 Deverá ver uma tabela que contém as propriedades básicas do utilizador com sessão iniciada:

| Propriedade | Valor | Descrição|
|---|---|---|
| Nome | {Nome completo do utilizador} | O utilizador do primeiro e último nome
|Nome de utilizador | <span>user@domain.com</span>| O nome de utilizador utilizado para identificar o utilizador com sessão iniciada
| Assunto| {Requerente}|Uma cadeia para identificar exclusivamente o início de sessão do utilizador através da web|
| ID do inquilino| {Guid}| A *guid* para exclusivamente representar a organização do utilizador do Azure Active Directory.|

Além disso, verá uma tabela, incluindo todas as afirmações de utilizador incluídas no pedido de autenticação. Para obter uma lista de todas as afirmações num Id de Token explicação e ver estas [artigo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims "lista de afirmações num Token de Id").


### <a name="test-accessing-a-method-that-has-an-authorize-attribute-optional"></a>Teste aceder a um método que tenha um *[autorizar]* atributo (opcional)
Neste passo, irá testar a aceder ao controlador de autenticado como utilizador anónimo:<br/>
Selecione a ligação ao fim de sessão do utilizador e concluir o processo de início de sessão.<br/>
Agora no seu browser, escreva http://localhost: {porta} / autenticado para aceder ao seu controlador de que está protegido com o `[Authorize]` atributo

#### <a name="expected-results"></a>Resultados esperados
Deverá receber a linha de comandos exigir que se autentique, para ver a vista.

## <a name="additional-information"></a>Informações adicionais

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>Proteger o web site completo
Para proteger todo o site web, adicione o `AuthorizeAttribute` para `GlobalFilters` no `Global.asax` `Application_Start` método:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

> [!NOTE]
> **Como restringir os utilizadores da organização apenas um para iniciar sessão sua aplicação**

> Por predefinição, pessoal contas (incluindo outlook.com, live.com e outros), bem como de trabalho e contas escolares de qualquer da empresa ou organização que tem integrado com o Azure Active Directory podem iniciar sessão para a aplicação. 

> Se pretender que a aplicação para aceitar inícios de sessão da organização apenas um do Azure Active Directory, substitua o `Tenant` parâmetro *Web. config* de `Common` para o nome do inquilino da organização – exemplo, *contoso.onmicrosoft.com*. Depois disso, altere o `ValidateIssuer` argumento na sua *classe de Startup da OWIN* para `true`.

> Para permitir que os utilizadores apenas numa lista de organizações específicas, defina `ValidateIssuer` true e utilizar o `ValidIssuers` parâmetro para especificar uma lista das organizações.

> Outra opção consiste em implementar um método personalizado para validar os emissores utilizando o parâmetro IssuerValidator. Para obter mais informações sobre `TokenValidationParameters`, consulte [isto](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "artigo do TokenValidationParameters MSDN") artigo da MSDN.

[!INCLUDE  [Help and Support Options](../../../../includes/active-directory-develop-help-support-include.md)]