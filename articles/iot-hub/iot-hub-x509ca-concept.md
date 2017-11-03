---
title: "Conceitos de segurança de x. 509 do Azure IoT Hub | Microsoft Docs"
description: "Certificados de autoridade de fabrico de dispositivos de IoT e autenticação de certificados de conceito - compreender o valor x. 509."
services: iot-hub
documentationcenter: .net
author: eustacea
manager: arjmands
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 34d4be431b76d5ba8258d932cb21ed6f4818863e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Compreensão conceptual a x. 509 de certificados da AC na indústria IoT

Este artigo descreve o valor da utilização de certificados de autoridade (AC) de certificado x. 509 no fabrico de dispositivos de IoT e autenticação ao IoT Hub.  Inclui informações sobre alimentação encadeiam configuração e realça as vantagens.

Este artigo descreve:

* Quais são os certificados da AC de x. 509 e como obtê-las
* Como registar o seu certificado de AC de x. 509 ao IoT Hub
* Como configurar um fabrico fornecer cadeia para autenticação baseada na AC de x. 509
* A forma como os dispositivos assinada com a AC de x. 509 estabelecer ligação ao IoT Hub

## <a name="overview"></a>Descrição geral

Autenticação de autoridade de certificação (CA) de x. 509 é uma abordagem para autenticar dispositivos ao IoT Hub, utilizando um método que simplifica significativamente identidade criação e o ciclo de vida do gestão de dispositivos na cadeia de fornecimento.

Um atributo distintivo da autenticação do x. 509 AC é uma relação um-para-muitos que tem um certificado de AC com os respetivos dispositivos a jusante.  Esta relação ativa o registo de qualquer número de dispositivos para o IoT Hub ao registar um certificado x. 509 AC uma vez, caso contrário, certificados de dispositivo exclusivo têm de ser previamente registados para todos os dispositivos antes de um dispositivo se possa ligar. Esta relação um-para-muitos também simplifica as operações de gestão de ciclo de vida de certificados de dispositivo.

Outro atributo importante da autenticação do x. 509 AC é simplificação do logística da cadeia de fornecimento.  Autenticação segura de dispositivos requer que cada dispositivo contém um segredo exclusivo como uma chave como base de confiança. Autenticação baseada em certificados, este segredo é uma chave privada. Um dispositivo típico fabrico fluxo envolve vários passos e conservadores de chaves.  Em segurança gerir chaves privadas de dispositivos entre vários conservadores de chaves e manutenção de confiança são difícil e dispendioso.  Utilizar autoridades de certificação resolve este problema por cada conservador para uma cadeia de fidedignidade de criptografia de assinatura em vez de entrusting-los com as chaves privadas do dispositivo.  Cada conservador inicia, por sua vez, dispositivos no respetivo passo do respetivo processo do fluxo de fabrico.  O resultado geral é uma cadeia de fornecimento ideal com accountability incorporada através da utilização da cadeia de criptografia de confiança.  É vale a pena realçar que este processo gera a maior segurança quando dispositivos proteger as respetivas chaves privadas exclusivos.  Para este fim, recomendamos vivamente a utilização de Hardware seguro módulos (HSM) capaz de gerar internamente chaves privadas que nunca irão ver claro do dia.

Este artigo oferece uma vista de ponto a ponto de utilizar a autenticação de x. 509 AC, do programa de configuração de cadeia de fornecimento a ligação do dispositivo, ao efetuar a utilização de um exemplo do mundo real a solidificar compreender.

## <a name="introduction"></a>Introdução

O certificado x. 509 AC é um certificado digital cujo marcador de posição pode assinar outros certificados.  Este certificado digital é x. 509 porque está em conformidade com um certificado de formatação padrão prescrita pelo padrão de 5280 de RFC da IETF e é uma autoridade de certificação (AC), porque o marcador de posição pode assinar outros certificados.

A utilização da AC de x. 509 melhor entendida em relação a um exemplo em concreto.  Considere empresa-X, um maker de Smart-X-Widgets concebido para instalação profissionais. Empresa X outsources fabrico e instalação.  -Contrai fabricante fábrica-Y para manufacture Smart-X-Widgets e o fornecedor de serviços técnico Z para instalar. Empresa X desires que Smart-X-Widget diretamente é fornecido de fábrica-Y a Z técnico para instalação e que ligar diretamente à empresa-X's instância do IoT Hub após a instalação sem intervenção adicional do X da empresa. Para se isto acontecer, a empresa X tem de concluir algumas operações de uma única configuração prime Smart-X-Widget para ligação automática.  Com o cenário de ponto a ponto em mente, o resto deste artigo está estruturado da seguinte forma:

