---
title: "Matriz Virtual StorSimple desastre dispositivo e de recuperação de ativação pós-falha | Microsoft Docs"
description: "Saiba mais sobre como a ativação pós-falha a matriz de Virtual StorSimple."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 3c1f9c62-af57-4634-a0d8-435522d969aa
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 12079f8dbc409afe5acc274fa08bda878c90b76e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>Ativação pós-falha de dispositivo e de recuperação após desastre para a matriz de Virtual StorSimple através do portal do Azure

## <a name="overview"></a>Descrição geral
Este artigo descreve a recuperação de desastre para o Microsoft Azure StorSimple Virtual matriz, incluindo os passos detalhados para a ativação pós-falha para outro matriz virtual. Uma ativação pós-falha permite-lhe mover os dados a partir de um *origem* dispositivo no datacenter para um *destino* dispositivo. O dispositivo de destino pode estar localizado no mesmo ou outra localização geográfica. A ativação pós-falha de dispositivo destina-se a todo o dispositivo. Durante a ativação pós-falha, os dados de nuvem para o dispositivo de origem alterações de propriedade para que o dispositivo de destino.

Este artigo só é aplicável às matrizes de Virtual StorSimple. Para efetuar a ativação pós-falha de um dispositivo da 8000 série, aceda a [dispositivo ativação pós-falha e recuperação após desastre do dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md).

## <a name="what-is-disaster-recovery-and-device-failover"></a>O que é a ativação pós-falha de dispositivo e de recuperação de desastre?

Num cenário de recuperação (DR) após desastre, o dispositivo primário deixa de funcionar. Neste cenário, pode mover os dados na nuvem associados ao dispositivo falhou ao outro dispositivo. Pode utilizar o dispositivo primário como a *origem* e especifique outro dispositivo, como o *destino*. Este processo é denominado o *ativação pós-falha*. Durante a ativação pós-falha, todos os volumes ou partilhas do dispositivo de origem, alterar a propriedade e são transferidas para o dispositivo de destino. Nenhuma filtragem dos dados é permitida.

DR é modelada como um restauro completa de dispositivo utilizando o térmico baseado no mapa criação de camadas e de controlo. Um mapa térmico é definido ao atribuir um valor de térmico para os dados com base na leitura e escrita padrões. Este térmico do mapa, em seguida, camadas os segmentos de dados térmico mais baixos para a nuvem pela primeira vez, mantendo os segmentos de dados térmico elevada (mais utilizado) no escalão local. Durante uma DR, StorSimple utiliza o mapa térmico restaurar e rehydrate os dados da nuvem. O dispositivo obtém todos os volumes/partilhas de na última cópia de segurança recente (conforme determinado internamente) e efetua um restauro dessa cópia de segurança. A matriz virtual orquestra todo o processo de DR.

> [!IMPORTANT]
> O dispositivo de origem é eliminado no fim da ativação pós-falha do dispositivo e, por conseguinte, não é suportada uma reativação pós-falha.
> 
> 

Recuperação após desastre é orquestrada através da funcionalidade de ativação pós-falha do dispositivo e é iniciada a partir de **dispositivos** painel. Este painel tabulates todos os dispositivos StorSimple ligados ao seu serviço do Gestor de dispositivos do StorSimple. Para cada dispositivo, pode ver o nome amigável, estado, aprovisionada e máxima de capacidade, tipo e modelo.

## <a name="prerequisites-for-device-failover"></a>Pré-requisitos para ativação pós-falha do dispositivo

### <a name="prerequisites"></a>Pré-requisitos

Para um dispositivo de ativação pós-falha, certifique-se de que são cumpridos os seguintes pré-requisitos:

* O dispositivo de origem tem de ser um **desativado** estado.
* O dispositivo de destino tem de aparecer como **preparado para configurar** no portal do Azure. Aprovisione uma matriz de virtual de destino da capacidade igual ou superior. Utilize a IU da web local para configurar e registar com êxito a matriz de virtual de destino.
  
  > [!IMPORTANT]
  > Não tente configurar o dispositivo virtual registado através do serviço. Nenhuma configuração de dispositivo deve ser efetuada através do serviço.
  > 
  > 
