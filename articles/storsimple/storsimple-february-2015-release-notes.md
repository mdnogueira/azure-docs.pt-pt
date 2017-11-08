---
title: "Notas de versão 0,3 de atualização de 8000 do StorSimple | Microsoft Docs"
description: "Descreve as novas funcionalidades e correções, problemas abertos e soluções disponíveis de Fevereiro de 2015 versão do Microsoft Azure StorSimple (atualização 0.3)."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: b01bfd04-f9f8-45f4-ade8-95ac2b979e6a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 87a0a59d0c980baa87aa5d7f5f0e8098884b5ec9
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="storsimple-8000-series-update-03-release-notes---february-2015"></a>Notas de versão 0,3 de atualização de série de 8000 do StorSimple - Fevereiro de 2015
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Descrição geral
As seguintes notas de versão identificam os problemas abertos críticos para a atualização 0.3 lançada em Fevereiro de 2015 da série 8000 do StorSimple. Também contêm uma lista das atualizações de software e firmware StorSimple incluído nesta versão. Este é o terceiro versão depois de ter a versão de lançamento de série 8000 do StorSimple foi disponibilizada geralmente em Julho de 2014.

Esta atualização não alterar a versão do software de dispositivo a partir da atualização de Janeiro. Este continua a ser versão 6.3.9600.17312. Pode confirmar que a atualização foi instalada, verificando o **atualizado último** data. Se a data for 2/10/2015 ou posterior, em seguida, a atualização foi instalada com êxito.  

Recomendamos que procurar e aplicar as atualizações disponíveis imediatamente depois de instalar o dispositivo StorSimple. Também pode ativar as atualizações automáticas para transferir e instalar atualizações de alta prioridade da Microsoft, assim são lançadas. Para obter mais informações, consulte [atualizar o dispositivo StorSimple](storsimple-update-device.md).  

Reveja as informações contidas nas notas de versão antes de implementar a atualização na sua solução StorSimple.  

> [!IMPORTANT]
> * Utilize o serviço StorSimple Manager e não do Windows PowerShell para StorSimple para instalar a atualização de Fevereiro.   
> * Demora aproximadamente uma hora para instalar esta atualização. No entanto, se estiver a instalar as atualizações cumulativas, o processo pode demorar cerca de 3 horas para concluir.  
> * A versão de Fevereiro de StorSimple não contém quaisquer atualizações para o dispositivo virtual StorSimple. Pode continuar a aplicar as atualizações disponíveis do Windows para o dispositivo virtual, incluindo correções de segurança recente, mas não irá ver uma alteração de versão para o dispositivo virtual.  
> 
> 

Certifique-se de que os seguintes pré-requisitos são cumpridos antes de atualizar o seu dispositivo StorSimple.  

