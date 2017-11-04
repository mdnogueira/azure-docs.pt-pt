---
title: "Notas de versão 4 de atualização de série de 8000 StorSimple | Microsoft Docs"
description: "Descreve as novas funcionalidades, problemas e soluções para StorSimple 8000 série atualização 4."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/04/2017
ms.author: alkohli
ms.openlocfilehash: 23f1bbb066c5b6481988ee841ad8979d78abf084
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>Notas de versão do StorSimple 8000 série atualização 4

## <a name="overview"></a>Descrição geral

As notas de versão seguintes descrevem as novas funcionalidades e identificam os problemas críticos abertos para StorSimple 8000 série atualização 4. Também contêm uma lista das atualizações de software StorSimple incluído nesta versão. 

Atualização 4 pode ser aplicada a todos os dispositivos StorSimple com o lançamento (GA) ou atualização 0.1 através da atualização 3.1. A versão de dispositivo associada a Update 4 é 6.3.9600.17820.

Reveja as informações contidas nas notas de versão antes de implementar a atualização na sua solução StorSimple.

> [!IMPORTANT]
> * Atualização 4 tem o software de dispositivos, USM de firmware, controladores de LSI e firmware, firmware de disco, Storport e Spaceport, segurança e outras atualizações do SO. Demora cerca de 4 horas para instalar esta atualização. Atualização de firmware do disco é uma atualização acontece e resulta num tempo de inatividade para o seu dispositivo. Recomendamos que aplique a atualização 4 para manter o seu dispositivo atualizados. 
> * Para os novos lançamentos, poderá não ver atualizações imediatamente porque fazemos de uma implementação faseada de atualizações. Aguarde alguns dias e, em seguida, análise de atualizações novamente como estes deixará de estar disponível em breve.

## <a name="whats-new-in-update-4"></a>Novidades na atualização 4

Foram efetuadas as seguintes melhorias de chaves e correções de erros na atualização 4.

* **Algoritmos de recuperação de espaço de automatizada mais inteligentes** – na atualização 4, a recuperação de espaço automatizada algoritmos são melhorados para ajustar os ciclos de recuperação de espaço com base no espaço reclaimed esperado disponível na nuvem. 

* **Melhoramentos de desempenho para volumes localmente afixados** – Update 4 melhorou o desempenho de volumes localmente afixados em cenários que têm a ingestão de dados de elevado (comparáveis à tamanho do volume de dados).

