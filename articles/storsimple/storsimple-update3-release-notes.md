---
title: "Notas de versão do StorSimple 8000 série Update 3 | Microsoft Docs"
description: "Descreve as novas funcionalidades, problemas e soluções para StorSimple 8000 série Update 3."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 2158aa7a-4ac3-42ba-8796-610d1adb984d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/25/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b8b230904e1e079417c3b39bbc281bc3a87668a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>Atualizar as notas de versão 3 para o seu dispositivo de série 8000 do StorSimple

## <a name="overview"></a>Descrição geral
As notas de versão seguintes descrevem as novas funcionalidades e identificam os problemas críticos abertos para StorSimple 8000 série Update 3. Também contêm uma lista das atualizações de software StorSimple incluído nesta versão. 

Atualização 3 pode ser aplicada a todos os dispositivos StorSimple com o lançamento (GA) ou atualização 0.1 através da atualização 2.2. A versão de dispositivo associada com o Update 3 é 6.3.9600.17759.

Reveja as informações contidas nas notas de versão antes de implementar a atualização na sua solução StorSimple.

> [!IMPORTANT]
> * A atualização 3 tem o software de dispositivos, LSI controladores e firmware e Storport e Spaceport atualizações. Demora aproximadamente 1,5-2 horas para instalar esta atualização. 
> * Para os novos lançamentos, poderá não ver atualizações imediatamente porque fazemos de uma implementação faseada de atualizações. Aguarde alguns dias e, em seguida, análise de atualizações novamente como estes deixará de estar disponível em breve.
> 
> 

## <a name="whats-new-in-update-3"></a>Novidades na atualização 3
Foram efetuadas as seguintes melhorias de chaves e correções de erros no Update 3.

* **Automatizada as alterações de libertação do espaço** – iniciar atualização 3, os algoritmos de recuperação de espaço executados no controlador de reserva do sistema, resultando em execução mais rápida. Para obter mais informações sobre as portas que são necessárias para trabalhar com a recuperação de espaço, consulte o [StorSimple requisitos de rede](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).
* **Melhoramentos de desempenho** – Update 3 melhorou desempenho de leitura e escrita para a nuvem.
* **Melhorias de migração** – nesta versão, vários erros correções e melhorias foram efetuadas para a funcionalidade de migração de dispositivos das séries 5000/7000 para dispositivos de 8000 série. Para obter mais informações sobre como utilizar a funcionalidade de migração, aceda a [migração a partir de dispositivos de série de 5000/7000 dispositivo da 8000 série](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b). 
* **Monitorização correções relacionadas** - nesta versão, erros relacionados com a monitorização de gráficos, dashboard de serviço, e o dashboard do dispositivo foram corrigidos.

## <a name="issues-fixed-in-update-3"></a>Problemas fixos em Update 3
As tabelas seguintes fornece um resumo dos problemas que foram corrigidos no Update 3.    

| Não | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Migração de dados do lado do anfitrião |Na versão anterior, a aplicação de nuvem do StorSimple foi ficar offline durante uma migração de dados do lado do anfitrião. Este problema ser corrigido nesta versão. |Não |Sim |
| 2 |Volumes localmente afixados |Na versão anterior, ocorreram problemas relacionados com falhas e/s, falhas de conversão do volume e falhas de datapath para volumes afixados localmente. Estes problemas foram causado de raiz e fixo nesta versão. |Sim |Não |
| 3 |Monitorização |Ocorreram vários problemas relacionados com unidades de relatórios e a monitorização, bem como os gráficos de dashboard do dispositivo onde informações incorretas tiver sido apresentadas para volumes afixados localmente. Estes problemas estiverem corrigidos nesta versão. |Sim |Não |
| 4 |E/s de escritas pesada |Quando utilizar StorSimple para cargas de trabalho que envolvem escritas pesadas, o utilizador seria depare com um pouco frequentes erros onde o conjunto de trabalho estava a ser em camadas para a nuvem. Nesta versão, é corrigido este erro. |Sim |Sim |
| 5 |Cópia de segurança |Em determinadas situações raras, nas versões anteriores do software, quando o utilizador que demorou a cópia de segurança de um clone remoto, executam erros de nuvem e a operação teria o erro. Nesta versão, o problema e a operação for concluída com êxito. |Sim |Sim |
| 6 |Política de cópia de segurança |Em determinadas situações raras, as versões anteriores do software, surgiu a um erro relacionado com a eliminação da política de cópia de segurança. Este problema ser corrigido nesta versão. |Sim |Sim |

## <a name="known-issues-in-update-3"></a>Problemas conhecidos no Update 3
A tabela seguinte fornece um resumo dos problemas conhecidos desta versão.

