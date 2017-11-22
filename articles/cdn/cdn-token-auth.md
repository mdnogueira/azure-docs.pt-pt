---
title: "Proteger recursos da CDN do Azure com a autenticação de token | Microsoft Docs"
description: "Saiba como utilizar a autenticação de token para proteger o acesso a recursos da CDN do Azure."
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
ms.date: 11/17/2017
ms.author: mezha
ms.openlocfilehash: f6d008a92677d28d0184e64637dcb2e093299519
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2017
---
# <a name="securing-azure-content-delivery-network-assets-with-token-authentication"></a>Proteger recursos de rede de entrega de conteúdos do Azure com a autenticação de token

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Descrição geral

Autenticação de token é um mecanismo que permite-lhe impedir que a rede Azure entrega de conteúdos (CDN) que serve ativos para os clientes não autorizados. Autenticação de token é geralmente feita para evitar "hotlinking" do conteúdo, em que um Web site diferente, tal como uma placa de mensagem, utiliza os recursos sem permissão. Hotlinking pode ter um impacto nos custos de entrega de conteúdos. Ao ativar a autenticação com token na CDN, os pedidos são autenticados pelo servidor de limite CDN antes da CDN oferece o conteúdo. 

## <a name="how-it-works"></a>Como funciona

Autenticação de token verifica que os pedidos são gerados por um site fidedigno, exigindo que os pedidos de modo a conter um valor de token que contém codificado informações sobre o autor do pedido. Conteúdo é fornecido para um autor de pedido apenas se as informações codificadas cumprem os requisitos; caso contrário, os pedidos são negados. Pode configurar os requisitos, utilizando um ou mais dos seguintes parâmetros:

