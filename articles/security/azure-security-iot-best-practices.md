---
title: "Internet das coisas segurança melhores práticas | Microsoft Docs"
description: "O artigo fornece uma lista organizada de Microsoft Internet das coisas segurança melhores práticas e recomendações gerais."
services: security
documentationcenter: na
author: TomShinder
manager: StevenPo
editor: TomSh
ms.assetid: 2d5598c5-4c30-481d-b8f4-51ee024ea9a7
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: 8937437652a78e05b94574cb9fe5df7962edb1ad
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="internet-of-things-security-best-practices"></a>Internet das coisas segurança melhores práticas
Proteger a infraestrutura de Internet das coisas (IoT) é um undertaking crítico para todas as pessoas envolvidas com soluções de IoT. Devido ao número de dispositivos envolvidos e a natureza distribuída destes dispositivos, o impacto relacionados com um evento de segurança ao comprometimento de milhões de dispositivos de IoT não trivial e pode ter impacto ampla.

Por este motivo, a segurança de IoT tem uma abordagem de segurança em profundidade. Dados tem de ser segura na nuvem e são transmitidos através de redes públicas e privadas. Métodos tem de estar implementados para aprovisionar segura de dispositivos de IoT. Cada camada à rede, para a nuvem de back-end do dispositivo, tem de oferece garantias ao rendimento de segurança forte.

Melhores práticas de IoT podem ser classificadas da seguinte forma:

* Fabricante de hardware de IoT ou integrador
* Para programadores de solução IoT
* Implementador de solução IoT
* Operador de solução IoT

Este artigo resume [Internet das coisas melhores práticas de segurança](../iot-suite/iot-security-best-practices.md). Consulte a esse artigo para obter informações mais detalhadas.

## <a name="iot-hardware-manufacturer-or-integrator"></a>Fabricante de hardware de IoT ou integrador
Se for um fabrico de hardware de IoT ou integrador de hardware, siga as melhores práticas abaixo:

* **Definir o âmbito de hardware para os requisitos mínimos**: a estrutura de hardware deve incluir funcionalidades mínimas necessárias para a operação de hardware e nada mais. 
* **Efetuar uma prova de adulteração de hardware**: criar no mecanismos para detetar a adulteração física do hardware, tais como abrir abrange o dispositivo, removendo a parte do dispositivo, etc. 
* **Criar em torno de hardware seguro**: se [COGS](https://en.wikipedia.org/wiki/Cost_of_goods_sold) permitir, crie as funcionalidades de segurança, tais como a funcionalidade de arranque baseada no Trusted Platform Module TPM e de armazenamento seguro e encriptado.
* **Efetuar atualizações segura**: atualizar o firmware durante a duração do dispositivo é inevitável.

## <a name="iot-solution-developer"></a>Para programadores de solução IoT
Siga as melhores práticas abaixo se for um programador de solução IoT:

* **Siga a metodologia de desenvolvimento de software segura**: desenvolver software segura requer zero pensar sobre a segurança de inception do projeto para a implementação, teste e a implementação.
* **Escolha o software de código aberto com cuidado**: software open source para fornece uma oportunidade para rapidamente desenvolver soluções.
* **Integrar com cuidado**: muitas das falhas de segurança de software existem em limites de bibliotecas e APIs. 

## <a name="iot-solution-deployer"></a>Implementador de solução IoT
Siga as melhores práticas abaixo se for um implementador de solução IoT:

* **Implementar hardware de forma segura**: implementações de IoT podem necessitar de hardware para ser implementados em localizações não seguros, tais como espaços públicos ou regiões supervisionados.
* **Manter as chaves de autenticação seguros**: durante a implementação, cada dispositivo requer IDs de dispositivo e chaves de autenticação geradas pelo serviço de nuvem associadas. Manter estas chaves fisicamente seguro, mesmo após a implementação. Qualquer tecla comprometida pode ser utilizada por um dispositivo malicioso para representação como um dispositivo existente.

## <a name="iot-solution-operator"></a>Operador de solução IoT
Siga as melhores práticas abaixo se for um operador de solução IoT:

* **Manter sistemas atualizados**: Certifique-se de que sistemas operativos de dispositivos e todos os controladores de dispositivo são atualizados para as versões mais recentes. 
* **Proteger contra atividade maliciosa**: se permite que o sistema operativo, coloque as capacidades de antivírus e antimalware mais recentes em cada sistema operativo do dispositivo. 
* **Auditoria frequentemente**: auditoria de segurança relacionadas com problemas é chave quando a responder a incidentes de segurança de IoT infraestrutura.
* **Fisicamente proteger a infraestrutura de IoT**: os pior ataques de segurança em relação a infraestrutura de IoT são iniciados utilizando o acesso físico aos dispositivos.
* **Proteger credenciais na nuvem**: credenciais de autenticação em nuvem utilizadas para configurar e utilizar uma implementação de IoT possivelmente são a forma mais fácil de obter acesso e comprometer um sistema de IoT. 

