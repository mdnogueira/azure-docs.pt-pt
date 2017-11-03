---
title: "Descrição geral de segurança de AC de x. 509 do Azure IoT Hub | Microsoft Docs"
description: "Descrição geral - como autenticar dispositivos ao utilizar autoridades de certificação x. 509 do IoT Hub."
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
ms.openlocfilehash: 080c83fd0c34bdcb8978edf0ba4f783402a88b1f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Autenticação de dispositivo com certificados de AC de x. 509

Este artigo descreve como utilizar certificados de autoridade de certificado x. 509 (AC) para autenticar dispositivos que se ligam IoT Hub.  Este artigo irá aprender:

* Como obter um certificado de AC de x. 509
* Como registar o certificado da AC de x. 509 ao IoT Hub
* Como assinar dispositivos utilizando certificados de AC de x. 509
* Como os dispositivos assinados com a AC de x. 509 são autenticados

## <a name="overview"></a>Descrição geral

A funcionalidade de x. 509 AC permite a autenticação do dispositivo ao IoT Hub com uma autoridade de certificação (CA). Simplifica bastante o processo de inscrição de dispositivos inicial e logística da cadeia de fornecimento durante fabrico do dispositivo. [Saiba mais neste artigo de cenário sobre o valor da utilização de certificados de AC de x. 509](iot-hub-x509ca-concept.md) para autenticação do dispositivo.  Aconselhamo-lo para ler este artigo de cenário antes de continuar, como explica por que razão os passos que se seguem existem.

## <a name="prerequisite"></a>Pré-requisito

Utilizar a funcionalidade de x. 509 AC requer que tiver uma conta do IoT Hub.  [Saiba como criar uma instância do IoT Hub](iot-hub-csharp-csharp-getstarted.md) se ainda não tiver um.

## <a name="how-to-get-an-x509-ca-certificate"></a>Como obter um certificado de AC de x. 509

O certificado de AC de x. 509 está no topo da cadeia de certificados para cada um dos seus dispositivos.  Pode comprar ou criar um, consoante a forma como pretende utilizá-lo.

Para o ambiente de produção, recomendamos que compre um certificado de AC de x. 509 de uma autoridade de certificação de raiz pública. Comprar um certificado de AC tem a vantagem da AC de raiz a agir como uma conta de terceiros fidedigna para vouch para a legitimidade dos seus dispositivos. Considere esta opção se tenciona os seus dispositivos como parte de uma rede de IoT aberta onde são esperados para interagir com terceiros produtos ou serviços.

Também pode criar uma AC autoassinada de x. 509 para experimentação ou para utilização em redes de IoT fechados.

Independentemente de como obter o certificado x. 509 AC, certifique-se de que mantém a respetiva correspondente privada chave secreta e protegidos em todas as vezes.  Isto é necessário para a criação de confiança na autenticação de AC de x. 509 de confiança. 

Saiba como [criar um certificado de AC autoassinado](iot-hub-security-x509-create-certificates.md#createcerts), que pode utilizar para a experimentação ao longo desta descrição da funcionalidade.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>Dispositivos de início de sessão para a cadeia de fidedignidade de certificados

O proprietário de um certificado de AC de x. 509 criptograficamente pode iniciar sessão uma AC intermediária que por sua vez pode iniciar sessão de outra AC intermediária, e assim sucessivamente, até que o último intermédio AC termina este processo ao inscrever um dispositivo. O resultado é uma cadeia cascata de conhecido como uma cadeia de certificados de confiança de certificados. No vida real este desempenha como delegação de confiança para a assinatura de dispositivos. Esta delegação é importante porque estabelece uma cadeia de custódia criptograficamente variável e evita a partilha de chaves de assinatura.

![img-Generic-CERT-chain-of-Trust](./media/generic-cert-chain-of-trust.png)

Saiba mais aqui como [criar uma cadeia de certificados](iot-hub-security-x509-create-certificates.md#createcertchain) como feito ao iniciar dispositivos.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Como registar o certificado da AC de x. 509 ao IoT Hub

Registe o certificado de AC de x. 509 ao IoT Hub onde será utilizado para autenticar os seus dispositivos durante o registo e a ligação.  Registar o certificado x. 509 AC é um processo de dois passos que compreende o carregamento do ficheiro de certificado e uma prova de posse.

O processo de carregamento envolve carregar um ficheiro que contém o certificado.  Este ficheiro nunca deve conter quaisquer chaves privadas.

Prova de passo posse envolve um criptográfico desafio e o processo de resposta entre si e IoT Hub.  Dado que o conteúdo de certificado digital é público e, por conseguinte, suscetível a escutas, o IoT Hub pretende ascertain que realmente seu certificado da AC.  Deverá efetuar, através da geração de um desafio aleatório que tem de iniciar sessão com a chave privada correspondente do certificado de AC.  Se manteve a chave privada secreta e protegido aconselhado como anteriormente, em seguida, apenas serão tenha conhecimento para concluir este passo. Segredo de chaves privadas é a origem de confiança neste método.  Depois de iniciar o desafio, conclua este passo através do carregamento de um ficheiro que contém os resultados.

Saiba mais aqui como [registar o certificado de AC](iot-hub-security-x509-get-started.md#registercerts).

## <a name="how-to-create-a-device-on-iot-hub"></a>Como criar um dispositivo no IoT Hub

A preclude representação do dispositivo, o IoT Hub requer para informá-lo a saber que dispositivos que são esperados.  Fazê-lo através da criação de uma entrada de dispositivo no registo de dispositivos do IoT Hub.  Este processo é um processo automatizado quando utilizar o IoT Hub [serviço de aprovisionamento de dispositivos](https://azure.microsoft.com/en-us/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/) (DPS). 

Saiba mais aqui como [criar manualmente um dispositivo no IoT Hub](iot-hub-security-x509-get-started.md#createdevice).

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>Autenticação de dispositivos assinados com certificados de AC de x. 509

Com o certificado de AC de x. 509 registado e dispositivos com sessão iniciados numa cadeia de fidedignidade de certificados, que permanece é a autenticação do dispositivo quando o dispositivo estabelece ligação, mesmo pela primeira vez.  Quando uma AC de x. 509 assinado dispositivo estabelece ligação, carrega a cadeia de certificados para validação. A cadeia inclui todos os certificados de AC e dispositivo intermédios.  Com esta informação, o IoT Hub autentica o dispositivo no processo de dois passos.  IoT Hub criptograficamente valida a cadeia de certificados para consistência interna e, em seguida, emite um desafio de prova de posse no dispositivo.  IoT Hub declara o dispositivo autêntica numa resposta com êxito de uma prova de posse do dispositivo.  Esta declaração de parte do princípio de que a chave privada do dispositivo está protegido e que apenas o dispositivo pode responder com êxito este desafio.  Recomendamos a utilização de chips seguras, como o Hardware seguro módulos (HSM) nos dispositivos para proteger chaves privadas.

Uma ligação do dispositivo concluída com êxito ao IoT Hub conclui o processo de autenticação e também é indicativa de uma configuração adequada.

Saiba mais aqui como [concluir este passo de ligação do dispositivo](iot-hub-security-x509-get-started.md#authenticatedevice).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [o valor de autenticação de AC de x. 509](iot-hub-x509ca-concept.md) no IoT.

Introdução ao IoT Hub [serviço de aprovisionamento de dispositivos](https://docs.microsoft.com/en-us/azure/iot-dps/).