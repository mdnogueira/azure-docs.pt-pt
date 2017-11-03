---
title: "Descrição geral do modelo de licença Widevine | Microsoft Docs"
description: "Este tópico fornece uma descrição geral de um modelo de licença Widevine que utilizou para configurar licenças Widevine."
author: juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: 0e6f1f05-7ed6-4ed6-82a0-0cc2182b075a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 667ff16dc7608dab2a5b8b1fd7df715da4620ca1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="widevine-license-template-overview"></a>Descrição geral do modelo de licença Widevine
## <a name="overview"></a>Descrição geral
Media Services do Azure agora permite-lhe configurar e pedido licenças Widevine. Quando o leitor de utilizador final tenta reproduzir o conteúdo de Widevine protegido, é enviado um pedido para o serviço de entrega de licença para obter uma licença. Se o serviço de licenciamento aprova o pedido, que emite a licença que é enviada para o cliente e pode ser utilizada para desencriptar e reproduzir o conteúdo especificado.

Pedido de licença Widevine é formatado como uma mensagem JSON.  

>[!NOTE]
> Pode optar por criar uma mensagem vazia com nenhuma valores apenas "{}" e será criado um modelo de licença com todas as predefinições. A predefinição funciona para a maioria dos casos. Por exemplo, para MS baseada em cenários de entrega de licença que devem sempre ser predefinido. Se precisar de definir os valores de "content_id" e "fornecedor", um fornecedor tem de corresponder as credenciais de Widevine da Google.

    {  
       “payload”:“<license challenge>”,
       “content_id”: “<content id>” 
       “provider”: ”<provider>”
       “allowed_track_types”:“<types>”,
       “content_key_specs”:[  
          {  
             “track_type”:“<track type 1>”
          },
          {  
             “track_type”:“<track type 2>”
          },
          …
       ],
       “policy_overrides”:{  
          “can_play”:<can play>,
          “can persist”:<can persist>,
          “can_renew”:<can renew>,
          “rental_duration_seconds”:<rental duration>,
          “playback_duration_seconds”:<playback duration>,
          “license_duration_seconds”:<license duration>,
          “renewal_recovery_duration_seconds”:<renewal recovery duration>,
          “renewal_server_url”:”<renewal server url>”,
          “renewal_delay_seconds”:<renewal delay>,
          “renewal_retry_interval_seconds”:<renewal retry interval>,
          “renew_with_usage”:<renew with usage>
       }
    }

## <a name="json-message"></a>Mensagem JSON
| Nome | Valor | Descrição |
| --- | --- | --- |
| Payload |Cadeia codificada em Base64 |O pedido de licença enviado por um cliente. |
| content_id |Cadeia codificada em Base64 |Identificador utilizado para derivar KeyId(s) e das chaves de conteúdo para cada content_key_specs.track_type. |
| Fornecedor |Cadeia |Utilizado para procurar as chaves de conteúdo e políticas. Se a entrega de chave MS é utilizada para entrega de licença Widevine, este parâmetro é ignorado. |
| policy_name |Cadeia |Nome de uma política anteriormente registado. Opcional |
| allowed_track_types |Enum |SD_ONLY ou SD_HD. Controlos que conteúdo chaves devem ser incluídos uma licença |
| content_key_specs |matriz de estruturas JSON, consulte **especificações de chave de conteúdo** abaixo |Um melhorar detalhada controlo sobre o conteúdo de chaves para devolver. Consulte a especificação de chave de conteúdo abaixo para obter mais detalhes.  Pode ser especificado apenas um dos allowed_track_types e content_key_specs. |
| use_policy_overrides_exclusively |valor booleano. VERDADEIRO ou FALSO |Utilize os atributos de política especificados por policy_overrides e omitir todas as políticas anteriormente armazenada. |
| policy_overrides |Estrutura JSON, consulte **política substitui** abaixo |Definições de política para esta licença.  No caso deste recurso tem uma política de pré-definida, serão utilizados o estes valores especificados. |
| session_init |Estrutura JSON, consulte **inicialização da sessão** abaixo |Dados opcionais transmitido a licença. |
| parse_only |valor booleano. VERDADEIRO ou FALSO |O pedido de licença é analisado mas nenhuma licença é emitida. No entanto, os valores formulário de pedido de licença são devolvido na resposta. |

## <a name="content-key-specs"></a>Especificações da chave de conteúdo
Se existir uma política já existente, não é necessário especificar qualquer um dos valores existentes na especificação de chave de conteúdo.  A política de pré-existente associada este conteúdo será utilizada para determinar a proteção de saída como HDCP e CGMS.  Se uma política pré-existente não está registada com o servidor de licenças Widevine, o fornecedor de conteúdo pode inserir os valores para o pedido de licença.   

Cada content_key_specs tem de ser especificado para todas as controla, independentemente da opção use_policy_overrides_exclusively. 

