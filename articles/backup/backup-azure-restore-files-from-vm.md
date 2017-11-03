---
title: "Cópia de segurança do Azure: Recuperar ficheiros e pastas a partir de uma cópia de segurança de VM do Azure | Microsoft Docs"
description: "Recuperar ficheiros a partir de um ponto de recuperação da máquina virtual do Azure"
services: backup
documentationcenter: dev-center-name
author: pvrk
manager: shivamg
keywords: "recuperação ao nível do item; recuperação de ficheiros de cópia de segurança de VM do Azure; restaurar ficheiros a partir da VM do Azure"
ms.assetid: f1c067a2-4826-4da4-b97a-c5fd6c189a77
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/27/2017
ms.author: pullabhk;markgal
ms.openlocfilehash: 46cc2737c23b02c6542320e355607f83042bd058
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Recuperar ficheiros de cópia de segurança da máquina virtual do Azure

Cópia de segurança do Azure fornece a capacidade de restaurar [máquinas de virtuais (VMs) do Azure e os discos](./backup-azure-arm-restore-vms.md) de cópias de segurança de VM do Azure, também conhecido como restaurar pontos. Este artigo explica como recuperar ficheiros e pastas a partir de uma cópia de segurança de VM do Azure. Restaurar ficheiros e pastas só está disponível para VMs do Azure implementadas utilizando o modelo do Resource Manager e protegidos para um cofre de serviços de recuperação.

> [!Note]
> Não é suportada a recuperação de ficheiros a partir de uma cópia de segurança VM encriptada.
>

## <a name="mount-the-volume-and-copy-files"></a>Montar o volume e copiar os ficheiros

Para restaurar ficheiros ou pastas a partir do ponto de restauro, vá para a máquina virtual e optar por ponto de restauro. 

