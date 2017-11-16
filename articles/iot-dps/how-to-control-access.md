---
title: "Pontos finais de segurança no serviço de aprovisionamento de dispositivos de IoT | Microsoft Docs"
description: "Conceitos - como controlar o acesso ao serviço de aprovisionamento de dispositivos de IoT para aplicações de back-end. Inclui informações sobre os tokens de segurança."
services: iot-dps
documentationcenter: .net
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-dps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2017
ms.author: dkshir,rajeevmv
ms.openlocfilehash: 718fe9b3ca449f8f7b1420080ea75716e8badcf5
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Controlar o acesso ao serviço de aprovisionamento de dispositivos do Azure IoT Hub

Este artigo descreve as opções para proteger o seu serviço de aprovisionamento de dispositivos de IoT. A aprovisionamento utiliza serviço *permissões* para conceder acesso para cada ponto final. Permissões de limitam o acesso a uma instância de serviço com base na funcionalidade.

Este artigo descreve:

* As permissões diferentes que pode conceder a uma aplicação de back-end para aceder ao seu serviço de aprovisionamento.
* O processo de autenticação e os tokens que utiliza para verificar permissões.

### <a name="when-to-use"></a>Quando utilizar

Tem de ter as permissões adequadas para aceder a qualquer um dos pontos finais de serviço do aprovisionamento. Por exemplo, uma aplicação de back-end tem de incluir um token que contém as credenciais de segurança juntamente com cada mensagem envia para o serviço.

## <a name="access-control-and-permissions"></a>Controlo de acesso e permissões