* Adquirir o certificado de AC de x. 509

* Registar certificado de AC de x. 509 ao IoT Hub

* Dispositivos de início de sessão para uma cadeia de fidedignidade de certificados

* Ligação do dispositivo

## <a name="acquire-the-x509-ca-certificate"></a>Adquirir o certificado de AC de x. 509

Empresa X tem a opção de comprar um certificado de AC de x. 509 de uma autoridade de certificação de raiz pública ou criar um através de um processo autoassinado.  Uma opção ideal seria detrimento dos outros, dependendo do cenário de aplicação.  Independentemente da opção, o processo envolve dois passos fundamentais, gerar um par de chaves públicas/privadas e a chave pública de assinatura para um certificado.

![fluxo de img csr](./media/csr-flow.png)

Detalhes sobre como efetuar estes passos diferem com vários fornecedores de serviços.

### <a name="purchasing-an-x509-ca-certificate"></a>Comprar um certificado de AC de x. 509

Comprar um certificado de AC tem a vantagem de ter um ato de AC de raiz bem conhecido como uma conta de terceiros fidedigna para vouch para a legitimidade dispositivos de IoT quando os dispositivos se ligam. Empresa X deverá escolher esta opção se que pretendem Smart-X-Widget para interagir com terceiros produtos ou serviços após a ligação inicial ao IoT Hub.

Para comprar um certificado de AC de x. 509, empresa X deverá escolher um fornecedor de serviços de certificados de raiz. Uma pesquisa na internet para a frase de AC de raiz irá produzir boas clientes potenciais clientes potenciais.  A AC de raiz guiará empresa X sobre como criar o par de chaves públicas/privadas e como gerar um certificado de assinatura do pedido (CSR) para os respetivos serviços.  Um CSR é o processo de solicitar um certificado de uma autoridade de certificação formal.  O resultado esta compra é um certificado para utilização como um certificado de autoridade.  O certificado fornecido a ubiquidade do certificados x. 509, é provável que ter sido corretamente formatado para RFC 5280 da IETF padrão.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Criar um certificado autoassinado x. 509 AC

O processo para criar um certificado autoassinado x. 509 AC é semelhante à aquisição com a exceção que envolvem um signatário de terceiros, como a autoridade de certificação de raiz. No nosso exemplo, a empresa X assinará o certificado de autoridade em vez de uma autoridade de certificação de raiz.  X da empresa pode escolher esta opção para testar até que o se estiverem prontos para comprar um certificado de autoridade. X da empresa também pode utilizar um certificado de AC de x. 509 autoassinado na produção, se Smart-X-Widget não se destina para ligar a todos os serviços de terceiros fora do IoT Hub.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Registar o certificado x. 509 ao IoT Hub

Empresa X tem de registar a AC de x. 509 ao IoT Hub onde irá servir para autenticar Smart-X-Widgets, como se ligam.  Este é um processo único, que permite a capacidade autenticar e gerir a qualquer número de dispositivos de Smart-X-Widget.  Este processo é o uso devido a uma relação um-para-muitos entre dispositivos e a autoridade de certificação e também constitui uma das principais vantagens de utilizar o método de autenticação de AC de x. 509.  A alternativa é carregar thumbprints de certificado individuais para todas as dispositivos de Smart-X-Widget, deste modo, a adição aos custos operacionais.

Registar o certificado da AC de x. 509 é um processo de dois passos, o carregamento do certificado e o certificado prova de posse.

