---
title: "Azure Service Fabric inverso comunicação segura proxy | Microsoft Docs"
description: "Configure um proxy inverso para ativar a comunicação de ponto a ponto segura."
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/10/2017
ms.author: kavyako
ms.openlocfilehash: 568f9638c59282bcd7d3fae058a1588a889c22dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Ligar a um serviço seguro com o proxy inverso

Este artigo explica como estabelecer uma ligação segura entre o proxy inverso e serviços, permitindo um canal seguro de ponto a ponto.

A ligar aos serviços segurados só é suportada quando proxy inverso estiver configurado para escutar HTTPS. Resto do documento assume que for este o caso.
Consulte [proxy no Azure Service Fabric inverso](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) para configurar o proxy reverso no Service Fabric.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Estabelecimento da ligação segura entre os serviços e proxy inverso 

### <a name="reverse-proxy-authenticating-to-services"></a>Autenticação nos serviços de um proxy inverso:
O proxy reverso identifica-se aos serviços utilizando o respetivo certificado, especificado com ***reverseProxyCertificate*** propriedade no **Cluster** [secção de tipo de recurso](../azure-resource-manager/resource-group-authoring-templates.md). Serviços podem implementar a lógica para verificar o certificado apresentado pelo proxy inverso. Os serviços podem especificar os detalhes do certificado de cliente aceite como definições de configuração no pacote de configuração. Esta situação pode ser lido no tempo de execução e utilizada para validar o certificado apresentado pelo proxy inverso. Consulte [Gerir aplicação parâmetros](service-fabric-manage-multiple-environment-app-configuration.md) para adicionar as definições de configuração. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Verificar a identidade do serviço através do certificado apresentado pelo serviço de um proxy inverso:
Para efetuar a validação do certificado de servidor de certificados apresentados pelos serviços, proxy inverso suporta uma das seguintes opções: None, ServiceCommonNameAndIssuer e ServiceCertificateThumbprints.
Para selecionar uma destas três opções, especifique o **ApplicationCertificateValidationPolicy** na secção de parâmetros do elemento de ApplicationGateway/Http em [fabricSettings](service-fabric-cluster-fabric-settings.md).

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "None"
              }
            ]
          }
        ],
        ...
}
```

Consulte a secção seguinte para obter detalhes sobre a configuração adicional para cada uma destas opções.

### <a name="service-certificate-validation-options"></a>Opções de validação do certificado de serviço 

- **Nenhum**: proxy inverso ignora a verificação do certificado de serviço efetuados e estabelece a ligação segura. Este é o comportamento predefinido.
Especifique o **ApplicationCertificateValidationPolicy** com valor **nenhum** na secção de parâmetros do elemento de ApplicationGateway/Http.

- **ServiceCommonNameAndIssuer**: proxy inverso verifica o certificado apresentado pelo serviço, com base no nome comum do certificado e o thumbprint do emissor imediata: Especifique o **ApplicationCertificateValidationPolicy** com valor **ServiceCommonNameAndIssuer** na secção de parâmetros do elemento de ApplicationGateway/Http.

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "ServiceCommonNameAndIssuer"
              }
            ]
          }
        ],
        ...
}
```

Para especificar a lista de nome comum do serviço e os thumbprints de emissor, adicione um **Http/ApplicationGateway/ServiceCommonNameAndIssuer** elemento em fabricSettings, conforme mostrado abaixo. Podem ser adicionados vários nome comum do certificado e pares de thumbprint do emissor do elemento de matriz de parâmetros. 

Se o proxy reverso de ponto final está a ligar ao apresenta um certificado que é comum nome e o emissor thumbprint corresponde a qualquer um dos valores especificados aqui, o canal SSL for estabelecida. Após a falha para fazer corresponder os detalhes do certificado, proxy inverso falha o pedido do cliente com um código de estado 502 (Gateway incorreto). A linha de estado HTTP também irá conter a frase "Certificado SSL inválido." 

```json
{
"fabricSettings": [
          ...
          {
             "name": "ApplicationGateway/Http/ServiceCommonNameAndIssuer",
            "parameters": [
              {
                "name": "WinFabric-Test-Certificate-CN1",
                "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 b4 22 11"
              },
              {
                "name": "WinFabric-Test-Certificate-CN2",
                "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 11 33 44"
              }
            ]
          }
        ],
        ...
}
```


