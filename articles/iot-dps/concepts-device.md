---
title: Conceitos de dispositivo no aprovisionamento de dispositivos do Azure | Microsoft Docs
description: "Descreve conceitos específicos a dispositivos com o IoT Hub e o serviço de aprovisionamento de dispositivos de aprovisionamento de dispositivos"
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
ms.openlocfilehash: 5297bc57729d9e983d63244c71eb21995cf73f0e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>Conceitos de dispositivo do serviço de aprovisionamento de dispositivos IoT Hub

Aprovisionamento de serviço do dispositivos do Hub IoT é um serviço de programa auxiliar do IoT Hub que utiliza para configurar o dispositivo de zero touch aprovisionamento para um hub IoT especificado. Com o serviço de aprovisionamento de dispositivos pode aprovisionar milhões de dispositivos de forma segura e escalável.

Este artigo fornece uma descrição geral sobre o *dispositivo* conceitos envolvidas no aprovisionamento de dispositivos. Este artigo é mais relevante para pessoas fictícias envolvidas no [fabrico passo](about-iot-dps.md#manufacturing-step) de preparar para a implementação de um dispositivo.

## <a name="attestation-mechanism"></a>Mecanismo de atestado

O mecanismo de atestado é o método utilizado para confirmar a identidade do dispositivo. O mecanismo de atestado também é relevante para a lista de inscrição, o que indica o serviço de aprovisionamento que método de atestado para utilizar com um dispositivo especificado.

> [!NOTE]
> IoT Hub utiliza "esquema de autenticação" para um conceito semelhante em que o serviço.

O serviço de aprovisionamento de dispositivos suporta duas formas de atestado:
* **Certificados x. 509** com base no fluxo de autenticação de certificado de x. 509 padrão.
* **Os tokens SAS** com base num desafio de nonce utilizando a norma TPM para chaves. Isto requer um TPM no dispositivo físico, mas o serviço espera atestem utilizando a chave de endossamento pelo [TPM spec](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).

## <a name="hardware-security-module"></a>Módulo de hardware de segurança

O módulo de hardware de segurança ou HSM, é utilizado para armazenamento seguro, baseada em hardware de segredos do dispositivo e é a forma mais segura de armazenamento secreta. Certificados x. 509 e SAS tokens podem ser armazenados no HSM. HSMs podem ser utilizados com ambos os mecanismos de atestado o aprovisionamento suporta de serviço.

> [!TIP]
> Recomendamos vivamente que utilizar um HSM com dispositivos para armazenar segredos com segurança nos seus dispositivos.

Segredos do dispositivo também podem ser armazenados no software (memória), mas é um formulário menos seguro do armazenamento de um HSM.

## <a name="registration-id"></a>ID de registo

O ID de registo é utilizado para identificar exclusivamente um dispositivo no serviço de aprovisionamento de dispositivos. O ID de dispositivo tem de ser exclusivo no serviço de aprovisionamento [âmbito ID](#id-scope). Cada dispositivo tem de ter um ID de registo. O ID de registo é alfanuméricos, minúsculas e pode conter hífenes.

* No caso do TPM, o ID de registo é fornecido por TPM próprio.
* No caso de x. 509 com base no atestado, o ID de registo é fornecido como o nome do requerente do certificado.

## <a name="device-id"></a>ID do dispositivo

O ID de dispositivo é o ID, conforme é apresentado no IoT Hub. Pode ser definido o ID de dispositivo pretendidos na entrada de inscrição, mas não é necessário ser definida. Se não for especificado nenhum ID de dispositivo pretendidos na lista de inscrição, o ID de registo é utilizado como o ID de dispositivo ao registar o dispositivo. Saiba mais sobre [os IDs do IoT Hub do dispositivo](../iot-hub/iot-hub-devguide-identity-registry.md).

## <a name="id-scope"></a>Âmbito de ID

O âmbito de ID é atribuído a um serviço de aprovisionamento de dispositivo quando é criado pelo utilizador e é utilizado para identificar exclusivamente o serviço de aprovisionamento específico, que vai registar o dispositivo através do. O âmbito de ID for gerado pelo serviço e é imutável, que garante a exclusividade.

> [!NOTE]
> É importante para operações de implementação de longa execução e cenários fusão e aquisição de exclusividade.
