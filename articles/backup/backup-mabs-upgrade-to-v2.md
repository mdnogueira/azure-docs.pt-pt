---
title: "Instalar o servidor de cópia de segurança do Azure v2 | Microsoft Docs"
description: "V2 de servidor do Backup do Azure dá-lhe capacidades de cópia de segurança avançadas para proteger as VMs, ficheiros e pastas, cargas de trabalho e muito mais. Saiba como instalar ou atualizar para o servidor de cópia de segurança do Azure v2."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: masaran;markgal
ms.openlocfilehash: 1bbb16afef7940933b4c3ae23873f212770137e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="install-azure-backup-server-v2"></a>Instalar o servidor de cópia de segurança do Azure v2

Servidor de cópia de segurança do Azure ajuda a proteger as máquinas virtuais (VMs), cargas de trabalho, ficheiros e pastas e muito mais. V2 de servidor do Backup do Azure baseia-se no servidor de cópia de segurança do Azure v1 e dá-lhe novas funcionalidades que não estão disponíveis no v1. Para ver uma comparação de funcionalidades entre v1 e v2, consulte [matriz de proteção do servidor de cópia de segurança do Azure](backup-mabs-protection-matrix.md). 

As funcionalidades adicionais no servidor de cópia de segurança v2 são uma atualização do servidor de cópia de segurança v1. No entanto, v1 do servidor de cópia de segurança não é um pré-requisito para a instalação do servidor de cópia de segurança v2. Se pretender atualizar do v1 do servidor de cópia de segurança para o servidor de cópia de segurança v2, instale o servidor de cópia de segurança v2 no servidor de proteção do servidor de cópia de segurança. As definições de servidor de cópia de segurança existentes permanecem intactas.

Pode instalar o servidor de cópia de segurança v2 no Windows Server 2012 R2 ou Windows Server 2016. Para tirar partido das novas funcionalidades, como o System Center 2016 proteção Manager moderna cópia de segurança do armazenamento de dados, tem de instalar o servidor de cópia de segurança v2 no Windows Server 2016. Antes de atualizar para ou instalar o servidor de cópia de segurança v2, leia sobre o [os pré-requisitos da instalação](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites).

> [!NOTE]
> Servidor de cópia de segurança do Azure tem o mesmo código base como o System Center Data Protection Manager. V1 cópia de segurança do servidor é equivalente ao Data Protection Manager 2012 R2 e o servidor de cópia de segurança v2 é equivalente ao Data Protection Manager 2016. Este artigo ocasionalmente referencia a documentação do Data Protection Manager.
>
>

## <a name="upgrade-backup-server-to-v2"></a>Atualizar o servidor de cópia de segurança para v2
Para atualizar v1 do servidor de cópia de segurança para o servidor de cópia de segurança v2, certifique-se que a instalação tem as atualizações necessárias:

- [Atualizar os agentes de proteção](backup-mabs-upgrade-to-v2.md#update-the-dpm-protection-agent) nos servidores protegidos.
- Atualize o Windows Server 2012 R2 para o Windows Server 2016.
- Atualize o administrador de remota do servidor de cópia de segurança do Azure em todos os servidores de produção.
- Certifique-se de que as cópias de segurança estão definidas para continuar sem reiniciar o servidor de produção.


### <a name="upgrade-steps-for-backup-server-v2"></a>Passos de atualização para o servidor de cópia de segurança v2

1. No Centro de transferências, [transferir o instalador atualização](https://go.microsoft.com/fwlink/?LinkId=626082).

2. Depois de extrair o Assistente de configuração, certifique-se de que **executar setup.exe** está selecionado e, em seguida, selecione **concluir**.

  ![Programa de instalação do programa de configuração - configuração de execução](./media/backup-mabs-upgrade-to-v2/run-setup.png)

3. No Assistente do servidor de cópia de segurança do Microsoft Azure, em **instalar**, selecione **servidor de cópia de segurança do Microsoft Azure**.

  ![Programa de instalação do programa de configuração - selecione instalação](./media/backup-mabs-upgrade-to-v2/mabs-installer-s1.png)

4. No **boas-vindas** página, consulte os avisos e, em seguida, selecione **seguinte**.

  ![Programa de instalação do programa de configuração - página boas-vindas](./media/backup-mabs-upgrade-to-v2/mabs-installer-s2.png)

5. O Assistente de configuração executa verificações de pré-requisitos para se certificar de que pode atualizar o seu ambiente. No **verificações de pré-requisitos** página, selecione **verifique**.

  ![Programa de instalação do programa de configuração - página verificações de pré-requisitos](./media/backup-mabs-upgrade-to-v2/mabs-installer-s3-perform-checks.png)

6. O ambiente tem de passar as verificações de pré-requisitos. Se o ambiente não obtiver as verificações, tenha em atenção os problemas e corrija-os. Em seguida, selecione **verifique novamente**. Depois de passar as verificações de pré-requisitos, selecione **seguinte**.

  ![Instalador - botão verificar novamente o programa de configuração](./media/backup-mabs-upgrade-to-v2/mabs-installer-s4-pass-checks.png)

7. No **as definições de SQL** página, selecione a opção relevante para a sua instalação do SQL Server e, em seguida, selecione **verificar e instalar**.

  ![Programa de instalação do programa de configuração - página de definições do SQL Server](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5-sql-settings.png)

  As verificações de poderão demorar alguns minutos. Quando as verificações de terminar, selecione **seguinte**.

  ![Programa de instalação - Verifique as definições de SQL no botão e instalar o programa de configuração](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5a-check-and fix-settings.png)

8. No **definições de instalação** página, efetue as alterações para a localização onde o servidor de cópia de segurança está instalado ou para a localização de rascunho. Selecione **seguinte**.

  ![Programa de instalação do programa de configuração - página de definições de instalação](./media/backup-mabs-upgrade-to-v2/mabs-installer-s6-installation-settings.png)

9. Para concluir o Assistente de configuração, selecione **concluir**.

  ![Programa de instalação do programa de configuração - concluir](./media/backup-mabs-upgrade-to-v2/run-setup.png)



## <a name="add-storage-for-modern-backup-storage"></a>Adicionar armazenamento para o armazenamento de cópia de segurança moderna

Para melhorar a eficiência do armazenamento de cópia de segurança, o servidor de cópia de segurança v2 adiciona suporte para volumes. Como o servidor de cópia de segurança v1, v2 do servidor de cópia de segurança suporta discos.

### <a name="add-volumes-and-disks"></a>Adicionar a volumes e discos
Se executar o servidor de cópia de segurança v2 no Windows Server 2016, pode utilizar os volumes para armazenar dados de cópia de segurança. Volumes oferecem reduções de armazenamento e as cópias de segurança mais rápidas. Porque volumes estiverem familiarizados com o servidor de cópia de segurança, tem de adicioná-los. 

Quando adiciona um volume ao servidor de cópia de segurança, pode dar o volume de um nome amigável. Clique em de **nome amigável** coluna do volume que pretende nome. Pode alterar o nome posteriormente, se necessário. Também pode utilizar do PowerShell para adicionar ou alterar os nomes amigáveis para volumes.

Para adicionar um volume na consola do administrador:

1. Na consola de administrador do servidor de cópia de segurança do Azure, selecione **gestão** > **armazenamento em disco** > **adicionar**.

    ![Abra o Assistente para adicionar armazenamento de disco](./media//backup-mabs-upgrade-to-v2/open-add-disk-storage-wizard.png)

    Esta ação abre o Assistente para adicionar armazenamento de disco.

2. No **adicionar armazenamento em disco** na página de **volumes disponíveis** , selecione um volume e, em seguida, selecione **adicionar**.
3. No **selecionado volumes** , introduza um nome amigável para o volume e, em seguida, selecione **OK**.

      ![Adicionar Assistente de armazenamento em disco - adicionar volume](./media/backup-mabs-upgrade-to-v2/add-volume.png)

  Se pretender adicionar um disco, o disco tem de pertencer a um grupo de proteção que tem um armazenamento de legado. Estes discos só podem ser utilizados para estes grupos de proteção. Se o servidor de cópia de segurança não tem origens que têm proteção legada, o disco não está listado.

  Para obter mais informações sobre a adição de discos, consulte [adição de discos para aumentar o armazenamento legado](http://docs.microsoft.com/system-center/dpm/upgrade-to-dpm-2016#adding-disks-to-increase-legacy-storage). Não é possível atribuir um disco um nome amigável.


### <a name="assign-workloads-to-volumes"></a>Atribuir as cargas de trabalho para volumes

No servidor de cópia de segurança, especifique as cargas de trabalho são atribuídas para os volumes. Por exemplo, pode definir volumes dispendiosas que suportam um número elevado de operações de entrada/saída por segundo (IOPS) para armazenar apenas as cargas de trabalho que necessitam de cópias de segurança frequentes, elevado volume. Um exemplo é o SQL Server com registos de transações.

#### <a name="update-dpmdiskstorage"></a>Atualização DPMDiskStorage

Para atualizar as propriedades de um volume no agrupamento de armazenamento no servidor de cópia de segurança, utilize o cmdlet do PowerShell DPMDiskStorage de atualização.

Sintaxe:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

Todas as alterações efetuadas utilizando o PowerShell são refletidas na IU.


## <a name="protect-data-sources"></a>Proteger origens de dados
Para começar a proteger as origens de dados, crie um grupo de proteção. Os seguintes passos realce as alterações ou adições ao Assistente de novo grupo de proteção.

Para criar um grupo de proteção:

1. Na consola do administrador do servidor de cópia de segurança, selecione **proteção**.

2. No Friso, selecione **novo**.

    Esta ação abre o Assistente Criar novo grupo de proteção.

  ![Assistente Criar novo grupo de proteção](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-1.png)

3. No **boas-vindas** página, selecione **seguinte**.
4. No **selecionar tipo de grupo de proteção** página, selecione o tipo de grupo de proteção que pretende criar e, em seguida, selecione **seguinte**.

  ![Página do tipo de selecionar o grupo de proteção](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-2.png)

5. No **selecionar membros do grupo** na página de **membros disponíveis** painel, os membros com proteção agentes estão listados. Para este exemplo, selecione o volume d:\. e e:\<pontodemontagem> e adicioná-los para o **selecionado membros** painel. Selecione **seguinte**.

  ![Página de membros do grupo de selecione](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-3.png)

6. No **selecionar método de proteção de dados** página, introduza um **nome do grupo de proteção**, selecione o método de proteção e, em seguida, selecione **seguinte**. Se pretender que a proteção de curta duração, tem de selecionar o **disco** método de cópia de segurança.

  ![Selecione a página de método de proteção de dados](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-4.png)

7. No **especificar objetivos a curto prazo** página, selecione os detalhes para **período de retenção** e **frequência de sincronização**. Em seguida, selecione **seguinte**. Opcionalmente, para alterar a agenda para quando são criados os pontos de recuperação, selecione **modificar**.

  ![Especifique a página de objetivos a curto prazo](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-5.png)

8. No **rever alocação de armazenamento de disco** página, rever detalhes sobre as origens de dados selecionada, a respetiva tamanho e os valores para o espaço a ser aprovisionado e o volume de armazenamento de destino.

  ![Página de alocação de armazenamento de disco de revisão](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-6.png)

  Volumes de armazenamento são baseadas na alocação de volume da carga de trabalho (definido utilizando o PowerShell) e o armazenamento disponível. Pode alterar os volumes de armazenamento selecionando outros volumes no menu pendente. Se alterar o valor para **armazenamento de destino**, o valor para **armazenamento em disco disponível** dinamicamente as alterações para refletir os valores em **espaço livre** e  **Underprovisioned espaço**.

  Se as origens de dados aumentam conforme ativação pós-falha planeada, o valor para o **Underprovisioned espaço** coluna na **armazenamento em disco disponível** reflete a quantidade de armazenamento adicional que precisa. Utilize este valor para o ajudar a planear as suas necessidades de armazenamento para cópias de segurança uniforme. Não se o valor é zero, existem nenhum potenciais problemas de armazenamento no futuro previsível. Se o valor for um número diferente de zero, não dispõe de armazenamento suficiente atribuído (com base na sua política de proteção e o tamanho dos dados dos seus membros protegidos).

  ![Armazenamento de disco underallocated](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-7.png)

   Para concluir a criação do seu grupo de proteção, conclua o assistente.

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrar o armazenamento legado para armazenamento de cópia de segurança moderna
Depois de atualizar ou instalar o servidor de cópia de segurança v2 e atualizar o sistema operativo para o Windows Server 2016, atualize os grupos de proteção para utilizar o armazenamento de cópia de segurança moderna. Por predefinição, os grupos de proteção não são alterados. Podem continuar a funcionar como foram inicialmente configuraram. 

Atualizar os grupos de proteção para utilizar o armazenamento de cópia de segurança modernas é opcional. Para atualizar o grupo de proteção, interrompa a proteção de todas as origens de dados utilizando a opção de dados de manter. Em seguida, adicione as origens de dados para um novo grupo de proteção.

1. Na consola do administrador, selecione o **proteção** funcionalidade. No **membro do grupo de proteção** lista, o membro com o botão direito e, em seguida, selecione **interromper a proteção dos membros**.

  ![Interrompa a proteção do membro](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. No **remover do grupo** diálogo caixa, reveja o espaço em disco utilizado e o espaço livre disponível para o agrupamento de armazenamento. A predefinição é manter os pontos de recuperação no disco e permitir-lhes a expirar por sua política de retenção associado. Clique em **OK**.

  Se pretender regressar imediatamente o espaço em disco utilizado para o agrupamento de armazenamento livre, selecione o **Eliminar réplica no disco** caixa de verificação para eliminar os dados de cópia de segurança (e os pontos de recuperação) associados a esse membro.

  ![Remover da caixa de diálogo de grupo](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Crie um grupo de proteção que utiliza o armazenamento de cópia de segurança moderna. Inclua as origens de dados desprotegida.


## <a name="add-disks-to-increase-legacy-storage"></a>Adicionar discos para aumentar o armazenamento legado

Se pretender utilizar o armazenamento legado com o servidor de cópia de segurança, poderá ter de adicionar discos para aumentar o armazenamento de legado. 

Para adicionar armazenamento em disco:

1. Na consola do administrador, selecione **gestão** > **armazenamento em disco** > **adicionar**.

    ![Adicionar caixa de diálogo de armazenamento em disco](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

4. No **adicionar armazenamento em disco** caixa de diálogo, selecione **adicionar discos**.

5. Na lista de discos disponíveis, selecione os discos que pretende adicionar, selecione **adicionar**e, em seguida, selecione **OK**.

## <a name="update-the-data-protection-manager-protection-agent"></a>Atualizar o agente de proteção do Data Protection Manager

Servidor de cópia de segurança utiliza o agente de proteção do System Center Data Protection Manager para atualizações. Se estiver a atualizar um agente de proteção que não está ligado à rede, não é possível utilizar a consola de administrador do Gestor de proteção de dados para concluir uma atualização do agente ligado. Tem de atualizar o agente de proteção num ambiente de domínio nonactive. Até que o computador cliente está ligado à rede, a consola do administrador do Data Protection Manager mostra que a atualização do agente de proteção está pendente.

As secções seguintes descrevem como atualizar agentes de proteção para computadores cliente que estão ligados e computadores cliente que não estão ligados.

### <a name="update-a-protection-agent-for-a-connected-client-computer"></a>Atualizar um agente de proteção para um computador cliente ligado

1. Na consola do administrador do servidor de cópia de segurança, selecione **gestão** > **agentes**.

2. No painel de visualização, selecione os computadores de cliente para o qual pretende atualizar o agente de proteção.

  > [!NOTE]
  > O **as atualizações de agente** coluna indica quando uma atualização do agente de proteção está disponível para cada computador protegido. No **ações** painel, o **atualização** ação está disponível apenas quando um computador protegido está selecionado e as atualizações estiverem disponíveis.
  >
  >

3. Para instalar agentes de proteção atualizados em computadores selecionados, o **ações** painel, selecione **atualização**.

### <a name="update-a-protection-agent-on-a-client-computer-that-is-not-connected"></a>Atualizar um agente de proteção num computador cliente que não está ligado

1. Na consola do administrador do servidor de cópia de segurança, selecione **gestão** > **agentes**.

2. No painel de visualização, selecione os computadores de cliente para o qual pretende atualizar o agente de proteção.

  > [!NOTE]
   > O **as atualizações de agente** coluna indica quando uma atualização do agente de proteção está disponível para cada computador protegido. No **ações** painel, o **atualização** ação não está disponível quando um computador protegido é selecionado, a menos que as atualizações estiverem disponíveis.
  >
  >

3. Para instalar agentes de proteção atualizados em computadores selecionados, selecione **atualização**.

4. Para um computador cliente que não está ligado à rede, até que o computador está ligado à rede, o **estado do agente** coluna mostra um Estado de **atualização pendente**.

  Depois de um computador cliente está ligado à rede, o **as atualizações de agente** coluna para o computador cliente mostra um Estado de **atualização**.
  
### <a name="move-legacy-protection-groups-from-old-version-and-sync-the-new-version-with-azure"></a>Mover grupos de proteção legados da versão antiga e sincronizar a nova versão com o Azure

Assim que o servidor de cópia de segurança do Azure e o SO é actualizado, está pronto para proteger as origens de dados de novo utilizando o armazenamento de cópia de segurança moderna. No entanto já protegidos origens de dados irão continuar a ser protegido da forma legado, tal como estavam no servidor de cópia de segurança do Azure, mas todas as nova proteção utilizará moderna armazenamento de cópia de segurança.

Passos abaixo está a migrar origens de dados de modo legado de proteção para o armazenamento de cópia de segurança Moderno.

• Adicione o novo volumes ao agrupamento de armazenamento do DPM e atribuir etiquetas de origem de dados e os nomes amigáveis, se assim o desejar.
• Para cada origem de dados que está no modo legado, pare a proteção das origens de dados e "Manter dados protegidos".  Isto permitirá recuperação antigo dos pontos de recuperação após a migração.

• Criar um novo grupo de PROTEÇÃO e selecione as origens de dados que estão a ser armazenado utilizando o novo formato.
• O DPM executará uma cópia da réplica do armazenamento de cópia de segurança legado para o volume de armazenamento de cópia de segurança moderna localmente.
Nota: Isto irá ser visto como um • de tarefas de pós-recuperação operação todos os novos sincronização e pontos de recuperação, em seguida, serão armazenados no armazenamento de cópia de segurança moderna.
• Pontos de recuperação antigos serão eliminados de saída expire e, eventualmente, liberte o espaço em disco.
• Depois de todos os volumes legados são eliminados do armazenamento antigo, o disco pode ser removido do backup do Azure e o sistema.
• Efetuar uma cópia de segurança da DPMDB Azure.

Parte 2:-itens importantes > o novo servidor, terá de ter o nome mesmo que o servidor original de cópia de segurança do Azure. Não é possível alterar o nome do novo servidor de cópia de segurança do Azure, se pretender utilizar o agrupamento de armazenamento antigo e DPMDB para manter os pontos de recuperação - tem de ter a cópia de segurança da DPMDB, esta terá de ser restaurados

1) Encerramento do Azure original cópia de segurança de servidor ou levá-los fora da transmissão.
2) Repor a conta de computador no active directory.
3) Instalar Server 2016 computador novo e atribua-o mesmo nome de máquina que o servidor original de cópia de segurança do Azure.
4) Aderir ao domínio
5) Instalar o servidor de cópia de segurança do Azure V2 (discos de agrupamento de armazenamento do DPM mover do servidor antigo e importação)
6) Restaure o DPMDB obtida a partir do fim da parte 2
7) Anexe o armazenamento do servidor de cópia de segurança original para o novo servidor.
8) A partir de restauro do SQL Server a BD do DPM
9) Na linha de comandos de administrador no novo servidor cd para cópia de segurança do Microsoft Azure instalar localização e a pasta Reciclagem