* Certifique-se de que ambos os controladores de dispositivo estão em execução antes de procurar atualizações. Se o controlador não está em execução, a análise irá falhar. Para verificar que os controladores estão em bom estado, navegue até à **estado do Hardware** sob o **manutenção** página. Se existirem componentes que **necessitem de atenção**, contacte a Microsoft Support antes de prosseguir.
* Certifique-se de que os IPs fixos para o controlador 0 e o controlador 1 encaminhável e pode ligar à Internet, como estes campos são utilizados para manutenção de atualizações para o dispositivo. Pode utilizar o [cmdlet Test-Connection](https://technet.microsoft.com/library/hh849808.aspx) um ping a um endereço conhecido fora da rede, tais como outlook.com, para verificar se o controlador tem conetividade à rede externa.
* Certifique-se de que as portas 80 e 443 estão disponíveis no dispositivo StorSimple para comunicação de saída. Para obter mais informações, consulte o [requisitos de rede para o dispositivo StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).
* Se a versão do software de dispositivos é anterior ao 6.3.9600.17312 (atualização de Outubro de 2014), desative as portas de dados 2 e dados 3, se estiver ativada, antes de iniciar a atualização. Abandonar o fileparser os dados 2 ou 3 dados portas ativadas quando tem de aplicar a atualização poderá fazer com que o controlador de dispositivo entrar em modo de recuperação. Tenha em atenção que ao desativar as interfaces de rede, todos os volumes associados serão colocados offline e a e/s serão interrompidos durante a duração da atualização.  

## <a name="whats-new-in-the-february-release"></a>Novidades na versão Fevereiro
Esta atualização contém uma correção para problema ocorreu de reposição de fábrica de dispositivos que tinham foram atualizados a partir do DG de versão para a versão de Outubro de 2014. Para obter mais informações, consulte [problemas corrigidos nesta versão](#issues-fixed-in-the-february-release).   

Esta atualização não contém novas funcionalidades.  

## <a name="issues-fixed-in-the-february-release"></a>Corrigido na versão de Fevereiro de problemas
A tabela seguinte descreve o problema foi corrigido esta atualização.  

| Não. | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Reposição de fábrica |Tentar efetuar uma reposição de fábrica no dispositivo que originalmente tinha a versão GA (versão 6.3.9600.17215) instalada, mas que foi atualizado para o Outubro versão (versão 6.3.9600.17312). O reposição de fábrica falha e o dispositivo fica instável. |Sim |Não |

## <a name="known-issues-in-the-february-release"></a>Problemas conhecidos na versão Fevereiro
A tabela seguinte fornece um resumo dos problemas conhecidos desta versão.

| Não. | Funcionalidade | Problema | Comentários/solução | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Reposição de fábrica |Em alguns casos, quando efetua uma reposição de fábrica do dispositivo StorSimple pode estar bloqueada e apresentar esta mensagem: **reposição de fábrica está em curso (fase 8)**. Isto acontece se premir CTRL + C enquanto o cmdlet está em curso. |Não é prima CTRL + C depois de iniciar uma reposição de fábrica. Se já estiver a trabalhar neste estado, contacte a Microsoft Support para passos. |Sim |Não |
| 2 |Quórum de disco |Em situações raras, se a maioria dos discos no bastidor EBOD de um 8600device estiver desligada, resultando em quórum sem disco, em seguida, o agrupamento de armazenamento estarão offline. Irá permanecer offline, mesmo se os discos estão ligados de novo. |Terá de reiniciar o dispositivo. Se o problema persistir, contacte a Microsoft Support para passos seguintes. |Sim |Não |
| 3 |Falhas de instantâneos de nuvem |Em situações raras, um instantâneo na nuvem pode falhar com o erro **foi atingido o limite cópia de segurança máximo**. Isto ocorre se exceder 255 clones online no mesmo dispositivo, do volume original mesmo que tenha sido eliminado. | |Sim |Sim |
| 4 |ID de controlador incorreto |Quando é efetuada uma substituição de controlador, controlador 0 poderá aparecer como controlador 1. Durante a substituição de controlador, quando a imagem ser carregada a partir do nó de elemento de rede, o ID de controlador pode aparecer inicialmente como ID de. o controlador de ponto a ponto Em situações raras, este comportamento também pode ser visto após um reinício do sistema. |Não é necessária qualquer ação do utilizador. Esta situação irá resolver-se automaticamente depois de concluída a substituição de controlador. |Sim |Não |
| 5 |Gráficos de monitorização de dispositivos |O serviço StorSimple Manager, os gráficos de monitorização de dispositivos não funcionam quando básica ou autenticação NTLM está ativada na configuração do servidor proxy para o dispositivo. |Modificar a configuração de proxy web para o dispositivo registado no serviço StorSimple Manager, para que a autenticação está definida como NONE. Para tal, execute o Windows PowerShell para o cmdlet Set-HcsWebProxy do StorSimple. |Sim |Sim |
| 6 |Contas de armazenamento |Com o serviço de armazenamento para eliminar a conta de armazenamento é um cenário não suportado. Esta funcionalidade irá originar uma situação em que não não possível obter dados do utilizador. | |Sim |Sim |
| 7 |Ativação pós-falha do dispositivo |Não é suportada a várias ativações pós-falha de um contentor de volume do mesmo dispositivo de origem para os dispositivos de destino diferente.    Ativação pós-falha de um único dispositivo Inativos para vários dispositivos faz com que os contentores de volume no primeiro dispositivo de ativação pós-falha perder a propriedade de dados. Após a ativação pós-falha, estes contentores de volume serão apresentados ou comportar-se de forma diferente quando vê-las no portal clássico do Azure. | |Sim |Não |
| 8 |Instalação |Durante a placa de StorSimple para a instalação do SharePoint, tem de fornecer um IP do dispositivo para que a instalação seja concluída com êxito. | |Sim |Não |
| 9 |Web proxy |Se a configuração de proxy web tem HTTPS como o protocolo especificado, em seguida, a comunicação de serviço de dispositivo será afetada e o dispositivo será fique offline. Pacotes de suporte também serão gerados no processo de consumir recursos significativos no seu dispositivo. |Certifique-se de que o URL de proxy web tem HTTP como o protocolo especificado. Obter mais informações sobre como [configurar o proxy web para o seu dispositivo](storsimple-configure-web-proxy.md). |Sim |Não |
| 10 |Web proxy |Se configurar e ativar o proxy da web num dispositivo registado, terá de reiniciar o controlador de Active Directory no seu dispositivo. | |Sim |Não |
| 11 |Latência de nuvem de alta e carga de trabalho de e/s elevada |Quando o dispositivo StorSimple encontra uma combinação de latências muito elevado de nuvem (ordem de segundos) e a elevada carga de trabalho de e/s, os volumes do dispositivo aceda num Estado degradado e a e/s pode falhar com um erro de "device não preparado". |Será necessário reiniciar os controladores de dispositivo ou execute uma ativação pós-falha de dispositivo para recuperar desta situação manualmente. |Sim |Não |

## <a name="physical-device-updates-in-the-february-release"></a>Atualizações ao dispositivo físico de Fevereiro de versão
Esta atualização corrige o problema de reposição de fábrica que ocorreram nos dispositivos que tinham foram atualizados a partir do DG de versão para a versão de Outubro de 2014. Não contém quaisquer outras atualizações para o dispositivo StorSimple.  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-february-release"></a>Controlador de SCSI (SAS) ligados à série e atualizações de firmware de Fevereiro de versão
Esta versão não contém quaisquer atualizações para o controlador de SCSI (SAS) ligados à série ou o firmware. A atualização do controlador estava em Outubro, versão 2014.  

## <a name="virtual-device-updates-in-the-february-release"></a>Atualizações ao dispositivo virtual de Fevereiro de versão
Esta versão não contém quaisquer atualizações para o dispositivo virtual. Aplicar esta atualização não irá alterar a versão do software de um dispositivo virtual.

