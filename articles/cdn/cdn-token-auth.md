---
title: "Proteger recursos da CDN do Azure com a autenticação de token | Microsoft Docs"
description: "Utilizar a autenticação de token para proteger o acesso a recursos da CDN do Azure."
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: 
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/11/2016
ms.author: mezha
ms.openlocfilehash: b1bcaf14e9805afa82c765092b62201f58c7cbc4
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="securing-azure-content-delivery-network-assets-with-token-authentication"></a>Proteger recursos de rede de entrega de conteúdos do Azure com a autenticação de token

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Descrição geral

Autenticação de token é um mecanismo que permite-lhe impedir que a rede Azure entrega de conteúdos (CDN) que serve ativos para os clientes não autorizados. Autenticação de token é geralmente feita para evitar "hotlinking" do conteúdo, em que um Web site diferente, muitas vezes, uma placa de mensagem, utiliza os recursos sem permissão. Hotlinking pode ter um impacto nos custos de entrega de conteúdos. Ao ativar esta funcionalidade na CDN, os pedidos são autenticados pelo limite CDN POPs antes da CDN oferece o conteúdo. 

## <a name="how-it-works"></a>Como funciona

Autenticação de token verifica pedidos são gerados por um site fidedigno, exigindo que os pedidos de modo a conter um valor de token que contém codificado sobre o autor do pedido. Conteúdo é fornecido para um autor de pedido apenas se as informações codificadas cumprem os requisitos; caso contrário, os pedidos são negados. Pode configurar os requisitos, utilizando um ou mais dos seguintes parâmetros:

