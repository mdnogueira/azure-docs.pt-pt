---
title: "CENC com múltipla DRM e controlo de acesso: uma estrutura de referência e a implementação no Azure e Azure dos Media Services | Microsoft Docs"
description: "Saiba mais sobre como ao licenciamento do Microsoft® uniforme de transmissão em fluxo cliente transferências Kit."
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.assetid: 7814739b-cea9-4b9b-8370-538702e5c615
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: willzhan;kilroyh;yanmf;juliako
ms.openlocfilehash: e4a53d053a4c792f54e215c19a8f0c4064815839
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="cenc-with-multi-drm-and-access-control-a-reference-design-and-implementation-on-azure-and-azure-media-services"></a>CENC com Controlo de Acesso e Multi-DRM: Uma Estrutura de Referência e Implementação no Azure e Serviços de Multimédia do Azure
 
## <a name="introduction"></a>Introdução
É bem conhecidos, que é uma tarefa complexa para conceber e criar um subsistema DRM para um OTT ou online de solução de transmissão em fluxo. E é uma prática comum para fornecedores de vídeo operadores online outsource esta peça para fornecedores de serviços DRM especializados. O objetivo deste documento é apresentar uma estrutura de referência e a implementação de subsistema de DRM ponto-a-ponto OTT ou solução de transmissão em fluxo online.

Os leitores alvo deste documento são engenheiros de trabalhar no subsistema DRM de OTT ou transmissão em fluxo/várias-screen soluções online ou qualquer leitores interessados no subsistema DRM. Pressuposto é que os leitores encontram familiarizados com pelo menos uma das tecnologias DRM no mercado, tais como PlayReady, Widevine, do FairPlay ou Adobe acesso.

Por DRM, iremos também incluem CENC (encriptação comum) com múltipla DRM. Uma tendência principais na transmissão em fluxo online e do setor OTT consiste em utilizar CENC com várias-native-DRM em várias plataformas de cliente, que é um bit da tendência anterior da utilização de um único DRM e o cliente SDK em várias plataformas de cliente. Quando utilizar CENC com várias native DRM tanto PlayReady como Widevine são encriptados de acordo com o [encriptação comum (ISO/IEC 23001 7 CENC)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) especificação.

Os benefícios da CENC com múltipla DRM são da seguinte forma:

1. Reduz o custo de uma vez que o processamento de encriptação único é utilizado direcionada para plataformas diferentes com o respetivos DRMs nativos; de encriptação
2. Reduz o custo de gerir recursos encriptados, uma vez que é necessária apenas uma única cópia dos recursos encriptadas;
3. Elimina o custo de licenciamento, uma vez que o cliente DRM nativo é normalmente livre na respetiva plataforma nativa de clientes DRM.

Microsoft foi um promoter Active Directory de DASH e CENC juntamente com alguns jogadores principais da indústria. Serviços de suporte de dados do Microsoft Azure tem sido fornecer suporte de DASH e CENC. Para anúncios recentes, consulte blogues do Mingfei: [serviços de entrega de licença de anunciar Widevine da Google nos Media Services do Azure](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/), e [empacotamento Widevine da Google para entrega de adiciona de Media Services do Azure fluxo de múltipla DRM](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).  

### <a name="overview-of-this-article"></a>Descrição geral deste artigo
O objetivo deste artigo inclui o seguinte:

1. Fornece uma conceção de referência do subsistema DRM utilizando CENC com múltipla DRM;
2. Fornece uma implementação de referência na plataforma de Media Services do Microsoft Azure/Azure;
3. Descreve alguns tópicos de design e implementação.

O artigo "múltipla DRM" abrange o seguinte:

1. Microsoft PlayReady
2. Widevine da Google
3. Apple FairPlay 

A tabela seguinte resume a aplicação de plataforma nativo/nativo e browsers suportados por cada DRM.

| **Plataforma de cliente** | **Suporte nativo DRM** | **Browser/aplicação** | **Formatos de transmissão em fluxo** |
| --- | --- | --- | --- |
| **TVs inteligentes, o operador STBs, OTT STBs** |PlayReady principalmente, e/ou Widevine e/ou outras |Linux, Opera, WebKit, outros |Vários formatos |
| **Dispositivos Windows 10 (Windows PC, Windows Tablets, Windows Phone, Xbox)** |PlayReady |MS Edge/IE11/EME<br/><br/><br/>UWP |TRAÇO (para HLS, PlayReady não é suportado)<br/><br/>DASH, transmissão em fluxo uniforme (para HLS, PlayReady não é suportado) |
| **Dispositivos Android (telemóvel, Tablet, TV)** |Widevine |Chrome/EME |DASH, HLS |
| **iOS (iPhone, iPad), os clientes dos X e Apple TV** |FairPlay |Safari 8 + /Safari/Chrome EME |HLS |


Tendo em consideração o estado atual da implementação para cada DRM, um serviço normalmente, irá querer DRMs 2 ou 3 para se certificar de que todos os tipos de pontos finais de endereços da melhor forma de implementar.

