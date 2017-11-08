---
title: "Notas de versão do StorSimple 8000 série atualização 2.2 | Microsoft Docs"
description: "Descreve as novas funcionalidades, problemas e soluções para 2.2 de atualização de série do StorSimple 8000."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 5cf03ea8-2a0f-4552-b6dc-7ea517783d7b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 06c14bdd24dd24a98b3838a2ba73b657ce56785a
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>Notas de versão do StorSimple 8000 série atualização 2.2
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Descrição geral
As notas de versão seguintes descrevem as novas funcionalidades e identificam os problemas críticos abertos para 2.2 de atualização de série do StorSimple 8000. Também contêm uma lista das atualizações de software StorSimple incluído nesta versão.

Atualização 2.2 pode ser aplicada a todos os dispositivos StorSimple com o lançamento (GA) ou atualização 0.1 através da atualização 2.1. A versão de dispositivo associada a atualização 2.2 é 6.3.9600.17708.

Reveja as informações contidas nas notas de versão antes de implementar a atualização na sua solução StorSimple.

> [!IMPORTANT]
> * Atualização 2.2 tem únicas atualizações de software. Demora aproximadamente 1,5-2 horas para instalar esta atualização. 
> * Se estiver a executar 2.1 de atualização, recomendamos que aplique a atualização 2.2 logo que possível.
> * Para os novos lançamentos, poderá não ver atualizações imediatamente porque fazemos de uma implementação faseada de atualizações. Aguarde alguns dias e, em seguida, análise de atualizações novamente como estes deixará de estar disponível em breve.
> 
> 

## <a name="whats-new-in-update-22"></a>Novidades na atualização 2.2
Foram efetuadas as seguintes melhorias de chaves 2.2 de atualização.

* **Automatizada otimização de recuperação de espaço** – quando forem eliminados dados nos volumes de aprovisionamento dinâmico, a necessidade de blocos de armazenamento não utilizados para ser recuperada. Esta versão foi melhorado o processo de recuperação de espaço da nuvem resultando num espaço não utilizado se tornarem disponíveis mais rapidamente em comparação com as versões anteriores.
* **Melhoramentos de desempenho de instantâneos** – 2.2 de atualização foi melhorado o tempo para processar uma nuvem de instantâneos em determinados cenários em que grandes volumes que estão a ser utilizados e não existe mínima para nenhum fluxo de dados. Um cenário que seria beneficiar este melhoramento seria os volumes de arquivo.
* **A proteção do pacote de suporte de recolha** – foram melhorias da forma que o pacote de suporte é recolhido e carregado nesta versão. 
* **Atualizar melhoramentos de fiabilidade** – esta versão foi correções de erros que resultam de uma fiabilidade melhorada de atualização.

## <a name="issues-fixed-in-update-22"></a>Problemas fixos em 2.2 de atualização
As tabelas seguintes fornece um resumo dos problemas que foram corrigidos 2.2 atualizações e 2.1.    

| Não | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Desempenho de anfitrião |Na versão anterior, problemas de desempenho do lado do anfitrião foram observados durante a criação de um volume localmente afixado e durante a conversão de um volume em camadas para um volume afixado localmente. Estes problemas estiverem corrigidos nesta versão, deste modo, causando uma melhoria no desempenho durante os procedimentos de criação e a conversão do volume anfitrião. |Sim |Não |
| 2 |Volumes localmente afixados |Em situações raras, o sistema seria falhas ao criar um volume afixado localmente. Corrigir este erro nesta versão. |Sim |Não |
| 3 |Criação de camadas |Ocorreram falhas esporádicas quando os metadados para os dispositivos de nuvem do StorSimple (8010 e 8020) em camadas para a nuvem. Este problema ser corrigido nesta versão. |Não |Sim |
| 4 |Criação de instantâneos |Ocorreram problemas relacionados para a criação de instantâneos incrementais nos cenários com grandes volumes e mínimo para nenhum fluxo de dados. Estes problemas estiverem corrigidos nesta versão. |Sim |Sim |
| 5 |Autenticação Openstack |Quando utilizar Openstack como o fornecedor de serviço em nuvem, o utilizador seria depare com um pouco frequentes erros relacionados com a autenticação onde o parser JSON resultou numa falha. Nesta versão, é corrigido este erro. |Sim |Não |
| 6 |Copiar do lado do anfitrião |Em versões anteriores do software, um influxo erros relacionados com a temporização de ODX foi visto quando copiar os dados a partir de um volume para outro volume. Isto resulta numa ativação pós-falha de controlador e o sistema potencialmente foi possível entrar em modo de recuperação. Nesta versão, é corrigido este erro. |Sim |Não |
| 7 |Windows Management Instrumentation (WMI) |Nas versões anteriores do software, existem várias instâncias de falha de proxy web com a exceção "<ManagementException> falha no fornecedor de carga". Este erro foi atribuído a uma fuga de memória WMI e agora é fixo. |Sim |Não |
| 8 |Atualizar |Em determinadas situações raras, nas versões anteriores do software, o utilizador recebeu um "CisPowershellHcsscripterror" ao tentar analisar ou instalar atualizações. Este problema ser corrigido nesta versão. |Sim |Sim |
| 9 |Pacote de suporte |Nesta versão, ter sido melhoramentos para a forma como o pacote de suporte é recolhido e carregado. |Sim |Sim |

