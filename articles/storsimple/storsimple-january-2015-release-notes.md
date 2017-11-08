---
title: "Notas de versão 0.2 de atualização de 8000 do StorSimple | Microsoft Docs"
description: "Descreve as novas funcionalidades e correções, problemas abertos e soluções disponíveis para Janeiro de 2015 versão do Microsoft Azure StorSimple (atualização 0,2)."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: d9684ae3-b38f-4678-9d70-e5dbc6b03350
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: f2b147f32f10208b8daa391095a7d0094a0c2c09
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="storsimple-8000-series-update-02-release-notes---january-2015"></a>Notas de versão 0.2 de atualização de série de 8000 do StorSimple - Janeiro de 2015
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Descrição geral
As seguintes notas de versão identificam os problemas abertos críticos para a versão de Janeiro de 2015 do Microsoft Azure StorSimple. Também contêm uma lista das atualizações de software e firmware StorSimple incluído nesta versão. Esta é a segunda versão depois de ter a versão de lançamento de série 8000 do StorSimple foi disponibilizada geralmente em Julho de 2014.

Esta atualização não alterar a versão do software de dispositivo físico a partir da atualização de Outubro. Este continua a ser versão 6.3.9600.17312. A imagem utilizada pela imagem dispositivo virtual foi alterado nesta versão. Por conseguinte, todos os dispositivos virtuais novos criados após 20/1/2015 apresentará a versão como 6.3.9600.17361.  

Reveja as seguintes informações contidas nas notas de versão para a atualização de Janeiro de 2015.

> [!IMPORTANT]
> * Esta atualização não está disponível através do Windows Update e não pode ser instalada como outras atualizações. O dispositivo não irá receber esta atualização, mesmo se tiver aplicado as atualizações utilizando o portal clássico do Azure. Esta atualização apenas se aplica a dispositivos virtuais criados após 20 de Janeiro de 2015. 
> * A versão de Janeiro do StorSimple não contém quaisquer atualizações para o dispositivo físico StorSimple. Pode continuar a aplicar as atualizações disponíveis do Windows para o dispositivo virtual, incluindo correções de segurança recente, mas não irá ver uma alteração de versão para o dispositivo físico StorSimple.
> 
> 