Pode conceder [permissões](#device-provisioning-service-permissions) das seguintes formas:

* **Políticas de autorização de acesso de partilhado**. Políticas de acesso partilhado podem conceder qualquer combinação de [permissões](#device-provisioning-service-permissions). Pode definir políticas no [portal do Azure][lnk-management-portal], ou através de programação utilizando o [APIs de REST do serviço de aprovisionamento dispositivos][lnk-resource-provider-apis]. Um serviço de aprovisionamento recém-criado tem a política predefinida seguinte:

  * **provisioningserviceowner**: política com todas as permissões.

> [!NOTE]
> Consulte [permissões](#device-provisioning-service-permissions) para obter informações detalhadas.

## <a name="authentication"></a>Autenticação

Serviço do Azure IoT Hub dispositivo aprovisionamento concede acesso a pontos finais, verificando um token relativamente às políticas de acesso partilhado. Credenciais de segurança, tais como chaves simétricas, nunca são enviadas através da transmissão.

> [!NOTE]
> O fornecedor de recursos do serviço de aprovisionamento de dispositivos é protegido através da sua subscrição do Azure, pois todos os fornecedores no [do Azure Resource Manager][lnk-azure-resource-manager].

Para obter mais informações sobre como construir e utilize tokens de segurança, consulte a secção seguinte.

HTTP for o único protocolo suportado e implementa a autenticação, incluindo um token válido no **autorização** cabeçalho do pedido.

#### <a name="example"></a>Exemplo
`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`

> [!NOTE]
> O [SDKs IoT do Azure dispositivo aprovisionamento serviço] [ lnk-sdks] automaticamente gerar tokens ao ligar ao serviço.

## <a name="security-tokens"></a>Tokens de segurança
O serviço de aprovisionamento de dispositivos utiliza os tokens de segurança para autenticar os serviços para evitar o envio de chaves em risco. Além disso, tokens de segurança estão limitados a validade de tempo e o âmbito. [Azure SDKs IoT do dispositivo aprovisionamento serviço] [ lnk-sdks] automaticamente gerar tokens sem necessidade de nenhuma configuração especial. Alguns cenários requerem a gerar e utilizar diretamente tokens de segurança. Tais cenários incluem a utilização direta da superfície HTTP.

### <a name="security-token-structure"></a>Estrutura de token de segurança

Utilize tokens de segurança para conceder acesso tem um vínculo de tempo para os serviços para funcionalidades específicas no serviço de aprovisionamento de dispositivos de IoT. Para obter autorização para ligar ao serviço de aprovisionamento, serviços tem de emitir tokens de segurança assinados com um acesso partilhado ou de uma chave simétrica.

Um token assinado com um concede chave de acesso partilhado acesso a todas as funcionalidades associadas com as permissões de política de acesso partilhado. 

O token de segurança tem o seguinte formato:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Seguem-se os valores esperados:

| Valor | Descrição |
| --- | --- |
| {assinatura} |Uma cadeia de assinatura de HMAC SHA256 com o formato: `{URL-encoded-resourceURI} + "\n" + expiry`. **Importante**: A chave de descodificar a partir de base64 e utilizada como chave para realizar a computação HMAC SHA256.|
| {expiração} |UTF8 cadeias para o número de segundos desde a UTC de 00:00:00 época no 1 de Janeiro de 1970. |
| {ResourceURI de codificados de URL} | Caso inferiores a codificação URL do recurso minúsculas URI. Prefixos de URI (por segmento) dos pontos finais que podem ser acedidos com este token, começando com o nome de anfitrião do serviço de aprovisionamento de dispositivos de IoT (nenhum protocolo). Por exemplo, `mydps.azure-devices-provisioning.net`. |
| {policyName} |O nome da política de acesso partilhado ao qual este token refere-se. |

**Tenha em atenção, no prefixo**: prefixo o URI é calculado pelo segmento e não pelo caráter. Por exemplo `/a/b` for um prefixo para `/a/b/c` , mas não para `/a/bc`.

O seguinte fragmento de Node.js mostra uma função de chamada **generateSasToken** que calcula o token de entradas `resourceUri, signingKey, policyName, expiresInMins`. As secções seguintes como inicializar as entradas diferentes para os casos de utilização de tokens diferentes de detalhe.

```nodejs
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

Como ver uma comparação, o código de Python equivalente para gerar um token de segurança é:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> Uma vez que a validade de tempo do token é validada em máquinas de serviço de aprovisionamento de dispositivos do IoT, o que se desviam no relógio da máquina que gera o token tem de ser mínima.


### <a name="use-security-tokens-from-service-components"></a>Utilize tokens de segurança de componentes do serviço

Componentes do serviço apenas podem gerar tokens de segurança através de políticas de acesso partilhado conceder as permissões adequadas, conforme explicado anteriormente.

Seguem-se as funções de exposto nos pontos finais:

| Ponto Final | Funcionalidade |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Fornece as operações de inscrição de dispositivos com o serviço de aprovisionamento de dispositivos. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Fornece operações para a gestão de grupos de inscrição de dispositivos. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Fornece as operações de obtenção e gerir o estado de registos de dispositivos. |


Por exemplo, um serviço gerado utilizando um previamente criadas partilhado a política de acesso denominada **enrollmentread** criaria um token com os seguintes parâmetros:

* URI do recurso: `{mydps}.azure-devices-provisioning.net`,
* chave de assinatura: uma das chaves do `enrollmentread` política,
* nome da política: `enrollmentread`,
* hora de expiração.

![Criar uma política de acesso partilhado para a instância DPS no portal][img-add-shared-access-policy]

```nodejs
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

O resultado, o qual pretende conceder acesso de leitura de todos os registos de inscrição, seria:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência, poderá obter mais informações sobre como controlar o acesso ao seu serviço de aprovisionamento de dispositivos de IoT.

## <a name="device-provisioning-service-permissions"></a>Permissões de serviço de aprovisionamento de dispositivos

A tabela seguinte lista as permissões que pode utilizar para controlar o acesso ao seu serviço de aprovisionamento de dispositivos de IoT.

| Permissão | Notas |
| --- | --- |
| **ServiceConfig** |Concede acesso ao alterar as configurações de serviço. <br/>Esta permissão é utilizada pelos serviços de nuvem de back-end. |
| **EnrollmentRead** |Concede acesso de leitura às inscrições de dispositivos e os grupos de inscrição. <br/>Esta permissão é utilizada pelos serviços de nuvem de back-end. |
| **EnrollmentWrite** |Concede acesso de escrita para as inscrições de dispositivos e os grupos de inscrição. <br/>Esta permissão é utilizada pelos serviços de nuvem de back-end. |
| **RegistrationStatusRead** |Concede acesso de leitura ao estado de registo do dispositivo. <br/>Esta permissão é utilizada pelos serviços de nuvem de back-end. |
| **RegistrationStatusWrite**  |Concede elimina acesso para o estado de registo do dispositivo. <br/>Esta permissão é utilizada pelos serviços de nuvem de back-end. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iot-dps/
