---
title: "Utilizar Axinom para fornecer licenças Widevine aos Media Services do Azure | Microsoft Docs"
description: "Este artigo descreve como pode utilizar os serviços de suporte de dados do Azure (AMS) para fornecer um fluxo que é encriptado dinamicamente pelo AMS com PlayReady e Widevine DRMs. A licença PlayReady provém do servidor de licenças PlayReady de serviços de suporte de dados e a licença Widevine é enviada pelo servidor de licenças Axinom."
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.assetid: 9c93fa4e-b4da-4774-ab6d-8b12b371631d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: willzhan;Mingfeiy;rajputam;Juliako
ms.openlocfilehash: 64e8d4a88ea78e0de065e5a2c12dba4885e08bad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Utilização do Axinom para entregar licenças de Widevine para Serviços de Multimédia do Azure
> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Descrição geral
Serviços de suporte de dados do Azure (AMS) foi adicionado proteção dinâmica Widevine da Google (consulte [blogue do Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) para obter detalhes). Além disso, o Azure Media Player (AMP) também foi adicionado suporte Widevine (consulte [documento AMP](http://amp.azure.net/libs/amp/latest/docs/) para obter detalhes). Este é um accomplishment principais na transmissão em fluxo conteúdo DASH protegido por CENC com várias-native-DRM (PlayReady e Widevine) em browsers modernos equipados com MSE e EME.

Começando com o SDK de .NET de Media Services versão 3.5.2, os Media Services permite-lhe configurar o modelo de licença Widevine e obtenha licenças Widevine. Também pode utilizar os seguintes parceiros de AMS para o ajudar a fornecer licenças Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) e [castLabs](http://castlabs.com/company/partners/azure/).

Este artigo descreve como integrar e testar o servidor de licenças Widevine gerido pelo Axinom. Especificamente, abrange:  

* Configurar a encriptação comum dinâmica com múltipla DRM (PlayReady e Widevine) com o URL de aquisição de licença correspondente;
* Gerar um token JWT para satisfazer os requisitos de servidor de licenciamento;
* Desenvolvimento da aplicação do leitor de multimédia do Azure que processa a aquisição de licença com autenticação de token JWT;

O sistema completo e o fluxo de conteúdo que Key, chave ID, o seed chave, o JTW token e o respetivas afirmações pode ser descrita melhor pelo diagrama a seguir.

![DASH e CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Proteção de Conteúdo
Para configurar a proteção dinâmica e a política de entrega de chave, consulte blogue do Mingfei: [como configurar empacotamento Widevine com Media Services do Azure](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Pode configurar a proteção do dinâmica CENC com múltipla DRM para DASH, transmissão em fluxo ter ambos um dos seguintes procedimentos:

1. Proteção de PlayReady para MS Edge e IE11, que pode ter um restrições de token de autorização. A política de token restrito tem de ser acompanhada por um token emitido por uma Secure Token serviço (STS), como o Azure Active Directory;
2. Proteção Widevine Chrome,-lo pode exigir a autenticação de token com token emitido por outro STS. 

Consulte [geração de Token JWT](media-services-axinom-integration.md#jwt-token-generation) por que razão as do Azure Active Directory não pode ser utilizado como um STS para o servidor de licenças Widevine da Axinom na secção.

### <a name="considerations"></a>Considerações
1. Tem de utilizar o Axinom especificado ID para gerar a chave de conteúdo para configurar o serviço de entrega de chave de chave de seed chave (8888000000000000000000000000000000000000) e o gerado ou selecionado. Servidor de licenças Axinom irá emitir todas as licenças que contém chaves conteúdas com base no seed chave mesmo, que é válido para teste e produção.
2. O URL de aquisição de licença Widevine para fins de teste: [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense). HTTP e HTTS são permitidos.

## <a name="azure-media-player-preparation"></a>Preparação de leitor de Media Services do Azure
AMP v1.4.0 suporta a reprodução do conteúdo de AMS que é empacotado dinâmica com PlayReady e Widevine DRM.
Se o servidor de licenças Widevine não necessitar de autenticação com token, não há nada adicional que precisa de fazer para testar um conteúdo DASH protegido pelo Widevine. Por exemplo, a equipa de AMP fornece um simples [exemplo](http://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevine_notoken.html), onde pode vê-lo a trabalhar num limite e IE11 com PlayReady e o Chrome com Widevine.
O servidor de licenças Widevine fornecido pelo Axinom requer autenticação de token JWT. O token JWT tem de ser submetido com pedido de licença através de um cabeçalho de HTTP "X-AxDRM-mensagem". Para esta finalidade, terá de adicionar o javascript seguinte na página web que aloja AMP antes de definir a origem:

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

O resto do código AMP é API AMP padrão como no documento AMP [aqui](http://amp.azure.net/libs/amp/latest/docs/).

Tenha em atenção que o javascript acima para o cabeçalho de autorização personalizada definição ainda é uma abordagem de curto prazo antes oficial abordagem de longa duração em AMP é libertada.

## <a name="jwt-token-generation"></a>Geração de Token JWT
Servidor de licenças Axinom Widevine para fins de teste requer autenticação de token JWT. Além disso, uma das afirmações no JWT token é de um tipo de objeto complexo em vez do tipo de dados primitivos.

Infelizmente, do Azure AD só pode emitir JWT tokens com tipos primitivos. Da mesma forma, API do .NET Framework (System.IdentityModel.Tokens.SecurityTokenHandler e JwtPayload) permite-lhe apenas o tipo de objeto complexo como afirmações de entrada. No entanto, as afirmações ainda são serializadas como cadeia. Por isso não é possível utilizar qualquer um dos dois para gerar o token JWT para o pedido de licença Widevine.

De João Sheehan [pacote JWT Nuget](https://www.nuget.org/packages/JWT) satisfaça as necessidades, pelo que iremos utilizar este pacote Nuget.

Segue-se o código para gerar token JWT com as afirmações necessários, conforme exigido pelo servidor de licenças Axinom Widevine para fins de teste:

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.IdentityModel.Tokens;
    using System.IdentityModel.Protocols.WSTrust;
    using System.Security.Claims;

    namespace OpenIdConnectWeb.Utils
    {
        public class JwtUtils
        {
            //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
            public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
            {
                byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.

                var payload = new Dictionary<string, object>()
                             {
                                 { "version", 1 },
                                 { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                                 { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
                             };

                string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);

                return token;
            }

            //convert hex string to byte[]
            public static byte[] ConvertHexStringToByteArray(string hexString)
            {
                if (hexString.Length % 2 != 0)
                {
                    throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
                }

                byte[] HexAsBytes = new byte[hexString.Length / 2];
                for (int index = 0; index < HexAsBytes.Length; index++)
                {
                    string byteValue = hexString.Substring(index * 2, 2);
                    HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
                }

                return HexAsBytes;
            }

        }  

    }  

Servidor de licenças Axinom Widevine

    <add key="ax:laurl" value="http://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

### <a name="considerations"></a>Considerações
1. Apesar do serviço de entrega de licença do AMS PlayReady requer "portador =" anterior a um token de autenticação, o servidor de licenças Axinom Widevine não utilizá-lo.
2. A chave de comunicação Axinom é utilizada como chave de assinatura. Tenha em atenção que a chave é uma cadeia hexadecimal, no entanto, que têm de ser tratado como uma série de bytes não é uma cadeia quando codificação. Isto é conseguido através do método ConvertHexStringToByteArray.

## <a name="retrieving-key-id"></a>Obter ID de chave
Poderá ter reparado que, no código para gerar um JWT, ID de token, chave é necessária. Uma vez que as necessidades de token JWT estar pronto antes de carregar AMP player, chave ID tem de ser obtido para gerar o JWT token.

De decorrer, existem várias formas de obter reter da chave de ID. Por exemplo, pode armazenar um ID de chave, juntamente com os metadados dos conteúdos numa base de dados. Ou pode obter ID de ficheiro MPD TRAÇO (descrição de apresentação do suporte de dados) da chave. O código abaixo destina-se a última opção.

    //get key_id from DASH MPD
    public static string GetKeyID(string dashUrl)
    {
        if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
        {
            dashUrl += "(format=mpd-time-csf)";
        }

        XPathDocument objXPathDocument = new XPathDocument(dashUrl);
        XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
        XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
        objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
        objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
        objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
        objXmlNamespaceManager.AddNamespace("xsi",  "http://www.w3.org/2001/XMLSchema-instance");
        objXmlNamespaceManager.PushScope();

        XPathNodeIterator objXPathNodeIterator;
        objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);

        string key_id = string.Empty;
        if (objXPathNodeIterator.MoveNext())
        {
            key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
        }

        return key_id;
    }

## <a name="summary"></a>Resumo
Com a adição de mais recente do suporte de Widevine na proteção de conteúdos do serviços de suporte de dados do Azure e Azure Media Player, mas é possível implementar a transmissão em fluxo do TRAÇO + várias-native-DRM (PlayReady + Widevine) com ambos os serviço de licença PlayReady do AMS e Widevine servidor de licenças de Axinom para os seguintes browsers modernos:

* Chrome
* Microsoft Edge no Windows 10
* IE 11 no Windows 8.1 e Windows 10
* O Firefox (ambiente de trabalho) e Safari no Mac (não iOS) também são suportados através do Silverlight e o mesmo URL com o Azure Media Player

Os parâmetros seguintes são necessários no servidor de licenças Axinom Widevine aproveitamento mini-solução. Exceto chave ID, o resto dos parâmetros fornecidos por Axinom com base na respetiva configuração de servidor Widevine.

| Parâmetro | Como é utilizado |
| --- | --- |
| ID de chave de comunicação |Tem de ser incluído como um valor de afirmação "com_key_id" no JWT token (consulte [isto](media-services-axinom-integration.md#jwt-token-generation) secção). |
| Chave de comunicação |Tem de ser utilizado como a chave de assinatura do JWT token (consulte [isto](media-services-axinom-integration.md#jwt-token-generation) secção). |
| Chave seed |Tem de ser utilizado para gerar a chave de conteúdo com qualquer conteúdo fornecido ID de chave (consulte [isto](media-services-axinom-integration.md#content-protection) secção). |
| URL de aquisição de licença Widevine |Tem de ser utilizado na configuração de política de entrega de elementos para transmissão em fluxo DASH (consulte [isto](media-services-axinom-integration.md#content-protection) secção). |
| ID de chave de conteúdo |Tem de ser incluídos como parte do valor de afirmação de mensagem de elegibilidade do JWT token (consulte [isto](media-services-axinom-integration.md#jwt-token-generation) secção). |

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Em que as confirmações
Gostaríamos de reconhecer seguintes pessoas que contribuíram para criar este documento: Kristjan Jõgi de Axinom, Mingfei Yan e Amit Rajput.