| Não. | Funcionalidade | Problema | Comentários / solução | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Quórum de disco |Em situações raras, se a maioria dos discos no bastidor EBOD de um dispositivo 8600 estiver desligada, resultando em quórum sem disco, em seguida, o agrupamento de armazenamento será fique offline. Irá permanecer offline, mesmo se os discos estão ligados de novo. |Terá de reiniciar o dispositivo. Se o problema persistir, contacte a Microsoft Support para passos seguintes. |Sim |Não |
| 2 |ID de controlador incorreto |Quando é efetuada uma substituição de controlador, controlador 0 poderá aparecer como controlador 1. Durante a substituição de controlador, quando a imagem ser carregada a partir do nó de elemento de rede, o ID de controlador pode aparecer inicialmente como ID de. o controlador de ponto a ponto Em situações raras, este comportamento também pode ser visto após um reinício do sistema. |Não é necessária qualquer ação do utilizador. Esta situação irá resolver-se automaticamente depois de concluída a substituição de controlador. |Sim |Não |
| 3 |Contas de armazenamento |Com o serviço de armazenamento para eliminar a conta de armazenamento é um cenário não suportado. Esta funcionalidade irá originar uma situação em que não não possível obter dados do utilizador. | |Sim |Sim |
| 4 |Ativação pós-falha do dispositivo |Não é suportada a várias ativações pós-falha de um contentor de volume do mesmo dispositivo de origem para os dispositivos de destino diferente. Ativação pós-falha de um único dispositivo Inativos para vários dispositivos faz com que os contentores de volume no primeiro dispositivo de ativação pós-falha perder a propriedade de dados. Após a ativação pós-falha, estes contentores de volume serão apresentados ou comportar-se de forma diferente quando vê-las no portal clássico do Azure. | |Sim |Não |
| 5 |Instalação |Durante a placa de StorSimple para a instalação do SharePoint, tem de fornecer um IP do dispositivo para que a instalação seja concluída com êxito. | |Sim |Não |
| 6 |Web proxy |Se a configuração de proxy web tem HTTPS como o protocolo especificado, em seguida, a comunicação de serviço de dispositivo será afetada e o dispositivo será fique offline. Pacotes de suporte também serão gerados no processo de consumir recursos significativos no seu dispositivo. |Certifique-se de que o URL de proxy web tem HTTP como o protocolo especificado. Para obter mais informações, veja [Configure web proxy for your device (Configurar o proxy Web para o seu dispositivo)](storsimple-configure-web-proxy.md). |Sim |Não |
| 7 |Web proxy |Se configurar e ativar o proxy da web num dispositivo registado, terá de reiniciar o controlador de Active Directory no seu dispositivo. | |Sim |Não |
| 8 |Latência de nuvem de alta e carga de trabalho de e/s elevada |Quando o dispositivo StorSimple encontra uma combinação de latências muito elevado de nuvem (ordem de segundos) e a elevada carga de trabalho de e/s, os volumes do dispositivo aceda num Estado degradado e a e/s pode falhar com um erro de "device não preparado". |Será necessário reiniciar os controladores de dispositivo ou execute uma ativação pós-falha de dispositivo para recuperar desta situação manualmente. |Sim |Não |
| 9 |Azure PowerShell |Quando utiliza o cmdlet do StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object - primeiro 1 - espera** para selecionar o objeto primeiro para que possa criar uma nova **VolumeContainer** objeto, o cmdlet devolve todos os objetos. |Moldar o cmdlet parênteses da seguinte forma: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object - primeiro 1 - espera** |Sim |Sim |
| 10 |Migração |Quando são transmitidos vários contentores de volume para a migração, ETA para cópia de segurança mais recente é preciso apenas para o contentor de volume primeiro. Além disso, migração de paralela será iniciado depois das primeiros 4 cópias de segurança no contentor de volume do primeiro são migradas. |Recomendamos que migrar de um contentor de volume cada vez. |Sim |Não |
| 11 |Migração |Após o restauro, os volumes de mensagens em fila não são adicionados à política de cópia de segurança ou o grupo de disco virtual. |Terá de adicionar estes volumes a uma política de cópia de segurança para criar cópias de segurança. |Sim |Sim |
| 12 |Migração |Depois de concluída a migração, o dispositivo de séries 5000/7000 não têm de aceder os contentores de dados migrados. |Recomendamos que elimina os contentores de dados migrados após a migração estar concluída e consolidada. |Sim |Não |
| 13 |Clone e DR |Um dispositivo StorSimple com o Update 1 não é possível clonar ou efetuar a recuperação de desastres num dispositivo com o software de pré-atualização 1. |Terá de atualizar o dispositivo de destino a atualizar 1 para permitir estas operações |Sim |Sim |
| 14 |Migração |Cópia de segurança de configuração para a migração poderá falhar num dispositivo séries 5000 7000 quando existem grupos de volume com nenhuma volumes associados. |Elimine todos os grupos de volume vazio com nenhuma volumes associados e, em seguida, repita a cópia de segurança de configuração. |Sim |Não |
| 15 |Cmdlets do PowerShell do Azure e volumes localmente afixados |Não é possível criar um volume localmente afixado através de cmdlets do Azure PowerShell. (Qualquer volume que criar através do Azure PowerShell irá ser colocado em camadas.) Além disso não utilize os cmdlets do PowerShell do Azure para modificar quaisquer propriedades de um volume localmente afixado, como o efeito indesejado de modificar o tipo de volume pode ter a em camadas. |Utilize sempre o serviço StorSimple Manager para configurar e modificar volumes localmente afixados.  |Sim |Não |
| 16 |Espaço disponível para volumes afixados localmente |Se eliminar um volume localmente afixado, o espaço disponível para novos volumes não pode ser atualizado imediatamente. O serviço StorSimple Manager atualiza o espaço local disponível aproximadamente a cada hora. |Aguarde uma hora antes de tentar criar o novo volume. |Sim |Não |
| 17 |Volumes localmente afixados |A tarefa de restauro expõe a cópia de segurança do instantâneo temporário no catálogo de cópias de segurança, mas apenas para a duração da tarefa de restauro. Além disso, expõe um grupo de disco virtual com o prefixo **tmpCollection** no **políticas de cópia de segurança** página, mas apenas para a duração da tarefa de restauro. |Este comportamento pode ocorrer se a tarefa de restauro tem apenas localmente afixado volumes ou uma combinação de volumes localmente afixados e em camadas. Se a tarefa de restauro inclui apenas os volumes em camadas, em seguida, este comportamento não ocorrerá. Não é necessária nenhuma intervenção do utilizador. |Sim |Não |
| 18 |Volumes localmente afixados |Se cancelar uma tarefa de restauro e uma ativação pós-falha controlador ocorre imediatamente posteriormente, a tarefa de restauro irá mostrar **falha** em vez de **cancelado**. Se uma tarefa de restauro falhar e uma ativação pós-falha controlador ocorre imediatamente posteriormente, a tarefa de restauro irá mostrar **cancelado** em vez de **falha**. |Este comportamento pode ocorrer se a tarefa de restauro tem apenas localmente afixado volumes ou uma combinação de volumes localmente afixados e em camadas. Se a tarefa de restauro inclui apenas os volumes em camadas, em seguida, este comportamento não ocorrerá. Não é necessária nenhuma intervenção do utilizador. |Sim |Não |
| 19 |Volumes localmente afixados |Se cancelar uma tarefa de restauro ou se falha um restauro e, em seguida, ocorre uma ativação pós-falha de controlador, aparece uma tarefa de restauro adicionais a **tarefas** página. |Este comportamento pode ocorrer se a tarefa de restauro tem apenas localmente afixado volumes ou uma combinação de volumes localmente afixados e em camadas. Se a tarefa de restauro inclui apenas os volumes em camadas, em seguida, este comportamento não ocorrerá. Não é necessária nenhuma intervenção do utilizador. |Sim |Não |
| 20 |Volumes localmente afixados |Se tentar converter um volume em camadas (criado e clonado com atualização 1.2 ou anterior) para um volume localmente afixado e o dispositivo está a ficar sem espaço ou existe uma falha de nuvem, em seguida, o clone(s) pode estar danificado. |Este problema ocorre apenas com volumes que foram software criada e clonado com anterior à atualização 2.1. Deve ser um cenário de pouco frequente. | | |
| 21 |Conversão de volume |Não atualizar ACRs ligados a um volume, enquanto uma conversão do volume está em curso (camadas para afixado localmente ou vice-versa). Atualizar os ACRs pode resultar em danos nos dados. |Se for necessário, atualize ACRs antes da conversão de volume e não efetuar mais ACR atualizações com a conversão em curso. | | |
| 22 |Atualizações |Ao aplicar Update 3, o **manutenção** página no portal clássico do Azure irá apresentar a mensagem seguinte relacionadas com a atualização 2 - "série 8000 do StorSimple atualização 2 inclui a capacidade para a Microsoft para registo de forma pró-ativa recolher informações do seu dispositivo quando detectarmos problemas potenciais". Isto é enganar como indica que o dispositivo está a ser atualizado para a atualização 2. Depois do dispositivo for succeesfully atualizado para o Update 3, esta mensagem desaparecerá. |Este comportamento será corrigido numa versão futura. |Sim |Não |

## <a name="controller-and-firmware-updates-in-update-3"></a>Controlador e atualizações de firmware no Update 3
Esta versão foi LSI atualizações de controladores e firmware. Para obter mais informações sobre como instalar o controlador de LSI e atualizações de firmware, consulte [instalar Update 3](storsimple-install-update-3.md) no dispositivo StorSimple.

## <a name="virtual-device-updates-in-update-3"></a>Atualizações ao dispositivo virtual no Update 3
Não é possível aplicar esta atualização para a aplicação de nuvem do StorSimple (também conhecido como o dispositivo virtual). Novos dispositivos virtuais terão de ser criado. 

## <a name="next-step"></a>Passo seguinte
Saiba como [instalar Update 3](storsimple-install-update-3.md) no dispositivo StorSimple.