- País: Permitir ou negar pedidos provenientes de países/regiões especificados pelo respetivo [indicativo de país](https://msdn.microsoft.com/library/mt761717.aspx).
- URL: Permitir apenas os pedidos que correspondam a ativo especificado ou o caminho.
- Anfitrião: Permitir ou negar pedidos que utilizam os anfitriões especificados no cabeçalho do pedido.
- Referência: Permitir ou negar o pedido a partir de referência especificada.
- Endereço IP: permitir apenas pedidos que teve origem do endereço IP específico ou a sub-rede IP.
- Protocolo: Permitir ou negar pedidos com base no protocolo utilizado para pedir o conteúdo.
- Hora de expiração: atribuir um período de data e hora para se certificar de que uma ligação permanece válida apenas para um período de tempo limitado.

Para obter mais informações, consulte os exemplos de configuração detalhados para cada parâmetro [ao configurar a autenticação de token](#setting-up-token-authentication).

Depois de gerar um token encriptado, acrescentá-la como uma cadeia de consulta ao fim do caminho de URL de ficheiro. Por exemplo, `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.

## <a name="reference-architecture"></a>Arquitetura de referência

O diagrama de fluxo de trabalho seguinte descreve como o CDN utiliza a autenticação de token para trabalhar com a sua aplicação web.

![Fluxo de trabalho de autenticação de token de CDN](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Lógica de validação de token no ponto final de CDN
    
O fluxograma a seguir descreve a forma como o CDN do Azure valida pedido de cliente quando a autenticação de token está configurada no ponto final de CDN.

![Lógica de validação de token de CDN](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Ao configurar a autenticação de token

1. Do [portal do Azure](https://portal.azure.com), navegue para o perfil de CDN e, em seguida, clique em **gerir** para iniciar o portal suplementar.

    ![Botão de gerir do perfil de CDN](./media/cdn-rules-engine/cdn-manage-btn.png)

2. Coloque o cursor sobre **HTTP grande**e, em seguida, clique em **Token Auth** no flyout. Configurar os parâmetros de encriptação neste separador e chave de encriptação.

    1. Introduza uma chave de encriptação exclusivo para **chave primária** e introduza outra para **chave de cópia de segurança**.

        ![Chave de configuração de autenticação de token de CDN](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    2. Configure parâmetros de encriptação com a ferramenta de encriptar. Com a ferramenta de encriptar, pode permitir ou negar pedidos com base no tempo de expiração, o país, o referência, o protocolo e o IP de cliente (em qualquer combinação).

        ![Ferramenta de encriptar de CDN](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

       - ec_expire: atribui uma hora de expiração a um token, após o qual expira o token. Pedidos submetidos após a hora de expiração são negadas. Este parâmetro utiliza um timestamp Unix, o que é baseada no número de segundos desde a época padrão de `1/1/1970 00:00:00 GMT`. (Pode utilizar ferramentas online a conversão entre a hora padrão e a hora de Unix.) Por exemplo, se pretender configurar o token para expirarem `12/31/2016 12:00:00 GMT`, utilize o valor de carimbo de Unix `1483185600`, da seguinte forma. 
    
         ![Exemplo de ec_expire CDN](./media/cdn-token-auth/cdn-token-auth-expire2.png)
    
       - ec_url_allow: permite-lhe personalizar os tokens para um determinado recurso ou um caminho. Restringe o acesso aos pedidos cujo URL começar com um caminho relativo específico. Os URLs são maiúsculas e minúsculas. Vários caminhos de entrada, separando cada caminho com uma vírgula. Dependendo dos requisitos, pode definir valores diferentes para fornecer um nível de acesso diferente. 
        
          Por exemplo, para o URL `http://www.mydomain.com/pictures/city/strasbourg.png`, defina o valor de entrada e o respetivo nível de acesso da seguinte forma:

           - Valor de entrada `"/"`: todos os pedidos são permitidos
           - Valor de entrada `"/pictures`. São permitidos os seguintes pedidos de:
              - `http://www.mydomain.com/pictures.png`
              - `http://www.mydomain.com/pictures/city/strasbourg.png`
              - `http://www.mydomain.com/picturesnew/city/strasbourgh.png`
            - Valor de entrada `/pictures/`: apenas pedidos de `/pictures/` são permitidos. Por exemplo, `http://www.mydomain.com/pictures/city/strasbourg.png`.
            - Valor de entrada `/pictures/city/strasbourg.png`: são permitidos apenas pedidos para este recurso específico.
    
          ![Exemplo de ec_url_allow CDN](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
    
       - ec_country_allow: apenas permite pedidos provenientes de uma ou mais países especificados. Pedidos provenientes de todos os outros países são negados. Utilizar indicativos de país e separe cada um com uma vírgula. Por exemplo, se pretender autorizar o acesso apenas dos Estados Unidos e França, introduza-nos, FR no campo da seguinte forma.  
        
           ![Exemplo de ec_country_allow CDN](./media/cdn-token-auth/cdn-token-auth-country-allow.png)

       - ec_country_deny: nega pedidos provenientes de uma ou mais países especificados. São permitidos pedidos provenientes de todos os outros países. Utilizar indicativos de país e separe cada um com uma vírgula. Por exemplo, se pretender negar o acesso dos Estados Unidos e França, de entrada US, FR no campo.
    
       - ec_ref_allow: apenas permite pedidos de referência especificada. Uma referência identifica o URL da página web que está ligada ao recurso que está a ser solicitado. Não inclua o protocolo no valor de parâmetro de referência. Os seguintes tipos de entrada são permitidos para o valor do parâmetro:
           - Um nome de anfitrião ou um nome de anfitrião e um caminho.
           - Vários referrers. Para adicionar vários referrers, separe cada referência com uma vírgula. Se especificar um valor de referência, mas as informações de referência não são enviadas no pedido devido a configuração do browser, esses pedidos são negados por predefinição. 
           - Pedidos com informações de referência em falta. Para permitir que estes tipos de pedidos, introduza o texto "em falta" ou introduza um valor em branco. 
           - Subdomínios. Para permitir que os subdomínios, introduza um asterisco (*). Por exemplo, para permitir que os subdomínios de `consoto.com` introduza `*.consoto.com`. 
           
          O exemplo seguinte mostra a entrada para permitir o acesso para pedidos de `www.consoto.com`, todos os subdomínios em `consoto2.com`e os pedidos com referrers em branco ou em falta.
        
          ![Exemplo de ec_ref_allow CDN](./media/cdn-token-auth/cdn-token-auth-referrer-allow2.png)
    
       - ec_ref_deny: nega pedidos de referência especificada. A implementação é o mesmo que o parâmetro ec_ref_allow.
         
       - ec_proto_allow: apenas permite pedidos do protocolo especificado. Por exemplo, HTTP ou HTTPS.
        
         ![Exemplo de ec_proto_allow CDN](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
            
       - ec_proto_deny: nega pedidos do protocolo especificado. Por exemplo, HTTP ou HTTPS.
    
       - ec_clientip: restringe o acesso ao endereço IP do especificado autor do pedido. São suportados IPV4 e IPV6. Pode especificar um endereço IP único pedido ou uma sub-rede IP.
            
         ![Exemplo de ec_clientip CDN](./media/cdn-token-auth/cdn-token-auth-clientip.png)
        
    3. Opcionalmente, pode teste o seu token com a ferramenta de descrição.

    4. Opcionalmente, personalize o tipo de resposta que é devolvido quando um pedido é negado. Por predefinição, é utilizado o código de resposta 403.

3. Em **HTTP grande**, clique em **motor de regras**. Utilize o motor de regras para definir caminhos para aplicar a funcionalidade, ativar a funcionalidade de autenticação de token e ativar funcionalidades relacionadas com autenticação de token adicionais.

    1. Utilize o **se** coluna para definir o elemento ou o caminho para o qual pretende aplicar a autenticação de token. 
    2. Para ativar a autenticação com token, selecione **Token Auth** do **funcionalidades** pendente.
        
    ![Exemplo de ativar autenticação de token do motor de regras CDN](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. O motor de regras, existem algumas capacidades adicionais, que pode ativar:
    
    - **Código de recusa de autenticação de token**: determina o tipo de resposta é devolvida a um utilizador quando um pedido é negado. As regras que definir aqui substituem a definição de código de recusa no **Token Auth** separador.
    - **Token de autenticação ignorar URL caso**: determina se o URL utilizado para validar o token de maiúsculas e minúsculas.
    - **Parâmetro de autenticação de token**: mudar o parâmetro de cadeia de consulta de token de autenticação que é apresentado no URL solicitado. 
        
    ![Exemplo de definições de autenticação de token do motor de regras CDN](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Pode personalizar o seu token, o que é uma aplicação que gera o token para as funcionalidades de autenticação baseada em tokens. Código de origem pode ser acedido no [GitHub](https://github.com/VerizonDigital/ectoken).
Idiomas disponíveis incluem:
    
    - C
    - C#
    - PHP
    - Perl
    - Java
    - Python    


## <a name="azure-cdn-features-and-provider-pricing"></a>Funcionalidades da CDN do Azure e o fornecedor de preços

Para informações, consulte [descrição geral da CDN](cdn-overview.md).
