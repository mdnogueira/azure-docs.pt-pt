---
title: "Pilha do Azure de política de manutenção | Microsoft Docs"
description: "Saiba mais sobre a pilha do Azure de manutenção política, como manter um sistema integrado num estado suportado."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: f2c99f19b30d2cdfdf65dea6dd3909b88ffc15dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stack-servicing-policy"></a>Pilha do Azure de política de manutenção

*Aplica-se a: Azure pilha integrado sistemas*

Este artigo descreve a política de manutenção para sistemas de pilha do Azure integrada e que terá de efetuar para manter o seu sistema num estado suportado. 

## <a name="update-package-types"></a>Tipos de pacote de atualização

Existem dois tipos de pacotes de atualização para sistemas integrados; As atualizações de software da Microsoft e as atualizações que são específicas do fornecedor do hardware de fabricante de equipamento original (OEM), tais como controladores e firmware. Estas atualizações são fornecidas como separados pacotes de atualização de pilha do Azure e são geridas de forma independente.

- **As atualizações de software da Microsoft**. Microsoft é responsável pelo ciclo de vida manutenção de ponto a ponto para os pacotes de atualização de software da Microsoft. Estes pacotes podem incluir as mais recentes atualizações de segurança do Windows Server, não-atualizações de segurança e atualizações de funcionalidade da pilha do Azure. Pode transferir os pacotes de atualização de presumem diretamente a partir de Microsoft.
- **Atualizações de fornecido de fornecedor de hardware do OEM**. Parceiros de hardware de pilha do Azure são responsáveis pelo ponto-a-ponto manutenção ciclo de vida (incluindo orientações) para o firmware relacionados com o hardware e pacotes de atualização de controladores. Além disso, os parceiros de hardware de pilha do Azure proprietários e manter a documentação de orientação para todo o software e hardware no anfitrião de ciclo de vida de hardware. O fornecedor de hardware do OEM aloja estes pacotes num site suas próprias transferências de atualização.

## <a name="update-package-release-cadence"></a>Cadência de versão do pacote de atualização

A Microsoft espera libertar os pacotes de atualização de software uma cadência mensal. No entanto, é possível ter várias ou não existem versões de atualização num mês. As atualizações em conta as necessidades de versão de fornecedores de hardware do OEM.

Um pacote de atualização da Microsoft tem a seguinte convenção de nomenclatura para o ajudar a identificar facilmente a data da versão:

*MajorProductVersion.MinorProductVersion.YYMMDD.BuildNumber*

Por exemplo, uma atualização de software da Microsoft lançada a 15 de Junho de 2017 teria a versão "1.0.170615.1".

## <a name="keep-your-system-under-support"></a>Manter o seu sistema de suporte

Para receber suporte para o seu sistema, tem de manter a pilha de Azure atualizadas dentro de um intervalo de tempo específico. A nossa política de diferimento por das atualizações de software da Microsoft é durante três meses. Se o sistema está Desatualizadas mais do que três meses, está a considerados como estando em conformidade. Tem de atualizar o sistema para, pelo menos, o mínimo suportado de versão para receber suporte. 

Pacotes de atualização de software Microsoft são não cumulativa e necessitam que o pacote de atualização anteriores como pré-requisito. Se decidir diferir atualizações de um ou mais, considere o tempo de execução global, se pretender obter a versão mais recente.

A tabela seguinte mostra as versões de pacote de atualização de exemplo, os respetivos pré-requisitos e a versão mínima suportada que o sistema tem de ser, para manter o suporte. A tabela baseia-se na versão inicial do sistemas de pilha do Azure integrado (criar 1708), com a primeira atualização pacote versão (1709) em Setembro de 2017. 

| Pacote de atualização mais recente (*exemplo*) | Pré-requisito | Versão mínima suportada |
| -- | -- | -- |
| 1709 | Compilação 1708 | N/D |
| 1710 | 1709 | N/D |
| 1711 | 1710 | N/D |
| 1712 | 1711 | 1709 |
| 1801 | 1712 | 1710 |
| 1802 | 1801 | 1711 |
| 1803 | 1802 | 1712 |
| 1804 | 1803 | 1801 |
| | | 

## <a name="next-steps"></a>Passos seguintes

- [Gerir as atualizações na pilha do Azure](azure-stack-updates.md)


