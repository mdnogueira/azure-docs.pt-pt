---
title: "Resolver problemas de cópia de segurança lenta de ficheiros e pastas na cópia de segurança do Azure | Microsoft Docs"
description: "Fornece orientações para resolução de problemas para o ajudar a diagnosticar a causa de problemas de desempenho de cópia de segurança do Azure"
services: backup
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.assetid: e379180a-db13-4e0c-90e4-28e5dd6f5b14
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 373a98855886cc7be7518c664f82bb6f92ca86f3
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Resolver problemas relacionados com cópias de segurança lentas de ficheiros e pastas no Azure Backup
Este artigo fornece orientação de resolução de problemas para o ajudar a diagnosticar a causa do desempenho lento de cópia de segurança de ficheiros e pastas quando estiver a utilizar a cópia de segurança do Azure. Quando utilizar o agente de cópia de segurança do Azure para cópia de segurança de ficheiros, o processo de cópia de segurança poderá demorar mais do que esperado. Este atraso pode ser causado por uma ou mais dos seguintes procedimentos:

* [Existem congestionamentos de desempenho no computador que está a ser efetuado.](#cause1)
* [Outro processo ou software antivírus é interferir com o processo de cópia de segurança do Azure.](#cause2)
* [O agente de cópia de segurança está em execução numa máquina virtual do Azure (VM).](#cause3)  
* [A cópia de segurança de um grande número (milhões) de ficheiros.](#cause4)

Antes de iniciar a resolução de problemas, recomendamos que transfira e instale o [agente de cópia de segurança do Azure mais recente](http://aka.ms/azurebackup_agent). Iremos efetuar atualizações frequentes para o agente de cópia de segurança para corrigir problemas de várias, adicionar as funcionalidades e melhorar o desempenho.

Também recomendamos que reveja o [FAQ do serviço de cópia de segurança do Azure](backup-azure-backup-faq.md) para se certificar de que não está a ocorrer qualquer um dos problemas comuns de configuração.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Causa: Congestionamentos de desempenho o computador
Estrangulamentos no computador que está a cópia de segurança podem provocar atrasos. Por exemplo, a capacidade do computador ler ou escrever no disco ou de largura de banda disponível para enviar dados através da rede, pode causar congestionamentos.

O Windows fornece uma ferramenta incorporada que é chamada [Monitor de desempenho](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) para detetar estes congestionamentos.

Seguem-se alguns contadores de desempenho e os intervalos que podem ser úteis para diagnosticar problemas congestionamentos para cópias de segurança ideal.

| Contador | Estado |
| --- | --- |
| Disco lógico (disco físico) – % inativo |• 100% inativo para 50% inativo = bom estado de funcionamento</br>• 49% inativo para 20% inativo = Monitor ou de aviso</br>• 19% inativo para 0% inativo = críticas ou de especificação |
| Disco lógico (disco físico) – % média De disco seg leitura ou escrita |• 0.001 ms para 0.015 ms = bom estado de funcionamento</br>• 0.015 ms para 0.025 ms = Monitor ou de aviso</br>• 0.026 ms ou já = críticas ou de especificação |
| Disco lógico (disco físico) - comprimento de fila do disco atual (para todas as instâncias) |80 pedidos por mais de 6 minutos |
| Memória – não paginável Bytes |• Inferior a 60% das conjunto consumido = bom estado de funcionamento<br>• 61% a 80% do conjunto consumido = Monitor ou de aviso</br>• Maior conjunto 80% consumido = críticas ou de especificação |
| Memória – na memória paginável Bytes |• Inferior a 60% das conjunto consumido = bom estado de funcionamento</br>• 61% a 80% do conjunto consumido = Monitor ou de aviso</br>• Maior conjunto 80% consumido = críticas ou de especificação |
| Memória – disponível em Megabytes |• 50% de memória livre disponível ou mais = bom estado de funcionamento</br>• 25% de memória livre disponível = Monitor</br>• 10% de memória livre disponível = aviso</br>• Menor do que 100 MB ou 5% de memória livre disponível = críticas ou de especificação |
| Processador –\%tempo do processador (todas as instâncias) |• Inferior a 60% consumido = bom estado de funcionamento</br>• 61% a 90% consumida = Monitor ou atenção</br>• 91% a 100% consumida = crítico |

> [!NOTE]
> Se determinar que a infraestrutura é o culprit, recomendamos que desfragmentar discos regularmente para um melhor desempenho.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Causa: Outro processo ou software antivírus interferir com a cópia de segurança do Azure
Iremos viu várias instâncias em que outros processos no sistema do Windows tem afetado negativamente o desempenho do processo do agente de cópia de segurança do Azure. Por exemplo, se utilizar o agente do Backup do Azure e o outro programa para cópias de segurança ou, se o software antivírus está em execução e se tem um bloqueio no ficheiros a cópia de segurança, o múltiplo bloqueia em ficheiros poderão causar a contenção. Nesta situação, a cópia de segurança poderá falhar ou a tarefa pode demorar mais do que esperado.

Melhor neste cenário para recomenda-se desativar o programa de cópia de segurança para ver se o tempo de cópia de segurança para o agente de cópia de segurança do Azure é alterado. Normalmente, certificando-se de que o várias tarefas de cópia de segurança não estão em execução em simultâneo é suficiente para impedir que afetem entre si.

Para os programas antivírus, recomendamos que exclua os seguintes ficheiros e localizações:

* C:\Programas\Microsoft c:\Programas\Microsoft Azure Recovery Services Agent\bin\cbengine.exe como um processo
* C:\Program Files\Microsoft Azure Recovery Services Agent\ pastas
* Rascunho localização (se não estiver a utilizar a localização padrão)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Causa: Agente de cópia de segurança em execução numa máquina virtual do Azure
Se estiver a executar o agente de cópia de segurança numa VM, o desempenho será mais lento do que quando executá-la no computador físico. Isto é esperado devido a limitações de IOPS.  No entanto, pode otimizar o desempenho, mudar as unidades de dados que estão a cópia de segurança para o Premium Storage do Azure. Estamos a trabalhar corrigir este problema e a correção estará disponível numa versão futura.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Causa: Cópia de segurança de um grande número (milhões) de ficheiros
Mover um grande volume de dados irá demorar mais do que mover de um volume de dados mais pequeno. Em alguns casos, tempo de cópia de segurança está relacionado com não apenas o tamanho dos dados, mas também o número de ficheiros ou pastas. Isto é especialmente verdadeiro quando estão a ser feitas milhões de ficheiros pequenos (alguns bytes para alguns quilobytes).

Este comportamento ocorre porque enquanto estiver a fazer cópias de segurança de dados e movê-los para o Azure, Azure em simultâneo é cataloging os ficheiros. Em alguns cenários de raros, a operação de catálogo pode demorar mais tempo que o esperado.

Os indicadores seguintes podem ajudar a compreender o valor e em conformidade funcionam nos passos seguintes:

* **IU está a ser mostrada progresso para a transferência de dados**. Os dados ainda estão a ser transferidos. A largura de banda de rede ou o tamanho dos dados poderá estar a causar atrasos.
* **IU é não é possível mostrar progresso para a transferência de dados**. Abra os registos localizados na C:\Microsoft Azure Recovery Services Agent\Temp e, em seguida, verifique a entrada de FileProvider::EndData nos registos. Esta entrada significa que a transferência de dados foi concluída e a operação de catálogo está a acontecer. Não cancele as tarefas de cópia de segurança. Em vez disso, aguarde um pouco já está a conclusão da operação de catálogo. Se o problema persistir, contacte [suporte do Azure](https://portal.azure.com/#create/Microsoft.Support).
