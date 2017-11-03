---
title: "O que fazer em caso de um Azure service interrupção que tem impacto nos Cloud Services do Azure | Microsoft Docs"
description: "Saiba o que fazer em caso de uma interrupção do serviço do Azure que tem impacto nos Cloud Services do Azure."
services: cloud-services
documentationcenter: 
author: mmccrory
manager: timlt
editor: 
ms.assetid: e52634ab-003d-4f1e-85fa-794f6cd12ce4
ms.service: cloud-services
ms.workload: cloud-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: mmccrory
ms.openlocfilehash: db6a980b85ea5ef8cbbba4ba5a36f9d033739df1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>O que fazer em caso de um Azure service interrupção que tem impacto nos Cloud Services do Azure
Na Microsoft, trabalhamos rígido para assegurar que os nossos serviços de sempre estão disponíveis para si quando precisá-los. Força se para além dos nosso controlo, por vezes, um impacto no-nos de formas que provocar interrupções de serviço não planeada.

A Microsoft fornece um contrato de nível de serviço (SLA) para os respetivos serviços, como um compromisso de tempo de atividade e conectividade. O SLA para serviços do Azure individuais pode ser encontrado em [contratos de nível de serviço do Azure](https://azure.microsoft.com/support/legal/sla/).

O Azure já tem muitas funcionalidades de plataforma incorporado que suportam aplicações altamente disponíveis. Para mais informações sobre estes serviços, leia o artigo [recuperação após desastre e elevada disponibilidade para aplicações do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Este artigo abrange um cenário de recuperação de desastre verdadeiro, quando uma região toda sofre uma falha devido a desastre natural principais ou interrupção do serviço ampla. Estes são ocorrências raras, mas tem de preparar para a possibilidade de se houver uma falha de uma região de toda. Se uma região toda sofre uma interrupção do serviço, as cópias dos seus dados localmente redundantes seria temporariamente indisponíveis. Se tiver ativado a georreplicação, três cópias adicionais dos blobs de armazenamento do Azure e tabelas são armazenadas numa região diferente. Em caso de uma falha regional concluída ou um desastre no qual a região primária não é recuperável, a Azure remaps todas as entradas DNS para a região de georreplicação.

> [!NOTE]
> Lembre-se de que não têm qualquer controlo sobre este processo e ocorrerá apenas para interrupções ao serviço em todo o datacenter. Por este motivo, deve também dependem outras estratégias de cópia de segurança específicos da aplicação para atingir o nível mais elevado de disponibilidade. Para obter mais informações, consulte [recuperação após desastre e elevada disponibilidade de aplicações incorporadas no Microsoft Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md). Se gostaria de conseguir afetam a suas próprias ativação pós-falha, pode querer considerar a utilização da [armazenamento georredundante com acesso de leitura (RA-GRS)](../storage/common/storage-redundancy.md#read-access-geo-redundant-storage), que cria uma cópia só de leitura de dados noutra região.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Opção 1: Utilizar uma implementação de cópia de segurança através do Gestor de tráfego do Azure
A solução de recuperação após desastre mais robusta envolve manter múltiplas implementações da aplicação em regiões diferentes, em seguida, utilizar [Traffic Manager do Azure](../traffic-manager/traffic-manager-overview.md) para direcionar o tráfego entre eles. Traffic Manager do Azure fornece várias [métodos de encaminhamento](../traffic-manager/traffic-manager-routing-methods.md), para que possa escolher se pretende gerir as implementações através de um modelo de cópia de segurança/principal ou dividir o tráfego entre as mesmas.

![Serviços Cloud do Azure de balanceamento em regiões com o Gestor de tráfego do Azure](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

A resposta mais rápido para a perda de uma região, é importante que configure o Gestor de tráfego [monitorização de pontos finais](../traffic-manager/traffic-manager-monitoring.md).

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Opção 2: Implementar a aplicação a uma região de novo
Manter múltiplas implementações de Active Directory, conforme descrito na opção anterior incorreu dos custos adicionais. Se o objetivo de tempo de recuperação (RTO) é flexível o suficiente e tem o código original ou compilado pacote de serviços em nuvem, pode criar uma nova instância da sua aplicação noutra região e atualizar os registos DNS para apontar para a nova implementação.

Para obter mais detalhes sobre como criar e implementar uma aplicação de serviço na nuvem, consulte [como criar e implementar um serviço em nuvem](cloud-services-how-to-create-deploy-portal.md).

Consoante as origens de dados de aplicação, poderá ter verificar os procedimentos de recuperação para a origem de dados de aplicação.

* Para origens de dados do Storage do Azure, consulte [replicação de armazenamento do Azure](../storage/common/storage-redundancy.md#read-access-geo-redundant-storage) para verificar as opções que estão disponíveis com base no modelo de replicação de escolha para a sua aplicação.
* Para origens de base de dados SQL, leia o artigo [descrição geral: na nuvem de recuperação de desastre de base de dados e de continuidade do negócio com a SQL Database](../sql-database/sql-database-business-continuity.md) para verificar as opções que estão disponíveis com base no modelo de replicação escolhido para a sua aplicação.


## <a name="option-3-wait-for-recovery"></a>Opção 3: Aguardar para recuperação
Neste caso, é necessária nenhuma ação da sua parte, mas o seu serviço estará disponível até a região é restaurada. Pode ver o estado atual do serviço no [Dashboard de estado de funcionamento do serviço de Azure](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como implementar uma estratégia de elevada disponibilidade e recuperação após desastre, consulte o artigo [recuperação após desastre e elevada disponibilidade para aplicações do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Para desenvolver uma compreensão técnica detalhada das capacidades de uma plataforma em nuvem, consulte [orientações técnica do Azure resiliência](../resiliency/resiliency-technical-guidance.md).