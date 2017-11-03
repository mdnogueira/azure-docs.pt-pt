# <a name="secure-your-iot-deployment"></a>Proteger a sua implementação de IoT
Este artigo fornece o próximo nível de detalhe para proteger a infraestrutura baseada no Azure IoT Internet das coisas (IoT). Contém ligações para detalhes de nível de implementação para configurar e implementar cada componente. Também fornece comparações e opções de entre vários métodos concorrentes.

Proteger a implementação do IoT do Azure pode ser dividida nas seguintes áreas de três segurança:

* **Segurança de dispositivos**: proteger os dispositivos de IoT enquanto está implementada no caráter universal.
* **Segurança de ligação**: garantir que todos os dados transmitidos entre o dispositivo IoT e o IoT Hub é confidencial e prova de adulteração.
* **Segurança de nuvem**: fornecer um meio para proteger dados enquanto se desloca pelas e são armazenado na nuvem.

![Três áreas de segurança][img-overview]

## <a name="secure-device-provisioning-and-authentication"></a>Proteger o aprovisionamento de dispositivos e autenticação
O Azure IoT Suite protege os dispositivos de IoT através dos seguintes dois métodos:

* Ao fornecer uma chave de identidade exclusiva (tokens de segurança) para cada dispositivo, o que pode ser utilizado pelo dispositivo para comunicar com o IoT Hub.
* Ao utilizar um dispositivo [certificado x. 509] [ lnk-x509] e a chave privada como um meio para autenticar o dispositivo ao IoT Hub. Este método de autenticação garante que a chave privada no dispositivo não é conhecida fora do dispositivo em qualquer altura, fornecer um nível mais elevado de segurança.

O método de token de segurança fornece autenticação para cada chamada efetuada pelo dispositivo ao IoT Hub ao associar a chave simétrica para cada chamada. Autenticação baseada em x. 509 permite a autenticação de um dispositivo IoT na camada física como parte do estabelecimento de ligação TLS. O método baseado em tokens de segurança pode ser utilizado sem a autenticação de x. 509 que é um padrão menos seguro. A escolha entre os dois métodos principalmente é ditada pelo como protegem o dispositivo autenticação tem de ser e disponibilidade de armazenamento segura no dispositivo (para armazenar a chave privada de forma segura).

## <a name="iot-hub-security-tokens"></a>Tokens de segurança do Hub IoT
IoT Hub utiliza os tokens de segurança para autenticar dispositivos e serviços para evitar o envio de chaves na rede. Além disso, tokens de segurança estão limitados a validade de tempo e o âmbito. Os SDKs IoT do Azure gerar automaticamente tokens sem necessidade de nenhuma configuração especial. No entanto, alguns cenários, necessitam do utilizador gerar e utilizar diretamente tokens de segurança. Estes incluem a utilização direta do analisa MQTT, AMQP ou HTTP ou a implementação padrão de serviço de tokens.

Podem encontrar mais detalhes sobre a estrutura da sua utilização e o token de segurança nos seguintes artigos:

* [Estrutura de token de segurança][lnk-security-tokens]
* [Utilização de tokens SAS como um dispositivo][lnk-sas-tokens]

Cada IoT Hub tem um [registo de identidade] [ lnk-identity-registry] que podem ser utilizados para criar recursos por dispositivo de serviço, por exemplo, uma fila que contém mensagens de nuvem para o dispositivo em trânsito e para permitir o acesso aos pontos finais do orientado para o dispositivo. O registo de identidade do IoT Hub fornece armazenamento seguro de identidades de dispositivo e as chaves de segurança para uma solução. Individuais ou grupos de identidades de dispositivo podem ser adicionados a uma lista de permissões ou uma lista de bloqueios, ativar o controlo total sobre o acesso ao dispositivo. Os artigos seguintes fornecem mais detalhes sobre a estrutura do registo de identidade e operações suportadas.

[IoT Hub suporta os protocolos, como MQTT, AMQP e HTTP][lnk-protocols]. Cada um destes protocolos de forma diferente, utilize tokens de segurança do dispositivo IoT ao IoT Hub:

* AMQP: SASL simples e baseada em afirmações AMQP segurança ({policyName}@sas.root. { iothubName} no caso de tokens de nível do hub IoT; {"deviceId"} em caso de tokens no âmbito do dispositivo).
* MQTT: Ligar utiliza pacotes {"deviceId"} o {ClientId}, {IoThubhostname} / {"deviceId"} no **Username** token de campo e uma SAS no **palavra-passe** campo.
* HTTP: Token válido está no cabeçalho do pedido de autorização.