* O dispositivo de destino não pode ter o mesmo nome que o dispositivo de origem.
* O dispositivo de origem e de destino tem de ser do mesmo tipo. Só pode efetuar a ativação pós-falha de uma matriz virtual configurada como um servidor de ficheiros para outro servidor de ficheiros. O mesmo se aplica-se um servidor de iSCSI.
* Para um servidor de ficheiros de DR, recomendamos que associar o dispositivo de destino ao mesmo domínio de origem. Esta configuração assegura que as permissões de partilha são automaticamente resolvidas. Apenas a ativação pós-falha para um dispositivo de destino no mesmo domínio.
* Os dispositivos de destino disponíveis para DR são dispositivos que tenham a mesma capacidade ou capacidade superior em comparação com o dispositivo de origem. Os dispositivos que estão ligados ao seu serviço, mas que não cumprem os critérios de espaço suficiente não estão disponíveis como dispositivos de destino.

### <a name="other-considerations"></a>Outras considerações

* Para uma ativação pós-falha planeada 
  
  * Recomendamos que colocar todos os volumes ou partilhas no dispositivo de origem offline.
  * Recomendamos que efetuar uma cópia de segurança do dispositivo e, em seguida, prossiga com a ativação pós-falha para minimizar a perda de dados. 
* Para uma ativação pós-falha não planeada, o dispositivo utilizar a cópia de segurança mais recente para restaurar os dados.

### <a name="device-failover-prechecks"></a>Prechecks de ativação pós-falha do dispositivo

Antes de começa a DR, o dispositivo efetua prechecks. Estas verificações garantir que não ocorrerem erros quando começa a DR. Os prechecks incluem:

* A validar a conta de armazenamento.
* A verificar a conectividade da nuvem para o Azure.
* A verificar o espaço disponível no dispositivo de destino.
* A verificar se tem um volume de dispositivo de origem de servidor de iSCSI
  
  * nomes válidos ACR.
  * IQN válido (que não exceda 220 carateres).
  * válido CHAP palavras-passe (12-16 carateres).

Se qualquer um dos precedente prechecks falhar, não é possível prosseguir com a DR. Resolva os problemas e, em seguida, repita DR.

A DR esteja concluída com êxito, a propriedade dos dados em nuvem no dispositivo de origem é transferida para o dispositivo de destino. Em seguida, o dispositivo de origem já não está disponível no portal. Acesso a todas as volumes/partilhas no dispositivo de origem é bloqueado e o dispositivo de destino fica ativo.

> [!IMPORTANT]
> Apesar do dispositivo já não estiver disponível, a máquina virtual que aprovisionado no sistema anfitrião ainda está a consumir recursos. Assim que a DR estiver concluída com êxito, pode eliminar esta máquina virtual do sistema anfitrião.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Ativação pós-falha para uma matriz de virtual

Recomendamos que aprovisionar, configurar e registar noutra matriz de Virtual StorSimple com o serviço do Gestor de dispositivos do StorSimple antes de executar este procedimento.

> [!IMPORTANT]
> 
> * Não é possível ativação pós-falha de um dispositivo de série 8000 do StorSimple para um dispositivo virtual 1200.
> * Pode falhar através de um dispositivo virtual Federal Information Processing Standard (FIPS) ativada para outro dispositivo FIPS ativada ou para um dispositivo não FIPS implementadas no portal de administração pública.


Execute os seguintes passos para restaurar o dispositivo para um destino do dispositivo virtual StorSimple.