Exemplo de caminho: C:\windows\system32 > cd "c:\Program Files\Microsoft Backup\DPM\DPM\bin\ do Azure
para o Azure criar cópias de segurança execute DPMSYNC-SYNC

10) Execute DPMSYNC-SYNC Nota Se tiver adicionado novos discos ao agrupamento de armazenamento do DPM em vez de mover as antigas, em seguida, execute DPMSYNC - Reallocatereplica

## <a name="new-powershell-cmdlets-in-v2"></a>Novos cmdlets do PowerShell na v2

Quando instalar o servidor de cópia de segurança do Azure v2, estão disponíveis dois novos cmdlets: 
* [Montagem DPMRecoveryPoint](https://technet.microsoft.com/library/mt787159.aspx)
* [Desmontagem DPMRecoveryPoint](https://technet.microsoft.com/library/mt787158.aspx)

## <a name="next-steps"></a>Passos seguintes

Saiba como preparar o servidor ou começar a proteger uma carga de trabalho:
- [Preparar as cargas de trabalho do servidor de cópia de segurança](backup-azure-microsoft-azure-backup.md)
- [Utilizar o servidor de cópia de segurança para fazer cópias de segurança de um servidor VMware](backup-azure-backup-server-vmware.md)
- [Utilizar o servidor de cópia de segurança para criar cópias de segurança do SQL Server](backup-azure-sql-mabs.md)
- [Utilizar o armazenamento de cópia de segurança moderno com o servidor de cópia de segurança](backup-mabs-add-storage.md)

