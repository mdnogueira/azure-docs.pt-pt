---
title: "Gerir as atualizações na descrição geral de pilha do Azure | Microsoft Docs"
description: "Saiba mais sobre a gestão de atualizações para os sistemas de pilha do Azure integrado."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 9b0781f4-2cd5-4619-a9b1-59182b4a6e43
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 3d0d5ea6cc3f3cc7bc0550b83dabbf0ae6af8a27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-updates-in-azure-stack-overview"></a>Gerir as atualizações na descrição geral de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas*

Microsoft vai lançar pacotes de atualização para sistemas de pilha do Azure integrado regularmente. Cada versão de atualizações de software da Microsoft é incluído como um pacote de atualização única. Como um operador de pilha do Azure, pode importar facilmente, instalação e monitorize o progresso de instalação destas de atualização de pacotes a partir do portal do administrador. 

O fornecedor de hardware de fabricante de equipamento original (OEM) também vai lançar atualizações, tais como atualizações de controladores e firmware. Estas atualizações são fornecidas como pacotes separados pelo seu fornecedor de hardware do OEM e são geridas separadamente das atualizações da Microsoft.

Para manter o seu sistema de suporte, tem de manter atualizado para um nível de versão específica de pilha de Azure. Certifique-se de que revê o [pilha Azure manutenção política](azure-stack-servicing-policy.md).

> [!NOTE]
> Não é possível aplicar os pacotes de atualização de pilha do Azure para o Kit de desenvolvimento de pilha do Azure. Os pacotes de atualização são concebidos para sistemas integrados.

## <a name="the-update-resource-provider"></a>O fornecedor de recursos de atualização

Pilha do Azure inclui um fornecedor de recursos de atualização que orquestra a aplicação de atualizações de software da Microsoft. Este fornecedor de recursos assegura que as atualizações são aplicadas em todos os anfitriões físicos, aplicações de Service Fabric e tempos de execução e todas as máquinas virtuais de infraestrutura e os respetivos serviços associados.

Como instalar atualizações, pode visualizar facilmente o estado de alto nível como destinos de processo de atualização dos subsistemas de vários na pilha do Azure (por exemplo, anfitriões físicos e máquinas virtuais de infraestrutura).

## <a name="plan-for-updates"></a>Plano para atualizações

Recomendamos vivamente que notifique os utilizadores de quaisquer operações de manutenção e agendar a janelas de manutenção normal durante horas quanto possíveis. Operações de manutenção podem afetar cargas de trabalho inquilinas e operações portais.

## <a name="using-the-update-tile-to-manage-updates"></a>Utilizar o mosaico de atualização para gerir atualizações
Gerir as atualizações a partir do portal do administrador é um processo simple. Um operador de pilha do Azure pode navegar para o mosaico de atualização no dashboard para:

- ver informações importantes, tais como a versão atual.
- instalar atualizações e monitorize o progresso.
- Reveja o histórico de atualização para que as atualizações instaladas anteriormente.
 
## <a name="determine-the-current-version"></a>Determinar a versão atual

O mosaico de atualização mostra a versão atual da pilha do Azure. Pode obter o mosaico de atualização utilizando um dos seguintes métodos no portal do administrador:

- No dashboard, ver a versão atual no **atualização** mosaico.
 
   ![As atualizações de mosaico no dashboard predefinido](./media/azure-stack-updates/image1.png)
 
- No **gestão região** mosaico, clique no nome de região. Ver a versão atual no **atualização** mosaico.

## <a name="next-steps"></a>Passos seguintes

- [Pilha do Azure de política de manutenção](azure-stack-servicing-policy.md) 
- [Gestão de região na pilha do Azure](azure-stack-region-management.md)     


