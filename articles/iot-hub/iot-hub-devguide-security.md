---
title: "Compreender a segurança de IoT Hub do Azure | Microsoft Docs"
description: "Guia para programadores - como controlar o acesso ao IoT Hub para aplicações de dispositivos e aplicações de back-end. Inclui informações sobre o suporte para certificados x. 509 e tokens de segurança."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 45631e70-865b-4e06-bb1d-aae1175a52ba
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: dobett
ms.openlocfilehash: a038a46c98af5b434456e1bb979fc6cd8e009d76
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="control-access-to-iot-hub"></a>Controlar o acesso ao Hub IoT

Este artigo descreve as opções para proteger o seu IoT hub. IoT Hub utiliza *permissões* para conceder acesso para cada ponto de final de hub IoT. Permissões de limitam o acesso a um IoT hub com base na funcionalidade.

Este artigo descreve:

* As permissões diferentes que pode conceder a uma aplicação de back-end ou de dispositivo para aceder ao seu IoT hub.
* O processo de autenticação e os tokens que utiliza para verificar permissões.
* Como as credenciais de âmbito para limitar o acesso a recursos específicos.
* Suporte de IoT Hub para certificados x. 509.
* Mecanismos de autenticação do dispositivo personalizada que utilizam os registos de identidade de dispositivo existente ou os esquemas de autenticação.

Tem de ter as permissões adequadas para aceder a qualquer um dos pontos finais do IoT Hub. Por exemplo, um dispositivo tem de incluir um token que contém as credenciais de segurança juntamente com cada mensagem a enviar ao IoT Hub.

## <a name="access-control-and-permissions"></a>Controlo de acesso e permissões