![fluxo de pop img](./media/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>Carregamento do certificado de AC de x. 509

O certificado da AC de x. 509 do processo de carregamento é o mesmo, carregue o certificado de AC para o IoT Hub.  IoT Hub espera que o certificado num ficheiro. Empresa X simplesmente carrega o ficheiro de certificado. O ficheiro de certificado não deve em circunstância alguma contém quaisquer chaves privadas.  Melhores práticas de normas que rege a infraestrutura de chaves públicas (PKI) exige deste conhecimentos da empresa-X's privada neste caso reside exclusivamente no X da empresa.

### <a name="proof-of-possession-of-the-certificate"></a>Prova de posse do certificado

O certificado da AC de x. 509, tal como qualquer certificado digital, é públicas informações que são suscetíveis de eavesdropping.  Como tal, um eavesdropper poderá intercetar um certificado e tenta carregá-lo como os seus próprios.  No nosso exemplo, gostaria que o IoT Hub para se certificar de que o certificado de AC que x da empresa está a carregar realmente pertence x da empresa. Faz para por um desafio de empresa-X para proof que de facto que tenha o certificado através de um [fluxo prova de posse (PoP)](https://tools.ietf.org/html/rfc5280#section-3.1). O fluxo de prova de posse envolve IoT Hub gerar um número aleatório sejam assinados pela empresa-X utilizando a respetiva chave privada.  Se empresa X seguido de melhores práticas de PKI e protegida de chave privada, em seguida, apenas estes seria na posição responder corretamente para o desafio de prova de posse.  IoT Hub avança para registar o certificado da AC de x. 509 após uma resposta de desafio a prova de posse com êxito.

Uma resposta com êxito para o desafio de prova de posse do IoT Hub conclui o registo de AC de x. 509.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Dispositivos de início de sessão para uma cadeia de fidedignidade de certificados

IoT requer que cada dispositivo tenha uma identidade exclusiva.  Estas identidades são os certificados de formulário para esquemas de autenticação baseada em certificado.  No nosso exemplo, isto significa que cada Smart-X-Widget precisa de ter um certificado de dispositivo exclusivo.  Como empresa X configurar para esta na respetiva cadeia de fornecimento?

Uma forma ir sobre esta é a geração prévia de certificados para Smart-X-Widgets e entrusting conhecimento de correspondentes chaves privadas de dispositivo exclusivo com parceiros de cadeia de fornecimento.  Para a empresa-X, isto significa entrusting Y de fábrica e técnico-Z.  Apesar de ser um método válido, vem com desafios que devem ser ultrapassados para garantir a confiança da seguinte forma:

1. Ter de partilhar chaves privadas do dispositivo com parceiros de cadeia de fornecimento, para além da ignorar a PKI de melhores práticas de nunca partilha as chaves privadas, faz com que a criação de confiança na cadeia de fornecimento dispendiosas.  Significa sistemas capital como gabinetes seguros para chaves privadas da próxima dispositivo e processos como as auditorias de segurança periódica tem de ser instalado.  Ambos adicionar custo para a cadeia de fornecimento.

2. Explicação de forma segura de dispositivos na cadeia de fornecimento e gerir mais tarde em implementação torna-se uma tarefa de um para um para cada par de chaves para o dispositivo do ponto de geração do dispositivo certificado exclusivo (chave privada, por conseguinte,) para extinção de dispositivos. Esta sequência exclui a gestão de grupo de dispositivos, a menos que o conceito de grupos explicitamente está incorporado no processo de examinar. Gestão de ciclo de vida do dispositivo de contabilização e segura, por conseguinte, torna-se uma carga pesada de operações.  No nosso exemplo, a empresa X seria tenha este fardo.

Autenticação de certificados de AC de x. 509 oferece soluções elegante para afore listados desafios através da utilização de cadeias de certificados.  Uma cadeia de certificados resulta de uma AC de assinatura de uma AC intermediária que por sua vez assina outra AC intermediária e, por isso, passa até que uma AC intermediária final inicia sessão num dispositivo.  No nosso exemplo, a empresa X inicia fábrica-Y, o que por sua vez inicia técnico-Z, finalmente, inicia Smart-X-Widget.

![img cert-cadeia hierarquia](./media/cert-chain-hierarchy.png)

Acima cascade dos certificados na cadeia apresenta a lógica mão convocaremos da autoridade.  Cadeias de alimentação muitos siga esta lógica mão-desativar em cada AC intermediária obtém iniciou a sessão na cadeia durante a receção de todos os certificados de AC a montante e a última AC intermediária inicia, finalmente, cada dispositivo e inserir todos os certificados de autoridade da cadeia de no dispositivo. Isto é comum quando a empresa de fabrico de contrato com uma hierarquia de factories commissions uma fábrica específica para fazer de fabrico.  Enquanto a hierarquia pode ser vários níveis profundo (por exemplo, por geografia/produto tipo/fabrico linha), apenas a fábrica no final obtém a interagir com o dispositivo, mas a cadeia é mantida na parte superior da hierarquia.

Cadeias alternativas podem ter diferentes AC intermediária interagir com o dispositivo no qual caso a interação de AC com o dispositivo injects conteúdo de cadeia de certificados nesse momento.  Modelos híbridos também são possíveis onde apenas algumas da AC tem física interação com o dispositivo.

No nosso exemplo, Y de fábrica e técnico Z interagem com Smart-X-Widget.  Enquanto X da empresa é a proprietária Smart-X-Widget,-lo, na verdade, não fisicamente interagir com ele na cadeia de fornecimento de todo.  A cadeia de certificados de confiança para Smart-X-Widget, por conseguinte, compõem X da empresa Y Factory inicia por sua vez técnico-Z, em seguida, irá fornecer a assinatura final aos Smart-X-Widget de assinatura. A instalação Smart-X-widget e fabrico compõem Y de fábrica e utilizar os respetivos certificados de AC intermediária para assinar todas as Smart-X-Widgets técnico-Z. O resultado final este processo completo é Smart-X-Widgets com certificados de dispositivo exclusivo e a cadeia de certificados de confiança que pretender dos certificados de AC de empresa-X.

![img cert-mfr cadeia](./media/cert-mfr-chain.png)

Este é um bom ponto para rever o valor do método x. 509 AC.  Em vez de pré-geração e manipulação desativar certificados para cada Smart-X-Widget para a cadeia de fornecimento, empresa X entregar apenas fábrica-Y de iniciar sessão uma vez.  Em vez de ter que controlar todos os dispositivos ao longo do ciclo de vida de dispositivos, X da empresa não pode controlar e gerir dispositivos através de grupos naturalmente surgir do processo de cadeia de fornecimento, por exemplo, dispositivos instalados por Z técnico depois de Julho de algumas ano.

Última mas não pelo menos, o método de AC de autenticação infuses accountability segura para o dispositivo a cadeia de fornecimento de fabrico. Devido ao processo de cadeia de certificados, as ações de todos os membros na cadeia é criptograficamente registado e verificável.

Este processo depende certos pressupostos que tem de ser anexados por questões de exaustividade.  Requer a criação independente de dispositivo exclusivo públicas/privadas par de chaves e que a chave privada protegidos dentro do dispositivo.  Felizmente, silicon segura chips sob a forma de Hardware seguro módulos (HSM) com capacidade de internamente gerar chaves e proteger chaves privadas existem.  X da empresa basta adicionar um desses chips fatura de componente da Smart-X-Widget lista de materiais.

## <a name="device-connection"></a>Ligação do dispositivo

Secções anteriores acima tem foi criar cópias de segurança para a ligação do dispositivo.  Registando simplesmente um certificado de AC de x. 509 ao IoT Hub uma hora, como potencialmente milhões de dispositivos ligar e obter autenticado desde a primeira vez?  Simples; através do mesmo carregamento do certificado e o fluxo de prova de posse encontrámos anteriormente com registar o certificado da AC de x. 509.

Dispositivos fabricados para autenticação de x. 509 AC estão equipados com certificados de dispositivo exclusivo e uma cadeia de certificados do respetivo respetiva cadeia de fornecimento de fabrico.  Ligação de dispositivos, mesmo para muito pela primeira vez, ocorre um processo de dois passos: carregamento de cadeia e prova de posse do certificado.

Durante o carregamento da cadeia de certificados, o dispositivo carrega o respetivo certificado exclusivo do dispositivo, juntamente com a cadeia de certificados instalado no mesmo ao IoT Hub.  Utilizar o certificado da AC de x. 509 previamente registado, IoT Hub pode validar criptograficamente duas coisas, que a cadeia de certificados carregados é internamente consistente e de que a cadeia foi teve origem pelo proprietário do certificado da AC de x. 509 válido.  Foi apenas com o processo de registo de AC de x. 509, IoT Hub deverá iniciar um processo de resposta de desafio prova de posse ascertain que a cadeia e, por conseguinte, um certificado de dispositivo, na verdade, pertence ao dispositivo carregá-lo.  Isto é feito através da geração de um desafio aleatório sejam assinados pelo dispositivo utilizando a respetiva chave privada para validação pelo IoT Hub.   Uma resposta com êxito aciona o IoT Hub para aceitar o dispositivo como autênticas e conceder esta ligação.

No nosso exemplo, cada Smart-X-Widget seria carregar o seu certificado de único dispositivo juntamente com certificados Y de fábrica e a AC de x. 509 do técnico Z e, em seguida, responder ao desafio a prova de posse do IoT Hub.

![img-dispositivo-pop-fluxo](./media/device-pop-flow.png)

Tenha em atenção que a base de confiança assenta proteger chaves privadas, incluindo as chaves privadas do dispositivo.  Iremos não é possível, por conseguinte, realçar suficiente a importância da silicon segura chips sob a forma de Hardware seguro módulos (HSM) para proteger chaves privadas do dispositivo e a geral melhor prática nunca partilha quaisquer chaves privadas, como uma fábrica entrusting outra com o chave privada.

