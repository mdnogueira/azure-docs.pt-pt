---
title: "Como delegar a subscrição de produto e registo de utilizador"
description: "Saiba como delegar a subscrição de produto e registo de utilizador a terceiros na API Management do Azure."
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 1fad082a576bc994897c6b396e5edcfa02313487
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Como delegar a subscrição de produto e registo de utilizador
Delegação permite-lhe utilizar o seu Web site existente para processar programador sessão-na/sessão-up e subscrição produtos, por oposição a utilizar a funcionalidade incorporada no portal do programador. Isto permite que o seu Web site para proprietários de dados de utilizador e efetuar a validação destes passos de uma forma personalizada.

## <a name="delegate-signin-up"></a>Delegating programador início de sessão e inscrição
Para delegar programador início de sessão e inscrição para o Web site existente, terá de criar um ponto final de delegação especial no seu site que age como o ponto de entrada para esse pedido de iniciadas a partir do portal do Programador de API Management.

O fluxo de trabalho final será o seguinte:

1. Programador clica na ligação de início de sessão ou inscrição no portal do Programador de API Management
2. Browser é redirecionado para o ponto final de delegação
3. Ponto final de delegação no return redireciona para ou apresenta IU solicitar que o utilizador iniciar sessão ou inscrição
4. Com êxito, o utilizador é redirecionado para a página portal de Programador de API Management iniciadas a partir do

Para começar, vamos primeiro gestão de API de configuração para encaminhar pedidos através do seu ponto final de delegação. No portal do publicador da API Management, clique em **segurança** e, em seguida, clique em de **delegação** separador. Clique na caixa de verificação para ativar 'Delegate início de sessão e inscrição'.

![Página de delegação][api-management-delegation-signin-up]

* Decidir o que o URL do seu ponto final de delegação especial será e introduza-na **URL de ponto final de delegação** campo. 
* Dentro do **chave de autenticação de delegação** campo introduza um segredo que será utilizado para calcular a assinatura de fornecida para verificação para se certificar de que o pedido, de facto, é proveniente de API Management do Azure. Pode clicar no **gerar** botão com gestão de API gerar aleatoriamente uma chave para si.

Agora tem de criar o **ponto final de delegação**. Tem de efetuar várias ações:

1. Recebe um pedido no seguinte formato:
   
   > *http://www.yourwebsite.com/apimdelegation?Operation=SignIn&returnUrl= {URL da página de origem} & salt = {cadeia} & sig = {cadeia}*
   > 
   > 
   
    Parâmetros de consulta para o cenário de início de sessão / inscrição:
   
   * **operação**: identifica o tipo de pedido de delegação é - só pode ser **SignIn** neste caso,
   * **returnUrl**: o URL da página onde o utilizador clica numa ligação de início de sessão ou inscrição
   * **Salt**: uma cadeia de salt especial utilizada para um hash de segurança de computação
   * **SIG**: um hash calculado segurança a ser utilizado para comparar com a sua própria hash calculado
2. Certifique-se de que o pedido é proveniente de API Management do Azure (opcional, mas vivamente recomendado de segurança)
   
   * Um hash HMAC SHA512 de uma cadeia com base de computação a **returnUrl** e **salt** parâmetros de consulta ([código de exemplo fornecido abaixo]):
     
     > HMAC (**salt** + '\n' + **returnUrl**)
     > 
     > 
   * Comparar o acima-hash calculado para o valor da **sig** parâmetro de consulta. Se os dois hashes coincidirem, avançar para o passo seguinte, caso contrário negar o pedido.
3. Certifique-se de que estão a receber um pedido de início de sessão na/início de sessão-cópia de segurança: o **operação** parâmetro de consulta será definido para "**SignIn**".
4. Apresentar ao utilizador da IU para início de sessão ou inscrição
5. Se o utilizador iniciar sessão de segurança tem de criar uma conta correspondente para as mesmas na API Management. [Criar um utilizador] com a API de REST de gestão de API. Ao fazê-lo, certifique-se de que definiu o ID de utilizador para o mesmo está no arquivo de utilizador ou para um ID pode manter controlar dos.
6. Quando o utilizador é autenticado com êxito:
   
   * [pedir um token de sessão único (SSO)] através da API de REST de gestão de API
   * acrescente um parâmetro de consulta returnUrl para o URL de SSO receberam na chamada de API acima:
     
     > Por exemplo, https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * redirecionar o utilizador para o URL de produzidos acima