## <a name="known-issues-in-update-22"></a>Problemas conhecidos no 2.2 de atualização
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
| 15 |Cmdlets do PowerShell do Azure e volumes localmente afixados |Não é possível criar um volume localmente afixado através de cmdlets do Azure PowerShell. (Qualquer volume que criar através do Azure PowerShell irá ser colocado em camadas.) |Utilize sempre o serviço StorSimple Manager para configurar volumes localmente afixados. |Sim |Não |
| 16 |Espaço disponível para volumes afixados localmente |Se eliminar um volume localmente afixado, o espaço disponível para novos volumes não pode ser atualizado imediatamente. O serviço StorSimple Manager atualiza o espaço local disponível aproximadamente a cada hora. |Aguarde uma hora antes de tentar criar o novo volume. |Sim |Não |
| 17 |Volumes localmente afixados |A tarefa de restauro expõe a cópia de segurança do instantâneo temporário no catálogo de cópias de segurança, mas apenas para a duração da tarefa de restauro. Além disso, expõe um grupo de disco virtual com o prefixo **tmpCollection** no **políticas de cópia de segurança** página, mas apenas para a duração da tarefa de restauro. |Este comportamento pode ocorrer se a tarefa de restauro tem apenas localmente afixado volumes ou uma combinação de volumes localmente afixados e em camadas. Se a tarefa de restauro inclui apenas os volumes em camadas, em seguida, este comportamento não ocorrerá. Não é necessária nenhuma intervenção do utilizador. |Sim |Não |
| 18 |Volumes localmente afixados |Se cancelar uma tarefa de restauro e uma ativação pós-falha controlador ocorre imediatamente posteriormente, a tarefa de restauro irá mostrar **falha** em vez de **cancelado**. Se uma tarefa de restauro falhar e uma ativação pós-falha controlador ocorre imediatamente posteriormente, a tarefa de restauro irá mostrar **cancelado** em vez de **falha**. |Este comportamento pode ocorrer se a tarefa de restauro tem apenas localmente afixado volumes ou uma combinação de volumes localmente afixados e em camadas. Se a tarefa de restauro inclui apenas os volumes em camadas, em seguida, este comportamento não ocorrerá. Não é necessária nenhuma intervenção do utilizador. |Sim |Não |
| 19 |Volumes localmente afixados |Se cancelar uma tarefa de restauro ou se falha um restauro e, em seguida, ocorre uma ativação pós-falha de controlador, aparece uma tarefa de restauro adicionais a **tarefas** página. |Este comportamento pode ocorrer se a tarefa de restauro tem apenas localmente afixado volumes ou uma combinação de volumes localmente afixados e em camadas. Se a tarefa de restauro inclui apenas os volumes em camadas, em seguida, este comportamento não ocorrerá. Não é necessária nenhuma intervenção do utilizador. |Sim |Não |
| 20 |Volumes localmente afixados |Se tentar converter um volume em camadas (criado e clonado com atualização 1.2 ou anterior) para um volume localmente afixado e o dispositivo está a ficar sem espaço ou existe uma falha de nuvem, em seguida, o clone(s) pode estar danificado. |Este problema ocorre apenas com volumes que foram software criada e clonado com anterior à atualização 2.1. Deve ser um cenário de pouco frequente. | | |
| 21 |Conversão de volume |Não atualizar ACRs ligados a um volume, enquanto uma conversão do volume está em curso (camadas para afixado localmente ou vice-versa). Atualizar os ACRs pode resultar em danos nos dados. |Se for necessário, atualize ACRs antes da conversão de volume e não efetuar mais ACR atualizações com a conversão em curso. | | |

## <a name="controller-and-firmware-updates-in-update-22"></a>Controlador e atualizações de firmware na atualização 2.2
Esta versão inclui atualizações apenas de software. No entanto, se estão a atualizar a partir de uma versão antes da atualização 2, terá de instalar o controlador Storport, Spaceport e (em alguns casos) atualizações de firmware no seu dispositivo de disco.

Para obter mais informações sobre como instalar o controlador, Storport, Spaceport e atualizações de firmware do disco, consulte [instalar a atualização 2.2](storsimple-install-update-21.md) no dispositivo StorSimple.

## <a name="virtual-device-updates-in-update-22"></a>Atualizações ao dispositivo virtual na atualização 2.2
Esta atualização não pode ser aplicada ao dispositivo virtual. Novos dispositivos virtuais terão de ser criado. 

## <a name="next-step"></a>Passo seguinte
Saiba como [instalar a atualização 2.2](storsimple-install-update-21.md) no dispositivo StorSimple.

