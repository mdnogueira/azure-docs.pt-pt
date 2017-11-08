---
title: "Notas de versão de atualização 2 do StorSimple 8000 série | Microsoft Docs"
description: "Descreve as novas funcionalidades, problemas e soluções para atualização 2 do StorSimple 8000 série."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: e2c8bffd-7fc5-4b77-b632-a4f59edacc3a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 285c5abf574544737f3d30981a6c5b8f9548922a
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="storsimple-8000-series-update-2-release-notes"></a>Notas de versão de atualização 2 do StorSimple 8000 série
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Descrição geral
As notas de versão seguintes descrevem as novas funcionalidades e identificam os problemas abertos críticos para a atualização 2 do StorSimple 8000 série. Também contêm uma lista de software, controlador e atualizações de firmware de disco incluídas nesta versão do StorSimple. 

A atualização 2 pode ser aplicada a qualquer dispositivo StorSimple com o lançamento (GA) ou atualização 0.1 através da atualização 1.2. A versão de dispositivo associada a atualização 2 é 6.3.9600.17673.

Reveja as informações contidas nas notas de versão antes de implementar a atualização na sua solução StorSimple.

> [!IMPORTANT]
> * Demora cerca de 4 a 7 horas para instalar esta atualização (incluindo as atualizações do Windows). 
> * A atualização 2 tem o software, os controladores de LSI e atualizações de firmware SSD.
> * Para os novos lançamentos, poderá não ver atualizações imediatamente porque fazemos de uma implementação faseada de atualizações. Aguarde alguns dias e, em seguida, análise de atualizações novamente como estes deixará de estar disponível em breve.
> 
> 

## <a name="whats-new-in-update-2"></a>Novidades na atualização 2
A atualização 2 apresenta as seguintes novas funcionalidades.

* **Afixado localmente volumes** – em versões anteriores da série 8000 do StorSimple, blocos de dados foram camados para a nuvem com base na utilização. Não ocorreu nenhuma forma de garantir que os blocos seriam permanecem no local. Na atualização 2, quando criar um volume, pode designar um volume como afixados localmente e primários dados nesse volume não irão ser colocado em camadas para a nuvem. Instantâneos de volumes localmente afixados ainda serão copiados para a nuvem para a cópia de segurança para que a nuvem pode ser utilizada para fins de recuperação após desastre e mobilidade dados. Além disso, pode alterar o tipo de volume (ou seja, converter em volumes de volumes localmente afixados camadas e converter afixado localmente volumes em camadas). 
* **Melhoramentos de dispositivo virtual StorSimple** – anteriormente, a série 8000 do StorSimple posicionado o dispositivo virtual como uma solução de desenvolvimento/teste ou de recuperação após desastre. Ocorreu só um modelo do dispositivo virtual (modelo 1100). A atualização 2 apresenta dois modelos de dispositivo virtual: 
  
  * 8010 (anteriormente denominadas 1100) – sem alteração; tem uma capacidade de 30 TB e utiliza o armazenamento padrão do Azure.
  * 8020 – tem uma capacidade de 64 TB e utiliza o armazenamento Premium do Azure para um melhor desempenho.
    
    Não há um único VHD para ambos os modelos do dispositivo virtual (8010/8020). Quando iniciar pela primeira vez o dispositivo virtual, Deteta os parâmetros de plataforma e aplica-se a versão do modelo correto.
* **Melhorias de funcionamento em rede** – Update 2 contém as seguintes melhorias de funcionamento em rede:
  
  * Vários NICs podem ser ativados para a nuvem, para que a ativação pós-falha pode ocorrer se um NIC falhar.
  * Melhorias de encaminhamento, com a métrica fixa para a nuvem ativado blocos.
  * Repita online de recursos com falhas antes de uma ativação pós-falha.
  * Novos alertas para falhas do serviço.
* **Atualizar melhoramentos** – atualizar 1.2 e anterior, a série 8000 do StorSimple foi atualizada através de dois canais: Windows Update para clustering, iSCSI e assim sucessivamente e o Microsoft Update para que os binários e firmware.
    A atualização 2 utiliza o Microsoft Update para todos os pacotes de atualização. Isto deve levar a menos tempo a aplicação de patches ou fazer as ativações pós-falha. 
* **Atualizações de firmware** – o firmware os seguintes estão incluídas atualizações:
  
  * LSI: lsi_sas2.sys versão de produto 2.00.72.10
  * SSD apenas (não existem atualizações HDD): XMGG, XGEG, KZ50, F6C2 e VR08
* **Suporte proativa** – Update 2 permite Microsoft obter informações de diagnóstico adicionais do dispositivo. Quando a nossa equipa de operações identifica dispositivos que estão a ter problemas, estamos melhor equipped recolher informações do dispositivo e diagnosticar problemas. **Ao aceitar atualização 2, nos permite fornecer este suporte proativa**.    

## <a name="issues-fixed-in-update-2"></a>Problemas fixos na atualização 2
As tabelas seguintes fornece um resumo dos problemas que foram corrigidos no 2 de atualizações.    

| Não. | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Interfaces de rede |Após uma atualização para atualização 1, o serviço StorSimple Manager reportou que as portas Data2 e Data3 falhou no controlador de um. Este problema foi corrigido. |Sim |Não |
| 2 |Atualizações |Após uma atualização para atualização 1, os alertas de alarme audible ocorreram no portal clássico do Azure em vários dispositivos. Este problema foi corrigido. |Sim |Não |
| 3 |Autenticação Openstack |Ao utilizar Openstack como o fornecedor de serviço em nuvem, pode receber um erro que a cadeia de autenticação em nuvem era demasiado longa. Esta correção. |Sim |Não |

## <a name="known-issues-in-update-2"></a>Problemas conhecidos na atualização 2
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
| 20 |Volumes localmente afixados |Se tentar converter um volume em camadas (criado e clonado com atualização 1.2 ou anterior) para um volume localmente afixado e o dispositivo está a ficar sem espaço ou existe uma falha de nuvem, em seguida, o clone(s) pode estar danificado. |Este problema ocorre apenas com volumes que foram software criada e clonado com anterior à atualização 2. Deve ser um cenário de pouco frequente. | | |
| 21 |Conversão de volume |Não atualizar ACRs ligados a um volume, enquanto uma conversão do volume está em curso (camadas para afixado localmente ou vice-versa). Atualizar os ACRs pode resultar em danos nos dados. |Se for necessário, atualize ACRs antes da conversão de volume e não efetuar mais ACR atualizações com a conversão em curso. | | |

## <a name="controller-and-firmware-updates-in-update-2"></a>Controlador e atualizações de firmware na atualização 2
Esta versão de atualizações de controladores e firmware do disco no seu dispositivo.

* Para obter mais informações sobre o firmware de LSI de atualização, consulte o artigo da base de dados de Conhecimento Microsoft 3121900. 
* Para obter mais informações sobre o firmware do disco de atualização, consulte o artigo da base de dados de Conhecimento Microsoft 3121899.

## <a name="virtual-device-updates-in-update-2"></a>Atualizações ao dispositivo virtual na atualização 2
Esta atualização não pode ser aplicada ao dispositivo virtual. Novos dispositivos virtuais terão de ser criado. 

## <a name="next-step"></a>Passo seguinte
Saiba como [instalar a atualização 2](storsimple-install-update-2.md) no dispositivo StorSimple.

