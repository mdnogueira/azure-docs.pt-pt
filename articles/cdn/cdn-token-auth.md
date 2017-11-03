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
ms.openlocfilehash: 42b182c314795b1ebf69639ec7ac5583208dc7c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Proteger recursos da CDN do Azure com a autenticação de token

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

##<a name="overview"></a>Descrição geral

Autenticação de token é um mecanismo que permite-lhe impedir que a CDN do Azure que serve ativos para os clientes não autorizados.  Isto é geralmente feito para evitar "hotlinking" do conteúdo, em que um Web site diferente, muitas vezes, uma placa de mensagem, utilize os recursos sem permissão.  Isto pode ter um impacto nos custos de entrega de conteúdos. Ao ativar esta funcionalidade na CDN, os pedidos serão autenticados pelo limite CDN POPs antes de entregar o conteúdo. 

## <a name="how-it-works"></a>Como funciona

Autenticação de token verifica pedidos são gerados por um site fidedigno, exigindo que os pedidos de modo a conter um valor de token que contém codificadas informações sobre o autor do pedido. Conteúdo só será servido ao autor do pedido quando as informações codificadas cumpre os requisitos, caso contrário pedidos serão rejeitados. Pode configurar o requisito utilizando um ou vários parâmetros abaixo.

- País: permitir ou negar pedidos que teve origem países especificados.  [Lista de indicativos de país válido.](https://msdn.microsoft.com/library/mt761717.aspx) 
- URL: permitir apenas ativo especificado ou o caminho do pedido.  
- Anfitrião: permitir ou negar pedidos utilizando anfitriões especificados no cabeçalho do pedido.
- Referência: permitir ou negar a referência especificada para o pedido.
- Endereço IP: permitir apenas pedidos que teve origem do endereço IP específico ou a sub-rede IP.
- Protocolo: permitir ou bloquear pedidos com base no protocolo utilizado para pedir o conteúdo.
- Hora de expiração: atribuir um período de data e hora para se certificar de que uma ligação apenas permanece válida durante um período de tempo limitado.

Consulte o exemplo de configuração detalhadas de cada um dos parâmetros.

## <a name="reference-architecture"></a>Arquitetura de referência

Veja a seguir uma arquitetura de referência de configuração de autenticação com token na CDN para trabalhar com a sua aplicação Web.

![Botão de gerir do painel do perfil da CDN](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Lógica de validação de token no ponto final de CDN
    
Este gráfico descreve a forma como o CDN do Azure valida pedido de cliente quando a autenticação de token está configurada no ponto final de CDN.

![Botão de gerir do painel do perfil da CDN](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Ao configurar a autenticação de token

1. Do [portal do Azure](https://portal.azure.com), navegue para o perfil de CDN e, em seguida, clique o **gerir** botão Iniciar portal suplementar.

    ![Botão de gerir do painel do perfil da CDN](./media/cdn-rules-engine/cdn-manage-btn.png)

2. Coloque o cursor sobre **HTTP grande**e, em seguida, clique em **Token Auth** no flyout. Irá configurar parâmetros de encriptação neste separador e chave de encriptação.

    1. Introduza uma chave de encriptação exclusivo para **chave primária**.  Introduza outra para **chave de cópia de segurança**

        ![Chave de configuração de autenticação de token de CDN](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    2. Configurar parâmetros de encriptação com a ferramenta de encriptação (permitir ou negar pedidos com base no tempo de expiração, país, referência, protocolo, IP de cliente. Pode utilizar qualquer combinação.)

        ![Botão de gerir do painel do perfil da CDN](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

        - EC-expirar: atribui uma hora de expiração de um token após um período de tempo especificado. Pedidos submetidos após a hora de expiração será rejeitada. Este parâmetro utiliza Unix timestamp (em segundos desde a época padrão de 1/1/1970 00:00:00 GMT. Pode utilizar ferramentas online para fornecer a conversão entre a hora padrão e a hora de Unix.)  Por exemplo, se pretender configurar o token para expirar em 31/12/2016 12:00:00 GMT, utilize o tempo de Unix: 1483185600 como abaixo:
    
        ![Botão de gerir do painel do perfil da CDN](./media/cdn-token-auth/cdn-token-auth-expire2.png)
    
        - permitir que o url de EC: permite-lhe personalizar os tokens para um determinado recurso ou um caminho. Restringe o acesso aos pedidos cujo URL começar com um caminho relativo específico. Pode introduzir vários caminhos separando cada caminho com uma vírgula. Os URLs são maiúsculas e minúsculas. Dependendo do requisito, pode configurar o valor diferente para fornecer um nível de acesso diferente. Seguem-se alguns cenários:
        
            Se tiver um URL: http://www.mydomain.com/pictures/city/strasbourg.png. Consulte o valor de entrada "" e o acesso de nível em conformidade

            1. Valor de entrada "/": todos os pedidos serão permitidos
            2. Valor de entrada "/ imagens": todos os pedidos seguintes será permitir
            
                - http://www.mydomain.com/Pictures.png
                - http://www.mydomain.com/Pictures/City/Strasbourg.png
                - http://www.mydomain.com/picturesnew/City/strasbourgh.png
            3. Introduza o valor "imagens /": apenas os pedidos de /pictures/ será permitida
            4. Valor de entrada "/ pictures/city/strasbourg.png": só permite-lhe pedido para este recurso
    
        ![Botão de gerir do painel do perfil da CDN](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
    
        - permitir que o EC-país: apenas permite pedidos provenientes de uma ou mais países especificados. Pedidos provenientes de todos os outros países serão rejeitados. Utilizar o código de país para configurar os parâmetros e separando cada código de país com uma vírgula. Por exemplo, se pretender permitir o acesso dos Estados Unidos e França, de entrada US, FR na coluna como abaixo.  
        
        ![Botão de gerir do painel do perfil da CDN](./media/cdn-token-auth/cdn-token-auth-country-allow.png)

        - Negar EC-país: nega pedidos teve um ou mais países especificados. Serão permitidos pedidos provenientes de todos os outros países. Utilizar o código de país para configurar os parâmetros e separando cada código de país com uma vírgula. Por exemplo, se pretender negar o acesso dos Estados Unidos e França, de entrada US, FR na coluna.
    
        - permitir que o EC-ref: apenas permite pedidos de referência especificada. Uma referência identifica o URL da página web que ligada para o recurso que está a ser solicitado. O valor do parâmetro de referência não deve incluir o protocolo. Pode introduzir um nome de anfitrião e/ou um caminho específico nesse nome de anfitrião. Também pode adicionar vários referrers dentro de um único parâmetro separando cada um com uma vírgula. Se tiver especificado um valor de referência, mas as informações de referência não são enviadas no pedido devido a uma configuração de browser, estes pedidos serão rejeitados por predefinição. Pode atribuir "Em falta" ou um valor em branco no parâmetro para permitir que estes pedidos com informações de referência em falta. Também pode utilizar "*. consoto.com" para permitir que os subdomínios de consoto.com.  Por exemplo, se pretender permitir o acesso para pedidos de www.consoto.com, todos os subdomínios em consoto2.com e erquests com reffers em branco ou em falta, valor de entrada abaixo:
        
        ![Botão de gerir do painel do perfil da CDN](./media/cdn-token-auth/cdn-token-auth-referrer-allow2.png)
    
        - Negar EC-ref: nega pedidos de referência especificada. Consulte os detalhes e exemplo no parâmetro "ec-ref-permitir".
         
        - permitir-EC proto: apenas permite pedidos do protocolo especificado. Por exemplo, http ou https.
        
        ![Botão de gerir do painel do perfil da CDN](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
            
        - Negar-EC proto: nega pedidos de protocolo especificado. Por exemplo, http ou https.
    
        - EC clientip: restringe o acesso ao endereço IP especificada autor do pedido. São suportados IPV4 e IPV6. Pode especificar o endereço IP único pedido ou a sub-rede IP.
            
        ![Botão de gerir do painel do perfil da CDN](./media/cdn-token-auth/cdn-token-auth-clientip.png)
        
    3. Pode testar o seu token com a ferramenta de decription.

    4. Também pode personalizar o tipo de resposta que vai ser devolvido ao utilizador quando o pedido é negado. Por predefinição, podemos utilizar 403.

3. Agora, clique em **motor de regras** separador em **HTTP grande**. Irá utilizar este separador para definir caminhos para aplicar a funcionalidade, ativar a funcionalidade de autenticação de token e ativar a autenticação de token adicional relacionados com capacidades.

    - Utilize a coluna de "Se" para definir o elemento ou o caminho que pretende aplicar a autenticação de token. 
    - Clique para adicionar "Autenticação de Token" na lista pendente funcionalidade para ativar a autenticação de token.
        
    ![Botão de gerir do painel do perfil da CDN](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. No **motor de regras** separador, existem algumas capacidades adicionais, pode ativar.
    
    - Código de recusa de autenticação de token: determina o tipo de resposta que vai ser devolvido ao utilizador quando um pedido é negado. Regras que definir aqui irão substituir a definição de código recusa no separador de token de autenticação.
    - Ignorar a autenticação de token: determina se o URL utilizado para validar o token será sensível às maiúsculas e minúsculas.
    - Parâmetro de token de autenticação: mudar o nome de parâmetro de cadeia de consulta token de autenticação que mostra no URL solicitado. 
        
    ![Botão de gerir do painel do perfil da CDN](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Pode personalizar o seu token que é uma aplicação que gera o token para as funcionalidades de autenticação baseada em tokens. Código de origem pode ser acedido aqui no [GitHub](https://github.com/VerizonDigital/ectoken).
Idiomas disponíveis incluem:
    
    - C
    - C#
    - PHP
    - Perl
    - Java
    - Python    


## <a name="azure-cdn-features-and-provider-pricing"></a>Funcionalidades da CDN do Azure e o fornecedor de preços

Consulte o [descrição geral da CDN](cdn-overview.md) tópico.