Pode conceder [permissões](#iot-hub-permissions) das seguintes formas:

* **Políticas de acesso de partilhado de nível do IoT hub**. Políticas de acesso partilhado podem conceder qualquer combinação de [permissões](#iot-hub-permissions). Pode definir políticas no [portal do Azure][lnk-management-portal], ou através de programação utilizando o [fornecedor de recursos do IoT Hub REST APIs][lnk-resource-provider-apis]. Um IoT hub recentemente criado tem as seguintes políticas de predefinição:

  * **iothubowner**: política com todas as permissões.
  * **serviço**: política com **ServiceConnect** permissão.
  * **dispositivo**: política com **DeviceConnect** permissão.
  * **registryRead**: política com **RegistryRead** permissão.
  * **registryReadWrite**: política com **RegistryRead** e RegistryWrite permissões.
  * **Credenciais de segurança por dispositivo**. Cada IoT Hub contém um [registo de identidade][lnk-identity-registry]. Para cada dispositivo neste registo de identidade, pode configurar as credenciais de segurança que conceda **DeviceConnect** permissões no âmbito para os pontos finais de dispositivo correspondente.

Por exemplo, numa solução IoT típica:

* O componente de gestão de dispositivos utiliza o *registryReadWrite* política.
* O componente de processador de eventos utiliza o *serviço* política.
* O componente de lógica de negócio de tempo de execução dispositivo utiliza o *serviço* política.
* Os dispositivos individuais ligam utilizando as credenciais armazenadas no registo de identidade do hub IoT.

> [!NOTE]
> Consulte [permissões](#iot-hub-permissions) para obter informações detalhadas.

## <a name="authentication"></a>Autenticação

IoT Hub do Azure concede acesso a pontos finais, verificando um token contra credenciais de segurança do registo de identidade e políticas de acesso partilhado.

Credenciais de segurança, tais como chaves simétricas, nunca são enviadas através da transmissão.

> [!NOTE]
> O fornecedor de recursos do IoT Hub do Azure é protegido através da sua subscrição do Azure, pois todos os fornecedores no [do Azure Resource Manager][lnk-azure-resource-manager].

Para obter mais informações sobre como construir e utilize tokens de segurança, consulte [tokens de segurança de IoT Hub][lnk-sas-tokens].

### <a name="protocol-specifics"></a>Especificações de protocolo

Cada protocolos suportados, tais como MQTT, AMQP e HTTPS, transportes tokens formas diferentes.

Quando utilizar MQTT, o pacote de ligar tem o ID do dispositivo como o ClientId, {iothubhostname} / {"deviceId"} no campo de nome de utilizador e um token SAS no campo de palavra-passe. {iothubhostname} deve ser o CName completo do hub IoT (por exemplo, contoso.azure-devices.net).

Quando utilizar [AMQP][lnk-amqp], suporta o IoT Hub [SASL simples] [ lnk-sasl-plain] e [AMQP afirmações com base-segurança][lnk-cbs].

Se utilizar AMQP afirmações com base-segurança, a norma Especifica como a transmitir estes tokens.

Para SASL simples, o **username** pode ser:

* `{policyName}@sas.root.{iothubName}`Se utilizar tokens de nível do hub IoT.
* `{deviceId}@sas.{iothubname}`Se a utilização de tokens no âmbito do dispositivo.

Em ambos os casos, o campo de palavra-passe contém o token, conforme descrito em [tokens de segurança de IoT Hub][lnk-sas-tokens].

HTTPS implementa autenticação, incluindo um token válido no **autorização** cabeçalho do pedido.

#### <a name="example"></a>Exemplo

Nome de utilizador (DeviceId é sensível a maiúsculas e minúsculas):`iothubname.azure-devices.net/DeviceId`

Palavra-passe (token SAS gerar com o [Explorador de dispositivo] [ lnk-device-explorer] ferramenta):`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> O [SDKs IoT do Azure] [ lnk-sdks] automaticamente gerar tokens ao ligar ao serviço. Em alguns casos, os SDKs IoT do Azure suporta todos os protocolos nem todos os métodos de autenticação.

### <a name="special-considerations-for-sasl-plain"></a>Considerações especiais para SASL simples

Quando utilizar SASL simples com AMQP, um cliente ao ligar a um IoT hub pode utilizar um único token para cada ligação de TCP. Quando o token expira, a ligação de TCP desliga do serviço e aciona um restabelecimento de ligação. Este comportamento, enquanto não problemático para uma aplicação de back-end, é prejudiciais para uma aplicação de dispositivo pelos seguintes motivos:

* Normalmente, ligar gateways em nome de vários dispositivos. Quando utilizar SASL simples, têm de criar uma ligação de TCP distinta para cada dispositivo ligar a um IoT hub. Este cenário consideravelmente aumenta o consumo de energia e recursos de rede e aumenta a latência de ligação de cada dispositivo.
* Dispositivos restrita de recursos são afetados pelo aumento da utilização de recursos para voltar a ligar depois de cada expiração do token.

## <a name="scope-iot-hub-level-credentials"></a>Definir o âmbito de credenciais de nível do hub IoT

Pode definir o âmbito políticas de segurança ao nível do hub IoT através da criação de tokens com um URI do recurso restrito. Por exemplo, o ponto final para enviar mensagens do dispositivo para nuvem a partir de um dispositivo é **/devices/ {"deviceId"} / mensagens/eventos**. Também pode utilizar uma política de acesso partilhado ao nível do hub IoT com **DeviceConnect** permissões para assinar um token cujo resourceURI é **/devices/ {"deviceId"}**. Esta abordagem cria um token que só pode ser utilizado para enviar mensagens em nome de dispositivo **deviceId**.

Este mecanismo é semelhante para o [política do publicador de Event Hubs][lnk-event-hubs-publisher-policy]e permite-lhe implementar métodos de autenticação personalizados.

## <a name="security-tokens"></a>Tokens de segurança

IoT Hub utiliza os tokens de segurança para autenticar dispositivos e serviços para evitar o envio de chaves em risco. Além disso, tokens de segurança estão limitados a validade de tempo e o âmbito. [Os SDKs IoT do Azure] [ lnk-sdks] automaticamente gerar tokens sem necessidade de nenhuma configuração especial. Alguns cenários requerem a gerar e utilizar diretamente tokens de segurança. Tais cenários incluem:

* A utilização direta do analisa MQTT, AMQP ou HTTPS.
* A implementação padrão de serviço de tokens, conforme explicado no [autenticação de dispositivo personalizada][lnk-custom-auth].

IoT Hub também permite que os dispositivos autenticar com o IoT Hub com [certificados x. 509][lnk-x509].

### <a name="security-token-structure"></a>Estrutura de token de segurança

Utilize tokens de segurança para conceder acesso tem um vínculo de tempo para os dispositivos e serviços para funcionalidades específicas do IoT Hub. Para obter autorização para estabelecer ligação ao IoT Hub, dispositivos e serviços tem de enviar os tokens de segurança assinados com um acesso partilhado ou de uma chave simétrica. Estas chaves são armazenadas com uma identidade de dispositivo no registo de identidade.

Um token assinado com um concede chave de acesso partilhado acesso a todas as funcionalidades associadas com as permissões de política de acesso partilhado. Um token assinado com concede apenas chave simétrica uma identidade de dispositivo a **DeviceConnect** permissão para a identidade de dispositivo associados.

O token de segurança tem o seguinte formato:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Seguem-se os valores esperados:

| Valor | Descrição |
| --- | --- |
| {assinatura} |Uma cadeia de assinatura de HMAC SHA256 com o formato: `{URL-encoded-resourceURI} + "\n" + expiry`. **Importante**: A chave de descodificar a partir de base64 e utilizada como chave para realizar a computação HMAC SHA256. |
| {resourceURI} |Prefixos de URI (por segmento) dos pontos finais que podem ser acedidos com este token, começando com o nome de anfitrião do hub IoT (nenhum protocolo). Por exemplo, `myHub.azure-devices.net/devices/device1` |
| {expiração} |UTF8 cadeias para o número de segundos desde a UTC de 00:00:00 época no 1 de Janeiro de 1970. |
| {ResourceURI de codificados de URL} |Caso inferiores a codificação URL do recurso minúsculas URI |
| {policyName} |O nome da política de acesso partilhado ao qual este token refere-se. Ausência de se o token refere-se às credenciais de registo do dispositivo. |

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

    // Construct autorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
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
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

A funcionalidade em c# para gerar um token de segurança é:

```C#
using System;
using System.Globalization;
using System.Net;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;

public static string generateSasToken(string resourceUri, string key, string policyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = WebUtility.UrlEncode(resourceUri).ToLower() + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}", WebUtility.UrlEncode(resourceUri).ToLower(), WebUtility.UrlEncode(signature), expiry);

    if (!String.IsNullOrEmpty(policyName))
    {
        token += "&skn=" + policyName;
    }

    return token;
}

```


> [!NOTE]
> Uma vez que a validade de tempo do token é validada em máquinas de IoT Hub, o que se desviam no relógio da máquina que gera o token tem de ser mínima.

### <a name="use-sas-tokens-in-a-device-app"></a>Utilize tokens SAS de uma aplicação de dispositivo

Existem duas formas de obter **DeviceConnect** permissões com o IoT Hub com tokens de segurança: Utilize um [chave simétrica de dispositivo no registo de identidade](#use-a-symmetric-key-in-the-identity-registry), ou utilize um [chave de acesso partilhado](#use-a-shared-access-policy).

Lembre-se de que todas as funcionalidades acessível a partir de dispositivos é exposta pelo estrutura em pontos finais com o prefixo `/devices/{deviceId}`.

> [!IMPORTANT]
> A única forma que o IoT Hub autentica um dispositivo específico está a utilizar a chave simétrica de identidade do dispositivo. Em casos quando uma política de acesso partilhado é utilizada para aceder à funcionalidade do dispositivo, a solução tem de considerar o componente emitir o token de segurança como uma subcomponente fidedigna.

Os pontos finais orientado para o dispositivo são (independentemente do protocolo):

| Ponto Final | Funcionalidade |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Envie mensagens do dispositivo para a nuvem. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Receba mensagens da nuvem para o dispositivo. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Utilizar uma chave simétrica no registo de identidade

Ao utilizar a chave simétrica de uma identidade de dispositivo para gerar um token, o policyName (`skn`) é omitido elemento do token.

Por exemplo, um token criado para aceder a todas as funcionalidades do dispositivo deve ter os seguintes parâmetros:

* URI do recurso: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* chave de assinatura: qualquer chave simétrica para o `{device id}` identidade,
* nenhum nome de política
* hora de expiração.

Um exemplo utilizando a função Node.js anterior seria:

```nodejs
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

O resultado, o qual concede acesso a todas as funcionalidades para device1, seria:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> É possível gerar um token SAS, utilizando o .NET [Explorador de dispositivo] [ lnk-device-explorer] ferramenta ou plataforma, com base no nó [iothub explorer] [ lnk-iothub-explorer] utilitário da linha de comandos.

### <a name="use-a-shared-access-policy"></a>Utilizar uma política de acesso partilhado

Quando cria um token de uma política de acesso partilhado, defina o `skn` campo para o nome da política. Esta política tem de conceder a **DeviceConnect** permissão.

Os dois cenários principais para utilizar políticas de acesso partilhado para aceder à funcionalidade do dispositivo são:

* [gateways de protocolo de nuvem][lnk-endpoints],
* [Serviços de token] [ lnk-custom-auth] utilizada para implementar os esquemas de autenticação personalizado.

Uma vez que a política de acesso partilhado, potencialmente, pode conceder acesso a ligar-se como qualquer dispositivo, é importante utilizar o URI do recurso correto durante a criação de tokens de segurança. Esta definição é particularmente importante para os serviços de token, que tem de analisar o token para um dispositivo específico utilizando o URI do recurso. Este ponto é menos relevante para gateways de protocolo, que já são mediating tráfego para todos os dispositivos.

Por exemplo, um serviço de tokens utilizando previamente criadas partilhado a política de acesso denominada **dispositivo** criaria um token com os seguintes parâmetros:

* URI do recurso: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* chave de assinatura: uma das chaves do `device` política,
* nome da política: `device`,
* hora de expiração.

Um exemplo utilizando a função Node.js anterior seria:

```nodejs
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

O resultado, o qual concede acesso a todas as funcionalidades para device1, seria:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Um gateway de protocolo poderia utilizar o mesmo token para todos os dispositivos, basta definir o URI do recurso para `myhub.azure-devices.net/devices`.

### <a name="use-security-tokens-from-service-components"></a>Utilize tokens de segurança de componentes do serviço

Componentes do serviço apenas podem gerar tokens de segurança através de políticas de acesso partilhado conceder as permissões adequadas, conforme explicado anteriormente.

Segue-se as funções de exposto nos pontos finais:

| Ponto Final | Funcionalidade |
| --- | --- |
| `{iot hub host name}/devices` |Criar, atualizar, obter e eliminar as identidades de dispositivo. |
| `{iot hub host name}/messages/events` |Receba mensagens dispositivo-nuvem. |
| `{iot hub host name}/servicebound/feedback` |Receba comentários para as mensagens da nuvem para o dispositivo. |
| `{iot hub host name}/devicebound` |Envie mensagens da nuvem para o dispositivo. |

Por exemplo, um serviço gerar utilizando previamente criadas partilhado política de acesso denominada **registryRead** criaria um token com os seguintes parâmetros:

* URI do recurso: `{IoT hub name}.azure-devices.net/devices`,
* chave de assinatura: uma das chaves do `registryRead` política,
* nome da política: `registryRead`,
* hora de expiração.

```nodejs
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

O resultado, o qual pretende conceder acesso de leitura a todas as identidades de dispositivo, seria:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Suportado certificados x. 509

Pode utilizar qualquer certificado x. 509 para autenticar um dispositivo com o IoT Hub. Certificados incluem:

* **Um certificado x. 509 existente**. Um dispositivo pode já ter um certificado x. 509 com associados. O dispositivo pode utilizar este certificado para autenticar com o IoT Hub.
* **A Self-gerados e X-509 certificado autoassinado**. Um fabricante do dispositivo ou implementador interna, pode gerar estes certificados e armazenar a chave privada correspondente (e certificados) no dispositivo. Pode utilizar ferramentas como [OpenSSL] [ lnk-openssl] e [Windows SelfSignedCertificate] [ lnk-selfsigned] utilitário para esta finalidade.
* **Certificado assinado para AC de x. 509**. Para identificar um dispositivo e autenticá-lo com o IoT Hub, pode utilizar um certificado x. 509 gerados e assinado por uma autoridade de certificação (AC). IoT Hub apenas verifica que o thumbprint apresentado correspondente ao thumbprint configurado. IotHub não valida a cadeia de certificados.

Um dispositivo pode utilizar um certificado x. 509 ou um token de segurança para autenticação, mas não ambos.

### <a name="register-an-x509-certificate-for-a-device"></a>Registe um certificado x. 509 para um dispositivo

O [SDK do serviço do Azure IoT para c#] [ lnk-service-sdk] (versão 1.0.8+) suporta o registo de um dispositivo que utiliza um certificado x. 509 para autenticação. Outras APIs, como de importação/exportação dos dispositivos também suportam certificados x. 509.

### <a name="c-support"></a>C\# suporte

O **RegistryManager** classe fornece uma forma programática para registar um dispositivo. Em particular, a **AddDeviceAsync** e **UpdateDeviceAsync** métodos permitem-lhe registar e atualização de um dispositivo no registo de identidade do IoT Hub. Estes dois métodos recolhem uma **dispositivo** instância como entrada. O **dispositivo** classe inclui um **autenticação** propriedade permite-lhe especificar thumbprints de certificado x. 509 primários e secundários. O thumbprint representa um hash SHA-1 do certificado x. 509 (armazenado utilizando binário de codificação DER). Tem a opção de especificar um thumbprint primário ou um thumbprint secundário ou ambos. Thumbprints primários e secundários são suportados para processar cenários de rollover de certificado.

> [!NOTE]
> IoT Hub não necessita ou armazenar o certificado x. 509 completo, apenas o thumbprint.

Eis um exemplo C\# fragmento de código para registar um dispositivo ao utilizar um certificado x. 509:

```csharp
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "921BC9694ADEB8929D4F7FE4B9A3A6DE58B0790B"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Utilizar um certificado x. 509 durante as operações de tempo de execução

O [dispositivos de IoT do Azure SDK para .NET] [ lnk-client-sdk] (versão 1.0.11+) suporta a utilização de certificados x. 509.

### <a name="c-support"></a>C\# suporte

A classe **DeviceAuthenticationWithX509Certificate** suporta a criação de **DeviceClient** instâncias a utilizar um certificado x. 509. O certificado x. 509 tem de estar no formato PFX (também denominada PKCS #12) que inclui a chave privada.

Eis um fragmento de código de exemplo:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-authentication"></a>Autenticação do dispositivo personalizada

Pode utilizar o IoT Hub [registo de identidade] [ lnk-identity-registry] para configurar as credenciais de segurança por dispositivo e utilizar o controlo de acesso [tokens][lnk-sas-tokens]. Se uma solução de IoT já tem um esquema de registo e/ou a autenticação de identidade personalizada, considere criar um *token serviço* integrar esta infraestrutura com o IoT Hub. Desta forma, pode utilizar outras funcionalidades de IoT na sua solução.

Um serviço de tokens é um serviço em nuvem personalizado. Utiliza um IoT Hub *partilhado a política de acesso* com **DeviceConnect** permissões para criar *no âmbito do dispositivo* tokens. Estes tokens permitem que um dispositivo ligar ao seu IoT hub.

![Passos do padrão de serviço de tokens][img-tokenservice]

Eis os passos principais do padrão de serviço de token:

1. Criar uma política de acesso partilhado do IoT Hub com **DeviceConnect** permissões para o seu IoT hub. Pode criar esta política no [portal do Azure] [ lnk-management-portal] ou através de programação. O serviço de token utiliza esta política para assinar os tokens cria.
1. Quando um dispositivo tem de aceder ao seu IoT hub, solicita um token assinado entre o serviço de token. O dispositivo pode autenticar com o seu esquema de autenticação/registo de identidade personalizada para determinar a identidade de dispositivo que utiliza o serviço de tokens para criar o token.
1. O serviço de token devolve um token. O token é criado utilizando `/devices/{deviceId}` como `resourceURI`, com `deviceId` como o dispositivo que está a ser autenticado. O serviço de token utiliza a política de acesso partilhado ao construir o token.
1. O dispositivo utiliza o token diretamente com o IoT hub.

> [!NOTE]
> Pode utilizar a classe de .NET [SharedAccessSignatureBuilder] [ lnk-dotnet-sas] ou a classe do Java [IotHubServiceSasToken] [ lnk-java-sas] para criar um token no seu serviço de token.

O serviço de tokens pode definir a expiração do token conforme pretendido. Quando o token expira, o IoT hub servidores a ligação do dispositivo. Em seguida, o dispositivo tem de pedir um novo token do serviço de token. Uma hora de expiração curto aumenta a carga no dispositivo e o serviço de tokens.

Para um dispositivo ligar ao seu hub, deve ainda adicioná-lo para o registo de identidade do IoT Hub — apesar do dispositivo está a utilizar um token e não uma chave de dispositivo para estabelecer a ligação. Por conseguinte, pode continuar a utilizar o controlo de acesso por dispositivo ativando ou desativando identidades de dispositivo no [registo de identidade][lnk-identity-registry]. Esta abordagem Mitigam os riscos de utilização de tokens com tempos de tempo de expiração.

### <a name="comparison-with-a-custom-gateway"></a>Comparação com um gateway personalizado

O padrão de serviço de tokens é a forma recomendada para implementar um esquema de autenticação/registo de identidade personalizada com o IoT Hub. Este padrão é recomendado porque o IoT Hub continua a processar a maioria do tráfego de solução. No entanto, se o esquema de autenticação personalizado, por isso, é intertwined com o protocolo, poderá ser necessário um *gateway personalizado* processar todo o tráfego. Um exemplo de cenário é utilizar[Transport Layer Security (TLS) e chaves pré-partilhadas (PSKs)][lnk-tls-psk]. Para obter mais informações, consulte o [gateway de protocolo] [ lnk-protocols] artigo.

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência, poderá obter mais informações sobre como controlar o acesso ao seu IoT hub.

## <a name="iot-hub-permissions"></a>Permissões de IoT Hub

A tabela seguinte lista as permissões que pode utilizar para controlar o acesso ao seu IoT hub.

| Permissão | Notas |
| --- | --- |
| **RegistryRead** |Concede acesso de leitura ao registo de identidade. Para obter mais informações, consulte [registo de identidade][lnk-identity-registry]. <br/>Esta permissão é utilizada pelos serviços de nuvem de back-end. |
| **RegistryReadWrite** |Concede de leitura e escrita ao registo de identidade. Para obter mais informações, consulte [registo de identidade][lnk-identity-registry]. <br/>Esta permissão é utilizada pelos serviços de nuvem de back-end. |
| **ServiceConnect** |Concede acesso à nuvem orientado para o serviço de comunicação e os pontos finais de monitorização. <br/>Concede permissão para receber mensagens dispositivo-nuvem, enviar mensagens da nuvem para o dispositivo e obter o ' em que as confirmações correspondente do entrega. <br/>Concede permissão para obter as confirmações de entrega para o ficheiro carrega. <br/>Concede permissão para aceder a dispositivos duplos para atualizar as etiquetas e propriedades pretendidas, obter as propriedades comunicadas e executar consultas. <br/>Esta permissão é utilizada pelos serviços de nuvem de back-end. |
| **DeviceConnect** |Concede acesso a pontos finais de orientado para o dispositivo. <br/>Concede permissão para enviar mensagens do dispositivo para nuvem e receber mensagens da nuvem para o dispositivo. <br/>Concede permissão para efetuar o carregamento de ficheiros a partir de um dispositivo. <br/>Concede permissão para receber notificações de propriedade do dispositivo duplo pretendida e atualizar o dispositivo duplo comunicadas propriedades. <br/>Concede permissão para executar o ficheiro carrega. <br/>Esta permissão é utilizado pelos dispositivos. |

## <a name="additional-reference-material"></a>Material de referência adicionais

Outros tópicos de referência no guia de programadores do IoT Hub incluem:

* [Pontos finais de IoT Hub] [ lnk-endpoints] descreve os vários pontos finais que cada IoT hub expõe para operações de gestão e de tempo de execução.
* [Limitação e quotas] [ lnk-quotas] descreve as quotas e limitação comportamentos que se aplicam ao serviço do IoT Hub.
* [Azure SDKs IoT do serviço e dispositivo] [ lnk-sdks] indica o idioma de vários SDKs que pode utilizar ao desenvolver aplicações de serviço e dispositivo que interagem com o IoT Hub.
* [A linguagem de consulta do IoT Hub] [ lnk-query] descreve o idioma de consulta pode utilizar para obter informações a partir do IoT Hub sobre os dispositivos duplos e tarefas.
* [Suporte do IoT Hub MQTT] [ lnk-devguide-mqtt] fornece mais informações sobre o suporte do IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu como controlar o acesso do IoT Hub, poderá estar interessado nos seguintes tópicos do guia de programadores do IoT Hub:

* [Utilizar dispositivos duplos para sincronizar o estado e configurações][lnk-devguide-device-twins]
* [Invocar um método direto num dispositivo][lnk-devguide-directmethods]
* [Agenda de tarefas em vários dispositivos][lnk-devguide-jobs]

Se pretender experimentar alguns dos conceitos descritos neste artigo, consulte os seguintes tutoriais do IoT Hub:

* [Introdução ao IoT Hub do Azure][lnk-getstarted-tutorial]
* [Como enviar mensagens da nuvem para o dispositivo com o IoT Hub][lnk-c2d-tutorial]
* [Como processar mensagens do dispositivo para a nuvem do IoT Hub][lnk-d2c-tutorial]

<!-- links and images -->

[img-tokenservice]: ./media/iot-hub-devguide-security/tokenservice.png
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-openssl]: https://www.openssl.org/
[lnk-selfsigned]: https://technet.microsoft.com/library/hh848633

[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-sas-tokens]: iot-hub-devguide-security.md#security-tokens
[lnk-amqp]: https://www.amqp.org/
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-management-portal]: https://portal.azure.com
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.auth._iot_hub_service_sas_token
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-custom-auth]: iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: iot-hub-devguide-security.md#supported-x509-certificates
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service
[lnk-client-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
