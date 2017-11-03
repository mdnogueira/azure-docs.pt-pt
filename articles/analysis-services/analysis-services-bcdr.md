---
title: Disponibilidade elevada de Analysis Services do Azure | Microsoft Docs
description: Assuring Azure Analysis Services elevada disponibilidade.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 554c5e6e3e3cfa2742ef27a3c1510176184b6bd0
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="analysis-services-high-availability"></a>Disponibilidade elevada do Analysis Services
Este artigo descreve assuring elevada disponibilidade para servidores do Azure Analysis Services. 


## <a name="assuring-high-availability-during-a-service-disruption"></a>Assuring elevada disponibilidade durante uma interrupção do serviço
Embora seja raro, um centro de dados do Azure pode ter uma falha. Quando ocorre uma falha, faz com que uma interrupção de negócios que poderá última alguns minutos ou pode última durante as horas. Elevada disponibilidade é normalmente conseguida com redundância de servidor. Com o Azure Analysis Services, pode conseguir redundância através da criação de servidores adicionais, secundárias num ou mais regiões. Quando criar servidores redundantes, para assegurar que os dados e os metadados nesses servidores na sincronização com o servidor numa região que ficou offline, pode:

* Implemente modelos servidores redundantes noutras regiões. Este método requer que dados de processamento no seu servidor primário e de servidores redundantes em paralelo, assuring todos os servidores estão em sincronização.

* Cópia de segurança bases de dados do seu servidor primário e de restauro em servidores redundantes. Por exemplo, pode automatizar as cópias de segurança noturna ao armazenamento do Azure e restaurar para outros servidores redundantes noutras regiões. 

Em ambos os casos, se o servidor primário sofre uma falha, tem de alterar as cadeias de ligação no Reporting Services que os clientes ligar ao servidor no Centro de dados regional diferente. Esta alteração deve ser considerada último recurso e ocorre apenas se uma falha do Centro de dados regionais catastrófica. É mais provável, uma falha do Centro de dados que aloja o servidor primário seria fique novamente online antes de lhe foi possível atualizar ligações em todos os clientes. 



## <a name="related-information"></a>Informações relacionadas
[Cópia de segurança e restauro](analysis-services-backup.md)   
[Gerir serviços de análise do Azure](analysis-services-manage.md) 