Registo de identidade do IoT Hub pode ser utilizado para configurar as credenciais de segurança por dispositivo e controlo de acesso. No entanto, se uma solução de IoT que já tem um investimento significativo num [esquema de registo e/ou a autenticação de identidade do dispositivo personalizada][lnk-custom-auth], podem ser integrados para uma infraestrutura existente com o IoT Hub através da criação de um serviço de tokens.

### <a name="x509-certificate-based-device-authentication"></a>Autenticação de dispositivos baseada em certificado x. 509
A utilização de um [certificado baseado nos dispositivos de x. 509] [ lnk-use-x509] e respetivos associado pública e privada par de chaves permite autenticação adicional na camada física. A chave privada é armazenada em segurança no dispositivo e não é detetável fora do dispositivo. O certificado x. 509 contém informações sobre o dispositivo, tal como ID de dispositivo e outros detalhes organizacionais. Uma assinatura do certificado é gerada utilizando a chave privada.

Fluxo de aprovisionamento de dispositivos de alto nível:

* Associe um identificador para um dispositivo físico – identidade de dispositivo e/ou o certificado x. 509 associados ao dispositivo durante dispositivo fabrico ou commissioning.
* Crie uma entrada correspondente de identidade do IoT Hub – identidade de dispositivo e informações de dispositivo associados no registo de identidade do IoT Hub.
* Armazene de maneira segura o thumbprint do certificado x. 509 no registo de identidade do IoT Hub.

### <a name="root-certificate-on-device"></a>Certificado de raiz no dispositivo
Ao estabelecer uma ligação de TLS segura com o IoT Hub, o dispositivo IoT autentica o IoT Hub com um certificado de raiz que faz parte do dispositivo SDK. Para o SDK do cliente do C o certificado está localizado na pasta "\\c\\certificados" na raiz do repositório. Embora estes certificados de raiz longa duração, ainda pode expirarem ou ser revogados. Se não houver nenhuma forma de atualizar o certificado no dispositivo, o dispositivo poderá não conseguir ligar subsequentemente ao IoT Hub (ou qualquer outro serviço de nuvem). Ter um meio para atualizar o certificado de raiz, depois do dispositivo de IoT é implementado será eficazmente mitigar este risco.

## <a name="securing-the-connection"></a>Proteger a ligação
Ligação de Internet entre o dispositivo IoT e o IoT Hub está protegida com a norma de segurança de camada de transporte (TLS). Azure IoT suporta [TLS 1.2][lnk-tls12], TLS 1.1 e TLS 1.0, esta ordem. Suporte para TLS 1.0 é fornecido para apenas a compatibilidade com versões anteriores. Recomenda-se para utilizar TLS 1.2, uma vez que fornece a maior segurança.

Azure IoT Suite suporta os seguintes conjuntos de cifras, por esta ordem.

| Conjunto de cifras | comprimento |
| --- | --- |
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA384 (0xc028) ECDH secp384r1 (eq. FS 7680 bits RSA) |256 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0xc027) ECDH secp256r1 (eq. FS 3072 bits RSA) |128 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA (0xc014) ECDH secp384r1 (eq. FS 7680 bits RSA) |256 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA (0xc013) ECDH secp256r1 (eq. FS 3072 bits RSA) |128 |
| TLS\_RSA\_WITH\_AES\_256\_GCM\_SHA384 (0x9d) |256 |
| TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256 (0x9c) |128 |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA256 (0x3d) |256 |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0x3c) |128 |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA (0x35) |256 |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA (0x2f) |128 |
| TLS\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA (0xa) |112 |

## <a name="securing-the-cloud"></a>Proteger a nuvem
IoT Hub do Azure permite a definição de [políticas de controlo de acesso] [ lnk-protocols] para cada chave de segurança. Utiliza o seguinte conjunto de permissões para conceder acesso a cada um dos pontos finais do IoT Hub. Permissões de limitam o acesso a um IoT Hub com base na funcionalidade.

* **RegistryRead**. Concede acesso de leitura ao registo de identidade. Para obter mais informações, consulte [registo de identidade][lnk-identity-registry].
* **RegistryReadWrite**. Concede de leitura e escrita ao registo de identidade. Para obter mais informações, consulte [registo de identidade][lnk-identity-registry].
* **ServiceConnect**. Concede acesso à nuvem orientado para o serviço de comunicação e os pontos finais de monitorização. Por exemplo, concede a permissão para serviços de cloud de back-end para receber mensagens dispositivo-nuvem, enviar mensagens da nuvem para o dispositivo e obter o ' em que as confirmações correspondente do entrega.
* **DeviceConnect**. Concede acesso a pontos finais de orientado para o dispositivo. Por exemplo, concede a permissão para enviar mensagens do dispositivo para nuvem e receber mensagens da nuvem para o dispositivo. Esta permissão é utilizado pelos dispositivos.