1. Inicie sessão no [portal do Azure](http://portal.Azure.com) e no menu da esquerda, clique em **máquinas virtuais**. Na lista de máquinas virtuais, selecione a máquina virtual para abrir o dashboard de nessa máquina virtual. 

2. No menu da máquina virtual, clique em **cópia de segurança** para abrir o dashboard de cópia de segurança.

    ![Item aberta de cópia de segurança do cofre dos serviços de recuperação](./media/backup-azure-restore-files-from-vm/open-vault-from-vm.png)

3. No menu do dashboard de cópia de segurança, clique em **recuperação de ficheiros** para abrir o menu.

    ![Menu de recuperação de ficheiro](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. Do **selecionar ponto de recuperação** menu pendente, selecione o ponto de recuperação que contém os ficheiros que pretende. Por predefinição, o ponto de recuperação mais recente já está selecionado.

5. Para transferir o software utilizado para copiar ficheiros a partir do ponto de recuperação, clique em **transferir executável** (para a VM do Windows Azure) ou **transferir o Script** (para a VM do Linux do Azure). 

    ![Palavra-passe gerada](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure transfere o ficheiro executável ou script no computador local.

    ![Transferir a mensagem para o executável ou script](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Para executar o executável ou script como um administrador, é sugerida que guarde a transferência para o seu computador.

6. O executável ou script é protegido de palavra-passe e requer uma palavra-passe. No **recuperação de ficheiros** menu, clique no botão Copiar para carregar a palavra-passe na memória.

    ![Palavra-passe gerada](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. A partir da localização de transferência (normalmente, a pasta de transferências), com o botão direito do executável ou script e executá-lo com as credenciais de administrador. Quando lhe for pedido, escreva a palavra-passe ou cole a palavra-passe da memória e prima Enter. Depois da palavra-passe válida é introduzido, o script estabelece ligação ao ponto de recuperação.

    ![Menu de recuperação de ficheiro](./media/backup-azure-restore-files-from-vm/executable-output.png)

    Se executar o script num computador com acesso restrito, certifique-se de que não há acesso ao:

    - download.microsoft.com
    - Pontos finais do Azure utilizados para cópias de segurança de VM do Azure
    - porta de saída 3260

   Para Linux, o script requer componentes 'open-iscsi' e 'lshw' para ligar ao ponto de recuperação. Se os componentes não existirem no computador onde o script é executado, o script pede-lhe permissão instalar os componentes. Fornecer consentimento para instalar os componentes necessários.  
         
   Pode executar o script em qualquer computador que tem o sistema de operativo mesmo (ou compatível) como a VM de cópia de segurança. Consulte o [tabela de SO compatível](backup-azure-restore-files-from-vm.md#compatible-os) para sistemas de operativos compatíveis. Se a máquina virtual do Azure protegida utiliza os espaços de armazenamento do Windows (para VMs do Windows Azure) ou Arrays(for Linux VMs) LVM/RAID, não é possível executar o executável ou script na mesma máquina virtual. Em vez disso, execute o ficheiro executável ou script em qualquer outra máquina com um sistema operativo compatível.

### <a name="compatible-os"></a>SO compatível

#### <a name="for-windows"></a>Para Windows

A tabela seguinte mostra a compatibilidade entre sistemas operativos de servidor e computador. Ao recuperar ficheiros, não é possível restaurar os ficheiros para uma versão anterior ou futuras do sistema operativo. Por exemplo, não é possível restaurar um ficheiro de uma VM do Windows Server 2016 para computador Windows Server 2012 ou Windows 8. Pode restaurar ficheiros a partir de uma VM para o mesmo sistema de operativo de servidor ou para o sistema operativo cliente compatível.   

|SO do servidor | SO de cliente compatível  |
| --------------- | ---- |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

#### <a name="for-linux"></a>Para Linux

No Linux, SO de computador utilizado para restaurar os ficheiros têm de suportar o sistema de ficheiros de máquina virtual protegida. Quando selecionar um computador para executar o script, certifique-se de que o computador tem um SO compatível e utiliza uma das versões identificadas na tabela seguinte:

|SO do Linux | Versões  |
| --------------- | ---- |
| Ubuntu | 12.04 e acima |
| CentOS | 6.5 e acima  |
| RHEL | 6.7 e acima |
| Debian | 7 e posterior |
| Oracle Linux | 6.4 e acima |

O script também requer o Python e bash componentes para executar e ligar de forma segura para o ponto de recuperação.

|Componente | Versão  |
| --------------- | ---- |
| Bash | 4 e posterior |
| python | 2.6.6 e acima  |


### <a name="identifying-volumes"></a>Identificar os Volumes

#### <a name="for-windows"></a>Para Windows

Quando executar o executável, o sistema operativo monta os novos volumes e atribui letras de unidade. Pode utilizar o Explorador do Windows ou o Explorador de ficheiros para procurar essas unidades. As letras de unidade atribuídas aos volumes não podem ser as mesmas letras de que a máquina virtual original, no entanto, o nome do volume é preservado. Por exemplo, se o volume na máquina virtual original foi "disco de dados (e:`\`)", que pode ser anexado volume no computador local como "disco de dados ('Qualquer letra':`\`). Procure através de todos os volumes mencionados no resultado do script até encontrar as ficheiros/pastas.  
       
   ![Menu de recuperação de ficheiro](./media/backup-azure-restore-files-from-vm/volumes-attached.png)
           
#### <a name="for-linux"></a>Para Linux

No Linux, dos pontos de recuperação estão montados para a pasta onde o script é executado. Os discos ligados, volumes e os caminhos de montagem correspondentes são apresentados em conformidade. Estes caminhos de montagem são visíveis para os utilizadores ter acesso de nível de raiz. Procure os volumes mencionados no resultado do script.

  ![Menu de recuperação de ficheiro do Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)
  

## <a name="closing-the-connection"></a>Fechar a ligação

Depois de identificar todos os ficheiros e copiá-las para uma localização de armazenamento local, remova (ou desmontar) as unidades adicionais. Desmontar as unidades no **recuperação de ficheiros** menu no portal do Azure, clique em **desmonte discos**.

![Desmonte discos](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Assim que os discos foram desmontados, receberá uma mensagem, permitindo-lhe saber que foi efetuada com êxito. Pode demorar alguns minutos para que a ligação atualizar, para que possa remover os discos.

No Linux, depois da ligação ao ponto de recuperação é severed, SO não remove os caminhos de montagem correspondentes automaticamente. Os caminhos de montagem de existir como volumes "órfãos" e estão visíveis mas gerem um erro quando a acesso/escrita de ficheiros. Podem ser manualmente removidas. O script, quando executado, identifica os volumes eventualmente existentes a partir de pontos de recuperação anterior e limpa-los após consentimento.

## <a name="special-configurations"></a>Configurações especiais

### <a name="dynamic-disks"></a>Discos dinâmicos

Se a VM do Azure protegido tiver volumes com uma ou ambas as seguintes características, não é possível executar o script executável na VM do mesma. 

  - Volumes que abrangem vários discos (abrangido e repartidos volumes)
  - Tolerância a falhas (volumes espelhados e RAID-5) de volumes em discos dinâmicos 

Em vez disso, execute o script executável em qualquer outro computador com um sistema operativo compatível.

### <a name="windows-storage-spaces"></a>Espaços de armazenamento do Windows

Os espaços de armazenamento do Windows é uma tecnologia do Windows que lhe permite virtualizar o armazenamento. Com os espaços de armazenamento do Windows pode agrupar discos de norma da indústria em agrupamentos de armazenamento. Em seguida, utilizar o espaço disponível nesses agrupamentos de armazenamento para criar discos virtuais, chamados espaços de armazenamento.

Se a VM do Azure protegido utilizar espaços de armazenamento do Windows, não é possível executar o script executável na VM do mesma. Em vez disso, execute o script executável em qualquer outra máquina com um sistema operativo compatível.

### <a name="lvmraid-arrays"></a>Matrizes LVM/RAID

No Linux, o Gestor de lógica de volumes (LVM) e/ou as matrizes de RAID de software é utilizada para gerir volumes lógicas através de vários discos. Se a VM com Linux protegida utiliza LVM e/ou as matrizes de RAID, não é possível executar o script na mesma VM. Em vez disso, execute o script em qualquer outra máquina com um SO compatível e que suporta o sistema de ficheiros da VM protegida.

O resultado do script seguinte mostra os discos LVM e/ou as matrizes de RAID e volumes com o tipo de partição.

   ![Menu de saída de LVM do Linux](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)
   
Para colocar online estas partições, execute os comandos nas secções seguintes. 

**Para partições LVM**

Para listar os nomes de grupo de volume no volume físico.
```
$ pvs <volume name as shown above in the script output> 
```
Para listar todos os volumes lógicos, nomes e os caminhos de um grupo de volume.

```
$ lvdisplay <volume-group-name from the pvs command’s results> 
```

Para montar os volumes lógicos para o caminho da sua preferência.

```
$ mount <LV path> </mountpath>
```



**Para as matrizes de RAID**

O comando seguinte apresenta os detalhes sobre todos os discos raid.

```
$ mdadm –detail –scan
```
 O disco RAID relevante é apresentado como`/dev/mdm/<RAID array name in the protected VM>`

Se o disco RAID tem volumes físicos, utilize o comando de montagem.
```
$ mount [RAID Disk Path] [/mountpath]
```

Se o disco RAID tiver outro LVM configurada, em seguida, utilize o procedimento anterior para partições LVM mas o nome do volume em vez do nome do disco de RAID

## <a name="troubleshooting"></a>Resolução de problemas

Se tiver problemas ao recuperar ficheiros de máquinas virtuais, consulte a tabela seguinte para obter informações adicionais.

| Mensagem de erro / cenário | Causa provável | Ação recomendada |
| ------------------------ | -------------- | ------------------ |
| Saída de exe: *exceção ao ligar ao destino* |Script não é possível aceder ao ponto de recuperação | Verifique se o computador satisfaz os requisitos de acesso anteriores. |  
|   Saída de exe: *o destino já foi registado através de uma sessão de ISCSI.* | O script já foi executado no mesmo computador e as unidades de tem sido ligadas | Os volumes do ponto de recuperação de já tem foi anexados. NÃO pode ser montadas com as mesmas letras de unidade da original VM. Procurar através de todos os volumes disponíveis no Explorador de ficheiros para o ficheiro |
| Saída de exe: *este script é inválido porque os discos têm foi desmontados através de RH 12 portal/excedeu o limite. Transferir um script novo do portal.* | Os discos têm foi desmontados do portal ou o limite de RH 12 excedido |    Este exe específico agora é inválido e não pode ser executada. Se pretender aceder aos ficheiros de que recuperação ponto no tempo, visite o portal para um novo exe|
| No computador onde é executado o exe: os novos volumes não são desmontados depois de clica no botão desmontagem |    O Iniciador do ISCSI no computador não responder/atualizar a ligação ao destino e mantém a cache |    Aguarde alguns minutos depois do dismount botão é premido. Se a novos volumes ainda não são desmontados, procure todos os volumes. Isto força o iniciador para atualizar a ligação e o volume é desmontado com uma mensagem de erro que o disco não está disponível|
| Saída de exe: Script é executado com êxito, mas "Novos volumes anexados" não são apresentados na saída do script | Este é um erro transitório   | Os volumes seriam ter sido já anexados. Abra o Explorador para procurar. Se estiver a utilizar o mesmo computador para executar scripts sempre, considere reiniciar o computador e a lista deverá ser apresentada nas execuções subsequentes exe. |
| Específica do Linux: não é possível ver os volumes pretendidos | Sistema operativo da máquina onde o script é executado não pode reconhecer o sistema de ficheiros subjacente da VM protegida | Verifique se o ponto de recuperação consistente com ficheiros ou consistente de falhas. Se o ficheiro consistente, execute o script noutro computador cujo SO reconhece o sistema de ficheiros a VM protegida |
| Específica do Windows: não é possível ver os volumes pretendidos | Os discos ter sido ligados, mas não foram configurados os volumes | No ecrã de gestão de disco, identifique os discos adicionais relacionadas com o ponto de recuperação. Se nenhum destes discos estão offline no Estado tente torná-los online clicando no disco e clique em 'Online'|