Para além de **SignIn** operação, também pode efetuar gestão de contas, seguindo os passos anteriores e utilizando uma das seguintes operações.

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**

Tem de passar os parâmetros de consulta seguinte para operações de gestão de conta.

* **operação**: identifica o tipo de pedido de delegação é (ChangePassword, ChangeProfile ou CloseAccount)
* **userId**: o id de utilizador da conta para gerir
* **Salt**: uma cadeia de salt especial utilizada para um hash de segurança de computação
* **SIG**: um hash calculado segurança a ser utilizado para comparar com a sua própria hash calculado

## <a name="delegate-product-subscription"></a>Delegar a subscrição do produto
Delegar a subscrição de produto funciona da mesma forma para delegar utilizador início de sessão/cópia de segurança. O fluxo de trabalho final seria o seguinte:

1. Programador seleciona um produto no portal do Programador de API Management e clica no botão subscrever
2. Browser é redirecionado para o ponto final de delegação
3. Ponto final de delegação efetua os passos de subscrição necessário produto - isto até que e poderá entail redirecionar para outra página para pedir informações de faturação, colocar questões adicionais, ou simplesmente armazenar as informações e não requer qualquer ação do utilizador

Para ativar a funcionalidade, no **delegação** página clique **delegar a subscrição de produto**.

Certifique-se que o ponto final de delegação efetua as seguintes ações:

1. Recebe um pedido no seguinte formato:
   
   > *http://www.yourwebsite.com/apimdelegation?Operation= {operação} & productId = {produto subscrever} & userId = {efetuar o pedido de utilizador} & salt = {cadeia} & sig = {cadeia}*
   > 
   > 
   
    Parâmetros de consulta para o cenário de subscrição do produto:
   
   * **operação**: identifica o tipo de pedido de delegação é. Para a subscrição de produto pedidos opções válidas são:
     * "Subscrever": fornecidos de um pedido para subscrever o utilizador para um produto especificado com o ID (ver abaixo)
     * "Anular a subscrição": um pedido para anular a subscrição de um utilizador de um produto
     * "Renovar": uma requst renovar uma subscrição (por exemplo, que pode ser expira)
   * **productId**: o ID do produto, o utilizador solicitou subscrever
   * **userId**: o ID do utilizador para o qual o pedido é efetuado
   * **Salt**: uma cadeia de salt especial utilizada para um hash de segurança de computação
   * **SIG**: um hash calculado segurança a ser utilizado para comparar com a sua própria hash calculado
2. Certifique-se de que o pedido é proveniente de API Management do Azure (opcional, mas vivamente recomendado de segurança)
   
   * Computação um SHA512 HMAC de uma cadeia com base no **productId**, **userId** e **salt** parâmetros de consulta:
     
     > HMAC (**salt** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * Comparar o acima-hash calculado para o valor da **sig** parâmetro de consulta. Se os dois hashes coincidirem, avançar para o passo seguinte, caso contrário negar o pedido.
3. Efetuar qualquer processamento de subscrição de produto com base no tipo de operação pedida no **operação** -por exemplo, de faturação, mais perguntas, etc.
4. Na subscrição com êxito o utilizador para o produto no seu lado, subscrever o utilizador para o produto de API Management por [chamada da API REST para a subscrição de produto].

## <a name="delegate-example-code"></a> Código de exemplo
Estes exemplos de código mostram como tirar o *chave de validação de delegação*, que está definido no ecrã de delegação do portal do publicador, para criar um HMAC que, em seguida, é utilizado para validar a assinatura, comprovar a validade do returnUrl transmitido . O mesmo código funciona para os productId e userId com ligeiras modificação.

**Código c# para gerar o hash do returnUrl**

```c#
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**Código de NodeJS para gerar o hash do returnUrl**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre delegação, veja o vídeo seguinte.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[pedir um token de sessão único (SSO)]: https://docs.microsoft.com/en-us/rest/api/apimanagement/User/GenerateSsoUrl
[criar um utilizador]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
[chamada da API REST para a subscrição de produto]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
[Next steps]: #next-steps
[código de exemplo fornecido abaixo]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