## <a name="whats-new-in-the-january-release"></a>Novidades na versão Janeiro
Esta atualização contém uma correção relacionados com os volumes ficar offline no dispositivo virtual. (Consulte [problemas corrigidos nesta versão](#issues-fixed-in-the-january-release).)  

A atualização não contém novas funcionalidades.  

## <a name="issues-fixed-in-the-january-release"></a>Corrigido na versão de Janeiro de problemas
A tabela seguinte descreve o problema foi corrigido esta atualização.

| Não. | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Volumes ficar offline |Quando as latências de nuvem de alta são mantidas durante vários minutos, os volumes do dispositivo virtual StorSimple fique offline nos anfitriões. Esta correção aumenta o limiar para latências de nuvem, deste modo, minimizando as situações que faria com que os volumes fique offline nos anfitriões. |Não |Sim |

## <a name="known-issues-in-the-january-release"></a>Problemas conhecidos na versão Janeiro
A tabela seguinte fornece um resumo dos problemas conhecidos desta versão.

| Não. | Funcionalidade | Problema | Comentários/solução | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Reposição de fábrica |Em alguns casos, quando efetua uma reposição de fábrica do dispositivo StorSimple pode estar bloqueada e apresentar esta mensagem: **reposição de fábrica está em curso (fase 8).** Isto acontece se premir CTRL + C enquanto o cmdlet está em curso. |Não é prima CTRL + C depois de iniciar uma reposição de fábrica. Se já estiver a trabalhar neste estado, contacte a Microsoft Support para passos. |Sim |Não |
| 2 |Quórum de disco |Em situações raras, se a maioria dos discos no bastidor EBOD de um dispositivo 8600 estiver desligada, resultando em quórum sem disco, em seguida, o agrupamento de armazenamento estarão offline. Irá permanecer offline, mesmo se os discos estão ligados de novo. |Terá de reiniciar o dispositivo. Se o problema persistir, contacte a Microsoft Support para passos seguintes. |Sim |Não |
| 3 |Falhas de instantâneos de nuvem |Em situações raras, um instantâneo na nuvem pode falhar com o erro **foi atingido o limite cópia de segurança máximo**. Isto ocorre se exceder 255 clones online no mesmo dispositivo, do volume original mesmo que tenha sido eliminado. | |Sim |Sim |
| 4 |ID de controlador incorreto |Quando é efetuada uma substituição de controlador, controlador 0 poderá aparecer como controlador 1. Durante a substituição de controlador, quando a imagem ser carregada a partir do nó de elemento de rede, o ID de controlador pode aparecer inicialmente como ID de. o controlador de ponto a ponto  Em situações raras, este comportamento também pode ser visto após um reinício do sistema. |Não é necessária qualquer ação do utilizador. Esta situação irá resolver-se automaticamente depois de concluída a substituição de controlador. |Sim |Não |
| 5 |Gráficos de monitorização de dispositivos |O serviço StorSimple Manager, os gráficos de monitorização de dispositivos não funcionam quando básica ou autenticação NTLM está ativada na configuração do servidor proxy para o dispositivo. |Modificar a configuração de proxy web para o dispositivo registado no serviço StorSimple Manager, para que a autenticação está definida como NONE. Para tal, execute o Windows PowerShell para o cmdlet Set-HcsWebProxy do StorSimple. |Sim |Sim |
| 6 |Contas de armazenamento |Com o serviço de armazenamento para eliminar a conta de armazenamento é um cenário não suportado. Esta funcionalidade irá originar uma situação em que não não possível obter dados do utilizador. | |Sim |Sim |
| 7 |Ativação pós-falha do dispositivo |Não é suportada a várias ativações pós-falha de um contentor de volume do mesmo dispositivo de origem para os dispositivos de destino diferente. |Ativação pós-falha de um único dispositivo Inativos para vários dispositivos faz com que os contentores de volume no primeiro dispositivo de ativação pós-falha perder a propriedade de dados. Após a ativação pós-falha, estes contentores de volume serão apresentados ou comportar-se de forma diferente quando vê-las no portal clássico do Azure. |Sim |Não |
| 8 |Instalação |Durante a placa de StorSimple para a instalação do SharePoint, tem de fornecer um IP do dispositivo para que a instalação seja concluída com êxito. | |Sim |Não |
| 9 |Web proxy |Se a configuração de proxy web tem HTTPS como o protocolo especificado, em seguida, a comunicação de serviço de dispositivo será afetada e o dispositivo será fique offline. Pacotes de suporte também serão gerados no processo de consumir recursos significativos no seu dispositivo. |Certifique-se de que o URL de proxy web tem HTTP como o protocolo especificado. Ver mais informações sobre como [configurar o proxy web para o seu dispositivo](storsimple-configure-web-proxy.md). |Sim |Não |
| 10 |Web proxy |Se configurar e ativar o proxy da web num dispositivo registado, terá de reiniciar o controlador de Active Directory no seu dispositivo. | |Sim |Não |
| 11 |Latência de nuvem de alta e carga de trabalho de e/s elevada |Quando o dispositivo StorSimple encontra uma combinação de latências muito elevado de nuvem (ordem de segundos) e a elevada carga de trabalho de e/s, os volumes do dispositivo aceda num Estado degradado e a e/s pode falhar com um erro de "device não preparado". |Será necessário reiniciar os controladores de dispositivo ou execute uma ativação pós-falha de dispositivo para recuperar desta situação manualmente. |Sim |Não |

## <a name="physical-device-updates-in-the-january-release"></a>Atualizações ao dispositivo físico de Janeiro de versão
Esta atualização não contém quaisquer outras alterações para o dispositivo StorSimple.

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-january-release"></a>Controlador de SCSI (SAS) ligados à série e atualizações de firmware de Janeiro de versão
Esta versão não contém quaisquer atualizações para o controlador de SCSI (SAS) ligados à série ou o firmware. A atualização do controlador estava em Outubro, versão 2014. 

## <a name="virtual-device-updates-in-the-january-release"></a>Atualizações ao dispositivo virtual de Janeiro de versão
Esta versão contém uma imagem atualizada para o dispositivo virtual. Todos os dispositivos virtuais criados após 20 de Janeiro de 2015 apresentará a versão do software como 6.3.9600.17361.