1. Aprovisiona e configura um dispositivo de destino que cumpra o [pré-requisitos para ativação pós-falha do dispositivo](#prerequisites). Conclua a configuração de dispositivos através da IU da web local e registá-lo ao seu serviço do Gestor de dispositivos do StorSimple. Se criar um servidor de ficheiros, vá para o passo 1 [configurado como servidor de ficheiros](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device). Se criar um servidor de iSCSI, vá para o passo 1 [configurado como servidor de iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device).

2. Colocar os volumes/partilhas offline no anfitrião. Para colocar as volumes/partilhas offline, consulte as instruções de sistema operativo específicas para o anfitrião. Se não já offline, terá de efetuar todas as volumes/partilhas offline no dispositivo da seguinte forma.
   
    1. Aceda a **dispositivos** painel e selecione o seu dispositivo.
   
    2. Aceda a **definições > Gerir > partilhas** (ou **definições > Gerir > Volumes**). 
   
    3. Selecione um partilha/volume, clique com o botão direito e selecione **colocar offline**. 
   
    4. Quando lhe for pedido para confirmação, verifique **compreendo o impacto de colocar offline nesta partilha.** 
   
    5. Clique em **colocar offline**.

3. No seu serviço do Gestor de dispositivos do StorSimple, aceda a **gestão > dispositivos**. No **dispositivos** painel, selecione e clique em seu dispositivo de origem.

4. No seu **dashboard dispositivo** painel, clique em **desativar**.

5. No **desativar** painel, lhe for pedida a confirmação. A desativação de dispositivo é um *permanente* processo não pode ser anulado. Também são reminded para tirar os partilhas/volumes offline no anfitrião. Escreva o nome de dispositivo para confirmar e clique em **desativar**.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. A desativação é iniciado. Receberá uma notificação a desativação esteja concluída com êxito.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. Na página de dispositivos, o estado do dispositivo será agora alterado para **desativado**.
    ![](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. No **dispositivos** painel, selecione e clique no dispositivo de origem desativado para ativação pós-falha. 
9. No **dashboard dispositivo** painel, clique em **falhar**. 
10. No **efetuar a ativação pós-falha do dispositivo** painel, efetue o seguinte procedimento:
    
    1. O campo de dispositivo de origem é preenchido automaticamente. Tenha em atenção o tamanho total dos dados para o dispositivo de origem. O tamanho de dados deve ser inferior à capacidade disponível no dispositivo de destino. Reveja os detalhes associados ao dispositivo de origem, tais como o nome de dispositivo, a capacidade total e os nomes das partilhas que estão a ativação pós-falha.

    2. Na lista pendente de dispositivos disponíveis, escolha um **dispositivo-alvo**. Apenas os dispositivos que tem capacidade suficiente são apresentados na lista pendente.

    3. Verifique se **compreendo que esta operação irá efetuar a ativação pós-falha de dados para o dispositivo de destino**. 

    4. Clique em **falhar**.
    
        ![](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. Inicia uma tarefa de ativação pós-falha e receber uma notificação. Aceda a **dispositivos > tarefas** para monitorizar a ativação pós-falha.
    
     ![](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. No **tarefas** painel, verá uma tarefa de ativação pós-falha criada para o dispositivo de origem. Esta tarefa efetua as prechecks DR.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     Depois dos prechecks DR são bem sucedidas, a tarefa de ativação pós-falha irão gerar as tarefas de restauro para cada partilha/volume que exista no seu dispositivo de origem.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. Depois de concluída a ativação pós-falha, vá para o **dispositivos** painel.
    
    1. Selecione e clique no dispositivo StorSimple que foi utilizado como o dispositivo de destino para o processo de ativação pós-falha.
    2. Aceda a **definições > gestão > partilhas** (ou **Volumes** se o servidor de iSCSI). No **partilhas** painel, pode ver todas as partilhas de (volumes) do dispositivo antigo.
        ![](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. Terá [criar um alias de DNS](https://support.microsoft.com/kb/168322) para que todas as aplicações que estão a tentar ligar, podem obter redirecionadas para o novo dispositivo.

## <a name="errors-during-dr"></a>Erros durante a DR

**Falha de conectividade de nuvem durante a DR**

Se a conectividade de nuvem é interrompida DR é iniciado e antes do dispositivo restauro está concluído, a DR irá falhar. Receberá uma notificação de failore. O dispositivo de destino para DR está marcado como *não utilizável.* Não é possível utilizar o mesmo dispositivo de destino para o DRs futuras.

**Nenhum dispositivo de destino compatível**

Se os dispositivos de destino disponíveis tem espaço suficiente, verá um erro para o efeito que não existem nenhum dispositivo de destino compatível.

**Precheck falhas**

Se um dos prechecks não é satisfeito, em seguida, verá precheck falhas.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Recuperação de desastre de continuidade de negócio (BCDR)

Um cenário de recuperação (BCDR) de desastre de continuidade do negócio ocorre quando o todo o datacenter do Azure deixa de funcionar. Isto pode afetar o seu serviço do Gestor de dispositivos do StorSimple e os dispositivos associados do StorSimple.

Se existirem dispositivos StorSimple que foram registados antes de um desastre ocorreu, em seguida, estes dispositivos StorSimple poderão ter de ser eliminado. Após desastre, pode recriá-lo e configurar esses dispositivos.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [administrar a matriz de Virtual StorSimple, utilizando a IU da web local](storsimple-ova-web-ui-admin.md).

