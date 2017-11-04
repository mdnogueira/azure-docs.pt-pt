---
title: "Ver o consumo de endereço IP público na pilha do Azure | Microsoft Docs"
description: "Os administradores podem ver o consumo de endereços IP públicos numa região"
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 0f77be49-eafe-4886-8c58-a17061e8120f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: scottnap
ms.openlocfilehash: 7651565eebf6272f307a4ce4790ca19b41bfa826
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Ver o consumo de endereço IP público na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Como um administrador da nuvem, pode ver o número de endereços IP públicos que teriam sido alocados aos inquilinos, o número de endereços IP públicos que ainda estão disponíveis para alocação e a percentagem de endereços IP públicos que teriam sido alocados nessa localização.

O **utilização de agrupamentos de IP público** mosaico mostra o número total de endereços IP públicos que ter sido consumida em todos os conjuntos de endereços IP públicos em recursos de infraestrutura, se de que tenham sido utilizados para o inquilino instâncias de VM do IaaS, recursos de infraestrutura os serviços, ou de recursos do endereço IP público que foram criados explicitamente pelos inquilinos.

O objetivo deste mosaico é dar aos administradores de pilha do Azure uma noção do que o número global de endereços IP públicos que ter sido consumida nesta localização. Isto ajuda a administradores de determinar se estiverem a executar baixas neste recurso.

No **fornecedores de recursos**, **rede** painel, o **endereços IP públicos** item de menu em **recursos de inquilino** lista apenas esses público Endereços IP que tenham sido *explicitamente criado por inquilinos*. Como tal, o número de **utilizado** endereços IP público no **utilização de agrupamentos de IP público** mosaico sempre é diferente do (superior) o número no **endereços IP públicos** mosaico em **recursos de inquilino**.

## <a name="view-the-public-ip-address-usage-information"></a>Ver as informações de utilização de endereço IP públicas
Para ver o número total de endereços IP públicos que ter sido consumida na região:

1. No portal de administrador de pilha do Azure, clique em **mais serviços**, em **recursos administrativo**, clique em **fornecedores de recursos**.
2. Na lista de **fornecedores de recursos**, selecione **rede**.
3. O **rede** painel apresenta o **utilização de agrupamentos de IP público** na peça de mosaico do **descrição geral** secção.

![Painel do fornecedor de recursos de rede](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

Tenha em atenção que o **utilizado** número representa o número de endereços IP públicos de todos os públicos conjuntos de endereços IP nessa localização que estão atribuídos. O **livres** número representa o número de IP público endereços de IP público todos os endereços agrupamentos que não foram atribuídos e ainda estão disponíveis. O **utilizada** número representa o número de utilizado ou atribuídos endereços como uma percentagem do número total de endereços IP públicos em todos os conjuntos de endereços IP públicos nessa localização.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Ver os endereços IP públicos que foram criados por subscrições de inquilino
Para ver uma lista de endereços IP públicos explicitamente criadas por subscrições de inquilino numa região específica, clique em **endereços IP públicos** em **recursos de inquilino**.

![Endereços IP públicos do inquilino](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

É possível que repare que alguns endereços IP públicos que teriam sido alocados dinamicamente aparecem na lista, mas não dispõe de um endereço associado aos mesmos ainda. Isto acontece porque o recurso de endereço ainda foi criado o fornecedor de recursos de rede, mas não no controlador de rede.

O controlador de rede não atribuir um endereço para este recurso, até que, na verdade, está vinculado a uma interface, uma placa de interface de rede (NIC), um balanceador de carga ou um gateway de rede virtual. Quando o endereço IP público está vinculado a uma interface, controlador de rede atribui um endereço IP para a mesma e é apresentado no **endereço** campo.

## <a name="view-the-public-ip-address-information-summary-table"></a>Ver a pública IP endereço informações tabela de resumo
Há uma série de diferentes cenários em que são atribuídos a endereços IP públicos para determinar se o endereço é apresentado na lista de um ou outro.

| **Caso de atribuição de endereço IP público** | **É apresentada no resumo de utilização** | **É apresentada na lista de endereços IP pública do inquilino** |
| --- | --- | --- |
| Endereço IP público dinâmico ainda não foi atribuído a um NIC ou Balanceador de carga (temporário) |Não |Sim |
| Dinâmico endereço IP público atribuído a um NIC ou Balanceador de carga. |Sim |Sim |
| Endereço IP público estático atribuído a um inquilino NIC ou Balanceador de carga. |Sim |Sim |
| Endereço IP público estático atribuído a um ponto de final de serviço da infraestrutura de recursos de infraestrutura. |Sim |Não |
| Endereço IP público implicitamente criado para instâncias de VM do IaaS e utilizada para NAT de saída na rede virtual. Estes são criados nos bastidores sempre que um inquilino cria uma instância VM para que as VMs podem enviar informações à Internet. |Sim |Não |

## <a name="next-steps"></a>Passos seguintes
[Gerir contas de armazenamento na pilha do Azure](azure-stack-manage-storage-accounts.md)