Existem duas formas de obter **DeviceConnect** permissões com o IoT Hub com [tokens de segurança][lnk-sas-tokens]: utilizar uma chave de identidade de dispositivo, ou uma chave de acesso partilhado. Além disso, é importante ter em atenção que todas as funcionalidades acessível a partir de dispositivos é exposta pelo estrutura em pontos finais com o prefixo `/devices/{deviceId}`.

[Componentes do serviço só é possível gerar tokens de segurança] [ lnk-service-tokens] utilizar partilhado de políticas de acesso conceder as permissões adequadas.

IoT Hub do Azure e outros serviços que podem fazer parte da solução de permitem a gestão de utilizadores que utilizam o Azure Active Directory.

Dados ingeridos pelo IoT Hub do Azure podem ser utilizados por uma variedade de serviços como o Azure Stream Analytics e armazenamento de Blobs do Azure. Estes serviços permitem o acesso de gestão. Leia mais sobre estes serviços e as opções disponíveis abaixo:

* [BD do Azure do Cosmos][lnk-cosmosdb]: um serviço de base de dados dimensionável e totalmente indexado para dados semiestruturados que gere os metadados para os dispositivos aprovisionar, como atributos, configuração e propriedades de segurança. BD do Azure do Cosmos oferece processamento elevado desempenho e débito elevado, o esquema agnóstico a indexação de dados e uma interface de consulta avançada do SQL Server.
* [O Azure Stream Analytics][lnk-asa]: fluxo em tempo real de processamento na nuvem que permite-lhe rapidamente desenvolver e implementar uma solução de análise de baixo custo para desvendar informações em tempo real a partir de dispositivos, sensores, infraestrutura e aplicações. Os dados a partir deste serviço completamente gerido podem Dimensionar para qualquer volume um débito elevado, latência baixa e resiliência.
* [Serviços de aplicações do Azure][lnk-appservices]: uma plataforma de nuvem para criar poderoso web apps e móveis que se ligam aos dados em qualquer lugar; na nuvem ou no local. Crie aplicações móveis cativantes para iOS, Android e Windows. Integre com o seu Software como serviço (SaaS) e aplicações empresariais out of box conectividade com dezenas de serviços baseados na nuvem e aplicações empresariais. O código no seu idioma favorito e IDE (.NET, Node.js, PHP, Python ou Java) para criar web apps e APIs mais rapidamente do que nunca.
* [As Logic Apps][lnk-logicapps]: funcionalidade o Logic Apps do App Service do Azure ajuda a integrar a sua solução de IoT aos seus sistemas de linha de negócio existentes e automatizar os processos de fluxo de trabalho. As Logic Apps permite aos programadores conceber fluxos de trabalho que começam com acionadores e, em seguida, executam uma série de passos — regras e ações que utilizam conectores poderosos para integrar com os processos empresariais. As Logic Apps oferece a conectividade de out of box uma vasta ecossistema de SaaS, baseado na nuvem e aplicações no local.
* [Armazenamento de Blobs do Azure][lnk-blob]: armazenamento de nuvem fiável e económica para os dados que enviam os seus dispositivos para a nuvem.

## <a name="conclusion"></a>Conclusão
Este artigo fornece detalhes de nível para estruturar e implementar uma infraestrutura de IoT utilizando o Azure IoT de descrição geral da implementação. Configuração de cada componente a ser segura é a chave de proteger a infraestrutura geral do IoT. As opções de design disponíveis no Azure IoT fornecem algum nível de flexibilidade e escolha; No entanto, cada escolha pode ter implicações de segurança. Recomenda-se que cada uma destas opções ser avaliadas através de uma avaliação de risco/custo.

[img-overview]: media/iot-secure-your-deployment/overview.png

[lnk-security-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure
[lnk-sas-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app
[lnk-identity-registry]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-protocols]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-custom-auth]: ../articles/iot-hub/iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: http://www.itu.int/rec/T-REC-X.509-201210-I/en
[lnk-use-x509]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-tls12]: https://tools.ietf.org/html/rfc5246
[lnk-service-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components
[lnk-cosmosdb]: https://azure.microsoft.com/services/cosmos-db/
[lnk-asa]: https://azure.microsoft.com/services/stream-analytics/
[lnk-appservices]: https://azure.microsoft.com/services/app-service/
[lnk-logicapps]: https://azure.microsoft.com/services/app-service/logic/
[lnk-blob]: https://azure.microsoft.com/services/storage/