* **Com base em Heatmap restauro** -nas versões anteriores, após uma recuperação após desastre (DR), os dados foi restaurados a partir da nuvem com base nos padrões de acesso, resultando num desempenho lento. 

    Uma funcionalidade nova é implementada na atualização 4 que controla os dados acedidos frequentemente para criar um heatmap quando o dispositivo está a ser utilizada antes de DR (segmentos de dados mais utilizados tem térmico elevado, enquanto a menor utilizada segmentos ter térmico baixo). Depois de DR, StorSimple utiliza o heatmap automaticamente restaurar e rehydrate os dados da nuvem. 

    Todos os restauros estão agora heatmap com base em restauros. Para obter mais informações sobre a consulta e cancelar tarefas de restauro e rehydration heatmap com base, aceda a [do Windows PowerShell para StorSimple referência de cmdlet](https://technet.microsoft.com/library/dn688168.aspx).

* **Ferramenta de diagnóstico do StorSimple** – na atualização 4, uma ferramenta de diagnóstico do StorSimple, está a ser lançado para permitir mais fácil diagnosticar e resolução de problemas relacionados com o estado de funcionamento de componente do sistema, rede, desempenho e hardware. Esta ferramenta é executada através do Windows PowerShell para StorSimple. Para obter mais informações, aceda a [resolver problemas com a ferramenta de diagnóstico do StorSimple](storsimple-8000-diagnostics.md).

* **Ferramenta de migração do StorSimple baseadas na IU** -antes desta versão, a migração de dados da série de 5000 7000 necessários os utilizadores a executar uma parte do fluxo de trabalho migração através da interface do PowerShell do Azure. Nesta versão, uma ferramenta de migração de StorSimple baseadas na IU de fácil utilização é disponibilizada para o suporte para o facilitar o mesmo fluxo de trabalho de migração. Esta ferramenta também permitirá para consolidação de registos de recuperação. 

* **Alterações relacionadas com FIPS** - nesta versão em diante, FIPS está ativado por predefinição em todos os dispositivos de série 8000 do StorSimple para contas de nuvem do Microsoft Azure Government e o Azure público.

* **Atualizar as alterações** -nesta versão, tem sido corrigidos erros relacionados com falhas de atualização.

* **Alerta de falhas de disco** -é adicionado um novo alerta que avisa o utilizador de falhas de disco iminente nesta versão. Se encontrar este alerta, contacte a Microsoft Support para envie um disco de substituição. Para obter mais informações, aceda a [alertas de hardware no dispositivo StorSimple](storsimple-manage-alerts.md#hardware-alerts).

* **Alterações de substituição de controlador** -é adicionado um cmdlet que permite ao utilizador consultar o estado do processo de substituição de controlador nesta versão. Para obter mais informações, visite o [cmdlet para o estado de substituição de controlador de consulta](https://technet.microsoft.com/library/dn688168.aspx).


## <a name="issues-fixed-in-update-4"></a>Problemas fixos na atualização 4

A tabela seguinte fornece um resumo dos problemas que foram corrigidos na atualização 4.    

| Não | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Ativação pós-falha |Na versão anterior, após a ativação pós-falha, Ocorreu um problema relacionado com a limpeza observada no site do cliente. Este problema ser corrigido nesta versão. |Sim |Sim |
| 2 |Volumes localmente afixados |Na versão anterior, Ocorreu um problema para a criação de volumes relacionados para volumes afixados localmente que resulta em falhas de criação do volume. Este problema foi causada de raiz e fixo nesta versão. |Sim |Não |
| 3 |Pacote de suporte |Na versão anterior, ocorreram problemas relacionados com o pacote de suporte que resultaria numa exceção de System.OutOfMemory ou outros erros, causando uma falha de criação do pacote de suporte. Estes erros são fixo nesta versão. |Sim |Sim |
| 4 |Monitorização |Na versão anterior, não existe um problema relacionado com a monitorização de gráficos para localmente afixado volumes onde consumo é mostrado no EB. Este erro é resolvido nesta versão. |Sim |Sim |
| 5 |Migração |Na versão anterior, existem vários problemas relacionados com a fiabilidade da migração da série de 5000 7000 para dispositivos de 8000 série. Estes problemas tem sido resolvidos nesta versão. |Sim |Sim |
| 6 |Atualizar |Em versões anteriores, se tiver ocorrido uma falha de atualização, os controladores seriam entrar em modo de recuperação e, por conseguinte, o utilizador não foi possível continuar com a atualização e tem de contactar o Support da Microsoft. <br> Este comportamento foi alterado nesta versão. Se o utilizador tiver uma falha de atualização depois de ambos os controladores executam a mesma versão (Update 4), os controladores não entrar em modo de recuperação. Se o utilizador encontra desta falha, recomendamos que aguarde um bit e, em seguida, tente efetuar novamente a atualização. Foi efetuada com êxito o novamente. Se a tentativa falhar, deverá possam contactar Support da Microsoft. |Sim |Sim |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Problemas conhecidos na atualização 4 a partir de versões anteriores

Existem não existem problemas conhecidos novo na atualização 4. Para obter uma lista dos problemas transportadas para atualização 4, a partir de versões anteriores, aceda a [notas de versão da atualização 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Série-attached SCSI (SAS) controlador e atualizações de firmware na atualização 4

Esta versão tem controlador SAS e atualizações de controladores e firmware LSI. Para obter mais informações sobre como instalar estas atualizações, consulte [instalar a atualização 4](storsimple-install-update-4.md) no dispositivo StorSimple.

## <a name="virtual-device-updates-in-update-4"></a>Atualizações ao dispositivo virtual na atualização 4

Não é possível aplicar esta atualização para a aplicação de nuvem do StorSimple (também conhecido como o dispositivo virtual). Novos dispositivos virtuais terão de ser criado. 

## <a name="next-step"></a>Passo seguinte

Saiba como [instalar a atualização 4](storsimple-install-update-4.md) no dispositivo StorSimple.

