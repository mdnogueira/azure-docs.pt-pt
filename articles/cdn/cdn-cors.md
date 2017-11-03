---
title: Utilizar a CDN do Azure com a CORS | Microsoft Docs
description: "Saiba como utilizar o Azure entrega rede conteúdos (CDN) para com-transversal Resource Sharing (CORS)."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 86740a96-4269-4060-aba3-a69f00e6f14e
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 7070397f6e69b21add75bad8220f0b8ebe36d266
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-azure-cdn-with-cors"></a>Utilizar a CDN do Azure com a CORS
## <a name="what-is-cors"></a>O que é CORS?
CORS (cruzada origem partilha de recursos) é uma funcionalidade HTTP que permite uma aplicação web em execução com um domínio aceder a recursos noutro domínio. Para reduzir a possibilidade de ataques de scripts entre sites, todos os browsers modernos web implementam uma restrição de segurança denominada [política da mesma origem](http://www.w3.org/Security/wiki/Same_Origin_Policy).  Isto impede que uma página web a partir de APIs chamadas num domínio diferente.  CORS fornece uma forma segura para permitir que uma origem (o domínio de origem) chamar as APIs na outra origem.

## <a name="how-it-works"></a>Como funciona
Existem dois tipos de pedidos CORS, *pedidos simples* e *pedidos complexos.*

### <a name="for-simple-requests"></a>Para pedidos simples:

1. O browser envia o pedido CORS com mais **origem** cabeçalho de pedido HTTP. O valor do cabeçalho é a origem servido a página principal, que é definida como a combinação de *protocolo,* *domínio,* e *porta.*  Quando uma página de https://www.contoso.com tenta aceder a dados de um utilizador na origem fabrikam.com, o seguinte cabeçalho de pedido seria enviado para fabrikam.com:

   `Origin: https://www.contoso.com`

2. O servidor pode responder com qualquer um dos seguintes:

   * Um **acesso controlo-permitir-origem** cabeçalho na respetiva resposta que indica que o site de origem é permitido. Por exemplo:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Um código de erro HTTP como 403 se o servidor não permitir que o pedido de várias origens após verificar o cabeçalho de origem

   * Um **acesso controlo-permitir-origem** cabeçalho com um caráter universal que permite que todas as origens:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Para pedidos complexos:

Um pedido complexo é um pedido CORS onde o browser é necessária para enviar um *pedido de verificação prévia* (ou seja, uma sonda Preliminar) antes de enviar o pedido CORS real. O pedido de verificação prévia pede-lhe a permissão de servidor se a CORS originais pedido pode continuar e é um `OPTIONS` pedido para o mesmo URL.

> [!TIP]
> Para obter mais detalhes sobre fluxos CORS e pitfalls comuns, veja o [manual para a CORS para REST APIs](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Carateres universais ou cenários de origem única
CORS na CDN do Azure irá funcionar automaticamente sem qualquer configuração adicional quando a **acesso controlo-permitir-origem** cabeçalho está definido como caráter universal (*) ou uma única origem.  A CDN serão colocados em cache a primeira resposta e pedidos subsequentes utilizarão o cabeçalho do mesmo.

Se já tiverem sido efetuados pedidos para a CDN antes de CORS a ser definidos na sua origem, terá de remover o conteúdo no seu conteúdo de ponto final para recarregar o conteúdo com o **acesso controlo-permitir-origem** cabeçalho.

## <a name="multiple-origin-scenarios"></a>Vários cenários de origem
Se precisar de permitir que uma lista específica de origens a permissão para CORS, coisas obtêm um pouco mais complicadas. O problema ocorre quando o CDN coloca em cache o **acesso controlo-permitir-origem** cabeçalho para a origem CORS primeiro.  Quando uma origem CORS diferentes faz um pedido subsequente, a CDN irá servir o em cache **acesso controlo-permitir-origem** cabeçalho, que não corresponde.  Existem várias formas para corrigir isto.

### <a name="azure-cdn-premium-from-verizon"></a>CDN Premium do Azure da Verizon
A melhor forma de ativar esta opção é utilizar **CDN do Azure Premium da Verizon**, que expõe algumas avançada funcionalidade. 

Terá [criar uma regra](cdn-rules-engine.md) para verificar o **origem** cabeçalho no pedido.  Se se tratar de uma origem válida, a regra irá definir o **acesso controlo-permitir-origem** cabeçalho com a origem fornecido no pedido.  Se a origem especificada no **origem** cabeçalho não é permitido, a regra deve omitir o **acesso controlo-permitir-origem** cabeçalho que fará com que o browser rejeitar o pedido. 

Existem duas formas de fazê-lo com o motor de regras.  Em ambos os casos, o **acesso controlo-permitir-origem** cabeçalho do servidor de origem do ficheiro é ignorado completamente, o motor de regras a CDN completamente gere as origens CORS permitidas.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Uma expressão regular com todas as origens válidas
Neste caso, irá criar uma expressão regular que inclui todas as origens que pretende permitir: 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$

> [!TIP]
> **CDN do Azure da Verizon** utiliza [Perl compatível, expressões regulares](http://pcre.org/) como o motor de expressões regulares.  Pode utilizar uma ferramenta como o [101 de expressões regulares](https://regex101.com/) para validar a expressão regular.  Tenha em atenção que o caráter "/" é válido em expressões regulares e não tem de ser caráter de escape correto, no entanto, escape esse caráter é considerada uma melhor prática e é esperada pelo algumas validações regex.
> 
> 

Se corresponder a expressão regular, a regra irá substituir o **acesso controlo-permitir-origem** cabeçalho (se aplicável) da origem com a origem que enviou o pedido.  Também pode adicionar cabeçalhos CORS adicionais, tais como **acesso-controlo-permitir-métodos**.

![Exemplo de regras com a expressão regular](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Regra de cabeçalho de pedido para cada origem.
Em vez de expressões regulares, em vez disso, pode criar uma regra separada para cada origem que pretende permitir utilizando o **universal do cabeçalho de pedido** [correspondem à condição](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_1). Tal como acontece com o método de expressão regular, o motor de regras individualmente define os cabeçalhos CORS. 

![Exemplo de regras sem expressão regular](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> No exemplo acima, a utilização do caráter universal * indica ao motor de regras para corresponder HTTP e HTTPS.
> 
> 

### <a name="azure-cdn-standard"></a>Standard da CDN do Azure
Nos perfis de CDN do Azure Standard, o mecanismo de apenas para permitir a várias origens sem a utilização da origem de caráter universal é utilizar [colocação em cache de cadeia de consulta](cdn-query-string.md).  Terá de ativar a definição da cadeia de consulta para o ponto final CDN e, em seguida, utilizar uma cadeia de consulta exclusivo para pedidos de cada domínio permitido. Efetuar este procedimento resultará na CDN um objeto separado para cada cadeia de consulta exclusivo a colocação em cache. Esta abordagem não é ideal, no entanto, pois irá resultar em várias cópias do mesmo ficheiro colocadas em cache no CDN.  