- País: Permitir ou negar pedidos provenientes de países/regiões especificados pelo respetivo [indicativo de país](https://msdn.microsoft.com/library/mt761717.aspx).
- URL: Permitir apenas os pedidos que correspondam a ativo especificado ou o caminho.
- Anfitrião: Permitir ou negar pedidos que utilizam os anfitriões especificados no cabeçalho do pedido.
- Referência: Permitir ou negar o pedido a partir de referência especificada.
- Endereço IP: permitir apenas pedidos que teve origem do endereço IP específico ou a sub-rede IP.
- Protocolo: Permitir ou negar pedidos com base no protocolo utilizado para pedir o conteúdo.
- Hora de expiração: atribuir um período de data e hora para se certificar de que uma ligação permanece válida apenas para um período de tempo limitado.

Para obter mais informações, consulte os exemplos de configuração detalhados para cada parâmetro [ao configurar a autenticação de token](#setting-up-token-authentication).

## <a name="reference-architecture"></a>Arquitetura de referência

O diagrama de fluxo de trabalho seguinte descreve como o CDN utiliza a autenticação de token para trabalhar com a sua aplicação web.

![Fluxo de trabalho de autenticação de token de CDN](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Lógica de validação de token no ponto final de CDN
    
O fluxograma a seguir descreve a forma como o CDN do Azure valida um pedido de cliente quando a autenticação de token está configurada no ponto final da CDN.

![Lógica de validação de token de CDN](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Ao configurar a autenticação de token

1. Do [portal do Azure](https://portal.azure.com), navegue para o perfil de CDN e, em seguida, clique em **gerir** para iniciar o portal suplementar.

    ![Botão de gerir do perfil de CDN](./media/cdn-token-auth/cdn-manage-btn.png)

2. Coloque o cursor sobre **HTTP grande**, em seguida, clique em **Token de autenticação** no flyout. Pode, em seguida, configurar os parâmetros de encriptação e a chave de encriptação da seguinte forma:

    1. Crie uma ou mais chaves de encriptação. Uma chave de encriptação é sensível e pode conter qualquer combinação de carateres alfanuméricos. Quaisquer outros tipos de carateres, incluindo espaços, não são permitidos. O comprimento máximo é 250 caracteres. Para garantir que as chaves de encriptação são aleatórias, é recomendado criar utilizando o [OpenSSL ferramenta](https://www.openssl.org/). 

       A ferramenta de OpenSSL tem a seguinte sintaxe:

       ```rand -hex <key length>```

       Por exemplo:

       ```OpenSSL> rand -hex 32``` 

       Para evitar períodos de indisponibilidade, crie um servidor principal e uma chave de cópia de segurança. Uma chave de cópia de segurança proporciona ininterrupto acesso ao conteúdo, quando está a ser atualizado a sua chave primária.
    
    2. Introduza uma chave de encriptação exclusivo no **chave primária** caixa e, opcionalmente, introduza uma chave de cópia de segurança no **chave de cópia de segurança** caixa.

    3. Selecione a versão mínima de encriptação para cada chave a partir da respetiva **versão mínima de encriptação** lista, em seguida, clique em **atualização**:
       - **V2**: indica que a chave pode ser utilizada para gerar tokens de versão 2.0 e 3.0. Utilize esta opção apenas se estão a mudar a partir de uma chave de encriptação de legado versão 2.0 para uma chave de versão 3.0.
       - **V3**: (recomendado) indica que a chave apenas pode ser utilizada para gerar tokens de versão 3.0.

    ![Chave de configuração de autenticação de token de CDN](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    4. Utilize a ferramenta de encriptar configurar parâmetros de encriptação e de gerar um token. Com a ferramenta de encriptar, pode permitir ou negar pedidos com base no tempo de expiração, o país, o referência, o protocolo e o IP de cliente (em qualquer combinação). Embora não exista nenhum limite para o número e a combinação de parâmetros que podem ser combinados para formar um token, o comprimento total de um token está limitado a 512 caracteres. 

       ![Ferramenta de encriptar de CDN](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

       Introduza valores para uma ou mais dos seguintes parâmetros de encriptação no **encriptar ferramenta** secção: 

       > [!div class="mx-tdCol2BreakAll"] 
       > <table>
       > <tr>
       >   <th>Nome do parâmetro</th> 
       >   <th>Descrição</th>
       > </tr>
       > <tr>
       >    <td><b>ec_expire</b></td>
       >    <td>Atribui um prazo de expiração a um token, após o qual expira o token. Pedidos submetidos após a hora de expiração são negadas. Este parâmetro utiliza um timestamp Unix, o que é baseada no número de segundos desde a época de Unix padrão de `1/1/1970 00:00:00 GMT`. (Pode utilizar ferramentas online a conversão entre a hora padrão e a hora de Unix.)> 
       >    Por exemplo, se pretender que o token para expirarem `12/31/2016 12:00:00 GMT`, introduza o valor de carimbo de Unix `1483185600`. 
       > </tr>
       > <tr>
       >    <td><b>ec_url_allow</b></td> 
       >    <td>Permite-lhe personalizar os tokens para um determinado recurso ou um caminho. Restringe o acesso aos pedidos cujo URL começar com um caminho relativo específico. Os URLs são maiúsculas e minúsculas. Vários caminhos de entrada, separando cada caminho com uma vírgula; Não adicione espaços. Dependendo dos requisitos, pode definir valores diferentes para fornecer um nível de acesso diferente.> 
       >    Por exemplo, para o URL `http://www.mydomain.com/pictures/city/strasbourg.png`, estes pedidos são permitidos para os seguintes valores de entrada: 
       >    <ul>
       >       <li>Valor de entrada `/`: todos os pedidos são permitidos.</li>
       >       <li>Valor de entrada `/pictures`, os seguintes pedidos são permitidos: <ul>
       >          <li>`http://www.mydomain.com/pictures.png`</li>
       >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
       >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
       >       </ul></li>
       >       <li>Valor de entrada `/pictures/`: apenas os pedidos que contém o `/pictures/` caminho são permitidos. Por exemplo, `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
       >       <li>Valor de entrada `/pictures/city/strasbourg.png`: apenas os pedidos para este caminho específico e o recurso são permitidos.</li>
       >    </ul>
       > </tr>
       > <tr>
       >    <td><b>ec_country_allow</b></td> 
       >    <td>Apenas permite pedidos provenientes de uma ou mais países especificados. Pedidos provenientes de todos os outros países são negados. Utilizar uma letra de dois [indicativo de país ISO 3166](https://msdn.microsoft.com/library/mt761717.aspx) para cada país e separe cada um com uma vírgula; não adicione um espaço. Por exemplo, se pretender autorizar o acesso apenas dos Estados Unidos e França, introduza `US,FR`.</td>
       > </tr>
       > <tr>
       >    <td><b>ec_country_deny</b></td> 
       >    <td>Nega pedidos provenientes de uma ou mais países especificados. São permitidos pedidos provenientes de todos os outros países. A implementação é o mesmo que o <b>ec_country_allow</b> parâmetro. Se um código de país está presente em ambos os <b>ec_country_allow</b> e <b>ec_country_deny</b> parâmetros, o <b>ec_country_allow</b> parâmetro tem precedência.</td>
       > </tr>
       > <tr>
       >    <td><b>ec_ref_allow</b></td>
       >    <td>Apenas permite pedidos de referência especificada. Uma referência identifica o URL da página web que está ligada ao recurso que está a ser solicitado. Não inclua o protocolo no valor do parâmetro.>    
       >    Os seguintes tipos de entrada são permitidos:
       >    <ul>
       >       <li>Um nome de anfitrião ou um nome de anfitrião e um caminho.</li>
       >       <li>Vários referrers. Para adicionar vários referrers, separe cada referência com uma vírgula; Não adicione um espaço. Se especificar um valor de referência, mas as informações de referência não são enviadas no pedido devido a configuração do browser, o pedido é negado por predefinição.</li> 
       >       <li>Pedidos com informações de referência em falta ou em branco. Por predefinição, o <b>ec_ref_allow</b> parâmetro bloqueia estes tipos de pedidos. Para permitir que estes pedidos, introduza o texto, "em falta", ou introduza um valor em branco (utilizando uma vírgula decimal).</li> 
       >       <li>Subdomínios. Para permitir que os subdomínios, introduza um asterisco (\*). Por exemplo, para permitir que os subdomínios de `contoso.com`, introduza `*.contoso.com`.</li>
       >    </ul>     
       >    Por exemplo, para permitir o acesso para pedidos de `www.contoso.com`, todos os subdomínios em `contoso2.com`, e pedidos com referrers em branco ou em falta, introduza `www.contoso.com,*.contoso.com,missing`.</td>
       > </tr>
       > <tr> 
       >    <td><b>ec_ref_deny</b></td>
       >    <td>Nega pedidos de referência especificada. A implementação é o mesmo que o <b>ec_ref_allow</b> parâmetro. Se uma referência está presente em ambos os <b>ec_ref_allow</b> e <b>ec_ref_deny</b> parâmetros, o <b>ec_ref_allow</b> parâmetro tem precedência.</td>
       > </tr>
       > <tr> 
       >    <td><b>ec_proto_allow</b></td> 
       >    <td>Apenas permite pedidos do protocolo especificado. Os valores válidos são `http`, `https`, ou `http,https`.</td>
       > </tr>
       > <tr>
       >    <td><b>ec_proto_deny</b></td>
       >    <td>Nega pedidos do protocolo especificado. A implementação é o mesmo que o <b>ec_proto_allow</b> parâmetro. Se um protocolo está presente em ambos os <b>ec_proto_allow</b> e <b>ec_proto_deny</b> parâmetros, o <b>ec_proto_allow</b> parâmetro tem precedência.</td>
       > </tr>
       > <tr>
       >    <td><b>ec_clientip</b></td>
       >    <td>Restringe o acesso ao endereço IP do especificado autor do pedido. São suportados IPV4 e IPV6. Pode especificar um endereço IP único pedido ou endereços IP associados uma sub-rede específica. Por exemplo, `11.22.33.0/22` permite que os pedidos de endereços IP 11.22.32.1 para 11.22.35.254.</td>
       > </tr>
       > </table>

    5. Depois de concluída a introduzir os valores de parâmetro de encriptação, selecione uma chave para encriptar (se tiver criado um site primário e uma chave de cópia de segurança) a **chave para encriptar** lista.
    
    6. Selecione uma versão de encriptação do **encriptação versão** lista: **V2** para versão 2 ou **V3** para a versão 3 (recomendado). 

    7. Clique em **encriptar** para gerar o token.

    Depois do token é gerado, é apresentada no **gerados tokens** caixa. Para utilizar o token, anexe como uma cadeia de consulta para o fim do ficheiro no seu caminho de URL. Por exemplo, `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
    8. Opcionalmente, pode teste o seu token com a ferramenta de desencriptação para que possa visualizar os parâmetros do seu token. Colar o valor do token no **Token para desencriptar** caixa. Selecionar a chave de encriptação a utilizar o **chave para desencriptar** lista, em seguida, clique em **desencriptar**.

    Depois do token é desencriptado, os parâmetros são apresentados no **parâmetros Original** caixa.

    9. Opcionalmente, personalize o tipo de código de resposta que é devolvido quando um pedido é negado. Selecione **ativado**, em seguida, selecione o código de resposta a **código de resposta** lista. **O nome do cabeçalho** é automaticamente definido para **localização**. Clique em **guardar** para implementar o novo código de resposta. Alguns códigos de resposta, também tem de introduzir o URL da sua página de erro no **valor do cabeçalho** caixa. O **403** código de resposta (proibido) está selecionado por predefinição. 

3. Em **HTTP grande**, clique em **motor de regras**. Utilize o motor de regras para definir caminhos para aplicar a funcionalidade, ativar a funcionalidade de autenticação de token e ativar funcionalidades relacionadas com autenticação de token adicionais. Para obter mais informações, consulte [regras motor referência](cdn-rules-engine-reference.md).

    1. Selecione uma regra existente ou crie uma nova regra para definir o elemento ou o caminho para o qual pretende aplicar a autenticação de token. 
    2. Para ativar a autenticação com token numa regra, selecione  **[Token Auth](cdn-rules-engine-reference-features.md#token-auth)**  do **funcionalidades** lista, em seguida, selecione **ativado**. Clique em **atualização** se estão a atualizar uma regra ou **adicionar** se estiver a criar uma regra.
        
    ![Exemplo de ativar autenticação de token do motor de regras CDN](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. O motor de regras, também pode ativar funcionalidades relacionadas com a autenticação de token adicionais. Para ativar qualquer uma das seguintes funcionalidades, selecione-à partir de **funcionalidades** lista, em seguida, selecione **ativado**.
    
    - **[Código de recusa de autenticação de token](cdn-rules-engine-reference-features.md#token-auth-denial-code)**: determina o tipo de resposta é devolvida a um utilizador quando um pedido é negado. Substituição de regras que definir aqui o código de resposta definido **processamento de recusa personalizado** secção na página de autenticação baseada em tokens.
    - **[Token de autenticação ignorar URL caso](cdn-rules-engine-reference-features.md#token-auth-ignore-url-case)**: determina se o URL utilizado para validar o token de maiúsculas e minúsculas.
    - **[Parâmetro de autenticação de token](cdn-rules-engine-reference-features.md#token-auth-parameter)**: mudar o parâmetro de cadeia de consulta de token de autenticação que é apresentado no URL solicitado. 
        
    ![Exemplo de definições de autenticação de token do motor de regras CDN](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Pode personalizar o seu token ao aceder ao código de origem no [GitHub](https://github.com/VerizonDigital/ectoken).
Idiomas disponíveis incluem:
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python 

## <a name="azure-cdn-features-and-provider-pricing"></a>Funcionalidades da CDN do Azure e o fornecedor de preços

Para obter informações sobre as funcionalidades, consulte [descrição geral da CDN](cdn-overview.md). Para obter informações sobre preços, consulte [preços de rede de entrega de conteúdos](https://azure.microsoft.com/pricing/details/cdn/).
