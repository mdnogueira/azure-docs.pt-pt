---
title: Proteger o seu Internet das coisas (IoT) no Azure | Microsoft Docs
description: " Azure internet dos serviços de coisas (IoT) oferecem uma vasta gama de capacidades. Este artigo ajuda-o a compreender como proteger as suas soluções de IoT no Azure. "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TomSh
ms.assetid: 1473c8dd-8669-48fb-86db-b3c50e2eaf59
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: baf9c81867f8dccf3897213121888d40b7472d03
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="internet-of-things-security-overview"></a>Descrição geral de segurança da Internet das coisas
Azure internet dos serviços de coisas (IoT) oferecem uma vasta gama de capacidades. Estes serviços de nível empresarial permitem-lhe:

* Recolher dados de dispositivos
* Analisar fluxos de dados em movimento
* Armazenar e consultar grandes conjuntos de dados
* Visualizar dados em tempo real e dados históricos
* Integrar com sistemas de back-office

Para fornecer estas capacidades, pacotes de Azure IoT Suite em conjunto vários serviços do Azure com extensões personalizadas, como soluções pré-configuradas. Estas soluções pré-configuradas são implementações de base de padrões comuns da solução IoT que ajudam a reduzir o tempo necessário para fornecer as suas soluções de IoT. Utilizar os kits de desenvolvimento de software IoT, pode personalizar e expandir essas soluções para satisfazer os seus próprios requisitos. Também pode utilizar estas soluções como exemplos ou modelos quando estiver a desenvolver novas soluções de IoT.

O Azure IoT suite é uma solução poderosa para as suas necessidades de IoT. No entanto, é importância upmost que as suas soluções de IoT concebidas com segurança em mente desde o início. Devido ao número de dispositivos de IoT sheer, qualquer incidente de segurança pode tornar rapidamente um evento ampla com consequências significativas.

Para ajudar a compreender como proteger as suas soluções de IoT, temos das seguintes informações.

## <a name="security-architecture"></a>Arquitetura de segurança
Ao conceber um sistema, é importante compreender as potenciais ameaças a esse sistema e ao adicionar o defesas adequadas em conformidade, como o sistema é concebido e criado. É importante para a estrutura do produto a partir do início com segurança em mente porque compreender como um atacante poderá comprometer um sistema de ajuda a mitigações adequadas se de que estão no local a partir do início.

Pode saber mais sobre a arquitetura de segurança de IoT através da leitura [Internet das coisas segurança arquitetura](../iot-suite/iot-security-architecture.md).

Este artigo aborda os seguintes tópicos:

* [Segurança começa com um modelo de ameaça](../iot-suite/iot-security-architecture.md#security-starts-with-a-threat-model)
* [Segurança de IoT](../iot-suite/iot-security-architecture.md#security-in-iot)
* [A arquitetura de referência do IoT do Azure de modelação de ameaça](../iot-suite/iot-security-architecture.md#threat-modeling-the-azure-iot-reference-architecture)

## <a name="security-from-the-ground-up"></a>Segurança desde o início
O IoT pode representar um únicos desafios de segurança, privacidade e conformidade para as empresas em todo o mundo. Ao contrário de tecnologia de informático tradicional onde estes problemas está centrada no software e como é implementado, o IoT seja relativo o que acontece quando o informático e os universos físicos convergir. Proteger soluções de IoT requer garantir segura de aprovisionamento de dispositivos, conectividade segura entre estes dispositivos e a nuvem e de proteção de proteger os dados na nuvem durante o processamento e armazenamento. Trabalhar com essas funcionalidades, no entanto, são dispositivos restrita de recursos, distribuição geográfica dos implementações e muitos dispositivos dentro de uma solução.

Pode saber como lidar com segurança nas seguintes áreas lendo [segurança da Internet das coisas partir do zero](../iot-suite/securing-iot-ground-up.md).

O artigo aborda os seguintes tópicos:

* [Segura infraestrutura a partir do zero](../iot-suite/securing-iot-ground-up.md#secure-infrastructure-from-the-ground-up)
* [Microsoft Azure – segura IoT infraestrutura para a sua empresa](../iot-suite/securing-iot-ground-up.md#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Melhores práticas
Proteger uma infraestrutura de IoT requer uma estratégia de segurança-na profundidade rigorosas. De proteger os dados na nuvem, a proteger a integridade dos dados em trânsito através da internet pública, para aprovisionamento de forma segura a dispositivos, cada camada cria o maior garantia de segurança na infraestrutura global.

Pode saber sobre a segurança da Internet das coisas melhores práticas através da leitura [práticas recomendadas de segurança de Internet das coisas](../iot-suite/iot-security-best-practices.md).

O artigo aborda os seguintes tópicos:

* [Fabricante de hardware de IoT/integrador](../iot-suite/iot-security-best-practices.md#iot-hardware-manufacturerintegrator)
* [Para programadores de solução IoT](../iot-suite/iot-security-best-practices.md#iot-solution-developer)
* [Implementador de solução IoT](../iot-suite/iot-security-best-practices.md#iot-solution-deployer)
* [Operador de solução IoT](../iot-suite/iot-security-best-practices.md#iot-solution-operator)