Não há um compromisso entre a complexidade da lógica de serviço e a complexidade do lado do cliente ao atingir um determinado nível de experiência de utilizador nos vários clientes.

Para tornar a sua seleção, tenha em atenção estes factos:

* PlayReady nativamente está implementado em cada dispositivo de Windows, em alguns dispositivos Android e disponível através do software SDKs em praticamente qualquer plataforma
* Widevine nativamente é implementada em todos os dispositivos Android, no Chrome e outros dispositivos
* FairPlay está disponível apenas em iOS e os clientes Mac OS ou através do iTunes.

Por isso, seria um múltipla DRM típica:

* Opção 1: PlayReady e Widevine
* Opção 2: PlayReady, Widevine e FairPlay

## <a name="a-reference-design"></a>Um design de referência
Nesta secção, iremos irá apresentar um design de referência que é agnóstico relativamente à tecnologias utilizadas para implementá-las.

Um subsistema DRM pode conter os seguintes componentes:

1. Gestão de chaves
2. Empacotamento DRM
3. Entrega de licença de DRM
4. Verificação de elegibilidade
5. Autenticação/autorização
6. Leitor
7. Origem/CDN

O diagrama seguinte ilustra a interação entre os componentes de um subsistema DRM alto nível.

![Subsistema com CENC DRM](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

Existem três básicas "camadas" na estrutura:

1. Camada de back-office (em negra) que não são expostos externamente;
2. Camada de "DMZ" (azul) que contém todos os pontos finais destinado ao público;
3. Camada da Internet de pública (blue leve) que contém o CDN e jogadores com o tráfego através da Internet pública.

Deverá haver uma ferramenta de gestão de conteúdo para controlar a proteção de DRM, independentemente de já é encriptação estática ou dinâmica. As entradas para a encriptação de DRM devem incluir:

1. Conteúdo de vídeo MBR;
2. Chave de conteúdo;
3. URL de aquisição de licença.

Durante o período de reprodução, o fluxo de alto nível é:

1. Utilizador é autenticado;
2. O token de autorização é criado para o utilizador;
3. Conteúdo DRM protegido (manifesto) é transferido para o leitor;
4. Leitor submete pedidos de aquisição de licença para servidores de licenças, juntamente com o ID de chave e a autorização token.

Antes de mover para o tópico seguinte, algumas palavras sobre a conceção da gestão de chaves.

| **ContentKey – ativo** | **Cenário** |
| --- | --- |
| 1 – 1 |O cenário mais simples. Fornece o controlo finest. Mas, geralmente, como resultado, o custo de entrega de licença mais elevado. No mínimo licença de um pedido é necessário para cada recurso protegido. |
| 1-para-muitos |Pode utilizar a mesma chave de conteúdo para vários recursos. Por exemplo, para todos os recursos num grupo lógico como um género ou um subconjunto do género (ou Gene de filmes), pode utilizar uma única chave de conteúdo. |
| Muitos-para-1 |Várias chaves de conteúdo são necessárias para cada recurso. <br/><br/>Por exemplo, se precisar de aplicar dinâmica proteção CENC com múltipla DRM para MPEG-DASH e encriptação AES-128 dinâmica para HLS, terá dois separadas conteúdas chaves, cada um com o seu próprio ContentKeyType. (Para a chave de conteúdo utilizada para proteção de CENC dinâmica, ContentKeyType.CommonEncryption deve ser utilizado, enquanto para a chave de conteúdo utilizada para encriptação AES-128 dinâmica, deve ser utilizada ContentKeyType.EnvelopeEncryption.)<br/><br/>Noutro exemplo, na proteção CENC do conteúdo DASH, em teoria, uma chave de conteúdo pode ser utilizado para proteger o fluxo de vídeo e outro chave de conteúdo para proteger a sequência de áudio. |
| Muitos-para - muitos |Combinação dos dois cenários acima: um conjunto de chaves de conteúdo são utilizadas para cada um dos vários recursos no mesmo elemento "grupo de". |

Outro fator importante a ter em consideração é a utilização de licenças persistentes e não persistente.

Por que razão são estas considerações importantes?

Têm um impacto direto custo de entrega de licença, se utilizar a nuvem pública para a entrega de licença. Vamos, considere os seguintes dois casos de design diferentes para ilustrar:

1. Uma subscrição mensal: utilizar licença persistente e mapeamento de recurso de chave de conteúdo de 1-para-muitos. Por exemplo, para todos os filmes menores, utilizamos uma única chave de conteúdo para a encriptação. Neste caso:

    Total de licenças de # pedidas para todos os menores filmes/dispositivo = 1
2. Uma subscrição mensal: utilizar a licença não persistentes e mapeamento de 1 para 1 entre os recursos e a chave de conteúdo. Neste caso:

    Total de licenças de # pedidas para todos os menores filmes/dispositivo = [observadas de filmes #] x [sessões #]

Como pode facilmente, das diferentes dois estruturas resultam em padrões de pedido de licença muito diferentes, por conseguinte, se o serviço de entrega de licença é fornecido por uma nuvem pública como os serviços de suporte de dados do Azure de custo de entrega de licença.

## <a name="mapping-design-to-technology-for-implementation"></a>Estrutura de mapeamento para a tecnologia de implementação
Em seguida, iremos mapear nosso estrutura genérica para tecnologias na plataforma de Media Services do Microsoft Azure/Azure, especificando que tecnologia a utilizar para cada bloco modular.

A tabela seguinte mostra o mapeamento de:

| **Bloco modular** | **Tecnologia** |
| --- | --- |
| **Leitor** |[Media Player do Azure](https://azure.microsoft.com/services/media-services/media-player/) |
| **Fornecedor de identidade (IDP)** |Azure Active Directory |
| **Serviço de Token seguro (STS)** |Azure Active Directory |
| **Fluxo de trabalho de proteção de DRM** |Proteção dinâmica de serviços de multimédia do Azure |
| **Entrega de licença DRM** |1. Licença entrega (PlayReady, Widevine, do FairPlay), de serviços de multimédia do Azure ou <br/>2. Servidor de licenças de Axinom, <br/>3. Servidor de licenças PlayReady personalizado |
| **Origem** |Ponto final de transmissão em fluxo de serviços de multimédia do Azure |
| **Gestão de chaves** |Não é necessária para a implementação de referência |
| **Gestão de conteúdo** |Uma aplicação de consola c# |

Por outras palavras, o fornecedor de identidade (IDP) e proteger o Token serviço (STS) será do Azure AD. Para o leitor, utilizamos [API do Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). Os Media Services do Azure e o leitor de multimédia do Azure suportam DASH e CENC com múltipla DRM.

O diagrama seguinte mostra a estrutura e fluxo com o mapeamento de tecnologia acima geral.

![CENC no AMS](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Para configurar a encriptação de CENC dinâmica, a ferramenta de gestão de conteúdo irá utilizar as seguintes entradas:

1. Abrir conteúdo;
2. Chave de conteúdo da chave geração/gestão;
3. URL de aquisição de licença;
4. Uma lista de informações do Azure AD.

O resultado da ferramenta de gestão de conteúdo será:

1. ContentKeyAuthorizationPolicy que contém a especificação na forma como a entrega de licença verifica um token JWT e especificações de licença da DRM;
2. AssetDeliveryPolicy que contém as especificações na transmissão em fluxo formato, proteção DRM e URLs de aquisição de licença.

Durante o tempo de execução, o fluxo está abaixo:

1. Após a autenticação de utilizador, é gerado um token JWT;
2. Uma das afirmações incluídas no JWT token é afirmação "grupos" que contém o ID de objeto do grupo de "EntitledUserGroup". Esta afirmação será utilizada para transmitir "verificar a elegibilidade".
3. Manifesto de cliente de transferências de leitor de um CENC conteúdo protegido e "verá" o seguinte:

   1. ID da chave
   2. o conteúdo é CENC protegido,
   3. URL de aquisição de licença.
4. Leitor faz um pedido de aquisição de licença com base no browser/DRM suportado. No pedido de aquisição de licença, ID de chave e o token JWT também irá ser submetido. Serviço de entrega de licença irá verificar o token JWT e afirmações incluídas antes de emitir a licença necessária.

## <a name="implementation"></a>Implementação
### <a name="implementation-procedures"></a>Procedimentos de implementação
A implementação irá incluir os seguintes passos:

1. Preparar o teste asset(s): codificar/pacote um vídeo de teste para transmissão múltipla fragmentada MP4 nos Media Services do Azure. Este elemento não é DRM protegido. Proteção de DRM será efetuada pela proteção dinâmica mais tarde.
2. Criar chave ID e conteúdo chave (e opcionalmente de seed chave). Para o nosso objetivo, sistema de gestão de chaves não é necessária uma vez que vamos são lidar com apenas um único conjunto de ID e a chave de conteúdo para alguns dos recursos de teste; da chave
3. Utilize a API do AMS para configurar os serviços de entrega de licença de múltipla DRM para o recurso de teste. Se estiver a utilizar servidores de licenças personalizados pela sua empresa ou fornecedores da sua empresa em vez dos serviços de licenciamento no Media Services do Azure, pode ignorar este passo e especificar os URLs de aquisição de licença no passo para configurar a entrega de licença. API de AMS é necessário especificar detalhadas algumas configurações, tais como restrição de política de autorização, modelos de resposta de licença para diferentes serviços de licença da DRM, etc. Neste momento, o portal do Azure não ainda fornecem a IU necessária para esta configuração. Pode encontrar informações de nível de API e exemplo de código no documento de Leonor Kornich: [utilizando PlayReady e/ou Widevine a encriptação comum dinâmica](media-services-protect-with-drm.md).
4. Utilize a API de AMS para configurar a política de entrega de elemento para o recurso de teste. Pode encontrar informações de nível de API e exemplo de código no documento de Leonor Kornich: [utilizando PlayReady e/ou Widevine a encriptação comum dinâmica](media-services-protect-with-drm.md).
5. Criar e configurar um inquilino do Azure Active Directory no Azure;
6. Crie algumas contas de utilizador e grupos no seu inquilino do Azure Active Directory: deve criar, pelo menos, "EntitledUser" de grupo e adicionar um utilizador a este grupo. Os utilizadores deste grupo serão passaram a verificação de elegibilidade na aquisição de licença e não os utilizadores deste grupo não conseguirão passaram a verificação de autenticação e não será possível adquirir qualquer licença. Facto de ser membro deste grupo de "EntitledUser" é uma afirmação de necessário "nos grupos" no token JWT emitido pelo Azure AD. Este requisito de afirmação deve ser especificado na configuração de passo de serviços de entrega de licença de múltipla DRM.
7. Crie uma aplicação ASP.NET MVC que irá alojar o leitor de vídeo. Esta aplicação ASP.NET vai ser protegida com autenticação de utilizador no inquilino do Azure Active Directory. Os tokens de acesso obtidos após a autenticação de utilizador serão incluídas afirmações adequadas. Para este passo, recomenda-se OpenID Connect API. Terá de instalar os pacotes de NuGet seguintes:
   * Pacote de instalação Microsoft.Azure.ActiveDirectory.GraphClient
   * Pacote de instalação Microsoft.Owin.Security.OpenIdConnect
   * Pacote de instalação Microsoft.Owin.Security.Cookies
   * Pacote de instalação Microsoft.Owin.Host.SystemWeb
   * Pacote de instalação Microsoft.IdentityModel.Clients.ActiveDirectory
8. Criar um leitor utilizando [API do Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). [ProtectionInfo API do Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/) permite-lhe especificar que tecnologia DRM a utilizar em várias plataformas DRM.
9. Matriz de teste:

| **DRM** | **Browser** | **Resultado para o utilizador realizada** | **Resultado para o utilizador anular realizada** |
| --- | --- | --- | --- |
| **PlayReady** |Limite de MS ou IE11 no Windows 10 |Concluída com êxito |Falhar |
| **Widevine** |Chrome no Windows 10 |Concluída com êxito |Falhar |
| **FairPlay** |TBD | | |

George Trifonov da equipa de serviços de suporte de dados do Azure tem de escrever um blogue fornecendo passos de detalhado na configuração do Azure Active Directory para uma aplicação de leitor de ASP.NET MVC: [integrar o Azure suporte de dados de serviços da OWIN MVC baseada em aplicações com o Azure Active Directory e restringir conteúdo de entrega de chave com base em afirmações JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

George também ter escrito um blogue no [autenticação de token JWT nos Media Services do Azure e a encriptação dinâmica](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

Para informações sobre o Azure Active Directory:

* Pode encontrar informações de Programador no [guia para programadores Directory Active do Azure](../active-directory/active-directory-developers-guide.md).
* Pode encontrar informações de administrador no [administrar o diretório do Azure AD](../active-directory/active-directory-administer.md).

### <a name="some-gotchas-in-implementation"></a>Alguns gotchas numa implementação
Existem algumas "gotchas" na implementação. Hopefully na seguinte lista de "gotchas" pode ajudá-lo caso se depare com problemas de resolução de problemas.

1. **Emissor** URL deve terminar com **"/"**.  

    **Público-alvo** deve ser o ID de cliente da aplicação de leitor e deverá ainda adicionar **"/"** no final do URL do emissor.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    No [JWT descodificador](http://jwt.calebb.net/), deverá ver **aud** e **iss** como abaixo no JWT token:

    ![gotcha 1ª](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)
2. Adicione permissões para a aplicação no AAD (no separador de configuração da aplicação). Isto é necessário para cada aplicação (versões de locais e implementadas).

    ![2nd gotcha](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)
3. Utilize o emissor direita em configurar a proteção de CENC dinâmica:

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    O seguinte não irá funcionar:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    O GUID é o ID de inquilino do AAD. O GUID pode ser encontrado no pop-up de pontos finais no portal do Azure.
4. Associação ao grupo de conceder privilégios de afirmações. Certifique-se no ficheiro de manifesto de aplicação do AAD, temos o seguinte

    "groupMembershipClaims": "All", (o valor predefinido é nulo)
5. Definição adequada TokenType ao criar requisitos de restrição.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Desde a adição de suporte de JWT (AAD) para além de SWT (ACS), a predefinição TokenType é TokenType.JWT. Se utilizar SWT/ACS, tem de definir como TokenType.SWT.

## <a name="additional-topics-for-implementation"></a>Tópicos adicionais para a implementação
Em seguida, irá detetar uss alguns tópicos adicionais no nosso design e implementação.

### <a name="http-or-https"></a>HTTP ou HTTPS?
A aplicação de leitor de ASP.NET MVC criámos têm de suportar o seguinte:

1. Autenticação de utilizador através do Azure AD que tem de estar em HTTPS;
2. Troca de tokens JWT entre cliente e o Azure AD que tem de estar em HTTPS;
3. Aquisição de licença da DRM pelo cliente que é necessário para estar em HTTPS se a entrega de licença é fornecida pelos serviços de suporte de dados do Azure. Obviamente, PlayReady suite de produto não mandatar HTTPS para entrega de licença. Se o servidor de licenças PlayReady está fora do Media Services do Azure, pode ser utilizado por HTTP ou HTTPS.

Por conseguinte, a aplicação de leitor ASP.NET utilizará HTTPS como melhor prática. Isto significa que o leitor de multimédia do Azure irá encontrar numa página em HTTPS. No entanto, para transmissão em fluxo preferir HTTP, por conseguinte, é necessário considerar problema conteúdo misto.

1. Browser não permitem o conteúdo misto. Mas permitem plug-ins, como o Silverlight e OSMF Plug-in para uniforme e travessão. O conteúdo misto é um problema de segurança - Isto acontece porque a ameaça de capacidade para injetar JS malicioso que pode fazer com que os dados de cliente para estar em risco.  Browsers bloquear este por predefinição, não sendo até ao momento a única forma de contorná-lo no lado do servidor (origem), para permitir que todos os domínios (independentemente https ou http). Isto é provavelmente não é uma boa ideia.
2. Iremos deve evitar conteúdo misto: ambos utilizam HTTP ou ambos utilizam HTTPS. Quando a reprodução do conteúdo misto, silverlightSS técnico requer limpar um aviso de conteúdo misto. técnico de flashSS processa conteúdo misto sem aviso conteúdo misto.
3. Se o ponto final de transmissão em fluxo foi criado antes de Agosto de 2014, não suporta HTTPS. Neste caso,. Crie e utilize um novo ponto de final de transmissão em fluxo para HTTPS.

A implementação de referência, para conteúdo DRM protegido, aplicações e de transmissão em fluxo serão em HTTTPS. Para abrir conteúdos, o leitor não necessita de autenticação ou de licença, pelo que terá de liberty para utilizar HTTP ou HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Active Directory que o rollover da chave de assinatura
Este é um ponto importante a ter em consideração da sua implementação. Se não considere o seguinte na sua implementação, o sistema concluído, eventualmente, deixará de trabalho completamente dentro de um máximo de 6 semanas.

AD do Azure utiliza o padrão da indústria para estabelecer fidedignidade entre si próprio e aplicações através do Azure AD. Especificamente, o Azure AD utiliza uma chave de assinatura que é composta por um par de chaves público e privado. Quando o Azure AD cria um token de segurança que contenha informações sobre o utilizador, este token é assinada pelo Azure AD utilizando a respetiva chave privada antes de ser enviada para a aplicação. Para verificar que o token é válido e, na verdade, teve origem do Azure AD, a aplicação tem de validar a assinatura do token utilizando a chave pública exposta pelo Azure AD que está contido no documento de metadados de Federação do inquilino. Esta chave pública – e a chave de assinatura partir da qual deriva – são o mesmo utilizada para todos os inquilinos no Azure AD.

Informações detalhadas sobre o rollover da chave do Azure AD podem ser encontrada no documento: [informações importantes sobre o Rollover de chave de assinatura no Azure AD](../active-directory/active-directory-signing-key-rollover.md).

Entre o [par de chaves públicas-privadas](https://login.microsoftonline.com/common/discovery/keys/),

* A chave privada é utilizada pelo Azure Active Directory para gerar um token JWT;
* A chave pública é utilizada por uma aplicação, tais como serviços de entrega de licença DRM no AMS para verificar o token JWT;

Para fins de segurança, o Azure Active Directory roda periodicamente este certificado (todas as semanas 6). Em caso de falhas de segurança, o rollover da chave pode ocorrer a qualquer altura. Por conseguinte, os serviços de entrega de licença no AMS tem de atualizar a chave pública utilizada como o Azure AD roda o par de chaves, caso contrário, a autenticação com token na AMS irá falhar e não será emitida nenhuma licença.

Isto é conseguido através da definição TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument quando configurar os serviços de entrega de licença DRM.

O fluxo do token JWT está abaixo:

1. Azure AD irá emitir o token JWT com a chave privada atual para um utilizador autenticado;
2. Quando um leitor vê um CENC com conteúdo de múltipla DRM protegido, primeiro localizará o token JWT emitido pelo Azure AD.
3. O leitor envia o pedido de aquisição de licença com o token JWT para serviços de entrega de licença no AMS;
4. Os serviços de entrega de licença no AMS irão utilizar a chave pública atual válido do Azure AD para verificar o token JWT, antes de emitir licenças.

Serviços de entrega de licença DRM serão sempre ser a verificar a chave pública atual válido do Azure AD. A chave pública apresentada pelo Azure AD será a chave utilizada para verificar um token JWT emitido pelo Azure AD.

E se o rollover da chave não crítica acontece depois do AAD gera um token JWT, mas antes do JWT token é enviado pelo jogadores aos serviços de entrega de licença do DRM no AMS para verificação?

Porque uma chave poderá ser revertida a qualquer momento, não há sempre mais do que uma chave pública válida disponíveis no documento de metadados de Federação. Entrega de licença de Media Services do Azure pode utilizar qualquer uma das chaves especificadas no documento, uma vez que uma chave poderá ser revertida em breve, outro pode ser sua substituição e assim sucessivamente.

### <a name="where-is-the-access-token"></a>Onde está o Token de acesso?
Se observar como uma aplicação web chama uma aplicação de API em [identidade da aplicação com a concessão de credenciais do OAuth 2.0 cliente](../active-directory/develop/active-directory-authentication-scenarios.md#web-application-to-web-api), o fluxo de autenticação está abaixo:

1. Um utilizador tem sessão iniciado para o Azure AD na aplicação web (consulte o [Browser para a aplicação Web](../active-directory/develop/active-directory-authentication-scenarios.md#web-browser-to-web-application).
2. O ponto final de autorização de AD do Azure redireciona o agente de utilizador para a aplicação de cliente com um código de autorização. O agente de utilizador devolve o código de autorização para o URI de redirecionamento da aplicação cliente.
3. A aplicação web tem de adquirir um token de acesso para que possa autenticar para a API web e obter o recurso pretendido. Faz um pedido para ponto final de token do Azure AD, fornecendo as credenciais, ID de cliente e URI de ID da aplicação de web da API. Apresente o código de autorização para provar que o utilizador consentiu.
4. Azure AD autentica a aplicação e devolve um token de acesso JWT que é utilizado para chamar a API web.
5. Através de HTTPS, a aplicação web utiliza o token de acesso JWT devolvido para adicionar a cadeia JWT com uma designação de "Portador" no cabeçalho de autorização do pedido para a API web. A API web, em seguida, valida o token JWT e se a validação for bem sucedida, devolve o recurso pretendido.

Neste fluxo "identidade da aplicação", a API web confianças de entidades que a aplicação web autenticar o utilizador. Por este motivo, este padrão é chamado um subsistema fidedigno. O [diagrama nesta página](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) descreve como o funciona de fluxo de concessão de códigos de autorização.

Na aquisição de licença com o token restrição, iremos estiver a seguir o mesmo padrão do subsistema fidedigna. E o serviço de entrega de licença no Media Services do Azure é o recurso de API, o "recursos de back-end" uma aplicação web precisa de aceder à web. Por isso, onde está o token de acesso?

Realmente, vamos obter token de acesso do Azure AD. Após a autenticação com êxito do utilizador, é devolvido o código de autorização. O código de autorização é utilizado, juntamente com a chave de ID e a aplicação de cliente, para exchange para o token de acesso. E o token de acesso para aceder a uma aplicação de "ponteiro" apontar ou que representa o serviço de entrega de licença de Media Services do Azure.

É preciso registar e configurar a aplicação "ponteiro" no Azure AD, seguindo os passos abaixo:

1. No inquilino do Azure AD

   * Adicione uma aplicação (recurso) com o URL de início de sessão:

   https://[resource_name].azurewebsites.NET/ e

   * URL de ID da aplicação:

   https://[aad_tenant_name].onmicrosoft.com/[resource_name];
2. Adicionar uma nova chave para a aplicação de recursos;
3. Atualizar o ficheiro de manifesto de aplicação para que a propriedade de groupMembershipClaims tem o seguinte valor: "groupMembershipClaims": "All",  
4. Na aplicação do Azure AD que aponta para a aplicação web de leitor, na secção "permissões para outras aplicações", adicione a aplicação de recurso que foi adicionada no passo 1 acima. Em "permissões delegadas" verificar "Acesso [resource_name]" marca de verificação. Isto permite que a permissão da aplicação web para criar tokens de acesso para aceder a recursos de aplicação. Deve efetuar este procedimento para a versão local e implementada da aplicação web se estiver a desenvolver com a aplicação de web do Visual Studio e o Azure.

Por conseguinte, o token JWT emitido pelo Azure AD é realmente o token de acesso para aceder a este recurso de "ponteiro".

### <a name="what-about-live-streaming"></a>E vai em direto transmissão em fluxo?
Acima, no nosso debate tem sido concentrar-se em ativos a pedido. E vai transmissão em direto?

Boas notícias é que pode utilizar o exatamente as mesma de conceção e implementação para proteger a transmissão em fluxo em direto nos Media Services do Azure, por encará-los o elemento associado um programa como um "recurso VOD".

Especificamente, é bem conhecido que fazer em direto de transmissão em fluxo numa Media Services do Azure, terá de criar um canal, em seguida, um programa no canal. Para criar o programa, tem de criar um recurso que irá conter o arquivo em direto para o programa. Para fornecer CENC com proteção múltipla DRM do conteúdo em direto, todos os que precisa de fazer, é para aplicar o mesmo programa de configuração/processamento do ativo como se era um "recurso VOD" antes de iniciar o programa.

### <a name="what-about-license-servers-outside-of-azure-media-services"></a>O que sobre servidores de licenças fora de Media Services do Azure?
Muitas vezes, os clientes podem tenham investido em licença farm de servidores tem os seus próprios dados center ou alojado por fornecedores de serviços DRM. Felizmente, a proteção de conteúdos do serviços de suporte de dados do Azure permite-lhe funcionar no modo de híbrida: conteúdo alojado e protegidas dinamicamente Media Services do Azure, enquanto licenças DRM são fornecidas pelos servidores fora Media Services do Azure. Neste caso, existem as seguintes considerações de alterações:

1. O serviço de Token seguro tem de emitir tokens que são aceitáveis e podem ser verificados pelo farm de servidores de licenciamento. Por exemplo, os servidores de licença Widevine fornecidos pelo Axinom requer um token JWT específico que contém "mensagem elegibilidade". Por conseguinte, tem de ter um STS emitir esse token JWT. Os autores concluir essa uma implementação e pode encontrar os detalhes no documento seguinte no [Centro de documentação do Azure](https://azure.microsoft.com/documentation/): [utilizando Axinom para fornecer licenças Widevine aos Media Services do Azure](media-services-axinom-integration.md).
2. Já não precisam de configurar o serviço de entrega de licença (ContentKeyAuthorizationPolicy) nos serviços de suporte de dados do Azure. O que precisa de fazer consiste em fornecer a licença de aquisição URLs (PlayReady, Widevine e do FairPlay) quando configurar AssetDeliveryPolicy configurar CENC com múltipla DRM.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>E se pretender utilizar um STS personalizado?
Pode existir algumas razões que um cliente pode optar por utilizar um STS personalizada (proteger o serviço de tokens) para fornecer os tokens JWT. Alguns deles são:

1. Identidade do fornecedor (IDP) utilizados pelo cliente não suporta STS. Neste caso, um STS personalizado podem ser uma opção.
2. O cliente pode precisar de mais flexível ou mais rígido controlo integrar STS de subscritor do cliente, sistema de faturação. Por exemplo, um operador MVPD poderão oferecer vários pacotes de subscritor OTT como premium e básica, desporto, etc. O operador pode querer corresponder as afirmações num token com o pacote de um subscritor para que apenas o conteúdo no pacote direita é disponibilizado. Neste caso, um STS personalizado fornece a flexibilidade necessária e o controlo.

Duas alterações tem de ser tomadas quando utilizar um STS personalizado:

1. Quando configurar o serviço de entrega de licença para um recurso, tem de especificar a chave de segurança utilizada para a verificação pelo STS personalizado (mais detalhes abaixo) em vez da chave atual do Azure Active Directory.
2. Quando é gerado um token JTW, foi especificada uma chave de segurança em vez da chave privada de X509 atual certificado no Azure Active Directory.

Existem dois tipos de chaves de segurança:

1. A chave simétrica: a mesma chave é utilizada para gerar e verificar um token JWT;
2. Chave assimétrica: um par de chaves públicas-privadas num certificado é utilizado com a chave privada para encriptar/gerar um token JWT e a chave pública para verificar o token de X509.

#### <a name="tech-note"></a>Nota de técnico
Se utilizar o .NET Framework / c# como a sua plataforma de desenvolvimento, a X509 certificado utilizado para a chave de segurança assimétrico tem de ter, pelo menos, 2048 de comprimento de chave. Este é um requisito da classe System.IdentityModel.Tokens.X509AsymmetricSecurityKey no .NET Framework. Caso contrário, a seguinte exceção será emitida:

IDX10630: 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' para a assinatura não pode ser menor que '2048' bits.

## <a name="the-completed-system-and-test"></a>O sistema concluído e teste
Vamos ajudá-alguns cenários no sistema ponto-a-ponto foi concluído, para que os leitores podem ter um basic "geral" do comportamento antes de obter uma conta de início de sessão.

É possível encontrar a aplicação web de leitor e o início de sessão [aqui](https://openidconnectweb.azurewebsites.net/).

Se o que precisa é "não integrada" cenário: vídeos recursos alojados nos serviços de suporte de dados do Azure que são um desprotegidos ou DRM protegido, mas sem autenticação com token (uma licença de emissão para quem-lo a pedir), pode testá-la sem início de sessão (por mudar para HTTP esteja as vídeo de transmissão em fluxo através de HTTP).

Se o que precisa é o cenário de integrada de ponto a ponto: ativos vídeos está sob dinâmica DRM a proteção na Media Services do Azure, com autenticação de token e o token JWT gerados pelo Azure AD, terá de iniciar sessão.

### <a name="user-login"></a>Início de sessão do utilizador
Para testar o sistema DRM integrado ponto-a-ponto, tem de ter uma "conta de" criado ou adicionado.

Que conta?

Embora o Azure permitisse apenas o acesso os utilizadores da conta Microsoft,-lo agora permite o acesso por utilizadores de ambos os sistemas. Isto foi feito ao ter todas as propriedades de fidedignidade do Azure para a autenticação no Azure AD, ter a autenticação de utilizadores organizacionais no Azure AD e ao criar uma relação em que o Azure AD confia no sistema de identidade do consumidor com conta Microsoft para autenticar os utilizadores. Como resultado, o Azure AD consegue autenticar contas “convidadas” da Microsoft, bem como contas “nativas” do Azure AD.

Uma vez que o Azure AD confia domínio da conta Microsoft (MSA), pode adicionar as contas de qualquer um dos seguintes domínios para o Azure AD personalizada inquilino e utilizam a conta de início de sessão:

| **Nome de domínio** | **Domínio** |
| --- | --- |
| **Domínio de inquilino personalizada do Azure AD** |somename.onmicrosoft.com |
| **Domínio empresarial** |Microsoft.com |
| **Domínio da conta Microsoft (MSA)** |Outlook.com, live.com, hotmail.com |

Poderá contactar qualquer um dos autores ter uma conta criada ou adicionadas por si.

Seguem-se as capturas de ecrã das páginas de início de sessão diferente utilizadas pelas contas de domínio diferente.

**Conta de domínio de inquilino do Azure AD personalizado**: neste caso, verá a página de início de sessão personalizado do personalizado domínio de inquilino do Azure AD.

![Conta de domínio do inquilino personalizada do Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Conta de domínio da Microsoft com smart card**: neste caso, verá a página de início de sessão personalizada ao Microsoft empresarial IT com a autenticação de dois fatores.

![Conta de domínio do inquilino personalizada do Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Conta Microsoft (MSA)**: neste caso, consulte a página de início de sessão do Microsoft Account para consumidores.

![Conta de domínio do inquilino personalizada do Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="using-encrypted-media-extensions-for-playready"></a>Utilizar extensões de suporte de dados encriptados para PlayReady
Num browser moderno com extensões de suporte de dados encriptados (EME) para obter suporte PlayReady, como o IE 11 no Windows 8.1 e cópia de segurança e o browser Microsoft Edge no Windows 10, PlayReady será o DRM subjacente para EME.

![Utilizar EME para PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

A área de leitor escuro é que PlayReady proteção impede que um realizem captura de ecrã de vídeo protegida.

O ecrã seguinte mostra o plug-ins de leitor e suporte MSE/EME.

![Utilizar EME para PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME no Microsoft Edge e o IE 11 no Windows 10 permite invocar de [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) em dispositivos Windows 10 que a suportam. PlayReady SL3000 desbloqueia o fluxo de conteúdo avançado premium (4K, HDR, etc.) e modelos de entrega de conteúdo novo (antecipado janela para o conteúdo avançado).

Concentre-se nos dispositivos Windows: PlayReady é a única DRM no hardware disponível nos dispositivos do Windows (PlayReady SL3000). Um serviço de transmissão em fluxo pode utilizar PlayReady, através de EME ou através de uma aplicação de UWP e oferecem uma qualidade do vídeo superior com PlayReady SL3000 que outro DRM. Normalmente, 2 mil conteúdo irá fluir pelo Chrome ou o Firefox e o conteúdo de 4K através do Microsoft Edge/IE11 ou uma aplicação de UWP no mesmo dispositivo (consoante as definições de serviço e de implementação).

#### <a name="using-eme-for-widevine"></a>Utilizar EME para Widevine
Num browser moderno com suporte para EME/Widevine, por exemplo, o Chrome 41 + no Windows 10, Windows 8.1, Mac OSX Yosemite e o Chrome em Android 4.4.4, Widevine da Google é DRM atrás EME.

![Utilizar EME para Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Tenha em atenção que Widevine não impede um realizem captura de ecrã de vídeo protegida.

![Utilizar EME para Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="not-entitled-users"></a>Utilizadores não realizados
Se um utilizador não é um membro do grupo "Utilizadores denominada", o utilizador não será capaz de transmitir "verificar a elegibilidade" e o serviço de licença de múltipla DRM será refuse emitir a pedido de licença, conforme mostrado abaixo. A descrição de detalhado é "adquirir licenças falha", que é que concebida.

![Utilizadores não realizados](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="running-custom-secure-token-service"></a>Executar personalizado serviço de Token seguro
Para o cenário de execução personalizado Secure Token serviço (STS), o token JWT será emitido pelo STS personalizado utilizando a chave simétrico ou assimétrico.

O caso de utilização de chave simétrica (utilizando o Chrome):

![Executar STS personalizados](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

O caso de utilização de chave assimétrica através de um X509 do certificado (utilizando o browser moderno Microsoft).

![Executar STS personalizados](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

Em ambos os casos acima, a autenticação do utilizador permanece igual – através do Azure AD. A única diferença é que os tokens JWT são emitidos pelo STS personalizado em vez do Azure AD. Obviamente, quando configurar a proteção de CENC dinâmica, a restrição do serviço de entrega de licença Especifica o tipo de JWT token, chave simétrico ou assimétrico.

## <a name="summary"></a>Resumo
Neste documento, discutimos CENC com várias native DRM e controlo de acesso através da autenticação token: respetiva estrutura e a respetiva implementação utilizando o Azure, os Media Services do Azure e Azure Media Player.

* Um design de referência é apresentado que contém todos os componentes necessários no subsistema DRM/CENC;
* Uma implementação de referência no Azure, o Media Services do Azure e o leitor de multimédia do Azure.
* Alguns tópicos diretamente envolvido na conceção e implementação também são debatidos.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
 
