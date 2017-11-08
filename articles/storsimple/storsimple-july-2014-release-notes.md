---
title: "Notas de versão de versão de lançamento de 8000 do StorSimple | Microsoft Docs"
description: "Descreve as novas funcionalidades, abra problemas e soluções disponíveis de Julho de 2014 versão do Microsoft Azure StorSimple."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 12f1796e-37c3-42b4-b997-a84fc1950c20
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: e6e087ad61ee54fef3552895c00a478fa51e2072
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="storsimple-8000-series-release-version-release-notes---july-2014"></a>Notas de versão de versão de lançamento do StorSimple 8000 série - Julho de 2014

> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Descrição geral

As notas de versão siga identificam os problemas abertos críticos para a série 8000 do StorSimple lançamento de disponibilidade geral (DG) de Julho de 2014 do Microsoft Azure StorSimple. Esta versão corresponde à versão do software 6.3.9600.17215.

Salvo especificação em contrário, estas notas de versão aplicam-se a todos os modelos do dispositivo StorSimple. As notas de versão continuamente são atualizadas; à medida que são descobertos problemas críticos que requerem uma solução, estes são adicionados. Antes de implementar a solução do Microsoft Azure StorSimple, considere as seguintes informações.

## <a name="known-issues-in-this-release"></a>Problemas conhecidos desta versão

A tabela seguinte fornece um resumo dos problemas conhecidos desta versão.  

| Não. | Funcionalidade | Problema | Comentários/solução | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Reposição de fábrica |Em alguns casos, quando efetua uma reposição de fábrica do dispositivo StorSimple pode estar bloqueada e apresentar esta mensagem: **reposição de fábrica está em curso (fase 8)**. Isto acontece se premir CTRL + C enquanto o cmdlet está em curso. |Não é prima CTRL + C depois de iniciar uma reposição de fábrica. Se já estiver a trabalhar neste estado, contacte a Microsoft Support para passos. |Sim |Não |
| 2 |Quórum de disco |Em situações raras, se a maioria dos discos no bastidor EBOD de um dispositivo 8600 estiver desligada, resultando em quórum sem disco, em seguida, o agrupamento de armazenamento fique offline. Irá permanecer offline, mesmo se os discos estão ligados de novo. |Terá de reiniciar o dispositivo. Se o problema persistir, contacte a Microsoft Support para passos seguintes. |Sim |Não |
| 3 |Falhas de instantâneos de nuvem |Em situações raras, um instantâneo na nuvem pode falhar com o erro **foi atingido o limite cópia de segurança máximo**. Isto ocorre se exceder 255 clones online no mesmo dispositivo, do volume original mesmo que tenha sido eliminado. | |Sim |Sim |
| 4 |ID de controlador incorreto |Quando é efetuada uma substituição de controlador, controlador 0 poderá aparecer como controlador 1. Durante a substituição de controlador, quando a imagem ser carregada a partir do nó de elemento de rede, o ID de controlador pode aparecer inicialmente como ID de. o controlador de ponto a ponto Em situações raras, este comportamento também pode ser visto após um reinício do sistema. |Não é necessária qualquer ação do utilizador. Esta situação irá resolver-se automaticamente depois de concluída a substituição de controlador. |Sim |Não |
| 5 |Gráficos de monitorização de dispositivos |O serviço StorSimple Manager, os gráficos de monitorização de dispositivos não funcionam quando básica ou autenticação NTLM está ativada na configuração do servidor proxy para o dispositivo. |Modificar a configuração de proxy web para o dispositivo registado no serviço StorSimple Manager, para que a autenticação está definida como NONE. Para tal, execute o Windows PowerShell para o cmdlet Set-HcsWebProxy do StorSimple. |Sim |Sim |
| 6 |Contas de armazenamento |Com o serviço de armazenamento para eliminar a conta de armazenamento é um cenário não suportado. Esta funcionalidade irá originar uma situação em que não não possível obter dados do utilizador. | |Sim |Sim |
| 7 |Reativação pós-falha |Não é suportada uma reativação pós-falha dentro de 24 horas de recuperação após desastre (DR). | |Sim |Não |
| 8 |Ativação pós-falha do dispositivo |Não é suportada a várias ativações pós-falha de um contentor de volume do mesmo dispositivo de origem para os dispositivos de destino diferente. Ativação pós-falha de um único dispositivo Inativos para vários dispositivos faz com que os contentores de volume no primeiro dispositivo de ativação pós-falha perder a propriedade de dados. Após a ativação pós-falha, estes contentores de volume serão apresentados ou comportar-se de forma diferente quando vê-las no portal clássico do Azure. | |Sim |Não |
| 9 |Instalação |Durante a placa de StorSimple para a instalação do SharePoint, tem de fornecer um IP do dispositivo para a instalação seja concluída com êxito. | |Sim |Não |
| 10 |Interfaces de rede |Interfaces de rede dados 2 e dados 3 foram alternadas no software. |Contacte Support da Microsoft se necessitar de configurar estas interfaces. |Sim |Não |

