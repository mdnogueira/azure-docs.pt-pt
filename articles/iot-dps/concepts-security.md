---
title: "Conceitos de segurança no serviço de aprovisionamento de dispositivos do Azure IoT Hub | Microsoft Docs"
description: "Descreve conceitos específicos a dispositivos com o IoT Hub e o serviço de aprovisionamento de dispositivos de aprovisionamento de segurança"
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 3ccbaaf55d2bdfedffcdb5ca069798328e2d75fd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>Conceitos de segurança do serviço de aprovisionamento de dispositivos IoT Hub 

Aprovisionamento de serviço do dispositivos do Hub IoT é um serviço de programa auxiliar do IoT Hub que utiliza para configurar o dispositivo de zero touch aprovisionamento para um hub IoT especificado. Com o serviço de aprovisionamento de dispositivos pode aprovisionar milhões de dispositivos de forma segura e escalável. Este artigo fornece uma descrição geral sobre o *segurança* conceitos envolvidas no aprovisionamento de dispositivos. Este artigo é relevante para todas as pessoas fictícias envolvidas na obtenção de um dispositivo preparado para a implementação.

## <a name="attestation-mechanism"></a>Mecanismo de atestado

O mecanismo de atestado é o método utilizado para confirmar a identidade do dispositivo. O mecanismo de atestado também é relevante para a lista de inscrição, o que indica o serviço de aprovisionamento que método de atestado para utilizar com um dispositivo especificado.

> [!NOTE]
> IoT Hub utiliza "esquema de autenticação" para um conceito semelhante em que o serviço.

Serviço de aprovisionamento de dispositivos suporta duas formas de atestado:
* **Certificados x. 509** com base no fluxo de autenticação de certificado de x. 509 padrão.
* **Os tokens SAS** com base num desafio de nonce utilizando a norma TPM para chaves. Isto requer um TPM no dispositivo físico, mas o serviço espera atestem utilizando a chave de endossamento pelo [TPM spec](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).

## <a name="hardware-security-module"></a>Módulo de hardware de segurança

O módulo de hardware de segurança ou HSM, é utilizado para armazenamento seguro, baseada em hardware de segredos do dispositivo e é a forma mais segura de armazenamento secreta. Certificados x. 509 e SAS tokens podem ser armazenados no HSM. HSMs podem ser utilizadas com ambos os mecanismos de atestado suporta o aprovisionamento.

> [!TIP]
> Recomendamos vivamente que utilizar um HSM com dispositivos para armazenar segredos com segurança nos seus dispositivos.

Segredos do dispositivo também podem ser armazenados no software (memória), mas é um formulário menos seguro do armazenamento de um HSM.

## <a name="trusted-platform-module-tpm"></a>Trusted Platform Module (TPM)

TPM pode fazer referência a um padrão para armazenar as chaves utilizadas para autenticar a plataforma de forma segura, ou podem referir-se para a interface de e/s utilizada para interagir com os módulos implementar padrão. Podem existir TPMs como hardware discreta, integrada hardware, firmware, funções ou baseada em software. Saiba mais sobre [TPMs e TPM atestado](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). Serviço de aprovisionamento de dispositivos só suporta TPM 2.0.

## <a name="endorsement-key"></a>Chave de endossamento

A chave de endossamento é uma chave assimétrica contida no interior do TPM que foi injetado em tempo de fabrico e é exclusivo para cada TPM. A chave de endossamento não pode ser alterada ou removida. A parte privada da chave de endossamento nunca é libertada fora do TPM, enquanto a parte pública da chave de endossamento é utilizada para reconhecer um TPM genuíno. Saiba mais sobre o [chave de endossamento](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

## <a name="storage-root-key"></a>Chave de raiz de armazenamento

A chave de raiz de armazenamento é armazenada no TPM e é utilizada para proteger chaves TPM criadas por aplicações, para que estas chaves não podem ser utilizadas sem TPM. A chave de raiz de armazenamento é gerada quando a assumir a propriedade do TPM; Quando desmarcar o TPM para que um novo utilizador pode assumir a propriedade, é gerada uma nova chave de raiz de armazenamento. Saiba mais sobre o [chave de raiz de armazenamento](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="root-certificate"></a>Certificado de raiz

Um certificado de raiz é um tipo de certificado x. 509 que representa uma autoridade de certificação e é autoassinado. É terminus da cadeia de certificados.

## <a name="intermediate-certificate"></a>Intermediária

Um certificado intermediária é um certificado x. 509 que assinado pelo certificado de raiz (ou outro certificado com o certificado de raiz na cadeia) e que é utilizada para assinar o certificado de folha.

## <a name="leaf-certificate"></a>Certificado de folha

Um certificado de folha ou o certificado de entidade final, é utilizado para identificar o marcador de posição do certificado e tem o certificado de raiz na respetiva cadeia de certificados. O certificado de folha não é utilizado para assinar quaisquer outros certificados.