- **ServiceCertificateThumbprints**: proxy inverso irá verificar o certificado de serviço efetuados com base no respetivo thumbprint. Pode escolher aceder esta rota de quando os serviços estão configurados com a recuperação Self-assinado certificados: Especifique o **ApplicationCertificateValidationPolicy** com valor **ServiceCertificateThumbprints** na secção de parâmetros do elemento de ApplicationGateway/Http.

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "ServiceCertificateThumbprints"
              }
            ]
          }
        ],
        ...
}
```

Também de especificar os thumbprints com um **ServiceCertificateThumbprints** entrada na secção de parâmetros do elemento de ApplicationGateway/Http. Thumbprints vários podem ser especificados como uma lista separada por vírgulas no campo valor, conforme mostrado abaixo:

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "ServiceCertificateThumbprints",
                "value": "78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf,78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 b9"
              }
            ]
          }
        ],
        ...
}
```

Se o thumbprint do certificado de servidor estiver indicada nesta entrada de configuração, proxy inverso é concluída com êxito a ligação SSL. Caso contrário, termina a ligação e falhar o pedido do cliente com um 502 (Gateway incorreto). A linha de estado HTTP também irá conter a frase "Certificado SSL inválido."

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Lógica de seleção de ponto final quando serviços expõem pontos finais de segurados, bem como protegidos
Recursos de infraestrutura de serviço suporta configurar vários pontos finais para um serviço. Consulte [Specify resources in a service manifest](service-fabric-service-manifest-resources.md) (Especificar recursos num manifesto do serviço).

Proxy inverso seleciona um dos pontos finais para reencaminhar o pedido com base no **ListenerName** parâmetro de consulta. Se não for especificado, pode escolher a qualquer ponto final da lista de pontos finais. Agora pode ser um ponto final HTTP ou HTTPS. Poderão existir cenários/requisitos onde pretende que o proxy inverso para operar num "modo apenas de seguro," revertidos não pretende que o proxy reverso seguro para reencaminhar pedidos para os pontos finais não segura. Isto pode ser conseguido com a especificação de **SecureOnlyMode** entrada de configuração com o valor **verdadeiro** na secção de parâmetros do elemento de ApplicationGateway/Http.   

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "SecureOnlyMode",
                "value": true
              }
            ]
          }
        ],
        ...
}
```

> 
> Quando a funcionar no **SecureOnlyMode**, se o cliente tiver especificado um **ListenerName** correspondente a um ponto final HTTP(unsecured), proxy inverso falha o pedido com um código de estado (não for encontrado) HTTP 404.

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Ao configurar a autenticação de certificado de cliente através do proxy inverso
Acontece terminação de SSL no proxy inverso e todos os dados de certificado de cliente é perdido. Para os serviços efetuar a autenticação de certificado de cliente, defina o **ForwardClientCertificate** definição na secção de parâmetros do elemento de ApplicationGateway/Http.

1. Quando **ForwardClientCertificate** está definido como **falso**, inverta proxy irá pedir para o certificado de cliente durante o handshake SSL com o cliente.
Este é o comportamento predefinido.

2. Quando **ForwardClientCertificate** está definido como **verdadeiro**, inverso pedidos de proxy para o certificado do cliente durante o handshake SSL com o cliente.
Este irá, em seguida, reencaminhar o cliente dados de certificado num cabeçalho HTTP personalizado denominado **certificado de cliente X**. O valor do cabeçalho é a cadeia de formato PEM codificado em base64 de certificado do cliente. O serviço pode ter êxito/falha o pedido com o código de estado adequado após inspecionar os dados de certificado.
Se o cliente não apresentar um certificado, o proxy inverso reencaminha um cabeçalho vazio e permitir que o serviço de processar as maiúsculas e minúsculas.

> Proxy inverso é um reencaminhador mere. Não efetuará nenhuma validação de certificado do cliente.


## <a name="next-steps"></a>Passos seguintes
* Consulte [Configurar proxy inverso para ligar aos serviços segurados](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) para o Azure Resource Manager amostras de modelo para configurar o secure proxy inverso com o certificado de serviço diferentes opções de validação.
* Ver um exemplo de comunicação HTTP entre serviços num [projeto de exemplo no GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Chamadas de procedimento remoto com o sistema de interação remota Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [API Web que utiliza OWIN no Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Gerir certificados de cluster](service-fabric-cluster-security-update-certs-azure.md)
