---
title: Proteger os dados pessoais no Microsoft Azure | Microsoft Docs
description: "Artigo primeiro uma série de artigos para o ajudar a utilizar o Azure para proteger os dados pessoais"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 4dbdb2dc11bdc515fb3856dd45203868122c7726
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="protect-personal-data-in-microsoft-azure"></a>Proteger os dados pessoais no Microsoft Azure

Este artigo apresenta uma série de artigos que o ajudam a utilizar serviços e as tecnologias de segurança do Azure para proteger os dados pessoais. Este é um requisito-chave para muitos empresarial e iniciativas de governação e de conformidade da indústria. O cenário, os objetivos de declaração e da empresa do problema são incluídos aqui.

## <a name="scenario-and-problem-statement"></a>Declaração de cenário e do problema

Uma empresa de grande cruise headquartered nos Estados Unidos, está a expandir as suas operações para oferecer itineraries no Mediterranean, Adriatic e Baltic seas, bem como o Isles território. Para suportar os esforços, obteve várias linhas cruise mais pequenas com base em (Itália), na Alemanha, Dinamarca e o U.K.

A empresa utiliza o Microsoft Azure para armazenar dados empresariais na nuvem. Isto pode incluir informações de cliente e/ou dos empregados, tais como:

- endereços
- Números de telefone
- Números de identificação de dedução dos impostos
- informações de cartão de crédito

A empresa tem de proteger a privacidade dos dados de cliente e o empregado ao tornar dados acessíveis para os departamentos que precisem dele. (por exemplo, os departamentos de folha de pagamentos e reservas)

## <a name="company-goals"></a>Objetivos da empresa 

- Origens de dados que contêm dados pessoais são encriptadas quando que reside no armazenamento na nuvem.

- Dados pessoais que são transferidos a partir de uma localização para outra são encriptados em trânsito. Isto acontece se os dados viaja através da rede virtual ou através da Internet entre o Centro de dados empresarial e a nuvem do Azure.

- Confidencialidade e integridade dos dados pessoais está protegido contra acesso não autorizado por gestão de identidades forte e tecnologias de controlo de acesso.

- Dados pessoais estão protegidos de exposição através de violação de dados através de monitorização de vulnerabilidades e ameaças.

- Estado de segurança dos serviços do Azure que armazena nem transmitir dados pessoais é avaliado para identificar oportunidades para proteger melhor os dados pessoais.

## <a name="data-protection-guidance"></a>Orientações de proteção de dados

Os artigos seguintes contêm orientações de procedimentos técnica que irão ajudar a obter os objetivos de proteção de dados pessoais listados acima:

- [Tecnologias de encriptação do Azure](protect-personal-data-at-rest.md)

- [Tecnologias de encriptação do Azure](protect-personal-data-in-transit-encryption.md)

- [Tecnologias de identidades e acessos do Azure](protect-personal-data-identity-access-controls.md)

- [Tecnologias de segurança de rede do Azure](protect-personal-data-network-security.md)

- [Centro de Segurança do Azure](protect-personal-data-azure-security-center.md)



## <a name="next-steps"></a>Passos seguintes

- [Site de informações de segurança do Azure](https://aka.ms/AzureSecInfo)

- [Centro de fidedignidade da Microsoft](https://www.microsoft.com/TrustCenter/default.aspx)

- [Centro de Segurança do Azure](https://azure.microsoft.com/services/security-center/)

- [Blogue da Equipa de Segurança do Azure](https://www.azuresecurityorg)

- [Blogue de Azure.com - segurança](https://azure.microsoft.com/blog/topics/security/)