| Nome | Valor | Descrição |
| --- | --- | --- |
| content_key_specs. track_type |Cadeia |Um nome de tipo de controlar. Se content_key_specs é especificado no pedido de licença, certifique-se de que especifica que todos os tipos de controlar explicitamente. Falha ao fazê-lo irá resultar na falha para reproduzir nos últimos 10 segundos. |
| content_key_specs  <br/> security_level |UInt32 |Define os requisitos do cliente robustez para reprodução. <br/> 1 - baseada em software whitebox crypto é necessário. <br/> 2 - criptografia de software e um descodificador oculto é necessário. <br/> 3 – o material de chaves e as operações de criptografia têm de ser executadas num ambiente de execução fidedigna de segurança de hardware. <br/> 4 - a criptografia e descodificar de conteúdo têm de ser executados dentro de um ambiente de execução fidedigna de segurança de hardware.  <br/> 5 - a criptografia, descodificar e processamento do suporte de dados (comprimidos e descomprimidos) de todos os devem ser processadas num ambiente de execução fidedigna de segurança de hardware. |
| content_key_specs <br/> required_output_protection.hDC |cadeia - um dos: HDCP_V2 HDCP_NONE, HDCP_V1, |Indica se é exigir HDCP |
| content_key_specs <br/>key |Base64 <br/>cadeia codificada |Chave de conteúdo a utilizar para este controlar. Se for especificado, é necessário o track_type ou key_id.  Esta opção permite ao fornecedor de conteúdos inserir a chave de conteúdo para este registo em vez de permitir que o servidor de licenças Widevine gerar ou procurar uma chave. |
| content_key_specs.key_id |Cadeia binária, de codificados Base64 16 bytes |Identificador exclusivo para a chave. |

## <a name="policy-overrides"></a>Substituições de política
| Nome | Valor | Descrição |
| --- | --- | --- |
| policy_overrides. can_play |valor booleano. VERDADEIRO ou FALSO |Indica que reprodução do conteúdo é permitida. Predefinição é falsa. |
| policy_overrides. can_persist |valor booleano. VERDADEIRO ou FALSO |Indica que a licença pode ser persistentes para o armazenamento não volátil para utilização offline. Predefinição é falsa. |
| policy_overrides. can_renew |valor boleano VERDADEIRO ou FALSO |Indica que a renovação esta licença é permitida. Se for VERDADEIRO, a duração da licença de pode ser expandida por heartbeat. Predefinição é falsa. |
| policy_overrides. license_duration_seconds |Int64 |Indica a janela de tempo para este licenças específico. Um valor de 0 indica que não há nenhum limite para a duração. Predefinição é 0. |
| policy_overrides. rental_duration_seconds |Int64 |Indica a janela de tempo enquanto reprodução é permitida. Um valor de 0 indica que não há nenhum limite para a duração. Predefinição é 0. |
| policy_overrides. playback_duration_seconds |Int64 |A janela de visualização de tempo depois de reprodução começar dentro da duração de licença. Um valor de 0 indica que não há nenhum limite para a duração. Predefinição é 0. |
| policy_overrides. renewal_server_url |Cadeia |Todos os pedidos de heartbeat (renovação) para esta licença deverá ser direcionados para o URL especificado. Este campo só é utilizado se can_renew for VERDADEIRO. |
| policy_overrides. renewal_delay_seconds |Int64 |Quantos segundos após license_start_time, antes de renovação pela primeira vez é tentada. Este campo só é utilizado se can_renew for VERDADEIRO. A predefinição é 0 |
| policy_overrides. renewal_retry_interval_seconds |Int64 |Especifica o atraso em segundos entre pedidos de renovação de licença subsequentes, em caso de falha. Este campo só é utilizado se can_renew for VERDADEIRO. |
| policy_overrides. renewal_recovery_duration_seconds |Int64 |A janela de tempo, em que é permitida a reprodução continuar enquanto renovação é tentada, mas sem êxito devido a problemas de back-end com o servidor de licenças. Um valor de 0 indica que não há nenhum limite para a duração. Este campo só é utilizado se can_renew for VERDADEIRO. |
| policy_overrides. renew_with_usage |valor boleano VERDADEIRO ou FALSO |Indica que a licença deverá ser enviada para renovação quando é iniciada a utilização. Este campo só é utilizado se can_renew for VERDADEIRO. |

## <a name="session-initialization"></a>Inicialização de sessão
| Nome | Valor | Descrição |
| --- | --- | --- |
| provider_session_token |Cadeia codificada em Base64 |Este token de sessão é passada de volta na licença de e existe na renovações subsequentes.  O token de sessão não serão mantidas para além de sessões. |
| provider_client_token |Cadeia codificada em Base64 |Token do cliente para enviar novamente na resposta licença.  Se o pedido de licença contém um token do cliente, este valor é ignorado. O token do cliente irá manter-se para além de sessões de licença. |
| override_provider_client_token |valor booleano. VERDADEIRO ou FALSO |Se false e o pedido de licença contém um token do cliente, utilize o token do pedido, mesmo se um token do cliente foi especificado nesta estrutura.  Se for VERDADEIRO, utilize sempre o token especificado nesta estrutura. |

## <a name="configure-your-widevine-licenses-using-net-types"></a>Configurar as suas licenças Widevine utilizando tipos de .NET
Os Media Services fornecem APIs .NET que permitem-lhe configurar as suas licenças Widevine. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>Classes, tal como definido no SDK do .NET de Media Services
Seguem-se as definições dos seguintes tipos.

    public class WidevineMessage
    {
        public WidevineMessage();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public AllowedTrackTypes? allowed_track_types { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public ContentKeySpecs[] content_key_specs { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public object policy_overrides { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

### <a name="example"></a>Exemplo
O exemplo seguinte mostra como utilizar os APIs de .NET para configurar uma licença Widevine simple.

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }


## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consultar também
[Utilizar encriptação comum PlayReady e/ou Widevine dinâmico](media-services-protect-with-drm.md)

